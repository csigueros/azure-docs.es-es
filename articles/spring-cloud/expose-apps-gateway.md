---
title: Exposición de aplicaciones a Internet mediante Application Gateway
titleSuffix: Azure Spring Cloud
description: Exposición de aplicaciones a Internet mediante Application Gateway
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: devx-track-java
ms.openlocfilehash: e23503c7e38009a8ac6b5da82dd17e26c7aeeaee
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404299"
---
# <a name="expose-applications-to-the-internet-using-application-gateway"></a>Exposición de aplicaciones a Internet mediante Application Gateway

En este artículo se explica cómo exponer las aplicaciones a Internet mediante Application Gateway. Cuando se implementa una instancia de servicio de Azure Spring Cloud en la red virtual, solo se puede acceder a las aplicaciones de la instancia de servicio en la red privada. Para que las aplicaciones sean accesibles en Internet, debe integrarlas con Azure Application Gateway.

## <a name="prerequisites"></a>Requisitos previos

- [CLI de Azure, versión 2.0.4 o posterior](/cli/azure/install-azure-cli).
- Una instancia de servicio de Azure Spring Cloud implementada en una red virtual con una aplicación accesible a través de una red privada mediante el sufijo de dominio `.private.azuremicroservices.io` predeterminado. Para más información, vea [Implementación de Azure Spring Cloud en una red virtual](./how-to-deploy-in-azure-virtual-network.md).
- Dominio personalizado que se va a usar para acceder a la aplicación.
- Un certificado, almacenado en Key Vault, que coincide con el dominio personalizado que se va a usar para establecer el agente de escucha HTTPS. Para más información, vea [Tutorial: Importación de un certificado en Azure Key Vault](../key-vault/certificates/tutorial-import-certificate.md).

## <a name="configure-application-gateway-for-azure-spring-cloud"></a>Configuración de Application Gateway para Azure Spring Cloud

Se recomienda que el nombre de dominio, tal como lo ve el explorador, sea el mismo que el nombre de host que Application Gateway usa para dirigir el tráfico al back-end de Azure Spring Cloud. Esta recomendación proporciona la mejor experiencia al usar Application Gateway para exponer las aplicaciones hospedadas en Azure Spring Cloud que residen en una red virtual. Si el dominio expuesto por Application Gateway es diferente del dominio aceptado por Azure Spring Cloud, las cookies y las direcciones URL de redireccionamiento generadas (por ejemplo) se pueden interrumpir.

Para configurar Application Gateway delante de Azure Spring Cloud, siga estos pasos.

1. Siga las instrucciones de [Implementación de Azure Spring Cloud en una red virtual](./how-to-deploy-in-azure-virtual-network.md).
1. Adquiera un certificado para el dominio que prefiera y almacénelo en Key Vault. Para más información, vea [Tutorial: Importación de un certificado en Azure Key Vault](../key-vault/certificates/tutorial-import-certificate.md).
1. Configure un dominio personalizado y el certificado correspondiente de Key Vault en una aplicación implementada en Azure Spring Cloud. Para más información, vea [Tutorial: Asignación de un dominio personalizado existente a Azure Spring Cloud](./tutorial-custom-domain.md).
1. Implemente Application Gateway en una red virtual configurada según la lista siguiente:
   - Use Azure Spring Cloud en el grupo de back-end, al que hace referencia el dominio con el sufijo `private.azuremicroservices.io`.
   - Incluya un agente de escucha HTTPS usando el mismo certificado de Key Vault.
   - Configure la red virtual con las opciones HTTP que usen el nombre de dominio personalizado configurado en Azure Spring Cloud, en lugar del dominio con el sufijo `private.azuremicroservices.io`.
1. Configure el DNS público para que apunte a Application Gateway.

## <a name="define-variables"></a>Definición de variables

A continuación, use los comandos siguientes para definir las variables para el grupo de recursos y la red virtual que creó como se indicó en [Implementación de Azure Spring Cloud en una red virtual](./how-to-deploy-in-azure-virtual-network.md). Personalice los valores en función de su entorno real. Al definir `SPRING_APP_PRIVATE_FQDN`, quite `https://` del identificador URI.

