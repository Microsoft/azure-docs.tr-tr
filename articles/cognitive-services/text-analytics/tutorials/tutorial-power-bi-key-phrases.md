---
title: 'Öğretici: Metin Analizi bilişsel hizmet ile Power BI tümleştirin'
titleSuffix: Azure Cognitive Services
description: Power BI'da depolanan metindeki anahtar ifadeleri ayıklamak için Metin Analizi'ni nasıl kullanacağınızı öğrenin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 97245a10602f763c3269218d87c6b1a5ba309817
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71241020"
---
# <a name="tutorial-integrate-power-bi-with-the-text-analytics-cognitive-service"></a>Öğretici: Metin Analizi bilişsel hizmet ile Power BI tümleştirin

Microsoft Power BI Desktop, verilerinize bağlanmanıza, verilerinizi dönüştürmenize ve görselleştirmenize olanak sağlayan ücretsiz bir uygulamadır. Microsoft Azure Bilişsel Hizmetler’in parçası olan Metin Analizi hizmeti, doğal dil işleme özelliği sağlar. Ham yapılandırılmamış veriler olduğunda, en önemli ifadeleri ayıklayabilir, yaklaşımı analiz edebilir ve markalar gibi iyi bilindik varlıkları belirleyebilir. Birlikte bu araçlar, müşterilerinizin ne hakkında konuştuğunu ve bu konuda nasıl hissettiğini hızlıca görmenize yardımcı olabilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Verileri içeri aktarmak ve dönüştürmek için Power BI Desktop’ı kullanma
> * Power BI Desktop’ta özel işlev oluşturma
> * Metin Analizi Anahtar İfadeler API’si ile Power BI Desktop’ı tümleştirme
> * Müşteri geri bildiriminden en önemli ifadeleri ayıklamak için Metin Analizi Anahtar İfadeler API’sini kullanma
> * Müşteri geri bildiriminden sözcük bulutu oluşturma

## <a name="prerequisites"></a>Önkoşullar
<a name="Prerequisites"></a>

