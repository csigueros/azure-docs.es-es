---
title: Montaje de Azure Blob Storage con el protocolo NFS 3.0 | Microsoft Docs
description: Aprenda a montar un contenedor en Blob Storage desde una máquina virtual (VM) de Azure o un cliente que se ejecuta de forma local mediante el protocolo NFS 3.0.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 54d406303019ebfa967133c26bd5487c848575b2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664891"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol"></a>Montaje de Blob Storage con el protocolo Network File System (NFS) 3.0

Puede montar un contenedor en Blob Storage desde una máquina virtual (VM) de Azure basada en Linux o un sistema Linux que se ejecuta de forma local mediante el protocolo NFS 3.0. En este artículo se proporcionan instrucciones paso a paso. Para más información sobre la compatibilidad con el protocolo NFS 3.0 en Blob Storage, vea [Compatibilidad del protocolo Network File System (NFS) 3.0 en Azure Blob Storage](network-file-system-protocol-support.md).

## <a name="step-1-create-an-azure-virtual-network-vnet"></a>Paso 1: Creación de una instancia de Azure Virtual Network (VNet)

La cuenta de almacenamiento debe incluirse en una red virtual. Una red virtual permite a los clientes conectarse de forma segura a su cuenta de almacenamiento. Para obtener más información sobre las redes virtuales y cómo crear una, consulte la [documentación de Virtual Network](../../virtual-network/index.yml).

