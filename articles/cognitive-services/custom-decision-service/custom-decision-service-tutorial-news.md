---
title: 'Öğretici: Makale kişiselleştirmesi-Özel Karar Alma Hizmeti'
titlesuffix: Azure Cognitive Services
description: Bağlamsal karar almaya yönelik makale kişiselleştirmesi için öğretici.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: f7eafed9db25fba904d98ddea652671dc45aa01d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707212"
---
# <a name="tutorial-article-personalization-for-contextual-decision-making"></a>Öğretici: Bağlamsal karar oluşturma için kişiselleştirme makalesi

Bu öğreticide, bir web sitesinin ön sayfasındaki makale seçkisini kişiselleştirmeye odaklanılmaktadır. Örneğin, Özel Karar Alma Hizmeti, ön sayfadaki *birden çok* makale listesini etkiler. Sayfa, yalnızca siyaset ve sporla ilgili bir haber web sitesi olabilir. Makalelerin üç sıralı listesini gösterir: siyasi, spor ve son dakika.

## <a name="applications-and-action-sets"></a>Uygulamalar ve eylem kümeleri

Aşağıda, senaryonuzu çerçeveye nasıl yerleştireceğiniz açıklanmaktadır. İyileştirilen her bir liste için birer tane olacak şekilde üç uygulama olduğunu varsayalım: uygulama-siyaset, uygulama-spor ve uygulama-son dakika. Her bir uygulamaya yönelik aday makaleleri belirtmek üzere biri siyaset için ve biri spor için olmak üzere iki eylem kümesi vardır. Uygulama-son dakika için eylem kümesi, diğer iki kümenin birleşimi olarak otomatik şekilde gelir.

> [!TIP]
> Eylem kümeleri, Özel Karar Alma Hizmeti’ndeki uygulamalar arasında paylaşılabilir.

## <a name="prepare-action-set-feeds"></a>Eylem kümesi akışlarını hazırlama

Özel Karar Alma Hizmeti, RSS veya müşterinin sağladığı Atom akışları aracılığıyla eylem kümelerini kullanır. Biri siyaset için ve biri spor için olmak üzere iki akış sağlarsınız. Bunların `http://www.domain.com/feeds/<feed-name>` kaynağından sunulduğunu varsayın.

Her akış bir makale listesi sağlar. RSS’de her biri aşağıdaki gibi bir `<item>` öğesiyle belirtilir:

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

Makalelerin sırası önemlidir. Varsayılan sıralamayı, başka bir deyişle, makalelerin nasıl sıralanması gerektiğine dair en iyi tahmininizi belirtir. Varsayılan derecelendirme daha sonra panoda performans karşılaştırması için kullanılır.

