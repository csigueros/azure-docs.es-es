---
title: Eliminación temporal de HSM administrado de Azure Key Vault | Microsoft Docs
description: La eliminación temporal en HSM administrado permite recuperar las claves y las instancias de HSM eliminadas. En este artículo se proporciona información general de la característica.
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: mbaldwin
ms.author: mbaldwin
ms.date: 06/01/2021
ms.openlocfilehash: b832aa066e1d31bfc064f988c722d2503ff96507
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195050"
---
# <a name="managed-hsm-soft-delete-overview"></a>Visión general de la eliminación temporal de HSM administrado

> [!IMPORTANT]
> La eliminación temporal no se puede desactivar para recursos de HSM administrado.

> [!IMPORTANT]
> Los recursos de HSM administrado que se eliminen temporalmente se seguirán facturando según su tarifa por hora completa hasta que se purguen.

La característica de eliminación temporal de HSM administrado permite recuperar claves y HSM eliminados. En concreto, esta característica proporciona las siguientes medidas de seguridad:

- Una vez que se elimine un HSM o una clave, se podrá recuperar durante un período configurable de entre 7 y 90 días naturales. Puede establecer el período de retención al crear un HSM. Si no especifica ningún valor, se usará el período de retención predeterminado de 90 días. Este período ofrece a los usuarios tiempo suficiente para darse cuenta de la eliminación accidental de una clave o HSM y reaccionar.
- Para eliminar permanentemente una clave, los usuarios deben realizar dos acciones. En primer lugar, deben eliminar la clave, acción que hará que pase a un estado de eliminación temporal. En segundo lugar, deben purgar la clave en el estado de eliminación temporal. La operación de purga requiere el rol "Responsable de cifrado" de HSM administrado. Estas medidas de seguridad adicionales reducen el riesgo de que un usuario elimine una clave o un HSM por error o de forma malintencionada.


## <a name="soft-delete-behavior"></a>Comportamiento de eliminación temporal

La eliminación temporal no se puede desactivar para recursos de HSM administrado.

Los recursos marcados como eliminados se conservan durante un período especificado. También hay un mecanismo para recuperar claves o HSM eliminados, por lo que puede deshacer la eliminación.

El período de retención predeterminado es de 90 días. Al crear un recurso de HSM, puede establecer el intervalo de la directiva de retención en un valor de entre 7 y 90 días. La directiva de retención de protección de purgas usa el mismo intervalo. Una vez que establezca la directiva de retención, no podrá cambiarla.

No puede volver a usar el nombre de un recurso de HSM que se ha eliminado temporalmente hasta que haya transcurrido el período de retención y se haya purgado (eliminado permanentemente) el recurso.

## <a name="purge-protection"></a>Protección de purgas

La protección contra purgas es un comportamiento opcional que no está habilitado de manera predeterminada. Para activarla, use la [CLI de Azure](./recovery.md?tabs=azure-cli) o [PowerShell](./recovery.md?tabs=azure-powershell).

Cuando la protección contra purgas está activada, no se pueden purgar HSM o claves en estado eliminado hasta que finalice el período de retención. Las claves y los HSM eliminados temporalmente todavía se pueden recuperar, lo que garantiza que la directiva de retención siga vigente.

El período de retención predeterminado es de 90 días. Al crear un recurso de HSM, puede establecer el intervalo de la directiva de retención en un valor de entre 7 y 90 días. El intervalo de la directiva de retención solo se puede establecer al crear un HSM. Después no se puede cambiar.

Vea [Uso de la eliminación flexible de HSM administrado con la CLI](./recovery.md?tabs=azure-cli#managed-hsms-cli) o [Uso de la eliminación flexible de HSM administrado con PowerShell](./recovery.md?tabs=azure-powershell#managed-hsms-powershell).

## <a name="managed-hsm-recovery"></a>Recuperación del HSM administrado

Al eliminar un HSM, el servicio crea un recurso de proxy en la suscripción y agrega suficientes metadatos para habilitar la recuperación. El recurso de proxy es un objeto almacenado que está disponible en la misma ubicación que el HSM eliminado. 

## <a name="key-recovery"></a>Recuperación de claves

Cuando se elimine una clave, el servicio la pasará a un estado eliminado, por lo que no estará accesible para ninguna operación. En este estado, las claves se pueden enumerar, recuperar o purgar. Para ver los objetos, use el comando `az keyvault key list-deleted` de la CLI de Azure (como se describe en [Eliminación temporal de HSM administrado y protección contra purgas con la CLI](./recovery.md?tabs=azure-cli#keys-cli)) o el parámetro `-InRemovedState` de Azure PowerShell (como se describe en [Eliminación temporal de HSM administrado y protección contra purgas con PowerShell](./recovery.md?tabs=azure-powershell#keys-powershell)).  

Si elimina la clave, HSM administrado programará la eliminación de los datos subyacentes correspondientes a la clave o el HSM eliminados, tras un intervalo de retención predeterminado. El registro de DNS correspondiente al HSM también se conserva durante el intervalo de retención.

## <a name="soft-delete-retention-period"></a>Período de retención de la eliminación temporal

Los recursos eliminados temporalmente se conservan durante un período de tiempo determinado: 90 días. Durante el intervalo de retención de eliminación temporal, se dan las circunstancias siguientes:

- Puede visualizar las claves y los HSM eliminados temporalmente con relación a su suscripción. También puede acceder a la información de eliminación y recuperación sobre estos.
- Solo los usuarios con el rol "Colaborador" de HSM administrado pueden enumerar los HSM eliminados. Se recomienda crear un rol personalizado con estos permisos especiales para gestionar los almacenes eliminados.
- Los nombres de HSM administrados deben ser únicos en una ubicación determinada. Al crear una clave, no se puede usar un nombre si el HSM contiene una clave con dicho nombre en estado eliminado.
- Solo los usuarios con el rol "Colaborador" de HSM administrado pueden enumerar, ver, recuperar y purgar HSM administrados.
- Solo los usuarios con el rol "Responsable de cifrado" de HSM administrado pueden enumerar, ver, recuperar y purgar claves.
  
A menos que se recupere un HSM administrado o una clave, al final del intervalo de retención el servicio realizará una purga del HSM o la clave que se hayan eliminado temporalmente. No se puede reprogramar la eliminación de recursos.

### <a name="billing-implications"></a>Implicaciones de facturación

HSM administrado es un servicio de un solo inquilino. Al crear un HSM administrado, el servicio reserva los recursos subyacentes asignados al HSM. Estos recursos permanecen asignados incluso cuando el HSM se encuentra en estado eliminado. El HSM se le facturará mientras se encuentre en este estado.

## <a name="next-steps"></a>Pasos siguientes

En estos artículos se describen los escenarios principales para usar la eliminación temporal:

- [Uso de la eliminación temporal de HSM administrado con PowerShell](./recovery.md?tabs=azure-powershell) 
- [Cómo usar la eliminación temporal de HSM administrado con la CLI de Azure](./recovery.md?tabs=azure-cli)
