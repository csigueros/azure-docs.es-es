---
title: Rotación de certificados para Azure Database for MariaDB
description: Información sobre los próximos cambios del certificado raíz que afectarán a Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 8a8a7fec5b5c5f62fb44f6c0ccf6859b1141295b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646518"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>Descripción de los cambios en la CA raíz para Azure Database for MariaDB

Azure Database for MariaDB completó correctamente el cambio de certificado raíz el **15 de febrero de 2021 (15/02/2021)** como parte de los procedimientos recomendados de seguridad y mantenimiento estándar. En este artículo se proporcionan más detalles sobre los cambios, los recursos afectados y los pasos necesarios para asegurarse de que la aplicación mantenga la conectividad con el servidor de base de datos.

> [!NOTE]
> Este artículo contiene referencias al término *esclavo*, un término que Microsoft ya no usa. Cuando se elimine el término del software, se eliminará también de este artículo.
>

## <a name="why-root-certificate-update-is-required"></a>¿Por qué se requiere la actualización del certificado raíz?

Los usuarios de Azure Database for MariaDB solo pueden usar el certificado predefinido para conectarse a un servidor de Azure Database for MariaDB, que se encuentra [aquí](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). Sin embargo, [Certificate Authority (CA) Browser Forum](https://cabforum.org/)  recientemente publicó informes relativos a la incompatibilidad de varios certificados emitidos por proveedores de CA.

Según los requisitos de cumplimiento normativo del sector, los proveedores de CA comenzaron a revocar los certificados de CA de las CA no compatibles, lo que requiere que los servidores usen certificados emitidos por CA compatibles y firmados por certificados de CA de esas CA compatibles. Como Azure Database for MariaDB usó uno de estos certificados no compatibles, tuvimos que rotar el certificado a la versión compatible a fin de minimizar la posible amenaza para los servidores MySQL.

El nuevo certificado se implementó y entró en vigor el 15 de febrero de 2021 (15/02/2021). 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>¿Qué cambio se realizó el 15 de febrero de 2021 (15/02/2021)?

El 15 de febrero de 2021, el [certificado raíz BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) se reemplazó por una **versión compatible** del mismo [certificado raíz BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) para garantizar que los clientes existentes no tengan que cambiar nada y que no afecte a sus conexiones con el servidor. Durante este cambio, el [certificado raíz BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) no se **reemplazó** por [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem); este cambio se ha aplazado para que los clientes tengan más tiempo para realizarlo.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>¿Tengo que hacer algún cambio en mi cliente para mantener la conectividad?

No se requiere ningún cambio en el lado cliente. Si ha seguido la recomendación anterior, que se indica a continuación, todavía podrá seguir conectándose siempre y cuando **no se quite el certificado BaltimoreCyberTrustRoot** del certificado de entidad de certificación combinado. **Se recomienda no quitar BaltimoreCyberTrustRoot del certificado de entidad de certificación combinado hasta nuevo aviso para mantener la conectividad**.

### <a name="previous-recommendation"></a>Recomendación anterior

- Descargue la CA **BaltimoreCyberTrustRoot** & **DigiCertGlobalRootG2** de los vínculos siguientes:

  - [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  - [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

- Genere un almacén de certificados de CA combinado que incluya los certificados **BaltimoreCyberTrustRoot** y **DigiCertGlobalRootG2**.

  - Para los usuarios de Java (Conector de MariaDB/J), ejecute lo siguiente:

    ```console
    keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
    ```

    A continuación, reemplace el archivo de almacén de claves original por el nuevo generado:

    - `System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");`
    - `System.setProperty("javax.net.ssl.trustStorePassword","password");`

  - Para los usuarios de .NET (Conector de MariaDB/NET, MariaDBConnector), asegúrese de **BaltimoreCyberTrustRoot** y **DigiCertGlobalRootG2** existen en el almacén de certificados de Windows, en las entidades de certificación raíz de confianza. Si no existe alguno de los certificados, importe el certificado que falta.

    [![Certificado de .NET de Azure Database for MariaDB](media/overview/netconnecter-cert.png)](media/overview/netconnecter-cert.png#lightbox)

  - En el caso de los usuarios de .NET en Linux con SSL_CERT_DIR, debe asegurarse de que **BaltimoreCyberTrustRoot** y **DigiCertGlobalRootG2** existan en el directorio indicado por SSL_CERT_DIR. Si no existe alguno de los certificados, cree el archivo de certificado que falta.

  - Para otros usuarios (MariaDB Client/MariaDB Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift), puede combinar dos archivos de certificado de CA como en el formato siguiente:

   ```
   -----BEGIN CERTIFICATE-----
   (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
   -----END CERTIFICATE-----
   -----BEGIN CERTIFICATE-----
   (Root CA2: DigiCertGlobalRootG2.crt.pem)
   -----END CERTIFICATE-----
   ```

- Reemplace el archivo PEM de la CA raíz original por el archivo de CA raíz combinado y reinicie la aplicación o el cliente.
- En el futuro, después de que se implemente el nuevo certificado en el servidor, puede cambiar el archivo PEM de la CA a DigiCertGlobalRootG2.crt.pem.

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>¿Por qué no se reemplazó el certificado BaltimoreCyberTrustRoot por DigiCertGlobalRootG2 durante este cambio el 15 de febrero de 2021?

Evaluamos la preparación de los clientes para aplicar este cambio y nos dimos cuenta de que muchos de ellos buscaban un plazo adicional para realizarlo. Con el fin de dar más tiempo a los clientes para que estuvieran preparados, decidimos aplazar el cambio de certificado a DigiCertGlobalRootG2 durante al menos un año, lo que proporciona un plazo de tiempo suficiente a los clientes y usuarios finales. 

Nuestra recomendación para los usuarios es que sigan los pasos mencionados anteriormente para crear un certificado combinado y se conecten al servidor, pero que no quiten el certificado BaltimoreCyberTrustRoot hasta que se envíe una comunicación para que lo hagan. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>¿Qué ocurre si se quita el certificado BaltimoreCyberTrustRoot?

Comenzará a experimentar errores de conectividad al conectarse al servidor de Azure Database for MariaDB. Tendrá que volver a [configurar SSL](howto-configure-ssl.md) con el certificado [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) para mantener la conectividad.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Si no utilizo SSL/TLS, ¿aún tengo que actualizar la CA raíz?

No se requiere ninguna acción si no se usa SSL/TLS.

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Si uso SSL/TLS, ¿es necesario reiniciar el servidor de base de datos para actualizar la CA raíz?

No, no es necesario reiniciar el servidor de base de datos para empezar a usar el nuevo certificado. La actualización del certificado es un cambio en el lado cliente y las conexiones de cliente entrantes deben usar el nuevo certificado para asegurarse de que pueden conectarse al servidor de bases de datos.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. ¿Cómo puedo saber si estoy usando SSL/TLS con la comprobación del certificado raíz?

Para saber si las conexiones comprueban el certificado raíz, puede revisar la cadena de conexión.

- Si la cadena de conexión incluye `sslmode=verify-ca` o `sslmode=verify-identity`, tiene que actualizar el certificado.
- Si la cadena de conexión incluye `sslmode=disable`, `sslmode=allow`, `sslmode=prefer` o `sslmode=require`, no tiene que actualizar los certificados.
- Si la cadena de conexión no especifica sslmode, no tiene que actualizar los certificados.

Si usa un cliente que abstrae la cadena de conexión, revise la documentación del cliente para saber si comprueba los certificados.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mariadb"></a>4. ¿Qué consecuencia tendrá si se usa App Service con Azure Database for MariaDB?

En el caso de los servicios de aplicaciones de Azure que se conectan a Azure Database for MariaDB, hay dos escenarios posibles y depende de cómo se use SSL con la aplicación.

- Este nuevo certificado se ha agregado a App Service en el nivel de la plataforma. Si usa en la aplicación los certificados SSL incluidos en la plataforma de App Service, no es necesario realizar ninguna acción. Se trata del escenario más habitual. 
- Si va a incluir explícitamente la ruta de acceso al archivo de certificado SSL en el código, debería descargar el nuevo certificado y actualizar el código para usar el nuevo certificado. Un buen ejemplo de este escenario es cuando se usan contenedores personalizados de App Service como compartidos en la [documentación de App Service](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress). Se trata de un escenario poco común, pero algunos usuarios lo utilizan.

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mariadb"></a>5. ¿Qué consecuencia tendrá si se usa Azure Kubernetes Service (AKS) con Azure Database for MariaDB?

Si está intentando conectarse a Azure Database for MariaDB con Azure Kubernetes Services (AKS), es similar a obtener acceso desde un entorno de host de clientes dedicado. Consulte los pasos [aquí](../aks/ingress-own-tls.md).

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mariadb"></a>6. ¿Qué consecuencia tendrá si se usa Azure Data Factory para conectarse con Azure Database for MariaDB?

Para el conector que usa Azure Integration Runtime, el conector usa los certificados del almacén de certificados de Windows en el entorno hospedado en Azure. Estos certificados ya son compatibles con los certificados recién aplicados y, por lo tanto, no es necesaria ninguna acción.

Para el conector que usa el entorno de ejecución de integración autohospedado donde se incluye explícitamente la ruta de acceso al archivo de certificado SSL en la cadena de conexión, deberá descargar el [nuevo certificado](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) y actualizar la cadena de conexión para utilizarlo.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. ¿Es necesario planear un tiempo de inactividad de mantenimiento del servidor de base de datos para este cambio?

No. Dado que el cambio aquí solo está en el lado del cliente para conectarse al servidor de base de datos, este cambio no requiere ningún tiempo de inactividad de mantenimiento para el servidor de base de datos.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. Si creo un nuevo servidor después del 15 de febrero de 2021 (15/02/2021), ¿me afectará este cambio?

En los servidores creados después del 15 de febrero de 2021 (15/02/2021), seguirá utilizando [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) para que las aplicaciones se conecten mediante SSL.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. ¿Con qué frecuencia actualiza Microsoft sus certificados o cuál es la directiva de expiración?

Los certificados utilizados por Azure Database for MariaDB provienen de entidades de certificación (CA) de confianza. Por lo tanto, la compatibilidad de estos certificados está ligada a la compatibilidad de estos certificados por parte de la entidad de certificación. El certificado [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) está programado para que expire en 2025, por lo que Microsoft deberá realizar un cambio de certificado antes de la expiración. Además, en caso de que se produzcan errores imprevistos en estos certificados predefinidos, Microsoft tendrá que realizar la rotación de certificados lo antes posible, de forma parecida al cambio realizado el 15 de febrero de 2021, para asegurarse de que el servicio sea seguro y compatible en todo momento.

### <a name="10-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>10. Si utilizo réplicas de lectura, ¿tengo que realizar esta actualización solo en el servidor de origen o en las réplicas de lectura?

Puesto que esta actualización es un cambio en el lado cliente, si el cliente leía los datos del servidor réplica, deberá aplicar también los cambios para esos clientes.

### <a name="11-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>11. Si estoy usando la replicación de datos de entrada, ¿es necesario llevar a cabo alguna acción?

- Si la replicación de datos se realiza desde una máquina virtual (local o de Azure) en Azure Database for MySQL, debe comprobar si se usa SSL para crear la réplica. Ejecute **SHOW SLAVE STATUS** (MOSTRAR ESTADO DEL ELEMENTO SECUNDARIO) y compruebe la siguiente configuración.

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

Si usa la [replicación de datos de entrada](concepts-data-in-replication.md) para conectarse a Azure Database for MySQL, hay dos aspectos que deben tenerse en cuenta:

- Si la replicación de datos se realiza desde una máquina virtual (local o de Azure) en Azure Database for MySQL, debe comprobar si se usa SSL para crear la réplica. Ejecute **SHOW SLAVE STATUS** (MOSTRAR ESTADO DEL ELEMENTO SECUNDARIO) y compruebe la siguiente configuración. 

  ```azurecli-interactive
  Master_SSL_Allowed            : Yes
  Master_SSL_CA_File            : ~\azure_mysqlservice.pem
  Master_SSL_CA_Path            :
  Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
  Master_SSL_Cipher             :
  Master_SSL_Key                : ~\azure_mysqlclient_key.pem
  ```

  Si observa que el certificado se proporciona para CA_file, SSL_Cert y SSL_Key, deberá actualizar el archivo mediante la adición del [nuevo certificado](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) y crear un archivo de certificado combinado.

- Si la replicación de datos se realiza entre dos instancias de Azure Database for MySQL, deberá restablecer la réplica ejecutando **CALL mysql.az_replication_change_master** y proporcionar el nuevo certificado raíz dual como último parámetro [master_ssl_ca](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication).

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. ¿Tenemos que consultar en el lado servidor para comprobar si se está utilizando SSL?

Para comprobar si está usando la conexión SSL para conectarse al servidor, consulte [Comprobación de la conexión SSL](howto-configure-ssl.md#verify-the-ssl-connection).

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13. ¿Debo hacer algo si ya tengo DigiCertGlobalRootG2 en el archivo de certificado?

No. No es necesario realizar ninguna acción si el archivo de certificado ya contiene **DigiCertGlobalRootG2**.

### <a name="14-what-if-i-have-further-questions"></a>14. ¿Qué hago si tengo más preguntas?

Si tiene alguna pregunta, obtenga respuestas de expertos de la comunidad en [Microsoft Q&A](mailto:AzureDatabaseformariadb@service.microsoft.com). Si tiene un plan de soporte técnico y necesita ayuda técnica, [póngase en contacto con nosotros](mailto:AzureDatabaseformariadb@service.microsoft.com).
