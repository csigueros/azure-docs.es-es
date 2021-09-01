---
title: Integración de Azure Security Center con Azure VMware Solution
description: Aprenda a proteger las máquinas virtuales de Azure VMware Solution con las herramientas de seguridad nativas de Azure desde el panel de Azure Security Center.
ms.topic: how-to
ms.date: 06/14/2021
ms.openlocfilehash: 9c7326fca3aeebf277b5f54a65729e2594933984
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228667"
---
# <a name="integrate-azure-security-center-with-azure-vmware-solution"></a>Integración de Azure Security Center con Azure VMware Solution 

Azure Security Center proporciona protección contra amenazas avanzada en máquinas virtuales (VM) locales y de Azure VMware Solution. Evalúa la vulnerabilidad de las máquinas virtuales de Azure VMware Solution y genera alertas según sea necesario. Estas alertas de seguridad se pueden reenviar a Azure Monitor para su resolución. Puede definir las directivas de seguridad en Azure Security Center. Para más información, consulte [Administrar directivas de seguridad](../security-center/tutorial-security-policy.md). 

Azure Security Center ofrece muchas características, entre las que se incluyen:
- Supervisión de la integridad de los archivos
- Detección de ataques sin archivos
- Evaluación de revisiones del sistema operativo 
- Evaluación de configuraciones de seguridad incorrectas
- Evaluación de EndPoint Protection

En el diagrama se muestra la arquitectura de supervisión integrada de la seguridad integrada de las máquinas virtuales de Azure VMware Solution.
 
:::image type="content" source="media/azure-security-integration/azure-integrated-security-architecture.png" alt-text="Diagrama que muestra la arquitectura de la seguridad integrada de Azure" border="false":::

El **agente de Log Analytics** recopila datos de registro de Azure, Azure VMware Solution y las máquinas virtuales locales. Los datos de registro se envían a los registros de Azure Monitor y se almacenan en un **área de trabajo de Log Analytics**. Cada área de trabajo tiene su propio repositorio de datos y configuración para almacenar datos.  Una vez recopilados los registros, **Azure Security Center** evalúa el estado de vulnerabilidades de las máquinas virtuales de Azure VMware Solution y genera una alerta por cualquier vulnerabilidad crítica. Una vez realizada la evaluación, Azure Security Center reenvía el estado de vulnerabilidad a Azure Sentinel para crear un incidente y asignarlo a otras amenazas.  Azure Security Center se conecta a Azure Sentinel mediante el conector de Azure Security Center. 

## <a name="prerequisites"></a>Prerrequisitos

- [Planee el uso optimizado de Security Center](../security-center/security-center-planning-and-operations-guide.md).

- [Revise las plataformas admitidas en Security Center](../security-center/security-center-os-coverage.md).

- [Cree un área de trabajo de Log Analytics](../azure-monitor/logs/quick-create-workspace.md) para recopilar datos de varios orígenes.

- [Habilite Azure Security Center en la suscripción](../security-center/security-center-get-started.md). 

   >[!NOTE]
   >Azure Security Center es una herramienta preconfigurada que no requiere implementación, pero deberá habilitarla.

- [Habilite Azure Defender](../security-center/enable-azure-defender.md). 


## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Adición de máquinas virtuales de Azure VMware Solution en Security Center

1. En Azure Portal, busque **Azure Arc** y selecciónelo.

2. En Recursos, seleccione **Servidores** y, después, **+Agregar**.

   :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Captura de pantalla que muestra la página de servidores de Azure Arc para agregar una máquina virtual de Azure VMware Solution a Azure.":::

3. Seleccione **Generar scripts**.
 
   :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Captura de pantalla de la página de Azure Arc en la que se muestra la opción para agregar un servidor con un script interactivo."::: 
 
4. En la pestaña **Requisitos previos**, seleccione **Siguiente**.

5. En la pestaña **Detalles del recurso**, rellene los detalles siguientes y, a continuación, seleccione **Siguiente: Etiquetas**. 

    - Subscription

    - Grupo de recursos

    - Region 

    - Sistema operativo

    - Detalles del servidor proxy
    
6. En la pestaña **Etiquetas**, seleccione **Siguiente**.

7. En la pestaña **Descargar y ejecutar el script**, seleccione **Descargar**.

8. Especifique el sistema operativo y ejecute el script en la máquina virtual de Azure VMware Solution.

## <a name="view-recommendations-and-passed-assessments"></a>Visualización de recomendaciones y evaluaciones superadas

Las recomendaciones y evaluaciones proporcionan los detalles sobre el estado de seguridad del recurso. 

1. En Azure Security Center, seleccione **Inventario** en el panel izquierdo.

2. En Tipo de recurso, seleccione **Servidores: Azure Arc**.
 
   :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Captura de pantalla de la página de inventario de Azure Security Center que muestra Servidores: Azure Arc seleccionado en Tipo de recurso.":::

3. Seleccione el nombre del recurso. Se abre una página que muestra los detalles del estado de seguridad del recurso.

4. En **Lista de recomendaciones**, seleccione las pestañas **Recomendaciones**, **Evaluaciones correctas**, y **Evaluaciones no disponibles** para ver estos detalles.

   :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Captura de pantalla que muestra las recomendaciones de seguridad y las evaluaciones de Azure Security Center":::.

## <a name="deploy-an-azure-sentinel-workspace"></a>Implementación de un área de trabajo de Azure Sentinel

Azure Sentinel proporciona análisis de seguridad, detección de alertas y respuesta automática a amenazas en un entorno. Se trata de una solución de administración de eventos e información de seguridad (SIEM) nativa de la nube que se basa en un área de trabajo de Log Analytics.

