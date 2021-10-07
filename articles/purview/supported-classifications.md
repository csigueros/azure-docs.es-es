---
title: Lista de clasificaciones admitidas
description: En esta página se enumeran las clasificaciones del sistema admitidas en Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: reference
ms.date: 09/27/2021
ms.openlocfilehash: edd43fc34a1b94c3d389670c7417ea9123d1586f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214738"
---
# <a name="supported-classifications-in-azure-purview"></a>Clasificaciones admitidas en Azure Purview

En este artículo se enumeran las clasificaciones del sistema admitidas y definidas en Azure Purview.


- **Umbral de datos único**: es el número total de valores de datos únicos que deben encontrarse en una columna antes de que el escáner ejecute el patrón de datos en ella. Un umbral de datos único no tiene nada que ver con la coincidencia de patrones, pero es un requisito previo de esta. Nuestras reglas de clasificación del sistema requieren que haya al menos 8 valores distintos en cada columna para someterlos a clasificación. Asimismo, el sistema necesita este valor para asegurarse de que la columna contiene suficientes datos para que el escáner pueda clasificarla con precisión. Por ejemplo, una columna que contenga varias filas con el valor 1 no se clasificará. Tampoco se clasificarán las columnas que contengan una fila con un valor y el resto de las filas con valores NULL. Recuerde que si especifica varios patrones, este valor se aplica a cada uno de ellos.

- **Umbral de coincidencia mínimo**: es el porcentaje mínimo de coincidencias de valores de datos que debe encontrar el escáner en una columna para que se aplique la clasificación. El valor de clasificación del sistema se establece en 60 %.


## <a name="defined-system-classifications"></a>Clasificaciones del sistema definidas

