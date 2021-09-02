---
title: Mejoras en la administración de usuarios (versión preliminar) en Azure Active Directory | Microsoft Docs
description: En este artículo se describe cómo Azure Active Directory habilita la funcionalidad de búsqueda de usuarios, filtrado y obtención de más información sobre los usuarios.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: de315722641248a2a577c97403da400a6de67e6c
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867080"
---
# <a name="user-management-enhancements-preview-in-azure-active-directory"></a>Mejoras en la administración de usuarios (versión preliminar) en Azure Active Directory

En este artículo se describe cómo usar la versión preliminar de las mejoras en la administración de usuarios en el portal de Azure Active Directory (Azure AD). Las páginas **Todos los usuarios** y **Usuarios eliminados** se han actualizado para proporcionar más información y facilitar la búsqueda de usuarios. Para más información sobre las versiones preliminares, consulte [Términos de uso complementarios de las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Estos son algunos de los cambios de esta versión preliminar:

- Propiedades de usuario más visibles, incluidos el identificador de objeto, el estado de sincronización de directorios, el tipo de creación y el emisor de identidades.
- Al buscar ahora se permite la búsqueda de subcadenas y la búsqueda combinada de nombres, correos electrónicos e identificadores de objeto
- Filtrado mejorado según el tipo de usuario (miembro, invitado y ninguno), estado de sincronización de directorios, tipo de creación, nombre de la empresa y nombre de dominio
- Nuevas funciones de organización en propiedades como el nombre y el nombre principal de usuario
- Un nuevo número total de usuarios que se actualiza con búsquedas o filtros

> [!NOTE]
> Esta versión preliminar no está disponible actualmente para los inquilinos de Azure AD B2C.

## <a name="find-the-preview"></a>Búsqueda de la versión preliminar

La versión preliminar está activada de forma predeterminada, por lo que puede usarla inmediatamente. También puede consultar las características y mejoras seleccionando **Información de versión preliminar** en la página **Todos los usuarios**. Todas las páginas que se han actualizado como parte de esta versión preliminar mostrarán una etiqueta de versión preliminar. Si tiene algún problema, puede volver a la experiencia heredada:

1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com) y seleccione **Usuarios**.
1. En la página **Usuarios - Todos los usuarios**, seleccione el banner situado en la parte superior de la página.
1. En el panel **Características de versión preliminar**, desactive **Administración de usuarios mejorada**.

   ![Cómo y dónde activar y desactivar Administración mejorada de usuarios](./media/users-search-enhanced/enable-preview.png)

Agradecemos sus comentarios para que podamos mejorar nuestra experiencia.

## <a name="more-user-properties"></a>Más propiedades de usuario

Hemos realizado algunos cambios en las columnas disponibles en las páginas **Todos los usuarios** y **Usuarios eliminados**. Además de las columnas que se proporcionan para administrar la lista de usuarios, hemos agregado algunas más.

### <a name="all-users-page"></a>Panel Todos los usuarios

A continuación se muestran las propiedades de usuario mostradas en la página **Todos los usuarios**:

- Nombre: Nombre para mostrar del usuario.
- Nombre principal del usuario: El nombre principal de usuario (UPN) del usuario.
- Tipo de usuario: miembro, invitado y ninguno.
- Hora de creación: la fecha y hora de creación del usuario.
- Puesto: el puesto del usuario.
- Departamento: el departamento en el que trabaja el usuario.
- Directorio sincronizado: indica si el usuario se ha sincronizado desde un directorio local.
- Emisor de identidades: los emisores de la identidad utilizados para iniciar sesión en una cuenta de usuario.
- Identificador de objeto: el identificador de objeto del usuario.
- Tipo de creación: indica cómo se creó la cuenta de usuario.
- Nombre de la empresa: el nombre de la empresa al que está asociado el usuario.
- Estado de invitación: el estado de la invitación de un usuario invitado.
- Correo: el correo electrónico del usuario.

