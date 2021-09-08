---
title: 'Inicio de sesión único de la integración de Azure con Logz.io: soluciones de asociados de Azure'
description: Aprenda a configurar el inicio de sesión único para la integración de Azure con Logz.io.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 08/17/2021
ms.author: tomfitz
ms.openlocfilehash: 04a2616e46f526e9601e102c8ecb4104b6595164
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122428587"
---
# <a name="set-up-logzio-single-sign-on"></a>Configuración del inicio de sesión único de Logz.io

En este artículo se describe cómo configurar el inicio de sesión único (SSO) en Azure Active Directory. SSO para la integración de Logz.io es opcional.

## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

Para usar la característica de SSO de Lenguaje de marcado de aserción de seguridad dentro del recurso Logz.io, debe configurar una aplicación empresarial.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En el menú del portal, seleccione **Azure Active Directory**  o busque _Azure Active Directory_.
1. Vaya a **Administrar** > **aplicaciones empresariales** y seleccione el botón **Nueva aplicación**.
1. Busque _Logz.io_ y seleccione la aplicación SAML denominada **Logz.io - Azure AD Integration** y, a continuación, seleccione **Crear**.

   :::image type="content" source="./media/sso-setup/gallery.png" alt-text="Examine la galería de Azure Active Directory.":::

1. En **Información general**, copie el **id. de la aplicación** de la aplicación SSO.

   Anote el _id. de la aplicación,_ ya que lo usará en un paso posterior para configurar SAML.

   :::image type="content" source="./media/sso-setup/app-id.png" alt-text="Copie el id. de la aplicación":::.

1. Seleccione el icono denominado **2. Configure el inicio de sesión único**.

   :::image type="content" source="./media/sso-setup/setup.png" alt-text="Configuración del inicio de sesión único":::

1. Seleccione el protocolo **SAML** como método de inicio de sesión único.

   :::image type="content" source="./media/sso-setup/saml.png" alt-text="Inicio de sesión único SAML":::

1. En la configuración de SAML, seleccione valores existentes o use los cuadros de texto para escribir los campos obligatorios **Identificador** y **Dirección URL de respuesta**. Para crear nuevas entradas, use el _id. de la aplicación_ que se copió en un paso anterior.

   Use los patrones siguientes para agregar nuevos valores:

   - **Identificador**: `urn:auth0:logzio:<Application ID>`
   - **URL de respuesta**: `https://logzio.auth0.com/login/callback?connection=<Application ID>`

   :::image type="content" source="./media/sso-setup/saml-config.png" alt-text="Configuración básica de SAML":::

1. En la solicitud **Guardar configuración de inicio de sesión único**, seleccione **Sí** y, luego, **Guardar**.

   Para la solicitud de SSO, seleccione **No, lo probaré más adelante**.

1. Una vez guardada la configuración de SAML, vaya a **Administrar** > **propiedades** y establezca **¿Asignación de usuario necesaria?** en **No**. Seleccione **Guardar**.

   :::image type="content" source="./media/sso-setup/properties.png" alt-text="Propiedades del inicio de sesión único":::

## <a name="next-steps"></a>Pasos siguientes

- Para solucionar los problemas de SSO, consulte [Solución de problemas](troubleshoot.md).
- Para crear una integración de Logz.io, consulte [Inicio rápido: Creación de un recurso Logz.io en Azure Portal](create.md).
