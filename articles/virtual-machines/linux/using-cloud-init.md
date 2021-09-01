---
title: Información general de la compatibilidad de cloud-init con máquinas virtuales Linux en Azure
description: Información general de las funcionalidades cloud-init para configurar una máquina virtual en tiempo de aprovisionamiento en Azure.
author: srijang
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/29/2021
ms.author: srijangupta
ms.openlocfilehash: be1bf712d91aaaff460ff2dcc4a899f8b8a089be
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741764"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Compatibilidad con cloud-init para máquinas virtuales en Azure
En este artículo se explica la compatibilidad que existe para [cloud-init](https://cloudinit.readthedocs.io) para configurar una máquina virtual (VM) o conjuntos de escalado de máquinas virtuales en el momento del aprovisionamiento en Azure. Estas configuraciones de cloud-init se ejecutan durante el primer arranque una vez que Azure ha aprovisionado los recursos.  

El aprovisionamiento de máquinas virtuales es el proceso en el que Azure pasará los valores de los parámetros de creación de la máquina virtual, como nombre de host, nombre de usuario, contraseña, etc., y los pone a disposición de la máquina virtual cuando se inicia. Un "agente de aprovisionamiento" consumirá esos valores, configurará la máquina virtual y devolverá una notificación cuando se complete. 

Azure admite dos agentes de aprovisionamiento [cloud-init](https://cloudinit.readthedocs.io) y el [agente de Linux de Azure (WALA)](../extensions/agent-linux.md).

## <a name="cloud-init-overview"></a>Introducción a cloud-init
[cloud-init](https://cloudinit.readthedocs.io) es un enfoque ampliamente usado para personalizar una máquina virtual Linux la primera vez que se arranca. Puede usar cloud-init para instalar paquetes y escribir archivos o para configurar los usuarios y la seguridad. Dado que se llama a cloud-init durante el proceso de arranque inicial, no se necesitan pasos adicionales ni agentes para aplicar la configuración.  Para más información sobre cómo dar formato correctamente a los archivos `#cloud-config` u otras entradas, consulte el [sitio de documentación de cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  Los archivos `#cloud-config` son archivos de texto codificados en base64.

cloud-init también funciona entre distribuciones. Por ejemplo, no use **apt-get install** o **yum install** para instalar un paquete. En su lugar, puede definir una lista de paquetes que se van a instalar. cloud-init usará automáticamente la herramienta de administración de paquetes nativos para la distribución de Linux (distro) que seleccione.

Estamos trabajando activamente con nuestros asociados de distribuciones de Linux certificadas para disponer de imágenes con cloud-init habilitado en Azure Marketplace. Estas imágenes harán que las implementaciones y configuraciones de cloud-init funcionen perfectamente con las máquinas virtuales y los conjuntos de escalado de máquinas virtuales. Inicialmente colaboramos con los partners de distribución de Linux aprobados y el nivel superior para garantizar las funciones de cloud-init con el sistema operativo en Azure; posteriormente, los paquetes se actualizan y están disponibles públicamente en los repositorios de paquetes de distribución. 

Hay dos fases para que cloud-init esté disponible para el sistema operativo de distribución de Linux aprobado en Azure, la compatibilidad con paquetes y, después, la compatibilidad con imágenes:
* La "compatibilidad con paquetes de cloud-init en Azure" documenta que paquetes de cloud-init se admiten o están en versión preliminar, por lo que puede usar estos paquetes con el sistema operativo en una imagen personalizada.
* "Imagen preparada para cloud-init" documenta si la imagen ya está configurada para usar cloud-init.


### <a name="canonical"></a>Canonical
| Publicador o versión| Oferta | SKU | Versión | Imagen preparada para cloud-init | compatibilidad con paquetes de cloud-init en Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 20.04 |UbuntuServer |20.04-LTS |latest |sí | sí |
|Canonical 18.04 |UbuntuServer |18.04-LTS |latest |sí | sí |


### <a name="rhel"></a>RHEL
| Publicador o versión| Oferta | SKU | Versión | Imagen preparada para cloud-init | compatibilidad con paquetes de cloud-init en Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Red Hat 7 |RHEL |7.7, 7.8 y 7_9 |latest |sí | Sí |
|RedHat 8 |RHEL |8.1, 8.2, 8_3 y 8_4 |latest |sí | Sí |

* Todas las demás SKU de RedHat a partir de RHEL 7 (versión 7.7) y RHEL 8 (versión 8.1), incluidas las imágenes Gen1 y Gen2, se aprovisionan mediante cloud-init. Las imágenes de RHEL 6 no admiten cloud-init. 


### <a name="centos"></a>CentOS
 Publicador o versión| Oferta | SKU | Versión | Imagen preparada para cloud-init | compatibilidad con paquetes de cloud-init en Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7 |CentOS |7.7, 7.8 y 7.9 |latest |sí | Sí |
|OpenLogic 8 |CentOS |8.1, 8.2 y 8.3 |latest |sí | Sí |

* Todas las demás SKU de CentOS a partir de CentOS 7 (versión 7.7) y CentOS 8 (versión 8.1), incluidas las imágenes Gen1 y Gen2, se aprovisionan mediante cloud-init. Las imágenes de CentOS 6.10, 7.4, 7.5 y 7.6 no admiten cloud-init. 

> [!NOTE]
> OpenLogic ahora es Rogue Wave Software. 



### <a name="oracle"></a>Oracle

 Publicador o versión| Oferta | SKU | Versión | Imagen preparada para cloud-init | compatibilidad con paquetes de cloud-init en Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7 |Oracle Linux |77, 78 y ol79 |latest |sí | Sí |
|Oracle 8 |Oracle Linux |81, ol82, ol83-lvm y ol84-lvm |latest |sí | Sí |

* Todas las demás SKU de Oracle a partir de Oracle 7 (versión 7.7) y Oracle 8 (versión 8.1), incluidas las imágenes Gen1 y Gen2, se aprovisionan mediante cloud-init.


### <a name="suse-sles"></a>SUSE SLES

 Publicador o versión| Oferta | SKU | Versión | Imagen preparada para cloud-init | compatibilidad con paquetes de cloud-init en Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
| SUSE 15 |SLES (SUSE Linux Enterprise Server) |sp1, sp2 y sp3 |latest |sí | Sí |
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sp5 |latest |sí | Sí |

* Todas las demás SKU de SUSE a partir de SLES 15 (sp1) y SLES 12 (sp5), incluidas las imágenes Gen1 y Gen2, se aprovisionan mediante cloud-init.
* Además, estas imágenes también se aprovisionan con cloud-init:


 Publicador o versión| Oferta | SKU / Versión
|:--- |:--- |:---
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sles-{byos/sap/sap-byos}:12-sp4:2020.06.10
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sles-{byos/sap/sap-byos}:12-sp3:2020.06.10
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sles-{byos/sap/sap-byos}:12-sp2:2020.06.10
| SUSE 15 |SLES (SUSE Linux Enterprise Server) |manager-proxy-4-byosgen1:2020.06.10
| SUSE 15 |SLES (SUSE Linux Enterprise Server) |manager-server-4-byos:gen1:2020.06.10


### <a name="debian"></a>Debian
| Publicador o versión | Oferta | SKU | Versión | Imagen preparada para cloud-init | compatibilidad con paquetes de cloud-init en Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
| debian (Gen1) |debian-10 | 10-cloudinit |10:0.20201013.422| sí | sí, compatibilidad desde la versión del paquete: `20.2-2~deb10u1` |
| debian (Gen2) |debian-10 | 10-cloudinit-gen2 |0.20201013.422| sí | sí, compatibilidad desde la versión del paquete: `20.2-2~deb10u1` |


Actualmente, Azure Stack admitirá el aprovisionamiento de imágenes habilitadas para cloud-init.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>¿Cuál es la diferencia entre cloud-init y el agente de Linux (WALA)?
WALA es un agente específico de la plataforma de Azure que se usa para aprovisionar y configurar máquinas virtuales y controlar las [extensiones de Azure](../extensions/features-linux.md). 

Estamos mejorando la tarea de configuración de las máquinas virtuales para usar cloud-init en lugar del agente de Linux con objeto de permitir que los clientes existentes de cloud-init usen sus scripts actuales de cloud-init o que los nuevos clientes se aprovechen de la rica funcionalidad de configuración de cloud-init. Si tiene inversiones existentes en scripts de cloud-init para configurar sistemas Linux, **no se necesitan configuraciones adicionales** para permitir que cloud-init las procese. 

cloud-init no puede procesar las extensiones de Azure, por lo que todavía se requiere WALA en la imagen para procesar extensiones, pero debe tener deshabilitado su código de aprovisionamiento para que las imágenes de distribuciones de Linux aprobadas que se van a convertir en cloud-init tengan WALA instalado y configurado correctamente.

Al crear una máquina virtual, si no se incluye el modificador `--custom-data` de la CLI de Azure en el momento del aprovisionamiento, cloud-init o WALA toma los parámetros de aprovisionamiento de máquina virtual mínimos necesarios para aprovisionar la máquina virtual y completar la implementación con los valores predeterminados.  Si hace referencia a la configuración de cloud-init con el modificador `--custom-data`, el contenido de los datos personalizados estará disponible en cloud-init cuando arranque la máquina virtual.

Las configuraciones de cloud-init que se aplican a las máquinas virtuales no tienen restricciones de tiempo y no producirán un error de implementación porque se cumpla el tiempo de espera. Esto no es cierto para WALA, si cambia los valores predeterminados de WALA para procesar los datos personalizados, no puede superar el tiempo total de aprovisionamiento de la máquina virtual de 40 min; si es así, se producirá un error al crear máquina virtual.

## <a name="cloud-init-vm-provisioning-without-a-udf-driver"></a>Aprovisionamiento de máquinas virtuales de cloud-init sin un controlador UDF  
A partir de cloud-init 21.2, puede usar cloud-init para aprovisionar una máquina virtual en Azure sin un controlador UDF. Si no hay disponible un controlador UDF en la imagen, cloud-init usa los metadatos que están disponibles en Azure Instance Metadata Service para aprovisionar la máquina virtual. Tenga en cuenta que esta opción solo funciona para la clave SSH y los [datos de usuario](../user-data.md). Para pasar una contraseña o datos personalizados a una máquina virtual durante el aprovisionamiento, debe usar un controlador UDF.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Implementación de una máquina Virtual con cloud-init habilitado
La implementación de una máquina virtual con cloud-init habilitado es tan simple como hacer referencia a una distribución con cloud-init habilitado durante la implementación.  Los encargados de mantener la distribución de Linux tienen que elegir habilitar e integrar cloud-init en sus imágenes básicas publicadas de Azure. Cuando haya confirmado que la imagen que quiere implementar tiene habilitado cloud- init, puede usar la CLI de Azure para implementar la imagen. 

El primer paso para implementar esta imagen es crear un grupo de recursos con el comando [az group create](/cli/azure/group). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. 

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

El paso siguiente es crear un archivo en el shell actual, denominado *cloud-init.txt* y pegar la siguiente configuración. Para este ejemplo, cree el archivo en Cloud Shell, no en la máquina local. Puede utilizar el editor que prefiera. Escriba `sensible-editor cloud-init.txt` para crear el archivo y ver una lista de editores disponibles. Elija el número 1 para utilizar el editor **nano**. Asegúrese de que todo el archivo cloud-init se copia correctamente, especialmente la primera línea:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
> [!NOTE]
> cloud-init tiene varios [tipos de entrada](https://cloudinit.readthedocs.io/en/latest/topics/format.html), cloud-init usará la primera línea de CustomData/UserData para indicar cómo debe procesar la entrada; por ejemplo, `#cloud-config` indica que el contenido debe procesarse como una configuración de cloud-init.


Presione `ctrl-X` para salir del archivo, escriba `y` para guardar el archivo y presione `enter` para confirmar el nombre del archivo al salir.

El paso final es crear la máquina virtual con el comando [az vm create](/cli/azure/vm). 

En el ejemplo siguiente, se crea una máquina virtual denominada *centos74* y las claves SSH si aún no existen en una ubicación de claves predeterminada. Para utilizar un conjunto específico de claves, utilice la opción `--ssh-key-value`.  Use el parámetro `--custom-data` para pasar su archivo de configuración cloud-init. Proporcione la ruta de acceso completa a la configuración de *cloud-init.txt* si guardó el archivo fuera de su directorio de trabajo actual. 

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Cuando se haya creado la VM, la CLI de Azure mostrará información específica a la implementación. Anote el valor de `publicIpAddress`. Esta dirección se utiliza para tener acceso a la máquina virtual.  Transcurre algo de tiempo hasta que la máquina virtual se crea, los paquetes se instalan y la aplicación se inicia. Hay tareas en segundo plano que continúan ejecutándose después de que la CLI de Azure vuelve a abrir el símbolo del sistema. Puede usar SSH en la VM y usar los pasos descritos en la sección de solución de problemas para ver los registros de cloud-init. 

También puede implementar una máquina virtual con cloud-init habilitado pasando los [parámetros en la plantilla de Resource Manager](../../azure-resource-manager/templates/deploy-cli.md#inline-parameters).

## <a name="troubleshooting-cloud-init"></a>Solución de problemas de cloud-init
Una vez que se ha aprovisionado la máquina virtual, cloud-init se ejecuta en todos los módulos y en el script definido en `--custom-data` para configurar dicha máquina virtual.  Si tiene que solucionar algún error u omisión de la configuración, debe buscar el nombre del módulo (`disk_setup` o `runcmd`,0 por ejemplo) en el registro de cloud-init, ubicado en **/var/log/cloud-init.log**.

> [!NOTE]
> No todos los errores de módulo dan como resultado un error irrecuperable en la configuración global de cloud-init. Por ejemplo, si se usa el módulo `runcmd` y se produce un error en el script, cloud-init seguirá notificando un aprovisionamiento correcto porque se ejecutó el módulo runcmd.

Para más detalles sobre el registro de cloud-init, consulte la [documentación de cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html). 

## <a name="next-steps"></a>Pasos siguientes

[Solución de problemas con cloud-init](cloud-init-troubleshooting.md)


Para ejemplos de cloud-init de cambios de configuración, vea los siguientes documentos:
 
- [Incorporación de otro usuario de Linux a una máquina virtual](cloudinit-add-user.md)
- [Ejecución de un administrador de paquetes para actualizar los existentes durante el primer arranque](cloudinit-update-vm.md)
- [Cambio del nombre de host de la máquina virtual local](cloudinit-update-vm-hostname.md) 
- [Instalación de un paquete de aplicación, actualización de los archivos de configuración e inserción de claves](tutorial-automate-vm-deployment.md)
