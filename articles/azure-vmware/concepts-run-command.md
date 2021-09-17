---
title: 'Conceptos: Ejecución de comandos en Azure VMware Solution (versión preliminar)'
description: Obtenga información sobre cómo usar comandos de ejecución en Azure VMware Solution.
ms.topic: conceptual
ms.date: 08/31/2021
ms.openlocfilehash: 1e2889254c8711c92efdc53ab181e26d3ac06b43
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541646"
---
# <a name="run-command-in-azure-vmware-solution-preview"></a>Ejecución de comandos en Azure VMware Solution (versión preliminar)

En Azure VMware Solution, vCenter tiene un usuario local integrado llamado *cloudadmin* que tiene asignado el rol CloudAdmin. El rol CloudAdmin tiene [privilegios](concepts-identity.md#view-the-vcenter-privileges) de vCenter que son distintos a los de otras soluciones de nube e implementaciones locales de VMware. La característica Ejecutar comando (versión preliminar) permite realizar operaciones que normalmente requerirían privilegios elevados mediante una colección de cmdlets de PowerShell. 

Azure VMware Solution admite las siguientes operaciones:

- [Instalación y desinstalación de la solución de recuperación ante desastres de JetStream](deploy-disaster-recovery-using-jetstream.md)

- [Configuración de un origen de identidad externo](configure-identity-source-vcenter.md)

- [Visualización y establecimiento de directivas de almacenamiento](configure-storage-policy.md) 


>[!NOTE]
>Los comandos de ejecución se ejecutan de uno en uno en el orden enviado.

## <a name="view-the-status-of-an-execution"></a>Visualización del estado de una ejecución

Puede ver el estado de cualquier comando de ejecución que se haya ejecutado, incluidos la salida, los errores, las advertencias y los registros de información de los cmdlets.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione **Ejecutar comando** > **Estado de la ejecución del proceso**.

   Puede seleccionar una de las diversas columnas para ordenar por ella.  

   :::image type="content" source="media/run-command/run-execution-status.png" alt-text="Captura de pantalla que muestra la pestaña Estado de la ejecución del proceso." lightbox="media/run-command/run-execution-status.png":::

1. Seleccione la suscripción que quiere ver. Se abre un panel con detalles sobre la ejecución y otras pestañas para los distintos tipos de salida generados por el cmdlet.

   :::image type="content" source="media/run-command/run-execution-status-example.png" alt-text="Captura de pantalla que muestra un ejemplo de ejecución.":::

   Puede ver más detalles sobre la ejecución, incluidos los resultados, los errores, las advertencias y la información.

   - **Detalles**: resumen de los detalles de ejecución, como el nombre, el estado, el paquete, el nombre del cmdlet y el error, si se produjo alguno en el comando. 

   - **Salida**: mensajes generados por el cmdlet. Puede incluir el progreso o el resultado de la operación. No todos los cmdlets tienen salida.

      :::image type="content" source="media/run-command/run-execution-status-example-output.png" alt-text="Captura de pantalla que muestra la salida de la ejecución de un proceso.":::

   - **Error**: mensajes de error generados en la ejecución del cmdlet. Esto se suma al mensaje de error de terminación en el panel de detalles.    

      :::image type="content" source="media/run-command/run-execution-status-example-error.png" alt-text="Captura de pantalla que muestra los errores detectados durante la ejecución de una ejecución.":::

   - **Advertencia**: mensajes de advertencia generados durante la ejecución. 

      :::image type="content" source="media/run-command/run-execution-status-example-warning.png" alt-text="Captura de pantalla que muestra las advertencias detectadas durante la ejecución de una ejecución.":::

   - **Información**: mensajes de progreso y diagnóstico generados durante la ejecución de un cmdlet. 

      :::image type="content" source="media/run-command/run-execution-status-example-information.png" alt-text="Captura de pantalla que muestra el progreso general en tiempo real del cmdlet mientras se ejecuta.":::



## <a name="cancel-or-delete-a-job"></a>Cancelación o eliminación de un trabajo



### <a name="method-1"></a>Método 1

Este método intenta cancelar la ejecución y, a continuación, la elimina al finalizar.

>[!IMPORTANT]
>El método 1 es irreversible.

1. Seleccione **Ejecutar comando** > **Estado de ejecución de la tarea** y, a continuación, seleccione el trabajo que desea cancelar.

   :::image type="content" source="media/run-command/run-execution-cancel-delete-job-method-1.png" alt-text="Captura de pantalla que muestra cómo cancelar y eliminar un comando de ejecución.":::

2. Seleccione **Sí** para cancelar y quitar el trabajo de todos los usuarios.



### <a name="method-2"></a>Método 2

1. Seleccione **Ejecutar comando** > **Paquetes** > **Estado de la ejecución del proceso**.

2. Seleccione **Más** (...) en la lista el trabajo que desea cancelar.

   :::image type="content" source="media/run-command/run-execution-cancel-delete-job-method-2.png" alt-text="Captura de pantalla que muestra cómo cancelar y eliminar un comando de ejecución mediante los puntos suspensivos.":::

3. Seleccione **Sí** para cancelar y quitar el trabajo de todos los usuarios.



## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido sobre los conceptos del comando Ejecutar, puede usar la característica Ejecutar comando para:

- [Configurar la directiva de almacenamiento](configure-storage-policy.md): a cada máquina virtual implementada en un almacén de datos vSAN se le asigna una directiva de almacenamiento de vSAN. Puede asignar una directiva de almacenamiento de vSAN en una implementación inicial de una máquina o al realizar otras operaciones con la máquina virtual, como la clonación o la migración.

- [Configurar el origen de identidad externo para vCenter (comando Ejecutar)](configure-identity-source-vcenter.md): configure Active Directory sobre LDAP o LDAPS para vCenter, lo que permite el uso de un origen de identidad externo como Active Directory. A continuación, puede agregar grupos desde el origen de identidad externo al rol CloudAdmin.

- [Implementar la recuperación ante desastres mediante JetStream](deploy-disaster-recovery-using-jetstream.md): almacene los datos directamente en un clúster de recuperación en vSAN. Los datos se capturan mediante filtros de E/S que se ejecutan en vSphere. El almacén de datos subyacente puede ser VMFS, VSAN, vVol o cualquier plataforma HCI. 
