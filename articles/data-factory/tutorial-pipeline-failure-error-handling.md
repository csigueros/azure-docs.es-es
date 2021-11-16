---
title: Error de canalización y mensaje de error
description: Comprender cómo se determinan el estado de error de la canalización y el mensaje de error
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: 746b8d0b3b24901469f46090adf25b02f786dcef
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063899"
---
# <a name="understanding-pipeline-failure"></a>Descripción del error de canalización

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="error-handling"></a>Tratamiento de errores

La orquestación de Azure Data Factory y la canalización de Synapse permite la lógica condicional y permite al usuario tomar diferentes rutas de acceso en función de los resultados de una actividad anterior. El uso de rutas de acceso diferentes permite a los usuarios crear canalizaciones sólidas e incorpora el control de errores en la lógica de ETL/ELT. En total, se permiten cuatro rutas de acceso condicionales,

* Si es correcto (paso predeterminado)
* Tras error
* Al finalizar
* Al omitir

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-1-four-branches.png" alt-text="Captura de pantalla que muestra las cuatro ramas de una actividad.":::

### <a name="common-error-handling-mechanism"></a>Mecanismo común de control de errores

#### <a name="try-catch-block"></a>Bloque Try/Catch

En este enfoque, el cliente define la lógica de negocios y solo define la ruta de acceso _En caso de error_ para detectar cualquier error de la actividad anterior. Este enfoque representa que la canalización se realiza correctamente, si la ruta de acceso _En caso de error_ se realiza correctamente.

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-2-try-catch-definition.png" alt-text="Captura de pantalla que muestra la definición y el resultado de un bloque try/catch.":::

#### <a name="do-if-else-block"></a>Bloque Do If Else

En este enfoque, el cliente define la lógica de negocios y define las rutas _En caso de error_ y _Si es correcto_. Este enfoque representa que la canalización se realiza con errores, incluso si la ruta de acceso _En caso de error_ se realiza correctamente.

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-3-do-if-else-definition.png" alt-text="Captura de pantalla que muestra la definición y el resultado de un bloque Do If Else.":::

#### <a name="do-if-skip-else-block"></a>Bloque Do If Skip Else

En este enfoque, el cliente define la lógica de negocios y define las rutas _En caso de error_ y _Si es correcto_, con la actividad ficticia _Al omitir_ adjunta. Este enfoque representa que la canalización se realiza correctamente, si la ruta de acceso _En caso de error_ se realiza correctamente.

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-4-do-if-skip-else-definition.png" alt-text="Captura de pantalla que muestra la definición y el resultado de un bloque Do If Skip Else.":::

### <a name="summary-table"></a>Tabla de resumen

Enfoque | Qué define | Cuando la actividad se realiza correctamente, se muestra la canalización general | Cuando la actividad se realiza con errores, se muestra la canalización general
---------------------------- | ------------------- | ------------------- | -------------------
[Try-Catch](#try-catch-block) | Solo la ruta _En caso de error_ | Correcto |  Correcto
[Do-If-Else](#do-if-else-block) | Ruta _En caso de error_ y rutas _Si es correcto_ | Correcto |  Error
[Do-If-Skip-Else](#do-if-skip-else-block) |  Ruta _En caso de error_ y ruta _Si es correcto_ (con una _omisión ficticia_ al final) | Correcto |  Correcto

### <a name="how-pipeline-failure-are-determined"></a>Cómo se determina el error de canalización

Diferentes mecanismos de control de errores darán lugar a un estado diferente para la canalización: mientras que algunas canalizaciones producirán un error, otras se realizarán correctamente. Determinamos el éxito y los errores de la canalización de la manera siguiente:

* Se evalúa el resultado de todas las actividades del nivel de hoja. Si se omitió una actividad del nivel de hoja, se evalúa su actividad primaria en su lugar.
* El resultado de la canalización es correcto si y solo si todos los nodos se evaluaron correctamente.

Suponiendo que la actividad _En caso de error_ y la actividad _En caso de error ficticio_ se realizaron correctamente,

* en el enfoque [Try-Catch](#try-catch-block),

  * Cuando la actividad anterior se realiza correctamente: se omite el nodo _En caso de error_ y su nodo primario se realiza correctamente, la canalización general se realiza correctamente.
  * Cuando se produce un error en la actividad anterior: se realiza el nodo _En caso de error_, la canalización general se realiza correctamente.

* En el enfoque [Do-If-Else](#do-if-else-block),

  * Cuando la actividad anterior se realiza correctamente: el nodo _Si es correcto_ se realiza correctamente y se omite el nodo _En caso de error_ (y su nodo primario se realiza correctamente), la canalización general se realiza correctamente.
  * Cuando se produce un error en la actividad anterior: se omite el nodo _Si es correcto_ y se produce un error en su nodo primario, se produce un error en la canalización general.

* En el enfoque [Do-If-Skip-Else](#do-if-skip-else-block),

  * Cuando la actividad anterior se realiza correctamente: se omite el nodo _Al omitir ficticio_ y su nodo primario _Si es correcto_ se realiza correctamente, la otra actividad de nodo, _En caso de error_, se omite y su nodo primario se realiza correctamente, la canalización general se realiza correctamente
  * Cuando se produce un error en la actividad anterior: el nodo _En caso de error_ se realiza correctamente y _Al omitir ficticio_ se realiza correctamente, la canalización general se realiza correctamente.

## <a name="next-steps"></a>Pasos siguientes

[Métricas y alertas de Data Factory](monitor-metrics-alerts.md)

[Supervisión visual](monitor-visually.md#alerts)
