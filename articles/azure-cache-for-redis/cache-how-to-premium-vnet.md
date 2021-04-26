---
title: Redsıs örneği için sanal ağ-Premium katmanı Azure önbelleği yapılandırma
description: Redsıs örneği için Premium katmanlı Azure önbelleğiniz için sanal ağ desteği oluşturma ve yönetme hakkında bilgi edinin
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 94bbb9bb683f40d44d6649802b66bda6feeee218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375281"
---
# <a name="configure-virtual-network-support-for-a-premium-azure-cache-for-redis-instance"></a>Redsıs örneği için Premium Azure önbelleği için sanal ağ desteğini yapılandırma

[Azure sanal ağ](https://azure.microsoft.com/services/virtual-network/) dağıtımı, erişimi daha fazla kısıtlamak için alt ağlar, erişim denetim ilkeleri ve diğer özelliklerle birlikte gelişmiş güvenlik ve yalıtım özellikleri sağlar. Redsıs örneği için bir Azure önbelleği bir sanal ağ ile yapılandırıldığında, genel olarak adreslenebilir ve yalnızca sanal ağ içindeki sanal makineler ve uygulamalardan erişilebilir. Bu makalede, Redsıs örneği için Premium katmanlı bir Azure önbelleği için sanal ağ desteğinin nasıl yapılandırılacağı açıklanır.

> [!NOTE]
> Redsıs için Azure Cache, hem klasik dağıtım modelini hem de Azure Resource Manager sanal ağları destekler.
> 

## <a name="set-up-virtual-network-support"></a>Sanal ağ desteğini ayarlama

Sanal ağ desteği, önbellek oluşturma sırasında **redin bölmesi Için yeni Azure önbelleğinde** yapılandırılır.

1. Premium katmanlı bir önbellek oluşturmak için [Azure Portal](https://portal.azure.com) oturum açın ve **kaynak oluştur**' u seçin. Azure portal önbellekler oluşturmaya ek olarak, Kaynak Yöneticisi şablonları, PowerShell veya Azure CLı kullanarak da oluşturabilirsiniz. Redsıs örneği için Azure önbelleğinin nasıl oluşturulacağı hakkında daha fazla bilgi için bkz. [önbellek oluşturma](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Kaynak oluşturmayı gösteren ekran görüntüsü.":::
   
1. **Yeni** sayfada **veritabanları**' nı seçin. Ardından **redsıs Için Azure önbelleği**' ni seçin.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Redsıs için Azure önbelleği seçmeyi gösteren ekran görüntüsü.":::

1. **Yeni Redis Cache** sayfasında, yeni Premium katman önbelleğiniz için ayarları yapılandırın.
   
   | Ayar      | Önerilen değer  | Açıklama |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS adı** | Genel olarak benzersiz bir ad girin. | Önbellek adı yalnızca rakam, harf veya kısa çizgi içeren 1 ile 63 karakter arasında bir dize olmalıdır. Ad bir sayı veya harfle başlamalı ve bitmeli ve ardışık çizgi içeremez. Önbellek örneğinizin *ana bilgisayar adı* *\<DNS name> . Redis.cache.Windows.net* olacaktır. |
   | **Abonelik** | Açılır listeden aboneliğinizi seçin. | Redsıs örneği için bu yeni Azure önbelleğinin oluşturulacağı abonelik. |
   | **Kaynak grubu** | Açılan listeden bir kaynak grubu seçin veya **Yeni oluştur** ' u seçin ve yeni bir kaynak grubu adı girin. | Önbelleğinizin ve diğer kaynaklarınızın oluşturulacağı kaynak grubunun adı. Tüm uygulama kaynaklarınızı tek bir kaynak grubuna yerleştirerek, bunları birlikte kolayca yönetebilir veya silebilirsiniz. |
   | **Konum** | Açılan listeden bir konum seçin. | Önbelleğinizi kullanacak diğer hizmetlerin yakınında bir [bölge](https://azure.microsoft.com/regions/) seçin. |
   | **Önbellek türü** |Premium katman özelliklerini yapılandırmak için açılan listeden bir Premium katman önbelleği seçin. Daha fazla bilgi için bkz. [redsıs fiyatlandırması Için Azure önbelleği](https://azure.microsoft.com/pricing/details/cache/). |  Fiyatlandırma katmanı önbellek için kullanılabilen boyut, performans ve özellikleri belirler. Daha fazla bilgi için bkz. [redsıs Için Azure önbelleği 'ne genel bakış](cache-overview.md). |

1. **Ağ** sekmesini seçin veya sayfanın altındaki **ağ** düğmesini seçin.

1. **Ağ** sekmesinde, bağlantı yönteminiz olarak **sanal ağlar** ' ı seçin. Yeni bir sanal ağ kullanmak için, [Azure Portal kullanarak sanal ağ oluşturma](../virtual-network/manage-virtual-network.md#create-a-virtual-network) veya [Azure Portal kullanarak bir sanal ağ (klasik) oluşturma](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)bölümündeki adımları izleyerek oluşturun. Ardından, Premium katmanlı önbelleğinizi oluşturmak ve yapılandırmak için **redsıs Için yeni Azure önbelleğine** dönün.

   > [!IMPORTANT]
   > Redsıs için Azure önbelleğini Kaynak Yöneticisi sanal bir ağa dağıttığınızda, önbelleğin Redsıs örnekleri için Azure önbelleği dışında başka hiçbir kaynak içermeyen bir ayrılmış alt ağda olması gerekir. Redsıs örneği için bir Azure önbelleğini diğer kaynakları içeren bir Kaynak Yöneticisi sanal ağ alt ağına dağıtmaya çalışırsanız, dağıtım başarısız olur.
   > 
   > 

   | Ayar      | Önerilen değer  | Açıklama |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Sanal ağ** | Açılan listeden Sanal ağınızı seçin. | Önbelleğiniz ile aynı abonelikte ve konumda bulunan bir sanal ağ seçin. |
   | **Alt ağ** | Aşağı açılan listeden alt ağlarınızı seçin. | Alt ağın adres aralığı CıDR gösteriminde olmalıdır (örneğin, 192.168.1.0/24). Sanal ağın adres alanı tarafından içerilmelidir. |
   | **Statik IP adresi** | Seçim Statik bir IP adresi girin. | Statik bir IP adresi belirtmezseniz, otomatik olarak bir IP adresi seçilir. |

   > [!IMPORTANT]
   > Azure, bazı IP adreslerini her alt ağ içinde ayırır ve bu adresler kullanılamaz. Alt ağların ilk ve son IP adresleri protokol uyumu için ayrılmıştır ve Azure hizmetleri için kullanılan üç adres daha vardır. Daha fazla bilgi için bkz. [Bu alt AĞLARDAKI IP adreslerini kullanma konusunda herhangi bir kısıtlama var mı?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > Azure sanal ağ altyapısı tarafından kullanılan IP adreslerine ek olarak, alt ağdaki redin örneği için her bir Azure önbelleği, parça başına iki IP adresi ve yük dengeleyici için bir ek IP adresi kullanır. Kümelenmemiş bir önbellek, bir parça olarak kabul edilir.
   > 

1. **İleri: Gelişmiş** sekmesini seçin veya sayfanın altındaki **İleri: Gelişmiş** düğmesini seçin.

1. Premium katman önbellek örneğinin **Gelişmiş** SEKMESINDE, TLS olmayan bağlantı noktası, kümeleme ve veri kalıcılığı için ayarları yapılandırın.

1. **Sonraki: Etiketler** sekmesini seçin veya sayfanın altındaki **Sonraki: Etiketler** düğmesini seçin.

1. İsteğe bağlı olarak, **Etiketler** sekmesinde, kaynağı sınıflandırmak istiyorsanız adı ve değeri girin.

1. **Gözden geçir ve oluştur**’u seçin. Azure 'un yapılandırmanızı doğruladığı, **gözden geçir + oluştur** sekmesine götürülürsünüz.

1. Yeşil **doğrulama başarılı** Iletisi göründüğünde **Oluştur**' u seçin.

Önbelleğin oluşturulması biraz zaman alır. Redsıs **genel bakış** sayfasında ilerlemeyi izleyebilirsiniz. **Durum** **çalışıyor** olarak görüntülendiğinde, önbellek kullanıma hazırdır. Önbellek oluşturulduktan sonra, **kaynak** menüsünden **sanal ağ** ' ı seçerek sanal ağın yapılandırmasını görüntüleyebilirsiniz.

![Sanal ağ][redis-cache-vnet-info]

Bir sanal ağ kullandığınızda Redsıs örneği için Azure önbelleğinize bağlanmak için, aşağıdaki örnekte gösterildiği gibi, bağlantı dizesinde önbelleğinizin ana bilgisayar adını belirtin:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
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
```

## <a name="azure-cache-for-redis-virtual-network-faq"></a>Redsıs sanal ağı için Azure önbelleği hakkında SSS

Aşağıdaki liste, Redsıs ölçeklendirmesi için Azure önbelleği hakkında sık sorulan soruların yanıtlarını içerir.

* Redsıs ve sanal ağlar için Azure önbelleğindeki bazı yaygın yanlış yapılandırma sorunları nelerdir?
* [Önbelleğim bir sanal ağda çalıştığını nasıl doğrulayabilirim?](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* Bir sanal ağda Redsıs örneği için Azure Önbelleğim 'e bağlanmayı denediğimde, uzak sertifikanın geçersiz olduğunu belirten bir hata alıyorum?
* [Standart veya temel önbellek ile sanal ağları kullanabilir miyim?](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* Redsıs örneği için neden Azure önbelleği oluşturulması bazı alt ağlarda başarısız oluyor, ancak başkaları için mi?
* [Alt ağ adres alanı gereksinimleri nelerdir?](#what-are-the-subnet-address-space-requirements)
* [Bir önbellek bir sanal ağda barındırıldığı zaman tüm önbellek özellikleri çalışır mı?](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks"></a>Redsıs ve sanal ağlar için Azure önbelleğindeki bazı yaygın yanlış yapılandırma sorunları nelerdir?

Redin için Azure önbelleği bir sanal ağda barındırıldığı zaman, aşağıdaki tablolardaki bağlantı noktaları kullanılır.

>[!IMPORTANT]
>Aşağıdaki tablolardaki bağlantı noktaları engellenirse, önbellek düzgün çalışmayabilir. Bu bağlantı noktalarından biri veya daha fazlası engelleniyorsa, bir sanal ağda redin için Azure önbelleğini kullandığınızda en yaygın yanlış yapılandırma sorunu vardır.
> 

- [Giden bağlantı noktası gereksinimleri](#outbound-port-requirements)
- [Gelen bağlantı noktası gereksinimleri](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Giden bağlantı noktası gereksinimleri

Dokuz giden bağlantı noktası gereksinimi vardır. Bu aralıklardaki giden istekler, önbelleğe alma için gereken diğer hizmetlere giden bağlantı veya düğümler arası iletişim için redin alt ağına iç bağlantı sağlar. Coğrafi çoğaltma için, birincil ve çoğaltma önbelleğinin alt ağları arasındaki iletişim için ek giden gereksinimler vardır.

| Bağlantı noktaları | Yön | Aktarım Protokolü | Amaç | Yerel IP | Uzak IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Giden |TCP |Azure depolama/PKI (internet) üzerinde redsıs bağımlılıkları | (Redsıs alt ağı) |* <sup>4</sup> |
| 443 | Giden | TCP | Azure Key Vault ve Azure Izleyici 'de redsıs bağımlılığı | (Redsıs alt ağı) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |Giden |TCP/UDP |DNS 'de redsıs bağımlılıkları (Internet/sanal ağ) | (Redsıs alt ağı) | 168.63.129.16 ve 169.254.169.254 <sup>2</sup> ve alt ağ <sup>3</sup> için özel DNS sunucusu |
| 8443 |Giden |TCP |Redsıs iç iletişimleri | (Redsıs alt ağı) | (Redsıs alt ağı) |
| 10221-10231 |Giden |TCP |Redsıs iç iletişimleri | (Redsıs alt ağı) | (Redsıs alt ağı) |
| 20226 |Giden |TCP |Redsıs iç iletişimleri | (Redsıs alt ağı) |(Redsıs alt ağı) |
| 13000-13999 |Giden |TCP |Redsıs iç iletişimleri | (Redsıs alt ağı) |(Redsıs alt ağı) |
| 15000-15999 |Giden |TCP |Redsıs ve coğrafi çoğaltma için dahili iletişimler | (Redsıs alt ağı) |(Redsıs alt ağı) (Coğrafi çoğaltma eş alt ağı) |
| 6379-6380 |Giden |TCP |Redsıs iç iletişimleri | (Redsıs alt ağı) |(Redsıs alt ağı) |

<sup>1</sup> AzureKeyVault ve AzureMonitor hizmet etiketlerini Kaynak Yöneticisi ağ güvenlik grupları (NSG 'ler) ile kullanabilirsiniz.

<sup>2</sup> Microsoft 'un sahip olduğu bu IP adresleri, Azure DNS hizmet veren ana bilgisayar VM 'sini ele almak için kullanılır.

<sup>3</sup> özel DNS sunucusu olmayan alt ağlar veya özel DNS 'yi yoksaymaya yönelik daha yeni redsıs önbellekler için bu bilgiler gerekli değildir.

<sup>4</sup> daha fazla bilgi için bkz. [ek sanal ağ bağlantısı gereksinimleri](#additional-virtual-network-connectivity-requirements).

#### <a name="geo-replication-peer-port-requirements"></a>Coğrafi çoğaltma eş bağlantı noktası gereksinimleri

Azure sanal ağlarında önbellekler arasında coğrafi çoğaltma kullanıyorsanız, her iki önbellekte *hem gelen hem* de giden yönlerindeki tüm alt ağ için 15000-15999 bağlantı noktalarından engellemeyi kaldırın. Bu yapılandırmayla, gelecekteki bir coğrafi Yük devretme durumunda olsa, alt ağdaki tüm çoğaltma bileşenleri birbirleriyle doğrudan iletişim kurabilir.

#### <a name="inbound-port-requirements"></a>Gelen bağlantı noktası gereksinimleri

Sekiz gelen bağlantı noktası aralığı gereksinimi vardır. Bu aralıklardaki gelen istekler, aynı sanal ağda barındırılan diğer hizmetlerden veya Redsıs alt ağı iletişimlerinde iç yollardır.

| Bağlantı noktaları | Yön | Aktarım Protokolü | Amaç | Yerel IP | Uzak IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Gelen |TCP |Redsıs ile istemci iletişimi, Azure Yük Dengeleme | (Redsıs alt ağı) | (Redsıs alt ağı), (Istemci alt ağı), AzureLoadBalancer <sup>1</sup> |
| 8443 |Gelen |TCP |Redsıs iç iletişimleri | (Redsıs alt ağı) |(Redsıs alt ağı) |
| 8500 |Gelen |TCP/UDP |Azure yük dengeleme | (Redsıs alt ağı) | AzureLoadBalancer |
| 10221-10231 |Gelen |TCP |Redsıs kümelerine istemci iletişimi, Redsıs iç iletişimleri | (Redsıs alt ağı) |(Redsıs alt ağı), AzureLoadBalancer, (Istemci alt ağı) |
| 13000-13999 |Gelen |TCP |Redsıs kümelerine istemci iletişimi, Azure Yük Dengelemesi | (Redsıs alt ağı) | (Redsıs alt ağı), (Istemci alt ağı), AzureLoadBalancer |
| 15000-15999 |Gelen |TCP |Redsıs kümelerine istemci iletişimi, Azure Yük Dengelemesi ve coğrafi çoğaltma | (Redsıs alt ağı) | (Redsıs alt ağı), (Istemci alt ağı), AzureLoadBalancer, (coğrafi çoğaltma eş alt ağı) |
| 16001 |Gelen |TCP/UDP |Azure yük dengeleme | (Redsıs alt ağı) | AzureLoadBalancer |
| 20226 |Gelen |TCP |Redsıs iç iletişimleri | (Redsıs alt ağı) |(Redsıs alt ağı) |

<sup>1</sup> AzureLoadBalancer for the NSG kurallarını yazmak için klasik dağıtım modeli için Kaynak Yöneticisi veya AZURE_LOADBALANCER için hizmet etiketi kullanabilirsiniz.

#### <a name="additional-virtual-network-connectivity-requirements"></a>Ek sanal ağ bağlantısı gereksinimleri

Redsıs için Azure önbelleği için bir sanal ağda karşılanmamış olabilecek ağ bağlantısı gereksinimleri vardır. Redsıs için Azure önbelleği, bir sanal ağ içinde kullanıldığında aşağıdaki öğelerin tümünün düzgün çalışmasını gerektirir:

* Dünya çapındaki Azure depolama uç noktalarına giden ağ bağlantısı. Redsıs örneği için Azure önbelleğiyle aynı bölgedeki ve *diğer* Azure bölgelerinde bulunan depolama uç noktaları dahil olan uç noktalar dahildir. Azure depolama uç noktaları şu DNS etki alanları altında çözümlenir: *Table.Core.Windows.net*, *BLOB.Core.Windows.net*, *Queue.Core.Windows.net* ve *File.Core.Windows.net*.
* *OCSP.DigiCert.com*, *crl4.DigiCert.com*, *OCSP.msocsp.com*, *mscrl.Microsoft.com*, *crl3.DigiCert.com*, *cacerts.DigiCert.com*, *oneocsp.Microsoft.com* ve *CRL.Microsoft.com* giden ağ bağlantısı. Bu bağlantı, TLS/SSL işlevselliğini desteklemek için gereklidir.
* Sanal ağın DNS yapılandırması, önceki noktalarda bahsedilen tüm uç noktaları ve etki alanlarını çözebilme yeteneğine sahip olmalıdır. Bu DNS gereksinimleri, sanal ağ için yapılandırılmış ve korunan geçerli bir DNS altyapısının sağlanması sağlanarak karşılanacaktır.
* Aşağıdaki Azure Izleme uç noktalarına giden ağ bağlantısı: *shoebox2-Black.shoebox2.Metrics.nsatc.net*, *North-prod2.prod2.Metrics.nsatc.net*, *azglobal-Black.azglobal.Metrics.nsatc.net*, *shoebox2-Red.shoebox2.Metrics.nsatc.net*, *East-prod2.prod2.Metrics.nsatc.net*, *azglobal-Red.azglobal.Metrics.nsatc.net*, *shoebox3.prod.microsoftmetrics.com*, *shoebox3-Red.prod.microsoftmetrics.com* ve *shoebox3-Black.prod.microsoftmetrics.com*.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-virtual-network"></a>Önbelleğim bir sanal ağda çalıştığını nasıl doğrulayabilirim?

>[!IMPORTANT]
>Bir sanal ağda barındırılan Redsıs örneği için bir Azure önbelleğine bağlandığınızda, önbellek istemcileriniz aynı sanal ağda veya sanal ağ eşlemesi aynı Azure bölgesinde etkinleştirilmiş bir sanal ağda olmalıdır. Genel sanal ağ eşlemesi Şu anda desteklenmiyor. Bu gereksinim, tüm test uygulamaları veya tanılama için ping araçları için geçerlidir. İstemci uygulamasının nerede barındırıldığından bağımsız olarak, NSG 'ler veya diğer ağ katmanları, istemcinin ağ trafiğinin Redsıs örneği için Azure önbelleğine erişmesine izin verilecek şekilde yapılandırılmalıdır.
>

Bağlantı noktası gereksinimleri önceki bölümde açıklandığı gibi yapılandırıldıktan sonra, aşağıdaki adımları izleyerek önbelleğinizin çalıştığını doğrulayabilirsiniz:

- Tüm önbellek düğümlerini [yeniden başlatın](cache-administration.md#reboot) . [Gelen bağlantı noktası gereksinimleri](cache-how-to-premium-vnet.md#inbound-port-requirements) ve [giden bağlantı noktası gereksinimleri](cache-how-to-premium-vnet.md#outbound-port-requirements)bölümünde belirtildiği gibi, tüm gerekli önbellek bağımlılıklarına ulaşılırsa, önbellek başarıyla yeniden başlatılabilir.
- Önbellek düğümleri yeniden başlatıldıktan sonra, Azure portal önbellek durumu tarafından raporlandıktan sonra, aşağıdaki testleri yapabilirsiniz:
  - [Tcpıng](https://www.elifulkerson.com/projects/tcping.php)kullanarak önbellek ile aynı sanal ağ içinde olan bir makineden 6380 bağlantı noktasını kullanarak önbellek uç noktasına ping gönderin. Örnek:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Araç, `tcping` bağlantı noktasının açık olduğunu bildirirse, önbellek sanal ağdaki istemcilerden bağlantı için kullanılabilir.

  - Test etmenin başka bir yolu da, önbelleğe bağlanan ve önbellekten bazı öğeleri ekleyen ve alan bir test önbelleği istemcisi (StackExchange. Redsıs kullanan basit bir konsol uygulaması olabilir) oluşturmaktır. Örnek istemci uygulamasını, önbellek ile aynı sanal ağda bulunan bir VM üzerine yükler. Ardından, önbelleğe bağlantıyı doğrulamak için çalıştırın.


### <a name="when-i-try-to-connect-to-my-azure-cache-for-redis-instance-in-a-virtual-network-why-do-i-get-an-error-stating-the-remote-certificate-is-invalid"></a>Bir sanal ağda Redsıs örneği için Azure Önbelleğim 'e bağlanmayı denediğimde, uzak sertifikanın geçersiz olduğunu belirten bir hata alıyorum?

Bir sanal ağ içindeki Redsıs örneği için bir Azure önbelleğine bağlanmaya çalıştığınızda, şöyle bir sertifika doğrulama hatası görürsünüz:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Nedeni, ana bilgisayara IP adresiyle bağlanmanıza neden olabilir. Ana bilgisayar adını kullanmanızı öneririz. Diğer bir deyişle, aşağıdaki dizeyi kullanın:

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Aşağıdaki bağlantı dizesine benzer IP adresini kullanmaktan kaçının:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

DNS adını çözemezseniz, bazı istemci kitaplıkları, `sslHost` StackExchange. redsıs istemcisi tarafından belirtilen gibi yapılandırma seçeneklerini içerir. Bu seçenek, sertifika doğrulama için kullanılan ana bilgisayar adını geçersiz kılmanıza olanak sağlar. Örnek:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-virtual-networks-with-a-standard-or-basic-cache"></a>Standart veya temel önbellek ile sanal ağları kullanabilir miyim?

Sanal ağlar yalnızca Premium katman önbellekler ile kullanılabilir.

### <a name="why-does-creating-an-azure-cache-for-redis-instance-fail-in-some-subnets-but-not-others"></a>Redsıs örneği için neden Azure önbelleği oluşturulması bazı alt ağlarda başarısız oluyor, ancak başkaları için mi?

Bir sanal ağa Redsıs örneği için bir Azure önbelleği dağıtıyorsanız, önbelleğin başka kaynak türü içermeyen bir ayrılmış alt ağda olması gerekir. Azure Application Gateway örnekleri ve giden NAT gibi diğer kaynakları içeren Kaynak Yöneticisi bir sanal ağ alt ağına Redsıs örneği için Azure önbelleğini dağıtmaya yönelik bir girişim yapılırsa, dağıtım genellikle başarısız olur. Redsıs örneği için yeni bir Azure önbelleği oluşturabilmeniz için, diğer türdeki mevcut kaynakları silmeniz gerekir.

Ayrıca, alt ağda yeterli sayıda IP adresiniz olmalıdır.

### <a name="what-are-the-subnet-address-space-requirements"></a>Alt ağ adres alanı gereksinimleri nelerdir?

Azure, bazı IP adreslerini her alt ağ içinde ayırır ve bu adresler kullanılamaz. Alt ağların ilk ve son IP adresleri protokol uyumu için ayrılmıştır ve Azure hizmetleri için kullanılan üç adres daha vardır. Daha fazla bilgi için bkz. [Bu alt AĞLARDAKI IP adreslerini kullanma konusunda herhangi bir kısıtlama var mı?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Azure sanal ağ altyapısı tarafından kullanılan IP adreslerine ek olarak, alt ağdaki reddo örneği için her bir Azure önbelleği, küme parça başına iki IP adresi ve varsa ek çoğaltmalar için ek IP adresleri kullanır. Yük Dengeleyici için bir ek IP adresi kullanılır. Kümelenmemiş bir önbellek, bir parça olarak kabul edilir.

### <a name="do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network"></a>Bir önbellek bir sanal ağda barındırıldığı zaman tüm önbellek özellikleri çalışır mı?

Önbelleğiniz bir sanal ağın parçası olduğunda, yalnızca sanal ağdaki istemciler önbelleğe erişebilir. Sonuç olarak, aşağıdaki önbellek yönetimi özellikleri şu anda çalışmaz:

* **Redsıs konsolu**: Redsıs konsolu, genellikle sanal ağa bağlı olmayan bir geliştirici makinesinde olan yerel tarayıcınızda çalıştığından, daha sonra önbelleğinize bağlanamaz.

## <a name="use-expressroute-with-azure-cache-for-redis"></a>Redsıs için Azure Cache ile ExpressRoute kullanma

Müşteriler, sanal ağ altyapısına bir [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) bağlantı hattı bağlanabilir. Bu şekilde, şirket içi ağı Azure 'a genişletirler.

Varsayılan olarak, yeni oluşturulan bir ExpressRoute devresi, bir sanal ağ üzerinde Zorlamalı tünel (varsayılan bir yol 0.0.0.0/0) gerçekleştirmez. Sonuç olarak, doğrudan sanal ağdan giden internet bağlantısına izin verilir. İstemci uygulamaları, Redsıs örneği için bir Azure önbelleği içeren diğer Azure uç noktalarına bağlanabilir.

Ortak bir müşteri yapılandırması, giden internet trafiğini şirket içi Flow 'a zorlayan Zorlamalı tünel oluşturma (varsayılan bir yol tanıtma) kullanmaktır. Bu trafik akışı Reda için Azure Cache ile bağlantıyı keser ve bu durumda, Redsıs örneği için Azure önbelleğinin bağımlılıklarıyla iletişim kurabilmesi için şirket içinde engellenmiş olur.

Çözüm, Redsıs örneği için Azure önbelleğini içeren alt ağda bir veya daha fazla Kullanıcı tanımlı yol (UDRs) tanımlamaktır. UDR, varsayılan yol yerine kabul edilecek alt ağa özgü yolları tanımlar.

Mümkünse, aşağıdaki yapılandırmayı kullanın:

* ExpressRoute yapılandırması 0.0.0.0/0 duyurur ve varsayılan olarak, Şirket içindeki tüm giden trafiği tünellere zorlar.
* Redl örneği için Azure önbelleğini içeren alt ağa uygulanan UDR, genel İnternet 'e TCP/IP trafiği için çalışan bir rota ile 0.0.0.0/0 tanımlar. Örneğin, bir sonraki atlama türünü *İnternet*'e ayarlar.

Bu adımların Birleşik etkisi, alt ağ düzeyi UDR 'nin ExpressRoute zorlamalı tünelden öncelikli olmasını sağlar ve bu da Redsıs örneği için Azure önbelleğinden giden internet erişimi sağlar.

ExpressRoute kullanarak bir şirket içi uygulamadan Redsıs örneği için Azure önbelleğine bağlanma, Performans nedenlerinden dolayı tipik bir kullanım senaryosu değildir. En iyi performans için, Redsıs istemcilerinin Azure önbelleği Redsıs örneği için Azure önbelleği ile aynı bölgede olmalıdır.

>[!IMPORTANT]
>Bir UDR 'de tanımlanan yolların, ExpressRoute yapılandırması tarafından tanıtılan tüm yollarla öncelikli *olması gerekir* . Aşağıdaki örnek, büyük 0.0.0.0/0 adres aralığını kullanır ve bu nedenle, daha belirli adres aralıklarını kullanan yol tanıtımlarında yanlışlıkla geçersiz kılınabilir.

>[!WARNING]
>Redsıs için Azure önbelleği *, genel eşleme yolundan özel eşleme yoluna yönelik yolların yanlışlıkla çapraz bir şekilde tanıtıldığı* ExpressRoute yapılandırmalarında desteklenmez. Ortak eşlemeye sahip ExpressRoute yapılandırmalarında, büyük bir Microsoft Azure IP adresi aralığı kümesi için Microsoft 'tan yol reklamları alın. Bu adres aralıkları özel eşleme yolunda yanlış bir şekilde bildiriliyorsa, sonuç Redsıs örneği için Azure önbelleğindeki tüm giden ağ paketlerinin bir müşterinin Şirket içi ağ altyapısına yanlışlıkla zorla tünellemesini sağlar. Bu ağ akışı Redsıs için Azure önbelleğini keser. Bu sorunun çözümü, genel eşleme yolundan özel eşleme yoluna yönelik çapraz reklam yollarını durdurmaktır.

UDRs ile ilgili arka plan bilgileri, [sanal ağ trafiği yönlendirme](../virtual-network/virtual-networks-udr-overview.md)bölümünde bulunabilir.

ExpressRoute hakkında daha fazla bilgi için bkz. [ExpressRoute teknik genel bakış](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Sonraki adımlar

Redsıs özellikleri için Azure önbelleği hakkında daha fazla bilgi edinin.

* [Redsıs Premium hizmet katmanları için Azure önbelleği](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
