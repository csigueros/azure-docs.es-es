---
title: Protección de Azure Active Directory B2C con Azure Sentinel
titleSuffix: Azure AD B2C
description: En este tutorial se muestra cómo realizar análisis de seguridad de Azure Active Directory B2C con Azure Sentinel.
services: active-directory-b2c
author: agabrielle
manager: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: agher
ms.subservice: B2C
ms.date: 07/19/2021
ms.openlocfilehash: a405cb97a021c05a3b0c6aa004d3f92ce4657d24
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178073"
---
# <a name="tutorial-how-to-perform-security-analytics-for-azure-ad-b2c-data-with-azure-sentinel"></a>Tutorial: Cómo realizar análisis de seguridad de datos de Azure Active Directory B2C con Azure Sentinel

Puede proteger aún más su entorno de Azure Active Directory B2C redirigiendo registros e información de auditoría a Azure Sentinel. Azure Sentinel es una solución nativa de nube de **administración de eventos e información de seguridad (SIEM) y respuesta automatizada de orquestación de seguridad (SOAR)** . Azure Sentinel proporciona detección de alertas, visibilidad de amenazas, búsqueda proactiva y respuesta a amenazas para **Azure Active Directory B2C**.

Usando Azure Sentinel junto con Azure Active Directory B2C, puede hacer lo siguiente:

- Detecte amenazas que antes no se detectaban y minimice los falsos positivos mediante el análisis y la inteligencia sobre amenazas sin precedentes de Microsoft.
- Investigue amenazas con inteligencia artificial y busque actividades sospechosas a escala, aprovechando el trabajo de ciberseguridad que ha llevado a cabo Microsoft durante décadas.
- Responda a los incidentes con rapidez con la orquestación y la automatización de tareas comunes integradas.
- Satisfacer los requisitos de seguridad y cumplimiento de su organización.

En este tutorial, aprenderá a hacer lo siguiente:

1. Transferir los registros de B2C al área de trabajo de registros de Azure Monitor.
1. Habilitar **Azure Sentinel** en un área de trabajo de Log Analytics.
1. Crear una regla de ejemplo en Sentinel que desencadene un incidente.
1. Configurar una respuesta automatizada.

## <a name="configure-aad-b2c-with-azure-monitor-logs-analytics"></a>Configuración de Azure Active Directory B2C con Azure Monitor Log Analytics

Los siguientes pasos le guiarán en el proceso para habilitar la **_configuración de diagnóstico_** en Azure Active Directory, en su inquilino de Azure Active Directory B2C.
La configuración de diagnóstico define dónde se deben enviar los registros y las métricas de un recurso.

Siga los pasos **1 a 5** de [Supervisión de Azure Active Directory B2C con Azure Monitor](./azure-monitor.md) para configurar Azure Active Directory B2C con el fin de enviar registros a Azure Monitor.

## <a name="deploy-an-azure-sentinel-instance"></a>Implementación de una instancia de Azure Sentinel

> [!IMPORTANT]
> Para habilitar Azure Sentinel, necesita **permisos de colaborador** en la suscripción en la que reside el área de trabajo de Azure Sentinel. Para usar Azure Sentinel, necesita permisos de colaborador o lector en el grupo de recursos al que pertenece el área de trabajo.

Una vez que haya configurado la instancia de Azure Active Directory B2C para enviar registros a Azure Monitor, debe habilitar otra de Azure Sentinel.

1. Inicie sesión en Azure Portal. Asegúrese de que esté seleccionada la suscripción en la que se ha creado el área de trabajo de Log Analytics en el paso anterior.

2. Busque y seleccione **Azure Sentinel**.

3. Seleccione **Agregar**.

   :::image type="content" source="./media/azure-sentinel/azure-sentinel-add.png" alt-text="Búsqueda de Azure Sentinel en Azure Portal":::

4. Seleccione el área de trabajo usada en el paso anterior.

   :::image type="content" source="./media/azure-sentinel/create-new-workspace.png" alt-text="Selección del área de trabajo de Sentinel":::

5. Seleccione **Agregar Azure Sentinel**.

   > [!NOTE]
   > Puede ejecutar Azure Sentinel en más de un área de trabajo, pero los datos se aíslan en un área de trabajo. Para obtener más información sobre cómo habilitar Sentinel, consulte esta [guía de inicio rápido](../sentinel/quickstart-onboard.md).

## <a name="create-a-sentinel-rule"></a>Creación de una regla de Sentinel

