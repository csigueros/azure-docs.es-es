---
title: Tutorial para evaluar instancias de SQL para la migración a Azure SQL Managed Instance y Azure SQL Database
description: Obtenga información sobre cómo crear una evaluación para Azure SQL en Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 02/07/2021
ms.openlocfilehash: 33c4b7f00bcba89c973838c7382397c447d3464c
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114204566"
---
# <a name="tutorial-assess-sql-instances-for-migration-to-azure-sql"></a>Tutorial: Evaluación de instancias de SQL para la migración a Azure SQL

Como parte del recorrido de la migración a Azure, puede evaluar las cargas de trabajo locales para medir la preparación de la nube, identificar los riesgos y hacer una estimación de los costos y la complejidad.
En este artículo se explica cómo evaluar las base de datos de instancia de SQL Server detectadas que están en preparación para la migración a Azure SQL mediante la herramienta de detección y evaluación de Azure Migrate.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Ejecutar una evaluación en función de la configuración del servidor y los datos de rendimiento.
> * Revisar una evaluación de Azure SQL 

> [!NOTE]
> Los tutoriales muestran la manera más rápida de probar un escenario y utilizar las opciones predeterminadas siempre que sea posible. 

> [!NOTE]
> Si las instancias de SQL Server se ejecutan en plataformas que no son de VMware. [Evalúe la preparación de la migración de un patrimonio de datos de SQL Server a Azure SQL Database mediante Data Migration Assistant](/sql/dma/dma-assess-sql-data-estate-to-sqldb).

## <a name="prerequisites"></a>Requisitos previos

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de empezar.

- Antes de seguir este tutorial para evaluar las instancias de SQL Server para la migración a Azure SQL, asegúrese de que ha detectado las instancias de SQL que desea evaluar mediante el dispositivo de Azure Migrate; para ello, [siga este tutorial](tutorial-discover-vmware.md).
- Si quiere probar esta característica en un proyecto existente, asegúrese de haber completado los [requisitos previos](how-to-discover-sql-existing-project.md) de este artículo.


## <a name="run-an-assessment"></a>Ejecución de una evaluación
Las evaluaciones se realizan como se indica a continuación:
1. En la página **Overview** (Información general) > **Windows, Linux and SQL Server** (Windows, Linux y SQL Server), haga clic en **Assess and migrate servers** (Evaluar y migrar servidores).
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Página de información general de Azure Migrate":::
2. En **Azure Migrate: Discovery and assessment**, (Azure Migrate: Detección y evaluación), haga clic en **Assess** (Evaluar) y elija el tipo de evaluación **Azure SQL**.
    :::image type="content" source="./media/tutorial-assess-sql/assess.png" alt-text="Lista desplegable en la que se puede elegir el tipo de evaluación Azure SQL":::
3. En **Assess servers** (Evaluar los servidores), podrá ver que el tipo de evaluación preseleccionado es **Azure SQL** y que el origen de detección se ha establecido de manera predeterminada en **Machines discovered from Azure Migrate appliance** (Máquinas detectadas desde el dispositivo de Azure Migrate).

4. Haga clic en **Editar** para revisar las propiedades de la evaluación.
     :::image type="content" source="./media/tutorial-assess-sql/assess-servers-sql.png" alt-text="Botón Editar desde el que se pueden personalizar las propiedades de la evaluación":::
5. En Assessment properties (Propiedades de evaluación) > **Target Properties** (Propiedades de destino):
    - En **Ubicación de destino**, especifique la región de Azure a la que desee migrar. 
        - La configuración de Azure SQL y los costos recomendados dependen de la ubicación que especifique. 
    - En **Target deployment type** (Tipo de implementación de destino):
        - Seleccione **Recommended** (Recomendado) si desea que Azure Migrate evalúe la preparación de las instancias de SQL para su migración a Azure SQL MI y Azure SQL DB, y recomiende la opción de implementación de destino, el nivel de destino, la configuración de Azure SQL y las estimaciones mensuales que mejor se adapten. [Más información](concepts-azure-sql-assessment-calculation.md)
        - Seleccione **Azure SQL DB** si quiere evaluar la preparación de las instancias de SQL para migrar exclusivamente a Azure SQL Database y revisar el nivel de destino, la configuración de Azure SQL y las estimaciones mensuales.
        - Seleccione **Azure SQL MI** si quiere evaluar la preparación de las instancias de SQL para migrar exclusivamente a Azure SQL Managed Instance y revisar el nivel de destino, la configuración de Azure SQL y las estimaciones mensuales.
    - En **Reserved Capacity** (Capacidad reservada), especifique si desea usar capacidad reservada para el servidor SQL Server después de la migración.
        - Si selecciona una opción de capacidad reservada, no podrá especificar "Descuento (%)".

