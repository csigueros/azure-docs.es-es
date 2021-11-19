---
title: Comprensión de las características de seguridad mejoradas de Microsoft Defender for Cloud
description: Obtenga información sobre las ventajas de habilitar la seguridad mejorada en Microsoft Defender for Cloud.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: overview
ms.date: 11/14/2021
ms.openlocfilehash: 4f17d5228bdac13797aaef0fe4441f61d474042a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557175"
---
# <a name="microsoft-defender-for-clouds-enhanced-security-features"></a>Características de seguridad mejoradas de Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Las características de seguridad mejoradas son gratuitas durante los primeros 30 días. Después de 30 días, si decide continuar usando el servicio, empezaremos a cobrar automáticamente el uso.

Puede actualizar desde la página **Configuración del entorno**, tal como se describe en [Inicio rápido: Habilitación de características de seguridad mejoradas](enable-enhanced-security.md). Para obtener información sobre los precios en la moneda de su elección y según su región, consulte la [página de precios](https://azure.microsoft.com/pricing/details/security-center/).

:::image type="content" source="media/enhanced-security-features-overview/defender-plans-top.png" alt-text="Habilite las características de seguridad mejoradas de Microsoft Defender for Cloud.":::

## <a name="what-are-the-benefits-of-enabling-enhanced-security-features"></a>¿Cuáles son las ventajas de habilitar las características de seguridad mejoradas?

Defender for Cloud se ofrece en dos modos:

- **Sin características de seguridad mejoradas** (gratis): Defender for Cloud se habilita de forma gratuita en todas sus suscripciones de Azure cuando visita por primera vez el panel de protección de cargas de trabajo de Azure Portal o si lo habilita mediante programación utilizando las API. Mediante modo gratuito tendrá la puntuación segura y sus características relacionadas: la directiva de seguridad, la evaluación de seguridad continua y las recomendaciones de seguridad prácticas para que pueda proteger los recursos de Azure.

- **Defender for Cloud con todas las características de seguridad mejoradas**: habilitar la seguridad mejorada extiende las capacidades del modo gratuito a las cargas de trabajo que se ejecutan en nubes privadas y públicas, proporcionando así una administración de seguridad unificada y protección contra amenazas en todas las cargas de trabajo de la nube híbrida. Algunas de las principales ventajas incluyen:

    - **Microsoft Defender para punto de conexión**: Microsoft Defender para servidores incluye [Microsoft Defender para punto de conexión](https://www.microsoft.com/microsoft-365/security/endpoint-defender) para obtener una completa detección y respuesta de puntos de conexión (EDR). Obtenga más información sobre las ventajas de usar Microsoft Defender para punto de conexión junto con Defender for Cloud en [Uso de la solución EDR integrada de Defender for Cloud](integration-defender-for-endpoint.md).
    - **Evaluación de vulnerabilidades para máquinas virtuales, registros de contenedor y recursos de SQL** : habilite fácilmente soluciones de evaluación de vulnerabilidades para detectar, administrar y resolver puntos vulnerables. Vea, investigue y corrija lo que detecte directamente en Defender for Cloud.
    - **Seguridad en varias nubes**: conecte las cuentas de Amazon Web Services (AWS) y Google Cloud Platform (GCP) para proteger los recursos y cargas de trabajo de esas plataformas con una variedad de características de seguridad de Microsoft Defender for Cloud.
    - **Seguridad híbrida**: Obtenga una vista unificada de la seguridad de todas sus cargas de trabajo locales y en la nube. Aplique directivas de seguridad y evalúe constantemente la seguridad de las cargas de trabajo de nube híbrida para garantizar el cumplimiento normativo con los estándares de seguridad. Recopile, busque y analice datos de seguridad de varios orígenes, incluidos firewalls y otras soluciones de partners.
    - **Alertas de protección contra amenazas**: el análisis de comportamiento avanzado y Microsoft Intelligent Security Graph proporcionan una ventaja frente a los ataques cibernéticos en evolución. La funcionalidad integrada de análisis del comportamiento y aprendizaje automático puede identificar ataques y vulnerabilidades de seguridad de día cero. Supervise las redes, las máquinas, los almacenes de datos (servidores SQL hospedados dentro y fuera de Azure, bases de datos de Azure SQL, Azure SQL Managed Instance y Azure Storage) y los servicios en la nube para impedir que se produzcan ataques y administrar las actividades que deben emprenderse tras una vulneración. Optimice la investigación con herramientas interactivas e inteligencia de amenazas contextual.
    - **Seguimiento del cumplimiento con una gama de estándares**: Defender for Cloud evalúa continuamente el entorno de nube híbrida para analizar los factores de riesgo con arreglo a los controles y los procedimientos recomendados que se describen en [Punto de referencia de seguridad de Azure](/security/benchmark/azure/introduction). Al habilitar las características de seguridad mejoradas, puede aplicar una variedad de otros estándares del sector, estándares normativos y puntos de referencia según las necesidades de la organización. Agregue estándares y realice un seguimiento del cumplimiento con ellos desde el [panel de cumplimiento normativo](update-regulatory-compliance-packages.md).
    - **Controles de acceso y de aplicación**: bloquee el malware y otras aplicaciones no deseadas aplicando recomendaciones generadas mediante aprendizaje automático y adaptadas a sus cargas de trabajo específicas para crear listas de permitidos y bloqueados. Reduzca la superficie de la red que está expuesta a ataques mediante un acceso Just-In-Time controlado a los puertos de administración de las VM de Azure. Los controles de acceso y aplicación reducen drásticamente la exposición a ataques por fuerza bruta y a otros ataques de la red.
    - **Características de seguridad de contenedor**: Aproveche la administración de vulnerabilidades y la protección contra amenazas en tiempo real en los entornos en contenedores. Los cargos se basan en el número de imágenes de contenedor únicas insertadas en el registro conectado. Una vez que se haya analizado una imagen, no se le cobrará de nuevo a menos que se modifique e inserte una vez más.
    - **Amplia protección contra amenazas para los recursos conectados al entorno de Azure**: medidas de protección contra amenazas nativas de la nube que están incluidas en los servicios de Azure comunes a todos los recursos: Azure Resource Manager, Azure DNS, nivel de red de Azure y Azure Key Vault. Defender for Cloud tiene una visibilidad exclusiva de la capa de administración de Azure y la capa de Azure DNS y, por tanto, puede proteger los recursos en la nube que están conectados a esas capas.


## <a name="faq---pricing-and-billing"></a>Preguntas frecuentes sobre precios y facturación 

- [¿Cómo puedo realizar un seguimiento de quién en mi organización habilitó un plan de Microsoft Defender en Defender for Cloud?](#how-can-i-track-who-in-my-organization-enabled-a-microsoft-defender-plan-in-defender-for-cloud)
- [¿Cuáles son los planes que ofrece Defender for Cloud?](#what-are-the-plans-offered-by-defender-for-cloud)
- [¿Cómo puedo habilitar la seguridad mejorada de Defender for Cloud en la suscripción?](#how-do-i-enable-defender-for-clouds-enhanced-security-for-my-subscription)
- [¿Puedo habilitar Microsoft Defender para servidores en un subconjunto de servidores de mi suscripción?](#can-i-enable-microsoft-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Si ya tengo una licencia de Microsoft Defender para punto de conexión, ¿puedo obtener un descuento para Defender para servidores?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-defender-for-servers)
- [Mi suscripción tiene Microsoft Defender para servidores habilitado, ¿debo pagar por los servidores que no estén en ejecución?](#my-subscription-has-microsoft-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [¿Se me cobrará por máquinas sin el agente de Log Analytics instalado?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Si un agente de Log Analytics envía notificaciones a diversas áreas de trabajo, ¿se me cobrará varias veces?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Si un agente de Log Analytics envía notificaciones a varias áreas de trabajo, ¿la ingesta de datos gratuita de 500 MB estará disponible en todas ellas?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [¿Se calcula la ingesta de 500 MB de datos gratis para todo el área de trabajo o estrictamente por máquina?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [¿Qué tipos de datos se incluyen en la asignación diaria de datos de 500 MB?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-a-microsoft-defender-plan-in-defender-for-cloud"></a>¿Cómo puedo realizar un seguimiento de quién en mi organización habilitó un plan de Microsoft Defender en Defender for Cloud?
Las suscripciones de Azure pueden tener varios administradores con permisos para cambiar la configuración de precios. Para averiguar qué usuario realizó un cambio, use el registro de actividad de Azure.

:::image type="content" source="media/enhanced-security-features-overview/logged-change-to-pricing.png" alt-text="Registro de actividad de Azure en el que se muestra un evento de cambio de precio.":::

Si la información del usuario no aparece en la columna **Evento iniciado por**, explore el JSON del evento para ver los detalles pertinentes.

:::image type="content" source="media/enhanced-security-features-overview/tracking-pricing-changes-in-activity-log.png" alt-text="Explorador JSON del registro de actividad de Azure.":::


### <a name="what-are-the-plans-offered-by-defender-for-cloud"></a>¿Cuáles son los planes que ofrece Defender for Cloud? 
La oferta gratuita de Microsoft Defender for Cloud ofrece la puntuación segura y las herramientas relacionadas. La habilitación de la seguridad mejorada activa todos los planes de Microsoft Defender para proporcionar una variedad de ventajas de seguridad para todos los recursos en entornos de Azure, ya sean híbridos o de varias nubes.  

### <a name="how-do-i-enable-defender-for-clouds-enhanced-security-for-my-subscription"></a>¿Cómo puedo habilitar la seguridad mejorada de Defender for Cloud en la suscripción? 
Puede usar cualquiera de los siguientes métodos para habilitar la seguridad mejorada en la suscripción: 

| Método                                          | Instructions                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Páginas de Defender for Cloud de Azure Portal    | [Habilitación de protecciones mejoradas](enable-enhanced-security.md)                                                                                         |
| API DE REST                                        | [API de precios](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [Precios de conjunto](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-microsoft-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>¿Puedo habilitar Microsoft Defender para servidores en un subconjunto de servidores de mi suscripción?
No. Al habilitar [Microsoft Defender para servidores](defender-for-servers-introduction.md) en una suscripción, Defender para servidores protegerá todas las máquinas de la suscripción.

Una alternativa es habilitar Microsoft Defender para servidores en el nivel de área de trabajo de Log Analytics. Si lo hace, solo se protegerán y facturarán los servidores que envían notificaciones a esa área de trabajo. Sin embargo, varias funcionalidades no estarán disponibles, como el acceso a máquina virtual Just-in-Time, las detecciones de red, el cumplimiento normativo, la protección de red adaptable y los controles de aplicaciones adaptables, entre otras. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-defender-for-servers"></a>Si ya tengo una licencia de Microsoft Defender para punto de conexión, ¿puedo obtener un descuento para Defender para servidores?
Si ya tiene una licencia de Microsoft Defender para punto de conexión, no tendrá que pagar esa parte de la licencia de Defender para servidores.

Para solicitar el descuento, póngase en contacto con el equipo de soporte técnico de Defender for Cloud y proporcione la información pertinente del id. de área de trabajo, región y número de licencias de Microsoft Defender para punto de conexión aplicadas a las máquinas del área de trabajo dada.

El descuento será efectivo a partir de la fecha de aprobación y no tendrá lugar con carácter retroactivo.

### <a name="my-subscription-has-microsoft-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Mi suscripción tiene Microsoft Defender para servidores habilitado, ¿debo pagar por los servidores que no estén en ejecución? 
No. Si habilita [Microsoft Defender para servidores](defender-for-servers-introduction.md) en una suscripción, no se le cobrará por las máquinas que estén en el estado de energía desasignado mientras estén en ese estado. Las máquinas se facturan según su estado de energía, como se muestra en la tabla siguiente:

| State        | Descripción                                                                                                                                      | Facturación del uso de la instancia |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Iniciando     | La máquina virtual se está iniciando.                                                                                                                               | No facturado            |
| En ejecución      | Estado de funcionamiento normal para una máquina virtual                                                                                                                    | Facturado                |
| Deteniéndose     | Se trata de un estado transitorio. Cuando haya terminado, aparecerá como Detenido.                                                                           | Facturado                |
| Detenido      | Se ha apagado la máquina virtual desde dentro del sistema operativo invitado o está utilizando las API de PowerOff. El hardware aún está asignado a la máquina virtual y permanece en el host. | Facturado                |
| Desasignando | Estado transitorio. Cuando haya terminado, la máquina virtual aparecerá como Desasignado.                                                                             | No facturado            |
| Desasignado  | La máquina virtual se ha detenido correctamente y se ha eliminado del host.                                                                                  | No facturado            |

:::image type="content" source="media/enhanced-security-features-overview/deallocated-virtual-machines.png" alt-text="Azure Virtual Machines muestra una máquina desasignada.":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>¿Se me cobrará por máquinas sin el agente de Log Analytics instalado?
Sí. Al habilitar [Microsoft Defender para servidores](defender-for-servers-introduction.md) en una suscripción, las máquinas de esa suscripción obtienen una serie de protecciones incluso si no ha instalado el agente de Log Analytics. Esto es aplicable a máquinas virtuales de Azure, instancias de conjuntos de escalado de máquinas virtuales de Azure y servidores habilitados para Azure Arc.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Si un agente de Log Analytics envía notificaciones a diversas áreas de trabajo, ¿se me cobrará varias veces? 
Sí. Si ha configurado el agente de Log Analytics para enviar datos a dos o más áreas de trabajo de Log Analytics diferentes (hospedaje múltiple), se le cobrará por cada área de trabajo que tenga instaladas soluciones de seguridad o antimalware. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Si un agente de Log Analytics envía notificaciones a varias áreas de trabajo, ¿la ingesta de datos gratuita de 500 MB estará disponible en todas ellas?
Sí. Si ha configurado el agente de Log Analytics para enviar datos a dos o más áreas de trabajo de Log Analytics diferentes (hospedaje múltiple), obtendrá una ingesta de datos gratuita de 500 MB. Se calcula por nodo, por área de trabajo notificada y por día, y está disponible para cada área de trabajo que tenga instaladas soluciones de seguridad o antimalware. Se le cobrarán los datos ingeridos por encima del límite de 500 MB.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>¿Se calcula la ingesta de 500 MB de datos gratis para todo el área de trabajo o estrictamente por máquina?
Con cada máquina Windows conectada al área de trabajo, podrá ingerir 500 MB de datos gratis al día. Específicamente para tipos de datos de seguridad recopilados directamente por Defender for Cloud. 

Estos datos son una tasa diaria promediada en todos los nodos. Por tanto, aunque algunas máquinas envíen 100 MB y otras 800 MB, si el total no supera el límite gratuito de **[número de máquinas] x 500 MB**, no se le cobrará ningún cargo adicional.

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>¿Qué tipos de datos se incluyen en la asignación diaria de datos de 500 MB?
La facturación de Defender for Cloud está estrechamente vinculada a la facturación de Log Analytics. [Microsoft Defender para servidores](defender-for-servers-introduction.md) asigna 500 MB por nodo y día en el siguiente subconjunto de [tipos de datos de seguridad](/azure/azure-monitor/reference/tables/tables-category#security):
- SecurityAlert
- SecurityBaseline
- SecurityBaselineSummary
- SecurityDetection
- SecurityEvent
- WindowsFirewall
- MaliciousIPCommunication
- SysmonEvent
- ProtectionStatus
- Los tipos de datos Update y UpdateSummary cuando la solución Update Management no se está ejecutando en el área de trabajo o el destino de la solución está habilitado.

Si el área de trabajo está en el plan de tarifa heredado por nodo, las asignaciones de Defender for Cloud y Log Analytics se combinan y se aplican conjuntamente a todos los datos ingeridos facturables.

## <a name="next-steps"></a>Pasos siguientes
En este artículo se explican las opciones de precios de Defender for Cloud. Para obtener material relacionado, consulte:

- [Optimización de los costos de la carga de trabajo de Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Detalles de precios en la moneda de su elección y según su región](https://azure.microsoft.com/pricing/details/security-center/)
- Quizá quiera administrar los costos y limitar la cantidad de datos recopilados para una solución mediante la limitación a un conjunto determinado de agentes. Utilice la [selección de destino de la solución](../azure-monitor/insights/solution-targeting.md) para aplicar un ámbito a la solución y tener como destino un subconjunto de equipos del área de trabajo. Si usa la selección de destino de la solución, Defender for Cloud muestra el área de trabajo como si no tuviera una solución.
