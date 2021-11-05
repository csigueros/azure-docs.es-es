---
title: 'Microsoft Defender para registros de contenedor: ventajas y características'
description: Obtenga información sobre las ventajas y características de Microsoft Defender para registros de contenedor.
author: memildin
ms.author: memildin
ms.date: 10/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 627ac6bacbd9d6f1c3649229bd29bbcd27d3e93b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131463836"
---
# <a name="introduction-to-microsoft-defender-for-container-registries"></a>Introducción a Microsoft Defender para registros de contenedor

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Container Registry (ACR) es un servicio de registro de Docker privado y administrado que almacena y administra las imágenes de contenedor de las implementaciones de Azure en un registro central. Se basa en el registro 2.0 de Docker de código abierto.

Para proteger los registros basados en Azure Resource Manager de la suscripción, habilite **Microsoft Defender para registros de contenedor** en el nivel de suscripción. Después, Defender para nube examinará las imágenes insertadas en el registro o importadas en el registro, así como las extraídas en los últimos 30 días. Se le cobrará por cada imagen que se examine, una vez por imagen.

## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Disponible con carácter general|
|Precios:|**Microsoft Defender para registros de contenedor** se factura como se muestra en la [página de precios](https://azure.microsoft.com/pricing/details/security-center/)|
|Registros e imágenes compatibles:|Imágenes de Linux en registros de ACR accesibles desde la red pública de Internet con acceso al shell<br>[Registros de ACR protegidos con Azure Private Link](../container-registry/container-registry-private-link.md)|
|Registros e imágenes no compatibles:|Imágenes de Windows<br>Registros "privados" (a menos que se conceda acceso a los [servicios de confianza](../container-registry/allow-access-trusted-services.md#trusted-services))<br>Imágenes excesivamente minimalistas, como las imágenes [base de Docker](https://hub.docker.com/_/scratch/) o imágenes "sin distribución" que solo contienen una aplicación y sus dependencias en tiempo de ejecución sin un administrador de paquetes, shell o sistema operativo.<br>Imágenes con la [especificación del formato de imagen Open Container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md)|
|Roles y permisos necesarios:|**Lector de seguridad** y [roles y permisos de Azure Container Registry](../container-registry/container-registry-roles.md)|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png" border="false"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/yes-icon.png" border="false"::: Nacionales o soberanas (Azure Government, Azure China 21Vianet)|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-container-registries"></a>¿Cuáles son las ventajas de Microsoft Defender para registros de contenedor?

Defender para nube identifica registros de ACR basados en Azure Resource Manager en la suscripción y brinda una función fluida de evaluación y administración de vulnerabilidades nativa de Azure para las imágenes del registro.

**Microsoft Defender para registros de contenedor** incluye un detector de vulnerabilidades para examinar las imágenes de los registros de Azure Container Registry basados en Azure Resource Manager y proporciona una mayor visibilidad de las vulnerabilidades de las imágenes. El detector integrado lo proporciona el proveedor de análisis de vulnerabilidades líder del sector, Qualys.

Cuando se encuentran problemas (por Qualys o Defender para la nube), se le notificará en el panel de protección de cargas de trabajo. Por cada vulnerabilidad, Defender para nube proporciona recomendaciones útiles, junto con una clasificación de gravedad e información sobre cómo corregir el problema. Para más información sobre las recomendaciones de Defender para la nube para los contenedores, consulte la [lista de recomendaciones de referencia](recommendations-reference.md#recs-container).

Defender para la nube filtra y clasifica los resultados del análisis. Cuando una imagen es correcta, Defender para la nube la marca como tal. Defender para nube solo genera recomendaciones de seguridad para las imágenes que tienen incidencias sin resolver. Defender para nube proporciona detalles de cada vulnerabilidad detectada y una clasificación de gravedad. Asimismo, ofrece instrucciones sobre cómo corregir las vulnerabilidades específicas encontradas en cada imagen.

Al enviar notificaciones solo cuando hay problemas, Defender para nube reduce las alertas informativas no deseadas.


> [!TIP]
> Para más información sobre las características de seguridad de contenedores de Defender para nube, consulte:
>
> - [Microsoft Defender para la seguridad en la nube y los contenedores](container-security.md)
> - [Introducción a Microsoft Defender para Kubernetes](defender-for-kubernetes-introduction.md)

## <a name="when-are-images-scanned"></a>¿Cuándo se examinan las imágenes?

Hay tres desencadenadores para un examen de imagen:

- **Al insertar**: cada vez que se inserta una imagen en el registro, Defender para registros de contenedor la examina de forma automática. Para desencadenar el examen de una imagen, insértela en el repositorio.

- **Extraído recientemente**: dado que se detectan nuevas vulnerabilidades cada día, **Microsoft Defender para registros de contenedor** también examina semanalmente cualquier imagen que se haya extraído en los últimos 30 días. No se aplican cargos adicionales por los nuevos exámenes; como se ha mencionado anteriormente, se le facturará una vez por imagen.

- **Al importar**: Azure Container Registry tiene herramientas de importación para incorporar imágenes al registro desde Docker Hub, Microsoft Container Registry u otro registro de contenedor de Azure. **Microsoft Defender para registros de contenedor** examina las imágenes admitidas que importe. Obtenga más información en [Importación de imágenes de contenedor en un registro de contenedor](../container-registry/container-registry-import-images.md).
 
El análisis se completa normalmente en 2 minutos, pero puede tardar hasta 15 minutos. Los resultados se publican como recomendaciones de seguridad, como en este caso:

[![Ejemplo de recomendación de Microsoft Defender para nube sobre vulnerabilidades detectadas en una imagen hospedada en Azure Container Registry (ACR).](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-defender-for-cloud-work-with-azure-container-registry"></a>Cómo funciona Defender para nube con Azure Container Registry

A continuación se muestra un diagrama detallado de los componentes y las ventajas de proteger los registros con Defender para nube.

![Información general de alto nivel de Microsoft Defender para nube y Azure Container Registry (ACR).](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq---azure-container-registry-image-scanning"></a>Preguntas frecuentes: Análisis de imágenes de Azure Container Registry

### <a name="how-does-defender-for-cloud-scan-an-image"></a>¿Cómo analiza Defender para nube una imagen?
Defender para nube extrae la imagen del registro y la ejecuta en un espacio aislado con el detector de Qualys. El detector extrae una lista de vulnerabilidades conocidas.

Defender para nube filtra y clasifica los resultados del análisis. Cuando una imagen es correcta, Defender para la nube la marca como tal. Defender para nube solo genera recomendaciones de seguridad para las imágenes que tienen incidencias sin resolver. Al enviar notificaciones solo cuando hay problemas, Defender para nube reduce las alertas informativas no deseadas.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>¿Se pueden obtener los resultados del examen mediante la API REST?
Sí. Los resultados se encuentran en [API REST de valoración secundaria](/rest/api/securitycenter/subassessments/list/). Asimismo, puede usar Azure Resource Graph (ARG), la API similar a Kusto para todos los recursos: una consulta puede recuperar un análisis específico.

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>¿Qué tipos de registros se analizan? ¿Qué tipos se facturan?
Para ver los tipos de registros de contenedor que admite Microsoft Defender para registros de contenedor, consulte [Disponibilidad](#availability).

Si conecta registros no admitidos a su suscripción de Azure, Defender para nube no los examinará y no se facturarán.

### <a name="can-i-customize-the-findings-from-the-vulnerability-scanner"></a>¿Puedo personalizar los resultados del detector de vulnerabilidades?
Sí. Si tiene una necesidad organizativa de omitir un resultado, en lugar de corregirlo, tiene la opción de deshabilitarlo. Los resultados deshabilitados no afectan a la puntuación de seguridad ni generan ruido no deseado.

[Aprenda a crear reglas para deshabilitar los resultados desde la herramienta integrada de valoración de vulnerabilidades](defender-for-container-registries-usage.md#disable-specific-findings-preview).

### <a name="why-is-defender-for-cloud-alerting-me-to-vulnerabilities-about-an-image-that-isnt-in-my-registry"></a>¿Por qué me está avisando Defender para nube de las vulnerabilidades de una imagen que no está en mi registro?
Defender para nube proporciona evaluaciones de vulnerabilidades de cada imagen que se inserta o se extrae en un registro. Algunas imágenes pueden volver a usar etiquetas de una imagen que ya se ha examinado. Por ejemplo, puede reasignar la etiqueta "Más reciente" cada vez que se agrega una imagen a un código hash. En esos casos, la imagen "antigua" todavía existe en el registro y es posible que todavía se pueda extraer mediante su código hash. Si la imagen tiene resultados de seguridad y se extrae, mostrará vulnerabilidades de seguridad.


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Detección de vulnerabilidades en sus imágenes](defender-for-container-registries-usage.md)
