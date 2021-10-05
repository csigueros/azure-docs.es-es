---
title: Implementación de SAP S/4 HANA o BW/4 HANA en una máquina virtual de Azure | Microsoft Docs
description: Implementación de SAP S/4HANA o BW/4HANA en una máquina virtual de Azure
services: virtual-machines-linux
documentationcenter: ''
author: hobru
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/26/2021
ms.author: hobruche
ms.openlocfilehash: 71dde25230c2a474a42872f7b2830c60439738c7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630781"
---
# <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library

[SAP Cloud Appliance Library](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) permite crear rápidamente un entorno de demostración con un sistema SAP totalmente preconfigurado. Con unos pocos clics, puede hacer que el sistema SAP esté en funcionamiento. Los vínculos siguientes resaltan varias soluciones que puede implementar rápidamente en Azure. Solo tiene que seleccionar el vínculo "Crear instancia". 

Tendrá que autenticarse con S-User o P-User. Puede crear P-User de forma gratuita por medio de [SAP Community](https://community.sap.com/).  A continuación encontrará más información.

| Solución | Vínculo |
| -------------- | :--------- | 
| **SAP S/4HANA 2020 FPS02, dispositivo totalmente activado**  27 de julio de 2021 | [Create Instance](https://cal.sap.com/registration?sguid=d48af08b-e2c6-4409-82f8-e42d5610e918&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Este dispositivo contiene SAP S/4HANA 2020 (FPS02) con procedimientos recomendados de SAP activados previamente para las funciones principales de SAP S/4HANA y otros escenarios para Service, Master Data Governance (MDG), Transportation Mgmt. (TM), Portfolio Mgmt. (PPM), Human Capital Management (HCM), Analytics, Migration Cockpit, etc. El acceso de usuario se realiza mediante SAP Fiori, SAP GUI, SAP HANA Studio, escritorio remoto de Windows o el sistema operativo de back-end para un acceso administrativo completo. |  [Detalles]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/d48af08b-e2c6-4409-82f8-e42d5610e918) |
| **SAP S/4HANA 2020 FPS01, dispositivo completamente activado**  20 de abril de 2021 | [Create Instance](https://cal.sap.com/registration?sguid=a0b63a18-0fd3-4d88-bbb9-4f02c13dc343&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Este dispositivo contiene SAP S/4HANA 2020 (FPS01) con procedimientos recomendados de SAP activados previamente para las funciones principales de SAP S/4HANA y otros escenarios para Service, Master Data Governance (MDG), Transportation Mgmt. (TM), Portfolio Mgmt. (PPM), Human Capital Management (HCM), Analytics, Migration Cockpit, etc. El acceso de usuario se realiza mediante SAP Fiori, SAP GUI, SAP HANA Studio, escritorio remoto de Windows o el sistema operativo de back-end para un acceso administrativo completo. |  [Detalles](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/a0b63a18-0fd3-4d88-bbb9-4f02c13dc343) | 
| **SAP S/4HANA 2020 FPS02**  10 de junio de 2021 | [Create Instance](https://cal.sap.com/registration?sguid=c7cff775-cbf7-4cd1-a907-6eeca95a0946&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
| Esta solución se incluye como una instalación estándar del sistema S/4HANA e incluye un escritorio remoto para facilitar el acceso de front-end. Contiene una interfaz de usuario de SAP S/4HANA Fiori preconfigurada y activada en el cliente 100, con los componentes de requisitos previos activados según la nota de SAP 3045635 Activación rápida para SAP Fiori en SAP S/4HANA 2020 FPS02. Vea el vínculo Más información. | [Detalles](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/c7cff775-cbf7-4cd1-a907-6eeca95a0946) | 
| **SAP BW/4HANA 2.0 SP07 incluido BW/4HANA Content 2.0 SP06**   24 de febrero de 2020 | [Create Instance](https://cal.sap.com/registration?sguid=0f2f20f4-d012-4f76-81af-6ff15063db66&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Esta solución ofrece una visión general de SAP BW/4HANA. SAP BW/4HANA es la nueva generación de Data Warehouse optimizada para HANA. Además de las opciones básicas de BW/4HANA, la solución ofrece abundante contenido de BW/4HANA optimizado para HANA y el siguiente paso de Escenarios híbridos con SAP Data Warehouse Cloud. Como el sistema está preconfigurado, puede empezar a implementar directamente los escenarios.| [Detalles](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/0f2f20f4-d012-4f76-81af-6ff15063db66) | 
| **SAP Business One 10.0 PL02, versión para SAP HANA** 4 de agosto de 2020  | [Create Instance](https://cal.sap.com/registration?sguid=371edc8c-56c6-4d21-acb4-2d734722c712&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Con la confianza de más de 70 000 pequeñas y medianas empresas en más de 170 países, SAP Business One es una solución ERP flexible, asequible y escalable con la potencia de SAP HANA. La solución está preconfigurada mediante una licencia de prueba de 31 días y tiene una base de datos de demostración preinstalada que podrá elegir. Consulte la guía de introducción para obtener información sobre el ámbito de la solución y cómo agregar fácilmente nuevas bases de datos de demostración. |  [Detalles](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/371edc8c-56c6-4d21-acb4-2d734722c712) |
| **Detector de información para SAP Data Custodian v2106** 30 de agosto de 2021  | [Create Instance](https://cal.sap.com/registration?sguid=db44680c-8a2a-405d-8963-838db38fa7dd&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|El detector de información para SAP Data Custodian se puede usar para automatizar el etiquetado de datos de los recursos en la nube. Los detectores de información buscan en los recursos de infraestructura y determinan si contienen determinados tipos de información. |  [Detalles](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/db44680c-8a2a-405d-8963-838db38fa7dd) |
| **SAP Yard Logistics 2009 for SAP S/4HANA**  28 de julio de 2021 | [Create Instance](https://cal.sap.com/registration?sguid=9cdf4f13-73a5-4743-a213-82e0d1a68742&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Ejecute una logística de cadena de suministro más eficaz y rentable con la aplicación SAP Yard Logistics. Maximice la visibilidad de todos los procesos y vistas previas de las cargas de trabajo planeadas con una variedad de herramientas de visualización y creación de informes, para que pueda optimizar el uso de los recursos y admitir las tareas de planificación, ejecución y facturación con un único sistema.|  [Detalles](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/9cdf4f13-73a5-4743-a213-82e0d1a68742) | 
| **SAP S/4HANA 2020 FPS02, dispositivo totalmente activado**  27 de julio de 2021 | [Create Instance](https://cal.sap.com/registration?sguid=d48af08b-e2c6-4409-82f8-e42d5610e918&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) | 
|Esta solución se incluye como una instalación estándar del sistema S/4HANA e incluye un escritorio remoto para facilitar el acceso de front-end. Contiene una interfaz de usuario de SAP S/4HANA Fiori preconfigurada y activada en el cliente 100, con los componentes de requisitos previos activados según la nota de SAP 3045635 Activación rápida para SAP Fiori en SAP S/4HANA 2020 FPS02. Vea el vínculo Más información.| [Detalles](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/d48af08b-e2c6-4409-82f8-e42d5610e918) | 
| **SAP Focused Run 3.0 FP01, sin configurar**  21 de julio de 2021 | [Create Instance](https://cal.sap.com/registration?sguid=82bdb96e-3578-41aa-a3e1-a6d9a8335ae1&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Focused Run está diseñado específicamente para empresas que necesitan supervisión, alertas y análisis de aplicaciones y sistemas de gran volumen. Es una solución eficaz para los proveedores de servicios que quieren hospedar a todos los clientes en un entorno central, escalable, seguro y automatizado. También aborda las necesidades avanzadas de los clientes relacionadas con la administración del sistema, la supervisión de usuarios, la supervisión de integración y el análisis de configuración y seguridad.|  [Detalles](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/82bdb96e-3578-41aa-a3e1-a6d9a8335ae1) | 
| **SAP S/4HANA 2020 FPS01 Utilities Trial**  21 de julio de 2021 | [Create Instance](https://cal.sap.com/registration?sguid=68785eeb-a228-4aa8-8273-b4c30775590c&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Esta solución permite crear un sistema de utilidades de SAP S/4HANA 2020 propio y obtener experiencia práctica, incluido el acceso de administrador completo a todas las áreas. Las visitas guiadas seleccionadas le ayudarán a comprender el procesamiento optimizado de los datos de medición, el proceso de facturación simplificado por medio de interfaces de usuario FIORI basadas en roles y la oferta de servicio al cliente específica del sector en Customer Engagement.|  [Detalles](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/68785eeb-a228-4aa8-8273-b4c30775590c)| 
| **SAP Product Lifecycle Costing 4.0 SP3 Hotfix 2**  1 de agosto de 2021 | [Create Instance](https://cal.sap.com/registration?sguid=f2bf191a-7efc-48a2-b8ac-51756eb225bc&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Product Lifecycle Costing es una solución para calcular los costos y otras dimensiones de los nuevos productos o las ofertas relacionadas con los productos en una fase inicial de su ciclo de vida, para identificar rápidamente los factores de costos y simular y comparar las alternativas.|  [Detalles](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/f2bf191a-7efc-48a2-b8ac-51756eb225bc)| 




---







## <a name="setup-and-get-started-with-sap-cloud-appliance-library"></a>Configuración e introducción a SAP Cloud Appliance Library

> [!NOTE]
> Para más información sobre SAP CAL, vaya al sitio web de [SAP Cloud Appliance Library](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8). SAP también tiene un blog acerca de [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).

> [!NOTE]
> A partir del 29 de mayo de 2017 puede usar el modelo de implementación mediante Azure Resource Manager, además del modelo de implementación clásico, que se usa menos, para implementar SAP CAL. Se recomienda usar el nuevo modelo de implementación mediante Resource Manager, en lugar del modelo de implementación clásico.

## <a name="create-an-account-in-the-sap-cal"></a>Creación de una cuenta en SAP CAL
1. La primera vez que inicie sesión en SAP CAL, utilice S-User de SAP, o cualquier otro usuario registrado de SAP. A continuación, defina una cuenta de SAP CAL que SAP CAL use para implementar aplicaciones en Azure. En la definición de la cuenta, debe:

    a. Seleccione el modelo de implementación en Azure (Resource Manager o clásico).

    b. Especifique la suscripción a Azure. Una cuenta de SAP CAL no se puede asignar a más de una suscripción. Si necesita más de una suscripción, debe crear otra cuenta de SAP CAL.

    c. Conceda el permiso de SAP CAL para realizar la implementación en la suscripción de Azure.

    > [!NOTE]
    > Los pasos siguientes muestran cómo crear una cuenta de SAP CAL para las implementaciones mediante Resource Manager. Si ya tiene una cuenta de SAP CAL vinculada al modelo de implementación clásico, *tendrá que* seguir estos pasos para crear una cuenta de SAP CAL nueva. La nueva cuenta de SAP CAL se tiene que implementar mediante el modelo de Resource Manager.

2. Cree una nueva cuenta de SAP CAL. La página **Cuentas** muestra tres opciones de Azure: 

    a. **Microsoft Azure (classic)** es el modelo de implementación clásico y ya no se suele usar.

    b. **Microsoft Azure** es el nuevo modelo de implementación mediante Resource Manager.

    c. **Windows Azure operado por 21Vianet** es una opción en China que usa el modelo de implementación clásica.

    Para implementar mediante el modelo de Resource Manager, seleccione **Microsoft Azure**.

    ![Detalles de la cuenta de SAP CAL](./media/cal-s4h/s4h-pic-2a.png)

3. En el campo **Suscription ID**, especifique el identificador de suscripción de Azure, que se puede encontrar en Azure Portal.

   ![Cuentas de SAP CAL](./media/cal-s4h/s4h-pic3c.png)

4. Para autorizar que SAP CAL se implemente en la suscripción de Azure que ha definido, haga clic en **Authorize** (Autorizar). La siguiente página aparecerá en la pestaña del explorador:

   ![Inicio de sesión de servicios en la nube de Internet Explorer](./media/cal-s4h/s4h-pic4c.png)

5. Si se enumera más de un usuario, elija la cuenta de Microsoft vinculada para ser el coadministrador de la suscripción de Azure que ha seleccionado. La siguiente página aparecerá en la pestaña del explorador:

   ![Confirmación de servicios en la nube de Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

6. Haga clic en **Aceptar**. Si la autorización es correcta, vuelve a mostrarse la definición de la cuenta de SAP CAL. Poco después, un mensaje confirma que el proceso de autorización se ha realizado correctamente.

7. Para asignar la cuenta de SAP CAL recién creada al usuario, escriba en el cuadro de texto de la derecha su **identificador de usuario** y haga clic en **Agregar**.

   ![Asociación de cuenta a usuario](./media/cal-s4h/s4h-pic8a.png)

8. Para asociar una cuenta con el usuario que utiliza para iniciar sesión en SAP CAL, haga clic en **Review** (Revisar). 
 
9. Para crear la asociación entre el usuario y la cuenta de SAP CAL recién creada, haga clic en **Create** (Crear).

   ![Asociación de usuario a cuenta de SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Ha creado correctamente una cuenta de SAP CAL que puede:

- Usar el modelo de implementación mediante Resource Manager.
- Implementar sistemas de SAP en su suscripción a Azure.

Ya puede empezar a implementar S/4HANA en su suscripción de usuario en Azure.

> [!NOTE]
> Antes de continuar, determine si tiene las cuotas de núcleos de Azure necesarias. Algunas soluciones de SAP CAL usan máquinas virtuales de la serie M de Azure para implementar algunas de las soluciones basadas en SAP HANA. Es posible que la suscripción de Azure no tenga ninguna cuota de núcleos para la serie H. Si es así, es posible que tenga que ponerse en contacto con el soporte técnico de Azure para obtener una cuota necesaria.

> [!NOTE]
> Al implementar una solución de Azure en SAP CAL, es posible que solo pueda elegir una región de Azure. Para implementar en otras regiones de Azure distintas a la que sugiere SAP CAL, debe adquirir una suscripción de CAL desde SAP. Es posible que también necesite abrir un mensaje con SAP para habilitar su cuenta de CAL para entregar en regiones de Azure distintas de las que se sugirieron inicialmente.

## <a name="deploy-a-solution"></a>Implementación de una solución

Vamos a implementar una solución desde la página **Soluciones** de SAP CAL. SAP CAL tiene dos secuencias para implementar:

- Una secuencia básica que utiliza una página para definir el sistema que se va a implementar
- Una secuencia avanzada que le ofrece varias opciones en relación al tamaño de las máquinas virtuales 

Aquí se muestra la ruta de acceso básica a la implementación.

1. En la página **Detalles de la cuenta**, debe:

    a. Seleccionar una cuenta de SAP CAL. (Utilice una cuenta asociada para implementar con el modelo de implementación de Resource Manager).

    b. Especificar en **Name** el nombre de la instancia.

    c. Seleccionar una **región** de Azure. SAP CAL sugiere una región. Si necesita otra región de Azure y no tiene una suscripción de SAP CAL, deberá solicitar una suscripción de CAL con SAP.

    d. Escriba una **contraseña** maestra para la solución que contenga ocho o nueve caracteres. La contraseña se usa para los administradores de los distintos componentes.

   ![SAP CAL Basic Mode: Create Instance](./media/cal-s4h/s4h-pic10a.png)

2. Haga clic en **Crear** y, en el cuadro de mensaje que aparece, haga clic en **Aceptar**.

   ![Tamaños de máquinas virtuales que admite SAP CAL](./media/cal-s4h/s4h-pic10b.png)

3. En el cuadro de diálogo **Clave privada**, haga clic en **Almacenar** para almacenar la clave privada en SAP CAL. Para usar la protección con contraseña para la clave privada, haga clic en **Descargar**. 

   ![Clave privada de SAP CAL](./media/cal-s4h/s4h-pic10c.png)

4. Lea el mensaje de **advertencia** de SAP CAL y haga clic en **Aceptar**.

   ![Advertencia de SAP CAL](./media/cal-s4h/s4h-pic10d.png)

    Ahora se realizará la implementación. Después de un tiempo, y en función del tamaño y la complejidad de la solución (SAP CAL ofrece una estimación), el estado pasa a mostrarse como activo y listo para usarse.

5. Para buscar las máquinas virtuales que se recopilan con los demás recursos asociados de un grupo de recursos, vaya a Azure Portal: 

   ![Objetos de SAP CAL implementados en el nuevo portal](./media/cal-s4h/sapcaldeplyment_portalview.png)

6. En el portal de SAP CAL, el estado aparece como **Activo**. Para conectarse a la solución, haga clic en **Conectar**. Se implementan diferentes opciones para conectarse a los distintos componentes en esta solución.

   ![Instancias de SAP CAL](./media/cal-s4h/active_solution.png)

7. Para poder usar una de las opciones para conectarse a los sistemas implementados, haga clic en **Guía de introducción**. 

   ![Conectarse a la instancia](./media/cal-s4h/connect_to_solution.png)

    La documentación asigna los nombres de los usuarios en cada uno de los métodos de conectividad. Las contraseñas de dichos usuarios se establecen en la contraseña maestra que se definió al comienzo del proceso de implementación. En la documentación, otros usuarios más funcionales se enumeran junto con sus contraseñas, que se pueden usar para iniciar sesión en el sistema implementado. 

    Por ejemplo, si utiliza la GUI de SAP que está preinstalada en la máquina del Escritorio remoto de Windows, puede que el sistema S/4 sea parecido al siguiente:

   ![SM50 en la GUI de SAP preinstalada](./media/cal-s4h/gui_sm50.png)

    O bien, si utiliza DBACockpit, puede que la instancia se parezca a la siguiente:
 

   ![SM50 en la GUI de SAP de DBACockpit](./media/cal-s4h/dbacockpit.png)

En unas horas, una aplicación SAP S/4 correcta se implementa en Azure.

Si ha adquirido una suscripción a SAP CAL, SAP admite totalmente implementaciones a través de SAP CAL en Azure. La cola de compatibilidad es BC-VCM-CAL.







