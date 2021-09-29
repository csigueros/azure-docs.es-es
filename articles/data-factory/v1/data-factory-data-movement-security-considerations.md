---
title: Consideraciones de seguridad para el movimiento de datos en Azure Data Factory
description: Información acerca de cómo proteger el movimiento de datos en Azure Data Factory.
author: nabhishek
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 0823c085470e83c82164fa578f1465e95eda81f0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596208"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory: consideraciones de seguridad para el movimiento de datos

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si usa la versión actual del servicio Data Factory, consulte el artículo acerca de las [consideraciones de seguridad para el movimiento de datos de Data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Introducción
En este artículo se describe la infraestructura de seguridad básica que utilizan los servicios de movimiento de datos en Azure Data Factory para proteger los datos. Los recursos de administración de Azure Data Factory están integrados en la infraestructura de seguridad de Azure y aplican todas las medidas de seguridad que ofrece Azure.

En una solución de Data Factory, se crean una o varias [canalizaciones](data-factory-create-pipelines.md)de datos. Una canalización es una agrupación lógica de actividades que realizan una tarea. Estas canalizaciones residen en la región donde se creó la factoría de datos. 

Aunque Data Factory solamente está disponible en las regiones **Oeste de EE. UU.** , **Este de EE.UU.** y **Norte de Europa**, el servicio de movimiento de datos está disponible [globalmente en varias regiones](data-factory-data-movement-activities.md#global). El servicio de Data Factory garantiza que los datos no abandonan un área geográfica o región, a menos que se indique explícitamente al servicio que utilice una región alternativa si el servicio de movimiento de datos no se ha implementado todavía en esa región. 

Azure Data Factory en sí no almacena datos, excepto las credenciales de servicio vinculadas de los almacenes de datos en la nube, que se cifran mediante certificados. Permite crear flujos de trabajo controlados por datos para orquestar el movimiento de los datos entre los [almacenes de datos admitidos](data-factory-data-movement-activities.md#supported-data-stores-and-formats) y organizar el procesamiento de los datos mediante [servicios de proceso](data-factory-compute-linked-services.md) en otras regiones o en entornos locales. También permite [supervisar y administrar flujos de trabajo](data-factory-monitor-manage-pipelines.md) mediante mecanismos de programación y la interfaz de usuario.

El movimiento de datos mediante Azure Data Factory tiene la **certificación**:
-    [HIPAA/HITECH](/compliance/regulatory/offering-hipaa-hitech)  
-    [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-    [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-    [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Si está interesado en el cumplimiento de Azure y en cómo Azure protege su propia infraestructura, visite el [Centro de confianza de Microsoft](https://microsoft.com/en-us/trustcenter/default.aspx). 

En este artículo, revisamos las consideraciones de seguridad en los dos escenarios de movimiento de datos siguientes: 

- **Escenario de nube**: en este escenario, el origen y el destino son públicamente accesibles a través de Internet. Por ejemplo, los servicios de almacenamiento administrado en la nube, como Azure Storage, Azure Synapse Analytics, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, los servicios SaaS como Salesforce y los protocolos web, como FTP y OData. [Aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) encontrará una lista integral de los orígenes de datos admitidos.
- **Escenario híbrido**: en este escenario, el origen o el destino está detrás de un firewall o dentro de una red corporativa local, o el almacén de datos se encuentra en una red privada o virtual (normalmente, el origen) y no es accesible públicamente. Los servidores de base de datos hospedados en máquinas virtuales también se incluyen en este escenario.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Escenarios de nube
### <a name="securing-data-store-credentials"></a>Protección de las credenciales del almacén de datos
Azure Data Factory protege las credenciales del almacén de datos al **cifrarlos** con **certificados administrados por Microsoft**. Estos certificados se alternan cada **dos años** (lo cual incluye la renovación del certificado y la migración de las credenciales). Estas credenciales cifradas se almacenan de forma segura en una instancia de **Azure Storage administrada por los servicios de administración de Azure Data Factory**. Para más información acerca de la seguridad de Azure Storage, consulte [Introducción a la seguridad de Azure Storage](../../storage/blobs/security-recommendations.md).

### <a name="data-encryption-in-transit"></a>Cifrado de datos en tránsito
Si el almacén de datos en la nube es compatible con HTTPS o TLS, todas las transferencias de datos entre los servicios de movimiento de datos de Data Factory y un almacén de datos en la nube se realizan a través del canal seguro HTTPS o TLS.

> [!NOTE]
> Todas las conexiones a **Azure SQL Database** y **Azure Synapse Analytics** requieren cifrado (SSL/TLS) siempre que haya datos en tránsito hacia y desde la base de datos. Al crear una canalización con un editor de JSON, agregue la propiedad **encryption** y establézcala en **true** en la **cadena de conexión**. Cuando se usa el [Asistente para copia](data-factory-azure-copy-wizard.md), el asistente establece esta propiedad de forma predeterminada. Para **Azure Storage**, puede usar **HTTPS** en la cadena de conexión.

### <a name="data-encryption-at-rest"></a>Cifrado de datos en reposo
Algunos almacenes de datos admiten el cifrado de datos en reposo. Se recomienda habilitar el mecanismo de cifrado de datos para estos almacenes. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics
El Cifrado de datos transparente (TDE) de Azure Synapse Analytics ayuda a proteger frente a la amenaza de actividad malintencionada al realizar el cifrado y descifrado en tiempo real de los datos en reposo. Este comportamiento es transparente para el cliente. Para obtener más información, consulte [Protección de una base de datos en Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database admite también el Cifrado de datos transparente (TDE), que ayuda a proteger frente a la amenaza de actividad malintencionada al realizar el cifrado y descifrado en tiempo real de los datos sin que haya que efectuar cambios en la aplicación. Este comportamiento es transparente para el cliente. Para más información, consulte [Transparent Data Encryption with Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) (Cifrado de datos transparente con Azure SQL Database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store también ofrece el cifrado de los datos que se almacenan en la cuenta. Cuando se habilita, Data Lake Store cifrará automáticamente los datos antes de la persistencia y los descifrará antes de la recuperación, por lo que resulta un proceso completamente transparente para el cliente que accede a los datos. Para más información, consulte [Seguridad en Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage y Azure Table Storage
Azure Blob Storage y Azure Table Storage admiten el cifrado del servicio Storage (SSE), que cifra automáticamente los datos antes de su persistencia en el almacenamiento y los descifra antes de su recuperación. Para más información, consulte [Cifrado del servicio Azure Storage para datos en reposo](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 admite el cifrado de cliente y servidor para datos en reposo. Para más información, consulte [Protección de datos del cliente mediante cifrado](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Actualmente, Data Factory no admite Amazon S3 en Virtual Private Cloud (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift admite cifrado de clúster para datos en reposo. Para más información, consulte [Amazon Redshift Database Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html) (Cifrado de base de datos de Amazon Redshift). Actualmente, Data Factory no admite Amazon Redshift en VPC. 

#### <a name="salesforce"></a>Salesforce
Salesforce admite Shield Platform Encryption, que permite el cifrado de todos los archivos, datos adjuntos y campos personalizados. Para más información, consulte [Understanding the Web Server OAuth Authentication Flow](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm) (Descripción del flujo de autenticación OAuth de servidor web).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Escenarios híbridos (con Data Management Gateway)
Los escenarios híbridos necesitan Data Management Gateway instalado en una red local o virtual (Azure), o en una nube privada virtual (Amazon). La puerta de enlace debe tener acceso a los almacenes de datos locales. Para más información sobre la puerta de enlace, consulte [Data Management Gateway](data-factory-data-management-gateway.md). 

:::image type="content" source="media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png" alt-text="Canales de Data Management Gateway":::

El **canal del comandos** permite la comunicación entre los servicios de movimiento de datos de Data Factory y Data Management Gateway. La comunicación contiene información relacionada con la actividad. El canal de datos se usa para transferir datos entre los almacenes de datos locales y los almacenes de datos en la nube.    

### <a name="on-premises-data-store-credentials"></a>Credenciales de los almacenes de datos locales
Las credenciales de los almacenes de datos locales no se almacenan en la nube, sino de manera local. Pueden establecerse de tres maneras distintas. 

- Con **texto sin formato** (menos seguro) a través de HTTPS desde Azure Portal o el Asistente para copia. Las credenciales se pasan en texto sin formato a la puerta de enlace local.
- Con la **biblioteca de criptografía de JavaScript del Asistente para copia**.
- Con la **aplicación de administración de credenciales con un clic**. La aplicación de un clic se ejecuta en la máquina local con acceso a la puerta de enlace y establece las credenciales para el almacén de datos. Esta opción y la siguiente son las más seguras. La aplicación de administración de credenciales usa el puerto 8050 de la máquina con la puerta de enlace para una comunicación segura de manera predeterminada.  
- Use el cmdlet [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) de PowerShell para cifrar las credenciales. El cmdlet usa el certificado cuyo uso tiene configurado esa puerta de enlace para cifrar las credenciales. Puede usar las credenciales cifradas que devuelve este cmdlet y agregarlas al elemento **EncryptedCredential** de **connectionString** en el archivo JSON que se usa con el cmdlet [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) o en el fragmento JSON en el Editor de Data Factory del portal. Esta opción y la aplicación de un solo clic son las más seguras. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Cifrado basado en la biblioteca de criptografía de JavaScript
Puede cifrar las credenciales del almacén de datos con la [biblioteca de criptografía de JavaScript](https://www.microsoft.com/download/details.aspx?id=52439) del [Asistente para copia](data-factory-copy-wizard.md). Cuando se selecciona esta opción, el Asistente para copia recupera la clave pública de la puerta de enlace y la usa para cifrar las credenciales del almacén de datos. La máquina de puerta de enlace descifra las credenciales y la [DPAPI](/previous-versions/ms995355(v=msdn.10)) de Windows las protege.

**Exploradores admitidos:** IE8, IE9, IE10, IE11, Microsoft Edge y las versiones más recientes de los exploradores Firefox, Chrome, Opera y Safari. 

#### <a name="click-once-credentials-manager-app"></a>Aplicación de administración de credenciales con un clic
Puede iniciar la aplicación de administración de credenciales con un clic desde Azure Portal o el Asistente para copia al autorizar canalizaciones. Esta aplicación garantiza que las credenciales no se transfieren en texto sin formato con la conexión. De manera predeterminada, usa el puerto **8050** de la máquina con la puerta de enlace para una comunicación segura. Si es necesario, se puede cambiar este puerto.  
  
:::image type="content" source="media/data-factory-data-movement-security-considerations/https-port-for-gateway.png" alt-text="Puerto HTTPS para la puerta de enlace":::

Actualmente, Data Management Gateway utiliza un solo **certificado**. Este certificado se crea durante la instalación de la puerta de enlace (aplicable a las versiones de Data Management Gateway creadas desde noviembre de 2016 y la versión 2.4.xxxx.x o posteriores). Puede reemplazar este certificado por su propio certificado SSL/TLS. La aplicación de administración con un solo clic usa este certificado para la conexión segura a la máquina de puerta de enlace con el fin de establecer las credenciales del almacén de datos. Las credenciales del almacén de datos se almacenan localmente de manera segura gracias a la [DPAPI](/previous-versions/ms995355(v=msdn.10)) de Windows de la máquina con la puerta de enlace. 

> [!NOTE]
> Las puertas de enlace instaladas antes de noviembre de 2016 y la versión 2.3.xxxx.x seguirán usando las credenciales cifradas y almacenadas en la nube. Aunque actualice la puerta de enlace a la versión más reciente, las credenciales no se migran a una máquina local.    
  
| Versión de la puerta de enlace (durante la creación) | Credenciales almacenadas | Cifrado/seguridad de las credenciales | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | En la nube | Cifrado mediante certificado (diferente del de la aplicación de administración de credenciales) | 
| > = 2.4.xxxx.x | En el entorno local | Protección con la DPAPI | 
  

### <a name="encryption-in-transit"></a>Cifrado en tránsito
Todas las transferencias de datos se realizan a través del canal seguro **HTTPS** y **TLS sobre TCP** para evitar ataques tipo "Man in the middle" durante la comunicación con los servicios de Azure.
 
También puede usar una [conexión VPN de IPSec](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) o [ExpressRoute](../../expressroute/expressroute-introduction.md) para proteger aún más el canal de comunicación entre la red local y Azure.

Una red virtual es una representación lógica de la red en la nube. Puede conectar una red local a la red virtual de Azure (VNet) mediante la configuración de una conexión VPN de IPSec (de sitio a sitio) o ExpressRoute (Emparejamiento privado)        

En la tabla siguiente se resumen las recomendaciones de configuración de red y puerta de enlace en función de diferentes combinaciones de ubicaciones de origen y destino para el movimiento de datos híbridos.

| Source | Destination | Network configuration (Configuración de red) | Instalación de la puerta de enlace |
| ------ | ----------- | --------------------- | ------------- | 
| Local | Máquinas virtuales y servicios en la nube implementados en redes virtuales | VPN de IPSec (de punto a sitio o de sitio a sitio) | La puerta de enlace se puede instalare una máquina local o virtual de Azure (VM) en una red virtual | 
| Local | Máquinas virtuales y servicios en la nube implementados en redes virtuales | ExpressRoute (Emparejamiento privado) | La puerta de enlace se puede instalare una máquina local o virtual de Azure en una red virtual | 
| Local | Servicios basados en Azure que tienen un punto de conexión público | ExpressRoute (Emparejamiento público) | La puerta de enlace debe estar instalada en la máquina local | 

Las siguientes imágenes muestran el uso de Data Management Gateway para mover datos entre una base de datos local y servicios de Azure mediante ExpressRoute y conexión VPN de IPSec (con Virtual Network):

**ExpressRoute:**
 
:::image type="content" source="media/data-factory-data-movement-security-considerations/express-route-for-gateway.png" alt-text="Uso de ExpressRoute con la puerta de enlace"::: 

**Conexión VPN de IPSec:**

:::image type="content" source="media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png" alt-text="Conexión VPN de IPSec con la puerta de enlace":::

### <a name="firewall-configurations-and-filtering-ip-address-of-gateway"></a>Configuraciones de firewall y filtrado de direcciones IP de puerta de enlace

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisitos de firewall para redes locales o privadas    
En una empresa, se ejecuta un **firewall corporativo** en el enrutador central de la organización y el **firewall de Windows** se ejecuta como demonio en la máquina local con la puerta de enlace instalada. 

En la tabla siguiente se proporcionan el **puerto de salida** y los requisitos de dominio para el **firewall corporativo**.

| Nombres de dominio | Puertos de salida | Descripción |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Lo necesita la puerta de enlace para conectarse a los servicios de movimiento de datos de Data Factory. |
| `*.core.windows.net` | 443 | Lo usa la puerta de enlace para conectarse a la cuenta de Azure Storage cuando se usa la característica [Copia almacenada provisionalmente](data-factory-copy-activity-performance.md#staged-copy). | 
| `*.frontend.clouddatahub.net` | 443 | Lo necesita la puerta de enlace para conectarse al servicio Azure Data Factory. | 
| `*.database.windows.net` | 1433    | (OPCIONAL) Necesario cuando el destino es Azure SQL Database o Azure Synapse Analytics. Usa la característica de copia de almacenamiento temporal para copiar datos en Azure SQL Database o Azure Synapse Analytics sin abrir el puerto 1433. | 
| `*.azuredatalakestore.net` | 443 | (OPCIONAL) Necesario cuando el destino es Azure Data Lake Store. | 

> [!NOTE] 
> Puede que tenga que administrar puertos o dominios de filtrado en el nivel del firewall corporativo según los requisitos de los respectivos orígenes de datos. En esta tabla, Azure SQL Database, Azure Synapse Analytics y Azure Data Lake Store solo se usan a modo de ejemplo.   

En la tabla siguiente se proporcionan los requisitos del **puerto de entrada** para el **firewall de Windows**.

| Puertos de entrada | Descripción | 
| ------------- | ----------- | 
| 8050 (TCP) | Lo necesita la aplicación de administración de credenciales para establecer de forma segura las credenciales para los almacenes de datos locales en la puerta de enlace. | 

:::image type="content" source="media/data-factory-data-movement-security-considerations/gateway-port-requirements.png" alt-text="Requisitos de puerto de la puerta de enlace":::

#### <a name="ip-configurationsfiltering-in-data-store"></a>Configuraciones o filtrado de IP en el almacén de datos
Algunos almacenes de datos en la nube también exigen la aprobación de las direcciones IP de la máquina que accede a ellos. Asegúrese de que la dirección IP de la máquina de puerta de enlace esté aprobada o configurada adecuadamente en el firewall.

Los siguientes almacenes de datos en la nube exigen la aprobación de las direcciones IP de la máquina de puerta de enlace. De forma predeterminada, algunos de estos almacenes de datos pueden no necesitar la aprobación de las direcciones IP. 

- [Azure SQL Database](../../azure-sql/database/firewall-configure.md) 
- [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/how-to-configure-firewall.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**Pregunta:** ¿Se puede compartir la puerta de enlace entre factorías de datos diferentes?
**Respuesta:** Aún no se admite esta característica. Estamos trabajando en ello.

**Pregunta:** ¿Cuáles son los requisitos de puerto para que funcione la puerta de enlace?
**Respuesta:** La puerta de enlace hace conexiones basadas en HTTP para abrir Internet. Los **puertos de salida 80 y 443** deben estar abiertos para que la puerta de enlace establezca la conexión. Abra el **puerto de entrada 8050** solo en la máquina (no en el nivel del firewall corporativo) para la aplicación de administración de credenciales. Si se utiliza Azure SQL Database o Azure Synapse Analytics como origen y destino, tendrá que abrir también el puerto **1433**. Para obtener más información, vea la sección [Configuraciones de firewall y filtrado de direcciones IP de puerta de enlace](#firewall-configurations-and-filtering-ip-address-of gateway). 

**Pregunta:** ¿Cuáles son los certificados necesarios para la puerta de enlace?
**Respuesta:** La puerta de enlace actual requiere un certificado que usa la aplicación de administración de credenciales para establecer las credenciales del almacén de datos de forma segura. Este certificado está autofirmado y se creó y configuró durante la instalación de la puerta de enlace. En su lugar, puede usar su propio certificado TLS/SSL. Para más información, consulte la sección sobre la [aplicación de administración de credenciales con un solo clic](#click-once-credentials-manager-app). 

## <a name="next-steps"></a>Pasos siguientes
Para información sobre el rendimiento de la actividad de copia, consulte la [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md).