---
title: 'Hızlı başlangıç: ExpressRoute devresini oluşturma ve değiştirme: Azure CLı'
description: Bu hızlı başlangıçta, Azure CLı kullanarak bir ExpressRoute bağlantı hattı oluşturma, sağlama, doğrulama, güncelleştirme, silme ve kaldırma işlemlerinin nasıl yapılacağı gösterilmektedir.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: a1d50c3f8f94fbfd7dbcb9b25e051b7f2951c518
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91969101"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak bir ExpressRoute bağlantı hattı oluşturma ve değiştirme

Bu hızlı başlangıçta, komut satırı arabirimini (CLı) kullanarak Azure ExpressRoute bağlantı hattı oluşturma açıklanmaktadır. Bu makalede ayrıca, durumu denetleme, güncelleştirme veya silme ve devre sağlamayı kaldırma işlemlerinin nasıl yapılacağı gösterilir.

## <a name="prerequisites"></a>Önkoşullar

* Yapılandırmaya başlamadan önce [önkoşulları](expressroute-prerequisites.md) ve [iş akışlarını](expressroute-workflows.md) gözden geçirin.
* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* CLı komutlarının en son sürümünü (2,0 veya üzeri) yükler. CLI komutlarını yükleme hakkında bilgi için bkz. [Azure CLI’yi yükleme](/cli/azure/install-azure-cli) ve [Azure CLI’yi Kullanmaya Başlama](/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute bağlantı hattı oluşturma ve sağlama

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure hesabınızda oturum açın ve aboneliğinizi seçin

Yapılandırmanızı başlatmak için Azure hesabınızda oturum açın. "TRY It" Cloud Shell kullanırsanız, otomatik olarak oturum açtınız. Bağlanmanıza yardımcı olması için aşağıdaki örnekleri kullanın:

```azurecli-interactive
az login
```

Hesapla ilişkili abonelikleri kontrol edin.

```azurecli-interactive
az account list
```

ExpressRoute bağlantı hattı oluşturmak istediğiniz aboneliği seçin.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>Desteklenen sağlayıcıların, konumların ve bant genişliklerinin listesini al

ExpressRoute bağlantı hattı oluşturmadan önce, desteklenen bağlantı sağlayıcıları, konumlar ve bant genişliği seçeneklerinin listesine ihtiyacınız vardır. CLı komutu `az network express-route list-service-providers` , sonraki adımlarda kullanacağınız bu bilgileri döndürür:

```azurecli-interactive
az network express-route list-service-providers
```

Yanıt aşağıdaki örneğe benzer:

```output
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

Bağlantı sağlayıcınızın listelenip listelenmediğini görmek için yanıtı denetleyin. Bir devre oluşturduğunuzda ihtiyacınız olacak aşağıdaki bilgileri unutmayın:

* Name
* PeeringLocations
* BandwidthsOffered

Artık bir ExpressRoute devresi oluşturmaya hazırsınız.

### <a name="create-an-expressroute-circuit"></a>ExpressRoute bağlantı hattı oluşturma

> [!IMPORTANT]
> ExpressRoute bağlantı hattı, hizmet anahtarının verildiği andan itibaren faturalandırılır. Bağlantı sağlayıcısı, devreyi sağlamaya hazırsa bu işlemi gerçekleştirin.
>
>

Henüz bir kaynak grubunuz yoksa, ExpressRoute bağlantı hattını oluşturmadan önce bir tane oluşturmanız gerekir. Aşağıdaki komutu çalıştırarak bir kaynak grubu oluşturabilirsiniz:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

Aşağıdaki örnek, bir 200 Mbps ExpressRoute bağlantı hattının, Silicon Valley içinde Equinx aracılığıyla nasıl oluşturulacağını göstermektedir. Farklı bir sağlayıcı ve farklı ayarlar kullanıyorsanız, isteğinizi yaparken bu bilgileri değiştirin.

Doğru SKU katmanını ve SKU ailesini belirttiğinizden emin olun:

* SKU katmanı, bir ExpressRoute devresinin [Yerel](expressroute-faqs.md#expressroute-local), standart veya [Premium](expressroute-faqs.md#expressroute-premium)olduğunu belirler. *Yerel*, * standart veya *Premium* belirtebilirsiniz. SKU 'YU *Standart/Premium* 'dan *Yerel* olarak değiştiremezsiniz.
* SKU ailesi Faturalandırma türünü belirler. Tarifeli veri planı için *MeteredData* , sınırsız bir veri planı Için de *unlimiteddata* belirtebilirsiniz. Faturalandırma türünü *MeteredData* 'Den *unlimiteddata* olarak değiştirebilirsiniz, ancak türü *Unlimiteddata* iken *MeteredData* olarak değiştiremezsiniz. *Yerel* devre yalnızca *limiteddata* ' dır.


ExpressRoute bağlantı hattı, hizmet anahtarının verildiği andan itibaren faturalandırılır. Aşağıdaki örnek, yeni bir hizmet anahtarı isteği örneğidir:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Yanıt, hizmet anahtarını içerir.

### <a name="list-all-expressroute-circuits"></a>Tüm ExpressRoute devreleri listeleyin

Oluşturduğunuz tüm ExpressRoute devrelerinin listesini almak için `az network express-route list` komutunu çalıştırın. Bu bilgileri, bu komutu kullanarak istediğiniz zaman alabilirsiniz. Tüm devreleri listelemek için, hiçbir parametre olmadan çağrıyı yapın.

```azurecli-interactive
az network express-route list
```

Hizmet anahtarınız, yanıtın *ServiceKey* alanında listelenir.

```output
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

'-H ' parametresini kullanarak komutu çalıştırarak tüm parametrelerin ayrıntılı açıklamalarını alabilirsiniz.

```azurecli-interactive
az network express-route list -h
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Hizmet anahtarını sağlama için bağlantı sağlayıcınıza gönderin

' ServiceProviderProvisioningState ', hizmet sağlayıcı tarafında sağlama durumunun geçerli durumu hakkında bilgi sağlar. Durum, Microsoft tarafında durumu sağlar. Daha fazla bilgi için bkz. [Iş akışları makalesi](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Yeni bir ExpressRoute bağlantı hattı oluşturduğunuzda, devre aşağıdaki durumda olur:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Bağlantı sağlayıcısı şu anda sizin için etkinleştirdiğinden, devre aşağıdaki duruma geçer:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

ExpressRoute devresini kullanmak için, aşağıdaki durumda olmalıdır:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Devre anahtarının durumunu ve durumunu düzenli olarak denetleyin

Hizmet anahtarının durumunun ve durumunun denetlenmesi, sağlayıcınızın devrenizi ne zaman sağladığından haberdar etmenize olanak tanır. Devre yapılandırıldıktan sonra, aşağıdaki örnekte gösterildiği gibi *Serviceproviderprovisioningstate* *sağlandı* olarak görünür:

```azurecli-interactive
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

Yanıt aşağıdaki örneğe benzer:

```output
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

### <a name="create-your-routing-configuration"></a>Yönlendirme yapılandırmanızı oluşturma

Adım adım yönergeler için, bkz. bağlantı hattı eşlemelerini oluşturmak ve değiştirmek için [ExpressRoute devrouting yapılandırma](howto-routing-cli.md) makalesi.

> [!IMPORTANT]
> Bu yönergeler yalnızca, katman 2 bağlantı hizmetleri sunan hizmet sağlayıcılarıyla oluşturulan devrelere uygulanır. Yönetilen katman 3 Hizmetleri (genellikle MPLS gibi bir IP VPN) sunan bir hizmet sağlayıcısı kullanıyorsanız, bağlantı sağlayıcınız yönlendirmeyi yapılandırır ve yönetir.
>
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>ExpressRoute bağlantı hattına bir sanal ağı bağlama

Ardından, bir sanal ağı ExpressRoute bağlantı hattına bağlayın. [Sanal ağları ExpressRoute devresine bağlama](howto-linkvnet-cli.md) makalesini kullanın.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>ExpressRoute devresini değiştirme

Bağlantıyı etkilemeden bir ExpressRoute devresine ait belirli özellikleri değiştirebilirsiniz. Kapalı kalma süresi olmadan aşağıdaki değişiklikleri yapabilirsiniz:

* ExpressRoute devreniz için bir ExpressRoute Premium eklentisini etkinleştirebilir veya devre dışı bırakabilirsiniz. SKU 'nun *Standart/Premium* 'dan *Yerel* olarak değiştirilmesi desteklenmez.
* Bağlantı noktasında kullanılabilir kapasiteyi sağlayan ExpressRoute bağlantı hattının bant genişliğini artırabilirsiniz. Ancak, devre dışı bir bağlantı hattının eski sürüme düşürülmesi desteklenmez.
* Ölçüm planını ölçülen verilerden sınırsız veriye dönüştürebilirsiniz. Ancak, ölçüm planının sınırsız verilerden ölçülen verilerle değiştirilmesi desteklenmez.
* *Klasik Işlemlere Izin ver*' i etkinleştirebilir ve devre dışı bırakabilirsiniz.

Sınırlar ve sınırlamalar hakkında daha fazla bilgi için bkz. [ExpressRoute SSS](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>ExpressRoute Premium eklentisini etkinleştirmek için

Aşağıdaki komutu kullanarak mevcut bağlantı hattınızı ExpressRoute Premium eklentisi ' ni etkinleştirebilirsiniz:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Devre dışı bırakıldı ExpressRoute Premium eklenti özellikleri etkinleştirilmiştir. Komut başarıyla çalıştırıldığında, Premium eklenti özelliği için faturalandırmaya başlayacağız.

### <a name="to-disable-the-expressroute-premium-add-on"></a>ExpressRoute Premium eklentisini devre dışı bırakmak için

> [!IMPORTANT]
> Standart devre için izin verilen miktardan daha büyük kaynaklar kullanıyorsanız bu işlem başarısız olabilir.
>
>

ExpressRoute Premium eklentisini devre dışı bırakmadan önce aşağıdaki ölçütleri anlayın:

* Premium 'dan standart sürümüne düşürme yapmadan önce, bağlantı hattına bağlı sanal ağların sayısının 10 ' dan küçük olduğundan emin olmanız gerekir. Bunu yapmazsanız, güncelleştirme isteğiniz başarısız olur ve size Premium ücretler üzerinden faturalandırırız.
* Diğer geopolitik bölgelerdeki tüm sanal ağların ilk olarak bağlantısı kaldırılmalıdır. Bağlantıyı kaldıramazsanız, güncelleştirme isteğiniz başarısız olur ve Premium ücretlerden faturalandırıldığımızda çalışmaya devam ediyoruz.
* Yol tablonuz, özel eşleme için 4.000 rotadan az olmalıdır. Yol tablonuzun boyutu 4.000 rotadan büyükse BGP oturumu başlatılır. Tanıtılan ön ek sayısı 4.000 altına çıkana kadar BGP oturumu yeniden etkinleştirilmez.

Aşağıdaki örneği kullanarak mevcut devre için ExpressRoute Premium eklentisini devre dışı bırakabilirsiniz:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>ExpressRoute bağlantı hattı bant genişliğini güncelleştirmek için

Sağlayıcınız için desteklenen bant genişliği seçenekleri için bkz. [ExpressRoute SSS](expressroute-faqs.md). Mevcut devrenin boyutundan daha büyük bir boyut seçebilirsiniz.

> [!IMPORTANT]
> Mevcut bağlantı noktasında yetersiz kapasite varsa, ExpressRoute devresini yeniden oluşturmanız gerekebilir. Bu konumda kullanılabilir ek kapasite yoksa, devreyi yükseltemezsiniz.
>
> Bir ExpressRoute devresinin bant genişliğini kesintiye uğramadan azaltamaz. Bant genişliğini düşürme, ExpressRoute bağlantı hattının sağlanması ve ardından yeni bir ExpressRoute bağlantı hattı sağlamanız gerekir.
>

İhtiyacınız olan boyuta karar verdikten sonra, devrenizi yeniden boyutlandırmak için aşağıdaki komutu kullanın:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Devreniz Microsoft tarafında yükseltilecek. Daha sonra, bu değişikliği eşleştirmek üzere kendi taraflarındaki yapılandırmaların güncelleştirilmesini sağlamak için bağlantı sağlayıcınızla iletişim kurmanız gerekir. Bu bildirimi yaptıktan sonra, güncelleştirilmiş bant genişliği seçeneği için faturalandırmaya başlayacağız.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>SKU 'YU tarifeli 'ten sınırsız 'e taşımak için

Aşağıdaki örneği kullanarak bir ExpressRoute devresine ait SKU 'sunu değiştirebilirsiniz:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Klasik ve Kaynak Yöneticisi ortamlarına erişimi denetlemek için

[ExpressRoute devrelerini klasik Kaynak Yöneticisi dağıtım modeline taşıma](expressroute-howto-move-arm.md)bölümündeki yönergeleri gözden geçirin.

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute devresinin sağlamasını kaldırma

Bir ExpressRoute devresini sağlamayı ve silmeyi kaldırmak için aşağıdaki ölçütleri anladığınızdan emin olun:

* Tüm sanal ağların ExpressRoute bağlantı hattı 'ndan bağlantısı kaldırılmalıdır. Bu işlem başarısız olursa, devline bağlı sanal ağların olup olmadığını denetleyin.
* ExpressRoute bağlantı hattı hizmeti sağlayıcısı sağlama **durumu sağlandıysa veya** sağlanmışsa  , devre dışı bırakmak için hizmet sağlayıcınızla birlikte çalışmanız gerekir. Hizmet sağlayıcısı, devre sağlamasını kaldırmayı ve bize haber verene kadar, kaynakları ayırmaya ve sizi faturalandırmaya devam ediyoruz.
* Hizmet sağlayıcı, hizmet sağlayıcısı sağlama durumunun **sağlanmamış** olarak ayarlandığı anlamına gelen devre sağlamasını kaldırdıysa, devreyi silebilirsiniz. Devre için faturalandırma daha sonra durur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Aşağıdaki komutu çalıştırarak ExpressRoute devrenizi silebilirsiniz:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bağlantı hattını oluşturduktan ve sağlayıcınızda sağladıktan sonra, eşlemeyi yapılandırmak için sonraki adıma geçin:

> [!div class="nextstepaction"]
> [ExpressRoute bağlantı hattı için yönlendirme oluşturma ve değiştirme](howto-routing-cli.md)
