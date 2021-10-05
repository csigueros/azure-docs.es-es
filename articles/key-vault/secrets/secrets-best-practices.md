---
title: 'Procedimientos recomendados para la administración de secretos: Azure Key Vault | Microsoft Docs'
description: Más información sobre los procedimientos recomendados para la administración de secretos de Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: mbaldwin
ms.openlocfilehash: ae4d2bd17dc1a233be71f48ebd106f1db2a31de6
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094469"
---
# <a name="best-practices-for-secrets-management-in-key-vault"></a>Procedimientos recomendados para la administración de secretos en Key Vault

Azure Key Vault permite almacenar de forma segura credenciales de servicio o de aplicación (por ejemplo, contraseñas y claves de acceso) como secretos. Todos los secretos de Key Vault se almacenan cifrados en clave de software. Cuando usan Key Vault, los desarrolladores de aplicaciones ya no tienen que almacenar la información de seguridad en su aplicación. No tener que almacenar información de seguridad en las aplicaciones elimina la necesidad de que esta información sea parte del código. 

Ejemplos de secretos que se deben almacenar en Key Vault:

- Secretos de aplicación cliente
- Cadenas de conexión
- Contraseñas
- Claves de acceso (Redis Cache, Centro de eventos, Cosmos DB)
- Claves SSH

Cualquier otra información confidencial (como las direcciones IP, los nombres de servicio y otras opciones de configuración) debe almacenarse en [Azure App Configuration](../../azure-app-configuration/overview.md).

Cada almacén de claves individual define el límite de seguridad de los secretos. Se recomienda usar un almacén de claves único por aplicación, por región y por entorno para proporcionar un aislamiento pormenorizado de los secretos para la aplicación. 

Para obtener más información sobre los procedimientos recomendados para Key Vault, consulte el documento siguiente:
- [Procedimientos recomendados para utilizar Key Vault](../general/best-practices.md)

## <a name="configuration-and-storing"></a>Configuración y almacenamiento 

Se recomienda almacenar como valor las partes dinámicas de las credenciales, que se generan durante la rotación, como los secretos de aplicación cliente, las contraseñas y las claves de acceso de los secretos. Los identificadores y atributos estáticos relacionados, como los nombres de usuario, los identificadores de aplicaciones y las direcciones URL de los servicios, deben almacenarse como etiquetas secretas y copiarse en la nueva versión de un secreto durante la rotación.

Para obtener más información acerca de los secretos, consulte el documento siguiente:
- [Acerca de los secretos de Azure Key Vault](about-secrets.md)

## <a name="secrets-rotation"></a>Cambio de secretos
Muchas veces, los secretos se almacenan en la memoria de la aplicación como variables de entorno o valores de configuración durante todo el ciclo de vida de la aplicación, lo que los hace vulnerables a una exposición no deseada. Dado que los secretos son susceptibles a la pérdida de datos o exposición, es importante rotarlos a menudo, como mínimo cada 60 días. 

Para obtener más información sobre el proceso de rotación de los secretos, consulte el documento siguiente:
- [Automatización de la rotación de un secreto para recursos que tienen dos conjuntos de credenciales de autenticación](tutorial-rotation-dual.md) 

## <a name="access-and-network-isolation"></a>Acceso y aislamiento de red

Puede reducir la exposición de los almacenes si especifica las direcciones IP que tienen acceso a ellos. Configure el firewall para permitir que solo la aplicación y los servicios relacionados puedan acceder a los secretos del almacén, a fin de reducir la posibilidad de que los atacantes puedan acceder también. 

Para obtener más información acerca de la seguridad de la red, consulte el documento siguiente:
- [Configuración de las redes de Azure Key Vault](../general/how-to-azure-key-vault-network-security.md)

Asimismo, las aplicaciones deben seguir el acceso con privilegios mínimos, con acceso solo a la lectura de secretos. El acceso a los secretos puede controlarse con directivas de acceso o con el control de acceso basado en roles de Azure. 

Para obtener más información sobre el control de acceso en Azure Key Vault, consulte los documentos siguientes:
- [Acceso a las claves, los certificados y los secretos de Key Vault con un control de acceso basado en rol de Azure](../general/rbac-guide.md)
- [Asignación de una directiva de acceso de Key Vault](../general/assign-access-policy.md)
 
## <a name="service-limits-and-caching"></a>Límites del servicio y almacenamiento en caché
Key Vault se creó originalmente con los límites especificados en [Límites del servicio Azure Key Vault](../general/service-limits.md). Para maximizar las tasas de rendimiento de Key Vault, estas son algunas directrices y procedimientos recomendados para maximizar el rendimiento:
- Almacenar los secretos en la memoria caché en la aplicación durante al menos ocho horas
- Implementar lógica de reintento de retroceso exponencial para controlar los escenarios en los que se superan los límites del servicio

Para obtener más información acerca de la guía sobre la limitación, consulte lo siguiente:
- [Guía de las limitaciones de Azure Key Vault](../general/overview-throttling.md)

## <a name="monitoring"></a>Supervisión
Active el registro de su instancia de Vault para supervisar el acceso a los secretos y su ciclo de vida. Puede usar [Azure Monitor](https://docs.microsoft.com//azure/azure-monitor/overview) para supervisar todas las actividades de los secretos de todos los almacenes en un solo lugar, o bien usar [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) para supervisar el ciclo de vida de los secretos con la integración sencilla de Logic Apps y Azure Functions.

Para más información, consulte:
- [Azure Key Vault como origen de Event Grid](https://docs.microsoft.com/azure/event-grid/event-schema-key-vault?tabs=event-grid-event-schema)
- [Registro de Azure Key Vault](../general/logging.md)
- [Supervisión y alertas de Azure Key Vault](../general/alert.md)

## <a name="backup-and-purge-protection"></a>Copia de seguridad y protección de purga
Active la [protección de purga](../general/soft-delete-overview.md#purge-protection) para protegerse frente a la eliminación forzada del secreto. Realice copias de seguridad del almacén periódicamente, cuando actualice, elimine o cree secretos dentro de un almacén.

### <a name="azure-powershell-backup-commands"></a>Comandos de copia de seguridad de Azure PowerShell

* [Copia de seguridad de secreto](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Comandos de copia de seguridad de la CLI de Azure

* [Copia de seguridad de secreto](/cli/azure/keyvault/secret#az_keyvault_secret_backup)

## <a name="learn-more"></a>Más información
- [Acerca de los secretos de Azure Key Vault](about-secrets.md)
- [Procedimientos recomendados para utilizar Key Vault](../general/best-practices.md)
