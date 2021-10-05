---
title: Procedimientos recomendados de implementación
description: En este artículo se proporcionan los procedimientos recomendados para implementar Azure Purview. Azure Purview permite a cualquier usuario registrar, detectar, conocer y consumir orígenes de datos.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: d357a85449a6ae734b3311f2649180b36646d4f4
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214339"
---
# <a name="azure-purview-deployment-best-practices"></a>Procedimientos recomendados de implementación de Azure Purview

En este artículo se identifican tareas comunes que pueden ayudarle a implementar Purview en la producción. Estas tareas se pueden completar en fases, en el transcurso de un mes o más. Incluso las organizaciones que ya han implementado Purview pueden usar esta guía para asegurarse de que sacan el máximo partido a su inversión.

Una implementación bien planeada de una plataforma de gobierno de datos (por ejemplo, Azure Purview), puede proporcionarle las siguientes ventajas:

- Una mejor detección de datos.
- Colaboración de análisis mejorada.
- Rentabilidad de la inversión maximizada.

## <a name="prerequisites"></a>Requisitos previos

* Acceso a Microsoft Azure con una suscripción de desarrollo o de producción.
* Capacidad para crear recursos de Azure, incluida la instancia de Purview.
* Acceso a orígenes de datos como Azure Data Lake Storage o Azure SQL en entornos de prueba, desarrollo o producción.
  * En el caso de Data Lake Storage, el rol necesario para examinar es el rol de lector.
  * En el caso de SQL, la identidad debe ser capaz de consultar las tablas para realizar el muestreo de las clasificaciones.
* Acceso a Azure Security Center o a la capacidad de colaborar con el administrador de Security Center para el etiquetado de datos.

## <a name="identify-objectives-and-goals"></a>Identificación de objetivos

Muchas organizaciones han empezado su recorrido en el gobierno de datos mediante el desarrollo de soluciones individuales que satisfacen los requisitos específicos de grupos aislados y de los dominios de datos de toda la organización. Aunque las experiencias pueden variar en función del sector, el producto y la cultura, la mayoría de las organizaciones encuentra difícil poder mantener controles y directivas coherentes en estos tipos de soluciones.

Algunos de los objetivos del gobierno de datos más comunes que podría querer identificar en las primeras fases son los siguientes:

* Maximizar el valor empresarial de los datos.
* Habilitar una referencia cultural de datos en la que los consumidores de datos puedan encontrar esos datos, interpretarlos y confiar en ellos fácilmente.
* Aumentar la colaboración entre varias unidades de negocio para proporcionar una experiencia de datos coherente.
* Fomentar la innovación al acelerar el análisis de datos para aprovechar las ventajas que ofrece la nube.
* Disminuir el tiempo para detectar datos mediante las opciones de autoservicio de varios grupos de aptitudes.
* Reducir el plazo de comercialización de la entrega de soluciones de análisis que mejoran el servicio a sus clientes.
* Reducir los riesgos operativos debido al uso de herramientas específicas del dominio y tecnología no admitida.

El enfoque general consiste en desglosar los objetivos globales en varias categorías y objetivos. Ejemplos:

|Category|Objetivo|
|---------|---------|
|Detección|Los administradores deben ser capaces de examinar los orígenes de datos, tanto si son de Azure como si no (incluidos los orígenes locales) para recopilar información sobre los recursos de datos de forma automática.|
|clasificación|La plataforma debe clasificar automáticamente los datos basándose en un muestreo de los mismos y permitir la invalidación manual mediante las clasificaciones personalizadas.|
|Consumo|Los usuarios empresariales deben poder encontrar información sobre cada recurso para los metadatos empresariales y técnicos.|
|Lineage|Cada recurso debe mostrar una vista gráfica de los conjuntos de datos subyacentes, para que los usuarios entiendan de dónde provienen los orígenes y qué cambios que se han realizado.|
|Colaboración|La plataforma debe permitir que los usuarios colaboren proporcionando información adicional sobre cada recurso de datos.|
|Generación de informes|Los usuarios deben ser capaces de ver los informes del patrimonio de datos, incluidos los datos confidenciales y los datos que necesiten enriquecimiento adicional.|
|Gobernanza de datos|La plataforma debe permitir que el administrador defina las directivas de control de acceso y aplique automáticamente el acceso a los datos en función de cada usuario.|
|Flujo de trabajo|La plataforma debe tener la capacidad de crear y modificar el flujo de trabajo para que sea fácil escalarlo horizontalmente y automatizar diversas tareas dentro de la misma plataforma.|
|Integración|Otras tecnologías de terceros, como los vales o la orquestación, deben ser capaces de integrarse en la plataforma a través de las API de REST o del script.|

