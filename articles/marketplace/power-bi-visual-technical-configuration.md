---
title: Configuración del apartado técnico de la oferta visual de Power BI en el Centro de partners para Microsoft AppSource
description: Aprenda a configurar la oferta visual de Power BI en el Centro de partners para Microsoft AppSource.
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/21/2021
ms.openlocfilehash: 94f1a884eb2fbb10ced8c343d0408845f0ff34ba
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273029"
---
# <a name="set-up-power-bi-visual-offer-technical-configuration"></a>Configuración técnica de la oferta visual de Power BI

En la pestaña **Configuración técnica**, proporcione los archivos necesarios para la oferta visual de Power BI.

:::image type="content" source="media/power-bi-visual/technical-configuration.png" alt-text="Muestra la página Configuración técnica del Centro de partners":::.

## <a name="pbiviz-package"></a>Paquete PBIVIZ

[Empaquete el objeto visual de Power BI](/power-bi/developer/visuals/package-visual) en un paquete PBIVIZ que contenga todos los metadatos necesarios:

- Nombre de objeto visual
- Nombre para mostrar
- GUID (vea la nota siguiente)
- Versión (vea la nota siguiente)
- Descripción
- Nombre y correo electrónico del autor

> [!NOTE]
> Si va a actualizar o volver a enviar un objeto visual:
> - El GUID debe permanecer igual.
> - El número de versión debe incrementarse entre las actualizaciones del paquete.

## <a name="sample-pbix-report-file"></a>Archivo de informe PBIX de ejemplo

Para presentar el objeto visual, debe ayudar a los usuarios a familiarizarse con él. Resalte el valor que el objeto visual aporta al usuario y ofrezca ejemplos de uso y opciones de formato. Agregue una página de "sugerencias" al final que contenga trucos, sugerencias y acciones que conviene evitar. El archivo de informe PBIX de ejemplo debe funcionar sin conexión, sin ninguna conexión externa.

> [!NOTE]
> - El informe PBIX debe usar la misma versión del objeto visual que PBIVIZ.
> - El archivo de informe PBIX debe funcionar sin conexión, sin ninguna conexión externa.

Seleccione **Guardar borrador** antes de omitir en el menú de navegación izquierdo a la pestaña **Administración de ofertas**.

## <a name="next-steps"></a>Pasos siguientes

- [Administración de ofertas](power-bi-visual-manage-names.md)
