---
title: Klasik bir sanal ağdan Azure AD Domain Services geçirme | Microsoft Docs
description: Klasik sanal ağ modelinden var olan Azure AD Domain Services yönetilen bir etki alanı örneğini Kaynak Yöneticisi tabanlı bir sanal ağa geçirmeyi öğrenin.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: iainfou
ms.openlocfilehash: 8cba2cbf8fcbad1acae8c36892308c3249fc4181
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/20/2019
ms.locfileid: "72674911"
---
# <a name="preview---migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Önizleme-Azure AD Domain Services klasik sanal ağ modelinden Kaynak Yöneticisi 'e geçirin

Azure Active Directory Domain Services (AD DS), şu anda klasik sanal ağ modelini kullanan müşterilerin Kaynak Yöneticisi sanal ağ modeline tek seferlik bir taşımayı destekler.

Bu makalede, var olan bir Azure AD DS örneğini başarılı bir şekilde geçirmek için gereken adımlar ve geçiş konuları özetlenmektedir. Bu özellik şu anda önizleme sürümündedir.

## <a name="overview-of-the-migration-process"></a>Geçiş işlemine genel bakış

Geçiş işlemi, klasik bir sanal ağda çalışan mevcut bir Azure AD DS örneğini alır ve var olan bir Kaynak Yöneticisi sanal ağa taşıır. Geçiş, PowerShell kullanılarak gerçekleştirilir ve iki ana yürütme- *hazırlama* ve *geçiş*aşamasına sahiptir.

![Azure AD DS için geçiş işlemine genel bakış](media/migrate-from-classic-vnet/migration-overview.png)

*Hazırlama* aşamasında Azure AD DS, kullanıcıların, grupların ve parolaların yönetilen etki alanıyla eşitlenmiş en son anlık görüntüsünü almak için etki alanının bir yedeğini alır. Eşitleme daha sonra devre dışıdır ve Azure AD DS yönetilen etki alanını barındıran bulut hizmeti silinir. Hazırlama aşamasında Azure AD DS yönetilen etki alanı, kullanıcıların kimliğini doğrulayamadı.

![Azure AD DS geçişine yönelik hazırlık aşaması](media/migrate-from-classic-vnet/migration-preparation.png)

*Geçiş* aşamasında, klasik Azure AD DS yönetilen etki alanındaki etki alanı denetleyicileri için temel sanal diskler, Kaynak Yöneticisi dağıtım modelini kullanarak VM 'ler oluşturmak için kopyalanır. Azure AD DS yönetilen etki alanı daha sonra, LDAPS ve DNS yapılandırmasını içeren yeniden oluşturulur. Azure AD ile eşitleme yeniden başlatılır ve LDAP sertifikaları geri yüklenir. Tüm makineleri Azure AD DS yönetilen bir etki alanına yeniden eklemeye gerek yoktur. Bunlar yönetilen etki alanına katılmaları ve değişiklik yapılmadan çalıştırırlar.

