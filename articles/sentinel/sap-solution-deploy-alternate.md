---
title: Implementación del conector de datos Azure Sentinel SAP en el entorno local | Microsoft Docs
description: Aprenda a implementar el conector de datos de Azure Sentinel para entornos SAP mediante una máquina local.
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.topic: how-to
ms.custom: mvc
ms.date: 05/19/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: fc045d4b6c185b9e27573a1dd97c1194239f7463
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466465"
---
# <a name="deploy-the-azure-sentinel-sap-data-connector-on-premises"></a>Implementación del conector de datos Azure Sentinel SAP en el entorno local

En este artículo se describe cómo implementar el conector de datos Azure Sentinel SAP en un proceso experto o personalizado, como el uso de una máquina local y una instancia de Azure Key Vault para almacenar las credenciales.

> [!NOTE]
> El proceso predeterminado y más recomendado para implementar el conector de datos Azure Sentinel SAP es [mediante una máquina virtual de Azure](sap-deploy-solution.md). Este artículo está destinado a usuarios avanzados.

> [!IMPORTANT]
> La solución Azure Sentinel SAP está actualmente en VERSIÓN PRELIMINAR. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="prerequisites"></a>Requisitos previos

Los requisitos previos básicos para implementar el conector Azure Sentinel SAP son los mismos, independientemente del método de implementación.

