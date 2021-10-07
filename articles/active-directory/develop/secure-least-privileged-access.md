---
title: Aumento de la seguridad de las aplicaciones con el principio de privilegios mínimos
titleSuffix: Microsoft identity platform
description: Obtenga información sobre cómo el principio de privilegios mínimos puede ayudar a aumentar la seguridad de la aplicación, sus datos y qué características de la Plataforma de identidad de Microsoft puede usar para implementar el acceso con privilegios mínimos.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: yuhko, saumadan, marsma
ms.openlocfilehash: a7e504bfd13d89ba417067faf88cf17cf12e1d0b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124786491"
---
# <a name="enhance-security-with-the-principle-of-least-privilege"></a>Mejora de la seguridad con el principio de privilegios mínimos

El principio de seguridad de la información de privilegios mínimos establece que a los usuarios y las aplicaciones se les debe conceder acceso solo a los datos y las operaciones que necesitan para realizar sus trabajos.

Siga esta guía para ayudar a reducir la superficie de ataque de su aplicación y el impacto de una vulneración de seguridad (el *radio de impacto*) en caso de que se produjera en la aplicación su aplicación integrada en la Plataforma de identidad de Microsoft.

## <a name="recommendations-at-a-glance"></a>Recomendaciones de un vistazo

- Evite las **aplicaciones con demasiados privilegios** revocando los permisos no *usados* y *reducibles*.
- Use el marco de **consentimiento** de la plataforma de identidad para requerir que una persona dé su consentimiento a la solicitud de la aplicación de acceder a datos protegidos.
- **Cree** aplicaciones teniendo en cuenta los privilegios mínimos durante todas las fases de desarrollo.
- **Audite** las aplicaciones implementadas periódicamente para identificar las aplicaciones con demasiados privilegios.

## <a name="whats-an-overprivileged-application"></a>¿Qué es una aplicación *con demasiados privilegios*?

Cualquier aplicación a la que se haya concedido un permiso **no utilizado** o **reducible** se considera que tiene "demasiados privilegios". Los permisos no usados y reducibles pueden proporcionar acceso no autorizado o no deseado a datos u operaciones que la aplicación o sus usuarios no necesitan para realizar sus tareas.

