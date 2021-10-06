---
title: Migración de trabajos locales de SQL Server Integration Services (SSIS) a Azure Data Factory
description: En este artículo se describe cómo migrar trabajos de SQL Server Integration Services (SSIS) a canalizaciones, actividades o desencadenadores de Azure Data Factory mediante SQL Server Management Studio.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: b1e7f42274b8402b55d358e10cde30f93520dd72
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124824695"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>Migración de trabajos de Agente SQL Server a ADF con SSMS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Al [migrar cargas de trabajo locales de SQL Server Integration Services (SSIS) a SSIS en ADF](scenario-ssis-migration-overview.md), después de migrar los paquetes SSIS, puede realizar la migración por lotes de trabajos del Agente SQL Server con el tipo de paso de trabajo del paquete SQL Server Integration Services a canalizaciones, actividades o desencadenadores de Azure Data Factory (ADF) mediante el **Asistente para migración de trabajos de SSIS** de SQL Server Management Studio (SSMS).

En general, para los trabajos del Agente SQL seleccionados con los tipos de pasos de trabajo aplicables, el **Asistente para migración de trabajos de SSIS** puede:

- asignar la ubicación del paquete SSIS local a donde se van a migrar los paquetes, a la que puede acceder mediante SSIS en ADF.
    > [!NOTE]
    > Solo se admite la ubicación del paquete del sistema de archivos.
- migrar los trabajos aplicables con los pasos de trabajo aplicables a los recursos de ADF correspondientes, como se indica a continuación:

|Objeto de trabajo del Agente SQL  |Recurso de ADF  |Notas|
|---------|---------|---------|
|Trabajo del Agente SQL|pipeline     |El nombre de la canalización se *generará para \<job name>* . <br> <br> Los trabajos del agente integrados no son aplicables: <li> Trabajo de mantenimiento del servidor de SSIS <li> syspolicy_purge_history <li> collection_set_* <li> mdw_purge_data_* <li> sysutility_*|
|Paso de trabajo de SSIS|Ejecución de una actividad del paquete de SSIS|<li> El nombre de la actividad será \<step name>. <li> La cuenta de proxy usada en el paso de trabajo se migrará como autenticación de Windows de esta actividad. <li> Las *opciones de ejecución* excepto *Use 32-bit runtime* (Usar entono de ejecución de 32 bits) definidas en el paso de trabajo se omitirán en la migración. <li> La *comprobación* definida en el paso de trabajo se omitirá en la migración.|
|schedule      |Programación de un desencadenador        |El nombre del desencadenador de programación se *generará para \<schedule name>* . <br> <br> Las siguientes opciones de programación de trabajos del Agente SQL se omitirán en la migración: <li> Intervalo de segundo nivel. <li> *Iniciar automáticamente al iniciar el Agente SQL Server.* <li> *Iniciar al quedar inactivas las CPU* <li> *día laborable* y *fin de semana* &gt;zona horaria&lt; <br> A continuación, se muestran las diferencias después de migrar la programación de trabajos del Agente SQL al desencadenador de programación de ADF: <li> La ejecución posterior del desencadenador de programación de ADF es independiente del estado de la ejecución desencadenada previa. <li> La configuración de periodicidad del desencadenador de programación de ADF difiere de la frecuencia diaria del trabajo del Agente SQL.|

- generar plantillas de Azure Resource Manager (ARM) en la carpeta de salida local e implementarla directamente en la factoría de datos o más tarde manualmente. Para más información sobre las plantillas de Azure Resource Manager, consulte [Tipos de recursos de Microsoft.DataFactory](/azure/templates/microsoft.datafactory/allversions).

## <a name="prerequisites"></a>Requisitos previos

La característica descrita en este artículo requiere SQL Server Management Studio, versión 18.5 o posterior. Para obtener la versión más reciente de SSMS, vea [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) [Descargar SQL Server Management Studio (SSMS)].

## <a name="migrate-ssis-jobs-to-adf"></a>Migración de trabajos de SSIS a ADF

1. En SSMS, en el Explorador de objetos, seleccione Agente SQL Server, elija los trabajos, haga clic con el botón derecho y seleccione **Migrate SSIS Jobs to ADF** (Migración de trabajos de SSIS a ADF).
:::image type="content" source="media/how-to-migrate-ssis-job-ssms/menu.png" alt-text="Instantánea del Explorador de objetos de SQL Server Management Studio, donde se pueden seleccionar trabajos y migrar trabajos de SSIS a ADF.":::

