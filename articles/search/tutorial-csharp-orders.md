---
title: Sonuçları sıralamada C# öğreticisi
titleSuffix: Azure Cognitive Search
description: Bu C# öğreticisi, arama sonuçlarının nasıl sipariş alınacağını gösterir. Önceki bir oteller projesi üzerinde oluşturulur, birincil özelliğe göre sıralar, ikincil özellik ve yükseltme ölçütü eklemek için bir Puanlama profili içerir.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1f8100dd6340383eadec5d10b7f23db59ba0ebdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786393"
---
# <a name="tutorial-order-search-results-using-the-net-sdk"></a>Öğretici: .NET SDK kullanarak arama sonuçlarını sıralama

Bu öğretici serisinin tamamında, sonuçlar döndürülür ve [varsayılan sırada](index-add-scoring-profiles.md#what-is-default-scoring)gösterilir. Bu öğreticide, birincil ve ikincil sıralama ölçütlerini ekleyeceksiniz. Sayısal değerlere göre sıralamaya alternatif olarak, nihai örnek, sonuçların özel bir Puanlama profiline göre nasıl gösterileceğini gösterir. Ayrıca _karmaşık türlerin_ görüntüsüne daha ayrıntılı bir şekilde gidecağız.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Sonuçları bir özelliğe göre sıralama
> * Birden çok özelliğe göre sıra sonuçları
> * Puanlama profili temelinde sonuçları sıralama

## <a name="overview"></a>Genel Bakış

Bu öğreticide, [Arama sonuçlarına sayfalama Ekle](tutorial-csharp-paging.md) öğreticisinde oluşturulan sonsuz kayan proje genişletilir.

Bu öğreticideki kodun tamamlanmış bir sürümü aşağıdaki projede bulunabilir:

* [5-sıra-sonuçlar (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/5-order-results)

## <a name="prerequisites"></a>Önkoşullar

* [2B-Add-Infinite-Scroll (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll) çözümü. Bu proje, önceki öğreticiden ya da GitHub 'dan bir kopyadan oluşturulmuş kendi sürümünüz olabilir.

Bu öğretici, [Azure.Search.Documstalar (sürüm 11)](https://www.nuget.org/packages/Azure.Search.Documents/) paketini kullanacak şekilde güncelleştirilmiştir. .NET SDK 'sının önceki bir sürümü için bkz. [Microsoft. Azure. Search (sürüm 10) kod örneği](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="order-results-based-on-one-property"></a>Sonuçları bir özelliğe göre sıralama

Otel derecelendirmesi gibi bir özelliğe göre sonuçları düzenlerken, yalnızca sıralı sonuçları istemedik, ayrıca siparişin doğru olduğundan emin olmak istiyoruz. Sonuçlara derecelendirme alanını eklemek, sonuçların doğru şekilde sıralanacağını doğrulamamızı sağlar.

Bu alıştırmada, sonuçların görüntüsüne biraz daha fazlasını de ekleyeceğiz: her otel için en ucuz oda oranı ve en pahalı oda oranı.

Sıralamayı etkinleştirmek için modellerden herhangi birini değiştirmenize gerek yoktur. Yalnızca görünüm ve denetleyici için güncelleştirmeler gerekir. Ana denetleyiciyi açarak başlayın.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>OrderBy özelliğini Arama parametrelerine ekleyin

1. HomeController. cs ' de, **OrderBy** seçeneğini ekleyin ve derecelendirme özelliğini azalan sıralama düzeni ile birlikte ekleyin. **Dizin (SearchData model)** yönteminde, Arama parametrelerine aşağıdaki satırı ekleyin.

    ```cs
    options.OrderBy.Add("Rating desc");
    ```

    >[!Note]
    > Varsayılan sıra artan, ancak `asc` bunu açık hale getirmek için özelliğine ekleyebilirsiniz. Azalan sıra eklenerek belirtilir `desc` .

1. Şimdi uygulamayı çalıştırın ve tüm ortak arama terimini girin. Sonuçlar doğru sırada olmayabilir veya olmayabilir, bu, kullanıcının değil geliştirici olarak, sonuçları doğrulayan kolay bir yolu vardır!

1. Sonuçların derecelendirmeye göre sıralandığından emin olalım. İlk olarak, oteller. CSS dosyasındaki **Box1** ve **box2** sınıflarını aşağıdaki sınıflarla değiştirin (Bu sınıflar, bu öğretici için ihtiyaç duyduğumuz tüm yeni olanlardır).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    > [!Tip]
    > Tarayıcılar genellikle CSS dosyalarını önbelleğe alabilir ve bu, eski bir CSS dosyasının kullanılmasına yol açabilir ve düzenlemeleriniz yok sayılır. Bunun iyi bir yolu, bağlantıya sürüm parametresi olan bir sorgu dizesi eklemektir. Örnek:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    > Tarayıcınız tarafından eski bir CSS dosyası kullanıldığını düşünüyorsanız sürüm numarasını güncelleştirin.

1. Sonuç olarak, aşağıdaki üç alanı içerecek şekilde **Dizin (SearchData model)** yönteminde **Select** parametresine **Derecelendirme** özelliğini ekleyin:

    ```cs
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Rating");
    ```

1. Görünümü (index. cshtml) açın ve oluşturma döngüsünü (**&lt; !--otel verilerini göster.-- &gt;**) aşağıdaki kodla değiştirin.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var ratingText = $"Rating: {result[i].Document.Rating}";

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Derecelendirmenin hem ilk görüntülenen sayfada hem de sonsuz kaydırma yoluyla çağrılan sonraki sayfalarda kullanılabilir olması gerekir. Bu iki durum için, denetleyicideki bir **sonraki** eylemi ve görünümdeki **kaydırılan** işlevi güncelleştirmemiz gerekir. Denetleyiciden başlayarak **sonraki** yöntemi aşağıdaki kodla değiştirin. Bu kod, derecelendirme metnini oluşturur ve iletişim kurar.

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
    
            string fullDescription = result.Document.Description;
    
            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Şimdi, görünümdeki **kaydırılan** işlevi, derecelendirme metnini görüntülemek için güncelleştirin.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 3) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Şimdi uygulamayı yeniden çalıştırın. "WiFi" gibi tüm ortak bir dönemde arama yapın ve sonuçların, otel derecelendirmesine göre azalan sıraya göre sıralanmıştır olduğunu doğrulayın.

    ![Derecelendirmeye dayalı sıralama](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Birkaç otelde aynı dereceye sahip olduğunu fark edeceksiniz ve bu nedenle, ekranda görüntülenen görünüşünün verilerin bulunduğu sıra, rastgele olan bir sıra olduğunu fark edersiniz.

    İkinci bir sıralama düzeyi eklemeye bakmadan önce, Oda tarifelerinin aralığını görüntülemesi için bazı kodlar ekleyelim. Bu kodu hem _karmaşık bir türden_ veri ayıklayarak hem de ayrıca, fiyata göre sıralama sonuçlarını (Belki de ilk olarak) temel alarak tartışacağız.

### <a name="add-the-range-of-room-rates-to-the-view"></a>Görünüme Oda oranları aralığını ekleyin

1. Otel. cs modeline en ucuz ve en pahalı oda oranını içeren özellikleri ekleyin.

    ```cs
    // Room rate range
    public double cheapest { get; set; }
    public double expensive { get; set; }
    ```

1. Giriş denetleyicisindeki **Dizin (SearchData model)** eyleminin sonundaki oda oranlarını hesaplayın. Geçici verilerin depolandıktan sonra hesaplamalar ekleyin.

    ```cs
    // Ensure TempData is stored for the next call.
    TempData["page"] = page;
    TempData["searchfor"] = model.searchText;

    // Calculate the room rate ranges.
    await foreach (var result in model.resultList.GetResultsAsync())
    {
        var cheapest = 0d;
        var expensive = 0d;

        foreach (var room in result.Document.Rooms)
        {
            var rate = room.BaseRate;
            if (rate < cheapest || cheapest == 0)
            {
                cheapest = (double)rate;
            }
            if (rate > expensive)
            {
                expensive = (double)rate;
            }
        }
        model.resultList.Results[n].Document.cheapest = cheapest;
        model.resultList.Results[n].Document.expensive = expensive;
    }
    ```

1. **Oda** özelliğini denetleyicinin **Dizin (searchdata model)** eylem yönteminde **Select** parametresine ekleyin.

    ```cs
    options.Select.Add("Rooms");
    ```

1. Görünümdeki işleme döngüsünü değiştirerek sonuçların ilk sayfasının hız aralığını görüntüleyin.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Sonraki sonuç sayfaları için, giriş denetleyicisindeki **sonraki** yöntemi, hız aralığını iletmek üzere değiştirin.

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";

            string fullDescription = result.Document.Description;

            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(rateText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Görünümdeki **kaydırılan** işlevi, oda fiyatları metnini işleyecek şekilde güncelleştirin.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 4) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Uygulamayı çalıştırın ve oda oranı aralıklarının görüntülendiğini doğrulayın.

    ![Oda oranı aralıklarını görüntüleme](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

Arama parametrelerinin **OrderBy** özelliği, odalar gibi bir girişi kabul etmez **.** bu, odalar zaten oranlara sıralanmış olsa bile, en fazla oda oranını sağlamak için kullanılır. Bu durumda, odalar ücret olarak sıralanmaz. Örnek veri kümesindeki oteller, Oda hızına göre sıralanmış olarak görüntülemek için, sonuçları ana denetleyicinizde sıralayıp bu sonuçları istenen sırada görünüme göndermeniz gerekir.

## <a name="order-results-based-on-multiple-values"></a>Birden çok değere göre sıra sonuçları

Bu soru artık aynı derecelendirmeye sahip oteller arasında ayrım yapmak için kullanılır. Tek bir yaklaşım, otelin en son ne zaman daha büyük bir süre içinde daha yüksek görünmesi için, otelin her ne kadar sürmüş olduğunu temel alan ikincil bir sıralama olabilir.

1. İkinci bir sıralama düzeyi eklemek için, arama sonuçlarına ve **OrderBy** öğesine **Dizin (searchdata model)** yönteminde **Lastrenovationdate** ekleyin.

    ```cs
    options.Select.Add("LastRenovationDate");

    options.OrderBy.Add("LastRenovationDate desc");
    ```

    >[!Tip]
    > **OrderBy** listesinde herhangi bir sayıda özellik girilebilir. Oteller aynı derecelendirme ve buluşma tarihi içeriyorsa, aralarında ayrım yapmak için üçüncü bir özellik girilebilir.

1. Yine de, sıralamanın doğru olduğundan emin olmak için görünümdeki yeniden düzenleme tarihini görmeniz gerekir. Bu tür bir şey için, büyük olasılıkla yalnızca yıl yeterlidir. Görünümdeki işleme döngüsünü aşağıdaki kodla değiştirin.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var lastRenovatedText = $"Last renovated: { result[i].Document.LastRenovationDate.Value.Year}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Son yeniden geçen tarihin yıl bileşenini iletmek için giriş denetleyicisindeki **sonraki** yöntemi değiştirin.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var ratingText = $"Rating: {result.Document.Rating}";
                var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
                var lastRenovatedText = $"Last renovated: {result.Document.LastRenovationDate.Value.Year}";

                string fullDescription = result.Document.Description;

                // Add strings to the list.
                nextHotels.Add(result.Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(fullDescription);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. Görünüm içindeki **kaydırılan** işlevi değiştirerek, yeniden işle metni görüntüleyin.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 5) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Uygulamayı çalıştırın. "Havuz" veya "Görünüm" gibi ortak bir dönemde arama yapın ve aynı derecelendirmeye sahip olan oteller artık daha fazla süre için azalan sırada görüntülendiğini doğrulayın.

    ![Yeniden sipariş tarihini sıralama](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="order-results-based-on-a-scoring-profile"></a>Puanlama profili temelinde sonuçları sıralama

Öğreticide verilen örnekler, _kesin_ bir sıralama dizisi sağlayan sayısal değerleri (derecelendirme ve yeniden kullanma tarihi) nasıl sıralayarak göstermektedir. Ancak bazı aramalar ve bazı veriler, bu iki veri öğesi arasında kolay bir karşılaştırmaya kendisini hiçbir şekilde vermez. Tam metin arama sorguları için Bilişsel Arama, _Derecelendirme_ kavramını içerir. Daha karmaşık ve nitel karşılaştırmaları sağlayan sonuçların nasıl derecelendirilir olduğunu etkilemek için _Puanlama profilleri_ belirlenebilir.

[Puanlama profilleri](index-add-scoring-profiles.md) dizin şemasında tanımlanmıştır. Otel verilerinde çeşitli Puanlama profilleri ayarlanmış. Puanlama profilinin nasıl tanımlandığını inceleyelim, sonra da bunları aramak için kod yazmayı deneyin.

### <a name="how-scoring-profiles-are-defined"></a>Puanlama profillerinin tanımlanması

Puanlama profilleri, tasarım zamanında bir arama dizininde tanımlanır. Microsoft tarafından barındırılan salt-okunurdur oteller dizininde üç Puanlama profili vardır. Bu bölümde, Puanlama profilleri incelenmektedir ve içinde kodunun nasıl kullanılacağı gösterilir.

1. Aşağıda, bir **OrderBy** veya **ScoringProfile** parametresi belirtmediğinden kullanılan oteller veri kümesi için varsayılan Puanlama profili verilmiştir. Bu profil, otel adında, açıklamada veya etiket listesinde (değişiklik) arama metni varsa, bir otel için _puanı_ artırır. Puanlama ağırlıklarının belirli alanları nasıl tercih ettiğini unutmayın. Arama metni başka bir alanda görüntüleniyorsa, aşağıda listelenmediyse 1 ağırlığı olur. Puanın ne kadar yüksek olması durumunda görünümde bir sonuç görüntülenir.

     ```cs
    {
        "name": "boostByField",
        "text": {
            "weights": {
                "Tags": 3,
                "HotelName": 2,
                "Description": 1.5,
                "Description_fr": 1.5,
            }
        }
    }
    ```

1. Aşağıdaki alternatif Puanlama profili, sağlanan bir parametre bir veya daha fazla etiket listesi içeriyorsa ("değişiklik" yaptığımız) puanı önemli ölçüde artırır. Bu profilin anahtar noktası, metin içeren bir parametre sağlanması _gerekir_ . Parametre boşsa veya sağlanmazsa bir hata oluşur.

    ```cs
    {
        "name":"boostAmenities",
        "functions":[
            {
            "fieldName":"Tags",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":null,
            "distance":null,
            "tag":{
                "tagsParameter":"amenities"
            },
            "type":"tag",
            "boost":5
            }
        ],
        "functionAggregation":0
    },
    ```

1. Bu üçüncü profilde, otel derecelendirmesi puanı önemli ölçüde artırır. Son yeniden kullanım tarihi de puanı artırır, ancak bu veriler geçerli tarihin 730 gün (2 yıl) içinde yer alıyorsa puan de artırır.

    ```cs
    {
        "name":"renovatedAndHighlyRated",
        "functions":[
            {
            "fieldName":"Rating",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":{
                "boostingRangeStart":0,
                "boostingRangeEnd":5,
                "constantBoostBeyondRange":false
            },
            "distance":null,
            "tag":null,
            "type":"magnitude",
            "boost":20
            },
            {
            "fieldName":"LastRenovationDate",
            "freshness":{
                "boostingDuration":"P730D"
            },
            "interpolation":"quadratic",
            "magnitude":null,
            "distance":null,
            "tag":null,
            "type":"freshness",
            "boost":10
            }
        ],
        "functionAggregation":0
    }
    ```

    Şimdi bu profillerin çalışır durumda olduğunu düşündüğünüzden bize bakalım.

### <a name="add-code-to-the-view-to-compare-profiles"></a>Profilleri karşılaştırmak için görünüme kod ekleyin

1. İndex. cshtml dosyasını açın ve &lt; gövde &gt; bölümünü aşağıdaki kodla değiştirin.

    ```html
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

1. SearchData. cs dosyasını açın ve **searchdata** sınıfını aşağıdaki kodla değiştirin.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

1. Oteller. css dosyasını açın ve aşağıdaki HTML sınıflarını ekleyin.

    ```html
    .facetlist {
        list-style: none;
    }
    
    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Puanlama profili belirtmek için denetleyiciye kod ekleme

1. Giriş denetleyicisi dosyasını açın. Aşağıdaki **using** ifadesini ekleyin (liste oluşturmaya yardımcı olmak için).

    ```cs
    using System.Linq;
    ```

1. Bu örnekte, ilk görünümü döndürenden biraz daha fazlasını yapmak için **Dizin** için ilk çağrıya ihtiyacımız var. Yöntemi artık görünümde görüntülenecek en fazla 20 değişiklik arar.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchOptions options = new SearchOptions();
            // Search for up to 20 amenities.
            options.Facets.Add("Tags,count:20");

            SearchResults<Hotel> searchResult = await _searchClient.SearchAsync<Hotel>("*", options);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

1. Modelleri geçici depolamaya kaydetmek ve geçici depolama alanından kurtarmak ve bir modeli doldurmak için iki özel yöntem gerekir.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

1. **OrderBy** ve **ScoringProfile** parametrelerini gereken şekilde ayarlamanız gerekir. Var olan **Dizin (SearchData model)** yöntemini aşağıdaki kodla değiştirin.

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Recover the facet text, and the facet check box settings.
                RecoverFacets(model, true);

                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First search with text. 
                // Recover the facet text, but ignore the check box settings, and use the current model settings.
                RecoverFacets(model, false);

                // First call. Check for valid text input, and valid scoring profile.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                if (model.scoring == null)
                {
                    model.scoring = "Default";
                }
                page = 0;
            }

            // Setup the search parameters.
            var options = new SearchOptions
            {
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Size = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalCount = true,
            };
            // Select the data properties to be returned.
            options.Select.Add("HotelName");
            options.Select.Add("Description");
            options.Select.Add("Tags");
            options.Select.Add("Rooms");
            options.Select.Add("Rating");
            options.Select.Add("LastRenovationDate");

            List<string> parameters = new List<string>();
            // Set the ordering based on the user's radio button selection.
            switch (model.scoring)
            {
                case "RatingRenovation":
                    // Set the ordering/scoring parameters.
                    options.OrderBy.Add("Rating desc");
                    options.OrderBy.Add("LastRenovationDate desc");
                    break;

                case "boostAmenities":
                    {
                        options.ScoringProfile = model.scoring;

                        // Create a string list of amenities that have been clicked.
                        for (int a = 0; a < model.facetOn.Length; a++)
                        {
                            if (model.facetOn[a])
                            {
                                parameters.Add(model.facetText[a]);
                            }
                        }

                        if (parameters.Count > 0)
                        {
                            options.ScoringParameters.Add($"amenities-{ string.Join(',', parameters)}");
                        }
                        else
                        {
                            // No amenities selected, so set profile back to default.
                            options.ScoringProfile = "";
                        }
                    }
                    break;

                case "renovatedAndHighlyRated":
                    options.ScoringProfile = model.scoring;
                    break;

                default:
                    break;
            }

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options);

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
            TempData["scoring"] = model.scoring;
            SaveFacets(model, true);

            // Calculate the room rate ranges.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var cheapest = 0d;
                var expensive = 0d;

                foreach (var room in result.Document.Rooms)
                {
                    var rate = room.BaseRate;
                    if (rate < cheapest || cheapest == 0)
                    {
                        cheapest = (double)rate;
                    }
                    if (rate > expensive)
                    {
                        expensive = (double)rate;
                    }
                }

                result.Document.cheapest = cheapest;
                result.Document.expensive = expensive;
            }
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    Her **anahtar** seçimi için açıklamaları okuyun.

1. Önceki bölümde yer alan ve birden çok özelliği temel alan sıralama için ek kodu tamamladığınız takdirde, **sonraki** eylemde herhangi bir değişiklik yapmak zorunda değilsiniz.

### <a name="run-and-test-the-app"></a>Uygulamayı çalıştırma ve test etme

1. Uygulamayı çalıştırın. Görünümde tam bir değişiklik kümesi görmeniz gerekir.

1. Sıralama için, "sayısal derecelendirmeye göre" seçeneğinin belirlenmesi, bu öğreticide daha önce uygulamış olduğunuz sayısal sıralamayı, eşit derecelendirmenin otelleriyle ilgili bir tarih karar verecek şekilde verir.

   ![Derecelendirmeye göre "plaj" sıralaması](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

1. Şimdi "değişiklik ölçütü" profilini deneyin. Çeşitli değişiklik seçimlerini yapın ve bu değişikliklerle ilgili oteller, sonuçlar listesinin yükseltildiğini doğrulayın.

   ![Profile göre "plaj" sıralaması](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

1. Beklediğiniz şeyi öğrenmek için "yeniden düzenlenmiş Tarih/derecelendirme profili" ni deneyin. Yalnızca son yeniden düzenlenen oteller bir _yenilik_ artışı almalıdır.

### <a name="resources"></a>Kaynaklar

Daha fazla bilgi için bkz. [Azure bilişsel arama dizinine Puanlama profilleri ekleme](./index-add-scoring-profiles.md).

## <a name="takeaways"></a>Paketler

Bu projeden aşağıdaki bu devralmayı göz önünde bulundurun:

* Kullanıcılar arama sonuçlarının en çok ilgisi olacak şekilde sıralanmasını bekler.
* Verilerin, sıralamanın kolay olması için yapılandırılması gerekir. "Cheapest" üzerinde, verilerin ek kod olmadan yapılmasını sağlamak üzere yapılandırılmamış olması için önce kolayca sıralayamazsınız.
* Daha yüksek bir sıralama düzeyinde aynı değere sahip olan sonuçlara ayırt etmek için sıralama için çok sayıda düzey olabilir.
* Bazı sonuçların artan düzende (örneğin, bir noktadan uzağa) ve bazıları azalan sırada (örneğin, konuğun derecelendirmesi) sırada sıralanabilmesi doğal bir şekilde yapılır.
* Bir veri kümesi için sayısal karşılaştırmalar yoksa veya yeterince akıllı olmadığında Puanlama profilleri tanımlanabilir. Puanlama her sonuç, sonuçların bir şekilde sipariş edilmesine ve görüntülenmesini sağlamaya yardımcı olur.

## <a name="next-steps"></a>Sonraki adımlar

Bu C# öğreticilerini tamamladınız-Azure Bilişsel Arama API 'Lerinde değerli bilgiye sahip olmanız gerekir.

Daha fazla başvuru ve öğretici için, [Azure bilişsel arama belgelerindeki](./index.yml) [Microsoft Learn](/learn/browse/?products=azure)veya Diğer öğreticilere göz atmayı düşünün.