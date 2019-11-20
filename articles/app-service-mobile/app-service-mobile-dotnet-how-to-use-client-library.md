---
title: App Service Mobile Apps yönetilen istemci kitaplığı ile çalışma | Microsoft Docs
description: .NET istemci kitaplığı 'nı Windows ve Xamarin uygulamalarıyla Azure App Service Mobile Apps nasıl kullanacağınızı öğrenin.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: crdun
editor: ''
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 1a56c6eed8b6a78587ea272c44f01d16e8a2b88d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388712"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Azure Mobile Apps için yönetilen istemci kullanma
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış
Bu kılavuzda, Windows ve Xamarin uygulamaları için Azure App Service Mobile Apps yönetilen istemci kitaplığını kullanarak genel senaryoları nasıl gerçekleştireceğiniz gösterilmektedir. Mobile Apps yeni olduğunuzda, önce [Azure Mobile Apps hızlı başlangıç][1] öğreticisini tamamlamayı düşünmelisiniz. Bu kılavuzda, istemci tarafı yönetilen SDK 'ya odaklanıyoruz. Mobile Apps yönelik sunucu tarafı SDK 'Ları hakkında daha fazla bilgi için, [.NET Server SDK][2] veya [Node. js sunucu SDK][3]belgelerine bakın.

## <a name="reference-documentation"></a>Başvuru belgeleri
İstemci SDK 'Sı için başvuru belgeleri şurada bulunur: [Azure Mobile Apps .NET istemci başvurusu][4].
[Azure-Samples GitHub deposunda][5]birçok istemci örneği de bulabilirsiniz.

## <a name="supported-platforms"></a>Desteklenen platformlar
.NET platformu aşağıdaki platformları destekler:

* API 19 ile 24 arasında Xamarin Android sürümleri (KitKat ile Nougat)
* İOS sürümleri 8,0 ve üzeri için Xamarin iOS sürümleri
* Evrensel Windows Platformu
* Windows Phone 8,1
* Silverlight uygulamaları hariç Windows Phone 8,0

"Sunucu-akışı" kimlik doğrulaması, sunulan kullanıcı arabirimi için bir WebView kullanır.  Cihaz bir WebView Kullanıcı arabirimini sunmıyorsa, diğer kimlik doğrulama yöntemleri gereklidir.  Bu SDK bu nedenle, Izleme türü veya benzer kısıtlanmış cihazlar için uygun değildir.

## <a name="setup"></a>Kurulum ve Önkoşullar
En az bir tablo içeren mobil uygulama arka uç projenizi zaten oluşturmuş ve yayımladığınızı varsayalım.  Bu konuda kullanılan kodda, tablo `TodoItem` olarak adlandırılmıştır ve şu sütunlara sahiptir: `Id`, `Text` ve `Complete`. Bu tablo, [Azure Mobile Apps hızlı başlangıç][1]adımlarını tamamladıktan sonra oluşturulan tablodur.

' De C# karşılık gelen türü belirtilmiş istemci tarafı türü aşağıdaki sınıftır:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

[Jsonpropertyattribute][6] , istemci alanı ve tablo alanı arasındaki *PropertyName* eşlemesini tanımlamak için kullanılır.

Mobile Apps arka ucunuzdaki tablo oluşturmayı öğrenmek için [.NET Server SDK konusuna][7] veya [Node. js sunucu SDK][8]konusuna bakın. Mobil uygulama arka ucunuzu hızlı başlangıç kullanarak Azure portal oluşturduysanız, [Azure portalda] **kolay tablolar** ayarını da kullanabilirsiniz.

### <a name="how-to-install-the-managed-client-sdk-package"></a>Nasıl yapılır: yönetilen istemci SDK paketini yüklemek
[NuGet][9]'den Mobile Apps için YÖNETILEN istemci SDK paketini yüklemek için aşağıdaki yöntemlerden birini kullanın:

* **Visual Studio** Projenize sağ tıklayın, **NuGet Paketlerini Yönet**' e tıklayın, `Microsoft.Azure.Mobile.Client` paketini arayın ve ardından **Install**' a tıklayın.
* **Xamarin Studio** Projenize sağ tıklayın, **ekle** > **NuGet paketleri Ekle**' ye tıklayın, `Microsoft.Azure.Mobile.Client` paketini arayın ve ardından **paket Ekle**' ye tıklayın.

Ana Etkinlik dosyanızda, aşağıdaki **using** ifadesini eklemeyi unutmayın:

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Android projenizde başvurulan tüm destek paketlerinin aynı sürüme sahip olması gerektiğini unutmayın. SDK, Android platformu için `Xamarin.Android.Support.CustomTabs` bağımlılığı vardır. bu nedenle, projeniz daha yeni destek paketleri kullanıyorsa, çakışmaları önlemek için bu paketi gerekli sürüme doğrudan yüklemeniz gerekir.

### <a name="symbolsource"></a>Nasıl yapılır: Visual Studio 'da hata ayıklama sembolleriyle çalışma
Microsoft. Azure. Mobile ad alanı sembolleri, [Symbolsource][10]'ta kullanılabilir.  Symbols kaynağını Visual Studio ile bütünleştirmek için [symbolsource yönergelerine][11] bakın.

## <a name="create-client"></a>Mobile Apps istemcisini oluşturma
Aşağıdaki kod, mobil uygulama arka ucunuza erişmek için kullanılan [MobileServiceClient][12] nesnesini oluşturur.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

Yukarıdaki kodda `MOBILE_APP_URL` ' ı, [Azure portalda]mobil uygulama arka ucunuzun dikey penceresinde bulunan mobil uygulama arka ucunun URL 'siyle değiştirin. MobileServiceClient nesnesi tek bir olmalıdır.

## <a name="work-with-tables"></a>Tablolarla çalışma
Aşağıdaki bölümde kayıtları arama ve alma ve tablodaki verileri değiştirme ayrıntıları yer alır.  Aşağıdaki konular ele alınmıştır:

