---
title: Azure Kubernetes Service (AKS) içinde Kullanıcı tanımlı yolları (UDR) özelleştirme
description: Azure Kubernetes Service (AKS) içinde özel çıkış yolu tanımlama hakkında bilgi edinin
services: container-service
ms.topic: article
ms.date: 06/29/2020
ms.openlocfilehash: e9433978c8ee855ec66901c7692e4d2b59261fd3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773054"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Küme çıkışı 'nı bir User-Defined rotası ile özelleştirme

AKS kümesinden çıkış, belirli senaryolara uyacak şekilde özelleştirilebilir. Varsayılan olarak AKS, çıkış için ayarlanacak ve kullanılacak standart bir SKU Load Balancer sağlar. Ancak, genel IP 'Ler izin verilmedikçe veya çıkış için ek atlamalar gerekliyse, varsayılan kurulum tüm senaryoların gereksinimlerini karşılamayabilir.

Bu makalede, genel IP 'Lere izin vermeyen ve kümenin bir ağ sanal gereci (NVA) arkasına oturmasının gerekli olduğu gibi özel ağ senaryolarını desteklemek için bir kümenin çıkış yolunun nasıl özelleştirileceği gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar
* Azure CLı sürüm 2.0.81 veya üzeri
* `2020-01-01`Veya daha büyük API sürümü


## <a name="limitations"></a>Sınırlamalar
* OutboundType, yalnızca küme oluşturma zamanında tanımlanabilir ve daha sonra güncellenemiyor.
* Ayar `outboundType` , ve için AKS kümeleri `vm-set-type` gerektirir `VirtualMachineScaleSets` `load-balancer-sku` `Standard` .
* `outboundType`Bir değere ayarlandığında `UDR` , küme için geçerli giden bağlantıya sahip kullanıcı tanımlı bir yol gerekir.
* `outboundType`Bir değere ayarlandığında `UDR` , yük dengeleyicisine yönlendirilen giriş kaynak IP 'si kümenin giden çıkış hedefi adresiyle **eşleşmeyebilir** .

## <a name="overview-of-outbound-types-in-aks"></a>AKS 'deki giden türlere genel bakış

AKS kümesi, veya türünde benzersiz bir şekilde özelleştirilebilir `outboundType` `loadBalancer` `userDefinedRouting` .

> [!IMPORTANT]
> Giden türü yalnızca kümenizin çıkış trafiğini etkiler. Daha fazla bilgi için bkz. giriş [denetleyicilerini ayarlama](ingress-basic.md).

> [!NOTE]
> UDR ve Kubernetes kullanan Networking ile kendi [yol tablonuzu][byo-route-table] kullanabilirsiniz. Küme kimliğinin (hizmet sorumlusu veya yönetilen kimlik) özel yol tablosuna katkıda bulunan izinlerine sahip olduğundan emin olun.

### <a name="outbound-type-of-loadbalancer"></a>Yük dengeleyici giden türü

`loadBalancer`Ayarlanırsa, AKS aşağıdaki yapılandırmayı otomatik olarak tamamlar. Yük dengeleyici, bir AKS atanmış genel IP üzerinden çıkış için kullanılır. Giden türü `loadBalancer` `loadBalancer` , aks kaynak sağlayıcısı tarafından oluşturulan yük dengeleyiciden çıkış bekleyen, türü Kubernetes hizmetlerini destekler.

Aşağıdaki yapılandırma AKS tarafından yapılır.
   * Küme çıkışı için genel bir IP adresi sağlandı.
   * Genel IP adresi, yük dengeleyici kaynağına atanır.
   * Yük Dengeleyici için arka uç havuzları kümedeki aracı düğümleri için ayarlanır.

Varsayılan olarak, AKS kümelerinde dağıtılan ve ' ı kullanan bir ağ topolojisi vardır `outboundType` `loadBalancer` .

