---
title: 'Uso de Queue Storage desde PHP: Azure Storage'
description: Aprenda a usar el servicio Azure Queue Storage para crear y eliminar colas, así como insertar, obtener y eliminar mensajes. Los ejemplos están escritos en C++.
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 01/11/2018
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 174de881eaf0b929f05b2d0aff4a3782d02ba678
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128579541"
---
# <a name="how-to-use-queue-storage-from-php"></a>Uso de Queue Storage desde PHP

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio Azure Queue Storage. Los ejemplos se escriben mediante clases de la [biblioteca cliente de Azure Storage para PHP](https://github.com/Azure/azure-storage-php). Entre los escenarios descritos se incluyen insertar, ojear, obtener y eliminar mensajes de la cola, así como crear y eliminar colas.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Creación de una aplicación PHP

El único requisito para crear una aplicación PHP que acceda a Azure Queue Storage es que el código haga referencia a clases de la [biblioteca cliente de Azure Storage para PHP](https://github.com/Azure/azure-storage-php). Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, incluido el Bloc de notas.

En esta guía, se usan funciones del servicio Queue Storage a las que se puede llamar desde una aplicación PHP localmente, o bien mediante código en una aplicación web de Azure.

## <a name="get-the-azure-client-libraries"></a>Obtención de las bibliotecas de clientes de Azure

### <a name="install-via-composer"></a>Instalación mediante Composer

1. Cree un archivo con el nombre `composer.json` en la raíz del proyecto y agréguele el código siguiente:

    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```

2. Descargue [`composer.phar`](https://getcomposer.org/composer.phar) en la raíz del proyecto.

3. Abra un símbolo del sistema y ejecute el siguiente comando en la raíz del proyecto:

    ```console
    php composer.phar install
    ```

También puede ir a la [biblioteca cliente de Azure Storage para PHP](https://github.com/Azure/azure-storage-php) en GitHub para clonar el código fuente.

## <a name="configure-your-application-to-access-queue-storage"></a>Configuración de la aplicación para obtener acceso a Queue Storage

Para usar las API de Azure Queue Storage, es preciso:

1. Hacer referencia al archivo cargador automático mediante la instrucción [`require_once`](https://www.php.net/manual/en/function.require-once.php).
2. Hacer referencia a todas las clases que puede que use.

En el siguiente ejemplo se muestra cómo incluir el archivo autocargador y hacer referencia a la clase `QueueRestProxy`.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

En los ejemplos siguientes, la instrucción `require_once` se muestra siempre, pero solo se hará referencia a las clases necesarias para ejecutar el ejemplo.

## <a name="set-up-an-azure-storage-connection"></a>Configuración de una conexión de Azure Storage

Para crear una instancia de un cliente de Azure Queue Storage, primero es preciso tener una cadena de conexión válida. Este es el formato de la cadena de conexión de Queue Storage.

Para obtener acceso a un servicio en directo:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Para obtener acceso al emulador de almacenamiento:

```php
UseDevelopmentStorage=true
```

Para crear un cliente de Azure Queue Storage, debe usar la clase `QueueRestProxy`. Puede usar cualquiera de las técnicas siguientes:

- pasarle directamente la cadena de conexión, o bien
- usar variables de entorno en la aplicación web para almacenar la cadena de conexión. Consulte el documento sobre la [configuración de aplicaciones web en Azure](../../app-service/configure-common.md) para configurar cadenas de conexión.

En los ejemplos descritos aquí, la cadena de conexión se pasa directamente.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Creación de una cola

Un objeto `QueueRestProxy` le permite crear una cola mediante el método `CreateQueue`. Al crear una cola, puede establecer opciones en ella, aunque no es obligatorio. Este ejemplo muestra cómo establecer metadatos en una cola.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> No debe confiar en la distinción entre minúsculas y mayúsculas para las claves de metadatos. Todas las claves se leen en el servicio en minúsculas.

## <a name="add-a-message-to-a-queue"></a>un mensaje a una cola

Para agregar un mensaje a una cola, use `QueueRestProxy->createMessage`. El método toma el nombre de la cola, el texto del mensaje y las opciones de mensaje (que son opcionales).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello, World");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Inspección del siguiente mensaje

Para inspeccionar uno o varios mensajes situados en la parte delantera de una cola, sin quitarlos de la cola, llame a `QueueRestProxy->peekMessages`. De forma predeterminada, el método `peekMessage` devuelve un solo mensaje, pero ese valor se puede cambiar mediante el método `PeekMessagesOptions->setNumberOfMessages`.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>Extracción del siguiente mensaje

El código borra un mensaje de una cola en dos pasos. Primero llama a `QueueRestProxy->listMessages`, que hace que el mensaje sea invisible a cualquier otro código que esté leyendo de la cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos. (Si el mensaje no se elimina en este período, volverá a estar visible de nuevo en la cola). Para acabar de quitar el mensaje de la cola, debe llamar a `QueueRestProxy->deleteMessage`. Este proceso de extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. El código siguiente llama a `deleteMessage` justo después de haberse procesado el mensaje.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Cambio del contenido de un mensaje en cola

El contenido de un mensaje local en la cola se puede cambiar llamando a `QueueRestProxy->updateMessage`. Si el mensaje representa una tarea de trabajo, puede usar esta característica para actualizar el estado de la tarea de trabajo. El siguiente código actualiza el mensaje de la cola con contenido nuevo y amplía el tiempo de espera de la visibilidad en 60 segundos más. De este modo, se guarda el estado de trabajo asociado al mensaje y se le proporciona al cliente un minuto más para que siga elaborando el mensaje. Esta técnica se puede utilizar para realizar un seguimiento de los flujos de trabajo de varios pasos en los mensajes en cola, sin que sea necesario volver a empezar desde el principio si se produce un error en un paso del proceso a causa de un error de hardware o software. Normalmente, también mantendría un número de reintentos y, si el mensaje se intentara más de *n* veces, lo eliminaría. Esto proporciona protección frente a un mensaje que produce un error en la aplicación cada vez que se procesa.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-dequeuing-messages"></a>Opciones adicionales para quitar mensajes de la cola

Hay dos formas de personalizar la recuperación de mensajes de una cola. En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo lugar, puede establecer un tiempo de espera de la visibilidad más largo o más corto para que el código disponga de más o menos tiempo para procesar cada mensaje. El siguiente ejemplo de código usa el método `getMessages` para obtener 16 mensajes en una llamada. Luego, utiliza un bucle `for` para procesar cada mensaje. También establece el tiempo de espera de la invisibilidad en cinco minutos para cada mensaje.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>longitud de la cola

Puede obtener una estimación del número de mensajes existentes en una cola. El método `QueueRestProxy->getQueueMetadata` recupera los metadatos de una cola. La llamada al método `getApproximateMessageCount` en el objeto devuelto proporciona el recuento del número mensajes que hay en la cola. El recuento es solo aproximado, ya que se pueden agregar o borrar mensajes después de que Queue Storage haya respondido su solicitud.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Eliminación de una cola

Para eliminar una cola y todos los mensajes que contiene, llame al método `QueueRestProxy->deleteQueue`.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los aspectos básicos de Azure Queue Storage, siga estos vínculos para obtener más información acerca de tareas de almacenamiento más complejas:

- Consulte la [referencia de API para la biblioteca cliente de Azure Storage para PHP](https://azure.github.io/azure-storage-php/).
- Consulte el [ejemplo de cola avanzada](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Para más información, consulte el artículo sobre el [Centro para desarrolladores de PHP](https://azure.microsoft.com/develop/php/).
