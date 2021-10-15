---
title: Configuración de mensajes EDIFACT
description: Guía de referencia para la configuración de mensajes EDIFACT en contratos para Azure Logic Apps con Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: reference
ms.date: 09/29/2021
ms.openlocfilehash: 05d39063a6b85eae3f247e44f10118b4cf99eded
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403123"
---
# <a name="reference-for-edifact-message-settings-in-agreements-for-azure-logic-apps"></a>Referencia de la configuración de mensajes EDIFACT en contratos para Azure Logic Apps

Esta referencia describe las propiedades que puede establecer en un contrato EDIFACT para especificar cómo manejar los mensajes entre [asociados comerciales](logic-apps-enterprise-integration-partners.md). Configure estas propiedades en función del contrato con el asociado con el que intercambia mensajes.

<a name="EDIFACT-inbound-messages"></a>

## <a name="edifact-receive-settings"></a>Configuración de recepción de EDIFACT

![Captura de pantalla que muestra Azure Portal y la configuración del contrato EDIFACT para los mensajes entrantes.](./media/logic-apps-enterprise-integration-edifact-message-settings/edifact-receive-settings.png)

### <a name="identifiers"></a>Identificadores

| Propiedad | Descripción |
|----------|-------------|
| **UNB6.1 (contraseña de referencia del destinatario)** | Valor alfanumérico de 1 a 14 caracteres. |
| **UNB6.2 (calificador de referencia del destinatario)** | Valor alfanumérico de 1 a 2 caracteres. |
|||

### <a name="acknowledgments"></a>Agradecimientos

| Propiedad | Descripción |
|----------|-------------|
| **Receipt of Message (CONTRL) (Recepción del mensaje [CONTRL])** | Devuelve un reconocimiento técnico (CONTRL) al remitente del intercambio, según la Configuración de envío del contrato. |
| **Acknowledgement (CONTRL) (Confirmación [CONTRL])** | Devuelve un reconocimiento funcional (CONTRL) al remitente del intercambio, según la Configuración de envío del contrato. |
|||

<a name="receive-settings-schemas"></a>

### <a name="schemas"></a>Esquemas

| Propiedad | Descripción |
|----------|-------------|
| **UNH2.1 (tipo)** | Tipo de conjunto de transacciones. |
| **UNH2.2 (versión)** | Número de versión del mensaje que tiene entre 1 y 3 caracteres. |
| **UNH2.3 (versión)** | Número de versión del mensaje que tiene entre 1 y 3 caracteres. |
| **UNH2.5 (código asignado asociado)** | Código asignado que es alfanumérico y tiene entre 1 y 6 caracteres. |
| **UNG2.1 (id. del remitente de la aplicación)** |Escriba un valor alfanumérico con un carácter como mínimo y 35 como máximo. |
| **UNG2.2 (calificador del código del remitente de la aplicación)** |Escriba un valor alfanumérico con un máximo de cuatro caracteres. |
| **Esquema** | Esquema cargado previamente que quiera usar desde cualquier tipo de recurso: <p>- Consumo: cuenta de integración vinculada a la aplicación lógica. <br>- Estándar: creación del recurso para la aplicación lógica. |
|||

### <a name="control-numbers"></a>Control Numbers (Números de control)

| Propiedad | Descripción |
|----------|-------------|
| **No permite duplicados en números de control de intercambio.** | Bloquee los intercambios duplicados. La acción de descodificación de EDIFACT comprueba que el número de control de intercambio (UNB5) del intercambio recibido no coincide con el número de control de intercambio. Si se detecta una coincidencia, el intercambio no se procesa. |
| **Check for duplicate UNB5 every (days) (Comprobar UNB5 duplicados cada [días])** | Si decide no permitir números de control de intercambio duplicados, puede especificar el número de días para la ejecución de la comprobación. |
| **No permitir duplicados en números de control de grupo** | Permite bloquear los intercambios que tienen números de control de grupo duplicados (UNG5). |
| **No permitir duplicados en números de control de conjuntos de transacciones** | Permite bloquear los intercambios que tengan números de control de conjunto de transacciones duplicados (UNH1). |
| **EDIFACT Acknowledgement Control Number (Número de control de confirmación EDIFACT)** | Permite asignar los números de referencia del conjunto de transacciones que utilizará en las confirmaciones; para ello, escriba un valor para el prefijo, un intervalo de números de referencia y un sufijo. |
|||

