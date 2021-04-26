---
title: Azure 'da Linux VM 'Leri için zaman eşitleme
description: Linux sanal makineleri için zaman eşitleme.
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: 18c8570a8066985cab5263c4779787062dc32d75
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552652"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Azure 'da Linux VM 'Leri için zaman eşitleme

Zaman eşitleme, güvenlik ve olay bağıntısı açısından önemlidir. Bazen dağıtılmış işlem uygulamaları için kullanılır. Birden çok bilgisayar sistemi arasındaki zaman doğruluğu eşitleme yoluyla elde edilir. Eşitleme, zaman kaynağı ve saati geçen bilgisayar arasındaki yeniden başlatmalar ve ağ trafiği dahil olmak üzere birden çok işlemden etkilenebilir. 

Azure, Windows Server 2016 çalıştıran altyapıyla desteklenir. Windows Server 2016, saat ve durum durumunu düzeltmek için kullanılan ve UTC ile eşitlenmesi için geliştirilmiş algoritmalara sahiptir.  Windows Server 2016 doğru zaman özelliği, sanal makineleri doğru süre için konak ile yöneten Vmictimessync hizmetinin nasıl büyük ölçüde geliştirilmiştir. Geliştirmeler, VM başlatma veya VM geri yükleme ve kesme gecikme süresi düzeltmesinin daha doğru ilk zamanını içerir. 

