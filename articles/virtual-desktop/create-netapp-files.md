---
title: 'Creación de Azure NetApp Files en Azure Virtual Desktop: Azure'
description: En este artículo se describe cómo crear Azure NetApp Files en Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: fa6052c91be73e05bc2413f57810405fe5a198a0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818634"
---
# <a name="upload-msix-images-to-azure-netapp-files-in-azure-virtual-desktop"></a>Carga de imágenes MSIX en Azure NetApp Files en Azure Virtual Desktop

En este artículo se describe cómo crear imágenes MSIX para Azure NetApp Files en Azure Virtual Desktop.

## <a name="requirements"></a>Requisitos

Antes de empezar a cargar las imágenes, deberá configurar Azure NetApp Files si aún no lo ha hecho.

Para configurar Azure NetApp Files, necesitará lo siguiente:

- Una cuenta de Azure con acceso de colaborador o administrador

- Una máquina virtual (VM) o física unida a Active Directory Domain Services (AD DS) y permisos para acceder a ella

- Un grupo de hosts de Azure Virtual Desktop compuesto por hosts de sesión unidos a un dominio. Cada host de sesión debe estar en la misma región que la región en la que se crean las instancias de Azure NetApp Files. Para obtener más información, consulte [Disponibilidad regional](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Si los hosts de sesión existentes no están en una de las regiones disponibles, deberá crear otros nuevos.

## <a name="start-using-azure-netapp-files"></a>Uso de Azure NetApp Files

Para empezar a usar Azure NetApp Files:

1. Para configurar la cuenta de Azure NetApp Files, siga las instrucciones de [Configuración de la cuenta de Azure NetApp Files](create-fslogix-profile-container.md#set-up-your-azure-netapp-files-account).
2. Para configurar el grupo de capacidad, siga las instrucciones que se muestran en [Configuración de un grupo de capacidad](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).
3. Para unir una conexión de Azure Active Directory (Azure AD), siga las instrucciones de [Unión a una conexión de Active Directory](create-fslogix-profile-container.md#join-an-active-directory-connection).
4. Para crear un nuevo volumen, siga las instrucciones de [Creación de un nuevo volumen](create-fslogix-profile-container.md#create-a-new-volume) y [Configuración de parámetros de acceso al volumen](create-fslogix-profile-container.md#configure-volume-access-parameters).
5. Para asegurarse de que la conexión al recurso compartido de Azure NetApp Files funciona, siga las instrucciones de [Asegúrese de que los usuarios puedan tener acceso al recurso compartido de Azure NetApp Files](create-fslogix-profile-container.md#make-sure-users-can-access-the-azure-netapp-file-share).

## <a name="upload-an-msix-image-to-the-azure-netapp-file-share"></a>Carga de una imagen MSIX en el recurso compartido de archivos de Azure NetApp Files

Ahora que ha configurado el recurso compartido de Azure NetApp Files, puede empezar a cargar imágenes en él.

Para cargar una imagen MSIX en el recurso compartido de archivos de Azure NetApp Files:

1. En cada host de sesión, instale el certificado con el que firmó el paquete MSIX. Asegúrese de almacenar los certificados en la carpeta denominada **Personas de confianza**.
2. Copie la imagen MSIX que desea agregar al recurso compartido de archivos de Azure NetApps Files.
3. Vaya a **Explorador de archivos** y escriba la ruta de montaje y, a continuación, pegue la imagen MSIX en la carpeta de la ruta de montaje.

La imagen MSIX ahora debe ser accesible para los hosts de sesión cuando agregan un paquete MSIX mediante Azure Portal o PowerShell.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado un recurso compartido de archivos de Azure NetApp Files, estos son algunos recursos sobre qué se puede usar en Azure Virtual Desktop:

- [Creación de un contenedor de perfiles con Azure NetApp Files y AD DS](create-fslogix-profile-container.md)
- [Opciones de almacenamiento para los contenedores de perfiles de FSLogix de Azure Virtual Desktop](store-fslogix-profile.md)
- [Creación de un emparejamiento de replicación para Azure NetApp Files](../azure-netapp-files/cross-region-replication-create-peering.md)
