---
title: Protección de Azure Active Directory B2C con Azure Sentinel
titleSuffix: Azure AD B2C
description: Tutorial para realizar análisis de seguridad de datos de Azure Active Directory B2C con Azure Sentinel
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: be45eaa692b0b8235541c798cf82ca26b14b9f3f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691570"
---
# <a name="tutorial-configure-security-analytics-for-azure-active-directory-b2c-data-with-azure-sentinel"></a>Tutorial: configuración del análisis de seguridad de datos de Azure Active Directory B2C con Azure Sentinel

Puede proteger aún más su entorno de Azure Active Directory (AD) B2C redirigiendo registros e información de auditoría a Azure Sentinel. Azure Sentinel es una solución nativa de nube de administración de eventos e información de seguridad (SIEM) y respuesta automatizada de orquestación de seguridad (SOAR). Azure Sentinel proporciona detección de alertas, visibilidad de amenazas, búsqueda proactiva y respuesta a amenazas para Azure AD B2C.

Al usar Azure Sentinel con Azure AD B2C, puede:

- Detectar amenazas que antes no se detectaban y minimizar los falsos positivos mediante el análisis y la inteligencia sobre amenazas sin precedentes de Microsoft.
- Investigar amenazas con inteligencia artificial. Buscar actividades sospechosas a gran escala y aprovechar años de trabajo relacionado con la ciberseguridad en Microsoft.
- Responda a los incidentes con rapidez con la orquestación y la automatización de tareas comunes integradas.
- Satisfacer los requisitos de seguridad y cumplimiento de su organización.

En este tutorial, obtendrá información sobre cómo:

