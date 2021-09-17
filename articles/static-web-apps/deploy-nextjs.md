---
title: 'Tutorial: Implementación de sitios web de Next.js representados de forma estática en Azure Static Web Apps'
description: Genere e implemente sitios dinámicos de Next.js con Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 08/05/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 1f8ef3146ce7ef1b1767c04284ddbdb191b50d81
ms.sourcegitcommit: d01c2b2719e363178720003b67b968ac2a640204
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122455908"
---
# <a name="deploy-static-rendered-nextjs-websites-on-azure-static-web-apps"></a>Implementación de sitios web de Next.js representados de forma estática en Azure Static Web Apps

En este tutorial, aprenderá a implementar un sitio web estático generado por [Next.js](https://nextjs.org) en [Azure Static Web Apps](overview.md). Para obtener más información sobre aspectos específicos de Next.js, consulte el archivo [Léame de la plantilla de inicio](https://github.com/staticwebdev/nextjs-starter#readme).

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/).
- Una cuenta de GitHub. [Cree una cuenta gratuita](https://github.com/join).
- [Node.js](https://nodejs.org) instalado.

## <a name="set-up-a-nextjs-app"></a>Configuración de una aplicación de Next.js

En lugar de usar la CLI de Next.js para crear la aplicación, puede usar un repositorio de inicio. El repositorio de inicio contiene una aplicación de Next.js existente que admite rutas dinámicas.

Para comenzar, cree un repositorio en su cuenta de GitHub desde un repositorio de plantillas.

1. Vaya a [https://github.com/staticwebdev/nextjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nextjs-starter/generate).
1. Asigne el nombre **nextjs-starter** al repositorio.
1. A continuación, clone el nuevo repositorio en la máquina. Asegúrese de reemplazar `<YOUR_GITHUB_ACCOUNT_NAME>` por el nombre de su cuenta.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. Vaya a la aplicación de Next.js recién clonada:

   ```bash
   cd nextjs-starter
   ```

1. Instale las dependencias:

    ```bash
    npm install
    ```

1. Inicie la aplicación de Next.js en el entorno de desarrollo:

    ```bash
    npm run dev
    ```

Vaya a `http://localhost:3000` para abrir la aplicación, donde debería ver el siguiente sitio web abierto en el explorador de su preferencia:

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="Inicio de la aplicación de Next.js":::

Al seleccionar un marco o biblioteca, debería ver una página de detalles acerca del elemento seleccionado:

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="Página de detalles":::

## <a name="deploy-your-static-website"></a>Implementación del sitio web estático

En los pasos siguientes, se muestra cómo vincular la aplicación a Azure Static Web Apps. Una vez que esté en Azure, podrá implementar la aplicación en un entorno de producción.

### <a name="create-a-static-app"></a>Creación de una aplicación estática

1. Acceda a [Azure Portal](https://portal.azure.com).
1. Seleccione **Crear un recurso**.
1. Busque **Static Web Apps**.
1. Seleccione **Static Web Apps**.
1. Seleccione **Crear**.
1. En la pestaña _Datos básicos_, especifique los valores siguientes.

    | Propiedad | Value |
    | --- | --- |
    | _Suscripción_ | El nombre de la suscripción de Azure. |
    | _Grupos de recursos_ | **my-nextjs-group**  |
    | _Nombre_ | **my-nextjs-app** |
    | _Tipo de plan_ | **Gratis** |
    | _Región para la API y los entornos de ensayo de Azure Functions_ | Seleccione la región más cercana a la suya. |
    | _Origen_ | **GitHub** |

1. Seleccione **Iniciar sesión con GitHub** y autentíquese con GitHub.

1. Escriba los siguientes valores de GitHub.

    | Propiedad | Value |
    | --- | --- |
    | _Organización_ | Seleccione la organización de GitHub adecuada. |
    | _Repositorio_ | Seleccione **nextjs-starter**. |
    | _Rama_ | Seleccione **main** (principal). |

1. En la sección _Detalles de la compilación_, seleccione **Personalizado** en _Valores preestablecidos de compilación_. Agregue los siguientes valores para la configuración de compilación.

    | Propiedad | Valor |
    | --- | --- |
    | _Ubicación de la aplicación_ | Escriba **/** en el cuadro. |
    | _Ubicación de la API_ | Deje este cuadro vacío. |
    | _Ubicación de salida_ | Escriba **out** (salida) en el cuadro. |

### <a name="review-and-create"></a>Revisar y crear

1. Seleccione el botón **Revisar y crear** para comprobar que todos los detalles sean correctos.

1. Seleccione **Crear** para comenzar la creación de la aplicación web estática de App Service y aprovisionar una Acción de GitHub para la implementación.

1. Cuando finalice la implementación, seleccione **Ir al recurso**.

1. En la ventana _Información general_, seleccione el vínculo *Dirección URL* para abrir la aplicación implementada.

Si el sitio web no se carga inmediatamente, la compilación todavía se está ejecutando. Una vez que se complete el flujo de trabajo, puede actualizar el explorador para ver la aplicación web.

Para comprobar el estado del flujo de trabajo Acciones, vaya al panel Acciones del repositorio:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

Ahora, los cambios realizados en la rama `main` inician una nueva compilación e implementación del sitio web.

### <a name="sync-changes"></a>Sincronización de cambios

Al crear la aplicación, Azure Static Web Apps creó un archivo de Acciones de GitHub en el repositorio. Sincronice con el servidor mediante la extracción de la versión más reciente en el repositorio local.

Vuelva al terminal y ejecute el siguiente comando: `git pull origin main`.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, puede eliminar la instancia de Azure Static Web Apps mediante los siguientes pasos:

1. Abra [Azure Portal](https://portal.azure.com).
1. Busque **my-nextjs-group** en la barra de búsqueda superior.
1. Seleccione el nombre del grupo.
1. Seleccione el botón **Eliminar**.
1. Seleccione **Sí** para confirmar la acción de eliminación.

> [!div class="nextstepaction"]
> [Configuración de un dominio personalizado](custom-domain.md)
