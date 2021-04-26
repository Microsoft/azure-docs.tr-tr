---
title: Azure sanal ağında Azure sanal makine ağ gecikmesini sınama | Microsoft Docs
description: Bir sanal ağdaki Azure sanal makineler arasındaki ağ gecikmesini test etme hakkında bilgi edinin
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 7dc8aac730fdf46cab47a3297b8c001cb0b8e314
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226414"
---
# <a name="test-vm-network-latency"></a>VM ağ gecikmesini sınama

En doğru sonuçlara ulaşmak için, Azure sanal makinenizin (VM) ağ gecikmesini görev için tasarlanan bir araçla ölçün. SockPerf (Linux için) ve latte.exe (Windows için) gibi genel kullanıma sunulan araçlar, uygulama gecikmesi gibi diğer gecikme türlerini dışlarken ağ gecikmesini yalıtabilir ve ölçebilir. Bu araçlar, uygulama performansını etkileyen ağ trafiği türüne odaklanmaktadır (yani Iletim Denetimi Protokolü [TCP] ve Kullanıcı Datagram Protokolü [UDP] trafiği). 

Ping gibi diğer yaygın bağlantı araçları gecikme süresini ölçebilir, ancak sonuçları gerçek iş yüklerinde kullanılan ağ trafiğini temsil edemeyebilir. Bunun nedeni, bu araçların çoğu, uygulama trafiğinden farklı davranılan ve sonuçları TCP ve UDP kullanan iş yükleri için uygulanamadığı Internet Denetim Iletisi Protokolü 'Nü (ıCMP) kullanır. 

Çoğu uygulama tarafından kullanılan protokollerin doğru ağ gecikmesi testi için, SockPerf (Linux için) ve latte.exe (Windows için) en ilgili sonuçları üretir. Bu makalede, bu araçların her ikisi de ele alınmaktadır.

## <a name="overview"></a>Genel Bakış

Biri gönderici ve diğeri alıcı olarak olmak üzere iki VM kullanarak iki yönlü bir iletişim kanalı oluşturursunuz. Bu yaklaşımda, her iki yönde de paket gönderip alabilir ve gidiş dönüş süresini (RTT) ölçebilir.

İki VM arasında veya iki fiziksel bilgisayar arasında ağ gecikmesini ölçmek için bu yaklaşımı kullanabilirsiniz. Gecikme ölçümleri aşağıdaki senaryolar için yararlı olabilir:

- Dağıtılan VM 'Ler arasındaki ağ gecikmesi için bir kıyaslama noktası oluşturun.
- Ağ gecikmesi içindeki değişikliklerin etkilerini, ilgili değişiklikler yapıldıktan sonra karşılaştırın:
  - Yapılandırma değişiklikleri dahil olmak üzere işletim sistemi (OS) veya ağ yığını yazılımı.
  - Bir kullanılabilirlik bölgesine veya yakınlık yerleşimi grubuna (PPG) dağıtım gibi bir VM dağıtım yöntemi.
  - Hızlandırılmış ağ veya boyut değişiklikleri gibi VM özellikleri.
  - Yönlendirme veya filtreleme değişiklikleri gibi bir sanal ağ.

### <a name="tools-for-testing"></a>Test araçları
Gecikme süresini ölçmek için iki farklı araç seçeneğiniz vardır:

* Windows tabanlı sistemler için: [latte.exe (Windows)](https://github.com/microsoft/latte/releases/download/v0/latte.exe)
* Linux tabanlı sistemler için: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Bu araçları kullanarak, yalnızca TCP veya UDP yük teslim sürelerinin, uygulamalar tarafından kullanılmayan, ıCMP (ping) veya diğer paket türlerini ölçtüğüyle ve performanslarını etkilemediğinden emin olmanıza yardımcı olursunuz.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>En iyi VM yapılandırması oluşturmaya yönelik ipuçları

VM yapılandırmanızı oluşturduğunuzda aşağıdaki önerileri göz önünde bulundurun:
- Windows veya Linux 'un en son sürümünü kullanın.
- En iyi sonuçlar için hızlandırılmış ağı etkinleştirin.
- Bir [Azure yakınlık yerleşimi grubuyla](../virtual-machines/co-location.md)VM 'leri dağıtın.
- Daha büyük VM 'ler genellikle daha küçük VM 'lerden daha iyi gerçekleştirilir.

### <a name="tips-for-analysis"></a>Analiz ipuçları

Test sonuçlarını analiz ettiğiniz için aşağıdaki önerileri göz önünde bulundurun:

- Dağıtım, yapılandırma ve iyileştirmeler tamamlandıktan hemen sonra bir taban çizgisi oluşturun.
- Yeni sonuçları her zaman bir taban çizgisine veya diğer bir testten, denetimli değişikliklerle diğerine karşılaştırın.
- Değişiklikler her gösterildiğinde veya planlanarak testleri tekrarlayın.


## <a name="test-vms-that-are-running-windows"></a>Windows çalıştıran VM 'Leri test etme

### <a name="get-latteexe-onto-the-vms"></a>VM 'lere latte.exe alın

[En son latte.exesürümünü ](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)indirin.

latte.exe, *c:\Tools* gibi ayrı bir klasöre yerleştirmeyi düşünün.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Windows Defender güvenlik duvarı üzerinden latte.exe izin ver

*Alıcı* üzerinde, latte.exe trafiğinin gelmesini sağlamak Için Windows Defender güvenlik duvarı 'Nda bir izin verme kuralı oluşturun. Gelen belirli TCP bağlantı noktalarına izin vermek yerine latte.exe programın tamamının ada göre izin vermek en kolay yoldur.

Aşağıdaki komutu çalıştırarak Windows Defender güvenlik duvarı üzerinden latte.exe izin verin:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Örneğin, latte.exe *c:\Tools* klasörüne kopyaladıysanız şu komut olacaktır:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Gecikme testleri çalıştırma

* *Alıcı* üzerinde latte.exe başlatın (PowerShell 'den DEĞIL, cmd penceresinden çalıştırın):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    65.000 ' den fazla yineleme, temsilci sonuçları döndürmek için yeterince uzun.

    Kullanılabilir herhangi bir bağlantı noktası numarası iyidir.

    VM 'nin IP adresi 10.0.0.4 ise, komut şöyle görünür:

    `latte -a 10.0.0.4:5005 -i 65100`

* *Gönderenin* latte.exe başlatın (PowerShell 'den DEĞIL, cmd penceresinden çalıştırın):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    Elde edilen komut, &nbsp; Bu *istemci* ya da *gönderici* olduğunu göstermek için *-c* ' nin eklenmesi dışında, alıcı ile aynıdır.

    `latte -c -a 10.0.0.4:5005 -i 65100`

Sonuçları bekle. VM 'Lerin ne kadar uzaklıkta olduğuna bağlı olarak, testin tamamlanması birkaç dakika sürebilir. Daha uzun testleri çalıştırmadan önce başarı için test etmek üzere daha az yinelemeden başlamak isteyebilirsiniz.

## <a name="test-vms-that-are-running-linux"></a>Linux çalıştıran VM 'Leri test etme

Linux çalıştıran VM 'Leri test etmek için [SockPerf](https://github.com/mellanox/sockperf)kullanın.

### <a name="install-sockperf-on-the-vms"></a>VM 'Lere SockPerf 'i yükler

Linux VM 'lerinde hem *gönderici* hem de *alıcı*, VM 'lerde SockPerf hazırlamak için aşağıdaki komutları çalıştırın. Ana distrolar için komutlar sağlanır.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Red Hat Enterprise Linux için (RHEL)/CentOS

Aşağıdaki komutları çalıştırın:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Ubuntu için

Aşağıdaki komutları çalıştırın:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>Tüm kaldırmalar için

Aşağıdaki adımlara göre SockPerf kopyalayın, derleyin ve yüklersiniz:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
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

SockPerf yüklemesi tamamlandıktan sonra, sanal makineler gecikme testlerini çalıştırmaya hazırdır. 

İlk olarak, *alıcı* üzerinde SockPerf başlatın.

Kullanılabilir herhangi bir bağlantı noktası numarası iyidir. Bu örnekte, 12345 numaralı bağlantı noktasını kullanacağız:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Sunucu dinlerken, istemci, dinlediği bağlantı noktasında sunucuya paket göndermeye başlayabilir (Bu durumda, 12345).

Aşağıdaki örnekte gösterildiği gibi, 100 saniye hakkında, temsilci sonuçları döndürmek için yeterince uzun.

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Sonuçları bekle. VM 'Lerin ne kadar uzakta olduğuna bağlı olarak, yineleme sayısı farklılık gösterecektir. Daha uzun testler çalıştırmadan önce başarılı olup olmadığını test etmek için yaklaşık 5 saniyelik daha kısa testler kullanmayı deneyin.

Bu SockPerf örneği, ortalama bir paket için tipik bir 350 baytlık ileti boyutu kullanır. Sanal makinelerinizdeki çalışan iş yükünü daha doğru şekilde temsil eden sonuçlara ulaşmak için boyutu daha yüksek veya daha düşük olarak ayarlayabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar
* Bir [Azure yakınlık yerleşimi grubuyla](../virtual-machines/co-location.md)gecikme süresini geliştirir.
* Senaryonuza yönelik [VM 'lerin ağ iletişimini nasıl iyileştireceğinizi](../virtual-network/virtual-network-optimize-network-bandwidth.md) öğrenin.
* [Bant genişliğinin sanal makinelere nasıl ayrıldığı](../virtual-network/virtual-machine-network-throughput.md)hakkında bilgi edinin.
* Daha fazla bilgi için bkz. [Azure sanal ağ hakkında SSS](../virtual-network/virtual-networks-faq.md).