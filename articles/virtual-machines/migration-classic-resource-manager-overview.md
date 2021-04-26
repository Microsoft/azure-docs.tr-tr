---
title: IaaS kaynaklarının klasik ile Azure Resource Manager geçişine genel bakış
description: Klasik kaynaklardan Azure Resource Manager ' e kadar olan platform tarafından desteklenen geçişi adım adım inceleyin.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 116e99339ac79e9e6a2de5e7a6222460a71bf4a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102615100"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>IaaS kaynaklarının klasik modelden Azure Resource Manager’a platform destekli geçişi

> [!IMPORTANT]
> Bugün, IaaS VM 'lerinin yaklaşık %90 ' u [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)kullanıyor. 28 Şubat 2020 itibariyle klasik VM 'Ler kullanımdan kaldırılmıştır ve 1 Mart 2023 tarihinde tamamen kullanımdan kaldırılacaktır. Bu kullanımdan kaldırma ve [nasıl etkilediği](classic-vm-deprecation.md#how-does-this-affect-me)hakkında [daha fazla bilgi edinin]( https://aka.ms/classicvmretirement) .



Bu makalede, Azure Service Manager (asm) diğer adıyla Classic 'ten Kaynak Yöneticisi (ARM) dağıtım modelleriyle kaynakları geçirme ve sanal ağ siteden siteye ağ geçitleri kullanılarak aboneliğinizde bulunan iki dağıtım modelinden kaynakları bağlama hakkında genel bakış sunulmaktadır. [Azure Resource Manager özellikleri ve avantajları](../azure-resource-manager/management/overview.md)hakkında daha fazla bilgi edinebilirsiniz. 

ASM iki farklı işlem ürününü destekler, Azure sanal makineleri (klasik) diğer adıyla IaaS VM 'leri, [Azure Cloud Services (klasik)](../cloud-services/index.yml) diğer adıyla PaaS VM 'leri veya Web/çalışan rolleri &. Bu belge yalnızca Azure sanal makinelerini (klasik) geçirme hakkında konuşur.

## <a name="goal-for-migration"></a>Geçiş için hedef
Kaynak Yöneticisi, şablonlar aracılığıyla karmaşık uygulamaların dağıtılmasını, sanal makineleri VM uzantıları kullanarak yapılandırmayı ve erişim yönetimi ve etiketleme özelliklerini içerir. Azure Resource Manager, sanal makineler için kullanılabilirlik kümelerine ölçeklenebilir, paralel dağıtım içerir. Yeni dağıtım modeli ayrıca işlem, ağ ve depolamanın yaşam döngüsü yönetimini bağımsız olarak sağlar. Son olarak, bir sanal ağ içindeki sanal makinelerin zorlanması ile varsayılan olarak güvenliği etkinleştirmeye odaklanılmıştır.

Klasik dağıtım modelinden neredeyse tüm özellikler Azure Resource Manager altında işlem, ağ ve depolama için desteklenir. Azure Resource Manager yeni yeteneklerin avantajlarından yararlanmak için, mevcut dağıtımları klasik dağıtım modelinden geçirebilirsiniz.

## <a name="supported-resources--configurations-for-migration"></a>Geçiş için desteklenen kaynaklar & yapılandırma

### <a name="supported-resources-for-migration"></a>Geçiş için desteklenen kaynaklar
* Sanal Makineler
* Kullanılabilirlik Kümeleri
* Depolama Hesapları
* Sanal Ağlar
* VPN Ağ Geçitleri
* [Hızlı rota ağ geçitleri](../expressroute/expressroute-howto-move-arm.md) _(yalnızca sanal ağ ile aynı abonelikte)_
* Ağ Güvenlik Grupları
* Yönlendirme Tabloları
* Ayrılmış IP’ler

## <a name="supported-configurations-for-migration"></a>Geçiş için desteklenen konfigürasyonlar
Bu klasik IaaS kaynakları geçiş sırasında desteklenir

