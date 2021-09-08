---
title: Instalación de HANA en SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Aprenda a instalar HANA en SAP HANA en Azure (instancias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/4/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e4e7aec0422d3dfe5772748215c350073155a30
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111559795"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Instalación de HANA en SAP HANA en Azure (instancias grandes)

En este artículo, se describirán los pasos de instalación de HANA en SAP HANA en Azure (instancias grandes) (también conocido como infraestructura sin sistema operativo).

## <a name="prerequisites"></a>Requisitos previos

Para instalar HANA en SAP HANA en Azure (instancias grandes), primero:

- Proporcione a Microsoft todos los datos para realizar la implementación de forma automática en una instancia grande de SAP HANA.
- Reciba la instancia grande de SAP HANA de Microsoft.
- Cree una red virtual de Azure que esté conectada a la red local.
- Conecte el circuito de ExpressRoute para HANA (instancias grandes) a la misma red virtual de Azure.
- Instale una máquina virtual de Azure que se use como jumpbox para HANA (instancias grandes).
- Asegúrese de que se puede conectar desde el jumpbox a la instancia grande de HANA y viceversa.
- Compruebe si están instalados todos los paquetes y las revisiones que se necesitan.
- Lea las notas de SAP y la documentación sobre la instalación de HANA en el sistema operativo que usa. Asegurarse de que la versión de HANA que prefiere es compatible con la versión del sistema operativo.

## <a name="download-the-sap-hana-installation-bits"></a>Descarga de los elementos de instalación de SAP HANA

Ahora vamos a descargar los paquetes de instalación de HANA en la máquina virtual de jumpbox. En este ejemplo, el sistema operativo es Windows.

Las unidades de la instancia grande de HANA no están conectadas directamente a Internet. No puede descargar directamente los paquetes de instalación de SAP a la máquina virtual de la instancia grande de HANA. En su lugar, debe descargar los paquetes a la máquina virtual jump box.

Necesita un usuario S-user de SAP u otro usuario, lo que le permite acceder a SAP Marketplace.

1. Inicie sesión y vaya a [SAP Service Marketplace](https://support.sap.com/en/index.html). Seleccione **Download Software** > **Installations and Upgrade** > **By Alphabetical Index**. Luego seleccione **Under H – SAP HANA Platform Edition** > **SAP HANA Platform Edition 2.0** > **Installation**. Descargue los archivos que se muestran en la captura de pantalla siguiente.

   ![Captura de pantalla de los archivos de instalación de HANA que se deben descargar.](./media/hana-installation/image16_download_hana.PNG)

2. En este ejemplo, descargamos los paquetes de instalación de SAP HANA 2.0. En la máquina virtual jump box de Azure, expanda los archivos autoextraíbles en el directorio tal como se muestra a continuación.

   ![Captura de pantalla del archivo autoextraíble.](./media/hana-installation/image17_extract_hana.PNG)

3. A medida que se extraen los archivos, copie el directorio que crea la extracción (en este caso, 51052030). Copie el directorio desde el volumen /hana/shared de la unidad de instancia grande de HANA a un directorio que haya creado.

   > [!Important]
   > No copie los paquetes de instalación en la raíz ni el LUN de arranque. El espacio es limitado y también lo usan otros procesos.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalar SAP HANA en la unidad de instancia grande de HANA

1. Para instalar SAP HANA, inicie sesión como usuario raíz. Solo la raíz tiene los permisos suficientes para instalar SAP HANA. Establezca permisos en el directorio que copió en /hana/shared.

    ```
    chmod –R 744 <Installation bits folder>
    ```
    
    Para instalar SAP HANA mediante el programa de instalación de la interfaz gráfica de usuario, es necesario instalar el paquete gtk2 en HANA (instancias grandes). Para comprobar si está instalado, ejecute el comando siguiente:
    
    ```
    rpm –qa | grep gtk2
    ```

    (En pasos posteriores, se muestra el programa de instalación de SAP HANA con la interfaz gráfica de usuario).         

2. Vaya al directorio de instalación y navegue al subdirectorio HDB_LCM_LINUX_X86_64. 

    Desde ese directorio, inicie:
    
    ```
    ./hdblcmgui 
    ```
3. En este punto, avanzará a través de una secuencia de pantallas en las que proporcionará los datos para la instalación. En este ejemplo, se instalan los componentes de cliente de SAP HANA y el servidor de bases de datos de SAP HANA. Por tanto, nuestra selección es **Base de datos de SAP HANA**.

    ![Captura de la pantalla de administración del ciclo de vida de SAP HANA, con la base de datos de SAP HANA seleccionada.](./media/hana-installation/image18_hana_selection.PNG)

4. Seleccione **Install new system** (Instalar nuevo sistema).

    ![Captura de la pantalla de administración del ciclo de vida de SAP HANA con la opción Install New System (Instalar nuevo sistema) seleccionada.](./media/hana-installation/image19_select_new.PNG)

5. Seleccione lo que le interese entre los diversos componentes que se pueden instalar.

    ![Captura de la pantalla de administración del ciclo de vida de SAP HANA con una lista de los componentes adicionales](./media/hana-installation/image20_select_components.PNG)

6. Elija SAP HANA Client y SAP HANA Studio. Instale también una instancia de escalado vertical. Luego, seleccione **Single-Host System** (Sistema de host único). 

    ![Captura de la pantalla de administración del ciclo de vida de SAP HANA con la opción Single Host System (Sistema de host único) seleccionada.](./media/hana-installation/image21_single_host.PNG)

7. A continuación, proporcionará algunos datos. Para la ruta de instalación, use el directorio /hana/shared.

    ![Captura de la pantalla de administración del ciclo de vida de SAP HANA con los campos de propiedades del sistema que se van a definir.](./media/hana-installation/image22_provide_sid.PNG)

    > [!Important]
    > Como Id. de sistema de HANA (SID), debe proporcionar el mismo SID que proporcionó a Microsoft cuando solicitó la implementación de la instancia grande de HANA. Elegir un SID distinto hace que se produzca un error en la instalación debido a problemas de permisos de acceso en los diferentes volúmenes.

8. Proporcione las ubicaciones para los archivos de datos y de registro de HANA.

    ![Captura de la pantalla de administración del ciclo de vida de SAP HANA con los campos de área de datos y registro.](./media/hana-installation/image23_provide_log.PNG)

    > [!Note]
    > El SID que especificó al definir las propiedades del sistema (hace dos pantallas) debe coincidir con el SID de los puntos de montaje. Si hay algún error de coincidencia, vuelva atrás y ajuste el SID al valor que tiene en los puntos de montaje.

9. Revise el nombre de host y corríjalo según sea necesario. 

    ![Captura de la pantalla de administración del ciclo de vida de SAP HANA con el nombre de host.](./media/hana-installation/image24_review_host_name.PNG)

10. Recupere los datos que proporcionó a Microsoft cuando solicitó la implementación de la instancia grande de HANA. 

    ![Captura de pantalla de Administración del ciclo de vida de SAP HANA con los campos de administrador del sistema que se van a definir](./media/hana-installation/image25_provide_guid.PNG)

    > [!Important]
    > Proporcione el **identificador de usuario del administrador del sistema** y el **identificador del grupo de usuarios** que proporcionó a Microsoft cuando solicitó la implementación de la unidad. De lo contrario, la instalación de SAP HANA en la unidad de instancia grande de HANA no se realizará correctamente.

11. Las dos pantallas siguientes no se muestran aquí. En ellas puede proporciona la contraseña del usuario SYSTEM de la base de datos de SAP HANA y la contraseña del usuario sapadm. Esta última se usa para el agente de host de SAP que se instala como parte de la instancia de base de datos de SAP HANA.

    Después de definir la contraseña, verá una pantalla de confirmación. Compruebe todos los datos que se muestran y continúe con la instalación. Llegará a una pantalla de progreso similar a esta en la que se documenta el progreso de la instalación:

    ![Captura de la pantalla de administración del ciclo de vida de SAP HANA con los indicadores de progreso de la instalación.](./media/hana-installation/image27_show_progress.PNG)

12. Cuando finalice la instalación, debería ver una pantalla como esta:

    ![Captura de la pantalla de administración del ciclo de vida de SAP HANA que indica que la instalación ha finalizado.](./media/hana-installation/image28_install_finished.PNG)

    La instancia de SAP HANA debe estar en ejecución y lista para su uso. Puede conectarse a ella desde SAP HANA Studio. No olvide buscar y aplicar las actualizaciones más recientes.


## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre la alta disponibilidad y la recuperación ante desastres de SAP HANA en Azure (instancias grandes).

> [!div class="nextstepaction"]
> [Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)](hana-overview-high-availability-disaster-recovery.md)
