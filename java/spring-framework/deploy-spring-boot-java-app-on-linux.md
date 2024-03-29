---
title: Implementación de una aplicación web de Spring Boot en Azure App Service for Container
description: Este tutorial le guiará por los pasos necesarios para implementar una aplicación Spring Boot como una aplicación web de Linux en Microsoft Azure.
services: azure app service
documentationcenter: java
author: bmitchell287
manager: douge
editor: ''
ms.assetid: ''
ms.author: brendm
ms.date: 12/19/2018
ms.devlang: java
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: mvc
ms.openlocfilehash: 93c67221748f354f2bf772a5f67903512a241063
ms.sourcegitcommit: f799dd4590dc5a5e646d7d50c9604a9975dadeb1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2019
ms.locfileid: "68691177"
---
# <a name="deploy-a-spring-boot-application-on-azure-app-service-for-container"></a>Implementación de una aplicación de Spring Boot en Azure App Service for Container

En este tutorial se explica cómo usar [Docker] para incluir su aplicación de [Spring Boot] en un contenedor e implementar su propia imagen de docker en un host Linux en [Azure App Service](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro).

## <a name="prerequisites"></a>Requisitos previos

Para realizar los pasos de este tutorial, necesitará tener los siguientes requisitos previos:

* Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN] o registrarse para obtener una [cuenta de Azure gratuita].
* La [Interfaz de la línea de comandos (CLI) de Azure].
* Un kit de desarrollo de Java (JDK) admitido Para más información sobre los JDK disponibles para desarrollar en Azure, consulte <https://aka.ms/azure-jdks>.
* La herramienta de compilación [Maven] de Apache (versión 3).
* Un cliente [Git].
* Un cliente de [Docker].

> [!NOTE]
>
> Dados los requisitos de virtualización de este tutorial, los pasos que se describen en este artículo no se pueden seguir en una máquina virtual; es preciso usar un equipo físico con características de virtualización habilitadas.
>

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Creación de la aplicación web Spring Boot on Docker Getting Started

Los siguientes pasos le guiarán por las fases necesarias para crear una aplicación web de Spring Boot sencilla y probarla de forma local.

1. Abra el símbolo del sistema, cree un directorio local para alojar la aplicación y cambie a dicho directorio, por ejemplo:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- o --
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Clone el proyecto de ejemplo [Spring Boot on Docker Getting Started] (inicial de Spring Boot en Docker) en el directorio que ha creado, por ejemplo:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Cambie de directorio al del proyecto finalizado, por ejemplo:
   ```
   cd gs-spring-boot-docker/complete
   ```

1. Compile el archivo JAR con Maven, por ejemplo:
   ```
   mvn package
   ```

1. Una vez creada la aplicación web, cambie el directorio al directorio `target` donde se encuentra el archivo JAR e inicie la aplicación web, por ejemplo:
   ```
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar
   ```

1. Pruebe la aplicación web. Para ello, navegue a ella localmente mediante un explorador web. Por ejemplo, si tiene curl disponible y ha configurado el servidor Tomcat para que se ejecute en el puerto 80:
   ```
   curl http://localhost
   ```

