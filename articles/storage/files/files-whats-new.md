---
title: Novedades de Azure Files
description: Obtenga más información sobre las nuevas características y mejoras de Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/31/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bc44ba9bc2cad45ab447b86a580fb505c8abd6d
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273238"
---
# <a name="whats-new-in-azure-files"></a>Novedades de Azure Files
Azure Files se actualiza periódicamente para ofrecer nuevas características y mejoras. En este artículo se proporciona información detallada sobre las novedades de Azure Files.

## <a name="2021-quarter-3-july-august-september"></a>3\.er trimestre de 2021 (julio, agosto, septiembre)
### <a name="smb-multichannel-is-generally-available"></a>SMB multicanal está disponible con carácter general
SMB multicanal permite a los clientes SMB establecer varias conexiones paralelas a un recurso compartido de archivos de Azure. Esto permite a los clientes SMB aprovechar al máximo todo el ancho de banda de red disponible y los hace resistentes a los errores de red, lo que reduce el costo total de propiedad y habilita 2-3x para lecturas y 3-4x para escrituras a través de un solo cliente. SMB multicanal está disponible para recursos compartidos de archivos Premium (recursos compartidos de archivos implementados en el tipo de cuenta de almacenamiento FileStorage) y está deshabilitado de forma predeterminada. 

Para más información, consulte:

- [Rendimiento de SMB multicanal en Azure Files](storage-files-smb-multichannel-performance.md)
- [Habilitar SMB multicanal](files-smb-protocol.md#smb-multichannel)
- [Información general sobre SMB multicanal en la documentación de Windows Server](/azure-stack/hci/manage/manage-smb-multichannel)

### <a name="smb-311-and-smb-security-settings"></a>Configuración de seguridad de SMB 3.1.1 y SMB
SMB 3.1.1 es la versión más reciente del protocolo SMB, publicada con Windows 10, que contiene actualizaciones importantes de seguridad y rendimiento. Azure Files SMB 3.1.1 se suministra con dos modos de cifrado adicionales, AES-128-GCM y AES-256-GCM, además de AES-128-CCM, que ya se admite. Para maximizar el rendimiento, AES-128-GCM se negocia como la opción de cifrado de canal SMB predeterminada; AES-128-CCM solo se negociará en clientes anteriores que no admitan AES-128-GCM. 

Según los requisitos normativos y de cumplimiento de la organización, se puede negociar AES-256-GCM en lugar de AES-128-GCM, ya sea restringiendo las opciones de cifrado del canal SMB permitidas en los clientes SMB, en Azure Files o en ambos. Se agregó compatibilidad con AES-256-GCM en Windows Server 2022 y Windows 10, versión 21H1.

Además de SMB 3.1.1, Azure Files expone la configuración de seguridad que cambia el comportamiento del protocolo SMB. Con esta versión, puede configurar las versiones de protocolo SMB permitidas, las opciones de cifrado del canal SMB, los métodos de autenticación y las opciones de cifrado de vales de Kerberos. De manera predeterminada, Azure Files habilita las opciones más compatibles, pero estas opciones se pueden cambiar en cualquier momento.

Para más información, consulte:

- [Configuración de seguridad de SMB](files-smb-protocol.md#smb-security-settings)
- Información de la versión SMB de [Windows](storage-how-to-use-files-windows.md) y [Linux](storage-how-to-use-files-linux.md)
- [Introducción a las características de SMB en la documentación de Windows Server](/windows-server/storage/file-server/file-server-smb-overview)

## <a name="2021-quarter-2-april-may-june"></a>2\.º trimestre de 2021 (abril, mayo, junio)
### <a name="premium-hot-and-cool-storage-capacity-reservations"></a>Reservas de capacidad de almacenamiento premium, de acceso frecuente y de acceso esporádico 
Azure Files admite reservas de la capacidad de almacenamiento (también denominadas *instancias de reserva*). Las reservas de la capacidad de almacenamiento le permiten conseguir un descuento en el almacenamiento mediante la confirmación previa del uso del almacenamiento. Azure Files admite reservas de la capacidad en los niveles premium, de acceso frecuente y de acceso esporádico. Las reservas de capacidad se venden en unidades de 10 TiB o 100 TiB, por periodos de un año o tres años. 

Para más información, consulte:

- [Descripción de la facturación de Azure Files](understanding-billing.md)
- [Optimización de costos de Azure Files con capacidad reservada](files-reserve-capacity.md)
- [Precios de Azure Files](https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="improved-portal-experience-for-domain-joining-to-active-directory"></a>Mejora de la experiencia del portal para la unión de dominios a Active Directory
Se ha mejorado la experiencia de unirse a un dominio de una cuenta de almacenamiento de Azure para ayudar a guiar a los administradores de recursos compartidos de archivos de Azure por primera vez a través del proceso. Al seleccionar Active Directory en **File share settings** (Configuración de recursos compartidos de archivos) en la sección **File shares** (Recursos compartidos de archivos) de Azure Portal, se le guiará por los pasos necesarios para la unión a un dominio.

:::image type="content" source="media/files-whats-new/ad-domain-join-1.png" alt-text="Captura de pantalla de la nueva experiencia del portal para unir una cuenta de almacenamiento a Active Directory" lightbox="media/files-whats-new/ad-domain-join-1.png":::

Para más información, consulte:

- [Introducción a las opciones de autenticación basadas en la identidad de Azure Files para el acceso con SMB](storage-files-active-directory-overview.md)
- [Introducción: autenticación de Active Directory Domain Services local en SMB para recursos compartidos de archivos de Azure](storage-files-identity-auth-active-directory-enable.md)

## <a name="2021-quarter-1-january-february-march"></a>1\.er trimestre de 2021 (enero, febrero, marzo)
### <a name="azure-files-management-now-available-through-the-control-plane"></a>Administración de Azure Files disponible ahora a través del plano de control
Las API de administración de los recursos de Azure Files, el servicio de archivos y los recursos compartidos de archivos están disponibles ahora a través del plano de control (proveedor de recursos `Microsoft.Storage`). Esto permite crear recursos compartidos de archivos de Azure con una plantilla de Azure Resource Manager o Bicep, para que sean totalmente administrables cuando el plano de datos (es decir, la API de FileREST) no está accesible (como cuando el punto de conexión público de la cuenta de almacenamiento está deshabilitado) y para admitir la semántica de control de acceso basado en roles (RBAC) completa.

Se recomienda administrar Azure Files a través del plano de control en la mayoría de los casos. Para admitir la administración del servicio de archivos y los recursos compartidos de archivos a través del plano de control, Azure Portal, el módulo PowerShell de Azure Storage y la CLI de Azure se han actualizado para admitir la mayoría de las acciones de administración a través del plano de control. 

Para conservar el comportamiento de los scripts existentes, el módulo PowerShell de Azure Storage y la CLI de Azure mantienen los comandos existentes que utilizan el plano de datos para administrar el servicio de archivos y los recursos compartidos de archivos, además de agregar nuevos comandos para usar el plano de control. Las solicitudes del portal solo pasan por el proveedor de recursos del plano de control. Los comandos de PowerShell y la CLI se denominan de la siguiente manera:

- Az.Storage PowerShell:
    - Los cmdlets de recurso compartido de archivos del plano de control llevan el prefijo `Rm`, por ejemplo: `New-AzRmStorageShare`, `Get-AzRmStorageShare`, `Update-AzRmStorageShare` y `Remove-AzRmStorageShare`. 
    - Los cmdlets de recurso compartido de archivos de plano de datos tradicionales no tienen prefijo, por ejemplo: `New-AzStorageShare`, `Get-AzStorageShare`, `Set-AzStorageShareQuota` y `Remove-AzStorageShare`.
    - Los cmdlets para administrar el servicio de archivos solo están disponibles a través del plano de control y no tienen ningún prefijo especial, por ejemplo, `Get-AzStorageFileServiceProperty` y `Update-AzStorageFileServiceProperty`.
- CLI de Azure Storage:
    - Los comandos del recurso compartido de archivos del plano de control están disponibles en el grupo de comandos `az storage share-rm`, por ejemplo: `az storage share-rm create`, `az storage share-rm update`, etc.
    - Los comandos del recurso compartido de archivos tradicional están disponibles en el grupo de comandos `az storage share`, por ejemplo: `az storage share create`, `az storage share update`, etc.
    - Los comandos para administrar el servicio de archivos solo están disponibles a través del plano de control y no están disponibles a través del grupo de comandos `az storage account file-service-properties`, por ejemplo: `az storage account file-service-properties show` y `az storage account file-service-properties update`.

Para obtener más información sobre la API de administración de Azure Files, consulte:

- [Información general de la API de REST de Azure Files](/rest/api/storageservices/file-service-rest-api)
- API del plano de control (proveedor de recursos `Microsoft.Storage`) para recursos de Azure Files: 
    - [`FileService`](/rest/api/storagerp/file-services) 
    - [`FileShare`](/rest/api/storagerp/file-shares) 
- [Azure PowerShell](/powershell/module/az.storage) y [CLI de Azure](/en-us/cli/azure/storage)

## <a name="2020-quarter-4-october-november-december"></a>4\.º trimestre de 2020 (octubre, noviembre, diciembre)
### <a name="azure-file-share-soft-delete"></a>Eliminación temporal de recursos compartidos de archivos de Azure
Los recursos compartidos de archivos de Azure admiten la eliminación temporal. Cuando la eliminación temporal está habilitada, los recursos compartidos eliminados accidentalmente se pueden recuperar fácilmente si se recuperan dentro del período de retención definido por el usuario. Durante el período de retención, los recursos compartidos eliminados temporalmente incurren en cargos por capacidad de uso de datos durante el período de retención con la tasa de instantáneas de recurso compartido.

Para obtener más información sobre la eliminación temporal, consulte:

- [Introducción a la eliminación temporal](storage-files-prevent-file-share-deletion.md)
- [Habilitación de la eliminación temporal](storage-files-enable-soft-delete.md)
- [Precios de Azure Files](https://azure.microsoft.com/pricing/details/storage/files/)

## <a name="see-also"></a>Consulte también
- [¿Qué es Azure Files?](storage-files-introduction.md)
- [Planeamiento de una implementación de Azure Files](storage-files-planning.md)
- [Creación de un recurso compartido de archivos de Azure](storage-how-to-create-file-share.md)