### <a name="validation"></a>Validación

Cuando termine de configurar una fila de validación, aparecerá automáticamente la siguiente fila. Si no especifica ninguna regla, la validación usa la fila etiquetada como **Predeterminada**.

| Propiedad | Descripción |
|----------|-------------|
| **Tipo de mensaje** | Tipo de mensaje de EDI. |
| **Validación de EDI** | Permite ejecutar una validación de EDI en los tipos de datos según lo definido en las propiedades de EDI del esquema, las restricciones de longitud, los elementos de datos vacíos y los separadores finales. |
| **Validación extendida** | Si el tipo de datos no es EDI, la validación se ejecuta en el requisito de elemento de datos, así como en las repeticiones permitidas, las enumeraciones y la validación de la longitud del elemento de datos (mínimo o máximo). |
| **Permitir ceros iniciales/finales** | Permite conservar los caracteres de espacio y cero iniciales o finales adicionales. No los quite. |
| **Recortar ceros iniciales y finales** | Permite quitar los caracteres de espacio y cero iniciales o finales. |
| **Directiva de separador final** | Genere separadores finales. <p> - **No permitido**: permite prohibir los delimitadores y separadores finales en el intercambio recibido. Si el intercambio contiene delimitadores y separadores finales, se declara no válido. <p>- **Opcional**: acepta intercambios con o sin delimitadores y separadores finales. <p>- **Obligatorio**: el intercambio recibido debe tener delimitadores y separadores finales. |
|||

### <a name="internal-settings"></a>Internal Settings (Configuración interna)

| Propiedad | Descripción |
|----------|-------------|
| **Crear etiquetas XML vacías si se permiten los separadores finales** | El remitente del intercambio incluye etiquetas XML vacías para los separadores finales. |
| **Dividir intercambio como conjuntos de transacciones: suspender conjuntos de transacciones en caso de error** | Permite analizar todos los conjuntos de transacciones de un intercambio en un documento XML independiente aplicando el sobre adecuado al conjunto de transacciones. Suspende solo los conjuntos de transacciones que no superen la validación. |
| **Dividir intercambio como conjuntos de transacciones: suspender intercambio en caso de error** | Permite analizar todos los conjuntos de transacciones de un intercambio en un documento XML independiente aplicando el sobre adecuado. Suspende todo el intercambio cuando uno o varios conjuntos de transacciones no superan la validación. |
| **Conservar intercambio: suspender conjuntos de transacciones en caso de error** | Deja intacto el intercambio y crea un documento XML para todo el intercambio por lotes. Suspende solo los conjuntos de transacciones que no superan la validación, sin dejar de procesar el resto. |
| **Conservar intercambio: suspender intercambio en caso de error** | Deja intacto el intercambio y crea un documento XML para todo el intercambio por lotes. Suspende todo el intercambio cuando uno o varios conjuntos de transacciones no superan la validación. |
|||

<a name="EDIFACT-outbound-messages"></a>

## <a name="edifact-send-settings"></a>Configuración de envío de EDIFACT

![Captura de pantalla que muestra Azure Portal y la configuración del contrato EDIFACT para los mensajes salientes.](./media/logic-apps-enterprise-integration-edifact-message-settings/edifact-send-settings.png)

### <a name="identifiers"></a>Identificadores

| Propiedad | Descripción |
|----------|-------------|
| **UNB1.2 (versión de sintaxis)** | Valor de 1 a 4. |
| **UNB2.3 (Sender Reverse Routing Address) (UNB2.3 [dirección de enrutamiento inverso del remitente])** | Valor alfanumérico de 1 a 14 caracteres. |
| **UNB3.3 (Recipient Reverse Routing Address) (UNB3.3 [dirección de enrutamiento inverso del destinatario])** | Valor alfanumérico de 1 a 14 caracteres. |
| **UNB6.1 (contraseña de referencia del destinatario)** | Valor alfanumérico de 1 a 14 caracteres. |
| **UNB6.2 (calificador de referencia del destinatario)** | Valor alfanumérico de 1 a 2 caracteres. |
| **UNB7 (Application Reference ID) (UNB7 [Identificador de referencia de solicitud])** | Valor alfanumérico de 1 a 14 caracteres. |
|||

### <a name="acknowledgment"></a>Acknowledgement (Confirmación)

