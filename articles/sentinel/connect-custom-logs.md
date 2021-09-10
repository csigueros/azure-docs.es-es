---
title: Recopilación de datos en formatos de registro personalizados para Azure Sentinel | Microsoft Docs
description: Recopile datos de orígenes de datos personalizados e ingiéralos en Azure Sentinel mediante el agente de Log Analytics.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: d99785da00f277480505b417947781821df1caa2
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260962"
---
# <a name="collect-data-in-custom-log-formats-to-azure-sentinel-with-the-log-analytics-agent"></a>Recopilación de datos en formatos de registro personalizados para Azure Sentinel con el agente de Log Analytics

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Muchas aplicaciones registran datos en archivos de texto, en lugar de los servicios de registro estándar, como el registro de eventos de Windows o Syslog. Puede usar el agente de Log Analytics para recopilar datos en archivos de texto de formatos no estándar de equipos Windows y Linux. Una vez recopilados, puede analizar los datos en campos individuales en las consultas o extraerlos durante la recopilación de campos individuales.

En este artículo se describe cómo conectar los orígenes de datos a Azure Sentinel mediante formatos de registro personalizados. Para obtener más información sobre los conectores de datos compatibles que usan este método, consulte [Referencia de conectores de datos](data-connectors-reference.md).

Obtenga información sobre los [registros personalizados en la documentación de Azure Monitor](../azure-monitor/agents/data-sources-custom-logs.md).

De forma similar a Syslog, hay dos pasos para configurar la recopilación de registros personalizados:

- Instale el agente de Log Analytics en la máquina Linux o Windows que generará los registros.

- Configure los valores de registro de la aplicación.

- Configure el agente de Log Analytics desde dentro de Azure Sentinel.

## <a name="install-the-log-analytics-agent"></a>Instalación del agente de Log Analytics

Instale el agente de Log Analytics en la máquina Linux o Windows que generará los registros.

> [!NOTE]
> Algunos proveedores recomiendan instalar el agente de Log Analytics en un servidor de registro independiente en lugar de hacerlo directamente en el dispositivo. Consulte la sección del producto en la página [Referencia de conectores de datos](data-connectors-reference.md) o la documentación del producto.

Seleccione la pestaña adecuada a continuación, en función de si el conector tiene una página del conector de datos en Azure Sentinel.

# <a name="from-a-specific-data-connector-page"></a>[Desde una página específica del conector de datos](#tab/DCG)

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. Seleccione el tipo de dispositivo y, a continuación, seleccione **Abrir página del conector**.

1. Instale e incorpore el agente en el dispositivo que genera los registros. Elija Linux o Windows según corresponda.

    | Tipo de máquina | Instrucciones |
    | --------- | --------- |
    | **Para una VM Linux de Azure** | <ol><li>En **Elegir dónde instalar el agente de Linux**, expanda **Instalación del agente en una máquina virtual Linux de Azure**.<br><br><li>Seleccione el vínculo **Descargar e instalar el agente para máquinas virtuales Linux de Azure >** .<br><br><li>En la hoja **Máquinas virtuales**, seleccione una máquina virtual en la que quiera instalar el agente y, después, seleccione **Conectar**. Repita este paso para cada VM que quiera conectar. |
    | **Para cualquier otra máquina Linux** | <ol><li>En **Elegir dónde instalar el agente Linux**, expanda **Instalación del agente en una máquina Linux que no sea de Azure**.<br><br><li>Seleccione el vínculo **Descargar e instalar el agente para máquinas Linux que no sean de Azure >** .<br><br><li>En la hoja **Administración de agentes**, seleccione la pestaña **Servidores Linux** y, a continuación, copie el comando para **descargar e incorporar el agente para Linux** y ejecútelo en la máquina Linux.<br><br> Si desea mantener una copia local del archivo de instalación del agente de Linux, seleccione el vínculo **Descargar agente de Linux** encima del comando "Descargar e incorporar agente".|
    | **Para una máquina virtual de Azure Windows** | <ol><li>En **Elegir dónde instalar el agente de Windows**, expanda **Instalación del agente en una máquina virtual Windows de Azure**.<br><br><li>Seleccione el vínculo **Descargar e instalar el agente para máquinas virtuales Windows de Azure >** .<br><br><li>En la hoja **Máquinas virtuales**, seleccione una máquina virtual en la que quiera instalar el agente y, después, seleccione **Conectar**. Repita este paso para cada VM que quiera conectar. |
    | **Para cualquier otra máquina Windows** | <ol><li>En **Elegir dónde instalar el agente de Windows**, expanda **Instalación del agente en una máquina Windows que no sea de Azure**.<br><br><li>Seleccione el vínculo **Descargar e instalar el agente para máquinas Windows que no sean de Azure >** .<br><br><li>En la hoja **Administración de agentes**, en la pestaña **Servidores Windows**, seleccione el vínculo **Descargar el agente de Windows**  para sistemas de 32 o 64 bits, según corresponda. |