Azure Purview clasifica los datos por [expresiones regulares](https://wikipedia.org/wiki/Regular_expression) y [filtro de Bloom](https://wikipedia.org/wiki/Bloom_filter). En las siguientes listas se describen el formato, el patrón y las palabras clave de las clasificaciones del sistema que define Azure Purview.

Cada nombre de clasificación tiene el prefijo MICROSOFT.

## <a name="bloom-filter-classifications"></a>Clasificaciones del filtro de Bloom

## <a name="city-country-and-place"></a>Ciudad, País o región y Ubicación

Los filtros de Ciudad, País o región y Ubicación se han creado con los mejores conjuntos de datos disponibles para preparar los datos.

## <a name="person"></a>Person

El filtro de Bloom de tipo Person se ha preparado con los dos conjuntos de datos siguientes.

- [Datos del censo de 2010de EE. UU. para los apellidos (entradas 162-K)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [Nombres de bebé populares (de SSN), teniendo en cuenta todos los años de 1880 a 2019 (entradas 98-K)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>Clasificaciones RegEx

## <a name="aba-routing-number"></a>Número de ruta de ABA

### <a name="format"></a>Formato

Nueve dígitos que pueden estar en un patrón con formato o sin formato

### <a name="pattern"></a>Patrón

- dos dígitos en los intervalos 00-12, 21-32, 61-72 u 80
- dos dígitos
- un guión opcional
- cuatro dígitos
- un guión opcional
- un dígito


### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_aba_routing"></a>Keyword_aba_routing

- aba number
- aba#
- aba
- abarouting#
- abaroutingnumber
- americanbankassociationrouting#
- americanbankassociationroutingnumber
- bankrouting #
- bankroutingnumber
- routing #
- routing no
- routing number
- routing transit number
- routing#
- RTN

## <a name="argentina-national-identity-dni-number"></a>Número de identidad nacional de Argentina (DNI)

### <a name="format"></a>Formato

Ocho dígitos con o sin puntos.

### <a name="pattern"></a>Patrón

Ocho dígitos:
- dos dígitos
- un punto opcional
- tres dígitos
- un punto opcional
- tres dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_argentina_national_id"></a>Keyword_argentina_national_id

- Argentina National Identity number
- cedula
- cédula
- dni
- documento nacional de identidad
- documento número
- documento numero
- registro nacional de las personas
- rnp

## <a name="australia-bank-account-number"></a>Número de cuenta bancaria de Australia

### <a name="format"></a>Formato

De seis a 10 dígitos con o sin un número de sucursal bancaria

### <a name="pattern"></a>Patrón

El número de cuenta tiene entre 6 y 10 dígitos.

Número de sucursal bancaria de Australia:
- tres dígitos
- un guion
- tres dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_australia_bank_account_number"></a>Keyword_australia_bank_account_number

- swift bank code
- correspondent bank
- moneda base
- usa account
- holder address
- bank address
- information account
- fund transfers
- bank charges
- bank details
- banking information
- full names
- iaea

## <a name="australia-business-number"></a>Número de negocio de Australia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security


### <a name="format"></a>Formato

11 dígitos más delimitadores opcionales

### <a name="pattern"></a>Patrón

11 dígitos más delimitadores opcionales:

- dos dígitos
- un guion o espacio opcional
- tres dígitos
- un guion o espacio opcional
- tres dígitos
- un guion o espacio opcional
- tres dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_australia_business_number"></a>Keyword_australia_business_number

- australia business no
- business number
- abn#
- businessid#
- business id
- abn
- businessno#

## <a name="australia-company-number"></a>Número de sociedad de Australia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

nueve dígitos con delimitadores

### <a name="pattern"></a>Patrón

nueve dígitos con delimitadores:

- tres dígitos
- un espacio
- tres dígitos
- un espacio
- tres dígitos


### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_australia_company_number"></a>Keyword_australia_company_number

- acn
- australia company no
- australia company no#
- australia company number
- australian company no
- australian company no#
- australian company number

## <a name="australia-drivers-license-number"></a>Número de permiso de conducir de Australia

### <a name="format"></a>Formato

nueve letras y dígitos

### <a name="pattern"></a>Patrón

nueve letras y dígitos:

- dos dígitos o letras (sin distinguir mayúsculas de minúsculas)
- dos dígitos
- cinco dígitos o letras (sin distinguir mayúsculas de minúsculas)

O BIEN

- una o dos letras opcionales (sin distinguir mayúsculas de minúsculas)
- de cuatro a nueve dígitos

O BIEN

- nueve dígitos o letras (sin distinguir mayúsculas de minúsculas)

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_australia_drivers_license_number"></a>Keyword_australia_drivers_license_number

- international driving permits
- australian automobile association
- international driving permit
- DriverLicence
- DriverLicences
- Driver Lic
- Driver Licence
- Driver Licences
- DriversLic
- DriversLicence
- DriversLicences
- Drivers Lic
- Drivers Lics
- Drivers Licence
- Drivers Licences
- Driver'Lic
- Driver'Lics
- Driver'Licence
- Driver'Licences
- Driver' Lic
- Driver' Lics
- Driver' Licence
- Driver' Licences
- Driver'sLic
- Driver'sLics
- Driver'sLicence
- Driver'sLicences
- Driver's Lic
- Driver's Lics
- Driver's Licence
- Driver's Licences
- DriverLic#
- DriverLics#
- DriverLicence#
- DriverLicences#
- Driver Lic#
- Driver Lics#
- Driver Licence#
- Driver Licences#
- DriversLic#
- DriversLics#
- DriversLicence#
- DriversLicences#
- Drivers Lic#
- Drivers Lics#
- Drivers Licence#
- Drivers Licences#
- Driver'Lic#
- Driver'Lics#
- Driver'Licence#
- Driver'Licences#
- Driver' Lic#
- Driver' Lics#
- Driver' Licence#
- Driver' Licences#
- Driver'sLic#
- Driver'sLics#
- Driver'sLicence#
- Driver'sLicences#
- Driver's Lic#
- Driver's Lics#
- Driver's Licence#
- Driver's Licences#

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword_australia_drivers_license_number_exclusions

- aaa
- DriverLicense
- DriverLicenses
- Driver License
- Driver Licenses
- DriversLicense
- DriversLicenses
- Drivers License
- Drivers Licenses
- Driver'License
- Driver'Licenses
- Driver' License
- Driver' Licenses
- Driver'sLicense
- Driver'sLicenses
- Driver's License
- EE. UU.
- DriverLicense#
- DriverLicenses#
- Driver License#
- Driver Licenses#
- DriversLicense#
- DriversLicenses#
- Drivers License#
- Drivers Licenses#
- Driver'License#
- Driver'Licenses#
- Driver' License#
- Driver' Licenses#
- Driver'sLicense#
- Driver'sLicenses#
- Driver's License#
- Driver's Licenses#

## <a name="australia-medical-account-number"></a>Número de cuenta del seguro médico de Australia

### <a name="format"></a>Formato

10-11 dígitos

### <a name="pattern"></a>Patrón

10-11 dígitos:
- El primer dígito está en el intervalo 2-6.
- El noveno dígito es un dígito de control.
- El décimo dígito es el dígito de emisión.
- El undécimo dígito (opcional) es el número individual.

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_australia_medical_account_number"></a>Keyword_Australia_Medical_Account_Number

- bank account details
- medicare payments
- mortgage account
- bank payments
- information branch
- credit card loan
- department of human services
- local service
- medicare


## <a name="australia-passport-number"></a>Número de pasaporte de Australia

### <a name="format"></a>Formato

Ocho o nueve caracteres alfanuméricos

### <a name="pattern"></a>Patrón

- Una letra (N, E, D, F, A, C, U, X) seguida de siete dígitos o
- Dos letras (PA, PB, PC, PD, PE, PF, PU, PW, PX, PZ) seguidas de siete dígitos.

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_australia_passport_number"></a>Keyword_australia_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers
- passport details
- immigration and citizenship
- commonwealth of australia
- department of immigration
- national identity card
- travel document
- issuing authority


## <a name="australia-tax-file-number"></a>Número de expediente fiscal de Australia

### <a name="format"></a>Formato

de ocho a nueve dígitos

### <a name="pattern"></a>Patrón

entre ocho y nueve dígitos que normalmente se presentan con espacios tal como se indica a continuación:
- tres dígitos
- un espacio opcional
- tres dígitos
- un espacio opcional
- de dos a tres dígitos en los que el último dígito es un dígito de control

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_australia_tax_file_number"></a>Keyword_australia_tax_file_number

- australian business number
- tipo imposindo marginal
- medicare levy
- portfolio number
- service veterans
- withholding tax
- individual tax return
- tax file number
- tfn

## <a name="austria-drivers-license-number"></a>Número de permiso de conducir de Austria

### <a name="format"></a>Formato

ocho dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

ocho dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_austria_eu_drivers_license_number"></a>Keywords_austria_eu_driver's_license_number

- fuhrerschein
- führerschein
- Führerscheine
- Führerscheinnummer
- Führerscheinnummern

## <a name="austria-identity-card"></a>Tarjeta de identidad de Austria
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Combinación de 24 caracteres de letras, dígitos y caracteres especiales

### <a name="pattern"></a>Patrón

24 caracteres:

-  22 letras (sin distinguir mayúsculas de minúsculas), dígitos, barras diagonales inversas, barras diagonales o signos más

- dos letras (sin distinguir mayúsculas de minúsculas), dígitos, barras diagonales inversas, barras diagonales, signos más o signos igual

### <a name="checksum"></a>Suma de comprobación

No aplicable

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_austria_eu_national_id_card"></a>Keywords_austria_eu_national_id_card

- identity number
- national id
- personalausweis republik österreich

## <a name="austria-passport-number"></a>Número de pasaporte de Australia

### <a name="format"></a>Formato

Una letra seguida de un espacio opcional y siete dígitos

### <a name="pattern"></a>Patrón

Combinación de una letra, siete dígitos y un espacio:

- una letra (sin distinguir mayúsculas de minúsculas)
- un espacio (opcional)
- siete dígitos

### <a name="checksum"></a>Suma de comprobación

no aplicable

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_austria_eu_passport_number"></a>Keywords_austria_eu_passport_number

- reisepassnummer
- reisepasse
- No-Reisepass
- Nr-Reisepass
- Reisepass-Nr
- Passnummer
- reisepässe

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry

## <a name="austria-social-security-number"></a>Número del seguro social de Alemania

### <a name="format"></a>Formato

10 dígitos en el formato especificado

### <a name="pattern"></a>Patrón

10 dígitos:

- tres dígitos que corresponden a un número de serie
- un dígito de comprobación
- seis dígitos que corresponden a la fecha de nacimiento (DDMMAA)

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_austria_eu_ssn_or_equivalent"></a>Keywords_austria_eu_ssn_or_equivalent

- austrian ssn
- ehic number
- ehic no
- insurance code
- insurancecode#
- insurance number
- insurance no
- krankenkassennummer
- krankenversicherung
- socialsecurityno
- socialsecurityno#
- social security no
- social security number
- social security code
- sozialversicherungsnummer
- sozialversicherungsnummer#
- soziale sicherheit kein
- sozialesicherheitkein#
- ssn#
- ssn
- versicherungscode
- versicherungsnummer
- zdravstveno zavarovanje

## <a name="austria-tax-identification-number"></a>Número de identificación fiscal de Austria

### <a name="format"></a>Formato

nueve dígitos con guion opcional y barra diagonal

### <a name="pattern"></a>Patrón

nueve dígitos con guion opcional y barra diagonal:

- dos dígitos
- un guion (opcional)
- tres dígitos
- una barra diagonal (opcional)
- cuatro dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_austria_eu_tax_file_number"></a>Keywords_austria_eu_tax_file_number

- österreich
- st.nr.
- steuernummer
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#
- tax number

## <a name="austria-value-added-tax"></a>Impuesto sobre el valor añadido de Austria
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Patrón alfanumérico de 11 caracteres

### <a name="pattern"></a>Patrón

Patrón alfanumérico de 11 caracteres:

- A o a
- T o t
- Espacio opcional
- U o u
- espacio opcional
- dos o tres dígitos
- espacio opcional
- cuatro dígitos
- espacio opcional
- uno o dos dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_austria_value_added_tax"></a>Keyword_austria_value_added_tax

- vat number
- vat#
- austrian vat number
- vat no.
- vatno#
- value added tax number
- austrian vat
- mwst
- umsatzsteuernummer
- mwstnummer
- ust.-identifikationsnummer
- umsatzsteuer-identifikationsnummer
- vat identification number
- atu number
- uid number

## <a name="belgium-drivers-license-number"></a>Número de permiso de conducir de Alemania

### <a name="format"></a>Formato

10 dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

10 dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number

#### <a name="keywords_belgium_eu_drivers_license_number"></a>Keywords_belgium_eu_driver's_license_number

- rijbewijs
- rijbewijsnummer
- führerschein
- führerscheinnummer
- füehrerscheinnummer
- fuhrerschein
- fuehrerschein
- fuhrerscheinnummer
- fuehrerscheinnummer
- permis de conduire
- numéro permis conduire


## <a name="belgium-national-number"></a>Número nacional de Bélgica

### <a name="format"></a>Formato

11 dígitos más delimitadores opcionales

### <a name="pattern"></a>Patrón

11 dígitos más delimitadores:
- seis dígitos y dos puntos opcionales con el formato AA.MM.DD para la fecha de nacimiento
- Un delimitador opcional de punto, guion o espacio
- tres dígitos secuenciales (impares para masculino, pares para femenino)
- Un delimitador opcional de punto, guion o espacio
- dos dígitos de control

### <a name="checksum"></a>Suma de comprobación

Yes


### <a name="keywords"></a>Palabras clave

#### <a name="keyword_belgium_national_number"></a>Keyword_belgium_national_number

- belasting aantal
- bnn#
- bnn
- carte d’identité
- identifiant national
- identifiantnational#
- identificatie
- identificación
- identifikation
- identifikationsnummer
- identifizierung
- identité
- identiteit
- identiteitskaart
- identidad
- inscription
- national number
- national register
- nationalnumber#
- nationalnumber
- nif#
- nif
- numéro d'assuré
- numéro de registre national
- numéro de sécurité
- numéro d'identification
- numéro d'immatriculation
- numéro national
- numéronational#
- personal id number
- personalausweis
- personalidnumber#
- registratie
- registro
- registrationsnumme
- registrierung
- social security number
- ssn#
- ssn
- steuernummer
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#

## <a name="belgium-passport-number"></a>Número de pasaporte de Bélgica

### <a name="format"></a>Formato

dos letras seguidas de seis dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

dos letras mayúsculas seguidas de seis dígitos

### <a name="checksum"></a>Suma de comprobación

no aplicable

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_belgium_eu_passport_number"></a>Keywords_belgium_eu_passport_number

- numéro passeport
- paspoort nr
- paspoort-nr
- paspoortnummer
- paspoortnummers
- Passeport carte
- Passeport livre
- Pass-Nr
- Passnummer
- reisepass kein

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry

## <a name="belgium-value-added-tax-number"></a>Número de identificación fiscal de Bélgica
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Patrón alfanumérico de 12 caracteres

### <a name="pattern"></a>Patrón

Patrón alfanumérico de 12 caracteres:

- una letra B o b
- una letra E o e
- un dígito 0
- un dígito de 1 a 9
- un punto opcional, guion o espacio
- cuatro dígitos
- un punto opcional, guion o espacio
- cuatro dígitos


### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_belgium_value_added_tax_number"></a>Keyword_belgium_value_added_tax_number

- nº tva
- vat number
- vat no
- numéro t.v.a
- umsatzsteuer-identifikationsnummer
- umsatzsteuernummer
- btw
- btw#
- vat#


## <a name="brazil-cpf-number"></a>Número de CPF de Brasil

### <a name="format"></a>Formato

11 dígitos que incluyen un dígito de control y pueden tener formato o no.

### <a name="pattern"></a>Patrón

Con formato:
- tres dígitos
- un punto
- tres dígitos
- un punto
- tres dígitos
- un guion
- dos dígitos, que son dígitos de control

Sin formato:
- 11 dígitos en los que los dos últimos dígitos son dígitos de control.

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_brazil_cpf"></a>Keyword_brazil_cpf

- CPF
- Identificación
- Registro
- Ingresos
- Cadastro de Pessoas Físicas
- Imposto
- Identificação
- Inscrição
- Receita


## <a name="brazil-legal-entity-number-cnpj"></a>Número de entidad jurídica de Brasil (CNPJ)

### <a name="format"></a>Formato

14 dígitos que incluyen un número de registro, un número de sucursal y los dígitos de control, más los delimitadores.

### <a name="pattern"></a>Patrón

14 dígitos más delimitadores:

- dos dígitos
- un punto
- tres dígitos
- un punto
- tres dígitos (estos primeros ocho dígitos son el número de registro)
- una barra diagonal
- número de sucursal de cuatro dígitos
- un guion
- dos dígitos, que son dígitos de control

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_brazil_cnpj"></a>Keyword_brazil_cnpj

- CNPJ
- CNPJ/MF
- CNPJ-MF
- National Registry of Legal Entities
- Taxpayers Registry
- Legal entity
- Legal entities
- Estado de registro
- Business
- Compañía
- CNPJ
- Cadastro Nacional da Pessoa Jurídica
- Cadastro Geral de Contribuintes
- CGC
- Pessoa jurídica
- Pessoas jurídicas
- Situação cadastral
- Inscrição
- Empresa


## <a name="brazil-national-identification-card-rg"></a>Número de identificación nacional de Brasil (RG)

### <a name="format"></a>Formato

Registro Geral (formato antiguo): nueve dígitos

Registro de Identidade (RIC) (nuevo formato): 11 dígitos

### <a name="pattern"></a>Patrón

Registro Geral (formato antiguo):
- dos dígitos
- un punto
- tres dígitos
- un punto
- tres dígitos
- un guion
- un dígito, que es un dígito de control

Registro de Identidade (RIC) (nuevo formato):
- 10 dígitos
- un guion
- un dígito, que es un dígito de control

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_brazil_rg"></a>Keyword_brazil_rg

- Cédula de identidade
- identity card
- national id
- número de rregistro
- registro de Iidentidade
- registro geral
- RG (esta palabra clave distingue mayúsculas de minúsculas)
- RIC (esta palabra clave distingue mayúsculas de minúsculas)


## <a name="bulgaria-drivers-license-number"></a>Número de permiso de conducir de Bulgaria

### <a name="format"></a>Formato

nueve dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

nueve dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_bulgaria_eu_drivers_license_number"></a>Keywords_bulgaria_eu_driver's_license_number

- свидетелство за управление на мпс
- свидетелство за управление на моторно превозно средство
- сумпс
- шофьорска книжка
- шофьорски книжки

## <a name="bulgaria-uniform-civil-number"></a>Número civil uniforme de Bulgaria
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

10 dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

10 dígitos sin espacios ni delimitadores

- seis dígitos que corresponden a la fecha de nacimiento (AAMMDD)
- dos dígitos que corresponden al orden de nacimiento
- un dígito que corresponde al género: un dígito par para el varón y un dígito impar para la mujer
- un dígito de comprobación

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_bulgaria_eu_national_id_card"></a>Keywords_bulgaria_eu_national_id_card

- bnn#
- bnn
- bucn#
- bucn
- edinen grazhdanski nomer
- egn#
- egn
- identification number
- national id
- national number
- nationalnumber#
- nationalnumber
- personal id
- personal no
- personal number
- personalidnumber#
- social security number
- ssn#
- ssn
- uniform civil id
- uniform civil no
- uniform civil number
- uniformcivilno#
- uniformcivilno
- uniformcivilnumber#
- uniformcivilnumber
- unique citizenship number
- егн#
- егн
- единен граждански номер
- идентификационен номер
- личен номер
- лична идентификация
- лично не
- национален номер
- номер на гражданството
- униформ id
- униформ граждански id
- униформ граждански не
- униформ граждански номер
- униформгражданскиid#
- униформгражданскине.#


## <a name="bulgaria-passport-number"></a>Número de pasaporte de Bulgaria

### <a name="format"></a>Formato

nueve dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

nueve dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_bulgaria_eu_passport_number"></a>Keywords_bulgaria_eu_passport_number

- номер на паспорта
- номер на паспорт
- паспорт №

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry

## <a name="canada-bank-account-number"></a>Número de cuenta bancaria de Canadá

### <a name="format"></a>Formato

7 o 12 dígitos

### <a name="pattern"></a>Patrón

Un número de cuenta bancaria de Canadá tiene entre 7 o 12 dígitos.

Un número de tránsito de cuenta bancaria de Canadá tiene:
- cinco dígitos
- un guion
- tres dígitos O
- un cero "0"
- ocho dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_canada_bank_account_number"></a>Keyword_canada_bank_account_number

- canada savings bonds
- canada revenue agency
- canadian financial institution
- direct deposit form
- canadian citizen
- legal representative
- notary public
- commissioner for oaths
- child care benefit
- universal child care
- canada child tax benefit
- income tax benefit
- harmonized sales tax
- social insurance number
- income tax refund
- child tax benefit
- territorial payments
- institution number
- deposit request
- banking information
- direct deposit


## <a name="canada-drivers-license-number"></a>Número de permiso de conducir de Canadá

### <a name="format"></a>Formato

Varía según la provincia.

### <a name="pattern"></a>Patrón

Varios patrones que abarcan:
- Alberta
- Columbia Británica
- Manitoba
- Nuevo Brunswick
- Terranova y Labrador
- Nueva Escocia
- Ontario
- Isla del Príncipe Eduardo
- Quebec
- Saskatchewan

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_province_name_drivers_license_name"></a>Keyword_[nombre_provincia]_drivers_license_name

- La abreviatura de la provincia; por ejemplo, AB.
- El nombre de la provincia, por ejemplo, Alberta.

#### <a name="keyword_canada_drivers_license"></a>Keyword_canada_drivers_license

- DL
- DLS
- CDL
- CDLS
- DriverLic
- DriverLics
- DriverLicense
- DriverLicenses
- DriverLicence
- DriverLicences
- Driver Lic
- Driver Lics
- Driver License
- Driver Licenses
- Driver Licence
- Driver Licences
- DriversLic
- DriversLics
- DriversLicence
- DriversLicences
- DriversLicense
- DriversLicenses
- Drivers Lic
- Drivers Lics
- Drivers License
- Drivers Licenses
- Drivers Licence
- Drivers Licences
- Driver'Lic
- Driver'Lics
- Driver'License
- Driver'Licenses
- Driver'Licence
- Driver'Licences
- Driver' Lic
- Driver' Lics
- Driver' License
- Driver' Licenses
- Driver' Licence
- Driver' Licences
- Driver'sLic
- Driver'sLics
- Driver'sLicense
- Driver'sLicenses
- Driver'sLicence
- Driver'sLicences
- Driver's Lic
- Driver's Lics
- Driver's License
- EE. UU.
- Driver's Licence
- Driver's Licences
- Permis de Conduire
- id
- ids
- idcard number
- idcard numbers
- idcard #
- idcard #s
- idcard card
- idcard cards
- idcard
- identification number
- números de identificación
- identification #
- identification #s
- identification card
- identification cards
- identificación
- DL#
- DLS#
- CDL#
- CDLS#
- DriverLic#
- DriverLics#
- DriverLicense#
- DriverLicenses#
- DriverLicence#
- DriverLicences#
- Driver Lic#
- Driver Lics#
- Driver License#
- Driver Licenses#
- Driver License#
- Driver Licences#
- DriversLic#
- DriversLics#
- DriversLicense#
- DriversLicenses#
- DriversLicence#
- DriversLicences#
- Drivers Lic#
- Drivers Lics#
- Drivers License#
- Drivers Licenses#
- Drivers Licence#
- Drivers Licences#
- Driver'Lic#
- Driver'Lics#
- Driver'License#
- Driver'Licenses#
- Driver'Licence#
- Driver'Licences#
- Driver' Lic#
- Driver' Lics#
- Driver' License#
- Driver' Licenses#
- Driver' Licence#
- Driver' Licences#
- Driver'sLic#
- Driver'sLics#
- Driver'sLicense#
- Driver'sLicenses#
- Driver'sLicence#
- Driver'sLicences#
- Driver's Lic#
- Driver's Lics#
- Driver's License#
- Driver's Licenses#
- Driver's Licence#
- Driver's Licences#
- Permis de Conduire#
- id#
- ids#
- idcard card#
- idcard cards#
- idcard#
- identification card#
- identification cards#
- identification#


## <a name="canada-health-service-number"></a>Número del servicio de salud de Canadá

### <a name="format"></a>Formato

 10 dígitos

### <a name="pattern"></a>Patrón

10 dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_canada_health_service_number"></a>Keyword_canada_health_service_number

- personal health number
- patient information
- health services
- speciality services
- automobile accident
- patient hospital
- psychiatrist
- workers compensation
- disability


## <a name="canada-passport-number"></a>Número de pasaporte de Canadá

### <a name="format"></a>Formato

dos letras mayúsculas seguidas de seis dígitos

### <a name="pattern"></a>Patrón

dos letras mayúsculas seguidas de seis dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_canada_passport_number"></a>Keyword_canada_passport_number

- canadian citizenship
- canadian passport
- passport application
- passport photos
- certified translator
- canadian citizens
- processing times
- renewal application

#### <a name="keyword_passport"></a>Keyword_passport

- Número de pasaporte
- Passport No
- Passport #
- Passport#
- PassportID
- Passportno
- passportnumber
- パスポート
- パスポート番号
- パスポートのNum
- パスポート＃
- Numéro de passeport
- Passeport n °
- Passeport Non
- Passeport #
- Passeport#
- PasseportNon
- Passeportn °


## <a name="canada-personal-health-identification-number-phin"></a>Número de identificación sanitaria personal de Canadá (PHIN)

### <a name="format"></a>Formato

nueve dígitos

### <a name="pattern"></a>Patrón

nueve dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_canada_phin"></a>Keyword_canada_phin

- social insurance number
- health information act
- income tax information
- manitoba health
- health registration
- prescription purchases
- benefit eligibility
- personal health
- power of attorney
- registration number
- personal health number
- practitioner referral
- wellness professional
- patient referral
- health and wellness

#### <a name="keyword_canada_provinces"></a>Keyword_canada_provinces

- Nunavut
- Quebec
- Territorios Noroccidentales
- Ontario
- Columbia Británica
- Alberta
- Saskatchewan
- Manitoba
- Yukón
- Terranova y Labrador
- Nuevo Brunswick
- Nueva Escocia
- Isla del Príncipe Eduardo
- Canadá


## <a name="canada-social-insurance-number"></a>Número de la seguridad social de Canadá

### <a name="format"></a>Formato

nueve dígitos con guiones o espacios opcionales

### <a name="pattern"></a>Patrón

Con formato:
- tres dígitos
- un guion o un espacio
- tres dígitos
- un guion o un espacio
- tres dígitos

Sin formato: nueve dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_sin"></a>Keyword_sin

- sin
- social insurance
- numero d'assurance sociale
- sins
- ssn
- ssns
- social security
- numero d'assurance social
- national identification number
- national id
- sin#
- soc ins
- social ins

#### <a name="keyword_sin_collaborative"></a>Keyword_sin_collaborative

- driver's license
- drivers license
- driver's licence
- drivers licence
- DOB
- Fecha de nacimiento
- Birthday
- Fecha de nacimiento

## <a name="chile-identity-card-number"></a>Número del carné de identidad de Chile

### <a name="format"></a>Formato

de siete a ocho dígitos más delimitadores de un dígito o letra de control.

### <a name="pattern"></a>Patrón

de siete a ocho dígitos más delimitadores:
- uno o dos dígitos
- un punto opcional
- tres dígitos
- un punto opcional
- tres dígitos
- un guión
- un dígito o letra (sin distinguir mayúsculas de minúsculas), que es un dígito de control

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_chile_id_card"></a>Keyword_chile_id_card

- cédula de identidad
- identificación
- national identification
- national identification number
- national id
- número de identificación nacional
- rol único nacional
- rol único tributario
- EJECUTAR
- RUT
- tarjeta de identificación
- Rol Unico Nacional
- Rol Unico Tributario
- RUN#
- RUT#
- nationaluniqueroleID#
- nacional identidad
- número identificación
- identidad número
- numero identificacion
- identidad numero
- Chilean identity no.
- Chilean identity number
- Chilean identity #
- Unique Tax Registry
- Unique Tributary Role
- Unique Tax Role
- Unique Tributary Number
- Unique National Number
- Unique National Role
- National unique role
- Chile identity no.
- Chile identity number
- Chile identity #


## <a name="china-resident-identity-card-prc-number"></a>Número del carné de identidad de residente de China (PRC)

### <a name="format"></a>Formato

18 dígitos

### <a name="pattern"></a>Patrón

18 dígitos:
- seis dígitos, que son un código de dirección
- ocho dígitos con el formato AAAAMMDD, que son la fecha de nacimiento
- tres dígitos, que son un código de orden
- un dígito, que es un dígito de control

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

### <a name="keyword_china_resident_id"></a>Keyword_china_resident_id

- Resident Identity Card
- PRC
- National Identification Card
- 身份证
- 居民 身份证
- 居民身份证
- 鉴定
- 身分證
- 居民 身份證
- 鑑定


## <a name="credit-card-number"></a>Número de tarjeta de crédito

### <a name="format"></a>Formato

de 14 a 16 dígitos que pueden tener formato o no (dddddddddddddddd) y que deben pasar la prueba Luhn.

### <a name="pattern"></a>Patrón

Detecta tarjetas de todas las marcas principales en todo el mundo, como Visa, MasterCard, Discover Card, JCB, American Express, tarjetas regalo, tarjetas Diner, Rupay y China UnionPay.

### <a name="checksum"></a>Suma de comprobación

Sí, la suma de comprobación de Luhn

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_cc_verification"></a>Keyword_cc_verification

- card verification
- card identification number
- cvn
- cid
- cvc2
- cvv2
- pin block
- security code
- security number
- security no
- issue number
- issue no
- cryptogramme
- numéro de sécurité
- numero de securite
- kreditkartenprüfnummer
- kreditkartenprufnummer
- prüfziffer
- prufziffer
- sichercherts Kode
- sicherheitscode
- sicherchertsnummer
- verfalldatum
- codice di verifica
- cod. sicurezza
- cod sicurezza
- n autorizzazione
- código
- codigo
- cod. seg
- cod seg
- código de segurança
- codigo de seguranca
- codigo de segurança
- código de seguranca
- cód. segurança
- cod. seguranca
- cod. segurança
- cód. seguranca
- cód segurança
- cod seguranca
- cod segurança
- cód seguranca
- número de verificação
- numero de verificacao
- ablauf
- gültig bis
- gültigkeitsdatum
- gultig bis
- gultigkeitsdatum
- scadenza
- data scad
- fecha de expiracion
- fecha de venc
- vencimiento
- válido hasta
- valido hasta
- vto
- data de expiração
- data de expiracao
- data em que expira
- validade
- valor
- vencimento
- transaction
- transaction number
- reference number
- セキュリティコード
- セキュリティ コード
- セキュリティナンバー
- セキュリティ ナンバー
- セキュリティ番号

#### <a name="keyword_cc_name"></a>Keyword_cc_name

- amex
- american express
- americanexpress
- americano espresso
- Visa
- mastercard
- master card
- mc
- mastercards
- master cards
- diner's Club
- diners club
- dinersclub
- detectar
- discover card
- discovercard
- discover cards
- JCB
- BrandSmart
- japanese card bureau
- carte blanche
- carteblanche
- tarjeta de crédito
- cc#
- cc#:
- expiration date
- exp date
- expiry date
- date d’expiration
- date d'exp
- date expiration
- bank card
- bankcard
- card number
- card num
- cardnumber
- cardnumbers
- card numbers
- creditcard
- credit cards
- creditcards
- ccn
- card holder
- cardholder
- card holders
- cardholders
- check card
- checkcard
- check cards
- checkcards
- debit card
- debitcard
- debit cards
- debitcards
- atm card
- atmcard
- atm cards
- atmcards
- en ruta
- en route
- card type
- Cardmember Acct
- cardmember account
- Cardno
- Corporate Card
- Corporate cards
- Type of card
- card account number
- card member account
- Cardmember Acct.
- card no.
- card no
- card number
- carte bancaire
- carte de crédit
- carte de credit
- numéro de carte
- numero de carte
- nº de la carte
- nº de carte
- kreditkarte
- karte
- karteninhaber
- karteninhabers
- kreditkarteninhaber
- kreditkarteninstitut
- kreditkartentyp
- eigentümername
- kartennr
- kartennummer
- kreditkartennummer
- kreditkarten-nummer
- carta di credito
- carta credito
- n. carta
- n carta
- nr. carta
- nr carta
- numero carta
- numero della carta
- numero di carta
- tarjeta credito
- tarjeta de credito
- tarjeta crédito
- tarjeta de crédito
- tarjeta de atm
- tarjeta atm
- tarjeta debito
- tarjeta de debito
- tarjeta débito
- tarjeta de débito
- nº de tarjeta
- n°. de tarjeta
- no de tarjeta
- numero de tarjeta
- número de tarjeta
- tarjeta no
- tarjetahabiente
- cartão de crédito
- cartão de credito
- cartao de crédito
- cartao de credito
- cartão de débito
- cartao de débito
- cartão de debito
- cartao de debito
- débito automático
- debito automatico
- número do cartão
- número do cartão
- número do cartao
- numero do cartao
- número de cartão
- número de cartão
- número de cartao
- numero de cartao
- nº do cartão
- nº do cartao
- nº. do cartão
- no do cartão
- no do cartao
- n°. do cartão
- n°. do cartao
- rupay
- union pay
- unionpay
- diner's
- diners
- クレジットカード番号
- クレジットカードナンバー
- クレジットカード＃
- クレジットカード
- クレジット
- クレカ
- カード番号
- カードナンバー
- カード＃
- アメックス
- アメリカンエクスプレス
- アメリカン エクスプレス
- Visaカード
- Visa カード
- マスターカード
- マスター カード
- マスター
- ダイナースクラブ
- ダイナース クラブ
- ダイナース
- 有効期限
- 期限
- キャッシュカード
- キャッシュ カード
- カード名義人
- カードの名義人
- カードの名義
- デビット カード
- デビットカード
- 中国银联
- 银联



## <a name="croatia-drivers-license-number"></a>Número de permiso de conducir de Croacia

### <a name="format"></a>Formato

ocho dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

ocho dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_croatia_eu_drivers_license_number"></a>Keywords_croatia_eu_driver's_license_number

- vozačka dozvola
- vozačke dozvole


## <a name="croatia-identity-card-number"></a>Número del carné de identidad de Croacia
Esta entidad se incluye en el tipo de información confidencial del Número de identificación nacional de la UE. Está disponible como una entidad de tipo de información confidencial independiente.

### <a name="format"></a>Formato

nueve dígitos

### <a name="pattern"></a>Patrón

nueve dígitos consecutivos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_croatia_id_card"></a>Keyword_croatia_id_card

- majstorski broj građana
- master citizen number
- nacionalni identifikacijski broj
- national identification number
- oib#
- oib
- osobna iskaznica
- osobni id
- osobni identifikacijski broj
- personal identification number
- porezni broj
- porezni identifikacijski broj
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#


## <a name="croatia-passport-number"></a>Número de pasaporte de Croacia

### <a name="format"></a>Formato

nueve dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

nueve dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_croatia_eu_passport_number"></a>Keywords_croatia_eu_passport_number

- broj putovnice
- br. Putovnice
- br putovnice

## <a name="croatia-personal-identification-oib-number"></a>Número de identificación personal de Croacia (OIB)

### <a name="format"></a>Formato

11 dígitos

### <a name="pattern"></a>Patrón

11 dígitos:
- 10 dígitos
- el dígito final es un dígito de control

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_croatia_oib_number"></a>Keyword_croatia_oib_number

- majstorski broj građana
- master citizen number
- nacionalni identifikacijski broj
- national identification number
- oib#
- oib
- osobna iskaznica
- osobni id
- osobni identifikacijski broj
- personal identification number
- porezni broj
- porezni identifikacijski broj
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#

## <a name="cyprus-drivers-license-number"></a>Número de permiso de conducir de Chipre

### <a name="format"></a>Formato

12 dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

12 dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number

#### <a name="keywords_cyprus_eu_drivers_license_number"></a>Keywords_cyprus_eu_driver's_license_number

- άδεια οδήγησης
- αριθμό άδειας οδήγησης
- άδειες οδήγησης


## <a name="cyprus-identity-card"></a>Carné de identidad de Chipre
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

10 dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

10 dígitos

### <a name="checksum"></a>Suma de comprobación

no aplicable

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_cyprus_eu_national_id_card"></a>Keywords_cyprus_eu_national_id_card

- id card number
- identity card number
- kimlik karti
- national identification number
- personal id number
- ταυτοτητασ


## <a name="cyprus-passport-number"></a>Número de pasaporte de Chipre

### <a name="format"></a>Formato

una letra seguida de 6-8 dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

una letra seguida de seis a ocho dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_cyprus_eu_passport_number"></a>Keywords_cyprus_eu_passport_number

- αριθμό διαβατηρίου
- pasaportu
- Αριθμός Διαβατηρίου
- κυπριακό διαβατήριο
- διαβατήριο#
- διαβατήριο
- αριθμός διαβατηρίου
- Pasaport Kimliği
- pasaport numarası
- Pasaport no.
- Αρ. Διαβατηρίου

#### <a name="keywords_cyprus_eu_passport_date"></a>Keywords_cyprus_eu_passport_date

- expires on
- issued on


## <a name="cyprus-tax-identification-number"></a>Número de identificación fiscal de Chipre
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

ocho dígitos y una letra en el patrón especificado

### <a name="pattern"></a>Patrón

ocho dígitos y una letra:

- un "0" o "9"
- siete dígitos
- una letra (sin distinguir mayúsculas de minúsculas)

### <a name="checksum"></a>Suma de comprobación

no aplicable

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_cyprus_eu_tax_file_number"></a>Keywords_cyprus_eu_tax_file_number

- tax id
- tax identification code
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tic#
- tic
- tin id
- tin no
- tin#
- vergi kimlik kodu
- vergi kimlik numarası
- αριθμός φορολογικού μητρώου
- κωδικός φορολογικού μητρώου
- φορολογική ταυτότητα
- φορολογικού κωδικού


## <a name="czech-republic-drivers-license-number"></a>Número de permiso de conducir de la República Checa

### <a name="format"></a>Formato

dos letras seguidas por seis dígitos

### <a name="pattern"></a>Patrón

nueve letras y dígitos:

- letra "E" (sin distinguir mayúsculas de minúsculas)
- una letra
- un espacio (opcional)
- seis dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number

#### <a name="keywords_czech_republic_eu_drivers_license_number"></a>Keywords_czech_republic_eu_driver's_license_number

- řidičský prúkaz
- řidičské průkazy
- číslo řidičského průkazu
- čísla řidičských průkazů


## <a name="czech-passport-number"></a>Número de pasaporte checo

### <a name="format"></a>Formato

ocho dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

ocho dígitos sin espacios ni delimitadores

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_czech_republic_eu_passport_number"></a>Keywords_czech_republic_eu_passport_number

- cestovní pas
- číslo pasu
- cestovní pasu
- passeport no
- čísla pasu

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="czech-national-identity-card-number"></a>Número del carné de identidad nacional checo

### <a name="format"></a>Formato

nueve dígitos con barra diagonal opcional (formato antiguo) 10 dígitos con barra diagonal opcional (nuevo formato)

### <a name="pattern"></a>Patrón

nueve dígitos (formato antiguo):
- seis dígitos que representan la fecha de nacimiento
- una barra diagonal opcional
- tres dígitos

10 dígitos (nuevo formato):
- seis dígitos que representan la fecha de nacimiento
- una barra diagonal opcional
- cuatro dígitos en los que el último es un dígito de control

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_czech_id_card"></a>Keyword_czech_id_card

- birth number
- czech republic id
- czechidno#
- daňové číslo
- identifikační číslo
- identity no
- identity number
- identityno#
- identityno
- insurance number
- national identification number
- nationalnumber#
- national number
- osobní číslo
- personalidnumber#
- personal id number
- personal identification number
- personal number
- pid#
- pid
- pojištění číslo
- rč
- rodne cislo
- rodné číslo
- ssn
- ssn#
- social security number
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#
- unique identification number


## <a name="denmark-drivers-license-number"></a>Número de permiso de conducir de Dinamarca

### <a name="format"></a>Formato

ocho dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

ocho dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number

#### <a name="keywords_denmark_eu_drivers_license_number"></a>Keywords_denmark_eu_driver's_license_number

- kørekort
- kørekortnummer


## <a name="denmark-passport-number"></a>Número de pasaporte de Dinamarca

### <a name="format"></a>Formato

nueve dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

nueve dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_denmark_eu_passport_number"></a>Keywords_denmark_eu_passport_number

- pasnummer
- Passeport n°
- pasnumre

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="denmark-personal-identification-number"></a>Número de identificación personal de Dinamarca

### <a name="format"></a>Formato

10 dígitos que contienen un guion

### <a name="pattern"></a>Patrón

10 dígitos:
- seis dígitos con el formato DDMMAA, que son la fecha de nacimiento
- un guion
- cuatro dígitos en los que el último dígito es un dígito de control

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_denmark_id"></a>Keyword_denmark_id

- centrale personregister
- civilt registreringssystem
- cpr
- cpr#
- gesundheitskarte nummer
- gesundheitsversicherungkarte nummer
- health card
- health insurance card number
- health insurance number
- identification number
- identifikationsnummer
- identifikationsnummer#
- identity number
- krankenkassennummer
- nationalid#
- nationalnumber#
- national number
- personalidnumber#
- personalidentityno#
- personal id number
- personnummer
- personnummer#
- reisekrankenversicherungskartenummer
- rejsesygesikringskort
- ssn
- ssn#
- skat id
- skat kode
- skat nummer
- skattenummer
- social security number
- sundhedsforsikringskort
- sundhedsforsikringsnummer
- sundhedskort
- sundhedskortnummer
- sygesikring
- sygesikringkortnummer
- tax code
- travel health insurance card
- uniqueidentityno#
- tax number
- tax registration number
- tax id
- tax identification number
- taxid#
- taxnumber#
- tax no
- taxno#
- taxnumber
- tax identification no
- tin#
- taxidno#
- taxidnumber#
- tax no#
- tin id
- tin no
- cpr.nr
- cprnr
- cprnummer
- personnr
- personregister
- sygesikringsbevis
- sygesikringsbevisnr
- sygesikringsbevisnummer
- sygesikringskort
- sygesikringskortnr
- sygesikringskortnummer
- sygesikringsnr
- sygesikringsnummer


## <a name="drug-enforcement-agency-dea-number"></a>Número de la agencia antidroga de Estados Unidos (DEA)

### <a name="format"></a>Formato

dos letras seguidas por siete dígitos

### <a name="pattern"></a>Patrón

El patrón debe incluir lo siguiente:
- una letra (sin distinguir mayúsculas de minúsculas) de este conjunto de letras posibles: abcdefghjklmnprstux, que es un código de registrador
- una letra (sin distinguir mayúsculas de minúsculas), que es la primera letra del apellido o dígito "9" del registrador
- siete dígitos, el último de los cuales es el dígito de control

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_dea_number"></a>Keyword_dea_number

- dea
- dea#
- drug enforcement administration
- drug enforcement agency


## <a name="estonia-drivers-license-number"></a>Número de permiso de conducir de Estonia

### <a name="format"></a>Formato

dos letras seguidas por seis dígitos

### <a name="pattern"></a>Patrón

dos letras y seis dígitos:

- letras "ET" (sin distinguir mayúsculas de minúsculas)
- seis dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number

#### <a name="keywords_estonia_eu_drivers_license_number"></a>Keywords_estonia_eu_driver's_license_number

-- permis de conduire
- juhilubade numbrid
- juhiloa number
- juhiluba


## <a name="estonia-personal-identification-code"></a>Código de identificación personal de Estonia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

11 dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

11 dígitos:

- un dígito que corresponde al sexo y el siglo de nacimiento (número impar para varón, número par para mujer; 1-2: siglo XIX; 3-4: siglo XX; 5-6: siglo XXI)
- seis dígitos que corresponden a la fecha de nacimiento (AAMMDD)
- tres dígitos que corresponden a un número de serie que separa a las personas que han nacido en la misma fecha
- un dígito de comprobación

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_estonia_eu_national_id_card"></a>Keywords_estonia_eu_national_id_card

- id-kaart
- ik
- isikukood#
- isikukood
- maksu id
- maksukohustuslase identifitseerimisnumber
- maksunumber
- national identification number
- national number
- personal code
- personal id number
- personal identification code
- personal identification number
- personalidnumber#
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#


## <a name="estonia-passport-number"></a>Número de pasaporte de Estonia

### <a name="format"></a>Formato

una letra seguida de siete dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

una letra seguida de siete dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_estonia_eu_passport_number"></a>Keywords_estonia_eu_passport_number

- eesti kodaniku pass
- passi number
- passinumbrid
- document number
- document no
- dokumendi nr

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="eu-debit-card-number"></a>Número de tarjeta de débito de la UE

### <a name="format"></a>Formato

16 dígitos

### <a name="pattern"></a>Patrón

Patrón complejo y sólido.

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_eu_debit_card"></a>Keyword_eu_debit_card

- account number
- card number
- card no.
- security number
- cc#

#### <a name="keyword_card_terms_dict"></a>Keyword_card_terms_dict

- acct nbr
- acct num
- acct no
- american express
- americanexpress
- americano espresso
- amex
- atm card
- atm cards
- atm kaart
- atmcard
- atmcards
- atmkaart
- atmkaarten
- bancontact
- bank card
- bankkaart
- card holder
- card holders
- card num
- card number
- card numbers
- card type
- cardano numerico
- cardholder
- cardholders
- cardnumber
- cardnumbers
- carta bianca
- carta credito
- carta di credito
- cartao de credito
- cartao de crédito
- cartao de debito
- cartao de débito
- carte bancaire
- carte blanche
- carte bleue
- carte de credit
- carte de crédit
- carte di credito
- carteblanche
- cartão de credito
- cartão de crédito
- cartão de debito
- cartão de débito
- cb
- ccn
- check card
- check cards
- checkcard
- checkcards
- chequekaart
- cirrus
- cirrus-edc-maestro
- controlekaart
- controlekaarten
- tarjeta de crédito
- credit cards
- creditcard
- creditcards
- debetkaart
- debetkaarten
- debit card
- debit cards
- debitcard
- debitcards
- debito automatico
- diners club
- dinersclub
- detectar
- discover card
- discover cards
- discovercard
- discovercards
- débito automático
- edc
- eigentümername
- european debit card
- hoofdkaart
- hoofdkaarten
- in viaggio
- japanese card bureau
- japanse kaartdienst
- jcb
- kaart
- kaart num
- kaartaantal
- kaartaantallen
- kaarthouder
- kaarthouders
- karte
- karteninhaber
- karteninhabers
- kartennr
- kartennummer
- kreditkarte
- kreditkarten-nummer
- kreditkarteninhaber
- kreditkarteninstitut
- kreditkartennummer
- kreditkartentyp
- maestro
- master card
- master cards
- mastercard
- mastercards
- mc
- mister cash
- n carta
- carta
- no de tarjeta
- no do cartao
- no do cartão
- n°. de tarjeta
- n°. do cartao
- n°. do cartão
- nr carta
- nr. carta
- numeri di scheda
- numero carta
- numero de cartao
- numero de carte
- número de cartão
- numero de tarjeta
- numero della carta
- numero di carta
- numero di scheda
- numero do cartao
- número do cartão
- numéro de carte
- nº carta
- nº de carte
- nº de la carte
- nº de tarjeta
- nº do cartao
- nº do cartão
- nº. do cartão
- número de cartao
- número de cartão
- número de tarjeta
- número do cartao
- scheda dell'assegno
- scheda dell'atmosfera
- scheda dell'atmosfera
- scheda della banca
- scheda di controllo
- scheda di debito
- scheda matrice
- schede dell'atmosfera
- schede di controllo
- schede di debito
- schede matrici
- scoprono la scheda
- scoprono le schede
- solo
- supporti di scheda
- supporto di scheda
- switch
- tarjeta atm
- tarjeta credito
- tarjeta de atm
- tarjeta de credito
- tarjeta de debito
- tarjeta debito
- tarjeta no
- tarjetahabiente
- tipo della scheda
- ufficio giapponese della
- scheda
- v pay
- v-pay
- visa
- visa plus
- visa electron
- visto
- visum
- vpay

#### <a name="keyword_card_security_terms_dict"></a>Keyword_card_security_terms_dict

- card identification number
- card verification
- cardi la verifica
- cid
- cod seg
- cod seguranca
- cod segurança
- cod sicurezza
- cod. seg
- cod. seguranca
- cod. segurança
- cod. sicurezza
- codice di sicurezza
- codice di verifica
- codigo
- codigo de seguranca
- codigo de segurança
- crittogramma
- cryptogram
- cryptogramme
- cv2
- cvc
- cvc2
- cvn
- cvv
- cvv2
- cód seguranca
- cód segurança
- cód. seguranca
- cód. segurança
- código
- código de seguranca
- código de segurança
- de kaart controle
- geeft nr uit
- issue no
- issue number
- kaartidentificatienummer
- kreditkartenprufnummer
- kreditkartenprüfnummer
- kwestieaantal
- n°. dell'edizione
- n°. di sicurezza
- numero de securite
- numero de verificacao
- numero dell'edizione
- numero di identificazione della
- scheda
- numero di sicurezza
- numero van veiligheid
- numéro de sécurité
- nº autorizzazione
- número de verificação
- perno il blocco
- pin block
- prufziffer
- prüfziffer
- security code
- security no
- security number
- sicherheits kode
- sicherheitscode
- sicherchertsnummer
- speldblok
- veiligheid nr
- veiligheidsaantal
- veiligheidscode
- veiligheidsnummer
- verfalldatum

#### <a name="keyword_card_expiration_terms_dict"></a>Keyword_card_expiration_terms_dict

- ablauf
- data de expiracao
- data de expiração
- data del exp
- data di exp
- data di scadenza
- data em que expira
- data scad
- data scadenza
- date de validité
- datum afloop
- datum van exp
- de afloop
- espira
- espira
- exp date
- exp datum
- expiration
- expire
- expires
- expiry
- fecha de expiracion
- fecha de venc
- gultig bis
- gultigkeitsdatum
- gültig bis
- gültigkeitsdatum
- la scadenza
- scadenza
- valable
- validade
- valido hasta
- valor
- venc
- vencimento
- vencimiento
- verloopt
- vervaldag
- vervaldatum
- vto
- válido hasta


## <a name="eu-drivers-license-number"></a>Número de permiso de conducir de la UE

Estas entidades se incluyen en el Número de permiso de conducir de la UE y son tipos de información confidencial.

- [Austria](#austria-drivers-license-number)
- [Bélgica](#belgium-drivers-license-number)
- [Bulgaria](#bulgaria-drivers-license-number)
- [Croacia](#croatia-drivers-license-number)
- [Chipre](#cyprus-drivers-license-number)
- [Checo](#czech-republic-drivers-license-number)
- [Dinamarca](#denmark-drivers-license-number)
- [Estonia](#estonia-drivers-license-number)
- [Finlandia](#finland-drivers-license-number)
- [Francia](#france-drivers-license-number)
- [Alemania](#germany-drivers-license-number)
- [Grecia](#greece-drivers-license-number)
- [Hungría](#hungary-drivers-license-number)
- [Irlanda](#ireland-drivers-license-number)
- [Italia](#italy-drivers-license-number)
- [Letonia](#latvia-drivers-license-number)
- [Lituania](#lithuania-drivers-license-number)
- [Luxemburgo](#luxemburg-drivers-license-number)
- [Malta](#malta-drivers-license-number)
- [Países Bajos](#netherlands-drivers-license-number)
- [Polonia](#poland-drivers-license-number)
- [Portugal](#portugal-drivers-license-number)
- [Rumanía](#romania-drivers-license-number)
- [Eslovaquia](#slovakia-drivers-license-number)
- [Eslovenia](#slovenia-drivers-license-number)
- [España](#spain-drivers-license-number)
- [Suecia](#sweden-drivers-license-number)
- [Reino Unido](#uk-drivers-license-number)


## <a name="eu-national-identification-number"></a>Número de identificación nacional de la UE

Estas entidades se incluyen en el Número de identificación nacional de la UE y son tipos de información confidencial.

- [Austria](#austria-identity-card)
- [Bélgica](#belgium-national-number)
- [Bulgaria](#bulgaria-uniform-civil-number)
- [Croacia](#croatia-identity-card-number)
- [Chipre](#cyprus-identity-card)
- [Checo](#czech-national-identity-card-number)
- [Dinamarca](#denmark-personal-identification-number)
- [Estonia](#estonia-personal-identification-code)
- [Finlandia](#finland-national-id)
- [Francia](#france-national-id-card-cni)
- [Alemania](#germany-identity-card-number)
- [Grecia](#greece-national-id-card)
- [Hungría](#hungary-personal-identification-number)
- [Irlanda](#ireland-personal-public-service-pps-number)
- [Italia](#italy-fiscal-code)
- [Letonia](#latvia-personal-code)
- [Lituania](#lithuania-personal-code)
- [Luxemburgo](#luxemburg-national-identification-number-natural-persons)
- [Malta](#malta-identity-card-number)
- [Países Bajos](#netherlands-citizens-service-bsn-number)
- [Polonia](#poland-national-id-pesel)
- [Portugal](#portugal-citizen-card-number)
- [Rumanía](#romania-personal-numeric-code-cnp)
- [Eslovaquia](#slovakia-personal-number)
- [Eslovenia](#slovenia-unique-master-citizen-number)
- [España](#spain-dni)
- [Reino Unido](#uk-national-insurance-number-nino)


## <a name="eu-passport-number"></a>Número de pasaporte de la UE

Estas entidades se incluyen en el Número de pasaporte de la UE y son tipos de información confidencial. Estas entidades se incluyen en el conjunto del número de pasaporte de la UE.

- [Austria](#austria-passport-number)
- [Bélgica](#belgium-passport-number)
- [Bulgaria](#bulgaria-passport-number)
- [Croacia](#croatia-passport-number)
- [Chipre](#cyprus-passport-number)
- [Checo](#czech-passport-number)
- [Dinamarca](#denmark-passport-number)
- [Estonia](#estonia-passport-number)
- [Finlandia](#finland-passport-number)
- [Francia](#france-passport-number)
- [Alemania](#germany-passport-number)
- [Grecia](#greece-passport-number)
- [Hungría](#hungary-passport-number)
- [Irlanda](#ireland-passport-number)
- [Italia](#italy-passport-number)
- [Letonia](#latvia-passport-number)
- [Lituania](#lithuania-passport-number)
- [Luxemburgo](#luxemburg-passport-number)
- [Malta](#malta-passport-number)
- [Países Bajos](#netherlands-passport-number)
- [Polonia](#poland-passport-number)
- [Portugal](#portugal-passport-number)
- [Rumanía](#romania-passport-number)
- [Eslovaquia](#slovakia-passport-number)
- [Eslovenia](#slovenia-passport-number)
- [España](#spain-passport-number)
- [Suecia](#sweden-passport-number)
- [Reino Unido](#us--uk-passport-number)


## <a name="eu-social-security-number-or-equivalent-identification"></a>Número del seguro social (SSN) o identificación equivalente de la UE

Estas entidades se encuentran en el Número del seguro social de la UE o de una identificación equivalente y son tipos de información confidencial.

- [Austria](#austria-social-security-number)
- [Bélgica](#belgium-national-number)
- [Croacia](#croatia-personal-identification-oib-number)
- [Checo](#czech-national-identity-card-number)
- [Dinamarca](#denmark-personal-identification-number)
- [Finlandia](#finland-national-id)
- [Francia](#france-social-security-number-insee)
- [Alemania](#germany-identity-card-number)
- [Grecia](#greece-national-id-card)
- [Hungría](#hungary-social-security-number-taj)
- [Portugal](#portugal-citizen-card-number)
- [España](#spain-social-security-number-ssn)
- [Suecia](#sweden-national-id)


## <a name="eu-tax-identification-number"></a>Número de identificación fiscal de la UE

Estas son las entidades del tipo de información confidencial del Número de identificación fiscal de la UE.

- [Austria](#austria-tax-identification-number)
- [Bélgica](#belgium-national-number)
- [Bulgaria](#bulgaria-uniform-civil-number)
- [Croacia](#croatia-identity-card-number)
- [Chipre](#cyprus-tax-identification-number)
- [Checo](#czech-national-identity-card-number)
- [Dinamarca](#denmark-personal-identification-number)
- [Estonia](#estonia-personal-identification-code)
- [Finlandia](#finland-national-id)
- [Francia](#france-tax-identification-number)
- [Alemania](#germany-tax-identification-number)
- [Grecia](#greece-tax-identification-number)
- [Hungría](#hungary-tax-identification-number)
- [Irlanda](#ireland-personal-public-service-pps-number)
- [Italia](#italy-fiscal-code)
- [Letonia](#latvia-personal-code)
- [Lituania](#lithuania-personal-code)
- [Luxemburgo](#luxemburg-national-identification-number-non-natural-persons)
- [Malta](#malta-tax-identification-number)
- [Países Bajos](#netherlands-tax-identification-number)
- [Polonia](#poland-tax-identification-number)
- [Portugal](#portugal-tax-identification-number)
- [Rumanía](#romania-personal-numeric-code-cnp)
- [Eslovaquia](#slovakia-personal-number)
- [Eslovenia](#slovenia-tax-identification-number)
- [España](#spain-tax-identification-number)
- [Suecia](#sweden-tax-identification-number)
- [Reino Unido](#uk-unique-taxpayer-reference-number)


## <a name="finland-drivers-license-number"></a>Número de permiso de conducir de Finlandia

### <a name="format"></a>Formato

10 dígitos que contienen un guion

### <a name="pattern"></a>Patrón

10 dígitos que contienen un guion:

- seis dígitos
- un guion
- tres dígitos
- un dígito o una letra

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_finland_eu_drivers_license_number"></a>Keywords_finland_eu_driver's_license_number

- ajokortti
- permis de conduire
- ajokortin numero
- kuljettaja lic.
- körkort
- körkortnummer
- förare lic.
- ajokortit
- ajokortin numerot


## <a name="finland-european-health-insurance-number"></a>Número de seguro médico europeo de Finlandia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Número de 20 dígitos

### <a name="pattern"></a>Patrón

Número de 20 dígitos:

- 10 dígitos: 8024680246
- un espacio o guion opcional
- 10 dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_finland_european_health_insurance_number"></a>Keyword_finland_european_health_insurance_number

- ehic#
- ehic
- finlandehicnumber#
- finska sjukförsäkringskort
- health card
- health insurance card
- health insurance number
- hälsokort
- sairaanhoitokortin
- sairausvakuutuskortti
- sairausvakuutusnumero
- sjukförsäkring nummer
- sjukförsäkringskort
- suomen sairausvakuutuskortti
- terveyskortti


## <a name="finland-national-id"></a>Documento nacional de identidad de Finlandia

### <a name="format"></a>Formato

seis dígitos más un carácter que indica un siglo, más tres dígitos, más un dígito de control

### <a name="pattern"></a>Patrón

El patrón debe incluir lo siguiente:
- seis dígitos con el formato DDMMAA, que son la fecha de nacimiento
- marcador de siglo ("-", "+" o "a")
- número de identificación personal de tres dígitos
- un dígito o letra (no distingue entre mayúsculas y minúsculas), que es un dígito de control

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

- ainutlaatuinen henkilökohtainen tunnus
- henkilökohtainen tunnus
- henkilötunnus
- henkilötunnusnumero#
- henkilötunnusnumero
- hetu
- id no
- id number
- identification number
- identiteetti numero
- identity number
- idnumber
- kansallinen henkilötunnus
- kansallisen henkilökortin
- national id card
- national id no.
- personal id
- personal identity code
- personalidnumber#
- personbeteckning
- personnummer
- social security number
- sosiaaliturvatunnus
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#
- tunnistenumero
- tunnus numero
- tunnusluku
- tunnusnumero
- verokortti
- veronumero
- verotunniste
- verotunnus


## <a name="finland-passport-number"></a>Número de pasaporte de Finlandia

Esta entidad se incluye en el tipo de información confidencial del Número de pasaporte de la UE y está disponible como entidad independiente del tipo de información confidencial.

### <a name="format"></a>Formato
combinación de nueve letras y dígitos.

### <a name="pattern"></a>Patrón
combinación de nueve letras y dígitos:
- dos letras (sin distinguir mayúsculas de minúsculas)
- siete dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keyword_finland_passport_number"></a>Keyword_finland_passport_number

- suomalainen passi
- passin numero
- passin numero.#
- passin numero#
- passin numero.
- passi#
- passi number

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry

## <a name="france-drivers-license-number"></a>Número de permiso de conducir de Francia

Esta entidad se incluye en el tipo de información confidencial del Número de permiso de conducir de la UE y está disponible como entidad independiente del tipo de información confidencial.

### <a name="format"></a>Formato

12 dígitos

### <a name="pattern"></a>Patrón

12 dígitos con validación para descontar patrones similares, como números de teléfono franceses.

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_french_drivers_license"></a>Keyword_french_drivers_license

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number
- permis de conduire
- licence number
- license number
- licence numbers
- license numbers
- numéros de licence


## <a name="france-health-insurance-number"></a>Número de seguro médico de Francia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Número de 21 dígitos

### <a name="pattern"></a>Patrón

Número de 21 dígitos:

- 10 dígitos
- un espacio opcional
- 10 dígitos
- un espacio opcional
- un dígito


### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_france_health_insurance_number"></a>Keyword_France_health_insurance_number

- insurance card
- carte vitale
- carte d'assuré social


## <a name="france-national-id-card-cni"></a>Tarjeta de identificación nacional de Francia (CNI)

### <a name="format"></a>Formato

12 dígitos

### <a name="pattern"></a>Patrón

12 dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_france_eu_national_id_card"></a>Keywords_france_eu_national_id_card

- card number
- carte nationale d’identité
- carte nationale d'idenite no
- cni#
- cni
- compte bancaire
- national identification number
- national identity
- nationalidno#
- numéro d'assurance maladie
- numéro de carte vitale


## <a name="france-passport-number"></a>Número de pasaporte de Francia
Esta entidad está disponible en el tipo de información confidencial del Número de pasaporte de la UE. También está disponible como una entidad de tipo de información confidencial independiente.

### <a name="format"></a>Formato

nueve dígitos y letras

### <a name="pattern"></a>Patrón

nueve dígitos y letras:
- dos dígitos
- dos letras (sin distinguir mayúsculas de minúsculas)
- cinco dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_france_eu_passport_number"></a>Keywords_france_eu_passport_number

- numéro de passeport
- passeport n °
- passeport non
- passeport #
- passeport#
- passeportnon
- passeportn °
- passeport français
- passeport livre
- passeport carte
- numéro passeport
- passeport n°
- n° du passeport
- n° passeport

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="france-social-security-number-insee"></a>Número del seguro social de Francia (INSEE)

### <a name="format"></a>Formato

15 dígitos

### <a name="pattern"></a>Patrón

Debe coincidir con uno de dos patrones:
- 13 dígitos seguidos de un espacio, seguido de dos dígitos<br/>
o
- 15 dígitos consecutivos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_fr_insee"></a>Keyword_fr_insee

- code sécu
- d'identité nationale
- insee
- fssn#
- le numéro d'identification nationale
- le code de la sécurité sociale
- national id
- national identification
- no d'identité
- n°. d'identité
- numéro d'assurance
- numéro d'identité
- numero d'identite
- numéro de sécu
- numéro de sécurité sociale
- no d'identite
- n°. d'identite
- ssn
- ssn#
- sécurité sociale
- securité sociale
- securite sociale
- socialsecuritynumber
- social security number
- social security code
- social insurance number


## <a name="france-tax-identification-number"></a>Número de identificación fiscal de Francia

### <a name="format"></a>Formato

13 dígitos

### <a name="pattern"></a>Patrón

13 dígitos

- Un dígito que debe ser 0, 1, 2 o 3
- Un dígito
- Un espacio (opcional)
- Dos dígitos
- Un espacio (opcional)
- Tres dígitos
- Un espacio (opcional)
- Tres dígitos
- Un espacio (opcional)
- Tres dígitos de comprobación


### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_france_eu_tax_file_number"></a>Keywords_france_eu_tax_file_number

- numéro d'identification fiscale
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#


## <a name="france-value-added-tax-number"></a>Número de identificación fiscal de Francia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Patrón alfanumérico de 13 caracteres

### <a name="pattern"></a>Patrón

Patrón alfanumérico de 13 caracteres:

- dos letras: FR (sin distinguir mayúsculas de minúsculas)
- un espacio o guion opcional
- dos letras o dígitos
- un espacio opcional, punto, guion o coma
- tres dígitos
- un espacio opcional, punto, guion o coma
- tres dígitos
- un espacio opcional, punto, guion o coma
- tres dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_france_value_added_tax_number"></a>Keyword_France_value_added_tax_number

- vat number
- vat no
- vat#
- value added tax
- siren identification no numéro d'identification taxe sur valeur ajoutée
- taxe valeur ajoutée
- taxe sur la valeur ajoutée
- n° tva
- numéro de tva
- numéro d'identification siren


## <a name="germany-drivers-license-number"></a>Número de permiso de conducir de Alemania

Esta entidad de tipo de información confidencial está incluida en el tipo de información confidencial del número de permiso de conducir de la UE. También está disponible como una entidad de tipo de información confidencial independiente.

### <a name="format"></a>Formato

combinación de 11 dígitos y letras

### <a name="pattern"></a>Patrón

11 dígitos y letras (sin distinguir mayúsculas de minúsculas):
- un dígito o una letra
- dos dígitos
- seis dígitos o letras
- un dígito
- un dígito o una letra

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_german_drivers_license_number"></a>Keyword_german_drivers_license_number

- ausstellungsdatum
- ausstellungsort
- ausstellende behöde
- ausstellende behorde
- ausstellende behoerde
- führerschein
- fuhrerschein
- fuehrerschein
- führerscheinnummer
- fuhrerscheinnummer
- fuehrerscheinnummer
- führerschein- 
- fuhrerschein- 
- fuehrerschein- 
- führerscheinnummernr
- fuhrerscheinnummernr
- fuehrerscheinnummernr
- führerscheinnummerklasse
- fuhrerscheinnummerklasse
- fuehrerscheinnummerklasse
- nr-führerschein
- nr-fuhrerschein
- nr-fuehrerschein
- no-führerschein
- no-fuhrerschein
- no-fuehrerschein
- n-führerschein
- n-fuhrerschein
- n-fuehrerschein
- permis de conduire
- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dlno


## <a name="germany-identity-card-number"></a>Número del carné de identidad de Alemania

### <a name="format"></a>Formato

desde el 1 de noviembre de 2010: Nueve letras y dígitos

desde el 1 de abril de 1987 hasta el 31 de octubre de 2010: 10 dígitos

### <a name="pattern"></a>Patrón

desde el 1 de noviembre de 2010:
- una letra (sin distinguir mayúsculas de minúsculas)
- ocho dígitos

desde el 1 de abril de 1987 hasta el 31 de octubre de 2010:
- 10 dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_germany_id_card"></a>Keyword_germany_id_card

- ausweis
- gpid
- identificación
- identifikation
- identifizierungsnummer
- identity card
- identity number
- id-nummer
- personal id
- personalausweis
- persönliche id nummer
- persönliche identifikationsnummer
- persönliche-id-nummer


## <a name="germany-passport-number"></a>Número de pasaporte de Alemania

Esta entidad se incluye en el tipo de información confidencial del Número de pasaporte de la UE y está disponible como entidad independiente del tipo de información confidencial.

### <a name="format"></a>Formato

10 dígitos o letras

### <a name="pattern"></a>Patrón

El patrón debe incluir lo siguiente:
- el primer carácter es un dígito o una letra de este conjunto (C, F, G, H, J, K)
- tres dígitos
- cinco dígitos o letras de este conjunto (C, -H, J-N, P, R, T, V-Z)
- un dígito

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_german_passport"></a>Keyword_german_passport

- reisepasse
- reisepassnummer
- No-Reisepass
- Nr-Reisepass
- Reisepass-Nr
- Passnummer
- reisepässe
- passeport no.
- passeport no

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers


## <a name="germany-tax-identification-number"></a>Número de identificación fiscal de Alemania

### <a name="format"></a>Formato

11 dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

11 dígitos

- Dos dígitos
- Un espacio opcional
- Tres dígitos
- Un espacio opcional
- Tres dígitos
- Un espacio opcional
- Dos dígitos
- un dígito de comprobación

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_germany_eu_tax_file_number"></a>Keywords_germany_eu_tax_file_number

- identifikationsnummer
- steuer id
- steueridentifikationsnummer
- steuernummer
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#
- zinn#
- zinn
- zinnnummer


## <a name="germany-value-added-tax-number"></a>Número de identificación fiscal de Alemania
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Patrón alfanumérico de 11 caracteres

### <a name="pattern"></a>Patrón

Patrón alfanumérico de 11 caracteres:

- una letra D o d
- una letra E o e
- un espacio opcional
- tres dígitos
- un espacio o coma opcionales
- tres dígitos
- un espacio o coma opcionales
- tres dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_germany_value_added_tax_number"></a>Keyword_germany_value_added_tax_number

- vat number
- vat no
- vat#
- vat#  mehrwertsteuer
- mwst
- mehrwertsteuer identifikationsnummer
- mehrwertsteuer nummer


## <a name="greece-drivers-license-number"></a>Número de permiso de conducir de Grecia

Esta entidad se incluye en el tipo de información confidencial del Número de permiso de conducir de la UE. También está disponible como una entidad de tipo de información confidencial independiente.

### <a name="format"></a>Formato

nueve dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

nueve dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_greece_eu_drivers_license_number"></a>Keywords_greece_eu_driver's_license_number

- δεια οδήγησης
- Adeia odigisis
- Άδεια οδήγησης
- Δίπλωμα οδήγησης


## <a name="greece-national-id-card"></a>Número de carné de identificación nacional de Grecia

### <a name="format"></a>Formato

Combinación de 7-8 letras y números más un guion

### <a name="pattern"></a>Patrón

Siete letras y números (formato anterior):
- Una letra (cualquier letra del alfabeto griego)
- Un guión
- Seis dígitos

Ocho letras y números (formato nuevo):
- Dos letras cuyo carácter en mayúsculas aparece en los alfabetos griego y latino (ABEZHIKMNOPTYX)
- Un guión
- Seis dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_greece_id_card"></a>Keyword_greece_id_card

- greek id
- greek national id
- greek personal id card
- greek police id
- identity card
- tautotita
- ταυτότητα
- ταυτότητας


## <a name="greece-passport-number"></a>Número de pasaporte de Grecia

### <a name="format"></a>Formato

Dos letras seguidas de siete dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

Dos letras seguidas por siete dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_greece_eu_passport_number"></a>Keywords_greece_eu_passport_number

- αριθμός διαβατηρίου
- αριθμούς διαβατηρίου
- αριθμός διαβατηριο


## <a name="greece-social-security-number-amka"></a>Número del seguro social de Grecia (AMKA)
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

11 dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

- Seis dígitos como fecha de nacimiento AAMMDD
- Cuatro dígitos
- un dígito de control

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_greece_eu_ssn_or_equivalent"></a>Keywords_greece_eu_ssn_or_equivalent

- ssn
- ssn#
- social security no
- socialsecurityno#
- social security number
- amka
- a.m.k.a.
- Αριθμού Μητρώου Κοινωνικής Ασφάλισης


## <a name="greece-tax-identification-number"></a>Número de identificación fiscal de Grecia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Nueve dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

Nueve dígitos

### <a name="checksum"></a>Suma de comprobación

No aplicable

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_greece_eu_tax_file_number"></a>Keywords_greece_eu_tax_file_number

- afm#
- afm
- aφμ|aφμ αριθμός
- aφμ
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- tax registry no
- tax registry number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- taxregistryno#
- tin id
- tin no
- tin#
- αριθμός φορολογικού μητρώου
- τον αριθμό φορολογικού μητρώου
- φορολογικού μητρώου νο


## <a name="hong-kong-identity-card-hkid-number"></a>Número del carné de identidad de Hong Kong (HKID)

### <a name="format"></a>Formato

Combinación de 8-9 letras y números más paréntesis opcionales alrededor del carácter final.

### <a name="pattern"></a>Patrón

Combinación de 8-9 letras:
- 1-2 letras (sin distinguir mayúsculas de minúsculas)
- Seis dígitos
- El carácter final (cualquier dígito o la letra A), es el dígito de control y se incluye opcionalmente entre paréntesis.

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_hong_kong_id_card"></a>Keyword_hong_kong_id_card

- hkid
- hong kong identity card
- HKIDC
- id card
- identity card
- hk identity card
- hong kong id
- 香港身份證
- 香港永久性居民身份證
- 身份證
- 身份証
- 身分證
- 身分証
- 香港身份証
- 香港身分證
- 香港身分証
- 香港身份證
- 香港居民身份證
- 香港居民身份証
- 香港居民身分證
- 香港居民身分証
- 香港永久性居民身份証
- 香港永久性居民身分證
- 香港永久性居民身分証
- 香港永久性居民身份證
- 香港非永久性居民身份證
- 香港非永久性居民身份証
- 香港非永久性居民身分證
- 香港非永久性居民身分証
- 香港特別行政區永久性居民身份證
- 香港特別行政區永久性居民身份証
- 香港特別行政區永久性居民身分證
- 香港特別行政區永久性居民身分証
- 香港特別行政區非永久性居民身份證
- 香港特別行政區非永久性居民身份証
- 香港特別行政區非永久性居民身分證
- 香港特別行政區非永久性居民身分証


## <a name="hungary-drivers-license-number"></a>Número de permiso de conducir de Hungría

### <a name="format"></a>Formato

Dos letras seguidas por seis dígitos

### <a name="pattern"></a>Patrón

Dos letras y seis dígitos:

- Dos letras (no se distingue entre mayúsculas y minúsculas)
- Seis dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_hungary_eu_drivers_license_number"></a>Keywords_hungary_eu_driver's_license_number

- vezetoi engedely
- vezetői engedély
- vezetői engedélyek


## <a name="hungary-personal-identification-number"></a>Número de identificación personal de Hungría
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

11 dígitos

### <a name="pattern"></a>Patrón

11 dígitos:

- Un dígito que corresponde al género, 1 para el varón, 2 para la mujer. Otros números también son posibles para los ciudadanos que han nacido antes de 1900 o para los ciudadanos con doble nacionalidad.
- Seis dígitos que corresponden a la fecha de nacimiento (AAMMDD)
- Tres dígitos que corresponden a un número de serie
- Un dígito de comprobación

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_hungary_eu_national_id_card"></a>Keywords_hungary_eu_national_id_card

- id number
- identification number
- sz ig
- sz. ig.
- sz.ig.
- személyazonosító igazolvány
- személyi igazolvány


## <a name="hungary-passport-number"></a>Número de pasaporte de Hungría

### <a name="format"></a>Formato

Dos letras seguidas de seis o siete dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

Dos letras seguidas de seis o siete dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_hungary_eu_passport_number"></a>Keywords_hungary_eu_passport_number

- útlevél száma
- Útlevelek száma
- útlevél szám

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="hungary-social-security-number-taj"></a>Número del seguro social de Hungría (TAJ)

### <a name="format"></a>Formato

Nueve dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

Nueve dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_hungary_eu_ssn_or_equivalent"></a>Keywords_hungary_eu_ssn_or_equivalent

- hungarian social security number
- social security number
- socialsecuritynumber#
- hssn#
- socialsecuritynno
- hssn
- taj
- taj#
- ssn
- ssn#
- social security no
- áfa
- közösségi adószám
- általános forgalmi adó szám
- hozzáadottérték adó
- áfa szám
- magyar áfa szám


## <a name="hungary-tax-identification-number"></a>Número de identificación fiscal de Hungría
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

10 dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

10 dígitos:

- Un dígito que debe ser "8"
- Ocho dígitos
- Un dígito de comprobación

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_hungary_eu_tax_file_number"></a>Keywords_hungary_eu_tax_file_number

- adóazonosító szám
- adóhatóság szám
- adószám
- hungarian tin
- hungatiantin#
- tax authority no
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#
- vat number


## <a name="hungary-value-added-tax-number"></a>Número de identificación fiscal de Hungría
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Patrón alfanumérico de 10 caracteres

### <a name="pattern"></a>Patrón

Patrón alfanumérico de 10 caracteres:

- dos letras: HU o hu
- espacio opcional
- ocho dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_hungary_value_added_tax_number"></a>Keyword_Hungary_value_added_tax_number

- vat
- value added tax number
- vat#
- vatno#
- hungarianvatno#
- tax no.
- value added tax áfa
- közösségi adószám
- általános forgalmi adó szám
- hozzáadottérték adó
- áfa szám

## <a name="india-permanent-account-number-pan"></a>Número de cuenta permanente de India (PAN)

### <a name="format"></a>Formato

10 letras o dígitos

### <a name="pattern"></a>Patrón

10 letras o dígitos:
- Tres letras (sin distinguir mayúsculas de minúsculas)
- Una letra C, P, H, F, A, T, B, L, J, G (sin distinguir mayúsculas de minúsculas)
- Una letra
- Cuatro dígitos
- Una letra que es un dígito de control alfabético

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_india_permanent_account_number"></a>Keyword_india_permanent_account_number

- Número de cuenta permanente
- PAN

## <a name="india-unique-identification-aadhaar-number"></a>Número de identificación único de India (Aadhaar)

### <a name="format"></a>Formato

12 dígitos que contienen espacios o guiones opcionales

### <a name="pattern"></a>Patrón

12 dígitos:
- Un dígito, que no es 0 ni 1
- Tres dígitos
- Un espacio o un guion opcional
- Cuatro dígitos
- Un espacio o un guion opcional
- El dígito final es el dígito de control

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_india_aadhar"></a>Keyword_india_aadhar
- aadhaar
- aadhar
- aadhar#
- uid
- आधार
- uidai

## <a name="indonesia-identity-card-ktp-number"></a>Número del carné de identidad de Indonesia (KTP)

### <a name="format"></a>Formato

16 dígitos que contienen puntos opcionales

### <a name="pattern"></a>Patrón

16 dígitos:
- Código de provincia de dos dígitos
- Un punto (opcional)
- Código de regencia de ciudad de dos dígitos
- Código de subdistrito de dos dígitos
- Un punto (opcional)
- Seis dígitos con el formato DDMMAA, que son la fecha de nacimiento
- Un punto (opcional)
- Cuatro dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_indonesia_id_card"></a>Keyword_indonesia_id_card

- KTP
- Kartu Tanda Penduduk
- Nomor Induk Kependudukan

## <a name="international-banking-account-number-iban"></a>Número de cuenta bancaria internacional (IBAN)

### <a name="format"></a>Formato

Código del país (dos letras), más los dígitos de control (dos dígitos), más el número bban (hasta 30 caracteres)

### <a name="pattern"></a>Patrón

El patrón debe incluir lo siguiente:

- Código de país o región de dos letras
- Dos dígitos de control (seguidos de un espacio opcional)
- 1-7 grupos de cuatro letras o dígitos (pueden separarse con espacios)
- 1-3 letras o dígitos

El formato de cada país o región es ligeramente diferente. El tipo de información confidencial de IBAN abarca estos 60 países o regiones:

- ad
- ae
- al
- en
- az
- ba
- be
- bg
- bh
- ch
- cr
- cy
- cz
- de
- dk
- do
- ee
- es
- fi
- fo
- fr
- gb
- ge
- gi
- gl
- gr
- h
- hu
- ie
- il
- is
- it
- kw
- kz
- lb
- li
- lt
- lu
- lv
- mc
- md
- yo
- mk
- mr
- mt
- mu
- nl
- No
- pl
- pt
- ro
- rs
- sa
- se
- si
- sk
- sm
- tn
- tr
- vg

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

None

## <a name="ip-address"></a>Dirección IP

### <a name="format"></a>Formato

#### <a name="ipv4"></a>IPv4:
Patrón complejo que tiene en cuenta las versiones con formato (puntos) y sin formato (sin puntos) de las direcciones IPv4.

#### <a name="ipv6"></a>IPv6:
patrón complejo que tiene en cuenta los números de las direcciones IPv6 con formato (que incluyen dos puntos).

### <a name="pattern"></a>Patrón

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_ipaddress"></a>Keyword_ipaddress

- IP (esta palabra clave distingue mayúsculas de minúsculas)
- dirección IP
- ip addresses
- internet protocol
- IP-כתובת ה


## <a name="ip-address-v4"></a>Dirección IP v4

### <a name="format"></a>Formato

Patrón complejo que tiene en cuenta las versiones con formato (puntos) y sin formato (sin puntos) de las direcciones IPv4.

### <a name="pattern"></a>Patrón


### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_ipaddress"></a>Keyword_ipaddress

- IP (distingue mayúsculas de minúsculas)
- dirección IP
- ip addresses
- internet protocol
- IP-כתובת ה


## <a name="ip-address-v6"></a>Dirección IP v6

### <a name="format"></a>Formato

patrón complejo que tiene en cuenta los números de las direcciones IPv6 con formato (que incluyen dos puntos).

### <a name="pattern"></a>Patrón


### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_ipaddress"></a>Keyword_ipaddress

- IP (distingue mayúsculas de minúsculas)
- dirección IP
- ip addresses
- internet protocol
- IP-כתובת ה


## <a name="ireland-drivers-license-number"></a>Número de permiso de conducir de Irlanda

### <a name="format"></a>Formato

Seis dígitos seguidos de cuatro letras

### <a name="pattern"></a>Patrón

Seis dígitos y cuatro letras:

- Seis dígitos
- Cuatro letras (sin distinguir mayúsculas de minúsculas)

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_ireland_eu_drivers_license_number"></a>Keywords_ireland_eu_driver's_license_number

- ceadúnas tiomána
- ceadúnais tiomána

## <a name="ireland-passport-number"></a>Número de pasaporte de Irlanda

### <a name="format"></a>Formato

Dos letras o dígitos seguidos de siete dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

Dos letras o dígitos seguidos de siete dígitos:

- Dos dígitos o letras (sin distinguir mayúsculas de minúsculas)
- Siete dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_ireland_eu_passport_number"></a>Keywords_ireland_eu_passport_number

- passeport numero
- uimhreacha pasanna
- uimhir pas
- uimhir phas
- uimhreacha pas
- uimhir cárta
- uimhir chárta

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="ireland-personal-public-service-pps-number"></a>Número de servicio público personal de Irlanda (PPS)

### <a name="format"></a>Formato

Formato antiguo (hasta el 31 de diciembre de 2012):
- siete dígitos seguidos de una o dos letras

Nuevo formato (a partir del 1 de enero de 2013):
- siete dígitos seguidos de dos letras

### <a name="pattern"></a>Patrón

Formato antiguo (hasta el 31 de diciembre de 2012):
- siete dígitos
- una o dos letras (sin distinguir mayúsculas de minúsculas)

Nuevo formato (a partir del 1 de enero de 2013):
- siete dígitos
- una letra (sin distinguir mayúsculas de minúsculas), que es un dígito de control alfabético
- Una letra opcional en el intervalo A-I o "W"

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_ireland_eu_national_id_card"></a>Keywords_ireland_eu_national_id_card

- client identity service
- identification number
- personal id number
- personal public service number
- personal service no
- phearsanta seirbhíse poiblí
- pps no
- pps number
- pps num
- pps service no
- ppsn
- ppsno#
- ppsno
- psp
- public service no
- publicserviceno#
- publicserviceno
- revenue and social insurance number
- rsi no
- rsi number
- rsin
- seirbhís aitheantais cliant
- uimh
- uimhir aitheantais chánach
- uimhir aitheantais phearsanta
- uimhir phearsanta seirbhíse poiblí
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#


## <a name="israel-bank-account-number"></a>Número de cuenta bancaria de Israel

### <a name="format"></a>Formato

13 dígitos

### <a name="pattern"></a>Patrón

Con formato:
- dos dígitos
- un guión
- tres dígitos
- un guión
- ocho dígitos

Sin formato:
- 13 dígitos consecutivos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_israel_bank_account_number"></a>Keyword_israel_bank_account_number

- Número de cuenta bancaria
- Cuenta bancaria
- Account Number
- מספר חשבון בנק

## <a name="israel-national-identification-number"></a>Número de identificación nacional de Israel

### <a name="format"></a>Formato

nueve dígitos

### <a name="pattern"></a>Patrón

nueve dígitos consecutivos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_israel_national_id"></a>Keyword_Israel_National_ID

-   מספר זהות
-   מספר זיה וי
-   מספר זיהוי ישר אלי      
-   זהותישר אלית
-   هو ية اسرائيل ية عدد
-   هوية إسرائ يلية
-   رقم الهوية
-   عدد هوية فريدة من نوعها
-   idnumber#
-   id number
-   identity no        
-   identitynumber#
-   identity number
-   israeliidentitynumber       
-   personal id
-   unique id  


## <a name="italy-drivers-license-number"></a>Número de permiso de conducir de Italia

Esta entidad de tipo se incluye en el tipo de información confidencial del Número de permiso de conducir de la UE. También está disponible como una entidad de tipo de información confidencial independiente.

### <a name="format"></a>Formato

combinación de 10 letras y dígitos.

### <a name="pattern"></a>Patrón

combinación de 10 letras y dígitos:
- una letra (sin distinguir mayúsculas de minúsculas)
- la letra "A" o "V" (sin distinguir mayúsculas de minúsculas)
- siete dígitos
- una letra (sin distinguir mayúsculas de minúsculas)

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number

#### <a name="keyword_italy_drivers_license_number"></a>Keyword_italy_drivers_license_number

- numero di patente
- patente di guida
- patente guida
- patenti di guida
- patenti guida


## <a name="italy-fiscal-code"></a>Código fiscal de Italia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

una combinación de 16 caracteres de letras y dígitos en el patrón especificado

### <a name="pattern"></a>Patrón

Una combinación de 16 caracteres de letras y dígitos:
- tres letras que corresponden a las tres primeras consonantes del nombre de familia
- tres letras que corresponden a la primera, tercera y cuarta consonantes del nombre
- dos dígitos que corresponden a los últimos dígitos del año de nacimiento
- una letra que corresponde a la letra del mes de nacimiento: las letras se usan en orden alfabético, pero solo se usan las letras A a E, H, L, M, P, R a T (por lo tanto, enero es A y octubre es R)
- dos dígitos que corresponden al día del mes de nacimiento para diferenciar entre géneros, se agregan 40 al día de nacimiento de las mujeres.
- cuatro dígitos que corresponden al código de área específico del municipio donde nació la persona (para los países extranjeros se utilizan códigos de ámbito nacional)
- un dígito de paridad

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_italy_eu_national_id_card"></a>Keywords_italy_eu_national_id_card

- codice fiscal
- codice fiscale
- codice id personale
- codice personale
- fiscal code
- numero certificato personale
- numero di identificazione fiscale
- numero id personale
- numero personale
- personal certificate number
- personal code
- personal id code
- personal id number
- personalcodeno#
- tax code
- tax id
- tax identification no
- tax identification number
- tax identity number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#


## <a name="italy-passport-number"></a>Número de pasaporte de Italia

### <a name="format"></a>Formato

dos letras o dígitos seguidos de siete dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

dos letras o dígitos seguidos de siete dígitos:

- dos dígitos o letras (sin distinguir mayúsculas de minúsculas)
- siete dígitos

### <a name="checksum"></a>Suma de comprobación

no aplicable

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_italy_eu_passport_number"></a>Keywords_italy_eu_passport_number

- italiana passaporto
- passaporto italiana
- passaporto numero
- numéro passeport
- numero di passaporto
- numeri del passaporto
- passeport italien

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="italy-value-added-tax-number"></a>Número de identificación fiscal de Italia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Patrón alfanumérico de 13 caracteres con delimitadores opcionales

### <a name="pattern"></a>Patrón

Patrón alfanumérico de 13 caracteres con delimitadores opcionales:

- I o i
- T o t
- espacio opcional, punto, guion o coma
- 11 dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_italy_value_added_tax_number"></a>Keyword_italy_value_added_tax_number

- vat number
- vat no
- vat#
- iva
- iva#


## <a name="japan-bank-account-number"></a>Número de cuenta bancaria de Japón

### <a name="format"></a>Formato

siete u ocho dígitos

### <a name="pattern"></a>Patrón

número de cuenta bancaria:
- siete u ocho dígitos
- código de la sucursal de la cuenta bancaria:
- cuatro dígitos
- un espacio o guion (opcional)
- tres dígitos

Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_jp_bank_account"></a>Keyword_jp_bank_account

- Checking Account Number
- Comprobando cuenta
- Checking Account #
- Checking Acct Number
- Checking Acct #
- Checking Acct No.
- Checking Account No.
- Número de cuenta bancaria
- Cuenta bancaria
- Bank Account #
- Bank Acct Number
- Bank Acct #
- Bank Acct No.
- Bank Account No.
- Savings Account Number
- Cuenta de ahorros
- Savings Account #
- Savings Acct Number
- Savings Acct #
- Savings Acct No.
- Savings Account No.
- Debit Account Number
- Debit Account
- Debit Account #
- Debit Acct Number
- Debit Acct #
- Debit Acct No.
- Debit Account No.
- 口座番号
- 銀行口座
- 銀行口座番号
- 総合口座
- 普通預金口座
- 普通口座
- 当座預金口座
- 当座口座
- 預金口座
- 振替口座
- 銀行
- バンク

#### <a name="keyword_jp_bank_branch_code"></a>Keyword_jp_bank_branch_code

- 支店番号
- 支店コード
- 店番号

## <a name="japan-drivers-license-number"></a>Número de permiso de conducir de Japón

### <a name="format"></a>Formato

12 dígitos

### <a name="pattern"></a>Patrón

12 dígitos consecutivos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_jp_drivers_license_number"></a>Keyword_jp_drivers_license_number

- driverlicense
- driverslicense
- driver'slicense
- driverslicenses
- driver'slicenses
- driverlicenses
- dl#
- dls#
- lic#
- lics#
- 運転免許証
- 運転免許
- 免許証
- 免許
- 運転免許証番号
- 運転免許番号
- 免許証番号
- 免許番号
- 運転免許証ナンバー
- 運転免許ナンバー
- 免許証ナンバー
- 運転免許証no
- 運転免許no
- 免許証no
- 免許no
- 運転経歴証明書番号
- 運転経歴証明書
- 運転免許証No.
- 運転免許No.
- 免許証No.
- 免許No.
- 運転免許証#
- 運転免許#
- 免許証#
- 免許#


## <a name="japan-my-number---corporate"></a>Mi número de Japón: corporativo
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Número de 13 dígitos

### <a name="pattern"></a>Patrón

Número de 13 dígitos:

- un dígito de uno a nueve
- 12 dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_japan_my_number_corporate"></a>Keyword_japan_my_number_corporate

- corporate number
- マイナンバー
- 共通番号
- マイナンバーカード
- マイナンバーカード番号
- 個人番号カード
- 個人識別番号
- 個人識別ナンバー
- 法人番号
- 指定通知書


## <a name="japan-my-number---personal"></a>Mi número de Japón: personal
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Número de 12 dígitos

### <a name="pattern"></a>Patrón

Número de 12 dígitos:

- cuatro dígitos
- un espacio opcional, punto o guion
- cuatro dígitos
- un espacio opcional, punto o guion
- cuatro dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_japan_my_number_personal"></a>Keyword_japan_my_number_personal

- my number
- マイナンバー
- 個人番号
- 共通番号
- マイナンバーカード
- マイナンバーカード番号
- 個人番号カード
- 個人識別番号
- 個人識別ナンバー
- 通知カード


## <a name="japan-passport-number"></a>Número de pasaporte de Japón

### <a name="format"></a>Formato

dos letras seguidas por siete dígitos

### <a name="pattern"></a>Patrón

Dos letras (sin distinguir mayúsculas de minúsculas) seguidas de siete dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_jp_passport"></a>Keyword_jp_passport

- Passport
- Número de pasaporte
- Passport No.
- Passport #
- パスポート
- パスポート番号
- パスポートナンバー
- パスポート＃
- パスポート#
- パスポートNo.
- 旅券番号
- 旅券番号＃
- 旅券番号♯
- 旅券ナンバー


## <a name="japan-residence-card-number"></a>Número de la tarjeta de residencia de Japón

### <a name="format"></a>Formato

12 letras y dígitos

### <a name="pattern"></a>Patrón

12 letras y dígitos:
- dos letras (sin distinguir mayúsculas de minúsculas)
- ocho dígitos
- dos letras (sin distinguir mayúsculas de minúsculas)

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_jp_residence_card_number"></a>Keyword_jp_residence_card_number

- Número de la tarjeta de residencia
- Residence card no
- Residence card #
- 在留カード番号
- 在留カード
- 在留番号

## <a name="japan-resident-registration-number"></a>Número de registro de residente de Japón

### <a name="format"></a>Formato

11 dígitos

### <a name="pattern"></a>Patrón

11 dígitos consecutivos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_jp_resident_registration_number"></a>Keyword_jp_resident_registration_number

- Número de registro de residente
- Residents Basic Registry Number
- Resident Registration No.
- Resident Register No.
- Residents Basic Registry No.
- Basic Resident Register No.
- 外国人登録証明書番号
- 証明書番号
- 登録番号
- 外国人登録証


## <a name="japan-social-insurance-number-sin"></a>Número del seguro social (SIN) de Japón

### <a name="format"></a>Formato

7-12 dígitos

### <a name="pattern"></a>Patrón

7-12 dígitos:
- cuatro dígitos
- un guion (opcional)
- seis dígitos O
- 7-12 dígitos consecutivos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_jp_sin"></a>Keyword_jp_sin

- Social Insurance No.
- Social Insurance Num
- Social Insurance Number
- 健康保険被保険者番号
- 健保番号
- 基礎年金番号
- 雇用保険被保険者番号
- 雇用保険番号
- 保険証番号
- 社会保険番号
- 社会保険No.
- 社会保険
- 介護保険
- 介護保険被保険者番号
- 健康保険被保険者整理番号
- 雇用保険被保険者整理番号
- 厚生年金
- 厚生年金被保険者整理番号


## <a name="latvia-drivers-license-number"></a>Número de permiso de conducir de Letonia

### <a name="format"></a>Formato

tres letras seguidas por seis dígitos

### <a name="pattern"></a>Patrón

tres letras y seis dígitos:

- tres letras (sin distinguir mayúsculas de minúsculas)
- seis dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_latvia_eu_drivers_license_number"></a>Keywords_latvia_eu_driver's_license_number

- autovadītāja apliecība
- autovadītāja apliecības
- vadītāja apliecība

## <a name="latvia-personal-code"></a>Código personal de Letonia

### <a name="format"></a>Formato

11 dígitos y un guion opcional

### <a name="pattern"></a>Patrón

Formato anterior

11 dígitos y un guion:

- seis dígitos que corresponden a la fecha de nacimiento (DDMMAA)
- un guion
- un dígito que corresponde al siglo de nacimiento ("0" para el siglo XIX, "1" para el siglo XX y "2" para el siglo XXI)
- cuatro dígitos, generados aleatoriamente

Formato nuevo

11 dígitos

- Dos dígitos "32"
- Nueve dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_latvia_eu_national_id_card"></a>Keywords_latvia_eu_national_id_card

- administrative number
- alvas nē
- birth number
- citizen number
- civil number
- electronic census number
- electronic number
- fiscal code
- healthcare user number
- id#
- id-code
- identification number
- identifikācijas numurs
- id-number
- individual number
- latvija alva
- nacionālais id
- national id
- national identifying number
- national identity number
- national insurance number
- national register number
- nodokļa numurs
- nodokļu id
- nodokļu identifikācija numurs
- personal certificate number
- personal code
- personal id code
- personal id number
- personal identification code
- personal identifier
- personal identity number
- personal number
- personal numeric code
- personalcodeno#
- personas kods
- population identification code
- public service number
- registration number
- revenue number
- social insurance number
- social security number
- state tax code
- tax file number
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#
- voter’s number

## <a name="latvia-passport-number"></a>Número de pasaporte de Letonia

### <a name="format"></a>Formato

dos letras o dígitos seguidos de siete dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

dos letras o dígitos seguidos de siete dígitos:

- dos dígitos o letras (sin distinguir mayúsculas de minúsculas)
- siete dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_latvia_eu_passport_number"></a>Keywords_latvia_eu_passport_number

- pase numurs
- pase numur
- pases numuri
- pases nr
- passeport no
- n° du Passeport

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="lithuania-drivers-license-number"></a>Número de permiso de conducir de Lituania

### <a name="format"></a>Formato

ocho dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

ocho dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_lithuania_eu_drivers_license_number"></a>Keywords_lithuania_eu_driver's_license_number

- vairuotojo pažymėjimas
- vairuotojo pažymėjimo numeris
- vairuotojo pažymėjimo numeriai

## <a name="lithuania-personal-code"></a>Código personal de Lituania
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

11 dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

11 dígitos sin espacios ni delimitadores:

- un dígito (1-6) que corresponde al género y el siglo de nacimiento de la persona
- seis dígitos que corresponden a la fecha de nacimiento (AAMMDD)
- tres dígitos que corresponden al número de serie de la fecha de nacimiento
- un dígito de comprobación

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_lithuania_eu_national_id_card"></a>Keywords_lithuania_eu_national_id_card

- asmeninis skaitmeninis kodas
- asmens kodas
- citizen service number
- mokesčių id
- mokesčių identifikavimas numeris
- mokesčių identifikavimo numeris
- mokesčių numeris
- national identification number
- personal code
- personal numeric code
- piliečio paslaugos numeris
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#
- unikalus identifikavimo kodas
- unikalus identifikavimo numeris
- unique identification number
- unique identity number
- uniqueidentityno#

## <a name="lithuania-passport-number"></a>Número de pasaporte de Lituania

### <a name="format"></a>Formato

ocho dígitos o letras sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

ocho dígitos o letras (sin distinguir mayúsculas de minúsculas)

### <a name="checksum"></a>Suma de comprobación

no aplicable

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_lithuania_eu_passport_number"></a>Keywords_lithuania_eu_passport_number

- paso numeris
- paso numeriai
- paso nr

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="luxemburg-drivers-license-number"></a>Número de permiso de conducir de Luxemburgo

### <a name="format"></a>Formato

seis dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

seis dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_luxemburg_eu_drivers_license_number"></a>Keywords_luxemburg_eu_driver's_license_number

- fahrerlaubnis
- Führerschäin

## <a name="luxemburg-national-identification-number-natural-persons"></a>Número de identificación nacional de Luxemburgo: personas físicas
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

13 dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

13 dígitos:

- 11 dígitos
- dos dígitos de control

### <a name="checksum"></a>Suma de comprobación

sí

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_luxemburg_eu_national_id_card"></a>Keywords_luxemburg_eu_national_id_card

- eindeutige id
- eindeutige id-nummer
- eindeutigeid#
- id personnelle
- idpersonnelle#
- idpersonnelle
- individual code
- individual id
- individual identification
- individual identity
- numéro d'identification personnel
- personal id
- personal identification
- personal identity
- personalidno#
- personalidnumber#
- persönliche identifikationsnummer
- unique id
- unique identity
- uniqueidkey#

## <a name="luxemburg-national-identification-number-non-natural-persons"></a>Número de identificación nacional de Luxemburgo: personas jurídicas

### <a name="format"></a>Formato

11 dígitos

### <a name="pattern"></a>Patrón

11 dígitos

- dos dígitos
- un espacio opcional
- tres dígitos
- un espacio opcional
- tres dígitos
- un espacio opcional
- dos dígitos
- un dígito de comprobación

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_luxemburg_eu_tax_file_number"></a>Keywords_luxemburg_eu_tax_file_number

- carte de sécurité sociale
- étain non
- étain#
- identifiant d'impôt
- luxembourg tax identifikatiounsnummer
- numéro d'étain
- numéro d'identification fiscal luxembourgeois
- numéro d'identification fiscale
- social security
- sozialunterstützung
- sozialversécherung
- sozialversicherungsausweis
- steier id
- steier identifikatiounsnummer
- steier nummer
- steuer id
- steueridentifikationsnummer
- steuernummer
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#
- zinn#
- zinn
- zinnzahl

## <a name="luxemburg-passport-number"></a>Número de pasaporte de Luxemburgo

### <a name="format"></a>Formato

ocho dígitos o letras sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

ocho dígitos o letras (sin distinguir mayúsculas de minúsculas)

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_luxemburg_eu_passport_number"></a>Keywords_luxemburg_eu_passport_number
- ausweisnummer
- luxembourg pass
- luxembourg passeport
- luxembourg passport
- no de passeport
- no-reisepass
- nr-reisepass
- numéro de passeport
- pass net
- pass nr
- passnummer
- passeport nombre
- reisepässe
- reisepass-nr
- reisepassnummer

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="luxemburg-national-identification-number-non-natural-persons"></a>Número de identificación nacional de Luxemburgo (personas jurídicas)

### <a name="format"></a>Formato

11 dígitos

### <a name="pattern"></a>Patrón

11 dígitos

- dos dígitos
- un espacio opcional
- tres dígitos
- un espacio opcional
- tres dígitos
- un espacio opcional
- dos dígitos
- un dígito de comprobación

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_luxemburg_eu_tax_file_number"></a>Keywords_luxemburg_eu_tax_file_number

- carte de sécurité sociale
- étain non
- étain#
- identifiant d'impôt
- luxembourg tax identifikatiounsnummer
- numéro d'étain
- numéro d'identification fiscal luxembourgeois
- numéro d'identification fiscale
- social security
- sozialunterstützung
- sozialversécherung
- sozialversicherungsausweis
- steier id
- steier identifikatiounsnummer
- steier nummer
- steuer id
- steueridentifikationsnummer
- steuernummer
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#
- zinn#
- zinn
- zinnzahl


## <a name="malaysia-identification-card-number"></a>Número del carné de identidad de Malasia

### <a name="format"></a>Formato

12 dígitos que contienen guiones opcionales

### <a name="pattern"></a>Patrón

12 dígitos:
- seis dígitos con el formato AAMMDD, que son la fecha de nacimiento
- un guión (opcional)
- un código del lugar de nacimiento de dos letras
- un guión (opcional)
- tres dígitos aleatorios
- un código de género de un dígito

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_malaysia_id_card_number"></a>Keyword_malaysia_id_card_number

- digital application card
- i/c
- i/c no
- ic
- ic no
- id card
- identification Card
- identity card
- k/p
- k/p no
- kad akuan diri
- kad aplikasi digital
- kad pengenalan malaysia
- kp
- kp no
- mykad
- mykas
- mykid
- mypr
- mytentera
- malaysia identity card
- malaysian identity card
- nric
- personal identification card

## <a name="malta-drivers-license-number"></a>Número de permiso de conducir de Malta

### <a name="format"></a>Formato

Combinación de dos caracteres y seis dígitos en el patrón especificado

### <a name="pattern"></a>Patrón

combinación de dos caracteres y seis dígitos:

- dos caracteres (dígitos o letras, sin distinguir mayúsculas de minúsculas)
- un espacio (opcional)
- tres dígitos
- un espacio (opcional)
- tres dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_malta_eu_drivers_license_number"></a>Keywords_malta_eu_driver's_license_number

- liċenzja tas-sewqan
- liċenzji tas-sewwieq


## <a name="malta-identity-card-number"></a>Número de la tarjeta de identificación de Malta
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

siete dígitos seguidos de una letra

### <a name="pattern"></a>Patrón

siete dígitos seguidos de una letra:

- siete dígitos
- una letra de "M, G, A, P, L, H, B, Z" (sin mayúsculas y minúsculas)

### <a name="checksum"></a>Suma de comprobación

No aplicable

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_malta_eu_national_id_card"></a>Keywords_malta_eu_national_id_card

- citizen service number
- id tat-taxxa
- identifika numru tal-biljett
- kodiċi numerali personali
- numru ta 'identifikazzjoni personali
- numru ta 'identifikazzjoni tat-taxxa
- numru ta 'identifikazzjoni uniku
- numru ta' identità uniku
- numru tas-servizz taċ-ċittadin
- numru tat-taxxa
- personal numeric code
- unique identification number
- unique identity number
- uniqueidentityno#


## <a name="malta-passport-number"></a>Número de pasaporte de Malta

### <a name="format"></a>Formato

siete dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

siete dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_malta_eu_passport_number"></a>Keywords_malta_eu_passport_number

- numru tal-passaport
- numri tal-passaport
- Nru tal-passaport

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="malta-tax-identification-number"></a>Número de identificación fiscal de Malta

### <a name="format"></a>Formato

Para los ciudadanos malteses:
- siete dígitos y una letra en el patrón especificado

Entidades maltesas y ciudadanos no malteses:
- nueve dígitos

### <a name="pattern"></a>Patrón

Ciudadanos malteses: siete dígitos y una letra

- siete dígitos
- una letra (sin distinguir mayúsculas de minúsculas)

Entidades maltesas y ciudadanos no maltesas: nueve dígitos

- nueve dígitos

### <a name="checksum"></a>Suma de comprobación

No aplicable

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_malta_eu_tax_file_number"></a>Keywords_malta_eu_tax_file_number

- citizen service number
- id tat-taxxa
- identifika numru tal-biljett
- kodiċi numerali personali
- numru ta 'identifikazzjoni personali
- numru ta 'identifikazzjoni tat-taxxa
- numru ta 'identifikazzjoni uniku
- numru ta' identità uniku
- numru tas-servizz taċ-ċittadin
- numru tat-taxxa
- personal numeric code
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#
- unique identification number
- unique identity number
- uniqueidentityno#

## <a name="netherlands-citizens-service-bsn-number"></a>Número de servicio de ciudadano de Países Bajos (BSN)

### <a name="format"></a>Formato

ocho o nueve dígitos que contienen espacios opcionales

### <a name="pattern"></a>Patrón

entre ocho y nueve dígitos:
- tres dígitos
- un espacio (opcional)
- tres dígitos
- un espacio (opcional)
- entre dos y tres dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_netherlands_eu_national_id_card"></a>Keywords_netherlands_eu_national_id_card

- bsn#
- bsn
- burgerservicenummer
- citizen service number
- person number
- personal number
- personal numeric code
- person-related number
- persoonlijk nummer
- persoonlijke numerieke code
- persoonsgebonden
- persoonsnummer
- sociaal-fiscaal nummer
- social-fiscal number
- sofi
- sofinummer
- uniek identificatienummer
- uniek identiteitsnummer
- unique identification number
- unique identity number
- uniqueidentityno#

## <a name="netherlands-drivers-license-number"></a>Número de permiso de conducir de Países Bajos

### <a name="format"></a>Formato

10 dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

10 dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_netherlands_eu_drivers_license_number"></a>Keywords_netherlands_eu_driver's_license_number

- permis de conduire
- rijbewijs
- rijbewijsnummer
- rijbewijzen
- rijbewijs nummer
- rijbewijsnummers


## <a name="netherlands-passport-number"></a>Número de pasaporte de Países Bajos

### <a name="format"></a>Formato

nueve letras o dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

nueve letras o dígitos

### <a name="checksum"></a>Suma de comprobación

no aplicable

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_netherlands_eu_passport_number"></a>Keywords_netherlands_eu_passport_number

- paspoort nummer
- paspoortnummers
- paspoortnummer
- paspoort nr

## <a name="netherlands-tax-identification-number"></a>Número de identificación fiscal de Países Bajos
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

nueve dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

nueve dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_netherlands_eu_tax_file_number"></a>Keywords_netherlands_eu_tax_file_number

- btw nummer
- hollânske tax identification
- hulandes impuesto id number
- hulandes impuesto identification
- identificatienummer belasting
- identificatienummer van belasting
- impuesto identification number
- impuesto number
- nederlands belasting id nummer
- nederlands belasting identificatie
- nederlands belasting identificatienummer
- nederlands belastingnummer
- nederlandse belasting identificatie
- netherlands tax identification
- netherland's tax identification
- netherlands tin
- netherland's tin
- tax id
- tax identification no
- tax identification number
- tax identification tal
- tax no#
- tax no
- tax number
- tax registration number
- tax tal
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#


## <a name="netherlands-value-added-tax-number"></a>Número de identificación fiscal de Países Bajos
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Patrón alfanumérico de 14 caracteres

### <a name="pattern"></a>Patrón

Patrón alfanumérico de 14 caracteres:

- N o n
- L o l
- espacio opcional, punto o guion
- nueve dígitos
- espacio opcional, punto o guion
- B o b
- dos dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_netherlands_value_added_tax_number"></a>Keyword_netherlands_value_added_tax_number

- vat number
- vat no
- vat#
- wearde tafoege tax getal
- btw nûmer
- btw-nummer


## <a name="new-zealand-bank-account-number"></a>Número de cuenta bancaria de Nueva Zelanda
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Patrón de 14 a 16 dígitos con delimitador opcional

### <a name="pattern"></a>Patrón

Patrón de 14 a 16 dígitos con delimitador opcional:

- dos dígitos
- un guion o espacio opcional
- de tres a cuatro dígitos
- un guion o espacio opcional
- siete dígitos
- un guion o espacio opcional
- de dos a tres dígitos
- un guion o espacio de opciones

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_new_zealand_bank_account_number"></a>Keyword_new_zealand_bank_account_number

- account number
- bank account
- bank_acct_id
- bank_acct_branch
- bank_acct_nbr


## <a name="new-zealand-drivers-license-number"></a>Número del permiso de conducir de Nueva Zelanda
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

patrón alfanumérico de ocho caracteres

### <a name="pattern"></a>Patrón

patrón alfanumérico de ocho caracteres

- dos letras
- seis dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_new_zealand_drivers_license_number"></a>Keyword_new_zealand_drivers_license_number

- driverlicence
- driverlicences
- driver lic
- driver licence
- driver licences
- driverslic
- driverslicence
- driverslicences
- drivers lic
- drivers lics
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's licence
- driver's licences
- driverlic#
- driverlics#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver licence#
- driver licences#
- driverslic#
- driverslics#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's licence#
- driver's licences#
- international driving permit
- international driving permits
- nz automobile association
- new zealand automobile association


## <a name="new-zealand-inland-revenue-number"></a>Número de Hacienda Pública de Nueva Zelanda
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

ocho o nueve dígitos con delimitadores opcionales

### <a name="pattern"></a>Patrón

ocho o nueve dígitos con delimitadores opcionales

- dos o tres dígitos
- un espacio o guion opcional
- tres dígitos
- un espacio o guion opcional
- tres dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_new_zealand_inland_revenue_number"></a>Keyword_new_zealand_inland_revenue_number

- ird no.
- ird no#
- nz ird
- new zealand ird
- ird number
- inland revenue number


## <a name="new-zealand-ministry-of-health-number"></a>Número del Ministerio de Sanidad de Nueva Zelanda

### <a name="format"></a>Formato

tres letras, un espacio (opcional) y cuatro dígitos

### <a name="pattern"></a>Patrón

- tres letras (sin distinguir mayúsculas de minúsculas), excepto "I" y "O"
- un espacio (opcional)
- cuatro dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_nz_terms"></a>Keyword_nz_terms

- NHI
- Nueva Zelanda
- Sanidad
- tratamiento
- National Health Index Number
- nhi number
- nhi no.
- NHI#
- National Health Index No.
- National Health Index Id
- National Health Index #

## <a name="new-zealand-social-welfare-number"></a>Número de bienestar social de Nueva Zelanda

Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

nueve dígitos

### <a name="pattern"></a>Patrón

nueve dígitos

- tres dígitos
- un guión opcional
- tres dígitos
- un guión opcional
- tres dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_new_zealand_social_welfare_number"></a>Keyword_new_zealand_social_welfare_number

- social welfare #
- social welfare#
- social welfare No.
- social welfare number
- swn#


## <a name="norway-identification-number"></a>Número de identificación de Noruega

### <a name="format"></a>Formato

11 dígitos

### <a name="pattern"></a>Patrón

11 dígitos:
- seis dígitos con el formato DDMMAA, que son la fecha de nacimiento
- número individual de tres dígitos
- dos dígitos de control

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_norway_id_number"></a>Keyword_norway_id_number

- Personal identification number
- Norwegian ID Number
- Número de identificación
- Identificación
- Personnummer
- Fødselsnummer


## <a name="philippines-unified-multi-purpose-identification-number"></a>Número de identificación multiuso unificado de Filipinas

### <a name="format"></a>Formato

12 dígitos separados por guiones

### <a name="pattern"></a>Patrón

12 dígitos:
- cuatro dígitos
- un guion
- siete dígitos
- un guion
- un dígito

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_philippines_id"></a>Keyword_philippines_id

- Unified Multi-Purpose ID
- UMID
- Identity Card
- Pinag-isang Multi-Layunin ID

## <a name="poland-drivers-license-number"></a>Número de permiso de conducir de Polonia

### <a name="format"></a>Formato

14 dígitos que contienen dos barras diagonales

### <a name="pattern"></a>Patrón

14 dígitos y dos barras diagonales:

- cinco dígitos
- una barra diagonal
- dos dígitos
- una barra diagonal
- siete dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_poland_eu_drivers_license_number"></a>Keywords_poland_eu_driver's_license_number

- prawo jazdy
- prawa jazdy

## <a name="poland-identity-card"></a>Carné de identidad de Polonia

### <a name="format"></a>Formato

tres letras y seis dígitos

### <a name="pattern"></a>Patrón

tres letras (sin distinguir mayúsculas de minúsculas) seguidas de seis dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_poland_national_id_passport_number"></a>Keyword_poland_national_id_passport_number

- Dowód osobisty
- Numer dowodu osobistego
- Nazwa i numer dowodu osobistego
- Nazwa i nr dowodu osobistego
- Nazwa i nr dowodu tożsamości
- Dowód Tożsamości
- dow. os.


## <a name="poland-national-id-pesel"></a>Identificador nacional de Polonia (PESEL)

### <a name="format"></a>Formato

11 dígitos

### <a name="pattern"></a>Patrón

- seis dígitos que representan la fecha de nacimiento en el formato AAMMDD
- cuatro dígitos
- un dígito de comprobación

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_pesel_identification_number"></a>Keyword_pesel_identification_number

- dowód osobisty
- dowódosobisty
- niepowtarzalny numer
- niepowtarzalnynumer
- nr.-pesel
- nr-pesel
- numer identyfikacyjny
- pesel
- tożsamości narodowej


## <a name="poland-passport-number"></a>Número de pasaporte de Polonia
Esta entidad de tipo de información confidencial está incluida en el tipo de información confidencial del Número de pasaporte de la UE. También está disponible como una entidad de tipo de información confidencial independiente.

### <a name="format"></a>Formato

dos letras y siete dígitos

### <a name="pattern"></a>Patrón

Dos letras (sin distinguir mayúsculas de minúsculas) seguidas de siete dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keyword_polish_national_passport_number"></a>Keyword_polish_national_passport_number

- numer paszportu
- numery paszportów
- numery paszportowe
- nr paszportu
- nr. paszportu
- nr paszportów
- n° passeport
- passeport n°

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="poland-regon-number"></a>Número REGON de Polonia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Número de 9 o 14 dígitos

### <a name="pattern"></a>Patrón

número de nueve o 14 dígitos:

- nueve dígitos
- nueve dígitos
- guion
- cinco dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_poland_regon_number"></a>Keywords_poland_regon_number

- regon id
- statistical number
- statistical id
- statistical no
- regon number
- regonid#
- regonno#
- company id
- companyid#
- companyidno#
- numer statystyczny
- numeru regon
- numerstatystyczny#
- numeruregon#


## <a name="poland-tax-identification-number"></a>Número de identificación fiscal de Polonia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

11 dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

11 dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_poland_eu_tax_file_number"></a>Keywords_poland_eu_tax_file_number

- nip#
- nip
- numer identyfikacji podatkowej
- numeridentyfikacjipodatkowej#
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#
- vat id#
- vat id
- vat no
- vat number
- vatid#
- vatid
- vatno#


## <a name="portugal-citizen-card-number"></a>Número de la tarjeta de ciudadano de Portugal

### <a name="format"></a>Formato

ocho dígitos

### <a name="pattern"></a>Patrón

ocho dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_portugal_citizen_card"></a>Keyword_portugal_citizen_card

- bilhete de identidade
- cartão de cidadão
- citizen card
- document number
- documento de identificação
- id number
- identification no
- identification number
- identity card no
- identity card number
- national id card
- nic
- número bi de portugal
- número de identificação civil
- número de identificação fiscal
- número do documento
- portugal bi number


## <a name="portugal-drivers-license-number"></a>Número de permiso de conducir de Portugal

### <a name="format"></a>Formato

dos patrones: dos letras seguidas de 5-8 dígitos con caracteres especiales

### <a name="pattern"></a>Patrón

Patrón 1: Dos letras seguidas de 5/6 con caracteres especiales:
- Dos letras (no se distingue entre mayúsculas y minúsculas)
- Un guión
- Cinco o seis dígitos
- Un espacio
- Un dígito

Patrón 2: Una letra seguida de 6/8 dígitos con caracteres especiales:
- Una letra (no distingue entre mayúsculas y minúsculas)
- Un guión
- Seis u ocho dígitos
- Un espacio
- Un dígito


### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_portugal_eu_drivers_license_number"></a>Keywords_portugal_eu_driver's_license_number

- carteira de motorista
- carteira motorista
- carteira de habilitação
- carteira habilitação
- número de licença
- número licença
- permissão de condução
- permissão condução
- Licença condução Portugal
- carta de condução

## <a name="portugal-passport-number"></a>Número de pasaporte de Portugal

### <a name="format"></a>Formato

una letra seguida de seis dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

una letra seguida de siete dígitos:

- una letra (sin distinguir mayúsculas de minúsculas)
- seis dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_portugal_eu_passport_number"></a>Keywords_portugal_eu_passport_number

- número do passaporte
- portuguese passport
- portuguese passeport
- portuguese passaporte
- passaporte nº
- passeport nº
- números de passaporte
- portuguese passports
- número passaporte
- números passaporte

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="portugal-tax-identification-number"></a>Número de identificación fiscal de Portugal

### <a name="format"></a>Formato

nueve dígitos con espacios opcionales

### <a name="pattern"></a>Patrón

- tres dígitos
- un espacio opcional
- tres dígitos
- un espacio opcional
- tres dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_portugal_eu_tax_file_number"></a>Keywords_portugal_eu_tax_file_number

- cpf#
- cpf
- nif#
- nif
- número de identificação fisca
- numero fiscal
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#


## <a name="romania-drivers-license-number"></a>Número de permiso de conducir de Rumanía

### <a name="format"></a>Formato

un carácter seguido de ocho dígitos

### <a name="pattern"></a>Patrón

un carácter seguido de ocho dígitos:
- una letra (sin distinguir mayúsculas de minúsculas) o un dígito
- ocho dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_romania_eu_drivers_license_number"></a>Keywords_romania_eu_driver's_license_number

- permis de conducere
- permisului de conducere
- permisului conducere
- permisele de conducere
- permisele conducere
- permis conducere

## <a name="romania-personal-numeric-code-cnp"></a>Código numérico personal de Rumanía (CNP)
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

13 dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

- un dígito del 1 al 9
- seis dígitos que representan la fecha de nacimiento (AAMMDD)
- dos dígitos, que pueden ser 01-52 o 99
- cuatro dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_romania_eu_national_id_card"></a>Keywords_romania_eu_national_id_card

- cnp#
- cnp
- cod identificare personal
- cod numeric personal
- cod unic identificare
- codnumericpersonal#
- codul fiscal nr.
- identificarea fiscală nr#
- id-ul taxei
- insurance number
- insurancenumber#
- national id#
- national id
- national identification number
- număr identificare personal
- număr identitate
- număr personal unic
- număridentitate#
- număridentitate
- numărpersonalunic#
- numărpersonalunic
- număru de identificare fiscală
- numărul de identificare fiscală
- personal numeric code
- pin#
- pin
- tax file no
- tax file number
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#
- unique identification number
- unique identity number
- uniqueidentityno#
- uniqueidentityno

## <a name="romania-passport-number"></a>Número de pasaporte de Rumanía

### <a name="format"></a>Formato

ocho o nueve dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

ocho o nueve dígitos:

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_romania_eu_passport_number"></a>Keywords_romania_eu_passport_number

- numărul pașaportului
- numarul pasaportului
- numerele pașaportului
- Pașaport nr

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="russia-passport-number-domestic"></a>Número de pasaporte nacional de Rusia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Número de 10 dígitos

### <a name="pattern"></a>Patrón

Número de 10 dígitos:

- dos dígitos
- un espacio o guion opcional
- dos dígitos
- un espacio opcional
- seis dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_russia_passport_number_domestic"></a>Keyword_russia_passport_number_domestic

- número de pasaporte
- passport no
- passport #
- passport id
- passportno#
- passportnumber#
- паспорт нет
- паспорт id
- pоссийской паспорт
- pусский номер паспорта
- паспорт#
- паспортid#
- номер паспорта
- номерпаспорта#


## <a name="russia-passport-number-international"></a>Número internacional de pasaporte de Rusia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

número de nueve dígitos

### <a name="pattern"></a>Patrón

número de nueve dígitos:

- dos dígitos
- un espacio o guion opcional
- siete dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_russia_passport_number_international"></a>Keywords_russia_passport_number_international

- número de pasaporte
- passport no
- passport #
- passport id
- passportno#
- passportnumber#
- паспорт нет
- паспорт id
- pоссийской паспорт
- pусский номер паспорта
- паспорт#
- паспортid#
- номер паспорта
- номерпаспорта#


## <a name="saudi-arabia-national-id"></a>Identificador nacional de Arabia Saudí

### <a name="format"></a>Formato

10 dígitos

### <a name="pattern"></a>Patrón

10 dígitos consecutivos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_saudi_arabia_national_id"></a>Keyword_saudi_arabia_national_id

- Identification Card
- I card number
- ID number
- الوطنية الهوية بطاقة رقم


## <a name="singapore-national-registration-identity-card-nric-number"></a>Número del carné de identidad del registro nacional de Singapur (NRIC)

### <a name="format"></a>Formato

nueve letras y dígitos

### <a name="pattern"></a>Patrón

- nueve letras y dígitos:
- la letra "F", "G, "S" o "T" (sin distinguir mayúsculas de minúsculas)
- siete dígitos
- un dígito de control alfabético

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_singapore_nric"></a>Keyword_singapore_nric

- National Registration Identity Card
- Número del carné de identidad
- NRIC
- IC
- Foreign Identification Number
- FIN
- 身份证
- 身份證

## <a name="slovakia-drivers-license-number"></a>Número de permiso de conducir de Eslovaquia

### <a name="format"></a>Formato

un carácter seguido de siete dígitos

### <a name="pattern"></a>Patrón

un carácter seguido de siete dígitos

- una letra (sin distinguir mayúsculas de minúsculas) o un dígito
- siete dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_slovakia_eu_drivers_license_number"></a>Keywords_slovakia_eu_driver's_license_number

- vodičský preukaz
- vodičské preukazy
- vodičského preukazu
- vodičských preukazov

## <a name="slovakia-personal-number"></a>Número personal de Eslovaquia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

nueve o 10 dígitos que contienen una barra diagonal inversa opcional

### <a name="pattern"></a>Patrón

- seis dígitos que representan la fecha de nacimiento
- barra diagonal opcional (/)
- tres dígitos
- un dígito de comprobación opcional

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_slovakia_eu_national_id_card"></a>Keywords_slovakia_eu_national_id_card

- azonosító szám
- birth number
- číslo národnej identifikačnej karty
- číslo občianského preukazu
- daňové číslo
- id number
- identification no
- identification number
- identifikačná karta č
- identifikačné číslo
- identity card no
- identity card number
- národná identifikačná značka č
- national number
- nationalnumber#
- nemzeti személyazonosító igazolvány
- personalidnumber#
- rč
- rodne cislo
- rodné číslo
- social security number
- ssn#
- ssn
- személyi igazolvány szám
- személyi igazolvány száma
- személyigazolvány szám
- tax file no
- tax file number
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#

## <a name="slovakia-passport-number"></a>Número de pasaporte de Eslovaquia

### <a name="format"></a>Formato

un dígito o una letra seguidos de siete dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

Un dígito o una letra (sin distinguir mayúsculas de minúsculas) seguidos de siete dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_slovakia_eu_passport_number"></a>Keywords_slovakia_eu_passport_number

- číslo pasu
- čísla pasov
- pas č.
- Passeport n°
- n° Passeport

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="slovenia-drivers-license-number"></a>Número de permiso de conducir de Eslovenia

### <a name="format"></a>Formato

nueve dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

nueve dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_slovenia_eu_drivers_license_number"></a>Keywords_slovenia_eu_driver's_license_number

- vozniško dovoljenje
- vozniška številka licence
- vozniških dovoljenj
- številka vozniškega dovoljenja
- številke vozniških dovoljenj

## <a name="slovenia-unique-master-citizen-number"></a>Número único de ciudadano de Eslovenia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

13 dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

13 dígitos en el patrón especificado:

- siete dígitos que corresponden a la fecha de nacimiento (DDMMLLL) donde "LLL" corresponde a los tres últimos dígitos del año de nacimiento
- dos dígitos que corresponden al área de nacimiento "50"
- tres dígitos que corresponden a una combinación de género y número de serie para las personas que han nado el mismo día. 000-499 para hombres y 500-999 para mujeres.
- un dígito de comprobación

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_slovenia_eu_national_id_card"></a>Keywords_slovenia_eu_national_id_card

- edinstvena številka glavnega državljana
- emšo
- enotna maticna številka obcana
- id card
- identification number
- identifikacijska številka
- identity card
- nacionalna id
- nacionalni potni list
- national id
- osebna izkaznica
- osebni koda
- osebni ne
- osebni številka
- personal code
- personal number
- personal numeric code
- številka državljana
- unique citizen number
- unique id number
- unique identity number
- unique master citizen number
- unique registration number
- uniqueidentityno #
- uniqueidentityno#

## <a name="slovenia-passport-number"></a>Número de pasaporte de Eslovenia

### <a name="format"></a>Formato

dos letras seguidas de siete dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

dos letras seguidas por siete dígitos:

- la letra "P"
- una letra mayúscula
- siete dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_slovenia_eu_passport_number"></a>Keywords_slovenia_eu_passport_number

- številka potnega lista
- potek veljavnosti
- potni list#
- datum rojstva
- potni list
- številke potnih listov

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="slovenia-tax-identification-number"></a>Número de identificación fiscal de Eslovenia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

ocho dígitos sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

- un dígito del 1 al 9
- seis dígitos
- un dígito de comprobación

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_slovenia_eu_tax_file_number"></a>Keywords_slovenia_eu_tax_file_number

- davčna številka
- identifikacijska številka davka
- številka davčne datoteke
- tax file no
- tax file number
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#


## <a name="south-africa-identification-number"></a>Número de identificación de Sudáfrica

### <a name="format"></a>Formato

13 dígitos que pueden contener espacios

### <a name="pattern"></a>Patrón

13 dígitos:
- seis dígitos con el formato AAMMDD, que son la fecha de nacimiento
- cuatro dígitos
- indicador de ciudadanía de un solo dígito
- el dígito "8" o "9"
- un dígito, que es un dígito de suma de comprobación

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_south_africa_identification_number"></a>Keyword_south_africa_identification_number

- Identity card
- ID
- Identificación

## <a name="south-korea-resident-registration-number"></a>Número de registro de residente de Corea del Sur

### <a name="format"></a>Formato

13 dígitos que contienen un guion

### <a name="pattern"></a>Patrón

13 dígitos:
- seis dígitos con el formato AAMMDD, que son la fecha de nacimiento
- un guion
- un dígito determinado por el siglo y el sexo
- un código de la región de nacimiento de cuatro dígitos
- un dígito que se usa para diferenciar a las personas cuyos números anteriores son idénticos
- un dígito de control

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_south_korea_resident_number"></a>Keyword_south_korea_resident_number

- National ID card
- Citizen's Registration Number
- Jumin deungnok beonho
- RRN
- 주민등록번호

## <a name="spain-drivers-license-number"></a>Número de permiso de conducir de España

### <a name="format"></a>Formato

ocho dígitos seguidos de un carácter

### <a name="pattern"></a>Patrón

ocho dígitos seguidos de un carácter:

- ocho dígitos
- un dígito o una letra (sin distinguir mayúsculas de minúsculas)

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_spain_eu_drivers_license_number"></a>Keywords_spain_eu_driver's_license_number

- permiso de conducción
- permiso conducción
- licencia de conducir
- licencia conducir
- permiso conducir
- permiso de conducir
- permisos de conducir
- permisos conducir
- carnet conducir
- carnet de conducir
- licencia de manejo
- licencia manejo

## <a name="spain-dni"></a>DNI de España
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

ocho dígitos seguidos de un carácter

### <a name="pattern"></a>Patrón

siete dígitos seguidos de un carácter

- ocho dígitos
- un espacio o guion opcional
- una letra de comprobación (sin distinguir mayúsculas de minúsculas)

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_spain_eu_national_id_card"></a>Keywords_spain_eu_national_id_card

- carné de identidad
- dni#
- dni
- dninúmero#
- documento nacional de identidad
- identidad único
- identidadúnico#
- insurance number
- national identification number
- national identity
- nationalid#
- nationalidno#
- nie#
- nie
- nienúmero#
- número de identificación
- número nacional identidad
- personal identification number
- personal identity no
- unique identity number
- uniqueid#

## <a name="spain-passport-number"></a>Número de pasaporte de España

### <a name="format"></a>Formato

una combinación de ocho o nueve caracteres de letras y números sin espacios ni delimitadores

### <a name="pattern"></a>Patrón

una combinación de letras y números de ocho o nueve caracteres:

- dos dígitos o letras
- un dígito o una letra (opcional)
- seis dígitos

### <a name="checksum"></a>Suma de comprobación

No aplicable

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_spain_eu_passport_number"></a>Keywords_spain_eu_passport_number

- libreta pasaporte
- número pasaporte
- españa pasaporte
- números de pasaporte
- número de pasaporte
- números pasaporte
- pasaporte no
- Passeport n°
- n° Passeport
- pasaporte no.
- pasaporte n°
- spain passport

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="spain-social-security-number-ssn"></a>Número de la seguridad social de España (SSN)


### <a name="format"></a>Formato

11-12 dígitos

### <a name="pattern"></a>Patrón

11-12 dígitos:
- dos dígitos
- una barra diagonal (opcional)
- de siete a ocho dígitos
- una barra diagonal (opcional)
- dos dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_spain_eu_passport_number"></a>Keywords_spain_eu_passport_number

- ssn
- ssn#
- socialsecurityno
- social security no
- social security number
- número de la seguridad social

## <a name="spain-tax-identification-number"></a>Número de identificación fiscal de España
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

siete u ocho dígitos y una o dos letras en el patrón especificado

### <a name="pattern"></a>Patrón

Personas ciudadanas de España con un carné de identidad de España:

- ocho dígitos
- una letra mayúscula (distingue mayúsculas de minúsculas)

Españoles no residentes sin carné de identidad de España

- una letra mayúscula "L" (distingue mayúsculas de minúsculas)
- siete dígitos
- una letra mayúscula (distingue mayúsculas de minúsculas)

Españoles residentes menores de 14 años sin un carné de identidad de España:

- una letra mayúscula "K" (distingue mayúsculas de minúsculas)
- siete dígitos
- una letra mayúscula (distingue mayúsculas de minúsculas)

Extranjeros con el número de identificación de extranjería

- una letra mayúscula que sea "X", "Y" o "Z" (distingue mayúsculas de minúsculas)
- siete dígitos
- una letra mayúscula (distingue mayúsculas de minúsculas)

Extranjeros sin un número de identificación de extranjería

- una letra mayúscula que sea "M" (distingue mayúsculas de minúsculas)
- siete dígitos
- una letra mayúscula (distingue mayúsculas de minúsculas)

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_spain_eu_tax_file_number"></a>Keywords_spain_eu_tax_file_number

- cif
- cifid#
- cifnúmero#
- número de contribuyente
- número de identificación fiscal
- número de impuesto corporativo
- spanishcifid#
- spanishcifid
- spanishcifno#
- spanishcifno
- tax file no
- tax file number
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#

## <a name="sweden-drivers-license-number"></a>Número de permiso de conducir de Suecia

### <a name="format"></a>Formato

10 dígitos que contienen un guion

### <a name="pattern"></a>Patrón

10 dígitos que contienen un guion:

- seis dígitos
- un guion
- cuatro dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


#### <a name="keywords_sweden_eu_drivers_license_number"></a>Keywords_sweden_eu_driver's_license_number

- ajokortti
- permis de conducere
- ajokortin numero
- kuljettajat lic.
- drivere lic.
- körkort
- numărul permisului de conducere
-  שאָפער דערלויבעניש נומער
- förare lic.
-  דריווערס דערלויבעניש
- körkortsnummer

## <a name="sweden-national-id"></a>Identificación nacional de Suecia

### <a name="format"></a>Formato

10 o 12 dígitos y un delimitador opcional

### <a name="pattern"></a>Patrón

10 o 12 dígitos y un delimitador opcional:
- dos dígitos (opcional)
- Seis dígitos en formato de fecha AAMMDD
- delimitador de "-" o "+" (opcional)
- cuatro dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_swedish_national_identifier"></a>Keywords_swedish_national_identifier

- id no
- id number
- id#
- identification no
- identification number
- identifikationsnumret#
- identifikationsnumret
- identitetshandling
- identity document
- identity no
- identity number
- id-nummer
- personal id
- personnummer#
- personnummer
- skatteidentifikationsnummer

## <a name="sweden-passport-number"></a>Número de pasaporte de Suecia

### <a name="format"></a>Formato

ocho dígitos

### <a name="pattern"></a>Patrón

ocho dígitos consecutivos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keyword_sweden_passport"></a>Keyword_sweden_passport

- alien registration card
- g3 processing fees
- multiple entry
- Numéro de passeport
- passeport n °
- passeport non
- passeport #
- passeport#
- passeportnon
- passeportn °
- passnummer
- pass nr
- schengen visa
- schengen visas
- single entry
- sverige pass
- visa requirements
- visa processing
- visa type

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- date of issue
- date of expiry


## <a name="sweden-tax-identification-number"></a>Número de identificación fiscal de Suecia
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

10 dígitos y un símbolo en el patrón especificado

### <a name="pattern"></a>Patrón

10 dígitos y un símbolo:

- seis dígitos que corresponden a la fecha de nacimiento (AAMMDD)
- signo más o signo menos
- tres dígitos que hacen que el número de identificación sea único donde:
  - para los números emitidos antes de 1990, los dígitos séptimo y octavo identifican el país de nacimiento o las personas de origen extranjero.
  - el dígito de la novena posición indica el género, impar para el varón o par para la mujer.
- un dígito de comprobación

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_sweden_eu_tax_file_number"></a>Keywords_sweden_eu_tax_file_number

- personal id number
- personnummer
- skatt id nummer
- skatt identifikation
- skattebetalarens identifikationsnummer
- sverige tin
- tax file
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax number
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#


## <a name="swift-code"></a>Código SWIFT

### <a name="format"></a>Formato

cuatro letras seguidas de 5-31 letras o dígitos

### <a name="pattern"></a>Patrón

cuatro letras seguidas de 5-31 letras o dígitos:
- código bancario de cuatro letras (sin distinguir mayúsculas de minúsculas)
- un espacio opcional
- 4-28 letras o dígitos (el número de cuenta bancaria básico [BBAN])
- un espacio opcional
- de una a tres letras o dígitos (resto del BBAN)

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_swift"></a>Keyword_swift

- international organization for standardization 9362
- iso 9362
- iso9362
- swift#
- swiftcode
- swiftnumber
- swiftroutingnumber
- swift code
- swift number #
- swift routing number
- bic number
- bic code
- bic #
- bic#
- bank identifier code
- Organisation internationale de normalisation 9362
- rapide #
- code SWIFT
- le numéro de swift
- swift numéro d'acheminement
- le numéro BIC
- \# BIC
- code identificateur de banque
- SWIFTコード
- SWIFT番号
- BIC番号
- BICコード
- SWIFT コード
- SWIFT 番号
- BIC 番号
- BIC コード
- 金融機関識別コード
- 金融機関コード
- 銀行コード

## <a name="switzerland-ssn-ahv-number"></a>Número AHV de SSN de Suiza
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Número de 13 dígitos

### <a name="pattern"></a>Patrón

Número de 13 dígitos:

- tres dígitos: 756
- un punto opcional
- cuatro dígitos
- un punto opcional
- cuatro dígitos
- un punto opcional
- dos dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_swiss_ssn_ahv_number"></a>Keyword_swiss_ssn_AHV_number

- ahv
- ssn
- pid
- insurance number
- personalidno#
- social security number
- personal id number
- personal identification no.
- insuranceno#
- uniqueidno#
- unique identification no.
- avs number
- personal identity no versicherungsnummer
- identifikationsnummer
- einzigartige identität nicht
- sozialversicherungsnummer
- identification personnelle id
- numéro de sécurité sociale


## <a name="taiwan-national-identification-number"></a>Número de identificación nacional de Taiwán

### <a name="format"></a>Formato

una letra (en inglés) seguida de nueve dígitos

### <a name="pattern"></a>Patrón

una letra (en inglés) seguida de nueve dígitos:
- una letra (en inglés y sin distinguir mayúsculas de minúsculas)
- el dígito "1" o "2"
- ocho dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_taiwan_national_id"></a>Keyword_taiwan_national_id

- 身份證字號
- 身份證
- 身份證號碼
- 身份證號
- 身分證字號
- 身分證
- 身分證號碼
- 身份證號
- 身分證統一編號
- 國民身分證統一編號
- 簽名
- 蓋章
- 簽名或蓋章
- 簽章

## <a name="taiwan-passport-number"></a>Número de pasaporte de Taiwán

### <a name="format"></a>Formato

- número de pasaporte biométrico: nueve dígitos
- número de pasaporte no biométrico: nueve dígitos

### <a name="pattern"></a>Patrón
número de pasaporte biométrico:
- el carácter "3"
- ocho dígitos

número de pasaporte no biométrico:
- nueve dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_taiwan_passport"></a>Keyword_taiwan_passport

- ROC passport number
- Passport number
- Passport no
- Passport Num
- Passport #
- 护照
- 中華民國護照
- Zhōnghuá Mínguó hùzhào

## <a name="taiwan-resident-certificate-arctarc-number"></a>Certificado de residente de Taiwán (ARC/TARC)

### <a name="format"></a>Formato

10 letras y dígitos

### <a name="pattern"></a>Patrón

10 letras y dígitos:
- dos letras (sin distinguir mayúsculas de minúsculas)
- ocho dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_taiwan_resident_certificate"></a>Keyword_taiwan_resident_certificate

- Resident Certificate
- Resident Cert
- Resident Cert.
- Identification card
- Resident Certificate
- ARC
- Taiwan Area Resident Certificate
- TARC
- 居留證
- 外僑居留證
- 台灣地區居留證

## <a name="thai-citizen-id"></a>Thai Citizen ID

### <a name="format"></a>Formato

13 dígitos

### <a name="pattern"></a>Patrón

13 dígitos:
- el primer dígito no es cero o nueve
- 12 dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_thai_citizen_id"></a>Keyword_thai_citizen_Id

- Número de identificación
- Identification Number
- บัตรประชาชน
- รหัสบัตรประชาชน
- บัตรประชาชน
- รหัสบัตรประชาชน

## <a name="turkish-national-identification-number"></a>Número de identificación nacional de Turquía

### <a name="format"></a>Formato

11 dígitos

### <a name="pattern"></a>Patrón

11 dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_turkish_national_id"></a>Keyword_turkish_national_id

- TC Kimlik No
- TC Kimlik numarası
- Vatandaşlık numarası
- Vatandaşlık no

## <a name="uk-drivers-license-number"></a>Reino Unido Número de permiso de conducir

### <a name="format"></a>Formato

Combinación de 18 letras y dígitos en el formato especificado

### <a name="pattern"></a>Patrón

18 letras y dígitos:
- Cinco letras (sin distinguir mayúsculas de minúsculas) o el dígito "9" en lugar de una letra.
- Un dígito.
- Cinco dígitos en el formato de fecha MMDDA para la fecha de nacimiento. El séptimo carácter se incrementa en 50 si el conductor es mujer; por ejemplo, de 51 a 62 en lugar de 01 a 12.
- Dos letras (sin distinguir mayúsculas de minúsculas) o el dígito "9" en lugar de una letra.
- Cinco dígitos.

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- driver lics
- driver license
- driver licenses
- driver licence
- driver licences
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- drivers license
- drivers licenses
- drivers licence
- drivers licences
- driver'lic
- driver'lics
- driver'license
- driver'licenses
- driver'licence
- driver'licences
- driver' lic
- driver' lics
- driver' license
- driver' licenses
- driver' licence
- driver' licences
- driver'slic
- driver'slics
- driver'slicense
- driver'slicenses
- driver'slicence
- driver'slicences
- driver's lic
- driver's lics
- driver's license
- driver's licenses
- driver's licence
- driver's licences
- dl#
- dls#
- driverlic#
- driverlics#
- driverlicense#
- driverlicenses#
- driverlicence#
- driverlicences#
- driver lic#
- driver lics#
- driver license#
- driver licenses#
- driver licences#
- driverslic#
- driverslics#
- driverslicense#
- driverslicenses#
- driverslicence#
- driverslicences#
- drivers lic#
- drivers lics#
- drivers license#
- drivers licenses#
- drivers licence#
- drivers licences#
- driver'lic#
- driver'lics#
- driver'license#
- driver'licenses#
- driver'licence#
- driver'licences#
- driver' lic#
- driver' lics#
- driver' license#
- driver' licenses#
- driver' licence#
- driver' licences#
- driver'slic#
- driver'slics#
- driver'slicense#
- driver'slicenses#
- driver'slicence#
- driver'slicences#
- driver's lic#
- driver's lics#
- driver's license#
- driver's licenses#
- driver's licence#
- driver's licences#
- driving licence 
- driving license
- dlno#
- driv lic
- driv licen
- driv license
- driv licenses
- driv licence
- driv licences
- driver licen
- drivers licen
- driver's licen
- driving lic
- driving licen
- driving licenses
- driving licence
- driving licences
- driving permit
- dl no
- dlno
- dl number


## <a name="uk-electoral-roll-number"></a>Reino Unido número de lista electoral

### <a name="format"></a>Formato

dos letras seguidas por 1-4 dígitos

### <a name="pattern"></a>Patrón

dos letras (sin distinguir mayúsculas de minúsculas) seguidas de los números 1 al 4

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_uk_electoral"></a>Keyword_uk_electoral

- nominación del consejo
- formulario de nominación
- registro electoral
- lista electoral


## <a name="uk-national-health-service-number"></a>Reino Unido número del servicio de salud nacional

### <a name="format"></a>Formato

10-17 dígitos separados por espacios

### <a name="pattern"></a>Patrón

10-17 dígitos:
- 3 o 10 dígitos
- un espacio
- tres dígitos
- un espacio
- cuatro dígitos

### <a name="checksum"></a>Suma de comprobación

Yes

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_uk_nhs_number"></a>Keyword_uk_nhs_number

- national health service
- nhs
- health services authority
- health authority

#### <a name="keyword_uk_nhs_number1"></a>Keyword_uk_nhs_number1

- patient id
- patient identification
- patient no
- patient number

#### <a name="keyword_uk_nhs_number_dob"></a>Keyword_uk_nhs_number_dob

- GP
- DOB
- D.O.B
- Fecha de nacimiento
- Birth Date

## <a name="uk-national-insurance-number-nino"></a>Reino Unido número del seguro nacional (NINO)
Esta entidad de tipo de información confidencial está incluida en el tipo de información confidencial del Número de identificación nacional de la UE. También está disponible como una entidad de tipo de información confidencial independiente.

### <a name="format"></a>Formato

siete o nueve caracteres separados por espacios o guiones

### <a name="pattern"></a>Patrón

dos posibles patrones:

- dos letras (los números NINO válidos usan solo ciertos caracteres en este prefijo, que el patrón se encarga de validar; no se distingue entre mayúsculas y minúsculas)
- seis dígitos
- "A", "B", "C" o "D" (como en el prefijo, solo se permiten determinados caracteres en el sufijo; no se distingue entre mayúsculas y minúsculas)

O BIEN

- dos letras
- un espacio o guion
- dos dígitos
- un espacio o guion
- dos dígitos
- un espacio o guion
- dos dígitos
- un espacio o guion
- "A", "B", "C" o "D"

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_uk_nino"></a>Keyword_uk_nino

- national insurance number
- national insurance contributions
- protection act
- seguros
- social security number
- insurance application
- medical application
- social insurance
- medical attention
- social security
- great britain
- NI Number
- NI No.
- NI #
- NI#
- insurance#
- insurancenumber
- nationalinsurance#
- nationalinsurancenumber


## <a name="uk-unique-taxpayer-reference-number"></a>Reino Unido Número único de referencia del contribuyente
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

10 dígitos sin espacios ni delimitadores


### <a name="pattern"></a>Patrón

10 dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_uk_eu_tax_file_number"></a>Keywords_uk_eu_tax_file_number

- tax number
- tax file
- tax id
- tax identification no
- tax identification number
- tax no#
- tax no
- tax registration number
- taxid#
- taxidno#
- taxidnumber#
- taxno#
- taxnumber#
- taxnumber
- tin id
- tin no
- tin#

## <a name="us-bank-account-number"></a>Número de cuenta bancaria de EE. UU.

### <a name="format"></a>Formato

6-17 dígitos

### <a name="pattern"></a>Patrón

6-17 dígitos consecutivos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_usa_bank_account"></a>Keyword_usa_Bank_Account

- Checking Account Number
- Comprobando cuenta
- Checking Account #
- Checking Acct Number
- Checking Acct #
- Checking Acct No.
- Checking Account No.
- Número de cuenta bancaria
- Bank Account #
- Bank Acct Number
- Bank Acct #
- Bank Acct No.
- Bank Account No.
- Savings Account Number
- Savings Account.
- Savings Account #
- Savings Acct Number
- Savings Acct #
- Savings Acct No.
- Savings Account No.
- Debit Account Number
- Debit Account
- Debit Account #
- Debit Acct Number
- Debit Acct #
- Debit Acct No.
- Debit Account No.

## <a name="us-drivers-license-number"></a>Número de permiso de conducir de EE. UU.

### <a name="format"></a>Formato

Depende del estado

### <a name="pattern"></a>Patrón

depende del estado; por ejemplo, Nueva York:
- nueve dígitos con formato ddd ddd ddd coincidirán.
- nueve dígitos, con formato ddddddddd, no coincidirán.

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_us_drivers_license_abbreviations"></a>Keyword_us_drivers_license_abbreviations

- DL
- DLS
- CDL
- CDLS
- ID
- Ids.
- DL#
- DLS#
- CDL#
- CDLS#
- ID#
- IDs#
- ID number
- ID numbers
- LIC
- LIC#

#### <a name="keyword_us_drivers_license"></a>Keyword_us_drivers_license

- DriverLic
- DriverLics
- DriverLicense
- DriverLicenses
- Driver Lic
- Driver Lics
- Driver License
- Driver Licenses
- DriversLic
- DriversLics
- DriversLicense
- DriversLicenses
- Drivers Lic
- Drivers Lics
- Drivers License
- Drivers Licenses
- Driver'Lic
- Driver'Lics
- Driver'License
- Driver'Licenses
- Driver' Lic
- Driver' Lics
- Driver' License
- Driver' Licenses
- Driver'sLic
- Driver'sLics
- Driver'sLicense
- Driver'sLicenses
- Driver's Lic
- Driver's Lics
- Driver's License
- EE. UU.
- identification number
- números de identificación
- identification #
- id card
- id cards
- identification card
- identification cards
- DriverLic#
- DriverLics#
- DriverLicense#
- DriverLicenses#
- Driver Lic#
- Driver Lics#
- Driver License#
- Driver Licenses#
- DriversLic#
- DriversLics#
- DriversLicense#
- DriversLicenses#
- Drivers Lic#
- Drivers Lics#
- Drivers License#
- Drivers Licenses#
- Driver'Lic#
- Driver'Lics#
- Driver'License#
- Driver'Licenses#
- Driver' Lic#
- Driver' Lics#
- Driver' License#
- Driver' Licenses#
- Driver'sLic#
- Driver'sLics#
- Driver'sLicense#
- Driver'sLicenses#
- Driver's Lic#
- Driver's Lics#
- Driver's License#
- Driver's Licenses#
- id card#
- id cards#
- identification card#
- identification cards#


#### <a name="keyword_state_name_drivers_license_name"></a>Keyword_[nombre_estado]_drivers_license_name

- abreviatura del estado (por ejemplo, "NY")
- nombre del estado (por ejemplo, "Nueva York")

## <a name="us-individual-taxpayer-identification-number-itin"></a>Número de identificación fiscal (NIF) individual de EE. UU. (ITIN)

### <a name="format"></a>Formato

Nueve dígitos que comienzan con un "9" y contienen un "7" u "8" como cuarto dígito; en un formato opcional con espacios o guiones

### <a name="pattern"></a>Patrón

con formato:
- el dígito "9"
- dos dígitos
- un espacio o guion
- un "7" u "8"
- un dígito
- un espacio o guion
- cuatro dígitos

sin formato:
- el dígito "9"
- dos dígitos
- un "7" u "8"
- cinco dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_itin"></a>Keyword_itin

- taxpayer
- tax id
- tax identification
- itin
- i.t.i.n.
- ssn
- tin
- social security
- tax payer
- itins
- taxid
- individual taxpayer


## <a name="us-social-security-number-ssn"></a>Número del seguro social de EE. UU. (SSN)

### <a name="format"></a>Formato

nueve dígitos que pueden estar en un patrón con formato o sin formato.

> [!NOTE]
> Si se emite antes de mediados de 2011, el SSN tiene un formato seguro en el que algunas partes del número deben estar dentro de determinados intervalos para ser válidas (pero no hay ninguna suma de comprobación).

### <a name="pattern"></a>Patrón

cuatro funciones buscan el número de SSN en cuatro patrones diferentes:
- Func_ssn busca números de SSN con un formato seguro anterior a 2011, a los que se agregan guiones o espacios (ddd-dd-dddd O ddd dd dddd).
- Func_unformatted_ssn busca números de SSN con un formato seguro anterior a 2011 que no tengan formato de nueve dígitos consecutivos (ddddddddd).
- Func_randomized_formatted_ssn busca números de SSN anteriores a 2011, que tengan un formato que incluye guiones o espacios (ddd-dd-dddd O ddd dd dddd).
- Func_randomized_unformatted_ssn busca números de SSN anterior a 2011 que no tengan formato de nueve dígitos consecutivos (ddddddddd).

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_ssn"></a>Keyword_ssn

- SSA Number
- social security number
- social security #
- social security#
- social security no
- Social Security#
- Soc Sec
- SSN
- SSNS
- SSN#
- SS#
- SSID

## <a name="us--uk-passport-number"></a>EE. UU./Reino Unido número de pasaporte

### <a name="format"></a>Formato

nueve dígitos

### <a name="pattern"></a>Patrón

nueve dígitos consecutivos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- número de pasaporte
- passportnumbers
- passport numbers

#### <a name="keywords_uk_eu_passport_number"></a>Keywords_uk_eu_passport_number

- british passport
- uk passport


## <a name="ukraine-passport-domestic"></a>Pasaporte nacional de Ucrania
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

nueve dígitos

### <a name="pattern"></a>Patrón

nueve dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_ukraine_passport_domestic"></a>Keyword_ukraine_passport_domestic

- ukraine passport
- número de pasaporte
- passport no
- паспорт України
- номер паспорта
- персональний


## <a name="ukraine-passport-international"></a>Pasaporte internacional de Ucrania
Este tipo de información confidencial solo está disponible para su uso en:
- directivas para la prevención de pérdida de datos
- directivas de cumplimiento de la comunicación
- gobernanza de la información
- administración de registros
- Microsoft Cloud App Security

### <a name="format"></a>Formato

Patrón alfanumérico de ocho caracteres

### <a name="pattern"></a>Patrón

Patrón alfanumérico de ocho caracteres:
- dos letras o dígitos
- seis dígitos

### <a name="checksum"></a>Suma de comprobación

No

### <a name="keywords"></a>Palabras clave

#### <a name="keyword_ukraine_passport_international"></a>Keyword_ukraine_passport_international

- ukraine passport
- número de pasaporte
- passport no
- паспорт України
- номер паспорта
