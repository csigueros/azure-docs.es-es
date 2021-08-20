---
title: Introducción a Azure Purview (versión preliminar)
description: En este artículo se proporciona información general de Azure Purview, incluidas sus características y los problemas que soluciona. Azure Purview permite a cualquier usuario registrar, detectar, conocer y consumir orígenes de datos.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 11/30/2020
ms.openlocfilehash: a4ca7c318dc1ac9d135c43c6d063a0f8a45276fe
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729767"
---
# <a name="what-is-azure-purview"></a>¿Qué es Azure Purview?

> [!IMPORTANT]
> Azure Purview se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

Azure Purview es un servicio de gobernanza de datos unificado que le ayuda a administrar y controlar los datos locales, multinube y de software como servicio (SaaS). Cree fácilmente un mapa holístico actualizado del panorama de sus datos con detección automatizada de datos, clasificación de datos confidenciales y linaje de datos de un extremo a otro. Permita a los consumidores de datos encontrar datos valiosos y confiables.

Azure Purview Data Map proporciona la base para la detección de datos y una gobernanza eficaz de los datos. Purview Data Map es un servicio PaaS nativo de la nube que captura metadatos acerca de los datos empresariales presentes en los sistemas de operaciones y análisis locales y en la nube. Purview Data Map se mantiene actualizado automáticamente con un sistema de clasificación y análisis automatizados integrado. Los usuarios empresariales pueden configurar y usar Purview Data Map a través de una interfaz de usuario intuitiva, mientras que los desarrolladores pueden interactuar mediante programación con Data Map mediante las API de Apache Atlas 2.0 de código abierto.

Azure Purview Data Map da servicio al catálogo de datos de Purview y la información de datos de Purview como experiencias unificadas en Purview Studio.
 
Con Purview Data Catalog, tanto los usuarios técnicos como los empresariales pueden encontrar fácilmente datos relevantes mediante una experiencia de búsqueda con filtros basados en diversas lentes, como términos del glosario, clasificaciones, etiquetas de confidencialidad, etc. En el caso de los expertos en la materia, administradores de datos y personal responsable, Purview Data Catalog proporciona características de protección de datos como la administración de un glosario empresarial y la capacidad de automatizar el etiquetado de los recursos de datos con los términos del glosario. Los consumidores y productores de datos también pueden realizar un seguimiento visual del linaje de los recursos de datos, desde los sistemas operativos locales, pasando por su movimiento, transformación y enriquecimiento con varios sistemas de procesamiento y almacenamiento de datos en la nube, hasta su consumo en un sistema de análisis como Power BI.

Con la información de los datos de Purview, tanto los responsables de los datos como los responsables de la seguridad pueden obtener una vista aérea y una comprensión a primera vista de qué datos se exploran activamente, dónde están los datos confidenciales y cómo se mueven.

## <a name="discovery-challenges-for-data-consumers"></a>Desafíos de detección para los consumidores de datos

Tradicionalmente, la detección de orígenes de datos empresariales ha sido un proceso orgánico basado en el conocimiento colectivo. Este enfoque presenta muchos desafíos a las empresas que desean el máximo partido de sus recursos de información:

* Al no haber ubicación central en la que se registren los orígenes de datos, es posible que los usuarios no conozcan un origen de datos, salvo que entren en contacto con él como parte de otro proceso.
* Salvo que los usuarios conozcan la ubicación de un origen de datos, no se podrán conectar a ellos mediante una aplicación cliente. Las experiencias de consumo de datos requieren que los usuarios conozcan la cadena de conexión o la ruta de acceso.
* Los usuarios no pueden ver el uso previsto de los datos, salvo que conozcan la ubicación de la documentación de un origen de datos. Los orígenes de datos y la documentación pueden encontrarse en varios lugares y consumirse a través de distintos tipos de experiencias.
* Si los usuarios tienen dudas acerca de un recurso de información, deben buscar al experto o equipos responsables de los datos e interactuar con ellos sin conexión. No hay ninguna conexión explícita entre los datos y los expertos que tienen perspectivas sobre su uso.
* Salvo que los usuarios conozcan el proceso de solicitud de acceso al origen de datos, la detección del origen de datos y su documentación no les ayudará a acceder a los datos.

## <a name="discovery-challenges-for-data-producers"></a>Desafíos de detección para los productores de datos

Aunque los consumidores de datos se enfrentan a los desafíos mencionados, los usuarios responsables de producir y mantener recursos de información se enfrentan a los suyos propios:

* A menudo, la anotación de orígenes de datos con metadatos descriptivos es un esfuerzo baldío. Las aplicaciones cliente suelen ignorar las descripciones que se almacenan en el origen de datos.
* La creación de documentación para los orígenes de datos puede resultar difícil y mantener la documentación sincronizada con los orígenes de datos es una responsabilidad continua. Es posible que los usuarios no confíen en aquella documentación que se percibe como obsoleta.
* La creación y el mantenimiento de la documentación de los orígenes de datos son tareas complejas y lentas. Que dicha documentación esté disponible para todos los que usen el origen de datos también puede serlo.
* La restricción del acceso a los orígenes de datos y la garantía de que los consumidores de datos saben cómo solicitar el acceso suponen un desafío continuo.

Cuando dichos retos se combinan, suponen una barrera importante para las empresas que desean fomentar y promover el uso y conocimiento de los datos empresariales.

## <a name="discovery-challenges-for-security-administrators"></a>Desafíos de detección para los administradores de seguridad

Es posible que los usuarios responsables de garantizar la seguridad de los datos de su organización tengan cualquiera de los desafíos enumerados anteriormente como consumidores y productores de datos, así como los siguientes desafíos adicionales:

* Los datos de una organización crecen, se almacenan y se comparten constantemente en nuevas direcciones. La tarea de detectar, proteger y controlar los datos confidenciales nunca termina. Desea asegurarse de que el contenido de su organización se comparte con las personas, las aplicaciones y los permisos correctos.
* El conocimiento de los niveles de riesgo en los datos de su organización requiere profundizar en el contenido, buscar palabras clave, patrones de RegEx o tipos de datos confidenciales. Los tipos de datos confidenciales pueden incluir números de tarjetas de crédito, números de la seguridad social o números de cuenta bancaria, por nombrar algunos. Debe supervisar constantemente todos los orígenes de datos, por si tienen contenido confidencial, ya que hasta una pérdida de datos mínima puede ser crítica para su organización.
* Asegurarse de que la organización sigue cumpliendo las directivas de seguridad corporativas se convierte en un reto cuando el contenido crece y cambia, y se actualizan los requisitos y las directivas para cambiar las realidades digitales. A menudo, la tarea de los administradores de seguridad es garantizar la seguridad de los datos en el menor tiempo posible.

## <a name="azure-purview-advantages"></a>Ventajas de Azure Purview

Azure Purview está diseñado para abordar los problemas mencionados en las secciones anteriores y ayudar a las empresas a sacar el máximo partido de los recursos de información existentes. El catálogo facilita que los usuarios que administran los datos puedan detectar y conocer los orígenes de datos.

Azure Purview proporciona un servicio basado en la nube en el que se pueden registrar los orígenes de datos. Durante el registro, los datos permanecen en su ubicación existente, pero una copia de sus metadatos se agrega a Azure Purview, junto con una referencia a la ubicación del origen de datos. Los metadatos también se indexan no solo para que todos los orígenes de datos se puedan detectar fácilmente a través de la búsqueda, sino también para que los usuarios que los detecten puedan comprenderlos.

Después de registrar un origen de datos, puede enriquecer sus metadatos. El usuario que registró el origen de datos u otro usuario de la empresa agregan los metadatos. Cualquier usuario puede anotar un origen de datos proporcionando descripciones, etiquetas u otros metadatos para solicitar acceso al origen de datos. Estos metadatos descriptivos complementan a los metadatos estructurales, como los nombres de columna y los tipos de datos, registrados desde el origen de datos.

El descubrimiento y comprensión de los orígenes de datos y su uso es el propósito principal de registrar los orígenes. Los usuarios empresariales pueden necesitar los datos para la inteligencia empresarial, el desarrollo de aplicaciones, la ciencia de datos o cualquier otra tarea en la que se requieran los datos correctos. Usan la experiencia de detección del catálogo de datos para encontrar rápidamente datos que se ajusten a sus necesidades, conocer los datos para evaluar su idoneidad para un fin concreto y consumir los datos abriendo el origen de datos en su herramienta preferida.

Al mismo tiempo, los usuarios pueden contribuir al catálogo mediante el etiquetado, la documentación y la anotación de los orígenes de datos que ya han registrado. También pueden registrar nuevos orígenes de datos que la comunidad de usuarios del catálogo, posteriormente, detectan, entienden y consumen.

## <a name="in-region-data-residency"></a>Residencia de datos en la región
Para Azure Purview, determinados nombres de tabla, rutas de acceso de archivo e información de ruta de acceso de objeto se almacenan en el Estados Unidos. Con la excepción mencionada, la capacidad de permitir el almacenamiento de todos los demás datos de los clientes en una sola región está actualmente disponible en todas las ubicaciones geográficas.

## <a name="next-steps"></a>Pasos siguientes

Para empezar a trabajar con Azure Purview, consulte [Creación de una cuenta de Azure Purview](create-catalog-portal.md).
