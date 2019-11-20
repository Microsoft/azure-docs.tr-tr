---
title: StorSimple 8000 serisi Sistem gereksinimleri | Microsoft Docs
description: Microsoft Azure StorSimple çözümü için yazılım, ağ ve yüksek kullanılabilirlik gereksinimlerini ve en iyi uygulamaları açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 2e7c1eedf02c8a7783ee90f403dbd77ec2ee53ea
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963342"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>StorSimple 8000 serisi yazılım, yüksek kullanılabilirlik ve ağ gereksinimleri

## <a name="overview"></a>Genel Bakış

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Microsoft Azure StorSimple hoş geldiniz. Bu makalede, StorSimple cihazınız ve cihaza erişen depolama istemcileri için önemli sistem gereksinimleri ve en iyi uygulamalar açıklanmaktadır. StorSimple sisteminizi dağıtmadan önce bilgileri dikkatlice incelemenizi ve ardından dağıtım ve sonraki işlemler sırasında gerektiği şekilde geri başvurmalarını öneririz.

Sistem gereksinimleri şunları içerir:

* **Depolama istemcileri Için yazılım gereksinimleri** -bu işletim sistemleri için desteklenen işletim sistemlerini ve ek gereksinimleri açıklar.
* **StorSimple cihazı Için ağ gereksinimleri** -iSCSI, bulut veya Yönetim trafiğine izin vermek için güvenlik duvarınızdaki açık olması gereken bağlantı noktaları hakkında bilgi sağlar.
* **StorSimple Için yüksek kullanılabilirlik gereksinimleri** -StorSimple cihazınız ve ana bilgisayarınız için yüksek kullanılabilirlik gereksinimlerini ve en iyi uygulamaları açıklar.

## <a name="software-requirements-for-storage-clients"></a>Depolama istemcileri için yazılım gereksinimleri

Aşağıdaki yazılım gereksinimleri, StorSimple cihazınıza erişen depolama istemcilerine yöneliktir.

