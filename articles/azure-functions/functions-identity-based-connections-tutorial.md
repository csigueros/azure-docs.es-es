---
title: Creación de una aplicación de funciones sin secretos de almacenamiento predeterminados en su definición
titleSuffix: Azure Functions
ms.service: azure-functions
description: Obtenga información sobre cómo quitar las cadenas de conexión de almacenamiento de la definición de la aplicación de funciones.
ms.topic: tutorial
ms.date: 10/20/2021
ms.openlocfilehash: 8e6e4bfc80f2154843aab5d3971854054f4319b4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271220"
---
# <a name="tutorial-create-a-function-app-that-connects-to-azure-services-using-identities-instead-of-secrets"></a>Tutorial: Creación de una aplicación de funciones que se conecta a los servicios de Azure mediante identidades en lugar de secretos

En este tutorial, se muestra cómo configurar una aplicación de funciones mediante identidades de Azure Active Directory en lugar de secretos o cadenas de conexión, siempre que sea posible. El uso de identidades ayuda a evitar la pérdida accidental de secretos confidenciales y puede proporcionar una mejor visibilidad sobre cómo se accede a los datos. Para más información sobre las conexiones basadas en identidades, consulte [Configuración de una conexión basada en identidades](functions-reference.md#configure-an-identity-based-connection).

Si bien los procedimientos que se muestran funcionan generalmente para todos los lenguajes, este tutorial admite actualmente funciones de biblioteca de clases de C# en Windows específicamente. 

En este tutorial, obtendrá información sobre cómo:
> [!div class="checklist"]
> * Crear una aplicación de funciones en Azure mediante una plantilla de ARM
> * Habilitar identidades administradas asignadas por el sistema y asignadas por el usuario en la aplicación de funciones
> * Crear asignaciones de roles que conceden permisos a otros recursos
> * Mover los secretos que no se pueden reemplazar por identidades a Azure Key Vault
> * Configurar una aplicación para que se conecte al almacenamiento de host predeterminado mediante su identidad administrada

Después de completar este tutorial, debe completar el tutorial siguiente, que muestra cómo [usar conexiones basadas en identidades en lugar de secretos con desencadenadores y enlaces]. 

## <a name="prerequisites"></a>Prerrequisitos

+ Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2), versión 3.x.

## <a name="why-use-identity"></a>¿Por qué usar identidades?

La administración de secretos y credenciales es un desafío común para los equipos de todos los tamaños. Los secretos se deben proteger frente a robos o divulgaciones accidentales, y es posible que se deban rotar periódicamente. Muchos servicios de Azure permiten usar en su lugar una identidad de [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) para autenticar los clientes y comprobarlos con sus permisos, que se pueden modificar y revocar rápidamente. Esto permite un mayor control sobre la seguridad de las aplicaciones con menos sobrecarga operativa. Una identidad podría ser un usuario humano, como el desarrollador de una aplicación, o una aplicación que se ejecuta en Azure con una [identidad administrada](../active-directory/managed-identities-azure-resources/overview.md).

Algunos servicios no admiten la autenticación de Azure Active Directory, por lo que es posible que las aplicaciones aún requieran secretos. Sin embargo, se pueden almacenar en [Azure Key Vault](../key-vault/general/overview.md), lo que ayuda a simplificar el ciclo de vida de administración de los secretos. El acceso a un almacén de claves también se controla con identidades.

Al comprender cómo usar identidades en lugar de secretos cuando se puede y cómo usar Key Vault cuando no se puede, podrá reducir el riesgo, reducir la sobrecarga operativa y, por lo general, mejorar la posición de seguridad de las aplicaciones.

## <a name="create-a-function-app-that-uses-key-vault-for-necessary-secrets"></a>Creación de una aplicación de funciones que usa Key Vault para los secretos necesarios