Antes de empezar, asegúrese de que el sistema cumple los requisitos previos que se documentan en el [tutorial principal de implementación del conector de datos de SAP](sap-deploy-solution.md#prerequisites).

Para más información, consulte [Requisitos de SAP detallados de la solución Azure Sentinel SAP (versión preliminar pública)](sap-solution-detailed-requirements.md).

## <a name="create-your-azure-key-vault"></a>Creación del almacén de claves de Azure

Cree un almacén de claves de Azure que pueda dedicar a su conector de datos Azure Sentinel SAP.

Ejecute el siguiente comando para crear el almacén de claves de Azure:

``` azurecli
kvgp=<KVResourceGroup>

kvname=<keyvaultname>

#Create key vault
az keyvault create \
  --name $kvname \
  --resource-group $kvgp
```

Para más información, consulte [Inicio rápido: Creación de un almacén de claves mediante la CLI de Azure](/azure/key-vault/general/quick-create-cli).

## <a name="add-azure-key-vault-secrets"></a>Adición de secretos de Azure Key Vault

Para agregar secretos de Azure Key Vault, ejecute el siguiente script, con su propio identificador de sistema y las credenciales que quiera agregar:

```azurecli
#Add Abap username
az keyvault secret set \
  --name <SID>-ABAPUSER \
  --value "<abapuser>" \
  --description SECRET_ABAP_USER --vault-name $kvname

#Add Abap Username password
az keyvault secret set \
  --name <SID>-ABAPPASS \
  --value "<abapuserpass>" \
  --description SECRET_ABAP_PASSWORD --vault-name $kvname

#Add java Username
az keyvault secret set \
  --name <SID>-JAVAOSUSER \
  --value "<javauser>" \
  --description SECRET_JAVAOS_USER --vault-name $kvname

#Add java Username password
az keyvault secret set \
  --name <SID>-JAVAOSPASS \
  --value "<javauserpass>" \
  --description SECRET_JAVAOS_PASSWORD --vault-name $kvname

#Add abapos username
az keyvault secret set \
  --name <SID>-ABAPOSUSER \
  --value "<abaposuser>" \
  --description SECRET_ABAPOS_USER --vault-name $kvname

#Add abapos username password
az keyvault secret set \
  --name <SID>-ABAPOSPASS \
  --value "<abaposuserpass>" \
  --description SECRET_ABAPOS_PASSWORD --vault-name $kvname

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

## <a name="perform-an-expert--custom-installation"></a>Realización de una instalación de experto o personalizada

En este procedimiento se describe cómo implementar el conector de datos de SAP mediante una instalación de experto o personalizada, como la instalación en el entorno local. 

Se recomienda realizar este procedimiento después de tener un almacén de claves preparado con las credenciales de SAP.

**Para implementar el conector de datos de SAP:**

1. En la máquina local, descargue el SDK de SAP NW RFC más reciente del [sitio de SAP Launchpad](https://support.sap.com) > **SAP NW RFC SDK** > **SAP NW RFC SDK 7.50** > **nwrfc750X_X-xxxxxxx.zip**.

    > [!NOTE]
    > Necesitará la información de inicio de sesión de usuario de SAP para acceder al SDK y descargar el SDK que coincida con el sistema operativo.
    >
    > Asegúrese de seleccionar la opción **LINUX ON X86_64**.

1. En la máquina local, cree una carpeta con un nombre descriptivo y copie el archivo ZIP del SDK en la nueva carpeta.

1. Clone el repositorio de GitHub de la solución de Azure Sentinel en la máquina local y copie el archivo **systemconfig.ini** de la solución Azure Sentinel SAP en la nueva carpeta.

    Por ejemplo:

    ```bash
    mkdir /home/$(pwd)/sapcon/<sap-sid>/
    Cd /home/$(pwd)/sapcon/<sap-sid>/
    Wget  https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/systemconfig.inicp <**nwrfc750X_X-xxxxxxx.zip**> /home/$(pwd)/sapcon/<sap-sid>/
    ```

1. Edite el archivo **systemconfig.ini** según sea necesario, con los comentarios insertados como guía. Para más información, consulte [Configuración manual del conector de datos de SAP](#manually-configure-the-sap-data-connector).

    Para probar la configuración, puede agregar el usuario y la contraseña directamente al archivo de configuración **systemconfig.ini**. Aunque se recomienda usar [Azure Key Vault](#add-azure-key-vault-secrets) para almacenar las credenciales, también puede usar un archivo **env.list** y [secretos de Docker](#manually-configure-the-sap-data-connector), o puede agregar sus credenciales directamente al archivo **systemconfig.ini**.

1. Defina los registros que quiere ingerir en Azure Sentinel mediante las instrucciones del archivo **systemconfig.ini**. Por ejemplo, consulte [Definición de los registros de SAP que se envían a Azure Sentinel](#define-the-sap-logs-that-are-sent-to-azure-sentinel).

1. Defina las siguientes configuraciones mediante las instrucciones del archivo **systemconfig.ini**:

    - Si se deben incluir direcciones de correo electrónico de usuario en los registros de auditoría
    - Si se deben reintentar las llamadas API con error
    - Si se deben incluir registros de auditoría cexal
    - Si se debe esperar un intervalo de tiempo entre extracciones de datos, especialmente para extracciones grandes

    Para más información, consulte [Configuraciones del conector de registros de SAP](#sal-logs-connector-settings).

1. Guarde el archivo **systemconfig.ini** actualizado en el directorio **sapcon** de la máquina.

1. Si ha elegido usar un archivo **env.list** para sus credenciales, cree un archivo **env.list** temporal con las credenciales necesarias. Una vez que el contenedor de Docker se ejecute correctamente, asegúrese de eliminar este archivo.

    > [!NOTE]
    > En el siguiente script cada contenedor de Docker se conecta a un sistema ABAP específico. Modifique el script según sea necesario para su entorno.
    >

    Ejecute:

    ```bash
    ##############################################################
    ##############################################################
    # env.list template
    SAPADMUSER=<SET_SAPCONTROL_USER>
    SAPADMPASSWORD=<SET_SAPCONTROL_PASS>
    ABAPUSER=SET_ABAP_USER>
    ABAPPASS=<SET_ABAP_PASS>
    JAVAUSER=<SET_JAVA_OS_USER>
    JAVAPASS=<SET_JAVA_OS_USER>
    ##############################################################
    ```

1. Descargue y ejecute la imagen de Docker predefinida con el conector de datos de SAP instalado.  Ejecute:

    ```bash
    docker pull mcr.microsoft.com/azure-sentinel/solution/sapcon:latest-preview
    docker run --env-file=<env.list_location> -d -v /home/$(pwd)/sapcon/<sap-sid>/:/sapcon-app/sapcon/config/system --name sapcon-<sid> sapcon
    rm -f <env.list_location>
    ```

1. Compruebe que el contenedor de Docker se ejecute correctamente. Ejecute:

    ```bash
    docker logs –f sapcon-[SID]
    ```

1. Continúe con la implementación de la solución de **Azure Sentinel para la supervisión continua de amenazas en SAP**.

    Al implementar la solución, el conector de datos de SAP se muestra en Azure Sentinel y se implementan el libro y las reglas de análisis de SAP. Cuando haya terminado, agregue y personalice manualmente las listas de reproducción de SAP.

    Para más información, consulte [Implementación de contenido de seguridad de SAP](sap-deploy-solution.md#deploy-sap-security-content).

## <a name="manually-configure-the-sap-data-connector"></a>Configuración manual del conector de datos de SAP

El conector de datos de la solución Azure Sentinel SAP se configura en el archivo **systemconfig.ini**, que ha clonado en la máquina del conector de datos de SAP como parte del [procedimiento de implementación](#perform-an-expert--custom-installation).

El código siguiente muestra un archivo **systemconfig.ini** de ejemplo:

```Python
[Secrets Source]
secrets = '<DOCKER_RUNTIME/AZURE_KEY_VAULT/DOCKER_SECRETS/DOCKER_FIXED>'
keyvault = '<SET_YOUR_AZURE_KEYVAULT>'
intprefix = '<SET_YOUR_PREFIX>'

[ABAP Central Instance]
##############################################################
# Define the following values according to your server configuration.
ashost = <SET_YOUR_APPLICATION_SERVER_HOST>
mshost = <SET_YOUR_MESSAGE_SERVER_HOST> - #In case different then App
##############################################################
group = <SET_YOUR_LOGON_GROUP>
msserv = <SET_YOUR_MS_SERVICE> - #Required only if the message server service is not defined as sapms<SYSID> in /etc/services
sysnr = <SET_YOUR_SYS_NUMBER>
user = <SET_YOUR_USER>
##############################################################
# Enter your password OR your X509 SNC parameters
passwd = <SET_YOUR_PASSWORD>
snc_partnername = <SET_YOUR_SNC_PARTNER_NAME>
snc_lib = <SET_YOUR_SNC_LIBRARY_PATH>
x509cert = <SET_YOUR_X509_CERTIFICATE>
##############################################################
sysid = <SET_YOUR_SYSTEM_ID>
client = <SET_YOUR_CLIENT>

[Azure Credentials]
loganalyticswsid = <SET_YOUR_LOG_ANALYTICS_WORKSPACE_ID>
publickey = <SET_YOUR_PUBLIC_KEY>

[File Extraction ABAP]
osuser = <SET_YOUR_SAPADM_LIKE_USER>
##############################################################
# Enter your password OR your X509 SNC parameters
ospasswd = <SET_YOUR_SAPADM_PASS>
x509pkicert = <SET_YOUR_X509_PKI_CERTIFICATE>
##############################################################
appserver = <SET_YOUR_SAPCTRL_SERVER>
instance = <SET_YOUR_SAP_INSTANCE>
abapseverity = <SET_ABAP_SEVERITY>
abaptz = <SET_ABAP_TZ>

[File Extraction JAVA]
javaosuser = <SET_YOUR_JAVAADM_LIKE_USER>
##############################################################
# Enter your password OR your X509 SNC parameters
javaospasswd = <SET_YOUR_JAVAADM_PASS>
javax509pkicert = <SET_YOUR_X509_PKI_CERTIFICATE>
##############################################################
javaappserver = <SET_YOUR_JAVA_SAPCTRL_SERVER>
javainstance = <SET_YOUR_JAVA_SAP_INSTANCE>
javaseverity = <SET_JAVA_SEVERITY>
javatz = <SET_JAVA_TZ>
```

### <a name="define-the-sap-logs-that-are-sent-to-azure-sentinel"></a>Definición de los registros de SAP que se envían a Azure Sentinel

Agregue el código siguiente al archivo **systemconfig.ini** de la solución Azure Sentinel SAP para definir los registros que se envían a Azure Sentinel.

Para más información, consulte la [referencia sobre registros de la solución Azure Sentinel SAP (versión preliminar pública)](sap-solution-log-reference.md).

```Python
##############################################################
# Enter True OR False for each log to send those logs to Azure Sentinel
[Logs Activation Status]
ABAPAuditLog = True
ABAPJobLog = True
ABAPSpoolLog = True
ABAPSpoolOutputLog = True
ABAPChangeDocsLog = True
ABAPAppLog = True
ABAPWorkflowLog = True
ABAPCRLog = True
ABAPTableDataLog = False
# ABAP SAP Control Logs - Retrieved by using SAP Conntrol interface and OS Login
ABAPFilesLogs = False
SysLog = False
ICM = False
WP = False
GW = False
# Java SAP Control Logs - Retrieved by using SAP Conntrol interface and OS Login
JAVAFilesLogs = False
##############################################################
```

### <a name="sal-logs-connector-settings"></a>Configuración del conector de registros de SAP

Agregue el código siguiente al archivo **systemconfig.ini** del conector de datos Azure Sentinel SAP para definir otras configuraciones para los registros de SAP ingeridos en Azure Sentinel.

Para más información, consulte [Realización de una instalación de experto o personalizada del conector de datos de SAP](#perform-an-expert--custom-installation).


```Python
##############################################################
[Connector Configuration]
extractuseremail = True
apiretry = True
auditlogforcexal = False
auditlogforcelegacyfiles = False
timechunk = 60
##############################################################
```

Esta sección le permite configurar los parámetros siguientes:

|Nombre de parámetro  |Descripción  |
|---------|---------|
|**extractuseremail**     |  Determina si las direcciones de correo electrónico del usuario se incluyen en los registros de auditoría.       |
|**apiretry**     |   Determina si las llamadas API se reintentan como mecanismo de conmutación por error.      |
|**auditlogforcexal**     |  Determina si el sistema fuerza el uso de registros de auditoría para sistemas que no son SAP, como SAP BASIS versión 7.4.       |
|**auditlogforcelegacyfiles**     |  Determina si el sistema fuerza el uso de registros de auditoría con funcionalidades del sistema heredadas, como las de la versión 7.4 de SAP BASIS con niveles de revisión inferiores.|
|**timechunk**     |   Determina que el sistema espera un número específico de minutos como intervalo entre extracciones de datos. Use este parámetro si se espera una gran cantidad de datos. <br><br>Por ejemplo, durante la carga de datos inicial durante las primeras 24 horas, es posible que quiera que la extracción de datos se ejecute solo cada 30 minutos para dar tiempo suficiente a cada uno de estos procesos. En tales casos, establezca este valor en **30**.  |
|     |         |


## <a name="next-steps"></a>Pasos siguientes

Una vez instalado el conector de datos de SAP, puede agregar el contenido de seguridad relacionado con SAP.

Para más información, consulte [Implementación de la solución SAP](sap-deploy-solution.md#deploy-sap-security-content).

Para más información, consulte:

- [Requisitos detallados de SAP para la solución Azure Sentinel SAP](sap-solution-detailed-requirements.md)
- [Referencia sobre los registros de la solución Azure Sentinel SAP](sap-solution-log-reference.md)
- [Solución Azure Sentinel SAP: referencia al contenido de seguridad](sap-solution-security-content.md)