---
title: Configuración de un laboratorio para enseñar MATLAB con Azure Lab Services | Microsoft Docs
description: Aprenda a configurar un laboratorio para enseñar MATLAB con Azure Lab Services.
author: emaher
ms.topic: how-to
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 8df8963f705fa8314ae3314af194f834deb69bdc
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176802"
---
# <a name="setup-a-lab-to-teach-matlab"></a>Configuración de un laboratorio para enseñar MATLAB

[MATLAB](https://www.mathworks.com/products/matlab.html), que es la abreviatura de laboratorio de matrices, es una plataforma de programación de [MathWorks](https://www.mathworks.com/).  Combina la potencia del cálculo con una buena visualización, lo que lo convierte en una herramienta popular en los campos de matemáticas, ingeniería, física y química.

Si usa una [licencia para todo el campus](https://www.mathworks.com/academia/tah-support-program/administrators.html), consulte las instrucciones en [Descargar archivos de instalación de MATLAB](https://www.mathworks.com/matlabcentral/answers/259632-how-can-i-get-matlab-installation-files-for-use-on-an-offline-machine) para descargar los archivos del instalador de MATLAB en la plantilla de máquina virtual.  

En este artículo, le mostraremos cómo configurar una clase que use el software cliente de MATLAB con un servidor de licencias.

## <a name="license-server"></a>Servidor de licencias

Antes de modificar la plantilla de máquina virtual del laboratorio, deberá configurar el servidor para que ejecute el software [Network License Manager](https://www.mathworks.com/help/install/administer-network-licenses.html).  Estas instrucciones solo se aplican a las instituciones que eligen la opción de licencias de red para MATLAB, la cual permite a los usuarios compartir un grupo de claves de licencia.  También deberá guardar el archivo de licencia y la clave de instalación de archivos para más adelante.  Para obtener instrucciones detalladas sobre cómo descargar un archivo de licencia, consulte el primer paso del artículo [Instalación de Network License Manager con conexión a Internet](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html).

Las instrucciones detalladas para la instalación de un servidor de licencias están disponibles en [Instalación de Network License Manager con conexión a Internet](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html).  Para permitir los préstamos, consulte el artículo [Pedir prestada una licencia](https://www.mathworks.com/help/install/license/borrow-licenses.html).

Suponiendo que el servidor de licencias se encuentra en una red local o en una red privada dentro de Azure, no olvide [emparejar la red virtual](how-to-connect-peer-virtual-network.md) a su [cuenta de laboratorio](tutorial-setup-lab-account.md).  El emparejamiento de redes debe realizarse antes de crear el laboratorio para que las máquinas virtuales de este puedan acceder al servidor de licencias.

## <a name="lab-configuration"></a>Configuración del laboratorio

Para configurar este laboratorio, para empezar necesita una suscripción de Azure.  Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar. Una vez que obtenga una suscripción de Azure, puede crear una nueva cuenta de laboratorio en Azure Lab Services o usar una existente.  Para crear una nueva cuenta de laboratorio, consulte el [tutorial de configuración de una cuenta de laboratorio](tutorial-setup-lab-account.md).

Para crear un nuevo laboratorio, complete el [tutorial de configuración de un laboratorio educativo](tutorial-setup-classroom-lab.md).  Aplique los valores siguientes:

| Tamaño de la máquina virtual | Imagen |
| -------------------- | ----- |
| Media | Windows 10 |

MATLAB es compatible con más sistemas operativos.  Consulte [Requisitos de sistema de MATLAB](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) para más información.

> [!WARNING]
> No olvide [emparejar la red virtual](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) de la cuenta de laboratorio a la red virtual del servidor de licencias antes de crear el laboratorio.

## <a name="template-machine"></a>Máquina de plantilla

Una vez creada la plantilla de máquina virtual, inicie la máquina y conéctese a ella para completar las tareas principales siguientes.

1. Descargue los archivos de instalación del software cliente de MATLAB.
2. Instale MATLAB con la clave de instalación del archivo.

La instalación de MATLAB será un proceso de varias partes.  La primera parte descargará los archivos de MATLAB y cualquier otro producto que desee instalar.  El uso de una clave de instalación de archivos requiere que todos los archivos de instalación de los productos que se van a instalar se hayan descargado previamente.  En la segunda parte se instalará el software de MATLAB en la plantilla de máquina virtual y se activará el software.  Si la plantilla de máquina virtual está configurada para activarse mediante el servidor de licencias, las máquinas virtuales de los alumnos harán lo mismo.

### <a name="download-installation-files"></a>Descarga de los archivos de instalación

Debe ser administrador de licencias para descargar los archivos de instalación, así como para obtener el archivo de licencia y la clave de instalación de archivos.  A continuación se indican los pasos para descargar los archivos de instalación.

1. Inicie sesión en su cuenta en [https://www.mathworks.com](https://www.mathworks.com).
2. Seleccione **Mi cuenta**.
3. En la sección **Mi software** de la página de la cuenta, haga clic en la licencia asociada a la configuración de Network License Manager para el laboratorio.
4. En la página de detalles de la licencia, haga clic en **Descargar productos**.
5. Espere a que el instalador se extraiga automáticamente.
6. Inicie el programa de instalación.  
7. En la página **Iniciar sesión en su cuenta de MathWorks**, especifique la información de su cuenta de MathWorks.
8. En la página **Contrato de licencia de MathWorks**, acepte las condiciones y haga clic en el botón **Siguiente**.
9. Haga clic en la lista desplegable **Opciones avanzadas** y elija **Deseo descargar sin instalar**.
10. En la página **Seleccionar carpeta de destino**, haga clic en **Siguiente**.
11. Seleccione **Windows** como plataforma del equipo en el que va a instalar MATLAB.
12. En la página **Seleccionar producto**, asegúrese de que se selecciona MATLAB junto con cualquier otro producto de MathWorks que quiera instalar.
13. En la página **Confirmar selecciones y descargar**, haga clic en **Empezar la descarga**.  
14. Espere a que se descarguen los productos seleccionados.  Haga clic en **Finalizar**

También puede descargar una imagen ISO desde el sitio web de MathWorks.

1. Inicie sesión en su cuenta en [https://www.mathworks.com](https://www.mathworks.com).
2. Vaya a [https://www.mathworks.com/downloads](https://www.mathworks.com/downloads).
3. Seleccione la versión de MATLAB que desea instalar.
4. Haga clic en el vínculo "Obtener {version}.iso image" que aparece debajo de los vínculos relacionados donde {version} es algo parecido a R2020a.
5. Haga clic en el vínculo azul de la **versión de descarga** para Windows.

### <a name="run-installer"></a>Ejecución del instalador

Una vez descargados los archivos, el segundo paso es ejecutar el instalador. Una vez más, debe ser administrador de licencias para completar este paso.  Solo los administradores de licencias pueden instalar MATLAB con una clave de instalación de archivos.

1. Revise el archivo de licencia descargado y compruebe que la línea SERVER muestra el servidor de licencias correctamente.  Para más información sobre cómo se debe dar formato al archivo de licencia, consulte los artículos sobre [actualización de licencias de red](https://www.mathworks.com/help/install/ug/network-license-files.html), [préstamos de licencias](https://www.mathworks.com/help/install/license/borrow-licenses.html) y [búsqueda del identificador de host](https://www.mathworks.com/matlabcentral/answers/101892-what-is-a-host-id-how-do-i-find-my-host-id-in-order-to-activate-my-license).
2. Inicie el instalador de MATLAB.
3. En la página **Iniciar sesión en su cuenta de MathWorks**, especifique la información de su cuenta de MathWorks.
4. En la página **Contrato de licencia de MathWorks**, acepte las condiciones y haga clic en el botón **Siguiente**.
5. Haga clic en la lista desplegable **Opciones avanzadas** y elija **Tengo una clave de instalación de archivos**.
6. En la página **Instalar mediante clave de instalación de archivos**, escriba la clave del servidor de licencias.   Haga clic en **Next**.
7. En la página **Seleccionar archivo de licencia**, desplácese hasta el archivo de licencia guardado al descargar los archivos de instalación anteriores.
8. En la página **Seleccionar carpeta de destino**, haga clic en **Siguiente**.
9. En la página **Selección de productos**, haga clic en **Siguiente**.
10. En la página **Seleccionar opciones**, haga clic en **Siguiente**.
11. En la página **Confirmar selecciones e instalar**, haga clic en **Empezar la instalación**.
12. En la página **Instalación finalizada**, compruebe que la opción **Activar MATLAB** está seleccionada.  Haga clic en **Finalizar**

## <a name="cost-estimate"></a>Estimación del costo

Vamos a cubrir una posible estimación de costos para esta clase.  Esta estimación no incluye el costo de ejecutar el servidor de licencias.  Usaremos una clase de 25 alumnos.  Hay 20 horas de tiempo de clase programado.  Además, cada alumno obtiene una cuota de 10 horas para deberes o tareas fuera del tiempo de clase programado.  El tamaño de la máquina virtual que elegimos es el medio, que tiene 55 unidades de laboratorio.

A continuación se incluye un ejemplo de una posible estimación del costo para esta clase:

25 alumnos \* (20 horas programadas + 10 horas de cuota) \* 55 unidades de laboratorio \* 0,01 USD por hora = 412,50 USD

>[!IMPORTANT]
> La estimación de costos solo se utiliza con fines de ejemplo.  Para conocer los detalles actuales sobre los precios, consulte [Precios de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Pasos siguientes

Los pasos siguientes son comunes a la configuración de cualquier laboratorio.

- [Creación, administración y publicación de una plantilla](how-to-create-manage-template.md)
- [Incorporación de usuarios](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Establecimiento de la cuota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Establecimiento de la programación](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Vínculos de registro por correo electrónico para los alumnos](how-to-configure-student-usage.md#send-invitations-to-users)
