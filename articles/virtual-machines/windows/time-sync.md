---
title: Azure 'da Windows VM 'Leri için zaman eşitleme
description: Windows sanal makineleri için zaman eşitleme.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 58824b13cfac264c051de6bea45d2dab3aae8fae
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74068114"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Azure 'da Windows VM 'Leri için zaman eşitleme

Zaman eşitleme, güvenlik ve olay bağıntısı açısından önemlidir. Bazen dağıtılmış işlem uygulamaları için kullanılır. Birden çok bilgisayar sistemi arasındaki zaman doğruluğu eşitleme yoluyla elde edilir. Eşitleme, zaman kaynağı ve saati geçen bilgisayar arasındaki yeniden başlatmalar ve ağ trafiği dahil olmak üzere birden çok işlemden etkilenebilir. 

Azure artık Windows Server 2016 çalıştıran altyapı tarafından desteklenmektedir. Windows Server 2016, saat ve durum durumunu düzeltmek için kullanılan ve UTC ile eşitlenmesi için geliştirilmiş algoritmalara sahiptir.  Windows Server 2016 Ayrıca sanal makinelerin doğru zaman için konakla nasıl eşitleneceğini yöneten Vmictimessync hizmetini geliştirmiştir. Geliştirmeler, VM başlatma veya VM geri yükleme sırasında daha doğru ilk zamanı ve Windows saatine (W32Time) sağlanan örnekler için kesme gecikmesi düzeltmesini içerir. 


