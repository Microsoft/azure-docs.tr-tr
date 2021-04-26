---
title: PowerShell kullanarak Azure Cloud Services 'a (genişletilmiş destek) geçiş
description: PowerShell kullanarak Azure Cloud Services (klasik) ile Azure Cloud Services (genişletilmiş destek) geçirme
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: eea49a41e81e7e580becce815ff91aff6aa430d6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287006"
---
# <a name="migrate-to-azure-cloud-services-extended-support-using-powershell"></a>PowerShell kullanarak Azure Cloud Services 'a (genişletilmiş destek) geçiş

Bu adımlar, [Cloud Services (klasik)](../cloud-services/cloud-services-choose-me.md) ' den [Cloud Services (genişletilmiş destek)](overview.md)arasında geçiş yapmak için Azure PowerShell komutlarının nasıl kullanılacağını gösterir.

> [!IMPORTANT]
> Geçiş aracını kullanarak Cloud Services (klasik) Cloud Services (genişletilmiş destek) ' e geçiş Şu anda genel önizlemededir. Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="1-plan-for-migration"></a>1) geçiş planı
Planlama, başarılı bir geçiş deneyimi için en önemli adımdır. Herhangi bir geçiş adımını çalıştırmadan önce, [Cloud Services (genişletilmiş destek) genel bakış](overview.md) ve [IaaS kaynaklarının klasik 'Ten Azure Resource Manager geçişini planlama](../virtual-machines/migration-classic-resource-manager-plan.md) konusuna bakın. 

## <a name="2-install-the-latest-version-of-powershell"></a>2) PowerShell 'in en son sürümünü yükler
Azure PowerShell yüklemek için iki ana seçenek vardır: [PowerShell Galerisi](https://www.powershellgallery.com/profiles/azure-sdk/) veya [Web Platformu Yükleyicisi (WebPI)](https://aka.ms/webpi-azps). WebPI aylık güncelleştirmeleri alır. PowerShell Galerisi güncelleştirmeleri sürekli olarak alır. Bu makale, Azure PowerShell sürüm 2.1.0 ' i temel alır.

Yükleme yönergeleri için bkz. [Azure PowerShell yükleme ve yapılandırma](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0&preserve-view=true).

## <a name="3-ensure-admin-permissions"></a>3) yönetici izinlerinin olduğundan emin olun
Bu geçişi gerçekleştirmek için, [Azure Portal](https://portal.azure.com)abonelik için bir ortak yönetici olarak eklenmeli.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Hub** menüsünde, **abonelik**' ı seçin. Bunu görmüyorsanız, **tüm hizmetler**' i seçin.
3. Uygun abonelik girişini bulun ve ardından **rol alanım** ' a bakın. Coadministrator için, değer *Hesap Yöneticisi* olmalıdır.

Ortak yönetici ekleyemediğinizde, abonelik için bir hizmet yöneticisiyle veya ortak yönetici ile iletişim kurun.

## <a name="4-register-the-classic-provider-and-cloudservice-feature"></a>4) klasik sağlayıcı ve CloudService özelliğini kaydetme
İlk olarak bir PowerShell istemi başlatın. Geçiş için ortamınızı hem klasik hem de Kaynak Yöneticisi için ayarlayın.

Kaynak Yöneticisi modeli için hesabınızda oturum açın.

```powershell
Connect-AzAccount
```

Aşağıdaki komutu kullanarak kullanılabilir abonelikleri alın:

```powershell
Get-AzSubscription | Sort Name | Select Name
```

Geçerli oturum için Azure aboneliğinizi ayarlayın. Bu örnek, varsayılan abonelik adını **Azure aboneliğim** olarak ayarlar. Örnek abonelik adını kendi adınızla değiştirin.

```powershell
Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

Aşağıdaki komutu kullanarak geçiş kaynak sağlayıcısına kaydolun:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```
> [!NOTE]
> Kayıt tek seferlik bir adımdır, ancak geçişe kalkışmadan önce bunu bir kez yapmanız gerekir. Kaydolmadan aşağıdaki hata iletisini görürsünüz:
>
> *Rozet Isteği: abonelik geçiş için kaydedilmemiş.*

Aboneliğiniz için CloudServices özelliğini kaydedin. Kayıtların tamamlanması birkaç dakika sürebilir. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Kaydın tamamlanabilmesi için beş dakika bekleyin. 

Aşağıdaki komutu kullanarak klasik sağlayıcı onayının durumunu denetleyin:

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Aşağıdaki kullanarak kayıt durumunu denetleyin:  
```powershell
Get-AzProviderFeature -FeatureName CloudServices
```

Devam etmeden önce RegistrationState 'in `Registered` her ikisi için de olduğundan emin olun.

Klasik dağıtım modeline geçmeden önce, geçerli dağıtımınızın veya sanal ağınızın Azure bölgesinde yeterli sayıda Azure Resource Manager vCPU kotasına sahip olduğunuzdan emin olun. Azure Resource Manager ' de mevcut vCPU sayısını denetlemek için aşağıdaki PowerShell komutunu kullanabilirsiniz. VCPU kotaları hakkında daha fazla bilgi için bkz. [sınırlara ve Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

Bu örnek **Batı ABD** bölgesindeki kullanılabilirliği denetler. Örnek bölge adını kendi adınızla değiştirin.

```powershell
Get-AzVMUsage -Location "West US"
```

Şimdi, klasik dağıtım modeli için hesabınızda oturum açın.

```powershell
Add-AzureAccount
```

Aşağıdaki komutu kullanarak kullanılabilir abonelikleri alın:

```powershell
Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Geçerli oturum için Azure aboneliğinizi ayarlayın. Bu örnekte, varsayılan abonelik **Azure aboneliğime** ayarlanır. Örnek abonelik adını kendi adınızla değiştirin.

```powershell
Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="5-migrate-your-cloud-services"></a>5) Cloud Services geçirin 
* [Sanal ağda bulunmayan bir bulut hizmetini geçirme](#51-option-1---migrate-a-cloud-service-not-in-a-virtual-network)
* [Bir sanal ağdaki bulut hizmetini geçirme](#51-option-2---migrate-a-cloud-service-in-a-virtual-network)

> [!NOTE]
> Burada açıklanan tüm işlemler ıdempotent. Desteklenmeyen bir özellik veya yapılandırma hatası dışında bir sorununuz varsa, hazırlama, durdurma veya işleme işlemini yeniden denemeniz önerilir. Platform daha sonra eylemi yeniden dener.


### <a name="51-option-1---migrate-a-cloud-service-not-in-a-virtual-network"></a>5,1) seçenek 1-Sanal ağda olmayan bir bulut hizmetini geçirme
Aşağıdaki komutu kullanarak bulut hizmetleri listesini alın. Ardından, geçirmek istediğiniz bulut hizmetini seçin.

```powershell
Get-AzureService | ft Servicename
```

Bulut hizmeti için dağıtım adını alın. Bu örnekte hizmet adı **hizmetim**. Örnek hizmet adını kendi hizmet adınızla değiştirin.

```powershell
$serviceName = "My Service"
$deployment = Get-AzureDeployment -ServiceName $serviceName
$deploymentName = $deployment.DeploymentName
```

İlk olarak, aşağıdaki komutları kullanarak bulut hizmetini geçirebileceğiniz doğrulayın:

```powershell
$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
$validate.ValidationMessages
 ```

Aşağıdaki komut, geçişi engelleyen tüm uyarıları ve hataları görüntüler. Doğrulama başarılı olursa, hazırla adımına geçebilirsiniz.

```powershell
Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```

### <a name="51-option-2---migrate-a-cloud-service-in-a-virtual-network"></a>5,1) seçenek 2-bir sanal ağdaki bulut hizmetini geçirme

Bir sanal ağ içinde bir bulut hizmetini geçirmek için sanal ağı geçirin. Bulut hizmeti sanal ağla otomatik olarak geçirilir.

> [!NOTE]
> Sanal ağ adı, yeni portalda gösterilenden farklı olabilir. Yeni Azure portal adı olarak görüntüler `[vnet-name]` , ancak gerçek sanal ağ adı türündedir `Group [resource-group-name] [vnet-name]` . Geçişe başlamadan önce, komutu kullanarak gerçek sanal ağ adını bulun `Get-AzureVnetSite | Select -Property Name` veya eski Azure Portal görüntüleyin. 

Bu örnek, sanal ağ adını **Myvnet** olarak ayarlar. Örnek sanal ağ adını kendi adınızla değiştirin.

```powershell
$vnetName = "myVnet"
```

İlk olarak, aşağıdaki komutu kullanarak sanal ağı geçirebileceğiniz doğrulayın:

```powershell
Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Aşağıdaki komut, geçişi engelleyen tüm uyarıları ve hataları görüntüler. Doğrulama başarılı olursa, aşağıdaki hazırlama adımıyla devam edebilirsiniz:

```powershell
Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Azure PowerShell veya Azure portal kullanarak hazırlanan bulut hizmeti için yapılandırmayı denetleyin. Geçişe hazırsanız ve eski durumuna geri dönmek istiyorsanız aşağıdaki komutu kullanın:

```powershell
Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Hazırlanan yapılandırma iyi görünüyorsa, aşağıdaki komutu kullanarak kaynakları ileriye doğru taşıyabilir ve kaydedebilirsiniz:

```powershell
Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```


## <a name="next-steps"></a>Sonraki adımlar
Dağıtım dosyalarındaki değişiklikleri, otomasyonu ve yeni Cloud Services (genişletilmiş destek) dağıtımınızın diğer özniteliklerini görmek için [geçiş sonrası değişiklikler](in-place-migration-overview.md#post-migration-changes) bölümünü gözden geçirin. 
