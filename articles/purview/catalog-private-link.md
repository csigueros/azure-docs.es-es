---
title: Uso de puntos de conexión privados para el acceso seguro a Purview
description: En este artículo se describe cómo puede configurar un punto de conexión privado para la cuenta de Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/11/2021
ms.openlocfilehash: 5101e71f6caf23e48adc59c9afde402427aa9995
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/14/2021
ms.locfileid: "112080424"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>Uso de puntos de conexión privados para la cuenta de Purview

Puede usar puntos de conexión privados para sus cuentas de Purview para que los clientes y usuarios de una red virtual (VNet) puedan acceder de forma segura al catálogo a través de una instancia de Private Link. El punto de conexión privado usa una dirección IP del espacio de direcciones de la red virtual para la cuenta de Purview. El tráfico de red entre los clientes de la red virtual y la cuenta de Purview atraviesa la red virtual y un vínculo privado de la red troncal de Microsoft, lo que elimina la exposición a la red pública de Internet.

   :::image type="content" source="media/catalog-private-link/purview-private-link-architecture.png" alt-text="Arquitectura de Azure Purview y Private Link":::



Revise las [Preguntas frecuentes sobre los puntos de conexión privados de Azure Purview](./catalog-private-link-faqs.md).

## <a name="create-purview-account-with-a-private-endpoint"></a>Creación de una cuenta de Purview con un punto de conexión privado

