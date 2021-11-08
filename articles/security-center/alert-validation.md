---
title: Validación de alertas en Microsoft Defender for Cloud | Microsoft Docs
description: Obtenga información sobre cómo validar que las alertas de seguridad estén configuradas correctamente en Microsoft Defender for Cloud.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: b5acd38ec3885fab3d1de9d70a80fa76cd9c60b0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453295"
---
# <a name="alert-validation-in-microsoft-defender-for-cloud"></a>Validación de alertas en Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Este documento le ayuda a comprobar si el sistema está configurado correctamente para las alertas de Microsoft Defender for Cloud.

## <a name="what-are-security-alerts"></a>¿Qué son las alertas de seguridad?
Las alertas son notificaciones que Defender for Cloud genera cuando detecta amenazas en los recursos. Asigna prioridades y enumera las alertas, junto con la información necesaria para que pueda investigar rápidamente el problema. Defender for Cloud también proporciona recomendaciones sobre el modo en que puede corregir un ataque.
Para obtener más información, consulte [Alertas de seguridad en Defender for Cloud](alerts-overview.md) y [Administración y respuesta a las alertas de seguridad](managing-and-responding-alerts.md).


## <a name="generate-sample-security-alerts"></a>Generación de alertas de seguridad de ejemplo

Si usa la nueva experiencia de alertas de versión preliminar, tal como se describe en [Administración y respuesta a las alertas de seguridad en Microsoft Defender for Cloud](managing-and-responding-alerts.md), puede crear alertas de ejemplo con unos cuantos clics en la página de alertas de seguridad de Azure Portal.

Use alertas de ejemplo para:

- evaluar el valor y las funcionalidades de los planes de Microsoft Defender
- validar las configuraciones que haya realizado para las alertas de seguridad (como integraciones de SIEM, automatización de flujos de trabajo y notificaciones de correo electrónico).

Para crear alertas de ejemplo:

1. Como usuario con el rol **Colaborador de la suscripción**, en la barra de herramientas de la página de alertas, seleccione **Crear alertas de ejemplo**.
1. Seleccione la suscripción.
1. Seleccione el plan de Microsoft Defender correspondiente para el que quiere ver las alertas. 
1. Seleccione **Create sample alerts** (Crear alertas de ejemplo).

    :::image type="content" source="media/alert-validation/create-sample-alerts-procedures.png" alt-text="Pasos para crear alertas de ejemplo en Microsoft Defender for Cloud.":::
    
    Aparecerá una notificación en la que se le informa de que se están creando las alertas de ejemplo:

    :::image type="content" source="media/alert-validation/notification-sample-alerts-creation.png" alt-text="Notificación que indica que se están generando las alertas de ejemplo.":::

    Después de unos minutos, las alertas aparecerán en la página alertas de seguridad. También aparecerán en cualquier otro lugar que haya configurado para recibir las alertas de seguridad de Microsoft Defender for Cloud (SIEM conectados, notificaciones de correo electrónico, etc.).

    :::image type="content" source="media/alert-validation/sample-alerts.png" alt-text="Alertas de ejemplo en la lista de alertas de seguridad.":::

    > [!TIP]
    > Las alertas son para los recursos simulados.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Simulación de alertas en las VM de Azure (Windows) <a name="validate-windows"></a>

Una vez que el agente de Log Analytics esté instalado en el equipo, siga estos pasos desde el equipo donde quiera que esté el recurso atacado de la alerta:

1. Copie un archivo ejecutable (por ejemplo **calc.exe**) en el escritorio del equipo o en otro directorio que prefiera y cambie el nombre a **ASC_AlertTest_662jfi039N.exe**.
1. Abra el símbolo del sistema y ejecute este archivo con un argumento (un nombre de argumento falso), por ejemplo, ```ASC_AlertTest_662jfi039N.exe -foo```
1. Espere de 5 a 10 minutos y abra Alertas de Defender for Cloud. Debería aparecer una alerta.

> [!NOTE]
> Al revisar esta alerta de prueba para Windows, asegúrese de que el campo **Arguments Auditing Enabled** (Auditoría de argumentos habilitada) aparece como **true**. Si es **false**, debe habilitar la auditoría de argumentos de línea de comandos. Para habilitarlo, utilice la línea de comandos siguiente:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Simulación de alertas en las VM de Azure (Linux) <a name="validate-linux"></a>

Una vez que el agente de Log Analytics esté instalado en el equipo, siga estos pasos desde el equipo donde quiera que esté el recurso atacado de la alerta:

1. Copie un archivo ejecutable en una ubicación conveniente y cámbiele el nombre a `./asc_alerttest_662jfi039n`. Por ejemplo:

    `cp /bin/echo ./asc_alerttest_662jfi039n`

1. Abra el símbolo del sistema y ejecute este archivo:

    `./asc_alerttest_662jfi039n testing eicar pipe`

1. Espere de 5 a 10 minutos y luego abra Alertas de Defender for Cloud. Debería aparecer una alerta.

## <a name="simulate-alerts-on-kubernetes"></a>Simulación de alertas en Kubernetes <a name="validate-kubernetes"></a>

Si ha integrado Azure Kubernetes Service con Defender for Cloud, puede probar que sus alertas funcionan con el siguiente comando kubectl:

`kubectl get pods --namespace=asc-alerttest-662jfi039n`

Para obtener más información sobre cómo defender los nodos y clústeres de Kubernetes, consulte [Introducción a Microsoft Defender para Kubernetes](defender-for-kubernetes-introduction.md).

## <a name="next-steps"></a>Pasos siguientes
En este artículo se explicó el proceso de validación de las alertas. Ahora que conoce esta validación, intente los siguientes pasos:

* [Validación de la detección de amenazas de Azure Key Vault en Microsoft Defender for Cloud](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Administración y respuesta a las alertas de seguridad en Microsoft Defender for Cloud](managing-and-responding-alerts.md): aprenda a administrar alertas y responder a incidentes de seguridad en Defender for Cloud.
* [Comprender las alertas de seguridad en Microsoft Defender for Cloud](./alerts-overview.md): obtenga información sobre los distintos tipos de alertas de seguridad.
