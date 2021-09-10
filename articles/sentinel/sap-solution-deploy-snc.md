---
title: Implementación del conector de datos de SAP de Azure Sentinel con Secure Network Communications (SNC) | Microsoft Docs
description: Información sobre cómo implementar el conector de datos de Azure Sentinel para entornos SAP a través de SNC para registros basados en interfaces de NetWeaver/ABAP
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.topic: how-to
ms.custom: mvc
ms.date: 08/01/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: cdc576951702c8d381ebd3ae0661b0d40be98a09
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122868505"
---
# <a name="deploy-the-azure-sentinel-sap-data-connector-with-snc"></a>Implementación del conector de datos de SAP de Azure Sentinel con SNC

En este artículo se describe cómo implementar el conector de datos de SAP de Azure Sentinel cuando hay una conexión segura a SAP a través de Secure Network Communications (SNC) para registros basados en interfaces de NetWeaver/ABAP.

> [!NOTE]
> El proceso predeterminado y más recomendado para implementar el conector de datos Azure Sentinel SAP es [mediante una máquina virtual de Azure](sap-deploy-solution.md). Este artículo está destinado a usuarios avanzados.

> [!IMPORTANT]
> La solución Azure Sentinel SAP está actualmente en VERSIÓN PRELIMINAR. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="prerequisites"></a>Requisitos previos

Los requisitos previos básicos para implementar el conector Azure Sentinel SAP son los mismos, independientemente del método de implementación.

