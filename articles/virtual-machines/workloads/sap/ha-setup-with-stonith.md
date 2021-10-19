---
title: Configuración de alta disponibilidad de SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Vea cómo establecer la alta disponibilidad para SAP HANA en Azure (instancias grandes) en SUSE mediante el dispositivo STONITH.
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
ms.date: 9/01/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5866fbb227477b0079f5f2ac314357ca8e67a364
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710849"
---
# <a name="high-availability-setup-in-suse-using-the-stonith-device"></a>Configuración de la alta disponibilidad en SUSE mediante el dispositivo STONITH

En este artículo, vamos a explicar los pasos necesarios para configurar la alta disponibilidad (HA) en HANA (instancias grandes) en el sistema operativo SUSE mediante el dispositivo STONITH.

> [!NOTE]
> Esta guía se obtiene como resultado de probar correctamente la configuración en el entorno de instancias grandes de Microsoft HANA. El equipo de administración de servicios de Microsoft para HANA (instancias grandes) no admite el sistema operativo. Para solucionar problemas o aclarar el nivel del sistema operativo, póngase en contacto con SUSE. 
>
> El equipo de administración de servicios de Microsoft configura y admite totalmente el dispositivo STONITH. Puede ayudar a solucionar problemas de dispositivos STONITH.

## <a name="prerequisites"></a>Prerrequisitos

Para configurar la alta disponibilidad con los clústeres de SUSE, siga estos pasos:

- Aprovisione HANA (instancias grandes).
- Instale y registre el sistema operativo con las revisiones más recientes.
- Conecte los servidores de HANA (instancias grandes) al servidor SMT para obtener revisiones y paquetes.
- Configure el protocolo de tiempo de redes (servidor de tiempo de NTP).
- Lea y comprenda la documentación más reciente de SUSE sobre la configuración de alta disponibilidad.

## <a name="setup-details"></a>Detalles de la configuración

Esta guía utiliza la siguiente configuración:

- Sistema operativo: SLES 12 SP1 para SAP
- Instancias grandes de HANA: 2xS192 (cuatro zócalos, 2 TB)
- Versión de HANA: HANA 2.0 SP1
- Nombres de servidor: sapprdhdb95 (node1) y sapprdhdb96 (node2)
- Dispositivo STONITH: basado en iSCSI
- NTP en uno de los nodos de HANA (instancias grandes)

Si configura HANA (instancias grandes) con la replicación del sistema de HANA, puede solicitar al equipo de administración de servicios de Microsoft que configure el dispositivo STONITH. Debe hacerlo en el momento del aprovisionamiento. 

Si ya es un cliente con HANA (instancias grandes) aprovisionado, también puede solicitar la configuración del dispositivo STONITH. Proporcione la siguiente información al equipo de Microsoft Service Management en el formulario de solicitud de servicio (SRF). Puede obtener el formulario SRF a través del director técnico de su cuenta o de su contacto de Microsoft para la incorporación de HANA (instancias grandes).

- Nombre y dirección IP del servidor (por ejemplo, myhanaserver1 y 10.35.0.1)
- Ubicación (por ejemplo, Este de EE. UU.)
- Nombre del cliente (por ejemplo, Microsoft)
- Identificador del sistema HANA (SID) (por ejemplo, H11)

Una vez configurado el dispositivo STONITH, el equipo de Microsoft Service Management le proporcionará el nombre del dispositivo de bloques STONITH (SBD) y la dirección IP del almacenamiento iSCSI. Puede usar esta información para la configuración de STONITH. 

Siga los pasos de las secciones siguientes para configurar HA mediante STONITH.

## <a name="identify-the-sbd-device"></a>Identificación del dispositivo SBD

> [!NOTE]
> Esta sección solo se aplica a los clientes actuales. Si es un cliente nuevo, el equipo de Microsoft Service Management le dará el nombre del dispositivo SBD, así que puede omitir esta sección.

1. Modifique */etc/iscsi/initiatorname.isci* para: 

    ``` 
    iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
    ```
    
    Microsoft Service Management proporciona esta cadena. Modifique el archivo en *ambos* nodos. Sin embargo, el número de nodo es distinto en cada uno.
    
    ![Captura de pantalla que muestra el archivo initiatorname con los valores de InitiatorName de un nodo.](media/HowToHLI/HASetupWithStonith/initiatorname.png)

