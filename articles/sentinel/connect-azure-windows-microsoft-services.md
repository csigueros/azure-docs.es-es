---
title: Conexión de Azure Sentinel a servicios de Azure, Windows y Microsoft
description: Aprenda a conectar Azure Sentinel a servicios en la nube de Azure y Microsoft 365, y a registros de eventos de Windows Server.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 08/18/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: fab632ae17f71829ddfb36ced149253e124c9851
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456905"
---
# <a name="connect-azure-sentinel-to-azure-windows-microsoft-and-amazon-services"></a>Conexión de Azure Sentinel a servicios de Azure, Windows, Microsoft y Amazon

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel usa la base de Azure para proporcionar compatibilidad integrada entre servicios para la ingesta de datos de muchos servicios de Azure y Microsoft 365, Amazon Web Services y varios servicios de Windows Server. Hay algunos métodos diferentes para realizar estas conexiones y en este artículo se describe cómo hacerlo.

En este artículo se describen los siguientes tipos de conectores:

- Conexiones **basadas en API**
- Conexiones de **configuración de diagnóstico**, algunas administradas mediante Azure Policy
- Conexiones basadas en el **agente de Log Analytics**

En este artículo se presenta información común a los grupos de conectores. Vea la página adjunta de [referencia de conectores de datos](data-connectors-reference.md) para obtener información exclusiva de cada conector, como los requisitos previos de licencia y las tablas de Log Analytics para el almacenamiento de datos.

Las integraciones siguientes son más únicas y populares, y se tratan de manera individual, con sus propios artículos:

- [Microsoft 365 Defender](connect-microsoft-365-defender.md)
- [Azure Defender](connect-azure-security-center.md)
- [Azure Active Directory](connect-azure-active-directory.md)
- [Eventos de seguridad de Windows](connect-windows-security-events.md)
- [Amazon Web Services (AWS) CloudTrail](connect-aws.md)


## <a name="api-based-connections"></a>Conexiones basadas en API

### <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Log Analytics.
- Debe tener el rol Administrador global o Administrador de seguridad en el inquilino del área de trabajo de Azure Sentinel.

### <a name="instructions"></a>Instrucciones

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. Seleccione el servicio en la galería de conectores de datos y, luego, seleccione **Open Connector Page** (Abrir página del conector) en el panel de vista previa.

1. Seleccione **Connect** (Conectar) para iniciar el flujo de eventos o alertas desde el servicio a Azure Sentinel.

1. Si en la página del conector hay una sección **Create incidents - recommended!** (Crear incidentes: recomendado), seleccione **Enable** (Habilitar) si quiere crear incidentes de forma automática a partir de las alertas.

Puede buscar y consultar los datos de cada servicio mediante los nombres de tabla que aparecen en la sección del conector del servicio en la página [Referencia de conectores de datos](data-connectors-reference.md).

## <a name="diagnostic-settings-based-connections"></a>Conexiones basadas en configuración de diagnóstico

La configuración de algunos conectores de este tipo se administra mediante Azure Policy. Seleccione la pestaña **Azure Policy** siguiente para obtener instrucciones. Para los demás conectores de este tipo, seleccione la pestaña **Independiente**.

# <a name="standalone"></a>[Independiente](#tab/SA)

### <a name="prerequisites"></a>Requisitos previos

Para ingerir datos en Azure Sentinel:

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

### <a name="instructions"></a>Instrucciones

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. Seleccione el tipo de recurso en la galería de conectores de datos y, luego, seleccione **Open Connector Page** (Abrir página del conector) en el panel de vista previa.

1. En la sección **Configuration** (Configuración) de la página del conector, seleccione el vínculo para abrir la página de configuración del recurso.

    Si aparece una lista de recursos del tipo deseado, seleccione el vínculo de un recurso cuyos registros quiera ingerir.

1. Desde el menú de navegación del recurso, seleccione **Configuración de diagnóstico**.

1. Seleccione **+ Agregar configuración de diagnóstico**.

1. En la pantalla **Configuración de diagnóstico**, escriba un nombre en el campo **Nombre de la configuración de diagnóstico**.

    Marque la casilla **Enviar a Log Analytics**. Se mostrarán dos nuevos campos debajo. Elija la **suscripción** y el **área de trabajo de Log Analytics** pertinentes (donde reside Azure Sentinel).

1. Active las casillas de los tipos de registros y métricas que quiera recopilar. Vea las opciones recomendadas para cada tipo de recurso en la sección del conector del recurso en la página [Referencia de conectores de datos](data-connectors-reference.md).

1. En la parte superior de la pantalla, seleccione **Guardar**.

# <a name="azure-policy"></a>[Azure Policy](#tab/AP)

### <a name="prerequisites"></a>Requisitos previos

Para ingerir datos en Azure Sentinel:

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

