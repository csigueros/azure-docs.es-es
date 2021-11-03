---
title: Personalización de idioma en flujos de usuario de Azure AD
description: Más información acerca de cómo personalizar la experiencia de lenguaje en los flujos de usuario en Azure Active Directory.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf645226de725d19af859118c43c336e5f542804
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067108"
---
# <a name="language-customization-in-azure-active-directory"></a>Personalización de idioma de Azure Active Directory

La personalización de idioma en Azure Active Directory (Azure AD) permite que el flujo de usuario albergue distintos idiomas a fin de satisfacer las necesidades del usuario. Microsoft proporciona las traducciones de [36 idiomas](#supported-languages). Incluso si su experiencia se proporciona únicamente para un idioma, puede personalizar los nombres de atributos en la página de la colección de atributos.

## <a name="how-language-customization-works"></a>¿Cómo funciona la personalización de idioma?

De manera predeterminada, la personalización de idioma está habilitada para que los usuarios se registren a fin de garantizar una experiencia de registro coherente. Puede usar idiomas para modificar las cadenas que se muestran a los usuarios como parte del proceso de la colección de atributos durante el registro.

> [!NOTE]
> Si va a usar atributos de usuario personalizados, debe proporcionar sus propias traducciones. Para más información, consulte [Personalización de las cadenas](#customize-your-strings).

## <a name="customize-your-strings"></a>Personalización de las cadenas

La personalización de idioma le permite personalizar cualquier cadena del flujo de usuario.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador de Azure AD.
2. En **Servicios de Azure**, seleccione **Azure Active Directory**.
3. En el menú de la izquierda, seleccione **External Identities**.
4. Seleccione **Flujos de usuario**.
3. Seleccione el flujo de usuario que quiere habilitar para las traducciones.
4. Seleccione **Idiomas**.
5. En la página **Idiomas** del flujo de usuario, seleccione un idioma que desee personalizar.
6. Expanda la **página Colección de atributos**.
7. Seleccione **Descargar valores predeterminados** (o **Descargar invalidaciones** si ha editado anteriormente este idioma).

Estos pasos le proporcionan un archivo JSON que puede usar para comenzar a editar las cadenas.

### <a name="change-any-string-on-the-page"></a>Cambio de cualquier cadena en la página

1. Abra el archivo JSON descargado según las instrucciones anteriores en un editor JSON.
1. Busque el elemento que desea cambiar. Puede encontrar el valor de `StringId` de la cadena que busca o buscar el atributo `Value` que quiere cambiar.
1. Actualice el atributo `Value` con el que quiere que se muestre.
1. Para cada cadena que desea cambiar, cambie `Override` a `true`.
1. Guarde el archivo y cargue los cambios. (Puede encontrar el control de carga en el mismo lugar que donde descargó el archivo JSON).

> [!IMPORTANT]
> Si necesita reemplazar una cadena, asegúrese de establecer el valor `Override` en `true`. Si no se cambia el valor, se omite la entrada.

### <a name="change-extension-attributes"></a>Cambio de los atributos de extensión

Si quiere cambiar la cadena de un atributo de usuario personalizado, o quiere agregar una a JSON, debe estar en el siguiente formato:

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Reemplace `<ExtensionAttribute>` por el nombre de su atributo de usuario personalizado.

Reemplace `<ExtensionAttributeValue>` por la nueva cadena que se mostrará.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Entrega de una lista de valores mediante LocalizedCollections

Si desea proporcionar una lista establecida de valores para respuestas, debe crear un atributo `LocalizedCollections`. `LocalizedCollections` es una matriz de pares de `Name` y `Value`. El orden de los elementos será el orden en el que se muestran. Para agregar `LocalizedCollections`, utilice el siguiente formato:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` es el atributo de usuario para el que este atributo `LocalizedCollections` es una respuesta.
* `Name` es el valor que se muestra al usuario.
* `Value` es lo que se devuelve en la notificación cuando se selecciona esta opción.

### <a name="upload-your-changes"></a>Carga de los cambios

1. Una vez completados los cambios en el archivo JSON, vuelva al inquilino.
1. Seleccione **Flujos de usuario** y haga clic en el flujo de usuario que desea habilitar para las traducciones.
1. Seleccione **Idiomas**.
1. Seleccione el idioma al que quiere traducir.
1. Seleccione la **página Colección de atributos**.
1. Seleccione el icono de carpeta y el archivo JSON para cargar.

Este cambio se guarda en el flujo de usuario automáticamente.

## <a name="additional-information"></a>Información adicional

### <a name="page-ui-customization-labels-as-overrides"></a>Etiquetas de personalización de la interfaz de usuario de página como invalidaciones

Cuando se habilita la personalización de idioma, las ediciones anteriores de etiquetas que usan la personalización de la interfaz de usuario de página se almacenan en un archivo JSON para inglés (en). Para seguir cambiando las etiquetas y otras cadenas, cargue los recursos de idioma en Personalización de idioma.

### <a name="up-to-date-translations"></a>Actualización de traducciones

Microsoft se compromete a proporcionar las traducciones más actualizadas para que haga uso de ellas. Microsoft mejora continuamente las traducciones y garantiza su cumplimiento. Microsoft identificará errores y cambios en la terminología global y creará actualizaciones que funcionen perfectamente en su flujo de usuario.

### <a name="support-for-right-to-left-languages"></a>Compatibilidad con idiomas que se leen de derecha a izquierda

Microsoft no proporciona actualmente compatibilidad con idiomas que se leen de derecha a izquierda. Para ello, puede usar configuraciones regionales personalizadas y CSS para cambiar la manera en la que se muestran las cadenas. Si necesita esta característica, vote por ella en [Comentarios de Azure](https://feedback.azure.com/d365community/idea/10a7e89c-c325-ec11-b6e6-000d3a4f0789).

### <a name="social-identity-provider-translations"></a>Traducciones de proveedores de identidades sociales

Microsoft proporciona el parámetro OIDC `ui_locales` a los inicios de sesión de redes sociales. Pero algunos proveedores de identidades de redes sociales, incluidas Facebook y Google, no los respetan.

### <a name="browser-behavior"></a>Comportamiento del explorador

Tanto Chrome como Firefox solicitan su idioma establecido. Si es un idioma admitido, se muestra antes el valor predeterminado. Microsoft Edge no solicita actualmente un idioma y va directamente al predeterminado.

## <a name="supported-languages"></a>Idiomas compatibles

Azure AD incluye compatibilidad con los idiomas siguientes. Azure AD proporciona los idiomas de flujo de usuario. [Azure AD MFA](../authentication/concept-mfa-howitworks.md) proporciona los idiomas de notificación de autenticación multifactor (MFA).

| Idioma              | Código de lenguaje | Flujos de usuario         | Notificaciones de MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Árabe                | ar            | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Búlgaro             | bg            | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Bengalí                | bn            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![X indica No.](./media/user-flow-customize-language/no.png) |
| Catalán               | ca            | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Checo                 | cs            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Danés                | da            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Alemán                | de            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Griego                 | el            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Inglés               | en            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Español               | es            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Estonio              | et            | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Vasco                | eu            | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Finés               | fi            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Francés                | fr            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Gallego              | gl            | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Gujarati              | gu            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![X indica No.](./media/user-flow-customize-language/no.png) |
| Hebreo                | he            | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Hindi                 | hi            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Croata              | hr            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Húngaro             | hu            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Indonesio            | id            | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Italiano               | it            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Japonés              | ja            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Kazajo                | kk            | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Canarés               | kn            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![X indica No.](./media/user-flow-customize-language/no.png) |
| Coreano                | ko            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Lituano            | lt            | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Letón               | lv            | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Malayalam             | ml            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![X indica No.](./media/user-flow-customize-language/no.png) |
| Maratí               | mr            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![X indica No.](./media/user-flow-customize-language/no.png) |
| Malayo                 | ms            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Noruego Bokmal      | nb            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![X indica No.](./media/user-flow-customize-language/no.png) |
| Neerlandés                 | nl            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Noruego             | no            | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Punjabi               | pa            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![X indica No.](./media/user-flow-customize-language/no.png) |
| Polaco                | pl            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Portugués (Brasil)   | pt-br         | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Portugués (Portugal) | pt-pt         | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Rumano              | ro            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Ruso               | ru            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Eslovaco                | sk            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Esloveno             | sl            | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Serbio (cirílico)    | sr-cryl-cs    | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Serbio (latino)       | sr-latn-cs    | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Sueco               | sv            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Tamil                 | ta            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![X indica No.](./media/user-flow-customize-language/no.png) |
| Telugu                | te            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![X indica No.](./media/user-flow-customize-language/no.png) |
| Tailandés                  | th            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Turco               | tr            | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Ucraniano             | uk            | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Vietnamita            | vi            | ![X indica No.](./media/user-flow-customize-language/no.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Chino (simplificado)  | zh-hans       | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
| Chino (tradicional) | zh-hant       | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) | ![Marca de verificación verde.](./media/user-flow-customize-language/yes.png) |
