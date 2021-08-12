---
title: Crear una valoración de AVS con Azure Migrate | Microsoft Docs
description: En este artículo se describe cómo crear una valoración de AVS con Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: f7014f8a403614740bdbc05abab4e7024f83c196
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570527"
---
# <a name="create-an-azure-vmware-solution-assessment"></a>Creación de una evaluación de Azure VMware Solution

En este artículo se describe cómo crear una valoración de Azure VMware Solution para servidores locales de entornos VMware con Azure Migrate: Detección y evaluación.

[Azure Migrate](migrate-services-overview.md) le ayuda a migrar a Azure. Azure Migrate proporciona un centro principal para realizar el seguimiento de la detección, valoración y migración de infraestructuras, aplicaciones y datos locales a Azure. El centro proporciona herramientas de Azure para la valoración y migración, así como ofertas de proveedores de software independientes (ISV) externos.

## <a name="before-you-start"></a>Antes de comenzar

- Asegúrese de que ha [creado](./create-manage-projects.md) un proyecto de Azure Migrate.
- Si ya ha creado un proyecto, asegúrese de que ha [agregado](how-to-assess.md) la herramienta Azure Migrate: Detección y evaluación.
- Para crear una evaluación, debe configurar un dispositivo con Azure Migrate para [VMware](how-to-set-up-appliance-vmware.md), que detecta los servidores locales y envía metadatos y datos de rendimiento a Azure Migrate: Detección y evaluación. [Más información](migrate-appliance.md).
- También podría [importar los metadatos de servidor](./tutorial-discover-import.md) en formato de valores separados por comas (CSV).


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Información general sobre la valoración de Azure VMware Solution (AVS)

Existen tres tipos de evaluaciones que puede crear con Azure Migrate: Detección y evaluación.

