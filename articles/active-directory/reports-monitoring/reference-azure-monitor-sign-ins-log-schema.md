---
title: Esquema de registros de inicio de sesión en Azure Monitor | Microsoft Docs
description: Se describe el esquema de registros de inicio de sesión de Azure AD para su uso en Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/26/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d0cc6d793c70f9466a09c9ad54e3588295da5ae
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969163"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Interpretación del esquema de registros de inicio de sesión de Azure AD en Azure Monitor

En este artículo se describe el esquema de registro de inicio de sesión de Azure Active Directory (Azure AD) en Azure Monitor. La mayoría de la información relacionada con los inicios de sesión se proporciona en el atributo *Properties* del objeto `records`.


```json
{
  "time": "2019-03-12T16:02:15.5522137Z",
  "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
  "operationName": "Sign-in activity",
  "operationVersion": "1.0",
  "category": "SignInLogs",
  "tenantId": "<TENANT ID>",
  "resultType": "50140",
  "resultSignature": "None",
  "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.",
  "durationMs": 0,
  "callerIpAddress": "<CALLER IP ADDRESS>",
  "correlationId": "a75a10bd-c126-486b-9742-c03110d36262",
  "identity": "Timothy Perkins",
  "Level": 4,
  "location": "US",
  "properties": 
       {
    "id": "0231f922-93fa-4005-bb11-b344eca03c01",
    "createdDateTime": "2019-03-12T16:02:15.5522137+00:00",
    "userDisplayName": "Timothy Perkins",
    "userPrincipalName": "<USER PRINCIPAL NAME>",
    "userId": "<USER ID>",
    "appId": "<APPLICATION ID>",
    "appDisplayName": "Azure Portal",
    "ipAddress": "<IP ADDRESS>",
    "status": {
      "errorCode": 50140,
      "failureReason": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
    },
    "clientAppUsed": "Browser",
    "userAgent": "<USER AGENT>",
    "deviceDetail":
   {
     "deviceId": "8bfcb982-6856-4402-924c-ada2486321cc",
     "operatingSystem": "Windows 10",
     "browser": "Chrome 72.0.3626"
     },
    "location":
    {
      "city": "Bellevue",
      "state": "Washington",
      "countryOrRegion": "US",
      "geoCoordinates": 
     {
        "latitude": 45,
        "longitude": 122
      }
    },
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262",
    "conditionalAccessStatus": "notApplied",
    "appliedConditionalAccessPolicies": [
      {
        "id": "ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
        "displayName": "HR app access policy",
        "enforcedGrantControls": [
          "Mfa"
        ],
        "enforcedSessionControls": [],
        "result": "notApplied",
        "conditionsSatisfied": 0,
        "conditionsNotSatisfied": 0
      },
      {
        "id": "b915a70b-2eee-47b6-85b6-ff4f4a66256d",
        "displayName": "MFA for all but global support access",
        "enforcedGrantControls": [],
        "enforcedSessionControls": [],
        "result": "notEnabled",
        "conditionsSatisfied": 0,
        "conditionsNotSatisfied": 0
      },
      {
        "id": "830f27fa-67a8-461f-8791-635b7225caf1",
        "displayName": "Header Based Application Control",
        "enforcedGrantControls": [
          "Mfa"
        ],
        "enforcedSessionControls": [],
        "result": "notApplied",
        "conditionsSatisfied": 0,
        "conditionsNotSatisfied": 0
      },
      {
        "id": "8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
        "displayName": "MFA for everyones",
        "enforcedGrantControls": [],
        "enforcedSessionControls": [],
        "result": "notEnabled",
        "conditionsSatisfied": 0,
        "conditionsNotSatisfied": 0
      },
      {
        "id": "52924e0f-798b-4afd-8c42-49055c7d6395",
        "displayName": "Device compliant",
        "enforcedGrantControls": [],
        "enforcedSessionControls": [],
        "result": "notEnabled",
        "conditionsSatisfied": 0,
        "conditionsNotSatisfied": 0
      }
    ],
    "originalRequestId": "f2f0a254-f831-43b9-bcb0-2646fb645c00",
    "isInteractive": true,
    "authenticationProcessingDetails": [
      {
        "key": "Login Hint Present",
        "value": "True"
      }
    ],
    "networkLocationDetails": [],
    "processingTimeInMilliseconds": 238,
    "riskDetail": "none",
    "riskLevelAggregated": "none",
    "riskLevelDuringSignIn": "none",
    "riskState": "none",
    "riskEventTypes": [],
    "riskEventTypes_v2": [],
    "resourceDisplayName": "Office 365 SharePoint Online",
    "resourceId": "00000003-0000-0ff1-ce00-000000000000",
    "resourceTenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "homeTenantId": "<USER HOME TENANT ID>",
    "tokenIssuerName": "",
    "tokenIssuerType": "AzureAD",
    "authenticationDetails": [
      {
        "authenticationStepDateTime": "2019-03-12T16:02:15.5522137+00:00",
        "authenticationMethod": "Previously satisfied",
        "succeeded": true,
        "authenticationStepResultDetail": "First factor requirement satisfied by claim in the token",
        "authenticationStepRequirement": "Primary authentication",
        "StatusSequence": 0,
        "RequestSequence": 0
      },
      {
        "authenticationStepDateTime": "2021-08-12T15:48:12.8677211+00:00",
        "authenticationMethod": "Previously satisfied",
        "succeeded": true,
        "authenticationStepResultDetail": "MFA requirement satisfied by claim in the token",
        "authenticationStepRequirement": "Multi-factor authentication"
      }
    ],
    "authenticationRequirementPolicies": [
      {
        "requirementProvider": "multiConditionalAccess",
        "detail": "Conditional Access"
      }
    ],
    "authenticationRequirement": "multiFactorAuthentication",
    "alternateSignInName": "<ALTERNATE SIGN IN>",
    "signInIdentifier": "<SIGN IN IDENTIFIER>",
    "servicePrincipalId": "",
    "userType": "Member",
    "flaggedForReview": false,
    "isTenantRestricted": false,
    "autonomousSystemNumber": 8000,
    "crossTenantAccessType": "none",
    "privateLinkDetails": {},
    "ssoExtensionVersion": ""
    }
}

```


