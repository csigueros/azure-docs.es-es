---
title: Implementación de contenido personalizado desde el repositorio
description: En este artículo se explica cómo crear conexiones con un repositorio de GitHub o Azure DevOps donde puede guardar el contenido personalizado e implementarlo en Microsoft Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9548e3dfd01e4cc0c48a0d5e61a68a0c1b3276d4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132336836"
---
# <a name="deploy-custom-content-from-your-repository-public-preview"></a>Implementación de contenido personalizado desde el repositorio (versión preliminar pública)

> [!IMPORTANT]
>
> La página **Repositorios** de Microsoft Sentinel se encuentra actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

El *contenido* de Microsoft Sentinel es contenido de Administración de eventos e información de seguridad (SIEM) que ayuda a los clientes a ingerir, supervisar, alertar, buscar, etc. en Microsoft Sentinel. Por ejemplo, el contenido de Microsoft Sentinel incluye conectores de datos, analizadores, libros y reglas de análisis. Para obtener más información, vea [Acerca de las soluciones y el contenido de Microsoft Sentinel](sentinel-solutions.md).

Puede usar el contenido de serie (integrado) que se proporciona en el centro de contenido de Microsoft Sentinel y personalizarlo para sus propias necesidades, o bien crear su propio contenido personalizado desde cero.

Al crear contenido personalizado, puede almacenarlo y administrarlo en sus propias áreas de trabajo de Microsoft Sentinel o en un repositorio de control de código fuente externo, incluidos repositorios de GitHub y Azure DevOps. En este artículo se explica cómo crear y administrar las conexiones entre Microsoft Sentinel y repositorios de control de código fuente externos. La administración del contenido de un repositorio externo le permite realizar actualizaciones en ese contenido fuera de Microsoft Sentinel, e implementarlo automáticamente en las áreas de trabajo.

