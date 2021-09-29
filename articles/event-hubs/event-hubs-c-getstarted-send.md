---
title: 'Inicio rápido: Envío de eventos a Azure Event Hubs con C'
description: 'Inicio rápido: en este artículo se ofrece un tutorial para crear una aplicación de C que envía eventos a Azure Event Hubs.'
ms.topic: quickstart
ms.date: 09/23/2021
ms.openlocfilehash: 5f93e3cac123db184f4a669f87f90adfebdce5b0
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215270"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>Inicio rápido: Envío de eventos a Azure Event Hubs con C

## <a name="introduction"></a>Introducción
Azure Event Hubs es una plataforma de streaming de macrodatos y servicio de ingesta de eventos de gran escalabilidad capaz de recibir y procesar millones de eventos por segundo. Event Hubs puede procesar y almacenar eventos, datos o telemetría generados por dispositivos y software distribuido. Los datos enviados a un centro de eventos se pueden transformar y almacenar con cualquier proveedor de análisis en tiempo real o adaptadores de procesamiento por lotes y almacenamiento. Para más información sobre Event Hubs, consulte [Introducción a Event Hubs](event-hubs-about.md) y [Características de Event Hubs](event-hubs-features.md).

En este tutorial se describe cómo enviar eventos a un centro de eventos mediante una aplicación de consola en C. 

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, necesitará lo siguiente:

* Un entorno de desarrollo de C. Para este tutorial se presupone el uso de la pila de gcc en una máquina virtual Linux de Azure con Ubuntu 14.04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* **Creación de un espacio de nombres de Event Hubs y un centro de eventos**. Use [Azure Portal](https://portal.azure.com) para crear un espacio de nombres de tipo Event Hubs y obtener las credenciales de administración que la aplicación necesita para comunicarse con el centro de eventos. Para crear un espacio de nombres y un centro de eventos, siga el procedimiento que se indica en [este artículo](event-hubs-create.md). Para obtener el valor de la clave de acceso del centro de eventos, siga las instrucciones del artículo: [Obtención de la cadena de conexión](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Utilice la clave de acceso en el código que escriba más adelante en este tutorial. El nombre de la clave predeterminada es: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Escritura de código para enviar mensajes a Event Hubs
En esta sección se muestra cómo se escribe una aplicación en C para enviar eventos al centro de eventos. El código usa la biblioteca Proton AMQP del [proyecto Apache Qpid](https://qpid.apache.org/). Esto es parecido a usar temas y colas de Service Bus con AMQP desde C como se muestra [en este ejemplo](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Para más información, consulte la [documentación de Qpid Proton](https://qpid.apache.org/proton/index.html).

1. En la página [Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html), siga las instrucciones para instalar Qpid Proton según su entorno.
2. Para compilar la biblioteca Proton, instale los paquetes siguientes:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Descargue la [biblioteca de Qpid Proton](https://qpid.apache.org/proton/index.html) y extráigala; por ejemplo:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Cree un directorio de compilación, compile y realice la instalación:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. En su directorio de trabajo, cree un nuevo archivo denominado **sender.c** con el siguiente código. No olvide reemplazar los valores de nombre/clave de SAS, el nombre del centro de eventos y del espacio de nombres. También debe sustituir una versión con codificación URL de la clave para la regla **SendRule** creada anteriormente. Puede codificar con URL [aquí](https://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Compile el archivo, suponiendo que **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > En este código se usa una ventana de salida de 1 para forzar que los mensajes salgan tan pronto como sea posible. Se recomienda que la aplicación pruebe con los mensajes por lotes para aumentar el rendimiento. Consulte la [página Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html) para más información sobre cómo usar la biblioteca de Qpid Proton en este y otros entornos y desde las plataformas para las que se proporcionan enlaces (actualmente, Perl, PHP, Python y Ruby).

Ejecute la aplicación para enviar mensajes al centro de eventos. 

Felicidades. Ha enviado mensajes a un centro de eventos.

## <a name="next-steps"></a>Pasos siguientes
Lea los siguientes artículos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Características y terminología de Azure Event Hubs](event-hubs-features.md)


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
