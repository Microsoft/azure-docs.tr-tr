---
title: 'Öğretici: MongoDB ile Linux Java uygulaması'
description: Azure 'da çalışan bir MongoDB bağlantısı ile Azure App Service çalışan veri odaklı bir Linux Java uygulamasını nasıl alabileceğinizi öğrenin (Cosmos DB).
author: rloutlaw
ms.author: routlaw
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: mvc, seodec18, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 7a0dd1198bcad675f7662a2cf4eb369f2a276445
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479415"
---
# <a name="tutorial-build-a-java-spring-boot-web-app-with-azure-app-service-on-linux-and-azure-cosmos-db"></a>Öğretici: Linux ve Azure Cosmos DB üzerinde Azure App Service bir Java Spring Boot Web uygulaması oluşturun

Bu öğretici, Azure 'da Java Web uygulamaları oluşturma, yapılandırma, dağıtma ve ölçeklendirme sürecinde size yol gösterir. İşiniz bittiğinde, verileri [Linux üzerinde Azure App Service](overview.md)çalıştıran [Azure Cosmos DB](../cosmos-db/index.yml) bir [Spring Boot](https://projects.spring.io/spring-boot/) uygulamasına sahip olursunuz.

![Yay önyükleme uygulaması Azure Cosmos DB verileri depolama](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Cosmos DB veritabanı oluşturun.
> * Örnek bir uygulamayı veritabanına bağlama ve yerel olarak test etme
> * Örnek uygulamayı Azure 'a dağıtma
> * App Service tanılama günlüklerini akışla
> * Örnek uygulamanın ölçeğini genişletmek için ek örnekler ekleme

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Kendi bilgisayarınızda yüklü olan [Azure CLI](/cli/azure/overview). 
* [Git](https://git-scm.com/)
* [Java JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>Örnek TODO uygulamasını kopyalayın ve depoyu hazırlayın

Bu öğreticide, [Spring Data Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb)tarafından desteklenen yay REST API çağıran bir Web Kullanıcı arabirimine sahip BIR örnek Yapılacaklar listesi uygulaması kullanılmaktadır. Uygulamanın kodu [GitHub '](https://github.com/Microsoft/spring-todo-app)da kullanılabilir. Spring ve Cosmos DB kullanarak Java uygulamaları yazma hakkında daha fazla bilgi edinmek için bkz. [Spring Boot Starter for Azure Cosmos db SQL API öğreticisi](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) ve [yay verileri Azure Cosmos DB hızlı başlangıç](https://github.com/Microsoft/spring-data-cosmosdb#quick-start).


Örnek depoyu kopyalamak ve örnek uygulama ortamını ayarlamak için terminalinizde aşağıdaki komutları çalıştırın.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Azure Cosmos DB oluşturma

Aboneliğinizde bir Azure Cosmos DB veritabanı oluşturmak için aşağıdaki adımları izleyin. YAPıLACAKLAR listesi uygulaması bu veritabanına bağlanır ve çalışırken verisini depolar, uygulamayı çalıştırdığınız yere bakılmaksızın uygulama durumunu kalıcı hale getirmeyi sağlar.

1. Azure CLı 'niz üzerinde oturum açın ve oturum açma kimlik bilgilerinizle birden fazla bağlantı varsa aboneliğinizi isteğe bağlı olarak ayarlayın.

    ```azurecli
    az login
    az account set -s <your-subscription-id>
    ```   

2. Kaynak grubu adını belirterek bir Azure Kaynak grubu oluşturun.

    ```azurecli
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Tür ile Azure Cosmos DB oluşturun `GlobalDocumentDB` . Cosmos DB adı yalnızca küçük harf kullanımı kullanmalıdır. `documentEndpoint`Komuttan gelen yanıttaki alanı aşağı göz önünde edin.

    ```azurecli
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Uygulamaya bağlanmak için Azure Cosmos DB anahtarınızı alın. Bir `primaryMasterKey` `documentEndpoint` sonraki adımda ihtiyacınız olacak şekilde yakınınızda saklayın.

    ```azurecli
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>TODO uygulaması özelliklerini yapılandırma

Bilgisayarınızda bir terminali açın. Yeni oluşturduğunuz Cosmos DB veritabanınız için özelleştirebilmeniz için kopyalanmış depoya örnek betik dosyasını kopyalayın.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
En `.scripts/set-env-variables.sh` sevdiğiniz düzenleyicide düzenleyin ve Azure Cosmos DB bağlantı bilgilerini sağlayın. App Service Linux yapılandırması için, `your-resource-group-region` `your-azure-group-name` Cosmos db veritabanını oluştururken kullanılan () ve kaynak grubu () ile aynı bölgeyi kullanın. Herhangi bir Azure dağıtımında herhangi bir Web uygulaması adı yinelemediğinden benzersiz bir WEBAPP_NAME seçin.

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

# App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Sonra betiği çalıştırın:

```bash
source .scripts/set-env-variables.sh
```
   
Bu ortam değişkenleri, YAPıLACAKLAR listesi uygulamasında ' de kullanılır `application.properties` . Özellikler dosyasındaki alanlar yay verileri için varsayılan bir depo yapılandırması ayarlar:

```properties
azure.cosmosdb.uri=${COSMOSDB_URI}
azure.cosmosdb.key=${COSMOSDB_KEY}
azure.cosmosdb.database=${COSMOSDB_DBNAME}
```

```java
@Repository
public interface TodoItemRepository extends DocumentDbRepository<TodoItem, String> {
}
```

Ardından örnek uygulama, `@Document` `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` Cosmos DB tarafından depolanacak ve yönetilmek üzere bir varlık türü ayarlamak için öğesinden içeri aktarılan ek açıklamayı kullanır:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>Örnek uygulamayı çalıştırma

Örneği çalıştırmak için Maven 'i kullanın.

```bash
mvn package spring-boot:run
```

Çıktı aşağıdaki gibi görünmelidir.

```output
bash-3.2$ mvn package spring-boot:run
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 


[INFO] SimpleUrlHandlerMapping - Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] SimpleUrlHandlerMapping - Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] WelcomePageHandlerMapping - Adding welcome page: class path resource [static/index.html]
2018-10-28 15:04:32.101  INFO 7673 --- [           main] c.m.azure.documentdb.DocumentClient      : Initializing DocumentClient with serviceEndpoint [https://sample-cosmos-db-westus.documents.azure.com:443/], ConnectionPolicy [ConnectionPolicy [requestTimeout=60, mediaRequestTimeout=300, connectionMode=Gateway, mediaReadMode=Buffered, maxPoolSize=800, idleConnectionTimeout=60, userAgentSuffix=;spring-data/2.0.6;098063be661ab767976bd5a2ec350e978faba99348207e8627375e8033277cb2, retryOptions=com.microsoft.azure.documentdb.RetryOptions@6b9fb84d, enableEndpointDiscovery=true, preferredLocations=null]], ConsistencyLevel [null]
[INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
[INFO] TomcatWebServer - Tomcat started on port(s): 8080 (http) with context path ''
[INFO] TodoApplication - Started TodoApplication in 45.573 seconds (JVM running for 76.534)
```

Uygulama başlatıldıktan sonra Spring TODO uygulamasına bu bağlantıyı kullanarak yerel olarak erişebilirsiniz: `http://localhost:8080/` .

 ![Spring TODO uygulamasına yerel olarak erişin](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

"Started TodoApplication" iletisi yerine özel durumlar görürseniz, `bash` önceki adımda bulunan betiğin ortam değişkenlerini doğru bir şekilde ve oluşturduğunuz Azure Cosmos DB veritabanı için doğru olduğundan emin olun.

## <a name="configure-azure-deployment"></a>Azure dağıtımını yapılandırma

`pom.xml`Dosyayı `initial/spring-boot-todo` dizinde açın ve [Maven yapılandırması Için aşağıdaki Azure Web uygulaması eklentisini](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) ekleyin.

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.13.0</version>
        <configuration>
            <schemaVersion>v2</schemaVersion>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>

            <!-- Java Runtime Stack for Web App on Linux-->
            <runtime>
                 <os>linux</os>
                 <javaVersion>jre8</javaVersion>
                 <webContainer>jre8</webContainer>
             </runtime>
             <deployment>
                 <resources>
                 <resource>
                     <directory>${project.basedir}/target</directory>
                     <includes>
                     <include>*.jar</include>
                     </includes>
                 </resource>
                 </resources>
             </deployment>

            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property> 
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Dserver.port=80</value>
                </property>
            </appSettings>

        </configuration>
    </plugin>           
    ...
</plugins>
```

## <a name="deploy-to-app-service-on-linux"></a>Linux üzerinde App Service dağıtma

`mvn azure-webapp:deploy`Maven hedefini kullanarak, Todo uygulamasını Linux üzerinde Azure App Service için dağıtın.

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.11.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Auth Type : AZURE_CLI, Auth Files : [C:\Users\testuser\.azure\azureProfile.json, C:\Users\testuser\.azure\accessTokens.json]
[INFO] Subscription : xxxxxxxxx
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource to /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Renaming /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7/spring-todo-app-2.0-SNAPSHOT.jar to app.jar
[INFO] Deploying the zip package spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7718326714198381983.zip...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2019-11-06T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

Çıktı, dağıtılan uygulamanızın URL 'sini içerir (Bu örnekte, `https://spring-todo-app.azurewebsites.net` ). Uygulamanızı yüklemek için, bu URL 'YI Web tarayıcınıza kopyalayabilir veya Terminal pencerenizde aşağıdaki komutu çalıştırabilirsiniz.

```bash
explorer https://spring-todo-app.azurewebsites.net
```

Adres çubuğunda uzak URL ile çalışan uygulamayı görmeniz gerekir:

 ![Uzak URL ile çalıştırılan Spring Boot uygulaması](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="stream-diagnostic-logs"></a>Tanılama günlüklerini akışla aktarma

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]


## <a name="scale-out-the-todo-app"></a>TODO uygulamasını genişletme

Başka bir çalışan ekleyerek uygulamayı ölçeklendirin:

```azurecli
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu kaynaklara başka bir öğretici için ihtiyaç duymuyorsanız (bkz. [sonraki adımlar](#next)), Cloud Shell şu komutu çalıştırarak silebilirsiniz: â Â € ̄Â 
```azurecli
az group delete --name <your-azure-group-name>
```

<a name="next"></a>

## <a name="next-steps"></a>Sonraki adımlar

[Java geliştiricileri](/java/azure/) 
 için Azure [Spring Boot](https://spring.io/projects/spring-boot), Cosmos DB, [Azure Cosmos DB](../cosmos-db/introduction.md) ve [App Service Linux](overview.md) [için yay verileri](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db).

Java uygulamalarını, Linux üzerinde App Service geliştirici kılavuzunda çalıştırma hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"] 
> [App Service Linux’ta Java geliştirme kılavuzu](configure-language-java.md?pivots=platform-linux)
