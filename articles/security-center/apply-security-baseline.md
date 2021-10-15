---
title: Protección del sistema operativo Windows y Linux con la línea de base de seguridad de Azure y Azure Security Center
description: Descubra cómo usa Azure Security Center la configuración de invitado para comparar la protección del sistema operativo con la guía de Azure Security Benchmark.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/04/2021
ms.author: memildin
ms.openlocfilehash: 9a15a87eb5f00316f88109e05e069032de795807
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129435968"
---
# <a name="apply-azure-security-baselines-to-machines"></a>Aplicación de líneas base de seguridad de Azure a máquinas

Para reducir la superficie de ataque de una máquina y evitar riesgos conocidos, es importante configurar el sistema operativo (SO) de la forma más segura posible.

Azure Security Benchmark tiene instrucciones para la protección del sistema operativo, lo que ha llevado a documentos de línea de base de seguridad para [Windows](../governance/policy/samples/guest-configuration-baseline-windows.md)y [Linux](../governance/policy/samples/guest-configuration-baseline-linux.md).

Use las recomendaciones de seguridad descritas en este artículo para evaluar las máquinas de su entorno y hacer lo siguiente:

- Identificar brechas en las configuraciones de seguridad.
- Obtener información sobre cómo corregir esas brechas.

## <a name="availability"></a>Disponibilidad
|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Versión preliminar.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|Precios:|Gratuito|
|Requisitos previos:|Las máquinas deben (1) ser miembros de un grupo de trabajo, (2) tener la extensión de configuración de invitado, (3) tener una identidad administrada asignada por el sistema y (4) ejecutar un sistema operativo compatible:<br>• Windows Server 2012, 2012r2, 2016 o 2019<br>• Ubuntu 14.04, 16.04, 17.04, 18.04 o 20.04<br>• Debian 7, 8, 9, o 10<br>• CentOS 7 u 8<br>• Red Hat Enterprise Linux (RHEL) 7 u 8<br>• Oracle Linux 7 u 8<br>• SUSE Linux Enterprise Server 12|
|Roles y permisos necesarios:|Para instalar la extensión de configuración de invitado y sus requisitos previos, se requiere el permiso de **escritura** en las máquinas correspondientes.<br>Para **ver** las recomendaciones y explorar los datos de línea base del sistema operativo, se requiere permiso de **lectura** en el nivel de suscripción.|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/no-icon.png"::: Nacionales o soberanas (Azure Government y Azure China 21Vianet)|
|||

## <a name="what-are-the-hardening-recommendations"></a>¿Cuáles son las recomendaciones de seguridad?

Azure Security Center incluye dos recomendaciones que comprueban si la configuración de las máquinas Windows y Linux en su entorno cumple las configuraciones de línea de base de seguridad de Azure:

- En el caso de las máquinas **Windows**, [las vulnerabilidades en la configuración de seguridad de las máquinas Windows deben corregirse (con tecnología de la Configuración de invitado)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6) y se comparan con la configuración de la [línea de base de seguridad de Windows](../governance/policy/samples/guest-configuration-baseline-windows.md).
- En el caso de las máquinas **Linux**, [las vulnerabilidades en la configuración de seguridad de las máquinas Linux deben corregirse (con tecnología de la Configuración de invitado)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda) y se comparan con la configuración de la [línea de base de seguridad de Linux](../governance/policy/samples/guest-configuration-baseline-linux.md).

Estas recomendaciones usan la característica Configuración de invitado de Azure Policy para comparar la configuración del sistema operativo de una máquina con la línea de base definida en [Azure Security Benchmark](/security/benchmark/azure/overview).

## <a name="compare-machines-in-your-subscriptions-with-the-os-security-baselines"></a>Comparación de las máquinas de las suscripciones con las líneas base de seguridad del sistema operativo

Para comparar las máquinas con las líneas base de seguridad del sistema operativo:
 
1. En las páginas del portal de Security Center, abra la página **Recomendaciones**. 
1. Seleccione la recomendación pertinente:
    - En las máquinas **Windows**, las [vulnerabilidades de la configuración de seguridad de las máquinas Windows deben corregirse (con tecnología de Configuración de invitado)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6).
    - En las máquinas **Linux**, las [vulnerabilidades de la configuración de seguridad de las máquinas Linux deben corregirse (con tecnología de Configuración de invitado)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda).

    :::image type="content" source="media/apply-security-baseline/recommendations-baseline.png" alt-text="Estas son las dos recomendaciones para comparar la configuración del sistema operativo de las máquinas con la línea de base de seguridad de Azure pertinente." lightbox="media/apply-security-baseline/recommendations-baseline.png":::