```bash
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_CLOUD_NAME='name-of-spring-cloud-instance'
APPNAME='name-of-app-in-azure-spring-cloud'
SPRING_APP_PRIVATE_FQDN='$APPNAME.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Use el siguiente comando para iniciar sesión en la CLI de Azure y elija su suscripción activa.

```azurecli
az login
az account set --subscription $SUBSCRIPTION
```

## <a name="acquire-a-certificate"></a>Adquisición de un certificado

### <a name="use-a-publicly-signed-certificate"></a>[Uso de un certificado firmado públicamente](#tab/public-cert)

En el caso de las implementaciones de producción, lo más probable es que use un certificado firmado públicamente. En este caso, importe el certificado en Azure Key Vault. Para más información, vea [Tutorial: Importación de un certificado en Azure Key Vault](../key-vault/certificates/tutorial-import-certificate.md). Asegúrese de que el certificado incluye toda la cadena de certificados.

### <a name="use-a-self-signed-certificate"></a>[Uso de un certificado autofirmado](#tab/self-signed-cert)

Cuando necesite un certificado autofirmado para pruebas o desarrollo, debe crearlo. También deberá asegurarse de que la lista de "Nombres alternativos del firmante" del certificado contiene el nombre de dominio en el que expondrá la aplicación. Al crear un certificado autofirmado mediante Azure Key Vault, puede hacerlo en Azure Portal. Como alternativa, al usar la CLI de Azure, necesitará un archivo JSON de directiva.

Para solicitar la directiva predeterminada, use el siguiente comando:

```azurecli
az keyvault certificate get-default-policy
```

A continuación, adapte el archivo JSON de directiva como se muestra en el ejemplo siguiente, indicando `subject` y `SubjectAlternativeNames`:

```json
{
  // ...
    "subject": "C=US, ST=WA, L=Redmond, O=Contoso, OU=Contoso HR, CN=myapp.mydomain.com",
    "subjectAlternativeNames": {
        "dnsNames": [
            "myapp.mydomain.com",
            "*.myapp.mydomain.com"
        ],
        "emails": [
            "hello@contoso.com"
        ],
          "upns": []
      }
  // ...
}
```

Cuando haya terminado de actualizar el archivo JSON de directiva (vea [Actualización de la directiva de certificados](/rest/api/keyvault/update-certificate-policy/update-certificate-policy)), puede crear un certificado autofirmado en Key Vault mediante los comandos siguientes:

```azurecli
KV_NAME='name-of-key-vault'
CERT_NAME_IN_KV='name-of-certificate-in-key-vault'

az keyvault certificate create \
    --vault-name $KV_NAME \
    --name $CERT_NAME_IN_KV \
    --policy "$KV_CERT_POLICY"
```

---

## <a name="configure-the-public-domain-name-on-azure-spring-cloud"></a>Configuración del nombre de dominio público en Azure Spring Cloud

El tráfico escribirá la aplicación implementada en Azure Spring Cloud con el nombre de dominio público. Para configurar la aplicación para que escuche en este nombre de host a través de HTTPS, use los siguientes comandos para agregar un dominio personalizado a la aplicación:

```azurecli
KV_NAME='name-of-key-vault'
KV_RG='resource-group-name-of-key-vault'
CERT_NAME_IN_ASC='name-of-certificate-in-Azure-Spring-Cloud'
CERT_NAME_IN_KV='name-of-certificate-with-intermediaries-in-key-vault'
DOMAIN_NAME=myapp.mydomain.com

# provide permissions to ASC to read the certificate from Key Vault:
VAULTURI=$(az keyvault show -n $KV_NAME -g $KV_RG --query properties.vaultUri -o tsv)

# get the object id for the Azure Spring Cloud Domain-Management Service Principal:
ASCDM_OID=$(az ad sp show --id 03b39d0f-4213-4864-a245-b1476ec03169 --query objectId --output tsv)

# allow this Service Principal to read and list certificates and secrets from Key Vault:
az keyvault set-policy -g $KV_RG -n $KV_NAME  --object-id $ASCDM_OID --certificate-permissions get list --secret-permissions get list

# add custom domain name and configure TLS using the certificate:
az spring-cloud certificate add \
    --resource-group $RESOURCE_GROUP \
    --service $SPRING_CLOUD_NAME \
    --name $CERT_NAME_IN_ASC \
    --vault-certificate-name $CERT_NAME_IN_KV \
    --vault-uri $VAULTURI
