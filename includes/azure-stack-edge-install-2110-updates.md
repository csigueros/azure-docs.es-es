---
author: alkohli
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 09/27/2021
ms.openlocfilehash: 24cc360da36e49726cc23b407ada5a3d88c3a2a1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083081"
---
1. Cuando las actualizaciones estén disponibles para el dispositivo, verá una notificación en la página **Información general** del recurso Azure Stack Edge. Seleccione la notificación o, en la barra de comandos superior, seleccione **Actualizar dispositivo**. Esto le permitirá aplicar las actualizaciones de software del dispositivo.

    ![Selección del dispositivo de actualización](media/azure-stack-edge-install-2110-updates/install-updates-portal-1.png)

2. En la hoja **Actualizaciones del dispositivo**, compruebe que ha revisado los términos de licencia asociados a las nuevas características en las notas de la versión.

    Una vez que las actualizaciones se han descargado en el dispositivo, puede elegir **Instalar automáticamente** las actualizaciones. 

    ![Seleccione la opción Instalar actualizaciones automáticamente.](media/azure-stack-edge-install-2110-updates/install-updates-portal-2.png)    

    También puede descargar las actualizaciones y, a continuación,**instalar manualmente las actualizaciones más adelante**.

    ![Seleccione la opción Instalar actualizaciones manualmente más adelante.](media/azure-stack-edge-install-2110-updates/install-updates-portal-3.png)

3. Se inicia la descarga de actualizaciones. Verá una notificación de que la descarga está en curso.

    ![Notificación que muestra la descarga de actualizaciones en curso](media/azure-stack-edge-install-2110-updates/install-updates-portal-4.png)

    También se muestra un banner de notificación en Azure Portal. Indica el progreso de la descarga. Puede seleccionar esta notificación o seleccionar **Actualizar dispositivo** para ver el estado detallado de la actualización.

    ![Ver el estado de actualización detallado en la hoja Actualizaciones del dispositivo](media/azure-stack-edge-install-2110-updates/install-updates-portal-5.png)


4. Una vez completada la descarga, el banner de notificación se actualiza para indicar la finalización. Si decide instalar automáticamente las actualizaciones, la instalación comenzará automáticamente.

    Si decide instalar las actualizaciones manualmente más adelante, seleccione la notificación para abrir la hoja **Actualizaciones del dispositivo**. Seleccione **Instalar actualización**.
 
    ![Seleccione Instalar actualización después de descargar las actualizaciones.](media/azure-stack-edge-install-2110-updates/install-updates-portal-6.png)
 
5. Verá una notificación de que la instalación está en curso. El portal también muestra una alerta informativa para indicar que la instalación está en curso. El dispositivo se queda sin conexión y pasa a modo de mantenimiento.
   
    ![Notificación de banner que muestra que el dispositivo está bajo mantenimiento ](media/azure-stack-edge-install-2110-updates/install-updates-portal-7.png)

6. Dado que se trata de un dispositivo de un nodo, el dispositivo se reiniciará una vez instaladas las actualizaciones. 

    ![Notificación de banner que muestra que el dispositivo se está reiniciando.](media/azure-stack-edge-install-2110-updates/install-updates-portal-8.png)

7. Después del reinicio, el software del dispositivo terminará de actualizarse. La actualización de software de Kubernetes se iniciará automáticamente. El dispositivo se queda sin conexión y pasa a modo de mantenimiento.

    ![Notificación de banner que muestra que el dispositivo está en modo mantenimiento.](media/azure-stack-edge-install-2110-updates/install-updates-portal-9.png)   


8. Una vez que las actualizaciones de software y Kubernetes del dispositivo se instalen correctamente, la notificación del banner desaparece. El estado del dispositivo se actualiza y muestra **Su dispositivo está en línea.** 

    ![La actualización se ha completado y el dispositivo está en línea](media/azure-stack-edge-install-2110-updates/install-updates-portal-10.png)

    Vaya a la interfaz de usuario web local y, a continuación, vaya a la página **Actualización de software**. Compruebe que el software del dispositivo y los Kubernetes se han actualizado correctamente y que la versión de software lo refleja.

    ![Visualización de la versión de software actualizada en la interfaz de usuario local](media/azure-stack-edge-install-2110-updates/install-updates-portal-11.png)


