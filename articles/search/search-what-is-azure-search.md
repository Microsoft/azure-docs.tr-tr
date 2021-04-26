---
title: Azure Bilişsel Arama’ya giriş
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama, Microsoft 'un tam olarak yönetilen bir bulut arama hizmetidir. Kullanım örnekleri, geliştirme iş akışı, diğer Microsoft arama ürünleriyle karşılaştırmalar ve nasıl başlaleyeceğinizi hakkında bilgi edinin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/09/2021
ms.custom: contperf-fy21q1
ms.openlocfilehash: e17d08d09814c135af3e0b4fc299b6e6f42326d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549898"
---
# <a name="what-is-azure-cognitive-search"></a>Azure Bilişsel Arama nedir?

Azure Bilişsel Arama ([eskiden "Azure Search" olarak bilinirdi](whats-new.md#new-service-name)), geliştiriciler için Web, mobil ve kurumsal uygulamalarda özel, heterojen içerik üzerinde zengin arama deneyimi oluşturmaya yönelik API 'ler ve araçlar sağlayan bir bulut arama hizmetidir. 

Arama, katalog veya belge arama, e-ticaret sitesi arama veya veri bilimi için bilgi madenciliği gibi yaygın senaryolar sayesinde kullanıcılara içerik sunan herhangi bir uygulamaya yöneliktir. Bilişsel Arama API 'Leri ve mimarisi, herhangi bir çözüme gelişmiş bilgi alımı ekleme görevini basitleştirir.

Bir arama hizmeti aşağıdaki bileşenlere sahiptir:

+ Tam metin araması için arama altyapısı
+ Kullanıcıya ait dizine alınmış içeriğin kalıcı depolaması
+ İçerik dizinleme ve sorgulama için API 'Ler
+ İsteğe bağlı [AI tabanlı zenginler](cognitive-search-concept-intro.md), görüntülerden aranabilir içerik oluşturma, ham yapılandırılmamış metin, uygulama dosyaları
+ Veri, makine öğrenimi/AI, izleme ve güvenlik için diğer Azure hizmetleriyle isteğe bağlı tümleştirme
+ İyileştirilmiş uygunluk için isteğe bağlı [anlamsal arama (Önizleme)](semantic-search-overview.md) uygulamasına

Mimari türsel olarak, bir arama hizmeti, dizini oluşturulmamış verilerinizi içeren dış veri depoları arasında ve bir arama dizinine sorgu istekleri gönderen ve yanıtı işleyen istemci uygulamanız arasında yer alır.

![Azure Bilişsel Arama mimarisi](media/search-what-is-azure-search/azure-search-diagram.svg "Azure Bilişsel Arama mimarisi")

Dışarıdan, arama, Azure veri kaynaklarından veri alımı/alımı otomatikleştiren diğer Azure hizmetleriyle ve görüntü ve metin analizi gibi bilişsel hizmetlerden ya da Azure Işlevleri içinde oluşturduğunuz veya Azure Machine Learning oluşturduğunuz özel bir *AI içeren* *becerileri* ile tümleşebilir.

## <a name="inside-a-search-service"></a>Bir arama hizmeti içinde

Arama hizmetinin kendisinde, iki birincil iş yükü *Dizin oluşturma* ve *sorgulama* yapılır. 

+ [Dizin oluşturma](search-what-is-an-index.md) işlemi, arama hizmetinize içerik yükleyen ve aranabilir hale getiren bir Intake işlemidir. Dahili olarak, gelen metinler belirteçlerde işlenir ve hızlı taramalar için ters dizinler halinde depolanır. JSON belgeleri biçiminde olan herhangi bir metni karşıya yükleyebilirsiniz.

  Ayrıca, içeriğiniz karışık dosyalar içeriyorsa, bilişsel [yetenekler](cognitive-search-working-with-skillsets.md)aracılığıyla *AI zenginleştirme* ekleme seçeneğiniz vardır. AI zenginleştirme, uygulama dosyalarına gömülü metni ayıklayabilir ve ayrıca içeriği çözümleyerek metin olmayan dosyalardan metin ve yapıyı çıkarmaz. 

  Analizi sağlayan yetenekler, Microsoft 'un veya oluşturduğunuz özel yeteneklerin ön tanımlı olanlardır. Sonraki analizler ve dönüştürmeler, daha önce mevcut olmayan yeni bilgi ve yapılara neden olabilir ve birçok arama ve bilgi araştırma senaryosu için yüksek yardımcı program sağlar.

+ Bir dizin aranabilir metinle doldurulduktan sonra [sorgulama](search-query-overview.md) , istemci uygulamanız bir arama hizmetine sorgu istekleri gönderdiğinde ve yanıtları işlediğinde, bu durum oluşabilir. Tüm sorgu yürütmesi, hizmetinize oluşturduğunuz, sahip olduğunuz ve depoladığınız bir arama dizininden daha fazla. İstemci uygulamanızda, arama deneyimi Azure Bilişsel Arama API 'Leri kullanılarak tanımlanır ve ilgi ayarlama, otomatik tamamlama, eş anlamlı eşleştirme, belirsiz eşleştirme, model eşleştirme, filtre ve sıralama dahil olabilir.

Bilgi alma sürecinin karmaşıklığını maskeleyen basit bir [REST API’si](/rest/api/searchservice/) veya [.NET SDK’sı](search-howto-dotnet-sdk.md) aracılığıyla bu işlev sunulur. Ayrıca, hizmet yönetimi ve içerik yönetimi için Azure portal kullanarak, prototiplerinizi prototip ve becerileri sorgulama ve dizin oluşturma araçları da kullanabilirsiniz. Hizmet bulutta çalıştığından, altyapı ve kullanılabilirlik Microsoft tarafından yönetilir.

## <a name="why-use-cognitive-search"></a>Bilişsel Arama neden kullanılmalıdır

Azure Bilişsel Arama aşağıdaki uygulama senaryoları için uygundur:

+ Heterojen içeriği özel, Kullanıcı tanımlı bir arama dizinine birleştirin.

+ Aramayla ilgili özellikleri kolayca uygulayın: ilgi ayarlama, çok yönlü gezinme, filtreler (coğrafi uzamsal arama dahil), eş anlamlı eşleme ve otomatik tamamlama.

+ Büyük ölçekli metin veya resim dosyalarını veya Azure Blob depolamada veya Cosmos DB depolanan uygulama dosyalarını aranabilir JSON belgelerine dönüştürün. Bu, dış işlem ekleyen bilişsel [yetenekler](cognitive-search-concept-intro.md) aracılığıyla Dizin sırasında elde edilir.

+ Dil veya özel metin analizi ekleyin. Ingilizce olmayan içeriğiniz varsa Azure Bilişsel Arama hem Lucene Çözümleyicileri hem de Microsoft 'un doğal dil işlemcilerini destekler. Ayrıca, vurguları, aksanların filtrelenmesi veya dizelerde desenler tanınması veya korunması gibi ham içeriğin özel işlemesini elde etmek için de yapılandırabilirsiniz.

Belirli işlevler hakkında daha fazla bilgi için bkz. [Azure bilişsel arama özellikleri](search-features-list.md)

## <a name="how-to-get-started"></a>Nasıl kullanmaya başlarım

Temel arama özelliklerinin uçtan uca incelenmesi, dört adımda elde edilebilir:

1. Yalnızca hizmetiniz tarafından kullanılan adanmış kaynaklar için paylaşılan ücretsiz katmanda veya [faturalanabilir katmanda](https://azure.microsoft.com/pricing/details/search/) [**bir arama hizmeti oluşturun**](search-create-service-portal.md) . Tüm hızlı başlangıçler ve öğreticiler, paylaşılan bir hizmette tamamlanabilir.

1. Portal, [REST API](/rest/api/searchservice/create-index), [.NET SDK](search-howto-dotnet-sdk.md)veya başka bir SDK kullanarak [**bir arama dizini oluşturun**](search-what-is-an-index.md) . Dizin şeması Aranabilir içeriğin yapısını tanımlar.

1. Herhangi bir kaynaktan JSON belgelerini göndermek için ["Push" modelini](tutorial-optimize-indexing-push-api.md) kullanarak [**içeriği karşıya yükleyin**](search-what-is-data-import.md) veya kaynak verileriniz Azure üzerinde ise ["çekme" modelini (Dizin oluşturucular)](search-indexer-overview.md) kullanın.

1. Portal, [REST API](search-get-started-rest.md), [.NET SDK](/dotnet/api/azure.search.documents.searchclient.search)veya başka bir SDK 'daki [Arama Gezgini](search-explorer.md) 'ni kullanarak [**bir dizini sorgulayın**](search-query-overview.md) .

İlk araştırma için, [**verileri Içeri aktarma Sihirbazı**](search-get-started-portal.md) ile başlayın ve bir dizin oluşturmak, yüklemek ve birkaç dakika içinde sorgulamak için yerleşik bir Azure veri kaynağı.

Karmaşık veya özel çözümlerle ilgili yardım için Bilişsel Arama teknolojide derin uzmanlığa sahip [**bir iş ortağıyla iletişim kurun**](resource-partners-knowledge-mining.md) .

## <a name="compare-search-options"></a>Arama seçeneklerini karşılaştırma

Müşteriler genellikle Azure Bilişsel Arama diğer aramayla ilgili diğer çözümlerle nasıl karşılaştırdığını sorar. Aşağıdaki tabloda temel farklılıklar özetlenmiştir.

| Karşılaştırılan | Temel farklılıklar |
|-------------|-----------------|
| Microsoft Arama | [Microsoft Search](/microsoftsearch/overview-microsoft-search) , SharePoint 'te içerik üzerinde sorgu yapması gereken Microsoft 365 kimliği doğrulanmış kullanıcılar içindir. Microsoft ve diğer kaynaklardan gelen bağlayıcılar aracılığıyla dış içeriği kabul etme olanağı sunan yöneticiler tarafından etkinleştirilmiş ve yapılandırılmış kullanıma sunulan bir arama deneyimi olarak sunulur. Senaryonuzu açıkladıysanız Microsoft Search 'ü Microsoft 365 keşfetmeye yönelik etkileyici bir seçenektir.<br/><br/>Buna karşılık, Azure Bilişsel Arama sorguları, tanımladığınız bir dizin üzerinde yürütür, genellikle farklı kaynaklardan, sahip olduğunuz veriler ve belgelerle doldurulur. Azure Bilişsel Arama, bazı Azure veri kaynakları için Dizin [oluşturucular](search-indexer-overview.md)aracılığıyla gezgin özelliklerine sahiptir, ancak dizin şemanıza uyan HERHANGI bir JSON belgesini tek ve birleştirilmiş bir aranabilir kaynağa gönderebilirsiniz. Ayrıca, dizin oluşturma işlem hattını makine öğrenimi ve sözlü Çözümleyicileri içerecek şekilde özelleştirebilirsiniz. Bilişsel Arama, büyük çözümlerde eklenti bileşeni olacak şekilde oluşturulduğundan, aramayı dilediğiniz platformda neredeyse her uygulamayla tümleştirebilirsiniz.|
|Bing | [Bing Web Araması API'si](../cognitive-services/bing-web-search/index.yml), Bing.com adresindeki dizinlerde gönderdiğiniz eşleşen terimleri arar. Dizinler, HTML, XML ve genel sitelerdeki diğer web içeriklerinden derlenir. Aynı temel üzerine oluşturulan [Bing Özel Arama](/azure/cognitive-services/bing-custom-search/), tek tek web sitelerine kapsamı belirlenmiş şekilde web içeriği türleri için aynı gezgin teknolojisini sunar.<br/><br/>Bilişsel Arama, dizini tanımlayabilir ve doldurabilirsiniz. Dizin [oluşturucular](search-indexer-overview.md) kullanarak Azure veri kaynaklarında verileri arayabilir veya herhangi bir DIZINLE uyumlu JSON belgesini arama hizmetinize gönderebilirsiniz. |
|Veritabanı araması | Birçok veritabanı platformu yerleşik bir arama deneyimi içerir. SQL Server'ın [tam metin araması](/sql/relational-databases/search/full-text-search) vardır. Cosmos DB ve benzeri teknolojilerin sorgulanabilir dizinleri vardır. Arama ile depolamayı birleştiren ürünler değerlendirilirken, hangisinin tercih edileceğini saptamak zor olabilir. Birçok çözüm, her ikisini de kullanır: depolama için DBMS ve özelleştirilmiş arama özellikleri için Azure Bilişsel Arama.<br/><br/>Azure Bilişsel Arama, DBMS aramasına kıyasla, heterojen kaynaklardan içerik depolar ve [56 dilde](/rest/api/searchservice/language-support)dile duyarlı metin işleme (sözcük kullanımı, katileştirme, Word Forms) gibi özel metin işleme özellikleri sunar. Ayrıca yanlış yazılmış sözcükleri otomatik düzeltme, [eş anlamlılar](/rest/api/searchservice/synonym-map-operations), [öneriler](/rest/api/searchservice/suggestions), [puanlama denetimleri](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [modeller](./search-filters-facets.md) ve [özel belirteçlere ayırma](/rest/api/searchservice/custom-analyzers-in-azure-search) özelliklerini de destekler. Azure Bilişsel Arama 'daki [tam metin arama altyapısı](search-lucene-query-architecture.md) , bilgi alma bölümünde sektör standardı olan Apache Lucene üzerinde oluşturulmuştur. Ancak, Azure Bilişsel Arama ters bir dizin biçimindeki verileri sürdürüyor olsa da, doğru veri depolama alanı için bir değiştirme değildir ve bu kapasitede kullanılması önerilmez. Daha fazla bilgi için bu [forum gönderisine](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data) bakın. <br/><br/>Kaynak kullanımı, bu kategorideki başka bir çekim noktasıdır. Dizin oluşturma ve bazı sorgu işlemleri çoğunlukla yoğun bilgi işlem içerir. Arama DBMS'den buluttaki ayrılmış bir çözüme aktarıldığında işlem yürütme için sistem kaynakları korunur. Üstelik, aramayı harici hale getirerek, sorgu hacmiyle eşleşecek şekilde kolayca ölçeği ayarlayabilirsiniz.|
|Ayrılmış arama çözümü | Tüm işlevleriyle birlikte ayrılmış arama kullanmaya karar verdiğinizi varsayarak, son kategorik karşılaştırma şirket içi çözümlerle bulut hizmetleri arasındadır. Birçok arama teknolojisi dizin oluşturma ve sorgu işlem hatları üzerinde kontrol, daha zengin sorgulama ve filtreleme söz dizimine erişim, derece ve ilgi düzeyi üzerinde kontrol ve kendinden yönlendirmeli ve akıllı arama özellikleri sunar. <br/><br/>Minimum ek yük ve bakım ile anahtar teslim bir çözüm istiyorsanız bulut hizmeti doğru seçimdir. <br/><br/>Bulut paradigması içinde birçok sağlayıcı, tam metin araması, coğrafi arama ve arama girişlerindeki belirli bir belirsizlik düzeyini işleme yeteneğiyle birlikte karşılaştırılabilir temel özellikler sunar. Genellikle bu [özel bir özellik olup](search-features-list.md) en iyi uygunluğu belirleyen yönetimin, araçların ve API’lerin kolaylaştırılmasını sağlar. |

Bulut sağlayıcıları arasında Azure Bilişsel Arama, birincil olarak hem bilgi alımı hem de içerik gezintisi için arama yapan uygulamalar için Azure 'daki içerik depoları ve veritabanları üzerinde tam metin araması iş yükleri için en güçlü bir güvendir. 

Temel güçlü yönleri şunlardır:

+ Dizin oluşturma katmanında Azure veri tümleştirmesi (gezginler)
+ Internet güvenlik gereksinimlerini desteklemek için Azure özel bağlantı tümleştirmesi
+ Aranabilir içerik türlerini metin ile aranabilir hale getirmek için AI işlemeyle tümleştirme.
+ 56 dilde güçlü metin araması için çözümleyicilerle birlikte linguistik ve özel analiz
+ [Kritik Özellikler](search-features-list.md): zengin sorgu dili, ilgi ayarlama, Faks, otomatik tamamlama, eş anlamlılar, coğrafi arama ve sonuç oluşturma.
+ Azure ölçekleme, güvenilirlik ve birinci sınıf kullanılabilirlik

Müşterilerimiz arasında, Azure Bilişsel Arama ' deki en geniş özelliklerden faydalanabilir. Çevrimiçi Kataloglar, iş kolu programları ve belge bulma uygulamaları vardır.

## <a name="watch-this-video"></a>Bu videoyu izleyin

Bu 15 dakikalık videoda, program Yöneticisi Lusıs Cabrera, Azure Bilişsel Arama tanıtır.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]