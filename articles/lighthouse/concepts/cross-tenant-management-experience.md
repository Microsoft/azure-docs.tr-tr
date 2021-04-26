---
title: Kiracılar arası yönetim deneyimleri
description: Azure Temsilcili kaynak yönetimi, bir çapraz kiracı yönetim deneyimi sunar.
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: 027d1d5e81d5a652a7e2d5441c40440c661f730f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778634"
---
# <a name="cross-tenant-management-experiences"></a>Kiracılar arası yönetim deneyimleri

Hizmet sağlayıcı olarak, [Azure Mathouse](../overview.md) kullanarak, birden çok müşteriye ait kaynakları kendi Azure Active Directory (Azure AD) kiracınızda yönetebilirsiniz. Yönetilen kiracılar arasında çok sayıda görev ve hizmet, [Azure tarafından atanan kaynak yönetimi](../concepts/azure-delegated-resource-management.md)kullanılarak gerçekleştirilebilir.

> [!TIP]
> Azure Temsilcili kaynak yönetimi, platformlar arası yönetimi basitleştirmek için [birden çok Azure AD kiracısına sahip bir kuruluşta](enterprise.md) de kullanılabilir.

## <a name="understanding-tenants-and-delegation"></a>Kiracılar ve temsilciyi anlama

Bir Azure AD kiracısı, kuruluşun bir gösterimidir. Bu, bir kuruluşun Azure, Microsoft 365 veya diğer hizmetlere kaydolarak Microsoft ile bir ilişki oluşturduklarında aldığı adanmış bir Azure AD örneğidir. Her Azure AD kiracısı farklı ve diğer Azure AD kiracılarından ayrıdır ve kendi kiracı KIMLIĞINE (bir GUID) sahiptir. Daha fazla bilgi için bkz. [Azure Active Directory nedir?](../../active-directory/fundamentals/active-directory-whatis.md)

Genellikle, bir müşterinin Azure kaynaklarını yönetmek için, hizmet sağlayıcılarının bu müşterinin kiracısıyla ilişkili bir hesabı kullanarak Azure portal oturum açması gerekir, bu da müşterinin kiracısında hizmet sağlayıcısı için Kullanıcı hesapları oluşturmak ve yönetmek için bir yönetici gerektirir.

Azure Use ile, ekleme işlemi, hizmet sağlayıcısının kiracısındaki kullanıcıları, müşterinin kiracısında Temsilcili abonelikler ve kaynak grupları üzerinde çalışabilecektir. Bu kullanıcılar daha sonra kendi kimlik bilgilerini kullanarak Azure portal oturum açabilirler. Azure portal içinde, erişimleri olan tüm müşterilere ait olan kaynakları yönetebilir. Bu işlem, Azure portal [müşteriler](../how-to/view-manage-customers.md) sayfasından veya Azure Portal ya da API 'ler aracılığıyla doğrudan söz konusu müşterinin aboneliği kapsamında çalışarak yapılabilir.

Azure Athouse, farklı kiracılarda farklı hesaplarda oturum açmak zorunda kalmadan birden fazla müşteriye ait kaynakların yönetilmesine daha fazla esneklik sağlar. Örneğin, bir hizmet sağlayıcısı farklı sorumluluklara ve erişim düzeylerine sahip iki müşteriye sahip olabilir. Yetkili kullanıcılar, Azure Athouse kullanılarak bu kaynaklara erişmek için hizmet sağlayıcının kiracısında oturum açabilir.

