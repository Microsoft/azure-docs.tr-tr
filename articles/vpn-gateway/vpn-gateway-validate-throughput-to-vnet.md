---
title: Microsoft Azure Sanal Ağ VPN aktarım hızını doğrulama | Microsoft Docs
description: Bu belgenin amacı, kullanıcının şirket içi kaynaklarından bir Azure sanal makinesine ağ aktarım hızını doğrulaması için yardım sağlamaktır.
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 05/29/2019
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: 9c2f50c49037305663330a3c455e40291b9e6242
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058810"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Bir sanal ağa VPN aktarım hızını doğrulama

VPN Ağ Geçidi bağlantısı, Azure içindeki sanal ağınız ile şirket içi BT altyapınız arasında güvenli ve şirketler arası bağlantı kurmanıza olanak sağlar.

Bu makalede, şirket içi kaynaklardan bir Azure sanal makinesine (VM) ağ aktarım hızı doğrulaması gösterilmektedir.

> [!NOTE]
> Bu makale, yaygın sorunları tanılamaya ve çözmeye yardımcı olmak için tasarlanmıştır. Aşağıdaki bilgileri kullanarak sorunu çözeemiyorsanız, [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="overview"></a>Genel Bakış

VPN Ağ Geçidi bağlantısı aşağıdaki bileşenleri içerir:

* Şirket içi VPN cihazı ( [DOĞRULANAN VPN cihazlarının](vpn-gateway-about-vpn-devices.md#devicetable)listesini görüntüleyin.)
* Genel internet
* Azure VPN ağ geçidi
* Azure VM

Aşağıdaki diyagramda, VPN aracılığıyla bir Azure sanal ağı ile şirket içi ağın mantıksal bağlantısı gösterilmektedir.

![VPN kullanarak müşteri ağının MSFT ağına mantıksal bağlantı](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Beklenen maksimum giriş/çıkış sayısını hesapla

1. Uygulamanızın temel aktarım hızı gereksinimlerini saptayın.
1. Azure VPN ağ geçidi verimlilik limitlerinizi belirleme. Yardım için, [VPN Gateway hakkında](vpn-gateway-about-vpngateways.md#gwsku)konusunun "ağ geçidi SKU 'ları" bölümüne bakın.
1. VM boyutunuz için [Azure VM işleme kılavuzunu](../virtual-machines/virtual-machines-windows-sizes.md) saptayın.
1. Internet servis sağlayıcınız (ISS) bant genişliğinizi belirleme.
1. VM, VPN Gateway veya ISS 'nin en az bant genişliğini alarak beklenen aktarım hızını hesaplayın; saniye başına megabit (/) cinsinden ölçülen (/) sekiz (8) olarak ölçülür.

Hesaplanan aktarım hızı uygulamanızın temel aktarım hızı gereksinimlerini karşılamıyorsa, performans sorunu olarak tanımladığınız kaynağın bant genişliğini artırmanız gerekir. Azure VPN Gateway yeniden boyutlandırmak için bkz. [ağ GEÇIDI SKU 'Sunu değiştirme](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Bir sanal makineyi yeniden boyutlandırmak için bkz. [bir VM 'Yi yeniden boyutlandırma](../virtual-machines/virtual-machines-windows-resize-vm.md). Beklenen Internet bant genişliğiyle karşılaşamıyorsanız ISS 'nize da başvurabilirsiniz.

> [!NOTE]
> VPN Gateway üretilen iş, tüm Site-to-Site\VNET-to-VNET veya Noktadan siteye bağlantıları toplasıdır.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Performans araçlarını kullanarak ağ aktarım hızını doğrulama

Bu doğrulama yoğun olmayan saatlerde gerçekleştirilmelidir, çünkü sınama sırasında VPN tüneli işleme doygunluğu doğru sonuçlar vermez.

Bu test için kullandığımız araç, hem Windows hem de Linux 'ta ve hem istemci hem de sunucu modlarına sahip olan Iperf 'dir. Windows VM 'Leri için 3Gbps ile sınırlıdır.

Bu araç diske okuma/yazma işlemi gerçekleştirmez. Yalnızca bir uçtan diğerine kendi kendine oluşturulmuş TCP trafiği üretir. İstemci ve sunucu düğümleri arasında kullanılabilir bant genişliğini ölçen deneme tabanlı istatistikler oluşturur. İki düğüm arasında test edilirken, bir düğüm sunucu olarak davranır ve diğer düğüm istemci gibi davranır. Bu test tamamlandıktan sonra düğümlerin rollerini her iki düğümdeki hem karşıya yükleme hem de indirme performansını test etmek için tersine çevirmeyi öneririz.

### <a name="download-iperf"></a>Iperf 'yi indir

[Iperf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip)'yi indirin. Ayrıntılar için bkz. [Iperf belgeleri](https://iperf.fr/iperf-doc.php).

 > [!NOTE]
 > Bu makalede ele alınan üçüncü taraf ürünleri, Microsoft 'tan bağımsız şirketler tarafından üretilmektedir. Microsoft bu ürünlerin performansı veya güvenilirliği hakkında hiçbir garanti vermez.

### <a name="run-iperf-iperf3exe"></a>Iperf (iperf3. exe) Çalıştır

1. Trafiğe izin veren bir NSG/ACL kuralını etkinleştirin (Azure VM 'de genel IP adresi testi için).

1. Her iki düğümde de 5001 numaralı bağlantı noktası için bir güvenlik duvarı özel durumu etkinleştirin.

   **Windows:** Yönetici olarak aşağıdaki komutu çalıştırın:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Test tamamlandığında kuralı kaldırmak için şu komutu çalıştırın:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux:** Azure Linux görüntülerinin izin veren güvenlik duvarları vardır. Bir bağlantı noktasında dinleme yapan bir uygulama varsa, trafiğe izin verilir. Güvenli hale getirilen özel görüntülerin, açıkça açılan bağlantı noktalarına ihtiyacı olabilir. Ortak Linux işletim sistemi katmanı güvenlik duvarları `iptables`, `ufw`, veya `firewalld`içerir.

1. Sunucu düğümünde, iperf3. exe ' nin ayıklandığı dizine geçin. Ardından sunucu modunda Iperf komutunu çalıştırın ve aşağıdaki komutlar olarak 5001 numaralı bağlantı noktasını dinlemek üzere ayarlayın:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > 5001 numaralı bağlantı noktası, ortamınızdaki belirli güvenlik duvarı kısıtlamaları için özelleştirilebilir.

1. İstemci düğümünde, Iperf aracının ayıklandığı dizine geçin ve aşağıdaki komutu çalıştırın:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   İstemci, 5001 numaralı bağlantı noktası üzerinde otuz saniyelik trafiği sunucusuna yönlendirdi. '-P ' bayrağı, sunucu düğümüne 32 eş zamanlı bağlantı yaptığımız anlamına gelir.

   Aşağıdaki ekranda bu örnekteki çıktı gösterilmektedir:

   ![Output](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. SEÇIM Test sonuçlarını korumak için şu komutu çalıştırın:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Önceki adımları tamamladıktan sonra, rol tersine çevrilmiş şekilde aynı adımları yürütün, böylece sunucu düğümü artık istemci düğümü olur ve tam tersi de geçerlidir.

> [!Note]
> Iperf tek araç değildir. [NTttcp, test için alternatif bir çözümdür](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing).

## <a name="test-vms-running-windows"></a>Windows çalıştıran VM 'Leri test etme

### <a name="load-latteexe-onto-the-vms"></a>Yük Lat. exe ' yi VM 'lere yükleme

[Latte. exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b) ' nin en son sürümünü indirin

Latte. exe ' yi ayrı bir klasöre yerleştirmeyi düşünün, örneğin`c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Windows Güvenlik Duvarı aracılığıyla latte. exe ' ye izin ver

Alıcı üzerinde, latte. exe trafiğinin gelmesini sağlamak için Windows güvenlik duvarında bir Izin verme kuralı oluşturun. Gelen belirli TCP bağlantı noktalarına izin vermek yerine, tüm latte. exe programının adına göre kullanılmasına izin vermek en kolay yoldur.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Bu şekilde Windows Güvenlik Duvarı aracılığıyla latte. exe ' ye izin ver

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Örneğin, latte. exe ' yi "c:\Tools" klasörüne kopyaladıysanız, bu komut şu şekilde olur:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Gecikme testleri çalıştırma

ALıCı üzerinde latte. exe dosyasını başlatın (PowerShell 'den değil, CMD 'den çalıştırın):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Alanlarda 65.000 yinelemelerinin çevresinde, temsilci sonuçları döndürmek için yeterince uzun.

Kullanılabilir herhangi bir bağlantı noktası numarası iyidir.

VM 'nin IP adresi 10.0.0.4 ise, bu şöyle görünür

`latte -c -a 10.0.0.4:5005 -i 65100`

Gönderen üzerinde latte. exe dosyasını başlatın (PowerShell 'den değil, CMD 'den Çalıştır)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

Elde edilen komut "Client" veya sender olduğunu göstermek için "-c" eklenmesi dışında alıcıda aynıdır

`latte -c -a 10.0.0.4:5005 -i 65100`

Sonuçları bekle. VM 'Lerin ne kadar uzaklıkta olduğuna bağlı olarak, tamamlanması birkaç dakika sürebilir. Daha uzun testleri çalıştırmadan önce başarı için test etmek üzere daha az yinelemeden başlamak isteyebilirsiniz.

## <a name="test-vms-running-linux"></a>Linux çalıştıran VM 'Leri test etme

VM 'Leri test etmek için [SockPerf](https://github.com/mellanox/sockperf) kullanın.

### <a name="install-sockperf-on-the-vms"></a>VM 'Lere SockPerf 'i yükler

Linux VM 'lerinde (Gönderen ve alıcı), sanal makinelerinize SockPerf hazırlamak için şu komutları çalıştırın:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS/RHEL-GIT ve diğer yardımcı araçları yükler

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu-GIT ve diğer yardımcı araçları yükler

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash-tümü

Bash komut satırından (git 'in yüklü olduğunu varsayar)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

Bunun daha yavaş olması birkaç dakika sürebilir

`make`

Yüklemeyi hızlı yapın

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>VM 'lerde SockPerf çalıştırma

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Yüklemeden sonra örnek komutlar. Sunucu/alıcı-sunucunun IP 'si 10.0.0.4 olduğunu varsayar

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>İstemci-sunucunun IP 'si 10.0.0.4 olduğunu varsayar

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> VM ve ağ geçidi arasında üretilen işleme testi sırasında ara atlama (ör. Sanal Gereç) olmadığından emin olun.
> Yukarıdaki Iperf/NTTTCP testlerinden gelen önemli sonuçlar (genel verimlilik açısından) varsa, sorunun olası temel nedenlerini öğrenmek için lütfen aşağıdaki makaleye başvurun: https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

Özellikle, istemci ve sunucudan paralel olarak toplanan paket yakalama izlemelerinin (Wireshark/Ağ İzleyicisi) analizi, hatalı performans değerlendirmelerinde yardımcı olacaktır. Bu izlemelerde paket kaybı, yüksek gecikme süresi, MTU boyutu yer alabilir. Parçalama, TCP 0 penceresi, sıra dışı parçalar vb.

## <a name="address-slow-file-copy-issues"></a>Yavaş dosya kopyalama sorunlarını giderme

Önceki adımlarla (Iperf/NTTTCP/vb.) birlikte değerlendirilen genel aktarım hızı iyi olsa bile, Windows Gezgini 'ni kullanırken veya bir RDP oturumunu sürükleyip bırakarak yavaş dosya kopyalama işlemi yaşayabilirsiniz. Bu sorun, normalde aşağıdaki faktörlerden biri veya her ikisi nedeniyle oluşur:

* Windows Gezgini ve RDP gibi dosya kopyalama uygulamaları, dosyaları kopyalarken birden çok iş parçacığı kullanmaz. Daha iyi performans için, dosyaları 16 veya 32 iş parçacığı kullanarak kopyalamak üzere [RichCopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) gibi çok iş parçacıklı bir dosya kopyalama uygulaması kullanın. RichCopy içindeki dosya kopyası için iş parçacığı numarasını değiştirmek üzere **eylem** > **kopyalama seçenekleri** > **dosya kopyalama**' ya tıklayın.

   ![Yavaş dosya kopyalama sorunları](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Tüm uygulamalar aynı değildir ve tüm uygulamalar/işlemler tüm iş parçacıklarından yararlanır. Testi çalıştırırsanız bazı iş parçacıklarının boş olduğunu görebilir ve doğru işleme sonuçları sağlayamayacağız.
   > Uygulama dosyası aktarım performansınızı denetlemek için, uygulamanın veya dosya aktarımının en iyi iş verimini bulmak için iş parçacığı sayısını art arda veya azalmış olarak artırarak çok iş parçacığı kullanın.

* VM diski okuma/yazma hızı yetersiz. Daha fazla bilgi için bkz. [Azure Storage sorunlarını giderme](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Şirket içi cihaza yönelik dış arabirim

Azure 'un yerel ağ geçidinde VPN üzerinden ulaşmasını istediğiniz şirket içi aralıkların alt ağları belirtiliyor. Aynı anda, Azure 'daki VNET adres alanını şirket içi cihaza tanımlayın.

* **Rota tabanlı ağ geçidi**: Rota temelli VPN’lerle ilgili ilke veya trafik seçici herhangi birinden herhangi birine (veya joker karakterler) olarak yapılandırılır.

* **Ilke tabanlı ağ geçidi**: İlke temelli VPN'ler, şirket içi ağınızla Azure VNet'iniz arasında adres öneklerinin birleşimleri temelindeki IPsec tüneller üzerinden paketleri şifreler ve yönlendirirler. İlke (veya Trafik Seçici) çoğunlukla VPN yapılandırmasında bir erişim listesi olarak tanımlanır.

* **UsePolicyBasedTrafficSelector** bağlantıları: ("UsePolicyBasedTrafficSelectors" bir bağlantı $true Için, Azure VPN ağ geçidini şirket içi Ilke tabanlı VPN güvenlik duvarına bağlanacak şekilde yapılandırır. PolicyBasedTrafficSelectors 'ı etkinleştirirseniz, VPN cihazınızın, şirket içi ağ (yerel ağ geçidi) öneklerinizin ve Azure sanal ağ öneklerine ait tüm birleşimleriyle tanımlanmış olan eşleşen trafik seçicilerini içerdiğinden emin olmanız gerekir (yerine herhangi bir-herhangi bir.

Uygun olmayan yapılandırma tünel, paket düşmesi, hatalı verimlilik ve gecikme süresi içinde sık sık bağlantı kesilmesine neden olabilir.

## <a name="check-latency"></a>Gecikme süresini denetle

Aşağıdaki araçları kullanarak gecikme süresini kontrol edebilirsiniz:

* WinMTR
* Tcpizleme Oute
* `ping`Ayrıca `psping` (Bu araçlar, RTT için iyi bir tahmin sağlayabilir, ancak her durumda kullanılamaz.)

![Gecikme süresini denetle](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

MS ağ omurgasını girmeden önce herhangi bir sıçramadan yüksek gecikmeli bir ani artış fark ederseniz, Internet servis sağlayıcınızla daha fazla araştırmalar yapmaya devam etmek isteyebilirsiniz.

"Msn.net" içindeki atlamalardan büyük, olağan dışı gecikme bir ani artış fark ediyorsanız, daha fazla araştırma için lütfen MS desteği ile iletişime geçin.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi veya yardım için aşağıdaki bağlantıyı inceleyin:

* [Microsoft Desteği](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
