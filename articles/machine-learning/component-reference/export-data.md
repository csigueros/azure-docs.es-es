---
title: 'Exportación de datos: referencia de componente'
titleSuffix: Azure Machine Learning
description: Use el componente Exportación de datos del diseñador de Azure Machine Learning para guardar resultados y datos intermedios fuera de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/19/2021
ms.openlocfilehash: 60821daf863e90ecc4f4f14db2197632d965740c
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566065"
---
# <a name="export-data-component"></a>Componente Exportación de datos

En este artículo se describe un componente del diseñador de Azure Machine Learning.

Use este componente para guardar resultados, datos intermedios y datos de trabajo de las canalizaciones en destinos de almacenamiento en la nube. 

Este componente permite la exportación de los datos a los siguientes servicios de datos en la nube:

- Azure Blob Container
- Recurso compartido de archivos de Azure
- Azure Data Lake Storage Gen1
- Azure Data Lake Storage Gen2
- Azure SQL Database

Antes de exportar los datos, debe registrar un almacén de datos en el área de trabajo de Azure Machine Learning. Para más información, consulte [Acceso a los datos en los servicios de almacenamiento de Azure](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Procedimiento para configurar la exportación de datos

1. Agregue el componente **Exportación de datos** a la canalización del diseñador. Puede encontrar este componente en la categoría **Entrada y salida**.

1. Conecte **Exportación de datos** al componente que contiene los datos que quiere exportar.

1. Seleccione **Exportación de datos** para abrir el panel **Propiedades**.

1. En **Almacén de datos**, seleccione un almacén de datos existente en la lista desplegable. También puede crear un nuevo almacén de datos. Para ver cómo, consulte [Acceso a los datos en los servicios de almacenamiento de Azure](../how-to-access-data.md).

    > [!NOTE]
    > No se admite la exportación de datos de un tipo determinado a una columna de SQL Database especificada como otro tipo de datos. No es necesario que la tabla de destino exista primero.

1. La casilla **Regenerar salida** decide si se va a ejecutar el componente para regenerar la salida en tiempo de ejecución. 

    No está activada de manera predeterminada, lo que significa que si el componente se ha ejecutado con los mismos parámetros anteriormente, el sistema reutiliza la salida de la última ejecución para reducir el tiempo de ejecución. 

    Si está activada, el sistema vuelve a ejecutar el componente para regenerar la salida.

1. Defina la ruta del almacén de datos en el que se encuentran los datos. La ruta de acceso es relativa. Tome `data/testoutput` como ejemplo, que significa que los datos de entrada de **Exportación de datos** se exportan a `data/testoutput` en el almacén de datos establecido en la **Configuración de salida** del componente.

    > [!NOTE]
    > No se permiten las rutas de acceso vacías ni de **dirección URL**.


1. Para **Formato de archivo**, seleccione el formato en el que se deben almacenar los datos.
 
1. Envíe la canalización.

## <a name="limitations"></a>Limitaciones

Debido a la limitación del acceso al almacén de datos, si la canalización de inferencia contiene el componente **Exportación de datos**, se quita automáticamente cuando se implementa en el punto de conexión en tiempo real.

## <a name="next-steps"></a>Pasos siguientes

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 
