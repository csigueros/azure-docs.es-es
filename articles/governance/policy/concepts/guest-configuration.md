---
title: Descripción de la característica de configuración de invitado de Azure Policy
description: Obtenga información sobre la forma en que Azure Policy usa la característica de configuración de invitado para auditar o configurar valores dentro de las máquinas virtuales.
ms.date: 07/15/2021
ms.topic: conceptual
ms.openlocfilehash: d9da1454fa531bcc6526cc11dda3b341be0688df
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092630"
---
# <a name="understand-the-guest-configuration-feature-of-azure-policy"></a>Descripción de la característica de configuración de invitado de Azure Policy

Azure Policy puede auditar o configurar valores dentro de una máquina, tanto para las que se ejecutan en Azure como para las [habilitadas para Arc](../../../azure-arc/servers/overview.md).
El agente de configuración de invitado realiza cada tarea en Windows y Linux.
La extensión de configuración de invitado, por medio del agente, administra valores como los siguientes:

- La configuración del sistema operativo
- Configuración de la aplicación o presencia
- Configuración del entorno

[Hay disponible un tutorial de vídeo de este documento](https://youtu.be/t9L8COY-BkM).

## <a name="enable-guest-configuration"></a>Habilitación de la configuración de invitado

Para administrar el estado de las máquinas del entorno, incluidas las de Azure y los servidores habilitados para Arc, revise los detalles siguientes.

## <a name="resource-provider"></a>Proveedor de recursos

Para poder usar la característica de configuración de invitado de Azure Policy, debe registrar el proveedor de recursos `Microsoft.GuestConfiguration`. Si la asignación de una directiva de configuración de invitado se realiza en el portal, o si la suscripción está inscrita en Azure Security Center, el proveedor de recursos se registra automáticamente. Puede registrarse manualmente mediante el [portal](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) o la [CLI de Azure](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Requisitos de implementación de Azure Virtual Machines

Para administrar la configuración en una máquina, se habilita una [extensión de máquina virtual](../../../virtual-machines/extensions/overview.md) y la máquina debe tener una identidad administrada por el sistema. La extensión descarga la asignación de configuración de invitado aplicable y las dependencias correspondientes. La identidad se usa para autenticar la máquina a medida que lee y escribe en el servicio de configuración de invitado. La extensión no es necesaria para los servidores habilitados para Arc porque está incluida en el agente Connected Machine de Arc.

> [!IMPORTANT]
> La extensión de configuración de invitado y una identidad administrada son necesarias para administrar las máquinas virtuales de Azure.

Para implementar la extensión a gran escala en muchas máquinas, asigne la iniciativa de directiva `Deploy prerequisites to enable guest configuration policies on virtual machines`
a un grupo de administración, una suscripción o un grupo de recursos que contenga las máquinas que planea administrar.

Si prefiere implementar la extensión y la identidad administrada en una sola máquina, siga las instrucciones para cada uno:

- [Información general sobre la extensión Guest Configuration de Azure Policy](../../../virtual-machines/extensions/guest-configuration.md)
- [Configurar identidades administradas para recursos de Azure en una VM mediante Azure Portal](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

Para usar paquetes de configuración de invitado que aplican configuraciones, se necesita la versión **1.29.24** o posterior de la extensión de configuración de invitado de máquina virtual de Azure.

### <a name="limits-set-on-the-extension"></a>Límites establecidos en la extensión

Para limitar que la extensión afecte a las aplicaciones que se ejecutan en la máquina, no se permite que el agente de configuración de invitado supere el 5 % de la CPU. Esta limitación existe tanto para definiciones integradas como personalizadas. Lo mismo se aplica al servicio de configuración de invitado en el agente Connected Machine de Arc.

### <a name="validation-tools"></a>Herramientas de validación

Dentro de la máquina, el cliente de configuración de invitado usa herramientas locales para realizar tareas.

En la tabla siguiente se muestra una lista de las herramientas locales usadas en cada sistema operativo compatible. En el caso de contenido integrado, la configuración de invitado controla la carga de estas herramientas automáticamente.

|Sistema operativo|Herramienta de validación|Notas|
|-|-|-|
|Windows|[Desired State Configuration de PowerShell](/powershell/scripting/dsc/overview/overview) v3| Instalado de prueba en una carpeta que solo usa Azure Policy. No entrará en conflicto con DSC de Windows PowerShell. PowerShell Core no se agrega a la ruta de acceso del sistema.|
|Linux|[Desired State Configuration de PowerShell](/powershell/scripting/dsc/overview/overview) v3| Instalado de prueba en una carpeta que solo usa Azure Policy. PowerShell Core no se agrega a la ruta de acceso del sistema.|
|Linux|[Chef InSpec](https://www.chef.io/inspec/) | Instala Chef InSpec versión 2.2.61 en la ubicación predeterminada y lo agrega a la ruta de acceso del sistema. También se instalan las dependencias del paquete InSpec, que incluyen Ruby y Python. |

### <a name="validation-frequency"></a>Frecuencia de validación

El cliente de configuración de invitado busca asignaciones de invitados nuevas o modificadas cada cinco minutos. Una vez que se recibe una asignación de invitado, se comprueban los valores de esa configuración en un intervalo de 15 minutos. Si se asignan varias configuraciones, cada una se evalúa secuencialmente. Las configuraciones de larga duración afectan al intervalo de todas las configuraciones, ya que la siguiente no se ejecutará hasta que finalice la configuración anterior.

Los resultados se envían al servicio de configuración de invitado cuando finaliza la auditoría.
Cuando se produce un [desencadenador de evaluación](../how-to/get-compliance-data.md#evaluation-triggers) de directiva, el estado de la máquina se escribe en el proveedor de recursos de configuración de invitado. Esta actualización hace que Azure Policy evalúe las propiedades de Azure Resource Manager. Una evaluación de Azure Policy a petición recupera el valor más reciente del proveedor de recursos de la configuración de invitado. Pero no desencadena una nueva actividad dentro de la máquina. Después, el estado se escribe en Azure Resource Graph.

## <a name="supported-client-types"></a>Tipos de cliente admitidos

Las definiciones de directivas de configuración de invitado son inclusivas de las nuevas versiones. Las versiones anteriores de los sistemas operativos disponibles en Azure Marketplace se excluyen si el cliente de configuración de invitado no es compatible. En la tabla siguiente se muestra una lista de sistemas operativos compatibles en imágenes de Azure.
El texto ".x" es simbólico y se usa para representar nuevas versiones secundarias de distribuciones de Linux.

|Publicador|Nombre|Versiones|
|-|-|-|
|Amazon|Linux|2|
|Canonical|Ubuntu Server|16.04 - 20.x|
|Credativ|Debian|9 - 10.x|
|Microsoft|Windows Server|2012 - 2019|
|Microsoft|Cliente Windows|Windows 10|
|Oracle|Oracle-Linux|7.x-8.x|
|OpenLogic|CentOS|7.3 -8.x|
|Red Hat|Red Hat Enterprise Linux\*|7.4 - 8.x|
|SUSE|SLES|12 SP3-SP5, 15.x|

\* Red Hat CoreOS no se admite.

Las definiciones de directivas de configuración de invitado admiten imágenes de máquina virtual personalizadas, siempre y cuando se trate de uno de los sistemas operativos de la tabla anterior.

## <a name="network-requirements"></a>Requisitos de red

Las máquinas virtuales de Azure pueden usar su adaptador de red local o un vínculo privado para comunicarse con el servicio de configuración de invitado.

Las máquinas de Azure Arc se conectan mediante la infraestructura de red local para llegar a los servicios de Azure e informar del estado de cumplimiento.

### <a name="communicate-over-virtual-networks-in-azure"></a>Comunicación a través de redes virtuales en Azure

Para comunicarse con el proveedor de recursos de la configuración de invitado en Azure, las máquinas necesitan acceso de salida a los centros de datos de Azure en el puerto **443**. Si una red en Azure no permite el tráfico saliente, las excepciones deben configurarse con las reglas del [grupo de seguridad de red](../../../virtual-network/manage-network-security-group.md#create-a-security-rule). Las [etiquetas de servicio](../../../virtual-network/service-tags-overview.md) "AzureArcInfrastructure" y "Storage" se pueden usar para hacer referencia a los servicios de configuración de invitado y almacenamiento en lugar de mantener manualmente la [lista de intervalos IP](https://www.microsoft.com/download/details.aspx?id=56519) para los centros de datos de Azure. Las dos etiquetas son obligatorias porque los paquetes de contenido de configuración de invitado se hospedan en Azure Storage.

### <a name="communicate-over-private-link-in-azure"></a>Comunicación a través de un vínculo privado en Azure

Las máquinas virtuales pueden usar un [vínculo privado](../../../private-link/private-link-overview.md) para la comunicación con el servicio de configuración de invitado. Aplique la etiqueta con el nombre `EnablePrivateNetworkGC` y el valor `TRUE` para habilitar esta característica. La etiqueta se puede aplicar antes o después de que se apliquen las definiciones de directivas de configuración de invitado a la máquina.

El tráfico se enruta mediante la [dirección IP pública virtual](../../../virtual-network/what-is-ip-address-168-63-129-16.md) de Azure para establecer un canal seguro y autenticado con recursos de la plataforma Azure.

### <a name="azure-arc-enabled-servers"></a>Servidores habilitados para Azure Arc

Los nodos que se encuentran fuera de Azure y que están conectados mediante Azure Arc necesitan conectividad con el servicio de configuración de invitado. Detalles sobre los requisitos de red y proxy que se proporcionan en la [documentación de Azure Arc](../../../azure-arc/servers/overview.md).

En el caso de los servidores habilitados para Arc en centros de datos privados, permita el tráfico con los siguientes patrones:

- Puerto: solo se requiere el puerto TCP 443 para el acceso a Internet
- Dirección URL global: `*.guestconfiguration.azure.com`

## <a name="assigning-policies-to-machines-outside-of-azure"></a>Asignación de directivas a máquinas fuera de Azure

Las definiciones de directivas de auditoría disponibles para la configuración de invitado incluyen el tipo de recurso **Microsoft.HybridCompute/machines**. Todas las máquinas incorporadas a [Azure Arc para servidores](../../../azure-arc/servers/overview.md) que se encuentran en el ámbito de asignación de directivas se incluyen automáticamente.

## <a name="managed-identity-requirements"></a>Requisitos de identidad administrada

Las definiciones de directiva de la iniciativa _Implementar los requisitos previos para habilitar directivas de configuración de invitado en máquinas virtuales_ habilitan una identidad administrada asignada por el sistema, si no existe ninguna. Hay dos definiciones de directivas en la iniciativa que administran la creación de identidades. Las condiciones IF en las definiciones de directiva garantizan el comportamiento correcto en función del estado actual del recurso de máquina en Azure.

Si la máquina no tiene identidades administradas actualmente, la directiva efectiva es: [Agregar identidad administrada asignada por el sistema para habilitar asignaciones de configuración de invitado en máquinas virtuales sin identidades](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e).

Si la máquina tiene actualmente una identidad de sistema asignada por el usuario, la directiva efectiva es: [Agregar identidad administrada asignada por el sistema para habilitar las asignaciones de configuración de invitado en máquinas virtuales con una identidad asignada por el usuario](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)

## <a name="availability"></a>Disponibilidad

Los clientes que diseñan una solución de alta disponibilidad deben tener en cuenta los requisitos de planeamiento de redundancia para las [máquinas virtuales](../../../virtual-machines/availability.md), ya que las asignaciones de invitados son extensiones de recursos de máquina en Azure. Cuando los recursos de asignación de invitados se aprovisionan en una región de Azure [emparejada](../../../best-practices-availability-paired-regions.md), siempre y cuando haya al menos una región en el par disponible, los informes de asignación de invitados estarán disponibles. Si la región de Azure no está emparejada y deja de estar disponible, no será posible acceder a los informes de una asignación de invitado hasta que se restaure la región.

Al considerar una arquitectura para aplicaciones de alta disponibilidad, especialmente cuando las máquinas virtuales se aprovisionan en [conjuntos de disponibilidad](../../../virtual-machines/availability.md#availability-sets) detrás de una solución de equilibrador de carga para proporcionar alta disponibilidad, se recomienda asignar las mismas definiciones de directiva con los mismos parámetros a todas las máquinas de la solución. Si es posible, una asignación de directiva única que abarca todas las máquinas ofrecería la menor sobrecarga administrativa.

En el caso de las máquinas protegidas por [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md), asegúrese de que las máquinas de un sitio secundario están dentro del ámbito de las asignaciones de Azure Policy para las mismas definiciones con los mismos valores de parámetro que las máquinas del sitio primario.

## <a name="data-residency"></a>Residencia de datos

Configuración de invitado almacena o procesa los datos del cliente. De manera predeterminada, los datos del cliente se replican en [la región emparejada](../../../best-practices-availability-paired-regions.md).
En el caso de una sola región residente, todos los datos de los clientes se almacenan y procesan en la región.

## <a name="troubleshooting-guest-configuration"></a>Solución de problemas de la configuración de invitado

Para obtener más información sobre la solución de problemas de la configuración de invitado, vea [Solución de problemas de Azure Policy](../troubleshoot/general.md).

### <a name="multiple-assignments"></a>Asignaciones múltiples

Actualmente, las definiciones de directiva de configuración de invitado solo admiten la asignación de la misma asignación de invitado una vez por cada máquina cuando la asignación de directiva usa otros parámetros.

### <a name="assignments-to-azure-management-groups"></a>Asignaciones a grupos de administración de Azure

Las definiciones de Azure Policy de la categoría "Configuración de invitado" se pueden asignar a Grupos de administración solo cuando el efecto es "AuditIfNotExists". Las definiciones de directiva con el efecto "DeployIfNotExists" no se admiten como asignaciones a Grupos de administración.

### <a name="client-log-files"></a>Archivos de registro de cliente

La extensión de la configuración de invitado escribe archivos de registro en las ubicaciones siguientes:

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux

- VM de Azure: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`
- Servidor habilitado para Arc: `/var/lib/GuestConfig/arc_policy_logs/gc_agent.log`

### <a name="collecting-logs-remotely"></a>Recopilar registros de forma remota

El primer paso para solucionar problemas de configuraciones o módulos de configuración de invitado debe ser usar los cmdlets siguiendo los pasos descritos en [Procedimiento para probar artefactos de paquete de configuración de invitado](../how-to/guest-configuration-create-test.md).
Si no se realiza correctamente, la recopilación de registros de cliente puede ayudar a diagnosticar problemas.

#### <a name="windows"></a>Windows

Capture información de archivos de registro con el [comando de ejecución de VM de Azure](../../../virtual-machines/windows/run-command.md). El siguiente script de PowerShell de ejemplo puede ser útil.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Capture información de archivos de registro con el [comando de ejecución de VM de Azure](../../../virtual-machines/linux/run-command.md). El siguiente script de Bash de ejemplo puede ser útil.

```bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

### <a name="agent-files"></a>Archivos de agente

El agente de configuración de invitado descarga paquetes de contenido en una máquina y extrae el contenido. Para comprobar qué contenido se ha descargado y almacenado, consulte las ubicaciones de carpeta que se indican a continuación.

Windows: `c:\programdata\guestconfig\configuration`

Linux: `/var/lib/GuestConfig/Configuration`

## <a name="guest-configuration-samples"></a>Ejemplos de configuración de invitado

Hay ejemplos de directivas integradas de configuración de invitado disponibles en las ubicaciones siguientes:

- [Definiciones de directivas integradas: configuración de invitado](../samples/built-in-policies.md#guest-configuration)
- [Iniciativas integradas: configuración de invitado](../samples/built-in-initiatives.md#guest-configuration)
- [Repositorio de GitHub de ejemplos de Azure Policy](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Pasos siguientes

- Configure un [entorno de desarrollo](../how-to/guest-configuration-create-setup.md) de paquetes de configuración de invitado personalizado.
- [Cree un artefacto de paquete](../how-to/guest-configuration-create.md) para la configuración de invitado.
- [Pruebe el artefacto de paquete](../how-to/guest-configuration-create-test.md) desde el entorno de desarrollo.
- Use el módulo `GuestConfiguration` a fin de [crear una definición de Azure Policy](../how-to/guest-configuration-create-definition.md) para la administración a gran escala del entorno.
- [Asigne la definición de directiva personalizada](../assign-policy-portal.md) mediante Azure Portal.
- Obtenga información sobre cómo ver los [detalles de cumplimiento para las asignaciones de directivas de configuración de invitado](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration).
