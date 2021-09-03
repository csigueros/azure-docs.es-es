---
title: Códigos de error y confirmaciones CONTRL de EDIFACT
description: Obtenga información sobre las confirmaciones CONTRL y los códigos de error para los mensajes EDIFACT en Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: praveensri
ms.author: psrivas
ms.reviewer: estfan, divswa, azla
ms.topic: reference
ms.date: 07/25/2021
ms.openlocfilehash: 3d17edf35b8da5a2d8b158fdbf1bd6e5d13e627b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780190"
---
# <a name="contrl-acknowledgments-and-error-codes-for-edifact-messages-in-azure-logic-apps"></a>Confirmaciones CONTRL y códigos de error para los mensajes EDIFACT en Azure Logic Apps

En Azure Logic Apps, puede crear flujos de trabajo que controlen los mensajes EDIFACT para la comunicación del intercambio electrónico de datos (EDI) al usar las operaciones **EDIFACT**. En la mensajería EDI, las confirmaciones proporcionan el estado del procesamiento de un intercambio EDI. Al recibir un intercambio, la [acción **Descodificación EDIFACT Decode**](logic-apps-enterprise-integration-edifact-decode.md) puede devolver uno o varios tipos de confirmaciones al emisor en función de los tipos de confirmación que estén habilitados y el nivel de validación especificado.

En este tema se proporciona información general breve sobre la confirmación CONTRL de EDIFACT, los segmentos de confirmación CONTRL en un intercambio y los códigos de error que se utilizan en esos segmentos.

## <a name="contrl-as-technical-and-functional-acknowledgment"></a>CONTRL como confirmación técnica y funcional

Para los mensajes con codificación EDIFACT, la confirmación CONTRL (ACK) sirve de confirmación técnica *y* confirmación funcional. El mensaje CONTRL completo sirve como confirmación funcional, mientras que las secciones de la confirmación funcional se vuelven a utilizar para la confirmación técnica. Por ejemplo, si selecciona confirmaciones tanto técnicas como funcionales en las propiedades del acuerdo para el asociado emisor o en las propiedades globales, la acción **Descodificación EDIFACT** genera dos mensajes CONTRL, una confirmación técnica de CONTRL y una confirmación funcional de CONTRL. La confirmación CONTRL ACK cumple con el esquema para **EFACT_<*número-de-versión*>_CONTRL.xsd**.

> [!NOTE]
> Con el procesamiento de mensajes EDIFACT en Azure Logic Apps, un mensaje CONTRL, ya sea para confirmación de recepción, aceptación o rechazo, se envía como respuesta a un intercambio recibido que contiene solo uno o varios mensajes CONTRL. En Logic Apps, no se envía ningún mensaje CONTRL como respuesta a un intercambio recibido que contiene solo uno o varios mensajes CONTRL. 
>
> Debe informarse de los errores en mensajes CONTRL recibidos por otros medios distintos a un mensaje CONTRL. Si uno o más mensajes CONTRL se incluyen en un intercambio que contiene mensajes de datos, el mensaje CONTRL generado como respuesta a ese intercambio se genera como si no hubiera mensajes CONTRL en el intercambio recibido.

Como confirmación técnica, el mensaje CONTRL indica que el destinatario del intercambio recibió el intercambio en cuestión más lo siguiente:

* Partes comprobadas del intercambio a fin de confirmar la precisión sintáctica de los elementos de datos copiados en el segmento Respuesta del intercambio (UCI) de los informes.
* Acepta la responsabilidad de notificar al emisor la aceptación o el rechazo de las demás partes del intercambio.
* Toma medidas razonables para notificar al emisor.

