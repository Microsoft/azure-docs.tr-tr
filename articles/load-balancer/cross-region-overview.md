---
title: Bölgeler arası yük dengeleyici (Önizleme)
titleSuffix: Azure Load Balancer
description: Azure Load Balancer için çapraz bölge yük dengeleyici katmanına genel bakış.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: 8e14b22895c4734f1efd8688a5b20c946422a080
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225634"
---
# <a name="cross-region-load-balancer-preview"></a>Bölgeler arası yük dengeleyici (Önizleme)

Azure Load Balancer yük dengeleyici ön ucuna, arka uç havuzu örneklerine ulaşan gelen trafiği dağıtır.

Azure Standart Load Balancer, coğrafi olarak yedekli HA senaryolarına olanak sağlayan bölgeler arası yük dengelemesini destekler:

* Birden çok bölgeden kaynaklanan gelen trafik.
* Bir sonraki en iyi bölgesel dağıtıma [hızlı genel yük devretme](#regional-redundancy) .
* Son [derece düşük gecikme süresine](#ultra-low-latency)sahip bölgeler arasında dağıtımı en yakın Azure bölgesine yükleyin.
* Tek bir uç noktanın arkasında [ölçeği artırma/azaltma](#ability-to-scale-updown-behind-a-single-endpoint) yeteneği.
* [Statik IP](#static-ip)
* [İstemci IP koruması](#client-ip-preservation)
* Öğrenme eğrisi olmadan [mevcut yük dengeleyici çözümünde derleme](#build-cross-region-solution-on-existing-azure-load-balancer)

> [!IMPORTANT]
> Çapraz bölge yük dengeleyici Şu anda önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bölgeler arası yük dengeleme, bölgesel standart yük dengeleyici olarak yüksek performanslı ve düşük gecikme süreleriyle aynı avantajları sunar. 

Bölgeler arası yük dengeleyicinizin ön uç IP yapılandırması statiktir ve [çoğu Azure](#participating-regions)bölgesinde tanıtılmış olur.

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="Bölgeler arası yük dengeleyici diyagramı." border="true":::

> [!NOTE]
> Çapraz bölge yük dengeleyicideki Yük Dengeleme kuralınızın arka uç bağlantı noktası, bölgesel standart yük dengeleyicideki Yük Dengeleme kuralı/gelen NAT kuralının ön uç bağlantı noktasıyla eşleşmelidir. 

### <a name="regional-redundancy"></a>Bölgesel artıklık

Mevcut yük dengeleyicilere küresel bir ön uç genel IP adresi ekleyerek bölgesel artıklığı yapılandırın. 

Bir bölge başarısız olursa, trafik sonraki en iyi sağlıklı bölgesel yük dengeleyiciye yönlendirilir.  

Çapraz bölge yük dengeleyicinin sistem durumu araştırması, her 20 saniyede bir kullanılabilirlik hakkında bilgi toplar. Bir bölgesel yük dengeleyici, kullanılabilirliğini 0 olarak bırakmazsa, çapraz bölge yük dengeleyici hatayı algılar. Bölgesel yük dengeleyici daha sonra dönüşten sonra alınır. 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="Genel bölge trafiği görünümü diyagramı." border="true":::

### <a name="ultra-low-latency"></a>Ultra düşük gecikme süresi

Coğrafi yakınlık yükü dengeleme algoritması, kullanıcılarınızın ve bölgesel dağıtımlarınızın coğrafi konumunu temel alır. 

Bir istemciden başlatılan trafik, en yakın bölge dağıtımına ulaşmak için Microsoft küresel ağ omurgasına ulaşan en yakın bölgeyi vuracaktır. 

Örneğin, Azure bölgelerindeki standart yük dengeleyiciler içeren bir çapraz bölge yük dengeleyiciye sahipsiniz:

* Batı ABD
* Kuzey Avrupa

Bir akış Seattle 'dan başlatılırsa trafik Batı ABD girer. Bu bölge Seattle 'dan en yakın katılımcı bölgesidir. Trafik, Batı ABD olan en yakın bölge yük dengeleyicisine yönlendirilir.

Azure bölgeler arası yük dengeleyici, yönlendirme kararı için coğrafi yakınlık Yük Dengeleme algoritmasını kullanır. 

Bölgesel yük dengeleyiciler için yapılandırılmış yük dağıtım modu, coğrafi yakınlık için birden çok bölgesel yük dengeleyici kullanıldığında son yönlendirme kararı oluşturmak için kullanılır.

Daha fazla bilgi için bkz. [Azure Load Balancer Dağıtım modunu yapılandırma](./load-balancer-distribution-mode.md).


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>Tek bir uç noktanın arkasında ölçeği artırma/azaltma yeteneği

Bir çapraz bölge yük dengeleyicinin küresel uç noktasını müşterilere kullanıma sunana zaman, genel uç noktanın arkasında kesinti olmadan bölgesel dağıtımlar ekleyebilir veya kaldırabilirsiniz. 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-ip"></a>Statik IP
Çapraz bölge yük dengeleyici statik bir genel IP ile gelir ve IP adresinin aynı kalmasını sağlar. Statik IP hakkında daha fazla bilgi edinmek için [buradan](../virtual-network/public-ip-addresses.md#allocation-method) okuyun

### <a name="client-ip-preservation"></a>İstemci IP koruması
Çapraz bölge yük dengeleyici, katman 4 geçişli bir ağ yükü dengeleyicidir. Bu geçişli, paketin orijinal IP 'sini korur.  Özgün IP, sanal makinede çalışan kod tarafından kullanılabilir. Bu koruma, bir IP adresine özgü mantığı uygulamanıza olanak tanır.

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>Mevcut Azure Load Balancer çapraz bölge çözümü oluştur
Bölgeler arası yük dengeleyicinin arka uç havuzu bir veya daha fazla bölgesel yük dengeleyiciyi içerir. 

Yüksek oranda kullanılabilir ve çapraz bölge dağıtımı için mevcut yük dengeleyici dağıtımlarınızı çapraz bölge yük dengeleyiciye ekleyin.

**Ana bölge** , bölgeler arası yük dengeleyicinin veya genel KATMANıN genel IP adresinin dağıtıldığı yerdir. Bu bölge trafiğin nasıl yönlendirildiğini etkilemez. Bir giriş bölgesi aşağı gittiğinde trafik akışı etkilenmez.

### <a name="home-regions"></a>Giriş bölgeleri
* Doğu ABD 2
* Batı ABD
* West Europe
* Güneydoğu Asya
* Central US
* Kuzey Avrupa
* Doğu Asya

> [!NOTE]
> Yalnızca bölge içi yük dengeleyiciyi veya genel IP 'yi, yukarıdaki 7 bölgeden birinde genel katmanda dağıtabilirsiniz.

Bir **katılım bölgesi** , yük dengeleyicinin genel genel IP 'nin kullanılabilir olduğu yerdir. 

Kullanıcı tarafından başlatılan trafik, Microsoft Core ağı üzerinden en yakın katılım bölgesine taşınır. 

Bölgeler arası yük dengeleyici, trafiği uygun bölgesel yük dengeleyiciye yönlendirir.

### <a name="participating-regions"></a>Katılan bölgeler
* Doğu ABD 
* West Europe 
* Orta ABD 
* Doğu ABD 2 
* Batı ABD 
* Kuzey Avrupa 
* Orta Güney ABD 
* Batı ABD 2 
* Güney Birleşik Krallık 
* Güneydoğu Asya 
* Orta Kuzey ABD 
* Doğu Japonya 
* Doğu Asya 
* Orta Batı ABD 
* Güneydoğu Avustralya 
* Doğu Avustralya 
* Orta Hindistan 

## <a name="limitations"></a>Sınırlamalar

* Bölgeler arası ön uç IP yapılandırması yalnızca genel kullanıma açıktır. Bir iç ön uç Şu anda desteklenmiyor.

* Bir çapraz bölgedeki yük dengeleyicinin arka uç havuzuna özel veya iç yük dengeleyici eklenemiyor 

* Çapraz bölge IPv6 ön uç IP yapılandırması desteklenmez. 

* Bir sistem durumu araştırması Şu anda yapılandırılamaz. Varsayılan bir sistem durumu araştırması, bölgesel yük dengeleyici ile ilgili kullanılabilirlik bilgilerini her 20 saniyede bir otomatik olarak toplar. 

* Azure Kubernetes hizmeti (AKS) ile tümleştirme Şu anda kullanılamıyor. Arka uçta AKS kümesi dağıtılan standart yük dengeleyiciye bir çapraz bölge yük dengeleyici dağıtırken bağlantı kaybı oluşur.

## <a name="pricing-and-sla"></a>Fiyatlandırma ve SLA
Bölgeler arası yük dengeleyici, standart yük dengeleyicinin [SLA 'sını](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) paylaşır.

 
## <a name="next-steps"></a>Sonraki adımlar

- Bir çapraz bölge yük dengeleyici oluşturmak için [Azure Portal kullanarak bir çapraz bölge yük dengeleyici oluşturun. öğreticiye](tutorial-cross-region-portal.md) bakın.
- Standart bölgesel yük dengeleyici oluşturmak için bkz. [Genel Standart yük dengeleyici oluşturma](quickstart-load-balancer-standard-public-portal.md) .
- [Azure Load Balancer](load-balancer-overview.md)hakkında daha fazla bilgi edinin.
