---
title: Uso de certificados TLS/SSL en la aplicación en Azure Spring Cloud
titleSuffix: Azure Spring Cloud
description: Use de certificados TLS/SSL en una aplicación.
author: karlerickson
ms.author: jieshe
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/08/2021
ms.custom: devx-track-java
ms.openlocfilehash: 9a8671e6a52210bc7f49b6654383e12fd3090b63
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258114"
---
# <a name="use-tlsssl-certificates-in-your-application-in-azure-spring-cloud"></a>Uso de certificados TLS/SSL en la aplicación en Azure Spring Cloud

En este artículo se muestra cómo usar certificados públicos en Azure Spring Cloud para la aplicación. La aplicación puede actuar como un cliente y acceder a un servicio externo que requiere autenticación de certificado, o puede que tenga que realizar tareas criptográficas.  

Cuando permite que Azure Spring Cloud administre los certificados TLS/SSL, puede mantener por separado los certificados y el código de la aplicación para proteger así la información confidencial. El código de la aplicación puede acceder a los certificados públicos que agregue a la instancia de Azure Spring Cloud.

> [!NOTE]
> En este artículo estarán disponibles próximamente ejemplos y compatibilidad de la CLI de Azure y Terraform.

## <a name="prerequisites"></a>Requisitos previos

- Una aplicación implementada en Azure Spring Cloud. Consulte [Inicio rápido: implementación de la primera aplicación de Azure Spring Cloud](./quickstart.md) o use una aplicación existente.
- Un archivo de certificado con la extensión *.crt*, *.cer*, *.pem* o *.der* o una instancia implementada de Azure Key Vault con un certificado privado.

## <a name="import-a-certificate"></a>Importación de un certificado

Puede elegir importar el certificado en la instancia de Azure Spring Cloud desde Key Vault o usar un archivo de certificado local.

### <a name="import-a-certificate-from-key-vault"></a>Importación de un certificado de Key Vault

Para conceder acceso a su almacén de claves a Azure Spring Cloud antes de importar el certificado, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione **Almacenes de claves** y, a continuación, seleccione la instancia de Key Vault desde la que importará el certificado.
1. En el panel de navegación izquierdo, seleccione **Directivas de acceso** y, a continuación, seleccione **+ Agregar directivas de acceso**.
1. Seleccione **Permisos de certificado** y, a continuación, seleccione **Obtener** y **Enumerar**.
1. En **Seleccionar entidad de seguridad**, seleccione **Ninguna seleccionada** y, a continuación, seleccione la entidad de seguridad.
1. Seleccione **Seleccionar** y, a continuación, seleccione **Agregar**.

:::image type="content" source="media/use-tls-certificates/grant-key-vault-permission.png" alt-text="Captura de pantalla de dónde conceder acceso al almacén de claves antes de importar un certificado" lightbox="media/use-tls-certificates/grant-key-vault-permission.png":::

Una vez que conceda acceso al almacén de claves, puede importar el certificado mediante estos pasos:

1. Vaya a la instancia del servicio.
1. En el panel de navegación izquierdo de la instancia, seleccione **Configuración de TLS/SSL**.
1. Seleccione **Importar certificado de Key Vault** en la sección **Certificados de clave pública**.
1. Seleccione la instancia de Key Vault en **Almacén de claves** y el certificado en **Certificado** y, a continuación, **Seleccionar** y **Aplicar**.
1. Cuando haya importado correctamente el certificado, lo verá en la lista de Certificados de clave pública.

### <a name="import-a-local-certificate-file"></a>Importación de un archivo de certificado local

Puede importar un archivo de certificado almacenado localmente mediante estos pasos:

1. Vaya a la instancia del servicio.
1. En el panel de navegación izquierdo de la instancia, seleccione **Configuración de TLS/SSL**.
1. Seleccione **Cargar certificado público** en la sección **Certificados de clave pública**.
1. Cuando haya importado correctamente el certificado, lo verá en la lista de Certificados de clave pública.

## <a name="load-a-certificate"></a>Carga de un certificado

Para cargar un certificado en la aplicación en Azure Spring Cloud, comience con estos pasos:

1. Vaya a la instancia de la aplicación.
1. En el panel de navegación izquierdo de la aplicación, seleccione **Administración del certificado**.
1. Seleccione **Agregar certificado** para elegir certificados accesibles para la aplicación.

:::image type="content" source="media/use-tls-certificates/load-certificate.png" alt-text="Captura de pantalla de dónde cargar un certificado en la aplicación" lightbox="media/use-tls-certificates/load-certificate.png":::

### <a name="load-a-certificate-from-code"></a>Carga de un certificado desde el código

Los certificados cargados están disponibles en la carpeta */etc/azure-spring-cloud/certs/public*. Use el código Java siguiente para cargar un certificado público en una aplicación en Azure Spring Cloud.

```java
CertificateFactory factory = CertificateFactory.getInstance("X509");
FileInputStream is = new FileInputStream("/etc/azure-spring-cloud/certs/public/<certificate name>");
X509Certificate cert = (X509Certificate) factory.generateCertificate(is);

// use the loaded certificate
```

### <a name="load-a-certificate-into-the-trust-store"></a>Carga de un certificado en el almacén de confianza

Para una aplicación Java, puede elegir **Cargar en el almacén de confianza** para el certificado seleccionado. El certificado se agregará automáticamente a los TrustStores predeterminados de Java para autenticar un servidor en la autenticación SSL.

El siguiente registro de la aplicación muestra que el certificado se ha cargado correctamente.

```
Load certificate from specific path. alias = <certificate alias>, thumbprint = <certificate thumbprint>, file = <certificate name>
```

## <a name="next-steps"></a>Pasos siguientes

* [Habilitación de la seguridad de la capa de transporte de un extremo a otro](./how-to-enable-end-to-end-tls.md)
* [Acceso a Config Server y Service Registry](./how-to-access-data-plane-azure-ad-rbac.md)