## <a name="top-questions-to-ask"></a>Principales preguntas que se deben formular

Una vez que su organización acepte los objetivos de alto nivel, habrá muchas preguntas de varios grupos. Es fundamental recopilar estas preguntas para poder elaborar un plan que solucione todos los problemas. Por ejemplo, aquí tiene algunas preguntas que podrían surgir durante la fase inicial:

1. ¿Cuáles son los principales orígenes y sistemas de datos de la organización?
2. Si hay orígenes de datos que no son compatibles con Purview, ¿qué opciones tengo?
3. ¿Cuántas instancias de Purview necesitamos?
4. ¿Quiénes son los usuarios?
5. ¿Quién puede examinar los nuevos orígenes de datos?
6. ¿Quién puede modificar el contenido de Purview?
7. ¿Qué proceso puedo usar para mejorar la calidad de los datos en Purview?
8. ¿Cómo puedo arrancar la plataforma con recursos críticos, términos del glosario y los contactos existentes?
9. ¿Cómo la puedo integrar con los sistemas existentes?
10. ¿Cómo se recopilan comentarios y se crea un proceso sostenible?

Aunque es posible que no tenga la respuesta a la mayoría de estas preguntas de inmediato, estas pueden serle útiles para que su organización pueda comenzar a usar este proyecto y asegurarse así de que se cumplen todos los requisitos imprescindibles.

## <a name="include-the-right-stakeholders"></a>Inclusión de la parte interesada correcta

Para garantizar el éxito de la implementación de Purview en toda la empresa, es importante involucrar a las partes interesadas adecuadas. Solo algunas personas participan en la fase inicial. Sin embargo, a medida que el ámbito de trabajo se expande, necesitará roles adicionales que contribuyan al proyecto y proporcionen comentarios.

Estas son algunas de las partes interesadas clave que puede querer incluir:

|Persona|Roles|
|---------|---------|
|**Director de datos**|El Director de datos supervisa una serie de funciones que pueden incluir la administración de datos, la calidad de los mismos, la administración de datos maestros, la ciencia de datos, la inteligencia empresarial y la creación de una estrategia de datos. Asimismo, puede ser el patrocinador del proyecto de implementación de Purview.|
|**Propietario del dominio o la empresa**|Esta es el rol de la empresa que decide qué herramientas se usan y controla el presupuesto.|
|**Analista de datos**|Puede detectar un problema empresarial y analizar los datos para que los líderes de la empresa puedan tomar decisiones empresariales adecuadas.|
|**Arquitecto de datos**|Se encarga de diseñar bases de datos para aplicaciones críticas de línea de negocio, junto con el diseño y la implementación de la seguridad de los datos.|
|**Ingeniero de datos**|Se encarga del funcionamiento y el mantenimiento de la pila de datos, de la extracción de datos de diferentes orígenes, de integrar y preparar los datos y de configurar las canalizaciones de datos.|
|**Científico de datos**|Se encarga de compilar modelos analíticos y de configurar los productos de datos a los que accederán las API.|
|**Administrador de BD**|Este rol posee las bases de datos, se encarga realizar los seguimientos y de resolver incidentes y solicitudes relacionados con las bases de datos en contratos de nivel de servicio (SLA); además, puede configurar las canalizaciones de datos.|
|**DevOps**|Se encargan del desarrollo e implementación de las aplicaciones de línea de negocio; asimismo, también administran la escritura de scripts y las capacidades de orquestación.|
|**Especialista en seguridad de datos**|Se encarga de realizar la evaluación de la seguridad general de la red y de los datos, incluidos los datos que entran y salen de Purview.|

