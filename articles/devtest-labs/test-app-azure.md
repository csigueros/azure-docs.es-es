---
title: Procedimiento para probar aplicaciones en Azure
description: Aprenda a implementar aplicaciones de escritorio o web en un recurso compartido de archivos y cómo probarlas.
ms.topic: how-to
ms.date: 11/03/2021
ms.openlocfilehash: af9fc320c6f08e0ef4141aac8076e121a2e6292d
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131575948"
---
# <a name="test-your-app-in-azure"></a>Prueba de las aplicaciones en Azure 

En esta guía, aprenderá a probar la aplicación en Azure mediante DevTest Labs. Puede usar Visual Studio para implementar la aplicación en un recurso compartido de archivos de Azure. A continuación, accederá al recurso compartido desde una máquina virtual (VM) de laboratorio.  

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Una estación de trabajo local con [Visual Studio](https://visualstudio.microsoft.com/free-developer-offers/).

- Un laboratorio en [DevTest Labs](devtest-lab-overview.md).

- Una [máquina virtual de Azure](devtest-lab-add-vm.md) que ejecute Windows en el laboratorio.

- Un [recurso compartido de archivos](../storage/files/storage-how-to-create-file-share.md) en la cuenta de almacenamiento de Azure existente del laboratorio. Con el laboratorio se crea automáticamente una cuenta de almacenamiento.

- El [recurso compartido de archivos de Azure montado](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share) en la estación de trabajo local y la máquina virtual de laboratorio.

## <a name="publish-your-app-from-visual-studio"></a>Publicación de la aplicación desde Visual Studio

En esta sección, publicará la aplicación desde Visual Studio en el recurso compartido de archivos de Azure.

1. Abra Visual Studio y elija **Crear un nuevo proyecto** en la ventana Inicio.

    :::image type="content" source="./media/test-app-in-azure/launch-visual-studio.png" alt-text="Captura de pantalla de la página de inicio de Visual Studio.":::

1. Seleccione **Aplicación de consola** y, a continuación, **Siguiente**.

    :::image type="content" source="./media/test-app-in-azure/select-console-application.png" alt-text="Captura de pantalla de la opción para elegir la aplicación de consola.":::

1. En la página **Configure su nuevo proyecto**, deje los valores predeterminados y seleccione **Siguiente**.

1. En la página **Información adicional**, deje los valores predeterminados y seleccione **Crear**.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Generar**.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**.

    :::image type="content" source="./media/test-app-in-azure/publish-application.png" alt-text="Captura de pantalla de la opción para publicar la aplicación.":::

1. En la página **Publicar**, seleccione **Carpeta** y, a continuación, **Siguiente**.

    :::image type="content" source="./media/test-app-in-azure/publish-to-folder.png" alt-text="Captura de pantalla de la opción para publicar en la carpeta.":::

1. En la opción **Destino específico**, seleccione **Carpeta** y, a continuación, **Siguiente**.

1. En la opción **Ubicación**, seleccione **Examinar** y después el recurso compartido de archivos que ha montado anteriormente. Seleccione **Aceptar** y después **Finalizar**. 

    :::image type="content" source="./media/test-app-in-azure/selecting-file-share.png" alt-text="Captura de pantalla de la opción para seleccionar el recurso compartido de archivos.":::

1. Seleccione **Publicar**. Visual Studio compila la aplicación y la publica en el recurso compartido de archivos.

    :::image type="content" source="./media/test-app-in-azure/final-publish.png" alt-text="Captura de pantalla del botón para publicar.":::

## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Prueba de la aplicación en la máquina virtual de prueba del laboratorio

1. Conéctese a la máquina virtual de laboratorio.

1. Dentro de la máquina virtual, inicie el **Explorador de archivos** y seleccione **Este equipo** para encontrar el recurso compartido de archivos que montó anteriormente.

    :::image type="content" source="./media/test-app-in-azure/find-share-on-vm.png" alt-text="Captura de pantalla del Explorador de archivos.":::

1. Abra el recurso compartido de archivos y confirme que ve la aplicación que ha implementado desde Visual Studio. 

    :::image type="content" source="./media/test-app-in-azure/open-file-share.png" alt-text="Captura de pantalla del contenido del recurso compartido de archivos.":::

    Ahora puede acceder a la aplicación y probarla dentro de la máquina virtual de prueba que creó en Azure.

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos para aprender a usar máquinas virtuales de un laboratorio. 

- [Adición de una máquina virtual a un laboratorio](devtest-lab-add-vm.md)
- [Reinicio de una máquina virtual de laboratorio](devtest-lab-restart-vm.md)
- [Cambio de tamaño de una máquina virtual de laboratorio](devtest-lab-resize-vm.md)
