---
title: Hospedaje de sitios web estáticos en Azure Storage
description: Azure Storage le ofrece hospedaje de sitios web estáticos, lo que le proporciona una solución rentable y escalable para hospedar aplicaciones web modernas.
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 09/04/2020
ms.subservice: blobs
ms.custom: devx-track-js
ms.openlocfilehash: 388b84dc831984cf03b91ff16e4cfb8962b88c73
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662730"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hospedaje de sitios web estáticos en Azure Storage

Puede proporcionar contenido estático (HTML, CSS, JavaScript y archivos de imagen) directamente desde un contenedor de almacenamiento llamado *$web*. El hospedaje de contenido en Azure Storage permite usar arquitecturas sin servidor que incluyen [Azure Functions](../../azure-functions/functions-overview.md) y otros servicios de plataforma como servicio (PaaS). El hospedaje de sitios web estáticos de Azure Storage es una opción excelente en los casos en los que no es necesario que un servidor web represente contenido.

Las [aplicaciones web estáticas de App Service](https://azure.microsoft.com/services/app-service/static/) es una excelente alternativa al hospedaje de sitios web estáticos de Azure Storage y también es adecuado en los casos en los que no es necesario que un servidor web represente contenido. Las aplicaciones web estáticas de App Service proporcionan un flujo de trabajo de integración continua y entrega continua (CI/CD) totalmente administrado desde el origen de GitHub a la implementación global.

Si necesita un servidor web para representar contenido, puede usar [Azure App Service](https://azure.microsoft.com/services/app-service/).

## <a name="setting-up-a-static-website"></a>Configuración de un sitio web estático

El hospedaje de sitios web estáticos es una característica que permite habilitar la cuenta de almacenamiento.

Para habilitar el hospedaje de sitios web estáticos, seleccione el nombre del archivo predeterminado y, opcionalmente, proporcione una ruta de acceso a una página 404 personalizada. Si aún no existe un contenedor de almacenamiento de blobs denominado **$web** en la cuenta, se creará uno. Agregue los archivos del sitio a ese contenedor.

Para obtener instrucciones detalladas, consulte [Hospedaje de sitios web estáticos en Azure Storage](storage-blob-static-website-how-to.md).

![Métrica de métricas de sitios web estáticos de Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Los archivos del contenedor **$web** distinguen mayúsculas de minúsculas, se proporcionan mediante solicitudes de acceso anónimo y solo están disponibles a través operaciones de lectura.

## <a name="uploading-content"></a>Carga de contenido

Puede usar cualquiera de estas herramientas para cargar contenido en el contenedor **$web**:

> [!div class="checklist"]
> - [CLI de Azure](storage-blob-static-website-how-to.md?tabs=azure-cli)
> - [Módulo de Azure PowerShell](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> - [AzCopy](../common/storage-use-azcopy-v10.md)
> - [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/)
> - [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> - [Extensión de Visual Studio Code](- https://channel9.msdn.com/Shows/Docs-Azure/Deploy-static-website-to-Azure-from-Visual-Studio-Code/player)

## <a name="viewing-content"></a>Visualización de contenido

Los usuarios pueden ver contenido del sitio desde un explorador usando la dirección URL pública del sitio web. Puede buscar la dirección URL usando Azure Portal, CLI de Azure o PowerShell. Consulte cómo [Búsqueda de la dirección URL del sitio web](storage-blob-static-website-how-to.md#portal-find-url).

El documento del índice que especifique al habilitar el hospedaje de sitios web estáticos aparece cuando los usuarios abren el sitio y no especifican un archivo concreto (por ejemplo, `https://contosoblobaccount.z22.web.core.windows.net`).

Si el servidor devuelve un error 404 y no se ha especificado un documento de error al habilitar el sitio web, se devuelve una página 404 predeterminada al usuario.

> [!NOTE]
> El [uso compartido de recursos entre orígenes (CORS) para Azure Storage](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) no se admite para los sitios web estáticos.

### <a name="secondary-endpoints"></a>Puntos de conexión secundarios

Si configura la [redundancia en una región secundaria](../common/storage-redundancy.md#redundancy-in-a-secondary-region), también puede tener acceso al contenido del sitio web mediante un punto de conexión secundario. Dado que los datos se replican en las regiones secundarias de forma asincrónica, los archivos que están disponibles en el punto de conexión secundario no están siempre sincronizados con los archivos que están disponibles en el punto de conexión principal.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Impacto de la configuración del nivel de acceso público del contenedor web

Puede modificar el nivel de acceso público del contenedor **$web**, lo cual no afecta al punto de conexión principal del sitio web estático porque estos archivos se proporcionan a través de solicitudes de acceso anónimo. Eso significa acceso público (de solo lectura) a todos los archivos.

En la siguiente captura de pantalla se muestra la configuración del nivel de acceso público en Azure Portal:

![Captura de pantalla que muestra cómo establecer el nivel de acceso público en el portal](./media/anonymous-read-access-configure/configure-public-access-container.png)

Aunque el punto de conexión principal del sitio web estático no se ve afectado, un cambio en el nivel de acceso público afecta al punto de conexión principal de Blob service.

Por ejemplo, si cambia el nivel de acceso público del contenedor **$web** de **Privado (sin acceso anónimo)** a **Blob (acceso anónimo de lectura solo para blobs)** , el nivel de acceso público al punto de conexión principal del sitio web estático `https://contosoblobaccount.z22.web.core.windows.net/index.html` no cambia.

Sin embargo, el acceso público al punto de conexión principal de Blob service `https://contosoblobaccount.blob.core.windows.net/$web/index.html` cambia de privado a público. Ahora los usuarios pueden abrir ese archivo mediante cualquiera de estos dos punto de conexión.

Deshabilitar el acceso público en una cuenta de almacenamiento no afecta a los sitios web estáticos que se hospedan en ella. Para más información, consulte [Configuración del acceso de lectura público anónimo a contenedores y blobs](anonymous-read-access-configure.md).

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Asignación de un dominio personalizado a una dirección URL de un sitio web estático

Puede hacer que el sitio web estático esté disponible a través de un dominio personalizado.

Es más fácil habilitar el acceso HTTP para un dominio personalizado, ya que Azure Storage lo admite de forma nativa. Para habilitar HTTPS, tendrá que usar Azure CDN porque Azure Storage no admite de forma nativa HTTPS con dominios personalizados. Para obtener instrucciones pormenorizadas, consulte [Asignación de un dominio personalizado a un punto de conexión de Azure Blob Storage](storage-custom-domain-name.md).

Si la cuenta de almacenamiento está configurada para [requerir la transferencia segura](../common/storage-require-secure-transfer.md) a través de HTTPS, los usuarios deben utilizar el punto de conexión HTTPS.

> [!TIP]
> Consider la posibilidad de hospedar el dominio en Azure. Para más información, consulte [Hospedaje de un dominio en Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Adición de encabezados HTTP

No hay forma de configurar encabezados como parte de la característica de sitio web estático. Sin embargo, puede usar Azure CDN para agregar encabezados y anexar (o sobrescribir) sus valores. Consulte [Referencia del motor de reglas estándar de Azure CDN](../../cdn/cdn-standard-rules-engine-reference.md).

Si quiere usar encabezados para controlar el almacenamiento en caché, consulte [Control del comportamiento del almacenamiento en caché de Azure CDN con reglas de almacenamiento en caché](../../cdn/cdn-caching-rules.md).

## <a name="multi-region-website-hosting"></a>Hospedaje de sitios web de varias regiones

Si planea hospedar un sitio web en varias zonas geográficas, se recomienda usar [Content Delivery Network](../../cdn/index.yml) para el almacenamiento en caché regional. Use [Azure Front Door](../../frontdoor/index.yml) para proporcionar contenido diferente en cada región. También proporciona funcionalidad de conmutación por error. [No se recomienda](../../traffic-manager/index.yml) Azure Traffic Manager si tiene previsto usar un dominio personalizado. Pueden surgir problemas debido a cómo Azure Storage comprueba los nombres de dominio personalizados.

## <a name="permissions"></a>Permisos

El permiso para habilitar el sitio web estático es Microsoft.Storage/storageAccounts/blobServices/write o clave compartida.  Los roles integrados que proporcionan este acceso incluyen Colaborador de la cuenta de almacenamiento.

## <a name="pricing"></a>Precios

Puede habilitar el hospedaje de sitios web estáticos de forma gratuita. Se le factura solo por el almacenamiento de blobs que el sitio usa y por los costes de las operaciones. Para obtener más información acerca de los precios de Azure Blob Storage, consulte la [página de precios de Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Métricas

Puede habilitar métricas en páginas de sitios web estáticos. Después de habilitar las métricas, las estadísticas de tráfico de los archivos en el contenedor **$web** aparecen en el panel de métricas.

Para habilitar métricas en las páginas de su sitio web estático, consulte [Enable metrics on static website pages](storage-blob-static-website-how-to.md#metrics) (Habilitación de métricas en páginas de sitios web estáticos).

## <a name="feature-support"></a>Compatibilidad de características

En esta tabla se muestra cómo se admite esta característica en la cuenta y el impacto en la compatibilidad al habilitar determinadas funcionalidades.

| Tipo de cuenta de almacenamiento                | Blob Storage (compatibilidad predeterminada)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| De uso general estándar, v2 | ![Sí](../media/icons/yes-icon.png) |![Sí](../media/icons/yes-icon.png)              | ![Sí](../media/icons/yes-icon.png) |
| Blobs en bloques Premium          | ![Sí](../media/icons/yes-icon.png)|![Sí](../media/icons/yes-icon.png) | ![Sí](../media/icons/yes-icon.png) |

<sup>1</sup> Tanto Data Lake Storage Gen2 como el protocolo Network File System (NFS) 3.0 necesitan una cuenta de almacenamiento con un espacio de nombres jerárquico habilitado.

## <a name="faq"></a>Preguntas más frecuentes

##### <a name="does-the-azure-storage-firewall-work-with-a-static-website"></a>¿Funciona el firewall de Azure Storage con un sitio web estático?

Sí. Se admiten [reglas de seguridad de red](../common/storage-network-security.md) de la cuenta de almacenamiento, como firewalls basados en IP y VNET, para el punto de conexión del sitio web estático, y se pueden usar para proteger el sitio web.

##### <a name="do-static-websites-support-azure-active-directory-azure-ad"></a>¿Los sitios web estáticos admiten Azure Active Directory (Azure AD)?

No. Un sitio web estático solo admite acceso de lectura público anónimo para los archivos del contenedor **$web**.

##### <a name="how-do-i-use-a-custom-domain-with-a-static-website"></a>¿Cómo uso un dominio personalizado con un sitio web estático?

Puede configurar un [dominio personalizado](./static-website-content-delivery-network.md) con un sitio web estático mediante [Azure Content Delivery Network (Azure CDN)](./storage-custom-domain-name.md#map-a-custom-domain-with-https-enabled). Azure CDN proporciona latencias bajas coherentes al sitio web desde cualquier lugar del mundo.

##### <a name="how-do-i-use-a-custom-ssl-certificate-with-a-static-website"></a>¿Cómo se usa el certificado SSL personalizado con un sitio web estático?

Puede configurar un certificado [SSL personalizado](./static-website-content-delivery-network.md) con un sitio web estático mediante [Azure CDN](./storage-custom-domain-name.md#map-a-custom-domain-with-https-enabled). Azure CDN proporciona latencias bajas coherentes al sitio web desde cualquier lugar del mundo.

##### <a name="how-do-i-add-custom-headers-and-rules-with-a-static-website"></a>¿Cómo se agregan encabezados y reglas personalizados con un sitio web estático?

Puede configurar el encabezado host para un sitio web estático mediante [Azure CDN - Verizon Premium](../../cdn/cdn-verizon-premium-rules-engine.md). Nos interesa conocer sus comentarios [aquí](https://feedback.azure.com/forums/217298-storage/suggestions/34959124-allow-adding-headers-to-static-website-hosting-in).

##### <a name="why-am-i-getting-an-http-404-error-from-a-static-website"></a>¿Por qué obtengo un error HTTP 404 de un sitio web estático?

Esto puede ocurrir si hace referencia a un nombre de archivo con mayúsculas y minúsculas incorrectas. Por ejemplo, `Index.html` en lugar de `index.html`. Los nombres de archivo y las extensiones en la URL de un sitio web estático distinguen mayúsculas de minúsculas, aunque entreguen a través de HTTP. Esto también puede ocurrir si el punto de conexión de Azure CDN aún no está aprovisionado. Espere hasta 90 minutos después de aprovisionar una nueva red de Azure CDN para que se complete la propagación.

##### <a name="why-isnt-the-root-directory-of-the-website-not-redirecting-to-the-default-index-page"></a>¿Por qué el directorio raíz del sitio web no redirige a la página de índice predeterminada?

En Azure Portal, abra la página de configuración del sitio web estático de su cuenta y busque el nombre y la extensión que se establecieron en el campo **Nombre del documento de índice**. Asegúrese de que este nombre sea exactamente el mismo que el nombre del archivo ubicado en el contenedor **$web** de la cuenta de almacenamiento. Los nombres de archivo y las extensiones en la URL de un sitio web estático distinguen mayúsculas de minúsculas, aunque entreguen a través de HTTP.

## <a name="next-steps"></a>Pasos siguientes

- [Hospedaje de sitios web estáticos en Azure Storage](storage-blob-static-website-how-to.md)
- [Asignación de un dominio personalizado a un punto de conexión de Azure Blob Storage](storage-custom-domain-name.md)
- [Funciones de Azure](../../azure-functions/functions-overview.md)
- [Azure App Service](../../app-service/overview.md)
- [Crear la primera aplicación web sin servidor](/azure/functions/tutorial-static-website-serverless-api-with-database)
- [Tutorial: Hospedaje del dominio en Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