## <a name="identify-key-scenarios"></a>Identificación de los escenarios clave

Purview se puede usar para administrar de forma centralizada la gobernanza de datos en los entornos locales y en la nube de la organización. Para que una implementación sea correcta, debe identificar los escenarios clave que sean críticos para el negocio. Estos escenarios pueden cruzar los límites de las unidades de negocio o afectar a varios usuarios de nivel superior o descendente.

Asimismo, estos escenarios se pueden escribir de varias maneras, pero tenga en cuenta que debe incluir al menos estas cinco dimensiones:

1. Persona: ¿quiénes son los usuarios?
2. Sistema de origen: ¿qué son los orígenes de datos, como Azure Data Lake Storage Gen2 o Azure SQL Database?
3. Área de impacto: ¿cuál es la categoría de este escenario?
4. Escenarios de detalle: ¿cómo utilizan Purview los usuarios para solucionar problemas?
5. Resultado esperado: ¿cuáles son los criterios para el éxito?

Los escenarios deben ser específicos, accionables, ejecutables y deben tener resultados medibles. Estos son algunos escenarios de ejemplo que puede usar:

|Escenario|Detalle|Persona|
|---------|---------|---------|
|Catalogar recursos críticos para la empresa|Necesito obtener información sobre cada conjunto de datos para entenderlos correctamente. En este escenario se incluyen metadatos técnicos y empresariales sobre el conjunto de datos del catálogo. Los orígenes de datos incluyen Azure Data Lake Storage Gen2, Azure Synapse DW y Power BI. Este escenario también incluye recursos locales como SQL Server.|Analista de negocios, Científico de datos, Ingeniero de datos|
|Descubrir recursos críticos para la empresa|Necesito un motor de búsqueda que pueda buscar en todos los metadatos del catálogo. Debería poder realizar búsquedas con términos técnicos y términos empresariales mediante una búsqueda simple o compleja gracias a los caracteres comodín.|Analista de negocios, Científico de datos, Ingeniero de datos, Administrador de datos|
|Seguimiento de datos para comprender su origen y solucionar problemas|Necesito obtener un linaje de datos para realizar un seguimiento hasta el origen de los datos de los informes, las predicciones o los modelos, para así poder comprender los cambios realizados y dónde se han guardado los datos durante el ciclo de vida de los mismos. Este escenario debe admitir canalizaciones de datos con prioridad de Azure Data Factory y Databricks.|Ingeniero de datos, Científico de datos|
|Enriquecer metadatos en recursos de datos críticos|Necesito enriquecer el conjunto de datos en el catálogo con metadatos técnicos que se generen automáticamente. La clasificación y el etiquetado son algunos ejemplos.|Ingeniero de datos, Propietario del dominio o la empresa|
|Controlar los recursos de datos con una experiencia de usuario sencilla|Necesito un glosario empresarial para metadatos específicos de la empresa. Los usuarios empresariales pueden usar Purview en escenarios de autoservicio, con el fin de anotar los datos y permitir que se detecten fácilmente a través de la opción de búsqueda.|Propietario del dominio o la empresa, Analista de negocios, Científico de datos, Ingeniero de datos|

## <a name="deployment-models"></a>Modelos de implementación

Si solo tiene un grupo pequeño en Purview con casos de uso de consumo básico, el enfoque podría ser tan simple como tener una instancia de Purview para atender a todo el grupo. Sin embargo, también es posible que su organización necesite más de una instancia de Purview. Si se usan varias instancias de Purview, es necesario saber cómo los empleados pueden promover los recursos de una fase a otra.

### <a name="determine-the-number-of-purview-instances"></a>Determinación del número de instancias de Purview

En la mayoría de los casos, solo debería haber una cuenta de Purview para toda la organización. Este enfoque aprovecha al máximo los "efectos de red" en los que el valor de la plataforma aumenta exponencialmente como una función de los datos que residen en la misma plataforma.

Sin embargo, existen excepciones a este patrón:

