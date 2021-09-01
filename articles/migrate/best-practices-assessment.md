---
title: Procedimiento recomendado de evaluaciones en la herramienta de detección y evaluación de Azure Migrate
description: Sugerencias para crear evaluaciones con la herramienta de detección y evaluación de Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/28/2021
ms.openlocfilehash: cf8f4adabf07ec349c2b5890d0f3a6cfd6bbb192
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732659"
---
# <a name="best-practices-for-creating-assessments"></a>Procedimientos recomendados para crear evaluaciones

[Azure Migrate](./migrate-services-overview.md) proporciona un centro de herramientas que le ayuda a detecta las aplicaciones, la infraestructura y las cargas de trabajo, a evaluarlas y a migrarlas a Microsoft Azure. Este centro incluye herramientas de Azure Migrate y ofertas de fabricantes de software independientes (ISV) de terceros.

En este artículo se resumen los procedimientos recomendados para crear evaluaciones mediante la herramienta de detección y evaluación de Azure Migrate.

Las evaluaciones que se crean con la herramienta de Azure Migrate: Detección y evaluación son una instantánea puntual de los datos. Existen cuatro tipos de evaluaciones que puede crear con la herramienta de detección y evaluación de Azure Migrate:

**Tipo de evaluación** | **Detalles**
--- | ---
**MV de Azure** | Evaluaciones para la migración de los servidores locales a máquinas virtuales de Azure. <br/><br/> Con este tipo de evaluación, puede evaluar los servidores locales en los entornos de [VMware](how-to-set-up-appliance-vmware.md) e [Hyper-V](how-to-set-up-appliance-hyper-v.md) y los [servidores físicos](how-to-set-up-appliance-physical.md) para la migración a Azure. [Más información](concepts-assessment-calculation.md)
**SQL de Azure** | Evaluaciones para migrar las instancias locales de SQL Server desde su entorno de VMware a Azure SQL Database o a Azure SQL Managed Instance. [Más información](concepts-azure-sql-assessment-calculation.md)
**Azure App Service** | Evaluaciones para migrar las aplicaciones web de ASP.NET locales, que se ejecutan en un servidor web IIS, desde el entorno de VMware a Azure App Service. [Más información](concepts-azure-webapps-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Evaluaciones para la migración de los servidores locales a [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Puede evaluar las [máquinas virtuales de VMware](how-to-set-up-appliance-vmware.md) locales para la migración a Azure VMware Solution (AVS) con este tipo de evaluación. [Más información](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Si el número de evaluaciones de la máquina virtual de Azure o de AVS no es correcto en la herramienta de detección y evaluación, haga clic en el número total de evaluaciones para desplazarse a todas ellas y volver a calcularlas. La herramienta de detección y evaluación mostrará el recuento correcto de ese tipo de evaluación. 

### <a name="sizing-criteria"></a>Criterios de dimensionamiento
Opciones de criterios de dimensionamiento en evaluaciones de Azure Migrate:

**Criterio de tamaño** | **Detalles** | **Data**
--- | --- | ---
**Basada en el rendimiento** | Evaluaciones que realizan recomendaciones basadas en datos de rendimiento recopilados | **Evaluación de máquinas virtuales de Azure**: La recomendación del tamaño de VM se basa en los datos de uso de la CPU y la memoria.<br/><br/> La recomendación de tipo de disco (HDD/SSD estándar, discos administrados prémium o Ultra) se basa en la IOPS y el rendimiento de los discos locales.<br/><br/>**Evaluación de Azure SQL**: La configuración de Azure SQL se basa en los datos de rendimiento de las instancias y bases de datos de SQL, lo que incluye el uso de CPU, el uso de memoria, la IOPS (archivos de datos y de registro), el rendimiento y la latencia de las operaciones de E/S.<br/><br/>**Evaluación de Azure VMware Solution (AVS)** : La recomendación de los nodos de AVS se basa en los datos de uso de la CPU y la memoria.
**Tal cual en el entorno local** | Evaluaciones que no usan datos de rendimiento para hacer recomendaciones. | **Evaluación de máquinas virtuales de Azure**: La recomendación de tamaño de la máquina virtual se basa en el tamaño de la máquina virtual local.<br/><br> El tipo de disco recomendado se basa en lo que selecciona en la configuración de tipo de almacenamiento para la evaluación.<br/><br/> **Evaluación de Azure App Service**: La recomendación de evaluación se basa en los datos de configuración de aplicaciones web locales.<br/><br/> **Evaluación de Azure VMware Solution (AVS)** : La recomendación de los nodos de AVS se basa en el tamaño de la máquina virtual local.

#### <a name="example"></a>Ejemplo
Como ejemplo, si tiene una máquina virtual local con cuatro núcleos con un uso del 20 % y una memoria de 8 GB con un uso del 10 %, la evaluación de las máquinas virtuales de Azure será la siguiente:

- **Evaluación basada en el rendimiento**:
    - Identifica la memoria y los núcleos efectivos según el uso del núcleo (4 x 0,20 = 0,8) y la memoria (8 GB x 0,10 = 0,8).
    - Aplica el factor de confort especificado en las propiedades de evaluación (por ejemplo, 1,3 x) para obtener los valores que se van a usar para el ajuste de tamaño. 
    - Recomienda el tamaño de máquina virtual más cercano en Azure que puede admitir ~1,04 núcleos (0,8 x 1,3) y una memoria de ~1,04 GB (0,8 x 1,3).

- **Evaluación tal cual (como en el entorno local)** :
    -  Recomienda una máquina virtual con cuatro núcleos; 8 GB de memoria.


## <a name="best-practices-for-creating-assessments"></a>Procedimientos recomendados para crear evaluaciones

El dispositivo Azure Migrate genera perfiles continuamente de su entorno local y envía metadatos y datos de rendimiento a Azure. Siga estos procedimiento recomendado para evaluar los servidores detectados mediante un dispositivo:

- **Crear evaluaciones tal cual**: Puede crear evaluaciones tal cual inmediatamente después de que se muestren los servidores en el portal de Azure Migrate. Puede crear una evaluación de Azure SQL con criterios relacionados con el dimensionamiento, como el criterio basado en el entorno local. La evaluación de Azure App Service de manera predeterminado es "Como local".
- **Crear una evaluación basada en el rendimiento**: después de configurar la detección, le recomendamos esperar al menos un día antes de ejecutar una evaluación basada en el rendimiento:
    - La recopilación de datos de rendimiento lleva tiempo. El hecho de esperar al menos un día garantiza que haya suficientes puntos de datos de rendimiento antes de ejecutar la evaluación.
    - Cuando ejecuta evaluaciones basadas en el rendimiento, asegúrese de generar un perfil del entorno para la duración de la evaluación. Por ejemplo, si crea una evaluación con la duración de rendimiento establecida en una semana, debe esperar al menos una semana después de iniciar la detección para que se recopilen todos los puntos de datos. Si no lo hace, la evaluación no obtendrá una clasificación de cinco estrellas.
- **Recalcular evaluaciones**: dado que las evaluaciones son instantáneas de un momento dado, no se actualizan automáticamente con los datos más recientes. Para actualizar una evaluación con los datos más recientes, debe recalcularla.

Siga estos procedimientos recomendados para evaluar los servidores importados en Azure Migrate mediante un archivo .CSV:

- **Crear evaluaciones tal cual**: Puede crear evaluaciones tal cual inmediatamente después de que se muestren los servidores en el portal de Azure Migrate.
- **Crear una evaluación basada en el rendimiento**: ayuda a obtener una mejor estimación de los costos, en especial si se ha aprovisionado en exceso la capacidad del servidor en el entorno local. Sin embargo, la precisión de la evaluación basada en el rendimiento depende de los datos de rendimiento que se especifiquen para los servidores. 
- **Recalcular evaluaciones**: dado que las evaluaciones son instantáneas de un momento dado, no se actualizan automáticamente con los datos más recientes. Para actualizar una evaluación con los datos importados más recientes, debe volver a calcularla.
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>Parámetros de dimensionamiento de FTT para las evaluaciones de AVS

El motor de almacenamiento que se usa en AVS es vSAN. Las directivas de almacenamiento de vSAN definen los requisitos de almacenamiento de las máquinas virtuales. Estas directivas garantizan el nivel de servicio requerido para las máquinas virtuales, ya que determinan cómo se asigna el almacenamiento a la máquina virtual. Estas son las combinaciones FTT-RAID disponibles: 

**Errores tolerables (FTT)** | **Configuración de RAID** | **Hosts mínimos requeridos** | **Consideración de dimensionamiento**
--- | --- | --- | ---
1 | RAID-1 (Creación de reflejo) | 3 | Una máquina virtual de 100 GB consumiría 200 GB.
1 | RAID-5 (codificación de borrado) | 4 | Una máquina virtual de 100 GB consumiría 133,33 GB.
2 | RAID-1 (Creación de reflejo) | 5 | Una máquina virtual de 100 GB consumiría 300 GB.
2 | RAID-6 (codificación de borrado) | 6 | Una máquina virtual de 100 GB consumiría 150 GB.
3 | RAID-1 (Creación de reflejo) | 7 | Una máquina virtual de 100 GB consumirá 400 GB.

## <a name="best-practices-for-confidence-ratings"></a>Procedimientos recomendados para las clasificaciones de confianza

Cuando realiza evaluaciones basadas en el rendimiento, se asigna a la evaluación una clasificación de confianza que puede oscilar entre 1 estrella (la más baja) y 5 estrellas (la más alta). Para utilizar de manera eficiente las clasificaciones de confianza:

- Las evaluaciones de las máquinas virtuales de Azure y AVS necesitan:
    - Los datos de uso de CPU y memoria para cada uno de los servidores
    - Los datos de rendimiento o IOPS de lectura o escritura para cada disco asociado al servidor local
    - Los datos de entrada o salida de red para cada adaptador de red asociado al servidor.
     
- Las evaluaciones de SQL de Azure necesitan los datos de rendimiento de las instancias y bases de datos de SQL que se van a evaluar, entre las que se incluyen:
    - Datos de uso de la CPU y de la memoria
    - Datos de IOPS o rendimiento de lectura o escritura de los archivos de datos y de registro
    - Latencia de operaciones de E/S

La clasificación de confianza de una evaluación se proporciona en función del porcentaje de puntos de datos disponibles para la duración seleccionada, tal y como se resume en la tabla siguiente:

   **Disponibilidad del punto de datos** | **Clasificación de confianza**
   --- | ---
   0 % - 20 % | 1 estrella
   21 % - 40 % | 2 estrellas
   41 % - 60 % | 3 estrellas
   61 % - 80 % | 4 estrellas
   81 % - 100 % | 5 estrellas

## <a name="common-assessment-issues"></a>Problemas de evaluación comunes

A continuación, se indica cómo se deben abordar algunos problemas comunes del entorno que afectan a las evaluaciones.

###  <a name="out-of-sync-assessments"></a>Evaluaciones sin sincronizar

Si agrega o quita servidores de un grupo después de crear una evaluación, la evaluación que ha creado se marcará **sin sincronizar**. Vuelva a ejecutar la evaluación **(Recalcular**) para reflejar los cambios en el grupo.

### <a name="outdated-assessments"></a>Evaluaciones obsoletas

#### <a name="azure-vm-assessment-and-avs-assessment"></a>Evaluación de las máquinas virtuales de Azure y AVS

Si hay cambios en el entorno local de los servidores que se encuentran en un grupo que se ha evaluado, la evaluación se marca como **obsoleta**. Una valoración se puede marcar como "obsoleta" debido a uno o varios cambios en las siguientes propiedades:

- Número de núcleos de procesador
- Memoria asignada
- Tipo de arranque o firmware
- Nombre, versión y arquitectura del sistema operativo
- Número de discos
- Número de adaptadores de red
- Cambio de tamaño de disco (GB asignados)
- Actualización de las propiedades de NIC. Ejemplo: Cambios de dirección Mac, adición de direcciones IP, etc.

Vuelva a ejecutar la valoración (**Recalcular**) para reflejar los cambios en el grupo.

#### <a name="azure-sql-assessment"></a>Evaluación de Azure SQL

Si hay cambios en las instancias de SQL del entorno local que se encuentran en un grupo que se ha evaluado, la evaluación se marca como **obsoleta**. Una valoración se puede marcar como "obsoleta" debido a uno o varios de los siguientes motivos:

- Se ha agregado o eliminado una instancia de SQL de un servidor
- Se ha agregado o eliminado una base de datos SQL de una instancia de SQL
- El tamaño total de la base de datos de una instancia de SQL ha cambiado en más del 20 %
- Cambio en el número de núcleos de procesador
- Cambio en la memoria asignada
  
    Vuelva a ejecutar la valoración (**Recalcular**) para reflejar los cambios en el grupo.

#### <a name="azure-app-service-assessment"></a>Evaluación de Azure App Service

Si hay cambios en las aplicaciones web locales que se encuentran en un grupo que se ha evaluado, la evaluación se marca como **obsoleta**. Una valoración se puede marcar como "obsoleta" debido a uno o varios de los siguientes motivos:

- Se agregaron o quitaron aplicaciones web de un servidor.
- Se realizaron cambios de configuración en las aplicaciones web existentes.
  
    Vuelva a ejecutar la valoración (**Recalcular**) para reflejar los cambios en el grupo.

### <a name="low-confidence-rating"></a>Clasificación de confianza baja

Puede que una evaluación no tenga todos los puntos de datos debido a muchos motivos:

- No generó un perfil de su entorno durante el tiempo que está creando la evaluación. Por ejemplo, si está creando una evaluación con la duración de rendimiento establecida en una semana, debe esperar al menos una semana después de iniciar la detección para que se recopilen todos los puntos de datos. Si no puede esperar a la duración, cambie la duración del rendimiento a un período más pequeño y "recalcule" la evaluación.
 
- La evaluación no puede recopilar los datos de rendimiento de algunos o de todos los servidores en el período de evaluación. Para obtener una clasificación de confianza alta, asegúrese de que: 
    - Los servidores están encendidos durante la evaluación.
    - Se permiten las conexiones salientes en los puertos 443.
    - La memoria dinámica está habilitada en los servidores de Hyper-V. 
    - Si el estado de la conexión de los agentes en Azure Migrate es "Conectado" y compruebe el último latido.
    - Para las evaluaciones de Azure SQL, el estado de conexión de Azure Migrate para todas las instancias de SQL es "Conectado" en la hoja de la instancia de SQL detectada.

    "Recalcule" la evaluación para reflejar los cambios más recientes en la clasificación de confianza.

- En el caso de las evaluaciones de máquinas virtuales de Azure y AVS, se crearon pocos servidores después de iniciar la detección. Por ejemplo, si va a crear una evaluación para el historial de rendimiento del último mes, pero se crearon algunos servidores en el entorno hace solo una semana. En este caso, los datos de rendimiento de los nuevos servidores no estarán disponibles para todo el período y la clasificación de confianza será baja.

- En el caso de las evaluaciones de Azure SQL, se crearon algunas instancias de SQL o bases de datos después de que se iniciara la detección. Por ejemplo, si va a crear una evaluación para el historial de rendimiento del último mes, pero se crearon algunas instancias o bases de datos de SQL en el entorno hace solo una semana. En este caso, los datos de rendimiento de los nuevos servidores no estarán disponibles para todo el período y la clasificación de confianza será baja.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Guía de la herramienta de migración para valoraciones de AVS

En el informe preparación de Azure para la valoración de Azure VMware Solution (AVS), puede ver las siguientes herramientas sugeridas: 
- **VMware HCX o Enterprise**: En el caso de los servidores de VMware, la solución Hybrid Cloud Extension (HCX) de VMware es la herramienta de migración sugerida para migrar la carga de trabajo local a la nube privada de Azure VMware Solution (AVS). [Más información](../azure-vmware/install-vmware-hcx.md).
- **Desconocido**: en el caso de los servidores importados mediante un archivo CSV, se desconoce la herramienta de migración predeterminada. Sin embargo, para los servidores del entorno de VMware, se recomienda usar la solución Hybrid Cloud Extension (HCX) de VMware.


## <a name="next-steps"></a>Pasos siguientes

- [Averigüe](concepts-assessment-calculation.md) cómo se calculan las evaluaciones.
- [Averigüe](how-to-modify-assessment.md) cómo puede personalizar una evaluación.
