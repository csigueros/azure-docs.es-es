---
title: 'Aprovisionamiento y configuración de enrutamiento directo de Azure: Azure Communication Services'
description: Obtenga información sobre cómo agregar un controlador de límites de sesión y configurar el enrutamiento de voz para el enrutamiento directo de Azure Communication Services
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 27b1b08196ef76bba92bf1f2ff518585c896178a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743536"
---
# <a name="session-border-controllers-and-voice-routing"></a>Controladores de límites de sesión y enrutadores de voz
El enrutamiento directo de Azure Communication Services le permite conectar la infraestructura de telefonía existente a Azure. En el artículo se enumeran los pasos de alto nivel necesarios para conectar un controlador de límites de sesión (CLS) compatible con enrutamiento directo y cómo funciona el enrutamiento de voz para el recurso de comunicación habilitado. 

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]
 
Para saber si el enrutamiento directo de Azure Communication Services es la solución adecuada para su organización, consulte [Conceptos de telefonía de Azure](./telephony-concept.md). Para obtener información sobre los requisitos previos y la planificación de la implementación, consulte [Requisitos de infraestructura de enrutamiento directo de Azure Communication Services](./direct-routing-infrastructure.md).

## <a name="connect-the-sbc-with-azure-communication-services"></a>Conectar el CLS con Azure Communication Services

### <a name="configure-using-azure-portal"></a>Configurar mediante Azure Portal 
1. En el panel de navegación izquierdo, seleccione Enrutamiento directo en Llamadas de voz - RTC y, a continuación, seleccione Configurar en la pestaña Controlador de límites de sesión.
1. Escriba un nombre de dominio completo y un puerto de señalización para el CLS.
 
Si usa Office 365, asegúrese de que la parte del dominio del nombre de dominio completo del CLS sea diferente del dominio que registró en el portal de administración de Office 365 en Dominios. 
- Por ejemplo, si `contoso.com` es un dominio registrado en O365, no puede usar `sbc.contoso.com` para Communication Services. Pero puede usar un dominio de nivel superior si no existe en O365: puede crear un dominio `acs.contoso.com` y usar el nombre de dominio completo `sbc.acs.contoso.com` como nombre del CLS.
- El certificado CLS debe coincidir con el nombre; se admiten certificados comodín.
- El dominio *.onmicrosoft.com no se puede usar para el nombre de dominio completo del CLS.
Para obtener la lista completa de requisitos, consulte [Requisitos de infraestructura de enrutamiento directo de Azure](./direct-routing-infrastructure.md).

   :::image type="content" source="../media/direct-routing-provisioning/add-session-border-controller.png" alt-text="Adición del controlador de límites de sesión.":::
- Cuando termine, haga clic en Siguiente.
Si todo está configurado correctamente, debería ver el intercambio de mensajes OPTIONS entre Microsoft y el controlador de límites de sesión para usar la supervisión o los registros del CLS para validar la conexión.

## <a name="voice-routing-considerations"></a>Consideraciones sobre el enrutamiento de voz

El enrutamiento directo de Azure Communication Services tiene un mecanismo de enrutamiento que permite enviar una llamada a un controlador de límites de sesión (CLS) específico basado en el patrón del número llamado.
Al agregar una configuración de enrutamiento directo a un recurso, todas las llamadas realizadas desde las instancias (identidades) de este recurso probarán primero un tronco de enrutamiento directo. El enrutamiento se basa en un número marcado y una coincidencia en las rutas de voz configuradas para el recurso. Si hay una coincidencia, la llamada pasa por el tronco de enrutamiento directo. Si no hay ninguna coincidencia, el siguiente paso es procesar el parámetro alternateCallerId del método callAgent.startCall. Si el recurso está habilitado para llamadas de voz (RTC) y tiene al menos un número comprado en Microsoft (y si alternateCallerId coincide con uno de los números comprados para el recurso), la llamada se enruta a través de la llamada de voz (RTC) mediante la infraestructura de Microsoft. Si el parámetro alternateCallerId no coincide con ninguno de los números comprados, se producirá un error en la llamada. En el diagrama siguiente se muestra la lógica de enrutamiento de voz de Azure Communication Services.

:::image type="content" source="../media/direct-routing-provisioning/voice-routing-diagram.png" alt-text="Enrutamiento de voz saliente de Communication Services.":::

## <a name="voice-routing-examples"></a>Ejemplos de enrutamiento de voz
En los ejemplos siguientes se muestra el enrutamiento de voz en un flujo de llamadas.

### <a name="one-route-example"></a>Un ejemplo de ruta:
Si ha creado una ruta de voz con un patrón `^\+1(425|206)(\d{7})$` y le ha agregado `sbc1.contoso.biz` y `sbc2.contoso.biz`, cuando el usuario realice una llamada a `+1 425 XXX XX XX` o `+1 206 XXX XX XX`, la llamada primero se enrutará al CLS `sbc1.contoso.biz` o `sbc2.contoso.biz`. Si no hay CLS disponible, se descarta la llamada.

