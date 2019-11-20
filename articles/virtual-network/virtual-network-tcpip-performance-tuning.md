---
title: Azure VM 'Leri için TCP/IP performans ayarlaması | Microsoft Docs
description: Çeşitli yaygın TCP/IP performansı ayarlama tekniklerini ve bunların Azure VM 'Lerle ilişkilerini öğrenin.
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: bb23484903ac3ce129c6e7a7a27e0765c227fb1d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297778"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Azure VM 'Leri için TCP/IP performans ayarı

Bu makalede, yaygın TCP/IP performansı ayarlama teknikleri ve bunları Azure üzerinde çalışan sanal makineler için kullandığınızda göz önünde bulundurmanız gereken bazı noktalar açıklanmaktadır. Bu, tekniklerin temel bir genel görünümünü sağlar ve bunların nasıl ayarlanacağını keşfedebilir.

## <a name="common-tcpip-tuning-techniques"></a>Ortak TCP/IP ayarlama teknikleri

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU, parçalanma ve büyük gönderme boşaltma

#### <a name="mtu"></a>BOYUTUNU

En yüksek iletim birimi (MTU), bir ağ arabirimi üzerinden gönderilebilecek, bayt cinsinden belirtilen en büyük boyut çerçevesidir (paket). MTU, yapılandırılabilir bir ayardır. Azure VM 'lerinde kullanılan varsayılan MTU ve ağ cihazlarının çoğu genel olarak varsayılan ayar 1.500 bayttır.

#### <a name="fragmentation"></a>Masına

Parçalama, bir ağ arabiriminin MTU değerini aşan bir paket gönderildiğinde oluşur. TCP/IP yığını, paketi arabirimin MTU 'SU ile uyumlu olan küçük parçalara (parçalar) keser. Parçalanma IP katmanında oluşur ve temel alınan protokolle (TCP gibi) bağımsızdır. 2\.000 baytlık bir paket, MTU değeri 1.500 olan bir ağ arabirimi üzerinden gönderildiğinde, paket 1 1.500-bayt paketi ve 1 500-Byte paketi olarak bölünür.

Kaynak ve hedef arasındaki yoldaki ağ aygıtları, MTU 'YU aşan paketleri bırakabilir veya paketi daha küçük parçalara ayırır.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>Bir IP paketinde parçalara ayırma biti

Parçalara ayırma (DF) biti, IP protokolü üstbilgisindeki bir bayraktır. DF bit, gönderen ve alıcı arasındaki ağ cihazlarının paketi parçalara ayrılmadığını gösterir. Bu bit birçok nedenden dolayı ayarlanabilir. (Bir örnek için bu makalenin "yol MTU Keşfi" bölümüne bakın.) Bir ağ aygıtı, parçalara ayırma biti ayarlı bir paket aldığında ve bu paket cihazın arabirim MTU 'sunu aşarsa, cihazın paketi bırakması için standart davranış vardır. Cihaz, paketin özgün kaynağına geri bir ıCMP parçalanma gerekli iletisi gönderir.

#### <a name="performance-implications-of-fragmentation"></a>Parçalanmanın performans etkileri

Parçalanmanın olumsuz performans etkileri olabilir. Performans üzerindeki etkinin temel nedenlerinden biri, paketlerin parçalanması ve yeniden birleştirilmesinin CPU/bellek etkisidir. Bir ağ cihazının bir paketi ayırması gerektiğinde, parçalanma gerçekleştirmek için CPU/bellek kaynakları ayırması gerekecektir.

Paket yeniden birleştirildiğinde aynı şey olur. Ağ aygıtı, tüm parçaları alınana kadar depolarlar, böylece bunları özgün pakete yeniden ayrılabilir. Bu parçalama ve yeniden birleştirme süreci de gecikmeye neden olabilir.

Parçalanmanın diğer olası olumsuz performansı, parçalanmış paketlerin sıra dışına gelebileceğinden oluşur. Paketler sırada alındığında, bazı ağ aygıtı türleri bunları bırakabilir. Bu durumda, tüm paketin yeniden iletilmelidir.

Parçalar genellikle ağ güvenlik duvarları gibi güvenlik cihazları veya bir ağ cihazının Alma arabellekleri tükendiğinde bırakılır. Bir ağ cihazının Alım arabellekleri tükendiğinde, bir ağ aygıtı parçalanmış bir paketi yeniden atamalarla çalışıyor ancak paketi depolamak ve yeniden atamak için kaynaklara sahip değildir.

Parçalanma negatif bir işlem olarak görülebilir, ancak internet üzerinden farklı ağlar bağlarken parçalama desteği gerekir.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>MTU değiştirme avantajları ve sonuçları

Genel anlamda, MTU 'YU artırarak daha verimli bir ağ oluşturabilirsiniz. Aktarılan her paketin, özgün pakete eklenen üst bilgi bilgileri vardır. Parçalama daha fazla paket oluşturduğunda, daha fazla başlık yükü vardır ve bu da ağın daha az etkili olmasını sağlar.