6. En Assessment properties (Propiedades de evaluación) > **Assessment criteria** (Criterios de evaluación):
    - El valor de Sizing criteria (Criterios de ajuste de tamaño) está establecido de manera predeterminada en **Performance-based** (Basado en el rendimiento), lo que significa que Azure Migrate recopilará las métricas de rendimiento relativas a las bases de datos y las instancias de SQL que administra para recomendar una configuración de Azure SQL Database y Azure SQL Managed Instance que tenga el tamaño óptimo. Puede especificar:
        - **Performance history** (Historial de rendimiento), para indicar la duración de los datos en los que desea basar la evaluación. (El valor predeterminado es un día).
        - **Percentile utilization** (Utilización del percentil), para especificar el valor de percentil que desea utilizar con la muestra de rendimiento. (El valor predeterminado es el percentil 95).
    - En **Factor de confort**, indique el búfer que desee usar durante la valoración. Tiene en cuenta problemas como el uso estacional, el historial de rendimiento corto y los posibles aumentos en el uso futuro. Por ejemplo, si usa un factor de confort de dos: 
        
        **Componente** | **Utilización efectiva** | **Agregar factor de confort (2,0)**
        --- | --- | ---
        Núcleos | 2  | 4
        Memoria | 8 GB | 16 GB
   
7. En **Precios**:
    - En **Offer/Licensing program** (Oferta/programa de licencias), especifique la oferta de Azure en la que está inscrito. En la actualidad, solo puede elegir entre Pay-as-you-go (Pago por uso) y Pay-as-you-go Dev/Test (Desarrollo/pruebas - Pago por uso). 
        - Puede beneficiarse de descuentos adicionales combinando la capacidad reservada y la Ventaja híbrida de Azure con la oferta de pago por uso. 
        - Puede combinar la Ventaja híbrida de Azure con la opción de desarrollo y pruebas de pago por uso. Actualmente, la evaluación no permite combinar la capacidad reservada con la oferta de desarrollo y pruebas de pago por uso.
    - En **Service Tier** (Nivel de servicio), elija la opción de nivel de servicio que mejor se ajuste a las necesidades de la empresa para migrar a Azure SQL Database o Azure SQL Managed Instance: 
        - Seleccione **Recommended** (Recomendado) si desea que Azure Migrate recomiende el nivel de servicio que se ajuste mejor a los servidores. Puede ser De uso general o Crítico para la empresa. Más información
        - Seleccione **General Purpose** (De uso general) si desea utilizar una configuración de Azure SQL diseñada para cargas de trabajo orientadas al presupuestos.
        - Seleccione **Business Critical** (Crítico para la empresa) si desea utilizar una configuración de Azure SQL diseñada para cargas de trabajo de baja latencia con alta resistencia a errores y conmutaciones por error rápidas.
    - En **Descuento (%)** , agregue cualquier descuento específico de la suscripción que reciba a partir de la oferta de Azure. La configuración predeterminada es 0 %.
    - En **Moneda**, seleccione la moneda de facturación para la cuenta.
    - En **Azure Hybrid Benefit** (Ventaja híbrida de Azure), especifique si ya tiene una licencia de SQL Server. Si la tiene y está incluida en las suscripciones activas de Software Assurance para SQL Server, podrá solicitar la Ventaja híbrida de Azure cuando traiga sus licencias a Azure.
    - Haga clic en Guardar si realiza cambios.
     :::image type="content" source="./media/tutorial-assess-sql/view-all.png" alt-text="Botón Guardar de Propiedades de evaluación":::
8. En **Assess Servers** (Evaluar los servidores), haga clic en Next (Siguiente).
9.  En **Select servers to assess** > **Assessment name** (Seleccionar servidores que se van a evaluar > Nombre de la evaluación), especifique el nombre de la evaluación.
10. En **Seleccionar o crear un grupo**, seleccione **Crear nuevo** y especifique un nombre de grupo.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-add-servers.png" alt-text="Ubicación del botón para crear un nuevo grupo":::
11. Seleccione el dispositivo y los servidores que desee agregar al grupo. A continuación, haga clic en Siguiente.
12. En **Revisar y crear valoración**, revise los detalles de la valoración y haga clic en Crear evaluación para crear el grupo y ejecutar la evaluación.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-create.png" alt-text="Ubicación del botón para revisar y crear una evaluación.":::
13. Una vez creada la evaluación, vaya al icono **Windows, Linux and SQL Server** > **Azure Migrate: Discovery and assessment** (Windows, Linux y SQL Server > Azure Migrate: Detección y evaluación) y haga clic en el número que aparece junto a la evaluación de Azure SQL.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-navigation.png" alt-text="Acceso a la evaluación creada":::
15. Haga clic en el nombre de la evaluación que desea ver.

