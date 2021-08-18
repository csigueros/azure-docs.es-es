---
title: Entender mejor las amenazas globales, regionales y locales
description: Conozca las amenazas globales, regionales y locales mediante el mapa del sitio de la consola de administración local.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: db3b9fbca9acd6c4ce1cfe137a4024f66d8a6292
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113020370"
---
# <a name="gain-insight-into-global-regional-and-local-threats"></a>Entender mejor las amenazas globales, regionales y locales

El mapa del sitio de la consola de administración local le ayuda a lograr una cobertura de seguridad completa al dividir la red en segmentos geográficos y lógicos que reflejan la topología de su empresa:

- **Nivel de instalación geográfica**: un sitio refleja un número de dispositivos agrupados según una ubicación geográfica presentada en el mapa. De forma predeterminada, Azure Defender para IoT le proporciona un mapa mundial. El mapa se actualiza para reflejar la estructura de la organización o la empresa. Por ejemplo, use un mapa que refleje los sitios de un determinado país, ciudad o campus industrial. Cuando el color del sitio cambia en el mapa, indica al equipo de SOC el estado crítico del sistema en la instalación.

  El mapa es interactivo y permite abrir cada sitio y profundizar en la información que contiene.

- **Capa lógica global**: una unidad de negocio es una manera de dividir su empresa en segmentos lógicos de acuerdo con sectores específicos. Al hacerlo, la topología de la empresa se refleja en el mapa.

  Por ejemplo, una empresa global que contiene fábricas de vidrio, de plástico y de automóviles se puede administrar como tres unidades de negocio diferentes. Un sitio físico ubicado en Toronto incluye tres líneas de producción de vidrio diferentes, una línea de producción de plástico y una línea de producción de motores de camión. Por lo tanto, este sitio tiene representantes de las tres unidades de negocio.

- **Nivel de región geográfica**: cree regiones para dividir una empresa global en regiones geográficas. Por ejemplo, la empresa que se describe podría usar las regiones Norteamérica, Europa Occidental y Europa Oriental. Norteamérica tiene fábricas de las tres unidades de negocio. Europa Occidental tiene fábricas de automóviles y fábricas de vidrio, y Europa Oriental solo tiene fábricas de plástico.

- **Nivel de segmento lógico local**: una zona es un segmento lógico dentro de un sitio que define, por ejemplo, un área funcional o una línea de producción. Trabajar con zonas permite el cumplimiento de las directivas de seguridad que son pertinentes para la definición de zona. Por ejemplo, un sitio que contenga cinco líneas de producción se puede segmentar en cinco zonas.

- **Nivel de vista local**: una vista local de la instalación de un solo sensor proporciona información sobre el estado operativo y de seguridad de los dispositivos conectados.

## <a name="work-with-site-map-views"></a>Trabajar con vistas de mapa del sitio

La consola de administración local proporciona una vista general de la red industrial en un mapa relacionado con el contexto. La vista de mapa general presenta el mapa global de la organización con la ubicación geográfica de cada sitio.

:::image type="content" source="media/how-to-work-with-maps/enterprise.png" alt-text="Captura de pantalla de la vista de mapa empresarial.":::

### <a name="color-coded-map-views"></a>Vistas de mapa codificadas por colores

**Verde**: el número de eventos de seguridad está por debajo del umbral que Defender para IoT ha definido para el sistema. No se requiere ninguna acción.

**Amarillo**: el número de eventos de seguridad es igual al umbral que Defender para IoT ha definido para el sistema. Considere la posibilidad de investigar los eventos.  

**Rojo**: el número de eventos de seguridad está por encima del umbral que Defender para IoT ha definido para el sistema. Emprenda acciones inmediatas.

### <a name="risk-level-map-views"></a>Vistas del mapa de niveles de riesgo

**Evaluación de riesgos**: la vista de evaluación de riesgos muestra información sobre los riesgos de los sitios. La información de riesgo le ayuda a clasificar por orden de prioridad la mitigación y a crear una hoja de ruta para planear mejoras de seguridad.

**Respuesta a los incidentes**: obtenga una vista centralizada de todas las alertas no confirmadas en cada sitio de la empresa. Puede explorar en profundidad y administrar las alertas detectadas en un sitio específico.

:::image type="content" source="media/how-to-work-with-maps/incident-responses.png" alt-text="Captura de pantalla de la vista de mapa empresarial con la respuesta a incidentes.":::

**Actividad malintencionada**: si se detectó malware, el sitio aparece en rojo. Esto indica que debe emprender acciones inmediatas.

:::image type="content" source="media/how-to-work-with-maps/malicious-activity.png" alt-text="Captura de pantalla de la vista de mapa empresarial con Actividad malintencionada.":::

**Alertas operativas**: esta vista de mapa para sistemas de OT proporciona una mejor comprensión de qué sistema de OT podría experimentar incidentes operativos, como paradas de PLC, carga de firmware y carga de programas.

:::image type="content" source="media/how-to-work-with-maps/operational-alerts.png" alt-text="Captura de pantalla de la vista de mapa empresarial con Alertas operativas.":::

Para elegir una vista de mapa:

1. Seleccione **Vista predeterminada** en el mapa.
2. Seleccione una vista.

:::image type="content" source="media/how-to-work-with-maps/map-views.png" alt-text="Captura de pantalla de la vista predeterminada del mapa del sitio.":::

## <a name="update-the-site-map-image"></a>Actualización de la imagen del mapa del sitio

Defender para IoT proporciona un mapa mundial predeterminado. Puede cambiarlo para que refleje su organización: un mapa de país o un mapa de ciudad, por ejemplo. 

Para reemplazar el mapa:

1. En el panel izquierdo, seleccione **Configuración del sistema**.

2. Seleccione **Change Site Map** (Cambiar mapa del sitio) y cargue el archivo gráfico para reemplazar el mapa predeterminado.

## <a name="next-step"></a>Paso siguiente

[Visualización de alertas](how-to-view-alerts.md)
