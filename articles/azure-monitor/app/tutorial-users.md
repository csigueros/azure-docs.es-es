---
title: Información sobre sus clientes en Application Insights | Microsoft Docs
description: Tutorial sobre el uso de Application Insights para entender la forma en que los clientes utilizan su aplicación.
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 07/30/2021
ms.custom: mvc
ms.openlocfilehash: 8848d7db278576f8f165b55f6b69ce998bfe7629
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732114"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Use Azure Application Insights para entender la forma en que los clientes utilizan su aplicación

 Application Insights recopila información de uso que le ayuda a entender la forma en que los usuarios interactúan con su aplicación.  Este tutorial le guía a través de los diferentes recursos disponibles para analizar esta información.  Aprenderá a:

> [!div class="checklist"]
> * Analizar los detalles de los usuarios que accedan a su aplicación
> * Usar información de la sesión para analizar la forma en que los clientes usan su aplicación
> * Definir embudos que le permitan comparar la actividad de usuario deseada con la actividad de usuario real 
> * Crear un libro para consolidar visualizaciones y consultas en un solo documento
> * Agrupar usuarios similares para analizarlos juntos conjuntamente
> * Saber qué usuarios vuelven a su aplicación
> * Inspeccionar la forma en que los usuarios navegan por la aplicación


## <a name="prerequisites"></a>Prerequisites

Para completar este tutorial:

- Instale [Visual Studio 2019](https://www.visualstudio.com/downloads/) con las cargas de trabajo siguientes:
    - ASP.NET y desarrollo web
    - Desarrollo de Azure
- Descargue e instale [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger).
- Implemente una aplicación de .NET en Azure y [habilite el SDK de Application Insights](../app/asp-net.md). 
- [Envíe telemetría desde su aplicación](../app/usage-overview.md#send-telemetry-from-your-app) para agregar eventos personalizados o vistas de página
- Enviar el [contexto del usuario](./usage-overview.md) para realizar un seguimiento de lo que un usuario hace con el tiempo y utilizar plenamente las características de uso.

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure
Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="get-information-about-your-users"></a>Obtención de información acerca de los usuarios
El panel **Usuarios** le permite conocer detalles importantes acerca de los usuarios de varias formas. Este panel se puede utilizar para saber desde dónde se conectan los usuarios, conocer detalles de sus clientes y las áreas de la aplicación a las que acceden. 

1. En el recurso de Application Insights situado bajo *Uso*, seleccione **Usuarios** en el menú.
2. La vista predeterminada muestra el número de usuarios únicos que se han conectado a la aplicación en las últimas 24 horas.  Puede cambiar la ventana de tiempo y establezca otros criterios para filtrar esta información.

3. Haga clic en la lista desplegable **Durante** y cambie la ventana de tiempo a 7 días.  Así aumentan los datos que se incluyen en los distintos gráficos del panel.

4. Haga clic en la lista desplegable **Dividido por** para agregar al grafo un desglose por una propiedad del usuario.  Seleccione **País o región**.  El grafo incluye los mismos datos, pero permite ver un desglose del número de usuarios de cada país o región.

      :::image type="content" source="./media/tutorial-users/user-1.png" alt-text="Captura de pantalla del generador de consultas de la pestaña Usuario." lightbox="./media/tutorial-users/user-1.png":::

5. Coloque el cursor sobre las distintas barras del gráfico y tenga en cuenta que el recuento de cada país o región refleja solo la ventana de tiempo representada por dicha barra.
6. Para obtener más información, seleccione **Ver más conclusiones**. 

      :::image type="content" source="./media/tutorial-users/user-2.png" alt-text="Captura de pantalla de la pestaña Usuario para ver más información." lightbox="./media/tutorial-users/user-2.png":::


## <a name="analyze-user-sessions"></a>Análisis de sesiones del usuario
El panel **Sesiones** es similar al panel **Usuarios**.  Mientras que **Usuarios** le ayuda a conocer los detalles acerca de los usuarios que acceden a su aplicación, **Sesiones** le ayuda a saber la forma en que dichos usuarios han utilizado su aplicación.  

1. En *Uso*, seleccione **Sesiones**.
2. Eche un vistazo al grafo y tenga en cuenta que cuenta con las mismas opciones para filtrar y desglosar los datos que en el panel **Usuarios**.

     :::image type="content" source="./media/tutorial-users/sessions.png" alt-text="Captura de pantalla de la pestaña Sesiones con un gráfico de barras mostrado." lightbox="./media/tutorial-users/sessions.png":::

4. Para ver la escala de tiempo de la sesión, seleccione **Ver más conclusiones**. A continuación, bajo las sesiones activas, seleccione **Ver escala de tiempo de la sesión** en una de las escalas. Las escalas de tiempo de la sesión muestran todas las acciones de las sesiones. Esto puede ayudarle a identificar información como las sesiones con un gran número de excepciones.

     :::image type="content" source="./media/tutorial-users/timeline.png" alt-text="Captura de pantalla de la pestaña Sesiones con una escala de tiempo seleccionada." lightbox="./media/tutorial-users/timeline.png":::

## <a name="group-together-similar-users"></a>Agrupar usuarios similares
Una **cohorte** es un conjunto de usuarios agrupados por tener características similares.  Las cohortes se pueden usar para filtrar datos de otros paneles, lo que le permite analizar grupos de usuarios concretos.  Por ejemplo, puede desear analizar solo aquellos usuarios que han realizado una compra.

1.  Seleccione **Crear una cohorte** en la parte superior de una de las pestañas de uso (Usuarios, Sesiones, Eventos, etc.).

1.  Seleccione una plantilla de la galería.

    :::image type="content" source="./media/tutorial-users/cohort.png" alt-text="Captura de pantalla de la galería de plantillas para cohortes." lightbox="./media/tutorial-users/cohort.png":::
1.  Edite la cohorte y seleccione **Guardar**.
1.  Para ver la cohorte, selecciónela en el menú desplegable **Mostrar**. 

    :::image type="content" source="./media/tutorial-users/cohort-2.png" alt-text="Captura de pantalla de la lista desplegable Mostrar, en la que se muestra una cohorte." lightbox="./media/tutorial-users/cohort-2.png":::


## <a name="compare-desired-activity-to-reality"></a>Comparación de la actividad deseada con la realidad
Mientras que los paneles anteriores se centran en lo que han hecho los usuarios de la aplicación, **Embudos** se centra en lo que desea que hagan los usuarios.  Un embudo representa un conjunto de pasos de una aplicación y el porcentaje de usuarios que se mueven entre dichos pasos.  Por ejemplo, puede crear un embudo que mida el porcentaje de usuarios que se conectan a una aplicación que buscan un producto.  A continuación, puede ver el porcentaje de usuarios que agregan dicho producto a un carro de la compra y, después, el porcentaje de ellos que completan una compra.

1. Seleccione **Embudos** en el menú y, a continuación, **Editar**. 

3. Cree un embudo con un mínimo de dos pasos y seleccione una acción para cada paso.  La lista de acciones se crea a partir de los datos de uso recopilados por Application Insights.

    :::image type="content" source="./media/tutorial-users/funnel.png" alt-text="Captura de pantalla de la pestaña Embudo y selección de los pasos en la pestaña Edición" lightbox="./media/tutorial-users/funnel.png":::.

4. Seleccione la pestaña **Ver** para ver los resultados La ventana a la derecha muestra los eventos más comunes antes de la primera actividad y después de la última para ayudarle a conocer las tendencias del usuario en una secuencia determinada.

     :::image type="content" source="./media/tutorial-users/funnel-2.png" alt-text="Captura de pantalla de la pestaña embudo en la vista." lightbox="./media/tutorial-users/funnel-2.png":::

4. Para guardar el embudo, seleccione **Guardar**. 

## <a name="learn-which-customers-return"></a>Información acerca de los clientes que vuelven

**Retención** le ayuda a saber qué usuarios vuelven a una aplicación.  

1. Seleccione **Retención** en el menú y, a continuación, *Libro de análisis de retención.
2. De forma predeterminada, la información analizada incluye aquellos usuarios que realizaron alguna acción y volvieron para llevar a cabo alguna acción.  Este filtro se puede cambiar para que incluya a cualquiera, por ejemplo, solo aquellos usuarios que hayan vuelto después de completar una compra.

      :::image type="content" source="./media/tutorial-users/retention.png" alt-text="Captura de pantalla que muestra un grafo para los usuarios que coincidan con los criterios establecidos para un filtro de retención." lightbox="./media/tutorial-users/retention.png":::

3. Los usuarios que vuelven que coinciden con los criterios se muestran tanto en forma de gráfico como de tabla en distintas duraciones. El patrón más frecuente es una caída gradual en la vuelta de los usuarios con el paso del tiempo.  Una caída repentina de un período de tiempo al siguiente puede provocar un problema. 

      :::image type="content" source="./media/tutorial-users/retention-2.png" alt-text="Captura de pantalla del libro de retención en la que se muestra el regreso del usuario después del gráfico de número de semanas." lightbox="./media/tutorial-users/retention-2.png":::

## <a name="analyze-user-navigation"></a>Análisis de la navegación del usuario
Un **flujo de usuarios** visualiza la forma en que los usuarios navegan entre las páginas y características de la aplicación.  Esto le ayuda a responder a preguntas como a qué lugar se suelen mover los usuarios desde una página concreta, cómo salen habitualmente de su aplicación y si hay acciones que se repitan de manera regular.

1.  Seleccione **Flujos de usuarios** en el menú.
2.  Haga clic en **Nuevo** para crear un nuevo flujo de usuario y, a continuación, seleccione **Editar** para editar sus detalles.
3.  Aumente el valor de **Intervalo de tiempo** a 7 días y, luego, seleccione un evento inicial.  El flujo hará un seguimiento de las sesiones de usuario que empiezan por ese evento.

     :::image type="content" source="./media/tutorial-users/flowsedit.png" alt-text="Captura de pantalla que muestra cómo crear un nuevo flujo de usuario." lightbox="./media/tutorial-users/flowsedit.png":::
    
4.  Se muestra el flujo de usuario y puede ver las rutas de acceso de los diferentes usuarios y el número de sesiones.  Las líneas azules indican una acción que el usuario ha realizado después de la acción actual.  Una línea roja indica el final de la sesión del usuario.

   :::image type="content" source="./media/tutorial-users/flows.png" alt-text="Captura de pantalla que muestra la visualización de rutas de acceso de usuario y recuentos de sesiones para un flujo de usuario." lightbox="./media/tutorial-users/flows.png":::

5.  Para quitar un evento del flujo, seleccione **x** de la esquina de la acción y, después, **Crear gráfico**.  El grafo se vuelve a dibujar sin ninguna de las instancias de dicho evento. Seleccione **Editar** para ver que el evento se ha agregado a **Eventos excluidos**.

    :::image type="content" source="./media/tutorial-users/flowsexclude.png" alt-text="Captura de pantalla que muestra la lista de eventos excluidos de un flujo de usuario." lightbox="./media/tutorial-users/flowsexclude.png":::

## <a name="consolidate-usage-data"></a>Consolidación de datos de uso
Los **libros** combinan las visualizaciones de datos, las consultas de Analytics y texto en documentos interactivos.  Se pueden usar para agrupar la información de uso común, consolidar la información de un incidente determinado o informar al equipo sobre el uso de la aplicación.

1.  Seleccione **Libros** en el menú.
2.  Seleccione **Nuevo** para crear un libro nuevo.
3.  Ya se proporciona una consulta que incluye todos los datos de uso en el último día que se muestra como un gráfico de barras.  Puede usar esta consulta, editarla manualmente o seleccionar **Ejemplos** para seleccionar otras consultas útiles.

    :::image type="content" source="./media/tutorial-users/sample-queries.png" alt-text="Captura de pantalla que muestra el botón de ejemplo y una lista de consultas de ejemplo que puede usar." lightbox="./media/tutorial-users/sample-queries.png":::

4.  Seleccione **Edición finalizada**.
5.  Seleccione **Editar** en el panel superior para editar el texto de la parte superior del libro.  Para darle formato se usa Markdown.

6.  Seleccione **Agregar usuarios** para agregar un grafo con información del usuario.  Si lo desea, edite los detalles del grafo y, después, seleccione **Edición finalizada** para guardarlo.

Para más información sobre los libros, visite [la introducción a los libros](../visualize/workbooks-overview.md).

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido a analizar a los usuarios, pase al siguiente tutorial para aprender a crear paneles personalizados que combinan esta información con otros datos útiles de la aplicación.

> [!div class="nextstepaction"]
> [Creación de paneles personalizados](./tutorial-app-dashboards.md)