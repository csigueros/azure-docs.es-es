---
title: Configuración de la recuperación ante desastres de Active Directory y DNS con Azure Site Recovery
description: En este artículo se describe cómo implementar una solución de recuperación ante desastres para Active Directory y DNS con Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mayg
ms.openlocfilehash: f7df260f0ef02df5d706bc78ed4938cb1868eead
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065091"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Configuración de la recuperación ante desastres para Active Directory y DNS

Las aplicaciones empresariales, como SharePoint, Dynamics AX y SAP, dependen de la infraestructura de DNS y AD para funcionar correctamente. Cuando se configura la recuperación ante desastres para aplicaciones, a fin de asegurar la correcta funcionalidad de la aplicación, a menudo necesitará recuperar Active Directory y el sistema de nombres de dominio (DNS) antes que otros componentes de la aplicación.

Puede usar [Site Recovery](site-recovery-overview.md) para crear un plan de recuperación ante desastres para Active Directory. Cuando se produce una interrupción, puede iniciar una conmutación por error. Puede hacer que Active Directory vuelva a funcionar en unos minutos. Si ha implementado Active Directory para varias aplicaciones en el sitio primario, por ejemplo, para SharePoint y SAP, puede querer conmutar por error el sitio completo. En primer lugar, puede conmutar por error Active Directory mediante Site Recovery. A continuación, conmutar por error las otras aplicaciones mediante planes de recuperación específicos para cada aplicación.

En este artículo se explica cómo crear una solución de recuperación ante desastres para Active Directory. Incluye los requisitos previos e instrucciones de conmutación por error. Debe estar familiarizado con Active Directory y Site Recovery antes de empezar.

## <a name="prerequisites"></a>Prerrequisitos

- Si va a replicar en Azure, [prepare los recursos de Azure](tutorial-prepare-azure.md), entre los que se incluyen una suscripción, una red virtual de Azure, una cuenta de almacenamiento y un almacén de Recovery Services.
- Revise los [requisitos de compatibilidad](./vmware-physical-azure-support-matrix.md) de todos los componentes.

## <a name="replicate-the-domain-controller"></a>Replicación del controlador de dominio

