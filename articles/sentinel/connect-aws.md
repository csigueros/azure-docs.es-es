---
title: Conexión de datos de AWS CloudTrail a Microsoft Sentinel | Microsoft Docs
description: Use el conector de AWS para delegar el acceso de Microsoft Sentinel en los registros de recursos de AWS, lo que crea una relación de confianza entre AWS CloudTrail y Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: eff347499e045c35ecd2e08185e09fede68a1a42
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707336"
---
# <a name="connect-aws-cloudtrail-to-microsoft-sentinel"></a>Conexión de AWS CloudTrail a Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Utilice el conector de AWS para transmitir los eventos de administración de AWS CloudTrail a Microsoft Sentinel. Este proceso de conexión delega el acceso de Microsoft Sentinel en los registros de recursos de AWS, lo que crea una relación de confianza entre AWS CloudTrail y Microsoft Sentinel. Para ello, en AWS se crea un rol que concede a Microsoft Sentinel el permiso necesario para acceder a los registros de AWS.

> [!NOTE]
> AWS CloudTrail tiene [limitaciones integradas](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html) en su LookupEvents API. No permite más de dos transacciones por segundo (TPS) por cuenta y ninguna consulta puede devolver más de 50 registros. En consecuencia, si un único inquilino genera constantemente más de 100 registros por segundo en una región, se producirán trabajos pendientes y retrasos en la ingesta de datos.
> Actualmente, solo puede conectar AWS Commercial CloudTrail a Microsoft Sentinel y no a AWS GovCloud CloudTrail.

## <a name="prerequisites"></a>Requisitos previos

Debe tener permiso de escritura en el área de trabajo de Microsoft Sentinel.

> [!NOTE]
> Azure Microsoft recopila eventos de administración de CloudTrail de todas las regiones. Se recomienda no transmitir eventos de una región a otra.

## <a name="connect-aws"></a>Conexión de AWS 


1. En Microsoft Sentinel, seleccione **Conectores de datos** y, a continuación, la línea **Amazon Web Services** de la tabla. Después, en el panel de AWS situado a la derecha, seleccione **Abrir la página del conector**.

1. Siga las instrucciones que aparecen en **Configuration** (Configuración) mediante los pasos siguientes.
 
1.  En la consola de Amazon Web Services, en **Security, Identity & Compliance** (Seguridad, identidad y cumplimiento), haga clic en **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Haga clic en **Roles** y en **Create role** (Crear rol).

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Seleccione **Another AWS account** (Otra cuenta de AWS). En el campo **Account ID** (Id. de cuenta), especifique el **identificador de la cuenta Microsoft** (**123412341234**) que encontrará en la página del conector de AWS del portal de Microsoft Sentinel.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Asegúrese de que la opción **Require External ID** (Requerir id. externo) esté seleccionada y escriba el identificador externo (identificador del área de trabajo), que encontrará en la página del conector de AWS del portal de Microsoft Sentinel.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  En **Attach permissions policy** (Asociar directiva de permisos), seleccione **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Escriba una etiqueta (opcional).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  A continuación, especifique el **nombre del rol** y haga clic en el botón **Create role** (Crear rol).

    ![AWS7](./media/connect-aws/aws-7.png)

1.  En la lista Roles, seleccione el rol que ha creado.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Copie el valor de **ARN de rol**. En el portal de Microsoft Sentinel, en la pantalla del conector de Amazon Web Services, pegue el valor en el campo **Rol que se va a agregar** y seleccione **Agregar**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Para usar el esquema correspondiente de Log Analytics con los eventos de AWS, busque **AWSCloudTrail**.

    > [!IMPORTANT]
    > A partir del 1 de diciembre de 2020, el campo **AwsRequestId** se ha reemplazado por el campo **AwsRequestId_** (tenga en cuenta el guion bajo agregado). Los datos del campo anterior **AwsRequestId** se conservarán hasta el final del período de retención de datos especificado por el cliente.

## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a conectar AWS CloudTrail con Microsoft Sentinel. Para obtener más información sobre Microsoft Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Microsoft Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.
