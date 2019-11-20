---
title: Microsoft Azure StorSimple Sanal dizi sistem gereksinimleri | Microsoft Docs
description: StorSimple Sanal diziniz için yazılım ve ağ gereksinimleri hakkında bilgi edinin
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 65d2a21a9f40470cee1dd9d713f9f9cb5431a245
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516693"
---
# <a name="storsimple-virtual-array-system-requirements"></a>StorSimple Sanal Dizini sistem gereksinimleri

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Genel Bakış

Bu makalede, Microsoft Azure StorSimple Sanal diziniz ve diziye erişen depolama istemcileri için önemli sistem gereksinimleri açıklanmaktadır. StorSimple sisteminizi dağıtmadan önce bilgileri dikkatlice incelemenizi ve ardından dağıtım ve sonraki işlemler sırasında gerektiği şekilde geri başvurmalarını öneririz.

Sistem gereksinimleri şunları içerir:

* **Depolama istemcileri Için yazılım gereksinimleri** -desteklenen sanallaştırma platformları, Web tarayıcıları, iSCSI BAŞLATıCıLARı, SMB istemcileri, minimum sanal cihaz gereksinimleri ve bu işletim sistemleri için ek gereksinimler açıklanmaktadır.
* **StorSimple cihazı Için ağ gereksinimleri** -iSCSI, bulut veya Yönetim trafiğine izin vermek için güvenlik duvarınızdaki açık olması gereken bağlantı noktaları hakkında bilgi sağlar.

Bu makalede yayımlanan StorSimple sistem gereksinimleri bilgileri yalnızca StorSimple Sanal dizileri için geçerlidir.

* 8000 serisi cihazlarda, [StorSimple 8000 serisi cihazınız Için sistem gereksinimleri](storsimple-system-requirements.md)' ne gidin.
* 7000 serisi cihazlarda, [StorSimple 5000-7000 Serisi cihazınız Için sistem gereksinimleri](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements)' ne gidin.

## <a name="software-requirements"></a>Yazılım gereksinimleri
Yazılım gereksinimleri, desteklenen Web tarayıcıları, SMB sürümleri, sanallaştırma platformları ve en düşük sanal cihaz gereksinimleriyle ilgili bilgileri içerir.

### <a name="supported-virtualization-platforms"></a>Desteklenen sanallaştırma platformları
| **Yöneticiye** | **Sürüm** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 ve üzeri |
| VMware ESXi |5,0, 5,5, 6,0 ve 6,5. |

> [!IMPORTANT]
> StorSimple Sanal diziniz üzerine VMware araçları yüklemeyin; Bu, desteklenmeyen bir yapılandırmaya neden olur.

### <a name="virtual-device-requirements"></a>Sanal cihaz gereksinimleri
| **Bileşen** | **Gereksinim** |
| --- | --- |
| En az sanal işlemci sayısı (çekirdek) |4 |
| Minimum bellek (RAM) |8 GB <br> Bir dosya sunucusu için 2.000.000 ' den az dosya için 8 GB ve 2-4 milyon dosya için 16 GB|
| Disk alanı<sup>1</sup> |İşletim sistemi diski-80 GB <br></br>Veri diski-500 GB-8 TB |
| En az ağ arabirimi sayısı |1\. |
| Internet bant genişliği<sup>2</sup> |Minimum bant genişliği gereklidir: 5 Mbps <br> Önerilen bant genişliği: 100 Mbps <br> Veri aktarımının hızı Internet bant genişliğine göre ölçeklendirilir. Örneğin, günlük yedeklemeler gün içinde tamamlanamadığından, 100 GB veri, 5 Mbps hızında aktarım yapmak için 2 gün sürer. 100 Mbps 'lik bir bant genişliği ile 100 GB veri, 2,5 saat içinde aktarılabilir.   |

<sup>1</sup> -ölçülü kaynak sağlandı

