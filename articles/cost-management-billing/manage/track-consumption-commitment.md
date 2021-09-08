---
title: Seguimiento del compromiso de consumo de Microsoft Azure (MACC)
description: Obtenga información sobre cómo realizar un seguimiento del compromiso de consumo de Microsoft Azure (MACC) de un Contrato de cliente de Microsoft.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 07/06/2021
ms.author: banders
ms.openlocfilehash: 41a4f22e669300e24ddce0248e8ab1744b773202
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/06/2021
ms.locfileid: "113301931"
---
# <a name="track-your-microsoft-azure-consumption-commitment-macc"></a>Seguimiento del compromiso de consumo de Microsoft Azure (MACC)

El compromiso de consumo de Microsoft Azure (MACC) es un compromiso contractual que su organización puede haber realizado para Microsoft Azure a lo largo del tiempo. Si su organización tiene un MACC para una cuenta de facturación de Contrato de cliente de Microsoft (MCA), puede comprobar aspectos importantes de su compromiso, incluidas las fechas de inicio y finalización, el compromiso restante y el gasto válido en Azure Portal o a través de las API REST. Las cuentas de facturación de MACC o CTC para Contratos Enterprise (EA) aún no están disponibles en Azure Portal o mediante las API REST.

## <a name="track-your-macc-commitment"></a>Seguimiento del compromiso MACC

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Busque **Administración de costos + facturación**.  
    :::image type="content" source="./media/track-consumption-commitment/billing-search-cost-management-billing.png" alt-text="Captura de pantalla que muestra la búsqueda en el portal de Cost Management + Billing." lightbox="./media/track-consumption-commitment/billing-search-cost-management-billing.png" :::
