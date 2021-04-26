---
title: Azure Cloud Services (klasik) Azure Cloud Services (genişletilmiş destek) geçirme
description: Cloud Services (klasik) ile bulut hizmeti arasında geçişe genel bakış (genişletilmiş destek)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 96315899f80d0bd02ac3d2108b7cd76876025218
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286998"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Azure Cloud Services (klasik) Azure Cloud Services (genişletilmiş destek) geçirme

Bu makalede, platform tarafından desteklenen geçiş aracına ve Azure [Cloud Services (klasik)](../cloud-services/cloud-services-choose-me.md) [Azure Cloud Services (genişletilmiş destek)](overview.md)' e geçirmek için nasıl kullanılacağı hakkında genel bir bakış sunulmaktadır.

Geçiş Aracı aynı API 'Leri kullanır ve [sanal makine (klasik) geçişle](https://docs.microsoft.com/azure/virtual-machines/migration-classic-resource-manager-overview)aynı deneyime sahiptir. 

> [!IMPORTANT]
> Geçiş aracını kullanarak Cloud Services (klasik) Cloud Services (genişletilmiş destek) ' e geçiş Şu anda genel önizlemededir. Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Geçişiniz için yardıma ihtiyacınız varsa aşağıdaki kaynaklara bakın: 

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html): geçiş için Microsoft ve topluluk desteği.
- [Azure geçiş desteği](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%22pesId%22:%22e79dcabe-5f77-3326-2112-74487e1e5f78%22,%22supportTopicId%22:%22fca528d2-48bd-7c9f-5806-ce5d5b1d226f%22%7D): geçiş sırasında teknik yardım için adanmış destek ekibi. Teknik destek içermeyen müşteriler, bu geçiş için özel olarak sunulan [ücretsiz destek yeteneklerini](https://aka.ms/cs-migration-errors) kullanabilir.
- Şirketinizin/kuruluşunuzun Microsoft ile iş ortağı varsa veya bulut çözümü mimarları veya teknik hesap yöneticileri gibi Microsoft temsilcileriyle çalışıyorsa, geçiş için daha fazla kaynak için onlara ulaşın.
- Cloud Services (genişletilmiş destek) ürün ekibine geri bildirim sağlamak veya sorun yükseltmek için [Bu anketi](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR--AgudUMwJKgRGMO84rHQtUQzZYNklWUk4xOTFXVFBPOFdGOE85RUIwVC4u) doldurun. 

## <a name="migration-benefits"></a>Geçiş avantajları
Platform tarafından desteklenen geçiş, aşağıdaki önemli avantajları sağlar:
- Geçiş, platform tarafından tam olarak düzenlenir ve tüm dağıtım ve ilişkili kaynaklar Azure Resource Manager olarak taşınır.
- Kesinti süresi geçişi yok.
- El ile görevleri en aza indirerek, diğer geçiş yollarına kıyasla kolay ve hızlı geçiş. 
- Geçişin bir parçası olarak IP adresi ve DNS etiketi Cloud Services korur. 

Diğer avantajlar ve neden geçirilmesi gerektiği için bkz. [Cloud Services (genişletilmiş destek)](overview.md) ve [Azure Resource Manager](../azure-resource-manager/management/overview.md). 

## <a name="setup-access-for-migration"></a>Geçiş için erişim kurulumu

Bu geçişi gerçekleştirmek için abonelik için bir abonelikteki olarak eklenmelidir ve gereken sağlayıcıları kaydetmeniz gerekir. 

1. Azure portalında oturum açın.
3. Hub menüsünde, abonelik ' ı seçin. Bunu görmüyorsanız, tüm hizmetler ' i seçin.
3. Uygun abonelik girişini bulun ve ardından rol ALANıM ' a bakın. Coadministrator için, değer Hesap Yöneticisi olmalıdır. Ortak yönetici ekleyemediğinizde, abonelik için bir hizmet yöneticisiyle veya ortak yönetici ile iletişim kurun.

4. [Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) veya [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) kullanarak Microsoft. ClassicInfrastructureMigrate ad alanı aboneliğinizi kaydedin

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. [Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) veya [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) kullanarak Cloud Services geçiş önizleme özelliği için aboneliğinizi kaydedin

    ```powershell
    Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

6. Kaydlarınızın durumunu denetleyin. Kaydın tamamlanması birkaç dakika sürebilir. 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>Cloud Services (klasik) için sanal makinelerden (klasik) geçiş işlemi nasıl yapılır?
Azure Service Manager, iki farklı işlem ürününü, [Azure sanal makinelerini (klasik)](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic) ve [Azure Cloud Services (klasik)](../cloud-services/cloud-services-choose-me.md) veya Web/çalışan rollerini destekler. İki ürün, bulut hizmeti içinde yer alan dağıtım türüne göre farklılık gösterir. Azure Cloud Services (klasik) Web/çalışan rolleri olan dağıtımlar içeren bulut hizmetini kullanır. Azure sanal makineleri (klasik), IaaS VM 'Leri olan dağıtımlar içeren bir bulut hizmeti kullanır.

Desteklenen senaryoların listesi, dağıtım türlerindeki farklılıklar nedeniyle Cloud Services (klasik) ve sanal makineler (klasik) arasında farklılık gösterir.

## <a name="migration-steps"></a>Geçiş adımları
 
Müşteriler, sanal makineleri geçirmek için kullanılan dört işlemi (klasik) kullanarak Cloud Services (klasik) dağıtımlarını geçirebilir. 

1. **Geçişi doğrula** -geçişin desteklenmeyen yaygın senaryolar tarafından önlenmeyecek olduğunu doğrular.
2. **Geçişe hazırlanma** – Azure Resource Manager kaynak meta verileri çoğaltır. Kaynak meta verilerin Azure Sunucu Yöneticisi ve Azure Resource Manager arasında eşitlenmiş olduğundan emin olmak için tüm kaynaklar oluşturma/güncelleştirme/silme işlemleri için kilitlenir. Tüm okuma işlemleri, hem Cloud Services (klasik) hem de Cloud Services (genişletilmiş destek) API 'Leri kullanılarak çalışır.
3. **Geçişi durdur** -Azure Resource Manager kaynak meta verilerini kaldırır. Oluşturma/güncelleştirme/silme işlemleri için tüm kaynakların kilidini açar.
4. **Geçişi Yürüt** -kaynak meta verilerini Azure Service Manager kaldırır. Oluşturma/güncelleştirme/silme işlemleri için kaynağın kilidini açar. Tamamlama işlemi denendikten sonra durdurmaya artık izin verilmez.

>[!NOTE]
> Hazırlama, durdurma ve tamamlama ıdempotent, bu nedenle başarısız olursa, yeniden deneme sorunu çözmelidir.

:::image type="content" source="media/in-place-migration-1.png" alt-text="Görüntüde geçişle ilişkili adımların diyagramı gösterilmektedir.":::

Daha fazla bilgi için bkz. [Klasik 'Ten IaaS kaynaklarının platformdan desteklenen geçişine genel bakış Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)

## <a name="supported-resources-and-features-available-for-migration-associated-with-cloud-services-classic"></a>Cloud Services (klasik) ile ilişkili geçiş için kullanılabilen desteklenen kaynaklar ve Özellikler
-   Depolama Hesapları
-   Sanal Ağlar
-   Ağ Güvenlik Grupları
-   Ayrılmış genel IP adresleri
-   Uç nokta Access Control listeleri
-   Kullanıcı Tanımlı Yollar
-   İç yük dengeleyici
-   Anahtar kasasına Sertifika geçişi
-   Eklentiler ve uzantı (XML ve JSON tabanlı)
-   Başlangıç/on durdurma görevleri
-   Hızlandırılmış ağ ile dağıtımlar
-   Tek veya birden çok rol kullanan dağıtımlar
-   Temel yük dengeleyici
-   Giriş, örnek girişi, Iç uç noktalar
-   Dinamik genel IP adresleri
-   DNS Adı 
-   Ağ trafiği kuralları
-   Hypernet sanal ağı

## <a name="supported-configurations--migration-scenarios"></a>Desteklenen konfigürasyonlar/geçiş senaryoları
Bunlar, kaynak, özellik ve Cloud Services birleşimleri içeren en önemli senaryolardır. Bu liste ayrıntılı değildir.

| Hizmet | Yapılandırma | Yorumlar | 
|---|---|---|
| [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet) | Azure Active Directory etki alanı Hizmetleri içeren sanal ağlar. | Hem bulut hizmeti dağıtımını hem de Azure AD etki alanı hizmetlerini içeren sanal ağ desteklenir. Müşterinin ilk olarak Azure AD etki alanı Hizmetleri 'ni geçirilmesi ve ardından sanal ağı yalnızca bulut hizmeti dağıtımıyla sola geçirilmesi gerekir |
| Bulut Hizmeti | Yalnızca tek bir yuvada bir dağıtıma sahip bulut hizmeti. | Üretim ya da hazırlama yuvası dağıtımını içeren Cloud Services geçirilebilir |
| Bulut Hizmeti | Genel olarak görünen bir sanal ağda (varsayılan sanal ağ dağıtımı) değil dağıtım | Bulut hizmeti, gizli bir sanal ağda, genel olarak görünür bir sanal ağda olabilir veya herhangi bir sanal ağda yer almaz.  Gizli bir sanal ağda Cloud Services ve genel olarak görünen sanal ağlar geçiş için desteklenir. Müşteri, bir dağıtımın varsayılan bir sanal ağ içinde olup olmadığını söylemek için Validate API 'sini kullanabilir ve bu nedenle geçirilemeyeceğini tespit edebilir. |
|Bulut Hizmeti | XML uzantıları (BgInfo, Visual Studio hata ayıklayıcı, Web Dağıtımı ve uzaktan hata ayıklama). | Geçiş için tüm XML uzantıları desteklenir 
| Sanal Ağ | Birden çok Cloud Services içeren sanal ağ. | Sanal ağ, geçiş için desteklenen birden çok bulut hizmeti içerir. Sanal ağ ve içindeki tüm Cloud Services, Azure Resource Manager birlikte geçirilecek. |
| Sanal Ağ | Portal aracılığıyla oluşturulan sanal ağların geçirilmesi (. cscfg dosyasında "Grup kaynağı-grup-adı VNet-Name" kullanılmasını gerektirir)  | Geçişin bir parçası olarak, cscfg 'deki sanal ağ adı, sanal ağın Azure Resource Manager KIMLIĞINI kullanacak şekilde değiştirilir. (abonelik/abonelik-kimlik/kaynak-grup/kaynak-grup-adı/kaynak/VNET adı) <br><br>Geçişten sonra dağıtımı yönetmek için,. cscfg dosyasının yerel kopyasını sanal ağ adı yerine Azure Resource Manager KIMLIĞI kullanmaya başlamak üzere güncelleştirin. <br><br>Eski adlandırma düzenini kullanan bir. cscfg dosyası doğrulamayı geçmeyecektir. 
| Sanal Ağ | Farklı alt ağdaki rollerle dağıtım geçişi. | Farklı alt ağlardaki farklı rollere sahip bir bulut hizmeti geçiş için desteklenir. |
    

## <a name="resources-and-features-not-available-for-migration"></a>Geçiş için kullanılamayan kaynaklar ve Özellikler
Bunlar, kaynak, özellik ve Cloud Services birleşimleri içeren en önemli senaryolardır. Bu liste ayrıntılı değildir. 

| Kaynak | Sonraki adımlar/iş-çözüm | 
|---|---|
| Otomatik ölçeklendirme kuralları | Geçiş aşamasından geçer ancak kurallar bırakılır. Cloud Services geçişten sonra [kuralları yeniden oluşturun](https://docs.microsoft.com/azure/cloud-services-extended-support/configure-scaling) (genişletilmiş destek). | 
| Uyarılar | Geçiş aşamasından geçer, ancak uyarılar bırakılır. Cloud Services geçişten sonra [kuralları yeniden oluşturun](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-alerts) (genişletilmiş destek). | 
| VPN Gateway | Geçişe başlamadan önce VPN Gateway kaldırın ve ardından geçiş tamamlandıktan sonra VPN Gateway yeniden oluşturun. | 
| Express Route Gateway (yalnızca sanal ağ ile aynı abonelikte) | Geçişe başlamadan önce Express Route Gateway 'ı kaldırın ve geçiş tamamlandıktan sonra ağ geçidini yeniden oluşturun. | 
| Kota  | Kota geçirilmez. Doğrulamanın başarılı olması için geçişten önce Azure Resource Manager [yeni kota isteyin](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-resource-quota#solution) . | 
| Benzeşim Grupları | Desteklenmez. Geçişten önce tüm benzeşim gruplarını kaldırın.  | 
| Sanal [ağ eşlemesi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) kullanan sanal ağlar| Bir sanal ağı başka bir sanal ağa geçirmeden önce eşlemeyi silin, sanal ağı Kaynak Yöneticisi ' a geçirin ve eşlemeyi yeniden oluşturun. Bu, mimariye bağlı olarak kesinti oluşmasına neden olabilir. | 
| App Service ortamları içeren sanal ağlar | Desteklenmez | 
| HDInsight hizmetlerini içeren sanal ağlar | Desteklenmez. 
| Azure API Management dağıtımlarını içeren sanal ağlar | Desteklenmez. <br><br> Sanal ağı geçirmek için API Management dağıtımının sanal ağını değiştirin. Bu, kesinti süresi olmayan bir işlemdir. | 
| Klasik Express Route devreleri | Desteklenmez. <br><br>PaaS geçişine başlamadan önce bu devrelerin Azure Resource Manager geçirilmesi gerekir. Daha fazla bilgi edinmek için bkz. [ExpressRoute devreleri klasik 'dan Kaynak Yöneticisi dağıtım modeline taşıma](../expressroute/expressroute-howto-move-arm.md). |  
| Rol Tabanlı Access Control | Geçiş sonrası, kaynak değişikliklerinin, `Microsoft.ClassicCompute` `Microsoft.Compute` geçiş sonrasında RBAC ilkelerine kadar güncelleştirilmesi gerekir. | 
| Application Gateway | Desteklenmiyor. <br><br> Geçişe başlamadan önce Application Gateway kaldırın ve ardından Geçiş tamamlandığında Application Gateway yeniden oluşturun Azure Resource Manager | 

## <a name="unsupported-configurations--migration-scenarios"></a>Desteklenmeyen yapılandırmalar/geçiş senaryoları

| Yapılandırma/senaryo  | Sonraki adımlar/iş-çözüm | 
|---|---|
| Bir sanal ağda bulunmayan bazı eski dağıtımlar geçişi | Sanal ağda olmayan bazı bulut hizmeti dağıtımları geçiş için desteklenmez. <br><br> 1. dağıtımın geçişe uygun olup olmadığını denetlemek için Validate API 'sini kullanın. <br> 2. uygunsa, dağıtımlar "DefaultRdfeVnet" önekiyle birlikte bir sanal ağ altında Azure Resource Manager taşınır | 
| Dinamik IP adresleri kullanarak hem üretim hem de hazırlama yuvası dağıtımını içeren dağıtımlar geçişi | İki yuvalı bir bulut hizmetinin geçirilmesi, hazırlama yuvasının silinmesini gerektirir. Hazırlama yuvası silindikten sonra, üretim yuvasını Azure Resource Manager bağımsız bir bulut hizmeti (genişletilmiş destek) olarak geçirin. Ardından, hazırlama ortamını yeni bir bulut hizmeti (genişletilmiş destek) olarak yeniden dağıtın ve birinci bir ile takas özellikli hale getirin. | 
| Ayrılmış IP adreslerini kullanarak hem üretim hem de hazırlama yuvası dağıtımını içeren dağıtımlar geçişi | Desteklenmez. | 
| Farklı sanal ağda üretim ve hazırlama dağıtımının geçirilmesi|İki yuvalı bir bulut hizmetinin geçirilmesi, hazırlama yuvasının silinmesini gerektirir. Hazırlama yuvası silindikten sonra, üretim yuvasını Azure Resource Manager bağımsız bir bulut hizmeti (genişletilmiş destek) olarak geçirin. Yeni bir Cloud Services (genişletilmiş destek) dağıtımı daha sonra, takas edilebilir özelliği etkin olan geçirilmiş dağıtıma bağlanabilir. Eski hazırlama yuvası dağıtımının dağıtım dosyaları, bu yeni takas özellikli dağıtımı oluşturmak için yeniden kullanılabilir. | 
| Boş bulut hizmeti geçişi (dağıtım olmadan bulut hizmeti) | Desteklenmez. | 
| Uzak Masaüstü eklentisi ve uzak masaüstü uzantılarını içeren dağıtımın geçirilmesi | Seçenek 1: geçişten önce Uzak Masaüstü eklentisini kaldırın. Bu, dağıtım dosyalarında değişiklik yapılmasını gerektirir. Geçiş daha sonra devam edecektir. <br><br> 2. seçenek: Uzak Masaüstü uzantısını kaldırın ve dağıtımı geçirin. Geçiş sonrası, eklentiyi kaldırın ve uzantıyı yükledikten sonra. Bu, dağıtım dosyalarında değişiklik yapılmasını gerektirir. <br><br> Geçişten önce eklentiyi ve uzantıyı kaldırın. [Eklentiler](https://docs.microsoft.com/azure/cloud-services-extended-support/deploy-prerequisite#required-service-definition-file-csdef-updates) Cloud Services (genişletilmiş destek) üzerinde kullanım için önerilmez.| 
| PaaS ve IaaS dağıtımına sahip sanal ağlar |Desteklenmiyor <br><br> PaaS veya IaaS dağıtımlarını farklı bir sanal ağa taşıyın. Bu, kapalı kalma süresine neden olur. | 
Eski rol boyutlarını kullanan bulut hizmeti dağıtımları (küçük veya Extrabüyük gibi). | Geçiş tamamlanır, ancak rol boyutları modern rol boyutlarını kullanacak şekilde güncelleştirilir. Maliyet veya SKU özelliklerinde değişiklik yapılmaz ve sanal makine bu değişiklik için yeniden başlatılmalıdır. Tüm dağıtım yapıtlarını bu yeni modern rol boyutlarına başvuracak şekilde güncelleştirin. Daha fazla bilgi için bkz. [KULLANILABILIR VM boyutları](available-sizes.md)|
| Bulut hizmetinin farklı bir sanal ağa geçirilmesi | Desteklenmez <br><br> 1. geçiş işleminden önce dağıtımı farklı bir klasik sanal ağa taşıyın. Bu, kapalı kalma süresine neden olur. <br> 2. yeni sanal ağı Azure Resource Manager geçirin. <br><br> Veya <br><br> 1. sanal ağı Azure Resource Manager geçirin <br>2. bulut hizmetini yeni bir sanal ağa taşıyın. Bu, kapalı kalma süresine neden olur. | 
| Bir sanal ağdaki bulut hizmeti, ancak atanmış bir açık alt ağ yok | Desteklenmez. Risk azaltma, rolün yeniden başlatma (kapalı kalma süresi) gerektiren bir alt ağa taşınmasını içerir | 


## <a name="post-migration-changes"></a>Geçiş sonrası değişiklikler
Cloud Services (klasik) dağıtımı bir bulut hizmeti (genişletilmiş destek) dağıtımına dönüştürülür. Daha fazla bilgi için [Cloud Services (genişletilmiş destek) belgelerine](deploy-prerequisite.md) bakın.  

### <a name="changes-to-deployment-files"></a>Dağıtım dosyalarında yapılan değişiklikler 

Dağıtım dosyalarının Azure Resource Manager ve Cloud Services (genişletilmiş destek) gereksinimlerine uyum sağlamak için müşterinin. csdef ve. cscfg dosyasında küçük değişiklikler yapılır. Geçiş sonrası yeni dağıtım dosyalarınızı alır veya mevcut dosyaları güncelleştirir. Bu, güncelleştirme/silme işlemleri için gerekli olacaktır.  

- Sanal ağ,. cscfg dosyasının NetworkConfiguration bölümünde yalnızca kaynak adı yerine tam Azure Resource Manager kaynak KIMLIĞI kullanır. Örneğin, `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`. Bulut hizmeti ile aynı kaynak grubuna ait olan sanal ağlar için, yalnızca sanal ağ adını kullanarak. cscfg dosyasını yeniden güncelleştirmeyi tercih edebilirsiniz.  

- Küçük, büyük, Extrabüyük gibi klasik Boyutlar, Standard_A * olan yeni boyut adlarına göre değiştirilmiştir. Boyut adlarının. csdef dosyasındaki yeni adlarıyla değiştirilmesi gerekir. Daha fazla bilgi için bkz. [Cloud Services (genişletilmiş destek) dağıtım önkoşulları](deploy-prerequisite.md#required-service-definition-file-csdef-updates)

- Dağıtım dosyalarının en son kopyasını almak için Get API 'sini kullanın. 
    - [Portalı](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell#export-resource-groups-to-templates), [CLI](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli#export-resource-groups-to-templates)ve [REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate) 'yi kullanarak şablonu edinme 
    - [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) veya [REST API](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package)kullanarak. csdef dosyasını alın. 
    - [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) veya [REST API](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package)kullanarak. cscfg dosyasını alın. 
    
 

### <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>Müşterinin Otomasyonu, CI/CD işlem hattı, Özel betikler, özel panolar, özel araçlar, vb. değişiklikler.  

Müşterilerin, dağıtımını yönetmek üzere yeni API 'Leri/komutları kullanmaya başlamak için araçları ve Otomasyonu güncelleştirmeleri güncelleştirmesi gerekir. Müşteri, bu değişikliğin bir parçası olarak Azure Resource Manager/Cloud Services (genişletilmiş destek) yeni özelliklerini ve yeteneklerini kolayca benimseyebilirler. 

- Kaynak ve kaynak grubu adlarında yapılan değişiklikler geçiş sonrası
    - Geçişin bir parçası olarak, bulut hizmeti, genel IP adresleri vs. gibi birkaç kaynak adı değiştirin. Bu değişikliklerin, bulut hizmeti 'nin güncelleştirmesinden önce dağıtım dosyalarına yansıtılmasına gerek duyabilirsiniz. [Değişen kaynakların adları hakkında daha fazla bilgi edinin](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration).  

- Bulut hizmetlerini yönetmek ve ölçeklendirmek için gereken kuralları ve ilkeleri yeniden oluşturma 
    - [Otomatik ölçeklendirme kuralları](configure-scaling.md) geçirilmez. Geçişten sonra otomatik ölçeklendirme kurallarını yeniden oluşturun.  
    - [Uyarılar](enable-alerts.md) geçirilmez. Geçişten sonra, uyarıları yeniden oluşturun.
    - Key Vault, herhangi bir erişim ilkesi olmadan oluşturulur. Sertifikalarınızı görüntülemek veya yönetmek için Key Vault [uygun Ilkeleri oluşturun](../key-vault/general/assign-access-policy-portal.md) . Sertifikalar gizli olarak adlandırılan sekmede ayarlar altında görünür olacaktır.

## <a name="next-steps"></a>Sonraki adımlar
- [IaaS kaynaklarının klasik ile Azure Resource Manager geçişine genel bakış](../virtual-machines/migration-classic-resource-manager-overview.md)
- [Azure Portal](in-place-migration-portal.md) kullanarak Cloud Services (genişletilmiş destek) öğesine geçirin
- [PowerShell](in-place-migration-powershell.md) kullanarak Cloud Services (genişletilmiş destek) 'e geçirme
