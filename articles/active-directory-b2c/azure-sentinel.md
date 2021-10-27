---
title: Protección de Azure Active Directory B2C con Azure Sentinel
titleSuffix: Azure AD B2C
description: En este tutorial, usará Azure Sentinel para realizar análisis de seguridad para datos de Azure Active Directory B2C.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 02e994d743269b5cf8d4b66a713f129a66e4ef36
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129995919"
---
# <a name="tutorial-configure-security-analytics-for-azure-active-directory-b2c-data-with-azure-sentinel"></a>Tutorial: configuración del análisis de seguridad de datos de Azure Active Directory B2C con Azure Sentinel

Puede proteger aún más su entorno de Azure Active Directory B2C (Azure AD B2C) redirigiendo registros e información de auditoría a Azure Sentinel. Azure Sentinel es una solución nativa de nube de servicio de administración de eventos e información de seguridad (SIEM) y de respuesta automatizada de orquestación de seguridad (SOAR). Azure Sentinel proporciona detección de alertas, visibilidad de amenazas, búsqueda proactiva y respuesta a amenazas para Azure AD B2C.

Al usar Azure Sentinel con Azure AD B2C, puede:

- Detectar amenazas que antes no se detectaban y minimizar los falsos positivos mediante el análisis y la inteligencia sobre amenazas de Microsoft.
- Investigar las amenazas con inteligencia artificial. Buscar actividades sospechosas a gran escala y aprovechar años de trabajo relacionado con la ciberseguridad en Microsoft.
- Responda a los incidentes con rapidez con la orquestación y la automatización de tareas comunes integradas.
- Satisfacer los requisitos de seguridad y cumplimiento de su organización.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Transferir registros de Azure AD B2C a un área de trabajo de Log Analytics.
> * Habilitar Azure Sentinel en un área de trabajo de Log Analytics.
> * Crear una regla de ejemplo en Azure Sentinel que desencadene un incidente.
> * Configurar la respuesta automatizada.

## <a name="configure-azure-ad-b2c-with-azure-monitor-log-analytics"></a>Configuración de Azure AD B2C con Log Analytics en Azure Monitor

Habilite la **configuración de diagnóstico** en Azure AD en el inquilino de Azure AD B2C para definir dónde se deben enviar los registros y las métricas de un recurso. A continuación, [configure Azure AD B2C para enviar registros a Azure Monitor](./azure-monitor.md).

## <a name="deploy-an-azure-sentinel-instance"></a>Implementación de una instancia de Azure Sentinel

Una vez que haya configurado la instancia de Azure AD B2C para enviar registros a Azure Monitor, debe habilitar otra de Azure Sentinel.

>[!IMPORTANT]
>Para habilitar Azure Sentinel, necesita permisos de colaborador en la suscripción en la que reside el área de trabajo de Azure Sentinel. Para usar Azure Sentinel, necesita permisos de colaborador o lector en el grupo de recursos al que pertenece el área de trabajo.

