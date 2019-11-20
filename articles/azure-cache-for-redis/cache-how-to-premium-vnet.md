---
title: Redsıs için Premium Azure önbelleği için bir sanal ağ yapılandırma
description: Redsıs örnekleri için Premium katman Azure önbelleğiniz için sanal ağ desteği oluşturma ve yönetme hakkında bilgi edinin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: yegu
ms.openlocfilehash: b2ddac9439183321691104d4eedccb0c971d19c9
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129406"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Redsıs için Premium Azure önbelleği için sanal ağ desteğini yapılandırma
Redin için Azure önbelleğinde, kümeleme, kalıcılık ve sanal ağ desteği gibi Premium katman özellikleri de dahil olmak üzere, önbellek boyutu ve özellikleri seçimine esneklik sağlayan farklı önbellek teklifleri vardır. VNet, buluttaki özel bir ağ. Redsıs örneği için bir Azure önbelleği bir sanal ağ ile yapılandırıldığında, bu, genel olarak adreslenebilir değildir ve yalnızca VNet içindeki sanal makineler ve uygulamalardan erişilebilir. Bu makalede, Redsıs örneği için Premium bir Azure önbelleği için sanal ağ desteğinin nasıl yapılandırılacağı açıklanır.

> [!NOTE]
> Redsıs için Azure önbelleği hem klasik hem de Kaynak Yöneticisi sanal ağları destekler.
> 
> 

