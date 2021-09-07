---
title: Planeación de una solución de comprobación de Azure Active Directory Verifiable Credentials (versión preliminar)
description: Obtenga información básica para planear y diseñar una solución de comprobación.
documentationCenter: ''
author: barbaraselden
manager: martinco
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: baselden
ms.custom: references_regions
ms.openlocfilehash: 0bc7f6a01dc2e537e91f162ba76eb6e505518747
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746010"
---
# <a name="plan-your-azure-active-directory-verifiable-credentials--verification-solution-preview"></a>Planeación de una solución de comprobación de Azure Active Directory Verifiable Credentials (versión preliminar)

>[!IMPORTANT]
> Las credenciales verificables de Azure Active Directory son una característica que se encuentra actualmente en versión preliminar. Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

El servicio Azure Active Directory Verifiable Credentials (Azure AD VC) de Microsoft le permite confiar en las pruebas de identidad de los usuarios sin expandir los límites de confianza, mediante la creación de cuentas o la federación con otro proveedor de identidades. Al usar credenciales verificables basadas en un estándar abierto, un intercambio de comprobación permite a las aplicaciones solicitar credenciales que no están enlazadas a un dominio específico. Esto facilita la solicitud y comprobación de credenciales a gran escala.

Si aún no lo ha hecho, le recomendamos que revise la [Introducción a la arquitectura de Azure AD Verifiable Credentials](introduction-to-verifiable-credentials-architecture.md). También puede revisar [Planeación de una solución de emisión de Azure AD Verifiable Credentials](plan-issuance-solution.md).

## <a name="scope-of-guidance"></a>Ámbito de la guía

Este contenido abarca los aspectos técnicos de la planeación de una solución de comprobación de credenciales verificables (VC) mediante productos y servicios de Microsoft. La solución se comunica con Identity Overlay Network (ION), que funciona como infraestructura de clave pública descentralizada (DPKI). 

Las tecnologías complementarias que no son específicas de las soluciones de comprobación quedan fuera del ámbito. Por ejemplo, los sitios web se usan en una solución de comprobación de credenciales verificable, pero la planeación de una implementación de sitios web no se trata en detalle.

Al planear la solución de comprobación, debe tener en cuenta qué funcionalidad empresarial se está agregando o modificando, y qué funcionalidades de TI se pueden aprovechar o se deben agregar para crear la solución. También debe tener en cuenta qué formación es necesaria para las personas involucradas en el proceso empresarial, así como para las personas que brindan asistencia a los usuarios finales y al personal de la solución. Estos temas no se abordan en este contenido. Se recomienda revisar el [Marco de buena arquitectura de Microsoft Azure](/azure/architecture/framework/) para obtener información sobre estos temas.

## <a name="components-of-the-solution"></a>Componentes de la solución

Como parte del plan para una solución de comprobación, tiene que habilitar las interacciones entre el comprobador, el sujeto y el emisor. En este artículo, los términos usuario de confianza y comprobador se usan indistintamente. En el diagrama siguiente se muestran los componentes de la arquitectura de comprobación.

![Componentes de una solución de comprobación](media/plan-verification-solution/verification-solution-architecture.png)


### <a name="azure-ad-verifiable-credentials-service"></a>Servicio Azure AD Verifiable Credentials

En el contexto de una solución de comprobador, el servicio Azure AD Verifiable Credentials es la interfaz entre los componentes de Microsoft de la solución e ION. El servicio aprovisiona el conjunto de claves en Key Vault, aprovisiona el identificador descentralizado (DID) y escribe el documento de DID en ION, donde los sujetos y emisores pueden usarlo. 

### <a name="azure-active-directory-tenant"></a>Inquilino de Azure Active Directory 

El servicio requiere un inquilino de Azure AD que proporcione un plano de control de Administración de identidades y acceso (IAM) para los recursos de Azure que forman parte de la solución. Hay una única instancia del servicio Azure AD VC dentro de un inquilino, y emite un único documento de DID que representa al comprobador. Si tiene varios usuarios de confianza que usan el servicio de comprobación, todos usan el mismo DID comprobador. El DID comprobador proporciona punteros a la clave pública que permite a los sujetos y emisores validar los mensajes que proceden del usuario de confianza.

### <a name="azure-key-vault"></a>Azure Key Vault

![Azure Key Vault](./media/plan-verification-solution/verification-solution-key-vault.png)

