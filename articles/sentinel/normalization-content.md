---
title: Contenido del modelo de información de Azure Sentinel (ASIM) | Microsoft Docs
description: En este artículo se describe el contenido de Azure Sentinel que ha utilizado el modelo de información de Azure Sentinel (ASIM).
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2021
ms.author: ofshezaf
ms.openlocfilehash: deb5377aef61736a14ce8110e96c16e5352096cd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552262"
---
# <a name="azure-sentinel-information-model-asim-security-content--public-preview"></a>Contenido de seguridad del modelo de información de Azure Sentinel (ASIM) (versión preliminar pública)

El contenido de seguridad normalizado de Azure Sentinel incluye reglas de análisis, consultas de búsqueda y libros que funcionan con analizadores de normalización independientes de origen.

<a name="builtin"></a>Puede encontrar contenido integrado normalizado en galerías y [soluciones](sentinel-solutions-catalog.md) de Azure Sentinel, crear su propio contenido normalizado o modificar el contenido existente para usar datos normalizados.

En este artículo se enumera el contenido de Azure Sentinel integrado que se ha configurado para admitir ASIM.  Aunque a continuación se proporcionan vínculos al repositorio de GitHub de Azure Sentinel como referencia, también puede encontrar estas reglas en la [galería de reglas de Azure Sentinel Analytics](detect-threats-built-in.md). Use las páginas de GitHub vinculadas para copiar las consultas de búsqueda pertinentes.