- Microsoft Power BI Desktop. [Ücretsiz olarak indirin](https://powerbi.microsoft.com/get-started/).
- Bir Microsoft Azure hesabı. [Ücretsiz bir deneme başlatın](https://azure.microsoft.com/free/) veya [oturum açın](https://portal.azure.com/).
- Metin Analizi API’si ile Bilişsel Hizmetler API’si hesabı. Yoksa, [kaydolup](../../cognitive-services-apis-create-account.md) 5000 işlem/ay ücretsiz katmanını kullanabilirsiniz (bu öğreticiyi tamamlamak için [fiyatlandırma ayrıntılarına](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) bakın).
- Kayıt sırasında sizin için oluşturulan [Metin Analizi erişim anahtarı](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource).
- Müşteri yorumları. [Örnek verilerimizi](https://aka.ms/cogsvc/ta) veya kendi verilerinizi kullanabilirsiniz. Bu öğreticide, örnek verilerimizi kullandığınız varsayılır.

## <a name="load-customer-data"></a>Müşteri verilerini yükleme
<a name="LoadingData"></a>

Başlamak için Power BI Desktop’ı açın ve [Önkoşullar](#Prerequisites)’da indirdiğiniz virgülle ayrılmış değer (CSV) dosyasını `FabrikamComments.csv` yükleyin. Bu dosyada, kurgusal olarak oluşturulmuş küçük bir şirketin destek forumundaki bir günlük varsayımsal etkinlik yer almaktadır.

> [!NOTE]
> Power BI ile Facebook veya bir SQL veritabanı gibi çok çeşitli kaynaklarda yer alan veriler kullanılabilir. [Power BI ile Facebook tümleştirmesi](https://powerbi.microsoft.com/integrations/facebook/) ve [Power BI ile SQL Server tümleştirmesi](https://powerbi.microsoft.com/integrations/sql-server/) başlıklı makalelerde daha fazla bilgiye ulaşın.

Ana Power BI Desktop penceresinde **Ana Sayfa** şeridini seçin. Şeridin **Dış veri** grubunda **Veri Al** açılır menüsünü açın ve **Metin/CSV** seçeneğini belirleyin.

![[Veri Al düğmesi]](../media/tutorials/power-bi/get-data-button.png)

Aç iletişim kutusu görünür. İndirmeler klasörünüze veya `FabrikamComments.csv` dosyasını indirdiğiniz klasöre gidin. `FabrikamComments.csv` dosyasına ve ardından, **Aç** düğmesine tıklayın. CSV içeri aktarımı iletişim kutusu görünür.

![[CSV İçeri Aktarımı iletişim kutusu]](../media/tutorials/power-bi/csv-import.png)

CSV içeri aktarımı iletişim kutusu, Power BI Desktop'ın karakter kümesini, sınırlayıcıyı, üst bilgi satırlarını ve sütun türlerini doğru bir şekilde algıladığını doğrulamanıza olanak sağlar. Bu bilgilerin tümü doğru olduğuna göre **Yükle**’ye tıklayın.

Yüklenen verileri görmek için, Power BI çalışma alanının sol kenarındaki **Veri Görünümü** düğmesine tıklayın. Microsoft Excel’de olduğu gibi verileri içeren bir tablo açılır.

![[İçeri aktarılan verilerin başlangıç görünümü]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="prepare-the-data"></a>Verileri hazırlama
<a name="PreparingData"></a>

Metin Analizi hizmetinin Anahtar İfadeler API’si tarafından işlenmeye hazır hale gelebilmesi için verilerinizi Power BI Desktop’ta dönüştürmeniz gerekebilir.

Örnek veriler bir `subject` sütunu ve `comment` sütunu içerir. Power BI Desktop’ta Sütunları Birleştirme işleviyle, yalnızca `comment` sütunundaki değil, her iki sütundaki verilerden anahtar ifadeleri ayıklayabilirsiniz.

Power BI Desktop’ta **Ana Sayfa** şeridini seçin. **Dış veri** grubunda **Sorguları Düzenle**’ye tıklayın.

![[Giriş şeridindeki Dış Veri grubu]](../media/tutorials/power-bi/edit-queries.png)

Önceden seçilmediyse, pencerenin sol tarafındaki **Sorgular** listesinden `FabrikamComments` seçeneğini belirleyin.

Şimdi de tablodaki `subject` ve `comment` sütunlarının her ikisini de seçin. Bu sütunları görmek için yatay olarak kaydırma yapmanız gerekebilir. Öncelikle, `subject` sütun üst bilgisine tıklayın, ardından, Control tuşunu basılı tutarak `comment` sütun üst bilgisine tıklayın.

![[Birleştirilecek alanları seçme]](../media/tutorials/power-bi/select-columns.png)

**Dönüştür** şeridini seçin. Şeridin **Metin Sütunları** grubunda, **Sütunları Birleştir** seçeneğine tıklayın. Sütunları Birleştir iletişim kutusu görünür.

![[Sütunları Birleştir iletişim kutusunu kullanarak alanları birleştirme]](../media/tutorials/power-bi/merge-columns.png)

Sütunları Birleştir iletişim kutusunda, ayırıcı olarak `Tab` seçeneğini belirleyip **Tamam**’a tıklayın.

Boş Olanı Kaldır filtresinden yararlanarak veya yazdırılamayan karakterleri Temizle dönüşümü ile kaldırarak boş iletileri de filtreleyebilirsiniz. Verileriniz, örnek dosyadaki `spamscore` sütunu gibi bir sütun içeriyorsa Sayı Filtresi kullanarak "istenmeyen" yorumları atlayabilirsiniz.

## <a name="understand-the-api"></a>API’yi anlama
<a name="UnderstandingAPI"></a>

Metin Analizi hizmetinin [Anahtar İfadeler API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)’si, HTTP isteği başına bine kadar metin belgesini işleyebilir. Power BI, kayıtları tek tek işler. Bu nedenle, API’ye yönelik çağrılarınızın her biri yalnızca tek bir belge içerir. Anahtar İfadeler API’si, işlenen her belge için aşağıdaki alanları gerektirir.

| | |
| - | - |
| `id`  | İstekte bu belge için yer alan benzersiz tanımlayıcı. Yanıtta da bu alan bulunur. Böylece, birden fazla belge işlemeniz halinde, ayıklanan anahtar ifadeleri bu ifadelerin kaynağı olan belge ile kolayca eşleyebilirsiniz. Bu öğreticide, istek başına yalnızca bir belge işlediğinizden, `id` değerini, her bir istek için aynı olacak şekilde doğrudan yazabilirsiniz.|
| `text`  | İşlenecek metin. Bu alanın değeri, [önceki bölümde](#PreparingData) oluşturduğunuz ve konu satırını ve yorum metnini birlikte içeren `Merged` sütunundan gelir. Anahtar tümceleri API 'SI bu verilerin yaklaşık 5.120 karakterden uzun olmaması gerekir.|
| `language` | Belgenin yazıldığı doğal dilin kodu. Örnek verilerdeki tüm iletiler İngilizce’dir, bu nedenle bu alan için `en` değerini doğrudan yazabilirsiniz.|

## <a name="create-a-custom-function"></a>Özel işlev oluşturma
<a name="CreateCustomFunction"></a>

Artık Power BI ve Metin Analizi’ni tümleştirecek özel işlevi oluşturmaya hazırsınız. İşlev, işlenecek metni bir parametre olarak alır. Verileri gerekli JSON biçimine/biçiminden dönüştürür ve Anahtar İfadeler API’sine HTTP isteğinde bulunur. Daha sonra işlev, API’deki yanıtı ayrıştırır ve ayıklanan anahtar ifadelerin virgülle ayrılmış bir listesini içerir.

> [!NOTE]
> Power BI Desktop özel işlevleri [Power Query M formül dilinde](https://docs.microsoft.com/powerquery-m/power-query-m-reference) (veya kısaca "M") yazılır. M, [F#](https://docs.microsoft.com/dotnet/fsharp/) temelindeki işlevsel bir programlama dilidir. Bu öğreticiyi tamamlamanız için programcı olmanız gerekmese de gerekli kod aşağıda verilmiştir.

Power BI Desktop’ta, halen Sorgu Düzenleyicisi penceresinde bulunduğunuzdan emin olun. Aksi takdirde, **Ana Sayfa** şeridini seçin ve **Dış veri** grubunda **Sorguları Düzenle**’ye tıklayın.

Şimdi **Ana Sayfa** şeridindeki **Yeni Sorgu** grubunda **Yeni Kaynak** açılır menüsünü açın ve **Boş Sorgu**’yu seçin. 

Sorgular listesinde, başlangıçta `Query1` olarak adlandırılan yeni bir sorgu görüntülenir. Söz konusu girişe çift tıklayın ve girişi `KeyPhrases` olarak adlandırın.

Şimdi **Ana Sayfa** şeridindeki **Sorgu** grubunda **Gelişmiş Düzenleyici**’ye tıklayarak Gelişmiş Düzenleyici penceresini açın. Pencerede bulunan kodu silin ve aşağıdaki kodu yapıştırın. 

> [!NOTE]
> Aşağıdaki örnek uç noktayı, metin analizi kaynağınız `<your-custom-subdomain>`için oluşturulan uç noktayla değiştirin (içeren). [Azure Portal](https://azure.microsoft.com/features/azure-portal/)oturum açıp metin analizi aboneliğinizi seçip seçerek `Quick start`bu uç noktayı bulabilirsiniz.


```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics" & "/v2.1/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

`YOUR_API_KEY_HERE` değerini, Metin Analizi erişim anahtarınızla değiştirin. [Azure portalda](https://azure.microsoft.com/features/azure-portal/) oturum açıp Metin Analizi aboneliğinizi ve sonra Genel Bakış sayfasını seçerek de bu anahtarı bulabilirsiniz. Anahtarın önündeki ve arkasındaki tırnak işaretlerini kaldırmadığınızdan emin olun. Ardından, **Bitti** seçeneğine tıklayın.

## <a name="use-the-custom-function"></a>Özel işlevi kullanma
<a name="UseCustomFunction"></a>

Artık her bir müşteri yorumundan anahtar ifadeleri ayıklamak ve tablodaki yeni bir sütunda depolamak için özel işlevi kullanabiliriz. 

Power BI Desktop’ta Sorgu Düzenleyicisi penceresinde `FabrikamComments` sorgusuna geri dönün. **Sütun Ekle** şeridini seçin. **Genel** grubunda **Özel İşlev Çağır**’a tıklayın.

![[Özel İşlev Çağır düğmesi]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

Özel İşlev Çağır iletişim kutusu görüntülenir. **Yeni sütun adı** bölümüne `keyphrases` girin. **İşlev sorgusu** bölümünde, oluşturduğunuz özel işlevi (`KeyPhrases`) seçin.

İletişim kutusunda yeni bir alan görüntülenir: **metin (isteğe bağlı)** . Bu alan, Anahtar İfadeler API’sinin `text` parametresi için değer sağlamak amacıyla hangi sütunu kullanmak istediğimizi sorar. (`language` ve `id` parametreleri için değerleri önceden doğrudan yazdığınızı unutmayın.) Açılan menüden `Merged` ([daha önce](#PreparingData) konu ve ileti alanlarını birleştirerek oluşturduğumuz sütun) seçeneğini belirleyin.

![[Özel işlev çağırma]](../media/tutorials/power-bi/invoke-custom-function.png)

Son olarak, **Tamam**'a tıklayın.

Her şey hazırsa Power BI, tablodaki her bir satır için bir kez özel işlevinizi çağırır. Anahtar İfadeler API’sine sorgular gönderir ve sonuçları depolamak için tabloya yeni bir sütun ekler. Ancak bundan önce kimlik doğrulaması ve gizlilik ayarlarını belirtmeniz gerekebilir.

## <a name="authentication-and-privacy"></a>Kimlik doğrulaması gizlilik
<a name="Authentication"></a>

Özel İşlev Çağır iletişim kutusunu kapatmanızın ardından, Anahtar İfadeler API’sine nasıl bağlanılacağını belirtmenizin istendiği bir bant görüntülenebilir.

![[kimlik bilgileri bandı]](../media/tutorials/power-bi/credentials-banner.png)

**Kimlik Bilgilerini Düzenle**’ye tıklayın, iletişim kutusunda `Anonymous` seçeneğinin belirlendiğinden emin olun ve sonra **Bağlan**’a tıklayın. 

> [!NOTE]
> Metin Analizi hizmeti, erişim anahtarınızı kullanarak kimliğinizi doğruladığından `Anonymous` seçeneğini belirlersiniz, bu nedenle Power BI’ın HTTP isteği için kimlik bilgileri sağlaması gerekmez.

> [!div class="mx-imgBorder"]
> ![[kimlik doğrulaması anonim olarak ayarlanıyor]](../media/tutorials/power-bi/access-web-content.png)

Anonim erişimi seçtiğiniz halde Kimlik Bilgilerini Düzenle bandını görüyorsanız, `KeyPhrases` [özel işlevdeki](#CreateCustomFunction) koda Metin Analizi erişim anahtarınızı yapıştırmayı unutmuş olabilirsiniz.

Ardından, veri kaynaklarınızın gizliliği hakkında bilgiler sunmanızın istendiği bir bant görüntülenebilir. 

![[gizlilik bandı]](../media/tutorials/power-bi/privacy-banner.png)

**Devam Et**’e tıklayın ve iletişim kutusundaki her bir veri kaynağı için `Public` seçeneğini belirleyin. Ardından, **Kaydet**'e tıklayın.

![[veri kaynağı gizliliğini ayarlama]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="create-the-word-cloud"></a>Sözcük bulutunu oluşturma
<a name="WordCloud"></a>

Görüntülenen tüm bantlarla ilgili gerekli işlemleri yaptıktan sonra, Sorgu Düzenleyicisi'ni kapatmak için Giriş şeridindeki **Kapat ve Uygula** seçeneğine tıklayın.

Power BI Desktop kısa bir süre içinde gerekli HTTP isteklerini yapar. Tablodaki her bir satır için yeni `keyphrases` sütunu, Anahtar İfade Ayıklama API'si tarafından metinde algılanan anahtar ifadeleri içerir. 

Şimdi sözcük bulutu oluşturmak için bu sütunu kullanacaksınız. Başlamak için, ana Power BI Desktop penceresinde çalışma alanının sol tarafında yer alan **Rapor** düğmesine tıklayın.

> [!NOTE]
> Kelime bulutu oluşturmak için her bir yorumun tam metni yerine neden ayıklanan anahtar ifadeleri kullanmalı? Anahtar ifadeler bize yalnızca müşteri yorumlarında yer alan *en sık kullanılan* sözcükleri değil, *önemli* sözcükleri de sunar. Ayrıca sonuçta elde edilen buluttaki sözcük boyutlandırması, bir kelimenin nispeten az sayıda yorumda sık olarak kullanılmasına göre şekillenmez.

Henüz yapmadıysanız, Word Cloud özel görselini yükleyin. Çalışma alanının sağ tarafındaki Görsel Öğeler bölmesinde, üç nokta ( **...** ) simgesine tıklayın ve **Marketten içe aktarın** seçeneğini belirleyin. Ardından, "cloud" araması yapın ve Word Cloud görselinin yanındaki **Ekle** düğmesine tıklayın. Power BI, Sözcük Bulutu görselini yükler ve başarıyla yüklendiğini size bildirir.

![[özel görsel ekleme]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

İlk olarak, Görsel Öğeler bölmesindeki Word Cloud simgesine tıklayın.

![[Görsel Öğeler bölmesindeki Word Cloud simgesi]](../media/tutorials/power-bi/visualizations-panel.png)

Çalışma alanında yeni bir rapor görünür. Alanlar bölmesindeki `keyphrases` alanını Görsel Öğeler bölmesindeki Kategori alanına sürükleyin. Kelime bulutu raporda görünür.

Şimdi de Görsel Öğeler bölmesinin Biçim sayfasına geçin. Durdurma Sözcükleri kategorisinde, "of" gibi kısa ve sık kullanılan sözcükleri buluttan kaldırmak için **Varsayılan Durdurma Sözcükleri**'ni etkinleştirin. 

![[varsayılan durdurma sözcüklerini etkinleştirme]](../media/tutorials/power-bi/default-stop-words.png)

Bu bölmenin biraz daha alt kısmında, **Metni Döndür** ve **Başlık**'ı kapatın.

![[odak modunu etkinleştirme]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

Kelime bulutumuzu daha yakından incelemek için raporda Odak Modu aracına tıklayın. Araç, aşağıda gösterildiği gibi çalışma alanının tamamını dolduracak şekilde kelime bulutunu genişletir.

![[Bir Kelime Bulutu]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>Diğer Metin Analizi hizmetleri
<a name="MoreServices"></a>

Microsoft Azure tarafından sunulan Bilişsel Hizmetler'den biri olan Metin Analizi hizmeti, yaklaşım analizi ve dil algılama özelliklerini de sunar. Dil algılama özellikle de müşteri geri bildirimi tamamen İngilizce dilinde değilse faydalıdır.

Bu diğer API’lerin her ikisi de Anahtar İfadeler API’sine oldukça benzerdir. Başka bir deyişle, bu öğreticide oluşturduğunuza neredeyse benzer özel işlevleri kullanarak bunları Power BI Desktop ile tümleştirebilirsiniz. Boş bir sorgu oluşturup aşağıdaki uygun kodu daha önce yaptığınız gibi Gelişmiş Düzenleyici'ye yapıştırmanız yeterlidir. (Erişim anahtarınızı unutmayın!) Ardından, daha önce olduğu gibi, tabloya yeni sütun eklemek için işlevi kullanın.

Aşağıdaki Yaklaşım Analizi işlevi, metinde ifade edilen yaklaşımın ne ölçüde olumlu olduğunu gösteren bir puan döndürür.

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v2.1/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[score]
in  sentiment
```

Dil Algılama işlevine ilişkin iki sürüm aşağıda verilmiştir. İlk sürüm, ISO dil kodunu (örneğin, İngilizce için `en`), ikinci sürüm ise "kolay" adı (örneğin, `English`) döndürür. Bu iki sürüm arasında yalnızca gövdenin son satırının değişiklik gösterdiğine dikkat edin.

```fsharp
// Returns the two-letter language code (for example, 'en' for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v2.1/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (for example, 'English') of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v2.1/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

Son olarak, sunulmuş olan Anahtar İfade Ayıklama işlevinin, virgülle ayrılan ifadelerden oluşan tek bir dize yerine ifadeleri liste nesnesi olarak döndüren bir değişkeni aşağıda verilmiştir. 

> [!NOTE]
> Tek bir dize döndürülmesi kelime bulutu örneğimizi kolaylaştırmıştır. Diğer yandan listeler, Power BI'da döndürülen ifadelerle çalışmak için daha esnek bir biçimdir. Sorgu Düzenleyicisi'nin Dönüştür şeridindeki Yapılandırılmış Sütun grubunu kullanarak Power BI Desktop'taki liste nesnelerini denetleyebilirsiniz.

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v2.1/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>Sonraki adımlar
<a name="NextSteps"></a>

Metin Analizi hizmeti, Power Query M formül dili veya Power BI hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Metin Analizi API'si başvurusu](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)

> [!div class="nextstepaction"]
> [Power Query M başvurusu](https://docs.microsoft.com/powerquery-m/power-query-m-reference)

> [!div class="nextstepaction"]
> [Power BI belgeleri](https://powerbi.microsoft.com/documentation/powerbi-landing-page/)