İşte bir örnek. Ethernet üst bilgi boyutu, çerçeve tutarlılığı sağlamak için 14 bayt ve 4 baytlık bir çerçeve denetim sırasıdır. 1 2.000 baytlık paket gönderilirse, ağa 18 baytlık Ethernet ek yükü eklenir. Paket, 1.500 baytlık bir pakette ve 500 baytlık bir pakette parçalanmışsa, her pakette 18 baytlık Ethernet üstbilgisi ve toplam 36 bayt vardır.

MTU değerinin artırılması daha verimli bir ağ oluşturmayacağınızı unutmayın. Bir uygulama yalnızca 500 baytlık paketler gönderirse, MTU 'nun 1.500 bayt veya 9.000 bayt olmasına bakılmaksızın aynı üstbilgi ek yükü mevcut olacaktır. Ağ, yalnızca MTU 'dan etkilenen daha büyük paket boyutları kullanıyorsa daha etkili olur.

#### <a name="azure-and-vm-mtu"></a>Azure ve VM MTU

Azure VM 'Leri için varsayılan MTU 1.500 bayttır. Azure sanal ağ yığını, bir paketi 1.400 baytından parçalara kalkışmaya çalışacaktır.

Sanal ağ yığınının, VM 'Lerin 1.500 ' de bir MTU değeri olsa bile, paketleri 1.400 bayt boyutunda parçalar olduğundan, bu sanal ağ yığınının kendiliğinden verimsiz olmadığını unutmayın Ağ paketlerinin büyük bir yüzdesi 1.400 veya 1.500 bayttan çok daha küçüktür.

#### <a name="azure-and-fragmentation"></a>Azure ve parçalanma

Sanal ağ yığını "sıra dışı parçalar", diğer bir deyişle orijinal parçalara ayrılmış bir sıraya ulaşamayacak parçalanmış paketleri bırakmak üzere ayarlanır. Bu paketler temel olarak, Kasım 2018 ' de, FragmentSmack adlı bir ağ güvenlik açığı duyurulduğu için bırakılır.

FragmentSmack, Linux çekirdeğinin parçalanmış IPv4 ve IPv6 paketlerinin yeniden birleştirilme biçiminde bir hata olur. Uzak bir saldırgan, daha fazla CPU ve hedef sistemde hizmet reddine neden olabilecek pahalı parça yeniden birleştirme işlemlerini tetiklemek için bu kusuru kullanabilir.

#### <a name="tune-the-mtu"></a>MTU 'YU ayarlama

Diğer işletim sistemlerinde olduğu gibi bir Azure VM MTU 'u yapılandırabilirsiniz. Ancak, bir MTU yapılandırırken yukarıda açıklanan Azure 'da oluşan parçalanmayı göz önünde bulundurmanız gerekir.

Müşterileri VM 'leri artıracak şekilde teşvik ettik. Bu tartışma, Azure 'un MTU uygulayan ve parçalanma alma işlemlerinin ayrıntılarını açıklamak amacıyla hazırlanmıştır.

> [!IMPORTANT]
>Performansı artırmak için MTU 'un artırılması bilinmez ve uygulama performansı üzerinde olumsuz bir etkiye sahip olabilir.
>
>

#### <a name="large-send-offload"></a>Büyük gönderme boşaltması

Büyük gönderme boşaltması (LSO), paketlerin Ethernet bağdaştırıcısına ayrılmasını sağlayarak ağ performansını iyileştirebilir. LSO etkinleştirildiğinde TCP/IP yığını büyük bir TCP paketi oluşturur ve bunu iletmeden önce, Ethernet bağdaştırıcısına gönderir. LSO 'nin avantajı, CPU 'nun paketleri, MTU ile uyumlu olan ve bu işlemin donanımda çalıştırıldığı Ethernet arabirimine yük boşaltma ve yük boşaltma ile olan boyutlara göre serbest bırakmasıdır. LSO 'nin avantajları hakkında daha fazla bilgi edinmek için bkz. [büyük gönderme boşaltmasını destekleme](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

LSO etkinleştirildiğinde Azure müşterileri, paket yakalamalarını gerçekleştirirken büyük çerçeve boyutları görebilirler. Bu büyük çerçeve boyutları, bazı müşterilerin parçalanmasını düşündüğünü veya bir büyük MTU olmadığından büyük bir MTU kullanıldığını ortaya çıkmasına neden olabilir. LSO ile, Ethernet bağdaştırıcısı daha büyük bir TCP paketi oluşturmak için TCP/IP yığınına daha büyük bir en büyük kesim boyutu (paketler) verebilir. Bu sanal olmayan çerçevenin tamamı daha sonra Ethernet bağdaştırıcısına iletilir ve VM üzerinde gerçekleştirilen bir paket yakalama bölümünde görülebilir. Ancak, Ethernet bağdaştırıcısının MTU 'SU doğrultusunda, paket Ethernet bağdaştırıcısı tarafından çok daha küçük bir kareye bölünecektir.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>TCP, pencere ölçekleme ve PMTUD

#### <a name="tcp-maximum-segment-size"></a>En büyük TCP segment boyutu

TCP en büyük kesim boyutu (paketlerinde), TCP paketlerinin parçalanmasını önleyen TCP kesimlerinin boyutunu sınırlayan bir ayardır. İşletim sistemleri, genellikle bu formülü, bu formülü kullanarak bu formülü kullanır:

`MSS = MTU - (IP header size + TCP header size)`

