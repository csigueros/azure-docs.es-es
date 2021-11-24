---
title: Conexión de datos de Microsoft 365 Defender con Microsoft Sentinel | Microsoft Docs
description: Obtenga información sobre cómo ingerir incidentes, alertas y datos de eventos sin procesar desde Microsoft 365 Defender en Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0ad84db74a303277323c800edf88ff2bd9197738
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718116"
---
# <a name="connect-data-from-microsoft-365-defender-to-microsoft-sentinel"></a>Conexión de datos de Microsoft 365 Defender con Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> **Microsoft 365 Defender** se conocía antes como **Microsoft Threat Protection** o **MTP**.
>
> **Microsoft Defender para punto de conexión** se conocía antes como **Protección contra amenazas avanzada de Microsoft Defender** o **MDATP**.
>
> **Microsoft Defender para Office 365** se conocía anteriormente como **Protección contra amenazas avanzada de Office 365**.
>
> Es posible que vea que los nombres antiguos todavía están en uso durante un tiempo.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="background"></a>Fondo

El conector [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) de Microsoft Sentinel con la integración de incidentes le permite transmitir todos los incidentes y alertas de M365D a Microsoft Sentinel y mantiene los incidentes sincronizados entre ambos portales. Los incidentes de M365D incluyen todas sus alertas, entidades y otra información relevante, y se enriquecen y agrupan las alertas de los servicios de componentes de M365D **Microsoft Defender para punto de conexión**, **Microsoft Defender for Identity**, **Microsoft Defender para Office 365** y **Microsoft Defender for Cloud Apps**.

El conector también permite transmitir eventos de **búsqueda avanzada** desde Microsoft Defender para punto de conexión y Microsoft Defender para Office 365 a Microsoft Sentinel, lo que permite copiar dichas consultas de búsqueda avanzada de los componentes de Defender en Microsoft Sentinel, enriquecer las alertas de Sentinel con datos de eventos sin formato de los componentes de Defender para proporcionar información adicional y almacenar los registros con una mayor retención en Log Analytics.