| Propiedad | Descripción |
|----------|-------------|
| **Receipt of Message (CONTRL) (Recepción del mensaje [CONTRL])** | El asociado de host que envía el mensaje solicita una confirmación técnica (CONTRL) del asociado invitado. |
| **Acknowledgement (CONTRL) (Confirmación [CONTRL])** | El asociado de host que envía el mensaje espera una confirmación funcional (CONTRL) del asociado invitado. |
| **Generate SG1/SG4 loop for accepted transaction sets (Generar bucle SG1/SG4 en los conjuntos de transacciones aceptadas)** | Si decide solicitar una confirmación funcional, esta configuración fuerza la generación de bucles SG1/ SG4 en las confirmaciones funcionales para conjuntos de transacciones aceptadas. |
|||

### <a name="schemas"></a>Esquemas

| Propiedad | Descripción |
|----------|-------------|
| **UNH2.1 (tipo)** | Tipo de conjunto de transacciones. |
| **UNH2.2 (versión)** | Número de versión del mensaje. |
| **UNH2.3 (versión)** | Número de versión del mensaje. |
| **Esquema** | Esquema cargado previamente que quiera usar desde cualquier tipo de recurso: <p>- Consumo: cuenta de integración vinculada a la aplicación lógica. <br>- Estándar: creación del recurso para la aplicación lógica. |
|||

### <a name="envelopes"></a>Envelopes (Sobres)

Cuando termine de configurar una fila de sobre, aparecerá automáticamente la siguiente fila. Si no especifica ninguna regla, la validación usa la fila etiquetada como **Predeterminada**.

| Propiedad | Descripción |
|----------|-------------|
| **UNB8 (Processing Priority Code) (UNB8 [Código de prioridad de procesamiento])** | Valor alfabético que no tenga más de un solo carácter. |
| **UNB10 (Communication Agreement) (UNB10 [Acuerdo de comunicaciones])** | Valor alfanumérico de 1 a 40 caracteres. |
| **UNB11 (Test Indicator) (UNB11 [Indicador de prueba])** | Permite indicar que el intercambio generado son datos de prueba. |
| **Aplicar segmento UNA (notificación del servicio)** | Permite generar un segmento UNA para el intercambio que se va a enviar. |
| **Aplicar segmentos UNG (encabezado de grupo funcional)** | Permite crear segmentos de agrupación en el encabezado de grupo funcional de los mensajes enviados al asociado invitado. Los siguientes valores se utilizan para crear los segmentos UNG: <p>- **Esquema**: esquema cargado previamente que quiera usar desde cualquier tipo de recurso: <p>- Consumo: cuenta de integración vinculada a la aplicación lógica. <p>- Estándar: creación del recurso para la aplicación lógica. <p>- **UNG1**: valor alfanumérico de 1 a 6 caracteres. <p>- **UNG2.1**: valor alfanumérico de 1 a 35 caracteres. <p>- **UNG2.2**: valor alfanumérico de 1 a 4 caracteres. <p>- **UNG3.1**: valor alfanumérico de 1 a 35 caracteres. <p>- **UNG3.2**: valor alfanumérico de 1 a 4 caracteres. <p>- **UNG6**: valor alfanumérico de 1 a 3 caracteres. <p>- **UNG7.1**: valor alfanumérico de 1 a 3 caracteres. <p>- **UNG7.2**: valor alfanumérico de 1 a 3 caracteres. <p>- **UNG7.3**: valor alfanumérico de 1 a 6 caracteres. <p>- **UNG8**: valor alfanumérico de 1 a 14 caracteres. |
|||

### <a name="character-sets-and-separators"></a>Character Sets and Separators (Juegos de caracteres y separadores)

Además del juego de caracteres, puede especificar otro juego de delimitadores para que se use con cada tipo de mensaje. Si no especifica ningún juego de caracteres para un esquema de mensaje concreto, se usa el juego de caracteres **predeterminado**.

