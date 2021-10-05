---
title: Configuración de copias de seguridad basadas en directivas para Azure NetApp Files | Microsoft Docs
description: Describe cómo configurar las copias de seguridad basadas en directivas (programadas) para los volúmenes de Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/27/2021
ms.author: b-juche
ms.openlocfilehash: acdb84621af21cc29feb3f750a5ea60cb21e3d4a
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094877"
---
# <a name="configure-policy-based-backups-for-azure-netapp-files"></a>Configuración de copias de seguridad basadas en directivas para Azure NetApp Files 

La copia de seguridad de Azure NetApp Files admite tanto copias de seguridad *basadas en directivas* (programadas) como copias de seguridad *manuales* (a petición) en el nivel de volumen. Puede usar ambos tipos de copias de seguridad en el mismo volumen. Para poder hacer copias de seguridad manuales o basadas en directivas, deberá habilitar antes la característica de copia de seguridad para un volumen de Azure NetApp Files durante el proceso de configuración. 

En este artículo se muestra cómo configurar las copias de seguridad basadas en directivas.  Para ver la configuración de copia de seguridad manual, consulte [Configuración de copias de seguridad manuales](backup-configure-manual.md).  

> [!IMPORTANT]
> La característica de copia de seguridad de Azure NetApp Files se encuentra actualmente en versión preliminar. Para acceder a la característica, debe enviar una solicitud de lista de espera a través de la página de **[versión preliminar pública de copias de seguridad de Azure NetApp Files](https://aka.ms/anfbackuppreviewsignup)** . Espere a recibir el correo electrónico de confirmación oficial del equipo de Azure NetApp Files antes de utilizar la característica de copia de seguridad de dicho servicio.

## <a name="about-policy-based-backups"></a>Acerca de las copias de seguridad basadas en directivas  

Un volumen debe tener los dos tipos de directivas siguientes para que la característica de copia de seguridad basada en directivas funcione:  

* Una *directiva de instantáneas* que controle cuándo se crean instantáneas para el volumen.
* Una *directiva de copia de seguridad* que controle qué instantáneas se usarán para realizar copias de seguridad en el almacenamiento de Azure.

Una directiva de instantáneas controla la creación de instantáneas en el volumen. La funcionalidad de copia de seguridad la usa para realizar copias de seguridad de las instantáneas en el almacenamiento de Azure.   

Las copias de seguridad son operaciones de larga duración. El sistema programa las copias de seguridad en función de la carga de trabajo principal (a la que se le asigna una prioridad más alta) y las ejecuta en segundo plano. Una copia de seguridad puede ejecutarse en segundo plano durante horas, según el tamaño del volumen del que se realiza dicha copia. No hay ninguna opción para seleccionar la hora de inicio de las copias de seguridad. El servicio realiza las copias de seguridad en función de la lógica de optimización y programación interna. 

Al asignar una directiva, se crea una instantánea de línea base que refleja el estado actual del volumen y se transfiere la instantánea al almacenamiento de Azure. La instantánea de línea base se crea con un nombre que empieza por `snapmirror`. Esta instantánea de línea base se eliminará automáticamente cuando se complete la primera copia de seguridad programada (según la directiva). Si la directiva de copia de seguridad está asociada a un volumen, la lista de copias de seguridad estará vacía hasta que se transfiera la instantánea de línea base. Una vez completada la copia de seguridad, la entrada de la copia de seguridad de línea base aparecerá en la lista de copias de seguridad del volumen. Después de la transferencia de línea base, la lista se actualizará a diario en función de la directiva. Una lista de copias de seguridad vacía indica que la copia de seguridad de línea base está en curso. Si un volumen ya tiene copias de seguridad manuales antes de asignarle una directiva de copia de seguridad, la instantánea de línea base no se crea. Solo se crea una instantánea de línea base cuando el volumen no tiene copias de seguridad anteriores.

## <a name="configure-and-apply-a-snapshot-policy"></a>Configuración y aplicación de una directiva de instantáneas  

Debe crear una directiva de instantáneas y asociarla al volumen del que quiere realizar las copias de seguridad. Una sola directiva de instantáneas puede asociarse a varios volúmenes. Los cambios en la directiva de instantáneas pueden afectar a la funcionalidad de copia de seguridad de un volumen. 

1. Inicie sesión en Azure Portal y navegue a **Azure NetApp Files**.    
2. Seleccione su suscripción a Azure NetApp Files.   
3. Seleccione **Directiva de instantánea**.   

    ![Captura de pantalla que muestra cómo navegar a la opción Directiva de instantánea.](../media/azure-netapp-files/backup-navigate-snapshot-policy.png)   

4.  Haga clic en **Add snapshot policy** (Agregar directiva de instantáneas).
5.  En la página de directiva de instantáneas que aparece, especifique el número de instantáneas que quiere conservar y la programación de creación de instantáneas para el volumen. Haga clic en **Save**(Guardar).  

    Actualmente, la funcionalidad de copia de seguridad solo puede realizar copias de seguridad de instantáneas diarias, semanales y mensuales. (No se admiten copias de seguridad por hora).   

    * Para establecer una configuración de instantánea diaria, especifique la hora del día a la que quiere que se cree la instantánea. 
    * Para establecer una configuración de instantánea semanal, especifique el día de la semana y la hora del día en que quiere que se cree la instantánea. 
    * Para establecer una configuración de instantánea mensual, especifique el día del mes y la hora del día en que quiere que se cree la instantánea. 
    * Para cada configuración de instantánea, especifique el número de instantáneas que quiere conservar.

    Por ejemplo, si quiere que se realicen copias de seguridad diarias, debe configurar una directiva de instantáneas con una programación de instantáneas y un número de instantáneas diarios y, a continuación, aplicar esa directiva de instantáneas diaria al volumen. Si cambia la directiva de instantáneas o elimina la configuración de instantáneas diarias, no se crearán instantáneas diarias y, por lo tanto, no se realizarán copias de seguridad diarias. El mismo proceso y comportamiento se aplican a las copias de seguridad semanales y mensuales.  

    Asegúrese de que cada instantánea tenga una configuración de programación de instantánea única. Por diseño, Azure NetApp Files impide eliminar la última copia de seguridad. Si varias instantáneas tienen la misma hora (por ejemplo, la misma configuración de programación diaria y semanal), Azure NetApp Files las considera como las más recientes y se impide su eliminación.  

    En el ejemplo siguiente se muestra una configuración de directiva de instantánea diaria: 

    ![Captura de pantalla que muestra la configuración de la directiva de instantánea diaria.](../media/azure-netapp-files/backup-daily-snapshot-policy.png)

6.  Aplique la directiva de instantánea al volumen del que quiera hacer una copia de seguridad:  

    1. Vaya a la página **Volúmenes**, haga clic con el botón derecho en el volumen al que quiera aplicar una directiva de instantánea y seleccione **Editar**.   
        ![Captura de pantalla que muestra el menú de edición del volumen.](../media/azure-netapp-files/backup-volume-edit-menu.png)   

    2. En la ventana de edición, en **Directiva de instantánea**, seleccione la directiva que se va a aplicar. Haga clic en **OK**.   
        ![Captura de pantalla que muestra la ventana de edición con la lista desplegable de directiva de instantánea.](../media/azure-netapp-files/backup-volume-edit-snapshot-policy.png)    

## <a name="configure-a-backup-policy"></a>Configuración de una directiva de copia de seguridad

Una directiva de copia de seguridad permite proteger un volumen a intervalos programados periódicamente.  

Debe crear una directiva de copia de seguridad y asociarla al volumen del que quiere realizar la copia de seguridad. Una sola directiva de copia de seguridad puede asociarse a varios volúmenes. Las copias de seguridad pueden suspenderse temporalmente si se deshabilita la directiva o si se deshabilitan las copias de seguridad en el nivel de volumen. Las copias de seguridad también pueden deshabilitarse por completo en el nivel de volumen, lo que da lugar a la limpieza de todos los datos asociados en el almacenamiento de Azure. Una directiva de copia de seguridad no se puede eliminar si está asociada a cualquier volumen.

Para habilitar una copia de seguridad basada en directivas (programada): 

1. Inicie sesión en Azure Portal y navegue a **Azure NetApp Files**. 
2. Seleccione su suscripción a Azure NetApp Files.
3. Seleccione **Copias de seguridad**. 

    ![Captura de pantalla en la que se muestra cómo navegar a la opción Copias de seguridad.](../media/azure-netapp-files/backup-navigate.png)

4. Seleccione **Directivas de copia de seguridad**.
5. Seleccione **Agregar**. 
6. En la página **Directiva de copia de seguridad**, especifique el nombre de dicha directiva.  Escriba el número de copias de seguridad que quiere conservar para las copias de seguridad diarias, semanales y mensuales. Haga clic en **Save**(Guardar).

    ![Captura de pantalla en la que se muestra la ventana Directiva de copia de seguridad.](../media/azure-netapp-files/backup-policy-window-daily.png)

    * Si configura una directiva de copia de seguridad y la asocia al volumen sin asociar una directiva de instantánea, la copia de seguridad no funciona correctamente. Solo se transferirá una instantánea de línea base al almacenamiento de Azure. 
    * Por cada directiva de copia de seguridad que configure (por ejemplo, de copias de seguridad diarias), asegúrese de tener una configuración de directiva de instantánea correspondiente (en este caso, de instantáneas diarias).
    * La directiva de copia de seguridad tiene una dependencias de la directiva de instantánea. Si aún no ha creado la directiva de instantánea, puede configurar ambas directivas al mismo tiempo si selecciona la casilla **Create snapshot policy** (Crear directiva de instantáneas) en la ventana Directiva de copia de seguridad.   

        ![Captura de pantalla que muestra la ventana Directiva de copia de seguridad con la directiva de instantánea seleccionada.](../media/azure-netapp-files/backup-policy-snapshot-policy-option.png)
 
### <a name="example-of-a-valid-configuration"></a>Ejemplo de una configuración válida

En la configuración de ejemplo siguiente se muestra cómo configurar una directiva de protección de datos en el volumen con las cinco instantáneas diarias más recientes, las cuatro instantáneas semanales más recientes y las tres instantáneas mensuales más recientes del volumen. Esta configuración da como resultado la copia de seguridad de las 15 instantáneas diarias más recientes, las seis instantáneas semanales más recientes y las cuatro instantáneas mensuales más recientes.

* Directiva de instantánea:   
    Diaria: `Number of Snapshots to Keep = 5`   
    Semanal: `Number of Snapshots to Keep = 4`   
    Mensual: `Number of Snapshots to Keep = 3`
* Directiva de copia de seguridad:   
    Diaria: `Daily Backups to Keep = 15`   
    Semanal: `Weekly Backups to Keep = 6`   
    Mensual: `Monthly Backups to Keep = 4`

### <a name="example-of-an-invalid-configuration"></a>Ejemplo de una configuración no válida

En la configuración de ejemplo siguiente hay una directiva de copia de seguridad configurada para copias de seguridad diarias, pero la directiva de instantánea no tiene ninguna configuración correspondiente. Como resultado, no se crean instantáneas diarias de las que la directiva de copia de seguridad pueda realizar copias de seguridad. Esta configuración solo haría copias de seguridad de las instantáneas semanales y mensuales.

* Directiva de instantánea:   
    Semanal: `Number of Snapshots to Keep = 4`   
    Mensual: `Number of Snapshots to Keep = 3`   
* Directiva de copia de seguridad:   
    Diaria: `Daily Backups to Keep = 15`   
    Semanal: `Weekly Backups to Keep = 6`   
    Mensual: `Monthly Backups to Keep = 4`   

## <a name="enable-backup-functionality-for-a-volume-and-assign-a-backup-policy"></a>Habilitación de la funcionalidad de copia de seguridad para un volumen y asignación de una directiva de copia de seguridad

Cada uno de los volúmenes de Azure NetApp Files debe tener habilitada la funcionalidad de copia de seguridad para poder realizar copias de seguridad (basadas en directivas o manuales). 

Una vez habilitada dicha funcionalidad, debe asignar una directiva de copia de seguridad a un volumen para que las copias de seguridad basadas en directivas surtan efecto. (Para las copias de seguridad manuales, el uso de una directiva de copia de seguridad es opcional).

Para habilitar la funcionalidad de copia de seguridad de un volumen:  

1. Vaya a **Volúmenes** y seleccione el volumen para el que quiera habilitar la copia de seguridad.
2. Seleccione **Configurar**.
3. En la página de configuración de copias de seguridad, cambie el valor **Habilitado** a **Activado**.
4. En el menú desplegable **Directiva de instantánea**, asigne la directiva de instantánea que se va a usar para el volumen. 
5. En el menú desplegable **Directiva de copia de seguridad**, asigne la directiva de copia de seguridad que se va a usar para el volumen. Haga clic en **OK**.

    La información del almacén se rellena previamente.  

    ![Captura de pantalla que muestra la ventana de configuración de copias de seguridad.](../media/azure-netapp-files/backup-configure-window.png)


## <a name="next-steps"></a>Pasos siguientes  

* [Descripción de la copia de seguridad de archivos de Azure NetApp Files](backup-introduction.md)
* [Requisitos y consideraciones para la copia de seguridad de archivos de Azure NetApp Files](backup-requirements-considerations.md)
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuración de copias de seguridad manuales](backup-configure-manual.md)
* [Administrar directivas de copia de seguridad](backup-manage-policies.md)
* [Búsqueda de copias de seguridad](backup-search.md)
* [Restauración de una copia de seguridad en un volumen nuevo](backup-restore-new-volume.md)
* [Deshabilitación de la funcionalidad de copia de seguridad de un volumen](backup-disable.md)
* [Eliminación de copias de seguridad de un volumen](backup-delete.md)
* [Métricas de copia de seguridad de los volúmenes](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)


