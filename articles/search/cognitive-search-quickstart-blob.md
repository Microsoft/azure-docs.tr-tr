---
title: Azure portal bir beceri oluşturma
titleSuffix: Azure Cognitive Search
description: Bu portal hızlı başlangıçta, Azure Bilişsel Arama 'te bir dizin oluşturma işlem hattına bilişsel beceriler eklemek için veri alma Sihirbazı ' nı nasıl kullanacağınızı öğrenin. Yetenekler arasında optik karakter tanıma (OCR) ve doğal dil işleme sayılabilir.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/21/2021
ms.openlocfilehash: d3e1b73789d6bd4df3dfe9a0e05048f9bbbb25bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104770980"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Hızlı başlangıç: Azure portal Azure Bilişsel Arama bilişsel bir beceri oluşturma

Bu hızlı başlangıçta portalda optik karakter tanıma (OCR) ve varlık tanımanın, görüntü ve uygulama dosyalarından aranabilir metin içeriği oluşturmak için nasıl kullanılabileceğini gösteren beceri desteği gösterilmektedir.

Hazırlanmak için birkaç kaynak oluşturacak ve örnek görüntüleri ve uygulama içerik dosyalarını karşıya yükleyeceksiniz. Her şey olduktan sonra, Azure portal **verileri Içeri aktarma** Sihirbazı 'nı bir araya getirin. Nihai sonuç, AI işleme tarafından oluşturulan verilerle doldurulan ve portalda ([Arama Gezgini](search-explorer.md)) sorgulayabilmeniz için aranabilir bir dizindir.

Kodla başlamasını tercih etmek istiyor musunuz? Bkz [. Öğretici: Azure Bloblarından veya öğreticiden aranabilir içerik oluşturmak IÇIN REST ve AI kullanma](cognitive-search-tutorial-blob.md) : bunun yerine [, Azure Bloblarından aranabilir içerik oluşturmak IÇIN .net ve AI](cognitive-search-tutorial-blob-dotnet.md) kullanın.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdaki önkoşulları yerine getirin:

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/).

+ Bir Azure Bilişsel Arama hizmeti. Geçerli aboneliğiniz kapsamında [bir hizmet oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu hızlı başlangıç için ücretsiz bir hizmet kullanabilirsiniz. 

+ [BLOB depolama alanı](../storage/blobs/index.yml)olan bir Azure depolama hesabı.

> [!NOTE]
> Bu hızlı başlangıç Ayrıca AI için Azure bilişsel [Hizmetler](https://azure.microsoft.com/services/cognitive-services/) 'i de kullanır. İş yükü çok küçük olduğu için bilişsel hizmetler, en fazla 20 işlem için ücretsiz işleme için arka planda dokunduğunda. Bu, ek bir bilişsel hizmetler kaynağı oluşturmak zorunda kalmadan bu Alıştırmayı tamamlayabilmeniz anlamına gelir.

## <a name="set-up-your-data"></a>Verilerinizi kurma

Aşağıdaki adımlarda, heterojen içerik dosyalarını depolamak için Azure depolama 'da bir blob kapsayıcısı ayarlayın.

1. Farklı türlerden oluşan küçük bir dosya kümesini içeren [örnek verileri indirin](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4). Dosyaları sıkıştırmayı açın.

1. [Bir Azure depolama hesabı oluşturun](../storage/common/storage-account-create.md?tabs=azure-portal) veya [var olan bir hesabı bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Bant genişliği ücretlerinden kaçınmak için Azure Bilişsel Arama ile aynı bölgeyi seçin. 

   + StorageV2 (genel amaçlı v2) öğesini seçin.

1. Blob Hizmetleri sayfalarını açın ve bir kapsayıcı oluşturun. Varsayılan genel erişim düzeyini kullanabilirsiniz. 

1. Kapsayıcıda, ilk adımda indirdiğiniz örnek dosyaları karşıya yüklemek için **karşıya yükle** ' ye tıklayın. Yerel biçimlerinde aranabilir tam metin olmayan resimler ve uygulama dosyaları dahil olmak üzere çok çeşitli içerik türleri olduğuna dikkat edin.

   :::image type="content" source="media/cognitive-search-quickstart-blob/sample-data.png" alt-text="Azure blob depolamadaki kaynak dosyalar" border="false":::

Artık veri alma Sihirbazı 'na taşımaya hazırsınız.

## <a name="run-the-import-data-wizard"></a>Veri alma Sihirbazı 'nı çalıştırma

1. Azure hesabınızla [Azure portalında](https://portal.azure.com/) oturum açın.

1. [Arama hizmetinizi bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) ve genel bakış sayfasında, komut çubuğunda **verileri Içeri aktar** ' a tıklayarak Bilişsel Zenginleştirme dört adımda ayarlayın.

   :::image type="content" source="media/cognitive-search-quickstart-blob/import-data-cmd2.png" alt-text="Verileri içeri aktar komutu" border="false":::

### <a name="step-1---create-a-data-source"></a>1. adım-veri kaynağı oluşturma

1. **Verilerinize bağlanın** bölümünde **Azure Blob depolama**' yı seçin, oluşturduğunuz depolama hesabını ve kapsayıcıyı seçin. Veri kaynağına bir ad verin ve geri kalanı için varsayılan değerleri kullanın. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/blob-datasource.png" alt-text="Azure blob yapılandırması" border="false":::

    Sonraki sayfaya devam edin.

### <a name="step-2---add-cognitive-skills"></a>2. adım-bilişsel yetenekler ekleme

Ardından, AI zenginleştirme 'yı OCR, görüntü analizi ve doğal dil işlemeyi çağırmak üzere yapılandırın. 

1. Bu hızlı başlangıçta, **ücretsiz** bilişsel hizmetler kaynağını kullanıyoruz. Örnek veriler 14 dosyadan oluşur, bu nedenle bilişsel hizmetler üzerinde 20 işlemin ücretsiz servis birimi bu hızlı başlangıç için yeterlidir. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/cog-search-attach.png" alt-text="Bilişsel hizmetler iliştirme temel hizmeti Ekle" border="false":::

1. **Zenginleştirme Ekle** ' yi genişletin ve dört seçim yapın. 

   Sihirbaz sayfasına görüntü analizi becerileri eklemek için OCR 'yi etkinleştirin.

   Metni daha küçük parçalara bölmek için sayfalar için ayrıntı düzeyi ayarlayın. Çeşitli metin becerileri 5 KB 'lik girişlerle sınırlıdır.

   Varlık tanıma (kişiler, kuruluşlar, konumlar) ve görüntü analizi becerileri seçin.

   :::image type="content" source="media/cognitive-search-quickstart-blob/skillset.png" alt-text="Bilişsel hizmetler Ekle beceri için hizmet seçme" border="false":::

   Sonraki sayfaya devam edin.

### <a name="step-3---configure-the-index"></a>3. adım-dizini yapılandırma

Bir dizin, aranabilir içeriğinizi içerir ve **veri Içeri aktarma** Sihirbazı, genellikle veri kaynağını örnekleyerek şemayı oluşturabilir. Bu adımda, oluşturulan şemayı gözden geçirin ve ayarları olası olarak gözden geçirin. Demo blob veri kümesi için oluşturulan varsayılan şema aşağıda verilmiştir.

Bu hızlı başlangıç, makul varsayılanlar ayarlanması konusunda iyi bir iş çıkarır:  

+ Varsayılan alanlar, mevcut Blobların özelliklerine ve zenginleştirme çıkışı (örneğin,,,) içeren yeni alanlara dayanır `people` `organizations` `locations` . Veri türleri meta verilerden ve veri örneklemesi tarafından algılanır.

+ Varsayılan belge anahtarı *metadata_storage_path* (alan benzersiz değerler içerdiğinden seçilidir).

+ Varsayılan öznitelikler **alınabilir** ve **aranabilir**. **Aranabilir** , bir alan için tam metin aramasına izin verir. **Alınabilir** anlamına gelir, sonuç olarak alan değerleri döndürülür. Sihirbaz, bir beceri aracılığıyla oluşturduğunuz için bu alanların alınabilir ve aranabilir olmasını istediğiniz varsayılır.

  :::image type="content" source="media/cognitive-search-quickstart-blob/index-fields.png" alt-text="Dizin alanları" border="false":::

Alana göre **alınabilir** özniteliğinde, çizili ve soru işaretine dikkat edin `content` . Metin açısından ağır blob belgeleri için, `content` alan büyük olasılıkla binlerce satıra çalışan dosyanın toplu kısmını içerir. Bu gibi bir alan, arama sonuçlarında çok daha gerçekçi bir alandır ve bu demo için dışarıda bırakmanız gerekir. 

Ancak, dosya içeriklerini istemci koduna geçirmeniz gerekirse, bu dosyanın, **alınabilir** olarak seçili kaldığından emin olun. Aksi takdirde, `content` ayıklanan öğeler (örneğin,, vb `people` `organizations` `locations` .) yeterliyse, üzerinde bu özniteliği temizlemeyi göz önünde bulundurun.

Bir alanı **alınabilir** olarak işaretlemek alanın arama sonuçlarında bulunması *gerektiği* anlamına gelmez. Hangi alanların ekleneceğini belirlemek için **$Select** sorgu parametresini kullanarak arama sonuçları kompozisyonunu kesin bir şekilde denetleyebilirsiniz. Gibi metin açısından ağır alanlar için `content` , **$Select** parametresi, uygulamanızın insan kullanıcılarına yönetilebilir arama sonuçları sağlamaya yönelik çözümünüzde, istemci kodunun **alınabilir** öznitelik aracılığıyla ihtiyaç duyacağı tüm bilgilere erişmesine olanak sağlar.
  
Sonraki sayfaya devam edin.

### <a name="step-4---configure-the-indexer"></a>4. adım-Dizin oluşturucuyu yapılandırma

Dizin oluşturucu, dizin oluşturma işlemini destekleyen, yüksek düzeyli bir kaynaktır. Veri kaynağı adı, hedef dizin ve yürütme sıklığını belirtir. **Verileri Içeri aktarma** Sihirbazı birçok nesne oluşturur ve bu her zaman sürekli çalıştırabileceğiniz bir dizin oluşturucudır.

1. **Dizin Oluşturucu** sayfasında, varsayılan adı kabul edebilir ve hemen çalıştırmak Için **bir kez** zamanlama seçeneğine tıklayabilirsiniz. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-def.png" alt-text="Dizin oluşturucu tanımı" border="false":::

1. Dizin oluşturucuyu oluşturmak ve aynı anda çalıştırmak için **Gönder** ' e tıklayın.

## <a name="monitor-status"></a>İzleme durumu

Bilişsel yetenekler dizin oluşturma işlemi, genellikle OCR ve resim analizinden daha uzun sürer. İlerlemeyi izlemek için genel bakış sayfasına gidin ve sayfanın ortasında **Dizin oluşturucular** ' ye tıklayın.

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-notification.png" alt-text="Azure Bilişsel Arama bildirimi" border="false":::

Uyarılar, çok çeşitli içerik türleri için normaldir. Bazı içerik türleri belirli beceriler ve daha düşük katmanlarda geçerli değildir, [Dizin Oluşturucu limitleriyle](search-limits-quotas-capacity.md#indexer-limits)karşılaşmanız yaygındır. Örneğin, 32.000 karakterlik kesme bildirimleri, ücretsiz katmanda bir Dizin Oluşturucu sınırdır. Bu tanıtımı daha yüksek bir katmanda çalıştırdıysanız birçok kesme uyarısı kaybolur.

Uyarıları veya hataları denetlemek için, Dizin oluşturucular listesindeki uyarı durumuna tıklayarak yürütme geçmişi sayfasını açın.

Bu sayfada, aşağıda gösterilene benzer uyarıların listesini görüntülemek için uyarı durumu ' na tıklayın. 

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-warnings.png" alt-text="Dizin Oluşturucu uyarı listesi" border="false":::

Belirli bir durum satırına tıkladığınızda ayrıntılar görüntülenir. Bu uyarı, birleştirmenin en büyük eşiğe ulaştıktan sonra durdurulduğunu belirtir (Bu PDF büyük olur).

  :::image type="content" source="media/cognitive-search-quickstart-blob/warning-detail.png" alt-text="Uyarı ayrıntıları" border="false":::

## <a name="query-in-search-explorer"></a>Arama gezgininde sorgulama

Bir dizin oluşturulduktan sonra, sonuçları döndürmek için sorguları çalıştırabilirsiniz. Portalda bu görev için **Arama Gezgini** ' ni kullanın. 

1. Arama hizmeti panosu sayfasında, komut çubuğunda **Arama Gezgini** ' ne tıklayın.

1. Oluşturduğunuz dizini seçmek için üst kısımdaki **Dizini değiştir**'e tıklayın.

1. Dizini sorgulamak için bir arama dizesi girin, örneğin `search=Microsoft&$select=people,organizations,locations,imageTags` .

Sonuçlar, özellikle de Azure Bloblarından kaynaklanan büyük belgelerde ayrıntılı ve okunabilir olabilecek JSON olarak döndürülür. Bu araçta arama için bazı ipuçları aşağıdaki teknikleri içerir:

+ `$select`Sonuçlara hangi alanların ekleneceğini belirtmek için Ekle. 
+ Belirli özellikler veya terimler için JSON içinde arama yapmak için CTRL-F kullanın.

Sorgu dizeleri büyük/küçük harfe duyarlıdır; bu nedenle, bir "bilinmeyen alan" iletisi alırsanız, ad ve durumu doğrulamak için **alanları** veya **DIZIN tanımını (JSON)** denetleyin. 

  :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer.png" alt-text="Arama gezgini örneği" border="false":::

## <a name="takeaways"></a>Paketler

Şimdi ilk beceri oluşturdunuz ve kendi verilerinizi kullanarak zenginleştirilmiş bir arama çözümünü prototip oluşturma konusunda yararlı olan öğrendiğimiz önemli kavramlar.

Topladığınız bazı temel kavramlar, Azure veri kaynaklarına bağımlılık içerir. Bir beceri bir dizin oluşturucuya bağlanır ve Dizin oluşturucular Azure ve kaynağa özgüdür. Bu hızlı başlangıçta Azure Blob depolama kullanılsa da, başka Azure veri kaynakları da mümkündür. Daha fazla bilgi için bkz. [Azure bilişsel arama 'Da Dizin oluşturucular](search-indexer-overview.md). 

Diğer önemli bir kavram, yeteneklerin içerik türlerini üzerinde çalışması ve heterojen içerikle çalışırken bazı girişler atlanacak. Ayrıca, büyük dosyalar veya alanlar hizmet katmanınızın Dizin Oluşturucu sınırlarını aşabilir. Bu olaylar gerçekleştiğinde uyarıları görmek normaldir. 

Çıktı bir arama dizinine yönlendirilir ve dizininizdeki tek tek alanlar sırasında oluşturulan ad-değer çiftleri arasında bir eşleme vardır. Dahili olarak portal, [ek açıklamalar](cognitive-search-concept-annotations-syntax.md) ayarlar ve işlemlerin sırasını ve genel akışı oluşturan bir [beceri kümesini](cognitive-search-defining-skillset.md) tanımlar. Bu adımlar portalda gizlenir, ancak kod yazmaya başladığınızda bu kavramlar önemli hale gelir.

Son olarak, dizini sorgulayarak içeriği doğrulayabildiğini öğrendiniz. Son olarak, Azure Bilişsel Arama 'in sağladığı, [basit](/rest/api/searchservice/simple-query-syntax-in-azure-search) veya [tam genişletilmiş sorgu söz dizimini](/rest/api/searchservice/lucene-query-syntax-in-azure-search)kullanarak sorgulayabilmeniz için aranabilir bir dizindir. Zenginleştirilmiş alanlar içeren bir dizin de diğerlerine benzer. Standart veya [özel çözümleyiciler](search-analyzers.md), [Puanlama profilleri](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [eş anlamlı](search-synonyms.md), çok [yönlü filtreler](search-filters-facets.md), coğrafi arama veya diğer Azure bilişsel arama özelliklerini birleştirmek istiyorsanız, kesinlikle bunu yapabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kendi aboneliğinizde çalışırken, projenin sonunda oluşturduğunuz kaynaklara hala ihtiyacınız olup olmadığını belirlemek iyi bir fikirdir. Çalışır durumda bırakılan kaynaklar maliyetlerin artmasına neden olabilir. Kaynakları teker teker silebilir veya tüm kaynak grubunu silerek kaynak kümesinin tamamını kaldırabilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Portal, .NET SDK veya REST API kullanarak becerileri oluşturabilirsiniz. Daha ayrıntılı bilgi için Postman ve daha fazla örnek veri kullanarak REST API deneyin.

> [!div class="nextstepaction"]
> [Öğretici: REST API 'Leri kullanarak JSON Bloblarından metin ve yapı ayıklama ](cognitive-search-tutorial-blob.md)

> [!Tip]
> Bu alıştırmayı yinelemek veya farklı bir AI zenginleştirme Kılavuzu denemek istiyorsanız, portaldaki Dizin oluşturucuyu silin. Dizin oluşturucunun silinmesi, bilişsel hizmetler işleme için ücretsiz günlük işlem sayacını yeniden sıfır olarak sıfırlar.