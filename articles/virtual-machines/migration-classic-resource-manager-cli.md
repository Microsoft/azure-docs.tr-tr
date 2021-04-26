---
title: Azure CLı kullanarak VM 'Leri Kaynak Yöneticisi geçirme
description: Bu makalede, Azure CLı kullanılarak klasik 'dan Azure Resource Manager kaynaklara yönelik platform tarafından desteklenen geçiş anlatılmaktadır.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurecli
ms.openlocfilehash: 671b27f927c91397d2aacd98cb7b500d8197d1c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669339"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Azure CLI kullanarak IaaS kaynaklarını klasik modelden Azure Resource Manager’a geçirme

> [!IMPORTANT]
> Bugün, IaaS VM 'lerinin yaklaşık %90 ' u [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)kullanıyor. 28 Şubat 2020 itibariyle klasik VM 'Ler kullanımdan kaldırılmıştır ve 1 Mart 2023 tarihinde tamamen kullanımdan kaldırılacaktır. Bu kullanımdan kaldırma ve [nasıl etkilediği](classic-vm-deprecation.md#how-does-this-affect-me)hakkında [daha fazla bilgi edinin]( https://aka.ms/classicvmretirement) .

Bu adımlarda, klasik dağıtım modelinden Azure Resource Manager dağıtım modeline hizmet olarak altyapı (IaaS) kaynaklarını geçirmek için Azure komut satırı arabirimi (CLı) komutlarının nasıl kullanılacağı gösterilmektedir. Makale, [Azure klasık CLI](/cli/azure/install-classic-cli)'yi gerektirir. Azure CLı yalnızca Azure Resource Manager kaynakları için geçerli olduğundan, bu geçiş için kullanılamaz.

> [!NOTE]
> Burada açıklanan tüm işlemler ıdempotent. Desteklenmeyen bir özellik veya yapılandırma hatası dışında bir sorununuz varsa, hazırlama, durdurma veya işleme işlemini yeniden denemeniz önerilir. Platform daha sonra eylemi yeniden dener.
> 
> 

<br>
Bir geçiş işlemi sırasında hangi adımların yürütülmesi gerektiği sırayı belirlemek için bir akış çizelgesi aşağıda verilmiştir

