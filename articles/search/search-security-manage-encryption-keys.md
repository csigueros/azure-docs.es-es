---
title: Cifrado de datos mediante claves administradas por el cliente
titleSuffix: Azure Cognitive Search
description: Complemente el cifrado del lado servidor con índices y mapas de sinónimos de Azure Cognitive Search usando claves que se crean y administran en Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 5513e4f26f6b2fed17f406f43110eb358179ce79
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113232833"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Configuración de claves administradas por el cliente para el cifrado de datos en Azure Cognitive Search

Azure Cognitive Search cifra automáticamente el contenido con [claves administradas por el servicio](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Si se necesita más protección, puede complementar el cifrado predeterminado con un nivel de cifrado adicional con las claves que se crean y administran en Azure Key Vault. Los objetos que se pueden cifrar incluyen índices, listas de sinónimos, indizadores, orígenes de datos y conjuntos de aptitudes.

Este artículo le guía por los pasos necesarios para configurar el cifrado con clave administrada por el cliente. Estos son algunos aspectos que debe tener en cuenta:

+ El cifrado con clave administrada por el cliente depende de [Azure Key Vault](../key-vault/general/overview.md). Puede crear sus propias claves de cifrado y almacenarlas en un almacén de claves, o puede usar las API de Azure Key Vault para generar las claves de cifrado.

+ El cifrado con claves administradas por el cliente se habilita cuando se crean objetos, para cada objeto. No se puede cifrar el contenido que ya existe.

El cifrado es intensivo a nivel computacional de descifrar, por lo que solo se cifra el contenido confidencial. Esto incluye todo el contenido dentro de índices y listas de sinónimos. Para indizadores, orígenes de datos y conjuntos de aptitudes, solo se cifran los campos en los que se almacenan cadenas de conexión, descripciones, claves y entradas de usuario. Por ejemplo, los conjuntos de aptitudes tienen claves de Cognitive Services y algunas aptitudes aceptan entradas de usuario, como entidades personalizadas. Las claves y las entradas de usuario en las aptitudes se cifran.

## <a name="double-encryption"></a>Doble cifrado

El cifrado doble es una extensión del cifrado de claves administradas por el cliente (CMK). El cifrado de CMK se aplica al almacenamiento a largo plazo que se escribe en un disco de datos. El término *cifrado doble* hace referencia al cifrado adicional del almacenamiento a corto plazo (del contenido escrito en discos temporales). No es necesario realizar ninguna configuración. Cuando se aplica CMK a objetos, se invoca automáticamente el cifrado doble.

Aunque el cifrado doble está disponible en todas las regiones, la compatibilidad se ha implantado en dos fases. La primera implementación se realizó en agosto de 2020 e incluyó las cinco regiones enumeradas a continuación. La segunda implementación de mayo de 2021 extendió el cifrado doble a todas las regiones restantes. Si usa CMK en un servicio anterior y desea un cifrado doble, deberá crear un nuevo servicio de búsqueda en la región que prefiera.

| Region | Fecha de creación del servicio |
|--------|-----------------------|
| Oeste de EE. UU. 2 | Después del 1 de agosto de 2020 |
| Este de EE. UU. | Después del 1 de agosto de 2020 |
| Centro-sur de EE. UU.  | Después del 1 de agosto de 2020 |
| US Gov - Virginia  | Después del 1 de agosto de 2020 |
| US Gov: Arizona  | Después del 1 de agosto de 2020 |
| [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=search#select-product) | Después del 13 de mayo de 2021 |

## <a name="prerequisites"></a>Requisitos previos

En este escenario se usan los servicios y las herramientas siguientes.

+ [Azure Cognitive Search](search-create-service-portal.md) en un [nivel de servicio facturable](search-sku-tier.md#tier-descriptions) (básico o superior, en cualquier región).
+ [Azure Key Vault](../key-vault/general/overview.md), puede crear un almacén de claves mediante [Azure Portal](../key-vault//general/quick-create-portal.md), la [CLI de Azure](../key-vault//general/quick-create-cli.md) o [Azure PowerShell](../key-vault//general/quick-create-powershell.md). en la misma suscripción que Azure Cognitive Search. El almacén de claves debe tener la **eliminación temporal** y la **protección contra purgas** habilitada.
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Si no tiene una, [configure un nuevo inquilino](../active-directory/develop/quickstart-create-new-tenant.md).

Debe tener una aplicación de búsqueda que pueda crear el objeto cifrado. En este código, hará referencia a una clave del almacén de claves y a la información de registro de Active Directory. Este código puede ser una aplicación de trabajo o un código de prototipo como el [ejemplo de código de C# DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK).

> [!TIP]
> Puede usar [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) o [Azure PowerShell](search-get-started-powershell.md), para llamar a las API REST que crean índices y asignaciones de sinónimos que incluyen un parámetro de clave de cifrado. También puede usar SDK de Azure. En este momento, no se admite el portal para la adición de una clave a índices o mapas de sinónimos.

## <a name="key-vault-tips"></a>Sugerencias de Key Vault

Si no tiene experiencia con Azure Key Vault, revise este inicio rápido para obtener información sobre las tareas básicas: [Establecimiento y recuperación de un secreto de Azure Key Vault mediante PowerShell](../key-vault/secrets/quick-create-powershell.md). Estas son algunas sugerencias para usar Key Vault:

+ Use tantos almacenes de claves como necesite. Las claves administradas pueden estar en almacenes de claves diferentes. Un servicio de búsqueda puede tener varios objetos cifrados, cada uno con claves de cifrado administradas por el cliente distintas, en almacenes de claves diferentes.

+ [Habilite el registro](../key-vault/general/logging.md) en Key Vault para poder supervisar la utilización de las claves.

+ Asegúrese de seguir procedimientos estrictos durante el giro rutinario de las claves del almacén de claves y de los secretos y el registro de aplicaciones de Active Directory. Actualice siempre todo el [contenido cifrado](search-security-get-encryption-keys.md) para usar nuevos secretos y claves antes de eliminar los antiguos. Si omite este paso, el contenido no se podrá descifrar.

## <a name="1---enable-purge-protection"></a>1 - Habilitación de la protección de purga

Como primer paso, asegúrese de que la [eliminación temporal](../key-vault/general/soft-delete-overview.md) y la [protección de purga](../key-vault/general/soft-delete-overview.md#purge-protection) están habilitadas en el almacén de claves. Debido a la naturaleza del cifrado con claves administradas por el cliente, ninguna podrá recuperar sus datos si se elimina la clave de Azure Key Vault. 

Para evitar la pérdida de datos causada por las eliminaciones accidentales de claves de Key Vault, debe habilitar las opciones de eliminación temporal y de protección de purgas en el almacén de claves. La eliminación temporal está habilitada de forma predeterminada, por lo que solo tendrá problemas si la deshabilitó intencionadamente. La protección de purga no está habilitada de forma predeterminada, pero es necesaria para el cifrado con clave administrada por el cliente de Cognitive Search. 

Puede establecer ambas propiedades mediante el portal, PowerShell o los comandos de la CLI de Azure.

### <a name="using-azure-portal"></a>Uso de Azure Portal

1. [Inicie sesión en Azure Portal](https://portal.azure.com) y abra la página de información general del almacén de claves.

1. En la página **Información general**, en **Información esencial**, habilite **Eliminación temporal** y **Protección de purga**.

### <a name="using-powershell"></a>Usar PowerShell

1. Ejecute `Connect-AzAccount` para configurar las credenciales de Azure.

1. Ejecute el siguiente comando para conectarse al almacén de claves. Para hacerlo, reemplace `<vault_name>` por un nombre válido:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault se crea con la eliminación temporal habilitada. Si está deshabilitada en el almacén, ejecute el siguiente comando:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Habilitación de la protección de purga:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Guarde las actualizaciones:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Uso de la CLI de Azure

+ Si tiene una [instalación de la CLI de Azure](/cli/azure/install-azure-cli), puede ejecutar el siguiente comando para habilitar las propiedades necesarias.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2\. Creación de una clave en Key Vault

Omita la generación de claves si ya tiene una clave en Azure Key Vault que quiere usar, pero recopile el identificador de clave. Necesitará esta información al crear un objeto cifrado.

1. [Inicie sesión en Azure Portal](https://portal.azure.com) y abra la página de información general del almacén de claves.

1. Seleccione **Claves** a la izquierda y, luego, elija **+ Generate/Import** (+ Generar/Importar).

1. En el panel **Crear una clave**, forme la lista de **Opciones** y elija el método que quiera usar para crear una clave. También puede **generar** una nueva clave, **cargar** una clave existente, o usar **Restaurar copia de seguridad** para seleccionar una copia de seguridad de una clave.

1. Especifique un **nombre** para la clave y, opcionalmente, seleccione otras propiedades clave.

1. Seleccione **Crear** para iniciar la implementación.

1. Tome nota del identificador de la clave: se compone del **Uri del valor de clave**, el **nombre de clave** y la **versión de clave**. Necesitará el identificador para definir un índice cifrado en Azure Cognitive Search.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Crear una clave del almacén de claves":::

## <a name="3---register-an-app"></a>3 - Registro de una aplicación

1. En [Azure Portal](https://portal.azure.com), busque el recurso de Azure Active Directory de su suscripción.

1. A la izquierda, en **Administrar**, seleccione **Registros de aplicaciones** y, luego, elija **Nuevo registro**.

1. Asigne un nombre al registro; podría ser uno similar al de la aplicación de búsqueda. Seleccione **Registrar**.

1. Una vez creado el registro de aplicaciones, copie el identificador de la aplicación. Tendrá que proporcionar esta cadena a la aplicación. 

   Si va a recorrer paso a paso [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK), pegue este valor en el archivo **appsettings.json**.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Identificador de la aplicación en la sección Información esencial":::

1. Seleccione **Certificados y secretos** a la izquierda.

1. Seleccione **Nuevo secreto de cliente**. Asigne al secreto un nombre para mostrar y seleccione **Agregar**.

1. Copie el secreto de la aplicación. Si va a recorrer el ejemplo paso a paso, pegue este valor en el archivo **appsettings.json**.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Secreto de aplicación":::

## <a name="4---grant-permissions"></a>4 - Concesión de permisos

En este paso, creará una directiva de acceso en Key Vault. Esta directiva proporciona a la aplicación que registró en Active Directory permiso para usar la clave administrada por el cliente.

Los permisos de acceso se pueden revocar en cualquier momento. Una vez revocados, cualquier índice o mapa de sinónimos de servicio de búsqueda que utilice ese almacén de claves quedará inutilizable. La restauración de los permisos de acceso al almacén de claves en un momento posterior restaurará el acceso al índice o mapa de sinónimos. Para más información, consulte [Protección del acceso a un almacén de claves](../key-vault/general/security-features.md).

1. Todavía en Azure Portal, abra la página **Información general** del almacén de claves. 

1. Seleccione **Directivas de acceso** a la izquierda y luego **Agregar directiva de acceso**.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Agregar una nueva directiva de acceso del almacén de claves":::

1. Elija **Seleccionar la entidad de seguridad** y elija la aplicación que registró en Active Directory. Puede buscarla por el nombre.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Seleccionar la entidad de seguridad de la directiva de acceso del almacén de claves":::

1. En **Permisos clave**, elija *Obtener*, *Desencapsular clave* y *Encapsular clave*.

1. En **Permisos de los secretos**, seleccione *Obtener*.

1. En **Permisos de los certificados**, seleccione *Obtener*.

1. Seleccione **Agregar** y, después, **Guardar**.

> [!Important]
> El contenido cifrado de Azure Cognitive Search está configurado para usar una clave específica de Azure Key Vault con una **versión** concreta. Si cambia la clave o versión, debe actualizarse el índice o el mapa de sinónimos para utilizar la nueva versión de clave **antes de** eliminar la versión de clave anterior. Si no lo hace, el índice o el mapa de sinónimos quedará inutilizable, ya que no podrá descifrar el contenido una vez que se pierda la clave de acceso.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5\. Cifrado del contenido

Para agregar una clave administrada por el cliente en un índice, un mapa de sinónimos, un indizador, un origen de datos o un conjunto de aptitudes, use la [API REST Search](/rest/api/searchservice/) o un SDK de Azure para crear un objeto que tenga habilitado el cifrado. El portal no expone las asignaciones de sinónimos ni las propiedades de cifrado. 

1. Llame a las API Create para especificar la propiedad **encryptionKey**:

   + [Crear índice](/rest/api/searchservice/create-index)
   + [Crear mapa de sinónimos](/rest/api/searchservice/create-synonym-map)
   + [Crear indexador](/rest/api/searchservice/create-indexer)
   + [Creación de un origen de datos](/rest/api/searchservice/create-data-source) 
   + [Crear un conjunto de aptitudes](/rest/api/searchservice/create-skillset).

1. Inserte la construcción encryptionKey en la definición del objeto. Es una propiedad de primer nivel, el mismo que el del nombre y la descripción. En los [ejemplos siguientes](#rest-examples) se muestra la selección de ubicación de las propiedades. Si usa el mismo almacén de claves, clave y versión, puede pegar la misma construcción encryptionKey en cada objeto para el que habilite el cifrado.

   En el ejemplo de JSON siguiente se muestra encryptionKey, con valores de marcador de posición para Azure Key Vault y el registro de aplicaciones en Azure Active Directory:

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
        "accessCredentials": {
          "applicationId": "00000000-0000-0000-0000-000000000000",
          "applicationSecret": "myApplicationSecret"
        }
      }
    }
    ```

Una vez que cree el objeto cifrado en el servicio de búsqueda, puede usarlo como haría con cualquier otro objeto de su tipo. El cifrado es transparente para el usuario y el desarrollador.

> [!Note]
> Ninguno de estos detalles del almacén de claves se considera secreto y se pueden recuperar fácilmente yendo a la página de claves relevante de Azure Key Vault en Azure Portal.

## <a name="rest-examples"></a>Ejemplos de REST

En esta sección se muestra el código JSON de varios objetos para que pueda ver dónde buscar `encryptionKey` en la definición de un objeto.

### <a name="index-encryption"></a>Cifrado del índice

Los detalles de la creación de un índice a través de la API REST se pueden encontrar en [Creación de un índice (API REST)](/rest/api/searchservice/create-index), donde la única diferencia aquí es especificar los detalles de la clave de cifrado como parte de la definición del índice:

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true}
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Ahora puede enviar la solicitud de creación del índice y, a continuación, empiece a usar el índice con normalidad.

### <a name="synonym-map-encryption"></a>Cifrado del mapa de sinónimos

Cree una asignación de sinónimos cifrada con el procedimiento de [Creación de una asignación de sinónimos (API REST de Azure Cognitive Search)](/rest/api/searchservice/create-synonym-map). Use la propiedad `encryptionKey` para especificar la clave de cifrado que se va a usar.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Ahora puede enviar la solicitud de creación del mapa de sinónimos y, a continuación, empiece a usarlo con normalidad.

### <a name="data-source-encryption"></a>Cifrado de origen de datos

Cree un origen de datos cifrado mediante la [API REST Create Data Source](/rest/api/searchservice/create-data-source) (Crear origen de datos). Use la propiedad `encryptionKey` para especificar la clave de cifrado que se va a usar.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Ahora puede enviar la solicitud de creación del origen de datos y luego empezar a usarlo con normalidad.

### <a name="skillset-encryption"></a>Cifrado de conjunto de aptitudes

Cree un conjunto de aptitudes cifrado mediante la [API REST Create Skillset](/rest/api/searchservice/create-skillset) (Crear conjunto de aptitudes). Use la propiedad `encryptionKey` para especificar la clave de cifrado que se va a usar.

```json
{
    "name": "skillset1",
    "skills":  [ omitted for brevity ],
    "cognitiveServices": { omitted for brevity },
      "knowledgeStore":  { omitted for brevity  },
    "encryptionKey": (optional) { 
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "accessCredentials": {
            "applicationId": "00000000-0000-0000-0000-000000000000",
            "applicationSecret": "myApplicationSecret"}
    }
}
```

Ahora puede enviar la solicitud de creación del conjunto de aptitudes y luego empezar a usarlo con normalidad.

### <a name="indexer-encryption"></a>Cifrado de indizador

Cree un indizador cifrado mediante la [API REST Create Indexer](/rest/api/searchservice/create-indexer) (Crear indizador). Use la propiedad `encryptionKey` para especificar la clave de cifrado que se va a usar.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Ahora puede enviar la solicitud de creación del indizador y luego empezar a usarlo con normalidad.

>[!Important]
> Aunque `encryptionKey` no se puede agregar a asignaciones de índices de búsqueda o sinónimos existentes, se puede actualizar proporcionando diferentes valores para cualquiera de los tres detalles del almacén de claves (por ejemplo, mediante la actualización de la versión de la clave). Al cambiar a una nueva clave o versión de clave de Key Vault, cualquier asignación de índices de búsqueda o sinónimos que utilice la clave debe actualizarse primero para usar la nueva clave o versión **antes** de eliminar la clave o versión anteriores. Si no lo hace, el índice o el mapa de sinónimos quedarán inutilizables, ya que no podrá descifrar el contenido una vez que se pierda el acceso a la clave. Si bien, al restaurar los permisos de acceso a Key Vault en un momento posterior se restaurará el acceso al contenido.

## <a name="simpler-alternative-trusted-service"></a>Alternativa más sencilla: Servicio de confianza

En función de los requisitos de configuración y autenticación de inquilinos, es posible que pueda implementar un enfoque más sencillo para acceder a una clave del almacén de claves. En lugar de crear y usar una aplicación de Active Directory, puede convertir un servicio de búsqueda en un servicio de confianza si habilita para él una identidad administrada por el sistema, o bien asigna una identidad administrada por el usuario al servicio de búsqueda. Después, tendría que usar el servicio de búsqueda de confianza o la identidad administrada asignada por el usuario como entidad de seguridad, en lugar de una aplicación registrada en AD, para acceder a la clave del almacén de claves.

Estos dos enfoques permiten omitir los pasos para el registro de aplicaciones y los secretos de aplicación, y simplifican la definición de la clave de cifrado.

### <a name="system-assigned-managed-identity"></a>Identidad administrada asignada por el sistema

En general, una identidad administrada permite que el servicio de búsqueda se autentique en Azure Key Vault sin almacenar las credenciales (ApplicationID o ApplicationSecret) en código. El ciclo de vida de este tipo de identidad administrada está ligado al ciclo de vida del servicio de búsqueda, que solo puede tener una identidad administrada. Para más información sobre cómo funcionan las identidades administradas, consulte [Qué son las identidades administradas de recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md).

1. Convierta el servicio de búsqueda en un servicio de confianza.
   ![Activar la identidad administrada asignada por el sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Activar la identidad administrada asignada por el sistema")

1. Al configurar una directiva de acceso en Azure Key Vault, elija el servicio de búsqueda de confianza como entidad de seguridad (en lugar de la aplicación registrada en AD). Asigne los mismos permisos (varios OBTENER, ENCAPSULAR, DESEMCAPSULAR), tal y como se indica en el paso para conceder permisos de clave de acceso.

1. Use una construcción simplificada de `encryptionKey` que omita las propiedades de Active Directory.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

Entre las condiciones que impedirán adoptar este enfoque simplificado se incluyen las siguientes:

+ No puede conceder directamente a su servicio de búsqueda permisos de acceso al almacén de claves (por ejemplo, si el servicio de búsqueda se encuentra en un inquilino de Active Directory diferente al de Azure Key Vault).

+ Se requiere un único servicio de búsqueda para hospedar varias asignaciones de sinónimos e índices cifrados, cada una de las cuales utiliza una clave diferente de un almacén de claves diferente, en el que cada almacén de claves debe utilizar una **identidad diferente** para la autenticación. Como un servicio de búsqueda solo puede tener una identidad administrada, un requisito de varias identidades descalificará el enfoque simplificado para el escenario.  

### <a name="user-assigned-managed-identity-preview"></a>Identidad administrada asignada por el usuario (versión preliminar)

> [!IMPORTANT] 
> La compatibilidad con identidades administradas asignadas por el usuario está en versión preliminar pública en los [términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> Las versión 2021-04-30-Preview de la de API REST y [Management REST API 2021-04-01-Preview](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) proporcionan esta característica.

La asignación de una identidad administrada asignada por el usuario al servicio de búsqueda permitirá que el servicio de búsqueda se autentique en Azure Key Vault sin almacenar las credenciales (ApplicationID o ApplicationSecret) en código. El ciclo de vida de este tipo de identidad administrada es independiente al del servicio de búsqueda. Para más información sobre cómo funcionan las identidades administradas, consulte [Qué son las identidades administradas de recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md).

1. Si todavía no tiene una identidad administrada asignada por el usuario creada, tendrá que crear una. Para crear una identidad administrada asignada por el usuario, siga estos pasos:

    1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
    1. Seleccione **+ Crear un nuevo recurso**.
    1. En la barra de búsqueda "Servicios de búsqueda y Marketplace", busque "Identidad administrada asignada por el usuario" y, después, seleccione **Crear**.
    1. Asigne un nombre descriptivo a la identidad.

1. A continuación, asigne la identidad administrada asignada por el usuario al servicio de búsqueda. Esto se puede hacer mediante la API de administración [2021-04-01-preview](/rest/api/searchmanagement/management-api-versions).

    La propiedad identity toma un tipo y una o varias identidades asignadas por el usuario completas:
    
    * **type** es el tipo de identidad utilizado para el recurso. El tipo "SystemAssigned, UserAssigned" incluye una identidad creada por el sistema y un conjunto de identidades asignadas por el usuario. El tipo "None" quitará todas las identidades del servicio.
    * **userAssignedIdentities** incluye los detalles de la identidad administrada asignada por el usuario.
        * Formato de la identidad administrada asignada por el usuario: 
            * /subscriptions/**id_de_la_suscripción**/resourcegroups/**nombre_del_grupo_de_recursos**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**nombre_de_la_identidad_administrada**
    
    Ejemplo de cómo asignar una identidad administrada asignada por el usuario a un servicio de búsqueda:
    
    ```http
    PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Search/searchServices/[search service name]?api-version=2021-04-01-preview
    Content-Type: application/json

    {
      "location": "[region]",
      "sku": {
        "name": "[sku]"
      },
      "properties": {
        "replicaCount": [replica count],
        "partitionCount": [partition count],
        "hostingMode": "default"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]": {}
        }
      }
    } 
    ```

1. Al configurar una directiva de acceso en Azure Key Vault, elija la identidad administrada asignada por el usuario como principio (en lugar de la aplicación registrada en AD). Asigne los mismos permisos (varios OBTENER, ENCAPSULAR, DESEMCAPSULAR), tal y como se indica en el paso para conceder permisos de clave de acceso.

1. Use una construcción simplificada de `encryptionKey` que omita las propiedades de Active Directory y agregue una propiedad de identidad. Asegúrese de usar la versión 2021-04-30-preview de la API REST.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://[key vault name].vault.azure.net",
        "keyVaultKeyName": "[key vault key name]",
        "keyVaultKeyVersion": "[key vault key version]",
        "identity" : { 
            "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
            "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]"
        }
      }
    }
    ```

## <a name="work-with-encrypted-content"></a>Trabajo con contenido cifrado

Con el cifrado de clave administrada por el cliente, observará una latencia para la indexación y las consultas debido al trabajo de cifrado o descifrado adicional. Azure Cognitive Search no registra la actividad de cifrado, pero puede supervisar el acceso a la clave mediante el registro del almacén de claves. Se recomienda [habilitar el registro](../key-vault/general/logging.md) como parte de la configuración del almacén de claves.

Se espera que se produzca una rotación de claves con el tiempo. Cuando se realice la rotación de claves, es importante seguir esta secuencia:

1. [Determine la clave que usa un índice o asignación de sinónimos](search-security-get-encryption-keys.md).
1. [Cree una nueva clave en el almacén de claves](../key-vault/keys/quick-create-portal.md), pero deje la clave original disponible.
1. [Actualice las propiedades de encryptionKey](/rest/api/searchservice/update-index) en una asignación de sinónimo o índice para usar los nuevos valores. Solo se pueden actualizar los objetos que se crearon originalmente con esta propiedad para usar un valor diferente.
1. Deshabilite o elimine la clave anterior en el almacén de claves. Supervise el acceso a la clave para comprobar que se está usando la nueva clave.

Por motivos de rendimiento, el servicio de búsqueda almacena la clave en la memoria caché durante varias horas. Si deshabilita o elimina la clave sin proporcionar una nueva, las consultas seguirán funcionando de manera temporal hasta que expire la memoria caché. Sin embargo, una vez que el servicio de búsqueda no pueda descifrar el contenido, recibirá este mensaje: “Acceso prohibido. Puede que la clave de consulta se haya revocado. Vuelva a intentarlo.” 

## <a name="next-steps"></a>Pasos siguientes

Si no está familiarizado con la arquitectura de seguridad de Azure, revise la [documentación sobre seguridad de Azure](../security/index.yml), y en particular, este artículo:

> [!div class="nextstepaction"]
> [Cifrado en reposo de datos](../security/fundamentals/encryption-atrest.md)