1. **Probar nuevas configuraciones**: es posible que algunas organizaciones quieran crear varias instancias para probar las configuraciones o las clasificaciones de análisis en entornos aislados. Aunque hay una característica de "control de versiones" en algunas áreas de la plataforma (como el glosario), es más fácil tener una instancia "descartable" para realizar pruebas libremente.
2. **Separación de la prueba, la preproducción y la producción**: las organizaciones pueden querer crear distintas plataformas para diferentes tipos de datos almacenados en entornos diferentes. Esto no se recomienda porque esos tipos de datos pertenecen a tipos de contenido diferentes. Puede usar el término de glosario en el nivel de jerarquía superior o en la categoría para separar los tipos de contenido.
3. **Conglomerados y modelo federado**: los conglomerados suelen tener muchas unidades de negocio (BU) que operan de forma individual y, en algunos casos, no comparten la facturación entre sí. En esos casos, la organización acaba creando una instancia de Purview para cada BU. Este modelo no es el idóneo, pero puede ser necesario, especialmente porque las unidades de negocio no suelen estar dispuestas a compartir la facturación.
4. **Cumplimiento**: existen algunos regímenes de cumplimiento estricto, que tratan incluso los metadatos como información confidencial, y que deben estar en una geografía específica. Si una empresa tiene varias regiones geográficas, la única solución es tener varias instancias de Purview, una para cada geografía.

### <a name="create-a-process-to-move-to-production"></a>Creación de un proceso para pasar a producción

Algunas organizaciones pueden decidir simplificar las cosas trabajando con una única versión de producción de Purview. Probablemente no tengan que ir más allá de los escenarios de detección, búsqueda y exploración. Si algunos recursos tienen términos de glosario incorrectos, es bastante permisivo dejar que los usuarios corrijan los términos incorrectos. Sin embargo, la mayoría de las organizaciones que quieren implementar Purview en varias unidades de negocio querrán tener algún tipo de proceso y control.

Otro aspecto importante que se debe incluir en el proceso de producción, es la manera de migrar las clasificaciones y las etiquetas. Purview tiene más de 90 clasificadores de sistema. Puede aplicar clasificaciones personalizadas o de sistema en los recursos de archivo, tabla o columna. Las clasificaciones son similares a las etiquetas de asunto y se usan para marcar e identificar el contenido de un tipo específico que se encuentre en su patrimonio de datos durante el análisis. Las etiquetas de confidencialidad se usan para identificar categorías de tipos de clasificación en los datos de la organización, así como el grupo de las directivas que quiere aplicar a cada categoría. Esta opción usa los mismos tipos de información confidencial que Microsoft 365, lo que le permite ampliar las directivas de seguridad y la protección existentes en todo el contenido y los datos. Asimismo, puede examinar y clasificar documentos automáticamente. Por ejemplo, si tiene un archivo denominado multiple.docx y tiene un número de id. nacional en su contenido, Purview agregará una clasificación como el Número de id. nacional de la UE en la página de detalles del recurso.

En Purview hay varias áreas en las que los administradores del catálogo necesitan garantizar la coherencia y el mantenimiento de los procedimientos recomendados a lo largo de su ciclo de vida:

