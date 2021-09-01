---
title: 'Solución de problemas del cifrado de datos: Azure Database for MySQL'
description: Aprenda a solucionar problemas del cifrado de datos en Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: 3697130dd0d623a71158121c572aba7a52be28c7
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652013"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-mysql"></a>Solución de problemas del cifrado de datos en Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

En este artículo se describe cómo identificar y resolver problemas habituales que se producen en Azure Database for MySQL cuando se configura con cifrado de datos mediante una clave administrada por el cliente.

## <a name="introduction"></a>Introducción

Cuando configura el cifrado de datos para que use una clave administrada por el cliente en Azure Key Vault, los servidores requieren un acceso continuo a la clave. Si el servidor pierde el acceso a la clave administrada por el cliente en Azure Key Vault, denegará todas las conexiones con su correspondiente mensaje de error y cambiará su estado a ***Inaccesible*** en Azure Portal.

Si ya no se necesita un servidor de Azure Database for MySQL inaccesible, puede eliminarlo para dejar de incurrir en gastos. No se permite ninguna otra acción en el servidor hasta que se haya restaurado el acceso al almacén de claves y el servidor vuelva a estar disponible. Tampoco es posible cambiar la opción de cifrado de datos de `Yes`(administrada por el cliente) a `No` (administrada por el servicio) en un servidor inaccesible mientras este está cifrado con una clave administrada por el cliente. Tendrá que volver a validar la clave manualmente antes de que se pueda acceder al servidor de nuevo. Esta acción es necesaria para proteger los datos contra el acceso no autorizado mientras se revocan los permisos para la clave administrada por el cliente.

## <a name="common-errors-that-cause-the-server-to-become-inaccessible"></a>Errores comunes que hacen que el servidor sea inaccesible

Las siguientes configuraciones incorrectas producen la mayoría de los problemas del cifrado de datos que usa claves de Azure Key Vault:

- El almacén de claves no está disponible o no existe:
  - El almacén se claves se eliminó por error.
  - Un error de red intermitente hace que el almacén de claves no esté disponible.

- No tiene permisos para acceder al almacén de claves o la clave no existe:
  - La clave se ha eliminado accidentalmente, se ha deshabilitado o ha expirado.
  - La identidad administrada de la instancia de Azure Database for MySQL se eliminó accidentalmente.
  - La identidad administrada de la instancia de Azure Database for MySQL tiene permisos de claves insuficientes. Por ejemplo, los permisos no incluyen las operaciones Get, Wrap y Unwrap.
  - Los permisos de la identidad administrada en la instancia de Azure Database for MySQL se han revocado o eliminado.

## <a name="identify-and-resolve-common-errors"></a>Identificación y resolución de los errores comunes

### <a name="errors-on-the-key-vault"></a>Errores en el almacén de claves

#### <a name="disabled-key-vault"></a>Almacén de claves deshabilitado

- `AzureKeyVaultKeyDisabledMessage`
- **Explicación**: La operación no pudo completarse en el servidor porque la clave de Azure Key Vault está deshabilitada.

#### <a name="missing-key-vault-permissions"></a>Permisos de Key Vault que faltan

- `AzureKeyVaultMissingPermissionsMessage`
- **Explicación**: El servidor no tiene los permisos Get, Wrap y Unwrap necesarios para Azure Key Vault. Conceda los permisos que faltan a la entidad de servicio con identificador.

### <a name="mitigation"></a>Mitigación

- Confirme que la clave administrada por el cliente está presente en el almacén de claves.
- Identifique el almacén de claves y vaya a él en Azure Portal.
- Asegúrese de que el URI de clave identifica una clave que está presente.

## <a name="next-steps"></a>Pasos siguientes

[Uso de Azure Portal para configurar el cifrado de datos con una clave administrada por el cliente en Azure Database for MySQL](howto-data-encryption-portal.md)