3. En la página de ámbitos de facturación, seleccione la cuenta de facturación para la que desea realizar el seguimiento del compromiso. La cuenta de facturación será del tipo **Contrato de cliente de Microsoft**.  
    :::image type="content" source="./media/track-consumption-commitment/list-of-scopes.png" alt-text="Captura de pantalla que muestra los ámbitos de facturación." lightbox="./media/track-consumption-commitment/list-of-scopes.png" :::
    > [!NOTE]
     > Azure Portal recuerda el último ámbito de facturación al que tiene acceso y muestra el ámbito la próxima vez que llega a la página Administración de costos + facturación. No verá la página de ámbitos de facturación si ha visitado Administración de costos y facturación anteriormente. Si es así, compruebe que se encuentra en el [ámbito correcto](#check-access-to-a-microsoft-customer-agreement). En caso contrario, [cambie el ámbito](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) para seleccionar la cuenta de facturación de un Contrato de cliente de Microsoft.
4. Seleccione **Propiedades** en el lado izquierdo y después seleccione **Compromiso de consumo de Microsoft Azure (MACC)** .  
    :::image type="content" source="./media/track-consumption-commitment/select-macc-tab.png" alt-text="Captura de pantalla que muestra la selección de la pestaña MACC." lightbox="./media/track-consumption-commitment/select-macc-tab.png" :::
5. La pestaña Compromiso de consumo de Microsoft Azure (MACC) tiene las secciones siguientes.

#### <a name="remaining-commitment"></a>Compromiso restante 

El compromiso restante muestra el importe restante del compromiso desde la última factura.

:::image type="content" source="./media/track-consumption-commitment/macc-remaining-commitment.png" alt-text="Captura de pantalla del compromiso restante para un MACC." lightbox="./media/track-consumption-commitment/macc-remaining-commitment.png" :::

#### <a name="details"></a>Detalles

En la sección Detalles se muestran otros aspectos importantes del compromiso.

:::image type="content" source="./media/track-consumption-commitment/macc-details.png" alt-text="Captura de pantalla de los detalles del MACC." lightbox="./media/track-consumption-commitment/macc-details.png" :::

| Término | Definición |
|---|---|
| ID | El identificador único de su MACC. Este identificador se usa para obtener la información del MACC a través de las API REST. |
| Fecha de compra | Fecha en que realizó el compromiso. |
| Fecha de inicio | Fecha en que el compromiso se hizo efectivo. |
| Fecha de finalización | Fecha en que expiró el compromiso. |
| Importe del compromiso | La cantidad que se ha comprometido a gastar en productos o servicios aptos para MACC. |
| Status | El estado de su compromiso. |

Su MACC puede tener uno de los siguientes valores:

- Activo: el MACC está activo. Cualquier gasto apto contribuirá a su compromiso de MACC.
- Completado: ha completado el compromiso de MACC. 
- Expirado: el MACC ha expirado. Póngase en contacto con el equipo de cuentas de Microsoft. 
- Cancelado: el MACC se ha cancelado. El nuevo gasto de Azure no contribuirá al compromiso del MACC.

#### <a name="events"></a>Eventos

En la sección Eventos se muestran los eventos (gasto facturado) que disminuyeron el compromiso de MACC.

:::image type="content" source="./media/track-consumption-commitment/macc-events.png" alt-text="Captura de pantalla de los eventos del MACC." lightbox="./media/track-consumption-commitment/macc-events.png" :::

| Término | Definición |
|---|---|
| Date | Fecha en la que sucedió el evento. |
| Descripción | descripción del evento. |
| Perfil de facturación | El perfil de facturación para el que se produjo el evento. |
| Reducción de MACC | Cantidad de reducción de MACC del evento |
| Compromiso restante | El compromiso de MACC restante después del evento |

### <a name="rest-api"></a>[REST API](#tab/rest)

Puede usar las API de [facturación de Azure](/rest/api/billing/) y de [consumo](/rest/api/consumption/) para obtener mediante programación el Compromiso de consumo de Microsoft Azure (MACC) de la cuenta de facturación.

Los ejemplos que se muestran a continuación usan las API de REST. Actualmente, no están admitidos PowerShell ni la CLI de Azure.

### <a name="find-billing-accounts-you-have-access-to"></a>Búsqueda de cuentas de facturación a las que tiene acceso

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2020-05-01
```
La respuesta de la API devuelve una lista de cuentas de facturación.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "type": "Microsoft.Billing/billingAccounts",
      "properties": {
        "displayName": "Contoso",
        "agreementType": "MicrosoftCustomerAgreement",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "hasReadAccess": true,
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/9a12f056-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "9a12f056-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "type": "Microsoft.Billing/billingAccounts",
      "properties": {
        "displayName": "Kayla Lewis",
        "agreementType": "MicrosoftCustomerAgreement",
        "accountStatus": "Active",
        "accountType": "Individual",
        "hasReadAccess": true,
      }
    }
  ]
}
```

Use la propiedad `displayName` de la cuenta de facturación para identificar la cuenta de facturación para la que quiere realizar el seguimiento del MACC. Copie el `name` de la cuenta de facturación. Por ejemplo, si quiere realizar un seguimiento del MACC de la cuenta de facturación de **Contoso**, deberá copiar `9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`. Pegue este valor en algún lugar para poder usarlo en el paso siguiente.

### <a name="get-a-list-of-microsoft-azure-consumption-commitments"></a>Obtención de una lista de los compromisos de consumo de Microsoft Azure

Realice la siguiente solicitud; para ello, reemplace `<billingAccountName>` por el elemento `name` copiado en el primer paso (`9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`).

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/providers/Microsoft.Consumption/lots?api-version=2021-05-01&$filter=source%20eq%20%27ConsumptionCommitment%27
```
La respuesta de la API devuelve una lista de los MACC de la cuenta de facturación.

```json
    {
    "value": [
      {
        "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "name": "G2021032459206000XXXX",
        "type": "Microsoft.Consumption/lots",
        "eTag": null,
        "properties": {
          "purchasedDate": "2021-03-24T16:26:46.0000000Z",
          "status": "Active",
          "originalAmount": {
            "currency": "USD",
            "value": 10000.0
          },
          "closedBalance": {
            "currency": "USD",
            "value": 9899.42
          },
          "source": "ConsumptionCommitment",
          "startDate": "2021-03-01T00:00:00.0000000Z",
          "expirationDate": "2024-02-28T00:00:00.0000000Z"
        }
      },
      {
        "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G1011082459206000XXXX",
        "name": "G1011082459206000XXXX",
        "type": "Microsoft.Consumption/lots",
        "eTag": null,
        "properties": {
          "purchasedDate": "2021-03-24T16:26:46.0000000Z",
          "status": "Complete",
          "originalAmount": {
            "currency": "USD",
            "value": 10000.0
          },
          "closedBalance": {
            "currency": "USD",
            "value": 0.00
          },
          "source": "ConsumptionCommitment",
          "startDate": "2020-03-01T00:00:00.0000000Z",
          "expirationDate": "2021-02-28T00:00:00.0000000Z"
        }
      }
    ]
  }
```

| Nombre del elemento  | Descripción                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `purchasedDate`  | Fecha en que se compró el MACC.   |
| `status`  | El estado de su compromiso. |
| `originalAmount` | Importe del compromiso original. |
| `closedBalance`   | Compromiso restante desde la última factura.    |
| `source`      | Para MACC, el origen siempre será ConsumptionCommitment. |
| `startDate`      |  La fecha en la que se activó el MACC.  |
| `expirationDate`  | La fecha en la que caducó el MACC.   |

Su MACC puede tener uno de los siguientes valores: 

- Activo: el MACC está activo. Cualquier gasto apto contribuirá a su compromiso de MACC.
- Completado: ha completado el compromiso de MACC. 
- Expirado: el MACC ha expirado. Póngase en contacto con el equipo de cuentas de Microsoft. 
- Cancelado: el MACC se ha cancelado. El nuevo gasto de Azure no contribuirá al compromiso del MACC. 

### <a name="get-events-that-affected-macc-commitment"></a>Obtención de eventos que afectaron al compromiso MACC

Realice la siguiente solicitud; para ello, reemplace `<billingAccountName>` por el elemento `name` copiado en el primer paso (`5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`). Deberá introducir los valores de **startDate** (fecha de inicio) y **endDate** (fecha de finalización) a fin de obtener los eventos para la duración que necesita.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/providers/Microsoft.Consumption/events?api-version=2021-05-01&startDate=<startDate>&endDate=<endDate>&$filter=lotsource%20eq%20%27ConsumptionCommitment%27
```

La respuesta de la API devuelve todos los eventos que afectaron al compromiso MACC.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/events/103axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "name": "103axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/SWFF-DVM4-XXX-XXX",
        "billingProfileDisplayName": "Finance",
        "lotId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "lotSource": "ConsumptionCommitment",
        "transactionDate": "2021-05-05T00:09:13.0000000Z",
        "description": "Balance after invoice T00075XXXX",
        "charges": {
          "currency": "USD",
          "value": -100.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 9899.71
        },
        "eventType": "SettledCharges",
        "invoiceNumber": "T00075XXXX"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/events/203axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "name": "203axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/SWFF-DVM4-XXX-XXX",
        "billingProfileDisplayName": "Engineering",
        "lotId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "lotSource": "ConsumptionCommitment",
        "transactionDate": "2021-04-05T00:09:13.0000000Z",
        "description": "Balance after invoice T00074XXXX",
        "charges": {
          "currency": "USD",
          "value": -0.29
        },
        "closedBalance": {
          "currency": "USD",
          "value": 9999.71
        },
        "eventType": "SettledCharges",
        "invoiceNumber": "T00074XXXX"
      }
    }
  ]
}

