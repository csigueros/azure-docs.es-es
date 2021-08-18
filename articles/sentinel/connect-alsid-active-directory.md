---
title: Conexión de Alsid para Active Directory a Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector Alsid para Active Directory para extraer registros de Alsid en Azure Sentinel. Vea los datos de Alsid en los libros, cree alertas y mejore la investigación.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 50b1256849bd469c2729896f6a3e10b90431fe91
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252329"
---
# <a name="connect-your-alsid-for-active-directory-ad-to-azure-sentinel"></a>Conexión de Alsid para Active Directory (AD) a Azure Sentinel

> [!IMPORTANT]
> El conector Alsid para Active Directory está actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar la solución Alsid para AD a Azure Sentinel. El conector de datos de Alsid para Active Directory le permite conectar fácilmente los registros de Alsid para AD con Azure Sentinel, de modo que pueda ver los datos en los libros, consultarlos para crear alertas personalizadas e incorporarlos para mejorar la investigación. La integración entre Alsid para AD y Azure Sentinel permite el uso de un servidor de Syslog con el agente de Log Analytics instalado. También usa un analizador de registro personalizado basado en una función de Kusto.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permiso de escritura en el área de trabajo de Azure Sentinel.

- La solución Alsid para AD debe estar configurada para exportar registros a través de Syslog.

## <a name="send-alsid-for-ad-logs-to-azure-sentinel-via-the-syslog-agent"></a>Envío de registros de Alsid para AD a Azure Sentinel a través del agente de Syslog

Configure Alsid para AD para reenviar mensajes de Syslog a su área de trabajo de Azure Sentinel a través del agente de Syslog.

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. En la galería **Conectores de datos**, seleccione el conector **Alsid para Active Directory (versión preliminar)** y, a continuación, seleccione la **página Abrir conector**.

