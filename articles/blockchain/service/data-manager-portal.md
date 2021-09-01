---
title: 'Configuración de Blockchain Data Manager con Azure Portal: Azure Blockchain Service'
description: Cree y administre una instancia de Blockchain Data Manager para Azure Blockchain Service mediante Azure Portal.
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: b80bbc1b302579f0145e7b5cb6c5e3e0561d2ef7
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2021
ms.locfileid: "122652619"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Configuración de cadena de bloques Data Manager con Azure Portal

Configure la cadena de bloques Data Manager para que la cadena de bloques de Azure Blockchain Service capture los datos de la cadena de bloques y los envíe a un tema Azure Event Grid.

[!INCLUDE [Retirement note](./includes/retirement.md)]

Para configurar una instancia de cadena de bloques Data Manager, puede:

* Crear una instancia de cadena de bloques Data Manager para un nodo de transacción de la cadena de bloques de Azure Blockchain Service
* Agregue sus aplicaciones de cadena de bloques

## <a name="prerequisites"></a>Prerrequisitos

* Realizar el tutorial [Quickstart: Creación de un miembro de cadena de bloques mediante Azure Portal](create-member.md) o [Inicio rápido: Creación de un miembro de cadena de bloques de Azure Blockchain Service mediante la CLI de Azure](create-member-cli.md). Se recomienda el nivel *Estándar* de Azure Blockchain Service cuando se usa Blockchain Data Manager.
* Crear un [tema de Event Grid](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Más información sobre [Controladores de eventos en Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Crear instancia

Una instancia de Blockchain Data Manager conecta y supervisa un nodo de transacción de Azure Blockchain Service. Solo los usuarios con acceso al nodo de transacción pueden crear una conexión. Una instancia captura todos los datos de transacciones y bloques sin procesar del nodo de transacción. Blockchain Data Manager publica un mensaje **RawBlockAndTransactionMsg**, que es un superconjunto de la información devuelta por las consultas web3.eth [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) y [getTransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction).

Una conexión saliente envía datos de la cadena de bloques a Azure Event Grid. Configura una conexión saliente única cuando crea la instancia. La cadena de bloques Data Manager admite varias conexiones salientes de tema Event Grid para cualquier instancia de cadena de bloques Data Manager determinada. Puede enviar datos de la cadena de bloques a un único destino o enviar datos de la cadena de bloques a varios destinos. Para agregar otro destino, solo tiene que agregar conexiones salientes adicionales a la instancia.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Vaya al miembro de la cadena de bloques de Azure Blockchain Service que quiere conectar con la cadena de bloques Data Manager. Seleccione **Blockchain Data Manager**.
1. Seleccione **Agregar**.

    ![Agregar Blockchain Data Manager](./media/data-manager-portal/add-instance.png)

    Escriba la siguiente información:

    Configuración | Descripción
    --------|------------
    Nombre | Escriba un nombre único para una instancia conectada de Blockchain Data Manager. El nombre de la cadena de bloques Data Manager puede contener letras minúsculas y números y tener una longitud máxima de 20 caracteres.
    Nodo de transacción | Elija un nodo de transacción. Solo se muestran los nodos de transacción a los que tiene acceso de lectura.
    Nombre de conexión | Escriba un nombre único de la conexión saliente donde se envían los datos de la transacción de la cadena de bloques.
    Punto de conexión de Event Grid | Elija un tema de Event Grid en la misma suscripción que la instancia de la cadena de bloques Data Manager.

1. Seleccione **Aceptar**.

    Se tarda menos de un minuto crear una instancia de Blockchain Data Manager. Una vez implementada la instancia, se inicia automáticamente. Una instancia de la cadena de bloques Data Manager en ejecución captura los eventos de la cadena de bloques desde el nodo de transacción y envía los datos a las conexiones salientes.

    La nueva instancia aparece en la lista de instancias de la cadena de bloques Data Manager para el miembro de Azure Blockchain Service.

    ![Lista de instancias de miembro de datos de la cadena de bloques](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Agregar aplicación de cadena de bloques

Si agrega una aplicación de cadena de bloques, la cadena de bloques Data Manager descodifica el estado de los eventos y las propiedades de la aplicación. De lo contrario, solo se envían datos de transacciones y bloques sin procesar. La cadena de bloques Data Manager también detecta direcciones de contrato cuando se implementa el contrato. Puede agregar varias aplicaciones de la cadena de bloques a una instancia de cadena de bloques Data Manager.

> [!IMPORTANT]
> Actualmente, las aplicaciones de cadena de bloques que declaran [tipos de matriz ](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) o [tipos de asignación](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) de Solidity no son totalmente compatibles. Las propiedades declaradas como matrices o tipos de asignación no se descodificarán en mensajes *ContractPropertiesMsg* ni *DecodedContractEventsMsg*.

Blockchain Data Manager requiere la ABI del contrato inteligente y el archivo de código de bytes implementado para agregar la aplicación.

### <a name="get-contract-abi-and-bytecode"></a>Obtener la ABI del contrato y el código de bytes

La ABI del contrato define las interfaces del contrato inteligente. Describe cómo interactuar con el contrato inteligente. Puede usar la [extensión del kit de desarrollo para Ethereum de Azure Blockchain](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) para copiar la ABI del contrato en el portapapeles.

1. En el panel del explorador de Visual Studio Code, expanda la carpeta **build/contracts** del proyecto de Solidity.
1. Haga clic con el botón derecho en el archivo JSON de metadatos del contrato. El nombre de archivo es el nombre del contrato inteligente seguido de la extensión **.json**.
1. Seleccione **Copy contract ABI** (Copiar ABI del contrato).

    ![Panel de Visual Studio Code con la selección Copy contract ABI (Copiar ABI del contrato)](./media/data-manager-portal/abi-devkit.png)

    La ABI del contrato se copia en el portapapeles.

1. Guarde la matriz **abi** como archivo JSON. Por ejemplo, *abi.json*. Lo usará en un paso posterior.

Blockchain Data Manager requiere el código de bytes implementado para el contrato inteligente. El código de bytes implementado es diferente del código de bytes del contrato inteligente. Puede usar la extensión del kit de desarrollo de Azure Blockchain para copiar el código de bytes en el portapapeles.

1. En el panel del explorador de Visual Studio Code, expanda la carpeta **build/contracts** del proyecto de Solidity.
1. Haga clic con el botón derecho en el archivo JSON de metadatos del contrato. El nombre de archivo es el nombre del contrato inteligente seguido de la extensión **.json**.
1. Seleccione **Copiar código de bytes de la transacción**.

    ![Panel de Visual Studio Code con la selección Copiar código de bytes de la transacción](./media/data-manager-portal/bytecode-devkit.png)

    El código de bytes se copia en el portapapeles.

1. Guarde el valor del **código de bytes** como archivo JSON. Por ejemplo, *bytecode.json*. Lo usará en un paso posterior.

El ejemplo siguiente muestra archivos *abi.json* y *bytecode.json* abiertos en el editor de VS Code. Sus archivos deben ser similares.

![Ejemplo de archivos abi.json y bytecode.json](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Obtener la ABI del contrato y la dirección URL del código de bytes

Blockchain Data Manager requiere que una dirección URL pueda acceder a la ABI del contrato y a los archivos del código de bytes al agregar una aplicación. Puede usar una cuenta de Azure Storage para proporcionar una dirección URL de acceso privado.

#### <a name="create-storage-account"></a>Crear cuenta de almacenamiento

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Cargar archivos de contrato

1. Cree un nuevo contenedor para la cuenta de almacenamiento. Seleccione **Contenedores > Contenedor**.

    ![Crear un contenedor de cuenta de almacenamiento](./media/data-manager-portal/create-container.png)

    | Campo | Descripción |
    |-------|-------------|
    | Nombre  | Nombre del contenedor. Por ejemplo, *smartcontract* |
    | Nivel de acceso público | Elija *Privado (sin acceso anónimo)* |

1. Seleccione **Aceptar** para crear el contenedor.
1. Seleccione el contenedor y, a continuación, seleccione **Cargar**.
1. Elija los dos archivos JSON creados en la sección [Obtener la ABI del contrato y el código de bytes](#get-contract-abi-and-bytecode).

    ![Carga de blob](./media/data-manager-portal/upload-blobs.png)

    Seleccione **Cargar**.

#### <a name="generate-url"></a>Generar dirección URL

Para cada blob, genere una firma de acceso compartido.

1. Seleccione el blob JSON de ABI.
1. Seleccione **Generar SAS**
1. Establezca la expiración de la firma de acceso deseada y seleccione **Generar el token de SAS de blob y la dirección URL**.

    ![Generación de un token de SAS](./media/data-manager-portal/generate-sas.png)

1. Copie la **dirección URL de SAS de blob** y guárdela en la siguiente sección.
1. Repita los pasos para [generar una dirección URL](#generate-url) para el blob JSON del código de bytes.

### <a name="add-application-to-instance"></a>Agregar aplicación a instancia

1. Seleccione la instancia de Blockchain Data Manager en la lista de instancias.
1. Seleccione **Blockchain applications** (aplicaciones de cadena de bloques).
1. Seleccione **Agregar**.

    ![Agregar una aplicación de cadena de bloques](./media/data-manager-portal/add-application.png)

    Escriba el nombre de la aplicación de cadena de bloques y las direcciones URL de la ABI del contrato inteligente y los códigos de bytes.

    Configuración | Descripción
    --------|------------
    Nombre | Escriba un nombre único para la aplicación de cadena de bloques de la que se va a realizar el seguimiento.
    ABI del contrato | Ruta de acceso URL al archivo ABI del contrato. Para más información, consulte [Crear una dirección URL de la ABI del contrato y el código de bytes](#create-contract-abi-and-bytecode-url).
    Código de bytes del contrato | Ruta de acceso URL al archivo de código de bytes. Para más información, consulte [Crear una dirección URL de la ABI del contrato y el código de bytes](#create-contract-abi-and-bytecode-url).

1. Seleccione **Aceptar**.

    Una vez creada la aplicación, esta aparece en la lista de aplicaciones de cadena de bloques.

    ![Lista de aplicaciones de cadena de bloques](./media/data-manager-portal/artifact-list.png)

Puede eliminar la cuenta de Azure Storage o usarla para configurar más aplicaciones de cadena de bloques. Si quiere eliminar la cuenta de Azure Storage, puede eliminar el grupo de recursos. Al eliminar el grupo de recursos, también se elimina la cuenta de almacenamiento asociada y cualquier otro recurso que esté asociado a dicho grupo.

## <a name="stop-instance"></a>Detener instancia

Detenga la instancia de Blockchain Manager cuando quiera dejar de capturar eventos Blockchain y enviar datos a las conexiones salientes. Cuando se detiene la instancia, no se aplica ningún cargo por la cadena de bloques Data Manager. Para más información, consulte los [precios](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Vaya a **Información general** y seleccione **Detener**.

    ![Detener instancia](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Pasos siguientes

Intente crear con el siguiente tutorial un explorador de mensajes de transacción de cadena de bloques con Blockchain Data Manager y Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Uso de Blockchain Data Manager para enviar datos a Azure Cosmos DB](data-manager-cosmosdb.md)