```
| Nombre del elemento  | Descripción                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `billingProfileId` | Identificador único del perfil de facturación para el que se produjo el evento. |
| `billingProfileDisplayName` | Nombre para mostrar del perfil de facturación para el que se produjo el evento. |
| `lotId`   | Identificador único del MACC.    |
| `lotSource`      | Será ConsumptionCommitment para MACC. |
| `transactionDate`      |  Fecha en la que sucedió el evento.  |
| `description`  | Descripción del evento.   |
| `charges`  | Cantidad de reducción de MACC.   |
| `closedBalance`  | Saldo después del evento.   |
| `eventType`  | Solo se admiten eventos de SettledCharges para MACC.   |
| `invoiceNumber`  | Identificador único de la factura cuyos cargos disminuyeron el MACC.   |

---

## <a name="azure-services-and-marketplace-offers-that-are-eligible-for-macc"></a>Servicios de Azure y ofertas de Marketplace que son aptas para MACC.

Puede determinar qué servicios de Azure y ofertas de Marketplace son aptos para la reducción de MACC en Azure Portal. Para obtener más información, consulte [Determine qué ofertas son válidas para los compromisos de consumo de Azure (MACC/CtC)](/marketplace/azure-consumption-commitment-benefit#determine-which-offers-are-eligible-for-azure-consumption-commitments-maccctc).

## <a name="azure-credits-and-macc"></a>Créditos de Azure y MACC

Si su organización recibió créditos de Azure de Microsoft, el consumo o las compras realizadas con créditos no contribuirán a su compromiso de MACC.

Si su organización adquirió pagos por adelantado de Azure, el consumo o las compras realizadas con créditos no contribuirán a su compromiso de MACC,  pero la propia compra pagada por adelantado reducirá su compromiso de MACC.

Por ejemplo, Contoso realizó un compromiso de MACC de 50 000 USD en mayo. En junio, adquirieron un prepago de Azure de 10 000 USD. La compra reducirá su compromiso de MACC y el compromiso restante será de 40 000 USD. En junio, Contoso consumió 10 000 USD de servicios aptos para prepago de Azure. Los cargos de servicio estarán cubiertos por su prepago de Azure, pero los cargos de servicio no disminuirán su compromiso de MACC. Una vez que el prepago de Azure se utilice por completo, todo el consumo de servicios de Azure y otras compras aptas reducirán el compromiso de MACC.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Comprobación del acceso a un contrato de cliente de Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si necesita ayuda, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

## <a name="next-steps"></a>Pasos siguientes

- [Determine qué ofertas son válidas para los compromisos de consumo de Azure (MACC/CtC)](/marketplace/azure-consumption-commitment-benefit#determine-which-offers-are-eligible-for-azure-consumption-commitments-maccctc)
- [Seguimiento del saldo de créditos de Azure](mca-check-azure-credits-balance.md)