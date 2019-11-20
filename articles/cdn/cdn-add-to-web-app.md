---
title: Öğretici - Azure App Service web uygulamasına Azure CDN ekleme | Microsoft Docs
description: Bu öğreticide, Statik dosyalarınızı, dünya çapındaki müşterilerinize yakın sunucularda önbelleğe almak ve dağıtmak için bir Azure App Service web uygulamasına Azure Content Delivery Network (CDN) eklenir.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 1b67522834497a264d95fc9b80246b16841d6026
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594220"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>Öğretici: Azure CDN, bir Azure App Service web uygulamasına ekleme

Bu öğretici, [Azure Content Delivery (CDN)](cdn-overview.md) hizmetinin [Azure App Service’teki bir web uygulamasına](../app-service/overview.md) nasıl ekleneceğini gösterir. Web apps; web uygulamaları, REST API'leri ve mobil arka uçlar barındırmaya yönelik bir hizmettir. 

Kullanacağınız örnek statik HTML sitesinin ana sayfası:

![Örnek uygulama ana sayfası](media/cdn-add-to-web-app/sample-app-home-page.png)

Öğrenecekleriniz:

> [!div class="checklist"]
> * CDN uç noktası oluşturma.
> * Önbelleğe alınan varlıkları yenileme.
> * Önbelleğe alınan sürümleri denetlemek için sorgu dizeleri kullanma.


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