Diğer Premium önbellek özellikleri hakkında daha fazla bilgi için bkz. [Redsıs Premium katmanı Için Azure önbelleğine giriş](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Neden VNet?
[Azure sanal ağ (VNet)](https://azure.microsoft.com/services/virtual-network/) dağıtımı, Azure önbelleğiniz için gelişmiş güvenlik ve yalıtımın yanı sıra alt ağlar, erişim denetim ilkeleri ve diğer özellikler için erişimi daha da kısıtlamak sağlar.

## <a name="virtual-network-support"></a>Sanal ağ desteği
Sanal ağ (VNet) desteği, önbellek oluşturma sırasında **redsıs dikey penceresinde yeni Azure önbelleğinde** yapılandırılır. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Premium fiyatlandırma katmanını seçtikten sonra önbelleğiniz ile aynı abonelikte ve konumda bulunan bir VNet seçerek Redsıs VNet tümleştirmesini yapılandırabilirsiniz. Yeni bir VNet kullanmak için, [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/manage-virtual-network.md#create-a-virtual-network) veya [Azure Portal bir sanal ağ oluşturma (klasik)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) içindeki adımları izleyerek oluşturun ve ardından Premium önbelleğinizi oluşturmak ve yapılandırmak üzere **Redsıs dikey penceresinde yeni Azure önbelleğine** geri dönün.

VNet 'i yeni önbelleğiniz için yapılandırmak için, **yeni Azure önbelleğinde redin** dikey penceresinde **sanal ağ ' a** tıklayın ve açılan listeden istediğiniz VNET ' i seçin.

![Sanal ağ][redis-cache-vnet]

**Alt ağ** açılan listesinden istediğiniz alt ağı seçin ve Istenen **statik IP adresini**belirtin. Klasik VNet kullanıyorsanız **STATIK IP adresi** alanı isteğe bağlıdır ve hiçbiri belirtilmemişse seçili alt ağdan bir tane seçilir.

> [!IMPORTANT]
> Redsıs için Azure önbelleğinin bir Kaynak Yöneticisi VNet 'e dağıtılmasında, önbelleğin Redsıs örnekleri için Azure önbelleği dışında başka hiçbir kaynak içermeyen bir ayrılmış alt ağda olması gerekir. Redsıs için Azure önbelleğini diğer kaynakları içeren bir alt ağa bir Kaynak Yöneticisi VNet 'e dağıtmak için bir girişimde bulunuldu, dağıtım başarısız olur.
> 
> 

![Sanal ağ][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure, bazı IP adreslerini her alt ağ içinde ayırır ve bu adresler kullanılamaz. Alt ağların ilk ve son IP adresleri protokol uyumu için ayrılmıştır ve Azure hizmetleri için kullanılan üç adres daha vardır. Daha fazla bilgi için bkz. [Bu alt AĞLARDAKI IP adreslerini kullanma konusunda herhangi bir kısıtlama var mı?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Azure VNET altyapısı tarafından kullanılan IP adreslerine ek olarak, alt ağdaki her reddo örneği, yük dengeleyici için bir parça başına iki IP adresi ve bir ek IP adresi kullanır. Kümelenmemiş bir önbelleğin bir parça olduğu kabul edilir.
> 
> 

Önbellek oluşturulduktan sonra, **Kaynak menüsünden** **sanal ağ ' a** tıklayarak VNET 'in yapılandırmasını görüntüleyebilirsiniz.

![Sanal ağ][redis-cache-vnet-info]

VNet kullanırken Redsıs örneği için Azure önbelleğinize bağlanmak için aşağıdaki örnekte gösterildiği gibi bağlantı dizesinde önbelleğinizin ana bilgisayar adını belirtin:

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-cache-for-redis-vnet-faq"></a>Redsıs VNet için Azure önbelleği hakkında SSS
Aşağıdaki liste, Redsıs ölçeklendirmesi için Azure önbelleği hakkında sık sorulan soruların yanıtlarını içerir.

* Redsıs ve VNET 'ler için Azure önbelleğindeki bazı yaygın yanlış yapılandırma sorunları nelerdir?
* [Önbelleğim VNET 'te çalıştığını nasıl doğrulayabilirim?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Bir VNET 'te Redsıs için Azure Önbelleğim 'e bağlanmaya çalışırken, neden uzak sertifika geçersiz olduğunu belirten bir hata alıyorum?
* [Standart veya temel önbellek ile sanal ağları kullanabilir miyim?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Bazı alt ağlardaki Reda için Azure önbelleği neden başarısız oluyor, ancak başkaları tarafından kullanılamaz mi?
* [Alt ağ adres alanı gereksinimleri nelerdir?](#what-are-the-subnet-address-space-requirements)
* [VNET 'te önbellek barındırırken tüm önbellek özellikleri çalışıyor mu?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Redsıs ve VNET 'ler için Azure önbelleğindeki bazı yaygın yanlış yapılandırma sorunları nelerdir?
Redin için Azure önbelleği bir sanal ağda barındırılıyorsa, aşağıdaki tablolardaki bağlantı noktaları kullanılır. 

>[!IMPORTANT]
>Aşağıdaki tablolardaki bağlantı noktaları engellenirse, önbellek düzgün çalışmayabilir. Bu bağlantı noktalarından biri veya daha fazlası engelleniyorsa, bir sanal ağda redin için Azure önbelleği kullanılırken en sık karşılaşılan yanlış yapılandırma sorunu vardır.
> 
> 

- [Giden bağlantı noktası gereksinimleri](#outbound-port-requirements)
- [Gelen bağlantı noktası gereksinimleri](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Giden bağlantı noktası gereksinimleri

Dokuz giden bağlantı noktası gereksinimi vardır.

- İnternet 'e giden tüm bağlantılar, bir istemcinin şirket içi denetim cihazından yapılabilir.
- Bağlantı noktası sayısı, Azure depolama ve Azure DNS hizmet verme ile trafiği Azure uç noktalarına yönlendirmekte.
- Kalan bağlantı noktası aralıkları ve iç redin alt ağ iletişimleri için. İç Redın alt ağ iletişimleri için alt ağ NSG kuralları gerekmez.

| Bağlantı noktaları | Yön | Aktarım Protokolü | Amaç | Yerel IP | Uzak IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Giden |TCP |Azure depolama/PKI (Internet) üzerinde redsıs bağımlılıkları | (Redsıs alt ağı) |* |
| 443 | Giden | TCP | Azure Key Vault redsıs bağımlılığı | (Redsıs alt ağı) | AzureKeyVault <sup>1</sup> |
| 53 |Giden |TCP/UDP |DNS 'de redsıs bağımlılıkları (Internet/VNet) | (Redsıs alt ağı) | 168.63.129.16 ve 169.254.169.254 <sup>2</sup> ve alt ağ <sup>3</sup> için özel DNS sunucusu |
| 8443 |Giden |TCP |Redsıs iç iletişimleri | (Redsıs alt ağı) | (Redsıs alt ağı) |
| 10221-10231 |Giden |TCP |Redsıs iç iletişimleri | (Redsıs alt ağı) | (Redsıs alt ağı) |
| 20226 |Giden |TCP |Redsıs iç iletişimleri | (Redsıs alt ağı) |(Redsıs alt ağı) |
| 13000-13999 |Giden |TCP |Redsıs iç iletişimleri | (Redsıs alt ağı) |(Redsıs alt ağı) |
| 15000-15999 |Giden |TCP |Redsıs ve coğrafi çoğaltma için dahili iletişimler | (Redsıs alt ağı) |(Redsıs alt ağı) (Coğrafi çoğaltma eş alt ağı) |
| 6379-6380 |Giden |TCP |Redsıs iç iletişimleri | (Redsıs alt ağı) |(Redsıs alt ağı) |

<sup>1</sup> ' AzureKeyVault ' hizmet etiketini Kaynak Yöneticisi ağ güvenlik grupları ile birlikte kullanabilirsiniz.

<sup>2</sup> Microsoft 'un sahip olduğu bu IP adresleri, Azure DNS hizmet veren ana bilgisayar VM 'sini ele almak için kullanılır.

özel DNS sunucusu olmayan alt ağlar için <sup>3</sup> veya özel DNS 'yi yoksayarak daha yeni redsıs önbellekler gerekmez.

#### <a name="geo-replication-peer-port-requirements"></a>Coğrafi çoğaltma eş bağlantı noktası gereksinimleri

Azure sanal ağlarında bulunan önbellekler arasında coğrafi çoğaltma kullanıyorsanız, lütfen tüm çoğaltma bileşenlerinin her iki önbellekte gelen ve giden yönlerdeki tüm alt ağ için 15000-15999 bağlantı noktalarının engellemesini kaldırmanız gerektiğini unutmayın. alt ağda, gelecekteki coğrafi Yük devretme durumunda bile birbirleriyle doğrudan iletişim kurabilir.

#### <a name="inbound-port-requirements"></a>Gelen bağlantı noktası gereksinimleri

Sekiz gelen bağlantı noktası aralığı gereksinimi vardır. Bu aralıklardaki gelen istekler, aynı VNET 'te barındırılan diğer hizmetlerden veya Redsıs alt ağı iletişimlerinde iç yollardır.

| Bağlantı noktaları | Yön | Aktarım Protokolü | Amaç | Yerel IP | Uzak IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Gelen |TCP |Redsıs ile istemci iletişimi, Azure Yük Dengeleme | (Redsıs alt ağı) | (Redsıs alt ağı), sanal ağ, Azure Load Balancer <sup>1</sup> |
| 8443 |Gelen |TCP |Redsıs iç iletişimleri | (Redsıs alt ağı) |(Redsıs alt ağı) |
| 8500 |Gelen |TCP/UDP |Azure Yük Dengelemesi | (Redsıs alt ağı) |Azure Load Balancer |
| 10221-10231 |Gelen |TCP |Redsıs iç iletişimleri | (Redsıs alt ağı) |(Redsıs alt ağı), Azure Load Balancer |
| 13000-13999 |Gelen |TCP |Redsıs kümelerine istemci iletişimi, Azure Yük Dengelemesi | (Redsıs alt ağı) |Sanal ağ, Azure Load Balancer |
| 15000-15999 |Gelen |TCP |Redsıs kümelerine istemci iletişimi, Azure Yük Dengelemesi ve coğrafi çoğaltma | (Redsıs alt ağı) |Sanal ağ, Azure Load Balancer, (coğrafi çoğaltma eş alt ağı) |
| 16001 |Gelen |TCP/UDP |Azure Yük Dengelemesi | (Redsıs alt ağı) |Azure Load Balancer |
| 20226 |Gelen |TCP |Redsıs iç iletişimleri | (Redsıs alt ağı) |(Redsıs alt ağı) |

<sup>1</sup> NSG kurallarını yazmak Için ' AzureLoadBalancer ' (Kaynak Yöneticisi) hizmet etiketini (veya klasik için ' AZURE_LOADBALANCER ') kullanabilirsiniz.

#### <a name="additional-vnet-network-connectivity-requirements"></a>Ek VNET ağ bağlantısı gereksinimleri

Redsıs için Azure önbelleği için bir sanal ağda karşılanmamış olabilecek ağ bağlantısı gereksinimleri vardır. Redo için Azure Cache, bir sanal ağ içinde kullanıldığında aşağıdaki öğelerin tümünün düzgün çalışmasını gerektirir.

* Dünya çapındaki Azure depolama uç noktalarına giden ağ bağlantısı. Bu, Redsıs örneği için Azure önbelleği ile aynı bölgede bulunan uç noktaları ve **diğer** Azure bölgelerinde bulunan depolama uç noktalarını içerir. Azure depolama uç noktaları şu DNS etki alanları altında çözümlenir: *Table.Core.Windows.net*, *BLOB.Core.Windows.net*, *Queue.Core.Windows.net*ve *File.Core.Windows.net*. 
* *OCSP.msocsp.com*, *mscrl.Microsoft.com*ve *CRL.Microsoft.com*giden ağ bağlantısı. Bu bağlantı, SSL işlevselliğini desteklemek için gereklidir.
* Sanal ağın DNS yapılandırması, önceki noktalarda bahsedilen tüm uç noktaları ve etki alanlarını çözebilme yeteneğine sahip olmalıdır. Bu DNS gereksinimleri, sanal ağ için yapılandırılmış ve korunan geçerli bir DNS altyapısının sağlanması sağlanarak karşılanacaktır.
* Aşağıdaki Azure Izleme uç noktalarına giden ağ bağlantısı: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net , shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Önbelleğim VNET 'te çalıştığını nasıl doğrulayabilirim?

>[!IMPORTANT]
>VNET 'te barındırılan Redsıs örneği için bir Azure önbelleğine bağlanırken, önbellek istemcileriniz aynı VNET 'te veya aynı Azure bölgesinde sanal VNET eşlemesi etkinleştirilmiş bir sanal ağda olmalıdır. Genel Sanal Ağ Eşleme Şu anda desteklenmiyor. Buna tüm test uygulamaları veya tanılama ile ping araçları dahildir. İstemci uygulamasının nerede barındırıldığından bağımsız olarak, ağ güvenlik gruplarının, istemcinin ağ trafiğinin Redsıs örneğine erişmesine izin verilecek şekilde yapılandırılması gerekir.
>
>

Bağlantı noktası gereksinimleri önceki bölümde açıklandığı gibi yapılandırıldıktan sonra, aşağıdaki adımları gerçekleştirerek önbelleğinizin çalıştığını doğrulayabilirsiniz.

- Tüm önbellek düğümlerini [yeniden başlatın](cache-administration.md#reboot) . Tüm gerekli önbellek bağımlılıklarına ulaşılamadığından ( [gelen bağlantı noktası gereksinimleri](cache-how-to-premium-vnet.md#inbound-port-requirements) ve [giden bağlantı noktası gereksinimleri](cache-how-to-premium-vnet.md#outbound-port-requirements)bölümünde belirtildiği gibi), önbellek başarıyla yeniden başlatılabilir.
- Önbellek düğümleri yeniden başlatıldıktan sonra (Azure portal önbellek durumu tarafından raporlanarak), aşağıdaki testleri gerçekleştirebilirsiniz:
  - [tcpıng](https://www.elifulkerson.com/projects/tcping.php)kullanarak önbellek uç noktasına (6380 numaralı bağlantı noktasını kullanarak) önbellek ile aynı VNET içinde olan bir makineden ping gönderin. Örneğin:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    `tcping` aracı bağlantı noktasının açık olduğunu bildirirse, önbellek, VNET 'teki istemcilerden bağlantı için kullanılabilir.

  - Test etmenin başka bir yolu da, önbelleğe bağlanan ve önbellekten bazı öğeleri ekleyen ve alan bir test önbelleği istemcisi (StackExchange. Redsıs kullanan basit bir konsol uygulaması olabilir) oluşturmaktır. Örnek istemci uygulamasını, önbellek ile aynı VNET 'teki bir sanal makineye yükler ve önbelleğe bağlantıyı doğrulamak için çalıştırın.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Bir VNET 'te Redsıs için Azure Önbelleğim 'e bağlanmaya çalışırken, neden uzak sertifika geçersiz olduğunu belirten bir hata alıyorum?

Bir sanal ağ içinde redin için bir Azure önbelleğine bağlanmaya çalışırken şöyle bir sertifika doğrulama hatası görürsünüz:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Nedeni, ana bilgisayara IP adresiyle bağlanıyorsunuz olabilir. Ana bilgisayar adı kullanmanızı öneririz. Diğer bir deyişle, aşağıdakileri kullanın:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Aşağıdaki bağlantı dizesine benzer IP adresini kullanmaktan kaçının:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

DNS adını çözemezseniz, bazı istemci kitaplıkları StackExchange. Redsıs istemcisi tarafından belirtilen `sslHost` gibi yapılandırma seçeneklerini içerir. Bu, sertifika doğrulama için kullanılan ana bilgisayar adını geçersiz kılmanızı sağlar. Örneğin:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Standart veya temel önbellek ile sanal ağları kullanabilir miyim?
VNET 'ler yalnızca Premium önbellekler ile kullanılabilir.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Bazı alt ağlardaki Reda için Azure önbelleği neden başarısız oluyor, ancak başkaları tarafından kullanılamaz mi?
Redsıs for Kaynak Yöneticisi VNet 'e yönelik bir Azure önbelleği dağıtıyorsanız, önbelleğin başka kaynak türü içermeyen bir ayrılmış alt ağda olması gerekir. Redsıs için Azure önbelleğini diğer kaynakları içeren bir Kaynak Yöneticisi VNet alt ağına dağıtmaya yönelik bir girişim yapılırsa, dağıtım başarısız olur. Redsıs için yeni bir Azure önbelleği oluşturabilmeniz için, alt ağ içindeki mevcut kaynakları silmeniz gerekir.

Birden çok kaynak türünü klasik VNet 'e, yeterli sayıda IP adresiniz varsa dağıtabilirsiniz.

### <a name="what-are-the-subnet-address-space-requirements"></a>Alt ağ adres alanı gereksinimleri nelerdir?
Azure, bazı IP adreslerini her alt ağ içinde ayırır ve bu adresler kullanılamaz. Alt ağların ilk ve son IP adresleri protokol uyumu için ayrılmıştır ve Azure hizmetleri için kullanılan üç adres daha vardır. Daha fazla bilgi için bkz. [Bu alt AĞLARDAKI IP adreslerini kullanma konusunda herhangi bir kısıtlama var mı?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Azure VNET altyapısı tarafından kullanılan IP adreslerine ek olarak, alt ağdaki her reddo örneği, yük dengeleyici için bir parça başına iki IP adresi ve bir ek IP adresi kullanır. Kümelenmemiş bir önbelleğin bir parça olduğu kabul edilir.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>VNET 'te önbellek barındırırken tüm önbellek özellikleri çalışıyor mu?
Önbelleğiniz bir sanal ağın parçası olduğunda, yalnızca VNET 'teki istemciler önbelleğe erişebilir. Sonuç olarak, aşağıdaki önbellek yönetimi özellikleri şu anda çalışmaz.

* Redsıs konsolu-Redsıs konsolu, sanal ağ dışında bir yerel tarayıcınızda çalıştığından, önbelleğinize bağlanamaz.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Redsıs için Azure Cache ile ExpressRoute kullanma

Müşteriler, [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) bağlantı hattını sanal ağ altyapısına bağlanarak şirket Içi ağınızı Azure 'a genişletmelerini sağlayabilir. 

Varsayılan olarak, yeni oluşturulan bir ExpressRoute devresi, sanal ağ üzerinde Zorlamalı tünel (varsayılan bir yol 0.0.0.0/0) gerçekleştirmez. Sonuç olarak, doğrudan VNET 'ten giden Internet bağlantısına izin verilir ve istemci uygulamaları Redsıs için Azure önbelleği dahil diğer Azure uç noktalarına bağlanabilir.

Bununla birlikte, genel bir müşteri yapılandırması, giden Internet trafiğini şirket içi akışa yönelden zorlayan Zorlamalı tünel oluşturma (varsayılan rota tanıtma) kullanmaktır. Bu trafik akışı Reda için Azure Cache ile bağlantıyı keser ve ardından, redin örneği için Azure önbelleğinin bağımlılıklarıyla iletişim kuramaması gibi şirket içi trafik engellenir.

Çözüm, redin için Azure önbelleğini içeren alt ağda bir (veya daha fazla) Kullanıcı tanımlı yol (UDRs) tanımlamak içindir. UDR, varsayılan yol yerine kabul edilecek alt ağa özgü yolları tanımlar.

Mümkünse, aşağıdaki yapılandırmanın kullanılması önerilir:

* ExpressRoute yapılandırması 0.0.0.0/0 duyurur ve varsayılan olarak, Şirket içindeki tüm giden trafiği tünellere zorlar.
* Reds için Azure önbelleğini içeren alt ağa uygulanan UDR, genel İnternet 'e TCP/IP trafiği için çalışan bir rota ile 0.0.0.0/0 tanımlar; Örneğin, bir sonraki atlama türünü ' Internet ' olarak ayarlayarak.

Bu adımların Birleşik etkisi, alt ağ düzeyi UDR 'nin ExpressRoute zorlamalı tünelden öncelikli olmasını sağlar ve bu sayede redin için Azure önbelleğinden giden Internet erişimi sağlar.

ExpressRoute kullanarak bir şirket içi uygulamadan Redsıs örneği için Azure önbelleğine bağlanma, performans nedenleriyle tipik bir kullanım senaryosu değildir (Redsıs istemcileri için en iyi performans için Azure önbelleğinin Redsıs için Azure önbelleğiyle aynı bölgede olması gerekir) .

>[!IMPORTANT] 
>Bir UDR 'de tanımlanan yolların, ExpressRoute yapılandırması tarafından tanıtılan tüm yollarla öncelikli **olması gerekir** . Aşağıdaki örnek, büyük 0.0.0.0/0 adres aralığını kullanır ve bu nedenle daha belirli adres aralıkları kullanılarak yol tanıtımlarının yanlışlıkla geçersiz kılınması olabilir.

>[!WARNING]  
>Redsıs için Azure önbelleği, **genel eşleme yolundan özel eşleme yoluna yönelik yolların yanlışlıkla çapraz bir şekilde tanıtıldığı**ExpressRoute yapılandırmalarında desteklenmez. Ortak eşleme yapılandırılmış ExpressRoute yapılandırmalarında, büyük bir Microsoft Azure IP adresi aralığı kümesi için Microsoft 'tan yol tanıtımları alın. Bu adres aralıkları özel eşleme yolunda yanlış bir şekilde bildiriliyorsa, sonuç Redsıs örneği için Azure önbelleğindeki tüm giden ağ paketlerinin bir müşterinin Şirket içi ağına yanlışlıkla zorla tünel oluşturmasını sağlar gerekli. Bu ağ akışı Redsıs için Azure önbelleğini keser. Bu sorunun çözümü, genel eşleme yolundan özel eşleme yoluna yönelik çapraz reklam yollarını durdurmaktır.


Bu [genel bakışta](../virtual-network/virtual-networks-udr-overview.md)Kullanıcı tanımlı yollarla ilgili arka plan bilgileri bulabilirsiniz.

ExpressRoute hakkında daha fazla bilgi için bkz. [ExpressRoute teknik genel bakış](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla Premium önbellek özelliği kullanmayı öğrenin.

* [Redsıs Premium katmanı için Azure önbelleğine giriş](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