# <a name="other-data-sources"></a>[Otros orígenes de datos](#tab/CUS)

1. En el menú de navegación de Azure Sentinel, seleccione **Configuración** y, a continuación, la pestaña **Configuración del área de trabajo**.

1. Instale e incorpore el agente en el dispositivo que genera los registros. Elija Linux o Windows según corresponda.

    | Tipo de máquina | Instrucciones |
    | --------- | --------- |
    | **Máquina virtual de Azure (Windows o Linux)** | <ol><li>En el menú de navegación del área de trabajo de Log Analytics, seleccione **Máquinas virtuales**.<br><br><li>En la hoja **Máquinas virtuales**, seleccione una máquina virtual en la que quiera instalar el agente y, después, seleccione **Conectar**.<br>Repita este paso para cada VM que quiera conectar. |
    | **Cualquier otra máquina Windows o Linux** | <ol><li>En el menú de navegación del área de trabajo de Log Analytics, seleccione **Administración de agentes**.<br><br><li>Seleccione las pestañas **Servidores Windows** o **Servidores Linux** según corresponda.<br><br><li>Para Windows, seleccione el vínculo **Descargar el agente de Windows** para sistemas de 32 o 64 bits, según corresponda. Para Linux, copie el comando para **Descargar e incorporar agente para Linux** y ejecútelo desde la línea de comandos, o seleccione el vínculo **Descargar el agente de Linux** para descargar una copia local del archivo de instalación. |

---

## <a name="configure-the-logs-to-be-collected"></a>Configure los registros que se van a recopilar.

Muchos tipos de dispositivo tienen sus propios conectores de datos que aparecen en la página **Conectores de datos** de Azure Sentinel. Algunos de estos conectores requieren instrucciones adicionales especiales para configurar correctamente la recopilación de registros en Azure Sentinel. Estas instrucciones pueden incluir la implementación de un analizador basado en una función de Kusto. 

Todos los conectores enumerados en Azure Sentinel mostrarán instrucciones específicas en sus respectivas páginas de conector en el portal, así como en sus secciones de la página [Referencia de conectores de datos de Azure Sentinel](data-connectors-reference.md).

Si el producto no aparece en la página **Conectores de datos**, consulte la documentación del proveedor para obtener instrucciones sobre cómo configurar el registro para el dispositivo.

## <a name="configure-the-log-analytics-agent"></a>Configuración del agente de Log Analytics

1. En la página del conector, seleccione el vínculo **Open your workspace custom logs configuration** (Abrir la configuración de los registros personalizados del área de trabajo).
    O bien, en el menú de navegación del área de trabajo de Log Analytics, seleccione **Registros personalizados**.

1. En la pestaña **Tablas personalizadas**, seleccione **Agregar registro personalizado**.

1. En la pestaña **Muestra**, cargue una muestra de un archivo de registro desde el dispositivo (por ejemplo, access.log o error.log). Después, seleccione **Siguiente**.

1. En la pestaña **Delimitador de registro**, seleccione un delimitador de registro, ya sea **Nueva línea** o **Marca de tiempo** (consulte las instrucciones de esa pestaña), y seleccione **Siguiente**.

1. En la pestaña **Rutas de acceso de la colección**, seleccione un tipo de ruta de acceso de Windows o Linux, y escriba la ruta de acceso en los registros del dispositivo en función de la configuración. Después, seleccione **Siguiente**.

1. Asigne un nombre a su registro personalizado y, de forma opcional, una descripción y seleccione **Siguiente**.  
    No termine el nombre con "_CL", ya que se anexará automáticamente.


## <a name="find-your-data"></a>Búsqueda de sus datos

Para consultar los datos de registro personalizados en **Registros**, escriba el nombre que asignó al registro personalizado (que termina en "_CL") en la ventana de consulta.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a recopilar datos de tipos de registro personalizados para ingerirlos en Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.