![nuevas propiedades de usuario que se muestran en las páginas Todos los usuarios y Usuarios eliminados](./media/users-search-enhanced/user-properties.png)

### <a name="deleted-users-page"></a>Página Usuarios eliminados

En la página **Usuarios eliminados** se incluyen todas las columnas que están disponibles en la página **Todos los usuarios** y algunas columnas adicionales, que son:

- Fecha de eliminación: la fecha en que el usuario se eliminó por primera vez de la organización (el usuario es restaurable).
- Fecha de eliminación permanente: Fecha después de la cual comienza de forma automática el proceso de eliminación permanente del usuario de la organización.
- Nombre principal de usuario original: UPN original del usuario antes de que el id. de objeto se agregara como prefijo al UPN eliminado.

> [!NOTE]
> Las fechas de eliminación se muestran en hora universal coordinada (UTC).

Algunas columnas se muestran de forma predeterminada. Para agregar otras columnas, seleccione **Columnas** en la página, elija los nombres de columna que desea agregar y seleccione **Aceptar** para guardar las preferencias.

### <a name="identity-issuers"></a>Emisores de identidades

Seleccione una entrada en la columna **Emisor de identidades** de cualquier usuario para ver detalles adicionales sobre el emisor, incluidos el tipo de inicio de sesión y el identificador asignado del emisor. Las entradas de la columna **Emisor de identidades** pueden tener varios valores. Si hay varios emisores de la identidad del usuario, verá la palabra "Varios" en la columna **Emisor de identidades** de las páginas **Todos los usuarios** y **Usuarios eliminados**, y en el panel de detalles se muestra una lista de todos los emisores.

> [!NOTE]
> La columna **Origen** se ha reemplazado por varias columnas, entre las que se incluyen **Tipo de creación**, **Directorio sincronizado** y **Emisor de identidades**, para poder filtrar de forma más granular.

## <a name="user-list-search"></a>Búsqueda en la lista de usuarios

Al escribir una cadena de búsqueda, ahora la búsqueda usa "comienza por" y la búsqueda de subcadenas para comparar nombres, correos electrónicos o identificadores de objeto en una sola búsqueda. Puede escribir cualquiera de estos atributos en el cuadro de búsqueda para que se busque automáticamente en todas estas propiedades y se devuelvan todos los resultados coincidentes. La búsqueda de subcadenas solo se realiza sobre palabras completas. Puede realizar la misma búsqueda en las páginas **Todos los usuarios** y **Usuarios eliminados**.

## <a name="user-list-filtering"></a>Filtrado de la lista de usuarios

Las funcionalidades de filtrado se han mejorado para proporcionar más opciones de filtrado en las páginas **Todos los usuarios** y **Usuarios eliminados**. Ahora puede filtrar por varias propiedades simultáneamente y por más propiedades.

### <a name="filtering-all-users-list"></a>Filtrado de la lista Todos los usuarios

A continuación se muestran las propiedades que se pueden filtrar en la página **Todos los usuarios**:

- Tipo de usuario: miembro, invitado y ninguno
- Estado de Directorio sincronizado: sí/no
- Tipo de creación: Invitación, Correo electrónico comprobado y Cuenta local
- Hora de creación: últimos 7, 14, 30, 90, 360 o hace más de 360 días
- Puesto: escriba el puesto
- Departamento: escriba un nombre de departamento
- Group: búsqueda de un grupo
- Estado de invitación: Aceptación pendiente y Aceptadas
- Nombre de dominio: escriba un nombre de dominio
- Nombre de la empresa: escriba un nombre de compañía
- Unidad administrativa: seleccione esta opción para restringir el ámbito de los usuarios que ve a una sola unidad administrativa. Para obtener más información, consulte [Versión preliminar de la administración de unidades administrativas](../roles/administrative-units.md).

### <a name="filtering-deleted-users-list"></a>Filtrado de la lista Usuarios eliminados

