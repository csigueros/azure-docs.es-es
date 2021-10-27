---
title: Evaluación de los servidores de VMware para la migración a Azure VMware Solution (AVS) con Azure Migrate
description: Aprenda a evaluar los servidores en un entorno de VMware para la migración a AVS con Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: a2637fbfcaf1e30b1df9f0739630ea2883eba3fa
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994218"
---
# <a name="tutorial-assess-vmware-servers-for-migration-to-avs"></a>Tutorial: Evaluación de servidores de VMware para la migración a AVS

Como parte del recorrido de la migración a Azure, puede evaluar las cargas de trabajo locales para medir la preparación de la nube, identificar los riesgos y hacer una estimación de los costos y la complejidad.

En este artículo se muestra cómo evaluar los servidores de VMware detectados para la migración a Azure VMware Solution (AVS) mediante la herramienta Azure Migrate. AVS es un servicio administrado que le permite ejecutar la plataforma de VMware en Azure.

En este tutorial, aprenderá a:
> [!div class="checklist"]
- Ejecutar una evaluación basada en los metadatos del servidor y en la información de configuración.
- Ejecutar una valoración basada en los datos de rendimiento.

> [!NOTE]
> Los tutoriales muestran la manera más rápida de probar un escenario y utilizar las opciones predeterminadas siempre que sea posible. 

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de empezar.



## <a name="prerequisites"></a>Prerrequisitos

Antes de seguir este tutorial a fin de evaluar los servidores para la migración a AVS, asegúrese de haber detectado los servidores que desee evaluar:

- Para detectar los servidores mediante el dispositivo de Azure Migrate, [siga este tutorial](tutorial-discover-vmware.md). 
- Para detectar los servidores mediante un archivo CSV importado, [siga este tutorial](tutorial-discover-import.md).


## <a name="decide-which-assessment-to-run"></a>Decisión de qué valoración ejecutar


Decida si desea ejecutar una evaluación usando criterios de dimensionamiento en función de los datos o metadatos de configuración del servidor que se recopilan tal cual en el entorno local o en los datos de rendimiento dinámicos.

**Valoración** | **Detalles** | **Recomendación**
--- | --- | ---
**Tal cual en el entorno local** | Evalúe en función de los datos o los metadatos de configuración del servidor.  | El tamaño de nodo recomendado en AVS se basa en el tamaño del servidor o la máquina virtual local, junto con la configuración especificada en la evaluación para el tipo de nodo, el tipo de almacenamiento y la configuración de la tolerancia a errores.
**Basada en el rendimiento** | Valore en función de los datos de rendimiento dinámico recopilados. | El tamaño de nodo recomendado en AVS se basa en los datos de uso de CPU y memoria, junto con los valores que se especifican en la evaluación para el tipo de nodo, el tipo de almacenamiento y la configuración de la tolerancia a errores.

> [!NOTE]
> La evaluación de Azure VMware Solution (AVS) solo se puede crear para servidores o máquinas virtuales con VMware.

## <a name="run-an-assessment"></a>Ejecución de una evaluación

Las evaluaciones se realizan como se indica a continuación:

1.  En la página **Overview** (Información general) > **Windows, Linux and SQL Server** (Windows, Linux y SQL Server), haga clic en **Assess and migrate servers** (Evaluar y migrar servidores).
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Página de información general de Azure Migrate":::

1. En **Azure Migrate: Discovery and assessment**, haga clic en **Assess** (Evaluar).

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

## <a name="review-an-assessment"></a>Revisión de una evaluación

Una evaluación de AVS describe:

- **Preparación de Azure VMware Solution (AVS)** : si los servidores locales son adecuados para la migración a Azure VMware Solution (AVS).
- **Número de nodos de Azure VMware Solution**: número estimado de nodos de Azure VMware Solution necesarios para ejecutar los servidores.
- **Uso en los nodos de AVS**: uso previsto de CPU, memoria y almacenamiento en todos los nodos.
    - El uso incluye la factorización inicial de las siguientes sobrecargas de administración del clúster, como vCenter Server, NSX Manager (grande) y NSX Edge; si HCX está implementado, también las de HCX Manager y el dispositivo IX que consume ~ 44 CPU virtuales (11 CPU), 75 GB de RAM y 722 GB de almacenamiento antes de la compresión y la desduplicación.
    - El factor de limitación determina el número de hosts o nodos necesarios para alojar los recursos.
