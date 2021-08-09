---
title: 'Procedimientos recomendados para el acceso con privilegios mínimos en Azure AD: plataforma de identidad de Microsoft'
description: Obtenga información sobre un conjunto de procedimientos recomendados e instrucciones generales para los privilegios mínimos.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/26/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: yuhko, saumadan, marsma
ms.openlocfilehash: db2eb2dcda894b997f485795ab62d983966a7f2f
ms.sourcegitcommit: eb20dcc97827ef255cb4ab2131a39b8cebe21258
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/03/2021
ms.locfileid: "111372777"
---
# <a name="best-practices-for-least-privileged-access-for-applications"></a>Procedimientos recomendados para el acceso con privilegios mínimos de las aplicaciones

El principio de privilegios mínimos es un concepto de seguridad de la información, que aplica la idea de que se debe conceder a los usuarios y las aplicaciones el nivel mínimo de acceso requerido para realizar las tareas necesarias. Comprender el principio de privilegios mínimos le ayuda a crear aplicaciones de confianza para sus clientes.

La adopción de privilegios mínimos es algo más que una buena práctica de seguridad. El concepto ayuda a conservar la integridad y la seguridad de los datos. También protege la privacidad de los datos y reduce los riesgos ya que impide que las aplicaciones tengan un acceso a los datos mayor de lo absolutamente necesario. En un nivel más amplio, la adopción del principio de privilegios mínimos es una de las formas en que las organizaciones pueden adoptar la seguridad proactiva con [Confianza cero](https://www.microsoft.com/security/business/zero-trust).

En este artículo se describe un conjunto de procedimientos recomendados que pueden usar para adoptar el principio de privilegios mínimos con el fin de que las aplicaciones sean más seguras para los usuarios finales. Comprenderá los siguientes aspectos de los privilegios mínimos:
- Funcionamiento del consentimiento con permisos
- Significado de que una aplicación tenga demasiados privilegios o privilegios mínimos
- Enfoque de los privilegios mínimos como desarrollador
- Enfoque de privilegios mínimos como organización

## <a name="using-consent-to-control-access-permissions-to-data"></a>Uso del consentimiento para controlar los permisos de acceso a los datos

El acceso a los datos protegidos requiere el [consentimiento](../develop/application-consent-experience.md#consent-and-permissions) del usuario final. Cada vez que una aplicación que se ejecuta en el dispositivo del usuario solicita acceso a los datos protegidos, la aplicación debe solicitar el consentimiento del usuario antes de conceder acceso a dichos datos. El usuario final debe conceder (o denegar) el consentimiento para el permiso solicitado antes de que la aplicación pueda avanzar. Como desarrollador de aplicaciones, es mejor solicitar permiso de acceso con los privilegios mínimos.

:::image type="content" source="./media/least-privilege-best-practice/api-permissions.png" alt-text="Captura de pantalla de Azure Portal que muestra el panel de permisos de API de un registro de aplicaciones.":::

## <a name="overprivileged-and-least-privileged-applications"></a>Aplicaciones con demasiados privilegios y con privilegios mínimos

Una aplicación con demasiados privilegios puede tener una de las siguientes características:
- **Permisos no usados**: una aplicación puede tener permisos no usados cuando no puede realizar llamadas API que usan todos los permisos que se le han concedido. Por ejemplo, en [MS Graph](/graph/overview), puede que una aplicación solo lea archivos de OneDrive (con el permiso "*Files.Read.All*"), pero también se le haya concedido el permiso "*Calendars.Read*", a pesar de no integrarse con ninguna API de calendario.
- **Permisos reducibles**: una aplicación tiene un permiso reducible cuando el permiso concedido tiene un reemplazo con menos privilegios que puede completar la llamada API deseada. Por ejemplo, una aplicación que solo lee perfiles de usuario, pero a la que se le ha concedido "*User.ReadWrite.All*" puede considerarse que tiene demasiados privilegios. En este caso, en su lugar, se debe conceder "*User.Read.All*" a la aplicación, que es el permiso con privilegios mínimos necesario para satisfacer la solicitud.

Para que una aplicación se considere con privilegios mínimos, debe tener:
- **Solo los permisos suficientes**: conceda solo el conjunto mínimo de permisos requerido por un usuario final de una aplicación, servicio o sistema para realizar las tareas necesarias.

## <a name="approaching-least-privilege-as-an-application-developer"></a>Aproximación a privilegios mínimos como desarrollador de aplicaciones

Como desarrollador, tiene la responsabilidad de contribuir a la seguridad de los datos del cliente. Al desarrollar las aplicaciones, debe adoptar el principio de privilegios mínimos. Se recomienda seguir estos pasos para evitar que la aplicación tenga demasiados privilegios:
- Comprenda completamente los permisos necesarios para las llamadas API que la aplicación debe realizar.
- Comprenda el permiso con privilegios mínimos para cada llamada API que la aplicación necesita realizar mediante el [Probador de Graph](https://developer.microsoft.com/graph/graph-explorer).
- Busque los [permisos](/graph/permissions-reference) correspondientes desde los que tienen menos privilegios a los que tienen más.
- Quite los conjuntos de permisos duplicados en los casos en los que la aplicación realice llamadas API que tengan permisos superpuestos.
- Aplique solo el conjunto de permisos con privilegios mínimos a la aplicación eligiendo el permiso con privilegios mínimos en la lista de permisos.

## <a name="approaching-least-privilege-as-an-organization"></a>Enfoque de privilegios mínimos como organización

A menudo, las organizaciones dudan en modificar las aplicaciones existentes, ya que pueden afectar a las operaciones empresariales, pero esto supone un desafío cuando los permisos ya concedidos tienen demasiados privilegios y deben revocarse. Como organización, es un procedimiento recomendado comprobar y revisar los permisos con regularidad. Se recomienda seguir estos pasos para que las aplicaciones sigan siendo correctas:
- Evalúe las llamadas API que se realizan desde las aplicaciones.
- Use el [Probador de Graph](https://developer.microsoft.com/graph/graph-explorer) y la documentación de [Microsoft Graph](/graph/overview) para los permisos necesarios y los privilegios mínimos.
- Audite los privilegios que se conceden a los usuarios o las aplicaciones.
- Actualice las aplicaciones con el conjunto de permisos con privilegios mínimos.
- Realice una revisión periódica de los permisos para asegurarse de que todos los permisos autorizados siguen siendo pertinentes.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre el consentimiento y los permisos en Azure Active Directory, vea [Descripción de las experiencias de consentimiento de la aplicación de Azure AD](../develop/application-consent-experience.md).
- Para más información sobre los permisos y el consentimiento en la identidad de Microsoft, vea [Permisos y consentimiento en la plataforma de identidad de Microsoft](../develop/v2-permissions-and-consent.md).
- Para más información sobre la Confianza cero, vea [Centro de implementación de Confianza cero](/security/zero-trust/).
