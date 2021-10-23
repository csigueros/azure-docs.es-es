---
title: 'Procedimiento: Escritura de un TokenProvider con una función de Azure'
description: Cómo escribir un proveedor de tokens personalizado como una función de Azure e implementarlo.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/tokenproviders/
ms.openlocfilehash: d6987b4e4592167fcb41a7f6654ff46140a79724
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661667"
---
# <a name="how-to-write-a-tokenprovider-with-an-azure-function"></a>Procedimiento: Escritura de un TokenProvider con una función de Azure

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

En [Fluid Framework](https://fluidframework.com/), los proveedores de tokens son responsables de crear y firmar tokens que usa `@fluidframework/azure-client` para realizar solicitudes al servicio Azure Fluid Relay. Fluid Framework proporciona un proveedor de tokens sencillo y poco seguro con fines de desarrollo, llamado acertadamente **insecureTokenProvider**. Cada servicio de Fluid debe implementar un proveedor de tokens personalizado basado en las consideraciones particulares de seguridad y autenticación del servicio.

## <a name="implementing-your-own-tokenprovider-class"></a>Implementación de su propia clase TokenProvider

A cada inquilino del servicio Azure Fluid Relay que cree se le asigna un **identificador de inquilino** y su propia **clave secreta de inquilino** única. La clave secreta es un **secreto compartido**. La conocen la aplicación o el servicio y el servicio Azure Fluid Relay. 

Los proveedores de tokens deben conocer la clave secreta para firmar las solicitudes, pero no se puede incluir en el código de cliente. Los proveedores de tokens se ponen en contacto con el servidor de Fluid en tiempo de ejecución para obtener de forma segura la clave secreta sin exponerla al cliente. Esto se logra mediante dos llamadas API independientes:`fetchOrdererToken` y `fetchStorageToken`. Estas llamadas son responsables de capturar las direcciones URL del nodo solicitante y de almacenamiento del host, respectivamente. Ambas funciones devuelven objetos `TokenResponse` que representan el valor del token.

## <a name="tokenprovider-class-example"></a>Ejemplo de clase TokenProvider

Una opción para crear un proveedor de tokens seguro es crear una función de Azure sin servidor y exponerla como proveedor de tokens. Esto le permite almacenar la *clave secreta de inquilino* en un servidor seguro. A continuación, la aplicación llamaría a la función de Azure para generar tokens.

En este ejemplo se implementa ese patrón en una clase denominada **AzureFunctionTokenProvider**. Se acepta la dirección URL de la función de Azure, `userId` y `userName`. Esta implementación específica también se proporciona como exportación desde el paquete `@fluidframework/azure-client`.

```typescript
import { ITokenProvider, ITokenResponse } from "@fluidframework/azure-client";

export class AzureFunctionTokenProvider implements ITokenProvider {
  constructor(
    private readonly azFunctionUrl: string,
    private readonly userId: string,
    private readonly userName: string,
  );

  public async fetchOrdererToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    public async fetchStorageToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }
}
```

Para tener la seguridad de que la clave secreta del inquilino se mantiene segura, se almacena en una ubicación de back-end protegida y solo es accesible desde la función de Azure. Una manera de capturar un token firmado es realizar una solicitud `GET` a la función de Azure y proporcionar `tenantID`, `documentId`, `userID`, / y `userName`. La función de Azure es responsable de la asignación entre el identificador de inquilino y un secreto de clave de inquilino para generar y firmar correctamente el token de modo que el servicio Azure Fluid Relay lo acepte.

```typescript
private async getToken(tenantId: string, documentId: string): Promise<string> {
    const params = {
        tenantId,
        documentId,
        userId: this.userId,
        userName: this.userName,
    };
    const token = this.getTokenFromServer(params);
    return token;
}
```

En el ejemplo siguiente se usa la biblioteca [`axios`](https://www.npmjs.com/package/axios) para realizar solicitudes HTTP. Puede usar otras bibliotecas o enfoques para ello.

```typescript
private async getTokenFromServer(input: any): Promise<string> {
    return axios.get(this.azFunctionUrl, {
        params: input,
    }).then((response) => {
        return response.data as string;
    }).catch((err) => {
        return err as string;
    });
}
```

En este ejemplo se muestra cómo crear su propia **función de Azure HTTPTrigger**, que captura el token pasando la clave de inquilino.

```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions";
import { ScopeType } from "@fluidframework/azure-client";
import { generateToken } from "@fluidframework/azure-service-utils";

//Replace "myTenantKey" with your key here.
const key = "myTenantKey";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    // tenantId, documentId, userId and userName are required parameters
    const tenantId = (req.query.tenantId || (req.body && req.body.tenantId)) as string;
    const documentId = (req.query.documentId || (req.body && req.body.documentId)) as string;
    const userId = (req.query.userId || (req.body && req.body.userId)) as string;
    const userName = (req.query.userName || (req.body && req.body.userName)) as string;
    const scopes = (req.query.scopes || (req.body && req.body.scopes)) as ScopeType[];

    if (!tenantId) {
        context.res = {
            status: 400,
            body: "No tenantId provided in query params",
        };
    }

    if (!key) {
        context.res = {
            status: 404,
            body: `No key found for the provided tenantId: ${tenantId}`,
        };
    }

    if (!documentId) {
        context.res = {
            status: 400,
            body: "No documentId provided in query params"
        };
    }

    let user = { name: userName, id: userId };

    // Will generate the token and returned by an ITokenProvider implementation to use with the AzureClient.
    const token = generateToken(
        tenantId,
        documentId,
        key,
        scopes ?? [ScopeType.DocRead, ScopeType.DocWrite, ScopeType.SummaryWrite],
        user
    );

    context.res = {
        status: 200,
        body: token
    };
};

export default httpTrigger;
```

La función `generateToken` genera un token para el usuario en cuestión que se firma con la clave secreta del inquilino. De este modo, el token se devuelve al cliente sin que se le exponga nunca el propio secreto. En su lugar, el token se genera con él para proporcionar acceso limitado al documento especificado. Este token se puede devolver mediante una implementación de `ITokenProvider` para su uso con `AzureClient`.

## <a name="see-also"></a>Consulte también

- [Adición de datos personalizados a un token de autenticación](connect-fluid-azure-service.md#adding-custom-data-to-tokens)
