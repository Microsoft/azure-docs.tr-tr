---
title: 'ExpressRoute bağlantı hattı oluşturma ve değiştirme: Azure CLı'
description: Bu makalede, oluşturma, sağlama, doğrulayın, güncelleştirme, silme ve CLI kullanarak bir ExpressRoute bağlantı hattının sağlamasını Kaldır gösterilmektedir.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: cherylmc
ms.openlocfilehash: 75729811b63e8de3047e45e9b90f5fa3ec657901
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083231"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>CLI kullanarak bir ExpressRoute devre oluşturma ve değiştirme


Bu makalede komut satırı arabirimi (CLI) kullanarak bir Azure ExpressRoute bağlantı hattı oluşturmayı açıklar. Bu makalede ayrıca durumu, güncelleştirme veya silme kontrol edin ve bir bağlantı hattının sağlamasını kaldırma işlemini gösterir. ExpressRoute bağlantı hatları ile çalışmak için farklı bir yöntem kullanmak istiyorsanız, aşağıdaki listeden makaleyi seçebilirsiniz:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager şablonu](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure portalı](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasik)](expressroute-howto-circuit-classic.md)
>

## <a name="before-you-begin"></a>Başlamadan önce

* Başlamadan önce, CLI komutlarının en son sürümünü (2.0 veya üzeri) yükleyin. CLI komutlarını yükleme hakkında bilgi için bkz. [Azure CLI’yi yükleme](/cli/azure/install-azure-cli) ve [Azure CLI’yi Kullanmaya Başlama](/cli/azure/get-started-with-azure-cli).
* Gözden geçirme [önkoşulları](expressroute-prerequisites.md) ve [iş akışları](expressroute-workflows.md) yapılandırmaya başlamadan önce.

## <a name="create"></a>Oluşturma ve bir ExpressRoute bağlantı hattı sağlama

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Azure hesabınızda oturum açın ve aboneliğinizi seçin

Yapılandırmanızı başlamak için Azure hesabınızda oturum açın. "Try It" CloudShell kullanıyorsanız, otomatik olarak oturum açtınız. Bağlanmanıza yardımcı olması için aşağıdaki örnekleri kullanın:

```azurecli
az login
```

Hesapla ilişkili abonelikleri kontrol edin.

```azurecli-interactive
az account list
```

Bir ExpressRoute bağlantı hattı oluşturmak istediğiniz aboneliği seçin.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. desteklenen sağlayıcıların, konumların ve bant genişliklerinin listesini alın

Bir ExpressRoute bağlantı hattı oluşturmadan önce desteklenen bağlantı sağlayıcıları ve konumları bant genişliği seçenekleri listesi gerekir. CLı komutu `az network express-route list-service-providers`, sonraki adımlarda kullanacağınız bu bilgileri döndürür:

```azurecli-interactive
az network express-route list-service-providers
```

Yanıt aşağıdaki örneğe benzer:

```azurecli
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Yanıta bağlantı sağlayıcınız listelenip listelenmediğini denetleyin. Bir devreyi oluştururken ihtiyacınız olacak aşağıdaki bilgileri not edin:

* Ad
* PeeringLocations
* BandwidthsOffered

Bir ExpressRoute bağlantı hattı oluşturmak artık hazırsınız.

### <a name="3-create-an-expressroute-circuit"></a>3. bir ExpressRoute bağlantı hattı oluşturma

> [!IMPORTANT]
> ExpressRoute bağlantı hattı, bir hizmet anahtarı verildiğinde andan itibaren faturalandırılır. Bağlantı sağlayıcısı devreyi sağlamak hazır olduğunda, bu işlemi gerçekleştirin.
>
>

Bir kaynak grubu zaten sahip değilseniz, ExpressRoute devreniz oluşturmadan önce bir oluşturmanız gerekir. Aşağıdaki komutu çalıştırarak bir kaynak grubu oluşturabilirsiniz:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

Aşağıdaki örnek, 200 MB/sn, Silikon vadisi ExpressRoute bağlantı hattı üzerinden Equinix oluşturma işlemi gösterilmektedir. Farklı bir sağlayıcı ve farklı ayarlar kullanıyorsanız, bu bilgileri, isteğinde bulunduğunda değiştirin.

SKU ailesi ve SKU katmanı doğru belirttiğinizden emin olun:

* SKU katmanı, bir ExpressRoute devresinin [Yerel](expressroute-faqs.md#expressroute-local), standart veya [Premium](expressroute-faqs.md#expressroute-premium)olup olmadığını belirler. *Yerel*, *Standart* veya *Premium*belirtebilirsiniz.
* SKU ailesi, fatura türü belirler. Belirtebileceğiniz *Metereddata* ölçülen veri planı için ve *Unlimiteddata* sınırsız veri planı için. Fatura türünden değiştirebilirsiniz *Metereddata* için *Unlimiteddata*, ancak türünden değiştiremezsiniz *Unlimiteddata* için *Metereddata*. *Yerel* devre yalnızca *limiteddata* ' dır.


ExpressRoute bağlantı hattı, bir hizmet anahtarı verildiğinde andan itibaren faturalandırılır. Aşağıdaki örnek, bir istek için yeni bir hizmet anahtarı verilmiştir:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Yanıt hizmet anahtarı içerir.

### <a name="4-list-all-expressroute-circuits"></a>4. tüm ExpressRoute devreleri listeleyin

Oluşturduğunuz tüm ExpressRoute devrelerinin bir listesini almak için `az network express-route list` komutunu çalıştırın. Bu komutu kullanarak bu bilgileri istediğiniz zaman alabilir. Tüm devreler listelemek için parametresiz çağrısı yapın.

```azurecli-interactive
az network express-route list
```

Hizmet anahtarınız listelenen *Servicekey'ini* yanıtın alan.

```azurecli
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

