---
title: Azure geçişi 'nde değerlendirme ve bağımlılık görselleştirmesine sorun giderme
description: Azure geçişi 'nde değerlendirme ve bağımlılık görselleştirmesiyle ilgili yardım alın.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 78e54543c94cd6e8434023b61516242c2491f353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863607"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Değerlendirme/bağımlılık görselleştirmesi sorunlarını giderme

Bu makale, [Azure geçişi: bulma ve değerlendirme](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)ile değerlendirme ve bağımlılık görselleştirmesiyle ilgili sorunları gidermenize yardımcı olur.


## <a name="assessment-readiness-issues"></a>Değerlendirme hazırlığı sorunları

Değerlendirme hazırlık sorunlarını aşağıdaki şekilde giderin:

**Sorun** | **Düzeltme**
--- | ---
Desteklenmeyen önyükleme türü | Azure, EFı önyükleme türü olan VM 'Leri desteklemez. Geçiş çalıştırmadan önce önyükleme türünü BIOS 'a dönüştürmeniz önerilir. <br/><br/>Bu sanal makinelerin geçişini yönetmek için Azure geçişi sunucu geçişini kullanabilirsiniz. Geçiş sırasında VM 'nin önyükleme türünü BIOS 'a dönüştürür.
Koşullu olarak desteklenen Windows işletim sistemi | İşletim sistemi destek son tarihini geçti ve [Azure 'da destek](/troubleshoot/azure/virtual-machines/server-software-support)için özel bir destek SÖZLEŞMESINE (CSA) ihtiyaç duyuyor. Azure 'a geçiş yapmadan önce yükseltmeyi göz önünde bulundurun. Azure 'a geçiş için [Windows Server 2003 çalıştıran sunucuları hazırlama](prepare-windows-server-2003-migration.md) hakkındaki bilgileri gözden geçirin.
Desteklenmeyen Windows işletim sistemi | Azure yalnızca [Seçili Windows işletim sistemi sürümlerini](/troubleshoot/azure/virtual-machines/server-software-support)destekler. Azure 'a geçiş yapmadan önce sunucuyu yükseltmeyi düşünün.
Koşullu olarak onaylama Linux işletim sistemi | Azure yalnızca [Seçili Linux işletim sistemleri sürümlerini](../virtual-machines/linux/endorsed-distros.md)onaylar. Azure 'a geçiş yapmadan önce sunucuyu yükseltmeyi düşünün. Ayrıca daha fazla ayrıntı için [buraya](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment) başvurun.
Hazırlanmamış Linux işletim sistemi | Sunucu Azure 'da başlayabilir, ancak Azure işletim sistemi desteği sağlamaz. Azure 'a geçiş yapmadan önce, [onaylı bir Linux sürümüne](../virtual-machines/linux/endorsed-distros.md) yükseltmeyi düşünün.
Bilinmeyen işletim sistemi | VM 'nin işletim sistemi vCenter Server içinde "Other" olarak belirtilmiştir. Bu davranış, Azure geçişi 'nin VM 'nin Azure 'un hazır olduğunu doğrulamasını engeller. Sunucuyu geçirmeden önce işletim sisteminin Azure tarafından [desteklendiğinden](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements) emin olun.
Desteklenmeyen bit sürümü | 32 bitlik bir işletim sistemi olan VM 'Ler Azure 'da önbaşlatılabilir, ancak Azure 'a geçmeden önce 64 bit 'e yükseltmeniz önerilir.
Microsoft Visual Studio aboneliği gerektirir | Sunucu, yalnızca bir Visual Studio aboneliği aracılığıyla desteklenen bir Windows istemci işletim sistemi çalıştırıyor.
Gerekli depolama performansı için VM bulunamadı | Sunucu için gerekli olan depolama performansı (saniye başına giriş/çıkış işlemi [ıOPS] ve üretilen iş), Azure VM desteğini aşıyor. Geçişten önce sunucunun depolama gereksinimlerini azaltın.
Gerekli ağ performansı için VM bulunamadı | Sunucu için gerekli olan ağ performansı (ın/out), Azure VM desteğini aşıyor. Sunucu için ağ gereksinimlerini azaltın.
Belirtilen konumda VM bulunamadı | Geçişten önce farklı bir hedef konum kullanın.
Bir veya daha fazla uygun olmayan disk | VM 'ye bağlı bir veya daha fazla disk, Azure gereksinimlerini karşılamıyor. A<br/><br/> Azure geçişi: bulma ve değerlendirme Şu anda Ultra SSD diskleri desteklememektedir ve Premium yönetilen diskler (32 TB) için disk sınırlarına göre diskleri değerlendirir.<br/><br/> SANAL makineye bağlı her disk için, diskin boyutunun < 64 TB (Ultra SSD diskler tarafından desteklenir) olduğundan emin olun.<br/><br/> Değilse, Azure 'a geçmeden önce disk boyutunu azaltın veya Azure 'da birden çok disk kullanın ve daha yüksek depolama sınırları almak için [bunları birlikte toplayın](../virtual-machines/premium-storage-performance.md#disk-striping) . Her disk için gereken performansın (ıOPS ve aktarım hızı) Azure [yönetilen sanal makine diskleri](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)tarafından desteklendiğinden emin olun.
Bir veya daha fazla uygun olmayan ağ bağdaştırıcısı. | Kullanılmayan ağ bağdaştırıcılarını geçişten önce sunucudan kaldırın.
Disk sayısı sınırı aşıyor | Kullanılmayan diskleri geçişten önce sunucudan kaldırın.
Disk boyutu sınırı aşıyor | Azure geçişi: bulma ve değerlendirme Şu anda Ultra SSD diskleri desteklemiyor ve değerlendirir diskler, Premium disk sınırlarına (32 TB) dayalı olarak diskler.<br/><br/> Ancak Azure, 64 TB 'a kadar olan diskleri destekler (Ultra SSD diskleri tarafından desteklenir). Geçiş işleminden önce diskleri 64 TB 'den daha az bir değere küçültün veya Azure 'da birden çok disk kullanın ve daha yüksek depolama sınırları almak için [bunları birlikte toplayın](../virtual-machines/premium-storage-performance.md#disk-striping) .
Belirtilen konumda disk yok | Geçirmeden önce diskin hedef konumunda olduğundan emin olun.
Belirtilen artıklık için disk kullanılamıyor | Disk, değerlendirme ayarlarında tanımlanan artıklık depolama türünü kullanmalıdır (varsayılan olarak LRS).
Bir iç hata nedeniyle disk uygunluğu belirlenemedi | Grup için yeni bir değerlendirme oluşturmayı deneyin.
Gerekli çekirdek ve bellek içeren VM bulunamadı | Azure uygun bir VM türü bulamadı. Geçirmeden önce şirket içi sunucunun bellek ve çekirdek sayısını azaltın.
Bir iç hata nedeniyle VM uygunluğu belirlenemedi | Grup için yeni bir değerlendirme oluşturmayı deneyin.
Bir iç hata nedeniyle bir veya daha fazla disk için uygunluğu belirlenemedi | Grup için yeni bir değerlendirme oluşturmayı deneyin.
Bir iç hata nedeniyle bir veya daha fazla ağ bağdaştırıcısı için uygunluğu belirlenemedi | Grup için yeni bir değerlendirme oluşturmayı deneyin.
Teklif para birimi ayrılmış örneği için VM boyutu bulunamadı | Seçili RI, teklif ve para birimi birleşimi için VM boyutu bulunamadığı için sunucu uygun değil olarak işaretlendi. Geçerli birleşimleri seçmek ve değerlendirmeyi yeniden hesaplamak için değerlendirme özelliklerini düzenleyin. 
Koşullu olarak Ready Internet Protokolü | Yalnızca Azure VMware Çözüm (AVS) değerlendirmelerinde geçerlidir. AVS, IPv6 Internet adresi faktörünü desteklemez. Sunucunuz IPv6 ile algılanıyorsa düzeltme kılavuzu için AVS ekibine başvurun.

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>İçeri aktarma temelli AVS değerlendirmesi bilinmeyen olarak işaretlenmiş olan önerilen geçiş aracı

Bir CSV dosyası aracılığıyla içeri aktarılan sunucular için, ve AVS değerlendirmesi için varsayılan geçiş aracı bilinmez. Ancak, VMware ortamındaki sunucular için, VMware karma bulut uzantısı (HCX) çözümünün kullanılması önerilir. [Daha Fazla Bilgi Edinin](../azure-vmware/tutorial-deploy-vmware-hcx.md).

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Linux VM 'Leri, bir Azure VM değerlendirmesinde "koşullu olarak hazırlanıyor"

VMware ve Hyper-V VM 'lerinde, Azure VM değerlendirmesi, bilinen bir boşluk nedeniyle Linux sanal makinelerini "koşullu olarak hazırlanıyor" olarak işaretler. 

- Bu Aralık, BT 'nin şirket içi VM 'lerde yüklü Linux IŞLETIM sisteminin ikincil sürümünü algılamasını önler.
- Örneğin, RHEL 6,10 için şu anda Azure VM değerlendirmesi, işletim sistemi sürümü olarak yalnızca RHEL 6 ' ı algılar. Bunun nedeni, Hyper-V ana bilgisayarının vCenter Server ar, Linux VM işletim sistemleri için çekirdek sürümü sağlamasunmamalıdır.
-  Azure yalnızca Linux 'un belirli sürümlerini sağladığından, Linux VM 'Leri Şu anda Azure VM değerlendirmesi için koşullu olarak kullanılabilir olarak işaretlendi.
- [Azure Linux desteğini](../virtual-machines/linux/endorsed-distros.md)inceleyerek, ŞIRKET içi VM 'de çalışan Linux Işletim sisteminin Azure 'da olup olmadığını belirleyebilirsiniz.
-  Doğrulanan dağıtımı doğruladıktan sonra bu uyarıyı yoksayabilirsiniz.

Bu boşluk, VMware VM 'lerinde [uygulama bulma](./how-to-discover-applications.md) etkinleştirilerek çözülebilir. Azure VM değerlendirmesi, belirtilen konuk kimlik bilgilerini kullanarak VM 'de algılanan işletim sistemini kullanır. Bu işletim sistemi verileri, hem Windows hem de Linux VM 'lerinde doğru IŞLETIM sistemi bilgilerini tanımlar.

## <a name="operating-system-version-not-available"></a>İşletim sistemi sürümü kullanılamıyor

Fiziksel sunucular için işletim sistemi ikincil sürüm bilgileri kullanılabilir olmalıdır. Kullanılamıyorsa Microsoft Desteği başvurun. VMware ortamındaki sunucular için Azure geçişi, vCenter Server ' de VM için belirtilen işletim sistemi bilgilerini kullanır. Ancak, vCenter Server işletim sistemleri için ikincil sürüm sağlamaz. İkincil sürümü bulmak için, [uygulama bulmayı](./how-to-discover-applications.md)ayarlamanız gerekir. Hyper-V VM 'Leri için işletim sistemi ikincil sürüm keşfi desteklenmez. 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure VM değerlendirmesinde Azure SKU 'Ları Şirket içinden daha büyük

Azure VM değerlendirmesi, değerlendirme türüne göre geçerli şirket içi ayırmadan daha fazla çekirdeğe ve belleğe sahip Azure VM SKU 'Larını önerebilir:

- VM SKU 'SU önerisi, değerlendirme özelliklerine bağlıdır.
- Bu, Azure VM değerlendirmesi: *performans tabanlı* veya *Şirket içi olarak* gerçekleştirdiğiniz değerlendirme türünden etkilenir.
- Performans tabanlı değerlendirmelere yönelik Azure VM değerlendirmesi, şirket içi sanal makinelerinize yönelik doğru hedef VM SKU 'sunu belirlemede şirket içi VM 'lerin (CPU, bellek, disk ve ağ kullanımı) kullanım verilerini dikkate alır. Ayrıca etkili kullanımı saptarken de bir konfor katsayısı ekler.
- Şirket içi boyutlandırma için performans verileri göz önünde bulundurulmaz ve hedef SKU, şirket içi ayırmaya göre önerilir.

Bunun önerileri nasıl etkileyebileceğini göstermek için bir örnek alalım:

Dört çekirdekli ve sekiz GB bellek içeren, %50 CPU kullanımı ve %50 bellek kullanımı ve belirli bir rahatlık 1,3 faktörü içeren bir şirket içi VM sunuyoruz.

-  Değerlendirme **Şirket içinde** ise, dört çekirdekli ve 8 GB bellek Içeren BIR Azure VM SKU 'su önerilir.
- Değerlendirme, etkin CPU ve bellek kullanımı 50 (4 çekirdek * 1,3 = 2,6 çekirdekler 50 ve 8 GB bellek * 1,3 = 5,3-GB bellek) temel alınarak, dört çekirdekli sanal makine SKU 'SU (en yakın desteklenen çekirdek sayısı) ve sekiz GB bellek (en yakın desteklenen bellek boyutu) önerilir.
- Değerlendirme boyutlandırma hakkında [daha fazla bilgi edinin](concepts-assessment-calculation.md#types-of-assessments) .

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure VM değerlendirmesinde önerilen Azure disk SKU 'Ları neden Şirket içinden daha büyük?

Azure VM değerlendirmesi, değerlendirme türüne göre daha büyük bir disk önerebilir.
- Disk boyutlandırma iki değerlendirme özelliklerine bağlıdır: boyutlandırma ölçütleri ve depolama türü.
- Boyutlandırma ölçütü **performans tabanlıdır** ve depolama türü **Otomatik**, IOPS ve aktarım hızı değerleri, hedef disk türü (Standart HDD, standart SSD veya Premium) tanımlanırken kabul edilir. Daha sonra disk türünden bir disk SKU 'SU önerilir ve öneri, şirket içi diskin boyut gereksinimlerini dikkate alır.
- Boyutlandırma ölçütü **performans tabanlıdır** ve depolama türü **Premium** ise, şirket içi diskin IOPS, verimlilik ve Boyut gereksinimlerine bağlı olarak Azure 'DA BIR Premium disk SKU 'su önerilir. Aynı mantık, boyutlandırma ölçütleri **Şirket içinde** olduğunda ve depolama türü **Standart HDD**, **Standart SSD** veya **Premium** olduğunda disk boyutlandırmayı gerçekleştirmek için kullanılır.

Örnek olarak, 32 GB bellek içeren bir şirket içi diskiniz varsa, ancak diskin toplanmış okuma ve yazma ıOPS değeri 800 ıOPS ise, Azure VM değerlendirmesi bir Premium disk önerir (daha yüksek ıOPS gereksinimleri nedeniyle) ve ardından gerekli ıOPS ve boyutu destekleyebilen bir disk SKU 'SU önerir. Bu örnekteki en yakın eşleşme P15 (256 GB, 1100 IOPS) olabilir. Şirket içi diskin gerektirdiği boyut 32 GB olsa da, Azure VM değerlendirmesi şirket içi diskin yüksek ıOPS gereksinimi nedeniyle daha büyük bir disk önerir.

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>Değerlendirme raporumda bazı/tüm VM'lerin performans verileri neden eksik?

"Performans tabanlı" değerlendirmede Azure Geçişi aleti şirket içi VM’ler için performans verilerini toplayamazsa değerlendirme raporu dışarı aktarmasında 'PercentageOfCoresUtilizedMissing' veya 'PercentageOfMemoryUtilizedMissing' hatası verir. Lütfen şunu denetleyin:

- Değerlendirmeyi oluşturduğunuz süre boyunca VM'lerin açılıp açılmadığı
- Yalnızca bellek sayaçları eksikse ve Hyper-V sanal makinelerini değerlendirmeye çalışıyorsanız, bu sanal makinelerde dinamik belleğin etkinleştirilip etkinleştirilmediğini denetleyin. Bu bilinen bir sorundur ve şu anda Azure Geçişi aletinin bu tür VM'ler için bellek kullanımı bilgilerini toplayamamasından kaynaklanır.
- Tüm performans sayaçları eksikse, değerlendirme için bağlantı noktası erişim gereksinimlerinin karşılandığından emin olun. [VMware](./migrate-support-matrix-vmware.md#port-access-requirements), [Hyper-V](./migrate-support-matrix-hyper-v.md#port-access) ve [fiziksel](./migrate-support-matrix-physical.md#port-access) değerlendirme için bağlantı noktası erişim gereksinimleri hakkında daha fazla bilgi edinin.
Note-performans sayaçlarından herhangi biri eksikse, Azure geçişi: bulma ve değerlendirme, şirket içinde ayrılmış çekirdeğe/belleğe geri döner ve bir VM boyutunu uygun şekilde önerir.

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Azure VM 'mdeki ve/veya AVS değerlendirmesi raporundaki bazı/tüm sunucular için performans verileri neden eksik?

"Performans tabanlı" değerlendirme için, Azure geçiş gereci şirket içi sunucular için performans verilerini toplayamazsa, değerlendirme raporu dışarı aktarma ' PercentageOfCoresUtilizedMissing ' veya ' PercentageOfMemoryUtilizedMissing ' diyor. Lütfen şunu denetleyin:

- Değerlendirme oluşturduğunuz süre boyunca sunucular açık ise
- Yalnızca bellek sayaçları eksikse ve Hyper-V ortamındaki sunucuları değerlendirmeye çalışıyorsanız. Bu senaryoda, lütfen sunucularda dinamik belleği etkinleştirin ve en son değişiklikleri yansıtacak şekilde değerlendirmesi ' yeniden hesaplayın '. Gereç, Hyper-V ortamındaki yarı sunucular için bellek kullanım değerlerini yalnızca sunucuda dinamik bellek etkinken toplayabilir.

- Tüm performans sayaçları eksikse, 443 (HTTPS) bağlantı noktalarında giden bağlantılara izin verildiğinden emin olun.

    > [!Note]
    > Performans sayaçlarından herhangi biri eksikse, Azure geçişi: bulma ve değerlendirme, şirket içi çekirdekler/belleğe geri döner ve buna uygun bir VM boyutu önerir.


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Azure SQL değerlendirmem içindeki bazı/tüm SQL örnekleri/veritabanları için performans verileri neden eksik?

Performans verilerinin toplandığından emin olmak için lütfen şunu denetleyin:

- Değerlendirme oluşturduğunuz süre boyunca SQL Server 'lar açık ise
- Azure geçişi 'nde SQL aracısının bağlantı durumu ' Connected ' ise ve son sinyali kontrol ederseniz 
- Azure geçişi 'nin tüm SQL örnekleri için bağlantı durumu, bulunan SQL örneği dikey penceresinde ' bağlı ' ise
- Performans sayaçlarının hepsi eksikse 443 numaralı bağlantı noktası üzerinden giden bağlantılara (HTTPS) izin verildiğinden emin olun

Performans sayaçlarından herhangi biri eksikse, Azure SQL değerlendirmesi Bu örnek/veritabanı için en küçük Azure SQL yapılandırmasını önerir.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Değerlendirmemin güvenilirlik derecesi neden düşük?

"Performans tabanlı" değerlendirmeler için güvenilirlik derecesi, değerlendirmeyi hesaplarken gereken [kullanılabilir veri noktaları](./concepts-assessment-calculation.md#ratings) yüzdesi temelinde hesaplanır. Değerlendirme güvenilirlik derecesinin düşük olmasının nedenlerini aşağıda bulunabilirsiniz:

- Değerlendirmeyi oluşturduğunuz süre boyunca ortamınızın profilini oluşturmadınız. Örneğin değerlendirmeyi bir hafta olarak ayarlanmış performans süresiyle oluşturuyorsanız, toplanacak tüm veri noktalarının keşfini başlattıktan sonra en az bir hafta beklemeniz gerekir. Süre bekleyemez, lütfen performans süresini daha küçük bir süre değiştirin ve değerlendirmeyi **yeniden hesaplayın** .
 
- Değerlendirme, değerlendirme süresi içinde sunucuların bir bölümü veya tümü için performans verilerini toplayamıyor. Yüksek güvenilirlikli bir derecelendirme için lütfen şunları doğrulayın: 
    - Değerlendirme süresi boyunca sunucular açık
    - 443 bağlantı noktalarında giden bağlantılara izin verilir
    - Hyper-V sunucuları için dinamik bellek etkin 
    - Azure geçişi 'ndeki aracıların bağlantı durumu ' Connected ' ve son sinyali denetle
    - Azure SQL değerlendirmelerinde, tüm SQL örnekleri için Azure geçişi bağlantı durumu bulunan SQL örneği dikey penceresinde "bağlandı" olarak ayarlanır

    Güvenilirlik derecelendirmesindeki en son değişiklikleri yansıtacak şekilde değerlendirmeyi **Yeniden Hesaplayın**.

- Azure VM ve AVS değerlendirmelerinde, bulma başlatıldıktan sonra birkaç sunucu oluşturulmuştur. Örneğin, son bir ayın performans geçmişi için bir değerlendirme oluşturuyorsanız ancak ortamda yalnızca bir hafta önce birkaç sunucu oluşturuluyorsa. Bu durumda, yeni sunucular için performans verileri sürenin tamamına uygun olmayacaktır ve güvenirlik derecelendirmesi düşük olacaktır. [Daha fazla bilgi edinin](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- Azure SQL değerlendirmelerinde, bulma başlatıldıktan sonra birkaç SQL örneği veya veritabanı oluşturuldu. Örneğin, son bir ayın performans geçmişi için bir değerlendirme oluşturuyorsanız ancak ortamda yalnızca bir hafta önce birkaç SQL örneği veya veritabanı oluşturuluyorsa. Bu durumda, yeni sunucular için performans verileri sürenin tamamına uygun olmayacaktır ve güvenirlik derecelendirmesi düşük olacaktır. [Daha fazla bilgi edinin](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>İşletim sistemi lisansı bir Azure VM değerlendirmesine dahil midir?

Azure VM değerlendirmesi Şu anda yalnızca Windows sunucuları için işletim sistemi lisans maliyetini dikkate alır. Linux sunucuları için lisans maliyetleri Şu anda dikkate alınmamaktadır.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Performans tabanlı boyutlandırma bir Azure VM değerlendirmesi ile nasıl çalışır?

Azure VM değerlendirmesi, şirket içi sunucuların performans verilerini sürekli olarak toplar ve Azure 'da VM SKU ve disk SKU 'sunu önermek için kullanır. Performans tabanlı verilerin nasıl toplandığını [öğrenin](concepts-assessment-calculation.md#calculate-sizing-performance-based) .

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>Değerlendirmem neden ayrılmış örneklerin, VM çalışma süresinin ve Iskontonun (%) bir birleşimi ile oluşturulduğunu belirten bir uyarı gösteriyor?
' Ayrılmış örnekler ' seçtiğinizde, ' Discount (%) ' ve ' VM çalışma süresi ' özellikleri geçerli değil. Değerlendirmenizi bu özelliklerin geçersiz bir birleşimiyle oluşturulduğundan, Düzenle ve yeniden hesapla düğmeleri devre dışı bırakılır. Lütfen yeni bir değerlendirme oluşturun. [Daha fazla bilgi edinin](./concepts-assessment-calculation.md#whats-an-assessment).

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Fiziksel sunucularım üzerinde bazı ağ bağdaştırıcılarının performans verilerini görmüyorum

Fiziksel sunucuda Hyper-V Sanallaştırması etkinleştirilmişse bu durum oluşabilir. Bu sunucularda, bir ürün boşluğu nedeniyle Azure geçişi Şu anda hem fiziksel hem de sanal ağ bağdaştırıcılarını bulur. Ağ aktarım hızı yalnızca bulunan sanal ağ bağdaştırıcılarında yakalanır.

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>Fiziksel sunucum için önerilen Azure VM SKU 'SU büyük

Fiziksel sunucuda Hyper-V Sanallaştırması etkinleştirilmişse bu durum oluşabilir. Azure geçişi Şu anda bu sunucularda fiziksel ve sanal ağ bağdaştırıcılarını tespit eder. Bu nedenle, hayır. bulunan ağ bağdaştırıcılarının sayısı, gerçek değerden daha yüksek. Azure VM değerlendirmesi, gereken sayıda ağ bağdaştırıcısını destekleyebilen bir Azure VM 'si seçer; Bu, büyük olasılıkla büyük olasılıkla VM 'nin oluşmasına neden olabilir. Hayır 'ın etkisi hakkında [daha fazla bilgi edinin](./concepts-assessment-calculation.md#calculating-sizing) . ağ bağdaştırıcılarının boyutunu boyutlandırmadır. Bu, ileride gönderilecek bir ürün eksikmidir.

## <a name="readiness-category-not-ready-for-my-physical-server"></a>Fiziksel sunucum için hazırlık kategorisi "hazır değil"

Hyper-V Sanallaştırması etkin olan bir fiziksel sunucu söz konusu olduğunda, hazırlık kategorisi yanlış bir şekilde "hazır değil" olarak işaretlenmiş olabilir. Bu sunucularda, bir ürün boşluğu nedeniyle Azure geçişi Şu anda fiziksel ve Sanal bağdaştırıcıları bulur. Bu nedenle, hayır. bulunan ağ bağdaştırıcılarının sayısı, gerçek değerden daha yüksek. Hem şirket içinde hem de performans tabanlı değerlendirmelerinde, Azure VM değerlendirmesi gereken sayıda ağ bağdaştırıcısını destekleyebilen bir Azure VM 'si seçer. Ağ bağdaştırıcılarının sayısının 32 ' den yüksek olduğu tespit edildiğinde, en fazla No. Azure VM 'lerde desteklenen NIC 'Ler, sunucu "Ready" olarak işaretlenir.  Hayır 'ın etkisi hakkında [daha fazla bilgi edinin](./concepts-assessment-calculation.md#calculating-sizing) . , boyutlandırmanın bulunduğu NIC 'ler.


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>Fiziksel sunucular için gerçek sayıdan yüksek bulunan NIC sayısı

Fiziksel sunucuda Hyper-V Sanallaştırması etkinleştirilmişse bu durum oluşabilir. Azure geçişi Şu anda bu sunucularda fiziksel ve Sanal bağdaştırıcıları tespit eder. Bu nedenle, hayır. bulunan NIC 'ler gerçek değerden daha yüksek.

## <a name="dependency-visualization-in-azure-government"></a>Azure Kamu 'da bağımlılık görselleştirmesi

Aracı tabanlı bağımlılık analizi, Azure Kamu 'da desteklenmez. Lütfen aracısız bağımlılık analizini kullanın.


## <a name="dependencies-dont-show-after-agent-install"></a>Bağımlılıklar, aracı yüklendikten sonra gösterilmez

Şirket içi VM 'Lere bağımlılık görselleştirme aracılarını yükledikten sonra, Azure geçişi genellikle portalda bağımlılıkları göstermek için 15-30 dakika sürer. 30 dakikadan uzun süre beklemişseniz, Microsoft Monitoring Agent (MMA) Log Analytics çalışma alanına bağlanabildiğinizden emin olun.

Windows VM 'Leri için:
1. Denetim Masası 'nda MMA 'yı başlatın.
2.   >  **Azure Log Analytics (OMS)** Microsoft Monitoring Agent özelliklerinde, çalışma alanının **durumunun** yeşil olduğundan emin olun.
3. Durum yeşil değilse, çalışma alanını kaldırmayı ve onu yeniden eklemeyi deneyin.

    ![MMA durumu](./media/troubleshoot-assessment/mma-properties.png)

Linux sanal makineleri için, MMA ve bağımlılık aracısının yükleme komutlarının başarılı olduğundan emin olun. Daha fazla sorun giderme kılavuzuna [bakın.](../azure-monitor/vm/service-map.md#post-installation-issues)

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

- **MMS Aracısı**: desteklenen [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)ve [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems) işletim sistemlerini gözden geçirin.
- **Bağımlılık Aracısı**: desteklenen [Windows ve Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) işletim sistemleri.

## <a name="visualize-dependencies-for--1-hour"></a>> 1 saat için bağımlılıkları görselleştirin

Aracısız bağımlılık analizi sayesinde, bağımlılıkları görselleştirin veya 30 güne kadar bir süre için bir haritada dışarı aktarabilirsiniz.

Aracı tabanlı bağımlılık analizine sahip olan Azure geçişi, son ayın belirli bir tarihine geri dönmenize rağmen, bağımlılıkları görselleştirebileceğiniz en uzun süre bir saattir. Örneğin, dün bağımlılıklarını görüntülemek için bağımlılık eşlemesindeki süre süresi işlevini kullanabilirsiniz, ancak bunları yalnızca bir saatlik dönem için görüntüleyebilirsiniz. Ancak, Azure Izleyici günlüklerini daha uzun bir süre boyunca [bağımlılık verilerini sorgulamak](./how-to-create-group-machine-dependencies.md) için kullanabilirsiniz.

## <a name="visualized-dependencies-for--10-servers"></a>> 10 sunucuları için görselleştirilen bağımlılıklar

Azure geçişi 'nde, aracı tabanlı bağımlılık analizi ile, en fazla 10 VM içeren [gruplar için bağımlılıkları görselleştirebilirsiniz](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) . Daha büyük gruplar için, bağımlılıkları görselleştirmek üzere VM 'Leri daha küçük gruplara bölmeniz önerilir.


## <a name="servers-show-install-agent"></a>Sunucular "yüklemeyi aracıyı" gösterir

Bağımlılık görselleştirmesi etkin olan sunucuları Azure 'a geçirdikten sonra, sunucular aşağıdaki davranış nedeniyle "bağımlılıkları görüntüle" yerine "aracı yüklemeyi" eylemi gösterebilir:

- Azure 'a geçişten sonra şirket içi sunucular kapalıdır ve eşdeğer VM 'Ler Azure 'da kapalıdır. Bu sunucular farklı bir MAC adresi elde.
- Sunucular, şirket içi IP adresini tutdum ya da değil, aynı zamanda farklı bir IP adresine sahip olabilir.
- MAC ve IP adreslerinin her ikisi de Şirket içinden farklıysa Azure geçişi, şirket içi sunucuları Hizmet Eşlemesi bağımlılık verileriyle ilişkilendirmez. Bu durumda, bağımlılıkları görüntülemek yerine aracıyı yüklemek için seçeneği görüntülenir.
- Azure 'a bir test geçişten sonra şirket içi sunucular beklendiği gibi açık kalır. Azure 'da eşdeğer sunucular, farklı bir MAC adresi elde edin ve farklı IP adresleri alabilir. Giden Azure Izleyici günlük trafiğini bu sunuculardan engellemeniz gerekmedikçe Azure geçişi, şirket içi sunucuları Hizmet Eşlemesi bağımlılığı verileriyle ilişkilendirmez ve bu nedenle, bağımlılıkları görüntülemek yerine aracıları yüklemek için seçeneği gösterir.

## <a name="dependencies-export-csv-shows-unknown-process"></a>CSV dışarı aktarma bağımlılıkları "Bilinmeyen işlem" gösterir
Aracısız bağımlılık analizinde, işlem adları en iyi çaba temelinde yakalanır. Belirli senaryolarda, kaynak ve hedef sunucu adları ve hedef bağlantı noktası yakalansa da, bağımlılığın her iki ucunda işlem adlarını belirlenmesi uygun değildir. Bu gibi durumlarda, işlem "Bilinmeyen işlem" olarak işaretlenir.

## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-azure-migrate"></a>Log Analytics çalışma alanım, Azure geçişi 'nde çalışma alanı yapılandırmaya çalışırken listelenmez
Azure Geçişi şu anda OMS çalışma alanı oluşturulmasını Doğu ABD, Güneydoğu Asya ve Batı Avrupa bölgelerinde desteklemektedir. Çalışma alanı başka bir bölgede Azure geçişi dışında oluşturulduysa, şu anda bir projeyle ilişkilendirilemez.


## <a name="capture-network-traffic"></a>Ağ trafiğini yakala

Ağ trafiği günlüklerini aşağıda gösterildiği gibi toplayın:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Geliştirici Araçları başlamak için F12 tuşuna basın. Gerekirse,  **Gezinti ayarında girişleri temizle** ' yi temizleyin.
3. **Ağ** sekmesini seçin ve ağ trafiğini yakalamaya başlayın:
   - Chrome 'da **günlüğü koru**' yı seçin. Kayıt otomatik olarak başlamalıdır. Kırmızı bir daire trafiğin yakalandığını gösterir. Kırmızı daire görünmezse başlamak için siyah daireyi seçin.
   - Microsoft Edge ve Internet Explorer 'da kayıt otomatik olarak başlamalıdır. Aksi takdirde, yeşil oynatma düğmesini seçin.
4. Hatayı yeniden oluşturmaya çalışın.
5. Kayıt sırasında hata ile karşılaşduktan sonra kaydı durdurun ve kayıtlı etkinliğin bir kopyasını kaydedin:
   - Chrome 'da, sağ tıklayın ve **IÇERIKLE har olarak kaydet**' i seçin. Bu eylem, günlükleri bir. har dosyası olarak sıkıştırır ve dışarı aktarır.
   - Microsoft Edge veya Internet Explorer 'da **yakalanan trafiği dışarı aktar** seçeneğini belirleyin. Bu eylem günlüğü sıkıştırır ve dışarı aktarır.
6. Herhangi bir uyarı veya hata olup olmadığını denetlemek için **konsol** sekmesini seçin. Konsol günlüğünü kaydetmek için:
   - Chrome 'da konsol günlüğünde herhangi bir yere sağ tıklayın. Günlüğe **Kaydet**, dışarı aktarmak için ve günlüğü posta ' yı seçin.
   - Microsoft Edge veya Internet Explorer 'da hatalara sağ tıklayıp **Tümünü Kopyala**' yı seçin.
7. Geliştirici Araçları kapatın.


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>Değerlendirmem içindeki işletim sistemi verileri nerede bulundu?

- VMware VM 'Leri için varsayılan olarak, vCenter tarafından belirtilen işletim sistemi verileri vardır. 
   - VMware Linux VM 'Leri için, uygulama bulma etkinse, işletim sistemi ayrıntıları Konuk VM 'den alınır. Değerlendirmede hangi IŞLETIM sistemi ayrıntılarının olduğunu denetlemek için, bulunan sunucular görünümüne ve fareyi "Işletim sistemi" sütunundaki değerin üzerine gidin. Açılan metinde, gördüğünüz işletim sistemi verilerinin vCenter Server 'dan mı yoksa VM kimlik bilgileri kullanılarak Konuk VM 'den mi toplandığını görebileceksiniz. 
   - Windows VM 'Leri için, işletim sistemi ayrıntıları her zaman vCenter Server getirilir.
- Hyper-V VM 'Leri için, işletim sistemi verileri Hyper-V konağından toplanır
- Fiziksel sunucular için, sunucusundan getirilir.

## <a name="next-steps"></a>Sonraki adımlar

Bir değerlendirme [oluşturun](how-to-create-assessment.md) veya [özelleştirin](how-to-modify-assessment.md) .