- Para usar Azure Policy a fin de aplicar una directiva de streaming de registro a los recursos, debe tener asignado el rol de propietario para el ámbito de asignación de directivas.

### <a name="instructions"></a>Instrucciones

Los conectores de este tipo usan Azure Policy para aplicar una configuración de diagnóstico única a una colección de recursos de un solo tipo, definidos como un ámbito. Puede ver los tipos de registro ingeridos desde un tipo de recurso concreto en el lado izquierdo de la página del conector para ese recurso, en **Tipos de datos**.

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. Seleccione el tipo de recurso en la galería de conectores de datos y, luego, seleccione **Open Connector Page** (Abrir página del conector) en el panel de vista previa.

1. En la sección **Configuration** (Configuración) de la página del conector, expanda los expansores que vea y seleccione el botón **Launch Azure Policy Assignment wizard** (Iniciar el asistente para asignación de Azure Policy).

    Se abre el asistente para la asignación de directivas, listo para crear una directiva, con un nombre de directiva rellenado previamente.

    1. En la pestaña **Datos básicos**, seleccione el botón con los tres puntos debajo de **Ámbito** para elegir la suscripción (y, opcionalmente, un grupo de recursos). También puede agregar una descripción.

    1. En la pestaña **Parámetros**:
       - Desactive la casilla **Mostrar solo los parámetros que requieran entrada**.
       - Si ve los campos **Efecto** y **Nombre de configuración**, déjelos como están.
       - Elija el área de trabajo de Azure Sentinel en la lista desplegable **Área de trabajo de Log Analytics**.
       - Los campos desplegables restantes representan los tipos de registro de diagnóstico disponibles. Deje marcado como "True" todos los tipos de registro que quiera ingerir.

    1. La directiva se aplicará a los recursos agregados en el futuro. Para aplicar la directiva también a los recursos existentes, seleccione la pestaña **Corrección** y marque la casilla **Crear una tarea de corrección**.

    1. En la pestaña **Revisar y crear**, haga clic en **Crear**. La directiva se asigna ahora al ámbito elegido.

---

> [!NOTE]
>
> Con este tipo de conector de datos, los indicadores de estado de conectividad (una franja de color en la galería de conectores de datos y los iconos de conexión situados junto a los nombres de tipo de datos) mostrarán el estado *Conectado* (verde) solo si los datos se han ingerido en algún momento en los últimos 14 días. Transcurridos catorce días sin ingesta de datos, el conector se mostrará como desconectado. En el momento en que circulen más datos, se devolverá el estado *conectado*.

Puede buscar y consultar los datos de cada tipo de recurso mediante el nombre de tabla que aparece en la sección del conector del recurso en la página [Referencia de conectores de datos](data-connectors-reference.md).

## <a name="windows-agent-based-connections"></a>Conexiones basadas en agentes de Windows

# <a name="azure-monitor-agent"></a>[Agente de Azure Monitor](#tab/AMA)

> [!IMPORTANT]
>
> - Algunos conectores basados en el agente de Azure Monitor (AMA) se encuentran actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

El [agente de Azure Monitor](../azure-monitor/agents/azure-monitor-agent-overview.md) usa **reglas de recopilación de datos (DCR)** para definir los datos que se recopilan de cada agente. Las reglas de recopilación de datos ofrecen dos ventajas distintas:

- **Permiten administrar la configuración de la recopilación a gran escala** y, al mismo tiempo, habilitan configuraciones únicas con ámbito para subconjuntos de máquinas. Son independientes del área de trabajo e independientes de la máquina virtual, lo que significa que se pueden definir una vez y reutilizarlas en distintas máquinas y entornos. Consulte [Configuración de la recopilación de datos para el agente de Azure Monitor](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md).

- **Cree filtros personalizados** para elegir los eventos exactos que quiere ingerir. El agente de Azure Monitor usa estas reglas para filtrar los datos *en el origen* e ingerir solo los eventos que quiera, a la vez que se olvida del resto. Esto puede ahorrar mucho dinero en costos de ingesta de datos.

Consulte a continuación cómo crear reglas de recopilación de datos.

### <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

- Para recopilar eventos de cualquier sistema que no sea una máquina virtual de Azure, el sistema debe tener [**Azure Arc**](../azure-monitor/agents/azure-monitor-agent-install.md) instalado y habilitado *antes* de habilitar el conector basado en agente de Azure Monitor.

   Esto incluye:
   
    - Servidores Windows instalados en máquinas físicas
    - Servidores Windows instalados en máquinas virtuales locales
    - Servidores Windows instalados en máquinas virtuales en nubes que no son de Azure

### <a name="instructions"></a>Instrucciones

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**. Seleccione su conector de la lista y, a continuación, seleccione **Abrir la página del conector** en el panel de detalles. A continuación, siga las instrucciones en pantalla debajo de la pestaña **Instrucciones**, tal como se describe en el resto de esta sección.

