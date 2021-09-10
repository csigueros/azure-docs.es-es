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
ms.openlocfilehash: c13e2e5ad14ada3c867682382b334242881ddb78
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260960"
---
# <a name="connect-azure-sentinel-to-azure-windows-microsoft-and-amazon-services"></a>Conexión de Azure Sentinel a servicios de Azure, Windows, Microsoft y Amazon

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

## <a name="log-analytics-agent-based-connections"></a>Conexiones basadas en el agente de Log Analytics

### <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Log Analytics y en cualquier área de trabajo que contenga máquinas de las que quiera recopilar registros.
- Debe tener el rol **Colaborador de Log Analytics** en la solución SecurityInsights (Azure Sentinel) de esas áreas de trabajo, además de cualquier rol de Azure Sentinel.

### <a name="instructions"></a>Instrucciones

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. Seleccione el servicio (**DNS** o **Windows Firewall** [Firewall de Windows]), y después **Open connector page** (Abrir página del conector).

1. Instale e incorpore el agente en el dispositivo que genera los registros.

    | Tipo de máquina  | Instrucciones  |
    | --------- | --------- |
    | **Para una máquina virtual de Azure Windows** | 1. En **Choose where to install the agent** (Elegir dónde instalar el agente), expanda **Install agent on Azure Windows virtual machine** (Instalar el agente en la máquina virtual Windows de Azure). <br><br>2. Seleccione el vínculo **Download & install agent for Azure Windows Virtual machines >** (Descargar e instalar el agente para máquinas virtuales Windows de Azure >). <br><br>3. En el panel **Máquinas virtuales**, seleccione una máquina virtual en la que quiera instalar el agente y, después, seleccione **Conectar**. Repita este paso para cada VM que quiera conectar. |
    | **Para cualquier otra máquina Windows** | 1. En **Choose where to install the agent** (Elegir dónde instalar el agente), expanda **Install agent on non-Azure Windows Machine** (Instalar el agente en la máquina virtual Windows que no es de Azure). <br><br>2. Seleccione el vínculo **Download & install agent for non-Azure Windows machines >** (Descargar e instalar el agente para máquinas virtuales Windows que no son de Azure >).  <br><br>3. En el panel **Administración de agentes**, en la pestaña **Servidores Windows**, seleccione el vínculo **Download Windows Agent** (Descargar el agente de Windows) para sistemas de 32 o 64 bits, según corresponda.      |

1. Seleccione el botón **Instalar solución** para DNS o Firewall de Windows.

Puede buscar y consultar los datos de DNS y Firewall de Windows mediante los nombres de tabla **DnsEvents**, **DnsInventory** y **WindowsFirewall**, respectivamente. Puede ver esta y otra información sobre estos dos conectores de servicio en sus secciones de la página [Referencia de conectores de datos](data-connectors-reference.md).


## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar servicios de Azure, Microsoft y Windows, así como Amazon Web Services, a Azure Sentinel. 
- Obtenga información general sobre los [conectores de datos de Azure Sentinel](connect-data-sources.md).
- [Búsqueda del conector de datos de Azure Sentinel](data-connectors-reference.md).
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).