1. [Transferir los registros de Azure AD B2C al área de trabajo de registros de Azure Monitor](#configure-azure-ad-b2c-with-azure-monitor-logs-analytics)
2. [Habilitar Azure Sentinel en un área de trabajo de Log Analytics](#deploy-an-azure-sentinel-instance)
3. [Crear una regla de ejemplo en Azure Sentinel que desencadene un incidente](#create-an-azure-sentinel-rule)
4. [Configurar la respuesta automatizada](#automated-response)

## <a name="configure-azure-ad-b2c-with-azure-monitor-logs-analytics"></a>Configuración de Azure AD B2C con Azure Monitor Log Analytics

Habilite la **configuración de diagnóstico** en Azure AD en el inquilino de Azure AD B2C para definir dónde se deben enviar los registros y las métricas de un recurso.

A continuación, [configure Azure AD B2C para enviar registros a Azure Monitor](https://docs.microsoft.com/azure/active-directory-b2c/azure-monitor).

## <a name="deploy-an-azure-sentinel-instance"></a>Implementación de una instancia de Azure Sentinel

>[!IMPORTANT]
>Para habilitar Azure Sentinel, necesita **permisos de colaborador** en la suscripción en la que reside el área de trabajo de Azure Sentinel. Para usar Azure Sentinel, necesita permisos de colaborador o lector en el grupo de recursos al que pertenece el área de trabajo.

Una vez que haya configurado la instancia de Azure Active Directory B2C para enviar registros a Azure Monitor, debe habilitar otra de Azure Sentinel.

1. Vaya a [Azure Portal](https://portal.azure.com). Seleccione la suscripción dónde se ha creado el área de trabajo de Log Analytics.

2. Busque y seleccione **Azure Sentinel**.

3. Seleccione **Agregar**.

![imagen que muestra la búsqueda de Azure Sentinel en Azure Portal](./media/azure-sentinel/azure-sentinel-add.png)

4. Seleccione la nueva área de trabajo.

![imagen de la selección del área de trabajo de Sentinel](./media/azure-sentinel/create-new-workspace.png)

5. Seleccione **Agregar Azure Sentinel**.

>[!NOTE]
>Puede [ejecutar Azure Sentinel](../sentinel/quickstart-onboard.md) en más de un área de trabajo, pero los datos se aíslan en un área de trabajo única.

## <a name="create-an-azure-sentinel-rule"></a>Creación de una regla de Azure Sentinel

>[!NOTE]
>Azure Sentinel proporciona plantillas integradas listas para usar, con el fin de ayudarle a crear reglas de detección de amenazas diseñadas por el equipo de analistas y expertos en seguridad de Microsoft. Las reglas creadas a partir de estas plantillas buscan automáticamente actividad sospechosa en sus datos. No hay conectores de Azure AD B2C nativos disponibles en este momento. En el ejemplo de este tutorial, crearemos nuestra propia regla.

Ahora que ha habilitado Azure Sentinel, recibirá notificaciones cuando se produzca algo sospechoso en su inquilino de Azure AD B2C.

Puede crear [reglas de análisis personalizadas](../sentinel/tutorial-detect-threats-custom.md) para detectar amenazas y comportamientos anómalos presentes en el entorno. Estas reglas buscan eventos específicos o conjuntos de eventos y le avisan cuando se alcanzan determinados umbrales de eventos o se cumplen determinadas condiciones. A continuación, generan incidentes para su posterior investigación.

En el ejemplo siguiente, se explica el escenario en el que se recibe una notificación si alguien intenta forzar el acceso a su entorno pero no se realiza correctamente. Podría significar un ataque por fuerza bruta. Puede recibir una notificación para dos o más inicios de sesión no correctos en un plazo de 60 segundos.

1. En el menú de navegación de Azure Sentinel, seleccione **Análisis**.

2. En la barra de acciones de la parte superior, seleccione **+ Crear** y elija **Regla de consulta programada**. Se abrirá el **Asistente para reglas de Analytics**.

![imagen que muestra la selección para crear una regla de consulta programada](./media/azure-sentinel/create-scheduled-rule.png)

3. En el Asistente para reglas de Analytics, vaya a la pestaña **General**.

   | Campo | Value |
   |:--|:--|
   |Nombre | Inicios de sesión incorrectos en B2C |
   |Descripción | Notificar dos o más inicios de sesión incorrectos en un plazo de 60 segundos |
   | Tácticas | Elija entre las categorías de ataques por las que clasificar la regla. Estas categorías se basan en las tácticas del marco [MITRE ATT&CK](https://attack.mitre.org/).<BR>En nuestro ejemplo, elegiremos `PreAttack`. <BR> MITRE ATT&CK® es una knowledge base accesible en todo el mundo que contiene tácticas y técnicas de adversarios basadas en observaciones del mundo real. La knowledge base ATT&CK® se usa como base para el desarrollo de metodologías y modelos de amenazas específicos.
   | severity | Según el caso |
   | Estado | Cuando crea la regla, el valor predeterminado del campo Estado es `Enabled`, lo que significa que se ejecutará inmediatamente después de que termine de crearla. Si no desea ejecutarla de inmediato, seleccione `Disabled` para agregar la regla a la pestaña Reglas activas, desde donde podrá habilitarla cuando sea necesario.|

![imagen de la especificación de propiedades básicas de la regla](./media/azure-sentinel/create-new-rule.png)

4.  Para definir la lógica de consulta de regla y configurar las opciones, en la pestaña **Establecer lógica de regla**, escriba una consulta directamente en el campo **Consulta de regla**. Esta consulta le avisará cuando haya dos o más inicios de sesión incorrectos en su inquilino de Azure AD B2C en un plazo de 60 segundos y se organizará mediante la propiedad `UserPrincipalName`.

![imagen que muestra la indicación de la consulta de regla en la pestaña de la lógica](./media/azure-sentinel/rule-query.png)

En la sección Query scheduling (Programación de consultas), establezca los siguientes parámetros:

![imagen que muestra el establecimiento de los parámetros de programación de la consulta](./media/azure-sentinel/query-scheduling.png)

5. Seleccione **Siguiente: configuración de incidentes (versión preliminar)** . Configurará y agregará la respuesta automatizada más adelante.

6. Haga clic en la pestaña **Revisar y crear** para revisar todos los valores de configuración de la nueva regla de alerta. Cuando aparezca el mensaje **Validación superada**, seleccione **Crear** para inicializar la regla de alerta.

![imagen de la revisión y creación de la regla](./media/azure-sentinel/review-create.png)

7. Vea la regla y los incidentes que genera. Puede encontrar la regla personalizada recién creada de tipo **Programado** en la tabla en la pestaña **Reglas activas** de la pantalla **Análisis** principal. Desde esta lista puede **editar**, **habilitar**, **deshabilitar** o **eliminar** reglas.

![imagen de la pantalla "Análisis" en la que se muestran las opciones para editar, habilitar, deshabilitar o eliminar reglas](./media/azure-sentinel/rule-crud.png)

8. Vea los resultados de la nueva regla de inicios de sesión incorrectos de Azure AD B2C. Vaya a la página **Incidentes**, donde puede realizar la evaluación de prioridades, investigar y corregir las amenazas. Un incidente puede incluir varias alertas, a modo de agregado de todas las pruebas relevantes en una investigación en concreto. Puede establecer propiedades como la gravedad y el estado en el nivel de incidente.

>[!Note]
>Una característica clave de Azure Sentinel es la [investigación de incidentes](../sentinel/tutorial-investigate-cases.md).

9. Para iniciar la investigación, seleccione un incidente específico. A la derecha, puede ver información detallada del incidente, como su gravedad, las entidades implicadas, los eventos sin procesar que lo han desencadenado y su identificador único.

![imagen de la pantalla alt-text="incident](./media/azure-sentinel/select-incident.png)

10. Seleccione **Ver detalles completos** en la página del incidente y revise las pestañas correspondientes donde se resume la información del incidente y se proporcionan más detalles.

![imagen de la regla 73](./media/azure-sentinel/full-details.png)

11. Seleccione **Evidencia** > **Eventos** > **Vincular a Log Analytics**. En los resultados se mostrará la propiedad `UserPrincipalName` de la identidad que ha tratado de iniciar sesión con el número de intentos.

![imagen de los detalles del incidente seleccionado](./media/azure-sentinel/logs.png)

## <a name="automated-response"></a>Respuesta automatizada

Azure Sentinel proporciona una [sólida funcionalidad SOAR](../sentinel/automation-in-azure-sentinel.md). Es posible adjuntar acciones automatizadas, denominadas cuadernos de estrategias en Azure Sentinel, a reglas de análisis para satisfacer sus requisitos.

En este ejemplo, se agrega una notificación por correo electrónico tras un incidente creado por la regla. Use un [cuaderno de estrategias existente del repositorio de GitHub de Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification) para realizar esta tarea. Una vez configurado el cuaderno de estrategias, solo tendrá que editar la regla existente y seleccionar el cuaderno de estrategias en la pestaña Automatización.

![imagen de la pantalla de configuración de la respuesta automatizada asociada a una regla](./media/azure-sentinel/automation-tab.png)

## <a name="next-steps"></a>Pasos siguientes

- [Control de falsos positivos en Azure Sentinel](../sentinel/false-positives.md)

- [Libros de ejemplo](https://github.com/azure-ad-b2c/siem#workbooks)

- [Documentación de Azure Sentinel](../sentinel/index.yml)