La página **Usuarios eliminados** tiene más filtros que no están en la página **Todos los usuarios**. A continuación se muestran las propiedades que se pueden filtrar en la página **Usuarios eliminados**:

- Tipo de usuario: miembro, invitado y ninguno
- Estado de Directorio sincronizado: sí/no
- Tipo de creación: Invitación, Correo electrónico comprobado y Cuenta local
- Hora de creación: últimos 7, 14, 30, 90, 360 o hace más de 360 días
- Puesto: escriba el puesto
- Departamento: escriba un nombre de departamento
- Estado de invitación: Aceptación pendiente y Aceptada
- Fecha de eliminación: últimos 7, 14 o 30 días
- Nombre de dominio: escriba un nombre de dominio
- Nombre de la empresa: escriba un nombre de compañía
- Fecha de eliminación permanente: últimos 7, 14 o 30 días

## <a name="user-list-sorting"></a>Ordenación de la lista de usuarios

Ahora puede ordenar por nombre y nombre principal de usuario en las páginas **Todos los usuarios** y **Usuarios eliminados**. También puede ordenar por fecha de eliminación en la lista **Usuarios eliminados**.

## <a name="user-list-counts"></a>Recuentos de listas de usuarios

Puede ver el número total de usuarios en las páginas **Todos los usuarios** y **Usuarios eliminados**. Al buscar o filtrar las listas, el recuento se actualiza para reflejar el número total de usuarios encontrados.

![Ilustración de los recuentos de listas de usuarios en la página Todos los usuarios](./media/users-search-enhanced/user-list-sorting.png)

## <a name="frequently-asked-questions-faq"></a>Preguntas más frecuentes

Pregunta | Respuesta
-------- | ------
¿Por qué se sigue mostrando el usuario eliminado cuando se ha superado la fecha de eliminación permanente? | La fecha de eliminación permanente se muestra en la zona horaria UTC, por lo que es posible que no coincida con la zona horaria actual. Además, esta fecha es la más temprana después de la cual se eliminará de forma permanente al usuario de la organización, por lo que todavía puede estar en proceso. Los usuarios eliminados permanentemente se quitarán de forma automática de la lista.
¿Qué ocurre con las funcionalidades de operaciones masivas para usuarios e invitados? | Las operaciones masivas siguen estando disponibles para usuarios e invitados; por ejemplo, Creación masiva, Invitación masiva, Eliminación masiva y Descargar usuarios. Simplemente se han fusionado en un menú denominado **Operaciones masivas**. En la parte superior de la página **Todos los usuarios**, puede encontrar las opciones de **Operaciones masivas**.
¿Qué ha ocurrido con la columna Origen? | La columna **Origen** se ha reemplazado por otras columnas que proporcionan información similar, a la vez que permiten filtrar esos valores por separado. Por ejemplo, **Tipo de creación**, **Directorio sincronizado** y **Emisor de entidades**.
¿Qué ha ocurrido con la columna Nombre de usuario? | La columna **Nombre de usuario** sigue estando, solo que ahora se llama **Nombre principal de usuario**. Este nombre refleja mejor la información contenida en esa columna. También observará que ahora se muestra el nombre principal de usuario completo para los invitados B2B. Esto coincide con lo que obtendría en Microsoft Graph.  

## <a name="next-steps"></a>Pasos siguientes

Operaciones de usuario

- [Add or change profile information](../fundamentals/active-directory-users-profile-azure-portal.md) (Incorporación o modificación de la información del perfil)
- [Adición o eliminación de usuarios](../fundamentals/add-users-azure-active-directory.md)

Operaciones masivas

- [Descarga de la lista de usuarios](users-bulk-download.md)
- [Adición masiva de usuarios](users-bulk-add.md)
- [Eliminación masiva de usuarios](users-bulk-delete.md)
- [Restauración de usuarios masiva](users-bulk-restore.md)
