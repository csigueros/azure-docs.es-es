---
title: Uso de Azure Policy para aplicar la autenticación exclusiva de Azure Active Directory
description: Este artículo le guía en el uso de Azure Policy para aplicar la autenticación exclusiva de Azure Active Directory (Azure AD) con Azure SQL Database y Azure SQL Managed Instance.
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/22/2021
ms.openlocfilehash: 24cb978a20bc3a6e2385ed37994389abb6d32a32
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699542"
---
# <a name="using-azure-policy-to-enforce-azure-active-directory-only-authentication-with-azure-sql"></a>Uso de Azure Policy para aplicar la autenticación exclusiva de Azure Active Directory con Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> La **autenticación exclusiva de Azure AD** y la característica de Azure Policy asociada que se describen en este artículo se encuentran en **versión preliminar pública**. 

Este artículo le guía en la creación de una directiva de Azure Policy que aplicará la autenticación exclusiva de Azure AD cuando los usuarios creen una instancia de Azure SQL Managed Instance o un [servidor lógico](logical-servers.md) para Azure SQL Database. Para más información sobre la autenticación exclusiva de Azure AD durante la creación de recursos, consulte [Creación de un servidor con autenticación solo de Azure AD habilitada en Azure SQL](authentication-azure-ad-only-authentication-create-server.md).

En este artículo aprenderá a:

> [!div class="checklist"]
> - Crear una directiva de Azure Policy que aplique la creación de servidores lógicos o instancias administradas con la [autenticación exclusiva de Azure AD](authentication-azure-ad-only-authentication.md) habilitada
> - Comprobar el cumplimiento de Azure Policy

## <a name="prerequisite"></a>Requisito previo

