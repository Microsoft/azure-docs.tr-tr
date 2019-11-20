---
title: Öğretici-güncelleştirmeyi coğrafi olarak çoğaltılan Azure Container Registry gönderin
description: Coğrafi olarak çoğaltılan Azure Container Registry 'nize güncelleştirilmiş bir Docker görüntüsü gönderin, daha sonra birden çok bölgede çalışan Web uygulamalarına otomatik olarak dağıtılan değişikliklere bakın. Üç bölümden oluşan bir serinin üçüncü bölümü.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 04/30/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: adf6348e3b4c5fa728a0289ccd5bd3f289872108
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931386"
---
# <a name="tutorial-push-an-updated-container-image-to-a-geo-replicated-container-registry-for-regional-web-app-deployments"></a>Öğretici: bölgesel Web uygulaması dağıtımları için coğrafi olarak çoğaltılan bir kapsayıcı kayıt defterine güncelleştirilmiş bir kapsayıcı görüntüsü gönderme

Bu, üç bölümden oluşan bir öğretici serisinin üçüncü bölümüdür. [Önceki öğreticide](container-registry-tutorial-deploy-app.md) coğrafi çoğaltma, iki farklı bölgesel Web Uygulaması dağıtımı için yapılandırılmıştır. Bu öğreticide ilk olarak uygulamayı değiştirir, ardından yeni bir kapsayıcı görüntüsü derler ve bunu coğrafi olarak çoğaltılmış kayıt defterinize gönderirsiniz. Son olarak, her iki Web Uygulaması örneğinde, Azure Container Registry web kancaları tarafından otomatik olarak dağıtılan değişikliği görüntülersiniz.

Bu öğreticide, serinin son kısmı:

> [!div class="checklist"]
> * Web uygulaması HTML’ini değiştirme
> * Docker görüntüsünü derleme ve etiketleme
> * Azure Container Registry’ye değişikliği gönderme
> * İki farklı bölgede güncelleştirilmiş uygulamayı görüntüleme

Henüz iki *Kapsayıcılar için Web Uygulaması* bölgesel dağıtımını yapılandırmadıysanız, seride yer alan [Azure Container Registry’den web uygulaması dağıtma](container-registry-tutorial-deploy-app.md) adlı önceki öğreticiye geri dönün.

## <a name="modify-the-web-application"></a>Web uygulamasını değiştirme

Bu adımda, güncelleştirilmiş kapsayıcı görüntüsünü Azure Container Registry’ye göndermenizin ardından, web uygulaması üzerinde yüksek oranda görünür olacak bir değişiklik yapın.

