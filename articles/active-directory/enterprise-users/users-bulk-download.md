---
title: Descargar una lista de usuarios en el portal de Azure Active Directory | Microsoft Docs
description: Descargue los registros de usuarios de forma masiva en el Centro de administración de Azure en Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 10/26/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cfeef7e89d94b2f00d1ca1795bb9b4874858411
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402093"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Descarga de una lista de usuarios en el portal de Azure Active Directory

Azure Active Directory (Azure AD) admite operaciones (creación) de descarga masiva de lista de usuarios.

## <a name="required-permissions"></a>Permisos necesarios

Tanto los usuarios administradores como los usuarios que no son administradores pueden descargar listas de usuarios.

## <a name="to-download-a-list-of-users"></a>Para descargar una lista de usuarios

1. [Inicie sesión en su organización de Azure AD](https://aad.portal.azure.com).
2. Vaya a **Azure Active Directory** > **Usuarios**.
3. En Azure AD, seleccione **Usuarios** > **Descargar usuarios**. De manera predeterminada, se exportan todos los perfiles de usuario.
4. En la página **Descargar usuarios**, seleccione **Iniciar** para recibir un archivo .csv que enumera las propiedades de perfil del usuario. Si hay errores, puede descargar y ver el archivo de resultados en la página **Resultados de la operación masiva**. El archivo contiene el motivo de cada error.

   ![Seleccione dónde quiere la lista de usuarios que quiere descargar.](./media/users-bulk-download/bulk-download.png)
   
>[!NOTE]
>El archivo de descarga contendrá la lista filtrada de usuarios en función del ámbito de los filtros aplicados.

   Se incluyen los atributos de usuario siguientes:

   - `userPrincipalName`
   - `displayName`
   - `surname`
   - `mail`
   - `givenName`
   - `objectId`
   - `userType`
   - `jobTitle`
   - `department`
   - `accountEnabled`
   - `usageLocation`
   - `streetAddress`
   - `state`
   - `country`
   - `physicalDeliveryOfficeName`
   - `city`
   - `postalCode`
   - `telephoneNumber`
   - `mobile`
   - `authenticationAlternativePhoneNumber`
   - `authenticationEmail`
   - `alternateEmailAddress`
   - `ageGroup`
   - `consentProvidedForMinor`
   - `legalAgeGroupClassification`

## <a name="check-status"></a>Comprobar estado

Puede ver el estado de las solicitudes masivas pendientes en la página **Resultados de la operación masiva**.

[![Comprobación del estado en la página Resultados de la operación masiva.](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Límites del servicio de descarga de forma masiva

La ejecución de cada actividad en bloque para exportar una lista de usuarios puede durar hasta una hora. Este ritmo permite exportar y descargar una lista de hasta 500 000 usuarios.

## <a name="next-steps"></a>Pasos siguientes

- [Adición masiva de usuarios](users-bulk-add.md)
- [Eliminación masiva de usuarios](users-bulk-delete.md)
- [Restauración de usuarios masiva](users-bulk-restore.md)
