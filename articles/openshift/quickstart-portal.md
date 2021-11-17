---
title: Implementación de un clúster de ARO mediante Azure Portal
description: Implementación de un clúster de ARO mediante Azure Portal
author: rahulmehta
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: quickstart
ms.date: 10/21/2021
ms.openlocfilehash: 15fe2cc5c9735c28de867510cd5497a892554db8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271231"
---
# <a name="quickstart-deploy-an-azure-red-hat-openshift-aro-cluster-using-the-azure-portal"></a>Inicio rápido: Implementación de un clúster de Red Hat OpenShift en Azure (ARO) mediante Azure Portal

Red Hat OpenShift en Azure (ARO) es un servicio de OpenShift administrado que le permite implementar y administrar clústeres rápidamente. En este inicio rápido, implementaremos un clúster de ARO mediante Azure Portal.

## <a name="prerequisites"></a>Requisitos previos
Inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="create-an-aro-cluster"></a>Creación de un clúster de ARO
1.  En el menú de Azure Portal o en la **página principal**, seleccione **Crear un recurso**.
2.  Seleccione **Contenedores** > **Red Hat OpenShift en Azure**.
3.  En la página **Datos básicos**, configure las siguientes opciones:
    * **Detalles del proyecto**:
        *   Seleccione una **suscripción de Azure**.
        *   Seleccione o cree un **grupo de recursos de Azure**, como *myResourceGroup*.
    * **Detalles del clúster**:
        * Seleccione una **Región** para el clúster de ARO.
        *   Escriba un **nombre de clúster** de OpenShift, como *myAROCluster*.
        *   Escriba el **Nombre de dominio**.
        *   Seleccione **Master VM Size** (Tamaño de máquina virtual maestra) y **Worker VM Size** (Tamaño de máquina virtual de trabajo).

![Pestaña **Básico** en Azure Portal](./media/portal-quickstart/basics-tab.jpg)

4.  En la página **Autenticación**, configure las siguientes opciones:
    1) Elija **Seleccionar existente** en el selector Tipo de entidad de servicio (si ya está seleccionado previamente, no se requiere ninguna acción y puede continuar con los pasos siguientes).
    2) Busque el proveedor de recursos Red Hat OpenShift en Azure y selecciónelo. 

>[!NOTE]
>Ignore el botón "Crear nuevo" y otros campos opcionales.

![Pestaña **Autenticación** en Azure Portal](./media/portal-quickstart/authentication.jpg)

5.  En la pestaña **Redes**, asegúrese de configurar lo siguiente:
    * Virtual network
    * Subred maestra
    * Subred de trabajo
    * Visibilidad del servidor de API
    * Visibilidad de la entrada

![Pestaña **Redes** en Azure Portal](./media/portal-quickstart/networking.jpg)

6.  En la sección **Etiquetas**, agregue etiquetas para organizar los recursos.

![Pestaña **Etiquetas** en Azure Portal](./media/portal-quickstart/tags.jpg)
 
7.  Haga clic en **Revisar y crear** y, luego, en **Crear** cuando finalice la validación.

![Pestaña **Revisar y crear** en Azure Portal](./media/portal-quickstart/review.jpg)
 
8.  La creación del clúster de ARO tarda aproximadamente entre 35 y 45 minutos. Una vez finalizada la implementación, vaya al recurso; puede hacerlo de dos maneras:
    *   Haga clic en **Ir al recurso**.
    *   Vaya al grupo de recursos del clúster de ARO y seleccione el recurso de ARO.
        *   Por ejemplo, en el panel de clúster siguiente, busque *myResourceGroup* y seleccione el recurso *myAROCluster*.