Dado que Azure Sentinel se basa en un área de trabajo de Log Analytics, solo tendrá que seleccionar el área de trabajo que desea usar.

1. En Azure Portal, busque **Azure Sentinel** y selecciónelo.

2. En la página Áreas de trabajo de Azure Sentinel, seleccione **+Agregar**.

3. Seleccione el área de trabajo de Log Analytics y, después, **Agregar**.

## <a name="enable-data-collector-for-security-events"></a>Habilitación del recopilador de datos para eventos de seguridad

1. En la página Áreas de trabajo de Azure Sentinel, seleccione el área de trabajo configurada.

2. En Configuración, seleccione **Conectores de datos**.

3. En la columna Nombre del conector, seleccione **Eventos de seguridad** en la lista y, después, seleccione **Abrir página del conector**.

4. En la página del conector, seleccione los eventos que desea transmitir y, después, seleccione **Aplicar cambios**.

   :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Captura de pantalla de la página de eventos de seguridad de Azure Sentinel, donde puede seleccionar los eventos que desea transmitir":::.




## <a name="connect-azure-sentinel-with-azure-security-center"></a>Conexión de Azure Sentinel con Azure Security Center  

1. En la página del área de trabajo de Azure Sentinel, seleccione el área de trabajo configurada.

2. En Configuración, seleccione **Conectores de datos**.

3. Seleccione **Azure Security Center** en la lista y, después, seleccione **Abrir página del conector**.

   :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Captura de pantalla de la página de conectores de datos de Azure Sentinel que muestra la selección para conectar Azure Security Center con Azure Sentinel":::.

4. Seleccione **Conectar** para conectar Azure Security Center con Azure Sentinel.

5. Habilite **Crear incidente** para generar un incidente para Azure Security Center.

## <a name="create-rules-to-identify-security-threats"></a>Creación de reglas para identificar amenazas de seguridad

Después de conectar los orígenes de datos a Azure Sentinel, puede crear reglas para generar alertas para las amenazas detectadas. En el ejemplo siguiente, crearemos una regla para los intentos de inicio de sesión en Windows Server con una contraseña incorrecta.

1. En la página de información general de Azure Sentinel, en Configuraciones, seleccione **Analytics**.

2. En Configuraciones, seleccione **Analytics**.

3. Seleccione **+Crear** y, en el menú desplegable, seleccione **Regla de consulta programada**.

4. En la pestaña **General**, escriba la información necesaria y seleccione **Siguiente: Establecer lógica de regla**.

    - Nombre

    - Descripción

    - Tácticas

    - Gravedad

    - Status

5. En la pestaña **Establecer la lógica de la regla**, escriba la información necesaria y seleccione **Siguiente**.

    - Consulta de la regla (aquí se muestra nuestra consulta de ejemplo)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Asignar entidades

    - Programación de consultas

    - Umbral de alerta

    - Agrupación de eventos

    - Supresión


6. En la pestaña **Configuración de los incidentes**, habilite **Crear incidentes a partir de las alertas desencadenadas por esta regla de análisis** y seleccione **Siguiente: Respuesta automatizada**.
 
    :::image type="content" source="../sentinel/media/tutorial-detect-threats-custom/general-tab.png" alt-text="Captura de pantalla que muestra el Asistente para reglas de análisis para crear una regla en Azure Sentinel.":::

7. Seleccione **Siguiente: Review** (Siguiente: revisar).

8. En la pestaña **Revisar y crear**, revise la información y seleccione **Crear**.

>[!TIP]
>Después del tercer intento fallido de iniciar sesión en Windows Server, la regla creada desencadena un incidente para cada intento fallido.

## <a name="view-alerts"></a>Visualización de alertas

Puede ver los incidentes generados con Azure Sentinel. También puede asignar incidentes y cerrarlos una vez resueltos, todo ello desde Azure Sentinel.

1. Vaya a la página de información general de Azure Sentinel.

2. En Administración de amenazas, seleccione **Incidentes**.

3. Seleccione un incidente y, a continuación, asígnelo a un equipo para su resolución.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Captura de pantalla de la página de incidentes de Azure Sentinel con el incidente seleccionado y la opción para asignar el incidente para su resolución":::.

>[!TIP]
>Después de resolver el problema, puede cerrarlo.

## <a name="hunt-security-threats-with-queries"></a>Búsqueda de amenazas de seguridad con consultas

Puede crear consultas o usar la consulta predefinida disponible en Azure Sentinel para identificar amenazas en su entorno. En los pasos siguientes, se ejecuta una consulta predefinida.

1. En la página de información general de Azure Sentinel, en Administración de amenazas, seleccione **Búsqueda**. Se muestra una lista de consultas predefinidas.

   >[!TIP]
   >También puede crear una nueva consulta seleccionando **Nueva consulta**. 
   >
   >:::image type="content" source="../sentinel/media/hunting/save-query.png" alt-text="Captura de pantalla de la página de búsqueda de Azure Sentinel con la opción + Nueva consulta resaltada":::.

3. Seleccione una consulta y, después, seleccione **Ejecutar consulta**.

4. Seleccione **Ver resultados** para comprobar los resultados.



## <a name="next-steps"></a>Pasos siguientes

Ahora que se ha explicado cómo proteger las máquinas virtuales de Azure VMware Solution, puede que quiera obtener información sobre:

- [Uso del panel de Azure Defender](../security-center/azure-defender-dashboard.md)
- [Detección avanzada de ataques de varias fases en Azure Sentinel](../azure-monitor/logs/quick-create-workspace.md)
- [Integración de servicios nativos de Azure en Azure VMware Solution](integrate-azure-native-services.md)