![Geçiş adımlarını gösteren ekran görüntüsü](./media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>1. Adım: geçişe hazırlanma
IaaS kaynaklarını klasik ' ten Kaynak Yöneticisi geçirmeyi değerlendirirken önerdiğimiz birkaç en iyi yöntem aşağıda verilmiştir:

* [Desteklenmeyen yapılandırmaların veya özelliklerin listesini](migration-classic-resource-manager-overview.md)okuyun. Desteklenmeyen yapılandırmalar veya özellikler kullanan sanal makineleriniz varsa, özellik/yapılandırma desteğinin duyurulmalarını beklemeniz önerilir. Alternatif olarak, bu özelliği kaldırabilir veya gereksinimlerinize uygun olan geçiş özelliğini etkinleştirmek için bu yapılandırmanın dışına geçebilirsiniz.
* Altyapınızı ve uygulamalarınızı hemen dağıtan otomatikleştirilmiş betikleriniz varsa, geçiş için bu betikleri kullanarak benzer bir test kurulumu oluşturmayı deneyin. Alternatif olarak, Azure portal kullanarak örnek ortamları da ayarlayabilirsiniz.

> [!IMPORTANT]
> Application Gateway 'ler, klasik ' ten Kaynak Yöneticisi geçiş için şu anda desteklenmiyor. Bir Application Gateway ile klasik bir sanal ağı geçirmek için, ağı taşımak üzere hazırlama işlemini çalıştırmadan önce ağ geçidini kaldırın. Geçişi tamamladıktan sonra, Azure Resource Manager ağ geçidini yeniden bağlayın. 
>
>Başka bir abonelikte ExpressRoute devrelerine bağlanan ExpressRoute ağ geçitleri otomatik olarak geçirilemez. Bu gibi durumlarda, ExpressRoute ağ geçidini kaldırın, sanal ağı geçirin ve ağ geçidini yeniden oluşturun. Daha fazla bilgi için lütfen bkz. [ExpressRoute devreleri ve ilişkili sanal ağları klasik Kaynak Yöneticisi dağıtım modeline geçirme](../expressroute/expressroute-migration-classic-resource-manager.md) .
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>2. Adım: aboneliğinizi ayarlama ve sağlayıcıyı kaydetme
Geçiş senaryolarında, ortamınızı hem klasik hem de Kaynak Yöneticisi için ayarlamanız gerekir. [Azure CLI](/cli/azure/install-classic-cli) 'yı yükleyip [aboneliğinizi seçin](/cli/azure/authenticate-azure-cli).

Hesabınızda oturum açın.

```azurecli
azure login
```

Aşağıdaki komutu kullanarak Azure aboneliğini seçin.

```azurecli
azure account set "<azure-subscription-name>"
```

> [!NOTE]
> Kayıt tek seferlik bir adımdır, ancak geçiş girişiminden önce bir kez yapılması gerekir. Kaydolmadan aşağıdaki hata iletisini görürsünüz 
> 
> *Rozet Isteği: abonelik geçiş için kaydedilmemiş.* 
> 
> 

Aşağıdaki komutu kullanarak geçiş kaynak sağlayıcısına kaydolun. Bazı durumlarda bu komutun zaman aşımına uğraydığına unutmayın. Ancak kayıt başarılı olur.

```azurecli
azure provider register Microsoft.ClassicInfrastructureMigrate
```

Lütfen kaydın tamamlanmasını beş dakika bekleyin. Aşağıdaki komutu kullanarak onay durumunu kontrol edebilirsiniz. Devam etmeden önce RegistrationState 'in olduğundan emin olun `Registered` .

```azurecli
azure provider show Microsoft.ClassicInfrastructureMigrate
```

Şimdi CLı 'yi moda geçirin `asm` .

```azurecli
azure config mode asm
```

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>3. Adım: geçerli dağıtımınızın veya VNET 'nizin Azure bölgesinde yeterli sayıda Azure Resource Manager sanal makineye sahip olduğunuzdan emin olun
Bu adım için moda geçmeniz gerekir `arm` . Bunu aşağıdaki komutla yapın.

```azurecli
azure config mode arm
```

Azure Resource Manager içinde sahip olduğunuz vCPU 'ların geçerli sayısını denetlemek için aşağıdaki CLı komutunu kullanabilirsiniz. VCPU kotaları hakkında daha fazla bilgi için bkz. [sınırlara ve Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

```azurecli
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Bu adımı doğrulamayı tamamladıktan sonra moda geri dönebilirsiniz `asm` .

```azurecli
azure config mode asm
```

## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>4. Adım: 1. seçenek-bir bulut hizmetindeki sanal makineleri geçirme
Aşağıdaki komutu kullanarak bulut hizmetleri listesini alın ve ardından geçirmek istediğiniz bulut hizmetini seçin. Bulut hizmetindeki VM 'Lerin bir sanal ağda veya Web/çalışan rollerinin varsa, bir hata iletisi alınacağını unutmayın.

```azurecli
azure service list
```

Ayrıntılı çıktıdan bulut hizmetinin dağıtım adını almak için aşağıdaki komutu çalıştırın. Çoğu durumda, dağıtım adı bulut hizmeti adıyla aynıdır.

```azurecli
azure service show <serviceName> -vv
```

İlk olarak, aşağıdaki komutları kullanarak bulut hizmetini geçirebilmeniz gerektiğini doğrulayın:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Bulut hizmetindeki sanal makineleri geçiş için hazırlayın. Aralarından seçim yapabileceğiniz iki seçeneğiniz vardır.

VM 'Leri platform tarafından oluşturulan bir sanal ağa geçirmek istiyorsanız aşağıdaki komutu kullanın.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""
```

Kaynak Yöneticisi dağıtım modelinde var olan bir sanal ağa geçiş yapmak istiyorsanız aşağıdaki komutu kullanın.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>
```

Hazırlama işlemi başarılı olduktan sonra, VM 'lerin geçiş durumunu almak ve durumunda olduklarından emin olmak için ayrıntılı çıktıyı gözden geçirebilmeniz gerekir `Prepared` .

```azurecli
azure vm show <vmName> -vv
```

CLı veya Azure portal kullanarak hazırlanan kaynaklar için yapılandırmayı denetleyin. Geçişe hazırsanız ve eski durumuna geri dönmek istiyorsanız aşağıdaki komutu kullanın.

```azurecli
azure service deployment abort-migration <serviceName> <deploymentName>
```

Hazırlanan yapılandırma iyi görünüyorsa, aşağıdaki komutu kullanarak kaynakları ileri taşıyabilir ve kaydedebilirsiniz.

```azurecli
azure service deployment commit-migration <serviceName> <deploymentName>
```

## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>4. Adım: 2. seçenek-sanal bir ağdaki sanal makineleri geçirme
Geçirmek istediğiniz sanal ağı seçin. Sanal ağ, desteklenmeyen yapılandırmalara sahip web/çalışan rolleri veya VM 'Ler içeriyorsa, bir doğrulama hata iletisi alınacağını unutmayın.

Aşağıdaki komutu kullanarak abonelikteki tüm sanal ağları alın.

```azurecli
azure network vnet list
```

Çıkış şuna benzer olacaktır:

![Tüm sanal ağ adının vurgulandığı komut satırının ekran görüntüsü.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

Yukarıdaki örnekte **Virtualnetworkname** tüm **"Group classicubuntu16 classicubuntu16"** adıdır.

İlk olarak, aşağıdaki komutu kullanarak sanal ağı geçirebiliyorsanız doğrulayın:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Aşağıdaki komutu kullanarak, geçiş için tercih ettiğiniz sanal ağı hazırlayın.

```azurecli
azure network vnet prepare-migration <virtualNetworkName>
```

CLı veya Azure portal kullanarak hazırlanan sanal makinelerin yapılandırmasını denetleyin. Geçişe hazırsanız ve eski durumuna geri dönmek istiyorsanız aşağıdaki komutu kullanın.

```azurecli
azure network vnet abort-migration <virtualNetworkName>
```

Hazırlanan yapılandırma iyi görünüyorsa, aşağıdaki komutu kullanarak kaynakları ileri taşıyabilir ve kaydedebilirsiniz.

```azurecli
azure network vnet commit-migration <virtualNetworkName>
```

## <a name="step-5-migrate-a-storage-account"></a>5. Adım: depolama hesabı geçirme
Sanal makineleri geçirmeyi tamamladıktan sonra, depolama hesabını geçirmeniz önerilir.

Aşağıdaki komutu kullanarak depolama hesabını geçiş için hazırlama

```azurecli
azure storage account prepare-migration <storageAccountName>
```

CLı veya Azure portal kullanarak hazırlanan depolama hesabının yapılandırmasını denetleyin. Geçişe hazırsanız ve eski durumuna geri dönmek istiyorsanız aşağıdaki komutu kullanın.

```azurecli
azure storage account abort-migration <storageAccountName>
```

Hazırlanan yapılandırma iyi görünüyorsa, aşağıdaki komutu kullanarak kaynakları ileri taşıyabilir ve kaydedebilirsiniz.

```azurecli
azure storage account commit-migration <storageAccountName>
```

## <a name="next-steps"></a>Sonraki adımlar

* [IaaS kaynaklarının klasik ile Azure Resource Manager geçişine genel bakış](migration-classic-resource-manager-overview.md)
* [Klasik modelden Azure Resource Manager’a platform destekli geçişe ayrıntılı teknik bakış](migration-classic-resource-manager-deep-dive.md)
* [IaaS kaynaklarının Klasik’ten Azure Resource Manager’a geçişini planlama](migration-classic-resource-manager-plan.md)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için PowerShell 'i kullanma](migration-classic-resource-manager-ps.md)
* [IaaS kaynaklarının klasik 'dan Azure Resource Manager geçişine yardımcı olacak topluluk araçları](migration-classic-resource-manager-community-tools.md)
* [En sık karşılaşılan geçiş hatalarını gözden geçirme](migration-classic-resource-manager-errors.md)
* [IaaS kaynaklarını klasik konumundan Azure Resource Manager geçirme hakkında en sık sorulan soruları gözden geçirin](migration-classic-resource-manager-faq.md)
