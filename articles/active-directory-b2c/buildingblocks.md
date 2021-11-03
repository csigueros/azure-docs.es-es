---
title: BuildingBlocks
titleSuffix: Azure AD B2C
description: Especifique el elemento BuildingBlocks de una directiva personalizada en Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 92af0923e7422e6b99f7c99526cade2f4d49686d
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "131008092"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

El elemento **BuildingBlocks** se agrega dentro del elemento [TrustFrameworkPolicy](trustframeworkpolicy.md).

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
    <DisplayControls>
      ...
    </DisplayControls>
 </BuildingBlocks>
```

El elemento **BuildingBlocks** contiene los siguientes elementos que deben especificarse en el orden definido:

- [ClaimsSchema](claimsschema.md): define los tipos de notificación a los que se puede hacer referencia como parte de la directiva. El esquema de notificaciones es el lugar en el que se declaran los tipos de notificación. Un tipo de notificación se parece a una variable en muchos lenguajes de programación. Puede usar el tipo de notificación para recopilar datos del usuario de la aplicación, recibir notificaciones de proveedores de identidades sociales, enviar y recibir datos de una API de REST personalizada o almacenar cualquier dato interno usado por la directiva personalizada.

- [Predicates y PredicateValidationsInput](predicates.md): le permiten realizar un proceso de validación para asegurarse de que solo se introducen datos bien formados en una notificación.

- [ClaimsTransformations](claimstransformations.md): contiene una lista de transformaciones de notificaciones que se pueden usar en la directiva.  Una transformación de notificaciones convierte una notificación en otra. En la transformación de notificaciones, debe especificar un método de transformación, por ejemplo:
  - Cambiar las mayúsculas y minúsculas de una notificación de cadena por la tipología especificada. Por ejemplo, cambiar una cadena en minúsculas a mayúsculas.
  - Comparar dos notificaciones y devolver una notificación con true para indicar que las notificaciones coinciden y false en caso contrario.
  - Crear una notificación de cadena a partir del parámetro proporcionado en la directiva.
  - Crear una cadena aleatoria mediante el generador de números aleatorios.
  - Dar formato a una notificación de acuerdo con la cadena de formato proporcionada. Esta transformación usa el método `String.Format` de C#.

- InputValidation: este elemento le permite realizar agregaciones booleanas similares a *and* y *or*.

- [ContentDefinitions](contentdefinitions.md): contiene las URL para las plantillas HTML5 que va a usar en su recorrido del usuario. En una directiva personalizada, una definición de contenido define el identificador URI de la página HTML5 que se usa en un paso concreto del recorrido del usuario. Por ejemplo, páginas de error, de registro o inicio de sesión, o de restablecimiento de contraseña. Para modificar la apariencia, invalide el elemento LoadUri del archivo HTML5. También puede crear nuevas definiciones de contenido según sus necesidades. Este elemento puede contener una referencia a recursos localizados mediante un identificador de localización.

- [Localización](localization.md): le ofrece compatibilidad con varios idiomas. La compatibilidad de localización en directivas permite configurar la lista de idiomas admitidos en una directiva y elegir un idioma predeterminado. Las cadenas y colecciones específicas del idioma también se admiten.

- [DisplayControls](display-controls.md): define los controles que se van a mostrar en una página. Los controles de visualización tienen una funcionalidad especial e interactúan con los perfiles técnicos de validación de back-end. 
