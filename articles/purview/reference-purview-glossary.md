---
title: Glosario de productos de Purview
description: Glosario que define la terminología usada en Azure Purview
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: ec011e4c9b0cab17bd9427020ba8842734e1f590
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111811919"
---
# <a name="azure-purview-product-glossary"></a>Glosario de productos de Azure Purview

Este es un glosario de terminología que se usa en Azure Purview.

## <a name="annotation"></a>Anotación
Información asociada a los recursos de datos de Azure Purview, por ejemplo, términos del glosario y clasificaciones. Una vez aplicadas, las anotaciones se pueden usar en la búsqueda para ayudar a la detección de los recursos de datos. 
## <a name="approved"></a>Aprobado
Estado dado a cualquier solicitud aceptada como satisfactoria por el individuo o grupo designado que tiene autoridad para cambiar el estado de la solicitud. 
## <a name="asset"></a>Recurso
Cualquier objeto único almacenado en un catálogo de datos de Azure Purview.
> [!NOTE]
> Un único objeto del catálogo podría representar muchos objetos en el almacenamiento; por ejemplo, un conjunto de recursos es un recurso, pero se compondrá de muchos archivos de partición en el almacenamiento.
## <a name="azure-information-protection"></a>Azure Information Protection
Una solución en la nube que admite el etiquetado de documentos y correos electrónicos para clasificar y proteger la información. Los elementos etiquetados se pueden proteger mediante cifrado, marcarse con una marca de agua o restringirse a acciones o usuarios específicos, y están vinculados al elemento. Esta solución basada en la nube depende de Azure Rights Management Service (RMS) para aplicar restricciones. 
## <a name="business-glossary"></a>Glosario empresarial
Lista que permite búsquedas de términos especializados que una organización usa para describir palabras empresariales clave y sus definiciones. La utilización de un glosario empresarial puede proporcionar un uso de datos coherente en toda la organización. 
## <a name="classification-report"></a>Informe de clasificación
Informe que muestra detalles de clasificación clave sobre los datos analizados.  
## <a name="classification"></a>clasificación
Tipo de anotación que se usa para identificar un atributo de un recurso o una columna como "Edad", "Dirección de correo electrónico" y "Dirección postal". Estos atributos se pueden asignar durante los exámenes o agregarse manualmente. 
## <a name="classification-rule"></a>Regla de clasificación
Una regla de clasificación es un conjunto de condiciones que determinan cómo se deben clasificar los datos examinados cuando el contenido coincide con el patrón especificado.  
## <a name="contact"></a>Contacto
Una persona que está asociada a una entidad en el catálogo de datos. 
## <a name="control-plane"></a>Plano de control
Operaciones que administran los recursos de la suscripción, como el control de acceso basado en rol y la directiva de Azure, que se envían al punto de conexión de Azure Resource Manager. 
## <a name="credential"></a>Credential:
Comprobación de identidad o herramienta que se usa en un sistema de control de acceso. Se pueden usar credenciales para autenticar a un individuo o grupo con el fin de concederle acceso a un recurso de datos. 
## <a name="data-catalog"></a>Catálogo de datos
Características de Azure Purview que permiten a los clientes ver y administrar los metadatos de los recursos de su patrimonio de datos.
## <a name="data-map"></a>Mapa de datos
Características de Azure Purview que permiten a los clientes administrar su patrimonio de datos, como examen, linaje y movimiento.
## <a name="expert"></a>Experto
Individuo dentro de una organización que comprende el contexto completo de un recurso de datos o un término del glosario. 
## <a name="fully-qualified-name-fqn"></a>Nombre completo (FQN)
Ruta de acceso que define la ubicación de un recurso dentro de su origen de datos.  
## <a name="glossary-term"></a>Términos del glosario
Entrada del glosario empresarial que define un concepto específico de una organización. Los términos del glosario pueden contener información sobre sinónimos, acrónimos y términos relacionados. 
## <a name="insights"></a>Información detallada
Un área dentro de Azure Purview donde puede ver informes que resumen información sobre los datos.
## <a name="lineage"></a>Linaje
El modo en que los datos se transforman y fluyen a medida que se mueven de su origen a su destino. Comprender este flujo en el patrimonio de datos ayuda a las organizaciones a ver el historial de sus datos y a solucionar problemas o analizar el impacto. 
## <a name="management-center"></a>Centro de administración
Un área dentro de Azure Purview donde se pueden administrar conexiones, usuarios, roles y credenciales.
## <a name="on-premises-data"></a>Datos locales
Datos que se encuentran, por ejemplo, en un centro de datos controlado por un cliente, no en la nube ni como software como servicio (SaaS). 
## <a name="owner"></a>Propietario
Un individuo o grupo a cargo de administrar un recurso de datos.  
## <a name="purview-instance"></a>Instancia de Purview
Un único recurso de Azure Purview. 
## <a name="registered-source"></a>Origen registrado
Origen que se ha agregado a una instancia de Azure Purview y que ahora se administra como parte del catálogo de datos. 
## <a name="related-terms"></a>Términos relacionados
Términos del glosario que están vinculados a otros términos dentro de la organización.  
## <a name="resource-set"></a>Conjunto de recursos
Un único recurso que representa muchos archivos u objetos con particiones en el almacenamiento. Por ejemplo, Azure Purview almacena la salida de Apache Spark con particiones como un único conjunto de recursos en lugar de como recursos únicos para cada archivo individual. 
## <a name="role"></a>Rol
Permisos asignados a un usuario dentro de una instancia de Azure Purview. Los roles, como conservador de datos o lector de datos de Purview, determinan lo que se puede hacer dentro del producto. 
## <a name="scan"></a>Examinar
Un proceso de Azure Purview que explora un origen o conjunto de orígenes e ingiere sus metadatos en el catálogo de datos. Los exámenes se pueden ejecutar manualmente o según una programación mediante un desencadenador de examen. 
## <a name="scan-ruleset"></a>Conjunto de reglas de examen
Conjunto de reglas que definen qué tipos de datos y clasificaciones ingiere un examen en un catálogo. 
## <a name="scan-trigger"></a>Desencadenador de examen
Programación que determina la periodicidad de ejecución de un examen. 
## <a name="sensitivity-label"></a>Etiqueta de confidencialidad
Anotaciones que clasifican y protegen los datos de una organización. Azure Purview se integra con Microsoft Information Protection para la creación de etiquetas de confidencialidad. 
## <a name="sensitivity-label-report"></a>Informe de etiquetas de confidencialidad
Resumen de las etiquetas de confidencialidad que se aplican en el patrimonio de datos. 
## <a name="service"></a>Servicio
Un producto que proporciona funcionalidad independiente y está disponible para los clientes por suscripción o licencia. 
## <a name="source"></a>Source
Un sistema donde se almacenan los datos. Los orígenes se pueden hospedar en varios lugares, como una nube o el entorno local. Los orígenes se registran y examinan para poder administrarlos en Azure Purview. 
## <a name="source-type"></a>Tipo de origen
Clasificación de los orígenes registrados que se usan en una instancia de Azure Purview, por ejemplo, Azure SQL Database, Azure Blob Storage, Amazon S3 o SAP ECC. 
## <a name="steward"></a>Encargado
Los individuos que definen los estándares de un término del glosario. Son responsables de mantener los estándares de calidad, la nomenclatura y las reglas de la entidad asignada. 

## <a name="next-steps"></a>Pasos siguientes

Para empezar a trabajar con Azure Purview, consulte [Inicio rápido: Creación de una cuenta de Azure Purview](create-catalog-portal.md).