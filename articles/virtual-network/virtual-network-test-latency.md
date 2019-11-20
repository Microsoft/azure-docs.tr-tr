---
title: Azure sanal makineleri için Azure sanal makinelerini sınama ağ gecikmesi | Microsoft Docs
description: Bir sanal ağdaki Azure sanal makineler arasındaki ağ gecikmesini test etme hakkında bilgi edinin
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 760a181b4459db28d3a6eee5022cc0f984c4bee0
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588284"
---
# <a name="testing-network-latency"></a>Sınama ağı gecikmesi

Görev için tasarlanan bir araçla ağ gecikme süresinin ölçülmesi en doğru sonuçları verecektir. SockPerf gibi genel kullanıma sunulan araçlar (Linux için) ve latte (Windows için), uygulama gecikmesi gibi diğer gecikme türlerini dışlarken ağ gecikmesini ayırabilecek ve ölçmeye yönelik araçların örnekleridir. Bu araçlar, TCP ve UDP gibi uygulama performansını etkileyen ağ trafiği türüne odaklanmaktadır. Ping gibi diğer yaygın bağlantı araçları bazen gecikme süresini ölçmek için kullanılabilir, ancak bu sonuçlar gerçek iş yüklerinde kullanılan ağ trafiği temsilcisi olmayabilir. Bunun nedeni, bu araçların çoğunun uygulama trafiğinden farklı şekilde işlenebileceği ıCMP protokolünü kullanmasına ve sonuçların TCP ve UDP kullanan iş yükleri için geçerli olmayabilir. Çoğu uygulama tarafından kullanılan protokollerin doğru ağ gecikmesi testi için, SockPerf (Linux için) ve latte (Windows için) en ilgili sonuçları üretir. Bu belgede bu araçların her ikisi de ele alınmaktadır.

## <a name="overview"></a>Genel Bakış

Biri gönderici ve diğeri alıcı olarak olmak üzere iki sanal makine kullanarak, gidiş dönüş süresini (RTT) ölçmek için her iki yönde paket göndermek ve almak üzere iki yönlü bir iletişim kanalı oluşturulur.

Bu adımlar, iki sanal makine (VM) ile iki fiziksel bilgisayar arasında ağ gecikmesini ölçmek için kullanılabilir. Gecikme ölçümleri aşağıdaki senaryolar için yararlı olabilir:

- Dağıtılan VM 'Ler arasındaki ağ gecikmesi için bir kıyaslama oluşturma
- Ağ gecikmesi içindeki değişikliklerin etkilerini, ilgili değişiklikler yapıldıktan sonra karşılaştırın:
  - Yapılandırma değişiklikleri de dahil olmak üzere işletim sistemi veya ağ yığını yazılımı
  - Bir bölgeye veya PPG 'ye dağıtma gibi VM dağıtım yöntemi
  - Hızlandırılmış ağ veya boyut değişiklikleri gibi VM özellikleri
  - Yönlendirme veya filtreleme değişiklikleri gibi sanal ağ

### <a name="tools-for-testing"></a>Test araçları
Gecikme süresini ölçmek için, biri Windows tabanlı sistemler için ve Linux tabanlı sistemler için olmak üzere iki farklı seçeneğe sahipsiniz

