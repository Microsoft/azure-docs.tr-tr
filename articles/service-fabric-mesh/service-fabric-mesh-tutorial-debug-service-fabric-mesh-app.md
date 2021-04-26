---
title: Yerel olarak çalışan bir Azure Service Fabric ağı Web uygulamasında hata ayıklama
description: Bu öğreticide, yerel kümenizde çalışan bir Azure Service Fabric Mesh uygulamasında hata ayıklaması yaparsınız.
author: georgewallace
ms.topic: tutorial
ms.date: 10/31/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 9e3dc16481340c0266cd398d0970e2147648e17f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625457"
---
# <a name="tutorial-debug-a-service-fabric-mesh-application-running-in-your-local-development-cluster"></a>Öğretici: Yerel geliştirme kümenizde çalışan bir Service Fabric Mesh uygulamasının hatalarını ayıklama

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

Bir serinin ikinci kısmı olan bu öğreticide, yerel geliştirme kümenizdeki bir Azure Service Fabric Mesh uygulamasında derleme ve hata ayıklama işleminin nasıl yapıldığı gösterilir.

Bu öğreticide şunları öğreneceksiniz:

> [!div class="checklist"]
> * Azure Service Fabric Mesh uygulaması derlediğinizde ne olur?
> * Hizmetten hizmete çağrıyı gözlemlemek için kesme noktası nasıl ayarlanır

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * [Visual Studio’da Service Fabric Mesh uygulaması oluşturma](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Yerel geliştirme kümenizde çalışan bir Service Fabric Mesh uygulamasının hatalarını ayıklama
> * [Service Fabric Mesh uygulaması dağıtma](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Service Fabric Mesh uygulamasını yükseltme](service-fabric-mesh-tutorial-upgrade.md)
> * [Service Fabric Mesh kaynaklarını temizleme](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturabilirsiniz](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

* [Geliştirme ortamınızı ayarladığınızdan](service-fabric-mesh-howto-setup-developer-environment-sdk.md) ve Service Fabric çalışma zamanı, SDK, Docker ve Visual Studio 2017'yi yüklediğinizden emin olun.

## <a name="download-the-to-do-sample-application"></a>Yapılacaklar örnek uygulamasını indirme

[Bu öğretici serisinin birinci kısmında](service-fabric-mesh-tutorial-create-dotnetcore.md)yapılacak do örnek uygulamasını oluşturmadıysanız, indirebilirsiniz. Komut penceresinde, örnek uygulama deposunu yerel makinenize kopyalamak için aşağıdaki komutu çalıştırın.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Uygulama `src\todolistapp` dizininin altındadır.

## <a name="build-and-debug-on-your-local-cluster"></a>Yerel kümenizde derleme ve hata ayıklama

Docker görüntüsü, projeniz yüklenir yüklenmez yerel kümenizde otomatik olarak derlenir ve dağıtılır. Bu işlem biraz zaman alabilir. Visual Studio'da ilerleme durumunu izlemek için, **Çıkış** bölmesinde Çıkış bölmesi **Çıkışı göster:** açılan listesini **Service Fabric Araçları**'na ayarlayın.

Hizmetinizi yerel olarak derlemek ve çalıştırmak için **F5** tuşuna basın. Proje yerel olarak çalıştırılıp hataları ayıklandığında Visual Studio şunları yapar:

* Docker for Windows’un çalıştırıldığından ve kapsayıcı işletim sistemi olarak Windows'u kullanacak şekilde ayarlandığından emin olur.
* Eksik Docker temel görüntülerini indirir. Bu bölüm biraz zaman alabilir
* Kod projenizi barındırmak için kullanılan Docker görüntüsünü derler (veya yeniden derler).
* Yerel Service Fabric geliştirme kümesinde kapsayıcıyı dağıtır ve çalıştırır.
* Hizmetlerinizi çalıştırır ve ayarladığınız tüm kesme noktalarına gelir.

Yerel dağıtım bittikten ve Visual Studio uygulamanızı çalıştırdıktan sonra, bir tarayıcı penceresinde varsayılan örnek web sayfası açılır.

## <a name="debugging-tips"></a>Hata ayıklama ipuçları

[Visual Studio performansını iyileştirme](service-fabric-mesh-howto-optimize-vs.md)bölümündeki yönergeleri izleyerek ilk hata ayıklamanın çalışmasını (F5) çok daha hızlı hale getirin.

Şu anda `using (HttpResponseMessage response = client.GetAsync("").GetAwaiter().GetResult())` hizmete bağlanma başarısız olmasına neden olan bir sorun var. Ana bilgisayarınızın IP adresi değiştiğinde bu durumda karşılaşabilirsiniz. Bunu çözmek için:

1. Uygulamayı yerel kümeden kaldırın (Visual Studio 'da,   >  **temiz çözüm** oluşturun).
2. Service Fabric Yerel Küme Yöneticisi'nde **Yerel Kümeyi Durdur**'u ve ardından **Yerel Kümeyi Başlat**'ı seçin.
3. Uygulamayı yeniden dağıtın (Visual Studio'da, **F5**).

**Service Fabric yerel kümesi çalışmıyor** hatasını alırsanız, Service Fabric Yerel Küme Yöneticisi'nin (LCM) çalıştığından emin olun, sonra görev çubuğunda LCM simgesine tıklayın ve **Yerel Kümeyi Başlat**'a tıklayın. Başlatıldıktan sonra Visual Studio’ya dönün ve **F5** tuşuna basın.

Uygulama başlatıldığında **404** hatası alırsanız, **service.yaml** içindeki ortam değişkenleriniz yanlış olabilir. `ApiHostPort` ve `ToDoServiceName` değerlerinin [Ortam değişkenlerini oluşturma](./service-fabric-mesh-tutorial-create-dotnetcore.md#create-environment-variables) yönergelerine uygun olarak doğru ayarlandığından emin olun.

**Service.yaml** içinde derleme hataları alırsanız, satırlarda girinti yapmak için sekme yerine boşlukların kullanıldığından emin olun. Ayrıca şu an için uygulamayı İngilizce yerel ayarı kullanarak derlemeniz gerekir.

### <a name="debug-in-visual-studio"></a>Visual Studio'da hata ayıklama

Visual Studio 'da bir Service Fabric kafes uygulamasında hata ayıkladığınızda, yerel bir Service Fabric geliştirme kümesi kullanıyorsunuz demektir. Arka uç hizmetinden yapılacaklar öğelerinin nasıl alındığını görmek için OnGet() yönteminde hata ayıklaması yapın.
1. **Webön uç** projesinde,   >  **Index. cshtml**  >  **index. cshtml. cs** dosyasını açın ve **OnGet** yönteminde (17. satır) bir kesme noktası ayarlayın.
2. **ToDoService** projesinde, **TodoController. cs** ' yi açın ve **Get** yönteminde (15. satır) bir kesme noktası ayarlayın.
3. Tarayıcınıza geri dönüp sayfayı yenileyin. Web ön ucu `OnGet()` yönteminde kesme noktasına ulaşırsınız. `backendUrl` değişkenini inceleyerek **service.yaml** dosyasında tanımladığınız ortam değişkenlerinin arka uç hizmetiyle bağlantı kurmak için kullanılan URL'yle nasıl birleştirildiği görebilirsiniz.
4. `client.GetAsync(backendUrl).GetAwaiter().GetResult())` çağrısından ilerleyin (F10); denetleyicinin `Get()` kesme noktasına ulaşırsınız. Bu yöntemde, yapılacaklar öğesi listesinin bellek içi listeden nasıl alındığını görebilirsiniz.
5. İşiniz bittiğinde, **SHIFT + F5** tuşlarına basarak Visual Studio 'da projenizin hatalarını ayıklamayı durdurun.

## <a name="next-steps"></a>Sonraki adımlar

Öğreticinin bu bölümünde şunları öğrendiniz:

> [!div class="checklist"]
> * Azure Service Fabric Mesh uygulaması derlediğinizde ne olur?
> * Hizmetten hizmete çağrıyı gözlemlemek için kesme noktası nasıl ayarlanır

Sonraki öğreticiye ilerleyin:
> [!div class="nextstepaction"]
> [Service Fabric Mesh uygulaması dağıtma](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
