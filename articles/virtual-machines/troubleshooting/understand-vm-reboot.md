---
title: Azure VM için sistemin yeniden başlatılmasını anlama | Microsoft Docs
description: Bir VM 'nin yeniden başlatılmasına neden olabilecek olayları listeler
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: a536eb89f2040333617329e963ce00f5c6b1ba7a
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086974"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Azure VM için sistemin yeniden başlatılmasını anlama

Azure sanal makineleri (VM 'Ler) bazen, yeniden başlatma işlemini başlatmaktan herhangi bir kanıt olmadan, görünür bir nedenle yeniden başlatılabilir. Bu makalede, sanal makinelerin yeniden başlatılmasına neden olabilecek eylemler ve olaylar listelenmekte ve beklenmeyen yeniden başlatma sorunlarından kaçının veya bu sorunların etkilerini azaltabilirsiniz.

## <a name="configure-the-vms-for-high-availability"></a>Yüksek kullanılabilirlik için VM 'Leri yapılandırma

Azure 'da çalışan bir uygulamayı VM yeniden başlatmaları ve kapalı kalma süresine karşı korumanın en iyi yolu, VM 'Leri yüksek kullanılabilirlik için yapılandırmaktır.

Uygulamanızda bu artıklık düzeyini sağlamak için bir kullanılabilirlik kümesinde iki veya daha fazla VM 'yi gruplandırmalarını öneririz. Bu yapılandırma, planlı veya plansız bir bakım olayı sırasında en az bir VM 'nin kullanılabilmesini ve yüzde 99,95 [Azure SLA 'sını](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/)karşılamasını sağlar.

Kullanılabilirlik kümeleri hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [VM 'lerin kullanılabilirliğini yönetme](../windows/manage-availability.md)
- [VM 'lerin kullanılabilirliğini yapılandırma](../windows/classic/configure-availability.md)

## <a name="resource-health-information"></a>Kaynak Durumu bilgileri

Azure Kaynak Durumu, bireysel Azure kaynaklarının sistem durumunu ortaya çıkaran ve sorun giderme sorunları için eyleme dönüştürülebilir rehberlik sağlayan bir hizmettir. Sunuculara veya altyapı öğelerine doğrudan erişmek mümkün olmayan bir bulut ortamında, Kaynak Durumu amacı, sorun giderme sırasında harcadığınız süreyi azaltmaktır. Özellikle, bu, sorunun kökünün uygulamada mı yoksa Azure platformunun içindeki bir olayda mi olduğunu belirlemek için harcadığınız süreyi azaltmaktır. Daha fazla bilgi için bkz. [kaynak durumu anlama ve kullanma](../../resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>VM 'nin yeniden başlatılmasına neden olabilecek eylemler ve olaylar

### <a name="planned-maintenance"></a>Planlı bakım

Microsoft Azure, VM 'Leri kapsayan konak altyapısının güvenilirliğini, performansını ve güvenliğini geliştirmek için düzenli olarak dünya genelinde güncelleştirmeler yapar. Bellek koruma güncelleştirmeleri de dahil olmak üzere bu güncelleştirmelerin birçoğu, sanal makinelerinize veya bulut hizmetlerinize herhangi bir etki olmadan gerçekleştirilir.

Ancak bazı güncelleştirmeler için yeniden başlatma gerekir. Bu gibi durumlarda, altyapı düzeltme eki uygulanırken sanal makineler kapatılır ve sonra sanal makineler yeniden başlatılır.

Azure planlı bakımın ne olduğunu ve Linux sanal makinelerinizin kullanılabilirliğini nasıl etkileyebileceğini anlamak için burada listelenen makalelere bakın. Makaleler Azure planlı bakım işlemi ve etkiyi daha da azaltmak için planlı bakımın nasıl zamanlanacağı hakkında arka plan bilgileri sağlar.

- [Azure’da VM’ler için planlı bakım](../windows/planned-maintenance.md)
- [Azure sanal makinelerinde planlı bakımı zamanlama](../windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Bellek koruma güncelleştirmeleri

Microsoft Azure Bu güncelleştirme sınıfı için, kullanıcılar çalışan VM 'Leri üzerinde hiçbir etkisi olmaz. Bu güncelleştirmelerin çoğu, çalışan örneği engellemeden güncelleştirilebilecek bileşenlere veya hizmetler içindir. Bazıları, VM 'lerin yeniden başlatılması olmadan uygulanabilen ana bilgisayar işletim sistemindeki platform altyapı güncelleştirmeleridir.

Bu bellek koruma güncelleştirmeleri, yerinde dinamik geçiş olanağı sağlayan teknolojiyle gerçekleştirilir. Güncelleştirilebileceği zaman, sanal makine *duraklatılmış* duruma gelir. Bu durum RAM belleğini korur ve bu arada alttaki konak işletim sistemi gerekli güncelleştirmeleri ve düzeltme eklerini alır. VM duraklatıldıktan sonra 30 saniye içinde devam ettirilir. VM devam ettirildikten sonra saati otomatik olarak eşitlenir.

Kısa duraklama süresi nedeniyle, bu mekanizmadan güncelleştirme dağıtmak VM 'lerdeki etkiyi büyük ölçüde azaltır. Ancak, tüm güncelleştirmeler bu şekilde dağıtılamaz. 

Çok örnekli güncelleştirmeler (bir kullanılabilirlik kümesindeki VM'ler için), bir seferde bir güncelleme etki alanı şeklinde uygulanır.

> [!NOTE]
> Eski çekirdek sürümlerine sahip Linux makineler, bu güncelleştirme yöntemi sırasında bir çekirdek panik tarafından etkilendi. Bu sorundan kaçınmak için çekirdek sürümü 3.10.0-327.10.1 veya sonraki bir sürüme güncelleştirin. Daha fazla bilgi için bkz. [ana bilgisayar düğümü yükseltildikten sonra 3,10 tabanlı çekirdek bölmeleri üzerinde Azure Linux sanal makinesi](https://support.microsoft.com/help/3212236).

### <a name="user-initiated-reboot-or-shutdown-actions"></a>Kullanıcı tarafından başlatılan yeniden başlatma veya kapalı eylemler

Azure portal, Azure PowerShell, komut satırı arabiriminden veya API 'yi sıfırladıktan sonra, olayı [Azure etkinlik günlüğünde](../../azure-monitor/platform/activity-logs-overview.md)bulabilirsiniz.

Eylemi VM 'nin işletim sisteminden gerçekleştirirseniz, olayı sistem günlüklerinde bulabilirsiniz.

Genellikle VM 'nin yeniden başlatılmasına neden olan diğer senaryolar, birden çok yapılandırma değiştirme eylemi içerir. Genellikle, belirli bir eylemi yürütmenin VM 'nin yeniden başlatılmasının sonucuna işaret edecek bir uyarı iletisi görürsünüz. Örnek olarak, tüm VM yeniden boyutlandırma işlemleri, yönetim hesabının parolasını değiştirme ve statik bir IP adresi ayarlama sayılabilir.

### <a name="azure-security-center-and-windows-update"></a>Azure Güvenlik Merkezi ve Windows Update

Azure Güvenlik Merkezi, eksik işletim sistemi güncelleştirmeleri için günlük Windows ve Linux VM 'lerini izler. Güvenlik Merkezi, Windows VM 'de hangi hizmetin yapılandırıldığına bağlı olarak Windows Update veya Windows Server Update Services (WSUS) ' dan kullanılabilen güvenlik ve kritik güncelleştirmeler listesini alır. Güvenlik Merkezi, Linux sistemleri için en son güncelleştirmeleri de denetler. SANAL makinenizde bir sistem güncelleştirmesi eksikse, Güvenlik Merkezi, sistem güncelleştirmelerini uygulamanızı önerir. Bu sistem güncelleştirmelerinin uygulaması, Azure portal Güvenlik Merkezi aracılığıyla denetlenir. Bazı güncelleştirmeler uygulandıktan sonra, VM yeniden başlatmaları gerekebilir. Daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'nde sistem güncelleştirmelerini uygulama](../../security-center/security-center-apply-system-updates.md).

Şirket içi sunucular gibi, Azure, bu makinelerin kullanıcıları tarafından yönetilmesi amaçlanan için Windows Update güncelleştirmeleri Windows VM 'lerine itelemez. Ancak, otomatik Windows Update ayarını etkin bırakmanız önerilir. Güncelleştirmelerin Windows Update otomatik olarak yüklenmesi Ayrıca güncelleştirmeler uygulandıktan sonra yeniden başlatma işleminin oluşmasına neden olabilir. Daha fazla bilgi için bkz. [WINDOWS Update SSS](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>VM 'nizin kullanılabilirliğini etkileyen diğer durumlar

Azure 'un VM kullanımını etkin bir şekilde askıya alabileceği başka durumlar da vardır. Bu eylem yapılmadan önce e-posta bildirimleri alacaksınız, bu nedenle temeldeki sorunları çözme şansı elde edersiniz. Sanal makine kullanılabilirliğini etkileyen sorunların örnekleri, güvenlik ihlallerinin yanı sıra ödeme yöntemlerinin sona erme süresini içerir.

### <a name="host-server-faults"></a>Ana sunucu hataları

VM, bir Azure veri merkezinde çalışan fiziksel bir sunucuda barındırılır. Fiziksel sunucu, diğer diğer Azure bileşenlerine ek olarak konak Aracısı adlı bir aracı çalıştırır. Fiziksel sunucudaki bu Azure yazılım bileşenleri yanıt vermemeye başlarsa, izleme sistemi, kurtarmayı denemek için ana bilgisayar sunucusunun yeniden başlatılmasını tetikler. VM genellikle beş dakika içinde yeniden kullanılabilir ve daha önceden aynı ana bilgisayarda canlı olmaya devam eder.

Sunucu hataları genellikle bir sabit disk veya katı hal sürücüsü arızası gibi donanım arızasından kaynaklanır. Azure bu oluşumları sürekli izler, temeldeki hataları tanımlar ve hafifletme uygulandıktan ve sınandıktan sonra güncelleştirmeleri kaydeder.

Bazı ana bilgisayar sunucusu hataları bu sunucuya özgü olabileceğinden, sanal makineyi el ile başka bir konak sunucusuna yeniden dağıtarak yinelenen bir VM yeniden başlatma durumu artırılabilir. Bu işlem, VM 'nin Ayrıntılar sayfasındaki yeniden **dağıtma** seçeneği kullanılarak veya Azure Portal VM 'yi durdurup yeniden başlatarak tetiklenebilir.

### <a name="auto-recovery"></a>Otomatik kurtarma

Ana bilgisayar sunucusu herhangi bir nedenle yeniden başlatılamediğinde, Azure platformu, daha fazla araştırma için hatalı ana bilgisayar sunucusunu döndürmeden geri almak üzere bir otomatik kurtarma eylemi başlatır. 

Bu konaktaki tüm VM 'Ler otomatik olarak farklı, sağlıklı bir konak sunucusuna yeniden konumlandırılır. Bu işlem genellikle 15 dakika içinde tamamlanır. Otomatik kurtarma işlemi hakkında daha fazla bilgi edinmek için bkz. [sanal makinelerin otomatik olarak kurtarılması](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Planlanmamış bakım

Nadir durumlarda Azure işlem ekibinin, Azure platformunun genel durumunu sağlamak için bakım etkinlikleri gerçekleştirmesi gerekebilir. Bu davranış, VM kullanılabilirliğini etkileyebilir ve genellikle daha önce açıklandığı gibi otomatik kurtarma eylemine neden olur.  

Planlanmamış bakım şunları içerir:

- Acil düğüm birleştirme
- Acil ağ anahtarı güncelleştirmeleri

### <a name="vm-crashes"></a>VM çöküyor

VM 'deki sorunlar nedeniyle sanal makineler yeniden başlatılabilir. VM üzerinde çalışan iş yükü veya rol, Konuk işletim sistemi içinde bir hata denetimi tetiklenebilir. Kilitlenme nedenini belirlemede yardımcı olması için, Windows VM 'Leri için sistem ve uygulama günlüklerini ve Linux VM 'lerinin seri günlüklerini görüntüleyin.

### <a name="storage-related-forced-shutdowns"></a>Depolama ile ilgili zorlamalı kapatmalar

Azure 'daki VM 'Ler, Azure depolama altyapısında barındırılan işletim sistemi ve veri depolama için sanal disklere bağımlıdır. VM ve ilişkili sanal diskler arasındaki kullanılabilirlik veya bağlantı 120 saniyeden uzun bir süre boyunca etkilendiğinde, Azure platformu veri bozulmasını önlemek için VM 'lerin zorla kapatılmasını gerçekleştirir. Depolama bağlantısı geri yüklendikten sonra VM 'Ler otomatik olarak yeniden desteklenir. 

Kapatılma süresi beş dakika kadar kısa olabilir, ancak önemli ölçüde uzun sürebilir. Aşağıda, depolama ile ilgili zorlanan kapatmalar ile ilişkili belirli durumlardan biri verilmiştir: 

**GÇ sınırlarını aşma**

G/ç istekleri sürekli kısıtlandığı için VM 'Ler, saniye başına g/ç işlemi (ıOPS) birimi disk için g/ç sınırlarını aştığından geçici olarak devre dışı olabilir. (Standart disk depolaması 500 ıOPS ile sınırlıdır.) Bu sorunu azaltmak için, iş yüküne bağlı olarak disk şeritleme veya Konuk VM 'nin içindeki depolama alanını yapılandırma ' yı kullanın. Ayrıntılar için bkz. [Azure VM 'Leri En Iyi depolama performansı Için yapılandırma](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx).

### <a name="other-incidents"></a>Diğer olaylar

Nadir koşullarda, yaygın olarak karşılaşılan bir sorun, bir Azure veri merkezinde birden çok sunucuyu etkileyebilir. Bu sorun oluşursa, Azure ekibi etkilenen aboneliklere e-posta bildirimleri gönderir. [Azure hizmet durumu panosu](https://azure.microsoft.com/status/) ' nu ve devam eden kesintiler ve geçmiş olayların durumu için Azure Portal kontrol edebilirsiniz.
