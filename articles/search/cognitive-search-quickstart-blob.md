---
title: 'Hızlı başlangıç: Azure portal bir beceri oluşturma'
titleSuffix: Azure Cognitive Search
description: Bir dizin oluşturma işlem hattına bilişsel beceriler eklemek için veri Içeri aktarma Sihirbazı 'nı kullanın. Bilişsel yetenekler, optik karakter tanıma (OCR) ve doğal dil işleme işlemlerini içerir.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 2280b718fe949384bb67b1b606ab143ddca8e077
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113360"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Hızlı başlangıç: Azure portal Azure Bilişsel Arama bilişsel bir beceri oluşturma

Beceri, büyük ölçekli metinden veya resim dosyalarından bilgi ve yapıyı çıkaran ve Azure Bilişsel Arama tam metin arama sorguları için dizinlenebilir ve aranabilir hale getiren bir AI özelliğidir. 

Bu hızlı başlangıçta, beceri oluşturmak için Azure bulutundaki Hizmetleri ve verileri birleştirebilirsiniz. Her şey oluşturulduktan sonra, portalda **verileri Içeri aktarma** Sihirbazı 'nı çalıştırarak tümünü bir araya getirin. Nihai sonuç, AI işleme tarafından oluşturulan verilerle doldurulan ve portalda ([Arama Gezgini](search-explorer.md)) sorgulayabilmeniz için aranabilir bir dizindir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-services-and-load-data"></a>Hizmet oluşturma ve veri yükleme

Bu hızlı başlangıç, AI için Azure Bilişsel Arama, Azure Blob depolama ve Azure bilişsel [Hizmetler](https://azure.microsoft.com/services/cognitive-services/) 'i kullanır. 

İş yükü çok küçük olduğu için bilişsel hizmetler, Azure Bilişsel Arama çağrıldığında günlük olarak 20 ' ye kadar işlem için ücretsiz işleme sağlamak üzere arka planda dokunmakta. Sağladığımız örnek verileri kullandığınız sürece bilişsel hizmetler kaynağı oluşturma veya ekleme işlemini atlayabilirsiniz.

1. Farklı türlerden oluşan küçük bir dosya kümesini içeren [örnek verileri indirin](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4). Dosyaları sıkıştırmayı açın.

1. Geçerli aboneliğinizde [bir Azure depolama hesabı oluşturun](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) veya [mevcut bir hesabı bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) . 

   Azure Bilişsel Arama ile aynı bölgeyi seçin. Bilgi deposu özelliğini daha sonra denemek istiyorsanız, başka bir yönergede StorageV2 (genel amaçlı v2) hesap türünü seçin. Aksi takdirde herhangi bir tür seçin.

1. Blob Hizmetleri sayfalarını açın ve bir kapsayıcı oluşturun. Varsayılan genel erişim düzeyini kullanabilirsiniz. 

1. Kapsayıcıda, ilk adımda indirdiğiniz örnek dosyaları karşıya yüklemek için **karşıya yükle** ' ye tıklayın. Yerel biçimlerinde aranabilir tam metin olmayan resimler ve uygulama dosyaları dahil olmak üzere çok çeşitli içerik türleri olduğuna dikkat edin.

   ![Azure blob depolamadaki kaynak dosyalar](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Aynı abonelikte [bir Azure bilişsel arama hizmeti oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu hızlı başlangıç için ücretsiz bir hizmet kullanabilirsiniz.

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. In many tutorials, especially those that use the REST API, you will need a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

Artık veri alma Sihirbazı 'na taşımaya hazırsınız.

## <a name="run-the-import-data-wizard"></a>Veri alma Sihirbazı 'nı çalıştırma

Arama hizmeti genel bakış sayfasında, komut çubuğunda **verileri Içeri aktar** ' a tıklayarak Bilişsel Zenginleştirme dört adımda ayarlayın.

  ![Verileri içeri aktar komutu](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>1\. Adım: Veri kaynağı oluşturma

1. **Verilerinize bağlanın**bölümünde **Azure Blob depolama**' yı seçin, oluşturduğunuz depolama hesabını ve kapsayıcıyı seçin. Veri kaynağına bir ad verin ve geri kalanı için varsayılan değerleri kullanın. 

   ![Azure blob yapılandırması](./media/cognitive-search-quickstart-blob/blob-datasource.png)

1. Sonraki sayfaya devam edin.

### <a name="step-2-add-cognitive-skills"></a>2\. Adım: Bilişsel yetenekler ekleme

Sonra doğal dil işlemeyi çağırmak için bilişsel yetenekler ekleyin. Örnek veriler 12 dosyadan oluşur, bu nedenle bilişsel hizmetler üzerinde 20 işlemin ücretsiz servis birimi bu hızlı başlangıç için yeterlidir. OCR kullandığımızda, bu işlemde yalnızca görüntü olmayan dosyalar sayılacak, kırılmamış ve kullanılacaktır.

1. Bu hızlı başlangıçta, **ücretsiz** bilişsel hizmetler kaynağını kullanıyoruz.

   ![Bilişsel Hizmetleri Ekleme](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. **Yetenek Ekle** ' yi genişletin ve doğal dil işleme yapan becerileri seçin. Bu hızlı başlangıç için, kişiler, kuruluşlar ve konumlar için varlık tanımayı seçin.

   ![Bilişsel Hizmetleri Ekleme](media/cognitive-search-quickstart-blob/skillset.png)

1. Varsayılan kaynak alanını kabul et: `content`. Bu küçük bir hedef gibi görünebilir, ancak Azure Blobları için `content` alanı blob belgesinin çoğunu (örneğin, bir Word belgesi veya PowerPoint destesi) içerir ve bunu iyi bir aday hale getirir.

1. Sonraki sayfaya devam edin.

> [!NOTE]
> Doğal dil işleme becerileri, örnek veri kümesindeki metin içeriği üzerinde çalışır. OCR seçeneğini seçmediğinizden, örnek veri kümesinde bulunan JPEG ve PNG dosyaları bu hızlı başlangıçta işlenmez. 

### <a name="step-3-configure-the-index"></a>3\. Adım: Dizini yapılandırma

Azure Bilişsel Arama 'de bir dizin, aranabilir içeriğinizi içerir ve **veri Içeri aktarma** Sihirbazı, genellikle veri kaynağını örnekleyerek şemayı oluşturabilir. Bu adımda, oluşturulan şemayı gözden geçirin ve ayarları olası olarak gözden geçirin. Demo blob veri kümesi için oluşturulan varsayılan şema aşağıda verilmiştir.

Bu hızlı başlangıç, makul varsayılanlar ayarlanması konusunda iyi bir iş çıkarır: 

+ Varsayılan ad, veri kaynağı türüne göre *azureblob-index* ' dir. 

+ Varsayılan alanlar, özgün kaynak veri alanı (`content`) ve bilişsel yetenekler tarafından oluşturulan çıkış alanlarını (`people`, `organizations`ve `locations`) temel alır. Varsayılan veri türleri meta verilerden ve veri örneklemede algılanır.

+ Varsayılan belge anahtarı *metadata_storage_path* (alan benzersiz değerler içerdiğinden seçilidir).

+ Varsayılan öznitelikler **alınabilir** ve bu alanlar için **aranabilir** . **Aranabilir** bir alanın aranamayacağını gösterir. **Alınabilir** , sonuçlarda döndürülebilecek anlamına gelir. Sihirbaz, bir beceri aracılığıyla oluşturduğunuz için bu alanların alınabilir ve aranabilir olmasını istediğiniz varsayılır.

  ![Dizin alanları](media/cognitive-search-quickstart-blob/index-fields.png)

`content` alanına göre **alınabilir** özniteliğinde üstü çizili ve soru işaretine dikkat edin. Metin açısından ağır blob belgeleri için `content` alanı, büyük olasılıkla binlerce satıra çalışan dosyanın toplu kısmını içerir. Dosya içeriğini istemci koduna geçirmeniz gerekirse, **alınabilir** ' ın seçili olduğundan emin olun. Aksi takdirde, ayıklanan öğeler (`people`, `organizations`ve `locations`) amacınıza uygun olduğunda bu özniteliği `content` temizlemeyi düşünün.

Bir alanı **alınabilir** olarak işaretlemek alanın arama sonuçlarında bulunması *gerektiği* anlamına gelmez. Hangi alanların ekleneceğini belirlemek için **$Select** sorgu parametresini kullanarak arama sonuçları kompozisyonunu kesin bir şekilde denetleyebilirsiniz. `content`gibi metin açısından ağır alanlar için, **$Select** parametresi, uygulamanızın insan kullanıcılarına yönetilebilir arama sonuçları sağlamaya yönelik çözümünüzde, Istemci kodunun **alınabilir** öznitelik aracılığıyla ihtiyaç duyacağı tüm bilgilere erişmesine olanak sağlar.
  
Sonraki sayfaya devam edin.

### <a name="step-4-configure-the-indexer"></a>4\. Adım: Dizin oluşturucuyu yapılandırma

Dizin oluşturucu, dizin oluşturma işlemini destekleyen, yüksek düzeyli bir kaynaktır. Veri kaynağı adı, hedef dizin ve yürütme sıklığını belirtir. **Verileri Içeri aktarma** Sihirbazı birçok nesne oluşturur ve bu her zaman sürekli çalıştırabileceğiniz bir dizin oluşturucudır.

1. **Dizin Oluşturucu** sayfasında, varsayılan adı kabul edebilir ve hemen çalıştırmak Için **bir kez** zamanlama seçeneğine tıklayabilirsiniz. 

   ![Dizin oluşturucu tanımı](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Dizin oluşturucuyu oluşturmak ve aynı anda çalıştırmak için **Gönder** ' e tıklayın.

## <a name="monitor-status"></a>İzleme durumu

Bilişsel yetenekler dizininin oluşturulması, tipik metin tabanlı dizin oluşturma işleminin daha uzun sürer. İlerlemeyi izlemek için genel bakış sayfasına gidin ve sayfanın ortasında **Dizin oluşturucular** ' ye tıklayın.

Uyarı, JPG ve PNG resim dosyalarının veri kaynağında olduğu ve bu işlem hattından OCR becerinizi atdığımız için oluşur. Ayrıca, kesme bildirimleri de bulacaksınız. Ayıklama, ücretsiz katmanda 32.000 karakterle sınırlıdır.

  ![Azure Bilişsel Arama bildirimi](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Dizin oluşturma ve zenginleştirme zaman alabilir; ilk keşif için daha küçük veri kümelerinin önerilmesinin nedeni budur. 

Azure portal, tıklatılabilir bir **Azure bilişsel arama bildirim** durumu bağlantısı için bildirimler etkinlik günlüğünü de izleyebilirsiniz. Yürütmenin tamamlanması birkaç dakika sürebilir.

## <a name="query-in-search-explorer"></a>Arama gezgininde sorgulama

Bir dizin oluşturulduktan sonra, dizinden belgeleri döndürmek için sorgular gönderebilirsiniz. Portalda **Arama gezgini**’ni kullanarak sorgular çalıştırın ve sonuçları görüntüleyin. 

1. Arama hizmeti panosu sayfasında, komut çubuğunda **Arama gezgini**’ne tıklayın.

1. Oluşturduğunuz dizini seçmek için üst kısımdaki **Dizini değiştir**'e tıklayın.

1. Dizini sorgulamak için `search=Microsoft&searchFields=Organizations`gibi bir arama dizesi girin.

Sonuçlar JSON olarak döndürülür, bu nedenle özellikle de Azure bloblardan gelen büyük belgelerde ayrıntılı ve okuması zor olabilir. Sonuçları kolayca tarayamazsanız, belgeler içinde arama yapmak için CTRL-F tuşlarını kullanın. Bu sorgu için, JSON içinde belirli koşullara göre arama yapabilirsiniz. 

CTRL-F tuş birleşimi, belirli bir sonuç kümesinde kaç tane belgenin bulunduğunu belirlemenize de yardımcı olabilir. Her değer belge için benzersiz olduğundan Azure blobları için portal, anahtar olarak "metadata_storage_path" öğesini seçer. CTRL-F kullanarak, "metadata_storage_path" araması yapıp belgelerin sayısını alın. 

  ![Arama gezgini örneği](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Paketler

Şimdi ilk beceri oluşturdunuz ve kendi verilerinizi kullanarak zenginleştirilmiş bir arama çözümünü prototip oluşturma konusunda yararlı olan öğrendiğimiz önemli kavramlar.

Topladığınız bazı temel kavramlar, Azure veri kaynaklarına bağımlılık içerir. Bir beceri bir dizin oluşturucuya bağlanır ve Dizin oluşturucular Azure ve kaynağa özgüdür. Bu hızlı başlangıçta Azure Blob depolama kullanılsa da, başka Azure veri kaynakları da mümkündür. Daha fazla bilgi için bkz. [Azure bilişsel arama 'Da Dizin oluşturucular](search-indexer-overview.md).

Başka bir önemli kavram da, becerilerin giriş alanları üzerinde çalışmasıdır. Portalda, tüm yetenekler için tek kaynak alan seçmeniz gerekir. Kodda girişler, başka alanlar olabilir veya bir yukarı akış becerisinin çıktısı olabilir.

Çıktı bir arama dizinine yönlendirilir ve dizininizdeki tek tek alanlar sırasında oluşturulan ad-değer çiftleri arasında bir eşleme vardır. Dahili olarak portal, [ek açıklamalar](cognitive-search-concept-annotations-syntax.md) ayarlar ve işlemlerin sırasını ve genel akışı oluşturan bir [beceri kümesini](cognitive-search-defining-skillset.md) tanımlar. Bu adımlar portalda gizlenir, ancak kod yazmaya başladığınızda bu kavramlar önemli hale gelir.

Son olarak, dizini sorgulayarak içeriği doğrulayabildiğini öğrendiniz. Son olarak, Azure Bilişsel Arama 'in sağladığı, [basit](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) veya [tam genişletilmiş sorgu söz dizimini](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)kullanarak sorgulayabilmeniz için aranabilir bir dizindir. Zenginleştirilmiş alanlar içeren bir dizin de diğerlerine benzer. Standart veya [özel çözümleyiciler](search-analyzers.md), [Puanlama profilleri](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [eş anlamlı](search-synonyms.md), çok [yönlü filtreler](search-filters-facets.md), coğrafi arama veya diğer Azure bilişsel arama özelliklerini birleştirmek istiyorsanız, kesinlikle bunu yapabilirsiniz.

## <a name="clean-up"></a>Temizleme

Kendi aboneliğinizde çalışırken, sizin oluşturduğunuz kaynaklara hala ihtiyacınız olup olmadığını belirlemek için bir projenin sonunda iyi bir fikir olur. Çalışan kaynaklar sizin için ücret verebilir. Kaynakları tek tek silebilir veya kaynak grubunu silerek tüm kaynak kümesini silebilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Portal, .NET SDK veya REST API kullanarak becerileri oluşturabilirsiniz. Daha ayrıntılı bilgi için Postman ve daha fazla örnek veri kullanarak REST API deneyin.

> [!div class="nextstepaction"]
> [Öğretici: AI zenginleştirme ile "yapılandırılmamış içerik" yapısına yapı ekleme](cognitive-search-tutorial-blob.md)

> [!Tip]
> Bu alıştırmayı yinelemek veya farklı bir AI zenginleştirme Kılavuzu denemek istiyorsanız, portaldaki Dizin oluşturucuyu silin. Dizin oluşturucunun silinmesi, bilişsel hizmetler işleme için ücretsiz günlük işlem sayacını yeniden sıfır olarak sıfırlar.