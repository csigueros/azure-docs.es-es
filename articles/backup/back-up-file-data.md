---
title: Copias de seguridad de datos de archivo con MABS
description: Puede realizar una copia de seguridad de los datos de archivo en equipos cliente y servidor con MABS.
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: 5c5535969340b5612758ee0781e030fad0ec6dc6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806705"
---
# <a name="back-up-file-data-with-mabs"></a>Copias de seguridad de datos de archivo con MABS

Puede usar Microsoft Azure Backup Server (MABS) para realizar copias de seguridad de datos de archivo en equipos cliente y servidor.

## <a name="before-you-start"></a>Antes de comenzar

1. **Implementación de MABS:** compruebe que MABS esté instalado e implementado correctamente. Le recomendamos que consulte los siguientes artículos:

   - [Instalación de MABS](backup-azure-microsoft-azure-backup.md)

   - [¿De qué se puede realizar una copia de seguridad con MABS?](backup-mabs-protection-matrix.md)

   - [Novedades de MABS](backup-mabs-whats-new-mabs.md)

   - [Notas de la versión de MABS](backup-mabs-release-notes-v3.md)

1. **Configurar el almacenamiento**: con Azure puede almacenar datos de copia de seguridad en disco, en cinta y en la nube. Obtenga más información en [Preparación del almacenamiento de datos](/system-center/dpm/plan-long-and-short-term-data-storage?view=sc-dpm-2019&preserve-view=trus).

1. **Configuración del agente de protección de MABS:** deberá instalar el agente de protección de MABS en todas las máquinas de las que quiera realizar una copia de seguridad. Consulte [Implementación del agente de protección de MABS](backup-azure-microsoft-azure-backup.md).

## <a name="back-up-file-data"></a>Hacer una copia de seguridad de los datos de archivo

Una vez configurada la infraestructura de MABS, puede habilitar máquinas de protección con datos de archivo de los que desee realizar una copia de seguridad.

1. Para crear un grupo de protección, haga clic en **Protección** > **Acciones** > **Crear grupo de protección** para abrir el Asistente para **crear nuevo grupo de protección** en la Consola de MABS.

1. En **Seleccionar tipo de grupo de protección**, seleccione **Servidores**.

