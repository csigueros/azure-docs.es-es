---
title: 'ClaimsSchema: Azure Active Directory B2C'
description: Especifique el elemento ClaimsSchema de una directiva personalizada en Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 8930ceceb3b4f55bf579e110c15f58893bbfa6d2
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "131008054"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

El elemento **ClaimsSchema** define los tipos de notificación a los que se puede hacer referencia como parte de la directiva. El esquema de notificaciones es el lugar en el que se declaran las notificaciones. Una notificación puede ser el nombre, apellido, nombre para mostrar, número de teléfono y mucho más. El elemento ClaimsSchema contiene la lista de elementos **ClaimType**. El elemento **ClaimType** contiene el atributo **Id**, que es el nombre de la notificación.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>ClaimType

El elemento **ClaimType** contiene el siguiente atributo:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Identificador | Sí | Un identificador que se usa para el tipo de notificación. Hay otros elementos que pueden usar este identificador en la directiva. |

El elemento **ClaimType** contiene los elementos siguientes:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | El título que se muestra a los usuarios en varias pantallas. El valor se puede [localizar](localization.md). |
| DataType | 1:1 | Tipo de la notificación. |
| DefaultPartnerClaimTypes | 0:1 | Los tipos de notificación predeterminada del partner que se van a usar para un protocolo especificado. El valor del elemento **PartnerClaimType** especificado se puede sobrescribir en los elementos **InputClaim** o **OutputClaim**. Use este elemento para especificar el nombre predeterminado de un protocolo.  |
| Máscara | 0:1 | Cadena opcional de caracteres de enmascaramiento que se pueden aplicar al mostrar la notificación. Por ejemplo, el número de teléfono 324-232-4343 se puede enmascarar como XXX-XXX-4343. |
| UserHelpText | 0:1 | Una descripción del tipo de notificación que puede ser útil para que los usuarios comprendan su propósito. El valor se puede [localizar](localization.md). |
| UserInputType | 0:1 | El tipo de control de entrada que el usuario debe tener disponible al especificar manualmente los datos de notificación para el tipo de notificación. Vea los tipos de entrada de usuario que se definen más adelante en esta página. |
| AdminHelpText | 0:1 | Una descripción del tipo de notificación que puede ser útil para que los administradores comprendan su propósito. |
| Restricción | 0:1 | Las restricciones de valor de esta notificación, como una expresión regular (Regex) o una lista de valores aceptables. El valor se puede [localizar](localization.md). |
PredicateValidationReference| 0:1 | Una referencia a un elemento **PredicateValidationsInput**. Los elementos **PredicateValidationReference** le permiten realizar un proceso de validación para asegurarse de que solo se introducen datos bien formados. Para obtener más información, vea [Predicados](predicates.md). |



### <a name="datatype"></a>DataType

El elemento **DataType** admite los siguientes valores:

| Tipo | Descripción |
| ------- | ----------- |
|boolean|Representa un valor booleano (`true` o `false`).|
|date| Representa un instante de tiempo, normalmente expresado en forma de fecha de un día. El valor de la fecha sigue la convención ISO 8601.|
|dateTime|Representa un instante de tiempo, normalmente expresado en forma de fecha y hora del día. El valor de la fecha sigue la convención ISO 8601.|
|duration|Representa un intervalo de tiempo en años, meses, días, horas, minutos y segundos. El formato es `PnYnMnDTnHnMnS`, donde `P` indica positivo o `N` un valor negativo. `nY` es el número de años seguido de un literal `Y`. `nMo` es el número de meses seguido de un literal `Mo`. `nD` es el número de días seguido de un literal `D`. Ejemplos: `P21Y` representa 21 años. `P1Y2Mo` representa un año y dos meses. `P1Y2Mo5D` representa un año, dos meses y cinco días.  `P1Y2M5DT8H5M620S` representa un año, dos meses, cinco días, ocho horas, cinco minutos y veinte segundos.  |
|phoneNumber|Representa un número de teléfono. |
|int| Representa un número entre -2.147.483.648 y 2.147.483.647.|
|long| Representa un número entre-9.223.372.036.854.775.808 y 9.223.372.036.854.775.807 |
|string| Representa texto como una secuencia de unidades de código UTF-16.|
|stringCollection|Representa una colección de `string`.|
|userIdentity| Representa una identidad de usuario.|
|userIdentityCollection|Representa una colección de `userIdentity`.|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

**DefaultPartnerClaimTypes** puede contener el siguiente elemento:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| Protocolo | 1:n | Lista de protocolos con su nombre de tipo de notificación de partner predeterminado. |