1. Compruebe que tiene los permisos adecuados, tal como se describe en la sección **Requisitos previos** de la página del conector.

1. En **Configuración,** seleccione **+Agregar regla de recopilación de datos**. El **Asistente para crear reglas de recopilación de datos** se abrirá a la derecha.

1. En **Aspectos básicos,** escriba una **regla de nombre** y especifique una **suscripción** y un **grupo de recursos** donde se creará la regla de recopilación de datos (DCR). *No* tiene que ser el mismo grupo de recursos o suscripción en el que se encuentran las máquinas supervisadas y sus asociaciones, siempre y cuando estén en el mismo inquilino.

1. En la pestaña **Recursos,** seleccione **+Agregar recursos** para agregar máquinas a las que se aplicará la regla de recopilación de datos. Se abrirá el cuadro de diálogo **Seleccionar un ámbito** y verá una lista de suscripciones disponibles. Expanda una suscripción para ver sus grupos de recursos y expanda un grupo de recursos para ver las máquinas disponibles. Verá las máquinas virtuales de Azure y los servidores habilitados para Azure Arc en la lista. Puede marcar las casillas de suscripciones o grupos de recursos para seleccionar todas las máquinas que contienen, o puede seleccionar máquinas individuales. Seleccione **Aplicar** cuando haya elegido todas las máquinas. Al final de este proceso, el agente de Azure Monitor se instalará en las máquinas seleccionadas que aún no lo tengan instalado.

