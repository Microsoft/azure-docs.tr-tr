---
title: Azure CLı kullanarak VM 'Leri Kaynak Yöneticisi geçirme
description: Bu makalede, Azure CLı kullanılarak klasik 'ten Azure Resource Manager kaynakların klasik sürümüne geçişi gösterilmektedir
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 69107052d84f28dfd08f59dec40ea66eca79ecaa
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035770"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Azure CLı kullanarak IaaS kaynaklarını klasik 'dan Azure Resource Manager geçirme
Bu adımlarda, klasik dağıtım modelinden Azure Resource Manager dağıtım modeline hizmet olarak altyapı (IaaS) kaynaklarını geçirmek için Azure komut satırı arabirimi (CLı) komutlarının nasıl kullanılacağı gösterilmektedir. Makale, [Azure klasık CLI](../../cli-install-nodejs.md)'yi gerektirir. Azure CLı yalnızca Azure Resource Manager kaynakları için geçerli olduğundan, bu geçiş için kullanılamaz.

> [!NOTE]
> Burada açıklanan tüm işlemler ıdempotent. Desteklenmeyen bir özellik veya yapılandırma hatası dışında bir sorununuz varsa, hazırlama, durdurma veya işleme işlemini yeniden denemeniz önerilir. Platform daha sonra eylemi yeniden dener.
> 
> 

<br>
Bir geçiş işlemi sırasında hangi adımların yürütülmesi gerektiği sırayı belirlemek için bir akış çizelgesi aşağıda verilmiştir

