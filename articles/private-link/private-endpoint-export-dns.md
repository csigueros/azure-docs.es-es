---
title: Exportación de registros DNS para un punto de conexión privado mediante Azure Portal
titleSuffix: Azure Private Link
description: En este tutorial, aprenderá a exportar los registros DNS para un punto de conexión privado en Azure Portal.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: how-to
ms.date: 07/25/2021
ms.custom: how-to
ms.openlocfilehash: d4ad1fd995bfd4f1565215491037e6450bd65bf2
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713188"
---
# <a name="export-dns-records-for-a-private-endpoint-using-the-azure-portal"></a>Exportación de registros DNS para un punto de conexión privado mediante Azure Portal

Un punto de conexión privado en Azure necesita registros DNS para la resolución de nombres del punto de conexión. El registro DNS resuelve la dirección IP privada del punto de conexión para el recurso configurado. Para exportar los registros DNS del punto de conexión, use el centro Private Link del portal.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un punto de conexión privado configurado en la suscripción. En el ejemplo de este artículo, se usa un punto de conexión privado para una aplicación web de Azure. Para obtener más información sobre cómo crear un punto de conexión privado para una aplicación web, vea [Tutorial: Conexión a una aplicación web mediante un punto de conexión privado de Azure](tutorial-private-endpoint-webapp-portal.md).

## <a name="export-endpoint-dns-records"></a>Exportación de registros DNS de punto de conexión

En esta sección, iniciará sesión en Azure Portal y buscará el centro de vínculos privados.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda de la parte superior del portal, escriba **Private Link**.

3. Seleccione **Private link**.

4. En Private Link Center, seleccione **Puntos de conexión privados**.

    :::image type="content" source="./media/private-endpoint-export-dns/private-link-center.png" alt-text="Selección de Puntos de conexión privados en el centro de Private Link":::

5. En **Puntos de conexión privados**, seleccione el punto de conexión para el que quiera exportar los registros DNS. Seleccione **Descargar hostfile** para descargar los registros DNS del punto de conexión en un formato de archivo de host.
    
    :::image type="content" source="./media/private-endpoint-export-dns/download-host-file.png" alt-text="Descarga de registros DNS de punto de conexión":::

6. Los registros de archivos de host descargados tendrán un aspecto similar al siguiente:

    ```text
    # Exported from the Azure portal "2021-07-26 11:26:03Z"
    # Private IP    FQDN    Private Endpoint Id
    10.1.0.4    mywebapp8675.scm.azurewebsites.net    #/subscriptions/7cc654c6-760b-442f-bd02-1a8a64b17413/resourceGroups/myResourceGroup/providers/Microsoft.Network/privateEndpoints/mywebappendpoint
    10.1.0.4    mywebapp8675.azurewebsites.net    #/subscriptions/7cc654c6-760b-442f-bd02-1a8a64b17413/resourceGroups/myResourceGroup/providers/Microsoft.Network/privateEndpoints/mywebappendpoint
    ```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre Azure Private Link y DNS, vea [Configuración de DNS de punto de conexión privado de Azure](private-endpoint-dns.md).

Para obtener más información sobre Private Link, vea:

* [¿Qué es Azure Private Link?](private-link-overview.md)
* [¿Qué es el servicio Azure Private Link?](private-link-service-overview.md)
* [Preguntas frecuentes sobre Azure Private Link](private-link-faq.yml)