1. En la página de detalles de la recomendación puede ver:
    1. Recursos afectados.
    1. Comprobaciones de seguridad específicas con error.

    :::image type="content" source="media/apply-security-baseline/recommendation-details-page-vulnerabilities-windows.png" alt-text="Página de detalles de recomendaciones para la recomendación de Windows sobre vulnerabilidades en la configuración de línea de base de las máquinas Windows." lightbox="media/apply-security-baseline/recommendation-details-page-vulnerabilities-windows.png":::

1. Para obtener más información sobre un resultado específico, selecciónelo.

    :::image type="content" source="media/apply-security-baseline/finding-details.png" alt-text=" Obtener más información sobre un resultado específico a partir de la comparación de la configuración de invitado correspondiente a una configuración de sistema operativo con la línea de base de seguridad definida." lightbox="media/apply-security-baseline/finding-details.png":::

1. Otras posibilidades de investigación:

    - Para ver la lista de máquinas que se han evaluado, abra **Recursos afectados**.
    - Para ver la lista de resultados de una máquina, selecciónela en la pestaña **Recursos incorrectos**. Se abrirá una página en la que solo se mostrarán los resultados de esa máquina.


## <a name="faq---hardening-an-os-according-to-the-security-baseline"></a>Preguntas más frecuentes: Protección de un sistema operativo según la línea de base de seguridad

- [¿Cómo puedo implementar los requisitos previos para las recomendaciones de configuración de seguridad?](#how-do-i-deploy-the-prerequisites-for-the-security-configuration-recommendations)
- [¿Por qué se muestra una máquina como no aplicable?](#why-is-a-machine-shown-as-not-applicable)

### <a name="how-do-i-deploy-the-prerequisites-for-the-security-configuration-recommendations"></a>¿Cómo puedo implementar los requisitos previos para las recomendaciones de configuración de seguridad?

Para implementar la extensión de la configuración de invitado con sus requisitos previos:

- Para las máquinas seleccionadas, siga la recomendación de seguridad **Es necesario instalar la extensión de configuración de invitado en las máquinas** que está en el control de seguridad **Implementar los procedimientos recomendados de seguridad**.

- A escala, asigne la iniciativa de la directiva **Implementar los requisitos previos para habilitar las directivas de configuración de invitado en las máquinas virtuales**.


### <a name="why-is-a-machine-shown-as-not-applicable"></a>¿Por qué se muestra una máquina como no aplicable?

La lista de recursos en la pestaña **No aplicable** incluye una columna **Motivo**. Entre algunas de las razones habituales se incluyen:

| Motivo                                                            | Detalles                                                                                                                                                                        |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **No hay datos de examen disponibles en la máquina**                         | No hay ningún resultado de cumplimiento para esta máquina en Azure Resource Graph. Todos los resultados de cumplimiento se escriben en Azure Resource Graph mediante la extensión de configuración de invitado. Puede comprobar los datos en Azure Resource Graph mediante las consultas de muestra en [Configuración de invitado de Azure Policy: consultas ARG de ejemplo](../governance/policy/samples/resource-graph-samples.md?tabs=azure-cli#azure-policy-guest-configuration).|
| **La extensión de configuración de invitado no está instalada en la máquina** | A la máquina le falta la extensión de configuración de invitado, ya que es un requisito previo para evaluar el cumplimiento de la línea de base de seguridad de Azure.                               |
| **La identidad administrada del sistema no está configurada en la máquina**      | Se debe implementar una identidad administrada asignada por el sistema en la máquina.                                                                                                           |
| **La recomendación está deshabilitada en la directiva**                      | La definición de directiva que evalúa la línea de base del sistema operativo está deshabilitada en el ámbito que incluye la máquina correspondiente.                                                               |
|                                                                   |                                                                                                                                                                                |

## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a usar las recomendaciones de configuración de invitado de Security Center para comparar la protección del sistema operativo con la línea de base de seguridad de Azure.

Para obtener más información sobre estos cambios de configuración, consulte:

- [Línea de base de seguridad de Windows](../governance/policy/samples/guest-configuration-baseline-windows.md)
- [Línea base de seguridad de Linux](../governance/policy/samples/guest-configuration-baseline-linux.md)
- [Azure Security Benchmark](/security/benchmark/azure/overview)