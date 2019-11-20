---
title: StorSimple Sanal dizisi için en iyi uygulamalar | Microsoft Docs
description: StorSimple Sanal dizisinin dağıtımı ve yönetilmesi için en iyi yöntemleri açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: a8aed646f03b777722518152354cfe80cea043a0
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002814"
---
# <a name="storsimple-virtual-array-best-practices"></a>StorSimple Sanal dizi en iyi uygulamaları

## <a name="overview"></a>Genel Bakış

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Microsoft Azure StorSimple Sanal dizi, bir Hiper yöneticide çalışan bir şirket içi sanal cihaz ve bulut depolama Microsoft Azure arasındaki depolama görevlerini yöneten tümleşik bir depolama çözümüdür. StorSimple Sanal dizisi, 8000 serisi fiziksel dizisine yönelik verimli ve ekonomik bir alternatiftir. Sanal dizi, mevcut hiper yönetici altyapınız üzerinde çalışabilir, hem Iscsı hem de SMB protokollerini destekler ve uzak Office/şube ofis senaryolarına uygundur. StorSimple çözümleri hakkında daha fazla bilgi için [Microsoft Azure StorSimple genel bakış](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx)sayfasına gidin.

Bu makalede, StorSimple Sanal dizisinin ilk kurulumu, dağıtımı ve yönetimi sırasında uygulanan en iyi uygulamalar ele alınmaktadır. Bu en iyi uygulamalar, sanal dizinizi ayarlama ve yönetimi için doğrulanan yönergeler sağlar. Bu makale, veri merkezlerinde sanal dizileri dağıtan ve yöneten BT yöneticilerine yöneliktir.

Kurulum veya işlem akışında değişiklik yapıldığında cihazınızın hala uyumlu olduğundan emin olmak için en iyi uygulamaları düzenli bir şekilde gözden geçirmeniz önerilir. Sanal dizinizdeki bu en iyi yöntemleri uygularken herhangi bir sorunla karşılaşmanız gerekir, yardım için [Microsoft desteği başvurun](storsimple-virtual-array-log-support-ticket.md) .

## <a name="configuration-best-practices"></a>En iyi yapılandırma uygulamaları
Bu en iyi uygulamalar, sanal dizilerin ilk kurulumu ve dağıtımı sırasında izlenmesi gereken yönergeleri kapsar. Bu en iyi uygulamalar, sanal makine sağlama, Grup İlkesi ayarları, boyutlandırma, ağı ayarlama, depolama hesaplarını yapılandırma ve sanal dizi için paylaşımlar ve birimler oluşturma ile ilgili olanları içerir. 

### <a name="provisioning"></a>Sağlama
StorSimple Sanal dizisi, ana bilgisayar sunucunuzun Hiper yöneticide (Hyper-V veya VMware) sağlanan bir sanal makinedir (VM). Sanal makine sağlanırken, ana makinenizin yeterli kaynakları ayırabildiğinden emin olun. Daha fazla bilgi için, bir dizi sağlamak üzere [En düşük kaynak gereksinimlerine](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) gidin.

Sanal diziyi sağlarken aşağıdaki en iyi yöntemleri uygulayın:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Sanal makine türü** |**2. nesil** Windows Server 2012 veya üzeri ve *. vhdx* görüntüsüyle kullanılacak VM. <br></br> **1. nesil** Windows Server 2008 veya üzeri ile bir *. vhd* görüntüsüyle kullanılacak VM. |*. Vmdk* görüntüsünü kullanırken sanal makine sürüm 8 ' i kullanın. |
| **Bellek türü** |**Statik bellek**olarak yapılandırın. <br></br> **Dinamik bellek** seçeneğini kullanmayın. | |
| **Veri diski türü** |Dinamik olarak **genişleyen**sağlama.<br></br> **Sabit boyut** uzun zaman alır. <br></br> **Fark kayıt** seçeneğini kullanmayın. |**Ölçülü kaynak sağlama** seçeneğini kullanın. |
| **Veri diski değişikliği** |Genişletme veya küçültme yapılmasına izin verilmez. Bunun bir girişimi, cihazdaki tüm yerel verilerin kaybedilmesine neden olur. |Genişletme veya küçültme yapılmasına izin verilmez. Bunun bir girişimi, cihazdaki tüm yerel verilerin kaybedilmesine neden olur. |

### <a name="sizing"></a>Boyutlandırma
StorSimple Sanal dizinizi boyutlandırdığınızda aşağıdaki faktörleri göz önünde bulundurun:

* Birimler veya paylaşımlar için yerel ayırma. Alanın yaklaşık% 12 ' i, sağlanan her katmanlı birim veya paylaşımın yerel katmanında ayrılmıştır. Alanın yaklaşık% 10 ' unun, dosya sistemi için yerel olarak sabitlenmiş bir birim için de ayrılmış olması.
* Anlık görüntü yükü. Yerel katmanda yaklaşık% 15 alan anlık görüntüler için ayrılmıştır.
* Geri yüklemeler gerekiyor. Geri yükleme yeni bir işlem olarak yapıldığında, boyutlandırma için gereken alan için boyutlandırma hesabı olmalıdır. Geri yükleme işlemi, aynı boyuttaki bir paylaşıma veya birime yapılır.
* Bazı arabellekler beklenmeyen büyüme için ayrılmalıdır.

