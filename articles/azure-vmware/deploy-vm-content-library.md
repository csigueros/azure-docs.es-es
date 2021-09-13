---
title: Creación de una biblioteca de contenido para implementar máquinas virtuales en Azure VMware Solution
description: Cree una biblioteca de contenido para implementar una máquina virtual en una nube privada de Azure VMware Solution.
ms.topic: how-to
ms.date: 06/28/2021
ms.openlocfilehash: ed98d48037df6cfb50c3c94bb6a7187097f5b0e9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323691"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Creación de una biblioteca de contenido para implementar máquinas virtuales en Azure VMware Solution

Una biblioteca de contenido almacena y administra el contenido en forma de elementos de biblioteca. Un solo elemento de biblioteca consta de archivos que se usan para implementar máquinas virtuales (VM).

En este artículo, creará una biblioteca de contenido en el cliente de vSphere y, luego, implementará una máquina virtual mediante una imagen ISO de la biblioteca de contenido.

## <a name="prerequisites"></a>Requisitos previos

Para realizar este tutorial, se necesitan un segmento de NSX-T y un servicio DHCP administrado.  Para más información, consulte el artículo [Configuración de DHCP para Azure VMware Solution](configure-dhcp-azure-vmware-solution.md).  

## <a name="create-a-content-library"></a>Crear una biblioteca de contenido

1. En el cliente de vSphere local, seleccione **Menú** > **Bibliotecas de contenido**.

   :::image type="content" source="media/content-library/vsphere-menu-content-libraries.png" alt-text="Captura de pantalla que muestra la opción de menú Bibliotecas de contenido en el cliente de vSphere.":::

1. Seleccione **Agregar** para crear una nueva biblioteca de contenido.

   :::image type="content" source="media/content-library/create-new-content-library.png" alt-text="Captura de pantalla que muestra cómo crear una biblioteca de contenido en vSphere.":::

1. Proporcione un nombre y confirme la dirección IP del servidor vCenter; luego, seleccione **Siguiente**.

   :::image type="content" source="media/content-library/new-content-library-step-1.png" alt-text="Captura de pantalla que muestra el nombre y la dirección IP de vCenter Server de la nueva biblioteca de contenido.":::

1. Seleccione **Local content library** (Biblioteca de contenido local) y elija **Next** (Siguiente).

   :::image type="content" source="media/content-library/new-content-library-step-2.png" alt-text="Captura de pantalla que muestra la opción Biblioteca de contenido local seleccionada para la nueva biblioteca de contenido.":::

1. Seleccione el almacén de datos que almacenará la biblioteca de contenido y, luego, elija **Siguiente**.

   :::image type="content" source="media/content-library/new-content-library-step-3.png" alt-text="Captura de pantalla que muestra la ubicación de almacenamiento de vsanDatastore seleccionada.":::

1. Revise y compruebe la configuración de la biblioteca de contenido y, luego, seleccione **Finalizar**.

   :::image type="content" source="media/content-library/new-content-library-step-4.png" alt-text="Captura de pantalla que muestra la configuración de la nueva biblioteca de contenido.":::

## <a name="upload-an-iso-image-to-the-content-library"></a>Cargar una imagen ISO en la biblioteca de contenido

Ahora que se ha creado la biblioteca de contenido, puede agregar una imagen ISO para implementar una máquina virtual en un clúster de nube privada. 

1. En el cliente de vSphere, seleccione **Menú** > **Bibliotecas de contenido**.

1. Haga clic con el botón derecho en la biblioteca de contenido que quiera usar para la nueva imagen ISO y seleccione **Import Item** (Importar elemento).

1. Importe un elemento de la biblioteca para el origen mediante una de las acciones siguientes y, luego, seleccione **Import** (Importar):
   1. Seleccione la dirección URL y proporcione una dirección URL para descargar una imagen ISO.

   1. Seleccione **Local File** (Archivo local) para cargarla desde el sistema local.

   > [!TIP]
   > También puede definir un nombre de elemento personalizado y notas para el destino.

1. Abra la biblioteca y seleccione la pestaña **Other Types** (Otros tipos) para comprobar que la imagen ISO se cargó correctamente.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Implementación de una máquina virtual en un clúster de nube privada

1. En el cliente de vSphere, seleccione **Menú** > **Hosts y clústeres**.

1. En el panel izquierdo, expanda el árbol y seleccione un clúster.

1. Seleccione **Acciones** > **Nueva máquina virtual**.

1. Siga los pasos del asistente y modifique los valores que desee.

1. Seleccione **New CD/DVD Drive** > **Client Device** > **Content Library ISO File** (Nueva unidad de CD/DVD > Dispositivo cliente > Archivo ISO de la biblioteca de contenido).

1. Seleccione la imagen ISO cargada en la sección anterior y, luego, elija **OK** (Aceptar).

1. Active la casilla **Connect** (Conectar) para que la imagen ISO se monte en el momento del encendido.

1. Seleccione **Nueva red** > **Select dropdown** (Seleccionar lista desplegable)  > **Examinar**.

1. Seleccione el **conmutador lógico (segmento)** y elija **OK** (Aceptar).

1. Modifique cualquier otra configuración de hardware y seleccione **Next** (Siguiente).

1. Compruebe la configuración y seleccione **Finish** (Finalizar).


## <a name="next-steps"></a>Pasos siguientes

Ahora que se ha explicado la creación de una biblioteca de contenido para implementar máquinas virtuales en Azure VMware Solution, puede que quiera obtener información sobre:

- [Migración de las cargas de trabajo de máquina virtual a la nube privada](configure-vmware-hcx.md)
- [Integración de servicios nativos de Azure en Azure VMware Solution](integrate-azure-native-services.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
