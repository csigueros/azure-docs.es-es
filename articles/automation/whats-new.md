---
title: Novedades de Azure Automation
description: Se realizan actualizaciones significativas de la documentación de Azure Automation todos los meses.
services: automation
ms.subservice: ''
ms.topic: overview
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: 84644759a3f2f887662faae686814f032410266b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123107940"
---
# <a name="whats-new-in-azure-automation"></a>Novedades de Azure Automation

Azure Automation recibe mejoras de forma continua. Para mantenerse al día de los avances más recientes, este artículo proporciona información acerca de los elementos siguientes:

- Versiones más recientes
- Problemas conocidos
- Corrección de errores

Esta página se actualiza mensualmente, por lo que se recomienda visitarla con frecuencia. Si busca elementos que tengan más de seis meses, puede encontrarlos en el [Archivo de novedades de Azure Automation](whats-new-archive.md).

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

Los clientes pueden administrar y proteger el cifrado de los recursos de Azure Automation mediante sus propias claves administradas. Con la introducción de las claves administradas por el cliente puede complementar el cifrado predeterminado con una capa de cifrado adicional mediante una clave que se crea y administra en Azure Key Vault. Este cifrado adicional debería ayudarle a satisfacer las necesidades normativas o de cumplimiento de su organización.

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

Azure Automation admite ahora [identidades administradas asignadas por el sistema](./automation-security-overview.md#managed-identities-preview) para trabajos híbridos y en la nube en las regiones Azure global y Azure Government. Para más información, lea este [anuncio](https://azure.microsoft.com/updates/azure-automation-system-assigned-managed-identities/).

## <a name="march-2021"></a>Marzo de 2021

### <a name="new-azure-automation-built-in-policy-definitions"></a>Nuevas definiciones de directivas integradas de Azure Automation

**Tipo:** Nueva característica

Azure Automation ha agregado cinco nuevas directivas integradas:

- Las cuentas de Automation deben deshabilitar el acceso a la red pública.
- Las cuentas de Azure Automation deben usar claves administradas por el cliente para cifrar los datos en reposo.
- Configurar cuentas de Azure Automation para deshabilitar el acceso a la red pública
- Configurar conexiones de punto de conexión privado en cuentas de Azure Automation
- Las conexiones de punto de conexión privado en cuentas de Automation deben estar habilitadas.

Para más información, consulte la [referencia sobre Azure Policy](./policy-reference.md).

### <a name="support-for-automation-and-state-configuration-declared-ga-in-south-india"></a>Compatibilidad con la disponibilidad general declarada de Automation y State Configuration en Sur de la India

**Tipo:** Nueva característica

Use la automatización de procesos y la característica State Configuration en la región Sur de la India. Para más información, lea este [anuncio](https://azure.microsoft.com/updates/azure-automation-in-south-india-region/).

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uk-west"></a>Compatibilidad con la disponibilidad general declarada de Automation y State Configuration en Oeste de Reino Unido

**Tipo:** Nueva característica

Use la automatización de procesos y la característica State Configuration en la región Oeste de Reino Unido. Para más información, lea el [anuncio](https://azure.microsoft.com/updates/azure-automation-in-uk-west-region/).

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uae-central"></a>Compatibilidad con la disponibilidad general declarada de Automation y State Configuration en Centro de Emiratos Árabes Unidos

**Tipo:** Nueva característica

Use la automatización de procesos y la característica State Configuration en la región Centro de Emiratos Árabes Unidos. Para más información, lea este [anuncio](https://azure.microsoft.com/updates/azure-automation-in-uae-central-region/).

### <a name="support-for-automation-and-state-configuration-available-in-australia-central-2-norway-west-and-france-south"></a>Compatibilidad con Automation y State Configuration disponible en Centro de Australia 2, Oeste de Noruega y Sur de Francia

**Tipo:** Nueva característica

Para más información sobre la [página Residencia de datos](https://azure.microsoft.com/global-infrastructure/data-residency/), seleccione la ubicación geográfica de cada región.

### <a name="new-scripts-added-for-installing-hybrid-worker-on-windows-and-linux"></a>Nuevos scripts agregados para instalar Hybrid Worker en Windows y Linux

**Tipo:** Nueva característica

Se han agregado dos nuevos scripts al [repositorio de GitHub](https://github.com/azureautomation) de Azure Automation que permiten abordar uno de los escenarios clave de Azure Automation para configurar una instancia de Hybrid Runbook Worker en una máquina Windows o Linux. El script crea una máquina virtual o usa una ya existente, crea un área de trabajo de Log Analytics si es necesario, instala el agente de Log Analytics para Windows o para Linux y registra la máquina en el área de trabajo de Log Analytics. El script de Windows se denomina **Create Automation Windows HybridWorker** y el de Linux es **Create Automation Linux HybridWorker**.

### <a name="invoke-runbook-through-an-azure-resource-manager-template-webhook"></a>Invocación de runbook mediante un webhook de una plantilla de Resource Manager

**Tipo:** Nueva característica

Para más información, consulte [Uso de un webhook de una plantilla de ARM](./automation-webhooks.md#create-runbook-and-webhook-with-arm-template).

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>Azure Update Management ahora admite Centos 8.x, Red Hat Enterprise Linux Server 8.x y SUSE Linux Enterprise Server 15

**Tipo:** Nueva característica

Consulte la [lista completa](./update-management/operating-system-requirements.md) de sistemas operativos Linux compatibles para más información.

### <a name="in-region-data-residency-support-for-brazil-south-and-south-east-asia"></a>Compatibilidad con la residencia de datos en la región para las regiones Sur de Brasil y Sudeste de Asia

**Tipo:** Nueva característica

En todas las regiones excepto Sur de Brasil y Sudeste de Asia, los datos de Azure Automation se almacenan en una región diferente (región emparejada de Azure) para proporcionar continuidad empresarial y recuperación ante desastres (BCDR). En el caso de las regiones Sur de Brasil y Sudeste de Asia, los datos de Azure Automation se almacenan en la misma región para adaptarse a los requisitos de residencia de datos de estas regiones. Para más información, consulte [Replicación geográfica en Azure Automation](./automation-managing-data.md#geo-replication-in-azure-automation).

## <a name="february-2021"></a>Febrero de 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Compatibilidad con la disponibilidad general declarada de Automation y State Configuration en Japón Occidental

**Tipo:** Nueva característica

Disponibilidad de la cuenta de Automation y de State Configuration en la región Japón Occidental. Para más información, lea el [anuncio](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/).

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Se ha presentado el cumplimiento de Azure Policy personalizado para aplicar la ejecución de runbooks en Hybrid Worker

**Tipo:** nueva característica

Puede usar la nueva regla de cumplimiento de Azure Policy para permitir la creación de trabajos, webhooks y programaciones de trabajos para que se ejecuten solo en grupos de Hybrid Worker.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Disponibilidad de Update Management en las regiones Este de EE. UU., Centro de Francia y Norte de Europa

**Tipo:** Nueva característica

La característica Update Management de Automation está disponible en las regiones Este de EE. UU., Centro de Francia y Norte de Europa. Vea [Asignación de regiones admitidas](how-to/region-mappings.md) para obtener actualizaciones de la documentación en la que se refleja este cambio.

## <a name="next-steps"></a>Pasos siguientes

Para colaborar en la documentación de Azure Automation, consulte la [Guía para colaboradores de Microsoft Docs](/contribute/).