![Geçiş adımlarını gösteren ekran görüntüsü](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>1\. Adım: geçişe hazırlanma
IaaS kaynaklarını klasik ' ten Kaynak Yöneticisi geçirmeyi değerlendirirken önerdiğimiz birkaç en iyi yöntem aşağıda verilmiştir:

* [Desteklenmeyen yapılandırmaların veya özelliklerin listesini](../windows/migration-classic-resource-manager-overview.md)okuyun. Desteklenmeyen yapılandırmalar veya özellikler kullanan sanal makineleriniz varsa, özellik/yapılandırma desteğinin duyurulmalarını beklemeniz önerilir. Alternatif olarak, bu özelliği kaldırabilir veya gereksinimlerinize uygun olan geçiş özelliğini etkinleştirmek için bu yapılandırmanın dışına geçebilirsiniz.
* Altyapınızı ve uygulamalarınızı hemen dağıtan otomatikleştirilmiş betikleriniz varsa, geçiş için bu betikleri kullanarak benzer bir test kurulumu oluşturmayı deneyin. Alternatif olarak, Azure portal kullanarak örnek ortamları da ayarlayabilirsiniz.

> [!IMPORTANT]
> Application Gateway 'ler, klasik ' ten Kaynak Yöneticisi geçiş için şu anda desteklenmiyor. Bir Application Gateway ile klasik bir sanal ağı geçirmek için, ağı taşımak üzere hazırlama işlemini çalıştırmadan önce ağ geçidini kaldırın. Geçişi tamamladıktan sonra, Azure Resource Manager ağ geçidini yeniden bağlayın. 
>
>Başka bir abonelikte ExpressRoute devrelerine bağlanan ExpressRoute ağ geçitleri otomatik olarak geçirilemez. Bu gibi durumlarda, ExpressRoute ağ geçidini kaldırın, sanal ağı geçirin ve ağ geçidini yeniden oluşturun. Daha fazla bilgi için lütfen bkz. [ExpressRoute devreleri ve ilişkili sanal ağları klasik Kaynak Yöneticisi dağıtım modeline geçirme](../../expressroute/expressroute-migration-classic-resource-manager.md) .
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>2\. Adım: aboneliğinizi ayarlama ve sağlayıcıyı kaydetme
Geçiş senaryolarında, ortamınızı hem klasik hem de Kaynak Yöneticisi için ayarlamanız gerekir. [Azure CLI](../../cli-install-nodejs.md) 'yı yükleyip [aboneliğinizi seçin](/cli/azure/authenticate-azure-cli).

Hesabınızda oturum açın.

    azure login

Aşağıdaki komutu kullanarak Azure aboneliğini seçin.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> Kayıt tek seferlik bir adımdır, ancak geçiş girişiminden önce bir kez yapılması gerekir. Kaydolmadan aşağıdaki hata iletisini görürsünüz 
> 
> *Rozet Isteği: abonelik geçiş için kaydedilmemiş.* 
> 
> 

Aşağıdaki komutu kullanarak geçiş kaynak sağlayıcısına kaydolun. Bazı durumlarda bu komutun zaman aşımına uğraydığına unutmayın. Ancak kayıt başarılı olur.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Lütfen kaydın tamamlanmasını beş dakika bekleyin. Aşağıdaki komutu kullanarak onay durumunu kontrol edebilirsiniz. Devam etmeden önce RegistrationState `Registered` olduğundan emin olun.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Artık CLı 'yı `asm` moduna geçirin.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>3\. Adım: geçerli dağıtımınızın veya VNET 'nizin Azure bölgesinde yeterli sayıda Azure Resource Manager sanal makineye sahip olduğunuzdan emin olun
Bu adım için `arm` moduna geçmeniz gerekir. Bunu aşağıdaki komutla yapın.

```
azure config mode arm
```

Azure Resource Manager içinde sahip olduğunuz vCPU 'ların geçerli sayısını denetlemek için aşağıdaki CLı komutunu kullanabilirsiniz. VCPU kotaları hakkında daha fazla bilgi için bkz [. sınırlara ve Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Bu adımı doğrulamayı tamamladıktan sonra, `asm` moduna geri dönebilirsiniz.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>4\. Adım: 1. seçenek-bir bulut hizmetindeki sanal makineleri geçirme
Aşağıdaki komutu kullanarak bulut hizmetleri listesini alın ve ardından geçirmek istediğiniz bulut hizmetini seçin. Bulut hizmetindeki VM 'Lerin bir sanal ağda veya Web/çalışan rollerinin varsa, bir hata iletisi alınacağını unutmayın.

    azure service list

Ayrıntılı çıktıdan bulut hizmetinin dağıtım adını almak için aşağıdaki komutu çalıştırın. Çoğu durumda, dağıtım adı bulut hizmeti adıyla aynıdır.

    azure service show <serviceName> -vv

İlk olarak, aşağıdaki komutları kullanarak bulut hizmetini geçirebilmeniz gerektiğini doğrulayın:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Bulut hizmetindeki sanal makineleri geçiş için hazırlayın. Aralarından seçim yapabileceğiniz iki seçeneğiniz vardır.

VM 'Leri platform tarafından oluşturulan bir sanal ağa geçirmek istiyorsanız aşağıdaki komutu kullanın.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Kaynak Yöneticisi dağıtım modelinde var olan bir sanal ağa geçiş yapmak istiyorsanız aşağıdaki komutu kullanın.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Hazırlama işlemi başarılı olduktan sonra, VM 'lerin geçiş durumunu almak ve `Prepared` durumunda olduklarından emin olmak için ayrıntılı çıktıya göz atabilirsiniz.

    azure vm show <vmName> -vv

CLı veya Azure portal kullanarak hazırlanan kaynaklar için yapılandırmayı denetleyin. Geçişe hazırsanız ve eski durumuna geri dönmek istiyorsanız aşağıdaki komutu kullanın.

    azure service deployment abort-migration <serviceName> <deploymentName>

Hazırlanan yapılandırma iyi görünüyorsa, aşağıdaki komutu kullanarak kaynakları ileri taşıyabilir ve kaydedebilirsiniz.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>4\. Adım: 2. seçenek-sanal bir ağdaki sanal makineleri geçirme
Geçirmek istediğiniz sanal ağı seçin. Sanal ağ, desteklenmeyen yapılandırmalara sahip web/çalışan rolleri veya VM 'Ler içeriyorsa, bir doğrulama hata iletisi alınacağını unutmayın.

Aşağıdaki komutu kullanarak abonelikteki tüm sanal ağları alın.

    azure network vnet list

Çıktı şuna benzer şekilde görünecektir:

![Tüm sanal ağ adının vurgulandığı komut satırının ekran görüntüsü.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

Yukarıdaki örnekte **Virtualnetworkname** tüm **"Group classicubuntu16 classicubuntu16"** adıdır.

İlk olarak, aşağıdaki komutu kullanarak sanal ağı geçirebiliyorsanız doğrulayın:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Aşağıdaki komutu kullanarak, geçiş için tercih ettiğiniz sanal ağı hazırlayın.

    azure network vnet prepare-migration <virtualNetworkName>

CLı veya Azure portal kullanarak hazırlanan sanal makinelerin yapılandırmasını denetleyin. Geçişe hazırsanız ve eski durumuna geri dönmek istiyorsanız aşağıdaki komutu kullanın.

    azure network vnet abort-migration <virtualNetworkName>

Hazırlanan yapılandırma iyi görünüyorsa, aşağıdaki komutu kullanarak kaynakları ileri taşıyabilir ve kaydedebilirsiniz.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>5\. Adım: depolama hesabı geçirme
Sanal makineleri geçirmeyi tamamladıktan sonra, depolama hesabını geçirmeniz önerilir.

Aşağıdaki komutu kullanarak depolama hesabını geçiş için hazırlama

    azure storage account prepare-migration <storageAccountName>

CLı veya Azure portal kullanarak hazırlanan depolama hesabının yapılandırmasını denetleyin. Geçişe hazırsanız ve eski durumuna geri dönmek istiyorsanız aşağıdaki komutu kullanın.

    azure storage account abort-migration <storageAccountName>

Hazırlanan yapılandırma iyi görünüyorsa, aşağıdaki komutu kullanarak kaynakları ileri taşıyabilir ve kaydedebilirsiniz.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Sonraki adımlar

* [IaaS kaynaklarının klasik ile Azure Resource Manager geçişine genel bakış](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Klasik modelden Azure Resource Manager’a platform destekli geçişe ayrıntılı teknik bakış](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [IaaS kaynaklarının Klasik’ten Azure Resource Manager’a geçişini planlama](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için PowerShell 'i kullanma](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarının klasik 'dan Azure Resource Manager geçişine yardımcı olacak topluluk araçları](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En sık karşılaşılan geçiş hatalarını gözden geçirme](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [IaaS kaynaklarını klasik konumundan Azure Resource Manager geçirme hakkında en sık sorulan soruları gözden geçirin](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
