---
title: Movimiento de un almacén de claves a una región diferente en Azure Key Vault | Microsoft Docs
description: En este artículo se ofrecen instrucciones para mover un almacén de claves a una región diferente.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2021
ms.author: mbaldwin
ms.custom: subject-moving-resources
ms.openlocfilehash: b8364ae34c8fcef3b65b83d5f2e1851920634edd
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370146"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Movimiento de un almacén de claves de Azure entre regiones

Azure Key Vault no permite mover un almacén de claves de una región a otra. Sin embargo, puede crear un almacén de claves en la nueva región, copiar manualmente cada clave, secreto o certificado individuales del almacén de claves existente en el nuevo almacén de claves y, a continuación, quitar el almacén de claves original.

## <a name="prerequisites"></a>Prerrequisitos

Es fundamental comprender las implicaciones de esta solución alternativa antes de intentar aplicarla en un entorno de producción.

## <a name="prepare"></a>Preparación

En primer lugar, debe crear un nuevo almacén de claves en la región a la que desea trasladarse. Para ello, puede usar [Azure portal](quick-create-portal.md), la [CLI de Azure](quick-create-cli.md)o [Azure PowerShell](quick-create-powershell.md).

Tenga presente los conceptos siguientes:

* Los nombres de los almacenes de claves son únicos globalmente. No se puede reutilizar un nombre de almacén.
* Tiene que volver a configurar las directivas de acceso y la configuración de red en el nuevo almacén de claves.
* Tiene que volver a configurar la protección de eliminación temporal y de purga en el nuevo almacén de claves.
* La operación de copia de seguridad y restauración no conservará la configuración de autogiro. Es posible que tenga que volver a configurar las opciones.

## <a name="move"></a>Move

Exporte las claves, los secretos o los certificados desde el almacén de claves antiguo y, a continuación, impórtelas al nuevo almacén. 

Puede realizar una copia de seguridad de cada secreto, clave y certificado individual del almacén mediante el comando de copia de seguridad. Los secretos se descargan como un blob cifrado.  Para obtener instrucciones paso a paso, consulte [copia de seguridad y restauración de Azure Key Vault](backup.md).

Como alternativa, puede descargar determinados tipos de secretos manualmente. Por ejemplo, puede descargar certificados como un archivo .pfx. Esta opción elimina las restricciones geográficas de algunos tipos de secretos, como los certificados. Puede cargar los archivos .pfx en cualquier almacén de claves de cualquier región. El secreto se descarga en un formato sin protección con contraseña. Usted es responsable de proteger los secretos durante el traslado.

Una vez que haya descargado las claves, los secretos o los certificados, restáurelos en el nuevo almacén de claves. 

El uso de los comandos backup y restore tiene dos limitaciones:

* no se puede realizar una copia de seguridad de un almacén de claves en una ubicación geográfica y restaurarlo en otra. Para obtener más información, consulte [Zonas geográficas de Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

* El comando backup realiza una copia de seguridad de todas las versiones de cada secreto. Si tiene un secreto con un gran número de versiones anteriores (más de 10), existe la posibilidad de que la solicitud supere el tamaño máximo permitido de la solicitud y que se produzca un error en la operación.

## <a name="verify"></a>Comprobar

Antes de eliminar el almacén de claves antiguo, compruebe que el nuevo almacén contenga todas las claves, secretos y certificados necesarios. 


## <a name="next-steps"></a>Pasos siguientes

- [Copia de seguridad y restauración de Azure Key Vault](backup.md)
- [Movimiento de un almacén de Azure Key Vault entre grupos de recursos](move-resourcegroup.md)
- [Traslado de Azure Key Vault a otra suscripción](move-subscription.md)