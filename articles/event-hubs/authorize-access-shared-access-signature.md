---
title: Autorización del acceso con una firma de acceso compartido en Azure Event Hubs
description: En este artículo se proporciona información sobre cómo autorizar el acceso a recursos de Azure Event Hubs mediante el uso de firmas de acceso compartido (SAS).
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: cc5ed89f9197bfb79735e558b9f36e22f87eba4b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561051"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Autorización del acceso a recursos de Event Hubs mediante firmas de acceso compartido
Una firma de acceso compartido (SAS) le proporciona una manera de conceder acceso limitado a los recursos en el espacio de nombres de Event Hubs. La firma de acceso compartido protege el acceso a los recursos de Event Hubs en función de las reglas de autorización. Estas reglas se configuran en un espacio de nombres o en una entidad (centro de eventos o tema). En este artículo se proporciona información general sobre el modelo SAS y se revisan los procedimientos recomendados de SAS.

## <a name="what-are-shared-access-signatures"></a>¿Qué son las firmas de acceso compartido?
Una firma de acceso compartido (SAS) proporciona acceso delegado a los recursos de Event Hubs en función de las reglas de autorización. Una regla de autorización tiene un nombre, se asocia con unos derechos específicos e incluye un par de claves criptográficas. Para generar tokens de SAS, debe usar el nombre y la clave de la regla a través de los clientes de Event Hubs o en su propio código. A continuación, el cliente puede pasar el token a Event Hubs para demostrar la autorización para la operación solicitada.

La firma de acceso compartido es un mecanismo de autorización basado en notificaciones que utiliza tokens simples. Si utiliza SAS, las claves nunca se pasan en la conexión. Las claves se utilizan para firmar criptográficamente información que más adelante pueda verificar el servicio. El uso de SAS es similar a un esquema de nombre de usuario y contraseña donde el cliente está en posesión inmediata de un nombre de regla de autorización y una clave coincidente. SAS puede utilizarse de manera similar a un modelo de seguridad federado, donde el cliente recibe un token de acceso firmado y de tiempo limitado de un servicio de token de seguridad sin poseer en ningún momento la clave de firma.

