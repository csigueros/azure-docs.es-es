---
title: Uso de Key Vault para almacenar claves de Azure Cosmos DB y acceder a ellas
description: Use Azure Key Vault para almacenar puntos de conexión, claves y cadenas de conexión de Azure Cosmos DB y acceder a ellos.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 6d09fb5f728ec430991f487b8b4acba937f7262c
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113355821"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Protección de las claves de Azure Cosmos con Azure Key Vault 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

>[!IMPORTANT]
> La solución recomendada para acceder a las claves de Azure Cosmos DB es usar una [identidad administrada asignada por el sistema](managed-identity-based-authentication.md). Si el servicio no puede aprovechar las identidades administradas, use la [solución basada en certificados](certificate-based-authentication.md). Si la solución de identidad administrada y la solución basada en certificados no satisfacen sus necesidades, use la solución de Key Vault que aparece a continuación.

Cuando se usa Azure Cosmos DB para las aplicaciones, puede acceder a las bases de datos, las colecciones y los documentos mediante el punto de conexión y la clave en el archivo de configuración de la aplicación.  Sin embargo, no es seguro colocar las claves y la dirección URL directamente en el código de la aplicación porque están disponibles en formato de texto no cifrado para todos los usuarios. Debe asegurarse de que el punto de conexión y las claves están disponibles, pero mediante un mecanismo seguro. En este caso, Azure Key Vault puede ayudarle a almacenar y administrar de forma segura secretos de aplicación.

Los pasos siguientes son necesarios para almacenar y leer las claves de acceso de Azure Cosmos DB desde Key Vault:

* Creación de un almacén de claves  
* Adición de claves de acceso de Azure Cosmos DB en Key Vault  
* Creación de una aplicación web de Azure  
* Registro de la aplicación y concesión de permisos para leer el almacén de claves  


## <a name="create-a-key-vault"></a>Creación de un almacén de claves

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).  
2. Seleccione **Crear un recurso > Seguridad > Key Vault**.  
3. En la sección **Crear Key Vault**, proporcione la siguiente información:  
   * **Nombre:** proporcione un nombre único para el almacén de claves.  
   * **Subscription** (Suscripción): elija la suscripción que va a utilizar.  
   * En **Grupo de recursos** elija **Crear nuevo** y escriba un nombre para el grupo de recursos.  
   * En el menú desplegable Ubicación elija una ubicación.  
   * Deje las restantes opciones con sus valores predeterminados.  
4. Después de proporcionar la información descrita anteriormente, seleccione **Crear**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Agregue las claves de acceso de Azure Cosmos DB en Key Vault.
1. Vaya al almacén de claves que creó en el paso anterior y abra la pestaña **Secretos**.  
2. Seleccione **Generar o importar**. 

   * Seleccione **Manual** para **Opciones de carga**.
   * Proporcione un **nombre** para el secreto.
   * Especifique la cadena de conexión de la cuenta de Cosmos DB en el campo **Valor**. Después, seleccione **Crear**.

   :::image type="content" source="./media/access-secrets-from-keyvault/create-a-secret.png" alt-text="Crear un secreto":::

4. Una vez creado el secreto, ábralo y copie el **Identificador secreto que se encuentra en el siguiente formato. Usará este identificador en la siguiente sección. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Creación de una aplicación web de Azure

1. Cree una aplicación web de Azure o puede descargar la aplicación del [repositorio de GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/Demo/keyvaultdemo). Es una sencilla aplicación de MVC.  

2. Descomprima la aplicación descargada y abra el archivo **HomeController.cs**. Actualice el identificador secreto en la siguiente línea:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Guarde** el archivo y **compile** la solución.  
4. Luego implemente la aplicación en Azure. Haga clic con el botón derecho en el proyecto y elija **Publicar**. Cree un perfil de servicio de aplicación (puede asignar el nombre WebAppKeyVault1 a la aplicación) y seleccione **Publicar**.   

5. Una vez implementada la aplicación: En Azure Portal, vaya a la aplicación web implementada y active la opción **Managed Service Identity** de esta aplicación.  

   :::image type="content" source="./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png" alt-text="Identidad de servicio administrado":::

Si va a ejecutar la aplicación ahora, verá el siguiente error, ya que no tienen ningún permiso para esta aplicación en Key Vault.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-without-access.png" alt-text="Aplicación implementada sin acceso":::

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Registro de la aplicación y concesión de permisos para leer el almacén de claves

En esta sección, registre la aplicación en Azure Active Directory y conceda permisos para que la aplicación lea el almacén de claves. 

1. Vaya a Azure Portal y abra el **almacén de claves** creado en la sección anterior.  

2. Abra **Directivas de acceso**, seleccione **+ Agregar nuevo**, busque la aplicación web que implementó, seleccione los permisos y seleccione **Aceptar**.  

   :::image type="content" source="./media/access-secrets-from-keyvault/add-access-policy.png" alt-text="Agregar directiva de acceso":::

Ahora, si ejecuta la aplicación, puede leer el secreto del almacén de claves.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-with-access.png" alt-text="Aplicación implementada con secreto":::
 
De forma similar, puede agregar un usuario para acceder al almacén de claves. Debe agregarse al almacén de claves; para ello, seleccione **Directivas de acceso** y después conceda todos los permisos que necesita para ejecutar la aplicación desde Visual Studio. Cuando se ejecuta esta aplicación desde el escritorio, usa su identidad.

## <a name="next-steps"></a>Pasos siguientes

* Para configurar un firewall para Azure Cosmos DB, vea el artículo sobre la [compatibilidad con un firewall](how-to-configure-firewall.md).
* Para configurar el punto de conexión de servicio de red virtual, vea el artículo [Protección del acceso a una cuenta de Azure Cosmos DB con el uso de un punto de conexión de servicio de red virtual de Azure](how-to-configure-vnet-service-endpoint.md).