1. Vaya a [Azure Portal](https://portal.azure.com) y luego a su cuenta de Purview.

1. Rellene la información básica y seleccione el método de conectividad al punto de conexión privado en la pestaña **Redes**. Para configurar los puntos de conexión privados de ingesta, indique los detalles de **Suscripción, Red virtual y Subred** que quiera emparejar con el punto de conexión privado.

   > [!NOTE]
   > Cree un punto de conexión privado de ingesta solo si desea habilitar el aislamiento de red para escenarios de análisis de un extremo a otro, tanto para los orígenes locales como de Azure. Actualmente no se admiten los puntos de conexión privados de ingesta que funcionan con orígenes de AWS.

   :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Creación de un punto de conexión privado en Azure Portal":::

1. Como opción, puede elegir configurar una **Zona DNS privada** para cada punto de conexión privado de ingesta.

1. Seleccione **Agregar** para agregar un punto de conexión privado para la cuenta de Purview.

1. En la página Crear un punto de conexión privado, establezca el subrecurso de Purview en **cuenta**, elija la red virtual y la subred, y seleccione la Zona DNS privada donde se registrará el DNS (también puede usar sus propios servidores DNS o crear registros de DNS mediante archivos host en sus máquinas virtuales).

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="Selecciones de creación de puntos de conexión privados":::

1. Seleccione **Aceptar**.

1. Seleccione **Revisar + crear**. Se le remitirá a la página Revisar y crear, donde Azure validará la configuración.

1. Cuando reciba el mensaje Validación superada, seleccione **Crear**.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="Validación superada para la creación de la cuenta":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>Creación de un punto de conexión privado para el portal web de Purview

1. Vaya a la cuenta de Purview que acaba de crear y seleccione Conexiones de punto de conexión privado en la sección Configuración.

1. Seleccione **+ Punto de conexión privado** para crear uno.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Creación de punto de conexión privado en el portal":::

1. Rellene la información básica.

1. En la pestaña Recurso, seleccione el Tipo de recurso **microsoft.Purview/accounts**.

1. Seleccione como recurso la cuenta de Purview recién creada y seleccione el subrecurso de destino para que sea **portal**.
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="Detalles del punto de conexión privado en el portal":::

1. Seleccione la red virtual y la zona DNS privada en la pestaña Configuración. Vaya a la página Resumen, haga clic en **Crear** para crear el punto de conexión privado en el portal.

## <a name="private-dns-zone-requirements-for-private-endpoints"></a>Requisitos de zona DNS privada para los puntos de conexión privados
Al crear un punto de conexión privado, el registro del recurso CNAME de DNS de Purview se actualiza a un alias de un subdominio con el prefijo `privatelink`. De forma predeterminada, también se crea una [zona DNS privada](../dns/private-dns-overview.md), que se corresponde con el subdominio `privatelink`, con los registros de recursos D de DNS para los puntos de conexión privados.
 
Cuando se resuelve la dirección URL del punto de conexión de Purview desde fuera de la red virtual con el punto de conexión privado, se resuelve en el punto de conexión público de Azure Purview. Cuando se resuelve desde la red virtual que hospeda el punto de conexión privado, la dirección URL del punto de conexión de Purview se resuelve en la dirección IP del punto de conexión privado.

Por ejemplo, si el nombre de la cuenta de Purview es "PurviewA", cuando se resuelve desde fuera de la red virtual que hospeda el punto de conexión privado, será:

| Nombre | Tipo | Value |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | A | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview public endpoint\> |
 
Los registros de recursos DNS para PurviewA, cuando se resuelvan en la instancia de VNet que hospeda el punto de conexión privado, serán:
 
| Nombre | Tipo | Value |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | A | \<private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<private endpoint IP address\> |
 
 > [!important]
 > Si no usa reenviadores DNS y, en su lugar, administra registros A directamente en los servidores DNS locales para resolver los puntos de conexión a través de sus direcciones IP privadas, es posible que tenga que crear registros A adicionales en los servidores DNS:

| Nombre | Tipo | Value |
| ---------- | -------- | --------------- |
| `PurviewA.scan.Purview.azure.com` | A | \<private endpoint IP address\> |
| `PurviewA.catalog.Purview.azure.com` | A | \<private endpoint IP address\> |

<br> 

_Ejemplo de resolución de nombres DNS de Azure Purview desde fuera de la red virtual o cuando Azure Private Endpoint no está configurado:_

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="Resolución de nombres de Purview desde fuera de CorpNet":::

_Ejemplo de resolución de nombres DNS de Azure Purview desde dentro de la red virtual:_

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="Resolución de nombres de Purview desde dentro de CorpNet":::

Es importante definir correctamente la configuración de DNS para resolver la dirección IP del punto de conexión privado en el nombre de dominio completo (FQDN) de la cadena de conexión.

Si va a usar un servidor DNS personalizado en la red, los clientes deben ser capaces de resolver el FQDN del punto de conexión de Purview en la dirección IP del punto de conexión privado. Debe configurar el servidor DNS para delegar el subdominio del vínculo privado en la zona DNS privada de la red virtual, o bien configurar los registros D para "PurviewA.privatelink.purview.azure.com" con la dirección IP del punto de conexión privado.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="Resolución de nombres de Purview":::

Para más información, consulte [Configuración de DNS para puntos de conexión privados de Azure](../private-link/private-endpoint-dns.md).

## <a name="enabling-access-to-azure-active-directory"></a>Habilitación del acceso a Azure Active Directory

> [!NOTE]
> Si la VM, la puerta de enlace de VPN o la puerta de enlace de emparejamiento de VNet tienen acceso público a Internet, pueden acceder al portal de Purview y a la cuenta de Purview habilitada con puntos de conexión privados, y no es necesario seguir el resto de las instrucciones a continuación. Si la red privada tiene reglas de grupo de seguridad de red establecidas para denegar todo el tráfico público de Internet, tendrá que agregar algunas reglas para habilitar el acceso a AAD. Para ello, siga las instrucciones que se indican a continuación.

Las instrucciones siguientes son para acceder de forma segura a Purview desde una VM de Azure. Es necesario seguir pasos similares si usa VPN u otras puertas de enlace de emparejamiento de VNet.

1. Vaya a la VM en Azure Portal, seleccione la pestaña Redes en la sección Configuración. A continuación, seleccione Reglas de puerto de salida y haga clic en Agregar regla de puerto de salida.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="Adición de regla de salida":::

2. En la hoja Agregar regla de puerto de salida, seleccione *Destino* como Etiqueta de servicio, Etiqueta de servicio de destino como **AzureActiveDirectory**, Intervalos de puertos de destino como *, defina Acción en Permitir, **Prioridad** debe ser mayor que la regla que denegó todo el tráfico de Internet. Cree la regla.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="Adición de los detalles de la regla de salida":::

3. Siga los mismos pasos para crear otra regla que permita la etiqueta de servicio **AzureResourceManager**. Si necesita acceder a Azure Portal, también puede agregar una regla para la etiqueta de servicio *AzurePortal*.

4. Conéctese a la VM, abra el explorador, vaya a la consola del explorador (CTRL+Mayús+J) y cambie a la pestaña Red para supervisar las solicitudes de red. Escriba web.purview.azure.com en el cuadro de dirección URL e intente iniciar sesión con sus credenciales de AAD. Es posible que se produzca un error en el inicio de sesión, y que en la pestaña Red de la consola vea que AAD intenta acceder a aadcdn.msauth.net, pero se bloquea.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Detalles de error de inicio de sesión":::

5. En este caso, abra un símbolo del sistema en la VM y haga ping a esta dirección URL (aadcdn.msauth.net), obtenga su dirección IP y, a continuación, agregue una regla de puerto de salida para la IP en las reglas de seguridad de red de la VM. Establezca Destino en Dirección IP y establezca Intervalos de direcciones IP de destino en la dirección IP de aadcdn. Debido al equilibrador de carga y el administrador de tráfico, es posible que la dirección IP de AAD CDN sea dinámica. Una vez que obtenga su dirección IP, es mejor agregarla al archivo host de la VM para obligar al explorador a visitar esa IP para conectarse con AAD CDN.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Ping de prueba":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Regla de AAD CDN":::

6. Una vez creada la nueva regla, vuelva a la máquina virtual e intente iniciar sesión de nuevo con las credenciales de Azure Active Directory. Si logra iniciar sesión, el portal de Purview está listo para usar. Pero en algunos casos, AAD se redirigirá a otros dominios para iniciar sesión según el tipo de cuenta del cliente. Por ejemplo, en el caso de una cuenta de live.com, AAD redirigirá a live.com para iniciar sesión; después, esas solicitudes se volverán a bloquear. En el caso de las cuentas de empleados de Microsoft, AAD tendrá acceso a msft.sts.microsoft.com para obtener la información de inicio de sesión. Compruebe las solicitudes de red en la pestaña Redes del explorador para ver qué solicitudes de dominio se están bloqueando, repita el paso anterior para obtener su IP y agregue reglas de puerto de salida en el grupo de seguridad de red para permitir las solicitudes para esa IP (si es posible, agregue la dirección URL y la IP al archivo host de la VM para corregir la resolución de DNS). Si conoce los intervalos exactos de IP del dominio de inicio de sesión, también puede agregarlos directamente a las reglas de red.

7. Ahora, el inicio de sesión en Azure Active Directory debería ser correcto. El portal de Purview se cargará correctamente, pero el listado de todas las cuentas de Purview no funcionará, ya que solo puede acceder a una cuenta de Purview específica. Escriba `web.purview.azure.com/resource/{PurviewAccountName}` para visitar directamente la cuenta de Purview para la que ha configurado correctamente un punto de conexión privado.
 
## <a name="ingestion-private-endpoints-and-scanning-sources-in-private-networks-vnets-and-behind-private-endpoints"></a>Puntos de conexión privados de ingesta y examen de orígenes en redes privadas, redes virtuales y detrás de puntos de conexión privados

Si desea garantizar el aislamiento de red para los metadatos que fluyen desde el origen que se está analizando hasta DataMap de Purview, siga estos pasos:

1. Habilite un **punto de conexión privado de ingesta** siguiendo los pasos de [esta](#creating-an-ingestion-private-endpoint) sección.

1. Analice el origen mediante un **IR autohospedado**.
 
    1. Actualmente, todos los tipos de orígenes locales como SQL Server, Oracle, SAP, etc., solo se admiten a través de exámenes basados en IR autohospedados. El IR autohospedado debe ejecutarse dentro de la red privada y, a continuación, emparejarse con la red virtual en Azure. La red virtual de Azure debe entonces habilitarse en el punto de conexión privado de ingesta siguiendo [estos pasos](#creating-an-ingestion-private-endpoint). 

    2. En el caso de todos los tipos de origen de **Azure**, como Azure Blob Storage, Azure SQL Database y otros, debe elegir explícitamente la ejecución del examen mediante el IR autohospedado para garantizar el aislamiento de red. Siga los pasos descritos en [Creación y administración de un entorno de ejecución de integración autohospedado](manage-integration-runtimes.md) para configurar un entorno de ejecución de integración autohospedado. A continuación, configure el examen en el origen de Azure; para ello, elija el IR autohospedado en la lista desplegable **Conectar mediante el entorno de ejecución de integración** para garantizar el aislamiento de red. 
    
    :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="Ejecución de examen de Azure con IR autohospedado":::

> [!NOTE]
> Cuando se usa un punto de conexión privado para la ingesta, puede usar Azure Integration Runtime para examinar solo los siguientes orígenes de datos:
> - Azure Blob Storage 
> - Azure Data Lake Gen 2
>  
> Para otros orígenes de datos, se requiere un entorno de ejecución de integración autohospedado.
> Actualmente no se admite el método de credenciales de MSI cuando se examinan los orígenes de Azure mediante un IR autohospedado. Debe usar uno de los otros métodos de credenciales admitidos para ese origen de Azure.

## <a name="enable-private-endpoint-on-existing-purview-accounts"></a>Habilitación de un punto de conexión privado en cuentas de Purview existentes

Hay dos maneras de agregar puntos de conexión privados de Purview después de crear la cuenta de Purview:

- Mediante Azure Portal (cuenta de Purview)
- Mediante Private Link Center

### <a name="using-the-azure-portal-purview-account"></a>Mediante Azure Portal (cuenta de Purview)

1. Vaya a la cuenta de Purview desde Azure Portal, seleccione Conexiones de punto de conexión privado en la sección **Redes** de **Configuración**.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Creación de un punto de conexión privado de cuenta":::

1. Seleccione **+ Punto de conexión privado** para crear uno.

1. Rellene la información básica.

1. En la pestaña Recurso, seleccione el **Tipo de recurso** **Microsoft.Purview/accounts**.

1. Seleccione como **Recurso** la cuenta de Purview y seleccione el subrecurso de destino para que sea **cuenta**.

1. Seleccione la **red virtual** y la **zona DNS privada** en la pestaña Configuración. Vaya a la página Resumen, haga clic en **Crear** para crear el punto de conexión privado en el portal.

> [!NOTE]
> También tendrá que seguir los mismos pasos que antes para el subrecurso de destino seleccionado como **Portal**.

#### <a name="creating-an-ingestion-private-endpoint"></a>Creación de un punto de conexión privado de ingesta

1. Vaya a la cuenta de Purview desde Azure Portal, seleccione Conexiones de punto de conexión privado en la sección **Redes** de **Configuración**.

3. Vaya a la pestaña **Ingestion private endpoint connections** (Conexiones de punto de conexión privado de ingesta) y haga clic en **+Nuevo** para crear un nuevo punto de conexión privado de ingesta.

4. Rellene la información básica y los detalles de la red virtual.
 
   :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="Relleno de los detalles del punto de conexión privado":::

5. Haga clic en **Crear** para finalizar la configuración.

> [!NOTE]
> Los puntos de conexión privados de la ingesta solo se pueden crear a través de la experiencia de Azure Portal en Purview que se ha descrito anteriormente. No se pueden crear desde el centro de Private Link.

### <a name="using-the-private-link-center"></a>Mediante Private Link Center

1. Acceda a [Azure Portal](https://portal.azure.com).

2. En la barra de búsqueda de la parte superior de la página, busque "private link" y navegue hasta la hoja Private Link haciendo clic en la primera opción.

3. Seleccione **+ Agregar** y rellene los detalles básicos.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="Creación de un punto de conexión desde Private Link Center":::

4. Seleccione como recurso la cuenta de Purview ya creada y seleccione el subrecurso de destino para que sea **cuenta**.

5. Seleccione la red virtual y la zona DNS privada en la pestaña Configuración. Vaya a la página Resumen, haga clic en **Crear** para crear el punto de conexión privado en la cuenta.

> [!NOTE]
> También tendrá que seguir los mismos pasos que antes para el subrecurso de destino seleccionado como **Portal**.

## <a name="firewalls-to-restrict-public-access"></a>Firewalls para la restricción del acceso público

Para cortar completamente el acceso a la cuenta de Purview desde la red pública, siga los pasos indicados a continuación. Esta configuración se aplicará a las conexiones del punto de conexión privado y el punto de conexión privado de ingesta.

1. Vaya a la cuenta de Purview desde Azure Portal, seleccione Conexiones de punto de conexión privado en la sección **Redes** de **Configuración**.

3. Vaya a la pestaña del firewall y asegúrese de que el botón de alternancia esté establecido en **Denegar**.

   :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="Configuración de firewall de punto de conexión privado":::

## <a name="next-steps"></a>Pasos siguientes

- [Navegación por el catálogo de datos de Azure Purview](how-to-browse-catalog.md)

- [Búsqueda en el catálogo de datos de Azure Purview](how-to-search-catalog.md)