### <a name="two-routes-example"></a>Ejemplo de dos rutas:
Si ha creado una ruta de voz con un patrón `^\+1(425|206)(\d{7})$` y le ha agregado `sbc1.contoso.biz` y `sbc2.contoso.biz`, y luego ha creado una segunda ruta con el mismo patrón con `sbc3.contoso.biz` y `sbc4.contoso.biz`. En este caso, cuando el usuario realiza una llamada a `+1 425 XXX XX XX` o `+1 206 XXX XX XX`, la llamada se enruta primero al CLS `sbc1.contoso.biz` o `sbc2.contoso.biz`. Si sbc1 y sbc2 no están disponibles, se probará la ruta con menor prioridad (`sbc3.contoso.biz` y `sbc4.contoso.biz`). Si ninguno de los CLS de la segunda ruta está disponible, se descarta la llamada.

### <a name="three-routes-example"></a>Ejemplo de tres rutas:
Si ha creado una ruta de voz con un patrón `^\+1(425|206)(\d{7})$` y le ha agregado `sbc1.contoso.biz` y `sbc2.contoso.biz`, y luego ha creado una segunda ruta con el mismo patrón con `sbc3.contoso.biz` y `sbc4.contoso.biz`. y se ha creado una tercera ruta con `^+1(\d[10])$` con `sbc5.contoso.biz`. En este caso, cuando el usuario realiza una llamada a `+1 425 XXX XX XX` o `+1 206 XXX XX XX`, la llamada se enruta primero al CLS `sbc1.contoso.biz` o `sbc2.contoso.biz`. Si sbc1 y sbc2 no están disponibles, se probará la ruta con menor prioridad (`sbc3.contoso.biz` y `sbc4.contoso.biz`). Si ninguno de los CLS de la segunda ruta está disponible, se probará la tercera ruta; si sbc5 tampoco está disponible, se descarta la llamada. Además, si un usuario marca `+1 321 XXX XX XX`, la llamada va a `sbc5.contoso.biz` y no está disponible, se descarta la llamada.

> [!NOTE]
> En todos los ejemplos, aunque la ruta de voz superior tiene mayor prioridad, los CLS de una ruta se prueban en orden aleatorio.

> [!NOTE]
> En todos los ejemplos, si el número marcado no coincide con el patrón, la llamada se descartará a menos que exista un número comprado para el recurso de comunicación y este número se usó como `alternateCallerId` en la aplicación. 

## <a name="configure-voice-routing"></a>Configuración del enrutamiento de voz 

### <a name="configure-using-azure-portal"></a>Configurar mediante Azure Portal

:::image type="content" source="../media/direct-routing-provisioning/voice-routing-configuration.png" alt-text="Configuración del enrutamiento de voz saliente de Communication Services.":::

Asigne un nombre a la ruta de voz, especifique el patrón numérico con expresiones regulares y seleccione el CLS para ese patrón. A continuación se muestran algunos ejemplos de expresiones regulares:
- `^\+\d+$`: coincide con un número de teléfono con uno o más dígitos que comienza con un signo más
- `^+1(\d[10])$`: coincide con un número de teléfono con diez dígitos después de un `+1`
- `^\+1(425|206)(\d{7})$`: coincide con un número de teléfono que comienza con `+1425` o `+1206` seguido de siete dígitos
- `^\+0?1234$`: coincide con números de teléfono `+01234` y `+1234`.

Para obtener más información sobre expresiones regulares, consulte [Información general de expresiones regulares .NET](/dotnet/standard/base-types/regular-expressions).

Puede seleccionar varios CLS para un solo patrón. En tal caso, el algoritmo de enrutamiento los elegirá en orden aleatorio. También puede especificar el patrón numérico exacto más de una vez. La fila más alta tendrá mayor prioridad y, si todos los CLS asociados a esa fila no están disponibles, se seleccionará la siguiente fila. De este modo, se crean escenarios de enrutamiento complejos.

## <a name="delete-direct-routing-configuration"></a>Eliminación de la configuración de enrutamiento directo

### <a name="delete-using-azure-portal"></a>Eliminación con Azure Portal

#### <a name="to-delete-a-voice-route"></a>Para eliminar una ruta de voz:
1. En el panel de navegación izquierdo, vaya a Enrutamiento directo en Llamada de voz - RTC y, a continuación, seleccione la pestaña Rutas de voz.
1. Seleccione la(s) ruta(s) que quiera eliminar marcando la casilla.
1. Seleccione Quitar.

#### <a name="to-delete-an-sbc"></a>Para eliminar un CLS:
1. En el panel de navegación izquierdo, vaya a Enrutamiento directo en Llamadas de voz - RTC.
1. En una pestaña Controladores de límites de sesión, seleccione Configurar.
1. Borre los campos FQDN y Puerto del CLS que quiera quitar y seleccione Siguiente.
1. En una pestaña Rutas de voz, revise la configuración de enrutamiento de voz y realice cambios si es necesario. Seleccione Guardar.

> [!NOTE]
> Al quitar el CLS asociado a una ruta de voz, puede elegir otro CLS para la ruta en la pestaña Rutas de voz. Se eliminarán las rutas de voz sin CLS.

## <a name="next-steps"></a>Pasos siguientes

### <a name="conceptual-documentation"></a>Documentación conceptual

- [Controladores de límites de sesión certificados para enrutamiento directo de Azure Communication Services](./certified-session-border-controllers.md)
- [Precios](../pricing.md)

### <a name="quickstarts"></a>Guías de inicio rápido

- [Llamada a teléfono](../../quickstarts/voice-video-calling/pstn-call.md)
