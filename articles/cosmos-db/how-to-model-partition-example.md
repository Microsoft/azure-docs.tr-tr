---
title: Gerçek dünyada bir örnekle Azure Cosmos DB verileri modelleme ve bölümleme
description: Azure Cosmos DB Core API 'sini kullanarak gerçek dünyada bir örnek modelleyip bölümleyeceğinizi öğrenin
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/23/2019
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: d2f35ae7a6110acb2ca89bdaeb487eddabf84923
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98185827"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Gerçek dünyadan bir örnek kullanarak Azure Cosmos DB'de verileri modelleme ve bölümleme
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede, [veri modelleme](modeling-data.md), [bölümleme](partitioning-overview.md)ve [sağlanan aktarım hızı](request-units.md) gibi çeşitli Azure Cosmos DB kavramlarıyla ilgili olarak gerçek bir veri tasarımı denemesinin nasıl üstesinden geldiğini göstermek için derleme yapılır.

Genellikle ilişkisel veritabanları ile çalışıyorsanız, büyük olasılıkla bir veri modelinin nasıl tasarlanacağını gösteren alışkanlıkları ve kavramları oluşturabilirsiniz. Ayrıca, belirli kısıtlamalar nedeniyle Azure Cosmos DB benzersiz güçlerinin yanı sıra, bu en iyi uygulamalardan çoğu doğru şekilde çevrilmez ve sizi en uygun çözümlere sürükleyebilirsiniz. Bu makalenin amacı, öğe modellemesinin varlık birlikte bulundurma ve kapsayıcı bölümlendirme olarak Azure Cosmos DB bir gerçek dünya kullanım örneğini modellemenin tam sürecinde size rehberlik sağlamaktır.

