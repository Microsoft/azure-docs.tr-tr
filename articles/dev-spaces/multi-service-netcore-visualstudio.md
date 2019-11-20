---
title: .NET Core ve Visual Studio kullanarak birden çok bağımlı hizmetleri çalıştırma
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Azure’da kapsayıcılar ve mikro hizmetlerle hızlı Kubernetes geliştirme
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kapsayıcılar, Helm, hizmet kafes, ağ hizmeti Yönlendirme, kubectl, k8s
ms.openlocfilehash: dd90dee2f973bb26a43706eb77f15778cb9116a0
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502966"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Azure geliştirme alanları ile birden çok hizmet geliştirme

Bu öğreticide, bazı geliştirme alanları sağlayan sağlamasının yanı sıra Azure geliştirme alanları kullanarak, çoklu hizmet uygulamalarını nasıl geliştirebileceğinizi öğreneceksiniz.

## <a name="call-another-container"></a>Başka bir kapsayıcı çağırma
Bu bölümde, ikinci bir hizmet oluşturacağız `mywebapi`ve `webfrontend` bunu çağırın. Her hizmet ayrı kapsayıcılarda çalışır. Ardından her iki kapsayıcıda da hata ayıklayacaksınız.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>*mywebapi* için örnek kod indirme
Zamandan kazanmak adına örnek kodu bir GitHub deposundan indirelim. [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces ) adresine gidip **Kopyala veya İndir**’i seçerek GitHub deposunu indirin. Bu bölümün kodu `samples/dotnetcore/getting-started/mywebapi` konumundadır.

### <a name="run-mywebapi"></a>*mywebapi* hizmetini çalıştırın
1. `mywebapi` projesini *ayrı bir Visual Studio penceresinde* açın.
1. Daha önce `webfrontend` projesinde yaptığınız gibi başlatma ayarları açılır listesinden **Azure Dev Spaces** seçeneğini belirleyin. Bu sefer yeni bir AKS kümesi oluşturmak yerine, önceden oluşturduğunuz ortamı seçin. Önceki seferde olduğu gibi, Alan açılır listesini varsayılan `default` değerinde bırakın ve **Tamam**’a tıklayın. Çıktı penceresinde, Visual Studio başlatılır, "Bu yeni hizmet, geliştirme alanındaki hata ayıklamaya başladığınızda işlemleri hızlandırmak için Isınma için" fark edebilirsiniz.
1. F5'e bastıktan sonra hizmetin oluşturulup dağıtılmasını bekleyin. Visual Studio durum çubuğu turuncuya döndüğünde hazır olduğunu biliyor olacaksınız
1. Uç nokta URL'si görüntülendiğinde Not **AKS için Azure geliştirme alanları** bölmesinde **çıkış** penceresi. `http://localhost:<portnumber>` gibi görünür. Kapsayıcı yerel olarak çalışıyor gibi görünebilir, ancak gerçekte Azure’daki geliştirme ortamında çalışıyordur.
2. `mywebapi` hazır olduğunda, tarayıcınızı localhost adresine açın ve `ValuesController` için varsayılan GET API’yi çağırmak üzere URL’ye `/api/values` öğesini ekleyin. 
3. Tüm adımları başarılı olursa, `mywebapi` hizmetinden şöyle bir yanıt görebilmelisiniz.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>*webfrontend*’den *mywebapi*’ye istek gönderme
Şimdi `webfrontend` uygulamasında `mywebapi` hizmetine istek gönderen bir kod yazalım. `webfrontend` projesinin bulunduğu Visual Studio penceresine geçin. İçinde `HomeController.cs` dosyası *değiştirin* hakkında yönteminin kodunu aşağıdaki kodla:

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

Önceki kod örneğinde `azds-route-as` üst bilgisi gelen istekten giden isteğe iletilmektedir. Bu daha üretken bir geliştirme deneyimi nasıl daha sonra kolaylaştıran görürsünüz [takım senaryoları](team-development-netcore-visualstudio.md).

### <a name="debug-across-multiple-services"></a>Birden çok hizmette hata ayıklama
1. Bu noktada, `mywebapi` hizmetinin hata ayıklayıcısı ekli bir şekilde çalışmaya devam ediyor olması gerekir. Devam etmiyorsa, `mywebapi` projesinde F5'e basın.
1. `api/values/{id}` GET isteklerini işleyen `Controllers/ValuesController.cs` dosyasının içerdiği `Get(int id)` yönteminde bir kesme noktası ayarlayın.
1. Yukarıdaki kodu yapıştırdığınız `webfrontend` projesinde, `mywebapi/api/values` konumuna GET isteği göndermeden hemen önce bir kesme noktası ayarlayın.
1. `webfrontend` projesinde F5'e basın. Visual Studio yeniden uygun localhost bağlantı noktasına bir tarayıcı açar ve web uygulaması görüntülenir.
1. `webfrontend` projesindeki kesme noktasını tetiklemek için sayfanın üst kısmındaki **Hakkında** bağlantısına tıklayın. 
1. Devam etmek için F10'a basın. `mywebapi` projesindeki kesme noktası tetiklenir.
1. Devam etmek üzere F5’e basarak `webfrontend` projesindeki koda dönersiniz.
1. F5’e bir kez daha bastığınızda istek tamamlanır ve tarayıcıda bir sayfa döndürülür. Web uygulamasında hakkında sayfasında iki hizmet tarafından birleştirilmiş bir ileti görüntülenir: "Hello webfrontend ve Hello mywebapi gelen."

### <a name="well-done"></a>Bravo!
Artık her kapsayıcının ayrı ayrı geliştirilip dağıtılabileceği çok kapsayıcılı bir uygulamanız var.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Takım geliştirme geliştirme alanları hakkında bilgi edinin](team-development-netcore-visualstudio.md)
