---
title: 'Öğretici: uçtan uca bir çözümü bağlama'
titleSuffix: Azure Digital Twins
description: Cihaz verileri tarafından yönetilen uçtan uca bir Azure dijital TWINS çözümü oluşturmaya yönelik öğretici.
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: f1653158f7a181ad2d61bc726ba7765eab934341
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107491"
---
# <a name="tutorial-build-out-an-end-to-end-solution"></a>Öğretici: uçtan uca bir çözüm oluşturma

Ortamınızdaki canlı veriler tarafından yönetilen tam uçtan uca bir çözüm kurmak için, Azure dijital TWINS örneğinizi cihaz ve verilerin yönetimi için diğer Azure hizmetlerine bağlayabilirsiniz.

Bu öğreticide,...
> [!div class="checklist"]
> * Azure dijital TWINS örneği ayarlama
> * Örnek yapı senaryosu hakkında bilgi edinin ve önceden yazılmış bileşenleri oluşturun
> * Bir [IoT Hub](../iot-hub/about-iot-hub.md) cihazından dijital ikizi özelliklerine sanal telemetri yönlendirmek Için bir [Azure işlevleri](../azure-functions/functions-overview.md) uygulaması kullanma
> * Azure Işlevleri, uç noktaları ve rotalar ile dijital ikizi bildirimlerini işleyerek **ikizi Graph** aracılığıyla değişiklikleri yayma

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell oturum ayarlama
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>Oluşturma senaryosuna başlayın

Bu öğreticide kullanılan örnek proje, bir kat, Oda ve bir termostat cihazı içeren gerçek dünya **oluşturma senaryosunu** temsil eder. Bu bileşenler, verilerin taşınmasını kolaylaştırmak için [IoT Hub](../iot-hub/about-iot-hub.md), [Event Grid](../event-grid/overview.md)ve iki [Azure Işlevi](../azure-functions/functions-overview.md) Ile bağlantılı olacak bir Azure dijital TWINS örneğinde dijital olarak temsil edilir.

Tam senaryoyu temsil eden bir diyagram aşağıda verilmiştir. 

Önce Azure dijital TWINS örneğini (diyagram içinde **bölüm** ) oluşturun, ardından telemetri veri akışını dijital TWINS (**ok B**) ile ayarlayın, sonra da Ikizi Graf (**ok C**) aracılığıyla veri yaymayı ayarlayın.

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Tam bina senaryosunun grafiği. Bir cihazdan bir Azure işlevi (ok B) aracılığıyla bir Azure dijital TWINS örneğine (Bölüm A) kadar bir cihazdan veri akışı IoT Hub, ardından işleme için başka bir Azure işlevine Event Grid kadar (ok C)":::

Senaryo aracılığıyla çalışmak için daha önce indirdiğiniz önceden yazılmış örnek uygulamanın bileşenleriyle etkileşime geçebilirsiniz.

Yapı senaryosu *AdtSampleApp* örnek uygulaması tarafından uygulanan bileşenler şunlardır:
* Cihaz kimlik doğrulaması 
* [.Net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client) kullanım örnekleri ( *commandloop. cs* içinde bulunur)
* Azure dijital TWINS API 'sini çağırmak için konsol arabirimi
* *Sampleclientapp* -örnek bir Azure dijital TWINS çözümü
* *Samplefunctionsapp* -Azure dijital TWINS grafınızı IoT Hub ve Azure dijital TWINS etkinliklerindeki telemetri sonucu olarak güncelleştiren bir Azure işlevleri uygulaması

### <a name="instantiate-the-pre-created-twin-graph"></a>Önceden oluşturulmuş ikizi grafiğini oluşturma

İlk olarak, örnek projeden *AdtSampleApp* çözümünü kullanarak uçtan uca senaryonun Azure dijital TWINS parçasını (**Bölüm A**) oluşturursunuz:

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="Azure dijital TWINS örneği olan, tam yapı senaryosu grafik vurgulama bölümünden bir alıntı":::

_**AdtE2ESample**_ projesinin açık olduğu Visual Studio pencerenizde, araç çubuğunda Bu düğmeyle projeyi çalıştırın:

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="Visual Studio Başlangıç düğmesi (SampleClientApp Projesi)":::