Bu makaledeki kavramları gösteren [topluluk tarafından oluşturulan bir kaynak kodunu indirin veya görüntüleyin](https://github.com/jwidmer/AzureCosmosDbBlogExample) . Bu kod örneği bir topluluk katılımcısı tarafından katkıda bulunulmuş ve Azure Cosmos DB ekibi, bakımını desteklemiyor.

## <a name="the-scenario"></a>Senaryo

Bu alıştırmada, *kullanıcıların* *gönderi* oluşturbilecekleri bir blog platformunun etki alanını göz önünde bulunduracağız. Kullanıcılar *Ayrıca bu* gönderilere yorum ekleyebilir ve bu postalara *yorum* ekleyebilir.

> [!TIP]
> Bazı kelimeleri *italik* olarak vurgulıyoruz; Bu sözcükler, modelimizin ' i işlemek zorunda olacağı "şeyleri" türünü belirler.

Belirtimize daha fazla gereksinim ekleniyor:

- Ön sayfa, son oluşturulan gönderilerin akışını görüntüler,
- Bir kullanıcının tüm gönderilerini, bir gönderiye ilişkin tüm açıklamaları ve bir gönderiye ilişkin tüm beğeni getirebilirsiniz.
- Gönderimler, yazarların Kullanıcı adı ve kaç yorum ve beğendikleri bir sayı ile döndürülür,
- Açıklamalar ve beğeni, bunları oluşturan kullanıcıların Kullanıcı adı ile de döndürülür,
- Liste olarak görüntülendiğinde, gönderilerin yalnızca içeriğinin kesilmiş bir özetini sunması gerekir.

## <a name="identify-the-main-access-patterns"></a>Ana erişim desenlerini tanımla

Başlamak için çözümünüzün erişim desenlerini tanımlayarak ilk belirtimize bazı yapılara izin veririz. Azure Cosmos DB için bir veri modeli tasarlarken, modelin bu istekleri verimli bir şekilde sunacağına emin olmak için modelinizin hangi isteklerin sunacağına anlaşılması önemlidir.

Genel işlemi daha kolay hale getirmek için, bu farklı istekleri komut veya sorgu olarak sınıflandırıyoruz. Bu, [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) 'den bazı sözlük (örneğin, sistemi güncelleştirme amaçları) ve sorguları salt okuma isteklerdir.

Platformumuzu göstermek için gereken isteklerin listesi aşağıda verilmiştir:

- **[C1]** Kullanıcı Oluştur/Düzenle
- **[S1]** Kullanıcı alma
- **[C2]** Gönderi Oluştur/Düzenle
- **[S2]** Gönderi alma
- **[Q3]** Kullanıcı Gönderilerini kısa biçimde listeleme
- **[C3]** Açıklama oluşturma
- **[S4]** Bir postanın açıklamalarını listeleme
- **[C4]** Gönderi gibi
- **[Q5]** Bir postanın beğeneni listeleyin
- **[Q6]** Kısa bir biçimde (akış) oluşturulan en son *x* gönderilerini listeleyin

Bu aşamada, her bir varlığın (Kullanıcı, gönderi vs.) içereceği ayrıntıları düşünmemiş. Bu adım genellikle, bu varlıkların tablolar, sütunlar, yabancı anahtarlar vb. açısından nasıl çevrileceğini anlamak zorunda olduğumuz için, ilişkisel bir mağazaya göre tasarlanırken ilk olarak, bir ilişkisel depoya göre tasarlanırken ilk olanların arasındadır. Yazma sırasında hiçbir şemayı zorlayameyen bir belge veritabanı ile ilgili bir kaygıdan çok daha azdır.

Başlangıçtan itibaren erişim modellerimizi belirlemek için önemli olmasının önemi, çünkü bu istek listesi test paketimize gidiyor. Veri modelimizi her tekrarlıyoruz, her bir isteği ele alacak ve performansını ve ölçeklenebilirliğini denetlemeye devam edeceğiz. Her modelde tüketilen istek birimlerini hesaplar ve bunları iyileştirir. Tüm bu modeller varsayılan dizin oluşturma ilkesini kullanır ve belirli özellikleri dizinleyerek geçersiz kılabilir, bu da RU tüketimini ve gecikmesini daha da iyileştirebilir.

## <a name="v1-a-first-version"></a>V1: ilk sürüm

İki kapsayıcıyla başlıyoruz: `users` ve `posts` .

### <a name="users-container"></a>Kullanıcı kapsayıcısı

Bu kapsayıcı yalnızca Kullanıcı öğelerini depolar:

```json
{
    "id": "<user-id>",
    "username": "<username>"
}
```

Bu kapsayıcıyı tarafından bölümliyoruz, yani bu kapsayıcı `id` içindeki her mantıksal bölüm yalnızca bir öğe içerecekse.

### <a name="posts-container"></a>Gönderi kapsayıcısı

Bu kapsayıcı gönderi, yorum ve beğeni barındırır:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "title": "<post-title>",
    "content": "<post-content>",
    "creationDate": "<post-creation-date>"
}

