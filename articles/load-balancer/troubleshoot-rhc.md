---
title: Azure Load Balancer kaynak durumu, ön uç ve arka uç kullanılabilirliği sorunlarını giderme
description: Azaltılmış veya kullanılamıyor Azure Standart Load Balancer tanımak için kullanılabilir ölçümleri kullanın.
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2020
ms.author: errobin
ms.openlocfilehash: 3acaaba86c9a546a0bd45b5386287908168d50d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97955629"
---
# <a name="troubleshoot-resource-health-and-inbound-availability-issues"></a>Kaynak sistem durumu ve gelen kullanılabilirlik sorunlarını giderme 

Bu makale, yük dengeleyici ön uç IP ve arka uç kaynaklarınızın kullanılabilirliğini etkileyen sorunları araştırmak için bir kılavuzdur. 

Load Balancer için Kaynak Durumu denetimi (RHC), yük dengeleyicinizin sistem durumunu belirlemede kullanılır. Yük Dengeleme uç noktaları, Yük Dengeleme kurallarıyla ön uç IP ve ön uç bağlantı noktaları birleşimleri varsa, veri yolu kullanılabilirliği ölçüsünü **2 dakikalık** bir aralıkta analiz eder.

Aşağıdaki tabloda, yük dengeleyicinizin sistem durumunu belirlemede kullanılan RHC mantığı açıklanmaktadır.

| Kaynak sistem durumu | Description |
| --- | --- |
| Kullanılabilir | Standart yük dengeleyici kaynağınız sağlıklı ve kullanılabilir durumda. |
| Düzeyi düşürüldü | Standart yük dengeleyiciye, performansı etkileyen platform veya Kullanıcı tarafından başlatılan olaylar vardır. Veri Yolu Kullanılabilirliği ölçümü en az iki dakika süreyle %90’dan az ama %25’ten fazla durum bildirdi. Orta derecede önemli performans etkisi yaşayacaktır. 
| Kullanılamaz | Standart yük dengeleyici kaynağınız sağlıklı değil. Veri yolu kullanılabilirlik ölçümü, en az iki dakika boyunca %25 sistem durumunu daha az raporladı. Gelen bağlantı için önemli bir performans etkisi veya kullanılabilirlik eksikliği yaşanacaktır. Kullanılamaz duruma neden olan kullanıcı veya platform olayları olabilir. |
| Bilinmiyor | Standart yük dengeleyici kaynağınızın kaynak sistem durumu henüz güncelleştirilmemiş veya son 10 dakika boyunca veri yolu kullanılabilirliği bilgilerini almamış. Bu durum geçici olacak ve veriler alınınca hemen doğru durum yansıtılacaktır. |


## <a name="about-the-metrics-well-use"></a>Kullanacağımız ölçümler hakkında
Kullanılacak iki ölçüm *veri yolu kullanılabilirliği* ve *sistem durumu araştırma durumudur* ve doğru öngörüleri türetmede anlamını anlamak önemlidir. 

## <a name="data-path-availability"></a>Veri yolu kullanılabilirliği
Veri yolu kullanılabilirlik ölçümü, yük dengeleme ve gelen NAT kuralları yapılandırılmış olan tüm ön uç bağlantı noktalarında her 25 saniyede bir TCP ping işlemi tarafından oluşturulur. Bu TCP ping daha sonra sağlıklı (denetlenen) arka uç örneklerinden herhangi birine yönlendirilir. Hizmet, ping komutuna bir yanıt alırsa, başarı olarak kabul edilir ve ölçüm toplamı bir kez yinelenebilir ve başarısız olursa, işlem başarısız olur. Bu ölçümün sayısı, örnek süre başına toplam TCP ping değerinin 1/100 ' dir. Bu nedenle, sürenin ortalamasını göz önünde bulundurmanız istiyoruz. Bu, dönemin toplam ortalamasını/sayısını gösterir. Ortalama olarak toplanan veri yolu kullanılabilirliği ölçümü, yük dengeleme ve gelen NAT kurallarınızın her biri için bağlantı noktası olan ön uç IP 'niz üzerinde TCP ping işlemleri için yüzde bir başarı oranı verir.

