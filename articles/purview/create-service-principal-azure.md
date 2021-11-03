---
title: Creación de una entidad de servicio en Azure
description: En este artículo se describe cómo crear una entidad de servicio en Azure.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d2be616d19ec862a7bf27d55f0a35ed1737fbf86
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093494"
---
# <a name="creating-a-service-principal"></a>Creación de una entidad de servicio

Puede crear una nueva o usar una entidad de servicio existente en el inquilino de Azure Active Directory.

## <a name="app-registration"></a>Registro de aplicación

1. Acceda a [Azure Portal](https://portal.azure.com).
2. Seleccione **Azure Active Directory** en el menú de la izquierda.
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-aad.png" alt-text="Captura de pantalla en la que se muestra el enlace a Azure Active Directory":::

3. Seleccione **Registros de aplicaciones** y **+ Nuevo registro**
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-new-reg.png" alt-text="Captura de pantalla en la que se muestra el enlace a Nuevo registro":::

4. Escriba un nombre para la **aplicación** (el nombre de la entidad de servicio).

5. Seleccione **Solo las cuentas de este directorio organizativo**.

6. En **URI de redirección**, seleccione **Web** y escriba cualquier dirección URL; no tiene que ser real ni estar en funcionamiento.

7. Después, seleccione **Register** (Registrar).
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-register.png" alt-text="Captura de pantalla en la que se muestran los detalles del nuevo registro de aplicación":::
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-new-app.png" alt-text="Captura de pantalla en la que se muestra la aplicación creada recientemente":::

## <a name="adding-a-secret-to-the-client-credentials"></a>Adición de un secreto a las credenciales de cliente

1. Seleccione la aplicación en **Registros de aplicaciones**
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-app-select.png" alt-text="Captura de pantalla en la que se muestra la aplicación que debe registrarse":::

2. Haga clic en **Agregar un certificado o secreto**
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-add-secret.png" alt-text="Captura de pantalla en la que se muestra la aplicación":::

3. Haga clic en **+ Nuevo secreto de cliente** en **Secretos de cliente**
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-new-client-secret.png" alt-text="Captura de pantalla en la que se muestra el menú de secretos de cliente":::

4. Proporcione una **descripción** y establezca la fecha de **expiración** del secreto :::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-secret-desc.png" alt-text="Captura de pantalla en la que se muestran los detalles del secreto de cliente":::
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-client-secret.png" alt-text="Captura de pantalla en la que se muestra el secreto del cliente":::

5. Copie el valor de **Credenciales de cliente** desde **Información general**
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-client-cred.png" alt-text="Captura de pantalla en la que se muestra la información general de la aplicación":::

## <a name="adding-the-secret-to-the-key-vault"></a>Adición del secreto al almacén de claves

1. Vaya a su **almacén de claves**
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-key-vault.png" alt-text="Captura de pantalla en la que se muestra el almacén de claves":::

2. Seleccione **Configuración** --> **Secretos** -->  **+ Generar o importar**.  
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-generate-secret.png" alt-text="Captura de pantalla en la que se muestran las opciones del almacén de claves":::

3. Introduzca el **nombre** que quiera y el **valor** como el **secreto de cliente** de su entidad de servicio.  
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-sp-secret.png" alt-text="Captura de pantalla en la que se muestra el almacén de claves para crear un secreto":::

4. Seleccione **Crear** para completar la acción.