{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "creationDate": "<like-creation-date>"
}
```

Bu kapsayıcıyı tarafından bölümliyoruz `postId` , yani bu kapsayıcı içindeki her mantıksal bölüm bir gönderi, bu gönderiyle ilgili tüm yorumlar ve bu gönderiyle ilgili tüm beğeni içerir.

Bu kapsayıcının `type` barındırdığı üç varlık türü arasında ayrım yapmak için bu kapsayıcıda depolanan öğelerde bir özellik sunduğumuz unutulmamalıdır.

Ayrıca, eklemek yerine ilgili verilere başvurmayı seçtik (bu kavramlarla ilgili ayrıntılar için [Bu bölüme](modeling-data.md) bakın):

- bir kullanıcının kaç tane posta oluştur, bir üst sınırı yoktur,
- gönderimler oldukça uzun olabilir,
- bir gönderinin kaç yorum ve beğeni olabilir,
- Post 'un kendisini güncelleştirmek zorunda kalmadan bir yorum veya bir gönderinin gibi bir yorum ekleyebilmesini istiyoruz.

## <a name="how-well-does-our-model-perform"></a>Modelimiz ne kadar iyi çalışıyor?

İlk sürümümüzün performansını ve ölçeklenebilirliğini değerlendirmek artık zaman alabilir. Daha önce tanımlanan isteklerin her biri için, gecikme süresini ve tükettiği istek birimi sayısını ölçyoruz. Bu ölçüm, Kullanıcı başına 5 ile 50 arasında bir ileti ve posta başına en fazla 25 yorum ve 100 beğeni olan 100.000 kullanıcı içeren bir kukla veri kümesine karşı yapılır.

### <a name="c1-createedit-a-user"></a>= Kullanıcı Oluştur/Düzenle

Yalnızca kapsayıcıda bir öğe oluştururken veya güncelleştirtiğimiz için bu istek basittir `users` . İstekler `id` bölüm anahtarına teşekkür ederiz.

:::image type="content" source="./media/how-to-model-partition-example/V1-C1.png" alt-text="Kullanıcılar kapsayıcısına tek bir öğe yazma" border="false":::

| **Gecikme süresi** | **RU ücreti** | **Performans** |
| --- | --- | --- |
| 7 MS | 5,71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>Q1 Kullanıcı alma

Kullanıcının alınması, kapsayıcıdan karşılık gelen öğe okunarak yapılır `users` .

:::image type="content" source="./media/how-to-model-partition-example/V1-Q1.png" alt-text="Kullanıcılar kapsayıcısından tek bir öğe alma" border="false":::

| **Gecikme süresi** | **RU ücreti** | **Performans** |
| --- | --- | --- |
| 2 MS | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>Geç Gönderi Oluştur/Düzenle

**[C1]** benzer şekilde, kapsayıcıya yazmanız yeterlidir `posts` .

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Gönderi kapsayıcısına tek bir öğe yazma" border="false":::

| **Gecikme süresi** | **RU ücreti** | **Performans** |
| --- | --- | --- |
| 9 MS | 8,76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>Üç Gönderi alma

İlgili belgeyi kapsayıcıdan alarak başlayacağız `posts` . Bu, belirtimizin uyarınca, bu kadar çok sayıda yorum ve bu gönderideki kaç tane beğeneni (3 ek SQL sorgusunun verilmek üzere) toplamamız gerekir.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q2.png" alt-text="Gönderi alma ve ek veri toplama" border="false":::

Ek sorguların her biri, ilgili kapsayıcısının bölüm anahtarında filtreleyip performansı ve ölçeklenebilirliği en üst düzeye çıkarmak istiyoruz. Ancak sonunda, tek bir gönderi döndürmek için dört işlem gerçekleştirmemiz gerekir, bu nedenle bir sonraki yinelemede bunu iyileştireceğiz.

| **Gecikme süresi** | **RU ücreti** | **Performans** |
| --- | --- | --- |
| 9 MS | 19,54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>S3 Kullanıcı Gönderilerini kısa biçimde listeleme

İlk olarak, belirli bir kullanıcıya karşılık gelen gönderileri getiren bir SQL sorgusuyla istenen gönderileri almamız gerekir. Ancak yazarın Kullanıcı adını ve yorumların sayısını ve beğeni toplamak için ek sorgular da vermek istiyoruz.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q3.png" alt-text="Bir kullanıcının tüm gönderilerini alma ve ek verilerini toplama" border="false":::

Bu uygulama birçok sakıncalar sunar:

- ilk sorgu tarafından döndürülen her bir gönderi için açıklama ve beğeni toplanan sorguların verilmesi gerekir,
- ana sorgu kapsayıcının bölüm anahtarını filtrelemez `posts` , bir fanı ve kapsayıcı genelinde bir bölüm taraması yapar.

| **Gecikme süresi** | **RU ücreti** | **Performans** |
| --- | --- | --- |
| 130 MS | 619,41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>C3 Açıklama oluşturma

Kapsayıcıda karşılık gelen öğe yazılarak bir açıklama oluşturulur `posts` .

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Gönderi kapsayıcısına tek bir öğe yazma" border="false":::

| **Gecikme süresi** | **RU ücreti** | **Performans** |
| --- | --- | --- |
| 7 MS | 8,57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>Ç Bir postanın açıklamalarını listeleme

Bu gönderiyle ilgili tüm açıklamaları getiren bir sorgu ile başlıyoruz ve bir kez daha, her yorum için de Kullanıcı adlarını ayrı olarak topladık.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q4.png" alt-text="Bir gönderi için tüm açıklamaları alma ve ek verilerini toplama" border="false":::

Ana sorgu kapsayıcının bölüm anahtarına filtre uygulayabilir, ancak kullanıcı adlarını genel performansa göre ayrı olarak toplayarak penalizes. Daha sonra bu sürümü iyileştireceğiz.

| **Gecikme süresi** | **RU ücreti** | **Performans** |
| --- | --- | --- |
| 23 MS | 27,72 RU | ⚠ |

### <a name="c4-like-a-post"></a>C4 Gönderi gibi

Yalnızca **[C3]** gibi, kapsayıcıda karşılık gelen öğeyi oluşturacağız `posts` .

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Gönderi kapsayıcısına tek bir öğe yazma" border="false":::

| **Gecikme süresi** | **RU ücreti** | **Performans** |
| --- | --- | --- |
| 6 MS | 7,05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Bir postanın beğeneni listeleyin

Tıpkı **[S4]** gibi, bu gönderi için beğeneni sorgulıyoruz ve sonra Kullanıcı adlarını topladık.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q5.png" alt-text="Bir gönderi için tüm beğeni alma ve ek verilerini toplama" border="false":::

| **Gecikme süresi** | **RU ücreti** | **Performans** |
| --- | --- | --- |
| 59 MS | 58,92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Kısa bir biçimde (akış) oluşturulan en son x gönderilerini listeleyin

`posts`Kapsayıcıyı azalan oluşturma tarihine göre sıralanmış olarak sorgulayarak en son gönderileri, sonra da her gönderi için Kullanıcı adlarını ve açıklama sayılarını ve beğeni toplar.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q6.png" alt-text="En son postaların alınması ve ek verilerinin toplamı alınıyor" border="false":::

Bir kez daha, ilk sorgumuz kapsayıcının bölüm anahtarını filtreleyip `posts` maliyetli bir fanı tetikler. Bu, çok daha büyük bir sonuç kümesini hedeflediğimizden ve sonuçları bir yan tümcesiyle sıraladığımızda daha kötüleştik, bu da `ORDER BY` İstek birimleri açısından daha pahalı hale gelir.

| **Gecikme süresi** | **RU ücreti** | **Performans** |
| --- | --- | --- |
| 306 MS | 2063,54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>V1 performansını yansıtma

Önceki bölümde karşılaştığı performans sorunlarına bakarak iki ana sorun sınıfını tanımlayabiliriz:

- bazı istekler, dönmesi gereken tüm verilerin toplanması için birden çok sorgunun verilmesini gerektirir,
- Bazı sorgular, hedefledikleri kapsayıcıların bölüm anahtarını üzerine filtreleyip ölçeklenebilirlik sağlayan bir fanı sağlar.

Bu sorunlardan her birini, birinciyle başlayarak çözelim.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: okuma sorgularını iyileştirmek için bir denormalleştirme

Bazı durumlarda ek istek verme nedenimiz nedeni, ilk isteğin sonuçlarının dönmesi gereken tüm verileri içermemesi nedenidir. Azure Cosmos DB gibi, ilişkisel olmayan bir veri deposuyla çalışırken, bu tür bir sorun genellikle veri kümesi genelinde verilerin uzlaştırılması yoluyla çözülür.

Bizim örneğimizde gönderi yazarının Kullanıcı adını, açıklama sayısını ve beğeni sayısını eklemek için öğe gönderi ' ı değiştirirsiniz:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Ayrıca, açıklamaları ve bunları oluşturan kullanıcının Kullanıcı adını eklemek için öğeleri de değiştirin:

```json
{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "userUsername": "<comment-author-username>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "userUsername": "<liker-username>",
    "creationDate": "<like-creation-date>"
}
```

### <a name="denormalizing-comment-and-like-counts"></a>Açıklama ve LIKE sayıları

Her ne zaman bir yorum veya benzer bir şekilde bir açıklama eklediğimiz, `commentCount` ilgili postadaki veya öğesini de arttık `likeCount` . `posts`Kapsayıcımız tarafından bölümlendiğimiz `postId` için, yeni öğe (açıklama veya benzer) ve karşılık gelen gönderi aynı mantıksal bölümde yer. Sonuç olarak, bu işlemi gerçekleştirmek için bir [saklı yordam](stored-procedures-triggers-udfs.md) kullanabiliriz.

Artık bir yorum (**[C3]**) oluştururken, kapsayıcıda yalnızca yeni bir öğe eklemek yerine, `posts` Bu kapsayıcıda aşağıdaki saklı yordamı çağırdık:

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

Bu saklı yordam, post 'un KIMLIĞINI ve yeni açıklamanın gövdesini parametre olarak alır, ardından:

- Gönderiyi alır
- Şunu artırır `commentCount`
- Gönderi yerini alır
- yeni açıklamayı ekler

Saklı yordamlar atomik işlem olarak yürütüldüğü için, değeri `commentCount` ve gerçek açıklama sayısı her zaman eşitlenmiş durumda kalır.

Arttırmak için yeni beğeni eklerken benzer bir saklı yordam çağırdık `likeCount` .

### <a name="denormalizing-usernames"></a>Kullanıcı adlarını denormallaştırın

Kullanıcı adları, kullanıcılar farklı bölümlerde, ancak farklı bir kapsayıcıda yer aldığı için farklı bir yaklaşım gerektirir. Verileri bölümler ve kapsayıcılar arasında normalleştirmeye yönelik olması gerektiğinde, kaynak kapsayıcısının [değişiklik akışını](change-feed.md)kullanabiliriz.

Bizim örneğimizde, `users` Kullanıcıların Kullanıcı adlarını güncelleştirdiğinde tepki vermek için kapsayıcının değişiklik akışını kullanırız. Bu durumda, kapsayıcıda başka bir saklı yordam çağırarak değişikliği yayıyoruz `posts` :

:::image type="content" source="./media/how-to-model-partition-example/denormalization-1.png" alt-text="Gönderi kapsayıcısına Kullanıcı adları eşitleniyor" border="false":::

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();
  
  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

Bu saklı yordam, kullanıcının KIMLIĞINI ve kullanıcının yeni kullanıcı adını parametreler olarak alır, ardından:

- ile eşleşen tüm öğeleri getirir `userId` (gönderi, açıklamalar veya beğeni olabilir)
- Bu öğelerin her biri için
  - Şunu değiştirir `userUsername`
  - öğeyi değiştirir

> [!IMPORTANT]
> Bu işlem, kapsayıcının her bölümünde bu saklı yordamın yürütülmesini gerektirdiğinden maliyetlidir `posts` . Kullanıcıların, kayıt sırasında uygun bir Kullanıcı adı seçeceğini ve bunu değiştirmeyeceği varsayıyoruz. bu nedenle, bu güncelleştirme çok seyrek çalışacaktır.

## <a name="what-are-the-performance-gains-of-v2"></a>V2 'nin performans artışı nelerdir?

### <a name="q2-retrieve-a-post"></a>Üç Gönderi alma

Artık izin verdiğimiz için, bu isteği işlemek üzere yalnızca tek bir öğe getirmesi gerekir.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q2.png" alt-text="Gönderi kapsayıcısından tek bir öğe alma" border="false":::

| **Gecikme süresi** | **RU ücreti** | **Performans** |
| --- | --- | --- |
| 2 MS | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>Ç Bir postanın açıklamalarını listeleme

Burada, Kullanıcı adlarını getirilen ek istekleri yedekleyebilir ve bölüm anahtarında filtre uygulayan tek bir sorgu ile bitiyoruz.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q4.png" alt-text="Gönderi için tüm açıklamaları alma" border="false":::

| **Gecikme süresi** | **RU ücreti** | **Performans** |
| --- | --- | --- |
| 4 MS | 7,72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Bir postanın beğeneni listeleyin

Beğeni listelenirken aynı durum kesin.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q5.png" alt-text="Gönderi için tüm beğeni alma" border="false":::

| **Gecikme süresi** | **RU ücreti** | **Performans** |
| --- | --- | --- |
| 4 MS | 8,92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: tüm isteklerin ölçeklenebilir olduğundan emin olma

Genel performans geliştirmelerimize baktığınızda, tam olarak iyileştirildiğimiz iki istek vardır: **[Q3]** ve **[Q6]**. Bunlar, hedefleytikleri kapsayıcıların bölüm anahtarını filtrelememe sorguları içeren isteklerdir.

### <a name="q3-list-a-users-posts-in-short-form"></a>S3 Kullanıcı Gönderilerini kısa biçimde listeleme

Bu istek, ek sorguları kapsayan v2 sürümünde tanıtılan geliştirmelerden zaten faydalanır.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q3.png" alt-text="Kısa biçimli bir kullanıcının gönderilerini listelemek üzere sorgu gösteren diyagram." border="false":::

Ancak kalan sorgu hala kapsayıcının bölüm anahtarında filtrelenemiyor `posts` .

Bu durumu göz önünde bulundurmanız, aslında basittir:

1.  `userId` Belirli bir kullanıcı için tüm gönderileri getirmek istiyoruz, bu isteğin üzerine filtre uygulamak istiyor
1. `posts`Şu şekilde bölümlenmemiş çünkü kapsayıcıya göre bölümlenmemiş`userId`
1. Belirgin *bir şekilde,* bu isteği tarafından bölümlenen bir kapsayıcıya karşı yürüterek performans sorunumuzu çözeceğiz. `userId`
1. Zaten bu tür bir kapsayıcınıza sahip olduğumuz `users` bir kapsayıcıdır.

Bu nedenle, tüm gönderileri kapsayıcıyla çoğaltarak ikinci bir kat düzeyi sunuyoruz `users` . Bunu yaparak, gönderilerimizin bir kopyasını, yalnızca farklı boyutlarda bölümleyerek, bu sayede daha verimli bir şekilde elde eteceğiz `userId` .

`users`Kapsayıcıda artık 2 tür öğe var:

```json
{
    "id": "<user-id>",
    "type": "user",
    "userId": "<user-id>",
    "username": "<username>"
}

