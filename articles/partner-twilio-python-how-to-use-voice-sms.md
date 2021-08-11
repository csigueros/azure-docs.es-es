---
title: Uso de Twilio para voz y SMS (Python) | Microsoft Docs
description: Aprenda a realizar llamadas telefónicas y a enviar mensajes SMS con el servicio de la API de Twilio en Azure. Ejemplos de código escritos en Python.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.custom: devx-track-python
ms.openlocfilehash: 6d2f2b9cd145a2472ccc9ed24fd78f30107db710
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110463815"
---
# <a name="use-twilio-for-voice-and-sms-capabilities-in-python"></a>Uso de Twilio para las funcionalidades de voz y SMS en Python
En esta guía se describe cómo realizar tareas comunes de programación comunes con el servicio de API de Twilio en Azure. Los escenarios descritos incluyen realizar una llamada telefónica y enviar un mensaje de servicio de mensajes cortos (SMS). 

Para obtener más información sobre Twilio y el uso de voz y mensajes SMS en sus aplicaciones, consulte la sección [Pasos siguientes](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>¿Qué es Twilio?
Twilio permite a los desarrolladores insertar voz, voz sobre IP (VoIP) y mensajería en las aplicaciones. Los desarrolladores virtualizan toda la infraestructura necesaria en un entorno global basado en la nube y lo exponen a través de la plataforma de API de Twilio. Las aplicaciones son sencillas de compilar y pueden escalarse.

Los componentes de Twilio incluyen:

- **Twilio Voice**: permite que sus aplicaciones realicen y reciban llamadas telefónicas.
- **Twilio SMS**: permite que su aplicación envíe y reciba mensajes de texto.
- **Twilio Client**: permite realizar llamadas VoIP desde cualquier teléfono, tableta o explorador. Admite la especificación WebRTC para la comunicación en tiempo real.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Precios de Twilio y ofertas especiales
Los clientes de Azure reciben una [oferta especial][special_offer] de 10 USD de crédito de Twilio cuando actualizan su cuenta de Twilio. Este crédito se puede aplicar a cualquier uso de Twilio. Un crédito de 10 USD equivale al envío de hasta 1000 mensajes SMS o la recepción de hasta 1000 minutos de voz entrante, según la ubicación del número de teléfono y el destino del mensaje o de la llamada.

Twilio es un servicio de pago por uso. No hay precios de configuración y puede cerrar su cuenta en cualquier momento. Puede encontrar más detalles en [Precios de Twilio][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Conceptos
La API de Twilio es una API de RESTful que proporciona funciones de voz y SMS para las aplicaciones. Las bibliotecas de cliente están disponibles en varios idiomas. Para obtener una lista, consulte las [bibliotecas de la API de Twilio][twilio_libraries].

Aspectos fundamentales de la API de Twilio son los verbos de Twilio y el lenguaje de marcado de Twilio (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Verbos de Twilio
La API usa verbos como estos que indican a Twilio lo que debe hacer:

* **&lt;Dial&gt;** : conecta la persona que llama con otro teléfono.
* **&lt;Gather&gt;** : recopila los dígitos numéricos que se introdujeron en el teclado del teléfono.
* **&lt;Hangup&gt;** : finaliza una llamada.
* **&lt;Pause&gt;** : espera en silencio una cantidad de segundos específica.
* **&lt;Play&gt;** : reproduce un archivo de audio.
* **&lt;Queue&gt;** : agrega la llamada a una cola de autores de llamada.
* **&lt;Record&gt;** : graba la voz de la persona que llama y devuelve una dirección URL de un archivo que contiene la grabación.
* **&lt;Redirect&gt;** : transfiere el control de una llamada o SMS al TwiML en una URL diferente.
* **&lt;Reject&gt;** : rechaza una llamada entrante al número de Twilio sin cobrarle.
* **&lt;Say&gt;** : convierte texto a voz para hacer una llamada.
* **&lt;Sms&gt;** : envía un mensaje SMS.

Obtenga información sobre los demás verbos y las funcionalidades de Twilio a través de [Documentación de lenguaje de marcado de Twilio][twiml].

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML es un conjunto de instrucciones basadas en XML y en los verbos de Twilio que indican a Twilio cómo procesar una llamada o un mensaje SMS.

Como ejemplo, la instrucción de TwiML siguiente convertiría el texto **Hola mundo** en la instrucción de voz:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
  <Response>
    <Say>Hello World</Say>
  </Response>
```

Cuando su aplicación llama a la [API de Twilio][twilio_api], uno de los parámetros de API es la URL que devuelve la respuesta de TwiML. Con fines de desarrollo, puede usar las URL que ofrece Twilio para proporcionar las respuestas de TwiML que usarán sus aplicaciones. También puede hospedar sus propias direcciones URL para producir las repuestas de TwiML. Otra opción es usar el objeto `TwiMLResponse`.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Creación de una cuenta de Twilio
Cuando esté preparado para obtener una cuenta de Twilio, regístrese para obtener una [evaluación de Twilio][try_twilio]. Puede empezar con una cuenta gratuita y, posteriormente, actualizarla.

Cuando se registre para obtener una cuenta de Twilio, recibirá un id. de seguridad (SID) de la cuenta y un token de autenticación. Los necesitará ambos para realizar llamadas API de Twilio. Para evitar el acceso no autorizado a su cuenta, mantenga protegido el token de autenticación. Puede ver el SID de la cuenta y el token de autenticación en la [consola de Twilio][twilio_console], en los campos con las etiquetas **ACCOUNT SID** y **AUTH TOKEN**, respectivamente.

## <a name="create-a-python-application"></a><a id="create_app"></a>Creación de una aplicación de Python
Una aplicación de Python que usa Twilio y que se ejecuta en Azure no es distinta a otra aplicación de Python que usa Twilio. Si bien los servicios de Twilio se basan en REST y pueden llamarse desde Python de varias formas, este artículo se centra en cómo usar los servicios de Twilio con la [biblioteca de Twilio para Python desde GitHub][twilio_python]. Para obtener más información sobre el uso de esta biblioteca, consulte la [documentación de la biblioteca para Python de Twilio][twilio_lib_docs].

Primero, [configure una nueva máquina virtual Linux de Azure][azure_vm_setup] que actúe como host de la nueva aplicación web de Python. Una vez que la máquina virtual esté en ejecución, tendrá que exponer la aplicación en un puerto público.

Para agregar una regla de entrada:
  1. Vaya a la página [Grupo de seguridad de red][azure_nsg].
  2. Seleccione el grupo de seguridad de red que se corresponda con su máquina virtual.
  3. Agregue una **Regla de salida** para el **puerto 80**. Asegúrese de permitir la entrada desde cualquier dirección.

Para establecer la etiqueta de nombre DNS:
  1. Vaya a la página [Direcciones IP públicas][azure_ips].
  2. Seleccione la dirección IP pública que se corresponda con su máquina virtual.
  3. Establezca la información de **Etiqueta de nombre DNS** en la sección **Configuración**. En este ejemplo, es similar a *\<your-domain-label\>.centralus.cloudapp.azure.com*.

Una vez que pueda conectarse a través de SSH a la máquina virtual, podrá instalar el marco web que elija. Los dos más conocidos en Python son [Flask](http://flask.pocoo.org/) y [Django](https://www.djangoproject.com). Puede instalar cualquiera de estos con el comando `pip install`.

Tenga en cuenta que configuramos la máquina virtual para permitir el tráfico solo en el puerto 80. Por tanto, asegúrese de configurar la aplicación para que utilice este puerto.

## <a name="configure-your-application-to-use-the-twilio-library"></a><a id="configure_app"></a>Configuración de su aplicación para utilizar la biblioteca de Twilio
Hay dos formas de configurar la aplicación para que use la biblioteca de Twilio para Python:

* Instale la biblioteca de Twilio para Python como un paquete PIP mediante el siguiente comando:
   
  `$ pip install twilio`

* Descargue la [biblioteca de Twilio para Python desde GitHub][twilio_python] e instálela de esta forma:

  `$ python setup.py install`

Después de instalar la biblioteca de Twilio para Python, puede importarla en los archivos de Python:

`import twilio`

Para más información, consulte el archivo [Léame de Twilio en GitHub](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a name="make-an-outgoing-call"></a><a id="howto_make_call"></a>Realización de una llamada saliente
A continuación se muestra cómo realizar una llamada saliente. Este código usa el sitio proporcionado por Twilio para devolver la respuesta de TwiML. Sustituya los valores por los números de teléfono `from_number` y `to_number`. Asegúrese de haber comprobado el número de teléfono `from_number` para la cuenta de Twilio antes de ejecutar el código.

```python
from urllib.parse import urlencode

# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified.
from_number = "NNNNNNNNNNN"

# The number of the phone receiving call.
to_number = "NNNNNNNNNNN"

# Use the Twilio-provided site for the TwiML response.
url = "https://twimlets.com/message?"

# The phone message text.
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url + urlencode({'Message': message}))
print(call.sid)
```

> [!IMPORTANT]
> Los números de teléfono deben tener el formato de un signo más y un código de país. Un ejemplo es `+16175551212` (formato E.164). Twilio también aceptará números de EE. UU. sin formato, como `(415) 555-1212` o `415-555-1212`.

Este código usa el sitio proporcionado por Twilio para devolver la respuesta de TwiML. Puede en cambio usar su propio sitio para proporcionar la respuesta de TwiML. Para más información, consulte [Proporcionar respuestas de TwiML desde su propio sitio web](#howto_provide_twiml_responses).

## <a name="send-an-sms-message"></a><a id="howto_send_sms"></a>Enviar un mensaje SMS
El ejemplo siguiente muestra cómo enviar un mensaje SMS con la clase `TwilioRestClient`. Twilio proporciona el número `from_number` de cuentas de prueba para enviar mensajes SMS. El número `to_number` se debe comprobar en la cuenta de Twilio antes de ejecutar el código.

```python
# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

from_number = "NNNNNNNNNNN"  # With a trial account, texts can only be sent from your Twilio number.
to_number = "NNNNNNNNNNN"
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Send the SMS message.
message = client.messages.create(to=to_number,
                                    from_=from_number,
                                    body=message)
```

## <a name="provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Proporcionar respuestas de TwiML desde su propio sitio web
Cuando la aplicación inicia una llamada a la API de Twilio, Twilio envía su solicitud a una URL que debe devolver una respuesta de TwiML. El ejemplo anterior usa la dirección URL [https://twimlets.com/message][twimlet_message_url]proporcionada por Twilio. 

> [!NOTE]
> Aunque TwiML está diseñado para utilizarse en Twilio, se puede ver en el explorador. Por ejemplo, seleccione [https://twimlets.com/message][twimlet_message_url] para ver un elemento `<Response>` vacío. Para obtener otro ejemplo, seleccione [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] para ver un elemento `<Response>` que contiene un elemento `<Say>`.

En lugar de confiar en la URL que Twilio proporciona, puede crear su propio sitio que devuelve respuestas HTTP. Puede crear el sitio en cualquier lenguaje que devuelva respuestas XML. En este artículo se da por supuesto que usará Python para crear la respuesta de TwiML.

Los siguientes ejemplos dan como resultado una respuesta de TwiML que dice **Hello World** en la llamada.

Con Flask:

```python
from flask import Response
@app.route("/")
def hello():
    xml = '<Response><Say>Hello world.</Say></Response>'
    return Response(xml, mimetype='text/xml')
```

Con Django:

```python
from django.http import HttpResponse
def hello(request):
    xml = '<Response><Say>Hello world.</Say></Response>'
    return HttpResponse(xml, content_type='text/xml')
```

Como puede ver en el ejemplo anterior, la respuesta de TwiML es sencillamente un documento XML. La biblioteca de Twilio para Python contiene clases que generarán TwiML de manera automática. El ejemplo siguiente genera la respuesta equivalente como se mostró anteriormente, pero usa el módulo `twiml` de la biblioteca de Twilio para Python:

```python
from twilio import twiml

response = twiml.Response()
response.say("Hello world.")
print(str(response))
```

Para obtener más información sobre TwiML, consulte la [referencia de TwiML][twiml_reference].

Una vez configurada la aplicación de Python para proporcionar respuestas de TwiML, use la dirección URL de la aplicación como la dirección URL que se pasa al método `client.calls.create`. Por ejemplo, si tiene una aplicación web denominada *MyTwiML* implementada en un servicio hospedado de Azure, puede usar su dirección URL como webhook, como se muestra en el ejemplo siguiente:

```python
from twilio.rest import TwilioRestClient

account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"
from_number = "NNNNNNNNNNN"
to_number = "NNNNNNNNNNN"
url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

# Initialize the Twilio Client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url)
print(call.sid)
```

## <a name="use-additional-twilio-services"></a><a id="AdditionalServices"></a>Uso de servicios de Twilio adicionales
Además de los ejemplos aquí mostrados, Twilio ofrece API basadas en web que puede utilizar para obtener funciones adicionales de Twilio desde su aplicación de Azure. Para obtener todos los detalles, vea la [documentación de las API de Twilio][twilio_api].

## <a name="next-steps"></a><a id="NextSteps"></a>Pasos siguientes
Ahora que conoce los fundamentos del servicio Twilio, siga estos vínculos para obtener más información:

* [Directrices de seguridad de Twilio][twilio_security_guidelines]
* [Guías de procedimientos y código de ejemplo de Twilio][twilio_howtos]
* [Tutoriales de inicio rápido de Twilio][twilio_quickstarts]
* [Twilio en GitHub][twilio_on_github]
* [Contacto con el servicio técnico de Twilio][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/all
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
[azure_ips]: ./virtual-network/virtual-network-public-ip-address.md
[azure_vm_setup]: ./virtual-machines/linux/quick-create-portal.md
[azure_nsg]: ./virtual-network/manage-network-security-group.md