>[!NOTE]
>Windows Saat hizmeti 'ne hızlı bir genel bakış için, bu [üst düzey genel bakış videosunu](https://aka.ms/WS2016TimeVideo)inceleyin.
>
> Daha fazla bilgi için bkz. [Windows Server 2016 Için doğru süre](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Genel Bakış

Bir bilgisayar saatinin doğruluğu, bilgisayar saatinin Eşgüdümlü Evrensel Saat (UTC) zaman standardına ne kadar yakın olduğunu gauged. UTC, 300 yıl içinde yalnızca bir saniye boyunca kapalı olabilecek, çok uluslu bir tam atomik saatler örneği tarafından tanımlanır. Ancak UTC okuma doğrudan özel donanım gerektirir. Bunun yerine, zaman sunucuları UTC ile eşitlenir ve ölçeklenebilirlik ve sağlamlık sağlamak için diğer bilgisayarlardan erişilir. Her bilgisayarda, hangi zaman sunucularının kullanılacağını bilen ve bilgisayar saatinin düzeltilmesi ve gerektiğinde zaman ayarlaması gerekip gerekmediğini bilen zaman eşitleme hizmeti çalışmaktadır. 

Azure Konakları, Microsoft 'a ait stratum 1 cihazlarından, GPS anteniyle zaman alan iç Microsoft zaman sunucularıyla eşitlenir. Azure 'daki sanal makineler, ana bilgisayarlarına bağlı olarak doğru süreyi (*konak saati*) VM 'ye veya VM 'nin bir zaman sunucusundan ya da her ikisinin birleşimini doğrudan almasını sağlayabilir. 

Konak ile sanal makine etkileşimleri de aynı zamanda saati etkileyebilir. [Bakım koruma](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)sırasında, VM 'ler 30 saniyeye kadar duraklatılır. Örneğin, bakım başlamadan önce VM saati 10:00:00 ' i gösterir ve 28 saniye sürer. VM çalışmaya devam ettikten sonra, sanal makine üzerindeki saat yine 28 saniye olan 10:00:00 ' i gösteriyor olabilir. Bunu düzeltmek için, Vmictimessync hizmeti konakta neler olduğunu izler ve VM 'lerde telafi etmek için değişikliklerin gerçekleşmesini ister.

Vmictimessync hizmeti, örnek veya eşitleme modunda çalışır ve yalnızca ileriye dönük saati etkiler. W32Time 'ın çalıştırılmasını gerektiren örnek modda, Vmictimessync hizmeti ana bilgisayarı 5 saniyede bir yoklar ve W32Time için zaman örnekleri sağlar. Her 30 saniyede bir, W32Time hizmeti en son örneği alır ve konuğun saatini etkilemek için kullanır. Bir konuğun sürdürüleceği veya bir konuğun saatinin, ana bilgisayar saatinin arkasında 5 saniyeden uzun Drifts, eşitleme modu etkinleştirir. W32Time hizmetinin düzgün çalıştığı durumlarda, ikinci durum asla gerçekleşmemelidir.

Zaman eşitleme çalışmaya gerek kalmadan, VM 'deki saat hata birikmesini ister. Yalnızca bir VM olduğunda, iş yükü son derece doğru zaman kazandıran olması gerekmedikçe etki önemli olmayabilir. Ancak çoğu durumda, işlemleri izlemek için zaman kullanan birden çok, birbirine bağlı sanal makine ve tüm dağıtımın tamamında tutarlı olması gerekir. VM 'Ler arasındaki zaman farklıysa, aşağıdaki etkileri görebilirsiniz:

- Kimlik doğrulaması başarısız olur. Kerberos veya sertifikaya bağımlı teknoloji gibi güvenlik protokolleri, sistemler arasında tutarlı olan zamana bağlıdır. 
- Günlükler (veya diğer veriler) zaman içinde kabul etmiyorsanız sistemde ne olduğunu anlamak çok zordur. Aynı olay farklı zamanlarda gerçekleşmekle benzer ve bağıntı daha zor hale gelir.
- Saat kapalıysa faturalandırma yanlış hesaplanabilir.

Windows dağıtımları için en iyi sonuçlar, Konuk işletim sistemi olarak Windows Server 2016 kullanılarak elde edilir ve bu da zaman eşitlemeyle ilgili en son geliştirmeleri kullanmanıza neden olur.

## <a name="configuration-options"></a>Yapılandırma seçenekleri

Azure 'da barındırılan Windows sanal makinelerinize zaman eşitlemesini yapılandırmak için üç seçenek vardır:

- Ana bilgisayar süresi ve time.windows.com. Bu, Azure Marketi görüntülerinde kullanılan varsayılan yapılandırmadır.
- Yalnızca konak.
- Konak süresi kullanmadan veya olmadan başka bir dış saat sunucusu kullanın.


### <a name="use-the-default"></a>Varsayılanı kullan

Varsayılan olarak, Windows işletim sistemi VM görüntüleri W32Time için iki kaynaktan eşitlenecek şekilde yapılandırılır: 

- Time.windows.com adresinden bilgi alan NtpClient sağlayıcısı.
- Konak saatini VM 'Lerle iletişim kurmak ve bakım için VM duraklatıldıktan sonra düzeltmeler yapmak için kullanılan Vmictimessync hizmeti. Azure Konakları, doğru zaman saklamak için Microsoft 'a ait stratum 1 cihazlarını kullanır.

W32Time, zaman sağlayıcısını aşağıdaki öncelik sırasına göre tercih eder: katman düzeyi, kök gecikmesi, kök dağılımı, zaman değeri. Çoğu durumda, time.windows.com, daha düşük bir sınır bildirdiğinden, W32Time ana bilgisayara yönelik olarak tercih edebilir. 

Etki alanına katılmış makinelerde, etki alanının kendisi zaman eşitleme hiyerarşisi oluşturur, ancak orman kökünün hala herhangi bir yerden zaman sürilmesi gerekir ve aşağıdaki noktalar doğru kalmaya devam eder.


### <a name="host-only"></a>Yalnızca konak 

Time.windows.com genel bir NTP sunucusu olduğundan, süresi ile eşitleme, internet üzerinden trafik gönderilmesini gerektirir, farklı paket gecikmeleri, zaman eşitlemenin kalitesini olumsuz etkileyebilir. Yalnızca konak eşitlemeye geçiş yaparak time.windows.com kaldırıldığında, bazen zaman eşitleme sonuçlarınızı geliştirebilirsiniz.

Yalnızca konak zaman eşitlemeye geçiş yapmak, varsayılan yapılandırmayı kullanarak zaman eşitleme sorunlarıyla karşılaşırsanız anlamlı hale gelir. VM 'nin zaman eşitlemesini iyileştirecağından emin olmak için yalnızca konak eşitlemesini deneyin. 

### <a name="external-time-server"></a>Dış saat sunucusu

Belirli zaman eşitleme gereksinimleriniz varsa, dış saat sunucuları kullanma seçeneği de vardır. Dış saat sunucuları, test senaryoları için faydalı olabilecek, Microsoft olmayan veri merkezlerinde barındırılan makinelerle zaman tutarlılığına olanak sağlayan veya özel bir şekilde çok fazla saniye idare eden belirli bir zaman sağlayabilir.

Varsayılan yapılandırmaya benzer sonuçlar sağlamak için dış sunucuları Vmictimessync hizmeti ve VMICTimeProvider ile birleştirebilirsiniz. 

## <a name="check-your-configuration"></a>Yapılandırmanızı denetleyin


NtpClient saat sağlayıcısının açık NTP sunucuları (NTP) veya etki alanı zaman eşitlemesini (NT5DS) kullanacak şekilde yapılandırılıp yapılandırılmadığını denetleyin.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

VM NTP kullanıyorsa, aşağıdaki çıktıyı görürsünüz:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

NtpClient saat sağlayıcısının hangi zaman sunucusunu kullandığını görmek için, yükseltilmiş bir komut isteminde şunu yazın:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

VM varsayılan değer kullanıyorsa, çıkış şöyle görünür:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Hangi zaman sağlayıcının kullanılmakta olduğunu görmek için.

```
w32tm /query /source
```


Görebileceğiniz çıktı ve ne anlama gelir:
    
- **Time.Windows.com** -varsayılan yapılandırmada w32time, Time.Windows.com 'ten zaman alabilir. Zaman eşitleme kalitesi, internet bağlantısına bağlıdır ve paket gecikmelerinden etkilenir. Bu, varsayılan kurulumun olağan çıktıdır.
- **VM IC zaman eşitleme sağlayıcısı** -VM, zaman konaktan eşitleniyor. Bu, genellikle yalnızca konak zaman eşitlemesini tercih ediyorsanız veya NtpServer Şu anda kullanılabilir durumda değilse oluşur. 
- *Etki alanı sunucunuz* -geçerli makine bir etki alanında ve etki alanı zaman eşitleme hiyerarşisini tanımlar.
- *Diğer bir sunucu* -W32Time, başka bir sunucudan saati almak üzere açık bir şekilde yapılandırılmıştır. Zaman eşitleme kalitesi, bu zamana sunucu kalitesine bağlıdır.
- **Yerel CMOS saat** saati zaman uyumsuz. W32Time 'ın yeniden başlatmanın ardından başlaması yeterli zaman yoksa veya yapılandırılan tüm zaman kaynakları kullanılabilir olmadığında bu çıktıyı alabilirsiniz.


## <a name="opt-in-for-host-only-time-sync"></a>Yalnızca konak zaman eşitlemesini kabul etme

Azure, konaklarda zaman eşitlemesini iyileştirmek için sürekli olarak çalışmaktadır ve tüm zaman eşitleme altyapısının Microsoft 'un sahip olduğu veri merkezlerinde birlikte bulunduğundan emin olabilir. Birincil saat kaynağı olarak time.windows.com kullanmayı tercih eden varsayılan kurulumla zaman eşitleme sorunlarınız varsa, yalnızca konak zaman eşitlemesini kabul etmek için aşağıdaki komutları kullanabilirsiniz.

VMıC sağlayıcısını etkin olarak işaretleyin. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

NTPClient sağlayıcısını devre dışı olarak işaretleyin.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

W32Time hizmetini yeniden başlatın.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Windows Server 2012 ve R2 VM 'Leri 

Windows Server 2012 ve Windows Server 2012 R2 zaman eşitleme için farklı varsayılan ayarlara sahiptir. W32Time varsayılan olarak, hizmetin daha düşük bir zamana kadar yük yükünü tercih eden bir şekilde yapılandırılmıştır. 

Windows Server 2012 ve 2012 R2 dağıtımlarınızı kesin bir süre tercih eden daha yeni varsayılan değerleri kullanmak üzere taşımak istiyorsanız, aşağıdaki ayarları uygulayabilirsiniz.

W32Time yoklama ve güncelleştirme aralıklarını Windows Server 2016 ayarlarıyla eşleşecek şekilde güncelleştirin.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

W32Time 'nin yeni yoklama aralıklarını kullanabilmesi için, NtpServers bunları kullanarak işaretlenir. Sunucularla, bu mekanizmayı geçersiz kılabilecek 0x1 bitflag maskesi ile açıklanacaksa ve W32Time, bunun yerine SpecialPollInterval kullanır. Belirtilen NTP sunucularının 0x8 bayrağını kullanıyor veya hiç bayrak bulunmadığından emin olun:

Kullanılan NTP sunucuları için hangi bayrakların kullanıldığını kontrol edin.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Sonraki adımlar

Zaman eşitleme hakkında daha fazla ayrıntı için bağlantılar aşağıda verilmiştir:

- [Windows Saat hizmeti araçları ve ayarları](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [Windows Server 2016 geliştirmeleri](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Windows Server 2016 için doğru saat](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [Windows Saat hizmetini yüksek doğruluk ortamları için yapılandırmak üzere sınır desteği](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)


