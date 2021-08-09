---
title: Protección de secretos de autenticación en Azure Key Vault
description: Use la identidad administrada para proteger los secretos de autenticación en Azure Key Vault.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/17/2021
ms.author: cshoe
ms.openlocfilehash: cc0ced1a6c91bf2e7960e638c295d33a45db135e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110073349"
---
# <a name="securing-authentication-secrets-in-azure-key-vault"></a>Protección de secretos de autenticación en Azure Key Vault

Al configurar proveedores de autenticación personalizados, es posible que desee almacenar los secretos de conexión en Azure Key Vault. En este artículo se muestra cómo utilizar una identidad administrada para conceder a Azure Static Web Apps acceso a Key Vault para los secretos de autenticación personalizados.

Los secretos de seguridad requieren la aplicación de los siguientes elementos.

- Crear una identidad asignada por el sistema en la instancia de Static Web Apps.
- Conceder a un secreto de Key Vault acceso a la identidad.
- Hacer referencia al secreto de Key Vault desde la configuración de la aplicación de Static Web Apps.

En este artículo se muestra cómo configurar cada uno de estos elementos en la aplicación.

## <a name="prerequisites"></a>Requisitos previos

- Tener ya un sitio de Azure Static Web Apps.
- Tener ya un recurso de Key Vault con un valor secreto.

## <a name="create-identity"></a>Creación de la identidad

1. Abra el sitio Static Web Apps en Azure Portal.

1. En el menú _Configuración_, seleccione **Identidad**.

1. Vaya a la pestaña **Asignado por el sistema**.

1. En la etiqueta _Estado_, seleccione el botón **Activar**.

1. Seleccione el botón **Guardar**.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-enable-managed-identity.png" alt-text="Adición de una identidad asignada por el sistema":::

1. Cuando aparezca el cuadro de diálogo de confirmación, seleccione el botón **Sí**.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-enable-managed-identity-confirmation.png" alt-text="Confirmación de la asignación de la identidad":::

Ahora puede agregar una directiva de acceso para permitir que la aplicación web estática lea secretos de Key Vault.

## <a name="add-a-key-vault-access-policy"></a>Adición de una directiva de acceso de Key Vault

1. Abra el recurso Key Vault en Azure Portal.

1. En el menú _Configuración_, seleccione **Directivas de acceso**.

1. Seleccione el vínculo **Agregar directiva de acceso**.

1. En la lista desplegable _Permisos de secretos_, seleccione **Obtener**.

1. Junto a la etiqueta _Seleccionar entidad de seguridad_, seleccione el vínculo **Ninguno seleccionado**.

1. En el cuadro de búsqueda, busque el nombre de aplicación Static Web Apps.

1. Seleccione el elemento de lista que coincida con el nombre de la aplicación.

1. Seleccione el botón **Seleccionar**.

1. Seleccione el botón **Agregar**.

1. Seleccione el botón **Guardar**.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-key-vault-save-policy.png" alt-text="Guardado de la directiva de acceso de Azure Key Vault":::

La directiva de acceso ahora se guarda en Key Vault. A continuación, acceda al URI del secreto para usarlo al asociar la aplicación web estática al recurso Key Vault.

1. En el menú _Configuración_, seleccione **Secretos**.

1. Seleccione en la lista el secreto deseado.

1. Seleccione en la lista la versión de secreto deseada.

1. Seleccione el **botón Copiar** al final del cuadro de texto _Identificador secreto_ para copiar el valor del URI del secreto en el Portapapeles.

1. Pegue el valor en un editor de texto para usarlo posteriormente.

## <a name="add-application-setting"></a>Adición de configuración de la aplicación

1. Abra el sitio Static Web Apps en Azure Portal.

1. En el menú _Configuración_, seleccione **Configuración**.

1. En la sección _Configuración de la aplicación_, seleccione el botón **Agregar**.

1. Escriba un nombre en el cuadro de texto para el campo _Nombre_.

1. Determine el valor del secreto en el cuadro de texto del campo _Valor_.

    El valor del secreto es una composición de algunos valores diferentes. En la plantilla siguiente se muestra cómo se crea la cadena final.

    ```text
    @Microsoft.KeyVault(SecretUri=<YOUR-KEY-VAULT-SECRET-URI>)
    ```

    Siga estos pasos para compilar el valor de secreto completo.

1. Copie la plantilla de arriba y péguela en un editor de texto.

1. Reemplace `<YOUR-KEY-VAULT-SECRET-URI>` por el valor del URI de Key Vault que reservó anteriormente.

1. Copie el nuevo valor de cadena completa.

1. Pegue el valor en el cuadro de texto del campo _Valor_.

1. Seleccione el botón **Aceptar**.

1. Seleccione el botón **Guardar** que encontrará en la parte superior de la barra de herramientas _Configuración de aplicación_.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-application-settings-save.png" alt-text="Guardado de la configuración de la aplicación":::

Ahora, cuando la configuración de autenticación personalizada hace referencia a la configuración de la aplicación recién creada, el valor se extrae de Azure Key Vault mediante la identidad de la aplicación web estática.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Autenticación personalizada](./authentication-custom.md)