1. En **Seleccionar miembros del grupo** debe agregar al grupo de protección los equipos de cuyos datos de archivo quiera hacer una copia de seguridad. En dichos equipos debe seleccionar las ubicaciones, los recursos compartidos y las carpetas que quiera proteger.  [Implemente los grupos de protección](backup-support-matrix-mabs-dpm.md). Puede seleccionar distintos tipos de carpetas (como el escritorio), distintos archivos o todo el volumen. También puede excluir de la protección determinadas ubicaciones.

   >[!NOTE]
   > Si va a proteger el volumen en el que está habilitada la desduplicación, asegúrese de que el rol de servidor [Desduplicación de datos](/windows-server/storage/data-deduplication/install-enable) esté instalado en el servidor MABS. Consulte la [matriz de compatibilidad](backup-support-matrix-mabs-dpm.md#deduplicated-volumes-support) para obtener información detallada sobre la desduplicación.   

1. En **Seleccionar método de protección de datos**, especifique cómo quiere tratar las copias de seguridad a corto y largo plazo. La copia de seguridad a corto plazo siempre se hace primero en el disco, con la opción de hacer una copia de seguridad del disco a la nube de Azure con la copia de seguridad de Azure (ya sea a corto o largo plazo). Como alternativa a la copia de seguridad a largo plazo en la nube, también puede configurar una copia de seguridad a largo plazo en un dispositivo de cinta independiente o una biblioteca de cintas conectada al servidor de MABS.

1. En **Especificar objetivos a corto plazo**, especifique cómo quiere hacer la copia de seguridad en el almacenamiento a corto plazo en el disco.  En **Duración de retención**, especfique cuánto tiempo quiere conservar los datos en el disco. En **Frecuencia de la sincronización** debe especificar la frecuencia con la que quiere ejecutar una copia de seguridad incremental en el disco. Si no desea establecer un intervalo de copia de seguridad, puede marcar **Solo antes de un punto de recuperación**, para que MABS ejecute una copia de seguridad completa rápida justo antes de que haya programado un punto de recuperación.

1. Si quiere almacenar los datos en cinta para el almacenamiento a largo plazo, en **Especificar objetivos a largo plazo**, indique durante cuánto tiempo quiere conservar los datos de la cinta (de 1 a 99 años). En Frecuencia de copia de seguridad, especifique la frecuencia con la que se deben ejecutar las copias de seguridad en cinta. La frecuencia se basa en la duración de retención que haya especificado:

   - Cuando la duración de retención es de 1 a 99 años, puede seleccionar que las copias de seguridad se hagan con una frecuencia diaria, semanal, bisemanal, mensual, trimestral, semestral o anual.

   - Cuando la duración de retención es de 1 a 11 meses, puede seleccionar que las copias de seguridad se hagan con una frecuencia diaria, semanal, bisemanal o mensual.

   - Cuando la duración de retención es de 1 a 4 semanas, puede seleccionar que las copias de seguridad se hagan con una frecuencia diaria o semanal.

   En caso de utilizar una unidad de cinta independiente, MABS usará la misma cinta para las copias de seguridad diarias hasta que no haya suficiente espacio en ella. También puede colocar datos de diferentes grupos de protección en cinta.

   En la página **Seleccionar detalles de biblioteca y cinta**, especifique la cinta o biblioteca que va a usar. Especifique también si los datos se deben comprimir y cifrar en la cinta.

1. En la página **Revisar asignación de disco**, revise el espacio en disco del bloque de almacenamiento asignado al grupo de protección.

   **Total Data size** (Tamaño total de datos) es el tamaño de los datos de los que quiere realizar la copia de seguridad y **Disk space to be provisioned on MABS** (Espacio en disco que se va a aprovisionar en MABS) es el espacio que recomienda MABS para el grupo de protección. MABS elige el volumen ideal para la copia de seguridad, en función de la configuración. Pero puede editar las opciones de volumen de copia de seguridad en los **detalles de asignación del disco**. En el caso de las cargas de trabajo, seleccione el almacenamiento de su preferencia en el menú desplegable. Las modificaciones cambiarán los valores de **Almacenamiento total** y **Free Storage** (Almacenamiento libre) en el panel **Almacenamiento en disco disponible**. El espacio insuficiente es la cantidad de almacenamiento que MABS sugiere agregar al volumen, para continuar las copias de seguridad sin problemas en el futuro.

1. En **Seleccionar método de creación de réplicas**, seleccione cómo quiere tratar la replicación inicial de datos completos.  Si selecciona la opción de replicar a través de la red, le recomendamos que elija una hora de menor actividad. Para grandes cantidades de datos o condiciones de red no del todo óptimas, considere la posibilidad de replicar los datos sin conexión con medios extraíbles.

1. En **Elegir las opciones de la comprobación de coherencia**, seleccione cómo quiere automatizar las comprobaciones de coherencia. Puede habilitar una comprobación para que se ejecute solo cuando los datos de réplica no sean coherentes, o bien según una programación. Si no quiere configurar la comprobación de coherencia automática, puede ejecutar una comprobación manual en cualquier momento: para ello, haga clic con el botón derecho en el grupo de protección en el área **Protección** de la consola de MABS y seleccione **Realizar comprobación de coherencia**.

1. Si ha seleccionado realizar una copia de seguridad en la nube mediante Azure Backup, asegúrese de que en la página **Especificar datos de protección en línea** están seleccionadas las cargas de trabajo de las que quiere realizar una copia de seguridad en Azure.

1. En **Especificar la programación de copia de seguridad en línea**, especifique la frecuencia con la que se deben hacer las copias de seguridad incrementales en Azure. Puede programar que las copias de seguridad se ejecuten cada día, semana, mes y año y seleccionar la hora y la fecha en la que se deben ejecutar. Pueden realizarse copias de seguridad hasta dos veces al día. Cada vez que se ejecuta una copia de seguridad, se crea un punto de recuperación de datos en Azure a partir de la copia de los datos de copia de seguridad almacenados en el disco de MABS.

1. En **Especificar la directiva de retención en línea** puede especificar cómo se conservan en Azure los puntos de recuperación creados a partir de las copias de seguridad diaria, semanal, mensual o anual.

1. En **Elegir replicación en línea**, especifique cómo se efectuará la replicación completa inicial de datos. Puede replicar a través de la red o realizar una copia de seguridad sin conexión (propagación sin conexión). Las copias de seguridad sin conexión usan la característica Azure Import. [Más información](./backup-azure-backup-import-export.md).

1. En la página **Resumen**, revise la configuración. Al hacer clic en **Crear grupo**, se produce la replicación inicial de los datos. Cuando haya terminado, el grupo de protección tendrá el estado **Aceptar** en la página **Estado**. A continuación, se realiza la copia de seguridad en función de la configuración del grupo de protección.

## <a name="recover-backed-up-file-data"></a>Recuperar los datos de archivo de copia de seguridad

Puede usar el Asistente para recuperación para recuperar los datos. Al hacer doble clic en un volumen protegido en el panel **Datos protegidos** del asistente, MABS muestra los datos que pertenecen a dicho volumen en el panel de resultados. Para filtrar los nombres de servidores protegidos en orden alfabético haga clic en **Filtro**. Después de seleccionar un origen de datos para recuperar en la vista de árbol, puede seleccionar un punto de recuperación específico si hace clic en las fechas en negrita del calendario. Al hacer clic en **Recuperar** en el panel **Acciones**, MABS inicia el trabajo de recuperación.

## <a name="recover-data"></a>Recuperar datos
Para recuperar datos desde la consola de MABS, haga lo siguiente:

1. En la consola de MABS, haga clic en **Recuperación** en la barra de navegación. y busque los datos que quiere recuperar. En el panel de resultados, seleccione los datos.

1. Los puntos de recuperación disponibles se indican en negrita en el calendario de la sección de puntos de recuperación. Seleccione la fecha en negrita del punto de recuperación que desee recuperar.

1. En el panel **Elemento recuperable**, seleccione el elemento recuperable que desee recuperar.

1. En el panel **Acciones**, haga clic en **Recuperar** para abrir el Asistente para recuperación.

1. Puede recuperar los datos del siguiente modo:

   1. **Recuperar en ubicación original**. Tenga en cuenta que esto no funciona si el equipo cliente está conectado a través de una VPN. En tal caso, use una ubicación alternativa y copie los datos desde dicha ubicación.

   1. **Recuperar en una ubicación alternativa**.

   1. **Copiar en cinta**. Esta opción copia el volumen que contiene los datos seleccionados en una cinta de una biblioteca de MABS. También puede elegir comprimir o cifrar los datos en la cinta.

1. Especifique las opciones de recuperación:

   1. **Comportamiento de la recuperación de la versión existente**. Seleccione **Crear copia**, **Omitir** o **Sobrescribir**. Esta opción solo está habilitada si va a efectuar una recuperación en la ubicación original.

   1. **Restaurar seguridad**. Seleccione **Aplicar configuración de seguridad del equipo de destino** o **Aplicar configuración de seguridad de la versión de puntos de recuperación**.

   1. **Límite de uso del ancho de banda de red**. Haga clic en **Modificar** para habilitar el límite de uso del ancho de banda de la red.

   1. **Habilitar recuperación basada en SAN mediante instantáneas de hardware**. Seleccione esta opción para usar instantáneas de hardware basadas en SAN para una recuperación más rápida.
      
      Esta opción solo es válida cuando se tiene una red SAN donde está habilitada la funcionalidad de instantáneas de hardware, la red SAN tiene la capacidad de crear un clon y dividirlo para que se pueda escribir en él, y el equipo protegido y el servidor de MABS están conectados a la misma red SAN.

   1. **Notificación**. Haga clic en **Enviar correo electrónico cuando se complete la recuperación** y especifique los destinatarios que recibirán la notificación. Separe las direcciones de correo electrónico mediante comas.

1. Haga clic en **Siguiente** cuando haya realizado las selecciones en las opciones anteriores.

1. Revise la configuración de recuperación y haga clic en **Recuperar**. Tenga en cuenta que los trabajos de sincronización del elemento de recuperación seleccionado se cancelarán mientras la recuperación esté en curso.

Si usa Modern Backup Storage (MBS), no se admite la recuperación por el usuario final (EUR) del servidor de archivos. La EUR de servidor de archivos tiene una dependencia del Servicio de instantáneas de volumen (VSS) , que no utiliza el almacenamiento de copia de seguridad moderno. Si la recuperación por el usuario final está habilitada, se recuperan los datos de la siguiente forma:

1. Desplácese hasta el archivo de datos protegido. Haga clic con el botón derecho en el nombre de archivo > **Propiedades**.

1. En **Propiedades** > **Versiones anteriores**, seleccione la versión que desea recuperar.