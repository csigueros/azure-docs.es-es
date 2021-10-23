---
title: Configuración de un laboratorio centrado en el aprendizaje profundo mediante Azure Lab Services | Microsoft Docs
description: Aprenda a configurar un laboratorio centrado en el aprendizaje profundo en el procesamiento de lenguaje natural (NLP) mediante Azure Lab Services.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: f087cf890e622634b92b5b9c10db6a6e8621ece1
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176954"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Configuración de un laboratorio centrado en el aprendizaje profundo en el procesamiento de lenguaje natural mediante Azure Lab Services
En este artículo se muestra cómo configurar un laboratorio centrado en el aprendizaje profundo en el procesamiento de lenguaje natural (NLP) mediante Azure Lab Services. El procesamiento de lenguaje natural (NLP) es una forma de inteligencia artificial (AI) que permite a los equipos utilizar la traducción, el reconocimiento de voz y otras capacidades de comprensión de lenguajes.  

Los alumnos que sigan una clase de NLP obtienen una máquina virtual Linux para aprender a aplicar algoritmos de red neuronal para desarrollar modelos de aprendizaje profundo que se utilizan para analizar el lenguaje humano escrito. 

## <a name="lab-configuration"></a>Configuración del laboratorio
Para configurar este laboratorio, para empezar necesita una suscripción de Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar. Una vez que tenga una suscripción de Azure, puede crear una nueva cuenta de laboratorio en Azure Lab Services o usar una existente. Consulte el siguiente tutorial sobre la creación de una nueva cuenta de laboratorio: [Tutorial de configuración de una cuenta de laboratorio](tutorial-setup-lab-account.md).
 
Después de crear la cuenta de laboratorio, habilite la siguiente configuración en ella: 

| Configuración de la cuenta de laboratorio | Instructions |
| ----------- | ------------ |  
| Imágenes de Marketplace | Habilite la imagen de Data Science Virtual Machine para Linux (Ubuntu) para usarla en su cuenta de laboratorio.  Para obtener instrucciones, vea el siguiente artículo: [Especificación de las imágenes de Marketplace disponibles para los creadores de laboratorios](specify-marketplace-images.md). | 

Siga [este tutorial](tutorial-setup-classroom-lab.md) para crear un laboratorio y aplique la configuración siguiente:

| Configuración del laboratorio | Valor/instrucciones | 
| ------------ | ------------------ |
| Tamaño de la máquina virtual | **GPU pequeña (proceso)** . Este tamaño es más adecuado para aplicaciones de proceso intensivo y uso intensivo de la red; por ejemplo, la inteligencia artificial y el aprendizaje profundo. |
| Imagen de la máquina virtual | [Data Science Virtual Machine para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Esta imagen proporciona marcos y herramientas de aprendizaje profundo para aprendizaje automático y ciencia de datos. Para ver la lista completa de las herramientas instaladas en esta imagen, consulte el siguiente artículo: [¿Qué incluye DSVM?](../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm) |
| Habilitar la conexión a Escritorio remoto | <p>La imagen de Data Science ya está configurada para usar X2Go. De manera que profesores y alumnos pueden conectarse mediante un escritorio remoto de la GUI.  X2Go *no* requiere que esté activada la opción **Habilitar la conexión a escritorio remoto**.  Esta opción solo debe estar activada si decide usar RDP.

>**Importante**: Aunque se recomienda usar X2Go con la imagen de Data Science, si decide usar RDP, tendrá que conectarse a la máquina virtual Linux mediante SSH la primera vez e instalar los paquetes de RDP y de la GUI.  A continuación, el usuario o los alumnos podrán conectarse a la máquina virtual Linux mediante RDP más adelante.  Para obtener más información, consulte [Habilitación del Escritorio remoto para máquinas virtuales Linux en un laboratorio de Azure Lab Services](how-to-enable-remote-desktop-linux.md).

La imagen de Data Science Virtual Machine para Linux proporciona las herramientas y los marcos de aprendizaje profundo necesarios para este tipo de clase. Como resultado, después de la creación de la máquina de plantilla, no es necesario personalizarla más. Se puede publicar para que los alumnos la utilicen. Seleccione el botón **Publicar** en la página de la plantilla para publicarla en el laboratorio.  

## <a name="cost"></a>Coste
Si desea calcular el costo de este laboratorio, puede usar el ejemplo siguiente: 

Para una clase de 25 alumnos con 20 horas de clase programada y 10 horas de cuota para deberes o tareas, el precio del laboratorio sería de 25 alumnos * (20 + 10) horas * 139 unidades de laboratorio * 0,01 USD por hora = 1042,5 USD

Para más detalles sobre los precios, consulte [Precios de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusión
En este artículo se explican los pasos necesarios para crear un laboratorio de clase de procesamiento de lenguaje natural. Puede usar una configuración similar para otras clases de aprendizaje profundo.

## <a name="next-steps"></a>Pasos siguientes
Los pasos siguientes son comunes a la configuración de cualquier laboratorio:

- [Incorporación de usuarios](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Establecimiento de la cuota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Establecimiento de la programación](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Vínculos de registro por correo electrónico para los alumnos](how-to-configure-student-usage.md#send-invitations-to-users) 

