---
title: Service Fabric ağ oluşturma ve çoklu hizmet uygulaması dağıtma
description: Bu öğreticide bir arka uç web hizmetiyle iletişim kuran bir ASP.NET Core web sitesini içeren çok hizmet sunan bir Azure Service Fabric Mesh uygulaması oluşturacak, yerel ortamda hatalarını ayıklayacak ve Azure'da yayımlayacaksınız.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: b0bdb3c09aead812e1c16f4d0d17aae58e141809
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626801"
---
# <a name="tutorial-create-debug-deploy-and-upgrade-a-multi-service-service-fabric-mesh-app"></a>Öğretici: Çok hizmet sunan bir Service Fabric Mesh uygulaması oluşturma, hata ayıklama, dağıtma ve yükseltme

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

Bu öğretici, bir dizinin birinci bölümüdür. Visual Studio kullanarak ASP.NET web ön ucu ve ASP.NET Core Web API arka uç hizmeti olan bir Azure Service Fabric Mesh uygulaması oluşturmayı öğreneceksiniz. Ardından yerel geliştirme kümenizde uygulamanın hatalarını ayıklayacaksınız. Uygulamayı Azure’da yayımlayacak ve sonra yapılandırma ve kod değişiklikleri yapıp uygulamayı yükselteceksiniz. Son olarak, kullanmadığınız kaynaklar için ücret yansıtılmaması amacıyla, kullanılmayan Azure kaynaklarını temizleyeceksiniz.

İşiniz bittiğinde, uygulama yaşam döngüsü yönetiminin birçok aşamasından geçmiş ve bir Service Fabric Mesh uygulamasıda hizmetten hizmete çağrı gösteren bir uygulama derlemiş olacaksınız.

