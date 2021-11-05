---
title: Respuesta a las alertas de Microsoft Defender para Resource Manager
description: Obtenga información sobre los pasos necesarios para responder a las alertas de Microsoft Defender para Resource Manager.
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 913f1f07a588842d1cc2b4a845d5e9ff025b77fb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453124"
---
# <a name="respond-to-microsoft-defender-for-resource-manager-alerts"></a>Respuesta a las alertas de Microsoft Defender para Resource Manager

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cuando reciba una alerta de Microsoft Defender para Resource Manager, se recomienda investigar y responder a la alerta, tal y como se describe a continuación. Microsoft Defender para Resource Manager protege todos los recursos conectados, por lo que, incluso si está familiarizado con la aplicación o el usuario que desencadenó la alerta, es importante comprobar la situación relativa a cada alerta.  


## <a name="step-1-contact"></a>Paso 1. Contacto

1. Póngase en contacto con el propietario del recurso para determinar si se esperaba el comportamiento o era intencionado.
1. Si se espera la actividad, descarte la alerta.
1. Si no se espera la actividad, trate las cuentas de usuario, suscripciones y máquinas virtuales relacionadas como elementos en peligro y mitíguelo tal como se describe en el siguiente paso.

## <a name="step-2-immediate-mitigation"></a>Paso 2. Mitigación inmediata 

1. Corrija las cuentas de usuario que están en peligro:
    - Si no está familiarizado con ellas, elimínelas, ya que es posible que las haya creado un actor de amenaza
    - Si está familiarizado, cambie sus credenciales de autenticación
    - Use registros de actividad de Azure para revisar todas las actividades realizadas por el usuario e identifique las que sean sospechosas

1. Corrija las suscripciones que están en peligro:
    - Quite los runbooks con los que no esté familiarizado de la cuenta de Automation en peligro
    - Revise los permisos de IAM de la suscripción y quite los permisos para las cuentas de usuario que resulten familiares
    - Revise todos los recursos de Azure en la suscripción y elimine los que no estén familiarizados
    - Revise e investigue las alertas de seguridad de la suscripción en Microsoft Defender for Cloud.
    - Use registros de actividad de Azure para revisar todas las actividades realizadas en la suscripción e identifique las que sean sospechosas

1. Corrija las máquinas virtuales que están en peligro
    - Cambie las contraseñas de todos los usuarios
    - Ejecute un examen antimalware en la máquina
    - Restablezca la imagen inicial de las máquinas desde un origen sin malware


## <a name="next-steps"></a>Pasos siguientes

En esta página se explica el proceso de respuesta a una alerta de Microsoft Defender para Resource Manager. Para obtener información relacionada, consulte las páginas siguientes:

- [Introducción a Microsoft Defender para Resource Manager](defender-for-resource-manager-introduction.md)
- [Eliminación de alertas de seguridad](alerts-suppression-rules.md)
- [Exportación continua de datos de Defender for Cloud](continuous-export.md)