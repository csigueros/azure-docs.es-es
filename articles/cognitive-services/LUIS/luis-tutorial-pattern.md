---
title: 'Tutorial: Patrones: LUIS'
description: Use patrones para aumentar la predicción de intenciones y entidades, a la vez que se proporcionan menos expresiones de ejemplo, con este tutorial. El patrón se proporciona como un ejemplo de plantilla de expresión, que incluye la sintaxis para identificar las entidades y el texto que se puede pasar por alto.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/06/2020
ms.openlocfilehash: 5d4ac6af77344717a9f38a96a28217de35d28d3d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454777"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Tutorial: Incorporación de formatos comunes de expresión de plantilla de patrón para mejorar las predicciones

En este tutorial se usan patrones para aumentar la predicción de intenciones y entidades, lo cual le permite proporcionar menos expresiones de ejemplo. El patrón es una expresión de plantilla que se asigna a una intención, que contiene la sintaxis para identificar las entidades y el texto que se puede pasar por alto.

**En este tutorial, aprenderá a:**

> [!div class="checklist"]
> * Crear un patrón
> * Comprobar las mejoras de la predicción del patrón
> * Marcar texto como irrelevante y anidar dentro del patrón
> * Usar el panel de prueba para comprobar la corrección del patrón

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Expresiones en intenciones y patrones

Hay dos tipos de expresiones que se almacenan en la aplicación LUIS:

* Expresiones de ejemplo de la intención
* Expresiones de plantilla del patrón

La incorporación de expresiones de plantilla como patrón le permite proporcionar menos expresiones de ejemplo en total a una intención.

Un patrón se aplica como una combinación de aprendizaje automático y coincidencia de texto.  La expresión de plantilla del patrón, junto con las expresiones de ejemplo de la intención, permiten que LUIS comprenda mejor qué expresiones se ajustan a la intención.

## <a name="import-example-app-and-clone-to-new-version"></a>Importación de una aplicación de ejemplo y clonación en una nueva versión

Siga estos pasos:

1.  Descargue y guarde el [archivo JSON de la aplicación](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true).

