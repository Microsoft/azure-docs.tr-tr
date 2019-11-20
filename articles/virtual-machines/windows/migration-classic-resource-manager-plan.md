---
title: IaaS kaynaklarının klasik 'dan Azure Resource Manager geçişini planlama
description: IaaS kaynaklarının klasik 'dan Azure Resource Manager geçişini planlama
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: 2c0f4924c41b36c306d4e6b9286105662744c4da
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033235"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>IaaS kaynaklarının klasik 'dan Azure Resource Manager geçişini planlama
Azure Resource Manager çok sayıda harika özellik sunurken, işlerin sorunsuz şekilde çalıştığından emin olmak için geçiş yolculuğunun planlanmaları önemlidir. Planlamada harcama süresi, geçiş etkinliklerini yürütürken sorunlarla karşılaşmamanız gerekir.

> [!NOTE]
> Aşağıdaki yönergeler, Azure müşteri danışmanlık ekibi ve bulut çözümü mimarlarının büyük ortamları geçirme konusunda müşterilerle çalıştığı bir şekilde katkıda bulunmıştı. Bu belge başarılı bir şekilde başarılı oldu desenler olarak güncellenmeye devam edecek, bu nedenle yeni öneriler olup olmadığını görmek için zaman zaman geri çekin.

Geçiş yolculuğunun dört genel aşaması vardır:<br>

