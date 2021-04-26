---
title: Giden bağlantılar için kaynak ağ adresi çevirisi (SNAT)
titleSuffix: Azure Load Balancer
description: Giden Internet bağlantısı (SNAT) için Azure Load Balancer nasıl kullanıldığını öğrenin.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 3b92ef3ce195a2eee9bce53e08d977593a9f1fc2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477715"
---
# <a name="using-source-network-address-translation-snat-for-outbound-connections"></a>Giden bağlantılar için kaynak ağ adresi çevirisini (SNAT) kullanma

Azure genel yük dengeleyicisinin ön uç IP 'Leri, arka uç örnekleri için internet 'e giden bağlantı sağlamak üzere kullanılabilir. Bu yapılandırma, **kaynak ağ adresi çevirisi 'ni (SNAT)** kullanır. SNAT, arka ucun IP adresini yük dengeleyicinizin genel IP adresine yeniden yazar. 

SNAT, arka uç örneğinin **IP** 'yi aşağı olarak etkinleştirmesine izin vermez. Bu aşağı doğru, dış kaynakların arka uç örneklerine doğrudan bir adres olmasını önler. Arka uç örnekleri arasında paylaşılan bir IP adresi, statik genel IP 'lerin maliyetini azaltır. Bilinen bir IP adresi, bilinen genel IP 'lerden gelen trafikle IP izin 'i basitleştirme gibi senaryoları destekler. 

>[!Note]
> Belirli bir sanal ağdan kullanılacak tek bir IP kümesi gerektiren çok sayıda giden bağlantı veya kurumsal müşteri gerektiren uygulamalar için, [sanal ağ NAT](../virtual-network/nat-overview.md) önerilen çözümdür. Dinamik ayırma, her IP adresinin basit yapılandırmasına ve SNAT bağlantı noktalarının en verimli kullanımına olanak tanır. Sanal ağdaki tüm kaynakların, bir yük dengeleyiciyi paylaşması gerekmeden bir IP adresleri kümesini paylaşmasına izin verir.

>[!Important]
> Giden SNAT yapılandırılmamışsa bile, aynı bölgedeki Azure depolama hesapları da erişilebilir olmaya devam eder ve arka uç kaynakları hala Windows güncelleştirmeleri gibi Microsoft hizmetlerine erişime sahip olur.

>[!NOTE] 
>Bu makalede yalnızca Azure Resource Manager dağıtımları ele alınmaktadır. Azure 'daki tüm klasik dağıtım senaryoları için [giden bağlantıları (klasik)](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic) gözden geçirin.

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> Ön uç IP adresini arka uç kaynakları genelinde paylaşma

Bir yük dengeleyicinin arka uç kaynakları, örnek düzeyi genel IP (ıLPıP) adreslerine sahip değilse, genel yük dengeleyicinin ön uç IP 'si aracılığıyla giden bağlantı kurar. Bağlantı noktaları, ayrı akışları korumak için kullanılan benzersiz tanımlayıcılar oluşturmak için kullanılır. Internet, bu ayrımı sağlamak için beş tanımlama grubu kullanır.

Beş demet aşağıdakilerden oluşur:

* Hedef IP
* Hedef bağlantı noktası
* Kaynak IP
* Bu ayrımı sağlamak için kaynak bağlantı noktası ve protokol.

Gelen bağlantılar için bir bağlantı noktası kullanılıyorsa, bu bağlantı noktasındaki gelen bağlantı istekleri için bir **dinleyicisi** vardır. Bu bağlantı noktası giden bağlantılar için kullanılamaz. Giden bir bağlantı kurmak için, hedefe iletişim kurmak ve ayrı bir trafik akışının bakımını yapmak üzere bir bağlantı noktası sağlamak için **kısa ömürlü bir bağlantı noktası** kullanılır. Bu kısa ömürlü bağlantı noktaları SNAT için kullanıldığında, **SNAT bağlantı noktaları** olarak adlandırılırlar 

