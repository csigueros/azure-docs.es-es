---
title: Investigación de incidentes con Azure Sentinel | Microsoft Docs
description: En este artículo, aprenderá a usar Azure Sentinel para crear reglas de alerta avanzadas que generan incidentes que puede asignar e investigar.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2021
ms.author: yelevin
ms.openlocfilehash: e688216668c1ccc6c660c9c37d9e432f974bdb76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780118"
---
# <a name="investigate-incidents-with-azure-sentinel"></a>Investigación de incidentes con Azure Sentinel

> [!IMPORTANT]
> Actualmente, las características indicadas están en VERSIÓN PRELIMINAR. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

Este artículo le ayuda a investigar incidentes con Azure Sentinel. Después de conectar los orígenes de datos a Azure Sentinel, querrá recibir una notificación cuando suceda algo sospechoso. Para ello, Azure Sentinel le permite crear reglas de alerta avanzadas, que generan incidentes que se pueden asignar e investigar.

En este artículo se describe:
> [!div class="checklist"]
> * Investigación de incidentes
> * Uso del gráfico de investigación
> * Respuesta a amenazas

Un incidente puede incluir varias alertas, a modo de agregado de todas las pruebas relevantes en una investigación en concreto. Los incidentes se crearán en función de las reglas de análisis que haya creado en la página **Análisis**. Las propiedades relacionadas con alertas, como la gravedad y el estado, se establecen en el nivel de incidente. Después de indicar a Azure Sentinel qué tipos de amenazas está buscando y cómo detectarlas, puede supervisar las amenazas que se detecten investigando cada incidente.

## <a name="prerequisites"></a>Requisitos previos

- Solo podrá investigar el incidente si ha usado los campos de asignación de entidades al configurar la regla de análisis. El gráfico de investigación requiere que el incidente original incluya entidades.