> [!NOTE]
> Azure Event Hubs admite la autorización de los recursos de Event Hubs mediante Azure Active Directory (Azure AD). La autorización de usuarios o aplicaciones mediante un token de OAuth 2.0 devuelto por Azure AD proporciona una seguridad superior y facilidad de uso sobre las firmas de acceso compartido (SAS). Con Azure AD, no hay ninguna necesidad de almacenar los tokens en su código y arriesgarse a posibles vulnerabilidades de seguridad.
>
> Microsoft recomienda usar Azure AD con las aplicaciones de Azure Event Hubs cuando sea posible. Para más información, consulte [Autenticación del acceso al recurso de Azure Event Hubs con Azure Active Directory](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> Los tokens de SAS (firmas de acceso compartido) son fundamentales para proteger los recursos. A la vez que proporciona granularidad, SAS concede a los clientes acceso a los recursos de Event Hubs. No se deben compartir públicamente. Si resulta necesario compartirla para solucionar problemas, considere la posibilidad de usar una versión reducida de los archivos de registro o eliminar los tokens de SAS (si existen) de dichos archivos, y asegúrese de que las capturas de pantalla tampoco incluyan información de SAS.

## <a name="shared-access-authorization-policies"></a>Directivas de autorización de acceso compartido
Cada espacio de nombres y entidad de Event Hubs (una instancia de centro de eventos o un tema de Kafka) tiene una directiva de autorización de acceso compartido que se compone de reglas. La directiva a nivel de espacio de nombres se aplica a todas las entidades del espacio de nombres, independientemente de su configuración de directiva individual.
Para cada regla de directiva de autorización, decida tres tipos de información: nombre, ámbito y derechos. El nombre es un nombre único en ese ámbito. El ámbito es el URI del recurso en cuestión. Para un espacio de nombres de Event Hubs, el ámbito es el nombre de dominio completo (FQDN), como `https://<yournamespace>.servicebus.windows.net/`.

Los derechos proporcionados por la regla de directiva pueden ser una combinación de:
- **Enviar**: otorga el derecho a enviar mensajes a la entidad.
- **Escuchar**: otorga el derecho a escuchar o recibir a la entidad.
- **Administrar**: otorga el derecho a administrar la topología del espacio de nombres, incluidas la creación y la eliminación de entidades.

> [!NOTE]
> El derecho **Administrar** incluye los derechos **Enviar** y **Escuchar**.

Una directiva de espacio de nombres o entidad puede contener hasta 12 reglas de autorización de acceso compartido, lo que proporciona espacio para tres conjuntos de reglas, y cada una de ellas cubre los derechos básicos y la combinación de Enviar y Escuchar. Este límite subraya que el almacén de directivas de SAS no pretende ser un almacén de la cuenta de usuario o servicio. Si la aplicación necesita conceder acceso a Event Hubs en función de las identidades de usuario o servicio, debe implementar un servicio de token de seguridad que emita tokens de SAS después de una comprobación de acceso y autenticación.

A la regla de autorización se le asigna una **clave principal** y una **clave secundaria**. Estas claves son claves de alta seguridad criptográfica. No las pierda ni las filtre. Siempre estarán disponibles en Azure Portal. Puede usar cualquiera de las claves generadas y regenerarlas en cualquier momento. Si vuelve a generar o cambia una clave en la directiva, todos los tokens emitidos previamente en base a esa clave dejan de ser válidos inmediatamente. Sin embargo, las conexiones continuas creadas de acuerdo con esos tokens seguirán funcionando hasta que el token expire.

Cuando se crea un espacio de nombres de Event Hubs, se crea automáticamente una regla de directiva denominada **RootManageSharedAccessKey** para el espacio de nombres. Esta directiva tiene permisos de **administración** para todo el espacio de nombres. Se recomienda tratar esta regla como una cuenta raíz administrativa, así que no la use en la aplicación. Puede crear reglas de directivas adicionales en la pestaña **Configurar** para el espacio de nombres en el portal, a través de PowerShell o la CLI de Azure.

## <a name="best-practices-when-using-sas"></a>Procedimientos recomendados al usar SAS
Cuando use firmas de acceso compartido en sus aplicaciones, debe tener en cuenta dos posibles riesgos:

- Si se filtró una SAS, cualquier persona que la consiga puede usarla, lo que puede poner en riesgo sus recursos de Event Hubs.
- Si una SAS proporcionada para una aplicación cliente expira y la aplicación no puede recuperar una nueva SAS del servicio y, luego, la funcionalidad de la aplicación puede verse afectada.

Las siguientes recomendaciones para el uso de firmas de acceso compartido pueden ayudar a mitigar estos riesgos:

- **Haga que los clientes renueven automáticamente la SAS si fuese necesario**: los clientes deben renovar la SAS correctamente antes de la expiración para que exista tiempo para los reintentos si el servicio que ofrece la SAS no está disponible. Si la SAS se ha creado para usarse en un número reducido de operaciones de corta duración e inmediatas, que se espera que se va a completar dentro del tiempo de expiración determinado, es posible que no sea necesario ese procedimiento, ya que no se espera que la SAS se renueve. Sin embargo, si dispone de un cliente que realice solicitudes de forma rutinaria a través de la SAS, existe la posibilidad de la expiración. La consideración clave es equilibrar la necesidad de que la SAS sea de corta duración (como se indicó anteriormente) con el requisito de garantizar que el cliente solicitan la renovación con la suficiente antelación como para evitar la interrupción debida a la expiración de SAS antes de una renovación correcta.
- **Tenga cuidado con la hora de inicio de la SAS**: si establece la hora de inicio de la SAS en **now**, pueden producirse errores intermitentes durante los primeros minutos debido al desplazamiento del reloj (diferencias en la hora actual según las distintas máquinas). En general, establezca la hora de inicio sea al menos 15 minutos en el pasado. O, no establezca esta opción en absoluto, lo que hará que sea válido inmediatamente en todos los casos. Lo mismo suele aplicarse también a la hora de expiración. Recuerde que es posible observar hasta 15 minutos de distorsión del reloj en cualquier dirección en cualquier solicitud. 
- **Sea específico con el recurso al que se va a tener acceso**: un procedimiento recomendado de seguridad es proporcionar al usuario los privilegios mínimos necesarios. Si un usuario solo necesita acceso de lectura en una única entidad, concédale acceso de lectura a esa única entidad y no acceso de lectura, escritura o eliminación a todas las entidades. También ayuda a reducir los daños si se pone en peligro una SAS porque la SAS tiene menos poder en manos de un atacante.
- **No use siempre la SAS**: en algunas ocasiones, los riesgos asociados a una operación determinada en Event Hubs superan a las ventajas del uso de la SAS. Para esas operaciones, cree un servicio de nivel intermedio que escriba en la instancia de Event Hubs después de llevar a cabo una auditoría, autenticación o validación de la regla de negocio.
- **Siempre use HTTPS**: use siempre HTTPS para crear una SAS o distribuirla. Si se pasa una SAS a través de HTTP y se intercepta, un atacante que realice un ataque de tipo "Man in the middle" puede leer la SAS y, luego, usarla como lo podría hacer el usuario previsto. Esto puede poner en riesgo la información confidencial o permitir que un usuario malintencionado provoque daños en los datos.

## <a name="conclusion"></a>Conclusión
Las firmas de acceso compartido son útiles para proporcionar permisos limitados para los recursos de Event Hubs a los clientes. Son parte fundamental del modelo de seguridad de cualquier aplicación que usa Azure Event Hubs. Si sigue los procedimientos recomendados que se describen en este artículo, puede usar SAS para ofrecer una mayor flexibilidad de acceso a los recursos sin que se ponga en riesgo la seguridad de la aplicación.

## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos relacionados siguientes: 

- [Autenticación de solicitudes a Azure Event Hubs desde una aplicación mediante Azure Active Directory](authenticate-application.md)
- [Autenticación de una identidad administrada con Azure Active Directory para acceder a recursos de Event Hubs](authenticate-managed-identity.md)
- [Autenticación de solicitudes para Azure Event Hubs mediante firmas de acceso compartido](authenticate-shared-access-signature.md)
- [Autorización del acceso a recursos de Event Hubs mediante Azure Active Directory](authorize-access-azure-active-directory.md)


