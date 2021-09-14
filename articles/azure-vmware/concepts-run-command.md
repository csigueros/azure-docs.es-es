---
title: 'Conceptos: Ejecución de comandos en Azure VMware Solution'
description: Obtenga información sobre cómo usar comandos de ejecución en Azure VMware Solution.
ms.topic: conceptual
ms.date: 08/31/2021
ms.openlocfilehash: 28cbf76dd035ce9b04909a61e3001063aa151162
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123310677"
---
# <a name="run-commands-in-azure-vmware-solution"></a>Ejecución de comandos en Azure VMware Solution

En Azure VMware Solution, se obtiene acceso a vCenter con el rol CloudAdmin. Puede [ver los privilegios concedidos](concepts-identity.md#view-the-vcenter-privileges) al rol CloudAdmin de Azure VMware Solution en su instancia de vCenter de la nube privada de Azure VMware Solution. Los comandos de ejecución son una colección de cmdlets de PowerShell que se ejecutan en determinadas operaciones en vCenter, lo que requiere privilegios elevados. 

Azure VMware Solution admite las siguientes operaciones:

- [Instalación y desinstalación de la solución de recuperación ante desastres de JetStream](deploy-disaster-recovery-using-jetstream.md)

- [Configuración de un origen de identidad externo](configure-identity-source-vcenter.md)

- [Visualización y edición de la directiva de almacenamiento](configure-storage-policy.md) 


>[!NOTE]
>Los comandos de ejecución se ejecutan de uno en uno en el orden enviado.

## <a name="view-the-status-of-an-execution"></a>Visualización del estado de una ejecución

Puede ver el estado de cualquier comando de ejecución ejecutado, incluidos los resultados, los errores, las advertencias y la información.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione **Ejecutar comando** > **Estado de la ejecución del proceso**.

   Puede ordenar por el nombre de ejecución, el nombre del paquete, la versión del paquete, el nombre del comando, la hora de inicio, la hora de finalización y el estado.  

   :::image type="content" source="media/run-command/run-execution-status.png" alt-text="Captura de pantalla que muestra la pestaña Estado de la ejecución del proceso." lightbox="media/run-command/run-execution-status.png":::

1. Seleccione la suscripción que quiere ver.

   :::image type="content" source="media/run-command/run-execution-status-example.png" alt-text="Captura de pantalla que muestra un ejemplo de ejecución.":::

   Puede ver más detalles sobre la ejecución, incluidos los resultados, los errores, las advertencias y la información.

   - **Detalles**: resumen de los detalles de ejecución, como el nombre, el estado, el paquete y el nombre del comando ejecutados. 

   - **Salida**: mensaje al final de la ejecución correcta de un cmdlet. No todos los cmdlets tienen salida.

      :::image type="content" source="media/run-command/run-execution-status-example-output.png" alt-text="Captura de pantalla que muestra la salida de la ejecución de un proceso.":::

   - **Error**: excepción de terminación que detuvo la ejecución de un cmdlet.    

      :::image type="content" source="media/run-command/run-execution-status-example-error.png" alt-text="Captura de pantalla que muestra los errores detectados durante la ejecución de una ejecución.":::

   - **Advertencia**: excepción de no terminación durante la ejecución de un cmdlet. 

      :::image type="content" source="media/run-command/run-execution-status-example-warning.png" alt-text="Captura de pantalla que muestra las advertencias detectadas durante la ejecución de una ejecución.":::

   - **Información**: mensaje de progreso durante la ejecución de un cmdlet. 

      :::image type="content" source="media/run-command/run-execution-status-example-information.png" alt-text="Captura de pantalla que muestra el progreso general en tiempo real del cmdlet mientras se ejecuta.":::



## <a name="cancel-or-delete-a-job"></a>Cancelación o eliminación de un trabajo



### <a name="method-1"></a>Método 1

>[!NOTE]
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

- [Configurar la directiva de almacenamiento](configure-storage-policy.md): a cada máquina virtual implementada en un almacén de datos vSAN se le asigna al menos una directiva de almacenamiento de máquina virtual. Puede asignar una directiva de almacenamiento de máquina virtual en una implementación inicial de una máquina virtual o al realizar otras operaciones de máquina virtual, como la clonación o la migración.

- [Configurar el origen de identidad externo de vCenter](configure-identity-source-vcenter.md): en Azure VMware Solution, vCenter tiene un usuario local integrado denominado cloudadmin que está asignado al rol CloudAdmin. El usuario cloudadmin local se usa para configurar usuarios en Active Directory (AD). Con la característica Ejecutar comando, puede configurar Active Directory a través de LDAP o LDAPS para vCenter como origen de identidad externo.

- [Implementar la recuperación ante desastres mediante JetStream](deploy-disaster-recovery-using-jetstream.md): almacene los datos directamente en un clúster de recuperación en vSAN. Los datos se capturan mediante filtros de E/S que se ejecutan en vSphere. El almacén de datos subyacente puede ser VMFS, VSAN, vVol o cualquier plataforma HCI. 
