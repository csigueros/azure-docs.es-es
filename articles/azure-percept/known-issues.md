---
title: Problemas conocidos de Azure Percept
description: Aprenda más sobre los problemas conocidos de Azure Percept y sus soluciones alternativas.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: reference
ms.date: 03/25/2021
ms.openlocfilehash: bf1e6f1297e73811e4ce7044319b13dc4c5b149f
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114295858"
---
# <a name="azure-percept-known-issues"></a>Problemas conocidos de Azure Percept

A continuación se muestran los problemas con Azure Percept DK, Azure Percept Audio o Azure Percept Studio que los equipos de productos conocen. Siempre que sea posible, se proporcionan soluciones alternativas y pasos para solucionar problemas. Si se encuentra bloqueado por cualquiera de estos problemas, puede publicarlo como una pregunta en [Microsoft Q&A](/answers/topics/azure-percept.html) o enviar una solicitud de soporte técnico al cliente en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). 

|Área|Síntomas|Descripción del problema|Solución alternativa|
|-------|---------|---------|---------|
| Azure Percept DK | No se pueden implementar los modelos de ejemplo y demostración en Azure Percept Studio | A veces, los módulos azureeyemodule o azureearspeechmodule dejan de ejecutarse. Los registros de edgeAgent muestran el error que indica que hay "demasiados niveles de vínculos simbólicos". | Restablezca el dispositivo. Para ello, [actualícelo a través de USB](./how-to-update-via-usb.md) |
| Localización | Es posible que los usuarios que no hablan inglés vean partes de la experiencia de configuración de Azure Percept DK en ese idioma. | La experiencia de configuración de Azure Percept DK no está totalmente localizada. | La corrección está programada para julio de 2021  |
| Azure Percept DK | Cuando pasa por la experiencia de configuración en un equipo Mac, es posible que esta experiencia se cierre de manera imprevista después de conectarse a la red Wi-Fi. | Al pasar por la experiencia de configuración en un equipo Mac, se abre inicialmente en una ventana en lugar de hacerlo en un explorador web. La ventana no se conserva una vez que la conexión cambia del punto de acceso del dispositivo a la red Wi-Fi. | Abra un explorador web y vaya a https://10.1.1.1, lo que le permitirá completar la experiencia de configuración. |
| Azure Percept DK | El kit de desarrollo ejecuta un modelo personalizado y, después de reiniciar el kit de desarrollo, ejecuta el modelo de ejemplo predeterminado. | El contenedor de módulos gemelos para el modelo personalizado no se conserva en los reinicios del dispositivo. Después del reinicio, se debe volver a generar el módulo gemelo para el módulo personalizado, lo que puede tardar 5 minutos o más. El kit de desarrollo ejecutará el modelo predeterminado hasta que se complete ese proceso. | Después de un reinicio, debe esperar hasta que se vuelva a crear el módulo gemelo personalizado. |