---
title: Novedades de Azure Automation
description: Se realizan actualizaciones significativas de la documentación de Azure Automation todos los meses.
services: automation
ms.subservice: ''
ms.topic: overview
ms.date: 11/02/2021
ms.custom: references_regions
ms.openlocfilehash: ea18171bdd957781e22743c3e59690fc8d4703f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432441"
---
# <a name="whats-new-in-azure-automation"></a>Novedades de Azure Automation

Azure Automation recibe mejoras de forma continua. Para mantenerse al día de los avances más recientes, este artículo proporciona información acerca de los elementos siguientes:

- Versiones más recientes
- Problemas conocidos
- Corrección de errores

Esta página se actualiza mensualmente, por lo que se recomienda visitarla con frecuencia. Si busca elementos que tengan más de seis meses, puede encontrarlos en el [Archivo de novedades de Azure Automation](whats-new-archive.md).

## <a name="october-2021"></a>Octubre de 2021

### <a name="preview-support-for-hybrid-runbook-worker-extension-for-azure-vms-and-arc-enabled-servers"></a>Compatibilidad en versión preliminar con la extensión Hybrid Runbook Worker para máquinas virtuales de Azure y servidores habilitados para Arc

**Tipo:** Nueva característica

Azure Automation ha liberado la integración nativa de User Hybrid Runbook Worker no solo para máquinas virtuales de Azure, sino también para máquinas que no son de Azure, a través de servidores habilitados para Arc. Para más información, lea este [anuncio](https://azure.microsoft.com/updates/automation-user-hybrid-extension-support).

### <a name="preview-support-for-azure-active-directory-authentication"></a>Compatibilidad en versión preliminar con la autenticación de Azure Active Directory

**Tipo:** Nueva característica

Azure Automation agregó una característica de seguridad crítica con la compatibilidad de la autenticación de Azure AD con todos los puntos de conexión públicos del servicio Automation. La característica se ha implementado a través de la compatibilidad con la extensión Hybrid Runbook Worker para máquinas virtuales de Azure y servidores habilitados para Arc.

Así se elimina la dependencia de los certificados y se permite cumplir los estrictos requisitos de cumplimiento y auditoría, ya que no se usan métodos de autenticación locales. Para más información, lea este [anuncio](https://azure.microsoft.com/updates/automation-user-hybrid-extension-support).

### <a name="source-control-enabled-to-use-managed-identities"></a>Control de código fuente habilitado para usar identidades administradas

**Tipo:** Nueva característica

La integración del control de código Azure Automation ahora puede usar [identidades administradas](automation-security-overview.md#managed-identities), en lugar de una cuenta de ejecución. Para más información, consulte los [requisitos previos de la integración del control de código fuente](source-control-integration.md#prerequisites).

## <a name="september"></a>Septiembre

### <a name="support-for-az-modules-by-default"></a>Compatibilidad predeterminada con los módulos de Az

**Tipo:** Nueva característica

Azure Automation ahora admite los módulos Az de forma predeterminada. Las nuevas cuentas de Automation creadas incluyen la versión más reciente de los módulos Az: 6.4.0 de forma predeterminada. Automation también incluye una opción en Azure Portal, **Actualizar módulos Az**, que le permite actualizar módulos Az en las cuentas de Automation existentes. Para más información, lea este [anuncio](https://azure.microsoft.com/updates/azure-automation-az-module-support).

## <a name="august-2021"></a>Agosto de 2021

### <a name="azure-policy-guest-configuration"></a>Configuración de invitado de Azure Policy

**Tipo:** Plan de cambio

Los clientes deben evaluar y planear la migración de State Configuration de Azure Automation a la configuración de invitado de Azure Policy. Para más información, consulte [Configuración de invitado de Azure Policy](../governance/policy/concepts/guest-configuration.md).

## <a name="july-2021"></a>Julio de 2021

### <a name="preview-support-for-user-assigned-managed-identity"></a>Compatibilidad en versión preliminar con la identidad administrada asignada por el usuario

**Tipo:** Nueva característica

Azure Automation admite ahora [identidades administradas asignadas por el usuario](automation-secure-asset-encryption.md) para trabajos en la nube en las regiones Azure global, Azure Government y Azure China. Para más información, lea este [anuncio](https://azure.microsoft.com/updates/azure-automation-user-assigned-identities/).

### <a name="general-availability-of-customer-managed-keys-for-azure-automation"></a>Disponibilidad general de claves administradas por el cliente para Azure Automation

**Tipo:** Nueva característica

Los clientes pueden administrar y proteger el cifrado de los recursos de Azure Automation mediante sus propias claves administradas. Con la introducción de las claves administradas por el cliente puede complementar el cifrado predeterminado con una capa extraordinaria de cifrado mediante una clave que se crea y administra en Azure Key Vault. Este nivel adicional de cifrado debería ayudarle a satisfacer las necesidades normativas o de cumplimiento de su organización.

Para más información, consulte [Uso de claves administradas por el cliente](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account).

## <a name="june-2021"></a>Junio de 2021

### <a name="security-update-for-log-analytics-contributor-role"></a>Actualización de seguridad para el rol Colaborador de Log Analytics

**Tipo:** Plan de cambio

Microsoft quiere quitar los derechos de la cuenta de Automation del rol Colaborador de Log Analytics. Actualmente, el rol integrado [Colaborador de Log Analytics](./automation-role-based-access-control.md#log-analytics-contributor) puede escalar los privilegios al rol [Colaborador](./../role-based-access-control/built-in-roles.md#contributor) de la suscripción. Como las cuentas de ejecución de la cuenta de Automation se configuran inicialmente con derechos de Colaborador en la suscripción, un atacante puede usarla para crear runbooks y ejecutar código como Colaborador en la suscripción.

Debido a este riesgo de seguridad, se recomienda no utilizar el rol Colaborador de Log Analytics para ejecutar trabajos de Automation. En su lugar, cree el rol personalizado Colaborador de Azure Automation y utilícelo para las acciones relacionadas con la cuenta de Automation. Para ver los pasos de implementación, consulte [Rol Colaborador de Azure Automation personalizado](./automation-role-based-access-control.md#custom-azure-automation-contributor-role).

### <a name="support-for-automation-and-state-configuration-available-in-west-us-3"></a>Compatibilidad con Automation y State Configuration disponible en Oeste de EE. UU. 3

**Tipo:** Nueva característica

Para más información, consulte [Residencia de datos en Azure](https://azure.microsoft.com/global-infrastructure/data-residency/) y seleccione su ubicación geográfica de la lista desplegable.

## <a name="may-2021"></a>Mayo de 2021

### <a name="startstop-vms-during-off-hours-v1"></a>Start/Stop VMs during off-hours (v1)

**Tipo:** Plan de cambio

Start/Stop VMs during off-hours (v1) caerá en desuso el 21 de mayo de 2022. Los clientes deben evaluar y planificar la migración a Start/Stop VMs v2 (versión preliminar). Para más información, consulte [Información general sobre Start/Stop VMs v2 (versión preliminar)](../azure-functions/start-stop-vms/overview.md).

## <a name="april-2021"></a>Abril de 2021

### <a name="support-for-update-management-and-change-tracking"></a>Compatibilidad con Update Management y Change Tracking

**Tipo:** Nueva característica

La asignación de regiones se ha actualizado para admitir Update Management y Change Tracking en Este de Noruega, Norte de Emiratos Árabes Unidos, Centro-norte de EE. UU., Sur de Brasil y Centro de Corea del Sur. Para más información, consulte [Asignaciones admitidas](./how-to/region-mappings.md#supported-mappings).

### <a name="support-for-system-assigned-managed-identities"></a>Compatibilidad con identidades administradas asignadas por el sistema

**Tipo:** Nueva característica

Ahora, Azure Automation admite [identidades administradas asignadas por el sistema](./automation-security-overview.md#managed-identities) para trabajos híbridos y en la nube en las regiones Azure global y Azure Government. Para más información, lea este [anuncio](https://azure.microsoft.com/updates/azure-automation-system-assigned-managed-identities/).

## <a name="next-steps"></a>Pasos siguientes

Para colaborar en la documentación de Azure Automation, consulte la [Guía para colaboradores de Microsoft Docs](/contribute/).