El servicio Azure Key Vault almacena las claves del comprobador, que se generan al habilitar el servicio de emisión de Azure AD Verifiable Credentials. Las claves se usan para proporcionar la seguridad de los mensajes. Cada comprobador tiene un único conjunto de claves que se usa para firmar, actualizar y recuperar credenciales de verificación. Este conjunto de claves se usa cada vez que entrega una solicitud de comprobación. Actualmente, el conjunto de claves de Microsoft usa la criptografía de curva elíptica (ECC) [SECP256k1](https://en.bitcoin.it/wiki/Secp256k1). Estamos explorando otros esquemas de firma criptográfica que adoptará la comunidad DID más amplia.

### <a name="azure-ad-vc-apis-and-sdks"></a>API y SDK de Azure AD VC

![API y SDK de Azure AD VC](./media/plan-verification-solution/verification-solution-tools.png)

Las interfaces de programación de aplicaciones (API) y un kit de desarrollo de software (SDK) ofrecen a los desarrolladores un método para abstraer las interacciones entre los componentes de la solución para ejecutar operaciones de comprobación. 

### <a name="ion"></a>ION 

![ION de Azure AD VC](./media/plan-verification-solution/verification-solution-ion.png)

Las soluciones de credenciales verificables usan un sistema de libro de contabilidad descentralizado para registrar las transacciones. Microsoft usa [Identity Overlay Network (ION)](https://identity.foundation/ion/), una [red basada en Sidetree](https://identity.foundation/sidetree/spec/) que usa Bitcoin como libro de contabilidad con estilo de cadena de bloques para la implementación de identificadores descentralizados (DID). El documento de DID del emisor se almacena en ION, y las partes de la transacción lo usan para realizar comprobaciones de firmas criptográficas.

### <a name="microsoft-authenticator-application"></a>Aplicación Microsoft Authenticator

![Aplicación Microsoft Authenticator](media/plan-verification-solution/verification-solution-authenticator.png)

Microsoft Authenticator es la aplicación móvil que organiza las interacciones entre el usuario de confianza, el usuario, el servicio de emisión de Azure AD Verifiable Credentials y las dependencias descritas en el contrato usado para emitir credenciales verificables. Sirve de cartera digital en la que el titular de la credencial verificable la almacena. Es también el mecanismo usado para presentar las credenciales verificables para su comprobación.

### <a name="relying-party-rp"></a>Usuario de confianza 

![Componentes del usuario de confianza](media/plan-verification-solution/verification-solution-relying-party.png)

#### <a name="web-front-end"></a>Front end web

El front-end web del usuario de confianza usa las API o el SDK de Azure AD VC para comprobar las credenciales verificables mediante la generación de vínculos profundos o códigos QR que consume la cartera del sujeto. En función del escenario, el front-end puede ser un sitio web interno o de acceso público para habilitar experiencias de usuario final que requieren comprobación. Sin embargo, los puntos de conexión a los que accede la cartera deben ser accesibles públicamente. En concreto, controla el redireccionamiento a la cartera con parámetros de solicitud específicos. Esto se logra mediante las API y el SDK proporcionados por Microsoft.

#### <a name="business-logic"></a>Lógica de negocios 

Puede crear una nueva lógica, o usar una ya existente que sea específica del usuario de confianza y mejorarla con la presentación de credenciales verificables.

## <a name="scenario-specific-designs"></a>Diseños específicos del escenario

A continuación se muestran ejemplos de diseños para satisfacer casos de uso específicos. El primero corresponde a la incorporación de cuentas, que se usa para reducir el tiempo, los costos y los riesgos asociados a la incorporación de nuevos empleados. El segundo es para la recuperación de cuentas, que permite a un usuario final recuperar o desbloquear su cuenta a través de un mecanismo de autoservicio. El tercero es para acceder a recursos y aplicaciones de alto valor, específicamente para casos de uso de negocio a negocio en los que se proporciona acceso a personas que trabajan para otras empresas. 

### <a name="account-onboarding"></a>Incorporación de cuentas

Las credenciales verificables también se pueden usar para permitir una incorporación más rápida, ya que se reemplazan algunas interacciones humanas. Las credenciales verificables se pueden usar para incorporar empleados, alumnos, ciudadanos u otros usuarios para que accedan a los servicios. Por ejemplo, en lugar de que un empleado tenga que ir a una oficina central para activar un distintivo de empleado, puede usar una credencial verificable que compruebe su identidad para activar una distintivo que se le entregue de forma remota. En lugar de que un ciudadano reciba un código que tenga que canjear para acceder a los servicios gubernamentales, puede usar una credencial verificable para demostrar su identidad y obtener acceso. 

![Escenario de incorporación de cuentas](media/plan-verification-solution/verification-solution-onboarding.png)

#### <a name="additional-elements"></a>Elementos adicionales 

**Portal de incorporación**: se trata de un front-end web que organiza las llamadas a las API o SDK de Azure AD VC para la presentación y validación de credenciales verificables, y la lógica para incorporar cuentas.

**Lógica o flujos de trabajo personalizados**: lógica específica con pasos específicos de la organización antes y después de actualizar la cuenta de usuario. Puede incluir flujos de trabajo de aprobación, validaciones adicionales, registros, notificaciones, etc.

**Sistemas de identidad de destino**: repositorios de identidades específicos de la organización con los que el portal de incorporación tiene que interactuar durante la incorporación de los sujetos. Los sistemas que se van a integrar se determinan en función de los tipos de identidades que quiere incorporar con la validación de credenciales verificables. Entre los escenarios comunes de verificación de identidades para la incorporación se incluyen:

* Identidades externas, como proveedores, asociados, proveedores y clientes, que en sistemas de identidad centralizados se incorporan a Azure AD mediante API para emitir invitaciones de negocio a negocio (B2B), o asignaciones de administración de derechos a paquetes. 

* Identidades de empleado, que en sistemas de identidad centralizados ya se incorporan a través de sistemas de recursos humanos (RR. HH.). En este caso, la verificación de identidad podría integrarse como parte de las fases existentes de los flujos de trabajo de RR. HH. 

#### <a name="design-considerations"></a>Consideraciones de diseño

* **Emisor**: la incorporación de cuentas es una buena opción para un servicio de revisión de identidades externas como emisor de las credenciales verificables. Entre los ejemplos de comprobaciones para la incorporación se incluyen: comprobación de identidad, validación de documentos emitidos por el Gobierno, confirmación de direcciones o números de teléfono, etc.

* **Almacenamiento de atributos de credenciales verificables**: siempre que sea posible, no almacene atributos de las credenciales verificables en el almacén específico de la aplicación. Tenga especial cuidado con los datos personales. Si esta información es necesaria para flujos específicos dentro de las aplicaciones, considere la posibilidad de solicitar que la credencial verificable que recupere las notificaciones a petición. 

* **Correlación del atributo de credenciales verificables con sistemas back-end**: al definir los atributos de la credencial verificable con el emisor, establezca un mecanismo para correlacionar la información en el sistema back-end después de que el usuario presente la credencial verificable. Normalmente, se usa un identificador único con límite de tiempo en el contexto del usuario de confianza en combinación con las notificaciones que recibe. He aquí algunos ejemplos:

   * **Nuevo empleado**: cuando el flujo de trabajo de RR. HH. alcanza el punto en el que se requiere la revisión de identidades, el usuario de confianza puede generar un vínculo con un identificador único con límite de tiempo y enviarlo a la dirección de correo electrónico del candidato en el sistema de RR. HH. Este identificador único debe ser suficiente para correlacionar información como firstName, lastName de la solicitud de comprobación de la credencial verificable con el registro de RR. HH. o los datos subyacentes. Los atributos de la credencial verificable se pueden usar para completar los atributos de usuario en el sistema de RR. HH. o para validar la precisión de los atributos de usuario sobre el empleado.

   * **Identidades externas** - invitación: cuando un usuario existente de la organización invita a un usuario externo a incorporarse al sistema de destino, el usuario de confianza puede generar un vínculo con un identificador único que represente la transacción de invitación y la envíe a la dirección de correo electrónico del usuario externo. Este identificador único debe ser suficiente para correlacionar la solicitud de comprobación de la credencial verificable con el registro de invitación o los datos subyacentes, y continuar con el flujo de trabajo de aprovisionamiento. Los atributos de la credencial verificable se pueden usar para validar o completar los atributos de usuario externos.

   * **Identidades externas**, autoservicio: cuando las identidades externas se registran en el sistema de destino mediante el autoservicio (por ejemplo, una aplicación B2C), los atributos de la credencial verificable se pueden usar para rellenar los atributos iniciales de la cuenta de usuario. Los atributos de la credencial verificable también se pueden usar para averiguar si ya existe un perfil.

* **Interacción con sistemas de identidad de destino**: la comunicación de servicio a servicio entre el front-end web y los sistemas de identidad de destino debe protegerse como sistema con privilegios elevados, ya que puede crear cuentas. Conceda al front-end web los roles con menos privilegios posibles. Estos son algunos ejemplos:

   * Para crear un nuevo usuario en Azure AD, el sitio web del usuario de confianza puede usar una entidad de servicio a la que se conceda el ámbito de MS Graph User.ReadWrite.All para crear usuarios, y el ámbito UserAuthenticationMethod.ReadWrite.All para restablecer el método de autenticación.

   * Para invitar usuarios a Azure AD mediante la colaboración B2B, el sitio web del usuario de confianza puede usar una entidad de servicio a la que se conceda el ámbito de Graph MS User.Invite.All para crear invitaciones.

   * Si el usuario de confianza se ejecuta en Azure, use Identidades administradas para llamar a Microsoft Graph; esto eliminará los riesgos de administrar las credenciales de la entidad de servicio en los archivos de código o de configuración. Para más información sobre las identidades administradas, vaya a [¿Qué son las identidades administradas de recursos de Azure?](../managed-identities-azure-resources/overview.md)

### <a name="accessing-high-value-applications-inside-organizations"></a>Acceso a aplicaciones de alto valor dentro de las organizaciones 

Las credenciales verificables se pueden usar como prueba adicional para acceder a aplicaciones confidenciales dentro de una organización. Por ejemplo, las credenciales verificables también se pueden usar para proporcionar a los empleados acceso a aplicaciones de línea de negocio basadas en la obtención de criterios específicos, como una certificación.

![Acceso dentro de los límites de confianza](media/plan-verification-solution/inside-trust-boundary-access.png)

#### <a name="additional-elements"></a>Elementos adicionales 

**Front-end web del usuario de confianza**: Se trata del front-end web de la aplicación que se mejora mediante las llamadas API o el SDK de Azure AD Verifiable Credentials para la presentación y validación de credenciales verificables, en función de los requisitos empresariales.

**Lógica de autorización de acceso de usuario**: Capa lógica de la aplicación que autoriza el acceso de los usuarios y que se mejora para consumir los atributos de usuario dentro de la credencial verificable para tomar decisiones de autorización. 

**Otros servicios y dependencias de back-end**: Representa el resto de la lógica de la aplicación, que normalmente no se modifica mediante la inclusión de pruebas de identidad a través de credenciales verificables.

#### <a name="design-considerations"></a>Consideraciones de diseño

* **Objetivo**: El objetivo del escenario determina qué tipo de credencial y emisor son necesarios. Entre los escenarios típicos se incluyen:

   * **Autorización**: En este escenario, el usuario presenta la credencial verificable para tomar una decisión de autorización. Las credenciales verificables diseñadas para la prueba de finalización de un entrenamiento o de posesión de una certificación específica son una buena opción para este escenario. Los atributos de las credenciales verificables deben contener información específica que favorezcan las decisiones de autorización y la auditoría. Por ejemplo, si la credencial verificable se usa para certificar que la persona está entrenada y puede acceder a aplicaciones financieras confidenciales, la lógica de la aplicación puede comprobar la notificación del departamento para obtener una autorización detallada, y usar el identificador de empleado con fines de auditoría. 

   * **Confirmación de la comprobación de identidad**: En este escenario, el objetivo es confirmar que la misma persona que se incorporó inicialmente es realmente la que intenta acceder a la aplicación de alto valor. Una credencial de un emisor de verificación de identidad sería una buena opción, y la lógica de la aplicación debería validar que los atributos de la credencial verificable se alineen con el usuario que inició sesión en la aplicación. 

* **Comprobación de revocación**: Cuando se usan credenciales verificables para acceder a recursos confidenciales, es habitual comprobar el estado de la credencial verificable con el emisor original y denegar el acceso a las credenciales verificables revocadas. Al trabajar con los emisores, asegúrese de que la revocación se analice explícitamente como parte del diseño del escenario. 

* **Experiencia del usuario**: Cuando se usan credenciales verificables para acceder a recursos confidenciales, hay dos patrones que puede tener en cuenta. 

   * **Autenticación paso a paso**: Los usuarios inician la sesión con la aplicación mediante los mecanismos de autenticación existentes. Los usuarios tienen que presentar una credencial verificable para operaciones específicas de alto valor dentro de la aplicación, como aprobaciones de flujos de trabajo de negocio. Se trata de una buena opción para escenarios en los que estas operaciones de gran valor son fáciles de identificar y actualizar dentro de los flujos de la aplicación.

   * **Establecimiento de sesión**: Los usuarios deben presentar una credencial verificable como parte del inicio de una sesión con la aplicación. Esto es una buena opción cuando la naturaleza de toda la aplicación es de alto valor. 

### <a name="accessing-applications-outside-organization-boundaries"></a>Acceso a aplicaciones fuera de los límites de la organización

Los usuarios de confianza que quieren conceder acceso o ventajas en función de la relación de pertenencia o empleo de una organización diferente también pueden usar las credenciales verificables. Por ejemplo, un portal de comercio electrónico puede ofrecer ventajas, como descuentos a los empleados de una empresa determinada, alumnos de una institución determinada, etc. 

La naturaleza descentralizada de las credenciales verificables permite este escenario sin establecer relaciones de federación. 

![Acceso fuera de los límites de confianza](media/plan-verification-solution/outside-trust-boundary-access.png)

#### <a name="additional-elements"></a>Elementos adicionales 

**Front-end web del usuario de confianza**: Se trata del front-end web de la aplicación que se mejora mediante las llamadas API o el SDK de Azure AD Verifiable Credentials para la presentación y validación de credenciales verificables, en función de los requisitos empresariales.

**Lógica de autorización de acceso de usuario**: Capa lógica de la aplicación que autoriza el acceso de los usuarios y que se mejora para consumir los atributos de usuario dentro de la credencial verificable para tomar decisiones de autorización.

**Otros servicios y dependencias de back-end**: Representa el resto de la lógica de la aplicación, que normalmente no se modifica mediante la inclusión de pruebas de identidad a través de credenciales verificables.

#### <a name="design-considerations"></a>Consideraciones de diseño

* **Objetivo**: El objetivo del escenario determina qué tipo de credencial y emisor son necesarios. Entre los escenarios típicos se incluyen:

   * **Autenticación**: En este escenario, un usuario debe tener posesión de una credencial verificable para demostrar el empleo o la relación con una organización determinada. En este caso, el usuario de confianza debe configurarse para aceptar las credenciales verificables emitidas por las organizaciones de destino. 

   * **Autorización**: En función de los requisitos de cada aplicación, las aplicaciones pueden consumir los atributos de la credencial verificable para las decisiones de autorización detalladas y la auditoría. Por ejemplo, si un sitio web de comercio electrónico ofrece descuentos a los empleados de las organizaciones en una ubicación determinada, se puede validar en función de la notificación de país en la credencial verificable (si existe).

* **Comprobación de revocación**: Cuando se usan credenciales verificables para acceder a recursos confidenciales, es habitual comprobar el estado de la credencial verificable con el emisor original y denegar el acceso a las credenciales verificables revocadas. Al trabajar con los emisores, asegúrese de que la revocación se analice explícitamente como parte del diseño del escenario. 

* **Experiencia del usuario**: Los usuarios pueden presentar una credencial verificable como parte del inicio de una sesión con la aplicación. Normalmente, las aplicaciones también proporcionan un método alternativo para iniciar la sesión para permitir los casos en los que los usuarios no tienen credenciales verificables. 


### <a name="account-recovery"></a>Recuperación de cuentas

Las credenciales verificables se pueden usar como enfoque para la recuperación de cuentas. Por ejemplo, cuando un usuario tiene que recuperar su cuenta, puede acceder a un sitio web que requiere que presente una credencial verificable e inicie un restablecimiento de credenciales de Azure AD mediante una llamada a las API de MS Graph, como se muestra en el diagrama siguiente.

Nota: Aunque el escenario que se describe en esta sección es específico para recuperar cuentas de Azure AD, este enfoque también se puede usar para recuperar cuentas en otros sistemas.

![Solución de recuperación de cuentas](media/plan-verification-solution/account-recovery.png)

#### <a name="additional-elements"></a>Elementos adicionales

**Portal de cuentas**: Se trata de un front-end web que organiza las llamadas API o SDK para la presentación y validación de credenciales verificables. Esta orquestación puede incluir llamadas de Microsoft Graph para recuperar cuentas en Azure AD.

**Lógica o flujos de trabajo personalizados**: Lógica con pasos específicos de la organización antes y después de actualizar la cuenta de usuario. Puede incluir flujos de trabajo de aprobación, validaciones adicionales, registros, notificaciones, etc.

**Microsoft Graph**: Expone las API de Transferencia de estado representacional (REST) y las bibliotecas cliente para acceder a los datos de Azure AD que se usan para la recuperación de cuentas. 

**Directorio empresarial de Azure AD**: Es el inquilino de Azure AD que contiene las cuentas que se crean o actualizan a través del portal de cuentas.

#### <a name="design-considerations"></a>Consideraciones de diseño

**Correlación del atributo de credencial verificable con Azure AD**: Al definir los atributos de la credencial verificable en colaboración con el emisor, establezca un mecanismo para correlacionar la información con sistemas internos en función de las notificaciones de la credencial verificable y la entrada del usuario. Por ejemplo, si tiene un proveedor de verificación de identidad (IDV) que comprueba la identidad antes de incorporar a los empleados, asegúrese de que la credencial verificable emitida incluya notificaciones que también estarían presentes en un sistema interno, como un sistema de recursos humanos, para la correlación. Puede tratarse de un número de teléfono, una dirección o una fecha de nacimiento. Además de las notificaciones en la credencial verificable, el usuario de confianza puede solicitar información, como los últimos 4 dígitos de su número de seguridad social (SSN), como parte de este proceso.

**Rol de las credenciales verificables con funcionalidades existentes de restablecimiento de credenciales de Azure AD**: Azure AD tiene una funcionalidad integrada de autoservicio de restablecimiento de contraseña (SSPR). Las credenciales verificables se pueden usar para brindar una manera adicional de recuperación, especialmente en los casos en los que los usuarios no tienen acceso al método SSPR o han perdido el control de este método, por ejemplo, han perdido el equipo y el dispositivo móvil. En este escenario, el usuario puede volver a obtener una credencial verificable de un emisor de comprobación de identidad y presentarla para recuperar su cuenta. 

Del mismo modo, puede usar una credencial verificable para generar un pase de acceso temporal que permitirá a los usuarios restablecer sus métodos de autenticación MFA sin una contraseña. 

**Autorización**: Cree un mecanismo de autorización, como un grupo de seguridad, que el usuario de confianza compruebe antes de continuar con la recuperación de credenciales. Por ejemplo, solo los usuarios de grupos específicos pueden ser aptos para recuperar una cuenta con una credencial verificable.

**Interacción con Azure AD**: La comunicación de servicio a servicio entre el front-end web y Azure AD debe protegerse como un sistema con privilegios elevados, dado que puede restablecer las credenciales de los empleados. Conceda al front-end web los roles con menos privilegios posibles. Estos son algunos ejemplos:

* Conceda al sitio web del usuario de confianza la capacidad de usar una entidad de servicio a la que se le haya concedido el ámbito UserAuthenticationMethod.ReadWrite.All de MS Graph para restablecer los métodos de autenticación. No conceda User.ReadWrite.All, que permite crear y eliminar usuarios.

* Si el usuario de confianza se ejecuta en Azure, use identidades administradas para llamar a Microsoft Graph. Esto elimina los riesgos en torno a la administración de las credenciales de las entidades de servicio en archivos de código o de configuración. Para más información, consulte [Identidades administradas para los recursos de Azure](../managed-identities-azure-resources/overview.md).

## <a name="plan-for-identity-management"></a>Planeación de la administración de identidades 

A continuación se muestran algunas consideraciones sobre IAM al incorporar credenciales de confianza a los usuarios de confianza. En general, los usuarios de confianza son aplicaciones. 

### <a name="authentication"></a>Authentication

* El sujeto de una credencial verificable debe ser una persona. 

* La persona titular de la credencial verificable, que tiene la credencial verificable en su cartera, debe presentarla de manera interactiva. No se admiten flujos no interactivos, como "en nombre de". 

### <a name="authorization"></a>Authorization 

* Una presentación correcta de la credencial verificable se puede considerar una puerta de autorización general por sí misma. Los atributos de la credencial verificable también se pueden consumir para tomar decisiones de autorización detalladas.

* Decida si una credencial verificable expirada tiene sentido en su aplicación; si es así, compruebe el valor de la notificación "exp" (la hora de expiración) de la credencial verificable como parte de las comprobaciones de autorización. Un ejemplo donde la expiración no es pertinente es cuando se exige un documento emitido por el Gobierno, como un permiso de conducir, para validar si el sujeto es mayor de 18 años. La notificación de fecha de nacimiento es válida, incluso si la credencial verificable ha expirado. 

* Determine si una credencial verificable revocada tiene sentido para la decisión de autorización. 

   * Si no es pertinente, omita la llamada para comprobar la API de estado (que está activada de manera predeterminada). 

   * Si es pertinente, agregue el control adecuado de las excepciones a la aplicación.

### <a name="user-profiles"></a>Perfiles de usuario

Puede usar la información de las credenciales verificables presentadas para crear un perfil de usuario. Si quiere consumir atributos para crear un perfil, tenga en cuenta lo siguiente.

* Cuando se emite la credencial verificable, contiene una instantánea de los atributos en el momento de la emisión. Las credenciales verificables pueden tener largos períodos de validez, por lo que es necesario determinar la antigüedad de los atributos que aceptará como suficientemente nuevos para usarlos como parte del perfil. 

* Si es necesario presentar una credencial verificable cada vez que el sujeto inicia una sesión con el usuario de confianza, considere la posibilidad de usar la salida de la presentación de la credencial verificable para crear un perfil de usuario no persistente con los atributos. Esto ayuda a reducir los riesgos de privacidad asociados al almacenamiento de propiedades de usuario en reposo. Si la aplicación tiene que conservar localmente los atributos del sujeto, almacene solo el conjunto mínimo de notificaciones que requiere la aplicación (en lugar de almacenar todo el contenido de la credencial verificable).

* Si la aplicación requiere un almacén de perfiles de usuario persistente:

   * Considere la posibilidad de usar la notificación "sub" como identificador inmutable del usuario. Se trata de un atributo único opaco que será constante para un par sujeto/usuario de confianza determinado. 

   * Defina un mecanismo para desaprovisionar el perfil de usuario de la aplicación. Debido a la naturaleza descentralizada del sistema Azure AD Verifiable Credentials, no hay ningún ciclo de vida de aprovisionamiento de usuarios en la aplicación.

   * No almacene las notificaciones de datos personales devueltas en el token de la credencial verificable.

   * Almacene solo las notificaciones necesarias para la lógica del usuario de confianza. 

## <a name="plan-for-performance"></a>Planeación del rendimiento

Al igual que sucede con cualquier solución, debe planear el rendimiento. Entre las áreas donde enfocarse se incluyen la latencia, el rendimiento, el almacenamiento y la escalabilidad. Durante las fases iniciales de un ciclo de publicación, el rendimiento no debería ser una inquietud. Aun así, cuando la adopción de la solución de emisión conlleve la emisión de muchas credenciales verificables, la planeación del rendimiento podría convertirse en parte crítica de la solución.

A continuación se indican las áreas que debe tener en cuenta al planear el rendimiento:

* El servicio de emisión de Azure AD Verifiable Credentials se implementa en las regiones de Azure de Oeste de Europa, Norte de Europa, Oeste de EE. UU. 2 y Centro-oeste de EE. UU. Para limitar la latencia, implemente el front-end (sitio web) de verificación y el almacén de claves en la región indicada anteriormente que se encuentre más cerca del lugar en el que se espera que se originen las solicitudes.

* Modelo basado en el rendimiento:

   * La capacidad de comprobación de credenciales verificables está sujeto a los [límites de servicio de Azure Key Vault](../../key-vault/general/service-limits.md). 

   * Cada comprobación de una credencial verificable requiere una operación de firma de Key Vault.

   * El rendimiento máximo de la firma de una instancia de Key Vault es de 2000 firmas/~10 segundos. Esto significa que la solución puede admitir hasta 12 000 solicitudes de validación de credenciales verificables por minuto.

   * No se puede controlar la limitación; sin embargo, se recomienda leer la [guía de las limitaciones de Key Vault](../../key-vault/general/overview-throttling.md) para comprender cómo la limitación puede afectar al rendimiento. 

## <a name="plan-for-reliability"></a>Planeación de la confiabilidad

Para planear mejor la alta disponibilidad y recuperación ante desastres, se recomienda lo siguiente:

* El servicio Azure AD Verifiable Credentials se implementa en las regiones de Azure de Oeste de Europa, Norte de Europa, Oeste de EE. UU. 2 y Centro-oeste de EE. UU. Considere la posibilidad de implementar los servidores web compatibles y las aplicaciones compatibles en una de esas regiones, específicamente en aquellas desde las que espera que se origine la mayor parte del tráfico de validación. 

* Revise e incorpore los procedimientos recomendados de [disponibilidad y redundancia de Azure Key Vault](../../key-vault/general/disaster-recovery-guidance.md) a medida que diseñe para cumplir los objetivos de disponibilidad y redundancia.

## <a name="plan-for-security"></a>Planear la seguridad

A medida que diseñe para cumplir con la seguridad, tenga en cuenta lo siguiente:

* Todos los usuarios de confianza de un solo inquilino tienen el mismo límite de confianza, ya que comparten el mismo DID.

* Defina una entidad de servicio dedicada para un sitio web que acceda a Key Vault.

* Solo el servicio Azure AD Verifiable Credentials y las entidades de servicio del sitio web deben tener permisos para usar Key Vault para firmar mensajes con la clave privada. 

* No asigne ningún permiso administrativo de identidad humana a Key Vault. Para más información sobre los procedimientos recomendados de Key Vault, consulte [Línea de base de seguridad de Azure para Key Vault](../../key-vault/general/security-baseline.md).

* Revise [Protección de entornos de Azure con Azure Active Directory](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/) para conocer los procedimientos recomendados para administrar los servicios compatibles de la solución. 

* Para mitigar los riesgos de suplantación de seguridad:

   * Implemente la comprobación de DNS para ayudar a los clientes a identificar la personalización de marca del emisor.

   * Use dominios que tengan significado para los usuarios finales.

* Mitigue los riesgos de denegación de servicio distribuido (DDOS) y de limitación de recursos de Key Vault. Cada solicitud de presentación de credenciales verificables genera operaciones de firma de Key Vault que se acumulan dentro de los límites del servicio. Para proteger el tráfico, se recomienda incorporar una autenticación alternativa o CAPTCHA antes de generar solicitudes de emisión.

## <a name="plan-for-operations"></a>Planeación de las operaciones

A medida que planee las operaciones, se recomienda que capture cada intento de validación de credenciales como parte de la auditoría. Use esa información para auditorías y solución de problemas. Además, considere la posibilidad de generar identificadores (Id.) de transacción únicos a los que los clientes e ingenieros de soporte técnico puedan hacer referencia de ser necesario. 

Como parte del planeamiento operativo, considere supervisar lo siguiente:

* **Para la escalabilidad**:

   * Supervise la validación de credenciales verificables con errores como parte de las métricas de seguridad de las aplicaciones de un extremo a otro.

   * Supervise la latencia de la comprobación de credenciales de un extremo a otro.

* **Para la confiabilidad y las dependencias**:

   * Supervise las dependencias subyacentes que usa la solución de comprobación. 

   * Siga [Supervisión y alertas de Azure Key Vault](../../key-vault/general/alert.md).

* **Para la seguridad**:

   * Habilite el registro para Key Vault para hacer un seguimiento de las operaciones de firma, así como para supervisar y alertar sobre los cambios de configuración. Para más información, consulte [Habilitación del registro de Key Vault](../../key-vault/general/howto-logging.md).

   * Archive los registros en sistemas de Administración de eventos e información de seguridad (SIEM), como [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/), para la retención a largo plazo.

 

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la arquitectura de las soluciones de credenciales verificables

   * [Introducción a la arquitectura de Azure AD Verifiable Credentials](introduction-to-verifiable-credentials-architecture.md)

   * [Planeación de una solución de emisión de Azure AD Verifiable Credentials](plan-issuance-solution.md)

Implementación de Verifiable Credentials

   * [Introducción a las credenciales verificables de Azure Active Directory (versión preliminar)](decentralized-identifier-overview.md) 

   * [Introducción a Verifiable Credentials](get-started-verifiable-credentials.md)

[Preguntas más frecuentes](verifiable-credentials-faq.md)