IP üstbilgisi ve TCP üstbilgisi her biri 20 bayttır veya toplam 40 bayttır. Bu nedenle, MTU 1.500 olan bir arabirim 1.460 ' e sahip olacaktır. Ancak bu, yapılandırılabilir.

Bir kaynak ve hedef arasında TCP oturumu ayarlandığında, bu ayar TCP üç yönlü el sıkışmasında kabul edilir. Her iki taraf da bir sahiplik değeri gönderir ve ikisi de TCP bağlantısı için kullanılır.

Kaynak ve hedefin muzları 'nın, bu değeri belirleyecek tek etken olmadığına dikkat edin. Azure VPN Gateway dahil olmak üzere VPN ağ geçitleri gibi ara ağ cihazları, en iyi ağ performansını sağlamak için MTU 'YU kaynak ve hedefin bağımsız olarak ayarlayabilir.

#### <a name="path-mtu-discovery"></a>Yol MTU Keşfi

Bu anlaşılırsa, ancak kullanılabilecek gerçek bir, bu durum söz konusu olabilir. Bunun nedeni, kaynak ve hedef arasındaki yoldaki diğer ağ cihazlarının kaynak ve hedefe göre daha düşük bir MTU değeri olabilir. Bu durumda, MTU değeri paketten daha küçük olan cihaz paketi düşürülecektir. Cihaz, MTU değerini içeren bir ıCMP parçalanması (tür 3, kod 4) iletisini geri gönderir. Bu ıCMP iletisi kaynak konağın yol MTU 'sunu uygun şekilde azaltmasına olanak tanır. İşlem yol MTU Keşfi (PMTUD) olarak adlandırılır.

PMTUD işlemi verimsiz bir işlemdir ve ağ performansını etkiler. Bir ağ yolunun MTU değerini aşan paketler gönderildiğinde, paketlerin daha düşük bir en düşük düzeyde yeniden gönderilmesi gerekir. Gönderen ıCMP parçalanma gerekli iletisini almazsa, yoldaki bir ağ güvenlik duvarı (genellikle *Pmtud kara*olarak adlandırılır) nedeniyle, gönderici, bu kaynağı bir veya sürekli olarak yeniden aktarım yapması gerektiğini bilmez. Azure VM MTU 'YU arttırmayı önermiyoruz.

#### <a name="vpn-and-mtu"></a>VPN ve MTU

Kapsülleme gerçekleştiren VM 'Leri (IPSec VPN 'Leri gibi) kullanıyorsanız, paket boyutu ve MTU ile ilgili bazı ek hususlar vardır. VPN 'Ler paketlere daha fazla üst bilgi ekler, bu da paket boyutunu artırır ve daha küçük bir bir en az bir

Azure için TCP/üst sınırı 1.350 bayt ve tünel arabirimi MTU değerini 1.400 olarak ayarlamanızı öneririz. Daha fazla bilgi için [VPN cihazları ve IPSec/IKE parametreleri sayfasına](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)bakın.

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Gecikme süresi, gidiş dönüş süresi ve TCP pencere ölçeklendirmesi

#### <a name="latency-and-round-trip-time"></a>Gecikme süresi ve gidiş dönüş süresi

Ağ gecikmesi, fiber optik bir ağ üzerinden ışık hızına tabidir. TCP 'nin ağ aktarım hızı, iki ağ aygıtı arasındaki gidiş dönüş süresi (RTT) ile de etkili bir şekilde yönetilir.

| | | | |
|-|-|-|-|
|**Yolu**|**Uzaklık**|**Tek yönlü saat**|**RTT**|
|New York-San Francisco|4\.148 km|21 MS|42 ms|
|New York-Londra|5\.585 km|28 MS|56 MS|
|Yeni York-Sidney|15.993 km|80 MS|160 ms|

Bu tabloda iki konum arasındaki düz çizgi uzaklığı gösterilmektedir. Ağlarda, uzaklık genellikle düz çizgili uzaklıktan daha uzundur. Aşağıda, ışığın hızına göre en düşük RTT 'yi hesaplamak için basit bir formül verilmiştir:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Yayma hızı için 200 kullanabilirsiniz. Bu, bu ışığın 1 milisaniyeye göre hareket eder.

Örnek olarak San Francisco 'ye yeni bir New York bakalım. Düz çizgi uzaklığı 4.148 km 'dir. Bu değeri denkleme takmak için aşağıdakiler verilmiştir:

`Minimum RTT = 2 * (4,148 / 200)`

Denklemin çıktısı milisaniyedir.

En iyi ağ performansını elde etmek istiyorsanız, mantıksal seçenek aralarında en kısa uzaklığa sahip olan hedefleri seçer. Ayrıca, trafik yolunu iyileştirmek ve gecikme süresini azaltmak için Sanal ağınızı tasarlamanız gerekir. Daha fazla bilgi için bu makalenin "ağ tasarımı konuları" bölümüne bakın.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>TCP üzerinde gecikme ve gidiş dönüş süresi etkileri

Gidiş dönüş süresinin en yüksek TCP aktarım hızı üzerinde doğrudan etkisi vardır. TCP protokolünde, *pencere boyutu* , gönderenin alıcıdan alındı almasını GEREKTIREN bir TCP bağlantısı üzerinden gönderilebilecek maksimum trafik miktarıdır. 1\.460 TCP ve TCP penceresi boyutu 65.535 olarak ayarlandıysa, gönderen kişiden onay almak zorunda olmadan önce, gönderen, 45 paketleri gönderebilir. Gönderen onay almazsa, verileri yeniden gönderilir. Formül şu şekildedir:

`TCP window size / TCP MSS = packets sent`

Bu örnekte, 65.535/1.460, 45 ' e yuvarlanır.

Bu "onay bekleniyor" durumu, verilerin güvenilir bir şekilde teslim edilmesini sağlamaya yönelik bir mekanizma, RTT 'nin TCP performansını etkilemesine neden olur. Gönderenin bildirim için beklediği daha uzun süre, daha fazla veri göndermeden önce beklemesi gerekir.

Tek bir TCP bağlantısının en yüksek aktarım hızını hesaplamak için aşağıdaki formül aşağıda verilmiştir:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Bu tabloda tek bir TCP bağlantısının en fazla megabayt/saniye başına aktarım hızı gösterilmektedir. (Okunabilirlik için, ölçü birimi için megabayt kullanılır.)

| | | | |
|-|-|-|-|
|**TCP pencere boyutu (bayt)**|**RTT gecikmesi (MS)**|**Maksimum megabayt/saniye üretilen iş**|**Maksimum megabit/saniye işleme**|
|65,535|1\.|65,54|524,29|
|65,535|30|2,18|17,48|
|65,535|60|1,09|8,74|
|65,535|90|.73|5,83|
|65,535|120|.55|4.37|

Paketler kaybolursa, gönderici zaten gönderdiği verileri yeniden aktardığında bir TCP bağlantısının en yüksek aktarım hızı azaltılır.

#### <a name="tcp-window-scaling"></a>TCP pencere ölçeklendirmesi

TCP pencere ölçeklendirmesi, bir onaylama gerekmeden önce daha fazla verinin gönderilmesine izin vermek için TCP pencere boyutunu dinamik olarak artıran bir tekniktir. Önceki örnekte, bir onaylama gerekmeden önce 45 paket gönderilir. Bir onaylama gerekmeden önce gönderilebilecek paket sayısını artırırsanız, bir göndericinin onay bekleme süresini azaltmış olursunuz. Bu, TCP maksimum aktarım hızını artırır.

Bu tabloda bu ilişkiler gösterilmektedir:

| | | | |
|-|-|-|-|
|**TCP pencere boyutu (bayt)**|**RTT gecikmesi (MS)**|**Maksimum megabayt/saniye üretilen iş**|**Maksimum megabit/saniye işleme**|
|65,535|30|2,18|17,48|
|131.070|30|4.37|34,95|
|262.140|30|8,74|69,91|
|524.280|30|17,48|139,81|

Ancak TCP pencere boyutu için TCP üstbilgi değeri yalnızca 2 bayt uzunluğundadır, bu da alma penceresi için maksimum değer 65.535 ' dir. Maksimum pencere boyutunu artırmak için bir TCP pencere ölçek faktörü sunuldu.

Ölçek faktörü Ayrıca bir işletim sisteminde yapılandırabileceğiniz bir ayardır. Ölçek faktörleri kullanarak TCP pencere boyutunu hesaplama formülü aşağıda verilmiştir:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Aşağıda, bir pencere ölçek faktörü 3 ve pencere boyutu 65.535 için bir hesaplama verilmiştir:

`65,535 \* (2^3) = 262,140 bytes`

14 ölçek faktörü, TCP pencere boyutu olan 14 ' ün (izin verilen en yüksek uzaklığa) oluşur. TCP pencere boyutu 1.073.725.440 bayt (8,5 Gigabit) olacaktır.

#### <a name="support-for-tcp-window-scaling"></a>TCP pencere ölçeklendirmesi için destek

Windows, farklı bağlantı türleri için farklı ölçekleme faktörleri ayarlayabilir. (Bağlantı sınıfları veri merkezi, internet vb.) Pencere ölçeklendirme bağlantı `Get-NetTCPConnection` türünü görüntülemek için PowerShell komutunu kullanın:

```powershell
Get-NetTCPConnection
```

Her bir sınıfın değerlerini `Get-NetTCPSetting` görüntülemek için PowerShell komutunu kullanabilirsiniz:

```powershell
Get-NetTCPSetting
```

Windows 'daki ilk TCP pencere boyutunu ve TCP ölçeklendirme faktörünü `Set-NetTCPSetting` PowerShell komutunu kullanarak ayarlayabilirsiniz. Daha fazla bilgi için bkz. [set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Bunlar için `AutoTuningLevel`geçerli TCP ayarları şunlardır:

| | | | |
|-|-|-|-|
|**Oto Tuninglevel**|**Ölçeklendirme faktörü**|**Ölçek çarpanı**|**Maksimum pencere boyutunu hesaplama formülü<br/>**|
|Devre dışı|Yok.|Yok.|Pencere boyutu|
|Kısıtlı|4|2 ^ 4|Pencere boyutu * (2 ^ 4)|
|Yüksek oranda kısıtlanmış|2|2 ^ 2|Pencere boyutu * (2 ^ 2)|
|Normal|8|2 ^ 8|Pencere boyutu * (2 ^ 8)|
|Deneysel|14|2 ^ 14|Pencere boyutu * (2 ^ 14)|