Yapılacak işler uygulamasını el ile oluşturmak istemiyorsanız, tamamlanmış uygulamanın [kaynak kodunu indirebilir](https://github.com/azure-samples/service-fabric-mesh) ve [Yerel ortamda uygulama hatalarını ayıklama](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md) bölümüne atlayabilirsiniz.

Serinin birinci bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * ASP.NET web ön ucu içeren bir Service Fabric Mesh uygulaması oluşturmak için Visual Studio kullanın.
> * Yapılacak işleri temsil eden bir model oluşturma.
> * Bir arka uç hizmeti oluşturma ve ondan veri alma.
> * Arka uç hizmeti için Model Görünüm Denetleyicisi modelinin bir parçası olarak bir denetleyici ve DataContext ekleme.
> * Yapılacak işleri görüntülemek için bir web sayfası oluşturma.
> * Arka uç hizmetini tanımlayan ortam değişkenlerini oluşturma

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * Visual Studio’da Service Fabric Mesh uygulaması oluşturma
> * [Yerel geliştirme kümenizde çalışan bir Service Fabric Mesh uygulamasının hatalarını ayıklama](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Service Fabric Mesh uygulaması dağıtma](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Service Fabric Mesh uygulamasını yükseltme](service-fabric-mesh-tutorial-upgrade.md)
> * [Service Fabric Mesh kaynaklarını temizleme](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturabilirsiniz](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

* [Geliştirme ortamınızı ayarladığınızdan](service-fabric-mesh-howto-setup-developer-environment-sdk.md) ve Service Fabric çalışma zamanı, SDK, Docker ve Visual Studio 2017'yi yüklediğinizden emin olun.

## <a name="create-a-service-fabric-mesh-project-in-visual-studio"></a>Visual Studio’da Service Fabric Mesh projesi oluşturma

Visual Studio 'yu çalıştırın ve **Dosya**  >  **Yeni**  >  **proje..** . öğesini seçin.

**Yeni Proje** iletişim kutusunun en üstündeki **Ara** kutusuna `mesh` yazın. **Service Fabric Mesh Uygulaması** şablonunu seçin. (Bu şablonu görmüyorsanız [geliştirme ortamınızı ayarlama](service-fabric-mesh-howto-setup-developer-environment-sdk.md) bölümünde anlatılan şekilde Mesh SDK'sını ve VS araçları önizleme sürümünü yüklediğinizden emin olun.)  

**Ad** kutusuna `todolistapp` yazın ve **Konum** kutusunda projenin dosyalarını depolamak istediğiniz klasörü belirtin.

**Çözüm için dizin oluştur** kutusunun işaretli olduğundan emin olun ve **Tamam**'a tıklayarak Service Fabric Mesh projesini oluşturun.

![Service Fabric kafes projesi oluşturmayı gösteren ekran görüntüsü.](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-project.png)

Ardından **Yeni Service Fabric Hizmeti** iletişim kutusu açılır.

### <a name="create-the-web-front-end-service"></a>Web ön uç hizmetini oluşturma

**Yeni Service Fabric Hizmeti** iletişim kutusunda **ASP.NET Core** proje türünü seçin ve **Kapsayıcı işletim sistemi** için **Windows**'un seçildiğinden emin olun.

**Hizmet Adı**'nı **WebFrontEnd** yapın. ASP.NET Core hizmetini oluşturmak için **Tamam**'a basın.

![Visual Studio yeni Service Fabric Mesh projesi iletişim kutusu](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-service-fabric-service.png)

Daha sonra ASP.NET Core Web uygulaması iletişim kutusunu görürsünüz. **Web Uygulaması**’nı seçin ve **Tamam**’a tıklayın.

![Web Appliccation şablonunu vurgulayan ekran görüntüsü.](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-aspnetcore-app.png)

Artık bir Service Fabric Mesh uygulamanız var. Şimdi yapılacak işler bilgilerini içerecek modeli oluşturun.

## <a name="create-the-to-do-items-model"></a>Yapılacak işler öğelerini içeren modeli oluşturma

Kolaylık olması için yapılacak işler bellekte bir liste halinde depolanır. Yapılacak işler için bir sınıf kitaplığı ve onları barındıracak bir liste oluşturun. Visual Studio 'da, şu anda **todolistapp** çözümü yüklü olan **Dosya**  >    >  **Yeni proje** Ekle ' yi seçin.

Üstteki **Yeni Proje Ekle** **iletişim kutusunda** , yazın `C# .net core class` . **Sınıf Kitaplığı (.NET Core)** şablonunu seçin.

**Ad** kutusuna `Model` yazın. Sınıf kitaplığını oluşturmak için **Tamam**'a tıklayın.

Çözüm gezgininin **Model** bölümünde **Class1.cs** öğesine sağ tıklayıp **Yeniden adlandır**'ı seçin. Sınıfı **ToDoItem.cs** olarak yeniden adlandırın. Tüm başvuruların yeniden adlandırılacağını soran bir istem göründüğünde **Evet**' e tıklayın.

Boş `class ToDoItem` içeriğini şununla değiştirin:

```csharp
public class ToDoItem
{
    public string Description { get; set; }
    public int Index { get; set; }
    public bool Completed { get; set; }

    public ToDoItem(string description)
    {
        Description = description;
        Index = 0;
    }

    public static ToDoItem Load(string description, int index, bool completed)
    {
        ToDoItem newItem = new ToDoItem(description)
        {
            Index = index,
            Completed = completed
        };

        return newItem;
    }
}
```

Bu sınıf yapılacak-do öğelerini temsil eder.

Visual Studio'da **Model** sınıf kitaplığına sağ tıklayın ve **Ekle** > **Sınıf...** yolunu izleyerek yapılacak işler öğelerini barındıracak bir liste oluşturun. **Yeni Öğe Ekle** iletişim kutusu açılır. **Ad** için `ToDoList.cs` yazın ve **Ekle**'ye tıklayın.

**ToDoList.cs** içinde boş `class ToDoList` öğesini şununla değiştirin:

```csharp
public class ToDoList
{
    private List<ToDoItem> _items;

    public string Name { get; set; }
    public IEnumerable<ToDoItem> Items { get => _items; }

    public ToDoList(string name)
    {
        Name = name;
        _items = new List<ToDoItem>();
    }

    public ToDoItem Add(string description)
    {
        var item = new ToDoItem(description);
        _items.Add(item);
        item.Index = _items.IndexOf(item);
        return item;
    }
    public void Add(ToDoItem item)
    {
        _items.Add(item);
        item.Index = _items.Count - 1;
    }

    public ToDoItem RemoveAt(int index)
    {
        if (index >= 0 && index < _items.Count)
        {
            var result = _items[index];
            _items.RemoveAt(index);

            // Reorder items
            for (int i = index; i < _items.Count; i++)
            {
                _items[i].Index = i;
            }

            return result;
        }
        else
        {
            throw new IndexOutOfRangeException();
        }
    }
}
```

Ardından yapılacak işler öğelerini takip edecek Service Fabric hizmetini oluşturun.

## <a name="create-the-back-end-service"></a>Arka uç sunucusunu oluşturma

Visual Studio'nun **Çözüm Gezgini** penceresinde **todolistapp** öğesine sağ tıklayın ve **Ekle** > **Yeni Service Fabric Hizmeti...** öğesini seçin.

**Yeni Service Fabric Hizmeti** iletişim kutusu açılır. **ASP.NET Core** proje türünü seçin ve **Kapsayıcı işletim sistemi** ayarının **Windows** olduğundan emin olun. **Hizmet Adı** olarak **ToDoService** yazın. ASP.NET Core hizmetini oluşturmak için **Tamam**'a tıklayın.

Ardından **Yeni ASP.NET Core Web Uygulaması** iletişim kutusu açılır. Bu iletişim kutusunda **API**'yi ve ardından **Tamam**'ı seçin. Çözüme hizmet için bir proje eklenir.

![Visual Studio yeni ASP.NET Core uygulaması](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-webapi.png)

Arka uç hizmeti arabirim sağlamadığından hizmet başlatıldığında tarayıcının çalıştırılması ayarını kapatın. **Çözüm Gezgini**'nde **ToDoService** öğesine sağ tıklayıp **Özellikler**'i seçin. Açılan özellikler penceresinin sol tarafındaki **Hata ayıklama** sekmesini seçin ve **Tarayıcıyı başlat** kutusunun seçimini kaldırın. Değişikliği kaydetmek için **Ctrl+S** tuşlarına basın.

Bu hizmet yapılacak işler bilgilerini tuttuğundan Model sınıf kitaplığına başvuru ekleyin. Çözüm Gezgini, **ToDoService** öğesine sağ tıklayın ve ardından başvuru **Ekle**  >  **...** öğesini seçin. **Başvuru Yöneticisi** iletişim kutusu görüntülenir.

**Başvuru Yöneticisi**'nde **Model** onay kutusunu seçin ve **Tamam**'a tıklayın.

### <a name="add-a-data-context&quot;></a>Veri bağlamı ekleme

Ardından veri modelinden veri sunumunu gerçekleştiren bir veri bağlamı oluşturun.

Veri bağlamı sınıfı eklemek için çözüm gezgininde **ToDoService** öğesine sağ tıklayıp **Ekle** > **Sınıf** yolunu izleyin.
Açılan **Yeni Öğe Ekle** iletişim kutusunda **Sınıf** öğesinin seçili olduğundan emin olun ve **Ad** için `DataContext.cs` yazıp **Ekle**'ye tıklayın.

**DataContext.cs** içinde boş `class DataContext` içeriğini şununla değiştirin:

```csharp
public static class DataContext
{
    public static Model.ToDoList ToDoList { get; } = new Model.ToDoList(&quot;Azure learning List");

    static DataContext()
    {
        // Seed to-do list
        ToDoList.Add(Model.ToDoItem.Load("Learn about microservices", 0, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric", 1, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric Mesh", 2, false));
    }
}
```

Bu minimal veri bağlamı, örnek yapılacak işler öğeleri ekler ve bunlara erişim sağlar.

### <a name="add-a-controller"></a>Denetleyici ekleme

HTTP isteklerini işleyen ve HTTP yanıtını oluşturan varsayılan denetleyici, **ToDoService** projesi oluşturulduğunda şablon tarafından sağlanmıştır. **Çözüm Gezgini**'nin **ToDoService** bölümünde **Denetleyiciler** klasörünü açarak **ValuesController.cs** dosyasına ulaşın. 

**ValuesController.cs** dosyasına sağ tıklayıp **Yeniden adlandır**'ı seçin. Dosyayı `ToDoController.cs` olarak yeniden adlandırın. Tüm başvuruları yeniden adlandırmak için bir istem görünürse **Evet**'e tıklayın.

**ToDoController.cs** dosyasını açın ve `class ToDoController` içeriğini şununla değiştirin:

```csharp
[Route("api/[controller]")]
public class ToDoController : Controller
{
    // GET api/todo
    [HttpGet]
    public IEnumerable<Model.ToDoItem> Get()
    {
        return DataContext.ToDoList.Items;
    }

    // GET api/todo/5
    [HttpGet("{index}")]
    public Model.ToDoItem Get(int index)
    {
        return DataContext.ToDoList.Items.ElementAt(index);
    }

    //// POST api/values
    //[HttpPost]
    //public void Post([FromBody]string value)
    //{
    //}

    //// PUT api/values/5
    //[HttpPut("{id}")]
    //public void Put(int id, [FromBody]string value)
    //{
    //}

    // DELETE api/values/5
    [HttpDelete("{index}")]
    public void Delete(int index)
    {
    }
}
```

Odağı başka bir hizmetle iletişim kurma üzerinde tutmak için bu öğreticide ekleme, silme ve diğer işlemler kullanılmamıştır.

## <a name="create-the-web-page-that-displays-to-do-items"></a>Yapılacak işler öğelerini görüntüleyecek web sayfasını oluşturma

Arka uç hizmetini uyguladıktan sonra yapılacak işler öğelerini gösterecek web sayfasını kodlayın. Aşağıdaki adımlar **WebFrontEnd** projesinde gerçekleştirilecektir.

Yapılacak işler öğelerini görüntüleyen web sayfasının **ToDoItem** sınıfına ve listesine erişmesi gerekir.
**Çözüm Gezgini**, **Web ön** ucu ' na sağ tıklayıp başvuru **Ekle**  >  **...** öğesini seçerek model projesine bir başvuru ekleyin. **Başvuru Yöneticisi** iletişim kutusu görüntülenir.

**Başvuru Yöneticisi**'nde **Model** onay kutusuna ve **Tamam**'a tıklayın.

**Çözüm Gezgini**'nde **WebFrontEnd** > **Sayfalar** > **Index.cshtml** yolunu izleyerek Index.cshtml sayfasını açın. **Index.cshtml** sayfasını açın.

Dosyanın içeriğinin tamamını aşağıdaki yapılacak işler öğelerini görüntüleyen basit bir tabloyu tanımlayan HTML koduyla değiştirin:

```HTML
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

**Çözüm Gezgini** **index. cshtml** dosyasının açılır simgesine tıklayın ve ardından **index. cshtml. cs** dosyasını açın.

**Index.cshtml.cs** dosyasının en üstüne `using System.Net.Http;` ifadesini ekleyin.

`public class IndexModel` içeriğini şununla değiştirin:

```csharp
public class IndexModel : PageModel
{
    public Model.ToDoItem[] Items = new Model.ToDoItem[] { };

    public void OnGet()
    {
        HttpClient client = new HttpClient();

        using (HttpResponseMessage response = client.GetAsync(backendUrl).GetAwaiter().GetResult())
        {
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                Items = Newtonsoft.Json.JsonConvert.DeserializeObject<Model.ToDoItem[]>(response.Content.ReadAsStringAsync().Result);
            }
        }
    }

    private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
    private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
}
```

### <a name="create-environment-variables"></a>Ortam değişkenlerini oluşturma

Arka uç hizmetiyle iletişim kurmak için URL'sini kullanmanız gerekir. Bu öğreticinin amaçları doğrultusunda aşağıdaki kod parçası (IndexModel öğesinin bir parçası olarak yukarıda tanımlanan) ortam değişkenlerini okuyarak URL'yi oluşturur:

```csharp
private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
```

URL, hizmet adından ve bağlantı noktasından oluşur. Bu bilgilerin tümü **ToDoService** projesindeki service.yaml dosyasında yer alır.

> [!IMPORTANT]
> Aşağıdaki adımlarda YAML dosyaları değiştirilecek.
> service.yaml dosyasındaki değişkenleri girintilemek için sekme değil boşluk kullanılması gerekir, aksi halde dosya derlenmez. Ortam değişkeni eklediğinizde Visual Studio sekme ekleyebilir. Tüm sekmeleri boşluklarla değiştirin. **Derleme** hata ayıklama çıktısında hata görebileceksiniz, ancak uygulama yine de çalışmaya devam eder ancak sekmeleri boşluklara ve yeniden derlemeden dönüştürene kadar olmaz. Service. YAML dosyasında herhangi bir sekme bulunmadığından emin olmak için, Visual Studio Düzenleyicisi 'nde,    >  **Gelişmiş**   >  **Görünüm alanı** Düzenle ' yi kullanarak boşlukların görünmesini sağlayabilirsiniz.
> service.yaml dosyalarının İngilizce dil ayarı kullanılarak işlendiğini unutmayın. Ondalık ayırıcısı kullanmanız gerekiyorsa, örneğin virgül yerine bir nokta kullanın.

**Çözüm Gezgini**'nde **ToDoService** projesine gidin ve **Hizmet Kaynakları** > **service.yaml** dosyasını açın.

![Şekil 1 - ToDoService service.yaml dosyası](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-port.png)

 Hizmet adı, `ToDoService` `services:` Yukarıdaki şekilde (1) bakın bölümünde bulunur.

* Bağlantı noktası, `80` `endpoints:` Yukarıdaki şekilde (2) bölümünde bulunur. Projenizin bağlantı noktası numarası muhtemelen farklı olacaktır.

Daha sonra, Web ön uç projesinde hizmet adını ve bağlantı noktası numarasını temsil eden ortam değişkenlerini tanımlamamız gerekir, bu nedenle arka uç hizmetini çağırabilir.

**Çözüm Gezgini**'nde **WebFrontEnd** > **Hizmet Kaynakları** > **service.yaml** dosyasına gideren arka uç hizmeti adresini belirten değişkenleri tanımlayın.

Service. YAML dosyasında, aşağıdaki değişkenleri altına ekleyin `environmentVariables:` (Bu adı ortadan kaldırmak için ilk olarak kaldırmanız gerekir), daha sonra `#` `environmentVariables:` eklediğiniz değişkenleri altındaki diğer değişkenlerle hizalayın `environmentVariables:` . ApiHostPort değeri, ToDoService 'ın Service. YAML dosyasında daha önce görülmüş olan ToDoServiceListener için bağlantı noktası değeriyle eşleşir.

```yaml
- name: ApiHostPort
  value: 
- name: ToDoServiceName
  value: ToDoService
```

> [!Tip]
> İçin değer belirtmek için iki yol vardır `ToDoServiceName` : 
> - Yalnızca Windows 10 ' da hata ayıklama senaryosunda ve hizmeti Azure Service Fabric ağı 'na dağıtmanın yanı sıra hizmet adı.
> - Tam olarak ServiceName. AppName olarak nitelenir. Bu, yalnızca Windows 10 ' da hata ayıklarken çalışır.
> Hizmet çözümlemesi için yalnızca hizmet adını kullanmak iyi bir uygulamadır.

**WebFrontEnd** projenizin **service.yaml** dosyasının aşağıdakine benzer olması gerekir ancak `ApiHostPort` değeriniz muhtemelen farklı olacaktır:

![WebFrontEnd projesindeki Service.yaml dosyası](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-envvars.png)


Artık Service Fabric Mesh uygulaması görüntüsünü arka uç web hizmetiyle birlikte yerel kümenizde derlemek ve dağıtmak için hazırsınız.

## <a name="next-steps"></a>Sonraki adımlar

Öğreticinin bu bölümünde, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * ASP.NET web ön ucu içeren bir Service Fabric Mesh uygulaması oluşturun.
> * Yapılacak işleri temsil eden bir model oluşturma.
> * Bir arka uç hizmeti oluşturma ve ondan veri alma.
> * Arka uç hizmeti için Model Görünüm Denetleyicisi modelinin bir parçası olarak bir denetleyici ve DataContext ekleme.
> * Yapılacak işleri görüntülemek için bir web sayfası oluşturma.
> * Arka uç hizmetini tanımlayan ortam değişkenlerini oluşturma

Sonraki öğreticiye ilerleyin:
> [!div class="nextstepaction"]
> [Yerel geliştirme kümenizde çalışan bir Service Fabric Mesh uygulamasının hatalarını ayıklama](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)