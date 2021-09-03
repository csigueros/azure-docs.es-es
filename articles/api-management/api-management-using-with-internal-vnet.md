---
title: Conexión a una red virtual interna mediante Azure API Management
titleSuffix: Azure API Management
description: Aprenda a instalar y configurar Azure API Management en una red virtual interna.
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: how-to
ms.date: 06/08/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b0f67862352f2354dbdb5439542229dbee67ae00
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113107494"
---
# <a name="connect-to-an-internal-virtual-network-using-azure-api-management"></a>Conexión a una red virtual interna mediante Azure API Management 
Con las redes virtuales de Azure (VNET), Azure API Management puede administrar API inaccesibles por Internet mediante varias tecnologías de VPN para realizar la conexión. Puede implementar API Management mediante modos [externos](./api-management-using-with-vnet.md) o internos. En este artículo, aprenderá a implementar API Management en el modo de red virtual interno.

Cuando API Management se implementa en modo de red virtual interno, solo puede ver los siguientes puntos de conexión de servicio dentro de una red virtual cuyo acceso controle.
* Puerta de enlace de proxy
* Portal para desarrolladores
* Administración directa
* Git 

> [!NOTE]
> Ninguno de los puntos de conexión de servicio está registrado en el DNS público. Los puntos de conexión de servicio permanecerán inaccesibles hasta que [configure DNS](#apim-dns-configuration) para la red virtual.

Use API Management en modo interno para:

* Conseguir que las API hospedadas en el centro de datos privado sean accesibles para terceros desde fuera de este centro si usa conexiones de VPN de Azure o Azure ExpressRoute.
* Puede permitir escenarios de nube híbrida exponiendo las API basadas en la nube y las API locales a través de una puerta de enlace común.
* Administrar las API hospedadas en diversas ubicaciones geográficas, mediante un único punto de conexión de puerta de enlace.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Requisitos previos

+ **Una suscripción activa a Azure**. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Una instancia de Azure API Management (SKU admitidas: Developer, Premium y Isolated)** . Para más información, vea [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

Cuando se implementa un servicio API Management en una red virtual, se usa una [lista de puertos](./api-management-using-with-vnet.md#required-ports) que se deben abrir.

## <a name="creating-an-api-management-in-an-internal-vnet"></a><a name="enable-vpn"> </a>Creación de una instancia de API Management en una red virtual interna
En las redes virtuales internas, el servicio API Management se hospeda detrás de un equilibrador de carga interno. La SKU del equilibrador de carga depende de la API de administración que se use para crear el servicio. Para más información, consulte [SKU de Azure Load Balancer](../load-balancer/skus.md).

| Versión de API | Hospedado detrás de |
| ----------- | ------------- |
| 2020-12-01 | Un equilibrador de carga interno en la SKU Básica |
| 2020-01-01-preview, con una dirección IP pública de la suscripción | Una SKU Estándar de equilibrador de carga |

### <a name="enable-a-vnet-connection-using-the-azure-portal"></a>Habilitación de una conexión de red virtual a red virtual mediante Azure Portal

1. Navegue a la instancia de Azure API Management en [Azure Portal](https://portal.azure.com/).
1. Seleccione **Red virtual**.
1. Configure el tipo de acceso **Interno**. Para ver los pasos detallados, consulte [Habilitación de la conectividad de red virtual mediante Azure Portal](api-management-using-with-vnet.md#enable-vnet-connectivity-using-the-azure-portal).

    ![Menú de configuración de una instancia de Azure API Management en una red virtual interna][api-management-using-internal-vnet-menu]

4. Seleccione **Guardar**.

Después de que la implementación se realice correctamente, debería ver la dirección IP virtual **privada** y la dirección IP virtual **pública** del servicio API Management en el panel **Información general**. 

| Dirección IP virtual | Descripción |
| ----- | ----- |
| **Dirección IP virtual privada** | Una dirección IP con equilibrio de carga dentro de la subred delegada de API Management, por la que se puede acceder a los puntos de conexión `gateway`, `portal`, `management` y `scm`. |  
| **Dirección IP virtual pública** | *Solo* se usa para el tráfico del plano de control al punto de conexión `management` por medio de `port 3443`. Se puede bloquear a la etiqueta de servicio [ApiManagement][ServiceTags]. |  

![Panel de API Management con una red virtual interna configurada][api-management-internal-vnet-dashboard]

> [!NOTE]
> Como la dirección URL de puerta de enlace no está registrada en el DNS público, la consola de prueba disponible en Azure Portal no funcionará con un servicio implementado en una red virtual **interna**. En su lugar, use la consola de prueba que se ofrece en el **Portal para desarrolladores**.

### <a name="deploy-api-management-into-vnet"></a><a name="deploy-apim-internal-vnet"> </a> Implementación de API Management en la red virtual

También puede habilitar la conectividad de la red virtual con los siguientes métodos.


### <a name="api-version-2020-12-01"></a>Versión de API 2020-12-01

* [Plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet) de Resource Manager

     [![Implementar en Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet%2Fazuredeploy.json)

* Cmdlets de Azure PowerShell: [creación](/powershell/module/az.apimanagement/new-azapimanagement) o [actualización](/powershell/module/az.apimanagement/update-azapimanagementregion) de una instancia de API Management en una red virtual

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>Configuración de DNS
En el modo de red virtual externa, Azure administra el DNS. En el modo de red virtual interna, tendrá que administrar su propio DNS. Es recomendable que:
1. Configure una zona privada de Azure DNS.
1. Vincule la zona privada de Azure DNS a la red virtual en la que ha implementado el servicio API Management. 

Aprenda a [configurar una zona privada en Azure DNS](../dns/private-dns-getstarted-portal.md).

> [!NOTE]
> El servicio API Management no escucha las solicitudes procedentes de direcciones IP. Solo responde a las solicitudes dirigidas al nombre de host establecido en los puntos de conexión de servicio. Estos puntos de conexión incluyen lo siguiente:
> * Puerta de enlace
> * El Portal de Azure
> * Portal para desarrolladores
> * Punto de conexión de administración directa
> * Git

### <a name="access-on-default-host-names"></a>Acceso de nombres de host predeterminados
Al crear un servicio de API Management (por ejemplo, `contosointernalvnet`) se configuran de forma predeterminada los siguientes puntos de conexión de servicio:

| Punto de conexión | Configuración de punto de conexión |
| ----- | ----- |
| Puerta de enlace o proxy | `contosointernalvnet.azure-api.net` |
| Portal para desarrolladores | `contosointernalvnet.portal.azure-api.net` |
| Nuevo portal para desarrolladores | `contosointernalvnet.developer.azure-api.net` |
| Punto de conexión de administración directa | `contosointernalvnet.management.azure-api.net` |
| Git | `contosointernalvnet.scm.azure-api.net` |

Para acceder a estos puntos de conexión de servicio de API Management, puede crear una máquina virtual en una subred conectada a la red virtual en la que esté implementado API Management. Si la dirección IP virtual interna del servicio es 10.1.0.5, puede asignar el archivo de hosts, `%SystemDrive%\drivers\etc\hosts`, de esta forma:

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

1. Configure nombres de dominio personalizados para todos los puntos de conexión de servicio, como se muestra en la imagen siguiente:

   ![Configuración de un dominio personalizado para API Management][api-management-custom-domain-name]

2. Cree registros en el servidor DNS para acceder a los puntos de conexión accesibles desde la red virtual.

## <a name="routing"></a><a name="routing"> </a> Enrutamiento

* Se reservará una dirección IP virtual *privada* con equilibrio de carga del intervalo de subred (DIP) para acceder a los puntos de conexión del servicio API Management desde la red virtual. 
    * Busque esta dirección IP privada en el panel Información general del servicio en Azure Portal. 
    * Registre esta dirección con los servidores DNS que usa la red virtual.
* También se reservará una dirección IP *pública* (VIP) con equilibrio de carga para proporcionar acceso al punto de conexión de servicio de administración por medio de `port 3443`. 
    * Busque esta dirección IP pública en el panel Información general del servicio en Azure Portal. 
    * Use solo la dirección IP *pública* para el tráfico del plano de control al punto `management` de conexión por medio de `port 3443`. 
    * Esta dirección IP se puede bloquear a la etiqueta de servicio [ApiManagement][ServiceTags].
* Se asignarán direcciones DIP a todas las máquinas virtuales del servicio y se usarán para acceder a los recursos *dentro* de la red virtual. Se usará una dirección VIP para acceder a los recursos situados *fuera* de la red virtual. Si los recursos dentro de la red virtual se protegen mediante listas de restricciones de IP, tendrá que especificar el intervalo completo de la subred donde se implementa el servicio API Management para conceder o restringir el acceso desde el servicio.
* Las direcciones IP privadas y públicas con equilibrio de carga pueden encontrarse en la hoja de información general de Azure Portal.
* Si quita o agrega el servicio en la red virtual, pueden cambiar las direcciones IP asignadas para el acceso público y privado. Es posible que tenga que actualizar los registros de DNS, las reglas de enrutamiento y las listas de restricciones de IP dentro de la red virtual.

## <a name="related-content"></a><a name="related-content"> </a>Contenido relacionado
Para más información, vea los siguientes artículos:
* [Problemas comunes de configuración de red al establecer Azure API Management en una red virtual][Common network configuration problems]
* [Preguntas más frecuentes sobre la red virtual](../virtual-network/virtual-networks-faq.md)
* [Creating a record in DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10)) (Creación de un registro en DNS)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/updated-api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/updated-api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/updated-api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
