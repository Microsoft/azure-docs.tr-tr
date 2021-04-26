---
title: 'Öğretici: becerileri gidermek için hata ayıklama oturumlarını kullanma'
titleSuffix: Azure Cognitive Search
description: Hata ayıklama oturumları (Önizleme) bir beceri içinde sorunları bulmak, tanılamak ve onarmak için kullanılan bir Azure portal aracıdır.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/02/2021
ms.openlocfilehash: ed988baec46152d55cf63aec09fce7a298157212
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99509158"
---
# <a name="tutorial-debug-a-skillset-using-debug-sessions"></a>Öğretici: hata ayıklama oturumlarını kullanarak bir beceri hata ayıklayın

Becerileri içeriği çözümleyen veya dönüştüren bir dizi eylemi koordine eder. Bu, bir yeteneğin çıktısının başka bir giriş haline geldiği yerdir. Girişler çıkışlara bağımlıysa, Beceri tanımlarındaki ve alan ilişkilendirmelerinin hataları kaçırılmış işlem ve verilere neden olabilir.

Azure portal **hata ayıklama oturumları** , bir beceri bütünsel görselleştirmesini sağlar. Bu aracı kullanarak, bir eylemin nerede olabileceğini kolayca görmek için belirli adımlara gidebilirsiniz.

Bu makalede, **hata ayıklama oturumlarını** , 1) eksik bir giriş ve 2) çıkış alanı eşlemelerini bulmak ve gidermek için kullanacaksınız. Öğretici hepsi dahil değildir. Dizin oluşturma (klinik deneme verileri), nesneleri oluşturan bir Postman koleksiyonu ve beceri 'de sorunları bulmak ve gidermek için **hata ayıklama oturumlarını** kullanma yönergeleri için veri sağlar.

> [!Important]
> Hata ayıklama oturumları, bir hizmet düzeyi sözleşmesi olmadan sunulan bir önizleme özelliğidir ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdaki önkoşulları yerine getirin:

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/).

