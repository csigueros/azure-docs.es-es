---
title: Residencia de datos en Azure Network Watcher | Microsoft Docs
description: Este artículo le ayudará a comprender la residencia de datos para el servicio Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 638c466e0d8573ce0b23f31188c5e952a28f74b2
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112240463"
---
# <a name="data-residency-for-azure-network-watcher"></a>Residencia de datos en Azure Network Watcher
A excepción del servicio Connection Monitor, Azure Network Watcher no almacena datos de los clientes.


## <a name="connection-monitor-data-residency"></a>Residencia de datos de Connection Monitor
El servicio Connection Monitor almacena datos de los clientes. Network Watcher almacena automáticamente estos datos en una sola región. Por tanto, Connection Monitor satisface automáticamente los requisitos de residencia de datos en la región, incluidos los que se especifican en el [Centro de confianza](https://azuredatacentermap.azurewebsites.net/).

## <a name="data-residency"></a>Residencia de datos
En Azure, la característica que permite almacenar los datos de clientes en una única región solo está disponible actualmente en la región de Sudeste Asiático (Singapur) de la geoárea Asia Pacífico y en la región Sur de Brasil (estado de Sao Paulo) de la geoárea Brasil. En todas las demás regiones, los datos de clientes se almacenan en la geoárea. Para más información, consulte el [Centro de confianza](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Pasos siguientes

* Lea la información general de [Network Watcher](./network-watcher-monitoring-overview.md).