![Azure AD DS geçişi](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>Geçiş avantajları

Bu geçiş işlemini kullanarak Azure AD DS yönetilen bir etki alanını taşıdığınızda, makineleri yönetilen etki alanına yeniden ekleme veya Azure AD DS örneğini silme ve sıfırdan bir tane oluşturma ihtiyacını ortadan kaldırma. VM 'Ler, geçiş işleminin sonunda Azure AD DS tarafından yönetilen etki alanına katılalmaya devam eder.

Geçişten sonra Azure AD DS, yalnızca Kaynak Yöneticisi sanal ağları kullanan etki alanları için kullanılabilen birçok özellik sağlar; örneğin:

* Hassas parola ilkesi desteği.
* AD hesabı kilitleme koruması.
* Azure AD DS yönetilen etki alanındaki uyarıların e-posta bildirimleri.
* Azure Izleyici kullanarak günlükleri denetleyin.
* Azure dosyaları tümleştirmesi
* HD Insights tümleştirmesi

Kaynak Yöneticisi sanal ağ kullanan Azure AD DS yönetilen etki alanları, en son yeni özelliklerle güncel kalmanıza yardımcı olur. Klasik sanal ağları kullanarak Azure AD DS desteği gelecekte kullanım dışı olacaktır.

## <a name="example-scenarios-for-migration"></a>Geçiş için örnek senaryolar

Azure AD DS yönetilen bir etki alanını geçirmeye yönelik bazı yaygın senaryolar aşağıdaki örnekleri içerir.

> [!NOTE]
> Başarılı bir geçiş işlemi onaylayapana kadar klasik sanal ağı dönüştürmeyin. Geçiş ve doğrulama aşamaları sırasında herhangi bir sorun oluşursa, sanal ağın dönüştürülmesi, Azure AD DS yönetilen etki alanını geri alma veya geri yükleme seçeneğini kaldırır.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Azure AD DS 'yi var olan bir Kaynak Yöneticisi sanal ağa geçirme (önerilir)

Yaygın bir senaryo, var olan klasik kaynakları bir Kaynak Yöneticisi dağıtım modeline ve sanal ağa zaten taşıdığınız yerdir. Eşleme daha sonra Kaynak Yöneticisi sanal ağından Azure AD DS çalıştırmaya devam eden klasik sanal ağa kullanılır. Bu yaklaşım, Kaynak Yöneticisi uygulama ve hizmetlerin klasik sanal ağda Azure AD DS yönetilen etki alanının kimlik doğrulama ve yönetim işlevlerini kullanmasına olanak sağlar. Geçiş yapıldıktan sonra, tüm kaynaklar Kaynak Yöneticisi dağıtım modelini ve sanal ağı kullanarak çalışır.

![Azure AD DS var olan bir Kaynak Yöneticisi sanal ağa geçirme](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

Bu örnek geçiş senaryosuna dahil olan üst düzey adımlar aşağıdaki bölümleri içerir:

1. Klasik sanal ağda yapılandırılmış mevcut VPN ağ geçitlerini veya sanal ağ eşlemesini kaldırın.
1. Azure AD DS yönetilen etki alanını bu makalede açıklanan adımları kullanarak geçirin.
1. Başarılı bir geçişi test edin ve onaylayın, sonra klasik sanal ağı silin.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Azure AD DS dahil olmak üzere birden çok kaynak geçirme

Bu örnek senaryoda, Azure AD DS ve diğer ilişkili kaynakları klasik dağıtım modelinden Kaynak Yöneticisi dağıtım modeline geçirolursunuz. Bazı kaynaklar klasik sanal ağda Azure AD DS yönetilen etki alanı ile birlikte çalışmaya devam ediyorsanız, Kaynak Yöneticisi dağıtım modeline geçiş özelliğinden faydalanabilir.

![Birden çok kaynağı Kaynak Yöneticisi dağıtım modeline geçirme](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

Bu örnek geçiş senaryosuna dahil olan üst düzey adımlar aşağıdaki bölümleri içerir:

1. Klasik sanal ağda yapılandırılmış mevcut VPN ağ geçitlerini veya sanal ağ eşlemesini kaldırın.
1. Azure AD DS yönetilen etki alanını bu makalede açıklanan adımları kullanarak geçirin.
1. Klasik sanal ağ ile Kaynak Yöneticisi ağı arasında sanal ağ eşlemesi ayarlayın.
1. Başarılı bir geçişi test edin ve onaylayın.
1. [VM 'ler gibi ek klasik kaynakları taşıyın][migrate-iaas].

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Azure AD DS geçirme, ancak diğer kaynakları klasik sanal ağda tutma

Bu örnek senaryoyla, bir oturumda en düşük kesinti süresine sahip olursunuz. Yalnızca Azure AD DS bir Kaynak Yöneticisi sanal ağa geçirin ve mevcut kaynakları klasik dağıtım modelinde ve sanal ağda saklayın. Aşağıdaki bakım döneminde, ek kaynakları klasik dağıtım modelinden ve sanal ağdan istediğiniz gibi geçirebilirsiniz.

![Yalnızca Azure AD DS Kaynak Yöneticisi dağıtım modeline geçir](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

Bu örnek geçiş senaryosuna dahil olan üst düzey adımlar aşağıdaki bölümleri içerir:

1. Klasik sanal ağda yapılandırılmış mevcut VPN ağ geçitlerini veya sanal ağ eşlemesini kaldırın.
1. Azure AD DS yönetilen etki alanını bu makalede açıklanan adımları kullanarak geçirin.
1. Klasik sanal ağ ile yeni Kaynak Yöneticisi sanal ağ arasında sanal ağ eşlemesi ayarlayın.
1. Daha sonra, [ek kaynakları][migrate-iaas] klasik sanal ağdan gerektiği gibi geçirin.

## <a name="before-you-begin"></a>Başlamadan önce

Azure AD DS yönetilen bir etki alanını hazırlarken ve geçirdikten sonra, kimlik doğrulama ve yönetim hizmetlerinin kullanılabilirliği konusunda bazı konular vardır. Azure AD DS yönetilen etki alanı, geçiş sırasında bir süre için kullanılamaz. Geçiş sırasında Azure AD DS deneyimlerine bağlı olan uygulama ve hizmetler.

> [!IMPORTANT]
> Geçiş işlemine başlamadan önce bu geçiş makalesini ve kılavuzunu okuyun. Geçiş işlemi, Azure AD DS etki alanı denetleyicilerinin zaman aralıklarında kullanılabilirliğini etkiler. Kullanıcılar, hizmetler ve uygulamalar geçiş işlemi sırasında yönetilen etki alanında kimlik doğrulaması yapamaz.

### <a name="ip-addresses"></a>IP adresleri

Azure AD DS yönetilen bir etki alanı için etki alanı denetleyicisi IP adresleri geçişten sonra değişir. Bu, Güvenli LDAP uç noktası için genel IP adresini içerir. Yeni IP adresleri, Kaynak Yöneticisi sanal ağındaki yeni alt ağın adres aralığının içindedir.

Geri alma durumunda IP adresleri geri alındıktan sonra değişebilir.

Azure AD DS, genellikle adres aralığındaki kullanılabilir ilk iki IP adresini kullanır, ancak bu garanti edilmez. Şu anda geçişten sonra kullanılacak IP adreslerini belirtemezsiniz.

### <a name="downtime"></a>Aksak

Geçiş işlemi, etki alanı denetleyicilerinin bir süre çevrimdışı olmasını içerir. Azure AD DS Kaynak Yöneticisi dağıtım modeline ve sanal ağa geçirildiğinde etki alanı denetleyicilerine erişilemez. Ortalama süre kapalı kalma süresi 1 ile 3 saat arasında. Bu süre, etki alanı denetleyicilerinin ilk etki alanı denetleyicisinin yeniden çevrimiçi duruma geldiği sırada çevrimdışına alındığı zamana göre belirlenir. Bu ortalama, ikinci etki alanı denetleyicisinin çoğaltılması için geçen süreyi veya Kaynak Yöneticisi dağıtım modeline ek kaynakların geçirilmesi için gereken süreyi içermez.

### <a name="account-lockout"></a>Hesap kilitleme

Klasik sanal ağlarda çalışan Azure AD DS tarafından yönetilen etki alanları, AD hesabı kilitleme ilkeleri yerinde değildir. VM 'Ler Internet 'e sunulmaya çalışıyorsa, saldırganlar, kendi hesaplarına bir yol sağlamak için parola spreyi yöntemlerini kullanabilir. Bu girişimleri durdurmak için hesap kilitleme ilkesi yoktur. Kaynak Yöneticisi dağıtım modelini ve sanal ağları kullanan Azure AD DS yönetilen etki alanları için AD hesabı kilitleme ilkeleri bu parola spreyi saldırılarına karşı koruma altına alın.

Varsayılan olarak, 5 hatalı parola 30 dakikalık bir hesabı kilitleyerek 2 dakika sonra çalışır.

Kilitli bir hesap, ' de oturum açamaz ve bu, Azure AD DS yönetilen etki alanını veya hesap tarafından yönetilen uygulamaları yönetme özelliğini kesintiye uğratabilecek. Azure AD DS yönetilen bir etki alanı geçirildikten sonra, oturum açma başarısız girişimleri nedeniyle hesaplar kalıcı kilitleme gibi ne kadar gerçekçi bir kilit olabilir. Geçişten sonraki iki yaygın senaryo şunları içerir:

* Son kullanma parolası olan bir hizmet hesabı.
    * Hizmet hesabı, zaman aşımına uğradı ve hesabı kilitleyen bir süre sonra oturum açmayı dener. Bu hatayı onarmak için, kimlik bilgilerinin dolması ve parolayı güncelleştirmek üzere uygulamayı veya VM 'yi bulun.
* Kötü amaçlı bir varlık, hesaplar üzerinde oturum açmaya yönelik deneme yanılma girişimlerini kullanıyor.
    * VM 'Ler internet 'e sunulduklarında, saldırganlar genellikle imzalamayı denediğinde ortak Kullanıcı adı ve parola birleşimlerini dener. Bu yinelenen başarısız oturum açma girişimleri hesapların kilidini açabilir. Yönetim hesaplarının kilitlenmesinden en aza indirmek için yönetici veya *yönetici* *gibi genel* adlarla yönetici hesaplarının kullanılması önerilmez.
    * İnternet 'e açık olan sanal makinelerin sayısını en aza indirin. Azure portal kullanarak VM 'lere güvenli bir şekilde bağlanmak için [Azure savunma (Şu anda önizlemede)][azure-bastion] kullanabilirsiniz.

Geçişten sonra bazı hesapların kilitlendiğini kuşkulanıyorsanız, son geçiş adımları denetimi nasıl etkinleştireceğinizi veya hassas parola ilkesi ayarlarını değiştirmeyi özetler.

### <a name="roll-back-and-restore"></a>Geri alma ve geri yükleme

Geçiş başarılı olmazsa, Azure AD DS yönetilen bir etki alanını geri alma veya geri yükleme işlemi yapılır. Geri alma, geçiş girişiminden önce, yönetilen etki alanının durumunu hemen döndürmek için bir self servis seçenektir. Azure destek mühendisleri, yönetilen bir etki alanını son çare olarak yedekten da geri yükleyebilir. Daha fazla bilgi için bkz. [başarısız geçişten geri alma veya geri yükleme](#roll-back-and-restore-from-migration).

### <a name="restrictions-on-available-virtual-networks"></a>Kullanılabilir sanal ağlarda kısıtlamalar

Sanal ağlarda Azure AD DS yönetilen bir etki alanının geçirilebileceğini gösteren bazı kısıtlamalar vardır. Hedef Kaynak Yöneticisi sanal ağı aşağıdaki gereksinimlere uymalıdır:

* Kaynak Yöneticisi sanal ağ, Azure AD DS 'in Şu anda dağıtıldığı klasik sanal ağla aynı Azure aboneliğinde olmalıdır.
* Kaynak Yöneticisi sanal ağ, Azure AD DS 'in Şu anda dağıtıldığı klasik sanal ağ ile aynı bölgede olmalıdır.
* Kaynak Yöneticisi sanal ağın alt ağının en az 3-5 kullanılabilir IP adresi olmalıdır.
* Kaynak Yöneticisi sanal ağın alt ağı, Azure AD DS için ayrılmış bir alt ağ olmalıdır ve diğer iş yüklerini barındırmamalıdır.

Sanal ağ gereksinimleri hakkında daha fazla bilgi için bkz. [sanal ağ tasarımı konuları ve yapılandırma seçenekleri][network-considerations].

## <a name="migration-steps"></a>Geçiş adımları

Kaynak Yöneticisi dağıtım modeline ve sanal ağa geçiş, 5 ana adıma bölünür:

| Adım    | Üzerinde gerçekleştirilen  | Tahmini süre  | Aksak  | Geri alma/geri yükleme? |
|---------|--------------------|-----------------|-----------|-------------------|
| [1. adım-yeni sanal ağı güncelleştirme ve bulma](#update-and-verify-virtual-network-settings) | Azure portal | 15 dakika | Kesinti süresi gerekli değildir | Yok |
| [2. adım-geçiş için Azure AD DS yönetilen etki alanını hazırlama](#prepare-the-managed-domain-for-migration) | PowerShell | 15 – ortalama 30 dakika | Azure AD DS kapalı kalma süresi bu komut tamamlandıktan sonra başlar. | Geri alma ve geri yükleme var. |
| [3. adım-Azure AD DS yönetilen etki alanını mevcut bir sanal ağa taşıma](#migrate-the-managed-domain) | PowerShell | 1 – 3 saat (Ortalama) | Bu komut tamamlandığında bir etki alanı denetleyicisi kullanılabilir, kapalı kalma süresi sona erer. | Hata durumunda hem geri alma (self servis) hem de geri yükleme kullanılabilir. |
| [4. Adım-çoğaltma etki alanı denetleyicisi için test ve bekleme](#test-and-verify-connectivity-after-the-migration)| PowerShell ve Azure portal | test sayısına bağlı olarak 1 saat veya daha fazla | Her iki etki alanı denetleyicisi de kullanılabilir ve normal şekilde çalışır. | Yok. İlk VM başarıyla geçirildikten sonra, geri alma veya geri yükleme seçeneği yoktur. |
| [5. adım-Isteğe bağlı yapılandırma adımları](#optional-post-migration-configuration-steps) | Azure portal ve VM 'Ler | Yok | Kesinti süresi gerekli değildir | Yok |

> [!IMPORTANT]
> Ek kapalı kalma süresini önlemek için, geçiş işlemine başlamadan önce bu geçiş makalesinin ve kılavuzunun hepsini okuyun. Geçiş işlemi, Azure AD DS etki alanı denetleyicilerinin bir süre için kullanılabilirliğini etkiler. Kullanıcılar, hizmetler ve uygulamalar geçiş işlemi sırasında yönetilen etki alanında kimlik doğrulaması yapamaz.

## <a name="update-and-verify-virtual-network-settings"></a>Sanal ağ ayarlarını güncelleştirme ve doğrulama

Geçişe başlamadan önce, aşağıdaki ilk denetimleri ve güncelleştirmeleri doldurun. Bu adımlar, geçişten önce herhangi bir zamanda gerçekleşebilir ve Azure AD DS yönetilen etki alanının işlemini etkilemez.

1. Yerel Azure PowerShell ortamınızı en son sürüme güncelleştirin. Geçiş adımlarını tamamlayabilmeniz için en az sürüm *2.3.2*gerekir.

    Denetleme ve güncelleştirme hakkında daha fazla bilgi için bkz. [Azure PowerShell genel bakış][azure-powershell].

1. Mevcut bir Kaynak Yöneticisi sanal ağ oluşturun veya seçin.

    Ağ ayarlarının Azure AD DS için gerekli olan bağlantı noktalarını engellemediğinden emin olun. Bağlantı noktalarının hem klasik sanal ağda hem de Kaynak Yöneticisi sanal ağda açık olması gerekir. Bu ayarlar yol tablolarını içerir (ancak yol tablolarının kullanılması önerilmese de) ve ağ güvenlik grupları.

    Gerekli olan bağlantı noktalarını görüntülemek için bkz. [ağ güvenlik grupları ve gerekli bağlantı noktaları][network-ports]. Ağ iletişimi sorunlarını en aza indirmek için, geçiş başarıyla tamamlandıktan sonra, Kaynak Yöneticisi sanal ağına bir ağ güvenlik grubu veya yol tablosu beklemeniz ve uygulamanız önerilir.

    Bu hedef kaynak grubunu, hedef sanal ağı ve hedef sanal ağ alt ağını bir yere getirin. Bu kaynak adları, geçiş işlemi sırasında kullanılır.

1. Azure portal Azure AD DS yönetilen etki alanı sistem durumunu kontrol edin. Yönetilen etki alanı için herhangi bir uyarıınız varsa, geçiş işlemine başlamadan önce bunları çözümleyin.
1. İsteğe bağlı olarak, diğer kaynakları Kaynak Yöneticisi dağıtım modeline ve sanal ağa taşımayı planlıyorsanız, bu kaynakların geçirilebileceğini onaylayın. Daha fazla bilgi için bkz. [Klasik 'dan Kaynak Yöneticisi IaaS kaynaklarının platformu tarafından desteklenen geçişi][migrate-iaas].

    > [!NOTE]
    > Klasik sanal ağı bir Kaynak Yöneticisi sanal ağa dönüştürmeyin. Bunu yaparsanız Azure AD DS yönetilen etki alanını geri alma veya geri yükleme seçeneği yoktur.

## <a name="prepare-the-managed-domain-for-migration"></a>Yönetilen etki alanını geçiş için hazırlama

Azure PowerShell, Azure AD DS yönetilen etki alanını geçişe hazırlamak için kullanılır. Bu adımlar, bir yedekleme alma, eşitlemeyi duraklatma ve Azure AD DS barındıran bulut hizmetini silme işlemleri içerir. Bu adım tamamlandığında, Azure AD DS bir süre çevrimdışı yapılır. Hazırlama adımı başarısız olursa, [önceki duruma geri](#roll-back)dönebilirsiniz.

Azure AD DS yönetilen etki alanını geçişe hazırlamak için aşağıdaki adımları izleyin:

1. [PowerShell Galerisi][powershell-script]`Migrate-Aaads` betiğini yükler. Bu PowerShell geçiş betiği, Azure AD Mühendisliği ekibi tarafından dijital olarak imzalanır.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. [Get-Credential][get-credential] cmdlet 'ini kullanarak geçiş betiği tarafından kimlik bilgilerini tutacak bir değişken oluşturun.

    Belirttiğiniz kullanıcı hesabı, Azure AD DS ve ardından Azure aboneliğinizde *katkıda* bulunan ayrıcalıkları etkinleştirmek IÇIN Azure AD kiracınızda *genel yönetici* ayrıcalıklarına sahip olmalıdır ve gerekli Azure AD DS kaynaklarını oluşturur.

    İstendiğinde, uygun bir kullanıcı hesabı ve parola girin:

    ```powershell
    $creds = Get-Credential
    ```

1. Şimdi *-Prepare* parametresini kullanarak `Migrate-Aadds` cmdlet 'ini çalıştırın. Azure AD DS yönetilen etki alanınız için *contoso.com*gibi *-manageddomainfqdn* sağlayın:

    ```powershell
    Migrate-Aadds `
        -Prepare -ManagedDomainFqdn contoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>Yönetilen etki alanını geçirme

Azure AD DS yönetilen etki alanı hazırlandı ve yedeklendiğinde etki alanı geçirilebilir. Bu adım, Kaynak Yöneticisi dağıtım modelini kullanarak Azure AD Domain Services etki alanı denetleyicisi sanal makinelerini yeniden oluşturur. Bu adımın tamamlanması 1-3 saat sürebilir.

*-COMMIT* parametresini kullanarak `Migrate-Aadds` cmdlet 'ini çalıştırın. *Contoso.com*gibi önceki bölümde hazırlanan Azure AD DS yönetilen etki alanınız için *-manageddomainfqdn* sağlayın:

Azure AD DS geçirmek istediğiniz sanal ağı içeren hedef kaynak grubunu ( *Myresourcegroup*gibi) belirtin. *Myvnet*gibi hedef sanal ağı ve *DomainServices*gibi alt ağı sağlayın.

Bu komut çalıştıktan sonra geri alamazsınız:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn contoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

Komut dosyası, yönetilen etki alanını geçişe hazırlandıktan sonra geçiş işlemini başlatmak için *Y* girin.

> [!IMPORTANT]
> Geçiş işlemi sırasında klasik sanal ağı bir Kaynak Yöneticisi sanal ağa dönüştürmeyin. Sanal ağı dönüştürürseniz, özgün sanal ağ artık mevcut olmadığından Azure AD DS tarafından yönetilen etki alanını geri alma veya geri yükleme işlemini geri alamazsınız.

Geçiş işlemi sırasında her iki dakikada bir ilerleme göstergesi, aşağıdaki örnek çıktıda gösterildiği gibi geçerli durumu bildirir:

![Azure AD DS geçişinin ilerleme göstergesi](media/migrate-from-classic-vnet/powershell-migration-status.png)

Geçiş işlemi, PowerShell betiğini kapatsanız bile çalışmaya devam eder. Azure portal, yönetilen etki alanı raporlarının *geçiş*olarak durumu.

Geçiş başarıyla tamamlandığında, ilk etki alanı denetleyicinizin IP adresini Azure portal veya Azure PowerShell aracılığıyla görüntüleyebilirsiniz. Kullanılabilir ikinci etki alanı denetleyicisinde tahmini bir zaman gösterilir.

Bu aşamada, isteğe bağlı olarak diğer mevcut kaynakları klasik dağıtım modelinden ve sanal ağdan taşıyabilirsiniz. Ya da, Azure AD DS geçişi tamamlandıktan sonra kaynakları klasik dağıtım modelinde tutabilir ve sanal ağları eşler.

## <a name="test-and-verify-connectivity-after-the-migration"></a>Geçişten sonra bağlantıyı test edin ve doğrulayın

İkinci etki alanı denetleyicisinin başarıyla dağıtılması ve Azure AD DS yönetilen etki alanında kullanılmak üzere kullanılabilmesi biraz zaman alabilir.

Kaynak Yöneticisi dağıtım modeliyle, Azure AD DS yönetilen etki alanı için ağ kaynakları Azure portal veya Azure PowerShell gösterilir. Bu ağ kaynaklarının ne olduğu hakkında daha fazla bilgi edinmek için bkz. [Azure AD DS tarafından kullanılan ağ kaynakları][network-resources].

En az bir etki alanı denetleyicisi kullanılabilir olduğunda, VM 'lerle ağ bağlantısı için aşağıdaki yapılandırma adımlarını uygulayın:

* **DNS sunucusu ayarlarını Güncelleştir** Kaynak Yöneticisi sanal ağındaki diğer kaynakların Azure AD DS yönetilen etki alanını çözümleyip kullanmasına izin vermek için DNS ayarlarını, yeni etki alanı denetleyicilerinin IP adresleriyle güncelleştirin. Azure portal bu ayarları sizin için otomatik olarak yapılandırabilir. Kaynak Yöneticisi sanal ağını yapılandırma hakkında daha fazla bilgi için bkz. [Azure sanal ağı IÇIN DNS ayarlarını güncelleştirme][update-dns].
* **Etki alanına katılmış VM 'Leri yeniden başlatma** -Azure AD DS etki alanı DENETLEYICILERI için DNS sunucusu IP adresleri değiştiğinde, etki alanına katılmış tüm VM 'leri yeniden başlatarak yeni DNS sunucusu ayarlarını kullanın. Uygulamalar veya VM 'Ler DNS ayarlarını el ile yapılandırdıysa, Azure portal gösterilen etki alanı denetleyicilerinin yeni DNS sunucusu IP adresleriyle el ile güncelleştirin.

Artık sanal ağ bağlantısını ve ad çözümlemesini test edin. Kaynak Yöneticisi sanal ağa bağlı veya onunla eşlenmeye yönelik bir VM 'de aşağıdaki ağ iletişim testlerini deneyin:

1. Etki alanı denetleyicilerinden birinin IP adresine ping atabildiğini (örneğin, `ping 10.1.0.4`) denetleyin
    * Etki alanı denetleyicilerinin IP adresleri, Azure portal Azure AD DS yönetilen etki alanının **Özellikler** sayfasında gösterilir.
1. Yönetilen etki alanının ad çözümlemesini doğrulayın, örneğin `nslookup contoso.com`
    * DNS ayarlarının doğru ve çözümlendiğini doğrulamak için, Azure AD DS yönetilen etki alanınız için DNS adını belirtin.

İkinci etki alanı denetleyicisi, geçiş cmdlet 'i bittikten sonra 1-2 saat kullanılabilir olmalıdır. İkinci etki alanı denetleyicisinin kullanılabilir olup olmadığını denetlemek için, Azure portal Azure AD DS yönetilen etki alanının **Özellikler** sayfasına bakın. İki IP adresi gösteriliyorsa, ikinci etki alanı denetleyicisi hazırlayın.

## <a name="optional-post-migration-configuration-steps"></a>İsteğe bağlı geçiş sonrası yapılandırma adımları

Geçiş işlemi başarıyla tamamlandığında, bazı isteğe bağlı yapılandırma adımları denetim günlüklerini veya e-posta bildirimlerini etkinleştirmeyi veya hassas parola ilkesini güncelleştirmeyi içerir.

#### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Azure Izleyici 'yi kullanarak denetim günlüklerine abone olma

Azure AD DS, etki alanı denetleyicilerindeki olayları sorun gidermeye ve görüntülemeye yardımcı olmak için Denetim günlüklerini kullanıma sunar. Daha fazla bilgi için bkz. [Denetim günlüklerini etkinleştirme ve kullanma][security-audits].

Günlüklerde gösterilen önemli bilgileri izlemek için şablonları kullanabilirsiniz. Örneğin, denetim günlüğü çalışma kitabı şablonu Azure AD DS yönetilen etki alanında olası hesap kilitlenmelerini izleyebilir.

#### <a name="configure-azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services e-posta bildirimlerini yapılandırma

Azure AD DS yönetilen etki alanında bir sorun algılandığında bildirim almak için Azure portal e-posta bildirimi ayarlarını güncelleştirin. Daha fazla bilgi için bkz. [bildirim ayarlarını yapılandırma][notifications].

#### <a name="update-fine-grained-password-policy"></a>Hassas parola ilkesini Güncelleştir

Gerekirse, hassas parola ilkesini varsayılan yapılandırmadan daha az kısıtlayıcı olacak şekilde güncelleştirebilirsiniz. Daha az kısıtlayıcı bir ayarın mantıklı olup olmadığını anlamak için Denetim günlüklerini kullanabilir, sonra da ilkeyi gerektiği şekilde yapılandırın. Geçişten sonra sürekli olarak Kilitlenen hesapların ilke ayarlarını gözden geçirmek ve güncelleştirmek için aşağıdaki üst düzey adımları kullanın:

1. Azure AD DS yönetilen etki alanında daha az kısıtlama için [parola Ilkesi yapılandırın][password-policy] ve denetim günlüklerindeki olayları gözlemleyin.
1. Herhangi bir hizmet hesabı, denetim günlüklerinde tanımlandığı şekilde, zaman aşımına uğradı parolaları kullanıyorsa, bu hesapları doğru parolayla güncelleştirin.
1. VM internet 'e sunulduğunu, yüksek oturum açma girişimleri ile *yönetici*, *Kullanıcı*veya *Konuk* gibi genel hesap adlarını gözden geçirin. Mümkün olduğunda, bu VM 'Leri daha az genel olarak adlandırılmış hesaplar kullanacak şekilde güncelleştirin.
1. Saldırıların kaynağını bulmak için sanal makinede bir ağ izlemesi kullanın ve oturum açma işlemlerini deneyebilmek için bu IP adreslerini engelleyin.
1. En düşük kilitleme sorunları olduğunda, hassas parola ilkesini gerektiği kadar kısıtlayıcı olacak şekilde güncelleştirin.

#### <a name="creating-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

Azure AD DS, yönetilen etki alanı için gereken bağlantı noktalarının güvenliğini sağlamak ve diğer tüm gelen trafiği engellemek için bir ağ güvenlik grubu gerektirir. Bu ağ güvenlik grubu, yönetilen etki alanına erişimi kilitlemek için ek bir koruma katmanı işlevi görür ve otomatik olarak oluşturulmaz. Ağ güvenlik grubu oluşturmak ve gerekli bağlantı noktalarını açmak için aşağıdaki adımları gözden geçirin:

1. Azure portal Azure AD DS kaynağınızı seçin. Genel Bakış sayfasında, Azure AD Domain Services ilişkili hiçbiri varsa ağ güvenlik grubu oluşturmak için bir düğme görüntülenir.
1. Güvenli LDAP kullanıyorsanız, *TCP* bağlantı noktası *636*için gelen trafiğe izin vermek üzere ağ güvenlik grubuna bir kural ekleyin. Daha fazla bilgi için bkz. [GÜVENLI LDAP yapılandırma][secure-ldap].

## <a name="roll-back-and-restore-from-migration"></a>Geçişten geri alma ve geri yükleme

### <a name="roll-back"></a>Geri al

Adım 2 ' de Geçişe hazırlanmak üzere PowerShell cmdlet 'ini çalıştırdığınızda bir hata oluşursa, adım 3 ' te Azure AD DS yönetilen etki alanı özgün yapılandırmaya geri dönebilir. Bu geri alma, başlangıçtaki klasik sanal ağı gerektirir. IP adreslerinin geri alma işleminin ardından hala değişebileceğini unutmayın.

*-Abort* parametresini kullanarak `Migrate-Aadds` cmdlet 'ini çalıştırın. *Contoso.com*gibi önceki bir bölümde hazırlanan Azure AD DS yönetilen etki alanınız için *-manageddomainfqdn* sağlayın:

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn contoso.com `
    -Credentials $creds
```

### <a name="restore"></a>Geri Yükleme

Son çare olarak, Azure AD Domain Services kullanılabilir son yedeklemeden geri yüklenebilir. En güncel yedeklemenin kullanılabilir olduğundan emin olmak için geçişin adım 1 ' de bir yedekleme gerçekleştirilir. Bu yedekleme 30 gün boyunca depolanır.

Azure AD DS yönetilen etki alanını yedekten geri yüklemek için [Azure Portal kullanarak bir destek talebi bileti açın][azure-support]. Dizin KIMLIĞINIZI, etki alanı adınızı ve geri yükleme nedeninizi belirtin. Destek ve geri yükleme işleminin tamamlanması birkaç gün sürebilir.

## <a name="troubleshooting"></a>Sorun giderme

Kaynak Yöneticisi dağıtım modeline geçişten sonra sorunlarla karşılaşırsanız, aşağıdaki ortak sorun giderme alanlarından bazılarını gözden geçirin:

* [Etki alanına ekleme sorunlarını giderme][troubleshoot-domain-join]
* [Hesap kilitleme sorunlarını giderme][troubleshoot-account-lockout]
* [Hesap oturum açma sorunlarını giderme][troubleshoot-sign-in]
* [Güvenli LDAP bağlantı sorunlarını giderme][tshoot-ldaps]

## <a name="next-steps"></a>Sonraki adımlar

Azure AD DS yönetilen etki alanınız Kaynak Yöneticisi dağıtım modeline geçirilmiş olarak, [bir Windows sanal makinesi oluşturun ve etki alanına ekleyin][join-windows] ve ardından [Yönetim Araçları 'nı yükler][tutorial-create-management-vm].

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/1.0