> [!TIP]
> En este artículo *no* se describe cómo crear tipos específicos de contenido desde cero. Para obtener más información, vea la [wiki de GitHub de Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/wiki#get-started) pertinente de cada tipo de contenido.
>

## <a name="prerequisites-and-scope"></a>Requisitos previos y ámbito

Antes de conectar el área de trabajo de Microsoft Sentinel a un repositorio de control de código fuente externo, asegúrese de que tiene:

- Acceso a un repositorio de GitHub o Azure DevOps, con cualquier archivo de contenido personalizado que quiera implementar en sus áreas de trabajo en las [plantillas de Azure Resource Manager (ARM)](/azure/azure-resource-manager/templates/) pertinentes.

    Microsoft Sentinel admite actualmente conexiones solo con repositorios de GitHub y Azure DevOps.

- Un rol **Propietario** en el grupo de recursos que contiene el área de trabajo de Microsoft Sentinel. El rol **Propietario** es necesario para crear la conexión entre Microsoft Sentinel y el repositorio de control de código fuente.

### <a name="maximum-connections-and-deployments"></a>Número máximo de conexiones e implementaciones

- Cada área de trabajo de Microsoft Sentinel está limitada actualmente a **cinco conexiones**.

- El historial de implementaciones de cada grupo de recursos de Azure está limitado a **800 implementaciones**. Si tiene un gran volumen de implementaciones de plantillas de ARM en sus grupos de recursos, es posible que vea un error `Deployment QuotaExceeded`. Para obtener más información, consulte [DeploymentQuotaExceeded](/azure/azure-resource-manager/templates/deployment-quota-exceeded) en la documentación de las plantillas de Azure Resource Manager.

### <a name="validate-your-content"></a>Validación del contenido

La implementación de contenido en Microsoft Sentinel por medio de una conexión de repositorio no valida ese contenido, únicamente comprueba que los datos están en el formato correcto de plantilla de ARM.

Se recomienda validar las plantillas de contenido mediante el proceso de validación habitual. Puede aprovechar las herramientas y el [proceso de validación de GitHub de Microsoft Sentinel ](https://github.com/Azure/Azure-Sentinel/wiki#test-your-contribution) para configurar su propio proceso de validación.

## <a name="connect-a-repository"></a>Conexión de un repositorio

En este procedimiento se explica cómo conectar un repositorio de GitHub o Azure DevOps al área de trabajo de Microsoft Sentinel, donde puede guardar y administrar el contenido personalizado, en lugar de hacerlo en Microsoft Sentinel.

Cada conexión puede admitir varios tipos de contenido personalizado, como reglas de análisis, reglas de automatización, consultas de búsqueda, analizadores, cuadernos de estrategias y libros. Para obtener más información, vea [Acerca de las soluciones y el contenido de Microsoft Sentinel](sentinel-solutions.md).

**Para crear la conexión**:

1. Asegúrese de que ha iniciado sesión en su aplicación de control de código fuente con las credenciales que quiere usar para la conexión.  Si ha iniciado sesión con otras credenciales, primero debe cerrar sesión.

1. En Microsoft Sentinel, a la izquierda, en **Administración de contenido**, seleccione **Repositorios**.

1. Seleccione **Agregar nuevo** y, después, en la página **Crear una conexión nueva**, escriba un nombre descriptivo y una descripción para la conexión.

1. En la lista desplegable **Control de código fuente**, seleccione el tipo de repositorio al que quiere conectarse y, luego, seleccione **Autorizar**.

1. Seleccione una de las pestañas siguientes según su tipo de conexión:

    # <a name="github"></a>[GitHub](#tab/github)

    1. Escriba sus credenciales de GitHub cuando se le solicite.

        La primera vez que agregue una conexión, verá una nueva ventana o pestaña del explorador en la que se le pide que autorice la conexión a Microsoft Sentinel. Si ya ha iniciado sesión en su cuenta de GitHub en el mismo explorador, las credenciales de GitHub se rellenarán automáticamente.

    1. Ahora se mostrará el área **Repositorio** en la página **Crear una conexión nueva**, donde puede seleccionar un repositorio existente al que conectarse. Seleccione un repositorio de la lista y, después, seleccione **Add repository** (Agregar repositorio).

        La primera vez que se conecte a un repositorio específico, se abrirá una nueva ventana o pestaña del explorador en la que se le pedirá que instale la aplicación **Azure-Sentinel** en el repositorio. Si tiene varios repositorios, seleccione aquellos en los que quiera instalar la aplicación **Azure-Sentinel** e instálela.

        Se le dirigirá a GitHub para continuar con la instalación de la aplicación.

    1. Una vez instalada la aplicación **Azure-Sentinel** en su repositorio, la lista desplegable **Rama** de la página **Crear una conexión nueva** se rellena automáticamente con sus ramas. Seleccione la rama que quiere conectar al área de trabajo de Microsoft Sentinel.

    1. En la lista desplegable **Tipo de contenido**, seleccione el tipo de contenido que va a implementar.

        - Tanto los analizadores como las consultas de búsqueda usan la API de **búsquedas guardadas** para implementar contenido en Microsoft Sentinel. Si selecciona uno de estos tipos de contenido y también tiene contenido del otro tipo en la rama, se implementarán ambos tipos de contenido.

        - En cuanto a los demás tipos de contenido, al seleccionar uno en el panel **Crear una conexión nueva**, solo se implementa ese contenido en Microsoft Sentinel. No se implementan los otros tipos de contenido.

    1. Seleccione **Crear** para crear su conexión. Por ejemplo:

        :::image type="content" source="media/ci-cd/create-new-connection-github.png" alt-text="Captura de pantalla de una nueva conexión a un repositorio de GitHub.":::

    # <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

    > [!NOTE]
    > Al crear conexiones de Azure DevOps, los [usuarios invitados](/azure/active-directory/external-identities/what-is-b2b) no pueden conectarse de momento a un área de trabajo que no esté en su propio inquilino de Azure Active Directory. Estos escenarios entre inquilinos aún no son compatibles con las conexiones de Azure DevOps.
    >

    Está autorizado automáticamente a que Azure DevOps use las credenciales actuales de Azure. Para garantizar una conectividad válida, [compruebe que ha sido autorizado en la misma cuenta de Azure DevOps](https://aex.dev.azure.com/) a la que se conecta desde Microsoft Sentinel o use una ventana del explorador InPrivate para crear la conexión.
    
    1.  En Microsoft Sentinel, en las listas desplegables que aparecen, seleccione la **Organización**, el **Proyecto**, el **Repositorio**, la **Rama** y los **Tipos de contenido**.

        - Tanto los analizadores como las consultas de búsqueda usan la API de **búsquedas guardadas** para implementar contenido en Microsoft Sentinel. Si selecciona uno de estos tipos de contenido y también tiene contenido del otro tipo en la rama, se implementarán ambos tipos de contenido.

        - En cuanto a los demás tipos de contenido, al seleccionar uno en el panel **Crear una conexión nueva**, solo se implementa ese contenido en Microsoft Sentinel. No se implementan los otros tipos de contenido.

    1. Seleccione **Crear** para crear su conexión. Por ejemplo:

        :::image type="content" source="media/ci-cd/create-new-connection-devops.png" alt-text="Captura de pantalla de una nueva conexión a un repositorio de GitHub.":::

    ---

    > [!NOTE]
    > No puede crear conexiones duplicadas con el mismo repositorio y la misma rama en una sola área de trabajo de Microsoft Sentinel.
    >

Una vez creada la conexión, se genera un nuevo flujo de trabajo o una canalización en el repositorio, y el contenido almacenado en el repositorio se implementa en el área de trabajo de Microsoft Sentinel.

El tiempo de implementación puede variar en función de la cantidad de contenido que se va a implementar. 

### <a name="view-the-deployment-status"></a>Visualización del estado de la implementación

- **En GitHub**: en la pestaña **Actions** (Acciones) del repositorio. Seleccione el archivo **.yaml** del flujo de trabajo que se muestra para acceder a los registros de implementación detallados y a los mensajes de error específicos, si procede.
- **En Azure DevOps**: en la pestaña **Pipelines** (Canalizaciones) del repositorio.

Una vez finalizada la implementación:

- El contenido almacenado en el repositorio se muestra en el área de trabajo de Microsoft Sentinel, en la página de Microsoft Sentinel correspondiente.

- Los detalles de conexión de la página **Repositorios** se actualizan con el vínculo a los registros de implementación de la conexión. Por ejemplo:

    :::image type="content" source="media/ci-cd/deployment-logs-link.png" alt-text="Captura de pantalla de los registros de implementación de la conexión a un repositorio de GitHub.":::

### <a name="customize-the-deployment-workflow"></a>Personalización del flujo de trabajo de implementación

La implementación de contenido predeterminada implementa todo el contenido personalizado pertinente de la rama del repositorio conectado cada vez que se realizan inserciones en cualquier elemento de esa rama.

Si la configuración predeterminada para la implementación de contenido de GitHub o Azure DevOps no cumple todos sus requisitos, puede modificar la experiencia para que se ajuste a sus necesidades.

Por ejemplo, puede que quiera configurar distintos desencadenadores de implementación o implementar contenido solo desde una carpeta raíz específica.

Seleccione una de las pestañas siguientes según su tipo de conexión:

# <a name="github"></a>[GitHub](#tab/github)

**Para personalizar el flujo de trabajo de implementación de GitHub**:

1. En GitHub, vaya a su repositorio y busque su flujo de trabajo en el directorio `.github/workflows`.

    El nombre del flujo de trabajo se muestra en la primera línea del archivo de flujo de trabajo y tiene la siguiente convención de nomenclatura predeterminada: `Deploy Content to <workspace-name> [<deployment-id>]`.

    Por ejemplo: `name: Deploy Content to repositories-demo [xxxxx-dk5d-3s94-4829-9xvnc7391v83a]`

1. Seleccione el botón de lápiz situado en la parte superior derecha de la página para abrir el archivo para su edición y, después, modifique la implementación de la siguiente manera:

    - **Para modificar el desencadenador de implementación**, actualice la sección `on` del código, la cual describe el evento que desencadena la ejecución del flujo de trabajo.

        De forma predeterminada, esta configuración está establecida en `on: push`, lo que significa que el flujo de trabajo se desencadena con cualquier inserción en la rama conectada, incluidas las modificaciones en el contenido existente y las adiciones de contenido nuevo al repositorio. Por ejemplo:

        ```yml
        on:
            push:
                branches: [ main ]
                paths:
                - `**`
                - `!.github/workflows/**` # this filter prevents other workflow changes from triggering this workflow
                - `.github/workflows/sentinel-deploy-<deployment-id>.yml`
        ```

        Es posible que quiera cambiar esta configuración, por ejemplo, para programar el flujo de trabajo para que se ejecute periódicamente o para combinar diferentes eventos de flujo de trabajo.

        Para obtener más información, vea la [documentación de GitHub](https://docs.github.com/en/actions/learn-github-actions/events-that-trigger-workflows#configuring-workflow-events) sobre cómo configurar eventos de flujo de trabajo.

    - **Para modificar la ruta de acceso de la implementación**:

        En la configuración predeterminada que se muestra más arriba para la sección `on`, los caracteres comodín (`**`) de la primera línea de la sección `paths` indican que toda la rama está en la ruta de acceso de los desencadenadores de implementación.

        Esto significa que un flujo de trabajo de implementación se desencadena cada vez que se inserta contenido en cualquier parte de la rama.

        Más adelante en el archivo, la sección `jobs` incluye la siguiente configuración predeterminada: `directory: '${{ github.workspace }}'`. Esta línea indica que toda la rama de GitHub está en la ruta de acceso de la implementación de contenido, y no se está filtrando ninguna ruta de acceso de carpeta.

        Para implementar contenido únicamente desde una ruta de acceso de carpeta específica, agréguela a la configuración de `paths` y de `directory`. Por ejemplo, para implementar contenido únicamente desde una carpeta raíz denominada `SentinelContent`, actualice el código de la siguiente manera:

        ```yml
        paths:
        - `SentinelContent/**`
        - `!.github/workflows/**` # this filter prevents other workflow changes from triggering this workflow
        - `.github/workflows/sentinel-deploy-<deployment-id>.yml`

        ...
            directory: '${{ github.workspace }}/SentinelContent'
        ```

Para obtener más información, consulte la [documentación de GitHub](https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions#onpushpull_requestpaths) sobre Acciones de GitHub y sobre la edición de flujos de trabajo de GitHub.

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

**Para personalizar su canalización de implementación de Azure DevOps**:

1. En Azure DevOps, vaya a su repositorio y busque el archivo de definición de la canalización en el directorio `.sentinel`.

    El nombre de la canalización se muestra en la primera línea del archivo de canalización y tiene la siguiente convención de nomenclatura predeterminada: `Deploy Content to <workspace-name> [<deployment-id>]`.

    Por ejemplo: `name: Deploy Content to repositories-demo [xxxxx-dk5d-3s94-4829-9xvnc7391v83a]`

1. Seleccione el botón de lápiz situado en la parte superior derecha de la página para abrir el archivo para su edición y, después, modifique la implementación de la siguiente manera:

    - **Para modificar el desencadenador de implementación**, actualice la sección `trigger` del código, la cual describe el evento que desencadena la ejecución del flujo de trabajo.

        De forma predeterminada, esta configuración está establecida para detectar cualquier inserción en la rama conectada, incluidas las modificaciones en el contenido existente y las adiciones de contenido nuevo al repositorio.

        Cambie este desencadenador por cualquiera de los desencadenadores de Azure DevOps disponibles, como los desencadenadores de programación o de solicitudes de incorporación de cambios. Para obtener más información, consulte la [documentación sobre desencadenadores de Azure DevOps](/azure/devops/pipelines/yaml-schema).

    - **Para modificar la ruta de acceso de la implementación**:

        La configuración predeterminada de la sección `trigger` tiene el código siguiente, que indica que la rama `main` está en la ruta de acceso de los desencadenadores de implementación:

        ```yml
        trigger:
            branches:
                include:
                - main
        ```

        Esto significa que se desencadena una canalización de implementación cada vez que se inserta contenido en cualquier parte de la rama `main`.

        Para implementar contenido únicamente desde una ruta de acceso de carpeta específica, agregue el nombre de la carpeta a la sección `include` para el desencadenador, y a la sección `steps` para la ruta de acceso de implementación, según sea necesario.

        Por ejemplo, para implementar contenido únicamente desde una carpeta raíz denominada `SentinelContent` en su rama `main`, agregue la configuración de `include` y `workingDirectory` al código de la siguiente manera:

        ```yml
        paths:
            exclude:
            - .sentinel/*
            include:
            - .sentinel/sentinel-deploy-39d8ekc8-397-5963-49g8-5k63k5953829.yml
            - SentinelContent
        ....
        steps:
        - task: AzurePowerShell@5
          inputs:
            azureSubscription: `Sentinel_Deploy_ServiceConnection_0000000000000000`
            workingDirectory: `SentinelContent`
        ```

Para obtener más información, consulte la [documentación de Azure DevOps](/azure/devops/pipelines/yaml-schema) sobre el esquema YAML.

---

> [!IMPORTANT]
> Tanto en GitHub como en Azure DevOps, asegúrese de que los directorios de las rutas de acceso del desencadenador y de implementación sean coherentes.
>
## <a name="edit-or-delete-content-in-your-repository"></a>Edición o eliminación de contenido en el repositorio

Una vez que haya creado correctamente una conexión al repositorio de control de código fuente, siempre que se modifique o se agregue contenido en ese repositorio, el flujo de trabajo de implementación se ejecuta de nuevo e implementa todo el contenido del repositorio en todas las áreas de trabajo de Microsoft Sentinel conectadas.

Se recomienda editar cualquier contenido almacenado en un repositorio conectado *solo* en el repositorio y no en Microsoft Sentinel. Por ejemplo, para realizar cambios en las reglas de análisis, aplique dichos cambios directamente en GitHub o en Azure DevOps.

Si ha editado el contenido en Microsoft Sentinel, asegúrese de exportarlo al repositorio de control de código fuente para evitar que los cambios se sobrescriban la próxima vez que el contenido del repositorio se implemente en el área de trabajo.

Si va a eliminar contenido, asegúrese de eliminarlo tanto del repositorio como de Azure Portal. La eliminación de contenido del repositorio no lo elimina del área de trabajo de Microsoft Sentinel.

## <a name="remove-a-repository-connection"></a>Eliminación de la conexión a un repositorio

En este procedimiento se explica cómo quitar la conexión a un repositorio de control de código fuente desde Microsoft Sentinel.

**Para quitar la conexión**:

1. En Microsoft Sentinel, a la izquierda, en **Administración de contenido**, seleccione **Repositorios**.
1. En la cuadrícula, seleccione la conexión que quiere quitar y, después, seleccione **Eliminar**.
1. Seleccione **Sí** para confirmar la eliminación.

Después de quitar la conexión, el contenido que se ha implementado previamente mediante la conexión permanece en el área de trabajo de Microsoft Sentinel. El contenido agregado al repositorio después de quitar la conexión no se implementa.

> [!TIP]
> Si encuentra problemas o un mensaje de error al eliminar la conexión, se recomienda comprobar el control de código fuente para confirmar que se ha eliminado el flujo de trabajo de GitHub o la canalización de Azure DevOps asociados a la conexión.
>

### <a name="removing-the-microsoft-sentinel-app-from-your-github-repository"></a>Eliminación de la aplicación Microsoft Sentinel del repositorio de GitHub

Si piensa eliminar la aplicación Microsoft Sentinel de un repositorio de GitHub, se recomienda quitar *primero* todas las conexiones asociadas desde la página **Repositorios** de Microsoft Sentinel.

Cada instalación de la aplicación Microsoft Sentinel tiene un identificador único que se usa al agregar y quitar la conexión. Si falta el identificador o se ha cambiado, debe quitar la conexión desde la página **Repositorios** de Microsoft Sentinel y quitar manualmente el flujo de trabajo del repositorio de GitHub para evitar futuras implementaciones de contenido.

## <a name="next-steps"></a>Pasos siguientes

Use el contenido personalizado de Microsoft Sentinel de la misma manera que usaría el contenido de serie.

Para más información, consulte:

- [Detección e implementación de soluciones de Microsoft Sentinel (versión preliminar pública)](sentinel-solutions-deploy.md)
- [Conectores de datos de Microsoft Sentinel](connect-data-sources.md)
- [Analizadores del modelo de información SIEM avanzado (ASIM) (versión preliminar pública)](normalization-about-parsers.md)
- [Visualización de los datos recopilados](get-visibility.md)
- [Creación de reglas de análisis personalizadas para detectar amenazas](detect-threats-custom.md)
- [Búsqueda de amenazas con Microsoft Sentinel](hunting.md)
- [Uso de listas de seguimiento de Microsoft Sentinel](watchlists.md)
- [Automatización de la respuesta a amenazas con cuadernos de estrategias en Microsoft Sentinel](automate-responses-with-playbooks.md)
