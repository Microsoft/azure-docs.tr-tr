---
title: include dosyası
description: include dosyası
services: virtual-machines
author: singhkays
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: kasing
ms.custom: include file
ms.openlocfilehash: 40da2016026c8a7e02d1b243a783d01559e8c197
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005520"
---
Bu makalede, bir hizmet olarak altyapı (IaaS) kaynaklarını klasik ' ten Kaynak Yöneticisi dağıtım modelleriyle geçirme ve sanal ağ kullanarak aboneliğinizde bulunan iki dağıtım modelinden kaynakları bağlama ayrıntıları açıklanmaktadır siteden siteye ağ geçitleri. [Azure Resource Manager özellikleri ve avantajları](../articles/azure-resource-manager/resource-group-overview.md)hakkında daha fazla bilgi edinebilirsiniz. 

## <a name="goal-for-migration"></a>Geçiş için hedef
Kaynak Yöneticisi, şablonlar aracılığıyla karmaşık uygulamaların dağıtılmasını, sanal makineleri VM uzantıları kullanarak yapılandırmayı ve erişim yönetimi ve etiketleme özelliklerini içerir. Azure Resource Manager, sanal makineler için kullanılabilirlik kümelerine ölçeklenebilir, paralel dağıtım içerir. Yeni dağıtım modeli ayrıca işlem, ağ ve depolamanın yaşam döngüsü yönetimini bağımsız olarak sağlar. Son olarak, bir sanal ağ içindeki sanal makinelerin zorlanması ile varsayılan olarak güvenliği etkinleştirmeye odaklanılmıştır.

Klasik dağıtım modelinden neredeyse tüm özellikler Azure Resource Manager altında işlem, ağ ve depolama için desteklenir. Azure Resource Manager yeni yeteneklerin avantajlarından yararlanmak için, mevcut dağıtımları klasik dağıtım modelinden geçirebilirsiniz.

## <a name="supported-resources-for-migration"></a>Geçiş için desteklenen kaynaklar
Bu klasik IaaS kaynakları geçiş sırasında desteklenir

* Virtual Machines
* Kullanılabilirlik Kümeleri
* Bulut Hizmetleri ve Sanal Makineler
* Depolama Hesapları
* Sanal Ağlar
* VPN Ağ Geçitleri
* Hızlı rota ağ geçitleri _(yalnızca sanal ağ ile aynı abonelikte)_
* Ağ Güvenlik Grupları
* Yönlendirme Tabloları
* Ayrılmış IP’ler

## <a name="supported-scopes-of-migration"></a>Desteklenen geçiş kapsamları
İşlem, ağ ve depolama kaynaklarının geçişini tamamlamaya yönelik dört farklı yol vardır:

* [Sanal makinelerin geçişi (sanal bir ağda DEĞIL)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Sanal makinelerin geçişi (bir sanal ağda)](#migration-of-virtual-machines-in-a-virtual-network)
* [Depolama hesaplarının geçirilmesi](#migration-of-storage-accounts)
* [Eklenmemiş kaynakların geçişi](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Sanal makinelerin geçişi (sanal bir ağda DEĞIL)
Kaynak Yöneticisi dağıtım modelinde, varsayılan olarak uygulamalarınız için güvenlik zorlanır. Tüm VM 'Lerin Kaynak Yöneticisi modelinde bir sanal ağda olması gerekir. Azure platformu, geçişin bir parçası olarak VM 'Leri yeniden başlatır (`Stop`, `Deallocate`ve `Start`). Sanal makinelerin geçirilecek sanal ağlar için iki seçeneğiniz vardır:

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
3. **Ayarlar** bölümünde **ARM 'ye geçir ' e**tıklayın.
4. Geçiş olasılığı 'nı öğrenmek için **Doğrula** 'ya tıklayın.
5. Doğrulama başarılı olursa, geçirilen bir depolama hesabı oluşturmak için **hazırla** ' ya tıklayın.
6. Geçişi onaylamak için **Evet** yazın ve geçişi tamamlamak için **Yürüt** ' e tıklayın.

    ![Depolama hesabını doğrula](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Depolama hesabını hazırlama](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Depolama hesabı geçişini sonlandırma](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

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
| Ağ | Uç nokta ACL 'Leri. | Uç nokta ACL 'Lerini kaldırın ve geçişi yeniden deneyin. |
| Ağ | Application Gateway | Geçişe başlamadan önce Application Gateway kaldırın ve ardından geçiş tamamlandıktan sonra Application Gateway yeniden oluşturun. |
| Ağ | VNet eşlemesi kullanan sanal ağlar. | Sanal ağı Kaynak Yöneticisi ve ardından eşe geçirin. [VNET eşlemesi](../articles/virtual-network/virtual-network-peering-overview.md)hakkında daha fazla bilgi edinin. |

### <a name="unsupported-configurations"></a>Desteklenmeyen yapılandırmalar
Aşağıdaki yapılandırma Şu anda desteklenmiyor.

| Hizmet | Yapılandırma | Öneri |
| --- | --- | --- |
| Resource Manager |Klasik kaynaklar için rol tabanlı Access Control (RBAC) |Kaynak URI 'SI geçişten sonra değiştirildiğinden, geçişten sonra gerçekleşmesi gereken RBAC ilke güncelleştirmelerini planlamanız önerilir. |
| İşlem |Bir VM ile ilişkili birden çok alt ağ |Alt ağ yapılandırmasını yalnızca bir alt ağa başvuracak şekilde güncelleştirin. Bu, VM 'den bir ikincil NIC (başka bir alt ağa başvuran) kaldırmanızı ve geçiş tamamlandıktan sonra yeniden iliştirmesini gerektirebilir. |
| İşlem |Bir sanal ağa ait olan ancak açık bir alt ağa sahip olmayan sanal makineler |İsteğe bağlı olarak VM 'yi silebilirsiniz. |
| İşlem |Uyarıları olan sanal makineler, otomatik ölçeklendirme ilkeleri |Geçiş aşamasından geçer ve bu ayarlar bırakılır. Geçiş yapmadan önce ortamınızı değerlendirmeniz kesinlikle önerilir. Alternatif olarak, geçiş tamamlandıktan sonra uyarı ayarlarını yeniden yapılandırabilirsiniz. |
| İşlem |XML VM uzantıları (BgInfo 1. *, Visual Studio hata ayıklayıcısı, Web Dağıtımı ve uzaktan hata ayıklama) |Bu desteklenmez. Geçişe devam etmek için bu uzantıları sanal makineden kaldırmanız önerilir veya geçiş işlemi sırasında otomatik olarak bırakılır. |
| İşlem |Premium Depolama ile önyükleme tanılaması |Geçişe devam etmeden önce VM 'Ler için önyükleme Tanılama özelliğini devre dışı bırakın. Geçiş tamamlandıktan sonra Kaynak Yöneticisi yığınında önyükleme tanılamayı yeniden etkinleştirebilirsiniz. Ayrıca, bu Bloblar için artık ücretlendirilmemek üzere ekran görüntüsü ve seri günlükleri için kullanılan Blobların silinmesi gerekir. |
| İşlem | Web/çalışan rollerini içeren bulut Hizmetleri | Bu şu anda desteklenmiyor. |
| İşlem | Birden fazla kullanılabilirlik kümesi veya birden çok kullanılabilirlik kümesi içeren bulut hizmetleri. |Bu şu anda desteklenmiyor. Lütfen geçirmeden önce sanal makineleri aynı Kullanılabilirlik kümesine taşıyın. |
| İşlem | Azure Güvenlik Merkezi uzantısı olan VM | Azure Güvenlik Merkezi, güvenliğini izlemek ve uyarıları yükseltmek için sanal makinelerinizdeki uzantıları otomatik olarak kurar. Bu uzantılar genellikle abonelikte Azure Güvenlik Merkezi ilkesi etkinse otomatik olarak yüklenir. Sanal makineleri geçirmek için, abonelikteki Güvenlik Merkezi ilkesini devre dışı bırakın ve bu, güvenlik merkezi izleme uzantısını sanal makinelerden kaldırır. |
| İşlem | Yedekleme veya anlık görüntü uzantısına sahip VM | Bu uzantılar Azure Backup hizmetiyle yapılandırılmış bir sanal makineye yüklenir. Bu VM 'lerin geçişi desteklenirken, geçişten önce alınmış yedeklemeleri tutmak için [buradaki](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault) yönergeleri izleyin.  |
| Ağ |Sanal makineler ve web/çalışan rolleri içeren sanal ağlar |Bu şu anda desteklenmiyor. Lütfen geçirmeden önce web/çalışan rollerini kendi sanal ağına taşıyın. Klasik sanal ağ geçirildikten sonra geçirilmiş Azure Resource Manager sanal ağı, daha önce olduğu gibi benzer yapılandırmalar elde etmek için klasik sanal ağla eşlenebilir.|
| Ağ | Klasik Express Route devreleri |Bu şu anda desteklenmiyor. IaaS geçişine başlamadan önce bu devrelerin Azure Resource Manager geçirilmesi gerekir. Daha fazla bilgi edinmek için bkz. [ExpressRoute devreleri klasik 'dan Kaynak Yöneticisi dağıtım modeline taşıma](../articles/expressroute/expressroute-move.md).|
| Azure uygulama hizmeti |App Service ortamları içeren sanal ağlar |Bu şu anda desteklenmiyor. |
| Azure HDInsight |HDInsight hizmetlerini içeren sanal ağlar |Bu şu anda desteklenmiyor. |
| Microsoft Dynamics yaşam döngüsü Hizmetleri |Dynamics yaşam döngüsü Hizmetleri tarafından yönetilen sanal makineleri içeren sanal ağlar |Bu şu anda desteklenmiyor. |
| Azure AD Domain Services |Azure AD etki alanı Hizmetleri 'ni içeren sanal ağlar |Bu şu anda desteklenmiyor. |
| Azure API Management |Azure API Management dağıtımlarını içeren sanal ağlar |Bu şu anda desteklenmiyor. IaaS VNET 'i geçirmek için, kesinti süresi olmayan bir işlem olan API Management dağıtımının VNET 'i değiştirin. |
