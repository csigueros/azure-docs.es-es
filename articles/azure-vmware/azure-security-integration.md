---
title: Integración de Microsoft Defender for Cloud con Azure VMware Solution
description: Vea cómo puede proteger las máquinas virtuales de Azure VMware Solution con las herramientas de seguridad nativas de Azure desde el panel de protección de cargas de trabajo.
ms.topic: how-to
ms.date: 06/14/2021
ms.openlocfilehash: f1ec6d6282a773cca3164f377f7efd8bdaa42c77
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312695"
---
# <a name="integrate-microsoft-defender-for-cloud-with-azure-vmware-solution"></a>Integración de Microsoft Defender for Cloud con Azure VMware Solution 

Microsoft Defender for Cloud proporciona protección contra amenazas avanzada en las máquinas virtuales (VM) de Azure VMware Solution y del entorno local. Evalúa la vulnerabilidad de las máquinas virtuales de Azure VMware Solution y genera alertas según sea necesario. Estas alertas de seguridad se pueden reenviar a Azure Monitor para su resolución. En Microsoft Defender for Cloud, puede definir directivas de seguridad. Para más información, consulte [Administrar directivas de seguridad](../security-center/tutorial-security-policy.md). 

Microsoft Defender for Cloud ofrece muchas características, entre las que se incluyen las siguientes:
- Supervisión de la integridad de los archivos
- Detección de ataques sin archivos
- Evaluación de revisiones del sistema operativo 
- Evaluación de configuraciones de seguridad incorrectas
- Evaluación de EndPoint Protection

En el diagrama se muestra la arquitectura de supervisión integrada de la seguridad integrada de las máquinas virtuales de Azure VMware Solution.
 
:::image type="content" source="media/azure-security-integration/azure-integrated-security-architecture.png" alt-text="Diagrama que muestra la arquitectura de la seguridad integrada de Azure" border="false":::

El **agente de Log Analytics** recopila datos de registro de Azure, Azure VMware Solution y las máquinas virtuales locales. Los datos de registro se envían a los registros de Azure Monitor y se almacenan en un **área de trabajo de Log Analytics**. Cada área de trabajo tiene su propio repositorio de datos y configuración para almacenar datos.  Una vez recopilados los registros, **Microsoft Defender for Cloud** evalúa el estado de vulnerabilidad de las máquinas virtuales de Azure VMware Solution y genera una alerta si detecta alguna vulnerabilidad crítica. Tras la evaluación, Microsoft Defender for Cloud reenvía el estado de vulnerabilidad a Microsoft Sentinel para crear un incidente y asignarlo junto con otras amenazas.  Microsoft Defender for Cloud se conecta a Microsoft Sentinel mediante el conector de Microsoft Defender for Cloud. 

## <a name="prerequisites"></a>Requisitos previos

- [Planee un uso optimizado de Defender for Cloud](../security-center/security-center-planning-and-operations-guide.md).

- [Revise las plataformas compatibles con Defender for Cloud](../security-center/security-center-os-coverage.md).

- [Cree un área de trabajo de Log Analytics](../azure-monitor/logs/quick-create-workspace.md) para recopilar datos de varios orígenes.

- [Habilite Microsoft Defender for Cloud en su suscripción](../security-center/security-center-get-started.md). 

   >[!NOTE]
   >Microsoft Defender for Cloud es una herramienta preconfigurada que no requiere implementación, pero sí debe habilitarse.

- [Habilite Microsoft Defender for Cloud](../security-center/enable-azure-defender.md). 


## <a name="add-azure-vmware-solution-vms-to-defender-for-cloud"></a>Adición de máquinas virtuales de Azure VMware Solution a Defender for Cloud

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

1. En Microsoft Defender for Cloud, seleccione **Inventario** en el panel izquierdo.

2. En Tipo de recurso, seleccione **Servidores: Azure Arc**.
 
   :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Captura de pantalla de la página Inventario de Microsoft Defender for Cloud, que muestra Servidores: Azure Arc seleccionado en Tipo de recurso.":::

3. Seleccione el nombre del recurso. Se abre una página que muestra los detalles del estado de seguridad del recurso.

4. En **Lista de recomendaciones**, seleccione las pestañas **Recomendaciones**, **Evaluaciones correctas**, y **Evaluaciones no disponibles** para ver estos detalles.

   :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Captura de pantalla que muestra las evaluaciones y recomendaciones de seguridad de Microsoft Defender for Cloud.":::

## <a name="deploy-a-microsoft-sentinel-workspace"></a>Implementación de un área de trabajo de Microsoft Sentinel

Microsoft Sentinel proporciona análisis de seguridad, detección de alertas y respuesta automática a amenazas en todo un entorno. Se trata de una solución de administración de eventos e información de seguridad (SIEM) nativa de la nube que se basa en un área de trabajo de Log Analytics.

Dado que Microsoft Sentinel se basa en un área de trabajo de Log Analytics, solo tiene que seleccionar el área de trabajo que desea usar.

