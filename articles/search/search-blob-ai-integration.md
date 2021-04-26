---
title: Blob içeriğini zenginleştirmek için AI kullanma
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de doğal dil ve görüntü analizi özellikleri ve bu işlemlerin Azure Blob 'larında depolanan içeriklere nasıl uygulandığı hakkında bilgi edinin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 3d427d80e502eed0825165e640acc0755515c5b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99222057"
---
# <a name="use-ai-to-process-and-analyze-blob-content-in-azure-cognitive-search"></a>Azure Bilişsel Arama blob içeriğini işlemek ve analiz etmek için AI kullanma

Resimlerin veya uzun bir şekilde değişmeyen metinden oluşan Azure Blob depolamada bulunan içerikler, aşağı akış uygulamaları için faydalı bilgileri açığa çıkarmak ve ayıklamak üzere derin öğrenme analizinden geçebilir. [AI zenginleştirme](cognitive-search-concept-intro.md)kullanarak şunları yapabilirsiniz:

+ Optik karakter tanıma özelliğini kullanarak görüntülerden metin ayıklama (OCR)
+ Fotoğraftan bir sahne açıklaması veya etiketleri üretme
+ Dili algıla ve metni farklı dillere çevir
+ Kişilere, tarihlere, yerlere veya kuruluşlara yönelik başvuruları bularak yapıyı varlık tanıma aracılığıyla çıkarçıkar

Bu AI özelliklerinden yalnızca birine ihtiyacınız olabilir, ancak birden çok aynı işlem hattına (örneğin, taranmış bir görüntüden metin ayıklanarak ve bu konumda başvurulan tüm tarihleri ve yerleri buluyor) daha yaygın olarak vardır. Ayrıca, önde gelen kenar harici paketleri veya verilerinize ve gereksinimlerinize uygun şirket içi modellere göre özel AI veya makine öğrenimi işlemesini dahil etmek de yaygındır.

Bir arama Dizin Oluşturucu tarafından desteklenen herhangi bir veri kaynağına AI zenginleştirme uygulayabilseniz de, Bloblar bir enzenginleştirme ardışık düzeninde en sık kullanılan yapılardır. Sonuçlar, tam metin araması için bir arama dizinine çekilir veya bulma veya analiz senaryoları için verileri araştırmayı içeren yeni uygulama deneyimlerine Power Storage 'a yeniden yönlendirilir. 

Bu makalede, Bloblar içindeki ham verileri bir arama dizininde veya bilgi deposunda sorgulanabilir bilgilere dönüştürmek için, tüm süreci hızlı bir şekilde bir lens aracılığıyla bir çok lens aracılığıyla inceleyeceğiz.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>AI ile "zenginleştirme" blobu verilerinin ne anlama geldiğini

*AI zenginleştirme* , Microsoft 'tan veya sağladığınız özel öğrenme modellerinden makine öğrenimi modellerini tümleştiren Azure bilişsel arama 'in dizin oluşturma mimarisinin bir parçasıdır. Blob 'ları (varsa veya güncelleştirdikleri gibi mevcut olanları ve yeni olanları) işlemek için tüm dosya biçimlerini açmak, çeşitli AI yeteneklerini kullanarak istenen bilgileri ayıklamak ve hızlı arama, alma ve keşfetme için bir arama dizininde dizin oluşturmanız gereken uçtan uca senaryolar uygulamanıza yardımcı olur. 

Azure Blob depolama alanında, tek bir kapsayıcıda bulunan bloblarınızın girdileri vardır. Blob 'lar neredeyse her türlü metin veya resim verisi olabilir. 

Çıktı, istemci uygulamalarında hızlı metin arama, alma ve araştırma için kullanılan her zaman bir arama dizinidir. Ayrıca, çıktı Ayrıca, belgeleri Azure Blob 'larına veya Power BI ya da veri bilimi iş yükleri gibi araçlarla aşağı akış analizi için Azure tablolarına zenginleştiren bir [*bilgi deposu*](knowledge-store-concept-intro.md) da olabilir.

Between, ardışık düzen mimarisinin kendisidir. İşlem hattı, AI sağlayan bir veya daha fazla *becerilerden* oluşan bir [*beceri*](cognitive-search-working-with-skillsets.md)atayabileceğiniz [*Dizin oluşturuculara*](search-indexer-overview.md)dayalıdır. İşlem hattının amacı, ardışık düzeni ham içerik olarak girebileceğiniz ancak ardışık düzen boyunca geçiş yaparken ek yapıyı, bağlamı ve bilgileri alan, *zenginleştirilmiş belgeler* oluşturmak için kullanılır. Zenginleştirilmiş belgeler, tam metin aramasında veya araştırmayla ve analizte kullanılan ters dizinler ve diğer yapılar oluşturmak için dizin oluşturma sırasında tüketilecektir.

## <a name="required-resources"></a>Gerekli kaynaklar

Azure Blob depolama ve Azure Bilişsel Arama 'nin yanı sıra AI sağlayan üçüncü bir hizmet veya mekanizmaya ihtiyacınız vardır:

+ Yerleşik AI için Bilişsel Arama Azure bilişsel hizmetler Vision ve doğal dil işleme API 'Leri ile tümleşir. Optik karakter tanıma (OCR), görüntü analizi veya doğal dil işleme (dil algılama, metin çevirisi, varlık tanıma, anahtar tümceciği ayıklama) eklemek için bilişsel [Hizmetler kaynağı](cognitive-search-attach-cognitive-services.md) ekleyebilirsiniz. 

+ Azure kaynaklarını kullanan özel AI için, kullanmak istediğiniz dış işlevi veya modeli sarmalayan özel bir yetenek tanımlayabilirsiniz. [Özel yetenekler](cognitive-search-custom-skill-interface.md) , Azure işlevleri, Azure Machine Learning, Azure form TANıYıCı veya https üzerinden erişilebilen başka bir kaynak tarafından sunulan kodu kullanabilir.

+ Özel Azure dışı AI için, modelinize veya modülünüzün HTTP üzerinden bir Dizin Oluşturucu tarafından erişilebilir olması gerekir.

Tüm hizmetleri kullanıma hazır değilse, doğrudan depolama hesabı portalı sayfanızda başlatın. Sol Gezinti sayfasında, **BLOB hizmeti** altında **Azure bilişsel arama Ekle** ' ye tıklayarak yeni bir hizmet oluşturun veya var olan bir hizmeti seçin. 

Depolama hesabınıza Azure Bilişsel Arama eklediğinizde, herhangi bir Azure veri kaynağındaki verileri zenginleştirmek için standart işlemi izleyebilirsiniz. AI zenginleştirme 'ya kolay bir başlangıç için Azure Bilişsel Arama **veri alma** Sihirbazı 'nı öneririz. İş akışı sırasında bilişsel hizmetler kaynağı iliştirebilirsiniz. Bu hızlı başlangıçta, [portalda BIR AI zenginleştirme işlem hattı oluşturma](cognitive-search-quickstart-blob.md)adımları anlatılmaktadır. 

Aşağıdaki bölümler bileşenlere ve iş akışına daha yakından göz atalım.

## <a name="use-a-blob-indexer"></a>Blob Dizin Oluşturucu kullanma

AI zenginleştirme, dizin oluşturma işlem hattının bir eklentisi ve Azure Bilişsel Arama, bu işlem hatları bir *dizin oluşturucunun* üzerine kurulmuştur. Dizin Oluşturucu, verileri örnekleme, meta veri verileri okuma, verileri alma ve yerel biçimlerdeki verileri, sonraki içeri aktarma için JSON belgelerine serileştirmede iç mantığa sahip olan veri kaynağı kullanan bir alt hizmettir. Dizin oluşturucular genellikle AI tarafından içeri aktarma için kullanılır, ancak bir AI zenginleştirme işlem hattı oluşturmak istiyorsanız, bir Dizin Oluşturucu ve bir beceri ile birlikte çalışmak için bir gerekir. Bu bölüm, Dizin oluşturucuyu vurgular; sonraki bölüm becerileri 'e odaklanır.

Azure depolama 'daki Bloblar, [BLOB Dizin Oluşturucu](search-howto-indexing-azure-blob-storage.md)kullanılarak dizine alınır. Bu dizin oluşturucuyu, **verileri Içeri aktarma** Sihirbazı 'nı, bir REST API veya bir SDK 'yı kullanarak çağırabilirsiniz. Dizin Oluşturucu tarafından kullanılan veri kaynağı bir Azure Blob kapsayıcısıdır blob Indexer çağrılır. Bir sanal dizin oluşturarak bloblarınızın bir alt kümesini dizinleyerek bir parametre olarak geçirebilmeniz veya bir dosya türü uzantısı üzerinde filtreleyerek.

Bir Dizin Oluşturucu, içeriği incelemek için bir blob açan "belgeyi çözme" yapmaz. Veri kaynağına bağlandıktan sonra, işlem hattının ilk adımı vardır. Blob verileri için PDF, Office belgeleri, görüntü ve diğer içerik türlerinin algılandığı yerdir. Metin ayıklama ile belge çözme ücretsizdir. Görüntü ayıklama ile belge çözme, [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/search/)bulabileceğiniz oranlar üzerinden ücretlendirilir.

Tüm belgelerin kırdığı halde, zenginleştirme yalnızca açıkça bunu yapmak için becerileri sağladığınızda oluşur. Örneğin, işlem hattınız yalnızca görüntü analizini içeriyorsa, kapsayıcıınızdaki veya belgelerinizdeki metin yok sayılır.

Blob Indexer yapılandırma parametreleriyle birlikte gelir ve temel alınan veriler yeterli bilgi sağlıyorsa değişiklik izlemeyi destekler. [BLOB Dizin oluşturucuyu yapılandırma hakkında](search-howto-indexing-azure-blob-storage.md)daha fazla bilgi edinebilirsiniz.

## <a name="add-ai-components"></a>AI bileşenleri ekleme

AI zenginleştirme, desenleri veya özellikleri belirten modüller anlamına gelir ve ardından bir işlem gerçekleştirir. Fotoğraflarda yüz tanıma, fotoğrafların metin açıklamaları, bir belgedeki anahtar tümceleri algılama ve OCR (ya da ikili dosyalardaki yazdırılmış veya el ile yazılmış metinleri tanıma) tanım örnekleri verilmiştir.

Azure Bilişsel Arama, *beceriler* tek başına kullanabileceğiniz veya diğer becerilerle birlikte tek tek tekil AI işleme bileşenleridir. 

+ Yerleşik yetenekler bilişsel hizmetler tarafından desteklenir, Görüntü İşleme dayalı görüntü analizi ve Metin Analizi temelinde doğal dil işleme. Tam liste için bkz. [içerik zenginleştirme Için yerleşik yetenekler](cognitive-search-predefined-skills.md).

+ Özel yetenekler, işlem hattına tümleştirmeye izin veren bir [arabirim tanımına](cognitive-search-custom-skill-interface.md) Sarmalanan özel koddur. Müşteri çözümlerinde, açık kaynaklı, üçüncü taraf veya birinci taraf AI modülleri sağlayan özel yetenekler ile her ikisini de kullanmak yaygın bir uygulamadır.

*Beceri* , bir işlem hattında kullanılan yeteneklerin koleksiyonudur ve belge çözme aşaması içeriği kullanılabilir hale getirdikten sonra çağrılır. Bir Dizin Oluşturucu tam olarak bir beceri tüketebilir, ancak diğer senaryolarda yeniden kullanabilmeniz için bu beceri bir dizin oluşturucudan bağımsız olarak mevcuttur.

Özel yetenekler karmaşık olabilir, ancak uygulama bakımından basit ve kolay olabilir. Model eşleştirme veya sınıflandırma modelleri sağlayan mevcut Paketleriniz varsa, bloblardan ayıklamanız gereken içerik işlenmek üzere bu modellere geçirilebilir. AI zenginleştirme, Azure tabanlı olduğundan modelinize Azure 'da da sahip olmanız gerekir. Bazı yaygın barındırma yöntemleri [Azure işlevleri](cognitive-search-create-custom-skill-example.md) veya [kapsayıcıları](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)kullanmayı içerir.

Bilişsel hizmetler tarafından desteklenen yerleşik yetenekler, kaynağa erişmenizi sağlayan, bağlı bilişsel [hizmetlerin](cognitive-search-attach-cognitive-services.md) hepsi bir arada bir abonelik anahtarı gerektirir. Hepsi bir arada anahtar, görüntü analizi, dil algılama, metin çevirisi ve metin analizi sağlar. Diğer yerleşik yetenekler Azure Bilişsel Arama özelliklerdir ve ek hizmet veya anahtar gerektirmez. Metin Shaper, Splitter ve birleşme, işlem hattı tasarlarken bazen gerekli olan yardımcı beceriler örnekleridir.

Yalnızca özel yetenekler ve yerleşik yardımcı program becerileri kullanıyorsanız, bilişsel hizmetlerle ilgili bağımlılık veya maliyet yoktur.

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Aşağı akış çözümlerinde AI zenginleştirme çıkışı kullanma

AI zenginleştirme çıktısı, Azure Bilişsel Arama 'deki bir arama dizinidir veya Azure Storage 'daki bir [bilgi deposudur](knowledge-store-concept-intro.md) .

Azure Bilişsel Arama 'de, bir istemci uygulamasında ücretsiz metin ve filtrelenmiş sorgular kullanan etkileşimli araştırma için bir arama dizini kullanılır. AI aracılığıyla oluşturulan zenginleştirilmiş belgeler, bir dizin oluşturucunun sağladığı avantajlardan yararlanarak Azure Bilişsel Arama 'de tüm belgelerin dizinlendiği şekilde JSON ve dizinli olarak biçimlendirilir. Örneğin, dizin oluşturma sırasında, blob Indexer herhangi bir alan eşlemelerini kullanmak veya algılama mantığını değiştirmek için yapılandırma parametreleri ve ayarları anlamına gelir. Bu ayarlar, normal dizin oluşturma ve yapay zeka zenginleştirilmiş iş yükleri için tamamen kullanılabilir. Dizin oluşturma sonrası, içerik Azure Bilişsel Arama 'da depolandığında, içeriğinizi anlamak için zengin sorgular ve filtre ifadeleri oluşturabilirsiniz.

Azure depolama 'da bir bilgi deposunda iki bildirim bulunur: bir blob kapsayıcısı veya tablo depolamadaki tablolar. 

+ Blob kapsayıcısı, zenginleştirilmiş belgeleri tamamen yakalar ve diğer işlemlere akış yapmak istiyorsanız yararlı olur. 

+ Buna karşılık tablo depolama, zenginleştirilmiş belgelerin fiziksel projeksiyonlarını kapsayabilmelidir. Belirli kısımları dahil eden veya hariç tutkaya ait belgelerin dilimlerini veya katmanlarını oluşturabilirsiniz. Power BI analize yönelik olarak, Azure Tablo depolamadaki tablolar, daha fazla görselleştirme ve araştırma için veri kaynağı haline gelir.

İşlem hattının sonunda zenginleştirilmiş bir belge, zenginleştirme sırasında ayıklanan veya oluşturulan yeni bilgileri içeren ek alanların varlığına göre orijinal giriş sürümünden farklıdır. Bu nedenle, kullandığınız çıkış yapısını ne olursa olsun, orijinal ve oluşturulmuş içeriğin bir bileşimiyle çalışabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Farklı yollarla bilişsel hizmetler 'i birleştirme ve senaryo için mevcut bilişsel hizmet olmadığı durumlar için özel yetenekler yazma dahil olmak üzere, Azure depolama 'daki verilerden en iyi şekilde yararlanmak için, AI zenginleştirmesiyle ilgili çok daha fazla şey vardır. Aşağıdaki bağlantıları izleyerek daha fazla bilgi edinebilirsiniz.

+ [Azure portal (Azure Blob depolama) ile Blobları karşıya yükleme, indirme ve listeleme](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Blob Dizin oluşturucuyu ayarlama (Azure Bilişsel Arama)](search-howto-indexing-azure-blob-storage.md) 
+ [AI zenginleştirme genel bakış (Azure Bilişsel Arama)](cognitive-search-concept-intro.md) 
+ [Beceri oluşturma (Azure Bilişsel Arama)](cognitive-search-defining-skillset.md)
+ [Ek açıklama ağacındaki düğümleri eşleme (Azure Bilişsel Arama)](cognitive-search-output-field-mapping.md)