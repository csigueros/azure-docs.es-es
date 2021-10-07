---
title: Tipos de clase de ejemplo en Azure Lab Services | Microsoft Docs
description: Proporciona algunos tipos de clases para los que puede configurar laboratorios mediante Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 086f5eebb6ef96b1846c2f2777b045821b0418c8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736630"
---
# <a name="class-types-overview---azure-lab-services"></a>Información general sobre tipos de clase: Azure Lab Services

Azure Lab Services le permite configurar rápidamente un entorno de laboratorio educativo en la nube. En los artículos de esta sección se proporcionan instrucciones sobre cómo configurar varios tipos de laboratorios mediante Azure Lab Services.

## <a name="adobe-creative-cloud"></a>Adobe Creative Cloud

La colección [Adobe Creative Cloud](https://www.adobe.com/creativecloud.html) de aplicaciones se usa normalmente en clases de artes digitales y medios.  

Para obtener más información sobre cómo configurar este tipo de laboratorio, consulte [Configuración de un laboratorio de Adobe Creative Cloud](class-type-adobe-creative-cloud.md).

## <a name="arcgis"></a>ArcGIS

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) es un tipo de sistema de información geográfica (GIS).  Puede configurar un laboratorio que use distintas aplicaciones de ArcGIS Desktop, como [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm), para crear, editar y analizar mapas 2D.

Para más información sobre cómo configurar este tipo de laboratorio, consulte [Configuración de un laboratorio de ArcMap\ArcGIS Desktop](class-type-arcgis.md).

## <a name="autodesk"></a>AutoDesk