> [!NOTE]
> Como las evaluaciones de Azure SQL están basadas en el rendimiento, se recomienda esperar al menos un día después del inicio de la detección antes de crear una evaluación. De este modo, gana tiempo para recopilar los datos de rendimiento con mayor confianza. Si la detección aún está en curso, el nivel de preparación de las instancias de SQL aparecerá como **desconocido**. Lo mejor es que, después de iniciar la detección, **espere la duración de rendimiento que haya especificado (día/semana/mes)** para crear o volver a calcular la evaluación y conseguir una calificación de confianza alta. 

## <a name="review-an-assessment"></a>Revisión de una evaluación

**Para ver una evaluación**:

1. Vaya a **Windows, Linux and SQL Server** > **Azure Migrate: Discovery and assessment** (Windows, Linux y SQL Server > Azure Migrate: Detección y evaluación) y haga clic en el número que aparece junto a la evaluación de Azure SQL.
2. Haga clic en el nombre de la evaluación que desea ver. Veamos un ejemplo (las estimaciones y los costos son ficticios): :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-summary.png" alt-text="Información general sobre la evaluación de SQL":::
3. Revise el resumen de valoraciones. También puede editar las propiedades de la evaluación o volver a calcularla.

#### <a name="discovered-items"></a>Elementos detectados

Indica el número de servidores, instancias y bases de datos de SQL que se han incluido en esta evaluación.
    
#### <a name="azure-readiness"></a>Preparación para Azure

Indica la distribución de las instancias de SQL evaluadas: 
    
**Tipo de implementación de destino (en las propiedades de evaluación)** | **Preparación**   
--- | --- |
**Recomendado** |  Las instancias están preparadas para Azure SQL Database y Azure SQL Managed Instance, potencialmente preparadas para Azure VM o se desconoce su nivel de preparación (en caso de que la detección esté en curso o haya que solucionar algún problema con la detección)
**Azure SQL DB** o **Azure SQL MI** | Las instancias están preparadas para Azure SQL Database o Azure SQL Managed Instance, no están preparadas para Azure SQL Database o Azure SQL Managed Instance, o se desconoce su nivel de preparación (en caso de que la detección esté en curso o haya que solucionar algún problema con la detección)
     
Puede profundizar para más detalles sobre los problemas o advertencias de la migración que puede corregir antes de migrar a Azure SQL. Si necesita [más información](concepts-azure-sql-assessment-calculation.md), también puede consultar las configuraciones de Azure SQL recomendadas para migrar a bases de datos o instancias administradas de Azure SQL.
    
#### <a name="azure-sql-database-and-managed-instance-cost-details"></a>Detalles de los costos de Azure SQL Database y Azure SQL Managed Instance

