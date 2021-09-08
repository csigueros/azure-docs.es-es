---
title: ¿Qué son las plantillas de aplicación en Azure IoT Central | Microsoft Docs
description: Las plantillas de aplicación de Azure IoT Central permiten pasar al desarrollo de soluciones de IoT.
author: ankitscribbles
ms.author: ankitgup
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 26f9c41b23353343c5533c83db59320698824233
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123470893"
---
# <a name="what-are-application-templates"></a>¿Qué son las plantillas de aplicación?

Las plantillas de aplicación de Azure IoT Central son una herramienta que ayuda a poner en marcha el desarrollo de las soluciones de IoT. Puede usar las plantillas de aplicación para todo; para tener una idea de lo que es posible, o para realizar la personalización completa de la aplicación para su reventa a los clientes.

Las plantillas de aplicación constan de:

- Paneles de ejemplo
- Plantillas de dispositivo de ejemplo
- Dispositivos simulados que producen datos en tiempo real
- Reglas y trabajos preconfigurados
- Documentación enriquecida que incluye tutoriales y procedimientos

Seleccione la plantilla de aplicación al crear la aplicación. No puede cambiar la plantilla una vez creada la aplicación.

## <a name="custom-templates"></a>Plantillas personalizadas

Si quiere crear la aplicación desde cero, elija la plantilla **Aplicación personalizada**. El identificador de la plantilla de aplicación personalizada es `iotc-pnp-preview`.

## <a name="industry-focused-templates"></a>Plantillas centradas en el sector

Azure IoT Central es una plataforma de aplicaciones independiente del sector. Las plantillas de aplicación son ejemplos centrados en el sector disponibles para estos sectores actualmente:

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="connected-logistics"></a>Logística conectada

Se espera que el gasto logístico global alcance los 10,6 billones de dólares en 2020. El transporte de mercancías representa la mayor parte de este gasto. Los proveedores de servicios de envío están sujetos a restricciones y sufren una gran presión debido a la fuerte competencia.

Use los sensores de IoT para recopilar y supervisar las condiciones ambientales tales como temperatura, humedad, inclinación, golpes, luz y ubicación de un envío. Puede combinar la telemetría recopilada de sensores y dispositivos de IoT con otros orígenes de datos, como información meteorológica y de tráfico, en sistemas de inteligencia empresarial basados en la nube.

Entre las ventajas de la solución de logística conectada se incluyen:

* Supervisión del envío con seguimiento y trazabilidad en tiempo real 
* Integridad del envío con supervisión de las condiciones ambientales en tiempo real
* Seguridad contra los robos, las pérdida o el daño de los envíos
* Geovallado, optimización de rutas, administración de flotas y análisis de vehículos
* Previsión para la salida y llegada predecibles de los envíos

Las siguientes capturas de pantallas muestran el panel integrado en la plantilla de aplicación. El panel es totalmente personalizable para satisfacer los requisitos específicos de la solución:

:::image type="content" source="media/concepts-app-templates/connected-logistics-dashboard-1.png" alt-text="Captura de pantalla que muestra la mitad superior del panel de operaciones de la aplicación de logística conectada.":::

:::image type="content" source="media/concepts-app-templates/connected-logistics-dashboard-2.png" alt-text="Captura de pantalla que muestra la mitad inferior del panel de operaciones de la aplicación de logística conectada.":::

Para más información, consulte el tutorial para [implementar y recorrer la plantilla de aplicación de logística conectada](../retail/tutorial-iot-central-connected-logistics.md).

## <a name="digital-distribution-center"></a>Centro de distribución digital

A medida que los fabricantes y minoristas establecen su presencia en todo el mundo, sus cadenas de suministro se diversifican y se vuelven más complejas. Ahora, los consumidores esperan que haya grandes selecciones de productos disponibles y que las mercancías lleguen uno o dos días después de haberlas comprado. Los centros de distribución deben adaptarse a estas tendencias y, al mismo tiempo, superar las ineficiencias existentes.

