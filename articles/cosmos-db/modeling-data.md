---
title: Azure Cosmos DB verileri modelleme
titleSuffix: Azure Cosmos DB
description: NoSQL veritabanlarında veri modelleme hakkında bilgi edinin, ilişkisel bir veritabanında ve belge veritabanında modelleme verileri arasındaki farklılıklar.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 523049ea3286445117f41147f3dd12a2c911d1ae
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755008"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Azure Cosmos DB veri modellemesi

Azure Cosmos DB gibi şemaya ücretsiz veritabanları olsa da, yapılandırılmamış ve yarı yapılandırılmış verilerin depolanmasını ve sorgulanmasını kolaylaştırır; performans ve ölçeklenebilirlik ve en düşük düzeyde hizmetin en iyi şekilde yararlanmak için veri modelinize bir süre harcamanız gerekir YT.

Veriler nasıl depolanır? Uygulamanız verileri nasıl alıp sorgular? Uygulamanızın okuma ağır mi yoksa koyu mı olduğunu mı var?

Bu makaleyi okuduktan sonra aşağıdaki soruları cevaplayabilirsiniz:

* Veri modelleme nedir ve neden ilgilenmeliyim?
* Azure Cosmos DB veri modellemesi, ilişkisel bir veritabanına göre farklı midir?
* Nasıl yaparım? ilişkisel olmayan bir veritabanında Express veri ilişkileri mi?
* Ne zaman veri ekleyebilirim ve verilere ne zaman bağlantı oluşturabilirim?

## <a name="embedding-data"></a>Veri katıştırma

Azure Cosmos DB ' de modelleme verileri başlattığınızda varlıklarınızı JSON belgeleri olarak temsil edilen **bağımsız öğeler** olarak işleme çalışın.

Karşılaştırma için, bir ilişkisel veritabanındaki verileri modelliyoruz. Aşağıdaki örnek, bir kişinin ilişkisel veritabanında nasıl depolandığını gösterir.

![İlişkisel veritabanı modeli](./media/sql-api-modeling-data/relational-data-model.png)

İlişkisel veritabanlarıyla çalışırken, strateji tüm verilerinizi normalleştirilemiyor. Verilerinizin normalleştirilmesi, genellikle kişi gibi bir varlık almayı ve ayrı bileşenlere doğru bir şekilde bölünmesini içerir. Yukarıdaki örnekte, bir kişi birden fazla kişi ayrıntısı kaydına ve birden çok adres kaydına sahip olabilir. İletişim ayrıntıları, bir tür gibi ortak alanlar daha fazla ayıklanarak daha fazla ayrılabilir. Aynı adres için de geçerlidir, her bir kayıt *Ev* veya *iş*türünde olabilir.

Verileri normalleştirirken temel alan, her kayıtta gereksiz verileri depolamayı ve bunun yerine verilere **başvurmaktan kaçınmaktır** . Bu örnekte, kişinin tüm iletişim ayrıntılarını ve adreslerini içeren bir kişiyi okumak için, çalışma zamanında verilerinizi etkin bir şekilde oluşturmak (veya yeniden kullanmak) için BIRLEŞTIRMELERI kullanmanız gerekir.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Tek bir kişinin kişi ayrıntıları ve adresleriyle güncelleştirilmesi, birçok ayrı tabloda yazma işlemleri gerektirir.