En la estimación de costos mensuales se incluyen los costos de proceso y almacenamiento de las configuraciones de Azure SQL que corresponden al tipo de implementación recomendada de Azure SQL Database o Azure SQL Managed Instance. [Más información](concepts-azure-sql-assessment-calculation.md#calculate-monthly-costs)


### <a name="review-readiness"></a>Revisión de la preparación

1. Haga clic en **Azure SQL readiness** (Preparación para Azure SQL).
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-readiness.png" alt-text="Detalles de la preparación para Azure SQL":::
1. En Azure SQL readiness (Preparación para Azure SQL), consulte el grado de preparación de las instancia de SQL evaluadas para **Azure SQL DB** y **Azure SQL MI**:
    - **Preparada**: la instancia está preparada para migrarse a Azure SQL DB/MI sin ningún problema o advertencia en relación con la migración. 
        - Preparada (hipervínculo e icono de información en color azul): la instancia está preparada para migrarse a Azure SQL DB/MI sin ningún problema de migración, pero tiene algunas advertencias de migración que es necesario revisar. Puede hacer clic en el hipervínculo para consultar las advertencias de migración y las instrucciones de corrección recomendadas: :::image type="content" source="./media/tutorial-assess-sql/assess-ready.png" alt-text="Evaluación con el estado &quot;Preparada&quot;":::.       
    - **No preparada**: la instancia tiene uno o varios problemas que impiden que pueda migrarse a Azure SQL DB o Azure SQL MI. Puede hacer clic en el hipervínculo para consultar los problemas de migración y ver las instrucciones de corrección recomendadas.
    - **Desconocido**: Azure Migrate no puede evaluar la preparación porque la detección está en curso o surgieron problemas durante la detección que deben corregirse desde la hoja de notificaciones. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Microsoft. 
1. Consulte el tipo de implementación recomendado para la instancia de SQL de acuerdo con la matriz siguiente:

    - **Tipo de implementación de destino** (el que se seleccionó en las propiedades de evaluación): **Recomendado**

        **Preparación para Azure SQL DB** | **Preparación para Azure SQL MI** | **Tipo de implementación recomendado** | **¿Se han calculado los costos estimados y la configuración de Azure SQL?**
         --- | --- | --- | --- |
        Ready | Ready | Azure SQL DB o Azure SQL MI [Más información](concepts-azure-sql-assessment-calculation.md#recommended-deployment-type) | Sí
        Ready | No preparado o Desconocido | Azure SQL DB | Sí
        No preparado o Desconocido | Ready | Azure SQL MI  | Sí
        No está listo | No está listo | Potencialmente preparada para máquina virtual de Azure [Más información](concepts-azure-sql-assessment-calculation.md#potentially-ready-for-azure-vm) | No
        No preparado o Desconocido | No preparado o Desconocido | Unknown | No
    
    - **Tipo de implementación de destino** (el que se seleccionó en las propiedades de evaluación): **Azure SQL DB**
    
        **Preparación para Azure SQL DB** | **¿Se han calculado los costos estimados y la configuración de Azure SQL?**
        --- | --- |
        Ready | Sí
        No está listo | No
        Desconocido | No
    
    - **Tipo de implementación de destino** (el que se seleccionó en las propiedades de evaluación): **Azure SQL MI**
    
        **Preparación para Azure SQL MI** | **¿Se han calculado los costos estimados y la configuración de Azure SQL?**
         --- | --- |
        Ready | Sí
        No está listo | No
        Desconocido | No

4. Haga clic en el nombre de la instancia para acceder a los detalles y ver el número de bases de datos de usuario, los datos de la instancia (como sus propiedades) y los datos sobre la capacidad de almacenamiento (solo de la instancia) y de almacenamiento de la base de datos de origen.
5. Haga clic en el número de bases de datos de usuario para consultar la lista de bases de datos y sus detalles. Veamos un ejemplo (las estimaciones y los costos son ficticios): :::image type="content" source="./media/tutorial-assess-sql/assessment-db.png" alt-text="Detalles de la instancia de SQL":::
5. En la columna Migration issues (Problemas de migración), haga clic en View details (Ver detalles) para consultar las advertencias y los problemas relacionados con la migración de un tipo de implementación de destino en particular. 
    :::image type="content" source="./media/tutorial-assess-sql/assessment-db-issues.png" alt-text="Problemas y advertencias de la migración de bases de datos":::

### <a name="review-cost-estimates"></a>Revisar estimaciones de costos
En el resumen de la evaluación se muestran los costos de procesamiento y almacenamiento mensuales estimados de las configuraciones de Azure SQL correspondientes al tipo de implementación recomendado de Azure SQL Database o Azure SQL Managed Instance.

1. Revise los costos totales mensuales. Se suman los costos de todas las instancias de SQL del grupo evaluado.
    - Las estimaciones de costos dependen de la configuración de Azure SQL recomendada para una instancia. 
    - Se muestran los costos mensuales estimados de proceso y almacenamiento. Veamos un ejemplo (las estimaciones y los costos son ficticios):
    
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-cost.png" alt-text="Detalles del costo":::

1. Puede profundizar hasta el nivel de la instancia para ver la configuración de Azure SQL y las estimaciones de costos en ese nivel.  
1. También puede profundizar en la lista de bases de datos para consultar la configuración de Azure SQL y las estimaciones de costos de cada base de datos cuando la configuración recomendada es la de Azure SQL Database.

### <a name="review-confidence-rating"></a>Examen de la clasificación de confianza
Azure Migrate asigna una calificación de confianza a todas las evaluaciones de Azure SQL en función de la disponibilidad de los puntos de datos de rendimiento y uso necesarios para calcular la evaluación de todas las instancias y las bases de datos de SQL evaluadas. La clasificación es de una estrella (más baja) a cinco estrellas (más alta).
La clasificación de confianza sirve de ayuda para calcular la confiabilidad de las recomendaciones de tamaño de la evaluación. Las calificaciones de confianza son las siguientes:

**Disponibilidad del punto de datos** | **Clasificación de confianza**
--- | ---
0 % - 20 % | 1 estrella
21 % - 40 % | 2 estrellas
41 % - 60 % | 3 estrellas
61 % - 80 % | 4 estrellas
81 % - 100 % | 5 estrellas

[Obtenga más información](concepts-azure-sql-assessment-calculation.md#confidence-ratings) sobre la clasificación de confianza.


## <a name="next-steps"></a>Pasos siguientes

- [Más información](concepts-azure-sql-assessment-calculation.md) sobre el cálculo de las evaluaciones de Azure SQL.
- Comience a migrar instancias y bases de datos de SQL con [Azure Database Migration Service](../dms/dms-overview.md).