Bir konsol penceresi açılır, kimlik doğrulama işlemi gerçekleştirebilir ve bir komut için bekler. Bu konsolda, örnek Azure dijital TWINS çözümünü oluşturmak için bir sonraki komutu çalıştırın.

> [!IMPORTANT]
> Azure dijital TWINS Örneğinizde zaten dijital TWINS ve ilişkiler varsa, bu komutun çalıştırılması onları siler ve örnek senaryoya ilişkin TWINS ve ilişkilerle değiştirilir.

```cmd/sh
SetupBuildingScenario
```

Bu komutun çıktısı, üç [**dijital TWINS**](concepts-twins-graph.md) 'Nin oluşturulup Azure dijital TWINS Örneğinizde bağlandığı bir dizi onay iletisi vardır: *Floor1* adlı bir kat, *room21* adlı bir oda ve *thermostat67* adlı bir sıcaklık algılayıcısı. Bu dijital TWINS, gerçek dünyada bir ortamda mevcut olan varlıkları temsil eder.

İlişkiler aracılığıyla aşağıdaki [**ikizi grafiğine**](concepts-twins-graph.md)bağlanır. İkizi Graph, varlıkların ile nasıl etkileşime gireceğini ve birbirleriyle nasıl ilişkili olduğunu da kapsayan, ortamı bir bütün olarak temsil eder.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="Floor1 room21 ve room21 Contains thermostat67 içerdiğini gösteren bir grafik" border="false":::

Aşağıdaki komutu çalıştırarak oluşturulan TWINS 'i doğrulayabilirsiniz, bu, içerdiği tüm dijital TWINS için bağlı Azure dijital TWINS örneğini sorgular:

```cmd/sh
Query
```

>[!TIP]
> Bu basitleştirilmiş Yöntem _**AdtE2ESample**_ projesinin bir parçası olarak sağlanır. Bu örnek kod bağlamı dışında, [sorgu API 'lerini](/rest/api/digital-twins/dataplane/query) veya [CLI komutlarını](how-to-use-cli.md)kullanarak, örneğiniz Içindeki tüm TWINS 'leri istediğiniz zaman sorgulayabilirsiniz.
>
> Örneğiniz için tüm dijital TWINS 'i almak için tam sorgu gövdesi aşağıda verilmiştir:
> 
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

Bundan sonra projeyi çalıştırmayı durdurabilirsiniz. Visual Studio 'da çözümü açık tutun, ancak öğreticinin tamamında kullanmaya devam edersiniz.

## <a name="set-up-the-sample-function-app"></a>Örnek işlev uygulamasını ayarlama

Sonraki adımda, bu öğreticide verileri işlemek için kullanılacak bir [Azure işlevleri uygulaması](../azure-functions/functions-overview.md) ayarlanıyor. *Samplefunctionsapp* işlev uygulaması iki işlev içerir:
* *ProcessHubToDTEvents*: gelen IoT Hub verilerini Işler ve Azure dijital TWINS 'i buna göre güncelleştirir
* *Processdtkabteddata*: dijital TWINS 'ten gelen verileri Işler ve Azure dijital TWINS 'de üst TWINS 'i uygun şekilde güncelleştirir

Bu bölümde, önceden yazılmış işlev uygulamasını yayımlayacaksınız ve işlev uygulamasının bir Azure Active Directory (Azure AD) kimliği atayarak Azure dijital TWINS 'e erişebildiğinden emin olursunuz. Bu adımları tamamlamak, öğreticinin geri kalanının işlev uygulaması içindeki işlevleri kullanmasına izin verir. 

_**AdtE2ESample**_ projesinin açık olduğu Visual Studio pencerenizi geri döndüğünüzde, Işlev uygulaması _**Samplefunctionsapp**_ proje dosyasında bulunur. *Çözüm Gezgini* bölmesinde görüntüleyebilirsiniz.

### <a name="update-dependencies"></a>Bağımlılıkları Güncelleştir

Uygulamayı yayımlamadan önce, tüm dahil edilen paketlerin en son sürümüne sahip olduğunuzdan emin olmak için bağımlılıklarınızın güncel olduğundan emin olmanız iyi bir fikirdir.

*Çözüm Gezgini* bölmesinde _**samplefunctionsapp** > bağımlılıklar_' ı genişletin. *Paketler* ' i sağ seçin ve *NuGet Paketlerini Yönet...* seçeneğini belirleyin.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="Visual Studio: SampleFunctionsApp projesi için NuGet paketlerini yönetme" border="false":::

Bu, NuGet Paket Yöneticisi 'Ni açar. *Güncelleştirmeler* sekmesini seçin ve güncelleştirme yapılacak herhangi bir paket varsa, *tüm paketleri seçmek* için kutuyu işaretleyin. Ardından *Güncelleştir*' i ziyaret edin.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="Visual Studio: NuGet paket yöneticisinde tüm paketleri güncelleştirmek için seçme":::

### <a name="publish-the-app"></a>Uygulamayı yayımlama

_**AdtE2ESample**_ projesinin açık olduğu Visual Studio penceresine geri döndüğünüzde, *Çözüm Gezgini* bölmesinde _**samplefunctionsapp**_ projesini bulun.

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

İşlev uygulamanızın Azure dijital TWINS 'e erişebilmesi için Azure dijital TWINS örneğinizi ve örneğin ana bilgisayar adına erişim izinleri olması gerekir. Bunu bir sonraki adımda yapılandıracaksınız.

### <a name="configure-permissions-for-the-function-app"></a>İşlev uygulaması için izinleri yapılandırma

Azure dijital TWINS örneğinize erişmek için işlev uygulamasının ayarlanması gereken iki ayar vardır. Bunlar her ikisi de [Azure Cloud Shell](https://shell.azure.com)komutları aracılığıyla yapılabilir. 

#### <a name="assign-access-role"></a>Erişim rolü ata

İlk ayar, işlev uygulamasını Azure dijital TWINS örneğinde **Azure Digital TWINS veri sahibi** rolünü verir. Bu rol, örnekte birçok veri düzlemi etkinliği gerçekleştirmek isteyen herhangi bir kullanıcı veya işlev için gereklidir. Güvenlik ve rol atamaları hakkında daha fazla bilgi edinmek için bkz. [*Azure dijital TWINS çözümleri Için güvenlik*](concepts-security.md). 

1. İşlev için sistem tarafından yönetilen kimliğin ayrıntılarını görmek için aşağıdaki komutu kullanın. Çıktıda **PrincipalId** alanını bir yere göz atın.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Bir kimliğin ayrıntılarını göstermek yerine sonuç boşsa, bu komutu kullanarak işlev için yeni bir sistem tarafından yönetilen kimlik oluşturun:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > Daha sonra çıktı, sonraki adım için gereken **PrincipalId** değeri de dahil olmak üzere kimliğin ayrıntılarını görüntüler. 

1. **principalId** değerini aşağıdaki komutta kullanarak işlev uygulamasının kimliğini Azure Digital Twins örneğinizin **Azure Digital Twins Veri Sahibi** rolüne atayın.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

Bu komutun sonucu, oluşturduğunuz rol ataması hakkında bilgi verilir. İşlev uygulamasının artık Azure dijital TWINS örneğinizdeki verilere erişme izinleri vardır.

#### <a name="configure-application-settings"></a>Uygulama ayarlarını yapılandırma

İkinci ayar, Azure dijital TWINS örneğinizin URL 'sini içeren işlev için bir **ortam değişkeni** oluşturur. İşlev kodu, örneğinizi ifade etmek için bunu kullanacaktır. Ortam değişkenleri hakkında daha fazla bilgi için bkz. [*işlev uygulamanızı yönetme*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

Aşağıdaki komutu çalıştırarak yer tutucuları kaynaklarınızın ayrıntılarına göre doldurun.

```azurecli-interactive
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```

Çıktı, artık **ADT_SERVICE_URL** adlı bir giriş Içermesi gereken Azure işlevi için ayarların listesidir.


## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>IoT Hub cihazdan sanal telemetri işleme

Azure dijital TWINS grafiğinin, gerçek cihazlardan telemetri tarafından kullanılması amaçlanmıştır. 

Bu adımda, [IoT Hub](../iot-hub/about-iot-hub.md) kayıtlı bir sanal bir termostat cihazını Azure dijital TWINS 'te temsil eden dijital ikizi bağlayacaksınız. Sanal cihaz Telemetriyi yayar, veriler, dijital ikizi 'de karşılık gelen bir güncelleştirmeyi tetikleyen *ProcessHubToDTEvents* Azure işlevi aracılığıyla yönlendirilir. Bu şekilde, dijital ikizi gerçek cihazın verileriyle güncel kalır. Azure dijital TWINS 'de, olay verilerini bir konumdan diğerine yönlendiren işlem [**yönlendirme olayları**](concepts-route-events.md)olarak adlandırılır.

Bu, uçtan uca senaryonun bu bölümünde gerçekleşir (**ok B**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="Tam bina senaryosu grafik vurgulama oku B, Azure dijital TWINS 'den önceki öğeler: cihaz, IoT Hub ve ilk Azure işlevi":::

Bu cihaz bağlantısını kurmak için tamamlayacaksınız eylemler şunlardır:
1. Sanal cihazı yönetecek bir IoT Hub 'ı oluşturma
2. Bir olay aboneliği ayarlayarak IoT Hub 'ını uygun Azure işlevine bağlama
3. Sanal cihazı IoT Hub 'a kaydetme
4. Sanal cihazı çalıştırma ve telemetri oluşturma
5. Canlı sonuçları görmek için Azure dijital TWINS 'i sorgulama

### <a name="create-an-iot-hub-instance"></a>IoT Hub örneği oluşturma

Azure dijital TWINS, cihazları ve bunların verilerini yönetmeye yönelik bir Azure hizmeti olan [IoT Hub](../iot-hub/about-iot-hub.md)birlikte çalışmak üzere tasarlanmıştır. Bu adımda, bu öğreticide örnek cihazı yönetecek bir IoT Hub 'ı ayarlayacaksınız.

Azure Cloud Shell ' de, yeni bir IoT Hub 'ı oluşturmak için bu komutu kullanın:

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

Bu komutun çıktısı, oluşturulan IoT Hub 'ı hakkında bilgi olur.

IoT Hub 'ınıza verdiğiniz **adı** kaydedin. Daha sonra bu adı kullanacaksınız.

### <a name="connect-the-iot-hub-to-the-azure-function"></a>IoT Hub 'ını Azure işlevine bağlama

IoT Hub daha sonra, Azure Digital TWINS ' i güncelleştiren, IoT Hub 'ınızı daha önce yayımladığınız işlev uygulamasındaki *ProcessHubToDTEvents* Azure işlevine bağlayın.

Bunu yapmak için, Azure işleviyle bir uç nokta olarak IoT Hub bir **olay aboneliği** oluşturacaksınız. Bu, işlevi IoT Hub oluşan olaylara abone olur.

[Azure Portal](https://portal.azure.com/), en üstteki arama çubuğunda adını arayarak yeni oluşturduğunuz IoT Hub 'ınıza gidin. Hub menüsünden *olay* ' ı seçin ve *+ olay aboneliği*' ni seçin.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Azure portal: IoT Hub olay aboneliği":::

Bu işlem *olay aboneliği oluştur* sayfasını getirir.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Azure portal: olay aboneliği oluştur":::

Alanları aşağıda gösterildiği gibi (varsayılan olarak doldurulan alanlar bahsedilmez) girin:
* *olay ABONELIĞI ayrıntıları*  >  **Ad**: olay aboneliğinize bir ad verin.
* *konu ayrıntıları*  >  **Sistem konu adı**: sistem konusu için kullanılacak bir ad verin. 
* *olay türleri*  >  **Olay türlerine filtrele**: menü seçeneklerinden *cihaz telemetrisi* ' ni seçin.
* *uç nokta ayrıntıları*  >  **Uç nokta türü**: menü seçeneklerinden *Azure işlevi* ' ni seçin.
* *uç nokta ayrıntıları*  >  **Uç nokta**: *uç nokta seçin* bağlantısına tıklayın. Bu işlem bir *Azure Işlevi Seç* penceresi açar: :::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="olay aboneliği Azure Portal: Azure işlevi seçin" border="false":::
    - **Aboneliğiniz**, **kaynak grubunuz**, **işlev uygulaması** ve **işlevinizi** (*ProcessHubToDTEvents*) girin. Bunlardan bazıları abonelik seçildikten sonra otomatik olarak doldurulabilir.
    - **Seçimi Onayla**' ya basın.

*Olay aboneliği oluştur* sayfasına dönün ve **Oluştur**' a basın.

### <a name="register-the-simulated-device-with-iot-hub"></a>Sanal cihazı IoT Hub Kaydet 

Bu bölüm, IoT Hub KIMLIĞI *thermostat67* olan bir cihaz temsili oluşturur. Sanal cihaz buna bağlanır ve telemetri olaylarının cihazdan IoT Hub 'e gitmesi, bu da önceki adımdaki abone olunan Azure işlevinin dinleme yaptığı, olayların hazırlanmaya ve işlemeye devam etmesine hazırlanma gibi bir deyişle

Azure Cloud Shell, aşağıdaki komutla IoT Hub bir cihaz oluşturun:

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

Çıktı, oluşturulan cihazla ilgili bilgiler.

### <a name="configure-and-run-the-simulation"></a>Simülasyonu yapılandırma ve çalıştırma

Şimdi cihaz simülatörünü yapılandırın ve IoT Hub örneğinize veri gönderin.

Şu komutla *IoT Hub bağlantı dizesini* alarak başlayın:

```azurecli-interactive
az iot hub connection-string show -n <your-IoT-hub-name>
```

Ardından şu komutla *cihaz bağlantı dizesini* alın:

```azurecli-interactive
az iot hub device-identity connection-string show --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

Simülatörü bu IoT Hub 'ına ve IoT Hub cihazına bağlamak için bu değerleri yerel projenizdeki cihaz simülatör koduna bağlayacaksınız.

Yeni bir Visual Studio penceresinde (indirilen çözüm klasöründen) _cihaz simülatörü > **devicesimülatör. sln**_' yi açın.

>[!NOTE]
> Artık, biri _**Devicesimülatör. sln**_ ve _**AdtE2ESample. sln**_ ile bir tane olmak üzere iki Visual Studio sürümüne sahip olmanız gerekir.

Bu yeni Visual Studio penceresindeki *Çözüm Gezgini* bölmesinde, Düzen _simülatörü/**AzureIoTHub. cs**_ ' yi seçerek dosyayı Düzenle penceresinde açın. Aşağıdaki bağlantı dizesi değerlerini yukarıda topladığınız değerlerle değiştirin:

```csharp
iotHubConnectionString = <your-hub-connection-string>
deviceConnectionString = <your-device-connection-string>
```

Dosyayı kaydedin.

Şimdi, ayarlamış olduğunuz veri benzetiminin sonuçlarını görmek için, araç çubuğunda Bu düğmeyle **Devicesimülatör** projesini çalıştırın:

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="Visual Studio Başlangıç düğmesi (Devicesimülatör Projesi)":::

Bir konsol penceresi açılır ve sanal sıcaklık telemetri iletilerini görüntüler. Bunlar IoT Hub gönderilir, burada Azure işlevi tarafından alınır ve işlenir.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Gönderilen sıcaklık telemetrisini gösteren cihaz simülatörü konsol çıkışı":::

Bu konsolda başka bir şey yapmanız gerekmez, ancak sonraki adımları tamamlayarak çalışır durumda bırakın.

### <a name="see-the-results-in-azure-digital-twins"></a>Azure dijital TWINS 'deki sonuçlara bakın

Daha önce yayımladığınız *ProcessHubToDTEvents* işlevi IoT Hub verileri dinler ve bir Azure dijital TWıNS API 'sini çağırarak *Thermostat67* ikizi üzerindeki *sıcaklık* özelliğini güncelleştirir.

Azure dijital TWINS tarafındaki verileri görmek için _**AdtE2ESample**_ projesinin açık olduğu Visual Studio penceresine gidin ve projeyi çalıştırın.

Açılan proje konsolu penceresinde, dijital ikizi *thermostat67* tarafından bildirilen sıcaklıkları almak için aşağıdaki komutu çalıştırın:

```cmd
ObserveProperties thermostat67 Temperature
```

*Azure dijital TWINS örneğinizin* , her iki saniyede bir konsola kaydedilen canlı güncelleştirilmiş sıcaklıkları görmeniz gerekir.

>[!NOTE]
> Cihazdaki verilerin ikizi 'e yayılması birkaç saniye sürebilir. İlk birkaç sıcaklık readsler, verilerin gelmesi başlamadan önce 0 olarak gösterilebilir.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="Dijital ikizi thermostat67 'ten sıcaklık iletilerinin günlüğünü gösteren konsol çıkışı":::

Bu işlemi başarıyla çalıştığını doğruladıktan sonra, her iki projenin da çalıştırılmasını durdurabilirsiniz. Visual Studio pencerelerini öğreticinin geri kalanında kullanmaya devam edecek şekilde açık tutun.

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Azure dijital TWINS olaylarını Graf aracılığıyla yayma

Bu öğreticide, Azure Digital TWINS 'in dış cihaz verilerinden nasıl güncelleştirileceğini gördünüz. Daha sonra, bir dijital ikizi yapılan değişikliklerin Azure dijital TWINS grafından nasıl yayabileğinden (diğer bir deyişle, hizmet dahili verilerden TWINS 'i güncelleştirme) göreceksiniz.

Bunu yapmak için, bağlı *termostat* ikizi güncelleştirildiği zaman bir *Oda* Ikizi güncellemek üzere *processdtkabteddata* Azure işlevini kullanacaksınız. Bu, uçtan uca senaryonun bu bölümünde gerçekleşir (**ok C**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="Tam bina senaryosu grafik vurgulama ok C, Azure dijital TWINS 'den sonraki öğeler: Event Grid ve ikinci Azure işlevi":::

Bu veri akışını ayarlamak için tamamlayacaksınız eylemler şunlardır:
1. Azure dijital TWINS 'te örneği Event Grid 'ye bağlayan Event Grid uç noktası oluşturma
2. Azure dijital TWINS içinde, ikizi özellik değişikliği olaylarını uç noktaya göndermek için bir yol ayarlayın
3. Uç noktada dinleme yapan ( [Event Grid](../event-grid/overview.md)) bir Azure işlevleri uygulaması dağıtın ve diğer TWINS 'i buna uygun olarak güncelleştirir
4. Sanal cihazı çalıştırın ve canlı sonuçları görmek için Azure dijital TWINS 'i sorgulayın

### <a name="set-up-endpoint"></a>Uç noktayı ayarla

[Event Grid](../event-grid/overview.md) , Azure hizmetlerinden gelen olayları Azure 'daki diğer yerlere yönlendirmenize ve sunmanıza yardımcı olan bir Azure hizmetidir. Bir kaynaktan belirli olayları toplamak için bir [olay Kılavuzu konusu](../event-grid/concepts.md) oluşturabilir ve daha sonra aboneler, olayları geldiği şekilde almak için konu üzerinde dinleme yapabilir.

Bu bölümde, bir olay Kılavuzu konusu oluşturun ve ardından Azure dijital TWINS içinde, bu konuya işaret eden (olayları gönderen) bir uç nokta oluşturursunuz. 

Azure Cloud Shell bir olay Kılavuzu konusu oluşturmak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az eventgrid topic create -g <your-resource-group> --name <name-for-your-event-grid-topic> -l <region>
```

> [!TIP]
> Azure CLı 'de komutlara geçirilebilecek Azure bölge adlarının bir listesini çıkarmak için şu komutu çalıştırın:
> ```azurecli-interactive
> az account list-locations -o table
> ```

Bu komutun çıktısı, oluşturduğunuz olay Kılavuzu konusu hakkında bilgi içermektedir.

Daha sonra, Azure dijital TWINS 'de bir Event Grid uç noktası oluşturun ve bu, örneğinizi olay Kılavuzu konu başlığına bağlayacaktır. Aşağıdaki komutu kullanarak yer tutucu alanlarını gerektiği şekilde doldurun:

```azurecli-interactive
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

Bu komutun çıktısı, oluşturduğunuz uç nokta hakkında bilgi.

Ayrıca, bu uç nokta için Azure dijital TWINS örneğinizi sorgulamak üzere aşağıdaki komutu çalıştırarak bitiş noktası oluşturma 'nın başarılı olduğunu doğrulayabilirsiniz:

```azurecli-interactive
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

`provisioningState`Çıktıda alanı bulun ve değerin "başarılı" olup olmadığını kontrol edin. Ayrıca "sağlama" da olabilir, yani uç noktanın oluşturulduğu anlamına gelir. Bu durumda, birkaç saniye bekleyin ve başarıyla tamamlandığını denetlemek için komutu yeniden çalıştırın.

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="Uç nokta sorgusunun sonucu, bir provisioningState 'in başarılı olduğunu gösteriyor":::

**Olay kılavuzunuza** verdiğiniz adları ve Azure dijital TWINS 'teki Event Grid **uç** noktanızı kaydedin. Bunları daha sonra kullanacaksınız.

### <a name="set-up-route"></a>Rotayı ayarlama

Ardından, az önce oluşturduğunuz Event Grid uç noktasına olayları gönderen bir Azure dijital TWINS yolu oluşturun.

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

Bu komutun çıktısı, oluşturduğunuz rota hakkında bazı bilgiler.

>[!NOTE]
>Uç noktaların (önceki adımdan), bunları kullanan bir olay yolu ayarlamadan önce sağlanması gerekir. Uç noktalar hazırlanmadığı için yol oluşturma başarısız olursa birkaç dakika bekleyip yeniden deneyin.

#### <a name="connect-the-function-to-event-grid"></a>İşlevi Event Grid bağlayın

Daha önce, *Processdtkabteddata* Azure işlevini daha önce oluşturduğunuz olay kılavuzu konusuna abone olmak için telemetri verileri, Azure dijital TWINS 'e geri dönerek ve *room21* ikizi uygun şekilde *thermostat67* ikizi.

Bunu yapmak için, daha önce *Processdtkabteddata* Azure işlevinizde oluşturduğunuz **olay Kılavuzu konusundan** veri gönderen **Event Grid bir abonelik** oluşturacaksınız.

[Azure Portal](https://portal.azure.com/), en üstteki arama çubuğunda adını arayarak olay kılavuzunuza gidin. *+ Olay Aboneliği*'ni seçin.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Azure portal: Event Grid olay aboneliği":::

Bu olay aboneliğini oluşturma adımları, bu öğreticide daha önce IoT Hub ilk Azure işlevine abone olduğunuz zamana benzer. Bu kez, dinlenecek olay türü olarak *cihaz telemetrisini* belirtmeniz gerekmez ve farklı bir Azure işlevine bağlanırsınız.

*Olay aboneliği oluştur* sayfasında, alanları aşağıdaki gibi girin (varsayılan olarak doldurulmuş alanlar verilmez):
* *olay ABONELIĞI ayrıntıları*  >  **Ad**: olay aboneliğinize bir ad verin.
* *uç nokta ayrıntıları*  >  **Uç nokta türü**: menü seçeneklerinden *Azure işlevi* ' ni seçin.
* *uç nokta ayrıntıları*  >  **Uç nokta**: *uç nokta seçin* bağlantısına tıklayın. Bu işlem bir *Azure Işlevi Seç* penceresi açar:
    - **Aboneliğiniz**, **kaynak grubunuz**, **işlev uygulaması** ve **işlevinizi** (*processdtkabteddata*) girin. Bunlardan bazıları abonelik seçildikten sonra otomatik olarak doldurulabilir.
    - **Seçimi Onayla**' ya basın.

*Olay aboneliği oluştur* sayfasına dönün ve **Oluştur**' a basın.

### <a name="run-the-simulation-and-see-the-results"></a>Benzetimi çalıştırın ve sonuçları görüntüleyin

Artık ayarladığınız yeni olay akışını çalıştırmak için cihaz simülatörünü çalıştırabilirsiniz. _**Devicesimülatör**_ projesinin açık olduğu Visual Studio pencerenize gidin ve projeyi çalıştırın.

Cihaz simülatörünü daha önce çalıştırdığınızda olduğu gibi, bir konsol penceresi açılır ve sanal sıcaklık telemetri iletilerini görüntüler. Bu olaylar, daha önce *thermostat67* ikizi ' ı güncellemek için ayarladığınız akışdan geçer ve ardından son zamanlarda, *room21* ikizi ile eşleşecek şekilde güncelleyeceksiniz.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Gönderilen sıcaklık telemetrisini gösteren cihaz simülatörü konsol çıkışı":::

Bu konsolda başka bir şey yapmanız gerekmez, ancak sonraki adımları tamamlayarak çalışır durumda bırakın.

Azure dijital TWINS tarafındaki verileri görmek için _**AdtE2ESample**_ projesinin açık olduğu Visual Studio penceresine gidin ve projeyi çalıştırın.

Açılan proje konsolu penceresinde, hem dijital ikizi *thermostat67* hem **de** dijital ikizi *room21* tarafından bildirilen sıcaklıkları almak için aşağıdaki komutu çalıştırın.

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

*Azure dijital TWINS örneğinizin* , her iki saniyede bir konsola kaydedilen canlı güncelleştirilmiş sıcaklıkları görmeniz gerekir. *Room21* için sıcaklık güncelleştirildiğinden güncelleştirmeler *thermostat67* ile eşleşecek şekilde güncellendiğine dikkat edin.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="Bir termostat ve odadan sıcaklık iletilerinin günlüğünü gösteren konsol çıkışı":::

Bu işlemi başarıyla çalıştığını doğruladıktan sonra, her iki projenin da çalıştırılmasını durdurabilirsiniz. Öğretici artık tamamlanmış olduğundan Visual Studio pencerelerini de kapatabilirsiniz.

## <a name="review"></a>Gözden geçirme

Bu öğreticide oluşturduğunuz senaryonun incelenmesi aşağıda verilmiştir.

1. Bir Azure dijital TWINS örneği, bir tabanı, odayı ve bir termostat 'yi (Aşağıdaki diyagramda **bölüm a** tarafından temsil edilir) dijital olarak temsil eder
2. Sanal cihaz telemetrisi, *ProcessHubToDTEvents* Azure işlevinin telemetri olaylarını dinlediği IoT Hub ' a gönderilir. *ProcessHubToDTEvents* Azure işlevi, bu olaylardaki bilgileri, *thermostat67* üzerindeki *sıcaklık* özelliğini ayarlamak için kullanır (diyagramda **ok B** ).
3. Azure dijital TWINS 'teki özellik değişiklik olayları, *Processdtkabteddata* Azure işlevinin olayları dinlediği bir Event Grid konusuna yönlendirilir. *Processdtkabteddata* Azure işlevi, bu olaylardaki bilgileri, *room21* üzerindeki *sıcaklık* özelliğini ayarlamak için kullanır (Diyagramdaki **ok C** ).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Tam bina senaryosunun grafiği. Bir cihazdan bir Azure işlevi (ok B) aracılığıyla bir Azure dijital TWINS örneğine (Bölüm A) kadar bir cihazdan veri akışı IoT Hub, ardından işleme için başka bir Azure işlevine Event Grid kadar (ok C)":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu Öğreticiyi tamamladıktan sonra, ne yapmak istediğinize bağlı olarak kaldırmak istediğiniz kaynakları seçebilirsiniz.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

* **Bu makalede ayarladığınız Azure dijital TWINS örneğini kullanmaya devam etmek, ancak modellerinin, TWINS ve ilişkilerinin bazılarını veya tümünü temizlemek** için, kaldırmak istediğiniz öğeleri silmek üzere bir [Azure Cloud Shell](https://shell.azure.com) penceresinde [az DT](/cli/azure/dt) CLI komutlarını kullanabilirsiniz.

    Bu seçenek, bu öğreticide (IoT Hub, Azure Işlevleri uygulaması vb.) oluşturulan diğer Azure kaynaklarından hiçbirini kaldırmayacak. Her kaynak türü için uygun olan [dt komutlarını](/cli/azure/reference-index) kullanarak bunları tek tek silebilirsiniz.

Yerel makinenizden proje klasörünü de silmek isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, canlı cihaz verileri tarafından yürütülen Azure dijital TWINS 'i gösteren bir uçtan uca senaryo oluşturdunuz.

Daha sonra, öğreticide birlikte çalıştığınız öğeler hakkında daha fazla bilgi edinmek için kavram belgelerini arayarak başlayın:

> [!div class="nextstepaction"]
> [*Kavramlar: özel modeller*](concepts-models.md)