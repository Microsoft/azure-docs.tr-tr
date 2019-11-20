---
title: C#sonuçları sipariş etme öğreticisi
titleSuffix: Azure Cognitive Search
description: Bu öğreticide, arama sonuçlarının nasıl sipariş yapılacağı gösterilmektedir. Önceki bir oteller projesinde, birincil özelliğe göre sıralama, ikincil özellik ve yükseltme ölçütü eklemek için bir Puanlama profili içerir.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 9230e555f9ea56ca4cda3fcabe1f9719e36a8f62
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112676"
---
# <a name="c-tutorial-order-the-results---azure-cognitive-search"></a>C#Öğretici: sonuçları sıralama-Azure Bilişsel Arama

Bu noktaya kadar öğretici serimize kadar, sonuçlar döndürülür ve varsayılan sırada görüntülenir. Bu, verilerin bulunduğu sıra veya bir sıralama parametresi belirtilmediğinde kullanılacak olan bir varsayılan _Puanlama profili_ olabilir. Bu öğreticide, sonuçları birincil bir özelliğe göre sipariş etme ve ardından aynı birincil özelliği olan sonuçlar için, bu seçimi ikincil bir özellikte sıralama bölümüne gidecağız. Sayısal değerlere göre sıralamaya alternatif olarak, son örnek özel bir Puanlama profiline göre nasıl sıralanın gösterir. Ayrıca _karmaşık türlerin_görüntüsüne daha ayrıntılı bir şekilde gidecağız.

