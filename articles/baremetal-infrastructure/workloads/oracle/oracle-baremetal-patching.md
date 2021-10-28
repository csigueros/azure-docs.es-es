---
title: Consideraciones de aplicación de revisiones de BareMetal para Oracle
description: Obtenga información acerca de las consideraciones de aplicación de revisiones de kernel o sistema operativo correspondientes a BareMetal Infrastructure para Oracle.
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 10/06/2021
ms.openlocfilehash: 145601a4725b257be283840eed1e31f68ed720d1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215922"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Consideraciones de aplicación de revisiones de BareMetal para Oracle

En este artículo, examinaremos consideraciones importantes de aplicación de revisiones de kernel o sistema operativo (SO) correspondientes a BareMetal Infrastructure para Oracle.

Para lograr un rendimiento de red y estabilidad del sistema adecuados, instale la versión específica del sistema operativo de los controladores eNIC y fNIC. Consulte la siguiente tabla de compatibilidad para obtener más detalles. 

Los servidores se entregan a los clientes con versiones compatibles. Sin embargo, durante la revisión del kernel o del sistema operativo, los controladores se pueden revertir a las versiones predeterminadas del controlador. Por lo tanto, asegúrese de confirmar que esté ejecutando la versión adecuada del controlador después de las revisiones del sistema operativo o kernel.

| Fabricante del sistema operativo | Versión del paquete del sistema operativo | Versión de firmware | Controlador eNIC | Controlador fNIC |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 4.0.0.8  | 2.0.0.60 |

## <a name="next-steps"></a>Pasos siguientes

Obtenga información acerca de la configuración de Ethernet de BareMetal para Oracle.

> [!div class="nextstepaction"]
> [Configuración de Ethernet de BareMetal para Oracle](oracle-baremetal-ethernet.md)

