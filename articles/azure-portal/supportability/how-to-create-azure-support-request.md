---
title: Creación de una solicitud de soporte técnico de Azure
description: Los clientes que necesiten asistencia pueden usar Azure Portal para buscar soluciones de autoservicio y para crear y administrar solicitudes de soporte técnico.
ms.topic: how-to
ms.custom: support-help-page
ms.date: 09/30/2021
ms.openlocfilehash: 4598cc16173100749ad3f1bdefd3165020554465
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358267"
---
# <a name="create-an-azure-support-request"></a>Creación de una solicitud de soporte técnico de Azure

Azure le permite crear y administrar solicitudes de soporte técnico, también conocidas como incidencias de soporte técnico. Puede crear y administrar las solicitudes en [Azure Portal](https://portal.azure.com), tal y como se describe en este artículo. También puede crear y administrar solicitudes mediante programación con la [API REST de incidencia de soporte técnico de Azure](/rest/api/support) o con la [CLI de Azure](/cli/azure/azure-cli-support-request).

> [!NOTE]
> La dirección URL de Azure Portal es específica de la nube de Azure donde está implementada la organización.
>
>- Azure Portal para uso comercial: [https://portal.azure.com](https://portal.azure.com)
>- Azure Portal para Alemania: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>- Azure Portal para el gobierno de los Estados Unidos: [https://portal.azure.us](https://portal.azure.us)

Azure proporciona soporte ilimitado para la administración de suscripciones, lo que incluye facturación, ajustes de cuota y transferencias de cuentas. Para obtener soporte técnico, necesita un plan de soporte técnico. Para más información, consulte [Comparar planes de soporte técnico](https://azure.microsoft.com/support/plans).

## <a name="getting-started"></a>Introducción

Puede ir a **Ayuda y soporte técnico** en Azure Portal. Está disponible en el menú de Azure Portal, en el encabezado global o en el menú de recursos de un servicio. Debe tener los permisos adecuados para enviar una solicitud de soporte técnico.

### <a name="azure-role-based-access-control"></a>Control de acceso basado en roles de Azure

Para crear una solicitud de soporte técnico, debe ser un [propietario](../../role-based-access-control/built-in-roles.md#owner), un[colaborador](../../role-based-access-control/built-in-roles.md#contributor) o debe tener asignado el rol de [colaborador de la solicitud de soporte técnico](../../role-based-access-control/built-in-roles.md#support-request-contributor) en el nivel de suscripción. Para crear una solicitud de soporte técnico sin una suscripción, por ejemplo, en un escenario de Azure Active Directory, debe ser [administrador](../../active-directory/roles/permissions-reference.md).

### <a name="go-to-help--support-from-the-global-header"></a>Ir a Ayuda y soporte técnico en el encabezado global

Para iniciar una solicitud de soporte técnico desde cualquier lugar de Azure Portal:

1. Seleccione el signo **?** en el encabezado global, seleccione **Ayuda y soporte técnico**.

   :::image type="content" source="media/how-to-create-azure-support-request/helpandsupportnewlower.png" alt-text="Captura de pantalla del menú Ayuda de Azure Portal":::.

1. Seleccione **Crear solicitud de soporte técnico**. Siga las indicaciones para proporcionar información sobre el problema. Sugeriremos algunas soluciones posibles, recopilaremos los detalles del problema y le ayudaremos a enviar y realizar el seguimiento de la solicitud de soporte técnico.

   :::image type="content" source="media/how-to-create-azure-support-request/newsupportrequest2lower.png" alt-text="Captura de pantalla de la página Ayuda y soporte técnico con el vínculo Crear solicitud de soporte técnico.":::

### <a name="go-to-help--support-from-a-resource-menu"></a>Ir a Ayuda y soporte técnico en un menú de recursos

Para iniciar una solicitud de soporte técnico en el contexto del recurso con el que está trabajando actualmente:

1. En el menú de recursos, en la sección **Soporte técnico y solución de problemas**, seleccione **Nueva solicitud de soporte técnico**.

   :::image type="content" source="media/how-to-create-azure-support-request/incontext2lower.png" alt-text="Captura de pantalla de la opción Nueva solicitud de soporte técnico en el panel de recursos.":::

1. Siga las indicaciones para proporcionarnos la información sobre el problema que tiene. Al iniciar el proceso de solicitud de soporte técnico desde un recurso, algunas opciones se preseleccionan automáticamente.

## <a name="create-a-support-request"></a>Crear una solicitud de soporte

Le guiaremos a través de algunos pasos para recopilar información sobre el problema y ayudarle a resolverlo. Este paso se describe en las secciones siguientes.

### <a name="problem-description"></a>Descripción del problema

El primer paso del proceso de solicitud de soporte técnico es seleccionar un tipo de problema. A continuación, se le pedirá más información, la cual puede variar en función del tipo de problema que haya seleccionado. En la mayoría de los casos, deberá especificar una suscripción, describir brevemente el problema y seleccionar un tipo de problema. Si selecciona **Técnico**, deberá especificar el servicio con el que se relaciona el problema. En función del servicio, verá opciones adicionales para **Tipo de problema** y **Subtipo de problema**.

:::image type="content" source="media/how-to-create-azure-support-request/basics2lower.png" alt-text="Captura de pantalla del paso Descripción del problema del proceso de solicitud de soporte técnico.":::

Una vez que haya proporcionado todos estos detalles, seleccione **Siguiente**.

### <a name="recommended-solution"></a>Solución recomendada

En función de la información proporcionada, le mostraremos las soluciones recomendadas que puede usar para intentar resolver el problema. En algunos casos, es posible que incluso ejecutemos un diagnóstico rápido. Las soluciones están escritas por ingenieros de Azure y resolverán la mayoría de los problemas comunes.

Si sigue sin poder resolver el problema, siga creando la solicitud de soporte técnico seleccionando **Siguiente**.

### <a name="additional-details"></a>Detalles adicionales

A continuación, recopilaremos información adicional sobre el problema. Proporcionar información exhaustiva y detallada en este paso nos ayuda a enrutar la solicitud de soporte técnico al ingeniero adecuado.

1. Complete los **detalles del problema** para que podamos tener más información sobre el mismo. Si es posible, indíquenos cuándo se inició el problema y los pasos para reproducirlo. Puede cargar un archivo, como un archivo de registro o una salida de diagnósticos. Para obtener más información sobre las cargas de archivos, consulte [Instrucciones de carga de archivos](how-to-manage-azure-support-request.md#file-upload-guidelines).

1. En la sección **Compartir información de diagnóstico**, seleccione **Sí** o **No**. Si elige **Sí**, el soporte técnico de Azure podrá recopilar [información de diagnóstico](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) de los recursos de Azure. Si prefiere no compartir esta información, seleccione **No**. Consulte la sección [Registros de información de diagnóstico avanzada](#advanced-diagnostic-information-logs) para obtener más detalles sobre los tipos de archivos que se pueden recopilar.

  En algunos casos, habrá opciones adicionales donde elegir. Por ejemplo, para determinados tipos de problemas de máquina virtual, puede elegir si desea [permitir el acceso a la memoria de una máquina virtual](#memory-dump-collection).

1. En la sección **Método de soporte técnico**, seleccione la gravedad del efecto. El nivel de gravedad máximo depende de su [plan de soporte técnico](https://azure.microsoft.com/support/plans).

1. Proporcione el método de contacto preferido, su disponibilidad y su idioma de soporte técnico preferido.

1. A continuación, complete la sección **Información de contacto** para que sepamos cómo ponernos en contacto con usted.

Seleccione **Siguiente** cuando haya completado toda la información necesaria.

### <a name="review--create"></a>Revisar y crear

Antes de crear la solicitud, revise todos los detalles que va a enviar al soporte técnico. Puede seleccionar **Anterior** para volver a cualquier pestaña si necesita realizar cambios. Cuando esté satisfecho con la solicitud de soporte técnico, seleccione **Crear**.

Un ingeniero de soporte técnico se pondrá en contacto con usted mediante el método que haya indicado. Consulte [Ámbito de soporte técnico y capacidad de respuesta](https://azure.microsoft.com/support/plans/response/) para obtener información sobre los tiempos de respuesta inicial.

### <a name="advanced-diagnostic-information-logs"></a>Registros de información de diagnóstico avanzada

Cuando se permite la recopilación de [información de diagnóstico avanzada](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/), el soporte técnico de Microsoft puede recopilar información que pueda ayudar a resolver el problema más rápidamente. Esta lista no exhaustiva incluye ejemplos de los archivos más comunes recopilados en información de diagnóstico avanzada para diferentes servicios o entornos.

- [Registros de VM de PaaS de Microsoft Azure](/troubleshoot/azure/virtual-machines/sdp352ef8720-e3ee-4a12-a37e-cc3b0870f359-windows-vm)
- [Registros de VM de IaaS de Microsoft Azure](https://github.com/azure/azure-diskinspect-service/blob/master/docs/manifest_by_file.md)
- [Registros de Microsoft Azure Service Fabric](/troubleshoot/azure/general/fabric-logs)
- [Registros de dispositivos y paquetes de soporte de StorSimple](https://support.microsoft.com/topic/storsimple-support-packages-and-device-logs-cb0a1c7e-6125-a5a7-f212-51439781f646)
- [Registros de SQL Server en Azure Virtual Machines](/troubleshoot/azure/general/sql-vm-logs)
- [Registros de Azure Active Directory](/troubleshoot/azure/active-directory/support-data-collection-diagnostic-logs)
- [Registros de dispositivos y paquetes de soporte de Azure Stack Edge](/troubleshoot/azure/general/azure-stack-edge-support-package-device-logs)
- [Registros de Azure Synapse Analytics](/troubleshoot/azure/general/synapse-analytics-apache-spark-pools-diagnostic-logs)

### <a name="memory-dump-collection"></a>Recopilación de volcado de memoria

Al crear un caso de soporte técnico para determinados tipos de problemas de máquina virtual (VM), se le preguntará si va a permitir que el soporte técnico acceda a la memoria de la máquina virtual. Si lo hace, podemos recopilar un volcado de memoria para ayudar a diagnosticar el problema.

Un volcado de memoria completo es el archivo de volcado de memoria en modo kernel más grande. Este archivo incluye toda la memoria física que usa Windows. De forma predeterminada, un volcado de memoria completo no incluye la memoria física que usa el firmware de la plataforma.

El volcado se copia desde el nodo de proceso (host de Azure) a otro servidor para la depuración en el mismo centro de datos. Los datos del cliente están protegidos, ya que los datos no salen del límite seguro de Azure.

El archivo de volcado se crea mediante la generación de un estado de guardado de Hyper-V de la máquina virtual. Esto pausará la máquina virtual durante un máximo de 10 minutos, después de lo cual se reanudará. La máquina virtual no se reinicia como parte de este proceso.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las opciones de soporte técnico de autoayuda de Azure, vea este vídeo:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

Siga estos vínculos para obtener más información:

* [Administración de una solicitud de soporte técnico de Azure](how-to-manage-azure-support-request.md)
* [API REST de incidencia de soporte técnico de Azure](/rest/api/support)
* Ponerse en contacto con nosotros en [Twitter](https://twitter.com/azuresupport)
* Obtener ayuda de sus compañeros en la [página de preguntas y respuestas de Microsoft](/answers/products/azure)
* Obtener más información en [Preguntas más frecuentes de soporte técnico de Azure](https://azure.microsoft.com/support/faq)