Windows için: latte. exe (Windows) [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Linux için: SockPerf (Linux) [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

Bu araçların kullanılması, yalnızca TCP veya UDP yükü teslim sürelerinin, uygulamalar tarafından kullanılmamış olan veya ıCMP (ping) ya da diğer paket türlerini ölçmesini sağlar ve performanslarını etkilemez.

### <a name="tips-for-an-optimal-vm-configuration"></a>En iyi VM yapılandırmasına yönelik ipuçları:

- Windows veya Linux 'un en son sürümünü kullanma
- En iyi sonuçlar için hızlandırılmış ağı etkinleştirin
- [Azure yakınlık yerleşimi grubuyla](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) VM 'leri dağıtma
- Daha büyük VM 'ler genellikle daha küçük VM 'lerden daha iyi gerçekleştirilir

### <a name="tips-for-analysis"></a>Analiz ipuçları

- Dağıtım, yapılandırma ve iyileştirmeler tamamlandıktan hemen sonra bir taban çizgisi oluşturun
- Yeni sonuçları her zaman bir taban çizgisi ile karşılaştırın veya diğer bir testten, denetimli değişikliklerle diğerine karşılaştırın
- Değişiklikler gözlemlendiğinde veya planlanarak testleri Yinele


## <a name="testing-vms-running-windows"></a>Windows çalıştıran VM 'Leri test etme

## <a name="get-latteexe-onto-the-vms"></a>Sanal makinelere latte. exe dosyasını alın

En son sürümü indirin: [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Latte. exe dosyasını c:\Tools gibi ayrı bir klasöre yerleştirmeyi düşünün

## <a name="allow-latteexe-through-the-windows-firewall"></a>Windows Güvenlik Duvarı aracılığıyla latte. exe ' ye izin ver

ALıCı üzerinde, latte. exe trafiğinin gelmesini sağlamak için Windows güvenlik duvarında bir Izin verme kuralı oluşturun. Gelen belirli TCP bağlantı noktalarına izin vermek yerine, tüm latte. exe programının adına göre kullanılmasına izin vermek en kolay yoldur.

Windows Güvenlik Duvarı aracılığıyla şu şekilde latte. exe ' ye izin verin:

netsh advfirewall firewall add rule program =\<PATH\>\\latte. exe adı =&quot;latte&quot; Protocol = any dir = in Action = Allow Enable = Yes profile = ANY


Örneğin, latte. exe ' yi &quot;c:\\araçları&quot; klasörüne kopyaladıysanız, bu komut şu şekilde olur:

```cmd
netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

## <a name="running-latency-tests"></a>Gecikme süresi testleri çalıştırma

ALıCı üzerinde latte. exe dosyasını başlatın (PowerShell 'den değil, CMD 'den çalıştırın):

Latte-bir &lt;alıcı IP adresi&gt;:&lt;bağlantı noktası&gt;-i &lt;yinelemeleri&gt;

Alanlarda 65.000 yinelemelerinin çevresinde, temsilci sonuçları döndürmek için yeterince uzun.

Kullanılabilir herhangi bir bağlantı noktası numarası iyidir.

VM 'nin IP adresi 10.0.0.4 ise şöyle görünür:

```cmd
latte -a 10.0.0.4:5005 -i 65100
```

Gönderen üzerinde latte. exe dosyasını başlatın (PowerShell 'den değil, CMD 'den çalıştırın):

Latte-c-a \<alıcı IP adresi\>:\<bağlantı noktası\>-i \<yinelemeleri\>


Elde edilen komut, bu &quot;istemci&quot; veya gönderici olduğunu göstermek için &quot;-c&quot; eklenmesi hariç alıcı ile aynıdır:

```cmd
latte -c -a 10.0.0.4:5005 -i 65100
```

Sonuçları bekle. VM 'Lerin ne kadar uzaklıkta olduğuna bağlı olarak, tamamlanması birkaç dakika sürebilir. Daha uzun testleri çalıştırmadan önce başarı için test etmek üzere daha az yinelemeden başlamak isteyebilirsiniz.



## <a name="testing-vms-running-linux"></a>Linux çalıştıran VM 'Leri test etme

SockPerf kullanın. [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf) kullanılabilir

### <a name="install-sockperf-on-the-vms"></a>VM 'Lere SockPerf 'i yükler

Linux VM 'lerinde (Gönderen ve alıcı), sanal makinelerinize SockPerf hazırlamak için şu komutları çalıştırın. Ana distrolar için komutlar sağlanır.

#### <a name="for-rhel--centos-follow-these-steps"></a>RHEL/CentOS için şu adımları izleyin:
```bash
#CentOS / RHEL - Install GIT and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu-follow-these-steps"></a>Ubuntu için şu adımları izleyin:
```bash
#Ubuntu - Install GIT and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros-copy-compile-and-install-sockperf-according-to-the-following-steps"></a>Tüm kaldırmalar için, SockPerf aşağıdaki adımlara göre kopyalayın, derleyin ve yükleyemezsiniz:
```bash
#Bash - all distros

#From bash command line (assumes git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>VM 'lerde SockPerf çalıştırma

SockPerf yüklemesi tamamlandıktan sonra, sanal makineler gecikme testlerini çalıştırmaya hazırlardır. 

İlk olarak, alıcı üzerinde SockPerf başlatın.

Kullanılabilir herhangi bir bağlantı noktası numarası iyidir. Bu örnekte 12345 numaralı bağlantı noktasını kullanacağız:
```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```
Sunucu dinlerken, istemci, dinlediği bağlantı noktasında sunucuya paket göndermeye başlayabilir, bu durumda 12345.

Aşağıdaki örnekte gösterildiği gibi, 100 saniye hakkında, temsilci sonuçları döndürmek için yeterince uzun.
```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Sonuçları bekle. VM 'Lerin ne kadar uzakta olduğuna bağlı olarak, yineleme sayısı farklılık gösterecektir. Daha uzun testleri çalıştırmadan önce başarılı olup olmadığını sınamak için 5 saniyelik daha kısa testler gerçekleştirmeyi deneyin.

Bu SockPerf örnek, tipik bir ortalama boyut paketi olduğundan 350 baytlık ileti boyutu kullanır. İleti boyutu, VM 'lerde çalışacak iş yükünü daha doğru bir şekilde temsil eden sonuçlara ulaşmak için daha yüksek veya daha düşük bir şekilde ayarlanabilir.


## <a name="next-steps"></a>Sonraki adımlar
* [Azure yakınlık yerleşimi grubuyla](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) gecikme süresini iyileştirme
* Senaryonuza yönelik [VM 'lerin ağ için nasıl Iyileştirileceği](../virtual-network/virtual-network-optimize-network-bandwidth.md) hakkında bilgi edinin.
* [Bant genişliğinin sanal makinelere nasıl ayrıldığı](../virtual-network/virtual-machine-network-throughput.md) hakkında bilgi edinin
* [Azure sanal ağ hakkında sık sorulan sorular (SSS)](../virtual-network/virtual-networks-faq.md) hakkında daha fazla bilgi edinin