<sup>2</sup> -ağ gereksinimleri günlük veri değişim hızına bağlı olarak değişebilir. Örneğin, bir cihazın gün içinde 10 GB veya daha fazla değişikliği yedeklemesi gerekiyorsa, 5 MB/sn 'Lik bir bağlantı üzerinden günlük yedekleme 4,25 saate kadar sürebilir (veriler sıkıştırııya da çoğaltılmamışsa).

### <a name="supported-web-browsers"></a>Desteklenen web tarayıcıları
| **Bileşen** | **Sürüm** | **Ek gereksinimler/notlar** |
| --- | --- | --- |
| Microsoft Edge |En son sürüm | |
| Internet Explorer |En son sürüm |Internet Explorer 11 ile test edilmiştir |
| Google Chrome |En son sürüm |Chrome 46 ile test edilmiştir |

### <a name="supported-storage-clients"></a>Desteklenen depolama istemcileri
Aşağıdaki yazılım gereksinimleri, StorSimple Sanal dizinizi (Iscsı sunucusu olarak yapılandırılmış) erişen Iscsı başlatıcılarına yöneliktir.

| **Desteklenen işletim sistemleri** | **Sürüm gerekli** | **Ek gereksinimler/notlar** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |StorSimple, ölçülü kaynak sağlanmış ve tam olarak sağlanan birimler oluşturabilir. Kısmen sağlanan birimler oluşturamaz. StorSimple Iscsı birimleri yalnızca için desteklenir: <ul><li>Windows temel disklerinde Basit birimler.</li><li>Bir birimi biçimlendirmek için Windows NTFS.</li> |

Aşağıdaki yazılım gereksinimleri, StorSimple Sanal dizinizi (bir dosya sunucusu olarak yapılandırılmış) erişen SMB istemcilerine yöneliktir.

| **SMB sürümü** |
| --- |
| SMB 2. x |
| SMB 3.0 |
| SMB 3,02 |

> [!IMPORTANT]
> Windows şifreleme dosya sistemi (EFS) tarafından korunan dosyaları StorSimple Sanal dizisi dosya sunucusuna kopyalamayın veya depolamamayın; Bu, desteklenmeyen bir yapılandırmaya neden olur.


### <a name="supported-storage-format"></a>Desteklenen depolama biçimi
Yalnızca Azure Blok Blobu depolama desteklenir. Sayfa Blobları desteklenmez. [Blok Blobları ve sayfa Blobları hakkında](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)daha fazla bilgi.