> [!NOTE]
> Azure Sentinel proporciona plantillas integradas listas para usar, con el fin de ayudarle a crear reglas de detección de amenazas diseñadas por el equipo de analistas y expertos en seguridad de Microsoft. Las reglas creadas a partir de estas plantillas buscan automáticamente actividad sospechosa en sus datos. Dado que actualmente no hay ningún conector nativo de Azure Active Directory B2C, no usaremos reglas nativas en nuestro ejemplo. A efectos de este tutorial, crearemos nuestra propia regla.

Ahora que ha habilitado Sentinel, le interesa recibir notificaciones cuando se produzca algo sospechoso en su inquilino de B2C.

Puede crear reglas de análisis personalizadas que le ayuden a detectar amenazas y comportamientos anómalos presentes en el entorno. Estas reglas buscan eventos específicos o conjuntos de eventos y le avisan cuando se alcanzan determinados umbrales de eventos o se cumplen determinadas condiciones. A continuación, generan incidentes para su posterior investigación.

> [!NOTE]
> Para obtener una revisión detallada sobre las reglas de análisis, consulte este [tutorial](/azure/active-directory-b2c/articles/sentinel/detect-threats-custom.md).

En nuestro escenario, queremos recibir una notificación si alguien intenta forzar el acceso a nuestro entorno, pero no lo consigue —lo cual podría implicar un ataque por fuerza bruta—. Queremos recibir una notificación en caso de **_dos o más inicios de sesión incorrectos en un plazo de 60 segundos_**.

1. En el menú de navegación de Azure Sentinel, seleccione **Análisis**.
2. En la barra de acciones de la parte superior, seleccione **+ Crear** y elija **Regla de consulta programada**. Así se abre el **Asistente para reglas de Analytics**.

   :::image type="content" source="./media/azure-sentinel/create-scheduled-rule.png" alt-text="Selección para crear una regla de consulta programada":::

