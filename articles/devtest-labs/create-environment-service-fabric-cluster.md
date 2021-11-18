---
title: Creación de un entorno de clúster de Service Fabric
description: Aprenda a crear un entorno con un clúster de Service Fabric independiente. Vea cómo iniciar y detener el clúster mediante programaciones.
author: EMaher
ms.topic: how-to
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: c9c7eec0d5c909285e4f7eb8768dbb300c86822e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325284"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Creación de un entorno con un clúster de Service Fabric independiente en Azure DevTest Labs
En este artículo se proporciona información sobre cómo crear un entorno con un clúster de Service Fabric independiente en Azure DevTest Labs. 

## <a name="overview"></a>Información general
DevTest Labs puede crear entornos de prueba independientes, tal como definen las plantillas de Azure Resource Manager. Estos entornos incluyen tanto recursos de IaaS, como máquinas virtuales, como recursos de PaaS, como Service Fabric. DevTest Labs le permite administrar máquinas virtuales en un entorno proporcionando comandos para controlarlas. Estos comandos le permiten iniciar o detener una máquina virtual según una programación. De forma similar, DevTest Labs también puede ayudarle a administrar clústeres de Service Fabric en un entorno. Puede iniciar o detener un clúster de Service Fabric en un entorno manualmente o a través de una programación.

## <a name="create-a-service-fabric-cluster"></a>Creación de un clúster de Service Fabric
Los clústeres de Service Fabric se crean mediante entornos en DevTest Labs. Una plantilla de Azure Resource Manager se encarga de definir cada entorno en un repositorio de Git. El [repositorio de Git público](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) de DevTest Labs incluye la plantilla de Resource Manager para crear un clúster de Service Fabric en la carpeta [ServiceFabric-Cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). 

1. En primer lugar, cree un laboratorio en Azure DevTest Labs con las instrucciones del siguiente artículo: [Creación de un laboratorio](devtest-lab-create-lab.md). Tenga en cuenta que la opción **Entornos públicos** está **Activada** de forma predeterminada. 
2. Confirme que el proveedor de Service Fabric está registrado para su suscripción siguiendo estos pasos:
    1. Seleccione **Suscripciones** en el menú de navegación izquierdo y seleccione su **Suscripción**.
    2. En la página **Suscripción**, seleccione **Proveedores de recursos** en la sección **Configuración** del menú izquierdo. 
    3. Si **Microsoft.ServiceFabric** no está registrado, seleccione **Registrar**. 
3. En la página **DevTest Lab** de su laboratorio, seleccione **+ Agregar** en la barra de herramientas. 
    
    ![Captura de pantalla en la que se muestra el botón Agregar en la barra de herramientas.](./media/create-environment-service-fabric-cluster/add-button.png)