[Autodesk](https://www.autodesk.com/) ofrece soluciones de software para arquitectura, ingeniería, construcción, diseño, fabricación, etc.  Estas soluciones se usan normalmente en clases de ingeniería y en el plan de estudios [Project Lead the Way](class-type-pltw.md).

Para obtener más información sobre cómo configurar este tipo de laboratorio, consulte [AutoDesk](class-type-autodesk.md).

## <a name="big-data-analytics"></a>Análisis de macrodatos

Puede configurar un laboratorio de GPU para enseñar una clase de análisis de macrodatos. Con este tipo de clase, los alumnos aprenden a manipular grandes volúmenes de datos y a aplicar algoritmos de aprendizaje automático y estadístico para obtener conclusiones sobre los datos. Un objetivo clave para los alumnos es aprender a usar herramientas de análisis de datos, como el paquete de software de código abierto de Apache Hadoop, que proporciona herramientas para almacenar, administrar y procesar macrodatos. 

Para obtener información detallada sobre cómo configurar este tipo de laboratorio, consulte [Configuración de un laboratorio para el análisis de macrodatos mediante una implementación de Docker de HortonWorks Data Platform](class-type-big-data-analytics.md).

## <a name="database-management"></a>Administración de bases de datos

Los conceptos de las bases de datos son uno de los cursos introductorios en la mayoría de los departamentos de informática de las universidades. Puede configurar un laboratorio para una clase de administración de bases de datos básica en Azure Lab Services. Por ejemplo, puede configurar una plantilla de máquina virtual en un laboratorio con un servidor de base de datos [MySQL](https://www.mysql.com/) o un servidor de [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019).

Para más información sobre cómo configurar este tipo de laboratorio, consulte [Configuración de un laboratorio para enseñar administración de bases de datos relacionales](class-type-database-management.md).

## <a name="deep-learning-in-natural-language-processing"></a>Aprendizaje profundo en el procesamiento del lenguaje natural

Puede configurar un laboratorio centrado en el aprendizaje profundo en el procesamiento de lenguaje natural (NLP) mediante Azure Lab Services. El procesamiento de lenguaje natural (NLP) es una forma de inteligencia artificial (AI) que permite a los equipos utilizar la traducción, el reconocimiento de voz y otras capacidades de comprensión de lenguajes. Los alumnos que sigan una clase de NLP obtienen una máquina virtual Linux para aprender a aplicar algoritmos de red neuronal para desarrollar modelos de aprendizaje profundo que se utilizan para analizar el lenguaje humano escrito.

Para más información sobre cómo configurar este tipo de laboratorio, consulte [Configuración de un laboratorio centrado en el aprendizaje profundo en el procesamiento de lenguaje natural mediante Azure Lab Services](class-type-deep-learning-natural-language-processing.md).

## <a name="ethical-hacking-with-hyper-v"></a>Piratería ética con Hyper-V

Puede configurar un laboratorio para una clase que se centre en la parte forense de la piratería ética. Las pruebas de penetración son una práctica que usa la comunidad de piratería ética y que se producen cuando alguien intenta obtener acceso al sistema o a la red para mostrar los puntos vulnerables que un atacante malintencionado podría aprovechar.

En una clase de piratería ética, los alumnos pueden aprender técnicas modernas para protegerse frente a los puntos vulnerables. Cada alumno obtiene una máquina virtual host de Windows Server que tiene dos máquinas virtuales anidadas: una máquina virtual con una imagen de [Metasploitable3](https://github.com/rapid7/metasploitable3) y otra con una imagen de [Kali Linux](https://www.kali.org/). La máquina virtual Metasploitable se usa para la explotación.  La máquina virtual Kali de Linux proporciona acceso a las herramientas necesarias para ejecutar tareas forenses.

Para más información sobre cómo configurar este tipo de laboratorio, consulte [Configuración de un laboratorio para impartir una clase de piratería ética](class-type-ethical-hacking.md).

## <a name="ethical-hacking-with-virtualbox"></a>Piratería ética con VirtualBox

Puede configurar un laboratorio para una clase que se centre en la parte forense de la piratería ética. Las pruebas de penetración son una práctica que usa la comunidad de piratería ética y que se producen cuando alguien intenta obtener acceso al sistema o a la red para mostrar los puntos vulnerables que un atacante malintencionado podría aprovechar.

En una clase de piratería ética, los alumnos pueden aprender técnicas modernas para protegerse frente a los puntos vulnerables. Cada alumno obtiene una máquina virtual host de Windows Server que tiene dos máquinas virtuales anidadas: una con una imagen de [SEED Lab](https://seedsecuritylabs.org/) y otra con una imagen de [Kali Linux](https://www.kali.org/). La máquina virtual SEED se usa con fines de vulnerabilidad de seguridad.  La máquina virtual Kali de Linux proporciona acceso a las herramientas necesarias para ejecutar tareas forenses.

Para más información sobre cómo configurar este tipo de laboratorio, consulte [Configuración de un laboratorio para impartir una clase de piratería ética](class-type-ethical-hacking-virtualbox.md).

## <a name="matlab"></a>MATLAB

[MATLAB](https://www.mathworks.com/products/matlab.html), que es la abreviatura de laboratorio de matrices, es una plataforma de programación de [MathWorks](https://www.mathworks.com/).  Combina la potencia del cálculo con una buena visualización, lo que lo convierte en una herramienta popular en los campos de matemáticas, ingeniería, física y química.

Para más información sobre cómo configurar este tipo de laboratorio, consulte [Configuración de un laboratorio para enseñar MATLAB](class-type-matlab.md).

## <a name="networking-with-gns3"></a>Redes con GNS3

Puede configurar un laboratorio para una clase orientado a permitir a los alumnos emular, configurar, probar y solucionar problemas de redes reales y virtuales mediante el software [GNS3](https://www.gns3.com/). 

Para más información sobre cómo configurar este tipo de laboratorio, consulte [Configuración de un laboratorio para impartir una clase de redes](class-type-networking-gns3.md).

## <a name="project-lead-the-way-pltw"></a>Project Lead the Way (PLTW)

[Project Lead the Way (PLTW)](https://www.pltw.org/) es una organización sin ánimo de lucro que proporciona un currículo de PreK-12 en informática, ingeniería y ciencia biomédica en los Estados Unidos.  En cada clase de PLTW, los alumnos usan varias aplicaciones de software como parte de su experiencia de aprendizaje práctico.

Para obtener información detallada sobre cómo configurar estos tipos de laboratorios, consulte [Configuración de laboratorios para las clases de Project Lead the Way](class-type-pltw.md).

## <a name="python-and-jupyter-notebooks"></a>Python y cuadernos de Jupyter Notebook

Puede configurar una máquina de plantilla en Azure Lab Services con las herramientas necesarias para enseñar a los alumnos cómo usar [cuadernos de Jupyter Notebook](http://jupyter-notebook.readthedocs.io). Jupyter Notebooks es un proyecto de código abierto que le permite combinar fácilmente texto enriquecido y código fuente [Python](https://www.python.org/) ejecutable en un solo lienzo denominado cuaderno. Al ejecutar un cuaderno, se genera un registro lineal de entradas y salidas.  Dichas salidas pueden incluir texto, tablas de información, gráficos de dispersión y mucho más.

Para más información sobre cómo configurar este tipo de laboratorio, consulte [Configuración de un laboratorio para enseñar ciencia de datos con Python y cuadernos de Jupyter Notebook](class-type-jupyter-notebook.md).

## <a name="react"></a>React

[React](https://reactjs.org/) es una popular biblioteca de JavaScript para crear interfaces de usuario (UI). React es una manera declarativa de crear componentes reutilizables para el sitio web.  Hay muchas bibliotecas populares para el desarrollo front-end basado en JavaScript.  Usaremos algunas de estas bibliotecas durante la creación de nuestro laboratorio.  [Redux](https://redux.js.org/) es una biblioteca que proporciona un contenedor de estado predecible para las aplicaciones JavaScript y que a menudo se usa en conjunto con React. [JSX](https://reactjs.org/docs/introducing-jsx.html) es una extensión de sintaxis de biblioteca para JavaScript que a menudo se usa con React para describir el aspecto de la interfaz de usuario.  [NodeJS](https://nodejs.org/) es una manera cómoda de ejecutar un servidor web para la aplicación React.

Para obtener información detallada sobre cómo configurar este tipo de laboratorio en Linux mediante [Visual Studio Code](https://code.visualstudio.com/) para el entorno de desarrollo, consulte [Configuración del laboratorio para React en Windows](class-type-react-linux.md).  Para obtener información detallada sobre cómo configurar este tipo de laboratorio en Windows mediante [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) para el entorno de desarrollo, consulte [Configuración del laboratorio para React en Windows](class-type-react-windows.md).

## <a name="rstudio"></a>RStudio

[R](https://www.r-project.org/about.html) es un lenguaje de código abierto que se usa en computación y gráficos estadísticos.  Se utiliza en el análisis estadístico de la genética, el procesamiento del lenguaje natural y el análisis de datos financieros.  R proporciona una experiencia de [línea de comandos interactiva](https://cran.r-project.org/doc/manuals/r-release/R-intro.html#Invoking-R-from-the-command-line).  [RStudio](https://www.rstudio.com/products/rstudio/) es un entorno de desarrollo interactivo (IDE) disponible para el lenguaje R.  La versión gratuita proporciona herramientas de edición de código, una experiencia de depuración integrada y herramientas de desarrollo de paquetes.  Este tipo de clase se centrará únicamente en RStudio y R como bloque de creación para una clase que requiere el uso de computación estadística.

Para más información sobre cómo configurar este tipo de laboratorio, consulte [Configuración de un laboratorio para enseñar R en Linux](class-type-rstudio-linux.md) o [Configuración de un laboratorio para enseñar R en Windows](class-type-rstudio-windows.md).

## <a name="shell-scripting-on-linux"></a>Generación de scripts en shell en Linux

Puede configurar un laboratorio para enseñar el scripting de shell en Linux. El scripting es una parte útil de la administración del sistema que permite a los administradores evitar tareas repetitivas. En este escenario de ejemplo, la clase cubre scripts bash tradicionales y scripts mejorados. Los scripts mejorados son scripts que combinan comandos bash y Ruby. Este enfoque permite a Ruby pasar datos y comandos bash para interactuar con el shell.

Los alumnos que sigan estas clases de scripting obtienen una máquina virtual Linux para conocer los aspectos básicos de Linux y también familiarizarse con el scripting de shell de bash. La máquina virtual Linux incluye el acceso al escritorio remoto habilitado y los editores de texto [Gedit](https://help.gnome.org/users/gedit/stable/) y [Visual Studio Code](https://code.visualstudio.com/) instalados.

Para más información sobre cómo configurar este tipo de laboratorio, consulte [Scripting de shell en Linux](class-type-shell-scripting-linux.md).

## <a name="solidworks-computer-aided-design-cad"></a>Diseño asistido por ordenador (CAD) de SolidWorks

Puede configurar un laboratorio de GPU que proporcione a los estudiantes de ingeniería acceso a [SolidWorks](https://www.solidworks.com/).  SolidWorks proporciona un entorno CAD 3D para el modelado de objetos sólidos.  Con SolidWorks, los ingenieros pueden crear, visualizar, simular y documentar sus diseños fácilmente.

Para más información sobre cómo configurar este tipo de laboratorio, consulte [Configuración de un laboratorio para clases de ingeniería mediante SolidWorks](class-type-solidworks.md).

## <a name="sql-database-and-management"></a>SQL Database y administración

SQL (Lenguaje de consulta estructurado) es el lenguaje estándar para la administración de bases de datos relacionales, lo que incluye agregar y administrar el contenido de una base de datos, así como acceder a él.  Puede configurar un laboratorio para enseñar conceptos de bases de datos mediante el servidor de base de datos [MySQL](https://www.mysql.com/) y el servidor [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019).

Para más información sobre cómo configurar este tipo de laboratorio, consulte [Configuración de un laboratorio para enseñar administración de bases de datos relacionales](class-type-database-management.md).

## <a name="next-steps"></a>Pasos siguientes

Vea los artículos siguientes:

- [Configuración de un laboratorio centrado en el aprendizaje profundo en el procesamiento de lenguaje natural mediante Azure Lab Services](class-type-deep-learning-natural-language-processing.md)
- [Configuración de un laboratorio para impartir una clase de redes](class-type-networking-gns3.md)
- [Configuración de un laboratorio para impartir una clase de piratería ética con Hyper-V](class-type-ethical-hacking.md)
- [Configuración de un laboratorio para impartir una clase de piratería ética con VirtualBox](class-type-ethical-hacking-virtualbox.md)