az spring-cloud app custom-domain bind \
    --resource-group $RESOURCE_GROUP \
    --service $SPRING_CLOUD_NAME \
    --domain-name $DOMAIN_NAME \
    --certificate $CERT_NAME_IN_ASC \
    --app $APPNAME
```

> [!NOTE]
> Con fines de desarrollo y pruebas cuando desea realizar una terminación TLS en Application Gateway, no es necesario configurar un certificado en Azure Spring Cloud ni proporcionar permisos para que Azure Spring Cloud lea desde Key Vault. Solo tiene que enlazar el nombre de dominio, y puede hacerlo con el siguiente comando:
>
> ```azurecli
> az spring-cloud app custom-domain bind \
>     --resource-group $RESOURCE_GROUP \
>     --service $SPRING_CLOUD_NAME \
>     --domain-name $DOMAIN_NAME \
>     --app $APPNAME
> ```

## <a name="create-network-resources"></a>Crear recursos de red

La instancia de Azure Application Gateway que se va a crear unirá la misma red virtual que la instancia de servicio de Azure Spring Cloud —o a la red virtual emparejada a ella—. En primer lugar, cree una nueva subred para Application Gateway en la red virtual con `az network vnet subnet create`, y cree también una dirección IP pública como front-end de Application Gateway con `az network public-ip create`.

```azurecli
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name $APPLICATION_GATEWAY_SUBNET_NAME \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VIRTUAL_NETWORK_NAME \
    --address-prefix $APPLICATION_GATEWAY_SUBNET_CIDR
az network public-ip create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --name $APPLICATION_GATEWAY_PUBLIC_IP_NAME \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-a-managed-identity-for-application-gateway"></a>Creación de una identidad administrada para Application Gateway

Application Gateway necesitará tener acceso a Key Vault para leer el certificado. Para ello, usará una [identidad administrada](../active-directory/managed-identities-azure-resources/overview.md) asignada por el usuario Cree la identidad administrada mediante el comando siguiente:

```azurecli
APPGW_IDENTITY_NAME='name-for-appgw-managed-identity'
az identity create \
    --resource-group $RESOURCE_GROUP \
    --name $APPGW_IDENTITY_NAME
```

A continuación, capture el identificador de objeto de la identidad administrada, ya que se usará más adelante para conceder derechos de acceso al certificado en Key Vault:

```azurecli
APPGW_IDENTITY_CLIENTID=$(az identity show --resource-group $RESOURCE_GROUP --name $APPGW_IDENTITY_NAME --query clientId --output tsv)
APPGW_IDENTITY_OID=$(az ad sp show --id $APPGW_IDENTITY_CLIENTID --query objectId --output tsv)
```

## <a name="set-policy-on-key-vault"></a>Establecimiento de la directiva en Key Vault

Configure Key Vault mediante el siguiente comando para que la identidad administrada de Application Gateway pueda acceder al certificado almacenado en Key Vault:

```azurecli
az keyvault set-policy \
    --name $KV_NAME \
    --resource-group $KV_RG \
    --object-id $APPGW_IDENTITY_OID \
    --secret-permissions get list \
    --certificate-permissions get list
```

## <a name="create-application-gateway"></a>Creación de la puerta de enlace de aplicaciones

Cree una puerta de enlace de aplicación mediante `az network application-gateway create` y especifique el nombre de dominio completo (FQDN) privado de la aplicación como servidores en el grupo de back-end. Asegúrese de usar la identidad administrada asignada por el usuario y de apuntar al certificado en Key Vault con el identificador de secreto del certificado. A continuación, actualice la configuración HTTP mediante `az network application-gateway http-settings update` para usar el nombre de host público.

```azurecli
APPGW_NAME='name-for-application-gateway'

KEYVAULT_SECRET_ID_FOR_CERT=$(az keyvault certificate show --name $CERT_NAME_IN_KV --vault-name $KV_NAME --query sid --output tsv)

az network application-gateway create \
    --name $APPGW_NAME \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --capacity 2 \
    --sku Standard_v2 \
    --frontend-port 443 \
    --http-settings-cookie-based-affinity Disabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address $APPLICATION_GATEWAY_PUBLIC_IP_NAME \
    --vnet-name $VIRTUAL_NETWORK_NAME \
    --subnet $APPLICATION_GATEWAY_SUBNET_NAME \
    --servers $SPRING_APP_PRIVATE_FQDN \
    --key-vault-secret-id $KEYVAULT_SECRET_ID_FOR_CERT \
    --identity $APPGW_IDENTITY_NAME
```

