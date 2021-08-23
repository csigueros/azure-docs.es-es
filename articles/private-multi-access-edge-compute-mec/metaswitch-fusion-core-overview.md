---
title: Solución Fusion Core en Azure
description: 'Descripción general de Fusion Core: una implementación nativa en la nube del 5G Next Generation Core (5G NGC o 5GC) definido por los estándares 3GPP que permite a los operadores de redes 5G agregar tráfico de datos desde todos los dispositivos finales a través de múltiples tecnologías de acceso fijo e inalámbrico.'
ms.service: private-multi-access-edge-compute-mec
author: djrmetaswitch
ms.author: drichards
ms.topic: overview
ms.date: 06/16/2021
ms.openlocfilehash: dd813b08301da960fb13e6047e51046ac2d95aed
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112464564"
---
# <a name="what-is-fusion-core"></a>¿Qué es Fusion Core?

Fusion Core es una implementación nativa en la nube del 5G Next Generation Core (5G NGC o 5GC) definido por los estándares 3GPP que permite a los operadores de redes 5G agregar tráfico de datos desde todos los dispositivos finales a través de múltiples tecnologías de acceso fijo e inalámbrico. Consta de una función de plano de usuario (UPF) 5G de alto rendimiento y muy programable, funciones básicas del plano de control, una cartera de elementos de arquitectura basados en servicios y componentes de administración para la supervisión de red.

Fusion Core se puede implementar en escenarios 5G puros o puede interoperar con redes 4G.

Fusion Core implementa las siguientes funciones de red 5G.

|Función de red  |Descripción  |
|---------|---------|
|**AMF**<br><br>Función de administración de acceso y movilidad     |Admite lo siguiente.<br><ul> <li>Terminación de la señalización NAS desde el gNB</li><li>Cifrado y protección de integridad de NAS</li><li>Administración de registros</li><li>Administración de las conexiones</li><li>Administración de la movilidad</li><li>Autenticación y autorización de acceso</li><li>Administración de contexto de seguridad</li></ul>         |
|**SMF**<br><br>Función de administración de sesiones     |SMF admite la configuración, modificación y versión de la sesión.<br><br>Como parte de esto, proporciona administración y asignación de direcciones IP de UE, incluido DHCP, y terminación de la señalización NAS relacionada con la administración de sesiones.<br><br>También proporciona asistencia para la paginación de dispositivos en el tráfico de datos de vínculo inferior y la configuración de dirección de tráfico como parte de la administración de sesiones.         |
|**UPF**<br><br>Función de plano de usuario     |La UPF es responsable de controlar el tráfico de datos de paquetes. Esto incluye el control del enrutamiento, reenvío, inspección y QoS para el tráfico de datos de paquetes.<br><br>Actúa como punto de anclaje para el tráfico tanto a la red de datos (DN) como para la entrega entre distintas redes de radio.         |
|**PCF**<br><br>Función de control de directivas     |La PCF proporciona un marco de directivas central para el tráfico, que proporciona reglas de directiva para la función del plano de control y actúa como origen de información de suscripción.         |
|**AUSF**<br><br>Función de servidor de autenticación     |El AUSF es el servidor de autenticación para suscriptores 5G.         |
|**UDM**<br><br>Administración de datos unificados     |La UDM admite la generación de las credenciales de AKA, la identificación del usuario, la autorización de acceso y la administración del suscriptor.         |
|**UDR**<br><br>Repositorio de datos unificado     |El UDR es un repositorio convergente de toda la información del suscriptor.         |
|**NRF**<br><br>Función de repositorio de red     |La NRF proporciona la detección de servicios para las NF.         |

También implementa las siguientes funciones de red cuando se entrelaza con una red 4G.

|Función de red  |Descripción  |
|---------|---------|
|**MME**<br><br>Entidad de administración de la movilidad     |La MME es el nodo de control clave de la red de acceso a LTE, y es responsable de controlar todos los aspectos del acceso de UE a la red.         |
|**S11-IWF**<br><br>Función de intertrabajo S11 |La S11-IWF expone una interfaz S11 a entidades de administración de movilidad (MME) 4G y proporciona traducciones de protocolos para las funciones de apoyo.         |
|**UDR**<br><br>Repositorio de datos unificado     |El UDR es un repositorio convergente de toda la información del suscriptor. El UDR de Fusion Core lleva a cabo el rol que normalmente realizaría un almacén de suscriptores de inicio (HSS) en un escenario 4G. Interactúa con la MME mediante la interfaz S6a.         |

En el diagrama siguiente se muestra cada una de estas funciones de red y las interfaces que usan para interoperar con componentes de terceros.

:::image type="content" source="./media/metaswitch-overview/fusion-core-architecture.png" alt-text="Arquitectura de Fusion Core":::