- [Git'i yükleyin](https://git-scm.com/)
- [Azure CLI'yı yükleme](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Web uygulaması oluşturma

Kullanacağınız web uygulamasını oluşturmak için, [statik HTML hızlı başlangıç](../app-service/app-service-web-get-started-html.md) ila **Uygulamaya göz atma** adımlarını izleyin.

## <a name="log-in-to-the-azure-portal"></a>Azure portalında oturum açma

Bir tarayıcıyı açın ve [Azure portalına](https://portal.azure.com) gidin.

### <a name="dynamic-site-acceleration-optimization"></a>Dinamik site hızlandırma iyileştirmesi
CDN uç noktanızı dinamik site hızlandırma (DSA) için iyileştirmek istiyorsanız, profilinizi ve uç noktanızı oluşturmak için [CDN portalını](cdn-create-new-endpoint.md) kullanmalısınız. [DSA iyileştirme](cdn-dynamic-site-acceleration.md) sayesinde, dinamik içeriğe sahip web sayfalarının performansı ölçülebilir şekilde iyileştirilir. CDN portalından bir CDN uç noktasını DSA için iyileştirme hakkında yönergeler için bkz. [Dinamik dosyaların teslimini hızlandırmak için CDN uç nokta yapılandırması](cdn-dynamic-site-acceleration.md#cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files). Aksi takdirde, yeni uç noktanızı iyileştirmek istemiyorsanız, sonraki bölümde yer alan adımları izleyerek oluşturmak için web uygulaması portalını kullanabilirsiniz. **Verizon’dan Azure CDN** profilleri için, bir CDN uç noktası oluşturulduktan sonra uç nokta iyileştirmesini değiştiremediğinizi unutmayın.

## <a name="create-a-cdn-profile-and-endpoint"></a>CDN profili ve uç noktası oluşturma

Sol gezinti bölmesinde **Uygulama Hizmetleri**’ni ve sonra [statik HTML hızlı başlangıç](../app-service/app-service-web-get-started-html.md) içinde oluşturduğunuz uygulamayı seçin.

![Portalda, App Service uygulamasını seçin](media/cdn-add-to-web-app/portal-select-app-services.png)

**App Service** sayfasındaki **Ayarlar** bölümünde, **Ağ > Uygulamanız için Azure CDN'i yapılandırın** seçeneğini belirleyin.

![Portalda CDN’yi seçin](media/cdn-add-to-web-app/portal-select-cdn.png)

**Azure Content Delivery Network** sayfasında, **Yeni uç nokta** ayarlarını tabloda belirtildiği gibi girin.

![Portalda profil ve uç nokta oluşturma](media/cdn-add-to-web-app/portal-new-endpoint.png)

| Ayar | Önerilen değer | Açıklama |
| ------- | --------------- | ----------- |
| **CDN profili** | myCDNProfile | CDN profili, aynı fiyatlandırma katmanına sahip bir CDN uç noktaları koleksiyonudur. |
| **Fiyatlandırma katmanı** | Standart Akamai | [Fiyatlandırma katmanı](cdn-features.md), sağlayıcıyı ve kullanılabilir özellikleri belirtir. Bu öğreticide *Standard Akamai* kullanılır. |
| **CDN uç noktası adı** | azureedge.net etki alanında benzersiz olan tüm adlar | Önbelleğe alınmış kaynaklarınıza *&lt;uçnoktaadı&gt;* .azureedge.net etki alanından erişebilirsiniz.

CDN profili oluşturmak için **Oluştur**’u seçin.

Azure, profili ve uç noktayı oluşturur. Yeni uç nokta, **Uç noktalar** listesinde gösterilir ve sağlandığında **Çalışıyor** durumunda olur.

![Listede yeni uç nokta](media/cdn-add-to-web-app/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>CDN uç noktasını sınama

 Kaydın yayılması zaman alacağından, uç nokta hemen kullanılabilir olmaz: 
   - **Microsoft’tan Azure CDN Standart** profilleri için yayma işlemi genellikle 10 dakikada tamamlanır. 
   - **Akamai’den Azure CDN Standart** profilleri için yayma işlemi genellikle bir dakika içinde tamamlanır. 
   - **Verizon’dan Azure CDN Standart** ve **Verizon’dan Azure CDN Premium** profilleri için yayma işlemi genellikle 90 dakika içinde tamamlanır. 

Örnek uygulamanın, *index.html* dosyası ve diğer statik varlıkları içeren *css*, *img* ve *js* klasörleri vardır. Bu dosyaların tümünün içerik yolları, CDN uç noktasında aynıdır. Örneğin, aşağıdaki URL'ler *css* klasöründeki *bootstrap.css* dosyasına erişir:

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

Bir tarayıcıdan aşağıdaki URL’ye gidin:

```
http://<endpointname>.azureedge.net/index.html
```

![CDN'den sunulan örnek uygulama ana sayfası](media/cdn-add-to-web-app/sample-app-home-page-cdn.png)

 Azure web uygulamasında daha önce çalıştırdığınız aynı sayfayı görürsünüz. Azure CDN, kaynak web uygulamasının varlıklarını almıştır ve CDN uç noktasından bunları sunmaktadır

Bu sayfanın CDN'de önbelleğe alınmasını sağlamak için sayfayı yenileyin. CDN'nin istenen içeriği önbelleğe alması için bazen aynı varlığa ilişkin iki istekte bulunulması gerekir.

Azure CDN profilleri ve uç noktaları oluşturma hakkında daha fazla bilgi için bkz. [Azure CDN kullanmaya başlama](cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>CDN’yi temizleme

CDN, yaşam süresi (TTL) yapılandırmasına bağlı olarak kaynak web uygulamasındaki kaynaklarını düzenli aralıklarla yeniler. Varsayılan TTL yedi gündür.

TTL’nin süresi dolmadan önce CDN'yi yenilemeniz gerekebilir (örn. güncelleştirilmiş içeriği web uygulamasına dağıtırken). Yenileme tetiklemek için, CDN kaynaklarını el ile temizleyin. 

Öğreticinin bu bölümünde, web uygulamasına bir değişiklik dağıtabilir ve önbelleğini yenilemek üzere CDN’i tetiklemek için CDN’i temizleyebilirsiniz.

### <a name="deploy-a-change-to-the-web-app"></a>Web uygulamasına değişiklik dağıtma

Aşağıdaki örnekte gösterildiği gibi, *index.html* dosyasını açın ve H1 başlığına *- V2* ekleyin: 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Değişikliğinizi uygulayın ve web uygulamasına dağıtın.

```bash
git commit -am "version 2"
git push azure master
```

Dağıtım tamamlandıktan sonra, değişikliği görmek için web uygulaması URL'sine bakın.

```
http://<appname>.azurewebsites.net/index.html
```

![Web uygulamasındaki başlıkta V2](media/cdn-add-to-web-app/v2-in-web-app-title.png)

Ana sayfa için CDN uç noktası URL'sine bakarsanız, CDN'de önbelleğe alınmış sürümün henüz süresi dolmadığından, değişikliği göremezsiniz. 

```
http://<endpointname>.azureedge.net/index.html
```

![CDN'deki başlıkta V2 yok](media/cdn-add-to-web-app/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Portalda CDN'i temizleme

Önbelleğe alınmış sürümünü güncelleştirmek üzere CDN'i tetiklemek için, CDN'i temizleyin.

Portalın sol gezinti bölümünde, **Kaynak grupları**’nı ve ardından ve web uygulamanız için oluşturduğunuz kaynak grubunu (myResourceGroup) seçin.

![Kaynak grubu seçin](media/cdn-add-to-web-app/portal-select-group.png)

Kaynak listesinden CDN uç noktanızı seçin.

![Uç nokta seçin](media/cdn-add-to-web-app/portal-select-endpoint.png)

**Uç nokta** sayfasının üst kısmında, **Temizle**'yi seçin.

![Temizleme seçin](media/cdn-add-to-web-app/portal-select-purge.png)

Temizlemek istediğiniz içerik yollarını girin. Tek bir dosyayı temizlemek için tam bir dosya yolunu geçirebileceğiniz gibi, bir klasördeki tüm içeriği temizlemek ve yenilemek için bir yol kesimini de geçirebilirsiniz. *index.html* dosyasını değiştirdiğiniz için, bunun yollardan birinde yer aldığından emin olun.

Sayfanın alt kısmındaki **Temizle**'yi seçin.

![Sayfayı temizleyin](media/cdn-add-to-web-app/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>CDN'in güncelleştirildiğini doğrulayın

Temizleme isteğinin işlenmesi tamamlanana kadar bekleyin. Bu işlem genellikle birkaç dakika sürer. Geçerli durumu görmek için, sayfanın üst kısmındaki zil simgesini seçin. 

![Temizleme bildirimi](media/cdn-add-to-web-app/portal-purge-notification.png)

*index.html* için CDN uç nokta URL'sine bakarken, giriş sayfasının başlığına eklediğiniz *V2*'yi görürsünüz; bu, CDN önbelleğinin yenilendiğini gösterir.

```
http://<endpointname>.azureedge.net/index.html
```

![CDN'deki başlıkta V2](media/cdn-add-to-web-app/v2-in-cdn-title.png)

Daha fazla bilgi için bkz. [Azure CDN uç noktasını temizleme](../cdn/cdn-purge-endpoint.md). 

## <a name="use-query-strings-to-version-content"></a>Sürüm içeriğini belirlemek için sorgu dizelerini kullanın

Azure CDN, aşağıdaki önbelleğe alma davranışı seçeneklerini sunar:

* Sorgu dizelerini yoksay
* Sorgu dizeleri için önbelleğe almayı atla
* Her benzersiz URL'yi önbelleğe al 

İlk seçenek varsayılan davranıştır ve URL’de kullanılan sorgu dizesine bakılmaksızın, bir varlığın yalnızca önbelleğe alınmış bir sürümü olduğu anlamına gelir. 

Öğreticinin bu bölümünde, önbelleğe alma davranışını, her benzersiz URL'yi önbelleğe alacak şekilde değiştirme hakkında bilgi edineceksiniz.

### <a name="change-the-cache-behavior"></a>Önbellek davranışını değiştirme

Azure portalında bulunan **CDN Uç Noktası** sayfasında **Önbellek**’i seçin.

**Sorgu dizesi önbellek davranışı** açılan listesinden, **Her benzersiz URL’yi önbelleğe al**’ı seçin.

**Kaydet**’i seçin.

![Sorgu dizesini önbelleğe alma davranışı seçin](media/cdn-add-to-web-app/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Benzersiz URL'lerin ayrı olarak önbelleğe alındığını doğrulayın

Bir tarayıcıda, CDN uç noktasındaki ana sayfaya gidin ve bir sorgu dizesi ekleyin: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

Azure CDN, başlıkta *V2*'nin de bulunduğu geçerli web uygulaması içeriğini döndürür. 

Bu sayfanın CDN'de önbelleğe alınmasını sağlamak için sayfayı yenileyin. 

*index.html* dosyasını açın, *V2*'yi *V3* olarak değiştirin ve değişikliği dağıtın. 

```bash
git commit -am "version 3"
git push azure master
```

Tarayıcıda, yeni sorgu dizesiyle (örneğin, `q=2`) CDN uç nokta URL'sine gidin. Azure CDN geçerli *index.html* dosyasını alır ve *V3* olarak görüntüler. Öte yandan CDN uç noktasına `q=1` sorgu dizesiyle giderseniz, *V2*'yi görürsünüz.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![CDN'deki başlıkta V3, sorgu dizesi 2](media/cdn-add-to-web-app/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![CDN'deki başlıkta V2, sorgu dizesi 1](media/cdn-add-to-web-app/v2-in-cdn-title-qs1.png)

Bu çıktı, her sorgu dizesinin farklı şekilde değerlendirildiğini gösterir:

* q = 1 daha önce kullanılmıştır, bu nedenle önbelleğe alınan içerikler döndürülür (V2).
* q = 2 yenidir, bu nedenle en son web uygulaması içerikleri alınır ve döndürülür (V3).

Daha fazla bilgi için bkz. [Sorgu dizeleri içeren Azure CDN önbelleğe alma davranışını kontrol etme](../cdn/cdn-query-string.md).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Sonraki adımlar

Öğrendikleriniz:

> [!div class="checklist"]
> * CDN uç noktası oluşturma.
> * Önbelleğe alınan varlıkları yenileme.
> * Önbelleğe alınan sürümleri denetlemek için sorgu dizeleri kullanma.

CDN performansını nasıl iyileştirebileceğinizi öğrenmek için aşağıdaki makalelere göz atın:

> [!div class="nextstepaction"]
> [Öğretici: Azure CDN uç noktanıza özel etki alanı ekleme](cdn-map-content-to-custom-domain.md)


