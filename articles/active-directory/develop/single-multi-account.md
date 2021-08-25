---
title: Aplicaciones cliente públicas de una o varias cuentas | Azure
description: Introducción a las aplicaciones cliente públicas de una o varias cuentas.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev, has-adal-ref
ms.reviewer: shoatman
ms.openlocfilehash: 4d8b45746702e8dcb9da0d9816f2a0e564854cf4
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122428975"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Aplicaciones cliente públicas de una o varias cuentas

Este artículo le ayudará a comprender los tipos que se usan en las aplicaciones cliente públicas de una o varias cuentas, con especial atención a las aplicaciones cliente públicas de una sola cuenta. 

La biblioteca de autenticación de Azure Active Directory (ADAL) modela el servidor.  En cambio, la biblioteca de autenticación de Microsoft (MSAL) modela la aplicación cliente.  La mayoría de las aplicaciones de Android se consideran aplicaciones cliente públicas. Una aplicación cliente pública es una aplicación que no puede mantener un secreto de forma segura.  

MSAL especializa la superficie de la API de `PublicClientApplication` para simplificar y aclarar la experiencia de desarrollo de las aplicaciones que solo permiten el uso de una cuenta a la vez. `PublicClientApplication` tiene las subclases `SingleAccountPublicClientApplication` y `MultipleAccountPublicClientApplication`.  En el diagrama siguiente se muestra la relación entre estas clases.

![Diagrama de clases UML de SingleAccountPublicClientApplication](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Aplicación cliente pública de una sola cuenta

La clase `SingleAccountPublicClientApplication` permite crear una aplicación basada en MSAL que solo permite iniciar sesión a una sola cuenta a la vez. `SingleAccountPublicClientApplication` difiere de `PublicClientApplication` en lo siguiente:

- MSAL realiza un seguimiento de la cuenta que tiene iniciada la sesión actualmente.
  - Si la aplicación usa un agente (el predeterminado durante el registro de la aplicación en Azure Portal) y está instalado en un dispositivo en el que hay un agente, MSAL comprobará que la cuenta sigue estando disponible en el dispositivo.
- `signIn` permite iniciar sesión en una cuenta de forma explícita y independiente de los ámbitos de la solicitud.
- `acquireTokenSilent` no requiere un parámetro de cuenta.  Si proporciona una cuenta y la cuenta que proporciona no coincide con la cuenta actual cuyo seguimiento se realiza mediante MSAL, se produce la excepción `MsalClientException`.
- `acquireToken` no permite al usuario cambiar de cuenta. Si el usuario intenta cambiar a una cuenta diferente, se produce una excepción.
- `getCurrentAccount` devuelve un objeto de resultado que proporciona lo siguiente:
  - Un valor booleano que indica si la cuenta ha cambiado. Una cuenta puede cambiar como resultado de la eliminación del dispositivo, por ejemplo.
  - La cuenta anterior. Esto resulta útil si necesita realizar una limpieza de datos local cuando la cuenta se quita del dispositivo o cuando una cuenta nueva inicia sesión.
  - La cuenta actual.
- `signOut` quita del dispositivo todos los tokens asociados con el cliente.  

Cuando hay un agente de autenticación de Android, como Microsoft Authenticator o Portal de empresa de Intune, instalado en el dispositivo y la aplicación está configurada para usar el agente, `signOut` no quitará la cuenta del dispositivo.

## <a name="single-account-scenario"></a>Escenario de una sola cuenta

En el siguiente pseudocódigo se muestra el uso de `SingleAccountPublicClientApplication`.

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>Aplicación cliente pública de varias cuentas

La clase `MultipleAccountPublicClientApplication` se usa para crear aplicaciones basadas en MSAL que permitan que varias cuentas inicien sesión al mismo tiempo. Permite obtener, agregar y quitar cuentas de la siguiente manera:

### <a name="add-an-account"></a>Agregar una cuenta

Use una o varias cuentas en la aplicación llamando a `acquireToken` una o varias veces.  

### <a name="get-accounts"></a>Obtención de cuentas

- Llame a `getAccount` para obtener una cuenta específica.
- Llame a `getAccounts` para obtener una lista de las cuentas que la aplicación conoce actualmente.

La aplicación no podrá enumerar todas las cuentas de la Plataforma de identidad de Microsoft en el dispositivo conocido por la aplicación de agente. Solo puede enumerar las cuentas usadas por la aplicación.  Estas funciones no devolverán las cuentas que se hayan quitado del dispositivo.

### <a name="remove-an-account"></a>Eliminación de una cuenta

Para quitar una cuenta, llame a `removeAccount` con un identificador de cuenta.

Si la aplicación está configurada para usar un agente y se instala un agente en el dispositivo, la cuenta no se quitará del agente cuando llame a `removeAccount`.  Solo se quitan los tokens asociados con el cliente.

## <a name="multiple-account-scenario"></a>Escenario de varias cuentas

En el siguiente pseudocódigo se muestra cómo crear una aplicación de varias cuentas, enumerar cuentas en el dispositivo y adquirir tokens.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
