---
title: Habilitación de la autenticación en una aplicación web mediante los bloques de creación de Azure Active Directory B2C
description: Utilice los bloques de creación de Azure Active Directory B2C para registrar e iniciar sesión con usuarios en una aplicación Angular.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 100fed96c2a7adaa0d0934ab316db1d70bffdcb9
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220824"
---
# <a name="enable-authentication-in-your-own-angular-application-by-using-azure-active-directory-b2c"></a>Habilitación de la autenticación en su propia aplicación Angular mediante Azure Active Directory B2C

En este artículo se muestra cómo agregar la autenticación de Azure Active Directory B2C (Azure AD B2C) a su propia aplicación de página única (SPA) de Angular. Más información sobre cómo integrar una aplicación Angular con la biblioteca de autenticación [MSAL para Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-angular). 

Use este artículo con el artículo relacionado titulado [Configuración de la autenticación en una aplicación de página única de Angular](./configure-authentication-sample-angular-spa-app.md). Sustituya la aplicación de Angular de ejemplo por su propia aplicación de Angular. Después de completar los pasos de este artículo, la aplicación aceptará inicios de sesión mediante Azure AD B2C.

## <a name="prerequisites"></a>Requisitos previos

Revise los requisitos previos y los pasos de integración del artículo [Configuración de la autenticación en una aplicación de página única de Angular de ejemplo](configure-authentication-sample-angular-spa-app.md).

## <a name="create-an-angular-app-project"></a>Creación de un proyecto de aplicación Angular

Puede usar un proyecto de aplicación Angular existente o crear uno nuevo. Para crear un nuevo proyecto, ejecute los siguientes comandos.

Los comandos:

1. Instale la [CLI de Angular](https://angular.io/cli) mediante el administrador de paquetes npm.
1. [Cree un área de trabajo de Angular ](https://angular.io/cli/new) con el módulo de enrutamiento. El nombre de la aplicación es `msal-angular-tutorial`. Puede cambiarlo a cualquier nombre de aplicación angular válido, como `contoso-car-service`.
1. Cambie a la carpeta del directorio de aplicaciones.

```
npm install -g @angular/cli 
ng new msal-angular-tutorial --routing=true --style=css --strict=false
cd msal-angular-tutorial
```

## <a name="install-the-dependencies"></a>Instalación de las dependencias

Para instalar las bibliotecas [MSAL Browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-browser) y [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-angular) en la aplicación, ejecute los comandos siguientes en el shell de comandos:

```
npm install @azure/msal-browser @azure/msal-angular
```

Instale la [biblioteca de componentes de Angular Material](https://material.angular.io/) (opcional para UI).

```
npm install @angular/material @angular/cdk
```

## <a name="add-the-authentication-components"></a>Adición de componentes de autenticación

Este código de ejemplo consta de los siguientes componentes: 

|Componente  |Tipo  |Descripción  |
|---------|---------|---------|
| auth-config.ts| Constantes | Este archivo de configuración contiene información sobre el proveedor de identidades de Azure AD B2C y el servicio de API web. La aplicación Angular usa esta información para establecer una relación de confianza con Azure AD B2C, iniciar y cerrar la sesión del usuario, adquirir tokens y validarlos. |
| app.module.ts| [Módulo Angular](https://angular.io/guide/architecture-modules)| Este componente describe cómo encajan los elementos de la aplicación. Este es el módulo raíz que se utiliza para iniciar y abrir la aplicación. En este tutorial, agregará algunos componentes al módulo *app.module.ts* e iniciará la biblioteca MSAL con el objeto de configuración de MSAL.  |
| app-routing.module.ts | [Módulo de enrutamiento de Angular](https://angular.io/tutorial/toh-pt5) | Este componente habilita la navegación mediante la interpretación de una dirección URL del explorador y la carga del componente correspondiente. En este tutorial, agregará algunos componentes al módulo de enrutamiento y protegerá los componentes con [MSAL Guard](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-guard.md). Solo los usuarios autorizados pueden acceder a los componentes protegidos.   |
| app.component.* | [Componente de Angular](https://angular.io/guide/architecture-components) | El comando `ng new` creó un proyecto Angular con un componente raíz. En este tutorial, cambiará el componente de la *aplicación* para hospedar la barra de navegación superior. La barra de navegación contiene varios botones, incluidos el de inicio de sesión y el de cierre de sesión. La clase `app.component.ts` controla los eventos de inicio y de cierre de sesión.  |
| home.component.* | [Componente de Angular](https://angular.io/guide/architecture-components)|En este tutorial, va a agregar el componente *home* para representar la página principal para un acceso anónimo. Este componente muestra cómo comprobar si un usuario ha iniciado sesión.  |
| profile.component.* | [Componente de Angular](https://angular.io/guide/architecture-components) | En este tutorial, va a agregar el componente *profile* para aprender a leer las notificaciones de los tokens de identificación. |
| webapi.component.* | [Componente de Angular](https://angular.io/guide/architecture-components)| En este tutorial, va a agregar el componente *webapi* para aprender a llamar a una API web. |

Para agregar los siguientes componentes a la aplicación, ejecute los siguientes comandos de la CLI de Angular. Los comandos `generate component`:

1. Crea una carpeta para cada componente. La carpeta contiene los archivos TypeScript, HTML, CSS y de prueba. 
1. Actualiza los archivos `app.module.ts` y `app-routing.module.ts` con referencias a los nuevos componentes. 

```
ng generate component home
ng generate component profile
ng generate component webapi
```

## <a name="add-the-app-settings"></a>Adición de la configuración de la aplicación

La configuración para el proveedor de identidades de Azure AD B2C y la API web se almacenan en el archivo *auth-config.ts*. En la carpeta *src/app*, cree un archivo llamado *auth-config.ts* que contiene el código siguiente. A continuación, cambie la configuración tal y como se describe en la sección [3.1 Configuración del ejemplo de Angular](configure-authentication-sample-angular-spa-app.md#31-configure-the-angular-sample).

```typescript
import { LogLevel, Configuration, BrowserCacheLocation } from '@azure/msal-browser';

const isIE = window.navigator.userAgent.indexOf("MSIE ") > -1 || window.navigator.userAgent.indexOf("Trident/") > -1;
 
export const b2cPolicies = {
     names: {
         signUpSignIn: "b2c_1_susi_reset_v2",
         editProfile: "b2c_1_edit_profile_v2"
     },
     authorities: {
         signUpSignIn: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_susi_reset_v2",
         },
         editProfile: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_edit_profile_v2"
         }
     },
     authorityDomain: "your-tenant-name.b2clogin.com"
 };
 
 
export const msalConfig: Configuration = {
     auth: {
         clientId: '<your-MyApp-application-ID>',
         authority: b2cPolicies.authorities.signUpSignIn.
         knownAuthorities: [b2cPolicies.authorityDomain],
         redirectUri: '/', 
     },
     cache: {
         cacheLocation: BrowserCacheLocation.LocalStorage,.
         storeAuthStateInCookie: isIE, 
     },
     system: {
         loggerOptions: {
            loggerCallback: (logLevel, message, containsPii) => {
                console.log(message);
             },
             logLevel: LogLevel.Verbose,
             piiLoggingEnabled: false
         }
     }
 }

export const protectedResources = {
  todoListApi: {
    endpoint: "http://localhost:5000/api/todolist",
    scopes: ["https://your-tenant-namee.onmicrosoft.com/api/tasks.read"],
  },
}
export const loginRequest = {
  scopes: []
};
```

## <a name="start-the-authentication-libraries"></a>Inicio de las bibliotecas de autenticación

Las aplicaciones cliente públicas no son de confianza para mantener los secretos de aplicación de forma segura, por lo tanto no tienen secretos de cliente. En la carpeta *src/app*, abra *app.module.ts* y haga los cambios siguientes:

1. Importe las bibliotecas Angular MSAL y MSAL Browser.
1. Importe el módulo de configuración de Azure AD B2C.
1. Importe `HttpClientModule`. El cliente HTTP se usa para llamar a las API web.
1. Importe el interceptor HTTP de Angular. MSAL usa el interceptor para insertar el token de portador en el encabezado de autorización HTTP.
1. Agregue los materiales esenciales de Angular.
1. Cree una instancia de MSAL mediante el objeto de aplicación cliente pública de varias cuentas. La inicialización de MSAL incluye pasar:
    1. El objeto de configuración para *auth-config.ts*.
    1. El objeto de configuración para la protección de enrutamiento.
    1. El objeto de configuración para el interceptor de MSAL. La clase del interceptor adquiere automáticamente tokens para las solicitudes salientes que usan la clase [HttpClient](https://angular.io/api/common/http/HttpClient) de Angular con los recursos protegidos conocidos.
1. Configure los proveedores `HTTP_INTERCEPTORS` y `MsalGuard` [de Angular](https://angular.io/guide/providers).  
1. Agregue `MsalRedirectComponent` al [arranque de Angular](https://angular.io/guide/bootstrapping).

En la carpeta *src/app*, edite *app.module.ts* y realice las modificaciones que se muestran en el siguiente fragmento de código. Los cambios se indican con "Los cambios comienzan aquí" y "Los cambios finalizan aquí". 

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

/* Changes start here. */
// Import MSAL and MSAL browser libraries. 
import { MsalGuard, MsalInterceptor, MsalModule, MsalRedirectComponent } from '@azure/msal-angular';
import { InteractionType, PublicClientApplication } from '@azure/msal-browser';

// Import the Azure AD B2C configuration 
import { msalConfig, protectedResources } from './auth-config';

// Import the Angular HTTP interceptor. 
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { ProfileComponent } from './profile/profile.component';
import { HomeComponent } from './home/home.component';
import { WebapiComponent } from './webapi/webapi.component';

// Add the essential Angular materials.
import { MatButtonModule } from '@angular/material/button';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatListModule } from '@angular/material/list';
import { MatTableModule } from '@angular/material/table';
/* Changes end here. */

@NgModule({
  declarations: [
    AppComponent,
    ProfileComponent,
    HomeComponent,
    WebapiComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    /* Changes start here. */
    // Import the following Angular materials. 
    MatButtonModule,
    MatToolbarModule,
    MatListModule,
    MatTableModule,
    // Import the HTTP client. 
    HttpClientModule,

    // Initiate the MSAL library with the MSAL configuration object
    MsalModule.forRoot(new PublicClientApplication(msalConfig),
      {
        // The routing guard configuration. 
        interactionType: InteractionType.Redirect,
        authRequest: {
          scopes: protectedResources.todoListApi.scopes
        }
      },
      {
        // MSAL interceptor configuration.
        // The protected resource mapping maps your web API with the corresponding app scopes. If your code needs to call another web API, add the URI mapping here.
        interactionType: InteractionType.Redirect,
        protectedResourceMap: new Map([
          [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
        ])
      })
    /* Changes end here. */
  ],
  providers: [
    /* Changes start here. */
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    },
    MsalGuard
    /* Changes end here. */
  ],
  bootstrap: [
    AppComponent,
    /* Changes start here. */
    MsalRedirectComponent
    /* Changes end here. */
  ]
})
export class AppModule { }
```

## <a name="configure-routes"></a>Configuración de rutas

En esta sección, va a configurar las rutas para la aplicación Angular. Cuando un usuario selecciona un vínculo en la página para desplazarse dentro de la aplicación de página única o se le asigna una dirección URL en la barra de direcciones, las rutas asignan la dirección URL a un componente de Angular. La interfaz [canActivate](https://angular.io/api/router/CanActivate) de enrutamiento de Angular utiliza MSAL Guard para comprobar si el usuario ha iniciado sesión. Si el usuario no ha iniciado sesión, MSAL lleva al usuario a Azure AD B2C para autenticarse.

En la carpeta *src/app*, edite *app.routing.module.ts* y haga las siguientes modificaciones que se muestran en el siguiente fragmento de código. Los cambios se indican con "Los cambios comienzan aquí" y "Los cambios finalizan aquí". 

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';
import { ProfileComponent } from './profile/profile.component';
import { WebapiComponent } from './webapi/webapi.component';

const routes: Routes = [
  /* Changes start here. */
  {
    path: 'profile',
    component: ProfileComponent,
    // The profile component is protected with MSAL Guard.
    canActivate: [MsalGuard]
  },
  {
    path: 'webapi',
    component: WebapiComponent,
    // The profile component is protected with MSAL Guard.
    canActivate: [MsalGuard]
  },
  {
    // The home component allows anonymous access
    path: '',
    component: HomeComponent
  }
  /* Changes end here. */
];


@NgModule({
  /* Changes start here. */
  // Replace the following line with the next one
  //imports: [RouterModule.forRoot(routes)],
  imports: [RouterModule.forRoot(routes, {
    initialNavigation:'enabled'
  })],
  /* Changes end here. */
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

## <a name="add-the-sign-in-and-sign-out-buttons"></a>Adición de los botones de inicio y de cierre de sesión

En esta sección, va a agregar los botones de inicio y cierre de sesión en el componente *app*. En la carpeta *src/app*, abra *app.component.ts* y haga los cambios siguientes:

1. Importe los componentes necesarios.
1. Cambie la clase para implementar el [método OnInit](https://angular.io/api/core/OnInit). El método `OnInit` suscribe el evento observable [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md)`inProgress$`. Use este evento para conocer el estado de las interacciones del usuario, especialmente para comprobar que las interacciones se han completado. 

   Antes de las interacciones con el objeto de cuenta de MSAL, compruebe que la propiedad `InteractionStatus` devuelve `InteractionStatus.None`. El evento `subscribe` llama al método `setLoginDisplay` para comprobar si el usuario está autenticado.
1. Agregue las variables de clase.
1. Agregue el método `login` que inicia el flujo de autorización.
1. Agregue el método `logout` que cierra la sesión del usuario. 
1. Agregue el método `setLoginDisplay` que comprueba si el usuario está autenticado.
1. Agregue el método [ngOnDestroy](https://angular.io/api/core/OnDestroy) para limpiar el evento de suscripción `inProgress$`.

Después de los cambios, el código debe ser parecido al siguiente fragmento de código.

```typescript
import { Component, OnInit, Inject } from '@angular/core';
import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
import { InteractionStatus, RedirectRequest } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})

/* Changes start here. */
export class AppComponent implements OnInit{
  title = 'msal-angular-tutorial';
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {

    this.broadcastService.inProgress$
    .pipe(
      filter((status: InteractionStatus) => status === InteractionStatus.None),
      takeUntil(this._destroying$)
    )
    .subscribe(() => {
      this.setLoginDisplay();
    })
  }

  login() {
    if (this.msalGuardConfig.authRequest){
      this.authService.loginRedirect({...this.msalGuardConfig.authRequest} as RedirectRequest);
    } else {
      this.authService.loginRedirect();
    }
  }

  logout() { 
    this.authService.logoutRedirect({
      postLogoutRedirectUri: 'http://localhost:4200'
    });
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
  /* Changes end here. */
}
```

En la carpeta *src/app*, edite *app.component.html* y realice los cambios siguientes:

1. Agregue un vínculo al perfil y a los componentes de API web.
1. Agregue el botón de inicio de sesión con el atributo de evento de clic establecido en el método `login()`. Este botón aparece solo si la variable de clase `loginDisplay` es `false`.
1. Agregue el botón de cierre de sesión con el atributo de evento de clic establecido en el método `logout()`. Este botón aparece solo si la variable de clase `loginDisplay` es `true`.
1. Agregue un elemento [router-outlet](https://angular.io/api/router/RouterOutlet). 

Después de los cambios, el código debe ser parecido al siguiente fragmento de código.

```html
<mat-toolbar color="primary">
  <a class="title" href="/">{{ title }}</a>

  <div class="toolbar-spacer"></div>

  <a mat-button [routerLink]="['profile']">Profile</a>
  <a mat-button [routerLink]="['webapi']">Web API</a>

  <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>
  <button mat-raised-button *ngIf="loginDisplay" (click)="logout()">Logout</button>

</mat-toolbar>
<div class="container">
  <router-outlet></router-outlet>
</div>
```

Opcionalmente, actualice el archivo *app.component.css* con el siguiente fragmento de código de CSS. 

```css
.toolbar-spacer {
    flex: 1 1 auto;
  }

  a.title {
    color: white;
  }
```

## <a name="handle-the-app-redirects"></a>Control de los redireccionamientos de la aplicación 

Al utilizar los redireccionamientos con MSAL, es obligatorio agregar la directiva [app-redirect](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/redirects.md) a *index.html*. En la carpeta *src*, modifique *index.html*, tal y como se muestra en el siguiente fragmento de código:

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>MsalAngularTutorial</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
<body>
  <app-root></app-root>
  <!-- Changes start here -->
  <app-redirect></app-redirect>
  <!-- Changes end here -->
</body>
</html>
```

## <a name="set-app-css-optional"></a>Establecimiento del CSS de aplicación (opcional)

En la carpeta */src*, actualice el archivo *styles.css* con el siguiente fragmento de código de CSS. 

```css
@import '~@angular/material/prebuilt-themes/deeppurple-amber.css';

html, body { height: 100%; }
body { margin: 0; font-family: Roboto, "Helvetica Neue", sans-serif; }
.container { margin: 1%; }
```

> [!TIP]
> En este momento, puede ejecutar la aplicación y probar la experiencia de inicio de sesión. Para ejecutar la aplicación, consulte la sección [Ejecución de la aplicación Angular](#run-the-angular-application).

## <a name="check-if-a-user-is-authenticated"></a>Comprobación de si un usuario está autenticado

El archivo *home.component* muestra cómo comprobar que el usuario está autenticado. En la carpeta *src/app/home*, actualice *home.component.ts* con el fragmento de código siguiente. 

El código:

1. Suscribe a los eventos observables [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) `msalSubject$` y `inProgress$`. 
1. Asegúrese de que el evento `msalSubject$` escriba el resultado de la autenticación en la consola del explorador.
1. Asegúrese de que el evento `inProgress$` compruebe si un usuario está autenticado. El método `getAllAccounts()` devuelve uno o más objetos.


```typescript
import { Component, OnInit } from '@angular/core';
import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
import { filter } from 'rxjs/operators';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {
  loginDisplay = false;

  constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

  ngOnInit(): void {
    this.msalBroadcastService.msalSubject$
      .pipe(
        filter((msg: EventMessage) => msg.eventType === EventType.LOGIN_SUCCESS),
      )
      .subscribe((result: EventMessage) => {
        console.log(result);
      });

    this.msalBroadcastService.inProgress$
      .pipe(
        filter((status: InteractionStatus) => status === InteractionStatus.None)
      )
      .subscribe(() => {
        this.setLoginDisplay();
      })
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }
}
```

En la carpeta *src/app/home*, actualice *home.component.html* con el fragmento de código HTML siguiente. La directiva [*ngIf](https://angular.io/api/common/NgIf) comprueba la variable de clase `loginDisplay` para mostrar u ocultar los mensajes de bienvenida.

```html
<div *ngIf="!loginDisplay">
    <p>Please sign-in to see your profile information.</p>
</div>

<div *ngIf="loginDisplay">
    <p>Login successful!</p>
    <p>Request your profile information by clicking Profile above.</p>
</div>
```

## <a name="read-the-id-token-claims"></a>Lectura de las notificaciones de tokens de identificador

El archivo *profile.component* muestra cómo acceder a las notificaciones del token de identificador del usuario. En la carpeta *src/app/profile*, actualice *profile.component.ts* con el fragmento de código siguiente. 

El código:

1. Importa los componentes necesarios.
1. Suscribe al evento observable [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) `inProgress$`. El evento carga la cuenta y lee las notificaciones del token de identificador.
1. Asegúrese de que el método `checkAndSetActiveAccount` compruebe y establezca la cuenta activa. Esta acción es habitual cuando la aplicación interactúa con varios flujos de usuario de Azure AD B2C o directivas personalizadas.
1. Asegúrese de que el método `getClaims` obtenga las notificaciones del token de identificador del objeto de cuenta MSAL activo. El método agrega las notificaciones a la matriz `dataSource`. La matriz se representa al usuario con el enlace de plantilla del componente.

```typescript
import { Component, OnInit } from '@angular/core';
import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-profile',
  templateUrl: './profile.component.html',
  styleUrls: ['./profile.component.css']
})

export class ProfileComponent implements OnInit {
  displayedColumns: string[] = ['claim', 'value'];
  dataSource: Claim[] = [];
  private readonly _destroying$ = new Subject<void>();
  
  constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

  ngOnInit(): void {

    this.msalBroadcastService.inProgress$
      .pipe(
        filter((status: InteractionStatus) =>  status === InteractionStatus.None || status === InteractionStatus.HandleRedirect),
        takeUntil(this._destroying$)
      )
      .subscribe(() => {
        this.checkAndSetActiveAccount();
        this.getClaims(this.authService.instance.getActiveAccount()?.idTokenClaims)
      })
  }

  checkAndSetActiveAccount() {

    let activeAccount = this.authService.instance.getActiveAccount();

    if (!activeAccount && this.authService.instance.getAllAccounts().length > 0) {
      let accounts = this.authService.instance.getAllAccounts();
      this.authService.instance.setActiveAccount(accounts[0]);
    }
  }

  getClaims(claims: any) {

    let list: Claim[]  =  new Array<Claim>();

    Object.keys(claims).forEach(function(k, v){
      
      let c = new Claim()
      c.id = v;
      c.claim = k;
      c.value =  claims ? claims[k]: null;
      list.push(c);
    });
    this.dataSource = list;

  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
}

export class Claim {
  id: number;
  claim: string;
  value: string;
}
``` 

En la carpeta *src/app/profile*, actualice *profile.component.hmtl* con el fragmento de código HTML siguiente. 

```html
<h1>ID token claims:</h1>

<table mat-table [dataSource]="dataSource" class="mat-elevation-z8">

  <!-- Claim Column -->
  <ng-container matColumnDef="claim">
    <th mat-header-cell *matHeaderCellDef> Claim </th>
    <td mat-cell *matCellDef="let element"> {{element.claim}} </td>
  </ng-container>

  <!-- Value Column -->
  <ng-container matColumnDef="value">
    <th mat-header-cell *matHeaderCellDef> Value </th>
    <td mat-cell *matCellDef="let element"> {{element.value}} </td>
  </ng-container>

  <tr mat-header-row *matHeaderRowDef="displayedColumns"></tr>
  <tr mat-row *matRowDef="let row; columns: displayedColumns;"></tr>
</table>
```

## <a name="call-a-web-api"></a>Llamada a una API de web

Para llamar a una [API web de autorización basada en tokens](enable-authentication-web-api.md), la aplicación necesita un token de acceso válido. El proveedor [MsalInterceptor](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-interceptor.md) adquiere automáticamente tokens para las solicitudes salientes que usan la clase [HttpClient](https://angular.io/api/common/http/HttpClient) de Angular en los recursos protegidos conocidos.

> [!IMPORTANT]
> El método de inicialización MSAL (en la clase `app.module.ts`) asigna recursos protegidos, como las API web, con los ámbitos de aplicación mediante el objeto `protectedResourceMap`. Si el código necesita llamar a otra API web, agregue el URI de la API web y el método HTTP de la API web, con los ámbitos correspondientes al objeto `protectedResourceMap`. Para más información, consulte [Asignación de recursos protegidos](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/master/lib/msal-angular/docs/v2-docs/msal-interceptor.md#protected-resource-map).


Cuando el objeto [HttpClient](https://angular.io/api/common/http/HttpClient) llama a la API web, el proveedor [MsalInterceptor](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-interceptor.md) realiza los pasos siguientes:

1. Adquiere un token de acceso con los permisos necesarios (ámbitos) para el punto de conexión de API web. 
1. Pasa el token de acceso como token de portador en el encabezado de autorización de la solicitud HTTP con este formato:

   ```http
   Authorization: Bearer <access-token>
   ```

El archivo *webapi.component* muestra cómo llamar a una API web. En la carpeta *src/app/webapi*, actualice *webapi.component.ts* con el fragmento de código siguiente.  

El código:

1. Utiliza la clase [HttpClient](https://angular.io/guide/http) de Angular para llamar a la API web.
1. Lee el elemento `protectedResources.todoListApi.endpoint` de la clase `auth-config`. Este elemento especifica el URI de la API web. En función del URI de la API web, el interceptor de MSAL adquiere un token de acceso con los ámbitos correspondientes. 
1. Obtiene el perfil de la API web y establece la variable de clase `profile`.

```typescript
import { Component, OnInit } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { protectedResources } from '../auth-config';

type ProfileType = {
  name?: string
};

@Component({
  selector: 'app-webapi',
  templateUrl: './webapi.component.html',
  styleUrls: ['./webapi.component.css']
})
export class WebapiComponent implements OnInit {
  todoListEndpoint: string = protectedResources.todoListApi.endpoint;
  profile!: ProfileType;

  constructor(
    private http: HttpClient
  ) { }

  ngOnInit() {
    this.getProfile();
  }

  getProfile() {
    this.http.get(this.todoListEndpoint)
      .subscribe(profile => {
        this.profile = profile;
      });
  }
}
```

En la carpeta *src/app/webapi*, actualice *webapi.component.html* con el fragmento de código HTML siguiente. La plantilla del componente representa el nombre que devuelve la API web. En la parte inferior de la página, la plantilla representa la dirección de la API web.

```html
<h1>The web API returns:</h1>
<div>
    <p><strong>Name: </strong> {{profile?.name}}</p>
</div>

<div class="footer-text">
    Web API: {{todoListEndpoint}}
</div>
```

Opcionalmente, actualice el archivo *webapi.component.css* con el siguiente fragmento de código de CSS. 

```css
.footer-text {
    position: absolute;
    bottom: 50px;
    color: gray;
}
```

## <a name="run-the-angular-application"></a>Ejecución de la aplicación Angular


Ejecute este comando:

```console
npm start
```

La ventana de la consola muestra el número de puerto donde se hospeda la aplicación.

```console
Listening on port 4200...
```

> [!TIP]
> Como alternativa, para ejecutar el comando `npm start`, use el [depurador de Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging). El depurador ayuda a acelerar el bucle de edición, compilación y depuración.

Vaya a `http://localhost:4200` en el explorador para ver la aplicación.


## <a name="next-steps"></a>Pasos siguientes

* [Configuración de las opciones de autenticación en su propia aplicación Angular mediante Azure AD B2C](enable-authentication-angular-spa-app-options.md)
* [Habilitación de la autenticación en su propia API web](enable-authentication-web-api.md)
