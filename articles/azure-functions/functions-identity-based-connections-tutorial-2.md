---
title: Uso de conexiones basadas en identidades con desencadenadores y enlaces de Azure Functions
ms.service: azure-functions
description: Aprenda a usar conexiones basadas en identidades en lugar de cadenas de conexión al conectarse a una cola de Service Bus mediante Azure Functions.
ms.topic: tutorial
ms.date: 10/20/2021
ms.openlocfilehash: 5a3eb4dc6006d9072abac95b7940692e8af737a5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271270"
---
# <a name="tutorial-use-identity-based-connections-instead-of-secrets-with-triggers-and-bindings"></a>Tutorial: Uso de conexiones basadas en identidades en lugar de secretos con desencadenadores y enlaces

En este tutorial se muestra cómo configurar Azure Functions para conectarse a las colas de Azure Service Bus mediante identidades administradas en lugar de secretos almacenados en la configuración de la aplicación de funciones. El tutorial es una continuación del tutorial [Creación de una aplicación de funciones sin secretos de almacenamiento predeterminados en su definición][tutorial anterior]. Para más información sobre las conexiones basadas en identidades, consulte [Configuración de una conexión basada en identidades.](functions-reference.md#configure-an-identity-based-connection)

Si bien los procedimientos que se muestran funcionan generalmente para todos los lenguajes, este tutorial admite actualmente funciones de biblioteca de clases de C# en Windows específicamente. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
>
> * Cree un espacio de nombres de Service Bus y una cola.
> * Configuración de la aplicación de funciones con identidad administrada
> * Creación de una asignación de roles que conceda permiso a esa identidad para leer desde la cola Service Bus
> * Creación e implementación de una aplicación de funciones con un desencadenador de Service Bus.
> * Compruebe la conexión basada en identidades a Service Bus

## <a name="prerequisite"></a>Requisito previo

Complete el tutorial anterior: [Creación de una aplicación de funciones con conexiones basadas en identidades][tutorial anterior].

## <a name="create-a-service-bus-and-queue"></a>Cree una cola i un Service Bus

1. En [Azure Portal](https://portal.azure.com), elija **Crear un recurso (+)** .

1. En la página **Crear un recurso**, seleccione **Integración** > **Service Bus**.

1. En la página **Aspectos básicos**, use la tabla siguiente para configurar las opciones de espacio de nombres de Service Bus. Use los valores predeterminados en las opciones restantes.

    | Opción      | Valor sugerido  | Descripción      |
    | ------------ | ---------------- | ---------------- |
    | **Suscripción** | Su suscripción | Suscripción en la que se han creado los recursos. |
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | El grupo de recursos que creó con la aplicación de funciones. |
    | **Nombre del espacio de nombres** | Nombre único globalmente | El espacio de nombres de la instancia desde el que se va a desencadenar la función. Dado que el espacio de nombres es accesible públicamente, debe usar un nombre que sea globalmente único en Azure. El nombre también debe tener entre 6 y 50 caracteres, contener solo caracteres alfanuméricos y guiones, y no puede empezar con un número. |
    | **[Ubicación](https://azure.microsoft.com/regions/)** | myFunctionRegion | La región en la que creó la aplicación de funciones. |
    | **Plan de tarifa** | Básico | El nivel de Service Bus básico. |

1. Seleccione **Revisar + crear**. Una vez que se complete la validación, seleccione **Crear**.

1. Una vez finalizada la implementación, seleccione **Ir al recurso**.

1. En el nuevo espacio de nombres de Service Bus, seleccione **+ Cola** para agregar una cola.

1. Escriba `myinputqueue` como nombre de la nueva cola y seleccione **Crear**.

Ahora que tiene una cola, agregue una asignación de roles a la identidad administrada de la aplicación de funciones.

## <a name="configure-your-service-bus-trigger-with-a-managed-identity"></a>Configure el desencadenador de Service Bus con una identidad administrada

Para usar desencadenadores de Service Bus con conexiones basadas en identidades, deberá agregar la asignación de roles de **Receptor de datos de Azure Service Bus** a la identidad administrada en la aplicación de funciones. Este rol es necesario cuando se usan identidades administradas para desencadenarse fuera del espacio de nombres de Service Bus. También puede agregar su propia cuenta a este rol, lo que permite conectarse al espacio de nombres de Service Bus durante las pruebas locales.

> [!NOTE]
> Los requisitos de rol para usar conexiones basadas en identidades varían en función del servicio y de cómo se conecte a él. Las necesidades varían entre desencadenadores, enlaces de entrada y de salida. Para obtener más información sobre requisitos de rol específicos, consulte la documentación sobre desencadenadores y enlaces para el servicio.

1. En el espacio de nombres de Service Bus que acaba de crear, seleccione **Access Control (IAM)** . Aquí es donde puede ver y configurar quién tiene acceso al recurso.  

1. Haga clic en **Agregar** y seleccione **Agregar asignación de roles**.

1. Busque **Receptor de datos de Azure Service Bus**, selecciónelo y haga clic en **Siguiente**.

1. En la pestaña **Miembros**, en **Asignar acceso a**, seleccione **Identidad administrada**

1. Haga clic en **Seleccionar miembros** para abrir el panel de **Seleccionar identidades administradas**.

1. Confirme que la **Suscripción** es donde creó los recursos previamente.

1. En el selector de **Identidades administradas**, seleccione **Aplicación de funciones** de la categoría **Identidad administrada asignada por el sistema**. La etiqueta "Aplicación de funciones" puede llevar un número entre paréntesis al lado, lo que indica el número de aplicaciones de la suscripción con identidades asignadas por el sistema.

1. La aplicación debe aparecer en una lista debajo de los campos de entrada. Si no lo ve, puede usar el cuadro **Seleccionar** para filtrar los resultados por el nombre de la aplicación.

1. Haga clic en la aplicación. Debe colocarse en la sección **Miembros seleccionados**. Haga clic en **Seleccionar**.

1. En la pantalla **Agregar asignación de roles**, haga clic en **Revisar y asignar**. Revise la configuración y haga clic en **Revisar y asignar**.

Ha concedido a la aplicación de funciones acceso al espacio de nombres de Service Bus mediante identidades administradas.

## <a name="connect-to-service-bus-in-your-function-app"></a>Conectar a Service Bus en la aplicación de funciones

1. En el portal, busque la aplicación de funciones que creó en el [tutorial anterior] o vaya a ella en la página **Aplicación de funciones**.

1. En **Configuración** de la aplicación de funciones, seleccione **Configuración**.

1. En **Configuración de la aplicación**, seleccione **+ Configuración de la aplicación nueva**, para crear los valores nuevos en la tabla siguiente.

    | Nombre      | Value  | Descripción |
    | ------------ | ---------------- | ----------- |
    | **ServiceBusConnection__fullyQualifiedNamespace** | <SERVICE_BUS_NAMESPACE>.servicebus.windows.net | Esta configuración conecta la aplicación de funciones al Service Bus y usa conexiones basadas en identidades en lugar de secretos. |

1. Después de crear los dos valores, seleccione **Guardar** > **Confirmar**.

Ahora que ha preparado la aplicación de funciones para conectarse al espacio de nombres de Service Bus mediante una identidad administrada, puede agregar una nueva función que use un desencadenador de Service Bus al proyecto local.

## <a name="add-a-service-bus-triggered-function"></a>Agregar una función de desencadenador de Service Bus

1. Ejecute el comando `func init`, de la manera siguiente, para crear un proyecto de funciones en una carpeta llamada LocalFunctionProj con el entorno de ejecución especificado:

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. Vaya a la carpeta del proyecto:

    ```console
    cd LocalFunctionProj
    ```

1. En la carpeta del proyecto raíz, ejecute los siguientes comandos:

    ```command
    dotnet remove package Microsoft.Azure.Webjobs.Extensions.ServiceBus
    dotnet add package Microsoft.Azure.Webjobs.Extensions.ServiceBus --prerelease
    ```

    Esto reemplaza la versión predeterminada del paquete de extensión de Service Bus por una versión que admite identidades administradas.

1. Ejecute el siguiente comando para agregar una función de desencadenador de Service Bus al proyecto:

    ```csharp
    func new --name ServiceBusTrigger --template ServiceBusQueueTrigger 
    ```

    Esto agrega el código para un nuevo desencadenador de Service Bus y una referencia al paquete de extensión. Debe agregar una configuración de conexión de espacio de nombres de Service Bus para este desencadenador.

1. Abra el nuevo archivo del proyecto ServiceBusTrigger.cs y reemplace la clase `ServiceBusTrigger` por el código siguiente:

    ```csharp
    public static class ServiceBusTrigger
    {
        [FunctionName("ServiceBusTrigger")]
        public static void Run([ServiceBusTrigger("myinputqueue", 
            Connection = "ServiceBusConnection")]string myQueueItem, ILogger log)
        {
            log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
        }
    }
    ```

    Este ejemplo de código actualiza el nombre de la cola a `myinputqueue`, que es el mismo nombre de la cola que creó anteriormente. También establece el nombre de la conexión de Service Bus en `ServiceBusConnection`. Este es el espacio de nombres de Service Bus utilizado por la conexión basada en identidades `ServiceBusConnection__fullyQualifiedNamespace` que configuró en el portal.

> [!NOTE]  
> Si intenta ejecutar las funciones ahora mediante `func start`, obtendrá un error. Esto se debe a que no tiene una conexión basada en identidades definida localmente. Si quiere ejecutar la función localmente, establezca la configuración de la aplicación `ServiceBusConnection__fullyQualifiedNamespace` en `local.settings.json` como hizo en [la sección anterior](#connect-to-service-bus-in-your-function-app). Además, deberá asignar el rol a la identidad del desarrollador. Para más información, consulte la [documentación de desarrollo local con conexiones basadas en identidades](./functions-reference.md#local-development-with-identity-based-connections).

## <a name="publish-the-updated-project"></a>Publique el proyecto actualizado

1. Ejecute el siguiente comando para generar localmente los archivos necesarios para el paquete de implementación:

    ```console
    dotnet publish --configuration Release
    ```

1. Vaya a la subcarpeta `\bin\Release\netcoreapp3.1\publish` y cree un archivo ZIP a partir de su contenido.

1. Publique el archivo ZIP mediante la ejecución del comando siguiente, reemplazando los parámetros `FUNCTION_APP_NAME`, `RESOURCE_GROUP_NAME`, y `PATH_TO_ZIP` según corresponda:

    ```azurecli
    az functionapp deploy -n FUNCTION_APP_NAME -g RESOURCE_GROUP_NAME --src-path PATH_TO_ZIP
    ```

Ahora que ha actualizado la aplicación de funciones con el nuevo desencadenador, puede comprobar que funciona con la identidad.

## <a name="validate-your-changes"></a>Validación de los cambios

1. En el portal, busque `Application Insights` y seleccione **Application Insights** en **Usuarios**.  

1. En **Application Insights**, busque la instancia con nombre.

1. En la instancia, seleccione **Métricas en directo** en **Investigar**.

1. Mantenga abierta la pestaña anterior y abra Azure Portal en una nueva pestaña. En la nueva pestaña, vaya al espacio de nombres de Service Bus y seleccione **Colas** en la hoja izquierda.

1. Seleccione la cola llamada `myinputqueue`.

1. Seleccione **Service Bus Explorer** en la hoja izquierda.

1. Enviar un mensaje de prueba.

1. Seleccione la pestaña **Métricas en directo** abierta y vea la ejecución de la cola de Service Bus.

Felicidades. Ha configurado correctamente el desencadenador de cola de Service Bus con una identidad administrada.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado una aplicación de funciones con conexiones basadas en identidades.

Use los vínculos siguientes para obtener más información sobre conexiones basadas en identidades de Azure Functions:

- [Identidad administrada en Azure Functions](../app-service/overview-managed-identity.md)
- [Conexiones basadas en identidades en Azure Functions](./functions-reference.md#configure-an-identity-based-connection)
- [Documentación de Functions para el desarrollo local](./functions-reference.md#local-development-with-identity-based-connections)

[tutorial anterior]: ./functions-identity-based-connections-tutorial.md
