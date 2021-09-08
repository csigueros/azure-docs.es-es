---
title: 'Creación de un prefijo de una dirección IP pública: Azure Portal'
titlesuffix: Azure Virtual Network
description: Aprenda a crear un prefijo de dirección IP pública mediante Azure Portal.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/06/2021
ms.author: allensu
ms.openlocfilehash: 3a1dfefb034816038af4401a22ee87f7f1914c54
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598458"
---
# <a name="create-a-public-ip-address-prefix-using-the-azure-portal"></a>Creación de un prefijo de dirección IP pública mediante Azure Portal

Obtenga información sobre el prefijo de una dirección IP pública y cómo crearlo, modificarlo y eliminarlo. Un prefijo de dirección IP pública es un rango continuo de direcciones IP públicas de SKU estándar. 

Al crear un recurso de dirección IP pública, puede asignar una dirección IP pública estática a partir del prefijo y asociar la dirección a máquinas virtuales, equilibradores de carga u otros recursos. Para más información, consulte [Prefijo de dirección IP pública](public-ip-address-prefix.md).

## <a name="prerequisites"></a>Requisitos previos

- Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).

## <a name="create-a-public-ip-address-prefix"></a>Creación del prefijo de una dirección IP pública

En esta sección, creará el prefijo de IP pública en Azure Portal.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **IP pública**.

3. En los resultados de la búsqueda, seleccione **Prefijos de direcciones IP públicas**.

4. Seleccione **+ Create** (+ Crear).

5. En **Crear un prefijo de dirección IP pública**, escriba o seleccione los valores siguientes en la pestaña **Datos básicos**:

    | Configuración | Value |
    | ------- | ----- |
    | **Detalles del proyecto** |   |
    | Suscripción | Seleccione su suscripción. |
    | Resource group | Seleccione **Crear nuevo**. </br> Escriba **myResourceGroup**. </br> Seleccione **Aceptar**. |
    | **Detalles de instancia** |   |
    | Nombre | Escriba **myPublicIPprefix**. |
    | Region | Seleccione **(EE. UU.) Oeste de EE. UU. 2**. |
    | Versión de la dirección IP | Deje el valor predeterminado, **IPv4**. |
    | Tamaño del prefijo | Seleccione el tamaño del prefijo. |

    :::image type="content" source="./media/create-public-ip-prefix-portal/create-public-ip-prefix.png" alt-text="Crear un prefijo de una dirección IP pública en Azure Portal" border="true":::
    
    > [!NOTE]
    >Para crear un prefijo de IPv6, elija **IPv6** en **Version de IP**.

     :::image type="content" source="./media/create-public-ip-prefix-portal/create-public-ipv6-prefix.png" alt-text="Crear un prefijo de una dirección IPv6 pública en Azure Portal" border="true":::

6. Seleccione la pestaña **Revisar y crear** o el botón azul **Revisar y crear** en la parte inferior de la página.

7. Seleccione **Crear**.

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Creación de una dirección IP pública estática a partir de un prefijo
Una vez que cree un prefijo, debe crear las direcciones IP estáticas a partir del prefijo. En esta sección, creará una dirección IP estática a partir del prefijo que ha creado antes.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **IP pública**.

2. En los resultados de la búsqueda, seleccione **Prefijos de direcciones IP públicas**.

3. En **Prefijos de direcciones IP públicas**, seleccione **myPublicIPPrefix**.

4. En **Información general** de **myPublicIPPrefix**, seleccione **+ Agregar dirección IP**.

    :::image type="content" source="./media/create-public-ip-prefix-portal/add-ip-address.png" alt-text="Crear una dirección IP pública desde un prefijo en Azure Portal" border="true":::

5. Escriba **myPublicIP** en **Nombre**. 

6. Deje los valores predeterminados en las restantes opciones.

7. Seleccione **Agregar**.

    >[!NOTE]
    >Solo se pueden asignar direcciones IP públicas estáticas con la SKU estándar desde el intervalo del prefijo. Para más información sobre las SKU de direcciones IP públicas, consulte [Direcciones IP publicas](./public-ip-addresses.md#public-ip-addresses).

8. Seleccione **Direcciones IP públicas** en **Configuración** para ver la dirección IP creada.
## <a name="delete-a-prefix"></a>Eliminación de un prefijo

En esta sección, aprenderá a ver o eliminar un prefijo.

1. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **IP pública**.

2. En los resultados de la búsqueda, seleccione **Prefijos de direcciones IP públicas**.

3. En **Prefijos de direcciones IP públicas**, seleccione **myPublicIPPrefix**.

4. Seleccione **Eliminar** en la sección **Información general**.

    >[!NOTE]
    >Si las direcciones dentro del prefijo están asociadas a recursos de dirección IP pública, primero debe eliminar dichos recursos de dirección IP pública. Consulte [Eliminar una dirección IP pública](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

## <a name="clean-up-resources"></a>Limpieza de recursos

En este artículo, ha creado un prefijo de IP pública y una IP pública a partir de ese prefijo. 

Cuando haya terminado con el prefijo de dirección IP pública, elimine el grupo de recursos y todos los recursos que contiene:

1. Busque y seleccione **myResourceGroup**.

2. Seleccione **Eliminar grupo de recursos**.

3. Escriba **myResourceGroup** para **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS** y seleccione **Eliminar**.
## <a name="next-steps"></a>Pasos siguientes

- Obtener información sobre los escenarios y las ventajas de usar un [prefijo de IP pública](public-ip-address-prefix.md)
