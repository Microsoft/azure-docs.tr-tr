---
title: Azure SignalR Hizmeti ile tümleştirme
titleSuffix: Azure Digital Twins
description: Azure SignalR kullanarak Azure dijital TWINS telemetrisini istemcilere nasıl akışla kullanabileceğinizi öğrenin
author: dejimarquis
ms.author: aymarqui
ms.date: 02/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e8bdb843ab6304f2f38228f37d8709e4084ee52e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775340"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Azure Digital TWINS 'i Azure SignalR hizmeti ile tümleştirme

Bu makalede, Azure Digital TWINS 'i [Azure SignalR hizmeti](../azure-signalr/signalr-overview.md)ile tümleştirmeyi öğreneceksiniz.

Bu makalede açıklanan çözüm, dijital ikizi telemetri verilerini tek bir Web sayfası veya mobil uygulama gibi bağlı istemcilere göndermenize olanak tanır. Sonuç olarak, istemciler gerçek zamanlı ölçümler ve IoT cihazlarından, sunucu yoklamaya veya güncelleştirmeler için yeni HTTP istekleri göndermeye gerek kalmadan gerçek zamanlı ölçümler ve durum ile güncelleştirilir.

## <a name="prerequisites"></a>Önkoşullar

Devam etmeden önce gerçekleştirmeniz gereken önkoşullar şunlardır:

* Çözümünüzü Azure SignalR hizmeti ile tümleştirmadan önce, Azure Digital TWINS öğreticisini tamamlamalısınız: Bu nasıl yapılır makalesi bunun üzerine derleme yaptığından uçtan [_**uca bir çözüm bağlayın**_](tutorial-end-to-end.md). Öğreticide, dijital ikizi güncelleştirmelerini tetiklemek için bir sanal IoT cihazıyla birlikte çalışarak bir Azure dijital TWINS örneği ayarlama işlemi adım adım gösterilmektedir. Bu nasıl yapılır makalesi, Azure SignalR hizmetini kullanarak bu güncelleştirmeleri örnek bir Web uygulamasına bağlayacaktır.

* Öğreticide aşağıdaki değerler gerekir:
  - Olay Kılavuzu konusu
  - Kaynak grubu
  - App Service/Function uygulama adı
    
* Makinenizde yüklü [**Node.js**](https://nodejs.org/) gerekir.

Ayrıca, Azure hesabınızla [Azure Portal](https://portal.azure.com/) de devam edip oturum açmanız gerekir.

## <a name="solution-architecture"></a>Çözüm mimarisi

Azure SignalR hizmetini aşağıdaki yoldan Azure dijital TWINS 'e iliştirirsiniz. Diyagramdaki A, B ve C bölümleri, [uçtan uca öğretici önkoşulun](tutorial-end-to-end.md)mimari diyagramından alınmıştır. Bu nasıl yapılır makalesinde, var olan mimaride bölüm D oluşturacaksınız.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Uçtan uca bir senaryoda Azure hizmetlerinin bir görünümü. Bir cihazdan bir Azure işlevi (ok B) aracılığıyla bir Azure dijital TWINS örneğine (Bölüm A) kadar bir cihazdan veri akışını IoT Hub, ardından işleme için başka bir Azure işlevine Event Grid (ok C) ile kullanıma gösterir. Bölüm D, ok C 'deki aynı Event Grid veri akışını, ' Broadcast ' etiketli bir Azure Işlevine gösterir. ' Broadcast ', ' anlaş ' etiketli başka bir Azure işlevi ile iletişim kurar ve bilgisayar cihazlarıyla birlikte ' Broadcast ' ve ' Negotiate ' iletişim kurar." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>Örnek uygulamaları indirme

İlk olarak, gerekli örnek uygulamaları indirin. Aşağıdakilerin her ikisine de ihtiyacınız olacak:
* [**Azure dijital TWINS uçtan uca örnekler**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/): Bu örnek, verileri bir Azure dijital TWINS örneği etrafında taşımak Için iki Azure işlevi tutan bir *AdtSampleApp* içerir (öğreticide daha ayrıntılı bilgi edinmek için bkz. [*uçtan uca bir çözümü bağlama*](tutorial-end-to-end.md)). Ayrıca, bir IoT cihazına benzetiren, her saniye yeni bir sıcaklık değeri oluşturan bir *Devicesimülatör* örnek uygulaması da içerir.
    - [*Önkoşulları önkoşul*](#prerequisites)olarak öğreticinin bir parçası olarak henüz indirmediyseniz örnek bağlantısına gidin ve başlığın altındaki *kodu görüntüle* düğmesine [tıklayın](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) . Bu işlem sizi, örnek olarak indirebileceğiniz GitHub deposuna götürür *.* *Kod* düğmesini seçip zip ' i *indirin*.

        :::image type="content" source="media/includes/download-repo-zip.png" alt-text="GitHub 'da dijital-TWINS-örnek deposunun görünümü. Kod düğmesi seçilidir ve posta Indirme düğmesinin vurgulandığı küçük bir iletişim kutusu oluşturulur." lightbox="media/includes/download-repo-zip.png":::

    Bu işlem, örnek deponun bir kopyasını **digital-twins-samples-master.zip** olarak makinenize indirir. Klasörün sıkıştırmasını açın.
* [**SignalR tümleştirmesi Web uygulaması örneği**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/): Bu, Azure SignalR hizmetinden Azure dijital TWINS telemetri verilerini tüketen örnek bir tepki veren Web uygulamasıdır.
    -  Örnek bağlantıya gidin ve örneğin bir kopyasını makinenize indirmek için aynı indirme işlemini kullanın, _**digitaltwins-signalr-webapp-sample-main.zip**_. Klasörün sıkıştırmasını açın.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Bir sonraki bölümde kullanacağınız gibi tarayıcı penceresini Azure portal açık bırakın.

## <a name="publish-and-configure-the-azure-functions-app"></a>Azure Işlevleri uygulamasını yayımlama ve yapılandırma

Bu bölümde, iki Azure işlevi ayarlayacaksınız:
* **Negotiate** -bir http tetikleyici işlevi. Geçerli bağlantı bilgilerini oluşturmak ve döndürmek için *Signalrconnectionınfo* giriş bağlamasını kullanır.
* **Broadcast** - [Event Grid](../event-grid/overview.md) tetikleyici işlevi. Olay kılavuzuyla Azure dijital TWINS telemetri verilerini alır ve önceki adımda oluşturduğunuz *SignalR* örneğinin çıkış bağlamasını kullanarak iletiyi tüm bağlı istemci uygulamalarına yayınlar.

Visual Studio 'Yu (veya seçtiğiniz başka bir kod düzenleyicisini) başlatın ve Code çözümünü *Digital-TWINS-Samples-master > ADTSampleApp* klasöründe açın. Ardından, işlevleri oluşturmak için aşağıdaki adımları uygulayın:

1. *Samplefunctionsapp* projesinde, **signalrfunctions. cs** adlı yeni bir C# sınıfı oluşturun.

1. Sınıf dosyasının içeriğini aşağıdaki kodla değiştirin:
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. Visual Studio 'nun *Paket Yöneticisi konsol* penceresinde veya makinenizde herhangi bir komut penceresinde, *Digital-Twins-Samples-master\AdtSampleApp\SampleFunctionsApp* klasörüne gidin ve `SignalRService` NuGet paketini projeye yüklemek için şu komutu çalıştırın:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Bu, sınıfındaki tüm bağımlılık sorunlarını çözmelidir.

1. *Bir uçtan uca çözüm oluşturma* öğreticisinin [ *uygulamayı yayımlama* bölümünde](tutorial-end-to-end.md#publish-the-app) açıklanan adımları kullanarak işlevinizi Azure 'da yayımlayın. Bunu, uçtan uca öğretici [ön](#prerequisites)eki 'nde kullandığınız App Service/Function uygulamasında yayımlayabilir veya yeni bir tane oluşturabilirsiniz, ancak aynı çoğaltmayı en aza indirmek için aynı olanı kullanmak isteyebilirsiniz. 

Sonra, işlevleri Azure SignalR örneğinizle iletişim kuracak şekilde yapılandırın. SignalR örneğinin **bağlantı dizesini** toplayıp başlatın ve ardından işlevler uygulamasının ayarlarına ekleyin.

1. [Azure Portal](https://portal.azure.com/) gidin ve portalın üst kısmındaki arama çubuğunda SignalR örneğinizin adını arayın. Açmak için örneği seçin.
1. SignalR hizmeti örneği için bağlantı dizelerini görüntülemek için örnek menüsünde **anahtarlar** ' ı seçin.
1. Birincil bağlantı dizesini kopyalamak için *Kopyala* simgesini seçin.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="SignalR örneği için anahtarlar sayfasını gösteren Azure portal ekran görüntüsü. Birincil bağlantı DIZESININ yanındaki ' panoya kopyala ' simgesi vurgulanır." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

1. Son olarak, aşağıdaki Azure CLı komutunu kullanarak, işlevin uygulama ayarlarına Azure SignalR **Bağlantı dizenizi** ekleyin. Ayrıca, yer tutucuları kaynak grubunuz ve App Service/Function uygulamanızın adı ile birlikte [öğretici önkoşulu](how-to-integrate-azure-signalr.md#prerequisites)ile değiştirin. Bu komut, [makinenizde yüklü](/cli/azure/install-azure-cli)olan Azure CLI 'niz varsa [Azure Cloud Shell](https://shell.azure.com)veya yerel olarak çalıştırılabilir:
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    Bu komutun çıktısı, Azure işleviniz için ayarlanmış tüm uygulama ayarlarını yazdırır. `AzureSignalRConnectionString`Eklendiğinden emin olmak için listenin en altında bulunan bölümüne bakın.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Bir komut penceresinde çıkış alıntısı, ' AzureSignalRConnectionString ' adlı bir liste öğesi gösteriliyor":::

#### <a name="connect-the-function-to-event-grid"></a>İşlevi Event Grid bağlayın

Ardından, *yayın* Azure işlevini, [öğretici önkoşulu](how-to-integrate-azure-signalr.md#prerequisites)sırasında oluşturduğunuz **olay kılavuzu konusuna** abone olun. Bu, Telemetri verilerinin thermostat67 ikizi 'den Event Grid konusunun ve işlevine akmasını sağlar. Bu işlev buradan, verileri tüm istemcilere yayınlayabilir.

Bunu yapmak için, olay kılavuzunuzun konusundan bir uç nokta olarak *yayın* Azure Işlevinizden bir **olay aboneliği** oluşturacaksınız.

[Azure Portal](https://portal.azure.com/), en üstteki arama çubuğunda adını arayarak olay kılavuzunuza gidin. *+ Olay Aboneliği*'ni seçin.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Azure portal: Event Grid olay aboneliği":::

*Olay aboneliği oluştur* sayfasında, alanları aşağıdaki gibi girin (varsayılan olarak doldurulmuş alanlar verilmez):
* *olay ABONELIĞI ayrıntıları*  >  **Ad**: olay aboneliğinize bir ad verin.
* *uç nokta ayrıntıları*  >  **Uç nokta türü**: menü seçeneklerinden *Azure işlevi* ' ni seçin.
* *uç nokta ayrıntıları*  >  **Uç nokta**: *uç nokta seçin* bağlantısına tıklayın. Bu işlem bir *Azure Işlevi Seç* penceresi açar:
    - **Aboneliğiniz**, **kaynak grubunuz**, **işlev uygulaması** ve **işlevinizi** (*yayın*) girin. Bunlardan bazıları abonelik seçildikten sonra otomatik olarak doldurulabilir.
    - **Seçimi Onayla**' ya basın.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Olay aboneliği oluşturma Azure portal görünümü. Yukarıdaki alanlar doldurulmuştur ve ' seçimi onayla ' ve ' oluştur ' düğmeleri vurgulanır.":::

*Olay aboneliği oluştur* sayfasına dönün ve **Oluştur**' a basın.

Bu noktada, *Event Grid konu* sayfasında iki olay aboneliği görmeniz gerekir.

:::image type="content" source="media/how-to-integrate-azure-signalr/view-event-subscriptions.png" alt-text="Olay Kılavuzu konu sayfasındaki iki olay aboneliğinin Azure portal görünümü." lightbox="media/how-to-integrate-azure-signalr/view-event-subscriptions.png":::

## <a name="configure-and-run-the-web-app"></a>Web uygulamasını yapılandırma ve çalıştırma

Bu bölümde, sonucu eylem olarak görürsünüz. İlk olarak, ayarlamış olduğunuz Azure SignalR akışına bağlanmak için **örnek istemci Web uygulamasını** yapılandırın. Daha sonra, Azure dijital TWINS örneğiniz aracılığıyla telemetri verileri gönderen **sanal cihaz örnek uygulamasını** başlacaksınız. Bundan sonra örnek Web uygulamasını görüntüleyerek, örnek Web uygulamasını gerçek zamanlı olarak güncelleştiren sanal cihaz verilerini görürsünüz.

### <a name="configure-the-sample-client-web-app"></a>Örnek istemci Web uygulamasını yapılandırma

Ardından, örnek istemci Web uygulamasını yapılandıracaksınız. *Negotiate* işlevinin **http uç noktası URL 'sini** toplayıp başlatın ve sonra makinenizde uygulama kodunu yapılandırmak için kullanın.

1. Azure portal [işlev uygulamaları](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) sayfasına gidin ve listeden işlev uygulamanızı seçin. Uygulama menüsünde *işlevler* ' i seçin ve *Negotiate* işlevini seçin.

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Menüde ' Functions ' vurgulanmış olarak işlev uygulamasının Azure portal görünümü. İşlevlerin listesi sayfada gösterilir ve ' anlaş ' işlevi de vurgulanır.":::

1. *İşlev URL 'Sini Al ' a* gidin ve değeri **_/API_ aracılığıyla kopyalayın (son _/Negotiate_'i eklemeyin?)**. Bunu bir sonraki adımda kullanacaksınız.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="' Anlaş ' işlevinin Azure portal görünümü. ' İşlev URL 'sini Al ' düğmesi vurgulanır ve URL 'nin kısmı '/API ' ile başlayarak başlar":::

1. Visual Studio 'Yu veya tercih ettiğiniz herhangi bir kod düzenleyicisini kullanarak, [*örnek uygulamaları indirin*](#download-the-sample-applications) bölümünde indirdiğiniz unzip olmayan _**digitaltwins-SignalR-WebApp-Sample-Main**_ klasörünü açın.

1. *Src/App.js* dosyasını açın ve IÇINDEKI işlev URL 'sini, `HubConnectionBuilder` önceki adımda KAYDETTIĞINIZ **Negotiate** işlevinin HTTP uç noktası URL 'si ile değiştirin:

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<Function URL>')
            .build();
    ```
1. Visual Studio 'nun *Geliştirici komut isteminde* veya makinenizde herhangi bir komut penceresinde *digitaltwins-SignalR-WebApp-Sample-main\src* klasörüne gidin. Bağımlı düğüm paketlerini yüklemek için aşağıdaki komutu çalıştırın:

    ```cmd
    npm install
    ```

Sonra, Azure portal işlev uygulamanızda izinleri ayarlayın:
1. Azure portal [işlev uygulamaları](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) sayfasında, işlev uygulaması örneğinizi seçin.

1. Örnek menüsünde aşağı kaydırın ve *CORS*' yi seçin. CORS sayfasında, `http://localhost:3000` boş kutuya girerek izin verilen bir kaynak olarak ekleyin. *Erişim-denetim-izin-kimlik bilgilerini etkinleştir* ve *Kaydet* için kutuyu işaretleyin.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Azure Işlevinde CORS ayarı":::

### <a name="run-the-device-simulator"></a>Cihaz simülatörünü Çalıştır

Uçtan uca öğretici ön eki sırasında, [cihaz simülatörünü](tutorial-end-to-end.md#configure-and-run-the-simulation) bir IoT Hub ve Azure dijital TWINS örneğiniz aracılığıyla veri gönderecek şekilde yapılandırdınız.

Şimdi yapmanız gerekir, *dijital-TWINS-örnekler-master > devicesimülatör > devicesimülatör. sln*' de bulunan simülatör projesini başlatmıştır. Visual Studio kullanıyorsanız, projeyi açabilir ve araç çubuğunda Bu düğmeyle çalıştırabilirsiniz:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="Visual Studio Başlangıç düğmesi (Devicesimülatör Projesi)":::

Bir konsol penceresi açılır ve sanal sıcaklık telemetri iletilerini görüntüler. Bunlar Azure Digital Twins örneğiniz aracılığıyla gönderilmekte ve burada Azure işlevleri ve SignalR tarafından alınırlar.

Bu konsolda başka bir şey yapmanız gerekmez, ancak bir sonraki adımı tamamladığınızda uygulamayı çalışır durumda bırakın.

### <a name="see-the-results"></a>Sonuçları görme

Sonuçları eylem ' e görmek için, **SignalR tümleştirmesi Web uygulaması örneğini** başlatın. Bunu, *digitaltwins-SignalR-WebApp-Sample-main\src* konumundaki herhangi bir konsol penceresinden, şu komutu çalıştırarak yapabilirsiniz:

```cmd
npm start
```

Bu işlem, bir görsel sıcaklık ölçer görüntüleyen örnek uygulamayı çalıştıran bir tarayıcı penceresi açar. Uygulama çalışmaya başladıktan sonra, Web uygulamasına gerçek zamanlı olarak yansıtılmakta olan Azure dijital TWINS aracılığıyla yayılan cihaz simülatörü 'nden sıcaklık telemetri değerlerini görmeye başlamanız gerekir.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Görsel sıcaklık ölçerin gösterildiği örnek istemci Web uygulamasından alıntı. Yansıtılan sıcaklık 67,52 ' dir":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede oluşturulan kaynaklara artık ihtiyacınız yoksa, bunları silmek için aşağıdaki adımları izleyin. 

Azure Cloud Shell veya yerel Azure CLı kullanarak, [az Group Delete](/cli/azure/group#az_group_delete) komutuyla bir kaynak grubundaki tüm Azure kaynaklarını silebilirsiniz. Kaynak grubunun kaldırılması da kaldırılır...
* Azure dijital TWINS örneği (uçtan uca öğreticiden)
* IoT Hub ve Hub cihaz kaydı (uçtan uca öğreticiden)
* olay Kılavuzu konusu ve ilişkili abonelikler
* Azure Işlevleri uygulaması, her üç işlev ve depolama gibi ilişkili kaynaklar dahil
* Azure SignalR örneği

> [!IMPORTANT]
> Silinen kaynak grupları geri alınamaz. Kaynak grubu ve içindeki tüm kaynaklar kalıcı olarak silinir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Son olarak, indirdiğiniz proje örnek klasörlerini yerel makinenize (*digital-twins-samples-master.zip*, *digitaltwins-signalr-webapp-sample-main.zip* ve bunların daraltılmış karşılıklarına) silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Digital TWINS telemetri olaylarını örnek bir istemci uygulamasına yayımlamak için Azure işlevleri 'ni SignalR ile ayarlarsınız.

Daha sonra, Azure SignalR hizmeti hakkında daha fazla bilgi edinin:
* [*Azure SignalR Hizmeti nedir?*](../azure-signalr/signalr-overview.md)

Veya Azure Işlevleri ile Azure SignalR hizmeti kimlik doğrulaması hakkında daha fazla bilgi edinin:
* [*Azure SignalR hizmeti kimlik doğrulaması*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)