3. Asistente para reglas de análisis: pestaña General

   - Proporcione un **nombre** y una **descripción** que sean únicos.
     - **Nombre**: _Inicios de sesión incorrectos en B2C_  **Descripción**: _Notificar en caso de dos o más inicios de sesión incorrectos en un plazo de 60 segundos_
   - En el campo **Tactics** (Táctica), puede elegir cualquiera de las categorías de ataques por las que se clasifica la regla. Se basan en las tácticas del marco [MITRE ATT&CK](https://attack.mitre.org/).

     - Para nuestro ejemplo, elegiremos _PreAttack_.

       > [!Tip]
       > MITRE ATT&CK® es una knowledge base accesible en todo el mundo que contiene tácticas y técnicas de adversarios basadas en observaciones del mundo real. La knowledge base ATT&CK® se usa como base para el desarrollo de metodologías y modelos de amenazas específicos.

   - Establezca la **Gravedad** de la alerta según sea necesario.
     - Dado que esta es nuestra primera regla, elegiremos _Alta_. Podemos realizar cambios en la regla más adelante.
   - Cuando cree la regla, el valor predeterminado del campo **Status** (Estado) es **Enabled** (Habilitado), lo que significa que se ejecutará inmediatamente después de que termine de crearla. Si no desea ejecutarla de inmediato, seleccione **Disabled** (Deshabilitado) para agregar la regla a la pestaña **Active rules** (Reglas activas), desde donde podrá habilitarla cuando sea necesario.

     :::image type="content" source="./media/azure-sentinel/create-new-rule.png" alt-text="Especificación de propiedades básicas de la regla":::

4. Defina la lógica de consulta de regla y configure los valores.

   En la pestaña **Establecer la lógica de la regla**, escribiremos una consulta directamente en el campo **Consulta de regla**. Esta consulta le avisará cuando haya dos o más inicios de sesión incorrectos en su inquilino de B2C en un plazo de 60 segundos y se organizará mediante la propiedad _UserPrincipalName_.

   ```kusto
   SigninLogs
   | where ResultType != "0"
   | summarize Count = count() by bin(TimeGenerated, 60s), UserPrincipalName
   | project Count = toint(Count), UserPrincipalName
   | where Count >= 1
   ```

   :::image type="content" source="./media/azure-sentinel/rule-query.png" alt-text="Indicación de la consulta de regla en la pestaña de la lógica":::

   En la sección Query scheduling (Programación de consultas), establezca los siguientes parámetros:

   :::image type="content" source="./media/azure-sentinel/query-scheduling.png" alt-text="Establecimiento de los parámetros de programación de la consulta":::

5. Haga clic en "Siguiente" en **Configuración de incidentes (versión preliminar)** y en **Respuesta automatizada**. Configurará y agregará la respuesta automatizada más adelante.

6. Haga clic en "Siguiente" para acceder a la pestaña **Revisar y crear** y revisar todos los valores de configuración de la nueva regla de alerta. Cuando aparezca el mensaje "Validación superada", seleccione **Crear** para inicializar la regla de alerta.

   :::image type="content" source="./media/azure-sentinel/review-create.png" alt-text="Revisión y creación de la regla":::

7. Vea la regla y los incidentes que genera.

   Puede encontrar la regla personalizada recién creada (de tipo "Programado") en la tabla en la pestaña **Reglas activas** de la pantalla **Análisis** principal. Desde esta lista puede **_editar_**, **_habilitar_**, **_deshabilitar_** o **_eliminar_** reglas.

   :::image type="content" source="./media/azure-sentinel/rule-crud.png" alt-text="Pantalla &quot;Análisis&quot; en la que se muestran las opciones para editar, habilitar, deshabilitar o eliminar reglas":::

   Para ver los resultados de nuestra nueva regla de inicios de sesión incorrectos en B2C, vaya a la página **Incidentes**, donde puede evaluar, investigar y solucionar las amenazas.

   Un incidente puede incluir varias alertas, a modo de agregado de todas las pruebas relevantes en una investigación en concreto. Puede establecer propiedades como la gravedad y el estado en el nivel de incidente.

   > [!NOTE]
   > Para obtener una revisión detallada sobre la investigación de incidentes, consulte [este tutorial](/azure/active-directory-b2c/articles/sentinel/investigate-cases.md).

   Para iniciar la investigación, seleccione un incidente específico. A la derecha, puede ver información detallada del incidente, como su gravedad, las entidades implicadas, los eventos sin procesar que lo han desencadenado y su identificador único.

   :::image type="content" source="./media/azure-sentinel/select-incident.png" alt-text="Pantalla de incidente":::

   Para ver más detalles sobre las alertas y las entidades del incidente, seleccione **Ver detalles completos** en la página del incidente y revise las pestañas correspondientes donde se resume la información del incidente.

   :::image type="content" source="./media/azure-sentinel/full-details.png" alt-text="Regla 73":::

   Para revisar más detalles sobre el incidente, puede seleccionar **Pruebas -> Eventos** o **Eventos -> Vínculo a Log Analytics**.

   En los resultados se mostrará la propiedad _UserPrincipalName_ de la identidad que ha tratado de iniciar sesión el _número_ de intentos.

   :::image type="content" source="./media/azure-sentinel/logs.png" alt-text="Detalles del incidente seleccionado":::

## <a name="automated-response"></a>Respuesta automatizada

Azure Sentinel también proporciona una sólida funcionalidad SOAR; encontrará información adicional en la documentación oficial de Sentinel [aquí](../sentinel/automation-in-azure-sentinel.md).

Es posible adjuntar acciones automatizadas, denominadas cuadernos de estrategias en Sentinel, a reglas de análisis para satisfacer sus requisitos.

En nuestro ejemplo, vamos a agregar una notificación por correo electrónico tras un incidente creado por nuestra regla.

Para llevar a cabo nuestra tarea, usaremos un cuaderno de estrategias existente del repositorio GitHub de Sentinel [Incident-Email-Notification](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification).

Una vez configurado el cuaderno de estrategias, solo tendrá que editar la regla existente y seleccionar el cuaderno en la pestaña "Automatización":

:::image type="content" source="./media/azure-sentinel/automation-tab.png" alt-text="Pantalla de configuración de la respuesta automatizada asociada a una regla":::

## <a name="next-steps"></a>Pasos siguientes

- Dado que ninguna regla es perfecta, si es necesario, puede actualizar la consulta de regla para excluir falsos positivos. Para obtener más información, consulte [Control de falsos positivos en Azure Sentinel](../sentinel/false-positives.md).

- Para ayudar con el análisis de datos y la creación de informes visuales enriquecidos, tiene a su disposición para descargar una galería de libros creados por expertos que revelan información basada en los datos. [Estos libros](https://github.com/azure-ad-b2c/siem#workbooks) se pueden personalizar fácilmente en función de sus necesidades.

- Obtenga más información en la [documentación de Azure Sentinel](../sentinel/index.yml).