1. Debería ver el mensaje siguiente mostrado: **¡Hello Docker World!**

   ![Examen local de aplicación de ejemplo][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Crear una instancia de Azure Container Registry para usarla como un Registro de Docker privado

Los siguientes pasos le muestran cómo usar Azure Portal para crear una instancia de Azure Container Registry.

> [!NOTE]
>
> Si quiere usar la CLI de Azure en lugar de Azure Portal, siga los pasos descritos en [Creación de un registro de contenedor privado de Docker con la CLI de Azure 2.0](/azure/container-registry/container-registry-get-started-azure-cli).
>

1. Vaya a [Azure Portal] e inicie sesión.

   Una vez que haya iniciado sesión en su cuenta en Azure Portal, puede seguir los pasos descritos en el artículo [Creación de un registro de contenedor privado de Docker con Azure Portal], que se vuelven a explicar en los pasos siguientes para mayor comodidad.

1. Haga clic en el icono de menú **+ Nuevo**, en **Contenedores** y en **Azure Container Registry**.
   
   ![Crear una instancia de Azure Container Registry][AR01]

1. Cuando aparezca la página de información de la plantilla de Azure Container Registry, haga clic en **Crear**. 

   ![Crear una instancia de Azure Container Registry][AR02]

1. Cuando aparezca la página **Crear Registro de contenedor**, escriba su **Nombre del Registro** y **Grupo de recursos**, elija **Habilitar** para el **Usuario administrador** y haga clic en **Crear**.

   ![Configurar Azure Container Registry][AR03]

1. Una vez que se haya creado el Registro de contenedor, desplácese hasta él en Azure Portal y haga clic en **Claves de acceso**. Tome nota del nombre de usuario y la contraseña para usarlos en los pasos siguientes.

   ![Claves de acceso de Azure Container Registry][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>Configurar Maven para que use las claves de acceso de Azure Container Registry

1. Vaya al directorio de proyecto completado de la aplicación Spring Boot (por ejemplo: "*C:\SpringBoot\gs-spring-boot-docker\complete*" o " */users/robert/SpringBoot/gs-spring-boot-docker/complete*") y abra el archivo *pom.xml* con un editor de texto.

1. Actualice la colección `<properties>` del archivo *pom.xml* con la última versión de [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) y el valor del servidor de inicio de sesión y la configuración de acceso de Azure Container Registry de la sección anterior de este tutorial. Por ejemplo:

   ```xml
   <properties>
      <jib-maven-plugin.version>1.2.0</jib-maven-plugin.version>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <username>wingtiptoysregistry</username>
      <password>{put your Azure Container Registry access key here}</password>
   </properties>
   ```

1. Agregue [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) a la colección `<plugins>` en el archivo *pom.xml*, especifique la imagen base en `<from>/<image>` y el nombre de la imagen final `<to>/<image>`, especifique el nombre de usuario y la contraseña de la sección anterior en `<to>/<auth>`. Por ejemplo:

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>openjdk:8-jre-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
            <auth>
               <username>${username}</username>
               <password>${password}</password>
            </auth>
        </to>
     </configuration>
   </plugin>
   ```

1. Navegue hasta el directorio de proyecto completado de la aplicación Spring Boot y ejecute el siguiente comando para recompilar la aplicación e insertar el contenedor en Azure Container Registry:

   ```cmd
   mvn compile jib:build
   ```

> [!NOTE]
>
> Cuando usa Jib para insertar la imagen en Azure Container Registry, la imagen no seguirá el formato *Dockerfile*, consulte [este](https://cloudplatform.googleblog.com/2018/07/introducing-jib-build-java-docker-images-better.html) documento para más información.
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-using-your-container-image"></a>Crear una aplicación web en Linux en Azure App Service mediante la imagen de contenedor

1. Vaya a [Azure Portal] e inicie sesión.

2. Haga clic en el icono de menú **+ Crear un recurso**, a continuación, haga clic en **Web** y, a continuación, haga clic en **Web App for Containers**.
   
   ![Crear una aplicación web en Azure Portal][LX01]

3. Cuando aparezca la página **Aplicación web en Linux**, especifique la información siguiente:

   a. Escriba un nombre único para el campo **Nombre de aplicación**, por ejemplo: "*wingtiptoyslinux*".

   b. Seleccione su **Suscripción** en la lista desplegable.

   c. Seleccione un **Grupo de recursos** existente o especifique un nombre para crear uno nuevo.

   d. Elija *Linux* como el **Sistema operativo**.

   e. Haga clic en **Plan de App Service/Ubicación** y elija un plan de App Service existente o haga clic en **Crear nuevo** para crear un nuevo plan de App Service.

   f. Haga clic en **Configurar contenedor** y especifique la información siguiente:

   * Elija **Contenedor único** y **Azure Container Registry**.

   * **Registro**: Elija el nombre del contenedor creado anteriormente. Por ejemplo: "*wingtiptoysregistry*".

   * **Imagen**: elija el nombre de la imagen; por ejemplo: "*gs-spring-boot-docker*".
   
   * **Etiqueta**: elija la etiqueta de la imagen; por ejemplo: "*más reciente*".
   
   * **Archivo de inicio**: déjela en blanco, ya que la imagen ya tiene el comando de inicio.
   
   e. Cuando haya especificado la información anterior, haga clic en **Aplicar**.

   ![Configurar aplicaciones web][LX02]

4. Haga clic en **Create**(Crear).

> [!NOTE]
>
> Azure asignará automáticamente las solicitudes de Internet al servidor Tomcat integrado que se ejecuta en los puertos estándar 80 u 8080. Aun así, si ha configurado el servidor Tomcat integrado para que se ejecute en un puerto personalizado, debe agregar una variable de entorno a la aplicación web que defina el puerto del servidor Tomcat integrado. Para ello, siga estos pasos:
>
> 1. Vaya a [Azure Portal] e inicie sesión.
> 
> 2. Haga clic en el icono de **App Services** y seleccione la aplicación web en la lista.
>
> 4. Haga clic en **Configuración**. (Elemento 1 de la imagen siguiente).
>
> 5. En la sección **Configuración de la aplicación**, agregue un nuevo valor llamado **PORT** y especifique como valor su número de puerto personalizado. (Elementos 2, 3 y 4 de la imagen siguiente).
>
> 6. Haga clic en **Save**(Guardar). (Vea el elemento n.º 5 de la imagen siguiente).
>
> ![Guardar un número de puerto personalizado en Azure Portal][LX03]
>

<!--
##  OPTIONAL: Configure the embedded Tomcat server to run on a different port

The embedded Tomcat server in the sample Spring Boot application is configured to run on port 8080 by default. However, if you want to run the embedded Tomcat server to run on a different port, such as port 80 for local testing, you can configure the port by using the following steps.

1. Go to the *resources* directory (or create the directory if it does not exist); for example:
   ```shell
   cd src/main/resources
   ```

1. Open the *application.yml* file in a text editor if it exists, or create a new YAML file if it does not exist.

1. Modify the **server** setting so that the server runs on port 80; for example:
   ```yaml
   server:
      port: 80
   ```

1. Save and close the *application.yml* file.
-->

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de Spring y Azure, vaya al centro de documentación de Azure.

> [!div class="nextstepaction"]
> [Spring en Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Recursos adicionales

Para obtener más información sobre el uso de aplicaciones de Spring Boot en Azure, consulte los siguientes artículos:

* [Implementación de una aplicación de Spring Boot en Azure App Service](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)
* [Implementación de una aplicación de Spring Boot en un clúster de Kubernetes en Azure Container Service](deploy-spring-boot-java-app-on-kubernetes.md)

Para más información sobre el uso de Azure con Java, consulte [Azure para desarrolladores de Java] y [Working with Azure DevOps and Java] (Trabajo con Azure DevOps y Java).

Para obtener más información sobre el proyecto de ejemplo Spring Boot en Docker, vea [Spring Boot on Docker Getting Started] (Introducción a Spring Boot en Docker).

Para obtener ayuda para dar sus primeros pasos con sus propias aplicaciones de Spring Boot, consulte **Spring Initializr** en https://start.spring.io/.

Para más información sobre cómo empezar a crear una sencilla aplicación de Spring Boot, consulte Spring Initializr en https://start.spring.io/.

Para ver más ejemplos de cómo usar imágenes de Docker personalizadas con Azure, consulte [Uso de una imagen personalizada de Docker para Web App on Linux de Azure].

<!-- URL List -->

[Interfaz de la línea de comandos (CLI) de Azure]: /cli/azure/overview
[Azure Container Service (AKS)]: https://azure.microsoft.com/services/container-service/
[Azure para desarrolladores de Java]: /azure/java/
[Azure Portal]: https://portal.azure.com/
[Creación de un registro de contenedor privado de Docker con Azure Portal]: /azure/container-registry/container-registry-get-started-portal
[Uso de una imagen personalizada de Docker para Web App on Linux de Azure]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[cuenta de Azure gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Working with Azure DevOps and Java]: /azure/devops/java/ (Trabajo con Azure DevOps y Java)
[Maven]: http://maven.apache.org/
[ventajas como suscriptor de MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[SB01]: ./media/deploy-spring-boot-java-app-on-linux/SB01.png
[SB02]: ./media/deploy-spring-boot-java-app-on-linux/SB02.png

[AR01]: ./media/deploy-spring-boot-java-app-on-linux/AR01.png
[AR02]: ./media/deploy-spring-boot-java-app-on-linux/AR02.png
[AR03]: ./media/deploy-spring-boot-java-app-on-linux/AR03.png
[AR04]: ./media/deploy-spring-boot-java-app-on-linux/AR04.png

[LX01]: ./media/deploy-spring-boot-java-app-on-linux/LX01.png
[LX02]: ./media/deploy-spring-boot-java-app-on-linux/LX02.png
[LX03]: ./media/deploy-spring-boot-java-app-on-linux/LX03.png
