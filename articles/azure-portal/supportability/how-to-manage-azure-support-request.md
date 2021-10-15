---
title: Administración de una solicitud de soporte técnico de Azure
description: Describe cómo ver solicitudes de soporte técnico, enviar mensajes, cambiar el nivel de gravedad de las solicitudes, compartir información de diagnóstico con el soporte técnico de Azure, volver a abrir una solicitud de soporte técnico cerrada y cargar archivos.
tags: billing
ms.topic: how-to
ms.date: 09/30/2021
ms.openlocfilehash: 8e7b074883fe2dcfb79913e54cf7180e26f29c2a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353208"
---
# <a name="manage-an-azure-support-request"></a>Administración de una solicitud de soporte técnico de Azure

Después de [crear una solicitud de soporte técnico de Azure](how-to-create-azure-support-request.md), puede administrarla en [Azure Portal](https://portal.azure.com), como se describe en este artículo. También puede crear y administrar solicitudes mediante programación con la [API REST de incidencia de soporte técnico de Azure](/rest/api/support) o con la [CLI de Azure](/cli/azure/azure-cli-support-request).

## <a name="view-support-requests"></a>Ver las solicitudes de soporte técnico

Para ver los detalles y el estado de las solicitudes de soporte técnico, vaya a **Ayuda y soporte técnico** >  **Todas las solicitudes de soporte técnico**.

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="Todas las solicitudes de soporte técnico":::

En esta página, puede buscar, filtrar y ordenar solicitudes de soporte técnico. Seleccione una solicitud de soporte técnico para ver los detalles, incluida su gravedad y los mensajes asociados con la solicitud.

## <a name="send-a-message"></a>Envío de un mensaje

1. En la página **Todas las solicitudes de soporte técnico**, seleccione la solicitud de soporte técnico.

1. En la página **Solicitud de soporte técnico**, seleccione **Nuevo mensaje**.

1. Escriba el mensaje y seleccione **Enviar**.

## <a name="change-the-severity-level"></a>Cambiar el nivel de gravedad

> [!NOTE]
> El nivel de gravedad máximo depende de su [plan de soporte técnico](https://azure.microsoft.com/support/plans).

1. En la página **Todas las solicitudes de soporte técnico**, seleccione la solicitud de soporte técnico.

1. En la página **Solicitud de soporte técnico**, seleccione **Cambiar**.

1. Azure Portal muestra una de dos pantallas, en función de si la solicitud ya está asignada a un ingeniero de soporte técnico:

    - Si no se ha asignado la solicitud, verá una pantalla similar a la siguiente. Seleccione un nivel de gravedad nuevo y, a continuación, seleccione **Cambiar**.

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="Seleccionar un nivel de gravedad nuevo":::

    - Si se ha asignado la solicitud, verá una pantalla similar a la siguiente. Seleccione **Aceptar** y, a continuación, cree un [nuevo mensaje](#send-a-message) para solicitar un cambio en el nivel de gravedad.

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="No se puede seleccionar un nuevo nivel de gravedad":::

## <a name="share-diagnostic-information-with-azure-support"></a>Compartir información de diagnóstico con el soporte técnico de Azure

Al crear una solicitud de soporte técnico, se puede seleccionar **Sí** o **No** en la sección **Compartir información de diagnóstico**. Esta opción determina si Soporte técnico de Azure puede recopilar [información de diagnóstico](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) como, por ejemplo, [archivos de registro](how-to-create-azure-support-request.md#advanced-diagnostic-information-logs), de los recursos de Azure que pueda ayudar a resolver el problema.

Para cambiar la selección de **Compartir información de diagnóstico** una vez creada la solicitud:

1. En la página **Todas las solicitudes de soporte técnico**, seleccione la solicitud.

1. En la página **Solicitud de soporte técnico**, busque **Compartir información de diagnóstico** y, a continuación, seleccione **Cambiar**.

1. Seleccione **Sí** o **No** y, a continuación, seleccione **Aceptar** para confirmar.

    :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="Conceder permisos para la información de diagnóstico":::

## <a name="upload-files"></a>Carga de archivos

Puede usar la opción de carga de archivos para cargar archivos de diagnóstico o cualquier otro archivo que considere pertinente para la solicitud de soporte técnico.

1. En la página **Todas las solicitudes de soporte técnico**, seleccione la solicitud.

1. En la página **Solicitud de soporte técnico**, busque el archivo y seleccione **Cargar**. Repita el proceso si tiene varios archivos.

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="Carga de un archivo":::.

### <a name="file-upload-guidelines"></a>Instrucciones de carga de archivos

Siga estas directrices cuando use la opción de carga de archivos:

- Para proteger su privacidad, no incluya ninguna información personal en su carga.
- El nombre de archivo debe ser de 110 caracteres como máximo.
- No se puede cargar más de un archivo.
- Los archivos no pueden ser superiores a 4 MB.
- Todos los archivos deben tener una extensión de nombre de archivo, por ejemplo, *.docx* o *.xlsx*. En la tabla siguiente se muestran las extensiones de nombre de archivo que se permiten para la carga.

| 0-9, A-C    | D-G   | H-N         | O-Q   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | .dat  | .har        | .odx  | .rar     | .uccapilog | .xlam   |
| .a          | .db   | .hwl        | .oft  | .rdl     | .uccplog   | .xlr    |
| .abc        | .DMP  | .ics        | .old  | .rdlc    | .udcx      | .xls    |
| .adm        | .do_  | .ini        | .one  | .re_     | .vb_       | .xlsb   |
| .aspx       | .doc  | .java       | .osd  | .remove  | .vbs_      | .xlsm   |
| .ATF        | .docm | .jpg        | .OUT  | .ren     | .vcf       | .xlsx   |
| .b          | .docx | .LDF        | .p1   | .rename  | .vsd       | .xlt    |
| .ba_        | .dotm | .letterhead | .pcap | .rft     | .wdb       | .xltx   |
| .bak        | .dotx | .lo_        | .pdb  | .rpt     | .wks       | .xml    |
| .blg        | .dtsx | .log        | .pdf  | .rte     | .wma       | .xmla   |
| .CA_        | .eds  | .lpk        | .piz  | .rtf     | .wmv       | .xps    |
| .CAB        | .emf  | .manifest   | .pmls | .run     | .wmz       | .xsd    |
| .cap        | .eml  | .master     | .png  | .saz     | .wps       | .xsn    |
| .catx       | .emz  | .mdmp       | .potx | .sql     | .wpt       | .xxx    |
| .CFG        | .err  | .mof        | .ppt  | .sqlplan | .wsdl      | .z_     |
| .compressed | .etl  | .mp3        | .pptm | .stp     | .wsp       | .z01    |
| .Config     | .evt  | .mpg        | .pptx | .svclog  | .wtl       | .z02    |
| .cpk        | .evtx | .ms_        | .prn  | .tdb     | -          | .zi     |
| .cpp        | .EX   | .msg        | .psf  | .tdf     | -          | .zi_    |
| .cs         | .ex_  | .mso        | .pst  | .text    | -          | .zip    |
| .CSV        | .ex0  | .msu        | .pub  | .thmx    | -          | .zip_   |
| .cvr        | .FRD  | .nfo        | -     | .tif     | -          | .zipp   |
| -           | .gif  | -           | -     | .trc     | -          | .zipped |
| -           | .guid | -           | -     | .TTD     | -          | .zippy  |
| -           | .gz   | -           | -     | .tx_     | -          | .zipx   |
| -           | -     | -           | -     | .txt     | -          | .zit    |
| -           | -     | -           | -     | -        | -          | .zix    |
| -           | -     | -           | -     | -        | -          | .zzz    |

## <a name="close-a-support-request"></a>Cierre de una solicitud de soporte técnico

Para cerrar una solicitud de soporte técnico, [envíe un mensaje](#send-a-message) para pedir que se cierre la solicitud.

## <a name="reopen-a-closed-request"></a>Volver a abrir una solicitud cerrada

Para volver a abrir una solicitud de soporte cerrada, cree un [mensaje nuevo](#send-a-message). Al hacerlo, la solicitud se volverá a abrir automáticamente.

## <a name="cancel-a-support-plan"></a>Cancelación de un plan de soporte técnico

Para cancelar un plan de soporte técnico, consulte [Cancelación de un plan de soporte técnico](../../cost-management-billing/manage/cancel-azure-subscription.md#cancel-a-support-plan).

## <a name="next-steps"></a>Pasos siguientes

- Consulte el proceso para [crear una solicitud de soporte técnico de Azure](how-to-create-azure-support-request.md).
- Obtenga más información sobre la [API REST de incidencia de soporte técnico de Azure](/rest/api/support).