## <a name="health-probe-status"></a>Sistem durumu yoklama durumu
Durum araştırma durumu ölçümü, sistem durumu araştırmasıyla tanımlanan protokolün ping işlemi tarafından oluşturulur. Bu ping, arka uç havuzundaki her örneğe ve sistem durumu araştırmasına tanımlanan bağlantı noktasına gönderilir. HTTP ve HTTPS araştırmaları için, başarılı bir ping HTTP 200 OK yanıtı gerektirir, ancak TCP araştırmaları ile tüm yanıtlar başarılı olarak kabul edilir. Her bir araştırmanın ardışık başarısı veya başarısızlığı, arka uç örneğinin sağlıklı olup olmadığını ve arka uç havuzunun atandığı Yük Dengeleme kuralları için trafik alıp alamayacağını belirler. Veri yolu kullanılabilirliğine benzer şekilde, örnekleme aralığı sırasında ortalama başarılı/toplam ping isteği bildiren ortalama toplamayı kullanırız. Bu durum araştırma durumu değeri, ön uç ile trafik göndermeden arka uç örneklerinizi inceleyerek yük dengeleyicinizden yalıtılmış arka uç durumunu belirtir.

>[!IMPORTANT]
>Durum araştırma durumu bir dakika temelinde örneklenir. Bu, aksi takdirde sabit bir değerde küçük dalgalanmalara yol açabilir. Örneğin, biri araştırılan ve bir araştırılan iki arka uç örneği varsa, sistem durumu araştırma hizmeti sağlıklı örnek için 7 örnek ve sağlıksız örnek için 6 alabilir. Bu, bir dakikalık zaman aralığı için 46,15 olarak gösterilen daha önceden sabit bir 50 değerine neden olur. 

