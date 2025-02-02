---
title: Administración de protocolos y cifrados en Azure API Management | Microsoft Docs
description: Obtenga información sobre cómo administrar protocolos (TLS) y cifrados (DES) en Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/07/2021
ms.author: danlep
ms.openlocfilehash: 5fc47f2238427c7875d2af29324e06d8b6487f1a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001796"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Administración de protocolos y cifrados en Azure API Management

Azure API Management admite varias versiones del protocolo de Seguridad de la capa de transporte (TLS):
* En el cliente
* En el back-end
* Para el cifrado 3DES

Esta guía muestra cómo administrar la configuración de los protocolos y cifrados en una instancia de Azure API Management.

![Administración de protocolos y cifrados en APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Requisitos previos

* Una instancia de API Management [Cree una suscripción si todavía no lo ha hecho](get-started-create-service-instance.md).

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Cómo administrar protocolos TLS y el cifrado 3DES

1. Vaya a la instancia de **API Management** en Azure Portal.
1. Desplácese a la sección **Seguridad** del menú lateral.
1. En la sección Seguridad, seleccione **Protocols + ciphers** (Protocolos y cifrados).  
1. Habilite o deshabilite los protocolos o los cifrados que quiera.
1. Haga clic en **Save**(Guardar). Los cambios se aplicarán en el plazo de una hora.  

> [!NOTE]
> Algunos protocolos o conjuntos de cifrado (como TLS 1.2 en el back-end) no se pueden habilitar ni deshabilitar desde Azure Portal. En su lugar, deberá utilizar la llamada REST. Utilice la estructura `properties.customProperties` de este artículo sobre la [creación o actualización de la API REST de API Management](/rest/api/apimanagement/2021-01-01-preview/api-management-service/create-or-update).

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre [TLS](/dotnet/framework/network-programming/tls).
* Consulte más [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre la administración de API.
