---
title: Hızlı başlangıç-Azure yay bulutuna uygulama oluşturma ve dağıtma
description: Azure yay bulutu 'na uygulama dağıtımını açıklar.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 727354593488cd5af77408eaa7c8d9e747a0b071
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879376"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Hızlı başlangıç: Azure yay bulutuna uygulama oluşturma ve dağıtma

::: zone pivot="programming-language-csharp"
Bu hızlı başlangıçta, Azure CLı kullanarak mikro hizmet uygulamalarını derleyin ve Azure Spring Cloud 'a dağıtırsınız.

## <a name="prerequisites"></a>Önkoşullar

* Bu serideki önceki hızlı başlangıçlarını doldurun:

  * [Azure yay bulut hizmeti sağlayın](spring-cloud-quickstart-provision-service-instance.md).
  * [Azure yay bulut yapılandırması sunucusunu ayarlayın](spring-cloud-quickstart-setup-config-server.md).

## <a name="download-the-sample-app"></a>Örnek uygulamayı indirme

Azure Cloud Shell bu noktaya kadar kullanıyorsanız, aşağıdaki adımlar için yerel bir komut istemine geçin.

1. Yeni bir klasör oluşturun ve örnek uygulama deposunu kopyalayın.

   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```console
   git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples
   ```

1. Depo dizinine gidin.

   ```console
   cd Azure-Spring-Cloud-Samples
   ```

## <a name="deploy-planetweatherprovider"></a>Planetdalgalı sağlayıcısını dağıt

1. Azure Spring Cloud örneğiniz içindeki Planetdalgalı sağlayıcı projesi için bir uygulama oluşturun.

   ```azurecli
   az spring-cloud app create --name planet-weather-provider --runtime-version NetCore_31
   ```

   Otomatik hizmet kaydını etkinleştirmek için, uygulamayı `spring.application.name` projenin *appsettings.js* dosyadaki değeri ile aynı adı vermiş olursunuz:

   ```json
   "spring": {
     "application": {
       "name": "planet-weather-provider"
     }
   }
   ```

   Bu komutun çalışması birkaç dakika sürebilir.

1. Dizini `PlanetWeatherProvider` Proje klasörü olarak değiştirin.

   ```console
   cd steeltoe-sample/src/planet-weather-provider
   ```

1. Dağıtılacak ikili dosyaları ve *. zip* dosyasını oluşturun.

   ```console
   dotnet publish -c release -o ./publish
   ```

   > [!TIP]
   > Proje dosyası, dosyaları *./Publish* klasörüne yazdıktan sonra bir *. zip* DOSYASıNA paketlemek için aşağıdaki XML 'i içerir:
   >
   > ```xml
   > <Target Name="Publish-Zip" AfterTargets="Publish">
   >   <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/publish-deploy-planet.zip" Overwrite="true" />
   > </Target>
   > ```

1. Azure 'a dağıtın.

   Aşağıdaki komutu çalıştırmadan önce komut isteminin proje klasöründe olduğundan emin olun.

   ```console
   az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip
   ```

   `--main-entry`Seçeneği, *. zip* dosyasının kök klasöründen uygulamanın giriş noktasını içeren *. dll* dosyasına göreli yolu belirtir. Hizmet *. zip* dosyasını karşıya yükledikten sonra, tüm dosya ve klasörleri ayıklar ve belirtilen *. dll* dosyasında giriş noktasını yürütmeye çalışır.

   Bu komutun çalışması birkaç dakika sürebilir.

## <a name="deploy-solarsystemweather"></a>SolarSystemWeather dağıtma

1. Azure Spring Cloud örneğiniz için bu kez SolarSystemWeather projesi için başka bir uygulama oluşturun:

   ```azurecli
   az spring-cloud app create --name solar-system-weather --runtime-version NetCore_31
   ```

   `solar-system-weather` , `SolarSystemWeather` projenin *appsettings.js* dosyada belirtilen addır.

   Bu komutun çalışması birkaç dakika sürebilir.

1. Dizini proje olarak değiştirin `SolarSystemWeather` .

   ```console
   cd ../solar-system-weather
   ```

1. Dağıtılacak ikili dosyaları ve *. zip* dosyasını oluşturun.

   ```console
   dotnet publish -c release -o ./publish
   ```

1. Azure 'a dağıtın.

   ```console
   az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip
   ```
   
   Bu komutun çalışması birkaç dakika sürebilir.

## <a name="assign-public-endpoint"></a>Ortak uç nokta ata

Uygulamayı test etmek için bir tarayıcıdan uygulamaya bir HTTP GET isteği gönderin `solar-system-weather` .  Bunu yapmak için, istek için genel bir uç noktaya ihtiyacınız vardır.

1. Uç noktayı atamak için aşağıdaki komutu çalıştırın.

   ```azurecli
   az spring-cloud app update -n solar-system-weather --assign-endpoint true
   ```

1. Uç noktanın URL 'sini almak için aşağıdaki komutu çalıştırın.

   Windows:

   ```azurecli
   az spring-cloud app show -n solar-system-weather -o table
   ```

   Linux:

   ```azurecli
   az spring-cloud app show --name solar-system-weather | grep url
   ```

## <a name="test-the-application"></a>Uygulamayı test edin

Uygulamaya bir GET isteği gönderin `solar-system-weather` . Bir tarayıcıda, sonuna eklenen genel URL 'ye gidin `/weatherforecast` . Örnek:

```
https://servicename-solar-system-weather.azuremicroservices.io/weatherforecast
```

Çıktı JSON olur:

```json
[{"Key":"Mercury","Value":"very warm"},{"Key":"Venus","Value":"quite unpleasant"},{"Key":"Mars","Value":"very cool"},{"Key":"Saturn","Value":"a little bit sandy"}]
```

Bu yanıt, hem mikro hizmet uygulamalarının çalıştığını gösterir. `SolarSystemWeather`Uygulama, uygulamadan alınan verileri döndürür `PlanetWeatherProvider` .
::: zone-end

::: zone pivot="programming-language-java"
Bu belgede, aşağıdaki kullanılarak mikro hizmet uygulamalarının Azure Spring Cloud 'a nasıl oluşturulacağı ve dağıtılacağı açıklanmaktadır:
* Azure CLI’si
* Maven eklentisi
* IntelliJ

Azure CLı veya Maven kullanarak dağıtımdan önce [Azure Spring Cloud 'ın bir örneğini](spring-cloud-quickstart-provision-service-instance.md) sağlayan örnekleri tamamlayın ve [yapılandırma sunucusunu ayarlayın](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Önkoşullar

* [JDK 8 ' i yükler](/java/azure/jdk/)
* [Azure aboneliğine kaydolma](https://azure.microsoft.com/free/)
* Seçim [Azure CLI sürüm 2.0.67 veya üstünü yükleyip](/cli/azure/install-azure-cli) Azure Spring Cloud uzantısını şu komutla birlikte yüklersiniz: `az extension add --name spring-cloud`
* Seçim [Azure Toolkit for IntelliJ yükleyip](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) [oturum açın](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="deployment-procedures"></a>Dağıtım yordamları

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>Mikro hizmet uygulamalarını yerel olarak oluşturma

1. Örnek uygulama deposunu Azure bulut hesabınıza kopyalayın.  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Dizini değiştirin ve projeyi derleyin.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

Projenin derlenmesi yaklaşık 5 dakika sürer. Tamamlandıktan sonra, ilgili klasörlerinde her bir hizmet için bireysel JAR dosyaları olmalıdır.

### <a name="create-and-deploy-the-apps"></a>Uygulamaları oluşturma ve dağıtma

1. Aşağıdaki komutları kullanarak varsayılan kaynak grubu adınızı ve küme adınızı ayarlayın:

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Önceki adımda oluşturulan JAR dosyalarını kullanarak Azure Spring Cloud mikro hizmetleri oluşturun. Üç uygulama oluşturacaksınız: **Gateway**, **AUTH-Service** ve **Account-Service**.

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. Önceki adımda oluşturulan uygulamaları Azure 'a dağıtmemiz gerekiyor. Üç uygulamayı da dağıtmak için aşağıdaki komutları kullanın:

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>Ağ geçidine genel uç nokta ata

Bir Web tarayıcısı aracılığıyla uygulamaya erişmek için bir yönteme ihtiyacımız var. Ağ Geçidi uygulamamız, herkese açık bir uç nokta gerektirir.

1. Aşağıdaki komutu kullanarak bitiş noktasını atayın:

    ```azurecli
    az spring-cloud app update -n gateway --assign-endpoint true
    ```

2. Uygulamanın çalıştığını doğrulayabilmeniz için **ağ geçidi** uygulamasını ortak IP 'si için sorgulayın:

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>Örnek uygulama deposunu kopyalama ve derleme

1. Aşağıdaki komutu çalıştırarak Git deposunu kopyalayın:

    ```
    git clone https://github.com/Azure-Samples/piggymetrics
    ```
  
1. Dizini değiştirin ve aşağıdaki komutu çalıştırarak projeyi derleyin:

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Azure yay bulutuna yapılandırma ve dağıtım oluşturma

1. Üst Pod 'yi içeren Pıbu ölçümlerin kök klasöründe aşağıdaki komutu çalıştırarak yapılandırma oluşturun. Zaten Azure CLı ile oturum açtıysanız, komut kimlik bilgilerini otomatik olarak seçer. Aksi takdirde, komut istemi yönergeleriyle oturumunuzu açacaksınız. Daha fazla bilgi için bkz. [wiki](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)sayfamız.

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.3.0:config
    ```
    
    Şunları seçmeniz istenir:
    * **Modüller:** `gateway`, `auth-service` Ve seçin `account-service` .
    * **Abonelik:** Bu, Azure yay bulutu örneği oluşturmak için kullanılan aboneliğiniz.
    * **Hizmet örneği:** Bu, Azure Spring Cloud örneğinizin adıdır.
    * **Ortak uç nokta:** Belirtilen projeler listesinde, öğesine karşılık gelen sayıyı girin `gateway` .  Bu, BT 'ye Genel erişim sağlar.

1. Pod artık eklenti bağımlılıklarını ve yapılandırmasını içerir. Aşağıdaki komutu kullanarak uygulamaları dağıtın. 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>IntelliJ 'de örnek projeyi içeri aktar

1. Bu öğretici için kaynak depoyu indirip sıkıştırmasını açın veya Git kullanarak kopyalayın: `git clone https://github.com/Azure-Samples/piggymetrics` 

1. IntelliJ **karşılama** iletişim kutusunu açın, içeri aktar sihirbazını açmak Için **projeyi içeri aktar** ' ı seçin.

1. `piggymetric`Klasör seçin.

    ![Projeyi içeri aktar](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>Ağ Geçidi uygulamasını Azure Spring Cloud 'a dağıtma
Azure 'a dağıtmak için Azure Toolkit for IntelliJ Azure hesabınızla oturum açmanız ve aboneliğinizi seçmeniz gerekir. Oturum açma ayrıntıları için bkz. [yükleme ve oturum açma](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. IntelliJ Proje Gezgini ' nde projenize sağ tıklayın ve **Azure**  ->  **yay bulutu 'na Azure dağıtımı**' nı seçin.

    ![Azure 'a dağıtma 1](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. **Ad** alanına, var olan ada *: ağ geçidini* ekleyin. 
1. **Yapıt** metin kutusunda, *com. pınmetriölçümleri: Gateway: 1.0-Snapshot*' ı seçin.
1. **Abonelik** metin kutusunda aboneliğinizi doğrulayın.
1. **Yay bulutu** metin kutusunda, [Azure Spring Cloud Instance sağlama](./spring-cloud-quickstart-provision-service-instance.md)bölümünde oluşturduğunuz Azure Spring Cloud örneğini seçin.
1. **Genel uç noktayı** *Etkinleştir* olarak ayarlayın.
1. **Uygulama:** metin kutusunda **uygulama oluştur...** seçeneğini belirleyin.
1. *Ağ geçidini* girin, ardından **Tamam**' a tıklayın.

    ![Azure 'a dağıtma Tamam](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. İletişim kutusunun **başlatma öncesi** bölümünde *Maven hedefini Çalıştır*' a çift tıklayın.
1. **Çalışma dizini** metin kutusunda, *Pize ölçümleri/ağ geçidi* klasörüne gidin.
1. **Komut satırı** metin kutusuna *Package-dskiptests* yazın. **Tamam**'a tıklayın.
1. **Azure yay bulut uygulaması dağıtma** iletişim kutusunun alt kısmındaki **Çalıştır** düğmesine tıklayarak dağıtımı başlatın. Eklenti uygulamada komutunu çalıştırır `mvn package` `gateway` ve komut tarafından oluşturulan jar 'yi dağıtır `package` .

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Azure yay bulutuna auth-Service ve Account-Service uygulamaları dağıtma
' İ dağıtmak için yukarıdaki adımları yineleyebilirsiniz `auth-service` ve `account-service` uygulamaları Azure Spring Cloud 'a uygulayabilirsiniz:

1. Uygulamayı tanımlamak için **adı** ve **yapıtı** değiştirin `auth-service` .
1. **Uygulama:** metin kutusunda, uygulamalar oluşturmak Için **uygulama oluştur...** seçeneğini belirleyin `auth-service` .
1. **Ortak uç nokta** seçeneğinin *devre dışı* olarak ayarlandığını doğrulayın.
1. İletişim kutusunun **başlatma öncesi** bölümünde, **çalışma dizinini** *pıbu ölçümler/auth-Service* klasörüne geçirin.
1. **Azure yay bulut uygulaması dağıtma** iletişim kutusunun alt kısmındaki **Çalıştır** düğmesine tıklayarak dağıtımı başlatın. 
1. Yapılandırmak ve dağıtmak için bu yordamları tekrarlayın `account-service` .
---

Plımetrik uygulamasına erişmek için önceki adımlarda çıktıda belirtilen URL 'ye gidin. Örnek: `https://<service instance name>-gateway.azuremicroservices.io`

![Erişim ölçümleri](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

URL 'YI bulmak için Azure portal de gidebilirsiniz. 
1. Hizmete git
2. **Uygulama** seçin
3. **Ağ geçidini** seçin

    ![Uygulamada gezin](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Ağ geçidinde URL 'YI bul **| Genel Bakış** sayfası

    ![Uygulamaya git ikinci](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, abonelikleriniz varsa ücretlendirmeye devam edecek Azure kaynakları oluşturdunuz. Sonraki hızlı başlangıca devam etmeyi düşünmüyorsanız bkz. [Kaynakları Temizleme](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). Aksi takdirde, sonraki hızlı başlangıca ilerleyin:

> [!div class="nextstepaction"]
> [Günlükler, ölçümler ve Izleme](spring-cloud-quickstart-logs-metrics-tracing.md)
