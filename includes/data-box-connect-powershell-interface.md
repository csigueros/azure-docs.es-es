---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 11/15/2021
ms.author: alkohli
ms.openlocfilehash: fa4d3974a016fc57624c2c51d9aaf703583f8764
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557179"
---
En función de la versión de software que Data Box esté ejecutando, es posible que tenga que realizar diferentes pasos para conectarse a la interfaz de PowerShell del dispositivo. El primer paso en todos estos casos sería identificar la versión de software que se ejecuta en el dispositivo Data Box.

### <a name="identify-software-version-of-the-device"></a>Identificación de la versión de software del dispositivo

> [!NOTE]
> El número de versión se muestra en la interfaz de usuario local a partir de la versión 4.9 y posteriores. Si no ve un número de versión como se describe aquí en la interfaz de usuario local, póngase en contacto con Soporte técnico de Microsoft para identificar el número de versión y, a continuación, siga los pasos para conectarse a la interfaz de PowerShell del dispositivo.

Use una de las siguientes maneras de identificar la versión de software que se ejecuta en el dispositivo y el número de serie de este.

 - Vaya a la página de **inicio de sesión**. La versión del software aparece en esta página.
 
    ![Número de serie y versión del software en la página de inicio de sesión de la interfaz de usuario web local de Data Box](media/data-box-connect-powershell-interface/device-serial-number-software-version-sign-in-local-ui.png)

 - En la interfaz de usuario web local, seleccione el icono **Ayuda**. En el panel **Ayuda**, aparece la versión del software y el número de serie del dispositivo. Anote ambos datos.
 
    ![Número de serie y versión del software en el panel Ayuda de la interfaz de usuario web local de Data Box](media/data-box-connect-powershell-interface/device-serial-number-software-version-help-pane-local-ui.png)



Los pasos siguientes están determinados por la versión de software que Data Box esté ejecutando.
 
### <a name="v41-and-later"></a>[v4.1 y posteriores](#tab/c)

Para estas versiones de software, primero debe instalar los certificados en el host que acceden al dispositivo Data Box y, después, conectarse a la interfaz de PowerShell del dispositivo. Al instalar certificados, puede usar los certificados predeterminados con los que se envía el dispositivo o traer sus propios certificados. En cada caso, los pasos específicos que va a realizar son ligeramente diferentes.


### <a name="step-1-install-certificate-on-host-or-client"></a>Paso 1: Instalación del certificado en el host o el cliente

#### <a name="use-default-certificates"></a>Uso de certificados predeterminados 

Siga estos pasos si va a usar los certificados predeterminados (incluidos con el dispositivo) en Data Box.

1. En Azure Portal, vaya al recurso de pedido del dispositivo Data Box. Vaya a **General > Device details** (General > Detalles del dispositivo). En la pestaña **API access to the device** (Acceso de API al dispositivo), seleccione **Descargar**.

    ![Descarga del certificado de Data Box en Azure Portal](media/data-box-connect-powershell-interface/download-certificate-data-box-portal.png)

1. Copie el certificado en el host que se conecta a Data Box. Si usa un host de Windows, abra Explorer. Haga clic con el botón derecho en el archivo de certificado que copió en el host y seleccione **Instalar certificado**. 