***Tipo de evaluación** | **Detalles**
--- | --- 
**MV de Azure** | Evaluaciones para la migración de los servidores locales a máquinas virtuales de Azure. Con este tipo de evaluación, puede evaluar los servidores locales en los entornos de [VMware](how-to-set-up-appliance-vmware.md) e [Hyper-V](how-to-set-up-appliance-hyper-v.md) y los [servidores físicos](how-to-set-up-appliance-physical.md) para la migración a máquinas virtuales de Azure.
**SQL de Azure** | Evaluaciones para migrar las instancias locales de SQL Server desde su entorno de VMware a Azure SQL Database o a Azure SQL Managed Instance.
**Azure VMware Solution (AVS)** | Evaluaciones para la migración de los servidores locales a [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). Puede evaluar los servidores locales [del entorno VMware](how-to-set-up-appliance-vmware.md) para la migración a Azure VMware Solution (AVS) con este tipo de evaluación. [Más información](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> La evaluación de Azure VMware Solution (AVS) solo se puede crear para servidores del entorno VMware.


Existen dos tipos de criterios de tamaño que puede usar para crear valoraciones de Azure VMware Solution (AVS):

**Valoración** | **Detalles** | **Data**
--- | --- | ---
**Basada en el rendimiento** | Evaluaciones basadas en los datos de rendimiento recopilados de los servidores locales. | **Tamaño del nodo recomendado**: se basa en los datos de uso de la CPU y de la memoria junto con el tipo de nodo, el tipo de almacenamiento y la configuración de FTT seleccionados para la evaluación.
**Como local** | Evaluaciones que se basan en el tamaño local. | **Tamaño del nodo recomendado**: se basa en el tamaño del servidor local junto con el tipo de nodo, el tipo de almacenamiento y la configuración de FTT seleccionados para la evaluación.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Ejecución de una valoración de Azure VMware Solution (AVS)

1.  En la página **Overview** (Información general) > **Windows, Linux and SQL Server** (Windows, Linux y SQL Server), haga clic en **Assess and migrate servers** (Evaluar y migrar servidores).
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Página de información general de Azure Migrate":::

1. En **Azure Migrate: Detección y evaluación**, haga clic en **Evaluar**.

   ![Ubicación del botón Evaluar](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. En **Evaluar los servidores** > **Tipo de evaluación**, seleccione **Azure VMware Solution (AVS)** .

1. En **Origen de detección**:

    - Si ha detectado servidores mediante el dispositivo, seleccione **Servers discovered from Azure Migrate appliance** (Servidores detectados desde el dispositivo de Azure Migrate).
    - Si ha detectado servidores mediante un archivo CSV importado, seleccione **Imported servers** (Servidores importados). 
    
1. Haga clic en **Editar** para revisar las propiedades de la evaluación.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Página para seleccionar la configuración de la evaluación":::
 

1. En **Assessment properties** (Propiedades de la evaluación)  > **Propiedades de destino**:

    - En **Ubicación de destino**, especifique la región de Azure a la que desee migrar.
       - Las recomendaciones de tamaño y costo se basan en la ubicación que especifique.
   - El **tipo de almacenamiento** tiene como valor predeterminado **vSAN**. Es el predeterminado para una nube privada de AVS.
   - Las **instancias reservadas** no se admiten actualmente para los nodos AVS.
1. En **Tamaño de VM**:
    - El **tipo de nodo** tiene como valor predeterminado **AV36**. Azure Migrate recomienda el tipo de nodos necesarios para migrar los servidores a AVS.
    - En **FTT setting, RAID level** (Configuración de FTT, nivel de RAID), seleccione la combinación de tolerancia a errores y RAID.  La opción de FTT seleccionada junto con el requisito de disco del servidor local determinará el almacenamiento de vSAN total que se requiere en AVS.
    - En **CPU Oversubscription** (Suscripción excesiva de CPU), especifique la proporción de núcleos virtuales asociados a un núcleo físico en el nodo AVS. Tenga en cuenta que una suscripción excesiva de vCPU mayor que 4:1 puede producir una degradación del rendimiento, pero se puede usar para cargas de trabajo del tipo del servidor web.
    - En **Memory overcommit factor** (Factor de asignación excesiva de memoria), especifique la relación de asignación excesiva de memoria en el clúster. Un valor de 1 representa el 100 % del uso de memoria; 0,5 el 50 % y 2 sería usar el 200 % de la memoria disponible. Solo puede agregar valores del 0,5 al 10 con hasta una posición decimal.
    - En **Dedupe and compression factor** (Eliminar duplicados y factor de compresión), especifique la eliminación de duplicados y el factor de compresión previstos para las cargas de trabajo. El valor real se puede obtener del vSAN local o de la configuración de almacenamiento, y puede variar en función de la carga de trabajo. Un valor de 3 significaría el triple, por lo que para un disco de 300 GB solo se utilizarían 100 GB de almacenamiento. Un valor de 1 significa que no hay eliminación de duplicados ni compresión. Solo puede agregar valores del 1 al 10 con hasta una posición decimal.
1. En **Tamaño del nodo**: 
    - En **Criterio de tamaño**, seleccione si desea basar la evaluación en metadatos estáticos o en datos basados en el rendimiento. Si utiliza datos de rendimiento:
        - En **Historial de rendimiento**, indique la duración de los datos en los que desee basar la valoración.
        - En **Uso de percentil**, especifique el valor de percentil que desee utilizar para la muestra de rendimiento. 
    - En **Factor de confort**, indique el búfer que desee usar durante la valoración. Tiene en cuenta problemas como el uso estacional, el historial de rendimiento corto y los posibles aumentos en el uso futuro. Por ejemplo, si usa un factor de confort de dos:
    
        **Componente** | **Utilización efectiva** | **Agregar factor de confort (2,0)**
        --- | --- | ---
        Núcleos | 2  | 4
        Memoria | 8 GB | 16 GB  

1. En **Precios**:
    - En **Oferta**, se muestra la [oferta de Azure](https://azure.microsoft.com/support/legal/offer-details/) en la que está inscrito. La evaluación calcula el costo de esa oferta.
    - En **Moneda**, seleccione la moneda de facturación para la cuenta.
    - En **Descuento (%)** , agregue cualquier descuento específico de la suscripción que reciba a partir de la oferta de Azure. La configuración predeterminada es 0 %.

1. Haga clic en **Guardar** si realiza cambios.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Propiedades de la evaluación":::

1. En **Evaluar los servidores**, haga clic en **Siguiente**.

1. En **Select servers to assess** > **Assessment name** (Seleccionar servidores que se van a evaluar > Nombre de la evaluación), especifique el nombre de la evaluación. 
 
1. En **Seleccionar o crear un grupo**, seleccione **Crear nuevo** y especifique un nombre de grupo. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Adición de servidores a un grupo":::
 
1. Seleccione el dispositivo y los servidores que desee agregar al grupo. A continuación, haga clic en **Siguiente**.

1. En **Revisar y crear valoración**, revise los detalles de la valoración y haga clic en **Crear evaluación** para crear el grupo y ejecutar la evaluación.

    > [!NOTE]
    > En el caso de las valoraciones basadas en el rendimiento, se recomienda esperar al menos un día después de iniciar la detección antes de crear una. De este modo, gana tiempo para recopilar los datos de rendimiento con mayor confianza. Lo mejor es que, después de iniciar la detección, espere el tiempo que especifique (día/semana/mes) para que la confianza sea alta.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Revise una valoración de Azure VMware Solution (AVS)

Una valoración de Azure VMware Solution (AVS) describe:

- **Preparación de Azure VMware Solution (AVS)** : si los servidores locales son adecuados para la migración a Azure VMware Solution (AVS).
- **Número de nodos de AVS**: número estimado de nodos de AVS necesarios para ejecutar los servidores.
- **Uso en los nodos de AVS**: uso previsto de CPU, memoria y almacenamiento en todos los nodos.
    - El uso incluye la factorización inicial de las siguientes sobrecargas de administración del clúster, como vCenter Server, NSX Manager (grande) y NSX Edge; si HCX está implementado, también las de HCX Manager y el dispositivo IX que consume ~ 44 CPU virtuales (11 CPU), 75 GB de RAM y 722 GB de almacenamiento antes de la compresión y la desduplicación.
- **Estimación del costo mensual**: los costos mensuales estimados de todos los nodos de Azure VMware Solution (AVS) que ejecutan las máquinas virtuales locales.


### <a name="view-an-assessment"></a>Visualización de una evaluación

1. En **Windows, Linux and SQL Server** > **Azure Migrate: Discovery and assessment** (Windows, Linux y SQL Server > Azure Migrate: Discovery and assessment), haga clic en el número que aparece junto a ** Azure VMware Solution**.

1. En **Evaluaciones**, seleccione una evaluación para abrirla. Como ejemplo (solo estimaciones y costos, por ejemplo): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Resumen de la valoración de AVS":::

1. Revise el resumen de valoraciones. También puede editar las propiedades de la valoración o volver a calcularla.

### <a name="review-azure-vmware-solution-avs-readiness"></a>Revisión de la preparación de Azure VMware Solution (AVS)

1. En **Preparación para Azure**, compruebe si los servidores están listos para su migración a AVS.

2. Revisar el estado del servidor:
    - **Listo para AVS**: el servidor se puede migrar como está a Azure (AVS) sin realizar ningún cambio. Se iniciará en AVS con soporte técnico de AVS completo.
    - **Preparado con condiciones**: es posible que el servidor tenga problemas de compatibilidad con la versión actual de vSphere y que, además, requiera herramientas de VMware y otras opciones de configuración antes de que pueda conseguirse la funcionalidad completa del servidor en AVS.
    - **No está listo para AVS**: el servidor no se iniciará en AVS. Por ejemplo, si el servidor VMware local tiene un dispositivo externo conectado, como un CD-ROM, se producirá un error en la operación de VMotion (si se usa VMware VMotion).
    - **Preparación desconocida**: Azure Migrate no ha podido encontrar la preparación del servidor debido a una recopilación insuficiente de metadatos en el entorno local.

3. Revise la herramienta sugerida:
    - **VMware HCX o Enterprise**: En el caso de los servidores de VMware, la solución Hybrid Cloud Extension (HCX) de VMware es la herramienta de migración sugerida para migrar la carga de trabajo local a la nube privada de Azure VMware Solution (AVS). [Más información](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **Desconocido**: en el caso de los servidores importados mediante un archivo CSV, se desconoce la herramienta de migración predeterminada. Sin embargo, para los servidores de VMware, se recomienda usar la solución Hybrid Cloud Extension (HCX) de VMware. 

4. Haga clic en un estado de **Preparación para AVS**. Puede ver los detalles de la preparación de la máquina virtual y explorar en profundidad los detalles de esta, entre los que se incluye la configuración de proceso, almacenamiento y red.

### <a name="review-cost-details"></a>Revisión de los datos de costo

Esta vista muestra el costo estimado de ejecución de servidores en Azure VMware Solution (AVS).

1. Revise los costos totales mensuales. Los costos se agregan para todos los servidores del grupo evaluado. 

    - Las estimaciones de los costos se basan en el número de nodos de AVS necesarios teniendo en cuenta los requisitos de recursos de todos los servidores en total.
    - Como los precios de Azure VMware Solution (AVS) son por nodo, el costo total no tiene costo de proceso ni distribución de costos de almacenamiento.
    - La estimación de costos es por la ejecución de los servidores en el entorno local en AVS. La evaluación de AVS no tiene en cuenta los costos de PaaS o SaaS.
    
2. Puede revisar las estimaciones del costo de almacenamiento mensual. Esta vista muestra los costos de almacenamiento agregados del grupo evaluado, divididos por los diferentes tipos de discos de almacenamiento.

3. Puede explorar en profundidad para ver los detalles de servidores concretos.


### <a name="review-confidence-rating"></a>Examen de la clasificación de confianza

Cuando se realizan valoraciones basadas en el rendimiento, se asigna una clasificación de confianza a la evaluación.

![Clasificación de confianza](./media/how-to-create-assessment/confidence-rating.png)

- Se concede una clasificación que oscila entre 1 estrella (la más baja) y 5 estrellas (la más alta).
- La clasificación de confianza sirve de ayuda para calcular la confiabilidad de las recomendaciones de tamaño que proporciona la evaluación.
- La clasificación de confianza se basa en la disponibilidad de los puntos de datos necesarios para calcular tal evaluación.
- Para dimensionamiento basado en el rendimiento, las evaluaciones de AVS necesitan los datos de uso de la CPU y de la memoria del servidor. Los siguientes datos de rendimiento se recopilan, pero no se usan en las recomendaciones de ajuste de tamaño de las valoraciones de AVS:
  - Los datos de IOPS y rendimiento de cada disco conectado al servidor.
  - La entrada o la salida de red de cada adaptador de red conectado a un servidor.

Estas son las posibles clasificaciones de confianza de una evaluación.

**Disponibilidad del punto de datos** | **Clasificación de confianza**
--- | ---
0 % - 20 % | 1 estrella
21 % - 40 % | 2 estrellas
41 % - 60 % | 3 estrellas
61 % - 80 % | 4 estrellas
81 % - 100 % | 5 estrellas

[Más información](concepts-azure-vmware-solution-assessment-calculation.md) sobre los datos de rendimiento 


## <a name="next-steps"></a>Pasos siguientes

- Aprenda a usar la [asignación de dependencias de máquina](how-to-create-group-machine-dependencies.md) para crear grupos de confianza alta.
- [Obtenga más información](concepts-azure-vmware-solution-assessment-calculation.md) sobre cómo se calculan las valoraciones de AVS.