En la actualidad, una dependencia del trabajo manual implica que la selección y el empaquetado supone entre el 55 y el 65 % de los costos de un centro de distribución. La selección y el empaquetado manual también son más lentos que los sistemas automatizados y las necesidades de personal que fluctúa rápidamente hacen aún más difícil cumplir con los volúmenes de envío. Esta fluctuación estacional da como resultado una gran rotación del personal y aumenta la probabilidad de que se produzcan errores costosos.

Las soluciones basadas en cámaras habilitadas para IoT pueden ofrecer ventajas transformacionales, ya que habilitan un bucle de comentarios digitales. Los datos de todo el centro de distribución genera conocimientos prácticos que, a su vez, dan como resultado mejores datos.

Entre las ventajas de un centro de distribución digital se incluyen las siguientes:

* Cámaras que supervisan las mercancías a medida que llegan y pasan por el sistema transportador
* Identificación automática de las mercancías defectuosas
* Seguimiento de pedidos eficaz
* Costos reducidos, mayor productividad y uso optimizado

En la captura de pantalla siguiente se muestra el panel integrado en la plantilla de aplicación. El panel es totalmente personalizable para satisfacer los requisitos específicos de la solución: 

:::image type="content" source="media/concepts-app-templates/digital-distribution-center-dashboard.png" alt-text="Panel Digital Distribution Center":::

Para más información, consulte el tutorial para [implementar y recorrer la plantilla de aplicación del centro de distribución digital](../retail/tutorial-iot-central-digital-distribution-center.md).

## <a name="in-store-analytics---condition-monitoring"></a>Análisis en el almacén: supervisión de condiciones

Para muchos minoristas, las condiciones ambientales dentro de sus almacenes son un diferenciador clave de sus competidores. Los minoristas quieren mantener condiciones agradables en sus almacenes para el beneficio de sus clientes.  

Puede usar la plantilla de aplicación de supervisión de la condición del análisis en el almacén de IoT Central para crear una solución integral. La plantilla de aplicación le permite conectarse digitalmente al entorno de un comercio minorista, y a supervisarlo, mediante diferentes tipos de dispositivos sensores. Estos dispositivos sensores generan telemetría que se puede convertir en información empresarial, lo que ayuda al minorista a reducir los costos operativos y crear una gran experiencia para sus clientes.

Utilice la plantilla de aplicación para:

* Conectar diferentes tipos de sensores de IoT a una instancia de la aplicación de IoT Central.
* Supervisar y administrar el estado de la red de sensores, y los dispositivos de puerta de enlace del entorno.
* Crear reglas personalizadas basadas en las condiciones del entorno de un almacén para desencadenar las alertas para los administradores del almacén.
* Transformar las condiciones del entorno del almacén en información que el equipo del almacén del comercio minorista pueda usar para mejorar la experiencia del cliente.
* Exportar la información agregada a aplicaciones empresariales nuevas o existentes que dan al personal del comercio minorista información útil u oportuna.

La plantilla de aplicación incluye un conjunto de plantillas de dispositivo y usa un conjunto de dispositivos simulados para rellenar el panel. 

En la captura de pantalla siguiente se muestra el panel integrado en la plantilla de aplicación. El panel es totalmente personalizable para satisfacer los requisitos específicos de la solución: 

:::image type="content" source="media/concepts-app-templates/in-store-analytics-condition-dashboard.png" alt-text="In-Store Analytics Condition Monitoring":::

Para más información, consulte el tutorial para [crear una aplicación de análisis en el almacén en Azure IoT Central](../retail/tutorial-in-store-analytics-create-app.md).

## <a name="in-store-analytics---checkout"></a>Análisis en el almacén: finalización de la compra

Para algunos minoristas, la experiencia de la finalización de la compra dentro de sus almacenes es un elemento diferenciador clave frente a la competencia. Los minoristas desean ofrecer una experiencia de finalización de la compra fluida en sus almacenes para animar a los clientes a volver.  

Puede usar la plantilla de aplicación de finalización de la compra de análisis en el almacén de IoT Central para crear una solución que proporcione información de la zona de finalización de la compra de un almacén al personal minorista. Por ejemplo, los sensores pueden proporcionar información sobre las longitudes de cola y el promedio de tiempo de espera para cada carril de finalización de la compra.

Utilice la plantilla de aplicación para:

* Conectar diferentes tipos de sensores de IoT a una instancia de la aplicación de IoT Central.
* Supervisar y administrar el estado de la red de sensores, y los dispositivos de puerta de enlace del entorno.
* Crear reglas personalizadas basadas en el estado de la finalización de la compra en un almacén para desencadenar las alertas para el personal de ventas minorista.
* Transformar el estado de la finalización de la compra en el almacén en información que pueda usar el equipo del almacén minorista para mejorar la experiencia del cliente.
* Exportar la información agregada a aplicaciones empresariales nuevas o existentes que dan al personal del comercio minorista información útil u oportuna.

La plantilla de aplicación incluye un conjunto de plantillas de dispositivo y usa un conjunto de dispositivos simulados para rellenar el panel con los datos de ocupación del carril. 

En la captura de pantalla siguiente se muestra el panel integrado en la plantilla de aplicación. El panel es totalmente personalizable para satisfacer los requisitos específicos de la solución: 

:::image type="content" source="media/concepts-app-templates/In-Store-Analytics-Checkout-Dashboard.png" alt-text="In-Store Analytics Checkout":::

Para más información, consulte el tutorial para [crear una aplicación de análisis en el almacén en Azure IoT Central](../retail/tutorial-in-store-analytics-create-app.md).

## <a name="smart-inventory-management"></a>Administración inteligente del inventario

El inventario son las existencias de mercancías que mantiene un minorista. La administración del inventario es fundamental para asegurarse de que el producto correcto se encuentra en el lugar adecuado en el momento preciso. El minorista debe equilibrar los costos de almacenar demasiado inventario con los costos de no tener suficientes artículos en existencia para satisfacer la demanda.

Los datos de IoT generados a partir de etiquetas de identificación de radiofrecuencia (RFID), señalizaciones y cámaras ofrecen oportunidades para mejorar los procesos de administración del inventario. Puede combinar la telemetría recopilada de sensores y dispositivos de IoT con otros orígenes de datos, como información meteorológica y de tráfico, en sistemas de inteligencia empresarial basados en la nube.

Las ventajas de la administración inteligente del inventario son las siguientes:

* Reducción del riesgo de que los artículos estén agotados y garantía del nivel de servicio deseado por el cliente. 
* Análisis exhaustivo e información sobre la exactitud del inventario casi en tiempo real.
* Herramientas para ayudar a decidir la cantidad adecuada de inventario para atender los pedidos de los clientes.

Esta plantilla de aplicación se centra en la conectividad de dispositivos y en la configuración y administración de dispositivos de lectura RFID y Bluetooth de bajo consumo (BLE).

En la captura de pantalla siguiente se muestra el panel integrado en la plantilla de aplicación. El panel es totalmente personalizable para satisfacer los requisitos específicos de la solución:

:::image type="content" source="media/concepts-app-templates/smart-inventory-management-dashboard.png" alt-text="Panel de Smart Inventory Management":::

Para más información, consulte el tutorial para [implementar y recorrer la plantilla de aplicación de administración inteligente](../retail/tutorial-iot-central-smart-inventory-management.md).

## <a name="micro-fulfillment-center"></a>Centro de micrologística

En el panorama cada vez más competitivo del comercio minorista, los minoristas se enfrentan constantemente a la presión de cerrar la brecha entre la demanda y la satisfacción. Una nueva tendencia que ha surgido para satisfacer la creciente demanda de consumidores es alojar el inventario cerca de los clientes finales y de los almacenes que visitan.

La plantilla de aplicación del centro de micrologística de IoT Central le permite supervisar y administrar todos los aspectos de sus centros logísticos totalmente automatizados. La plantilla incluye un conjunto de sensores de supervisión de condiciones simuladas y operadores robóticos para acelerar el proceso de desarrollo de la solución. Dichos dispositivos capturan señales significativas que se pueden convertir en información empresarial que permita al comercio minorista reducir sus costos de explotación y crear experiencias para sus clientes.

La plantilla de aplicación le permite: 