> [!NOTE]
> Una confirmación técnica de CONTRL informa del estado `Rejected` solo cuando el mensaje EDIFACT entrante es un duplicado o si existen errores en el sobre,como un problema con el juego de caracteres. EDIFACT no informa del estado `Interchange accepted with errors` en una confirmación técnica de CONTRL, porque x12 utiliza el campo TA104 en una confirmación TA1. Si se acepta parte del mensaje EDIFACT, la confirmación técnica CONTRL informa de un estado `Accepted`. En algunos escenarios, si se rechaza parte del mensaje, la confirmación de CONTRL de todos modos informa del estado `Accepted`. En tales escenarios, el elemento UCI5 puede informar del error.

Como confirmación funcional, el mensaje CONTRL informa del estado, como aceptación o rechazo, del intercambio, el grupo o el mensaje recibido, incluido cualquier error o funcionalidad no admitida. El mensaje también indica que el destinatario del intercambio:

* Recibió los niveles del intercambio confirmado a los que se hace referencia.
* Comprobó que no hay ningún error sintáctico grave en el nivel confirmado al que se hace referencia que evite un procesamiento posterior del intercambio.
* Comprobó que las partes confirmadas de los segmentos de servicio son semánticamente correctas (si no se informa de ningún error).
* Cumple con las acciones solicitadas en los niveles a los que se hace referencia y confirmados de los segmentos de servicio.
* Aceptó la responsabilidad de notificar al emisor por medios distintos del envío de un mensaje CONTRL en los casos siguientes:

  * Si se encuentran errores sintácticos o semánticos más adelante en la parte correspondiente.
  * Si no se puede procesar la parte por algún otro motivo una vez que se confirma en un mensaje CONTRL enviado.

* Toma medidas razonables para asegurarse de que se encuentren los errores de este tipo y para notificar al emisor.

El rechazo implica que el destinatario del intercambio:

* No puede confirmar el intercambio ni ninguna parte relevante por los motivos indicados en el mensaje CONTRL.
* No emprenderá ninguna acción adicional sobre la información empresarial contenida en la parte rechazada del intercambio en cuestión.

## <a name="contrl-technical-ack-segments"></a>Segmentos de confirmación técnica de CONTRL

Si selecciona la opción de generar una confirmación técnica en un acuerdo EDIFACT, o si el campo de mensaje **UNB9** está establecido en `2`, se genera un mensaje CONTRL como confirmación técnica para informar de los resultados de la recepción del intercambio.

En la tabla siguiente se describen los segmentos de confirmación técnica de CONTRL en un intercambio y se utilizan las definiciones siguientes:

* M = Obligatorio
* O = Opcional

| Nombre | Obligatorio u opcional | Descripción |
|------|-----------------------|-------------|
| Encabezado del mensaje UNH | M | El segmento de encabezado del mensaje (UNH) identifica y especifica el mensaje. |
| Respuesta de intercambio UCI | M | Identifica el intercambio e indica la naturaleza de la recepción del intercambio. El segmento UCI tiene una repetición máxima de `1` y, como resultado, informa del primer error que se encuentra en un segmento de control. |
| Finalizador del mensaje UNT | M | Se informa de un error en el elemento de datos UCI5 denominado `Syntax Error Code`. En el caso de los mensajes con codificación EDIFACT, no existe la condición `Accepted with errors`, como ocurre con los intercambios con codificación X12. |
||||

### <a name="contrl-technical-ack-data-elements"></a>Elementos de datos de confirmación técnica CONTRL

La confirmación técnica CONTRL incluye los siguientes elementos de datos:

| Elemento | Nombre | Uso |
|---------|------|-------|
| UNH1 | Número de referencia de mensaje | - |
| UNH2 | Subcomponentes del identificador de mensaje | Subcomponentes: <p><p>- 1 = CONTRL <br>- 2 = 4 <br>- 3 = 1 <br>- 4 = UN |
| UCI1 | Número de control de intercambio. | Asignado desde el campo UNB5 del mensaje recibido. |
| UCI2 | Remitente de intercambio | Asignado desde el campo UNB2 del mensaje recibido. <p><p>- Obligatorio: primer subcomponente o identificación. <br>- Opcional: segundo subcomponente, o calificador de código, y tercer componente, o dirección de enrutamiento inverso. |
| UCI3 | Destinatario de intercambio | Asignado desde el campo UNB3 del mensaje recibido. <p><p>- Obligatorio: primer subcomponente o identificación. <br>- Opcional: segundo componente, o calificador de código. |
| UCI4 | Código de acción | Este elemento es obligatorio. Códigos de acción: <p><p>- 8, si se acepta el intercambio. <br>- 7, si se acepta el intercambio pero se rechazan algunos conjuntos de transacciones. <br>- 4, si se rechaza el intercambio debido a un error en el segmento UNA o UNB. |
| UCI5 | Código de error de sintaxis | Este elemento es condicionalmente opcional e identifica la condición de error, si existe. |
| UCI6 | Etiqueta de segmento de servicio | Este elemento es condicionalmente opcional e identifica el segmento que tiene la condición de error en el elemento de datos UCI5. |
| UCI7 | Identificación de elementos de datos | Identifica los elementos de datos que presentan la condición de error en el elemento de datos UCI5. Subcomponentes: <p><p>- Obligatorio: posición de elemento de datos erróneo en el segmento. <br>- Condicionalmente opcional: posición del elemento de datos de componente erróneo en el segmento y aparición de un elemento de datos erróneo en el segmento. |
| UCI8 | - | - |
| UNT1 | Recuento de segmentos | - |
| UNT2 | Número de referencia de mensaje | - |
||||

## <a name="contrl-functional-ack-segments"></a>Segmentos de confirmación funcional CONTRL

Si selecciona la opción de generar una confirmación funcional en un acuerdo EDIFACT, o si el campo de mensaje **UNB9** está establecido en `1`, se genera un mensaje CONTRL como confirmación funcional para informar de los resultados de la comprobación de la sintaxis del intercambio.

En la tabla siguiente se describen los segmentos de confirmación funcional de CONTRL en un intercambio y se utilizan las definiciones siguientes:

* M = Obligatorio
* O = Opcional

| Nombre | Obligatorio u opcional | Descripción |
|------|-----------------------|-------------|
| Encabezado del mensaje UNH | M | El segmento de encabezado del mensaje (UNH) identifica y especifica el mensaje. |
| Segmento UCI | M | Identifica el intercambio, indica el estado de la recepción del intercambio y contiene referencias a los segmentos UNA, UNB y UNZ del intercambio recibido. El segmento UCI tiene una repetición máxima de `1` y, como resultado, informa del primer error que se encuentra en un segmento de control. |
| Segmento UCF | M, si existe el segmento UNG | Identifica un segmento de grupo, que es encapsulado por el encabezado UNG y el finalizador UNE, e indica la naturaleza de los errores. |
| Segmento UCM | M | Identifica un segmento de mensaje, que es encapsulado por el encabezado UNH y el finalizador UNT, e indica la naturaleza de los errores. |
| Segmento UCS | M | Identifica un conjunto de transacciones e indica la naturaleza de cualquier error. |
| Segmento UCD | O, de manera condicional | Identifica un elemento de datos de componentes o compuestos erróneo e indica la naturaleza del error. |
| Finalizador del mensaje UNT | M | Si una confirmación funcional CONTRL recibida solo contiene segmentos UNH, UCI y UNT, la canalización EDIReceive procesa la confirmación como una confirmación técnica de recepción CONTRL. Cada instancia de segmento en un nivel de informe puede informar de solo un error, por ejemplo, los segmentos UCI, UCF, UCM, UCS y UCD. |
||||

### <a name="sg-loops-and-contrl-functional-ack-structure"></a>Bucles SG y estructura de confirmación funcional CONTRL

En función de si el intercambio recibido incluye uno o varios grupos, la estructura de la confirmación funcional CONTRL varía.