| Desteklenen işletim sistemleri | Sürüm gerekli | Ek gereksinimler/notlar |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |StorSimple Iscsı birimleri yalnızca aşağıdaki Windows disk türlerinde kullanılmak üzere desteklenir:<ul><li>Temel diskte basit birim</li><li>Dinamik diskte basit ve yansıtılmış birim</li></ul>Yalnızca işletim sisteminde yerel olarak bulunan yazılım Iscsı başlatıcıları desteklenir. Donanım Iscsı başlatıcıları desteklenmez.<br></br>Windows Server 2012 ve 2016 ölçülü kaynak sağlama ve ODX özellikleri, StorSimple Iscsı birimi kullanıyorsanız desteklenir.<br><br>StorSimple, ölçülü kaynak sağlanmış ve tam olarak sağlanan birimler oluşturabilir. Kısmen sağlanan birimler oluşturamaz.<br><br>Ölçülü kaynak sağlanmış bir birimi yeniden biçimlendirme uzun zaman alabilir. Birimi silmenizi ve sonra yeniden biçimlendirme yerine yeni bir tane oluşturmayı öneririz. Ancak, yine de bir birimi yeniden biçimlendirmeyi tercih ediyorsanız:<ul><li>Boşluk geri kazanma gecikmelerini önlemek için yeniden biçimlendirmeye başlamadan önce aşağıdaki komutu çalıştırın: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Biçimlendirme işlemi tamamlandıktan sonra, geri kazanma alanını yeniden etkinleştirmek için aşağıdaki komutu kullanın:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Windows Server 2012 düzeltmesini Windows Server bilgisayarınıza [bb 2878635](https://support.microsoft.com/kb/2870270) ' de açıklandığı gibi uygulayın.</li></ul></li></ul></ul> SharePoint için StorSimple Snapshot Manager veya StorSimple Bağdaştırıcısı yapılandırıyorsanız, [isteğe bağlı bileşenler Için yazılım gereksinimleri](#software-requirements-for-optional-components)' ne gidin. |
| VMware ESX |5,5 ve 6,0 |Iscsı istemcisi olarak VMware vSphere desteklenir. VAAı-Block özelliği, StorSimple cihazlarında VMware vSphere desteklenir. |
| Linux RHEL/CentOS |5, 6 ve 7 |Open-Iscsı Başlatıcısı sürüm 5, 6 ve 7 olan Linux Iscsı istemcileri için destek. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AıX Şu anda StorSimple ile desteklenmiyor.


## <a name="software-requirements-for-optional-components"></a>İsteğe bağlı bileşenler için yazılım gereksinimleri

Aşağıdaki yazılım gereksinimleri isteğe bağlı StorSimple bileşenleri için (StorSimple Snapshot Manager ve SharePoint için StorSimple Bağdaştırıcısı) yöneliktir.

| Bileşen | Konak platformu | Ek gereksinimler/notlar |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1, 2012, 2012 R2 |Windows Server üzerinde StorSimple Snapshot Manager kullanımı, yansıtılmış dinamik disklerin yedeklenmesi/geri yüklenmesi ve uygulamayla tutarlı yedeklemeler için gereklidir.<br> StorSimple Snapshot Manager yalnızca Windows Server 2008 R2 SP1 (64-bit), Windows Server 2012 R2 ve Windows Server 2012 üzerinde desteklenir.<ul><li>Windows Server 2012 kullanıyorsanız, StorSimple Snapshot Manager yüklemeden önce .NET 3.5 – 4.5 ' i yüklemelisiniz.</li><li>Windows Server 2008 R2 SP1 kullanıyorsanız, StorSimple Snapshot Manager yüklemeden önce Windows Management Framework 3,0 ' ü yüklemelisiniz.</li></ul> |
| SharePoint için StorSimple Bağdaştırıcısı |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>SharePoint için StorSimple Bağdaştırıcısı yalnızca SharePoint 2010 ve SharePoint 2013 ' de desteklenir.</li><li>KÇY SQL Server Enterprise sürümü, sürüm 2008 R2 veya 2012 gerektirir.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>StorSimple cihazınız için ağ gereksinimleri

StorSimple cihazınız kilitli bir cihazdır. Ancak, Iscsı, bulut ve Yönetim trafiğine izin vermek için güvenlik duvarınızdaki bağlantı noktalarının açılması gerekir. Aşağıdaki tabloda güvenlik duvarınızda açılması gereken bağlantı noktaları listelenmektedir. Bu tabloda, veya *gelen* , gelen istemci tarafından cihazınıza erişim isteğinin yönünü ifade eder. *Çıkış* veya *Çıkış* , StorSimple cihazınızın verileri dışarıdan, dağıtımın ötesinde (örneğin, Internet 'e giden) gönderdiği yönü ifade eder.

| Bağlantı noktası No.<sup>1, 2</sup> | Dışarı veya dışarı | Bağlantı noktası kapsamı | Gerekli | Notlar |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |Çıkış |WAN |Hayır |<ul><li>Giden bağlantı noktası, güncelleştirmeleri almak için Internet erişimi için kullanılır.</li><li>Giden Web proxy 'si Kullanıcı tarafından yapılandırılabilir.</li><li>Sistem güncelleştirmelerine izin vermek için, bu bağlantı noktasının denetleyici sabit IP 'Leri için de açık olması gerekir.</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |Çıkış |WAN |Evet |<ul><li>Giden bağlantı noktası, buluttaki verilere erişmek için kullanılır.</li><li>Giden Web proxy 'si Kullanıcı tarafından yapılandırılabilir.</li><li>Sistem güncelleştirmelerine izin vermek için, bu bağlantı noktasının denetleyici sabit IP 'Leri için de açık olması gerekir.</li><li>Bu bağlantı noktası, her iki çöp toplama denetleyicisinde de kullanılır.</li></ul> |
| UDP 53 (DNS) |Çıkış |WAN |Bazı durumlarda; notlara bakın. |Bu bağlantı noktası yalnızca Internet tabanlı bir DNS sunucusu kullanıyorsanız gereklidir. |
| UDP 123 (NTP) |Çıkış |WAN |Bazı durumlarda; notlara bakın. |Bu bağlantı noktası yalnızca Internet tabanlı bir NTP sunucusu kullanıyorsanız gereklidir. |
| TCP 9354 |Çıkış |WAN |Evet |Giden bağlantı noktası StorSimple cihaz tarafından StorSimple Aygıt Yöneticisi hizmetiyle iletişim kurmak için kullanılır. |
| 3260 (Iscsı) |İçinde |LAN |Hayır |Bu bağlantı noktası, Iscsı üzerinden verilere erişmek için kullanılır. |
| 5985 |İçinde |LAN |Hayır |Gelen bağlantı noktası, StorSimple aygıtıyla iletişim kurmak için StorSimple Snapshot Manager tarafından kullanılır.<br>Bu bağlantı noktası, HTTP üzerinden StorSimple için Windows PowerShell uzaktan bağlandığınızda da kullanılır. |
| 5986 |İçinde |LAN |Hayır |Bu bağlantı noktası, HTTPS üzerinden StorSimple için Windows PowerShell uzaktan bağlandığınızda kullanılır. |

<sup>1</sup> genel Internet üzerinde hiçbir gelen bağlantı noktasının açık olması gerekmez.

<sup>2</sup> birden çok bağlantı noktası ağ geçidi yapılandırması içeriyorsa, giden yönlendirilen trafik sırası aşağıdaki [bağlantı noktası yönlendirmesinde](#routing-metric)açıklanan bağlantı noktası yönlendirme sırasına göre belirlenir.

<sup>3</sup> StorSimple cihazınızdaki denetleyici sabit IP 'leri yönlendirilebilir ve doğrudan veya yapılandırılmış Web proxy aracılığıyla Internet 'e bağlanabilir olmalıdır. Sabit IP adresleri, cihazdaki güncelleştirmelere ve çöp toplamaya hizmet vermek için kullanılır. Cihaz denetleyicileri, sabit IP 'Ler aracılığıyla Internet 'e bağlanamıyorsa, StorSimple cihazınızı güncellenemez ve çöp toplama işlemi düzgün çalışmayacaktır.

> [!IMPORTANT]
> Güvenlik duvarının StorSimple cihazı ve Azure arasındaki SSL trafiğini değiştirmediğinden veya şifresini çözmediğinden emin olun.


### <a name="url-patterns-for-firewall-rules"></a>Güvenlik duvarı kuralları için URL desenleri

Ağ yöneticileri, genellikle gelen ve giden trafiği filtrelemek için URL desenlerine göre gelişmiş güvenlik duvarı kuralları yapılandırabilir. StorSimple cihazınız ve StorSimple Aygıt Yöneticisi hizmeti, Azure Service Bus, Azure Active Directory Access Control, depolama hesapları ve Microsoft Update sunucuları gibi diğer Microsoft uygulamalarına bağımlıdır. Bu uygulamalarla ilişkili URL desenleri güvenlik duvarı kurallarını yapılandırmak için kullanılabilir. Bu uygulamalarla ilişkili URL desenlerinin değiştirebileceğini anlamak önemlidir. Bu işlem, ağ yöneticisinin StorSimple için güvenlik duvarı kurallarını ve gerektiğinde izlemesini ve güncelleştirmesini gerektirir.

Çoğu durumda, StorSimple sabit IP adreslerine bağlı olarak, giden trafik için güvenlik duvarı kurallarınızı ayarlamanızı öneririz. Bununla birlikte, güvenli ortamlar oluşturmak için gerekli olan gelişmiş güvenlik duvarı kurallarını ayarlamak için aşağıdaki bilgileri kullanabilirsiniz.

> [!NOTE]
> Cihaz (kaynak) IP 'Leri her zaman etkinleştirilmiş tüm ağ arabirimlerine ayarlanmalıdır. Hedef IP 'Ler, [Azure veri MERKEZI IP aralıkları](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653)olarak ayarlanmalıdır.


#### <a name="url-patterns-for-azure-portal"></a>Azure portal için URL desenleri

| URL kalıbı | Bileşen/Işlevsellik | Cihaz IP 'Leri |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |StorSimple Cihaz Yöneticisi hizmeti<br>Erişim Denetimi Hizmeti<br>Azure Service Bus<br>Kimlik Doğrulama Hizmeti |Bulut özellikli ağ arabirimleri |
| `https://*.backup.windowsazure.com` |Cihaz kaydı |Yalnızca VERI 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Sertifika iptali |Bulut özellikli ağ arabirimleri |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure depolama hesapları ve izleme |Bulut özellikli ağ arabirimleri |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update sunucuları<br> |Denetleyici yalnızca IP 'Leri düzeltildi |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Denetleyici yalnızca IP 'Leri düzeltildi |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Destek paketi |Bulut özellikli ağ arabirimleri |

#### <a name="url-patterns-for-azure-government-portal"></a>Azure Kamu portalı için URL desenleri

| URL kalıbı | Bileşen/Işlevsellik | Cihaz IP 'Leri |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |StorSimple Cihaz Yöneticisi hizmeti<br>Erişim Denetimi Hizmeti<br>Azure Service Bus<br>Kimlik Doğrulama Hizmeti |Bulut özellikli ağ arabirimleri |
| `https://*.backup.windowsazure.us` |Cihaz kaydı |Yalnızca VERI 0 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Sertifika iptali |Bulut özellikli ağ arabirimleri |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure depolama hesapları ve izleme |Bulut özellikli ağ arabirimleri |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update sunucuları<br> |Denetleyici yalnızca IP 'Leri düzeltildi |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |Denetleyici yalnızca IP 'Leri düzeltildi |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |Destek paketi |Bulut özellikli ağ arabirimleri |

### <a name="routing-metric"></a>Yönlendirme ölçümü

Yönlendirme ölçümü, verileri belirtilen ağlara yönlendiren arabirimler ve ağ geçidi ile ilişkilendirilir. Yönlendirme ölçümü, birden çok yolun aynı hedefe varolduğunu öğrenirse, belirli bir hedefin en iyi yolunu hesaplamak için yönlendirme protokolü tarafından kullanılır. Yönlendirme ölçümü ne kadar düşükse tercih daha yüksektir.

StorSimple bağlamında, birden çok ağ arabirimi ve ağ geçidi Kanal trafiği olarak yapılandırılırsa, arabirimlerin kullanılacağı göreli sırayı öğrenmek için yönlendirme ölçümleri yürütmeye gelir. Yönlendirme ölçümleri Kullanıcı tarafından değiştirilemez. Bununla birlikte, `Get-HcsRoutingTable` StorSimple cihazınızda yönlendirme tablosunu (ve ölçümleri) yazdırmak için cmdlet 'ini kullanabilirsiniz. [StorSimple dağıtımında sorun gidermeye](storsimple-troubleshoot-deployment.md)yönelik Get-HcsRoutingTable cmdlet 'i hakkında daha fazla bilgi.

Güncelleştirme 2 ve sonraki sürümler için kullanılan yönlendirme ölçüm algoritması aşağıdaki gibi açıklanabilir.

* Önceden tanımlanmış değerler kümesi ağ arabirimlerine atandı.
* Bulut etkin veya bulut devre dışı olduğunda, ancak yapılandırılmış bir ağ geçidiyle, çeşitli ağ arabirimlerine atanan değerlerin aşağıda gösterildiği örnek bir tablo düşünün. Burada atanan değerler yalnızca örnek değerlerdir.

    | Ağ arabirimi | Bulutta etkin | Bulut-ağ geçidi ile devre dışı |
    |-----|---------------|---------------------------|
    | Veri 0  | 1\.            | -                        |
    | Veri 1  | 2            | 20                       |
    | Veri 2  | 3            | 30                       |
    | Veri 3  | 4            | 40                       |
    | Veri 4  | 5            | 50                       |
    | Veri 5  | 6            | 60                       |


* Bulut trafiğinin ağ arabirimleri üzerinden yönlendirileceği sıra:
  
    *Veri 0 > veri 1 > Tarih 2 > veri 3 > veri 4 > Veri 5*
  
    Bu, aşağıdaki örnek tarafından açıklanabilir.
  
    Bulut özellikli iki ağ arabirimi, veri 0 ve veri 5 ile bir StorSimple cihazı düşünün. Veri 4 üzerinden veri 1, bulut devre dışı, ancak yapılandırılmış bir ağ geçidine sahip. Bu cihaz için trafiğin yönlendirileceği sıralama şu şekilde olacaktır:
  
    *Veri 0 (1) > Veri 5 (6) > veri 1 (20) > veri 2 (30) > veri 3 (40) > veri 4 (50)*
  
    *Parantez içindeki sayılar ilgili yönlendirme ölçümlerini gösterir.*
  
    Veriler 0 başarısız olursa, bulut trafiği veri 5 üzerinden yönlendirilir. Ağ geçidinin diğer tüm ağlar üzerinde yapılandırıldığı, hem veri 0 hem de veri 5 ' i başarısız olursa, bulut trafiği veri 1 ' den geçer.
* Bulut özellikli bir ağ arabirimi başarısız olursa, arabirime bağlanmak için 30 saniyelik bir gecikmeyle 3 yeniden deneme olur. Tüm yeniden denemeler başarısız olursa trafik, yönlendirme tablosu tarafından belirlendiği şekilde, bir sonraki kullanılabilir bulut etkin arabirimine yönlendirilir. Bulut özellikli tüm ağ arabirimleri başarısız olursa, cihaz diğer denetleyiciye yük devreder (Bu durumda yeniden başlatma yok).
* Iscsı özellikli bir ağ arabirimi için bir VIP hatası varsa 2 saniyelik bir gecikmeyle 3 yeniden deneme olur. Bu davranış önceki sürümlerden de aynı şekilde ifade etti. Tüm Iscsı ağ arabirimleri başarısız olursa, bir denetleyici yük devretmesi oluşur (bir yeniden başlatma ile birlikte).
* Bir VIP hatası olduğunda StorSimple cihazınızda da bir uyarı oluşturulur. Daha fazla bilgi için [Uyarı hızlı başvuru](storsimple-8000-manage-alerts.md)' ya gidin.
* Yeniden denemeler açısından Iscsı, buluta göre önceliklidir.
  
    Aşağıdaki örnek göz önünde bulundurun: StorSimple cihazında iki ağ arabirimi etkindir, veri 0 ve veri 1. Veri 0 bulutu etkindir, veri 1 hem bulut hem de Iscsı özellikli olur. Bu cihazdaki başka bir ağ arabirimi bulut veya Iscsı için etkin değil.
  
    Veri 1 başarısız olursa, son Iscsı ağ arabirimi olduğunda, bu, diğer denetleyicideki veri 1 ' e bir denetleyicinin yük devretmesine neden olur.

### <a name="networking-best-practices"></a>En iyi ağ uygulamaları

Yukarıdaki ağ gereksinimlerine ek olarak, StorSimple çözümünüzün en iyi performansı için lütfen aşağıdaki en iyi yöntemlere uyun:

* StorSimple cihazınızın, her zaman ayrılmış bir 40 Mbps bant genişliğine (veya daha fazlasına) sahip olduğundan emin olun. Bu bant genişliğinin diğer uygulamalarla paylaşılmaması gerekir (veya QoS ilkelerinin kullanılması aracılığıyla ayrılması gerekir).
* Internet 'e ağ bağlantısının her zaman kullanılabilir olduğundan emin olun. Internet bağlantısı dahil olmak üzere cihazlara bağımsız veya güvenilir olmayan Internet bağlantıları, desteklenmeyen bir yapılandırmaya neden olur.
* İSCSI ve bulut erişimi için cihazınızda adanmış ağ arabirimleri bulundurarak Iscsı ve bulut trafiğini yalıtın. Daha fazla bilgi için bkz. StorSimple cihazınızda [ağ arabirimlerini değiştirme](storsimple-8000-modify-device-config.md#modify-network-interfaces) .
* Ağ arabirimlerinizde bağlantı toplama Denetim Protokolü (LACP) yapılandırması kullanmayın. Bu, desteklenmeyen bir yapılandırmadır.

## <a name="high-availability-requirements-for-storsimple"></a>StorSimple için yüksek kullanılabilirlik gereksinimleri

StorSimple çözümüne dahil olan donanım platformunun, veri merkezinizde yüksek düzeyde kullanılabilir, hataya dayanıklı bir depolama altyapısı için bir temel sağlayan kullanılabilirlik ve güvenilirlik özellikleri vardır. Ancak, StorSimple çözümünüzün kullanılabilirliğini sağlamaya yardımcı olmak için uymanız gereken gereksinimler ve en iyi uygulamalar vardır. StorSimple 'ı dağıtmadan önce, StorSimple cihazı ve bağlı konak bilgisayarları için aşağıdaki gereksinimleri ve en iyi yöntemleri dikkatle gözden geçirin.

StorSimple cihazınızın donanım bileşenlerinin izlenmesi ve saklanması hakkında daha fazla bilgi için, bkz [. storsimple Aygıt Yöneticisi hizmetini kullanarak donanım bileşenlerini ve durum](storsimple-8000-monitor-hardware-status.md) ve [StorSimple donanım bileşeni değişimini izleyin ](storsimple-8000-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>StorSimple cihazınız için yüksek kullanılabilirlik gereksinimleri ve yordamları

StorSimple cihazınızın yüksek oranda kullanılabilirliğini sağlamak için aşağıdaki bilgileri dikkatle gözden geçirin.

#### <a name="pcms"></a>PCMs

StorSimple cihazları, yedekli, etkin, değiştirilebilir güç ve soğutma modülleri (PCMs) içerir. Her PCM, kasa genelinde hizmet sağlamak için yeterli kapasiteye sahiptir. Yüksek kullanılabilirlik sağlamak için hem PCMs 'nin yüklenmesi gerekir.

* Bir güç kaynağı başarısız olursa kullanılabilirlik sağlamak için PCMs 'nizi farklı güç kaynaklarına bağlayın.
* PCM başarısız olursa, hemen bir değiştirme isteyin.
* Hatalı bir PCM 'yi yalnızca değiştirme ve yüklemeye hazırlanma durumunda kaldırın.
* Her iki PCMs 'yi eşzamanlı olarak kaldırmayın. PCM modülü, yedekleme pili modülünü içerir. PCMs 'nin her ikisinin de kaldırılması, pil koruması olmadan kapanmaya neden olur ve cihaz durumu kaydedilmez. Pil hakkında daha fazla bilgi için [Yedekleme Pil modülünü koruyun](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module)sayfasına gidin.

#### <a name="controller-modules"></a>Denetleyici modülleri

StorSimple cihazları, yedekli, Hot-swapcontroller modülleri içerir. Denetleyici modülleri etkin/Pasif bir şekilde çalışır. Herhangi bir zamanda, bir denetleyici modülü etkindir ve hizmet sağlarken diğer denetleyici modülü pasif olur. Pasif denetleyici modülü açık olur ve etkin denetleyici modülü başarısız olursa veya kaldırılırsa çalışır duruma gelir. Her denetleyici modülünün, kasa genelinde hizmet sağlamak için yeterli kapasitesi vardır. Yüksek kullanılabilirlik sağlamak için hem denetleyici modüllerinin yüklenmesi gerekir.

* Her iki denetleyici modüllerinin de her zaman yüklü olduğundan emin olun.
* Bir denetleyici modülü başarısız olursa, hemen bir değiştirme isteyin.
* Başarısız bir denetleyici modülünü yalnızca değişiklik yaptığınız ve yüklemeye hazırsanız kaldırın. Genişletilmiş dönemler için bir modülün kaldırılması, Airflow 'u ve dolayısıyla sistemin soğutma olduğunu etkiler.
* Her iki denetleyici modülle ağ bağlantılarının aynı olduğundan ve bağlı ağ arabirimlerinin aynı ağ yapılandırmasına sahip olduğundan emin olun.
* Bir denetleyici modülü başarısız olursa veya değişiklik gerekiyorsa, başarısız denetleyici modülünü değiştirmeden önce diğer denetleyici modülünün etkin durumda olduğundan emin olun. Bir denetleyicinin etkin olduğunu doğrulamak için [cihazınızda etkin denetleyiciyi tanımla](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)' ya gidin.
* Her iki denetleyici modülünü aynı anda kaldırmayın. Bir denetleyici yük devretmesi devam ediyorsa, bekleme denetleyicisi modülünü kapatmayın veya gövdeden kaldırmayın.
* Denetleyici yük devretmesinin ardından, denetleyici modülünü kaldırmadan önce en az beş dakika bekleyin.

#### <a name="network-interfaces"></a>Ağ arabirimleri

StorSimple cihaz denetleyicisi modüllerinin her biri dört adet 1 Gigabit ve 2 10 Gigabit Ethernet ağ arabirimine sahiptir.

* Her iki denetleyici modülüne yönelik ağ bağlantılarının aynı olduğundan ve denetleyici modülü arabirimlerinin bağlandığı ağ arabirimlerinin aynı ağ yapılandırmasına sahip olduğundan emin olun.
* Mümkün olduğunda, bir ağ aygıtı arızası durumunda hizmet kullanılabilirliğini sağlamak için ağ bağlantılarını farklı anahtarlar arasında dağıtın.
* Yalnızca veya kalan en son Iscsı etkin arabirimini çıkarırken (IP atanmış olarak), önce arabirimi devre dışı bırakıp kabloları sökün. Arabirim önce söküme, etkin denetleyicinin pasif denetleyiciye yük devredememesine neden olur. Pasif denetleyicinin karşılık gelen arabirimleri de varsa, her iki denetleyici de bir denetleyicide kapatmadan önce birden çok kez yeniden başlatılır.
* Her denetleyici modülünden ağa en az iki VERI arabirimi bağlayın.
* 2 10 GbE arabirimlerini etkinleştirdiyseniz, bunları farklı anahtarlar genelinde dağıtın.
* Mümkün olduğunda, sunucuların bir bağlantı, ağ veya arabirim hatasına karşı tolerans sağlamak için sunucularda MPIO kullanın.

Cihazınızın yüksek kullanılabilirlik ve performans için ağ oluşturma hakkında daha fazla bilgi için, [storsimple 8100 cihazınızı yüklemeye](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) veya [StorSimple 8600 cihazınızı yüklemenize](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)gidin.

#### <a name="ssds-and-hdds"></a>SSD 'Ler ve HDD 'Ler

StorSimple cihazları, yansıtılmış boşluklar kullanılarak korunan katı hal diskleri (SSD 'Ler) ve sabit disk sürücüleri (HDD 'Ler) içerir. Yansıtılmış boşlukların kullanılması, cihazın bir veya daha fazla SSD veya HDD hatası olduğunu garanti edebilmesini sağlar.

* Tüm SSD ve HDD modüllerinin yüklü olduğundan emin olun.
* Bir SSD veya HDD başarısız olursa, hemen bir değiştirme isteyin.
* Bir SSD veya HDD başarısız olursa veya değişiklik gerektiriyorsa, yalnızca değiştirme gerektiren SSD veya HDD 'yi kaldırdığınızdan emin olun.
* Herhangi bir zamanda sistemden birden fazla SSD veya HDD kaldırmayın.
  Belirli türde (HDD, SSD) veya bir kısa zaman çerçevesinde ardışık hata 2 veya daha fazla disk arızası sistem arızası ve olası veri kaybına neden olabilir. Bu durumda yardım için [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md) .
* Değiştirme sırasında, SSD 'Ler ve HDD 'lerde bulunan sürücüler için **donanım sistem durumu** dikey penceresinde **paylaşılan bileşenleri** izleyin. Yeşil bir denetim durumu, disklerin sağlıklı veya Tamam olduğunu, ancak kırmızı bir ünlem işareti başarısız bir SSD veya HDD olduğunu gösterir.
* Bir sistem hatası durumunda korumanız gereken tüm birimler için bulut anlık görüntülerini yapılandırmanızı öneririz.

#### <a name="ebod-enclosure"></a>EBOD Kasası

StorSimple cihaz modeli 8600, birincil kutunun yanı sıra genişletilmiş bir dizi disk (EBOD) Kasası içerir. Bir EBOD, yansıtılmış boşluklar kullanılarak korunan EBOD denetleyicileri ve sabit disk sürücüleri (HDD 'Ler) içerir. Yansıtılmış boşlukların kullanılması, cihazın bir veya daha fazla HDD 'nin hatasını kabul edebilmesini sağlar. EBOD Kasası, yedekli SAS kabloları aracılığıyla birincil muhafaza ile bağlanır.

* Hem cbod Kasası denetleyici modüllerinin hem de SAS kablolarının ve tüm sabit disk sürücülerinin her zaman yüklü olduğundan emin olun.
* Bir EBOD Kasası denetleyicisi modülü başarısız olursa, hemen bir değiştirme isteyin.
* Bir EBOD Kasası denetleyicisi modülü başarısız olursa, başarısız modülün yerine geçmeden önce diğer denetleyici modülünün etkin olduğundan emin olun. Bir denetleyicinin etkin olduğunu doğrulamak için [cihazınızda etkin denetleyiciyi tanımla](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)' ya gidin.
* Ebod denetleyicisi modül değişikliği sırasında,**donanım sistem durumunu** **izlemeye** > erişerek StorSimple aygıt yöneticisi hizmetinde bileşenin durumunu sürekli olarak izleyin.
* SAS kablosu başarısız olursa veya değişiklik gerektiriyorsa (Bu tür bir belirleme yapmak için Microsoft Desteği dahil edilmelidir), yalnızca değiştirme gerektiren SAS kablosunu kaldırdığınızdan emin olun.
* Her zaman bir noktada, her iki SAS kablolarını sistemden eşzamanlı olarak kaldırmayın.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Ana bilgisayarlarınız için yüksek kullanılabilirlik önerileri

StorSimple cihazınıza bağlı olan konaklara yüksek kullanılabilirlik sağlamak için bu en iyi uygulamaları dikkatle gözden geçirin.

* [İki düğümlü dosya sunucusu kümesi yapılandırmasıyla][1]StorSimple 'ı yapılandırın. Tek hata noktalarını kaldırıp konak tarafında yedeklilik halinde derleme yaparak tüm çözüm yüksek oranda kullanılabilir hale gelir.
* Depolama denetleyicilerinin yük devretmesi sırasında yüksek kullanılabilirlik için Windows Server 2012 (SMB 3,0) ile kullanılabilen sürekli kullanılabilir (CA) paylaşımları kullanın. Windows Server 2012 ile dosya sunucusu kümelerini ve sürekli olarak kullanılabilir paylaşımları yapılandırma hakkında daha fazla bilgi için bu [video tanıtımı](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares)bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [StorSimple sistem sınırları hakkında bilgi edinin](storsimple-8000-limits.md).
* [StorSimple çözümünüzü dağıtmayı öğrenin](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
