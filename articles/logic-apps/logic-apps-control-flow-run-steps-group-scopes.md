---
title: Agrupación y ejecución de acciones por ámbito
description: Creación de acciones por ámbito que se ejecuten en función del estado del grupo en Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: 3b8e4395cd2ac46acb94abced52aa28eccf4f3d7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615992"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Ejecución de acciones basadas en el estado del grupo mediante ámbitos en Azure Logic Apps

Para ejecutar acciones solo después de que otro grupo de acciones se haya realizado correctamente o con errores, ponga esas acciones en un *ámbito*. Esta estructura es útil cuando desea organizar acciones como un grupo lógico, evaluar el estado del grupo y realizar acciones basadas en el estado del ámbito. Una vez que todas las acciones de un ámbito acaben de ejecutarse, el ámbito obtiene también su propio estado. Por ejemplo, puede usar ámbitos cuando desea implementar el [control de errores y excepciones](../logic-apps/logic-apps-exception-handling.md#scopes). 

Para comprobar el estado del ámbito, puede usar los mismos criterios que para determinar el estado de ejecución de una aplicación lógica, por ejemplo, **Correcto**, **Error**, **Cancelado**, etc. De forma predeterminada, cuando las acciones del ámbito se ejecutan correctamente, el estado del ámbito se marca como **Correcto**. Sin embargo, si cualquier acción en el ámbito genera un error o se cancela, el estado del ámbito se marca como **Error**. Para conocer los límites en los ámbitos, consulte [Límites y configuración](../logic-apps/logic-apps-limits-and-config.md). 

Por ejemplo, aquí hay una aplicación lógica de alto nivel que utiliza un ámbito para ejecutar acciones específicas y una condición para comprobar el estado del ámbito. Si una acción del ámbito genera un error o finaliza inesperadamente, el ámbito se marca como **Error** o **Anulado** respectivamente, y la aplicación lógica envía un mensaje de error de ámbito. Si todas las acciones del ámbito se realizan correctamente, la aplicación lógica enviará un mensaje de "Scope succeeded" (El ámbito se realizó correctamente).

![En el diagrama se muestra el flujo del ámbito de la aplicación lógica con ejemplos de error de ámbito y ámbito correcto.](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Prerrequisitos

Para seguir el ejemplo de este artículo, necesita estos elementos:

* Suscripción a Azure. Si aún no tiene una, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/). 

* Una cuenta de correo electrónico de cualquier proveedor de correo electrónico compatible con Logic Apps. Este ejemplo utiliza Outlook.com. Si utiliza un proveedor diferente, el flujo general sigue siendo el mismo, pero la interfaz de usuario es diferente.

* Una clave de Mapas de Bing. Para obtener esta clave, consulte cómo <a href="/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key" target="_blank">obtener una clave de Mapas de Bing</a>.

* Conocimientos básicos acerca de [cómo crear aplicaciones lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Creación de una aplicación lógica de ejemplo

En primer lugar, cree esta aplicación lógica de ejemplo para que pueda agregar un ámbito después:

![Creación de una aplicación lógica de ejemplo](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Un desencadenador **Programación: Periodicidad** que comprueba el servicio Mapas de Bing en un intervalo que puede especificar
* Una acción **Bing Maps - Get route** (Mapas de Bing: Obtener ruta) que comprueba el tiempo de viaje entre dos ubicaciones
* Una instrucción condicional que comprueba si el tiempo de viaje supera el tiempo de viaje especificado
* Una acción que le envía un correo electrónico indicando que el tiempo de viaje actual supera el tiempo especificado

Puede guardar la aplicación lógica en cualquier momento, así que guarde su trabajo con frecuencia.

1. Inicie sesión en <a href="https://portal.azure.com" target="_blank">Azure Portal</a> si aún no lo ha hecho. Crear una aplicación lógica en blanco.

1. Agregue el desencadenador **Programación: Periodicidad** con estos valores: **Intervalo** = "1" y **Frecuencia** = "Minuto"

   ![Configurar desencadenador "Programación: Periodicidad"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Para simplificar visualmente la vista y ocultar los detalles de cada acción en el diseñador, contraiga la forma de cada acción a medida que recorre estos pasos.

1. Agregue la acción **Bing Maps - Get route** (Mapas de Bing: Obtener ruta). 

   1. Si no tiene una conexión de Mapas de Bing, se le solicitará que cree una.

      | Configuración | Value | Descripción |
      | ------- | ----- | ----------- |
      | **Nombre de la conexión** | BingMapsConnection | Proporcione un nombre para la conexión. | 
      | **Clave de API** | <*su-clave-de-Mapas-de-Bing*> | Escriba la clave de Mapas de Bing recibida previamente. | 
      ||||  

   1. Configure la acción **Get route** (Obtener ruta) tal como se muestra en la tabla que aparece a continuación de esta imagen:

      ![Configurar acción "Bing Maps - Get route" (Mapas de Bing: Obtener ruta)](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Para más información acerca de estos parámetros, consulte [Calculate a route](/bingmaps/rest-services/routes/calculate-a-route) (Cálculo de una ruta).

      | Configuración | Value | Descripción |
      | ------- | ----- | ----------- |
      | **Waypoint 1** (Punto de referencia 1) | <*inicio*> | Escriba el origen de la ruta. | 
      | **Waypoint 2** (Punto de referencia 2) | <*fin*> | Escriba el destino de la ruta. | 
      | **Avoid** (Evitar) | None | Escriba los elemento a evitar en la ruta, como autopistas, peajes, etc. Para los valores posibles, consulte [Calculate a route](/bingmaps/rest-services/routes/calculate-a-route) (Calcular una ruta). | 
      | **Optimize** (Optimizar) | timeWithTraffic | Seleccione un parámetro para optimizar la ruta, como distancia, información sobre el tiempo de trayecto con el tráfico actual, etc. Este ejemplo utiliza este valor: "timeWithTraffic" | 
      | **Distance unit** (Unidad de distancia) | <*su preferencia*> | Especifique la unidad de distancia para calcular la ruta. Este ejemplo utiliza este valor: "Mile" (Milla) | 
      | **Travel mode** (Modo de desplazamiento) | Conducción | Escriba el modo de desplazamiento para la ruta. Este ejemplo utiliza este valor: "Driving" (Conducción) | 
      | **Transit Date-Time** (Fecha y hora de tránsito) | None | Se aplica solo al modo de tránsito. | 
      | **Transit Date-Type Type** (Fecha y tipo de tránsito) | None | Se aplica solo al modo de tránsito. | 
      ||||  

1. [Agregue una condición](../logic-apps/logic-apps-control-flow-conditional-statement.md) que compruebe si el tiempo de trayecto con el tráfico actual supera el tiempo especificado. 
   Para este ejemplo, siga estos pasos:

   1. Cambie el nombre de la condición por esta descripción: **Si la hora de tráfico es posterior a la hora especificada**

   1. En la columna más a la izquierda, haga clic dentro del cuadro **Elegir un valor** para que aparezca la lista de contenido dinámico. En la lista, seleccione el campo **Travel Duration Traffic** (Tráfico de duración del desplazamiento), que está en segundos. 

      ![Crear condición](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. En el cuadro del medio, seleccione este operador: **es mayor que**

   1. En la columna más a la derecha, escriba este valor de comparación, que está en segundos y equivale a 10 minutos: **600**

      Una vez que haya terminado, la condición debe ser parecida a la de este ejemplo:

      ![Condición finalizada](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. En la rama **If true**, agregue una acción "send email" (enviar correo electrónico) para su proveedor de correo electrónico. 
   Para configurar esta acción, siga los pasos descritos en esta imagen:

   ![Agregar acción "Send an email" (Enviar un correo electrónico) a la rama "If true"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. En el campo **Para**, escriba su dirección de correo electrónico con fines de prueba.

   1. En el campo **Asunto**, escriba este texto:

     `Time to leave: Traffic more than 10 minutes`

   1. En el campo **Cuerpo**, escriba este texto con un espacio final: 

     `Travel time:`

     Mientras el cursor aparece en el campo **Cuerpo**, la lista de contenido dinámica permanece abierta para que pueda seleccionar los parámetros que estén disponibles en este momento.

   1. En la lista de contenido dinámico, elija **Expresión**.

   1. Busque y seleccione la función **div()** . 
      Coloque el cursor dentro de los paréntesis de la función.

   1. Mientras el cursor se encuentre dentro de los paréntesis de la función, elija **Contenido dinámico** para que aparezca la lista de contenido dinámico. 
   
   1. Desde la sección **Get route**, seleccione el campo **Travel Duration Traffic**.

      ![Seleccionar "Travel Duration Traffic" (Tráfico de duración del desplazamiento)](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Una vez que el campo se resuelve en formato JSON, agregue una **coma** (```,```) seguida del número ```60``` para que convierta el valor de **Travel Duration Traffic** (Tráfico de duración del desplazamiento) de segundos a minutos. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Ahora, la expresión es similar a este ejemplo:

      ![Finalizar expresión](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Cuando termine, elija **Aceptar**.

   <!-- markdownlint-disable MD038 -->
   1. Una vez que la expresión se resuelve, agregue este texto con un espacio inicial: ``` minutes```
  
       Ahora, el campo **Cuerpo** es similar a este ejemplo:

       ![Campo "Cuerpo" terminado](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Guarde la aplicación lógica.

A continuación, agregue un ámbito para que pueda agrupar acciones específicas y evaluar su estado.

## <a name="add-a-scope"></a>Agregar un ámbito

1. Si aún no lo ha hecho, abra la aplicación lógica en el Diseñador de aplicación lógica. 

1. Agregue un ámbito a la ubicación del flujo de trabajo que quiera. Por ejemplo, para agregar un ámbito entre pasos existentes en el flujo de trabajo de la aplicación lógica, siga estos pasos: 

   1. Mueva el puntero sobre la flecha donde quiere agregar el ámbito. 
   Elija el **signo más** ( **+** ) > **Agregar una acción**.

      ![Agregar un ámbito](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. En el cuadro de búsqueda, escriba "ámbito" como filtro. 
   Seleccione la acción **Ámbito**.

## <a name="add-steps-to-scope"></a>Agregar pasos al ámbito

1. Ahora agregue los pasos o arrastre los pasos existentes que desea ejecutar dentro del ámbito. En este ejemplo, arrastre estas acciones al ámbito:
      
   * **Get route** (Obtener ruta)
   * **If traffic time is more than specified time** (Si el tiempo de trayecto es mayor que el tiempo especificado), que incluye las dos ramas: **true** y **false**

   Ahora, la aplicación lógica es similar a este ejemplo:

   ![Ámbito agregado](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. En el ámbito, agregue una condición que compruebe el estado del ámbito. Cambie el nombre de la condición por esta descripción: **Si el ámbito dio error**

   ![Agregar condición para comprobar el estado del ámbito](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. En la condición, agregue estas expresiones que comprueban si el estado del ámbito es igual a "Erróneo" o "Anulado". 

   1. Para agregar otra fila, elija **Agregar**. 

   1. En cada fila, haga clic en el cuadro de la izquierda para que aparezca la lista de contenido dinámico. 
   En la lista de contenido dinámico, elija **Expresión**. En el cuadro de edición, escriba esta expresión y, luego, elija **Aceptar**: 
   
      `result('Scope')[0]['status']`

      ![Captura de pantalla que muestra el cuadro "Expresión" con la expresión de resultado resaltada.](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Para ambas filas, seleccione **es igual a** como operador. 
   
   1. Para los valores de comparación, en la primera fila, escriba `Failed`. 
   En la segunda fila, escriba `Aborted`. 

      Una vez que haya terminado, la condición debe ser parecida a la de este ejemplo:

      ![Agregar expresión que comprueba el estado del ámbito](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Ahora, establezca la propiedad `runAfter` de la condición, para que esta compruebe el estado del ámbito y ejecute la acción correspondiente que definirá en pasos posteriores.

   1. En la condición **If scope failed** (Si el ámbito fue erróneo), elija el botón de **puntos suspensivos** (...) y, luego, elija **Configurar ejecución posterior**.

      ![Configurar la propiedad "runAfter"](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Seleccione todos estos estados de ámbito: **Correcto**, **con errores**, **se omitió** y **se superó el tiempo de espera**

      ![Seleccione los estados de ámbito](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Cuando haya finalizado, elija **Listo**. 
   La condición ahora muestra un icono de "información".

1. En las ramas **If true** e **If false**, agregue las acciones que quiere realizar en función de cada estado de ámbito, por ejemplo, enviar correo electrónico o un mensaje.

   ![Adición de acciones para realizar en función del estado del ámbito](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Guarde la aplicación lógica.

Ahora, la aplicación lógica finalizada es similar a este ejemplo:

![Aplicación lógica terminada con ámbito](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Comprobación del trabajo

En la barra de herramientas del diseñador, elija **Ejecutar**. Si todas las acciones del ámbito se realizan correctamente, recibirá un mensaje de "Scope succeeded" (El ámbito se realizó correctamente). Si alguna de las acciones del ámbito no se realiza correctamente, recibirá un mensaje de "Scope failed" (El ámbito produjo error). 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definición JSON

Si está trabajando en la vista de código, puede definir la estructura de un ámbito en la definición JSON de la aplicación lógica en su lugar. Por ejemplo, aquí está la definición JSON para el desencadenador y las acciones de la aplicación lógica anterior:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene alguna duda, visite la [página de preguntas y respuestas de Microsoft sobre Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Para enviar características y sugerencias o votar las que ya se han enviado, visite el [sitio web de comentarios de los usuarios de Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

* [Ejecución de pasos en función de una condición (instrucciones condicionales)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Realización de pasos en función de los diferentes valores (instrucciones switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Ejecución y repetición de pasos (bucles)](../logic-apps/logic-apps-control-flow-loops.md)
* [Ejecución o combinación de pasos en paralelo (ramas)](../logic-apps/logic-apps-control-flow-branches.md)