* **Recursos de datos**: los orígenes de datos tendrán que volver a examinarse en todos los entornos. No se recomienda realizar el examen solo en la fase de desarrollo y posteriormente volver a generar los orígenes con las API de producción. La razón principal es que los exámenes de Purview gestionan mucho más "cableado" de los recursos de datos en segundo plano, lo que podría ser complejo para moverlos a otra instancia de Purview. Por lo tanto, es mucho más fácil agregar simplemente el mismo origen de datos en la producción y volver a examinar los orígenes. El procedimiento recomendado general es disponer de documentación de todos los exámenes, conexiones y mecanismos de autenticación que se usen.
* **Examinar los conjuntos de reglas**: esta es la colección de reglas asignada a un examen específico, como el tipo de archivo y las clasificaciones que se deben detectar. Si no tiene tantos conjuntos de reglas de examen, es posible volver a crearlos manualmente a través de la producción. Tenga en cuenta que esto requerirá realizar un proceso interno y tener una documentación adecuada. Sin embargo, si el conjunto de reglas cambia de forma diaria o semanal, esto podría abordarse mediante la exploración de la ruta de la API de REST.
* **Clasificaciones personalizadas**: las clasificaciones no pueden cambiar de forma periódica. Durante la fase inicial de la implementación, puede llevarle algún tiempo comprender los diversos requisitos para elaborar clasificaciones personalizadas. Sin embargo, una vez que se haya familiarizado con esto, deberá realizar un pequeño cambio. Por lo tanto, es recomendable migrar manualmente cualquier clasificación personalizada mediante la API de REST.
* **Glosario**: es posible exportar e importar los términos del glosario a través de la experiencia de usuario. En el caso de los escenarios de automatización, también puede usar la API de REST.
* **Directivas del patrón del conjunto de recursos**: esta funcionalidad es demasiado avanzada para que las organizaciones típicas la apliquen. En algunos casos, Azure Data Lake Storage tiene convenciones de nomenclatura de carpetas y una estructura específica que pueden causar problemas cuando Purview genera el conjunto de recursos. Es posible que la unidad de negocio también tenga que cambiar la construcción del conjunto de recursos agregando personalizaciones adicionales, para poder ajustarse a las necesidades de la empresa. En este escenario, es mejor realizar un seguimiento de todos los cambios a través de la API de REST y documentar esos cambios a través de la plataforma de control de versiones externa.
* **Asignación de roles**: aquí es donde se supervisa quién tiene acceso a Purview y qué permisos tiene. Purview también tiene una API de REST para admitir la exportación e importación de usuarios y roles, pero no es compatible con la API de Atlas. En su lugar, se recomienda asignar un grupo de seguridad de Azure y administrar la pertenencia al grupo.

### <a name="plan-and-implement-different-integration-points-with-purview"></a>Planeación e implementación de distintos puntos de integración con Purview

Es probable que una organización madura ya tenga un catálogo de datos existente. La pregunta fundamental es si desea seguir usando la tecnología existente y sincronizar con Purview o no. Para controlar la sincronización con productos existentes en una organización, Purview proporciona las API REST de Atlas. Las API de Atlas incorporan un mecanismo eficaz y flexible para controlar escenarios de inserción y extracción. La información se puede publicar en Purview mediante las API de Atlas con fines de arranque o para insertar las actualizaciones más recientes desde otro sistema en Purview. La información disponible en Purview también se puede leer mediante las API de Atlas y después volver a sincronizar con los productos existentes. 

En otros escenarios de integración, como los vales, la interfaz de usuario personalizada y la orquestación, puede usar las API de Atlas y los puntos de conexión de Kafka. En general, Purview cuenta con cuatro puntos de integración:

* **Recurso de datos**: permite a Purview examinar los recursos de un almacén para enumerar cuáles son los recursos y recopilar los metadatos de los mismos disponibles. Así pues, podríamos estar hablando de una lista de bases de datos, tablas, procedimientos almacenados, vistas y datos de configuración de SQL que se guardan en lugares como `sys.tables`. En cuanto a Azure Data Factory (ADF), esta opción puede enumerar todas las canalizaciones y obtener los datos en el momento en que se crearon, la última ejecución y el estado actual.
* **Linaje**: permite a Purview recopilar información de un sistema de mutación de datos y análisis sobre cómo se transfieren los datos. En lo que se refiere a Spark, esto podría referirse a recopilar información de la ejecución de un cuaderno para ver qué datos ingirió el cuaderno, cómo lo transformaron esos datos y dónde los emitió. En lo que se refiere a SQL, esto podría ser el análisis de los registros de consultas para aplicar ingeniería inversa a las operaciones de mutación que se ejecutaron y a lo que hacían. En función de las necesidades, se admite el linaje de inserción y de extracción.
* **Clasificación**: esta opción permite a Purview tomar muestras físicas de los orígenes de datos y ejecutarlas a través de nuestro sistema de clasificación. El sistema de clasificación averigua la semántica de un fragmento de datos. Por ejemplo, es posible que sepa que un archivo pertenece a Parquet y que tiene tres columnas, siendo la tercera una cadena. Aún así, los clasificadores que se ejecutan en los ejemplos nos indicarán que la cadena es un nombre, una dirección o un número de teléfono. Si se resalta este punto de integración, significa que hemos definido la forma en que Purview abre objetos como cuadernos, canalizaciones, archivos de Parquet, tablas y contenedores.
* **Experiencia integrada**: los productos que tienen una experiencia similar a "Studio" (como ADF, Synapse, SQL Studio, PBI y Dynamics) suelen permitir que los usuarios encuentren los datos con los que quieren interactuar, además de buscar lugares para generar esos datos. El catálogo de Purview puede ayudarle a acelerar estas experiencias proporcionando a los usuarios una experiencia de inserción. Esta experiencia puede producirse en la API o en el nivel de experiencia de usuario en la opción del partner. Al insertar una llamada en Purview, la organización puede aprovechar el mapa de Purview que cuenta con la información de los datos, para buscar los recursos de datos, ver el linaje, comprobar los esquemas, mirar las clasificaciones, los contactos, etc.