Fusion Core se entrega en una máquina virtual (VM) conocida como máquina virtual base de Fusion Core. La máquina virtual base de Fusion Core está diseñada para implementarse como una aplicación administrada por Azure en Azure Stack Edge (ASE). Las funciones de red y los componentes de infraestructura necesarios para entregar la función Fusion Core se implementan como contenedores en la máquina virtual base de Fusion Core y se orquestan mediante Kubernetes.

:::image type="content" source="./media/metaswitch-overview/fusion-core-base-vm-azure-stack-edge-with-networking.png" alt-text="Máquina virtual base de Fusion Core en Azure Stack Edge":::

## <a name="why-use-fusion-core"></a>¿Por qué usar Fusion Core?

### <a name="deployment-in-private-networks"></a>Implementación en redes privadas

Fusion Core usa las funcionalidades del proceso perimetral de multiacceso privado de Azure para proporcionar una solución 5G para las empresas que combina el rendimiento y la baja latencia de los recursos de proceso de Edge necesarios para admitir casos de uso de industria 4.0, con una administración coherente y centralizada a través de Azure. Para obtener información detallada sobre el proceso perimetral de acceso múltiple privado, consulte [¿En qué consiste el proceso perimetral de multiacceso privado de Azure?](overview.md)

La implementación de Fusion Core en el perímetro empresarial garantiza que esté lo más cerca posible de los dispositivos a los que sirve, lo que le permite ofrecer niveles de latencia bajos y reducir la retroconexión a través del procesamiento de datos local cuando se combina con la lógica de aplicación en la misma ubicación. Esto proporciona una serie de ventajas importantes para las empresas, como las siguientes.

- **Automatización**: los mensajes de comando y control de sistemas automatizados (como robots) se pueden procesar en tiempo real para evitar el descontrol, lo que garantiza una mayor productividad.
- **Telemetría**: los datos de telemetría para la evaluación del estado y el funcionamiento de los sistemas automatizados se pueden procesar en tiempo real para evitar accidentes y garantizar la seguridad en el sitio.
- **Análisis**: se pueden transportar grandes cantidades de datos operativos y de diagnóstico a un costo mínimo, lo que garantiza que las acciones vitales no se retrasen.

:::image type="content" source="./media/metaswitch-overview/enterprise-edge-latency.png" alt-text="Fusion Core en Private 5G Edge":::

Fusion Core puede aprovechar esta baja latencia junto con la seguridad y el ancho de banda alto que ofrecen las redes privadas 5G, lo que la coloca en la posición óptima para admitir casos de uso de industria 4.0 como los siguientes.

- **Fabricación**: análisis de línea de producción y automatización de almacenamiento con robots.
- **Seguridad pública**: movilidad y conectividad para trabajadores de emergencia y agentes de recuperación ante desastres.
- **Energía y utilidades**: redes de retroconexión para medidores inteligentes y la distribución y el control de red.
- **Defensa**: publicaciones de comandos conectados y ataques con análisis en tiempo real.
- **Granjas inteligentes**: equipo conectado para el funcionamiento de la granja.

### <a name="pure-5g-and-4g-interworking-support"></a>Compatibilidad pura con intertrabajo de 5G y 4G

Fusion Core se puede implementar en escenarios puramente 5G. En este caso, Fusion Core se ejecuta en **modo independiente 5G**.

Fusion Core también admite el intertrabajo de 4G, lo que proporciona servicio a los UE 4G a través de un núcleo nativo de la nube. Esto se conoce como **modo 4G**. En el caso de los operadores de red nuevos y tradicionales, el modo 4G proporciona una ruta de acceso inmediata a 5G sin inversiones continuas en el mantenimiento de un núcleo de 4G.

### <a name="service-assurance-and-kpi-metrics"></a>Garantía de servicio y métricas de KPI

Fusion Core se integra con el **servidor de garantía de servicio (SAS) de Metaswitch**, lo que proporciona un análisis proactivo y en tiempo real de todo el tráfico de mensajes, incluidos los mensajes NGAP/NAS y las solicitudes y respuestas HTTP.

El servidor de garantía de servicio ofrece una GUI web que puede usar para recopilar seguimientos detallados para los flujos de señalización que implican Fusion Core. Se pueden usar para diagnosticar muchos problemas comunes de configuración, red e interoperabilidad que afectan al servicio de usuario.

:::image type="content" source="./media/metaswitch-overview/service-assurance-server-detailed-timeline.png" alt-text="Vista de escala de tiempo detallada en la GUI web del servidor de garantía de servicio":::

Fusion Core también se integra con **Metaswitch ServiceIQ Monitoring**, que proporciona herramientas de supervisión nativas en la nube estándar del sector, como Prometheus y Grafana, lo que permite el análisis en tiempo real del rendimiento del sistema, la identificación de errores y la solución de problemas.