- **Estimación del costo mensual**: los costos mensuales estimados de todos los nodos de Azure VMware Solution (AVS) que ejecutan las máquinas virtuales locales.

## <a name="view-an-assessment"></a>Visualización de una evaluación

Para ver una evaluación:

1. En **Windows, Linux and SQL Server** > **Azure Migrate: Discovery and assessment** (Windows, Linux y SQL Server > Azure Migrate: Discovery and assessment), haga clic en el número que aparece junto a ** Azure VMware Solution**.

1. En **Evaluaciones**, seleccione una evaluación para abrirla. Como ejemplo (solo estimaciones y costos, por ejemplo): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Resumen de la valoración de AVS":::

1. Revise el resumen de valoraciones. Puede hacer clic en **Suposiciones de dimensionamiento** para comprender las suposiciones que se realizaron en el dimensionamiento de los nodos y el uso de recursos. También puede editar las propiedades de la valoración o volver a calcularla.
 

### <a name="review-readiness"></a>Revisión de la preparación

1. Haga clic en **Preparación para Azure**.
2. En **Preparación para Azure**, revise el estado de preparación.

    - **Listo para AVS**: el servidor se puede migrar como está a Azure AVS sin realizar ningún cambio. El servidor se iniciará en AVS con soporte técnico de AVS completo.
    - **Preparado con condiciones**: es posible que el servidor tenga problemas de compatibilidad con la versión actual de vSphere. Puede que deba tener instaladas las herramientas de VMware u otra configuración, para disfrutar de la funcionalidad completa en AVS.
    - **No está listo para AVS**: la máquina virtual no se iniciará en AVS. Por ejemplo, si un servidor de VMware local tiene un dispositivo externo (como un CD-ROM) conectado y va a usar VMware VMotion, se producirá un error en la operación de VMotion.
 - **Preparación desconocida**: Azure Migrate no ha podido determinar la preparación del servidor debido a una recopilación insuficiente de metadatos en el entorno local.

3. Revise la herramienta sugerida.

    - VMware HCX o Enterprise: en el caso de los servidores de VMware, la solución Hybrid Cloud Extension (HCX) de VMware es la herramienta de migración sugerida para migrar la carga de trabajo local a la nube privada de Azure VMware Solution (AVS). Obtenga más información en.
    - Desconocido: en el caso de los servidores importados mediante un archivo CSV, se desconoce la herramienta de migración predeterminada. Sin embargo, para los servidores de VMware, se recomienda usar la solución Hybrid Cloud Extension (HCX) de VMware.
4. Haga clic en un estado de Preparación para AVS. Puede ver los detalles de la preparación del servidor y explorar en profundidad sus detalles, entre los que se incluye la configuración del proceso, el almacenamiento y la red.

### <a name="review-cost-estimates"></a>Revisar estimaciones de costos

El resumen de evaluación muestra el costo estimado de almacenamiento y proceso que conlleva ejecutar servidores en Azure. 

1. Revise los costos totales mensuales. Los costos se agregan para todos los servidores del grupo evaluado.

    - Las estimaciones de los costos se basan en el número de nodos de AVS necesarios teniendo en cuenta los requisitos de recursos de todos los servidores en total.
    - Como los precios de AVS son por nodo, el costo total no tiene costo de proceso ni distribución de costos de almacenamiento.
    - La estimación de costos es por la ejecución de los servidores en el entorno local en AVS. La evaluación de AVS no tiene en cuenta los costos de PaaS o SaaS.

2. Revise las estimaciones de almacenamiento mensuales. Esta vista muestra los costos de almacenamiento agregados del grupo evaluado, divididos por los diferentes tipos de discos de almacenamiento. 
3. Puede explorar en profundidad para ver los detalles de costo de servidores específicos.

### <a name="review-confidence-rating"></a>Examen de la clasificación de confianza

Server Assessment asigna una clasificación de confianza a las valoraciones basadas en el rendimiento. La clasificación es de una estrella (más baja) a cinco estrellas (más alta).

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
- Configure la asignación de dependencias [sin agente](how-to-create-group-machine-dependencies-agentless.md) o [basada en agente](how-to-create-group-machine-dependencies.md).
