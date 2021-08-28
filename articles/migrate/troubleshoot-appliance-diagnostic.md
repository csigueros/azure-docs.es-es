---
title: Solución de problemas de diagnóstico de dispositivos Azure Migrate
description: Obtenga ayuda para diagnosticar y solucionar los problemas que puedan producirse con el dispositivo Azure Migrate.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 08/11/2021
ms.openlocfilehash: 03be4d6a4b5afb15b5820c1c8b334e3fcb80bbbb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182684"
---
# <a name="diagnose-and-solve-issues-with-azure-migrate-appliance"></a>Diagnóstico y solución de problemas con el dispositivo Azure Migrate

La capacidad **Diagnosticar y solucionar problemas** de un dispositivo Azure Migrate ayuda a los usuarios a identificar y evaluar ellos mismos cualquier problema con la configuración del dispositivo que pudiera estar bloqueando el inicio de la detección, o problemas con una operación de migración en curso, como la detección, la evaluación o la replicación (*en el caso de un dispositivo VMware*) desde el dispositivo. 

Puede ejecutar **Diagnosticar y solucionar problemas** en cualquier momento desde el administrador de configuración del dispositivo para generar un informe de diagnóstico. El informe proporciona información sobre las comprobaciones realizadas, su estado, los problemas identificados y los pasos recomendados para resolver los problemas. 

> [!IMPORTANT]
> La capacidad **Diagnosticar y solucionar problemas** está actualmente en versión preliminar para los dispositivos Azure Migrate.
> Esta versión preliminar está incluida en el soporte al cliente y se puede usar para cargas de trabajo de producción.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="diagnostic-checks"></a>Comprobaciones de diagnóstico

*Diagnosticar y solucionar problemas* ejecuta algunas validaciones previas para ver si los archivos de configuración necesarios están presentes o si están bloqueados por un software antivirus en el dispositivo y luego realiza las siguientes comprobaciones: 

**Categoría** | **Comprobación de diagnóstico** |**Descripción**
--- | --- | --- |
**Comprobaciones de requisitos previos** | Comprobaciones de conectividad | Comprueba si el dispositivo tiene conectividad con Azure directamente o a través de un proxy.
|| Comprobación de sincronización de hora | Comprueba si la hora del servidor del dispositivo está sincronizada con la hora de la red.
|| Comprobación de actualización automática | Comprueba si la actualización automática está habilitada y si todos los agentes que se ejecutan en el dispositivo están actualizados.
||Comprobación de VDDK | Comprueba si los archivos VDDK necesarios se han descargado y copiado en la ubicación necesaria del servidor del dispositivo.
**Comprobaciones de estado del servicio** |Estado operativo |Comprueba si los agentes del dispositivo están en ejecución. <br>*Si no es así, el dispositivo resuelve automáticamente mediante el reinicio de los agentes.* 
||Conectividad del punto de conexión de servicio |Comprueba si los agentes pueden comunicarse con sus respectivos servicios en Azure directamente o a través de un proxy.
**Comprobaciones específicas de Azure** |Disponibilidad de la aplicación de AAD* | Comprueba si la aplicación de AAD creada durante el registro del dispositivo está disponible y es accesible desde este.
||Disponibilidad del proyecto de migración* | Comprueba si el proyecto de migración en el que se ha registrado el dispositivo todavía existe y es accesible desde este.
||Disponibilidad de recursos esenciales*| Comprueba si los recursos de migración creados durante el registro del dispositivo siguen existiendo y son accesibles desde este.
**Comprobaciones específicas del dispositivo** | Disponibilidad de certificado de Key Vault* | Comprueba si el certificado descargado de Key Vault durante el registro del dispositivo sigue estando disponible en el servidor de este. <br> *Si no es así, el dispositivo resuelve automáticamente mediante una nueva descarga del certificado, siempre que Key Vault esté disponible y sea accesible*.
|| Disponibilidad del almacén de credenciales | Comprueba si los recursos del almacén de credenciales del servidor del dispositivo no se han movido, eliminado ni editado.
|| Componentes del dispositivo de replicación/ASR | Comprueba si se ha usado también el mismo servidor para instalar los componentes del dispositivo de replicación/ASR. *Actualmente no se admite la instalación de Azure Migrate y el dispositivo de replicación (para la migración basada en agente) en el mismo servidor.*
|| Disponibilidad de licencia del sistema operativo | Comprueba si la licencia de evaluación del servidor del dispositivo creado a partir de OVA/VHD sigue siendo válida. *La licencia de evaluación de Windows Server 2016 es válida durante 180 días.*
|| Uso de CPU y memoria | Comprueba la CPU y la memoria usadas por los agentes de migración en el servidor del dispositivo.  

