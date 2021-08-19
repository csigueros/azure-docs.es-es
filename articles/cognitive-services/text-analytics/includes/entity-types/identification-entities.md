---
title: Entidades de identificación
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 08/11/2021
ms.author: aahi
ms.openlocfilehash: 35af9d16ff5c3e0fdfc4fe2c62abfd232439520e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734080"
---
### <a name="financial-account-identification"></a>Identificación de cuenta financiera

Esta categoría de entidad incluye información financiera y formas oficiales de identificación.

#### <a name="category-aba-routing-number"></a>Categoría: número de ruta de ABA

Este categoría contiene la entidad siguiente:

:::row:::
    :::column span="":::
        **Entidad**

        Número de ruta de ABA

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Números de enrutamiento de tránsito de American Banker Association (ABA). También se devuelve con `domain=phi`.

        Para obtener esta categoría de entidad, agregue `ABARoutingNumber` al parámetro `piiCategories`. `ABARoutingNumber` también se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento admitidos**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="category-swift-code"></a>Categoría: código SWIFT

Este categoría contiene la entidad siguiente:

:::row:::
    :::column span="":::
        **Entidad**

        Código SWIFT

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Códigos SWIFT de información de instrucciones de pago. También se devuelve con `domain=phi`.

        Para obtener esta categoría de entidad, agregue `SWIFTCode` al parámetro `piiCategories`. `SWIFTCode` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento admitidos**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>Categoría: tarjeta de crédito

Este categoría contiene la entidad siguiente:

:::row:::
    :::column span="":::
        **Entidad**

        Tarjeta de crédito

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Números de tarjeta de crédito. También se devuelve con `domain=phi`.

        Para obtener esta categoría de entidad, agregue `CreditCardNumber` al parámetro `piiCategories`. `CreditCardNumber` se devolverá en la respuesta de la API si se detecta.

    :::column-end:::
    :::column span="2":::
      **Idiomas de documento admitidos**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>Categoría: número de cuenta bancaria internacional (IBAN) 

Este categoría contiene la entidad siguiente:

:::row:::
    :::column span="":::
        **Entidad**

        Tarjeta de crédito

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Códigos IBAN de información de instrucciones de pago. También se devuelve con `domain=phi`.

        Para obtener esta categoría de entidad, agregue `InternationalBankingAccountNumber` al parámetro `piiCategories`. `InternationalBankingAccountNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento admitidos**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>Identificación específica del país o región y del gobierno

> [!NOTE]
> Las entidades financieras y específicas del país siguientes no se devuelven con el parámetro `domain=phi`:
> * Números de pasaporte
> * Id. de impuesto

Las entidades siguientes se agrupan y enumeran por país:

#### <a name="argentina"></a>Argentina

:::row:::
    :::column span="":::
        **Entidad**

        Número de identidad nacional de Argentina (DNI) 

    :::column-end:::
    :::column span="2":::
        **Detalles** También se devuelve con `domain=phi`.
        
        Para obtener esta categoría de entidad, agregue `ARNationalIdentityNumber` al parámetro `piiCategories`. `ARNationalIdentityNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


#### <a name="austria"></a>Austria

:::row:::
    :::column span="":::
        **Entidad**

        Tarjeta de identidad de Austria

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `ATIdentityCard` al parámetro `piiCategories`. `ATIdentityCard` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación fiscal de Austria

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `ATTaxIdentificationNumber` al parámetro `piiCategories`. `ATTaxIdentificationNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación fiscal (NIF) de Austria

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `ATValueAddedTaxNumber` al parámetro `piiCategories`. `ATValueAddedTaxNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



#### <a name="australia"></a>Australia

:::row:::
    :::column span="":::
        **Entidad**

        Número de cuenta bancaria de Australia

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `AUDriversLicenseNumber` al parámetro `piiCategories`. `AUDriversLicenseNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de empresa de Australia

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `AUBusinessNumber` al parámetro `piiCategories`. `AUBusinessNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de sociedad de Australia

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `AUCompanyNumber` al parámetro `piiCategories`. `AUCompanyNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Permiso de conducir de Australia  

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `AUDriversLicense` al parámetro `piiCategories`. `AUDriversLicense` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de cuenta del seguro médico de Australia

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `AUMedicalAccountNumber` al parámetro `piiCategories`. `AUMedicalAccountNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de pasaporte de Australia

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `ATPassportNumber` al parámetro `piiCategories`. `ATPassportNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de expediente fiscal de Australia

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `ATTaxIdentificationNumber` al parámetro `piiCategories`. `ATTaxIdentificationNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="belgium"></a>Bélgica

:::row:::
    :::column span="":::
        **Entidad**

        Número nacional de Bélgica

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `BENationalNumber` al parámetro `piiCategories`. `BENationalNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación fiscal (NIF) de Bélgica

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `BEValueAddedTaxNumber` al parámetro `piiCategories`. `BEValueAddedTaxNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


#### <a name="brazil"></a>Brasil 

:::row:::
    :::column span="":::
        **Entidad**

        Número de entidad jurídica de Brasil (CNPJ)

        

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `BRLegalEntityNumber` al parámetro `piiCategories`. `BRLegalEntityNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de CPF de Brasil

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `BRCPFNumber` al parámetro `piiCategories`. `BRCPFNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Tarjeta de identificación nacional de Brasil (RG)

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `BRNationalIDRG` al parámetro `piiCategories`. `BRNationalIDRG` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="canada"></a>Canadá

:::row:::
    :::column span="":::
        **Entidad**

        Número de cuenta bancaria de Canadá

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `CABankAccountNumber` al parámetro `piiCategories`. `CABankAccountNumber` se devolverá en la respuesta de la API si se detecta.
    
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de permiso de conducir de Canadá

    :::column-end:::

    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `CADriversLicenseNumber` al parámetro `piiCategories`. `CADriversLicenseNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número del servicio de salud de Canadá

        
    :::column-end:::

    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `CAHealthServiceNumber` al parámetro `piiCategories`. `CAHealthServiceNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de pasaporte de Canadá

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `CAPassportNumber` al parámetro `piiCategories`. `CAPassportNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación sanitaria personal de Canadá (PHIN)

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `CAPersonalHealthIdentification` al parámetro `piiCategories`. `CAPersonalHealthIdentification` se devolverá en la respuesta de la API si se detecta.

        También se devuelve con `domain=phi`.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de la seguridad social de Canadá

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `CASocialInsuranceNumber` al parámetro `piiCategories`. `CASocialInsuranceNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chile 

:::row:::
    :::column span="":::
        **Entidad**

        Número del carné de identidad de Chile

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `CLIdentityCardNumber` al parámetro `piiCategories`. `CLIdentityCardNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `es`
      
   :::column-end:::
:::row-end:::

#### <a name="china"></a>China

:::row:::
    :::column span="":::
        **Entidad**

        Número del carné de identidad de residente de China (PRC)

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `CNResidentIdentityCardNumber` al parámetro `piiCategories`. `CNResidentIdentityCardNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Unión Europea (UE)

:::row:::
    :::column span="":::
        **Entidad**

        Número de tarjeta de débito de la UE

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `EUDebitCardNumber` al parámetro `piiCategories`. `EUDebitCardNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de permiso de conducir de la UE

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `EUDriversLicenseNumber` al parámetro `piiCategories`. `EUDriversLicenseNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Coordenadas GPU de la UE

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `EUGPSCoordinates` al parámetro `piiCategories`. `EUGPSCoordinates` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación nacional de la UE

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `EUNationalIdentificationNumber` al parámetro `piiCategories`. `EUNationalIdentificationNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de pasaporte de la UE

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `EUPassportNumber` al parámetro `piiCategories`. `EUPassportNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número del seguro social (SSN) o identificador equivalente de la UE

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `EUSocialSecurityNumber` al parámetro `piiCategories`. `EUSocialSecurityNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación fiscal de la UE (TIN)

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `EUTaxIdentificationNumber` al parámetro `piiCategories`. `EUTaxIdentificationNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

#### <a name="france"></a>Francia

:::row:::
    :::column span="":::
        **Entidad**

        Número de permiso de conducir de Francia

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `FRDriversLicenseNumber` al parámetro `piiCategories`. `FRDriversLicenseNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de seguro médico de Francia

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `FRHealthInsuranceNumber` al parámetro `piiCategories`. `FRHealthInsuranceNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Carné de identidad nacional de Francia (CNI)

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `FRNationalID` al parámetro `piiCategories`. `FRNationalID` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de pasaporte de Francia

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `FRPassportNumber` al parámetro `piiCategories`. `FRPassportNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número del seguro social de Francia (INSEE)

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `FRSocialSecurityNumber` al parámetro `piiCategories`. `FRSocialSecurityNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación fiscal de Francia (número SPI)

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `FRTaxIdentificationNumber` al parámetro `piiCategories`. `FRTaxIdentificationNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación fiscal (NIF) de Francia

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `FRValueAddedTaxNumber` al parámetro `piiCategories`. `FRValueAddedTaxNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

#### <a name="germany"></a>Alemania

:::row:::
    :::column span="":::
        **Entidad**

        Número de permiso de conducir de Alemania

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `DEDriversLicenseNumber` al parámetro `piiCategories`. `DEDriversLicenseNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número del carné de identidad de Alemania

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `DEIdentityCardNumber` al parámetro `piiCategories`. `DEIdentityCardNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de pasaporte de Alemania

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `DEPassportNumber` al parámetro `piiCategories`. `DEPassportNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación fiscal de Alemania

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `DETaxIdentificationNumber` al parámetro `piiCategories`. `DETaxIdentificationNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación fiscal de Alemania

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `DEValueAddedNumber` al parámetro `piiCategories`. `DEValueAddedNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>RAE de Hong Kong

:::row:::
    :::column span="":::
        **Entidad**

        Número del carné de identidad de Hong Kong (HKID)

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `HKIdentityCardNumber` al parámetro `piiCategories`. `HKIdentityCardNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Hungría

:::row:::
    :::column span="":::
        **Entidad**

        Número de identificación personal de Hungría

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `HUPersonalIdentificationNumber` al parámetro `piiCategories`. `HUPersonalIdentificationNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación fiscal de Hungría

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `HUTaxIdentificationNumber` al parámetro `piiCategories`. `HUTaxIdentificationNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación fiscal de Hungría

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `HUValueAddedNumber` al parámetro `piiCategories`. `HUValueAddedNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="india"></a>India

:::row:::
    :::column span="":::
        **Entidad**

        Número de cuenta permanente de India (PAN)

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `INPermanentAccount` al parámetro `piiCategories`. `INPermanentAccount` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación única de India (Aadhaar)

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `INUniqueIdentificationNumber` al parámetro `piiCategories`. `INUniqueIdentificationNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonesia

:::row:::
    :::column span="":::
        **Entidad**

        Número del carné de identidad de Indonesia (KTP)

    :::column-end:::
    :::column span="2":::

        **Detalles**

        Para obtener esta categoría de entidad, agregue `IDIdentityCardNumber` al parámetro `piiCategories`. `IDIdentityCardNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Irlanda

:::row:::
    :::column span="":::
        **Entidad**

        Número de servicio público personal de Irlanda (PPS)

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `IEPersonalPublicServiceNumber` al parámetro `piiCategories`. `IEPersonalPublicServiceNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        Número de servicio público personal de Irlanda (PPS) versión 2

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `IEPersonalPublicServiceNumberV2` al parámetro `piiCategories`. `IEPersonalPublicServiceNumberV2` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="israel"></a>Israel

:::row:::
    :::column span="":::
        **Entidad**

        Identificación nacional

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `ILNationalID` al parámetro `piiCategories`. `ILNationalID` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de cuenta bancaria de Israel

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `ILBankAccountNumber` al parámetro `piiCategories`. `ILBankAccountNumber` se devolverá en la respuesta de la API si se detecta.
    
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="italy"></a>Italia

:::row:::
    :::column span="":::
        **Entidad**

        Id. del permiso de conducir de Italia

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `ITDriversLicenseNumber` al parámetro `piiCategories`. `ITDriversLicenseNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Código fiscal de Italia

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `ITFiscalCode` al parámetro `piiCategories`. `ITFiscalCode` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación fiscal de Italia

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `ITValueAddedTaxNumber` al parámetro `piiCategories`. `ITValueAddedTaxNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japón

:::row:::
    :::column span="":::
        **Entidad**

        Número de cuenta bancaria de Japón

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `JPBankAccountNumber` al parámetro `piiCategories`. `JPBankAccountNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de permiso de conducir de Japón

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `JPDriversLicenseNumber` al parámetro `piiCategories`. `JPDriversLicenseNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Mi número personal de Japón

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `JPMyNumberPersonal` al parámetro `piiCategories`. `JPMyNumberPersonal` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Mi número corporativo de Japón

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `JPMyNumberCorporate` al parámetro `piiCategories`. `JPMyNumberCorporate` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de registro de residente de Japón

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `ITValueAddedTaxNumber` al parámetro `piiCategories`. `ITValueAddedTaxNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de la tarjeta de residencia de Japón

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `JPResidenceCardNumber` al parámetro `piiCategories`. `JPResidenceCardNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número del seguro social (SIN) de Japón

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `JPSocialInsuranceNumber` al parámetro `piiCategories`. `JPSocialInsuranceNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de pasaporte de Japón

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `JPPassportNumber` al parámetro `piiCategories`. `JPPassportNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Luxemburgo

:::row:::
    :::column span="":::
        **Entidad**

        Número de identificación nacional de Luxemburgo (personas físicas)

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `LUNationalIdentificationNumberNatural` al parámetro `piiCategories`. `LUNationalIdentificationNumberNatural` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación nacional de Luxemburgo (personas jurídicas)

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `LUNationalIdentificationNumberNonNatural` al parámetro `piiCategories`. `LUNationalIdentificationNumberNonNatural` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Entidad**

        Número del carné de identidad de Malta

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `MTIdentityCardNumber` al parámetro `piiCategories`. `MTIdentityCardNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación fiscal de Malta

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `MTTaxIDNumber` al parámetro `piiCategories`. `MTTaxIDNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nueva Zelanda

:::row:::
    :::column span="":::
        **Entidad**

        Número de cuenta bancaria de Nueva Zelanda

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `NZBankAccountNumber` al parámetro `piiCategories`. `NZBankAccountNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número del permiso de conducir de Nueva Zelanda

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `NZDriversLicenseNumber` al parámetro `piiCategories`. `NZDriversLicenseNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de Hacienda Pública de Nueva Zelanda

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `NZInlandRevenueNumber` al parámetro `piiCategories`. `NZInlandRevenueNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número del Ministerio de Sanidad de Nueva Zelanda

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `NZMinistryOfHealthNumber` al parámetro `piiCategories`. `NZMinistryOfHealthNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número de bienestar social de Nueva Zelanda

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `NZSocialWelfareNumber` al parámetro `piiCategories`. `NZSocialWelfareNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filipinas

:::row:::
    :::column span="":::
        **Entidad**

        Número de identificación multiuso unificado de Filipinas

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `PHUnifiedMultiPurposeIDNumber` al parámetro `piiCategories`. `PHUnifiedMultiPurposeIDNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugal 

:::row:::
    :::column span="":::
        **Entidad**

        Número de la tarjeta de ciudadano de Portugal

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `PTCitizenCardNumber` al parámetro `piiCategories`. `PTCitizenCardNumber` se devolverá en la respuesta de la API si se detecta.
          
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número de identificación fiscal de Portugal

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `PTTaxIdentificationNumber` al parámetro `piiCategories`. `PTTaxIdentificationNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapur

:::row:::
    :::column span="":::
        **Entidad**

        Número de la tarjeta de identificación del registro nacional de Singapur (NRIC)

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `PTTaxIdentificationNumber` al parámetro `piiCategories`. `PTTaxIdentificationNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Sudáfrica

:::row:::
    :::column span="":::
        **Entidad**

        Número de identificación de Sudáfrica

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `ZAIdentificationNumber` al parámetro `piiCategories`. `ZAIdentificationNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Corea del Sur

:::row:::
    :::column span="":::
        **Entidad**

        Número de registro de residente de Corea del Sur

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `KRResidentRegistrationNumber` al parámetro `piiCategories`. `KRResidentRegistrationNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="spain"></a>España

:::row:::
    :::column span="":::
        **Entidad**

        DNI de España

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `ESDNI` al parámetro `piiCategories`. `ESDNI` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de seguridad social de España (SSN)

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `ESSocialSecurityNumber` al parámetro `piiCategories`. `ESSocialSecurityNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificación fiscal de España

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `ESTaxIdentificationNumber` al parámetro `piiCategories`. `ESTaxIdentificationNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Suiza

:::row:::
    :::column span="":::
        **Entidad**

        Número del seguro social de Suiza (AHV)

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `CHSocialSecurityNumber` al parámetro `piiCategories`. `CHSocialSecurityNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Taiwán 

:::row:::
    :::column span="":::
        **Entidad**

        Identificación nacional de Ta

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `TWNationalID` al parámetro `piiCategories`. `TWNationalID` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Certificado de residente de Taiwán (ARC/TARC)

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `TWResidentCertificate` al parámetro `piiCategories`. `TWResidentCertificate` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de pasaporte de Taiwán

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `TWPassportNumber` al parámetro `piiCategories`. `TWPassportNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Reino Unido

:::row:::
    :::column span="":::
        **Entidad**

        Reino Unido Número de permiso de conducir

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `UKDriversLicenseNumber` al parámetro `piiCategories`. `UKDriversLicenseNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido Número de lista electoral

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `UKNationalInsuranceNumber` al parámetro `piiCategories`. `UKNationalInsuranceNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido Número del servicio de salud nacional (NHS)

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `UKNationalHealthNumber` al parámetro `piiCategories`. `UKNationalHealthNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido Número del seguro social (NINO)

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `UKNationalInsuranceNumber` al parámetro `piiCategories`. `UKNationalInsuranceNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido o Estados Unidos Número de pasaporte

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `USUKPassportNumber` al parámetro `piiCategories`. `USUKPassportNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Reino Unido Número único de referencia del contribuyente

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `UKUniqueTaxpayerNumber` al parámetro `piiCategories`. `UKUniqueTaxpayerNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="united-states"></a>Estados Unidos

:::row:::
    :::column span="":::
        **Entidad**

        EE. UU. Número de seguridad social

    :::column-end:::
    :::column span="2":::
        **Detalles**

        Para obtener esta categoría de entidad, agregue `USSocialSecurityNumber` al parámetro `piiCategories`. `USSocialSecurityNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::
      **Idiomas de documento admitidos**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       EE. UU. Número de permiso de conducir

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `USDriversLicenseNumber` al parámetro `piiCategories`. `USDriversLicenseNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Estados Unidos o Reino Unido Número de pasaporte

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `USUKPassportNumber` al parámetro `piiCategories`. `USUKPassportNumber` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       EE. UU. Número de identificación fiscal (NIF) individual

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `USIndividualTaxpayerIdentification` al parámetro `piiCategories`. `USIndividualTaxpayerIdentification` se devolverá en la respuesta de la API si se detecta.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       EE. UU. Número de la agencia antidroga de Estados Unidos (DEA)

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `DrugEnforcementAgencyNumber` al parámetro `piiCategories`. `DrugEnforcementAgencyNumber` se devolverá en la respuesta de la API si se detecta.
      
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       EE. UU. Número de cuenta bancaria

    :::column-end:::
    :::column span="2":::

        Para obtener esta categoría de entidad, agregue `USBankAccountNumber` al parámetro `piiCategories`. `USBankAccountNumber` se devolverá en la respuesta de la API si se detecta.
        
        También se devuelve con `domain=phi`.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
