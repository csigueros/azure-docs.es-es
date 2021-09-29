---
title: ¿Qué es Azure Data Lake Storage Gen1? | Microsoft Docs
description: Información general sobre Azure Data Lake Storage Gen 1 (antes conocido como Azure Data Lake Store) y el valor que aporta en comparación con otros almacenes de datos
author: normesta
ms.service: data-lake-store
ms.topic: overview
ms.date: 04/17/2019
ms.author: normesta
ms.openlocfilehash: f105815e6c20777060c1350b9f12fbb160dbc710
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655588"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>¿Qué es Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 es un repositorio a gran escala de nivel empresarial para cargas de trabajo de análisis de macrodatos. Azure Data Lake permite capturar datos de cualquier tamaño, tipo y velocidad de ingesta en un único lugar para realizar análisis exploratorios y operativos.

Se puede acceder a Data Lake Storage Gen1 desde Hadoop (disponible con el clúster de HDInsight) mediante las API REST compatibles con WebHDFS. Está diseñado para habilitar el análisis de los datos almacenados y está optimizado para el rendimiento en escenarios de análisis de datos. Data Lake Storage Gen1 incluye todas las capacidades de nivel empresarial para la seguridad, manejabilidad, escalabilidad, confiabilidad y disponibilidad.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Principales capacidades

Entre las capacidades clave de Data Lake Storage Gen1, se incluyen las siguientes.

### <a name="built-for-hadoop"></a>Creado para Hadoop

Data Lake Storage Gen1 es un sistema de archivos de Apache Hadoop compatible con el Sistema de archivos distribuido de Hadoop (HDFS) que funciona con el ecosistema de Hadoop. Las aplicaciones o los servicios de HDInsight existentes que usen la API de WebHDFS se pueden integrar fácilmente con Data Lake Storage Gen1. Además, Data Lake Storage Gen1 expone una interfaz de REST compatible con WebHDFS para aplicaciones.

Los datos almacenados en Data Lake Storage Gen1 se pueden analizar fácilmente mediante marcos analíticos de Hadoop como MapReduce o Hive. Los clústeres de Azure HDInsight se pueden aprovisionar y configurar para que accedan directamente a datos almacenados en Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Almacenamiento ilimitado, archivos de petabytes de tamaño

Data Lake Storage Gen1 proporciona almacenamiento ilimitado y es capaz de almacenar diversos datos para análisis. No se impone ningún límite al tamaño de cuenta, el tamaño de archivo o la cantidad de datos que se pueden almacenar en un Data Lake. Los archivos individuales pueden oscilar entre kilobytes y petabytes de tamaño. Los datos se almacenan de forma duradera mediante la realización de varias copias y no hay ningún límite en cuanto al período de tiempo durante el que se pueden almacenar datos en Data Lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Rendimiento optimizado para el análisis de macrodatos

Data Lake Storage Gen1 se creó para ejecutar sistemas de análisis a gran escala que requieren un procesamiento masivo con el fin de consultar y analizar grandes cantidades de datos. Data Lake distribuye partes de un archivo entre varios servidores de almacenamiento individuales. Esto mejora el rendimiento de lectura cuando se lee el archivo en paralelo para realizar análisis de datos.

### <a name="enterprise-ready-highly-available-and-secure"></a>Preparado para la empresa: durable y con una elevada disponibilidad

Data Lake Storage Gen1 proporciona la confiabilidad y disponibilidad estándar del sector. Los recursos de datos se almacenan de forma duradera realizando copias redundantes para protegerse ante los errores inesperados.

