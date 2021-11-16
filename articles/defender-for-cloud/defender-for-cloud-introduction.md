---
title: 'Microsoft Defender for Cloud: introducción'
description: Use Microsoft Defender for Cloud para proteger los recursos y cargas de trabajo híbridos y de varias nubes de Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.custom: mvc
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: b809efec6a34179b97f7389cf09662a2600d61b4
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373442"
---
# <a name="what-is-microsoft-defender-for-cloud"></a>¿Qué es Microsoft Defender for Cloud?

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender for Cloud es una herramienta para la administración de la posición de seguridad y la protección contra amenazas. Esta refuerza la posición de seguridad de los recursos en la nube y, gracias a los planes integrados de Microsoft Defender, Defender for Cloud protege las cargas de trabajo híbridas que se ejecutan en Azure y otras plataformas en la nube.

Defender for Cloud proporciona las herramientas necesarias para proteger los recursos, realizar un seguimiento de su posición de seguridad, protegerse frente a ciberataques y simplificar la administración de la seguridad. Dado que está integrada de forma nativa, la implementación de Defender for Cloud es fácil, lo que proporciona un aprovisionamiento automático sencillo para proteger los recursos de forma predeterminada.

Defender for Cloud cubre tres necesidades vitales a medida que administra la seguridad de los recursos y las cargas de trabajo en la nube y en el entorno local:

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-synopsis.png" alt-text="Descripción de la funcionalidad básica de Microsoft Defender for Cloud ":::.

|Requisito de seguridad  | Solución Defender for Cloud|
|---------|---------|
|**Evaluación continua**: comprenda su posición de seguridad actual.   | **Puntuación de seguridad**: una sola puntuación para que pueda conocer de un vistazo la situación de la seguridad actual; cuanto mayor sea la puntuación, menor será el nivel de riesgo identificado.       |
|**Seguridad**: proteja todos los recursos y servicios conectados. | **Recomendaciones de seguridad**: tareas de protección personalizadas y prioritarias para mejorar su posición. Para implementar una recomendación, siga los pasos de corrección detallados que se proporcionan en la recomendación. Para proporcionar varias recomendaciones, Defender for Cloud ofrece el botón "Corregir" para realizar la implementación automatizada.|
|**Defensa**: detecte y resuelva las amenazas a esos recursos y servicios. | **Alertas de seguridad**: con las características de seguridad mejoradas habilitadas, Defender for Cloud detecta amenazas para los recursos y cargas de trabajo. Estas alertas aparecen en Azure Portal y Defender for Cloud también puede enviarlas por correo electrónico al personal correspondiente de su organización. Las alertas también se pueden transmitir a las soluciones SIEM, SOAR o IT Service Management según sea necesario. |

## <a name="posture-management-and-workload-protection"></a>Administración de posturas y protección de cargas de trabajo

Las características de Microsoft Defender para la nube cubren los dos grandes pilares de la seguridad en la nube: la administración de la posición de seguridad en la nube y la protección de cargas de trabajo en la nube.

### <a name="cloud-security-posture-management-cspm"></a>Administración de la posición de seguridad en la nube (CSPM)

En Defender for Cloud, las características de administración de posturas proporcionan:

- **Visibilidad**: para ayudarle a entender su situación de seguridad actual.
- **Guía de protección**: para ayudarle a mejorar la seguridad de forma eficaz.

La característica principal de Defender for Cloud que le ayudará a conseguir estos objetivos es la **puntuación de seguridad**. Defender for Cloud evalúa continuamente los recursos, suscripciones y la organización en busca de problemas de seguridad. A continuación, agrega todos los resultados a una sola puntuación para que pueda conocer de un vistazo la situación de la seguridad actual: cuanto mayor sea la puntuación, menor será el nivel de riesgo identificado.

Cuando abra Defender for Cloud por primera vez, esta herramienta cumplirá los objetivos de visibilidad y reforzamiento de la siguiente manera:

1. **Generará una puntuación segura** para sus suscripciones en función de la evaluación de sus recursos conectados en comparación con la guía de [Azure Security Benchmark](/security/benchmark/azure/overview). Use la puntuación para comprender su posición de seguridad y el panel de cumplimiento para revisar el cumplimiento con la prueba comparativa integrada. Cuando haya habilitado las características de seguridad mejoradas, puede personalizar los estándares que se usan para evaluar el cumplimiento y agregar otras regulaciones (como NIST y Azure CIS) o requisitos de seguridad específicos de la organización.

1. **Proporcionará recomendaciones de protección** en función de los errores de configuración y los puntos débiles de seguridad identificados. Use estas recomendaciones de seguridad para reforzar la posición de seguridad de los recursos híbridos y de varias nubes de Azure de su organización.

