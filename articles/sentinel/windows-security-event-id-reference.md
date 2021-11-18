---
title: Conjuntos de eventos de seguridad de Windows que pueden enviarse a Microsoft Sentinel
description: Obtenga información sobre los conjuntos precompilados de eventos de seguridad de Windows que puede recopilar y transmitir desde los sistemas de Windows al área de trabajo de Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: c20d94c34e83a6720888358b470a718e6c406d2b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710920"
---
# <a name="windows-security-event-sets-that-can-be-sent-to-microsoft-sentinel"></a>Conjuntos de eventos de seguridad de Windows que pueden enviarse a Microsoft Sentinel

Al ingerir eventos de seguridad desde dispositivos Windows mediante el [conector de datos de eventos de seguridad de Windows](data-connectors-reference.md#windows-security-events-via-ama) (incluida la [versión heredada](data-connectors-reference.md#security-events-via-legacy-agent-windows)), puede elegir qué eventos recopilar entre los siguientes conjuntos:

- **Todos los eventos**: todos los eventos de seguridad de Windows y AppLocker.

- **Común**: conjunto estándar de eventos con fines de auditoría. En este conjunto se proporciona una pista de auditoría de usuario completa. Por ejemplo, contiene los eventos de inicio de sesión y cierre de sesión de usuario (identificadores de evento 4624, 4634). También hay acciones de auditoría como los cambios en los grupos de seguridad, las operaciones de Kerberos en el controlador de dominio de clave y otros tipos de eventos que siguen los procedimientos recomendados aceptados.

    El conjunto de eventos **común** puede contener algunos tipos de eventos que no son tan comunes.  Esto se debe a que el argumento principal del conjunto **común** es reducir el volumen de eventos a un nivel más fácil de administrar, a la vez que se mantiene una capacidad de registro de auditoría completa.

- **Mínimo**: conjunto reducido de eventos que pueden señalar posibles amenazas. Este conjunto no contiene un registro de auditoría completo. Abarca solamente los eventos que podrían indicar una brecha correcta, así como otros eventos importantes que tienen tasas de aparición muy bajas. Por ejemplo, contiene un inicio de sesión de usuario correcto y uno erróneo (identificadores de evento 4624 y 4625), pero no contiene información del cierre de sesión (4634), que es importante para la auditoría pero no lo es para la detección de brechas y tiene un volumen relativamente alto. La mayor parte del volumen de datos de este conjunto está conformado por los eventos de inicio de sesión y de creación de proceso (identificador de evento 4688).

- **Personalizado**: conjunto de eventos determinados por el usuario y definidos en una regla de recopilación de datos mediante consultas XPath. [Más información sobre las reglas de recopilación de datos](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries).

## <a name="event-id-reference"></a>Referencia de Id. de evento

En la lista siguiente se muestra un desglose completo de los identificadores de evento de seguridad y de AppLocker para cada conjunto:

| Conjunto de eventos | Identificadores de eventos recopilados |
| --- | --- |
| **Mínimo** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| **Común** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |
|

## <a name="next-steps"></a>Pasos siguientes

En este documento ha aprendido a filtrar la colección de eventos de Windows en Microsoft Sentinel.

- Obtenga más información sobre [la recopilación de eventos de seguridad de Windows](connect-windows-security-events.md).
- Empiece a detectar amenazas con Microsoft Sentinel mediante reglas [integradas](detect-threats-built-in.md) o [personalizadas](detect-threats-custom.md).
