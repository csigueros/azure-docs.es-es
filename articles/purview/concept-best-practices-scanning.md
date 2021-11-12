---
title: Procedimientos recomendados para el examen de los orígenes de datos en Purview
description: En este artículo se proporcionan procedimientos recomendados para registrar y examinar varios orígenes de datos en Azure Purview.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 10/08/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 85dc954dcb352f9bc2447e72322d35e8d5e73cb3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552665"
---
# <a name="azure-purview-scanning-best-practices"></a>Procedimientos recomendados de análisis de Azure Purview

Azure Purview admite el análisis automatizado de orígenes de datos locales, en varias nubes y SaaS. Al ejecutar un "examen", se invoca el proceso para ingerir metadatos de los orígenes de datos registrados. Los metadatos seleccionados al final del proceso de examen y conservación incluyen metadatos técnicos como nombres de recursos de datos (nombres de tabla o nombres de archivo), el tamaño del archivo, las columnas y el linaje de datos, entre otros. En cuanto a los orígenes de datos estructurados (por ejemplo, el Sistema de administración de bases de datos relacionales), también se capturan los detalles del esquema. El proceso de protección aplica etiquetas de clasificación automatizadas en los atributos del esquema en función del conjunto de reglas de examen configurado y de las etiquetas de confidencialidad, si la cuenta de Purview está conectada a un centro de seguridad y cumplimiento (SCC) de Microsoft 365. 

## <a name="intended-audience"></a>Destinatarios

- Equipo de arquitectura de datos
- Conservador de datos
- Administrador de orígenes de datos
- Ingeniero de datos

## <a name="why-do-you-need-best-practices-to-manage-data-sources"></a>¿Por qué necesita los procedimientos recomendados para administrar orígenes de datos?

Los procedimientos recomendados permiten optimizar el costo, crear excelencia operativa, mejorar el cumplimiento de seguridad y la eficacia del rendimiento.
Las consideraciones y recomendaciones de diseño se han organizado en función de los pasos clave implicados en el proceso de examen.

## <a name="register-source-and-establish-connection"></a>Registro del origen y establecimiento de la conexión

### <a name="design-considerations"></a>Consideraciones de diseño

- La jerarquía que se alinea con la estrategia de la organización (geográfica, función empresarial, origen de datos, etc.) que define los orígenes de datos que se registrarán y examinarán debe crearse mediante colecciones.

- De forma predeterminada, no se pueden registrar orígenes de datos varias veces en una sola cuenta de Purview. Esta arquitectura le permite evitar el riesgo de asignar diferentes niveles de control de acceso a un único origen de datos.

### <a name="design-recommendations"></a>Recomendaciones de diseño

- Si varios equipos consumen los metadatos de un mismo origen de datos, puede registrar y administrar ese origen de datos en una colección primaria y crear los exámenes correspondientes en cada subcolección, de modo que los recursos pertinentes aparezcan en cada colección secundaria. Los orígenes no primarios se agrupan en un cuadro punteado en la vista de mapa sin ninguna flecha que los vincule a elementos primarios.

  :::image type="content" source="media/concept-best-practices/scanning-parent-child.png" alt-text="Captura de pantalla que muestra Azure Purview con el origen de datos registrado en la colección primaria.":::

- Use la opción **Azure Multiple** si necesita registrar varios orígenes (suscripciones o grupos de recursos de Azure) en la nube de Azure. Consulte la documentación que tiene a continuación para obtener más detalles:
    * [Examen de varios orígenes en Azure Purview](./register-scan-azure-multiple-sources.md)
    * [Comprobación de la preparación del origen de datos a escala](./tutorial-data-sources-readiness.md) 
    * [Configuración del acceso a orígenes de datos para MSI de Azure Purview a escala](./tutorial-msi-configuration.md)
     
- Una vez registrado un origen de datos, puede examinar el mismo origen varias veces, en caso de que distintos equipos o unidades de negocio usen el mismo origen de forma diferente.

Para obtener más información sobre cómo definir una jerarquía para registrar orígenes de datos, consulte los [procedimientos recomendados sobre la arquitectura de colecciones](./concept-best-practices-collections.md).

## <a name="scanning"></a>Exploración

### <a name="design-considerations"></a>Consideraciones de diseño

- Una vez registrado el origen de datos, debe configurar un examen para administrar el examen y la protección de metadatos automatizados y seguros.
- La configuración del examen incluye la configuración del nombre del examen, el ámbito de este, el entorno de ejecución de integración, la frecuencia del desencadenador del examen, el conjunto de reglas de examen y el conjunto de recursos de forma única para cada origen de datos por frecuencia de examen.
- Antes de crear credenciales, tenga en cuenta los tipos de origen de datos y los requisitos de red para decidir qué método de autenticación y entorno de ejecución de integración son necesarios para su escenario.

