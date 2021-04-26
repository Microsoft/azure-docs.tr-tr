---
title: Hızlı başlangıç - İlk Azure Spring Cloud uygulamanızı dağıtma
description: Bu hızlı başlangıçta, Azure Spring buluta bir yay bulutu uygulaması dağıdık.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/23/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 6ae56e4e1586a875d4d3d75d41580f375df0eebf
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962858"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Hızlı başlangıç: ilk Azure Spring Cloud uygulamanızı dağıtın

::: zone pivot="programming-language-csharp"
Bu hızlı başlangıçta, Azure 'da çalıştırmak üzere basit bir Azure yay bulutu mikro hizmet uygulamasının nasıl dağıtılacağı açıklanmaktadır.

>[!NOTE]
> Azure yay bulutu için steeltoe desteği şu anda genel önizleme olarak sunulmaktadır. Genel Önizleme teklifleri, müşterilerin resmi sürümünden önceki yeni özelliklerle deneme yapmasına olanak tanır.  Genel Önizleme özellikleri ve Hizmetleri üretim kullanımı için tasarlanmamıştır.  Önizlemeler sırasında destek hakkında daha fazla bilgi için bkz. [SSS](https://azure.microsoft.com/support/faq/) veya dosya a [destek isteği](../azure-portal/supportability/how-to-create-azure-support-request.md).

Bu hızlı başlangıcı izleyerek şunları nasıl yapacağınızı öğreneceksiniz:

> [!div class="checklist"]
> * Temel bir Steeltoe .NET Core projesi oluşturma
> * Azure yay bulut hizmeti örneği sağlama
> * Uygulamayı ortak bir uç nokta ile derleyin ve dağıtın
> * Gerçek zamanlı olarak günlük akışı yapma

Bu hızlı başlangıçta kullanılan uygulama kodu, .NET Core Web API 'SI proje şablonuyla oluşturulmuş basit bir uygulamadır. Bu örneği tamamladığınızda, uygulama çevrimiçi olarak erişilebilir olur ve Azure portal ve Azure CLı aracılığıyla yönetilebilir.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Azure yay bulut hizmeti, .NET Core 3,1 ve sonraki sürümlerini destekler.
* [Azure CLI sürüm 2.0.67 veya üzeri](/cli/azure/install-azure-cli).
* [Git](https://git-scm.com/).

## <a name="install-azure-cli-extension"></a>Azure CLI uzantısını yükleme

Azure CLı sürümünüzün 2.0.67 veya üzeri olduğunu doğrulayın:

```azurecli
az --version
```

Aşağıdaki komutu kullanarak Azure CLı için Azure yay bulutu uzantısını yükler:

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>Azure'da oturum açma

1. Azure CLı 'da oturum açma

    ```azurecli
    az login
    ```

1. Birden fazla aboneliğiniz varsa, bu hızlı başlangıç için kullanmak istediğiniz birini seçin.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="generate-a-steeltoe-net-core-project"></a>Steeltoe .NET Core projesi oluşturma

Visual Studio 'da, API proje şablonuyla "Hello-World" adlı bir ASP.NET Core Web uygulaması oluşturun. Bu, daha sonra test uç noktanız olacak otomatik olarak oluşturulmuş bir üst, bir otomatik oluşturulan dalgalı işlem denetleyicisi olacağını lütfen unutmayın.

1. Proje kaynak kodu için bir klasör oluşturun ve proje oluşturun.
 
   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```dotnetcli
   dotnet new webapi -n hello-world --framework netcoreapp3.1
   ```

1. Proje dizinine gidin.

   ```console
   cd hello-world
   ```

1. Aşağıdaki ayarları eklemek için dosyadaki *appSettings.js* düzenleyin:

   ```json
   "spring": {
     "application": {
       "name": "hello-world"
     }
   },
   "eureka": {
     "client": {
       "shouldFetchRegistry": true,
       "shouldRegisterWithEureka": true
     }
   }
   ```

1. Ayrıca *appsettings.jsüzerinde*, kategori için günlük düzeyini ' dan ' a değiştirin `Microsoft` `Warning` `Information` . Bu değişiklik, akış günlüklerini sonraki bir adımda görüntülediğinizde günlüklerin üretileceği bir işlem sağlar.

   Dosyadaki *appsettings.js* artık aşağıdaki örneğe benzer şekilde görünür:

   ```json
   {
     "Logging": {
       "LogLevel": {
         "Default": "Information",
         "Microsoft": "Information",
         "Microsoft.Hosting.Lifetime": "Information"
       }
     },
     "AllowedHosts": "*",
     "spring": {
       "application": {
         "name": "hello-world"
       }
     },
     "eureka": {
       "client": {
         "shouldFetchRegistry": true,
         "shouldRegisterWithEureka": true
       }
     }
   }
   ```
   
1. Bağımlılıklar ve bir `Zip` görevi *. csproj* dosyasına ekleyin:

   ```xml
   <ItemGroup>
     <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
     <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
   </ItemGroup>
   <Target Name="Publish-Zip" AfterTargets="Publish">
       <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/deploy.zip" Overwrite="true" />
   </Target>
   ```

   Paketler, Steeltoe hizmeti bulma ve Azure yay bulutu istemci kitaplığı içindir. Bu `Zip` görev, Azure 'a dağıtım içindir. `dotnet publish`Komutunu çalıştırdığınızda, bu, dosyaları *Yayımla* klasöründe oluşturur ve bu görev *Publish* klasörünü Azure 'a yüklediğiniz bir *. zip* dosyasına dönüştürür.

3. *Program. cs* dosyasında, `using` Azure yay bulutu istemci kitaplığı 'nı kullanan bir yönerge ve kod ekleyin:

   ```csharp
   using Microsoft.Azure.SpringCloud.Client;
   ```

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
               Host.CreateDefaultBuilder(args)
                   .UseAzureSpringCloudService()
                   .ConfigureWebHostDefaults(webBuilder =>
                   {
                       webBuilder.UseStartup<Startup>();
                   });
   ```

4. *Startup. cs* dosyasında, `using` ve yöntemlerinin sonunda Steeltoe hizmeti bulma 'yı kullanan bir yönerge ve kod ekleyin `ConfigureServices` `Configure` :

   ```csharp
   using Steeltoe.Discovery.Client;
   ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       // Template code not shown.

       services.AddDiscoveryClient(Configuration);
   }
   ```

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       // Template code not shown.

       app.UseDiscoveryClient();
   }
   ```

1. Derleme hatası olmadığından emin olmak için projeyi derleyin.

   ```dotnetcli
   dotnet build
   ```
 
## <a name="provision-a-service-instance"></a>Hizmet örneği sağlama

Aşağıdaki yordam Azure portal kullanarak bir Azure Spring Cloud örneği oluşturur.

1. [Azure portalını](https://ms.portal.azure.com/) açın. 

1. Üst arama kutusundan *Azure yay bulutu*' nı arayın.

1. Sonuçlardan *Azure yay bulutu* ' nı seçin.

   ![ASC simgesi başlangıç](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

1. Azure yay bulutu sayfasında **+ Ekle**' yi seçin.

   ![ASC simgesi ekle](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

1. Azure yay bulutu **Oluştur** sayfasında formu doldurun.  Aşağıdaki yönergeleri göz önünde bulundurun:

   * **Abonelik**: Bu kaynak için faturalandırılması istediğiniz aboneliği seçin.
   * **Kaynak grubu**: yeni bir kaynak grubu oluşturun. Buraya girdiğiniz ad sonraki adımlarda olduğu gibi kullanılacaktır **\<resource group name\>** .
   * **Hizmet Ayrıntıları/adı**: öğesini belirtin **\<service instance name\>** .  Ad 4 ila 32 karakter uzunluğunda olmalı ve yalnızca küçük harf, sayı ve kısa çizgi içermelidir.  Hizmet adının ilk karakteri bir harf olmalıdır ve son karakter bir harf ya da sayı olmalıdır.
   * **Bölge**: hizmet örneğiniz için bölgeyi seçin.

   ![ASC portalı başlangıcı](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. **Gözden geçir ve Oluştur '** u seçin.

## <a name="build-and-deploy-the-app"></a>Uygulama derleme ve dağıtma

Aşağıdaki yordam, daha önce oluşturduğunuz projeyi oluşturur ve dağıtır.

1. Komut isteminin hala proje klasöründe olduğundan emin olun.

1. Projeyi derlemek, ikilileri yayımlamak ve ikilileri proje klasöründeki bir *. zip* dosyasında depolamak için aşağıdaki komutu çalıştırın.

   ```dotnetcorecli
   dotnet publish -c release -o ./publish
   ```

1. Azure Spring Cloud örneğiniz için genel bir uç nokta atanmış olarak bir uygulama oluşturun. *Üzerindeappsettings.js* belirttiğiniz "Hello-World" uygulama adını kullanın.

   ```console
   az spring-cloud app create -n hello-world -s <service instance name> -g <resource group name> --assign-endpoint --runtime-version NetCore_31
   ```

1. *. Zip* dosyasını uygulamaya dağıtın.

   ```azurecli
   az spring-cloud app deploy -n hello-world -s <service instance name> -g <resource group name> --runtime-version NetCore_31 --main-entry hello-world.dll --artifact-path ./deploy.zip
   ```

   `--main-entry`Seçeneği, uygulamanın giriş noktasını içeren *. dll* dosyasını tanımlar. Hizmet *. zip* dosyasını karşıya yükledikten sonra, tüm dosya ve klasörleri ayıklar ve tarafından belirtilen *. dll* dosyasında giriş noktasını yürütmeye çalışır `--main-entry` .

   Uygulamanın dağıtım işleminin tamamlanmasının birkaç dakika sürer. Dağıtıldığını doğrulamak için Azure portal **uygulamalar** dikey penceresine gidin.

## <a name="test-the-app"></a>Uygulamayı test etme

Dağıtım tamamlandıktan sonra uygulamaya şu URL 'den erişin:

```http
https://<service instance name>-hello-world.azuremicroservices.io/weatherforecast
```

Uygulama, aşağıdaki örneğe benzer JSON verileri döndürür:

```json
[{"date":"2020-09-08T21:01:50.0198835+00:00","temperatureC":14,"temperatureF":57,"summary":"Bracing"},{"date":"2020-09-09T21:01:50.0200697+00:00","temperatureC":-14,"temperatureF":7,"summary":"Bracing"},{"date":"2020-09-10T21:01:50.0200715+00:00","temperatureC":27,"temperatureF":80,"summary":"Freezing"},{"date":"2020-09-11T21:01:50.0200717+00:00","temperatureC":18,"temperatureF":64,"summary":"Chilly"},{"date":"2020-09-12T21:01:50.0200719+00:00","temperatureC":16,"temperatureF":60,"summary":"Chilly"}]
```

## <a name="stream-logs-in-real-time"></a>Gerçek zamanlı olarak günlük akışı yapma

Uygulamadan gerçek zamanlı günlükleri almak için aşağıdaki komutu kullanın.

```azurecli
az spring-cloud app logs -n hello-world -s <service instance name> -g <resource group name> --lines 100 -f
```

Günlükler çıkışta görüntülenir:

```output
[Azure Spring Cloud] The following environment variables are loaded:
2020-09-08 20:58:42,432 INFO supervisord started with pid 1
2020-09-08 20:58:43,435 INFO spawned: 'event-gather_00' with pid 9
2020-09-08 20:58:43,436 INFO spawned: 'dotnet-app_00' with pid 10
2020-09-08 20:58:43 [Warning] No managed processes are running. Wait for 30 seconds...
2020-09-08 20:58:44,843 INFO success: event-gather_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
2020-09-08 20:58:44,843 INFO success: dotnet-app_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
←[40m←[32minfo←[39m←[22m←[49m: Steeltoe.Discovery.Eureka.DiscoveryClient[0]
      Starting HeartBeat
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://[::]:1025
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /netcorepublish/6e4db42a-b160-4b83-a771-c91adec18c60
2020-09-08 21:00:13 [Information] [10] Start listening...
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://asc-svc-hello-world.azuremicroservices.io/weatherforecast
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "Get", controller = "WeatherForecast"}. Executing controller action with signature System.Collections.Generic.IEnumerable`1[hello_world.WeatherForecast] Get() on controller hello_world.Controllers.WeatherForecastController (hello-world).
info: Microsoft.AspNetCore.Mvc.Infrastructure.ObjectResultExecutor[1]
      Executing ObjectResult, writing value of type 'hello_world.WeatherForecast[]'.
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action hello_world.Controllers.WeatherForecastController.Get (hello-world) in 1.8902ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 4.2591ms 200 application/json; charset=utf-8
```

> [!TIP]
> `az spring-cloud app logs -h`Daha fazla parametre ve günlük akışı işlevlerini araştırmak için kullanın.

Gelişmiş Log Analytics özellikleri için, [Azure Portal](https://portal.azure.com/)menüsündeki **Günlükler** sekmesini ziyaret edin. Burada günlüklerde birkaç dakika gecikme vardır.
[![Günlük Analizi ](media/spring-cloud-quickstart-java/logs-analytics.png)](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Bu hızlı başlangıçta, Azure 'da çalıştırmak üzere basit bir Azure yay bulutu mikro hizmet uygulamasının nasıl dağıtılacağı açıklanmaktadır. 

Bu öğreticide kullanılan uygulama kodu, Spring ınitialsır ile oluşturulmuş basit bir uygulamadır. Bu örneği tamamladığınızda, uygulama çevrimiçi olarak erişilebilir olur ve Azure portal aracılığıyla yönetilebilir.

Bu hızlı başlangıçta nasıl yapılacağı açıklanmaktadır:

> [!div class="checklist"]
> * Temel yay bulutu projesi oluşturma
> * Hizmet örneği sağlama
> * Uygulamayı ortak bir uç nokta ile derleyin ve dağıtın
> * Gerçek zamanlı olarak günlük akışı yapma

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için:

* [JDK 8 ' i yükler](/java/azure/jdk/)
* [Azure aboneliğine kaydolma](https://azure.microsoft.com/free/)
* Seçim [Azure CLI sürüm 2.0.67 veya üstünü](/cli/azure/install-azure-cli) ve Azure yay bulutu uzantısını şu komutla birlikte yüklersiniz: `az extension add --name spring-cloud`
* Seçim [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) ve [oturum açmayı](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) yükleyip

## <a name="generate-a-spring-cloud-project"></a>Yay bulutu projesi oluşturma

Azure Spring Cloud için önerilen bağımlılıklara sahip bir örnek proje oluşturmak üzere [Spring ınitialku](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.9.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client) ile başlayın. Aşağıdaki görüntüde, bu örnek proje için ayarlanan ınitialr gösterilmektedir.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client
```
Bu örneğin Java sürüm 8 ' i kullandığını unutmayın.  Java sürüm 11 ' i kullanmak istiyorsanız, **proje meta verileri**' ndeki seçeneği değiştirin.

  ![Initialkr sayfası](media/spring-cloud-quickstart-java/initializr-page.png)

1. Tüm bağımlılıklar ayarlandığında **Oluştur** ' a tıklayın. Paketi indirip paketini açın ve aşağıdaki şekilde ekleyerek basit bir Web uygulaması için bir Web denetleyicisi oluşturun `src/main/java/com/example/hellospring/HelloController.java` :

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>Azure yay bulutu örneği sağlama

Aşağıdaki yordam Azure portal kullanarak bir Azure Spring Cloud örneği oluşturur.

1. Yeni bir sekmede [Azure Portal](https://ms.portal.azure.com/)açın. 

2. Üst arama kutusundan *Azure yay bulutu*' nı arayın.

3. Sonuçlardan *Azure yay bulutu* ' nı seçin.

    ![ASC simgesi başlangıç](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Azure yay bulutu sayfasında **+ Ekle**' ye tıklayın.

    ![ASC simgesi ekle](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Azure yay bulutu **Oluştur** sayfasında formu doldurun.  Aşağıdaki yönergeleri göz önünde bulundurun:
    - **Abonelik**: Bu kaynak için faturalandırılması istediğiniz aboneliği seçin.
    - **Kaynak grubu**: yeni kaynaklar için yeni kaynak grupları oluşturmak en iyi uygulamadır. Bu, sonraki adımlarda olduğu gibi kullanılacaktır **\<resource group name\>** .
    - **Hizmet Ayrıntıları/adı**: öğesini belirtin **\<service instance name\>** .  Ad 4 ila 32 karakter uzunluğunda olmalı ve yalnızca küçük harf, sayı ve kısa çizgi içermelidir.  Hizmet adının ilk karakteri bir harf olmalıdır ve son karakter bir harf ya da sayı olmalıdır.
    - **Konum**: hizmet örneğiniz için bölgeyi seçin.

    ![ASC portalı başlangıcı](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. **Gözden geçir ve oluştur**’a tıklayın.

## <a name="build-and-deploy-the-app"></a>Uygulama derleme ve dağıtma
    
#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
Aşağıdaki yordam, Azure CLı kullanarak uygulamayı oluşturur ve dağıtır. Projenin kökünde aşağıdaki komutu yürütün.

1. Maven kullanarak projeyi derleyin:

    ```console
    mvn clean package -DskipTests
    ```

1. (Henüz yüklemediyseniz) Azure CLı için Azure yay bulutu uzantısını yükler:

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. Ortak uç nokta atanmış uygulamayı oluşturun. Java 11 kullanıyorsanız, `--runtime-version=Java_11` anahtarını ekleyin.

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --assign-endpoint true
    ```

1. Uygulama için jar dosyasını dağıtın ( `target\hellospring-0.0.1-SNAPSHOT.jar` Windows üzerinde):

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path <jar file path>
    ```
    
1. Uygulamanın dağıtım işleminin tamamlanmasının birkaç dakika sürer. Dağıtıldığını doğrulamak için Azure portal **uygulamalar** dikey penceresine gidin. Uygulamanın durumunu görmeniz gerekir.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Aşağıdaki yordam, IntelliJ FIKRINDEN örnek uygulamayı dağıtmak üzere Azure Spring Cloud için IntelliJ eklentisini kullanır.  

### <a name="import-project"></a>Projeyi içeri aktarma

1. IntelliJ **hoş geldiniz** iletişim kutusunu açın ve içeri aktarma Sihirbazı 'nı açmak Için **projeyi içeri aktar** ' ı seçin.
1. `hellospring`Klasör seçin.

    ![Projeyi içeri aktar](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>Uygulamayı dağıtma
Azure 'a dağıtmak için Azure hesabınızla oturum açmanız ve aboneliğinizi seçmeniz gerekir.  Oturum açma ayrıntıları için bkz. [yükleme ve oturum açma](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. IntelliJ Proje Gezgini ' nde projenize sağ tıklayın ve **Azure**  ->  **yay bulutu 'na Azure dağıtımı**' nı seçin.

    [![Azure 'a dağıtma 1 ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png)](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. **Ad** alanındaki uygulamanın adını kabul edin. **Ad** , uygulama adı değil, yapılandırmaya başvurur. Kullanıcıların genellikle bunu değiştirmesi gerekmez.
1. **Yapıt** metin kutusunda *HELLOSPRING-0.0.1-Snapshot. jar* öğesini seçin.
1. **Abonelik** metin kutusunda aboneliğinizi doğrulayın.
1. **Yay bulutu** metin kutusunda, [Azure Spring Cloud Instance sağlama](./spring-cloud-quickstart-provision-service-instance.md)bölümünde oluşturduğunuz Azure Spring Cloud örneğini seçin.
1. **Genel uç noktayı** *Etkinleştir* olarak ayarlayın.
1. **Uygulama:** metin kutusunda **uygulama oluştur...** seçeneğini belirleyin.
1. *Merhaba yay* girin ve ardından **Tamam**' a tıklayın.

    [![Azure 'a dağıtma Tamam ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png)](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. **Azure yay bulut uygulaması dağıtma** iletişim kutusunun alt kısmındaki **Çalıştır** düğmesine tıklayarak dağıtımı başlatın. Eklenti uygulamada komutunu çalıştırır `mvn package` `hellospring` ve komut tarafından oluşturulan jar 'yi dağıtır `package` .
---

Dağıtım tamamlandıktan sonra uygulamasına ' de erişebilirsiniz `https://<service instance name>-hellospring.azuremicroservices.io/` .

  [![Tarayıcıdan ](media/spring-cloud-quickstart-java/access-app-browser.png) uygulamaya erişme](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>Akış günlükleri gerçek zamanlı olarak

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Uygulamadan gerçek zamanlı günlükleri almak için aşağıdaki komutu kullanın.

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
Günlükler sonuçlarda görüntülenir:

[![Akış günlükleri ](media/spring-cloud-quickstart-java/streaming-logs.png)](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> `az spring-cloud app logs -h`Daha fazla parametre ve günlük akışı işlevlerini araştırmak için kullanın.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. **Azure Gezgini**' ni ve ardından **yay bulutu**' nı seçin.
1. Çalışan uygulamaya sağ tıklayın.
1. Açılan listeden **akış günlükleri** ' ni seçin.
1. Örnek seçin.

    [![Akış günlüklerini ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png) seçme](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. Akış günlüğü, çıkış penceresinde görünür olur.

    [![Akış günlüğü çıkışı ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

Gelişmiş Günlükler analiz özellikleri için [Azure Portal](https://portal.azure.com/)menüsündeki **Günlükler** sekmesini ziyaret edin. Burada günlüklerde birkaç dakika gecikme vardır.

[![Günlük Analizi ](media/spring-cloud-quickstart-java/logs-analytics.png)](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yukarıdaki adımlarda, abonelikleriniz sırasında ücretlendirmeye devam edecek Azure kaynakları oluşturdunuz. Gelecekte bu kaynaklara ihtiyacınız yoksa, kaynak grubunu portaldan silin veya Azure CLı 'de aşağıdaki komutu çalıştırın:

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunları öğrendiniz:

> [!div class="checklist"]
> * Temel bir Azure yay bulutu projesi oluşturma
> * Hizmet örneği sağlama
> * Uygulamayı ortak bir uç nokta ile derleyin ve dağıtın
> * Gerçek zamanlı olarak günlük akışı yapma

Daha fazla Azure yay özelliği kullanmayı öğrenmek için Azure Spring Cloud 'a örnek bir uygulama dağıtan hızlı başlangıç serisine ilerleyin:

> [!div class="nextstepaction"]
> [Mikro hizmetler oluşturun ve çalıştırın](spring-cloud-quickstart-sample-app-introduction.md)

GitHub 'da daha fazla örnek vardır: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