Antes de empezar, asegúrese de que el sistema cumple los requisitos previos que se documentan en el [procedimiento principal de implementación del conector de datos de SAP](sap-deploy-solution.md#prerequisites).

Estos son otros requisitos previos para trabajar con SNC:

- **Tener una conexión segura a SAP con SNC**. Defina los parámetros de SNC específicos de la conexión en las constantes de repositorio del sistema ABAP AS al que se esté conectando. Para obtener más información, consulte la [página wiki de la comunidad de SAP](https://wiki.scn.sap.com/wiki/display/Security/Securing+Connections+to+AS+ABAP+with+SNC).

- **Descargar la utilidad SAPCAR** desde SAP Service Marketplace. Para obtener más información, consulte la [Guía de instalación de SAP](https://help.sap.com/viewer/d1d04c0d65964a9b91589ae7afc1bd45/5.0.4/en-US/467291d0dc104d19bba073a0380dc6b4.html).

Para más información, consulte [Requisitos de SAP detallados de la solución Azure Sentinel SAP (versión preliminar pública)](sap-solution-detailed-requirements.md).

## <a name="create-your-azure-key-vault"></a>Creación del almacén de claves de Azure

Cree un almacén de claves de Azure que pueda dedicar a su conector de datos Azure Sentinel SAP.

Ejecute el siguiente comando para crear el almacén de claves de Azure y conceder acceso a una entidad de servicio de Azure:

``` azurecli
kvgp=<KVResourceGroup>

kvname=<keyvaultname>

spname=<sp-name>

kvname=<keyvaultname>
# Optional when Azure MI not enabled - Create sp user for AZ cli connection, save details for env.list file
az ad sp create-for-rbac –name $spname

SpID=$(az ad sp list –display-name $spname –query “[].appId” --output tsv

#Create key vault
az keyvault create \
  --name $kvname \
  --resource-group $kvgp

# Add access to SP
az keyvault set-policy --name $kvname --resource-group $kvgp --object-id $spID --secret-permissions get list set
```

Para más información, consulte [Inicio rápido: Creación de un almacén de claves mediante la CLI de Azure](../key-vault/general/quick-create-cli.md).

## <a name="add-azure-key-vault-secrets"></a>Adición de secretos de Azure Key Vault

Para agregar secretos de Azure Key Vault, ejecute el siguiente script, con su propio identificador de sistema y las credenciales que quiera agregar:

```azurecli
#Add Azure Log ws ID
az keyvault secret set \
  --name <SID>-LOG_WS_ID \
  --value "<logwsod>" \
  --description SECRET_AZURE_LOG_WS_ID --vault-name $kvname

#Add Azure Log ws public key
az keyvault secret set \
  --name <SID>-LOG_WS_PUBLICKEY \
  --value "<loswspubkey>" \
  --description SECRET_AZURE_LOG_WS_PUBLIC_KEY --vault-name $kvname
```

Para más información, consulte la documentación de la CLI de [az keyvault secret](/cli/azure/keyvault/secret).

## <a name="deploy-the-sap-data-connector"></a>Implementación del conector de datos de SAP

En este procedimiento se describe cómo implementar el conector de datos de SAP en una máquina virtual cuando se establece una conexión a través de SNC.

Se recomienda realizar este procedimiento una vez que se tenga un [almacén de claves](#create-your-azure-key-vault) preparado con sus [credenciales de SAP](#add-azure-key-vault-secrets).

**Para implementar el conector de datos de SAP:**

1. En la máquina virtual del conector de datos, descargue el SDK de SAP NW RFC más reciente desde el [sitio de SAP Launchpad](https://support.sap.com) > **SAP NW RFC SDK** > **SAP NW RFC SDK 7.50** > **nwrfc750X_X-xxxxxxx.zip**.

    > [!NOTE]
    > Necesitará la información de inicio de sesión de usuario de SAP para acceder al SDK y descargar el SDK que coincida con el sistema operativo.
    >
    > Asegúrese de seleccionar la opción **LINUX ON X86_64**.

1. Cree una carpeta con un nombre descriptivo y copie en ella el archivo ZIP del SDK.

1. Clone el repositorio de GitHub de la solución de Azure Sentinel en la máquina virtual del conector de datos y copie en esa nueva carpeta el archivo **systemconfig.ini** de la solución de SAP de Azure Sentinel.

    Por ejemplo:

    ```bash
    mkdir /home/$(pwd)/sapcon/<sap-sid>/
    cd /home/$(pwd)/sapcon/<sap-sid>/
    wget  https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/systemconfig.ini
    cp <**nwrfc750X_X-xxxxxxx.zip**> /home/$(pwd)/sapcon/<sap-sid>/
    ```

1. Edite el archivo **systemconfig.ini** según sea necesario, con los comentarios insertados como guía.

    Deberá editar todas las configuraciones, excepto los secretos del almacén de claves. Para más información, consulte [Configuración manual del conector de datos de SAP](sap-solution-deploy-alternate.md#manually-configure-the-sap-data-connector).

1. Defina los registros que quiere ingerir en Azure Sentinel mediante las instrucciones del archivo **systemconfig.ini**. 

    Por ejemplo, consulte [Definición de los registros de SAP que se envían a Azure Sentinel](sap-solution-deploy-alternate.md#define-the-sap-logs-that-are-sent-to-azure-sentinel).

    > [!NOTE]
    > Los registros relativos a las comunicaciones de SNC son únicamente aquellos que se recuperan a través de las interfaces de NetWeaver/ABAP. Los registros de SAP Control y HANA están fuera del ámbito de SNC.
    >

1. Defina las siguientes configuraciones mediante las instrucciones del archivo **systemconfig.ini**:

    - Si se deben incluir direcciones de correo electrónico de usuario en los registros de auditoría
    - Si se deben reintentar las llamadas API con error
    - Si se deben incluir registros de auditoría cexal
    - Si se debe esperar un intervalo de tiempo entre extracciones de datos, especialmente para extracciones grandes

    Para más información, consulte [Configuraciones del conector de registros de SAP](sap-solution-deploy-alternate.md#sal-logs-connector-settings).

1. Guarde el archivo **systemconfig.ini** actualizado en el directorio **sapcon** de la máquina virtual.

1. Descargue y ejecute la imagen de Docker predefinida con el conector de datos de SAP instalado.  Ejecute:

    ```bash
    docker pull docker pull mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    docker create -v $(pwd):/sapcon-app/sapcon/config/system -v /home/azureuser /sap/sec:/sapcon-app/sec --env SCUDIR=/sapcon-app/sec --name sapcon-snc mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    ```


## <a name="post-deployment-sap-system-procedures"></a>Procedimientos del sistema SAP posteriores a la implementación

Después de implementar el conector de datos de SAP, realice los siguientes procedimientos del sistema SAP:

1. Descargue la biblioteca criptográfica de SAP desde [SAP Service Marketplace](https://launchpad.support.sap.com/#/) > **Software Downloads** > **Browse our Download Catalog** > **SAP Cryptographic Software** (SAP Service Marketplace > Descargas de software > Buscar en nuestro catálogo criptográfico > Software criptográfico de SAP).

    Para obtener más información, consulte la [Guía de instalación de SAP](https://help.sap.com/viewer/d1d04c0d65964a9b91589ae7afc1bd45/5.0.4/en-US/86921b29cac044d68d30e7b125846860.html).

1. Use la utilidad SAPCAR para extraer los archivos de biblioteca e impleméntelos en la máquina virtual del conector de datos de SAP, en el `<sec>` directorio.

1. Confirme que tiene permisos para ejecutar los archivos de biblioteca.

1. Defina una variable de entorno denominada **SECUDIR** con el valor de ruta de acceso completa establecido en el directorio `<sec>`.

1. Cree un entorno de seguridad personal (PSE). La herramienta de línea de comandos **sapgenspe** está disponible en el directorio `<sec>` en la máquina virtual del conector de datos de SAP.

    Por ejemplo:

    ```bash
    ./sapgenpse get_pse -p my_pse.pse -noreq -x my_pin "CN=sapcon.com, O=my_company, C=IL"
    ```

    Para obtener más información, consulte [Creación de un entorno de seguridad personal](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/285bb1fda3fa472c8d9205bae17a6f95.html) en la documentación de SAP.

1. Cree las credenciales del entorno PSE. Por ejemplo:

    ```bash
    ./sapgenpse seclogin -p my_pse.pse -x my_pin -O MXDispatcher_Service_User
    ```

    Para obtener más información, consulte [Creación de credenciales](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/d8b50371667740e797e6c9f0e9b7141f.html) en la documentación de SAP.

1. Intercambie los certificados de clave pública entre el centro de identidades y el PSE de SNC de AS ABAP.

    Por ejemplo, ejecute lo siguiente para exportar el certificado de clave pública del centro de identidades:

    ```bash
    ./sapgenpse export_own_cert -o my_cert.crt -p my_pse.pse -x abcpin
    ```

    Importe el certificado al PSE de SNC de AS ABAP, expórtelo desde el PSE y, luego, vuelva a importarlo al centro de identidades.

    Por ejemplo, ejecute lo siguiente para importar el certificado al centro de identidades:

    ```bash
    ./sapgenpse maintain_pk -a full_path/my_secure_dir/my_exported_cert.crt -p my_pse.pse -x my_pin
    ```

    Para obtener más información, consulte [Intercambio de certificados de clave pública](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/7bbf90b29c694e6080e968559170fbcd.html) en la documentación de SAP.


## <a name="edit-the-sap-data-connector-configuration"></a>Edición de la configuración del conector de datos de SAP

1. En la máquina virtual del conector de datos de SAP, vaya al archivo **systemconfig.ini** y defina los siguientes parámetros con los valores pertinentes:

    ```ini
    [Secrets Source]
    secrets = AZURE_KEY_VAULT
    ```

1. Genere los siguientes secretos en su [almacén de claves de Azure](#create-your-azure-key-vault):

    - `<Interprefix>-ABAPSNCPARTNERNAME`, donde el valor es `<Relevant DN details>`
    - `<Interprefix>-ABAPSNCLIB`, donde el valor es `<lib_Path>`
    - `<Interprefix>-ABAPX509CERT`, donde el valor es `<Certificate_Code>)`

    Por ejemplo:

    ```ini
    S4H-ABAPSNCPARTNERNAME  =  'p:CN=help.sap.com, O=SAP_SE, C=IL' (Relevant DN)
    S4H-ABAPSNCLIB = 'home/user/sec-dir' (Relevant directory)
    S4H-ABAPX509CERT = 'MIIDJjCCAtCgAwIBAgIBNzA ... NgalgcTJf3iUjZ1e5Iv5PLKO' (Relevant certificate code)
    ```

    > [!NOTE]
    > El valor predeterminado de `<Interprefix>` es su SID, como `A4H-<ABAPSNCPARTNERNAME>`.
    >

Si especifica secretos directamente en el archivo de configuración, defina los parámetros de la siguiente manera:

```ini
[Secrets Source]
secrets = DOCKER_FIXED
[ABAP Central Instance]
snc_partnername =  <Relevant_DN_Deatils>
snc_lib =  <lib_Path>
x509cert = <Certificate_Code>
For example:
snc_partnername =  p:CN=help.sap.com, O=SAP_SE, C=IL (Relevant DN)
snc_lib = /sapcon-app/sec/libsapcrypto.so (Relevant directory)
x509cert = MIIDJjCCAtCgAwIBAgIBNzA ... NgalgcTJf3iUjZ1e5Iv5PLKO (Relevant certificate code)
```

### <a name="attach-the-snc-parameters-to-your-user"></a>Asociación de parámetros de SNC a su usuario

1. En la máquina virtual del conector de datos de SAP, llame a la transacción `SM30` y seleccione conservar la tabla `USRACLEXT`.

1. Agregue una entrada nueva. En el campo **User** (Usuario), escriba el usuario de comunicaciones que se usa para conectarse al sistema ABAP.

1. Escriba el nombre de SNC cuando se le solicite. El nombre de SNC es el nombre distintivo y único que se proporcionó al crear el PSE de Identity Manager. Por ejemplo: `CN=IDM, OU=SAP, C=DE`

    Asegúrese de que el nombre de SNC vaya precedido de una letra `p`. Por ejemplo: `p:CN=IDM, OU=SAP, C=DE`.

1. Seleccione **Guardar**.

SNC ya está habilitado en la máquina virtual del conector de datos.

## <a name="activate-the-sap-data-connector"></a>Activación del conector de datos de SAP

En este procedimiento se describe cómo activar el conector de datos de SAP mediante la conexión de SNC segura que creó con los procedimientos descritos anteriormente en este artículo.

1. Active la imagen de Docker:

    ```bash
    docker start sapcon-<SID>
    ```

1. Compruebe la conexión. Ejecute:

    ```bash
    docker logs sapcon-<SID>
    ```

1. Si se produce un error en la conexión, recurra a los registros para saber dónde está el problema.

    Si es necesario, deshabilite la imagen de Docker:

    ```bash
    docker stop sapcon-<SID>
    ```

Por ejemplo, pueden surgir problemas debido a una configuración incorrecta en el archivo **systemconfig.ini** o en el almacén de claves de Azure, o bien es posible que algunos de los pasos para crear una conexión segura a través de SNC no se hayan ejecutado como es debido.

Pruebe a realizar de nuevo los pasos anteriores para configurar una conexión segura a través de SNC. Para obtener más información, consulte [Solución de problemas de implementación de la solución SAP de Azure Sentinel](sap-deploy-troubleshoot.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez activado el conector de datos de SAP, pase a implementar la solución **Azure Sentinel - Continuous Threat Monitoring para SAP**. Para más información, consulte [Implementación de contenido de seguridad de SAP](sap-deploy-solution.md#deploy-sap-security-content).

Al implementar la solución, el conector de datos de SAP se muestra en Azure Sentinel y se implementan el libro y las reglas de análisis de SAP. Cuando haya terminado, agregue y personalice manualmente las listas de reproducción de SAP.

Para más información, consulte:

- [Requisitos detallados de SAP para la solución Azure Sentinel SAP](sap-solution-detailed-requirements.md)
- [Referencia sobre los registros de la solución Azure Sentinel SAP](sap-solution-log-reference.md)
- [Solución Azure Sentinel SAP: referencia al contenido de seguridad](sap-solution-security-content.md)

