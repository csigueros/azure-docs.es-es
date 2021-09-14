---
title: Implementación de directivas personalizadas con Acciones de GitHub
titleSuffix: Azure AD B2C
description: Aprenda a implementar directivas personalizadas de Azure AD B2C en una canalización de CI/CD mediante Acciones de GitHub.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 956803f14d9df49e99411eae2cb6a76db7636b03
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187810"
---
# <a name="deploy-custom-policies-with-github-actions"></a>Implementación de directivas personalizadas con Acciones de GitHub

[Acciones de GitHub](https://docs.github.com/actions/quickstart) permite crear flujos de trabajo de integración continua (CI) e implementación continua (CD) personalizados directamente en el repositorio GitHub. En este artículo se describe cómo automatizar la implementación de las [directivas personalizadas](user-flow-overview.md) de Azure Active Directory B2C (Azure AD B2C) mediante Acciones de GitHub.

Para automatizar el proceso de implementación de directivas personalizadas, use [GitHub Action para implementar directivas personalizadas de Azure AD B2C](https://github.com/marketplace/actions/deploy-azure-ad-b2c-custom-policy). Esta instancia de GitHub Action la ha desarrollado la [comunidad de Azure AD B2C](https://github.com/azure-ad-b2c/deploy-trustframework-policy). 

Esta acción implementa directivas personalizadas de Azure AD B2C en el inquilino de Azure AD B2C mediante [Microsoft Graph API](/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta&preserve-view=true). Si la directiva aún no existe en el inquilino, se creará. De lo contrario, se reemplazará.

> [!IMPORTANT]
> La administración de las directivas personalizadas de Azure AD B2C con Azure Pipelines usa actualmente la **vista previa** de las operaciones de disponibles en el punto de conexión `/beta` de Microsoft Graph API. No se admite su uso en aplicaciones de producción. Para obtener más información, consulte [Referencia del punto de conexión de la API de REST Microsoft Graph beta](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true).

## <a name="prerequisites"></a>Prerequisites

* Siga los pasos de [Introducción a las directivas personalizadas en Active Directory B2C](tutorial-create-user-flows.md).
* Si no ha creado un repositorio de GitHub, [cree uno](https://docs.github.com/en/get-started/quickstart/create-a-repo).  

## <a name="select-a-custom-policies-folder"></a>Selección de una carpeta de directivas personalizadas

El repositorio de GitHub puede contener todos los archivos de la directiva de Azure AD B2C y otros recursos. En el directorio raíz del repositorio, cree o elija una carpeta existente que contenga las directivas personalizadas. 

Por ejemplo, seleccione una carpeta denominada *policies*. Agregue los archivos de la directiva personalizada de Azure AD B2C a la carpeta *policies*. Después, **confirme** los cambios.

No **inserte** los cambios. Lo hará más adelante, después de configurar el flujo de trabajo de implementación.

## <a name="register-a-microsoft-graph-application"></a>Registro de una aplicación de Microsoft Graph

Para que la instancia de GitHub Action pueda interactuar con [Microsoft Graph API](microsoft-graph-operations.md), cree un registro de aplicación en el inquilino de Azure AD B2C. Si aún no lo ha hecho, [registre una aplicación de Microsoft Graph](microsoft-graph-get-started.md).

Para la instancia de GitHub Action tenga acceso a los datos de Microsoft Graph, conceda a la aplicación registrada los [permisos de aplicación](/graph/permissions-reference) pertinentes. Se concede el permiso **Microsoft Graph** > **Policy** > **Policy.ReadWrite.TrustFramework** dentro de los **permisos de API** del registro de la aplicación.

## <a name="create-a-github-encrypted-secret"></a>Creación de un secreto cifrado de GitHub

Los secretos de GitHub son variables de entorno cifradas que se crean en una organización, repositorio o entorno del repositorio. En este paso, almacenará el secreto de aplicación para la aplicación que registró anteriormente en el paso [Registrar una aplicación Graph MS](#register-a-microsoft-graph-application).

La instancia de GitHub Action para implementar directivas personalizadas de Azure AD B2C usa el secreto para adquirir un token de acceso que se usa para interactuar con Microsoft Graph API. Para más información, consulte [Creación de secretos cifrados para un repositorio](https://docs.github.com/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository).

Para crear un secreto de GitHub, siga estos pasos.

1. En GitHub, vaya a la página principal del repositorio.
1. En el nombre del repositorio, seleccione **Configuración**.
1. En la barra lateral izquierda, haga clic en **Secretos**.
1. Seleccione **New repository secret** (Nuevo secreto del repositorio).
1. En **Nombre**, escriba **ClientSecret**.
1. En **Valor**, escriba el secreto de aplicación que creó anteriormente.
1. Seleccione **Add secret** (Agregar secreto).

## <a name="create-a-github-workflow"></a>Creación de un flujo de trabajo de GitHub

Un flujo de trabajo de GitHub es un procedimiento automatizado que se agrega al repositorio. Los flujos de trabajo están integrados por uno o varios trabajos y se pueden programar o desencadenar mediante un evento. En este paso, creará un flujo de trabajo que implementa la directiva personalizada.

Para crear un flujo de trabajo, siga estos pasos:

1. En GitHub, vaya a la página principal del repositorio.
1. En el nombre del repositorio, seleccione **Acciones**.

   ![Captura de pantalla que muestra la pestaña Acciones de GitHub](media/deploy-custom-policies-github-action/github-actions-tab.png)

1. Si no ha configurado un flujo de trabajo antes, **configure un flujo de trabajo usted mismo**. En caso contrario, seleccione un **nuevo flujo de trabajo**.

   ![Captura de pantalla que muestra cómo crear un flujo de trabao](media/deploy-custom-policies-github-action/set-up-a-workflow.png)

1. GitHub ofrece crear un archivo de flujo de trabajo denominado `main.yml` en la carpeta `.github/workflows`. Este archivo contiene información sobre el flujo de trabajo, incluido el entorno de Azure AD B2C y las directivas personalizadas que se implementarán. En el editor web de GitHub, agregue el siguiente código YAML:

    ```yml
    on: push

    env:
      clientId: 00000000-0000-0000-0000-000000000000
      tenant: your-tenant.onmicrosoft.com
    
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
    
        - name: 'Upload TrustFrameworkBase Policy'
          uses: azure-ad-b2c/deploy-trustframework-policy@v3
          with:
            folder: "./Policies"
            files: "TrustFrameworkBase.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml"
            tenant: ${{ env.tenant }}
            clientId: ${{ env.clientId }}
            clientSecret: ${{ secrets.clientSecret }}
    ```

1.  Actualice las siguientes propiedades del archivo YAML:

    | Sección| Nombre | Value |
    | ---- | ----- |----- |
    | `env` | `clientId` | **Id. de aplicación (cliente)** de la aplicación que ha registrado en el paso [Registrar una aplicación MS Graph](#register-a-microsoft-graph-application). |
    |`env`| `tenant` | Su [nombre de inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C (por ejemplo, contoso.onmicrosoft.com). |
    | `with`| `folder`| Carpeta donde se almacenan los archivos de directivas personalizadas, por ejemplo, `./Policies`.|
    | `with`| `files` | Lista delimitada por comas de los archivos de directiva que se implementarán, por ejemplo, `TrustFrameworkBase.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml`.|
    
    > [!IMPORTANT]
    > Al ejecutar los agentes y cargar los archivos de directivas, asegúrese de que se carguen en el orden correcto:
    >
    > 1. *TrustFrameworkBase.xml*
    > 1. *TrustFrameworkExtensions.xml*
    > 1. *SignUpOrSignin.xml*
    > 1. *ProfileEdit.xml*
    > 1. *PasswordReset.xml*

1. Seleccione **Start commit** (Iniciar confirmación).
1. Debajo de los campos de mensaje de confirmación, indique si desea agregar la confirmación a la rama actual o a una nueva. Seleccione **Commit new file** (Confirmar nuevo archivo) o **Propose new file** (Proponer nuevo archivo) para crear una solicitud de incorporación de cambios.

## <a name="test-your-workflow"></a>Prueba del flujo de trabajo

Para probar el flujo de trabajo que creó, **inserte** los cambios de la directiva personalizada. Cuando el trabajo haya empezado a ejecutarse, podrá ver un gráfico de visualización del progreso de la ejecución y ver la actividad de cada paso en GitHub.

1. En GitHub, vaya a la página principal del repositorio.
1. En el nombre del repositorio, seleccione **Acciones**.
1. En la barra lateral izquierda, seleccione el flujo de trabajo que ha creado.
1. En **Workflow runs** (Ejecuciones de flujo de trabajo), seleccione el nombre de la ejecución que desea ver.

   ![Captura de pantalla que muestra cómo seleccionar la actividad de flujo de trabajo](media/deploy-custom-policies-github-action/workflow-report.png)

1. En **Jobs** (Trabajos) o en el gráfico de visualización, seleccione el trabajo que desea ver.
1. Vea los resultados de cada paso. En la captura de pantalla siguiente se muestra el registro del paso **Cargar directiva personalizada**.
 
   ![El registro de pasos de la directiva personalizada de carga](media/deploy-custom-policies-github-action/job-activity.png)


## <a name="optional-schedule-your-workflow"></a>Opcional: Programación del flujo de trabajo

El flujo de trabajo que ha creado lo desencadena el evento de [inserción](https://docs.github.com/actions/reference/events-that-trigger-workflows#push). Si lo prefiere, puede elegir otro evento para desencadenar el flujo de trabajo, por ejemplo, una [solicitud de incorporación de cambios](https://docs.github.com/actions/reference/events-that-trigger-workflows#pull_request).

También puede programar un flujo de trabajo para que se ejecute a horas UTC específicas mediante la [sintaxis cron de POSIX](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07). El evento de programación permite desencadenar un flujo de trabajo a una hora programada. Para obtener más información, consulte [Eventos programados](https://docs.github.com/actions/reference/events-that-trigger-workflows#scheduled-events).

En el ejemplo siguiente se desencadena el flujo de trabajo todos los días a las 5:30 y 17:30 UTC:

```yml
on:
  schedule:
    # * is a special character in YAML so you have to quote this string
    - cron:  '30 5,17 * * *'
```

Para editar el flujo de trabajo:

1. En GitHub, vaya a la página principal del repositorio.
1. En el nombre del repositorio, seleccione **Acciones**.
1. En la barra lateral izquierda, seleccione el flujo de trabajo que ha creado.
1. En **Workflow runs** (Ejecuciones de flujo de trabajo), seleccione el nombre de la ejecución que desea ver.
1. En el menú, seleccione los tres puntos **...** y, a continuación, seleccione **View the workflow file** (Ver el archivo de flujo de trabajo).

   ![Captura de pantalla que muestra cómo ver el archivo de flujo de trabajo](media/deploy-custom-policies-github-action/edit-workflow.png)
   
1. En el editor web de GitHub, seleccione **Edit** (Editar).
1. En el ejemplo anterior, cambie `on: push`.
1. **Confirme** los cambios.

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a configurar [eventos que desencadenan flujos de trabajo](https://docs.github.com/actions/reference/events-that-trigger-workflows).