ServiceIQ Monitoring se ejecuta en la máquina virtual de Fusion Core y permite acceder a varios paneles de Fusion Core, que proporcionan una manera flexible de supervisar las métricas clave relacionadas con la implementación de Fusion Core. También le permiten ver información sobre la activación de alertas, lo que garantiza que puede reaccionar rápidamente ante problemas emergentes.


[![Panel de información general de Fusion Core](media/metaswitch-overview/fusion-core-overview-dashboard.png)](media/metaswitch-overview/fusion-core-overview-dashboard.png#lightbox)

### <a name="other-features"></a>Otras características

|Característica  |Descripción  |
|---------|---------|
|**Distribución de red**     |Las implementaciones de Fusion Core se pueden configurar para proporcionar varios segmentos de red a redes lógicas independientes multiplex. Todas las funciones de red dentro de una única implementación de Fusion Core sirven a todos los segmentos configurados.<br><br>Puede elegir aprovisionar suscriptores con sus segmentos permitidos y predeterminados y definir una directiva específica de segmento (QoS), lo que proporciona un mecanismo sencillo y ubicuo para mantener la separación administrativa de grupos de suscriptores.         |
|**Procedimientos 5G admitidos**     |Fusion Core cumple con 3GPP TS 23.502 para los procedimientos siguientes cuando se opera como parte de una solución 5G privada más amplia.<ul><li>Registro/cancelación de registro de UE</li><li>Actualización del registro de movilidad / Actualización periódica del registro</li><li>Solicitud de servicio iniciada por UE (señalización/datos)</li><li>Versión de contexto iniciada por UE AN/Network</li><li>Establecimiento de sesión de PDU</li><li>Versión de sesión de PDU</li><li>Entrega basada en el nodo N2 de Inter NG-RAN</li><li>Entrega entre NG-RAN basada en Xn</li><li>Notificación o paginación de datos de vínculos descendentes iniciados en la red</li>        |
|**Autenticación UE**     |<ul><li>Compatibilidad con la función de delimitador de seguridad (SEAF) para proporcionar funcionalidad de autenticación en la red de servicio.</li><li>Autenticación mediante identificadores permanentes de suscripción (SUPI), identificadores ocultos de suscripción (SUCI) e identidades temporales únicas globales (5G-GUTI).</li><li>Asignación o reasignación de un 5G-GUTI a un UE.</li><li>Contrato de clave y autenticación de 3ª generación (EAP-AKA) y contrato de clave y autenticación 5G (5G-AKA) para la autenticación mutua entre los UE y la red.</li><li>Acuerdo de clave y autenticación basado en el sistema de paquetes evolucionado (EPS-AKA) para implementaciones que se ejecutan en modo 4G.</li>         |
|**Administración de contexto de seguridad de UE**     |La AMF de Fusion Core realiza la protección de cifrado e integridad de NAS 5G. Durante el registro de UE, el UE incluye sus funcionalidades de seguridad para NAS 5G con claves de 128 bits.<br><br>Entre los algoritmos admitidos por Fusion Core para la protección de la integridad y el cifrado se incluyen los siguientes.<ul><li>Algoritmo de cifrado NULL 5GS</li><li>Snow3G de 128 bits</li><li>AES de 128 bits</li><li>ZUC de 128 bits</li>        |
|**Configuración de MTU de UE**     |SMF de Fusion Core señala la MTU de una red de datos a los UE a petición como parte de los procedimientos de establecimiento de sesión de PDU para evitar la fragmentación.         |
|**Control de directivas**     |Fusion Core aplica las decisiones de control de directivas de un extremo a otro y el cumplimiento por sesión de PDU y por flujo. Esto proporciona la flexibilidad necesaria para aplicar diferentes niveles de QoS a flujos o UE concretos, y le permite exponer de forma selectiva servicios o redes de datos a grupos de UE y controlar los tipos de tráfico que fluyen a través de la red.         |
|**Listas de control de acceso UPF (ACL)**     |Las ACL se pueden usar para permitir o bloquear el tráfico dirigido desde o hacia intervalos de direcciones IPv4 específicos. Puede configurar ACL en las interfaces de acceso (N3) y núcleo (N6) de Fusion Core.         |
|**Indexación a la prioridad de selección de frecuencia/RAT (RFSP)**     |La AMF de Fusion Core puede proporcionar un RAN con un índice RFSP, que el RAN puede hacer coincidir con su configuración local para aplicar directivas específicas de administración de recursos de radio, como la reselección de celdas o el redireccionamiento de la capa de frecuencia.         |

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre cómo [implementar Fusion Core](deploy-metaswitch-fusion-core-solution.md)

