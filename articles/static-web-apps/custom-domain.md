---
title: Configuración de un dominio personalizado en Azure Static Web Apps
description: Aprenda a asignar un dominio personalizdo con un certificado SSL/TLS gratis a Azure Static Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: buhollan
ms.openlocfilehash: 07da31c48d70f25a2364b4af242bc0d1331d7036
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750666"
---
# <a name="set-up-a-custom-domain-with-free-certificate-in-azure-static-web-apps"></a>Configuración de un dominio personalizado con un certificado gratis en Azure Static Web Apps

De forma predeterminada, Azure Static Web Apps proporciona un nombre de dominio generado automáticamente. En este artículo se muestra cómo asignar un nombre de dominio personalizado a una aplicación de Azure Static Web Apps.

## <a name="free-ssltls-certificate"></a>Certificado SSL/TLS gratis

Azure Static Web Apps proporciona automáticamente un certificado SSL/TLS gratis para el nombre de dominio generado automáticamente y todos los dominios personalizados que puede agregar.

## <a name="walkthrough-video"></a>Tutorial en vídeo

> [!VIDEO https://channel9.msdn.com/Shows/5-Things/Configuring-a-custom-domain-with-Azure-Static-Web-Apps/player?format=ny]

## <a name="prerequisites"></a>Requisitos previos

- Un nombre de dominio adquirido
- Acceso a las propiedades de configuración de DNS para el dominio

## <a name="dns-configuration-options"></a>Opciones de configuración de DNS

Hay algunos tipos diferentes de configuraciones de DNS disponibles para una aplicación.

| Escenario                                                                                 | Ejemplo                                | Método de validación de dominio | Tipo de registros DNS |
| ---------------------------------------------------------------------------------------- | -------------------------------------- | ------------------------ | --------------- |
| [Adición de un dominio raíz o vértice](#add-domain-using-txt-record-validation)                        | `mydomain.com`, `example.co.uk`        | TXT                      | ALIAS           |
| [Adición de un subdominio](#add-domain-using-cname-record-validation)                             | `www.mydomain.com`, `foo.mydomain.com` | CNAME                    | CNAME           |
| [Transferencia de un subdominio que está actualmente en uso](#add-domain-using-txt-record-validation) | `www.mydomain.com`, `foo.mydomain.com` | TXT                      | CNAME           |

## <a name="add-domain-using-cname-record-validation"></a>Adición de dominio mediante la validación de registros CNAME

La validación de registros CNAME es la forma recomendada de agregar un dominio personalizado; sin embargo, solo funciona con subdominios. Si desea agregar un dominio raíz (`mydomain.com`), vaya directamente a la sección sobre la [incorporación de un dominio mediante la validación de registros TXT](#add-domain-using-txt-record-validation) y, después, la [creación de un registro ALIAS](#create-an-alias-record).

> [!IMPORTANT]
> Si el subdominio está asociado actualmente a un sitio activo y no está preparado para transferirlo a una aplicación web estática, use la [validación de registros TXT](#add-domain-using-txt-record-validation).

### <a name="enter-your-subdomain"></a>Especificación de un subdominio

1. Abra la aplicación web estática en [Azure Portal](https://portal.azure.com).

1. Seleccione **Dominios personalizados** en el menú.

1. Seleccione el botón **Agregar**.

1. En el campo _Nombre de dominio_, escriba el subdominio. Asegúrese de escribirlo sin protocolos. Por ejemplo, `www.mydomain.com`.

   :::image type="content" source="media/custom-domain/add-subdomain.png" alt-text="Pantalla Agregar dominio que muestra el subdominio personalizado en el cuadro de entrada":::

1. Seleccione el botón **Siguiente** para ir al paso _Validar y configurar_.

### <a name="configure-cname-with-your-domain-provider"></a>Configuración del registro CNAME con su proveedor de dominio

Deberá configurar un registro CNAME con el proveedor de dominio. Se recomienda Azure DNS, pero estos pasos funcionarán con cualquier proveedor de dominio.

# <a name="azure-dns"></a>[DNS de Azure](#tab/azure-dns)

1. Asegúrese de que **CNAME** está seleccionado en la lista desplegable _Tipo de registro de nombre de host_.

1. Copie el valor del campo _Valor_ en el Portapapeles, para lo que debe seleccionar el icono de **copia**.

   :::image type="content" source="media/custom-domain/copy-cname.png" alt-text="Validar y agregar una pantalla que muestra CNAME seleccionado y el contorno del icono de copia":::

1. En una pestaña o ventana del explorador independientes, abra la zona de Azure DNS en Azure Portal.

1. Seleccione el botón **+ Conjunto de registros**.

1. Cree un conjunto de registros **CNAME** con los siguientes valores.

   | Configuración          | Valor                                     |
   | ---------------- | ----------------------------------------- |
   | Nombre             | Su subdominio, como `www`             |
   | Tipo             | CNAME                                     |
   | Conjunto de registros de alias | No                                        |
   | TTL              | Deje el valor predeterminado                    |
   | Unidad de TTL         | Deje el valor predeterminado                    |
   | Alias            | Pegue el nombre de dominio del Portapapeles |

1. Seleccione **Aceptar**.

   :::image type="content" source="media/custom-domain/azure-dns-cname.png" alt-text="Pantalla Conjunto de registros de Azure DNS con los campos de nombre, tipo y alias resaltados":::

[!INCLUDE [validate CNAME](../../includes/static-web-apps-validate-cname.md)]

# <a name="other-dns"></a>[Otro DNS](#tab/other-dns)

1. Asegúrese de que **CNAME** está seleccionado en la lista desplegable _Tipo de registro de nombre de host_.

1. Copie el valor del campo _Valor_ en el Portapapeles, para lo que debe seleccionar el icono de **copia**.

   :::image type="content" source="media/custom-domain/copy-cname.png" alt-text="Validar y agregar una pantalla que muestra CNAME seleccionado y el contorno del icono de copia":::

1. En una pestaña o ventana del explorador independientes, inicie sesión en el sitio web de su proveedor de dominio.

1. Busque la página de administración de registros DNS. Cada proveedor de dominios tiene su propia interfaz de registros DNS, así que consulte la documentación del proveedor. Busque áreas del sitio etiquetadas como **Nombre de dominio**, **DNS** o **Administración del servidor del nombres**.

1. A menudo, se puede encontrar la página de registros DNS al ver la información de la cuenta y al buscar un vínculo, como **Mis dominios**. Vaya a dicha página y, después, busque un vínculo que se llame algo similar a **Zone file**, **DNS Records** o **Advanced configuration**.

   La captura de pantalla siguiente es un ejemplo de página de registros DNS:

   :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Configuración del proveedor de DNS de ejemplo":::

1. Cree un registro **CNAME** con los siguientes valores.

   | Configuración             | Valor                                     |
   | ------------------- | ----------------------------------------- |
   | Tipo                | CNAME                                     |
   | Host                | Su subdominio, como `www`             |
   | Valor               | Pegue el nombre de dominio del Portapapeles |
   | TTL (si corresponde) | Deje el valor predeterminado                    |

1. Guarde los cambios en el proveedor de DNS.

[!INCLUDE [validate CNAME](../../includes/static-web-apps-validate-cname.md)]

---

## <a name="add-domain-using-txt-record-validation"></a>Incorporación de un dominio mediante la validación de registros TXT

Azure usa un registro TXT para validar que un usuario posee un dominio. Esto resulta útil cuando se desea realizar una de las siguientes operaciones...

1. Se desea configurar un dominio raíz (es decir, `mydomain.com`). Es preciso validar que es el propietario del dominio para poder crear un registro ALIAS que configure el dominio raíz.

1. Quiere transferir un subdominio sin tiempo de inactividad. El método de validación de registros TXT permite validar que es el propietario del dominio y que las aplicaciones web estáticas pasan por el proceso de emisión de un certificado para ese dominio. Luego, puede cambiar el dominio para que apunte a la aplicación web estática en cualquier momento con un registro CNAME.

#### <a name="enter-your-domain"></a>Especificación de un dominio

1. Abra la aplicación web estática en [Azure Portal](https://portal.azure.com).

1. Seleccione **Dominios personalizados** en el menú.

1. Seleccione el botón **Agregar**.

1. En el campo _Nombre de dominio_, escriba el dominio raíz (es decir, `mydomain.com`) o el subdominio (es decir, `www.mydomain.com`).

   :::image type="content" source="media/custom-domain/add-domain.png" alt-text="Pantalla Agregar dominio que muestra el dominio personalizado en el cuadro de entrada":::

1. Haga clic en el botón **Siguiente** para ir al paso _Validar y configurar_.

#### <a name="configure-txt-record-with-your-domain-provider"></a>Configuración de un registro TXT con el proveedor de dominio

Deberá configurar un registro TXT con el proveedor de dominio. Se recomienda Azure DNS, pero estos pasos funcionarán con cualquier proveedor de dominio.

# <a name="azure-dns"></a>[DNS de Azure](#tab/azure-dns)

1. Asegúrese de que en la lista desplegable "Tipo de registro de nombre de host" se ha seleccionado "TXT".

1. Seleccione el botón **Generar código**.

   :::image type="content" source="media/custom-domain/generate-code.png" alt-text="Agregar pantalla personalizada con el botón Generar código resaltado":::

   Esta acción genera un código único, que puede tardar hasta un minuto en procesarse.

1. Seleccione el icono del Portapapeles junto al código para copiar el valor en el Portapapeles.

   :::image type="content" source="media/custom-domain/copy-code.png" alt-text="Pantalla Agregar dominio personalizado con el botón Copiar código resaltado":::

1. En una pestaña o ventana del explorador independientes, abra la zona de Azure DNS en Azure Portal.

1. Seleccione el botón **+ Conjunto de registros**.

1. Cree un conjunto de registros **TXT** con los siguientes valores.

   | Configuración  | Valor                                                                           |
   | -------- | ------------------------------------------------------------------------------- |
   | Nombre     | `@` para un dominio raíz o especifique `_dnsauth.<YOUR_SUBDOMAIN>` para el subdominio.         |
   | Tipo     | TXT                                                                             |
   | TTL      | Deje el valor predeterminado                                                          |
   | Unidad de TTL | Deje el valor predeterminado                                                          |
   | Valor    | Pegar el código desde el Portapapeles                                              |

1. Seleccione **Aceptar**.

   :::image type="content" source="media/custom-domain/azure-dns-txt.png" alt-text="Pantalla Conjunto de registros de Azure DNS con los campos de nombre, tipo y valor resaltados":::

[!INCLUDE [validate TXT record](../../includes/static-web-apps-validate-txt.md)]

# <a name="other-dns"></a>[Otro DNS](#tab/other-dns)

1. Asegúrese de que en la lista desplegable "Tipo de registro de nombre de host" se ha seleccionado "TXT".

1. Seleccione el botón **Generar código**.

   :::image type="content" source="media/custom-domain/generate-code.png" alt-text="Agregar pantalla personalizada con el botón Generar código resaltado":::

   Esta acción genera un código único, que puede tardar hasta un minuto en procesarse.

1. Seleccione el icono del Portapapeles junto al código para copiar el valor en el Portapapeles.

   :::image type="content" source="media/custom-domain/copy-code.png" alt-text="Pantalla Agregar dominio personalizado con el botón Copiar código resaltado":::

1. En una pestaña o ventana del explorador independientes, inicie sesión en el sitio web de su proveedor de dominio.

1. Busque la página de administración de registros DNS. Cada proveedor de dominios tiene su propia interfaz de registros DNS, así que consulte la documentación del proveedor. Busque áreas del sitio etiquetadas como **Nombre de dominio**, **DNS** o **Administración del servidor del nombres**.

   > [!NOTE]
   > A menudo, se puede encontrar la página de registros DNS al ver la información de la cuenta y al buscar un vínculo, como **Mis dominios**. Vaya a dicha página y, después, busque un vínculo que se llame algo similar a **Zone file**, **DNS Records** o **Advanced configuration**.

1. Cree un registro **TXT** con los siguientes valores.

   | Configuración             | Valor                                                                        |
   | ------------------- | ---------------------------------------------------------------------------- |
   | Tipo                | TXT                                                                          |
   | administrador de flujos de trabajo                | `@` para un dominio raíz o especifique `_dnsauth.<YOUR_SUBDOMAIN>` para el subdominio.      |
   | Valor               | Pegar el código desde el Portapapeles                                           |
   | TTL (si corresponde) | Deje el valor predeterminado                                                       |

> [!NOTE]
> Algunos proveedores de DNS usan una convención diferente a "@" to indicate a root domain or they change the "@" para el dominio raíz (es decir, mydomain.com) automáticamente. Esto es lo esperado y el proceso de validación seguirá funcionando.

[!INCLUDE [create repository from template](../../includes/static-web-apps-validate-txt.md)]

---

## <a name="create-an-alias-record"></a>Creación de un registro ALIAS

Los registros ALIAS asignan un dominio a otro. Se usan específicamente para dominios raíz (es decir, `mydomain.com`). En esta sección, creará un registro ALIAS que asigna un dominio raíz a la dirección URL generada automáticamente de la aplicación web estática.

# <a name="azure-dns"></a>[DNS de Azure](#tab/azure-dns)

> [!IMPORTANT]
> La zona de Azure DNS debe estar en la misma suscripción que la aplicación web estática.

1. Abra la zona de Azure DNS del dominio en Azure Portal.

1. Seleccione el botón **+ Conjunto de registros**.

1. Cree un conjunto de registros **D** con los siguientes valores.

   | Configuración          | Valor                              |
   | ---------------- | ---------------------------------- |
   | Nombre             | @                                  |
   | Tipo             | D: registro Alias para dirección IPv4   |
   | Conjunto de registros de alias | Sí                                |
   | Tipo de alias       | Recurso de Azure                     |
   | Suscripción     | \<Your Subscription>               |
   | Recurso de Azure   | \<Your Static Web App>             |
   | TTL              | Deje el valor predeterminado             |
   | Unidad de TTL         | Deje el valor predeterminado             |

1. Seleccione **Aceptar**.

   :::image type="content" source="media/custom-domain/azure-dns-alias.png" alt-text="Pantalla Conjunto de registros de Azure DNS con los campos de nombre, tipo, alias y recurso resaltados":::

Ahora que el dominio raíz está configurado, el proveedor de DNS puede tardar varias horas en propagar los cambios en todo el mundo.

# <a name="other-dns"></a>[Otro DNS](#tab/other-dns)

> [!IMPORTANT]
> El proveedor de dominios debe admitir registros [ALIAS](../dns/dns-alias.md) o ANAME, o bien el acoplamiento de CNAME.

1. Abra la aplicación web estática en [Azure Portal](https://portal.azure.com).

1. Seleccione **Dominio personalizados** en el menú.

1. Copie la dirección URL generada automáticamente de la aplicación web estática de la pantalla de dominio personalizado.

   :::image type="content" source="media/custom-domain/auto-generated.png" alt-text="Página de información general de una aplicación web estática con el icono de copia de dirección URL resaltado":::

1. Inicie sesión en el sitio web de su proveedor de dominios.

1. Busque la página de administración de registros DNS. Cada proveedor de dominios tiene su propia interfaz de registros DNS, así que consulte la documentación del proveedor. Busque áreas del sitio etiquetadas como **Nombre de dominio**, **DNS** o **Administración del servidor del nombres**.

   > [!NOTE]
   > A menudo, se puede encontrar la página de registros DNS al ver la información de la cuenta y al buscar un vínculo, como **Mis dominios**. Vaya a dicha página y, después, busque un vínculo que se llame algo similar a **Zone file**, **DNS Records** o **Advanced configuration**.

1. Cree un registro **ALIAS** con los siguientes valores...

   | Configuración             | Valor                                                          |
   | ------------------- | -------------------------------------------------------------- |
   | Tipo                | ALIAS o ANAME (use CNAME SI ALIAS no está disponible)                    |
   | Host                | @                                                              |
   | Value               | Pegue el nombre de dominio del Portapapeles                      |
   | TTL (si corresponde) | Deje el valor predeterminado                                         |

> [!IMPORTANT]
> Si el proveedor de dominios no ofrece un tipo de registro ALIAS o ANAME, use un tipo CNAME. Muchos proveedores ofrecen la misma funcionalidad que el tipo de registro ALIAS a través del tipo de registro CNAME y una característica denominada "Acoplamiento de CNAME".

Ahora que el dominio raíz está configurado, el proveedor de DNS puede tardar varias horas en propagar los cambios en todo el mundo.

---

## <a name="redirect-requests-to-a-default-domain"></a>Redirección de solicitudes a un dominio predeterminado

Se puede acceder a la aplicación web estática mediante su dominio generado automáticamente y cualquier dominio personalizado que haya configurado. Opcionalmente, puede configurar la aplicación para redirigir todo el tráfico a un dominio predeterminado.

### <a name="set-a-default-domain"></a>Establecimiento de un dominio predeterminado

Al designar un dominio personalizado como dominio predeterminado de la aplicación, las solicitudes a otros dominios se redirigen automáticamente al dominio predeterminado. Solo se puede establecer un dominio personalizado como predeterminado.

Siga estos pasos para establecer un dominio personalizado como predeterminado.

1. Con la aplicación web estática abierta en Azure Portal, seleccione **Dominios personalizados** en el menú.

1. Seleccione el dominio personalizado que quiere configurar como dominio predeterminado.

1. Seleccione **Predeterminado**.

   :::image type="content" source="media/custom-domain/set-default.png" alt-text="Establecimiento de un dominio personalizado como predeterminado":::

1. Una vez completada la operación, actualice la tabla para confirmar que el dominio está marcado como "predeterminado".

### <a name="unset-a-default-domain"></a>Anulación de un dominio predeterminado

Para detener los dominios que redirigen a un dominio predeterminado, siga estos pasos.

1. Con la aplicación web estática abierta en Azure Portal, seleccione **Dominios personalizados** en el menú.

1. Seleccione el dominio personalizado que configuró como predeterminado.

1. Seleccione **Unset default** (Anular predeterminado).

1. Una vez completada la operación, actualice la tabla para confirmar que ningún dominio está marcado como "predeterminado".

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configuración de aplicaciones](application-settings.md)
