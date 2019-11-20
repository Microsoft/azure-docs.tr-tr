---
title: Azure Windows Communication Foundation (WCF) geçiş karma şirket içi/bulut uygulaması (.NET) | Microsoft Docs
description: Azure Relay kullanarak, şirket içi WCF hizmetini bulutta bir Web uygulamasına nasıl kullanıma sunabileceğiniz hakkında bilgi edinin
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: b86d535e4cbc275b3ee777d7c70146f7711c502c
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212929"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Azure Relay kullanarak bir şirket içi WCF hizmetini bulutta bir Web uygulamasına sunun

Bu makale, Microsoft Azure ve Visual Studio ile nasıl karma bulut uygulaması derleyeceğinizi gösterir. Bulutta birden çok Azure kaynağı kullanan bir uygulama oluşturursunuz. Bu öğretici şunları öğrenmenize yardımcı olur:

* Bir web çözümünde kullanılması amacıyla web hizmeti oluşturma veya var olan bir web hizmetini uyarlama.
* Azure uygulaması ve başka bir yerde barındırılan bir Web hizmeti arasında veri paylaşmak için Azure Windows Communication Foundation (WCF) geçiş hizmetini kullanma.

Bu öğreticide aşağıdaki görevleri gerçekleştirebilirsiniz:

> [!div class="checklist"]
>
> * Bu öğretici için önkoşulları yükler.
> * Senaryoyu gözden geçirin.
> * Ad alanı oluşturun.
> * Şirket içi sunucu oluşturun.
> * Bir ASP .NET uygulaması oluşturun.
> * Uygulamayı yerel olarak çalıştırın.
> * Web uygulamasını Azure 'a dağıtın.
> * Uygulamayı Azure 'da çalıştırın.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdaki önkoşulları karşılamanız gerekir:

* Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).
* [Visual Studio 2015 veya üzeri](https://www.visualstudio.com). Bu öğreticideki örneklerde Visual Studio 2019 kullanılır.
* .NET için Azure SDK. [SDK İndirmeleri sayfasından](https://azure.microsoft.com/downloads/)bu uygulamayı yükler.

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Azure Relay geçişinin karma çözümlere yönelik yardımları

İş çözümleri genellikle özel kod ve mevcut işlevselliğin birleşiminden oluşur. Özel kod tackles yeni ve benzersiz iş gereksinimleri. Zaten var olan çözümler ve sistemler mevcut işlevlere sahiptir.

Çözüm mimarları, ölçek gereksinimlerini daha kolay bir şekilde karşılamak ve işlem maliyetlerini düşürmek için bulutu kullanmaya başlıyor. Bunu yaparken, çözümleri için yapı taşları olarak kullanmak istedikleri mevcut hizmet varlıklarının kurumsal güvenlik duvarının içinde ve bulut çözümü tarafından kolayca erişime sahip olduğunu bulmaktadır. Birçok iç hizmet, kurumsal ağ kenarından kolayca sunulanbilecekleri şekilde derlenmez veya barındırılmamaktadır.

[Azure Relay](https://azure.microsoft.com/services/service-bus/) , mevcut WCF Web hizmetlerini alır ve kurumsal ağ altyapısına müdahale eden değişiklikler yapmanıza gerek kalmadan kurumsal çevre dışındaki çözümlere güvenli bir şekilde erişilebilir hale getirir. Bu tür geçişi hizmetleri, var olan ortamlarında barındırılmaya devam eder ancak bu hizmetler gelen oturumları ve istekleri bulutta barındırılan geçiş hizmetine devreder. Ayrıca, Azure Geçişi [Paylaşılan Erişim İmzası (SAS)](../service-bus-messaging/service-bus-sas.md) kimlik doğrulaması kullanarak bu hizmetleri yetkilendirilmemiş erişime karşı korur.

## <a name="review-the-scenario"></a>Senaryoyu gözden geçirin

Bu öğreticide, ürün envanteri sayfasında ürünlerin listesini görmenizi sağlayan bir ASP.NET Web sitesi oluşturacaksınız.

![Senaryo][0]

Öğretici, var olan şirket içi sistemde ürün bilgilerine sahip olduğunuzu varsayar ve bu sisteme erişmek için Azure Geçişini kullanır. Basit konsol uygulamasında çalışan bir Web hizmeti bu durumun benzetimini yapar. Bu, bellek içi bir ürün kümesi içerir. Bu konsol uygulamasını kendi bilgisayarınızda çalıştırabilir ve Web rolünü Azure 'a dağıtabilirsiniz. Bunu yaptığınızda, Azure veri merkezinde çalışan Web rolünün bilgisayarınıza nasıl çağrı yaptığını görürsünüz. Bu çağrı, bilgisayarınız en az bir güvenlik duvarı ve bir ağ adresi çevirisi (NAT) katmanının neredeyse tamamen arkasında olacaktır.

## <a name="set-up-the-development-environment"></a>Geliştirme ortamını ayarlama

Azure uygulamalarını geliştirmeye başlamadan önce, araçları indirip geliştirme ortamınızı ayarlayın:

1. SDK [indirme sayfasından](https://azure.microsoft.com/downloads/) .NET için Azure SDK'sını yükleyin.
1. **.Net** sütununda, kullanmakta olduğunuz [Visual Studio](https://www.visualstudio.com) sürümünü seçin. Bu öğretici, Visual Studio 2019 kullanır.
1. Yükleyiciyi çalıştırmanız veya kaydetmeniz istendiğinde **Çalıştır**' ı seçin.
1. **Web Platformu Yükleyicisi** iletişim **kutusunda yükleme ' yi seçin ve** yüklemeye devam edin.

Yükleme tamamlandıktan sonra, uygulamayı geliştirmeye başlamak için gereken her şey vardır. SDK, Visual Studio'da Azure uygulamalarını kolayca geliştirmenize olanak sağlayan araçları içerir.

## <a name="create-a-namespace"></a>Ad alanı oluşturma

İlk adım, bir ad alanı oluşturmak ve [paylaşılan erişim imzası (SAS)](../service-bus-messaging/service-bus-sas.md) anahtarı elde etmek için kullanılır. Ad alanı, geçiş hizmeti aracılığıyla kullanıma sunulan her uygulama için bir uygulama sınırı sağlar. Bir hizmet ad alanı oluşturulduğunda sistem tarafından bir SAS anahtarı otomatik olarak oluşturulur. Hizmet ad alanı ve SAS anahtarı birleşimi, bir uygulamaya erişimin kimliğini doğrulamak için Azure kimlik bilgilerini sağlar.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Şirket içi sunucu oluşturma

İlk olarak, benzetimli bir şirket içi ürün kataloğu sistemi oluşturursunuz.  Bu proje bir Visual Studio konsol uygulamasıdır ve Service Bus kitaplıkları ile yapılandırma ayarlarını dahil etmek için [Azure Service Bus NuGet paketini](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) kullanır. <a name="create-the-project"></a>

1. Microsoft Visual Studio yönetici olarak başlatın. Bunu yapmak için, Visual Studio program simgesine sağ tıklayın ve **yönetici olarak çalıştır**' ı seçin.
1. Visual Studio 'da **Yeni proje oluştur**' u seçin.
1. **Yeni proje oluştur**' da, Için C# konsol uygulaması (.NET Framework) öğesini seçin ve **İleri**' yi seçin.
1. Projeyi *Productsserver* olarak adlandırın ve **Oluştur**' u seçin.

   ![Yeni projenizi yapılandırın][11]

1. **Çözüm Gezgini**, **productsserver** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.
1. **Araştır**' ı seçin, sonra **windowsazure. ServiceBus**öğesini arayıp seçin. **Yükler**' i seçin ve kullanım koşullarını kabul edin.

   ![NuGet paketini Seç][13]

   Gerekli istemci derlemelerine artık başvuruluyor.

1. Ürün sözleşmeniz için yeni bir sınıf ekleyin. **Çözüm Gezgini**, **productsserver** projesine sağ tıklayın ve**sınıf** **Ekle** > ' yi seçin.
1. **Ad**alanına *ProductsContract.cs* adını girin ve **Ekle**' yi seçin.

Çözümünüzde aşağıdaki kod değişikliklerini yapın:

1. *ProductsContract.cs* sınıfında, ad alanı tanımını hizmet sözleşmesini tanımlayan şu kod ile değiştirin:

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. *Program.cs*' de, ad alanı tanımını aşağıdaki kodla değiştirin, bu, profil hizmetini ve ana bilgisayarı ekler.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. **Çözüm Gezgini**' de, **app. config** dosyasına çift tıklayarak dosyayı Visual Studio düzenleyicisinde açın. `<system.ServiceModel>` Öğesinin alt tarafında, ancak hala içinde `<system.ServiceModel>`, aşağıdaki XML kodunu ekleyin. ' İ ad alanınız `yourServiceNamespace` adıyla ve `yourKey` daha önce portaldan aldığınız SAS anahtarı ile değiştirdiğinizden emin olun:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > Hatanın nedeni `transportClientEndpointBehavior` yalnızca bir uyarıdır ve bu örnek için engelleyici bir sorun değildir.

1. Hala *app. config*içinde, `<appSettings>` öğesinde, bağlantı dizesi değerini portaldan daha önce edindiğiniz bağlantı dizesiyle değiştirin.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Uygulamayı derlemek ve şu ana kadarki doğruluğu doğrulamak için CTRL + SHIFT + B ' **yi seçin veya** > **yapı oluşturma çözümünü** seçin.

## <a name="create-an-aspnet-application"></a>ASP.NET uygulaması oluşturma

Bu bölümde, ürün hizmetinizden alınan verileri görüntüleyen basit bir ASP.NET uygulaması oluşturacaksınız.

### <a name="create-the-project"></a>Proje oluşturma

1. Visual Studio 'Nun yönetici olarak çalıştığından emin olun.
1. Visual Studio 'da **Yeni proje oluştur**' u seçin.
1. **Yeni proje oluştur**' da, Için C# ASP.NET Web uygulaması (.NET Framework) öğesini seçin ve **İleri**' yi seçin.
1. Projeyi *Productsportal* olarak adlandırın ve **Oluştur**' u seçin.
1. **Yeni bir ASP.NET Web uygulaması oluşturma**bölümünde **MVC** ' yi seçin ve **kimlik doğrulaması**altında **Değiştir** ' i seçin.

   ![ASP .NET Web uygulaması Seç][16]

1. **Kimlik doğrulamasını Değiştir**' de **kimlik doğrulaması yok** ' u ve **Tamam**' ı seçin Bu öğreticide, kullanıcının oturum açmasını gerektirmeyen bir uygulamayı dağıtacağız.

    ![Kimlik doğrulaması belirtin][18]

1. **Yeni bir ASP.NET Web uygulaması oluşturun**, MVC uygulamasını oluşturmak için **Oluştur** ' u seçin.
1. Yeni bir Web uygulaması için Azure kaynaklarını yapılandırın. [Web uygulamanızı yayımlama](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard)bölümündeki adımları izleyin. Ardından, Bu öğreticiye dönüp bir sonraki adımla devam edin.
1. **Çözüm Gezgini**, **modeller** ' a sağ tıklayın ve ardından**sınıf** **Ekle** > ' yi seçin.
1. Sınıfı *Product.cs*olarak adlandırın, sonra **Ekle**' yi seçin.

    ![Ürün modeli oluştur][17]

### <a name="modify-the-web-application"></a>Web uygulamasını değiştirme

1. Visual Studio 'daki *Product.cs* dosyasında, var olan ad alanı tanımını şu kodla değiştirin:

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. **Çözüm Gezgini**, **denetleyiciler**' i genişletin ve sonra **HomeController.cs** ' i çift tıklatarak dosyayı Visual Studio 'da açın.
1. *HomeController.cs*' de, var olan ad alanı tanımını şu kodla değiştirin:

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. **Çözüm Gezgini**' de,**paylaşılan** **Görünümler** > ' i genişletin ve ardından dosyayı Visual Studio düzenleyicisinde açmak için **_Layout. cshtml** öğesine çift tıklayın.
1. Tüm yinelemelerini `My ASP.NET Application` *Northwind Traders ürünlerine*değiştirin.
1. `Home` ,`About`Ve bağlantılarını`Contact` kaldırın. Aşağıdaki örnekte vurgulanmış kodu silin.

    ![Oluşturulan liste öğelerini Sil][41]

1. **Çözüm Gezgini**' de, **Görünümler** > **giriş**' i genişletin ve **Index. cshtml** dosyasına çift tıklayarak dosyayı Visual Studio düzenleyicisinde açın. Dosyanın tüm içeriğini aşağıdaki kodla değiştirin:

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. Çalışmanızın şu ana kadarki doğruluğunu doğrulamak için, projeyi derlemek için CTRL + SHIFT + B ' yi seçebilirsiniz.

### <a name="run-the-app-locally"></a>Uygulamayı yerel olarak çalıştırma

Çalışır durumda olduğunu doğrulamak için uygulamayı çalıştırın.

1. **ProductsPortal** projesinin, etkin olduğundan emin olun. **Çözüm Gezgini** ' de proje adına sağ tıklayın ve **Başlangıç projesi olarak ayarla**' yı seçin.
1. Visual Studio 'da F5 ' i seçin.

Uygulamanız, bir tarayıcıda çalışıyor olarak görüntülenmelidir.

![Web uygulaması][21]

## <a name="put-the-pieces-together"></a>Parçaları bir araya getirme

Sonraki adım, şirket içi ürünlerin sunucusu ile ASP.NET uygulamasını birleştirmektir.

1. Henüz açık değilse, Visual Studio 'da [ASP.NET uygulaması oluşturma](#create-an-aspnet-application) bölümünde oluşturduğunuz **productsportal** projesini açın.
1. [Şirket içi sunucu oluşturma](#create-an-on-premises-server) bölümünde yer alan adıma benzer şekilde, proje başvurularına NuGet paketini ekleyin. **Çözüm Gezgini**, **productsportal** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.
1. *WindowsAzure.ServiceBus* araması yapın ve **WindowsAzure.ServiceBus** öğesini seçin. Ardından yüklemeyi ve bu iletişim kutusunu kapatın.
1. **Çözüm Gezgini**, **productsportal** projesine sağ tıklayın ve ardından**Varolan öğe** **Ekle** > ' yi seçin.
1. **ProductsServer** konsol projesinden *ProductsContract.cs* dosyasına gidin. *ProductsContract.cs*vurgulayın. **Ekle**' nin yanındaki aşağı oku seçin, sonra **bağlantı olarak ekle**' yi seçin.

   ![Bağlantı olarak ekle][24]

1. Şimdi, Visual Studio düzenleyicisinde *HomeController.cs* dosyasını açın ve ad alanı tanımını aşağıdaki kodla değiştirin. Öğesini hizmet ad alanınız `yourServiceNamespace` ve `yourKey` SAS anahtarınızla değiştirdiğinizden emin olun. Bu kod, istemcinin şirket içi hizmeti aramasını sağlar ve çağrının sonucunu döndürür.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. **Çözüm Gezgini**, **productsportal** çözümüne sağ tıklayın. Projeye değil, çözüme sağ tıkladığınızdan emin olun. **Varolan proje** **Ekle** > ' yi seçin.
1. **ProductsServer** projesine gidip *ProductsServer.csproj* çözümüne çift tıklayarak ekleyin.
1. **Productsportal**üzerinde verileri göstermek Için **productsserver** çalışıyor olmalıdır. **Çözüm Gezgini**, **productsportal** çözümüne sağ tıklayın ve **Özellikler** ' i seçerek **özellik sayfalarını**görüntüleyin.
1. **Ortak özellikler** > **Başlangıç projesi** ' ni seçin ve **birden çok başlangıç**projesi seçin. **Productsserver** ve **productsportal** 'in bu sırada göründüğünden ve her Ikisi için de **eylemin** **başlamadiğinden**emin olun.

      ![Birden çok başlangıç projesi][25]

1. Sol taraftaki **ortak özellikler** > **Proje bağımlılıklarını** seçin.
1. **Projeler**Için **productsportal**' i seçin. **ProductsServer** projesinin seçili olduğundan emin olun.

    ![Proje bağımlılıkları][26]

1. **Projeler**Için **productsserver**' ı seçin. **Productsportal** ' in seçili olmadığından emin olun ve ardından değişikliklerinizi kaydetmek için **Tamam** ' ı seçin.

## <a name="run-the-project-locally"></a>Projeyi yerel olarak çalıştırma

Uygulamayı yerel olarak test etmek için, Visual Studio 'da F5 ' i seçin. Şirket içi sunucu, **productsserver**ilk kez başlamalıdır, sonra **productsportal** uygulaması bir tarayıcı penceresinde başlamalıdır. Bu kez, ürün envanterinde ürün hizmeti şirket içi sisteminden alınan verileri listeleyen görürsünüz.

![Web uygulaması][10]

**Productsportal** sayfasında **Yenile** ' yi seçin. Sayfayı her yenilediğiniz sırada, `GetProducts()` **productsserver** çağrıldığında sunucu uygulamasının bir ileti görüntülemesini görürsünüz.

Sonraki bölüme geçmeden önce her iki uygulamayı da kapatın.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>ProductsPortal projesini bir Azure web uygulamasına dağıtma

Sonraki adım Azure Web App **Productsportal** ön ucunun yeniden yayımlamaktır:

1. **Çözüm Gezgini**, **productsportal** projesine sağ tıklayın ve **Yayımla**' yı seçin. **Yayımla** sayfasında **Yayımla**' yı seçin.

   > [!NOTE]
   > Dağıtımdan sonra **ProductsPortal** web projesinin otomatik olarak başlatılması durumunda tarayıcıda bir hata iletisi görüntülenebilir. **ProductsServer** uygulaması henüz çalışmadığından bu durumun meydana gelmesi olasıdır.
   >

1. Dağıtılan Web uygulamasının URL 'sini kopyalayın. URL 'ye daha sonra ihtiyacınız olacak. Ayrıca, bu URL 'YI Visual Studio 'daki **Azure App Service etkinlik** penceresinden de edinebilirsiniz:

   ![Dağıtılan uygulamanın URL 'SI][9]

1. Çalışan uygulamayı durdurmak için tarayıcı penceresini kapatın.

<a name="set-productsportal-as-web-app"></a>Uygulamayı bulutta çalıştırmadan önce, **Productsportal** 'In Visual Studio içinden bir Web uygulaması olarak başlatıldığından emin olmanız gerekir.

1. Visual Studio 'da **Productsportal** projesine sağ tıklayın ve **Özellikler**' i seçin.
1. **Web**'i seçin. **Başlatma eylemi**altında **URL 'yi Başlat**' ı seçin. Bu örnekte, `https://productsportal20190906122808.azurewebsites.net/`önceden dağıtılmış Web uygulamanızın URL 'sini girin.

    ![Başlangıç URL 'SI][27]

1. **Dosya** > **Tümünü Kaydet**' i seçin.
1. **Derleme** > **yeniden oluşturma çözümünü**seçin.

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı derlemek ve çalıştırmak için F5 ' i seçin. **Productsserver** konsol uygulaması olan şirket içi sunucu ilk kez başlamalıdır, ardından aşağıda gösterildiği gibi **productsportal** uygulaması bir tarayıcı penceresinde başlamalıdır:

   ![Web uygulamasını Azure 'da çalıştırma][1]

Ürün envanteri, ürün hizmeti şirket içi sisteminden alınan verileri listeler ve bu verileri Web uygulamasında görüntüler. **ProductsPortal**'ın bir Azure web uygulaması olarak bulutta çalıştığından emin olmak için URL'yi kontrol edin.

   > [!IMPORTANT]
   > **ProductsServer** konsol uygulamasının çalışır ve **ProductsPortal** uygulamasına veri sunma işlemini gerçekleştirebilir durumda olması gerekir. Tarayıcıda bir hata görüntüleniyorsa, **Productsserver** 'ın yüklemesi ve aşağıdaki iletiyi görüntülemesi için birkaç saniye daha bekleyin, ardından Tarayıcıyı yenileyin.
   >

Tarayıcıda, **Productsportal** sayfasını yenileyin. Sayfayı her yenilediğiniz sırada, `GetProducts()` **productsserver** çağrıldığında sunucu uygulamasının bir ileti görüntülemesini görürsünüz.

![Güncelleştirilmiş çıkış][38]

## <a name="next-steps"></a>Sonraki adımlar

Şu öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Bir şirket içi WCF hizmetini ağınızın dışındaki bir WCF istemcisinin kullanımına sunma](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
