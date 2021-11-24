---
title: Conexión de datos de Azure Active Directory a Microsoft Sentinel | Microsoft Docs
description: Obtenga información sobre cómo recopilar datos de Azure Active Directory y transmitir registros de inicio de sesión, auditoría y aprovisionamiento a Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 029fcc5d9df8ee16aa7727353763f0e4d12e2796
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716132"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-microsoft-sentinel"></a>Conexión de datos de Azure Active Directory (Azure AD) a Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> Como se indica a continuación, algunos de los tipos de registro disponibles están actualmente en **versión preliminar**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Puede usar el conector integrado de Microsoft Sentinel para recopilar datos de [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) y transmitirlos a la solución. El conector permite transmitir los siguientes tipos de registro:

- [**Registros de inicio de sesión**](../active-directory/reports-monitoring/concept-all-sign-ins.md), que contienen información sobre inicios de sesión de usuario interactivos, donde un usuario proporciona un factor de autenticación.

    Ahora, el conector de Azure AD incluye las tres categorías adicionales de registros de inicio de sesión, que se encuentran actualmente en **versión preliminar**:
    
    - [**Registros de inicios de sesión de usuario no interactivos**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins), que contienen información sobre los inicios de sesión realizados por un cliente en nombre de un usuario sin ninguna interacción ni factor de autenticación de parte del usuario.
    
    - [**Registros de inicios de sesión de entidad de servicio**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins), que contienen información sobre inicios de sesión realizados por aplicaciones y entidades de servicio que no involucran a ningún usuario. En estos inicios de sesión, la aplicación o el servicio proporcionan una credencial en su propio nombre para autenticarse o acceder a los recursos.
    
    - [**Registros de inicios de sesión de identidades administradas**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins), que contienen información sobre inicios de sesión realizados por recursos de Azure que tienen secretos administrados por Azure. Para obtener más información, consulte [¿Qué son las identidades administradas para recursos de Azure?](../active-directory/managed-identities-azure-resources/overview.md)

- [**Registros de auditoría**](../active-directory/reports-monitoring/concept-audit-logs.md), que contienen información sobre la actividad del sistema relativa a la administración de usuarios y grupos, aplicaciones administradas y actividades de directorio.

- [**Registros de aprovisionamiento**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (también en **versión preliminar**), que contienen información sobre la actividad del sistema relativa a usuarios, grupos y roles aprovisionados por el servicio de aprovisionamiento de Azure AD. 


## <a name="prerequisites"></a>Requisitos previos

- Se necesita una licencia de Azure Active Directory P1 o P2 para ingerir registros de inicio de sesión en Microsoft Sentinel. Cualquier licencia de Azure AD (gratuita/O365/P1/P2) es suficiente para ingerir los otros tipos de registro. Se pueden aplicar cargos adicionales por gigabyte en el caso de Azure Monitor (Log Analytics) y Microsoft Sentinel.

- Su usuario debe tener asignado el rol "Colaborador de Microsoft Sentinel" en el área de trabajo.

- El usuario debe tener asignados los roles Administrador global o Administrador de seguridad en el inquilino desde el que quiere transmitir los registros.

- El usuario debe tener permisos de lectura y escritura en la configuración de diagnóstico de Azure AD para poder ver el estado de la conexión. 

## <a name="connect-to-azure-active-directory"></a>Conectarse a Azure Active Directory

1. En Microsoft Sentinel, seleccione **Conectores de datos** en el menú de navegación.

1. En la galería de conectores de datos, seleccione **Azure Active Directory** y luego **Open connector page** (Abrir página del conector).

1. Active las casillas situadas junto a los tipos de registros que quiera transmitir a Microsoft Sentinel (véase anteriormente) y seleccione **Conectar**.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en **Registros**, en la sección **LogManagement**, de las tablas siguientes:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Para consultar los registros de Azure AD, escriba el nombre de tabla correspondiente en la parte superior de la ventana de consulta.

## <a name="next-steps"></a>Pasos siguientes
En este documento ha aprendido a conectar Azure Active Directory a Microsoft Sentinel. Para obtener más información sobre Microsoft Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Microsoft Sentinel](detect-threats-built-in.md).
