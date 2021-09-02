---
title: 'Referencia: Problemas conocidos y soluciones'
titleSuffix: Azure Data Science Virtual  Machine
description: Obtenga una lista de los problemas conocidos y soluciones para Azure Data Science Virtual Machine
services: machine-learning
ms.service: data-science-vm
author: michalmar
ms.author: mimarusa
ms.topic: reference
ms.date: 08/02/2021
ms.openlocfilehash: c928dfa133172f83c097aa9df7d92486524f62ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729806"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Problemas conocidos y solución de problemas de Azure Data Science Virtual Machine

Este artículo lo ayuda a encontrar y corregir errores que podría tener al usar Azure Data Science Virtual Machine.


## <a name="ubuntu"></a>Ubuntu

### <a name="fix-gpu-on-nvidia-a100-gpu-chip---azure-ndasrv4-series"></a>Corrección de GPU en el chip GPU NVIDIA A100: serie NDasrv4 de Azure 

La máquina virtual de la serie ND A100 v4 es una nueva incorporación insignia a la familia de GPU de Azure, diseñada para el entrenamiento de aprendizaje profundo de alto nivel y para cargas de trabajo de HPC de escalabilidad vertical y horizontal estrechamente acopladas.

Debido a una arquitectura diferente, requiere una configuración distinta para que las cargas de trabajo de gran demanda se beneficien de la aceleración de GPU mediante marcos de TensorFlow o PyTorch.

Estamos trabajando para admitir las GPU de máquinas A100 de ND de forma automática. Mientras tanto, puede hacer que la GPU funcione agregando Fabric Manager de NVIDIA y actualizando controladores. 

Siga estos sencillos pasos mientras se encuentra en Terminal:

1. Agregue el repositorio de NVIDIA para instalar o actualizar controladores: puede encontrar instrucciones paso a paso [aquí](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html#ubuntu-lts)
2. [OPCIONAL] También puede actualizar los controladores CUDA (desde el repositorio anterior)
3. Instale los controladores de Fabric Manager de NVIDIA:

    ```
    sudo apt-get install cuda-drivers-460
    sudo apt-get install cuda-drivers-fabricmanager-460
    ```

4. Reinicie la máquina virtual (para preparar los controladores)
5. Habilite e inicie el servicio NVIDIA Fabric Manager recién instalado:

    ```
    sudo systemctl enable nvidia-fabricmanager
    sudo systemctl start nvidia-fabricmanager
    ```

Ahora puede comprobar los controladores y el funcionamiento de la GPU mediante la ejecución de:
```
systemctl status nvidia-fabricmanager.service
``` 

Después, debería ver el servicio Fabric Manager que ejecuta ![ nvidia-fabric-manager-status](./media/nvidia-fabricmanager-status-ok-marked.png)


### <a name="connection-to-desktop-environment-fails"></a>Se produce un error en la conexión al entorno de escritorio

Si puede conectarse a DSVM a través del terminal SSH pero no a través de x2go, es posible que haya establecido un tipo de sesión incorrecto en x2go.
Para conectarse al entorno de escritorio de DSVM, necesita que el tipo de sesión en *x2go/session preferences/session* se establezca en *XFCE*. Actualmente no se admiten otros entornos de escritorio.

### <a name="fonts-look-wrong-when-connecting-to-dsvm-using-x2go"></a>Las fuentes no se ven bien al conectarse a DSVM mediante x2go

Cuando se conecta a x2go y algunas fuentes no se ven bien, podría estar relacionado con una configuración de sesión en x2go. Antes de conectarse a DSVM, desactive la casilla "Establecer DPI de visualización" en la pestaña "Entrada/salida" del cuadro de diálogo de preferencias de sesión.

### <a name="prompted-for-unknown-password"></a>Se le pide una contraseña desconocida

Al crear un valor de DSVM que establece *tipo de autenticación* en *Clave pública SSH* (que se recomienda), no se le dará una contraseña. Sin embargo, en algunos escenarios, algunas aplicaciones seguirán solicitando una contraseña. Ejecute `sudo passwd <user_name>` para crear una nueva contraseña para un usuario concreto. Con `sudo passwd`, puede crear una nueva contraseña para el usuario raíz.

La ejecución de este comando no cambiará la configuración de SSH y los mecanismos de inicio de sesión permitidos se mantendrán iguales. 

### <a name="prompted-for-password-when-running-sudo-command"></a>Se solicita una contraseña al ejecutar el comando sudo

Al ejecutar un comando `sudo` en una máquina Ubuntu, es posible que se le pida que vuelva a escribir la contraseña varias veces para confirmar que realmente es el usuario que ha iniciado sesión. Se espera este comportamiento y es el predeterminado en Ubuntu. Sin embargo, en algunos escenarios, una autenticación repetida no es necesaria y es bastante molesta.

Para deshabilitar la reautenticación en la mayoría de los casos, puede ejecutar el siguiente comando en un terminal.

 `echo -e "\n$USER ALL=(ALL) NOPASSWD: ALL\n" | sudo tee -a /etc/sudoers`

Después de reiniciar el terminal, sudo no pedirá otro inicio de sesión y considerará que la autenticación del inicio de sesión es suficiente.

### <a name="cannot-use-docker-as-non-root-user"></a>No se puede usar Docker como usuario no raíz

Para usar Docker como usuario no raíz, el usuario debe ser miembro del grupo de Docker. Puede ejecutar el comando `getent group docker` para comprobar qué usuarios pertenecen a ese grupo. Para agregar el usuario al grupo de Docker, ejecute `sudo usermod -aG docker $USER`.

### <a name="docker-containers-cannot-interact-with-the-outside-via-network"></a>Los contenedores de Docker no pueden interactuar con el exterior a través de la red

De forma predeterminada, Docker agrega nuevos contenedores a la llamada "red de puente", que es `172.17.0.0/16`. Si la subred de esa red de puente se superpone con la subred de la DSVM o con otra subred privada que tenga en la suscripción, no es posible que haya ninguna comunicación de red entre el host y el contenedor. En ese caso, no se puede acceder a las aplicaciones web que se ejecutan en el contenedor y el contenedor no puede actualizar paquetes de apt.

Para corregir el problema, debe volver a configurar Docker para usar un espacio de direcciones IP para su red de puente que no se superponga con otras redes de la suscripción. Por ejemplo, si se agrega

```json
"default-address-pools": [
        {
            "base": "10.255.248.0/21",
            "size": 21
        }
    ]
```

al documento JSON contenido en el archivo `/etc/docker/daemon.json`, Docker asignará otra subred a la red de puente. (El archivo debe editarse mediante sudo, por ejemplo mediante la ejecución de `sudo nano /etc/docker/daemon.json`).

Después del cambio, el servicio Docker debe reiniciarse mediante la ejecución de `service docker restart`.

Para comprobar si los cambios han tenido efecto, puede ejecutar `docker network inspect bridge`. El valor de *IPAM.Config.Subnet* debe corresponder al grupo de direcciones especificado anteriormente.

### <a name="gpus-not-available-in-docker-container"></a>GPU no disponibles en el contenedor de Docker

El Docker instalado en DSVM admite GPU de forma predeterminada. Sin embargo, hay algunos requisitos previos que deben cumplirse.

* Obviamente, el tamaño de máquina virtual de DSVM debe incluir al menos una GPU.
* Al iniciar el contenedor de Docker con `docker run`, debe agregar un parámetro *--gpus*, por ejemplo, `--gpus all`.
* Los tamaños de máquina virtual que incluyen GPU de NVIDIA A100 necesitan tener instalados paquetes de software adicionales, como [NVIDIA Fabric Manager](https://docs.nvidia.com/datacenter/tesla/pdf/fabric-manager-user-guide.pdf). Es posible que estos paquetes aún no estén instalados en la imagen.


## <a name="windows"></a>Windows

### <a name="accessing-sql-server"></a>Acceso a SQL Server

Al intentar conectarse a la instancia de SQL Server preinstalada, podría producirse un error del tipo "Error de inicio de sesión". Para conectarse correctamente a la instancia de SQL Server, debe ejecutar el programa con el que se está conectando, por ejemplo, SQL Server Management Studio (SSMS), en modo de administrador. El modo de administrador es necesario porque, según el comportamiento predeterminado de DSVM, solo los administradores pueden conectarse.

### <a name="hyper-v-does-not-work"></a>Hyper-V no funciona

Que Hyper-V no funcione inicialmente en Windows es el comportamiento esperado. Para el rendimiento de arranque, hemos deshabilitado algunos servicios.
Para habilitar Hyper-V:

1. Abra la barra de búsqueda en el DSVM de Windows.
1. Escriba "Servicios".
1. Establezca todos los servicios de Hyper-V en "Manual".
1. Establezca "Administración de máquinas virtuales de Hyper-V" en "Automático".

La pantalla final debería tener este aspecto:

   

![Habilitar Hyper-V](./media/workaround/hyperv-enable-dsvm.png)