1. En la pestaña **Recopilar**, elija los eventos que le gustaría recopilar: seleccione **Todos los eventos** o **Personalizado** para especificar otros registros o filtrar eventos usando las [consultas de XPath](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries) (consulte la nota que tiene a continuación). Escriba expresiones en el cuadro que se evalúen como criterios XML específicos para los eventos que se recopilan y, a continuación, seleccione **Agregar**. Puede escribir hasta 20 expresiones en un solo cuadro y hasta 100 cuadros en una regla.

    Obtenga más información sobre las [reglas de recopilación de datos](../azure-monitor/agents/data-collection-rule-overview.md#create-a-dcr) en la documentación de Azure Monitor.

    > [!NOTE]
    > - El conector de eventos de seguridad de Windows ofrece otros dos [**conjuntos de eventos precompilados**](windows-security-event-id-reference.md) que puede recopilar: el **común** y el **mínimo**.
    >
    > - El agente de Azure Monitor admite consultas XPath solo para la **[versión 1.0 de XPath](/windows/win32/wes/consuming-events#xpath-10-limitations)** .

1. Cuando haya agregado todas las expresiones de filtro que quiera, seleccione **Siguiente: Revisar y crear**.

1. Cuando vea el mensaje "Validación superada", seleccione **Crear**. 

Verá todas las reglas de recopilación de datos (incluidas las creadas a través de la API) en **Configuración** en la página del conector. Desde allí, puede editar o eliminar las reglas existentes.

> [!TIP]
> Use el cmdlet de PowerShell **Get-WinEvent** con el parámetro *-FilterXPath* para probar la validez de una consulta XPath. En el script siguiente se muestra un ejemplo:
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
> - Si se devuelven eventos, la consulta es válida.
> - Si recibe el mensaje “No se encontraron eventos que coincidan con los criterios de selección especificados”, la consulta puede ser válida, pero no hay eventos coincidentes en el equipo local.
> - Si recibe el mensaje “La consulta especificada no es válida”, la sintaxis de la consulta no es válida.

### <a name="create-data-collection-rules-using-the-api"></a>Creación de reglas de recopilación de datos mediante la API

También puede crear reglas de recopilación de datos mediante la API ([consulte el esquema](/rest/api/monitor/data-collection-rules)), lo que le puede facilitar la vida si va a crear muchas reglas (por ejemplo, si es un MSSP). A continuación, se muestra un ejemplo (para los [eventos de seguridad de Windows a través del conector AMA](data-connectors-reference.md#windows-security-events-via-ama)) que puede usar como plantilla para crear una regla:

**Encabezado y dirección URL de solicitud**

```http
PUT https://management.azure.com/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.Insights/dataCollectionRules/myCollectionRule?api-version=2019-11-01-preview
```

**Cuerpo de la solicitud**

```json
{
    "location": "eastus",
    "properties": {
        "dataSources": {
            "windowsEventLogs": [
                {
                    "streams": [
                        "Microsoft-SecurityEvent"
                    ],
                    "xPathQueries": [
                        "Security!*[System[(EventID=) or (EventID=4688) or (EventID=4663) or (EventID=4624) or (EventID=4657) or (EventID=4100) or (EventID=4104) or (EventID=5140) or (EventID=5145) or (EventID=5156)]]"
                    ],
                    "name": "eventLogsDataSource"
                }
            ]
        },
        "destinations": {
            "logAnalytics": [
                {
                    "workspaceResourceId": "/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.OperationalInsights/workspaces/centralTeamWorkspace",
                    "name": "centralWorkspace"
                }
            ]
        },
        "dataFlows": [
            {
                "streams": [
                    "Microsoft-SecurityEvent"
                ],
                "destinations": [
                    "centralWorkspace"
                ]
            }
        ]
    }
}
```
Consulte esta [descripción completa de las reglas de recopilación de datos](../azure-monitor/agents/data-collection-rule-overview.md) de la documentación de Azure Monitor.


# <a name="log-analytics-agent-legacy"></a>[Agente de Log Analytics (heredado)](#tab/LAA)

### <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Log Analytics y en cualquier área de trabajo que contenga máquinas de las que quiera recopilar registros.
- Debe tener el rol **Colaborador de Log Analytics** en la solución SecurityInsights (Azure Sentinel) de esas áreas de trabajo, además de cualquier rol de Azure Sentinel.

### <a name="instructions"></a>Instrucciones

#### <a name="install-the-agent"></a>Instalación del agente

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. Seleccione el servicio (**DNS** o **Windows Firewall** [Firewall de Windows]), y después **Open connector page** (Abrir página del conector).

1. Instale e incorpore el agente en el dispositivo que genera los registros.

    | Tipo de máquina  | Instrucciones  |
    | --------- | --------- |
    | **Para una máquina virtual de Azure Windows** | 1. En **Choose where to install the agent** (Elegir dónde instalar el agente), expanda **Install agent on Azure Windows virtual machine** (Instalar el agente en la máquina virtual Windows de Azure). <br><br>2. Seleccione el vínculo **Download & install agent for Azure Windows Virtual machines >** (Descargar e instalar el agente para máquinas virtuales Windows de Azure >). <br><br>3. En el panel **Máquinas virtuales**, seleccione una máquina virtual en la que quiera instalar el agente y, después, seleccione **Conectar**. Repita este paso para cada VM que quiera conectar. |
    | **Para cualquier otra máquina Windows** | 1. En **Choose where to install the agent** (Elegir dónde instalar el agente), expanda **Install agent on non-Azure Windows Machine** (Instalar el agente en la máquina virtual Windows que no es de Azure). <br><br>2. Seleccione el vínculo **Download & install agent for non-Azure Windows machines >** (Descargar e instalar el agente para máquinas virtuales Windows que no son de Azure >).  <br><br>3. En el panel **Administración de agentes**, en la pestaña **Servidores Windows**, seleccione el vínculo **Download Windows Agent** (Descargar el agente de Windows) para sistemas de 32 o 64 bits, según corresponda.  <br><br>4. Con el archivo ejecutable descargado, instale el agente en los sistemas Windows que prefiera y configúrelo mediante **el id. y las claves del área de trabajo** que aparecen bajo los vínculos de descarga en el paso anterior. |
    | | |

> [!NOTE]
>
> Para permitir que los sistemas Windows que no tengan la conexión a Internet necesaria sigan transmitiendo eventos a Azure Sentinel, descargue e instale la **Puerta de enlace de Log Analytics** en una máquina distinta, mediante el vínculo **Descarga de la Puerta de enlace de Log Analytics** que está en la página **Administración de agentes**, y que así actúe como un proxy.  Tendrá que instalar el agente de Log Analytics en todos los sistemas Windows cuyos eventos quiera recopilar.
>
> Para obtener más información sobre este escenario, consulte la [documentación de la **puerta de enlace de Log Analytics**](../azure-monitor/agents/gateway.md).

Para obtener más información sobre las opciones de instalación adicionales y otros detalles, consulte la [documentación del **agente de Log Analytics**](../azure-monitor/agents/agent-windows.md).


#### <a name="determine-the-logs-to-send"></a>Determinación de los registros que se envían

Para los conectores de Windows DNS Server y Windows Firewall, seleccione el botón **Instalar solución**. Para el conector de eventos de seguridad heredado, elija el [**conjunto de eventos**](windows-security-event-id-reference.md) que quiere enviar y seleccione **Actualizar**.

Puede buscar y consultar los datos de estos servicios mediante los nombres de tabla de sus respectivas secciones en la página de [referencia de los conectores de datos](data-connectors-reference.md).

---

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar servicios de Azure, Microsoft y Windows, así como Amazon Web Services, a Azure Sentinel. 
- Obtenga información general sobre los [conectores de datos de Azure Sentinel](connect-data-sources.md).
- [Búsqueda del conector de datos de Azure Sentinel](data-connectors-reference.md).
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
