---
title: Ejecución de una fábrica de imágenes desde AzureDevOps
description: En este artículo se tratan todos los preparativos necesarios para ejecutar la fábrica de imágenes desde Azure DevOps (anteriormente Visual Studio Team Services).
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: bc12dc67160a68898191ab802cc9f6e9bd3f3f62
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604867"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Ejecución de una fábrica de imágenes desde AzureDevOps
En este artículo se tratan todos los preparativos necesarios para ejecutar la fábrica de imágenes desde Azure DevOps (anteriormente Visual Studio Team Services).

> [!NOTE]
> Cualquier motor de orquestación servirá. No es obligatorio usar Azure DevOps. La fábrica de imágenes se ejecuta con scripts de Azure PowerShell, así que se podría ejecutar manualmente, mediante el Programador de tareas de Windows, otros sistemas de CI/CD, etc.

## <a name="create-a-lab-for-the-image-factory"></a>Creación de un laboratorio para la fábrica de imágenes
El primer paso para configurar la fábrica de imágenes crear un laboratorio en Azure DevTest Labs. Este laboratorio es el laboratorio de la fábrica de imágenes donde se crearán las máquinas virtuales y se guardarán las imágenes personalizadas. Este laboratorio se considera parte del proceso general de la fábrica de imágenes. Después de crear un laboratorio, asegúrese de guardar el nombre, ya que lo necesitará más adelante.