1. Inicie sesión en el [portal de LUIS](https://www.luis.ai), seleccione su **Suscripción** y **Recurso de creación** para ver las aplicaciones asignadas a ese recurso de creación.
1. Importe el archivo JSON a una nueva aplicación en el [portal de LUIS](https://www.luis.ai). En la página **Mis aplicaciones**, seleccione **+ New app for conversation** (+ Nueva aplicación para conversación) y, después, seleccione **Import as JSON** (Importar como JSON). Seleccione el archivo que descargó en el paso anterior y asigne a la aplicación el nombre `Patterns tutorial`.

## <a name="create-new-intents-and-their-utterances"></a>Creación de intenciones nuevas y sus expresiones

Las dos intenciones encuentran el administrador o los informes directos del administrador, según el texto de la expresión. La dificultad es que las dos intenciones _significan_ cosas diferentes, pero la mayoría de las palabras coinciden. Lo único que cambia es su orden. Para que la intención se prediga correctamente, tendría que tener muchos ejemplos.

1. Seleccione **Compilar** en la barra de navegación.

1. En la página **Intents** (Intenciones), seleccione **+ Create** (Crear) para crear una nueva intención.

1. Escriba `OrgChart-Manager` en el cuadro de diálogo emergente y seleccione **Done** (Listo).

    ![Creación de una ventana emergente de mensaje nuevo](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Agregue expresiones de ejemplo a la intención. Estas expresiones no son _exactamente_ iguales, pero tienen un patrón que se puede extraer.

    |Expresiones de ejemplo|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

1. Seleccione **Intents** (Intenciones) en el panel de navegación izquierdo.

1. Seleccione **+ Create** (Crear) para crear una nueva intención. Escriba `OrgChart-Reports` en el cuadro de diálogo emergente y seleccione **Done** (Listo).

1. Agregue expresiones de ejemplo a la intención.

    |Expresiones de ejemplo|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>Precaución sobre la cantidad de expresiones de ejemplo

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>Entrenamiento de la aplicación antes de las pruebas o la publicación

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicación de la aplicación en la consulta desde el punto de conexión

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>Obtención de intenciones y entidades del punto de conexión

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vaya al final de la dirección URL de la barra de direcciones y reemplace _YOUR_QUERY_HERE_ por: `Who is the boss of Jill Jones?`.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.326605469
                },
                "OrgChart-Reports": {
                    "score": 0.127583548
                },
                "EmployeeFeedback": {
                    "score": 0.0299124215
                },
                "MoveEmployee": {
                    "score": 0.01159851
                },
                "GetJobInformation": {
                    "score": 0.0104600191
                },
                "ApplyForJob": {
                    "score": 0.007508645
                },
                "Utilities.StartOver": {
                    "score": 0.00359402061
                },
                "Utilities.Stop": {
                    "score": 0.00336530479
                },
                "FindForm": {
                    "score": 0.002653719
                },
                "Utilities.Cancel": {
                    "score": 0.00263288687
                },
                "None": {
                    "score": 0.00238638581
                },
                "Utilities.Help": {
                    "score": 0.00226386427
                },
                "Utilities.Confirm": {
                    "score": 0.00211663754
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Se predijo la intención superior correcta, `OrgChart-Manager`, pero la puntuación no es superior al 70 % y no está lo suficientemente por encima de la siguiente intención más alta. Use los patrones para que la puntuación de la intención correcta sea considerablemente más alta en porcentaje y esté más lejos de la puntuación más alta siguiente.

Deje esta segunda ventana del explorador abierta. La usará más adelante en el tutorial.

## <a name="template-utterances"></a>Expresiones de plantilla
Dada la naturaleza del dominio del tema de recursos humanos, hay algunas formas habituales de preguntar por relaciones de empleados en las organizaciones. Por ejemplo:

|Grabaciones de voz|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Estas expresiones son demasiado cercanas como para determinar la singularidad contextual de cada una de ellas sin proporcionar _muchos_ ejemplos de expresiones. Al agregar un patrón para una intención, LUIS aprende patrones de expresión comunes para una intención sin necesidad de que se proporcionen muchos más ejemplos de expresiones.

Los ejemplos de expresiones de plantilla de esta intención incluyen:

|Ejemplos de expresiones de plantilla|significado de sintaxis|
|--|--|
|`Who does {EmployeeListEntity} report to[?]`|interchangeable `{EmployeeListEntity}`<br>ignore `[?]`|
|`Who reports to {EmployeeListEntity}[?]`|interchangeable `{EmployeeListEntity}`<br>ignore `[?]`|

La sintaxis `{EmployeeListEntity}` marca la ubicación de la entidad dentro de la expresión de plantilla, así como qué entidad. La sintaxis opcional, `[?]`, marca palabras o [signos de puntuación](luis-reference-application-settings.md#punctuation-normalization) que son opcionales. LUIS coincide con la expresión y omite el texto opcional entre corchetes.

Aunque la sintaxis se asemeja a una expresión regular, no lo es. Solo se admite la sintaxis de llave, `{}`, y corchete, `[]`. Se pueden anidar hasta dos niveles.

Para que un patrón coincida con una expresión, _primero_ las entidades dentro de la expresión tienen que coincidir con las de la expresión de plantilla. Esto significa que las entidades deben tener suficientes ejemplos de expresiones con un alto grado de predicción antes de que los patrones con entidades sean correctos. Sin embargo, la plantilla no ayuda a predecir las entidades, solo las intenciones.

**Si bien los patrones permiten proporcionar menos expresiones de ejemplo, si no se detectan las entidades, el patrón no coincide.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Agregar los patrones de la intención de OrgChart-Manager

1. Seleccione **Build** (Compilación) en el menú superior.

1. En el panel de navegación izquierdo, debajo de **Improve app performance** (Mejorar el rendimiento de la aplicación), seleccione **Patrones**.

1. Seleccione la intención **OrgChart-Manager** y luego escriba las siguientes expresiones de plantilla:

    |Expresiones de plantilla|
    |:--|
    |`Who is {EmployeeListEntity} the subordinate of[?]`|
    |`Who does {EmployeeListEntity} report to[?]`|
    |`Who is {EmployeeListEntity}['s] manager[?]`|
    |`Who does {EmployeeListEntity} directly report to[?]`|
    |`Who is {EmployeeListEntity}['s] supervisor[?]`|
    |`Who is the boss of {EmployeeListEntity}[?]`|

    Estas expresiones de plantilla incluyen la entidad **EmployeeListEntity** con la notación entre corchetes.

1. Mientras sigue en la página de patrones, seleccione la intención **OrgChart-Reports** y luego escriba las siguientes expresiones de plantilla:

    |Expresiones de plantilla|
    |:--|
    |`Who are {EmployeeListEntity}['s] subordinates[?]`|
    |`Who reports to {EmployeeListEntity}[?]`|
    |`Who does {EmployeeListEntity} manage[?]`|
    |`Who are {EmployeeListEntity} direct reports[?]`|
    |`Who does {EmployeeListEntity} supervise[?]`|
    |`Who does {EmployeeListEntity} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Consulta de punto de conexión cuando se usan patrones

Ahora que los patrones se han agregado a la aplicación, entrene, publique y consulte la aplicación en el punto de conexión en tiempo de ejecución de predicción.

1. Seleccione **Entrenar**. Una vez que haya finalizado el entrenamiento, seleccione **Publicar**, el espacio de **producción** y, a continuación, seleccione **Listo**.

1. Cuando finalice la publicación, cambie las pestañas de explorador de vuelta a la pestaña de la dirección URL del punto de conexión.

1. Vaya al final de la dirección URL en la barra de direcciones, compruebe que la consulta es todavía `Who is the boss of Jill Jones?` y envíe la dirección URL para una nueva predicción.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999999046
                },
                "OrgChart-Reports": {
                    "score": 3.237443E-05
                },
                "EmployeeFeedback": {
                    "score": 4.364242E-06
                },
                "GetJobInformation": {
                    "score": 1.616159E-06
                },
                "MoveEmployee": {
                    "score": 7.575752E-07
                },
                "ApplyForJob": {
                    "score": 5.234157E-07
                },
                "None": {
                    "score": 3.3E-09
                },
                "Utilities.StartOver": {
                    "score": 1.26E-09
                },
                "FindForm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Cancel": {
                    "score": 1.13636367E-09
                },
                "Utilities.Confirm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Help": {
                    "score": 1.13636367E-09
                },
                "Utilities.Stop": {
                    "score": 1.13636367E-09
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

La predicción de intenciones es ahora mucho más segura y la puntuación de la siguiente intención es muy baja. Estas dos intenciones no se alternarán al realizar el entrenamiento.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Trabajo con texto opcional y entidades creadas previamente

Las expresiones de plantilla del patrón anterior de este tutorial tenían algunos ejemplos de texto opcional, como el uso posesivo de la letra s, `'s` y el uso del signo de interrogación, `?`. Supongamos que necesita permitir fechas actuales y futuras en el texto de la expresión.

Las expresiones de ejemplo son:

|Intención|Expresiones de ejemplo con texto opcional y entidades creadas previamente|
|:--|:--|
|OrgChart-Manager (Organigrama-Administrador)|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager (Organigrama-Administrador)|`Who is Jill Jones manager now?`|
|OrgChart-Manager (Organigrama-Administrador)|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager (Organigrama-Administrador)|`Who will be Jill Jones manager on March 3?`|

Cada uno de estos ejemplos usa un tiempo verbal, `was`, `is`, `will be`, así como una fecha, `March 3`, `now` y `in a month`, que LUIS debe predecir correctamente. Observe que los dos últimos ejemplos de la tabla usan casi el mismo texto, excepto por `in` y `on`.

Ejemplo de declaraciones de plantilla que permiten esta información opcional:

|Intención|Expresiones de ejemplo con texto opcional y entidades creadas previamente|
|:--|:--|
|OrgChart-Manager (Organigrama-Administrador)|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager (Organigrama-Administrador)|`who is {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|


El uso de la sintaxis opcional de corchetes, `[]`, facilita la adición de este texto opcional a la expresión de plantilla y se pueden anidar hasta un segundo nivel, `[[]]` e incluir entidades o texto.


**Pregunta: ¿Por qué están todas las letras `w`, la primera letra de cada expresión de plantilla, en minúsculas? ¿No deberían estar en mayúsculas o minúsculas opcionalmente?** La expresión enviada al punto de conexión de consulta por la aplicación cliente se convierte en minúsculas. La expresión de plantilla puede estar en mayúsculas o minúsculas y la expresión del punto de conexión también puede estar en cualquiera de las dos. La comparación siempre se realiza después de la conversión a minúsculas.

**Pregunta: ¿Por qué el número creado previamente no forma parte de la expresión de plantilla si el 3 de marzo se predice como número `3` y fecha `March 3`?** La expresión de plantilla contextualmente usa una fecha, ya sea de modo literal, como en `March 3` o abstracto, como en `in a month`. Una fecha puede contener un número, pero un número es posible que no necesariamente se considere como una fecha. Use siempre la entidad que mejor representa el tipo en el que desea que se devuelvan los resultados JSON de la predicción.

**Pregunta: ¿Qué sucede con las expresiones de composición incorrecta como `Who will {EmployeeListEntity}['s] manager be on March 3?`?** Los tiempos verbales diferentes gramaticalmente, como este en el que `will` y `be` son independientes, deben ser una nueva expresión de plantilla. La expresión de plantilla existente no producirá coincidencia. Aunque no ha cambiado la intención de la expresión, ha cambiado la colocación de las palabras en la expresión. Este cambio afecta a la predicción de LUIS. También puede usar la sintaxis [group y OR](#use-the-or-operator-and-groups) con los tiempos verbales para combinar estas expresiones.

> [!CAUTION]
> **Recuerde: primero se encuentran las entidades y, a continuación, se compara el patrón.**

### <a name="add-new-pattern-template-utterances"></a>Adición de nuevas expresiones de plantilla del patrón

1. Mientras sigue en la sección **Patterns** (Patrones) de **Build** (Crear), agregue varias nuevas expresiones de plantilla del patrón. Seleccione **OrgChart-Manager** (Organigrama-Administrador) en el menú desplegable Intent (Intención) y escriba cada una de las siguientes expresiones de plantilla:

    |Intención|Expresiones de ejemplo con texto opcional y entidades creadas previamente|
    |--|--|
    |OrgChart-Manager (Organigrama-Administrador)|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager (Organigrama-Administrador)|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager (Organigrama-Administrador)|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

2. Seleccione **Entrenar** en la barra de navegación para entrenar la aplicación.

3. Una vez completado el entrenamiento, seleccione **Test** (Prueba) en la parte superior del panel para abrir el panel de pruebas.

4. Especifique varias expresiones de prueba para comprobar que el patrón coincide y la puntuación de la intención es considerablemente alta.

    Después de escribir la primera expresión, seleccione **Inspect** (Inspeccionar) en el resultado para ver todos los resultados de la predicción. Cada expresión debe tener la intención **OrgChart-Manager** y debe extraer los valores de las entidades `EmployeeListEntity` y `datetimeV2`.

    |Expresión|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Todas estas expresiones encuentran las entidades, por lo tanto coinciden con el mismo patrón y tienen una puntuación de predicción alta. Ha agregado algunos patrones que coincidirán con muchas variantes de expresiones. No necesitó agregar ninguna expresión de ejemplo a la intención para hacer que la expresión de plantilla funcionara en el patrón.

Este uso de patrones proporcionó:
* Mayores puntuaciones de predicción
* Con las mismas expresiones de ejemplo de la intención
* Con solo unas pocas expresiones de plantilla construidas correctamente en el patrón

### <a name="use-the-or-operator-and-groups"></a>Uso del operador OR y los grupos

Varias de las expresiones de plantilla anteriores son muy parecidas. Use la sintaxis **group**`()` y **OR**`|` para reducir las expresiones de plantilla.

Los dos siguientes patrones se pueden combinar en un único patrón mediante la sintaxis de grupo `()` y OR `|`.

|Intención|Expresiones de ejemplo con texto opcional y entidades creadas previamente|
|--|--|
|OrgChart-Manager (Organigrama-Administrador)|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager (Organigrama-Administrador)|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

La nueva expresión de plantilla será:

`who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`.

Esta expresión usa un elemento **group** alrededor del tiempo verbal necesario y los elementos opcionales `in` y `on` con una barra vertical **or** entre ellos.

> [!NOTE]
> Si usa el símbolo de _OR_, `|` (barra vertical), asegúrese de separarlo con un espacio antes y después en la plantilla de ejemplo.

1. En la página **Patterns** (Patrones), seleccione el filtro **OrgChart Manager**. Para limitar la lista, busque `manager`.

1. Guarde una versión de la expresión de plantilla (para editarla en el paso siguiente) y elimine las otras variaciones.

1. Cambie la expresión de plantilla a:

    `who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`

2. Seleccione **Entrenar** en la barra de navegación para entrenar la aplicación.

3. Una vez completado el entrenamiento, seleccione **Test** (Prueba) en la parte superior del panel para abrir el panel de pruebas.

    Use el panel de prueba para probar las versiones de la expresión:

    |Expresiones para escribir en el panel de prueba|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

Mediante el uso de una sintaxis de más patrones reduce el número de expresiones de plantilla que tiene que mantener en la aplicación, a la vez que sigue teniendo una alta puntuación de predicción.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>Uso de los delimitadores inicial y final de la expresión

La sintaxis del patrón proporciona la sintaxis de delimitador de inicio y final de un símbolo de intercalación, `^`. Los delimitadores de inicio y final de expresión se pueden usar juntos para dirigirse a expresiones muy específicas o posiblemente literales, o se pueden usar por separado para dirigirse a intenciones.

## <a name="using-patternany-entity"></a>Uso de la entidad Pattern.any

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

### <a name="add-example-utterances-with-patternany"></a>Incorporación de expresiones de ejemplo con Pattern.any

1. Seleccione **Build** (Compilación) en el menú de navegación superior y seleccione **Intents** (Intenciones) en el menú de navegación de la izquierda.

1. Seleccione **FindForm** en la lista de las intenciones.

1. Agregue algunas expresiones de ejemplo. El texto que debe predecirse como Pattern.any se encuentra en **negrita**. El nombre del formulario es difícil de determinar a partir de las palabras que le rodean en la expresión. La ayudará que le prestará Pattern.any será marcar los límites de la entidad.

    |Expresión de ejemplo|Nombre de formulario|
    |--|--|
    |Where is the form **What to do when a fire breaks out in the Lab** and who needs to sign it after I read it? (¿Dónde está el formulario "Procedimientos en caso de incendio en el laboratorio" y quién tiene que firmarlo después de que yo lo lea?)|What to do when a fire breaks out in the Lab (Procedimientos en caso de incendio en el laboratorio)
    |Where is **Request relocation from employee new to the company** on the server? (¿Dónde está el formulario "Solicitud de reubicación de un empleado nuevo en la empresa" en el servidor?)|Request relocation from employee new to the company (Solicitud de reubicación de un empleado nuevo en la empresa)|
    |Who authored "**Health and wellness requests on the main campus**" and what is the most current version? (¿Quién creó el formulario "Solicitudes de salud y bienestar en el complejo principal" y cuál es la versión más actual?)|Health and wellness requests on the main campus (Solicitudes de salud y bienestar en el complejo principal)|
    |I'm looking for the form named "**Office move request including physical assets**" (Estoy buscando el formulario "Solicitud de traslado de oficina, incluidos activos físicos") |Office move request including physical assets (Solicitud de traslado de oficina, incluidos activos físicos)|

    Sin una entidad Pattern.any, sería difícil que LUIS entendiera dónde finaliza el título del formulario debido a las muchas variaciones de nombres de formulario.

### <a name="create-a-patternany-entity"></a>Creación de una entidad Pattern.any
La entidad Pattern.any extrae entidades de diferente longitud. Solo funciona en un patrón porque este marca el principio y el final de la entidad con sintaxis.

1. Haga clic en **Entidades** en el panel de navegación izquierdo.

1. Seleccione **+ Create** (Crear), escriba el nombre `FormName` y seleccione **Pattern.any** como el tipo. Seleccione **Crear**.

### <a name="add-a-pattern-that-uses-the-patternany"></a>Agregar un modelo que usa la entidad Pattern.any

1. Haga clic en **Patrones** en el panel de navegación izquierdo.

1. Haga clic en la intención **FindForm**.

1. Escriba las expresiones de plantilla siguientes que usan la entidad nueva:

    |Expresiones de plantilla|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. Entrene la aplicación.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Probar el nuevo patrón para extraer datos de forma libre
1. Haga clic en **Probar** en la barra superior para abrir el panel de pruebas.

1. Escriba la expresión siguiente:

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Haga clic en **Inspeccionar** debajo del resultado para ver los resultados de la prueba de la entidad y la intención.

    Primero se encuentra la entidad `FormName` y luego se encuentra el patrón, que determina la intención. Si tiene un resultado de la prueba en el que no se detectan las entidades y, por tanto, no se encuentra el patrón, debe agregar más expresiones de ejemplo en la intención (no en el patrón).

1. Cierre el panel de pruebas; para ello, haga clic en el botón **Probar** situado en el panel de navegación superior.

### <a name="using-an-explicit-list"></a>Uso de una lista explícita

Si descubre que el patrón, cuando incluye una entidad Pattern.any, extrae entidades de forma incorrecta, use una [lista explícita](reference-pattern-syntax.md#explicit-lists) para corregir este problema.

## <a name="what-did-this-tutorial-accomplish"></a>¿Qué ha logrado este tutorial?

En este tutorial se han agregado patrones para ayudar a LUIS a predecir la intención con una puntuación significativamente mayor sin tener que agregar más expresiones de ejemplo. El marcado de entidades y texto irrelevante ha permitido a LUIS aplicar el patrón a una serie más amplia de expresiones.

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Pasos siguientes


> [!div class="nextstepaction"]
> [Más información sobre la sintaxis del patrón](./reference-pattern-syntax.md)