2. Defina `node.session.timeo.replacement_timeout=5` y `node.startup = automatic` para modificar */etc/iscsi/iscsid.conf*. Modifique el archivo en *ambos* nodos.

3. Ejecute el comando de detección siguiente en *ambos* nodos.

    ```
    iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
    ```
    
    Los resultados muestran cuatro sesiones.
    
    ![Captura de pantalla que muestra una ventana de consola con los resultados del comando de detección.](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

4. Ejecute el siguiente comando en *ambos* nodos para iniciar sesión en el dispositivo iSCSI. 

    ```
    iscsiadm -m node -l
    ```
    
    Los resultados muestran cuatro sesiones.
    
    ![Captura de pantalla que muestra una ventana de consola con los resultados del comando de nodo.](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

5. Use el siguiente comando para ejecutar el script *rescan-scsi-bus.sh* para volver a examinar. Este script muestra los nuevos discos creados para usted.  Ejecútelo en *ambos* nodos.

    ```
    rescan-scsi-bus.sh
    ```
    
    Los resultados deberían mostrar un número LUN superior a cero (por ejemplo: 1, 2, etc.).
     
    ![Captura de pantalla que muestra una ventana de consola con los resultados del script.](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

6. Para obtener el nombre del dispositivo, ejecute el comando siguiente en *ambos* nodos. 

    ```
      fdisk –l
    ```
    
    En los resultados, elija el dispositivo con el tamaño de 178 MiB.
    
    ![Captura de pantalla que muestra una ventana de consola con los resultados del comando f disk.](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="initialize-the-sbd-device"></a>Inicialización del dispositivo SBD

1. Use el siguiente comando para inicializar el dispositivo SBD en *ambos* nodos.

    ```
    sbd -d <SBD Device Name> create
    ```
    ![Captura de pantalla que muestra una ventana de consola con el resultado del comando s b d create.](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2. Use el siguiente comando en *ambos* nodos para comprobar lo que se ha escrito en el dispositivo.

    ```
    sbd -d <SBD Device Name> dump
    ```

## <a name="configure-the-suse-ha-cluster"></a>Configuración del clúster de HA de SUSE

1. Use el comando siguiente para comprobar si los patrones ha_sles y SAPHanaSR-doc están instalados en *ambos* nodos. En caso negativo, instálelos.

    ```
    zypper in -t pattern ha_sles
    zypper in SAPHanaSR SAPHanaSR-doc
    ```
    ![Captura de pantalla que muestra una ventana de la consola con el resultados del comando del patrón.](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
    
    ![Captura de pantalla que muestra una ventana de la consola con el resultado del comando SAPHanaSR-doc.](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)
    
2. Configure el clúster mediante el comando `ha-cluster-init` o el asistente de yast2. En este ejemplo, se usa el asistente de yast2. Realice este paso solamente en el *nodo principal*.

    1. Vaya a **yast2** > **Alta disponibilidad** > **Clúster**
    
        ![Captura de pantalla que muestra el centro de control de YaST con las opciones Alta disponibilidad y Clúster seleccionadas.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)        
                
    1. En el cuadro de diálogo que aparece sobre la instalación del paquete hawk, seleccione **Cancelar**, ya que el paquete halk2 ya está instalado.
    
        ![Captura de pantalla que muestra un cuadro de diálogo con las opciones Instalar y Cancelar.](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)
                    
    1. En el cuadro de diálogo que aparece, seleccione **Continuar**.
    
        ![Captura de pantalla que muestra un mensaje para continuar sin instalar los paquetes necesarios.](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)        
        
    1. El valor esperado es el número de nodos implementados (en este caso, 2). Seleccione **Next** (Siguiente).  

     
        
    1. Agregue los nombres de nodo y, a continuación, seleccione **Add suggested files** (Agregar archivos sugeridos).

        ![Captura de pantalla que muestra la ventana Cluster Configure (Configuración del clúster) con las listas Sync Host (Host de sincronización) y Sync File (Archivo de sincronización).](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
        
    1. Seleccione **Turn csync2 ON** (Activar csync2).
    
    1. Seleccione **Generate Pre-Shared-Keys** (Generar claves compartidas previamente). 
    
    1. En el mensaje emergente que aparece, seleccione **Aceptar**.
    
        ![Captura de pantalla que muestra un mensaje que indica que se ha generado la clave.](media/HowToHLI/HASetupWithStonith/yast-key-file.png)
        
    1. La autenticación se realiza con las direcciones IP y las claves compartidas previamente en Csync2. El archivo de clave se genera con `csync2 -k /etc/csync2/key_hagroup`. 
    
        Copie manualmente el archivo *key_hagroup* para todos los miembros del clúster después de crearlo. Asegúrese de copiar el archivo de node1 a node2. Luego, seleccione **Siguiente**.
        
        ![Captura de pantalla que muestra un cuadro de diálogo de configuración del clúster con las opciones necesarias para copiar la clave a todos los miembros del clúster.](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)
        
    1. En la opción predeterminada, el **arranque** está **desactivado**. **Actívelo** para que se inicie el servicio Pacemaker con el arranque. Puede elegir según los requisitos de configuración.

        ![Captura de pantalla que muestra la ventana Servicio de clúster con el arranque activado.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)
    
    1. Seleccione **Siguiente** para completar la configuración del clúster.

## <a name="set-up-the-softdog-watchdog"></a>Configuración del guardián Softdog

1. Agregue la siguiente línea a */etc/init.d/boot.local* en *ambos* nodos.
    
    ```
    modprobe softdog
    ```
    ![Captura de pantalla que muestra un archivo de arranque con la línea de softdog agregada.](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)
    
2. Use el comando siguiente para actualizar el archivo */etc/sysconfig/sbd* en *ambos* nodos.
    
    ```
    SBD_DEVICE="<SBD Device Name>"
    ```
    ![Captura de pantalla que muestra el archivo s b d con el valor de S B D_DEVICE agregado.](media/HowToHLI/HASetupWithStonith/sbd-device.png)
    
3. Cargue el módulo de kernel en *ambos* nodos, para lo que debe ejecutar el siguiente comando.
    
    ```
    modprobe softdog
    ```
    ![Captura de pantalla que muestra parte de una ventana de consola con el comando modprobe softdog.](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4. Use el comando siguiente para asegurarse de que softdog se está ejecutando en *ambos* nodos.
    
    ```
    lsmod | grep dog
    ```
    ![Captura de pantalla que muestra parte de una ventana de consola con el resultado de la ejecución del comando l s mod.](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)
    
5. Use el siguiente comando para iniciar el dispositivo SBD en *ambos* nodos.

    ```
    /usr/share/sbd/sbd.sh start
    ```
    ![Captura de pantalla que muestra parte de una ventana de consola con el comando start.](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)
    
6. Use el siguiente comando para probar el demonio de SBD en *ambos* nodos. 
    
    ```
    sbd -d <SBD Device Name> list
    ```
    Los resultados muestran dos entradas después de la configuración en ambos nodos.    
    
    ![Captura de pantalla que muestra parte de una ventana de consola que muestra dos entradas.](media/HowToHLI/HASetupWithStonith/sbd-list.png)
    
7. Envíe el mensaje de prueba siguiente a *uno* de los nodos.

    ```
    sbd  -d <SBD Device Name> message <node2> <message>
    ```
    
8. En el *segundo* nodo (node2), use el siguiente comando para comprobar el estado del mensaje.
    
    ```
    sbd  -d <SBD Device Name> list
    ```
    ![Captura de pantalla que muestra parte de una ventana de consola con uno de los miembros que muestra un valor de prueba para el otro miembro.](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)
    
9. Para adoptar la configuración de SBD, actualice el archivo */etc/sysconfig/sbd* como se indica a continuación en *ambos* nodos.

    ```
    SBD_DEVICE=" <SBD Device Name>" 
    SBD_WATCHDOG="yes" 
    SBD_PACEMAKER="yes" 
    SBD_STARTMODE="clean" 
    SBD_OPTS=""
    ```
10. Use el comando siguiente para iniciar el servicio Pacemaker en el *nodo principal* (node1).

    ```
    systemctl start pacemaker
    ```
    ![Captura de pantalla que muestra una ventana de consola que muestra el estado después de iniciar pacemaker.](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)
    
    Si se produce un error en el servicio Pacemaker, consulte la sección [Escenario 5: Error de servicio Pacemaker](#scenario-5-pacemaker-service-fails) más adelante en este artículo.

## <a name="join-the-node-to-the-cluster"></a>Unión del nodo al clúster

Ejecute el siguiente comando en *node2* para que dicho nodo se una al clúster.

```
ha-cluster-join
```

Si recibe un error al unirse al clúster, consulte la sección [Escenario 6: Node2 no se puede unir al clúster](#scenario-6-node2-cant-join-the-cluster) más adelante en este artículo.

## <a name="validate-the-cluster"></a>Validación del clúster

1. Use los comandos siguientes para comprobar y, opcionalmente, iniciar el clúster por primera vez en *ambos* nodos.
    
     ```
    systemctl status pacemaker
    systemctl start pacemaker
    ```
    ![Captura de pantalla que muestra una ventana de consola con el estado de pacemaker.](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
        
2. Ejecute el siguiente comando para comprobar que *ambos* nodos están en línea. También puede ejecutarlo en *cualquiera de los nodos* del clúster.
    
    ```
    crm_mon
    ```
    ![Captura de pantalla que muestra una ventana de la consola con el resultados del comando c r m_mon.](media/HowToHLI/HASetupWithStonith/crm-mon.png)
    
    También puede iniciar sesión en hawk para comprobar el estado del clúster: `https://\<node IP>:7630`. El usuario predeterminado es **hacluster** y la contraseña es **linux**. Si es necesario, puede cambiar la contraseña con el comando `passwd`.
    
## <a name="configure-cluster-properties-and-resources"></a>Configuración de las propiedades y los recursos del clúster

Esta sección describe los pasos para configurar los recursos del clúster.
En este ejemplo, puede configurar los recursos siguientes. Puede configurar el resto (si es necesario) haciendo referencia a la guía de HA de SUSE.

- Arranque del clúster
- Dispositivo STONITH
- Dirección IP virtual

Realice la configuración solo en el *nodo principal*.

1. Cree el archivo de arranque del clúster y configúrelo agregando el texto siguiente.
    
    ```
    sapprdhdb95:~ # vi crm-bs.txt
    # enter the following to crm-bs.txt
    property $id="cib-bootstrap-options" \
    no-quorum-policy="ignore" \
    stonith-enabled="true" \
    stonith-action="reboot" \
    stonith-timeout="150s"
    rsc_defaults $id="rsc-options" \
    resource-stickiness="1000" \
    migration-threshold="5000"
    op_defaults $id="op-options" \
    timeout="600"
    ```

2. Use el comando siguiente para agregar la configuración al clúster.

    ```
    crm configure load update crm-bs.txt
    ```
    ![Captura de pantalla que muestra parte de una ventana de consola que ejecuta el comando c r m.](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)
    
3. Configure el dispositivo STONITH. Para hacerlo, agregue el recurso, cree el archivo y especifique el texto que se indica a continuación.

    ```
    # vi crm-sbd.txt
    # enter the following to crm-sbd.txt
    primitive stonith-sbd stonith:external/sbd \
    params pcmk_delay_max="15"
    ```
    Use el comando siguiente para agregar la configuración al clúster.
        
    ```
    crm configure load update crm-sbd.txt
    ```
        
4. Para agregar la dirección IP virtual para el recurso, cree el archivo y agregue el texto siguiente.

    ```
    # vi crm-vip.txt
    primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
    operations $id="rsc_ip_HA1_HDB10-operations" \
    op monitor interval="10s" timeout="20s" \
    params ip="10.35.0.197"
    ```
    
    Use el comando siguiente para agregar la configuración al clúster.
    
    ```
    crm configure load update crm-vip.txt
    ```
        
5. Use el comando `crm_mon` para validar los recursos. 

    Los resultados muestran los dos recursos.

    ![Captura de pantalla que muestra una ventana de consola con dos recursos.](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

    También puede ver el estado en *https://\<node IP address>:7630/cib/live/state*.
    
    ![Captura de pantalla que muestra el estado de los dos recursos.](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)
    
## <a name="test-the-failover-process"></a>Prueba del proceso de conmutación por error

1. Para probar el proceso de conmutación por error, use el siguiente comando para detener el servicio Pacemaker en node1.

    ```
    Service pacemaker stop
    ```
    
    Los recursos conmutan por error en node2.

2. Detenga el servicio Pacemaker en node2. Los recursos conmutarán por error en node1.

    Este es el estado antes de la conmutación por error:  
    ![Captura de pantalla que muestra el estado de los dos recursos antes de la conmutación por error.](media/HowToHLI/HASetupWithStonith/Before-failover.png)  
    
    Este es el estado después de la conmutación por error:  
    ![Captura de pantalla que muestra el estado de los dos recursos después de la conmutación por error.](media/HowToHLI/HASetupWithStonith/after-failover.png)
    
    ![Captura de pantalla que muestra una ventana de consola con el estado de los recursos después de la conmutación por error.](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  
    

## <a name="troubleshooting"></a>Solución de problemas

En esta sección se describen los escenarios de error que pueden producirse durante la instalación.

### <a name="scenario-1-cluster-node-not-online"></a>Escenario 1: Nodo de clúster no en línea

Si alguno de los nodos no se muestra en línea en el administrador de clústeres, puede intentar probar el procedimiento siguiente para que esté en línea.

1. Utilice el siguiente comando para iniciar el servicio iSCSI.

    ```
    service iscsid start
    ```
    
2. Use el siguiente comando para iniciar sesión en ese nodo de iSCSI.

    ```
    iscsiadm -m node -l
    ```
    
    La salida esperada tiene el siguiente aspecto:

    ```
    sapprdhdb45:~ # iscsiadm -m node -l
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
    ```
### <a name="scenario-2-yast2-doesnt-show-graphical-view"></a>Escenario 2: Yast2 no muestra la vista gráfica

En este artículo, se usa la pantalla gráfica de yast2 para configurar el clúster de alta disponibilidad. Si yast2 no se abre con la ventana gráfica como se muestra y devuelve un error Qt, siga estos pasos para instalar los paquetes necesarios. Si se abre con la ventana gráfica, puede omitir los pasos.

Este es un ejemplo de error de Qt.

![Captura de pantalla que muestra parte de una ventana de consola con un mensaje de error.](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

Este es un ejemplo de la salida que se espera:

![Captura de pantalla que muestra el centro de control de YaST con la opción de alta disponibilidad y el clúster resaltados.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

1. Asegúrese de haber iniciado sesión como usuario "raíz" y contar con la configuración SMT para descargar e instalar los paquetes.

2. Vaya a **yast** > **Software** > **Software Management** (Administración de software)  > **Dependencias** y seleccione **Instalar paquetes recomendados**. 

    >[!NOTE]
    >Debe realizar los pasos en *ambos* nodos para poder acceder a la vista gráfica de yast2 desde los dos nodos.
    
    La captura de pantalla siguiente muestra la pantalla que se espera.
    
    ![Captura de pantalla que muestra una ventana de consola con el centro de control de YaST.](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)
    
3. En **Dependencias**, seleccione **Instalar paquetes recomendados**.

    ![Captura de pantalla que muestra una ventana de consola con la opción Instalar paquetes recomendados seleccionada.](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

4. Revise los cambios y seleccione **Aceptar**.

    ![Captura de pantalla que muestra una ventana de consola con una lista de paquetes seleccionados para la instalación.](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

    La instalación de paquetes continúa.

    ![Captura de pantalla que muestra una ventana de consola con el progreso de la instalación.](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

5. Seleccione **Next** (Siguiente).    

6. Cuando aparezca la pantalla **Installation Successfully Finished** (Instalación completada correctamente), seleccione **Finalizar**.

    ![Captura de pantalla que muestra una ventana de consola con un mensaje de operación correcta.](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

7. Use los siguientes comandos para instalar los paquetes libqt4 y libyui-qt.
    
    ```
    zypper -n install libqt4
    ```
    ![Captura de pantalla que muestra una ventana de consola que instala el primer paquete.](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
    
    ```
    zypper -n install libyui-qt
    ```
    ![Captura de pantalla que muestra una ventana de consola que instala el segundo paquete.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
    
    ![Captura de pantalla que muestra una ventana de consola que instala el segundo paquete (continuación).](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)
    
    Ahora, Yast2 puede abrir la vista gráfica.

    ![Captura de pantalla que muestra el centro de control de YaST con las opciones de software y actualización en línea seleccionadas.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-doesnt-show-the-high-availability-option"></a>Escenario 3: Yast2 no muestra la opción de alta disponibilidad

Para que la opción de alta disponibilidad esté visible en el centro de control de yast2, debe instalar los otros paquetes.

1. Vaya a **Yast2** > **Software** > **Software Management** (Administración de software). A continuación, seleccione **Software** > **Actualización en línea**.  

    ![Captura de pantalla que muestra el centro de control de YaST con las opciones de software y actualización en línea seleccionadas.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

2. Seleccione patrones para los siguientes elementos. Seleccione **Aceptar**.

    - Base del servidor SAP HANA
    - Compilador C/C++ y herramientas
    - Alta disponibilidad
    - Base del servidor de aplicaciones de SAP

    ![Captura de pantalla que muestra la selección del primer patrón en el elemento para el compilador y las herramientas.](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)

    ![Captura de pantalla que muestra la selección del segundo patrón en el elemento para el compilador y las herramientas.](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

4. En la lista de paquetes que se han cambiado para resolver dependencias, seleccione **Continuar**.

    ![Captura de pantalla que muestra el cuadro de diálogo Changed Packages (Paquetes modificados) con los paquetes modificados para resolver las dependencias.](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

4. En la página de estado **Performing Installation** (Realizando la instalación), seleccione **Siguiente**.

    ![Captura de pantalla que muestra la página de estado de la instalación en ejecución.](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

5. Una vez completada la instalación, aparece un informe de instalación. Seleccione **Finalizar**.

    ![Captura de pantalla que muestra el informe de instalación.](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Escenario 4: HANA no se instala debido a un error en los ensamblados de GCC

Si se produce un error en la instalación de HANA, es posible que reciba el siguiente error.

![Captura de pantalla que muestra un mensaje de error que indica que el sistema operativo no está preparado para realizar ensamblados de g c c 5.](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Para corregir el problema, instale las bibliotecas libgcc_sl y libstdc++6 como se muestra en la captura de pantalla siguiente.

![Captura de pantalla que muestra una ventana de consola que instala las bibliotecas necesarias.](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Escenario 5: Error del servicio Pacemaker

Aparece la siguiente información si el servicio Pacemaker no se puede iniciar.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Para corregirlo, elimine la siguiente línea del archivo */usr/lib/systemd/system/fstrim.timer*:

```
Persistent=true
```
    
![Captura de pantalla que muestra el archivo f s trim con el valor Persistent = true que se va a eliminar.](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node2-cant-join-the-cluster"></a>Escenario 6: Node2 no se puede unir al clúster

Aparece el siguiente error si hay un problema al unir node2 al clúster existente mediante el comando *ha-cluster-join*.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![Captura de pantalla que muestra una ventana de consola con un mensaje de error que indica que las claves SSH no se pueden recuperar de una dirección I P determinada.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Cómo solucionarlo:

1. Ejecute los siguientes comandos en *ambos nodos*.

    ```
    ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
    cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
    ```

    ![Captura de pantalla que muestra parte de una ventana de la consola que ejecuta el comando en el primer nodo.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

    ![Captura de pantalla que muestra parte de una ventana de la consola que ejecuta el comando en el segundo nodo.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

2. Confirme que node2 se ha agregado al clúster.

    ![Captura de pantalla que muestra una ventana de la consola con una ejecución correcta del comando join.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="next-steps"></a>Pasos siguientes

Puede encontrar más información sobre la configuración de alta disponibilidad de SUSE en los siguientes artículos: 

- [Escenario de rendimiento optimizado para la replicación del sistema de SAP HANA](https://www.suse.com/support/kb/doc/?id=000019450) (sitio web de SUSE)
- [Fencing y STONITH](https://documentation.suse.com/sle-ha/15-SP1/html/SLE-HA-all/cha-ha-fencing.html) (sitio web de SUSE)
- [Prepárese para usar el clúster de Pacemaker para SAP HANA: parte 1 (aspectos básicos)](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/), blog de SAP
- [Prepárese para usar el clúster de Pacemaker para SAP HANA: parte 2 (error en ambos nodos)](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/), blog de SAP

Descubra cómo realizar una copia de seguridad y restauración de archivos para un sistema operativo.

> [!div class="nextstepaction"]
> [Copia de seguridad y restauración del sistema operativo](large-instance-os-backup.md)