![Diyagram, giriş g/çıkış ı P 'yi gösterir; burada giriş ı P, trafiği bir iç kümeye ve diğer trafiğe trafiği Internet 'e yönlendiren, M C R, Azure gerekli hizmetleri ve A K S denetim düzlemi 'ni yönlendiren bir yük dengeleyiciye yönlendirir.](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>UserDefinedRouting 'in giden türü

> [!NOTE]
> Giden türü kullanmak gelişmiş bir ağ senaryosudur ve uygun ağ yapılandırması gerektirir.

`userDefinedRouting`Ayarlanırsa, AKS çıkış yollarını otomatik olarak yapılandırmaz. Çıkış kurulumu sizin tarafınızdan yapılmalıdır.

AKS kümesi, standart yük dengeleyici (SLB) mimarisi kullanılmadıklarında, açık çıkış oluşturmanız gerektiğinden, daha önce yapılandırılmış bir alt ağa sahip mevcut bir sanal ağa dağıtılmalıdır. Bu şekilde, Bu mimaride bir güvenlik duvarı, ağ geçidi, ara sunucu veya ağ adresi çevirisi (NAT) 'nin standart yük dengeleyiciye veya gerecine atanan bir genel IP tarafından yapılmasına izin vermek için bu mimarinin çıkış trafiğini açıkça göndermesi gerekir.

#### <a name="load-balancer-creation-with-userdefinedrouting"></a>UserDefinedRouting ile yük dengeleyici oluşturma

Giden UDR türünde AKS kümeleri, yalnızca ' loadBalancer ' türündeki ilk Kubernetes hizmeti dağıtıldığında standart yük dengeleyici (SLB) alır. Yük dengeleyici *gelen* istekler için genel bir IP adresi ve *gelen* istekler için bir arka uç havuzu ile yapılandırılır. Gelen kuralları Azure bulut sağlayıcısı tarafından yapılandırılır, ancak giden **genel IP adresi veya giden kuralları** , giden bir UDR türüne sahip olma sonucu olarak yapılandırılmaz. UDR 'niz çıkış trafiği için tek kaynak olmaya devam eder.

Azure yük dengeleyiciler [, bir kural yerleştirilene kadar ücretlendirilmez](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Bir kümeyi UDR ve Azure Güvenlik duvarının giden türüyle dağıtma

Kullanıcı tanımlı bir yol kullanarak giden türü olan bir kümenin uygulamasını göstermek için bir küme, kendi alt ağında Azure Güvenlik duvarı olan bir sanal ağ üzerinde yapılandırılabilir. [Azure Güvenlik Duvarı ile çıkış trafiğini kısıtla örneğinde](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall)bu örneğe bakın.

> [!IMPORTANT]
> UDR 'nin giden türü, yol tablosundaki NVA 'nın (ağ sanal gereç) 0.0.0.0/0 ve sonraki atlama hedefi için bir yol olmasını gerektirir.
> Yol tablosunun varsayılan bir 0.0.0.0/0 olması zaten Internet 'e ait bir genel IP 'ye sahip olmadığı için, bu yolun yalnızca bu yolu eklemeniz durumunda çıkış sağlayamayacak. AKS, Internet 'e işaret eden bir 0.0.0.0/0 yolu oluşturmayacağını doğrular, bunun yerine NVA veya Gateway, vb. olur. Bir UDR 'nin giden türü kullanılırken, *loadbalancer* türünde bir hizmet yapılandırılmadığı takdirde **gelen istekler** IÇIN yük dengeleyici genel IP adresi oluşturulmaz. Giden **istekler** için genel bir IP adresi, giden bir UDR türü ayarlandıysa aks tarafından hiçbir zaman oluşturulmaz.

## <a name="next-steps"></a>Sonraki adımlar

Bkz. [Azure ağ UDR 'ye genel bakış](../virtual-network/virtual-networks-udr-overview.md).

Bkz. [yol tablosu oluşturma, değiştirme veya silme](../virtual-network/manage-route-table.md).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
