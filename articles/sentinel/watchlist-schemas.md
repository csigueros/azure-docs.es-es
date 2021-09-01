---
title: Esquemas de plantillas de listas de reproducción de Azure Sentinel | Microsoft Docs
description: Obtenga información sobre los esquemas que se usan en cada plantilla de lista de reproducción integrada en Azure Sentinel.
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: reference
ms.custom: mvc
ms.date: 08/04/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: e263c742ae31a665eb428ad2cbeca72f73562b8a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780153"
---
# <a name="azure-sentinel-built-in-watchlist-template-schemas-public-preview"></a>Esquemas de plantillas de listas de reproducción integradas en Azure Sentinel (versión preliminar pública)

En este artículo se especifican los esquemas que se usan en cada plantilla de lista de reproducción integrada proporcionada por Azure Sentinel. Para más información, consulte [Creación de una lista de reproducción mediante una plantilla (versión preliminar pública)](watchlists.md#create-a-new-watchlist-using-a-template-public-preview).

> [!IMPORTANT]
> Las plantillas de listas de reproducción de Azure Sentinel están actualmente en versión preliminar. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>


## <a name="high-value-assets"></a>Recursos de alto valor

La lista de reproducción Recursos de alto valor enumera los dispositivos, recursos y otros recursos que tienen un valor crítico en la organización, e incluye los siguientes campos:

| Nombre del campo | Formato                              | Ejemplo                                                                                                                                | Obligatorio/Opcional |
| ---------- | ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------------------ |
| **Tipo de recurso** | String                              | `Device`, `Azure resource`, `AWS resource`, `URL`, `SPO`, `File share`, `Other`                                                                      | Mandatory          |
| **Id. de recurso**   | Cadena, depende del tipo de recurso | `/subscriptions/d1d8779d-38d7-4f06-91db-9cbc8de0176f/resourceGroups/SOC-Purview/providers/Microsoft.Storage/storageAccounts/purviewadls` | Mandatory          |
| **Nombre de recurso** | String                              | `Microsoft.Storage/storageAccounts/purviewadls`                                                                                          | Opcional           |
| **Nombre de dominio completo de recurso** | FQDN                                | `Finance-SRv.local.microsoft.com`                                                                                                        | Mandatory          |
| **Dirección IP** | IP                                  | `1.1.1.1`                                                                                                                                | Opcional           |
| **Etiquetas**       | List                                | `["SAW user","Blue Ocean team"] `                                                                                                        | Opcional           |
| | | | |

## <a name="vip-users"></a>Usuarios de VIP

La lista de reproducción Usuarios VIP enumera las cuentas de usuario de los empleados que tienen un valor de alto impacto en la organización e incluye los siguientes valores:

| Nombre de campo          | Formato | Ejemplo                                             | Obligatorio/Opcional |
| ------------------- | ------ | --------------------------------------------------- | ------------------ |
| **Identificador de usuario**     | UID    | `52322ec8-6ebf-11eb-9439-0242ac130002`                | Opcional           |
| **Identificador de objeto AAD de usuario**  | SID    | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | Opcional           |
| **Sid local de usuario**    | SID    | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | Opcional           |
| **Nombre principal del usuario** | UPN    | `JeffL@seccxp.ninja`                                  | Mandatory          |
| **Etiquetas**                | List   | `["SAW user","Blue Ocean team"]`                      | Opcional           |
| | | | |

## <a name="network-mapping"></a>Asignación de red

La lista de control Asignación de red enumera las subredes IP y sus respectivos contextos organizativos, e incluye los siguientes campos:

| Nombre del campo | Formato       | Ejemplo                      | Obligatorio/Opcional |
| ---------- | ------------ | ---------------------------- | ------------------ |
| **Subred IP**  | Rango de subred |` 198.51.100.0/24 - 198….../22` | Mandatory          |
| **Nombre de rango** | String       | `DMZ`                          | Opcional           |
| **Etiquetas**       | List         | `["Example","Example"]`        | Opcional           |
| | | | |

## <a name="terminated-employees"></a>Empleados terminados

En la lista de lista Empleados terminados se enumeran las cuentas de usuario de los empleados que se han terminado, o están a punto de terminarse, e incluye los campos siguientes:

| Nombre del campo          | Formato                                                                          | Ejemplo                              | Obligatorio/Opcional |
| ------------------- | ------------------------------------------------------------------------------- | ------------------------------------ | ------------------ |
| **Identificador de usuario**     | UID                                                                             | `52322ec8-6ebf-11eb-9439-0242ac130002` | Opcional           |
| **Identificador de objeto AAD de usuario**  | SID                                                                             | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e` | Opcional           |
| **Sid local de usuario**    | SID                                                                             | `S-1-12-1-4141952679-1282074057-123`   | Opcional           |
| **Nombre principal del usuario** | UPN                                                                             | `JeffL@seccxp.ninja`                  | Mandatory          |
| **Estado de usuario**           | String <br><br>Se recomienda usar `Notified` o `Terminated` | `Terminated`                           | Mandatory          |
| **Fecha de notificación**  | Marca de tiempo: día                                                                 | `01.12.20`                             | Opcional           |
| **Fecha de terminación**    | Marca de tiempo: día                                                                 | `01.01.21`                            | Mandatory          |
| **Etiquetas**                | List                                                                            | `["SAW user","Amba Wolfs team"]`       | Opcional           |
| | | | |


## <a name="identity-correlation"></a>Correlación de identidad

La lista de reproducción Correlación de identidades enumera las cuentas de usuario relacionadas que pertenecen a la misma persona e incluye los siguientes campos:

| Nombre del campo                       | Formato  | Ejemplo                                             | Obligatorio/Opcional |
| -------------------------------- | ------- | --------------------------------------------------- | ------------------ |
| **Identificador de usuario**                  | UID     | `52322ec8-6ebf-11eb-9439-0242ac130002`                | Opcional           |
| **Identificador de objeto AAD de usuario**               | SID     | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | Opcional           |
| **Sid local de usuario**                 | SID     | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | Opcional           |
| **Nombre principal del usuario**              | UPN     | `JeffL@seccxp.ninja`                                  | Mandatory          |
| **Id. de empleado**                      | String  | `8234123`                                             | Opcional           |
| **Correo electrónico**                            | Correo electrónico   | `JeffL@seccxp.ninja`                                  | Opcional           |
| **Id. de cuenta con privilegios asociada** | UID/SID | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | Opcional           |
| **Cuenta con privilegios asociada**    | UPN     | `Admin@seccxp.ninja`                                  | Opcional           |
| **Etiquetas**                             | List    | `["SAW user","Amba Wolfs team"]`                      | Opcional           |
| | | | |

## <a name="service-accounts"></a>Cuentas de servicio

La lista de reproducción Cuentas de servicio enumera las cuentas de servicio y sus propietarios, e incluye los siguientes campos:

| Nombre del campo                | Formato | Ejemplo                                             | Obligatorio/Opcional |
| ------------------------- | ------ | --------------------------------------------------- | ------------------ |
| **Identificador de servicio**        | UID    | `1111-112123-12312312-123123123`                      | Opcional           |
| **Id. de objeto AAD de servicio**     | SID    | `11123-123123-123123-123123`                          | Opcional           |
| **Sid local de servicio**       | SID    | `S-1-12-1-3123123-123213123-12312312-2916039507`      | Opcional           |
| **Nombre de entidad de seguridad de servicio**    | UPN    | `myserviceprin@contoso.com`                           | Mandatory          |
| **Identificador de usuario propietario**     | UID    | `52322ec8-6ebf-11eb-9439-0242ac130002`                | Opcional           |
| **Identificador de objeto AAD de usuario propietario**  | SID    | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | Opcional           |
| **Sid local de usuario propietario**    | SID    | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | Opcional           |
| **Nombre principal de usuario propietario** | UPN    | `JeffL@seccxp.ninja`                                  | Mandatory          |
| **Etiquetas**                      | List   | `["Automation Account","GitHub Account"]`             | Opcional           |
| | | | |

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte [Uso de las listas de reproducción de Azure Sentinel](watchlists.md).
