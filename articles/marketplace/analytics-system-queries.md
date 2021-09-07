---
title: Lista de consultas del sistema
description: Obtenga información sobre las consultas del sistema que puede usar para obtener datos de análisis mediante programación sobre sus ofertas en el marketplace comercial de Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/06/2021
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.openlocfilehash: 04d56f39cc4721d0290bb258595fb95b6440cf79
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748218"
---
# <a name="list-of-system-queries"></a>Lista de consultas del sistema

Las siguientes consultas del sistema se pueden usar en la [API de creación de informes](analytics-programmatic-access.md#create-report-api) directamente con un `QueryId`. Las consultas del sistema son como los informes de exportación del Centro de partners durante un período de cálculo de seis meses.

Para obtener más información sobre los nombres de columna, atributos y descripción, consulte estos artículos:

- [Panel Clientes de los análisis de marketplace comercial](customer-dashboard.md#customer-details-table)
- [Panel Pedidos de los análisis de marketplace comercial](orders-dashboard.md#orders-details-table)
- [Panel de uso de los análisis de marketplace comercial](usage-dashboard.md#usage-details-table)
- [Panel Información de marketplace de los análisis de marketplace comercial](insights-dashboard.md#marketplace-insights-details-table)

En las secciones siguientes se proporcionan consultas para varios informes.

## <a name="customers-report-query"></a>Consulta del informe de clientes

**Descripción del informe**: el informe de clientes durante los últimos 6 meses

**QueryID**: `b9df4929-073f-4795-b0cb-a2c81b11e28d`

**Consulta del informe**:

`SELECT MarketplaceSubscriptionId,DateAcquired,DateLost,ProviderName,ProviderEmail,FirstName,LastName,Email,CustomerCompanyName,CustomerCity,CustomerPostalCode,CustomerCommunicationCulture,CustomerCountryRegion,AzureLicenseType,PromotionalCustomers,CustomerState,CommerceRootCustomer,CustomerId,BillingAccountId,ID  FROM ISVCustomer TIMESPAN LAST_6_MONTHS`

## <a name="orders-report-query"></a>Consulta del informe de pedidos

**Descripción del informe**: el informe de pedidos durante los últimos 6 meses

**QueryID**: `fd0f299c-5a1c-4929-9f48-bfc6cc44355d`

**Consulta del informe**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId FROM ISVOrder TIMESPAN LAST_6_MONTHS`

**Descripción del informe**: informe de OrdersV2 para los últimos 6 meses

**QueryID**: `bd1b0cc1-ce45-4578-beba-6fe5a69fd421`

**Consulta del informe**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId,TermStartDate,TermEndDate,PurchaseRecordId,PurchaseRecordLineItemId,HasTrial,IsTrialDeployment,estimatedcharges FROM ISVOrderV2 TIMESPAN LAST_6_MONTHS`

## <a name="usage-report-queries"></a>Consultas del informe de uso

**Descripción del informe**: informe del uso normalizado de máquinas virtuales durante los últimos 6 meses

**QueryID**: `2c6f384b-ad52-4aed-965f-32bfa09b3778`

**Consulta del informe**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,NormalizedUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Descripción del informe**: informe del uso sin procesar de máquinas virtuales durante los últimos 6 meses

**QueryID**: `3f19fb95-5bc4-4ee0-872e-cedd22578512`

**Consulta del informe**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,RawUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Descripción del informe**: informe del uso medido de máquinas virtuales durante los últimos 6 meses

**QueryID**: `f0c4927f-1f23-4c99-be4a-1371a5a9a086`

**Consulta del informe**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,MeteredUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('SaaS', 'Azure Applications') TIMESPAN LAST_6_MONTHS`

## <a name="marketplace-insights-report-query"></a>Consulta del informe de detalles del marketplace

**Descripción del informe**: informe de detalles del marketplace durante los últimos 6 meses

**QueryID**: `6fd7624b-aa9f-42df-a61d-67d42fd00e92`

**Consulta del informe**:

`Date,OfferName,ReferralDomain,CountryName,PageVisits,GetItNow,ContactMe,TestDrive,FreeTrial FROM ISVMarketplaceInsights TIMESPAN LAST_6_MONTHS`

## <a name="next-steps"></a>Pasos siguientes

- [API para acceder a datos de análisis del marketplace comercial](analytics-available-apis.md)
- [Aplicación de ejemplo para acceder a datos de análisis del marketplace comercial](analytics-sample-application.md)
