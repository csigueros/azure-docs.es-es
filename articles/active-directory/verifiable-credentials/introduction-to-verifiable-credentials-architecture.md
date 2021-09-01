---
title: Introducción a la arquitectura de Azure Active Directory (versión preliminar)
description: Obtenga información básica para planear y diseñar cualquier solución
documentationCenter: ''
author: barbaraselden
manager: martinco
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: baselden
ms.openlocfilehash: 45dd0be664474b22a24354903233e5729479f095
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723123"
---
# <a name="azure-ad-verifiable-credentials-architecture-overview-preview"></a>Introducción a la arquitectura de Azure AD Verifiable Credentials (versión preliminar)

> [!IMPORTANT]
> Las credenciales verificables de Azure Active Directory son una característica que se encuentra actualmente en versión preliminar. Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [**Condiciones de uso complementarias de las versiones preliminares de Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Es importante planear una solución de credenciales verificable para que, además de generar y validar las credenciales, tenga una vista completa de los impactos arquitectónicos y empresariales de la solución. Si aún no ha consultado los artículos [Introducción a Azure Active Directory Verifiable Credentials](decentralized-identifier-overview.md) y las [Preguntas frecuentes](verifiable-credentials-faq.md), es aconsejable que lo haga y, posteriormente, complete el tutorial de [introducción](get-started-verifiable-credentials.md). 

Esta introducción a la arquitectura presenta las funcionalidades y los componentes del servicio Azure Active Directory Verifiable Credentials. Para obtener información más detallada sobre la emisión y validación, consulte 

* [Planeamiento de una solución de emisión](plan-issuance-solution.md)

* [Planeamiento de una solución de verificación](plan-verification-solution.md)

## <a name="approaches-to-identity"></a>Enfoques relativos a la identidad

En la actualidad, la mayoría de las organizaciones usan sistemas de identidad centralizados para proporcionar credenciales de empleados. También usan distintos métodos para llevar clientes, asociados, proveedores y usuarios de confianza a los límites de confianza de la organización. Estos métodos incluyen la federación, la creación y administración de cuentas de invitado con sistemas como Azure AD B2B y la creación de confianzas explícitas con usuarios de confianza. La mayoría de las relaciones empresariales tienen un componente digital, por lo que la habilitación de cierta forma de confianza entre organizaciones requiere un gran esfuerzo. 

### <a name="centralized-identity-systems"></a>Sistemas de identidad centralizados

Los enfoques centralizados siguen funcionando bien en muchos casos, como cuando las aplicaciones, los servicios y los dispositivos dependen de los mecanismos de confianza usados en un dominio o límite de confianza. 

En los sistemas de identidad centralizados, el proveedor de identidades (IDP) controla el ciclo de vida y uso de las credenciales. 

![Ejemplo de sistema de identidad centralizado](./media/introduction-to-verifiable-credentials-architecture/centralized-identity-architecture.png)


Sin embargo, hay escenarios en los que el uso de una arquitectura descentralizada con credenciales verificables puede proporcionar valor mediante el aumento de escenarios clave como:

* Proteger la incorporación de identidades de empleados y de otros usuarios, lo que incluye los escenarios remotos.

* Acceder a recursos que están dentro del límite de confianza de la organización en función de criterios concretos.

* Acceder a recursos que están fuera del límite de confianza, como acceder a recursos de asociados, con una credencial portátil emitida por la organización.

 

### <a name="decentralized-identity-systems"></a>Sistemas de identidad descentralizados

En los sistemas de identidad descentralizados, el control del ciclo de vida y el uso de las credenciales se comparten entre el emisor, el titular y el usuario de confianza que consume la credencial.

Fíjese en el escenario del diagrama siguiente, en el que Proseware, un sitio web de comercio electrónico, quiere ofrecer descuentos corporativos a los empleados de Woodgrove. 

 ![Ejemplo de sistema de identidad descentralizado](media/introduction-to-verifiable-credentials-architecture/decentralized-architecture.png)


La terminología de las credenciales verificables (VM) puede resultar confusa si no se está familiarizado con las credenciales verificables. Las definiciones siguientes son de la sección de terminología de [Verifiable Credentials Data Model 1.0](https://www.w3.org/TR/vc-data-model/). Después de cada una de ellas, las relacionamos con las entidades del diagrama anterior.

 "Un ***emisor*** es un rol que una entidad puede realizar mediante la validación de notificaciones sobre uno o varios asuntos, la creación de una credencial verificable a partir de estas notificaciones y la transmisión de la credencial verificable a un titular."

* En el diagrama anterior, Woodgrove es el emisor de credenciales verificables para sus empleados. 

 "Un ***titular*** es un rol que puede realizar una entidad al poseer una o varias credenciales verificables y la generación de presentaciones a partir de ellas. Habitualmente, un titular es un sujeto de las credenciales verificables que posee, pero no siempre es así. Los titulares almacenan sus credenciales en repositorios de credenciales." 

* En el diagrama anterior, Alice es una empleada de Woodgrove. Ha obtenido una credencial verificable del emisor Woodgrove y es titular de esa credencial.

 "Un ***comprobador*** es un rol que realiza una entidad al recibir una o varias credenciales verificables, opcionalmente dentro de una presentación verificable para su procesamiento. Otras especificaciones pueden hacer referencia a este concepto como usuario de confianza."

* En el diagrama anterior, Proseware es un comprobador de las credenciales que ha emitido Woodgrove. 

"Una ***credencial*** es un conjunto de una o varias notificaciones realizadas por un emisor. Una credencial verificable es una credencial a prueba de manipulaciones que tiene una autoría que se puede comprobar criptográficamente. Las credenciales verificables se pueden usar para crear presentaciones verificables, que también se pueden comprobar criptográficamente. Las notificaciones de una credencial pueden ser sobre diferentes temas."

 "Un ***identificador descentralizado*** es un identificador basado en identificador URI portátil, que también se conoce como DID, asociado a una entidad. Estos identificadores se usan a menudo en una credencial verificable y están asociados a asuntos, emisores y comprobadores."

* En el diagrama anterior, las claves públicas de los DID del actor se muestran almacenadas en el libro de contabilidad descentralizado (ION), en el documento del identificador descentralizado.

 "Un **documento de identificador descentralizado**, que también se denomina *_documento DID_**, es un documento al que se puede acceder mediante un registro de datos verificable y que contiene información relacionada con un identificador descentralizado específico, como el repositorio asociado y la información de clave pública." 

* En el escenario anterior, tanto el emisor como el comprobador tienen un DID y un documento de DID. El documento de DID contiene la clave pública y la lista de dominios web DNS asociados al DID (también conocidos como dominios vinculados).

* Woodgrove (emisor) firma las credenciales verificables de sus empleados con su clave pública; de forma similar, Proseware (comprobador) firma solicitudes para presentar una credencial verificable mediante su clave, que también está asociada a su DID.

 "Un ***libro de contabilidad distribuida*** es un sistema no centralizado para registrar eventos. Estos sistemas establecen la confianza suficiente para que los participantes utilicen en los datos registrados por otros usuarios para tomar decisiones operativas. Normalmente usan bases de datos distribuidas, en las que los diferentes nodos usan un protocolo de consenso para confirmar el orden de las transacciones con firma criptográfica. La vinculación de transacciones firmadas digitalmente a lo largo del tiempo a menudo hace que el historial del libro de contabilidad sea inmutable."

* La solución de Microsoft usa ***Identity Overlay Network (ION)*** para proporcionar la funcionalidad de infraestructura de clave pública (PKI) descentralizada.

 

### <a name="combining-centralized-and-decentralized-identity-architectures"></a>Combinación de arquitecturas de identidad centralizada y descentralizada

Al examinar una solución de credenciales verificable, resulta útil saber cómo se pueden combinar las arquitecturas de identidad descentralizada con arquitecturas de identidad centralizada para ofrecer una solución que reduzca el riesgo y ofrece importantes ventajas operativas.

## <a name="the-user-journey"></a>El recorrido del usuario

Esta información general sobre la arquitectura sigue el recorrido de un candidato a un trabajo y un empleado, que solicita empleo en una organización y lo acepta. Luego, sigue al empleado y a la organización a través de los cambios en los que las credenciales verificables pueden aumentar los procesos centralizados.

 

### <a name="actors-in-these-use-cases"></a>Actores de estos casos de uso

* **Alice**, una persona que solicita empleo en Woodgrove, Inc, a la que se lo conceden y lo acepta.

* **Woodgrove**, Inc., una empresa ficticia.

* **Adatum**, el asociado de verificación de identidad ficticio de Woodgrove.

* **Proseware**, la organización asociada ficticia de Woodgrove.

Woodgrove usa arquitecturas de identidad centralizada y descentralizada.

### <a name="steps-in-the-user-journey"></a>Pasos en el recorrido del usuario

* Alice se presenta a una vacante en Woodgrove, Inc, se lo conceden y se incorpora al puesto de trabajo.

* Acceso a los recursos digitales dentro del límite de confianza de Woodgrove.

* Acceso a los recursos digitales fuera del límite de confianza de Woodgrove sin ampliar los límites de confianza de Woodgrove o de los asociados.

Mientras el negocio de Woodgrove siga funcionando, será preciso que administre identidades continuamente. Los casos de uso de esta guía describen de qué forma puede usar Alice las funciones de autoservicio para obtener y mantener sus identificadores y de qué forma puede Woodgrove agregar, modificar y finalizar relaciones de negocio a negocio con diversos requisitos de confianza. 

Estos casos de uso muestran cómo se pueden combinar identidades centralizadas e identidades descentralizadas para proporcionar un ciclo de vida y una estrategia de identidad y confianza más sólidos y eficaces. 


## <a name="user-journey-onboarding-to-woodgrove"></a>Recorrido del usuario: incorporación a Woodgrove

![Recorrido de la incorporación del usuario a Woodgrove](media/introduction-to-verifiable-credentials-architecture/onboarding-journey.png)

 **Conocimiento**: Alice desea trabajar en Woodgrove, Inc. y visita el sitio web profesional de Woodgrove. 

**Activación**: el sitio de Woodgrove presenta a Alice un método para demostrar su identidad, se le pide un código QR o un vínculo profundo para visitar a su asociado de pruebas de identidad de confianza, Adatum.

**Solicitud y carga**: Adatum solicita una prueba de identidad a Alice. Alice se hace un selfie y hace una foto de su carnet de conducir y los carga en Adatum. 

**Emisión**: una vez que Adatum comprueba la identidad de Alice, Adatum emite a Alice una credencial verificable (VC) que atestigua su identidad.

**Presentación**: Alice (la titular y sujeto de la credencial) puede acceder al portal profesional de Woodgrove para completar el proceso de solicitud de empleo. Cuando Alice usa la credencial verificable para acceder al portal, Woodgrove adopta los roles de comprobador y usuario de confianza, y confía en la atestación de Adatum.


### <a name="distributing-initial-credentials"></a>Distribución de las credenciales iniciales

Alice acepta el empleo en Woodgrove. Como parte del proceso de incorporación, se crea una cuenta Azure Active Directory (AD) para que Alice la use dentro del límite de confianza de Woodgrove. El jefe de Alice debe averiguar cómo habilitar a Alice, que trabaja de forma remota, para que reciba la información inicial acerca del inicio de sesión de forma segura. Antes, el departamento de TI habría proporcionado esas credenciales al jefe de Alice, quien las imprimiría y se las entregaría a Alice. Sin embargo, esto ya no se hace con los empleados remotos.

Para agregar valor a los sistemas centralizados, las credenciales verificables aumentan el proceso de distribución de credenciales. En lugar de necesitar que el administrador proporcione credenciales, Alice puede usar su credencial verificable como prueba de identidad para recibir su nombre de usuario y credenciales iniciales para acceder a los sistemas centralizados. Alice presenta la prueba de identidad que han agregado a su cartera como parte del proceso de incorporación. 

 

En el caso de uso de la incorporación, los roles de la relación de confianza se distribuyen entre el emisor, el comprobador y el titular. 

* El emisor es responsable de validar las notificaciones que forman parte de la credencial verificable que emite. Adatum valida la identidad de Alice para emitir la credencial verificable. En este caso, las credenciales verificables se emiten sin tener en cuenta un comprobador o usuario de confianza.

* El titular posee la credencial verificable e inicia la presentación de la misma para su comprobación. Solo Alice puede presentar las credenciales verificables que contiene.

* El comprobador acepta las notificaciones en la credencial verificable de los emisores en que confía y valida la credencial verificable mediante la funcionalidad de libro de contabilidad descentralizado descrita en el modelo de datos de credenciales verificables. Woodgrove confía en las notificaciones de Adatum sobre la identidad de Alice.

 

Al combinar arquitecturas de identidad centralizada y descentralizada para la incorporación, Woodgrove no necesita almacenar la información con privilegios sobre Alice necesaria para la comprobación de su identidad, como su DNI, ya que confía en que la credencial verificable de Alice emitida por el asociado de verificación de identidad (Adatum) confirma su identidad. Se minimiza la duplicación de esfuerzo y se puede implementar un enfoque programático y predecible para las tareas iniciales de incorporación. 

 

## <a name="user-journey-accessing-resources-inside-the-trust-boundary"></a>Recorrido del usuario: acceso a recursos dentro del límite de confianza

![Acceso a recursos dentro del límite de confianza](media/introduction-to-verifiable-credentials-architecture/inside-trust-boundary.png)

Al ser empleado, Alice opera dentro del límite de confianza de Woodgrove. Woodgrove actúa como proveedor de identidades (IDP) y mantiene un control completo de la identidad y configuración de las aplicaciones que Alice usa para interactuar dentro del límite de confianza de Woodgrove. Para usar recursos en el límite de confianza de Azure AD, Alice proporciona potencialmente varias formas de prueba de identificación para iniciar sesión en el límite de confianza de Woodgrove y acceder a los recursos que se encuentran dentro del entorno tecnológico de Woodgrove. Se trata de un escenario típico que se sirve bien mediante una arquitectura de identidad centralizada. 

* Woodgrove administra el límite de confianza y mediante el uso de procedimientos de seguridad adecuados proporciona a Alice el nivel de acceso de privilegios mínimos en función de su trabajo. Para mantener una posición de seguridad sólida y, posiblemente, por motivos de cumplimiento, Woodgrove también debe ser capaz de realizar un seguimiento de los permisos y el acceso de los empleados a los recursos, y debe poder revocar los permisos cuando un empleado deja la empresa.

* Alice solo usa la credencial que Woodgrove mantiene para acceder a los recursos de Woodgrove. Alice no necesita realizar un seguimiento de cuándo se usa la credencial, ya que la administra Woodgrove y solo se usa con los recursos de Woodgrove. La identidad solo es válida dentro del límite de confianza de Woodgrove cuando se necesita acceder a los recursos de Woodgrove, por lo que Alice no tiene necesidad de poseer la credencial. 

### <a name="using-vcs-inside-the-trust-boundary"></a>Uso de credenciales verificables dentro del límite de confianza

Los empleados individuales tienen necesidades cambiantes con respecto a la identidad y las credenciales verificables pueden aumentar los sistemas centralizados para administrar esos cambios. 

* Durante el tiempo en que trabaje en Woodgrove Alice podría necesitar acceso adicional a los recursos si cumpliera determinados requisitos. Por ejemplo, cuando Alice complete el entrenamiento de privacidad, se le podrá emitir un nueva credencial verificable de empleado con esa notificación y esa credencial se puede usar para acceder a recursos restringidos.

* Las credenciales verificables se pueden usar dentro del límite de confianza para la recuperación de cuentas. Por ejemplo, si el empleado ha perdido el teléfono y el equipo, para recuperar el acceso debe obtener una nueva credencial verificable del servicio de comprobación de identidad de confianza de Woodgrove y, después, usar esa credencial verificable para obtener nuevas credenciales. 

 ## <a name="user-journey-accessing-external-resources"></a>Recorrido del usuario: acceso a recursos externos

Woodgrove negocia con Proseware un descuento en la compra de productos. Todos los empleados de Woodgrove pueden disfrutar de ese descuento. Woodgrove quiere proporcionar a Alice una manera de acceder al sitio web de Proseware y recibir el descuento en los productos que compre. Si Woodgrove usa una arquitectura de identidad centralizada, hay dos formas de proporcionar el descuento a Alice: 

* Alice podría proporcionar información personal para crear una cuenta con Proseware y, después, Proseware tendría que comprobar que Alice trabaja en Woodgrove.

* Woodgrove podría expandir su límite de confianza para incluir Proseware como usuario de confianza y Alice podría usar el límite de confianza extendido para recibir el descuento. 

Con identificadores descentralizados, Woodgrove puede proporcionar a Alice una credencial verificable que puede usar para acceder al sitio web de Proseware y a otros recursos externos. 

![Acceso a recursos que están fuera del límite de confianza](media/introduction-to-verifiable-credentials-architecture/external-resources.png)

 

Al proporcionar a Alice la credencial verificable, Woodgrove da fe de que Alice es empleada suya. Woodgrove es un emisor de credenciales verificables de confianza en la solución de validación de Proseware. Esta confianza en el proceso de emisión de Woodgrove permite a Proseware aceptar electrónicamente la credencial verificable como prueba de que Alice trabaja en Woodgrove y realizarle el descuento. Como parte de la validación de la credencial verificable que Alice presenta, Proseware comprueba la validez de la credencial verificable mediante el libro de contabilidad distribuida. En esta solución:

* Woodgrove permite a Alice proporcionar a Proseware una prueba de que trabaja para ellos sin tener que ampliar su límite de confianza. 

* Proseware no necesita expandir su límite de confianza para validar que Alice trabaja en Woodgrove. Proseware puede usar la credencial verificable que proporciona Woodgrove. Como el límite de confianza no se amplía, resulta más fácil administrar la relación de confianza y Proseware puede finalizar fácilmente la relación si deja de aceptar credenciales verificables.

* Alice no necesita proporcionar a Proseware ningún tipo de información personal, como una dirección de correo electrónico. Alice mantiene la credencial verificable en una aplicación de cartera en un dispositivo personal. La única persona que puede usar la credencial verificable es Alice y es también ella quien debe iniciar el uso de la credencial. La aplicación de cartera registra cada uso de la credencial verificable, por lo que Alice tiene un registro de cuándo y dónde se usa la credencial verificable. 

 

La combinación de arquitecturas de identidad centralizada y descentralizada para operar dentro y fuera de los límites de confianza permite reducir la complejidad y el riesgo y facilita la administración de las relaciones limitadas. 

### <a name="changes-over-time"></a>Cambios con el paso del tiempo

Woodgrove agregará y finalizará las relaciones empresariales con otras organizaciones y deberá determinar cuándo se usan las arquitecturas de identidad centralizada y descentralizada.

La combinación de arquitecturas de identidad centralizada y descentralizada permite distribuir la responsabilidad y el esfuerzo asociados a la identidad y la prueba de identidad y reducir el riesgo, y el usuario no se arriesga a divulgar su información privada con tanta frecuencia ni a tantos comprobadores desconocidos. En concreto:

* En las arquitecturas de identidad centralizada, el proveedor de identidades emite credenciales y realiza la comprobación de las credenciales emitidas. El proveedor de identidades procesa la información de todas las identidades, puede almacenarla o recuperarla de un directorio. Los proveedores de identidades también pueden aceptar dinámicamente tokens de seguridad de otros sistemas de proveedor de identidades, como inicios de sesión sociales o asociados comerciales. Para que un usuario de confianza utilice identidades en el límite de confianza del proveedor de identidades, deben configurarse para aceptar los tokens emitidos por el proveedor de identidades.

## <a name="how-decentralized-identity-systems-work"></a>Funcionamiento de los sistemas de identidad descentralizada

En las arquitecturas de identidad descentralizada, el emisor, el usuario y el usuario de confianza (RP) tienen cada uno un rol para establecer y garantizar el intercambio de confianza continuo de las credenciales de los demás. Las claves públicas de los DID de los actores se pueden resolver en ION, lo que permite la validación de firmas y, por tanto, la confianza de cualquier artefacto, incluida una credencial verificable. Los usuarios de confianza pueden consumir credenciales verificables sin tener que establecer relaciones de confianza con el emisor. En su lugar, el emisor proporciona al sujeto una credencial que este puede presentar como prueba a los usuarios de confianza. Todos los mensajes entre los actores se firman con el DID del actor; los DID de los emisores y comprobadores también deben ser propietarios de los dominios DNS que generaron las solicitudes. 

Por ejemplo: cuando los titulares de una credencial verificable necesitan acceder a un recurso, deben presentar la credencial a ese usuario de confianza. Para ello, usan una aplicación de cartera para leer la solicitud del usuario de confianza para presentar una credencial verificable. Como parte de la lectura de la solicitud, la aplicación de cartera usa el DID del usuario de confianza para buscar las claves públicas de dicho usuario mediante ION y valida que la solicitud para presentar la credencial verificable no se ha alterado. La cartera también comprueba que se hace referencia al DID en un documento de metadatos hospedado en el dominio DNS del usuario de confianza para demostrar la propiedad del dominio. 

 

![Funcionamiento de un sistema de identidad descentralizada](media/introduction-to-verifiable-credentials-architecture/how-decentralized-works.png)

### <a name="flow-1-verifiable-credential-issuance"></a>Flujo 1: emisión de una credencial verificable

En este flujo, el titular de la credencial interactúa con el emisor para solicitar una credencial verificable, como se muestra en el diagrama siguiente.

![Emisión de una credencial verificable](media/introduction-to-verifiable-credentials-architecture/issuance.png)

1. El titular inicia el flujo mediante un explorador o una aplicación nativa para acceder al front-end web del emisor. Allí, el sitio web del emisor lleva al usuario a recopilar datos y ejecuta la lógica específica del emisor para determinar si se puede emitir la credencial y su contenido. 

1. El front-end web del emisor llama al servicio de credenciales verificables de Azure AD para generar una solicitud de emisión de una credencial verificable. 

1. El front-end web representa un vínculo a la solicitud en forma de código QR o un vínculo profundo específico del dispositivo (según el dispositivo).

1. El titular lee el código QR o el vínculo profundo del paso 3 mediante una aplicación de cartera, como Microsoft Authenticator

1. La cartera descarga la solicitud del vínculo. La solicitud incluye:

   * DID del emisor. La aplicación de cartera lo usa para resolver el ION para buscar las claves públicas y los dominios vinculados.

   * Dirección URL con el manifiesto de credencial verificable, que especifica los requisitos del contrato para emitir la credencial. Aquí se pueden incluir id_token, los atributos de atestiguación automática que se deben proporcionar o la presentación de otra credencial verificable. 

   * Aspecto de la credencial verificable (dirección URL del archivo de logotipo, colores, etc.)

1. La cartera valida las solicitudes de emisión y procesa los requisitos del contrato:

   1. Valida que el mensaje de solicitud de emisión se ha firmado con las claves del emisor que se encuentran en el documento DID resuelto en ION. Esto garantiza que el mensaje no se ha alterado.

   1. Valida que el dominio DNS al que se hace referencia en el documento DID del emisor es propiedad del emisor. 

   1. En función de los requisitos del contrato de VC, la cartera puede requerir que el titular recopile información adicional, como por ejemplo, solicitar atributos de emisión automática o navegar por un flujo de OIDC para obtener id_token.

1. Envía los artefactos requeridos por el contrato al servicio de credenciales verificables de Azure AD. Este servicio devuelve la credencial verificable, firmada con la clave DID del emisor, y la cartera la almacena de forma segura.

Para más información sobre cómo crear una solución de emisión y conocer las consideraciones arquitectónicas, consulte el artículo en el que se explica cómo [planear una solución de emisión de Azure Active Directory Verifiable Credentials](plan-issuance-solution.md).

### <a name="flow-2-verifiable-credential-presentation"></a>Flujo 2: presentación de credenciales verificables

![Presentación de credenciales verificables](media/introduction-to-verifiable-credentials-architecture/presentation.png)

En este flujo, un titular interactúa con un usuario de confianza para presentar una credencial verificable como parte de sus requisitos de autorización.

1. El titular inicia el flujo mediante un explorador o una aplicación nativa para acceder al front-end web del usuario de confianza.

1. El front-end web llama al servicio de credenciales verificables de Azure AD para generar una solicitud de presentación de una credencial verificable.

1. El front-end web representa un vínculo a la solicitud en forma de código QR o un vínculo profundo específico del dispositivo (según el dispositivo).

1. El titular lee el código QR o el vínculo profundo del paso 3 mediante una aplicación de cartera, como Microsoft Authenticator

1. La cartera descarga la solicitud del vínculo. La solicitud incluye:

   * Una [solicitud basada en estándares para las credenciales](https://identity.foundation/presentation-exchange/) del tipo esquema o credencial. 

   * El DID del usuario de confianza, que la cartera busca en ION.


1. La cartera valida la solicitud de presentación y encuentra las credenciales verificables almacenadas que satisfacen la solicitud. En función de las credenciales verificables necesarias, la cartera guía al sujeto para seleccionar y dar su consentimiento para usar las credenciales verificables.

   * Una vez que el sujeto consiente el uso de la credencial verificable, la cartera genera un DID único en pares entre el sujeto y el usuario de confianza. 

   Luego, la cartera envía una carga de respuesta de presentación al servicio de credenciales verificables de Azure AD firmada por el sujeto. Contiene: 

   * Las credenciales verificables que el sujeto consintió.

   * El DID en pares generado como "sujeto" de la carga.

   * El DID del usuario de confianza como "audiencia" de la carga.

1. El servicio de credenciales verificables de Azure AD valida la respuesta enviada por la cartera. En función de cómo se haya creado la solicitud de presentación original en el paso 2, esta validación puede incluir la comprobación del estado de la credencial verificable presentada con el emisor de credenciales verificables para casos como la revocación.

1. Tras la validación, el servicio de credenciales verificables de Azure AD vuelve a llamar al usuario de confianza con el resultado. 

Para más información sobre cómo crear una solución de validación y conocer las consideraciones arquitectónicas, consulte el artículo en el que se explica [cómo planear una solución de emisión de Azure Active Directory Verifiable Credentials](plan-verification-solution.md).

## <a name="key-takeaways"></a>Puntos clave

Las arquitecturas descentralizadas se pueden usar para mejorar las soluciones existentes y proporcionar nuevas funcionalidades. 

Para cumplir las aspiraciones de la fundación [Decentralized Identity Foundation](https://identity.foundation/) (DIF) y los [objetivos de diseño](https://www.w3.org/TR/did-core/) de W3C, se debe tener en cuenta lo siguiente al crear una solución de credenciales verificable:

* No hay puntos centrales de establecimiento de confianza entre los actores del sistema. En otras palabras, los límites de confianza no se expanden a través de la federación porque los actores confían en credenciales verificables concretas.

   * ION permite la detección del identificador descentralizado (DID) de cualquier actor.

   * La solución permite a los comprobadores validar las credenciales verificables de cualquier emisor.

   * La solución no permite al emisor controlar la autorización del sujeto o del comprobador (usuario de confianza).

* Los actores operan de forma desacoplada, cada uno de ellos puede completar las tareas de sus roles.

   * Los emisores atienden todas las solicitud de credenciales verificables y no discriminan las solicitudes atendidas.

   * Los sujetos poseen su credencial verificable una vez emitida y pueden presentar su credencial verificable a cualquier comprobador.

   * Los comprobadores pueden validar cualquier credencial verificable de cualquier asunto o emisor.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la arquitectura de credenciales verificables

* [Planeamiento de una solución de emisión](plan-issuance-solution.md)

* [Planeamiento de una solución de verificación](plan-verification-solution.md)

* [Introducción a Azure Active Directory Verifiable Credentials](get-started-verifiable-credentials.md)
