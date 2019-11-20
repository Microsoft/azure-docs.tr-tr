---
title: 'Azure Cosmos DB tasarım kalıbı: sosyal medya uygulamaları'
description: Azure Cosmos DB ve diğer Azure hizmetlerinin depolama esnekliğinden yararlanarak sosyal ağlara yönelik bir tasarım kalıbı hakkında bilgi edinin.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 8428e417f5f86edca77edae6ca4b7ef84e5ff425
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827290"
---
# <a name="going-social-with-azure-cosmos-db"></a>Azure Cosmos DB ile sosyal olarak çalışmaya devam edin

Büyük ölçüde birbirine bağlı topluluğu, bazı bir noktada **sosyal ağın**bir parçası haline gelmeyeceğiniz anlamına gelir. Sosyal ağlarınızı arkadaşlarınızla, iş arkadaşlarınızla, ailenizle veya bazı durumlarda sık karşılaşılan ilgi alanları olan kişilerle paylaşmak için kullanabilirsiniz.

Mühendisler veya geliştiriciler olarak, bu ağların verilerinizi nasıl depolayıp depolayabileceğini merak etmiş olabilirsiniz. Ya da belirli bir Nica pazarında yeni bir sosyal ağ oluşturmak veya mimari oluşturmak için de daha fazla görevli olabilirsiniz. Bu, önemli soru ortaya çıkar: tüm bu veriler nasıl depolanır?

Kullanıcılarınızın resimler, videolar veya hatta müzik gibi ilgili medyayla makale nakledebileceği yeni ve parlak bir sosyal ağ oluşturduğunuzu varsayalım. Kullanıcılar gönderilere yorum yapabilir ve derecelendirme noktaları verebilir. Kullanıcıların, ana web sitesi giriş sayfasında göreceği ve etkileşimde bulunduğu gönderilerin bir akışı olacaktır. Bu yöntem, ilk başta zor değildir, ancak kolaylık sağlaması için burada duralım. (İlişkilerden etkilenen özel Kullanıcı akışlarını Delve halinde bulabilirsiniz, ancak bu makalenin amacının ötesinde ilerleyerek.)

Bu nedenle, bu verileri nasıl depolayabilirim?

