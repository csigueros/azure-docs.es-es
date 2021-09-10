---
title: Copia de seguridad de máquinas virtuales de Azure Stack HCI con MABS
description: Este artículo contiene los procedimientos para realizar copias de seguridad y recuperar máquinas virtuales mediante Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: e7a653f008541db548b4468a70c429fe72701797
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114722630"
---
# <a name="back-up-azure-stack-hci-virtual-machines-with-azure-backup-server"></a>Copia de seguridad de máquinas virtuales de Azure Stack HCI con Azure Backup Server

En este artículo se explica cómo realizar copias de seguridad de máquinas virtuales en Azure Stack HCI mediante Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Escenarios admitidos

MABS puede hacer copias de seguridad de las máquinas virtuales de Azure Stack HCI en los escenarios siguientes:

- **Host de Azure Stack HCI**: copia de seguridad y recuperación del estado del sistema o BMR del host de Azure Stack HCI. El agente de protección de MABS debe estar instalado en el host.

- **Máquinas virtuales del clúster con almacenamiento local o directo**: realice una copia de seguridad de las máquinas virtuales invitadas en un clúster que tenga almacenamiento local o conectado directamente. Por ejemplo, una unidad de disco duro, un dispositivo de red de área de almacenamiento (SAN) o un dispositivo de almacenamiento conectado a la red (NAS).

- **Máquinas virtuales en un clúster con almacenamiento CSV**: realice una copia de seguridad de las máquinas virtuales hospedadas en un clúster de Azure Stack HCI con almacenamiento de volumen compartido (CSV) de clúster. El agente de protección de MABS se instala en cada nodo del clúster.

- **Movimiento de máquinas virtuales dentro de un clúster**: cuando las máquinas virtuales se mueven dentro de un clúster extendido o normal, MABS sigue protegiendo las máquinas virtuales siempre que el agente de protección de MABS esté instalado en el host de Azure Stack HCI. La forma en que MABS protege las máquinas virtuales depende del tipo de migración en vivo implicada. Con el movimiento de máquinas virtuales dentro de un clúster, MABS detecta la migración y realiza una copia de seguridad de la máquina virtual desde el nuevo nodo de clúster, sin que sea necesaria la intervención del usuario. Como la ubicación de almacenamiento no ha cambiado, MABS continúa con copias de seguridad completas rápidas. 

- **Movimiento de máquinas virtuales a un clúster extendido o normal diferente**: no se admite el traslado de la máquina virtual a otro clúster extendido o normal.

## <a name="host-versus-guest-backup"></a>Diferencias entre copia de seguridad de host y de invitado

MABS puede realizar una copia de seguridad de nivel de host o de invitado de las máquinas virtuales en Azure Stack HCI. En el nivel de host, el agente de protección de MABS se instala en el clúster o servidor de host de Azure Stack HCI y protege todas las máquinas virtuales y los archivos de datos que se ejecutan en el host.   En el nivel de invitado, el agente está instalado en cada máquina virtual y protege la carga de trabajo que existe en esa máquina.

Ambos métodos presentan ventajas y desventajas:

- Las copias de seguridad de nivel de host son flexibles porque funcionan con independencia del tipo de sistema operativo que se ejecute en los equipos invitados y no requieren la instalación del agente de protección de MABS en cada máquina virtual. Si implementa copias de seguridad de nivel de host, puede recuperar una máquina virtual completa, o bien archivos y carpetas (recuperación en el nivel de elemento).

- La copia de seguridad de nivel de invitado resulta útil si se quieren proteger cargas de trabajo específicas que se ejecutan en una máquina virtual. En el nivel de host, puede recuperar una máquina virtual completa o archivos específicos, pero no se proporcionará recuperación en el contexto de una aplicación específica. Por ejemplo, para recuperar elementos específicos de SharePoint de una máquina virtual de la que se ha realizado una copia de seguridad, tendría que realizar una copia de seguridad de nivel de invitado de esa máquina virtual. Si quiere proteger los datos almacenados en discos de acceso directo, use la copia de seguridad de nivel de invitado. El acceso directo permite que la máquina virtual acceda directamente al dispositivo de almacenamiento y no almacena datos de volúmenes virtuales en un archivo de disco duro virtual.

## <a name="backup-prerequisites"></a>Requisitos previos de copia de seguridad

Estos son los requisitos previos para realizar copias de seguridad de las máquinas virtuales con MABS:

| Requisito previo | Detalles |
| ------------ | ------- |
| Requisitos previos de MABS | <ul> <li>Si quiere realizar la recuperación de nivel de elemento de las máquinas virtuales (recuperar archivos, carpetas o volúmenes), tendrá que instalar el rol de Hyper-V en el servidor MABS. Si solo quiere recuperar la máquina virtual y no el nivel de elemento, el rol no es necesario.</li> <li>Puede proteger hasta 800 máquinas virtuales de 100 GB cada una en un servidor MABS y permitir varios servidores MABS que admitan clústeres más grandes.</li> <li>MABS excluye el archivo de paginación de las copias de seguridad incrementales para mejorar el rendimiento de las copias de seguridad de máquinas virtuales.</li> <li>MABS puede realizar una copia de seguridad de un servidor o clúster en el mismo dominio que el servidor MABS, o bien en un dominio secundario o de confianza. Si quiere realizar una copia de seguridad de las máquinas virtuales en un grupo de trabajo o en un dominio que no sea de confianza, tendrá que configurar la autenticación. Para un solo servidor, puede usar la autenticación NTLM o de certificado. Para un clúster, solo puede usar la autenticación de certificado.</li> <li>No se admite el uso de copias de seguridad de nivel de host para realizar copias de seguridad de datos de máquinas virtuales en discos de acceso directo. En este escenario, se recomienda usar la copia de seguridad de nivel de host para los archivos de disco duro virtual y la copia de seguridad de nivel de invitado para otros datos que no sean visibles en el host.</li> <li>Puede realizar copias de seguridad de máquinas virtuales almacenadas en volúmenes desduplicados.</li> </ul> |
| máquina virtual | <ul> <li> La versión de los componentes de integración que se ejecuta en la máquina virtual debe ser la misma que la del host de Azure Stack HCI. </li> <li> Para cada copia de seguridad de máquina virtual, necesitará espacio libre en el volumen en el que se hospedan los archivos de disco duro virtual para permitir espacio suficiente para los discos de diferenciación (AVHD) durante la copia de seguridad. El espacio debe ser al menos igual al cálculo Tamaño de disco inicial *Tasa de renovación* Hora del periodo de copia de seguridad. Si ejecuta varias copias de seguridad en un clúster, necesitará capacidad de almacenamiento suficiente para acomodar los AVHD de cada una de las máquinas virtuales en las que se use este cálculo. </li> </ul> |
| Requisitos previos de Linux | <ul><li> Puede realizar copias de seguridad de las máquinas virtuales Linux con MABS. Se admiten sólo las instantáneas coherentes para el archivo.</li></ul> |

## <a name="back-up-virtual-machines"></a>Copia de seguridad de máquinas virtuales

1. Configure el [servidor MABS](backup-azure-microsoft-azure-backup.md) y el [almacenamiento](backup-mabs-add-storage.md). Al configurar el almacenamiento, use estas instrucciones de capacidad de almacenamiento.

   - Tamaño medio de máquina virtual: 100 GB
   - Número de máquinas virtuales por servidor MABS: 800
   - Tamaño total de 800 máquinas virtuales: 80 TB
   - Espacio necesario para el almacenamiento de copia de seguridad: 80 TB

2. Configure el agente de protección de MABS en el servidor o en cada nodo del clúster.

3. En la Consola de administrador MABS, seleccione **Protección** > **Crear grupo de protección** para abrir el **Asistente para crear nuevo grupo de protección**.

4. En la página **Seleccionar miembros del grupo**, seleccione las máquinas virtuales que quiera proteger de los servidores host en los que se encuentran. Se recomienda colocar en un grupo de protección todas las máquinas virtuales que vayan a tener la misma directiva de protección. Para hacer un uso eficaz del espacio, habilite la colocación. La colocación permite localizar datos de diferentes grupos de protección en el mismo almacenamiento en disco o en cinta, de modo que varios orígenes de datos tengan una sola réplica y un volumen de punto de recuperación.

5. En la página **Seleccionar método de protección de datos**, especifique un nombre para el grupo de protección. Seleccione **Deseo protección a corto plazo mediante disco** y **Deseo protección en línea** si quiere realizar una copia de seguridad de los datos en Azure mediante el servicio Azure Backup.

6. En **Especificar objetivos a corto plazo** > **Duración de retención**, especifique cuánto tiempo quiere conservar los datos de disco. En **Frecuencia de la sincronización**, especifique con qué frecuencia se deben ejecutar las copias de seguridad incrementales de los datos. Como alternativa, en lugar de seleccionar un intervalo para las copias de seguridad incrementales, puede habilitar **Solo antes de un punto de recuperación**. Con esta opción habilitada, MABS ejecutará una copia de seguridad completa rápida justo antes de cada punto de recuperación programado.

    > [!NOTE]
    >Si va a proteger las cargas de trabajo de la aplicación, los puntos de recuperación se crean de acuerdo a la frecuencia de sincronización, siempre que la aplicación admita copias de seguridad incrementales. Si no es así, MABS ejecuta una copia de seguridad completa rápida, en lugar de una copia de seguridad incremental, y crea puntos de recuperación de acuerdo con la programación de copias de seguridad rápidas.<br></br>El proceso de copia de seguridad no hace una copia de seguridad de los puntos de control asociados a las máquinas virtuales.

