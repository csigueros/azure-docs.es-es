---
title: Exportación de los certificados del Emulador de Azure Cosmos DB
description: Aprenda a exportar el certificado del emulador de Azure Cosmos DB para usarlo con aplicaciones de Java, Python y Node.js. Los certificados se deben exportar y usar para los lenguajes y entornos de runtime que no usan el almacén de certificados de Windows.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
ms.author: esarroyo
author: StefArroyo
ms.custom: devx-track-python, devx-track-java, contperf-fy21q1
ms.openlocfilehash: 669361fcc5ba5b79f0ec014b60fe4aa630b7b017
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113361430"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>Exportación de los certificados del emulador de Azure Cosmos DB para su uso con aplicaciones en Java, Python y Node.js
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

El Emulador de Azure Cosmos DB proporciona un entorno local que emula el servicio de Azure Cosmos DB con fines de desarrollo. El emulador de Azure Cosmos DB solo admite la comunicación segura a través de conexiones TLS.

Los certificados del emulador local de Azure Cosmos DB se generan la primera vez que ejecuta el emulador. Hay dos certificados. Uno de ellos se usa para conectarse al emulador local, y el otro se usa para administrar el cifrado predeterminado de los datos del emulador en el emulador. El certificado que desea exportar es el certificado de conexión con el nombre descriptivo "DocumentDBEmulatorCertificate".

Al usar el emulador para desarrollar aplicaciones en distintos lenguajes, como Java, Python o Node.js, debe exportar el certificado del emulador e importarlo en el almacén de certificados necesario.

