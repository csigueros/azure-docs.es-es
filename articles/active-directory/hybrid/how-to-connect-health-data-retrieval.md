---
title: Instrucciones para la recuperación de datos de Azure AD Connect Health | Microsoft Docs
description: En esta página se describe cómo recuperar datos de Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/02/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 435cc73d9c2a91a9c72bf07258a15dced581d580
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113110068"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>Instrucciones para la recuperación de datos de Azure AD Connect Health

En este documento se describe cómo usar Azure AD Connect para recuperar datos de Azure AD Connect Health.

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>Recupere todas las direcciones de correo electrónico de los usuarios configurados para las alertas de estado.

Siga estos pasos para recuperar las direcciones de correo electrónico de todos los usuarios que están configurados en Azure AD Connect Health para recibir alertas.

1.  Comience en la hoja Azure Active Directory Connect Health y seleccione **Servicios de sincronización** en la barra de navegación izquierda.
 ![Servicios de sincronización](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  Haga clic en el icono **Alertas**.</br>
 ![Alerta](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  Haga clic en **Configuración de notificación**.
 ![Notificación](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  En la hoja **Configuración de notificación**, encontrará la lista de direcciones de correo electrónico que se han habilitado como destinatarios para las notificaciones de las alertas de estado.
 ![Correos electrónicos](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>Recuperación de las cuentas marcadas con intentos de contraseña incorrecta de AD FS

Utilice los pasos siguientes para recuperar las cuentas marcadas con intentos de contraseña incorrecta de AD FS.

1.  En la hoja Azure Active Directory Health, seleccione **Errores de sincronización**.
 ![Errores de sincronización](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  En la hoja **Errores de sincronización**, haga clic en **Exportar**. Se exportará una lista de los errores de sincronización registrados.
 ![Exportarar](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>Pasos siguientes
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalación del agente de Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operaciones de Azure AD Connect Health](how-to-connect-health-operations.md)
* [Preguntas más frecuentes de Azure AD Connect Health](reference-connect-health-faq.yml)
* [Historial de versiones de Azure AD Connect Health](reference-connect-health-version-history.md)