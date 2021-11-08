---
title: Extensión de Visual Studio Code para Azure Video Analyzer
description: En este artículo de referencia se explica cómo usar los distintos elementos de las funcionalidades en la extensión de Visual Studio Code para Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 663f9289d6cc11a162c2325bc46381c1e2369193
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563974"
---
# <a name="visual-studio-code-extension-for-azure-video-analyzer"></a>Extensión de Visual Studio Code para Azure Video Analyzer

Azure Video Analyzer es una plataforma para facilitar la creación de programas de análisis de vídeo y la extensión de Visual Studio Code asociada es una herramienta para facilitar el aprendizaje de esa plataforma.  Este artículo es una referencia a los distintos elementos de funcionalidad que ofrece la extensión.  Trata los conceptos básicos de:

* Topologías de canalizaciones: creación, edición, eliminación y visualización del archivo JSON
* Canalizaciones en directo: creación, activación, desactivación, eliminación y visualización del archivo JSON
* Edición de una topología de canalización: módulos, parámetros, variables del sistema, conexiones y validación

Si no ha configurado la extensión para conectarse al dispositivo perimetral, siga os pasos de [Uso de la extensión de Visual Studio Code para Azure Video Analyzer](./edge/use-visual-studio-code-extension.md).

## <a name="managing-pipelines-topology"></a>Administración de topologías de canalizaciones

Para crear una topología, a la izquierda bajo el módulo, haga clic con el botón derecho en `Pipelines topologies` (Topologías de canalizaciones) y seleccione `Create pipeline topology` (Crear una topología de canalización).  Se abrirá una nueva topología en blanco.  A continuación, puede cargar una de las topologías predefinidas; para ello, selecciónela en la lista desplegable `Try sample topologies` (Probar topologías de ejemplo) de la parte superior o cree una usted mismo.  

Una vez completadas todas las áreas necesarias, deberá guardar la topología con la opción `Save` (Guardar) situada en la parte superior derecha.  Para las topologías de ejemplo, los campos necesarios se rellenarán automáticamente.  Esto hará que esté disponible para su uso con la creación de canalizaciones en directo.

Para editar una topología existente, a la izquierda, en Pipeline topologies (Topologías de canalización), haga clic con el botón derecho en el nombre de la topología y seleccione `Edit pipeline topology` (Editar topología de canalización).

Para eliminar una topología existente, a la izquierda, en Pipeline topologies (Topologías de canalización), haga clic con el botón derecho en el nombre de la topología y seleccione `Delete pipeline topology` (Eliminar topología de canalización).  Se deben quitar primero las canalizaciones en directo.

Si desea ver el código JSON subyacente de una topología existente, a la izquierda, en Pipeline topologies (Topologías de canalización), haga clic con el botón derecho en el nombre de la topología y seleccione `Show pipeline topology JSON` (Mostrar código JSON de la topología de canalización).

## <a name="live-pipelines"></a>Canalizaciones en directo

Para crear una canalización en directo, a la izquierda, en Pipeline topologies (Topologías de canalización), haga clic con el botón derecho en el nombre de la topología y seleccione `Create live pipeline` (Crear canalización en directo).  A continuación, deberá rellenar el nombre de la canalización en directo y los parámetros necesarios antes de continuar.  En la parte superior derecha, puede hacer clic en `Save` (Guardar), lo que la guardará en un estado inactivo, o en `Save and activate` (Guardar y activar), lo que iniciará la canalización en directo inmediatamente. 

Para activar una canalización en directo existente, a la izquierda, en Pipeline topologies (Topologías de canalización), haga clic con el botón derecho en el nombre de la canalización en directo y seleccione `Activate live pipeline` (Activar canalización en directo).

Para desactivar una instancia en ejecución, a la izquierda, en Pipeline topologies (Topologías de canalización), haga clic con el botón derecho en la canalización en directo y seleccione `Deactivate live pipeline` (Desactivar canalización en directo).  Esto no eliminará la canalización en directo.

Para eliminar una canalización en directo existente, a la izquierda, en Pipeline topologies (Topologías de canalización), haga clic con el botón derecho en la canalización en directo y seleccione `Delete live pipeline` (Eliminar canalización en directo).  No se puede eliminar una canalización en directo activa.

