---
title: Configuración de un servidor SMT para SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Aprenda a configurar un servidor SMT para SAP HANA en Azure (instancias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/25/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b8793e5d8badc6ef19462651e1e039cdae1e8bac
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2021
ms.locfileid: "112981383"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Configuración de servidor SMT para SUSE Linux

En este artículo, se describirán los pasos de configuración de un servidor SMT para SAP HANA en Azure (instancias grandes), también conocido como Infraestructura BareMetal.

Las instancias grandes de SAP HANA no tienen conectividad directa a Internet. No es un proceso sencillo registrar una de estas unidades con el proveedor de sistema operativo y descargar y aplicar las actualizaciones. Una solución para SUSE Linux podría consistir en configurar un servidor SMT en una máquina virtual (VM) de Azure. Hospedará la máquina virtual en una red virtual de Azure conectada a una instancia grande de HANA (HLI). Con este tipo de servidor SMT, la instancia grande de HANA podría registrar y descargar las actualizaciones. 

Para obtener más información sobre SUSE, vea [Herramienta de administración de suscripciones para SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

## <a name="prerequisites"></a>Requisitos previos

Para instalar un servidor SMT para HANA (instancias grandes), primero necesitará:

- Una red virtual de Azure conectada al circuito ExpressRoute de HANA (instancias grandes).
- Una cuenta de SUSE asociada a una organización. La organización debe tener una suscripción válida de SUSE.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Instalación de un servidor SMT en una máquina virtual de Azure

1. Inicie sesión en el [Centro de servicios al cliente de SUSE](https://scc.suse.com/). Vaya a **Organization** > **Organization Credentials** (Organización - Credenciales de organización). En esa sección debería encontrar las credenciales necesarias para configurar el servidor SMT.

2. Instale una máquina virtual SUSE Linux en la red virtual de Azure. Para implementar la máquina virtual, tome una imagen de SLES 12 SP2 de la galería de Azure (seleccione la imagen SUSE BYOS). En el proceso de implementación, no defina un nombre DNS y no use direcciones IP estáticas.

    ![Captura de pantalla de la implementación de la máquina virtual para el servidor SMT.](./media/hana-installation/image3_vm_deployment.png)

    La máquina virtual implementada tiene la dirección IP interna 10.34.1.4 en la red virtual de Azure. El nombre de la máquina virtual es *smtserver*. Después de la instalación, compruebe la conectividad con HANA (instancias grandes). En función de cómo haya organizado la resolución de nombres, es posible que tenga que configurar la resolución de HANA (instancias grandes) en etc/hosts de la máquina virtual de Azure. 

3. Agregue un disco a la máquina virtual. Usará este disco para almacenar las actualizaciones; el propio disco de arranque podría ser demasiado pequeño. En este caso, el disco se ha montado en /srv/www/htdocs, como se muestra en la captura de pantalla siguiente. Un disco de 100 GB debería ser suficiente.

    ![Captura de pantalla que muestra el disco agregado en la ventana de PuTTy.](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

4. Inicie sesión en HANA (instancias grandes) y mantenga /etc/hosts. Compruebe si puede acceder a la máquina virtual de Azure que ejecutará el servidor SMT por la red.

5. Inicie sesión en la máquina virtual de Azure que ejecutará el servidor SMT. Si usa putty para iniciar sesión en la máquina virtual, ejecute esta secuencia de comandos en la ventana de Bash:

    ```
    cd ~
    echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
    ```

6. Reinicie el bash para activar la configuración. Después, inicie YAST.

7. Conecte su máquina virtual (smtserver) al sitio de SUSE.

    ```
    smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
    Registered SLES_SAP 12.2 x86_64
    To server: https://scc.suse.com
    Using E-Mail: email address
    Successfully registered system.
    ```
    
8. Una vez que la máquina virtual esté conectada al sitio de SUSE, instale los paquetes de smt. Use el siguiente comando putty para instalar los paquetes smt.

    ```
    smtserver:~ # zypper in smt
    Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
    Loading repository data...
    Reading installed packages...
    Resolving package dependencies...
    ```
    
    También puede usar la herramienta YAST para instalar los paquetes smt. En YAST, vaya a **Mantenimiento de software** y busque smt. Seleccione **smt**, que cambia automáticamente a yast2-smt.

    ![Captura de pantalla de SMT en YAST.](./media/hana-installation/image5_smt_in_yast.PNG)

    Acepte la selección para la instalación en el servidor SMT. 


9. Una vez finalizada la instalación, vaya a la configuración del servidor SMT. Escriba las credenciales de la organización que recuperó anteriormente desde el Centro de servicios al cliente de SUSE. Escriba también el nombre de host de la máquina virtual de Azure y la dirección URL del servidor SMT. En este ejemplo es https:\//smtserver.

    ![Captura de pantalla de la configuración del servidor SMT.](./media/hana-installation/image6_configuration_of_smtserver1.png)

10. Ahora, compruebe si funciona la conexión al Centro de servicios al cliente de SUSE. Como se ve en la captura de pantalla siguiente, en este ejemplo ha funcionado.

    ![Captura de pantalla de la conexión de prueba al Centro de servicios al cliente de SUSE.](./media/hana-installation/image7_test_connect.png)

11. Una vez que se inicie el programa de instalación de SMT, proporcione una contraseña de base de datos. Dado que es una instalación nueva, debe definir esa contraseña como se muestra en la captura de pantalla siguiente.

    ![Captura de pantalla de definición de la contraseña de la base de datos.](./media/hana-installation/image8_define_db_passwd.PNG)

12. Cree un certificado.

    ![Captura de pantalla de la creación de un certificado para el servidor SMT.](./media/hana-installation/image9_certificate_creation.PNG)

    Al final de la configuración, puede que tarde unos minutos en ejecutarse la comprobación de sincronización. Después de la instalación y configuración del servidor SMT, debería encontrar el repositorio de directorio bajo el punto de montaje /srv/www/htdocs/. También hay algunos subdirectorios en el repositorio. 

13. Reinicie el servidor SMT y sus servicios relacionados con estos comandos.

    ```
    rcsmt restart
    systemctl restart smt.service
    systemctl restart apache2
    ```

## <a name="download-packages-onto-the-smt-server"></a>Descarga de paquetes en el servidor SMT

1. Después de reiniciar todos los servicios, seleccione los paquetes adecuados en la administración de SMT con Yast. La selección de paquetes depende de la imagen del sistema operativo del servidor de HANA (instancias grandes). La selección de paquetes no depende de la versión de SLES o de la versión de la máquina virtual que ejecuta el servidor SMT. La siguiente captura de pantalla le muestra un ejemplo de la pantalla de selección.

    ![Captura de pantalla de la selección de paquetes.](./media/hana-installation/image10_select_packages.PNG)

2. Inicie la copia inicial de los paquetes seleccionados para el servidor SMT que ha configurado. Esta copia se desencadena en el shell mediante el comando smt-mirror.

    ![Captura de pantalla de descarga de paquetes al servidor SMT](./media/hana-installation/image11_download_packages.PNG)

    Los paquetes se deben copiar en los directorios creados bajo el punto de montaje /srv/www/htdocs. Este proceso puede tardar una hora o más, según la cantidad de paquetes que seleccione. Cuando finalice este proceso, pase a la configuración del cliente SMT. 

## <a name="set-up-the-smt-client-on-hana-large-instances"></a>Configuración del cliente SMT en HANA (instancias grandes)

En este caso, los clientes son las instancias grandes de HANA. El programa de instalación del servidor SMT copió el script clientSetup4SMT.sh en la máquina virtual de Azure. 

Copie ese script en la instancia grande de HANA que quiere conectar al servidor SMT. Inicie el script con la opción -h y asígnele como parámetro el nombre del servidor SMT. En este ejemplo, el nombre es *smtserver*.

![Captura de pantalla de la configuración del cliente SMT.](./media/hana-installation/image12_configure_client.PNG)

Es posible que la carga del certificado desde el servidor por parte del cliente se realice correctamente. Pero en este ejemplo, se produce un error en el registro, como se muestra en la siguiente captura de pantalla.

![Captura de pantalla de error de registro del cliente.](./media/hana-installation/image13_registration_failed.PNG)

Si se produce un error en el registro, consulte el [documento de soporte de SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024) y ejecute los pasos que se describen.

> [!IMPORTANT] 
> Como nombre del servidor, proporcione el nombre de la máquina virtual, en este caso *smtserver*, sin el nombre de dominio completo. 
    
Después de realizar estos pasos, ejecute el comando siguiente en HANA (instancias grandes):
    
```
SUSEConnect –cleanup
```

> [!Note] 
> Espere unos minutos después de ese paso. Si ejecuta clientSetup4SMT.sh inmediatamente, podría producirse un error.

Si se produce un problema que tiene que corregir según los pasos descritos en el artículo de SUSE, reinicie clientSetup4SMT.sh en HANA (instancias grandes). Ahora debería finalizar correctamente.

![Captura de pantalla de la finalización correcta del registro de cliente.](./media/hana-installation/image14_finish_client_config.PNG)

Ha configurado el cliente SMT de HLI para conectarse con el servidor SMT instalado en la máquina virtual de Azure. Ahora, use "zypper up" o "zypper in" para instalar actualizaciones del sistema operativo en HANA (instancias grandes) o instale otros paquetes. Solo puede obtener las actualizaciones que haya descargado previamente en el servidor SMT.

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre cómo migrar SAP HANA en Azure (instancias grandes) a máquinas virtuales de Azure.

> [!div class="nextstepaction"]
> [Migración de SAP HANA en instancias grandes de Azure a máquinas virtuales de Azure](hana-large-instance-virtual-machine-migration.md)