![Geçiş aşamaları](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planlama

### <a name="technical-considerations-and-tradeoffs"></a>Teknik hususlar ve avantajları

Teknik gereksinim boyutunuza, coğrafi ve işletimsel uygulamalara bağlı olarak şunları göz önünde bulundurmanız gerekebilir:

1. Kuruluşunuz için neden Azure Resource Manager tercih edilir?  Geçiş için iş nedenleri nelerdir?
2. Azure Resource Manager yönelik teknik nedenler nelerdir?  Ne (varsa) ek Azure hizmetleri almak istiyorsunuz?
3. Geçişe hangi uygulama (veya sanal makine kümeleri) dahildir?
4. Geçiş API 'siyle hangi senaryolar desteklenir?  [Desteklenmeyen özellikleri ve konfigürasyonları](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations)gözden geçirin.
5. İşletimsel takımlar artık hem klasik hem de Azure Resource Manager uygulamalar/VM 'Leri destekliyor mu?
6. (Varsa) VM dağıtımınızı, yönetimini, izlemeyi ve raporlama işlemlerinizi nasıl değiştirip Azure Resource Manager?  Dağıtım betiklerinizin güncellenmesi gerekiyor mu?
7. Paydaşları (son kullanıcılar, uygulama sahipleri ve altyapı sahipleri) uyarmak için iletişim planı nedir?
8. Ortamın karmaşıklığına bağlı olarak, uygulamanın son kullanıcılar ve uygulama sahipleri tarafından kullanılamadığı bir bakım dönemi olması gerekir mi?  Öyleyse ne kadar süreyle kullanmıştı?
9. Paydaşların Azure Resource Manager konusunda bilgili ve proficde emin olmak için eğitim planı nedir?
10. Geçiş için program yönetimi veya proje yönetim planı nedir?
11. Azure Resource Manager geçişi ve diğer ilgili teknoloji yol haritaları için zaman çizelgeleri nelerdir?  En iyi şekilde hizalansın mı?

### <a name="patterns-of-success"></a>Başarı desenleri

Başarılı müşteriler, önceki soruların ele alındığı, belgelendiği ve yönetildiği ayrıntılı planlara sahiptir.  Geçiş planlarının sponsorlara ve proje katılımcılarına büyük ölçüde iletildiğinden emin olun.  Geçiş seçenekleriniz hakkında bilgi sahibi olmak için kendinizi deneyin; Aşağıdaki geçiş belge kümesini okuma işlemi, kesinlikle önerilir.

* [IaaS kaynaklarının klasik ile Azure Resource Manager geçişine genel bakış](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Klasik modelden Azure Resource Manager’a platform destekli geçişe ayrıntılı teknik bakış](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarının Klasik’ten Azure Resource Manager’a geçişini planlama](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için PowerShell 'i kullanma](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için CLı kullanma](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarının klasik 'dan Azure Resource Manager geçişine yardımcı olacak topluluk araçları](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En sık karşılaşılan geçiş hatalarını gözden geçirme](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarını klasik konumundan Azure Resource Manager geçirme hakkında en sık sorulan soruları gözden geçirin](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Kaçınmak için

- Plan oluşturulamadı.  Bu geçişin teknoloji adımları kanıtlanmış ve sonuç tahmin edilebilir.
- Platform tarafından desteklenen geçiş API 'sinin tüm senaryolar için hesap olacağını varsayımını unutmayın. Desteklenen senaryoları anlamak için [Desteklenmeyen özellikleri ve konfigürasyonları](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) okuyun.
- Son kullanıcılar için olası uygulama kesintisi planlanmıyor.  Potansiyel olarak kullanılamayan uygulama zamanının son kullanıcılarını yeterince uyarmak için yeterli arabellek planlayın.


## <a name="lab-test"></a>Laboratuvar testi

**Ortamınızı çoğaltın ve test geçişi yapın**
  > [!NOTE]
  > Mevcut ortamınızın tam çoğaltması, Microsoft Desteği tarafından resmi olarak desteklenmeyen, topluluk tarafından katkıda bulunulan bir araç kullanılarak yürütülür. Bu nedenle bu, **isteğe bağlı** bir adımdır ancak üretim ortamlarınıza dokunmadan sorunları bulmanın en iyi yoludur. Topluluk tarafından katkıda bulunulan bir araç kullanmak bir seçenek değilse, aşağıda doğrula/hazırla/durdur Çalıştır önerisi hakkında bilgi edinin.
  >

  Tam senaryonuz (işlem, ağ ve depolama) için bir laboratuar testi yürütmek, sorunsuz bir geçiş sağlamanın en iyi yoludur. Bu, şunları sağlamanıza yardımcı olur:

- Test etmek için bir veya daha fazla üretim olmayan mevcut bir ortam. Sürekli olarak geçirilebilecek ve kalıcı olarak değiştirilebilecek bir, tamamen ayrı bir laboratuvar yapmanızı öneririz.  Gerçek aboneliklerden meta verileri toplama/doldurma betikleri aşağıda listelenmiştir.
- Laboratuvarın ayrı bir abonelikte oluşturulması iyi bir fikirdir. Bunun nedeni, laboratuvarın tekrar tekrar parçalanmış ve ayrı ve yalıtılmış bir aboneliğe sahip olmanın, gerçek olarak yanlışlıkla silinme olasılığını azaltmaktır.

  Bu, AsmMetadataParser Aracı kullanılarak yapılabilir. [Bu araç hakkında daha fazla bilgi için buraya tıklayın](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Başarı desenleri

Daha büyük geçişlerde bulunan sorunlar aşağıda verilmiştir. Bu kapsamlı bir liste değildir ve daha fazla ayrıntı için [Desteklenmeyen özelliklere ve yapılandırmalara](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) başvurmalısınız.  Bu teknik sorunlarla karşılaşmanız veya karşılaşmamanız, ancak geçiş girişiminden önce bunları çözmenizde daha sorunsuz bir deneyim sağlamaktır.

- **Doğrulama/hazırlama/durdurma kuru çalıştırma** -bu, büyük olasılıkla Azure Resource Manager geçiş başarısını sağlamak için en önemli adımdır. Geçiş API 'sinin üç ana adımı vardır: doğrulama, hazırlama ve tamamlama. Doğrulama, klasik ortamınızın durumunu okur ve tüm sorunların sonucunu döndürür. Ancak, Azure Resource Manager yığınında bazı sorunlar mevcut olabileceğinden, doğrulama her şeyi yakalayamaz. Geçiş sürecinde bir sonraki adım, hazırlama bu sorunları ortaya çıkarmak için yardımcı olacaktır. Hazırlama, meta verileri klasik 'dan Azure Resource Manager taşıyacaktır, ancak taşıma işlemine uygulanmaz ve klasik taraftaki herhangi bir şeyi kaldırmaz veya değiştirmeyecektir. Kuru çalıştırma, geçişin hazırlanması ve iptal etme (yürütme**değil**) geçişinin hazırlanması ile ilgilidir. Doğrula/hazırla/durdur kuru çalıştırmasının hedefi, Azure Resource Manager yığındaki tüm meta verileri görmektir, bunu inceler (*Program aracılığıyla veya portalda*) ve her şeyin doğru şekilde geçirildiğini ve teknik sorunlardan çalıştığını doğrular.  Ayrıca, kapalı kalma süresini uygun şekilde planlayabilmeniz için geçiş süresi hakkında fikir verir.  Doğrulama/hazırlama/durdurma, herhangi bir kullanıcının kapalı kalma süresine neden olmaz; Bu nedenle, uygulama kullanımı kesintiye uğramamış olabilir.
  - Aşağıdaki öğelerin, Kuru çalıştırmadan önce çözülmesi gerekir, ancak bir kuru çalıştırma testi, kaçırıldıklarında bu hazırlık adımlarını güvenle da temizler. Kurumsal geçiş sırasında, geçiş hazırlığını sağlamak için kuru çalıştırmayı güvenli ve değerli bir yol olarak bulduk.
  - Hazırlama çalışırken, tüm sanal ağ için denetim düzlemi (Azure yönetim işlemleri) kilitlenir, bu nedenle doğrulama/hazırlama/durdurma sırasında VM meta verilerinde hiçbir değişiklik yapılamaz.  Ancak, herhangi bir uygulama işlevi (RD, VM kullanımı vb.) etkilenmeyecektir.  VM kullanıcıları kuru çalıştırmanın yürütüldüğünü bilmez.

- **Express Route devreleri ve VPN**. Halen yetkilendirme bağlantıları olan hızlı rota ağ geçitleri kapalı kalma süresi olmadan geçirilemez. Geçici çözüm için bkz. [ExpressRoute devreleri ve ilişkili sanal ağları klasik 'dan Kaynak Yöneticisi dağıtım modeline geçirme](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **VM uzantıları** -sanal makine uzantıları, çalışan VM 'leri geçirmeye yönelik en büyük yol bloklarından biridir. VM uzantılarının düzeltilmesi 1-2 güne kadar sürer ve bu nedenle plana göre plan yapın.  Çalışan VM 'lerin sanal makine uzantı durumunu raporlamak için çalışan bir Azure Aracısı gerekir. Durum, çalışan bir VM için bozuk olarak geri dönerse, bu işlem geçişi durdurur. Geçişin etkinleştirilmesi için aracının kendisi çalışma sırasında olması gerekmez, ancak sanal makinede uzantılar varsa, geçişin ileri ilerlemek için hem çalışan bir aracı hem de giden internet bağlantısı (DNS ile) gerekir.
  - Geçiş sırasında bir DNS sunucusuyla bağlantı kaybolursa, BgInfo sürüm 1 dışındaki tüm VM uzantıları.\*, geçiş hazırlanmadan önce her VM 'den kaldırılması gerekir ve ardından Azure Resource Manager geçişten sonra sanal makineye yeniden eklenir.  **Bu yalnızca çalıştıran VM 'Ler içindir.**  VM 'Ler serbest bırakılmış olarak durdurulmuşsa, VM uzantılarının kaldırılması gerekmez.

  > [!NOTE]
  > Azure tanılama ve Güvenlik Merkezi izleme gibi birçok uzantı, geçişten sonra kendilerini yeniden yükleyerek bir sorun değildir.

  - Ayrıca, ağ güvenlik gruplarının giden Internet erişimini kısıtmadığından emin olun. Bu durum bazı ağ güvenlik grupları yapılandırmalarında gerçekleşebilir. VM uzantılarının Azure Resource Manager 'ye geçirilmesi için giden internet erişimi (ve DNS) gereklidir.
  - BgInfo uzantısının iki sürümü mevcuttur ve 1 ve 2. sürüm olarak adlandırılır.  

      - VM, BgInfo sürüm 1 uzantısını kullanıyorsa, bu uzantıyı olduğu gibi bırakabilirsiniz. Geçiş API 'SI bu uzantıyı atlar. BgInfo uzantısı geçişten sonra eklenebilir.
      - VM JSON tabanlı BgInfo sürüm 2 uzantısını kullanıyorsa, VM Azure portal kullanılarak oluşturulmuştur. Geçiş API 'SI, aracının çalıştığı ve giden internet erişimi (ve DNS) olduğundan, bu uzantıyı Azure Resource Manager geçişe ekler.

  - **Düzeltme seçeneği 1**. Sanal makinelerinizin giden internet erişimi, çalışan bir DNS hizmeti ve VM 'lerde çalışan Azure aracıları olmadığını biliyorsanız, hazırlama işleminden önce geçiş işlemi kapsamında tüm VM uzantılarını kaldırın ve ardından geçişten sonra VM uzantılarını yeniden yükleyin.
  - **Düzeltme seçeneği 2**. VM uzantıları aceden çok büyük ise, diğer bir seçenek geçişten önce tüm sanal makinelerin kapatılmasını/serbest geçirilmesini sağlar. Serbest bırakılmış VM 'Leri geçirin ve Azure Resource Manager tarafında yeniden başlatın. Buradaki avantaj, VM uzantılarının geçirileceği bir avantajdır. Bu, tüm genel kullanıma açık sanal IP 'Lerin kaybolacağı (Bu bir Starter olmayan) ve çalışan uygulamalarda çok daha fazla etkiye neden olan VM 'Lerin kapanmasına açıktır.

    > [!NOTE]
    > Bir Azure Güvenlik Merkezi ilkesi geçirilmekte olan çalışan VM 'Lere göre yapılandırıldıysa, uzantılar kaldırılmadan önce güvenlik ilkesinin durdurulması gerekir, aksi takdirde güvenlik izleme uzantısı kaldırıldıktan sonra sanal makineye otomatik olarak yeniden yüklenir.

- **Kullanılabilirlik kümeleri** -Azure Resource Manager geçirilecek bir sanal ağ (vNet) Için, klasik dağıtımın (örneğin, bulut hizmeti) tümünün tek bir kullanılabilirlik kümesinde olması gerekir, aksi durumda VM 'lerin herhangi bir kullanılabilirlik kümesinde olmaması gerekir. Bulut hizmetinde birden fazla kullanılabilirlik kümesi Azure Resource Manager uyumlu değil ve geçiş işlemi durdurur.  Ayrıca, bir kullanılabilirlik kümesinde bazı VM 'Ler olamaz ve bazı VM 'Ler bir kullanılabilirlik kümesinde değildir. Bu sorunu çözmek için bulut hizmetinizi düzeltmeniz veya reshuffle gerekir.  Bu zaman alıcı olabilir.

- **Web/çalışan rolü dağıtımları** -Web ve çalışan rollerini içeren Cloud Services Azure Resource Manager geçirilemez. Web ve çalışan rollerinizin içeriğini geçirmek için, kodun kendisini daha yeni PaaS uygulama hizmetlerine geçirmeniz gerekir (Bu, bu belgenin kapsamının ötesinde). Web/çalışan rollerini olduğu gibi bırakmak, ancak klasik VM 'Leri Kaynak Yöneticisi dağıtım modeline geçirmek istiyorsanız, geçiş başlamadan önce web/çalışan rollerinin önce sanal ağdan kaldırılması gerekir.  Tipik bir çözüm, web/çalışan rolü örneklerini bir ExpressRoute devresine de bağlanmış ayrı bir klasik sanal ağa taşıyamaktır. Eski yeniden dağıtma durumunda, yeni bir klasik sanal ağ oluşturun, web/çalışan rollerini bu yeni sanal ağa taşıyın/yeniden dağıtın ve ardından taşınan sanal ağdan dağıtımları silin. Kod değişikliği gerekmiyor. Yeni [sanal ağ eşleme](../../virtual-network/virtual-network-peering-overview.md) özelliği, geçirilmekte olan sanal ağ (**sanal ağ 'dan sonra) gibi aynı Azure bölgesindeki web/çalışan rollerini ve diğer sanal ağları içeren klasik sanal ağı birbirine bağlamak için kullanılabilir. geçiş, eşlenmiş sanal ağlar geçirilemeyeceği şekilde tamamlanır**), bu nedenle performans kaybı olmadan aynı özellikleri ve gecikme/bant genişliği yaptırımlarını sağlar. [Sanal ağ eşlemesi](../../virtual-network/virtual-network-peering-overview.md)eklenmesi, web/çalışan rolü dağıtımları artık kolayca azaltılabilir ve Azure Resource Manager geçişe engel olmaz.

- **Azure Resource Manager kotaları** -Azure bölgelerinin hem klasik hem de Azure Resource Manager ayrı kotaları/limitleri vardır. Geçiş senaryosunda yeni donanım tüketilmese de *(mevcut VM 'Leri klasik bilgisayardan Azure Resource Manager olarak değiştirdik)* , geçişin başlayabilmesi için Azure Resource Manager kotaların hala yeterli kapasiteye sahip olması gerekir. Aşağıda listelenen önemli limitler aşağıda verilmiştir.  Sınırları yükseltmek için bir kota destek bileti açın.

    > [!NOTE]
    > Bu sınırların geçirilmesi için geçerli ortamla aynı bölgede oluşturulması gerekir.
    >

  - Ağ Arabirimleri
  - Yük Dengeleyiciler
  - Genel IP'ler
  - Statik genel IP 'Ler
  - Çekirdek
  - Ağ Güvenlik Grupları
  - Yönlendirme Tabloları

    Azure PowerShell en son sürümü ile aşağıdaki komutları kullanarak geçerli Azure Resource Manager kotalarınızı kontrol edebilirsiniz.
    
     

    **İşlem** *(çekirdek, kullanılabilirlik kümeleri)*

    ```powershell
    Get-AzVMUsage -Location <azure-region>
    ```

    **Ağ** *(sanal ağlar, statik genel IP 'Ler, genel IP 'Ler, ağ güvenlik grupları, ağ arabirimleri, yük dengeleyiciler, rota tabloları)*

    ```powershell
    Get-AzUsage /subscriptions/<subscription-id>/providers/Microsoft.Network/locations/<azure-region> -ApiVersion 2016-03-30 | Format-Table
    ```

    **Depolama** *(depolama hesabı)*

    ```powershell
    Get-AzStorageUsage
    ```

- **Azure Resource Manager API azaltma limitleri** -yeterince büyük bir ortamınız varsa (örn. > 400 VM 'Ler bir VNET 'te), Azure Resource Manager 'de yazma işlemleri için varsayılan API azaltma sınırlarına (Şu anda `1200 writes/hour`) ulaşırsınız. Geçişe başlamadan önce, aboneliğiniz için bu sınırı artırmak üzere bir destek bileti yükseltmeniz gerekir.


- **Sağlama süresi doldu VM durumu** -herhangi bir sanal makinenin durumu `provisioning timed out`varsa, bu, geçiş öncesi çözümlenmesi gerekir. Bunu yapmanın tek yolu, VM 'nin sağlamasını kaldırma/yeniden sağlama ile kapalı kalma süresi (silme, diski koruma ve VM 'yi yeniden oluşturma).

- **Rolestateunknown VM durumu** -geçiş bir `role state unknown` hata iletisi nedeniyle kilitlenmektedir, portalı kullanarak VM 'yi inceleyin ve çalıştığından emin olun. Bu hata genellikle birkaç dakika sonra kendi kendine (düzeltme gerekmez) geçer ve genellikle bir sanal makine `start`, `stop``restart` işlemleri sırasında görülen geçici bir tür olur. **Önerilen uygulama:** birkaç dakika sonra geçişi yeniden deneyin.

- **Yapı kümesi yok** ; bazı durumlarda bazı sanal makineler çeşitli tek nedenlerle geçirilemez. Bu bilinen durumlardan biri VM 'nin kısa süre önce oluşturulması (son hafta içinde veya bu nedenle) ve Azure Resource Manager iş yükleri için henüz donatılmış bir Azure kümesini aratadır.  `fabric cluster does not exist` ve VM 'nin geçirilemeyeceğini belirten bir hata alırsınız. Küme yakında Azure Resource Manager etkin olacak şekilde, birkaç günün beklenmesi genellikle bu sorunu çözer. Bununla birlikte, bir acil geçici çözüm VM `stop-deallocate`, ardından geçişe devam etmek ve geçiş işleminden sonra Azure Resource Manager sanal makineyi yeniden başlatmak için kullanılır.

### <a name="pitfalls-to-avoid"></a>Kaçınmak için

- Kısayolları almaz ve doğrulama/hazırlama/durdurma kuru çalıştırma geçişlerini atlayın.
- Büyük bir olasılıkla, tüm olası sorunlarınız, doğrulama/hazırlama/durdurma adımları sırasında yüzden oluşur.

## <a name="migration"></a>Geçiş

### <a name="technical-considerations-and-tradeoffs"></a>Teknik hususlar ve avantajları

Artık ortamınızdaki bilinen sorunlar üzerinden çalıştıysanız hazırsınız.

Gerçek geçişler için şunları göz önünde bulundurmanız gerekebilir:

1. Sanal ağı (en küçük geçiş birimi) artan önceliğe sahip planlayın ve zamanlayın.  Önce basit sanal ağları yapın, daha karmaşık sanal ağlarla devam edin.
2. Çoğu müşterinin üretim dışı ve üretim ortamları olur.  Üretimi en son zamanlayın.
3. **(Isteğe bağlı)** Beklenmedik bir sorun ortaya çıktığında çok sayıda arabelleğe sahip bir bakım kapalı kalma süresi zamanlayın.
4. Servis talebi sorunları ortaya çıktığında destek ekipleriyle iletişim kurun ve bunlarla hizalayın.

### <a name="patterns-of-success"></a>Başarı desenleri

_Laboratuvar testi_ bölümündeki teknik kılavuz, gerçek bir geçişten önce değerlendirilmeli ve azaltılmalıdır.  Yeterli test sayesinde geçiş aslında etkinlik dışı bir olaydır.  Üretim ortamları için, güvenilir bir Microsoft iş ortağı veya Microsoft Premier hizmetleri gibi ek desteğin olması yararlı olabilir.

### <a name="pitfalls-to-avoid"></a>Kaçınmak için

Tam test değil, geçiş sırasında sorunlara ve gecikmeye neden olabilir.  

## <a name="beyond-migration"></a>Geçişin ötesinde

### <a name="technical-considerations-and-tradeoffs"></a>Teknik hususlar ve avantajları

Artık Azure Resource Manager olduğunuza göre, platformu en üst düzeye çıkarın.  Ek avantajlar hakkında bilgi edinmek için [Azure Resource Manager genel bakış](../../azure-resource-manager/resource-group-overview.md) makalesini okuyun.

Göz önüne almanız gerekenler:

- Geçişi diğer etkinliklerle paketleme.  Çoğu müşteri bir uygulama bakım penceresini kabul edebilir.  Bu durumda, yönetilen disklere şifreleme ve geçiş gibi diğer Azure Resource Manager yeteneklerini etkinleştirmek için bu kapalı kalma süresini kullanmak isteyebilirsiniz.
- Azure Resource Manager için teknik ve iş nedenlerini yeniden ziyaret edin; yalnızca ortamınıza uygulanan Azure Resource Manager kullanılabilir ek hizmetleri etkinleştirin.
- PaaS hizmetleri ile ortamınızı modernleştirin.

### <a name="patterns-of-success"></a>Başarı desenleri

Artık Azure Resource Manager hangi hizmetlerde etkinleştirmek istediğinizi bilerek yapın.  Birçok müşteri, Azure ortamları için aşağıda etkileyici bilgi bulur:

- [Rol tabanlı Access Control](../../role-based-access-control/overview.md).
- Daha [kolay ve daha denetimli dağıtıma yönelik şablonlar Azure Resource Manager](../../azure-resource-manager/template-deployment-overview.md).
- [Etiketler](../../azure-resource-manager/resource-group-using-tags.md).
- [Etkinlik denetimi](../../azure-resource-manager/resource-group-audit.md)
- [Azure Ilkeleri](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Kaçınmak için

Bu klasik bu Azure Resource Manager geçiş yolculuğunu unutmayın.  Özgün iş nedenleri nelerdir? İş nedenine ulaştınız mı?


## <a name="next-steps"></a>Sonraki adımlar

* [IaaS kaynaklarının klasik ile Azure Resource Manager geçişine genel bakış](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Klasik modelden Azure Resource Manager’a platform destekli geçişe ayrıntılı teknik bakış](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için PowerShell 'i kullanma](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için CLı kullanma](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [VPN Gateway klasik Kaynak Yöneticisi geçişe](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [ExpressRoute devreleri ve ilişkili sanal ağları klasik 'dan Kaynak Yöneticisi dağıtım modeline geçirin](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [IaaS kaynaklarının klasik 'dan Azure Resource Manager geçişine yardımcı olacak topluluk araçları](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En sık karşılaşılan geçiş hatalarını gözden geçirme](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarını klasik konumundan Azure Resource Manager geçirme hakkında en sık sorulan soruları gözden geçirin](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