## <a name="networking-requirements"></a>Ağ gereksinimleri
Aşağıdaki tabloda Iscsı, SMB, bulut veya Yönetim trafiğine izin vermek için güvenlik duvarınızda açılması gereken bağlantı noktaları listelenmektedir. Bu *tabloda, veya* *gelen* , gelen istemci tarafından cihazınıza erişim isteğinin yönünü ifade eder. *Çıkış* veya *Çıkış* , StorSimple cihazınızın verileri dışarıdan, dağıtımın ötesinde (örneğin, Internet 'e giden) gönderdiği yönü ifade eder.

| **Bağlantı noktası No.<sup>1</sup>** | **Dışarı veya dışarı** | **Bağlantı noktası kapsamı** | **Gerekli** | **Notlar** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Çıkış |WAN |Hayır |Giden bağlantı noktası, güncelleştirmeleri almak için Internet erişimi için kullanılır. <br></br>Giden Web proxy 'si Kullanıcı tarafından yapılandırılabilir. |
| TCP 443 (HTTPS) |Çıkış |WAN |Evet |Giden bağlantı noktası, buluttaki verilere erişmek için kullanılır. <br></br>Giden Web proxy 'si Kullanıcı tarafından yapılandırılabilir. |
| UDP 53 (DNS) |Çıkış |WAN |Bazı durumlarda; notlara bakın. |Bu bağlantı noktası yalnızca Internet tabanlı bir DNS sunucusu kullanıyorsanız gereklidir. <br></br> Bir dosya sunucusu dağıtıyorsanız, yerel DNS sunucusu kullanmanızı öneririz. |
| UDP 123 (NTP) |Çıkış |WAN |Bazı durumlarda; notlara bakın. |Bu bağlantı noktası yalnızca Internet tabanlı bir NTP sunucusu kullanıyorsanız gereklidir.<br></br> Bir dosya sunucusu dağıtıyorsanız, Active Directory etki alanı denetleyicileriniz ile zaman eşitlemesini öneririz. |
| TCP 80 (HTTP) |İçinde |LAN |Evet |Bu, yerel yönetim için StorSimple cihazında yerel kullanıcı arabirimine yönelik gelen bağlantı noktasıdır. <br></br> HTTP üzerinden yerel kullanıcı arabirimine erişmenin otomatik olarak HTTPS 'ye yönlendirilmesini unutmayın. |
| TCP 443 (HTTPS) |İçinde |LAN |Evet |Bu, yerel yönetim için StorSimple cihazında yerel kullanıcı arabirimine yönelik gelen bağlantı noktasıdır. |
| TCP 3260 (Iscsı) |İçinde |LAN |Hayır |Bu bağlantı noktası, Iscsı üzerinden verilere erişmek için kullanılır. |

<sup>1</sup> genel Internet üzerinde hiçbir gelen bağlantı noktasının açık olması gerekmez.

> [!IMPORTANT]
> Güvenlik duvarının StorSimple cihazı ve Azure arasındaki SSL trafiğini değiştirmediğinden veya şifresini çözmediğinden emin olun.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Güvenlik duvarı kuralları için URL desenleri
Ağ yöneticileri, genellikle gelen ve giden trafiği filtrelemek için URL desenlerine göre gelişmiş güvenlik duvarı kuralları yapılandırabilir. Sanal diziniz ve StorSimple Aygıt Yöneticisi hizmeti, Azure Service Bus, Azure Active Directory Access Control, depolama hesapları ve Microsoft Update sunucuları gibi diğer Microsoft uygulamalarına bağımlıdır. Bu uygulamalarla ilişkili URL desenleri güvenlik duvarı kurallarını yapılandırmak için kullanılabilir. Bu uygulamalarla ilişkili URL desenlerinin değiştirebileceğini anlamak önemlidir. Bu işlem, ağ yöneticisinin StorSimple için güvenlik duvarı kurallarını ve gerektiğinde izlemesini ve güncelleştirmesini gerektirir. 

Çoğu durumda, StorSimple sabit IP adreslerine bağlı olarak, giden trafik için güvenlik duvarı kurallarınızı ayarlamanızı öneririz. Bununla birlikte, güvenli ortamlar oluşturmak için gerekli olan gelişmiş güvenlik duvarı kurallarını ayarlamak için aşağıdaki bilgileri kullanabilirsiniz.

> [!NOTE]
> 
> * Cihaz (kaynak) IP 'Leri her zaman bulut özellikli tüm ağ arabirimlerine ayarlanmalıdır. 
> * Hedef IP 'Ler, [Azure veri MERKEZI IP aralıkları](https://www.microsoft.com/download/confirmation.aspx?id=41653)olarak ayarlanmalıdır.
> 
> 

| URL kalıbı | Bileşen/Işlevsellik |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|StorSimple Cihaz Yöneticisi hizmeti<br>Erişim Denetimi Hizmeti<br>Azure Service Bus<br>Kimlik Doğrulama Hizmeti|
| `http://*.backup.windowsazure.com` |Cihaz kaydı |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Sertifika iptali |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure depolama hesapları ve izleme |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update sunucuları<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Destek paketi |
| `https://*.data.microsoft.com` |Telemetri hizmeti Windows 'da, bkz. [müşteri deneyimi ve tanılama telemetri güncelleştirmesi](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Sonraki adımlar
* [StorSimple Sanal dizinizi dağıtmak için portalı hazırlayın](storsimple-virtual-array-deploy1-portal-prep.md)
