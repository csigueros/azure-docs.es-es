---
title: 'Caso de uso de Factoría de datos: recomendaciones del producto'
description: Obtenga información acerca de un caso de uso que se implementan mediante Azure Data Factory junto con otros servicios.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: a9a194fb1f1db5723643f9f73a6c2018f5daa59f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665347"
---
# <a name="use-case---product-recommendations"></a>Caso de uso: recomendaciones de productos
Azure Data Factory es uno de los muchos servicios que se usan para implementar el conjunto de aplicaciones Cortana Intelligence de aceleradores de soluciones.  Consulte la página [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) para más información sobre este conjunto de aplicaciones. En este documento se describe un caso de uso común que los usuarios de Azure ya resolvieron e implementaron mediante Azure Data Factory y otros servicios del componente Cortana Intelligence.

## <a name="scenario"></a>Escenario
Los distribuidores en línea normalmente desean conseguir que sus clientes compren los productos presentando los productos que probablemente más les interesen y, por tanto, es más probable que compren. Para lograr esto, los distribuidores en línea deben personalizar la experiencia en línea de sus usuarios mediante recomendaciones de productos personalizadas para ese usuario concreto. Estas recomendaciones personalizadas se van a realizar en función de los datos de su comportamiento de compra actual e histórico, la información de productos, las marcas recién introducidas y los datos de segmentación de productos y clientes.  Además, se pueden proporcionar recomendaciones de productos a usuarios basadas en el análisis del comportamiento de uso general de todos los usuarios combinados.

El objetivo de estos distribuidores es optimizar para conseguir conversiones de clic a venta y obtener mayores ingresos por ventas.  Para conseguirlo, se proporcionan recomendaciones de productos contextuales, basadas en el comportamiento en función de los intereses y las acciones del cliente. En este caso práctico, usaremos distribuidores en línea como ejemplo de empresas que se quieren optimizar para sus clientes. Sin embargo, estos principios se aplican a cualquier empresa que quiera atraer a sus clientes a sus productos y servicios y mejorar su experiencia de compra con recomendaciones de productos personalizadas.

## <a name="challenges"></a>Desafíos
Existen muchos desafíos a los que se enfrentan los distribuidores en línea al intentar implementar este tipo de caso de uso. 

En primer lugar, se deben ingerir datos de diferentes tamaños y formas procedentes de varios orígenes de datos, tanto locales como en la nube. Estos datos incluyen datos de productos, datos históricos del comportamiento de los clientes y datos de los usuarios cuando exploran el sitio comercial en línea. 

En segundo lugar, las recomendaciones de productos personalizadas deben calcularse y predecirse de forma razonable y precisa. Además del producto, la marca, el comportamiento del cliente y los datos del explorador, los distribuidores en línea también necesitan incluir comentarios de los clientes sobre compras pasadas como factor para determinar las mejores recomendaciones de productos para un usuario. 

En tercer lugar, las recomendaciones deben poder entregarse inmediatamente al usuario para proporcionar una exploración y experiencia de compra perfectas, y proporcionar las recomendaciones más recientes y relevantes. 

Por último, los distribuidores necesitan medir la eficacia de su enfoque mediante el seguimiento de las ventas totales, las ventas cruzadas y la conversión de clic a ventas, así como realizar ajustes en sus recomendaciones futuras.

## <a name="solution-overview"></a>Información general de la solución
Este caso práctico de ejemplo fue resuelto e implementado por usuarios reales de Azure Data Factory y otros servicios del componente Cortana Intelligence, como [HDInsight](https://azure.microsoft.com/services/hdinsight/) y [Power BI](https://powerbi.microsoft.com/).

El distribuidor en línea usa un almacén de blobs de Azure, un servidor local de SQL Server, Azure SQL Database y un data mart relacional como opciones de almacenamiento de datos a lo largo del flujo de trabajo.  El almacén de blobs contiene información del cliente, los datos de comportamiento de cliente y los datos de información de los productos. Los datos de información de los productos incluyen información de la marca de producto y un catálogo de productos almacenado localmente en una instancia de Azure Synapse Analytics. 

Todos los datos se combinan e introducen en un sistema de recomendación de productos para ofrecer recomendaciones personalizadas según los intereses y las acciones del cliente, mientras el usuario examina los productos en el catálogo del sitio web del distribuidor. Los clientes también ven productos que puedan estar relacionados con el producto que miran de acuerdo con los patrones de uso general del sitio web que no están relacionados con ningún otro usuario.

:::image type="content" source="./media/data-factory-product-reco-usecase/diagram-1.png" alt-text="diagrama del caso de uso":::

Diariamente se generan gigabytes de archivos de registro web sin formato desde el sitio web del distribuidor en línea como archivos semiestructurados. Los archivos de registro web sin procesar y la información del catálogo de clientes y productos se introducen periódicamente en una cuenta de Azure Blog Storage mediante el movimiento de datos como servicio implementado globalmente de Data Factory. Los archivos de registro sin procesar del día se dividen (por año y mes) en el almacenamiento de blobs para el almacenamiento a largo plazo.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) se usa para particionar los archivos de registro sin procesar en el almacén de blobs y procesar los registros ingeridos a escala mediante scripts de Hive y Pig. Los datos de registro web particionados se procesan luego para extraer las entradas que necesita un sistema de recomendaciones de aprendizaje automático para generar las recomendaciones de productos personalizadas.

El sistema de recomendaciones usado para el aprendizaje automático en este ejemplo es una plataforma de recomendaciones de aprendizaje automático de código abierto de [Apache Mahout](https://mahout.apache.org/).  Cualquier modelo personalizado o de [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) se puede aplicar a este escenario.  El modelo Mahout se usa para predecir la similitud entre elementos en el sitio web del distribuidor de acuerdo con los patrones de uso general y para generar las recomendaciones personalizadas basadas en el usuario específico.

Por último, el conjunto de resultados de las recomendaciones de productos personalizadas se mueve a un data mart relacional para su consumo por el sitio web del distribuidor.  Otra aplicación también podría acceder al conjunto de resultados directamente desde el almacenamiento de blobs, o bien el conjunto de resultados podría moverse a almacenes adicionales para otros consumidores y casos de uso.

## <a name="benefits"></a>Ventajas
Al optimizar su estrategia de recomendación de productos y alinearla con los objetivos empresariales, la solución cumplió los objetivos de marketing y comercialización de los distribuidores en línea. Además, pudieron operacionalizar y administrar el flujo de trabajo de recomendaciones de productos de forma confiable, eficiente y rentable. El enfoque les facilitó la actualización de su modelo y el ajuste de su efectividad en función de las medidas de conversión de clic a ventas. Mediante Azure Data Factory, pudieron abandonar la tan costosa y laboriosa administración manual de los recursos en la nube y pasar a su administración a petición. Por lo tanto, consiguieron ahorrar tiempo y dinero y reducir el tiempo de implementación de la solución. Las vistas de linaje de datos y el estado de funcionamiento del servicio ahora se pueden visualizar y solucionar problemas fácilmente con la supervisión de factoría de datos intuitiva y la administración de interfaz de usuario disponible en el Portal de Azure. Su solución ahora se puede programar y administrar para que los datos terminados se generen y proporcionen a sus usuarios de forma confiable, y los datos y las dependencias de procesamiento se administran automáticamente sin intervención humana.

Gracias a esta experiencia de compra personalizada, el distribuidor en línea creó una experiencia del cliente más competitiva y atractiva y, por tanto, que aumenta la satisfacción general del cliente y las ventas.

