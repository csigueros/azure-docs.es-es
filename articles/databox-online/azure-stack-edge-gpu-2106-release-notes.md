---
title: Notas de la versión 2106 de Azure Stack Edge
description: Aquí se describen las incidencias críticas pendientes y las resoluciones de Azure Stack Edge, versión 2106.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 07/19/2021
ms.author: alkohli
ms.openlocfilehash: 81c6f9eb55a44be49cfdbde5e171ce431f4f3cfd
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481784"
---
# <a name="azure-stack-edge-2106-release-notes"></a>Notas de la versión 2106 de Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En las notas de la versión siguientes se identifican las incidencias críticas pendientes y las incidencias resueltas de la versión 2106 de los dispositivos Azure Stack Edge. Estas notas de la versión se aplican a los dispositivos de Azure Stack Edge Pro con GPU, Azure Stack Edge Pro R y Azure Stack Edge Mini R. Las características y las incidencias que corresponden a un modelo específico se indican siempre que proceda.

Las notas de la versión se actualizan continuamente y se van agregando a medida que se descubren problemas críticos que requieren una solución alternativa. Antes de implementar el dispositivo, revise detenidamente la información que encontrará en las notas de la versión.

Este artículo se aplica a la versión **Azure Stack Edge 2106**, correspondiente al número de versión de software **2.2.1636.3457**. Este software se puede aplicar a su dispositivo si ejecuta como mínimo la versión 2010 del software Azure Stack Edge (2.1.1377.2170).

## <a name="whats-new"></a>Novedades

Ya están disponibles las características nuevas siguientes en la versión 2106 de Azure Stack Edge. 

