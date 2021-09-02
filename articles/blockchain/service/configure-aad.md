---
title: 'Configuración del acceso a Azure Active Directory: Azure Blockchain Service'
description: Configuración de Azure Blockchain Service con acceso a Azure Active Directory
ms.date: 05/11/2021
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: ea08a6cb59bdf5f8a18b05b897b8e6161c86f337
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2021
ms.locfileid: "122653690"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Configuración del acceso a Azure Active Directory para Azure Blockchain Service

En este artículo, aprenderá a conceder acceso y conectarse a los nodos de Azure Blockchain Service mediante los id. de aplicación, grupo o usuario de Azure Active Directory (Azure AD).

[!INCLUDE [Retirement note](./includes/retirement.md)]

Azure AD proporciona la administración de identidades basada en la nube y le permite usar una identidad única en toda una empresa y las aplicaciones de acceso en Azure. Azure Blockchain Service se integra con Azure AD y ofrece ventajas como el id. de federación, el inicio de sesión único y la autenticación multifactor.

## <a name="prerequisites"></a>Requisitos previos

* [Create a blockchain member using the Azure portal](create-member.md) (Creación de un miembro de cadena de bloques mediante Azure Portal)

## <a name="grant-access"></a>Conceder acceso

Puede conceder acceso en el nivel de miembro y el nivel de nodo. Conceder derechos de acceso en el nivel de miembro dará acceso a su vez a todos los nodos que se encuentran el miembro.

### <a name="grant-member-level-access"></a>Conceder acceso de nivel de miembro

Para conceder permiso de acceso en el nivel de miembro:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Navegue hasta **Control de acceso (IAM) > Agregar > Agregar asignación de roles**.
1. Seleccione el rol **Acceso a nodos de miembros de Blockchain (versión preliminar)**  y agregue el objeto de id. de Azure AD al que desea conceder acceso. El objeto de id. de Azure AD puede ser:

    | Objeto de Azure AD | Ejemplo |
    |-----------------|---------|
    | Usuario de Azure AD   | `kim@contoso.onmicrosoft.com` |
    | Grupo de Azure AD  | `sales@contoso.onmicrosoft.com` |
    | Identificador de aplicación  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Agregar asignación de roles](./media/configure-aad/add-role-assignment.png)

1. Seleccione **Guardar**.

### <a name="grant-node-level-access"></a>Conceder acceso de nivel de nodo

Para conceder acceso en el nivel de nodo, navegue hasta la seguridad de nodo y haga clic en el nombre de nodo al que desea conceder acceso.

Seleccione el rol Acceso a nodos de miembros de Blockchain (versión preliminar) y agregue el objeto de id. de Azure AD al que desea conceder acceso.

Para más información, consulte [Configurar los nodos de transacción de Azure Blockchain Service](configure-transaction-nodes.md#azure-active-directory-access-control).

## <a name="connect-using-azure-blockchain-connector"></a>Conectar mediante el conector de Azure Blockchain

Descargue o clone el [conector de Azure Blockchain desde GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Siga la sección de inicio rápido de **Léame** para crear el conector desde el código fuente.

### <a name="connect-using-an-azure-ad-user-account"></a>Conectar con una cuenta de usuario de Azure AD

1. Ejecute el siguiente comando para autenticarse con una cuenta de usuario de Azure AD. Reemplace \<myAADDirectory\> por un dominio de Azure AD. Por ejemplo, `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory>
    ```

1. Azure AD pide las credenciales.
1. Inicie sesión con su nombre de usuario y contraseña.
1. Tras una autenticación correcta, el proxy local se conecta al nodo de cadena de bloques. Ahora puede adjuntar el cliente Geth con el punto de conexión local.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Conectar mediante un id. de aplicación

Muchas aplicaciones se autentican con Azure AD mediante un id. de aplicación en lugar de una cuenta de usuario de Azure AD.

Para conectarse al nodo mediante un id. de aplicación, reemplace **aadauthcode** por **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parámetro | Descripción |
|-----------|-------------|
| tenant-id | Dominio de Azure AD, por ejemplo, `yourdomain.onmicrosoft.com`
| client-id | Id. de cliente de la aplicación registrada en Azure AD
| client-secret | Secreto de cliente de la aplicación registrada en Azure AD

Para obtener más información sobre cómo registrar una aplicación en Azure AD, consulte [Procedimiento: Uso del portal para crear una aplicación de Azure AD y una entidad de servicio con acceso a los recursos](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Conectar un dispositivo móvil o un explorador de texto

Para un dispositivo móvil o explorador basado en texto en los que no es posible la visualización emergente de la autenticación de Azure AD, Azure AD genera un código de acceso de un solo uso. Puede copiar el código de acceso y continuar con la autenticación de Azure AD en otro entorno.

Para generar el código de acceso, reemplace **aadauthcode** por **aaddevice**. Reemplace \<myAADDirectory\> por un dominio de Azure AD. Por ejemplo, `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la seguridad de datos en Azure Blockchain Service, consulte [Seguridad de Azure Blockchain Service](data-security.md).