Komutunu çalıştırarak, tüm parametrelerin ayrıntılı açıklamaları alabilirsiniz '-h' parametresi.

```azurecli-interactive
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. sağlama için hizmet anahtarını bağlantı sağlayıcınıza gönderin

'ServiceProviderProvisioningState' hizmet sağlayıcı tarafta sağlama geçerli durumu hakkında bilgi sağlar. Durum Microsoft tarafında durumu sağlar. Daha fazla bilgi için [iş akışları makale](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Yeni bir ExpressRoute bağlantı hattı'ı oluşturduğunuzda, bağlantı hattı şu durumda olur:

```azurecli-interactive
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Bağlantı sağlayıcısı, etkinleştirmeden sürecinde olduğunda bağlantı hattının aşağıdaki duruma değiştirir:

```azurecli-interactive
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Bir ExpressRoute bağlantı hattı kullanabilmek için şu durumda olmalıdır:

```azurecli-interactive
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. her düzenli olarak devre anahtarı durumunu ve durumunu kontrol edin

Durum ve bağlantı hattı tuşunun durumunu denetleme, sağlayıcınız bağlantı hattınızın etkin olduğunda bilmenizi sağlar. Bağlantı hattı yapılandırıldıktan sonra 'ServiceProviderProvisioningState' 'Sağlanıyor' aşağıdaki örnekte gösterildiği gibi görünür:

```azurecli-interactive
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

Yanıt aşağıdaki örneğe benzer:

```azurecli
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="7-create-your-routing-configuration"></a>7. yönlendirme yapılandırmanızı oluşturun

Adım adım yönergeler için bkz: [ExpressRoute bağlantı hattı yönlendirme yapılandırması](howto-routing-cli.md) makale oluşturma ve değiştirme devre eşlemeleri.

> [!IMPORTANT]
> Bu yönergeler yalnızca Katman 2 bağlantı hizmetleri sunan hizmet sağlayıcıları ile oluşturulan bağlantı hatları için geçerlidir. Yönetilen sunan bir hizmet sağlayıcısı kullanıyorsanız, Katman 3 Hizmetleri (genellikle bir IP VPN, MPLS gibi), bağlantı sağlayıcınız yapılandırır ve yönlendirmeyi sizin için yönetir.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. bir ExpressRoute devresine bir sanal ağ bağlama

Ardından, bir sanal ağ, ExpressRoute bağlantı hattına bağlayın. Kullanım [sanal ağları ExpressRoute devresine bağlama](howto-linkvnet-cli.md) makalesi.

## <a name="modify"></a>Bir ExpressRoute bağlantı hattını değiştirme

Belirli bir ExpressRoute bağlantı hattı özelliklerini bağlantıyı etkilemeden değiştirebilirsiniz. Kapalı kalma süresi olmadan aşağıdaki değişiklikleri yapabilir:

* Etkinleştirebilir veya ExpressRoute bağlantı hattı için ExpressRoute premium eklenti devre dışı bırakın.
* Sağlanmış kapasite kullanılabilir bağlantı noktası üzerinde ExpressRoute bağlantı hattı bant genişliğini artırabilirsiniz. Ancak, bir bağlantı hattı bant önceki sürüme indirme desteklenmiyor.
* Sınırsız veri ölçülen verilerden ölçüm planı değiştirebilirsiniz. Ancak, ölçüm plan sınırsız verilerden ölçülen veri değiştirilmesi desteklenmiyor.
* Etkinleştirebilir ve devre dışı *Klasik işlemlere izin Ver'i*.