## <a name="phase-1-pilot"></a>Fase 1: piloto

En esta fase, Purview debe crearse y configurarse para un conjunto muy pequeño de usuarios. Normalmente, es simplemente un grupo de 2 o 3 personas que trabajan conjuntamente en escenarios de un extremo a otro. Estos son los asesores de Purview en la organización. El objetivo principal de esta fase es garantizar que se puedan cumplir las funcionalidades clave y que las partes interesadas adecuadas sean conscientes del proyecto.

### <a name="tasks-to-complete"></a>Tareas a completar

|Tarea|Detail|Duration|
|---------|---------|---------|
|Recopilación y acuerdo de los requisitos|Debate con todas las partes interesadas para recopilar un conjunto completo de requisitos. Los distintos roles deben participar para aceptar un subconjunto de requisitos para cada fase del proyecto.|1 semana|
|Navegar por Purview|Aprenda a usar Purview desde la página principal.|1 día|
|Configuración de ADF para el linaje|Identifique las canalizaciones clave y los recursos de datos. Recopile toda la información necesaria para conectarse a una cuenta de ADF interna.|1 día|
|Examen de un origen de datos, como Azure Data Lake Storage|Agregue el origen de datos y configure un examen. Asegúrese de que el examen detecta todos los recursos correctamente.|2 días|
|Buscar y examinar|Permita que los usuarios finales accedan a Purview y realicen tareas de búsqueda y exploración de un extremo a otro.|1 día|

### <a name="acceptance-criteria"></a>Criterios de aceptación

* La cuenta de Purview se creó correctamente en la suscripción de la organización que se encuentra en el inquilino de la misma.
* Un pequeño grupo de usuarios con varios roles puede acceder a Purview.
* Purview está configurado para examinar al menos un origen de datos.
* Los usuarios deben poder extraer valores clave de Purview como:
  * Buscar y examinar
  * Lineage
* Los usuarios deben poder asignar la propiedad de los recursos en la página de recursos.
* Presentación y demostración para aumentar el conocimiento de las partes interesadas clave.
* Participación de la administración para aprobar recursos adicionales para la fase MVP.

## <a name="phase-2-minimum-viable-product"></a>Fase 2: producto mínimo viable (MVP)

Una vez que tenga los requisitos acordados y las unidades de negocio que participan en la incorporación de Purview, el siguiente paso es trabajar en una versión de producto mínimo viable (MVP). En esta fase, ampliará el uso de Purview a más usuarios que tengan necesidades adicionales, tanto horizontales como verticales. Habrá escenarios clave que se deben cumplir de forma horizontal para todos los usuarios, como los términos del glosario, la búsqueda y la exploración. También habrá requisitos verticales más profundos en cada unidad de negocio o grupo, para poder cubrir escenarios de un extremo a otro, como el linaje desde Azure Data Lake Storage a Azure Synapse DW y a Power BI.