7. En la página **Revisar asignación de disco**, revise el espacio en disco del grupo de almacenamiento asignado para el grupo de protección.

   **Total Data size** (Tamaño total de datos) es el tamaño de los datos de los que quiere realizar la copia de seguridad y **Disk space to be provisioned on MABS** (Espacio en disco que se va a aprovisionar en MABS) es el espacio que recomienda MABS para el grupo de protección. MABS elige el volumen ideal para la copia de seguridad, en función de la configuración. Pero puede editar las opciones de volumen de copia de seguridad en los **detalles de asignación del disco**. En el caso de las cargas de trabajo, seleccione el almacenamiento de su preferencia en el menú desplegable. Las modificaciones cambiarán los valores de **Almacenamiento total** y **Free Storage** (Almacenamiento libre) en el panel **Almacenamiento en disco disponible**. El espacio insuficiente es la cantidad de almacenamiento que MABS sugiere agregar al volumen, para continuar las copias de seguridad sin problemas en el futuro.

8. En la página **Seleccionar método de creación de réplicas**, especifique cómo se realizará la replicación inicial de los datos en el grupo de protección. Si selecciona **Automatically replicate over the network** (Replicar automáticamente por la red), se recomienda que elija una hora fuera de horas punta. Para grandes cantidades de datos o condiciones de red que no sean del todo óptimas, considere la posibilidad de seleccionar **Manualmente**, que requiere que los datos se repliquen sin conexión con medios extraíbles.

9. En la página **Opciones de comprobación de coherencia**, seleccione cómo quiere automatizar las comprobaciones de coherencia. Puede habilitar una comprobación para que se ejecute solo cuando los datos de réplica no sean coherentes, o bien según una programación. Si no quiere configurar la comprobación de coherencia automática, puede ejecutar una comprobación manual en cualquier momento si hace clic con el botón derecho en el grupo de protección y selecciona **Realizar comprobación de coherencia**.

    Después de crear el grupo de protección, la replicación inicial de los datos se realiza de acuerdo con el método seleccionado. Después de la replicación inicial, cada copia de seguridad se realiza según la configuración del grupo de protección. Si tiene que recuperar datos de copia de seguridad, tenga en cuenta lo siguiente:

## <a name="back-up-replica-virtual-machines"></a>Copia de seguridad de máquinas virtuales de réplica

Si MABS se ejecuta en Windows Server 2012 R2 o una versión posterior, puede hacer una copia de seguridad de las máquinas virtuales de réplica. Esto es útil por varias razones:

**Se reduce el impacto de las copias de seguridad en la carga de trabajo en ejecución**: una copia de seguridad de una máquina virtual genera cierta sobrecarga ya que se crea una instantánea. Al descargar el proceso de copia de seguridad a un sitio remoto secundario, la carga de trabajo en ejecución ya no se ve afectada por la operación de copia de seguridad. Esto solo es aplicable a las implementaciones en las que la copia de seguridad se almacena en un sitio remoto. Por ejemplo, podría realizar copias de seguridad diarias y almacenar los datos localmente para garantizar tiempos de restauración rápidos, pero realizar copias de seguridad mensuales o trimestrales de máquinas virtuales de réplica almacenadas de forma remota para la retención a largo plazo.

**Se ahorra ancho de banda**: en una implementación típica de oficina central y oficinas remotas, se necesita una cantidad adecuada de ancho de banda aprovisionado para transferir los datos de copia de seguridad entre sitios. Si crea una estrategia de replicación y conmutación por error, además de la de copia de seguridad de datos, puede reducir la cantidad de datos redundantes que se envían a través de la red. Al realizar una copia de seguridad de los datos de la máquina virtual de réplica en lugar de la principal, se ahorra la sobrecarga de enviar los datos de copia de seguridad a través de la red.

**Se habilita la copia de seguridad de anfitrión**: puede usar un centro de datos hospedado como un sitio de réplica, sin necesidad de un centro de datos secundario. En este caso, el Acuerdo de Nivel de Servicio de anfitrión requiere una copia de seguridad coherente de las máquinas virtuales de réplica.

Una máquina virtual de réplica se apaga hasta que se inicia una conmutación por error y VSS no puede garantizar una copia de seguridad coherente con la aplicación para una máquina virtual de réplica. Por tanto, la copia de seguridad de una máquina virtual de réplica solo será coherente con bloqueos. Si no se puede garantizar la coherencia de bloqueos, se producirá un error en la copia de seguridad y esto puede ocurrir bajo distintas condiciones:

- La máquina virtual de réplica no es correcta y se encuentra en un estado crítico.

- La máquina virtual de réplica se está resincronizando (en el estado Resincronización en curso o Resincronización necesaria).

