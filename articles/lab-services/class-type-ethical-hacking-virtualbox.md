---
title: Configuración de un laboratorio de piratería ética en VirtualBox con Azure Lab Services | Microsoft Docs
description: Aprenda a configurar un laboratorio mediante Azure Lab Services para enseñar técnicas de piratería ética con VirtualBox.
ms.topic: article
ms.date: 06/11/2021
ms.openlocfilehash: 49db4562a7d541a4c3409a5956a67e3bd0aadef4
ms.sourcegitcommit: 40dfa64d5e220882450d16dcc2ebef186df1699f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113040806"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class-with-virtualbox"></a>Configuración de un laboratorio para impartir una clase de piratería ética con VirtualBox

En este artículo se muestra cómo configurar una clase que se centra en la parte referida al análisis forense de la piratería ética. Las pruebas de penetración son una práctica que usa la comunidad de piratería ética y que se producen cuando alguien intenta obtener acceso al sistema o a la red para mostrar los puntos vulnerables que un atacante malintencionado podría aprovechar.

En una clase de piratería ética, los alumnos pueden aprender técnicas modernas para protegerse frente a los puntos vulnerables. Cada alumno obtiene una máquina virtual host que tiene tres máquinas virtuales anidadas: dos máquinas virtuales con una imagen de [Seed](https://seedsecuritylabs.org/lab_env.html) y otra con una imagen de [Kali Linux](https://www.kali.org/). La máquina virtual Seed se usa para la explotación, mientras que la máquina virtual Kali proporciona acceso a las herramientas necesarias para ejecutar tareas de análisis forense.

Este artículo se divide en dos secciones principales. En la primera sección se explica cómo crear el laboratorio educativo. En la segunda se explica cómo crear la máquina de plantilla con la virtualización anidada habilitada y con las herramientas e imágenes necesarias. En este caso, dos imágenes de Seed y una imagen de Kali Linux en una máquina con [VirtualBox](https://www.virtualbox.org/) habilitado para hospedar las imágenes.

## <a name="lab-configuration"></a>Configuración del laboratorio

Para configurar este laboratorio, para empezar necesita una suscripción de Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar. Una vez que obtenga una suscripción de Azure, puede crear una nueva cuenta de laboratorio en Azure Lab Services o usar una existente. Consulte el siguiente tutorial sobre la creación de una nueva cuenta de laboratorio: [Tutorial de configuración de una cuenta de laboratorio](tutorial-setup-lab-account.md).

Siga [este tutorial](tutorial-setup-classroom-lab.md) para crear un laboratorio y aplique la configuración siguiente:

| Tamaño de la máquina virtual | Imagen |
| -------------------- | ----- |
| Mediano (virtualización anidada) | Windows Server 2019 Datacenter |
| Mediano (virtualización anidada) | Windows 10 |

## <a name="template-machine"></a>Máquina de plantilla

Una vez creada la máquina de plantilla, inicie la máquina y conéctese a ella para completar las tres tareas principales siguientes.

1. Configure la máquina para que use [VirtualBox](https://www.virtualbox.org/) para máquinas virtuales anidadas.
2. Configure la imagen de [Kali](https://www.kali.org/) Linux. Kali es una distribución de Linux que incluye herramientas para pruebas de penetración y auditoría de seguridad.
3. Configure la imagen de Seed. En este ejemplo, se utilizará la imagen [Seed](https://seedsecuritylabs.org/lab_env.html). Esta imagen se crea específicamente para el entrenamiento de seguridad.

En el resto de este artículo se explican los pasos manuales para completar las tareas anteriores.

### <a name="installing-virtualbox"></a>Instalación de VirtualBox

1. Descargue los [paquetes de la plataforma VirtualBox](https://www.virtualbox.org/wiki/Downloads) seleccionando la opción de hosts de Windows.
2. Ejecute el ejecutable de la instalación y use las opciones predeterminadas para completar la instalación.

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Configuración de una máquina virtual anidada con una imagen de Kali Linux

Kali es una distribución de Linux que incluye herramientas para pruebas de penetración y auditoría de seguridad.

1. Descargue la imagen de ova de las [imágenes de VirtualBox de VM Kali Linux](https://www.kali.org/get-kali/#kali-virtual-machines).  Se recomienda la versión de 32 bits, la versión de 64 bits se carga con errores.  Recuerde el nombre de usuario y la contraseña predeterminados que se anotaron en la página de descarga.
2. Abra VirtualBox Manager e [importe la imagen .ova](https://docs.oracle.com/cd/E26217_01/E26796/html/qs-import-vm.html).  Para poder continuar es preciso revisar y aceptar el contrato de licencia de Kali.

>[!Note]
>- La RAM predeterminada de VirtualBox para la máquina virtual de Kali es de 2 GB (2048). Se recomienda aumentar la RAM a un mínimo de 4 GB (4096), o más, en función de sus necesidades.  Esto lo pueden cambiar los alumnos en sus máquinas virtuales.  El cambio del tamaño de la RAM en VirtualBox no cambia el tamaño de la máquina virtual del laboratorio.
>- De forma predeterminada, el disco duro se establece en un límite de 80 GB, pero se asigna dinámicamente.  Las máquinas del servicio de laboratorio están limitadas a 128 GB de espacio en disco duro, por lo que debe tener cuidado de no superar este tamaño de disco.
>- La imagen de Kali tiene la habilitación de USB 2.0, que requiere [Oracle VM VirtualBox Extension Pack](https://www.virtualbox.org/wiki/Downloads) o establezca el controlador USB en 1.0 en la pestaña USB.

### <a name="setup-seed-lab-images"></a>Configuración de imágenes de laboratorio de Seed

1. Descargue y extraiga la [Imagen de máquina virtual de laboratorios SEED](https://seedsecuritylabs.org/labsetup.html).
2. Siga las instrucciones para [crear una máquina virtual en VirtualBox.](https://github.com/seed-labs/seed-labs/blob/master/manuals/vm/seedvm-manual.md)
   Si necesita que varias máquinas virtuales SEED realicen copias del archivo .iso para cada máquina, el uso del mismo archivo .iso para diferentes máquinas no funcionará correctamente.

>[!IMPORTANT] 
>Asegúrese de que todas las máquinas virtuales anidadas estén apagadas antes de publicar la plantilla.  Si se dejan encendidas, puede haber efectos secundarios inesperados, incluso daños en las máquinas virtuales.

## <a name="cost"></a>Coste  

Si desea calcular el costo de este laboratorio, puede usar el ejemplo siguiente:

Para una clase de 25 alumnos con 20 horas de clase programadas y 10 horas de cuota para deberes o tareas, el precio del laboratorio sería:

25 alumnos \* (20 + 10) horas \* 55 unidades de laboratorio \* 0,01 USD por hora = 412,50 USD

>[!IMPORTANT]
>La estimación de costos solo se utiliza con fines de ejemplo. Para conocer los detalles actuales sobre los precios, consulte [Precios de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusión

En este artículo se explican los pasos necesarios para crear un laboratorio para clases de piratería ética. Incluye los pasos para configurar la virtualización anidada para la creación de dos máquinas virtuales dentro de la máquina virtual del host para realizar pruebas de penetración.

## <a name="next-steps"></a>Pasos siguientes

Los pasos siguientes son comunes a la configuración de cualquier laboratorio:

- [Incorporación de usuarios](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Establecimiento de la cuota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Establecimiento de la programación](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Vínculos de registro por correo electrónico para los alumnos](how-to-configure-student-usage.md#send-invitations-to-users)