- Tener permisos para administrar Azure Policy. Para más información, consulte [Permisos de RBAC de Azure en Azure Policy](/azure/governance/policy/overview#azure-rbac-permissions-in-azure-policy).

## <a name="create-an-azure-policy"></a>Creación de una directiva de Azure Policy

Para empezar, cree una directiva de Azure Policy que aplique el aprovisionamiento de SQL Database o Instancia administrada con la autenticación exclusiva de Azure AD habilitada.

1. Vaya a [Azure Portal](https://portal.azure.com).
1. Busque el servicio **Policy**.
1. En la sección Creación, seleccione **Definiciones**.
1. En el cuadro **Buscar**, busque *Autenticación exclusiva de Azure Active Directory*.

   Hay dos directivas integradas disponibles para aplicar la autenticación exclusiva de Azure AD. Una es para SQL Database y la otra es para Instancia administrada.

   - Azure SQL Database debe tener habilitada solo la autenticación de Azure Active Directory
   - Azure SQL Managed Instance debe tener habilitada solo la autenticación de Azure Active Directory

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/policy-azure-ad-only-authentication.png" alt-text="Captura de pantalla de Azure Policy para la autenticación exclusiva de Azure AD":::

1. Seleccione el nombre de directiva para el servicio. En este ejemplo, usaremos Azure SQL Database. Seleccione **Azure SQL Database debe tener habilitada la autenticación exclusiva de Azure Active Directory**.
1. Seleccione **Asignar** en el nuevo menú.

   > [!NOTE]
   > El script JSON del menú muestra la definición de directiva integrada que se puede usar como plantilla para crear una directiva de Azure Policy personalizada para SQL Database. De forma predeterminada, se estable en `Audit`.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/assign-policy-azure-ad-only-authentication.png" alt-text="Captura de pantalla de la asignación de Azure Policy para la autenticación exclusiva de Azure AD":::

1. En la pestaña **Básico**, agregue un **Ámbito** mediante el selector ( **...** ) situado al lado del cuadro.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/selecting-scope-policy-azure-ad-only-authentication.png" alt-text="Captura de pantalla de la selección del ámbito de Azure Policy para la autenticación exclusiva de Azure AD":::

1. En el panel **Ámbito**, seleccione la **Suscripción** en el menú desplegable y seleccione un **Grupo de recursos** para esta directiva. Cuando haya terminado, use el botón **Seleccionar** para guardar la selección.

   > [!NOTE]
   > Si no selecciona un grupo de recursos, la directiva se aplicará a toda la suscripción.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/adding-scope-policy-azure-ad-only-authentication.png" alt-text="Captura de pantalla de la adición del ámbito de Azure Policy para la autenticación exclusiva de Azure AD":::

1. Una vez de vuelta en la pestaña **Básico**, personalice el campo **Nombre de asignación** y proporcione una **Descripción** opcional. Asegúrese de que **Cumplimiento de directivas** esté establecido en **Habilitado**.
1. Vaya a la pestaña **Parameters** (Parámetros). Anule la selección de la opción **Only show parameters that require input** (Mostrar solo los parámetros que requieren entrada).
1. En **Effect** (Efecto), seleccione **Deny** (Denegar). Esta configuración impedirá la creación de un servidor lógico sin la autenticación exclusiva de Azure AD habilitada.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/deny-policy-azure-ad-only-authentication.png" alt-text="Captura de pantalla del parámetro de efecto de Azure Policy para la autenticación exclusiva de Azure AD":::

1. En la pestaña **Non-compliance messages** (Mensajes de no cumplimiento), puede personalizar el mensaje de la directiva que se muestra si se ha producido una infracción de la directiva. El mensaje permitirá a los usuarios saber qué directiva se ha aplicado durante la creación del servidor.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/non-compliance-message-policy-azure-ad-only-authentication.png" alt-text="Captura de pantalla del mensaje de no cumplimiento de Azure Policy para la autenticación exclusiva de Azure AD":::

1. Seleccione **Revisar + crear**. Revise la directiva y seleccione el botón **Crear**.

> [!NOTE]
> La aplicación de la directiva recién creada puede tardar algún tiempo.

## <a name="check-policy-compliance"></a>Comprobar cumplimiento de directivas

Puede consultar el valor de **Cumplimiento** en el servicio **Policy** para ver el estado de cumplimiento.

Busque el nombre de asignación que ha dado anteriormente a la directiva.

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/compliance-policy-azure-ad-only-authentication.png" alt-text="Captura de pantalla del cumplimiento de Azure Policy para la autenticación exclusiva de Azure AD":::

Una vez creado el servidor lógico con la autenticación exclusiva de Azure AD, el informe de directiva aumentará el contador en el objeto visual **Resources by compliance state** (Recursos por estado de cumplimiento). Podrá ver qué recursos son compatibles o no compatibles.

Si el grupo de recursos que se ha elegido para cubrir la directiva contiene servidores ya creados, el informe de directiva indicará los recursos que son compatibles y no compatibles.

> [!NOTE]
> La actualización del informe de cumplimiento puede tardar algún tiempo. Los cambios relacionados con la creación de recursos o la configuración de la autenticación exclusiva de Azure AD no se notifican inmediatamente.    

## <a name="provision-a-server"></a>Aprovisionamiento de un servidor

A continuación, puede intentar aprovisionar un servidor lógico o una instancia administrada en el grupo de recursos al que asignó la directiva de Azure Policy. Si la autenticación exclusiva de Azure AD está habilitada durante la creación del servidor, el aprovisionamiento se realizará correctamente. Si la autenticación exclusiva de Azure AD no está habilitada, se producirá un error en el aprovisionamiento.

Para más información, consulte [Creación de un servidor con autenticación solo de Azure AD habilitada en Azure SQL](authentication-azure-ad-only-authentication-create-server.md).

## <a name="next-steps"></a>Pasos siguientes

- Introducción a [Azure Policy para autenticación exclusiva de Azure Active Directory con Azure SQL](authentication-azure-ad-only-authentication-policy.md)
- [Creación de un servidor con la autenticación solo de Azure AD habilitada en Azure SQL](authentication-azure-ad-only-authentication-create-server.md)
- Introducción a la [Autenticación solo de Azure AD con Azure SQL](authentication-azure-ad-only-authentication.md)
