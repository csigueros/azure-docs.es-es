---
title: Ventajas clave
description: Obtenga información sobre los conceptos básicos de Defender para IoT.
ms.date: 12/13/2020
ms.topic: article
ms.openlocfilehash: ca1e5a4d8554b208f5275fd0e7519f2db3fafc08
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113021273"
---
# <a name="basic-concepts"></a>Conceptos básicos 

En este artículo se describen las principales ventajas de Azure Defender para IoT.

## <a name="rapid-non-invasive-deployment-and-passive-monitoring"></a>Implementación rápida no invasiva y supervisión pasiva

Los sensores de Defender para IoT se conectan a puertos de SPAN (reflejo) del conmutador y a los puntos de acceso de terminal de red e inmediatamente comienzan a recopilar el tráfico de ICS mediante supervisión pasiva (sin agente). La inspección profunda de paquetes (PPP) se usa para analizar minuciosamente el tráfico desde el equipo de red de control de serie y Ethernet. Defender para IoT no tiene ningún impacto en las redes OT, porque no se coloca en la ruta de acceso de datos y no examina de manera activa los dispositivos OT. 

Para proporcionar instantáneas rápidas de información detallada sobre dispositivos Windows, el sensor de Defender para IoT se puede configurar para complementar la supervisión pasiva con un componente activo opcional. Este componente usa comandos seguros y aprobados por el proveedor para consultar los detalles de los dispositivos Windows, con tanta o tan poca frecuencia como desee.

## <a name="embedded-knowledge-of-ics-protocols-devices-and-applications"></a>Conocimiento insertado de protocolos, dispositivos y aplicaciones de ICS

Solo PPP no es suficiente para identificar las anomalías de protocolo e identificar el dispositivo en un nivel granular. El sensor de Defender para IoT trata algunos de los entornos más grandes y complejos. Se han analizado más de 1300 redes OT hasta la fecha, en todos los sectores industriales.

## <a name="analytics-and-self-learning-engines"></a>Análisis y motores de autoaprendizaje

Los motores identifican problemas de seguridad mediante la supervisión continua y cinco motores de análisis que incorporan el autoaprendizaje para eliminar la necesidad de actualizar firmas o definir reglas. Los motores utilizan el análisis de comportamiento y la ciencia de datos específicos de ICS para analizar continuamente el tráfico de red OT en busca de anomalías. Los cinco motores son los siguientes:

- **Detección de infracciones del protocolo**: identifica el uso de estructuras de paquetes y valores de campo que infringen las especificaciones del protocolo ICS.

- **Detección de infracciones de directivas**: identifica las infracciones de directivas, como el uso no autorizado de códigos de función, el acceso a objetos específicos o los cambios en la configuración de los dispositivos.

- **Detección de malware industrial**: identifica comportamientos que indican la presencia de malware conocido, como Conficker, Black Energy, Havex, WannaCry y NotPetya.

- **Detección de anomalías**: Detecta las comunicaciones de máquina a máquina (M2M) y los comportamientos inusuales. Con el modelado de las redes ICS como secuencias deterministas de estados y transiciones, el motor usa una técnica patentada denominada modelado de estado finito industrial (IFSM). La solución requiere un período de aprendizaje más corto que los enfoques matemáticos o los análisis genéricos, que se desarrollaron originalmente para TI en lugar de para OT. También detecta anomalías más rápido, con un número mínimo de falsos positivos.

- **Detección de incidentes operativos**: identifica incidentes operativos, como la conectividad intermitente, que pueden se indicadores de los primeros síntomas de errores en el equipo.

## <a name="network-traffic-analysis-for-risk-and-vulnerability-assessment"></a>Análisis del tráfico de red para la evaluación de riesgos y vulnerabilidades

Único en el sector, Defender para IoT usa algoritmos de análisis de tráfico de red (NTA) patentados para identificar de forma pasiva todas las vulnerabilidades de red y de puntos de conexión, como:

- Conexiones de acceso remoto no autorizadas
- Dispositivos no autorizados o no documentados
- Autenticación no segura
- Dispositivos vulnerables (basados en CVE sin revisar)
- Puentes no autorizados entre subredes
- Reglas de firewall no seguras

## <a name="data-mining-for-investigations-forensics-and-threat-hunting"></a>Minería de datos para investigaciones, análisis forenses y búsqueda de amenazas

La plataforma proporciona una interfaz de minería de datos intuitiva para la búsqueda granular de tráfico histórico en todas las dimensiones pertinentes. Entre los ejemplos se incluyen el período de tiempo, la dirección IP, la dirección MAC y los puertos. También puede realizar consultas específicas del protocolo basadas en códigos de función, servicios de protocolo y módulos. Los PCAP de plena fidelidad están disponibles para un análisis más detallado.

## <a name="sensor-cloud-management-mode"></a>Modo de administración de sensores conectados a la nube

El modo de administración de sensores conectados a la nube determina dónde se muestra el dispositivo, la alerta y otra información detectada por el sensor.

En el caso de **sensores conectados a la nube**, la información que el sensor detecta se muestra en la consola del sensor. Se proporciona información de alertas mediante un centro de IoT, que puede compartirse con otros servicios de Azure, como Azure Sentinel.

