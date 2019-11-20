---
title: Azure Standart Load Balancer ve Kullanılabilirlik Alanları
titleSuffix: Azure Load Balancer
description: Bu öğrenme yoluyla Azure Standart Load Balancer ve Kullanılabilirlik Alanları ile çalışmaya başlayın.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 63a7db6e7078df978f47a6d53ea82df83c22c800
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076978"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Standard Load Balancer ve Kullanılabilirlik Bölgeleri

Azure Standart Load Balancer, [kullanılabilirlik alanları](../availability-zones/az-overview.md) senaryolarını destekler. Kaynakları bölgelere ayırarak ve bölgeler arasında dağıtarak uçtan uca senaryonuzun kullanılabilirliği iyileştirmek için Standart Load Balancer kullanabilirsiniz.  Kullanılabilirlik [alanlarını,](../availability-zones/az-overview.md) hangi bölgelerin kullanılabilirlik alanlarını desteklediğini ve diğer ilgili kavramları ve ürünleri hakkında rehberlik için gözden geçirin. Standart Load Balancer ile birlikte kullanılabilirlik alanları, birçok farklı senaryo oluşturabileceğiniz bir expantik ve esnek özellik kümesidir.  Bu [kavramları](#concepts) ve temel senaryo [tasarım kılavuzunu](#design)anlamak için bu belgeyi gözden geçirin.

>[!IMPORTANT]
>Bölgeye özgü bilgiler de dahil olmak üzere ilgili konular için [kullanılabilirlik alanları](../availability-zones/az-overview.md) gözden geçirin.

## <a name="concepts"></a>Load Balancer uygulanan Kullanılabilirlik Alanları kavramları

Load Balancer kaynakları ile gerçek altyapı arasında doğrudan ilişki yoktur; Load Balancer oluşturmak bir örnek oluşturmaz. Load Balancer kaynaklar, Azure 'un, oluşturmak istediğiniz senaryoya ulaşmak için önceden oluşturulmuş çok kiracılı altyapısını nasıl programtabileceği hakkında ifade ettiğiniz nesnelerdir.  Tek bir Load Balancer kaynağı birden çok kullanılabilirlik bölgesindeki altyapının programlamayı denetleyebileceğinden, bu, kullanılabilirlik alanları bağlamında önemli bir deyişle, bölgesel olarak yedekli bir hizmet bir müşteri bakış noktasından bir kaynak olarak görünür.  

Load Balancer kaynağın kendisi bölge ve hiçbir şekilde hiç olamaz.  Hem VNet hem de alt ağ her zaman bölgesel ve hiçbir zaman. Yapılandırabileceğiniz ayarların ayrıntı düzeyi, ön uç, kural ve arka uç havuzu tanımının her bir yapılandırması tarafından sınırlandırılır.

Kullanılabilirlik alanları bağlamında, bir Load Balancer kuralın davranışı ve özellikleri, bölgesel olarak yedekli veya zonal olarak açıklanmaktadır.  Bölgesel olarak yedekli ve bölgesel bir özelliğin bölge düzeyini betimleyen.  Load Balancer bağlamında, bölgesel olarak yedekli her zaman *birden çok bölge* ve ZGen, hizmeti *tek bir bölgeye*ayırma anlamına gelir.

Hem genel hem de dahili Load Balancer, bölgesel olarak yedekli ve zGenel senaryoları destekler ve her ikisi de gerektiğinde trafiği bölgelere (*çapraz bölge yük dengelemesi*) yönlendirebilir. 

### <a name="frontend"></a>Uçta

Load Balancer ön ucu, bir genel IP adresi kaynağına veya bir sanal ağ kaynağının alt ağı içindeki özel bir IP adresine başvuran bir ön uç IP yapılandırması.  Hizmetinizin açığa çıkarılabileceği yük dengeli uç noktayı oluşturur.

Bir Load Balancer kaynak, aynı anda bölgesel ve bölgesel olarak yedekli ön uçları içeren kurallar içerebilir. 

Bir bölge için genel bir IP kaynağı veya özel bir IP adresi garanti edildiğinde, bölgenin bölgesi (veya eksikliği) değişebilir olmaz.  Genel IP veya özel IP adresi ön ucu 'nın bölge düzeyini değiştirmek veya atlamak istiyorsanız, genel IP 'yi uygun bölgede yeniden oluşturmanız gerekir.  Kullanılabilirlik alanları birden çok ön uç için kısıtlamaları değiştirmez, bu beceriyle ilgili ayrıntılar için [Load Balancer için birden fazla](load-balancer-multivip-overview.md) ön uç gözden geçirin.

#### <a name="zone-redundant-by-default"></a>Varsayılan olarak bölge yedekli

Kullanılabilirlik alanları olan bir bölgede, Standart Load Balancer ön uç varsayılan olarak bölgesel olarak yedekli olur.  Bölgesel olarak yedekli tüm gelen veya giden akışların, tek bir IP adresi kullanılarak aynı anda bir bölgedeki birden çok kullanılabilirlik bölgesi tarafından sunulduğunu belirtir. DNS artıklık şemaları gerekli değildir. Tek bir ön uç IP adresi, bölge başarısızlığından sonra bölge hatasından bağımsız olarak tüm (etkilenmeyen) arka uç havuzu üyelerine ulaşmak için kullanılabilir. Bir veya daha fazla kullanılabilirlik bölgesi başarısız olabilir ve bölgedeki bir bölge sağlıklı kaldığı sürece veri yolu daha uzundur. Ön uç 'nin tek IP adresi birden çok kullanılabilirlik bölgesinde birden çok bağımsız altyapı dağıtımı tarafından aynı anda sunulur.  Bu, daha az veri yolu değildir, ancak yeniden denemeler veya yeniden oluşturma, bölge hatasından etkilenmeyen diğer bölgelerde başarılı olur.   

Aşağıdaki alıntı, genel IP 'nin genel bir IP adresi için ortak Standart Load Balancer kullanılacak genel IP adresini nasıl tanımlayacağınızı gösteren bir örnekdir. Yapılandırmanızda mevcut Kaynak Yöneticisi şablonları kullanıyorsanız, bu şablonlara **SKU** bölümünü ekleyin.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Aşağıdaki alıntı, iç Standart Load Balancer için bölgesel olarak yedekli bir ön uç IP adresinin nasıl tanımlanacağını gösteren bir örnekdir. Yapılandırmanızda mevcut Kaynak Yöneticisi şablonları kullanıyorsanız, bu şablonlara **SKU** bölümünü ekleyin.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Önceki alıntıları 'ler tamamlanmamış şablonlar değildir ancak kullanılabilirlik alanları özelliklerinin nasıl gösterileceğini göstermek için tasarlanmıştır.  Bu deyimleri şablonlarınıza eklemeniz gerekir.

#### <a name="optional-zone-isolation"></a>İsteğe bağlı bölge yalıtımı

Tek bir bölge için bir ön uç garantisini seçebilirsiniz ve bu da *Bölgesel ön*ucu olarak bilinir.  Bu, gelen veya giden akışın bir bölgedeki tek bir bölge tarafından hizmet verdiği anlamına gelir.  Ön uç paylaşımlarınız, bölgenin sistem durumuyla birlikte fada.  Veri yolu, garantide olduğu yerde yer alan bölgelerde hatalara göre etkilenmemiştir. Kullanılabilirlik alanı başına bir IP adresi göstermek için, bölgesel ön uçları kullanabilirsiniz.  

Ayrıca, her bir bölgedeki yük dengeli uç noktalar için de, ZGen ön uçlarını doğrudan kullanabilirsiniz. Ayrıca, her bölgeyi tek tek izlemek için her bölge yük dengeli uç nokta için de kullanabilirsiniz.  Ya da genel uç noktalar için, bunları [Traffic Manager](../traffic-manager/traffic-manager-overview.md) gıbı bir DNS yük dengelemesi ürünüyle tümleştirebilir ve tek bir DNS adı kullanabilirsiniz. Daha sonra istemci daha sonra bu DNS adına birden çok IP adresine çözümlenir.  

Bu kavramları karıştırmak istiyorsanız (aynı arka uç için bölgesel olarak yedekli ve bölgesel), [Azure Load Balancer için birden fazla](load-balancer-multivip-overview.md)ön eki gözden geçirin.

Genel Load Balancer ön ucu için, ilgili kural tarafından kullanılan ön uç IP yapılandırması tarafından başvurulan genel IP kaynağına bir *bölgeler* parametresi eklersiniz.

İç Load Balancer ön uç için, iç Load Balancer ön uç IP yapılandırmasına bir *bölgeler* parametresi ekleyin. Bölgesel ön ucu, Load Balancer alt ağdaki bir IP adresinin belirli bir bölgeye karşı sağlanmasına neden olur.

Aşağıdaki alıntı, kullanılabilirlik bölge 1 bir bölgesel standart genel IP adresi tanımlamaya yönelik bir örnekdir. Yapılandırmanızda mevcut Kaynak Yöneticisi şablonları kullanıyorsanız, bu şablonlara **SKU** bölümünü ekleyin.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Aşağıdaki alıntı, kullanılabilirlik Bölge 1 dahili Standart Load Balancer ön ucunun nasıl tanımlanacağını gösteren bir örnekdir. Yapılandırmanızda mevcut Kaynak Yöneticisi şablonları kullanıyorsanız, bu şablonlara **SKU** bölümünü ekleyin. Ayrıca, alt kaynak için ön uç IP yapılandırmasında **bölgeler** özelliğini tanımlayın.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Precelılar, tüm şablonlar değildir ancak kullanılabilirlik alanları özelliklerinin nasıl gösterileceğini göstermek için tasarlanmıştır.  Bu deyimleri şablonlarınıza eklemeniz gerekir.

### <a name="cross-zone-load-balancing"></a>Bölgeler arası yük dengeleme

Bölgeler arası yük dengeleme, Load Balancer herhangi bir bölgedeki arka uç uç noktasına ulaşma ve ön uç ve bölge özelliklerinden bağımsız olma olanağıdır.  Herhangi bir yük dengeleme kuralı, tüm kullanılabilirlik bölgelerinde veya bölgesel örneklerde arka uç örneğini hedefleyebilir.

Senaryonuzu, bir kullanılabilirlik bölgesi kavramını ifade eden bir şekilde oluşturmak için dikkatli olmanız gerekir. Örneğin, sanal makine dağıtımınızın tek bir bölgede veya birden çok bölgede garanti etmeniz ve zeli ön uç ve bölgesel arka uç kaynaklarını aynı bölgeye hizalamanız gerekir.  Kullanılabilirlik bölgelerini yalnızca bölgesel kaynaklarıyla karşılıklı olarak kullanıyorsanız, senaryo çalışır, ancak kullanılabilirlik alanları açısından açık bir hata moduna sahip olmayabilir. 

### <a name="backend"></a>Sunucusundan

Load Balancer sanal makine örnekleriyle birlikte kullanılabilir.  Bunlar tek başına, kullanılabilirlik kümelerine veya sanal makine ölçek kümelerine sahip olabilir.  Tek bir sanal ağdaki herhangi bir sanal makine örneği, bir bölgede veya hangi bölgenin garantide garanti edilip edilmediklerine bakılmaksızın arka uç havuzunun bir parçası olabilir.

Ön uç ve arka ucunuzu tek bir bölge ile hizalamak ve güvence altına almak istiyorsanız, yalnızca aynı bölgedeki sanal makineleri ilgili arka uç havuzuna yerleştirebilirsiniz.

Birden çok bölge genelinde sanal makinelere adres eklemek istiyorsanız, sanal makineleri birden fazla bölgeye aynı arka uç havuzuna yerleştirebilirsiniz.  Sanal Makine Ölçek Kümeleri kullanırken, bir veya daha fazla sanal makine ölçek kümesini aynı arka uç havuzuna yerleştirebilirsiniz.  Bu sanal makine ölçek kümelerinin her biri, tek veya birden çok bölgede olabilir.

### <a name="outbound-connections"></a>Giden bağlantılar

Aynı bölge yedekli ve bölgesel özellikleri [giden bağlantılar](load-balancer-outbound-connections.md)için geçerlidir.  Giden bağlantılar için kullanılan bölge yedekli genel IP adresi tüm bölgeler tarafından sunulur. Bir genel IP adresi, yalnızca bunun garanti ettiği bölge tarafından sunulur.  Giden bağlantı SNAT bağlantı noktası ayırmaları kalan bölge hataları ve senaryonuz, bölge hatasından etkilenmemesi durumunda giden SNAT bağlantısı sağlamaya devam edecektir.  Bu, bir akış etkilenen bir bölge tarafından sunulduysa, bu işlem için veya bağlantıların, bölgesel olarak yedekli senaryolar için yeniden kurulması gerekebilir.  Etkilenen bölgeler dışındaki bölgelerde yer alan akışlar etkilenmez.

SNAT bağlantı noktası ön ayırma algoritması, kullanılabilirlik alanları olmadan veya bunlarla aynı değildir.

### <a name="health-probes"></a>Sistem durumu araştırmaları

Mevcut durum araştırma tanımlarınız, kullanılabilirlik alanları olmayan gibi kalır.  Ancak sistem durumu modelini bir altyapı düzeyinde genişlettik. 

Bölgesel olarak yedekli ön uçları kullanırken Load Balancer, iç sistem durumu modelini her bir kullanılabilirlik bölgesinden bir sanal makinenin ulaşılabilirliğini bağımsız olarak araştırmasını ve müşteri müdahalesi olmadan başarısız olabilecek bölgelerde yolları kapatmayı sağlar.  Belirli bir yol, bir bölgenin Load Balancer altyapısından başka bir bölgedeki bir sanal makineye yoksa, Load Balancer bu hatayı algılayabilir ve önleyebilir. Bu sanal makineye erişebilen diğer bölgeler, sanal makineyi ilgili ön uçlarından sunmaya devam edebilir.  Sonuç olarak, hata olayları sırasında her bölge, uçtan uca hizmetinizin genel sistem durumunu korurken her bir bölgenin yeni akışların biraz farklı dağılımlarına sahip olabilir.

## <a name="design"></a>Tasarım konuları

Load Balancer, kullanılabilirlik alanları bağlamında özellikle esnektir. Bölgelere hizalamayı seçebilirsiniz veya her kural için bölge yedekli olarak seçebilirsiniz.  Daha yüksek kullanılabilirlik, artan karmaşıklık fiyatına gelebilir ve en iyi performans için kullanılabilirliği tasarlayabilmeniz gerekir.  Bazı önemli tasarım konularına göz atalım.

### <a name="automatic-zone-redundancy"></a>Otomatik bölge-artıklık

Load Balancer, bölge yedekli ön uç olarak tek bir IP 'nin olmasını kolaylaştırır. Bölgesel olarak yedekli bir IP adresi herhangi bir bölgede güvenli bir şekilde bir bölgesel kaynağı sunabilir ve bir bölgenin bölgede sağlıklı kaldığı sürece bir veya daha fazla bölge başarısızlığının üzerinde kalabilirler. Buna karşılık, bir bölgesel ön ucu hizmetin tek bir bölgeye bir azalmasıyla birlikte Fate 'yı ilgili bölge ile paylaşır.

Bölge yedekliliği, itless DataPath veya denetim düzlemi göstermez;  açıkça veri düzledir. Bölgesel olarak yedekli akışlar herhangi bir bölgeyi kullanabilir ve bir müşterinin akışları bir bölgedeki tüm iyi alanları kullanır. Bölge hatası durumunda, zaman içinde sağlıklı bölgeleri kullanan trafik akışları etkilenmez.  Bölge hatası sırasında bölge kullanan trafik akışları etkilenebilir, ancak uygulamalar kurtarabilir. Bu akışlar, Azure 'un bölge hatası etrafında yakınsadıktan sonra yeniden aktarım veya yeniden oluşturma sırasında bölge içindeki kalan sağlıklı bölgelerde devam edebilir.

### <a name="xzonedesign"></a>Çapraz bölge sınırları

Uçtan uca bir hizmetin bölgeleri nasıl kesiştiği hakkında daha fazla bilgi için, Fate 'yi tek bir bölge değil, muhtemelen birden çok bölge ile paylaşacağınızı anlamanız önemlidir.  Sonuç olarak, uçtan uca hizmetiniz, hiç türlü dağıtım dışı dağıtımlar üzerinde kullanılabilirlik kazanmayabilir.

Kullanılabilirlik alanları kullanılırken kullanılabilirlik kazançlarını null olarak belirleyen, istenmeyen bölgeler arası bağımlılıklardan kaçının.  Uygulamanız birden çok bileşenden oluşur ve bölge hatasına dayanıklı olmak istediğinizde, bir bölgenin başarısız olması durumunda yeterli kritik bileşen olup olmadığından emin olmak için dikkatli olmanız gerekir.  Örneğin, uygulamanız için tek bir kritik bileşen yalnızca, kalan bölge (ler) in dışında bir bölgede mevcutsa uygulamanızın tamamını etkileyebilir.  Ayrıca, bölge geri yüklemesini ve uygulamanızın nasıl yakınsama olacağını da göz önünde bulundurun. Uygulamanızın bölümlerinin arızalarıyla ilgili neden olma nedenlerinizi anlamanız gerekir. Daha sonra bazı önemli noktaları incelim ve belirli senaryonuza göz önünde bulundurabileceğiniz sorulara soru-yanıt olarak kullanalım.

- Uygulamanızın bir IP adresi ve yönetilen diske sahip bir sanal makine gibi iki bileşeni varsa ve bölge 1 ve bölge 2 ' de garanti edildiğinde bölge 1 başarısız olduğunda, bölge 1 başarısız olduğunda uçtan uca hizmetiniz devam etmez.  Potansiyel olarak tehlikeli bir hata modu oluşturmadığınızı tam olarak anlamadığınız müddetçe, Dilimsiz senaryolarla çapraz bölgeler oluşturmayın.  Bu senaryonun esneklik sağlamasına izin verilir.

- Uygulamanızın bir IP adresi ve yönetilen diske sahip bir sanal makine gibi iki bileşeni varsa ve bunlar sırasıyla bölge yedekli ve bölge 1 olarak garanti altına alıyorsa, uçtan uca hizmetiniz bölge 2, bölge 3 veya her ikisi de bölge 1 başarısız olmadığı takdirde bölge arızasından kaçınacaktır.  Ancak, tüm gözlerinizin ön ucu için ulaşılabilirlik durumunda hizmetinizin sistem durumu hakkında bir neden özelliği kaybedersiniz.  Daha kapsamlı bir sistem durumu ve kapasite modeli geliştirmeyi düşünün.  İçgörüler ve yönetilebilirlik ' i genişletmek için, bölgesel olarak yedekli ve bölgesel kavramlarını birlikte kullanabilirsiniz.

- Uygulamanızda, bölgesel olarak yedekli Load Balancer ön ucu ve üç bölgede bir çapraz bölge sanal makine ölçek kümesi gibi iki bileşen varsa, bölgelerdeki kaynaklarınız hatadan etkilenmeyen kaynaklarınız kullanılabilir olacaktır, ancak uçtan uca hizmet kapasiteniz azaltılabilir bölge hatası sırasında. Bir altyapı perspektifinden, dağıtımınız bir veya daha fazla bölge hatasından devam edebilir ve bu, aşağıdaki soruları oluşturur:
  - Uygulamanızın bu hatalara ve düşürülmüş kapasiteye ilişkin nasıl neden olduğunu anlamış musunuz?
  - Gerekirse bir bölge çiftine yük devretmeyi zorlamak için hizmetinize korumalar olması gerekiyor mu?
  - Böyle bir senaryoyu nasıl izleyebilir, algılayabilir ve azaltabilirsiniz? Uçtan uca hizmet performanlarınızın izlenmesini artırmak için Standart Load Balancer tanılamayı kullanabilirsiniz. Nelerin kullanılabilir olduğunu ve tüm resim için genişletmede nelerin gerekli olabileceğini düşünün.

- Bölgeler, hataların daha kolay anlaşılır ve dahil olmasını sağlayabilir.  Ancak, bölge hatası, zaman aşımları, denemeler ve geri alma algoritmaları gibi kavramlara geldiğinde diğer hatalardan farklı değildir. Azure Load Balancer, bölgesel olarak yedekli yollar sağlar ve hızlı bir şekilde kurtarmaya çalışır, ancak bir paket düzeyinde gerçek zamanlı olarak yeniden aktarımlar veya reestablishments, bir hatanın onın ve kesil. Yük Dengeleme şemanızın devam edecek, ancak şunları planlamanız gerekir:
  - Bir bölge başarısız olduğunda, uçtan uca hizmetiniz bunu anlamış olur ve durum kaybolduysa nasıl kurtarılır?
  - Bir bölge döndürüldüğünde, uygulamanız güvenle nasıl yakınsama yapılacağını anlasın mı?

Uygulamanızın hata senaryolarıyla olan dayanıklılığını artırmak için [Azure bulut tasarım modellerini](https://docs.microsoft.com/azure/architecture/patterns/) gözden geçirin.

### <a name="zonalityguidance"></a>Bölgesel olarak yedekli ve ZGen karşılaştırması

Bölgesel olarak yedekli bir seçenek ve hizmet için tek bir IP adresi ile aynı zamanda esnek seçeneğinde, bölgesel olarak yedekli bir seçeneğe sahip bir kolaylık sağlayabilir.  Bu, karmaşıklığı azaltabilir.  Bölgesel olarak yedekli bölgeler arasında taşınabilirliği de vardır ve herhangi bir bölgedeki kaynaklarda güvenli bir şekilde kullanılabilir.  Ayrıca, kullanılabilirlik alanları olmayan bölgelerde daha ileride prova olur. Bu, bir bölge kullanılabilirlik alanları kazandıktan sonra gerekli değişiklikleri sınırlayabilir.  Bölgesel olarak yedekli bir IP adresi veya ön uç için yapılandırma sözdizimi, kullanılabilirlik alanları olmadan de dahil olmak üzere tüm bölgelerde başarılı olur: bölgenin bölgeler: özelliği içinde bir bölge belirtilmedi.

Zonal, bölge için açık bir garanti sağlayabilir ve bu da bölgenin sistem durumuyla birlikte açık bir şekilde paylaşım yapabilir. Bir IP adresi ön ucu veya bölgesel iç Load Balancer ön ucu ile Load Balancer kuralı oluşturmak, özellikle iliştirilen kaynağınız aynı bölgedeki bir zeli sanal makinedir.  Ya da uygulamanız, bir kaynağın bir süre önce bulunduğu bölge hakkında açık bilgi gerektiriyor ve ayrı bölgelerde açık bir şekilde kullanılabilirlik hakkında bir neden olmak istiyorsunuz.  Bölgeler arasında dağıtılmış uçtan uca bir hizmet için birden çok zikzak ön eki kullanıma sunabilirsiniz (yani, birden fazla sanal makine ölçek kümesi için bölge başına düşen ön uçlar).  Ve, genel IP adresleri ise, bu birden fazla bölgesel ön uçlarınızın [Traffic Manager](../traffic-manager/traffic-manager-overview.md)hizmetinizi kullanıma sunmak için kullanabilirsiniz.  Ya da, üçüncü taraf izleme çözümleri aracılığıyla bölge sistem durumu ve Performans öngörüleri elde etmek ve genel hizmeti bölgesel olarak yedekli bir ön uç ile kullanıma sunmak için birden fazla bölgesel ön ucu kullanabilirsiniz. Yalnızca aynı bölgeye hizalanmış ve büyük olasılıkla olası olabilecek çapraz bölge senaryolarına karşı, yalnızca Zine ön uçlar içeren bölgesel kaynaklarına sahip olmanız gerekir.  Tek tek kaynaklar yalnızca kullanılabilirlik bölgelerinin mevcut olduğu bölgelerde bulunur.

Hizmetin, hizmet mimarisini bilmeden daha iyi bir seçim olduğu konusunda genel bir rehberlik yoktur.  Uygulamanızın hata senaryolarıyla olan dayanıklılığını artırmak için [Azure bulut tasarım modellerini](https://docs.microsoft.com/azure/architecture/patterns/) gözden geçirin.

## <a name="limitations"></a>Sınırlamalar

- Veri düzlemi tamamen bölge yedekli olsa da (ZGen garantisi belirtilmediği sürece), denetim düzlemi işlemleri tam olarak bölgesel olarak yedekli değildir.

## <a name="next-steps"></a>Sonraki adımlar
- [Kullanılabilirlik alanları](../availability-zones/az-overview.md) hakkında daha fazla bilgi edinin
- [Standart Yük Dengeleyici](load-balancer-standard-overview.md) hakkında daha fazla bilgi edinin
- Bir [bölge Içinde VM 'lerin yükünü bir standart Load Balancer kullanarak bir bölgede yük ön uç ile yükleme](load-balancer-standard-public-zonal-cli.md) hakkında bilgi edinin
- Bölgesel olarak [yedekli bir ön uç ile standart Load Balancer kullanarak sanal makinelerin bölge üzerinden yük dengelenmesi](load-balancer-standard-public-zone-redundant-cli.md) hakkında bilgi edinin
- Uygulamanızın hata senaryolarıyla olan dayanıklılığını artırmak için [Azure bulut tasarım desenleri](https://docs.microsoft.com/azure/architecture/patterns/) hakkında bilgi edinin.