3. En la página **Elegir una base**, seleccione **Clúster de laboratorio de Service Fabric** en la lista. 

    ![Captura de pantalla en la que se muestra Service Fabric Lab Cluster en la lista.](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. En la página **Parámetros de configuración**, siga estos pasos: 
    1. Especifique un **nombre** para el **entorno** del clúster, que es el grupo de recursos de Azure para el clúster de Service Fabric. 
    2. Seleccione el **sistema operativo (SO)** de las máquinas virtuales del clúster. El valor predeterminado es: **Windows**.
    3. Especifique un nombre para el **administrador** del clúster. 
    4. Especifique una **contraseña** del administrador. 
    5. Para el **certificado**, escriba la información del mismo como una cadena codificada en Base64. Para crear un certificado, siga estos pasos:
        1. Descargue el archivo **Create-ClusterCertificate.ps1** desde el [repositorio de Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). También puede clonar el repositorio en la máquina. 
        2. Inicie **PowerShell**. 
        3. Ejecute el archivo **ps1** mediante el comando `.\Create-ClusterCertificate.ps1`. Verá un archivo de texto abierto en el Bloc de notas con la información que necesita para rellenar los campos relacionados con el certificado de esta página.
    6. Escriba la **contraseña del certificado**.
    7. Especifique la **huella digital** para su certificado.
    8. Seleccione **Agregar** en la página **Parámetros de configuración**. 

        ![Captura de pantalla en la que se muestra la opción Configurar el clúster.](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Una vez que se cree el clúster, verá un grupo de recursos con el nombre del entorno que proporcionó en el paso anterior. Cuando lo expanda, verá el clúster de Service Fabric en él. Si el estado del grupo de recursos se ha quedado atascado en **Creando**, seleccione **Actualizar** en la barra de herramientas. El entorno de **clúster de Service Fabric** crea un clúster de cinco nodos y un tipo de nodos en Linux o Windows.

    En el siguiente ejemplo, **mysfabricclusterrg** es el nombre del grupo de recursos creado específicamente para el clúster de Service Fabric. Recuerde que los entornos de laboratorio son independientes en el grupo de recursos en el que los creó. Esta limitación significa que la plantilla que define el entorno solo puede tener acceso a los recursos del grupo de recursos recién creado o las [redes virtuales que el laboratorio utiliza](devtest-lab-configure-vnet.md). En este ejemplo se crean todos los recursos necesarios en el mismo grupo de recursos.

    ![Captura de pantalla en la que se muestra el clúster creado.](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Inicio o detención del clúster
Puede iniciar o detener el clúster desde la misma página de DevTest Labs o desde la página Clúster de Service Fabric proporcionada por DevTest Labs. 

### <a name="from-the-devtest-labs-page"></a>Desde la página de DevTest Labs
Puede iniciar o detener el clúster en la página de DevTest Labs de su laboratorio. 

1. Seleccione **tres puntos (...)** para el clúster de Service Fabric como se muestra en la siguiente imagen: 

    ![Captura de pantalla en la que se muestran los comandos de inicio y detención del clúster.](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Verá dos comandos en el menú contextual para **iniciar** y **detener** el clúster. El comando de inicio inicia todos los nodos de un clúster. El comando de detención detiene todos los nodos de un clúster. Una vez que se detiene un clúster, el clúster de Service Fabric permanece en un estado listo. Pero no hay nodos disponibles hasta que vuelva a emitir el comando de inicio en el clúster del laboratorio.

    Deben tenerse en cuenta algunas consideraciones al usar un clúster de Service Fabric en un entorno de prueba. El clúster de Service Fabric puede tardar un poco en rehidratarse totalmente una vez que se hayan reiniciado los nodos. Para conservar los datos entre el apagado y el inicio, debe guardarlos en un disco administrado conectado a la máquina virtual. Hay implicaciones de rendimiento al usar un disco administrado conectado, por lo que solo se recomienda para entornos de prueba. Si no se hace una copia de seguridad del disco usado para el almacenamiento de datos, perderá los datos cuando emita el comando de detención en el clúster.

### <a name="from-the-service-fabric-cluster-page"></a>Desde la página Clúster de Service Fabric 
Existe otra forma de iniciar o detener el clúster. 

1. Seleccione su clúster de Service Fabric en la vista de árbol de la página de DevTest Labs. 

    ![Captura de pantalla en la que se muestra la opción para seleccionar el clúster.](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. En la página **Clúster de Service Fabric** del clúster, verá comandos en la barra de herramientas para iniciar este o detenerlo. 

    ![Captura de pantalla en la que se muestran los comandos de inicio y detención en la página del clúster de Service Fabric.](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Configuración de la programación del inicio y el apagado automáticos
Los clústeres de Service Fabric también se pueden iniciar o detener según una programación. Esta experiencia es similar a la de las máquinas virtuales en un laboratorio. Para ahorrar dinero, de forma predeterminada, todos los clústeres creados en un laboratorio automáticamente se apagan en el momento definido por la [directiva de apagado](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) del laboratorio. Puede llevar a cabo la invalidación especificando si el clúster debe apagarse o la hora a la que se apaga. 

![Captura de pantalla en la que se muestran las programaciones existentes para el inicio y el apagado automáticos.](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Participación en la programación del inicio automático
Para participar en la programación del inicio, siga estos pasos:

1. Seleccione **Inicio automático** en el menú izquierdo.
2. Seleccione **Activar** para **Permitir que este clúster de Service Fabric se programe para el inicio automático**. Esta página solo se habilita si el propietario del laboratorio ha permitido a los usuarios iniciar automáticamente sus máquinas virtuales o los clústeres de Service Fabric.
3. Seleccione **Guardar** en la barra de herramientas. 

    ![Captura de pantalla en la que se muestra la página de inicio automático.](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Configuración de opciones de apagado automático 
Para cambiar la configuración de apagado, siga estos pasos:

1. Seleccione **Apagado automático** en el menú izquierdo. 
2. En esta página, puede no participar en el apagado automático seleccionando **Desactivar** para **Habilitado**. 
3. Si ha seleccionado **Activar** para **Habilitado**, siga estos pasos:
    1. Especifique la **hora** de apagado.
    2. Especifique la **zona horaria** para la hora. 
    3. Especifique si desea que DevTest Labs envíe **notificaciones** antes del apagado automático. 
    4. Si seleccionó **Sí** para la opción de notificación, especifique la **Dirección URL de webhook** o la **dirección de correo electrónico** para enviar notificaciones. 
    5. Seleccione **Guardar** en la barra de herramientas.

        ![Captura de pantalla en la que se muestra la página de apagado automático.](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Para ver los nodos del clúster de Service Fabric
La página del clúster de Service Fabric de los pasos anteriores es una página de DevTest Labs. En esta página no se muestran los nodos del clúster. Para ver más información sobre el clúster, siga estos pasos:

1. En la página **DevTest Lab** de su laboratorio, seleccione el **grupo de recursos** en la vista de árbol de la sección **Mis máquinas virtuales**.

    ![Captura de pantalla en la que se muestra Seleccionar grupo de recursos.](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. En la página **Grupo de recursos**, verá todos los recursos del grupo de recursos en una lista. Seleccione su **clúster de Service Fabric** en la lista. 

    ![Captura de pantalla en la que se muestra la opción para seleccionar el clúster en la lista.](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Verá la página **Clúster de Service Fabric** que Service Fabric proporciona para el clúster. En la página se muestra toda la información sobre el clúster, como los nodos y los tipos de nodo.

    ![Captura de pantalla en la que se muestra la página principal del clúster de Service Fabric.](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Pasos siguientes
Consulte estos artículos para obtener detalles sobre los entornos: 

- [Creación de entornos de varias máquinas virtuales y recursos de PaaS con plantillas de Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configuración y uso de entornos públicos en Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