| Propiedad | Descripción |
|----------|-------------|
| **UNB1.1 (System Identifier) (UNB1.1 [Identificador del sistema])** | Juego de caracteres EDIFACT que se aplicará al intercambio saliente. |
| **Esquema** | Esquema cargado previamente que quiera usar desde cualquier tipo de recurso: <p>- Consumo: cuenta de integración vinculada a la aplicación lógica. <p>- Estándar: creación del recurso para la aplicación lógica. <p>Para el esquema seleccionado, elija el conjunto de separadores que desee usar, en función de las siguientes descripciones. Cuando termine de configurar una fila de esquema, aparecerá automáticamente la siguiente fila. |
| **Tipo de entrada** | Tipo de entrada para el mensaje. |
| **Separador de componentes** | Carácter único que se usará para separar elementos de datos compuestos. |
| **Separador de elementos de datos** | Carácter simple para separar los elementos de datos simples dentro de los elementos de datos compuestos. |
| **UNA3 (Notación decimal)** | Notación que se va a usar en el intercambio saliente. |
| **UNA4 (Indicador de lanzamiento)** | Valor para el indicador de versión que indica que el siguiente carácter no es un separador de sintaxis, un terminador o un carácter de versión, sino que es parte de los datos originales. |
| **UNA5 (Separador de repetición)** | Valor que usará para el separador de repetición para separar segmentos que se repiten dentro de un conjunto de transacciones. |
| **Terminador de segmento** | Carácter único que indica el final de un segmento EDI. |
| **Sufijo** | Carácter que se usa con el identificador del segmento. Si designa un sufijo, el elemento de datos del terminador de segmento podrá estar vacío. Si el terminador de segmentos se deja vacío, debe designar un sufijo. |
|||

### <a name="control-numbers"></a>Control Numbers (Números de control)

| Propiedad | Descripción |
|----------|-------------|
| **UNB5 (Interchange Control Number) (UNB5 [Número de control de intercambio])** | Un prefijo, un intervalo de valores para el número de control de intercambio y un sufijo. Estos valores se utilizan para generar un intercambio saliente. El prefijo y sufijo son opcionales, pero el número de control es necesario. El número de control se incrementa para cada nuevo mensaje; en cambio, el prefijo y el sufijo siguen siendo los mismos. |
| **UNG5 (Group Control Number) (UNG5 [Número de control de grupo])** | Un prefijo, un intervalo de valores para el número de control de intercambio y un sufijo. Estos valores se utilizan para generar el número de control de grupo. El prefijo y sufijo son opcionales, pero el número de control es necesario. El número de control se incrementa para cada nuevo mensaje hasta que se alcanza el valor máximo; en cambio, el prefijo y el sufijo siguen siendo los mismos. |
| **UNH1 (Message Header Reference Number) (UNH1 [Número de referencia de encabezado de mensaje])** | Un prefijo, un intervalo de valores para el número de control de intercambio y un sufijo. Estos valores se utilizan para generar el número de referencia de encabezado del mensaje. El prefijo y sufijo son opcionales; en cambio, el número de referencia es obligatorio. El prefijo y el sufijo son opcionales; el número de referencia, obligatorio. El número de referencia se incrementa para cada nuevo mensaje; en cambio, el prefijo y el sufijo siguen siendo los mismos. |
|||

### <a name="validation"></a>Validación

Cuando termine de configurar una fila de validación, aparecerá automáticamente la siguiente fila. Si no especifica ninguna regla, la validación usa la fila etiquetada como **Predeterminada**.

| Propiedad | Descripción |
|----------|-------------|
| **Tipo de mensaje** | Tipo de mensaje de EDI. |
| **Validación de EDI** | Permite ejecutar una validación de EDI en los tipos de datos según lo definido en las propiedades de EDI del esquema, las restricciones de longitud, los elementos de datos vacíos y los separadores finales. |
| **Validación extendida** | Si el tipo de datos no es EDI, lo que se valida es el requisito del elemento de datos, así como las repeticiones permitidas, las enumeraciones y la validación de la longitud del elemento de datos (mínimo/máximo). |
| **Permitir ceros iniciales/finales** | Permite conservar los caracteres de espacio y cero iniciales o finales adicionales. No los quite. |
| **Recortar ceros iniciales y finales** | Quite los ceros iniciales o finales. |
| **Directiva de separador final** | Genere separadores finales. <p>- **No permitido**: permite prohibir los delimitadores y separadores finales en el intercambio enviado. Si el intercambio contiene delimitadores y separadores finales, se declara no válido. <p>- **Opcional**: envía intercambios con o sin delimitadores y separadores finales. <p>-  **Obligatorio**: indica si el intercambio enviado debe contener delimitadores y separadores finales. |
|||

## <a name="next-steps"></a>Pasos siguientes

[Intercambio de mensajes EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md)