Önceki öğreticide `AcrHelloworld/Views/Home/Index.cshtml`GitHub’dan kopyaladığınız[ uygulama kaynağında ](container-registry-tutorial-prepare-registry.md#get-application-code) dosyasını bulun ve sık kullandığınız metin düzenleyicinizde açın. Mevcut `<h1>` satırının altına aşağıdaki satırı ekleyin:

```html
<h1>MODIFIED</h1>
```

Değiştirdiğiniz `Index.cshtml` şuna benzemelidir:

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<style>
    body {
        background-image: url('images/azure-regions.png');
        background-size: cover;
    }
    .footer {
        position: fixed;
        bottom: 0px;
        width: 100%;
    }
</style>

<h1 style="text-align:center;color:blue">Hello World from:  @ViewData["REGION"]</h1>
<h1>MODIFIED</h1>
<div class="footer">
    <ul>
        <li>Registry URL: @ViewData["REGISTRYURL"]</li>
        <li>Registry IP: @ViewData["REGISTRYIP"]</li>
        <li>Registry Region: @ViewData["REGION"]</li>
    </ul>
</div>
```

## <a name="rebuild-the-image"></a>Görüntüyü yeniden derleme

Web uygulamasını güncelleştirdiğinize göre artık kapsayıcı görüntüsünü yeniden derleyebilirsiniz. Daha önce olduğu gibi, etiket için oturum açma sunucusu tam etki alanı adı (FQDN) da dahil olmak üzere, tam görüntü adını kullanın:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="push-image-to-azure-container-registry"></a>Azure Container Registry’ye görüntü gönderme

Şimdi güncelleştirilmiş *acr-helloworld* kapsayıcı görüntüsünü coğrafi olarak çoğaltılmış kayıt defterinize gönderin. Burada, güncelleştirilmiş görüntüyü hem `docker push`Batı ABD*hem de*Doğu ABD*bölgesindeki kayıt defteri çoğaltmalarına dağıtmak için tek bir* komutunu yürütüyorsunuz.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

`docker push` çıktınızın aşağıdakine benzer olması gerekir:

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
5b9454e91555: Pushed
d6803756744a: Layer already exists
b7b1f3a15779: Layer already exists
a89567dff12d: Layer already exists
59c7b561ff56: Layer already exists
9a2f9413d9e4: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:4c3f2211569346fbe2d1006c18cbea2a4a9dcc1eb3a078608cef70d3a186ec7a size: 1792
```

## <a name="view-the-webhook-logs"></a>Web kancası günlüklerini görüntüleme

Görüntü çoğaltılırken, tetiklenen Azure Container Registry web kancalarını görebilirsiniz.

Önceki öğreticide kapsayıcıyı *Kapsayıcılar için Web Uygulamaları*’na dağıttığınızda oluşturulan bölgesel web kancalarını görmek için, Azure portalındaki kapsayıcı kayıt defterinize gidin ve **HİZMETLER** bölümünden **Web kancaları**’nı seçin.

![Azure portalındaki kapsayıcı kayıt defteri web kancaları][tutorial-portal-01]

Web kancasının çağrı ve yanıt geçmişini görmek için web kancasını seçin. Her iki Web Kancasının günlüklerinde **gönderme** eylemi için bir satır görmeniz gerekir. Burada, *Batı ABD* bölgesinde bulunan Web Kancasının günlüğü, önceki adımda **tarafından tetiklenen**gönderme`docker push` eylemini gösterir:

![Azure portalındaki kapsayıcı kayıt defteri Web Kancası (Batı ABD)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>Güncelleştirilen web uygulamasını görüntüleme

Web Kancaları, yeni görüntünün kayıt defterine gönderildiğini Web Uygulamalarına bildirir ve o da güncelleştirilen kapsayıcıyı iki bölgesel web uygulamasına otomatik olarak dağıtır.

Web tarayıcınızdaki her iki bölgesel Web Uygulaması dağıtımına giderek uygulamanın her iki dağıtımda güncelleştirildiğini doğrulayın. Dağıtılan web uygulamasının URL’sini, her bir Uygulama Hizmetinin genel bakış sekmesinin sağ üst kısmında bulabilirsiniz.

![Azure portalında Uygulama Hizmeti genel bakışı][tutorial-portal-03]

Güncelleştirilmiş uygulamayı görmek için Uygulama Hizmeti genel bakışında bağlantıyı seçin. Aşağıda, *Batı ABD*’de çalıştırılan uygulamanın örnek bir görünümü verilmiştir:

![Batı ABD bölgesinde çalıştırılan değiştirilmiş web uygulamasının tarayıcı görünümü][deployed-app-westus-modified]

Güncelleştirilmiş kapsayıcı görüntüsünü tarayıcınızda görüntüleyerek bunun *Doğu ABD* dağıtımına da dağıtıldığını doğrulayın.

![Doğu ABD bölgesinde çalıştırılan değiştirilmiş web uygulamasının tarayıcı görünümü][deployed-app-eastus-modified]

Tek bir `docker push` ile, her iki bölgesel Web App dağıtımında çalışan web uygulamasını otomatik olarak güncelleştirdiniz. Ve Azure Container Registry, her dağıtıma en yakın konumda bulunan depolardaki kapsayıcı görüntülerini sundu.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, web uygulaması kapsayıcısını güncelleştirdiniz ve web uygulaması kapsayıcısının yeni bir sürümünü coğrafi olarak çoğaltılmış kayıt defterinize gönderdiniz. Azure Container Registry’deki web kancaları, Kapsayıcılar için Web Apps’e güncelleştirmeyi bildirdi ve bu da en yakın kayıt defteri çoğaltmasından yerel bir çekme işlemini tetikledi.

### <a name="acr-build-automated-image-build-and-patch"></a>ACR yapı: Otomatik görüntü oluşturma ve düzeltme eki uygulama

Coğrafi çoğaltmaya ek olarak ACR Build, Azure Container Registry’nin kapsayıcı dağıtım işlem hattınızın iyileştirilmesine yardımcı olabilecek başka bir özelliğidir. Özellikleri hakkında bir fikir edinmek için ACR Build’a genel bakış ile başlayın:

[ACR Build ile işletim sistemi ve çerçeve düzeltme eki uygulamayı otomatikleştirme](container-registry-tasks-overview.md)

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png