---
title: Alertas e incidentes de seguridad en Microsoft Defender for Cloud
description: Obtenga información sobre cómo Microsoft Defender for Cloud genera alertas de seguridad y las correlaciona con incidentes.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: memildin
ms.openlocfilehash: 6a22cc027e8abd631cb0c5b1a40e722d37225900
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475395"
---
# <a name="security-alerts-and-incidents-in-microsoft-defender-for-cloud"></a>Alertas e incidentes de seguridad en Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender for Cloud genera alertas para los recursos implementados en sus entornos locales y de nube híbrida de Azure.

Las alertas de seguridad se activan mediante detecciones avanzadas y solo están disponibles con las características de seguridad habilitadas. Puede actualizar desde la página **Configuración del entorno**, tal como se describe en [Inicio rápido: Habilitación de características de seguridad mejoradas](enable-enhanced-security.md). Hay disponible una versión de evaluación gratuita de 30 días. Para obtener información sobre los precios en la moneda de su elección y según su región, consulte la [página de precios](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-security-alerts-and-security-incidents"></a>¿Qué son las alertas de seguridad y los incidentes de seguridad? 

Las **alertas** son notificaciones que Defender for Cloud genera cuando detecta amenazas en los recursos. Defender for Cloud asigna prioridades y enumera las alertas, junto con la información necesaria para que pueda investigar rápidamente el problema. Defender for Cloud también proporciona los pasos detallados para ayudarlo a corregir los ataques. Las alertas se conservan durante 90 días.

Un **incidente de seguridad** es una recopilación de alertas relacionadas, en lugar de una enumeración de alertas individuales. Defender for Cloud usa la [correlación de alertas inteligentes en la nube (incidentes)](#cloud-smart-alert-correlation-incidents) para poner en correlación diferentes alertas y señales de baja fidelidad en incidentes de seguridad.

Utilizando los incidentes, Defender for Cloud proporciona una vista única de una campaña de ataques y todas las alertas relacionadas. Esta vista le permite comprender rápidamente las acciones que ha realizado el atacante y qué recursos se han visto afectados.

## <a name="respond-to-todays-threats"></a>Respuesta a las amenazas actuales 

En los últimos 20 años, las amenazas han cambiado considerablemente. Por lo general, en el pasado, las compañías solo tenían que preocuparse por las alteraciones que atacantes individuales realizaban en sus sitios web, quienes normalmente solo pretendían comprobar “qué es lo que eran capaces de hacer”. Hoy en día, los atacantes son mucho más sofisticados y están más organizados. Normalmente, se mueven por un fin económico o estratégico. También disponen de más recursos, ya que a veces podrían estar financiados por estados o por el crimen organizado.

Estas realidades cambiantes han propiciado un nivel de profesionalidad entre los atacantes que no tiene precedentes. Ya no les interesa alterar los sitios web. Ahora, lo que buscan es robar información, cuentas financieras y datos privados, que pueden usar con fines lucrativos en el mercado libre o para sacar provecho de una determinada situación política, militar o empresarial. Si los atacantes con un objetivo financiero son motivo de preocupación, aún lo son más los que pretenden abrir una brecha en las redes para dañar la infraestructura o infligir daños personales.

Como respuesta, las organizaciones suelen implementar varias soluciones específicas, que se centran en defender el perímetro o los puntos de conexión de la empresa mediante la búsqueda de firmas de ataque conocidas. Estas soluciones suelen generar un gran número de alertas con un bajo nivel de confiabilidad, lo que requiere que un analista de seguridad las cribe e investigue. La mayor parte de las organizaciones no disponen de la experiencia ni del tiempo necesarios para responder a estas alertas, por lo que muchas quedan desatendidas.  

Además, los atacantes han mejorado sus métodos para debilitar muchas de las defensas basadas en firmas y para [adaptarse a los entornos en la nube](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Por tanto, se necesitan nuevos enfoques que permitan identificar con mayor rapidez las nuevas amenazas, así como acelerar su detección y respuesta.


## <a name="continuous-monitoring-and-assessments"></a>Supervisión y evaluaciones continuas

Microsoft Defender for Cloud se beneficia de la existencia de equipos de científicos de datos e investigadores de seguridad de Microsoft que supervisan sin descanso los cambios que se registran en el terreno de las amenazas. Estos son algunas de las iniciativas que llevan a cabo:

* **Supervisión de la inteligencia sobre amenazas**: la inteligencia sobre amenazas incluye mecanismos, indicadores, implicaciones y notificaciones para las amenazas nuevas o existentes. Esta información se comparte con la comunidad de seguridad, y Microsoft supervisa sin descanso las fuentes de orígenes internos y externos.
* **Uso compartido de señales**: la información que recopilan los equipos de seguridad en la amplia cartera de servicios tanto locales como en la nube, de servidores y de dispositivos cliente de punto de conexión de Microsoft se comparte y se analiza.
* **Especialistas en seguridad de Microsoft** colaboración continua con equipos de Microsoft que trabajan en campos de seguridad especializados, como análisis forense y detección de ataques web.
* **Ajuste de la detección**: los algoritmos se ejecutan en conjuntos de datos de clientes reales y los investigadores de seguridad trabajan en conjunción con los clientes para validar los resultados. Los falsos positivos y los positivos verdaderos se utilizan para perfeccionar los algoritmos de aprendizaje automático.

Toda esta combinación de esfuerzos culmina en nuevas y mejoradas técnicas de detección, de las que puede beneficiarse al instante sin ninguna acción por su parte.

## <a name="how-does-defender-for-cloud-detect-threats"></a>¿Cómo detecta Defender for Cloud las amenazas? <a name="detect-threats"> </a>

Los investigadores de seguridad de Microsoft trabajan sin descanso para localizar amenazas. Dada nuestra presencia global en la nube y en sistemas locales, tenemos acceso a un amplio conjunto de recursos de telemetría. La amplitud y diversidad de estos conjuntos de datos nos permite detectar nuevos patrones y tendencias de ataque tanto en nuestros productos locales, destinados a particulares y empresas, como en nuestros servicios en línea. Como resultado, Defender for Cloud es capaz de actualizar rápidamente los algoritmos de detección a medida que los atacantes idean nuevas y más sofisticadas vulnerabilidades de seguridad. Este enfoque le ayuda a mantenerse al día en entornos llenos de amenazas que cambian continuamente.

Para detectar amenazas reales y reducir los falsos positivos, Defender for Cloud recopila, analiza e integra los datos de registro de los recursos de Azure y de la red. También funciona con soluciones de asociados conectados, como firewalls y soluciones de protección de puntos de conexión. Defender for Cloud analiza estos datos (a menudo, relacionando la información de diferentes orígenes) para identificar las amenazas.

![Presentación y recopilación de datos de Defender for Cloud.](./media/alerts-overview/security-center-detection-capabilities.png)

Defender for Cloud utiliza análisis avanzados que superan con creces los enfoques basados en firmas. Los grandes avances registrados en las tecnologías de macrodatos y [aprendizaje automático](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) se utilizan para evaluar eventos en todo el tejido de la nube, lo que permite detectar amenazas que no se podrían identificar mediante enfoques manuales, así como predecir la evolución de los ataques. Estas técnicas de análisis son:

- **Información integrada sobre amenazas**: Microsoft dispone de una ingente cantidad de información sobre amenazas globales. Los recursos telemétricos proceden de diferentes fuentes, como Azure, Microsoft 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, la Unidad de crímenes digitales de Microsoft (DCU) y Microsoft Security Response Center (MSRC). Los investigadores también reciben información sobre amenazas que comparten con los principales proveedores de servicios en la nube y que procede de fuentes de otros terceros. Microsoft Defender for Cloud puede usar todos estos datos para avisarle de las amenazas procedentes de actores malintencionados conocidos.

- **Análisis del comportamiento**: El análisis del comportamiento es una técnica que analiza datos y los compara con una serie de patrones conocidos. No obstante, estos patrones no son simples firmas, sino que están determinados por unos complejos algoritmos de aprendizaje automático que se aplican a conjuntos de datos masivos. También se determinan por medio de un análisis cuidadoso, llevado a cabo por analistas expertos, de los comportamientos malintencionados. Microsoft Defender for Cloud puede utilizar el análisis del comportamiento para identificar recursos en peligro a partir del análisis de registros de las máquinas virtuales, registros de los dispositivos de redes virtuales, registros de los tejidos y otros orígenes.

- **Detección de anomalías**: Microsoft Defender para Cloud también usa la detección de anomalías para identificar amenazas. A diferencia del análisis del comportamiento, que depende de patrones conocidos obtenidos a partir de grandes conjuntos de datos, la detección de anomalías es una técnica más "personalizada" y se basa en referencias que son específicas de las implementaciones. El aprendizaje automático se aplica para determinar la actividad normal de las implementaciones. A partir de ahí, se generan reglas para definir condiciones de valores atípicos que podrían constituir un evento de seguridad.

## <a name="how-are-alerts-classified"></a>¿Cómo se clasifican las alertas?

Defender for Cloud asigna una gravedad a las alertas, que le ayudan a priorizar el orden en el que asiste a cada alerta, de modo que cuando un recurso está en peligro, puede obtenerlo inmediatamente. La gravedad se basa en la confianza que tiene Defender for Cloud en el hallazgo o en el análisis utilizados para emitir la alerta, así como en el nivel de confianza de que ha habido un intento malintencionado detrás de la actividad que ha provocado la alerta.

> [!NOTE]
> La gravedad de las alertas aparece de manera diferente en el portal y en las versiones de la API REST anteriores a 01-01-2019. Si usa una versión anterior de la API, actualícela para conseguir la experiencia coherente que se describe a continuación.

| severity          | Respuesta recomendada                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Alta**          | hay una probabilidad elevada de que el recurso esté en peligro. Debe investigarse de inmediato. El grado de certeza de Defender for Cloud sobre la mala intención de la acción y los hallazgos utilizados para emitir la alerta es elevado. Una alerta de este tipo sería podría detectar la ejecución de una herramienta malintencionada conocida; por ejemplo, Mimikatz, una herramienta que se usa habitualmente para robar credenciales.                                                                                                           |
| **Media**        | es probable que sea una actividad sospechosa que podría indicar que un recurso está en peligro. El grado de certeza de Defender for Cloud sobre el análisis o los hallazgos es medio, mientras que el grado de certeza sobre la mala intención es medio o alto. Suelen tratarse de detecciones basadas en anomalías o aprendizaje automático. Por ejemplo, un intento de inicio de sesión desde una ubicación anómala.                                                                                                            |
| **Baja**           | podría tratarse de un hallazgo benigno o de un ataque bloqueado. Defender for Cloud no tiene la certeza suficiente de que la intención fuera mala y la actividad pudiera ser inofensiva. Por ejemplo, borrar un registro es una acción que podría producirse si un atacante intenta ocultar sus huellas, pero en muchos casos es una operación rutinaria que realizan los administradores. Por lo general, Defender for Cloud no avisa cuando se bloquean ataques a menos que se considere un caso interesante que convenga examinar. |
| **Informational** (Informativo) | Normalmente, las incidencias se componen de varias alertas, algunas de las cuales podrían parecer meramente informativas, aunque a tenor de otras alertas podría ser conveniente investigarlas.                                                                                                                                                                                                                                                               |
|                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |

## <a name="export-alerts"></a>Exportación de alertas

Tiene una variedad de opciones para ver las alertas fuera de Defender for Cloud, entre ellas:

- La **descarga del informe de CSV** en el panel de alertas proporciona una exportación única a CSV.
- La **exportación continua** de la configuración del entorno permite configurar flujos de alertas de seguridad y recomendaciones a áreas de trabajo de Log Analytics y a Event Hubs. [Más información sobre la exportación continua](continuous-export.md).
- **El conector de Microsoft Sentinel** transmite las alertas de seguridad de Microsoft Defender for Cloud a Microsoft Sentinel. [Obtenga más información sobre cómo conectar Microsoft Defender for Cloud con Microsoft Sentinel](../sentinel/connect-azure-security-center.md).

Obtenga información sobre todas las opciones de exportación en [Transmisión de alertas a una solución de administración de servicios de TI, SIEM o SOAR](export-to-siem.md) y [Exportación continua de datos de Defender for Cloud](continuous-export.md).

## <a name="cloud-smart-alert-correlation-incidents"></a>Correlación de alertas inteligentes en la nube (incidentes)

Microsoft Defender for Cloud analiza continuamente las cargas de trabajo de la nube híbrida mediante el análisis avanzado e información sobre amenazas para alertarle de cualquier actividad malintencionada.

La magnitud de la cobertura de las amenazas está creciendo. La necesidad de detectar incluso el peligro más ligero es importante y puede ser difícil para los analistas de seguridad evaluar las distintas alertas e identificar un ataque real. Defender for Cloud ayuda a los analistas a afrontar este exceso de alertas. Ayuda a diagnosticar los ataques a medida que se producen, al correlacionar las distintas alertas y señales de baja fidelidad en incidentes de seguridad.

El análisis de fusión es la tecnología y el back-end analítico que permite generar los incidentes de Defender for Cloud, al poner en correlación diferentes alertas y señales contextuales entre sí. La fusión examina las distintas señales informadas en una suscripción en todos los recursos. La fusión encuentra patrones que revelan la progresión de los ataques o las señales con información contextual compartida, lo que indica que debe utilizar un procedimiento de respuesta unificado para ellos.

El análisis de fusión combina el conocimiento de dominios de seguridad con inteligencia artificial para analizar las alertas y detectar nuevos patrones de ataque cuando se producen. 

Defender for Cloud utiliza la matriz de ataque MITRE para asociar alertas con su intención percibida, lo que ayuda a formalizar el conocimiento de dominios de seguridad. Además, mediante el uso de la información recopilada para cada paso de un ataque, Defender for Cloud puede descartar actividades que parecen ser pasos de un ataque, pero en realidad no lo son.

Dado que los ataques se producen a menudo a través de distintos inquilinos, Defender for Cloud puede combinar algoritmos de inteligencia artificial para analizar las secuencias de ataque que se notifican en cada suscripción. Esta técnica identifica las secuencias de ataque como patrones de alerta más frecuentes, en lugar de asociarse simplemente entre sí.

Durante la investigación de un incidente, los analistas a menudo necesitan más contexto para llegar a un veredicto sobre la naturaleza de la amenaza y cómo mitigarla. Por ejemplo, incluso cuando se detecta una anomalía de red, sin saber qué más sucede en la red o con respecto al recurso objetivo, es difícil determinar las medidas que se deben tomar a continuación. En estos casos, pueden ser de utilidad los artefactos, los eventos relacionados y la información que un incidente de seguridad puede incluir. La información adicional disponible de los incidentes de seguridad variará según el tipo de amenaza detectada y la configuración de su entorno. 

> [!TIP]
> Para obtener una lista de alertas de incidentes de seguridad que se pueden producir en el análisis de fusión, consulte la [tabla de referencia de alertas](alerts-reference.md#alerts-fusion).

:::image type="content" source="./media/alerts-overview/security-incident.png" alt-text="Captura de pantalla del informe de incidentes de seguridad detectados":::

Para administrar los incidentes de seguridad, consulte [Cómo administrar los incidentes de seguridad en Microsoft Defender for Cloud](incidents.md).

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido obtenido información sobre los distintos tipos de alertas disponibles en Defender for Cloud. Para más información, consulte:

- [Alertas de seguridad en el registro de actividad de Azure](https://go.microsoft.com/fwlink/?linkid=2114113): además de estar disponibles en Azure Portal o mediante programación, las alertas y los incidentes de seguridad se auditan como eventos en el registro de actividad de Azure
- [Tabla de referencia de alertas de Defender for Cloud](alerts-reference.md)
- [Responder a alertas de seguridad](managing-and-responding-alerts.md#respond-to-security-alerts)