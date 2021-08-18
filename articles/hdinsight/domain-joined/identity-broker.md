---
title: Agente de identidad de Azure HDInsight (HIB)
description: Obtenga información sobre el Agente de identidad de Azure HDInsight para simplificar la autenticación de clústeres de Apache Hadoop unidos a un dominio.
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 8201935f9aff99c9fa2709fe0b1418b0dab56e87
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737767"
---
# <a name="azure-hdinsight-id-broker-hib"></a>Agente de identidad de Azure HDInsight (HIB)

En este artículo se describe cómo configurar y usar la característica Agente de identidad de Azure HDInsight. Puede usar esta característica para obtener una autenticación OAuth moderna en Apache Ambari a la vez que aplica la autenticación multifactor sin necesidad de hashes de contraseña heredados en Azure Active Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Información general

El Agente de identidad de HDInsight simplifica las configuraciones de autenticación complejas en los escenarios siguientes:

* La organización se basa en la federación para autenticar a los usuarios para acceder a los recursos en la nube. Anteriormente, para usar clústeres de Enterprise Security Package de HDInsight, tenía que habilitar la sincronización de hash de contraseñas desde el entorno local a Azure Active Directory (Azure AD). Este requisito puede ser difícil o no deseable para algunas organizaciones.
* Su organización desea exigir la autenticación multifactor para el acceso basado en web o en HTTP a Apache Ambari y a otros recursos de clúster.

El Agente de identidad de HDInsight proporciona la infraestructura de autenticación que permite la transición de protocolos de OAuth (moderno) a Kerberos (heredado) sin necesidad de sincronizar los hashes de contraseña con Azure AD DS. Esta infraestructura consta de componentes que se ejecutan en una máquina virtual (VM) con Windows Server con el nodo del Agente de identidad de HDInsight habilitado, junto con los nodos de puerta de enlace de clúster.

Utilice la siguiente tabla para determinar la mejor opción de autenticación en función de las necesidades de su organización.

|Opciones de autenticación |Configuración de HDInsight | Factores que deben tenerse en cuenta |
|---|---|---|
| Totalmente OAuth | Enterprise Security Package + Agente de identidad de HDInsight | La opción más segura. (Se admite la autenticación multifactor). La sincronización de hashes de contraseña *no* es necesaria. No hay acceso de ssh/kinit/keytab para las cuentas locales, que no tienen un hash de contraseña en Azure AD DS. Las cuentas solo en la nube pueden seguir usando ssh/kinit/keytab. Acceso basado en web a Ambari a través de OAuth. Requiere la actualización de aplicaciones heredadas (por ejemplo, JDBC/ODBC) para admitir OAuth.|
| OAuth + autenticación básica | Enterprise Security Package + Agente de identidad de HDInsight | Acceso basado en web a Ambari a través de OAuth. Las aplicaciones heredadas siguen usando la autenticación básica. La autenticación multifactor debe estar deshabilitada para el acceso de autenticación básica. La sincronización de hashes de contraseña *no* es necesaria. No hay acceso de ssh/kinit/keytab para las cuentas locales, que no tienen un hash de contraseña en Azure AD DS. Las cuentas solo en la nube pueden seguir usando ssh/kinit. |
| Totalmente autenticación básica | Enterprise Security Package | Es más similar a las configuraciones locales. Se requiere la sincronización de hash de contraseña en Azure AD DS. Las cuentas locales pueden usar ssh/kinit o usar keytab. La autenticación multifactor debe estar deshabilitada si el almacenamiento de seguridad es Azure Data Lake Storage Gen2. |

En el diagrama siguiente se muestra el flujo moderno de autenticación basada en OAuth para todos los usuarios, incluidos los usuarios federados, una vez habilitado el Agente de identidad de HDInsight:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Diagrama que muestra el flujo de autenticación con el Agente de identidad de HDInsight." border="false":::

En este diagrama, el cliente (es decir, un explorador o una aplicación) debe adquirir primero el token de OAuth. A continuación, presenta el token a la puerta de enlace en una solicitud HTTP. Si ya ha iniciado sesión en otros servicios de Azure, como Azure Portal, puede iniciar sesión en el clúster de HDInsight con una experiencia de inicio de sesión único.

Todavía puede haber muchas aplicaciones heredadas que solo admitan la autenticación básica (es decir, nombre de usuario y contraseña). Para estos escenarios, puede seguir usando la autenticación HTTP básica para conectarse a las puertas de enlace de clúster. En esta configuración, debe asegurarse de que la conectividad de red de los nodos de la puerta de enlace con el punto de conexión de Servicios de federación de Active Directory (AD FS) asegure una línea directa de visión desde los nodos de puerta de enlace.

En el diagrama siguiente se muestra el flujo de autenticación básica para los usuarios federados. En primer lugar, la puerta de enlace intenta completar la autenticación mediante el [flujo de ROPC](../../active-directory/develop/v2-oauth-ropc.md). En caso de que no haya ningún hash de contraseña sincronizado con Azure AD, recurre a la detección del punto de conexión de AD FS y completa la autenticación mediante el acceso al punto de conexión de AD FS.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Diagrama que muestra la arquitectura con autenticación básica." border="false":::

## <a name="enable-hdinsight-id-broker"></a>Habilitación del Agente de identidad de HDInsight

