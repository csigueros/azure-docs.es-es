---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: update_sas_token.sh
description: Actualiza el token de SAS de la biblioteca SAP en Azure Key Vault
ms.openlocfilehash: 09b9506c54e2595679d5fcd7189548453472d6a1
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733878"
---
# <a name="update_sas_tokensh"></a>update_sas_token.sh

## <a name="synopsis"></a>Sinopsis
Actualiza el token de SAS de la biblioteca SAP en Azure Key Vault.

## <a name="syntax"></a>Sintaxis

```bash

update_sas_token.sh
```

## <a name="description"></a>Descripción
Actualiza el token de SAS de la biblioteca SAP en Azure Key Vault. Solicita el nombre de la cuenta de almacenamiento de la biblioteca SAP y el nombre del almacén de claves del implementador.

## EXAMPLES

### <a name="example-1"></a>EJEMPLO 1

Solicita el nombre de la cuenta de almacenamiento de la biblioteca SAP y el nombre del almacén de claves del implementador.

```bash

update_sas_token.sh
```

### <a name="example-2"></a>EJEMPLO 2

```bash

export SAP_LIBRARY_TF=mgmtweeusaplibXXX
export SAP_KV_TF=MGMTWEEUDEP00userYYY

update_sas_token.sh
```


## <a name="parameters"></a>Parámetros

None

## <a name="notes"></a>Notas
v0.9: Versión inicial


Copyright (c) Microsoft Corporation.
Autorización sujeta a la licencia MIT.
## <a name="related-links"></a>Vínculos relacionados

[Repositorio de GitHub: Marco de automatización de la implementación de SAP](https://github.com/Azure/sap-hana)
