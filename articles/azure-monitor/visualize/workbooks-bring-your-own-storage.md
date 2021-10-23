---
title: Aportación del almacenamiento propio para los libros de Azure Monitor
description: Aprenda a guardar el contenido de los libros en su propio almacenamiento para protegerlos.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: 5dc75b3f28198bd3686df48b1d46cb5a8e192a23
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132519"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>Aportación del almacenamiento propio para guardar los libros

Hay ocasiones en las que puede tener una consulta o lógica de negocios que desea proteger. Los libros ofrecen una opción para proteger el contenido de los mismos guardándolo en su almacenamiento. Posteriormente, la cuenta de almacenamiento se puede cifrar con claves administradas por Microsoft o puede administrar el cifrado proporcionando sus propias claves. [Consulte la documentación de Azure sobre Storage Service Encryption.](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>Guardar libros con identidades administradas

1. Antes de que pueda guardar el libro en el almacenamiento, deberá crear una identidad administrada (Todos los servicios -> Identidades administradas) y darle acceso de `Storage Blob Data Contributor` a su cuenta de almacenamiento. [Consulte la documentación de Azure sobre identidades administradas.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![Captura de pantalla que muestra cómo agregar una asignación de roles](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. Crear un libro nuevo.
3. Seleccione el botón **Guardar** para guardar el libro.
4. Hay una opción para `Save content to an Azure Storage Account`, seleccione la casilla para guardar en una cuenta de Azure Storage.

    ![Captura de pantalla que muestra el cuadro de diálogo guardado](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. Seleccione la cuenta de almacenamiento y el contenedor que desee. La lista de cuentas de almacenamiento es de la suscripción seleccionada anteriormente.

    ![Captura de pantalla que muestra un cuadro de diálogo Guardar con la opción de almacenamiento](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. A continuación, elija **Cambiar** para seleccionar una identidad administrada creada anteriormente.

    [![Captura de pantalla que muestra el cuadro de diálogo Cambiar identidad](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. Después de seleccionar las opciones de almacenamiento, presione **Guardar** para guardar el libro.

## <a name="limitations"></a>Limitaciones

- Al guardar en el almacenamiento personalizado, no se pueden anclar partes individuales del libro en un panel, ya que los elementos anclados individuales contendrían información protegida en el panel. Cuando se usa el almacenamiento personalizado, solo se pueden anclar vínculos al libro en los paneles.
- Una vez que un libro se ha guardado en el almacenamiento personalizado, siempre se guardará allí y esto no se podrá desactivar. Para guardarlo en otro lugar, puede usar la opción "Guardar como" y elegir no guardar la copia en el almacenamiento personalizado.
- Los libros del recurso Application Insights son libros "heredados" que no admiten el almacenamiento personalizado. Los libros más recientes del recurso Application Insights están en la selección "...Más". Los libros heredados no tienen opciones de suscripción al guardar.

   ![Captura de pantalla que muestra el libro heredado](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a crear una visualización de [Mapa](workbooks-map-visualizations.md) en los libros.
- Aprenda a usar [grupos en libros](../visualize/workbooks-groups.md).