- La replicación inicial entre el sitio principal y el secundario está en curso o pendiente para la máquina virtual.

- Los registros .hrl se aplican a la máquina virtual de réplica, o bien se ha producido un error en una acción anterior para aplicarlos en el disco virtual o se ha cancelado o interrumpido.

- La migración o conmutación por error de la máquina virtual de réplica está en curso.

## <a name="recover-backed-up-virtual-machines"></a>Recuperación de máquinas virtuales con copia de seguridad

Cuando se puede recuperar una máquina virtual con copia de seguridad, se usa el Asistente para recuperación para seleccionar la máquina virtual y el punto de recuperación específico. Para abrir el Asistente para recuperación y recuperar una máquina virtual:

1. En la consola de administrador de MABS, escriba el nombre de la máquina virtual, o bien expanda la lista de elementos protegidos, vaya a **Todos los HyperV Data protegidos** y seleccione la máquina virtual que quiera recuperar.

2. En el panel **Puntos de recuperación para**, en el calendario, seleccione cualquier fecha para ver los puntos de recuperación disponibles. Después, en el panel **Ruta de acceso**, seleccione el punto de recuperación que quiera usar en el Asistente para recuperación.

3. En el menú **Acciones**, seleccione **Recuperar** para abrir el Asistente para recuperación.

    La máquina virtual y el punto de recuperación que ha seleccionado aparecen en la pantalla **Revisar selección de recuperación**. Seleccione **Next** (Siguiente).

4. En la pantalla **Seleccionar tipo de recuperación**, seleccione dónde quiere restaurar los datos y, después, elija **Siguiente**.

    - **Recuperar en instancia original**: al recuperar en la instancia original, se eliminan el disco duro virtual original y todos los puntos de control asociados. MABS recupera el disco duro virtual y otros archivos de configuración en la ubicación original mediante VSS Writer de Hyper-V. Al final del proceso de recuperación, las máquinas virtuales siguen siendo de alta disponibilidad.
        Para la recuperación, el grupo de recursos debe estar presente. Si no está disponible, realice la recuperación en una ubicación alternativa y, después, haga que la máquina virtual sea de alta disponibilidad.

    - **Recuperar como máquina virtual para cualquier host**: MABS admite la recuperación de ubicación alternativa (ALR), que proporciona una recuperación sin problemas de una máquina virtual de Azure Stack HCI protegida a otro host dentro del mismo clúster, independientemente de la arquitectura del procesador. Las máquinas virtuales de Azure Stack HCI que se recuperan en un nodo de clúster no tendrán alta disponibilidad. Si elige esta opción, el Asistente para recuperación presentará una pantalla adicional para identificar el destino y la ruta de acceso de destino.
    
        >[!NOTE]
        >Si selecciona el host original, el comportamiento es el mismo que el de **Recuperar en instancia original**. Se eliminan el disco duro virtual original y todos los puntos de control asociados.

    - **Copiar en una carpeta de red**: MABS admite la recuperación de nivel de elemento (ILR) de archivos, carpetas, volúmenes y discos duros virtuales (VHD) a partir de una copia de seguridad de nivel de host de máquinas virtuales de Azure Stack HCI en un recurso compartido de red o un volumen en un servidor protegido de MABS. No es necesario que el agente de protección de MABS esté instalado dentro del invitado para realizar la recuperación de nivel de elemento. Si elige esta opción, el Asistente para recuperación presentará una pantalla adicional para identificar el destino y la ruta de acceso de destino.

5. En **Especificar opciones de recuperación**, configure las opciones de recuperación y seleccione **Siguiente**:

    - Si va a recuperar una VM sobre ancho de banda reducido, seleccione **Modificar** para habilitar **Límite de uso del ancho de banda de red**. Después de activar la opción de límite, puede especificar la cantidad de ancho de banda que quiere que esté disponible y la hora a la que lo está.
    - Seleccione **Habilitar la recuperación basada en SAN mediante instantáneas de hardware** si ha configurado la red.
    - Seleccione **Send an e-mail when the recovery completes** (Enviar un correo electrónico cuando se complete la recuperación) y, después, proporcione las direcciones de correo electrónico, si quiere que se envíen notificaciones por correo electrónico una vez que se complete el proceso de recuperación.

6. En la pantalla Resumen, asegúrese de que todos los detalles sean correctos. Si los detalles no son correctos o si quiere realizar un cambio, seleccione **Atrás**. Si está satisfecho con la configuración, seleccione **Recuperar** para iniciar el proceso de recuperación.

7. En la pantalla **Estado de la recuperación** se proporciona información sobre el trabajo de recuperación.

## <a name="next-steps"></a>Pasos siguientes

[Recuperación de datos de Azure Backup Server](./backup-azure-alternate-dpm-server.md)