1. En Azure Portal, busque **Microsoft Sentinel** y selecciónelo.

2. En la página Áreas de trabajo de Microsoft Sentinel, seleccione **+Agregar**.

3. Seleccione el área de trabajo de Log Analytics y, después, **Agregar**.

## <a name="enable-data-collector-for-security-events"></a>Habilitación del recopilador de datos para eventos de seguridad

1. En la página Áreas de trabajo de Microsoft Sentinel, seleccione el área de trabajo configurada.

2. En Configuración, seleccione **Conectores de datos**.

3. En la columna Nombre del conector, seleccione **Eventos de seguridad** en la lista y, después, seleccione **Abrir página del conector**.

4. En la página del conector, seleccione los eventos que desea transmitir y, después, seleccione **Aplicar cambios**.

   :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Captura de pantalla de la página Eventos de seguridad de Microsoft Sentinel, donde puede seleccionar los eventos que desea transmitir":::.




## <a name="connect-microsoft-sentinel-with-microsoft-defender-for-cloud"></a>Conexión de Microsoft Sentinel con Microsoft Defender for Cloud  

1. En la página Áreas de trabajo de Microsoft Sentinel, seleccione el área de trabajo configurada.

2. En Configuración, seleccione **Conectores de datos**.

3. Elija **Microsoft Defender for Cloud** en la lista y seleccione **Open connector page** (Abrir página del conector).

   :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Captura de pantalla de la página Conectores de datos de Microsoft Sentinel en la que se muestra la opción seleccionada para conectar Microsoft Defender for Cloud con Microsoft Sentinel.":::

4. Seleccione **Conectar** para conectar Microsoft Defender for Cloud con Microsoft Sentinel.

5. Habilite **Crear incidente** para que se genere un incidente para Microsoft Defender for Cloud.

## <a name="create-rules-to-identify-security-threats"></a>Creación de reglas para identificar amenazas de seguridad

Después de conectar los orígenes de datos a Microsoft Sentinel, puede crear reglas que generen alertas para las amenazas detectadas. En el ejemplo siguiente, crearemos una regla para los intentos de inicio de sesión en Windows Server con una contraseña incorrecta.

1. En la página de información general de Microsoft Sentinel, en Configuraciones, seleccione **Analytics**.

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
 
    :::image type="content" source="../sentinel/media/tutorial-detect-threats-custom/general-tab.png" alt-text="Captura de pantalla que muestra el Asistente para reglas de análisis para crear una regla en Microsoft Sentinel.":::

7. Seleccione **Siguiente: Review** (Siguiente: revisar).

8. En la pestaña **Revisar y crear**, revise la información y seleccione **Crear**.

>[!TIP]
>Después del tercer intento fallido de iniciar sesión en Windows Server, la regla creada desencadena un incidente para cada intento fallido.

## <a name="view-alerts"></a>Visualización de alertas

Puede ver los incidentes generados con Microsoft Sentinel. También puede asignar incidentes y cerrarlos una vez resueltos, todo ello desde Microsoft Sentinel.

1. Vaya a la página de información general de Microsoft Sentinel.

2. En Administración de amenazas, seleccione **Incidentes**.

3. Seleccione un incidente y, a continuación, asígnelo a un equipo para su resolución.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Captura de pantalla de la página Incidentes de Microsoft Sentinel con el incidente seleccionado y la opción para asignar el incidente para su resolución":::.

>[!TIP]
>Después de resolver el problema, puede cerrarlo.

## <a name="hunt-security-threats-with-queries"></a>Búsqueda de amenazas de seguridad con consultas

Puede crear consultas o usar la consulta predefinida disponible en Microsoft Sentinel para identificar amenazas en su entorno. En los pasos siguientes, se ejecuta una consulta predefinida.

1. En la página de información general de Microsoft Sentinel, en Administración de amenazas, seleccione **Búsqueda**. Se muestra una lista de consultas predefinidas.

   >[!TIP]
   >También puede crear una nueva consulta seleccionando **Nueva consulta**. 
   >
   >:::image type="content" source="../sentinel/media/hunting/save-query.png" alt-text="Captura de pantalla de la página Búsqueda de Microsoft Sentinel con la opción Nueva consulta resaltada.":::

3. Seleccione una consulta y, después, seleccione **Ejecutar consulta**.

4. Seleccione **Ver resultados** para comprobar los resultados.



## <a name="next-steps"></a>Pasos siguientes

Ahora que se ha explicado cómo proteger las máquinas virtuales de Azure VMware Solution, puede que quiera obtener información sobre:

- [Uso del panel de protección de cargas de trabajo](../security-center/azure-defender-dashboard.md)
- [Creación de un área de trabajo de Log Analytics en Azure Portal](../azure-monitor/logs/quick-create-workspace.md)
- [Integración de servicios nativos de Azure en Azure VMware Solution](integrate-azure-native-services.md)
