---
title: Procedimientos recomendados para la recopilación de datos en Azure Sentinel
description: Obtenga información sobre los procedimientos recomendados que se deben emplear al conectar orígenes de datos a Azure Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: 30132d08a1094fc2bd4b7423ed787eadf77c2249
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178725"
---
#  <a name="data-collection-best-practices"></a>Procedimientos recomendados para la recopilación de datos

En esta sección se revisan los procedimientos recomendados para recopilar datos mediante conectores de datos de Azure Sentinel. Para obtener más información, consulte [Conexión de orígenes de datos](connect-data-sources.md), [Conectores de datos de partners de Azure Sentinel](partner-data-connectors.md) y el [catálogo de soluciones de Azure Sentinel](sentinel-solutions-catalog.md).

## <a name="prioritize-your-data-connectors"></a>Priorización de sus conectores de datos

Si no tiene claro qué conectores de datos satisfarán mejor las necesidades de su entorno, empiece por habilitar todos los [conectores de datos gratuitos](azure-sentinel-billing.md#free-data-sources).

Los conectores de datos gratuitos empezarán a mostrar el valor de Azure Sentinel lo antes posible, mientras sigue planeando otros conectores de datos y presupuestos.

En el caso de sus conectores de datos [de partners](partner-data-connectors.md) y [personalizados](create-custom-connector.md), empiece configurando los conectores [Syslog](connect-syslog.md) y [CEF](connect-common-event-format.md), primero los de mayor prioridad, así como dispositivos basados en Linux.

Si el coste de la ingesta de datos aumenta demasiado rápido, detenga o filtre los registros reenviados usando el [agente de Azure Monitor](../azure-monitor/agents/azure-monitor-agent-overview.md).

> [!TIP]
> Los conectores de datos personalizados permiten ingerir datos en Azure Sentinel desde orígenes de datos que no son compatibles actualmente con la funcionalidad integrada, como el agente, Logstash o la API. Para obtener más información, consulte [Recursos para crear conectores personalizados de Azure Sentinel](create-custom-connector.md).
>

## <a name="filter-your-logs-before-ingestion"></a>Filtrado de los registros antes de la ingesta

Es posible que le interese filtrar los registros recopilados, o incluso el contenido de estos, antes de que los datos se ingieren en Azure Sentinel. Por ejemplo, puede que desee filtrar los registros que son irrelevantes o poco importantes para las operaciones de seguridad, o bien eliminar los detalles no deseados de los mensajes de registro. Filtrar el contenido de los mensajes también puede ser útil al intentar reducir los costes cuando se trabaja con registros Syslog, CEF o basados en Windows en archivos que tengan muchos detalles irrelevantes.

Filtre los registros usando uno de los siguientes métodos:

- **Agente de Azure Monitor**. Se admite en Windows y Linux para ingerir [eventos de seguridad del primero](connect-windows-security-events.md). Filtre los registros recopilados configurando el agente para recopilar solo los eventos especificados.

- **Logstash**. Permite filtrar el contenido de mensajes y realizar cambios en los mensajes de registro. Para obtener más información, consulte [Conexión con Logstash](create-custom-connector.md#connect-with-logstash).

> [!IMPORTANT]
> El uso de Logstash para filtrar el contenido de mensajes hará que sus registros se ingieran como registros personalizados, lo que a su vez provocará que los [registros de nivel gratuito](azure-sentinel-billing.md#free-data-sources) pasen a ser de nivel de pago.
>
> Los registros personalizados también deben someterse a [reglas de análisis](automate-incident-handling-with-automation-rules.md), [búsqueda de amenazas](hunting.md) y [libros](get-visibility.md), ya que no se agregan automáticamente. Los registros personalizados tampoco son actualmente compatibles con funcionalidades de [aprendizaje automático](bring-your-own-ml.md).
>

## <a name="alternative-data-ingestion-requirements"></a>Requisitos de ingesta de datos alternativos

Es posible que la configuración estándar de la recopilación de datos no ofrezca un buen resultado a su organización, debido a varias dificultades. En las siguientes tablas se describen desafíos o requisitos habituales y posibles soluciones y consideraciones.

> [!NOTE]
> Muchas de las soluciones que se enumeran a continuación requieren un conector de datos personalizado. Para obtener más información, consulte [Recursos para crear conectores personalizados de Azure Sentinel](create-custom-connector.md).
>

### <a name="on-premises-windows-log-collection"></a>Recopilación local de registros de Windows


|Dificultad/requisito  |Posibles soluciones  |Consideraciones  |
|---------|---------|---------|
|**Requiere filtrado de registros**     | Use Logstash. <br><br>Uso de Azure Functions <br><br> Use LogicApps. <br><br> Use código personalizado (.NET o Python).  |  Aunque el filtrado puede traducirse en un ahorro de costes y solo ingiere los datos necesarios, algunas características de Azure Sentinel no son compatibles, como [UEBA](identify-threats-with-entity-behavior-analytics.md), las [páginas de entidad](identify-threats-with-entity-behavior-analytics.md#entity-pages), el [aprendizaje automático](bring-your-own-ml.md) y la [fusión](fusion.md). <br><br>Al configurar el filtrado de registros, deberá realizar actualizaciones en recursos como las consultas de búsqueda de amenazas y las reglas de análisis.     |
|**No se puede instalar el agente**     |Use el reenvío de eventos de Windows junto con el [agente de Azure Monitor](connect-windows-security-events.md#connector-options).       |   El reenvío de eventos de Windows reduce los eventos de equilibrio de carga por segundo del recopilador de eventos de Windows, de 10 000 a 500-1000.|
|**Los servidores no se conectan a Internet**     | Use la [puerta de enlace de Log Analytics](../azure-monitor/agents/gateway.md).        | Para configurar un proxy a su agente, hacen falta reglas de firewall adicionales que permitan que la puerta de enlace funcione.        |
|**Requiere etiquetado y enriquecimiento en la ingesta**     |Use Logstash para insertar un Id. de recurso. <br><br>Use una plantilla de ARM para insertar un Id. de recurso en máquinas locales. <br><br>Ingiera el Id. de recurso en áreas de trabajo independientes.        | Log Analytics no admite RBAC para tablas personalizadas. <br><br>Azure Sentinel no admite RBAC de nivel de fila. <br><br>**Sugerencia:** Es posible que desee adoptar el diseño y la funcionalidad entre áreas de trabajo para Azure Sentinel.        |
|**Requiere dividir los registros operaciones y seguridad**     | Use la funcionalidad de hospedaje múltiple de [Microsoft Monitoring Agent o del agente de Azure Monitor](connect-windows-security-events.md).        |  La funcionalidad de hospedaje múltiple requiere una mayor sobrecarga de implementación para el agente.       |
|**Requiere registros personalizados**     |   Recopile archivos de rutas de acceso de carpeta específicas. <br><br>Use la ingesta de API. <br><br>Uso de PowerShell <br><br>Use Logstash.     |   Es posible que tenga problemas para filtrar los registros. <br><br>No se admiten métodos personalizados. <br><br>Los conectores personalizados pueden requerir aptitudes de desarrollador.       |
| | | |

### <a name="on-premises-linux-log-collection"></a>Recopilación local de registros de Linux

|Dificultad/requisito  |Posibles soluciones  |Consideraciones  |
|---------|---------|---------|
|**Requiere filtrado de registros**     | Use Syslog-NG. <br><br>Use Rsyslog. <br><br>Use la configuración de FluentD para el agente. <br><br> Use el agente de Azure Monitor o Microsoft Monitoring Agent. <br><br> Use Logstash.  |  Es posible que algunas distribuciones de Linux no sean compatibles con el agente. <br> <br>El uso de Syslog o FluentD requiere conocimientos de desarrollo. <br><br>Para obtener más información, consulte [Conexión a servidores Windows para recopilar eventos de seguridad](connect-windows-security-events.md) y [Recursos para crear conectores personalizados de Azure Sentinel](create-custom-connector.md).      |
|**No se puede instalar el agente**     |  Use un reenviador de Syslog, como (syslog-ng o rsyslog.       |         |
|**Los servidores no se conectan a Internet**       | Use la [puerta de enlace de Log Analytics](../azure-monitor/agents/gateway.md).        | Para configurar un proxy a su agente, hacen falta reglas de firewall adicionales que permitan que la puerta de enlace funcione.          |
|**Requiere etiquetado y enriquecimiento en la ingesta**      | Use Logstash para el enriquecimiento o métodos personalizados, como API o EventHubs.         | Es posible que el filtrado requiera un esfuerzo adicional.    |
|**Requiere dividir los registros operaciones y seguridad**     |  Use el [agente de Azure Monitor](connect-windows-security-events.md) con la configuración de hospedaje múltiple.       |         |
|**Requiere registros personalizados**     |    Cree un recopilador personalizado usando Microsoft Monitoring Agent (Log Analytics).      |      |
| | | |

### <a name="endpoint-solutions"></a>Soluciones de punto de conexión

Si necesita recopilar registros de soluciones de punto de conexión, como EDR, otros eventos de seguridad, Sysmon, etc., use uno de los siguientes métodos:

- **Conector MTP** para recopilar registros de Microsoft 365 Defender para punto de conexión. Esta opción conlleva costes adicionales por la ingesta de datos.
- **Reenvío de eventos de Windows**.

> [!NOTE]
> El equilibrio de carga reduce los eventos por segundo que se pueden procesar en el área de trabajo.
>

### <a name="office-data"></a>Datos de Office

Si necesita recopilar datos de Microsoft Office aparte de los del conector estándar, use una de las siguientes soluciones:

|Dificultad/requisito  |Posibles soluciones  |Consideraciones  |
|---------|---------|---------|
|**Recopilación de datos sin procesar de Teams, seguimiento de mensajes, datos de phishing, etc.**     |    Use la funcionalidad integrada del [conector de Office 365](connect-office-365.md) y cree un conector personalizado para otros datos sin procesar.  |  La asignación de eventos al Id. de registro correspondiente puede plantear dificultades.  |
|**Requiere RBAC para dividir países, departamentos, etc.**     | Personalice la recopilación de datos agregando etiquetas a los datos y creando áreas de trabajo dedicadas para cada separación necesaria.|   La recopilación de datos personalizada tiene costes extras de ingesta.     |
|**Requiere varios inquilinos en una sola área de trabajo**     |  Personalice la recopilación de datos usando Azure LightHouse y una vista unificada de incidentes.|  La recopilación de datos personalizada tiene costes extras de ingesta.  <br><br>Para obtener más información, consulte [Extensión de Azure Sentinel por áreas de trabajo e inquilinos](extend-sentinel-across-workspaces-tenants.md).      |
| | | |

### <a name="cloud-platform-data"></a>Datos de plataforma de nube

|Dificultad/requisito  |Posibles soluciones  |Consideraciones  |
|---------|---------|---------|
|**Filtrado de registros de otras plataformas**     | Use Logstash. <br><br>Use el agente de Azure Monitor o Microsoft Monitoring Agent (Log Analytics).       |    La recopilación personalizada tiene costes extras de ingesta. <br><br>Es posible que tenga que recopilar todos los eventos de Windows o solo los de seguridad.     |
|**No se puede usar el agente**     |   Use el reenvío de eventos de Windows.      | Es posible que tenga que equilibrar la carga entre los recursos.        |
|**Los servidores están en redes separadas**     | Use la [puerta de enlace de Log Analytics](../azure-monitor/agents/gateway.md).        | Para configurar un proxy a su agente, hacen falta reglas de firewall que permitan que la puerta de enlace funcione.        |
|**RBAC, etiquetado y enriquecimiento en la ingesta**     |    Cree una recopilación personalizada a través de Logstash o la API de Log Analytics.     |  RBAC no es compatibles con tablas personalizadas <br><br>RBAC de nivel de fila no es compatible con ninguna tabla.       |
|     |         |         |

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Actividades previas a la implementación y requisitos previos para implementar Azure Sentinel](prerequisites.md)
- [Procedimientos recomendados para Azure Sentinel](best-practices.md)
- [Conexión con orígenes de datos](connect-data-sources.md)