---
title: Arquitectura de Azure Confidential Ledger
description: Arquitectura de Azure Confidential Ledger
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 855ad26422eb91c5c971d4525d419e5cee8b6606
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476421"
---
# <a name="architecture"></a>Arquitectura

Azure Confidential Ledger es un servicio de API REST que permite a los usuarios interactuar con el libro de contabilidad a través de llamadas API administrativas y funcionales.  Cuando los datos se registran en el libro de contabilidad, se envían a los nodos de cadena de bloques con permisos que son réplicas protegidas con copia de seguridad de enclave. Las réplicas siguen un concepto de consenso. Un usuario también puede recuperar recibos de los datos que se han confirmado en el libro de contabilidad.

También hay una noción de consorcio opcional que admitirá la colaboración entre varias entidades en el futuro.

## <a name="architecture-diagram"></a>Diagrama de la arquitectura

Esta imagen proporciona información general sobre la arquitectura de Azure Confidential Ledger y muestra a los usuarios de Azure Confidential Ledger que interactúan con las API en la nube para un libro de contabilidad creado.

:::image type="content" source="./media/architecture-overview.png" alt-text="Introducción a la arquitectura":::

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a Microsoft Azure Confidential Ledger](overview.md)
- [Autenticación de nodos de Azure Confidential Ledger](authenticate-ledger-nodes.md)