Şimdi de Azure Cosmos DB aynı verileri kendi kendine dahil edilen varlıkla modelliyoruz.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "addresses": [
            {
                "line1": "100 Some Street",
                "line2": "Unit 1",
                "city": "Seattle",
                "state": "WA",
                "zip": 98012
            }
        ],
        "contactDetails": [
            {"email": "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ]
    }

Yukarıdaki yaklaşımı kullanarak, kişi ayrıntıları ve adresleri gibi bu kişiyle **ilgili tüm bilgileri** *tek bir JSON* belgesine ekleyerek kişi kaydını **uzlaştırdık** .
Ayrıca, bir sabit şemayla sınırlandırmadığımızda, farklı şekillerin iletişim ayrıntılarını almak gibi şeyleri oluşturma esnekliği sunuyoruz.

Veritabanından bir bütün kişi kaydının alınması, artık tek bir kapsayıcıya ve tek bir öğeye karşı tek bir **okuma işlemidir** . Bir kişi kaydını, kişi ayrıntıları ve adresleriyle güncelleştirmek aynı zamanda tek bir öğe için **tek bir yazma işlemidir** .

Verilerin bir bütün olarak, genel işlemleri tamamlaması için daha az sorgu ve güncelleştirme vermesi gerekebilir.

### <a name="when-to-embed"></a>Ne zaman gömüleceği

Genel olarak, şu durumlarda ekli veri modellerini kullanın:

* Varlıklar arasında **Kapsanan** ilişkiler vardır.
* Varlıklar arasında **bire bir** ilişki vardır.
* **Seyrek olarak değişen**gömülü veriler vardır.
* **Bağlantılı olmadan**artmayacak gömülü veriler var.
* **Birlikte sık sorgulanan**Ekli veriler vardır.

> [!NOTE]
> Genellikle, yoğun veri modelleri daha iyi **okuma** performansı sağlar.

### <a name="when-not-to-embed"></a>Katıştırılamaz

Azure Cosmos DB 'daki Thumb kuralı her şeyi eşit hale getirme ve tüm verileri tek bir öğeye ekleme işlemi yaparken, bu durum kaçınılması gereken bazı durumlara yol açabilir.

Bu JSON kod parçacığını alın.

    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "comments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            …
            {"id": 100001, "author": "jane", "comment": "and on we go ..."},
            …
            {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
            …
            {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
        ]
    }

Bu, genel bir blog veya CMS, sistem modelliyoruz, gömülü açıklamalara sahip bir post varlığının nasıl görüneceğine benzeyebilir. Bu örnekteki sorun, Comments dizisinin **sınırsız**olması, yani tek bir gönderinin sahip olduğu açıklama sayısında (pratik) bir sınır olmadığı anlamına gelir. Öğenin boyutu sonsuz büyük büyüyerek bu sorun oluşabilir.

Öğenin boyutu, verilerin kablo üzerinden iletilmesi ve öğe okuma ve güncelleştirme özelliğinin ölçeğini büyürken, bu, ölçeğe karşı etkilenir.

Bu durumda, aşağıdaki veri modelini göz önünde bulundurmanız daha iyidir.

    Post item:
    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "recentComments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            {"id": 3, "author": "jane", "comment": "....."}
        ]
    }

    Comment items:
    {
        "postId": "1"
        "comments": [
            {"id": 4, "author": "anon", "comment": "more goodness"},
            {"id": 5, "author": "bob", "comment": "tails from the field"},
            ...
            {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
        ]
    },
    {
        "postId": "1"
        "comments": [
            {"id": 100, "author": "anon", "comment": "yet more"},
            ...
            {"id": 199, "author": "bored", "comment": "will this ever end?"}
        ]
    }

Bu modelde, sabit bir öznitelik kümesi olan bir dizi olan post kapsayıcısına gömülü en son üç açıklama bulunur. Diğer açıklamalar 100 açıklama toplu işleri halinde gruplandırılır ve ayrı öğeler olarak depolanır. Kurgusal uygulamamız kullanıcının aynı anda 100 açıklamalarını yüklemesine izin verdiğinden Batch 'in boyutu 100 olarak seçilmiştir.  

Verilerin gömülmesi iyi bir fikir olmak üzere, gömülü verilerin genellikle öğeler arasında kullanıldığı ve sıklıkla değiştirileceği bir diğer durumdur.

Bu JSON kod parçacığını alın.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            {
                "numberHeld": 100,
                "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
            },
            {
                "numberHeld": 50,
                "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
            }
        ]
    }

Bu, bir kişinin hisse senedi portföyünü temsil eder. Hisse senedi bilgilerini her bir portföy belgesine eklemeyi seçtik. Hisse senedi yapan bir ticaret uygulaması gibi ilgili verilerin sıklıkla değiştiği bir ortamda, sık sık değişen verilerin gömülmesi, her bir stok her seferinde her bir portföy belgesini sürekli güncelleştirdiğiniz anlamına gelir.

