---
title: Recarga automática de secretos y certificados
titleSuffix: Azure App Configuration
description: Aprenda a configurar la aplicación para volver a cargar automáticamente los secretos y certificados de Key Vault.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 05/25/2021
ms.author: avgupta
ms.openlocfilehash: f133c3184186ef46f7d4579a64d22733b773df7f
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111415473"
---
# <a name="reload-secrets-and-certificates-from-key-vault-automatically"></a>Recarga de secretos y certificados desde Key Vault automáticamente

App Configuration y Key Vault son servicios complementarios que se usan de forma conjunta en muchas aplicaciones. App Configuration ayuda a usar juntos los servicios mediante la creación de claves en el almacén de App Config que hacen referencia a secretos o certificados almacenados en Key Vault. Puesto que Key Vault almacena el par de claves pública y privada de un certificado como secreto, la aplicación puede recuperar cualquier certificado como secreto desde Key Vault.

Como práctica de seguridad recomendada, los [secretos](../key-vault/secrets/tutorial-rotation.md) y los [certificados](../key-vault/certificates/tutorial-rotate-certificates.md) se deben rotar periódicamente. Una vez que se hayan rotado en Key Vault, es aconsejable que la aplicación seleccione los valores de secreto y certificado más recientes. Hay dos maneras de lograrlo sin reiniciar la aplicación:
- Actualizar un par de clave-valor de Sentinel para desencadenar la actualización de toda la configuración, con lo que se vuelven a cargar todos los secretos y certificados de Key Vault. Para más información, vea cómo [usar la configuración dinámica en una aplicación de ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md).
- Recargar periódicamente algunos o todos los secretos y certificados de Key Vault.

En la primera opción, tendrá que actualizar el par de clave-valor de Sentinel en App Configuration cada vez que rote los secretos y certificados en Key Vault. Este enfoque funciona bien cuando se quiere forzar una recarga inmediata de secretos y certificados en la aplicación. Sin embargo, cuando los secretos y certificados se rotan automáticamente en Key Vault, la aplicación puede experimentar errores si no actualiza el par de clave-valor de Sentinel a tiempo. La segunda opción le permite automatizar completamente este proceso. Puede configurar la aplicación para recargar secretos y certificados desde Key Vault dentro del retraso aceptable desde el momento de la rotación. Este tutorial le guía por la segunda opción.


## <a name="prerequisites"></a>Requisitos previos

- En este tutorial se muestra cómo configurar la aplicación para recargar automáticamente los secretos y certificados de Key Vault. Se basa en el tutorial para implementar referencias de Key Vault en el código. Antes de continuar, finalice [Tutorial: Uso de referencias de Key Vault en una aplicación de ASP.NET Core](./use-key-vault-references-dotnet-core.md).

- Paquete [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) v4.4.0 o posterior.


## <a name="add-an-auto-rotating-certificate-to-key-vault"></a>Adición de un certificado de rotación automática a Key Vault

 Siga el [Tutorial: Configuración de la rotación automática de certificados en Key Vault](../key-vault/certificates/tutorial-rotate-certificates.md) para agregar un certificado de rotación automática denominado **ExampleCertificate** a la instancia de Key Vault creada en el tutorial anterior.


## <a name="add-a-reference-to-the-key-vault-certificate-in-app-configuration"></a>Adición de una referencia al certificado de Key Vault en App Configuration

1. En Azure Portal, seleccione **Todos los recursos** y después seleccione la instancia de almacén de App Configuration que creó en el tutorial anterior.

1. Seleccione **Explorador de configuración**.

1. Seleccione **+ Crear** > **Referencia del almacén de claves** y, a continuación, especifique los valores siguientes:
    - **Clave**: seleccione **TestApp:Settings:KeyVaultCertificate**.
    - **Etiqueta**: deje este valor en blanco.
    - **Suscripción**, **Grupo de recursos** y **Almacén de claves**: escriba los valores correspondientes al almacén de claves que creó en el tutorial anterior.
    - **Secreto**: seleccione el secreto llamado **ExampleCertificate** que creó en la sección anterior.
    - **Versión del secreto**: **Última versión**.

> [!Note]
> Si hace referencia a una versión específica, al recargar el secreto o el certificado desde Key Vault devolverá siempre el mismo valor.


## <a name="update-code-to-reload-key-vault-secrets-and-certificates"></a>Actualización del código para recargar los secretos y certificados de Key Vault

En el archivo *Program.cs*, actualice el método `AddAzureAppConfiguration` para configurar un intervalo de actualización para el certificado de Key Vault mediante el método `SetSecretRefreshInterval`. Con este cambio, la aplicación volverá a cargar el par de claves pública y privada para **ExampleCertificate** cada 12 horas.

```csharp
config.AddAzureAppConfiguration(options =>
{
    options.Connect(settings["ConnectionStrings:AppConfig"])
            .ConfigureKeyVault(kv =>
            {
                kv.SetCredential(new DefaultAzureCredential());
                kv.SetSecretRefreshInterval("TestApp:Settings:KeyVaultCertificate", TimeSpan.FromHours(12));
            });
});
```

El primer argumento del método `SetSecretRefreshInterval` es la clave de la referencia de Key Vault en App Configuration. Este argumento es opcional. Si se omite el parámetro de clave, el intervalo de actualización se aplicará a todos los secretos y certificados que no tienen intervalos de actualización individuales.

El intervalo de actualización define la frecuencia con la que los secretos y certificados se recargarán desde Key Vault, independientemente de los cambios en sus valores de Key Vault o App Configuration. Si quiere volver a cargar secretos y certificados cuando su valor cambie en App Configuration, puede supervisarlos mediante el método `ConfigureRefresh`. Para más información, vea cómo [usar la configuración dinámica en una aplicación de ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md).

Elija el intervalo de actualización según el retraso aceptable después de que los secretos y certificados se hayan actualizado en Key Vault. También es importante tener en cuenta los [límites del servicio de Key Vault](../key-vault/general/service-limits.md) para evitar la limitación.


## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]


## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha aprendido a configurar la aplicación para recargar automáticamente los secretos y certificados de Key Vault. Para aprender a usar una identidad administrada para simplificar el acceso a App Configuration y Key Vault, vaya al siguiente tutorial.

> [!div class="nextstepaction"]
> [Integración de identidades administradas](./howto-integrate-azure-managed-service-identity.md)
