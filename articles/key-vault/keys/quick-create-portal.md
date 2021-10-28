---
title: 'Plantillas de inicio rápido de Azure: Establecimiento y recuperación de una clave de Key Vault mediante Azure Portal | Microsoft Docs'
description: Inicio rápido en el que se muestra cómo establecer y recuperar una clave de Azure Key Vault mediante Azure Portal
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: f7db6c26dbc80e104bd9fbd85468b54fc4877dec
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130217832"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-the-azure-portal"></a>Inicio rápido: Establecimiento y recuperación de una clave de Azure Key Vault mediante Azure Portal

Azure Key Vault es un servicio de almacenamiento seguro de secretos en la nube. Puede almacenar de forma segura claves, contraseñas, certificados y otros secretos. Las instancias de Azure Key Vault se pueden crear y administrar a través de Azure Portal. En este inicio rápido se crea un almacén de claves y se usa para almacenar una clave. Para más información sobre Key Vault, consulte esta [introducción](../general/overview.md).

## <a name="prerequisites"></a>Requisitos previos

Para acceder a Azure Key Vault, necesitará una suscripción de Azure. Si todavía no tiene una suscripción, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Todo acceso a los secretos tiene lugar a través de Azure Key Vault. Para este inicio rápido, cree un almacén de claves mediante [Azure Portal](../general/quick-create-portal.md), la [CLI de Azure](../general/quick-create-cli.md) o [Azure PowerShell](../general/quick-create-powershell.md).

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="add-a-key-to-key-vault"></a>Incorporación de una clave a Key Vault

Para agregar una clave al almacén, no tiene más que realizar un par de pasos adicionales. En este caso se ha agregado una clave que una aplicación podría usar. La clave se denomina **ExampleKey**.

1. En las páginas de propiedades de Key Vault, seleccione **Claves**.
2. Haga clic en **Generar o Importar**.
3. En la pantalla **Crear una clave**, elija los siguientes valores:
    - **Opciones**: Generar.
    - **Name**: ExampleKey.
    - Deje las restantes opciones con sus valores predeterminados. Haga clic en **Crear**.

Una vez recibido el mensaje de que la clave se ha creado correctamente, puede hacer clic en ella en la lista. A continuación, puede ver algunas de las propiedades. 

:::image type="content" source="../media/keys/quick-create-portal/current-version-hidden.png" alt-text="Propiedades de la clave":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de inicio rápido y tutoriales de Key Vault se basan en esta. Si tiene pensado seguir trabajando en otras guías de inicio rápido y tutoriales, considere la posibilidad de dejar estos recursos activos.
Cuando ya no lo necesite, elimine el grupo de recursos; de este modo se eliminarán también Key Vault y los recursos relacionados. Para eliminar el grupo de recursos mediante el portal:

1. Escriba el nombre del grupo de recursos en el cuadro de búsqueda de la parte superior del portal. Cuando vea el grupo de recursos que se utiliza en esta guía de inicio rápido en los resultados de búsqueda, selecciónelo.
2. Seleccione **Eliminar grupo de recursos**.
3. En el cuadro **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** escriba el nombre del grupo de recursos y seleccione **Eliminar**.


## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha creado una instancia de Key Vault y ha almacenado una clave en ella. Para más información sobre Key Vault y cómo integrarlo con las aplicaciones, continúe con los artículos siguientes.

- Lea una [introducción a Azure Key Vault](../general/overview.md).
- Consulte la [guía del desarrollador de Azure Key Vault](../general/developers-guide.md).
- Consulte [Introducción a la seguridad de Azure Key Vault](../general/security-features.md)