En el caso de **sensores conectados localmente**, la información que el sensor detecta se muestra en la consola del sensor. La información de detección también se comparte con la consola de administración local si el sensor está conectado a ella.

## <a name="air-gapped-networks"></a>Redes aisladas

Si trabaja en una red aislada, la consola de administración local de Defender para IoT proporciona una vista en tiempo real de las alertas e indicadores de riesgo principales de IoT y OT en todas sus instalaciones. Al estar integrada estrechamente con los runbooks y flujos de trabajo de SOC, facilita la priorización de las actividades de mitigación y la correlación entre sitios de las amenazas.  

Defender para IoT proporciona una vista consolidada de todos los dispositivos. También proporciona información crítica sobre los dispositivos, como el tipo (PLC, RTU, DCS, etc.), el fabricante, el modelo y el nivel de revisión de firmware, así como la información de las alertas.  

Defender para IoT permite la administración efectiva de varias implementaciones y una vista unificada completa de la red. Defender para IoT optimiza la administración y el control de alertas de la seguridad de la red operativa.

La consola de administración local es una plataforma administrativa basada en Web que le permite supervisar y controlar las actividades de las instalaciones de sensores globales. Además de administrar los datos recibidos de los sensores implementados, la consola de administración local integra sin problemas los datos de varios recursos empresariales: CMDB, DNS, firewalls, API web y mucho más.

:::image type="content" source="media/concept-air-gapped-networks/site-management-alert-screen.png" alt-text="Pantalla de la consola de administración local":::

Le recomendamos que se familiarice con los conceptos, las funcionalidades y las características disponibles para los sensores antes de trabajar con la consola de administración local.

## <a name="integrations"></a>Integraciones

Puede ampliar las funcionalidades de Defender para IoT compartiendo la información de los dispositivos y las alertas con los sistemas asociados. Las integraciones ayudan a las empresas a enlazar soluciones de seguridad que anteriormente se encontraban en silos para mejorar significativamente la visibilidad de los dispositivos y la inteligencia sobre amenazas. Las integraciones también ayudan a las empresas a acelerar las respuestas en todo el sistema y a mitigar los riesgos con mayor rapidez. 

Las integraciones reducen la complejidad y eliminan los silos mediante su integración en la pila de seguridad y los flujos de trabajo de SOC existentes. Por ejemplo:

- SIEM, como IBM QRadar, Splunk, ArcSight, LogRhythm y RSA NetWitness

- Sistemas de orquestación de seguridad y vales, como ServiceNow e IBM Resilient

- Soluciones seguras de acceso remoto, como CyberArk Privileged Session Manager (PSM) y BeyondTrust

- Sistemas seguros de control de acceso a la red (NAC), como Aruba ClearPass y Forescout CounterACT

- Firewalls como Fortinet y Check Point

## <a name="complete-protocol-support"></a>Compatibilidad total con protocolos

Además de la compatibilidad con el protocolo incrustado, puede proteger los dispositivos IoT e ICS que ejecutan protocolos patentados y personalizados, o protocolos que se desvían de cualquier estándar. Con el SDK de Horizon Open Development Environment (ODE), los desarrolladores pueden crear complementos de disección que descodifican el tráfico de red según los protocolos definidos. Los servicios analizan el tráfico para ofrecer supervisión, alertas e informes completos. Use Horizon para:

- Ampliar la visibilidad y control sin necesidad de actualizar a nuevas versiones.

- Proteger la información propietaria mediante el desarrollo local como complemento externo.

- Localizar el texto de las alertas, eventos y parámetros del protocolo.

Además, puede usar alertas de protocolo propietario para comunicar la información:

- sobre las detecciones de tráfico basadas en protocolos y protocolos subyacentes en un complemento propietario de Horizon.

- sobre una combinación de campos de protocolo de todas las capas de protocolos. Por ejemplo, en un entorno que ejecuta MODBUS, puede que desee generar una alerta cuando el sensor detecta un comando de escritura en un registro de memoria en una dirección IP específica y un destino Ethernet. O bien, puede que desee generar una alerta cuando se realice cualquier acceso a una dirección IP específica.

Las alertas se desencadenan cuando se cumplen las condiciones de la regla de alertas de Horizon.

Además, el uso de alertas personalizadas de Horizon le permite escribir sus propios títulos y mensajes de alerta. Los campos y valores de protocolo resueltos se pueden insertar en el texto del mensaje de alerta.

El uso de la mensajería y activación de alertas personalizadas y basadas en condiciones ayuda a identificar la actividad de red específica y a actualizar de forma eficaz a los equipos operativos, de TI y de seguridad.


## <a name="high-availability"></a>Alta disponibilidad

Aumente la resistencia de la implementación de Defender para IoT mediante la instalación de un dispositivo de alta disponibilidad en la consola de administración local. Las implementaciones de alta disponibilidad garantizan que los sensores administrados informen continuamente a una consola de administración local activa.

Esta implementación se realiza con un par de la consola de administración local que incluye un dispositivo principal y otro secundario.

## <a name="localization"></a>Localización

Muchas características de la consola admiten una amplia gama de lenguajes.

## <a name="next-step"></a>Paso siguiente

[Introducción a Defender para IoT](getting-started.md)
