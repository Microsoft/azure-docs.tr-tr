---
title: SysRq ve NMI çağrıları için Azure seri konsolu
description: Azure sanal makinelerinde SysRq ve NMI çağrıları için seri konsol kullanma.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 5eba50e7cfc00c081622972ca09f305b34c19c47
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034989"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>SysRq ve NMI çağrıları için seri konsol kullan

## <a name="system-request-sysrq"></a>Sistem Isteği (SysRq)
SysRq, bir dizi önceden tanımlanmış eylemi tetikleyebilen Linux işlem sistemi çekirdeği tarafından anlaşılan bir anahtarlar dizisidir. Bu komutlar genellikle sanal makine sorun giderme veya kurtarma geleneksel yönetim aracılığıyla gerçekleştirilemediği zaman kullanılır (örneğin, VM yanıt vermiyorsa). Azure seri konsolunun SysRq özelliğinin kullanılması, SysRq anahtarına ve fiziksel bir klavyede girilen karakterlere yönelik olarak bir tuşa benzecektir.

SysRq sırası teslim edildiğinde, çekirdek yapılandırması sistemin nasıl yanıt verdiğini denetler. SysRq 'ı etkinleştirme ve devre dışı bırakma hakkında bilgi için bkz. [markup](https://aka.ms/linuxsysrq) | *SySRq Yönetici Kılavuzu* [metni](https://aka.ms/kernelorgsysreqdoc) .  

Azure seri konsolu, aşağıda gösterilen komut çubuğundaki klavye simgesini kullanarak bir Azure sanal makinesine bir SysRq göndermek için kullanılabilir.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

"Send SysRq komutunu" seçtiğinizde, genel SysRq seçeneklerini sağlayan bir iletişim kutusu açılır ve iletişim kutusuna girilen bir SysRq komutları dizisi kabul edilir.  Bu, ' nin kullanarak güvenli bir yeniden başlatma gibi yüksek düzey bir işlem gerçekleştirmesine olanak sağlar: `REISUB`.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

SysRq komutu durdurulmuş olan veya çekirdeği yanıt vermeyen bir durumda olan sanal makinelerde kullanılamaz. (örneğin, bir çekirdek Panic).

### <a name="enable-sysrq"></a>SysRq 'ı etkinleştir 
Yukarıdaki *SySRq yönetici kılavuzunda* açıklandığı gibi, SySRq, All, None veya yalnızca belirli komutların kullanılabilmesi için yapılandırılabilir. Tüm SysRq komutlarını aşağıdaki adımı kullanarak etkinleştirebilirsiniz, ancak yeniden başlatma işlemi devam etmez:
```
echo "1" >/proc/sys/kernel/sysrq
```
SysReq yapılandırmasını kalıcı hale getirmek için, tüm SysRq komutlarını etkinleştirmek üzere şunları yapabilirsiniz
1. Bu satırı */etc/sysctl.exe* 'e ekleme <br>
    `kernel.sysrq = 1`
1. Çalıştırarak sysctl 'yi yeniden başlatma veya güncelleştirme <br>
    `sysctl -p`

### <a name="command-keys"></a>Komut tuşları 
Yukarıdaki SysRq yönetim kılavuzunda:

|Komut| İşlev
| ------| ----------- |
|``b``  |   , Disklerinizi eşitlemeden veya bağlamadan geri yüklemeden sistemi hemen yeniden başlatır.
|``c``  |   NULL işaretçi başvurusu tarafından sistem kilitlenmesiyle gerçekleştirilir. Yapılandırıldıysa bir kilitlenme dökümü alınacaktır.
|``d``  |   Tutulan tüm kilitleri gösterir.
|``e``  |   İnit hariç tüm işlemlere bir SIGTERM gönderin.
|``f``  |   , Bir bellek barındırma işlemini sonlandırmak için OOM Killer çağrısını çağırır, ancak hiçbir şey sonlandırılıp sonlandırılmayacağını söylemez.
|``g``  |   KGDB tarafından kullanılan (çekirdek hata ayıklayıcı)
|``h``  |   Yardım görüntülenir (burada listelenenden başka herhangi bir anahtar de yardım görüntüler, ancak ``h`` hatırlamaları kolay olur)
|``i``  |    İnit hariç tüm işlemlere bir SIGKıLL gönderin.
|``j``  |    Zorla, fifreeze IOCTL tarafından dondurulmuş olan dosya sistemleri 'ı zorla.
|``k``  |    Güvenli erişim anahtarı (SAK) geçerli sanal konsolundaki tüm programları Killer. NOTE: SAK bölümünde aşağıdaki önemli açıklamalara bakın.
|``l``  |    Tüm etkin CPU 'Lar için yığın geri izlemeyi gösterir.
|``m``  |    Geçerli bellek bilgilerinin konsolunuza dökümünü alacak.
|``n``  |    RT görevlerinin iyi hale getirmek için kullanılır
|``o``  |    , Sisteminizi kapatacak (yapılandırıldıysa ve destekleniyorsa).
|``p``  |    Geçerli yazmaçların ve bayrakların konsolunuza dökümünü yapar.
|``q``  |    , Tüm clockevent cihazlarla ilgili tüm hrsüreölçerler (ancak normal timer_list zamanlayıcılar DEĞIL) ve ayrıntılı bilgiler için her türlü CPU listesi dökümünü alacak.
|``r``  |    Klavye ham modunu kapatır ve bunu XGEÇ olarak ayarlar.
|``s``  |    , Tüm bağlı dosya sistemlerini eşitlemeye çalışır.
|``t``  |    , Geçerli görevlerin listesini ve bunların bilgilerini konsolunuza döker.
|``u``  |    , Tüm bağlı dosya sistemlerini salt okunurdur.
|``v``  |    Framebuffer konsolunu zorla geri yükler
|``v``  |    ETM arabellek dökümünden neden olur [ARM 'ye özgü]
|``w``  |    Kesintisiz (engellenen) durumundaki görevlerin dökümünü yapar.
|``x``  |    PPC/PowerPC platformlarında XMON arabirimi tarafından kullanılır. SPARC64 üzerinde genel PMU kayıtlarını göster. MIPS üzerindeki tüm TLB girdilerini dökümünü alın.
|``y``  |    Genel CPU kayıtlarını göster [SPARC-64 özgü]
|``z``  |    FTRACE arabelleğinin dökümünü al
|``0``-``9`` | Konsolunuza hangi çekirdek iletilerinin yazdırılacağını denetleyen konsol günlük düzeyini ayarlar. (``0``Örneğin, bunu yalnızca PANICs veya Oopse gibi acil mesaj iletilerinin konsolunuza yapması için yapar.)

### <a name="distribution-specific-documentation"></a>Dağıtıma özgü belgeler ###
SysRq üzerinde dağıtıma özgü belgeler ve Linux 'u bir SysRq "kilitlenme" komutu aldığında kilitlenme dökümü oluşturmak üzere Linux yapılandırma adımları için aşağıdaki bağlantılara bakın:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Çekirdek kilitlenme dökümü](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [SysRq tesis nedir ve nasıl kullanabilirim?](https://access.redhat.com/articles/231663)
- [Bir RHEL sunucusundan bilgi toplamak için SysRq özelliğini kullanma](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Çekirdek temel döküm yakalamayı yapılandırma](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Kilitlenme günlüklerini toplama](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Maskelenemeyen kesme (NMI) 
Maskelenemeyen bir kesme (NMI), bir sanal makinede yazılımın yok saymayacak bir sinyal oluşturmak için tasarlanmıştır. Tarihsel olarak, NMIs belirli yanıt süreleri gerektiren sistemleri donanım sorunları izlemek için kullanılır.  Günümüzde programcılar ve sistem yöneticileri, yanıt vermeyen sistemlerde hata ayıklamak veya sorunları gidermek için genellikle NMI 'yi bir mekanizma olarak kullanır.

Seri konsol, aşağıda gösterilen komut çubuğundaki klavye simgesini kullanarak bir Azure sanal makinesine bir NMI göndermek için kullanılabilir. NMI teslim edildiğinde, sanal makine yapılandırması sistemin nasıl yanıt verdiğini denetler.  Linux işletim sistemleri, kilitlenme ve bellek dökümü oluşturmak için yapılandırılabilir ve işletim sistemi bir NMI alır.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

Çekirdek parametrelerini yapılandırmak için sysctl 'yi destekleyen Linux sistemleri için, aşağıdakileri kullanarak bu NMI 'yi alırken bir panik 'yi etkinleştirebilirsiniz:
1. Bu satırı */etc/sysctl.exe* 'e ekleme <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Çalıştırarak sysctl 'yi yeniden başlatma veya güncelleştirme <br>
    `sysctl -p`

`unknown_nmi_panic`, `panic_on_io_nmi`ve `panic_on_unrecovered_nmi`dahil Linux çekirdek yapılandırması hakkında daha fazla bilgi için bkz. [/proc/sys/kernel/for documentation *](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). NMI 'deki dağıtıma özgü belgeler ve bir NMI aldığında kilitlenme dökümü oluşturmak için Linux yapılandırma adımları için aşağıdaki bağlantılara bakın:
 
### <a name="ubuntu"></a>Ubuntu 
 - [Çekirdek kilitlenme dökümü](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat 
 - [NMI nedir ve ne için kullanabilirim?](https://access.redhat.com/solutions/4127)
 - [NMI anahtarı gönderildiğinde sistemmi kilitlenme için nasıl yapılandırabilirim?](https://access.redhat.com/solutions/125103)
 - [Kilitlenme dökümü Yönetici Kılavuzu](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE 
- [Çekirdek temel döküm yakalamayı yapılandırma](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS 
- [Kilitlenme günlüklerini toplama](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Sonraki adımlar
* Ana seri konsol Linux belge sayfası [burada](serial-console.md)bulunur.
* IDB 'de önyükleme yapmak [ve tek kullanıcı moduna girmek](serial-console-grub-single-user-mode.md) Için seri konsol kullanın
* Seri konsol [Windows](../windows/serial-console.md) VM 'leri için de kullanılabilir
* [Önyükleme tanılaması](boot-diagnostics.md) hakkında daha fazla bilgi