[Más información sobre la puntuación de seguridad](secure-score-security-controls.md)

### <a name="cloud-workload-protection-cwp"></a>Protección de cargas de trabajo en la nube (CWP)

Defender for Cloud ofrece alertas de seguridad con tecnología de [Inteligencia sobre amenazas de Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684). También incluye una gama de protecciones avanzadas e inteligentes para las cargas de trabajo. Las protecciones de cargas de trabajo se proporcionan a través de planes de Microsoft Defender específicos de los tipos de recursos de las suscripciones. Por ejemplo, puede habilitar **Microsoft Defender para Storage** y recibir alertas sobre actividades sospechosas relacionadas con las cuentas de Azure Storage.

## <a name="azure-hybrid-and-multi-cloud-protections"></a>Protecciones de Azure, híbridas y de varias nubes

Dado que Defender for Cloud es un servicio nativo de Azure, muchos servicios de Azure se supervisan y protegen sin necesidad de ninguna implementación.

Cuando sea necesario, Defender for Cloud puede implementar automáticamente un agente de Log Analytics para recopilar datos relacionados con la seguridad. En el caso de las máquinas de Azure, la implementación se controla directamente. Para entornos híbridos y de varias nubes, los planes de Microsoft Defender se extienden a máquinas que no son de Azure con la ayuda de [Azure Arc](https://azure.microsoft.com/services/azure-arc/). Las características de CSPM se extienden a máquinas de varias nubes sin necesidad de ningún agente (consulte [Defensa de los recursos que se ejecutan en otras nubes](#defend-resources-running-on-other-clouds)).

### <a name="azure-native-protections"></a>Protecciones nativas de Azure

Defender for Cloud le permite detectar amenazas en:

- **Servicios de PaaS de Azure**: puede detectar amenazas dirigidas a servicios de Azure como Azure App Service, Azure SQL, la cuenta de Azure Storage y otros servicios de datos. También puede realizar la detección de anomalías en los registros de actividad de Azure mediante la integración nativa con Microsoft Defender para aplicaciones en la nube (anteriormente conocido como Microsoft Cloud App Security).

- **Servicios de datos de Azure**: Defender for Cloud incluye funcionalidades que le ayudarán a clasificar automáticamente los datos en Azure SQL. También puede obtener evaluaciones de las posibles vulnerabilidades en los servicios de Azure SQL y Azure Storage, además de recomendaciones sobre cómo mitigarlas.

- **Redes**: Defender for Cloud le permite limitar la exposición a los ataques por fuerza bruta. Si reduce el acceso a los puertos de las máquinas virtuales mediante el acceso de máquina virtual Just-In-Time, puede proteger la red al prevenir el acceso innecesario. Puede establecer directivas de acceso seguro en los puertos seleccionados, solo para usuarios autorizados, direcciones IP o intervalos de direcciones IP de origen permitidos y durante un período limitado.

### <a name="defend-your-hybrid-resources"></a>Defensa de los recursos híbridos

Además de defender el entorno de Azure, puede agregar funcionalidades de Defender for Cloud a su entorno de nube híbrida para proteger los servidores que no sean de Azure. Obtenga inteligencia personalizada sobre las amenazas y alertas prioritarias según su entorno específico para que pueda centrarse en lo que más le importa.

Para ampliar la protección a las máquinas locales, implemente [Azure Arc](https://azure.microsoft.com/services/azure-arc/) y habilite las características de seguridad mejoradas de Defender for Cloud. Obtenga más información en [Incorporación de máquinas que no sean de Azure con Azure Arc](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc).

### <a name="defend-resources-running-on-other-clouds"></a>Defensa de los recursos que se ejecutan en otras nubes

Defender for Cloud puede proteger los recursos de otras nubes (como AWS y GCP).

Por ejemplo, si ha [conectado una cuenta de Amazon Web Services (AWS)](quickstart-onboard-aws.md) a una suscripción de Azure, puede habilitar cualquiera de estas protecciones:

- **Las características de CSPM de Defender para la nube** se extienden a los recursos de AWS. Este plan sin agente evalúa los recursos de AWS según las recomendaciones de seguridad específicas de AWS, que se incluyen en la puntuación de seguridad. También se evaluará el cumplimiento de los recursos de los estándares integrados específicos de AWS (AWS CIS, AWS PCI DSS y Procedimientos recomendados de seguridad fundamentales de AWS). La [página de inventario de recursos](asset-inventory.md) de Defender for Cloud es una característica habilitada para varias nubes, que permite administrar los recursos de AWS junto con los de Azure.
- **Microsoft Defender para Kubernetes** amplía la detección de amenazas de contenedores y defensas avanzadas a los **clústeres Linux de Amazon EKS**.
- **Microsoft Defender para servidores** proporciona la detección de amenazas y defensas avanzadas a las instancias de EC2 con Windows y Linux. Este plan incluye la licencia integrada de Microsoft Defender para punto de conexión, líneas base de seguridad y evaluaciones de nivel de sistema operativo, análisis de evaluación de vulnerabilidades, controles de aplicaciones adaptables (AAC), supervisión de la integridad de los archivos (FIM) y mucho más.

Más información sobre la conexión de las cuentas de [AWS](quickstart-onboard-aws.md) y [GCP](quickstart-onboard-gcp.md) a Microsoft Defender para la nube.

## <a name="vulnerability-assessment-and-management"></a>Evaluación y administración de vulnerabilidades

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-assess.png" alt-text="Céntrese en las características de evaluación de Microsoft Defender for Cloud.":::

Defender for Cloud incluye soluciones de evaluación de vulnerabilidades para las máquinas virtuales, los registros de contenedor y los servidores de SQL como parte de las características de seguridad mejoradas. Algunos de los exámenes están basados en Qualys. Aún así, no necesita ninguna licencia ni cuenta de Qualys, ya que todo se administra sin problemas en Defender for Cloud.

Microsoft Defender para servidores incluye integración nativa automática con Microsoft Defender para puntos de conexión. Para obtener más información, consulte [Protección de los puntos de conexión con la solución EDR integrada de Defender for Cloud: Microsoft Defender para puntos de conexión](integration-defender-for-endpoint.md). Si ha habilitado esta integración, tendrá acceso a los hallazgos relacionados con vulnerabilidades de la **administración de amenazas y vulnerabilidades de Microsoft**. Obtenga más información en [Investigación de puntos débiles con la solución de administración de amenazas y vulnerabilidades de Microsoft Defender para punto de conexión](deploy-vulnerability-assessment-tvm.md).

Revise los resultados de estos exámenes de vulnerabilidades y responda a todos ellos desde Defender for Cloud. Este amplio enfoque permite que Defender for Cloud sea el único panel para todos los esfuerzos de seguridad en la nube.

Más información en las siguientes páginas:

- [Examen de Qualys integrado en Defender para la nube para Azure y máquinas híbridas](deploy-vulnerability-assessment-vm.md)
- [Identificación de vulnerabilidades en las imágenes de los registros de contenedor de Azure](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)

## <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Optimice y mejore la seguridad mediante la configuración de controles recomendados

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-secure.png" alt-text="Céntrese en las características de &quot;seguridad&quot; de Microsoft Defender for Cloud.":::

En lo que respecta a la seguridad, resulta fundamental saber que las cargas de trabajo están protegidas y asegurarse de ello, y todo esto comienza con la implementación de directivas de seguridad a la medida. Como las directivas de Defender for Cloud se crean sobre los controles de directivas de Azure, puede obtener la gama completa y la flexibilidad de una **solución de directivas de primer nivel**. En Defender for Cloud, puede establecer que las directivas se ejecuten en grupos de administración, entre distintas suscripciones e incluso en un inquilino completo.

Defender for Cloud detecta continuamente nuevos recursos que se implementan en las cargas de trabajo y evalúa si están configurados según los procedimientos recomendados de seguridad. Si no es así, se marcan y se obtiene una lista prioritaria de recomendaciones para lo que necesita corregir. Las recomendaciones le permitirán disminuir la superficie expuesta a ataques en cada uno de los recursos.

La lista de recomendaciones está habilitada y es compatible con Azure Security Benchmark. Este punto de referencia es el conjunto de directrices específico de Azure y creado por Microsoft relativo a los procedimientos recomendados de seguridad y cumplimiento basados en marcos de cumplimiento comunes. Puede obtener más información en [Introducción a Azure Security Benchmark](/security/benchmark/azure/introduction).

De este modo, Defender for Cloud le permite no solo establecer las directivas de seguridad, sino también *aplicar los estándares de seguridad en todos los recursos*.

:::image type="content" source="./media/defender-for-cloud-introduction/recommendation-example.png" alt-text="Ejemplo de recomendación de Defender for Cloud.":::

Para ayudarle a conocer el grado de importancia que tiene cada una de las recomendaciones en su postura global acerca de la seguridad, Defender for Cloud agrupa las recomendaciones en controles de seguridad y agrega un valor de **puntuación de la seguridad** a cada control. Esto resulta esencial para permitirle **clasificar por orden de prioridad el trabajo de seguridad**.

:::image type="content" source="./media/defender-for-cloud-introduction/sc-secure-score.png" alt-text="Puntuación segura de Defender for Cloud.":::

## <a name="defend-against-threats"></a>Defensa contra amenazas

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-defend.png" alt-text="Céntrese en las características de &quot;defensa&quot; de Microsoft Defender for Cloud.":::

Defender for Cloud proporciona:

- **Alertas de seguridad**: cuando Defender for Cloud detecta una amenaza en cualquiera de las áreas del entorno, genera una alerta de seguridad. Estas alertas describen los detalles de los recursos afectados, los pasos de corrección sugeridos y, en algunos casos, una opción para desencadenar una aplicación lógica como respuesta. Tanto si Defender for Cloud genera una alerta como si la recibe desde un producto de seguridad integrado, puede exportarla. Para exportar las alertas a Microsoft Sentinel, la SIEM de terceros, o cualquier otra herramienta externa, sigue las instrucciones de [Transmisión de alertas a una solución de administración de servicios de TI, SIEM o SOAR](export-to-siem.md). La protección contra amenazas de Defender for Cloud incluye el análisis de la cadena de destrucción de fusión, que correlaciona de manera automática las alertas del entorno en función del análisis de la cadena de destrucción cibernética, para que pueda entender mejor la historia completa de una campaña de ataque, dónde empezó y qué tipo de impacto tuvo en los recursos. [Las intenciones de la cadena de eliminación admitidas de Defender for Cloud se basan en la versión 7 de MITRE ATT&CK Matrix](alerts-reference.md#intentions).

- **Características avanzadas de protección contra amenazas** para máquinas virtuales, bases de datos de SQL, contenedores, aplicaciones web y su red entre otros elementos: las protecciones incluyen la protección de los puertos de administración de las VM con acceso de tipo [Just-In-Time](just-in-time-access-overview.md) y los [controles de aplicaciones adaptables](adaptive-application-controls.md) para crear listas de elementos permitidos para las aplicaciones que deben y no deben ejecutarse en las máquinas.

La página de **planes de Defender** de Microsoft Defender for Cloud ofrece los siguientes planes para obtener defensas completas para los niveles de proceso, datos y servicio de su entorno:

- [Microsoft Defender para servidores](defender-for-servers-introduction.md)
- [Microsoft Defender para App Service](defender-for-app-service-introduction.md)
- [Microsoft Defender para Storage](defender-for-storage-introduction.md)
- [Microsoft Defender para SQL](defender-for-sql-introduction.md)
- [Microsoft Defender para Kubernetes](defender-for-kubernetes-introduction.md)
- [Microsoft Defender para registros de contenedor](defender-for-container-registries-introduction.md)
- [Microsoft Defender para Key Vault](defender-for-key-vault-introduction.md)
- [Microsoft Defender para Resource Manager](defender-for-resource-manager-introduction.md)
- [Microsoft Defender para DNS](defender-for-dns-introduction.md)
- [Microsoft Defender para bases de datos relacionales de código abierto](defender-for-databases-introduction.md)

Use los iconos de protección avanzada del [panel para la protección de cargas de trabajo](workload-protections-dashboard.md) para supervisar y configurar cada una de estas protecciones.

> [!TIP]
> Microsoft Defender para IoT es un producto independiente. Encontrará todos los detalles en [Presentación de Microsoft Defender para IoT](../defender-for-iot/overview.md).

## <a name="next-steps"></a>Pasos siguientes

- Para empezar a trabajar con Defender for Cloud, necesita una suscripción a Microsoft Azure. Si aún no tiene una suscripción, [regístrese para obtener una evaluación gratuita](https://azure.microsoft.com/free/).

- El plan gratuito de Defender para la nube está habilitado en todas las suscripciones actuales de Azure cuando visite las páginas de Defender para la nube en Azure Portal por primera vez, o si se habilita mediante programación con la API REST. Para aprovechar las funcionalidades de detección de amenazas y administración de seguridad avanzada, debe habilitar las características de seguridad mejoradas. Estas características son gratuitas durante los primeros 30 días. [Obtenga más información sobre los precios](https://azure.microsoft.com/pricing/details/security-center/).

- Si ya está listo para habilitar las características de seguridad mejoradas, el artículo [Guía de inicio rápido: Habilitación de características de seguridad mejoradas](enable-enhanced-security.md) le guiará a través de los pasos necesarios.

> [!div class="nextstepaction"]
> [Habilitación de los planes Microsoft Defender](enable-enhanced-security.md)
