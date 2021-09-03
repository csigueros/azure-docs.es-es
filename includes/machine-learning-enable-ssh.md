---
author: sdgilley
ms.service: machine-learning
ms.topic: include
ms.date: 07/30/2021
ms.author: sgilley
ms.openlocfilehash: bd6760d152edeb3f8e50fa50a5b72476fc46150f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736592"
---
Después de seleccionar **Siguiente: Configuración avanzada**:

1. Active **Habilitar acceso SSH**.
1. En **Origen de clave pública SSH**, seleccione una de estas opciones en la lista desplegable:
    * Si **Genera un par de claves nuevo**:
        1. Escriba un nombre para la clave en el **Nombre del par de claves**.
        1. Seleccione **Crear**.
        1. Seleccione **Download private key and create compute** (Descargar clave privada y crear proceso).  Normalmente, la clave se descarga en la carpeta **Descargas**.  
    * Si selecciona **Use existing public key stored in Azure** (Usar clave pública existente almacenada en Azure), busque y seleccione la clave en **Stored key** (Clave almacenada).
    * Si selecciona **Usar la clave pública existente**, proporcione una clave pública RSA en el formato de una sola línea (empezando por "ssh-rsa") o en el formato PEM de varias líneas. Puede generar claves SSH mediante ssh-keygen en Linux y OS X, o PuTTYGen en Windows.