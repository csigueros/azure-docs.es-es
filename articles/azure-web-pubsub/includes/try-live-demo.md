---
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/08/2021
ms.openlocfilehash: 1cd52fc7930407317d426b3e2615202dce37e860
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751180"
---
## <a name="get-the-client-url-with-a-temp-access-token"></a>Obtener la dirección URL del cliente con un token de acceso temporal

Azure Portal proporciona un generador de direcciones URL de cliente simple para generar una dirección URL temporal con fines de prueba y validación rápidas. Vamos a usar esta herramienta para obtener una dirección URL de acceso de cliente temporal y conectar con la instancia.

- Vaya a Azure Portal y descubra la instancia de Azure Web PubSub.
- Vaya a `Client URL Generator` en la hoja `Key`. 
- Establecer `Roles` correctamente: **Enviar a grupos y** **Unirse o dejar grupos**
- Genere y copie `Client Access URL`. 

:::image type="content" source="../media/quickstart-live-demo/generate-client-url.png" alt-text="Captura de pantalla de la generación de la dirección URL del cliente.":::

## <a name="try-the-instance-with-an-online-demo"></a>Prueba de la instancia con una demostración en línea

Con esta demostración en directo, puede unirse a un grupo, o dejarlo, y enviar mensajes a los miembros del grupo fácilmente:

> [!div class="nextstepaction"]
> [Abrir la demostración](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html)

> [!NOTE]
>  La **dirección URL de acceso de cliente** es una herramienta útil que se proporciona en el portal para simplificar la experiencia de introducción. También puede usar esta dirección URL de acceso de cliente para realizar algunas pruebas rápidas de conexión. Para escribir su propia aplicación, proporcionamos SDK en 4 lenguajes para ayudarle a generar la dirección URL. 

- Pruebe diferentes grupos a los que unirse y diferentes grupos a los que enviar mensajes y vea qué mensajes se reciben. Por ejemplo:
    - Haga que dos clientes se unan al mismo grupo. Verá que el mensaje se difunde a los miembros del grupo. 
    - Haga que dos clientes se unan a grupos diferentes. Verá que el cliente no puede recibir el mensaje si no es miembro del grupo. 
- También puede probar a desactivar `Roles` al generar `Client Access URL` para ver lo que ocurre cuando se une a un grupo o se envían mensajes a un grupo. Por ejemplo:
    - Desactive el permiso `Send to Groups`. Verá que el cliente no puede enviar mensajes al grupo. 
    - Desactive el permiso `Join/Leave Groups`. Verá que el cliente no puede unirse a un grupo. 
