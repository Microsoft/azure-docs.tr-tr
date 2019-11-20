---
title: Kiracılar arası yönetim deneyimleri
description: Azure Temsilcili kaynak yönetimi, bir çapraz kiracı yönetim deneyimi sunar.
ms.date: 11/7/2019
ms.topic: overview
ms.openlocfilehash: 0d04a7a77a3f92cffb185ff829f0d678dac2a9ff
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131927"
---
# <a name="cross-tenant-management-experiences"></a>Kiracılar arası yönetim deneyimleri

Hizmet sağlayıcısı olarak, Azure kaynaklarını, [Azure Portal](https://portal.azure.com)kendi kiracınızda birden fazla müşterinin Azure kaynaklarını [yönetmek için kullanabilirsiniz](../concepts/azure-delegated-resource-management.md) . Birçok görev ve hizmet yönetilen kiracılar genelinde Azure kaynakları için kullanılabilir. Bu makalede, Azure tarafından yetkilendirilen Kaynak yönetiminin etkili olduğu bazı gelişmiş senaryolar açıklanmaktadır.

> [!NOTE]
> Azure Temsilcili kaynak yönetimi, platformlar arası yönetimi basitleştirmek için birden fazla kiracının bulunduğu bir kuruluşta da kullanılabilir.

## <a name="understanding-customer-tenants"></a>Müşteri kiracılarını anlama

Azure Active Directory (Azure AD) kiracısı, kuruluşun bir gösterimidir. Bu, bir kuruluşun Azure, Microsoft 365 veya diğer hizmetlere kaydolarak Microsoft ile bir ilişki oluşturduklarında aldığı adanmış bir Azure AD örneğidir. Her Azure AD kiracısı farklı ve diğer Azure AD kiracılarından ayrıdır ve kendi kiracı KIMLIĞINE (bir GUID) sahiptir. Daha fazla bilgi için bkz. [Azure Active Directory nedir?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)

Genellikle, bir müşterinin Azure kaynaklarını yönetmek için, hizmet sağlayıcılarının bu müşterinin kiracısıyla ilişkili bir hesabı kullanarak Azure portal oturum açması gerekir ve Kullanıcı hesaplarını oluşturmak ve yönetmek için müşterinin kiracısında bir yönetici gerektirir hizmet sağlayıcı için.

Azure Temsilcili kaynak yönetimi sayesinde, ekleme işlemi, hizmet sağlayıcısının kiracısındaki kullanıcıları, müşteri kiracısında abonelikler, kaynak grupları ve kaynaklara erişebilecek ve yönetebilecek kullanıcıları belirler. Bu kullanıcılar daha sonra kendi kimlik bilgilerini kullanarak Azure portal oturum açabilirler. Azure portal içinde, erişimleri olan tüm müşterilere ait olan kaynakları yönetebilir. Bu işlem, Azure portal [müşteriler](../how-to/view-manage-customers.md) sayfasından veya Azure Portal ya da API 'ler aracılığıyla doğrudan söz konusu müşterinin aboneliği kapsamında çalışarak yapılabilir.

Azure Temsilcili kaynak yönetimi, farklı kiracılarda farklı hesaplarda oturum açmaya gerek kalmadan, birden fazla müşteriye ait kaynakların yönetilmesine daha fazla esneklik sağlar. Örneğin, bir hizmet sağlayıcısı, aşağıda gösterildiği gibi farklı sorumluluklara ve erişim düzeylerine sahip üç müşteriye sahip olabilir:

![Hizmet sağlayıcısı sorumluluklarını gösteren üç müşteri kiracının](../media/azure-delegated-resource-management-customer-tenants.jpg)

Yetkili kullanıcılar, Azure tarafından atanan kaynak yönetimini kullanarak bu kaynaklara erişmek için hizmet sağlayıcının kiracısında oturum açabilirler, burada gösterildiği gibi:

![Bir hizmet sağlayıcı kiracısıyla yönetilen müşteri kaynakları](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>API 'Ler ve Yönetim Aracı desteği

Temsilcili kaynaklar üzerinde doğrudan portalda veya API 'Ler ile yönetim araçlarını kullanarak (Azure CLı ve Azure PowerShell) yönetim görevleri gerçekleştirebilirsiniz. Tüm mevcut API 'Ler, işlevsellik çapraz Kiracı Yönetimi için desteklendiği ve Kullanıcı uygun izinlere sahip olduğu sürece, temsilcili kaynaklarla çalışırken kullanılabilir.

Ayrıca, Azure Temsilcili kaynak yönetimi görevlerini gerçekleştirmek için API 'Ler sunuyoruz. Daha fazla bilgi için **başvuru** bölümüne bakın.

## <a name="enhanced-services-and-scenarios"></a>Geliştirilmiş hizmetler ve senaryolar

Birçok görev ve hizmet, yönetilen kiracılar genelinde Temsilcili kaynaklar üzerinde gerçekleştirilebilir. Platformlar arası yönetimin etkili olabilmesi için aşağıdaki önemli senaryolardan bazılarını aşağıda bulabilirsiniz.

[Sunucular Için Azure Arc (Önizleme)](https://docs.microsoft.com/azure/azure-arc/servers/overview):

- Azure 'daki [Windows Server veya Linux makinelerini](https://docs.microsoft.com/azure/azure-arc/servers/quickstart-onboard-portal) , Azure 'daki abonelik ve/veya kaynak grupları için temsilci olarak bağlayın
- Azure Ilkesi ve etiketleme gibi Azure yapılarını kullanarak bağlı makineleri yönetme

[Azure Otomasyonu](https://docs.microsoft.com/azure/automation/):

- Atanan müşteri kaynaklarına erişmek ve bunlarla çalışmak için Otomasyon hesaplarını kullanma

[Azure Backup](https://docs.microsoft.com/azure/backup/):

- Müşteri kiracılarında müşteri verilerini yedekleme ve geri yükleme

[Azure Kubernetes hizmeti (AKS)](https://docs.microsoft.com//azure/aks/):

- Barındırılan Kubernetes ortamlarını yönetme ve müşteri kiracılarında Kapsayıcılı uygulamaları dağıtma ve yönetme

[Azure izleyici](https://docs.microsoft.com/azure/azure-monitor/):

- Tüm aboneliklerdeki uyarıları görüntüleyebilme olanağı sayesinde, temsilci atanmış abonelikler için uyarıları görüntüleme
- Temsilcili abonelikler için etkinlik günlüğü ayrıntılarını görüntüleme
- Log Analytics: birden çok Kiracıdaki uzak müşteri çalışma alanlarından verileri sorgulama
- Web kancaları aracılığıyla hizmet sağlayıcı kiracısındaki Azure Otomasyonu runbook 'ları veya Azure Işlevleri gibi Otomasyonu tetikleyen müşteri kiracılarında uyarı oluşturma

[Azure ilkesi](https://docs.microsoft.com/azure/governance/policy/):

- Uyumluluk anlık görüntüleri, atanan abonelikler içindeki atanmış ilkelerin ayrıntılarını gösterir
- Temsilci atanmış bir abonelik içinde ilke tanımları oluşturma ve düzenleme
- Atanan abonelik içinde müşteri tanımlı ilke tanımları atama
- Müşteriler, yazdığı ilkelerin yanı sıra hizmet sağlayıcı tarafından yazılan ilkeleri görür
- [DeployIfNotExists 'i düzeltebilir veya müşteri kiracısında atamaları değiştirebilirler](../how-to/deploy-policy-remediation.md)

[Azure Kaynak Grafiği](https://docs.microsoft.com/azure/governance/resource-graph/):

- Şimdi döndürülen sorgu sonuçlarında kiracı KIMLIĞINI içerir, bu da bir aboneliğin müşteri kiracısına veya hizmet sağlayıcı kiracısına ait olup olmadığını tanımlamanızı sağlar

[Azure Güvenlik Merkezi](https://docs.microsoft.com/azure/security-center/):

- Çapraz kiracı görünürlüğü
  - Güvenlik ilkelerine uyumluluğu izleyin ve tüm kiracıların kaynakları genelinde güvenlik kapsamı sağlayın
  - Tek bir görünümdeki birden çok müşteri arasında sürekli mevzuat uyumluluk izlemesi
  - Güvenli puan hesaplaması ile eylem yapılabilir güvenlik önerilerini izleyin, önceliklendirin ve önceliklendirin
- Çapraz kiracı güvenlik sonrası yönetimi
  - Güvenlik ilkelerini yönetme
  - İşlem yapılabilir güvenlik önerileri ile uyumlu olmayan kaynaklar üzerinde işlem gerçekleştirin
  - Güvenlikle ilgili verileri toplama ve depolama
- Çapraz kiracı tehdit algılama ve koruma
  - Kiracıların kaynakları genelinde tehditleri Algıla
  - Tam zamanında (JıT) VM erişimi gibi gelişmiş tehdit koruması denetimleri uygulayın
  - Uyarlamalı ağ sağlamlaştırma ile ağ güvenlik grubu yapılandırmasını Harden artırma
  - Sunucuların yalnızca Uyarlamalı uygulama denetimleriyle birlikte olması gereken uygulamaları ve süreçler çalıştırdığından emin olun
  - Dosya bütünlüğü Izleme (FIM) ile önemli dosyalarda ve kayıt defteri girdilerindeki değişiklikleri izleme

[Azure Sentinel](https://docs.microsoft.com/azure/sentinel/multiple-tenants-service-providers):

- Müşteri kiracılarında Azure Sentinel kaynaklarını yönetme

[Azure hizmet durumu](https://docs.microsoft.com/azure/service-health/):

- Azure Kaynak Durumu ile müşteri kaynaklarının sistem durumunu izleme
- Müşterileriniz tarafından kullanılan Azure hizmetlerinin sistem durumunu izleme

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/):

- Müşteri kiracılarında Azure sanal makineler için olağanüstü durum kurtarma seçeneklerini yönetme (VM uzantılarını kopyalamak için RunAs hesaplarını kullanmayacağınızı unutmayın)

[Azure sanal makineleri](https://docs.microsoft.com/azure/virtual-machines/):

- Müşteri kiracılarında Azure VM 'lerinde dağıtım sonrası yapılandırma ve otomasyon görevleri sağlamak için sanal makine uzantılarını kullanın
- Müşteri kiracılarında Azure VM 'lerinde sorun gidermek için önyükleme tanılamayı kullanma
- Müşteri kiracılarında seri konsol ile VM 'Lere erişme
- Bir VM 'ye uzaktan oturum açma için Azure Active Directory kullanmayacağınızı ve disk şifrelemesi için parolalar, gizlilikler veya şifreleme anahtarları için bir sanal makineyi Key Vault ile tümleştiremiyorum gerektiğini unutmayın

[Azure sanal ağı](https://docs.microsoft.com/azure/virtual-network/):

- Sanal ağlar ve sanal ağ arabirim kartları (vNIC 'ler) ile müşteri kiracılar arasında dağıtın ve yönetin

Destek istekleri:

- Temsilci atanan kaynaklar için destek isteklerini, Azure portal **Yardım + Destek** dikey penceresinden açın (Temsilcili kapsam için kullanılabilen destek planını seçme)

## <a name="current-limitations"></a>Geçerli sınırlamalar
Tüm senaryolarla, lütfen aşağıdaki geçerli sınırlamalara dikkat edin:

- Azure Resource Manager tarafından işlenen istekler, Azure tarafından atanan kaynak yönetimi kullanılarak gerçekleştirilebilir. Bu isteklerin işlem URI 'Leri `https://management.azure.com`başlar. Ancak, kaynak türünün bir örneği tarafından işlenen istekler (örneğin, Anahtar Kasası gizli dizileri erişimi veya depolama veri erişimi), Azure tarafından atanan kaynak yönetimi ile desteklenmez. Bu isteklerin işlem URI 'Leri genellikle örneğiniz için benzersiz olan ve `https://myaccount.blob.core.windows.net` veya `https://mykeyvault.vault.azure.net/`gibi bir adresle başlar. İkincisi ayrıca yönetim işlemleri yerine genellikle veri operasyonlardır. 
- Rol atamalarının rol tabanlı erişim denetimi (RBAC) [yerleşik rollerini](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)kullanması gerekir. Tüm yerleşik roller Şu anda, sahip veya [Dataactions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) iznine sahip herhangi bir yerleşik rol haricinde Azure tarafından yetkilendirilen kaynak yönetimi ile desteklenmektedir. Kullanıcı erişimi yönetici rolü yalnızca [yönetilen kimliklere rol atama](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)konusunda sınırlı kullanım için desteklenir.  Özel roller ve [Klasik abonelik yöneticisi rolleri](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) desteklenmez.
- Şu anda, abonelik Azure Databricks kullanıyorsa, Azure tarafından atanan kaynak yönetimi için abonelik (veya bir abonelik içinde kaynak grubu) ekleyemezsiniz. Benzer şekilde, **Microsoft. ManagedServices** kaynak sağlayıcısı ile ekleme için bir abonelik kaydedilmişse, bu abonelik için şu anda bir Databricks çalışma alanı oluşturamazsınız.
- Kaynak kilitleri olan Azure tarafından atanan kaynak yönetimi için abonelikler ve kaynak grupları ekleyebilirsiniz, ancak bu kilitler, eylemlerin yönetim kiracısındaki kullanıcılar tarafından gerçekleştirilmesini engellemez. Azure tarafından yönetilen uygulamalar veya Azure şemaları (sistem tarafından atanan reddetme atamaları) tarafından oluşturulanlar gibi sistem tarafından yönetilen kaynakları koruyan [atamaları reddetme](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) , yönetim kiracısındaki kullanıcıların bu kaynaklara göre davranmasını önler; Bununla birlikte, müşteri kiracısındaki kullanıcılar kendi reddetme atamalarını oluşturamaz (Kullanıcı tarafından atanan reddetme atamaları).

## <a name="next-steps"></a>Sonraki adımlar

- Müşterilerinizi [Azure Resource Manager şablonları kullanarak](../how-to/onboard-customer.md) veya [Azure Market 'e özel veya genel olarak yönetilen bir hizmet teklifi yayımlayarak](../how-to/publish-managed-services-offers.md), Azure tarafından atanan kaynak yönetimine ekleyin.
- Azure portal **müşterilerime** giderek [müşterileri görüntüleyin ve yönetin](../how-to/view-manage-customers.md) .
