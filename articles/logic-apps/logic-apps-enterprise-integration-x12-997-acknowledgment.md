---
title: Códigos de error y confirmaciones 997 de X12
description: Obtenga información sobre las confirmaciones funcionales 997 y los códigos de error de los mensajes de X12 en Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: praveensri
ms.author: psrivas
ms.reviewer: estfan, divswa, azla
ms.topic: reference
ms.date: 07/15/2021
ms.openlocfilehash: ec727997663286b732fe7bf1d00a50da2f706a0a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481695"
---
# <a name="997-functional-acknowledgments-and-error-codes-for-x12-messages-in-azure-logic-apps"></a>Confirmaciones funcionales 997 y códigos de error de los mensajes X12 en Azure Logic Apps

En Azure Logic Apps, se pueden crear flujos de trabajo que controlen los mensajes X12 para la comunicación del intercambio electrónico de datos (EDI) al usar operaciones **X12**. En la mensajería EDI, las confirmaciones proporcionan el estado del procesamiento de un intercambio EDI. Al recibir un intercambio, la acción [**Descodificación de X12**](logic-apps-enterprise-integration-x12-decode.md) puede devolver uno o varios tipos de confirmaciones al emisor, en función de los tipos de confirmación que estén habilitados y el nivel de validación especificado.

Por ejemplo, el receptor notifica el estado de la validación del encabezado de grupo funcional (GS) y el finalizador de grupo funcional (GE) en el mensaje con código X12 recibido mediante el envío de una *confirmación funcional (ACK) 997*, junto con cada error que se produce durante el procesamiento. La acción **Descodificación de X12** siempre genera confirmaciones ACK 997 compatibles con 4010, mientras que las acciones [**Codificación de X12**](logic-apps-enterprise-integration-x12-encode.md) y **Descodificación de X12** pueden validar confirmaciones ACK 997 compatibles con 5010.

El receptor envía confirmaciones ACK 997 dentro de un sobre de encabezado de grupo funcional (GS) y finalizador de grupo funcional (GE). Sin embargo, este sobre no es diferente de que el de cualquier otro conjunto de transacciones.

En este tema se proporciona una breve información general sobre las confirmaciones ACK 997 de X12, incluidos los segmentos ACK 997 de un intercambio y los códigos de error que se usan en esos segmentos. Para obtener información relacionada, consulte la siguiente documentación:

* [Códigos de error y confirmaciones técnicas TA1 de X12](logic-apps-enterprise-integration-x12-ta1-acknowledgment.md)
* [Intercambio de mensajes X12 para la integración empresarial B2B](logic-apps-enterprise-integration-x12.md)
* [Intercambio de mensajes EDIFACT para la integración empresarial B2B](logic-apps-enterprise-integration-edifact.md)
* [¿Qué es Azure Logic Apps?](logic-apps-overview.md)
* [Soluciones de integración empresarial B2B con Azure Logic Apps y Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

<a name="997-ack-segments"></a>

## <a name="997-ack-segments"></a>Segmentos ACK 997

En la tabla siguiente se describen los segmentos ACK 997 de un intercambio y se utilizan las definiciones siguientes:

* M = Obligatorio
* O = Opcional

| Posición | Id. de segmento | Nombre | Designación necesaria <br>(Req. Des.) | Uso máximo | Repetición de bucle |
|----------|------------|------|--------------------------------------|-------------|-------------|
| 010 | ST | Encabezado del conjunto de transacciones, para la confirmación | M | 1 | - |
| 020 | AK1 | Encabezado de respuesta de grupo funcional | M | 1 | - |
| 030 | AK2 | Encabezado de respuesta de conjunto de transacciones | O | 1 | 999999 <br>(Id. de bucle = AK2) |
| 040 | AK3 | Nota del segmento de datos | O | 1 | 999999 <br>(Id. de bucle = AK2 o AK3) |
| 050 | AK4 | Nota del elemento de datos| O | 9 9 | - |
| 060 | AK5 | Finalizador de respuesta de conjunto de transacciones | M | 1 | - |
| 070 | AK9 | Finalizador de respuesta de grupo funcional | M | 1 | - |
| 080 | SE | Finalizador del conjunto de transacciones, para la confirmación | M | 1 | - |
|||||||

En las secciones siguientes se proporciona más información sobre cada uno de los segmentos AK. En el bucle AK2 a AK5, los segmentos proporcionan información acerca de un error con un conjunto de transacciones.

### <a name="ak1"></a>AK1

El segmento AK1 obligatorio identifica el grupo funcional que se va a confirmar mediante los siguientes elementos de datos:

| Elemento | Descripción |
|---------|-------------|
| AK101 | Obligatorio, identifica el identificador de grupo funcional (GS01) que el grupo funcional debe confirmar. |
| AK102 | Obligatorio, identifica el número de control del grupo (GS06 y GE02) que el grupo funcional debe confirmar. |
| AK103 | Opcional, identifica la versión de implementación de EDI enviada en el GS08 de la transacción original. AK103 admite una confirmación de entrada ACK 997 compatible con 5010. |
|||

### <a name="ak2"></a>AK2

El segmento AK2 opcional contiene una confirmación de un conjunto de transacciones en el grupo funcional recibido. Si existen varios segmentos AK2, se envían como una serie de bucles. Cada bucle de AK2 identifica un conjunto de transacciones según el orden de recepción. Si un conjunto de transacciones genera un error, un bucle de AK2 contendrá segmentos AK3, AK4 y AK5. Para obtener más información, examine las descripciones de los segmentos en este mismo tema.

El segmento AK2 identifica el conjunto de transacciones mediante los siguientes elementos de datos:

| Elemento | Descripción |
|---------|-------------|
| AK201 | Obligatorio, especifica el identificador del conjunto de transacciones (ST01) que se debe confirmar. |
| AK202 | Obligatorio, identifica el número de control (ST02 y SE02) del conjunto de transacciones que se debe confirmar. |
| AK203 | Opcional, identifica la versión de implementación de EDI enviada en el ST03 de la transacción original. AK203 admite confirmaciones de entrada 997 compatibles con 5010. |
|||

#### <a name="generate-ak2-segments"></a>Generación de segmentos AK2

Puede especificar que se generen segmentos AK2 para *todos* los conjuntos de transacciones, aceptados y rechazados, o bien *solo* para los conjuntos de transacciones rechazados. En caso contrario, Azure Logic Apps generará bucles AK2 *solo* para los conjuntos de transacciones rechazados. Si no se resuelve un acuerdo para el intercambio al que se está respondiendo, la configuración de la generación de 997 toma de forma predeterminada el valor de la configuración del acuerdo de reserva y no se generan segmentos AK2 para los conjuntos de transacciones aceptados.

Para que Azure Logic Apps genere segmentos AK2 para los conjuntos de transacciones aceptados donde AK501 == A, siga estos pasos:

1. En Azure Portal, abra su cuenta de integración y, después, abra el artefacto del contrato X12 entre sus entidades X12.

1. Abra el panel **Configuración de recepción** y asegúrese de que **FA esperada** aparece seleccionada. Luego, puede seleccionar **Incluir bucle AK2/IK2**.

### <a name="ak3"></a>AK3

El segmento AK3 opcional genera informes de errores en un segmento de datos e identifica la ubicación del segmento de datos. Se crea un segmento AK3 para cada segmento de un conjunto de transacciones que tenga uno o varios errores. Si existen varios segmentos AK3, se envían como una serie de bucles con un segmento por bucle. El segmento AK3 especifica la ubicación de los segmentos que contienen errores y genera informes del tipo de error sintáctico encontrado en dicha ubicación mediante los siguientes elementos de datos:

| Elemento | Descripción |
|---------|-------------|
| AK301 | Obligatorio, identifica el segmento que contiene el error con el Id. de segmento X12, por ejemplo, NM1. |
| AK302 | Obligatorio, identifica el número de segmentos del segmento que contiene el error. El segmento ST es `1` y cada uno de los segmentos aumenta el número de segmentos en uno. |
| AK303 | Obligatorio, identifica un bucle delimitado, que es un bucle rodeado por un segmento de inicio de bucle (LS) y un segmento de fin de bucle (LE). AK303 contiene los valores de los segmentos LS y LE que enlazan el segmento que contiene el error. |
| AK304 | Opcional, especifica el código del error del segmento de datos. Aunque AK304 es opcional, el elemento es necesario cuando existe un error en el segmento identificado. En el caso de los códigos de error de AK304, revise [997 ACK error codes - Data Segment Note](#997-ack-error-codes) (Códigos de error ACK 997: nota del segmento de datos). |
|||

### <a name="ak4"></a>AK4

El segmento AK4 opcional informa de errores en un elemento de datos o en la estructura de datos compuesta e identifica la ubicación del elemento de datos. Cuando el elemento de datos AK304 es `"8", "Segment has data element errors"` se envía un segmento AK4 y puede repetirse hasta 99 veces dentro de cada segmento de AK3. El segmento AK4 especifica la ubicación de los elementos de datos o la estructura de datos compuesta que contiene errores e informa del tipo de error sintáctico encontrado en dicha ubicación mediante los siguientes elementos de datos:

| Elemento | Descripción |
|---------|-------------|
| AK401 | Obligatorio, un elemento de datos compuesto con los siguientes campos: AK41.1, AK41.2 y AK41.3 <p><p>- AK401-1: identifica el elemento de datos o la estructura de datos compuesta que contienen un error mediante su recuento numérico. Por ejemplo, si el segundo elemento de datos del segmento tiene un error, AK401 es igual a `2`. <br>AK401-2: identifica el recuento numérico del elemento de datos del componente de una estructura de datos compuesta que tiene un error. Si AK401 informa de un error en una estructura de datos que no es compuesta, no se ha especificado valor para AK401.2. <br>- AK41.3: opcional, este campo es la posición del elemento de datos que se repite. AK41.3 admite 5010 de entrada compatible con 997. |
| AK402 | Opcional, identifica el número simple del elemento de datos X12 del elemento que contiene el error. Por ejemplo, NM101 es el número 98 del elemento de datos X12 simple. |
| AK403 | Obligatorio, informa del error del elemento identificado. En el caso de los códigos de error de AK403, revise [997 ACK error codes - Data Element Note](#997-ack-error-codes) (Códigos de error ACK 997: nota del elemento de datos). |
| AK404 | Opcional, contiene una copia de elemento de datos identificado que contiene el error. AK404 no se utiliza si el error indica un carácter no válido. |
|||

### <a name="ak5"></a>AK5

El segmento AK5 indica si el conjunto de transacciones identificado en el segmento AK2 se identifica o se rechaza y el motivo. El segmento AK5 es obligatorio cuando el bucle AK2 opcional se incluye en la confirmación. El segmento AK4 especifica el estado del conjunto de transacciones mediante un único elemento de datos obligatorio y proporciona códigos de error mediante entre uno y cinco elementos de datos opcionales, en función en la sintaxis que edita el conjunto de transacciones.

| Elemento | Descripción |
|---------|-------------|
| AK501 | Obligatorio, especifica si se acepta o rechaza el conjunto de transacciones identificado. En el caso de los códigos de error de AK501, consulte [997 ACK error codes - Transaction Response Trailer](#997-ack-error-codes) (Códigos de error ACK 997: finalizador de respuesta de transacción). |
| AK502 - AK506 | Opcional, indique la naturaleza del error. En el caso de los códigos de error de AK502, consulte [997 ACK error codes - Transaction Set Response Trailer](#997-ack-error-codes) (Códigos de error ACK 997: finalizador de respuesta del conjunto de transacciones). |
|||

### <a name="ak9"></a>AK9

El segmento AK9 obligatorio indica si el grupo funcional identificado en el segmento AK1 se acepta o se rechaza y el motivo. El segmento AK9 especifica el estado del conjunto de transacciones y la naturaleza de cualquier error mediante cuatro elementos de datos obligatorios. El segmento especifica los errores anotados mediante el uso de entre uno y cinco elementos opcionales.

| Elemento | Descripción |
|---------|-------------|
| AK901 | Obligatorio, especifica si se acepta o se rechaza el grupo funcional identificado en AK1. En el caso de los códigos de error de AK901, consulte [997 ACK error codes - Functional Group Response Trailer (Códigos de error ACK 997: finalizador de respuesta de grupo funcional)](#997-ack-error-codes). |
| AK902 | Obligatorio, especifica el número de conjuntos de transacciones incluido en el finalizador del grupo funcional identificado (GE01). |
| AK903 | Obligatorio, especifica el número de conjuntos de transacciones recibidos. |
| AK904 | Obligatorio, especifica el número de conjuntos de transacciones aceptado en el grupo funcional identificado. |
| AK905 - AK909 | Opcional, indica de uno a cinco errores anotados en el grupo funcional identificado. En el caso de los códigos de error de AK905 a AK909, consulte [997 ACK error codes - Functional Group Response Trailer (Códigos de error ACK 997: finalizador de respuesta de grupo funcional)](#997-ack-error-codes). |
|||

<a name="997-ack-error-codes"></a>

## <a name="997-ack-error-codes"></a>Códigos de error de ACK 997

En esta sección se tratan los códigos de error que se usan en los [segmentos ACK 997](#997-ack-segments).  En cada tabla se enumeran los códigos de error admitidos y no admitidos, tal como se define en la especificación X12, para el procesamiento de mensajes X12 en Azure Logic Apps.

### <a name="ak304-error-codes---data-segment-note"></a>Códigos de error de AK304: nota del segmento de datos

La tabla siguiente muestra los códigos de error que se utilizan en el elemento de datos AK304 del segmento AK3 (nota del segmento de datos):

| Código de error | Condición | ¿Compatible? |
|------------|-----------|------------|
| 1 | Id. de segmento no reconocido. | Sí |
| 2 | Segmento inesperado. | Sí |
| 3 | Falta segmento obligatorio | Sí |
| 4 | Repetición de bucles superior al máximo permitido | Sí |
| 5 | El segmento excede la descripción de uso máximo | Sí |
| 6 | Segmento no definido en el conjunto de transacciones | Sí |
| 7 | El segmento no tiene el orden adecuado | Sí |
| 8 | El segmento contiene errores de elementos de datos | Sí |
| 511 | Se han encontrado separadores finales (código personalizado) | Sí |
||||

### <a name="ak403-error-codes----data-element-note"></a>Códigos de error de AK403: nota del elemento de datos

En la tabla siguiente se enumeran los códigos de error que se utilizan en el elemento de datos AK403 del segmento AK4 (nota del elemento de datos):

| Código de error | Condición | ¿Compatible? |
|------------|-----------|------------|
| 1 | Falta elemento de datos obligatorio. | Sí |
| 2 | Falta un elemento de datos requerido condicional. | Sí |
| 3 | Demasiados elementos de datos. | Sí |
| 4 | El elemento de datos es demasiado corto. | Sí |
| 5 | El elemento de datos es demasiado largo. | Sí |
| 6 | Carácter no válido en el elemento de datos. | Sí |
| 7 | Valor de código no válido | Sí |
| 8 | Fecha no válida | Sí |
| 9 | Hora no válida | Sí |
| 10 | Infracción de la condición de exclusión | Sí |
||||

### <a name="ak501-error-codes---transaction-set-response-trailer"></a>Códigos de error de AK501: finalizador de respuesta del conjunto de transacciones

En la tabla siguiente se enumeran los códigos de error que se utilizan en el elemento de datos AK501 del segmento AK5 (finalizador de respuesta del conjunto de transacciones):

| Código de error | Condición | ¿Compatible? |
|------------|-----------|------------|
| A | Aceptado | Sí |
| E | Se acepta, pero se registraron los errores. | Sí <p><p>**Nota**: ninguno de los códigos de error conduce al estado `E`. |
| M | Rechazado. Error del código de autenticación del mensaje (MAC). | No |
| P | Parcialmente aceptado, pero al menos un conjunto de transacciones se rechazó. | Sí |
| R | Rechazada | Sí |
| W | Rechazado. La seguridad no superó las pruebas de validez. | No |
| X | Rechazado. No se pudo analizar el contenido tras el descifrado. | No |
||||

### <a name="ak502-to-ak506-error-codes---transaction-set-response-trailer"></a>Códigos de error de AK502 a AK506: finalizador de respuesta del conjunto de transacciones

La tabla siguiente muestra los códigos de error que se utilizan en los elementos de datos de AK502 a AK506 del segmento AK5 (finalizador de respuesta del conjunto de transacciones):

| Código de error | Condición | ¿Compatible o <br>correlacionado con AK501? |
|------------|-----------|-----------------------------------------|
| 1 | Conjunto de transacciones no admitido | Sí, R |
| 2 | Falta el finalizador de conjunto de transacciones. | Sí, R |
| 3 | El número de control de conjunto de transacciones del encabezado y del finalizador no coincide. | Sí, R |
| 4 | El número de segmentos incluidos no coincide con el recuento real. | Sí, R |
| 5 | Error en uno o más segmentos. | Sí, R |
| 6 | El identificador del conjunto de transacciones falta o no es válido. | Sí, R |
| 7 | El número de control del conjunto de transacciones falta o no es válido, puede haberse producido la duplicación de un número de transacción. | Sí, R |
| 8 a 27 | - | No |
||||

### <a name="ak901-error-codes---functional-group-response-trailer"></a>Códigos de error de AK901: finalizador de respuesta de grupo funcional

En la tabla siguiente se enumeran los códigos de error que se utilizan en los elementos de datos AK901 del segmento AK9 (finalizador de respuesta del grupo funcional):

| Código de error | Condición | ¿Compatible o <br>correlacionado con AK501? |
|------------|-----------|-----------------------------------------|
| A | Aceptado | Sí |
| E | Se acepta, pero se registraron los errores. | Sí |
| M | Rechazado. Error del código de autenticación del mensaje (MAC). | No |
| P | Parcialmente aceptado, pero al menos un conjunto de transacciones se rechazó. | Sí |
| R | Rechazada | Sí |
| W | Rechazado. La seguridad no superó las pruebas de validez. | No |
| X | Rechazado. No se pudo analizar el contenido tras el descifrado. | No |
||||

### <a name="ak905-to-ak909-error-codes---functional-group-response-trailer"></a>Códigos de error de AK905 a AK909: finalizador de respuesta de grupo funcional

En la tabla siguiente se enumeran los códigos de error que se utilizan en los elementos de datos de AK905 a AK909 del segmento AK9 (finalizador de respuesta del grupo funcional):

| Código de error | Condición | ¿Compatible o <br>correlacionado con AK501? |
|------------|-----------|-----------------------------------------|
| 1 | Grupo funcional no compatible | No |
| 2 | Versión de grupo funcional no compatible | No |
| 3 | Falta el finalizador del grupo funcional. | Sí |
| 4 | El número de control de grupo del encabezado y el finalizador del grupo funcional no coinciden. | Sí |
| 5 | El número de conjuntos de transacciones incluidos no coincide con el recuento real. | Sí |
| 6 | El número de control de grupo infringe la sintaxis, puede haberse producido la duplicación de un número de control de grupo. | Sí |
| 7 a 26 | - | No |
||||

## <a name="next-steps"></a>Pasos siguientes

* [Intercambio de mensajes X12 para la integración empresarial B2B](logic-apps-enterprise-integration-x12.md)