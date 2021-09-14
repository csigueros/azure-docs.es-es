---
title: Seguridad de las bases de datos de Azure Cosmos DB
description: Obtenga información sobre cómo Azure Cosmos DB proporciona protección de bases de datos y seguridad para los datos.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: mjbrown
ms.openlocfilehash: 53e6ef24ba7f9df42ce15d62d11cf4f455825caa
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439459"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Seguridad en Azure Cosmos DB: introducción
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

En este artículo se describen los procedimientos recomendados de seguridad de bases de datos y las principales características que ofrece Azure Cosmos DB para ayudarle a evitar y detectar infracciones en bases de datos, así como a responder a estos incidentes.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Novedades en la seguridad de Azure Cosmos DB

El cifrado en reposo ahora está disponible para los documentos y copias de seguridad almacenados en Azure Cosmos DB en todas las regiones de Azure. El cifrado en reposo se aplica automáticamente a los clientes nuevos y existentes de estas regiones. No es necesario configurar nada; obtenga la misma latencia, rendimiento, disponibilidad y funcionalidad excelentes que antes con la ventaja de saber que los datos son seguros y de protegerlos con el cifrado en reposo.

## <a name="how-do-i-secure-my-database"></a>¿Cómo puedo proteger mi base de datos?

La seguridad de los datos constituye una responsabilidad compartida entre el cliente y el proveedor de base de datos. Dependiendo del proveedor de base de datos que elija, puede variar el nivel de responsabilidad que asumir. Si elige una solución local, debe proporcionar todos los elementos: desde la protección de extremo a extremo hasta la seguridad física del hardware, lo que no es tarea fácil. Si elige un proveedor de bases de datos en la nube PaaS como Azure Cosmos DB, se reduce considerablemente el área de responsabilidad. En la siguiente imagen, que se tomó prestada de las notas del producto [Shared Responsibilities for Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) (Responsabilidades compartidas de la informática en la nube), se muestra cómo se reduce la responsabilidad con un proveedor de PaaS como Azure Cosmos DB.

:::image type="content" source="./media/database-security/nosql-database-security-responsibilities.png" alt-text="Responsabilidades del cliente y del proveedor de bases de datos":::

En el diagrama anterior se muestran los componentes de seguridad en la nube de alto nivel, pero, en una solución de base de datos, ¿por qué debe preocuparse exactamente? ¿Y cómo puede comparar soluciones?

Recomendamos la siguiente lista de comprobación de requisitos para comparar sistemas de bases de datos:

- Seguridad de red y la configuración de firewall
- Autenticación de usuarios y controles de usuario muy específicos
- Capacidad de replicar datos globalmente en caso de errores regionales
- Capacidad de conmutar por error de un centro de datos a otro
- Replicación de datos local dentro de un centro de datos
- Copias de seguridad de datos automáticas
- Restauración de los datos eliminados de las copias de seguridad
- Protección y aislamiento de datos confidenciales
- Supervisión de ataques
- Respuesta a alertas
- Capacidad de aplicar límites geografos a datos para cumplir las restricciones de gobernanza de datos
- Protección física de los servidores en centros de datos protegidos
- Certificaciones

