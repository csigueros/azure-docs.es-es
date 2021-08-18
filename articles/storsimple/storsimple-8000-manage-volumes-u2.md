---
title: Administrar volúmenes de StorSimple (Update 3)
description: Explica cómo agregar, modificar, supervisar y eliminar volúmenes de StorSimple y cómo desconectarlos en caso necesario.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 08/11/2021
ms.author: alkohli
ms.openlocfilehash: 5653fdcaa37b1adb728a23454317c6088b9efbc7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750647"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Usar el servicio Administrador de dispositivos de StorSimple para administrar volúmenes (Update 3 o posterior)

## <a name="overview"></a>Información general

En este tutorial se explica cómo usar el servicio Administrador de dispositivos de StorSimple para crear y administrar volúmenes en los dispositivos de la serie StorSimple 8000 que ejecuten Update 3 y versiones posteriores.

El servicio StorSimple Device Manager es una extensión de Azure Portal que permite administrar la solución StorSimple desde una única interfaz web. Use Azure Portal para administrar los volúmenes en todos los dispositivos. También puede crear y administrar los servicios de StorSimple, administrar dispositivos, directivas de copia de seguridad y el catálogo de copias de seguridad, y ver las alertas.

## <a name="volume-types"></a>Tipos de volúmenes

Los volúmenes de StorSimple pueden ser:

* **Volúmenes anclados localmente**: los datos de estos volúmenes se mantienen en el dispositivo StorSimple local en todo momento.
* **Volúmenes en capas**: los datos de estos volúmenes pueden volcarse en la nube.

Un volumen de archivo es un tipo de volumen en capas. El tamaño del fragmento de desduplicación más grande utilizado para los volúmenes de archivo permite que el dispositivo transfiera a la nube segmentos más grandes de datos.

