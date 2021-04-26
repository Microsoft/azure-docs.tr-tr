---
title: Azure sanal ağ NAT bağlantısı sorunlarını giderme
titleSuffix: Azure Virtual Network
description: Sanal ağ NAT ile ilgili sorunları giderin.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2020
ms.author: allensu
ms.openlocfilehash: 51bc4233393be6d914578581597e8cce9d0373b0
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059267"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Azure sanal ağ NAT bağlantısı sorunlarını giderme

Bu makale, yöneticilerin sanal ağ NAT kullanırken bağlantı sorunlarını tanılayıp çözümlemesine yardımcı olur.

## <a name="problems"></a>Sorunlar

* [SNAT tükenmesi](#snat-exhaustion)
* [ICMP ping işlemi başarısız oluyor](#icmp-ping-is-failing)
* [Bağlantı sorunları](#connectivity-failures)
* [IPv6 birlikte bulunma](#ipv6-coexistence)
* [Bağlantı NAT ağ geçidi IP 'lerden kaynaklanmıyor](#connection-doesnt-originate-from-nat-gateway-ips)

Bu sorunları çözmek için aşağıdaki bölümdeki adımları izleyin.

## <a name="resolution"></a>Çözüm

### <a name="snat-exhaustion"></a>SNAT tükenmesi

Tek bir [NAT ağ geçidi kaynağı](nat-gateway-resource.md) 64.000 ' den 1.000.000 ' e kadar eşzamanlı akışı destekler.  Her IP adresi, kullanılabilir stoğa 64.000 SNAT bağlantı noktası sağlar. Her NAT ağ geçidi kaynağı için en fazla 16 IP adresi kullanabilirsiniz.  SNAT mekanizması daha ayrıntılı olarak [açıklanmıştır.](nat-gateway-resource.md#source-network-address-translation)

SNAT tükenmesi 'nın temel nedeni, giden bağlantının kurulduğu, yönetildiği veya yapılandırılabilir zamanlayıcılar değerlerinin varsayılan değerlerinden nasıl değiştiği için bir kenar modeldir.  Bu bölümü dikkatli bir şekilde inceleyin.

#### <a name="steps"></a>Adımlar

1. Varsayılan boşta kalma zaman aşımını 4 dakikadan daha büyük bir değere değiştirdiyseniz işaretleyin.
2. Uygulamanızın giden bağlantı oluşturma şeklini araştırın (örneğin, kod incelemesi veya paket yakalama). 
3. Bu etkinliğin beklenen davranış olup olmadığını veya uygulamanın hatalı çalışıp çalışmadığını belirleme.  Bulgularınızı eklemek için Azure Izleyici 'de [ölçümleri](nat-metrics.md) kullanın. SNAT bağlantıları ölçümü için "başarısız" kategorisini kullanın.
4. Uygun desenlerin izlendikten sonra değerlendirin.
5. SNAT bağlantı noktası tükenmesi 'nin NAT ağ geçidi kaynağına atanan ek IP adresleriyle azaltılıp azaltılmasının gerekip gerekmediğini değerlendirin.

#### <a name="design-patterns"></a>Tasarım desenleri

Mümkün olduğunda her zaman bağlantı yeniden kullanımı ve bağlantı havuzlarından yararlanın.  Bu desenler, kaynak tükenmesi sorunlarından kaçınır ve öngörülebilir davranışa neden olur. Bu desenlerin temelleri birçok geliştirme kitaplığı ve çerçeve içinde bulunabilir.

_**Çözüm:**_ Uygun desenleri ve en iyi uygulamaları kullanın

- NAT ağ geçidi kaynakları varsayılan TCP boşta kalma zaman aşımı 4 dakikadır.  Bu ayar daha yüksek bir değere değiştirilirse, NAT, daha uzun bir akışa sahip olur ve [SNAT bağlantı noktası envanterinde gereksiz basınç](nat-gateway-resource.md#timers)oluşmasına neden olabilir.
- Atomik istekler (bağlantı başına bir istek) kötü bir tasarım seçimdir. Bu şekilde, bu tür bir desenler ölçeği sınırlar, performansı azaltır ve güvenilirliği azaltır. Bunun yerine, bağlantı sayısını ve ilişkili SNAT bağlantı noktalarını azaltmak için HTTP/S bağlantılarını yeniden kullanın. TLS kullanılırken, daha düşük el sıkışmaları, ek yük ve şifreleme işlemi maliyeti nedeniyle uygulama ölçeği artar ve performans artar.
- DNS, istemci DNS çözümleyiciler sonucunu önbelleğe alırken, toplu olarak birçok ayrı akış oluşturabilir. Önbelleğe almayı kullanın.
- UDP akışları (örneğin, DNS aramaları), boşta kalma zaman aşımı süresi boyunca SNAT bağlantı noktalarını ayırır. Boşta kalma zaman aşımı, SNAT bağlantı noktalarında basınç arttıkça daha uzun olur. Kısa boşta kalma zaman aşımı (örneğin 4 dakika) kullanın.
- Bağlantı birimlerinizi şekillendirmek için bağlantı havuzlarını kullanın.
- Hiçbir şekilde hiçbir TCP akışını sessizce iptal edin ve akışı temizlemek için TCP zamanlayıcılarını güvenin. Bağlantıyı açık bir şekilde kapatmaya izin vermezseniz, durum ara sistemlere ve uç noktalara ayrılır ve SNAT bağlantı noktalarını diğer bağlantılar için kullanılamaz hale getirir. Bu model, uygulama başarısızlıklarını ve SNAT tükenmesi 'ni tetikleyebilir. 
- Uzman etkisi olmadan, işletim sistemi düzeyinde TCP ile ilgili süreölçer değerlerini değiştirmeyin. TCP yığını kurtarılarken, bir bağlantının uç noktalarında yanlış beklentiler olduğunda uygulama performansı olumsuz etkilenebilir. Zamanlayıcılarını değiştirme isteği genellikle temel alınan tasarım sorununun bir imzadır. Aşağıdaki önerileri gözden geçirin.

SNAT tükenmesi Ayrıca, temel alınan uygulamadaki diğer kenar desenleriyle de dağıtılabilir. Hizmetinizin ölçeğini ve güvenilirliğini artırmak için bu ek desenleri ve en iyi uygulamaları gözden geçirin.

- Daha önce SNAT bağlantı noktası envanterini boşaltmak için 4 dakikalık varsayılan boşta kalma zaman aşımı da dahil olmak üzere değerleri düşürmek için [TCP boşta kalma zaman aşımını](nat-gateway-resource.md#timers) azaltmanın etkisini keşfe
- Diğer işlemlere yönelik bağlantı kaynaklarını boşaltmak için uzun süre çalışan işlemler için [zaman uyumsuz yoklama düzenlerini](/azure/architecture/patterns/async-request-reply) düşünün.
- Uzun süreli akışlar (örneğin, yeniden kullanılan TCP bağlantıları), ara sistemlerin zaman aşımına uğramaması için TCP keepcanlı veya uygulama katmanı keepcanlı olarak kullanılmalıdır. Boşta kalma zaman aşımını artırmak son çare olur ve kök nedeni çözemeyebilir. Uzun zaman aşımı, zaman aşımı süresi dolduğunda düşük hız hatasına neden olabilir ve gecikme ve gereksiz arızalar ortaya çıkarabilir.
- Geçici hata veya hata kurtarma sırasında agresif yeniden denemeler/bursts önlemek için düzgün [yeniden deneme desenleri](/azure/architecture/patterns/retry) kullanılmalıdır.
Her HTTP işlemi için yeni TCP bağlantısı oluşturma ("atomik bağlantılar" olarak da bilinir), bir anti-örünmedir.  Atomik bağlantılar, uygulamanızın iyi ve çöp kaynaklarını ölçeklendirmasını engeller.  Her zaman ardışık düzen birden çok işlem aynı bağlantıya sahiptir.  Uygulamanız işlem hızı ve kaynak maliyetlerinde yarar olacaktır.  Uygulamanız aktarım katmanı şifrelemesini (örneğin, TLS) kullandığında, yeni bağlantıların işlenmesiyle ilişkili önemli bir maliyet vardır.  Ek en iyi yöntem desenleri için [Azure bulut tasarım modellerini](/azure/architecture/patterns/) gözden geçirin.

#### <a name="additional-possible-mitigations"></a>Olası ek azaltmalar

_**Çözüm:**_ Giden bağlantıyı aşağıdaki şekilde ölçeklendirin:

| Senaryo | Elde |Risk azaltma |
|---|---|---|
| SNAT bağlantı noktaları ve SNAT bağlantı noktası tükenmesi için yüksek kullanım dönemlerinde çekişme yaşıyor olursunuz. | Azure Izleyici 'de SNAT bağlantıları [ölçümü](nat-metrics.md) için "başarısız" kategorisi, zaman ve yüksek bağlantı hacminde geçici veya kalıcı sorunları gösterir.  | Ek genel IP adresi kaynakları veya genel IP öneki kaynakları ekleyip ekleyemediğine göre belirlenir. Bu ekleme, NAT ağ geçidiniz için toplamda en fazla 16 IP adresi sağlar. Bu ek, kullanılabilir SNAT bağlantı noktaları (IP adresi başına 64.000) için daha fazla envanter sağlar ve senaryonuzu daha da ölçeklendirmenize olanak tanır.|
| Zaten 16 IP adresi vermiş ve hala SNAT bağlantı noktası tükenmesi yaşıyor. | Ek IP adresi ekleme girişimi başarısız olur. Genel IP adresi kaynaklarından veya genel IP öneki kaynaklarından alınan toplam IP adresi sayısı toplam 16 değerini aşıyor. | Uygulama ortamınızı birden çok alt ağ arasında dağıtın ve her alt ağ için bir NAT ağ geçidi kaynağı sağlayın.  Önceki [kılavuza](#design-patterns)göre iyileştirmek için tasarım modellerinizi yeniden değerlendirin. |

>[!NOTE]
>SNAT tükenmesinin neden oluştuğunu anlamak önemlidir. Ölçeklenebilir ve güvenilir senaryolar için doğru desenleri kullandığınızdan emin olun.  İstek nedenini bilmeden bir senaryoya daha fazla SNAT bağlantı noktası eklenmesi, son çare olmalıdır. Senaryolarınızın SNAT bağlantı noktası envanterine neden olduğunu anladıysanız daha fazla IP adresi ekleyerek stoğa daha fazla SNAT bağlantı noktası eklenmesi, uygulamanız ölçeklenirken yalnızca aynı tükenme başarısızlığını geciktirecek.  Diğer verimsizlikleri ve anti-desenlerinde maskeleme yapabilirsiniz.

### <a name="icmp-ping-is-failing"></a>ICMP ping işlemi başarısız oluyor

[Sanal ağ NAT](nat-overview.md) , IPv4 UDP ve TCP protokollerini destekler. ICMP desteklenmez ve başarısız olması beklenir.  

_**Çözüm:**_ Bunun yerine, uçtan uca bağlantıyı doğrulamak için TCP bağlantı testlerini (örneğin, "TCP ping") ve UDP 'ye özgü uygulama katmanı testlerini kullanın.

Aşağıdaki tabloda, testleri başlatmak için kullanılacak araçlar için bir başlangıç noktası kullanılabilir.

| İşletim sistemi | Genel TCP bağlantısı sınaması | TCP uygulama katmanı testi | UDP |
|---|---|---|---|
| Linux | NC (genel bağlantı testi) | kıvrımlı (TCP uygulama katmanı testi) | uygulamaya özgü |
| Windows | [PsPing](/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | uygulamaya özgü |

### <a name="connectivity-failures"></a>Bağlantı sorunları

[Sanal ağ NAT](nat-overview.md) ile bağlantı sorunları çeşitli farklı sorunlardan kaynaklanıyor olabilir:

* yapılandırma hataları nedeniyle kalıcı hatalar.
* NAT ağ geçidinin geçici veya kalıcı [SNAT tükenmesi](#snat-exhaustion) ,
* Azure altyapısında geçici başarısızlıklar, 
* Azure ile genel Internet hedefi arasındaki yoldaki geçici sorunlar 
* genel Internet hedefindeki geçici veya kalıcı arızalar.

Doğrulama bağlantısı için aşağıdaki gibi araçları kullanın. [ICMP pingi desteklenmez](#icmp-ping-is-failing).

| İşletim sistemi | Genel TCP bağlantısı sınaması | TCP uygulama katmanı testi | UDP |
|---|---|---|---|
| Linux | NC (genel bağlantı testi) | kıvrımlı (TCP uygulama katmanı testi) | uygulamaya özgü |
| Windows | [PsPing](/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | uygulamaya özgü |

#### <a name="configuration"></a>Yapılandırma

Yapılandırmanızı denetleyin:
1. NAT ağ geçidi kaynağında en az bir genel IP kaynağı veya bir genel IP ön ek kaynağı var mı? NAT ağ geçidinin giden bağlantı sağlayabilmesi için en az bir IP adresinin ilişkilendirilmiş olması gerekir.
2. Sanal ağın alt ağı, NAT ağ geçidini kullanacak şekilde yapılandırılmış mı?
3. UDR (Kullanıcı tanımlı yol) kullanıyor musunuz ve hedefi geçersiz kılsın mı?  NAT ağ geçidi kaynakları, yapılandırılan alt ağlarda varsayılan yol (0/0) olur.

#### <a name="snat-exhaustion"></a>SNAT tükenmesi

Bu makaledeki [SNAT tükenmesi](#snat-exhaustion) bölümüne bakın.

#### <a name="azure-infrastructure"></a>Azure altyapısı

Azure, altyapısını büyük önem taşıyan şekilde izler ve çalışır. Geçici sorunlar ortaya çıkabilir, iletimlerin kayıpsız olduğunun garantisi yoktur.  TCP uygulamaları için SYN yeniden iletimlerine izin veren tasarım düzenlerini kullanın. Bir kayıp SYN paketinin neden olduğu geçici etkileri azaltmak için TCP SYN yeniden aktarım için yeterince büyük olan bağlantı zaman aşımlarını kullanın.

_**Çözümden**_

* [SNAT tükenmesi](#snat-exhaustion)olup olmadığını denetleyin.
* SYN yeniden aktarım davranışını denetleyen bir TCP yığınındaki yapılandırma parametresi RTO ([yeniden Iletim zaman aşımı](https://tools.ietf.org/html/rfc793)) olarak adlandırılır. RTO değeri ayarlanabilir ancak varsayılan olarak üstel geri ile 1 saniye veya daha yüksektir.  Uygulamanızın bağlantı zaman aşımı çok kısaysa (örneğin 1 saniye), tek biçimli bağlantı zaman aşımları görebilirsiniz.  Uygulama bağlantısı zaman aşımını artırın.
* Daha uzun bir süre gözlemlerseniz, varsayılan uygulama davranışlarıyla birlikte beklenmeyen zaman aşımları varsa, daha fazla sorun giderme için bir destek

TCP bağlantı zaman aşımını azaltma veya RTO parametresini ayarlama yapay önerilmez.

#### <a name="public-internet-transit"></a>Genel Internet geçişi

Geçici hataların olasılığı, hedef ve daha fazla ara sistemlere daha uzun bir yol ile artar. Geçici hataların, [Azure altyapısına](#azure-infrastructure)göre sıklığın artmasına yönelik olması beklenmektedir. 

Yukarıdaki [Azure altyapısı](#azure-infrastructure) bölümüyle aynı yönergeleri izleyin.

#### <a name="internet-endpoint"></a>Internet uç noktası

Önceki bölümler, iletişimin kurulduğu Internet uç noktasıyla birlikte geçerlidir. Bağlantı başarısını etkileyebilecek diğer faktörler şunlardır:

* hedef tarafta trafik yönetimi, örneğin
- Hedef tarafı tarafından uygulanan API hız sınırlaması
- Volumetric DDoS azaltmaları veya Aktarım katmanı trafiği şekillendirme
* güvenlik duvarı veya hedefteki diğer bileşenler 

Genellikle kaynak üzerinde paket yakalar ve hedefin ne olduğunu belirlemek için hedef (varsa) gereklidir.

_**Çözümden**_

* [SNAT tükenmesi](#snat-exhaustion)olup olmadığını denetleyin. 
* Aynı bölgedeki bir uç nokta ile veya başka bir yerde karşılaştırma için bağlantıyı doğrulayın.  
* Yüksek hacimli veya işlem oranı testi oluşturuyorsanız, oranın azaltılmasının, hataların oluşma riskini azalttığını araştırın.
* Değiştirme oranı başarısızlık oranını etkilerken, API Hız sınırlarına veya hedef taraftaki diğer kısıtlamalara ulaşılmadığını denetleyin.
* Araştırmanızın giderilmesi sonuçlandırıldıysanız, daha fazla sorun giderme için bir destek talebi açın.

#### <a name="tcp-resets-received"></a>TCP sıfırlama alındı

NAT ağ geçidi, devam ediyor olarak tanınmayan trafik için kaynak VM 'de TCP sıfırlamaları üretir.

Olası bir nedenden dolayı TCP bağlantısının boşta kalma süresi doldu.  Boşta kalma zaman aşımını 4 dakika ila 120 dakikaya kadar ayarlayabilirsiniz.

TCP sıfırlama, NAT ağ geçidi kaynaklarının genel tarafında oluşturulmaz. Hedef taraftaki TCP sıfırlamaları, NAT ağ geçidi kaynağı değil kaynak VM tarafından oluşturulur.

_**Çözümden**_

* [Tasarım desenleri](#design-patterns) önerilerini gözden geçirin.  
* Gerekirse daha fazla sorun giderme için bir destek talebi açın.

### <a name="ipv6-coexistence"></a>IPv6 birlikte bulunma

[Sanal ağ NAT](nat-overview.md) , IPv4 UDP ve TCP protokollerini destekler ve [IPv6 ön ekine sahip bir alt ağda dağıtım desteklenmez](nat-overview.md#limitations).

_**Çözüm:**_ NAT ağ geçidini IPv6 öneki olmayan bir alt ağda dağıtın.

[Sanal ağ NAT UserVoice](https://aka.ms/natuservoice)aracılığıyla ek özelliklerde ilgi gösterebilirsiniz.

### <a name="connection-doesnt-originate-from-nat-gateway-ips"></a>Bağlantı NAT ağ geçidi IP 'lerden kaynaklanmıyor

NAT ağ geçidini, kullanılacak IP adreslerini ve bir NAT ağ geçidi kaynağı kullanması gereken alt ağı yapılandırırsınız. Ancak, NAT ağ geçidi dağıtılmadan önce var olan sanal makine örneklerinin bağlantıları IP adreslerini kullanmaz.  Bunlar, NAT ağ geçidi kaynağıyla kullanılmayan IP adreslerini kullanıyor gibi görünürler.

_**Çözümden**_

[Sanal ağ NAT](nat-overview.md) , üzerinde yapılandırıldığı alt ağın giden bağlantısını değiştirir. Varsayılan SNAT veya yük dengeleyici giden SNAT 'den NAT ağ geçitleri kullanılarak geçiş yaparken yeni bağlantılar, NAT ağ geçidi kaynağıyla ilişkili IP adreslerini kullanmaya hemen başlar.  Ancak, bir sanal makine, NAT ağ geçidi kaynağına geçiş sırasında hala oluşturulmuş bir bağlantıya sahipse bağlantı oluşturulduğunda atanan eski SNAT IP adresini kullanmaya devam eder.  İşletim sistemi veya tarayıcı bir bağlantı havuzundaki bağlantıları önbelleğe alındığından, zaten var olan bir bağlantıyı yeniden kullanmak yerine yeni bir bağlantı kullandığınızdan emin olun.  Örneğin, PowerShell 'de _kıvrımlı_ kullanırken, yeni bir bağlantı zorlamak için _-disablekeepalive_ parametresini belirttiğinizden emin olun.  Bir tarayıcı kullanıyorsanız, bağlantılar da havuza alınabilir.

Bir NAT ağ geçidi kaynağı için bir alt ağ yapılandırarak bir sanal makineyi yeniden başlatmak gerekli değildir.  Ancak, bir sanal makine yeniden başlatıldığında bağlantı durumu temizlenir.  Bağlantı durumu boşaltılmışsa, tüm bağlantılar NAT ağ geçidi kaynağının IP adreslerini kullanmaya başlar.  Ancak bu, yeniden başlatmanın gerekli olduğu bir gösterge değil, sanal makinenin yeniden başlatıldığı yan etkilerden oluşur.

Hala sorun yaşıyorsanız, daha fazla sorun giderme için bir destek talebi açın.

## <a name="next-steps"></a>Sonraki adımlar

* [Sanal ağ NAT](nat-overview.md) hakkında bilgi edinin
* [NAT ağ geçidi kaynağı](nat-gateway-resource.md) hakkında bilgi edinin
* [NAT ağ geçidi kaynakları için ölçümler ve uyarılar](nat-metrics.md)hakkında bilgi edinin.
* [UserVoice 'Ta sanal ağ NAT için bir sonraki derleme yapmanız gerektiğini bize söyleyin](https://aka.ms/natuservoice).