* Si el intercambio incluye un grupo, la confirmación contiene un segmento UCF por grupo. Cada segmento UCF contiene un segmento UCM por mensaje. Cada segmento UCM también incluye una serie de segmentos UCS y UCD en conjunto.

  El formato XML del mensaje de confirmación incluye estos elementos de bucle:

  * Un elemento SG3Loop que encapsula cada segmento UCF.
  * Un elemento SG4Loop que encapsula cada elemento UCM.
  * Un elemento SG5Loop que encapsula cada par de elementos UCS y UCD.

  Las etiquetas de bucle SG no existen en el formato de mensaje EDI nativo.

* Si el intercambio no incluye un grupo, la confirmación no contiene ningún segmento UCF. En su lugar, la confirmación incluye un segmento UCM por mensaje. Cada segmento UCM incluye una serie de segmentos UCS y UCD en conjunto.

* El formato XML del mensaje de confirmación incluye estos elementos de bucle:

  * Un elemento SG1Loop que encapsula cada elemento UCM.
  * Un elemento SG2Loop que encapsula cada par de elementos UCS y UCD.

  Al igual que ocurre con los intercambios que incluyen grupos, las etiquetas SG no existen en el formato de confirmación nativo.

De manera predeterminada y debido al uso en el sector, no se espera que haya bucles SG1/SG4 en los conjuntos de transacciones aceptados. Sin embargo, y a fin de cumplir con los estándares, puede forzar la generación de bucles SG1/SG4 si sigue estos pasos:
 