Önceki faktörlere bağlı olarak, boyutlandırma gereksinimleri aşağıdaki denklemle temsil edilebilir:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Aşağıdaki örneklerde, gereksinimlerinize göre bir sanal diziyi nasıl kullanabileceğiniz gösterilmektedir.

#### <a name="example-1"></a>Örnek 1:
Sanal diziniz üzerinde şunları yapabilmek istiyorsunuz

* 2 TB katmanlı bir birim veya paylaşma sağlayın.
* 1 TB katmanlı birim veya paylaşma sağlayın.
* Yerel olarak sabitlenmiş bir birim veya Share 300 GB sağlayın.

Önceki birimler veya paylaşımlar için yerel katmanda alan gereksinimlerini hesaplamıza izin verin.

İlk olarak, her katmanlı birim/paylaşılan için yerel rezervasyon, birim/paylaşma boyutunun% 12 ' ye eşit olacaktır. Yerel olarak sabitlenmiş birim/paylaşma için yerel ayırma, yerel olarak sabitlenmiş birim/paylaşımın boyutunun% 10 ' u olur (sağlanan boyuta ek olarak). Bu örnekte, şunları yapmanız gerekir

* 240 GB yerel ayırma (2 TB katmanlı birim/paylaşma için)
* 120 GB yerel ayırma (1 TB katmanlı birim/paylaşma için)
* Yerel olarak sabitlenmiş birim veya paylaşma için 330-GB (sağlanan boyut 300 GB 'a yerel ayırma% 10)

Şu ana kadar yerel katmanda gereken toplam alan: 240 GB + 120 GB + 330 GB = 690 GB.

İkinci olarak, en büyük tek ayırma olarak yerel katmanda en az daha fazla alan olması gerekir. Bu ek miktar, bir bulut anlık görüntüsünden geri yüklemeniz gereken durumlarda kullanılır. Bu örnekte, en büyük yerel ayırma 330 GB 'tır (dosya sistemi için rezervasyon dahil), bu nedenle bunu 690 GB 'a eklersiniz: 690 GB + 330 GB = 1020 GB.
Sonraki ek geri yüklemeleri gerçekleştirmemiz durumunda, alanı her zaman önceki geri yükleme işleminden serbest bırakabilirsiniz.

Üçüncü olarak, yerel anlık görüntüleri depolamak için toplam yerel alanın% 15 ' i için yalnızca% 85 kullanılabilir olması gerekir. Bu örnekte, 1020 GB = 0,85&ast;tarafından sağlanan veri diski TB ' nin altında olacaktır. Bu nedenle, sağlanan veri diski (1020&ast;(1/0.85)) = 1200 GB = 1,20 TB ~ 1,25 TB (en yakın dörttebirine yuvarlama) olacaktır

Düzenleme beklenmeyen büyüme ve yeni geri yüklemeler içinde, 1,25-1,5 TB 'lık bir yerel disk sağlamanız gerekir.

> [!NOTE]
> Ayrıca yerel diskin ölçülü kaynak sağlaması önerilir. Bu öneri, geri yükleme alanının yalnızca beş günden eski verileri geri yüklemek istediğinizde gerekli olmasından kaynaklanır. Öğe düzeyinde kurtarma, geri yükleme için ek alan gerekmeden verileri son beş güne geri yüklemenize olanak tanır.


#### <a name="example-2"></a>Örnek 2:
Sanal diziniz üzerinde şunları yapabilmek istiyorsunuz

* 2 TB katmanlı birim sağlama
* 300 GB yerel olarak sabitlenmiş bir birim sağlama

Katmanlı birimler/paylaşımlar için yerel alan ayırmasının% 12 ' i ve yerel olarak sabitlenmiş birimler/paylaşımlar için% 10 ' a göre

* 240 GB yerel ayırma (2 TB katmanlı birim/paylaşma için)
* Yerel olarak sabitlenmiş birim veya paylaşma için 330-GB (300 GB sağlanan alana yerel ayırmanın% 10 ' unu ekleyerek)

Yerel katmanda gereken toplam alan: 240 GB + 330 GB = 570 GB

Geri yükleme için gereken en az yerel alan 330 GB 'dir.

Toplam diskinizin% 15 ' i, anlık görüntüleri yalnızca 0,85 kullanılabilir olacak şekilde depolamak için kullanılır. Bu nedenle disk boyutu (900&ast;(1/0.85)) = 1,06 TB ~ 1,25 TB (en yakın dörttebirine yuvarlama)

Düzenleme beklenmeyen büyümeye karşı, 1,25-1,5 TB yerel disk sağlayabilirsiniz.

### <a name="group-policy"></a>Grup ilkesi
Grup ilkesi, kullanıcılar ve bilgisayarlar için belirli konfigürasyonlar uygulamanıza olanak tanıyan bir altyapısıdır. Grup ilkesi ayarları, aşağıdaki Active Directory Domain Services (AD DS) kapsayıcılarıyla bağlantılı grup ilkesi nesneler (GPO) içinde bulunur: siteler, etki alanları veya kuruluş birimleri (OU). 

Sanal diziniz etki alanına katılmış ise, GPO 'Lara uygulanabilir. Bu GPO 'Lar, StorSimple Sanal dizisinin işlemini olumsuz yönde etkileyebilecek bir virüsten koruma yazılımı gibi uygulamaları yükleyebilir.

Bu nedenle şunları yapmanızı öneririz:

* Sanal diziniz Active Directory için kendi kuruluş biriminde (OU) olduğundan emin olun.
* Sanal dizize hiçbir Grup İlkesi nesnesi (GPO 'Lar) uygulanmadığından emin olun. Sanal dizinin (alt düğüm) üst öğeden hiçbir GPO 'yu otomatik olarak devralmamasından emin olmak için devralmayı engelleyebilirsiniz. Daha fazla bilgi için, [blok devralma](https://technet.microsoft.com/library/cc731076.aspx)bölümüne gidin.

### <a name="networking"></a>Ağ
Sanal diziniz için ağ yapılandırması yerel Web Kullanıcı arabirimi üzerinden yapılır. Sanal bir ağ arabirimi, sanal dizinin sağlandığı hiper yönetici aracılığıyla etkinleştirilir. Sanal ağ arabirimi IP adresini, alt ağını ve ağ geçidini yapılandırmak için [ağ ayarları](storsimple-virtual-array-deploy3-fs-setup.md) sayfasını kullanın.  Ayrıca, cihazınız için birincil ve ikincil DNS sunucusunu, zaman ayarlarını ve isteğe bağlı ara sunucu ayarlarını da yapılandırabilirsiniz. Ağ yapılandırmasının çoğu tek seferlik bir kurulumdır. Sanal diziyi dağıtmadan önce [StorSimple ağ gereksinimlerini](storsimple-ova-system-requirements.md#networking-requirements) gözden geçirin.

Sanal diziniz dağıtıldığında, bu en iyi yöntemleri izlemeniz önerilir:

* Sanal dizinin dağıtıldığı ağın her zaman 5 Mbps Internet bant genişliği (veya daha fazla) ayırmak için kapasiteye sahip olduğundan emin olun.
  
  * Internet bant genişliği gereksinimi, iş yükünün özelliklerine ve veri değişikliği hızına bağlı olarak değişir.
  * İşlenebilecek veri değişikliği, Internet bant genişliğinizin doğrudan orantılıdır. Bir yedek alırken, 5 Mbps 'lik bir bant genişliği 8 saat içinde 18 GB 'lık bir veri değişikliğine uyum sağlayabilir. Dört kat daha fazla bant genişliği (20 Mbps) ile dört kat daha fazla veri değişikliğini (72 GB) işleyebilirsiniz.
* Internet bağlantısının her zaman kullanılabilir olduğundan emin olun. Cihazlara tek veya güvenilir olmayan Internet bağlantıları, buluttaki verilere erişim kaybına neden olabilir ve desteklenmeyen bir yapılandırmaya neden olabilir.
* Cihazınızı bir Iscsı sunucusu olarak dağıtmayı planlıyorsanız:
  
  * **IP adresini otomatik olarak al** SEÇENEĞINI (DHCP) devre dışı bırakmanızı öneririz.
  * Statik IP adreslerini yapılandırın. Birincil ve ikincil bir DNS sunucusu yapılandırmanız gerekir.
  * Sanal diziniz üzerinde birden çok ağ arabirimi tanımlıyorsanız, yalnızca ilk ağ arabirimi (varsayılan olarak, bu arabirim **Ethernet**'dir) buluta ulaşabilir. Trafik türünü denetlemek için, sanal diziniz üzerinde (Iscsı sunucusu olarak yapılandırılmış) birden çok sanal ağ arabirimi oluşturabilir ve bu arabirimleri farklı alt ağlara bağlayabilirsiniz.
* Yalnızca bulut bant genişliğini azaltmak için (sanal dizi tarafından kullanılır), yönlendiricide veya güvenlik duvarında azaltma yapılandırın. Hiper yöneticide daraltma tanımlarsanız, yalnızca bulut bant genişliği yerine Iscsı ve SMB dahil olmak üzere tüm protokollerin sayısını ortadan kaldırılır.
* Hiper yöneticilerin zaman eşitlemesinin etkinleştirildiğinden emin olun. Hyper-v kullanıyorsanız, Hyper-v Yöneticisi 'nde sanal dizinizi seçin, **Ayarlar &gt; Tümleştirme Hizmetleri**' ne gidin ve **zaman eşitlemenin** denetlendiğinden emin olun.

### <a name="storage-accounts"></a>Depolama hesapları
StorSimple Sanal dizisi, tek bir depolama hesabıyla ilişkilendirilebilir. Bu depolama hesabı, otomatik olarak oluşturulan bir depolama hesabı, hizmetle aynı abonelikte bulunan bir hesap veya başka bir abonelikle ilişkili bir depolama hesabı olabilir. Daha fazla bilgi için bkz. [sanal diziniz için depolama hesaplarını yönetme](storsimple-virtual-array-manage-storage-accounts.md).

Sanal diziniz ile ilişkili depolama hesapları için aşağıdaki önerileri kullanın.

* Birden çok sanal diziyi tek bir depolama hesabıyla bağlarken, bir sanal dizi için maksimum kapasite (64 TB) ve bir depolama hesabı için maksimum boyut (500 TB) cinsinden faktörü. Bu, bu depolama hesabı ile ilişkilendirilebilen tam boyutlu sanal dizilerin sayısını 7 ile sınırlandırır.
* Yeni bir depolama hesabı oluştururken
  
  * Gecikme sürelerini en aza indirmek için StorSimple Sanal diziniz dağıtıldığı uzak Office/şube ofise en yakın bölgede oluşturmanız önerilir.
  * Bir depolama hesabını farklı bölgelerde taşıyamayacağınızı göz önünde bulundurun. Ayrıca, bir hizmeti abonelikler arasında taşıyamazsınız.
  * Veri merkezleri arasında artıklık uygulayan bir depolama hesabı kullanın. Coğrafi olarak yedekli depolama (GRS), bölge yedekli depolama (ZRS) ve yerel olarak yedekli depolama (LRS), sanal diziniz ile kullanım için desteklenir. Farklı türlerde depolama hesapları hakkında daha fazla bilgi için [Azure Storage çoğaltma](../storage/common/storage-redundancy.md)' ya gidin.

### <a name="shares-and-volumes"></a>Paylaşımlar ve birimler
StorSimple Sanal diziniz için, bir Iscsı sunucusu olarak yapılandırıldığında bir dosya sunucusu ve birim olarak yapılandırıldığında paylaşımlar sağlayabilirsiniz. Paylaşımlar ve birimler oluşturmak için en iyi uygulamalar, boyut ve yapılandırılan tür ile ilgilidir.

#### <a name="volumeshare-size"></a>Birim/paylaşma boyutu
Sanal diziniz üzerinde, bir Iscsı sunucusu olarak yapılandırıldığında dosya sunucusu ve birimler olarak yapılandırıldığında paylaşımlar sağlayabilirsiniz. Paylaşımlar ve birimler oluşturmak için en iyi uygulamalar, boyut ve yapılandırılan tür ile ilgilidir. 

Sanal cihazınızda paylaşımlar veya birimler sağlarken aşağıdaki en iyi uygulamaları aklınızda bulundurun.

* Katmanlı bir paylaşımın sağlanan boyutuna göre dosya boyutları katmanlama performansını etkileyebilir. Büyük dosyalarla çalışma, yavaş bir katmana neden olabilir. Büyük dosyalarla çalışırken, en büyük dosyanın paylaşma boyutunun% 3 ' inden küçük olmasını öneririz.
* Sanal dizi üzerinde en fazla 16 birim/paylaşım oluşturulabilir. Yerel olarak sabitlenmiş ve katmanlı birimlerin/paylaşımların boyut sınırları için her zaman [StorSimple Sanal dizi sınırlarına](storsimple-ova-limits.md)başvurun.
* Bir birim oluştururken, beklenen veri tüketiminde ve gelecekteki büyüme için faktör. Birim daha sonra genişletilemiyor.
* Birim oluşturulduktan sonra StorSimple üzerinde birimin boyutunu daraltamazsınız.
* StorSimple üzerinde katmanlı bir birime yazarken, birim verileri belirli bir eşiğe ulaştığında (birim için ayrılan yerel alana göre), GÇ kısıtlanıyor demektir. Bu birime yazmaya devam etmek GÇ 'yi önemli ölçüde yavaşlatır. Sağlanan kapasitesinin ötesinde katmanlı bir birime yazabilseniz de (kullanıcının sağlanan kapasitenin ötesine yazmasını etkin bir şekilde durdurmayın), aşırı abone olduğunuz etkiye yönelik bir uyarı bildirimi görürsünüz. Uyarıyı gördüğünüzde, birim verilerini silme (birim genişletmesi Şu anda desteklenmiyor) gibi düzeltici önlemler almanız zorunludur.
* Olağanüstü durum kurtarma kullanım örnekleri için, izin verilen paylaşımların/birimlerin sayısı 16 ve paralel olarak işlenebileceği en fazla paylaşım/birim sayısı da 16 ise, paylaşımların/birimlerin sayısında RPO ve RTOs üzerinde bir pul yoktur.

#### <a name="volumeshare-type"></a>Birim/paylaşma türü
StorSimple, kullanıma bağlı olarak iki birim/paylaşma türünü destekler: yerel olarak sabitlenmiş ve katmanlı. Yerel olarak sabitlenmiş birimler/paylaşımlar bol alanla sağlanır, katmanlı birimler/paylaşımlar ise ölçülü kaynak olarak sağlanır. Yerel olarak sabitlenmiş bir birimi/payı, oluşturulduktan sonra katmanlı veya *tam tersi* olarak dönüştüremezsiniz.

StorSimple birimlerini/paylaşımlarını yapılandırırken aşağıdaki en iyi yöntemleri uygulamanız önerilir:

* Birim türünü, bir birim oluşturmadan önce dağıtmayı planladığınız iş yüklerine göre belirler. Yerel olarak sabitlenmiş birimler (bir bulut kesintisi da dahil olmak üzere) ve düşük bulut gecikme süreleri gerektiren iş yükleri için yerel olarak sabitlenmiş birimler kullanın. Sanal diziniz üzerinde bir birim oluşturduktan sonra, birim türünü yerel olarak sabitlenmiş veya *tam tersi*olarak değiştiremezsiniz. Örnek olarak, sanal makineleri (VM 'Ler) barındıran SQL iş yüklerini veya iş yüklerini dağıttığınızda yerel olarak sabitlenmiş birimler oluşturun; dosya paylaşma iş yükleri için katmanlı birimleri kullanın.


#### <a name="volume-format"></a>Birim biçimi
Iscsı sunucunuzda StorSimple birimleri oluşturduktan sonra birimleri başlatmalısınız, bağlamanız ve biçimlendirmeniz gerekir. Bu işlem, StorSimple cihazınıza bağlı olan konakta gerçekleştirilir. StorSimple ana bilgisayarındaki birimleri bağlama ve biçimlendirme sırasında aşağıdaki en iyi yöntemler önerilir.

* Tüm StorSimple birimlerinde hızlı biçimlendirme gerçekleştirin.
* StorSimple birimini biçimlendirirken, 64 KB 'lik bir ayırma birimi boyutu (Avustralya) kullanın (varsayılan değer 4 KB 'dir). 64 KB au, yaygın StorSimple iş yükleri ve diğer iş yükleri için şirket içinde yapılan testi temel alır.
* Bir Iscsı sunucusu olarak yapılandırılmış StorSimple Sanal dizisi kullanılırken, bu birimler veya diskler StorSimple tarafından desteklenmediğinden yayılmış birimleri veya dinamik diskleri kullanmayın.

#### <a name="share-access"></a>Erişimi paylaşma
Sanal dizi dosya sunucunuzda paylaşımlar oluştururken şu yönergeleri izleyin:

* Bir paylaşma oluştururken, bir kullanıcı grubunu tek bir kullanıcı yerine bir paylaşılan yönetici olarak atayın.
* Paylaşımları Windows Gezgini aracılığıyla düzenleyerek paylaşım oluşturulduktan sonra NTFS izinlerini yönetebilirsiniz.

#### <a name="volume-access"></a>Birim erişimi
StorSimple Sanal dizinizdeki Iscsı birimleri yapılandırılırken, erişimin gerektiği her yerde denetlenmesi önemlidir. Hangi konak sunucularının birimlere erişebileceğini, erişim denetim kayıtlarını oluşturup (ACRs) StorSimple birimleriyle ilişkilendireceğini belirleyin.

StorSimple birimleri için ACRs 'yi yapılandırırken aşağıdaki en iyi yöntemleri kullanın:

* Her zaman en az bir ACR 'yi bir birimle ilişkilendirin.

* Bir birime birden fazla ACR atarken, birimin birden fazla kümelenmemiş konak tarafından aynı anda erişilebilen bir şekilde kullanıma sunulmadığından emin olun. Bir birime birden fazla ACRs atadıysanız, yapılandırmanızı gözden geçirmeniz için bir uyarı iletisi açılır.

### <a name="data-security-and-encryption"></a>Veri güvenliği ve şifreleme
StorSimple Sanal diziniz, verilerinizin gizliliğini ve bütünlüğünü sağlayan veri güvenliğine ve şifreleme özelliklerine sahiptir. Bu özellikleri kullanırken, bu en iyi yöntemleri izlemeniz önerilir: 

* Veri sanal dizinizden buluta gönderilmeden önce AES-256 şifrelemesi oluşturmak için bir bulut depolama şifreleme anahtarı tanımlayın. Verileriniz ile başlamak üzere şifrelendiyse bu anahtar gerekli değildir. Anahtar, [Azure Anahtar Kasası](../key-vault/key-vault-overview.md)gibi bir anahtar yönetim sistemi kullanılarak oluşturulabilir ve güvenli bir şekilde saklanabilir.
* StorSimple Yöneticisi hizmeti aracılığıyla depolama hesabını yapılandırırken, StorSimple cihazınız ve bulut arasında ağ iletişimi için güvenli bir kanal oluşturmak üzere SSL modunu etkinleştirdiğinizden emin olun.
* Değiştirilen Yöneticiler listesine göre erişim değişiklikleri için düzenli aralıklarla depolama hesaplarınız (Azure Storage Service 'e erişerek) anahtarlarını yeniden oluşturun.
* Sanal dizinizdeki veriler Azure 'a gönderilmeden önce sıkıştırılır ve yinelenenleri kaldırılmış. Windows Server konağında yinelenen verileri kaldırma rol hizmetini kullanmanızı önermiyoruz.

## <a name="operational-best-practices"></a>İşletimsel en iyi uygulamalar
İşletimsel en iyi uygulamalar, sanal dizinin günlük yönetimi veya çalışması sırasında izlenmesi gereken kılavuzlardır. Bu uygulamalar, yedeklemeleri alma, bir yedekleme kümesinden geri yükleme, yük devretme gerçekleştirme, diziyi devre dışı bırakma ve silme, sistem kullanımını ve sistem durumunu izleme ve sanal dizinizdeki virüs taramalarını çalıştırma gibi belirli yönetim görevlerini kapsar.

### <a name="backups"></a>Yedeklemeler
Sanal dizinizdeki veriler buluta yedeklenir. Bu, varsayılan olarak tüm cihazın 22:30 ' den başlayarak veya el ile isteğe bağlı bir yedekten bir otomatik günlük yedeklemesini sağlar. Varsayılan olarak cihaz, üzerinde bulunan tüm verilerin günlük bulut anlık görüntülerini otomatik olarak oluşturur. Daha fazla bilgi için, [StorSimple Sanal dizinizi yedekleyin](storsimple-virtual-array-backup.md)bölümüne gidin.

Varsayılan yedeklemelerle ilişkili sıklık ve bekletme değiştirilemez, ancak günlük yedeklemelerin her gün başlatıldığı saati yapılandırabilirsiniz. Otomatik yedeklemeler için başlangıç saatini yapılandırırken şunları öneririz:

* Yedeklemelerinizi yoğun olmayan saatlere zamanlayın. Yedekleme başlangıç saati çok sayıda konak GÇ ile aynı olmamalıdır.
* Sanal dizinizdeki verileri korumak için bir cihaz yük devretmesi veya bakım penceresinden önce, el ile isteğe bağlı yedekleme başlatın.

### <a name="restore"></a>Geri yükle
Bir yedekleme kümesinden iki şekilde geri yükleme yapabilirsiniz: başka bir birime geri yükleme veya öğe düzeyinde kurtarma (yalnızca dosya sunucusu olarak yapılandırılmış bir sanal dizi üzerinde kullanılabilir) gerçekleştirme. Öğe düzeyinde kurtarma, StorSimple cihazındaki tüm paylaşımların bir bulut yedeğinden dosya ve klasörlerin ayrıntılı bir şekilde kurtarılmasını sağlar. Daha fazla bilgi için [bir yedekten geri yükleme](storsimple-virtual-array-clone.md)bölümüne gidin.

Geri yükleme gerçekleştirirken aşağıdaki yönergeleri aklınızda bulundurun:

* StorSimple Sanal diziniz yerinde geri yüklemeyi desteklemiyor. Bu, ancak iki adımlı bir işlem tarafından kolayca sağlanabilir: sanal dizide yer açın ve sonra başka bir birime/paylaşıma geri yükleyin.
* Yerel bir birimden geri yükleme yaparken, geri yüklemenin uzun süre çalışan bir işlem olacağını aklınızda bulundurun. Birim hızla çevrimiçi olarak gelebilse de, veriler arka planda ortaya çıkmaya devam eder.
* Birim türü, geri yükleme işlemi sırasında aynı kalır. Katmanlı bir birim, başka bir katmanlı birime ve yerel olarak sabitlenmiş bir birime başka bir yerel olarak sabitlenmiş birime geri yüklenir.
* Yedekleme kümesinden bir birimi veya bir paylaşımın geri yüklenmeye çalışıldığında, geri yükleme işi başarısız olursa, portalda bir hedef birim veya paylaşma yine de oluşturulabilir. Bu öğeden doğan gelecekteki sorunları en aza indirmek için, bu kullanılmayan hedef birimi veya paylaşmak için portalda silmeniz önemlidir.

### <a name="failover-and-disaster-recovery"></a>Yük devretme ve olağanüstü durum kurtarma
Bir cihaz yük devretmesi, veri merkezindeki bir *kaynak* cihazdan aynı veya farklı coğrafi konumda bulunan başka bir *hedef* cihaza geçiş yapmanıza olanak sağlar. Cihaz yük devretmesi tüm cihaza yöneliktir. Yük devretme sırasında kaynak cihaz için bulut verileri, hedef cihazın sahipliğini değiştirir.

StorSimple Sanal diziniz için, yalnızca aynı StorSimple Yöneticisi hizmeti tarafından yönetilen başka bir sanal diziye yük devreder. 8000 serisi bir cihaza veya farklı bir StorSimple Manager hizmeti (kaynak cihazından) yönetilen bir diziye yük devretme yapılmasına izin verilmez. Yük devretme konuları hakkında daha fazla bilgi edinmek için [cihaz yük devretmesi önkoşulları](storsimple-virtual-array-failover-dr.md)bölümüne gidin.

Sanal diziniz için yük devretme gerçekleştirirken şunları aklınızda tutun:

* Planlı Yük devretme için, yük devretmeyi başlatmadan önce tüm birimleri/paylaşımları çevrimdışı duruma getirme önerilen en iyi uygulamadır. Öncelikle konakta bulunan birimleri/paylaşımları çevrimdışına almak için işletim sistemine özgü yönergeleri izleyin ve ardından sanal cihazınızda çevrimdışı duruma getirin.
* Bir dosya sunucusu olağanüstü durum kurtarma (DR) için, paylaşma izinlerinin otomatik olarak çözülmesi için hedef cihazın kaynakla aynı etki alanına katılması önerilir. Bu sürümde yalnızca aynı etki alanındaki bir hedef cihaza yük devretme desteklenir.
* DR başarıyla tamamlandıktan sonra kaynak cihaz otomatik olarak silinir. Cihaz artık kullanılamıyor olsa da, konak sisteminde sağladığınız sanal makine halen kaynak tüketiyor. Tahakkuk eden ücretleri engellemek için bu sanal makineyi konak sisteminizden silmenizi öneririz.
* Yük devretme başarısız olsa bile, **verilerin bulutta her zaman güvenli**olduğunu unutmayın. Yük devretmenin başarıyla tamamlanmadığından aşağıdaki üç senaryoyu göz önünde bulundurun:
  
  * Yük devretmenin, DR ön denetimleri gerçekleştirilirken olduğu gibi ilk aşamalarında bir hata oluştu. Bu durumda, hedef cihazınız hala kullanılabilir. Aynı hedef cihazda yük devretmeyi yeniden deneyebilirsiniz.
  * Gerçek yük devretme işlemi sırasında bir hata oluştu. Bu durumda, hedef cihaz kullanılamaz olarak işaretlenir. Başka bir hedef sanal dizi sağlamalısınız ve yapılandırmanız ve bu işlemi yük devretme için kullanmanız gerekir.
  * Kaynak cihazın silindiği ancak hedef cihazda sorunlar olduğu ve herhangi bir veriye erişemediği için yük devretme tamamlanmıştır. Veriler bulutta hala güvenlidir ve başka bir sanal dizi oluşturup daha sonra DR için hedef cihaz olarak kullanılarak kolayca alınabilir.

### <a name="deactivate"></a>Devre dışı bırak
StorSimple Sanal dizisini devre dışı bıraktıktan sonra, cihazla ilgili StorSimple Manager hizmeti arasındaki bağlantıyı sanal olarak kullanırsınız. Devre dışı bırakma **kalıcı** bir işlemdir ve geri alınamaz. Devre dışı bırakılmış bir cihaz, StorSimple Yöneticisi hizmetine yeniden kaydedilemez. Daha fazla bilgi için, [devre dışı bırak ' a gidin ve StorSimple Sanal dizinizi silin](storsimple-virtual-array-deactivate-and-delete-device.md).

Sanal dizinizi devre dışı bırakadığınızda aşağıdaki en iyi uygulamaları aklınızda bulundurun:

* Sanal bir cihazı devre dışı bırakmadan önce tüm verilerin bir bulut anlık görüntüsünü alın. Bir sanal diziyi devre dışı bıraktıktan sonra tüm yerel cihaz verileri kaybedilir. Bir bulut anlık görüntüsünün alınması, verileri daha sonraki bir aşamada kurtarmanıza izin verir.
* StorSimple Sanal dizisini devre dışı bırakmadan önce, söz konusu cihaza bağlı istemcileri ve Konakları durdurmayı veya silmeyi unutmayın.
* Artık kullanmıyorsanız, devre dışı bırakılmış bir cihazı, ücretleri tahakkuk ettirilmeyen bir cihaza silin.

### <a name="monitoring"></a>İzleme
StorSimple Sanal diziniz sürekli sağlıklı bir durumda olduğundan emin olmak için, diziyi izlemeniz ve uyarılar dahil olmak üzere sistemden bilgi aldığınızdan emin olmanız gerekir. Sanal dizinin genel durumunu izlemek için aşağıdaki en iyi yöntemleri uygulayın:

* Sanal dizi veri diskinizin yanı sıra işletim sistemi diskinin disk kullanımını izlemek için izlemeyi yapılandırın. Hyper-V çalıştırıyorsa, sanallaştırma konaklarınızı izlemek için System Center Virtual Machine Manager (SCVMM) ve System Center Operations Manager birleşimini kullanabilirsiniz.
* Belirli kullanım düzeylerinde uyarı göndermek için sanal dizinizdeki e-posta bildirimlerini yapılandırın.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Dizin arama ve virüs tarama uygulamaları
StorSimple Sanal dizisi, verileri yerel katmandan Microsoft Azure buluta otomatik olarak katman halinde açabilir. StorSimple üzerinde depolanan verileri taramak için Dizin arama veya virüs taraması gibi bir uygulama kullanıldığında, bulut verilerinin erişilmediğinden ve yerel katmana geri çekmediğinden emin olmanız gerekir.

Sanal dizinizdeki Dizin aramasını veya virüs taramasını yapılandırırken aşağıdaki en iyi yöntemleri uygulamanızı öneririz:

* Otomatik olarak yapılandırılmış tüm tarama işlemlerini devre dışı bırakın.
* Katmanlı birimlerde, bir artımlı tarama gerçekleştirmek için Dizin arama veya virüs tarama uygulamasını yapılandırın. Bu, yalnızca yerel katmanda büyük olasılıkla bulunan yeni verileri tarar. Artımlı bir işlem sırasında buluta katmanlı verilere erişilemez.
* Doğru arama filtrelerinin ve ayarlarının yalnızca istenen dosya türlerinin taranmasını sağlamak için yapılandırıldığından emin olun. Örneğin, görüntü dosyaları (JPEG, GIF ve TIFF) ve mühendislik çizimleri, artımlı veya tam dizin yeniden oluşturma sırasında taranmamalıdır.

Windows Dizin oluşturma işlemini kullanıyorsanız, aşağıdaki yönergeleri izleyin:

* Dizinin sık yeniden oluşturulması gerekiyorsa, katmanlı birimler için Windows Dizin oluşturucuyu, buluttan büyük miktarlarda veri (TBs) geri çeker. Dizinin yeniden oluşturulması, içeriklerinin dizinini oluşturmak için tüm dosya türlerini alır.
* Yerel olarak sabitlenmiş birimler için Windows Dizin oluşturma işlemini kullanın. Bu, dizini oluşturmak için yalnızca yerel katmanlardaki verilere erişir (bulut verilerine erişilmeyecektir).

### <a name="byte-range-locking"></a>Bayt aralığı kilitleme
Uygulamalar, dosyalar içindeki belirli bir bayt aralığını kilitleyebilir. StorSimple uygulamanıza yazılan uygulamalarda bayt aralığı kilitleme etkinleştirilirse, katmanlama sanal diziniz üzerinde çalışmaz. Katmanlama işinin çalışması için, erişilen dosyaların tüm alanlarının kilidinin açılması gerekir. Sanal dizinizdeki katmanlı birimlerde bayt aralığı kilitleme desteklenmez.

Bayt aralığı kilitlemeyi hafiflemede önerilen ölçüler şunları içerir:

* Uygulama mantığınızdaki bayt aralığı kilitlemeyi devre dışı bırakın.
* Bu uygulamayla ilişkili veriler için yerel olarak sabitlenmiş birimleri (katmanlı yerine) kullanın. Yerel olarak sabitlenmiş birimler buluta katman içermez.
* Bayt aralığı kilitleme etkinken yerel olarak sabitlenmiş birimler kullanılırken, geri yükleme tamamlanmadan önce birim çevrimiçi olabilir. Bu örneklerde geri yüklemenin tamamlanmasını beklemeniz gerekir.

## <a name="multiple-arrays"></a>Birden çok dizi
Birden çok sanal dizi, buluta taşılabilecek, daha sonra cihazın performansını etkileyecek şekilde, büyümekte olan bir veri kümesinin hesaba dağıtılması gerekebilir. Bu örneklerde, çalışma kümesi büyüdükçe cihazları ölçeklendirmek en iyisidir. Bu, şirket içi veri merkezinde bir veya daha fazla cihazın eklenmesini gerektirir. Cihazları eklerken şunları yapabilirsiniz:

* Geçerli veri kümesini Böl.
* Yeni cihaza yeni iş yükleri dağıtın.
* Birden çok sanal dizi dağıtıyorsanız, Yük Dengeleme perspektifinden diziyi farklı hiper yönetici konaklarına dağıtmanız önerilir.
* Birden çok sanal dizi (dosya sunucusu veya Iscsı sunucusu olarak yapılandırıldığında) bir Dağıtılmış Dosya Sistemi ad alanına dağıtılabilir. Ayrıntılı adımlar için [karma bulut depolama dağıtım kılavuzu ile dağıtılmış dosya sistemi ad alanı çözümüne](https://www.microsoft.com/download/details.aspx?id=45507)gidin. Dağıtılmış Dosya Sistemi çoğaltma şu anda sanal dizi ile kullanım için önerilmez. 

## <a name="see-also"></a>Ayrıca bkz.
StorSimple [sanal dizinizi](storsimple-virtual-array-manager-service-administration.md) StorSimple Yöneticisi hizmeti aracılığıyla yönetmeyi öğrenin.

