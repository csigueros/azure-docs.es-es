---
title: Consultas de ejemplo para análisis mediante programación
description: Use estas consultas de ejemplo para acceder mediante programación a los datos de análisis del marketplace comercial de Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 8/06/2021
ms.openlocfilehash: ac276f495ac2a5eb3bee6ac0f682185cf8424611
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748233"
---
# <a name="sample-queries-for-programmatic-analytics"></a>Consultas de ejemplo para análisis mediante programación

En este artículo se proporcionan consultas de ejemplo para los pedidos, el uso y los informes de clientes del marketplace comercial de Microsoft. Puede usar estas consultas llamando al punto de conexión de la API de [Creación de consultas de informe](analytics-programmatic-access.md#create-report-query-api). Si es necesario, se puede modificar la llamada de la API de [Creación de consultas de informes](analytics-programmatic-access.md#create-report-query-api) para agregar más columnas, ajustar el período de cálculo y agregar condiciones de filtro. Los períodos de tiempo admitidos son 6 meses, 12 meses y un período de tiempo personalizado.

Para más información sobre los nombres de columna, los atributos y las descripciones, consulte las tablas siguientes:

- [Tabla de detalles de clientes](customer-dashboard.md#customer-details-table)
- [Tabla Detalles del pedido](orders-dashboard.md#orders-details-table)
- [Tabla de detalles de uso](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>Consultas del informe de clientes

Estas consultas de ejemplo se aplican al informe de clientes.

| **Descripción de la consulta** | **Consulta de ejemplo** |
| --- | --- |
| Clientes activos del asociado hasta la fecha que elija | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| Clientes perdidos del asociado hasta la fecha que elija | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| Lista de clientes nuevos de una geografía específica en los últimos seis meses | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>Consultas del informe de uso

Estas consultas de ejemplo se aplican al informe de uso.

| **Descripción de la consulta** | **Consulta de ejemplo** |
| --- | --- |
| Uso normalizado de máquinas virtuales (VM) para el tipo de licencia de marketplace "Facturación a través de Azure" durante los últimos 6 meses | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Uso sin procesar de máquinas virtuales para el tipo de licencia de marketplace "Facturación a través de Azure" durante los últimos 12 meses | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| Uso normalizado de máquinas virtuales para el tipo de licencia de marketplace "Traiga su propia licencia" durante los últimos 6 meses | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Uso sin procesar de máquinas virtuales para el tipo de licencia de marketplace "Traiga su propia licencia" durante los últimos 6 meses | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| En función de la fecha de uso, el uso normalizado diario total y los "cargos totales estimados (PC/CC)" para los planes de pago durante el último mes | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| En función de la fecha de uso, el uso sin procesar diario total y los "cargos totales estimados (PC/CC)" para los planes de pago durante el último mes | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| Para un nombre de oferta específico, el uso normalizado de máquinas virtuales para el tipo de licencia de marketplace "Facturación a través de Azure" durante los últimos 6 meses | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Para un nombre de oferta específico, uso medido durante los últimos 6 meses | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>Consultas del informe de pedidos

Estas consultas de ejemplo se aplican al informe de pedidos.

| **Descripción de la consulta** | **Consulta de ejemplo** |
| --- | --- |
| Informe de pedidos para el tipo de licencia de Azure "Enterprise" durante los últimos 6 meses | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| Informe de pedidos para el tipo de licencia de Azure "Pago por uso" durante los últimos 6 meses | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| Informe de pedidos para un nombre de oferta específico durante los últimos 6 meses | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Informe de pedidos activos durante los últimos 6 meses | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| Informe de pedidos cancelados durante los últimos 6 meses | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
| Informe de pedidos con inicio de período, fecha de finalización del plazo y cargos estimados, moneda | `SELECT OrderId, TermStartId, TermEndId, estimatedcharges from ISVOrderV2 WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| Informe de pedidos de prueba activos durante los últimos 6 meses | `SELECT OrderId from ISVOrderV2 WHERE OrderStatus = ‘Active’ and HasTrial = ‘True’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>Pasos siguientes

- [API para acceder a datos de análisis del marketplace comercial](analytics-available-apis.md)
