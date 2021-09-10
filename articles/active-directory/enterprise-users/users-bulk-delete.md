---
title: Eliminación masiva de usuarios en el portal de Azure Active Directory | Microsoft Docs
description: Eliminación masiva de usuarios en el Centro de administración de Azure de Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 07/09/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdaa91a5ffed9e75666602490b4829a3a95e2782
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2021
ms.locfileid: "113597836"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Eliminación masiva de usuarios en Azure Active Directory

Mediante el portal de Azure Active Directory (Azure AD), puede quitar una gran cantidad de miembros de un grupo usando un archivo de valores separados por comas (CSV) para eliminar usuarios de forma masiva.

## <a name="csv-template-structure"></a>Estructura de la plantilla CSV

![El archivo CSV contiene los nombres y los identificadores de los usuarios que se eliminarán.](./media/users-bulk-delete/delete-csv-file.png)

Las filas de una plantilla CSV descargada son las siguientes:

- **Número de versión**: la primera fila, que contiene el número de versión, debe estar incluida en el archivo CSV de carga.
- **Encabezados de columna**: `User name [userPrincipalName] Required`. Las versiones anteriores de la plantilla pueden variar.
- **Fila de ejemplos**: hemos incluido en la plantilla un ejemplo de un valor aceptable. `Example: chris@contoso.com` Debe quitar la fila de ejemplo y reemplazarla por sus propias entradas.

### <a name="additional-guidance"></a>Instrucciones adicionales

- Las dos primeras filas de la plantilla de carga no se deben eliminar ni modificar, o no se podrá procesar la plantilla.
- Las columnas necesarias se enumeran en primer lugar.
- No agregue nuevas columnas a la plantilla. Cualquier columna adicional que agregue se omitirá y no se procesará.
- Descargue la versión más reciente de la plantilla CSV antes de realizar nuevos cambios.

## <a name="to-bulk-delete-users"></a>Para eliminar usuarios en masa

1. [Inicie sesión en la organización de Azure AD](https://aad.portal.azure.com) con una cuenta que sea la del administrador de usuarios de la organización.
1. En Azure AD, seleccione **Usuarios** > **Operaciones masivas** > **Eliminación masiva**.
1. En la página **Eliminación masiva de usuarios**, seleccione **Descargar** para descargar la versión más reciente de la plantilla CSV.
1. Abra el archivo CSV y agregue una línea por cada usuario que desee eliminar. El único valor necesario es **Nombre principal del usuario**. Guarde el archivo.
1. En la página **Eliminación masiva de usuarios**, en **Cargar el archivo csv**, vaya al archivo. Al seleccionar el archivo y hacer clic en Enviar, comienza su validación.
1. Cuando finalice la validación del contenido del archivo, aparecerá el mensaje **Archivo cargado correctamente**. Si hay errores, debe corregirlos para poder enviar el trabajo.
1. Cuando el archivo supere la validación, seleccione **Enviar** para iniciar la operación masiva de Azure que elimina los usuarios.
1. Cuando la operación de eliminación finalice, verá una notificación que indicará que la operación masiva se realizó correctamente.

Si hay errores, puede descargar y ver el archivo de resultados en la página **Resultados de la operación masiva**. El archivo contiene el motivo de cada error.

## <a name="check-status"></a>Comprobar estado

Puede ver el estado de todas las solicitudes masivas pendientes en la página **Resultados de la operación masiva**.

   [![Comprobación del estado de la eliminación en la página Resultados de la operación masiva.](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

A continuación, puede comprobar si los usuarios eliminados existen en la organización de Azure AD en Azure Portal o mediante PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Comprobación de usuarios eliminados en Azure Portal

1. Inicie sesión en Azure Portal con una cuenta que sea la del administrador de usuarios de la organización.
1. En el panel de navegación, seleccione **Azure Active Directory**.
1. En **Administrar**, seleccione **Usuarios**.
1. En **Mostrar**, seleccione **Todos los usuarios** y compruebe que los usuarios eliminados ya no se incluyen.

### <a name="verify-deleted-users-with-powershell"></a>Comprobación de usuarios eliminados con PowerShell

Ejecute el siguiente comando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Compruebe que los usuarios que ha eliminado ya no se incluyen.

## <a name="next-steps"></a>Pasos siguientes

- [Adición masiva de usuarios](users-bulk-add.md)
- [Descarga de la lista de usuarios](users-bulk-download.md)
- [Restauración de usuarios masiva](users-bulk-restore.md)
