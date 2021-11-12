---
title: Uso de las características de accesibilidad del diseñador
titleSuffix: Azure Machine Learning
description: Obtenga información sobre los métodos abreviados de teclado y las características de accesibilidad del lector de pantalla disponibles en el diseñador.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: lagayhar
author: lgayhardt
ms.date: 01/09/2020
ms.custom: designer
ms.openlocfilehash: dfa3b5630b09e2a3e3411afc7d77b76f1b651e72
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555008"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer"></a>Uso del diseñador de Azure Machine Learning mediante el teclado

Aprenda a usar el diseñador de Azure Machine Learning con un teclado y un lector de pantalla. Para obtener una lista de métodos abreviados de teclado que funcionen en cualquier lugar de Azure Portal, consulte [Métodos abreviados de teclado de Azure Portal](../azure-portal/azure-portal-keyboard-shortcuts.md)

Este flujo de trabajo se ha probado con [Narrator](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) y [JAWS](https://www.freedomscientific.com/products/software/jaws/), pero debería funcionar con otros lectores de pantalla estándar.

## <a name="navigate-the-pipeline-graph"></a>Desplazamiento por el gráfico de canalización

El gráfico de canalización se organiza como lista anidada. La lista externa es una lista de componentes, que describe todos los componentes del grafo de la canalización. La lista interna es una lista de conexiones, que describe todas las conexiones de un componente concreto.  

1. En la lista de componentes, use la tecla de flecha para cambiar los componentes.
1. Use el tabulador para abrir la lista de conexiones del componente de destino.
1. Use la tecla de dirección para cambiar entre los distintos puertos de conexión del componente.
1. Use "G" para ir al componente de destino.

## <a name="edit-the-pipeline-graph"></a>Edición del gráfico de canalización

### <a name="add-a-component-to-the-graph"></a>Adición de un componente al grafo

1. Use Ctrl+F6 para cambiar el foco del lienzo al árbol de componentes.
1. Busque el componente deseado en el árbol de componentes mediante el control de vista de árbol estándar.

### <a name="edit-a-component"></a>Edición de un componente

Para conectar un componente a otro componente:

1. Use Ctrl+Mayús+H cuando el destino sea un componente de la lista de componentes, para abrir el asistente de conexión.
1. Edite los puertos de conexión del componente.

Para ajustar las propiedades del componente:

1. Use Ctrl+Mayús+E cuando el destino sea un componente, para abrir las propiedades del componente.
1. Edite las propiedades de los componentes.

## <a name="navigation-shortcuts"></a>Métodos abreviados de navegación

| Pulsación de tecla | Descripción |
|-|-|
| Ctrl + F6 | Alterna el foco entre el lienzo y el árbol de componentes |
| Ctrl + F1   | Abre la tarjeta de información cuando se centra en un nodo del árbol de componentes |
| Ctrl + Mayús + H | Abre el asistente de conexión cuando el foco está en un nodo |
| Ctrl + Mayús + E | Abre las propiedades del componente cuando el foco está en un nodo |
| Ctrl + G | Mueve el foco al primer nodo con error si se produjo algún error al ejecutar la canalización |

## <a name="action-shortcuts"></a>Accesos directos de acciones

Use los siguientes accesos directo con la clave de acceso. Para más información sobre las claves de acceso, consulte https://en.wikipedia.org/wiki/Access_key.

| Pulsación de tecla | Acción |
|-|-|
| Clave de acceso + R | Ejecutar |
| Clave de acceso + P | Publicar |
| Clave de acceso + C | Clonar |
| Clave de acceso + D | Implementación |
| Clave de acceso + I | Crear o actualizar canalización de inferencia |
| Clave de acceso + B | Crear o actualizar canalización de inferencias por lotes |
| Clave de acceso + K | Abrir la lista desplegable "Create inference pipeline" (Crear canalización de inferencia) |
| Clave de acceso + U | Abrir la lista desplegable "Update inference pipeline" (Actualizar canalización de inferencia) |
| Clave de acceso + M | Abrir la lista desplegable más (...) |

## <a name="next-steps"></a>Pasos siguientes

- [Activación del contraste alto o cambio de tema](../azure-portal/set-preferences.md#choose-a-theme-or-enable-high-contrast)
- [Herramientas relacionadas con la accesibilidad en Microsoft](https://www.microsoft.com/accessibility)
