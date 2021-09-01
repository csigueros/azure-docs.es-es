---
title: Tutorial para configurar Azure Active Directory B2C con Datawiza
titleSuffix: Azure AD B2C
description: Aprenda a integrar la autenticación de Azure AD B2C con Datawiza para proporcionar un acceso híbrido seguro
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 7/07/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: a007f567b6f137f1bff225f90c3f52dfaf71c1f2
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228590"
---
# <a name="tutorial-configure-azure-ad-b2c-with-datawiza-to-provide-secure-hybrid-access"></a>Tutorial: Configuración de Azure AD B2C con Datawiza para proporcionar un acceso híbrido seguro

En este tutorial de ejemplo, aprenderá a integrar Azure Active Directory (AD) B2C con [Datawiza](https://www.datawiza.com/).
[Datawiza Access Broker (DAB)](https://www.datawiza.com/access-broker), de Datawiza, permite el inicio de sesión único (SSO) y un control de acceso granular que amplía Azure AD B2C para que proteja las aplicaciones heredadas locales. Con esta solución, las empresas pueden pasar rápidamente de un sistema heredado a Azure AD B2C sin tener que volver a escribir las aplicaciones.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

- Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).

- Un [inquilino de Azure AD B2C](./tutorial-create-tenant.md) vinculado a la suscripción de Azure.

- Para ejecutar DAB, se necesita [Docker](https://docs.docker.com/get-docker/). Las aplicaciones se pueden ejecutar en cualquier plataforma, como máquina virtual y en un equipo sin sistema operativo.

- Una aplicación local que pasará de un sistema de identidades heredado a Azure AD B2C. En este ejemplo, DAB se implementa en el mismo servidor en el que está la aplicación. La aplicación se ejecutará en localhost: 3001 y DAB redirecciona mediante proxy el tráfico a la aplicación a través de localhost: 9772. El tráfico a la aplicación llegará primero a DAB y, después, se le redireccionará mediante proxy a la aplicación.

## <a name="scenario-description"></a>Descripción del escenario

La integración de Datawiza incluye los siguientes componentes:

- **Azure AD B2C**: el servidor de autorización responsable de verificar las credenciales del usuario. Los usuarios autenticados pueden acceder a aplicaciones locales mediante una cuenta local almacenada en el directorio de Azure AD B2C.

- **Datawiza Access Broker (DAB)** : el usuario del servicio inicia sesión y pasa la identidad de forma transparente a las aplicaciones a través de encabezados HTTP.

- **Datawiza Cloud Management Console (DCMC)** : una consola de administración centralizada que administra DAB. DCMC proporciona API de interfaz de usuario y Restful para que los administradores administren las configuraciones de DAB y sus directivas de control de acceso.

En el siguiente diagrama de arquitectura se muestra la implementación.

![Imagen que muestra la arquitectura de una integración de Azure AD B2C con Datawiza para proteger el acceso a aplicaciones híbridas](./media/partner-datawiza/datawiza-architecture-diagram.png)

| Pasos | Descripción |
|:-------|:---------------|
| 1. | El usuario realiza una solicitud para acceder a la aplicación hospedada en el entorno local. DAB redirige mediante proxy la solicitud realizada por el usuario a la aplicación.|
| 2. | DAB comprueba el estado de autenticación del usuario. Si no recibe un token de sesión, o el token de sesión proporcionado no es válido, envía al usuario a Azure AD B2C para autenticarse.|
| 3. | Azure AD B2C envía la solicitud del usuario al punto de conexión especificado durante el registro de la aplicación DAB en el inquilino de Azure AD B2C.|
| 4. | DAB evalúa las directivas de acceso y calcula los valores de atributo que se van a incluir en los encabezados HTTP reenviados a la aplicación. Durante este paso, DAB puede llamar al proveedor de identidades para recuperar la información necesaria para establecer los valores de encabezado correctamente. DAB establece los valores de encabezado y envía la solicitud a la aplicación. |
|5.  | El usuario ya está autenticado y tiene acceso a la aplicación.|

## <a name="onboard-with-datawiza"></a>Incorporación con Datawiza

Para integrar una aplicación local heredada con Azure AD B2C, póngase en contacto con [Datawiza](https://login.datawiza.com/df3f213b-68db-4966-bee4-c826eea4a310/b2c_1a_linkage/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile&client_id=4f011d0f-44d4-4c42-ad4c-88c7bbcd1ac8&redirect_uri=https%3A%2F%2Fconsole.datawiza.com%2Fhome&state=eyJpZCI6Ijk3ZjI5Y2VhLWQ3YzUtNGM5YS1hOWU2LTg1MDNjMmUzYWVlZCIsInRzIjoxNjIxMjg5ODc4LCJtZXRob2QiOiJyZWRpcmVjdEludGVyYWN0aW9uIn0%3D&nonce=08e1b701-6e42-427b-894b-c5d655a9a6b0&client_info=1&x-client-SKU=MSAL.JS&x-client-Ver=1.3.3&client-request-id=3ac285ba-2d4d-4ae5-8dc2-9295ff6047c6&response_mode=fragment).

## <a name="configure-your-azure-ad-b2c-tenant"></a>Configuración del inquilino de Azure AD B2C

1. [Registre](https://docs.datawiza.com/idp/azureb2c.html#microsoft-azure-ad-b2c-configuration) la aplicación web en un inquilino de Azure AD B2C

2. [Configure un flujo de usuario de registro e inicio de sesión](https://docs.datawiza.com/idp/azureb2c.html#configure-a-user-flow) en el Portal de administración de Azure.

  >[!Note]
  >Más adelante, cuando configure DAB en DCMC necesitará el nombre del inquilino, el nombre del flujo de usuario, el identificador de cliente y el secreto de cliente.

## <a name="create-an-application-on-dcmc"></a>Creación de una aplicación en DCMC

1. [Cree una aplicación](https://docs.datawiza.com/step-by-step/step2.html) y genere un par de claves de `PROVISIONING_KEY` y `PROVISIONING_SECRET` para esta aplicación en DCMC.

2. [Configuración de Azure AD B2C](https://docs.datawiza.com/tutorial/web-app-azure-b2c.html#part-i-azure-ad-b2c-configuration) como proveedor de identidades

![Imagen que muestra los valores para configurar el proveedor de identidades](./media/partner-datawiza/configure-idp.png)

## <a name="run-dab-with-a-header-based-application"></a>Ejecución de DAB con una aplicación basada en encabezados

1. Para ejecutar DAB se pueden usar Docker o Kubernetes. La imagen de Docker es necesaria para que los usuarios creen una aplicación basada en encabezados de ejemplo. Para más detalles, consulte las instrucciones necesarias para [configurar la integración de DAB y SSO](https://docs.datawiza.com/step-by-step/step3.html) y para obtener instrucciones específicas de Kubernetes, consulte cómo [implementar DAB con Kubernetes](https://docs.datawiza.com/tutorial/web-app-AKS.html). Se proporciona una imagen de Docker de ejemplo `docker-compose.yml file` para que la descargue y la use. Inicie sesión en el registro de contenedor para descargar las imágenes de DAB y la aplicación basada en encabezados. Siga [estas instrucciones](https://docs.datawiza.com/step-by-step/step3.html#important-step).
 
   ```YML
   version: '3'

    services:
    datawiza-access-broker:
    image: registry.gitlab.com/datawiza/access-broker
    container_name: datawiza-access-broker
    restart: always
    ports:
      - "9772:9772"
    environment:
      PROVISIONING_KEY: #############################
      PROVISIONING_SECRET: #############################

    header-based-app:
    image: registry.gitlab.com/datawiza/header-based-app
    container_name: ab-demo-header-app
    restart: always
    environment:
      CONNECTOR: B2C
    ports:
      - "3001:3001"
    ```

 2. Después de ejecutar `docker-compose -f docker-compose.yml up`, la aplicación basada en encabezados debe tener el inicio de sesión único habilitado con Azure AD B2C. Abra un explorador y escriba `http://localhost:9772/`.

3. Se mostrará una página de inicio de sesión de Azure AD B2C.

## <a name="pass-user-attributes-to-the-header-based-application"></a>Paso de atributos de usuario a la aplicación basada en encabezados

1. DAB obtiene los atributos de usuario del proveedor de identidades y puede pasar los atributos de usuario a la aplicación mediante el encabezado o una cookie. Consulte las instrucciones sobre cómo [pasar atributos de usuario](https://docs.datawiza.com/step-by-step/step4.html) como la dirección de correo electrónico, el nombre y el apellido a la aplicación basada en encabezados. 

2. Después de configurar correctamente los atributos de usuario, debería ver el signo de verificación verde en cada uno de los atributos de usuario.

 ![La imagen muestra los atributos de usuario pasados](./media/partner-datawiza/pass-user-attributes.png)

## <a name="test-the-flow"></a>Prueba del flujo

1. Vaya a la dirección URL de la aplicación local.

2. DAB debe redirigirse a la página que configuró en el flujo de usuario.

3. Seleccione el proveedor de identidades en la lista de la página.

4. Una vez que se le redirija al proveedor de identidades, especifique sus credenciales según se solicite, incluido un token de Azure AD Multi-Factor Authentication (MFA) si el proveedor de identidades lo requiere.

5. Después de autenticarse correctamente, debe redirigirse a Azure AD B2C, que reenvía la solicitud de aplicación al identificador URI de redireccionamiento de DAB.

6. DAB evalúa las directivas, calcula los encabezados y envía el usuario a la aplicación de nivel superior.  

7. Debería ver la aplicación solicitada.

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte los artículos siguientes:

- [Directivas personalizadas de Azure AD B2C](./custom-policy-overview.md)

- [Introducción a las directivas personalizadas en Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)