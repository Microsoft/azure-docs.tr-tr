---
title: Azure portal bilgi deposu (Önizleme) oluşturma
titleSuffix: Azure Cognitive Search
description: Kalıcı içerik için kullanılan bir bilgi deposu oluşturmak için veri Içeri aktarma Sihirbazı 'nı kullanın. Diğer uygulamalardan analizler için bir bilgi deposuna bağlanın veya yukarı akış işlemlerine zenginleştirilmiş içerik gönderin. Bu özellik şu anda genel önizleme aşamasındadır.
author: lisaleib
manager: nitinme
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 56053a942604a6f1cb935e6bddcae85ffc5a9e54
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720116"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Hızlı başlangıç: Azure portal Azure Bilişsel Arama bilgi deposu oluşturma

> [!IMPORTANT] 
> bilgi deposu Şu anda genel önizleme aşamasındadır. Önizleme işlevselliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Bilgi deposu, sonraki analizler veya aşağı akış işleme için bilişsel yetenekler ardışık düzeninde çıktıyı sürekli olarak sürdüren Azure Bilişsel Arama özelliğidir. 

İşlem hattı görüntüleri ve yapılandırılmamış metinleri ham içerik olarak kabul eder, bilişsel hizmetler (görüntü ve doğal dil işleme) aracılığıyla AI uygular ve çıkış olarak zenginleştirilmiş içerik (yeni yapılar ve bilgiler) oluşturur. Bir işlem hattı tarafından oluşturulan fiziksel yapıtlardan biri, içerik çözümlemek ve araştırmak için Araçlar aracılığıyla erişebileceğiniz bir [bilgi deposudur](knowledge-store-concept-intro.md).

Bu hızlı başlangıçta, bir bilgi deposu oluşturmak için Azure bulutundaki Hizmetleri ve verileri birleştirebilirsiniz. Her şey oluşturulduktan sonra, portalda **verileri Içeri aktarma** Sihirbazı 'nı çalıştırarak tümünü bir araya getirin. Nihai sonuç orijinaldir ve portalda görüntüleyebileceğiniz AI tarafından oluşturulan içeriktir ([Depolama Gezgini](knowledge-store-view-storage-explorer.md)).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-services-and-load-data"></a>Hizmet oluşturma ve veri yükleme

Bu hızlı başlangıç, AI için Azure Bilişsel Arama, Azure Blob depolama ve Azure bilişsel [Hizmetler](https://azure.microsoft.com/services/cognitive-services/) 'i kullanır. 

İş yükü çok küçük olduğu için bilişsel hizmetler, Azure Bilişsel Arama çağrıldığında günlük olarak 20 ' ye kadar işlem için ücretsiz işleme sağlamak üzere arka planda dokunmakta. Sağladığımız örnek verileri kullandığınız sürece bilişsel hizmetler kaynağı oluşturma veya ekleme işlemini atlayabilirsiniz.

1. [HotelReviews_Free. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D)' i indirin. Bu veriler, bir CSV dosyasına kaydedilmiş (Kaggle.com kaynaklı) Otel gözden geçirme verileri ve tek bir otel hakkında yaklaşık 19 müşteri geri bildirimi içerir. 

1. Geçerli aboneliğinizde [bir Azure depolama hesabı oluşturun](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) veya [mevcut bir hesabı bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) . Azure Storage 'ı içeri aktarılacak ham içerik ve son sonuç olan bilgi deposu için kullanacaksınız.

   Bu hesap için iki gereksinim vardır:

   + Azure Bilişsel Arama ile aynı bölgeyi seçin. 
   
   + StorageV2 (genel amaçlı v2) hesap türünü seçin. 

1. Blob Hizmetleri sayfalarını açın ve bir kapsayıcı oluşturun.  

1. **Karşıya Yükle**'ye tıklayın.

    ![Verileri karşıya yükleme](media/knowledge-store-create-portal/upload-command-bar.png "Otel incelemelerini karşıya yükleyin")

1. İlk adımda indirdiğiniz **HotelReviews-Free. csv** dosyasını seçin.

    ![Azure Blob kapsayıcısını oluşturma](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Azure Blob kapsayıcısını oluşturma")

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. Get a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

1. Aynı abonelikte [bir Azure bilişsel arama hizmeti oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu hızlı başlangıç için ücretsiz bir hizmet kullanabilirsiniz.

Artık veri alma Sihirbazı 'na taşımaya hazırsınız.

## <a name="run-the-import-data-wizard"></a>Veri alma Sihirbazı 'nı çalıştırma

Dört adımda bilgi deposu oluşturmak için, arama hizmeti genel bakış sayfasında, komut çubuğunda **verileri Içeri aktar** ' a tıklayın.

  ![Verileri içeri aktar komutu](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>1\. Adım: Veri kaynağı oluşturma

1. **Verilerinize bağlanın**bölümünde **Azure Blob depolama**' yı seçin, oluşturduğunuz hesabı ve kapsayıcıyı seçin. 
1. **Ad**için `hotel-reviews-ds`girin.
1. **Ayrıştırma modu**Için, **sınırlandırılmış metin**' i seçin ve ardından **üstbilgi içerir** onay kutusunu seçin. **Sınırlayıcı karakterin** virgül (,) olduğundan emin olun.
1. Önceki bir adımda kaydettiğiniz depolama hizmeti **Bağlantı dizenizi** girin.
1. **Kapsayıcı adı**için `hotel-reviews`girin.
1. Ileri ' ye tıklayın **: AI zenginleştirme ekleyin (Isteğe bağlı)** .

      ![Veri kaynağı nesnesi oluşturma](media/knowledge-store-create-portal/hotel-reviews-ds.png "Veri kaynağı nesnesi oluşturma")

1. Sonraki sayfaya devam edin.

### <a name="step-2-add-cognitive-skills"></a>2\. Adım: Bilişsel yetenekler ekleme

Bu sihirbaz adımında Bilişsel Beceri bilişsel becerileri ile bir beceri oluşturacaksınız. Bu örnekte kullandığımız yetenekler, anahtar tümceleri ayıklar ve dili ve yaklaşımı tespit eder. Sonraki bir adımda bu zenginler, Azure tabloları olarak bir bilgi deposunda "yansıtılmaktadır" olacaktır.

1. Bilişsel **Hizmetler Ekle**' yi genişletin. **Ücretsiz (sınırlı enzenginler)** varsayılan olarak seçilidir. Bu kaynağı, HotelReviews-Free. csv içindeki kayıt sayısı 19 ve bu ücretsiz kaynak günde en fazla 20 işlem sağladığından kullanabilirsiniz.
1. Bilişsel **yetenekler ekleme**' yi genişletin.
1. **Beceri adı**için `hotel-reviews-ss`girin.
1. **Kaynak veri alanı**için **reviews_text**öğesini seçin.
1. **Zenginleştirme ayrıntı düzeyi**için **sayfalar (5000 karakter öbekleri)** seçeneğini belirleyin
1. Bu bilişsel becerileri seçin:
    + **Anahtar ifadeleri ayıklama**
    + **Dili algıla**
    + **Yaklaşımı Algıla**

      ![Beceri oluşturma](media/knowledge-store-create-portal/hotel-reviews-ss.png "Beceri kümesi oluşturma")

1. **Zenginleştirme bilgilerini bilgi deposuna kaydet**' i genişletin.
1. Önceki bir adımda kaydettiğiniz **depolama hesabı bağlantı dizesini** girin.
1. Şu **Azure Tablo projeksiyonlarını**seçin:
    + **Belgelerini**
    + **Sayfalar**
    + **Anahtar tümceleri**

    ![Bilgi deposunu yapılandırma](media/knowledge-store-create-portal/hotel-reviews-ks.png "Bilgi deposunu yapılandırma")

1. Sonraki sayfaya devam edin.

### <a name="step-3-configure-the-index"></a>3\. Adım: Dizini yapılandırma

Bu sihirbaz adımında isteğe bağlı tam metin arama sorguları için bir dizin yapılandıracaksınız. Sihirbaz, alanları ve veri türlerini çıkarması için veri kaynağınızı örnekedecektir. Yalnızca istediğiniz davranışın özniteliklerini seçmeniz gerekir. Örneğin, **alınabilir** özniteliği arama hizmetinin bir alan değeri döndürmesini sağlar, ancak **aranabilir** bir alanda tam metin aramasını etkinleştirir.

1. **Dizin adı**için `hotel-reviews-idx`girin.
1. Öznitelikler için şu seçimleri yapın:
    + Tüm alanlar için **alınabilir** ' i seçin.
    + Şu alanlar için **filtrelenebilir** ve çok **yönlü tablo** seçin *: yaklaşım*, *dil*, *Keyphrases*
    + Şu alanlar için **aranabilir** öğesini seçin: *şehir*, *ad*, *reviews_text*, *dil*, *Keyphrases*

    Dizininiz aşağıdaki görüntüye benzer görünmelidir. Liste uzun olduğundan, tüm alanlar görüntüde görünür değildir.

    ![Dizin yapılandırma](media/knowledge-store-create-portal/hotel-reviews-idx.png "Dizin yapılandırma")

1. Sonraki sayfaya devam edin.

### <a name="step-4-configure-the-indexer"></a>4\. Adım: Dizin oluşturucuyu yapılandırma

Bu sihirbaz adımında, veri kaynağını, beceri ve önceki sihirbaz adımlarında tanımladığınız dizini birlikte alacak bir Dizin Oluşturucu yapılandıracaksınız.

1. **Ad**için `hotel-reviews-idxr`girin.
1. **Zamanlama**Için varsayılanı **bir kez**saklayın.
1. Dizin oluşturucuyu çalıştırmak için **Gönder** ' e tıklayın. Veri ayıklama, dizin oluşturma, bilişsel yeteneklerin uygulaması bu adımda gerçekleşir.

## <a name="monitor-status"></a>İzleme durumu

Bilişsel Beceri dizin oluşturma, tipik metin tabanlı dizin oluşturma özelliğinden daha uzun sürer. İlerlemeyi izleyebilmek için sihirbazın Genel Bakış sayfasında Dizin Oluşturucu listesini açması gerekir. Kendi kendine gezinme için genel bakış sayfasına gidin ve **Dizin oluşturucular**' ye tıklayın.

Azure portal, tıklatılabilir bir **Azure bilişsel arama bildirim** durumu bağlantısı için bildirimler etkinlik günlüğünü de izleyebilirsiniz. Yürütmenin tamamlanması birkaç dakika sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bilişsel hizmetler 'i kullanarak verilerinizi zenginleştirdikten ve sonuçları bir bilgi deposuna yansıdığınıza göre, zenginleştirilmiş veri kümesini araştırmak için Depolama Gezgini veya Power BI kullanabilirsiniz.

Depolama Gezgini içeriği görüntüleyebilir veya görselleştirme ile ilgili Öngörüler elde etmek için Power BI bir adım adım izleyebilirsiniz.

> [!div class="nextstepaction"]
> [Power BI ile](knowledge-store-connect-power-bi.md)
> [Depolama Gezgini ile görüntüleme](knowledge-store-view-storage-explorer.md)

> [!Tip]
> Bu alıştırmayı yinelemek veya farklı bir AI zenginleştirme Kılavuzu denemek istiyorsanız, *otel-incelemeler-ıdxr* Dizin oluşturucuyu silin. Dizin oluşturucunun silinmesi, bilişsel hizmetler işleme için ücretsiz günlük işlem sayacını yeniden sıfır olarak sıfırlar.