El elemento **Protocol** contiene los atributos siguientes:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Nombre | Sí | El nombre de un protocolo válido compatible con Azure AD B2C. Los valores posibles son:  OAuth1, OAuth2, SAML2 y OpenIdConnect. |
| PartnerClaimType | Sí | El nombre del tipo de notificación que se usará. |

En el ejemplo siguiente, cuando el marco de experiencia de identidad interactúa con un proveedor de identidades de SAML2 o una aplicación de usuario de confianza, la notificación **surname** se asigna a `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`; con OpenIdConnect y OAuth2, la notificación se asigna a `family_name`.

```xml
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

Como resultado, el token JWT emitido por Azure AD B2C emite `family_name` en lugar del nombre **surname** de ClaimType.

```json
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Máscara

El elemento **Mask** contiene los siguientes atributos:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| `Type` | Sí | Tipo de enmascaramiento de la notificación. Valores posibles: `Simple` o `Regex`. El valor `Simple` indica que se aplica un solo enmascaramiento del texto en la parte inicial de una notificación de la cadena. El valor `Regex` indica que una expresión regular se aplica a una notificación de la cadena en conjunto.  Si se especifica el valor `Regex`, también debe definirse un atributo opcional con la expresión regular que se va a usar. |
| `Regex` | No | Si **`Type`** se establece en `Regex`, especifique la expresión regular que se va a usar.

El ejemplo siguiente configura una notificación **PhoneNumber** con el enmascaramiento `Simple`:

```xml
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

El marco de experiencia de identidad representa el número de teléfono con los seis primeros dígitos ocultos:

![Notificación de número de teléfono que se muestra en el explorador con los seis primeros dígitos enmascarados con caracteres X](./media/claimsschema/mask.png)

El ejemplo siguiente configura una notificación **AlternateEmail** con el enmascaramiento `Regex`:

```xml
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

El marco de experiencia de identidad representa solo la primera letra de la dirección de correo electrónico y el nombre de dominio del correo electrónico:

![Notificación por correo electrónico que se muestra en el explorador con los caracteres enmascarados con asteriscos](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Restricción

El elemento **Restriction** puede contener el atributo siguiente:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| MergeBehavior | No | El método usado para combinar los valores de enumeración con un ClaimType en una directiva principal con el mismo identificador. Use este atributo para sobrescribir una notificación especificada en la directiva base. Valores posibles: `Append`, `Prepend` o `ReplaceAll`. El valor `Append` es una colección de datos que se deben anexar al final de la colección especificada en la directiva principal. El valor `Prepend` es una colección de datos que se deben agregar antes de la colección especificada en la directiva principal. El valor `ReplaceAll` es una colección de datos especificada en la directiva principal que se debe omitir. |

El elemento **Restriction** contiene los elementos siguientes:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| Enumeración | 1:n | Las opciones disponibles en la interfaz de usuario para que el usuario seleccione una notificación, como un valor en un menú desplegable. |
| Patrón | 1:1 | Expresión regular que se va a usar. |

#### <a name="enumeration"></a>Enumeración

El elemento **Enumeration** define opciones disponibles para que el usuario seleccione una notificación en la interfaz de usuario, como un valor en `CheckboxMultiSelect`, `DropdownSingleSelect` o `RadioSingleSelect`. Como alternativa, puede definir y localizar las opciones disponibles con el elemento [LocalizedCollections](localization.md#localizedcollections). Para buscar un elemento en una colección **Enumeration** de notificaciones, use la transformación de notificaciones [GetMappedValueFromLocalizedCollection](string-transformations.md#getmappedvaluefromlocalizedcollection).

El elemento **Enumeration** contiene los siguientes atributos:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| Texto | Sí | La cadena de presentación que se muestra al usuario en la interfaz de usuario para esta opción. |
|Value | Sí | El valor de notificación que está asociado a la selección de esta opción. |
| SelectByDefault | No | Indica si esta opción se puede seleccionar o no de forma predeterminada en la interfaz de usuario. Valores posibles: True o False. |

En el ejemplo siguiente se configura una notificación de lista desplegable **Ciudad** con un valor predeterminado establecido en `New York`:

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

Lista desplegable Ciudad con un valor predeterminado establecido en Nueva York:

![Control desplegable representado en el explorador y que muestra el valor predeterminado](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Patrón

El elemento **Pattern** puede contener los siguientes atributos:

| Atributo | Obligatorio | Descripción |
| --------- | -------- | ----------- |
| RegularExpression | Sí | La expresión regular con la que deben coincidir las notificaciones de este tipo para que sean válidas. |
| HelpText | No | Un mensaje de error para los usuarios si se produce un error en la comprobación de la expresión regular. |

En el ejemplo siguiente se configura una notificación de **correo electrónico** con el texto de ayuda y la validación de entrada de la expresión regular:

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
  <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

El marco de experiencia de identidad presenta la notificación de dirección de correo electrónico con la validación de entrada de formato de correo electrónico:

![Cuadro de texto que muestra el mensaje de error que desencadena la restricción de regex](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

Azure AD B2C admite una serie de tipos de entrada de usuario, como una lista de cuadro de texto, contraseña y menú desplegable que puede usarse al escribir manualmente los datos de notificación para el tipo de notificación. Debe especificar **UserInputType** al recopilar información del usuario mediante un [perfil técnico autoafirmado](self-asserted-technical-profile.md) y [controles de visualización](display-controls.md).

Los tipos de entrada de usuario disponibles del elemento **UserInputType**:

| UserInputType | Compatible con ClaimType | Descripción |
| --------- | -------- | ----------- |
|CheckboxMultiSelect| `string` |Cuadro desplegable de selección múltiple. El valor de la notificación se representa en una cadena con delimitador de coma de los valores seleccionados. |
|DateTimeDropdown | `date`, `dateTime` |Se despliega para seleccionar un día, un mes y un año. |
|DropdownSingleSelect |`string` |Cuadro desplegable de selección única. El valor de la notificación es el valor seleccionado.|
|EmailBox | `string` |Campo de entrada de correo electrónico. |
|Paragraph | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|Campo que muestra solo texto en una etiqueta de párrafo. |
|Contraseña | `string` |Cuadro de texto de contraseña.|
|RadioSingleSelect |`string` | Colección de botones de radio. El valor de la notificación es el valor seleccionado.|
|Readonly | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| Cuadro de texto de solo lectura. |
|TextBox |`boolean`, `int`, `string` |Cuadro de texto de una sola línea. |


#### <a name="textbox"></a>TextBox

El tipo de entrada de usuario **TextBox** se usa para proporcionar un cuadro de texto de una sola línea.

![Cuadro de texto que muestra las propiedades especificadas en el tipo de notificación](./media/claimsschema/textbox.png)

```xml
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>EmailBox

El tipo de entrada de usuario **EmailBox** se usa para proporcionar un campo de entrada de correo electrónico básico.

![Cuadro de correo electrónico que muestra las propiedades especificadas en el tipo de notificación](./media/claimsschema/emailbox.png)

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

#### <a name="password"></a>Contraseña

El tipo de entrada de usuario **Password** se usa para registrar una contraseña escrita por el usuario.

![Uso del tipo de notificación con Password](./media/claimsschema/password.png)

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DateTimeDropdown

El tipo de entrada de usuario **DateTimeDropdown** se usa para proporcionar un conjunto de listas desplegables para seleccionar un día, un mes y un año. Puede usar los elementos Predicates y PredicateValidations para controlar los valores de fecha mínimo y máximo. Para obtener más información, vea la sección **Configure a date range** (Configurar un intervalo de fechas) de [Predicates and PredicateValidations](predicates.md) (Predicados y validaciones de predicados).

![Uso del tipo de notificación con Datetimedropdown](./media/claimsschema/datetimedropdown.png)

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

El tipo de entrada de usuario **RadioSingleSelect** se usa para proporcionar una serie de botones de opción que permiten al usuario seleccionar una opción.

![Uso del tipo de notificación con RadioSingleSelect](./media/claimsschema/radiosingleselect.png)

```xml
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

El tipo de entrada de usuario **DropdownSingleSelect** se usa para proporcionar un cuadro desplegable que permite al usuario seleccionar una opción.

![Uso del tipo de notificación con DropdownSingleSelect](./media/claimsschema/dropdownsingleselect.png)

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

El tipo de entrada de usuario **CheckboxMultiSelect** se usa para proporcionar una serie de casillas de selección que permiten al usuario seleccionar varias opciones.

![Uso del tipo de notificación con CheckboxMultiSelect](./media/claimsschema/checkboxmultiselect.png)

```xml
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="readonly"></a>Readonly

El tipo de entrada de usuario **Readonly** se usa para proporcionar un campo de solo lectura para mostrar la notificación y el valor.

![Uso del tipo de notificación con Readonly](./media/claimsschema/readonly.png)

```xml
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Paragraph

El tipo de entrada de usuario **Paragraph** se usa para proporcionar un campo que muestra solo texto en una etiqueta de párrafo.  Por ejemplo, &lt;p&gt;texto&lt;/p&gt;. Tipo de entrada de usuario **párrafo** `OutputClaim` de un perfil técnico autoafirmado, se debe establecer el atributo `Required` `false` (valor predeterminado).

![Uso del tipo de notificación con Paragraph](./media/claimsschema/paragraph.png)

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