- Si tiene un usuario invitado que necesita asignar incidentes, al usuario se le debe asignar el rol [Lector de directorio](../active-directory/roles/permissions-reference.md#directory-readers) en el inquilino de Azure AD. Los usuarios normales (no invitados) tienen asignado este rol de forma predeterminada.

## <a name="how-to-investigate-incidents"></a>Investigación de incidentes

1. Seleccione **Incidentes**. La página **Incidentes** le permite saber cuántos incidentes tiene, cuántos están abiertos, cuántos están establecidos en **En curso** y cuántos se han cerrado. De cada incidente puede ver la hora a la que tuvo lugar y su estado. Valore la gravedad para decidir qué incidentes abordar primero.

    ![Ver gravedad del incidente](media/tutorial-investigate-cases/incident-severity.png)

1. Puede filtrar los incidentes según sea necesario, por ejemplo, por estado o gravedad. Para más información, consulte [Búsqueda de incidentes](#search-for-incidents).

1. Para iniciar una investigación, seleccione un incidente específico. A la derecha, puede ver información detallada del incidente, como la gravedad o un resumen del número de entidades implicadas, los eventos sin procesar que desencadenaron este incidente y el identificador único del mismo.

1. Para ver más detalles sobre las alertas y las entidades del incidente, seleccione **View full details** (Ver detalles completos) en la página del incidente y revise las pestañas correspondientes donde se resume la información del incidente. 

    ![Ver detalles de la alerta](media/tutorial-investigate-cases/incident-timeline.png)

    Por ejemplo:

    - En la pestaña **Escala de tiempo**, revise la escala de tiempo de las alertas y los marcadores del incidente, para poder reconstruir la escala de tiempo de la actividad del atacante.
    - En la pestaña **Alertas**, revise la alerta en sí. Puede ver toda la información relevante sobre la alerta: la consulta que la ha desencadenado, el número de resultados devueltos por consulta y la capacidad de ejecutar cuadernos de estrategias en las alertas. Para explorar el incidente más en profundidad, seleccione el número de **eventos**. Esto abre la consulta que ha generado los resultados y los eventos que han desencadenado la alerta en Log Analytics. 
    - En la pestaña **Entities** (Entidades), puede ver todas las entidades que ha asignado como parte de la definición de regla de alerta.

1. Si está investigando un incidente de manera activa, una buena idea consiste en establecer el estado del incidente en **En curso** hasta que lo cierre.

1. Los incidentes se pueden asignar a un usuario específico. Para asignar un propietario a un incidente, hay que establecer el campo **Incident owner** (Propietario del incidente). Todos los incidentes se inician sin tener un propietario asignado. También puede agregar comentarios para que otros analistas puedan comprender lo que ha investigado y sus preocupaciones en torno al incidente.

    ![Asignación de un incidente al usuario](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Seleccione **Investigar** para ver el mapa de investigación.



## <a name="use-the-investigation-graph-to-deep-dive"></a>Uso del gráfico de investigación para un análisis en profundidad

El gráfico de investigación permite a los analistas formular las preguntas adecuadas para cada investigación. El gráfico de investigación le ayuda a comprender el ámbito y a identificar la causa principal de una posible amenaza de seguridad al correlacionar los datos pertinentes con las entidades implicadas. Puede profundizar e investigar cualquier entidad presentada en el gráfico seleccionándola y eligiendo entre las diferentes opciones de expansión.  
  
El gráfico de investigación le proporciona:

- **Contexto visual de datos sin procesar**: El gráfico visual y dinámico, muestra las relaciones de entidad extraídas automáticamente de los datos sin procesar. Esto le permite ver fácilmente las conexiones entre distintos orígenes de datos.

- **Detección del ámbito completo de la investigación**: Amplíe el ámbito de la investigación mediante consultas de exploración integradas para exponer el ámbito completo de una infracción de seguridad.

- **Pasos de investigación integrados**: Use opciones de exploración predefinidas para asegurarse de que está formulando las preguntas adecuadas en caso de una amenaza.

Para usar el gráfico de investigación:

1. Seleccione un incidente y, a continuación, seleccione **Investigar**. Esto le llevará al gráfico de investigación. El gráfico proporciona un mapa ilustrativo de las entidades conectadas directamente a la alerta y de cada recurso conectado más allá.


    [ ![Visualización de mapas.](media/tutorial-investigate-cases/investigation-map.png) ](media/tutorial-investigate-cases/investigation-map.png#lightbox)

   > [!IMPORTANT] 
   > - Solo podrá investigar el incidente si ha usado los campos de asignación de entidades al configurar la regla de análisis. El gráfico de investigación requiere que el incidente original incluya entidades.
   >
   > - Actualmente, Azure Sentinel admite la investigación de **incidentes cuya antigüedad no supere los 30 días**.


1. Seleccione una entidad para abrir el panel **Entidades** para que pueda revisar la información de esa entidad.

    ![Ver entidades en el mapa](media/tutorial-investigate-cases/map-entities.png)
  
1. Amplíe su investigación manteniendo el puntero sobre cada entidad para mostrar una lista de preguntas diseñadas por nuestros expertos y analistas en seguridad clasificadas por tipo de entidad para profundizar en la investigación. Llamamos a estas opciones **consultas de exploración**.

    ![Ver más detalles](media/tutorial-investigate-cases/exploration-cases.png)

   Por ejemplo, en un equipo puede solicitar alertas relacionadas. Si selecciona una consulta de exploración, los derechos resultantes se agregan de nuevo al gráfico. En este ejemplo, al seleccionar **Related Alerts** (Alertas relacionadas) se devolvieron las siguientes alertas en el gráfico:

    ![Ver alertas relacionadas](media/tutorial-investigate-cases/related-alerts.png)

1. Para cada consulta de exploración, puede seleccionar la opción para abrir los resultados de eventos sin procesar y la consulta usada en Log Analytics, seleccionando **Eventos\>** .

1. Para comprender el incidente, el gráfico le proporciona una escala de tiempo paralela.

    ![Ver escala de tiempo en el mapa](media/tutorial-investigate-cases/map-timeline.png)

1. Mantenga el puntero sobre la escala de tiempo para ver qué elementos del gráfico se produjeron en qué momento.

    ![Uso de una escala de tiempo en un mapa para investigar alertas](media/tutorial-investigate-cases/use-timeline.png)


## <a name="closing-an-incident"></a>Cierre de un incidente

Una vez que haya resuelto un incidente en particular (por ejemplo, cuando la investigación haya alcanzado su conclusión), debe establecer el estado del incidente en **Cerrado**. Al hacerlo, a los efectos de clasificar el incidente, se le pedirá que indique el motivo por el que lo cierra. Este paso es obligatorio. Haga clic en **Seleccionar clasificación** y elija una de las siguientes opciones de la lista desplegable:

- Verdadero positivo: actividad sospechosa
- Positivo inofensivo: sospechoso, pero esperado
- Falso positivo: lógica de alerta incorrecta
- Falso positivo: datos incorrectos
- Indeterminada

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-dropdown.png" alt-text="Captura de pantalla que destaca las clasificaciones disponibles en la lista Seleccionar clasificación.":::

Para más información sobre los falsos positivos y los positivos inofensivos, consulte [Control de falsos positivos en Azure Sentinel](false-positives.md).

Después de elegir la clasificación adecuada, agregue texto descriptivo en el campo **Comentario**. Esto será útil en el caso de que tenga que volver a consultar este incidente. Haga clic en **Aplicar** cuando haya terminado, y el incidente se cerrará.

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-comment-apply.png" alt-text="{alt-text}":::

## <a name="search-for-incidents"></a>Búsqueda de incidentes

Para buscar rápidamente un incidente concreto, escriba una cadena de búsqueda en el cuadro de búsqueda situado encima de la cuadrícula de incidentes y presione **Entrar** para modificar la lista de incidentes que se muestra en consecuencia. Si el incidente no se incluye en los resultados, es posible que desee restringir la búsqueda mediante las opciones de **Búsqueda avanzada**.

Para modificar los parámetros de búsqueda, seleccione el botón **Buscar** y, después, seleccione los parámetros donde desea ejecutar la búsqueda.

Por ejemplo:

:::image type="content" source="media/tutorial-investigate-cases/advanced-search.png" alt-text="Captura de pantalla del cuadro de búsqueda de incidentes y el botón para seleccionar opciones de búsqueda básicas o avanzadas.":::

De forma predeterminada, las búsquedas de incidentes se ejecutan solo en los valores de **Identificador de incidente**, **Título**, **Etiquetas**, **Propietario** y **Nombre de producto**. En el panel de búsqueda, desplácese hacia abajo en la lista para seleccionar uno o varios otros parámetros para buscar y seleccione **Aplicar** para actualizar los parámetros de búsqueda. Seleccione **Establecer como predeterminado** para restablecer los parámetros seleccionados a la opción predeterminada.


> [!NOTE]
> Las búsquedas en el campo **Propietario** admiten nombres y direcciones de correo electrónico.
>

El uso de opciones de búsqueda avanzada cambia el comportamiento de búsqueda como se indica a continuación:

|Comportamiento de búsqueda  |Descripción  |
|---------|---------|
|**Color del botón Buscar**     |El color del botón de búsqueda cambia en función de los tipos de parámetros que se usen en la búsqueda. <br><br>- Si solo están seleccionados los parámetros predeterminados, el botón será gris. <br>- Si hay distintos parámetros seleccionados, como los parámetros de búsqueda avanzada, el botón se vuelve azul.         |
|**Actualización automática**     | El uso de parámetros de búsqueda avanzada impide seleccionar que los resultados se actualicen automáticamente.        |
|**Parámetros de la entidad**     |Todos los parámetros de la entidad se admiten para búsquedas avanzadas. Al buscar en cualquier parámetro de la entidad, la búsqueda se ejecuta en todos los parámetros de la entidad.         |
|**Búsqueda en las cadenas**     |    La búsqueda de una cadena de palabras incluye todas las palabras de la consulta de búsqueda. Las cadenas de búsqueda distinguen mayúsculas de minúsculas.     |
|**Compatibilidad entre áreas de trabajo**     |    Las búsquedas avanzadas no son compatibles con las vistas entre áreas de trabajo.     |
| **Número de resultados de búsqueda que se muestran** | Cuando se usan parámetros de búsqueda avanzados, no se muestran más de 50 resultados a la vez. |
|     |         |

> [!NOTE]
> La búsqueda avanzada está actualmente en versión preliminar pública.
>

> [!TIP]
>  Si no puede encontrar el incidente que busca, quite los parámetros de búsqueda para expandir la búsqueda. Si la búsqueda genera demasiados elementos, agregue más filtros para restringir los resultados.
>


## <a name="next-steps"></a>Pasos siguientes
En este artículo ha aprendido cómo empezar a investigar incidentes mediante Azure Sentinel. Para más información, consulte:

- [Tutorial: Uso de cuadernos de estrategias con reglas de automatización en Azure Sentinel](tutorial-respond-threats-playbook.md)
- [Investigación de incidentes con datos de UEBA](investigate-with-ueba.md)