### <a name="tasks-to-complete"></a>Tareas a completar

|Tarea|Detail|Duration|
|---------|---------|---------|
|[Examen de Azure Synapse Analytics](register-scan-azure-synapse-analytics.md)|Empiece a incorporar los orígenes de base de datos y examínelos para rellenar recursos clave.|2 días|
|[Creación de clasificaciones y reglas personalizadas](create-a-custom-classification-and-classification-rule.md)|Una vez que se han examinado los recursos, es posible que los usuarios observen que hay casos de uso adicionales en más clasificaciones, junto a las clasificaciones predeterminadas de Purview.|2 a 4 semanas|
|[Examen de Power BI](register-scan-power-bi-tenant.md)|Si su organización usa Power BI, puede examinar su instancia de Power BI para recopilar todos los recursos de datos que usen los Científicos de datos o los Analistas de datos, ya que tienen requisitos para incluir el linaje de la capa de almacenamiento.|1 a 2 semanas|
|[Importación de los términos del glosario](how-to-create-import-export-glossary.md)|En la mayoría de los casos, es posible que su organización ya haya desarrollado una colección de términos de glosario y un modo de asignar esos términos a los recursos. Esto requerirá un proceso de importación en Purview a través del archivo .csv.|1 semana|
|Agregación de contactos a los recursos|En el caso de los recursos principales, es posible que quiera establecer un proceso que permita a otros roles asignar contactos o importarlos a través de las API de REST.|1 semana|
|Agregación de etiquetas y exámenes|Esto puede ser opcional en algunas organizaciones, en función del uso de etiquetado de Microsoft 365.|1 a 2 semanas|
|Obtención de clasificaciones y de información confidencial|Para la elaboración de informes y detalles en Purview, puede acceder a esta funcionalidad para obtener varios informes y proporcionar una presentación a la administración.|1 día|
|Incorporación de usuarios adicionales con usuarios administrados mediante Purview|Este paso requerirá que el administrador de Purview trabaje con el administrador de Azure Active Directory para establecer nuevos grupos de seguridad que concedan acceso a Purview.|1 semana|

### <a name="acceptance-criteria"></a>Criterios de aceptación

* Incorporación correcta de un grupo de usuarios mayor a Purview (más de 50).
* Examen de los orígenes de datos críticos para la empresa.
* Importación y asignación de todos los términos críticos del glosario.
* Prueba correcta del etiquetado importante en recursos clave.
* Cumplimiento correcto de los escenarios mínimos para los usuarios de las unidades de negocio que participaron.

## <a name="phase-3-pre-production"></a>Fase 3: Preproducción

Una vez que haya pasado la fase MVP, es el momento de planificar el hito de preproducción. La organización puede decidir tener una instancia independiente de Purview para la preproducción y la producción, o bien mantener la misma instancia pero restringir el acceso. Igualmente, en esta fase puede incluir el examen de los orígenes de datos locales, como SQL Server. Si hay algún intervalo en los orígenes de datos que Purview no admita, este es el momento de explorar la API de Atlas para comprender las opciones adicionales.

### <a name="tasks-to-complete"></a>Tareas a completar

|Tarea|Detail|Duration|
|---------|---------|---------|
|Refinar el examen con el conjunto de reglas de examen|Su organización tendrá muchos orígenes de datos para la preproducción. Es importante definir previamente los criterios clave para el examen, y que así las clasificaciones y la extensión de archivo se puedan aplicar de forma coherente en todo el panel.|1 a 2 días|
|Evaluar la disponibilidad de la región para el examen|En función de la región de los orígenes de datos y de los requisitos de la organización según el cumplimiento y la seguridad, es posible que quiera considerar qué regiones deben estar disponibles en el examen.|1 día|
|Comprender el concepto de firewall al realizar el examen|Para realizar este paso, debe saber cómo se configura el firewall de su organización y cómo puede autenticarse Purview para obtener acceso a los orígenes de datos que se deben examinar.|1 día|
|Comprender el concepto de Private Link al realizar el examen|Si su organización usa un Private Link, debe diseñar la base de la seguridad de red para incluir Private Link como parte de los requisitos.|1 día|
|[Examen de SQL Server local](register-scan-on-premises-sql-server.md)|Este paso es opcional si tiene una instancia local de SQL Server. El examen requerirá que configure el [entorno de ejecución de integración autohospedado](manage-integration-runtimes.md) y agregue SQL Server como origen de datos.|1 a 2 semanas|
|Usar la API de REST de Purview en escenarios de integración|Si tiene requisitos para integrar Purview con otras tecnologías de terceros, como la orquestación o el sistema de vales, puede que quiera explorar el área de la API de REST.|1 a 4 semanas|
|Comprender los precios de Purview|En este paso se proporcionará a la organización información financiera importante para tomar decisiones.|1 a 5 días|