Bu ayarlar, TCP performansını etkilemenin en olası nedenidir, ancak internet üzerindeki birçok farklı etkene Azure 'un denetimi dışında, TCP performansını da etkileyebilir.

#### <a name="increase-mtu-size"></a>MTU boyutunu artır

Daha büyük bir MTU daha büyük bir sahip olduğu için, MTU 'nun arttırıp 'nin TCP performansını artıramayacağını merak edebilirsiniz. Büyük olasılıkla değil. Yalnızca TCP trafiğinin ötesinde paket boyutunun olumlu ve olumsuz yönleri vardır. Daha önce anlatıldığı gibi, TCP verimlilik performansını etkileyen en önemli faktörler TCP pencere boyutu, paket kaybı ve RTT ' dir.

> [!IMPORTANT]
> Azure müşterilerinin sanal makinelerde varsayılan MTU değerini değiştirmesini önermiyoruz.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Hızlandırılmış ağ ve Alma Tarafı Ölçeklendirmesi

#### <a name="accelerated-networking"></a>Hızlandırılmış ağ iletişimi

Sanal makine ağ işlevleri, hem Konuk VM hem de hiper yönetici/ana bilgisayar üzerinde CPU kullanımı yoğun. Konak üzerinden geçişli her paket, tüm sanal ağ kapsülleme ve çözümlenmeden dahil olmak üzere konak CPU 'SU tarafından yazılımda işlenir. Bu nedenle, ana bilgisayardan geçen daha fazla trafik, CPU yükü artar. Ana bilgisayar CPU 'SU diğer işlemlerle meşgulse, bu da ağ aktarım hızını ve gecikme süresini de etkiler. Azure bu sorunu hızlandırılmış ağlarla gidermektedir.

Hızlandırılmış ağ, Azure 'un Şirket içi programlanabilir donanımı ve SR-ıOV gibi teknolojiler aracılığıyla tutarlı bir ytralow ağ gecikmesi sağlar. Hızlandırılmış ağ, Azure yazılım tanımlı ağ yığınının çoğunu CPU 'Lara ve FPGA tabanlı Smartnıc 'lere taşımaktadır. Bu değişiklik, son kullanıcı uygulamalarının işlem döngülerini geri kazanmasını sağlayarak VM 'ye daha az yük koyar, değişimi ve tutarsızlığı gecikmeden azaltır. Diğer bir deyişle, performans daha belirleyici olabilir.

Hızlandırılmış ağ, Konuk VM 'nin Konağı atlamasına ve bir konağın Smartnıc 'i ile doğrudan bir veri yolu kurmasını sağlayarak performansı geliştirir. Hızlandırılmış ağ avantajlarından bazıları şunlardır:

- **Saniye başına düşük gecikme süresi/daha yüksek paketler (PPS)** : Sanal anahtarın veri yolundan kaldırılması, ilke işleme için konakta harcadıkları zaman paketlerini ortadan kaldırır ve sanal MAKINEDE işlenebileceği paketlerin sayısını artırır.

- **Azaltılan değişim**: Sanal anahtar işleme, uygulanması gereken ilke miktarına ve işlemeyi gerçekleştiren CPU 'nun iş yüküne bağlıdır. İlke zorlamayı donanıma boşaltma, paketleri doğrudan VM 'ye teslim ederek bu değişkenliği, ana makineden VM iletişimini ve tüm yazılım kesmelerini ve bağlam anahtarlarını ortadan kaldırarak kaldırır.

- **AZALTıLMıŞ CPU kullanımı**: Konaktaki sanal anahtarı atlamak, ağ trafiğini işlemeye yönelik daha az CPU kullanımına neden oluyor.

Hızlandırılmış ağ kullanmak için, söz konusu sanal makine üzerinde açıkça etkinleştirmeniz gerekir. Yönergeler için bkz. [hızlandırılmış ağ Ile Linux sanal makinesi oluşturma](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) .

#### <a name="receive-side-scaling"></a>Alma Tarafı Ölçeklendirmesi

