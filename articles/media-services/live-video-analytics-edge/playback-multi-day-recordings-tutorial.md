---
title: Çok günlük kayıtların kayıttan yürütülmesi-Azure
description: Bu öğreticide, Azure Media Service API 'Lerini kullanarak çok günlü bir sürekli video kaydını kayıttan yürütmeyi öğreneceksiniz.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 81a778b40649c1318b3738a289f0db37fd35376a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492797"
---
# <a name="tutorial-playback-of-multi-day-recordings"></a>Öğretici: çok günlük kayıtları kayıttan yürütme  

Bu öğretici, [sürekli video kaydı](continuous-video-recording-concept.md) (CVR) öğreticisini oluşturur. Bu öğreticide, Azure Media Service API 'Lerini kullanarak buluttan çok günlük sürekli bir video kaydını kayıttan yürütmeyi öğreneceksiniz. 

Bu, genel güvenlik gibi senaryolarda, bir kameradan birden çok gün (veya hafta) için bir film kaydını sürdürme ve bu çekimin belirli kısımlarını izlemek için bir arada olması gereken bir sorun olduğu durumlarda kullanışlıdır.

Öğretici şunların nasıl yapıldığını göstermektedir:

> [!div class="checklist"]
> * Çok günlük bir kaydın içeriğine nasıl gözatacağınızı gösteren örnek uygulamayı çalıştırın
> * Bu kaydın seçili kısımlarını görüntüle

## <a name="suggested-pre-reading"></a>Önerilen önceden okuma  

Aşağıdaki belge sayfalarını okumanız önerilir:

* [IoT Edge genel bakış üzerinde canlı video analizi](overview.md)
* [IoT Edge terminolojisinde canlı video analizi](terminology.md)
* [Medya grafiği kavramı](media-graph-concept.md)
* [Sürekli video kaydı](continuous-video-recording-concept.md) 
* [Nasıl yapılır Kılavuzu: kayıtları kayıttan yürütme](playback-recordings-how-to.md)
* [Öğretici: sürekli video kaydı](continuous-video-recording-tutorial.md)

## <a name="prerequisites"></a>Önkoşullar

* [CVR öğreticisini](continuous-video-recording-tutorial.md)doldurun. Bu öğretici ve öğreticide ele alınan ilgili API 'Ler [: sürekli video kaydı](continuous-video-recording-tutorial.md) , 5 dakika veya daha uzun olan kayıtlar için geçerlidir, daha fazla değilse video 5 saatlik bir değer kaydetmeniz önerilir. Kayıtlara gözatmek için kullanılan API 'Ler, uzun kayıtlarla en iyi şekilde gösterilmiştir.
* Bu [öğreticiyi öğreticide çalıştırmanızı öneririz: sürekli video kaydı](continuous-video-recording-tutorial.md) çalışmaya devam ediyor; diğer bir deyişle, hala videoyu buluta kaydediyorsanız.

## <a name="run-the-sample"></a>Örneği çalıştırma 

