---
title: Azure 'da IaaS iş yükleri için en iyi güvenlik yöntemleri | Microsoft Docs
description: " İş yüklerinin Azure IaaS 'ye geçirilmesi, tasarımlarımızın yeniden değerlendirilme fırsatlarını getirir "
services: security
documentationcenter: na
author: barclayn
manager: rkarlin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: barclayn
ms.openlocfilehash: 7558ad2e5a4404db140daff89171af92c6072ef6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805986"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Azure'da IaaS iş yükleri için en iyi güvenlik yöntemleri
Bu makalede, VM 'Ler ve işletim sistemleri için en iyi güvenlik uygulamaları açıklanmaktadır.

En iyi uygulamalar, görüşlerden oluşan yarışmaları temel alır ve geçerli Azure platformu özellikleri ve özellik kümeleriyle çalışır. Zamanla ve teknolojiler zaman içinde değişeceğinden, bu makale bu değişiklikleri yansıtacak şekilde güncelleştirilir.

Hizmet olarak altyapı (IaaS) senaryolarında [Azure sanal makineleri (VM 'ler)](/azure/virtual-machines/) , bulut bilgi işlem kullanan kuruluşlar için ana iş yüküdür. Bu olgu, kuruluşların iş yüklerini buluta yavaş bir şekilde geçirmek istediği [karma senaryolara](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) karşı daha da çalışır. Bu tür senaryolarda, [IaaS için genel güvenlik konularını](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)izleyin ve tüm sanal makinelerinize en iyi güvenlik uygulamalarını uygulayın.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Kimlik doğrulama ve erişim denetimi kullanarak VM 'Leri koruma
VM 'lerinizi korumanın ilk adımı yalnızca yetkili kullanıcıların yeni VM 'Leri ayarlayave VM 'Lere erişim izni sağlamaktır.

> [!NOTE]
> Azure 'da Linux VM 'lerinin güvenliğini artırmak için Azure AD kimlik doğrulamasıyla tümleştirebilirsiniz. [Linux VM 'ler Için Azure AD kimlik doğrulaması](/azure/virtual-machines/linux/login-using-aad)kullandığınızda, sanal makinelere erişime izin veren veya erişimi reddeden ilkeleri merkezi olarak kontrol edin ve zorlayabilirsiniz.
>
>

**En iyi yöntem**: VM erişimini denetleyin.   
**Ayrıntı**: kuruluşunuzdaki kaynaklara yönelik kurallar oluşturmak ve özelleştirilmiş ilkeler oluşturmak için [Azure ilkelerini](/azure/azure-policy/azure-policy-introduction) kullanın. Bu ilkeleri [kaynak grupları](/azure/azure-resource-manager/resource-group-overview)gibi kaynaklara uygulayın. Bir kaynak grubuna ait olan VM 'Ler ilkelerini alırlar.

Kuruluşunuzun birçok aboneliği varsa, bu abonelikler için erişimi, ilkeleri ve uyumluluğu verimli bir şekilde yönetmeniz için bir yol gerekebilir. [Azure Yönetim grupları](/azure/azure-resource-manager/management-groups-overview) , aboneliklerin üzerinde bir kapsam düzeyi sağlar. Abonelikleri yönetim grupları (kapsayıcılar) halinde düzenler ve idare koşullarınızı bu gruplara uygularsınız. Bir yönetim grubu içindeki tüm abonelikler, gruba uygulanan koşulları otomatik olarak devralınır. Yönetim grupları, sahip olabileceğiniz abonelik türüne bakılmaksızın kurumsal düzeyde yönetimi büyük ölçekte sunar.

**En iyi yöntem**: VM 'lerin kurulumunda ve dağıtımında değişkenlik azaltma.   
**Ayrıntı**: dağıtım seçimlerinizi güçlendirmek ve ortamınızdaki VM 'leri daha kolay anlamak ve stoklanmasını kolaylaştırmak için [Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates) şablonlarını kullanın.

**En iyi yöntem**: güvenli ayrıcalıklı erişim.   
**Ayrıntı**: kullanıcıların VM 'lere erişmesini ve bunları kurmasını sağlamak için [en az ayrıcalık yaklaşımı](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) ve yerleşik Azure rolleri kullanın:

- [Sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): VM 'leri yönetebilir, ancak bağlı oldukları sanal ağ veya depolama hesabını yönetemez.
- [Klasik sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): klasik dağıtım modeli kullanılarak oluşturulan VM 'leri yönetebilir, ancak VM 'lerin bağlı olduğu sanal ağ veya depolama hesabı değildir.
- [Güvenlik Yöneticisi](../../role-based-access-control/built-in-roles.md#security-admin): yalnızca güvenlik merkezi 'nde: güvenlik ilkelerini görüntüleyebilir, güvenlik durumlarını görüntüleyebilir, güvenlik ilkelerini düzenleyebilir, uyarıları ve önerileri görüntüleyebilir, uyarıları ve önerileri kapatabilir.
- [DevTest Labs kullanıcısı](../../role-based-access-control/built-in-roles.md#devtest-labs-user): her şeyi görüntüleyebilir, sanal makineleri yükleyebilir, başlatabilir, yeniden başlatabilir ve kapatabilir.

Abonelik yöneticileri ve coyöneticileri bu ayarı değiştirebilir ve bu ayarı, bir abonelikteki tüm VM 'lerin yöneticilerini yapabilir. Herhangi bir makinelerinizde oturum açmak için tüm abonelik yöneticilerinizi ve ortak yöneticilerine güvendiğinizden emin olun.

> [!NOTE]
> Aynı yaşam döngüsüne sahip VM 'Leri aynı kaynak grubuyla birleştirmeniz önerilir. Kaynak gruplarını kullanarak kaynaklarınız için faturalandırma maliyetlerini dağıtabilir, izleyebilir ve izleyebilirsiniz.
>
>

VM erişimini ve kurulumunu denetleyen kuruluşlar, genel VM güvenliğini artırır.

## <a name="use-multiple-vms-for-better-availability"></a>Daha iyi kullanılabilirlik için birden çok VM kullanma
VM 'niz yüksek kullanılabilirliğe sahip olması gereken kritik uygulamalar çalıştırıyorsa, birden çok VM kullanmanızı önemle tavsiye ederiz. Daha iyi kullanılabilirlik için bir [kullanılabilirlik kümesi](../../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) veya kullanılabilirlik [alanları](../../availability-zones/az-overview.md)kullanın.

Kullanılabilirlik kümesi, Azure 'da yerleştirdiğiniz VM kaynaklarının bir Azure veri merkezinde dağıtıldıklarında birbirinden yalıtılmış olmasını sağlamak için Azure 'da kullanabileceğiniz mantıksal bir gruplandırmadır. Azure, bir kullanılabilirlik kümesine yerleştirdiğiniz VM 'Lerin birden çok fiziksel sunucuda, bilgi işlem raflarının, depolama biriminde ve ağ anahtarlarında çalıştırılmasını sağlar. Bir donanım veya Azure yazılım hatası oluşursa, sanal makinelerinizin yalnızca bir alt kümesi etkilenir ve genel uygulamanız müşterilerinizin kullanımına açık olmaya devam eder. Kullanılabilirlik kümeleri, güvenilir bulut çözümleri oluşturmak istediğinizde önemli bir yetenektir.

## <a name="protect-against-malware"></a>Kötü amaçlı yazılımlardan korunun
Virüslerin, casus yazılımların ve diğer kötü amaçlı yazılımların tanımlanmasına ve kaldırılmasına yardımcı olması için kötü amaçlı yazılımdan koruma koruması kurmanız gerekir. [Microsoft kötü amaçlı yazılımdan](antimalware.md) koruma veya bir Microsoft iş ortağının Endpoint Protection çözümünü ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security)ve [System Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)) yükleyebilirsiniz.

Microsoft Antimalware gerçek zamanlı koruma, zamanlanmış tarama, kötü amaçlı yazılım düzeltme, imza güncelleştirmeleri, altyapı güncelleştirmeleri, örnek raporlama ve dışlama olay toplama gibi özellikler içerir. Üretim ortamınızdan ayrı olarak barındırılan ortamlar için, VM 'lerinizin ve bulut hizmetlerinizin korunmasına yardımcı olmak üzere bir kötü amaçlı yazılımdan koruma uzantısı kullanabilirsiniz.

Microsoft kötü amaçlı yazılımdan koruma ve iş ortağı çözümlerini, dağıtım kolaylığı ve yerleşik algılamalar (Uyarılar ve olaylar) için [Azure Güvenlik Merkezi](../../security-center/index.yml) ile tümleştirebilirsiniz.

**En iyi yöntem**: kötü amaçlı yazılımlara karşı koruma için kötü amaçlı yazılımdan koruma çözümü yükler   
**Ayrıntı**: [bir Microsoft iş ortağı çözümü veya Microsoft Antimalware yüklemesi](../../security-center/security-center-install-endpoint-protection.md)

**En iyi yöntem**: korumanızla ilgili durumu izlemek için kötü amaçlı yazılımdan koruma çözümünüzü Güvenlik Merkezi ile tümleştirin.   
**Ayrıntı**: [Güvenlik Merkezi ile Endpoint Protection sorunlarını yönetme](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>VM güncelleştirmelerinizi yönetin
Tüm şirket içi VM 'Ler gibi Azure sanal makineleri, Kullanıcı tarafından yönetilmek üzere tasarlanmıştır. Azure, Windows güncelleştirmelerini bunlara göndermiyor. VM güncelleştirmelerinizi yönetmeniz gerekir.

**En iyi yöntem**: VM 'lerinizi güncel tutun.   
**Ayrıntı**: Azure 'da, şirket içi ortamlarda veya diğer bulut sağlayıcılarında dağıtılan Windows ve Linux bilgisayarlarınıza yönelik işletim sistemi güncelleştirmelerini yönetmek Için Azure otomasyonu 'nda [güncelleştirme yönetimi](../../automation/automation-update-management.md) çözümünü kullanın. Tüm aracı bilgisayarlardaki kullanılabilir güncelleştirmelerin durumunu hızla değerlendirebilir ve sunucular için gerekli güncelleştirmeleri yükleme işlemini yönetebilirsiniz.

Güncelleştirme Yönetimi tarafından yönetilen bilgisayarlar, değerlendirme ve güncelleştirme dağıtımları gerçekleştirmek için aşağıdaki konfigürasyonları kullanır:

- Windows veya Linux için Microsoft Monitoring Agent (MMA)
- Linux için PowerShell İstenen Durum Yapılandırması (DSC)
- Otomasyon Karma Runbook Çalışanı
- Windows bilgisayarlar için Microsoft Update veya Windows Server Update Services (WSUS)

Windows Update kullanırsanız, otomatik Windows Update ayarını etkin bırakın.

**En iyi yöntem**: oluşturduğunuz görüntülerin Windows güncelleştirmelerinin en son hepsini içerdiğinden emin olun.   
**Ayrıntı**: tüm Windows güncelleştirmelerini denetleyin ve her dağıtımın ilk adımı olarak yükler. Bu ölçü, sizin veya kendi kitaplığınızdan gelen görüntüleri dağıtırken uygulanması özellikle önemlidir. Azure Marketi 'ndeki görüntüler varsayılan olarak otomatik olarak güncellense de, genel bir sürümden sonra bir gecikme süresi (birkaç hafta kadar) olabilir.

**En iyi yöntem**: işletim sisteminin yeni bir sürümünü zorlamak için sanal makinelerinizi düzenli olarak yeniden dağıtın.   
**Ayrıntı**: daha kolay bir şekilde DAĞıTMAK için VM 'nizi bir [Azure Resource Manager şablonuyla](../../azure-resource-manager/resource-group-authoring-templates.md) tanımlayın. Bir şablon kullanmak gerektiğinde düzeltme eki uygulanmış ve güvenli bir VM sağlar.

**En iyi yöntem**: sanal makinelere güvenlik güncelleştirmelerini hızlıca uygulayın.   
**Ayrıntı**: [eksik güvenlik güncelleştirmelerini tanımlamak ve bunları uygulamak](../../security-center/security-center-apply-system-updates.md)için Azure Güvenlik Merkezi 'ni (ücretsiz katman veya Standart katman) etkinleştirin.

**En iyi yöntem**: en son güvenlik güncelleştirmelerini yükler.   
**Ayrıntı**: müşterilerin Azure 'a taşımadığı ilk iş yüklerinden bazıları Labs ve dışarıdan yönelik sistemlerdir. Azure VM 'leriniz, internet erişimine açık olması gereken uygulamaları veya hizmetleri barındıralıyorsa, düzeltme eki hakkında dikkatli olun. İşletim sisteminin ötesinde Düzeltme Eki. İş ortağı uygulamalarında düzeltme eki yüklenmemiş güvenlik açıkları, iyi bir düzeltme eki yönetimi gerçekleşirken kaçınıyabileceği sorunlara da yol açabilir.

**En iyi yöntem**: bir yedekleme çözümünü dağıtın ve test edin.   
**Ayrıntı**: bir yedeklemenin, başka bir işlemi idare ettiğiniz şekilde işlenmesi gerekir. Bu, üretim ortamınızın bir parçası olan ve buluta genişleyen sistemlerdir.

Test ve geliştirme sistemleri, kullanıcıların şirket içi ortamlarıyla karşılaşmalarına bağlı olarak ne kadar büyümekle benzer geri yükleme özellikleri sağlayan yedekleme stratejilerini izlemelidir. Azure 'a taşınan üretim iş yükleri mümkün olduğunda mevcut yedekleme çözümleriyle tümleştirmelidir. Veya, yedekleme gereksinimlerinizi karşılamak için [Azure Backup](../../backup/backup-azure-vms-first-look-arm.md) kullanabilirsiniz.

Yazılım güncelleştirme ilkelerini zorlayamayan kuruluşlar, bilinen ve önceden düzeltilen güvenlik açıklarından yararlanan tehditlere açıktır. Sektör düzenlemelerine uymak için şirketlerin, bulutta bulunan iş yüklerinin güvenliğini sağlamaya yardımcı olmak için doğru güvenlik denetimlerini kullandığını kanıtlamaları gerekir.

Yazılım-geleneksel bir veri merkezinde en iyi uygulamaları güncelleştirme ve Azure IaaS birçok benzerlikler vardır. Azure 'da bulunan VM 'Leri dahil etmek için geçerli yazılım güncelleştirme ilkelerinizi değerlendirmenizi öneririz.

## <a name="manage-your-vm-security-posture"></a>VM Güvenlik duruşunuzu yönetin
Siber tehditler gelişiyor. VM 'lerinizi koruma, tehditleri hızlı bir şekilde tespit etmek, Kaynaklarınıza yetkisiz erişimi engellemek, uyarıları tetiklemek ve hatalı pozitif sonuçları azaltmak için bir izleme yeteneği gerektirir.

[Windows](../../security-center/security-center-virtual-machine.md) ve [Linux sanal](../../security-center/security-center-linux-virtual-machine.md)makinelerinizin güvenlik duruşunu Izlemek için [Azure Güvenlik Merkezi](../../security-center/security-center-intro.md)'ni kullanın. Güvenlik Merkezi 'nde, aşağıdaki özelliklerden yararlanarak sanal makinelerinizi koruyun:

- Önerilen yapılandırma kuralları ile işletim sistemi güvenlik ayarlarını uygulayın.
- Eksik olabilecek sistem güvenliğini ve kritik güncelleştirmeleri belirleyip indirin.
- Endpoint antimalware koruması için öneriler dağıtın.
- Disk şifrelemeyi doğrulayın.
- Güvenlik açıklarını değerlendirin ve düzeltin.
- Tehditleri algılayın.

Güvenlik Merkezi tehditleri etkin bir şekilde izleyebilir ve olası tehditler güvenlik uyarıları 'nda kullanıma sunulur. Bağıntılı tehditler, güvenlik olayı adlı tek bir görünümde toplanır.

Güvenlik Merkezi, verileri [Azure izleyici günlüklerinde](/azure/log-analytics/log-analytics-overview)depolar. Azure Izleyici günlükleri, uygulama ve kaynaklarınızın çalışması hakkında Öngörüler sunan bir sorgu dili ve analiz altyapısı sağlar. Veriler ayrıca [Azure izleyici](../../batch/monitoring-overview.md), yönetim çözümleri ve bulutta veya Şirket içindeki sanal makinelere yüklenen aracılardan da toplanır. Bu paylaşılan işlevsellik, ortamınızın eksiksiz bir resmini oluşturmanıza yardımcı olur.

VM 'Ler için güçlü güvenlik zorlaması olmayan kuruluşlar, yetkisiz kullanıcıların güvenlik denetimlerini atlamalarını sağlayan olası denemelerden haberdar olmaya devam eder.

## <a name="monitor-vm-performance"></a>VM performansını izleme
Kaynak kötüye kullanımı, VM işlemlerinde gerekenden daha fazla kaynak tükettiği zaman bir sorun olabilir. Bir VM ile ilgili performans sorunları, hizmetin kesintiye uğramasına neden olabilir ve bu da kullanılabilirliği güvenlik ilkesini ihlal eder. Yüksek CPU veya bellek kullanımı bir hizmet reddi (DoS) saldırısı belirtebileceğinden, bu özellikle IIS veya diğer Web sunucularını barındıran VM 'Ler için önemlidir. Yalnızca bir sorun oluştuğu sırada VM erişiminin yeniden etkin olmaması, ancak normal işlem sırasında ölçülen taban çizgisi performansına karşı önlem olarak izlenmesi zorunludur.

Kaynağınızın sistem durumu hakkında görünürlük elde etmek için [Azure izleyicisini](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) kullanmanızı öneririz. Azure Izleyici özellikleri:

- [Kaynak tanılama günlük dosyaları](../../azure-monitor/platform/resource-logs-overview.md): VM kaynaklarınızı izler ve performansı ve kullanılabilirliği tehlikeye atabilecek olası sorunları tanımlar.
- [Azure tanılama uzantısı](/azure/azure-monitor/platform/diagnostics-extension-overview): Windows VM 'lerinde izleme ve tanılama özellikleri sağlar. Uzantıyı [Azure Resource Manager şablonun](/azure/virtual-machines/windows/extensions-diagnostics-template)bir parçası olarak ekleyerek bu özellikleri etkinleştirebilirsiniz.

VM performansını izleyen kuruluşlar, performans desenlerindeki belirli değişikliklerin normal veya olağan dışı olup olmadığını belirleyemez. Normalden daha fazla kaynak kullanan bir VM, bir dış kaynaktan veya VM 'de çalışan güvenliği aşılmış bir işlemden saldırı anlamına gelebilir.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Sanal sabit disk dosyalarınızı şifreleyin
Şifreleme anahtarlarınızla ve gizli dizilerinizle birlikte, depolamada bekleyen önyükleme hacimlerinizi ve veri hacimlerini korumanıza yardımcı olması için sanal sabit disklerinizi (VHD) şifrelemenizi öneririz.

[Azure disk şifrelemesi](../azure-security-disk-encryption-overview.md) , Windows ve Linux IaaS sanal makine disklerinizi şifrelemenize yardımcı olur. Azure disk şifrelemesi, işletim sistemi ve veri diskleri için birim şifrelemesi sağlamak üzere Windows 'un sektör standardı [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) özelliğini ve Linux 'un [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) özelliğini kullanır. Çözüm, Anahtar Kasası aboneliğinizdeki disk şifreleme anahtarlarını ve gizli dizileri denetlemenize ve yönetmenize yardımcı olmak üzere [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) ile tümleşiktir. Çözüm Ayrıca, sanal makine disklerindeki tüm verilerin Azure Storage 'da bekleyen olarak şifrelenmesini de sağlar.

Aşağıda, Azure disk şifrelemesi 'ni kullanmaya yönelik en iyi yöntemler verilmiştir:

**En iyi yöntem**: VM 'lerde şifrelemeyi etkinleştirin.   
**Ayrıntı**: Azure disk şifrelemesi, şifreleme anahtarlarını oluşturup anahtar kasanıza yazar. Anahtar Kasanızda şifreleme anahtarlarını yönetmek için Azure AD kimlik doğrulaması gerekir. Bu amaçla bir Azure AD uygulaması oluşturun. Kimlik doğrulama amacıyla, istemci gizli tabanlı kimlik doğrulaması veya [istemci sertifikası tabanlı Azure AD kimlik doğrulaması](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)kullanabilirsiniz.

**En iyi yöntem**: şifreleme anahtarları için ek bir güvenlik katmanı için anahtar şifreleme anahtarı (kek) kullanın. Anahtar kasanıza bir KEK ekleyin.   
**Ayrıntı**: anahtar kasasında anahtar şifreleme anahtarı oluşturmak için [Add-azkeyvaultkey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet 'ini kullanın. Ayrıca, anahtar yönetimi için şirket içi donanım güvenlik modülünüzü (HSM) bir KEK içeri aktarabilirsiniz. Daha fazla bilgi için [Key Vault belgelerine](../../key-vault/key-vault-hsm-protected-keys.md)bakın. Anahtar şifreleme anahtarı belirtildiğinde Azure disk şifrelemesi, Key Vault yazmadan önce şifreleme gizli dizilerini kaydırmak için bu anahtarı kullanır. Bu anahtarın bir şirket içi anahtar yönetimi HSM 'de bir kopyasını tutmak, anahtarların yanlışlıkla silinmesine karşı ek koruma sağlar.

**En iyi yöntem**: diskler şifrelenmeden önce bir [anlık görüntü](../../virtual-machines/windows/snapshot-copy-managed-disk.md) ve/veya yedekleme yapın. Yedeklemeler, şifreleme sırasında beklenmeyen bir hata oluşursa bir kurtarma seçeneği sağlar.   
**Ayrıntı**: yönetilen disklere sahip VM 'ler şifreleme gerçekleşmeden önce bir yedekleme gerektirir. Bir yedekleme yapıldıktan sonra, *-skipvmbackup* parametresini belirterek yönetilen diskleri şifrelemek için **set-AzVMDiskEncryptionExtension** cmdlet 'ini kullanabilirsiniz. Şifrelenmiş VM 'Leri yedekleme ve geri yükleme hakkında daha fazla bilgi için [Azure Backup](../../backup/backup-azure-vms-encryption.md) makalesine bakın.

**En iyi yöntem**: şifreleme gizli dizileri arasında bölge dizileri olmadığından emin olmak Için, Azure disk şifrelemesi, anahtar kasasının ve VM 'lerin aynı bölgede bulunmasını gerektirir.   
**Ayrıntı**: şifrelenecek VM ile aynı bölgede yer alan bir Anahtar Kasası oluşturun ve kullanın.

Azure disk şifrelemesi 'ni uyguladığınızda, aşağıdaki iş ihtiyaçlarını karşılamanız gerekir:

- IaaS VM 'Leri, kurumsal güvenlik ve uyumluluk gereksinimlerini karşılamak için sektör standardı şifreleme teknolojisi aracılığıyla geri kalanıyla korunmaktadır.
- IaaS VM 'Leri, müşteri denetimli anahtarlar ve ilkeler altında başlar ve Anahtar Kasanızda kullanımlarını denetleyebilirsiniz.

## <a name="restrict-direct-internet-connectivity"></a>Doğrudan internet bağlantısını kısıtla
VM doğrudan internet bağlantısını izleyin ve kısıtlayın. Saldırganlar açık yönetim bağlantı noktaları için ortak bulut IP aralıklarını sürekli tarar ve ortak parolalar ve bilinen düzeltme eki yüklenmemiş güvenlik açıkları gibi "kolay" saldırıları dener. Aşağıdaki tabloda, bu saldırılara karşı korunmaya yardımcı olacak en iyi uygulamalar listelenmektedir:

**En iyi yöntem**: ağ yönlendirmeye ve güvenliğe yanlışlıkla engel olma.   
**Ayrıntı**: yalnızca merkezi ağ grubunun ağ kaynaklarına erişim izni olduğundan emin olmak için RBAC kullanın.

**En iyi yöntem**: "Any" kaynak IP adresinden erişime izin veren, sunulan VM 'leri tanımla ve düzelt.   
**Ayrıntı**: Azure Güvenlik Merkezi 'ni kullanın. Güvenlik Merkezi, ağ güvenlik gruplarınızı "Any" kaynak IP adresinden erişime izin veren bir veya daha fazla gelen kurala sahipse, internet 'e yönelik uç noktalar aracılığıyla erişimi kısıtlamanız önerilir. Güvenlik Merkezi, gerçekte erişmesi gereken kaynak IP adreslerine [erişimi kısıtlamak](../../security-center/security-center-network-recommendations.md) için bu gelen kuralları düzenlemenizi önerir.

**En iyi yöntem**: yönetim bağlantı noktalarını kısıtla (RDP, SSH).   
**Ayrıntı**: [tam zamanında (JIT) VM erişimi](../../security-center/security-center-just-in-time.md) , Azure VM 'lerinize gelen trafiği kilitlemek için kullanılabilir, ancak gerektiğinde VM 'lere bağlanmak için kolay erişim sağlarken saldırılara maruz kalma olasılığını azaltır. JıT etkinleştirildiğinde, güvenlik merkezi bir ağ güvenlik grubu kuralı oluşturarak Azure VM 'lerinize gelen trafiği kilitler. Gelen trafiğin kilitlenebileceği sanal makine üzerindeki bağlantı noktalarını seçersiniz. Bu bağlantı noktaları JıT çözümü tarafından denetlenir.

## <a name="next-steps"></a>Sonraki adımlar
Azure 'u kullanarak bulut çözümlerinizi tasarlarken, dağıttığınızda ve yönetirken en iyi güvenlik uygulamaları için bkz. [Azure Güvenlik en iyi uygulamaları ve desenleri](best-practices-and-patterns.md) .

Aşağıdaki kaynaklar, Azure güvenliği ve ilgili Microsoft hizmetleri hakkında daha genel bilgiler sağlamak için kullanılabilir:
* Azure [güvenlik ekibi blogu](https://blogs.msdn.microsoft.com/azuresecurity/) -Azure güvenliği ile ilgili en son bilgiler için
* [Microsoft Güvenlik](https://technet.microsoft.com/library/dn440717.aspx) açıkları, Azure ile ilgili sorunlar da dahil olmak üzere Microsoft güvenlik açıklarına secure@microsoft.com bildirilebilir veya e-posta ile gönderebilirsiniz.
