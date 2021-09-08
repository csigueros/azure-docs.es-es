---
title: Creación de un entorno de clúster de Service Fabric en Azure DevTest Labs
description: Aprenda a crear un entorno con un clúster de Service Fabric independiente e inicie y detenga el clúster mediante programaciones.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: e85e4dd34a63500b569b75bb953a4c6061aefe5e
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586012"
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
    
    ![Botón Agregar de la barra de herramientas](./media/create-environment-service-fabric-cluster/add-button.png)
3. En la página **Elegir una base**, seleccione **Clúster de laboratorio de Service Fabric** en la lista. 

    ![Selección de Service Fabric Lab Cluster en la lista](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. En la página **Parámetros de configuración**, siga estos pasos: 
    1. Especifique un **nombre** para su **entorno** de clúster. Este es el nombre del grupo de recursos de Azure en el que se va a crear el clúster de Service Fabric. 
    2. Seleccione el **sistema operativo (SO)** de las máquinas virtuales del clúster. El valor predeterminado es: **Windows**.
    3. Especifique un nombre para el **administrador** del clúster. 
    4. Especifique una **contraseña** del administrador. 
    5. Para el **certificado**, escriba la información del mismo como una cadena codificada en Base64. Para crear un certificado, siga estos pasos:
        1. Descargue el archivo **Create-ClusterCertificate.ps1** desde el [repositorio de Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). De forma alternativa, clone el repositorio en la máquina. 
        2. Inicie **PowerShell**. 
        3. Ejecute el archivo **ps1** mediante el comando `.\Create-ClusterCertificate.ps1`. Verá un archivo de texto abierto en el Bloc de notas con la información que necesita para rellenar los campos relacionados con el certificado de esta página. . 
    6. Escriba la **contraseña del certificado**.
    7. Especifique la **huella digital** para su certificado.
    8. Seleccione **Agregar** en la página **Parámetros de configuración**. 

        ![Configuración de opciones del clúster](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Una vez que se cree el clúster, verá un grupo de recursos con el nombre del entorno que proporcionó en el paso anterior. Cuando lo expanda, verá el clúster de Service Fabric en él. Si el estado del grupo de recursos se ha quedado atascado en **Creando**, seleccione **Actualizar** en la barra de herramientas. El entorno de **clúster de Service Fabric** crea un clúster de cinco nodos y un tipo de nodos en Linux o Windows.

    En el siguiente ejemplo, **mysfabricclusterrg** es el nombre del grupo de recursos creado específicamente para el clúster de Service Fabric. Es importante tener en cuenta que los entornos de laboratorio son independientes dentro del grupo de recursos en el que se crean. Significa que la plantilla que define el entorno solo puede tener acceso a los recursos del grupo de recursos recién creado o las [redes virtuales configuradas para que el laboratorio las use](devtest-lab-configure-vnet.md). En este ejemplo de más arriba se crean todos los recursos necesarios en el mismo grupo de recursos.

    ![Clúster creado](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Inicio o detención del clúster
Puede iniciar o detener el clúster desde la misma página DevTest Lab o desde la página Clúster de Service Fabric proporcionada por DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Desde la página DevTest Lab
Puede iniciar o detener el clúster en la página DevTest Lab de su laboratorio. 

1. Seleccione **tres puntos (...)** para el clúster de Service Fabric como se muestra en la siguiente imagen: 

    ![Comandos de inicio y detención del clúster](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Verá dos comandos en el menú contextual para **iniciar** y **detener** el clúster. El comando de inicio inicia todos los nodos de un clúster. El comando de detención detiene todos los nodos de un clúster. Una vez que se detenga un clúster, el mismo clúster de Service Fabric permanecerá en estado Listo, pero no habrá ningún nodo disponible hasta que el comando de inicio vuelva a emitirse en el clúster del laboratorio.

    Deben tenerse en cuenta algunas consideraciones al usar un clúster de Service Fabric en un entorno de prueba. El clúster de Service Fabric puede tardar un poco en rehidratarse totalmente una vez que se hayan reiniciado los nodos. Para conservar los datos entre el apagado y el inicio, estos deben guardarse en un disco administrado asociado a la máquina virtual. Hay implicaciones de rendimiento al usar un disco administrado asociado, por lo que solo se recomienda para entornos de prueba. Si el disco usado para almacenar los datos carece de respaldo, se perderán los datos cuando el comando de detención se emita en el clúster.

### <a name="from-the-service-fabric-cluster-page"></a>Desde la página Clúster de Service Fabric 
Existe otra forma de iniciar o detener el clúster. 

1. Seleccione su clúster de Service Fabric en la vista de árbol de la página DevTest Lab. 

    ![Selección del clúster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. En la página **Clúster de Service Fabric** del clúster, verá comandos en la barra de herramientas para iniciar este o detenerlo. 

    ![Comandos de inicio o detención en la página Clúster de Service Fabric](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Configuración de la programación del inicio y el apagado automáticos
Los clústeres de Service Fabric también se pueden iniciar o detener según una programación. Esta experiencia es similar a la de las máquinas virtuales en un laboratorio. Para ahorrar dinero, de forma predeterminada, todos los clústeres creados en un laboratorio automáticamente se apagan en el momento definido por la [directiva de apagado](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) del laboratorio. Puede llevar a cabo la invalidación especificando si el clúster debe apagarse o la hora a la que se apaga. 

![Programaciones existentes del inicio y el apagado automáticos](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Participación en la programación del inicio automático
Para participar en la programación del inicio, siga estos pasos:

1. Seleccione **Inicio automático** en el menú izquierdo.
2. Seleccione **Activar** para **Permitir que este clúster de Service Fabric se programe para el inicio automático**. Esta página solo se habilita si el propietario del laboratorio ha permitido a los usuarios iniciar automáticamente sus máquinas virtuales o los clústeres de Service Fabric.
3. Seleccione **Guardar** en la barra de herramientas. 

    ![Página de inicio automático](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

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

        ![Página de apagado automático](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Para ver los nodos del clúster de Service Fabric
La página Clúster de Service Fabric que ha visto en los pasos anteriores es específica de la página DevTest Labs. En ella no se le muestran los nodos del clúster. Para ver más información sobre el clúster, siga estos pasos:

1. En la página **DevTest Lab** de su laboratorio, seleccione el **grupo de recursos** en la vista de árbol de la sección **Mis máquinas virtuales**.

    ![Selección de un grupo de recursos](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. En la página **Grupo de recursos**, verá todos los recursos del grupo de recursos en una lista. Seleccione su **clúster de Service Fabric** en la lista. 

    ![Selección del clúster en la lista](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Verá la página **Clúster de Service Fabric** de su clúster. Esta es la página que proporciona Service Fabric. Verá toda la información sobre los clústeres, como nodos, tipos de nodo, etc.

    ![Página principal del clúster de Service Fabric](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Pasos siguientes
Consulte estos artículos para obtener detalles sobre los entornos: 

- [Creación de entornos de varias máquinas virtuales y recursos de PaaS con plantillas de Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configuración y uso de entornos públicos en Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