![Bir hizmet sağlayıcı kiracısı aracılığıyla yönetilen müşteri kaynaklarını gösteren diyagram.](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>API 'Ler ve Yönetim Aracı desteği

Temsilcili kaynaklar üzerinde doğrudan portalda veya API 'Ler ile yönetim araçlarını kullanarak (Azure CLı ve Azure PowerShell) yönetim görevleri gerçekleştirebilirsiniz. Tüm mevcut API 'Ler, işlevsellik çapraz Kiracı Yönetimi için desteklendiği ve Kullanıcı uygun izinlere sahip olduğu sürece, temsilcili kaynaklarla çalışırken kullanılabilir.

Azure PowerShell [Get-AzSubscription cmdlet 'i](/powershell/module/Az.Accounts/Get-AzSubscription) , `TenantId` Varsayılan olarak yönetim kiracısı için gösterir. `HomeTenantId` `ManagedByTenantIds` Her abonelik için ve özniteliklerini kullanarak, döndürülen bir aboneliğin yönetilen bir kiracıya veya yönetim kiracınıza ait olduğunu tanımlamanızı sağlayabilirsiniz.

Benzer şekilde, [az Account List](/cli/azure/account#az_account_list) gıbı Azure CLI komutları `homeTenantId` ve özniteliklerini gösterir `managedByTenants` . Azure CLı kullanırken bu değerleri görmüyorsanız, arkasından ' i çalıştırarak Önbelleğinizi temizlemeyi deneyin `az account clear` `az login --identity` .

Azure REST API, [abonelikler-Get](/rest/api/resources/subscriptions/get) ve [abonelikler-liste](/rest/api/resources/subscriptions/list) komutları şunları içerir `ManagedByTenant` .

> [!NOTE]
> Azure mathouse ile ilgili kiracı bilgilerine ek olarak, bu API 'Ler tarafından gösterilen kiracılar, Azure Databricks veya Azure tarafından yönetilen uygulamalar için iş ortağı kiracılarını da yansıtabilir.

Ayrıca, Azure Use görevlerini gerçekleştirmeye özgü API 'Ler sunuyoruz. Daha fazla bilgi için **başvuru** bölümüne bakın.

## <a name="enhanced-services-and-scenarios"></a>Geliştirilmiş hizmetler ve senaryolar

Görevlerin ve hizmetlerin çoğu yönetilen kiracılar arasındaki temsilci kaynaklarında gerçekleştirilebilir. Bunlar, platformlar arası yönetimin özellikle etkili olabilecek önemli senaryolardan bazılarıdır.

[Azure yay](../../azure-arc/index.yml):

- Karma sunucuları ölçekli [Azure yay özellikli sunucularında](../../azure-arc/servers/overview.md)yönetme:
  - Azure [dışındaki Windows Server veya Linux makinelerini](../../azure-arc/servers/onboard-portal.md) , Azure 'daki Temsilcili aboneliklere ve/veya kaynak gruplarına bağlı olarak yönetin
  - Azure Ilkesi ve etiketleme gibi Azure yapılarını kullanarak bağlı makineleri yönetme
  - Müşterilerin karma ortamları arasında aynı ilke kümesinin uygulandığından emin olun
  - Müşterilerin karma ortamları arasında uyumluluğu izlemek için Azure Güvenlik Merkezi 'ni kullanma
- Karma Kubernetes kümelerini ölçekte yönetme- [Azure Arc etkin Kubernetes (Önizleme)](../../azure-arc/kubernetes/overview.md):
  - Azure 'da Temsilcili aboneliklere ve/veya kaynak gruplarına [bağlı Kubernetes kümelerini yönetme](../../azure-arc/kubernetes/quickstart-connect-cluster.md)
  - Bağlı kümeler için [Gilar kullanma](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md)
  - Bağlı kümeler arasında ilkeleri zorunlu kıl

[Azure Otomasyonu](../../automation/index.yml):

- Atanan kaynaklarla erişmek ve bunlarla çalışmak için Otomasyon hesaplarını kullanma

[Azure Backup](../../backup/index.yml):

- [Şirket içi iş yüklerinden, Azure VM 'lerinden, Azure dosya paylaşımlarından ve daha fazlasına ait](../..//backup/backup-overview.md#what-can-i-back-up) müşteri verilerini yedekleyin ve geri yükleyin
- Tüm temsilci atanmış müşteri kaynakları için [yedekleme merkezi](../../backup/backup-center-overview.md) 'nde verileri görüntüleyin
- Temsilci olan abonelikler için yedekleme öğelerinin (henüz yedekleme için yapılandırılmamış Azure kaynakları dahil) ve izleme bilgilerinin (işlerin ve uyarıların) işletimsel bilgilerini görüntülemeye yardımcı olması için [yedekleme Gezginini](../../backup/monitor-azure-backup-with-backup-explorer.md) kullanın. Yedekleme Gezgini Şu anda yalnızca Azure VM verileri için kullanılabilir.
- Geçmiş eğilimleri izlemek, yedekleme depolama tüketimini analiz etmek ve yedeklemeleri denetlemek ve geri yüklemek için, temsilcili abonelikler arasında [yedekleme raporları](../../backup/configure-reports.md) kullanın.

[Azure şemaları](../../governance/blueprints/index.yml):

- Kaynak şablonlarının ve diğer yapıların dağıtımını düzenlemek için Azure şemaları 'nı kullanın (müşteri aboneliğini hazırlamak için [ek erişim](https://www.wesleyhaakman.org/preparing-azure-lighthouse-customer-subscriptions-for-azure-blueprints/) gerekir)

[Azure maliyet yönetimi + faturalandırma](../../cost-management-billing/index.yml):

- Yönetim kiracısından, CSP iş ortakları, Azure planı kapsamındaki müşteriler için vergi öncesi tüketim maliyetlerini (satın almalara dahil değil) görüntüleyebilir, yönetebilir ve analiz edebilir. Maliyet, perakende tariflerine ve iş ortağının müşterinin aboneliğine sahip olduğu Azure rol tabanlı erişim denetimi (Azure RBAC) erişimine göre yapılır. Şu anda, Azure RBAC erişimine dayalı her bir müşteri aboneliği için perakende ücretlerine göre tüketim maliyetlerini görüntüleyebilirsiniz.

[Azure Key Vault](../../key-vault/general/index.yml):

- Müşteri kiracılarında Anahtar kasaları oluşturma
- Müşteri kiracılarında Anahtar kasaları oluşturmak için yönetilen bir kimlik kullanma

[Azure Kubernetes hizmeti (AKS)](../../aks/index.yml):

- Barındırılan Kubernetes ortamlarını yönetme ve müşteri kiracılarında Kapsayıcılı uygulamaları dağıtma ve yönetme
- Müşteri kiracılarında kümeleri dağıtma ve yönetme
-   Müşteri kiracılarında performansı izlemek için kapsayıcılar için Azure Izleyicisini kullanın

[Azure geçişi](../../migrate/index.yml):

- Müşteri kiracısında geçiş projeleri oluşturma ve VM 'Leri geçirme

[Azure izleyici](../../azure-monitor/index.yml):

- Tüm aboneliklerdeki uyarıları görüntüleme ve yenileme özelliği sayesinde, temsilci olan abonelikler için uyarıları görüntüleme
- Temsilcili abonelikler için etkinlik günlüğü ayrıntılarını görüntüleme
- [Log Analytics](../../azure-monitor/logs/service-providers.md): birden çok Kiracıdaki uzak çalışma alanlarından verileri sorgulama (müşteri kiracılarındaki çalışma alanlarından veriye erişmek için kullanılan Otomasyon hesaplarının aynı kiracıda oluşturulması gerektiğini unutmayın)
- Müşteri kiracılarında [etkinlik günlüğü uyarıları oluşturma, görüntüleme ve yönetme](../../azure-monitor/alerts/alerts-activity-log.md)
- Azure Otomasyonu runbook 'ları veya Azure Işlevleri gibi Otomasyonu tetikleyen müşteri kiracılarında, Web kancaları aracılığıyla kiracıyı yönetme bölümünde uyarı oluşturma
- Yönetim kiracısındaki çalışma alanlarına kaynak günlükleri göndermek için müşteri kiracılarında [Tanılama ayarları](../..//azure-monitor/essentials/diagnostic-settings.md) oluşturma
- SAP iş yükleri için, [Müşteri kiracılar genelinde toplu bir görünüm Ile SAP Çözümleri ölçümlerini izleyin](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293)

[Azure ağı](../../networking/networking-overview.md):

- Yönetilen kiracılar dahilinde [Azure sanal ağını](../../virtual-network/index.yml) ve sanal ağ arabirim kartlarını (vNIC 'ler) dağıtın ve yönetin
- Müşterilerin sanal ağ kaynaklarını korumak için [Azure Güvenlik duvarını](../../firewall/overview.md) dağıtma ve yapılandırma
- [Azure sanal WAN](../../virtual-wan/virtual-wan-about.md), [ExpressRoute](../../expressroute/expressroute-introduction.md)ve [VPN ağ geçitleri](../../vpn-gateway/vpn-gateway-about-vpngateways.md) gibi bağlantı hizmetlerini yönetme
- Azure [Athouse kullanarak Azure ağ msp programı](../../networking/networking-partners-msp.md) için önemli senaryoları destekleme

[Azure ilkesi](../../governance/policy/index.yml):

- Temsilci abonelikler içinde ilke tanımları oluşturma ve düzenleme
- Birden çok kiracının tamamında ilke tanımlarını ve ilke atamalarını dağıtma
- Atanan abonelikler içinde müşteri tanımlı ilke tanımları atama
- Müşteriler, yazdığı ilkelerin yanı sıra hizmet sağlayıcı tarafından yazılan ilkeleri görür
- [Dağıtım kiracısındaki deployIfNotExists 'i düzeltebilir veya atamaları değiştirebilir](../how-to/deploy-policy-remediation.md)
- Müşteri kiracılarında uyumlu olmayan kaynaklar için uyumluluk ayrıntılarını görüntülemenin Şu anda desteklenmediğini unutmayın

[Azure Kaynak Grafiği](../../governance/resource-graph/index.yml):

- Şimdi döndürülen sorgu sonuçlarında kiracı KIMLIĞINI içerir ve aboneliğin yönetilen bir kiracıya ait olup olmadığını tanımlamanızı sağlar

[Azure Güvenlik Merkezi](../../security-center/index.yml):

- Çapraz kiracı görünürlüğü
  - Güvenlik ilkelerine uyumluluğu izleyin ve tüm kiracıların kaynakları genelinde güvenlik kapsamı sağlayın
  - Tek bir görünümdeki birden fazla kiracıda sürekli mevzuat uyumluluğu izleme
  - Güvenli puan hesaplaması ile eylem yapılabilir güvenlik önerilerini izleyin, önceliklendirin ve önceliklendirin
- Çapraz kiracı güvenlik sonrası yönetimi
  - Güvenlik ilkelerini yönetin
  - İşlem yapılabilir güvenlik önerileri ile uyumlu olmayan kaynaklar üzerinde işlem gerçekleştirin
  - Güvenlikle ilgili verileri toplama ve depolama
- Çapraz kiracı tehdit algılama ve koruma
  - Kiracıların kaynakları genelinde tehditleri Algıla
  - Tam zamanında (JıT) VM erişimi gibi gelişmiş tehdit koruması denetimleri uygulayın
  - Uyarlamalı ağ sağlamlaştırma ile ağ güvenlik grubu yapılandırmasını Harden artırma
  - Sunucuların yalnızca Uyarlamalı uygulama denetimleriyle birlikte olması gereken uygulamaları ve süreçler çalıştırdığından emin olun
  - Dosya bütünlüğü Izleme (FIM) ile önemli dosyalarda ve kayıt defteri girdilerindeki değişiklikleri izleme
- Tüm aboneliğin, yönetim kiracısı için temsilci seçilmiş olması gerektiğini unutmayın; Azure Güvenlik Merkezi senaryoları, temsilcili kaynak grupları ile desteklenmez

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- [Müşteri Kiracılarında](../../sentinel/multiple-tenants-service-providers.md) Azure Sentinel kaynaklarını yönetme
- [Birden çok kiracıda saldırıları izleyin ve güvenlik uyarılarını görüntüleyin](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- Kiracılar genelinde yayılan birden çok Azure Sentinel çalışma alanı genelinde [olayları görüntüleme](../../sentinel/multiple-workspace-view.md)

[Azure hizmet durumu](../../service-health/index.yml):

- Azure Kaynak Durumu ile müşteri kaynaklarının sistem durumunu izleme
- Müşterileriniz tarafından kullanılan Azure hizmetlerinin sistem durumunu izleme

[Azure Site Recovery](../../site-recovery/index.yml):

- Müşteri kiracılarında Azure sanal makineler için olağanüstü durum kurtarma seçeneklerini yönetme ( `RunAs` VM uzantılarını kopyalamak için hesapları kullanmayacağınızı unutmayın)

[Azure sanal makineleri](../../virtual-machines/index.yml):

- Azure VM 'lerinde dağıtım sonrası yapılandırma ve otomasyon görevleri sağlamak için sanal makine uzantılarını kullanın
- Azure VM 'Leri sorunlarını gidermek için önyükleme tanılamayı kullanma
- Seri konsol ile VM 'Lere erişme
- Gizli dizileri yönetilen kiracılarda bir Key Vault depolandığından emin olmak için, [ilke aracılığıyla yönetilen kimlik](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret)kullanarak disk şifrelemesi için parolalar, gizlilikler veya şifreleme anahtarları için Azure Key Vault Ile VM 'leri tümleştirin
- VM 'Lerde uzaktan oturum açma için Azure Active Directory kullanmayacağınızı unutmayın

Destek istekleri:

- Temsilci seçme kaynakları için Azure portal [ **Yardım + Destek** 'Ten destek istekleri açın](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) (temsilci seçilen kapsam için kullanılabilen destek planını seçme)
- [Azure kota API](/rest/api/reserved-vm-instances/quotaapi) 'sini kullanarak, temsilcili müşteri kaynakları için Azure hizmet kotalarını görüntüleyin ve yönetin

## <a name="current-limitations"></a>Geçerli sınırlamalar

Tüm senaryolarla, lütfen aşağıdaki geçerli sınırlamalara dikkat edin:

- Azure Resource Manager tarafından işlenen istekler, Azure ışıklı kullanımı kullanılarak gerçekleştirilebilir. Bu isteklerin işlem URI 'Leri ile başlar `https://management.azure.com` . Ancak, bir kaynak türü örneği tarafından işlenen istekler (Key Vault gizli dizi erişimi veya depolama veri erişimi gibi) Azure ınthouse ile desteklenmez. Bu isteklerin işlem URI 'Leri genellikle örneğiniz için benzersiz olan bir adresle başlar, örneğin `https://myaccount.blob.core.windows.net` veya `https://mykeyvault.vault.azure.net/` . İkincisi ayrıca yönetim işlemleri yerine genellikle veri operasyonlardır.
- Rol atamaları [Azure yerleşik rollerini](../../role-based-access-control/built-in-roles.md)kullanmalıdır. Tüm yerleşik roller Şu anda, sahip veya izin içeren yerleşik roller hariç Azure tarafından yetkilendirilen kaynak yönetimi ile desteklenmektedir [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) . Kullanıcı erişimi yönetici rolü yalnızca [yönetilen kimliklere rol atama](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)konusunda sınırlı kullanım için desteklenir.  Özel roller ve [Klasik abonelik yöneticisi rolleri](../../role-based-access-control/classic-administrators.md) desteklenmez.
- Azure Databricks kullanan abonelikler ekleyebilirsiniz, ancak yönetme kiracısındaki kullanıcılar şu anda bir temsilci olan abonelikte Azure Databricks çalışma alanlarını başlatamaz.
- Kaynak kilitleri olan abonelikler ve kaynak grupları ekleyebilirsiniz, ancak bu kilitler yönetim kiracısındaki kullanıcılar tarafından gerçekleştirilen eylemlerin gerçekleştirilmesini engellemez. Azure tarafından yönetilen uygulamalar veya Azure şemaları (sistem tarafından atanan reddetme atamaları) tarafından oluşturulanlar gibi sistem tarafından yönetilen kaynakları koruyan [atamaları reddetme](../../role-based-access-control/deny-assignments.md) , yönetim kiracısındaki kullanıcıların bu kaynaklara göre davranmasını önler; Bununla birlikte, müşteri kiracısındaki kullanıcılar kendi reddetme atamalarını oluşturamaz (Kullanıcı tarafından atanan reddetme atamaları).
- Bir [Ulusal bulut](../../active-directory/develop/authentication-national-cloud.md) ve Azure genel bulutu genelinde veya iki ayrı ulusal bulutta abonelikler temsilciliğini desteklemez.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Resource Manager şablonları kullanarak](../how-to/onboard-customer.md) ya da [Azure Market 'e özel veya genel olarak yönetilen bir hizmet teklifi yayımlayarak](../how-to/publish-managed-services-offers.md), müşterilerinizi Azure aydınlathouse 'a ekleyin.
- Azure portal **müşterilerime** giderek [müşterileri görüntüleyin ve yönetin](../how-to/view-manage-customers.md) .