### <a name="design-recommendations"></a>Recomendaciones de diseño

Para evitar costos inesperados y repetir el trabajo, se recomienda planear y seguir el orden siguiente al configurar el examen, después de registrar el origen en la [colección](./how-to-create-and-manage-collections.md) correspondiente:

1. Identifique los requisitos de clasificación de las reglas de clasificación integradas del sistema o cree reglas de clasificación personalizadas específicas según sea necesario, en función de los requisitos específicos del sector, la empresa o región (que no están disponibles de forma integrada).
    - Use un método de expresión regular si el elemento de datos que se clasifica se puede expresar de forma coherente mediante el patrón de expresión regular o si el patrón se puede generar mediante el archivo de datos. Asegúrese de que los datos de ejemplo reflejan la población.
    - Use el método de diccionario si se espera que la lista de valores del elemento de datos se ajuste a un conjunto de datos determinado.
    - Para obtener más información, consulte el tutorial sobre [cómo crear una regla de clasificación personalizada](./create-a-custom-classification-and-classification-rule.md).

2. El proceso de clasificación puede dar lugar a una mayor duración del examen, lo que provocaría un costo adicional. Por lo tanto, se recomienda seleccionar solo las reglas de clasificación pertinentes para el origen de datos que está analizando.
    - Cree conjuntos de reglas de examen por origen y seleccione las reglas de clasificación pertinentes, incluidas las de clasificación del sistema y las personalizadas que haya configurado.
    - Al configurar un conjunto de reglas de examen, asegúrese de lo siguiente:
        - Compruebe si el conjunto de reglas de examen predeterminado del sistema es suficiente para el origen de datos que se está analizando; de lo contrario, defina el conjunto de reglas de examen personalizado.
        - El conjunto de reglas de examen personalizado puede incluir tanto el valor predeterminado del sistema como el personalizado, por lo que se desactivan los valores que no son pertinentes para los recursos de datos que se están analizando.
        - Compruebe siempre las reglas predeterminadas del sistema para la clasificación en función de los requisitos específicos del sector, la empresa o la región.
        - Cuando sea necesario, cree un conjunto de reglas personalizado para excluir las etiquetas de clasificación no deseadas. Por ejemplo, el conjunto de reglas del sistema contiene patrones de código gubernamental genéricos para el mundo, no solo para Estados Unidos; por lo tanto, los datos pueden coincidir con el patrón de algún otro tipo, como el "Número de carnet de conducir de Bélgica".
        - Limite las reglas de clasificación personalizadas a las etiquetas más importantes y pertinentes para evitar el desorden (esto es, que haya demasiadas etiquetas etiquetadas en el recurso).
        - Escenario de actualización: el examen completo se desencadenará si modifica la clasificación personalizada o el conjunto de reglas de examen. Por lo tanto, se recomienda configurar la clasificación y el conjunto de reglas de examen adecuadamente para evitar volver a realizar el trabajo y costosos exámenes completos.
        - Tenga en cuenta que todavía no se admite la clasificación de datos multilingües.
        - Al crear un examen de clasificación personalizado, asegúrese de que la regla de patrón y el umbral se hayan comprobado y configurado correctamente para evitar obtener una clasificación errónea.
        - Al analizar una cuenta de almacenamiento, Azure Purview usa un conjunto de patrones definidos para determinar si un grupo de recursos es un conjunto de recursos. Las [reglas de patrón del conjunto de recursos](./how-to-resource-set-pattern-rules.md) le permiten personalizar o invalidar la forma en que Azure Purview detecta qué recursos se agrupan como conjuntos de recursos, además de cómo se muestran dentro del catálogo. 
        > [!NOTE]
        > Esta característica tiene en cuenta los costos; consulte la [página de precios](https://azure.microsoft.com/pricing/details/azure-purview/) para obtener más información.

3. Configuración de un examen de los orígenes de datos registrados
    - **Nombre del examen**: de forma predeterminada, Purview usa una convención de nomenclatura **SCAN-[A-Z][a-z][a-z]** que no resulta útil al intentar identificar un examen que ya se ha ejecutado. Como procedimiento recomendado, use una convención de nomenclatura significativa.  Una instancia podría denominar el examen como _environment-source-frequency-time_; por ejemplo, DEVODS-Daily-0200, que representa un examen diario a las 0200 h.
    
    - **Autenticación**
        - Azure Purview ofrece varios métodos de autenticación para examinar los orígenes de datos, en función del tipo de orígenes (nube de Azure, locales o de terceros). Se recomienda seguir el principio de privilegios mínimos para el método de autenticación, siguiendo el orden de preferencia siguiente:
            - MSI de Purview: identidad administrada (por ejemplo, para orígenes de Azure Data Lake Gen2)
            - Identidad administrada asignada por el usuario
            - Entidad de servicio
            - Autenticación de SQL (por ejemplo, para orígenes locales o de Azure SQL)
            - Clave de cuenta o autenticación básica (por ejemplo, para orígenes de SAP S/4HANA)
            > [!Note]
            > Si tiene el firewall habilitado para la cuenta de almacenamiento, debe usar el método de autenticación de Identidad administrada al configurar un examen.
            > Al configurar una nueva credencial, el nombre de la credencial solo puede contener _letras, números, caracteres de subrayado y guiones_. 

    - **Integration runtime** (Tiempo de ejecución de integración)
        - Use el entorno de ejecución de integración de resolución automática de Azure, siempre que sea factible.
        - Si el punto de conexión público está restringido para los orígenes de datos que se están analizando, debe configurar un entorno de ejecución de integración autohospedado (SHIR) y crear una credencial. SHIR es una herramienta que se usa para permitir que el cliente ejecute el examen en su equipo. Reemplaza el proceso de Azure donde la máquina del cliente ejecuta normalmente el examen.
        - Cuando se usa un punto de conexión privado para la ingesta, debe usar un entorno de ejecución de integración autohospedado para examinar los orígenes de datos.
        - Asimismo, asegúrese de que esté instalada la [versión más reciente](https://www.microsoft.com/download/details.aspx?id=39717) de SHIR.
        - Una vez eliminado SHIR, se producirá un error en cualquier examen en curso que dependa de él.
      <!--
        - In Azure Purview there is no concept of SHIR HA or Shared SHIR.
        -->
        - Al usar SHIR, asegúrese de que la memoria sea suficiente para el origen de datos que se está analizando. Por ejemplo, al usar SHIR para examinar el origen de SAP, si observa el mensaje "error fuera de memoria":
            - Asegúrese de que la máquina de SHIR tenga suficiente memoria (se recomienda tener 128 GB).
            - En la configuración de examen, establezca la memoria máxima disponible como un valor adecuado (por ejemplo, 100).
            - Para obtener más información, consulte la sección 5.f de [este](./register-scan-sapecc-source.md#create-and-run-scan) documento.

    - **Examen de ámbito**
        - Al configurar el ámbito para el examen, seleccione solo los recursos, que son pertinentes en el nivel granular o en el nivel primario. Esto garantizará que el costo del examen sea óptimo y que el rendimiento sea eficaz. Todos los recursos futuros de un determinado elemento primario se seleccionarán automáticamente si este está seleccionado total o parcialmente. Por ejemplo:
            - En cuanto a Azure SQL Database o ADLS Gen2, puede limitar el ámbito del examen a partes específicas del origen de datos, como carpetas, colecciones o esquemas; para ello, marque los elementos correspondientes en la lista.
            - Para Oracle, la base de datos de Metastore de Hive y los orígenes de Teradata, se puede especificar una lista específica de esquemas que se exportarán a través de valores separados por puntos y comas o mediante patrones de nombre de esquema de expresiones de tipo SQL LIKE.
            - Para Google Big Query, se puede especificar una lista específica del conjuntos de datos que se exportarán a través de valores separados por puntos y comas.
            - Al crear un examen de toda una cuenta de AWS, puede seleccionar los cubos específicos que desea que se examinen. Al crear un examen de un cubo S3 de AWS específico, puede seleccionar las carpetas concretas que desea que se examinen.
            - Para Erwin, puede establecer el ámbito del examen mediante una lista separada por puntos y comas de cadenas de localizador de modelo.
            - Para Cassandra, la lista específica de espacios clave que se exportarán se puede especificar a través de valores separados por puntos y comas o a través de patrones de nombres de espacios de claves de expresiones de tipo SQL LIKE.
            - Para Looker, puede ampliar el alcance del examen proporcionando una lista separada por puntos y comas de los proyectos de Looker.
            - Para el inquilino de Power BI, solo puede especificar si se debe incluir o excluir un área de trabajo personal.
            - En general, se recomienda usar la opción "omitir patrones" (si se es posible) en función de los comodines (por ejemplo, para lagos de datos), y así excluir las tablas temporales, de configuración, las tablas del sistema RDMS o las tablas de copia de seguridad o STG.
            - Al examinar documentos o datos no estructurados, evite examinar un gran número de esos documentos, ya que el examen procesa los primeros 20 MB de dichos documentos y el examen puede alargarse.

    - **Conjunto de reglas de examen**
        - Al configurar el conjunto de reglas de examen, asegúrese de configurar el sistema o el conjunto de reglas de examen personalizado pertinente creado anteriormente.
        - Hay una opción para crear tipos de archivo personalizados, y puede rellenar los detalles en consecuencia. Actualmente, Azure Purview solo admite un carácter en el delimitador personalizado. Tenga en cuenta que, si usa delimitadores personalizados como ~ en los datos reales, debe crear un nuevo conjunto de reglas de examen.

    - **Tipo de examen y programación**
        - El proceso de examen se puede configurar para ejecutar exámenes completos o incrementales.
        - El proceso de examen se puede desencadenar para que se ejecute inmediatamente o se puede programar para que se ejecute periódicamente (semanalmente o mensualmente) para mantener actualizados los metadatos. El uso de recursos también depende en gran medida de la cantidad de datos que se examinen.
        - Se recomienda ejecutar los exámenes durante horas no laborables o fuera de las horas punta para evitar cualquier sobrecarga de procesamiento en el origen.
        - El examen inicial es un examen completo y cada examen posterior es incremental. Los exámenes posteriores se pueden programar como exámenes incrementales periódicos.
        - La frecuencia de los exámenes debe alinearse con la programación de administración de cambios del origen de datos o los requisitos empresariales. Por ejemplo:
            - Si la estructura de origen puede cambiarse (esto es, se agregan, modifican o eliminan nuevos recursos o campos dentro de un recurso) semanalmente, la frecuencia de examen debe estar sincronizada con la misma.
            - Si se espera que las etiquetas de clasificación o confidencialidad estén actualizadas semanalmente (puede deberse a motivos normativos), la frecuencia de examen debe ser semanal.
            Por ejemplo, si se agregan archivos de particiones cada semana en un lago de datos de origen, puede programar exámenes mensuales en lugar de semanales, ya que no se hará ningún cambio en los metadatos (suponiendo que no haya nuevos escenarios de clasificación).
            - Al programar un examen que se vaya a ejecutar el mismo día en que se crea, la hora de inicio debe ser anterior a la hora del examen (al menos un minuto).
            - La duración máxima para ejecutar el examen es de siete días (posiblemente debido a los problemas de memoria), lo que excluye el proceso de ingesta. Si el progreso no se ha actualizado después de siete días, el examen se marcará con errores. El proceso de ingesta (en el catálogo) no tiene actualmente ninguna limitación de este tipo.

    - **Cancelación de exámenes**
        - Actualmente, los exámenes solo se pueden cancelar o pausar si el estado del examen ha pasado de "En curso" a "En cola", después de desencadenar el examen.
        - Recuerde que no se admite la cancelación de un examen secundario individual.
        <!--
        - <need to add the concept of a parent and child scan
        - To remove any scanned assets from Purview you should remove asset by asset
        -->

    - **Eliminación y actualización de escenarios**
        - Si se quita un campo o columna, una tabla o un archivo del sistema de origen después de ejecutar el examen, solo se reflejará (quitará) en Purview después del siguiente examen completo o incremental programado.
        - Un recurso se puede eliminar del catálogo de Azure Purview mediante el icono de **eliminación** que está bajo el nombre del recurso (esto no quitará el objeto en el origen). Sin embargo, si ejecuta un examen completo en el mismo origen, se vuelve a ingerir en el catálogo. Si ha programado un examen semanal o mensual en su lugar (incremental), el recurso eliminado no se seleccionará a menos que el objeto se modifique en el origen (por ejemplo, si se agrega o quita una columna de la tabla).
        - Si realiza una actualización a nivel de recurso, como agregar al recurso una descripción, una clasificación a nivel de recurso, un término del glosario o un contacto, los exámenes posteriores actualizarán el esquema de recursos (el analizador detectará nuevas columnas y clasificaciones en ejecuciones de examen posteriores).
        - Si realiza una actualización a nivel de columna, como agregar una descripción, una clasificación de nivel de columna, un término del glosario o actualizar el tipo de datos o el nombre de la columna, los exámenes posteriores no actualizarán el esquema de recursos (es decir, el analizador no detectará nuevas columnas ni clasificaciones en ejecuciones de examen posteriores).

        Para obtener más información, consulte el tutorial sobre [cómo ver, editar y eliminar recursos](./catalog-asset-details.md).

## <a name="next-steps"></a>Pasos siguientes
-  [Administración de orígenes de datos](./manage-data-sources.md)