1. Siga las instrucciones que aparecen en la página del conector **Alsid para Active Directory**:

    1. Configuración de un servidor de Syslog

        1. Si aún no tiene ninguno, cree un servidor de Syslog de Linux para Alsid para AD al que enviar los registros. Azure Sentinel admite los demonios **rsyslog** y **syslog-ng**. 

        1. Configure el servidor de Syslog para que la salida de los registros de Alsid para AD se lleve a cabo en un archivo independiente.

    1. Configuración de Alsid para AD para enviar registros al servidor de Syslog

        1. En el portal de **Alsid para AD**, vaya a *Sistema*, *Configuración* y, a continuación, *Syslog*. Desde allí puede crear una nueva alerta de Syslog para el servidor de Syslog. En el caso del servidor remoto, use la dirección IP de la máquina Linux en la que instaló el agente de Linux.

        1. Compruebe que los registros se recopilan correctamente en el servidor en un archivo independiente (para ello, puede usar el botón **Test de configuration** (Probar la configuración) en la configuración de alertas de *Syslog* en Alsid para AD).

    1. Instalación e incorporación del agente de Log Analytics para Linux

        - Elija una VM Linux de Azure o una máquina Linux que no sea de Azure (física o virtual). Siga los vínculos y las instrucciones de la pantalla. Consulte [Configuración de la máquina o el dispositivo Linux](connect-syslog.md#configure-your-linux-machine-or-appliance) para obtener más información.

    1. Configuración de los registros que van a recopilar los agentes de Log Analytics

        - Seleccione los recursos y los niveles de gravedad en la configuración avanzada del área de trabajo.

            1. Haga clic en el vínculo **Open your workspace advanced settings configuration** (Abrir la configuración de las opciones avanzadas del área de trabajo) en la página del conector.

            1. En la pantalla **Opciones avanzadas**, seleccione **Datos** y, a continuación, **Registros personalizados**.

            1. Active la casilla **Aplicar la configuración que aparece a continuación a mis máquinas con Linux** y haga clic en **Agregar**.

            1. Haga clic en **Elegir archivo** para cargar un archivo de Syslog de Alsid para AD desde la máquina Linux que ejecuta el servidor de Syslog y haga clic en **Siguiente**.

            1. Compruebe que **Set record delimiter** (Establecer delimitador de registros) está establecido en **Nueva línea** y haga clic en **Siguiente**.

            1. Seleccione **Linux** y escriba la ruta de acceso al archivo de Syslog, haga clic en **+** y, a continuación en **Siguiente**.

            1. En el campo Nombre, escriba *AlsidForADLog* antes del sufijo _CL y, a continuación, haga clic en **Listo**.
    
Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en la opción **Registros** de **Registros personalizados** de la tabla *AlsidForADLog_CL*.

Este conector de datos depende de un analizador basado en una función de Kusto para funcionar según lo previsto. Utilice los pasos siguientes para configurar la función de Kusto **afad_parser** que se va a usar en las consultas y los libros.

1. En el menú de navegación de Azure Sentinel, seleccione **Registros**.

1. Copie la consulta siguiente y péguela en la ventana de consulta.
    ```kusto
    let CodenameTable=datatable(Codename: string, Explanation: string) [
    "test-checker-codename", "This is a test checker",
    "", "Not an alert",
    "C-ADM-ACC-USAGE", "Recent use of the default administrator account",
    "C-UNCONST-DELEG", "Dangerous delegation",
    "C-PASSWORD-DONT-EXPIRE", "Accounts with never expiring passwords",
    "C-USERS-CAN-JOIN-COMPUTERS", "Users allowed to join computers to the domain",
    "C-CLEARTEXT-PASSWORD", "Potential clear-text password",
    "C-PROTECTED-USERS-GROUP-UNUSED", "Protected Users group not used",
    "C-PASSWORD-POLICY", "Weak password policies are applied on users",
    "C-GPO-HARDENING", "Domain without computer-hardening GPOs",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-AAD-CONNECT", "Verify permissions related to AAD Connect accounts",
    "C-AAD-SSO-PASSWORD", "Verify AAD SSO account password last change",
    "C-GPO-SD-CONSISTENCY", "Verify sensitive GPO objects and files permissions",
    "C-DSHEURISTICS", "Domain using a dangerous backward-compatibility configuration",
    "C-DOMAIN-FUNCTIONAL-LEVEL", "Domains have an outdated functional level",
    "C-DISABLED-ACCOUNTS-PRIV-GROUPS", "Disabled accounts in privileged groups",
    "C-DCSHADOW", "Rogue domain controllers",
    "C-DC-ACCESS-CONSISTENCY", "Domain controllers managed by illegitimate users",
    "C-DANGEROUS-TRUST-RELATIONSHIP", "Dangerous trust relationship",
    "C-DANGEROUS-SENSITIVE-PRIVILEGES", "Dangerous sensitive privileges",
    "C-DANG-PRIMGROUPID", "User Primary Group ID",
    "C-BAD-PASSWORD-COUNT", "Brute-force attack detection",
    "C-ADMINCOUNT-ACCOUNT-PROPS", "AdminCount attribute set on standard users",
    "C-ACCOUNTS-DANG-SID-HISTORY", "Accounts having a dangerous SID History attribute",
    "C-ABNORMAL-ENTRIES-IN-SCHEMA", "Dangerous rights in AD's schema",
    "C-GPOLICY-DISABLED-UNLINKED", "Unlinked, disabled or orphan GPO",
    "C-KERBEROS-CONFIG-ACCOUNT", "Kerberos configuration on user account",
    "C-KRBTGT-PASSWORD", "KDC password last change",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-NATIVE-ADM-GROUP-MEMBERS", "Native administrative group members",
    "C-NETLOGON-SECURITY", "Unsecured configuration of Netlogon protocol",
    "C-OBSOLETE-SYSTEMS", "Computers running an obsolete OS",
    "C-PASSWORD-NOT-REQUIRED", "Account that might have an empty password",
    "C-PKI-WEAK-CRYPTO", "Use of weak cryptography algorithms into Active Directory PKI",
    "C-PRE-WIN2000-ACCESS-MEMBERS", "Accounts using a pre-Windows 2000 compatible access control",
    "C-PRIV-ACCOUNTS-SPN", "Privileged accounts running Kerberos services",
    "C-REVER-PWD-GPO", "Reversible passwords in GPO",
    "C-ROOTOBJECTS-SD-CONSISTENCY", "Root objects permissions allowing DCSync-like attacks",
    "C-SDPROP-CONSISTENCY", "Ensure SDProp consistency",
    "C-SENSITIVE-CERTIFICATES-ON-USER", "Ensure SDProp consistency",
    "C-SLEEPING-ACCOUNTS", "Sleeping accounts",
    "C-USER-PASSWORD", "User account using old password",
    "C-USERS-REVER-PWDS", "Reversible passwords"
    ];
    let Common = AlsidForADLog_CL
    | parse RawData with
                         Time:datetime  " "
                         Host:string  " "
                         Product:string "["
                         PID:int "]: \""
                         MessageType:int "\" \""
                         AlertID:int "\" \""
                         Forest:string "\" \""
                         Domain:string "\" "
                         DistinctPart:string;
    let Deviances = Common
    | where MessageType == 0 | parse DistinctPart with "\""
                         Codename:string "\" \""
                         Severity:string "\" \""
                         ADObject:string "\" \""
                         DevianceID:string "\" \""
                         ProfileID:string "\" \""
                         ReasonCodename:string "\" \""
                         EventID:string "\""
                         Attributes:string "\r\n";
    let Changes = Common
    | where MessageType == 1
    | parse kind=regex DistinctPart with "\""
                         ADObject:string "\" \""
                         EventID:string "\" \""
                         EventType:string "\" "
                         Attributes:string "\r?\n";
    union Changes, Deviances
    | project-away DistinctPart, Product, _ResourceId, _SubscriptionId
    | lookup kind=leftouter CodenameTable on Codename;
    ```

1. Haga clic en el menú desplegable **Guardar** y haga clic en **Guardar**. En el panel **Guardar**:

    1. En **Nombre**, escriba **afad_parser**.

    1. En **Guardar como**, elija **Función**.

    1. En **Alias de función**, escriba **afad_parser**.

    1. En **Categoría**, escriba **Funciones**.

    1. Haga clic en **Save**(Guardar).

    Las aplicaciones de funciones normalmente tardan entre 10 y 15 minutos en activarse.

Ahora está listo para consultar datos de Alsid para AD; para ello, escriba `afad_parser` en la línea superior de la ventana de consulta.

Consulte la pestaña **Pasos siguientes** de la página de conectores para más ejemplos de consulta.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Alsid para AD a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.