---
title: Migración de los datos de un bloque a Azure con Cirrus Data
titleSuffix: Azure Storage
description: Se proporciona una guía de inicio rápido para implementar Cirrus Migrate Cloud y migrar los datos a Azure.
author: dukicn
ms.author: nikoduki
ms.date: 09/06/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 69ee8dd0f7acf0959ccbfb47ebde01e6d17cfe21
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699437"
---
# <a name="migrate-your-block-data-to-azure-with-cirrus-migrate-cloud"></a>Migración de los datos de un bloque a Azure con Cirrus Migrate Cloud

Cirrus Migrate Cloud (CMC) permite la migración de discos desde un sistema de almacenamiento existente o desde la nube a Azure. La migración se realiza mientras el sistema original todavía está en funcionamiento. En este documento se presenta la metodología para configurar y ejecutar correctamente la migración.

## <a name="overview"></a>Información general

La solución usa agentes de migración distribuidos que se ejecutan en todos los host que permiten conexiones directas de host a host. Cada migración de host a host es independiente, lo que hace que la solución sea infinitamente escalable, sin cuellos de botella centrales para el flujo de datos. La migración usa la tecnología de cMotion™ para garantizar que la producción no se vea afectada. 

## <a name="use-cases"></a>Casos de uso

En este documento se trata un caso de migración genérico para trasladar la aplicación de una máquina virtual (que se ejecuta de forma local o en otro proveedor de nube) a una máquina virtual de Azure. Para consultar guías detalladas paso a paso de varios casos de uso, puede obtener más información en los vínculos siguientes:

- [Traslado de la carga de trabajo a Azure con cMotion](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-on-premises-to-azure-1xo3nuf/)
- [Traslado de discos Premium a discos Ultra](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-migration-between-azure-tiers-sxhppt/)
- [Traslado de AWS a Azure](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-migration-from-aws-to-azure-weegd9/.)

## <a name="components"></a>Componentes

Cirrus Migrate Cloud consta de varios componentes:

- La característica **cMotion™** de CMC realiza una migración de nivel de almacenamiento desde un origen a la nube de destino sin tiempo de inactividad para el host de origen. cMotion™ se usa para cambiar la carga de trabajo del disco de origen FC o iSCSI original al nuevo disco administrado de Azure de destino.
- El **Portal de administración basado en web** es una administración basada en web como un servicio. Permite a los usuarios administrar la migración y proteger cualquier almacenamiento en bloque. El Portal de administración basado en web proporciona interfaces para todas las tareas administrativas y de configuración de aplicaciones de CMC.

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-web-portal.jpg" alt-text="Captura de pantalla del Portal de CMC":::

## <a name="implementation-guide"></a>Guía de implementación

El usuario debe seguir los procedimientos recomendados de Azure para implementar una nueva máquina virtual. Si no está familiarizado con el proceso, obtenga más información en la [guía de inicio rápido](/azure/virtual-machines/windows/quick-create-portal).

Antes de iniciar la migración, asegúrese de se han cumplido estos requisitos previos:

- Compruebe que el sistema operativo de Azure tiene la licencia correcta.
- Compruebe el acceso a la máquina virtual de Azure.
- Compruebe que la licencia de la aplicación o base de datos está disponible para ejecutarse en Azure.
- Compruebe el permiso para asignar automáticamente el tamaño del disco de destino.
- Asegúrese de que el tamaño del disco administrado es igual o mayor que el del disco de origen. 
- Asegúrese de que la máquina virtual de origen o la de destino tiene un puerto abierto para permitir la conexión H2H.

1. **Preparación de la máquina virtual de Azure**. En este documento se supone que la máquina virtual está totalmente implementada. Por lo tanto, una vez migrados los discos de datos, el host de destino puede iniciar y conectar inmediatamente la aplicación. El estado de los datos será el mismo que el que tenían en el origen al apagarlos hace unos segundos. CMC no migra el disco del sistema operativo del origen al destino.

1. **Preparación de la aplicación en la máquina virtual de Azure**. En este ejemplo, el origen es el host de Linux. Puede ejecutar cualquier aplicación de usuario que acceda al almacenamiento de BSD correspondiente. Se usará una aplicación de base de datos que se ejecute en el origen mediante un disco de 1 GiB como dispositivo de almacenamiento de origen. No obstante, se puede usar cualquier aplicación. Configure una máquina virtual de Azure lista para usarse como máquina virtual de destino. Asegúrese de que la configuración de los recursos y el sistema operativo sean compatibles con la aplicación y estén listos para recibir la migración desde el origen mediante el portal de CMC. Los dispositivos de almacenamiento en bloque de destino se asignarán y crearán automáticamente durante el proceso de migración.