Azure Files es un ejemplo de un servicio que aún no admite la autenticación de Azure Active Directory para los recursos compartidos de archivos SMB. Azure Files es el sistema de archivos predeterminado para implementaciones de Windows en los planes Premium y Consumo. Aunque podríamos [quitar Azure Files por completo](./storage-considerations.md#create-an-app-without-azure-files), esto presenta limitaciones que es posible que no desee. En su lugar, vamos a mover la cadena de conexión de Azure Files a Azure Key Vault. De este modo, se administra de forma centralizada, con el acceso controlado mediante la identidad.

### <a name="create-an-azure-key-vault"></a>Crear una instancia de Azure Key Vault

En primer lugar, necesitará un almacén de claves en el que almacenar los secretos. Lo configurará para usar el [control de acceso basado en rol (RBAC)](../role-based-access-control/overview.md) de Azure para determinar quién puede leer secretos del almacén.

1. En [Azure Portal](https://portal.azure.com), elija **Crear un recurso (+)** .

1. En la página **Crear un recurso**, seleccione **Seguridad** > **Key Vault**.

1. En la página **Básico**, use la tabla siguiente para configurar el almacén de claves.

    | Opción      | Valor sugerido  | Descripción |
    | ------------ | ---------------- | ----------- |
    | **Suscripción** | Su suscripción | Suscripción en la que se creará esta aplicación de funciones nueva. |
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nombre del grupo de recursos nuevo en el que se creará la aplicación de funciones. |
    | **Nombre del almacén de claves** | Nombre único globalmente | Nombre que identifica el nuevo almacén de claves. El nombre del almacén solo debe contener caracteres alfanuméricos y guiones y no puede empezar por un número. |
    | **Plan de tarifa** | Estándar | Opciones de facturación. Estándar es suficiente para este tutorial. |
    |**Región**| Región preferida | Elija una [región](https://azure.microsoft.com/regions/) cerca de usted o cerca de otros servicios a los que tendrán acceso las funciones. |

    Use las selecciones predeterminadas para las secciones "Opciones de recuperación". 

1. Anote el nombre que ha utilizado, ya que lo necesitará más adelante.

1. Haga clic en **Siguiente: Directiva de acceso** para ir a la pestaña **Directiva de acceso**.

1. En **Modelo de permisos**, elija **Control de acceso basado en roles de Azure**.

1. Seleccione **Revisar + crear**. Revise la configuración y, a continuación, haga clic en **Crear**.

### <a name="set-up-an-identity-and-permissions-for-the-app"></a>Configuración de una identidad y permisos para la aplicación

Para poder usar Azure Key Vault, la aplicación deberá tener una identidad a la que se pueda conceder permiso para leer secretos. Esta aplicación usará una identidad asignada por el usuario para que los permisos se puedan configurar incluso antes de que se cree la aplicación. Puede obtener más información sobre las identidades administradas para Azure Functions en el tema [Cómo usar identidades administradas para App Service y Azure Functions](../app-service/overview-managed-identity.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

1. En [Azure Portal](https://portal.azure.com), elija **Crear un recurso (+)** .

1. En la página **Crear un recurso**, seleccione **Identidad** > **Identidad administrada asignada por el usuario**.

1. En la página **Básico**, use la tabla siguiente para configurar la identidad.

    | Opción      | Valor sugerido  | Descripción |
    | ------------ | ---------------- | ----------- |
    | **Suscripción** | Su suscripción | Suscripción en la que se creará esta aplicación de funciones nueva. |
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nombre del grupo de recursos nuevo en el que se creará la aplicación de funciones. |
    |**Región**| Región preferida | Elija una [región](https://azure.microsoft.com/regions/) cerca de usted o cerca de otros servicios a los que tendrán acceso las funciones. |
    | **Nombre** | Nombre único globalmente | Nombre que identifica la nueva identidad asignada por el usuario. |

1. Seleccione **Revisar + crear**. Revise la configuración y, a continuación, haga clic en **Crear**.

1. Cuando se haya creado la identidad, vaya a ella en el portal. Seleccione **Propiedades** y anote el **Identificador de recurso**, ya que lo necesitará más adelante.

1. Seleccione **Asignaciones de roles de Azure** y haga clic en **Agregar asignación de roles (versión preliminar)** .

1. En la página **Agregar asignación de roles (versión preliminar)** , use las opciones como se muestra en la tabla siguiente.

    | Opción      | Valor sugerido  | Descripción |
    | ------------ | ---------------- | ----------- |
    | **Ámbito** |  Key Vault |  Ámbito es el conjunto de recursos al que se aplica la asignación de roles. El ámbito tiene niveles que se heredan en niveles inferiores. Por ejemplo, si selecciona un ámbito de suscripción, la asignación de roles se aplica a todos los grupos de recursos y recursos de la suscripción. |
    |**Suscripción**| Su suscripción | Suscripción en la que se creará esta aplicación de funciones nueva. |
    |**Recurso**| Almacén de claves | El almacén de claves que creó anteriormente. |
    | **Rol** | Usuario de secretos de Key Vault | Un rol es una colección de permisos que se conceden. El usuario de secretos de Key Vault concede permiso para que la identidad lea los valores secretos del almacén. |

1. Seleccione **Guardar**. El rol puede tardar un minuto o dos en aparecer al actualizar la lista de asignaciones de roles de la identidad.

La identidad ahora podrá leer los secretos almacenados en el almacén. Más adelante en el tutorial, agregará asignaciones de roles adicionales para distintos propósitos.

### <a name="generate-a-template-for-creating-a-function-app"></a>Generación de una plantilla para crear una aplicación de funciones

La experiencia del portal para crear una aplicación de funciones no interactúa con Azure Key Vault, por lo que deberá generar y editar una plantilla de Azure Resource Manager. A continuación, puede usar esta plantilla para crear la aplicación de funciones que haga referencia a la cadena de conexión de Azure Files desde el almacén de claves.

> [!IMPORTANT]
> No cree la aplicación de funciones hasta que edite la plantilla de ARM. La configuración de Azure Files se debe establecer en el momento de la creación de la aplicación.

1. En [Azure Portal](https://portal.azure.com), elija **Crear un recurso (+)** .

1. En la página **Crear un recurso**, seleccione **Proceso** > **Aplicación de funciones**.

1. En la página **Básico**, use la tabla siguiente para configurar la aplicación de funciones.

    | Opción      | Valor sugerido  | Descripción |
    | ------------ | ---------------- | ----------- |
    | **Suscripción** | Su suscripción | Suscripción en la que se creará esta aplicación de funciones nueva. |
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nombre del grupo de recursos nuevo en el que se creará la aplicación de funciones. |
    | **Nombre de la aplicación de función** | Nombre único globalmente | Nombre que identifica la nueva Function App. Los caracteres válidos son `a-z` (no distingue mayúsculas de minúsculas), `0-9` y `-`.  |
    |**Publicar**| Código | Elija publicar archivos de código o un contenedor de Docker. |
    | **Pila en tiempo de ejecución** | .NET | En este tutorial se usa .NET. |
    |**Región**| Región preferida | Elija una [región](https://azure.microsoft.com/regions/) cerca de usted o cerca de otros servicios a los que tendrán acceso las funciones. |

1. Seleccione **Revisar + crear**. La aplicación usa los valores predeterminados en la página **Hospedaje** y **Supervisión**. Puede revisar las opciones predeterminadas y se incluirán en la plantilla de ARM que genere. 

1. En lugar de crear la aplicación de función aquí, elija **Descargar una plantilla para la automatización**, que se encuentra a la derecha del botón **Siguiente**.

1. En la página de la plantilla, seleccione **Implementar** y, en la página Implementación personalizada, seleccione **Editar plantilla**.

    :::image type="content" source="./media/functions-identity-connections-tutorial/function-app-portal-template-deploy-button.png" alt-text="Captura de pantalla que muestra dónde encontrar el botón Implementar en la parte superior de la pantalla de la plantilla.":::

### <a name="edit-the-template"></a>Edición de la plantilla

Ahora, editará la plantilla para almacenar la cadena de conexión de Azure Files en Key Vault y permitir que la aplicación de funciones haga referencia a ella. Asegúrese de que tiene los siguientes valores de las secciones anteriores antes de continuar:

- Identificador del recurso de la identidad administrada asignada por el usuario
- nombre del almacén de claves.

> [!NOTE]
> Si fuera a crear una plantilla completa para la automatización, querría incluir definiciones para los recursos de identidad y asignación de roles, con las cláusulas `dependsOn` adecuadas. Esto reemplazaría los pasos anteriores que usaban el portal. Consulte [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](../azure-resource-manager/templates/syntax.md) y la documentación de cada servicio.


1. En el editor, busque el lugar dónde comienza la matriz `resources`. Antes de la definición de la aplicación de funciones, agregue la siguiente sección, que coloca la cadena de conexión de Azure Files en Key Vault. Reemplace "VAULT_NAME" por el nombre del almacén de claves.

    ```json
    {
        "type": "Microsoft.KeyVault/vaults/secrets",
        "apiVersion": "2016-10-01",
        "name": "VAULT_NAME/azurefilesconnectionstring",
        "properties": {
            "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01').keys[0].value,';EndpointSuffix=','core.windows.net')]"
        },
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]"
        ]
    },
    ``` 
    
1. En la definición del recurso de la aplicación de funciones (cuyo elemento `type` se ha establecido en `Microsoft.Web/sites`), agregue `Microsoft.KeyVault/vaults/VAULT_NAME/secrets/azurefilesconnectionstring` a la matriz `dependsOn`. De nuevo, reemplace "VAULT_NAME" por el nombre del almacén de claves. Esto hace que la aplicación no se cree antes de definir ese secreto. La matriz `dependsOn` debe tener un aspecto similar al ejemplo siguiente.

    ```json
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "location": "[parameters('location')]",
            "tags": null,
            "dependsOn": [
                "microsoft.insights/components/idcxntut",
                "Microsoft.KeyVault/vaults/VAULT_NAME/secrets/azurefilesconnectionstring",
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]"
            ],
            // ...
        }
    ```

1. Agregue el bloque `identity` del ejemplo siguiente a la definición del recurso de la aplicación de funciones. Sustituya "IDENTITY_RESOURCE_ID" por el identificador de recurso de la identidad asignada por el usuario.

    ```json
    {
        "apiVersion": "2018-11-01",
        "name": "[parameters('name')]",
        "type": "Microsoft.Web/sites",
        "kind": "functionapp",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned,UserAssigned",
            "userAssignedIdentities": {
                "IDENTITY_RESOURCE_ID": {}
            }
        },
        "tags": null,
        // ...
    }
    ```

    Este bloque `identity` también configura una identidad asignada por el sistema que usará más adelante en este tutorial.

1. Agregue la propiedad `keyVaultReferenceIdentity` al objeto `properties` de la aplicación de funciones, como se muestra en el ejemplo siguiente. Sustituya "IDENTITY_RESOURCE_ID" por el identificador de recurso de la identidad asignada por el usuario.

    ```json
    {
        // ...
         "properties": {
                "name": "[parameters('name')]",
                "keyVaultReferenceIdentity": "IDENTITY_RESOURCE_ID",
                // ...
         }
    }
    ```

    Necesita esta configuración porque una aplicación podría tener varias identidades asignadas por el usuario configuradas. Siempre que quiera usar una identidad asignada por el usuario, debe especificar cuál mediante algún identificador. Esto no es cierto en las identidades asignadas por el sistema, ya que una aplicación solo tendrá una. Muchas características que usan la identidad administrada suponen que deben usar la identidad asignada por el sistema de manera predeterminada.

1. Ahora, busque los objetos JSON que definen la configuración de la aplicación `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`, que deben tener un aspecto parecido al del ejemplo siguiente:

    ```json
    {
        "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01').keys[0].value,';EndpointSuffix=','core.windows.net')]"
    },
    ```

1. Reemplace el campo `value` por una referencia al secreto, como se muestra en el ejemplo siguiente. Reemplace "VAULT_NAME" por el nombre del almacén de claves.

    ```json
    {
        "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
        "value": "[concat('@Microsoft.KeyVault(SecretUri=', reference(resourceId('Microsoft.KeyVault/vaults/secrets', 'VAULT_NAME', 'azurefilesconnectionstring')).secretUri, ')')]"
    },
    ```

1. Seleccione **Guardar** para guardar la plantilla de ARM actualizada.

### <a name="deploy-the-modified-template"></a>Implementación de la plantilla modificada

1. Asegúrese de que las opciones de creación, incluido el **grupo de recursos**, siguen siendo correctas y seleccione **Revisar y crear**.

1. Una vez que se valide la plantilla, anote el **nombre de la cuenta de almacenamiento**, ya que usará esta cuenta más adelante. Por último, seleccione **Crear** para crear los recursos de Azure e implementar el código en la aplicación de funciones. 

1. Una vez completada la implementación, seleccione **Ir al grupo de recursos** y, a continuación, seleccione la nueva aplicación de funciones. 

Felicidades. Ha creado correctamente la aplicación de funciones para hacer referencia a la cadena de conexión de Azure Files desde Azure Key Vault.

Siempre que la aplicación necesite agregar una referencia a un secreto, solo tendría que definir una nueva configuración de la aplicación que apunte al valor almacenado en Key Vault. Para más información sobre esto, consulte [Uso de referencias de Key Vault para App Service y Azure Functions](../app-service/app-service-key-vault-references.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

> [!TIP]
> La [cadena de conexión de Application Insights](../azure-monitor/app/sdk-connection-string.md) y su clave de instrumentación incluida no se consideran secretos y se pueden recuperar de Application Insights con permisos de [lector](../role-based-access-control/built-in-roles.md#reader). No es necesario moverlos a Key Vault, aunque puede hacerlo.

## <a name="use-managed-identity-for-azurewebjobsstorage-preview"></a>Uso de una identidad administrada para AzureWebJobsStorage (versión preliminar)

A continuación, usará la identidad asignada por el sistema que configuró en los pasos anteriores para la conexión de `AzureWebJobsStorage`. El entorno de ejecución de Functions y varios desencadenadores y enlaces utilizan `AzureWebJobsStorage` para la coordinación entre varias instancias en ejecución. Es necesario para que la aplicación de funciones funcione y, al igual que Azure Files, se configura con una cadena de conexión de manera predeterminada al crear una nueva aplicación de funciones.

### <a name="grant-the-system-assigned-identity-access-to-the-storage-account"></a>Concesión de acceso a la identidad asignada por el sistema a la cuenta de almacenamiento

De forma similar a los pasos que realizó antes con la identidad asignada por el usuario y el almacén de claves, ahora creará una asignación de roles que concede a la identidad asignada por el sistema acceso a la cuenta de almacenamiento.

1. En [Azure Portal](https://portal.azure.com), vaya a la cuenta de almacenamiento que se creó anteriormente con la aplicación de funciones.

1. Seleccione **Access Control (IAM)** . Aquí es donde puede ver y configurar quién tiene acceso al recurso.

1. Haga clic en **Agregar** y seleccione **Agregar asignación de roles**.

1. Busque **Propietario de datos de Storage Blob**, selecciónelo y haga clic en **Siguiente**.

1. En la pestaña **Miembros**, en **Asignar acceso a**, seleccione **Identidad administrada**.

1. Haga clic en **Seleccionar miembros** para abrir el panel para **seleccionar identidades administradas**.

1. Confirme que la **Suscripción** sea aquella en la que creó los recursos anteriormente.

1. En el selector **Identidad administrada**, seleccione **Aplicación de funciones** de la categoría **Identidad administrada asignada por el sistema**. La etiqueta "Aplicación de funciones" puede llevar un número entre paréntesis al lado, lo que indica el número de aplicaciones de la suscripción con identidades asignadas por el sistema.

1. La aplicación debe aparecer en una lista debajo de los campos de entrada. Si no la ve, puede usar el cuadro **Seleccionar** para filtrar los resultados por el nombre de la aplicación.

1. Haga clic en la aplicación. Debe colocarse en la sección **Miembros seleccionados**. Haga clic en **Seleccionar**.

1. En la pantalla **Agregar asignación de roles**, haga clic en **Revisar y asignar**. Revise la configuración y haga clic en **Revisar y asignar**.

### <a name="edit-the-azurewebjobsstorage-configuration"></a>Edición de la configuración AzureWebJobsStorage

A continuación, actualizará la aplicación de funciones para que use su identidad asignada por el sistema cuando use Blob service para el almacenamiento de host.

> [!IMPORTANT]
> Algunos desencadenadores y enlaces usan la configuración `AzureWebJobsStorage`, y esas extensiones también deben poder usar conexiones basadas en identidades. Es posible que las aplicaciones que usan desencadenadores de blobs o desencadenadores del centro de eventos tengan que actualizar esas extensiones. Dado que no se ha definido ninguna función para esta aplicación, todavía no hay ningún problema. Para más información sobre este requisito, consulte [Conexión al almacenamiento de host con una identidad (versión preliminar)](./functions-reference.md#connecting-to-host-storage-with-an-identity-preview).
>
> De forma similar, se usa `AzureWebJobsStorage` para los artefactos de implementación cuando se usa la compilación del lado servidor en el plan de Consumo para Linux. Al habilitar las conexiones basadas en identidades para `AzureWebJobsStorage` en el plan de Consumo para Linux, deberá realizar la implementación mediante un [paquete de implementación externo](/run-functions-from-deployment-package).

1. Vaya a la aplicación de funciones en [Azure Portal](https://portal.azure.com).

1. En **Configuración**, seleccione **Configuración**.

1. Seleccione el botón **Editar** situado junto a la configuración de la aplicación **AzureWebJobsStorage** y cámbielo en función de los valores siguientes.

    | Opción      | Valor sugerido  | Descripción |
    | ------------ | ---------------- | ----------- |
    | **Nombre** |  AzureWebJobsStorage__accountName | Actualice el nombre de **AzureWebJobsStorage** al nombre exacto `AzureWebJobsStorage__accountName`. Esta configuración indica al host que use la identidad en lugar de buscar un secreto almacenado. La nueva configuración usa un carácter de subrayado doble (`__`), que es un carácter especial en la configuración de la aplicación.  |
    | **Valor** | Nombre de la cuenta | Actualice el nombre de la cadena de conexión para que sea únicamente el **nombre de la cuenta**. |

    Esta configuración permitirá al sistema saber que debe usar una identidad para conectarse al recurso.

1. Seleccione **Aceptar** y, a continuación, **Guardar** > **Continuar** para guardar los cambios. 

Ha quitado el requisito de la cadena de conexión de almacenamiento para AzureWebJobsStorage mediante la configuración de la aplicación para conectarse a blobs mediante identidades administradas.  

## <a name="next-steps"></a>Pasos siguientes 

En este tutorial, se ha mostrado cómo crear una aplicación de funciones sin almacenar secretos en su configuración.

En el siguiente tutorial, aprenderá a usar la identidad en las conexiones de desencadenadores y enlaces.

> [!div class="nextstepaction"]
> [usar conexiones basadas en identidades en lugar de secretos con desencadenadores y enlaces]

[usar conexiones basadas en identidades en lugar de secretos con desencadenadores y enlaces]: ./functions-identity-based-connections-tutorial-2.md
