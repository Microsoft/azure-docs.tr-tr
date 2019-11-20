---
title: BLOB depolama verilerini anlamak için AI kullanma
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de bir AI zenginleştirme işlem hattı kullanarak Azure bloblarına anlam, doğal dil işleme ve görüntü analizi ekleyin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 591437eb3951164d53388b6164103948e9ad65e0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496429"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>BLOB depolama verilerini anlamak için AI kullanma

Azure Blob depolamada bulunan veriler genellikle görüntüler, uzun metin, PDF 'Ler ve Office belgeleri gibi yapılandırılmamış içerik olan çok çeşitli içeriktir. Azure Bilişsel Arama 'de AI özelliklerini kullanarak, çeşitli yollarla bloblardan değerli bilgileri anlayabilir ve ayıklayabilirsiniz. Blob içeriğine AI uygulama örnekleri şunlardır:

+ Optik karakter tanıma özelliğini kullanarak görüntülerden metin ayıklama (OCR)
+ Fotoğraftan bir sahne açıklaması veya etiketleri üretme
+ Dili algıla ve metni farklı dillere çevir
+ Kişilere, tarihlere, yerlere veya kuruluşlara yönelik başvuruları bulmak için adlandırılmış varlık tanıma (NER) ile metin işleme 

Bu AI özelliklerinden yalnızca birine ihtiyacınız olabilir, ancak birden çok aynı işlem hattına (örneğin, taranmış bir görüntüden metin ayıklanarak ve bu konumda başvurulan tüm tarihleri ve yerleri buluyor) daha yaygın olarak vardır. 

AI zenginleştirme, metin olarak yakalanan ve alanlarda depolanan yeni bilgiler oluşturur. Son zenginleştirme, bu bilgilere tam metin araması aracılığıyla bir arama dizininden erişebilirsiniz veya bulma veya analiz senaryoları için verileri araştırmayı kapsayan yeni uygulama deneyimlerine Power Storage 'a zenginleştirilmiş belgeler gönderebilirsiniz. 

Bu makalede, Bloblar içindeki ham verileri bir arama dizininde veya bilgi deposunda sorgulanabilir bilgilere dönüştürmek için, tüm süreci hızlı bir şekilde bir lens aracılığıyla bir çok lens aracılığıyla inceleyeceğiz.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>AI ile "zenginleştirme" blobu verilerinin ne anlama geldiğini

*AI zenginleştirme* , Microsoft 'un veya SAĞLADıĞıNıZ özel AI 'nın yerleşik AI 'Ni tümleştiren Azure bilişsel arama 'in dizin oluşturma mimarisinin bir parçasıdır. Blob 'ları (varsa veya güncelleştirdikleri gibi mevcut olanları ve yeni olanları) işlemek için tüm dosya biçimlerini açmak, çeşitli AI yeteneklerini kullanarak istenen bilgileri ayıklamak, ve ' yi hızlı arama, alma ve araştırma için bir arama dizininde dizin oluşturma. 

Azure Blob depolama alanında, tek bir kapsayıcıda bulunan bloblarınızın girdileri vardır. Blob 'lar neredeyse her türlü metin veya resim verisi olabilir. 

Çıktı, istemci uygulamalarında hızlı metin arama, alma ve araştırma için kullanılan her zaman bir arama dizinidir. Ayrıca, çıktı Ayrıca, belgeleri Azure Blob 'larına veya Power BI ya da veri bilimi iş yükleri gibi araçlarla aşağı akış analizi için Azure tablolarına zenginleştiren bir *bilgi deposu* da olabilir.

Between, ardışık düzen mimarisinin kendisidir. İşlem hattı, AI sağlayan bir veya daha fazla *beceriden* oluşan bir *beceri*atayabileceğiniz *Dizin Oluşturucu* özelliğini temel alır. İşlem hattının amacı, ham içerik olarak girebileceğiniz ancak ardışık düzen boyunca geçiş yaparken ek yapıyı, bağlamı ve bilgileri alan, *zenginleştirilmiş belgeler* oluşturmak için kullanılır. Zenginleştirilmiş belgeler, tam metin aramasında veya araştırmayla ve analizte kullanılan ters dizinler ve diğer yapılar oluşturmak için dizin oluşturma sırasında tüketilecektir.

## <a name="start-with-services"></a>Hizmetlerle başlayın

Azure Bilişsel Arama ve Azure Blob depolamaya ihtiyacınız vardır. BLOB depolama alanında, kaynak içerik sağlayan bir kapsayıcıya ihtiyacınız vardır.

Doğrudan depolama hesabı portalı sayfanızda başlayabilirsiniz. Sol Gezinti sayfasında, **BLOB hizmeti** altında **Azure bilişsel arama Ekle** ' ye tıklayarak yeni bir hizmet oluşturun veya var olan bir hizmeti seçin. 

Depolama hesabınıza Azure Bilişsel Arama eklediğinizde, herhangi bir Azure veri kaynağındaki verileri zenginleştirmek için standart işlemi izleyebilirsiniz. AI zenginleştirme 'ya kolay bir başlangıç için Azure Bilişsel Arama **veri alma** Sihirbazı 'nı öneririz. Bu hızlı başlangıçta, [portalda BIR AI zenginleştirme işlem hattı oluşturma](cognitive-search-quickstart-blob.md)adımları anlatılmaktadır. 

Aşağıdaki bölümlerde, daha fazla bileşen ve kavram keşfedeceğiz.

## <a name="use-a-blob-indexer"></a>Blob Dizin Oluşturucu kullanma

AI zenginleştirme, dizin oluşturma işlem hattının bir eklentisi ve Azure Bilişsel Arama, bu işlem hatları bir *dizin oluşturucunun*üzerine kurulmuştur. Dizin Oluşturucu, verileri örnekleme, meta veri verileri okuma, verileri alma ve yerel biçimlerdeki verileri, sonraki içeri aktarma için JSON belgelerine serileştirmede iç mantığa sahip olan veri kaynağı kullanan bir alt hizmettir. Dizin oluşturucular genellikle AI tarafından içeri aktarma için kullanılır, ancak bir AI zenginleştirme işlem hattı oluşturmak istiyorsanız, bir Dizin Oluşturucu ve bir beceri ile birlikte çalışmak için bir gerekir. Bu bölüm, Dizin oluşturucuyu vurgular; sonraki bölüm becerileri 'e odaklanır.

Azure depolama 'daki Bloblar, [azure bilişsel arama blob Storage Indexer](search-howto-indexing-azure-blob-storage.md)kullanılarak dizine alınır. **Veri alma** Sihirbazı 'nı, bir REST API veya .NET SDK 'sını kullanarak bu dizin oluşturucuyu çağırabilirsiniz. Kod içinde, bu dizin oluşturucuyu türü ayarlayarak ve bir blob kapsayıcısı ile birlikte bir Azure depolama hesabı içeren bağlantı bilgilerini sağlayarak kullanırsınız. Daha sonra bir parametre olarak geçirebilen veya bir dosya türü uzantısı üzerinde filtreleyerek sanal bir dizin oluşturarak bloblarınızı alt kümelayabilirsiniz.

Bir Dizin Oluşturucu, içeriği incelemek için bir blob açan "belgeyi çözme" yapmaz. Veri kaynağına bağlandıktan sonra, işlem hattının ilk adımı vardır. Blob verileri için PDF, Office belgeleri, görüntü ve diğer içerik türlerinin algılandığı yerdir. Metin ayıklama ile belge çözme ücretsizdir. Görüntü ayıklama ile belge çözme, [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/search/)bulabileceğiniz oranlar üzerinden ücretlendirilir.

Tüm belgelerin kırdığı halde, zenginleştirme yalnızca açıkça bunu yapmak için becerileri sağladığınızda oluşur. Örneğin, işlem hattınız yalnızca görüntü analizini içeriyorsa, kapsayıcıınızdaki veya belgelerinizdeki metin yok sayılır.

Blob Indexer yapılandırma parametreleriyle birlikte gelir ve temel alınan veriler yeterli bilgi sağlıyorsa değişiklik izlemeyi destekler. [Azure bilişsel arama blob Storage Indexer](search-howto-indexing-azure-blob-storage.md)'ın temel işlevleri hakkında daha fazla bilgi edinebilirsiniz.

## <a name="add-ai-components"></a>AI bileşenleri ekleme

AI zenginleştirme, desenleri veya özellikleri belirten modüller anlamına gelir ve sonra bir işlemi buna göre gerçekleştirir. Fotoğraflarda yüz tanıma, fotoğrafların metin açıklamaları, bir belgedeki anahtar tümceleri algılama ve OCR (ya da ikili dosyalardaki yazdırılmış veya el ile yazılmış metinleri tanıma) tanım örnekleri verilmiştir.

Azure Bilişsel Arama, *beceriler* tek başına kullanabileceğiniz veya diğer becerilerle birlikte tek tek tekil AI işleme bileşenleridir. 

+ Yerleşik yetenekler bilişsel hizmetler tarafından desteklenir, Görüntü İşleme dayalı görüntü analizi ve Metin Analizi temelinde doğal dil işleme. Tam liste için bkz. [içerik zenginleştirme Için yerleşik yetenekler](cognitive-search-predefined-skills.md).

+ Özel yetenekler, işlem hattına tümleştirmeye izin veren bir [arabirim tanımına](cognitive-search-custom-skill-interface.md) Sarmalanan özel koddur. Müşteri çözümlerinde, açık kaynaklı, üçüncü taraf veya birinci taraf AI modülleri sağlayan özel yetenekler ile her ikisini de kullanmak yaygın bir uygulamadır.

*Beceri* , bir işlem hattında kullanılan yeteneklerin koleksiyonudur ve belge çözme aşaması içeriği kullanılabilir hale getirdikten sonra çağrılır. Bir Dizin Oluşturucu tam olarak bir beceri tüketebilir, ancak diğer senaryolarda yeniden kullanabilmeniz için bu beceri bir dizin oluşturucudan bağımsız olarak mevcuttur.

Özel yetenekler karmaşık olabilir, ancak uygulama bakımından basit ve kolay olabilir. Model eşleştirme veya sınıflandırma modelleri sağlayan mevcut Paketleriniz varsa, bloblardan ayıklamanız gereken içerik işlenmek üzere bu modellere geçirilebilir. AI zenginleştirme, Azure tabanlı olduğundan modelinize Azure 'da da sahip olmanız gerekir. Bazı yaygın barındırma yöntemleri [Azure işlevleri](cognitive-search-create-custom-skill-example.md) veya [kapsayıcıları](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)kullanmayı içerir.

Bilişsel hizmetler tarafından desteklenen yerleşik yetenekler, kaynağa erişmenizi sağlayan, bağlı bilişsel [hizmetlerin](cognitive-search-attach-cognitive-services.md) hepsi bir arada bir abonelik anahtarı gerektirir. Hepsi bir arada anahtar, görüntü analizi, dil algılama, metin çevirisi ve metin analizi sağlar. Diğer yerleşik yetenekler Azure Bilişsel Arama özelliklerdir ve ek hizmet veya anahtar gerektirmez. Metin Shaper, Splitter ve birleşme, işlem hattı tasarlarken bazen gerekli olan yardımcı beceriler örnekleridir.

Yalnızca özel yetenekler ve yerleşik yardımcı program becerileri kullanıyorsanız, bilişsel hizmetlerle ilgili bağımlılık veya maliyet yoktur.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Aşağı akış çözümlerinde AI zenginleştirme çıkışı kullanma

AI zenginleştirme çıktısı, Azure Bilişsel Arama 'deki bir arama dizinidir veya Azure Storage 'daki bir [bilgi deposudur](knowledge-store-concept-intro.md) .

Azure Bilişsel Arama 'de, bir istemci uygulamasında ücretsiz metin ve filtrelenmiş sorgular kullanan etkileşimli araştırma için bir arama dizini kullanılır. AI aracılığıyla oluşturulan zenginleştirilmiş belgeler, bir dizin oluşturucunun sağladığı avantajlardan yararlanarak Azure Bilişsel Arama 'de tüm belgelerin dizinlendiği şekilde JSON ve dizinli olarak biçimlendirilir. Örneğin, dizin oluşturma sırasında, blob Indexer herhangi bir alan eşlemelerini kullanmak veya algılama mantığını değiştirmek için yapılandırma parametreleri ve ayarları anlamına gelir. Bu ayarlar, normal dizin oluşturma ve yapay zeka zenginleştirilmiş iş yükleri için tamamen kullanılabilir. Dizin oluşturma sonrası, içerik Azure Bilişsel Arama 'da depolandığında, içeriğinizi anlamak için zengin sorgular ve filtre ifadeleri oluşturabilirsiniz.

Azure depolama 'da bir bilgi deposunda iki bildirim bulunur: bir blob kapsayıcısı veya tablo depolamadaki tablolar. 

+ Blob kapsayıcısı, zenginleştirilmiş belgeleri tamamen yakalar ve diğer işlemlere akış yapmak istiyorsanız yararlı olur. 

+ Buna karşılık tablo depolama, zenginleştirilmiş belgelerin fiziksel projeksiyonlarını kapsayabilmelidir. Belirli kısımları dahil eden veya hariç tutkaya ait belgelerin dilimlerini veya katmanlarını oluşturabilirsiniz. Power BI analize yönelik olarak, Azure Tablo depolamadaki tablolar, daha fazla görselleştirme ve araştırma için veri kaynağı haline gelir.

İşlem hattının sonunda zenginleştirilmiş bir belge, zenginleştirme sırasında ayıklanan veya oluşturulan yeni bilgileri içeren ek alanların varlığına göre orijinal giriş sürümünden farklıdır. Bu nedenle, kullandığınız çıkış yapısını ne olursa olsun, orijinal ve oluşturulmuş içeriğin bir bileşimiyle çalışabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Farklı yollarla bilişsel hizmetler 'i birleştirme ve senaryo için mevcut bilişsel hizmet olmadığı durumlar için özel yetenekler yazma dahil olmak üzere, Azure depolama 'daki verilerden en iyi şekilde yararlanmak için, AI zenginleştirmesiyle ilgili çok daha fazla şey vardır. Aşağıdaki bağlantıları izleyerek daha fazla bilgi edinebilirsiniz.

+ [Azure portal (Azure Blob depolama) ile Blobları karşıya yükleme, indirme ve listeleme](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Blob Dizin oluşturucuyu ayarlama (Azure Bilişsel Arama)](search-howto-indexing-azure-blob-storage.md) 
+ [AI zenginleştirme genel bakış (Azure Bilişsel Arama)](cognitive-search-concept-intro.md) 
+ [Beceri oluşturma (Azure Bilişsel Arama)](cognitive-search-defining-skillset.md)
+ [Ek açıklama ağacındaki düğümleri eşleme (Azure Bilişsel Arama)](cognitive-search-output-field-mapping.md)
