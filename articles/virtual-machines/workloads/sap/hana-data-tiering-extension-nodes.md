---
title: Capas de datos y nodos de extensión para SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Aprenda sobre las capas de datos y los nodos de extensión para SAP HANA en Azure (instancias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/17/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c9ab5f48aac13153cfc510ce8b7ca5b7b8cc3f41
ms.sourcegitcommit: a9f131fb59ac8dc2f7b5774de7aae9279d960d74
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110189806"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Uso de los nodos de extensión y las capas de datos de SAP HANA

SAP admite un modelo de capas de datos para SAP Business Warehouse (BW) con diferentes versiones de SAP NetWeaver y SAP BW/4HANA. Para más información sobre el modelo de capas de datos, consulte [SAP BW/4HANA y SAP BW en HANA con nodos de extensión de SAP HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).

Con HANA (instancias grandes), puede usar la configuración de la opción 1 de los nodos de extensión de SAP HANA, como se explica en las preguntas más frecuentes y los documentos del blog de SAP. Las configuraciones de la opción 2 se pueden establecer con las siguientes SKU de HANA (instancias grandes): S72m, S192, S192m, S384 y S384m.

## <a name="advantages-of-sap-hana-extension-nodes"></a>Ventajas de los nodos de extensión de SAP HANA

Los nodos de extensión de SAP HANA, ya sea la opción 1 o 2, son una manera sencilla de hacer un mejor uso de la memoria de SAP HANA. Las ventajas de los nodos de extensión de SAP HANA se ven claramente si se examinan las directrices de tamaño de SAP. Estos son algunos ejemplos:

- Las directrices de tamaño de SAP HANA suelen requerir que la cantidad de volumen de datos sea el doble que la memoria. Al ejecutar la instancia de SAP HANA con datos de acceso frecuente, solo el 50 % o menos de la memoria almacena datos. Lo ideal es que la memoria restante se reserve para que SAP HANA haga su trabajo.
- Eso significa que en una unidad S192 de HANA (instancias grandes) con 2 TB de memoria que ejecuta una base de datos de SAP BW, solo dispone de 1 TB de volumen de datos.
- Si usa otro nodo de extensión de SAP HANA de la opción 1, también un SKU S192 de HANA (instancias grandes), tendría 2 TB adicionales de volumen de datos. En la configuración de la opción 2, obtiene otros 4 TB adicionales para el volumen de datos de acceso menos frecuente. En comparación con el nodo de acceso frecuente, la capacidad de memoria total del nodo de extensión de "acceso menos frecuente" puede usarse para almacenar datos para la opción 1. Se puede usar el doble de memoria para el volumen de datos en la configuración de la opción 2 del nodo de extensión de SAP HANA.
- Acabará con una capacidad de 3 TB para los datos y una relación entre acceso frecuente y menos frecuente de 1:2 para la opción 1. Con la configuración de la opción 2 del nodo de extensión tendrá 5 TB de datos y una relación de 1:4.

Cuanto mayor sea el volumen de datos en comparación con la memoria, más posibilidad habrá de que los datos de acceso menos frecuente que pide se almacenen en el disco.

## <a name="next-steps"></a>Pasos siguientes

Conozca el modelo de operaciones de SAP HANA en Azure (instancias grandes) y sus responsabilidades.

> [!div class="nextstepaction"]
> [Modelo de operaciones y responsabilidades](hana-operations-model.md)