- Conectar sin problemas diferentes tipos de sensores de IoT como robots o sensores de supervisión de condiciones a una instancia de la aplicación IoT Central.
- Supervisar y administrar el estado de la red de sensores, y los dispositivos de puerta de enlace del entorno.
- Crear reglas personalizadas basadas en las condiciones ambientales en un centro logístico para desencadenar las alertas apropiadas.
- Transformar las condiciones ambientales en el centro logístico en información que el equipo del almacén minorista pueda aprovechar.
- Exportar la información agregada a aplicaciones empresariales nuevas o existentes en beneficio de los miembros del personal del comercio minorista.

En la captura de pantalla siguiente se muestra el panel integrado en la plantilla de aplicación. El panel es totalmente personalizable para satisfacer los requisitos específicos de la solución:

:::image type="content" source="media/concepts-app-templates/MFC-Dashboard.png" alt-text="Centro de micrologística":::

Para más información, consulte el tutorial para [implementar y recorrer la plantilla de aplicación del centro de micrologística](../retail/tutorial-micro-fulfillment-center.md).

## <a name="video-analytics---object-and-motion-detection"></a>Análisis de vídeo: detección de objetos y movimiento

La plantilla de la aplicación de *análisis de vídeo de IoT Central: detección de objetos y movimiento* le permite experimentar rápidamente cómo implementar, administrar y supervisar una solución que usa cámaras de inteligencia perimetral para detectar objetos y movimiento.