*Zaza* , tek bir günde yüzlerce kez, binlerce kullanıcının de portföyüne bir *zaza* sahip olabilir. Yukarıdaki gibi bir veri modeliyle, her gün her gün en iyi ölçeklendirmeyecek bir sisteme birçok kez binlerce portföy belgesini güncelleştirmemiz gerekir.

## <a name="referencing-data"></a>Verilere başvurma

Verilerin gömülmesi çok sayıda durum için çalışır, ancak verilerinizin normalleştirilmesi sırasında daha fazla soruna neden olur. Şimdi ne yapabiliriz?

İlişkisel veritabanları, varlıklar arasında ilişkiler oluşturabileceğiniz tek yer değildir. Belge veritabanında, bir belgede diğer belgelerdeki verilerle ilgili bilgiler alabilirsiniz. Azure Cosmos DB veya başka bir belge veritabanında ilişkisel bir veritabanına daha uygun olan sistemlerin oluşturulmasını önermiyoruz, ancak basit ilişkiler ince ve yararlı olabilir.

Aşağıdaki JSON 'da, daha önce bir hisse senedi portföyünün örneğini kullanmayı seçtik, ancak bu süre bu kez portföyündeki stok öğesine katıştırıyoruz. Bu şekilde, hisse senedi öğesi gün boyunca sık değişiyorsa, tek stok belgesi, güncelleştirilmesi gereken tek belgedir.

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }

    Stock documents:
    {
        "id": "1",
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": "2",
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }

Bu yaklaşımın hemen bir yanındaki, uygulamanızın bir kişinin portföyünü görüntülerken tuttuğu her bir stok hakkındaki bilgileri göstermesi gerekir. Bu durumda, her hisse senedi belgesi için bilgileri yüklemek üzere veritabanına birden çok gidiş dönüş yapmanız gerekir. Burada, gün boyunca sıklıkla gerçekleşen yazma işlemlerinin verimliliğini artırmaya yönelik bir karar verdik, ancak bu belirli sistem performansı üzerinde daha az etkisi olan okuma işlemlerine zarar verdik.

> [!NOTE]
> Normalleştirilmiş veri modelleri sunucuya **daha fazla gidiş dönüş gerektirebilir** .

### <a name="what-about-foreign-keys"></a>Yabancı anahtarlar ne olacak?

Şu anda kısıtlama, yabancı anahtar veya başka türlü bir kavram olmadığından, belgelerinizde bulunan belge içi ilişkilerin etkili bir şekilde "zayıf bağlantı" olması ve veritabanının kendisi tarafından doğrulanmayacak olması gerekir. Bir belgenin gerçekten mevcut olduğundan emin olmak istiyorsanız, bunu uygulamanızda yapmanız veya Azure Cosmos DB üzerinde sunucu tarafı Tetikleyicileri veya saklı yordamların kullanılması gerekir.

### <a name="when-to-reference"></a>Ne zaman başvurulacak

Genel olarak, şu durumlarda normalleştirilmiş veri modellerini kullanın:

* **Bire çok** ilişkilerini temsil etme.
* **Çoka** çok ilişkileri temsil eden.
* İlgili veri **değişiklikleri sıklıkla**yapılır.
* Başvurulan verilerin **sınırsız**olması olabilir.

> [!NOTE]
> Genellikle normalleştirici daha iyi **yazma** performansı sağlar.

### <a name="where-do-i-put-the-relationship"></a>İlişkiyi nereye yerleştiririm?

İlişkinin büyümesi başvuruyu hangi belgenin depolayaceğini belirlemenize yardımcı olur.

