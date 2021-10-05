---
title: Implementación de aplicaciones virtuales Bitnami
description: Obtenga información sobre las aplicaciones virtuales empaquetadas por Bitnami para su implementación en una nube privada de Azure VMware Solution.
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 41201eddaa2ba14110f36c18350860efd4d3a93e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699493"
---
# <a name="bitnami-appliance-deployment"></a>Implementación de dispositivos Bitnami

Bitnami de VMware ofrece un catálogo amplio de aplicaciones virtuales inmediatas. Puede implementar cualquier dispositivo de Bitnami compatible con vSphere que encuentre disponible en [VMware Marketplace](https://marketplace.cloud.vmware.com/), incluidos muchos de los proyectos de software de código abierto más comunes.

En este artículo, aprenderá a instalar y configurar las aplicaciones virtuales empaquetadas por Bitnami siguientes en una nube privada de Azure VMware Solution:

- LAMP

- Jenkins

- PostgreSQL

- NGINX

- RabbitMQ



## <a name="prerequisites"></a>Requisitos previos

- Nube privada de Azure VMware Solution [implementada con un mínimo de tres nodos](deploy-azure-vmware-solution.md).

- Redes configuradas tal como se describe en [Lista de comprobación del planeamiento red](tutorial-network-checklist.md).



## <a name="step-1-download-the-bitnami-virtual-appliance-ovaovf-file"></a>Paso 1. Descarga del archivo OVA/OVF de la aplicación virtual Bitnami


1. Vaya a [VMware Marketplace](https://marketplace.cloud.vmware.com/) y descargue la aplicación virtual que desea instalar en la nube privada de Azure VMware Solution:

   - [Aplicación virtual LAMP empaquetada por Bitnami](https://marketplace.cloud.vmware.com/services/details/lampstack?slug=true)

   - [Jenkins](https://marketplace.cloud.vmware.com/services/details/jenkins?slug=true)

   - [PostgreSQL](https://marketplace.cloud.vmware.com/services/details/postgresql?slug=true)

   - [NGINX](https://marketplace.cloud.vmware.com/services/details/nginxstack?slug=true)

   - [RabbitMQ](https://marketplace.cloud.vmware.com/services/details/rabbitmq?slug=true)

1. Seleccione la versión, **Descargar** y, luego, acepte la licencia del CLUF. 

   >[!NOTE]
   >Asegúrese de que se pueda acceder al archivo desde la máquina virtual.



## <a name="step-2-access-the-local-vcenter-of-your-private-cloud"></a>Paso 2. Conexión a la instancia local de vCenter de la nube privada

1. Inicie sesión en [Azure Portal](https://portal.azure.com), seleccione la nube privada y, luego, **Administrar** > **Identidad**.

1. Copie la dirección URL, el nombre de usuario y la contraseña de vCenter. Los usará para acceder a la máquina virtual (VM). 

1. Seleccione **Información general**, la máquina virtual y, luego, conéctese a ella a través de RDP. Si necesita ayuda para conectarse, consulte los detalles en [Conexión a una máquina virtual](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine).

1. En la máquina virtual, abra un explorador y vaya a la dirección URL de vCenter. 

1. Inicie sesión con las credenciales de usuario de `cloudadmin@vsphere.local` que copió anteriormente.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Captura de pantalla que la página de inicio de sesión de VMware vSphere." border="true":::




## <a name="step-3-install-the-bitnami-ovaovf-file-in-vcenter"></a>Paso 3. Instalación del archivo OVA/OVF de Bitnami en vCenter

1. Haga clic con el botón derecho en el clúster en el que quiere instalar la aplicación virtual LAMP y seleccione **Implementar plantilla de OVF**.

1. Seleccione **Archivo local** y vaya al archivo OVF que descargó anteriormente. Luego, seleccione **Siguiente**.

1. Seleccione el centro de datos y proporcione un nombre para la VM de la aplicación virtual, por ejemplo, **bitnami-lampstack**. Luego, seleccione **Siguiente**.

1. Seleccione el host ESXi como el recurso de proceso para ejecutar la VM y seleccione **Siguiente**.

1. Revise los detalles y seleccione **Siguiente**.

1. Acepte el contrato de licencia y seleccione **Siguiente**.

1. Seleccione el almacenamiento de la VM y, luego, seleccione **Siguiente**.

1. Elija la red de destino de la VM y, luego, seleccione **Siguiente**.

1. Proporcione la información necesaria para personalizar la plantilla, como las propiedades de red y la máquina virtual. Luego, seleccione **Siguiente**.  

1. Revise los valores de configuración y seleccione **Finalizar**.

1. Desde la **Consola de tareas**, compruebe que el estado de la implementación de la plantilla de OVF se completó correctamente.

1. Cuando finalice la instalación, en **Acciones**, seleccione **Encender** para encender el dispositivo. 

1. En la consola de vCenter, seleccione **Iniciar consola web** e inicie sesión en la aplicación virtual Bitnami. Revise la [documentación de soporte técnico de la aplicación virtual Bitnami](https://docs.bitnami.com/vmware-marketplace/faq/get-started/find-credentials/) para conocer el nombre de usuario y la contraseña predeterminados.

   >[!NOTE]
   >Puede cambiar la contraseña predeterminada por una más segura. Si desea más información, consulte …



## <a name="step-4-assign-a-static-ip-to-the-virtual-appliance"></a>Paso 4. Asignación de una dirección IP estática a la aplicación virtual

En este paso, modificará los parámetros *bootproto* y *onboot* y asignará una dirección IP estática a la aplicación virtual Bitnami. 

1. Busque el archivo de configuración de red. 

   ```bash
   sudo find /etc -name \*ens160\*
   ```

1. Edite el archivo *\/etc\/sysconfig\/network-scripts\/ifcfg-ens160* y modifique los parámetros de arranque. Luego, agregue la dirección IP estática, la dirección de la máscara de red y la de la puerta de enlace.

   - bootproto=static

   - onboot=yes


1. Consulte y confirme los cambios realizados en el archivo **ifcfg-ens160**.

   ```bash
   cat ifcfg-ens160  
   ```

1. Reinicie el servicio de red. Esto detiene primero los servicios de red y, luego, aplica la configuración de IP. 

   ```bash
   sudo systemctl restart network
   ```

1. Haga ping a la dirección IP de la puerta de enlace para comprobar la configuración y que la máquina virtual esté conectada a la red.

1. Confirme que aparece la ruta predeterminada 0.0.0.0.

   ```bash
   sudo route -n
   ```



## <a name="step-5-enable-ssh-access-to-the-virtual-appliance"></a>Paso 5. Habilitación del acceso SSH a la aplicación virtual

En este paso, habilitará SSH en la aplicación virtual para el control de acceso remoto. El servicio SSH está deshabilitado de manera predeterminada. También usará PuTTy para conectarse a la consola de host.

1. Habilite e inicie el servicio SSH.

   ```bash
   sudo rm /etc/ssh/sshd_not_to_be_run
   sudo systemctl enable sshd
   sudo systemctl start sshd
   ```

1. Edite el archivo *\/etc\/ssh\/sshd_config* para cambiar la autenticación de contraseña.

   ```bash
   PasswordAuthentication yes
   ```

1. Consulte y confirme los cambios realizados en el archivo **sshd_config**.

   ```bash
   sudo cat sshd_config
   ```

1. Recargue los cambios hechos en el archivo. 

   ```bash
   sudo /etc/init.d/ssh force-reload
   ```

1. Abra PuTTY, seleccione la opción **SSH** y proporcione el nombre de host y **22* para el puerto. A continuación, seleccione **Abrir**. 

1. En el símbolo del sistema de la consola de la aplicación virtual, escriba el nombre de usuario y la contraseña de Bitnami para conectarse al host. 