### <a name="acceptance-criteria"></a>Criterios de aceptación

* Incorporar correctamente al menos una unidad de negocio con todos los usuarios.
* Examinar un origen de datos local, como SQL Server.
* Realizar una prueba de concepto en al menos un escenario de integración con la API de REST.
* Completar un plan para ir a producción, que debe incluir áreas clave de infraestructura y seguridad.

## <a name="phase-4-production"></a>Fase 4: Producción

Se deben seguir las fases anteriores para crear una gobernanza eficaz de la información, ya que esto es la base para mejorar los programas de gobernanza. La gobernanza de datos ayudará a su organización a prepararse para tendencias crecientes como la inteligencia artificial, Hadoop, IoT y la cadena de bloques. Esto es solo el principio de la gran cantidad de datos y análisis que tendrá; además, aún se pueden analizar muchas cosas más. Este resultado le ofrece una solución:

* **Centrada en la empresa**: una solución que está en concordancia con los requisitos y los escenarios empresariales con respecto a los requisitos técnicos.
* **Preparada para el futuro**: una solución que maximizará las características predeterminadas de la plataforma y usará prácticas estandarizadas del sector, para realizar actividades de configuración o de scripting y así poder respaldar el avance o la evolución de la plataforma.

### <a name="tasks-to-complete"></a>Tareas a completar

|Tarea|Detail|Duration|
|---------|---------|---------|
|Examinar orígenes de datos de producción con el firewall habilitado|Esto es opcional cuando el firewall está implementado, pero es importante explorar las opciones que le ayuden a proteger la infraestructura.|1 a 5 días|
|Habilitación de Private Link|Esto es opcional cuando se usa Private Link. De lo contrario, puede omitir este paso, ya que es un criterio de debe tener en cuenta cuando está habilitada la opción privada.|1 a 5 días|
|Crear un flujo de trabajo automatizado|El flujo de trabajo es importante para automatizar procesos como la aprobación, la escalación, la revisión y la administración de problemas.|2 a 3 semanas|
|Documentación de la operación|La gobernanza de datos no es un proyecto único. Se trata de un programa continuo para mejorar la toma de decisiones que suelen estar basadas en los datos, y así crear oportunidades para la empresa. Es fundamental documentar el procedimiento clave y los estándares empresariales.|1 semana|

### <a name="acceptance-criteria"></a>Criterios de aceptación

* Incorporar correctamente todas las unidades de negocio y sus usuarios.
* Cumplir correctamente los requisitos de infraestructura y de seguridad en la producción.
* Cumplir correctamente todos los casos de uso que requieran los usuarios.

## <a name="platform-hardening"></a>Protección de la plataforma

Se pueden realizar pasos adicionales de protección:

* Aumentar la postura de seguridad habilitando la opción de examen en los recursos del firewall o usar Private Link.
* Ajustar el examen del ámbito para mejorar el rendimiento del examen.
* Usar las API de REST para exportar las propiedades y los metadatos críticos de las copias de seguridad y de recuperación.
* Usar el flujo de trabajo para automatizar los vales y los eventos para evitar errores humanos.

## <a name="next-steps"></a>Pasos siguientes

- [Procedimientos recomendados para recopilaciones en Configuration Manager](concept-best-practices-collections.md)
- [Desplazamiento por la página principal y búsqueda de un recurso](tutorial-asset-search.md)
