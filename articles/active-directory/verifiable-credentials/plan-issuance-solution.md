---
title: Planeación de una solución de emisión de credenciales verificables de Azure Active Directory (versión preliminar)
description: Aprenda a planear una solución de emisión de un extremo a otro.
documentationCenter: ''
author: barbaraselden
manager: martinco
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: baselden
ms.custom: references_regions
ms.openlocfilehash: d724c41fd16ea762fc96c36e67fe4a00a5b5e72d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728166"
---
# <a name="plan-your-azure-active-directory-verifiable-credentials-issuance-solution-preview"></a>Planeación de una solución de emisión de credenciales verificables de Azure Active Directory (versión preliminar)

 >[!IMPORTANT]
> Las credenciales verificables de Azure Active Directory son una característica que se encuentra actualmente en versión preliminar. Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [**Condiciones de uso complementarias de las versiones preliminares de Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Es importante planear la solución de emisión para que, además de generar las credenciales, tenga una vista completa del impacto de la solución en la arquitectura y el negocio. Si no lo ha hecho, le recomendamos que consulte la [Introducción a la arquitectura de credenciales verificables de Azure Active Directory](introduction-to-verifiable-credentials-architecture.md) para obtener información básica.

## <a name="scope-of-guidance"></a>Ámbito de la guía

Este artículo trata sobre los aspectos técnicos de la planeación de una solución de emisión de credenciales verificables mediante productos de Microsoft para interoperar con Identity Overlay Network (ION). La solución de Microsoft para credenciales verificables sigue los estándares [Verifiable Credentials Data Model 1.0](https://www.w3.org/TR/vc-data-model/) y [Decentralized Identifiers (DID) V1.0](https://www.w3.org/TR/did-core/) de World Wide Web Consortium (W3C), por lo que puede interoperar con servicios que no son de Microsoft. Aun así, los ejemplos de este contenido reflejan la pila de soluciones de Microsoft para las credenciales verificables. 

Quedan fuera del ámbito de este contenido los temas relacionados con tecnologías complementarias que no son específicas de las soluciones de emisión. Por ejemplo, se usan sitios web en una solución de emisión de credenciales verificables, pero la planeación de la implementación de un sitio web no se trata en detalle.

## <a name="components-of-the-solution"></a>Componentes de la solución

Como parte del plan de la solución de emisión, debe diseñar una solución que permita las interacciones entre el emisor, el usuario y el comprobador. Puede emitir más de una credencial verificable. En el diagrama siguiente se muestran los componentes de la arquitectura de emisión.

### <a name="microsoft-vc-issuance-solution-architecture"></a>Arquitectura de la solución de emisión de credenciales verificables de Microsoft

![Componentes de una solución de emisión](media/plan-issuance-solution/plan-issuance-solution-architecture.png)


### <a name="azure-active-directory-tenant"></a>Inquilino de Azure Active Directory

Un requisito previo para ejecutar el servicio de credenciales verificables de Azure AD es que se hospede en un inquilino de Azure Active Directory (Azure AD). El inquilino de Azure AD proporciona un plano de control de Administración de identidades y acceso (IAM) para los recursos de Azure que forman parte de la solución.

Cada inquilino tiene una sola instancia del servicio de credenciales verificables de Azure AD y un solo identificador descentralizado (DID). El DID proporciona una prueba de que el emisor posee el dominio incorporado en el DID. El firmante y el comprobador usan el DID para validar el emisor. 

### <a name="microsoft-azure-services"></a>Servicios de Microsoft Azure

![Componentes de una solución de emisión, con un especial hincapié en los servicios de Azure](media/plan-issuance-solution/plan-issuance-solution-azure-services.png)

El servicio **Azure Key Vault** almacena las claves del emisor, que se generan al iniciar el servicio de emisión de credenciales verificables de Azure AD. Las claves y los metadatos se usan para ejecutar operaciones de administración de credenciales y proporcionar seguridad para los mensajes.

Cada emisor tiene un único conjunto de claves que se usa para la firma, la actualización y la recuperación. Este conjunto de claves se usa para cada emisión de una credencial verificable que se genere. 

**Azure Storage** se usa para almacenar definiciones y metadatos de credenciales (en concreto, las reglas y los archivos para mostrar de las credenciales). 

* Los archivos para mostrar determinan qué notificaciones se almacenan en la credencial verificable y cómo se muestra esta en la cartera del titular. El archivo para mostrar incluye también la personalización de marca y otros elementos. Los archivos de reglas tienen un tamaño limitado a 50 KB, mientras que el de los archivos para mostrar está limitado a 150 KB. Consulte [Procedimiento para personalizar las credenciales verificables](../verifiable-credentials/credential-design.md).

* Las reglas son un modelo definido por el emisor que describe las entradas necesarias de una credencial verificable, los orígenes de confianza de las entradas y la asignación de las notificaciones de entrada a las notificaciones de salida. 

   * **Entrada**: subconjunto del modelo en el archivo de reglas para el consumo del cliente. El subconjunto debe describir el conjunto de entradas, dónde se obtienen las entradas y el punto de conexión al que se va a llamar para obtener una credencial verificable.

* Las reglas y los archivos para mostrar de las distintas credenciales se pueden configurar para usar diferentes contenedores, suscripciones y almacenamiento. Por ejemplo, puede delegar permisos a distintos equipos que se encargan de la administración de credenciales verificables específicas. 

### <a name="azure-ad-verifiable-credentials-service"></a>Servicio de credenciales verificables de Azure AD

![Servicio de credenciales verificables de Microsoft Azure AD](media/plan-issuance-solution/plan-issuance-azure-active-directory-verifiable-credential-services.png)

El servicio de credenciales verificables de Azure AD le permite emitir y revocar credenciales verificables en función de su configuración. El servicio:

* Aprovisiona el identificador descentralizado (DID) y escribe el documento de DID en ION, donde lo pueden usar los firmantes y los comprobadores. Cada emisor tiene un solo DID por inquilino. 

* Aprovisiona conjuntos de claves para Key Vault. 

* Almacena los metadatos de configuración utilizados por el servicio de emisión y Microsoft Authenticator.

### <a name="ion"></a>ION

![ION](media/plan-issuance-solution/plan-issuance-solution-ion.png)

Microsoft usa [Identity Overlay Network (ION)](https://identity.foundation/ion/), una [red basada en Sidetree](https://identity.foundation/sidetree/spec/) que usa Bitcoin la cadena de bloques de Bitcoin para la implementación de identificadores descentralizados (DID). El documento de DID del emisor se almacena en ION y las partes de la transacción lo usan para realizar comprobaciones de firmas criptográficas.

### <a name="microsoft-authenticator-application"></a>Aplicación Microsoft Authenticator

![Aplicación Microsoft Authenticator](media/plan-issuance-solution/plan-issuance-solution-authenticator.png)

Microsoft Authenticator es la aplicación móvil que organiza las interacciones entre el usuario, el servicio de credenciales verificables de Azure AD y las dependencias descritas en el contrato usado para emitir credenciales verificables. Actúa como una cartera digital en la que el titular de la credencial verificable almacena la credencial en cuestión, incluida la clave privada de su firmante. Authenticator es también el mecanismo que se usa para presentar las credenciales verificables para su comprobación.

### <a name="issuance-business-logic"></a>Lógica de negocios de la emisión 

![Lógica de negocios de la emisión](media/plan-issuance-solution/plan-issuance-solution-business-logic.png)

La solución de emisión incluye un front-end web donde los usuarios solicitan una credencial verificable, un almacén de identidades y otro almacén de atributos donde obtener los valores para las notificaciones sobre el firmante y otros servicios de back-end. 

Un front-end web sirve las solicitudes de emisión a la cartera del firmante mediante la generación de vínculos profundos o códigos QR. En función de la configuración del contrato, podrían necesitarse otros componentes a fin de satisfacer los requisitos para crear una credencial verificable.

Estos servicios proporcionan funciones auxiliares que no tienen que integrarse necesariamente con ION o con el servicio de emisión de credenciales verificables de Azure AD. Esta capa normalmente incluye lo siguiente:

* Se usan **uno o varios servicios compatibles con Open ID Connect (OIDC)** para obtener los id_tokens necesarios para emitir la credencial verificable. Los sistemas de identidad existentes, como Azure AD o Azure AD B2C, pueden proporcionar el servicio compatible con OIDC, al igual que soluciones personalizadas como Identity Server.

* **Almacenes de atributos**: pueden estar fuera de los servicios de directorio y proporcionar los atributos necesarios para emitir una credencial verificable. Por ejemplo, un sistema de información para alumnos podría proporcionar notificaciones sobre los títulos obtenidos. 

* **Servicios adicionales de nivel intermedio** que contienen reglas de negocio para búsquedas, validación, facturación y cualquier otra comprobación en tiempo de ejecución, así como los flujos de trabajo necesarios para emitir credenciales.

Para obtener más información sobre cómo configurar el front-end web, consulte el tutorial [Configuración de Azure AD para emitir credenciales verificables](../verifiable-credentials/enable-your-tenant-verifiable-credentials.md). 

## <a name="credential-design-considerations"></a>Consideraciones sobre el diseño de credenciales

Los casos de uso específicos determinan el diseño de credenciales. El caso de uso determinará:

* Los requisitos de interoperabilidad

* La manera en que los usuarios tendrán que demostrar su identidad para obtener su credencial verificable

* Las notificaciones necesarias en las credenciales

* Si será necesario revocar las credenciales en algún momento

 

### <a name="credential-use-cases"></a>Casos de uso de las credenciales

Estos son los casos de uso más comunes de las credenciales verificables de Azure AD:

**Verificación de identidad**: se emite una credencial en función de varios criterios. Esto puede incluir la comprobación de la autenticidad de documentos emitidos por el gobierno, como un pasaporte o un permiso de conducir, y la asociación de la información de dicho documento con otra información, por ejemplo:

* Una foto del usuario 

* Una comprobación de vida

Este tipo de credencial es una buena opción para escenarios de incorporación de identidades de nuevos empleados, asociados, proveedores de servicios, alumnos y otras instancias en las que la comprobación de la identidad es esencial.

 

![Caso de uso de verificación de identidad](media/plan-issuance-solution/plan-issuance-solution-identity-verification-use-cases.png)

**Prueba de relación laboral o pertenencia**: se emite una credencial para demostrar una relación entre el usuario y una institución. Este tipo de credencial es una buena opción para acceder a aplicaciones de negocio a negocio de acoplamiento flexible, como minoristas que ofrecen descuentos a empleados o alumnos. Uno de los valores principales de las credenciales verificables es su portabilidad: una vez que se ha emitido una credencial verificable, el usuario puede usarla en muchos escenarios. 

![Caso de uso de prueba de relación laboral](media/plan-issuance-solution/plan-issuance-solution-employment-proof-use-cases.png)

Para conocer más casos de uso, consulte [Casos de uso de credenciales verificables (w3.org)](https://www.w3.org/TR/vc-use-cases/).

### <a name="credential-interoperability"></a>Interoperabilidad de las credenciales

Como parte del proceso de diseño, investigue los esquemas, los espacios de nombres y los identificadores específicos del sector con los que puede alinearse para maximizar la interoperabilidad y el uso. Encontrará ejemplos en [Schema.org](https://schema.org/) y en el [grupo de trabajo sobre notificaciones y credenciales de DIF](https://identity.foundation/working-groups/claims-credentials.html).

Tenga en cuenta que los esquemas comunes son un área en la que siguen emergiendo estándares. Un ejemplo de este tipo de esfuerzo es el [grupo de trabajo sobre las credenciales verificables para la educación](https://github.com/w3c-ccg/vc-ed). Le recomendamos que investigue y que realice su contribución a los estándares emergentes en el sector al que pertenece su organización.

### <a name="credential-attributes"></a>Atributos de las credenciales 

Después de establecer el caso de uso de una credencial, debe decidir qué atributos incluirá en ella. Los comprobadores pueden leer las notificaciones en la credencial verificable que presentan los usuarios.

Además de los estándares y los esquemas específicos del sector que podrían aplicarse a sus escenarios, tenga en cuenta los siguientes aspectos:

* **Minimice la información privada**: cumpla los casos de uso con la cantidad mínima necesaria de información privada. Por ejemplo, una credencial verificable empleada para sitios web de comercio electrónico que ofrece descuentos a empleados y alumnos puede cumplirse si se presenta la credencial solamente con las notificaciones de nombre y apellidos. No se necesita información adicional, como la fecha de contratación, el cargo, el departamento, etc.

* **Priorice las notificaciones abstractas**: las notificaciones deben satisfacer las necesidades y, al mismo tiempo, minimizar los detalles. Por ejemplo, una notificación denominada "ageOver" con valores discretos como "13", "21" y "60" es más abstracta que una notificación de fecha de nacimiento.

* **Planee la revocabilidad**: se recomienda definir una notificación de índice para habilitar mecanismos que permitan buscar y revocar credenciales. Como máximo, puede definir una notificación de índice por contrato. Es importante tener en cuenta que los valores de las notificaciones indexadas no se almacenan en el back-end; solo se almacena un hash del valor de notificación. Para obtener más información, consulte [Revocación de una credencial verificable emitida previamente](../verifiable-credentials/how-to-issuer-revoke.md).

Para conocer otras consideraciones sobre los atributos de las credenciales, consulte la especificación [Verifiable Credentials Data Model 1.0 (w3.org)](https://www.w3.org/TR/vc-data-model/).

## <a name="plan-quality-attributes"></a>Planeación de los atributos de calidad

### <a name="plan-for-performance"></a>Planeación del rendimiento

Al igual que sucede con cualquier solución, debe planear el rendimiento. Las áreas clave en las que debe centrarse son la latencia, el rendimiento, el almacenamiento y la escalabilidad. Durante las fases iniciales de un ciclo de versión, el rendimiento no debería ser un problema. Aun así, cuando la adopción de la solución de emisión conlleva que se emitan muchas credenciales verificables, la planeación del rendimiento podría convertirse en una parte crítica de la solución.

A continuación se indican las áreas que debe tener en cuenta al planear el rendimiento:

* El servicio de emisión de credenciales verificables de Azure AD se implementa en las regiones de Azure de Oeste de Europa, Norte de Europa, Oeste de EE. UU. 2 y Centro-oeste de EE. UU. No necesita seleccionar una región en la que implementar el servicio. 

* Para limitar la latencia, implemente el sitio web de front-end de emisión, el almacén de claves y el almacenamiento en la región indicada anteriormente que se encuentre más cerca del lugar en el que se espera que se originen las solicitudes.

Modelo basado en el rendimiento:
* El servicio de emisor está sujeto a los [límites de servicio de Azure Key Vault](../../key-vault/general/service-limits.md). 

*  En el caso de Azure Key Vault, cada emisión de credencial verificable consta de tres operaciones de firma:

      * Una para la solicitud de emisión desde el sitio web

      * Una para la credencial verificable que se crea

      * Una para la descarga del contrato

* El rendimiento máximo de la firma de Key Vault es de 2000 firmas/~10 segundos. Se trata de aproximadamente 12 000 firmas por minuto. Esto significa que la solución puede admitir hasta 4000 emisiones de credenciales verificables por minuto.

* No se puede controlar la limitación, pero se recomienda leer la [Guía de las limitaciones de Azure Key Vault](../../key-vault/general/overview-throttling.md). 

* Si planea un gran lanzamiento e incorporación de credenciales verificables, considere la posibilidad de procesar por lotes la creación de credenciales verificables para asegurarse de que no supera los límites.

* El servicio de emisión está sujeto a los límites de almacenamiento de Azure. En los casos de uso típicos, el almacenamiento no debería ser un problema. Aun así, si cree que podría superar los límites de almacenamiento o que el almacenamiento podría convertirse en un cuello de botella, revise lo siguiente: 

   * Se recomienda leer [Objetivos de escalabilidad y rendimiento de Blob Storage](../../storage/blobs/scalability-targets.md) como parte del proceso de planeación. El servicio de emisión de credenciales verificables de Azure AD lee las reglas y los archivos para mostrar y almacena en caché los resultados.

   * También se recomienda revisar la [Lista de comprobación de escalabilidad y rendimiento para Blob Storage (Azure Storage)](../../storage/blobs/storage-performance-checklist.md). 

Como parte del plan de rendimiento, determine qué supervisará para comprender mejor el rendimiento de la solución. Además de la supervisión de los sitios web del nivel de aplicación, tenga en cuenta lo siguiente al definir la estrategia de supervisión de emisión de credenciales verificables:

Para mejorar la escalabilidad, considere la posibilidad de implementar métricas para lo siguiente:

   * Definición de las fases lógicas del proceso de emisión Por ejemplo:

   * Solicitud inicial 

   * Mantenimiento del código QR o del vínculo profundo

   * Búsqueda de atributos

   * Llamadas al servicio de emisión de credenciales verificables de Azure AD

   * Credencial emitida

   * Defina métricas basadas en las fases:

      * Recuento total de solicitudes (volumen)

      * Solicitudes por unidad de tiempo (rendimiento)

      * Tiempo invertido (latencia)

* Supervise Azure Key Vault y Storage mediante lo siguiente:

   * [Supervisión y alertas de Azure Key Vault](../../key-vault/general/alert.md)

   * [Supervisión de Azure Blob Storage](../../storage/blobs/monitor-blob-storage.md)

* Supervisión de los componentes usados para la capa de lógica de negocios 

### <a name="plan-for-reliability"></a>Planeación de la confiabilidad

Para planear la confiabilidad, se recomienda lo siguiente:

* Después de definir los objetivos de disponibilidad y redundancia, use las siguientes guías para entender cómo puede alcanzar sus objetivos:

   * [Disponibilidad y redundancia de Azure Key Vault: Azure Key Vault](../../key-vault/general/disaster-recovery-guidance.md)

   * [Recuperación ante desastres y conmutación por error de la cuenta de almacenamiento: Azure Storage](../../storage/common/storage-disaster-recovery-guidance.md)

* Para el front-end y la capa empresarial, la solución se puede manifestar de numerosas maneras. Al igual que sucede con cualquier solución, asegúrese de que las dependencias que identifique sean resistentes y estén supervisadas. 

En el caso excepcional de que el servicio de emisión de credenciales verificables de Azure AD, Azure Key Vault o los servicios de Azure Storage no estén disponibles, toda la solución dejará de estar disponible.

### <a name="plan-for-compliance"></a>Planeación del cumplimiento

Su organización podría tener necesidades de cumplimiento específicas relacionadas con el sector, el tipo de transacciones o el país en el que opera. 

**Residencia de los datos**: el servicio de emisión de credenciales verificables de Azure AD se implementa en un subconjunto de regiones de Azure. El servicio solo se usa para las funciones de proceso. No almacenamos valores de credenciales verificables en sistemas de Microsoft. Aun así, como parte del proceso de emisión, se envían y se usan datos personales al emitir credenciales verificables. El uso del servicio de credenciales verificables no debería afectar a los requisitos de residencia de los datos. Si, como parte de la verificación de identidad, almacena información personal, debe guardarse en una región y de una manera acordes con los requisitos de cumplimiento. Para obtener instrucciones relacionadas con Azure, visite el sitio web del Centro de confianza de Microsoft. 

**Revocación de las credenciales**: determine si su organización necesitará revocar las credenciales. Por ejemplo, es posible que un administrador tenga que revocar las credenciales cuando un empleado abandone la empresa. O bien, si se emite una credencial para un permiso de conducir y se detecta al titular cometiendo una infracción que haría que se le retirase el permiso, es posible que la credencial verificable deba revocarse. Para obtener más información, consulte [Revocación de una credencial verificable emitida previamente](how-to-issuer-revoke.md).

**Expiración de las credenciales**: determine si le interesa que las credenciales expiren y, en caso afirmativo, en qué circunstancias. Por ejemplo, si emite una credencial verificable como prueba de que se está en posesión de un permiso de conducir, podría expirar al cabo de unos años. Si emite una credencial verificable como comprobación de una asociación con un usuario, puede que le interese que expire anualmente para asegurarse de que los usuarios obtengan cada año la versión más actualizada de la credencial verificable.

## <a name="plan-for-operations"></a>Planeamiento de las operaciones

Al planear las operaciones, es fundamental que elabore un esquema que se usará para solucionar problemas, crear informes y distinguir los diferentes clientes que admita. Además, si el equipo de operaciones es responsable de ejecutar la revocación de las credenciales verificables, ese proceso debe estar bien definido. Todos los pasos del proceso deben estar correlacionados para que pueda determinar qué entradas del registro se pueden asociar a cada solicitud de emisión única. Para la auditoría, se recomienda capturar cada intento de emisión de credenciales de forma individual. En concreto:

* Genere identificadores de transacción únicos a los que los clientes y los ingenieros de soporte técnico puedan hacer referencia según sea necesario.

* Diseñe un mecanismo para correlacionar los registros de las transacciones de Azure Key Vault con los identificadores de transacción de la parte emisora de la solución.

* Si ofrece un servicio de verificación de identidad que emite credenciales verificables en nombre de varios clientes, realice la supervisión y la mitigación por identificador de cliente o de contrato para los informes orientados al cliente y la facturación.

* Si ofrece un servicio de verificación de identidad que emite credenciales verificables en nombre de varios clientes, use el identificador de cliente o de contrato para los informes orientados al cliente, la facturación, la supervisión y la mitigación. 

## <a name="plan-for-security"></a>Planear la seguridad

Como parte de las consideraciones de diseño centradas en la seguridad, se recomienda lo siguiente:

* Para la administración de claves:

   * Cree una instancia de Key Vault dedicada para la emisión de credenciales verificables. Limite los permisos de Azure Key Vault al servicio de emisión de credenciales verificables de Azure AD y a la entidad de servicio del sitio web de front-end del servicio de emisión. 

   * Azure Key Vault emite credenciales a los clientes; trátelo como un sistema con privilegios elevados. Se recomienda que ninguna identidad humana tenga permisos permanentes en el servicio Azure Key Vault. Los administradores deberían tener únicamente acceso Just-In-Time a Key Vault. Si le interesa conocer más procedimientos recomendados par el uso de Azure Key Vault, consulte [Línea base de seguridad de Azure para Key Vault](/security/benchmark/azure/baselines/key-vault-security-baseline).

* Para la entidad de servicio que representa el sitio web de front-end de emisión:

   * Defina una entidad de servicio dedicada para autorizar el acceso a Azure Key Vault. Si el sitio web está en Azure, se recomienda usar una [identidad administrada de Azure](../managed-identities-azure-resources/overview.md). 

   * Trate la entidad de servicio que representa el sitio web y el usuario como un límite de confianza único. Aunque es posible crear varios sitios web, hay un solo conjunto de claves para la solución de emisión. 

Para el registro y la supervisión de seguridad, se recomienda lo siguiente:

* Habilite el registro y las alertas de Azure Key Vault para realizar un seguimiento de las operaciones de emisión de credenciales, los intentos de extracción de claves y los cambios de permisos, así como para supervisar y enviar alertas para los cambios de configuración. Encontrará más información en [Habilitación del registro de Key Vault](../../key-vault/general/howto-logging.md). 

* Habilite el registro de la cuenta de Azure Storage para supervisar y enviar alertas de los cambios de configuración. Encontrará más información en [Supervisión de Azure Blob Storage](../../storage/blobs/monitor-blob-storage.md).

* Archive los registros en sistemas de Administración de eventos e información de seguridad (SIEM), como [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel) para la retención a largo plazo.

* Para mitigar los riesgos de suplantación de identidad, use lo siguiente:

   * Comprobación de DNS para ayudar a los clientes a identificar la personalización de marca del emisor.

   * Nombres de dominio que tengan significado para los usuarios finales.

   * Personalización de marca de confianza que el usuario final reconozca.

* Mitigue los riesgos de denegación de servicio distribuido (DDoS) y de agotamiento de los recursos de Key Vault. Cada solicitud que desencadena una solicitud de emisión de credenciales verificables genera operaciones de firma de Key Vault que se acumulan dentro los límites del servicio. Para proteger el tráfico, se recomienda incorporar una autenticación o CAPTCHA antes de generar solicitudes de emisión.

Para obtener instrucciones sobre cómo administrar el entorno de Azure, se recomienda revisar [Azure Security Benchmark](/security/benchmark/azure/) y [Protección de entornos de Azure con Azure Active Directory](https://aka.ms/AzureADSecuredAzure). Estas guías proporcionan procedimientos recomendados para administrar los recursos subyacentes de Azure, incluidos Azure Key Vault, Azure Storage, sitios web y otros servicios y funcionalidades relacionados con Azure.

## <a name="additional-considerations"></a>Consideraciones adicionales

Cuando complete la prueba de concepto, recopile toda la información y la documentación que haya generado y considere la posibilidad de anular la configuración del emisor. De este modo, evitará emitir credenciales verificables después de que haya expirado el tiempo de vigencia de la prueba de concepto. 

Para obtener más información sobre la implementación y el funcionamiento de Key Vault, consulte [Procedimientos recomendados para usar Key Vault](../../key-vault/general/best-practices.md). Para obtener más información sobre cómo proteger entornos de Azure con Active Directory, consulte [Protección de entornos de Azure con Azure Active Directory](https://aka.ms/AzureADSecuredAzure). 

## <a name="next-steps"></a>Pasos siguientes

[Introducción a la arquitectura](introduction-to-verifiable-credentials-architecture.md)

[Planeamiento de una solución de verificación](plan-verification-solution.md)

[Introducción a las credenciales verificables](get-started-verifiable-credentials.md)
