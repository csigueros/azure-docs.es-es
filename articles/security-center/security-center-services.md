---
title: Características de Azure Security Center según el sistema operativo, el tipo de máquina y la nube
description: Descubra si las características de Azure Security Center están disponibles en función del sistema operativo, el tipo de máquina y la implementación en la nube.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 08/18/2021
ms.custom: references_regions
ms.author: memildin
ms.openlocfilehash: 3174aff7529e8248cffb39ee10684e843b4fd0b1
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418577"
---
# <a name="feature-coverage-for-machines"></a>Cobertura de características para las máquinas

En las dos tablas siguientes, se muestran las características de Azure Security Center disponibles para máquinas virtuales Windows y Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Características admitidas para máquinas virtuales y servidores <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Máquinas Windows**](#tab/features-windows)

|**Característica**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Máquinas habilitadas para Azure Arc**|**Azure Defender necesario**
|----|:----:|:----:|:----:|:----:|
|[Integración de Microsoft Defender for Endpoint](security-center-wdatp.md)|✔</br>✔ (en versiones admitidas)|✔</br>✔ (en versiones admitidas)|✔|Sí|
|[Análisis del comportamiento de máquinas virtuales (y alertas de seguridad)](alerts-reference.md)|✔|✔|✔|Sí|
|[Alertas de seguridad sin archivos](alerts-reference.md#alerts-windows)|✔|✔|✔|Sí|
|[Alertas de seguridad basadas en la red](other-threat-protections.md#network-layer)|✔|✔|-|Sí|
|[Acceso de máquina virtual Just-In-Time](security-center-just-in-time.md)|✔|-|-|Sí|
|[Evaluación de vulnerabilidades nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Sí|
|[Supervisión de la integridad de los archivos](security-center-file-integrity-monitoring.md)|✔|✔|✔|Sí|
|[Controles de aplicación adaptables](security-center-adaptive-application.md)|✔|-|✔|Sí|
|[Mapa de red](security-center-network-recommendations.md#network-map)|✔|✔|-|Sí|
|[Protección de red adaptable](security-center-adaptive-network-hardening.md)|✔|-|-|Sí|
|[Panel e informes de cumplimiento normativo](security-center-compliance-dashboard.md)|✔|✔|✔|Sí|
|[Protección de hosts de Docker](./harden-docker-hosts.md)|-|-|-|Sí|
|Evaluación de revisiones de SO que faltan|✔|✔|✔|Azure: No<br><br>Habilitada para Arc: Sí|
|Evaluación de configuraciones de seguridad incorrectas|✔|✔|✔|Azure: No<br><br>Habilitada para Arc: Sí|
|[Evaluación de EndPoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: No<br><br>Habilitada para Arc: Sí|
|Evaluación de Disk Encryption|✔</br>(para [escenarios admitidos](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|No|
|Evaluación de vulnerabilidades de terceros|✔|-|✔|No|
|[Evaluación de la seguridad de red](security-center-network-recommendations.md)|✔|✔|-|No|
||||||

### <a name="linux-machines"></a>[**Máquinas Linux**](#tab/features-linux)

| **Característica**                                                                                                               | **Azure Virtual Machines**                                                                                      | **Azure Virtual Machine Scale Sets** | **Máquinas habilitadas para Azure Arc** | **Azure Defender necesario**       |
|---------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------:|:------------------------------------:|:------------------------------:|:---------------------------------:|
| [Integración de Microsoft Defender for Endpoint](security-center-wdatp.md)                                                   | ✔                                                                                                               | -                                    | ✔                              | Sí                              |
| [Análisis del comportamiento de máquinas virtuales (y alertas de seguridad)](./azure-defender.md)                                         | ✔</br>✔ (en versiones admitidas)                                                                                  | ✔</br>✔ (en versiones admitidas)        | ✔                             | Sí                               |
| [Alertas de seguridad sin archivos](alerts-reference.md#alerts-windows)                                                            | -                                                                                                               | -                                    | -                              | Sí                               |
| [Alertas de seguridad basadas en la red](other-threat-protections.md#network-layer)                                                | ✔                                                                                                              | ✔                                    | -                              | Sí                               |
| [Acceso de máquina virtual Just-In-Time](security-center-just-in-time.md)                                                                 | ✔                                                                                                              | -                                    | -                              | Sí                               |
| [Evaluación de vulnerabilidades nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner) | ✔                                                                                                              | -                                    | ✔                             | Sí                               |
| [Supervisión de la integridad de los archivos](security-center-file-integrity-monitoring.md)                                                 | ✔                                                                                                              | ✔                                    | ✔                             | Sí                               |
| [Controles de aplicación adaptables](security-center-adaptive-application.md)                                                  | ✔                                                                                                              | -                                    | ✔                             | Sí                               |
| [Mapa de red](security-center-network-recommendations.md#network-map)                                                     | ✔                                                                                                              | ✔                                    | -                              | Sí                               |
| [Protección de red adaptable](security-center-adaptive-network-hardening.md)                                               | ✔                                                                                                              | -                                    | -                              | Sí                               |
| [Panel e informes de cumplimiento normativo](security-center-compliance-dashboard.md)                                      | ✔                                                                                                              | ✔                                    | ✔                             | Sí                               |
| [Protección de hosts de Docker](./harden-docker-hosts.md)                                                                         | ✔                                                                                                              | ✔                                    | ✔                             | Sí                               |
| Evaluación de revisiones de SO que faltan                                                                                             | ✔                                                                                                              | ✔                                    | ✔                             | Azure: No<br><br>Habilitada para Arc: Sí |
| Evaluación de configuraciones de seguridad incorrectas                                                                                     | ✔                                                                                                              | ✔                                    | ✔                             | Azure: No<br><br>Habilitada para Arc: Sí |
| [Evaluación de EndPoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)                    | -                                                                                                               | -                                    | -                              | No                                |
| Evaluación de Disk Encryption                                                                                                | ✔</br>(para [escenarios admitidos](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)) | ✔                                    | -                              | No                                |
| Evaluación de vulnerabilidades de terceros                                                                                      | ✔                                                                                                              | -                                    | ✔                             | No                                |
| [Evaluación de la seguridad de red](security-center-network-recommendations.md)                                                 | ✔                                                                                                              | ✔                                    | -                              | No                                |
||||||

--- 


> [!TIP]
>Para experimentar con características que solo están disponibles con Azure Defender, puede inscribirse en una evaluación de treinta días. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/security-center/)para obtener más información.


## <a name="supported-endpoint-protection-solutions"></a>Soluciones de protección de punto de conexión compatibles <a name="endpoint-supported"></a>

En la tabla siguiente se proporciona una matriz de:

 - Si puede usar Azure Security Center para instalar cada solución para usted.
 - Qué soluciones de protección de punto de conexión puede detectar Security Center. Si se detecta una de las soluciones de protección de punto de conexión de esta lista, Security Center no recomendará instalar ninguna.

Para más información sobre cuándo se generan recomendaciones para cada una de estas protecciones, vea [Evaluación y recomendaciones de Endpoint Protection](security-center-endpoint-protection.md).

| Solución                                                  | Plataformas compatibles                                    | Instalación de Security Center | Detección de Security Center |
|-----------------------------------------------------------|--------------------------------------------------------|------------------------------|---------------------------|
| Antivirus de Microsoft Defender                              | Windows Server 2016 o posterior                           | No (integrado en el sistema operativo)           | Sí                       |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2                                 | Mediante extensión                | Sí                       |
| Trend Micro: Deep Security                               | Windows Server (todos)                                   | No                           | Sí                       |
| Symantec v12.1.1100+                                      | Windows Server (todos)                                   | No                           | Sí                       |
| McAfee v10 o posterior                                               | Windows Server (todos)                                   | No                           | Sí                       |
| McAfee v10 o posterior                                               | Linux (versión preliminar)                                        | No                           | Sí                       |
| Sophos V9+                                                | Linux (versión preliminar)                                        | No                           | Sí                       |
|                                                           |                                                        |                              |                           |



## <a name="feature-support-in-government-and-sovereign-clouds"></a>Compatibilidad de características en las nubes gubernamentales y soberanas

| Característica o servicio                                                                                                                                           | Azure          | Azure Government               | Azure China 21Vianet   |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|---------------|
| **Características gratuitas de Security Center**                                                                                                                         |                |                                |               |
| - [Exportación continua](./continuous-export.md)                                                                                                             | GA             | GA                             | GA            |
| - [Automatización de flujos de trabajo](./continuous-export.md)                                                                                                           | GA             | GA                             | GA            |
| - [Reglas de exención de recomendaciones](./exempt-resource.md)                                                                                                  | Vista previa pública | No disponible                  | No disponible |
| - [Reglas de eliminación de alertas](./alerts-suppression-rules.md)                                                                                                | GA             | GA                             | GA            |
| - [Notificaciones de correo electrónico para alertas de seguridad](./security-center-provide-security-contact-details.md)                                                        | GA             | GA                             | GA            |
| - [Aprovisionamiento automático de agentes y extensiones](./security-center-enable-data-collection.md)                                                              | GA             | GA                             | GA            |
| - [Inventario de recursos](./asset-inventory.md)                                                                                                                 | GA             | GA                             | GA            |
| - [Informes de libros de Azure Monitor en la galería de libros de Azure Security Center](./custom-dashboards-azure-workbooks.md)                                  | GA             | GA                             | GA            |
| **Planes y extensiones de Azure Defender**                                                                                                                   |                |                                |               |
| - [Azure Defender para servidores](./defender-for-servers-introduction.md)                                                                                    | GA             | GA                             | GA            |
| - [Azure Defender para App Service](./defender-for-app-service-introduction.md)                                                                            | GA             | No disponible                  | No disponible |
| - [Azure Defender para DNS](./defender-for-dns-introduction.md)                                                                                            | GA             | GA                             | GA            |
| - [Azure Defender para registros de contenedor](./defender-for-container-registries-introduction.md) <sup>[1](#footnote1)</sup>                               | Disponibilidad general             | GA  <sup>[2](#footnote2)</sup> | GA  <sup>[2](#footnote2)</sup> |
| - [Azure Defender para registros de contenedor que examinan imágenes en flujos de trabajo de CI/CD](./defender-for-container-registries-cicd.md) <sup>[3](#footnote3)</sup> | Vista previa pública | No disponible                  | No disponible |
| - [Azure Defender para Kubernetes](./defender-for-kubernetes-introduction.md) <sup>[4](#footnote4)</sup>                                                   | GA             | GA                             | GA            |
| - [Extensión de Azure Defender para clústeres de Kubernetes habilitados para Azure Arc](./defender-for-kubernetes-azure-arc.md) <sup>[5](#footnote5)</sup>                 | Vista previa pública | No disponible                  | No disponible |
| - [Azure Defender para servidores de Azure SQL Database](./defender-for-sql-introduction.md)                                                                     | GA             | GA                             | GA  <sup>[9](#footnote9)</sup> |
| - [Azure Defender para servidores SQL en máquinas](./defender-for-sql-introduction.md)                                                                        | GA             | GA                             | No disponible |
| - [Azure Defender para bases de datos relacionales de código abierto](./defender-for-databases-introduction.md)                                                         | GA             | No disponible                  | No disponible |
| - [Azure Defender para Key Vault](./defender-for-key-vault-introduction.md)                                                                                | GA             | No disponible                  | No disponible |
| - [Azure Defender para Resource Manager](./defender-for-resource-manager-introduction.md)                                                                  | GA             | GA                             | GA            |
| - [Azure Defender para Storage](./defender-for-storage-introduction.md) <sup>[6](#footnote6)</sup>                                                         | GA             | GA                             | No disponible |
| - [Protección contra amenazas para Cosmos DB](./other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                          | Vista previa pública | No disponible                  | No disponible |
| - [Protección de cargas de trabajo de Kubernetes](./kubernetes-workload-protections.md)                                                                                  | GA             | GA                             | GA            |
| - [Sincronización de alertas bidireccional con Sentinel](../sentinel/connect-azure-security-center.md)                                                      | Vista previa pública | No disponible                  | No disponible |
| **Azure Defender para características de servidores** <sup>[7](#footnote7)</sup>                                                                                        |                |                                |               |
| - [Acceso de máquina virtual Just-In-Time](./security-center-just-in-time.md)                                                                                             | GA             | GA                             | GA            |
| - [Supervisión de la integridad de los archivos](./security-center-file-integrity-monitoring.md)                                                                             | GA             | GA                             | GA            |
| - [Controles de aplicaciones adaptables](./security-center-adaptive-application.md)                                                                              | GA             | GA                             | GA            |
| - [Protección de red adaptable](./security-center-adaptive-network-hardening.md)                                                                           | GA             | No disponible                  | No disponible |
| - [Protección de hosts de Docker](./harden-docker-hosts.md)                                                                                                       | GA             | GA                             | GA            |
| - [Evaluación integrada de vulnerabilidades para las máquinas](./deploy-vulnerability-assessment-vm.md)                                                             | GA             | No disponible                  | No disponible |
| - [Panel e informes de cumplimiento normativo](./security-center-compliance-dashboard.md) <sup>[8](#footnote8)</sup>                                       | GA             | GA                             | GA            |
| - [Implementación de Microsoft Defender para punto de conexión y licencia integrada](./security-center-wdatp.md)                                                         | GA             | GA                             | No disponible |
| - [Conexión de cuentas de AWS](./quickstart-onboard-aws.md)                                                                                                      | GA             | No disponible                  | No disponible |
| - [Conexión de cuentas de GCP](./quickstart-onboard-gcp.md)                                                                                                      | GA             | No disponible                  | No disponible |
|                                                                                                                                                           |                |                                |               |

<sup> <a name="footnote1" /> </a> 1</sup> Parcialmente en disponibilidad general: la capacidad de deshabilitar los resultados específicos de los exámenes de vulnerabilidades está en versión preliminar pública.

<sup><a name="footnote2" /></a>2</sup> Los exámenes de vulnerabilidades de los registros de contenedor en Azure Government Cloud solo se pueden realizar con la característica de examen en inserción.

<sup><a name="footnote3" /></a>3</sup> Requiere Azure Defender para registros de contenedor.

<sup><a name="footnote4" /></a>4</sup> Parcialmente en disponibilidad general: la compatibilidad con clústeres habilitados para Arc está en versión preliminar pública y no está disponible en Azure Government.

<sup><a name="footnote5" /></a>5</sup> Requiere Azure Defender para Kubernetes.

<sup><a name="footnote6" /></a> 6</sup> Parcialmente en disponibilidad general: algunas de las alertas de protección contra amenazas de Azure Defender para Storage están en versión preliminar pública.

<sup><a name="footnote7" /></a>7</sup> Todas estas características requieren [Azure Defender para servidores](./defender-for-servers-introduction.md).

<sup><a name="footnote8" /></a>8</sup> Puede haber diferencias en los estándares ofrecidos por cada tipo de nube.
 
<sup><a name="footnote9" /></a>9</sup> Parcialmente en disponibilidad general: subconjunto de alertas y evaluación de vulnerabilidades en servidores SQL. No están disponibles las protecciones contra amenazas de comportamiento.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [Security Center recopila datos mediante el agente de Log Analytics](security-center-enable-data-collection.md).
- Obtenga información sobre cómo [Security Center administra y protege los datos](security-center-data-security.md).
- Revise las [plataformas compatibles con Security Center](security-center-os-coverage.md).