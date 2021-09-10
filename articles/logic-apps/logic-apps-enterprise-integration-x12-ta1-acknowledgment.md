---
title: Códigos de error y confirmaciones TA1 de X12
description: Obtenga información sobre las confirmaciones técnicas TA1 y los códigos de error que se usan para los mensajes X12 en Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: praveensri
ms.author: psrivas
ms.reviewer: estfan, divswa, azla
ms.topic: reference
ms.date: 07/15/2021
ms.openlocfilehash: be168489b6cdccd3662a174ceab34d093db41244
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481703"
---
# <a name="ta1-technical-acknowledgments-and-error-codes-for-x12-messages-in-azure-logic-apps"></a>Códigos de error y confirmaciones técnicas TA1 para los mensajes X12 en Azure Logic Apps

En Azure Logic Apps, puede crear flujos de trabajo que controlen los mensajes X12 para la comunicación del intercambio electrónico de datos (EDI) al usar las operaciones **X12**. En la mensajería EDI, las confirmaciones proporcionan el estado del procesamiento de un intercambio EDI. Al recibir un intercambio, la [acción de **descodificación de mensajes X12**](logic-apps-enterprise-integration-x12-decode.md) puede devolver uno o varios tipos de confirmaciones al emisor, en función de los tipos de confirmación que estén habilitados y el nivel de validación especificado.

Por ejemplo, el receptor notifica el estado de la validación del encabezado de control de intercambio (ISA) y del finalizador de control de intercambio (IEA) en el mensaje codificado X12 recibido mediante el envío de una *confirmación técnica (ACK) TA1*. Si el encabezado y el finalizador son válidos, el receptor envía una ACK TA1 positiva, independientemente del estado de otro contenido. Si el encabezado y el finalizador no son válidos, el receptor envía una **ACK TA1** con un código de error en su lugar.

La ACK TA1 de X12 se corresponde con el esquema **X12_<*número de versión*>_TA1.xsd**. El receptor envía la ACK TA1 dentro de un sobre ISA e IEA. Sin embargo, este sobre no es diferente al de cualquier otro intercambio.

En este tema se proporciona información general breve sobre las ACK TA1 de X12, incluidos los segmentos ACK TA1 en un intercambio y los códigos de error que se usan en esos segmentos. Para obtener información relacionada, revise la documentación siguiente:

* [Códigos de error y confirmaciones funcionales 997 de X12](logic-apps-enterprise-integration-x12-997-acknowledgment.md)
* [Intercambio de mensajes X12 para la integración empresarial B2B](logic-apps-enterprise-integration-x12.md)
* [Intercambio de mensajes EDIFACT para integración empresarial B2B](logic-apps-enterprise-integration-edifact.md)
* [¿Qué es Azure Logic Apps?](logic-apps-overview.md)
* [Soluciones de integración empresarial B2B con Azure Logic Apps y Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

<a name="ta1-ack-segments"></a>

## <a name="ta1-ack-segments"></a>Segmentos ACK TA1

En la tabla siguiente se describen los segmentos ACK TA1 de un intercambio:

| Campo TA1 | Nombre del campo | Asignado a un intercambio entrante | Valor |
|-----------|------------|--------------------------------|-------|
| TA101 | Número de control de intercambio. | ISA13 - Número de control de intercambio | - |
| TA102 | Fecha de intercambio | ISA09: fecha del intercambio | - |
| TA103 | Hora de intercambio | ISA10: hora del intercambio | - |
| TA104 | Código de confirmación de intercambio* | N/D | * El comportamiento del motor se basa en la validación de elementos de datos, a excepción de la información de seguridad y autenticación, que se basan en comparaciones de cadenas en la información de configuración. <p>El valor de comportamiento del motor (TA104) es A, E o R, en función de las definiciones siguientes: <p><p>A = Aceptar <br>E = Intercambio aceptado con errores <br>R = Intercambio rechazado o suspendido <p><p>Para obtener más información, revise los [códigos de error ACK TA1](#ta1-ack-error-codes). |
| TA105 | Código de nota de intercambio | N/D | Código de error de resultado de procesamiento. Para obtener más información, revise los [códigos de error ACK TA1](#ta1-ack-error-codes). |
|||||

<a name="ta1-ack-error-codes"></a>

## <a name="ta1-ack-error-codes"></a>Códigos de error ACK TA1

En esta sección se tratan los códigos de error que se usan en los [segmentos ACK TA1](#ta1-ack-segments). En la tabla siguiente se enumeran los códigos de error admitidos y no admitidos, tal y como se define en la especificación X12, para el procesamiento de mensajes X12 en Azure Logic Apps. En la columna **Comportamiento del motor**, los valores TA104 tienen las definiciones siguientes:

* A = Aceptar
* E = Intercambio aceptado con errores
* R = Intercambio rechazado o suspendido

| Condición | Comportamiento del motor <br>(Valor de TA104) | Valor de TA105 | ¿Compatible? |
|-----------|-----------------------------------|-------------|------------|
| Correcto | A | 000 | Sí |
| Los números de control de intercambio del encabezado ISA 13 y del finalizador IEA02 no coinciden. | E | 001 | Sí |
| El estándar en ISA11 (estándares de control) no es compatible. | E | 002 | Sí, si no coinciden los identificadores. |
| La versión de los controles no es compatible. | E | 003 | No, se usa el código de error 017 en su lugar. |
| El terminador de segmento no es válido* <p><p>* El terminador de segmento puede tener las combinaciones válidas siguientes: <p><p>- Solo carácter de terminador de segmento. <br>- Carácter de terminador de segmento seguido del sufijo 1 y el sufijo 2. | R | 004 | Sí |
| Calificador de Id. de intercambio no válido para el remitente. | R | 005 | Sí, si no coinciden los identificadores. |
| Id. de intercambio no válido para el remitente. | E | 006 | Sí, si se recibe un intercambio en un puerto de recepción que requiere autenticación. <p><p>**Nota**: Las propiedades relacionadas con el identificador del remitente se revisan. Si estas propiedades no son coherentes o si la configuración de entidades no está disponible porque no se ha establecido, el intercambio se rechaza. |
| Calificador de Id. de intercambio no válido para el receptor. | R | 007 | Sí, si no coinciden los identificadores. |
| Id. de receptor de intercambio no válido. | E | 008 | No* <p><p>* Compatible si se recibe un intercambio en un puerto de recepción que requiere autenticación. Las propiedades relacionadas con el identificador del remitente se revisan. Si estas propiedades no son coherentes o si la configuración de entidades no está disponible porque no se ha establecido, el intercambio se rechaza. |
| Id. de receptor de intercambio desconocido. | E | 009 | Sí |
| Valor de calificador de información de autorización no válido. | R | 010 | Sí, si no coinciden los identificadores. |
| Valor de información de autorización no válido. | R | 011 | Sí, si la entidad está configurada o tiene valores. |
| Valor de calificador de información de seguridad no válido. | R | 012 | Sí, si no coinciden los identificadores. |
| Valor de información de seguridad no válido | R | 013 | Sí, si la entidad está configurada o tiene valores. |
| Valor de fecha de intercambio no válido. | R | 014 | Sí |
| Valor de hora de intercambio no válido. | R | 015 | Sí |
| Valor de identificador de estándares de intercambio no válido. | R | 016 | Sí |
| Valor de identificador de versión de intercambio no válido | R | 017 | Sí, lo que indica que el valor de enumeración no es válido. |
| Valor de número de control de intercambio no válido. | R | 018 | Sí |
| Valor solicitado de confirmación no válido | E | 019 | Sí |
| Valor del indicador de prueba no válido | E | 020 | Sí |
| Valor de número de grupos incluidos no válido | E | 021 | Sí |
| Estructura de control no válida. | R | 022 | Sí |
| Fin de archivo (transmisión) incorrecto (prematuro) | R | 023 | Sí |
| Contenido de intercambio no válido, por ejemplo, segmento GS no válido | R | 024 | Sí |
| Número de control de intercambio duplicado. | R, según la configuración | 025 | Sí |
| Separador de elemento de datos no válido. | R | 026 | Sí |
| Separador de elemento de componente no válido. | R | 027 | Sí |
| Fecha de entrega no válida en solicitud de entrega aplazada. | - | - | No |
| Hora de entrega no válida en solicitud de entrega aplazada. | - | - | No |
| Código de hora de entrega no válido en solicitud de entrega aplazada. | - | - | No |
| Calificación de servicio no válida. | - | - | No |
||||

## <a name="next-steps"></a>Pasos siguientes

* [Intercambio de mensajes X12 para la integración empresarial B2B](logic-apps-enterprise-integration-x12.md)