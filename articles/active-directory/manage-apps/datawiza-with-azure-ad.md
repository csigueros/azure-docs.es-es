---
title: Protección del acceso híbrido con Azure AD y Datawiza
description: En este tutorial, aprenderá a integrar Datawiza con Azure AD para el acceso híbrido seguro.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 8/27/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19a3f4835e3e2b20d0921aa2a259739ec6cd51bc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754177"
---
# <a name="tutorial-configure-datawiza-with-azure-active-directory-for-secure-hybrid-access"></a>Tutorial: Configuración de Datawiza con Azure Active Directory para el acceso híbrido seguro

En este tutorial de ejemplo, aprenderá a integrar Azure Active Directory (Azure AD) con [Datawiza](https://www.datawiza.com/) para el acceso híbrido seguro.

[Datawiza Access Broker (DAB)](https://www.datawiza.com/access-broker) de Datawiza amplía Azure AD para habilitar el inicio de sesión único (SSO) y controles de acceso granulares a fin de proteger aplicaciones locales y hospedadas en la nube, como Oracle E-Business Suite, Microsoft IIS y SAP.

Con esta solución, las empresas pueden pasar rápidamente de administradores de acceso web (WAM) heredados, como Symantec SiteMinder, NetIQ, Oracle e IBM a Azure AD sin volver a escribir aplicaciones. Las empresas también pueden usar Datawiza como una solución sin código o de poco código para integrar nuevas aplicaciones en Azure AD. Esto ahorra tiempo de ingeniería, reduce significativamente el costo y entrega el proyecto de una manera segura.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

- Suscripción a Azure. Si no tiene una suscripción, puede obtener una [cuenta de evaluación](https://azure.microsoft.com/free/).

- Un [inquilino de Azure AD](../fundamentals/active-directory-access-create-new-tenant.md) vinculado a la suscripción de Azure.

- [Docker](https://docs.docker.com/get-docker/) y [docker-compose](https://docs.docker.com/compose/install/) son necesarios para ejecutar DAB. Las aplicaciones se pueden ejecutar en cualquier plataforma, como en máquinas virtuales y equipos sin sistema operativo.

- Una aplicación que pasará de un sistema de identidades heredado a Azure AD. En este ejemplo, DAB se implementa en el mismo servidor en el que está la aplicación. La aplicación se ejecutará en localhost: 3001 y DAB redirecciona el tráfico mediante proxy a la aplicación por medio de localhost: 9772. El tráfico a la aplicación llegará primero a DAB y, después, se redireccionará mediante proxy a la aplicación.

## <a name="scenario-description"></a>Descripción del escenario

La integración de Datawiza incluye los componentes siguientes:

- [Azure AD](../fundamentals/active-directory-whatis.md): el servicio de administración de identidades y acceso basado en la nube de Microsoft, que ayuda a los usuarios a iniciar sesión y acceder a recursos internos y externos.

- Datawiza Access Broker (DAB): el usuario del servicio inicia sesión y pasa la identidad de forma transparente a las aplicaciones por medio de encabezados HTTP.

- Datawiza Cloud Management Console (DCMC): una consola de administración centralizada que administra DAB. DCMC proporciona API de interfaz de usuario y Restful para que los administradores administren las configuraciones de DAB y sus directivas de control de acceso.

En el siguiente diagrama de arquitectura se muestra la implementación.

![Imagen en la que se muestra el diagrama de la arquitectura](./media/datawiza-with-azure-active-directory/datawiza-architecture-diagram.png)

|Pasos| Descripción|
|:----------|:-----------|
|  1. | El usuario realiza una solicitud para acceder a la aplicación hospedada en el entorno local o en la nube. DAB redirige mediante proxy la solicitud realizada por el usuario a la aplicación.|
| 2. |DAB comprueba el estado de autenticación del usuario. Si no recibe un token de sesión, o el token de sesión proporcionado no es válido, envía al usuario a Azure AD para autenticarse.|
| 3. | Azure AD envía la solicitud del usuario al punto de conexión especificado durante el registro de la aplicación DAB en el inquilino de Azure AD.|
| 4. | DAB evalúa las directivas de acceso y calcula los valores de atributo que se van a incluir en los encabezados HTTP reenviados a la aplicación. Durante este paso, DAB puede llamar al proveedor de identidades para recuperar la información necesaria para establecer correctamente los valores de encabezado. DAB establece los valores de encabezado y envía la solicitud a la aplicación. |
| 5. |  El usuario ya está autenticado y tiene acceso a la aplicación.|

## <a name="onboard-with-datawiza"></a>Incorporación con Datawiza

Para integrar la aplicación local o hospedada en la nube con Azure AD, inicie sesión en [Datawiza Cloud Management Console](https://console.datawiza.com/) (DCMC).

## <a name="create-an-application-on-dcmc"></a>Creación de una aplicación en DCMC

[Cree una aplicación](https://docs.datawiza.com/step-by-step/step2.html) y genere un par de claves de `PROVISIONING_KEY` y `PROVISIONING_SECRET` para esta aplicación en DCMC.

Por Azure AD, Datawiza ofrece una cómoda [integración con un solo clic](https://docs.datawiza.com/tutorial/web-app-azure-one-click.html). Este método para integrar Azure AD con DCMC puede crear un registro de aplicación en su nombre en el inquilino de Azure AD.

![Imagen en la que se muestra la configuración de IDP](./media/datawiza-with-azure-active-directory/configure-idp.png)

En su lugar, si quiere usar una aplicación web existente en el inquilino de Azure AD, puede deshabilitar la opción y rellenar los campos del formulario. Necesitará el identificador de inquilino, el identificador de cliente y el secreto de cliente. [Cree una aplicación web y obtenga estos valores en el inquilino](https://docs.datawiza.com/idp/azure.html).

![Imagen en la que se muestra cómo configurar IDP mediante el formulario](./media/datawiza-with-azure-active-directory/use-form.png)

## <a name="run-dab-with-a-header-based-application"></a>Ejecución de DAB con una aplicación basada en encabezados

1. Para ejecutar DAB puede usar Docker o Kubernetes. La imagen de Docker es necesaria para que los usuarios creen una aplicación basada en encabezados de ejemplo. [Configure la integración de DAB y SSO](https://docs.datawiza.com/step-by-step/step3.html). [Implemente DAB con Kubernetes](https://docs.datawiza.com/tutorial/web-app-AKS.html). Se proporciona un archivo `docker-compose.yml` de imagen de Docker de ejemplo para que la descargue y la use. [Inicie sesión en el registro de contenedor](https://docs.datawiza.com/step-by-step/step3.html#important-step) para descargar las imágenes de DAB y la aplicación basada en encabezados.

    ```YML
    services:
      datawiza-access-broker:
      image: registry.gitlab.com/datawiza/access-broker
      container_name: datawiza-access-broker
      restart: always
      ports:
      - "9772:9772"
      environment:
      PROVISIONING_KEY: #############################################
      PROVISIONING_SECRET: ##############################################
      
      header-based-app:
      image: registry.gitlab.com/datawiza/header-based-app
      restart: always
     ports:
     - "3001:3001"
   ```

2. Después de ejecutar `docker-compose -f docker-compose.yml up`, la aplicación basada en encabezados debe tener el inicio de sesión único habilitado con Azure AD. Abra un explorador y escriba `http://localhost:9772/`.

3. Se mostrará una página de inicio de sesión de Azure AD.

## <a name="pass-user-attributes-to-the-header-based-application"></a>Paso de atributos de usuario a la aplicación basada en encabezados

1. DAB obtiene los atributos de usuario del proveedor de identidades y puede pasar los atributos de usuario a la aplicación mediante el encabezado o una cookie. Vea las instrucciones sobre cómo [pasar atributos de usuario](https://docs.datawiza.com/step-by-step/step4.html) como la dirección de correo electrónico, el nombre y el apellido a la aplicación basada en encabezados.

2. Después de configurar correctamente los atributos de usuario, debería ver el signo de verificación verde en cada uno.

   ![Imagen en la que se muestra la página principal de la aplicación Datawiza](./media/datawiza-with-azure-active-directory/datawiza-application-home-page.png)

## <a name="test-the-flow"></a>Prueba del flujo

1. Vaya a la dirección URL de la aplicación.

2. DAB debe redirigir a la página de inicio de sesión de Azure AD.

3. Después de autenticarse correctamente, se le debe redirigir a DAB.

4. DAB evalúa las directivas, calcula los encabezados y envía el usuario a la aplicación de nivel superior.

5. Debería aparecer la aplicación solicitada.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de Datawiza con Azure AD B2C](../../active-directory-b2c/partner-datawiza.md)

- [Documentación de Datawiza](https://docs.datawiza.com)