> [!TIP]
> Vea también el [seminario web de profundización sobre los analizadores de normalización de Azure Sentinel y el contenido normalizado](https://www.youtube.com/watch?v=zaqblyjQW6k) o revise las [diapositivas](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM). Para más información, consulte la sección [Pasos siguientes](#next-steps).
>

> [!IMPORTANT]
> ASIM está actualmente en versión preliminar. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="authentication-security-content"></a>Contenido de seguridad de autenticación

El siguiente contenido de autenticación integrado es compatible con la normalización de ASIM.

### <a name="analytics-rules"></a>Reglas de análisis

 - [Posible ataque de difusión de contraseña (usa la normalización de autenticación)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthPasswordSpray.yaml)
 - [Ataque por fuerza bruta contra las credenciales de usuario (usa la normalización de autenticación)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthBruteForce.yaml)
 - [Inicio de sesión de usuario de distintos países en un plazo de 3 horas (usa la normalización de autenticación)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthSigninsMultipleCountries.yaml)
 - [Inicios de sesión desde direcciones IP que intentan iniciar sesión en cuentas deshabilitadas (usa la normalización de autenticación)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imSigninAttemptsByIPviaDisabledAccounts.yaml)


## <a name="dns-query-security-content"></a>Contenido de seguridad de consultas de DNS

El siguiente contenido de consultas de DNS integrado es compatible con la normalización de ASIM.

### <a name="analytics-rules"></a>Reglas de análisis

 - (Versión preliminar) Entidad de dominio de asignación de TI a eventos DNS (DNS normalizado)
 - (Versión preliminar) Entidad de IP de asignación de TI a eventos DNS (DNS normalizado)
 - [DGA potencial detectado (ASimDNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_HighNXDomainCount_detection.yaml)
  - [Consultas de DNS NXDOMAIN excesivas (DNS normalizado)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_ExcessiveNXDOMAINDNSQueries.yaml)
 - [Eventos de DNS relacionados con grupos de minería de datos (DNS normalizado)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_Miners.yaml)
 - [Eventos de DNS relacionados con servidores proxy Tor (DNS normalizado)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_TorProxies.yaml)
 - [Dominios conocidos de Barium](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumDomainIOC112020.yaml)
 - [Direcciones IP conocidas de Barium](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumIPIOC112020.yaml) 
 - [Vulnerabilidades del servidor Exchange divulgadas en marzo de 2021 sobre coincidencias de IoC](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
 - [Códigos hash y dominios GALLIUM conocidos](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
 - [Known IRIDIUM IP (Dirección IP de IRIDIUM conocida)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
 - [NOBELIUM, indicadores de riesgo de dominio e IP: marzo de 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)
 - [IP/dominios de grupo de Phosphorus conocidos](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
 - [Dominios de grupo conocidos de STRONTIUM: julio de 2019](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
 - [Señal de red Solorigate](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
 - [Dominios de THALLIUM incluidos en el ataque de DCU](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
 - [Hashes de malware conocidos de Comebacker y Hashckring de ZINC](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

## <a name="file-activity-security-content"></a>Contenido de seguridad de la actividad del archivo

El siguiente contenido de actividad del archivo integrado es compatible con la normalización de ASIM.

### <a name="analytic-rules"></a>Reglas de análisis

- [Hashes de puerta trasera SUNBURST y SUPERNOVA (eventos de archivos normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimFileEvent/imFileESolarWindsSunburstSupernova.yaml)
- [Vulnerabilidades del servidor Exchange divulgadas en marzo de 2021 sobre coincidencias de IoC](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
- [El servicio HAFNIUM UM escribe un archivo sospechoso.](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/HAFNIUMUmServiceSuspiciousFile.yaml)
- [NOBELIUM: IOC de dominio, hash e IP (mayo de 2021)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_IOCsMay2021.yaml)
- [Creación de archivos de registro de SUNSPOT](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/SUNSPOTLogFile.yaml)
- [Hashes de malware conocidos de Comebacker y Hashckring de ZINC](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

## <a name="process-activity-security-content"></a>Contenido de seguridad de la actividad del proceso

El siguiente contenido de actividad del proceso integrado es compatible con la normalización de ASIM.

### <a name="analytics-rules"></a>Reglas de análisis

 - [Probable uso de la herramienta AdFind Recon (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_AdFind_Usage.yaml)
 - [Líneas de comandos de procesos de Windows codificadas en Base64 (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_base64_encoded_pefile.yaml)
 - [Malware en la papelera de reciclaje (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_malware_in_recyclebin.yaml)
 - [NOBELIUM: ejecución sospechosa de rundll32.exe de vbscript (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_NOBELIUM_SuspiciousRundll32Exec.yaml)
 - [SUNBURST: procesos secundarios sospechosos de SolarWinds (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_SolarWinds_SUNBURST_Process-IOCs.yaml)

### <a name="hunting-queries"></a>Consultas de búsqueda

 - [Desglose de resumen diario de scripts de Cscript (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_cscript_summary.yaml)
 - [Enumeración de usuarios y grupos (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_enumeration_user_and_group.yaml)
 - [Complemento de Exchange PowerShell agregado (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ExchangePowerShellSnapin.yaml)
 - [Host exportando buzón y quitando exportación (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_HostExportingMailboxAndRemovingExport.yaml)
 - [Uso de Invoke-PowerShellTcpOneLine (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Invoke-PowerShellTcpOneLine.yaml)
 - [Shell TCP inverso en Base64 (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_NishangReverseTCPShellBase64.yaml)
 - [Resumen de usuarios creados mediante modificadores de línea de comandos poco comunes o no documentados (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_persistence_create_account.yaml)
 - [Descarga de Powercat (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_PowerCatDownload.yaml)
 - [Descargas de PowerShell (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_powershell_downloads.yaml)
 - [Entropía para los procesos de un host determinado (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ProcessEntropy.yaml)
 - [Inventario de SolarWinds (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_SolarWindsInventory.yaml)
 - [Enumeración sospechosa mediante la herramienta Adfind (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Suspicious_enumeration_using_adfind.yaml)
 - [Apagado/reinicio del sistema de Windows (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Windows%20System%20Shutdown-Reboot(T1529).yaml)
 - [Certutil (LOLBins y LOLScripts, eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Certutil-LOLBins.yaml)
 - [Rundll32 (LOLBins y LOLScripts, eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/inProcess_SignedBinaryProxyExecutionRundll32.yaml)
 - [Procesos poco comunes: inferiores al 5 % (eventos de proceso normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_uncommon_processes.yaml)
 - [Ofuscación Unicode en la línea de comandos](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/UnicodeObfuscationInCommandLine.yaml)

## <a name="registry-activity-security-content"></a>Contenido de seguridad de la actividad del Registro

El siguiente contenido de actividad del Registro integrado es compatible con la normalización de ASIM.

### <a name="hunting-queries"></a>Consultas de búsqueda

- [Conservación a través de la clave del Registro IFEO](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/PersistViaIFEORegistryKey.yaml)

## <a name="modify-your-content-to-use-normalized-data"></a><a name="modify"></a>Modificación del contenido para usar datos normalizados

Para posibilitar que su contenido personalizado use la normalización, haga lo siguiente:

- Modifique sus consultas para usar los analizadores independientes de origen pertinentes para la consulta.
- Modifique los nombres de campo de la consulta para usar los nombres de campo de esquema normalizados.
- Si procede, cambie las condiciones para usar los valores normalizados de los campos en la consulta.

Por ejemplo, considere la regla de análisis de DNS **Rare client observed with high reverse DNS lookup count**, que funciona en eventos de DNS enviados por servidores DNS de Infoblox:

```kusto
let threshold = 200;
InfobloxNIOS
| where ProcessName =~ "named" and Log_Type =~ "client"
| where isnotempty(ResponseCode)
| where ResponseCode =~ "NXDOMAIN"
| summarize count() by Client_IP, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (InfobloxNIOS
    | where ProcessName =~ "named" and Log_Type =~ "client"
    | where isnotempty(ResponseCode)
    | where ResponseCode =~ "NXDOMAIN"
    ) on Client_IP
| extend timestamp = TimeGenerated, IPCustomEntity = Client_IP
```

El código siguiente es la versión independiente de origen, que usa la normalización para proporcionar la misma detección para cualquier origen que proporcione eventos de consulta de DNS:

```kusto
imDns(responsecodename='NXDOMAIN')
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns(responsecodename='NXDOMAIN')) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr```
```

La versión independiente de origen normalizada tiene las siguientes diferencias:

- Se usa el analizador normalizado `imDns` en lugar del analizador de Infoblox.

- `imDns` solo captura eventos de consulta de DNS, por lo que no es necesario comprobar el tipo de evento, tal y como hace `where ProcessName =~ "named" and Log_Type =~ "client"` en la versión de Infoblox.

- El campo `SrcIpAddr` se usa en lugar de `Client_IP`.
 
- El filtrado de parámetros del analizador se usa para ResponseCodeName, lo que elimina la necesidad de cláusulas where explícitas.


Además de admitir cualquier origen DNS normalizado, la versión normalizada es más corta y fácil de entender. 

Si el esquema o los analizadores no admiten parámetros de filtrado, los cambios son similares, excepto el último. En su lugar, las condiciones de filtrado se mantienen de la consulta original como se muestra a continuación:

```kusto
let threshold = 200;
imDns
| where isnotempty(ResponseCodeName)
| where ResponseCodeName =~ "NXDOMAIN"
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns
    | where isnotempty(ResponseCodeName)
    | where ResponseCodeName =~ "NXDOMAIN"
    ) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr
```

## <a name="next-steps"></a><a name="next-steps"></a>Pasos siguientes

En este artículo se describe el contenido del modelo de información de Azure Sentinel (ASIM).

Para más información, consulte:

- Vea el [seminario web de profundización sobre los analizadores de normalización de Azure Sentinel y el contenido normalizado](https://www.youtube.com/watch?v=zaqblyjQW6k) o revise las [diapositivas](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM).
- [Información general del modelo de información de Azure Sentinel](normalization.md)
- [Esquemas del modelo de información de Azure Sentinel](normalization-about-schemas.md)
- [Analizadores del modelo de información de Azure Sentinel](normalization-about-parsers.md)