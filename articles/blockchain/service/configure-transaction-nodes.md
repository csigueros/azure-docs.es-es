---
title: Configurar los nodos de transacción de Azure Blockchain Service
description: Cómo configurar los nodos de transacción de Azure Blockchain Service
ms.date: 05/11/2021
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 436c7721bac29e8a18a333e385f12a70e0701ba5
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2021
ms.locfileid: "122652155"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Configurar los nodos de transacción de Azure Blockchain Service

Los nodos de transacciones se usan para enviar transacciones de cadenas de bloques a Azure Blockchain Service a través de un punto de conexión público. El nodo de transacciones predeterminado contiene la clave privada de la cuenta de Ethereum registrada en la cadena de bloques y, por lo tanto, no se puede eliminar.

[!INCLUDE [Retirement note](./includes/retirement.md)]

Para ver los detalles del nodo de transacción predeterminado:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Vaya al miembro de Azure Blockchain Service. Seleccione **Nodos de transacción**.

    ![Selección del nodo de transacción predeterminado](./media/configure-transaction-nodes/nodes.png)

    Los detalles de información general incluyen las direcciones de punto de conexión público y la clave pública.

## <a name="create-transaction-node"></a>Creación del nodo de transacción

Puede agregar hasta nueve nodos adicionales de transacción al miembro de la cadena de bloques, para un total de 10 nodos de transacción. Al agregar nodos de transacción, puede aumentar la escalabilidad o distribuir la carga. Por ejemplo, podría tener un punto de conexión del nodo de transacción para diferentes aplicaciones cliente.

Para agregar un nodo de transacción:

1. En Azure Portal, vaya al miembro de Azure Blockchain Service y seleccione **Nodos de transacción > Agregar**.
1. Realice la configuración del nuevo nodo de transacción.

    ![Agregar nodos de transacción](./media/configure-transaction-nodes/add-node.png)

    | Configuración | Descripción |
    |---------|-------------|
    | Nombre | Nombre del nodo de transacción. El nombre se usa para crear la dirección DNS del punto de conexión del nodo de transacción. Por ejemplo, `newnode-myblockchainmember.blockchain.azure.com`. No se puede cambiar el nombre del nodo una vez creado. |
    | Contraseña | Establezca una contraseña segura. Use la contraseña para acceder al punto de conexión del nodo de transacción con autenticación básica.

1. Seleccione **Crear**.

    El aprovisionamiento de un nuevo nodo de transacción tarda unos 10 minutos. Los nodos de transacción adicionales generan costes. Para obtener más información sobre los costes, consulte [Precios de Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Puntos de conexión

Los nodos de transacción tienen un nombre DNS único y puntos de conexión públicos.

Para ver los detalles del punto de conexión de un nodo de transacción:

1. En Azure Portal, vaya a uno de los nodos de transacción del miembro de Azure Blockchain Service y seleccione **Información general**.

    ![Captura de pantalla que muestra la información general de los nodos de transacción para un miembro de cadena de bloques.](./media/configure-transaction-nodes/endpoints.png)

Los puntos de conexión del nodo de transacción son seguros y requieren autenticación. Puede conectarse a un punto de conexión de transacción mediante la autenticación de Azure AD, la autenticación básica HTTPS y mediante una clave de acceso a través de HTTPS o WebSocket a través de TLS.

### <a name="azure-active-directory-access-control"></a>Control de acceso de Azure Active Directory

Los puntos de conexión del nodo de transacción de Azure Blockchain Service admiten la autenticación de Azure Active Directory (Azure AD). Puede conceder al usuario, grupo y entidad de servicio de Azure AD acceso al punto de conexión.

Para conceder control de acceso de Azure AD al punto de conexión:

1. En Azure Portal, vaya al miembro de Azure Blockchain Service y seleccione **Nodos de transacción > Control de acceso (IAM) > Agregar > Agregar asignación de roles**.
1. Cree una nueva asignación de roles para un usuario, grupo o entidad de servicio (roles de aplicación).

    ![Agregar rol de IAM](./media/configure-transaction-nodes/add-role.png)

    | Configuración | Acción |
    |---------|-------------|
    | Role | Seleccione **Propietario**, **Colaborador** o **Lector**.
    | Asignar acceso a | Seleccione **Usuario, grupo o entidad de servicio de Azure AD**.
    | Seleccionar | Busque el usuario, grupo o entidad de servicio que quiere agregar.

1. Seleccione **Guardar** para agregar la asignación de roles.

Para más información sobre el control de acceso de Azure AD, consulte [Asignación de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-portal.md).

Para obtener más información sobre cómo conectarse mediante la autenticación de Azure AD, consulte [connect to your node using AAD authentication](configure-aad.md) (Conectarse al nodo mediante la autenticación de AAD).

### <a name="basic-authentication"></a>Autenticación básica

Para la autenticación básica HTTPS, las credenciales de nombre y contraseña del usuario se pasan en el encabezado HTTPS de la solicitud al punto de conexión.

Puede ver los detalles del punto de conexión de autenticación básica de un nodo de transacción en Azure Portal. Vaya a uno de los nodos de transacción del miembro de Azure Blockchain Service y seleccione **Autenticación básica** en la configuración.

![Autenticación básica](./media/configure-transaction-nodes/basic.png)

El nombre de usuario es el nombre del nodo y no se puede cambiar.

Para usar la dirección URL, reemplace \<password\> por la contraseña establecida cuando se aprovisionó el nodo. Para actualizar la contraseña, seleccione **Restablecer contraseña**.

### <a name="access-keys"></a>Claves de acceso

Para la autenticación de clave de acceso, la clave de acceso se incluye en la dirección URL del punto de conexión. Cuando se aprovisiona el nodo de transacción, se generan dos claves de acceso. Se puede usar cualquier clave de acceso para la autenticación. Dos claves le permiten cambiar y girar claves.

Puede ver los detalles de la clave de acceso de un nodo de transacción y copiar las direcciones del punto de conexión que incluyen las claves de acceso. Vaya a uno de los nodos de transacción del miembro de Azure Blockchain Service y seleccione **Claves de acceso** en la configuración.

### <a name="firewall-rules"></a>Reglas de firewall

Las reglas de firewall permiten limitar las direcciones IP que pueden intentar autenticarse en el nodo de transacción.  Si no hay reglas de firewall configuradas para el nodo de transacción, ninguna entidad puede acceder a él.

Para ver las reglas de firewall de un nodo de transacción, vaya a uno de los nodos de transacción del miembro de Azure Blockchain Service y seleccione **Reglas de firewall** en la configuración.

Puede agregar reglas de firewall mediante la especificación de un nombre de regla, una dirección IP inicial y una dirección IP final en la cuadrícula **Reglas de firewall**.

![Reglas de firewall](./media/configure-transaction-nodes/firewall-rules.png)

Para habilitar las siguientes opciones:

* **Dirección IP única:** Configure la misma dirección IP para las direcciones IP iniciales y finales.
* **Intervalo de direcciones IP:** Configure el intervalo de direcciones IP iniciales y finales. Por ejemplo, un intervalo que empieza en 10.221.34.0 y termina en 10.221.34.255 habilita la subred 10.221.34.xxx completa.
* **Permitir todas las direcciones IP:** Configure la dirección IP inicial en 0.0.0.0 y la dirección IP final en 255.255.255.255.

## <a name="connection-strings"></a>Cadenas de conexión

El sintaxis de la cadena de conexión para el nodo de transacción se proporcionan para la autenticación básica o mediante claves de acceso. Se proporcionan cadenas de conexión, incluidas las claves de acceso a través de HTTPS y WebSockets.

Puede ver las cadenas de conexión de un nodo de transacción y copiar las direcciones de punto de conexión. Vaya a uno de los nodos de transacción del miembro de Azure Blockchain Service y seleccione **Cadenas de conexión** en la configuración.

![Cadenas de conexión](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Código de ejemplo

El código de ejemplo se proporciona para habilitar rápidamente la conexión al nodo de transacción a través de Web3, Nethereum, Web3js y Truffle.

Puede ver el código de conexión de ejemplo de un nodo de transacción y copiarlo para su uso con herramientas de desarrollo populares. Vaya a uno de los nodos de transacción del miembro de Azure Blockchain Service y seleccione **Código de ejemplo** en la configuración.

Elija la pestaña Web3, Nethereum, Truffle o Web3j para ver el código de ejemplo que quiere usar.

![Código de ejemplo](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configuración de nodos de transacción mediante la CLI de Azure](manage-cli.md)