Tanım olarak, her IP adresinde 65.535 bağlantı noktası bulunur. Her bağlantı noktası, TCP (Iletim Denetimi Protokolü) ve UDP (Kullanıcı Datagram Protokolü) için gelen veya giden bağlantılar için kullanılabilir. 

Genel bir IP adresi bir yük dengeleyicisine ön uç IP 'si olarak eklendiğinde, Azure, SNAT için uygun olan 64.000 bağlantı noktası verir.

>[!NOTE]
> Yük Dengeleme veya gelen NAT kuralı için kullanılan her bağlantı noktası, bu 64.000 bağlantı noktalarından sekiz bağlantı noktası aralığı tüketir ve bu, SNAT için uygun bağlantı noktası sayısını azaltır. Bir yük dengeleme veya NAT kuralı, diğeri aynı sekiz aralığa sahip ise başka bir bağlantı noktası tüketmez. 

[Giden kuralları](./outbound-rules.md) ve yük dengeleme kuralları aracılığıyla, bu SNAT bağlantı noktaları, giden bağlantılar için yük dengeleyicinin genel IP 'lerini paylaşmasını sağlamak üzere arka uç örneklerine dağıtılabilir.

Aşağıdaki [Senaryo 2](#scenario2) yapılandırıldığında, her arka uç örneği için ana bilgisayar, giden bir bağlantının parçası olan paketleri SNAT olur. 

Bir arka uç örneğinden giden bir bağlantıda SNAT yaparken, ana bilgisayar kaynak IP 'yi ön uç IP 'lerinden birine yeniden yazar. 

Benzersiz akışları korumak için, ana bilgisayar her bir giden paketin kaynak bağlantı noktasını arka uç örneğindeki bir SNAT bağlantı noktasına yeniden yazar.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>Farklı senaryolar için giden bağlantı davranışı
  * Ortak IP 'si olan sanal makine.
  * Ortak IP olmadan sanal makine.
  * Ortak IP olmadan ve standart yük dengeleyici olmadan sanal makine.
        
 ### <a name="scenario-1-virtual-machine-with-public-ip-either-with-or-without-a-load-balancer"></a><a name="scenario1"></a> Senaryo 1: yük dengeleyici olan veya olmayan ortak IP 'si olan sanal makine.

 | İçermektedir | Yöntem | IP protokolleri |
 | ---------- | ------ | ------------ |
 | Ortak yük dengeleyici veya tek başına | [SNAT (kaynak ağ adresi çevirisi)](#snat) </br> kullanılmaz. | TCP (Iletim Denetim Protokolü) </br> UDP (Kullanıcı Datagram Protokolü) </br> ICMP (Internet Denetim Iletisi Protokolü) </br> ESP (Kapsüllenen Güvenlik Yükü) |

 #### <a name="description"></a>Açıklama

 Tüm trafik, sanal makinenin genel IP adresinden (örnek düzeyi IP) istek yapan istemciye döndürülür.
 
 Azure, tüm giden akışlar için örneğin NIC 'in IP yapılandırmasına atanan genel IP 'yi kullanır. Örnekte, tüm kısa ömürlü bağlantı noktaları kullanılabilir. VM 'nin yük dengeli olup olmadığı önemi yoktur. Bu senaryo diğerlerine göre önceliklidir. 

 Bir VM 'ye atanan genel IP, 1:1 ilişkidir (1: çok) ve durum bilgisiz 1:1 NAT olarak uygulanır.

 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>Senaryo 2: ortak IP olmadan ve standart ortak Load Balancer arkasında sanal makine

 | İçermektedir | Yöntem | IP protokolleri |
 | ------------ | ------ | ------------ |
 | Standart genel yük dengeleyici | [SNAT](#snat)için yük dengeleyici ön uç IP 'leri kullanımı.| TCP </br> UDP |

 #### <a name="description"></a>Açıklama

 Yük dengeleyici kaynağı, SNAT 'yi sağlayan bir giden kuralı veya bir yük dengeleme kuralıyla yapılandırılır. Bu kural, arka uç havuzuyla genel IP ön ucu arasında bağlantı oluşturmak için kullanılır. 

 Bu kural yapılandırmasını tamamlamazsanız, Senaryo 3 ' te açıklanmaktadır. 

 Sistem durumu araştırmasının başarılı olması için dinleyici içeren bir kural gerekli değildir.

 Bir VM bir giden akış oluşturduğunda, Azure Kaynak IP adresini ortak yük dengeleyici ön ucu genel IP adresine çevirir. Bu çeviri, [SNAT](#snat)aracılığıyla yapılır. 

 Yük dengeleyici ön uç genel IP adresinin kısa ömürlü bağlantı noktaları, VM 'ler tarafından kaynaklı bireysel akışları ayırt etmek için kullanılır. SNAT, giden akışlar oluşturulduğunda, [önceden ayrılmış kısa ömürlü bağlantı noktalarını](#preallocatedports) dinamik olarak kullanır. 

 Bu bağlamda, SNAT için kullanılan kısa ömürlü bağlantı noktaları SNAT bağlantı noktaları olarak adlandırılır. [Giden bir kuralın](./outbound-rules.md) açıkça yapılandırılması kesinlikle önerilir. Varsayılan SNAT 'yi bir yük dengeleme kuralı aracılığıyla kullanıyorsanız, SNAT bağlantı noktaları [varsayılan SNAT bağlantı noktaları ayırma tablosunda](#snatporttable)açıklandığı şekilde önceden ayrılır.

> [!NOTE]
> **Azure sanal ağ NAT** , yük dengeleyiciye gerek duymadan sanal makineler için giden bağlantı sağlayabilir. Daha fazla bilgi için bkz. [Azure sanal ağ NAT nedir?](../virtual-network/nat-overview.md) .

 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-standard-internal-load-balancer"></a><a name="scenario3"></a>Senaryo 3: genel IP 'siz ve standart iç Load Balancer arkasında sanal makine

 | İçermektedir | Yöntem | IP protokolleri |
 | ------------ | ------ | ------------ |
 | Standart iç yük dengeleyici | İnternet bağlantısı yok.| Yok |

 #### <a name="description"></a>Açıklama
 
Standart bir iç yük dengeleyici kullanırken, SNAT için kısa ömürlü IP adresleri kullanılamaz. Bu özellik varsayılan olarak güvenliği destekler. Bu özellik, kaynaklar tarafından kullanılan tüm IP adreslerinin yapılandırılabilir ve ayrılabilir olmasını sağlar. 

Standart bir iç yük dengeleyici kullanırken internet 'e giden bağlantı sağlamak için, [Senaryo 1](#scenario1)' deki davranışı izlemek üzere bir örnek DÜZEYI genel IP adresi yapılandırın. 

Diğer bir seçenek de arka uç örneklerini yapılandırılmış bir giden kuralla standart bir genel yük dengeleyiciye eklemektir. Arka uç örnekleri iç yük dengelemesi için bir iç yük dengeleyiciye eklenir. Bu dağıtım, [Senaryo 2](#scenario2)' deki davranışı izler. 

> [!NOTE]
> **Azure sanal ağ NAT** , yük dengeleyiciye gerek duymadan sanal makineler için giden bağlantı sağlayabilir. Daha fazla bilgi için bkz. [Azure sanal ağ NAT nedir?](../virtual-network/nat-overview.md) .

 ### <a name="scenario-4-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario4"></a>Senaryo 4: genel IP ve temel Load Balancer arka plan olmadan sanal makine

 | İçermektedir | Yöntem | IP protokolleri |
 | ------------ | ------ | ------------ |
 |Yok </br> Temel yük dengeleyici | Örnek düzeyinde dinamik IP adresi ile [SNAT](#snat)| TCP </br> UDP | 

 #### <a name="description"></a>Açıklama

 VM bir giden akış oluşturduğunda, Azure Kaynak IP adresini dinamik olarak verilen bir ortak kaynak IP adresine dönüştürür. Bu genel IP adresi **yapılandırılamaz** ve ayrılamaz. Bu adres, aboneliğin genel IP kaynak sınırına göre sayılmaz. 

Genel IP adresi serbest bırakılır ve yeniden dağıtıyorsanız yeni bir genel IP istenir: 

 * Sanal Makine
 * Kullanılabilirlik kümesi
 * Sanal makine ölçek kümesi 

 Bir allowlist öğesine IP eklemek için bu senaryoyu kullanmayın. Açık olarak giden davranışı bildirdiğiniz Senaryo 1 veya 2 ' i kullanın. [SNAT](#snat) bağlantı noktaları [varsayılan SNAT bağlantı noktaları ayırma tablosunda](#snatporttable)açıklandığı şekilde önceden ayrılır.

## <a name="exhausting-ports"></a><a name="scenarios"></a> Bağlantı noktaları tüketilme

Aynı hedef IP ve hedef bağlantı noktasına yapılan her bağlantı için bir SNAT bağlantı noktası kullanılır. Bu bağlantı, arka uç örneğinden veya **istemcisinden** bir **sunucuya** ayrı bir **trafik akışı** sağlar. Bu işlem, sunucuya trafiğin ele alınacağı ayrı bir bağlantı noktası sağlar. Bu işlem olmadan, istemci makinesi bir paketin bir parçası olduğu farkında değildir.

Birden çok tarayıcıyı giderek düşünün https://www.microsoft.com :

* Hedef IP = 23.53.254.142
* Hedef bağlantı noktası = 443
* Protokol = TCP

Geri dönüş trafiği için farklı hedef bağlantı noktaları olmadan (bağlantıyı kurmak için kullanılan SNAT bağlantı noktası), istemcinin bir sorgu sonucunu başka bir şekilde ayırabilmeyecektir.

Giden bağlantılar veri bloğu alabilir. Bir arka uç örneğine yetersiz bağlantı noktası tahsis edilebilir. **Bağlantı yeniden kullanımı** etkin OLMADıĞıNDA, SNAT **bağlantı noktası tükenmesi** riski artar.

Bağlantı noktası tükenmesi gerçekleştiğinde, hedef IP 'ye yönelik yeni giden bağlantılar başarısız olur. Bağlantı noktası kullanılabilir olduğunda bağlantı başarılı olur. Bu tükenme, bir IP adresinden 64.000 bağlantı noktası birçok arka uç örneğine ince yayıldığı zaman meydana gelir. SNAT bağlantı noktası tükenmesi 'ni azaltma hakkında yönergeler için bkz. [sorun giderme kılavuzu](./troubleshoot-outbound-connection.md).  

TCP bağlantılarında, yük dengeleyici her hedef IP ve bağlantı noktası için tek bir SNAT bağlantı noktası kullanır. Bu Multiuse aynı SNAT bağlantı noktasıyla aynı hedef IP 'ye birden çok bağlantı sağlar. Bağlantı farklı hedef bağlantı noktalarına değilse bu Multiuse sınırlı olur.

UDP bağlantılarında, yük dengeleyici hedef bağlantı noktasına her ne kadar hedef IP başına bir SNAT bağlantı noktası tüketen bir **bağlantı noktası kısıtlanmış konı NAT** algoritması kullanır. 

Bağlantı noktası sınırsız sayıda bağlantı için yeniden kullanılır. Bağlantı noktası yalnızca hedef IP veya bağlantı noktası farklı olduğunda yeniden kullanılır.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Varsayılan bağlantı noktası ayırma

Yük dengeleyicinizin ön uç IP 'si olarak atanan her genel IP 'nin, arka uç havuzu üyeleri için 64.000 SNAT bağlantı noktası verilir. Bağlantı noktaları, arka uç havuzu üyeleriyle paylaşılamaz. Bir adet SNAT bağlantı noktası aralığı, dönüş paketlerinin doğru şekilde yönlendirildiğinden emin olmak için yalnızca tek bir arka uç örneği tarafından kullanılabilir. 

SNAT bağlantı noktası ayırmayı yapılandırmak için açık bir giden kuralı kullanmanız önerilir. Bu kural, her arka uç örneği giden bağlantılar için kullanılabilir olan SNAT bağlantı noktası sayısını en üst düzeye çıkarır. 

Yük Dengeleme kuralı aracılığıyla giden SNAT 'nin otomatik olarak tahsisini kullanmanız gerekir; ayırma tablosu, bağlantı noktası ayırmayı tanımlayacaktır.

Aşağıdaki <a name="snatporttable"></a> tabloda, arka uç havuz boyutlarının katmanları IÇIN SNAT bağlantı noktası ön ayırmaları gösterilmektedir:

| Havuz boyutu (VM örnekleri) | IP yapılandırması başına önceden ayrılmış SNAT bağlantı noktaları |
| --- | --- |
| 1-50 | 1.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 | 

>[!NOTE]
> En fazla 10 boyutuna sahip bir arka uç havuzunuz varsa, açık bir giden kuralı tanımlarsanız her örnek 64000/10 = 6.400 bağlantı noktasına sahip olabilir. Yukarıdaki tabloya göre her biri yalnızca otomatik ayırmayı seçerseniz 1.024 olur.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Giden kuralları ve sanal ağ NAT

Azure Load Balancer giden kuralları ve sanal ağ NAT, bir sanal ağdan çıkış için kullanılabilir seçeneklerdir.

Giden kuralları hakkında daha fazla bilgi için bkz. [giden kuralları](outbound-rules.md).

Azure sanal ağ NAT hakkında daha fazla bilgi için bkz. [Azure sanal ağ NAT nedir?](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Kısıtlamalar

*   Bir bağlantı yeni paket gönderilmeden boşta kaldığında, bağlantı noktaları 4 – 120 dakika sonra serbest bırakılır.
  * Bu eşik, giden kuralları aracılığıyla yapılandırılabilir.
*   Her IP adresi, SNAT için kullanılabilecek 64.000 bağlantı noktası sağlar.
*   Her bağlantı noktası, hedef IP adresine yönelik TCP ve UDP bağlantıları için kullanılabilir
  * Hedef bağlantı noktasının benzersiz olup olmadığı bir UDP SNAT bağlantı noktası gerekir. Bir hedef IP 'ye yönelik her UDP bağlantısı için, bir UDP SNAT bağlantı noktası kullanılır.
  * Bir TCP SNAT bağlantı noktası, hedef bağlantı noktaları farklı olduğundan aynı hedef IP 'ye birden fazla bağlantı için kullanılabilir.
*   SNAT tükenmesi, bir arka uç örneği verilen SNAT bağlantı noktalarından çıktığında oluşur. Yük dengeleyici hala kullanılmayan SNAT bağlantı noktalarına sahip olabilir. Bir arka uç örneğinin kullanılan SNAT bağlantı noktaları, belirtilen SNAT bağlantı noktalarını aşarsa, yeni giden bağlantılar kurulamaz.

## <a name="next-steps"></a>Sonraki adımlar

*   [SNAT tükenmesi nedeniyle giden bağlantı hatalarıyla ilgili sorunları giderme](./troubleshoot-outbound-connection.md)
*   [SNAT ölçümlerini gözden geçirin](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) ve bunları filtrelemeniz, bölmeniz ve görüntülemeniz için doğru yolu tanıyın.
