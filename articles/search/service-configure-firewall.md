---
title: Configuración de un firewall de IP
titleSuffix: Azure Cognitive Search
description: Configure directivas de control de IP para restringir el acceso al servicio Azure Cognitive Search a direcciones IP específicas.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: e403a71525a8400f47dee01c14ac192c13ab3826
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223200"
---
# <a name="configure-an-ip-firewall-for-azure-cognitive-search"></a>Configuración de un firewall de IP para Azure Cognitive Search

Azure Cognitive Search admite reglas de IP para el acceso de entrada a través de un firewall, de forma similar a las reglas de IP que encontrará en un grupo de seguridad de red virtual de Azure. Al aprovechar las reglas de IP, puede restringir el acceso del servicio de búsqueda a un conjunto aprobado de máquinas y servicios en la nube. El acceso a los datos almacenados en el servicio de búsqueda desde los conjuntos aprobados de máquinas y servicios seguirá necesitando que el autor de la llamada presente un token de autorización válido.

Puede establecer reglas de IP en Azure Portal, como se describe en este artículo, en los servicios de búsqueda aprovisionados en el nivel Básico y superiores. También puede usar la [API de REST de administración, versión 2020-03-13](/rest/api/searchmanagement/), [Azure PowerShell](/powershell/module/az.search) o la [CLI de Azure](/cli/azure/search).

<a id="configure-ip-policy"></a> 

## <a name="set-ip-ranges-in-azure-portal"></a>Establecimiento de intervalos IP en Azure Portal

Para establecer la directiva de control de acceso IP en Azure Portal, vaya a la página del servicio Azure Cognitive Search y seleccione **Redes** en el panel de navegación izquierdo. La conectividad de red del punto de conexión debe ser **Acceso público**. Si la conectividad está establecida en **Acceso privado** o **Shared Private Access** (Acceso privado compartido), solo puede obtener acceso al servicio de búsqueda a través de un punto de conexión privado.

:::image type="content" source="media/service-configure-firewall/azure-portal-firewall.png" alt-text="Captura de pantalla que muestra cómo configurar un firewall de dirección IP en Azure Portal" border="true":::

Azure Portal proporciona la capacidad para especificar direcciones IP e intervalos de direcciones IP en formato CIDR. Un ejemplo de notación CIDR es 8.8.8.0/24, que representa las direcciones IP que van de 8.8.8.0 a 8.8.8.255.

Después de habilitar la directiva de control de acceso de direcciones IP en el servicio de Azure Cognitive Search, se rechazan todas las solicitudes al plano de datos desde máquinas que estén fuera de la lista permitida de rangos de direcciones IP. 

## <a name="allow-access-from-azure-portal"></a>Permitir el acceso desde Azure Portal

De forma predeterminada, cuando se configuran reglas de IP, algunas características de Azure Portal están deshabilitadas. Podrá ver y administrar la información de nivel de servicio, pero el acceso del portal a índices, indexadores y otros recursos de nivel superior está restringido.

Para conservar la administración del servicio a través del portal, en **Excepciones**, seleccione la opción "Permitir acceso". Como alternativa, use la [extensión de VS Code](https://aka.ms/vscode-search) para administrar el contenido.

## <a name="allow-access-from-your-client"></a>Permitir el acceso desde el cliente

Las aplicaciones cliente que insertan solicitudes de indexación y consulta en el servicio de búsqueda deben representarse en un intervalo IP. En Azure, generalmente puede determinar la dirección IP haciendo ping al FQDN de un servicio (por ejemplo, `ping <your-search-service-name>.search.windows.net` devolverá la dirección IP de un servicio de búsqueda). 

Proporcionar direcciones IP para los clientes garantiza que la solicitud no se rechace directamente; sin embargo, para un acceso correcto al contenido y las operaciones, también es necesaria la autorización. Use una de las metodologías siguientes para autenticar la solicitud:

+ [Autenticación basada en claves](search-security-api-keys.md), donde se proporciona una clave de API de consulta o administrador en la solicitud
+ [Autorización basada en roles](search-security-rbac.md), donde el autor de la llamada es miembro de un rol de seguridad en un servicio de búsqueda y la [aplicación registrada presenta un token de OAuth](search-howto-aad.md) desde Azure Active Directory.

### <a name="rejected-requests"></a>Solicitudes rechazadas

Cuando las solicitudes proceden de direcciones IP que no están en la lista de permitidas, se devuelve una respuesta genérica **403 Prohibido** sin detalles adicionales.

## <a name="next-steps"></a>Pasos siguientes

Si la aplicación cliente es una aplicación web estática en Azure, aprenda a determinar su intervalo IP para su inclusión en una regla de firewall de IP del servicio de búsqueda.

> [!div class="nextstepaction"]
> [Direcciones IP de entrada y salida en Azure App Service](../app-service/overview-inbound-outbound-ips.md)