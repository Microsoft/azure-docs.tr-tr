---
title: Azure portal bilgi deposu oluşturma
titleSuffix: Azure Cognitive Search
description: Kalıcı içerik için kullanılan bir bilgi deposu oluşturmak için veri Içeri aktarma Sihirbazı 'nı kullanın. Diğer uygulamalardan analizler için bir bilgi deposuna bağlanın veya yukarı akış işlemlerine zenginleştirilmiş içerik gönderin.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/17/2020
ms.openlocfilehash: 3225013f09abd326c619b67caf77918889a64859
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94741816"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Hızlı başlangıç: Azure portal Azure Bilişsel Arama bilgi deposu oluşturma

Bilgi deposu, sonraki analizler veya aşağı akış işleme için bir içerik işleme ardışık düzeninde çıkışı devam eden bir Azure Bilişsel Arama özelliğidir. 

İşlem hattı yapılandırılmamış metin ve görüntü içeriğini kabul eder, bilişsel hizmetler tarafından desteklenen AI uygular (OCR ve doğal dil işleme gibi) ve daha önce mevcut olmayan yeni yapıları ve bilgileri verir. Bir işlem hattı tarafından oluşturulan fiziksel yapıtlardan biri, içerik çözümlemek ve araştırmak için Araçlar aracılığıyla erişebileceğiniz bir [bilgi deposudur](knowledge-store-concept-intro.md).

Bu hızlı başlangıçta, bir bilgi deposu oluşturmak için Azure bulutundaki Hizmetleri ve verileri birleştirebilirsiniz. Her şey oluşturulduktan sonra, portalda **verileri Içeri aktarma** Sihirbazı 'nı çalıştırarak tümünü bir araya getirin. Nihai sonuç orijinal metin içeridir ve portalda görüntüleyebileceğiniz AI tarafından oluşturulan içeriktir ([Depolama Gezgini](knowledge-store-view-storage-explorer.md)).

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce aşağıdakilere sahip olmanız gerekir:

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/).

