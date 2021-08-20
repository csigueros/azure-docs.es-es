---
title: Evaluación de servidores físicos para la migración a Azure con Azure Migrate
description: Describe cómo evaluar servidores físicos locales para la migración a Azure mediante Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 44d92bbf5c3767a15e600836acd23cf3055f842e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464994"
---
# <a name="tutorial-assess-physical-servers-for-migration-to-azure"></a>Tutorial: Evaluación de servidores físicos para la migración a Azure

Como parte del recorrido de la migración a Azure, puede evaluar las cargas de trabajo locales para medir la preparación de la nube, identificar los riesgos y hacer una estimación de los costos y la complejidad.

Este artículo muestra cómo evaluar servidores físicos locales para la migración a Azure mediante la herramienta Azure Migrate: Discovery and assessment.


En este tutorial, aprenderá a:
> [!div class="checklist"]
- Ejecutar una evaluación basada en los metadatos del servidor y en la información de configuración.
- Ejecutar una valoración basada en los datos de rendimiento.

> [!NOTE]
> Los tutoriales muestran la manera más rápida de probar un escenario y utilizar las opciones predeterminadas siempre que sea posible. 

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de empezar.


## <a name="prerequisites"></a>Prerrequisitos

- Antes de seguir este tutorial a fin de valorar los servidores para la migración a máquinas virtuales de Azure, asegúrese de haber detectado los servidores que desee valorar:
    - Para detectar los servidores mediante el dispositivo de Azure Migrate, [siga este tutorial](tutorial-discover-physical.md). 
    - Para detectar los servidores mediante un archivo CSV importado, [siga este tutorial](tutorial-discover-import.md).
- Asegúrese de que en los servidores físicos que desea evaluar no se ejecutan Windows Server 2003 o SUSE Linux. No se admite la valoración en estos servidores.


## <a name="decide-which-assessment-to-run"></a>Decisión de qué valoración ejecutar


Decida si desea ejecutar una evaluación usando criterios de dimensionamiento en función de los datos o metadatos de configuración del servidor que se recopilan tal cual en el entorno local o en los datos de rendimiento dinámicos.

**Valoración** | **Detalles** | **Recomendación**
--- | --- | ---
**Tal cual en el entorno local** | Evalúe en función de los datos o los metadatos de configuración del servidor.  | El tamaño recomendado de máquina virtual de Azure se basa en el tamaño de la máquina virtual local.<br/><br> El tipo de disco recomendado de Azure se basa en lo que seleccione en la configuración de tipo de almacenamiento para la valoración.
**Basada en el rendimiento** | Valore en función de los datos de rendimiento dinámico recopilados. | El tamaño recomendado de máquina virtual de Azure se basa en los datos de uso de la CPU y la memoria.<br/><br/> El tipo de disco recomendado se basa en las IOPS y en el rendimiento de los discos locales.

## <a name="run-an-assessment"></a>Ejecución de una evaluación

Las evaluaciones se realizan como se indica a continuación:

1. En la página **Overview** (Información general) > **Windows, Linux and SQL Server** (Windows, Linux y SQL Server), haga clic en **Assess and migrate servers** (Evaluar y migrar servidores).

   ![Ubicación del botón Evaluar y migrar servidores.](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. En **Azure Migrate: Discovery and assessment**, haga clic en **Assess** (Evaluar).

    ![Ubicación del botón Evaluar](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. En **Evaluar los servidores** > **Tipo de valoración**, seleccione **Máquina virtual de Azure**.
4. En **Origen de detección**:

    - Si ha detectado servidores mediante el dispositivo, seleccione **Servers discovered from Azure Migrate appliance** (Servidores detectados desde el dispositivo de Azure Migrate).
    - Si ha detectado servidores mediante un archivo CSV importado, seleccione **Imported servers** (Servidores importados). 
    
1. Haga clic en **Editar** para revisar las propiedades de la evaluación.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assessment-name.png" alt-text="Ubicación del botón Editar para revisar las propiedades de la evaluación":::

1. En **Assessment properties** (Propiedades de la evaluación)  > **Propiedades de destino**:
    - En **Ubicación de destino**, especifique la región de Azure a la que desee migrar.
        - Las recomendaciones de tamaño y costo se basan en la ubicación que especifique. Una vez que cambie la ubicación de destino predeterminada, se le pedirá que especifique **Instancias reservadas** y **Serie de VM**.
        - En Azure Government, puede dirigir las valoraciones en [estas regiones](migrate-support-matrix.md#supported-geographies-azure-government).
    - En **Tipo de almacenamiento**:
        - Si desea usar datos basados en el rendimiento en la valoración, seleccione **Automático** para que Azure Migrate recomiende un tipo de almacenamiento, en función de la IOPS y del rendimiento del disco.
        - Como alternativa, seleccione el tipo de almacenamiento que desee usar para la máquina virtual en la migración.
    - En **Instancias reservadas**, especifique si desea usar las instancias reservadas para la máquina virtual en la migración.
        - Si selecciona usar una instancia reservada, no puede especificar '**Descuento (%)** ni **Tiempo de actividad de la máquina virtual**. 
        - [Más información](https://aka.ms/azurereservedinstances).
 1. En **Tamaño de VM**:
     - En **Sizing criterion** (Criterio de tamaño), seleccione si desea basar la valoración en metadatos o datos de configuración del servidor, o bien en datos basados en el rendimiento. Si utiliza datos de rendimiento:
        - En **Historial de rendimiento**, indique la duración de los datos en los que desee basar la valoración.
        - En **Uso de percentil**, especifique el valor de percentil que desee utilizar para la muestra de rendimiento. 
    - En **Series de máquinas virtuales**, especifique la serie de máquinas virtuales de Azure que desea considerar.
        - Si usa la valoración basada en el rendimiento, Azure Migrate sugiere un valor.
        - Ajuste la configuración según sea necesario. Por ejemplo, si no tiene un entorno de producción que necesite máquinas virtuales de la serie A en Azure, puede excluir esta serie de la lista.
    - En **Factor de confort**, indique el búfer que desee usar durante la valoración. Tiene en cuenta problemas como el uso estacional, el historial de rendimiento corto y los posibles aumentos en el uso futuro. Por ejemplo, si usa un factor de confort de dos:
    
        **Componente** | **Utilización efectiva** | **Agregar factor de confort (2,0)**
        --- | --- | ---
        Núcleos | 2  | 4
        Memoria | 8 GB | 16 GB
   
1. En **Precios**:
    - En **Oferta**, especifique la [oferta de Azure](https://azure.microsoft.com/support/legal/offer-details/) si está inscrito. La evaluación calcula el costo de esa oferta.
    - En **Moneda**, seleccione la moneda de facturación para la cuenta.
    - En **Descuento (%)** , agregue cualquier descuento específico de la suscripción que reciba a partir de la oferta de Azure. La configuración predeterminada es 0 %.
    - En **Tiempo de actividad de VM**, especifique el tiempo (días por mes/hora al día) que se ejecutarán las máquinas virtuales.
        - Esto resulta útil para las máquinas virtuales de Azure que no se ejecuten de forma continua.
        - Las estimaciones de costos se basan en la duración especificada.
        - El valor predeterminado es de 31 días al mes y 24 horas al día.
    - En **Suscripción a EA**, especifique si desea tener en cuenta un descuento por suscripción al Contrato Enterprise (EA) para la estimación de costos. 
    - En **Ventaja híbrida de Azure**, especifique si ya tiene una licencia de Windows Server. Si la tiene y está incluido en Active software Assurance de Windows Server Subscriptions, puede solicitar la [Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/) al incorporar licencias a Azure.

1. Haga clic en **Guardar** si realiza cambios.

    ![Propiedades de la evaluación](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

1. En **Evaluar los servidores**, haga clic en **Siguiente**.

1. En **Select servers to assess** > **Assessment name** (Seleccionar servidores que se van a evaluar > Nombre de la evaluación), especifique el nombre de la evaluación. 

1. En **Seleccionar o crear un grupo**, seleccione **Crear nuevo** y especifique un nombre de grupo. 
    

1. Seleccione el dispositivo y las máquinas virtuales que desee agregar al grupo. A continuación, haga clic en **Siguiente**.


1. En **Revisar y crear valoración**, revise los detalles de la valoración y haga clic en **Crear evaluación** para crear el grupo y ejecutar la evaluación.

1. Una vez creada la evaluación, la puede consultar en **Servers** > **Azure Migrate: Detección y evaluación** > **Assessments** (Servidores > Azure Migrate: Discovery and assessment > Evaluaciones).

1. Haga clic en **Exportar la evaluación** para descargarla como un archivo de Excel.
    > [!NOTE]
    > En el caso de las valoraciones basadas en el rendimiento, se recomienda esperar al menos un día después de iniciar la detección antes de crear una. De este modo, gana tiempo para recopilar los datos de rendimiento con mayor confianza. Lo mejor es que, después de iniciar la detección, espere el tiempo que especifique (día/semana/mes) para que la confianza sea alta.

## <a name="review-an-assessment"></a>Revisión de una evaluación

Una evaluación describe:

- **Preparación para Azure**: si las máquinas virtuales son adecuadas para la migración a Azure.
- **Estimación del costo mensual**: los costos mensuales estimados de proceso y almacenamiento por ejecutar las máquinas virtuales en Azure.
- **Estimación del costo mensual de almacenamiento**: costos estimados del almacenamiento en disco después de la migración.

Para ver una evaluación:

1. Vaya a **Windows, Linux and SQL Server** > **Azure Migrate: Discovery and assessment** (Windows, Linux y SQL Server > Azure Migrate: Discovery and assessment) y haga clic en el número que aparece **Assessments** (Evaluaciones).
2. En **Evaluaciones**, seleccione una evaluación para abrirla. Como ejemplo (solo estimaciones y costos, por ejemplo): 

    ![Resumen de evaluaciones](./media/tutorial-assess-physical/assessment-summary.png)

3. Revise el resumen de valoraciones. También puede editar las propiedades de la valoración o volver a calcularla.
 
 
### <a name="review-readiness"></a>Revisión de la preparación

1. Haga clic en **Preparación para Azure**.
2. En **Preparación para Azure**, revise el estado de la máquina virtual:
    - **Preparada para Azure**: se usa cuando Azure Migrate recomienda unas estimaciones del tamaño y costo de las máquinas virtuales en la valoración.
    - **Preparado con condiciones**: muestra los problemas y las soluciones que se sugieren.
    - **No está preparado para Azure**: muestra los problemas y las soluciones que se sugieren.
    - **Preparación desconocida**: se usa cuando Azure Migrate no puede valorar la preparación debido a problemas de disponibilidad de datos.

3. Seleccione uno de los estados de **Preparación para Azure**. Puede ver los detalles de preparación de la máquina virtual. También puede explorar en profundidad para ver los detalles de la máquina virtual, incluida la configuración de proceso, almacenamiento y red.

### <a name="review-cost-estimates"></a>Revisar estimaciones de costos

El resumen de evaluación muestra el costo estimado de almacenamiento y proceso que conlleva ejecutar máquinas virtuales en Azure. 

1. Revise los costos totales mensuales. Los costos se agregan para todas las máquinas virtuales del grupo evaluado.

    - Las estimaciones de costo se basan en las recomendaciones de tamaño de un servidor, así como de sus discos y propiedades.
    - Se muestran los costos mensuales estimados de proceso y almacenamiento.
    - La estimación de costos es para ejecutar las máquinas virtuales locales en las máquinas virtuales de Azure. La estimación no tiene en cuenta los costos de PaaS o SaaS.

2. Revise los costos de almacenamiento mensuales. Esta vista muestra los costos de almacenamiento agregados del grupo evaluado, divididos por los diferentes tipos de discos de almacenamiento. 
3. Puede explorar en profundidad para ver los detalles de costo de máquinas virtuales específicas.

### <a name="review-confidence-rating"></a>Examen de la clasificación de confianza

Azure Migrate asigna una clasificación de confianza a las evaluaciones basadas en el rendimiento. La clasificación es de una estrella (más baja) a cinco estrellas (más alta).

![Clasificación de confianza](./media/tutorial-assess-physical/confidence-rating.png)

La clasificación de confianza sirve de ayuda para calcular la confiabilidad de las recomendaciones de tamaño de la evaluación. Esta clasificación se basa en la disponibilidad de los puntos de datos necesarios para calcular tal valoración.

> [!NOTE]
> Las clasificaciones de confianza no se asignan si crea una valoración basada en un archivo CSV.

Las clasificaciones de confianza son las siguientes.

**Disponibilidad del punto de datos** | **Clasificación de confianza**
--- | ---
0 % - 20 % | 1 estrella
21 % - 40 % | 2 estrellas
41 % - 60 % | 3 estrellas
61 % - 80 % | 4 estrellas
81 % - 100 % | 5 estrellas

[Obtenga más información](concepts-assessment-calculation.md#confidence-ratings-performance-based) sobre la clasificación de confianza.

## <a name="next-steps"></a>Pasos siguientes

- Busque dependencias del servidor mediante la [asignación de dependencias](concepts-dependency-visualization.md).
- Configure la asignación de dependencias [basada en agente](how-to-create-group-machine-dependencies.md).
