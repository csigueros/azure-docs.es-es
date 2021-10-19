---
title: Administración de contenedores de Fluid
description: Información general sobre cómo administrar contenedores en el servicio Azure Fluid Relay.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 934122f4cb952a4be915c55c2555e336705a71c2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710365"
---
# <a name="managing-fluid-containers"></a>Administración de contenedores de Fluid

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

Un contenedor es la unidad atómica de almacenamiento del servicio Azure Fluid Relay y representa los datos almacenados de una sesión de Fluid, incluidas las operaciones y las instantáneas. El entorno en tiempo de ejecución de Fluid usa el contenedor para rehidratar el estado de una sesión de Fluid cuando un usuario se une por primera vez o vuelve después de haber salido.

Al compilar una aplicación con Fluid Framework, hay varias cosas que debe tener en cuenta respecto a la creación y administración de contenedores, como se resume en este diagrama.

:::image type="content" source="../images/fluid-service-architecture-ownership.jpg" alt-text="Ilustración de la arquitectura de un servicio de Fluid y qué partes pertenecen a desarrolladores o a Microsoft.":::

## <a name="key-concepts"></a>Conceptos clave

### <a name="container-permissions"></a>Permisos del contenedor 

En la mayoría de los casos, los desarrolladores necesitarán administrar un inventario de contenedores y permisos de contenedor. Esto incluiría información sobre quién tiene acceso a los contenedores, así como metadatos como el nombre descriptivo del contenedor.

### <a name="accessing-containers"></a>Acceso a contenedores

Se hace referencia a los contenedores por su identificador. Para que un usuario pueda crear o abrir un contenedor, debe solicitar un JWT que el entorno en tiempo de ejecución de Fluid usará al comunicarse con el servicio Azure Fluid Relay. Cualquier proceso con un JWT válido puede acceder a un contenedor. Es responsabilidad del desarrollador generar JWT para el acceso a contenedores, lo que les otorga el control de la lógica de negocios a la hora de gestionar el acceso según corresponda en su escenario. El servicio Azure Fluid Relay no tiene información sobre qué usuarios deben tener acceso a un contenedor. Para obtener más información sobre este tema, consulte el [Contrato de token de Azure Fluid Relay](../how-tos/fluid-json-web-token.md).

> [!NOTE]
> El campo **documentID** de JWT corresponde al identificador del contenedor de Fluid.

### <a name="container-naming"></a>Nomenclatura de los contenedores

El servicio Azure Fluid Relay asigna un nombre a los contenedores en el momento de su creación. La acción Crear devuelve un nombre de contenedor en forma de GUID que se debe usar más tarde para abrir el contenedor. En la mayoría de los casos, es recomendable que los desarrolladores almacenen el GUID del identificador del contenedor, junto con un nombre descriptivo, en su propio almacén de datos para facilitar los flujos de detección de contenedores. 

### <a name="container-discovery"></a>Detección de contenedores

Los desarrolladores son responsables de cualquier experiencia y lógica de negocios relacionada con la detección de contenedores existentes por parte de los usuarios. Esto podría tener la forma de una lista de contenedores que se puede explorar en función de la participación del usuario en la sesión de Fluid, el uso compartido y directo de contenedores entre usuarios o la asignación mediante programación de contenedores a artefactos o procesos existentes.

## <a name="see-also"></a>Consulte también

- [Introducción a la arquitectura de Azure Fluid Relay](architecture.md)
- [Estructuras de datos distribuidos](data-structures.md)
- [Contrato de token de Azure Fluid Relay](../how-tos/fluid-json-web-token.md)
