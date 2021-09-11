---
title: Conexión de Azure Sentinel a fuentes de inteligencia sobre amenazas STIX/TAXII | Microsoft Docs
description: Obtenga información sobre cómo conectar Azure Sentinel a fuentes de inteligencia sobre amenazas estándar del sector, con el fin de importar indicadores de amenazas.
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
ms.date: 07/12/2021
ms.author: yelevin
ms.openlocfilehash: ce1d2fafd330848c19dc5386fad11d87bfe2670a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732531"
---
# <a name="connect-azure-sentinel-to-stixtaxii-threat-intelligence-feeds"></a>Conexión de Azure Sentinel a fuentes de inteligencia sobre amenazas STIX/TAXII

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**Consulte también** [Conexión de su plataforma de inteligencia sobre amenazas (TIP) a Azure Sentinel](connect-threat-intelligence-tip.md)

El estándar del sector más ampliamente adoptado para la transmisión de la inteligencia sobre amenazas es una [combinación del formato de datos STIX y el protocolo TAXII](https://oasis-open.github.io/cti-documentation/). Si su organización recibe indicadores de amenazas de soluciones que admiten la versión actual de STIX/TAXII (2.0 o 2.1), puede usar el **conector de datos Inteligencia sobre amenazas: TAXII** para incorporar sus indicadores de amenazas a Azure Sentinel. Este conector habilita un cliente de TAXII integrado en Azure Sentinel para importar inteligencia sobre amenazas desde servidores TAXII 2.x.

:::image type="content" source="media/connect-threat-intelligence-taxii/threat-intel-taxii-import-path.png" alt-text="Ruta de acceso de importación de TAXII":::

Obtenga más información acerca de la [inteligencia sobre amenazas](understand-threat-intelligence.md) en Azure Sentinel y. específicamente, acerca de las [fuentes de inteligencia sobre amenazas](threat-intelligence-integration.md#taxii-threat-intelligence-feeds) que se pueden integrar con Azure Sentinel.

## <a name="prerequisites"></a>Requisitos previos  

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel para almacenar los indicadores de amenazas.
- Debe tener un **URI raíz de API** y un **Id. de colección** de TAXII 2.0 o TAXII 2.1.

## <a name="instructions"></a>Instructions

Siga estos pasos para importar indicadores de amenazas con formato STIX a Azure Sentinel desde un servidor TAXII:

1. Obtenga el Id. de colección y la raíz de API del servidor TAXII.

1. Habilite el conector de datos Inteligencia sobre amenazas: TAXII en Azure Sentinel

### <a name="get-the-taxii-server-api-root-and-collection-id"></a>Obtenga el Id. de colección y la raíz de API del servidor TAXII.

Los servidores TAXII 2.x anuncian raíces de API, que son direcciones URL que hospedan colecciones de inteligencia sobre amenazas. Normalmente puede encontrar la raíz de API y el Id. de colección en las páginas de documentación del proveedor de inteligencia sobre amenazas que hospeda el servidor TAXII. 

> [!NOTE]
> En algunos casos, el proveedor solo publicitará una dirección URL, denominada punto de conexión de detección. Puede usar la utilidad cURL para examinar el punto de conexión de detección y solicitar la raíz de API, como [se detalla a continuación](#find-the-api-root).

### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Habilite el conector de datos Inteligencia sobre amenazas: TAXII en Azure Sentinel

Para importar indicadores de amenazas en Azure Sentinel desde un servidor TAXII, siga estos pasos:

1. Desde [Azure Portal](https://portal.azure.com/), vaya al servicio **Azure Sentinel**.

1. Elija el **área de trabajo** al que desea importar indicadores de amenazas desde el servidor TAXII.

1. Seleccione **Conectores de datos** en el menú, seleccione **Inteligencia sobre amenazas: TAXII** en la galería de conectores y haga clic en el botón **Open connector page** (Abrir página del conector).

1. Especifique un **nombre descriptivo** para esta colección del servidor TAXII, la **dirección URL raíz de API**, el **Id. de colección**, un **nombre de usuario** (si es necesario) y una **contraseña** (si es necesario), y elija el grupo de indicadores y la frecuencia de sondeo que desee. Haga clic en el botón **Agregar**.

    :::image type="content" source="media/connect-threat-intelligence-taxii/threat-intel-configure-taxii-servers.png" alt-text="Configuración de servidores TAXII":::
 
Debe recibir una confirmación de que se ha establecido correctamente una conexión al servidor TAXII. Puede repetir el último paso anterior tantas veces como desee para conectarse a varias colecciones desde uno o más servidores TAXII.

En cuestión de minutos, los indicadores de amenazas deberían empezar a fluir en esta área de trabajo de Azure Sentinel. Puede encontrar los nuevos indicadores en la hoja **Inteligencia sobre amenazas**, a la que se puede acceder desde el menú de navegación de Azure Sentinel.

### <a name="find-the-api-root"></a>Búsqueda de la raíz de API

A continuación figura un ejemplo de cómo usar la utilidad de la línea de comandos [cURL](https://en.wikipedia.org/wiki/CURL), que se proporciona en Windows y en la mayoría de las distribuciones de Linux, para detectar la raíz de API y examinar las colecciones de un servidor TAXII, proporcionado solo el punto de conexión de detección. Mediante el punto de conexión de detección del servidor [Anomali Limo](https://www.anomali.com/community/limo) ThreatStream TAXII 2.0, puede solicitar el URI raíz de API y, a continuación, las colecciones.

1.  En un explorador, vaya al punto de conexión de detección del servidor ThreatStream TAXII 2.0 en https://limo.anomali.com/taxii para recuperar la raíz de API. Autentíquese con el nombre de usuario y la contraseña `guest`.

    Recibirá la siguiente respuesta:

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  Use la utilidad cURL y la raíz de API (https://limo.anomali.com/api/v1/taxii2/feeds/) de la respuesta anterior, anexando "`collections/`" a la raíz de AP, para examinar la lista de Ids. de colección hospedados en la raíz:

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    ```
    Después de volver a autenticarse con la contraseña "guest", recibirá la siguiente respuesta:

    ```json
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

Ahora tiene toda la información que necesita para conectar Azure Sentinel a una o varias colecciones del servidor TAXII proporcionadas por Anomali Limo.

| **Raíz de API** (https://limo.anomali.com/api/v1/taxii2/feeds/) | Id. de colección |
| ------------------------------------------------------------ | ------------: |
| **Phish Tank**                                               | 107           |
| **Abuse.ch Ransomware IPs**                                  | 135           |
| **Abuse.ch Ransomware Domains**                              | 136           |
| **DShield Scanning IPs**                                     | 150           |
| **Malware Domain List - Hotlist**                            | 200           |
| **Blutmagie TOR Nodes**                                      | 209           |
| **Emerging Threats C&C Server**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **CyberCrime**                                               |  41           |
| **Emerging Threats - Compromised**                           |  68           |
|

## <a name="next-steps"></a>Pasos siguientes

En este documento ha obtenido información sobre cómo conectar Azure Sentinel a fuentes de inteligencia sobre amenazas mediante el protocolo TAXII. Para obtener más información sobre Azure Sentinel, consulte los siguientes artículos.

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](./detect-threats-built-in.md).
