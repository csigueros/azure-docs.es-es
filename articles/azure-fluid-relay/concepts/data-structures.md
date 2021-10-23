---
title: Estructuras de datos distribuidos
description: Las estructura de datos distribuidos son los bloques de creación de las aplicaciones de Fluid
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/data-structures/overview/
ms.openlocfilehash: c79fea26de7cda6d3097c2a8c9403420451fe665
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661669"
---
# <a name="distributed-data-structures"></a>Estructuras de datos distribuidos

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

Fluid Framework proporciona a los desarrolladores estructuras de datos distribuidos (DDS) que garantizan automáticamente que todos los cliente conectados tengan acceso al mismo estado. Las API que proporcionan los DDS están diseñadas que las conozcan los programadores que han usado antes estructuras de datos comunes.

> [!NOTE]
> En este artículo se da por supuesto que conoce la [introducción a las estructuras de datos distribuidos](https://fluidframework.com/docs/build/dds/) en fluidframework.com.

Las estructuras de datos distribuidos se comportan como estructuras de datos locales. El código puede agregarle datos, quitarlos, actualizarlos, etc. Sin embargo, una estructura de datos distribuidos no es un objeto local. Otros clientes que exponen el mismo contenedor principal de la estructura de datos distribuidos también pueden cambiar una estructura de datos distribuidos. Dado que los usuarios pueden cambiar simultáneamente la misma estructura de datos distribuidos, es preciso tener en cuenta qué estructura se debe usar para modelar los datos.

> [!NOTE]
> **Significado de "simultáneamente"**
>
> Se considera que dos o más clientes hacen un cambio *simultáneamente* si cada uno de ellos realiza un cambio antes de recibir los cambios de los demás desde el servidor.

La elección de la estructura de datos correcta para su escenario puede mejorar el rendimiento y la estructura de código de la aplicación.

Las estructuras de datos distribuidos varían por tres características:

- **Estructura de datos básica**:por ejemplo, un par clave-valor, una secuencia o una cola.
- **Autonomía del cliente**: una DDS *optimista* permite a cualquier cliente cambiar cualquier valor de forma unilateral y el nuevo valor se retransmite a los restantes clientes. Pero una DDS de *consenso* solo permite aquellos cambio que hayan sido aceptados por otros clientes mediante un proceso de consenso.
- **Directiva de combinación**: la directiva que determina cómo se resuelven los cambios en conflicto de los clientes.

A continuación se enumeran las estructuras de datos y se describe en qué momentos y situaciones pueden ser más útiles.

## <a name="key-value-data"></a>Datos de clave-valor

Estas DDS se usan para almacenar datos de clave-valor. Son optimistas y usan una directiva de combinación del tipo el último en escribir gana. Aunque el valor de un par puede ser un objeto complejo, el valor de cualquier par determinado no se puede editar directamente; el valor completo se debe reemplazar por un nuevo valor que contenga las ediciones deseadas, de forma completa.

- **SharedMap**: una estructura de datos de clave-valor básica.

### <a name="key-value-scenarios"></a>Escenarios clave-valor

Las estructuras de datos de clave-valor son la opción más común en muchos escenarios.

- Datos de preferencias del usuario.
- Estado actual de una encuesta.
- La configuración de una vista.

### <a name="common-issues-and-best-practices-for-key-value-ddses"></a>Problemas comunes y procedimientos recomendados para estructuras de datos distribuidos de clave-valor

- El almacenamiento de un contador en SharedMap tendrá un comportamiento inesperado. En su lugar, utilice SharedCounter.
- El almacenamiento de matrices, listas o registros en una entrada de clave-valor puede provocar un comportamiento inesperado porque los usuarios no pueden modificar de forma colaborativa las distintas partes de las entradas. Intente almacenar los datos de la matriz o de la lista de SharedSequence o SharedInk.
- El almacenamiento de gran cantidad de datos en una entrada de clave-valor puede provocar problemas de rendimiento o de fusión mediante combinación. Cada actualización se realizará en el valor completo, en lugar de combinar dos actualizaciones. Pruebe a dividir los datos entre varias claves.

## <a name="sequences"></a>Secuencias

Estas DDS se usan para almacenar datos secuenciales. Son optimistas. Las estructuras de datos secuenciales son útiles cuando es necesario agregar o quitar datos en un índice especificado de una lista o matriz. A diferencia de las estructuras de datos de clave-valor, las secuencias tienen un orden secuencial y pueden controlar las inserciones simultáneas de varios usuarios.

- **SharedNumberSequence**: una secuencia de números.
- **SharedObjectSequence**: una secuencia de objetos sin formato.

### <a name="sequence-scenarios"></a>Escenarios de secuencias

- Listas
- Escalas de tiempo

### <a name="common-issues-and-best-practices-for-sequence-ddses"></a>Problemas comunes y procedimientos recomendados de las estructuras de datos distribuidos de secuencias

- Almacene solo los datos inmutables como un elemento de una secuencia. La única manera de cambiar el valor de un elemento es quitarlo primero de la secuencia y, después, insertar un nuevo valor en la posición en la que estaba el valor anterior. Pero como otros clientes pueden insertar y quitar valores, no hay ninguna manera confiable de obtener el nuevo valor en la posición deseada.

## <a name="strings"></a>Cadenas

La estructura de datos distribuidos SharedString se usa para datos de texto no estructurados que se pueden editar de forma colaborativa. Es optimista.

- `SharedString`: una estructura de datos para controlar el texto colaborativo.

### <a name="string-scenarios"></a>Escenarios de cadena

- Editores de texto

## <a name="see-also"></a>Consulte también

Para más información sobre las DDS y cómo usarlas, consulte las siguientes secciones de fluidframework.com:

- [Introducción a las estructuras de datos distribuidos](https://fluidframework.com/docs/build/dds/).
- [Tipos de estructuras de datos distribuidos](https://fluidframework.com/docs/data-structures/overview/).