:::row:::
   :::column span="":::
      ### <a name="unused-permissions"></a>Permisos no usados

        Un permiso no utilizado es un permiso que se ha concedido a una aplicación, pero, cuando la aplicación se usa según lo previsto, no llama a la API ni a la operación expuesta por ese permiso.

        - **Ejemplo**: una aplicación muestra una lista de archivos almacenados en la cuenta de OneDrive del usuario que ha iniciado sesión llamando a Microsoft Graph API y utilizando el permiso [Files.Read](/graph/permissions-reference). Sin embargo, a la aplicación también se le ha concedido el permiso [Calendars.Read](/graph/permissions-reference#calendars-permissions) pero no proporciona características de calendario y no llama a Calendars API.

        - **Riesgo de seguridad:** los permisos sin usar suponen un riesgo de seguridad por *elevación de privilegios horizontal*. Una entidad que aproveche una vulnerabilidad de seguridad en la aplicación podría utilizar un permiso no usado para obtener acceso a una API u operación que la aplicación normalmente no admite o no permite cuando esta se usa según lo previsto.

        - **Mitigación**: quite cualquier permiso que no se utilice en las llamadas API realizadas por la aplicación.
   :::column-end:::
   :::column span="":::
      ### <a name="reducible-permissions"></a>Permisos reducibles

        Un permiso reducible es un permiso que tiene un homólogo con menos privilegios que seguiría proporcionando a la aplicación y a sus usuarios el acceso que necesitan para realizar las tareas necesarias.

        - **Ejemplo**: una aplicación muestra la información del perfil del usuario que ha iniciado sesión llamando a Microsoft Graph API, pero no admite la edición de perfiles. Sin embargo, a la aplicación se le ha concedido el permiso [User.ReadWrite.All](/graph/permissions-reference#user-permissions). El permiso *User.ReadWrite.All* se considera reducible aquí porque el permiso *User.Read.All* menos permisivo concede acceso suficiente de solo lectura a los datos del perfil de usuario.

        - **Riesgo de seguridad**: los permisos reducibles suponen un riesgo de seguridad por *elevación de privilegios vertical*. Una entidad que aproveche una vulnerabilidad de seguridad en la aplicación podría usar el permiso reducible para acceder sin autorización a datos o para realizar operaciones no permitidas normalmente por el rol de esa entidad.

        - **Mitigación**: reemplace cada permiso reducible de la aplicación por su homólogo menos permisivo que sigue permitiendo la funcionalidad prevista de la aplicación.
   :::column-end:::
:::row-end:::

Evite los riesgos de seguridad que suponen los permisos no usados y reducibles concediendo el permiso *mínimo necesario*: el permiso con el acceso menos permisivo requerido por una aplicación o usuario para realizar las tareas necesarias.

## <a name="use-consent-to-control-access-to-data"></a>Uso del consentimiento para controlar el acceso a datos

La mayoría de las aplicaciones que cree requerirán acceso a datos protegidos y el propietario de esos datos debe [dar su consentimiento](application-consent-experience.md#consent-and-permissions) a ese acceso. El consentimiento se puede conceder de varias maneras, incluido a través de un administrador de inquilinos que puede dar su consentimiento para *todos* los usuarios de un inquilino de Azure AD o por los propios usuarios de la aplicación que pueden conceder acceso.

Cada vez que una aplicación que se ejecuta en el dispositivo del usuario solicita acceso a los datos protegidos, la aplicación debe solicitar el consentimiento del usuario antes de conceder acceso a dichos datos. El usuario final debe conceder (o denegar) el consentimiento para el permiso solicitado antes de que la aplicación pueda avanzar.

:::image type="content" source="./media/least-privilege-best-practice/api-permissions.png" alt-text="Captura de pantalla de Azure Portal que muestra el panel de permisos de API de un registro de aplicaciones":::

## <a name="least-privilege-during-app-development"></a>Privilegios mínimos durante el desarrollo de aplicaciones

Como desarrollador que crea una aplicación, considere la seguridad de la aplicación y los datos de los usuarios como *su* responsabilidad.

Siga estas directrices durante el desarrollo de aplicaciones para evitar crear una aplicación con demasiados privilegios:

- Comprenda completamente los permisos necesarios para las llamadas API que la aplicación debe realizar.
- Comprenda el permiso con privilegios mínimos para cada llamada API que la aplicación necesite realizar mediante el [Probador de Graph](https://developer.microsoft.com/graph/graph-explorer).
- Busque los [permisos](/graph/permissions-reference) correspondientes desde los que tienen menos privilegios a los que tienen más.
- Quite los conjuntos de permisos duplicados en los casos en los que la aplicación realice llamadas API que tengan permisos superpuestos.
- Aplique solo el conjunto de permisos con privilegios mínimos a la aplicación eligiendo el permiso con privilegios mínimos en la lista de permisos.

## <a name="least-privilege-for-deployed-apps"></a>Privilegios mínimos para aplicaciones implementadas

A menudo, las organizaciones dudan si modificar las aplicaciones en ejecución para evitar que sus operaciones empresariales normales se vean afectadas. Sin embargo, su organización debe considerar la posibilidad de mitigar el riesgo de que ocurra o se agrave un incidente de seguridad debido a que la aplicación tenga demasiados privilegios, de forma que resulte conveniente una actualización programada de la aplicación.

Establezca estas prácticas estándar en su organización para ayudar a garantizar que las aplicaciones implementadas no tienen demasiados privilegios y no los obtendrán con el tiempo:

- Evalúe las llamadas API que se realizan desde las aplicaciones.
- Use el [Probador de Graph](https://developer.microsoft.com/graph/graph-explorer) y la documentación de [Microsoft Graph](/graph/overview) para los permisos necesarios y los privilegios mínimos.
- Audite los privilegios que se conceden a los usuarios o las aplicaciones.
- Actualice las aplicaciones con el conjunto de permisos con privilegios mínimos.
- Realice revisiones periódicas de los permisos para asegurarse de que todos los permisos autorizados siguen siendo pertinentes.

## <a name="next-steps"></a>Pasos siguientes

**Acceso a recursos protegidos y consentimiento**

Para más información sobre cómo configurar el acceso a recursos protegidos y la experiencia del usuario para dar su consentimiento para acceder a esos recursos protegidos, consulte los siguientes artículos:

- [Permisos y consentimiento en la plataforma de identidad de Microsoft](../develop/v2-permissions-and-consent.md)
- [Descripción de las experiencias de consentimiento de la aplicación de Azure AD](../develop/application-consent-experience.md)

**Confianza cero**: considere la posibilidad de emplear las medidas de privilegios mínimos descritas aquí como parte de la [estrategia de seguridad proactiva de Confianza cero](/security/zero-trust/) de su organización.