Döndürülen sonuçları kolayca karşılaştırmak için, bu proje [ C# öğreticide oluşturulan sonsuz kayan proje üzerinde oluşturur: arama sonuçları sayfalandırma-Azure bilişsel arama](tutorial-csharp-paging.md) öğreticisi.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Sonuçları bir özelliğe göre sıralama
> * Birden çok özelliğe göre sıra sonuçları
> * Sonuçları coğrafi noktadan bir uzaklığa göre filtrele
> * Puanlama profili temelinde sonuçları sıralama

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakileri yapmanız gerekir:

Öğreticinin sonsuz kayan sürümüne sahip olmak [: arama sonuçları sayfalandırma-Azure bilişsel arama Project çalışıyor ve çalışıyor. C# ](tutorial-csharp-paging.md) Bu proje kendi sürümünüz olabilir ya da GitHub: [ilk uygulama oluştur](https://github.com/Azure-Samples/azure-search-dotnet-samples)' dan yükleyebilirsiniz.

## <a name="order-results-based-on-one-property"></a>Sonuçları bir özelliğe göre sıralama

Sonuçları bir özelliğe göre sıralıyoruz, otel derecelendirmesine söyliyoruz, yalnızca sıralı sonuçları sağlamadığımızda, siparişin doğru olduğunu de onaylamanızı istiyoruz. Diğer bir deyişle, derecelendirmeye göre sıralıyoruz, derecelendirmeyi görünümde göstermemiz gerekir.

Bu öğreticide, her otel için sonuçların, en ucuz oda oranının ve en pahalı oda oranının görüntülenmesini sağlayacak bir çok daha da ekleyeceğiz. Sıralamayı gözden vertiğimiz gibi, Ayrıca, sıramız için de bir görünüm de gösterildiği gibi değerleri de ekleyeceğiz.

Sıralamayı etkinleştirmek için modellerden herhangi birini değiştirmenize gerek yoktur. Görünüm ve denetleyicinin güncellenmesi gerekiyor. Ana denetleyiciyi açarak başlayın.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>OrderBy özelliğini Arama parametrelerine ekleyin

1. Tek bir sayısal özelliğe göre sonuçları sıralamak için gereken tüm özellikler, **OrderBy** parametresini özelliğin adına ayarlamaya yönelik olur. **Dizin (SearchData model)** yönteminde, Arama parametrelerine aşağıdaki satırı ekleyin.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > Varsayılan sıra artan, ancak bunu açık hale getirmek için özelliğine **ASC** ekleyebilirsiniz. Azalan sıra, **DESC**eklenerek belirtilir.

2. Şimdi uygulamayı çalıştırın ve tüm ortak arama terimini girin. Sonuçlar doğru sırada olmayabilir veya olmayabilir, bu, kullanıcının değil geliştirici olarak, sonuçları doğrulayan kolay bir yolu vardır!

3. Sonuçların derecelendirmeye göre sıralandığından emin olalım. İlk olarak, oteller. CSS dosyasındaki **Box1** ve **box2** sınıflarını aşağıdaki sınıflarla değiştirin (Bu sınıflar, bu öğretici için ihtiyaç duyduğumuz tüm yeni olanlardır).

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

    >[!Tip]
    >Tarayıcılar genellikle CSS dosyalarını önbelleğe alabilir ve bu, eski bir CSS dosyasının kullanılmasına yol açabilir ve düzenlemeleriniz yok sayılır. Bunun iyi bir yolu, bağlantıya sürüm parametresi olan bir sorgu dizesi eklemektir. Örneğin:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Tarayıcınız tarafından eski bir CSS dosyası kullanıldığını düşünüyorsanız sürüm numarasını güncelleştirin.

4. **Index (SearchData model)** yönteminde **Select** parametresine **Derecelendirme** özelliğini ekleyin.

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Görünümü (index. cshtml) açın ve oluşturma döngüsünü değiştirin ( **&lt;!--otel verilerini göster.--&gt;** ) aşağıdaki kodla.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

6. Derecelendirmenin hem ilk görüntülenen sayfada hem de sonsuz kaydırma yoluyla çağrılan sonraki sayfalarda kullanılabilir olması gerekir. Bu iki durum için, denetleyicideki bir **sonraki** eylemi ve görünümdeki **kaydırılan** işlevi güncelleştirmemiz gerekir. Denetleyiciden başlayarak **sonraki** yöntemi aşağıdaki kodla değiştirin. Bu kod, derecelendirme metnini oluşturur ve iletişim kurar.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

7. Şimdi, görünümdeki **kaydırılan** işlevi, derecelendirme metnini görüntülemek için güncelleştirin.

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

8. Şimdi uygulamayı yeniden çalıştırın. "WiFi" gibi tüm ortak bir dönemde arama yapın ve sonuçların, otel derecelendirmesine göre azalan sıraya göre sıralanmıştır olduğunu doğrulayın.

    ![Derecelendirmeye dayalı sıralama](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Birkaç otelde aynı dereceye sahip olduğunu fark edeceksiniz ve bu nedenle, ekranda görüntülenen görünüşünün verilerin bulunduğu sıra, rastgele olan bir sıra olduğunu fark edersiniz.

    İkinci bir sıralama düzeyi eklemeye bakmadan önce, Oda tarifelerinin aralığını görüntülemesi için bazı kodlar ekleyelim. Bu kodu hem _karmaşık bir türden_veri ayıklayarak hem de ayrıca, fiyata göre sıralama sonuçlarını (Belki de ilk olarak) temel alarak tartışacağız.

### <a name="add-the-range-of-room-rates-to-the-view"></a>Görünüme Oda oranları aralığını ekleyin

1. Hotel.cs modeline en ucuz ve en pahalı oda oranını içeren özellikleri ekleyin.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Giriş denetleyicisindeki **Dizin (SearchData model)** eyleminin sonundaki oda oranlarını hesaplayın. Geçici verilerin depolandıktan sonra hesaplamalar ekleyin.

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
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

3. **Oda** özelliğini denetleyicinin **Dizin (searchdata model)** eylem yönteminde **Select** parametresine ekleyin.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Görünümdeki işleme döngüsünü değiştirerek sonuçların ilk sayfasının hız aralığını görüntüleyin.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

5. Sonraki sonuç sayfaları için, giriş denetleyicisindeki **sonraki** yöntemi, hız aralığını iletmek üzere değiştirin.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

6. Görünümdeki **kaydırılan** işlevi, oda fiyatları metnini işleyecek şekilde güncelleştirin.

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

7. Uygulamayı çalıştırın ve oda oranı aralıklarının görüntülendiğini doğrulayın.

    ![Oda oranı aralıklarını görüntüleme](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

Arama parametrelerinin **OrderBy** özelliği, odalar gibi bir girişi kabul etmez **.** bu, odalar zaten oranlara sıralanmış olsa bile, en fazla oda oranını sağlamak için kullanılır. Bu durumda, odalar ücret olarak sıralanmaz. Örnek veri kümesindeki oteller, Oda hızına göre sıralanmış olarak görüntülemek için, sonuçları ana denetleyicinizde sıralayıp bu sonuçları istenen sırada görünüme göndermeniz gerekir.

## <a name="order-results-based-on-multiple-values"></a>Birden çok değere göre sıra sonuçları

Bu soru artık aynı derecelendirmeye sahip oteller arasında ayrım yapmak için kullanılır. Çok iyi bir yöntem, otelin en son ne zaman yeniden oluşturulduğu ile ilgili olarak sırada olacaktır. Diğer bir deyişle, otel ne kadar kısa bir süre sonra, Otelde daha fazla zaman alır.

1. İkinci bir sıralama düzeyi eklemek için, **OrderBy** öğesini değiştirin ve **Lastrenovationdate** özelliğini dahil etmek Için **Dizin (searchdata model)** yönteminde özellikleri **seçin** .

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >**OrderBy** listesinde herhangi bir sayıda özellik girilebilir. Oteller aynı derecelendirme ve buluşma tarihi içeriyorsa, aralarında ayrım yapmak için üçüncü bir özellik girilebilir.

2. Yine de, sıralamanın doğru olduğundan emin olmak için görünümdeki yeniden düzenleme tarihini görmeniz gerekir. Bu tür bir şey gibi bir şey için, büyük olasılıkla yalnızca yıl gereklidir. Görünümdeki işleme döngüsünü aşağıdaki kodla değiştirin.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

3. Son yeniden geçen tarihin yıl bileşenini iletmek için giriş denetleyicisindeki **sonraki** yöntemi değiştirin.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Görünüm içindeki **kaydırılan** işlevi değiştirerek, yeniden işle metni görüntüleyin.

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

5. Uygulamayı çalıştırın. "Havuz" veya "Görünüm" gibi ortak bir dönemde arama yapın ve aynı derecelendirmeye sahip olan oteller artık daha fazla süre için azalan sırada görüntülendiğini doğrulayın.

    ![Yeniden sipariş tarihini sıralama](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Sonuçları coğrafi noktadan bir uzaklığa göre filtrele

Derecelendirme ve buluşma tarihi, en iyi şekilde azalan düzende görüntülenen özelliklerin örnekleridir. Alfabetik bir liste, artan düzende iyi bir şekilde bir örnektir (örneğin, yalnızca bir **OrderBy** özelliği varsa ve **hotelname** olarak ayarlanmışsa alfabetik bir sıra görüntülenir). Ancak, örnek verilerimizde, coğrafi bir noktadan uzaklık daha uygun olacaktır.

Sonuçları coğrafi mesafeye göre göstermek için birkaç adım gereklidir.

1. Boylam, enlem ve RADIUS parametrelerine sahip bir filtre girerek, belirtilen bir yarıçapın dışındaki tüm otelleri filtreleyin. Boylam önce POINT işlevine verilir. Yarıçap kilometrede.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. Yukarıdaki filtre sonuçları uzaklığa göre _sıralamaz_ ve yalnızca aykırı değerleri kaldırır. Sonuçları sıralamak için geoDistance yöntemini belirten bir **OrderBy** ayarı girin.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Sonuçlar, Azure Bilişsel Arama bir uzaklık filtresi kullanılarak döndürülübilse de, veriler ile belirtilen nokta arasındaki hesaplanan _uzaklık döndürülmez._ Bu değeri, sonuçlarda görüntülemek istiyorsanız Görünüm veya denetleyicide yeniden hesaplayın.

    Aşağıdaki kod, iki Enlem/Boylam noktası arasındaki mesafeyi hesaplar.

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

4. Şimdi bu kavramları birbirine bağlamak gerekir. Bununla birlikte, bu kod parçacıkları öğreticimizin ulaştığı kadar çok olduğu için harita tabanlı bir uygulama oluşturmak okuyucu için bir alıştırma olarak kalır. Bu örneği daha fazla almak için yarıçap ile bir şehir adı girmeyi veya haritada bir nokta konumlandırmayı ve bir RADIUS seçmeyi düşünün. Bu seçenekleri daha fazla araştırmak için aşağıdaki kaynaklara bakın:

* [Azure Haritalar Belgeleri](https://docs.microsoft.com/azure/azure-maps/)
* [Azure haritalar arama hizmetini kullanarak bir adres bulma](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Puanlama profili temelinde sonuçları sıralama

Öğreticide verilen örnekler, _tam_ olarak sıralı bir işlem sağlayan sayısal değerleri (derecelendirme, yeniden oluşturma tarihi, coğrafi uzaklık) nasıl sıralayarak göstermektedir. Ancak bazı aramalar ve bazı veriler, bu iki veri öğesi arasında kolay bir karşılaştırmaya kendisini hiçbir şekilde vermez. Azure Bilişsel Arama _Puanlama_kavramını içerir. _Puanlama profilleri_ , daha karmaşık ve nitel karşılaştırmaları sağlamak için kullanılabilen bir veri kümesi için belirtilebilir. Bu, ne zaman en değerli olması gerekir, örneğin, ilk olarak görüntülenmek üzere metin tabanlı verileri karşılaştırma.

Puanlama profilleri kullanıcılar tarafından, genellikle bir veri kümesinin yöneticileri tarafından tanımlanmamıştır. Otel verilerinde çeşitli Puanlama profilleri ayarlanmış. Puanlama profilinin nasıl tanımlandığını inceleyelim, sonra da bunları aramak için kod yazmayı deneyin.

### <a name="how-scoring-profiles-are-defined"></a>Puanlama profillerinin tanımlanması

Puanlama profillerinin üç örneğini inceleyelim ve bunların _her birinin sonuçlar_ sırasını nasıl etkileyeceğini düşünün. Uygulama geliştiricisi olarak, bu profilleri yazmayın, ancak veri Yöneticisi tarafından yazılır, ancak söz konusu söz dizimini göz atmak yararlıdır.

1. Bu, bir **OrderBy** veya **ScoringProfile** parametresi belirtmediğinden kullanılan oteller veri kümesi için varsayılan Puanlama profilidir. Bu profil, otel adında, açıklamada veya etiket listesinde (değişiklik) arama metni varsa, bir otel için _puanı_ artırır. Puanlama ağırlıklarının belirli alanları nasıl tercih ettiğini unutmayın. Arama metni başka bir alanda görüntüleniyorsa, aşağıda listelenmediyse 1 ağırlığı olur. Puan arttıkça, daha önceki bir sonuç görünümde görüntülenir.

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

2. Aşağıdaki Puanlama profili, sağlanan bir parametre bir veya daha fazla etiket listesi içeriyorsa ("değişiklik" yaptığımız) puanı önemli ölçüde artırır. Bu profilin anahtar noktası, metin içeren bir parametre sağlanması _gerekir_ . Parametre boşsa veya sağlanmazsa bir hata oluşur.
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

3. Bu Üçüncü örnekte, derecelendirme puanı önemli ölçüde artırır. Son yeniden kullanım tarihi de puanı artırır, ancak bu veriler geçerli tarihin 730 gün (2 yıl) içinde yer alıyorsa puan de artırır.

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    Şimdi, bu profillerin çalıştığını düşündüğünüzden bize bakalım!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Profilleri karşılaştırmak için görünüme kod ekleyin

1. İndex. cshtml dosyasını açın ve &lt;Body&gt; bölümünü aşağıdaki kodla değiştirin.

    ```cs
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

2. SearchData.cs dosyasını açın ve **Searchdata** sınıfını aşağıdaki kodla değiştirin.

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

3. Oteller. css dosyasını açın ve aşağıdaki HTML sınıflarını ekleyin.

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

2.  Bu örnekte, ilk görünümü döndürenden biraz daha fazlasını yapmak için **Dizin** için ilk çağrıya ihtiyacımız var. Yöntemi artık görünümde görüntülenecek en fazla 20 değişiklik arar.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

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

3. Modelleri geçici depolamaya kaydetmek ve geçici depolama alanından kurtarmak ve bir modeli doldurmak için iki özel yöntem gerekir.

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

4. **OrderBy** ve **ScoringProfile** parametrelerini gereken şekilde ayarlamanız gerekir. Var olan **Dizin (SearchData model)** yöntemini aşağıdaki kodla değiştirin.

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
                    RecoverFacets(model,false);

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

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
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
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Her **anahtar** seçimi için açıklamaları okuyun.

5. Önceki bölümde yer alan ve birden çok özelliği temel alan sıralama için ek kodu tamamladığınız takdirde, **sonraki** eylemde herhangi bir değişiklik yapmak zorunda değilsiniz.

### <a name="run-and-test-the-app"></a>Uygulamayı çalıştırma ve test etme

1. Uygulamayı çalıştırın. Görünümde tam bir değişiklik kümesi görmeniz gerekir.

2. Sıralama için, "sayısal derecelendirmeye göre" seçeneğinin belirlenmesi, bu öğreticide daha önce uygulamış olduğunuz sayısal sıralamayı, eşit derecelendirmenin otelleriyle ilgili bir tarih karar verecek şekilde verir.

![Derecelendirmeye göre "plaj" sıralaması](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Şimdi "değişiklik ölçütü" profilini deneyin. Çeşitli değişiklik seçimlerini yapın ve bu değişikliklerle ilgili oteller, sonuçlar listesinin yükseltildiğini doğrulayın.

![Profile göre "plaj" sıralaması](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Beklediğiniz şeyi öğrenmek için "yeniden düzenlenmiş Tarih/derecelendirme profili" ni deneyin. Yalnızca son yeniden düzenlenen oteller bir _yenilik_ artışı almalıdır.

### <a name="resources"></a>Kaynaklar

Daha fazla bilgi için bkz. [Azure bilişsel arama dizinine Puanlama profilleri ekleme](https://docs.microsoft.com/azure/search/index-add-scoring-profiles).

## <a name="takeaways"></a>Paketler

Bu projeden aşağıdaki bu devralmayı göz önünde bulundurun:

* Kullanıcılar arama sonuçlarının en çok ilgisi olacak şekilde sıralanmasını bekler.
* Verilerin, sıralamanın kolay olması için yapılandırılması gerekir. "Cheapest" üzerinde, verilerin ek kod olmadan yapılmasını sağlamak üzere yapılandırılmamış olması için önce kolayca sıralayamazsınız.
* Daha yüksek bir sıralama düzeyinde aynı değere sahip olan sonuçlara ayırt etmek için sıralama için çok sayıda düzey olabilir.
* Bazı sonuçların artan düzende (örneğin, bir noktadan uzağa) ve bazıları azalan sırada (örneğin, konuğun derecelendirmesi) sırada sıralanabilmesi doğal bir şekilde yapılır.
* Bir veri kümesi için sayısal karşılaştırmalar yoksa veya yeterince akıllı olmadığında Puanlama profilleri tanımlanabilir. Puanlama her sonuç, sonuçların bir şekilde sipariş edilmesine ve görüntülenmesini sağlamaya yardımcı olur.

## <a name="next-steps"></a>Sonraki adımlar

Bu C# öğretici serisini tamamladınız-Azure bilişsel arama API 'lerinde değerli bilgiye sahip olmanız gerekir.

Daha fazla başvuru ve öğretici için, [Azure bilişsel arama belgelerindeki](https://docs.microsoft.com/azure/search/) [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure)veya Diğer öğreticilere göz atmayı düşünün.