Alma Tarafı Ölçeklendirmesi (RSS), çok işlemcili bir sistemdeki birden fazla CPU arasında alma işlemini dağıtarak ağ trafiğinin alınmasını daha verimli bir şekilde dağıtan bir ağ sürücüsü teknolojisidir. Basit koşullarda, RSS bir sistemin yalnızca bir tane yerine tüm kullanılabilir CPU 'Ları kullandığından daha fazla alınan trafiği işlemesini sağlar. RSS 'nin daha teknik bir tartışması için bkz. [alma tarafı ölçeklendirmeyi giriş](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Bir VM 'de hızlandırılmış ağ etkinleştirildiğinde en iyi performansı elde etmek için RSS 'yi etkinleştirmeniz gerekir. RSS, hızlandırılmış ağ kullanmayan VM 'lerde da avantajlar sağlayabilir. RSS 'nin etkinleştirilip etkinleştirilmediğini ve nasıl etkinleştireceğinizi belirleme konusunda genel bir bakış için bkz. [Azure sanal makineler için ağ aktarım hızını iyileştirme](https://aka.ms/FastVM).

### <a name="tcp-timewait-and-timewait-assassination"></a>TCP TIME_WAIT ve TIME_WAIT Assassination

TCP TIME_WAIT, ağ ve uygulama performansını etkileyen başka bir yaygın ayardır. İstemci veya sunucu olarak çok sayıda yuva açan ve kapatan meşgul VM 'lerde (kaynak IP: kaynak bağlantı noktası + hedef IP: hedef bağlantı noktası), TCP 'nin normal işlemleri sırasında, belirli bir yuva uzun bir süre boyunca TIME_WAIT durumunda sona bitebilirler. TIME_WAIT durumu, kapatmadan önce herhangi bir yuva üzerinde herhangi bir ek verinin teslim edilmesini sağlamak için tasarlanmıştır. Bu nedenle TCP/IP yığınları genellikle istemcinin TCP SYN paketini sessizce bırakarak bir yuvanın yeniden kullanılmasını önler.

Bir yuvanın TIME_WAIT içinde olduğu sürenin miktarı yapılandırılabilir. 30 saniyeden 240 saniyeye kadar değişebilir. Yuvalar, sınırlı bir kaynaktır ve belirli bir zamanda kullanılabilen yuva sayısı yapılandırılabilir. (Kullanılabilir yuva sayısı genellikle 30.000 ' dir.) Kullanılabilir yuvalar tüketilirken veya istemcilerin ve sunucuların uyuşmayan TIME_WAIT ayarları varsa ve bir VM bir yuvayı bir TIME_WAIT durumunda yeniden kullanmaya çalışırsa, TCP SYN paketleri sessizce bırakılmakta olduğundan yeni bağlantılar başarısız olur.

Giden yuvalar için bağlantı noktası aralığı değeri genellikle bir işletim sisteminin TCP/IP yığını içinde yapılandırılabilir. TCP TIME_WAIT ayarları ve yuva yeniden kullanımı için aynı şey geçerlidir. Bu sayıların değiştirilmesi, ölçeklenebilirliği iyileştirebilir. Ancak, duruma bağlı olarak bu değişiklikler birlikte çalışabilirlik sorunlarına neden olabilir. Bu değerleri değiştirirseniz dikkatli olmanız gerekir.

Bu ölçeklendirme sınırlamasını karşılamak için TIME_WAIT Assassination kullanabilirsiniz. TIME_WAIT Assassination, yeni bağlantının IP paketindeki sıra numarası önceki bağlantıdan son paketin sıra numarasını aştığında olduğu gibi, bir yuvanın belirli durumlarda yeniden kullanılmasına izin verir. Bu durumda, işletim sistemi yeni bağlantının bağlanmasına izin verir (yeni SYN/ACK 'i kabul eder) ve bir TIME_WAIT durumundaki önceki bağlantıyı kapatmaya zorlar. Bu özellik, Azure 'daki Windows sanal makinelerinde desteklenir. Diğer VM 'lerde destek hakkında bilgi edinmek için işletim sistemi satıcısına danışın.

TCP TIME_WAIT ayarlarını ve kaynak bağlantı noktası aralığını yapılandırma hakkında bilgi edinmek için bkz. [ağ performansını geliştirmek için değiştirilebilen ayarlar](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Performansı etkileyebilecek sanal ağ faktörleri

### <a name="vm-maximum-outbound-throughput"></a>Maksimum VM çıkış hacmi

Azure, her biri farklı performans özellikleri karışımı olan çeşitli VM boyutları ve türleri sağlar. Bu yeteneklerden biri, saniye başına megabit (Mbps) cinsinden ölçülen ağ aktarım hızı (veya bant genişliğidir). Sanal makineler paylaşılan donanımda barındırıldığından, ağ kapasitesinin aynı donanımı kullanan sanal makineler arasında oldukça paylaşılması gerekir. Daha büyük sanal makinelere, daha küçük sanal makinelerden daha fazla bant genişliği ayrılır.

Her bir sanal makineye ayrılan ağ bant genişliği, sanal makineden çıkış (giden) trafiği üzerinden ölçülür. Sanal makineyi terk eden tüm ağ trafiği, hedefe bakılmaksızın ayrılan sınıra doğru sayılır. Örneğin, bir sanal makinede 1.000 Mbps sınırı varsa, bu sınır giden trafiğin aynı sanal ağdaki başka bir sanal makineye veya Azure dışında bir sanal makineye yönelik olup olmayacağını uygular.

Giriş doğrudan tarifeli değil veya sınırlı değil. Ancak, sanal makinenin gelen verileri işleme yeteneğini etkileyebilecek CPU ve depolama sınırları gibi başka etmenler de vardır.

Hızlandırılmış ağ, gecikme süresi, aktarım hızı ve CPU kullanımı dahil olmak üzere ağ performansını geliştirmek için tasarlanmıştır. Hızlandırılmış ağ, sanal makinenin aktarım hızını iyileştirebilir, ancak yalnızca sanal makinenin ayrılmış bant genişliğine sahip olabilir.

Azure sanal makinelerinde en az bir ağ arabirimi eklenmiş olmalıdır. Birkaç tane olabilir. Bir sanal makineye ayrılan bant genişliği, makineye bağlı tüm ağ arabirimleri genelinde giden tüm trafiğin toplamıdır. Diğer bir deyişle, makineye kaç ağ arabirimi iliştirildiğine bakılmaksızın bant genişliği sanal makine bazında ayrılır.

Beklenen giden aktarım hızı ve her VM boyutu tarafından desteklenen ağ arabirimlerinin sayısı, [Azure 'Daki Windows sanal makinelerinin boyutları](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)bölümünde ayrıntılıdır. En yüksek aktarım hızını görmek için, **genel amaçlı**gibi bir tür seçin ve elde edilen sayfada boyut serisiyle ilgili bölümü bulun (örneğin, "dv2-Series"). Her seri için, "en büyük NIC/beklenen ağ bant genişliği (MB/sn)" başlıklı son sütunda ağ belirtimleri sağlayan bir tablo vardır.

Aktarım hızı sınırı, sanal makine için geçerlidir. Aktarım hızı şu faktörlerden etkilenmez:

- **Ağ arabirimi sayısı**: Bant genişliği sınırı, sanal makineden giden tüm trafiğin toplamına uygulanır.

- **Hızlandırılmış ağ**: Bu özellik, yayımlanan sınırı elde etmek için faydalı olabilir, ancak sınırı değiştirmez.

- **Trafik hedefi**: Tüm hedefler giden sınıra doğru sayılır.

- **Protokol**: Tüm protokollerin tüm giden trafiği sınıra doğru sayılır.

Daha fazla bilgi için bkz. [sanal makine ağ bant genişliği](https://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Internet performans konuları

Bu makalede açıklandığı gibi, internet üzerindeki ve Azure 'un dışındaki etmenler ağ performansını etkileyebilir. Bu faktörlerden bazıları şunlardır:

- **Gecikme süresi**: İki hedef arasındaki gidiş dönüş süresi, ara ağlardaki sorunlardan, "en kısa" uzaklık yolundan geçen ve alt düzey eşleme yollarından oluşan trafiğe göre etkilenebilir.

- **Paket kaybı**: Paket kaybı, ağ tıkanıklığı, fiziksel yol sorunları ve düşük performanslı ağ cihazlarının oluşmasına neden olabilir.

- **MTU boyutu/parçalanma**: Yol üzerinde parçalama, veri gelişmesinde veya paketlerin teslimini etkileyebilecek, hatta paketlerin teslimini etkileyebilecek gecikmelere neden olabilir.

İzleme yolu, bir kaynak cihaz ile hedef cihaz arasındaki her ağ yolu boyunca ağ performansı özelliklerini (paket kaybı ve gecikme gibi) ölçmek için iyi bir araçtır.

### <a name="network-design-considerations"></a>Ağ tasarımı konuları

Bu makalede daha önce bahsedilen noktalar ile birlikte, bir sanal ağın topolojisi ağın performansını etkileyebilir. Örneğin, tek hub sanal ağına genel olarak trafiği geri tanıtan bir hub ve bağlı bileşen tasarımı, genel ağ performansını etkileyecek ağ gecikmesi sağlar.

Ağ trafiğinin geçtiği ağ cihazlarının sayısı, genel gecikmeyi de etkileyebilir. Örneğin, hub ve bağlı bileşen tasarımında trafik, internet 'e geçiş yapmadan önce bir bağlı ağ sanal gereci ve hub sanal gerecinden geçerse, ağ sanal cihazları gecikme süresini ortaya çıkarabilir.

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure bölgeleri, sanal ağlar ve gecikme

Azure bölgeleri, genel bir coğrafi alanda bulunan birden çok veri merkezinden oluşur. Bu veri merkezleri, her birinin yanında fiziksel olarak görünmeyebilir. Bazı durumlarda, 10 kilometreye kadar ayrılırsınız. Sanal ağ, Azure fiziksel veri merkezi ağının en üstünde bulunan mantıksal bir yer. Bir sanal ağ, veri merkezinde belirli bir ağ topolojisini göstermez.

Örneğin, aynı sanal ağ ve alt ağdaki iki VM farklı dolaplar, satırlar veya hatta veri merkezlerinde olabilir. Bunlar, fiber optik kablo veya fiber optik kablonun kilometreler tarafından ayrılabilir. Bu çeşitleme, farklı VM 'Ler arasında değişken gecikme süresi (birkaç milisaniye fark) ortaya çıkarabilir.

VM 'lerin coğrafi yerleşimi ve iki VM arasındaki olası gecikme süresi, kullanılabilirlik kümelerinin ve Kullanılabilirlik Alanları yapılandırmasından etkilenebilir. Ancak bölgedeki veri merkezleri arasındaki mesafe bölgeye özeldir ve birincil olarak bölgedeki veri merkezi topolojisinden etkilenir.

### <a name="source-nat-port-exhaustion"></a>Kaynak NAT bağlantı noktası tükenmesi

Azure 'daki bir dağıtım, genel İnternet ve/veya genel IP alanında Azure dışındaki uç noktalarla iletişim kurabilir. Bir örnek giden bir bağlantı başlattığında, Azure özel IP adresini dinamik olarak genel bir IP adresiyle eşleştirir. Azure bu eşlemeyi oluşturduktan sonra, giden kaynaklı akış için geri dönüş trafiği akışın kaynaklandığı özel IP adresine de ulaşabilir.

Her giden bağlantı için Azure Load Balancer belirli bir süre için bu eşlemeyi koruması gerekir. Azure 'un çok kiracılı doğası gereği, her VM için her giden akış için bu eşlemenin saklanması kaynak kullanımı yoğun olabilir. Bu nedenle, ve Azure sanal ağının yapılandırmasına bağlı olarak ayarlanan sınırlar vardır. Ya da daha kesin söylemek için, bir Azure VM belirli bir zamanda yalnızca belirli sayıda giden bağlantı oluşturabilir. Bu sınırlara ulaşıldığında, VM daha fazla giden bağlantı yapamaz.

Ancak bu davranış yapılandırılabilir. SNAT ve SNAT bağlantı noktası tükenmesi hakkında daha fazla bilgi için [Bu makaleye](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)bakın.

## <a name="measure-network-performance-on-azure"></a>Azure 'da ağ performansını ölçme

Bu makaledeki performans üst sınır sayısı, iki VM arasında ağ gecikmesi/gidiş dönüş süresi (RTT) ile ilgilidir. Bu bölümde, gecikme/RTT sınama ve TCP performans ve VM ağ performansının nasıl test yapılacağı hakkında bazı öneriler sunulmaktadır. Daha önce açıklanan TCP/IP ve ağ değerlerini bu bölümde açıklanan teknikleri kullanarak ayarlayabilir ve performans testi yapabilirsiniz. Daha önce sağlanmış olan hesaplamalara gecikme süresi, MTU, UMS ve pencere boyutu değerleri ekleyebilir ve teorik en büyük değerleri, test sırasında gözlemlediğiniz gerçek değerlerle karşılaştırabilirsiniz.

### <a name="measure-round-trip-time-and-packet-loss"></a>Gidiş dönüş süresi ve paket kaybını ölçme

TCP performansı, RTT ve paket kaybını yoğun bir şekilde kullanır. Windows ve Linux 'ta kullanılabilen PING yardımcı programı, RTT ve paket kaybını ölçmenin en kolay yolunu sağlar. PING çıkışı, bir kaynak ve hedef arasındaki en düşük/en yüksek/ortalama gecikme süresini gösterir. Ayrıca, paket kaybını da gösterir. PING varsayılan olarak ıCMP protokolünü kullanır. PsPing kullanarak TCP RTT 'yi test edebilirsiniz. Daha fazla bilgi için bkz. [Psping](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Bir TCP bağlantısının gerçek performansını ölçme

NTttcp, bir Linux veya Windows VM 'nin TCP performansını test etmeye yönelik bir araçtır. Çeşitli TCP ayarlarını değiştirebilir ve ardından NTttcp kullanarak avantajları test edebilirsiniz. Daha fazla bilgi için şu kaynaklara bakın:

- [Bant genişliği/Işleme testi (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [NTttcp yardımcı programı](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Bir sanal makinenin gerçek bant genişliğini ölçme

Iperf adlı bir araç kullanarak farklı VM türlerinin performansını, hızlandırılmış ağ iletişimini ve benzerlerini test edebilirsiniz. Iperf, Linux ve Windows üzerinde de kullanılabilir. Iperf, genel ağ aktarım hızını test etmek için TCP veya UDP kullanabilir. Iperf TCP üretilen iş sınamaları, bu makalede ele alınan faktörlerden (gecikme süresi ve RTT gibi) etkilenir. En yüksek aktarım hızını test etmek istiyorsanız UDP daha iyi sonuç verebilir.

Daha fazla bilgi için şu makalelere bakın:

- [ExpressRoute ağ performansı sorunlarını giderme](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Sanal ağa yönelik VPN aktarım hızını doğrulama](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Verimsiz TCP davranışlarını Algıla

Paket yakalamalarında, Azure müşterileri, ağ performans sorunlarını gösterebilen TCP bayraklarıyla (SACK, d/Azure ACK, yeniden aktarım ve hızlı yeniden aktarım) sahip TCP paketleri görebilir. Bu paketler özellikle, paket kaybından kaynaklanan ağ verimsizlikleri belirtir. Ancak paket kaybı, Azure performans sorunlarından kaynaklanıyor olabilir. Performans sorunları, uygulama sorunlarının, işletim sistemi sorunlarının veya doğrudan Azure platformuyla ilgili olmayan diğer sorunların sonucu olabilir.

Ayrıca, bazı yeniden aktarım ve yinelenen ACK 'ler bir ağda normal olduğunu unutmayın. TCP protokolleri güvenilir olacak şekilde oluşturulmuştur. Bir paket yakalamadaki bu TCP paketlerinin kanıtları, aşırı olmadığı müddetçe bir systemik ağ sorunu belirtmeyebilir.

Yine de bu paket türleri, bu makalenin diğer bölümlerinde ele alınan nedenlerden dolayı TCP aktarım hızının en yüksek performansı elde olmadığını gösteren bir göstergeler.

## <a name="next-steps"></a>Sonraki adımlar

Azure VM 'Ler için TCP/IP performans ayarlaması hakkında bilgi edindiğinize göre, [sanal ağları planlama](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) [ve sanal ağları bağlama ve yapılandırma hakkında daha fazla bilgi edinmek](https://docs.microsoft.com/azure/virtual-network/)için diğer konuları okumak isteyebilirsiniz.