La aplicación de análisis de vídeo usa un módulo de [Live Video Analytics (LVA)](#live-video-analytics) que se ejecuta en IoT Edge. El módulo de LVA le proporciona una plataforma para crear aplicaciones de vídeo inteligentes que abarcan el perímetro y la nube. Puede usar la plataforma para mejorar las soluciones de IoT, como la aplicación de análisis de vídeo con detección de objetos y movimiento.

La plantilla de la aplicación incluye cuatro paneles de la aplicación:

* **Introducción** proporciona vínculos a recursos que le ayudarán a empezar a usar la plantilla de aplicación.

- En el **Panel de demostración** se proporciona una ilustración de los tipos de información que se pueden mostrar desde las cámaras conectadas.
- **Administración de cámaras reales (ejemplo)** usa cámaras simuladas para mostrar cómo se pueden administrar las cámaras desde la aplicación.
- **Supervisión de cámaras reales (ejemplo)** usa cámaras simuladas para mostrar cómo se pueden supervisar las cámaras desde la aplicación.

:::image type="content" source="media/concepts-app-templates/live-video-analytics.png" alt-text="Análisis de vídeo: detección de objetos y movimiento":::

Para aprender a implementar la solución, consulte el tutorial [Creación de una aplicación de análisis de vídeo en Azure IoT Central](../retail/tutorial-video-analytics-deploy.md).

### <a name="live-video-analytics"></a>Live Video Analytics

[Live Video Analytics](https://github.com/Azure/live-video-analytics) proporciona una plataforma que le permite crear aplicaciones de vídeo inteligentes que abarcan el perímetro y la nube. La plataforma ofrece la funcionalidad de capturar, grabar, analizar vídeo en directo y publicar los resultados, que podrían ser análisis de vídeo o vídeo, en los servicios de Azure. Los servicios de Azure podrían ejecutarse en la nube o en el perímetro. Puede usar la plataforma para mejorar las soluciones de IoT con análisis de vídeo.

## <a name="smart-meter-monitoring"></a>Supervisión de medidores inteligentes

 Los medidores inteligentes no solo permiten la facturación automática, sino que también casos de uso de medición avanzada, como las lecturas en tiempo real y la comunicación bidireccional. La plantilla de aplicaciones de medidores inteligentes permite a los servicios públicos y los asociados supervisar el estado y los datos de los medidores inteligentes, así como definir alarmas y notificaciones. Proporciona comandos de muestra, como, por ejemplo, para desconectar el medidor y actualizar el software. Los datos del medidor se pueden configurar para que se dirijan a otras aplicaciones empresariales y para desarrollar soluciones personalizadas. 

Funcionalidades clave de la aplicación:

- Ejemplo de un modelo de dispositivo de medidor
- Información del medidor y estado activo
- Lecturas de los medidores, como energía, alimentación y voltajes
- Ejemplos de comandos de medidor 
- Visualizaciones y paneles integrados
- Extensibilidad para el desarrollo de soluciones personalizadas

Puede probar la [aplicación de supervisión de medidores inteligentes de forma gratuita](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring) sin ninguna suscripción a Azure y sin compromiso.

Después de implementar la aplicación, verá los datos de medición simulados en el panel, tal como se muestra en la ilustración siguiente. Esta plantilla es una aplicación de ejemplo que puede extender y personalizar fácilmente para sus casos de uso específicos.

:::image type="content" source="media/concepts-app-templates/smart-meter-app-dashboard.png" alt-text="Panel de la aplicación de medidores inteligentes":::

## <a name="solar-panel-monitoring"></a>Supervisión de paneles solares

La aplicación de supervisión de paneles solar permite a los servicios públicos y los asociados supervisar paneles solares, como su estado de conexión y de generación de energía casi en tiempo real. Puede enviar notificaciones basadas en criterios de umbral definidos. Proporciona comandos de ejemplo, como, por ejemplo, para actualizar el firmware y otras propiedades. Los datos de los paneles solares se pueden configurar para que se dirijan a otras aplicaciones empresariales y para desarrollar soluciones personalizadas. 

Funcionalidades clave de la aplicación: 

- Ejemplo de un modelo de dispositivo de paneles solares 
- Información sobre paneles solares y estado activo
- Generación de la energía solar y otras lecturas
- Ejemplos de comandos y controles
- Visualizaciones y paneles integrados
- Extensibilidad para el desarrollo de soluciones personalizadas

Puede probar la [aplicación de supervisión de paneles solares de forma gratuita](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring) sin ninguna suscripción a Azure y sin compromiso.

Después de implementar la aplicación, verá los datos de los paneles solares simulados en un plazo de 1 o 2 minutos, tal como se muestra en el panel siguiente. Esta plantilla es una aplicación de ejemplo que puede extender y personalizar fácilmente para sus casos de uso específicos. 

:::image type="content" source="media/concepts-app-templates/solar-panel-app-dashboard.png" alt-text="Panel de la aplicación de paneles solares":::

## <a name="water-quality-monitoring"></a>Supervisión de la calidad del agua

La supervisión de la calidad del agua tradicional se basa en técnicas de muestreo manual y análisis de laboratorio de campo, lo que lleva mucho tiempo y dinero. Mediante la supervisión remota de la calidad del agua en tiempo real, se pueden administrar los problemas de calidad del agua antes de que los ciudadanos resulten afectados. Además, con el análisis avanzado, los servicios de agua y los organismos ambientales pueden actuar rápidamente si se aparecen problemas de calidad del agua y planear por adelantado el tratamiento que se da al agua.  

La aplicación Water Quality Monitoring es una plantilla de aplicación de IoT Central que le ayuda a iniciar el desarrollo de su solución de IoT y permite que los servicios de agua supervisen digitalmente la calidad del agua en las ciudades inteligentes.

:::image type="content" source="media/concepts-app-templates/water-quality-monitoring-dashboard-full.png" alt-text="Plantilla de la aplicación Water Quality Monitoring":::

La plantilla de la aplicación consta de:

- Paneles de ejemplo
- Plantillas de dispositivos de supervisión de la calidad del agua de ejemplo
- Dispositivos de supervisión de la calidad del agua simulados
- Reglas y trabajos preconfigurados
- Personalización de marca mediante etiquetado en blanco 

Empiece a trabajar con el tutorial de la aplicación [Water Quality Monitoring](../government/tutorial-water-quality-monitoring.md).

## <a name="water-consumption-monitoring"></a>Supervisión del consumo de agua

Tradicionalmente, para realizar el seguimiento del consumo de agua las empresas distribuidoras de agua leen manualmente los contadores de consumo de agua en los sitios de medición. Cada vez más ciudades están reemplazando los contadores tradicionales por contadores inteligentes avanzados, lo que permite la supervisión remota del consumo y el control remoto de las válvulas que controlan el flujo de agua. La supervisión del consumo de agua, junto con el mensaje de comentarios digitales al ciudadano, pueden aumentar la concienciación y reducir el consumo de agua. 

La aplicación Water Consumption Monitoring es una plantilla de aplicación de IoT Central que le ayuda a iniciar el desarrollo de su solución de IoT para que los servicios de agua y las ciudades supervisen y controlen de forma remota el flujo de agua para reducir el consumo. 

:::image type="content" source="media/concepts-app-templates/water-consumption-monitoring-dashboard-full.png" alt-text="Plantilla de la aplicación Water Consumption Monitoring":::

La plantilla de la aplicación Water Consumption Monitoring consta de varios elementos preconfigurados:

- Paneles de ejemplo
- Plantillas de dispositivos de supervisión de la calidad del agua de ejemplo
- Dispositivos de supervisión de la calidad del agua simulados
- Reglas y trabajos preconfigurados
- Personalización de marca mediante etiquetado en blanco

 Empiece a trabajar con el tutorial de la aplicación [Water Consumption Monitoring](../government/tutorial-water-consumption-monitoring.md).

## <a name="connected-waste-management"></a>Administración de residuos conectada 

La aplicación Connected Waste Management es una plantilla de aplicación de IoT Central que le ayuda a iniciar el desarrollo de su solución de IoT para que las ciudades inteligentes realicen una supervisión remota para maximizar la recogida eficaz de residuos. 

:::image type="content" source="media/concepts-app-templates/connected-waste-management-dashboard.png" alt-text="Plantilla de la aplicación Connected Waste Management":::

La plantilla de la aplicación Connected Waste Management consta de los siguientes elementos preconfigurados:

- Paneles de ejemplo
- Plantillas de dispositivos de cubo de basura conectados
- Dispositivos de cubo de basura conectados
- Reglas y trabajos preconfigurados
- Personalización de marca mediante etiquetado en blanco 

Empiece a trabajar con el [tutorial de la aplicación Connected Waste Management](../government/tutorial-connected-waste-management.md).

## <a name="continuous-patient-monitoring"></a>Supervisión continua de pacientes 

En el espacio de salud de IoT, la supervisión continua de pacientes es uno de los principales facilitadores a la hora de reducir el riesgo de readmisiones, administrar las enfermedades crónicas de un modo más eficaz y mejorar los resultados de los pacientes. La supervisión continua de pacientes puede dividirse en dos categorías principales:

1. **Supervisión en el paciente**: Con ponibles médicos y otros dispositivos en el hospital, los equipos de atención sanitaria pueden supervisar las señales vitales de los pacientes y sus condiciones médicas sin tener que enviar a una enfermera a visitar a un paciente varias veces al día. Los equipos de atención médica pueden detectar el momento en que un paciente necesita atención crítica mediante notificaciones y priorizar su tiempo de forma eficaz.
1. **Supervisión remota de pacientes**: Al usar dispositivos ponibles y resultados notificados por los pacientes (PRO) para supervisar a los pacientes fuera del hospital, se puede reducir el riesgo de nuevo ingreso hospitalario. Se pueden recopilar datos de pacientes con enfermedades crónicas y en rehabilitación para garantizar que los pacientes siguen los planes de atención médica y que las alertas del empeoramiento de los pacientes pueden llegar a los equipos de atención antes de ser críticas.

Esta plantilla de aplicación se puede usar para crear soluciones para ambas categorías de supervisión continua de pacientes. Entre las ventajas se incluye lo siguiente:

- Conectar fácilmente distintos tipos de dispositivos médicos ponibles a una instancia de IoT Central.
- Supervisar y administrar los dispositivos para asegurarse de que están en buen estado.
- Crear reglas personalizadas para los datos de los dispositivos para desencadenar las alertas adecuadas.
- Exportar los datos de estado de los pacientes a Azure API for FHIR, un almacén de datos compatible.
- Exportar la información agregada a las aplicaciones empresariales nuevas o existentes.

:::image type="content" source="media/concepts-app-templates/in-patient-dashboard.png" alt-text="CPM-dashboard":::


## <a name="next-steps"></a>Pasos siguientes

Ahora que sabe lo que son las plantillas de aplicación de IoT Central, empiece por [crear una aplicación de IoT Central](quick-deploy-iot-central.md).