1. Complete los pasos de instalación mediante el **Asistente para importación de certificados**. Para obtener pasos detallados, consulte [Importación de certificados en el cliente](../articles/databox/data-box-bring-your-own-certificates.md#import-certificates-to-client). 

1. Agregue una entrada al archivo host que asigna la dirección IP del dispositivo al FQDN de este. El formato de la entrada será: 

    `<Device IP address>  <Device serial number>.microsoftdatabox.com`

    Proporcione el número de serie del dispositivo del paso anterior. 

    ![Obtención del número de serie del dispositivo Data Box a partir de la interfaz de usuario local](media/data-box-connect-powershell-interface/get-device-serial-number-portal.png)
 

#### <a name="use-your-own-certificates"></a>Uso de tus propios certificados 

Siga estos pasos si va a traer sus propios certificados a Data Box.
 
1. Instale el certificado de la cadena de firma que trae, en la máquina host que se conectará a Data Box. Abra el certificado mediante Explorer, haga clic con el botón derecho en el archivo y seleccione **Instalar certificado**. 
1. Siga los pasos que se indican en [Importar certificados en el cliente](../articles/databox/data-box-bring-your-own-certificates.md#import-certificates-to-client) e instale el certificado en la máquina local o raíz. 
1. Agregue una entrada al archivo host que asigna la dirección IP del dispositivo al FQDN de este. El formato de la entrada será: 

    `<Device IP address>  <Name>.<DNS domain>` 

    Para obtener el **nombre** y el **dominio DNS** del dispositivo, en la interfaz de usuario local, vaya a la página **Certificados**. 
1. Para cargar los certificados, en la interfaz de usuario local, vaya a la página **Certificados**. Seleccione **+ Agregar certificados** y proporcione el certificado. Consulte [Carga de certificados](../articles/databox/data-box-bring-your-own-certificates.md#add-certificates-to-device).
 

### <a name="step-2-connect-to-the-powershell-interface"></a>Paso 2: Conexión a la interfaz de PowerShell


1. Establezca el parámetro `$Name`.

    ```powershell
    $Name = <Name>.<DNS domain>
    ```
    Proporcione el **nombre** y el **dominio DNS** del dispositivo de los pasos anteriores.

1. Agregue el dispositivo Data Box a la lista `TrustedHosts` mediante el parámetro `$Name`. Tipo:
 
    ```powershell
    Set-Item wsman:\localhost\Client\TrustedHosts $Name 
    ```

1. Obtenga la contraseña de la interfaz de usuario local en Azure Portal. Cree una cadena segura que contenga la contraseña.

    ```powershell
    $Pwd = ConvertTo-SecureString <Password from Azure portal> -AsPlainText -Force 
    ```
   <!--

    ```powershell
    $Cred = New-Object System.Management.Automation.PSCredential("<ipv4_address of databox>\StorSimpleAdmin",$Pwd) 
    ```-->

1. Establezca la conexión.

    ```powershell
    Enter-PSSession -ComputerName $Name -ConfigurationName Minishell -Credential ~\StorSimpleAdmin -UseSSL  
    ```
    A continuación, se incluye una salida de ejemplo:

    ```powershell
    PS C:\Users\Administrator> winrm quickconfig
    WinRM service is already running on this machine.
    WinRM is already set up for remote management on this computer.
    PS C:\Users\Administrator> $Name = "by506b4b5d0790.microsoftdatabox.com"
    PS C:\Users\Administrator> Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
    PS C:\Users\Administrator> Enter-PSSession -ComputerName $Name -Credential ~\StorSimpleAdmin -ConfigurationName Minishell -UseSSL
    WARNING: Please engage Microsoft Support if you need to access this interface
    to troubleshoot any potential issues you may be experiencing.
    Changes made through this interface without involving Microsoft
    Support could result in an unsupported configuration.
    [by506b4b5d0790.microsoftdatabox.com]: PS>
    ```

### <a name="skip-certificate-validation"></a>Omita la validación del certificado.

Si no usa los certificados (se recomienda usarlos), puede omitir la comprobación de la validación de certificados mediante las opciones de sesión: `-SkipCACheck -SkipCNCheck -SkipRevocationCheck`.

Siga estos pasos para omitir la validación del certificado:

1. Establezca el parámetro `$Name`.

    ```powershell
    $Name = <Name>.<DNS domain>
    ```
 
1. Use las opciones de sesión al abrir una sesión de PowerShell.

    ```powershell
    $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
    ```
1. Establezca la conexión.

    ```powershell
    Enter-PSSession -ComputerName $Name -ConfigurationName Minishell -Credential ~\StorSimpleAdmin -UseSSL -SessionOption $sessOptions 
    ```

### <a name="v30-to-v40"></a>[v3.0 a v4.0](#tab/b)

1. Abra una sesión de PowerShell mediante **Ejecutar como administrador**. 
1. Agregue el dispositivo Data Box a la lista `TrustedHosts` mediante la dirección IP. Tipo:

    ```powershell
    Set-Item Wsman:\localhost\Client\TrustedHosts <IPv4_address for your Data Box> 
    ``` 

1. Obtenga la contraseña del recurso de pedido de Data Box en Azure Portal. 

1. Convierta la contraseña proporcionada en texto sin formato en una cadena segura. Tipo:

    ```powershell
    $Pwd = ConvertTo-SecureString <Password from Azure portal> -AsPlainText -Force 
    ```
1. Proporcione el nombre de usuario y la contraseña de la sesión y cree un objeto de credencial. El nombre de usuario predeterminado es `StorSimpleAdmin` y la contraseña que obtuvo en el paso anterior.

    ```powershell
    $Cred = New-Object System.Management.Automation.PSCredential("~\StorSimpleAdmin",$Pwd)
    ``` 
1. Inicie una sesión con el dispositivo.

    ```powershell
    Enter-PSSession -Computer <IPv4_address for your Data Box> -ConfigurationName Minishell -Credential $Cred 
    ```

    Esta es una salida de ejemplo.
    
    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    
    PS C:\WINDOWS\system32> Set-Item wsman:\localhost\Client\TrustedHosts "10.128.45.52"
    
    WinRM Security Configuration.
    This command modifies the TrustedHosts list for the WinRM client. The computers in the
    TrustedHosts list might not be authenticated. The client might send credential information to
     these computers. Are you sure that you want to modify this list?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    PS C:\WINDOWS\system32> $Pwd = ConvertTo-SecureString "Password1" -AsPlainText -Force
    PS C:\WINDOWS\system32> $Cred = New-Object System.Management.Automation.PSCredential("~\StorSimpleAdmin",$Pwd)
    PS C:\WINDOWS\system32> Enter-PSSession -Computer "10.128.45.52" -ConfigurationName Minishell -Credential $Cred
    WARNING: Please engage Microsoft Support if you need to access this interface
    to troubleshoot any potential issues you may be experiencing.
    Changes made through this interface without involving Microsoft
    Support could result in an unsupported configuration.
    [10.128.45.52]: PS>
    ```




  