+ Bir Azure Bilişsel Arama hizmeti. Geçerli aboneliğiniz kapsamında [bir hizmet oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu hızlı başlangıç için ücretsiz bir hizmet kullanabilirsiniz. 

+ [BLOB depolama alanı](../storage/blobs/index.yml)olan bir Azure depolama hesabı.

> [!NOTE]
> Bu hızlı başlangıç Ayrıca AI için Azure bilişsel [Hizmetler](https://azure.microsoft.com/services/cognitive-services/) 'i de kullanır. İş yükü çok küçük olduğu için bilişsel hizmetler, en fazla 20 işlem için ücretsiz işleme için arka planda dokunduğunda. Bu, ek bir bilişsel hizmetler kaynağı oluşturmak zorunda kalmadan bu Alıştırmayı tamamlayabilmeniz anlamına gelir.

## <a name="set-up-your-data"></a>Verilerinizi kurma

Aşağıdaki adımlarda, heterojen içerik dosyalarını depolamak için Azure depolama 'da bir blob kapsayıcısı ayarlayın.

1. [HotelReviews_Free.csvindirin ](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Bu veriler, bir CSV dosyasına kaydedilmiş (Kaggle.com kaynaklı) Otel gözden geçirme verileri ve tek bir otel hakkında yaklaşık 19 müşteri geri bildirimi içerir. 

1. Geçerli aboneliğinizde [bir Azure depolama hesabı oluşturun](../storage/common/storage-account-create.md?tabs=azure-portal) veya [mevcut bir hesabı bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) . Azure Storage 'ı içeri aktarılacak ham içerik ve son sonuç olan bilgi deposu için kullanacaksınız.

   + **StorageV2 (genel amaçlı v2)** hesap türünü seçin.

1. Blob Hizmetleri sayfalarını açın ve *otel-incelemeler* adlı bir kapsayıcı oluşturun.

1. **Karşıya Yükle**'ye tıklayın.

    ![Verileri karşıya yükleme](media/knowledge-store-create-portal/upload-command-bar.png "Otel incelemelerini karşıya yükleyin")

1. İlk adımda indirdiğiniz **HotelReviews-Free.csv** dosyasını seçin.

    ![Azure Blob kapsayıcısını oluşturma](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Azure Blob kapsayıcısını oluşturma")

1. BLOB depolama sayfalarından çıkmadan önce, sol gezinti bölmesindeki bir bağlantıyı kullanarak **erişim tuşları** sayfasını açın. Blob depolamadan veri almak için bir bağlantı dizesi alın. Bir bağlantı dizesi aşağıdaki örneğe benzer şekilde görünür: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Artık **veri alma** Sihirbazı 'na taşımaya hazırsınız.

## <a name="run-the-import-data-wizard"></a>Veri alma Sihirbazı 'nı çalıştırma

1. Azure hesabınızla [Azure portalında](https://portal.azure.com/) oturum açın.

1. [Arama hizmetinizi bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) ve genel bakış sayfasında, dört adımda bilgi deposu oluşturmak için komut çubuğunda **verileri içeri aktar** ' a tıklayın.

   ![Verileri içeri aktar komutu](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>1. Adım: Veri kaynağı oluşturma

1. **Verilerinize bağlanın** bölümünde **Azure Blob depolama**' yı seçin, oluşturduğunuz hesabı ve kapsayıcıyı seçin. 

1. **Ad** için girin `hotel-reviews-ds` .

1. **Ayrıştırma modu** Için, **sınırlandırılmış metin**' i seçin ve ardından **üstbilgi içerir** onay kutusunu seçin. **Sınırlayıcı karakterin** virgül (,) olduğundan emin olun.

1. **Bağlantı dizesi**' nde, Azure depolama 'Daki **erişim tuşları** sayfasından kopyaladığınız bağlantı dizesini yapıştırın.

1. **Kapsayıcılar**' de, verileri tutan blob kapsayıcısının adını girin.

    Sayfanız aşağıdaki ekran görüntüsüne benzer görünmelidir.

    ![Veri kaynağı nesnesi oluşturma](media/knowledge-store-create-portal/hotel-reviews-ds.png "Veri kaynağı nesnesi oluşturma")

1. Sonraki sayfaya devam edin.

### <a name="step-2-add-cognitive-skills"></a>2. Adım: Bilişsel yetenekler ekleme

Bu sihirbaz adımında Bilişsel Beceri bilişsel becerileri ile bir beceri oluşturacaksınız. Kaynak veriler, çeşitli dillerdeki müşteri incelemelerinden oluşur. Bu veri kümesiyle ilgili yetenekler, anahtar tümceciği ayıklama, yaklaşım algılaması ve metin çevirisi içerir. Sonraki bir adımda bu zenginler, Azure tabloları olarak bir bilgi deposunda "yansıtılmaktadır" olacaktır.

1. Bilişsel **Hizmetler Ekle**' yi genişletin. **Ücretsiz (sınırlı enzenginler)** varsayılan olarak seçilidir. Bu kaynağı, HotelReviews-Free.csv içindeki kayıt sayısı 19 ve bu ücretsiz kaynak günde 20 işlem kadar izin verdiğinden kullanabilirsiniz.

1. **Zenginleştirme Ekle**' yi genişletin.

1. **Beceri adı** için girin `hotel-reviews-ss` .

1. **Kaynak veri alanı** için **reviews_text**' yi seçin.

1. **Zenginleştirme ayrıntı düzeyi** için **sayfalar (5000 karakter öbekleri)** seçeneğini belirleyin

1. Bu bilişsel becerileri seçin:
    + **Anahtar ifadeleri ayıklama**
    + **Metin çevirme**
    + **Yaklaşımı Algıla**

      ![Beceri kümesi oluşturma](media/knowledge-store-create-portal/hotel-reviews-ss.png "Beceri kümesi oluşturma")

1. **Zenginleştirme bilgilerini bilgi deposuna kaydet**' i genişletin.

1. Şu **Azure Tablo projeksiyonlarını** seçin:
    + **Belgeler**
    + **Sayfalar**
    + **Anahtar tümceleri**

1. Önceki bir adımda kaydettiğiniz **depolama hesabı bağlantı dizesini** girin.

    ![Bilgi deposunu yapılandırma](media/knowledge-store-create-portal/hotel-reviews-ks.png "Bilgi deposunu yapılandırma")

1. İsteğe bağlı olarak, bir Power BI şablonu indirin. Şablondan, sihirbazdan eriştiğinizde, yerel. pbit dosyası verilerinizin şeklini yansıtacak şekilde uyarlanmıştır.

1. Sonraki sayfaya devam edin.

### <a name="step-3-configure-the-index"></a>3. Adım: Dizini yapılandırma

Bu sihirbaz adımında isteğe bağlı tam metin arama sorguları için bir dizin yapılandıracaksınız. Sihirbaz, alanları ve veri türlerini çıkarması için veri kaynağınızı örnekedecektir. Yalnızca istediğiniz davranışın özniteliklerini seçmeniz gerekir. Örneğin, **alınabilir** özniteliği arama hizmetinin bir alan değeri döndürmesini sağlar, ancak **aranabilir** bir alanda tam metin aramasını etkinleştirir.

1. **Dizin adı** için girin `hotel-reviews-idx` .

1. Öznitelikler için varsayılan seçimleri kabul edin: işlem hattının oluşturmakta olduğu yeni alanlar için **alınabilir** ve **aranabilir** .

    Dizininiz aşağıdaki görüntüye benzer görünmelidir. Liste uzun olduğundan, tüm alanlar görüntüde görünür değildir.

    ![Dizin yapılandırma](media/knowledge-store-create-portal/hotel-reviews-idx.png "Dizin yapılandırma")

1. Sonraki sayfaya devam edin.

### <a name="step-4-configure-the-indexer"></a>4. Adım: Dizin oluşturucuyu yapılandırma

Bu sihirbaz adımında, veri kaynağını, beceri ve önceki sihirbaz adımlarında tanımladığınız dizini birlikte alacak bir Dizin Oluşturucu yapılandıracaksınız.

1. **Ad** için girin `hotel-reviews-idxr` .

1. **Zamanlama** Için varsayılanı **bir kez** saklayın.

1. Dizin oluşturucuyu çalıştırmak için **Gönder** ' e tıklayın. Veri ayıklama, dizin oluşturma, bilişsel yeteneklerin uygulaması bu adımda gerçekleşir.

## <a name="monitor-status"></a>İzleme durumu

Bilişsel Beceri dizin oluşturma, tipik metin tabanlı dizin oluşturma özelliğinden daha uzun sürer. İlerlemeyi izleyebilmek için sihirbazın Genel Bakış sayfasında Dizin Oluşturucu listesini açması gerekir. Kendi kendine gezinme için genel bakış sayfasına gidin ve **Dizin oluşturucular**' ye tıklayın.

Azure portal, tıklatılabilir bir **Azure bilişsel arama bildirim** durumu bağlantısı için bildirimler etkinlik günlüğünü de izleyebilirsiniz. Yürütmenin tamamlanması birkaç dakika sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bilişsel hizmetler 'i kullanarak verilerinizi zenginleştirdikten ve sonuçları bir bilgi deposuna yansıdığınıza göre, zenginleştirilmiş veri kümesini araştırmak için Depolama Gezgini veya Power BI kullanabilirsiniz.

Depolama Gezgini içeriği görüntüleyebilir veya görselleştirme ile ilgili Öngörüler elde etmek için Power BI bir adım adım izleyebilirsiniz.

> [!div class="nextstepaction"]
> [Depolama Gezgini](knowledge-store-view-storage-explorer.md) 
>  ile görüntüle [Power BI bağlanma](knowledge-store-connect-power-bi.md)

> [!Tip]
> Bu alıştırmayı yinelemek veya farklı bir AI zenginleştirme Kılavuzu denemek istiyorsanız, *otel-incelemeler-ıdxr* Dizin oluşturucuyu silin. Dizin oluşturucunun silinmesi, bilişsel hizmetler işleme için ücretsiz günlük işlem sayacını yeniden sıfır olarak sıfırlar.