Aşağıda, yayımcıları ve kitapları modelleyen JSON ' a bakacağız.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press",
        "books": [ 1, 2, 3, ..., 100, ..., 1000]
    }

    Book documents:
    {"id": "1", "name": "Azure Cosmos DB 101" }
    {"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure Cosmos DB" }
    ...
    {"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }

Yayımcı başına kitap sayısı sınırlı büyüme ile küçükse, kitap başvurusunu yayımcı belgesi içinde depolamak yararlı olabilir. Ancak, yayımcı başına kitap sayısı sınırsız ise, bu veri modeli yukarıdaki örnek yayımcı belgesinde olduğu gibi değişebilir, büyüyen diziler oluşmasına neden olur.

Bir bit içindeki şeyleri değiştirmek, yine de aynı verileri temsil eden bir modelin oluşmasına neden olur, ancak artık bu büyük kesilebilir koleksiyonları engeller.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }

    Book documents:
    {"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
    {"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}

Yukarıdaki örnekte, yayımcı belgesinde, sınırlandırılmamış koleksiyonu bırakıldık. Bunun yerine, her kitap belgesinde yayımcıya yalnızca bir başvuruyoruz.

### <a name="how-do-i-model-manymany-relationships"></a>Nasıl yaparım? modeli çok: çok ilişki?

İlişkisel bir veritabanında *birçok: birçok* ilişki genellikle kayıtları diğer tablolardan birlikte birleştiren JOIN tablolarıyla modellenir.

![Tabloları Birleştir](./media/sql-api-modeling-data/join-table.png)

Belgeleri kullanarak aynı şeyi çoğaltmak ve aşağıdakine benzer bir veri modeli oluşturmak isteyebilirsiniz.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

    Joining documents:
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

Bu işe çalışacaktır. Ancak, kendi kitaplarıyla bir yazar yüklemek veya yazarı ile bir kitabı yüklemek her zaman veritabanında en az iki ek sorgu gerektirir. Birleştirme belgesinde bir sorgu ve sonra da birleştirilen gerçek belgeyi getirmek için başka bir sorgu.

Bu birleştirme tablosu varsa, iki veri parçasına bir araya gelir ve neden tamamen düşürülemiyor?
Aşağıdakileri göz önünde bulundurun.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

Şimdi bir yazardaysam, hangi kitaplarınızın yazıldığını hemen öğrendim ve bunun tersine, bir kitap belgesi yüklüyse yazarın kimliklerini öğreniyorum. Bu, bu aracı sorgusunu, uygulamanızın yapması gereken sunucu gidiş dönüş sayısını azaltarak JOIN tablosuna kaydeder.

## <a name="hybrid-data-models"></a>Karma veri modelleri

Artık veri ekleme (veya normalleştirilmişi) ve başvuru (ya da normalleştiriyoruz), her birinin onların kenarları ve gördük.

Her zaman ya da olmaması gerekmez, çok az bir şeyi karıştırmayın.

Uygulamanızın belirli kullanım desenlerine ve iş yüklerine bağlı olarak, gömülü ve başvurulan verilerin karıştırıldığı durumlar olabilir ve iyi bir performans düzeyini sürdürirken daha az sunucu gidiş dönüşleriyle daha basit uygulama mantığına yol açabilir.

Aşağıdaki JSON 'ı göz önünde bulundurun.

    Author documents:
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "countOfBooks": 3,
        "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "https://....png"}
            {"profile": "https://....png"}
            {"large": "https://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "https://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        ]
    }

Burada (çoğunlukla), diğer varlıklardaki verilerin en üst düzey belgeye katıştırıldığı, ancak diğer verilere başvurulduğu, gömülü modeli izliyoruz.

Kitap belgesine bakarsanız, yazarların dizisine baktığımızda birkaç ilginç alan görebiliriz. Bir yazar belgesine, Normalleştirilmemiş bir modelde standart uygulamaya geri başvurabileceğiniz alan olan bir `id` alanı vardır ancak `name` ve `thumbnailUrl`de vardır. "LINK" kullanarak ilgili yazar belgesinden gereken ek bilgileri almak için `id` ve uygulamayı sola yazdık, ancak uygulamamız her kitapta görüntülenen yazarın adını ve küçük resim resmini görüntülediğinden , yazardaki **bazı** verileri kaldırarak bir listedeki kitap başına sunucuya gidiş dönüş tasarrufu sağlayabilir.

Yazarın adı değiştiyse ya da fotoğraflarını güncelleştirmek istiyorlarsa, yazarların adlarını sık değiştirmeme varsayımına bağlı olarak, bu, tüm kullanıcıların yayımladıkları her kitabı, ancak uygulamamız için bir yandan güncelleştirmemiz gerekir. Bu, kabul edilebilir bir tasarım karardır.  

Örnekte, bir okuma işleminde pahalı işleme kaydetmek için **önceden hesaplanmış toplamalar** değerleri vardır. Örnekte, yazar belgesine katıştırılmış verilerden bazıları çalışma zamanında hesaplanan verilerden oluşur. Her yeni kitap yayımlandığında, bir kitap belgesi oluşturulur **ve** countOfBooks alanı, belirli bir yazar için mevcut olan kitap belgelerinin sayısına göre hesaplanmış bir değere ayarlanır. Bu iyileştirme, okuma işlemlerini en iyi duruma getirmek için yazma işlemleri yapmak için uygun olan okuma ağır sistemlerde iyi hale getirilir.

Azure Cosmos DB **çok belgeli işlemleri**desteklediğinden, önceden hesaplanmış alanları olan bir modele sahip olabilme olasılığı vardır. Birçok NoSQL deposu belgelerde işlem yapılamaz ve bu nedenle, bu sınırlama nedeniyle "her şeyi her zaman katıştır" gibi tasarım kararları verebilir. Azure Cosmos DB ile, bir ACID işlemi içinde kitaplar ekleyen ve yazarları güncelleştiren sunucu tarafı tetikleyicilerini veya saklı yordamları kullanabilirsiniz. Artık verilerinizin tutarlı kalmasını sağlamak için her şeyi tek bir belgeye katıştırmanız **gerekmez.**

## <a name="distinguishing-between-different-document-types"></a>Farklı belge türleri arasında ayrım

Bazı senaryolarda, aynı koleksiyonda farklı belge türlerini karıştırmak isteyebilirsiniz; Bu, genellikle birden çok, ilişkili belgenin aynı [bölümde](partitioning-overview.md)yer olmasını istediğiniz durumdur. Örneğin, hem kitap hem de kitap incelemelerini aynı koleksiyona yerleştirebilir ve `bookId`göre bölümleyebilirsiniz. Böyle bir durumda, genellikle bunları ayırt etmek için türlerini tanımlayan bir alan ile belgelerinize eklemek istersiniz.

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "bookId": "b1",
        "type": "book"
    }

    Review documents:
    {
        "id": "r1",
        "content": "This book is awesome",
        "bookId": "b1",
        "type": "review"
    },
    {
        "id": "r2",
        "content": "Best book ever!",
        "bookId": "b1",
        "type": "review"
    }

## <a name="next-steps"></a>Sonraki adımlar

Bu makaledeki en büyük nokta, bir şemadan ücretsiz bir dünyada veri modellemesinin her zaman önemli olduğunu anlamaktır.

Bir ekrandaki verilerin bir parçasını göstermenin tek bir yolu olmadığı gibi, verilerinizi modeletmenin tek bir yolu yoktur. Uygulamanızı ve verilerin nasıl oluşturulacağını, tüketilmesi ve işleyeceğini anlamanız gerekir. Ardından burada sunulan yönergelerin bazılarını uygulayarak uygulamanızın hemen ihtiyaçlarını karşılayan bir model oluşturma hakkında ayarlayabilirsiniz. Uygulamalarınızın değiştirilmesi gerektiğinde, şema içermeyen bir veritabanının esnekliğinden yararlanarak veri modelinizi kolayca değiştirip bu verileri de daha kolay bir şekilde geliştirebilirsiniz.

Azure Cosmos DB hakkında daha fazla bilgi edinmek için hizmetin [Belgeler](https://azure.microsoft.com/documentation/services/cosmos-db/) sayfasına bakın.

Verilerinizin birden çok bölüme nasıl atıfta olduğunu anlamak için [Azure Cosmos DB bölümünde bölümlendirme verilerine](sql-api-partition-data.md)bakın.

Gerçek dünyada bir örnek kullanarak Azure Cosmos DB verileri modellemeyi ve Bölümlendirmeyi öğrenmek için, [veri modelleme ve bölümleme-gerçek hayatta bir örnek](how-to-model-partition-example.md)bölümüne bakın.
