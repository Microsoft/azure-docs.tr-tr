---
title: Mantıksal uygulama ve güç otomatikleştirme öğreticisiyle Video Indexer bağlayıcılar.
description: Bu öğreticide, mantıksal uygulama ve güç otomatikleştirme ile bağlayıcılar Video Indexer yeni deneyimlerin ve paraya getirme fırsatlarının nasıl kilidinin yapılacağı gösterilmektedir.
author: anzaman
manager: johndeu
ms.author: alzam
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: caff6a2496e907da1bdc140860c47476d1842df4
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600684"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>Öğretici: mantıksal uygulama ve güç otomatikleştirme ile Video Indexer kullanma

Azure Media Services [video Indexer v2 REST API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Video) hem sunucudan sunucuya hem de istemciden sunucuya iletişimi destekler ve video Indexer kullanıcıların video ve ses öngörülerini kolayca uygulama mantığına tümleştirmesini sağlar.

Tümleştirmeyi daha da kolay hale getirmek için, [](https://azure.microsoft.com/services/logic-apps/)   API 'imizde uyumlu olan Logic Apps ve [Güç otomatikleştirme](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/)   bağlayıcılarını destekliyoruz. Tek bir kod satırı yazmadan büyük miktarda video ve ses dosyasından öngörüleri etkin bir şekilde indekslemek ve ayıklamak üzere özel iş akışları ayarlamak için bağlayıcıları kullanabilirsiniz. Ayrıca, tümleştirmenize yönelik bağlayıcılar kullanılması, iş akışınızın sistem durumu ve hata ayıklamanın kolay bir yolu hakkında daha iyi görünürlük sağlar.  

Video Indexer bağlayıcılarıyla hızlıca çalışmaya başlamanıza yardımcı olması için, kullanabileceğiniz örnek bir mantıksal uygulama ve Power otomatikleştirmede izlenecek yol göstereceğiz. Bu öğreticide, Logic Apps kullanarak akışların nasıl ayarlanacağı gösterilmektedir. Ancak, düzenleyiciler ve yetenekler her iki çözümde da neredeyse aynıdır, bu nedenle diyagramlar ve açıklamalar hem Logic Apps hem de güç otomatikleştirme için geçerlidir.

Bu öğreticide ele alınan "videonuzu otomatik olarak karşıya yükle ve dizine yükle" senaryosu birlikte çalışan iki farklı akıştan oluşur. 
* Azure depolama hesabında bir blob eklendiğinde veya değiştirildiğinde ilk akış tetiklenir. Dizin oluşturma işlemi tamamlandıktan sonra bir bildirim göndermek için geri çağırma URL 'SI ile Video Indexer yeni dosyayı karşıya yükler. 
* İkinci akış geri çağırma URL 'SI temel alınarak tetiklenir ve ayıklanan Öngörüler Azure Storage 'daki bir JSON dosyasına geri kaydedilir. Bu iki Flow yaklaşımı, zaman uyumsuz karşıya yükleme ve daha büyük dosyaların dizinlenmesini desteklemek için kullanılır. 

Bu öğretici, nasıl yapılacağını göstermek için mantıksal uygulama kullanıyor.

> [!div class="checklist"]
> * Karşıya dosya yükleme akışını ayarlama
> * JSON ayıklama akışını ayarlama

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* İle başlamak için [API anahtarı aracılığıyla API 'lere erişim](video-indexer-use-apis.md)ile birlikte video Indexer bir hesaba ihtiyacınız olacaktır. 
* Ayrıca, bir Azure depolama hesabınızın olması gerekir. Depolama hesabınızın erişim anahtarını aklınızda bulundurun. İçinde Video Indexer tarafından oluşturulan öngörüleri depolamak için bir tane olmak üzere iki kapsayıcı oluşturun.  
* Sonra, Logic Apps veya güç otomatikleştirmede (kullandığınız bağlı olarak) iki ayrı akış açmanız gerekir. 

## <a name="set-up-the-first-flow---file-upload"></a>İlk akış dosyası karşıya yüklemeyi ayarlama   

Azure depolama kapsayıcıınızda her bir blob eklendiğinde ilk akış tetiklenir. Tetiklendikten sonra, Video Indexer videoyu karşıya yüklemek ve dizinlemek için kullanabileceğiniz bir SAS URI 'SI oluşturur. Bu bölümde, aşağıdaki akışı oluşturacaksınız. 

![Karşıya dosya yükleme akışı](./media/logic-apps-connector-tutorial/file-upload-flow.png)

İlk akışı ayarlamak için Video Indexer API anahtarınızı ve Azure Storage kimlik bilgilerinizi sağlamanız gerekir. 

![Azure blob depolama](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Bağlantı adı ve API anahtarı](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

> [!TIP]
> Daha önce bir Azure depolama hesabı veya Video Indexer hesabını bir mantıksal uygulamaya bağladıysanız, bağlantı ayrıntılarınız depolanır ve otomatik olarak bağlanırsınız. <br/>Azure depolama (depolama penceresi) veya Video Indexer (oynatıcı penceresi) eylemi altındaki **Bağlantıyı Değiştir** ' i tıklatarak bağlantıyı düzenleyebilirsiniz.

Azure depolama 'ya ve Video Indexer hesaplarına bağlanıp **Logic Apps tasarımcısında**"bir blob eklendiğinde veya değiştirildiğinde" tetikleyicisi ' ni bulun ve seçin.

Video dosyalarınızı yerleştireceğiniz kapsayıcıyı seçin. 

![Ekran görüntüsü bir blob eklendiğinde veya değiştirildiğinde bir kapsayıcı seçebileceğiniz bir iletişim kutusu gösterir.](./media/logic-apps-connector-tutorial/container.png)

Sonra, "yola göre SAS URI 'SI oluştur" eylemini bulup seçin. Eylem için iletişim kutusunda dinamik içerik seçeneklerinden dosya yolu listesi ' ni seçin.  

Ayrıca, yeni bir "paylaşılan erişim Protokolü" parametresi ekleyin. Parametrenin değeri için HttpsOnly öğesini seçin.

![Yola göre SAS URI 'si](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Video Indexer hesap belirtecini almak için [Hesap konumunuzu](regions.md) ve [Hesap kimliğinizi](./video-indexer-use-apis.md#account-id)doldurun   .

![Hesap erişim belirtecini al](./media/logic-apps-connector-tutorial/account-access-token.png)

"Video ve dizini karşıya yükle" için gerekli parametreleri ve video URL 'sini doldurun. "Yeni parametre Ekle" seçeneğini belirleyin ve geri çağırma URL 'sini seçin. 

![Karşıya yükleme ve Dizin](./media/logic-apps-connector-tutorial/upload-and-index.png)

Geri çağırma URL 'sini şimdilik boş bırakacaksınız. Bunu yalnızca geri çağırma URL 'sinin oluşturulduğu ikinci akışı tamamladıktan sonra eklersiniz. 

Diğer parametreler için varsayılan değeri kullanabilir veya gereksinimlerinize göre ayarlayabilirsiniz. 

**Kaydet**' e tıklayın ve ikinci akışı yapılandırmak için, karşıya yükleme ve dizin oluşturma işlemi tamamlandıktan sonra öngörüleri ayıklamak üzere ilerlim. 

## <a name="set-up-the-second-flow---json-extraction"></a>İkinci akışı ayarlama-JSON ayıklama  

İlk akıştan karşıya yükleme ve dizin oluşturma işleminin tamamlanması, ikinci akışı tetiklemek için doğru geri çağırma URL 'sine sahip bir HTTP isteği gönderir. Daha sonra, Video Indexer tarafından oluşturulan öngörüleri alır. Bu örnekte, dizin oluşturma işinizin çıkışını Azure Storage 'da depolayacaktır.  Bununla birlikte, çıktıyla yapabilecekleriniz size kadar sürer.  

İkinci akışı birinciden ayrı oluşturun. 

![JSON ayıklama akışı](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

Bu akışı ayarlamak için, Video Indexer API anahtarınızı ve Azure depolama kimlik bilgilerinizi yeniden sağlamanız gerekir. Aynı parametreleri ilk akışta yaptığınız gibi güncelleştirmeniz gerekir. 

Tetikleyiciniz için bir HTTP POST URL 'SI alanı görürsünüz. Akışınızı kaydettikten sonra URL oluşturulmaz; Bununla birlikte, URL sonunda gerekecektir. Buna geri dönecektir. 

Video Indexer hesap belirtecini almak için [Hesap konumunuzu](regions.md) ve [Hesap kimliğinizi](./video-indexer-use-apis.md#account-id)doldurun   .  

"Video dizinini al" eylemine gidin ve gerekli parametreleri doldurun. Video KIMLIĞI için şu ifadeyi koyun: Triggerçıktılar () [' sorgular '] [' ID '] 

![video Dizin Oluşturucu eylem bilgisi](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

Bu ifade, Bağlayıcısı 'ın, tetikleyicinizin çıktısından video kimliğini almasını söyler. Bu durumda, tetikleyicinizin çıktısı, ilk tetikleyicinizdeki "video ve dizin yükleme" çıktısı olacaktır. 

"Blob oluştur" eylemine gidin ve içgörüleri kaydedeceğiniz klasörün yolunu seçin. Oluşturmakta olduğunuz Blobun adını ayarlayın. Blob içeriği için şu ifadeyi koyun: Body (' Get_Video_Index ') 

![Blob oluşturma eylemi](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

Bu ifade, bu akıştan "video dizini Al" eyleminin çıkışını alır. 

**Akışı kaydet**' e tıklayın. 

Akış kaydedildikten sonra, tetikleyicide bir HTTP POST URL 'SI oluşturulur. Tetikleyiciden URL 'YI kopyalayın. 

![URL tetikleyicisini Kaydet](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Şimdi ilk akışa dönün ve geri çağırma URL 'SI parametresi için "video ve dizini karşıya yükle" eyleminde URL 'YI yapıştırın. 

Her iki akışın de kaydedildiğinden emin olun ve hazırsınız! 

Azure Blob kapsayıcılarınıza bir video ekleyerek yeni oluşturulan mantıksal uygulamanızı veya Power otomatikleştir çözümünüzü deneyin ve daha sonra öngörülerin hedef klasörde göründüğünü görmek için birkaç dakika sonra geri dönün. 

## <a name="generate-captions"></a>Açıklamalı altyazı oluştur

[Video Indexer ve Logic Apps ile açıklamalı altyazı oluşturmayı](https://techcommunity.microsoft.com/t5/azure-media-services/generating-captions-with-video-indexer-and-logic-apps/ba-p/1672198)gösteren adımlar için aşağıdaki bloga bakın. 

Makalede ayrıca bir videonun OneDrive 'a kopyalanarak ve OneDrive 'da Video Indexer tarafından oluşturulan açıklamalı alt yazıların nasıl depolandığı gösterilmektedir.
 
## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu Öğreticiyi tamamladıktan sonra, ihtiyacınız olursa bu mantıksal uygulamayı veya güç otomatikleştirme çözümünü çalışır durumda tutun. Ancak, bu çalışmayı sürdürmek istemiyorsanız ve faturalandırılmaya devam etmek istemiyorsanız, güç otomatikleştir kullanıyorsanız akışlarınızın her ikisini de kapatın. Logic Apps kullanıyorsanız her iki akışı da devre dışı bırakın. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğretici yalnızca bir Video Indexer bağlayıcılar örneği olduğunu gösterdi. Video Indexer tarafından sunulan herhangi bir API çağrısı için Video Indexer bağlayıcılarını kullanabilirsiniz. Örneğin: öngörüleri yükleyin ve alın, sonuçları çevirin, eklenebilir pencere öğeleri edinin ve hatta modellerinizi özelleştirin. Ayrıca, bu eylemleri dosya depolarında veya gönderilen e-postalardaki güncelleştirmeler gibi farklı kaynaklara göre de tetiklemeyi seçebilirsiniz. Daha sonra sonuçların ilgili altyapımız veya uygulamamızda güncelleştirilmesini veya istediğiniz sayıda eylem öğesini oluşturmayı seçebilirsiniz.  

> [!div class="nextstepaction"]
> [Video Indexer API'sini kullanma](video-indexer-use-apis.md)

Ek kaynaklar için [video dizin oluşturucuda](/connectors/videoindexer-v2/) bu belgeye başvurun.