- **Actualizaciones y correcciones de seguridad de Windows**: la [actualización acumulativa más reciente (LCU) de Windows y las correcciones de seguridad de junio](https://support.microsoft.com/en-us/topic/june-8-2021-kb5003697-monthly-rollup-457aa997-18a0-46e9-8612-497f01ccaa54) se incluyeron en el paquete de actualizaciones de Azure Stack Edge.
- **Correcciones de errores para el proceso perimetral multiacceso privado de Azure**: se corrigieron varias incidencias en las implementaciones del MEC privado de Azure.

    - Problemas relacionados con la supervisión de estado de la máquina virtual invitada, como la oscilación de vínculos, los errores en el registro de arranque y los reinicios.
    - Consumo de recursos de memoria a lo largo del tiempo.
    - Controlador, firmware y herramientas de Mellanox.
    - Herramientas para depurar problemas relacionados con la máquina virtual y la comprobación del estado de la red.
    - Problemas que provocaron que se eliminaran los paquetes salientes de VM de virtualización de E/S de raíz única (SR-IOV) o el tráfico de NetAdapters de máquina virtual LAN/WAN.
- **Mejoras en la recopilación de registros**: esta versión presentan mejoras en la recopilación de registros relacionadas con escenarios de actualización de Azure Stack Edge.



## <a name="issues-fixed-in-2106-release"></a>Problemas corregidos en la versión 2106

En la tabla siguiente se enumeran los problemas detectados en versiones anteriores y corregidos en la versión actual.

| No. | Característica | Problema | 
| --- | --- | --- |
|**1.**|MEC privado de Azure |El estado del vínculo del adaptador de red de VM oscila en tiempo de arranque y de manera periódica.|
|**2.**|MEC privado de Azure  |La marca de redireccionamiento DHCP de VFtoPF cuando se usa en interfaces de red de Mellanox puede hacer que se eliminen los paquetes.|
|**3.**|MEC privado de Azure  |El controlador, el firmware y las herramientas de la interfaz de red de Mellanox se deben actualizar a la versión 2.60.|
|**4.**|máquina virtual |El cmdlet `Get-VMInguestLogs` disponible para la recopilación de registros de invitado de máquina virtual genera un error al conectarse a través de la interfaz de PowerShell del dispositivo.|
|**5.**|MEC privado de Azure  |Cuando se configura el proxy web, la configuración de omisión del proxy web provoca un error de aprovisionamiento de máquinas virtuales. |
|**6.**|MEC privado de Azure |En el caso de las implementaciones de MEC/NFM anteriores a la actualización 2105, es posible que se enfrente a este problema poco frecuente en el que se descarta el tráfico de NetAdapters de máquina virtual LAN/WAN. En la actualización 2106, se corrigió este problema al establecer enableIPForwarding en True en las interfaces de red LAN/WAN de la máquina virtual, independientemente de si las máquinas virtuales se crearon antes o después de la versión 2105.   |
|**7.**|MEC privado de Azure  |Las interfaces de red de Mellanox (puerto 5 y puerto 6 en el dispositivo) pueden descartar los paquetes salientes de la máquina virtual de virtualización de E/S de raíz única (SR-IOV) cuando se usa una combinación del controlador Mellanox, las funciones virtuales (VF) de SR-IOV y la característica vftopfDHCPRedirect. En la versión  2106, se corrige el problema al deshabilitar la característica vftopfDHCPRedirect.  |


## <a name="known-issues-in-2106-release"></a>Problemas conocidos de la versión 2106

En la tabla siguiente se proporciona un resumen de los problemas conocidos de la versión 2106.

| No. | Característica | Problema | Soluciones alternativas o comentarios |
| --- | --- | --- | --- |
|**1.**|Características en vista previa |Para esta versión, todas las características siguientes están disponibles en versión preliminar: Azure Resource Manager local, máquinas virtuales, administración en la nube de las máquinas virtuales, administración en la nube de Kubernetes, Kubernetes habilitado para Azure Arc, VPN para Azure Stack Edge Pro R y Azure Stack Edge Mini R, servicio de varios procesos (MPS) y Multi-Access Edge Computing para Azure Stack Edge Pro con GPU.  |Estas características estarán disponibles con carácter general en versiones posteriores. |


## <a name="known-issues-from-previous-releases"></a>Problemas conocidos de las versiones anteriores

En la tabla siguiente se proporciona un resumen de los problemas conocidos que se arrastran de las versiones anteriores.

| No. | Característica | Problema | Soluciones alternativas o comentarios |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro y Azure SQL | La creación de una base de datos SQL requiere acceso de administrador.   |Realice los pasos siguientes en lugar de los pasos 1 y 2 en [Create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database). <ul><li>En la interfaz de usuario local del dispositivo, habilite la interfaz de proceso. Seleccione **Proceso > Número de puerto > Habilitar para el proceso > Aplicar**.</li><li>Descargue `sqlcmd` en la máquina cliente desde la [utilidad de comandos de SQL](/sql/tools/sqlcmd-utility). </li><li>Conéctese a la dirección IP de la interfaz de proceso (el puerto que se habilitó), agregando ",1401" al final de la dirección.</li><li>El comando final tendrá el siguiente aspecto: sqlcmd -S {IP de interfaz},1401-U SA -P "Con1raseña!Segura".</li>Tras este cambio, los pasos 3 y 4 de la documentación actual deben ser idénticos. </li></ul> |
| **2.** |Actualizar| No se admiten los cambios incrementales en los blobs restaurados mediante la funcionalidad de **actualización**. |En los puntos de conexión de blob, las actualizaciones parciales de los blobs después de la funcionalidad de actualización pueden dar lugar a que las actualizaciones no se carguen en la nube. Por ejemplo, supongamos una secuencia de acciones como la siguiente:<ul><li>Crear un blob en la nube. O bien, eliminar del dispositivo un blob cargado previamente.</li><li>Actualizar el blob desde la nube en el dispositivo mediante la funcionalidad de actualización.</li><li>Actualizar solo una parte del blob mediante las API REST de Azure SDK.</li></ul>Estas acciones pueden dar lugar a que las secciones actualizadas del blob no se actualicen en la nube. <br>**Solución alternativa**: use herramientas como robocopy o la copia normal de archivos con el Explorador o la línea de comandos para reemplazar los blobs completos.|
|**3.**|Limitaciones|En una limitación, si no se permiten nuevas escrituras en el dispositivo, las escrituras realizadas por el cliente NFS generarán el error "Permiso denegado".| El error se mostrará como se indica a continuación:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: no se puede crear el directorio 'test': Permiso denegado|
|**4.**|Ingesta de Blob Storage|Si usa AzCopy versión 10 para la ingesta de Blob Storage, ejecute AzCopy con el siguiente argumento: `Azcopy <other arguments> --cap-mbps 2000`.| Si no se proporcionan estos límites para AzCopy, se podría enviar un gran número de solicitudes al dispositivo, lo que provocaría problemas con el servicio.|
|**5.**|Cuentas de almacenamiento en capas|Cuando se usan cuentas de almacenamiento en capas, se aplica lo siguiente:<ul><li> Solo se admiten blobs en bloques. No se admiten los blobs en páginas.</li><li>No hay compatibilidad con la API de copia o instantánea.</li><li> No se admite la ingesta de cargas de trabajo de Hadoop mediante `distcp`, ya que utiliza la operación de copia de forma intensiva.</li></ul>||
|**6.**|Conexión de recurso compartido NFS|Si varios procesos se están copiando en el mismo recurso compartido y no se utiliza el atributo `nolock`, es posible que aparezcan errores durante la copia.|Para copiar archivos en el recurso compartido NFS, se debe usar el atributo `nolock` con el comando mount. Por ejemplo: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Clúster de Kubernetes|Cuando se aplica una actualización en el dispositivo que ejecuta un clúster de Kubernetes, las máquinas virtuales de Kubernetes se reinician. En este caso, solo se restauran automáticamente después de una actualización los pods que se implementan con las réplicas especificadas.  |Si ha creado pods individuales fuera de un controlador de replicación sin especificar un conjunto de réplicas, estos pods no se restaurarán automáticamente después de la actualización del dispositivo. Tendrá que restaurarlos de forma manual.<br>Los pods que se eliminan o finalizan por alguna razón, como un error en un nodo o una actualización del nodo que genere problemas, se sustituyen por conjuntos de réplicas. Por este motivo, es conveniente que utilice un conjunto de réplicas aunque la aplicación solamente necesite un único pod.|
|**8.**|Clúster de Kubernetes|Kubernetes solo se admite en Azure Stack Edge Pro con Helm v3 o una versión posterior. Para más información, consulte las [preguntas frecuentes: eliminación de Tiller](https://v3.helm.sh/docs/faq/).|
|**9.**|Kubernetes habilitado para Azure Arc |En el caso de la versión GA, se ha actualizado Kubernetes habilitado para Azure Arc de la versión 0.1.18 a 0.2.9. Dado que la actualización de Kubernetes habilitado para Azure Arc no es compatible con el dispositivo Azure Stack Edge, tendrá que volver a implementar Kubernetes habilitado para Azure Arc.|Siga estos pasos:<ol><li>[Aplique las actualizaciones del software del dispositivo y Kubernetes](azure-stack-edge-gpu-install-update.md).</li><li>Conéctese a la [interfaz de PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Quite el agente de Azure Arc existente. Escriba: `Remove-HcsKubernetesAzureArcAgent`.</li><li>Implemente [Azure Arc en un nuevo recurso](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). No use un recurso de Azure Arc existente.</li></ol>|
|**10.**|Kubernetes habilitado para Azure Arc|No se admiten implementaciones de Azure Arc si hay un proxy web configurado en el dispositivo Azure Stack Edge Pro.||
|**11.**|Kubernetes |El puerto 31000 está reservado para el panel de Kubernetes. El puerto 31001 está reservado para el registro de contenedor de Edge. De forma similar, en la configuración predeterminada, las direcciones IP 172.28.0.1 y 172.28.0.10 se reservan para el servicio Kubernetes y el servicio DNS principal, respectivamente.|No use las direcciones IP reservadas.|
|**12.**|Kubernetes |Actualmente, Kubernetes no permite servicios LoadBalancer con varios protocolos. Por ejemplo, un servicio DNS que tuviese que escuchar en TCP y UDP. |Para solucionar esta limitación de Kubernetes con MetalLB, se pueden crear dos servicios, uno para TCP y otro para UDP, en el mismo selector del pod. Estos servicios usan la misma clave de uso compartido y el mismo valor de spec.loadBalancerIP para compartir la misma dirección IP. También puede compartir las direcciones IP si tiene más servicios que direcciones IP disponibles. <br> Para obtener más información, consulte [Uso compartido de las direcciones IP](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**13.**|Clúster de Kubernetes|Es posible que los módulos de Azure IoT Edge Marketplace existentes necesiten modificaciones para que se ejecuten en IoT Edge en el dispositivo Azure Stack Edge.|Para más información, consulte el tema sobre modificación de los módulos de Azure IoT Edge de Marketplace para que se ejecuten en un dispositivo Azure Stack Edge.<!-- insert link-->|
|**14.**|Kubernetes |En un dispositivo Azure Stack Edge, no se admiten los montajes de enlace basados en archivos con Azure IoT Edge en Kubernetes.|IoT Edge usa una capa de traducción para traducir las opciones `ContainerCreate` a construcciones de Kubernetes. La creación de asignaciones de `Binds` al directorio `hostpath` y los posteriores montajes de los enlaces basados en archivos no se pueden enlazar a rutas de acceso en los contenedores de IoT Edge. Si es posible, asigne el directorio principal.|
|**15.**|Kubernetes |Si trae sus propios certificados para IoT Edge y los agrega al dispositivo Azure Stack Edge después de que el proceso se haya configurado en el dispositivo, los nuevos certificados no se seleccionarán.|Para solucionar este problema, debe cargar los certificados antes de configurar el proceso en el dispositivo. Si el proceso ya está configurado, [Conéctese a la interfaz de PowerShell del dispositivo y ejecute los comandos de IoT Edge](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Reinicie los pods `iotedged` y `edgehub`.|
|**16.**|Certificados |En determinados casos, el estado del certificado en la interfaz de usuario local puede tardar varios segundos en actualizarse. |Los escenarios siguientes en la interfaz de usuario local pueden verse afectados.<ul><li>La columna **Estado** en la página **Certificados**.</li><li>El icono **Seguridad** en la página de **Introducción**.</li><li>El mosaico **Configuración** en la página de **Información general**.</li></ul>  |
|**17.**|IoT Edge |Los módulos implementados a través de IoT Edge no pueden usar la red de host. | |
|**18.**|Compute + Kubernetes |Compute/Kubernetes no admite el proxy web NTLM. ||
|**19.**|Kubernetes + actualización |Las versiones de software anteriores, como las de 2008, tienen un problema de actualización de condición de carrera que hace que la actualización genere un error con ClusterConnectionException. |El uso de las compilaciones más recientes debería solucionar esta incidencia. Si sigue viendo este problema, vuelva a intentar la actualización y debería funcionar.|
|**20**|Internet Explorer|Si las características de seguridad mejorada están habilitadas, es posible que no pueda acceder a las páginas de la interfaz de usuario web local. | Deshabilite la seguridad mejorada y reinicie el explorador.|
|**21.**|Panel de Kubernetes | No se admite el punto de conexión *HTTPS* del panel de Kubernetes con el certificado SSL. | |
|**22.**|Kubernetes |Kubernetes no admite ":" en los nombres de las variables de entorno que utilizan las aplicaciones .NET. Esto también es necesario para que el módulo de IoT Edge de Event Grid funcione en el dispositivo de Azure Stack Edge y en otras aplicaciones. Para más información, consulte [Documentación de ASP.NET Core](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration#environment-variables).|Reemplace ":" por un guion bajo doble. Para más información, consulte [Incidencia de Kubernetes](https://github.com/kubernetes/kubernetes/issues/53201).|
|**23.** |Azure Arc + clúster de Kubernetes |De manera predeterminada, cuando el recurso `yamls` se elimina del repositorio de Git, no se eliminan los recursos correspondientes del clúster de Kubernetes.  |Para permitir la eliminación de recursos cuando se eliminen del repositorio de Git, establezca `--sync-garbage-collection` en Arc OperatorParams. Para obtener más información, consulte [Eliminación de una configuración](../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md#additional-parameters). |
|**24.**|NFS |Las aplicaciones que usan montajes de recursos compartidos de NFS en el dispositivo para escribir datos deben usar escritura exclusiva. Así se garantiza que las escrituras se realizan en el disco.| |
|**25.**|Configuración del proceso |Se produce un error en la configuración del proceso en configuraciones de red en las que las puertas de enlace o los conmutadores o enrutadores responden a las solicitudes del Protocolo de resolución de direcciones (ARP) para sistemas que no existen en la red.| |
|**26.**|Proceso y Kubernetes |Si Kubernetes se configura en primer lugar en el dispositivo, notifica todas las GPU disponibles. Por lo tanto, no es posible crear VM de Azure Resource Manager mediante GPU después de configurar Kubernetes. |Si el dispositivo tiene dos GPU, puede crear una máquina virtual que use la GPU y, a continuación, configurar Kubernetes. En este caso, Kubernetes usará el resto de la GPU disponible. |
|**27.**|Extensión de script personalizado en las máquinas virtuales |Hay un problema conocido en las máquinas virtuales Windows que se crearon en una versión anterior y el dispositivo se actualizó a la versión 2103. <br> Si agrega una extensión de script personalizado a estas máquinas virtuales, el agente invitado de la máquina virtual Windows (solo la versión 2.7.41491.901) se bloquea en la actualización, lo que hace que se agote el tiempo de espera de la implementación de la extensión. | Para evitar este problema: <ul><li> Conéctese a la máquina virtual Windows mediante el protocolo de escritorio remoto (RDP). </li><li> Asegúrese de que `waappagent.exe` se está ejecutando en la máquina: `Get-Process WaAppAgent`. </li><li> Si `waappagent.exe` no se está ejecutando, reinicie el servicio `rdagent`: `Get-Service RdAgent` \| `Restart-Service`. Espere 5 minutos.</li><li> Mientras `waappagent.exe` se está ejecutando, termine el proceso `WindowsAzureGuest.exe`. </li><li>Después de terminar el proceso, este empieza a ejecutarse de nuevo con la versión más reciente.</li><li>Compruebe que la versión del agente invitado de la máquina virtual Windows es 2.7.41491.971 con este comando: `Get-Process WindowsAzureGuestAgent` \| `fl ProductVersion`.</li><li>[Configure la extensión de script personalizado en una máquina virtual Windows](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md). </li><ul> |
|**28.**|Máquinas virtuales de GPU |Antes de esta versión, el ciclo de vida de la máquina virtual con GPU no se administraba en el flujo de actualización. Por lo tanto, durante la actualización a la versión 2103, las máquinas virtuales con GPU no se detienen automáticamente. Tendrá que detenerlas mediante una marca `stop-stayProvisioned` antes de actualizar el dispositivo. Para más información, consulte [Suspensión o apagado de la VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm).<br> Todas las máquinas virtuales con GPU que se mantengan en ejecución antes de la actualización se inician después de esta. En estos casos, las cargas de trabajo que se ejecutan en las máquinas virtuales no se terminan correctamente y las máquinas virtuales podrían acabar en un estado no deseado después de la actualización. <br>Todas las máquinas virtuales con GPU que se detengan mediante `stop-stayProvisioned` antes de la actualización se inician automáticamente después de esta. <br>Si se detienen las máquinas virtuales con GPU desde Azure Portal, deberá iniciarlas manualmente después de la actualización del dispositivo.| Si ejecuta las máquinas virtuales con GPU con Kubernetes, deténgalas justo antes de la actualización. <br>Cuando se detengan las máquinas virtuales con GPU, Kubernetes tomará las GPU que estas usaban originalmente. <br>Cuanto más tiempo pasen las máquinas virtuales con GPU en estado detenido, mayor será la probabilidad de que Kubernetes asuma las GPU. |
|**29.**|Servicio multiproceso (MPS) |Cuando se actualizan el software del dispositivo y el clúster de Kubernetes, la configuración de MPS para las cargas de trabajo no se conserva.   |[Vuelva a habilitar MPS](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) y a implementar las cargas de trabajo que usaban MPS. |


## <a name="next-steps"></a>Pasos siguientes

- [Actualización de dispositivos](azure-stack-edge-gpu-install-update.md)