Si desea ver el código JSON subyacente de una canalización en directo existente, a la izquierda, en Pipeline topologies (Topologías de canalización), haga clic con el botón derecho en el nombre de la canalización en directo y seleccione `Show live pipeline JSON` (Mostrar código JSON de la canalización en directo).

## <a name="editing-a-topology"></a>Edición de una topología 

Las topologías de canalización se construyen a partir de una variedad de piezas. Puede obtener información sobre estas piezas en el documento de concepto [Canalizaciones](pipeline.md). Esta sección trata sobre las partes de la interfaz que le ayudan a crear o editar una topología.

### <a name="modules"></a>Módulos

Los módulos están disponibles a la izquierda y se agrupan en orígenes, procesadores y receptores.  Para agregar uno a la topología, arrástrelo al área de edición.

Si quiere tener otro módulo del mismo tipo con los mismos parámetros de inicio, puede duplicarlo; para ello, haga clic con el botón derecho en el módulo y seleccione `Copy` (Copiar).

Si desea quitar un módulo de la topología, haga clic con el botón derecho en el módulo y seleccione `Delete` (Eliminar).

### <a name="parameters"></a>Parámetros

Para editar los atributos de un módulo, haga clic con el botón izquierdo en el módulo en el área de edición.  Se mostrará un panel a la derecha con una lista de todos los elementos.  Los elementos obligatorios están marcados con un asterisco rojo.  Hay una breve descripción en el texto de información junto al nombre de cada elemento.

Puesto que las topologías son plantillas eficaces desde las que puede crear varias canalizaciones en directo, hay algunos elementos que querrá rellenar en el momento de la creación.  Puede hacerlo con parámetros.  Para activarlos, haga clic con el botón derecho en los tres puntos a la derecha del atributo y seleccione `Parameterize` (Parametrizar).  Se abrirá una ventana.

En la ventana de parametrización, puede crear un nuevo parámetro que funcionará como un valor que se rellena en la creación de la canalización en directo o se selecciona uno existente.  La creación de uno nuevo requiere que rellene el nombre y el tipo.  Opcionalmente, puede especificar un valor predeterminado si solo es necesario cambiarlo a veces.  Cuando se crea una canalización en directo, solo se requerirán los parámetros sin un valor predeterminado.  Cuando termine, haga clic en `Set` (Establecer).

Si desea administrar los parámetros existentes, puede hacerlo con la opción `Manage parameters` (Administrar parámetros) de la parte superior.  El panel que aparece le permite agregar nuevos parámetros y editar o eliminar los existentes.

### <a name="system-variable"></a>Variable del sistema

Al crear una serie de canalizaciones en directo, es probable que haya casos en los que quiera usar variables para ayudar a dar nombre a los archivos o las salidas.  Por ejemplo, puede que desee asignar un nombre a un clip de vídeo con el nombre de la canalización en directo y la fecha y hora para saber de dónde procede y en qué momento.  Video Analyzer proporciona tres variables del sistema que puede usar en los módulos para ayudarle con esto.

| Variable del sistema        | Descripción                                                  | Ejemplo              |
| :--------------------- | :----------------------------------------------------------- | :------------------- |
| System.Runtime.DateTime        | Fecha y hora UTC en formato compatible con archivos ISO8601 (representación básica AAAAMMDDThhmmss). | 20200222T173200Z     |
| System.TopologyName    | Nombre proporcionado por el usuario de la topología de canalización en ejecución.          | IngestAndRecord      |
| System.PipelineName    | Nombre proporcionado por el usuario de la canalización en directo en ejecución.          | camera001            |

### <a name="connections"></a>Conexiones 

Al crear una topología, deberá conectar los distintos módulos.  Esto se lleva a cabo con conexiones.  Desde el círculo del borde de un módulo, arrastre hasta el círculo del módulo siguiente al que desea que fluyan los datos.  Esto genera una conexión.

De manera predeterminada, las conexiones envían los datos de vídeo de un módulo a otro. Si solo quiere enviar datos de audio o datos de aplicación, puede hacer clic en la conexión y editar los tipos de salida. Los tipos de datos seleccionables incluyen vídeo, audio y aplicación. SI no selecciona ningún tipo de salida, se enviarán todos los datos aplicables desde el nodo remitente.
