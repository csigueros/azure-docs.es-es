---
title: Requisitos detallados de SAP para la solución Azure Sentinel para SAP | Microsoft Docs
description: Obtenga información sobre los requisitos detallados del sistema SAP de la solución Azure Sentinel para SAP.
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: reference
ms.custom: mvc
ms.date: 06/09/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: a03031640bd116e2901ff123b5fec13d36151fd4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723428"
---
# <a name="azure-sentinel-sap-solution-detailed-sap-requirements-public-preview"></a>Requisitos detallados de SAP para la solución Azure Sentinel para SAP (versión preliminar pública)

El [procedimiento predeterminado para implementar la solución Azure Sentinel para SAP](sap-deploy-solution.md) incluye las solicitudes de cambios de SAP y las notas de SAP necesarias, y proporciona un rol integrado con todos los permisos necesarios.

En este artículo se enumeran detalladamente las solicitudes de cambios, las notas y los permisos de SAP necesarios.

Use este artículo como referencia si es administrador o si va a [implementar la solución SAP manualmente](sap-solution-deploy-alternate.md). Este artículo está destinado a usuarios de SAP avanzados.


> [!IMPORTANT]
> La solución Azure Sentinel para SAP está actualmente en VERSIÓN PRELIMINAR. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="recommended-virtual-machine-sizing"></a>Tamaño recomendado de máquina virtual

En la tabla siguiente se describe el tamaño recomendado para la máquina virtual, en función del uso previsto:

|Uso  |Tamaño recomendado  |
|---------|---------|
|**Especificación mínima**, como para un entorno de laboratorio     |   Una máquina virtual *Standard_B2s*      |
|**Conector estándar** (valor predeterminado)     |   Una máquina virtual *DS2_v2*, con: <br>- 2 núcleos<br>- 8 GB de memoria      |
|**Varios conectores**     |Una máquina virtual *Standard_B4ms*, con: <br>- 4 núcleos<br>- 16 GB de memoria         |
|     |         |

## <a name="required-sap-log-change-requests"></a>Solicitudes de cambios de registro de SAP obligatorias

Las siguientes solicitudes de cambios de registro de SAP son obligatorias para la solución SAP, en función de la versión de SAP Basis:

- **SAP Basis, versiones 7.50 y posteriores**: instale NPLK900144.
- **Para versiones anteriores**: instale NPLK900146.
- **Para crear un rol de SAP con las autorizaciones necesarias**, para cualquier versión de SAP Basis compatible, instale NPLK900140. Para más información, consulte [Configuración del sistema SAP](sap-deploy-solution.md#configure-your-sap-system) y [Autorizaciones de ABAP necesarias](#required-abap-authorizations).

> [!NOTE]
> Las solicitudes de cambios de registro de SAP obligatorias exponen las FM de RFC personalizadas necesarias para el conector y no cambian ningún objeto estándar o personalizado.
>

## <a name="required-sap-notes"></a>Notas obligatorias de SAP

Si tiene una versión 7.50 o inferior de SAP Basis, instale las siguientes notas de SAP:

|Versiones de SAP BASIS  |Nota requerida |
|---------|---------|
|- 750 SP01 a SP12<br>- 751 SP01 a SP06<br>- 752 SP01 a SP03     |  2641084: Acceso de lectura estandarizado para los datos de registro de auditoría de seguridad       |
|- 700 a 702<br>- 710 a 711, 730, 731, 740 y 750     | 2173545: CD: CHANGEDOCUMENT_READ_ALL        |
|- 700 a 702<br>- 710 a 711, 730, 731 y 740<br>- 750 a 752     | 2502336: CD (documento de cambios): RSSCD100: solo lectura desde el archivo, no desde la base de datos        |
|     |         |

Acceda a las notas de SAP desde el [sitio de Launchpad de soporte técnico de SAP](https://support.sap.com/en/index.html).

## <a name="required-abap-authorizations"></a>Autorizaciones de ABAP necesarias

En la tabla siguiente se indican las autorizaciones de ABAP necesarias para que el usuario de SAP de back-end se conecte a Azure Sentinel para los registros de SAP. Para más información, consulte [Configuración del sistema SAP](sap-deploy-solution.md#configure-your-sap-system).

Las autorizaciones necesarias se enumeran por tipo de registro. Solo necesita las autorizaciones indicadas para los tipos de registros que planea ingerir en Azure Sentinel.

> [!TIP]
> Para crear el rol con todas las autorizaciones necesarias, implemente la solicitud de cambio de SAP [NPLK900114](#required-sap-log-change-requests) en el sistema SAP. Esta solicitud de cambio crea el rol **/MSFTSEN/SENTINEL_CONNECTOR** y asigna el rol al usuario de ABAP que se conecta a Azure Sentinel.
>

| Objeto de autorización | Campo | Valor |
| -------------------- | ----- | ----- |
| **Todos los registros RFC** | | |
| S_RFC | FUGR | /OSP/SYSTEM_TIMEZONE |
| S_RFC | FUGR | ARFC |
| S_RFC | FUGR | STFC |
| S_RFC | FUGR | RFC1 |
| S_RFC | FUGR | SDIFRUNTIME  |
| S_RFC | FUGR | SMOI |
| S_RFC | FUGR | SYST |
| S_RFC | FUGR/FUNC | SRFC/RFC_SYSTEM_INFO |
| S_RFC | FUGR/FUNC | THFB/TH_SERVER_LIST |
| S_TCODE | TCD | SM51 |
| **Registro de aplicaciones de ABAP**  | | |
| S_APPL_LOG | ACTVT | Mostrar |
| S_APPL_LOG | ALG_OBJECT | * |
| S_APPL_LOG | ALG_SUBOBJ | * |
| S_RFC | FUGR | SXBP_EXT |
| S_RFC | FUGR | /MSFTSEN/_APPLOG |
| **Registro de documentos de cambio de ABAP** | | |
| S_RFC | FUGR | /MSFTSEN/_CHANGE_DOCS |
| **Registro de CR de ABAP** | | |
| S_RFC | FUGR | CTS_API |
| S_RFC | FUGR | /MSFTSEN/_CR |
| S_TRANSPRT | ACTVT | Mostrar |
| S_TRANSPRT | TTYPE | * |
| **Registro de datos de tabla de base de datos de ABAP** | | |
| S_RFC | FUGR | /MSFTSEN/_TD |
| S_TABU_DIS | ACTVT | Mostrar |
| S_TABU_DIS | DICBERCLS | &NC& |
| S_TABU_DIS | DICBERCLS | + Cualquier objeto necesario para el registro |
| S_TABU_NAM | ACTVT | Mostrar |
| S_TABU_NAM | TABLE | + Cualquier objeto necesario para el registro |
| S_TABU_NAM | TABLE | DBTABLOG |
| **Registro de trabajos de ABAP** | | |
| S_RFC | FUGR | SXBP |
| S_RFC | FUGR | /MSFTSEN/_JOBLOG |
| **Registro de trabajos de ABAP, registro de aplicaciones de ABAP** | | |
| S_XMI_PRD | INTERFACE | XBP |
| **Registro de auditoría de seguridad de ABAP: XAL** | | |
| All RFC | S_RFC | FUGR |
| S_ADMI_FCD | S_ADMI_FCD | AUDD |
| S_RFC | FUGR | SALX |
| S_USER_GRP | ACTVT | Mostrar |
| S_USER_GRP | CLASS | * |
| S_XMI_PRD | INTERFACE | XAL |
| **Registro de auditoría de seguridad de ABAP: XAL, registro de trabajos de ABAP, registro de aplicaciones de ABAP** | | |
| S_RFC | FUGR | SXMI |
| S_XMI_PRD | EXTCOMPANY | Microsoft |
| S_XMI_PRD | EXTPRODUCT | Azure Sentinel |
| **Registro de auditoría de seguridad de ABAP: SAL** | | |
| S_RFC | FUGR | RSAU_LOG |
| S_RFC | FUGR | /MSFTSEN/_AUDITLOG |
| **Registro de cola de ABAP, registro de salida de cola de ABAP** | | |
| S_RFC | FUGR | /MSFTSEN/_SPOOL |
| **Registro de flujo de trabajo de ABAP** | | |
| S_RFC | FUGR | SWRR |
| S_RFC | FUGR | /MSFTSEN/_WF |
| | |

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Implementación de la solución Azure Sentinel para SAP](sap-deploy-solution.md)
- [Opciones de configuración de expertos, implementación local y orígenes de registro de SAPControl](sap-solution-deploy-alternate.md)
- [Referencia sobre los registros de la solución Azure Sentinel para SAP](sap-solution-log-reference.md)
- [Azure Sentinel para SAP: contenido de seguridad disponible](sap-solution-security-content.md)
- [Solución de problemas de implementación de la solución SAP de Azure Sentinel](sap-deploy-troubleshoot.md)