* [Tablo başvurusu oluşturma](#instantiating)
* [Sorgu verileri](#querying)
* [Döndürülen verileri filtrele](#filtering)
* [Döndürülen verileri Sırala](#sorting)
* [Sayfalarda verileri döndürme](#paging)
* [Belirli sütunları seç](#selecting)
* [Kimliğe göre kayıt ara](#lookingup)
* [Türsüz sorgularla ilgilenme](#untypedqueries)
* [Veri ekleme](#inserting)
* Veriler güncelleştiriliyor
* [Verileri silme](#deleting)
* [Çakışma çözümü ve Iyimser eşzamanlılık](#optimisticconcurrency)
* [Windows Kullanıcı arabirimine bağlama](#binding)
* [Sayfa boyutunu değiştirme](#pagesize)

### <a name="instantiating"></a>Nasıl yapılır: tablo başvurusu oluşturma
Bir arka uç tablosundaki verileri erişen veya değiştiren tüm kodlar `MobileServiceTable` nesnesindeki işlevleri çağırır. [GetTable] metodunu aşağıdaki gibi çağırarak tabloya bir başvuru alın:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Döndürülen nesne, türü belirtilmiş serileştirme modelini kullanır. Türsüz bir serileştirme modeli de desteklenir. Aşağıdaki örnek, [türsüz bir tabloya başvuru oluşturur]:

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

Türsüz sorgularda, temeldeki OData sorgu dizesini belirtmeniz gerekir.

### <a name="querying"></a>Nasıl yapılır: mobil uygulamanızdan veri sorgulama
Bu bölümde, aşağıdaki işlevleri içeren mobil uygulama arka ucuna sorgu verme açıklanmaktadır:

* [Döndürülen verileri filtrele](#filtering)
* [Döndürülen verileri Sırala](#sorting)
* [Sayfalarda verileri döndürme](#paging)
* [Belirli sütunları seç](#selecting)
* [Verileri KIMLIĞE göre ara](#lookingup)

> [!NOTE]
> Tüm satırların döndürülmesini engellemek için sunucu odaklı bir sayfa boyutu zorlanır.  Sayfalama, büyük veri kümelerinin varsayılan isteklerini, hizmetin olumsuz şekilde etkilenmeden korur.  50 'den fazla satır döndürmek için, [sayfalardaki verileri Döndür](#paging)bölümünde açıklandığı gibi `Skip` ve `Take` yöntemini kullanın.

### <a name="filtering"></a>Nasıl yapılır: döndürülen verileri filtreleme
Aşağıdaki kod, bir sorguya `Where` yan tümcesini ekleyerek verilerin nasıl filtreleneceğini göstermektedir. @No__t-1 özelliği `false` ' ye eşit olan `todoTable` ' dan tüm öğeleri döndürür. [Olmadığı] işlevi, tabloya karşı sorguya bir satır filtreleme koşulu uygular.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Arka uca gönderilen isteğin URI 'sini, tarayıcı geliştirici araçları veya [Fiddler]gibi ileti İnceleme yazılımını kullanarak görüntüleyebilirsiniz. İstek URI 'sine bakarsanız, sorgu dizesinin değiştirildiğini unutmayın:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Bu OData isteği sunucu SDK 'sı tarafından bir SQL sorgusuna çevrilir:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

@No__t-0 yöntemine geçirilen işlev rastgele sayıda koşula sahip olabilir.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Bu örnek, sunucu SDK 'sı tarafından bir SQL sorgusuna çevrilir:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Bu sorgu birden çok yan tümce için de ayrılabilir:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

İki yöntem eşdeğerdir ve birbirlerinin yerine kullanılabilir.  Tek bir sorgudaki @ no__t-1' de birden çok koşul birleştirirken önceki @ no__t-0seçeneği daha kompakt ve önerilir.

@No__t-0 yan tümcesi OData alt kümesine çevrilen işlemleri destekler. İşlemler şunları içerir:

* İlişkisel işleçler (= =,! =, <, < =, >, > =),
* Aritmetik işleçler (+,-,/, *,%),
* Sayı duyarlığı (Math. Floor, Math. tavan),
* Dize işlevleri (length, SUBSTRING, Replace, IndexOf, StartsWith, EndsWith),
* Tarih özellikleri (yıl, ay, gün, saat, dakika, saniye),
* Bir nesnenin özelliklerine erişin ve
* Bu işlemlerden herhangi birini birleştiren ifadeler.

Sunucu SDK 'sının neleri desteklediğini düşünürken [OData v3 belgeleri]düşünebilirsiniz.

### <a name="sorting"></a>Nasıl yapılır: döndürülen verileri sıralama
Aşağıdaki kod, sorguya bir [OrderBy] veya [OrderByDescending] işlevi ekleyerek verilerin nasıl sıralanacağını gösterir. @No__t-0 ' dan `Text` alanı tarafından artan düzende sıralanan öğeleri döndürür.

```csharp
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>Nasıl yapılır: sayfalarda verileri döndürme
Varsayılan olarak, arka uç yalnızca ilk 50 satırı döndürür. [Almanız] metodunu çağırarak döndürülen satır sayısını artırabilirsiniz. Sorgu tarafından döndürülen toplam veri kümesinin belirli bir "sayfasını" istemek için [Şimdilik] yöntemiyle birlikte `Take` kullanın. Aşağıdaki sorgu yürütüldüğünde, tablodaki en üstteki üç öğeyi döndürür.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Aşağıdaki düzeltilen sorgu ilk üç sonucu atlar ve sonraki üç sonucu döndürür. Bu sorgu, sayfa boyutunun üç öğe olduğu, verilerin ikinci "sayfasını" üretir.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

[IncludeTotalCount] metodu, belirtilen tüm sayfalama/limit yan tümcesini yoksayarak döndürülen *Tüm* kayıtların toplam sayısını ister:

```csharp
query = query.IncludeTotalCount();
```

Gerçek bir dünya uygulamasında, yukarıdaki örneğe benzer sorguları bir sayfalayıcı denetimiyle veya sayfalar arasında gezinmek için karşılaştırılabilir Kullanıcı arabirimine kullanabilirsiniz.

> [!NOTE]
> Bir mobil uygulama arka ucunda 50 satırlık sınırı geçersiz kılmak için, [EnableQueryAttribute] ÖĞESINI ortak alma yöntemine de uygulamanız ve sayfalama davranışını belirtmeniz gerekir. Yöntemine uygulandığında, aşağıdaki en fazla döndürülen satırları 1000 olarak ayarlar:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Nasıl yapılır: belirli sütunları seçme
Sorgunuza bir [Seç] yan tümcesi ekleyerek sonuçlara hangi özellik kümesinin ekleneceğini belirtebilirsiniz. Örneğin, aşağıdaki kod, tek bir alanın nasıl ve ayrıca birden çok alanı seçme ve biçimlendirme işlemlerinin nasıl yapılacağını göstermektedir:

```csharp
// Select one field -- just the Text
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => todoItem.Text);
List<string> items = await query.ToListAsync();

// Select multiple fields -- both Complete and Text info
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => string.Format("{0} -- {1}",
                    todoItem.Text.PadRight(30), todoItem.Complete ?
                    "Now complete!" : "Incomplete!"));
List<string> items = await query.ToListAsync();
```

Şimdiye kadar açıklanan tüm işlevler eklenebilir, bu nedenle zincirleme devam edebilir. Her zincirleme çağrı sorguyu etkiler. Bir örnek daha:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Nasıl yapılır: KIMLIĞE göre verileri arama
[LookupAsync] işlevi, veritabanından belırlı bir kimliğe sahip nesneleri aramak için kullanılabilir.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Nasıl yapılır: türsüz sorgular yürütme
Türsüz bir tablo nesnesi kullanarak bir sorgu yürütürken, aşağıdaki örnekte olduğu gibi, [ReadAsync]'ı çağırarak OData sorgu dizesini açıkça belirtmeniz gerekir:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Bir özellik paketi gibi kullanabileceğiniz JSON değerlerini geri alırsınız. JToken ve Newtonsoft Json.NET hakkında daha fazla bilgi için bkz. [JSON.net] sitesi.

### <a name="inserting"></a>Nasıl yapılır: mobil uygulama arka ucuna veri ekleme
Tüm istemci türleri, varsayılan olarak bir dize olan **kimliği**adlı bir üye içermelidir. Bu **kimlik** , CRUD işlemlerini gerçekleştirmek ve çevrimdışı eşitleme için gereklidir. Aşağıdaki kod, bir tabloya yeni satırlar eklemek için [Insertasync] yönteminin nasıl kullanılacağını gösterir. Parametresi .NET nesnesi olarak eklenecek verileri içerir.

```csharp
await todoTable.InsertAsync(todoItem);
```

Bir ekleme sırasında `todoItem` ' a benzersiz bir özel KIMLIK değeri dahil edilmemelidir, sunucu tarafından bir GUID oluşturulur.
Çağrı dönüşdikten sonra nesneyi inceleyerek oluşturulan kimliği alabilirsiniz.

Türsüz verileri eklemek için Json.NET avantajlarından yararlanabilirsiniz:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Benzersiz bir dize kimliği olarak bir e-posta adresi kullanan bir örnek aşağıda verilmiştir:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>KIMLIK değerleriyle çalışma
Mobile Apps, tablonun **kimlik** sütunu için benzersiz özel dize değerlerini destekler. Bir dize değeri, uygulamaların, KIMLIK için e-posta adresleri veya Kullanıcı adları gibi özel değerleri kullanmasına izin verir.  Dize kimlikleri aşağıdaki avantajları sağlar:

* Kimlikler, veritabanına gidiş dönüş yapılmadan oluşturulur.
* Kayıtlar farklı tablolardan veya veritabanlarından birleştirmek daha kolaydır.
* Kimlikler değerleri, bir uygulamanın mantığı ile daha iyi tümleştirilebilir.

Ekli bir kayıtta bir dize KIMLIĞI değeri ayarlanmamışsa, mobil uygulama arka ucu KIMLIK için benzersiz bir değer oluşturur. İstemci veya arka uçta kendi KIMLIK değerlerinizi oluşturmak için [GUID. NewGuid] yöntemini kullanabilirsiniz.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Nasıl yapılır: bir mobil uygulama arka ucunda verileri değiştirme
Aşağıdaki kod, aynı KIMLIĞE sahip mevcut bir kaydı yeni bilgilerle güncelleştirmek için [UpdateAsync] yönteminin nasıl kullanılacağını gösterir. Parametresi, .NET nesnesi olarak güncellenen verileri içerir.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Türsüz verileri güncelleştirmek için, [JSON.net] 'den şu şekilde yararlanabilirsiniz:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Güncelleştirme yapılırken `id` alanı belirtilmelidir. Arka uç, hangi satırın güncelleştirilecek olduğunu belirlemek için `id` alanını kullanır. @No__t-0 alanı `InsertAsync` çağrısının sonucundan elde edilebilir. @No__t-1 değeri sağlamadan bir öğeyi güncelleştirmeye çalıştığınızda bir `ArgumentException` oluşturulur.

### <a name="deleting"></a>Nasıl yapılır: bir mobil uygulama arka ucunda verileri silme
Aşağıdaki kod, var olan bir örneği silmek için [DeleteAsync] yönteminin nasıl kullanılacağını gösterir. Örnek, `todoItem` ' de ayarlanmış `id` alanı tarafından tanımlanır.

```csharp
await todoTable.DeleteAsync(todoItem);
```

Türsüz verileri silmek için, Json.NET ' den şu şekilde yararlanabilirsiniz:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Silme isteği yaptığınızda bir KIMLIK belirtilmelidir. Diğer özellikler hizmete geçirilmez veya hizmette yok sayılır. @No__t-0 çağrısının sonucu genellikle `null` ' dir. Geçirilecek KIMLIK `InsertAsync` çağrısının sonucundan elde edilebilir. @No__t-1 alanını belirtmeden bir öğeyi silmeye çalıştığınızda bir `MobileServiceInvalidOperationException` oluşturulur.

### <a name="optimisticconcurrency"></a>Nasıl yapılır: çakışma çözümü için Iyimser eşzamanlılık kullanma
İki veya daha fazla istemci aynı öğedeki değişiklikleri aynı anda yazabilir. Çakışma algılaması olmadan, son yazma önceki güncelleştirmelerin üzerine yazar. **İyimser eşzamanlılık denetimi** , her bir işlemin işleme ve bu nedenle herhangi bir kaynak kilitlemeyi kullanmayan varsayılmaktadır.  Bir işlemi işlemeden önce, iyimser eşzamanlılık denetimi başka hiçbir işlemin verileri değiştirmadığını doğrular. Veriler değiştirilmişse, işleme işlemi geri alınır.

Mobile Apps, mobil uygulama arka ucunuzdaki her tablo için tanımlanan `version` sistem özelliği sütununu kullanarak her bir öğedeki değişiklikleri izleyerek iyimser eşzamanlılık denetimini destekler. Kayıt her güncelleştirildiği zaman, Mobile Apps bu kayıt için `version` özelliğini yeni bir değere ayarlar. Her güncelleştirme isteği sırasında, isteğe dahil edilen kaydın `version` özelliği, sunucudaki kayıt için aynı özellik ile karşılaştırılır. İstekle geçilen sürüm arka uca eşleşmiyorsa, istemci kitaplığı bir `MobileServicePreconditionFailedException<T>` özel durumu oluşturur. Özel duruma dahil edilen tür, kaydın sunucular sürümünü içeren arka ucun kaydıdır. Uygulama daha sonra bu bilgileri kullanarak, değişiklikleri kaydetmek için arka uçta doğru `version` değeriyle güncelleştirme isteğini yeniden çalıştırıp yürütmemeye karar verebilirsiniz.

İyimser eşzamanlılık sağlamak üzere `version` sistem özelliği için tablo sınıfında bir sütun tanımlayın. Örnek:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }

    // *** Enable Optimistic Concurrency *** //
    [JsonProperty(PropertyName = "version")]
    public string Version { set; get; }
}
```

Türsüz tablolar kullanan uygulamalar, tablonun `SystemProperties` bayrağını aşağıdaki gibi @no__t ayarlayarak iyimser eşzamanlılık sağlar.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

İyimser eşzamanlılık etkinleştirmenin yanı sıra, [UpdateAsync]çağrılırken kodunuzda `MobileServicePreconditionFailedException<T>` özel durumunu da yakalamalı olmanız gerekir.  Güncelleştirilmiş kayda doğru `version` uygulayarak çakışmayı çözün ve çözümlenen kayıtla [UpdateAsync] çağırın. Aşağıdaki kod, bir yazma çakışmasını tespit edildikten sonra nasıl çözümleneceğini gösterir:

```csharp
private async void UpdateToDoItem(TodoItem item)
{
    MobileServicePreconditionFailedException<TodoItem> exception = null;

    try
    {
        //update at the remote table
        await todoTable.UpdateAsync(item);
    }
    catch (MobileServicePreconditionFailedException<TodoItem> writeException)
    {
        exception = writeException;
    }

    if (exception != null)
    {
        // Conflict detected, the item has changed since the last query
        // Resolve the conflict between the local and server item
        await ResolveConflict(item, exception.Item);
    }
}


private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
{
    //Ask user to choose the resolution between versions
    MessageDialog msgDialog = new MessageDialog(
        String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
        serverItem.Text, localItem.Text),
        "CONFLICT DETECTED - Select a resolution:");

    UICommand localBtn = new UICommand("Commit Local Text");
    UICommand ServerBtn = new UICommand("Leave Server Text");
    msgDialog.Commands.Add(localBtn);
    msgDialog.Commands.Add(ServerBtn);

    localBtn.Invoked = async (IUICommand command) =>
    {
        // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        // catching a MobileServicePreConditionFailedException.
        localItem.Version = serverItem.Version;

        // Updating recursively here just in case another change happened while the user was making a decision
        UpdateToDoItem(localItem);
    };

    ServerBtn.Invoked = async (IUICommand command) =>
    {
        RefreshTodoItems();
    };

    await msgDialog.ShowAsync();
}
```

Daha fazla bilgi için bkz. [Azure Mobile Apps’te Çevrimdışı Veri Eşitleme] konusu.

### <a name="binding"></a>Nasıl yapılır: Windows Kullanıcı arabirimine Mobile Apps verileri bağlama
Bu bölümde, bir Windows uygulamasında kullanıcı arabirimi öğeleri kullanılarak döndürülen veri nesnelerinin nasıl görüntüleneceği gösterilmektedir.  Aşağıdaki örnek kod, tamamlanmamış öğeler için bir sorguyla birlikte listenin kaynağına bağlanır. [MobileServiceCollection] Mobile Apps duyarlı bir bağlama koleksiyonu oluşturur.

```csharp
// This query filters out completed TodoItems.
MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToCollectionAsync();

// itemsControl is an IEnumerable that could be bound to a UI list control
IEnumerable itemsControl  = items;

// Bind this to a ListBox
ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Yönetilen çalışma zamanındaki bazı denetimler [ISupportIncrementalLoading]adlı bir arabirimi destekler. Bu arabirim, Kullanıcı kaydırıldığında denetimlerin ek veri istemesine izin verir. Bu arabirim için, [MobileServiceIncrementalLoadingCollection]aracılığıyla Evrensel Windows uygulamaları için yerleşik destek bulunur ve bu, denetimlerden gelen çağrıları otomatik olarak işler. Windows uygulamalarında `MobileServiceIncrementalLoadingCollection` ' yı aşağıdaki gibi kullanın:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Yeni koleksiyonu Windows Phone 8 ve "Silverlight" uygulamalarında kullanmak için `IMobileServiceTableQuery<T>` ve `IMobileServiceTable<T>` ' de `ToCollection` uzantı yöntemlerini kullanın. Verileri yüklemek için `LoadMoreItemsAsync()` ' ı çağırın.

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

@No__t-0 veya `ToCollection` ' i çağırarak oluşturulan koleksiyonu kullandığınızda, UI denetimlerine bağlanabilen bir koleksiyon alırsınız.  Bu koleksiyon disk belleğine duyarlı.  Koleksiyon ağdan veri yüklüyor olduğundan, yükleme bazen başarısız olur. Bu tür sorunları işlemek için, `LoadMoreItemsAsync` ' ye yapılan çağrılardan kaynaklanan özel durumları işlemek için `MobileServiceIncrementalLoadingCollection` üzerindeki `OnException` yöntemini geçersiz kılın.

Tablonuzun çok fazla alan olup olmadığını ve yalnızca bazı bunları denetimenizde göstermek istediğinizi göz önünde bulundurun. Kullanıcı arabiriminde görüntülenecek belirli sütunları seçmek için yukarıdaki "[belirli sütunları seç](#selecting)" bölümündeki Kılavuzu kullanabilirsiniz.

### <a name="pagesize"></a>Sayfa boyutunu değiştirme
Azure Mobile Apps, varsayılan olarak istek başına en fazla 50 öğe döndürür.  Hem istemcide hem de sunucuda maksimum sayfa boyutunu artırarak disk belleği boyutunu değiştirebilirsiniz.  İstenen sayfa boyutunu artırmak için, `PullAsync()` kullanırken `PullOptions` belirtin:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

@No__t-0 ' a eşit veya sunucu içinde 100 ' den büyük bir istek olduğunu varsayarsak, bir istek en fazla 100 öğe döndürür.

## <a name="#offlinesync"></a>Çevrimdışı tablolarla çalışma
Çevrimdışı tablolar, verileri çevrimdışıyken kullanılmak üzere depolamak için yerel bir SQLite depo kullanır.  Tüm tablo işlemleri, uzak sunucu deposu yerine yerel SQLite deposuna göre yapılır.  Çevrimdışı bir tablo oluşturmak için önce projenizi hazırlayın:

1. Visual Studio 'da çözüme sağ tıklayın > çözüm **Için NuGet Paketlerini Yönet...** ' i sağ tıklatın ve Çözümdeki tüm projeler için **Microsoft. Azure. Mobile. Client. SQLiteStore** NuGet paketini arayın ve yüklemeyi yapın.
2. Seçim Windows cihazlarını desteklemek için aşağıdaki SQLite çalışma zamanı paketlerinden birini yüklemelisiniz:

   * **Windows 8.1 çalışma zamanı:** [Windows 8.1 Için SQLite][3]'ı yükler.
   * **Windows Phone 8,1:** [Windows Phone 8,1 Için SQLite][4]'ı yükler.
   * **Evrensel Windows platformu** [Evrensel pencereler Için SQLite][5]'ı yükler.
3. (İsteğe bağlı). Windows cihazları için, **başvurular** > **Başvuru Ekle...** ' ye tıklayın, **Windows** klasör > **uzantıları**' nı genişletin ve ardından **Visual C++ 2013 çalışma zamanı ile birlikte Windows SDK için uygun SQLite 'u etkinleştirin Windows SDK için** .
    SQLite SDK adları her bir Windows platformunda biraz farklılık gösterir.

Bir tablo başvurusu oluşturulmadan önce, yerel depo hazırlanmalıdır:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Mağaza başlatması normalde istemci oluşturulduktan hemen sonra yapılır.  **Offlinedbyolu** , destekledikleri tüm platformlarda kullanılmak üzere uygun bir dosya adı olmalıdır.  Yol tam olarak nitelenmiş bir yoldur (yani eğik çizgiyle başlıyorsa), bu yol kullanılır.  Yol tam nitelikli değilse, dosya platforma özgü bir konuma yerleştirilir.

* İOS ve Android cihazlarda, varsayılan yol "kişisel dosyalar" klasörüdür.
* Windows cihazlarında, varsayılan yol uygulamaya özgü "AppData" klasörüdür.

@No__t-0 yöntemi kullanılarak bir tablo başvurusu elde edilebilir:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Çevrimdışı bir tablo kullanmak için kimlik doğrulaması yapmanız gerekmez.  Yalnızca arka uç hizmetiyle iletişim kurarken kimlik doğrulaması yapmanız gerekir.

### <a name="syncoffline"></a>Çevrimdışı bir tabloyu eşitleme
Çevrimdışı tablolar varsayılan olarak arka uca eşitlenmez.  Eşitleme iki parçaya bölünür.  Değişiklikleri yeni öğeler indirilmeden ayrı olarak gönderebilirsiniz.  Tipik bir eşitleme yöntemi aşağıda verilmiştir:

```csharp
public async Task SyncAsync()
{
    ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

    try
    {
        await this.client.SyncContext.PushAsync();

        await this.todoTable.PullAsync(
            //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
            //Use a different query name for each unique query in your program
            "allTodoItems",
            this.todoTable.CreateQuery());
    }
    catch (MobileServicePushFailedException exc)
    {
        if (exc.PushResult != null)
        {
            syncErrors = exc.PushResult.Errors;
        }
    }

    // Simple error/conflict handling. A real application would handle the various errors like network conditions,
    // server conflicts and others via the IMobileServiceSyncHandler.
    if (syncErrors != null)
    {
        foreach (var error in syncErrors)
        {
            if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
            {
                //Update failed, reverting to server's copy.
                await error.CancelAndUpdateItemAsync(error.Result);
            }
            else
            {
                // Discard local change.
                await error.CancelAndDiscardItemAsync();
            }

            Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
        }
    }
}
```

@No__t-0 ' a yönelik ilk bağımsız değişken null ise, artımlı eşitleme kullanılmaz.  Her eşitleme işlemi tüm kayıtları alır.

SDK, kayıtları çekmeden önce örtük bir @no__t (0) gerçekleştirir.

@No__t-0 yönteminde çakışma işleme gerçekleşir.  Çakışmalarla, çevrimiçi tablolarla aynı şekilde ilgilenebilmeniz gerekir.  Ekleme, güncelleştirme veya silme sırasında yerine `PullAsync()` çağrıldığında çakışma üretilir. Birden çok çakışma meydana geliyorsa, bunlar tek bir MobileServicePushFailedException içinde paketlenmiştir.  Her başarısızlığı ayrı olarak işleyin.

## <a name="#customapi"></a>Özel bir API ile çalışma
Özel bir API, bir INSERT, Update, DELETE veya Read işlemiyle eşleşmeyen sunucu işlevlerini açığa çıkaran özel uç noktalar tanımlamanızı sağlar. Özel bir API kullanarak ileti üzerinde daha fazla denetime sahip olabilirsiniz ve HTTP ileti üst bilgilerini okumak ve ayarlamak ve JSON dışında bir ileti gövdesi biçimi tanımlamak dahil olabilirsiniz.

İstemci üzerindeki [Invokeapiasync] yöntemlerinden birini çağırarak özel bir API 'yi çağırabilirsiniz. Örneğin, aşağıdaki kod satırı arka uçtaki **Completeall** API 'SINE bir post isteği gönderir:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Bu form, yazılı bir yöntem çağrıdır ve **Markallresult** dönüş türünün tanımlanmasını gerektirir. Hem yazılan hem de türsüz yöntemler desteklenir.

Invokeapiasync () yöntemi, API '/' ile başlanmadığı takdirde çağırmak istediğiniz API 'ye '/api/' öğesine eklenir.
Örnek:

* `InvokeApiAsync("completeAll",...)`, arka uçta/api/completeAll çağırır
* `InvokeApiAsync("/.auth/me",...)` ' da/. auth/Me çağrısı arka uçta

Azure Mobile Apps ile tanımlanmayan bu WebAPIs dahil olmak üzere herhangi bir WebAPI çağırmak için ınvokeapiasync kullanabilirsiniz.  Invokeapiasync () kullandığınızda, kimlik doğrulama üst bilgileri de dahil olmak üzere uygun üstbilgiler istekle birlikte gönderilir.

## <a name="authentication"></a>Kullanıcıların kimliklerini doğrulama
Mobile Apps, farklı dış kimlik sağlayıcıları kullanarak uygulama kullanıcılarının kimlik doğrulamasını ve yetkilendiriyi destekler: Facebook, Google, Microsoft hesabı, Twitter ve Azure Active Directory. Belirli işlemlere erişimi yalnızca kimliği doğrulanmış kullanıcılarla kısıtlamak için tablolar üzerinde izinler belirleyebilirsiniz. Ayrıca, sunucu betiklerine yetkilendirme kuralları uygulamak için kimliği doğrulanmış kullanıcıların kimliğini de kullanabilirsiniz. Daha fazla bilgi için [Uygulamanıza kimlik doğrulaması ekleme] öğreticisine bakın.

İki kimlik doğrulama akışı desteklenir: *istemci tarafından yönetilen* ve *sunucu tarafından yönetilen* akış. Sunucu tarafından yönetilen akış, sağlayıcının web kimlik doğrulama arabirimini temel aldığından en basit kimlik doğrulama deneyimini sağlar. İstemci tarafından yönetilen akış, sağlayıcıya özel cihaza özgü SDK 'lara bağlı olduğundan cihaza özgü yetenekler ile daha derin tümleştirme sağlar.

> [!NOTE]
> Üretim uygulamalarınızda istemci tarafından yönetilen bir akış kullanmanızı öneririz.

Kimlik doğrulaması ayarlamak için uygulamanızı bir veya daha fazla kimlik sağlayıcısıyla kaydetmeniz gerekir.  Kimlik sağlayıcısı, uygulamanız için bir istemci KIMLIĞI ve bir istemci gizli anahtarı oluşturur.  Bu değerler daha sonra Azure App Service Kimlik Doğrulaması/Yetkilendirmeyi etkinleştirmek için arka ucunuza ayarlanır.  Daha fazla bilgi için, [uygulamanıza kimlik doğrulaması ekleme]öğreticisindeki ayrıntılı yönergeleri izleyin.

Bu bölümde aşağıdaki konular ele alınmıştır:

* [İstemci tarafından yönetilen kimlik doğrulaması](#clientflow)
* [Sunucu tarafından yönetilen kimlik doğrulaması](#serverflow)
* [Kimlik doğrulama belirtecini önbelleğe alma](#caching)

### <a name="clientflow"></a>İstemci tarafından yönetilen kimlik doğrulaması
Uygulamanız kimlik sağlayıcısıyla bağımsız olarak iletişim kurabilerek arka ucunuza oturum açma sırasında döndürülen belirteci sağlayabilir. Bu istemci akışı, kullanıcılar için bir çoklu oturum açma deneyimi sağlamanıza veya kimlik sağlayıcısından ek kullanıcı verileri almanıza olanak sağlar. Kimlik sağlayıcısı SDK 'Sı daha yerel bir UX sağlar ve ek özelleştirmeye izin veriyorsa, istemci akışı kimlik doğrulaması bir sunucu akışı kullanımı için tercih edilir.

Aşağıdaki istemci akışı kimlik doğrulama desenleri için örnekler verilmiştir:

* [Active Directory Authentication Library](#adal)
* [Facebook veya Google](#client-facebook)

#### <a name="adal"></a>Active Directory Authentication Library kullanıcıların kimliğini doğrulama
Azure Active Directory kimlik doğrulaması kullanarak istemciden kullanıcı kimlik doğrulaması başlatmak için Active Directory Authentication Library (ADAL) kullanabilirsiniz.

1. [Active Directory oturum açma için App Service yapılandırma] izleyerek AAD oturum açma için mobil uygulamanızın arka ucunu yapılandırın. Yerel istemci uygulamasını kaydetme işleminin isteğe bağlı adımını tamamladığınızdan emin olun.
2. Visual Studio veya Xamarin Studio içinde projenizi açın ve `Microsoft.IdentityModel.Clients.ActiveDirectory` NuGet paketine bir başvuru ekleyin. Arama sırasında yayın öncesi sürümleri dahil edin.
3. Aşağıdaki kodu, kullandığınız platforma göre uygulamanıza ekleyin. Her birinde aşağıdaki değişiklikleri yapın:

   * Eklentiyi, uygulamanızı sağladığınız kiracının **adıyla değiştirin.** Biçim https://login.microsoftonline.com/contoso.onmicrosoft.com olmalıdır. Bu değer, [Azure portalda]Azure Active Directory etki alanı sekmesinden kopyalanabilir.
   * **Insert-Resource-ID-burada** , mobil uygulama arka ucunuzun istemci kimliği ile değiştirin. İstemci KIMLIĞINI, portalda **Azure Active Directory ayarlar** ' ın altında bulunan **Gelişmiş** sekmesinden elde edebilirsiniz.
   * **Ekle-ISTEMCI kimliği-** ' ni yerel istemci uygulamasından KOPYALADıĞıNıZ istemci kimliğiyle değiştirin.
   * {1 & gt; **Insert-REDIRECT-URI** & lt; 1} ÖĞESINI, https şemasını kullanarak sitenizin */.Auth/login/done* uç noktasıyla değiştirin Bu değer *https://contoso.azurewebsites.net/.auth/login/done* ' e benzer olmalıdır.

     Her platform için gereken kod aşağıdadır:

     **Windows:**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
     }
     ```

     **Xamarin.iOS**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync(UIViewController view)
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
     }
     ```

     **Xamarin.Android**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
     }
     protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
     {

        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
     }
     ```

#### <a name="client-facebook"></a>Facebook veya Google 'dan bir belirteç kullanarak çoklu oturum açma
Facebook veya Google için bu kod parçacığında gösterildiği gibi istemci akışını kullanabilirsiniz.

```csharp
var token = new JObject();
// Replace access_token_value with actual value of your access token obtained
// using the Facebook or Google SDK.
token.Add("access_token", "access_token_value");

private MobileServiceUser user;
private async Task AuthenticateAsync()
{
    while (user == null)
    {
        string message;
        try
        {
            // Change MobileServiceAuthenticationProvider.Facebook
            // to MobileServiceAuthenticationProvider.Google if using Google auth.
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
            message = string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

### <a name="serverflow"></a>Sunucu tarafından yönetilen kimlik doğrulaması
Kimlik sağlayıcınızı kaydettikten sonra, [MobileServiceClient] üzerinde, sağlayıcınızdaki [Mobileserviceauthenticationprovider] değeri Ile [loginasync] yöntemini çağırın. Örneğin, aşağıdaki kod Facebook kullanarak bir sunucu akışı oturum açma işlemini başlatır.

```csharp
private MobileServiceUser user;
private async System.Threading.Tasks.Task Authenticate()
{
    while (user == null)
    {
        string message;
        try
        {
            user = await client
                .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
            message =
                string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

Facebook dışında bir kimlik sağlayıcısı kullanıyorsanız, [Mobileserviceauthenticationprovider] değerini sağlayıcınızın değerine değiştirin.

Sunucu akışında, Azure App Service seçili sağlayıcının oturum açma sayfasını görüntüleyerek OAuth kimlik doğrulama akışını yönetir.  Kimlik sağlayıcısı döntikten sonra, Azure App Service bir App Service kimlik doğrulama belirteci üretir. [Loginasync] yöntemi, bir JSON Web BELIRTECI (JWT) olarak kimliği doğrulanmış kullanıcının ve [Mobileserviceauthenticationtoken]'ın [UserID] kimliğini sağlayan bir [mobileserviceuser]döndürür. Bu belirteç önbelleğe alınabilir süresi sona erene kadar yeniden kullanılabilir. Daha fazla bilgi için bkz. [kimlik doğrulama belirtecini önbelleğe alma](#caching).

### <a name="caching"></a>Kimlik doğrulama belirtecini önbelleğe alma
Bazı durumlarda, oturum açma yöntemine yapılan çağrının kimlik doğrulama belirteci sağlayıcıdan depolanarak ilk başarılı kimlik doğrulamasından sonra kaçınılabilir.  Microsoft Store ve UWP uygulamaları, başarılı bir oturum açma işleminden sonra geçerli kimlik doğrulama belirtecini şu şekilde önbelleğe almak için [Passwordkasası] kullanabilir:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

UserID değeri kimlik bilgisinin Kullanıcı adı olarak depolanır ve belirteç parola olarak depolanır. Sonraki başlangıçlarda, önbelleğe alınmış kimlik bilgileri için **Passwordkasasını** kontrol edebilirsiniz. Aşağıdaki örnek, bulunan önbelleğe alınmış kimlik bilgilerini kullanır, aksi halde arka uca yeniden kimlik doğrulaması yapmayı dener:

```csharp
// Try to retrieve stored credentials.
var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
if (creds != null)
{
    // Create the current user from the stored credentials.
    client.currentUser = new MobileServiceUser(creds.UserName);
    client.currentUser.MobileServiceAuthenticationToken =
        vault.Retrieve("Facebook", creds.UserName).Password;
}
else
{
    // Regular login flow and cache the token as shown above.
}
```

Bir kullanıcının oturumunu kapattığınızda, depolanan kimlik bilgilerini aşağıdaki gibi de kaldırmanız gerekir:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Xamarin Apps, kimlik bilgilerini bir **Hesap** nesnesinde güvenli bir şekilde depolamak için [Xamarin. auth] API 'lerini kullanır. Bu API 'Leri kullanmayla ilgili bir örnek için [Contosodakika fotoğraf paylaşma örneğindeki](https://github.com/azure-appservice-samples/ContosoMoments) [AuthStore.cs] kod dosyasına bakın.

İstemci tarafından yönetilen kimlik doğrulaması kullandığınızda, sağlayıcınızda Facebook veya Twitter gibi alınan erişim belirtecini de önbelleğe alabilirsiniz. Bu belirteç arka uca yeni bir kimlik doğrulama belirteci istemek için aşağıdaki gibi sağlanabilir:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Anında iletme bildirimleri
Aşağıdaki konularda anında Iletme bildirimleri ele alınmaktadır:

* [Anında Iletme bildirimleri için kaydolun](#register-for-push)
* [Microsoft Store paketi SID 'SI edinin](#package-sid)
* [Platformlar arası şablonlarla kaydolun](#register-xplat)

### <a name="register-for-push"></a>Nasıl yapılır: anında Iletme bildirimleri için kaydolma
Mobile Apps istemcisi Azure Notification Hubs anında iletme bildirimleri için kayıt yapmanızı sağlar. Kayıt sırasında, platforma özgü anında Iletme bildirimi hizmetinden (PNS) edindiğiniz bir tanıtıcı elde edersiniz. Daha sonra, kaydı oluştururken bu değeri herhangi bir etiketle birlikte sağlarsınız. Aşağıdaki kod, Windows Notification hizmeti (WNS) ile birlikte anında iletme bildirimleri için Windows uygulamanızı kaydeder:

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

WNS ' a itiediyorsanız [bir Microsoft Store paketi SID 'si EDINMENIZ](#package-sid)gerekir.  Şablon kayıtlarına kaydolma da dahil olmak üzere Windows uygulamaları hakkında daha fazla bilgi için bkz. [uygulamanıza anında iletme bildirimleri ekleme].

İstemciden etiket isteme desteklenmiyor.  Etiket Istekleri kayıttan sessizce kesilir.
Cihazınızı etiketlere kaydetmek isterseniz, kayıt işlemini gerçekleştirmek için Notification Hubs API 'sini kullanan özel bir API oluşturun.  @No__t-0 yöntemi yerine özel API 'YI çağırın.

### <a name="package-sid"></a>Nasıl yapılır: Microsoft Store paketi SID 'SI edinme
Microsoft Store uygulamalarda anında iletme bildirimlerini etkinleştirmek için bir paket SID 'SI gerekir.  Bir paket SID 'SI almak için, Microsoft Store uygulamanızı kaydedin.

Bu değeri elde etmek için:

1. Visual Studio Çözüm Gezgini Microsoft Store uygulama projesine sağ tıklayın, **mağaza** > **uygulamayı mağaza ile ilişkilendir...** seçeneğine tıklayın.
2. Sihirbazda, **İleri**' ye tıklayın, Microsoft hesabı ile oturum açın, **Yeni bir uygulama adı**ayırarak uygulamanız için bir ad yazın ve ardından **Ayır**' a tıklayın.
3. Uygulama kaydı başarıyla oluşturulduktan sonra uygulama adını seçin, **İleri**' ye tıklayın ve ardından **ilişkilendir**' e tıklayın.
4. Microsoft hesabınızı kullanarak [Windows Geliştirme Merkezi] ' nde oturum açın. **Uygulamalarım**altında oluşturduğunuz uygulama kaydına tıklayın.
5. **Uygulama yönetimi** > **uygulama kimliği**' ne tıklayın ve ardından aşağı kaydırarak **paket SID**'nizi bulun.

Paket SID 'sinin birçok kullanımı, bunu bir URI olarak değerlendirir ve bu durumda, şema olarak *MS-App://* kullanmanız gerekir. Bu değeri bir ön ek olarak birleştirerek, paket SID 'sinin oluşturulmuş sürümünü unutmayın.

Xamarin uygulamaları, iOS veya Android platformlarında çalışan bir uygulamayı kaydedebilmek için bazı ek kodlar gerektirir. Daha fazla bilgi için platformunuzun konusuna bakın:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Nasıl yapılır: platformlar arası bildirimler göndermek için gönderme şablonlarını kaydetme
Şablonları kaydetmek için aşağıdaki gibi şablonlarla birlikte `RegisterAsync()` yöntemini kullanın:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Şablonlarınızın `JObject` türleri olması ve aşağıdaki JSON biçiminde birden çok şablon içermesi gerekir:

```csharp
public JObject myTemplates()
{
    // single template for Windows Notification Service toast
    var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

    var templates = new JObject
    {
        ["generic-message"] = new JObject
        {
            ["body"] = template,
            ["headers"] = new JObject
            {
                ["X-WNS-Type"] = "wns/toast"
            },
            ["tags"] = new JArray()
        },
        ["more-templates"] = new JObject {...}
    };
    return templates;
}
```

**RegisterAsync ()** yöntemi de ikincil kutucukları kabul eder:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Güvenlik için kayıt sırasında tüm Etiketler kaldırılır. Yüklemeler içindeki yüklemelere veya şablonlara Etiketler eklemek için, bkz. [Azure için .NET arka uç sunucu SDK 'Sı ile çalışma Mobile Apps].

Bu kayıtlı şablonların kullanılmasıyla bildirim göndermek için [Notification Hubs API 'Leri]bakın.

## <a name="misc"></a>Çeşitli konular
### <a name="errors"></a>Nasıl yapılır: hataları Işleme
Arka uçta bir hata oluştuğunda, istemci SDK `MobileServiceInvalidOperationException` ' ı başlatır.  Aşağıdaki örnek, arka uç tarafından döndürülen bir özel durumun nasıl işleneceğini göstermektedir:

```csharp
private async void InsertTodoItem(TodoItem todoItem)
{
    // This code inserts a new TodoItem into the database. When the operation completes
    // and App Service has assigned an Id, the item is added to the CollectionView
    try
    {
        await todoTable.InsertAsync(todoItem);
        items.Add(todoItem);
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // Handle error
    }
}
```

Hata koşulları ile ilgilenme ile ilgili başka bir örnek [Mobile Apps dosyaları örneği]bulunabilir. [Logginghandler] örneği, arka uca yapılan istekleri günlüğe kaydetmek için bir günlüğe kaydetme temsilci işleyicisi sağlar.

### <a name="headers"></a>Nasıl yapılır: istek üstbilgilerini özelleştirme
Belirli uygulama senaryonuzu desteklemek için, mobil uygulama arka ucu ile iletişimi özelleştirmeniz gerekebilir. Örneğin, her giden isteğe özel bir üst bilgi eklemek ve hatta yanıt durum kodlarını değiştirmek isteyebilirsiniz. Aşağıdaki örnekte olduğu gibi özel bir [DelegatingHandler]kullanabilirsiniz:

```csharp
public async Task CallClientWithHandler()
{
    MobileServiceClient client = new MobileServiceClient("AppUrl", new MyHandler());
    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
    var newItem = new TodoItem { Text = "Hello world", Complete = false };
    await todoTable.InsertAsync(newItem);
}

public class MyHandler : DelegatingHandler
{
    protected override async Task<HttpResponseMessage>
        SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        // Change the request-side here based on the HttpRequestMessage
        request.Headers.Add("x-my-header", "my value");

        // Do the request
        var response = await base.SendAsync(request, cancellationToken);

        // Change the response-side here based on the HttpResponseMessage

        // Return the modified response
        return response;
    }
}
```


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: https://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: https://github.com/SymbolSource/SymbolSource
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Uygulamanıza kimlik doğrulaması ekleme]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Azure Mobile Apps’te Çevrimdışı Veri Eşitleme]: app-service-mobile-offline-data-sync.md
[Uygulamanıza anında iletme bildirimleri ekleme]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: ../app-service/configure-authentication-provider-microsoft.md
[Active Directory oturum açma için App Service yapılandırma]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[türsüz bir tabloya başvuru oluşturur]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[Insertasync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[Invokeapiasync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Almanız]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Seç]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Şimdilik]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[UserID]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Olmadığı]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure portalda]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid. NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Geliştirme Merkezi]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Passwordkasası]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notification Hubs API 'Leri]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Mobile Apps dosyaları örneği]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 belgeleri]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin. auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