Para obtener más información sobre la integración de incidentes y la recopilación de eventos de búsqueda avanzada, consulte [Integración de Microsoft 365 Defender en Microsoft Sentinel](microsoft-365-defender-sentinel-integration.md#advanced-hunting-event-collection).

> [!IMPORTANT]
>
> El conector Microsoft 365 Defender está actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener una licencia válida de Microsoft 365 Defender, como se describe en [Requisitos previos de Microsoft 365 Defender](/microsoft-365/security/mtp/prerequisites). 

- Debe ser **administrador global** o un **administrador de seguridad** en Azure Active Directory.

## <a name="connect-to-microsoft-365-defender"></a>Conexión a Microsoft 365 Defender

1. En Microsoft Sentinel, seleccione **Conectores de datos**, elija **Microsoft 365 Defender (versión preliminar)** en la galería y seleccione **Abrir página del conector**.

1. En la sección **Conectar incidentes y alertas** de **Configuración**, seleccione el botón **Conectar incidentes y alertas**.

1. Para evitar la duplicación de incidentes, se recomienda marcar la casilla **Turn off all Microsoft incident creation rules for these products** (Desactivar todas las reglas de creación de incidentes de Microsoft para estos productos).

    > [!NOTE]
    > Cuando se habilita el conector de Microsoft 365 Defender, todos los conectores de los componentes de M365D (los que se mencionan al principio de este artículo) se conectan automáticamente en segundo plano. Para desconectar uno de los conectores de los componentes, primero debe desconectar el conector de Microsoft 365 Defender.

1. Para consultar los datos de incidentes de Microsoft 365 Defender, use la instrucción siguiente en la ventana de consulta:
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Si quiere recopilar eventos de búsqueda avanzada de Microsoft Defender para punto de conexión o Microsoft Defender para Office 365, se pueden recopilar los siguientes tipos de eventos de sus tablas de búsqueda avanzada correspondientes.

    1. Marque las casillas de las tablas con los tipos de evento que quiera recopilar:

       # <a name="defender-for-endpoint"></a>[Defender para punto de conexión](#tab/MDE)

       | Nombre de la tabla | Tipo de eventos |
       |-|-|
       | **[DeviceInfo](/microsoft-365/security/defender/advanced-hunting-deviceinfo-table)** | Información de la máquina, incluida la información del sistema operativo |
       | **[DeviceNetworkInfo](/microsoft-365/security/defender/advanced-hunting-devicenetworkinfo-table)** | Propiedades de red de los dispositivos, incluidos adaptadores físicos y direcciones IP y MAC, así como redes y dominios conectados |
       | **[DeviceProcessEvents](/microsoft-365/security/defender/advanced-hunting-deviceprocessevents-table)** | Creación de procesos y eventos relacionados |
       | **[DeviceNetworkEvents](/microsoft-365/security/defender/advanced-hunting-devicenetworkevents-table)** | Conexión de red y eventos relacionados |
       | **[DeviceFileEvents](/microsoft-365/security/defender/advanced-hunting-devicefileevents-table)** | Creación y modificación de archivos, y otros eventos del sistema de archivos |
       | **[DeviceRegistryEvents](/microsoft-365/security/defender/advanced-hunting-deviceregistryevents-table)** | Creación y modificación de entradas del Registro |
       | **[DeviceLogonEvents](/microsoft-365/security/defender/advanced-hunting-devicelogonevents-table)** | Inicios de sesión y otros eventos de autenticación en dispositivos |
       | **[DeviceImageLoadEvents](/microsoft-365/security/defender/advanced-hunting-deviceimageloadevents-table)** | Eventos de carga de DLL |
       | **[DeviceEvents](/microsoft-365/security/defender/advanced-hunting-deviceevents-table)** | Varios tipos de eventos, incluidos los desencadenados por controles de seguridad como Antivirus de Windows Defender y protección contra vulnerabilidades |
       | **[DeviceFileCertificateInfo](/microsoft-365/security/defender/advanced-hunting-DeviceFileCertificateInfo-table)** | Información de certificado de los archivos firmados obtenidos de los eventos de comprobación de certificados en los puntos de conexión |
       |

       # <a name="defender-for-office-365"></a>[Defender para Office 365](#tab/MDO)

       | Nombre de la tabla | Tipo de eventos |
       |-|-|
       | **[EmailAttachmentInfo](/microsoft-365/security/defender/advanced-hunting-emailattachmentinfo-table)** | Información sobre los archivos adjuntos a correos electrónicos |
       | **[EmailEvents](/microsoft-365/security/defender/advanced-hunting-emailevents-table)** | Eventos de correo electrónico de Microsoft 365, incluidos los eventos de entrega y bloqueo de correo electrónico |
       | **[EmailPostDeliveryEvents](/microsoft-365/security/defender/advanced-hunting-emailpostdeliveryevents-table)** | Eventos de seguridad que se producen después de la entrega, después de que Microsoft 365 haya entregado los correos electrónicos en el buzón del destinatario |
       | **[EmailUrlInfo](/microsoft-365/security/defender/advanced-hunting-emailurlinfo-table)** | Información sobre las direcciones URL de los correos electrónicos |
       |

       ---

    1. Haga clic en **Aplicar cambios**.

    1. Para consultar las tablas de búsqueda avanzada en Log Analytics, escriba el nombre de la tabla de la lista anterior en la ventana de consulta.

## <a name="verify-data-ingestion"></a>Comprobación de la ingesta de datos

El grafo de datos de la página del conector indica que se están ingiriendo datos. Observará que se muestra una línea para incidentes, alertas y eventos, y la línea de eventos es una agregación del volumen de eventos en todas las tablas habilitadas. Una vez que haya habilitado el conector, puede usar las siguientes consultas de KQL para generar grafos más específicos.

Use la siguiente consulta de KQL para obtener un grafo de los incidentes de Microsoft 365 Defender entrantes:

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

Use la siguiente consulta de KQL para generar un grafo de volumen de eventos para una sola tabla (cambie la tabla *DeviceEvents* por la tabla necesaria que prefiera):

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

En la pestaña **Pasos siguientes** encontrará algunos libros útiles, consultas de ejemplo y plantillas de reglas de análisis que se han incluido. Puede ejecutarlas en el acto o modificarlas y guardarlas.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha obtenido información sobre cómo integrar incidentes de Microsoft 365 Defender y datos de eventos de búsqueda avanzada de Microsoft Defender para punto de conexión y Microsoft Defender para Office 365 en Microsoft Sentinel mediante el conector de Microsoft 365 Defender. Para obtener más información sobre Microsoft Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Microsoft Sentinel](./detect-threats-built-in.md).
