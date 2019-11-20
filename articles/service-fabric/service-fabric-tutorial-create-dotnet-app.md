---
title: Azure'da Service Fabric üzerinde bir .NET uygulaması oluşturma | Microsoft Docs
description: Bu öğreticide, ASP.NET Core ön ucuyla ve durum bilgisi olan bir güvenilir hizmet arka ucuyla uygulama oluşturmayı ve uygulamayı kümeye dağıtmayı öğrenirsiniz.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: b5acee47a13e0faa538c5d8464835297088d03e8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598922"
---
# <a name="tutorial-create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Öğretici: ASP.NET Core Web API'si ön uç hizmeti ve durum bilgisi olan bir arka uç hizmetiyle uygulama oluşturma ve dağıtma

Bu öğretici, bir dizinin birinci bölümüdür.  Verileri depolamak için ASP.NET Core Web API'si ön ucu ve durum bilgisi olan bir arka uç hizmetiyle Azure Service Fabric uygulaması oluşturmayı öğreneceksiniz. Bitirdiğinizde, oylama sonuçlarını kümedeki durum bilgisi içeren arka uç hizmetine kaydeden bir ASP.NET Core web ön ucuna sahip oylama uygulaması sağlanır. Oylama uygulamasını el ile oluşturmak istemiyorsanız, tamamlanmış uygulamanın [kaynak kodunu indirebilir](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) ve [Oylama örnek uygulamasında izlenecek yol](#walkthrough_anchor) bölümüne atlayabilirsiniz.  İsterseniz, bu öğreticiye ait bir [video kılavuzu](https://channel9.msdn.com/Events/Connect/2017/E100) da izleyebilirsiniz.