{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Şunlara dikkat edin:

- `type`kullanıcıları gönderimler arasında ayrım yapmak için Kullanıcı öğesinde bir alan ekledik,
- Ayrıca, `userId` alan ile yedekli olan `id` ancak `users` kapsayıcı artık `userId` ( `id` daha önce değil) tarafından bölümlenerek gerekli olan Kullanıcı öğesine bir alan ekledik.

Bu eğilimi başarmak için, değişiklik akışını yeniden kullanırız. Bu kez, kapsayıcıya `posts` Yeni veya güncelleştirilmiş bir gönderi göndermek için kapsayıcının değişiklik akışına tepki veririz `users` . Ve liste gönderilerinin tam içeriğini döndürmesi gerekli olmadığı için, bu işlemleri işlemden kesebiliriz.

:::image type="content" source="./media/how-to-model-partition-example/denormalization-2.png" alt-text="Kullanıcılar kapsayıcısına gönderi gönderme" border="false":::

Şimdi sorgumuzu `users` kapsayıcıya yönlendirebilir ve kapsayıcının bölüm anahtarında filtre uygulayabilirsiniz.

:::image type="content" source="./media/how-to-model-partition-example/V3-Q3.png" alt-text="Bir kullanıcının tüm gönderilerini alma" border="false":::

| **Gecikme süresi** | **RU ücreti** | **Performans** |
| --- | --- | --- |
| 4 MS | 6,46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Kısa bir biçimde (akış) oluşturulan en son x gönderilerini listeleyin

Burada benzer bir durumla uğraşmanız gerekir: Sparing ' de sunulan ek sorgular gereksiz hale gelse bile, kalan sorgu kapsayıcının bölüm anahtarında filtrelemez:

:::image type="content" source="./media/how-to-model-partition-example/V2-Q6.png" alt-text="Kısa biçimde oluşturulan en son x gönderilerini listelemek için sorguyu gösteren diyagram." border="false":::

Aynı yaklaşımdan sonra, bu isteğin performansını ve ölçeklenebilirliğini en üst düzeye çıkarmak, yalnızca bir bölüme ait olması gerekir. Yalnızca sınırlı sayıda öğe döndürtiğimiz için bu Conceivable. Blog platformumuzu giriş sayfamızı doldurmak için, tüm veri kümesi üzerinde ilerlemeden yalnızca en son 100 gönderi almanız gerekir.

Bu son isteği iyileştirmek için tasarımımız için üçüncü bir kapsayıcı sunuyoruz ve bu isteğin sunulması için tamamen ayrıldık. Gönderilerimizi bu yeni kapsayıcıya uzlaştırdık `feed` :

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Bu kapsayıcı `type` , her zaman `post` öğelerimizde olacak şekilde bölümlenmiş. Bunun yapılması, bu kapsayıcıdaki tüm öğelerin aynı bölümde yer almasını sağlar.

Daha fazla işlem yapmak için, daha önce bu yeni kapsayıcıya gönderdiğimiz değişiklik akışı ardışık düzenine ulaşmanız gerekir. Göz önünde bulundurmanız gereken önemli bir şey; yalnızca en son 100 gönderi depolıyoruz. Aksi takdirde, kapsayıcının içeriği bir bölümün en büyük boyutunun ötesine çıkabilir. Bu işlem, kapsayıcıya bir belge eklendiğinde bir [son tetikleyici](stored-procedures-triggers-udfs.md#triggers) çağırarak yapılır:

:::image type="content" source="./media/how-to-model-partition-example/denormalization-3.png" alt-text="Akış kapsayıcısına gönderi gönderme" border="false":::

Koleksiyonu kesen tetiklemenin gövdesi aşağıda verilmiştir:

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

Son adım sorgumuzu yeni kapsayıcımızda yeniden yönlendirimdir `feed` :

:::image type="content" source="./media/how-to-model-partition-example/V3-Q6.png" alt-text="En son gönderilerin alınması" border="false":::

| **Gecikme süresi** | **RU ücreti** | **Performans** |
| --- | --- | --- |
| 9 MS | 16,97 RU | ✅ |

## <a name="conclusion"></a>Sonuç

Tasarımımızın farklı sürümlerine tanıtıldığımız genel performans ve ölçeklenebilirlik iyileştirmeleri göz atalım.

| | V1 | V2 | Yüklemesinde |
| --- | --- | --- | --- |
| **=** | 7 MS/5,71 RU | 7 MS/5,71 RU | 7 MS/5,71 RU |
| **Q1** | 2 ms/1 RU | 2 ms/1 RU | 2 ms/1 RU |
| **Geç** | 9 MS/8,76 RU | 9 MS/8,76 RU | 9 MS/8,76 RU |
| **Üç** | 9 MS/19,54 RU | 2 ms/1 RU | 2 ms/1 RU |
| **S3** | 130 MS/619,41 RU | 28 MS/201,54 RU | 4 MS/6,46 RU |
| **C3** | 7 MS/8,57 RU | 7 MS/15,27 RU | 7 MS/15,27 RU |
| **Ç** | 23 MS/27,72 RU | 4 MS/7,72 RU | 4 MS/7,72 RU |
| **C4** | 6 MS/7,05 RU | 7 MS/14,67 RU | 7 MS/14,67 RU |
| **[Q5]** | 59 MS/58,92 RU | 4 MS/8,92 RU | 4 MS/8,92 RU |
| **[Q6]** | 306 MS/2063,54 RU | 83 MS/532,33 RU | 9 MS/16,97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Bir okuma ağır senaryoyu en iyi duruma aldık

Yazma isteklerinin (komutların) masrafına yönelik okuma isteklerinin (sorguların) performansını artırma çabamız olduğunu fark etmiş olabilirsiniz. Çoğu durumda, yazma işlemleri artık değişiklik akışları aracılığıyla daha fazla hesaplama tetiklenir ve bu sayede daha fazla maliyetli ve daha uzun bir süre daha yüksektir.

Bu, bir blog platformunun (çoğu sosyal uygulamalar gibi) okuma açısından ağır olduğu ve bu da kendisine ait olduğu okuma isteklerinin, genellikle yazma isteklerinden daha yüksek bir büyüklükte olduğu anlamına gelir. Bu nedenle, okuma isteklerinin daha fazla ve daha iyi performans almasına izin vermek için yazma isteklerinin yürütülmesine daha pahalı hale getirilmesi mantıklıdır.

Yaptığımız en son iyileştirmelere baktığımızda, **[Q6]** 2000 + Rus ' den yalnızca 17 ru 'a gitti; her öğe için 10 ru 'ın üzerinde bir ücret karşılığında gönderimler elde ettiğimiz için. Gönderimler oluşturulduktan veya güncelleştirenden çok daha fazla akış isteği sunduğumuz için, bu yorun maliyeti genel tasarrufları göz önünde bulunduramaz.

### <a name="denormalization-can-be-applied-incrementally"></a>Artan bir şekilde uygulanabilir

Bu makalede araştırdığımız ölçeklenebilirlik iyileştirmeleri veri kümesi genelinde verilerin normalleştirilmesini ve çoğaltılmasını içerir. Bu iyileştirmelerin, 1. gün içinde yerleştirilmeleri gerekmez. Bölüm anahtarlarına filtre uygulayan sorgular ölçeği daha iyi bir şekilde gerçekleştirir, ancak geçici bölümleme sorguları nadiren veya sınırlı bir veri kümesine göre çağrılırsa tamamen kabul edilebilir. Yalnızca bir prototip oluşturuyorsanız veya küçük ve denetimli bir kullanıcı tabanına sahip bir ürün başladıysanız, bu geliştirmeleri daha sonra da gerçekleştirebilirsiniz. ne kadar önemli olursa, modelin performansını [izlemek](use-metrics.md) , bu sayede, bunları ne zaman ve ne zaman yapacağınıza karar verebilirsiniz.

Diğer kapsayıcılara güncelleştirmeleri dağıtmak için kullandığımız değişiklik akışı, tüm bu güncelleştirmeleri kalıcı olarak depolar. Bu durum, sisteminizde zaten çok fazla veri olsa bile kapsayıcı ve önyükleme sırasında oluşan görünümler tek seferlik bir yakalama işlemi olarak oluşturulduktan sonra tüm güncelleştirmelerin istenmesini mümkün kılar.

## <a name="next-steps"></a>Sonraki adımlar

Pratik veri modelleme ve bölümleme 'a giriş sonrasında, ele aldığımız kavramları gözden geçirmek için aşağıdaki makalelere göz atın:

- [Veritabanları, kapsayıcılar ve öğelerle çalışma](account-databases-containers-items.md)
- [Azure Cosmos DB'de bölümleme](partitioning-overview.md)
- [Azure Cosmos DB akışı değiştirme](change-feed.md)