## <a name="diagnose-degraded-and-unavailable-load-balancers"></a>Düşürülmüş ve kullanılamayan yük dengeleyicileri tanılayın
[Kaynak sistem durumu makalesinde](load-balancer-standard-diagnostics.md#resource-health-status)özetlenen şekilde, düşürülmüş bir yük dengeleyici, %25 ila %90 veri yolu kullanılabilirliği arasında ve kullanılamayan bir yük dengeleyici, iki dakikalık bir dönem boyunca %25 ' ten az veri yolu kullanılabilirliğiyle biridir. Bu adımlar, yapılandırdığınız sistem durumu araştırma durumunda veya veri yolu kullanılabilirliği uyarılarında gördüğünüz hatayı araştırmak için de alınabilir. Kaynak sistem durumumuzu denetlediğimiz ve yük dengeleyicimizi %0 ' ın bir veri yolu kullanılabilirliği ile kullanılamaz hale getireceğimizi keşfettireceğiz; hizmetimiz çalışmıyor.

İlk olarak, yük dengeleyici Öngörüler dikey penceremizin ayrıntılı ölçüm görünümüne gideceğiz. Bunu, yük dengeleyici kaynak dikey penceresi veya kaynak sistem sağlığı iletinizde bağlantı aracılığıyla yapabilirsiniz.  Ardından, ön uç ve arka uç kullanılabilirliği sekmesine gittik ve düşürülmüş veya kullanılamaz durumda olan zaman döneminin otuz dakikalık bir penceresini gözden geçiririz. Veri yolu kullanılabilirümüzün %0 olduğunu görüyoruz, yük dengeleme ve gelen NAT kurallarımızın tümünün trafiğini engellediğini ve bu etkinin ne kadar sürdüğünü görebileceklerini öğrendiğimiz bir sorun olduğunu biliyoruz. 

Bir sonraki yer, veri yolumuzın kullanım dışı olup olmadığını anlamak için sistem durumu araştırma durumu ölçümümüzdür. Yük Dengeleme ve gelen kurallarımızda en az bir adet sağlıklı arka uç örneğimiz varsa, bu durum, veri yollarımızın kullanılamamasına neden olan yapılandırma olmadığını biliyoruz. Bu senaryo, bir Azure platformu sorununun, nadir olarak, tüm platform sorunlarını hızla çözümlemek için ekibimize otomatik bir uyarı gönderildiğinden, bu sorunu fark etmez.

## <a name="diagnose-health-probe-failures"></a>Durum araştırma başarısızlıklarını tanılama
Durum araştırma durumumuzu denetliyoruz ve tüm örneklerin sağlıksız olarak gösterildiğini öğreniyoruz. Bu bulma, trafiğin nereye gidebileceği konusunda veri yolumuzu neden kullanılamadığını açıklar. Daha sonra genel yapılandırma hatalarını bırakmak için aşağıdaki denetim listesini inceleyin:
* Örneklerinizin gerçekten sağlıklı olup olmadığını kontrol etmek için kaynaklarınızın CPU kullanımını denetleyin
  * Bunu kontrol edebilirsiniz 
* Uygulama sağlıklı ve yanıt vermeye yönelik bir HTTP veya HTTPS araştırma denetimi kullanıyorsanız
  * Bu işlem, arka uç örneğinizle ilişkili özel IP adresi veya örnek düzeyi genel IP adresi aracılığıyla uygulamalara doğrudan erişerek çalışır
* Arka uç kaynaklarımıza uygulanan ağ güvenlik gruplarını gözden geçirin. Sistem durumu araştırmasını engelleyecek AllowAzureLoadBalancerInBound kıyasla daha yüksek öncelikli bir kural olmadığından emin olun
  * Bunu, arka uç sanal makinelerinizin veya sanal makine ölçek kümelerinin ağ dikey penceresini ziyaret ederek yapabilirsiniz
  * Bu NSG sorununu bulursanız, mevcut Izin verme kuralını taşıyın veya AzureLoadBalancer trafiğe izin vermek için yeni bir yüksek öncelikli kural oluşturun
* İşletim sistemini kontrol edin. Sanal makinelerinizin araştırma bağlantı noktasında dinleme yaptığı ve IP adresinden kaynaklanan araştırma trafiğini engellemediğinden emin olmak için işletim sistemi güvenlik duvarı kurallarını gözden geçirdiklerinden emin olun. 168.63.129.16
  * Bir Linux terminalinde netstat-a Windows komut istemi veya netstat-l komutunu çalıştırarak dinleme bağlantı noktalarını kontrol edebilirsiniz
* Yük dengeleyicinin arka uç havuzunda bir güvenlik duvarı NVA VM yerleştirmeyin, güvenlik duvarı üzerinden trafiği arka uç örneklerine yönlendirmek için [Kullanıcı tanımlı yolları](../virtual-network/virtual-networks-udr-overview.md#user-defined) kullanın
* Http olmayan bir uygulamayı dinleyen bir bağlantı noktasını yoklamek için HTTP kullanıyorsanız, doğru protokolü kullandığınızdan emin olun. araştırma başarısız olur

Bu denetim noktasından geçmiş ve hala durum araştırma hataları buldıysanız, örneklerinizin araştırma hizmetini etkileyen çok nadir platform sorunları olabilir. Bu durumda, Azure geri ve tüm platform sorunlarını hızlı bir şekilde çözmek için ekibimize otomatik bir uyarı gönderilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Load Balancer sistem durumu araştırması hakkında daha fazla bilgi edinin](load-balancer-custom-probe-overview.md)
* [Azure Load Balancer ölçümleri hakkında daha fazla bilgi edinin](load-balancer-standard-diagnostics.md)
