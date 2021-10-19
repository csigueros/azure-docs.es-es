---
title: Cómo crear una solicitud de soporte técnico de Azure para un problema de Contrato Enterprise
description: Los clientes de Contrato Enterprise que necesiten asistencia técnica pueden usar Azure Portal para buscar soluciones de autoservicio y para crear y administrar solicitudes de soporte técnico.
ms.topic: troubleshooting
ms.date: 10/07/2021
ms.author: banders
author: bandersmsft
ms.reviewer: sapnakeshari
ms.service: cost-management-billing
ms.subservice: billing
ms.openlocfilehash: 7826b2c19a77ef3762f39ade9a80f26615f704bd
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660198"
---
# <a name="create-an-azure-support-request-for-an-enterprise-agreement-issue"></a>Creación de una solicitud de soporte técnico de Azure para un problema de Contrato Enterprise

Azure le permite crear y administrar solicitudes de soporte técnico, también conocidas como incidencias de soporte técnico para Contratos Enterprise. Puede crear y administrar las solicitudes en [Azure Portal](https://portal.azure.com), tal y como se describe en este artículo. También puede crear y administrar solicitudes mediante programación con la [API REST de incidencia de soporte técnico de Azure](/rest/api/support) o con la [CLI de Azure](/cli/azure/azure-cli-support-request).

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

Para crear una solicitud de soporte técnico de un Contrato Enterprise, debe ser administrador de Enterprise asociado o administrador de asociados relacionado con una inscripción empresarial. 

### <a name="go-to-help--support-from-the-global-header"></a>Ir a Ayuda y soporte técnico en el encabezado global

Para iniciar una solicitud de soporte técnico desde cualquier lugar de Azure Portal:

1. Seleccione el símbolo con forma de signo de interrogación en el encabezado global y, a continuación, seleccione **Ayuda y soporte técnico**.

   :::image type="content" source="media/how-to-create-azure-support-request-ea/help-support-new-lower.png" alt-text="Captura de pantalla del menú Ayuda de Azure Portal":::.

1. Seleccione **Crear solicitud de soporte técnico**. Siga las indicaciones para proporcionar información sobre el problema. Sugeriremos algunas soluciones posibles, recopilaremos los detalles del problema y le ayudaremos a enviar y realizar el seguimiento de la solicitud de soporte técnico.

   :::image type="content" source="media/how-to-create-azure-support-request-ea/new-support-request-2-lower.png" alt-text="Captura de pantalla de la página Ayuda y soporte técnico con el vínculo Crear solicitud de soporte técnico.":::

### <a name="go-to-help--support-from-a-resource-menu"></a>Ir a Ayuda y soporte técnico en un menú de recursos

Para iniciar una solicitud de soporte técnico:

1. En el menú de recursos, en la sección **Soporte técnico y solución de problemas**, seleccione **Nueva solicitud de soporte técnico**.

   :::image type="content" source="media/how-to-create-azure-support-request-ea/in-context-2-lower.png" alt-text="Captura de pantalla de la opción Nueva solicitud de soporte técnico en el panel de recursos.":::

1. Siga las indicaciones para proporcionarnos la información sobre el problema que tiene. Al iniciar el proceso de solicitud de soporte técnico desde un recurso, algunas opciones se preseleccionan automáticamente.

## <a name="create-a-support-request"></a>Crear una solicitud de soporte

Le guiaremos a través de algunos pasos para recopilar información sobre el problema y ayudarle a resolverlo. Este paso se describe en las secciones siguientes.

### <a name="problem-description"></a>Descripción del problema

1. Escriba un resumen del problema y, a continuación, seleccione **Tipo de problema**. 
1. En la lista **Tipo de problema**, seleccione **Administración de inscripciones** para los problemas relacionados con el portal de EA.  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-issue-type-enrollment-administration.png" alt-text="Captura de pantalla que muestra Seleccionar administración de inscripciones" lightbox="./media/how-to-create-azure-support-request-ea/select-issue-type-enrollment-administration.png" :::.
1. En **Número de inscripción**, escriba el número de inscripción. 
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-enrollment.png" alt-text="Captura de pantalla que muestra Seleccionar el número de inscripción" :::.
1. En **Tipo de problema**, seleccione la categoría de problema que mejor describa el tipo de problema que tiene.  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-problem-type.png" alt-text="Captura de pantalla que muestra Seleccionar un tipo de problema" :::.
1. En **Subtipo de problema**, seleccione una subcategoría de problema. 

Una vez que haya proporcionado todos estos detalles, seleccione **Siguiente: soluciones**.

### <a name="recommended-solution"></a>Solución recomendada

En función de la información proporcionada, le mostraremos las soluciones recomendadas que puede usar para intentar resolver el problema. En algunos casos, es posible que incluso ejecutemos un diagnóstico rápido. Las soluciones están escritas por ingenieros de Azure y resolverán la mayoría de los problemas comunes.

Si sigue sin poder resolver el problema, siga creando la solicitud de soporte técnico seleccionando **Siguiente: detalles**.

### <a name="additional-details"></a>Detalles adicionales

A continuación, recopilaremos información adicional sobre el problema. Proporcionar información exhaustiva y detallada en este paso nos ayuda a enrutar la solicitud de soporte técnico al ingeniero adecuado.

1. En la pestaña Detalles, complete los **detalles del problema** para que podamos tener más información sobre el mismo. Si es posible, indíquenos cuándo se inició el problema y los pasos para reproducirlo. Puede cargar un archivo, como un archivo de registro o una salida de diagnósticos. Para obtener más información sobre las cargas de archivos, consulte [Instrucciones de carga de archivos](../../azure-portal/supportability/how-to-manage-azure-support-request.md#file-upload-guidelines).

1. En la sección **Compartir información de diagnóstico**, seleccione **Sí** o **No**. Si elige **Sí**, el soporte técnico de Azure podrá recopilar [información de diagnóstico](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) de los recursos de Azure. Si prefiere no compartir esta información, seleccione **No**. En algunos casos, habrá opciones adicionales donde elegir.

1. En la sección **Método de soporte técnico**, seleccione la gravedad del problema. El nivel de gravedad máximo depende de su [plan de soporte técnico](https://azure.microsoft.com/support/plans).

1. Proporcione el método de contacto preferido, su disponibilidad y su idioma de soporte técnico preferido.

1. A continuación, complete la sección **Información de contacto** para que sepamos cómo ponernos en contacto con usted.  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/details-tab.png" alt-text="Captura de pantalla que muestra la pestaña Detalles." lightbox="./media/how-to-create-azure-support-request-ea/details-tab.png" :::

Seleccione **Siguiente: revisión y creación** cuando haya completado toda la información necesaria.

### <a name="review--create"></a>Revisar y crear

Antes de crear la solicitud, revise todos los detalles que va a enviar al soporte técnico. Puede seleccionar **Anterior** para volver a cualquier pestaña si necesita realizar cambios. Cuando esté satisfecho con la solicitud de soporte técnico, seleccione **Crear**.

Un ingeniero de soporte técnico se pondrá en contacto con usted mediante el método que haya indicado. Consulte [Ámbito de soporte técnico y capacidad de respuesta](https://azure.microsoft.com/support/plans/response/) para obtener información sobre los tiempos de respuesta inicial.

## <a name="cant-create-request-with-microsoft-account"></a>No se puede crear una solicitud con la cuenta Microsoft

Si tiene una cuenta Microsoft (MSA), no puede crear una incidencia de soporte técnico de Azure. Las cuentas de Microsoft se crean para los servicios Outlook, Windows Live y Hotmail entre otros.

Para crear una incidencia de soporte técnico de Azure, la *cuenta profesional* debe tener el rol de administrador de EA o el rol de administrador de asociados.

Si tiene una MSA, haga que un administrador cree una cuenta profesional. A continuación, un administrador de empresa o de asociados debe agregar su cuenta profesional como administrador de empresa o de asociados. A continuación, puede usar su cuenta profesional para presentar una solicitud de soporte técnico.

- Para agregar un administrador de empresa, consulte [Creación de otro administrador de empresa](ea-portal-administration.md#create-another-enterprise-administrator).
- Para agregar un administrador de asociado, consulte [Creación de otro administrador de asociado](ea-partner-portal-administration.md#manage-partner-administrators).

## <a name="next-steps"></a>Pasos siguientes

Siga estos vínculos para obtener más información:

* [Cómo administrar una solicitud de soporte de Azure]()../../azure-portal/supportability/how-to-manage-azure-support-request.md).
* [API REST de incidencia de soporte técnico de Azure](/rest/api/support)
* Ponerse en contacto con nosotros en [Twitter](https://twitter.com/azuresupport)
* Obtener ayuda de sus compañeros en la [página de preguntas y respuestas de Microsoft](/answers/products/azure)
* Obtener más información en [Preguntas más frecuentes de soporte técnico de Azure](https://azure.microsoft.com/support/faq)