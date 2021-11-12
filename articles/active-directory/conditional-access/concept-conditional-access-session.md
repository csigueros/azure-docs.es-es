---
title: 'Controles de sesión en una directiva de acceso condicional: Azure Active Directory'
description: Qué son los controles de sesión en una directiva de acceso condicional de Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4d902a3c5471455f6f2d6cc614544aeb4e0dc39
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892085"
---
# <a name="conditional-access-session"></a>Acceso condicional: Sesión

En una directiva de acceso condicional, un administrador puede usar controles de sesión para habilitar experiencias limitadas en aplicaciones en la nube específicas.

![Directiva de acceso condicional con un control de concesión que requiere la autenticación multifactor](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Restricciones que exige la aplicación

Las organizaciones pueden usar este control para requerir que Azure AD transmita la información del dispositivo a la aplicación en la nube seleccionada. La información del dispositivo permite que las aplicaciones de nube sepan si una conexión se inicia desde un dispositivo compatible o unido al dominio y altera la experiencia de la sesión. Este control solo admite Exchange Online y SharePoint Online como aplicaciones de nube seleccionadas. Cuando se selecciona, la aplicación en la nube usa la información del dispositivo para proporcionar a los usuarios, en función del estado del dispositivo, una experiencia limitada (cuando el dispositivo no está administrado) o una experiencia completa (cuando el dispositivo está administrado y es compatible).

Para obtener más información sobre el uso y la configuración de las restricciones que exige la aplicación, consulte los siguientes artículos:

- [Habilitación del acceso limitado con SharePoint Online](/sharepoint/control-access-from-unmanaged-devices)
- [Habilitación del acceso limitado con Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Control de aplicaciones de acceso condicional

Control de aplicaciones de acceso condicional usa una arquitectura de proxy inverso y se integra de forma exclusiva con el acceso condicional de Azure AD. El acceso condicional de Azure AD permite exigir controles de acceso en las aplicaciones de la organización, en función de ciertas condiciones. Las condiciones definen quién (usuario o grupo de usuarios), qué (aplicaciones en la nube) y dónde (ubicaciones y redes) se aplica una directiva de acceso condicional. Una vez que haya determinado las condiciones, puede enrutar los usuarios a [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) donde puede proteger los datos con Control de aplicaciones de acceso condicional, mediante la aplicación de controles de acceso y de sesión.

Control de aplicaciones de acceso condicional permite supervisar y controlar las sesiones y el acceso a las aplicaciones de usuario en tiempo real, en función de las directivas de acceso y de sesión. Las directivas de acceso y de sesión se usan en el portal Cloud App Security para refinar los filtros y establecer las acciones que deben realizarse en un usuario. Con las directivas de acceso y de sesión, puede:

- Impedir la filtración de datos: puede bloquear la descarga, cortar, copiar e imprimir documentos confidenciales en, por ejemplo, dispositivos no administrados.
- Proteger en la descarga: en lugar de bloquear la descarga de documentos confidenciales, puede requerir que los documentos estén etiquetados y protegidos con Azure Information Protection. Esta acción garantiza que el documento está protegido y el acceso de usuario está restringido en una sesión de riesgo potencial.
- Impedir la carga de archivos sin etiqueta: antes de cargar, distribuir y usar un archivo confidencial, es importante asegurarse de que el archivo tiene la etiqueta y la protección adecuadas. Puede asegurarse de que los archivos sin etiqueta con contenido confidencial se bloqueen para que no se carguen hasta que el usuario clasifique el contenido.
- Supervisar el cumplimiento de las sesiones de usuario (versión preliminar): los usuarios de riesgo se supervisan cuando inician sesión en las aplicaciones y sus acciones se registran desde dentro de la sesión. Puede investigar y analizar el comportamiento del usuario para saber dónde se deben aplicar las directivas de sesión en el futuro, y en qué condiciones.
- Bloquear acceso (versión preliminar): puede bloquear de forma pormenorizada el acceso para aplicaciones y usuarios específicos en función de varios factores de riesgo. Por ejemplo, puede bloquearlos si usan certificados de cliente como forma de administración de dispositivos.
- Bloquear actividades personalizadas: algunas aplicaciones tienen escenarios únicos que conllevan riesgo, por ejemplo, el envío de mensajes con contenido confidencial en aplicaciones como Microsoft Teams o Slack. En estos tipos de escenarios, puede examinar los mensajes para detectar el contenido confidencial y bloquearlos en tiempo real.

Para obtener más información, consulte el artículo [Implementación del Control de aplicaciones de acceso condicional para aplicaciones destacadas](/cloud-app-security/proxy-deployment-aad).

## <a name="sign-in-frequency"></a>Frecuencia de inicio de sesión

La frecuencia de inicio de sesión define el período de tiempo antes de que se pida a un usuario que vuelva a iniciar sesión cuando intenta acceder a un recurso.

La configuración de la frecuencia de inicio de sesión funciona con aplicaciones que han implementado los protocolos OAUTH2 o OIDC de acuerdo con los estándares. La mayoría de aplicaciones nativas de Microsoft para Windows, Mac y dispositivos móviles que incluyen las aplicaciones web siguientes siguen la configuración.

- Word, Excel y PowerPoint Online
- OneNote Online
- Office.com
- Portal de administración de Microsoft 365
- Exchange Online
- SharePoint y OneDrive
- Cliente web de Teams
- Dynamics CRM Online
- Azure portal

Para más información, consulte el artículo [Configuración de la administración de las sesiones de autenticación con el acceso condicional](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session"></a>Sesión del explorador persistente

Una sesión persistente del explorador permite a los usuarios permanecer conectados después de cerrar y volver a abrir la ventana del explorador.

Para más información, consulte el artículo [Configuración de la administración de las sesiones de autenticación con el acceso condicional](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="customize-continuous-access-evaluation"></a>Personalización de la evaluación continua de acceso

[La evaluación continua de acceso](concept-continuous-access-evaluation.md) se habilita automáticamente como parte de las directivas de acceso condicional de una organización. Para las organizaciones que deseen deshabilitar o aplicar estrictamente la evaluación continua de acceso, esta configuración es ahora una opción en el control de sesión dentro del acceso condicional. El ámbito de las directivas de evaluación continua de acceso pueden ser todos los usuarios o usuarios y grupos específicos. Los administradores pueden hacer las siguientes selecciones al crear una nueva directiva o al editar una directiva de acceso condicional existente.

- **Deshabilitar** solo funciona cuando está seleccionado **Todas las aplicaciones en la nube**, no hay ninguna condición seleccionada y la opción **Deshabilitar** está seleccionada en **Sesión**  > **Customize continuous access evaluation** (Personalizar evaluación continua de acceso) en una directiva de acceso condicional. Puede optar por deshabilitar todos los usuarios o usuarios y grupos específicos.
- Se puede usar un **cumplimiento estricto** para reforzar aún más las ventajas de seguridad de CAE. Garantizará que cualquier evento y directiva críticos se aplicarán en tiempo real.  Hay dos escenarios adicionales en los que se aplicará CAE cuando el modo de cumplimiento estricto esté activado:
   - Los clientes no compatibles con CAE no podrán acceder a servicios compatibles con CAE.
   - El acceso se rechazará cuando la dirección IP del cliente que ve el proveedor de recursos no esté en el intervalo permitido del acceso condicional.

> [!NOTE] 
> Solo debe habilitar el cumplimiento estricto después de asegurarse de que todas las aplicaciones cliente admiten CAE y que ha incluido todas las direcciones IP que ven Azure AD y los proveedores de recursos (como Exchange Online y Azure Resource Manager) en la directiva de ubicación bajo Acceso condicional. De lo contrario, se podría bloquear a los usuarios de los inquilinos.

:::image type="content" source="media/concept-conditional-access-session/continuous-access-evaluation-session-controls.png" alt-text="Configuración de CAE en una nueva directiva de acceso condicional en Azure Portal." lightbox="media/concept-conditional-access-session/continuous-access-evaluation-session-controls.png":::

## <a name="disable-resilience-defaults-preview"></a>Deshabilitación de valores predeterminados de resistencia (versión preliminar)

Durante una interrupción, Azure AD ampliará el acceso a las sesiones existentes y aplicará directivas de acceso condicional. Si no se puede evaluar una directiva, la configuración de resistencia determina el acceso. 

Si los valores predeterminados de resistencia están deshabilitados, se deniega el acceso una vez que expiran las sesiones existentes. Para obtener más información, vea el artículo [Acceso condicional: valores predeterminados de resistencia](resilience-defaults.md).

## <a name="next-steps"></a>Pasos siguientes

- [Directivas de acceso condicional habituales](concept-conditional-access-policy-common.md)

- [Modo de solo informe](concept-conditional-access-report-only.md)
