---
title: 'Microsoft Defender para servidores: ventajas y características'
description: Obtenga información sobre las ventajas y características de Microsoft Defender para servidores.
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a02c6305dd3fecfcc3e83d48f228e1753dda1e2b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467594"
---
# <a name="introduction-to-microsoft-defender-for-servers"></a>Introducción a Microsoft Defender para servidores

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender para servidores es una de las características de seguridad mejoradas de Microsoft Defender for Cloud. Utilícelo para agregar detección de amenazas y defensas avanzadas a las máquinas Windows y Linux, tanto si se ejecutan en Azure, en el entorno local o en un entorno de varias nubes.

Para proteger las máquinas en entornos híbridos y de varias nubes, Defender for Cloud usa [Azure Arc](../azure-arc/index.yml). Conecte las máquinas híbridas y de varias nubes, como se explica en la guía de inicio rápido correspondiente:
- [Conexión de máquinas que no son de Azure a Microsoft Defender for Cloud](quickstart-onboard-machines.md)
- [Conexión de cuentas de AWS a Microsoft Defender for Cloud](quickstart-onboard-aws.md)

> [!TIP]
> Para más información sobre qué características de Defender para servidores son pertinentes para las máquinas que se ejecutan en otros entornos en la nube, consulte [Características admitidas para máquinas virtuales y servidores](supported-machines-endpoint-solutions-clouds.md?tabs=features-windows#supported-features-for-virtual-machines-and-servers-).

## <a name="what-are-the-benefits-of-microsoft-defender-for-servers"></a>¿Cuáles son las ventajas de Microsoft Defender para servidores?

Las funcionalidades de detección de amenazas y protección que se proporcionan con Microsoft Defender para servidores incluyen:

- **Licencia integrada de Microsoft Defender para punto de conexión**: Microsoft Defender para servidores incluye [Microsoft Defender para punto de conexión](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Esta integración ofrece funcionalidades completas de detección y respuesta (EDR) de puntos de conexión. Para más información, consulte [Proteja los puntos de conexión con la solución EDR integrada de Security Center: Microsoft Defender para punto de conexión](integration-defender-for-endpoint.md).

    Cuando Defender for Endpoint detecta una amenaza, desencadena una alerta. La alerta se muestra en Defender for Cloud. En Defender for Cloud, también puede dinamizar hasta la consola de Defender para punto de conexión para realizar una investigación detallada y descubrir el alcance del ataque. Obtenga más información acerca de Microsoft Defender for Endpoint.

    > [!IMPORTANT]
    > La integración de Defender for Cloud con Microsoft Defender para punto de conexión está habilitada de manera predeterminada. Por lo tanto, al habilitar Microsoft Defender, da su consentimiento para Defender for Cloud para que los servidores accedan a los datos de Microsoft Defender para punto de conexión relacionados con vulnerabilidades, software instalado y alertas de sus puntos de conexión.
    >
    > Actualmente ofrecemos el sensor para máquinas Linux en versión preliminar. Para obtener más información, consulte [Protección de los puntos de conexión con la solución EDR integrada de Defender for Cloud: Microsoft Defender para punto de conexión](integration-defender-for-endpoint.md).

- **Herramientas de evaluación de vulnerabilidades para máquinas**: Microsoft Defender para servidores incluye una selección de herramientas de detección y administración de vulnerabilidades para las máquinas. En las páginas de configuración de Defender for Cloud, puede seleccionar cuál de estas herramientas se implementa en las máquinas y las vulnerabilidades detectadas se mostrarán en una recomendación de seguridad.

    - **Administración de vulnerabilidades y amenazas de Microsoft**: detecte las vulnerabilidades y configuraciones incorrectas en tiempo real con Microsoft Defender for Endpoint y sin necesidad de agentes ni exámenes periódicos. La [administración de amenazas y vulnerabilidades](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt) prioriza las vulnerabilidades en función del panorama de las amenazas, las detecciones en la organización, la información confidencial de los dispositivos vulnerables y el contexto empresarial. Obtenga más información en [Investigación de puntos débiles con la solución de administración de amenazas y vulnerabilidades de Microsoft Defender para punto de conexión](deploy-vulnerability-assessment-tvm.md)

    - **Detector de vulnerabilidades con tecnología de Qualys**: el detector de Qualys es una de las herramientas líderes para identificar en tiempo real las vulnerabilidades en las máquinas virtuales híbridas y de Azure. No necesita ninguna licencia ni cuenta de Qualys, ya que todo se administra sin problemas en Defender for Cloud. Obtenga más información en [Examen de Qualys integrado en Defender for Cloud para Azure y máquinas híbridas](deploy-vulnerability-assessment-vm.md).

- **Acceso Just-In-Time (JIT) a máquinas virtuales**: los agentes de amenazas buscan activamente máquinas accesibles con puertos de administración abiertos, como RDP o SSH. Todas las máquinas virtuales son objetivos potenciales para un ataque. Cuando se consigue poner en peligro a una máquina virtual, se usa como punto de entrada para atacar más recursos dentro de su entorno.

    Una vez habilitado Microsoft Defender para servidores, puede usar el acceso Just-In-Time a máquinas virtuales para bloquear el tráfico entrante a las máquinas virtuales, lo que reduce la exposición a ataques al mismo tiempo que proporciona un acceso sencillo para conectarse a las máquinas virtuales cuando sea necesario. Para más información, consulte [Descripción del acceso a la máquina virtual Just-in-Time (JIT)](just-in-time-access-overview.md).

- **Supervisión de la integridad de los archivos (FIM)** : la supervisión de la integridad de los archivos (FIM), conocida también como supervisión de los cambios, examina los archivos y los registros del sistema operativo, el software de aplicación y demás para comprobar la existencia de cambios que podrían indicar un ataque. Para determinar si el estado actual del archivo es diferente del último examen del archivo, se usa un método de comparación. Puede aprovechar esta comparación para determinar si se han realizado modificaciones sospechosas o válidas en los archivos.

    Una vez habilitado Microsoft Defender para servidores, puede usar FIM para validar la integridad de los archivos de Windows, los registros de Windows y los archivos de Linux. Para obtener más información, vea [Supervisión de la integridad de los archivos en Microsoft Defender for Cloud](file-integrity-monitoring-overview.md).

- **Controles de aplicaciones adaptables (ACC)** : los controles de aplicaciones adaptables son una solución inteligente y automatizada que permite definir listas de aplicaciones permitidas seguras conocidas para las máquinas.

    Cuando haya habilitado y configurado controles de aplicaciones adaptables, recibirá alertas de seguridad si alguna aplicación ejecuta otros distintos a los definidos como seguros. Para más información, consulte [Uso de controles de aplicaciones adaptables para reducir las superficies de ataque de las máquinas](adaptive-application-controls.md).

- **Protección de red adaptable (ANH)** : la aplicación de grupos de seguridad de red (NSG) para filtrar el tráfico hacia y desde los recursos mejora la posición de seguridad de red. Sin embargo, aún puede haber algunos casos en los que el tráfico real que fluye a través del NSG es un subconjunto de las reglas de NSG definidas. En estos casos, puede mejorar la postura de seguridad al proteger aún más las reglas de NSG, según los patrones de tráfico real.

    La protección de red adaptable proporciona recomendaciones para proteger mejor las reglas de NSG. Usa un algoritmo de aprendizaje automático que tiene en cuenta el tráfico real, la configuración de confianza conocida, la inteligencia de amenazas y otros indicadores de riesgo, y luego proporciona recomendaciones para permitir el tráfico solo desde tuplas IP y puerto específicas. Para más información, consulte [Mejora de la posición de seguridad de red con la protección de redes adaptativa](adaptive-network-hardening.md).


- **Protección del host de Docker**: Microsoft Defender for Cloud identifica contenedores no administrados y que están hospedados en VM de IaaS Linux u otras máquinas de Linux que ejecutan contenedores de Docker. Defender for Cloud evalúa continuamente las configuraciones de estos contenedores. A continuación, las compara con el Banco de prueba para Docker del Centro de seguridad de Internet (CIS). Defender for Cloud incluye todo el conjunto de reglas del banco de prueba de Docker de CIS y le avisa si los contenedores no cumplen ninguno de los controles. Para más información, consulte [Protección de los hosts de Docker](harden-docker-hosts.md).

- **Detección de ataques sin archivos**: Los ataques sin archivos inyectan cargas malintencionadas en la memoria para evitar la detección mediante técnicas de detección basadas en disco. Luego, la carga del atacante se conserva dentro de la memoria de los procesos en peligro y realiza una amplia variedad de actividades malintencionadas.

  Con la detección de ataques sin archivos, las técnicas forense de memoria automatizadas identifican kits de herramientas, técnicas y comportamientos de los ataques sin archivos. Esta solución examina periódicamente la máquina en tiempo de ejecución y extrae conclusiones directamente de la memoria de los procesos. Las conclusiones específicas incluyen la identificación de: 

  - Kits de herramientas conocidas y software de minería de datos de cifrado. 

  - Shellcode, que es un pequeño fragmento de código que se usa normalmente como carga útil para aprovechar una vulnerabilidad de software.

  - Archivo ejecutable malintencionado insertado en la memoria de proceso.

  La detección de ataques sin archivos genera alertas de seguridad detalladas que incluyen descripciones con los metadatos de proceso, como la actividad de red. Estos detalles aceleran la evaluación de prioridades de alertas, la correlación y el tiempo de respuesta descendente. Este enfoque complementa a las soluciones EDR basadas en eventos y proporciona mayor cobertura de detección.

  Para obtener detalles de las alertas de detección de ataques sin archivo, consulte la [Tabla de referencia de alertas](alerts-reference.md#alerts-windows).

- **Integración de las alertas de auditd de Linux y el agente de Log Analytics (solo Linux)** : el sistema de auditd consta de un subsistema de nivel de kernel, que es responsable de supervisar las llamadas del sistema. Las filtra según un conjunto de reglas especificado y escribe mensajes para ellas en un socket. Defender for Cloud integra funcionalidades del paquete auditd dentro del agente de Log Analytics. Esta integración permite una colección de eventos de auditd en todas las distribuciones de Linux admitidas sin requisitos previos.

    El agente de Log Analytics para Linux recopila registros auditados, los enriquece y los agrega a eventos. Defender for Cloud agrega continuamente análisis nuevos que usan señales de Linux para detectar comportamientos malintencionados en máquinas Linux locales y en la nube. De manera similar a las funcionalidades de Windows, estos análisis abarcan varios procesos sospechosos, intentos de inicio de sesión dudosos, carga de módulos de kernel y otras actividades. Estas actividades pueden indicar que una máquina está sufriendo un ataque o se ha vulnerado.  

    Para obtener una lista de las alertas de Linux, consulte la [Tabla de referencia de alertas](alerts-reference.md#alerts-linux).

## <a name="how-does-defender-for-servers-collect-data"></a>¿Cómo recopila los datos Defender para servidores?

Para Windows, Microsoft Defender for Cloud se integra con servicios de Azure para supervisar y proteger las máquinas Windows. Defender for Cloud presenta las alertas y las sugerencias de corrección de todos estos servicios en un formato fácil de usar.

Para Linux, Defender for Cloud recopila registros de auditoría de máquinas Linux mediante auditd, uno de los marcos de trabajo de Linux más comunes.

En escenarios híbridos y de varias nubes, Defender for Cloud se integra con [Azure Arc](../azure-arc/index.yml) para asegurarse de que estas máquinas que no son de Azure se vean como recursos de Azure. 


## <a name="simulating-alerts"></a>Simulación de alertas

Puede simular alertas mediante la descarga de alguno de los cuadernos de estrategias:

- Por Windows: [Cuaderno de Microsoft Defender for Cloud: alertas de seguridad](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf).

- Para Linux: [Cuaderno de Microsoft Defender for Cloud: detecciones de Linux](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf).




## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha obtenido información sobre Microsoft Defender para servidores. 

> [!div class="nextstepaction"]
> [Habilitación de las protecciones mejoradas](enable-enhanced-security.md)

Puede encontrar material relacionado en la página siguiente:

- Tanto si Defender for Cloud genera una alerta como si la recibe desde un producto de seguridad diferente, puede exportarla. Para exportar las alertas a Microsoft Sentinel, a cualquier SIEM de terceros o a cualquier otra herramienta externa, siga las instrucciones de [Exportación de alertas a un SIEM](continuous-export.md).