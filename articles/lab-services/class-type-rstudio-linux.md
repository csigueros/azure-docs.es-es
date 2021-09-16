---
title: Configuración de un laboratorio con R y RStudio en Linux mediante Azure Lab Services
description: Obtenga información sobre cómo configurar laboratorios para enseñar el R mediante RStudio en Windows.
author: emaher
ms.topic: article
ms.date: 08/25/2021
ms.author: enewman
ms.openlocfilehash: 80a64ce36697db2ac7bf941fdb40ab997e9f700f
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123110103"
---
# <a name="set-up-a-lab-to-teach-r-on-linux"></a>Configuración de un laboratorio para enseñar R en Linux

[R](https://www.r-project.org/about.html) es un lenguaje de código abierto que se usa en computación y gráficos estadísticos.  Se utiliza en el análisis estadístico de la genética, en el procesamiento del lenguaje natural y en el análisis de datos financieros.  R proporciona una experiencia de [línea de comandos interactiva](https://cran.r-project.org/doc/manuals/r-release/R-intro.html#Invoking-R-from-the-command-line).  [RStudio](https://www.rstudio.com/products/rstudio/) es un entorno de desarrollo interactivo (IDE) disponible para el lenguaje R.  La versión gratuita proporciona herramientas de edición de código, una experiencia de depuración integrada y herramientas de desarrollo de paquetes.

Este artículo se centrará únicamente en RStudio y R como bloque de creación para una clase que requiere el uso de computación estadística.  Los tipos de clase [aprendizaje profundo](class-type-deep-learning-natural-language-processing.md) y [Python y Jupyter Notebooks](class-type-jupyter-notebook.md) configuran RStudio de forma diferente.  En cada artículo se describe cómo usar la imagen de Marketplace [Data Science Virtual Machine para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-US/marketplace/apps/microsoft-dsvm.ubuntu-1804), que tiene instaladas muchas [herramientas relacionadas con la ciencia de datos](/azure/machine-learning/data-science-virtual-machine/tools-included), incluido RStudio.  

## <a name="lab-account-configuration"></a>Configuración de la cuenta de laboratorio

Para configurar este laboratorio, para empezar, necesita una suscripción a Azure y una cuenta de laboratorio. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar. Una vez que obtenga una suscripción a Azure, puede crear una nueva cuenta de laboratorio en Azure Lab Services. Para más información sobre la creación de una nueva cuenta de laboratorio, consulte el tutorial sobre la [configuración de una cuenta de laboratorio](./tutorial-setup-lab-account.md). También puede usar una cuenta de laboratorio existente.

### <a name="lab-account-settings"></a>Configuración de la cuenta de laboratorio

Habilite la configuración de la cuenta de laboratorio tal y como se describe en la tabla siguiente. Para obtener más información sobre cómo habilitar imágenes de Azure Marketplace, consulte [Especificación de las imágenes de Azure Marketplace disponibles para los creadores de laboratorios](./specify-marketplace-images.md).

| Configuración de la cuenta de laboratorio | Instructions |
| -------------------- | ----- |
| Imágenes de Marketplace | Ubuntu Server 18.04 LTS |
| [Habilitar red virtual del mismo nivel](how-to-connect-peer-virtual-network.md) | Use esta opción en los siguientes casos:<ul><li>La clase requiere una instancia compartida de R Server.</li><li>La clase requiere archivos de datos grandes que desea almacenar externamente y no en la máquina virtual del alumno.</li></ul> |

> [!IMPORTANT]
> Si decide habilitar la red virtual del mismo nivel, debe hacerlo antes de crear el laboratorio.

## <a name="lab-configuration"></a>Configuración del laboratorio

Para obtener instrucciones sobre cómo crear un laboratorio y aplicar la configuración necesaria, consulte [Tutorial: Configuración de un laboratorio de clase](tutorial-setup-classroom-lab.md).  Al crear el laboratorio, aplique la siguiente configuración:

| Configuración del laboratorio | Valor y descripción |
| ------------ | ------------------ |
| Tamaño de la máquina virtual | GPU pequeña (proceso)|
| Imagen de la máquina virtual | Ubuntu Server 18.04 LTS |
| Habilitar la conexión a Escritorio remoto | Este valor debe habilitarse si opta por usar RDP.  Este valor no es necesario si elige [X2Go para conectarse a máquinas de laboratorio](how-to-use-remote-desktop-linux-student.md).  Deberá conectarse a la máquina virtual Linux mediante SSH la primera vez e instalar los paquetes RDP/X2Go y GUI.  Para obtener más información, consulte [Habilitación del Escritorio remoto para máquinas virtuales Linux](how-to-enable-remote-desktop-linux.md). |

## <a name="external-resource-configuration"></a>Configuración de recursos externos

Algunas clases requieren que determinados archivos (por ejemplo, los de datos grandes) se almacenen externamente.  Para ver opciones e instrucciones de configuración, consulte [Uso de almacenamiento de archivos externo en Azure Lab Services](how-to-attach-external-storage.md).

Si opta por tener una instancia compartida de R Server para los alumnos, se debe configurar el servidor antes de crear el laboratorio.  Para obtener más información sobre cómo configurar un servidor compartido, consulte [Creación de un laboratorio con un recurso compartido en Azure Lab Services](how-to-create-a-lab-with-shared-resource.md).  Para obtener instrucciones sobre cómo crear una instancia de RStudio Server, consulte [Descarga de RStudio Server para Debian y Ubuntu](https://www.rstudio.com/products/rstudio/download-server/debian-ubuntu/) y [Acceso a RStudio Server de código abierto](https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started).

## <a name="template-configuration"></a>Configuración de plantilla

Una vez creada la plantilla de máquina, inicie la máquina y conéctese a ella para [instalar R](https://docs.rstudio.com/resources/install-r/), [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) y, opcionalmente, [X2Go Server](https://wiki.x2go.org/doku.php/doc:installation:x2goserver).  

En primer lugar, vamos a actualizar apt y los paquetes existentes en la máquina.

```bash
sudo apt update 
sudo apt upgrade
```

### <a name="install-x2go-server"></a>Instalación de X2Go Server

Si opta por usar X2Go, [instale el servidor](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop#install-x2go-server).  Primero deberá [conectarse mediante SSH](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh) para instalar el componente de servidor.  Una vez completado el paso anterior, el resto de la instalación se puede completar después de [conectarse mediante el cliente X2Go](how-to-use-remote-desktop-linux-student.md).

La instalación predeterminada de X2Go no es compatible con RStudio.  Para evitar este problema, actualice el archivo de opciones x2goagent.

1. Edite el archivo `/etc/x2go/x2goagent.options`.  No olvide editar el archivo como sudo.
    1. Quite la marca de comentario de la línea en la que se indica lo siguiente: `X2GO_NXAGENT_DEFAULT_OPTIONS+=" -extension GLX"`
    1. Comente la línea en la que se indica lo siguiente: `X2GO_NXAGENT_DEFAULT_OPTIONS+=" -extension GLX"`
2. Reinicie X2Go Server para que se usen las nuevas opciones.

    ```bash
    sudo systemctl restart x2goserver
    ```

También puede compilar las bibliotecas necesarias siguiendo las instrucciones de [Glx Xlib workaround for X2Go](https://wiki.x2go.org/doku.php/wiki:development:glx-xlib-workaround) (Solución alternativa de Glx Xlib para X2Go).

### <a name="install-r"></a>Instalar R

Hay varias formas de instalar R en la máquina virtual.  Vamos a instalar R desde el repositorio de Comprehensive R Archive Network (CRAN),  que proporciona las versiones más actualizadas de R.   Una vez agregado el repositorio a nuestro equipo, podemos instalar R y muchos otros paquetes relacionados.

Es necesario agregar el repositorio de CRAN. Los comandos se modifican a partir de las instrucciones disponibles en [Ubuntu Packages for R - Brief Instructions](https://cran.rstudio.com/bin/linux/ubuntu/) (Paquetes de Ubuntu para R: instrucciones breves).

```bash
#download helper packages
sudo apt install --no-install-recommends software-properties-common dirmngr
# download and add the signing key (by Michael Rutter) for these repos
sudo wget -q "https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc" -O /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
#add repository
sudo add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran40/"
```

Ya podemos instalar R ejecutando el siguiente comando:

```bash
sudo apt install r-base
```

### <a name="install-rstudio"></a>Instalación de RStudio

Ahora que R está instalado localmente, podemos instalar el IDE de RStudio.  Vamos a instalar la versión gratuita de RStudio Desktop.  Para todas las versiones disponibles, consulte [Descargas de RStudio](https://www.rstudio.com/products/rstudio/download/).

1. [Importe la clave de firma de código](https://www.rstudio.com/code-signing/) para RStudio.

    ```bash
    sudo gpg --keyserver keyserver.ubuntu.com  --recv-keys 3F32EE77E331692F
    ```

2. Descargue el [archivo de paquete de Debian Linux (.deb) de R Studio](https://www.rstudio.com/products/rstudio/download/#download) para Ubuntu.  El archivo tendrá el formato `rstudio-{version}-amd64.deb`.  Por ejemplo:

    ```bash
    export rstudiover="1.4.1717"
    wget --quiet -O rstudio.deb https://download1.rstudio.org/desktop/bionic/amd64/rstudio-$rstudiover-amd64.deb
    ```

3. Use gdebi para instalar RStudio.   Asegúrese de usar la ruta de acceso del archivo para indicar a apt que vamos a instalar un archivo local.

    ```bash
    sudo apt install gdebi-core 
    echo "y" | gdebi rstudio.deb –quiet
    ```

### <a name="cran-packages"></a>Paquetes de CRAN

Es el momento de instalar los [paquetes de CRAN](https://cloud.r-project.org/web/packages/available_packages_by_name.html) que desee.  Primero, agregue el [repositorio "c2d4u" de R 4.0 o una versión posterior](https://cran.rstudio.com/bin/linux/ubuntu/#get-5000-cran-packages).

```bash
sudo add-apt-repository ppa:c2d4u.team/c2d4u4.0+
```

Use el comando `install.packages(“package name”)` en una sesión interactiva de R como se muestra en el artículo [Lista rápida de paquetes de R útiles](https://support.rstudio.com/hc/articles/201057987-Quick-list-of-useful-R-packages).  También puede usar el elemento de menú Tools -> Install Packages (Instalar > Instalar paquetes) de RStudio.

Si necesita ayuda para encontrar un paquete, use la [lista de paquetes por tarea](https://cran.r-project.org/web/views/) o la [lista alfabética de paquetes](https://cloud.r-project.org/web/packages/available_packages_by_name.html).

## <a name="cost"></a>Coste

Veamos un ejemplo de una posible estimación de costos para esta clase.  Supongamos que tiene una clase de 25 alumnos. Cada alumno tiene 20 horas de clase programadas.  Se asignan otras 10 horas de cuota para deberes o asignaciones fuera de las horas de clase programadas para cada alumno.  El tamaño de la máquina virtual que hemos elegido es el de **GPU pequeña (proceso)** , que tiene 139 unidades de laboratorio.

25 alumnos &times; (20 horas programadas + 10 horas de cuota) &times; 139 unidades de laboratorio &times; 0,01 USD por hora = 1042,5 USD

> [!IMPORTANT]
> La estimación de costos solo se utiliza con fines de ejemplo.  Para obtener información sobre los precios actuales, consulte [Precios de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Pasos siguientes

Ahora se puede publicar la imagen de plantilla en el laboratorio. Consulte [Publicación de la plantilla de máquina virtual](how-to-create-manage-template.md#publish-the-template-vm) para obtener más instrucciones.

Cuando configure el laboratorio, consulte los siguientes artículos:

- [Incorporación de usuarios](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Establecimiento de cuotas](how-to-configure-student-usage.md#set-quotas-for-users)
- [Establecimiento de la programación](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Vínculos de registro por correo electrónico para los alumnos](how-to-configure-student-usage.md#send-invitations-to-users)