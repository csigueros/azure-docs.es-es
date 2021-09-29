---
title: Conexión a una red virtual interna mediante Azure API Management
description: Aprenda a instalar y configurar Azure API Management en una red virtual mediante el modo interno.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 08/10/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ee5d0ac639cbd36215df5545d684909af3acd748
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128563827"
---
# <a name="connect-to-a-virtual-network-in-internal-mode-using-azure-api-management"></a>Conexión a una red virtual en modo interno mediante Azure API Management 
Con las redes virtuales de Azure (VNET), Azure API Management puede administrar API inaccesibles por Internet mediante varias tecnologías de VPN para realizar la conexión. Puede implementar API Management mediante modos [externos](./api-management-using-with-vnet.md) o internos. Para ver las opciones, los requisitos y las consideraciones de conectividad de red virtual, consulte [Uso de una red virtual con Azure API Management](virtual-network-concepts.md).

En este artículo, aprenderá a implementar API Management en el modo de red virtual interno. En este modo solo puede ver los siguientes puntos de conexión de servicio dentro de una red virtual cuyo acceso controle.
* La puerta de enlace de API
* Portal para desarrolladores
* Administración directa
* Git 

> [!NOTE]
> Ninguno de los puntos de conexión de servicio está registrado en el DNS público. Los puntos de conexión de servicio permanecen inaccesibles hasta que [configura DNS](#dns-configuration) para la red virtual.

Use API Management en modo interno para:

* Conseguir que las API hospedadas en el centro de datos privado sean accesibles para terceros desde fuera de este centro si usa conexiones de VPN de Azure o Azure ExpressRoute.
* Puede permitir escenarios de nube híbrida exponiendo las API basadas en la nube y las API locales a través de una puerta de enlace común.
* Administrar las API hospedadas en diversas ubicaciones geográficas, mediante un único punto de conexión de puerta de enlace.

:::image type="content" source="media/api-management-using-with-internal-vnet/api-management-vnet-internal.png" alt-text="Conexión a una red virtual interna":::

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Requisitos previos

Algunos requisitos previos varían en función de la versión (`stv2` o `stv1`) de la [plataforma de proceso](compute-infrastructure.md) de la instancia de API Management. 

> [!TIP]
> Cuando se usa el portal para crear o actualizar la configuración de red de la instancia de API Management, la instancia se hospeda en la plataforma de proceso `stv2`.

### <a name="stv2"></a>[stv2](#tab/stv2)

+ **Una instancia de API Management** Para más información, vea [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

* **Una red virtual y una subred** en la misma región y suscripción que la instancia de API Management. La subred puede contener otros recursos de Azure.

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

   > [!NOTE]
   > Al implementar un servicio API Management en una red virtual interna en la plataforma `stv2`, este se hospeda detrás de un equilibrador de carga interno de la [SKU estándar](../load-balancer/skus.md), mediante el recurso de dirección IP pública.

### <a name="stv1"></a>[stv1](#tab/stv1)

+ **Una instancia de API Management** Para más información, vea [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

* **Una red virtual y una subred** en la misma región y suscripción que la instancia de API Management.

    La subred debe estar dedicada a las instancias de API Management. Si se intenta implementar una instancia de Azure API Management en una subred de VNET de Resource Manager que contiene otros recursos, se producirá un error en la implementación.

   > [!NOTE]
   > Al implementar un servicio API Management en una red virtual interna en la plataforma `stv1`, este se hospeda detrás de un equilibrador de carga interno de la [SKU básica](../load-balancer/skus.md).

---

## <a name="enable-vnet-connection"></a>Habilitar la conexión de VNET

### <a name="enable-vnet-connectivity-using-the-azure-portal-stv2-platform"></a>Habilitación de la conectividad de VNET mediante Azure Portal (plataforma `stv2`)

1. Vaya a [Azure Portal](https://portal.azure.com) para buscar la instancia API Management. Busque y seleccione **Servicios API Management**.
1. Elija su instancia de API Management.
1. Seleccione **Red virtual**.
1. Seleccione el tipo de acceso **Interno**.
1. En la lista de ubicaciones (regiones) en las que se aprovisiona el servicio API Management: 
    1. Elija una **ubicación**.
    1. Seleccione **Red virtual**, **Subred** y **Dirección IP**. 
        * La lista de redes virtuales se rellena con redes virtuales de Resource Manager disponibles en las suscripciones a Azure configuradas en la región que va a configurar.
1. Seleccione **Aplicar**. La página **Red virtual** de la instancia de API Management se actualiza con las opciones de red virtual y subred nueva.
   :::image type="content" source="media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png" alt-text="Configuración de una red virtual interna en Azure Portal":::
1. Siga configurando los valores de red virtual para las ubicaciones restantes de la instancia de API Management.
1. En la barra de navegación superior, seleccione **Guardar** y, luego, **Aplicar configuración de red**.

    La instancia de API Management puede tardar entre 15 y 45 minutos en actualizarse.

Después de que la implementación se realice correctamente, debería ver la dirección IP virtual **privada** y la dirección IP virtual **pública** del servicio API Management en el panel **Información general**. Para más información sobre las direcciones IP, consulte [Enrutamiento](#routing) en este artículo.

:::image type="content" source="media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png" alt-text="Direcciones IP públicas y privadas en Azure Portal"::: 

> [!NOTE]
> Como la dirección URL de puerta de enlace no está registrada en el DNS público, la consola de prueba disponible en Azure Portal no funcionará con un servicio implementado en una red virtual **interna**. En su lugar, use la consola de prueba que se ofrece en el **Portal para desarrolladores**.

### <a name="enable-connectivity-using-a-resource-manager-template"></a>Habilitación de la conectividad mediante una plantilla de Resource Manager

#### <a name="api-version-2021-01-01-preview-stv2-platform"></a>Versión de API 2021-01-01-preview (plataforma `stv2`)

* [Plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet-publicip) de Resource Manager

     [![Implementar en Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet-publicip%2Fazuredeploy.json)

#### <a name="api-version-2020-12-01-stv1-platform"></a>Versión de API 2020-12-01 (plataforma `stv1`)

* [Plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet) de Resource Manager

     [![Implementar en Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet%2Fazuredeploy.json)

### <a name="enable-connectivity-using-azure-powershell-cmdlets-stv1-platform"></a>Habilitación de la conectividad mediante cmdlets de Azure PowerShell (plataforma `stv1`)

[Cree](/powershell/module/az.apimanagement/new-azapimanagement) o [actualice](/powershell/module/az.apimanagement/update-azapimanagementregion) una instancia de API Management en una red virtual.

## <a name="dns-configuration"></a>Configuración de DNS

En el modo de red virtual externa, Azure administra el DNS. En el modo de red virtual interna, tendrá que administrar su propio DNS. Es recomendable que:
1. Configure una [zona privada de Azure DNS](../dns/private-dns-overview.md).
1. Vincule la zona privada de Azure DNS a la red virtual en la que ha implementado el servicio API Management. 

Aprenda a [configurar una zona privada en Azure DNS](../dns/private-dns-getstarted-portal.md).

> [!NOTE]
> El servicio API Management no escucha las solicitudes de sus direcciones IP. Solo responde a las solicitudes dirigidas al nombre de host establecido en los puntos de conexión de servicio. Estos puntos de conexión incluyen lo siguiente:
> * Puerta de enlace de API
> * El Portal de Azure
> * Portal para desarrolladores
> * Punto de conexión de administración directa
> * Git

### <a name="access-on-default-host-names"></a>Acceso de nombres de host predeterminados
Al crear un servicio de API Management (por ejemplo, `contosointernalvnet`) se configuran de forma predeterminada los siguientes puntos de conexión de servicio:

| Punto de conexión | Configuración de punto de conexión |
| ----- | ----- |
| API Gateway | `contosointernalvnet.azure-api.net` |
| Portal para desarrolladores | `contosointernalvnet.portal.azure-api.net` |
| Nuevo portal para desarrolladores | `contosointernalvnet.developer.azure-api.net` |
| Punto de conexión de administración directa | `contosointernalvnet.management.azure-api.net` |
| Git | `contosointernalvnet.scm.azure-api.net` |

Para acceder a estos puntos de conexión de servicio de API Management, puede crear una máquina virtual en una subred conectada a la red virtual en la que esté implementado API Management. Si la dirección IP virtual interna del servicio es 10.1.0.5, puede asignar el archivo de hosts de esta forma. En Windows, este archivo se encuentra en `%SystemDrive%\drivers\etc\hosts`. 

| Dirección IP virtual interna | Configuración de punto de conexión |
| ----- | ----- |
| 10.1.0.5 | `contosointernalvnet.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.portal.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.developer.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.management.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.scm.azure-api.net` |

Así, podrá tener acceso a todos los puntos de conexión de servicio desde la máquina virtual que ha creado.

Si usa un servidor DNS personalizado en una red virtual, también puede crear registros A de DNS y acceder a estos puntos de conexión desde cualquier lugar de la red virtual.

### <a name="access-on-custom-domain-names"></a>Acceso de nombres de dominio personalizados

Si no quiere acceder al servicio API Management con los nombres de host predeterminados: 

1. Configure [nombres de dominio personalizados](configure-custom-domain.md) para todos los puntos de conexión de servicio, como se muestra en la imagen siguiente:

    :::image type="content" source="media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png" alt-text="Configuración de nombres de dominios personalizados":::

2. Cree registros en el servidor DNS para acceder a los puntos de conexión accesibles desde la red virtual.

## <a name="routing"></a>Enrutamiento

Las siguientes direcciones IP virtuales se configuran para una instancia de API Management de una red virtual interna. Obtenga más información sobre las [direcciones IP de API Management](api-management-howto-ip-addresses.md).

| Dirección IP virtual | Descripción |
| ----- | ----- |
| **Dirección IP virtual privada** | Una dirección IP con equilibrio de carga desde el intervalo de subred (DIP) de la instancia de API Management, a través de la cual puede acceder a la puerta de enlace de API, al portal para desarrolladores, a la administración y a los puntos de conexión de Git.<br/><br/>Registre esta dirección con los servidores DNS que usa la red virtual.     |  
| **Dirección IP virtual pública** | *Solo* se usa para el tráfico del plano de control al punto de conexión de administración a través del puerto 3443. Se puede bloquear a la etiqueta de servicio [ApiManagement][ServiceTags]. | 

Las direcciones IP privadas y públicas con equilibrio de carga pueden encontrarse en la hoja de **información general** de Azure Portal.

> [!NOTE]
> Las direcciones VIP de la instancia de API Management cambiarán cuando:
> * La red virtual se habilite o deshabilite. 
> * API Management se mueva del modo de red virtual **externa** a **interna**, o viceversa.
> * Se habilite, actualice o deshabilite la configuración de la [redundancia de zona](zone-redundancy.md) en una ubicación para la instancia (solo la SKU Premium).
>
> Es posible que tenga que actualizar los registros de DNS, las reglas de enrutamiento y las listas de restricciones de IP dentro de la red virtual.

### <a name="vip-and-dip-addresses"></a>Direcciones VIP y DIP

Se asignarán direcciones DIP a todas las máquinas virtuales subyacentes del servicio y se usarán para acceder a los recursos *dentro* de la red virtual. Se usará una dirección VIP para acceder a los recursos situados *fuera* de la red virtual. Si los recursos dentro de la red virtual se protegen mediante listas de restricciones de IP, tendrá que especificar el intervalo completo de la subred donde se implementa el servicio API Management para conceder o restringir el acceso desde el servicio.

Más información acerca del [tamaño de subred recomendado](virtual-network-concepts.md#subnet-size).

#### <a name="example"></a>Ejemplo

Si implementa una [unidad de capacidad](api-management-capacity.md) de API Management en el nivel Premium de una red virtual interna, se usarán tres direcciones IP: una para la IP virtual privada y una para cada uno de los DIP de dos máquinas virtuales. Si escala horizontalmente a 4 unidades, se usarán más direcciones IP para DIP adicionales de la subred.  

Si el punto de conexión de destino solo tiene permitido un conjunto fijo de DIP, se producirán errores de conexión si agrega nuevas unidades en el futuro. Por este motivo y dado que la subred está totalmente bajo su control, se recomienda permitir la enumeración de toda la subred en el back-end.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre:

* [Configuración de red al configurar Azure API Management en una red virtual][Common network configuration problems]
* [Preguntas más frecuentes sobre la red virtual](../virtual-network/virtual-networks-faq.md)
* [Creating a record in DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10)) (Creación de un registro en DNS)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/updated-api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/updated-api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/updated-api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags

