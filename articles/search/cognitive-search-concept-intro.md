---
title: AI zenginleştirme 'ye giriş
titleSuffix: Azure Cognitive Search
description: Bilişsel beceriler ve yapay zeka algoritmaları kullanarak Azure Bilişsel Arama 'te aranabilir içerik oluşturmak için içerik ayıklama, doğal dil işleme (NLP) ve görüntü işleme.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 11/04/2019
ms.openlocfilehash: 5dc33de19ef71a0714052a6457bef9f32fc159c3
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720152"
---
# <a name="introduction-to-ai-in-azure-cognitive-search"></a>Azure Bilişsel Arama 'de AI 'ye giriş

AI zenginleştirme, görüntülerden, Blobların ve diğer yapılandırılmamış veri kaynaklarından metin ayıklamak için kullanılan Azure Bilişsel Arama Dizin oluşturma olanağından bir dizin veya bilgi deposunda daha aranabilir olmasını sağlamak için içeriği zenginleştirir. Ayıklama ve zenginleştirme, bir dizin oluşturma işlem hattına eklenen bilişsel *yetenekler* aracılığıyla uygulanır. Hizmette yerleşik olarak bulunan bilişsel yetenekler şu kategorilere ayrılır: 

+ **Doğal dil işleme** becerileri [varlık tanıma](cognitive-search-skill-entity-recognition.md), [dil algılama](cognitive-search-skill-language-detection.md), [anahtar tümceciği ayıklama](cognitive-search-skill-keyphrases.md), metin işleme ve yaklaşım [algılamayı](cognitive-search-skill-sentiment.md)içerir. Bu becerilerle, yapılandırılmamış metin, bir dizinde aranabilir ve filtrelenebilir alanlar olarak eşlenmiş yeni formları kabul edebilir.

+ **Görüntü işleme** becerileri, [optik karakter tanıma (OCR)](cognitive-search-skill-ocr.md) ve yüz algılama, görüntü yorumu, görüntü tanıma (sık sık insanlar ve yer işaretleri) veya gibi özellikler gibi [Görsel özelliklerin](cognitive-search-skill-image-analysis.md)tanımlanmasını içerir. renkler veya resim yönü. Azure Bilişsel Arama 'in tüm sorgu yeteneklerini kullanarak aranabilir görüntü içeriğinin metin sunumlarını oluşturabilirsiniz.

![Zenginleştirme ardışık düzen diyagramı](./media/cognitive-search-intro/cogsearch-architecture.png "zenginleştirme ardışık düzenine genel bakış")

Azure Bilişsel Arama bilişsel yetenekler, Bilişsel Hizmetler API'si: [görüntü işleme](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) ve [metin analizi](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)için önceden eğitilen makine öğrenimi modellerini temel alır. 

Doğal dil ve görüntü işleme, veri alma aşaması sırasında, Azure Bilişsel Arama 'te aranabilir bir dizinde belge kompozisyonunun bir parçası haline gelmesine neden olacak şekilde uygulanır. Verilerin bir Azure veri kümesi olarak kaynağı oluşturulur ve ardından ihtiyacınız olan [yerleşik becerileri](cognitive-search-predefined-skills.md) kullanarak bir dizin oluşturma işlem hattı üzerinden gönderilir. Mimaride, yerleşik yetenekler yeterli değilse, özel işlemeyi bütünleştirmek için [özel yetenekler](cognitive-search-create-custom-skill-example.md) oluşturup iliştirebilirsiniz. Örnekler, finans, bilimsel yayınlar veya ilaç gibi belirli bir etki alanını hedefleyen özel bir varlık modülü veya belge Sınıflandırıcısı olabilir.

> [!NOTE]
> İşlem sıklığını artırarak, daha fazla belge ekleyerek veya daha fazla AI algoritması ekleyerek kapsamı genişlettikten sonra faturalandırılabilir bilişsel [Hizmetler kaynağı](cognitive-search-attach-cognitive-services.md)eklemeniz gerekir. Bilişsel hizmetlerde API 'Leri çağırırken ve Azure Bilişsel Arama belge çözme aşamasının bir parçası olarak görüntü ayıklama için ücretler tahakkuk eder. Belgelerden metin ayıklama için herhangi bir ücret alınmaz.
>
> Yerleşik yeteneklerin yürütülmesi, mevcut bilişsel [Hizmetler Kullandıkça Öde fiyatı](https://azure.microsoft.com/pricing/details/cognitive-services/)üzerinden ücretlendirilir. Görüntü ayıklama fiyatlandırması, [Azure bilişsel arama fiyatlandırma sayfasında](https://go.microsoft.com/fwlink/?linkid=2042400)açıklanmaktadır.

## <a name="when-to-use-cognitive-skills"></a>Bilişsel yetenekler ne zaman kullanılır?

Ham içeriğiniz yapılandırılmamış metin, görüntü içeriği veya dil algılama ve çeviri gerektiren içerikler ise, yerleşik bilişsel yetenekler kullanmayı göz önünde bulundurmanız gerekir. Yerleşik bilişsel yetenekler aracılığıyla AI uygulamak, bu içeriğin kilidini açabilir ve arama ve veri bilimi uygulamalarınızda değerini ve yardımcı programını artırır. 

Ayrıca, ardışık düzen ile birleştirmek istediğiniz açık kaynaklı, üçüncü taraf veya birinci taraf kodunuz varsa, özel bir yetenek eklemeyi de düşünebilirsiniz. Çeşitli belge türlerinin salgörüme özelliklerini tanımlayan sınıflandırma modelleri bu kategoriye girer, ancak içeriğinize değer ekleyen herhangi bir paket de kullanılabilir.

### <a name="more-about-built-in-skills"></a>Yerleşik yetenekler hakkında daha fazla bilgi

Yerleşik yetenekler kullanılarak derlenen bir beceri, aşağıdaki uygulama senaryolarında iyi şekilde uygundur:

+ Tam metin araması yapmak istediğiniz taranmış belgeler (JPEG). JPEG dosyalarından metin tanımlamak, ayıklamak ve almak için bir optik karakter tanıma (OCR) yeteneği ekleyebilirsiniz.

+ Birleştirilmiş görüntü ve metin içeren PDF 'Ler. PDF 'Lerdeki metin, enzenginleştirme adımlarının kullanımı olmadan dizin oluşturma sırasında ayıklanabilir, ancak görüntü ve doğal dil işlemenin eklenmesi genellikle standart bir dizin oluşturma işleminin sağladığı daha iyi bir sonuç üretebilir.

+ Dil algılamayı ve muhtemelen metin çevirisini uygulamak istediğiniz çok dilli içerik.

+ Büyük bir belgede gizli bir anlamı veya bağlamı olan içerik içeren yapılandırılmamış veya yarı yapılandırılmış belgeler. 

  Genellikle, tek bir "alan" ile paketlenmiş büyük bir içerik gövdesini içeren bloblardır. Bir dizin oluşturucuya görüntü ve doğal dil işleme becerileri ekleyerek, ham içerikte yer alan yeni bilgiler oluşturabilir, aksi takdirde ayrı alanlar olarak ortaya çıkmış olursunuz. Kullanıma hazır kullanıma hazır yerleşik bilişsel yetenekler: anahtar tümceciği ayıklama, yaklaşım Analizi ve varlık tanıma (kişiler, kuruluşlar ve konumlar).

  Ayrıca, yerleşik yetenekler metin bölünmüş, birleştirme ve şekil işlemleri aracılığıyla içeriği yeniden yapılandırmak için de kullanılabilir.

### <a name="more-about-custom-skills"></a>Özel yetenekler hakkında daha fazla bilgi

Özel yetenekler, formları tanıma gibi daha karmaşık senaryoları veya özel [Beceri Web arabirimine](cognitive-search-custom-skill-interface.md)sağladığınız ve kaydırabileceğiniz bir modeli kullanarak özel varlık algılamayı destekleyebilir. Özel yeteneklerin çeşitli örnekleri, [form tanıyıcı](/azure/cognitive-services/form-recognizer/overview), [Bing varlık arama API'si](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example)tümleştirmesi ve [özel varlık tanıma](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)içerir.


## <a name="components-of-an-enrichment-pipeline"></a>Zenginleştirme işlem hattının bileşenleri

Zenginleştirme işlem hattı, veri kaynaklarını gezinen ve uçtan uca Dizin işleme sağlayan [*dizin oluşturucularının*](search-indexer-overview.md) temel alır. Yetenekler artık, tanımladığınız beceri göre belgeleri kesintiye uğratan ve zenginleştirmektedir. Dizin oluşturulduktan sonra, [Azure bilişsel arama tarafından desteklenen tüm sorgu türleri](search-query-overview.md)aracılığıyla arama istekleri aracılığıyla içeriğe erişebilirsiniz.  Dizin oluşturuculardan yeni olduğunuzda, bu bölüm adımlarda size yol gösterir.

### <a name="step-1-connection-and-document-cracking-phase"></a>1\. Adım: bağlantı ve belge çözme aşaması

İşlem hattının başlangıcında, yapılandırılmamış metin veya metin olmayan içerik (örneğin, görüntü ve taranmış belge JPEG dosyaları) vardır. Verilerin bir Dizin Oluşturucu tarafından erişilebilen bir Azure veri depolama hizmetinde bulunması gerekir. Dizin oluşturucular, kaynak verilerden metin ayıklamak için kaynak belgeleri "çözebilir".

![Belge çözme aşaması](./media/cognitive-search-intro/document-cracking-phase-blowup.png "Belge çözme")

 Desteklenen kaynaklar Azure Blob depolama, Azure Tablo depolama, Azure SQL veritabanı ve Azure Cosmos DB içerir. Metin tabanlı içerik şu dosya türlerinden ayıklanabilir: PDF 'Ler, Word, PowerPoint, CSV dosyaları. Tam liste için bkz. [desteklenen biçimler](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>2\. Adım: bilişsel beceriler ve zenginleştirme aşaması

Zenginleştirme, Atomik işlemler gerçekleştirirken bilişsel *yetenekler* aracılığıyla yapılır. Örneğin, bir PDF 'den metin içeriğine sahip olduktan sonra, dizinde yerel olarak kullanılamayan yeni alanlar oluşturmak için varlık tanıma dil algılamayı veya anahtar tümceciği ayıklama uygulayabilirsiniz. Tamamen, işlem hattınızda kullanılan yeteneklerin koleksiyonuna bir *beceri*denir.  

![Zenginleştirme aşaması](./media/cognitive-search-intro/enrichment-phase-blowup.png "zenginleştirme aşaması")

Bir beceri, sağladığınız yerleşik bilişsel [becerileri](cognitive-search-predefined-skills.md) veya [özel becerileri](cognitive-search-create-custom-skill-example.md) temel alır ve beceri 'e bağlanırsınız. Bir beceri, en düşük veya çok karmaşık olabilir ve yalnızca işleme türü değil, işlem sırasını da belirler. Bir beceri ve bir dizin oluşturucunun parçası olarak tanımlanan alan eşlemeleri, zenginleştirme işlem hattını tam olarak belirtir. Tüm bu parçaları birlikte çekme hakkında daha fazla bilgi için bkz. [beceri tanımlama](cognitive-search-defining-skillset.md).

Dahili olarak, işlem hattı zenginleştirilmiş bir belge koleksiyonu oluşturur. Zenginleştirilmiş belgelerin hangi bölümlerinin arama dizininizdeki dizinlenebilir alanlarla eşlenmeli olduğuna karar verebilirsiniz. Örneğin, anahtar tümceleri ayıklama ve varlık tanıma becerileri uyguladıysanız, bu yeni alanlar zenginleştirilmiş belgenin bir parçası haline gelir ve dizininizdeki alanlarla eşleştirilebilir. Giriş/çıkış formaları hakkında daha fazla bilgi için bkz. [ek açıklamalar](cognitive-search-concept-annotations-syntax.md) .

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Zenginleştirmelerin kaydedileceği bir knowledgeStore öğesi ekleme

[Arama Rest api-Version = 2019-05 -06-Preview](search-api-preview.md) , becerileri ' i genişleterek, Azure depolama bağlantısı ve zenginlerin nasıl depolandığını betimleyen projeksiyonlar sağlayan bir `knowledgeStore` tanımıyla genişletilir. 

Bir beceri bilgi deposu eklemek, tam metin arama dışındaki senaryolar için kendi zenginleştirmelerinin bir temsilini proje olanağı sağlar. Daha fazla bilgi için bkz. [bilgi deposu (Önizleme)](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>3\. Adım: arama dizini ve sorgu tabanlı erişim

İşlem tamamlandığında, Azure Bilişsel Arama 'de tam metin araması yapılan, zenginleştirilmiş belgelerden oluşan bir arama dizinine sahip olursunuz. [Dizin sorgulama](search-query-overview.md) , geliştiricilerin ve kullanıcıların işlem hattı tarafından oluşturulan zenginleştirilmiş içeriğe nasıl erişmesinden oluşur. 

![Arama simgesiyle Dizin](./media/cognitive-search-intro/search-phase-blowup.png "Arama simgesiyle Dizin")

Dizin, Azure Bilişsel Arama için oluşturabileceğiniz herhangi bir diğer benzer: özel çözümleyiciler ile destekleyebilirsiniz, benzer arama sorgularını çağırabilir, filtrelenmiş arama ekleyebilir veya arama sonuçlarını yeniden şekillendirmek için Puanlama profilleriyle denemeler yapabilirsiniz.

Dizinler, Puanlama profilleri ve eş anlamlı haritalar gibi belirli bir dizine iliştirilmiş alanları, öznitelikleri ve diğer yapıları tanımlayan bir dizin şemasından oluşturulur. Bir dizin tanımlandıktan ve doldurulduktan sonra, yeni ve güncelleştirilmiş kaynak belgeleri almak için artımlı olarak dizin oluşturabilirsiniz. Belirli değişiklikler tam yeniden oluşturma gerektirir. Şema tasarımı kararlı olana kadar küçük bir veri kümesi kullanmanız gerekir. Daha fazla bilgi için bkz. [Yeniden dizin derleme](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Önemli özellikler ve kavramlar

| Kavram | Açıklama| Bağlantılar |
|---------|------------|-------|
| Beceri | Beceri koleksiyonu içeren bir üst düzey adlandırılmış kaynak. Bir beceri, enzenginleştirme ardışık düzeni. Dizin Oluşturucu tarafından dizin oluşturma sırasında çağrılır. | Bkz. [beceri tanımlama](cognitive-search-defining-skillset.md) |
| Bilişsel Beceri | Zenginleştirme ardışık düzeninde atomik bir dönüşüm. Genellikle, yapısını çıkaran veya veren ve bu nedenle giriş verilerini kavramak için bir bileşendir. Neredeyse her zaman, çıktı metin tabanlıdır ve işleme, görüntü girişlerinden metin çıkaran veya üreten doğal dil işleme veya görüntü işleme olur. Bir beceriye ait çıkış, dizindeki bir alanla eşleştirilebilir veya bir aşağı akış zenginleştirme için giriş olarak kullanılabilir. Bir yetenek önceden tanımlanmış ve Microsoft tarafından sağlanıyor ya da özel: sizin tarafınızdan oluşturulup dağıtılır. | [Yerleşik bilişsel yetenekler](cognitive-search-predefined-skills.md) |
| Veri ayıklama | Geniş kapsamlı bir işleme sahiptir, ancak AI zenginleştirmesiyle ilgili olarak, varlık tanıma yeteneği genellikle bu bilgileri yerel olarak sağlamayan bir kaynaktaki verileri (varlık) ayıklamak için kullanılır. | Bkz. [varlık tanıma becerisi](cognitive-search-skill-entity-recognition.md) ve [belge ayıklama becerisi (Önizleme)](cognitive-search-skill-document-extraction.md)| 
| Görüntü işleme | Bir yer işareti tanıma veya bir görüntüden metin çıkaran gibi bir görüntüden metin alır. Yaygın örneklerde, taranmış bir belge (JPEG) dosyasından karakter kaldırma için OCR veya sokak işareti içeren bir fotoğrafta cadde adı tanıma gösterilebilir. | Bkz. [görüntü analizi beceri](cognitive-search-skill-image-analysis.md) veya [OCR becerisi](cognitive-search-skill-ocr.md)
| Doğal dil işleme | Öngörüler ve metin girişleri hakkındaki bilgiler için metin işleme. Dil algılama, yaklaşım Analizi ve anahtar tümceciği ayıklama, doğal dil işleme altına giren yeteneklerdir.  | Bkz. [anahtar ifade ayıklama yetenek](cognitive-search-skill-keyphrases.md), [dil algılama beceri](cognitive-search-skill-language-detection.md), [metin çevirisi becerisi (Önizleme)](cognitive-search-skill-text-translation.md), [yaklaşım Analizi beceri](cognitive-search-skill-sentiment.md) |
| Belge çözme | Dizin oluşturma sırasında metin olmayan kaynaklardaki metin içeriğini ayıklama veya oluşturma işlemi. Optik karakter tanıma (OCR) bir örnektir, ancak genellikle Dizin Oluşturucu uygulama dosyalarından içerik çıkaran için temel Dizin Oluşturucu işlevselliğine başvurur. Kaynak dosya konumu sağlayan veri kaynağı ve alan eşlemeleri sağlayan Dizin Oluşturucu tanımı, belge çözme içindeki anahtar faktörlerdir. | Bkz. [Dizin oluşturuculara genel bakış](search-indexer-overview.md) |
| Biçimlendirmesini | Metin parçalarını daha büyük bir yapıda birleştirin veya daha büyük bir şekilde daha fazla aşağı akış işleme için daha büyük metin öbeklerini yönetilebilir bir boyuta bölün. | Bkz. bağımsız [yetenek](cognitive-search-skill-shaper.md), [metin birleşme yeteneği](cognitive-search-skill-textmerger.md), [metin bölünmüş yetenek](cognitive-search-skill-textsplit.md) |
| Zenginleştirilmiş belgeler | İşlem sırasında oluşturulan, son çıktı bir arama dizinine yansıtılmış bir geçişli iç yapı. Bir beceri, hangi enzenginlerin gerçekleştirileceğini belirler. Alan eşlemeleri dizine hangi veri öğelerinin ekleneceğini belirleme. İsteğe bağlı olarak, Depolama Gezgini, Power BI veya Azure Blob depolama alanına bağlanan herhangi bir araçla ilgili araçları kullanarak zenginleştirilmiş belgeleri kalıcı hale getirmek ve incelemek için bir bilgi deposu oluşturabilirsiniz. | [Bilgi deposuna bakın (Önizleme)](knowledge-store-concept-intro.md) |
| Dizinleyici |  Bir dış veri kaynağından aranabilir verileri ve meta verileri çıkaran ve belge çözme için dizin ve veri kaynağınız arasındaki alan eşlemelerine göre bir dizini dolduran bir gezgin. AI zenginleştirmelerinde, Dizin Oluşturucu bir beceri çağırır ve zenginleştirme çıktısını dizindeki hedef alanlarla ilişkilendirirken alan eşlemelerini içerir. Dizin Oluşturucu tanımı, işlem hattı işlemlerine yönelik tüm yönergeleri ve başvuruları içerir ve Dizin oluşturucuyu çalıştırdığınızda işlem hattı çağrılır. Ek yapılandırma ile, var olan işlemeyi yeniden kullanabilir ve yalnızca değiştirilen adımları ve becerileri çalıştırabilirsiniz. | Bkz. [Dizin oluşturucular](search-indexer-overview.md) ve [artımlı dizin oluşturma (Önizleme)](cognitive-search-incremental-indexing-conceptual.md). |
| Veri kaynağı  | Azure üzerinde desteklenen türlerin dış veri kaynağına bağlanmak için bir Dizin Oluşturucu tarafından kullanılan nesne. | Bkz. [Dizin oluşturuculara genel bakış](search-indexer-overview.md) |
| Dizin oluşturma | Alan yapısını ve kullanımını tanımlayan bir dizin şemasından oluşturulan Azure Bilişsel Arama kalıcı arama dizini. | Bkz. [temel dizin oluşturma](search-what-is-an-index.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Nereden başlamalıyım?

**1. Adım: [Azure bilişsel arama kaynağı oluşturma](search-create-service-portal.md)** 

**2. Adım: bazı hızlı başlangıçlara ve uygulamalı deneyim için örneklere deneme yapın**

+ [Hızlı Başlangıç (portal)](cognitive-search-quickstart-blob.md)
+ [Öğretici (HTTP istekleri)](cognitive-search-tutorial-blob.md)
+ [Örnek: AI zenginleştirme (C#) için özel bir yetenek oluşturma](cognitive-search-create-custom-skill-example.md)

Ücretsiz hizmetin öğrenme amacıyla önerilmesini öneririz, ancak ücretsiz işlem sayısı günde 20 belge ile sınırlıdır. Hem hızlı başlangıç hem de Öğreticiyi bir gün içinde çalıştırmak için, her iki alıştırmada de sığdırabilmeniz için daha küçük bir dosya kümesi (10 belge) kullanın veya bir sayacı sıfıra geri almak için hızlı başlangıçta veya öğreticide kullandığınız dizin oluşturucuyu silebilirsiniz.

**3. Adım: API 'YI gözden geçirme**

REST `api-version=2019-05-06` istekleri veya .NET SDK 'sını kullanabilirsiniz. Bilgi deposunu araştırıyorsanız, bunun yerine önizleme REST API kullanın (`api-version=2019-05-06-Preview`).

Bu adım, bir AI zenginleştirme çözümü oluşturmak için REST API 'Lerini kullanır. AI zenginleştirme için yalnızca iki API eklenir veya genişletilir. Diğer API 'Ler, genel olarak kullanılabilen sürümlerle aynı söz dizimine sahiptir.

| REST API | Açıklama |
|-----|-------------|
| [Veri Kaynağı Oluşturma](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Zenginleştirilmiş belgeler oluşturmak için kullanılan kaynak verileri sağlayan bir dış veri kaynağını tanımlayan kaynak.  |
| [Beceri oluşturma (api-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Bu API AI zenginleştirme 'ya özeldir. Dizin oluşturma sırasında bir zenginleştirme ardışık düzeninde kullanılan [yerleşik yeteneklerin](cognitive-search-predefined-skills.md) ve özel bilişsel [yeteneklerin](cognitive-search-custom-skill-interface.md) kullanımını koordine eden bir kaynaktır. |
| [Dizin Oluştur](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Azure Bilişsel Arama dizinini ifade eden bir şema. Dizindeki alanlar, kaynak verilerdeki alanlara veya zenginleştirme aşamasında üretilen alanlara (örneğin, varlık tanıma tarafından oluşturulan kuruluş adları için bir alan) eşlenir. |
| [Dizin Oluşturucu oluştur (api-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Dizin oluşturma sırasında kullanılan bileşenleri tanımlayan kaynak: veri kaynağı, Beceri, kaynak ve ara veri yapılarından hedef dizine ve dizinin kendisi de dahil olmak üzere. Dizin oluşturucuyu çalıştırmak, veri alımı ve zenginleştirme için tetikleyiciydi. Çıktı, kaynak verilerle doldurulan, becerileri aracılığıyla zenginleştirilmiş, Dizin şemasını temel alan bir arama dizinidir. Bu var olan API, Beceri özelliği dahil olmak üzere bilişsel arama senaryolarında genişletilir. |

**Denetim listesi: tipik bir iş akışı**

1. Azure Kaynak verilerinizi bir temsili örneğine alt küme haline koyun. Dizin oluşturma zaman alır, bu nedenle küçük, temsili bir veri kümesiyle başlar ve sonra çözüm malarınız kadar artımlı olarak oluşturun.

1. Veri alımı için bir bağlantı dizesi sağlamak üzere Azure Bilişsel Arama 'de bir [veri kaynağı nesnesi](https://docs.microsoft.com/rest/api/searchservice/create-data-source) oluşturun.

1. Zenginleştirme adımları ile bir [beceri](https://docs.microsoft.com/rest/api/searchservice/create-skillset) oluşturun.

1. [Dizin şemasını](https://docs.microsoft.com/rest/api/searchservice/create-index)tanımlayın. *Alanlar* koleksiyonu, kaynak verilerden alanlar içerir. Ayrıca, zenginleştirme sırasında oluşturulan içerikler için oluşturulan değerleri tutmak üzere ek alanlar da saplama yapmanız gerekir.

1. Veri kaynağına, beceri ve dizine başvuran [Dizin oluşturucuyu](https://docs.microsoft.com/rest/api/searchservice/create-skillset) tanımlayın.

1. Dizin Oluşturucu içinde *Outputfieldmappings*ekleyin. Bu bölüm, Beceri (adım 3 ' te) çıktısını Dizin şemasındaki (adım 4 ' te) giriş alanlarına eşler.

1. Azure Bilişsel Arama Dizin oluşturucuyu ifade etmek için az önce oluşturduğunuz (istek gövdesinde bir Dizin Oluşturucu tanımına sahip bir POST isteği) *Create Indexer* isteği gönderin. Bu adım, işlem hattını çağırarak Dizin oluşturucuyu nasıl çalıştıracağınızı açıklamaktadır.

1. Sonuçları değerlendirmek için sorguları çalıştırın ve becerileri, şema veya Dizin Oluşturucu yapılandırmasını güncelleştirmek için kodu değiştirin.

1. İşlem hattını yeniden oluşturmadan önce [Dizin oluşturucuyu sıfırlayın](search-howto-reindex.md) .

Belirli sorular veya sorunlar hakkında daha fazla bilgi için bkz. [sorun giderme ipuçları](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Sonraki adımlar

+ [AI zenginleştirme belgeleri bağlantıları](cognitive-search-resources-documentation.md)
+ [Hızlı başlangıç: bir portalda AI zenginleştirme yapmayı deneyin](cognitive-search-quickstart-blob.md)
+ [Öğretici: AI zenginleştirme API 'Leri hakkında bilgi edinin](cognitive-search-tutorial-blob.md)
+ [Bilgi deposu (Önizleme)](knowledge-store-concept-intro.md)
+ [Bilgi deposu izlenecek yol](knowledge-store-howto.md)
