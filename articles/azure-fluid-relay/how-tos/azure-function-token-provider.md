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
ms.openlocfilehash: 80524d6ab2da2e805e1107755cef4cfb367f6d2a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039456"
---
# <a name="how-to-write-a-tokenprovider-with-an-azure-function"></a>Procedimiento: Escritura de un TokenProvider con una función de Azure

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

En [Fluid Framework](https://fluidframework.com/), los proveedores de tokens son responsables de crear y firmar tokens que usa `@fluidframework/azure-client` para realizar solicitudes al servicio Azure Fluid Relay. Fluid Framework proporciona un proveedor de tokens sencillo y poco seguro con fines de desarrollo, llamado acertadamente **insecureTokenProvider**. Cada servicio de Fluid debe implementar un proveedor de tokens personalizado basado en las consideraciones particulares de seguridad y autenticación del servicio.

A cada inquilino del servicio Azure Fluid Relay que cree se le asigna un **identificador de inquilino** y su propia **clave secreta de inquilino** única. La clave secreta es un **secreto compartido**. La conocen la aplicación o el servicio y el servicio Azure Fluid Relay. Los proveedores de tokens deben conocer la clave secreta para firmar las solicitudes, pero no se puede incluir en el código de cliente.

## <a name="implement-an-azure-function-to-sign-tokens"></a>Implementación de una función de Azure para firmar los tokens

Una opción para crear un proveedor de tokens seguro es crear un punto de conexión HTTPS y una implementación de TokenProvider que realiza solicitudes HTTPS autenticadas a ese punto de conexión para recuperar tokens. Esto le permite almacenar la *clave secreta de inquilino* en una ubicación segura, como [Azure Key Vault](../../key-vault/general/overview.md).

La solución completa tiene dos partes:

1. Un punto de conexión HTTPS que acepta solicitudes y devuelve tokens de Azure Fluid Relay.
1. Una implementación de ITokenProvider que acepta una dirección URL a un punto de conexión y, a continuación, realiza solicitudes a ese punto de conexión para recuperar tokens.

### <a name="create-an-endpoint-for-your-tokenprovider-using-azure-functions"></a>Cree un punto de conexión para el TokenProvider mediante Azure Functions

[Azure Functions](../../azure-functions/functions-overview.md) es una manera rápida de crear este tipo de punto de conexión HTTPS. En el ejemplo siguiente se implementa ese patrón en una clase denominada **AzureFunctionTokenProvider**. Se acepta la dirección URL de la función de Azure, `userId` y `userName`. Esta implementación específica también se proporciona como exportación desde el paquete `@fluidframework/azure-client`.

En este ejemplo se muestra cómo crear su propia **función de Azure HTTPTrigger**, que captura el token pasando la clave de inquilino.

```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions";
import { ScopeType } from "@fluidframework/azure-client";
import { generateToken } from "@fluidframework/azure-service-utils";

// NOTE: retrieve the key from a secure location.
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
        return;
    }

    if (!key) {
        context.res = {
            status: 404,
            body: `No key found for the provided tenantId: ${tenantId}`,
        };
        return;
    }

    if (!documentId) {
        context.res = {
            status: 400,
            body: "No documentId provided in query params"
        };
        return;
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

La función `generateToken`, que se encuentra en el paquete `@fluidframework/azure-service-utils`, genera un token para el usuario en cuestión que se firma con la clave secreta del inquilino. Esto permite que el token se devuelva al cliente sin exponer el secreto. En su lugar, el token se genera en el servidor con el secreto a fin de proporcionar acceso limitado al documento especificado. El ITokenProvider de ejemplo que se muestra a continuación realiza solicitudes HTTP a esta función de Azure a fin de recuperar tokens.

### <a name="deploy-the-azure-function"></a>Implementación de la función de Azure

Es posible implementar Azure Functions de varias maneras. Consulte la sección de la [documentación de Azure Functions](../../azure-functions/functions-continuous-deployment.md) que trata sobre la **implementación** a fin de obtener más información sobre cómo implementar Azure Functions.

### <a name="implement-the-tokenprovider"></a>Implementación de TokenProvider

Un TokenProvider se puede implementar de varias maneras, pero es necesario implementar dos llamadas API independientes: `fetchOrdererToken` y `fetchStorageToken`. Estas API son responsables de capturar tokens para los servicios de orden y almacenamiento de Fluid, respectivamente. Ambas funciones devuelven objetos `TokenResponse` que representan el valor del token. El runtime de Fluid Framework llama a estas dos API según sea necesario para recuperar tokens.


Para tener la seguridad de que la clave secreta del inquilino se mantiene segura, se almacena en una ubicación de back-end protegida y solo es accesible desde la función de Azure. Para recuperar tokens, debe realizar una solicitud `GET` o `POST` a la función de Azure implementada y proporcionar los valores de `tenantID` y `documentId`, y `userID`/`userName`. La función de Azure es responsable de la asignación entre el identificador de inquilino y un secreto de clave de inquilino para generar y firmar correctamente el token.

En la implementación de ejemplo siguiente se utiliza la biblioteca [axios](https://www.npmjs.com/package/axios) para realizar solicitudes HTTP. Puede usar otras bibliotecas o enfoques para hacer una solicitud HTTP desde el código del servidor.

```typescript
import { ITokenProvider, ITokenResponse } from "@fluidframework/routerlicious-driver";
import axios from "axios";
import { AzureMember } from "./interfaces";

/**
 * Token Provider implementation for connecting to an Azure Function endpoint for
 * Azure Fluid Relay token resolution.
 */
export class AzureFunctionTokenProvider implements ITokenProvider {
    /**
     * Creates a new instance using configuration parameters.
     * @param azFunctionUrl - URL to Azure Function endpoint
     * @param user - User object
     */
    constructor(
        private readonly azFunctionUrl: string,
        private readonly user?: Pick<AzureMember, "userId" | "userName" | "additionalDetails">,
    ) { }

    public async fetchOrdererToken(tenantId: string, documentId?: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    public async fetchStorageToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    private async getToken(tenantId: string, documentId: string | undefined): Promise<string> {
        const response = await axios.get(this.azFunctionUrl, {
            params: {
                tenantId,
                documentId,
                userId: this.user?.userId,
                userName: this.user?.userName,
                additionalDetails: this.user?.additionalDetails,
            },
        });
        return response.data as string;
    }
}
```
## <a name="see-also"></a>Consulte también

- [Adición de datos personalizados a un token de autenticación](connect-fluid-azure-service.md#adding-custom-data-to-tokens)
- [Procedimiento: Implementación de aplicaciones de Fluid mediante Azure Static Web Apps](deploy-fluid-static-web-apps.md)
