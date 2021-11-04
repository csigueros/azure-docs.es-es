---
title: Administración de las directivas de suscripción de Azure
description: Aprenda a administrar las directivas de suscripción de Azure para controlar el movimiento de suscripciones de Azure desde los directorios y hacia estos.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/28/2021
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 2530cdd36a2844c5b311e97d9747c8734d808206
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464934"
---
# <a name="manage-azure-subscription-policies"></a>Administración de las directivas de suscripción de Azure

Este artículo le ayuda a configurar las directivas de suscripción de Azure para que las operaciones de suscripción controlen el movimiento de suscripciones de Azure desde los directorios y hacia estos.

## <a name="prerequisites"></a>Requisitos previos

- Solo los [administradores globales](../../active-directory/roles/permissions-reference.md#global-administrator) de directorios pueden editar las directivas de suscripción. Antes de editar las directivas de suscripción, el administrador global debe [elevar los privilegios de acceso para administrar todas las suscripciones y grupos de administración de Azure](../../role-based-access-control/elevate-access-global-admin.md). Después de eso, el administrador podrá editar las directivas de suscripción.
- Todos los demás usuarios solo pueden leer la configuración de directiva actual.

## <a name="available-subscription-policy-settings"></a>Configuración de directiva de suscripción disponible

Use la siguiente configuración de directiva para controlar el traslado de suscripciones de Azure desde los directorios y hacia estos.

### <a name="subscriptions-leaving-aad-directory"></a>Suscripciones que salen del directorio de AAD

La directiva permite o impide que los usuarios trasladen suscripciones fuera del directorio actual. Los [propietarios de la suscripción](../../role-based-access-control/built-in-roles.md#owner) pueden [cambiar el directorio de una suscripción de Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) a otro del que sean miembros. Esto plantea desafíos de gobernanza, por lo que los administradores globales pueden permitir que los usuarios cambien el directorio o no.

### <a name="subscriptions-entering-aad-directory"></a>Suscripciones que entran en el directorio de AAD

La directiva permite o impide que los usuarios de otros directorios, que tienen acceso al directorio actual, trasladen las suscripciones al directorio actual. Los [propietarios de la suscripción](../../role-based-access-control/built-in-roles.md#owner) pueden [cambiar el directorio de una suscripción de Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) a otro del que sean miembros. Esto plantea desafíos de gobernanza, por lo que los administradores globales pueden permitir que los usuarios cambien el directorio o no.

### <a name="exempted-users"></a>Usuarios exentos

Por motivos de gobernanza, los administradores globales pueden bloquear todos los traslados de los directorios de la suscripción hacia el directorio actual o fuera de este. Sin embargo, es posible que quieran permitir que usuarios específicos realicen cualquiera de estas operaciones. En cualquier caso, pueden configurar una lista de usuarios exentos que permita a los usuarios omitir la configuración de directiva que se aplica a todos los demás usuarios.

## <a name="setting-subscription-policy"></a>Establecimiento de la directiva de suscripción

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Vaya a **Suscripciones**. **Administrar directivas** aparece en la barra de comandos.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" alt-text="Captura de pantalla que muestra la opción Administrar directivas en Suscripciones." lightbox="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" :::
1. Seleccione **Administrar directivas** para ver los detalles sobre las directivas de suscripción actuales establecidas para el directorio. Un administrador global con [permisos elevados](../../role-based-access-control/elevate-access-global-admin.md) puede realizar modificaciones en la configuración, entre las que se incluyen la adición o eliminación de usuarios exentos.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies.png" alt-text="Captura de pantalla que muestra la configuración de directiva específica y los usuarios exentos." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies.png" :::
1. Seleccione **Save Changes** (Guardar cambios) en la parte inferior. Los cambios entrarán en vigor inmediatamente.

## <a name="read-subscription-policy"></a>Leer directiva de suscripción

Los administradores no globales pueden ir al área de directivas de la suscripción para ver la configuración de directivas del directorio. No pueden realizar ninguna modificación. No pueden ver la lista de usuarios exentos por motivos de privacidad. Pueden ver a sus administradores globales para solicitar cambios en las directivas siempre y cuando la configuración del directorio lo permita.

:::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" alt-text="Captura de pantalla que muestra la opción Administrar directivas en Suscripciones como lector." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" :::

## <a name="next-steps"></a>Pasos siguientes

- Lea la [documentación sobre Azure Cost Management + Billing](../index.yml).