El runtime y el lenguaje .NET utilizan el almacén de certificados de Windows para conectarse de forma segura al emulador local de Azure Cosmos DB cuando la aplicación se ejecuta en un host de sistema operativo Windows. Otros lenguajes tienen sus propios métodos de administración y uso de certificados. Por ejemplo, Java usa su propio [almacén de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), Python usa [contenedores de socket](https://docs.python.org/2/library/ssl.html), y Node.js usa [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

En este artículo se muestra cómo exportar los certificados TLS/SSL para su uso en diferentes lenguajes y entornos de runtime que no se integran en el almacén de certificados de Windows. Puede leer más sobre el emulador en el artículo [Uso del emulador de Azure Cosmos DB para desarrollo y pruebas](./local-emulator.md).

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>Exportación del certificado TLS/SSL de Azure Cosmos DB

Debe exportar el certificado del emulador para usar correctamente el punto de conexión del emulador de los lenguajes y entornos de runtime que no se integran en el almacén de certificados de Windows. Puede exportar el certificado mediante el administrador de certificados de Windows. Use las siguientes instrucciones paso a paso para exportar el certificado "DocumentDBEmulatorCertificate" como archivo X.509 (.cer) codificado en BASE 64:

1. Inicie el administrador de certificados de Windows; para ello, ejecute certlm.msc, vaya a la carpeta Personal-> Certificados y abra el certificado con el nombre descriptivo **DocumentDbEmulatorCertificate**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Paso 1 de exportación de un emulador local de Azure Cosmos DB":::

1. Haga clic en **Detalles** y, luego, en **Aceptar**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Paso 2 de exportación de un emulador local de Azure Cosmos DB":::

1. Haga clic en **Copiar en archivo...** .

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Paso 3 de exportación de un emulador local de Azure Cosmos DB":::

1. Haga clic en **Next**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Paso 4 de exportación de un emulador local de Azure Cosmos DB":::

1. Haga clic en **No exportar la clave privada** y, luego, haga clic en **Siguiente**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Paso 5 de exportación de un emulador local de Azure Cosmos DB":::

1. Haga clic en **X.509 codificado base 64 (. CER)** y luego en **Siguiente**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Paso 6 de exportación de un emulador local de Azure Cosmos DB":::

1. Asigne un nombre al certificado. En este caso, **documentdbemulatorcert** y, luego, haga clic en **Siguiente**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Paso 7 de exportación de un emulador local de Azure Cosmos DB":::

1. Haga clic en **Finalizar**

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Paso 8 de exportación de un emulador local de Azure Cosmos DB":::

## <a name="use-the-certificate-with-java-apps"></a>Uso del certificado con aplicaciones en Java

Al ejecutar aplicaciones de Java o MongoDB que utilizan un cliente basado en Java, es más fácil instalar el certificado en el almacén de certificados predeterminado de Java que pasar las marcas `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"`. Por ejemplo, la aplicación de demostración de Java (`https://localhost:8081/_explorer/index.html`) incluida depende del almacén de certificados predeterminado.

Siga las instrucciones de la publicación [Incorporación de un certificado al almacén de certificados de Java](https://docs.oracle.com/cd/E54932_01/doc.705/e54936/cssg_create_ssl_cert.htm) para importar el certificado X.509 en el almacén de certificados de Java. Tenga en cuenta que trabajará en el directorio *%JAVA_HOME%* al ejecutar keytool. Cuando el certificado se haya importado en el almacén de certificados, los clientes para la API SQL y la API de Azure Cosmos DB para MongoDB podrán conectarse al emulador de Azure Cosmos DB.

Como alternativa, puede ejecutar el siguiente script de Bash para importar el certificado:

```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

Una vez que el certificado TLS/SSL "CosmosDBEmulatorCertificate" esté instalado, la aplicación debería poder conectarse y utilizar el emulador local de Azure Cosmos DB. Si tiene algún problema, puede seguir el artículo [Depuración de conexiones SSL/TLS](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). En la mayoría de los casos, puede que el certificado no esté instalado en el almacén *%JAVA_HOME%/jre/lib/security/cacerts*. Por ejemplo, si tiene instaladas varias versiones de Java, es probable que la aplicación esté utilizando un almacén de certificados CA diferente del actualizado.

## <a name="use-the-certificate-with-python-apps"></a>Uso del certificado con aplicaciones en Python

Al conectarse al emulador desde aplicaciones en Python, se deshabilita la verificación de TLS. De forma predeterminada, el [SDK de Python (versión 2.0.0 o posterior)](sql-api-sdk-python.md) para la API de SQL no probará a usar el certificado TLS/SSL al conectarse con el emulador local. Sin embargo, si quiere usar la validación de TLS, puede seguir los ejemplos de la documentación de los [contenedores de sockets de Python](https://docs.python.org/2/library/ssl.html).

## <a name="how-to-use-the-certificate-in-nodejs"></a>Uso del certificado en Node.js

Al conectarse al emulador desde los SDK de Node.js, se deshabilita la verificación de TLS. De forma predeterminada, el [SDK de Node.js (versión 1.10.1 o posterior)](sql-api-sdk-node.md) para la API de SQL no probará a usar el certificado TLS/SSL al conectarse con el emulador local. Sin embargo, si desea usar la validación de TLS, puede seguir los ejemplos de la [documentación de Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="rotate-emulator-certificates"></a>Rotación de certificados del emulador

Para forzar la regeneración de los certificados del emulador, puede seleccionar **Reset Data** (Restablecer datos) en el emulador de Azure Cosmos DB que se ejecuta en la bandeja de Windows. Tenga en cuenta que esta acción también borrará todos los datos almacenados localmente por el emulador.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Restablecimiento de datos en el emulador local de Azure Cosmos DB":::

Si ha instalado el certificado en el almacén de certificados de Java o lo ha usado en otro lugar, debe volver a importarlo mediante los certificados actuales. La aplicación no se puede conectar al emulador local en tanto no se actualicen los certificados.

## <a name="next-steps"></a>Pasos siguientes

* [Uso de parámetros de línea de comandos y comandos de PowerShell para controlar el emulador](emulator-command-line-parameters.md)
* [Depuración de problemas con el emulador](troubleshoot-local-emulator.md)