Para crear un clúster de Enterprise Security Package con el Agente de identidad de HDInsight habilitado:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Siga los pasos de creación básicos para un clúster de Enterprise Security Package. Para obtener más información, consulte [Creación de un clúster de HDInsight con Enterprise Security Package](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Seleccione **Habilitar Agente de identidad de HDInsight**.

La característica Agente de identidad de HDInsight agrega una VM adicional al clúster. Esta VM es el nodo del Agente de identidad de HDInsight e incluye componentes de servidor para admitir la autenticación. El nodo del Agente de identidad de HDInsight está unido al dominio de Azure AD DS.

:::image type="content" source="./media/identity-broker/identity-broker-enable.png" alt-text="Diagrama que muestra la opción para habilitar el Agente de identidad de HDInsight." border="true":::

### <a name="use-azure-resource-manager-templates"></a>Utilización de plantillas del Administrador de recursos de Azure

Si agrega un nuevo rol denominado `idbrokernode` con los siguientes atributos al perfil de proceso de la plantilla, el clúster se creará con el nodo del Agente de identidad de HDInsight habilitado:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 2,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

Para ver un ejemplo completo de una plantilla de ARM, consulte la plantilla publicada [aquí](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).


## <a name="tool-integration"></a>Integración de herramientas

Las herramientas de HDInsight se actualizan para admitir OAuth de forma nativa. Use estas herramientas para el acceso moderno basado en OAuth a los clústeres. El [complemento IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib) de HDInsight se puede usar para aplicaciones basadas en Java, como Scala. Las [herramientas Spark y Hive para Visual Studio Code](../hdinsight-for-vscode.md) se pueden usar para trabajos de PySpark y Hive. Las herramientas admiten trabajos en lotes e interactivos.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Acceso mediante SSH sin hash de contraseña en Azure AD DS

|Opciones de SSH |Factores que deben tenerse en cuenta |
|---|---|
| Cuenta de VM local (por ejemplo, sshuser) | Proporcionó esta cuenta en el momento de creación del clúster. No hay ninguna autenticación Kerberos para esta cuenta. |
| Cuenta solo en la nube (por ejemplo, alice@contoso.onmicrosoft.com) | El hash de contraseña está disponible en Azure AD DS. La autenticación Kerberos es posible a través de SSH Kerberos. |
| Cuenta local (por ejemplo, alice@contoso.com) | La autenticación Kerberos de SSH solo es posible si un hash de contraseña está disponible en Azure AD DS. De lo contrario, este usuario no puede usar SSH en el clúster. |

Para acceder mediante SSH a una VM unida a un dominio o para ejecutar el comando `kinit`, debe proporcionar una contraseña. La autenticación Kerberos de SSH requiere que el hash esté disponible en Azure AD DS. Si desea usar SSH solo para escenarios administrativos, puede crear una cuenta solo en la nube y usarla para acceder mediante SSH al clúster. Otros usuarios locales aún pueden usar las herramientas de Ambari o HDInsight, o la autenticación básica HTTP, sin tener el hash de contraseña disponible en Azure AD DS.

Si su organización no sincroniza los hashes de contraseña con Azure AD DS, como procedimiento recomendado, cree un usuario solo en la nube en Azure AD. A continuación, asígnelo como administrador de clústeres al crear el clúster y úselo para fines de administración. Puede usarlo para obtener acceso raíz a las VM a través de SSH.

Para solucionar problemas de autenticación, consulte [esta guía](./domain-joined-authentication-issues.md).

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>Clientes que usan OAuth para conectarse a una puerta de enlace de HDInsight con el Agente de identidad de HDInsight

En la configuración del Agente de identidad de HDInsight, las aplicaciones personalizadas y los clientes que se conectan a la puerta de enlace se pueden actualizar para adquirir primero el token de OAuth que necesitan. Siga los pasos que aparecen en [este documento](../../storage/common/storage-auth-aad-app.md) para adquirir el token con la información siguiente:

*    URI del recurso de OAuth: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*    Permiso: (nombre: Cluster.ReadWrite; id.: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Después de adquirir el token de OAuth, úselo en el encabezado de autorización de la solicitud HTTP para la puerta de enlace del clúster (por ejemplo, https://\<clustername\>-int.azurehdinsight.net). Un comando cURL de ejemplo para la Livy API podría ser similar al siguiente ejemplo:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

Para utilizar Beeline y Livy, también puede seguir los códigos de ejemplo proporcionados [aquí](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples) a fin de configurar el cliente para que use OAuth y se conecte al clúster.

## <a name="faq"></a>Preguntas más frecuentes
### <a name="what-app-is-created-by-hdinsight-in-aad"></a>¿Qué aplicación crea HDInsight en AAD?
Para cada clúster, se registrará una aplicación de terceros en AAD con el URI del clúster como identifierUri (por ejemplo, `https://clustername.azurehdinsight.net`).

### <a name="why-are-users-prompted-for-consent-before-using-hib-enabled-clusters"></a>¿Por qué se solicita consentimiento a los usuarios antes de usar los clústeres habilitados para HIB?
En AAD, se requiere consentimiento para todas las aplicaciones de terceros a fin de que estas puedan autenticar a los usuarios o acceder a los datos.

### <a name="can-the-consent-be-approved-programatically"></a>¿Se puede aprobar el consentimiento mediante programación?
La API de Microsoft Graph le permite automatizar el consentimiento, consulte la [documentación de la API](/graph/api/resources/oauth2permissiongrant). La secuencia para automatizar el consentimiento es la siguiente:

* Registre una aplicación y conceda permisos Application.ReadWrite.All a la aplicación para acceder a Microsoft Graph.
* Una vez creado un clúster, consulte la aplicación de clúster basada en el URI del identificador.
* Registre el consentimiento para la aplicación.

Cuando se elimina el clúster, HDInsight elimina la aplicación y no es necesario borrar ningún consentimiento.

 


## <a name="next-steps"></a>Pasos siguientes

* [Configuración de un clúster de HDInsight con Enterprise Security Package mediante Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Sincronización de los usuarios de Azure Active Directory en un clúster de HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Supervisión del rendimiento del clúster](../hdinsight-key-scenarios-to-monitor.md)