Sınırlar ve sınırlamalar hakkında daha fazla bilgi için bkz. [ExpressRoute SSS](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>ExpressRoute premium eklentisi etkinleştirmek için

ExpressRoute premium eklentisi aşağıdaki komutu kullanarak, varolan bağlantı hattınız için etkinleştirebilirsiniz:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Bağlantı hattı artık etkin ExpressRoute premium eklenti özellikleri vardır. Komut başarıyla çalıştırıldı hemen sonra için premium eklenti özelliğini fatura başlamadan.

### <a name="to-disable-the-expressroute-premium-add-on"></a>ExpressRoute premium eklentisi devre dışı bırakmak için

> [!IMPORTANT]
> Bu işlem için standart devreyi izin daha büyük olan kaynaklar kullanıyorsanız, başarısız olabilir.
>
>

ExpressRoute premium eklentisi devre dışı bırakmadan önce aşağıdaki ölçütleri de anlamanız:

* Premium katmanından standart sürümüne düşürme önce işlem hattına bağlı 10'dan az sanal ağlara sahip emin olmanız gerekir. 10'dan fazla varsa, güncelleştirme isteği başarısız olur ve premium fiyatları üzerinden faturalandırılırsınız.
* Diğer jeopolitik bölgede tüm sanal ağları bağlantısını kaldırmanız gerekir. Tüm sanal ağları bağlantısını yok ise, güncelleştirme isteği başarısız olur ve premium fiyatları üzerinden faturalandırılırsınız.
* Özel eşdüzey hizmet sağlama için 4000'den az yollar yol tablonuz olması gerekir. Rota tablosu boyutunuz 4000 yollara kıyasla daha büyükse, BGP oturumu bırakır. Tanıtılan ön ek sayısı 4.000 olana kadar oturumu yeniden iler hale gerekmez.

Aşağıdaki örneği kullanarak mevcut bir devreyi için ExpressRoute premium eklentisi devre dışı bırakabilirsiniz:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>ExpressRoute bağlantı hattı bant genişliğini güncelleştirmek için

Denetimi sağlayıcınız için desteklenen bir bant genişliği seçenekleri için [ExpressRoute SSS](expressroute-faqs.md). Mevcut bağlantı hattınızın boyutundan daha büyük herhangi bir boyutta seçebilirsiniz.

> [!IMPORTANT]
> Var olan bağlantı noktası üzerinde yeterli kapasitesi varsa, ExpressRoute bağlantı hattı yeniden oluşturmanız gerekebilir. Yoksa hiçbir ek kapasite kullanılabilir o konumda devre yükseltemezsiniz.
>
> Kesintisiz bir ExpressRoute bağlantı hattı bant indiremezsiniz. Bant genişliği eski sürüme düşürme, ExpressRoute bağlantı hattının sağlamasını kaldırma ve ardından yeni ExpressRoute bağlantı hattı yeniden sağlamak gerektirir.
>

Gereksinim boyutu karar verdikten sonra bağlantı hattınızı yeniden boyutlandırmak için aşağıdaki komutu kullanın:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Bağlantı hattınız Microsoft tarafında boyutlandırılır. Ardından, bu değişikliği eşleşecek şekilde kendi tarafında yapılandırmaları güncelleştirmek için bağlantı sağlayıcınıza başvurmanız gerekir. Bu bildirim yaptıktan sonra güncelleştirilmiş bant seçeneği için faturalama başlayın.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>SKU taşımak için sınırsız olarak ölçülür

Aşağıdaki örneği kullanarak bir ExpressRoute bağlantı hattı SKU'su değiştirebilirsiniz:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Klasik ve Resource Manager ortamları erişimi denetlemek için

Gözden geçirme yönergeleri [Resource Manager dağıtım modeline taşıma ExpressRoute devreleri Klasikten](expressroute-howto-move-arm.md).

## <a name="delete"></a>Sağlama kaldırmayı ve bir ExpressRoute bağlantı hattı siliniyor

Sağlamasını kaldırma ve bir ExpressRoute bağlantı hattını silmek için aşağıdaki ölçütleri anladığınızdan emin olun:

* ExpressRoute bağlantı hattınızdaki tüm sanal ağların bağlantısını kaldırmanız gerekir. Bu işlem başarısız olursa, tüm sanal ağları işlem hattına bağlı olmadığını denetleyin.
* ExpressRoute bağlantı hattı Hizmet Sağlayıcısı sağlama durumu ise **sağlama** veya **sağlanan**, kendi tarafında bağlantı hattını sağlamasını kaldırmak için hizmet sağlayıcınızla birlikte çalışmanız gerekir. Kaynak ayırmanıza ve hizmeti sağlayıcısı devreyi sağlamayı kaldırma tamamlandıktan ve bize bildiren kadar faturalandırılırsınız devam ediyoruz.
* Hizmet sağlayıcısı devreyi sağlamayı durdurduğunda devre silebilirsiniz. Bir devreyi sağlaması, hizmet sağlayıcısı sağlama durumu kümesine **sağlanmadı**. Bu durumda bağlantı hattının faturalandırılması durdurulur.

Aşağıdaki komutu çalıştırarak, ExpressRoute devreniz silebilirsiniz:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bağlantı hattınızı oluşturduktan sonra aşağıdaki görevleri gerçekleştirmek emin olun:

* [ExpressRoute bağlantı hattı için yönlendirme oluşturma ve değiştirme](howto-routing-cli.md)
* [Sanal ağınız, ExpressRoute devresine bağlama](howto-linkvnet-cli.md)