**comprobado y notificado solo si el dispositivo ya se ha registrado. Estas comprobaciones se ejecutan en el contexto del usuario de Azure actual que ha iniciado sesión en el dispositivo*.

## <a name="running-diagnostic-checks"></a>Ejecución de comprobaciones de diagnóstico

Si tiene algún problema con el dispositivo durante su configuración o detecta problemas con operaciones de migración en curso, como la detección, la evaluación o la replicación (*en el caso de un dispositivo VMware*) en el portal, puede ir al administrador de configuración del dispositivo y ejecutar diagnósticos.

> [!NOTE]
> Actualmente, **Diagnosticar y solucionar problemas** puede realizar comprobaciones relacionadas con la conectividad del dispositivo con Azure, la disponibilidad de los recursos necesarios en el servidor del dispositivo o Azure. Los problemas de conectividad o detección en el entorno de origen, como vCenter Server/hosts de ESXi/hosts de Hyper-V/máquinas virtuales/servidores físicos, no se contemplan actualmente en **Diagnosticar y solucionar problemas**.
 
1. Seleccione **Diagnosticar y solucionar problemas** en la cinta de opciones de la parte superior del administrador de configuración.

    ![Selección de Diagnosticar y solucionar problemas](./media/troubleshoot-appliance-diagnostic-solve/appliance-configuration-manager-diagnose-solve.png)
    
    Después de seleccionar **Diagnosticar y solucionar problemas**, el dispositivo comienza automáticamente a ejecutar las comprobaciones de diagnóstico. Este paso puede tardar unos cinco minutos en terminar.
    *Si ha ejecutado las comprobaciones con anterioridad, debería ver la marca de tiempo del último informe de diagnóstico.*
     
    ![Informe de diagnóstico](./media/troubleshoot-appliance-diagnostic-solve/diagnostic-report.png)

1. Una vez completadas las comprobaciones de diagnóstico, puede ver el informe en otra pestaña, donde puede optar por guardarlo en formato PDF, o puede ir a esta ubicación: **C:\Users\Public\Desktop\DiagnosticsReport**, en el servidor del dispositivo, donde el informe se guarda automáticamente en formato HTML.

    ![Vista del informe de diagnóstico](./media/troubleshoot-appliance-diagnostic-solve/view-diagnostic-report.png)

1. El informe proporciona información sobre las comprobaciones realizadas, su estado, los problemas identificados y los pasos recomendados para resolver los problemas.

    ![Vista del estado del informe de diagnóstico](./media/troubleshoot-appliance-diagnostic-solve/view-status.png)

1. Puede seguir los pasos de corrección del informe para solucionar un problema. Si no puede resolver el problema, se recomienda adjuntar el informe de diagnóstico al crear un caso de soporte técnico de Microsoft para que ayude a acelerar la resolución.

## <a name="next-steps"></a>Pasos siguientes
Si tiene problemas que no se contemplan en **Diagnosticar y solucionar problemas**, puede ir a [Solución de problemas de dispositivos Azure Migrate](./troubleshoot-appliance.md) para encontrar los pasos de corrección.