SQL veritabanlarında deneyiminiz olabilir veya bir [ilişkisel veri modelleme](https://en.wikipedia.org/wiki/Relational_model)kavramı vardır. Aşağıdaki gibi bir şey çizmeyi başlatabilirsiniz:

![Göreli bir ilişkisel modeli gösteren diyagram](./media/social-media-apps/social-media-apps-sql.png)

Kusursuz bir normalleştirilmiş ve düzgün veri yapısı... Bu, ölçeklendirmez.

Bir daha kaçırmayın, SQL veritabanlarıyla tüm yaşamım ile çalıştım. Bunlar harika, ancak her model, uygulama ve yazılım platformu gibi her senaryo için kusursuz değildir.

Neden SQL Bu senaryoda en iyi seçim değil? Tek bir gönderi yapısına göz atalım. Gönderiyi bir Web sitesi veya uygulamada göstermek istiyorsam, ile bir sorgu yapmam gerekir... sekiz tabloya (!) yalnızca tek bir gönderi göstermek için katılarak. Şimdi, ekranda dinamik olarak yüklenen ve görüntülenen gönderilerin akışını resim halinde görüntüleyin ve nerede gittiğini görebilirsiniz.

İçeriğinizi karşılamak için birçok birleşimle binlerce sorguyu çözümlemek üzere yeterince güce sahip bir enorur SQL örneği kullanabilirsiniz. Ancak ne zaman daha basit bir çözüm var?

## <a name="the-nosql-road"></a>NoSQL yolu

Bu makale, Azure 'un NoSQL veritabanı [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) uygun maliyetli bir şekilde sosyal platformunuzun verilerini modellemeye kılavuzluk eder. Ayrıca, [Gremlin API](../cosmos-db/graph-introduction.md)gibi diğer Azure Cosmos DB özelliklerini nasıl kullanacağınızı da söyler. Bir [NoSQL](https://en.wikipedia.org/wiki/NoSQL) yaklaşımı kullanarak veri depolama ve JSON [biçiminde uygulama,](https://en.wikipedia.org/wiki/Denormalization)daha önce karmaşık gönderi tek bir [belgeye](https://en.wikipedia.org/wiki/Document-oriented_database)dönüştürülebilir:

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["https://myfirstimage.png","https://mysecondimage.png"],
        "videos":[
            {"url":"https://myfirstvideo.mp4", "title":"The first video"},
            {"url":"https://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"https://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"https://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

Ve tek bir sorgu ile ve birleştirme olmadan alınabilir. Bu sorgu çok basit ve basittir ve bütçe temelinde, daha iyi bir sonuç elde etmek için daha az kaynak gerektirir.

Azure Cosmos DB, tüm özelliklerin Otomatik Dizin oluşturma ile dizine eklendiğinden emin olur. Otomatik dizinleme de [özelleştirilebilir](index-policy.md). Şemaya ücretsiz yaklaşım, belgeleri farklı ve dinamik yapılarla depolamamızı sağlar. Belki de yarın, gönderilerin kendileriyle ilişkili kategorilerin veya diyez etiketlerinin bir listesini içermesini istiyor musunuz? Cosmos DB, eklenen özniteliklere sahip yeni belgeleri bizimle ilgili ek iş olmadan işleymeyecektir.

Bir gönderiyle ilgili açıklamalar, ana özelliği olan diğer gönderiler olarak değerlendirilir. (Bu uygulama, nesne eşlemenizi basitleştirir.)

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

Tüm sosyal etkileşimler, sayaçlar olarak ayrı bir nesne üzerinde depolanabilir:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Akış oluşturma, belirli bir ilgi sırasına sahip gönderi kimliklerinin bir listesini tutan belgeler oluşturmaktan yalnızca bir önemi vardır:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Oluşturma tarihine göre sıralanmış gönderilere sahip "en son" bir akışa sahip olabilirsiniz. Ya da son 24 saat içinde daha beğeneni olan bu gönderilere sahip "Hottest" akışına sahip olabilirsiniz. Aynı şekilde, her kullanıcı için izleyici ve ilgi alanları gibi mantığa göre özel bir akış da uygulayabilirsiniz. Bu, gönderilerin bir listesi olmaya devam eder. Bu listelerin oluşturulması, ancak okuma performansı açıklanmadan bağımsız kalır. Bu listelerden birini aldıktan sonra tek seferde gönderilerin sayfalarını almak için [ın anahtar sözcüğünü](sql-query-keywords.md#in) kullanarak Cosmos DB için tek bir sorgu verirsiniz.

Akış akışları, [Azure App Services](https://azure.microsoft.com/services/app-service/) 'in arka plan işlemleri kullanılarak oluşturulabilir: [Web işleri](../app-service/webjobs-create.md). Bir gönderi oluşturulduktan sonra arka plan işleme, Azure [Web İşleri SDK 'sı](https://github.com/Azure/azure-webjobs-sdk/wiki)kullanılarak tetiklenen [Azure depolama](https://azure.microsoft.com/services/storage/) [kuyrukları](../storage/queues/storage-dotnet-how-to-use-queues.md) ve Web işleri kullanılarak tetiklenebilir.

Bir gönderi üzerinde işaret ve beğeni, sonuçta tutarlı bir ortam oluşturmak için aynı tekniği kullanarak ertelenmiş bir şekilde işlenebilir.

Takipçler, karmaşık. Cosmos DB belge boyut sınırına sahiptir ve büyük belgeleri okuma/yazma, uygulamanızın ölçeklenebilirliğini etkileyebilir. Bu nedenle, izleyicileri Bu yapıyla bir belge olarak depolamayı düşünebilirsiniz:

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

Bu yapı birkaç binlerce izleyicileri olan bir kullanıcı için çalışabilir. Ancak, bir ünlütlerin derecelendirmesine katılılırsa, bu yaklaşım büyük bir belge boyutuna yol açacağından, sonuç olarak belge boyutu üst sınırına gelebilir.

Bu sorunu çözmek için karışık bir yaklaşım kullanabilirsiniz. Kullanıcı Istatistikleri belgesinin bir parçası olarak, izleme sayısını depolayabilmeniz gerekir:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

"A-B" ilişkilerini koruanların her bir Kullanıcı ve [kenar](http://mathworld.wolfram.com/GraphEdge.html) için [izdüşümler](http://mathworld.wolfram.com/GraphVertex.html) oluşturmak üzere Azure Cosmos DB [Gremlin API](../cosmos-db/graph-introduction.md) 'sini kullanarak takipçilerin gerçek graflarını saklayabilirsiniz. Gremlin API 'SI ile belirli bir kullanıcı için izleyicileri alabilir ve sık sık insanlar önermek için daha karmaşık sorgular oluşturabilirsiniz. İster veya keyfini çıkarmış olan Içerik kategorilerini grafiğe eklerseniz akıllı içerik bulma 'yı içeren, bu kişilerin beğeneceğiniz içerikleri öneren veya sizinle ortak olan kişileri bulma gibi dalgalı deneyimler başlatabilirsiniz.

Kullanıcı Istatistikleri belgesi, UI veya hızlı profil önizlemelerde kart oluşturmak için hala kullanılabilir.

## <a name="the-ladder-pattern-and-data-duplication"></a>"Ladder" deseninin ve veri yinelemesi

Bir gönderisini referans eden JSON belgesinde fark etmiş olabileceğiniz gibi, bir kullanıcının birçok oluşumu vardır. Bu, hemen tahmin ettiğiniz için, bu yinelemeler, bir kullanıcıyı açıklayan bilgilerin birden fazla yerde mevcut olabileceğini ifade eder.

Daha hızlı sorgulara izin vermek için veri çoğaltmaya tabi olursunuz. Bu yan etkiyle ilgili sorun, bazı eylemlere göre kullanıcının verilerinin değiştiği, kullanıcının yaptığı tüm etkinlikleri bulmanız ve tümünü güncelleştirmeniz gerekir. Pratik, doğru değil mi?

Her etkinlik için uygulamanızda göstereceğiniz bir kullanıcının anahtar özniteliklerini tanımlayarak bunu çözeceğiz. Uygulamanızda bir gönderiyi görsel olarak gösterir ve yalnızca oluşturucunun adını ve resmini belirtirseniz, tüm Kullanıcı verilerini "createdBy" özniteliğinde depolıyor musunuz? Yalnızca kullanıcının resmini gösteren her yorum için, Kullanıcı bilgilerinin geri kalanı gerçekten gerekmez. Bu, "Izleyici modelini" çağırdığım bir şeyin dahil olduğu yerdir.

Kullanıcı bilgilerini örnek olarak ele alalım:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"\@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

Bu bilgilere bakarak, kritik bilgileri ve hangilerinin olmadığını hızlı bir şekilde tespit edebilir ve bu nedenle "el merdivenini" oluşturursunuz:

![Bir el der deseninin diyagramı](./media/social-media-apps/social-media-apps-ladder.png)

En küçük adımda, bir kullanıcıyı tanımlayan ve veri çoğaltma için kullanılan en az bilgi parçası olan UserChunk adı verilir. Yinelenen verilerin boyutunu yalnızca "göstereceğiniz" bilgilerle azaltarak, büyük güncelleştirmeler olasılığını azaltabilirsiniz.

Orta adıma Kullanıcı adı verilir. En çok erişilen ve kritik olan Cosmos DB en fazla performansa bağımlı sorgularda kullanılacak tam veri. Bir UserChunk tarafından temsil edilen bilgileri içerir.

En büyük değer, Genişletilmiş Kullanıcı. Bu, oturum açma sürecinde olduğu gibi, önemli Kullanıcı bilgilerini ve hızlı bir şekilde okunması gerekmeyen diğer verileri içerir. Bu veriler, Azure SQL veritabanı veya Azure depolama tablolarında Cosmos DB dışında depolanabilir.

Kullanıcıyı neden bölecektir, hatta bu bilgileri farklı yerlere depoluyordu? Bir performans noktasından bir görünüm olduğundan, belgeler daha büyük olduğundan sorguları sorgular. Sosyal ağınıza yönelik tüm performansa bağımlı sorgularınızı yapmak için, belgeleri ince ve doğru bilgilerle koruyun. Kullanım Analizi ve büyük veri girişimleri için tam profil düzenlemeleri, oturum açmalar ve veri madenciliği gibi son senaryolar için diğer ek bilgileri saklayın. Veri madenciliği için veri toplama işlemi, Azure SQL veritabanı 'nda çalıştığından gerçekten dikkatli olmaz. Kullanıcılarınız hızlı ve ince bir deneyimle karşılaşmış olabilir. Cosmos DB depolanan bir Kullanıcı şu kod gibi görünür:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
    }

Bir gönderi şöyle görünür:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

Bir öbek özniteliğinin etkilendiğine ilişkin bir düzenleme yapıldığında, etkilenen belgeleri kolayca bulabilirsiniz. Yalnızca `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`gibi dizinli özniteliklere işaret eden sorguları kullanın ve ardından öbekleri güncelleştirin.

## <a name="the-search-box"></a>Arama kutusu

Kullanıcılar, çok fazla içerik oluşturur, yağlar. Ve doğrudan içerik akışlarındaki içeriği arama ve bulma özelliği sunabileceksiniz, ancak oluşturucuları takip edemeyeceksiniz ya da yalnızca altı ay önce yaptığınız eski gönderiyi bulmaya çalışıyorsunuz olabilirsiniz.

Azure Cosmos DB kullandığınız için, arama işlemi ve Kullanıcı arabirimi dışında herhangi bir kod yazmadan [Azure bilişsel arama](https://azure.microsoft.com/services/search/) kullanarak bir arama motorunu kolayca uygulayabilirsiniz.

Bu işlem ne kadar kolay?

Azure Bilişsel Arama, [dizin oluşturucularının](https://msdn.microsoft.com/library/azure/dn946891.aspx)çağırdıkları öğeleri, veri depolarınızda bulunan arka plan süreçlerini ve verileri dizinlerdeki nesne ekleme, güncelleştirme veya kaldırma işlemini uygular. Azure [SQL veritabanı Dizinleyicileri](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [Azure Blob Dizinleyicileri](../search/search-howto-indexing-azure-blob-storage.md) ve ktam, [Azure Cosmos DB Dizin oluşturucularını](../search/search-howto-index-documentdb.md)destekler. Cosmos DB 'den Azure Bilişsel Arama 'a bilgilerin geçişi basittir. Tek yapmanız gereken şekilde hem teknolojiler bilgileri JSON biçiminde depolamak [dizininizi oluşturma](../search/search-create-index-portal.md) ve istediğiniz dizinli belgelerinizi öznitelikleri eşleyin. Bu kadar! Verilerinizin boyutuna bağlı olarak, tüm içeriğiniz bulut altyapısındaki en iyi hizmet olarak arama çözümü ile dakikalar içinde Aranmak üzere kullanılabilir olacaktır.

Azure Bilişsel Arama hakkında daha fazla bilgi için, [arama yapmak üzere Hitchhiker ' ın kılavuzunu](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/)ziyaret edebilirsiniz.

## <a name="the-underlying-knowledge"></a>Temel alınan bilgi

Her gün büyüyen ve büyüdüğü tüm bu içeriği depoladıktan sonra, şu bilgilerden daha fazla bilgi için kullanıcılardan neler yapabilirim?

Yanıt basittir: çalışır hale getirin ve bu uygulamadan öğrenin.

Ancak neleri öğrenebilirsiniz? Birkaç kolay örnek, yaklaşım [analizini](https://en.wikipedia.org/wiki/Sentiment_analysis), kullanıcının tercihlerine dayalı içerik önerilerini, hatta sosyal ağınız tarafından yayımlanan içeriğin aile için güvenli olmasını sağlayan otomatik bir içerik moderatör içerir.

Artık kullanıma sundum, bu desenleri ve bilgileri basit veritabanlarının ve dosyaların dışına çıkarmak için matematik bilimi 'nin bazı PhD 'ye ihtiyacınız olduğunu düşündük, ancak yanlış bir sorun var.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx)bir parçası olarak, basit bir sürükle ve bırak arabirimindeki algoritmaları kullanarak Iş akışları oluşturmanıza, [R](https://en.wikipedia.org/wiki/R_\(programming_language\))'de kendi algoritmalarınızı kodlamanızı veya bazı : [metin analizi](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Content moderator veya [öneriler](https://gallery.azure.ai/Solution/Recommendations-Solution)gibi API 'leri zaten inşa edilmiş ve kullanıma hazır.

Bu Machine Learning senaryolarından herhangi birine ulaşmak için [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) kullanarak farklı kaynaklardaki bilgileri alabilirsiniz. Ayrıca, bilgileri işlemek için [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) ' i de kullanabilir ve Azure Machine Learning tarafından işlenebilecek bir çıktı oluşturabilirsiniz.

Diğer bir kullanılabilir seçenek, Kullanıcı içeriğinizi çözümlemek için Azure bilişsel [Hizmetler](https://www.microsoft.com/cognitive-services) 'i kullanmaktır; Onları yalnızca daha iyi ( [Metin Analizi API'si](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)ile yazdıklarınızı analiz ederek) anlayabilmektir, ancak istenmeyen veya yetişkinlere yönelik içeriği de algılayabilir ve [görüntü işleme API'si](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api)uygun şekilde davranabilir. Bilişsel hizmetler, her türlü Machine Learning bilgisinin kullanılmasını gerektirmeyen çok sayıda kullanıma hazır çözüm içerir.

## <a name="a-planet-scale-social-experience"></a>Bir Planet-ölçekli sosyal deneyim

En az bir son, ancak önemli olmayan önemli Makale: **ölçeklenebilirlik**' i ele almalıdır. Bir mimari tasarlarken, her bileşenin kendi kendine ölçeklendirilmesi gerekir. Son olarak daha fazla veri işlemek gerekecektir veya daha büyük bir coğrafi kapsama sahip olmak isteyeceksiniz. Her iki görevi de elde etmek, Cosmos DB ile birlikte bir **anahtar oluşturma deneyimidir** .

Cosmos DB, dinamik bölümlemeyi destekler. Bu, belgenizdeki bir öznitelik olarak tanımlanan belirli bir **bölüm anahtarına**göre otomatik olarak bölümler oluşturur. Doğru bölüm anahtarını tanımlamak, tasarım zamanında yapılmalıdır. Daha fazla bilgi için bkz. [Azure Cosmos DB bölümlendirme](partitioning-overview.md).

Bir sosyal deneyim için bölümleme stratejinizi sorgulama ve yazma yöntemiyle hizalamanız gerekir. (Örneğin, aynı bölüm içindeki okumalar istenir ve birden çok bölüme yazma yayarak "etkin nokta" kullanmaktan kaçının.) Bazı seçenekler şunlardır: zamana bağlı bir anahtara (gün/ay/hafta) göre, içerik kategorisine, coğrafi bölgeye veya kullanıcıya göre bölümler. Bu tek şey, verileri nasıl sorgulayıp sosyal deneyiminizdeki verileri nasıl gösterdiğinize bağlıdır.

Cosmos DB, tüm bölümlerinizde bulunan sorguları ( [toplamalar](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)dahil) saydam olarak çalıştıracak, bu nedenle verileriniz büyüdükçe herhangi bir mantık eklemeniz gerekmez.

Zamanla, son olarak trafikte ve kaynak tüketiminize ( [Rus](request-units.md)cinsinden ölçülen veya istek birimlerinde) büyütireceksiniz. Kullanıcı tabanınız büyüdükçe daha sık okuyacaksınız ve yazılacak. Kullanıcı tabanı daha fazla içerik oluşturmaya ve okumaya başlayacaktır. Bu nedenle, **aktarım hızını ölçeklendirmenin** önem taşır. Ru 'nizi kolayca artırabilirsiniz. Azure portal veya [API aracılığıyla komut vererek](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer)bunu birkaç tıklamayla yapabilirsiniz.

![Bölüm anahtarını ölçekleme ve tanımlama](./media/social-media-apps/social-media-apps-scaling.png)

Şeyler daha iyi devam etmeleri durumunda ne olur? Başka bir bölge, ülke veya kıtaya ait kullanıcıların platformunuzu fark ettiğini ve uygulamayı kullanmaya başlamasını varsayalım. Harika bir sürpriz!

Ancak bekleyin! Kısa süre içinde platformunuzun deneyimlerini en iyi şekilde fark etmiş olursunuz. Bu durumda, gecikme bölgeinizden gecikmeye neden olan işlem bölgenize çok uzakta. Bunların sonlandırmasını istemediğiniz açıktır. **Küresel erişiminizi genişletmenin**kolay bir yolu var mı? Var!

Cosmos DB, verilerinizi birkaç tıklamayla küresel ve şeffaf bir şekilde [çoğaltmanıza](../cosmos-db/tutorial-global-distribution-sql-api.md) ve [istemci kodınızdan](../cosmos-db/tutorial-global-distribution-sql-api.md)kullanılabilir bölgeler arasından otomatik olarak seçim yapmanıza olanak sağlar. Bu işlem, [birden fazla yük devretme bölgesinin](high-availability.md)olabileceği anlamına da gelir.

Verilerinizi küresel olarak çoğalttığınızda, istemcilerinizin avantajlarından faydalanabildiğinden emin olmanız gerekir. Web ön ucu kullanıyorsanız veya mobil istemcilerden API 'Lere erişiyorsanız, [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) dağıtabilir ve Azure App Service istediğiniz tüm bölgelerde kopyalayabilir ve bu da genişletilmiş küresel kapsamınızı desteklemek için bir performans yapılandırması kullanabilirsiniz. İstemcileriniz ön uç veya API 'lerinize eriştiğinizde, bu, en yakın App Service yönlendirilir ve bu da yerel Cosmos DB çoğaltmasına bağlanır.

![Sosyal platformunuza genel kapsam ekleme](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Sonuç

Bu makalede, düşük maliyetli hizmetlerle Azure 'da tamamen sosyal ağlar oluşturma alternatiflerine yönelik bir ışık yer alır. sonuçları, "basamaklı der" adlı çok katmanlı bir depolama çözümünün ve veri dağıtımının kullanımını teşvik göre sunar.

![Sosyal ağ için Azure hizmetleri arasındaki etkileşim diyagramı](./media/social-media-apps/social-media-apps-azure-solution.png)

Truth, bu tür senaryolar için gümüş bir madde işareti yoktur. Harika deneyimler oluşturmamızı sağlayan harika hizmetler birleşimi tarafından oluşturulan sinerjiden bahsederek denemelerini: harika bir sosyal uygulama sağlamak için Azure Cosmos DB hız ve özgürlük, Azure bilişsel arama gibi birinci sınıf bir arama çözümünün arkasındaki zeka, Azure Uygulama Hizmetleri 'nin, büyük miktarlarda veri ve Azure Machine Learning analitik gücünü ve çok büyük miktarlarda verileri depolamak için Genişletilebilir Azure depolama ve Azure SQL veritabanı gibi önemli olmayan uygulamalar barındırmada esneklik süreçlerinize geri bildirimde bulunmak ve doğru içeriği doğru kullanıcılara sunmamıza yardımcı olması için bilgi ve zeka oluşturun.

## <a name="next-steps"></a>Sonraki adımlar

Cosmos DB için kullanım örnekleri hakkında daha fazla bilgi edinmek için bkz. [ortak Cosmos DB kullanım örnekleri](use-cases.md).