1. **Registro para obtener una cuenta de CMC**. Para hacerse con una cuenta de CMC, siga la página de soporte técnico a fin de consultar las instrucciones exactas sobre cómo obtener una cuenta. Puede leer más información [aquí](https://support.cirrusdata.cloud/en/article/licensing-m4lhll/).

1. **Cree un proyecto de migración** que refleje las características de migración específicas, el tipo, el propietario de la migración y los detalles necesarios para definir las operaciones. 

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-create-project.jpg" alt-text="Captura de pantalla de la creación de un nuevo proyecto":::

1. **Definición de los parámetros del proyecto de migración**. Use el portal basado en web de CMC para configurar la migración y defina los parámetros origen y destino, entre otros.

1. **Instalación de los agentes de CMC de migración en los hosts de origen y de destino**. Con el portal de administración basado en web de CMC, seleccione **Deploy Cirrus Migrate Cloud** (Implementar Cirrus Migrate Cloud) a fin de obtener el comando para **New Installation** (Nueva instalación). Ejecute el comando en la interfaz de la línea de comandos de origen y destino.

1. **Creación de una conexión bidireccional entre los hosts de origen y de destino**. Use la pestaña **H2H** del portal de administración basado en web de CMC y el botón **Create New Connection** (Crear nueva conexión). Seleccione el dispositivo usado por la aplicación, no el dispositivo que usa el sistema operativo Linux.

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-1.jpg" alt-text="Captura de pantalla que muestra la lista de hosts implementados":::

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-2.jpg" alt-text="Captura de pantalla que muestra la lista de conexiones de host a host":::

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-3.jpg" alt-text="Captura de pantalla que muestra la lista de dispositivos migrados":::

1. **Inicie la migración a la máquina virtual de destino** mediante **Migrate Host Volumes** (Migrar volúmenes de host) desde el portal de administración basado en web de CMC. Siga las instrucciones para la ubicación remota. Use el portal de CMC para **Auto allocate destination volumes** (Asignar automáticamente volúmenes de destino) en la parte derecha de la pantalla. 
 
1. A continuación, es necesario agregar las credenciales de Azure para permitir la conectividad y el aprovisionamiento de discos mediante la pestaña **Integrations** (Integraciones) del portal de CMC. Rellene los campos obligatorios con los valores privados de su empresa para Azure: **Nombre de integración**, **Id.de inquilino**, **Id. de aplicación o cliente** y **Secreto**. Presione **Save**(Guardar). 

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-4.jpg" alt-text="Captura de pantalla que muestra la introducción de credenciales de Azure":::

    Para obtener más información sobre la aplicación Azure AD, consulte las [instrucciones detalladas](https://support.cirrusdata.cloud/en/article/creating-an-azure-service-account-for-cirrus-data-cloud-tw2c9n/). Al crear y registrar la aplicación Azure AD para CMC, se habilita la creación automática de discos administrados de Azure en la máquina virtual de destino.

    >[!NOTE]
    >Puesto que seleccionó **Auto allocate destination volumes** (Asignar volúmenes de destino automáticamente) en el paso anterior, no vuelva a presionarlo para una nueva asignación. Si lo hace, se producirá un error. En su lugar, presione **Continue** (Continuar).

## <a name="migration-guide"></a>Guía de migración

Tras presionar **Save** (Guardar) en el paso anterior, aparece la ventana **New Migration Session** (Nueva sesión de migración). Rellene los campos:
   - **Session description** (Descripción de la sesión): proporciona una descripción significativa.
   - **Auto Resync Interval** (Intervalo de resincronización automática): habilita la programación de la migración. 
   - Use iQoS para seleccionar el impacto que tendrá la migración en la producción:
     - **Minimum** (Mínimo) limita la frecuencia de migración al 25 % del ancho de banda disponible.
     - **Moderate** (Moderado) limita la frecuencia de migración al 50 % del ancho de banda disponible.
     - **Aggressive** (Agresivo) limita la frecuencia de migración al 75 % del ancho de banda disponible.
     - **Relentless** (Constante) no limita la migración.

       :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-iqos.jpg" alt-text="Captura de pantalla que muestra las opciones de configuración de iQoS":::

Presione **Create Session** (Crear sesión) para iniciar la migración.

Desde el principio de la sincronización inicial de la migración hasta que cMotion se inicia, no es necesario una interacción del usuario con CMC.  La única excepción es supervisar el progreso. Puede supervisar el estado actual, los volúmenes de la sesión y realizar un seguimiento de los cambios mediante el panel. 

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-1.jpg" alt-text="Captura de pantalla que muestra el progreso de la supervisión":::

Durante la migración, para observar los bloques cambiados en el dispositivo de origen, presione el mapa de datos modificados.  

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-2.jpg" alt-text="Captura de pantalla que muestra el mapa de datos modificados":::

Los detalles de iQoS mostrarán los bloques sincronizados y el estado de la migración. También muestran que no hay ningún impacto en la producción de E/S.

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-3.jpg" alt-text="Captura de pantalla que muestra los detalles del iQoS":::

## <a name="moving-the-workload-to-azure-with-cmotion"></a>Traslado de la carga de trabajo a Azure con cMotion

Una vez que finalice la sincronización inicial, se preparará el traslado de la carga de trabajo del disco de origen al disco administrado de Azure de destino mediante cMotion™.

### <a name="start-cmotion"></a>Inicio de cMotion™

En este momento, los sistemas están preparados para la migración total de cMotion™. 

1. En el portal de CMS, seleccione **Trigger cMotion™** (Desencadenar cMotion™) mediante la sesión para cambiar la carga de trabajo del disco de origen al de destino. Para comprobar si el proceso se ha realizado, puede usar el comando iostat o uno equivalente. Vaya al terminal de la máquina virtual de Azure, ejecute *iostat /dev/<device_name>* (por ejemplo, /dev/sdc) y observe que la aplicación escribe las E/S en el disco de destino de la nube de Azure.

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-4.jpg" alt-text="Captura de pantalla que muestra el estado de supervisión actual":::

En este estado, la carga de trabajo se puede volver a trasladar o cambiar al disco de origen en cualquier momento. Si quiere revertir la máquina virtual de producción, use el botón **Session Actions** (Acciones de sesión) y seleccione la opción **Revert cMotion™** (Revertir cMotion™). Se puede cambiar tantas veces como se quiera mientras la aplicación se ejecute en el host o máquina virtual de origen.

Cuando se necesite la migración final a la máquina virtual de destino, siga estos pasos:
1. Seleccione **Session Actions** (Acciones de sesión).
2. Haga clic en la opción **Finalize Cutover** (Finalizar migración) para "bloquear" la migración a la nueva máquina virtual de Azure y deshabilite la opción para quitar el disco de origen. Detenga cualquier otra aplicación que se ejecute en el host de origen para la migración final del host. 

### <a name="move-the-application-to-the-destination-virtual-machine"></a>Traslado de la aplicación a la máquina virtual de destino

Una vez realizada la migración, la aplicación debe cambiarse a la nueva máquina virtual. Para ello, siga estos pasos:

1. Detenga la aplicación.
2. Desmonte el dispositivo migrado.
3. Monte el nuevo dispositivo migrado en la máquina virtual de Azure. 
4. Inicie la misma aplicación en la máquina virtual de Azure del nuevo disco migrado. 
 
Compruebe que no haya ninguna E/S que vaya a los dispositivos de host de origen mediante la ejecución del comando iostat en el host de origen. La ejecución de este comando en una máquina virtual de Azure mostrará que la E/S se está ejecutando en el terminal de la máquina virtual de Azure.

### <a name="complete-the-migration-session-in-cmc-gui"></a>Finalización de la sesión de migración en la GUI de CMC 

El paso de migración se completó al redirigir todas las E/S a los dispositivos de destino después de desencadenar cMotion™. Ahora puede cerrar la sesión mediante **Session Actions** (Acciones de sesión). Haga clic en **Delete Session** (Eliminar sesión) para cerrar la sesión de migración. Como último paso, quitará los **Agentes de Cirrus Migrate Cloud** del host de origen y de la máquina virtual de Azure. Para realizar la desinstalación, obtenga el **comando Uninstall curl** del botón **Deploy Cirrus Migrate Cloud** (Implementar Cirrus Migrate Cloud). La opción está en la sección **Hosts** del portal. 

Una vez quitados los agentes, la migración se completa. Ahora la aplicación de origen se ejecuta en producción en la máquina virtual de Azure de destino con discos montados localmente.    

## <a name="support"></a>Soporte técnico

### <a name="how-to-open-a-case-with-azure"></a>Procedimiento para abrir un caso en Azure

En [Azure Portal](https://portal.azure.com), busque soporte técnico en la barra de búsqueda de la parte superior. Seleccione **Ayuda y soporte técnico** -> **Nueva solicitud de soporte técnico**.

### <a name="engaging-cirrus-support"></a>Interacción con el soporte técnico de Cirrus

En el portal de CMC, seleccione la pestaña **Help Center** (Centro de ayuda) para ponerse en contacto con el soporte técnico de Cirrus Data Solutions o vaya al [sitio web de CDSI](https://support.cirrusdata.cloud/en/) y cubra una solicitud de soporte técnico.

## <a name="next-steps"></a>Pasos siguientes
- Más información sobre [máquinas virtuales de Azure](/azure/virtual-machines/windows/overview)
- Más información sobre [Azure Managed Disks](/azure/virtual-machines/managed-disks-overview)
- Más información sobre la [migración de almacenamiento](/azure/storage/common/storage-migration-overview)
- [Sitio web de Cirrus Data](https://www.cirrusdata.com/)
- Guías detalladas de [cMotion](https://support.cirrusdata.cloud/en/category/howtos-1un623w/)
