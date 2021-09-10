---
title: Cómo firmar paquetes de configuración de invitado
description: Opcionalmente, puede firmar paquetes de contenido de configuración de invitado y forzar al agente a que solo permita contenido firmado.
ms.date: 07/12/2021
ms.topic: how-to
ms.openlocfilehash: 7c7d120f17e32b3ea9accb6697ac66f4afcbb879
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868575"
---
# <a name="how-to-sign-guest-configuration-packages"></a>Cómo firmar paquetes de configuración de invitado

Las directivas personalizadas de Configuración de invitado usan el hash SHA256 para validar que el paquete de directivas no haya cambiado. Opcionalmente, los clientes también pueden usar un certificado para firmar paquetes y forzar a la extensión de configuración de invitados a permitir solo el contenido firmado.

Para habilitar este escenario, hay dos pasos que debe completar. Ejecute el cmdlet para firmar el paquete de contenido y anexe una etiqueta a las máquinas que deben solicitar la firma del código.

## <a name="signature-validation-using-a-code-signing-certificate"></a>Validación de firmas mediante un certificado de firma de código

Para usar la característica de validación de firmas, ejecute el cmdlet `Protect-GuestConfigurationPackage` para firmar el paquete antes de publicarlo. Este cmdlet requiere un certificado de "firma de código".

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parámetros del cmdlet `Protect-GuestConfigurationPackage`:

- **Path**: ruta de acceso completa del paquete de configuración de invitados.
- **Certificate**: certificado de firma de código para firmar el paquete. Este parámetro solo se admite cuando se firma contenido para Windows.

## <a name="certificate-requirements"></a>Requisitos de certificados

El agente GuestConfiguration espera que la clave pública del certificado esté presente en la opción "Entidades de certificación raíz de confianza" de las máquinas Windows y en la ruta de acceso `/usr/local/share/ca-certificates/extra` de las máquinas Linux. Para que el nodo compruebe el contenido firmado, instale la clave pública del certificado en la máquina antes de aplicar la directiva personalizada. Este proceso se puede realizar con cualquier técnica dentro de la máquina virtual o mediante Azure Policy. Hay disponible una plantilla de ejemplo [para implementar un equipo con un certificado](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-push-certificate-windows).
La directiva de acceso de Key Vault debe permitir que el proveedor de recursos del proceso obtenga acceso a los certificados durante las implementaciones. Para obtener los pasos detallados, consulte [Configuración de Key Vault para máquinas virtuales en Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

El siguiente es un ejemplo para exportar la clave pública de un certificado de firma, para importarla a la máquina.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

## <a name="tag-requirements"></a>Requisitos de etiqueta

Una vez publicado el contenido, anexe una etiqueta con el nombre `GuestConfigPolicyCertificateValidation` y el valor `enabled` a todas las máquinas virtuales en las que se debe solicitar la firma de código. Consulte los [ejemplos de etiqueta](../samples/built-in-policies.md#tags) sobre cómo se pueden entregar etiquetas a escala mediante Azure Policy. Una vez que esta etiqueta esté en uso, la definición de la directiva que se genera mediante el cmdlet `New-GuestConfigurationPolicy` habilita el requisito a través de la extensión de configuración de invitados.

## <a name="next-steps"></a>Pasos siguientes

- [Pruebe el artefacto de paquete](./guest-configuration-create-test.md) desde el entorno de desarrollo.
- [Publique el artefacto del paquete](./guest-configuration-create-publish.md) para que sea accesible para los equipos.
- Use el módulo `GuestConfiguration` a fin de [crear una definición de Azure Policy](./guest-configuration-create-definition.md) para la administración a gran escala del entorno.
- [Asigne la definición de directiva personalizada](../assign-policy-portal.md) mediante Azure Portal.
- Obtenga información sobre cómo ver los [detalles de cumplimiento de las asignaciones de directivas de configuración de invitado](./determine-non-compliance.md#compliance-details-for-guest-configuration).