| Hizmet | Yapılandırma |
| --- | --- |
| Azure AD Domain Services | [Azure AD etki alanı Hizmetleri 'ni içeren sanal ağlar](../active-directory-domain-services/migrate-from-classic-vnet.md) |

## <a name="supported-scopes-of-migration"></a>Desteklenen geçiş kapsamları
İşlem, ağ ve depolama kaynaklarının geçişini tamamlamaya yönelik dört farklı yol vardır:

* [Sanal makinelerin geçişi (sanal bir ağda DEĞIL)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Sanal makinelerin geçişi (bir sanal ağda)](#migration-of-virtual-machines-in-a-virtual-network)
* [Depolama hesaplarının geçirilmesi](#migration-of-storage-accounts)
* [Eklenmemiş kaynakların geçişi](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Sanal makinelerin geçişi (sanal bir ağda DEĞIL)
Kaynak Yöneticisi dağıtım modelinde, varsayılan olarak uygulamalarınız için güvenlik zorlanır. Tüm VM 'Lerin Kaynak Yöneticisi modelinde bir sanal ağda olması gerekir. Azure platformu `Stop` `Deallocate` `Start` geçişin bir parçası olarak VM 'leri yeniden başlatır (, ve). Sanal makinelerin geçirilecek sanal ağlar için iki seçeneğiniz vardır:

* Yeni bir sanal ağ oluşturmak için platformu isteyebilir ve sanal makineyi yeni sanal ağa geçirebilirsiniz.
* Sanal makineyi Kaynak Yöneticisi var olan bir sanal ağa geçirebilirsiniz.

> [!NOTE]
> Bu geçiş kapsamında, geçiş sırasında bir süre boyunca hem yönetim düzlemi işlemlerine hem de veri düzlemi işlemlerine izin verilmiyor olabilir.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Sanal makinelerin geçişi (bir sanal ağda)
Çoğu VM yapılandırması için, yalnızca meta veriler klasik ve Kaynak Yöneticisi dağıtım modelleri arasında geçiş yapılır. Temel alınan VM 'Ler aynı donanımda, aynı ağda ve aynı depolamada çalışmaktadır. Geçiş sırasında belirli bir süre için yönetim düzlemi işlemlerine izin verilmiyor olabilir. Ancak, veri düzlemi çalışmaya devam eder. Diğer bir deyişle, VM 'lerde çalışan uygulamalarınızın (klasik) geçiş sırasında kapalı kalma süresi yoktur.

Aşağıdaki yapılandırma Şu anda desteklenmiyor. Daha sonra destek eklenirse, Bu yapılandırmadaki bazı sanal makineler kapalı kalma süresine yol açabilir (durdurma, serbest bırakma ve VM işlemlerini yeniden başlatma).

* Tek bir bulut hizmetinde birden fazla kullanılabilirlik kümesi var.
* Tek bir bulut hizmetinde kullanılabilirlik kümesinde olmayan bir veya daha fazla kullanılabilirlik kümesine ve VM 'ye sahipsiniz.

> [!NOTE]
> Bu geçiş kapsamında, geçiş sırasında bir süre için yönetim düzlemine izin verilmiyor olabilir. Daha önce açıklandığı gibi belirli yapılandırmalarda, veri düzlemi kapalı kalma süresi oluşur.
>

### <a name="migration-of-storage-accounts"></a>Depolama hesaplarının geçirilmesi
Kesintisiz geçişe izin vermek için Kaynak Yöneticisi VM 'Leri klasik bir depolama hesabında dağıtabilirsiniz. Bu özellik ile, işlem ve ağ kaynakları depolama hesaplarından bağımsız olarak geçirilebilirler. Sanal makinelerinizi ve Sanal ağınızı geçirdikten sonra, geçiş işlemini gerçekleştirmek için depolama hesaplarınızın üzerine geçiş yapmanız gerekir.

Depolama hesabınızda ilişkili disk veya sanal makine verisi yoksa ve yalnızca Bloblar, dosyalar, tablolar ve kuyruklar varsa Azure Resource Manager geçişi bağımlılıklar olmadan tek başına geçiş olarak yapılabilir.

> [!NOTE]
> Kaynak Yöneticisi dağıtım modelinde klasik görüntü ve disk kavramı yoktur. Depolama hesabı geçirildiğinde, klasik görüntüler ve diskler Kaynak Yöneticisi yığınında görünmez, ancak yedekleme VHD 'leri depolama hesabında kalır.

Aşağıdaki ekran görüntüleri Azure portal kullanarak klasik bir depolama hesabını Azure Resource Manager depolama hesabına nasıl yükselteceğiniz göstermektedir:
1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Depolama hesabınıza gidin.
3. **Ayarlar** bölümünde **ARM 'ye geçir ' e** tıklayın.
4. Geçiş olasılığı 'nı öğrenmek için **Doğrula** 'ya tıklayın.
5. Doğrulama başarılı olursa, geçirilen bir depolama hesabı oluşturmak için **hazırla** ' ya tıklayın.
6. Geçişi onaylamak için **Evet** yazın ve geçişi tamamlamak için **Yürüt** ' e tıklayın.

    ![Depolama hesabını doğrula](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Depolama hesabını hazırlama](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Depolama hesabı geçişini sonlandırma](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Eklenmemiş kaynakların geçişi
İlişkili diskleri olmayan depolama hesapları veya sanal makine verileri bağımsız olarak geçirilebilir.

Ağ güvenlik grupları, rota tabloları, herhangi bir sanal makineye bağlı olmayan ayrılmış IP 'Leri & ve sanal ağlar bağımsız olarak da geçirilebilirler.

<br>

## <a name="unsupported-features-and-configurations"></a>Desteklenmeyen özellikler ve yapılandırmalar
Bazı özellikler ve Konfigürasyonlar Şu anda desteklenmiyor; Aşağıdaki bölümlerde bunların çevresindeki önerileriniz açıklanır.

### <a name="unsupported-features"></a>Desteklenmeyen özellikler
Aşağıdaki özellikler Şu anda desteklenmemektedir. İsteğe bağlı olarak bu ayarları kaldırabilir, VM 'Leri geçirebilir ve sonra Kaynak Yöneticisi dağıtım modelindeki ayarları yeniden etkinleştirebilirsiniz.

| Kaynak sağlayıcısı | Özellik | Öneri |
| --- | --- | --- |
| İşlem | İlişkilendirilmemiş sanal makine diskleri. | Depolama hesabı geçirildiğinde bu disklerin arkasındaki VHD blob 'ları geçirilir |
| İşlem | Sanal makine görüntüleri. | Depolama hesabı geçirildiğinde bu disklerin arkasındaki VHD blob 'ları geçirilir |
| Ağ | Uç nokta ACL’leri. | Uç nokta ACL 'Lerini kaldırın ve geçişi yeniden deneyin. |
| Ağ | Application Gateway | Geçişe başlamadan önce Application Gateway kaldırın ve ardından geçiş tamamlandıktan sonra Application Gateway yeniden oluşturun. |
| Ağ | VNet eşlemesi kullanan sanal ağlar. | Sanal ağı Kaynak Yöneticisi ve ardından eşe geçirin. [VNET eşlemesi](../virtual-network/virtual-network-peering-overview.md)hakkında daha fazla bilgi edinin. |

### <a name="unsupported-configurations"></a>Desteklenmeyen yapılandırmalar
Aşağıdaki yapılandırma Şu anda desteklenmiyor.

| Hizmet | Yapılandırma | Öneri |
| --- | --- | --- |
| Resource Manager |Klasik kaynaklar için Role-Based Access Control (RBAC) |Kaynak URI 'SI geçişten sonra değiştirildiğinden, geçişten sonra gerçekleşmesi gereken RBAC ilke güncelleştirmelerini planlamanız önerilir. |
| İşlem |Bir VM ile ilişkili birden çok alt ağ |Alt ağ yapılandırmasını yalnızca bir alt ağa başvuracak şekilde güncelleştirin. Bu, VM 'den bir ikincil NIC (başka bir alt ağa başvuran) kaldırmanızı ve geçiş tamamlandıktan sonra yeniden iliştirmesini gerektirebilir. |
| İşlem |Bir sanal ağa ait olan ancak açık bir alt ağa sahip olmayan sanal makineler |İsteğe bağlı olarak VM 'yi silebilirsiniz. |
| İşlem |Uyarıları olan sanal makineler, otomatik ölçeklendirme ilkeleri |Geçiş aşamasından geçer ve bu ayarlar bırakılır. Geçiş yapmadan önce ortamınızı değerlendirmeniz kesinlikle önerilir. Alternatif olarak, geçiş tamamlandıktan sonra uyarı ayarlarını yeniden yapılandırabilirsiniz. |
| İşlem |XML VM uzantıları (BgInfo 1. *, Visual Studio hata ayıklayıcısı, Web Dağıtımı ve uzaktan hata ayıklama) |Bu özellik desteklenmez. Geçişe devam etmek için bu uzantıları sanal makineden kaldırmanız önerilir veya geçiş işlemi sırasında otomatik olarak bırakılır. |
| İşlem |Premium Depolama ile önyükleme tanılaması |Geçişe devam etmeden önce VM 'Ler için önyükleme Tanılama özelliğini devre dışı bırakın. Geçiş tamamlandıktan sonra Kaynak Yöneticisi yığınında önyükleme tanılamayı yeniden etkinleştirebilirsiniz. Ayrıca, bu Bloblar için artık ücretlendirilmemek üzere ekran görüntüsü ve seri günlükleri için kullanılan Blobların silinmesi gerekir. |
| İşlem | Web/çalışan rollerini içeren bulut Hizmetleri | Bu şu anda desteklenmiyor. |
| İşlem | Birden fazla kullanılabilirlik kümesi veya birden çok kullanılabilirlik kümesi içeren bulut hizmetleri. |Bu şu anda desteklenmiyor. Lütfen geçirmeden önce sanal makineleri aynı Kullanılabilirlik kümesine taşıyın. |
| İşlem | Azure Güvenlik Merkezi uzantısı olan VM | Azure Güvenlik Merkezi, güvenliğini izlemek ve uyarıları yükseltmek için sanal makinelerinizdeki uzantıları otomatik olarak kurar. Bu uzantılar genellikle abonelikte Azure Güvenlik Merkezi ilkesi etkinse otomatik olarak yüklenir. Sanal makineleri geçirmek için, abonelikteki Güvenlik Merkezi ilkesini devre dışı bırakın ve bu, güvenlik merkezi izleme uzantısını sanal makinelerden kaldırır. |
| İşlem | Yedekleme veya anlık görüntü uzantısına sahip VM | Bu uzantılar Azure Backup hizmetiyle yapılandırılmış bir sanal makineye yüklenir. Bu VM 'lerin geçişi desteklenirken, geçişten önce alınmış yedeklemeleri tutmak için [buradaki](./migration-classic-resource-manager-faq.md#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault) yönergeleri izleyin.  |
| İşlem | Azure Site Recovery uzantılı VM | Bu uzantılar Azure Site Recovery hizmetiyle yapılandırılmış bir sanal makineye yüklenir. Site Recovery ile kullanılan depolama alanı geçişi çalışır, ancak geçerli çoğaltma etkilenecek. Depolama geçişten sonra VM çoğaltmasını devre dışı bırakıp etkinleştirmeniz gerekir. |
| Ağ |Sanal makineler ve web/çalışan rolleri içeren sanal ağlar |Bu şu anda desteklenmiyor. Lütfen geçirmeden önce web/çalışan rollerini kendi sanal ağına taşıyın. Klasik sanal ağ geçirildikten sonra geçirilmiş Azure Resource Manager sanal ağı, daha önce olduğu gibi benzer yapılandırmalar elde etmek için klasik sanal ağla eşlenebilir.|
| Ağ | Klasik Express Route devreleri |Bu şu anda desteklenmiyor. IaaS geçişine başlamadan önce bu devrelerin Azure Resource Manager geçirilmesi gerekir. Daha fazla bilgi edinmek için bkz. [ExpressRoute devreleri klasik 'dan Kaynak Yöneticisi dağıtım modeline taşıma](../expressroute/expressroute-move.md).|
| Azure App Service |App Service ortamları içeren sanal ağlar |Bu şu anda desteklenmiyor. |
| Azure HDInsight |HDInsight hizmetlerini içeren sanal ağlar |Bu şu anda desteklenmiyor. |
| Microsoft Dynamics yaşam döngüsü Hizmetleri |Dynamics yaşam döngüsü Hizmetleri tarafından yönetilen sanal makineleri içeren sanal ağlar |Bu şu anda desteklenmiyor. |
| Azure API Management |Azure API Management dağıtımlarını içeren sanal ağlar |Bu şu anda desteklenmiyor. IaaS VNET 'i geçirmek için, kesinti süresi olmayan bir işlem olan API Management dağıtımının VNET 'i değiştirin. |

## <a name="next-steps"></a>Sonraki adımlar

* [Klasik modelden Azure Resource Manager’a platform destekli geçişe ayrıntılı teknik bakış](migration-classic-resource-manager-deep-dive.md)
* [IaaS kaynaklarının Klasik’ten Azure Resource Manager’a geçişini planlama](migration-classic-resource-manager-plan.md)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için PowerShell 'i kullanma](migration-classic-resource-manager-ps.md)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için CLı kullanma](migration-classic-resource-manager-cli.md)
* [IaaS kaynaklarının klasik 'dan Azure Resource Manager geçişine yardımcı olacak topluluk araçları](migration-classic-resource-manager-community-tools.md)
* [En sık karşılaşılan geçiş hatalarını gözden geçirme](migration-classic-resource-manager-errors.md)
* [IaaS kaynaklarını klasik konumundan Azure Resource Manager geçirme hakkında en sık sorulan soruları gözden geçirin](migration-classic-resource-manager-faq.md)
