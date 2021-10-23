---
title: Administración de los datos de usuarios en Azure Active Directory B2C
description: Obtenga información sobre cómo eliminar o exportar datos de usuario en Azure AD B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/06/2018
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 03b21f0e7a9f3035a5bfad6494d49f69ba3b5fef
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041773"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Administración de los datos de usuarios en Azure Active Directory B2C

 En este artículo se describe cómo se pueden administrar los datos de usuario en Azure Active Directory B2C (Azure AD B2C) mediante las operaciones proporcionadas por [Microsoft Graph API](/graph/use-the-api). La administración de datos de usuario incluye eliminar o exportar datos de registros de auditoría.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Eliminación de los datos de usuario

Los datos de usuario se almacenan en el directorio de Azure AD B2C y en los registros de auditoría. Todos los datos de auditoría del usuario se conservan durante un período de 7 días en Azure AD B2C. Si quiere eliminar los datos de usuario dentro de esos 7 días, puede usar la operación [Eliminar un usuario](/graph/api/user-delete). Se requiere una operación DELETE para cada uno de los inquilinos de Azure AD B2C donde puedan residir los datos.

A cada usuario en Azure AD B2C se le asigna un identificador de objeto. El identificador de objeto proporciona un identificador inequívoco, que puede usar para eliminar datos de usuario en Azure AD B2C. Según la arquitectura, el identificador de objeto puede ser un identificador de correlación útil a través de otros servicios, como bases de datos de administración de relación con los clientes, finanzas y marketing.

La manera más precisa para obtener el identificador de objeto para un usuario es hacerlo como parte de un proceso de autenticación con Azure AD B2C. Si recibe una solicitud de datos válida de un usuario mediante otros métodos, puede ser necesario un proceso sin conexión, como una búsqueda por parte de un agente de soporte técnico y servicio al cliente, para encontrar al usuario y anotar el id. de objeto asociado.

En el ejemplo siguiente, se muestra un flujo posible de eliminación de datos:

1. El usuario inicia sesión y selecciona **Delete my data** (Eliminar mis datos).
2. La aplicación ofrece una opción para eliminar los datos dentro de una sección de administración de la aplicación.
3. La aplicación fuerza una autenticación en Azure AD B2C. Azure AD B2C devuelve a la aplicación un token con el identificador de objeto del usuario.
4. La aplicación recibe el token y el identificador de objeto se usa para eliminar los datos de usuario mediante una llamada a Microsoft Graph API. Microsoft Graph API elimina los datos de usuario y devuelve un código de estado de 200 OK.
5. La aplicación organiza la eliminación de datos de usuario en otros sistemas organizacionales según sea necesario mediante el id. de objeto u otros identificadores.
6. La aplicación confirma la eliminación de los datos e indica al usuario los pasos siguientes.

## <a name="export-customer-data"></a>Exportación de los datos del cliente

El proceso para exportar los datos del cliente de Azure AD B2C es similar al proceso de eliminación.

Los datos de usuario de Azure AD B2C están limitados a:

- **Datos almacenados en Azure Active Directory**: puede recuperar los datos en un recorrido del usuario de autenticación de Azure AD B2C mediante el id. de objeto o cualquier nombre de inicio de sesión, como la dirección de correo electrónico o el nombre de usuario.
- **Informe de eventos de auditoría específicos del usuario**: los datos se indizan mediante el id. de objeto.

En el siguiente ejemplo de un flujo de datos de exportación, los pasos que se describen como realizados por la aplicación también pueden realizarse mediante un proceso de back-end o por un usuario con un rol de administrador en el directorio:

1. El usuario inicia sesión en la aplicación. Azure AD B2C exige la autenticación con Azure AD Multi-Factor Authentication si es necesario.
2. La aplicación usa las credenciales de usuario para llamar a una operación de Microsoft Graph API para recuperar los atributos del usuario. Microsoft Graph API proporciona los datos del atributo en formato JSON. Según el esquema, puede establecer el contenido del token de id. para incluir todos los datos personales sobre un usuario.
3. La aplicación recupera la actividad de auditoría del usuario. Microsoft Graph API proporciona los datos del evento a la aplicación.
4. La aplicación agrega los datos y los pone a disposición del usuario.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo los usuarios acceden a la aplicación, consulte [Administración de acceso de usuarios](manage-user-access.md).