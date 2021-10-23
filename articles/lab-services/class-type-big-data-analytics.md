---
title: Configuración de un laboratorio para enseñar el análisis de macrodatos con Azure Lab Services | Microsoft Docs
description: Obtenga información sobre cómo configurar un laboratorio para enseñar el análisis de macrodatos mediante una implementación de Docker de Hortonworks Data Platform (HDP).
author: nicolela
ms.topic: how-to
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 280ed6f2804e7a3aef6da8da9512a59da6e8c5f4
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177049"
---
# <a name="set-up-a-lab-for-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Configuración de un laboratorio para el análisis de macrodatos mediante una implementación de Docker de Hortonworks Data Platform

En este artículo se muestra cómo configurar un laboratorio para enseñar una clase de análisis de macrodatos.  Con este tipo de clase, los alumnos aprenden a manipular grandes volúmenes de datos y a aplicar algoritmos de aprendizaje automático y estadístico para obtener conclusiones sobre los datos.  Un objetivo clave para los alumnos es aprender a usar herramientas de análisis de datos, como el [paquete de software de código abierto de Apache Hadoop](https://hadoop.apache.org/), que proporciona herramientas para almacenar, administrar y procesar macrodatos.

En este laboratorio, los alumnos utilizarán una conocida versión comercial de Hadoop proporcionada por [Cloudera](https://www.cloudera.com/), denominada [Hortonworks Data Platform (HDP)](https://www.cloudera.com/products/hdp.html).  En concreto, los alumnos usarán [HDP Sandbox 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html), que es una versión simplificada y fácil de usar de la plataforma, que es gratuita y está pensada para aprendizaje y experimentación.  Aunque esta clase puede usar máquinas virtuales (VM) Windows o Linux con una instancia de HDP Sandbox implementada, en este artículo se muestra cómo usar Windows.

Otro aspecto interesante de este laboratorio es que implementaremos HDP Sandbox en las VM del laboratorio mediante contenedores de [Docker](https://www.docker.com/).  Cada contenedor de Docker proporciona su propio entorno aislado para que las aplicaciones de software se ejecuten en él.  Conceptualmente, los contenedores de Docker son como VM anidadas, y se pueden usar para implementar y ejecutar fácilmente una amplia variedad de aplicaciones de software basadas en las imágenes de contenedor que se proporcionan en [Docker Hub](https://www.docker.com/products/docker-hub).  El script de implementación de Cloudera para HDP Sandbox extrae automáticamente la [imagen de Docker de HDP Sandbox 3.0.1](https://hub.docker.com/r/hortonworks/sandbox-hdp) de Docker Hub y ejecuta dos contenedores de Docker:
  - sandbox-hdp
  - sandbox-proxy

## <a name="lab-configuration"></a>Configuración del laboratorio

Para configurar este laboratorio, para empezar, necesita una suscripción a Azure y una cuenta de laboratorio. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar. Una vez que obtenga una suscripción a Azure, puede crear una nueva cuenta de laboratorio en Azure Lab Services. Para obtener más información sobre la creación de una nueva cuenta de laboratorio, consulte el [tutorial para configurar una cuenta de laboratorio](tutorial-setup-lab-account.md).  También puede usar una cuenta de laboratorio existente.

### <a name="lab-account-settings"></a>Configuración de la cuenta de laboratorio

Habilite la configuración que se describe en la tabla siguiente para la cuenta de laboratorio. Para obtener más información sobre cómo habilitar imágenes de Marketplace, consulte [Especificación de las imágenes de Marketplace disponibles para los creadores de laboratorios](./specify-marketplace-images.md).

| Configuración de la cuenta de laboratorio | Instructions |
| ------------------- | ------------ |
|Imagen de Marketplace| Habilite la imagen de Windows 10 Pro para su uso en la cuenta de laboratorio.|

### <a name="lab-settings"></a>Configuración del laboratorio

Use las opciones de la tabla siguiente al configurar un laboratorio educativo.  Para obtener más información sobre cómo crear un laboratorio de clase, consulte el [tutorial Configuración de un laboratorio educativo](tutorial-setup-classroom-lab.md).

| Configuración del laboratorio | Valor/instrucciones |
| ------------ | ------------------ |
|Tamaño de la máquina virtual| Mediano (virtualización anidada). Este tamaño de VM es el más adecuado para bases de datos relacionales, análisis y almacenamiento en caché en memoria.  Este tamaño admite la virtualización anidada.|  
|Imagen de máquina virtual| Windows 10 Pro|

> [!NOTE] 
> Es necesario usar Mediano (virtualización anidada), ya que la implementación de HDP Sandbox con Docker requiere lo siguiente:
>   - Windows Hyper-V con virtualización anidada
>   - Al menos 10 GB de RAM.

## <a name="template-machine-configuration"></a>Configuración de la máquina de plantilla

Para configurar la máquina de plantilla, haremos lo siguiente:
- Instalación de Docker
- Implementación de HDP Sandbox
- Uso de PowerShell y el Programador de tareas de Windows para iniciar automáticamente los contenedores de Docker

### <a name="install-docker"></a>Instalación de Docker

Los pasos de esta sección se basan en las [instrucciones de Cloudera para la implementación con contenedores de Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html). 

Para usar contenedores de Docker, primero debe instalar Docker Desktop en la VM de plantilla:

1. Siga los pasos descritos en la [sección de requisitos previos](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) para instalar [Docker para Windows](https://docs.docker.com/docker-for-windows/install/). 

    > [!IMPORTANT] 
    > Asegúrese de que la opción de configuración **Use Windows containers instead of Linux containers** se deje desactivada.

1. Asegúrese de que **las características Contenedores de Windows y Hyper-V** estén activadas.
   ![Activar o desactivar características de Windows](./media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Siga los pasos de la sección [Memory for Windows](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) para configurar la memoria de Docker.

    > [!WARNING]
    > Si por accidente activa la opción **Use Windows containers instead of Linux containers** al instalar Docker, no verá las opciones de configuración de memoria.  Para solucionar esto, puede cambiar al uso de contenedores de Linux [haciendo clic en el icono de Docker en la bandeja del sistema de Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog); cuando se abra el menú de Docker Desktop, seleccione **Switch to Linux containers**.
 
### <a name="deploy-hdp-sandbox"></a>Implementación de HDP Sandbox

En esta sección, implementará HDP Sandbox y, a continuación, accederá también a HDP Sandbox mediante el explorador.

1. Asegúrese de haber instalado [Git Bash](https://gitforwindows.org/) como se muestra en la [sección de requisitos previos](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) de la guía, ya que se recomienda para completar los pasos siguientes.

1. Siguiendo la [guía de implementación e instalación para Docker de Cloudera](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html), complete los pasos de las secciones siguientes:
   
   -    Implementación de HDP Sandbox
   -    Verificación de HDP Sandbox

    > [!WARNING] 
    > Cuando descargue el archivo .zip más reciente para HDP, asegúrese de *no* guardar el archivo .zip en una ruta de acceso del directorio que incluya espacios en blanco.

    > [!NOTE] 
    > Si recibe una excepción durante la implementación que indica **Drive has not been shared**, debe compartir la unidad C con Docker para que los contenedores de Linux de HDP puedan acceder a los archivos locales de Windows.  Para solucionarlo, [haga clic en el icono de Docker en la bandeja del sistema de Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) para abrir el menú de Docker Desktop y seleccione **Settings**.  Cuando se abra **Docker's Settings**, seleccione **Resources > File Sharing** y marque la unidad **C**.  A continuación, puede repetir los pasos para implementar HDP Sandbox.

1. Una vez que los contenedores de Docker para HDP Sandbox se hayan implementado y estén en ejecución, puede acceder al entorno iniciando el explorador y siguiendo las instrucciones de Cloudera para abrir la [página principal de Sandbox](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) e iniciar el panel de HDP.

    > [!NOTE] 
    > En estas instrucciones se da por hecho que primero ha asignado la dirección IP local del entorno de espacio aislado a sandbox-hdp.hortonworks.com en el archivo de host en la VM de la plantilla.  Si **no** realiza esta asignación, puede acceder a la página principal de Sandbox yendo a `http://localhost:8080`.

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Inicio automático de los contenedores de Docker cuando los alumnos inician sesión

Para proporcionar una experiencia fácil de usar para los alumnos, usaremos un script de PowerShell que realice lo siguiente de manera automática:
  - Inicie los contenedores de Docker de HDP Sandbox cuando un alumno inicia una VM de laboratorio y se conecta a ella.
  - Inicie el explorador y navegue a la página principal de Sandbox.
También usaremos el Programador de tareas de Windows para ejecutar automáticamente este script cuando un alumno inicie sesión en la VM.
Para configurar esto, siga estos pasos: [Scripting de análisis de macrodatos](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Estimación del costo

Si desea calcular el costo de este laboratorio, puede usar el ejemplo siguiente.

Para una clase de 25 alumnos con 20 horas de clase programadas y 10 horas de cuota para deberes o tareas, el precio del laboratorio sería:
  - 25 alumnos * (20 + 10) horas * 55 unidades de laboratorio * 0,01 USD por hora = 412,50 USD

Para más detalles sobre los precios, consulte [Precios de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusión

En este artículo se explican los pasos necesarios para crear un laboratorio para una clase de análisis de macrodatos que usa Hortonworks Data Platform implementado con Docker.  La configuración de este tipo de clase se puede usar para clases similares de análisis de datos.  Esta configuración también puede aplicarse a otros tipos de clases que usen Docker para la implementación.

## <a name="next-steps"></a>Pasos siguientes

Los pasos siguientes son comunes a la configuración de cualquier laboratorio.

- [Creación y administración de plantillas](how-to-create-manage-template.md)
- [Incorporación de usuarios](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Establecimiento de la cuota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Establecimiento de la programación](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Vínculos de registro por correo electrónico para los alumnos](how-to-configure-student-usage.md#send-invitations-to-users)