## <a name="scripts-and-templates"></a>Scripts y plantillas
El siguiente paso para adoptar la fábrica de imágenes por su equipo es comprender lo que está disponible. Los scripts y las plantillas de la fábrica de imágenes están disponibles públicamente en el [repositorio de GitHub de DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Este es un resumen de las partes:

- Image Factory. Es la carpeta raíz.
    - Configuración. Las entradas a la fábrica de imágenes.
        - GoldenImages. Esta carpeta contiene archivos JSON que representan las definiciones de las imágenes personalizadas.
        - Labs.json. Archivo donde los equipos se registran para recibir imágenes personalizadas específicas.
- Scripts. El motor de la fábrica de imágenes.

En los artículos de esta sección se proporcionan más detalles sobre estos scripts y plantillas.

## <a name="create-an-azure-devops-team-project"></a>Creación de un proyecto de equipo de Azure DevOps
Azure DevOps le permiten almacenar el código fuente y ejecutar Azure PowerShell en un solo lugar. Puede programar ejecuciones periódicas para mantener actualizadas las imágenes. Se dispone de buenas herramientas para registrar los resultados y diagnosticar cualquier problema.  El uso de Azure DevOps no es un requisito; puede usar cualquier agente/motor que pueda conectarse a Azure y pueda ejecutar Azure PowerShell.

Si tiene una cuenta existente de DevOps o un proyecto que le gustaría usar en su lugar, omita este paso.

Para empezar, cree una cuenta gratuita en Azure DevOps. Visite https://www.visualstudio.com/ y seleccione **Comience a usarlo de manera gratuita** justo debajo de **Azure DevOps** (antes VSTS). Deberá elegir un nombre de cuenta único y asegurarse de elegir si quiere administrar código mediante Git. Una vez creada, guarde la dirección URL en el proyecto de equipo. Esta es una dirección URL de ejemplo: `https://<accountname>.visualstudio.com/MyFirstProject`.

## <a name="check-in-the-image-factory-to-git"></a>Inserción de la fábrica de imágenes en el repositorio de Git
Todos los elementos de PowerShell, plantillas y configuración de la fábrica de imágenes se encuentran en el [repositorio público de GitHub de DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). La manera más rápida de introducir el código en el nuevo proyecto de equipo es importar un repositorio. Como resultado, se extrae todo el repositorio de DevTest Labs (así que obtendrá documentos y ejemplos adicionales).

1. Visite el proyecto de Azure DevOps que creó en el paso anterior (la dirección URL es parecida a **https:\//\<accountname>.visualstudio.com/MyFirstProject**).
2. Seleccione **Import a Repository** (Importar un repositorio).
3. Escriba la **dirección URL de clonación** para el repositorio de DevTest Labs: `https://github.com/Azure/azure-devtestlab`.
4. Seleccione **Import** (Importar).

    ![Importación del repositorio de Git](./media/set-up-devops-lab/import-git-repo.png)

Si decide proteger insertar en el repositorio exactamente lo que necesita (los archivos de la fábrica de imágenes), siga [estos pasos](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) para clonar el repositorio de Git e insertar en el repositorio solo los archivos ubicados en el directorio **scripts/ImageFactory**.

## <a name="create-a-build-and-connect-to-azure"></a>Creación de una compilación y conexión a Azure
En este punto, tiene los archivos de origen almacenados en un repositorio de Git en Azure DevOps. Ahora, debe configurar una canalización para ejecutar Azure PowerShell. Hay muchas opciones para realizar estos pasos. En este artículo, se usará la definición de compilación por motivos de simplicidad, pero también sirve la compilación de DevOps, la versión de DevOps (de uno o varios entornos), otros motores de ejecución, como el Programador de tareas de Windows, o cualquier otra herramienta que pueda ejecutar Azure PowerShell.

> [!NOTE]
> Un punto importante a tener en cuenta es que algunos de los archivos de PowerShell tardan mucho tiempo en ejecutarse cuando hay muchas (más de 10) imágenes personalizadas para crear. Los agentes de compilación o versión de DevOps hospedados gratuitos tienen un tiempo de espera de 30 minutos, por lo que no puede usar este tipo de agente cuando empiece a compilar muchas imágenes. Esta inconveniencia del tiempo de expiración se aplica a cualquier herramienta que decida usar; así que es conveniente comprobar de antemano que puede ampliar los tiempos de expiración habituales para los scripts de larga ejecución de Azure PowerShell. En el caso de Azure DevOps, puede usar agentes hospedados de pago o su propio agente de compilación.

1. Para comenzar, seleccione **Set up Build** (Configurar compilación) en la página principal del proyecto de DevOps:

    ![Botón de configuración de compilación](./media/set-up-devops-lab/setup-build-button.png)
2. Especifique un **nombre** para la compilación (por ejemplo: Compilar y entregar imágenes en DevTest Labs).
3. Seleccione una definición de compilación **vacía** y seleccione **Apply** (Aplicar) para crear la compilación.
4. En esta fase, puede elegir **Hosted** (Hospedado) para el agente de compilación.
5. **Guarde** la definición de compilación.

    ![Definición de compilación](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Configuración de las variables de compilación
Para simplificar los parámetros de línea de comandos, encapsule los valores de clave que controlan la fábrica de imágenes en un conjunto de variables de compilación. Seleccione la pestaña **Variables** y verá una lista de varias variables predeterminadas. Esta es la lista de variables para escribir en Azure DevOps:


| Nombre de la variable | Value | Notas |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Es la ruta de acceso completa del repositorio a la carpeta **Configuration**. Si ha importado el repositorio completo anteriormente, el valor a la izquierda es correcto. En caso contrario, actualícelo para que apunte a la ubicación de configuración. |
| DevTestLabName | MyImageFactory | El nombre del laboratorio en Azure DevTest Labs usado como la fábrica para producir imágenes. Si no tiene uno, créelo. Asegúrese de que el laboratorio esté en la misma suscripción que el punto de conexión de servicio al que tiene acceso. |
| ImageRetention | 1 | El número de imágenes que quiere guardar de cada tipo. Establezca el valor predeterminado en 1. |
| MachinePassword | ******* | La contraseña de la cuenta de administrador integrada para las máquinas virtuales. Esta es una cuenta transitoria, así que asegúrese de que sea segura. Seleccione el pequeño icono de candado de la derecha para asegurarse de que es una cadena segura. |
| MachineUserName | ImageFactoryUser | El nombre de usuario de la cuenta de administrador integrada para las máquinas virtuales. Esta es una cuenta transitoria. |
| StandardTimeoutMinutes | 30 | El tiempo que se deben esperar las operaciones normales de Azure. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | El identificador de la suscripción donde existe el laboratorio y al que tiene acceso el punto de conexión de servicio. |
| VMSize | Standard_A3 | El tamaño de la máquina virtual que se usará en el paso de **creación**. Las máquinas virtuales creadas son transitorias. El tamaño debe ser aquel que está [habilitado para el laboratorio](devtest-lab-set-lab-policy.md). Confirme que hay suficiente [cuota de núcleos de suscripción](../azure-resource-manager/management/azure-subscription-service-limits.md).

![Variables de compilación](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Conexión con Azure
El siguiente paso es configurar la entidad de servicio. Se trata de una identidad de Azure Active Directory que permite que el agente de compilación de DevOps funcione en Azure en nombre del usuario. Para configurarlo, comience agregando el primero paso de compilación de Azure PowerShell.

1. Seleccione **Add Task** (Agregar tarea).
2. Busque **Azure PowerShell**.
3. Cuando lo encuentre, seleccione **Add** (Agregar) para agregar la tarea a la compilación. Al hacerlo, aparecerá la tarea en el lado izquierdo como agregada.

![Paso de configuración de PowerShell](./media/set-up-devops-lab/set-up-powershell-step.png)

La manera más rápida de configurar una entidad de servicio es dejar que lo haga Azure DevOps.

1. Seleccione la **tarea** que acaba de agregar.
2. En **Azure Connection Type** (Tipo de conexión de Azure), seleccione **Azure Resource Manager**.
3. Seleccione el vínculo **Manage** (Administrar) para configurar la entidad de servicio.

Para más información, consulte esta [entrada de blog](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Cuando seleccione el vínculo **Manage** (Administrar), aterrizará en el lugar correcto de DevOps (segunda captura de pantalla de la entrada de blog) para configurar la conexión a Azure. Durante este proceso, asegúrese de elegir **Azure Resource Manager Service Endpoint** (Punto de conexión de servicio de Azure Resource Manager).

## <a name="complete-the-build-task"></a>Creación de la tarea de compilación
Si selecciona la tarea de compilación, verá todos los detalles que se deben rellenar en el panel derecho.

1. En primer lugar, asigne un nombre a la tarea de compilación: **Crear máquinas virtuales**.
2. Elija la **entidad de servicio** que ha creado, para ello, seleccione **Azure Resource Manager**
3. Elija el **punto de conexión de servicio**.
4. En **Script Path** (Ruta de acceso del script), seleccione los puntos suspensivos ( **...** ) a la derecha.
5. Vaya al script **MakeGoldenImageVMs.ps1**.
6. Los parámetros del script deben tener este aspecto: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Creación de la definición de compilación](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Puesta en cola de la compilación
Para comprobar que tiene todo configurado correctamente, vamos a poner en cola una nueva compilación. Mientras se ejecuta la compilación, cambie a [Azure Portal](https://portal.azure.com) y, en **Todas las máquinas virtuales**, seleccione el laboratorio de la fábrica de imágenes para confirmar que todo funciona correctamente. Verá que se crean tres máquinas virtuales en el laboratorio.

![Máquinas virtuales del laboratorio](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Pasos siguientes
El primer paso para configurar la fábrica de imágenes basada en Azure DevTest Labs ha finalizado. En el siguiente artículo de la serie, se generalizarán esas máquinas virtuales y se guardarán en imágenes personalizadas. Luego, las distribuirá a todos los otros laboratorios. Consulte el siguiente artículo de la serie: [Guardar imágenes personalizadas y distribuirlas a varios laboratorios](image-factory-save-distribute-custom-images.md)