## <a name="field-descriptions"></a>Descripciones de los campos

| Nombre del campo | Clave | Descripción |
| --- | --- | --- | 
| Time |  - | Fecha y hora en formato UTC. |
| ResourceId | - | Este valor no se ha asignado, y puede ignorar este campo de forma segura.  |
| OperationName | - | Para inicios de sesión, este valor es siempre *Actividad de inicio de sesión*. |
| OperationVersion | - | Versión de la API REST solicitada por el cliente. |
| Category | - | Para inicios de sesión, este valor es siempre *SignIn*. | 
| TenantId | - | GUID del inquilino asociado con los registros. |
| ResultType | - | El resultado de la operación de inicio de sesión puede ser `0` en caso de que sea correcto o un *código de error* en caso de error. | 
| ResultSignature | - | Este valor siempre es *Ninguno*. |
| ResultDescription | N/D o en blanco | Proporciona la descripción del error para la operación de inicio de sesión. |
| riskDetail | riskDetail | Proporciona la "razón" detrás de un estado específico de un usuario de riesgo, un inicio de sesión o una detección de riesgo. Los valores posibles son: `none`, `adminGeneratedTemporaryPassword`, `userPerformedSecuredPasswordChange`, `userPerformedSecuredPasswordReset`, `adminConfirmedSigninSafe`, `aiConfirmedSigninSafe`, `userPassedMFADrivenByRiskBasedPolicy`, `adminDismissedAllRiskForUser`, `adminConfirmedSigninCompromised`, `unknownFutureValue`. El valor `none` significa que no se ha realizado hasta el momento ninguna acción sobre el usuario o el inicio de sesión. <br>**Nota:** Los detalles de esta propiedad requieren una licencia de Azure AD Premium P2. Otras licencias devuelven el valor `hidden`. |
| riskEventTypes | riskEventTypes | Tipos de detecciones de riesgo asociadas con el inicio de sesión. Los valores posibles son: `unlikelyTravel`, `anonymizedIPAddress`, `maliciousIPAddress`, `unfamiliarFeatures`, `malwareInfectedIPAddress`, `suspiciousIPAddress`, `leakedCredentials`, `investigationsThreatIntelligence`,  `generic` y `unknownFutureValue`. |
| authProcessingDetails | Biblioteca de autenticación de la aplicación de Azure AD | Contiene información de la familia, la biblioteca y la plataforma en el formato "Familia: biblioteca de ADAL: plataforma de ADAL.JS 1.0.0: JS". |
| authProcessingDetails | IsCAEToken | Los valores son "True" o "False". |
| riskLevelAggregated | riskLevel | Nivel de riesgo agregado. Los valores posibles son: `none`, `low`, `medium`, `high`, `hidden` y `unknownFutureValue`. El valor `hidden` significa que el usuario o el inicio de sesión no se habilitaron para Azure AD Identity Protection. **Nota:** Los detalles de esta propiedad solo están disponibles para los clientes de Azure AD Premium P2. Todos los demás clientes se devolverán como `hidden`. |
| riskLevelDuringSignIn | riskLevel | Nivel de riesgo durante el inicio de sesión. Los valores posibles son: `none`, `low`, `medium`, `high`, `hidden` y `unknownFutureValue`. El valor `hidden` significa que el usuario o el inicio de sesión no se habilitaron para Azure AD Identity Protection. **Nota:** Los detalles de esta propiedad solo están disponibles para los clientes de Azure AD Premium P2. Todos los demás clientes se devolverán como `hidden`. |
| riskState | riskState | Informa del estado del usuario de riesgo, el inicio de sesión o una detección de riesgo. Los valores posibles son: `none`, `confirmedSafe`, `remediated`, `dismissed`, `atRisk`, `confirmedCompromised`, `unknownFutureValue`. |
| DurationMs | - | Este valor no se ha asignado, y puede ignorar este campo de forma segura. |
| CallerIpAddress | - | Dirección IP del cliente que realizó la solicitud. | 
| CorrelationId | - | GUID opcional que el cliente pasa. Este valor puede ayudar a poner en correlación las operaciones de cliente con las operaciones de servidor y es útil al realizar el seguimiento de los registros que se distribuyen entre servicios. |
| Identidad | - | Identidad del token que se ha presentado al realizar la solicitud. Puede ser una cuenta de usuario, una cuenta del sistema o una entidad de servicio. |
| Nivel | - | Proporciona el tipo de mensaje. Para la auditoría, siempre es *informativo*. |
| Location | - | Indica la ubicación de la actividad de inicio de sesión. |
| Propiedades | - | Enumera todas las propiedades asociadas con los inicios de sesión.|



## <a name="next-steps"></a>Pasos siguientes

* [Interpretación del esquema de registros de auditoría en Azure Monitor](./overview-reports.md)
* [Más información sobre los registros de plataforma de Azure](../../azure-monitor/essentials/platform-logs-overview.md)