1. Inicie sesión en Azure, seleccione la suscripción de Azure, Data Factory e Integration Runtime. Azure Storage es opcional, se usa en el paso de asignación de la ubicación del paquete si los trabajos de SSIS que se van a migrar tienen paquetes del sistema de archivos de SSIS.
:::image type="content" source="media/how-to-migrate-ssis-job-ssms/step1.png" alt-text="menú":::

1. Asigne las rutas de los paquetes SSIS y los archivos de configuración de trabajos de SSIS a las rutas de destino a las que pueden acceder las canalizaciones migradas. En este paso de asignación, puede:

    1. Seleccionar una carpeta de origen y, luego, **Agregar asignación**.
    1. Actualizar la ruta a la carpeta de origen. Las rutas de acceso válidas son las rutas de acceso de carpeta o las rutas de acceso de carpeta primaria de paquetes.
    1. Actualizar la ruta de acceso a la carpeta de destino. El valor predeterminado es la ruta de acceso relativa a la cuenta de almacenamiento predeterminada, que se selecciona en el paso 1.
    1. Eliminar una asignación seleccionada mediante **Delete Mapping** (Eliminar asignación).
:::image type="content" source="media/how-to-migrate-ssis-job-ssms/step2.png" alt-text="Captura de pantalla en la que se muestra la página Map SSIS Package and Configuration Paths (Asignar las rutas de los paquetes SSIS y la configuración), donde se pueden agregar asignaciones.":::
:::image type="content" source="media/how-to-migrate-ssis-job-ssms/step2-1.png" alt-text="Captura de pantalla en la que se muestra la página Map SSIS Package and Configuration Paths (Asignar las rutas de los paquetes SSIS y la configuración), donde se pueden actualizar las rutas de las carpetas de destino y origen.":::

1. Seleccione los trabajos aplicables que se van a migrar y configure la opción *Ejecutar una actividad de paquete SSIS*.

    - La *configuración predeterminada* se aplica de forma predeterminada a todos los pasos seleccionados. Para más información de cada propiedad, consulte la *pestaña Configuración* de [Ejecutar una actividad de paquete SSIS](how-to-invoke-ssis-package-ssis-activity.md) cuando la ubicación del paquete es *File System (Package)* (Sistema de archivos [paquete]).
    :::image type="content" source="media/how-to-migrate-ssis-job-ssms/step3-1.png" alt-text="Instantánea de la página Select SSIS Jobs (Seleccionar trabajos de SSIS), donde se puede configurar la actividad del paquete de SSIS ejecutado correspondiente.":::
    - *Step Setting* (Configuración de paso): realice la configuración de un paso seleccionado.
        
        **Apply Default Setting** (Aplicación opción predeterminada): se selecciona el valor predeterminado. Desactive esta opción para establecer la configuración solo para el paso seleccionado.  
        Para más información sobre cada propiedad, consulte la *pestaña Configuración* de la opción [Ejecutar una actividad de paquete SSIS](how-to-invoke-ssis-package-ssis-activity.md) cuando la ubicación del paquete es *File System (Package)* (Sistema de archivos [paquete]).
    :::image type="content" source="media/how-to-migrate-ssis-job-ssms/step3-2.png" alt-text="Instantánea de la página Select SSIS Jobs (Seleccionar trabajos de SSIS), donde se puede aplicar la configuración predeterminada.":::

1. Genere e implemente una plantilla de ARM.
    1. Seleccione o especifique la ruta de acceso de salida de las plantillas de ARM de las canalizaciones de ADF migradas. La carpeta se creará automáticamente si no existe.
    2. Seleccione la opción **Deploy ARM templates to your data factory** (Implementar plantillas de ARM en la factoría de datos):
        - la opción predeterminada está deseleccionada. Puede implementar las plantillas de Resource Manager generadas más adelante manualmente.
        - Seleccione la implementación de plantillas de Resource Manager en la factoría de datos directamente.
    :::image type="content" source="media/how-to-migrate-ssis-job-ssms/step4.png" alt-text="Instantánea de la página Configure Migration (Configurar migración), donde se puede seleccionar o especificar la ruta de salida de las plantillas de Resource Manager para las canalizaciones de ADF migradas y seleccionar la opción Deploy ARM templates to your data factory (Implementar plantillas de Resource Manager en la factoría de datos).":::

1. Migre y, luego, compruebe los resultados.
:::image type="content" source="media/how-to-migrate-ssis-job-ssms/step5.png" alt-text="Instantánea de la página Migration Result (Resultado de la migración), donde se muestra el progreso de la migración.":::

## <a name="next-steps"></a>Pasos siguientes

[Ejecución y supervisión de la canalización](how-to-invoke-ssis-package-ssis-activity.md)