> [!NOTE]
> Windows Saat hizmeti 'ne hızlı bir genel bakış için, bu [üst düzey genel bakış videosunu](https://aka.ms/WS2016TimeVideo)inceleyin.
>
> Daha fazla bilgi için bkz. [Windows Server 2016 Için doğru süre](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Genel Bakış

Bir bilgisayar saatinin doğruluğu, bilgisayar saatinin Eşgüdümlü Evrensel Saat (UTC) zaman standardına ne kadar yakın olduğunu gauged. UTC, 300 yıl içinde yalnızca bir saniye boyunca kapalı olabilecek, çok uluslu bir tam atomik saatler örneği tarafından tanımlanır. Ancak UTC okuma doğrudan özel donanım gerektirir. Bunun yerine, zaman sunucuları UTC ile eşitlenir ve ölçeklenebilirlik ve sağlamlık sağlamak için diğer bilgisayarlardan erişilir. Her bilgisayarda, hangi zaman sunucularının kullanılacağını bilen ve bilgisayar saatinin düzeltilmesi ve gerektiğinde zaman ayarlaması gerekip gerekmediğini bilen zaman eşitleme hizmeti çalışmaktadır. 

Azure Konakları, Microsoft 'a ait stratum 1 cihazlarından, GPS anteniyle zaman alan iç Microsoft zaman sunucularıyla eşitlenir. Azure 'daki sanal makineler, ana bilgisayarlarına bağlı olarak doğru süreyi (*konak saati*) VM 'ye veya VM 'nin bir zaman sunucusundan ya da her ikisinin birleşimini doğrudan almasını sağlayabilir. 

Tek başına donanımda, Linux işletim sistemi yalnızca önyüklemede ana bilgisayar donanım saatini okur. Bundan sonra, saat, Linux çekirdeğindeki kesme süreölçeri kullanılarak korunur. Bu yapılandırmada saat, zaman içinde olur. Azure 'daki daha yeni Linux dağıtımlarında, VM 'Ler, ana bilgisayardan daha sık saat güncelleştirmeleri sorgulamak için Linux Integration Services (LIS) içinde bulunan Vmictimessync sağlayıcısını kullanabilir.

Konak ile sanal makine etkileşimleri de aynı zamanda saati etkileyebilir. [Bakım koruma](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)sırasında, VM 'ler 30 saniyeye kadar duraklatılır. Örneğin, bakım başlamadan önce VM saati 10:00:00 ' i gösterir ve 28 saniye sürer. VM çalışmaya devam ettikten sonra, sanal makine üzerindeki saat yine 28 saniye olan 10:00:00 ' i gösteriyor olabilir. Bunu düzeltmek için, Vmictimessync hizmeti konakta neler olduğunu izler ve VM 'lerde telafi etmek için değişikliklerin gerçekleşmesini ister.

Zaman eşitleme çalışmaya gerek kalmadan, VM 'deki saat hata birikmesini ister. Yalnızca bir VM olduğunda, iş yükü son derece doğru zaman kazandıran olması gerekmedikçe etki önemli olmayabilir. Ancak çoğu durumda, işlemleri izlemek için zaman kullanan birden çok, birbirine bağlı sanal makine ve tüm dağıtımın tamamında tutarlı olması gerekir. VM 'Ler arasındaki zaman farklıysa, aşağıdaki etkileri görebilirsiniz:

- Kimlik doğrulaması başarısız olur. Kerberos veya sertifikaya bağımlı teknoloji gibi güvenlik protokolleri, sistemler arasında tutarlı olan zamana bağlıdır.
- Günlükler (veya diğer veriler) zaman içinde kabul etmiyorsanız sistemde ne olduğunu anlamak çok zordur. Aynı olay farklı zamanlarda gerçekleşmekle benzer ve bağıntı daha zor hale gelir.
- Saat kapalıysa faturalandırma yanlış hesaplanabilir.



## <a name="configuration-options"></a>Yapılandırma seçenekleri

Azure 'da barındırılan Linux sanal makinelerinize zaman eşitlemesini yapılandırmanın genellikle üç yolu vardır:

- Azure Marketi görüntülerinin varsayılan yapılandırması hem NTP süresi hem de Vmictimessync ana bilgisayar-zamanını kullanır. 
- Yalnızca konak Vmictimessync kullanılarak.
- Vmictimessync ana bilgisayar-saati kullanmadan veya olmadan başka bir dış saat sunucusu kullanın.


### <a name="use-the-default"></a>Varsayılanı kullan

Varsayılan olarak, Linux için Azure Market görüntülerinin çoğu iki kaynaktan eşitlenmek üzere yapılandırılır: 

- NTP, birincil olarak NTP sunucusundan zaman alır. Örneğin, Ubuntu 16,04 LTS Market görüntüleri **NTP.Ubuntu.com** kullanır.
- Konak saati VM 'lere iletmek ve bakım için VM duraklatıldıktan sonra düzeltmeler yapmak için kullanılan, ikincil olarak Vmictimessync hizmeti. Azure Konakları, doğru zaman saklamak için Microsoft 'a ait stratum 1 cihazlarını kullanır.

Daha yeni Linux dağıtımlarında, Vmictimessync hizmeti bir duyarlık Time Protocol (PTP) donanım saati kaynağı sağlar, ancak önceki dağıtımlar bu saat kaynağını sağlayamayabilir ve konaktan zaman almak için NTP 'e geri dönecektir.

NTP 'nin doğru şekilde eşitlenmesini onaylamak için komutunu çalıştırın `ntpq -p` .

### <a name="host-only"></a>Yalnızca konak 

Time.windows.com ve ntp.ubuntu.com gibi NTP sunucuları ortak olduğundan, zaman ile eşitleme, internet üzerinden trafik gönderilmesini gerektirir. Değişen paket gecikmeleri, zaman eşitlemenin kalitesini olumsuz etkileyebilir. Yalnızca konak eşitlemeye geçiş yaparak NTP kaldırıldığında zaman eşitleme sonuçları iyileştirebilirler.

Yalnızca konak zaman eşitlemeye geçiş yapmak, varsayılan yapılandırmayı kullanarak zaman eşitleme sorunlarıyla karşılaşırsanız anlamlı hale gelir. VM 'nizin zaman eşitlemesini iyileştirecağından emin olmak için yalnızca konak eşitlemesini deneyin. 

### <a name="external-time-server"></a>Dış saat sunucusu

Belirli zaman eşitleme gereksinimleriniz varsa, dış saat sunucuları kullanma seçeneği de vardır. Dış saat sunucuları, test senaryoları için faydalı olabilecek, Microsoft olmayan veri merkezlerinde barındırılan makinelerle zaman tutarlılığına olanak sağlayan veya özel bir şekilde çok fazla saniye idare eden belirli bir zaman sağlayabilir.

Varsayılan yapılandırmaya benzer sonuçlar sağlamak için bir dış saat sunucusunu Vmictimessync hizmeti ile birleştirebilirsiniz. Dış saat sunucusunu Vmictimessync ile birleştirmek, VM 'Lerin bakım için duraklatıldığı zaman neden olabilecek sorunlarla ilgilenirken en iyi seçenektir. 

## <a name="tools-and-resources"></a>Araçlar ve kaynaklar

Zaman eşitleme yapılandırmanızı denetlemek için bazı temel komutlar vardır. Linux dağıtımına yönelik belgelerde, bu dağıtım için zaman eşitlemesini yapılandırmanın en iyi yolu hakkında daha fazla ayrıntı olacaktır.

### <a name="integration-services"></a>Tümleştirme hizmetleri

Tümleştirme hizmetinin (hv_utils) yüklü olup olmadığını denetleyin.

```bash
lsmod | grep hv_utils
```
Şuna benzer bir şey görmeniz gerekir:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Bkz. Hyper-V Tümleştirme Hizmetleri arka plan programı çalışıyor.

```bash
ps -ef | grep hv
```

Şuna benzer bir şey görmeniz gerekir:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp-clock-source"></a>PTP saat kaynağını denetle

Linux 'un daha yeni sürümleriyle, bir duyarlık Time Protocol (PTP) saat kaynağı Vmictimessync sağlayıcının bir parçası olarak kullanılabilir. Red Hat Enterprise Linux veya CentOS 7. x ' in eski sürümlerinde, [Linux Tümleştirme Hizmetleri](https://github.com/LIS/lis-next) indirilebilir ve güncelleştirilmiş sürücüyü yüklemek için kullanılabilir. PTP Clock kaynağı kullanılabilir olduğunda, Linux cihazı/dev/PTP *x* biçiminde olacaktır. 

Hangi PTP saat kaynaklarının kullanılabilir olduğunu görün.

```bash
ls /sys/class/ptp
```

Bu örnekte döndürülen değer *ptp0*, bu nedenle saat adını denetlemek için kullanırız. Cihazı doğrulamak için saat adını denetleyin.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Bu, döndürmelidir `hyperv` .

### <a name="chrony"></a>zaman hatası

Ubuntu 19,10 ve sonraki sürümlerinde, Red Hat Enterprise Linux ve CentOS 8. x, zaman [hatası](https://chrony.tuxfamily.org/) , PTP kaynak saati kullanacak şekilde yapılandırılmıştır. Daha eski Linux sürümleri, bu şekilde, PTP kaynaklarını desteklemeyen ağ zaman protokol cini (ntpd) kullanır. Bu sürümlerde PTP 'i etkinleştirmek için, zaman zaman aşağıdaki kod kullanılarak el ile yüklenmeli ve yapılandırılmalıdır (zaman hatası):

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Ubuntu ve NTP hakkında daha fazla bilgi için bkz. [zaman eşitleme](https://ubuntu.com/server/docs/network-ntp).

Red Hat ve NTP hakkında daha fazla bilgi için bkz. [NTP yapılandırma](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP). 

Zaman hatası hakkında daha fazla bilgi için bkz. zaman [hatası kullanımı](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony).

Aynı anda hem Tarihçe hem de Vmictimessync kaynakları etkinleştirilmişse, diğer kaynağı yedek olarak ayarlayan bir tane **tercih** edebilirsiniz. NTP Hizmetleri uzun bir süre sonra büyük bir eğriltin saatini güncelleştirmediğinden, Vmictimessync, saati yalnızca NTP tabanlı araçlardan daha hızlı bir şekilde duraklatılmış şekilde kurtaracaktır.

Varsayılan olarak, Tarihçe her zaman drivi giderecek şekilde sistem saatini hızlandırır veya yavaşlatır. Drın çok büyük hale gelirse, zaman hatası, drmali 'i düzeltemedi. Bunu aşmak için, `makestep` /etc/, ' deki parametre, belirtilen eşiği aşarsa, bir zaman eşitlemesini zorlamak için **/etc/yılın.exe** .

 ```bash
makestep 1.0 -1
```

Burada, Dry 1 saniyeden daha büyükse bir zaman güncelleştirmesine zorlayacaktır. Değişiklikleri uygulamak için, Tarihçe hizmetini yeniden başlatın:

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>Systemd 

19,10 öncesi SUSE ve Ubuntu yayınlarında, zaman eşitleme, [systemd](https://www.freedesktop.org/wiki/Software/systemd/)kullanılarak yapılandırılır. Ubuntu hakkında daha fazla bilgi için bkz. [saat eşitleme](https://help.ubuntu.com/lts/serverguide/NTP.html). SUSE hakkında daha fazla bilgi için, bkz. 4.5.8 in [SUSE Linux Enterprise Server 12 SP3 sürüm notları](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Windows Server 2016 Için doğru süre](/windows-server/networking/windows-time-service/accurate-time).


