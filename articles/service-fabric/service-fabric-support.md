---
title: Más información sobre las opciones de soporte técnico de Azure Service Fabric
description: Versiones de clúster de Azure Service Fabric compatibles y vínculos para presentar incidencias de soporte técnico
author: erikadoyle
ms.topic: troubleshooting
ms.date: 5/17/2021
ms.author: edoyle
ms.custom: support-help-page
ms.openlocfilehash: 3b84f85a01ebaff9c3901672d31a135586280e13
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633546"
---
# <a name="azure-service-fabric-support-options"></a>Opciones de soporte técnico de Azure Service Fabric

Hemos creado una serie de opciones de solicitud de soporte técnico para satisfacer las necesidades de administración de los clústeres y las cargas de trabajo de aplicación de Service Fabric, en función de la urgencia de la ayuda necesaria y de la gravedad del problema.

## <a name="self-help-troubleshooting"></a>Autoayuda para la solución de problemas
<div class='icon is-large'>
    <img alt='Self help content' src='./media/logos/doc-logo.png'>
</div>

Este es el material al que hacen referencia los servicios de asistencia al cliente cuando se crea un vale, los ingenieros de confiabilidad del sitio de Service Fabric para responder a un incidente y los usuarios al detectar soluciones a problemas activos del sistema.

Para obtener una lista completa de contenido de autoayuda para la solución de problemas, vea [Guías de solución de problemas de Service Fabric](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides).

## <a name="create-an-azure-support-request"></a>Creación de una solicitud de soporte técnico de Azure
<div class='icon is-large'>
    <img alt='Azure support' src='./media/logos/azure-logo.png'>
</div>

Para notificar los problemas relacionados con el clúster de Service Fabric que se ejecuta en Azure, abra una incidencia de soporte técnico [en Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) o en el [portal de soporte técnico de Microsoft](https://support.microsoft.com/oas/default.aspx?prid=16146).

Más información sobre:

- [Opciones de soporte técnico de Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Soporte técnico Premier de Microsoft](https://support.microsoft.com/premier).

> [!Note]
> Los clústeres que se ejecutan en un nivel de confiabilidad Bronze o clúster de un solo nodo le permitirán ejecutar solo cargas de trabajo de prueba. Si experimenta problemas con un clúster que se ejecuta en una confiabilidad de nivel Bronze o clúster de un solo nodo, el equipo de soporte técnico de Microsoft le ayudará a mitigar el problema, pero no llevará a cabo un análisis de causa principal. Para obtener más información, consulte las [características de confiabilidad del clúster](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster).
>
> Para obtener más detalles sobre lo que es necesario para un clúster listo para producción, consulte la [lista de comprobación sobre la preparación para la producción](./service-fabric-production-readiness-checklist.md).

<a id="getlivesitesupportonprem"></a>

## <a name="create-a-support-request-for-standalone-service-fabric-clusters"></a>Creación de una solicitud de soporte técnico para clústeres de Service Fabric independientes
<div class='icon is-large'>
    <img alt='Azure support' src='./media/logos/azure-logo.png'>
</div>

Si desea informar sobre problemas relacionados con los clústeres de Service Fabric que se ejecutan de forma local o en otras nubes, puede abrir una incidencia de soporte técnico en el [Portal de soporte técnico de Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Más información sobre:

- [Soporte técnico profesional para Microsoft local](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Soporte técnico Premier de Microsoft](https://support.microsoft.com/en-us/premier).

## <a name="post-a-question-to-microsoft-qa"></a>Publicación de una pregunta en Microsoft Q&A
<div class='icon is-large'>
    <img alt='Microsoft Q&A' src='./media/logos/microsoft-logo.png'>
</div>   

Obtenga respuestas a las preguntas sobre Service Fabric directamente de los ingenieros de Microsoft, los profesionales más valiosos (MVP) de Azure y los miembros de nuestra comunidad de expertos.

[Microsoft Q&A](/answers/topics/azure-service-fabric.html) es el origen recomendado de Azure del soporte técnico de la comunidad.

Si no encuentra una respuesta a su problema en Microsoft Q&A, envíe una pregunta nueva. Asegúrese de publicar su pregunta con la etiqueta [**azure-service-fabric**](/answers/topics/azure-service-fabric.html). Estas son algunas sugerencias de Microsoft Q&A para escribir [preguntas de alta calidad](/answers/articles/24951/how-to-write-a-quality-question.html).

## <a name="open-a-github-issue"></a>Apertura de una incidencia en GitHub
<div class='icon is-large'>
    <img alt='GitHub-image' src='./media/logos/github-logo.png'>
</div>

Informe de los problemas de Azure Service Fabric en el [GitHub de Service Fabric](https://github.com/microsoft/service-fabric/issues). Este repositorio está diseñado para informar y rastrear problemas con Azure Service Fabric y para generar solicitudes de funciones pequeñas. **No use este medio para informar problemas con el sitio activo**.

## <a name="check-the-stackoverflow-forum"></a>Consulta del foro StackOverflow
<div class='icon is-large'>
    <img alt='Stack Overflow' src='./media/logos/stack-overflow-logo.png'>
</div>

La etiqueta `azure-service-fabric` de [StackOverflow][stackoverflow] se usa para formular preguntas generales sobre cómo funciona la plataforma y cómo usarla para llevar a cabo determinadas tareas.

## <a name="stay-informed-of-updates-and-new-releases"></a>Mantenerse informado sobre las actualizaciones y nuevas versiones

<div class='icon is-large'>
    <img alt='Stay informed' src='./media/logos/updates-logo.png'>
</div>

Obtenga información sobre las actualizaciones de productos, el mapa de ruta y los anuncios importantes en [Actualizaciones de Azure](https://azure.microsoft.com/updates/?product=service-fabric).

Para consultar las últimas versiones y actualizaciones de los SDK y el entorno de ejecución de Service Fabric, vea [Versiones de Service Fabric](release-notes.md).



## <a name="next-steps"></a>Pasos siguientes

[Versiones admitidas de Service Fabric](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure