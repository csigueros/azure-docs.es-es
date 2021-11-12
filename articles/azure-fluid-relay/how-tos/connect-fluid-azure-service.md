---
title: 'Procedimiento: Conexión a un servicio Azure Fluid Relay'
description: 'Procedimiento: Conexión a un servicio Azure Fluid Relay mediante la biblioteca @fluidframework/azure-client.'
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/deployment/azure-frs/
ms.openlocfilehash: a2093b063e5c2e048bdf0a0a418a8046348032c8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849584"
---
# <a name="how-to-connect-to-an-azure-fluid-relay-service"></a>Procedimiento: Conexión a un servicio Azure Fluid Relay

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

Este artículo le guía por los pasos necesarios para aprovisionar el servicio Azure Fluid Relay y prepararlo para su uso. 

> [!IMPORTANT]
> Para poder conectar la aplicación a un servidor de Azure Fluid Relay, debe aprovisionar un recurso de [servidor de Azure Fluid Relay](provision-fluid-azure-portal.md) en su cuenta de Azure.

El servicio Azure Fluid Relay es un servicio de Fluid hospedado en la nube. Puede conectar la aplicación de Fluid a una instancia de Azure Fluid Relay mediante `AzureClient` en el paquete `@fluidframework/azure-client`. `AzureClient` controla la lógica de conectar el [contenedor de Fluid](https://fluidframework.com/docs/build/containers/) al servicio mientras se mantiene el propio objeto de contenedor independiente del servicio. Puede usar una instancia de este cliente para administrar varios contenedores.

En las secciones siguientes se explica cómo usar `AzureClient` en su propia aplicación.

## <a name="connecting-to-the-service"></a>Conexión al servicio

Para conectarse a una instancia de Azure Fluid Relay, primero debe crear un elemento `AzureClient`. Deberá proporcionar algunos parámetros de configuración, como el identificador de inquilino, las direcciones URL del nodo solicitante y de almacenamiento y un proveedor de tokens para generar el elemento JSON Web Token (JWT) que se usará para autorizar al usuario actual en el servicio. El paquete `@fluidframework/test-client-utils` proporciona un elemento `InsecureTokenProvider` que se puede usar con fines de desarrollo.

> [!CAUTION]
> `InsecureTokenProvider` solo se debe usar con fines de desarrollo, ya que **su uso expone el secreto de la clave del inquilino en la agrupación de código del lado cliente.** Este valor debe reemplazarse por una implementación de `ITokenProvider` que captura el token de su propio servicio back-end que es responsable de firmarlo con la clave del inquilino.

```javascript
const config = {
  tenantId: "myTenantId",
  tokenProvider: new InsecureTokenProvider("myTenantKey", { id: "userId" }),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};

const clientProps = {
  connection: config,
};

const client = new AzureClient(clientProps);
```

Ahora que tiene una instancia de `AzureClient`, puede empezar a usarlo para crear o cargar contenedores de Fluid.

### <a name="token-providers"></a>Proveedores de tokens

[AzureFunctionTokenProvider](https://github.com/microsoft/FluidFramework/blob/main/packages/framework/azure-client/src/AzureFunctionTokenProvider.ts) es una implementación de `ITokenProvider` que garantiza que el secreto de la clave del inquilino no se expone en el código de agrupación del lado cliente. `AzureFunctionTokenProvider` toma la dirección URL de la función de Azure anexada por `/api/GetAzureToken` junto con el objeto de usuario actual. Posteriormente, realiza una solicitud `GET` a la función de Azure pasando tenantId, documentId y userId/userName como parámetros opcionales.

```javascript
const config = {
  tenantId: "myTenantId",
  tokenProvider: new AzureFunctionTokenProvider(
    "myAzureFunctionUrl" + "/api/GetAzureToken",
    { userId: "userId", userName: "Test User" }
  ),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};

const clientProps = {
  connection: config,
};

const client = new AzureClient(clientProps);
```

### <a name="adding-custom-data-to-tokens"></a>Adición de datos personalizados a los tokens

El objeto de usuario también puede contener detalles de usuario adicionales que son opcionales. Por ejemplo:

```javascript
const userDetails = {
  email: "xyz@outlook.com",
  address: "Redmond",
};

const config = {
  tenantId: "myTenantId",
  tokenProvider: new AzureFunctionTokenProvider(
    "myAzureFunctionUrl" + "/api/GetAzureToken",
    { userId: "UserId", userName: "Test User", additionalDetails: userDetails }
  ),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};
```

La función de Azure generará el token para el usuario dado firmado con la clave secreta del inquilino y lo devolverá al cliente sin exponer el secreto propiamente dicho.

## <a name="managing-containers"></a>Administración de contenedores

La API `AzureClient` expone las funciones `createContainer` y `getContainer` para crear y obtener contenedores, respectivamente. Ambas funciones toman un _esquema de contenedor_ que define el modelo de datos del contenedor. Para la función `getContainer`, hay un parámetro adicional para el `id` del contenedor que le interesa capturar.

En el escenario de creación de contenedores, puede usar el siguiente patrón:

```javascript
const schema = {
  initialObjects: {
    /* ... */
  },
  dynamicObjectTypes: [
    /*...*/
  ],
};
const azureClient = new AzureClient(clientProps);
const { container, services } = await azureClient.createContainer(
  schema
);
const id = await container.attach();
```

La llamada `container.attach()` se produce cuando el contenedor se conecta realmente al servicio y se registra en su almacenamiento de blobs. Devuelve un `id` que es el identificador único de esta instancia de contenedor.

Cualquier cliente que quiera unirse a la misma sesión de colaboración debe llamar a `getContainer` con el mismo `id` de contenedor.

```javascript
const { container, services } = await azureClient.getContainer(
  id,
  schema
);
```

Para más información sobre cómo empezar a registrar las entradas emitidas por Fluid, consulte [Registro y telemetría](https://fluidframework.com/docs/testing/telemetry/).

El contenedor que se va a recuperar contendrá el elemento `initialObjects` definido en el esquema del contenedor. Consulte [Modelado de datos](https://fluidframework.com/docs/build/data-modeling/) para más información sobre cómo establecer el esquema y usar el objeto `container`.

## <a name="getting-audience-details"></a>Obtención de detalles de la audiencia

Las llamadas a `createContainer` y `getContainer` devuelven dos valores: un objeto `container`, descrito anteriormente, y un objeto `services`.

`container` contiene el modelo de datos de Fluid y es independiente del servicio. Cualquier código que escriba para este objeto de contenedor devuelto por `AzureClient` se puede reutilizar con el cliente en otro servicio. Por ejemplo, si ha creado un prototipo del escenario mediante `TinyliciousClient`, todo el código que interactúa con los objetos compartidos dentro del contenedor de Fluid se puede reutilizar usando `AzureClient`.

El objeto `services` contiene datos específicos del servicio Azure Fluid Relay. Este objeto contiene un valor `audience` que se puede usar para administrar la lista de usuarios que están conectados actualmente al contenedor.

El código siguiente muestra cómo puede usar el objeto `audience` para mantener una vista actualizada de todos los miembros que se encuentran actualmente en un contenedor.

```javascript
const { audience } = containerServices;
const audienceDiv = document.createElement("div");

const onAudienceChanged = () => {
  const members = audience.getMembers();
  const self = audience.getMyself();
  const memberStrings = [];
  const useAzure = process.env.FLUID_CLIENT === "azure";

  members.forEach((member) => {
    if (member.userId !== (self ? self.userId : "")) {
      if (useAzure) {
        const memberString = `${member.userName}: {Email: ${member.additionalDetails ? member.additionalDetails.email : ""},
                        Address: ${member.additionalDetails ? member.additionalDetails.address : ""}}`;
        memberStrings.push(memberString);
      } else {
        memberStrings.push(member.userName);
      }
    }
  });
  audienceDiv.innerHTML = `
            Current User: ${self ? self.userName : ""} <br />
            Other Users: ${memberStrings.join(", ")}
        `;
};

onAudienceChanged();
audience.on("membersChanged", onAudienceChanged);
```

`audience` proporciona dos funciones que devolverán objetos `AzureMember` que tienen un identificador y un nombre de usuario:

- `getMembers` devuelve un mapa de todos los usuarios conectados al contenedor. Estos valores cambiarán cada vez que un miembro se una al contenedor o lo abandone.
- `getMyself` devuelve el usuario actual en este cliente.

`audience` también emite eventos para cuando cambia la lista de miembros. `membersChanged` se activa con los cambios de lista, mientras que `memberAdded` y `memberRemoved` se activarán con sus respectivos cambios con los valores `clientId` y `member` que se han modificado. Después de que se activa cualquiera de estos eventos, una nueva llamada a `getMembers` devolverá la lista de miembros actualizada.

Un objeto `AzureMember` de ejemplo se parece al siguiente:

```json
{
  "userId": "0e662aca-9d7d-4ff0-8faf-9f8672b70f15",
  "userName": "Test User",
  "connections": [
    {
      "id": "c699c3d1-a4a0-4e9e-aeb4-b33b00544a71",
      "mode": "write"
    },
    {
      "id": "0e662aca-9d7d-4ff0-8faf-9f8672b70f15",
      "mode": "write"
    }
  ],
  "additionalDetails": {
    "email": "xyz@outlook.com",
    "address": "Redmond"
  }
}
```

Junto con el identificador de usuario, el nombre y los detalles adicionales, los objetos `AzureMember` también contienen una matriz de `connections`. Si el usuario ha iniciado la sesión con un solo cliente, `connections` solo contendrá un valor con el identificador del cliente y si está en modo de lectura y escritura. Sin embargo, si el mismo usuario ha iniciado sesión desde varios clientes (es decir, lo ha hecho desde distintos dispositivos o tiene varias pestañas del explorador abiertas con el mismo contenedor), `connections` aquí contendrá varios valores para cada cliente. En los datos de ejemplo anteriores, podemos ver que un usuario con el nombre "Usuario de prueba" y el identificador "0e662aca-9d7d-4ff0-8faf-9f8672b70f15" tiene actualmente abierto el contenedor de otros dos clientes. Los valores del campo `additionalDetails` coinciden con los valores proporcionados en la generación de tokens de `AzureFunctionTokenProvider`.

Estas funciones y eventos se pueden combinar para presentar una vista en tiempo real de los usuarios de la sesión actual.

**¡Enhorabuena!** Ahora ha conectado correctamente el contenedor de Fluid al servicio Azure Fluid Relay y ha recuperado los detalles de usuario de los miembros de la sesión de colaboración.