Si es necesario, puede cambiar un volumen local por uno en capas y viceversa. Para más información, consulte [Cambiar el tipo de volumen](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Volúmenes anclados localmente

Los volúmenes anclados localmente son volúmenes totalmente aprovisionados, sin capas de datos en la nube, lo que garantiza la conectividad local de los datos principales independiente de la nube. Los datos de los volúmenes anclados localmente no se desduplican ni comprimen, pero las instantáneas de dichos volúmenes se desduplican. 

Los volúmenes anclados localmente están totalmente aprovisionados, por lo que debe haber espacio suficiente en el dispositivo cuando los crea. Puede aprovisionar volúmenes anclados localmente hasta un tamaño máximo de 8 TB en el dispositivo StorSimple 8100 y de 20 TB en el dispositivo 8600. StorSimple reserva el resto del espacio local en el dispositivo para las instantáneas, los metadatos y el procesamiento de los datos. Puede aumentar el tamaño de un volumen anclado localmente hasta el espacio máximo disponible, pero no puede reducir el tamaño de un volumen una vez creado.

Cuando se crea un volumen anclado localmente, el espacio disponible para la creación de los volúmenes en capas se reduce. Y al contrario, si tiene volúmenes en capas existentes, el espacio disponible para crear volúmenes anclados localmente será inferior a los límites máximos indicados anteriormente. Para obtener más información sobre los volúmenes locales, consulte las [preguntas más frecuentes sobre los volúmenes anclados localmente](storsimple-8000-local-volume-faq.yml).

### <a name="tiered-volumes"></a>Volúmenes en capas

Los volúmenes en capas son volúmenes con aprovisionamiento reducido en los que los datos a los que se accede con frecuencia permanecen locales en el dispositivo y los datos de uso menos frecuente se apilan automáticamente en la nube. El aprovisionamiento fino es una tecnología de virtualización en que el almacenamiento disponible parece superar los recursos físicos. En lugar de reservar almacenamiento suficiente por adelantado, StorSimple utiliza el aprovisionamiento fino para asignar solo el espacio suficiente para cumplir con los requisitos actuales. La naturaleza elástica del almacenamiento en la nube facilita este enfoque porque StorSimple puede aumentar o disminuir el almacenamiento en la nube para cumplir con las exigencias cambiantes.

Si usa el volumen en capas para los datos de archivo, active casilla **Usar este volumen para los datos de archivo a los que accede con menos frecuencia** para cambiar el tamaño del fragmento de desduplicación del volumen a 512 KB. Si no selecciona esta opción, el volumen correspondiente en capas usará un tamaño del fragmento de 64 KB. Un mayor tamaño de fragmento de desduplicación permite que el dispositivo acelere la transferencia de datos de archivado de gran tamaño a la nube.


### <a name="provisioned-capacity"></a>Capacidad aprovisionada

Para conocer la capacidad máxima aprovisionada de cada dispositivo y tipo de volumen, consulte la siguiente tabla. (Tenga en cuenta que los volúmenes anclados localmente no están disponibles en un dispositivo virtual).

| Tipo | Tamaño máximo del volumen en capas | Tamaño máximo del volumen anclado localmente |
| --- | --- | --- |
| **Dispositivos físicos** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Dispositivos virtuales** | | |
| 8010 |30 TB |N/D |
| 8020 |64 TB |N/D |

## <a name="the-volumes-blade"></a>La hoja Volúmenes

La hoja **Volúmenes** permite administrar los volúmenes de almacenamiento que se aprovisionaron en el dispositivo de Microsoft Azure StorSimple para los iniciadores (servidores). Muestra la lista de volúmenes en los dispositivos de StorSimple conectados al servicio.

 ![Página de volúmenes](./media/storsimple-8000-manage-volumes-u2/volumes-list.png)

Un volumen se compone de una serie de atributos:

* **Nombre** : nombre descriptivo que debe ser único y que ayuda a identificar el volumen. Este nombre también se usa en los informes de supervisión cuando se filtra por un volumen específico. Una vez que se crea el volumen, no se puede cambiar el nombre.
* **Estado** : puede estar conectado o desconectado. Si un volumen está desconectado, no es visible para los iniciadores (servidores) que tienen permitido el acceso para usar el volumen.
* **Capacidad** : especifica la cantidad total de datos que puede almacenar el iniciador (servidor). Los volúmenes anclados localmente están completamente aprovisionados y residen en el dispositivo StorSimple. Los volúmenes en capas tienen aprovisionamiento reducido y los datos están desduplicados. Esto implica que el dispositivo ya no asigna previamente la capacidad física de almacenamiento de forma interna o en la nube en función de la capacidad configurada del volumen. La capacidad del volumen se asigna y se consume a petición.
* **Tipo**: indica si el tipo del volumen es **En capas** (predeterminado) o **Anclado localmente**.

Siga las instrucciones de este tutorial para realizar las siguientes tareas:

* Agregar un volumen 
* Modificar un volumen 
* Cambiar el tipo de volumen
* Eliminar un volumen 
* Desconectar un volumen 
* Supervisar un volumen 

## <a name="add-a-volume"></a>Agregar un volumen

Ya [creó un volumen](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) durante la implementación del dispositivo de la serie StorSimple 8000. El procedimiento para agregar un volumen es similar.

#### <a name="to-add-a-volume"></a>Para agregar un volumen

1. En la lista tabular de dispositivos de la hoja **Dispositivos**, seleccione el dispositivo. Haga clic en **+ Agregar volumen**.

    ![Adición de un nuevo volumen](./media/storsimple-8000-manage-volumes-u2/add-volume-01.png)

2. En la hoja **Agregar un volumen**:
   
    1. El campo **Seleccionar dispositivo** se rellena automáticamente con el dispositivo actual.

    2. En la lista desplegable, seleccione el contenedor de volúmenes en el que necesita agregar un volumen.

    3.  Escriba un **Nombre** para el volumen. Una vez que se crea el volumen, no se puede cambiar el nombre del volumen.

    4. En la lista desplegable, seleccione el **tipo** del volumen. Para cargas de trabajo que requieren garantías locales, latencias bajas y un rendimiento más alto, seleccione un volumen **Localmente anclado** . Para todos los demás datos, seleccione un volumen **En capas** . Si usa este volumen para datos de archivo, seleccione **Usar este volumen para los datos de archivo a los que accede con menos frecuencia**.
      
       Un volumen en capas se aprovisiona de manera fina y se puede crear rápidamente. Si utiliza el volumen en capas para los datos de archivo, al seleccionar **Usar este volumen para los datos de archivo a los que accede con menos frecuencia** se cambiará el tamaño del fragmento de desduplicación del volumen a 512 KB. Si este campo no está activado, el volumen correspondiente en capas utilizará un tamaño del fragmento de 64 KB. Un mayor tamaño de fragmento de desduplicación permite que el dispositivo acelere la transferencia de datos de archivado de gran tamaño a la nube.
       
       Un volumen localmente anclado se aprovisiona de forma intensa y garantiza que los datos principales del volumen continúen siendo locales en el dispositivo y que no se traspasan a la nube.  Si crea un volumen localmente anclado, el dispositivo comprobará el espacio disponible en los niveles locales para aprovisionar el volumen del tamaño solicitado. La operación que consiste en crear un volumen localmente anclado puede implicar traspasar los datos existentes del dispositivo a la nube, por lo que la creación del volumen puede llevar mucho tiempo. El tiempo total depende del tamaño del volumen aprovisionado, el ancho de banda de red disponible y los datos en el dispositivo.

    5. Especifique la **Capacidad aprovisionada** para el volumen. Tome nota de la capacidad que está disponible en función del tipo de volumen seleccionado. El tamaño del volumen especificado no debe superar el espacio disponible.
      
       Puede aprovisionar volúmenes anclados localmente de hasta 8,5 TB o volúmenes en capas de hasta 200 TB en el dispositivo 8100. En el dispositivo 8600 de mayor tamaño, puede aprovisionar volúmenes anclados localmente de hasta 22,5 TB o volúmenes en capas de hasta 500 TB. Como el espacio local del dispositivo es necesario para hospedar el espacio de trabajo de los volúmenes en capas, la creación de volúmenes localmente anclados afectará el espacio disponible para aprovisionar volúmenes en capas. Por lo tanto, si crea un volumen anclado localmente, se reduce el espacio disponible para la creación de volúmenes en capas. De forma similar, si se crea un volumen en capas, se reducirá el espacio disponible para la creación de volúmenes anclados localmente.
      
       Si se aprovisiona un volumen anclado localmente de 8,5 TB (tamaño máximo permitido) en el dispositivo 8100, habrá agotado todo el espacio local disponible en él. No puede crear ningún volumen en capas desde ese punto en adelante, porque no hay ningún espacio local en el dispositivo para hospedar el espacio de trabajo del volumen en capas. Los volúmenes existentes en capas también afectan al espacio disponible. Por ejemplo, si tiene un dispositivo 8100 que ya cuenta con volúmenes en capas de aproximadamente 106 TB, solo 4 TB de espacio estarán disponibles para volúmenes anclados localmente.

    6. En el campo **Hosts conectados**, haga clic en la flecha y, a continuación, seleccione cada ACR que quiera conectar. En la hoja **Hosts conectados**, seleccione un ACR existente o agregue un ACR nuevo. Si elige un nuevo ACR, proporcione después un **Nombre** para el ACR y proporcione el **Nombre calificado iSCSI** (IQN) del host de Windows. Si no tiene el IQN, vaya a Obtener el IQN de un host de Windows Server.<!--Please verify: Is new ACR capability still available? Are the labels and controls the same?--> 

        ![Adición de un host conectado](./media/storsimple-8000-manage-volumes-u2/add-volume-02.png)<!--New graphic. Source: add-volume-connected host-->

   7. Cuando finalice la configuración, haga clic en **Crear**. 

      Se creará un volumen con la configuración especificada. El nuevo volumen está listo para usarse.

> [!NOTE]
> Si crea un volumen anclado localmente e inmediatamente después crea otro, los trabajos de creación de volúmenes se ejecutan de manera secuencial. El primer trabajo de creación de volumen debe finalizar para que el siguiente pueda comenzar.

## <a name="modify-a-volume"></a>Modificar un volumen

Modifique un volumen cuando necesite expandirlo o cambiar los hosts que tienen acceso al volumen.

> [!IMPORTANT]
> * Si modifica el tamaño del volumen en el dispositivo, también deberá cambiar el tamaño del volumen en el host.
> * Los pasos del lado host que se describen aquí son para Windows Server 2012 (2012R2). Los procedimientos para Linux u otros sistemas operativos de host serán diferentes. Consulte las instrucciones del sistema operativo del host cuando modifique el volumen del host que esté ejecutando otro sistema operativo.

#### <a name="to-modify-a-volume"></a>Para modificar un volumen

1. Vaya al servicio Administrador de dispositivos de StorSimple y, después, haga clic en **Dispositivos**. En la lista tabular de los dispositivos, seleccione el dispositivo que tiene el volumen que quiere modificar. Haga clic en **Configuración > Volúmenes**.

    ![Ir a la hoja Volúmenes](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

2. Desde la lista tabular de volúmenes, seleccione el volumen y haga clic con el botón derecho para abrir el menú contextual. Seleccione **Desconectar** para desconectar el volumen que va a modificar.

    ![Seleccionar y desconectar el volumen](./media/storsimple-8000-manage-volumes-u2/modify-volume-04.png)

3. En la hoja **Desconectar**, revise el impacto de desconectar el volumen y active la casilla correspondiente. Asegúrese en primer lugar de que está desconectado el volumen correspondiente en el host. Para obtener información sobre cómo desconectar un volumen en el servidor de host conectado a StorSimple, consulte las instrucciones específicas del sistema operativo. Haga clic en **Desconectar**.

    ![Revisar el impacto de desconectar el volumen](./media/storsimple-8000-manage-volumes-u2/modify-volume-05.png)

4. Después de que el volumen esté sin conexión (como se muestra en el estado del volumen), seleccione el volumen y haga clic con el botón derecho para abrir el menú contextual. Seleccione **Modificar volumen**.

    ![Seleccionar Modificar volumen](./media/storsimple-8000-manage-volumes-u2/modify-volume-09.png)


5. En la hoja **Modificar volumen**, puede realizar los siguientes cambios:
   
   1. El **Nombre** del volumen no se puede editar.
   2. Convierta **Tipo** de anclado localmente a en capas o viceversa (vea [Cambiar el tipo de volumen](#change-the-volume-type) para más información).
   3. Aumentar la **Capacidad aprovisionada**. La **Capacidad aprovisionada** solo puede aumentarse. No se puede reducir un volumen después de crearlo.
   4. En **Hosts conectados**, puede modificar el ACR. Para modificar un ACR, el volumen debe estar sin conexión.

       ![Revisión del impacto de desconectar el volumen 2](./media/storsimple-8000-manage-volumes-u2/modify-volume-11.png)<!--Legacy screen doesn't match step. New graphic needed?-->

6. Haga clic en **Guardar** para guardar los cambios. Cuando se le pida confirmación, haga clic en **Sí**. El Portal de Azure mostrará un mensaje de actualización del volumen. Presentará en la pantalla un mensaje de confirmación cuando el volumen se haya actualizado correctamente.

    ![Revisión del impacto de desconectar el volumen 3](./media/storsimple-8000-manage-volumes-u2/modify-volume-05.png)<!--Updated graphic. Source: modify-volume-save-->

7. Si se va a expandir un volumen, realice los pasos siguientes en el equipo host Windows:
   
   1. Vaya a **Administración de equipos** ->**Administración de discos**.
   2. Haga clic con el botón derecho en **Administración de discos** y seleccione **Volver a examinar los discos**.
   3. En la lista de discos, seleccione el volumen que actualizó, haga clic en él con el botón derecho y seleccione **Extender volumen**. Se iniciará el Asistente para extender volúmenes. Haga clic en **Next**.
   4. Complete el asistente aceptando los valores predeterminados que se proporcionan. Cuando el asistente haya finalizado, el volumen debería mostrar el aumento de tamaño.
      
      > [!NOTE]
      > Si expande un volumen anclado localmente e inmediatamente después expande otro, los trabajos de expansión de volúmenes se ejecutan de manera secuencial. El primer trabajo de expansión de volumen debe finalizar para que el siguiente pueda comenzar.
      

## <a name="change-the-volume-type"></a>Cambiar el tipo de volumen

Puede cambiar el tipo de volumen en capas por uno anclado localmente y viceversa. Sin embargo, esta conversión no debería tener lugar con frecuencia.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Consideraciones de conversión de volúmenes en capas a locales

Algunas de las razones para convertir un volumen en capas por uno volumen anclado localmente son:

* Garantías locales respecto a la disponibilidad de datos y el rendimiento.
* Eliminación de problemas de conectividad y latencias en la nube.

Normalmente, son volúmenes existentes de pequeño tamaño a los que quiere acceder con frecuencia. Un volumen anclado localmente está aprovisionado por completo cuando se crea. 

Si va a convertir un volumen en capas en uno anclado localmente, StorSimple comprueba que haya espacio suficiente en el dispositivo antes de iniciar la conversión. Si no tiene suficiente espacio, recibirá un error y se cancelará la operación. 

> [!NOTE]
> Antes de comenzar la conversión de un volumen en capas en uno anclado localmente, asegúrese de tener en cuenta los requisitos de espacio de las otras cargas de trabajo. 

La conversión de un volumen en capas en uno anclado localmente puede afectar de forma negativa al rendimiento del dispositivo. Además, los siguientes factores podrían aumentar el tiempo que se tarda en completar la conversión:

* No hay suficiente ancho de banda.
* No hay ninguna copia de seguridad actual.

Para minimizar los efectos de estos factores:

* Revise su directivas de límite de ancho de banda y asegúrese de que haya disponible un ancho de banda dedicado de 40 Mbps.
* Programe la conversión para las horas de poca actividad.
* Tome una instantánea de nube antes de iniciar la conversión.

Si va a convertir varios volúmenes (compatibles con diferentes cargas de trabajo), debe dar precedencia a la conversión de los volúmenes de mayor prioridad. Por ejemplo, debe convertir los volúmenes que hospedan máquinas virtuales o volúmenes con cargas de trabajo SQL antes que los volúmenes con cargas de trabajo de recursos compartidos de archivos.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Consideraciones de conversión de volúmenes locales a en capas

Es posible que quiera cambiar un volumen anclado localmente por uno en capas si necesita espacio adicional para aprovisionar otros volúmenes. Al convertir el volumen anclado localmente en un volumen en capas, la capacidad disponible en el dispositivo aumenta en proporción al tamaño de la capacidad liberada. Si los problemas de conectividad impiden la conversión de un volumen local en un volumen en capas, el volumen local exhibirá propiedades de un volumen en capas hasta que finalice la conversión. Esto se debe a que algunos datos podrían haberse escrito en la nube. Estos datos volcados seguirán ocupando espacio local en el dispositivo que no se podrá liberar hasta que la operación se reinicie y finalice.

> [!NOTE]
> La conversión de un volumen puede tardar algún tiempo y una conversión no se puede cancelar una vez que se ha iniciado. El volumen permanece en línea durante la conversión y puede realizar copias de seguridad, pero no puede expandir ni restaurar el volumen mientras dura la operación.


#### <a name="to-change-the-volume-type"></a>Para cambiar el tipo de volumen

1. Vaya al servicio Administrador de dispositivos de StorSimple y, después, haga clic en **Dispositivos**. En la lista tabular de los dispositivos, seleccione el dispositivo que tiene el volumen que quiere modificar. Haga clic en **Configuración > Volúmenes**.

    ![Navegación a la hoja Volúmenes 2](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

3. Desde la lista tabular de volúmenes, seleccione el volumen y haga clic con el botón derecho para abrir el menú contextual. Seleccione **Modificar**.

    ![Seleccionar Modificar en el menú contextual](./media/storsimple-8000-manage-volumes-u2/change-volume-type-02.png)

4. En la hoja **Modificar volumen**, cambie el tipo de volumen; para ello, seleccione el nuevo tipo en la lista desplegable **Tipo**.
   
   * Si va a cambiar el tipo a **Anclado localmente**, StorSimple comprobará si hay capacidad suficiente.
   * Si va a cambiar el tipo a **En capas** y este volumen se va a usar para los datos de archivo, active la casilla **Usar este volumen para los datos de archivo a los que accede con menos frecuencia**.
   * Si va a configurar un volumen anclado localmente como un volumen en capas o _viceversa_, aparecerá el siguiente mensaje.
   
     ![Mensaje de cambio del tipo de volumen](./media/storsimple-8000-manage-volumes-u2/change-volume-type-03.png)

7. Haga clic en **Guardar** para guardar los cambios. Cuando se le pida confirmación, haga clic en **Sí** para iniciar el proceso de conversión. 

    ![Guardar y confirmar](./media/storsimple-8000-manage-volumes-u2/modify-volume-11.png)

8. En Azure Portal se muestra una notificación para la creación de trabajo que va a actualizar el volumen. Haga clic en la notificación para supervisar el estado del trabajo de conversión de volumen.

    ![Trabajo de conversión de volumen](./media/storsimple-8000-manage-volumes-u2/change-volume-type-05.png)

## <a name="take-a-volume-offline"></a>Desconectar un volumen

Es posible que necesite desconectar un volumen si tiene previsto modificarlo o eliminarlo. Cuando un volumen está desconectado, no está disponible para el acceso de lectura y escritura. Tiene que desconectar el volumen en el host y en el dispositivo.

#### <a name="to-take-a-volume-offline"></a>Para desconectar un volumen

1. Asegúrese de que el volumen en cuestión no está en uso antes de desconectarlo.
2. Desconecte el volumen primero en el host. Esto elimina cualquier riesgo potencial de dañar los datos del volumen. Para conocer los pasos específicos, consulte las instrucciones del sistema operativo del host.
3. Una vez desconectado el host, desconecte el volumen en el dispositivo siguiendo estos pasos:
   
    1. Vaya al servicio Administrador de dispositivos de StorSimple y, después, haga clic en **Dispositivos**. En la lista tabular de los dispositivos, seleccione el dispositivo que tiene el volumen que quiere modificar. Haga clic en **Configuración > Volúmenes**.

        ![Navegación a la hoja Volúmenes 3](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

    2. Desde la lista tabular de volúmenes, seleccione el volumen y haga clic con el botón derecho para abrir el menú contextual. Seleccione **Desconectar** para desconectar el volumen que va a modificar.

        ![Seleccionar y desconectar el volumen](./media/storsimple-8000-manage-volumes-u2/modify-volume-04.png)

3. En la hoja **Desconectar**, revise el impacto de desconectar el volumen y active la casilla correspondiente. Haga clic en **Desconectar**. 

    ![Revisión el impacto de desconectar el volumen 4](./media/storsimple-8000-manage-volumes-u2/modify-volume-05.png)
      
      Se le notificará cuando el volumen esté sin conexión. El estado del volumen también se actualiza a Sin conexión.
      
4. Después de desconectar un volumen, si lo selecciona y hace clic con el botón derecho, la opción **Conectar** está disponible en el menú contextual.

> [!NOTE]
> El comando **Desconectar** envía una solicitud al dispositivo para desconectar el volumen. Si los hosts todavía están usando el volumen las conexiones se interrumpirán, pero la desconexión del volumen no producirá un error.

## <a name="delete-a-volume"></a>Eliminar un volumen

> [!IMPORTANT]
> Puede eliminar un volumen solo si está desconectado.

Siga estos pasos para eliminar un volumen.

#### <a name="to-delete-a-volume"></a>Para eliminar un volumen

1. Vaya al servicio Administrador de dispositivos de StorSimple y, después, haga clic en **Dispositivos**. En la lista tabular de los dispositivos, seleccione el dispositivo que tiene el volumen que quiere modificar. Haga clic en **Configuración > Volúmenes**.

    ![Navegación a la hoja Volúmenes 4](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

3. Compruebe el estado del volumen que desea eliminar. Si el volumen que quiere eliminar no está desconectado, desconéctelo primero. Siga los pasos de [Desconexión de un volumen](#take-a-volume-offline).
4. Después de que el volumen esté sin conexión, seleccione el volumen, haga clic con el botón derecho para abrir el menú contextual y después seleccione **Eliminar**.

    ![Seleccionar Eliminar en el menú contextual](./media/storsimple-8000-manage-volumes-u2/delete-volume-01.png)

5. En la hoja **Eliminar**, revise y active la casilla del impacto de la eliminación de un volumen. Al eliminar un volumen, se pierden todos los datos que residen en él. 

    ![Guardado y confirmación de los cambios](./media/storsimple-8000-manage-volumes-u2/delete-volume-02.png)

6. Después de eliminar el volumen, la lista tabular de volúmenes se actualiza para indicar la eliminación.

    ![Lista de volúmenes actualizada](./media/storsimple-8000-manage-volumes-u2/delete-volume-03.png)
   
   > [!NOTE]
   > Si elimina un volumen anclado localmente, puede que el espacio disponible para los volúmenes nuevos no se actualice inmediatamente. El servicio Administrador de dispositivos de StorSimple actualiza el espacio local disponible de forma periódica. Le recomendamos esperar unos minutos antes de tratar de crear el nuevo volumen.
   >
   > Además, si elimina un volumen anclado localmente e inmediatamente después elimina otro, los trabajos de eliminación de volúmenes se ejecutarán de manera secuencial. El primer trabajo de eliminación de volumen debe finalizar para que el siguiente pueda comenzar.

## <a name="monitor-a-volume"></a>Supervisar un volumen

La supervisión de volúmenes permite recopilar estadísticas relacionados con la E/S de un volumen. La supervisión está habilitada de forma predeterminada para los 32 primeros volúmenes que cree. La supervisión de volúmenes adicionales está deshabilitada de forma predeterminada. 

> [!NOTE]
> La supervisión de volúmenes clonados está deshabilitada de forma predeterminada.


Siga estos pasos para habilitar o deshabilitar la supervisión de un volumen.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Para habilitar o deshabilitar la supervisión de volúmenes

1. Vaya al servicio Administrador de dispositivos de StorSimple y, después, haga clic en **Dispositivos**. En la lista tabular de los dispositivos, seleccione el dispositivo que tiene el volumen que quiere modificar. Haga clic en **Configuración > Volúmenes**.
2. Desde la lista tabular de volúmenes, seleccione el volumen y haga clic con el botón derecho para abrir el menú contextual. Seleccione **Modificar**.
3. En la hoja **Modificar volumen**, para **Supervisión** active **Habilitar** o **Deshabilitar** para habilitar o deshabilitar la supervisión.

    ![Deshabilitar la supervisión](./media/storsimple-8000-manage-volumes-u2/monitor-volume-01.png) 

4. Haga clic en **Guardar** y, cuando se le pida confirmación, haga clic en **Sí**. En Azure Portal se muestra una notificación de la actualización del volumen y luego un mensaje de confirmación, después de que el volumen se haya actualizado correctamente.

## <a name="next-steps"></a>Pasos siguientes

* Aprenda cómo [clonar un volumen de StorSimple](storsimple-8000-clone-volume-u2.md).
* Aprenda a [usar el servicio StorSimple Device Manager para administrar el dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