1. En [Azure Portal](https://portal.azurecom), abra la cuenta de integración.
1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Acuerdos**.
1. Abra el acuerdo EDIFACT y seleccione **Configuración de envío**.
1. En **Confirmación**, seleccione **Confirmación (CONTRL)** . Ahora puede seleccionar **Generar bucle SG1/SG4 para conjuntos de transacciones aceptadas**.

   Si se activa esta casilla, la canalización de recepción genera bucles SG1/SG4, se acepte o no el conjunto de transacciones. De lo contrario, estos bucles solo se generan para conjuntos de transacciones erróneas donde UCM5 no es igual a `7`.

### <a name="contrl-functional-ack-data-elements"></a>Elementos de datos de confirmación funcional CONTRL

El mensaje CONTRL contiene varios elementos de datos obligatorios que se copian del intercambio recibido. Si falta el elemento de datos del intercambio o no es válido sintácticamente, no se puede generar un mensaje CONTRL que sea válido sintácticamente. Debe informarse del error por otros medios distintos a un mensaje CONTRL.

La confirmación funcional CONTRL incluye los siguientes elementos de datos:

| Elemento | Nombre | Uso |
|---------|------|-------|
| UNH1 | Número de referencia de mensaje | - |
| UNH2 | Subcomponentes del identificador de mensaje | Subcomponentes: <p><p>- 1 = CONTRL <br>- 2 = 4 <br>- 3 = 1 <br>- 4 = UN |
| UCI1 | Número de control de intercambio. | Asignado desde el campo UNB5 del mensaje recibido. |
| UCI2 | Remitente de intercambio | Asignado desde el campo UNB2 del mensaje recibido. <p><p>- Obligatorio: primer subcomponente o identificación. <br>- Opcional: segundo subcomponente, o calificador de código, y tercer componente, o dirección de enrutamiento inverso. |
| UCI3 | Destinatario de intercambio | Asignado desde el campo UNB3 del mensaje recibido. <p><p>- Obligatorio: primer subcomponente o identificación. <br>- Opcional: segundo componente, o calificador de código. |
| UCI4 | Código de acción | Este elemento es obligatorio. Códigos de acción: <p><p>- 8, si se acepta el intercambio. <br>- 7, si se acepta el intercambio pero se rechazan algunos conjuntos de transacciones. <br>- 4, si se rechaza el intercambio debido a un error en el segmento UNA o UNB. |
| UCI5 | Código de error de sintaxis | Este elemento es condicionalmente opcional e identifica la condición de error, si existe. |
| UCI6 | Etiqueta de segmento de servicio | Este elemento es condicionalmente opcional e identifica el segmento que tiene la condición de error en el elemento de datos UCI5. |
| UCI7 | Identificación de elementos de datos | Identifica los elementos de datos que presentan la condición de error en el elemento de datos UCI5. Subcomponentes: <p><p>- Obligatorio: posición de elemento de datos erróneo en el segmento. <br>- Condicionalmente opcional: posición del elemento de datos de componente erróneo en el segmento y aparición de un elemento de datos erróneo en el segmento. |
| UCI8 | - | - |
| UCF1 | Número de referencia de grupo | Este elemento es obligatorio y se asigna desde el campo UNG5 del mensaje recibido. |
| UCF2 | Identificación del emisor de la aplicación | Este elemento es condicionalmente opcional y se asigna desde el campo UNG2 del mensaje recibido junto con subcomponentes. |
| UCF3 | Identificación del destinatario de la aplicación | Este elemento es condicionalmente opcional y se asigna desde el campo UNG3 del mensaje recibido junto con subcomponentes. |
| UCF4 | Acción codificada | Este elemento es obligatorio y el código se aplica a este nivel y a todos los niveles inferiores. Códigos de acción: <p><p>- 7, si se acepta el intercambio. <br>- 4, si se rechaza el intercambio debido a un error en el segmento UNA o UNB. |
| UCF5 | Error de sintaxis, codificado | Este elemento es condicionalmente opcional e identifica la condición de error en el grupo, si existe. |
| UCF6 | Etiqueta de segmento de servicio | Este elemento es condicionalmente opcional e identifica el segmento erróneo en el grupo. |
| UCF7 | Identificación de elementos de datos | Identifica los elementos de datos que presentan la condición de error identificada en el elemento de datos UCF5. Subcomponentes: <p><p>- Obligatorio: posición del elemento de datos erróneo en el segmento y aparición de un elemento de datos erróneo en el segmento. <br>- Condicionalmente opcional: posición de elemento de datos de componentes erróneo en el segmento. |
| UCM1 | Número de referencia de mensaje| Este elemento es obligatorio y se asigna desde el campo UNH1 del mensaje recibido. |
| UCM2 | Identificador de mensaje | Este elemento es condicionalmente opcional y se asigna desde el campo UNH2 del mensaje recibido junto con subcomponentes. |
| UCM3 | Acción codificada | Este elemento es obligatorio y el código se aplica a este nivel y a todos los niveles inferiores. Códigos de acción: <p><p>- 7, si se acepta el intercambio. <br>- 4, si se rechaza el intercambio debido a un error en el segmento UNA o UNB. |
| UCM4 | Error de sintaxis, codificado | Este elemento es condicionalmente opcional e identifica la condición de error en el grupo, si existe. |
| UCM5 | Etiqueta de segmento de servicio | Este elemento es condicionalmente opcional e identifica el segmento UNH o UNT con error. |
| UCM7 | Identificación de elementos de datos | Identifica los elementos de datos que presentan la condición de error identificada en el elemento de datos UCM5. Subcomponentes: <p><p>- Obligatorio: posición del elemento de datos erróneo en el segmento y aparición de un elemento de datos erróneo en el segmento. <br>- Condicionalmente opcional: posición de elemento de datos de componentes erróneo en el segmento. |
| UCS1 | Posición del segmento en el cuerpo del mensaje | Este elemento es obligatorio y es el recuento de posiciones del segmento erróneo, empezando con UNH como `1`. Para informar de la falta de un segmento, este valor es la posición de recuento numérico del último segmento que se procesó antes de la posición para la que se espera que exista el segmento que falta. Se indica que falta un grupo de segmentos mediante la identificación de que falta el primer segmento del grupo. |
| UCS2 | Error de sintaxis codificado | Este elemento es condicionalmente opcional e identifica la condición de error en el grupo, si existe. |
| UCD1 | Error de sintaxis codificado| Este elemento es condicionalmente opcional e identifica la condición de error en el grupo, si existe. <p><p>**Nota**: Si se produce un error de validación XSD, el elemento de datos UCD1 informa de un valor de código de `12, Invalid Value`. |
| UCD2 | Identificación de elementos de datos | Identifica los elementos de datos que presentan la condición de error identificada en el elemento de datos UCD1. Subcomponentes: <p><p>- Obligatorio: posición del elemento de datos erróneo en el segmento y aparición de un elemento de datos erróneo en el segmento. <br>- Condicionalmente opcional: posición de elemento de datos de componentes erróneo en el segmento. |
| UNT1 | Recuento de segmentos | - |
| UNT2 | Número de referencia de mensaje | - |
||||

## <a name="contrl-ack-error-codes"></a>Códigos de error de confirmación CONTRL

Estos errores se aplican en el nivel de datos, mensajes, grupos e intercambios. Cuando se encuentra un error admitido, se rechaza el conjunto de transacciones, el grupo o el intercambio completo. Los intercambios con codificación EDIFACT no tienen una condición `Accepted with errors` como sí la tienen los mensajes con codificación X12.

### <a name="standard-edifact-contrl-ack-error-codes"></a>Códigos de error de confirmación CONTRL de EDIFACT estándar

En la tabla siguiente se enumeran los códigos de error admitidos según la definición de la especificación EDIFACT que se usan en el campo UCI5 de la confirmación CONTRL para el procesamiento de mensajes EDIFACT en Azure Logic Apps.

| Código de error | Condición | Causa | ¿Compatible? |
|------------|-----------|-------|------------|
| 2 | Nivel o versión de sintaxis no admitidos | Notificación de que el destinatario no admite el nivel o la versión de la sintaxis. | No |
| 7 | El destinatario del intercambio no es el destinatario real | Notificación de que el destinatario del intercambio (S003) difiere del destinatario real. | No |
| 12 | Valor no válido | Notificación de que el valor de un elemento de datos independiente, de un elemento de datos compuesto o de un elemento de datos de componentes no cumple las especificaciones relevantes del valor. | Sí |
| 13 | Missing | Notificación de que falta un segmento de usuario o servicio obligatorio o necesario, un elemento de datos, un elemento de datos compuesto o un elemento de datos de componentes. | Sí |
| 14 | Valor no admitido en esta posición | Notificación de que el destinatario no admite el empleo del valor específico de un elemento de datos independiente, de un elemento de datos compuesto o de un elemento de datos de componentes identificado en la posición en la que se utiliza. El valor puede ser válido según las especificaciones relevantes, y puede admitirse si se utiliza en otra posición. | No |
| 15 | No admitido en esta posición | Notificación de que el destinatario no admite el empleo del tipo de segmento, del tipo de elemento de datos independiente, del tipo de elemento de datos compuesto o del tipo de elemento de datos de componentes en la posición identificada. | Sí |
| 16 | Demasiados constituyentes | Notificación de que el segmento identificado contiene demasiados elementos de datos o de que el elemento de datos compuesto identificado contiene demasiados elementos de datos de componentes. | Sí |
| 17 | No hay acuerdo | No existe acuerdo que permita la recepción de un intercambio, grupo, mensaje o paquete con el valor del elemento de datos independiente, del elemento de datos compuesto o del elemento de datos de componentes identificado. | No |
| 18 | Error no especificado | Notificación de que se ha identificado un error, pero no se ha determinado su naturaleza. | No |
| 19 | Notación decimal no válida | Notificación de que el carácter indicado como notación decimal en UNA no es válido, o la notación decimal utilizada en un elemento de datos no es coherente con la indicada en UNA. | No |
| 20 | Carácter no válido como carácter de servicio | Notificación de que un carácter recomendado en UNA no es válido como carácter de servicio. | No |
| 21 | Carácter(es) no válido(s) | Notificación de que uno o varios de los caracteres utilizados en el intercambio no son válidos, como se muestra en el identificador sintáctico indicado en el segmento UNB. El carácter no válido forma parte del nivel al que se hace referencia o aparece inmediatamente después de la parte identificada del intercambio. | Sí |
| 22 | Caracteres de servicio no válidos | Notificación de que los caracteres de servicio utilizados en el intercambio no son caracteres de servicio válidos, como se recomienda en el segmento UNA, o bien no se trata de uno de los caracteres de servicio predeterminados. Si el código se emplea en el segmento UCS o UCD, el carácter no válido aparece inmediatamente después de la parte identificada del intercambio. | No |
| 23 | Remitente de intercambio desconocido | Notificación de que el remitente del intercambio (S002) es desconocido. | No |
| 24 | Demasiado antiguo | Notificación de que el grupo o el intercambio recibidos son más antiguos que un límite especificado en IA o determinado por el destinatario. | No |
| 25 | Indicador de prueba no admitido | Notificación de que el procesamiento de prueba no puede efectuarse para el intercambio, grupo, mensaje o paquete identificado. | No |
| 26 | Duplicado detectado | Notificación de que se ha detectado un posible duplicado de un intercambio, grupo, mensaje o paquete recibido con anterioridad. Puede que se haya rechazado la transmisión anterior. | Sí |
| 27 | Función de seguridad no admitida | Notificación de que no se admite una función de seguridad relacionada con el elemento de datos o el nivel al que se hace referencia. | No |
| 28 | Las referencias no coinciden | Notificación de que la referencia de control del segmento UNB, UNG, UNH, UNO, USH o USD no coincide con la referencia de control del segmento UNZ, UNE, UNT, UNP, UST o USU, respectivamente. | No |
| 29 | La cuenta de control no coincide con el número de instancias recibidas | Notificación de que el número de grupos, mensajes o segmentos no coincide con el número especificado en el segmento UNZ, UNE, UNT o UST. O bien, la longitud de un objeto o la longitud de los datos cifrados no es igual a la longitud indicada en el segmento UNO, UNP, USD o USU. | Sí |
| 30 | Grupos y mensajes o paquetes mezclados | Notificación de que se han mezclado los grupos y los mensajes o paquetes exteriores a los grupos en el intercambio. | No |
| 31 | Más de un tipo de mensaje en el grupo | Notificación de que existen distintos tipos de mensaje en un grupo funcional. | Sí |
| 32 | Nivel inferior vacío | Notificación de una de las condiciones siguientes: <p><p>- El intercambio no contiene ningún mensaje, paquete o grupo. <br>- Un grupo no contiene ningún mensaje o paquete. | No |
| 33 | Suceso no válido fuera del mensaje, paquete o grupo|Notificación de que el intercambio contiene un segmento o elemento de datos no válido entre mensajes, paquetes o grupos. El rechazo se comunica en el nivel superior. | Sí |
| 34 | Indicador de anidamiento no admitido | Notificación de que se utilizó el anidamiento explícito en un mensaje en el que no estaba permitido. | No |
| 35 | Demasiados elementos de datos o repeticiones de segmentos | Notificación de que un elemento de datos independiente, un elemento de datos compuesto o un segmento se repite en demasiadas ocasiones. | Sí |
| 36 | Demasiadas repeticiones de grupos de segmentos | Notificación de que se ha repetido demasiado un grupo de segmentos. | Sí |
| 37 | Tipo de carácter(es) no válido(s) | Notificación de una de las condiciones siguientes: <p><p>- Se usan uno o varios caracteres numéricos en un elemento de datos alfabético (componente). <br>- Se usan uno o varios caracteres numéricos en un elemento de datos numérico (componente). | Sí |
| 38 | Dígito ausente delante de signo decimal | Notificación de que un signo decimal no está precedido de uno o varios dígitos. | Sí |
| 39 | Elemento de datos demasiado largo. | Notificación de que la longitud del elemento de datos recibido supera la longitud máxima especificada en la descripción de elementos de datos. | Sí |
| 40 | Elemento de datos demasiado corto. | Notificación de que la longitud del elemento de datos recibido es inferior a la longitud mínima especificada en la descripción de elementos de datos. | Sí |
| 41 | Error de red de comunicación permanente | Notificación de que la red de comunicación empleada para la transferencia del intercambio ha informado de la existencia de un error permanente. La retransmisión de un intercambio idéntico con los mismos parámetros no se efectuará correctamente en el nivel de red. | No |
| 42 | Error de red de comunicación temporal | Notificación de que la red de comunicación empleada para la transferencia del intercambio ha informado de la existencia de un error temporal. La retransmisión de un intercambio idéntico podría ser correcta. | No |
| 43 | Destinatario de intercambio desconocido | Notificación de que el destinatario del intercambio no es conocido para un proveedor de red. | No |
| 45 | Trailing Separator (Separador final) | Notificación de una de las condiciones siguientes: <p><p>- El carácter que precede al terminador de un segmento es un separador de elemento de datos, un separador de elemento de datos de componente o un separador de elemento de datos repetido. <br>- El carácter que precede al separador de un elemento de datos es un separador de elemento de datos de componentes o un separador de elemento de datos repetido. | Sí |
| 46 | Juego de caracteres no admitido | Notificación de una de las condiciones siguientes: <p><p>- Uno o varios caracteres usados no están en el juego de caracteres definido por el identificador de sintaxis. <br>- El destinatario no admite el juego de caracteres identificado por la secuencia de escape para la técnica de extensión de código. | Sí |
| 47 | Funcionalidad de sobre no admitida | Notificación de que el destinatario no admite la estructura de sobre detectada. | Sí |
| 48 | Infracción de condición de notas de dependencia | Notificación de que se ha producido una condición de error como resultado de una infracción de la condición de dependencia. | No |
|||||

### <a name="azure-logic-apps-contrl-ack-error-codes"></a>Códigos de error de confirmación CONTRL de Azure Logic Apps

En la tabla siguiente se enumeran los códigos de error personalizados que no se definen en función de la especificación EDIFACT, pero que se usan en el campo UCI5 de la confirmación CONTRL para el procesamiento de mensajes EDIFACT y son específicos para Azure Logic Apps.

| Código de error | Condición | Causa |
|------------|-----------|-------|
| 70 | Falta el identificador de conjunto de transacciones o éste no es válido | Notificación de que falta el identificador del conjunto de transacciones o que no es válido. |
| 71 | Número de control de grupo o conjunto de transacciones no coincidente | Notificación de que falta coincidencia de los números de conjunto de transacciones o control de grupo. |
| 72 | Id. de segmento no reconocido. | Notificación de que no se reconoce el identificador de segmento. |
| 73 | XML no está en la posición correcta | Notificación de que se produjo un problema al serializar el elemento raíz XML. |
| 74 | Repeticiones insuficientes de grupos de segmentos | Notificación de que un grupo de segmentos se repite menos de la cantidad necesaria. |
| 75 | Repeticiones insuficientes de segmento | Notificación de que un segmento se repite menos de la cantidad necesaria. |
| 76 | No se encontraron suficientes datos de elementos. | Notificación de que no se encontraron suficientes elementos de datos. |
||||

## <a name="next-steps"></a>Pasos siguientes

[Intercambio de mensajes EDIFACT](logic-apps-enterprise-integration-edifact.md)