> [!NOTE]
> Los clientes de la misma red virtual pueden montar contenedores en su cuenta. También puede montar un contenedor desde un cliente que se ejecuta en una red local, pero primero tendrá que conectar la red local a la red virtual. Consulte [Conexiones de red compatibles](network-file-system-protocol-support.md#supported-network-connections).

## <a name="step-2-configure-network-security"></a>Paso 2: Configuración de la seguridad de red

La única manera de proteger los datos en su cuenta es mediante una red virtual y otra configuración de seguridad de red. Las demás herramientas que se usan para proteger los datos, incluidas la autorización de claves de cuenta, la seguridad de Azure Active Directory (AD) y las listas de control de acceso (ACL), no se admiten todavía en cuentas que tienen habilitada la compatibilidad con el protocolo NFS 3.0.

Para proteger los datos de la cuenta, consulte estas recomendaciones: [Recomendaciones de seguridad de red para Blob Storage](security-recommendations.md#networking).

## <a name="step-3-create-and-configure-a-storage-account"></a>Paso 3: Creación y configuración de una cuenta de almacenamiento

Para montar un contenedor mediante NFS 3.0, debe crear una cuenta de almacenamiento. No se pueden habilitar las cuentas existentes.

El protocolo NFS 3.0 es compatible con las cuentas de almacenamiento estándar de uso general v2 y las cuentas de almacenamiento de blobs en bloques prémium. Para más información sobre estos tipos de cuentas de almacenamiento, consulte [Introducción a las cuentas de almacenamiento](../common/storage-account-overview.md).

A medida que configure la cuenta, elija estos valores:

|Configuración | Rendimiento Premium | Rendimiento Estándar
|----|---|---|
|Location|Todas las regiones disponibles |Todas las regiones disponibles
|Rendimiento|Premium| Estándar
|Tipo de cuenta|BlockBlobStorage| Uso general v2
|Replicación|Almacenamiento con redundancia local (LRS) o almacenamiento con redundancia de zona (ZRS)| Almacenamiento con redundancia local (LRS) o almacenamiento con redundancia de zona (ZRS)
|Método de conectividad|Punto de conexión público (redes seleccionadas) o punto de conexión privado. |Punto de conexión público (redes seleccionadas) o punto de conexión privado.
|Espacio de nombres jerárquico|habilitado|habilitado
|NFS V3|habilitado |habilitado

Puede aceptar los valores predeterminados de las demás opciones de configuración.

## <a name="step-4-create-a-container"></a>Paso 4: Creación de un contenedor

Cree un contenedor en la cuenta de almacenamiento mediante alguna de estas herramientas o SDK:

|Herramientas|SDK|
|---|---|
|[Azure Portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[CLI de Azure](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

> [!NOTE]
> De forma predeterminada, la opción de squash raíz de un nuevo contenedor es `no root squash`. Sin embargo, puede cambiar eso por `root squash` o `all squash`. Para obtener información sobre estas opciones de squash, consulte la documentación del sistema operativo.

En la imagen siguiente se muestran las opciones de squash tal como aparecen en Azure Portal.

> [!div class="mx-imgBorder"]
> ![Opciones de squash en Azure Portal](./media/network-file-system-protocol-how-to/squash-options-azure-portal.png)

## <a name="step-5-mount-the-container"></a>Paso 5: Montaje del contenedor

Cree un directorio en el sistema Linux y, a continuación, monte un contenedor en la cuenta de almacenamiento.

1. En un sistema Linux, cree un directorio.

   ```
   mkdir -p /mnt/test
   ```

2. Monte un contenedor mediante el comando siguiente.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Reemplace el marcador de posición `<storage-account-name>` que aparece en este comando por el nombre de la cuenta de almacenamiento.

   - Reemplace el marcador de posición `<container-name>` por el nombre del contenedor.

---

## <a name="resolve-common-errors"></a>Resolución de problemas comunes

|Error | Causa/resolución|
|---|---|
|`Access denied by server while mounting`|Asegúrese de que el cliente se ejecute en una subred compatible. Consulte las [ubicaciones de red compatibles](network-file-system-protocol-support.md#supported-network-connections).|
|`No such file or directory`| Asegúrese de escribir el comando mount y sus parámetros directamente en el terminal. Si copia y pega cualquier parte de este comando en el terminal desde otra aplicación, es posible que aparezca este error en los caracteres ocultos de la información pegada.|
|`Permision denied`| El modo predeterminado de un contenedor NFS v3 recién creado es 0750. Los usuarios no raíz no tienen acceso al volumen. Si se requiere acceso de usuarios no raíz, el usuario raíz debe cambiar el modo a 0755. Comando de ejemplo: `sudo chmod 0755 /mnt/<newcontainer>`|
|`EINVAL ("Invalid argument"`) |Este error puede aparecer cuando un cliente intenta lo siguiente:<li>Escribir en un blob que se creó a partir de un punto de conexión de blob.<li>Eliminar un blob que tiene una instantánea o que está en un contenedor que tiene una directiva WORM (una sola escritura/varias lecturas) activa.|
|`EROFS ("Read-only file system"`) |Este error puede aparecer cuando un cliente intenta lo siguiente:<li>Escribir en un blob o eliminar un blob que tiene una concesión activa.<li>Escribir en un blob o eliminar un blob en un contenedor que tiene una directiva WORM (una sola escritura/varias lecturas) activa. |
|`NFS3ERR_IO/EIO ("Input/output error"`) |Este error puede aparecer cuando un cliente intenta leer, escribir o establecer atributos en blobs almacenados en el nivel de acceso de archivo. |
|`OperationNotSupportedOnSymLink` con error| Este error se puede devolver durante una operación de escritura a través de una API de Blob o de Azure Data Lake Storage Gen2. No se permite el uso de estas API para escribir o eliminar vínculos simbólicos creados mediante NFS 3.0. Asegúrese de usar el punto de conexión NFS v3 para trabajar con vínculos simbólicos. |
|`mount: /mnt/test: bad option;`| Instale el programa del asistente de NFS mediante **sudo apt install nfs-common**.|

## <a name="see-also"></a>Vea también

- [Compatibilidad del protocolo Network File System (NFS) 3.0 en Azure Blob Storage](network-file-system-protocol-support.md)
- [Problemas conocidos con el protocolo Network File System (NFS) 3.0 en Azure Blob Storage](network-file-system-protocol-known-issues.md)