Akış biçimi hakkında daha fazla bilgi için bkz.[API başvurusu](custom-decision-service-api-reference.md#action-set-api-customer-provided).

## <a name="register-a-new-app"></a>Yeni uygulama kaydetme

1. [Microsoft hesabınızla](https://portal.ds.microsoft.com/) oturum açın. Şerit üzerinde **Portalım**’a tıklayın.

2. Yeni bir uygulama kaydolmak için **Yeni Uygulama** düğmesine tıklayın.

    ![Özel Karar Alma Hizmeti portalı](./media/custom-decision-service-tutorial/portal.png)

3. **Uygulama Kimliği** metin kutusuna uygulamanız için benzersiz bir ad girin. Bu ad zaten başka bir müşteri tarafından kullanılıyorsa sistem farklı bir uygulama kimliği seçmenizi ister. **Gelişmiş** onay kutusunu seçin ve Azure depolama hesabı için [bağlantı dizesini](../../storage/common/storage-configure-connection-string.md) girin. Normalde tüm uygulamalarınız için aynı depolama hesabını kullanırsınız.

    ![Yeni uygulama iletişim kutusu](./media/custom-decision-service-tutorial/new-app-dialog.png)

    Yukarıdaki senaryoda yer alan üç uygulamanın tamamını kaydetmenizin ardından uygulamalar listelenir:

    ![Uygulama listesi](./media/custom-decision-service-tutorial/apps.png)

    **Uygulamalar** düğmesine tıklayarak bu listeye geri dönebilirsiniz.

4. **Yeni Uygulama** iletişim kutusunda bir eylem akışı belirtin. Eylem akışları, **Akışlar** düğmesine tıklanıp sonra **Yeni Akış** düğmesine tıklanarak da belirtilebilir. Haber akışı için bir **Ad** girin, sağlandığı **URL**’yi girin ve **Yenileme Süresi**’ni girin. Yenileme süresi, Özel Karar Alma Hizmeti’nin ne sıklıkla akışı yenileyeceğini belirtir.

    ![Yeni akış iletişim kutusu](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    Eylem akışları, nerede belirtildiğine bakılmaksızın herhangi bir uygulama tarafından kullanılabilir. Bir senaryoda her iki eylem akışını belirtmenizin ardından eylem akışları listelenir:

    ![Akış listesi](./media/custom-decision-service-tutorial/feeds.png)

    **Akışlar** düğmesine tıklayarak bu listeye geri dönebilirsiniz.

## <a name="use-the-apis"></a>API’leri kullanma

Özel Karar Alma Hizmeti, Derecelendirme API’si aracılığıyla makaleleri derecelendirir. Bu API’yi kullanmak için aşağıdaki kodu ön sayfanın HTML başlığına ekleyin:

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

Derecelendirme API’sindeki HTTP yanıtı, JSONP tarafından biçimlendirilmiş bir dizedir. Örneğin, uygulama-siyaset için dize şöyle görünür:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

Daha sonra tarayıcı `callback()` işlevine bir çağrı olarak bu dizeyi yürütür. `callback()` işlevindeki `data` bağımsız değişkeni, işlenecek URL’lerin derecelendirmesini ve uygulama kimliğini içerir. Özellikle `callback()`, üç uygulamayı ayırt etmek için `data.appId` kullanmalıdır. `eventId`, varsa ilgili tıklama ile sağlanan derecelendirmeyle eşleştirmek için Özel Karar Alma Hizmeti tarafından dahili olarak kullanılır.

> [!TIP]
> `callback()`, `lastRefresh` alanını kullanarak her bir eylem akışının yeni olup olmadığını denetleyebilir. Belirli bir akış yeterince yeni değilse `callback()`, sağlanan derecelendirmeyi yoksayabilir, doğrudan bu akışı çağırabilir ve akış tarafından sağlanan varsayılan derecelendirmeyi kullanabilir.

Derecelendirme API’si tarafından sağlanan ek seçenekler ve belirtimler hakkında daha fazla bilgi için bkz. [API başvurusu](custom-decision-service-api-reference.md).

Kullanıcının en yüksek makale tercihleri, Ödül API’si çağrılarak döndürülür. En yüksek makale tercihi alındığında ön sayfada şu kod çağrılmalıdır:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

Tıklama işleme kodunda `appId` ve `eventId` kullanılırken dikkatli olunması gerekir. Örneğin, `callback()` işlevini aşağıdaki gibi uygulayabilirsiniz:

```javascript
function callback(data) {
    $.map(data.ranking, function (element) {
        //custom rendering function given content info
        render({appId:data.appId,
                article:element,
                onClick: element.id != data.rewardAction ? null :
                   function() {
                       $.ajax({
                       type: "POST",
                       url: '//ds.microsoft.com/api/v2/' + data.appId + '/reward/' + data.eventId,
                       contentType: "application/json" })}
                });
}}
```

Bu örnekte, belirli bir uygulamaya yönelik belirli bir makaleyi işlemek için `render()` işlevini uygulayın. Bu işlev, uygulama kimliğini ve makaleyi (Derecelendirme API’sindeki biçimde) girer. `onClick` parametresi, tıklamayı işlemek için `render()` öğesinden çağrılması gereken işlevdir. Tıklamanın üst dilimde olup olmadığını denetler. Daha sonra uygun uygulama kimliği ve olay kimliği ile Ödül API’sini çağırır.

## <a name="next-steps"></a>Sonraki adımlar

* Sağlanan işlevler hakkında daha fazla bilgi edinmek için [API başvurusu](custom-decision-service-api-reference.md)’na bakın.