Además, Data Lake Storage Gen1 también proporciona seguridad de nivel empresarial para los datos almacenados. Para obtener más información, consulte [Securing data in Azure Data Lake Storage Gen1](#DataLakeStoreSecurity) (Protección de los datos almacenados en Azure Data Lake Storage Gen1).

### <a name="all-data"></a>Todos los datos

Data Lake Storage Gen1 puede almacenar cualquier dato en su formato nativo, sin necesidad de transformarlo antes. Asimismo, Data Lake Storage Gen1 no requiere la definición de un esquema antes de que se carguen los datos, sino que deja que cada marco analítico interprete los datos y defina un esquema en el momento del análisis. La capacidad de almacenar archivos de formatos y tamaños arbitrarios hace posible que Data Lake Storage Gen1 administre datos estructurados, semiestructurados y no estructurados.

Los contenedores de datos de Data Lake Storage Gen1 son básicamente carpetas y archivos. Se opera en los datos almacenados mediante los SDK, Azure Portal y Azure Powershell. Si pone los datos en el almacén mediante estas interfaces y los contenedores adecuados, puede almacenar cualquier tipo de datos. Data Lake Storage Gen1 no realiza ningún control especial de datos según el tipo de datos que almacene.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Protección de datos

Data Lake Storage Gen1 usa Azure Active Directory (Azure AD) para la autenticación y listas de control de acceso (ACL) para administrar el acceso a los datos.

| Característica | Descripción |
| --- | --- |
| Authentication |Data Lake Storage Gen1 se integra con Azure AD para la administración de identidades y accesos de todos los datos almacenados en Data Lake Storage Gen1. Como resultado de la integración, Data Lake Storage Gen1 se beneficia de todas las características de Azure AD, lo que incluye la autenticación multifactor, el acceso condicional, el control de acceso basado en roles de Azure, la supervisión del uso de aplicaciones, la supervisión y las alertas de seguridad, etc. Igualmente, Data Lake Storage Gen1 es compatible con el protocolo OAuth 2.0 para la autenticación en la interfaz de REST. Vea [Autenticación de Data Lake Storage Gen1](data-lakes-store-authentication-using-azure-active-directory.md).|
| Control de acceso |Data Lake Storage Gen1 proporciona control de acceso gracias a la compatibilidad con los permisos de estilo POSIX que expone el protocolo WebHDFS. Las listas de control de acceso se pueden habilitar en la carpeta raíz, en subcarpetas y en archivos individuales. Para más información sobre cómo funcionan las listas de control de acceso en el contexto de Data Lake Storage Gen1, vea [Control de acceso en Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Cifrado |Data Lake Storage Gen1 también proporciona el cifrado de los datos que se almacenan en la cuenta. Puede especificar la configuración de cifrado mientras crea una cuenta de Data Lake Storage Gen1. Puede elegir si cifrar o no los datos. Para obtener más información, consulte el artículo de [Cifrado de datos en Data Lake Storage Gen1](data-lake-store-encryption.md). Para obtener instrucciones sobre cómo proporcionar una configuración relacionada con el cifrado, vea [Introducción a Data Lake Storage Gen1 con Azure Portal](data-lake-store-get-started-portal.md). |

Para obtener instrucciones sobre cómo proteger datos en el Data Lake Storage Gen1, consulte [Securing data in Azure Data Lake Storage Gen1](data-lake-store-secure-data.md) (Proteger los datos en Azure Data Lake Storage Gen1).

## <a name="application-compatibility"></a>Compatibilidad de aplicación

Data Lake Storage Gen1 es compatible con la mayor parte de los componentes de código abierto del ecosistema de Hadoop. También se integra perfectamente con otros servicios de Azure. Para más información sobre cómo se puede usar Data Lake Storage Gen1 con componentes de código abierto y otros servicios de Azure, use los vínculos siguientes:

- Consulte [Aplicaciones y servicios compatibles con Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) para obtener una lista de aplicaciones de código abierto interoperables con Data Lake Storage Gen1.
- Vea [Integración con otros servicios de Azure](data-lake-store-integrate-with-other-services.md) para saber cómo usar Data Lake Storage Gen1 con otros servicios de Azure para disfrutar de una gama más amplia de escenarios.
- Consulte [Scenarios for using Data Lake Storage Gen1](data-lake-store-data-scenarios.md) (Escenarios para usar Data Lake Storage Gen1) para obtener información acerca de cómo usar Data Lake Storage Gen1 en escenarios como la ingesta de datos, el procesamiento de datos, la descarga de datos y la visualización de datos.

## <a name="data-lake-storage-gen1-file-system"></a>Sistema de archivos de Data Lake Storage Gen1

En entornos de Hadoop (disponibles con el clúster de HDInsight), se puede acceder a Data Lake Storage Gen1 a través del sistema de archivos, AzureDataLakeFilesystem (adl://). Las aplicaciones y los servicios que usan adl:// pueden sacar un mayor partido de otras optimizaciones del rendimiento que actualmente no están disponibles en WebHDFS. Como resultado, Data Lake Storage Gen1 ofrece la posibilidad de elegir entre obtener el mejor rendimiento con la opción recomendada que usa adl:// o mantener el código existente usando la API de WebHDFS directamente. Azure HDInsight aprovecha completamente AzureDataLakeFilesystem para proporcionar el mejor rendimiento en Data Lake Storage Gen1.

Puede obtener acceso a los datos en Data Lake Storage Gen1 mediante `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Para más información sobre cómo acceder a los datos en Data Lake Storage Gen1, vea [Ver las propiedades de los datos almacenados](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a Data Lake Storage Gen1 con Azure Portal](data-lake-store-get-started-portal.md)
- [Introducción a Data Lake Storage Gen1 con el SDK de .NET](data-lake-store-get-started-net-sdk.md)
- [Use Azure HDInsight with Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md) (Uso de Azure HDInsight con Data Lake Storage Gen1)