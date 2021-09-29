---
title: Seguridad de red de Azure Data Lake Storage Gen1 | Microsoft Docs
description: Funcionamiento de la integración de redes virtuales en Azure Data Lake Storage Gen1
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 10/09/2018
ms.author: normesta
ms.openlocfilehash: faa71c0623fe2c0142f9c5f433fa76f36ddf2d5a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128578685"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1"></a>Integración de la red virtual en Azure Data Lake Storage Gen1

En este artículo se presenta la integración de red virtual de Azure Data Lake Storage Gen1. Con la integración de red virtual, puede configurar las cuentas para que acepten el tráfico procedente únicamente de redes virtuales y subredes específicas. 

Esta característica ayuda a proteger su cuenta de Data Lake Storage frente a amenazas externas.

Para la integración de red virtual en Data Lake Storage Gen1, se usa la seguridad del punto de conexión de servicio de red virtual entre la red virtual y Azure Active Directory (Azure AD) para generar notificaciones de seguridad adicionales en el token de acceso. Estas notificaciones se usan entonces para autenticar la red virtual en la cuenta de Data Lake Storage Gen1 y permitir el acceso.

> [!NOTE]
> No hay cargos adicionales asociados con el uso de estas funcionalidades. Su cuenta se factura según la tarifa estándar de Data Lake Storage Gen1. Para más información, consulte los [precios](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable). Para todos los otros servicios de Azure que use, consulte los [precios](https://azure.microsoft.com/pricing/#product-picker).

## <a name="scenarios-for-virtual-network-integration-for-data-lake-storage-gen1"></a>Escenarios de integración de red virtual en Data Lake Storage Gen1

Con la integración de red virtual de Data Lake Storage Gen1, puede restringir el acceso a la cuenta de Data Lake Storage Gen1 desde subredes y redes virtuales específicas. Una vez que la cuenta está bloqueada para la subred de la red virtual especificada, no se permite el acceso a otras redes virtuales o máquinas virtuales de Azure. Funcionalmente, la integración de red virtual de Data Lake Storage Gen1 permite el mismo escenario que los [puntos de conexión de servicio de red virtual](../virtual-network/virtual-network-service-endpoints-overview.md). En las siguientes secciones se detallan algunas diferencias importantes. 

![Diagrama del escenario de integración de red virtual de Data Lake Storage Gen1](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> Además de las reglas de red virtual, también se pueden usar las reglas de firewall de IP existentes para permitir el acceso desde redes locales. 

## <a name="optimal-routing-with-data-lake-storage-gen1-virtual-network-integration"></a>Enrutamiento óptimo con la integración de red virtual de Data Lake Storage Gen1

Una de las ventajas principales de los puntos de conexión de servicio de red virtual es el [enrutamiento óptimo](../virtual-network/virtual-network-service-endpoints-overview.md#key-benefits) desde la red virtual. Puede realizar la misma optimización de ruta para las cuentas de Data Lake Storage Gen1. Use las siguientes [rutas definidas por el usuario](../virtual-network/virtual-networks-udr-overview.md#user-defined) desde la red virtual hasta la cuenta de Data Lake Storage Gen1.

**Dirección IP pública de Data Lake Storage**: use la dirección IP pública para las cuentas de Data Lake Storage Gen1 de destino. Para identificar las direcciones IP de la cuenta de Data Lake Storage Gen1, [resuelva los nombres DNS](./data-lake-store-connectivity-from-vnets.md#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) de las cuentas. Cree una entrada independiente para cada dirección.

```azurecli
# Create a route table for your resource group.
az network route-table create --resource-group $RgName --name $RouteTableName

# Create route table rules for Data Lake Storage public IP addresses.
# There's one rule per Data Lake Storage public IP address. 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the virtual network, and apply the newly created route table to it.
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-virtual-network"></a>Filtración de datos desde la red virtual del cliente

Además de proteger el acceso a las cuentas de Data Lake Storage de la red virtual, quizá le interese también asegurarse de que no se produzca la filtración de datos a cuentas no autorizadas.

Use una solución de firewall en la red virtual para filtrar el tráfico saliente en función de la dirección URL de la cuenta de destino. Permita el acceso solo a cuentas de Data Lake Storage Gen1 autorizadas.

Algunas de las opciones disponibles son las siguientes:
- [Azure Firewall](../firewall/overview.md): [implemente y configure Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md) para la red virtual. Proteja el tráfico saliente de Data Lake Storage y bloquéelo hasta la dirección URL de la cuenta conocida y aprobada.
- Firewall de [aplicación virtual de red](https://azure.microsoft.com/solutions/network-appliances/): el administrador podría permitirle usar solo determinados proveedores de firewall comerciales. Use una solución de firewall de aplicación virtual de red que esté disponible en Azure Marketplace para realizar la misma función.

> [!NOTE]
> El uso de firewalls en la ruta de acceso a los datos introduce un salto adicional en dicha ruta. Esto podría afectar al rendimiento en el intercambio de datos de un extremo a otro. También podría afectar a la disponibilidad del rendimiento y la latencia de conexión. 

## <a name="limitations"></a>Limitaciones

- Los clústeres de HDInsight que se crearon antes de que estuviera disponible la compatibilidad con la integración de red virtual de Data Lake Storage Gen1 deben volver a crearse para admitir esta nueva característica.
 
- Al crear un nuevo clúster de HDInsight y seleccionar una cuenta de Data Lake Storage Gen1 con la integración de red virtual habilitada, se produce un error en el proceso. En primer lugar, deshabilite la regla de red virtual. O bien, en la hoja **Firewall y redes virtuales** de la cuenta de Data Lake Storage, seleccione **Allow access from all networks and services** (Permitir el acceso desde todas las redes y servicios). A continuación, cree el clúster de HDInsight antes de volver a habilitar la regla de red virtual o anular la selección de **Allow access from all networks and services** (Permitir el acceso desde todas las redes y servicios). Para más información, consulte la sección [Excepciones](#exceptions).

- La integración de red virtual de Data Lake Storage Gen1 no funciona con [identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md).
  
- Los datos de archivos y carpetas de la cuenta de Data Lake Storage Gen1 habilitada para la red virtual no son accesibles desde el portal. Esta restricción incluye el acceso desde una máquina virtual que esté dentro de la red virtual y actividades como el uso del Explorador de datos. Las actividades de administración de cuentas seguirán funcionando. Los datos de archivos y carpetas de la cuenta de Data Lake Storage habilitada para la red virtual son accesibles a través de todos los recursos que no sean del portal. Estos recursos incluyen acceso al SDK, scripts de PowerShell y otros servicios de Azure cuando no se originan desde el portal. 

## <a name="configuration"></a>Configuración

### <a name="step-1-configure-your-virtual-network-to-use-an-azure-ad-service-endpoint"></a>Paso 1: Configuración de la red virtual para usar un punto de conexión de servicio de Azure AD

1.  Vaya a Azure Portal e inicie sesión en su cuenta.
 
2.  [Cree una nueva red virtual ](../virtual-network/quick-create-portal.md) en su suscripción. O bien, puede ir a una red virtual existente. La red virtual debe estar en la misma región que la cuenta de Data Lake Storage Gen 1.
 
3.  En la hoja **Red virtual**, seleccione **Puntos de conexión de servicio**.
 
4.  Seleccione **Agregar** para agregar un nuevo punto de conexión de servicio.

    ![Incorporación de un punto de conexión de servicio de red virtual](media/data-lake-store-network-security/config-vnet-1.png)

5.  Seleccione **Microsoft.AzureActiveDirectory** como servicio del punto de conexión.

     ![Selección del punto de conexión de servicio Microsoft.AzureActiveDirectory](media/data-lake-store-network-security/config-vnet-2.png)

6.  Seleccione las subredes para las que planea permitir la conectividad. Seleccione **Agregar**.

    ![Selección de la subred](media/data-lake-store-network-security/config-vnet-3.png)

7.  La incorporación del punto de conexión de servicio puede tardar hasta 15 minutos. Después de agregarla, se muestra en la lista. Compruebe que se muestra y que todos los detalles son como se han configurado.
 
    ![Incorporación correcta del punto de conexión de servicio](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-virtual-network-or-subnet-for-your-data-lake-storage-gen1-account"></a>Paso 2: Configuración de la red virtual o la subred permitidas para la cuenta de Data Lake Storage Gen1

1.  Después de configurar la red virtual, [cree una cuenta de Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) en su suscripción. O bien, puede ir a una cuenta de Data Lake Storage Gen1 existente. La cuenta de Data Lake Storage Gen1 debe estar en la misma región que la red virtual.
 
2.  Elija **Firewall y redes virtuales**.

    > [!NOTE]
    > Si no ve **Firewall y redes virtuales** en la configuración, cierre la sesión en el portal. Cierre el explorador y borre la caché del explorador. Reinicie la máquina y vuelva a intentarlo.

       ![Incorporación de una regla de red virtual a la cuenta de Data Lake Storage](media/data-lake-store-network-security/config-adls-1.png)

3.  Seleccione **Redes seleccionadas**.
 
4.  Seleccione **Agregar red virtual existente**.

    ![Incorporación de una red virtual existente](media/data-lake-store-network-security/config-adls-2.png)

5.  Seleccione las redes virtuales y subredes para permitir la conectividad. Seleccione **Agregar**.

    ![Selección de la red virtual y las subredes](media/data-lake-store-network-security/config-adls-3.png)

6.  Asegúrese de que las redes virtuales y las subredes aparezcan correctamente en la lista. Seleccione **Guardar**.

    ![Guardar la nueva regla](media/data-lake-store-network-security/config-adls-4.png)

    > [!NOTE]
    > La configuración puede tardar en aplicarse hasta cinco minutos después de guardarla.

7.  [Opcional] En la página **Firewall y redes virtuales**, en la sección **Firewall**, puede permitir la conectividad desde direcciones IP específicas. 

## <a name="exceptions"></a>Excepciones
Puede habilitar la conectividad desde servicios y máquinas virtuales de Azure fuera de las redes virtuales seleccionadas. En la hoja **Firewall y redes virtuales**, en el área **Excepciones**, seleccione entre dos opciones:
 
- **Allow all Azure services to access this Data Lake Storage Gen1 account** (Permitir que todos los servicios de Azure accedan a esta cuenta de Data Lake Storage Gen1). Esta opción permite que los servicios de Azure como Azure Data Factory, Azure Event Hubs y todas las máquinas virtuales de Azure se comuniquen con su cuenta de Data Lake Storage.

- **Allow Azure Data Lake Analytics to access this Data Lake Storage Gen1 account** (Permitir que Azure Data Lake Analytics acceda a esta cuenta de Data Lake Storage Gen1). Esta opción permite la conectividad de Data Lake Analytics a esta cuenta de Data Lake Storage. 

  ![Excepciones de Firewall y redes virtuales](media/data-lake-store-network-security/firewall-exceptions.png)

Se recomienda mantener estas excepciones desactivadas. Actívelas solo si necesita conectividad desde estos otros servicios fuera de la red virtual.