1. Vaya a [Azure Portal](https://portal.azure.com). Seleccione la suscripción dónde se ha creado el área de trabajo de Log Analytics.

2. Busque y seleccione **Azure Sentinel**.

   ![Captura de pantalla que muestra la búsqueda de Azure Sentinel en Azure Portal.](./media/azure-sentinel/azure-sentinel-add.png)

3. Seleccione **Agregar**.

4. Seleccione la nueva área de trabajo.

   ![Captura de pantalla que muestra dónde seleccionar un área de trabajo de Azure Sentinel.](./media/azure-sentinel/create-new-workspace.png)

5. Seleccione **Agregar Azure Sentinel**.

>[!NOTE]
>Puede [ejecutar Azure Sentinel](../sentinel/quickstart-onboard.md) en más de un área de trabajo, pero los datos se aíslan en un área de trabajo única.

## <a name="create-an-azure-sentinel-rule"></a>Creación de una regla de Azure Sentinel

Ahora que ha habilitado Azure Sentinel, recibirá notificaciones cuando se produzca algo sospechoso en su inquilino de Azure AD B2C.

Puede crear [reglas de análisis personalizadas](../sentinel/detect-threats-custom.md) para detectar amenazas y comportamientos anómalos en el entorno. Estas reglas buscan eventos específicos o conjuntos de eventos y le avisan cuando se alcanzan determinados umbrales de eventos o se cumplen determinadas condiciones. A continuación, generan incidentes para su posterior investigación.

>[!NOTE]
>Azure Sentinel proporciona plantillas integradas listas con el fin de ayudarle a crear reglas de detección de amenazas diseñadas por el equipo de analistas y expertos en seguridad de Microsoft. Las reglas creadas a partir de estas plantillas buscan automáticamente actividad sospechosa en sus datos. No hay conectores de Azure AD B2C nativos disponibles en este momento. En el ejemplo de este tutorial, crearemos nuestra propia regla.

En el ejemplo siguiente, recibirá una notificación si alguien intenta forzar el acceso a su entorno pero no lo consigue. Podría tratarse de un ataque por fuerza bruta. Puede recibir una notificación para dos o más inicios de sesión incorrectos en un plazo de 60 segundos.

1. En el menú de la izquierda de Azure Sentinel, seleccione **Análisis**.

2. En la barra de acciones de la parte superior, seleccione **+ Crear** > **Regla de consulta programada**. 

    ![Captura de pantalla que muestra las selecciones para crear una regla de consulta programada.](./media/azure-sentinel/create-scheduled-rule.png)

3. En el Asistente para reglas de análisis, vaya a la pestaña **General** y escriba la siguiente información:

    | Campo | Value |
    |:--|:--|
    |**Nombre** | Escriba un nombre adecuado para los inicios de sesión incorrectos de Azure AD B2C. |
    |**Descripción** | Escriba una descripción que indique que la regla notificará dos o más inicios de sesión incorrectos en un período de 60 segundos. |
    | **Tácticas** | Elija entre las categorías de ataques por las que clasificar la regla. Estas categorías se basan en las tácticas del marco [MITRE ATT&CK](https://attack.mitre.org/).<BR>Para nuestro ejemplo, elegiremos **PreAttack**. <BR> MITRE ATT&CK es una knowledge base accesible en todo el mundo que contiene tácticas y técnicas de adversarios basadas en observaciones del mundo real. Esta knowledge base se usa como base para el desarrollo de metodologías y modelos de amenazas específicos.
    | **Gravedad** | Seleccione un nivel de gravedad adecuado. |
    | **Estado** | Al crear la regla, su estado es **Habilitada** de forma predeterminada. Ese estado significa que la regla se ejecutará inmediatamente después de terminar de crearla. Si no desea que se ejecute inmediatamente, seleccione **Deshabilitada**. A continuación, la regla se agregará a la pestaña **Reglas activas** y podrá habilitarla desde allí cuando sea necesario.|

    ![Captura de pantalla que muestra las propiedades básicas de una regla.](./media/azure-sentinel/create-new-rule.png)

4. Para definir la lógica de consulta de regla y configurar las opciones, en la pestaña **Establecer lógica de regla**, escriba una consulta directamente en el cuadro **Consulta de regla**. 

    ![Captura de pantalla que muestra cómo especificar la consulta de regla en la pestaña para establecer la lógica de regla.](./media/azure-sentinel/rule-query.png)

    Esta consulta le avisará cuando haya dos o más inicios de sesión incorrectos en su inquilino de Azure AD B2C en un plazo de 60 segundos. Organizará los inicios de sesión por `UserPrincipalName`.

5. En la sección **Query scheduling** (Programación de consultas), establezca los siguientes parámetros:

    ![Captura de pantalla que muestra cómo establecer parámetros de programación de consultas.](./media/azure-sentinel/query-scheduling.png)

6. Seleccione **Siguiente: configuración de incidentes (versión preliminar)** . Configurará y agregará la respuesta automatizada más adelante.

7. Haga clic en la pestaña **Revisar y crear** para revisar todos los valores de configuración de la nueva regla de alerta. Cuando aparezca el mensaje **Validación superada**, seleccione **Crear** para inicializar la regla de alerta.

    ![Captura de pantalla que muestra la pestaña para revisar y crear una regla.](./media/azure-sentinel/review-create.png)

8. Vea la regla y los incidentes que genera. Puede encontrar la regla personalizada recién creada de tipo **Programado** en la tabla en la pestaña **Reglas activas** de la pantalla **Análisis** principal. Desde esta lista puede editar, habilitar, deshabilitar o eliminar reglas con los botones correspondientes.

    ![Captura de pantalla que muestra las reglas activas con opciones para editar, habilitar, deshabilitar o eliminar.](./media/azure-sentinel/rule-crud.png)

9. Vea los resultados de la nueva regla para inicios de sesión incorrectos de Azure AD B2C. Vaya a la página **Incidentes**, donde puede realizar la evaluación de prioridades, investigar y corregir las amenazas. 

    Un incidente puede incluir varias alertas, a modo de agregado de todas las pruebas relevantes en una investigación en concreto. Puede establecer propiedades como la gravedad y el estado en el nivel de incidente.

    > [!NOTE]
    > Una característica clave de Azure Sentinel es la [investigación de incidentes](../sentinel/investigate-cases.md).
    
10. Para iniciar una investigación, seleccione un incidente específico. 

    A la derecha, puede ver información detallada del incidente. Entre esa información se incluye su gravedad, las entidades implicadas, los eventos sin procesar que lo han desencadenado y su identificador único.

    ![Captura de pantalla que muestra la información del incidente.](./media/azure-sentinel/select-incident.png)

11. Seleccione **Ver detalles completos** en el panel del incidente. Revise las pestañas que resumen la información del incidente y proporcionan más detalles.

    ![Captura de pantalla que muestra las pestañas de información del incidente.](./media/azure-sentinel/full-details.png)

12. Seleccione **Evidencia** > **Eventos** > **Vincular a Log Analytics**. En los resultados se muestra el valor `UserPrincipalName` de la identidad que ha tratado de iniciar sesión con el número de intentos.

    ![Captura de pantalla que muestra todos los detalles de un incidente seleccionado.](./media/azure-sentinel/logs.png)

## <a name="automated-response"></a>Respuesta automatizada

Azure Sentinel proporciona una [sólida funcionalidad SOAR](../sentinel/automation-in-azure-sentinel.md). Es posible adjuntar acciones automatizadas, denominadas *cuadernos de estrategias* en Azure Sentinel, a reglas de análisis para satisfacer sus requisitos.

En este ejemplo, se agrega una notificación por correo electrónico para un incidente que crea la regla. Para realizar esta tarea, use un [cuaderno de estrategias existente del repositorio de GitHub de Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification). Una vez configurado el cuaderno de estrategias, edite la regla existente y seleccione el cuaderno de estrategias en la pestaña **Respuesta automática**.

![Captura de pantalla que muestra la pantalla de configuración de la imagen para la respuesta automatizada asociada a una regla.](./media/azure-sentinel/automation-tab.png)

## <a name="related-information"></a>Información relacionada

Para más información sobre Azure Sentinel y Azure AD B2C, consulte:

- [Libros de ejemplo](https://github.com/azure-ad-b2c/siem#workbooks)

- [Documentación de Azure Sentinel](../sentinel/index.yml)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Control de falsos positivos en Azure Sentinel](../sentinel/false-positives.md)