[CVR öğreticisinin](continuous-video-recording-tutorial.md)bir parçası olarak bir medya hizmeti hesabı oluşturmuş olursunuz. Bu öğreticide, bu hesaba yönelik tüm API erişiminizin olması gerekir. Hizmet sorumlusu oluşturmak için [MEDIA SERVICES API 'sine erişmek için kimlik bilgilerini al](../latest/access-api-howto.md?tabs=portal) ' daki adımları kullanabilirsiniz. Aşağıdaki gibi görünen Azure portal bir JSON bloğu edinebilirsiniz:

```
{
    "AadClientId": "<<INSERT_AZURE_AD_APP_ID_HERE>>",
    "AadSecret": "<<INSERT_AZURE_AD_APP_SECRET_HERE>>",
    "AadTenantDomain": "<<YOUR_TENANT_DOMAIN>>",
    "AadTenantId": "<<YOUR_TENANT_ID>>",
    "AccountName": "<<YOUR_ACCOUNT_NAME>>",
    "Location": "<<YOUR_REGION_NAME>>",
    "ResourceGroup": "<<YOUR_RESOURCE_GROUP>>",
    "SubscriptionId": "<<YOUR_SUBSCRIPTION_ID>>",
    "ArmAadAudience": "https://management.core.windows.net",
    "ArmEndpoint": "https://management.azure.com"
}
```

Ardından, Visual Studio Code 'da src/AMS-varlık-Player ' ı açın. Bu klasör, bu öğretici için gerekli dosyaları içerir. appsettings.jsdosya üzerinde açın ve içeriğini yeni bir dosyaya kopyalayın, appsettings.development.js. Üzerinde yeni oluşturulan appsettings.development.jsaşağıdaki düzenlemeleri yapın:

```
  "AMS" : {
    "subscriptionId" : "Use value of SubscriptionId above",
    "resourceGroup" : "Use value of ResourceGroup above",
    "accountId" : "Use value of AccountName above",
    "aadTenantId" : "Use value of AadTenantId above",
    "aadClientId" : "Use value of AadClientId above",
    "aadSecret" : "Use value of AadSecret above"
} 
```

1. Visual Studio Code, **Uzantılar** sekmesini açın (veya CTRL + SHIFT + X tuşlarına basın) ve Azure IoT Hub aratın.
1. Sağ tıklayıp **uzantı ayarları**' nı seçin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Uzantı ayarları":::
1. "Ayrıntılı Iletiyi göster" i arayın ve etkinleştirin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Ayrıntılı Iletiyi göster":::
1. <!--In Visual Studio Code, you can click-->Çalıştırılacak kullanılabilir uygulamaları görüntülemek için sol taraftaki Çalıştır simgesine (veya CTRL + SHIFT + D) tıklayın:

    ![Ekran görüntüsü, seçili Çalıştır öğesiyle birlikte Visual Studio Code bir menü gösterir.](./media/playback-multi-day-recordings-tutorial/run.png)
1. Aşağıda gösterildiği gibi açılan kutudan AMS varlık oynatıcı uygulamasını seçin ve hata ayıklamaya başlamak için F5 'e basın.

    ![Ekran görüntüsü, AMS varlık yürütücüsü seçiliyken Visual Studio Code bir menü gösterir.](./media/playback-multi-day-recordings-tutorial/debug.png)

Örnek uygulama, varsayılan tarayıcı uygulamanızı derleyip başlatacaktır ve AMS varlık oynatıcı sayfasını açar.

> [!NOTE]
> Tarayıcınızdaki güvenlik ayarlarına bağlı olarak, bir uyarı iletisi görebilirsiniz. Web sayfası yerel olarak çalıştığından, uyarıyı yok saymayı seçebilirsiniz.

AMS varlık yürütücüsü, bir medya hizmeti varlığının adını girmenizi ister. Öğreticide video kaydetmek için kullandığınız varlığın adını kullanmanız gerekir [: sürekli video kaydı](continuous-video-recording-tutorial.md).

Varlık adını yazdıktan ve Gönder ' i gönderdikten sonra, oynatıcı kodu akış URL 'sini yükler. Daha fazla bilgi için bkz. [nasıl yapılır Kılavuzu: kayıtları kayıttan yürütme](playback-recordings-how-to.md). Bu durumda, hala varlığına kayıt yapıyorsanız, Player bunu algılar ve kaydedilen videonun en son bölümüne kayıttan yürütmeyi işaret etmeye çalışır. Oynatıcının sol üst kısmındaki zaman damgasını (UTC olarak) görebilirsiniz. Aşağıdaki ekran görüntüsünde, "canlı" düğmesinin nasıl seçili olduğunu aklınızda bulabilirsiniz.

![Akış](./media/playback-multi-day-recordings-tutorial/assetplayer1.png)
 
Player 'ın sağ tarafında, arşive gözatabilmeniz için denetimleri görebilirsiniz. Bu denetimdeki yıl, ay ve tarihler, [nasıl yapılır Kılavuzu: kayıtları kayıttan yürütme](playback-recordings-how-to.md)bölümünde belgelenen kullanılabilirlik Blemeçya API 'si kullanılarak doldurulur.
Günü genişlettiğinizde, CVR öğreticisinin birkaç saat boyunca çalışmasına izin verirseniz şöyle bir sonuç görürsünüz:

![Arşive gözatamıyorum](./media/playback-multi-day-recordings-tutorial/results.png)

Öğreticideki video akışı kaynağı bir MKV dosyasıdır. RSTP Simülatörü (bkz. [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) dosyanın sonuna ulaştığında akışı sonlandırır. Medya grafiğindeki RTSP kaynak düğümü bunu algılar ve bağlantıyı yeniden oluşturur ve video sürdürülür. Her bir dosya sonu ve yeniden bağlanma arasında, kayıtlı arşivde bir boşluk vardır ve bu, kullanılabilirlik Blemeçya sonuçlarında yeni bir giriş olarak gösterilir.

![Sonuçlar](./media/playback-multi-day-recordings-tutorial/assetplayer2.png)
 
Listede herhangi bir girişe tıkladığınızda, uygulama https://{hostname}/{Locatorıd}/Content. ISM/manifest (format = MPD-Time-CSF, startTime = YYYY-MM-DDTHH: MM: SS) gibi uygun filtreye sahip bir akış URL 'SI oluşturur. Oynatıcı bu URL 'YI yükler ve kayıttan yürütme, istenen startTime 'e göre değişir.

Alternatif olarak, sayfanın alt kısmındaki denetimler aracılığıyla belirli başlangıç ve bitiş zamanlarını kullanabilirsiniz. Kullanılabilirlik, başlangıç ve bitiş sürelerinin izin verilen değerlerine ilişkin kılavuz olarak, sağ tarafta listelendiği gibi, kullanılabilirliği Blemeçya çağrısının sonuçlarını kullanabilirsiniz. StartTime ve bitişsaati filtrelerinde ayrıntılı kısıtlamalar, [nasıl yapılır Kılavuzu: kayıtların kayıttan yürütülmesi](playback-recordings-how-to.md)bölümünde belgelenmiştir. Zaman değerlerini seçtikten sonra, Gönder ' i tıkladıktan sonra uygulama, Player 'ı şu şekilde bir akış URL 'SI ile yükler: https://{hostname}/{Locatorıd}/Content. ISM/bildirimi (format = MPD-Time-CSF, startTime = YYYY-MM-DDTHH: MM: SS, bitişsaati = YYYY-MM-DDTHH: MM: SS) oynatma, istenen startTime 'e göre değişir.

## <a name="next-steps"></a>Sonraki adımlar

[Bulutta buluta ve kayıttan yürütmeye yönelik olay tabanlı video kaydı](event-based-video-recording-tutorial.md)