Aunque puede parecer obvio, algunas [infracciones recientes de bases de datos de gran envergadura](https://thehackernews.com/2017/01/mongodb-database-security.html) nos recuerdan lo importantes que son los siguientes requisitos (que, además, son muy fáciles de cumplir):

- Servidores revisados que se mantengan actualizados
- Protocolo HTTPS de forma predeterminada/cifrado TLS
- Cuentas administrativas con contraseñas seguras

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>¿Cómo protege Azure Cosmos DB mi base de datos?

Echemos un vistazo volver a la lista anterior, ¿cuántos de esos requisitos de seguridad proporciona Azure Cosmos DB? Todos.

Analicemos cada uno de ellas en detalle.

|Requisito de seguridad|Enfoque de seguridad de Azure Cosmos DB|
|---|---|
|Seguridad de las redes|Usar un firewall IP es la primera línea de defensa para proteger una base de datos. Azure Cosmos DB admite controles de acceso basado en IP orientado a directivas para agregar compatibilidad con el firewall de entrada. Los controles de acceso basado en IP son similares a las reglas de firewall que emplean los sistemas de base de datos tradicionales, pero se han ampliado para que una cuenta de bases de datos de Azure Cosmos sea accesible desde un conjunto aprobado de máquinas o servicios en la nube. Más información en el artículo sobre [compatibilidad con un firewall de Azure Cosmos DB](how-to-configure-firewall.md).<br><br>Azure Cosmos DB permite habilitar una dirección IP específica (168.61.48.0), un intervalo IP (168.61.48.0/8) y combinaciones de direcciones IP e intervalos. <br><br>Todas las solicitudes procedentes de máquinas que no pertenezcan a esta lista permitida las bloqueará Azure Cosmos DB. Las solicitudes de máquinas aprobadas y servicios en la nube deben completar el proceso de autenticación para poder controlar el acceso a los recursos.<br><br> Puede usar [etiquetas de servicio de red virtual](../virtual-network/service-tags-overview.md) para lograr el aislamiento de red y proteger los recursos de Azure Cosmos DB ante Internet general. Utilice etiquetas de servicio en lugar de direcciones IP específicas al crear reglas de seguridad. Al especificar el nombre de la etiqueta de servicio (por ejemplo, AzureCosmosDB) en el campo de origen o destino apropiado de una regla, puede permitir o denegar el tráfico para el servicio correspondiente.|
|Authorization|Azure Cosmos DB utiliza el código de autenticación de mensajes basado en hash (HMAC) para la autorización. <br><br>Cada solicitud se cifra mediante la clave de cuenta secreta, y el hash codificado base64 subsiguiente se envía con cada llamada a Azure Cosmos DB. Para validar la solicitud, el servicio Azure Cosmos DB utiliza la clave secreta y las propiedades correctas para generar un valor hash y, luego, compara el valor con el que muestra la solicitud. Si los dos coinciden, significa que la operación está autorizada correctamente y se procesa la solicitud, en caso contrario, se produce un error de autorización y se rechaza la solicitud.<br><br>Puede usar una [clave principal](#primary-keys) o un [token de recurso](secure-access-to-data.md#resource-tokens), lo que permite acceso muy específico a un recurso, por ejemplo, un documento.<br><br>Obtenga más información en [Protección del acceso a los recursos de Cosmos DB](secure-access-to-data.md).|
|Usuarios y permisos|Mediante la clave principal de la cuenta, puede crear recursos de usuario y de permiso por base de datos. Un token de recurso está asociado con un permiso en una base de datos y determina si el usuario tiene acceso (lectura y escritura, de solo lectura, o ninguno) aun recurso de aplicación en la base de datos. Los recursos de aplicación incluyen contenedores, documentos, datos adjuntos, procedimientos almacenados, desencadenadores y UDF. El token de recurso se usa luego durante la autenticación para proporcionar o denegar el acceso al recurso.<br><br>Obtenga más información en [Protección del acceso a los recursos de Cosmos DB](secure-access-to-data.md).|
|Integración de Active Directory (RBAC de Azure)| También puede proporcionar o restringir el acceso a la cuenta de Cosmos, la base de datos, el contenedor y las ofertas (rendimiento) mediante el control de acceso (IAM) en Azure Portal. IAM proporciona una funcionalidad de control de acceso basado en roles y se integra con Active Directory. Puede usar roles integrados o personalizados para usuarios y grupos. Consulte el artículo [Integración de Active Directory](role-based-access-control.md) para más información.|
|Replicación global|Azure Cosmos DB ofrece una distribución global llave en mano, lo que permite replicar los datos en cualquiera de los centros de datos de todo el mundo de Azure con solo hacer clic en un botón. Gracias a la replicación global, podrá realizar un escalado a nivel internacional y proporcionar un acceso de latencia baja a datos de todo el mundo.<br><br>En el contexto de seguridad, la replicación global garantiza la protección de los datos frente a errores regionales.<br><br>Obtenga más información en [Distribución de datos global con DocumentDB](distribute-data-globally.md).|
|Conmutaciones por error regionales|Si se han replicado los datos en más de un centro de datos, Azure Cosmos DB sustituirá automáticamente las operaciones en caso de que se desconecte un centro de datos regional. Puede crear una lista de prioridades de regiones de conmutación por error con las regiones en las que se replicarán los datos. <br><br>Obtenga más información en [Conmutaciones por error regionales de Azure Cosmos DB](high-availability.md).|
|Replicación local|Incluso dentro de un solo centro de datos, Azure Cosmos DB replica automáticamente los datos para lograr una alta disponibilidad, lo que posibilita diversos [niveles de coherencia](consistency-levels.md). Esta replicación garantiza un [Acuerdo de Nivel de Servicio con disponibilidad](https://azure.microsoft.com/support/legal/sla/cosmos-db) del 99,99 % para todas las cuentas de una sola región y todas las cuentas de varias regiones con coherencia moderada y disponibilidad de lectura del 99,999 % para todas las cuentas de base de datos de varias regiones.|
|Copias de seguridad en línea automatizadas|Las copias de seguridad de las bases de datos de Azure Cosmos se realizan de manera periódica y se guardan en un almacén con redundancia geográfica. <br><br>Obtenga más información en [Copias de seguridad y restauración automáticas en línea con Azure Cosmos DB](online-backup-and-restore.md).|
|Restauración de los datos eliminados|Las copias de seguridad automatizadas en línea se pueden utilizar para recuperar los datos que puede que haya eliminado accidentalmente hasta unos 30 días después del suceso. <br><br>Obtenga más información en [Copias de seguridad y restauración automáticas en línea con Azure Cosmos DB](online-backup-and-restore.md).|
|Protección y aislamiento de datos confidenciales|Todos los datos de las regiones incluidos en Novedades ahora se cifran en reposo.<br><br>Los datos personales y otros datos confidenciales se pueden aislar en contenedores específicos. Además, se puede limitar el acceso de escritura-lectura o de solo lectura a usuarios concretos.|
|Supervisión de los ataques|Use los [registros de auditoría y los registros de actividad](./monitor-cosmos-db.md) para supervisar la actividad normal y la anómala de su cuenta. Puede ver qué operaciones se realizaron en los recursos, quién inició la operación, cuando se produjo, el estado y mucha más información, como se muestra en la captura de pantalla que sigue a esta tabla.|
|Respuesta a ataques|Una vez que se ha puesto en contacto con el equipo de asistencia técnica de Azure para informar de un posible ataque, se inicia un proceso de respuesta a incidentes de 5 pasos. El objetivo de dicho proceso es restaurar las operaciones y la seguridad de los servicios a su estado normal lo antes posible después de que se detecte un problema y se inicie una investigación.<br><br>Obtenga más información en [Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) (Respuesta de seguridad de Microsoft Azure en la nube).|
|Geovalla|Azure Cosmos DB garantiza la gobernanza de datos para regiones soberanas (por ejemplo, Alemania y US Gov).|
|Instalaciones protegidas|Los datos de Azure Cosmos DB se almacenan en los SSD de los centros de datos protegidos de Azure.<br><br>Obtenga información sobre los [centros de datos globales de Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters).|
|HTTPS, SSL y cifrado TLS|Todas las conexiones a Azure Cosmos DB admiten HTTPS. Azure Cosmos DB también admite TLS 1.2.<br>Es posible aplicar una versión de TLS mínima en el servidor. Para ello, póngase en contacto con [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com).|
|Cifrado en reposo|Todos los datos almacenados en Azure Cosmos DB se cifran en reposo. Más información en [Cifrado de Azure Cosmos DB en reposo](./database-encryption-at-rest.md)|
|Servidores revisados|Como una base de datos administrada, Azure Cosmos DB elimina la necesidad de administrar y aplicar revisiones a servidores, que se hace automáticamente.|
|Cuentas administrativas con contraseñas seguras|Es difícil creer que tengamos que hacer mención a este requisito, pero a diferencia de algunos de nuestros competidores, no se puede tener una cuenta administrativa sin contraseña en Azure Cosmos DB.<br><br> La seguridad mediante TLS y autenticación basada en secreto HMAC está incorporada de forma predeterminada.|
|Certificaciones de protección de datos y seguridad| Para disponer de la lista de certificaciones más actualizada, consulte el [sitio de cumplimiento de Azure](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) global así como el [documento de cumplimiento de Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) más reciente con todas las certificaciones (busque Cosmos). Para una lectura más centrada, consulte la publicación del 25 de abril de 2018 [Azure #CosmosDB: seguro, privado y compatible que incluye SOCS 1/2 Tipo 2, HITRUST, PCI DSS Nivel 1, ISO 27001, HIPAA, FedRAMP High, etc.

En la captura de pantalla siguiente se muestra cómo puede usar los registros de actividad y los registro de auditoría para supervisar su cuenta: :::image type="content" source="./media/database-security/nosql-database-security-application-logging.png" alt-text="registros de actividad de Azure Cosmos DB":::

<a id="primary-keys"></a>

## <a name="primarysecondary-keys"></a>Claves principal o secundaria

Las claves principal o secundaria proporcionan acceso a todos los recursos administrativos de la cuenta de base de datos. Claves principal o secundaria:

- Proporcionan acceso a cuentas, bases de datos, usuarios y permisos. 
- No se pueden usar para proporcionar acceso pormenorizado a contenedores y documentos.
- Se crean durante la creación de una cuenta.
- Se pueden regenerar en cualquier momento.

Cada cuenta consta de dos claves: una principal y una secundaria. El fin de las claves dobles es que pueda regenerar, o distribuir claves, lo que proporciona un acceso continuo a su cuenta y sus datos.

Las claves principal y secundaria se incluyen en dos versiones: de lectura y escritura y de solo lectura. Las claves de solo lectura solo permiten operaciones de lectura en la cuenta, pero no proporcionan acceso a los recursos de permisos de lectura.

### <a name="key-rotation-and-regeneration"></a><a id="key-rotation"></a> Rotación y regeneración de claves

El proceso de rotación y regeneración de claves es sencillo. En primer lugar, asegúrese de que **la aplicación usa de forma coherente la clave principal o la secundaria** para acceder a la cuenta de Azure Cosmos DB. Después, siga los pasos que se describen a continuación.

# <a name="sql-api"></a>[SQL API](#tab/sql-api)

#### <a name="if-your-application-is-currently-using-the-primary-key"></a>Si la aplicación usa actualmente la clave principal

1. Vaya a la cuenta de Azure Cosmos DB en Azure Portal.

1. Seleccione **Claves** en el menú de la izquierda y después **Regenerar clave secundaria** en los puntos suspensivos situados a la derecha de la clave secundaria.

    :::image type="content" source="./media/database-security/regenerate-secondary-key.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave secundaria" border="true":::

1. Compruebe que la nueva clave secundaria funciona de forma coherente con la cuenta de Azure Cosmos DB. La regeneración de claves puede tardar entre un minuto y varias horas, en función del tamaño de la cuenta de Cosmos DB.

1. Reemplace la clave principal por la clave secundaria en la aplicación.

1. Vuelva a Azure Portal y desencadene la regeneración de la clave principal.

    :::image type="content" source="./media/database-security/regenerate-primary-key.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave principal" border="true":::

#### <a name="if-your-application-is-currently-using-the-secondary-key"></a>Si la aplicación usa actualmente la clave secundaria

1. Vaya a la cuenta de Azure Cosmos DB en Azure Portal.

1. Seleccione **Claves** en el menú de la izquierda y después **Regenerar clave principal** en los puntos suspensivos situados a la derecha de la clave principal.

    :::image type="content" source="./media/database-security/regenerate-primary-key.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave principal" border="true":::

1. Compruebe que la nueva clave principal funciona de forma coherente con la cuenta de Azure Cosmos DB. La regeneración de claves puede tardar entre un minuto y varias horas, en función del tamaño de la cuenta de Cosmos DB.

1. Reemplace la clave secundaria por la clave principal en la aplicación.

1. Vuelva a Azure Portal y desencadene la regeneración de la clave secundaria.

    :::image type="content" source="./media/database-security/regenerate-secondary-key.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave secundaria" border="true":::

# <a name="azure-cosmos-db-api-for-mongodb"></a>[Azure Cosmos DB API para MongoDB](#tab/mongo-api)

#### <a name="if-your-application-is-currently-using-the-primary-key"></a>Si la aplicación usa actualmente la clave principal

1. Vaya a la cuenta de Azure Cosmos DB en Azure Portal.

1. Seleccione **Cadena de conexión** en el menú de la izquierda y después **Regenerar contraseña** en los puntos suspensivos situados a la derecha de la contraseña secundaria.

    :::image type="content" source="./media/database-security/regenerate-secondary-key-mongo.png" alt-text="Captura de pantalla de Azure Portal en la que muestra se cómo regenerar la clave secundaria" border="true":::

1. Compruebe que la nueva clave secundaria funciona de forma coherente con la cuenta de Azure Cosmos DB. La regeneración de claves puede tardar entre un minuto y varias horas, en función del tamaño de la cuenta de Cosmos DB.

1. Reemplace la clave principal por la clave secundaria en la aplicación.

1. Vuelva a Azure Portal y desencadene la regeneración de la clave principal.

    :::image type="content" source="./media/database-security/regenerate-primary-key-mongo.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave principal" border="true":::

#### <a name="if-your-application-is-currently-using-the-secondary-key"></a>Si la aplicación usa actualmente la clave secundaria

1. Vaya a la cuenta de Azure Cosmos DB en Azure Portal.

1. Seleccione **Cadena de conexión** en el menú de la izquierda y después **Regenerar contraseña** en los puntos suspensivos situados a la derecha de la contraseña principal.

    :::image type="content" source="./media/database-security/regenerate-primary-key-mongo.png" alt-text="Captura de pantalla de Azure Portal en la que muestra se cómo regenerar la clave principal" border="true":::

1. Compruebe que la nueva clave principal funciona de forma coherente con la cuenta de Azure Cosmos DB. La regeneración de claves puede tardar entre un minuto y varias horas, en función del tamaño de la cuenta de Cosmos DB.

1. Reemplace la clave secundaria por la clave principal en la aplicación.

1. Vuelva a Azure Portal y desencadene la regeneración de la clave secundaria.

    :::image type="content" source="./media/database-security/regenerate-secondary-key-mongo.png" alt-text="Captura de pantalla de Azure Portal en la que muestra se cómo regenerar la clave secundaria" border="true":::

# <a name="cassandra-api"></a>[Cassandra API](#tab/cassandra-api)

#### <a name="if-your-application-is-currently-using-the-primary-key"></a>Si la aplicación usa actualmente la clave principal

1. Vaya a la cuenta de Azure Cosmos DB en Azure Portal.

1. Seleccione **Cadena de conexión** en el menú de la izquierda y después **Regenerar una contraseña secundaria de lectura y escritura** en los puntos suspensivos situados a la derecha de la contraseña secundaria.

    :::image type="content" source="./media/database-security/regenerate-secondary-key-cassandra.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave secundaria" border="true":::

1. Compruebe que la nueva clave secundaria funciona de forma coherente con la cuenta de Azure Cosmos DB. La regeneración de claves puede tardar entre un minuto y varias horas, en función del tamaño de la cuenta de Cosmos DB.

1. Reemplace la clave principal por la clave secundaria en la aplicación.

1. Vuelva a Azure Portal y desencadene la regeneración de la clave principal.

    :::image type="content" source="./media/database-security/regenerate-primary-key-cassandra.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave principal" border="true":::

#### <a name="if-your-application-is-currently-using-the-secondary-key"></a>Si la aplicación usa actualmente la clave secundaria

1. Vaya a la cuenta de Azure Cosmos DB en Azure Portal.

1. Seleccione **Cadena de conexión** en el menú de la izquierda y después **Regenerar una contraseña principal de lectura y escritura** en los puntos suspensivos situados a la derecha de la contraseña principal.

    :::image type="content" source="./media/database-security/regenerate-primary-key-cassandra.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave principal" border="true":::

1. Compruebe que la nueva clave principal funciona de forma coherente con la cuenta de Azure Cosmos DB. La regeneración de claves puede tardar entre un minuto y varias horas, en función del tamaño de la cuenta de Cosmos DB.

1. Reemplace la clave secundaria por la clave principal en la aplicación.

1. Vuelva a Azure Portal y desencadene la regeneración de la clave secundaria.

    :::image type="content" source="./media/database-security/regenerate-secondary-key-cassandra.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave secundaria" border="true":::

# <a name="gremlin-api"></a>[Gremlin API](#tab/gremlin-api)

#### <a name="if-your-application-is-currently-using-the-primary-key"></a>Si la aplicación usa actualmente la clave principal

1. Vaya a la cuenta de Azure Cosmos DB en Azure Portal.

1. Seleccione **Claves** en el menú de la izquierda y después **Regenerar clave secundaria** en los puntos suspensivos situados a la derecha de la clave secundaria.

    :::image type="content" source="./media/database-security/regenerate-secondary-key-gremlin.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave secundaria" border="true":::

1. Compruebe que la nueva clave secundaria funciona de forma coherente con la cuenta de Azure Cosmos DB. La regeneración de claves puede tardar entre un minuto y varias horas, en función del tamaño de la cuenta de Cosmos DB.

1. Reemplace la clave principal por la clave secundaria en la aplicación.

1. Vuelva a Azure Portal y desencadene la regeneración de la clave principal.

    :::image type="content" source="./media/database-security/regenerate-primary-key-gremlin.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave principal" border="true":::

#### <a name="if-your-application-is-currently-using-the-secondary-key"></a>Si la aplicación usa actualmente la clave secundaria

1. Vaya a la cuenta de Azure Cosmos DB en Azure Portal.

1. Seleccione **Claves** en el menú de la izquierda y después **Regenerar clave principal** en los puntos suspensivos situados a la derecha de la clave principal.

    :::image type="content" source="./media/database-security/regenerate-primary-key-gremlin.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave principal" border="true":::

1. Compruebe que la nueva clave principal funciona de forma coherente con la cuenta de Azure Cosmos DB. La regeneración de claves puede tardar entre un minuto y varias horas, en función del tamaño de la cuenta de Cosmos DB.

1. Reemplace la clave secundaria por la clave principal en la aplicación.

1. Vuelva a Azure Portal y desencadene la regeneración de la clave secundaria.

    :::image type="content" source="./media/database-security/regenerate-secondary-key-gremlin.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave secundaria" border="true":::

# <a name="table-api"></a>[Table API](#tab/table-api)

#### <a name="if-your-application-is-currently-using-the-primary-key"></a>Si la aplicación usa actualmente la clave principal

1. Vaya a la cuenta de Azure Cosmos DB en Azure Portal.

1. Seleccione **Cadena de conexión** en el menú de la izquierda y después **Regenerar clave secundaria** en los puntos suspensivos situados a la derecha de la clave secundaria.

    :::image type="content" source="./media/database-security/regenerate-secondary-key-table.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave secundaria" border="true":::

1. Compruebe que la nueva clave secundaria funciona de forma coherente con la cuenta de Azure Cosmos DB. La regeneración de claves puede tardar entre un minuto y varias horas, en función del tamaño de la cuenta de Cosmos DB.

1. Reemplace la clave principal por la clave secundaria en la aplicación.

1. Vuelva a Azure Portal y desencadene la regeneración de la clave principal.

    :::image type="content" source="./media/database-security/regenerate-primary-key-table.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave principal" border="true":::

#### <a name="if-your-application-is-currently-using-the-secondary-key"></a>Si la aplicación usa actualmente la clave secundaria

1. Vaya a la cuenta de Azure Cosmos DB en Azure Portal.

1. Seleccione **Cadena de conexión** en el menú de la izquierda y después **Regenerar clave principal** en los puntos suspensivos situados a la derecha de la clave principal.

    :::image type="content" source="./media/database-security/regenerate-primary-key-table.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave principal" border="true":::

1. Compruebe que la nueva clave principal funciona de forma coherente con la cuenta de Azure Cosmos DB. La regeneración de claves puede tardar entre un minuto y varias horas, en función del tamaño de la cuenta de Cosmos DB.

1. Reemplace la clave secundaria por la clave principal en la aplicación.

1. Vuelva a Azure Portal y desencadene la regeneración de la clave secundaria.

    :::image type="content" source="./media/database-security/regenerate-secondary-key-table.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra cómo regenerar la clave secundaria" border="true":::

---

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las claves principales y los tokens de recursos, consulte [Protección del acceso a los datos de Azure Cosmos DB](secure-access-to-data.md).

Para más información sobre el registro de auditoría, consulte [Registro de diagnóstico de Azure Cosmos DB](./monitor-cosmos-db.md).

Para más información sobre las certificaciones de Microsoft, visite el [Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/).