La puerta de enlace de aplicaciones puede tardar hasta 30 minutos en crearse.

>[!NOTE]
> Con fines de desarrollo y pruebas cuando desea realizar la terminación TLS en Application Gateway, cambie `http-settings-port` por `80` y `http-settings-protocol` por `Https` y siga las instrucciones especificadas a continuación en "Uso de un certificado firmado públicamente".

### <a name="update-http-settings-to-use-the-domain-name-towards-the-backend"></a>Actualización de la configuración HTTP para usar el nombre de dominio hacia el back-end

#### <a name="use-a-publicly-signed-certificate"></a>[Uso de un certificado firmado públicamente](#tab/public-cert-2)

Actualice la configuración HTTP para usar el nombre de dominio público como el nombre de host en lugar del dominio con el sufijo ".private.azuremicroservices.io" para enviar tráfico a Azure Spring Cloud.

```azurecli
az network application-gateway http-settings update \
    --resource-group $RESOURCE_GROUP \
    --gateway-name $APPGW_NAME \
    --host-name-from-backend-pool false \
    --host-name $DOMAIN_NAME \
    --name appGatewayBackendHttpSettings
```

#### <a name="use-a-self-signed-certificate"></a>[Uso de un certificado autofirmado](#tab/self-signed-cert-2)

Actualice la configuración HTTP para usar el nombre de dominio público como el nombre de host en lugar del dominio con el sufijo ".private.azuremicroservices.io" para enviar tráfico a Azure Spring Cloud. Dado que se utiliza un certificado autofirmado, deberá estar en la lista de permitidos en la configuración HTTP de Application Gateway.

Para incluir el certificado en la lista de permitidos, primero capture la parte pública de dicho certificado desde Key Vault mediante el comando siguiente:

```azurecli
az keyvault certificate download \
    --vault-name $KV_NAME \
    --name $CERT_NAME_IN_KV \
    --file ./selfsignedcert.crt \
    --encoding DER
```

A continuación, cárguelo en Application Gateway mediante este comando:

```azurecli
az network application-gateway root-cert create \
    --resource-group $RG \
    --cert-file ./selfsignedcert.crt \
    --gateway-name $APPGW_NAME \
    --name MySelfSignedTrustedRootCert
```

Ahora puede actualizar la configuración HTTP para confiar en el nuevo certificado raíz (autofirmado) mediante este comando:

```azurecli
az network application-gateway http-settings update \
    --resource-group $RG \
    --gateway-name $APPGW_NAME \
    --host-name-from-backend-pool false \
    --host-name $DOMAIN_NAME \
    --name appGatewayBackendHttpSettings \
    --root-certs MySelfSignedTrustedRootCert
```

---

### <a name="check-the-deployment-of-application-gateway"></a>Comprobación de la implementación de Application Gateway

Una vez creado, compruebe el estado del back-end mediante el comando siguiente. La salida de este comando le permite determinar si Application Gateway llega a la aplicación mediante su FQDN privado.

```azurecli
az network application-gateway show-backend-health \
    --name $APPGW_NAME \
    --resource-group $RESOURCE_GROUP
```

La salida indica el estado correcto del grupo de back-end, como se muestra en el ejemplo siguiente:

```output
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="configure-dns-and-access-the-application"></a>Configuración del DNS y acceso a la aplicación

Ahora configure el DNS público para que apunte a Application Gateway mediante un registro CNAME o A. Puede encontrar la dirección pública de Application Gateway mediante el comando siguiente:

```azurecli
az network public-ip show \
    --resource-group $RESOURCE_GROUP \
    --name $APPLICATION_GATEWAY_PUBLIC_IP_NAME \
    --query [ipAddress] \
    --output tsv
```

Ahora puede acceder a la aplicación mediante el nombre de dominio público.

## <a name="see-also"></a>Consulte también

- [Solución de problemas de Azure Spring Cloud en una red virtual](./troubleshooting-vnet.md)
- [Responsabilidades del cliente para ejecutar Azure Spring Cloud en una red virtual](./vnet-customer-responsibilities.md)