![AngularJS + ASP. NET API ön ucu, Service Fabric üzerinde durum bilgisi olan bir arka uç hizmetine bağlanma](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Serinin birinci bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * ASP.NET Core Web API'si hizmetini durum bilgisi olan güvenilir bir hizmet olarak oluşturma
> * ASP.NET Core Web Uygulaması hizmetini durum bilgisi olmayan bir web hizmeti olarak oluşturma
> * Durum bilgisi olan hizmetle iletişim kurmak için ters proxy kullanma

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * .NET Service Fabric uygulaması oluşturma
> * [Uygulamayı uzak kümeye dağıtma](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Bir ASP.NET Core ön uç hizmetine HTTPS uç noktası ekleme](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Azure Pipelines kullanarak CI/CD yapılandırma](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Uygulama için izleme ve tanılamayı ayarlama](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:
* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun
* **Azure geliştirme** ve **ASP.net ve Web geliştirme** Iş yükleriyle [Visual Studio 2019](https://www.visualstudio.com/) sürüm 15,5 veya üstünü yükledikten sonra.
* [Service Fabric SDK'yı yükleyin](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>ASP.NET Web API'si hizmetini güvenilir bir hizmet olarak oluşturma

Başlangıçta, ASP.NET Core kullanarak oylama uygulamasının web ön ucunu oluşturun. ASP.NET Core, modern web kullanıcı arabirimleri ve web API'leri oluşturmak için kullanabileceğiniz basit, platformlar arası bir web geliştirme çerçevesidir. ASP.NET Core'un Service Fabric ile nasıl tümleştirildiğini her yönüyle anlamanız için, [Service Fabric Reliable Services'te ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) makalesini okumanızı kesinlikle öneririz. Şimdilik, hızlı bir başlangıç yapmak için öğreticiyi izleyebilirsiniz. ASP.NET Core hakkında daha fazla bilgi edinmek için [ASP.NET Core Belgelerine](https://docs.microsoft.com/aspnet/core/) bakın.

1. Visual Studio'yu **yönetici** olarak başlatın.

2. **Dosya**->**Yeni**->**Proje**'yi kullanarak bir proje oluşturun.

3. **Yeni Proje** iletişim kutusunda **Bulut > Service Fabric Uygulaması**'nı seçin.

4. Uygulamaya **Voting** adını verin ve **Tamam**'a tıklayın.

   ![Visual Studio'da yeni proje iletişim kutusu](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. **Yeni Service Fabric Hizmeti** sayfasında **Durum Bilgisi Olmayan ASP.NET Core** öğesini seçin ve hizmetinize **VotingWeb** adını verip **Tamam**'a tıklayın.
   
   ![Yeni hizmet iletişim kutusunda ASP.NET web hizmetini seçme](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. Sonraki sayfada, bir dizi ASP.NET Core proje şablonu sağlanır. Bu öğretici için, **Web Uygulaması (Model-View-Controller)** seçeneğini ve **Tamam**'a tıklayın.
   
   ![ASP.NET proje türünü seçme](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio bir uygulama ve bir hizmet projesi oluşturup bunları Çözüm Gezgini'nde görüntüler.

   ![ASP.NET Core Web API'si hizmeti içeren uygulama oluşturulduktan sonra Çözüm Gezgini]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="update-the-sitejs-file"></a>Site.js dosyasını güncelleştirme
**wwwroot/js/site.js** dosyasını açın.  Dosyanın içeriğini Giriş görünümlerinde kullanılan aşağıdaki JavaScript ile değiştirin ve kaydedin.

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Index.cshtml dosyasını güncelleştirme

Giriş denetleyicisine özgü görünüm olan **Views/Home/Index.cshtml** dosyasını açın.  Dosyanın içeriğini aşağıdakilerle değiştirin ve sonra da değişikliklerinizi kaydedin.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item"/>
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr/>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-layoutcshtml-file"></a>_Layout.cshtml dosyasını güncelleştirme

ASP.NET uygulaması için varsayılan düzen olan **Views/Shared/_Layout.cshtml** dosyasını açın.  Dosyanın içeriğini aşağıdakilerle değiştirin ve sonra da değişikliklerinizi kaydedin.


```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="https://angularjs.org">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.css" rel="stylesheet"/>
    <link href="~/css/site.css" rel="stylesheet"/>

</head>
<body>
<div class="container body-content">
    @RenderBody()
</div>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.7.2/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/2.5.0/ui-bootstrap-tpls.js"></script>
<script src="~/js/site.js"></script>

@RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>VotingWeb.cs dosyasını güncelleştirme

WebListener web sunucusunu kullanarak durum bilgisi olmayan hizmetin içinde ASP.NET Core WebHost'u oluşturan *VotingWeb.cs* dosyasını açın.

Dosyanın en üstüne `using System.Net.Http;` yönergesini ekleyin.

`CreateServiceInstanceListeners()` işlevini aşağıdaki kodla değiştirin, sonra değişikliklerinizi kaydedin.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(
            serviceContext =>
                new KestrelCommunicationListener(
                    serviceContext,
                    "ServiceEndpoint",
                    (url, listener) =>
                    {
                        ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

                        return new WebHostBuilder()
                            .UseKestrel()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<HttpClient>(new HttpClient())
                                    .AddSingleton<FabricClient>(new FabricClient())
                                    .AddSingleton<StatelessServiceContext>(serviceContext))
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
                    }))
    };
}
```

Ayrıca `CreateServiceInstanceListeners()` altına aşağıdaki `GetVotingDataServiceName` metodunu ekleyin ve yaptığınız değişiklikleri kaydedin. `GetVotingDataServiceName`, yoklama yapıldığında hizmet adını döndürür.

```csharp
internal static Uri GetVotingDataServiceName(ServiceContext context)
{
    return new Uri($"{context.CodePackageActivationContext.ApplicationName}/VotingData");
}
```

### <a name="add-the-votescontrollercs-file"></a>VotesController.cs dosyasını ekleme

Oylama eylemlerini tanımlayan bir denetleyici ekleyin. **Denetleyiciler** klasörüne sağ tıklayın ve **Ekle ->Yeni öğe->Visual C#->ASP.NET Core->Sınıf**'ı seçin. Dosyaya **VotesController.cs** adını verin ve **Ekle**'ye tıklayın.  

*VotesController.cs* dosyasının içeriğini aşağıdakilerle değiştirin, sonra değişikliklerinizi kaydedin.  Daha sonra, [VotesController.cs dosyasını güncelleştirme](#updatevotecontroller_anchor) bölümünde bu dosya arka uç hizmetinde oylama verilerini okuyacak ve yazacak şekilde değiştirilir.  Şimdilik, denetleyici görünüme statik dize verileri döndürür.

```csharp
namespace VotingWeb.Controllers
{
    using System;
    using System.Collections.Generic;
    using System.Fabric;
    using System.Fabric.Query;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Newtonsoft.Json;

    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>Dinleme bağlantı noktasını yapılandırma

VotingWeb ön uç hizmeti oluşturulduğunda, Visual Studio hizmetin dinlemesi için rastgele bir bağlantı noktası seçer.  VotingWeb hizmeti bu uygulama için ön uç işlevi görür ve dış trafiği kabul eder; bu nedenle şimdi bu hizmeti sabit ve iyi tanınan bir bağlantı noktasına bağlayalım.  [Hizmet bildirimi](service-fabric-application-and-service-manifests.md) hizmet uç noktalarını bildirir.

Çözüm Gezgini'nde *VotingWeb/PackageRoot/ServiceManifest.xml* dosyasını açın.  **Kaynaklar** bölümünde **Uç Nokta** öğesini bulun ve **Bağlantı Noktası** değerini **8080** olarak değiştirin. Uygulamayı yerel olarak dağıtmak ve çalıştırmak için, uygulama dinleme bağlantı noktasının bilgisayarınızda açık ve kullanılabilir olması gerekir.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
```

Ayrıca Voting projesindeki Uygulama URL'si özellik değerini de güncelleştirin ve uygulamanızda hata ayıklaması yaptığınızda web tarayıcısının doğru bağlantı noktasına açılmasını sağlayın.  Çözüm Gezgini'nde, **Voting** projesini seçin ve **Uygulama URL'si** özelliğini **8080** olarak güncelleştirin.

### <a name="deploy-and-run-the-voting-application-locally"></a>Voting uygulamasını yerel olarak dağıtma ve çalıştırma
Artık devam edip Voting uygulamasını hata ayıklama için çalıştırabilirsiniz. Visual Studio'da **F5**'e basarak uygulamayı yerel Service Fabric kümenize hata ayıklama modunda dağıtın. Daha önce Visual Studio'yu **yönetici** olarak açmadıysanız uygulama başarısız olur.

> [!NOTE]
> Uygulamayı yerel olarak ilk kez çalıştırdığınızda ve dağıttığınızda, Visual Studio hata ayıklama için yerel bir Service Fabric kümesi oluşturur.  Küme oluşturma işlemi biraz zaman alabilir. Küme oluşturma durumu, Visual Studio çıkış penceresinde görüntülenir.

Voting uygulaması yerel Service Fabric kümenizde dağıtıldıktan sonra web uygulamanız otomatik olarak bir tarayıcı penceresinde açılır ve şuna benzer bir görünüme sahip olur:

![ASP.NET Core ön ucu](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Uygulamada hata ayıklamasını durdurmak için, Visual Studio'ya dönün ve **Shift+F5** tuşlarına basın.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Uygulamanıza durum bilgisi olan bir arka uç hizmeti ekleme

Artık uygulamada bir ASP.NET Web API'si hizmeti çalıştırıldığına göre, devam edin ve uygulamadaki bazı verileri depolamak için durum bilgisi olan güvenilir bir hizmet ekleyin.

Service Fabric, güvenilir koleksiyonlar kullanarak verileri doğrudan hizmetinizin içinden tutarlı ve güvenilir bir şekilde depolamanıza olanak tanır. Güvenilir koleksiyonlar, C# koleksiyonlarını kullanmış olan herkesin iyi tanıdığı bir dizi yüksek kullanılabilirliğe sahip ve güvenilir koleksiyon sınıfıdır.

Bu öğreticide, güvenilir koleksiyonda sayaç değerini depolayan bir hizmet oluşturursunuz.

1. Çözüm Gezgini'nde, Voting uygulaması projesinin içindeki **Hizmetler**'e sağ tıklayın ve **Ekle -> Yeni Service Fabric Hizmeti**'ni seçin.
    
2. **Yeni Service Fabric Hizmeti** iletişim kutusunda **Durum Bilgisi Olan ASP.NET Core**'u seçin, hizmete **VotingData** adını verin ve ardından **Tamam**'a basın.

    Hizmet projeniz oluşturulduktan sonra, uygulamanızda iki hizmet olacaktır. Uygulamanızı oluşturmaya devam ederken, aynı yöntemle başka hizmetler de ekleyebilirsiniz. Bunlardan her birinin bağımsız olarak sürümü oluşturulabilir ve yükseltilebilir.

3. Sonraki sayfada, bir dizi ASP.NET Core proje şablonu sağlanır. Bu öğreticide, **API** seçeneğini kullanın.

    Visual Studio VotingData hizmet projesini oluşturur ve Çözüm Gezgini'nde görüntüler.

    ![Çözüm Gezgini](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>VoteDataController.cs dosyasını ekleme

**VotingData** projesinde **Denetleyiciler**'e sağ tıklayın ve ardından **Ekle->Yeni öğe->Sınıf**'ı seçin. Dosyaya **VoteDataController.cs** adını verin ve **Ekle**'ye tıklayın. Dosyanın içeriğini aşağıdakilerle değiştirin, sonra değişikliklerinizi kaydedin.

```csharp
namespace VotingData.Controllers
{
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.ServiceFabric.Data;
    using Microsoft.ServiceFabric.Data.Collections;

    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            CancellationToken ct = new CancellationToken();

            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                Microsoft.ServiceFabric.Data.IAsyncEnumerable<KeyValuePair<string, int>> list = await votesDictionary.CreateEnumerableAsync(tx);

                Microsoft.ServiceFabric.Data.IAsyncEnumerator<KeyValuePair<string, int>> enumerator = list.GetAsyncEnumerator();

                List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return this.Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```

## <a name="connect-the-services"></a>Hizmetleri bağlama

Bu sonraki adımda, iki hizmeti bağlayın ve ön uç Web uygulamasının arka uç hizmetinden oylama bilgilerini almasını ve ayarlamasını sağlayın.

Service Fabric, güvenilir hizmetlerle iletişiminize eksiksiz bir esneklik getirir. Tek bir uygulamanın içinde, TCP üzerinden erişilebilen hizmetleriniz olabilir. Diğer hizmetlere HTTP REST API üzerinden erişilebilir ve yine web yuvaları üzerinden erişilebilen başka hizmetler olabilir. Sağlanan seçeneklerin arka planı ve mevcut alışverişler için, bkz. [Hizmetlerle iletişim kurma](service-fabric-connect-and-communicate-with-services.md).

Bu öğreticide, VotingWeb ön uç web hizmetinin arka uç VotingData hizmetiyle iletişim kurabilmesi için [ASP.NET Core Web API](service-fabric-reliable-services-communication-aspnetcore.md) ve [Service Fabric ters proxy’sini](service-fabric-reverseproxy.md) kullanın. Ters proxy varsayılan olarak 19081 numaralı bağlantı noktasını kullanacak şekilde yapılandırılmıştır ve bu öğreticide düzgün çalışacaktır. Ters proxy bağlantı noktası, kümeyi kurmak için kullanılan Azure Resource Manager şablonunda ayarlanır. Hangi bağlantı noktasının kullanıldığını bulmak için **Microsoft.ServiceFabric/clusters** kaynağındaki küme şablonuna bakın: 

```json
"nodeTypes": [
          {
            ...
            "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
            "isPrimary": true,
            "vmInstanceCount": "[parameters('nt0InstanceCount')]",
            "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]"
          }
        ],
```
Yerel geliştirme Kümenizde kullanılan ters proxy bağlantı noktasını bulmak için yerel Service Fabric kümesi bildiriminde **HttpApplicationGatewayEndpoint** öğesini görüntüleyin:
1. Bir tarayıcı penceresi açın ve Service Fabric Explorer aracını açmak için\/http:/localhost: 19080 adresine gidin.
2. **Cluster-> bildirimi**' ni seçin.
3. HttpApplicationGatewayEndpoint öğesinin bağlantı noktasını not edin. Varsayılan olarak 19081 olmalıdır. 19081 değilse VotesController.cs dosyasındaki kodun aşağıdaki bölümünde GetProxyAddress metodunun bağlantı noktasını değiştirmeniz gerekir.

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>VotesController.cs dosyasını güncelleştirme

**VotingWeb** projesinde *Controllers/VotesController.cs* dosyasını açın.  `VotesController` sınıf tanımı içeriğini aşağıdakilerle değiştirin, sonra değişikliklerinizi kaydedin. Önceki adımda bulduğunuz ters proxy 19081 değilse, GetProxyAddress metodunda kullanılan bağlantı noktası numarasını 19081 yerine bulduğunuz değer olarak değiştirin.

```csharp
public class VotesController : Controller
{
    private readonly HttpClient httpClient;
    private readonly FabricClient fabricClient;
    private readonly StatelessServiceContext serviceContext;

    public VotesController(HttpClient httpClient, StatelessServiceContext context, FabricClient fabricClient)
    {
        this.fabricClient = fabricClient;
        this.httpClient = httpClient;
        this.serviceContext = context;
    }

    // GET: api/Votes
    [HttpGet("")]
    public async Task<IActionResult> Get()
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);

        ServicePartitionList partitions = await this.fabricClient.QueryManager.GetPartitionListAsync(serviceName);

        List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

        foreach (Partition partition in partitions)
        {
            string proxyUrl =
                $"{proxyAddress}/api/VoteData?PartitionKey={((Int64RangePartitionInformation) partition.PartitionInformation).LowKey}&PartitionKind=Int64Range";

            using (HttpResponseMessage response = await this.httpClient.GetAsync(proxyUrl))
            {
                if (response.StatusCode != System.Net.HttpStatusCode.OK)
                {
                    continue;
                }

                result.AddRange(JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync()));
            }
        }

        return this.Json(result);
    }

    // PUT: api/Votes/name
    [HttpPut("{name}")]
    public async Task<IActionResult> Put(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        StringContent putContent = new StringContent($"{{ 'name' : '{name}' }}", Encoding.UTF8, "application/json");
        putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        using (HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent))
        {
            return new ContentResult()
            {
                StatusCode = (int) response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }
    }

    // DELETE: api/Votes/name
    [HttpDelete("{name}")]
    public async Task<IActionResult> Delete(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        using (HttpResponseMessage response = await this.httpClient.DeleteAsync(proxyUrl))
        {
            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int) response.StatusCode);
            }
        }

        return new OkResult();
    }


    /// <summary>
    /// Constructs a reverse proxy URL for a given service.
    /// Example: http://localhost:19081/VotingApplication/VotingData/
    /// </summary>
    /// <param name="serviceName"></param>
    /// <returns></returns>
    private Uri GetProxyAddress(Uri serviceName)
    {
        return new Uri($"http://localhost:19081{serviceName.AbsolutePath}");
    }

    /// <summary>
    /// Creates a partition key from the given name.
    /// Uses the zero-based numeric position in the alphabet of the first letter of the name (0-25).
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    private long GetPartitionKey(string name)
    {
        return Char.ToUpper(name.First()) - 'A';
    }
}
```

<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Oylama örnek uygulamasında izlenecek yol

Oylama uygulaması iki hizmetten oluşur:

* Web ön uç hizmeti (VotingWeb)- Web sayfasına hizmet veren ve arka uç hizmetiyle iletişim için web API'lerini kullanıma sunan bir ASP.NET Core web ön uç hizmeti.
* Arka uç hizmeti (VotingData)- Oy sonuçlarını diskte kalıcı olan güvenilir bir sözlükte depolamak için API'yi kullanıma sunan bir ASP.NET Core web hizmeti.

![Uygulama Diyagramı](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Uygulamada oy kullandığınızda aşağıdaki olaylar gerçekleşir:

1. Oy isteğini bir JavaScript HTTP PUT isteği olarak web ön uç hizmetindeki web API'sine gönderir.

2. Web ön uç hizmeti bir ara sunucu kullanarak HTTP PUT isteğini bulur ve arka uç hizmetine iletir.

3. Arka uç hizmeti gelen isteği alır ve güncelleştirilmiş sonucu güvenilir bir sözlükte depolar. Bu sözlük küme içinde birden çok düğüme çoğaltılır ve diskte kalıcı olur. Uygulamanın tüm verileri kümede depolandığından, veritabanına gerek yoktur.

## <a name="debug-in-visual-studio"></a>Visual Studio'da hata ayıklama

Visual Studio'da uygulamada hata ayıklaması yaparken yerel bir Service Fabric geliştirme kümesi kullanırsınız. Hata ayıklama deneyiminizi senaryonuza göre ayarlama seçeneğiniz vardır. Bu uygulamada, güvenilir bir sözlük kullanarak verileri arka uç hizmetinde depolayın. Hata ayıklayıcıyı durdurduğunuzda Visual Studio varsayılan olarak uygulamayı kaldırır. Uygulamanın kaldırılması arka uç hizmetindeki verilerin de kaldırılmasına neden olur. Hata ayıklama oturumları arasında verilerin kalıcı olmasını sağlamak için, Visual Studio'da **Oylama** projesindeki bir özellik olarak **Uygulama Hata Ayıklama Modu**'nu değiştirebilirsiniz.

Kodda neler olduğuna bakmak için aşağıdaki adımları tamamlayın:

1. **Votingweb\votescontroller.cs** dosyasını açın ve Web API 'sinin **PUT** yönteminde (satır 72) bir kesme noktası ayarlayın.

2. **Votingdata\votedatacontroller.cs** dosyasını açın ve bu Web API 'sinin **PUT** yönteminde (satır 54) bir kesme noktası ayarlayın.

3. Uygulamayı hata ayıklama modunda çalıştırmak için **F5** tuşuna basın.

4. Tarayıcıya dönün ve bir oylama seçeneğine tıklayın veya yeni oylama seçeneği ekleyin. Web ön ucunun api denetleyicisinde ilk kesme noktasına ulaşırsınız.
    

   1. Burası, tarayıcıda JavaScript'in ön uç hizmetindeki API denetleyicisine istek gönderdiği yerdir.

      ![Oy Ön Uç Hizmeti Ekleme](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

   2. İlk olarak, arka uç hizmeti için ReverseProxy'nin URL'sini oluşturun **(1)** .
   3. Ardından, HTTP PUT İsteğini ReverseProxy'ye gönderin **(2)** .
   4. Son olarak, yanıtı arka uç hizmetinden istemciye döndürün **(3)** .

5. Devam etmek için **F5** tuşuna basın.
   1. Şimdi arka uç hizmetindeki kesme noktasındasınız.

      ![Oy Arka Uç Hizmeti Ekleme](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

   2. Yöntemin ilk satırında **(1)** `counts` adlı güvenilir sözlüğü almak veya eklemek için `StateManager` kullanın.
   3. Güvenilir bir sözcükteki değerlerle tüm etkileşimler bir işlem gerektirir; bu using deyimi **(2)** o işlemi oluşturur.
   4. İşlemde, oylama seçeneği için uygun anahtarın değerini güncelleştirin ve işlemi yürütün **(3)** . Commit yöntemi döndüğünde, sözlükteki veriler güncelleştirilir ve kümedeki diğer düğümlere çoğaltılır. Artık veriler güvenli bir şekilde kümede depolanır ve arka uç hizmeti verilerin kullanılabilir olduğu diğer düğümlere yük devretebilir.
6. Devam etmek için **F5** tuşuna basın.

Hata ayıklama oturumunu durdurmak için **Shift+F5** tuşlarına basın.

## <a name="next-steps"></a>Sonraki adımlar

Öğreticinin bu bölümünde, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * ASP.NET Core Web API'si hizmetini durum bilgisi olan güvenilir bir hizmet olarak oluşturma
> * ASP.NET Core Web Uygulaması hizmetini durum bilgisi olmayan bir web hizmeti olarak oluşturma
> * Durum bilgisi olan hizmetle iletişim kurmak için ters proxy kullanma

Sonraki öğreticiye ilerleyin:
> [!div class="nextstepaction"]
> [Uygulamayı Azure’a dağıtma](service-fabric-tutorial-deploy-app-to-party-cluster.md)