- Debe configurar la replicación de Site Recovery como mínimo en una máquina virtual (VM) que hospede el controlador de dominio o DNS.
- Si tiene varios controladores de dominio en su entorno, también debe configurar un controlador de dominio adicional en el sitio de destino. El controlador de dominio adicional puede estar en Azure o en un centro de datos local secundario.
- Si tiene solo algunas aplicaciones y un controlador de dominio, puede querer conmutar por error el sitio completo. En este caso, se recomienda usar Site Recovery para replicar el controlador de dominio en el sitio de destino, ya sea en Azure o en un centro de datos local secundario. Se puede usar la misma máquina virtual del controlador de dominio o DNS replicada para la [conmutación por error de prueba](#test-failover-considerations).
- Si tiene muchas aplicaciones y hay más de un controlador de dominio en el entorno, o si planea conmutar por error pocas aplicaciones a la vez, además de replicar la máquina virtual del controlador de dominio con Site Recovery, es recomendable que configure un controlador de dominio adicional en el sitio de destino (en Azure o un centro de datos secundario local). Para la [conmutación por error de prueba](#test-failover-considerations), puede usar un controlador de dominio que haya replicado con Site Recovery. Para la conmutación por error, puede usar el controlador de dominio adicional en el sitio de destino.

## <a name="enable-protection-with-site-recovery"></a>Habilitación de la protección con Site Recovery

Puede usar Site Recovery para proteger la máquina virtual que hospeda el controlador de dominio o DNS.

### <a name="protect-the-vm"></a>Protección de la máquina virtual

El controlador de dominio que se haya replicado con Site Recovery se utiliza para la [conmutación por error de prueba](#test-failover-considerations). Asegúrese de que cumple los requisitos siguientes:

1. El controlador de dominio es un servidor de catálogo global.
1. El controlador de dominio debe ser el propietario del rol Operaciones de maestro único flexible (FSMO) para los roles que se necesitan durante una conmutación por error de prueba. De lo contrario, estos roles deberán [asumirse](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) después de la conmutación por error.

### <a name="configure-vm-network-settings"></a>Configuración de las opciones de red de la máquina virtual

Para la máquina virtual que hospeda al controlador de dominio o DNS, en Site Recovery, configure los valores de red en la configuración **Proceso y red** de la máquina virtual replicada. Esto asegura que la máquina virtual se conecte a la red adecuada después de la conmutación por error.

## <a name="protect-active-directory"></a>Protección de Active Directory

### <a name="site-to-site-protection"></a>Protección de sitio a sitio

Cree un controlador de dominio en el sitio secundario. Al promover el servidor a un rol de controlador de dominio, especifique el mismo nombre del dominio del sitio principal. Puede usar el complemento **Servicios y sitios de Active Directory** para configurar los valores en el objeto de vínculo del sitio al que se agregan los sitios. Al configurar los valores en un vínculo de sitio, puede controlar cuándo se produce la replicación entre dos o más sitios y con qué frecuencia se produce. Para obtener más información, consulte [Programación de la replicación entre sitios](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731862(v=ws.11)).

### <a name="site-to-azure-protection"></a>Protección del sitio en Azure

En primer lugar, cree un controlador de dominio en una red virtual de Azure. Al promocionar el servidor a un rol de controlador de dominio, especifique el mismo nombre de dominio del sitio principal.

A continuación, vuelva a configurar el servidor DNS para la red virtual para usar el servidor DNS en Azure.

:::image type="content" source="./media/site-recovery-active-directory/azure-network.png" alt-text="Azure Network":::

### <a name="azure-to-azure-protection"></a>Protección de Azure en Azure

En primer lugar, cree un controlador de dominio en una red virtual de Azure. Al promocionar el servidor a un rol de controlador de dominio, especifique el mismo nombre de dominio del sitio principal.

A continuación, vuelva a configurar el servidor DNS para la red virtual para usar el servidor DNS en Azure.

## <a name="test-failover-considerations"></a>Consideraciones sobre la conmutación por error de prueba

Para evitar afectar a las cargas de trabajo de producción, la conmutación por error de prueba se produce en una red que está aislada de la red de producción.

La mayoría de las aplicaciones requieren la presencia de un controlador de dominio o un servidor DNS. Por lo tanto, antes de la conmutación por error de la aplicación, tiene que crear un controlador de dominio en la red aislada que se usará para la conmutación por error de prueba. La manera más fácil de hacerlo usar Site Recovery para replicar una máquina virtual que hospede un controlador de dominio o DNS. A continuación, ejecute una conmutación por error de prueba de la máquina virtual del controlador de dominio antes de ejecutar una conmutación por error de prueba del plan de recuperación para la aplicación.

1. Use Site Recovery para [replicar](vmware-azure-tutorial.md) la máquina virtual que hospeda el controlador de dominio o DNS.
1. Cree una red aislada. Cualquier red virtual que se cree en Azure está aislada de otras redes de forma predeterminada. Se recomienda usar el mismo intervalo de direcciones IP para esta red sea el mismo que el usado en la red de producción. No habilite la conectividad de sitio a sitio en esta red.
1. Proporcione una dirección IP de DNS en la red aislada. Use la dirección IP que se espera que obtenga la máquina virtual de DNS. Si replica en Azure, indique la dirección IP para la máquina virtual que se utiliza en la conmutación por error. Para escribir la dirección IP, en la máquina virtual replicada, en la opción **Proceso y red**, seleccione el valor **IP de destino**.

   :::image type="content" source="./media/site-recovery-active-directory/azure-test-network.png" alt-text="Red de prueba de Azure":::

   > [!TIP]
   > Site Recovery intenta crear máquinas virtuales de prueba en una subred del mismo nombre y con la misma dirección IP que se proporcionó en la opción **Proceso y red** de la máquina virtual. Si no hay disponible una subred con el mismo nombre en la red virtual de Azure proporcionada para la conmutación por error de prueba, la máquina virtual de prueba se crea en la primera subred por orden alfabético.
   >
   > Si la dirección IP de destino forma parte de la subred seleccionada, Site Recovery trata de crear la máquina de virtual de la conmutación por error de prueba con la dirección IP de destino. Si la dirección IP de destino no forma parte de la subred seleccionada, la máquina virtual de la conmutación por error de prueba se creará con la siguiente dirección IP disponible en la subred seleccionada.

### <a name="test-failover-to-a-secondary-site"></a>Conmutación por error de prueba en un sitio secundario

1. Si realiza la replicación en otro sitio local y usa DHCP, realice la [configuración de DNS y DHCP para la conmutación por error de prueba](hyper-v-vmm-test-failover.md#prepare-dhcp).
1. Realice la conmutación por error de prueba de la máquina virtual del controlador de dominio que se ejecuta en la red aislada. Use el último punto de recuperación _coherente con la aplicación_ disponible de la máquina virtual del controlador de dominio para realizar la conmutación por error de prueba.
1. Ejecute una conmutación por error de prueba para el plan de recuperación que contiene las máquinas virtuales donde se ejecuta la aplicación.
1. Una vez finalizada la prueba, realice una _limpieza de conmutación por error de prueba_ en la máquina virtual del controlador de dominio. En este paso se elimina el controlador de dominio que se creó para la conmutación por error de prueba.

### <a name="remove-references-to-other-domain-controllers"></a>Eliminación de las referencias a otros controladores de dominio

Al iniciar una conmutación por error de prueba, no incluya todos los controladores de dominio en la red de prueba. Para quitar las referencias a los demás controladores de dominio que existan en su entorno de producción, puede que tenga que [asumir los roles FSMO de Active Directory](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) y realizar una [limpieza de metadatos](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc816907(v=ws.10)) para los controladores de dominio que falten.

### <a name="issues-caused-by-virtualization-safeguards"></a>Problemas causados por las medidas de seguridad de la virtualización

> [!IMPORTANT]
> Algunas de las configuraciones que se describen en esta sección no son las configuraciones de controlador de dominio estándares o predeterminadas. Si no desea realizar estos cambios en un controlador de dominio de producción, puede crear un controlador de dominio específico para que Site Recovery utilice en la conmutación por error de prueba. Realice estos cambios únicamente en ese controlador de dominio.

A partir de Windows Server 2012, [se han integrado medidas de seguridad adicionales en Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Estas medidas de seguridad ayudan a proteger los controladores de dominio virtualizados frente a las reversiones de número de secuencias actualizadas (USN), si la plataforma de hipervisor subyacente admite **VM-GenerationID**. Azure es compatible con **VM-GenerationID**. Debido a esto, los controladores de dominio que ejecutan Windows Server 2012 o posterior en máquinas virtuales de Azure disponen de estas medidas de seguridad adicionales.

Cuando **VM-GenerationID** se restablece, el valor **InvocationID** de la base de datos de AD DS también se restablece. Además, se descarta el grupo de id. relativo (RID) y la carpeta `SYSVOL` se marca como no autoritativa. Para obtener más información, consulte [Introducción a la virtualización de Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) y [Virtualización segura de la replicación del sistema de archivos distribuido (DFSR)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/safely-virtualizing-dfsr/ba-p/424671).

La conmutación por error a Azure podría provocar el restablecimiento de **VM-GenerationID**. Restablecer **VM-GenerationID** activa medidas de seguridad adicionales cuando la máquina virtual del controlador de dominio se inicia en Azure. Esta situación puede dar lugar a un retraso importante en el inicio de sesión de la máquina virtual del controlador de dominio.

Puesto que este controlador de dominio se utiliza solo en una conmutación por error de prueba, las medidas de seguridad de virtualización no son necesarias. Para asegurarse de que no cambie el valor de **VM-GenerationID** de la máquina virtual del controlador de dominio, puede cambiar el valor de `DWORD` siguiente a **4** en el controlador de dominio local:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`

#### <a name="symptoms-of-virtualization-safeguards"></a>Síntomas de las medidas de seguridad de la virtualización

Si se han activado medidas de seguridad de virtualización después de una conmutación por error de prueba, podría ver uno o varios de los síntomas siguientes:

- El valor de **GenerationID** cambia:

  :::image type="content" source="./media/site-recovery-active-directory/Event2170.png" alt-text="Cambio del id. de generación":::

- El valor de **InvocationID** cambia:

  :::image type="content" source="./media/site-recovery-active-directory/Event1109.png" alt-text="Cambio del id. de invocación":::

- La carpeta `SYSVOL` y los recursos compartidos `NETLOGON` no están disponibles.

  :::image type="content" source="./media/site-recovery-active-directory/sysvolshare.png" alt-text="Recurso compartido de la carpeta SYSVOL":::

  :::image type="content" source="./media/site-recovery-active-directory/Event13565.png" alt-text="Carpeta SYSVOL de NtFrs":::

- Las bases de datos DFSR se eliminan.

  :::image type="content" source="./media/site-recovery-active-directory/Event2208.png" alt-text="Las bases de datos DFSR se eliminan":::

### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Solución de problemas del controlador de dominio durante la conmutación por error de prueba

> [!IMPORTANT]
> Algunas de las configuraciones que se describen en esta sección no son las configuraciones de controlador de dominio estándares o predeterminadas. Si no desea realizar estos cambios en un controlador de dominio de producción, puede crear un controlador de dominio específico para la conmutación por error de prueba de Site Recovery. Realice estos cambios únicamente en el controlador de dominio específico.

1. En un símbolo del sistema, ejecute el siguiente comando para comprobar si las carpetas `SYSVOL` y `NETLOGON` están compartidas:

    `NET SHARE`

1. En el símbolo del sistema, ejecute el siguiente comando para asegurarse de que el controlador de dominio funciona correctamente:

    `dcdiag /v > dcdiag.txt`

1. En el registro de salida, busque el texto siguiente. El texto confirma que el controlador de dominio funciona correctamente.

    - `passed test Connectivity`
    - `passed test Advertising`
    - `passed test MachineAccount`

Si se cumplen las condiciones anteriores, es probable que el controlador de dominio funcione correctamente. En caso contrario, siga los pasos a continuación:

1. Realice una restauración autoritativa del controlador de dominio. Recuerde la siguiente información:

    - Aunque no se recomienda usar el [servicio de replicación de archivos (FRS)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-end-is-nigh-for-frs-8211-updated-for-ws2016/ba-p/425379), si usa la replicación de FRS, siga los pasos para una restauración autoritativa. El proceso se describe en [Using the BurFlags registry key to reinitialize File Replication Service](https://support.microsoft.com/kb/290762) (Uso de la clave del Registro BurFlags para reinicializar el servicio de replicación de archivos).

      Para obtener más información sobre BurFlags, consulte la entrada de blog [D2 and D4: What is it for?](/archive/blogs/janelewis/d2-and-d4-what-is-it-for) (D2 y D4: ¿para qué sirven?).

    - Si utiliza la replicación DFSR, complete los pasos para una restauración autoritativa. El proceso se describe en [Cómo forzar una sincronización autoritaria y no autoritaria de SYSVOL DFSR replicado (por ejemplo, "D4/D2" para FRS)](https://support.microsoft.com/kb/2218556).

      También puede utilizar las funciones de PowerShell. Para obtener más información, consulte [DFSR-SYSVOL authoritative/non-authoritative restore PowerShell functions](/archive/blogs/thbouche/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions) (Funciones de PowerShell para restauración autoritativa y no autoritativa de DFSR-SYSVOL).

1. Para omitir el requisito de sincronización inicial, establezca la siguiente clave del Registro en **0** en el controlador de dominio local. Si la `DWORD` no existe, puede crearla en el nodo **Parameters**.

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

   Para obtener más información, consulte el artículo sobre cómo [Solucionar problemas del DNS ID 4013: El servidor DNS no pudo cargar zonas DNS integradas en AD](https://support.microsoft.com/kb/2001093).

1. Deshabilite el requisito de que haya un servidor de catálogo global disponible para validar el inicio de sesión de usuario. Para ello, en el controlador de dominio local, establezca la siguiente clave del Registro en **1**. Si la `DWORD` no existe, puede crearla en el nodo **Lsa**.

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

   Para obtener más información, consulte [cómo funciona el catálogo global](/previous-versions/windows/it-pro/windows-server-2003/cc737410(v=ws.10)).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS y controlador de dominio en equipos diferentes

Si va a ejecutar el controlador de dominio y el DNS en la misma máquina virtual, puede omitir este procedimiento.

Si el DNS no está en la misma máquina virtual que el controlador de dominio, tendrá que crear una máquina virtual de DNS para la conmutación por error de prueba. Puede utilizar un servidor DNS nuevo y crear todas las zonas necesarias. Por ejemplo, si el dominio de Active Directory es `contoso.com`, puede crear una zona DNS con el nombre `contoso.com`. Las entradas que corresponden a Active Directory deben actualizarse en el DNS de la forma siguiente:

1. Asegúrese de que está establecida esta configuración antes de iniciar cualquier otra máquina virtual en el plan de recuperación:

   - El nombre de la zona depende del nombre de raíz del bosque.
   - La zona debe estar respaldada por archivos.
   - La zona debe estar habilitada para actualizaciones seguras y no seguras.
   - La resolución de la máquina virtual que hospeda al controlador de dominio debe apuntar a la dirección IP de la máquina virtual de DNS.

1. Ejecute el siguiente comando en la máquina virtual que hospeda al controlador de dominio:

   `nltest /dsregdns`

1. Ejecute los siguientes comandos para agregar una zona en el servidor DNS, permitir actualizaciones no seguras y agregar una entrada para la zona al DNS:

   ```cmd
   dnscmd /zoneadd contoso.com  /Primary

   dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1

   dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>

   dnscmd /config contoso.com /allowupdate 1
   ```

## <a name="next-steps"></a>Pasos siguientes

[Más información](site-recovery-workload.md) sobre la protección de cargas de trabajo empresariales con Azure Site Recovery.