+ Bir Azure Bilişsel Arama hizmeti. Geçerli aboneliğiniz kapsamında [bir hizmet oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu hızlı başlangıç için ücretsiz bir hizmet kullanabilirsiniz. 

+ Örnek verileri barındırmak için ve bir hata ayıklama oturumu sırasında oluşturulan kalıcı geçici veriler için kullanılan [BLOB depolama alanına](../storage/blobs/index.yml)sahip bir Azure depolama hesabı.

+ REST API 'Leri kullanarak nesne oluşturmaya yönelik [Postman masaüstü uygulaması](https://www.getpostman.com/) ve bir [Postman koleksiyonu](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) .

+ [Örnek veriler (klinik denemeler)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19).

> [!NOTE]
> Bu hızlı başlangıç Ayrıca AI için Azure bilişsel [Hizmetler](https://azure.microsoft.com/services/cognitive-services/) 'i de kullanır. İş yükü çok küçük olduğu için bilişsel hizmetler, en fazla 20 işlem için ücretsiz işleme için arka planda dokunduğunda. Bu, ek bir bilişsel hizmetler kaynağı oluşturmak zorunda kalmadan bu Alıştırmayı tamamlayabilmeniz anlamına gelir.

## <a name="set-up-your-data"></a>Verilerinizi kurma

Bu bölüm, dizin oluşturucunun ve beceri birlikte çalışmak üzere içeriğe sahip olması için Azure Blob depolamada örnek veri kümesini oluşturur.

1. 19 dosyadan oluşan [örnek verileri (klinik-denemeler-PDF-19) indirin](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19).

1. [Bir Azure depolama hesabı oluşturun](../storage/common/storage-account-create.md?tabs=azure-portal) veya [var olan bir hesabı bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Bant genişliği ücretlerinden kaçınmak için Azure Bilişsel Arama ile aynı bölgeyi seçin.

   + StorageV2 (genel amaçlı v2) hesap türünü seçin.

1. Portalda Azure Storage Services sayfalarına gidin ve bir blob kapsayıcısı oluşturun. En iyi yöntem, "özel" erişim düzeyini belirtmektir. Kapsayıcınızı adlandırın `clinicaltrialdataset` .

1. Kapsayıcıda, ilk adımda indirdiğiniz ve daraltılmış örnek dosyaları karşıya yüklemek için **Yükle** ' ye tıklayın.

1. Portalda, Azure depolama için bağlantı dizesini alın ve kapatın. Bu, verileri dizine alan REST API çağrısı için gerekir. Bağlantı dizesini portalda **Ayarlar**  >  **erişim tuşlarından** alabilirsiniz.

## <a name="get-a-key-and-url"></a>Anahtar ve URL al

REST çağrıları için her istekte hizmet URL'sinin ve bir erişim anahtarının iletilmesi gerekir. Her ikisiyle de bir arama hizmeti oluşturulur. bu nedenle, aboneliğinize Azure Bilişsel Arama eklediyseniz, gerekli bilgileri almak için aşağıdaki adımları izleyin:

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetine **genel bakış** sayfasında URL 'yi alın. Örnek uç nokta `https://mydemo.search.windows.net` şeklinde görünebilir.

1. **Ayarlar**  >  **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="HTTP uç noktası ve erişim anahtarı al" border="false":::

Tüm istekler hizmetinize gönderilen her istekte bir API anahtarı gerektirir. İstek başına geçerli bir anahtara sahip olmak, isteği gönderen uygulama ve bunu işleyen hizmet arasında güven oluşturur.

## <a name="create-data-source-skillset-index-and-indexer"></a>Veri kaynağı, Beceri, dizin ve Dizin Oluşturucu oluşturma

Bu bölümde, Postman ve sağlanmış bir koleksiyon Bilişsel Arama veri kaynağı, Beceri, dizin ve Dizin Oluşturucu oluşturmak için kullanılır. Postman hakkında bilginiz varsa, [Bu hızlı başlangıç](search-get-started-rest.md)bölümüne bakın.

Bu görevi gerçekleştirmek için bu öğreticide [Postman koleksiyonunun](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) oluşturulması gerekir. 

1. Postman 'ı başlatın ve koleksiyonu içeri aktarın. Yeni **dosyalar** altında  >  , içeri aktarılacak koleksiyonu seçin.
1. Koleksiyon alındıktan sonra, eylemler listesini (...) genişletin.
1. **Düzenle**’ye tıklayın.
1. Geçerli değer altında, adını girin `searchService` (örneğin, uç nokta ise, `https://mydemo.search.windows.net` hizmet adı " `mydemo` ").
1. `apiKey`Arama hizmetinizin birincil veya ikincil anahtarıyla birlikte girin.
1. `storageConnectionString`Azure depolama hesabınızın anahtarlar sayfasından yazın.
1. `containerName`Depolama hesabında oluşturduğunuz kapsayıcı için yazın ve ardından **Güncelleştir**' e tıklayın.

   :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="Postman 'da değişkenleri Düzenle":::

Koleksiyonda, bu öğreticide kullanılan nesneleri oluşturmak için kullanılan dört farklı REST çağrısı bulunur.

İlk çağrı veri kaynağını oluşturur. `clinical-trials-ds`. İkinci çağrı, Beceri oluşturur `clinical-trials-ss` . Üçüncü çağrı dizinini oluşturur `clinical-trials` . Dördüncü ve son çağrı Dizin oluşturucuyu oluşturur `clinical-trials-idxr` .

+ Her isteği sırayla açın ve her isteği arama hizmetine göndermek için **Gönder** ' e tıklayın. 

Koleksiyondaki tüm çağrılar tamamlandıktan sonra Postman ' ı kapatıp Azure portal döndürün.

## <a name="check-results-in-the-portal"></a>Portalda sonuçları denetleme

Örnek kod, Beceri yürütme sırasında oluşan sorunların sonucu olarak kasıtlı olmayan bir dizin oluşturur. Sorunun verileri eksik. 

1. Azure portal 'de, arama hizmeti genel bakış sayfasında **dizinler** sekmesini seçin. 
1. Dizini seçin `clinical-trials` .
1. Bu sorgu dizesini girin: `$select=metadata_storage_path, organizations, locations&$count=true` belirli belgeler için alanları döndürmek için (benzersiz alan tarafından tanımlanır `metadata_storage_path` ).
1. Sorguyu çalıştırmak için **Ara** ' ya tıklayın, tüm 19 belge, "kuruluşlar" ve "konumlar" için boş değerler gösteriliyor.

Bu alanlar, blob 'un içeriğinde herhangi bir yerde bulunan kuruluşları ve konumları bulmak için kullanılan beceri [varlık tanıma özelliği](cognitive-search-skill-entity-recognition.md)aracılığıyla doldurulmuş olmalıdır. Bir sonraki alıştırmada, neyin yanlış gittiğini belirlemek için hata ayıklama oturumu kullanacaksınız.

Hataları ve uyarıları araştırmaya yönelik başka bir yol da Azure portal.

1. **Dizin oluşturucular** sekmesini açın ve öğesini seçin `clinical-trials-idxr` .
1. Dizin Oluşturucu iş başarılı olduğunda 57 uyarı olduğunu fark edersiniz.
1. Uyarıları görüntülemek için **başarılı** ' e tıklayın (genellikle hatalar varsa, ayrıntı bağlantısı **başarısız** olur). Dizin Oluşturucu tarafından yayılan her uyarının uzun bir listesini görürsünüz.

   :::image type="content" source="media/cognitive-search-debug/portal-indexer-errors-warnings.png" alt-text="Uyarıları görüntüle":::

## <a name="start-your-debug-session"></a>Hata ayıklama oturumunuzu başlatın

:::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="Yeni bir hata ayıklama oturumu başlatın":::

1. Genel Bakış sayfasında, **hata ayıklama oturumları** sekmesine tıklayın.
1. **+ Yeni hata ayıklama oturumu** seçin.
1. Oturuma bir ad verin. 
1. Oturumu depolama hesabınıza bağlayın. 
1. Dizin Oluşturucu şablonunda, Dizin Oluşturucu adını sağlayın. Dizin oluşturucunun veri kaynağına, beceri ve dizine başvuruları vardır.
1. Koleksiyondaki ilk belge için varsayılan belge seçimini kabul edin. 
1. Oturumu **kaydedin** . Oturumun kaydedilmesi, beceri tarafından tanımlanan AI zenginleştirme işlem hattını başlatabilir.

> [!Important]
> Bir hata ayıklama oturumu yalnızca tek bir belge ile kullanılabilir. Hangi belgenin hata ayıklayabileceği seçebilirsiniz veya yalnızca ilkini kullanmanız yeterlidir.

<!-- > :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="New debug session started"::: -->

Hata ayıklama oturumunun başlatılması tamamlandığında, oturum varsayılan olarak, **Yetenek grafiğini** vurgulayan **AI zenginleştirme** sekmesine göre yapılır. Beceri grafiği, Beceri görsel bir hiyerarşisini ve yürütme sırasını ardışık olarak ve paralel olarak sağlar.

## <a name="find-issues-with-the-skillset"></a>Beceri ile ilgili sorunları bulma

Dizin oluşturucunun bildirdiği tüm sorunlar bitişik **hatalar/uyarılar** sekmesinde bulunabilir. 

Bu liste yalnızca tek bir belge için hataları ayrıntılacağından, **hatalar/uyarılar** sekmesinin daha önce görüntülenenden çok daha küçük bir liste sunduğuna dikkat edin. Dizin Oluşturucu tarafından gösterildiği gibi, bir uyarı iletisine tıklayabilir ve bu uyarının ayrıntılarına bakabilirsiniz.

Bildirimleri gözden geçirmek için **hataları/uyarıları** seçin. Üç tane görmeniz gerekir:

   + "' Locations ' Çıkış alanı arama dizinine eşlenemiyor. Dizin oluşturucunun ' outputFieldMappings ' özelliğini denetleyin.
'/Document/merged_content/Locations ' değeri eksik. "

   + "' Kuruluşlar ' Çıkış alanı arama dizinine eşlenemiyor. Dizin oluşturucunun ' outputFieldMappings ' özelliğini denetleyin.
'/Document/merged_content/kuruluşlar ' değeri eksik. "

   + "Bir veya daha fazla yetenek girişi geçersiz olduğundan, yetenek yürütüldü ancak beklenmeyen sonuçlara neden olabilir.
İsteğe bağlı yetenek girişi eksik. Ad: ' languageCode ', kaynak: '/document/languageCode '. İfade dili ayrıştırma sorunları: '/document/languageCode ' değeri eksik. "

   Birçok beceri ' languageCode ' parametresine sahiptir. İşlemi inceleyerek, bu dil kodu girişinin `Enrichment.NerSkillV2.#1` ' konum ' ve ' kuruluşlar ' çıkışıyla ilgili sorun yaşayan varlık tanıma yeteneği olan ' de eksik olduğunu görebilirsiniz. 

Üç bildirimin tümü bu yetenek hakkında olduğundan, bir sonraki adımınız bu yeteneğin hata ayıklamada olur. Mümkünse, outputFieldMapping sorunlarına geçmeden önce giriş sorunlarını çözerek başlayın.

 :::image type="content" source="media/cognitive-search-debug/debug-session-errors-warnings.png" alt-text="Yeni hata ayıklama oturumu başlatıldı":::

<!-- + The Skill Graph provides a visual hierarchy of the skillset and its order of execution from top to bottom. Skills that are side by side in the graph are executed in parallel. Color coding of skills in the graph indicate the types of skills that are being executed in the skillset. In the example, the green skills are text and the red skill is vision. Clicking on an individual skill in the graph will display the details of that instance of the skill in the right pane of the session window. The skill settings, a JSON editor, execution details, and errors/warnings are all available for review and editing.

+ The Enriched Data Structure details the nodes in the enrichment tree generated by the skills from the source document's contents. -->

## <a name="fix-missing-skill-input-value"></a>Eksik yetenek girişi değerini düzeltir

**Hatalar/uyarılar** sekmesinde, etiketli bir işlem için hata vardır `Enrichment.NerSkillV2.#1` . Bu hatanın ayrıntıları, '/document/languageCode ' yetenek girişi değeriyle ilgili bir sorun olduğunu açıklamaktadır. 

1. **AI zenginleştirme** sekmesine dönün.
1. **Beceri grafiğine** tıklayın.
1. Ayrıntılarını sağ bölmede göstermek için **#1** etiketli yeteneğe tıklayın.
1. "LanguageCode" girdisini bulun.
1. **</>** Satırın başındaki simgeyi seçin ve Ifade değerlendirici ' ni açın.
1. Bu ifadenin bir hataya neden olduğunu onaylamak için **değerlendir** düğmesine tıklayın. "LanguageCode" özelliğinin geçerli bir giriş olduğunu doğrulayacaktır.

   :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="İfade Değerlendirici":::

Oturumda bu hatayı araştırmak için iki yol vardır. İlk olarak, girişin nereden geldiğini, hiyerarşideki hangi becerinin bu sonucu üretmesi gerektiğini bakmamız gerekir mi? Yetenek ayrıntıları bölmesindeki yürütmeler sekmesi girişin kaynağını görüntülemelidir. Kaynak yoksa, bu bir alan eşleme hatası olduğunu gösterir.

1. **Yürütmeler** sekmesine tıklayın.
1. Girişlere bakın ve "languageCode" öğesini bulun. Bu giriş için listelenen kaynak yok. 
1. Zenginleştirilmiş veri yapısını göstermek için sol bölmeyi değiştirin. "LanguageCode" öğesine karşılık gelen eşlenmiş yol yok.

   :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Zenginleştirilmiş veri yapısı":::

"Language" için eşlenmiş bir yol var. Bu nedenle, Beceri ayarlarında bir yazım hatası vardır. #1 Bu ifadeyi onarmak için, '/Document/Language ' ifadesiyle ilgili yeteneğin güncellenmesi gerekecektir.

1. **</>**"Language" yolu Için Ifade değerlendirici ' ni açın.
1. İfadeyi kopyalayın. Pencereyi kapatın.
1. #1 beceri için yetenek ayarlarına gidin ve **</>** "languageCode" girişi Için Ifade değerlendirici ' ni açın.
1. Yeni '/Document/Language ' değerini Ifade kutusuna yapıştırın ve **değerlendir**' e tıklayın.
1. Doğru "en" girişini görüntülemelidir. İfadeyi güncelleştirmek için Uygula ' ya tıklayın.
1. Sağ, yetenek ayrıntıları bölmesinde **Kaydet** ' e tıklayın.
1. Oturumun pencere menüsünde **Çalıştır** ' a tıklayın. Bu, belgeyi kullanarak beceri 'in başka bir yürütmesini de başlatabilir. 

Hata ayıklama oturumu yürütmesi tamamlandığında, hatalar/uyarılar sekmesine tıklayın ve "Enrichment. NerSkillV2. #1" etiketli hatanın gitti olduğunu gösterir. Ancak, hizmetin, kuruluşların ve konumların çıkış alanlarını arama dizinine eşleyemediğinden oluşan iki uyarı hala vardır. Eksik değerler var: '/Document/merged_content/kuruluşlar ' ve '/Document/merged_content/Locations '.

## <a name="fix-missing-skill-output-values"></a>Eksik yetenek çıkış değerlerini düzeltir

:::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Hatalar ve uyarılar":::

Bir yeteneğin eksik çıkış değerleri var. Beceriyle ilgili hatayı belirlemek için zenginleştirilmiş veri yapısına gidin, değer adını bulun ve kaynak kaynağına bakın. Eksik kuruluşlar ve konumlar değerleri söz konusu olduğunda, Beceri #1 çıktılardır. Her yol için </> Ifade Değerlendiricisi açmak, sırasıyla '/Document/Content/organizasyonlar ' ve '/Document/Content/Locations ' olarak listelenen ifadeleri görüntüler.

:::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="İfade değerlendirici kuruluşlar varlığı":::

Bu varlıkların çıktısı boş ve boş olmamalıdır. Bu sonucu üreten girişler nelerdir?

1. **Yetenek grafiğine** gidin ve yetenek #1 seçin.
1. Doğru yetenek ayrıntıları bölmesinde **yürütmeler** sekmesini seçin.
1. **</>**"Metin" girişi Için Ifade değerlendirici ' ni açın.

   :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Metin beceriye giriş":::

Bu giriş için görüntülenmiş sonuç metin girişi gibi görünmüyor. Yeni satırlarla çevrelenen bir görüntü gibi görünüyor. Metnin bulunmaması, hiçbir varlık tanımlanmayacağı anlamına gelir. Beceri hiyerarşisine bakarak içeriğin ilk olarak #6 (OCR) yetenek tarafından işlendiğini ve sonra da #5 (birleştirme) beceriye geçtiğini gösterir. 

1. **Yetenek grafiğinde**#5 (birleştirme) yeteneği seçin.
1. Doğru beceri ayrıntıları bölmesinde **yürütmeler** sekmesini seçin ve **</>** "BIRLEŞTIRIMETIN" çıktıları için ifade değerlendirici ' ni açın.

   :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Birleştirme yeteneği için çıkış":::

Burada metin görüntüyle eşleştirilmiş. '/Document/merged_content ' ifadesine bakarak #1 beceriye yönelik "kuruluşlar" ve "konumlar" yollarında hata görünür. '/Document/Content ' kullanmak yerine "metin" girişleri için '/Document/merged_content ' kullanması gerekir.

1. "Birleştirimetin" çıkışı için ifadeyi kopyalayın ve Ifade değerlendirici penceresini kapatın.
1. **Beceri grafiğinde** yetenek #1 seçin.
1. Doğru beceri ayrıntıları bölmesinde **yetenek ayarları** sekmesini seçin.
1. **</>**"Metin" girişi Için Ifade değerlendirici ' ni açın.
1. Yeni ifadeyi kutuya yapıştırın. **Değerlendir**' e tıklayın.
1. Eklenen metin ile doğru giriş görüntülenmelidir. Yetenek ayarlarını güncelleştirmek için **Uygula** ' ya tıklayın.
1. Sağ, yetenek ayrıntıları bölmesinde **Kaydet** ' e tıklayın.
1. Oturumlar penceresi menüsünde **Çalıştır** ' a tıklayın. Bu, belgeyi kullanarak beceri 'in başka bir yürütmesini de başlatabilir.

Dizin oluşturucunun çalışmayı bitirdikten sonra hatalar hala orada kalır. Yetenek #1 ve Araştır ' a geri dönün. Niteliğin girişi ' merged_content ' olarak ' content ' üzerinden düzeltildi. Beceriye bu varlıkların çıkışları nelerdir?

1. **AI zenginleştirme** sekmesini seçin.
1. **Yetenek grafiği** ' ni seçin ve yetenek #1 ' ye tıklayın.
1. "Çıktılar" bulmak için **yetenek ayarları** ' na gidin.
1. **</>**"Kuruluşlar" varlığı Için Ifade değerlendirici ' ni açın.

   :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Kuruluşlar varlığı için çıkış":::

İfadenin sonucunu değerlendirmek, doğru sonucu verir. Yetenek, "kuruluşlar" varlığı için doğru değeri belirlemek üzere çalışmaktadır. Ancak, varlığın yolundaki çıkış eşlemesi hala bir hata üretiliyor. Becerinizdeki çıkış yolunu hatada çıkış yolu ile karşılaştıran, çıkışları, kuruluşları ve/Document/Content düğümü altındaki konumları ele alan beceri. Çıkış alanı eşlemesi, sonuçların/Document/merged_content düğümü altında ana öğe olmasını bekliyor. Önceki adımda, giriş '/Document/Content ' iken '/Document/merged_content ' olarak değiştirildi. Çıkışın doğru içerikle oluşturulduğundan emin olmak için, yetenek ayarlarındaki bağlamın değiştirilmesi gerekir.

1. **AI zenginleştirme** sekmesini seçin.
1. **Yetenek grafiği** ' ni seçin ve yetenek #1 ' ye tıklayın.
1. "Bağlam" bulmak için **yetenek ayarları** ' na gidin.
1. "Bağlam" ayarına çift tıklayın ve '/Document/merged_content ' okumak için düzenleyin.
1. Sağ, yetenek ayrıntıları bölmesinde **Kaydet** ' e tıklayın.
1. Oturumlar penceresi menüsünde **Çalıştır** ' a tıklayın. Bu, belgeyi kullanarak beceri 'in başka bir yürütmesini de başlatabilir.

   :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Yetenek ayarında bağlam düzeltmesi":::

Tüm hatalar çözüldü.

## <a name="commit-changes-to-the-skillset"></a>Değişiklikleri beceri 'e Kaydet

Hata ayıklama oturumu başlatıldığında, arama hizmeti beceri bir kopyasını oluşturmuştur. Bu, arama hizmetinizde orijinal beceri korumak için gerçekleştirildi. Beceri hata ayıklamayı bitirdiğiniz için, düzeltmeler uygulanabilir (özgün beceri üzerine yazılır). 

Alternatif olarak, değişiklikleri kaydetmeye hazırsanız, hata ayıklama oturumunu kaydedebilir ve daha sonra yeniden açabilirsiniz.

1. Ana hata ayıklama oturumları menüsünde **değişiklikleri Yürüt** ' e tıklayın.
1. Beceri hesabınızı güncelleştirmek istediğinizi onaylamak için **Tamam** ' ı tıklatın.
1. Hata ayıklama oturumunu kapatın ve **Dizin oluşturucular** sekmesini seçin.
1. ' Klinik-deneme-ıdxr ' dosyanızı açın.
1. **Sıfırla**' ya tıklayın.
1. **Çalıştır**'a tıklayın. Onaylamak için **Tamam** ' ı tıklatın.

Dizin Oluşturucu çalışmayı bitirdiğinde, **yürütme geçmişi** sekmesinde en son çalıştırma için zaman damgasının yanında yeşil onay Işareti ve başarı sözcüğü gerekir. Değişikliklerin uygulandığından emin olmak için:

1. Genel Bakış sayfasında, **Dizin** sekmesini seçin.
1. ' Klinik-denemeler ' dizinini açın ve arama Gezgini sekmesinde, `$select=metadata_storage_path, organizations, locations&$count=true` belirli belgelerin alanlarını (benzersiz alan tarafından tanımlanır) döndürmek için şu sorgu dizesini girin: `metadata_storage_path`
1. **Ara**’ya tıklayın.

Sonuçlar, kuruluşların ve konumların artık beklenen değerlerle doldurulduğunu göstermelidir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kendi aboneliğinizde çalışırken, projenin sonunda oluşturduğunuz kaynaklara hala ihtiyacınız olup olmadığını belirlemek iyi bir fikirdir. Çalışır durumda bırakılan kaynaklar maliyetlerin artmasına neden olabilir. Kaynakları teker teker silebilir veya tüm kaynak grubunu silerek kaynak kümesinin tamamını kaldırabilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğretici, Beceri tanımının ve işlemenin çeşitli yönlerine dokunmaz. Kavramlar ve iş akışları hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

+ [Beceri çıkış alanlarını bir arama dizinindeki alanlarla eşleme](cognitive-search-output-field-mapping.md)

+ [Azure Bilişsel Arama 'de becerileri](cognitive-search-working-with-skillsets.md)

+ [Artımlı zenginleştirme için önbelleğe almayı yapılandırma](cognitive-search-incremental-indexing-conceptual.md)