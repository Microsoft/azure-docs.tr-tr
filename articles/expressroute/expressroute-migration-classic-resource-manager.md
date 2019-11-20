---
title: "Azure ExpressRoute: klasik VNET 'leri Kaynak Yöneticisi geçirin"
description: Bu sayfa devreniz taşıdıktan sonra ExpressRoute ilişkili sanal ağları Resource Manager'a geçiş işlemini açıklamaktadır.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: cherylmc
ms.openlocfilehash: 2b74523f42a1f57805388aa8c60cf1ad5b1d1331
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080078"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>ExpressRoute ilişkili sanal ağları Klasikten Resource Manager'a geçiş

Bu makalede, sanal ağları ExpressRoute ilişkili ExpressRoute devreniz taşıdıktan sonra Klasik dağıtım modelinden Azure Resource Manager dağıtım modeline geçirme açıklanmaktadır. 

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Azure PowerShell modüllerinin en son sürümlerine sahip olduğunuzu doğrulayın. Daha fazla bilgi için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/overview). PowerShell hizmeti yönetim modülünü (klasik dağıtım modeli için gereklidir) yüklemek için, bkz. [Azure PowerShell Service Management modülünü yükleme](/powershell/azure/servicemanagement/install-azure-ps).
* Geçirdiğinizden emin emin [önkoşulları](expressroute-prerequisites.md), [yönlendirme gereksinimleri](expressroute-routing.md), ve [iş akışları](expressroute-workflows.md) yapılandırmaya başlamadan önce.
* Altında sağlanan bilgileri gözden [bir ExpressRoute bağlantı hattını Klasikten Resource Manager'a taşıma](expressroute-move.md). Tam olarak sınırlar ve sınırlamalar anladığınızdan emin olun.
* Bağlantı hattı Klasik dağıtım modelinde tam olarak işlevsel olduğunu doğrulayın.
* Resource Manager dağıtım modelinde oluşturulan bir kaynak grubu olduğundan emin olun.
* Aşağıdaki kaynak geçişi belgeleri inceleyin:

    * [Iaas kaynaklarının Klasik modelden Azure Resource Manager'a Platform destekli geçiş](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Klasik modelden Azure Resource Manager’a platform destekli geçişe ayrıntılı teknik bakış](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [SSS: Iaas kaynaklarının Klasik modelden Azure Resource Manager'a Platform destekli geçişe](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [En sık karşılaşılan Geçiş hataları ve risk azaltma işlemleri gözden geçirin](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Desteklenen ve desteklenmeyen senaryolar

* Bir ExpressRoute bağlantı hattını Klasikten Resource Manager ortamına kapalı kalma süresi olmadan taşınabilir. Resource Manager ortamına kapalı kalma süresi olmadan, herhangi bir ExpressRoute bağlantı hattını Klasikten taşıyabilirsiniz. Bölümündeki yönergeleri [ExpressRoute bağlantı hatlarını Klasikten PowerShell kullanarak Resource Manager dağıtım modeline taşıma](expressroute-howto-move-arm.md). Bu sanal ağa bağlı kaynakları taşımak için bir önkoşuldur.
* Sanal ağlar, ağ geçitleri ve aynı Abonelikteki bir ExpressRoute bağlantı hattına bağlı sanal ağ içinde ilişkilendirilmiş dağıtımları, kapalı kalma süresi olmadan Resource Manager ortamına geçirilebilir. Sanal ağlar, ağ geçitleri ve sanal ağ içinde dağıtılan sanal makineler gibi kaynakları geçirmek için daha sonra açıklanan adımları takip edebilirsiniz. Geçişiniz yapılmadan önce sanal ağlar doğru şekilde yapılandırıldığından emin olmanız gerekir. 
* Sanal ağlar, ağ geçitleri ve sanal ağ içinde ExpressRoute bağlantı hattı ile aynı abonelikte değil ilişkilendirilmiş dağıtımları Geçişi tamamlamak için bazı kapalı kalma süresi gerektirir. Belgenin son bölümde kaynakları geçirmek için izlenmesi gereken adımlar açıklanmaktadır.
* Bir sanal ağda hem ExpressRoute ağ geçidi hem de VPN ağ geçidi geçirilemez.
* ExpressRoute devresi çapraz abonelik geçişi desteklenmiyor. Daha fazla bilgi için bkz. [Microsoft. Network taşıma desteği](../azure-resource-manager/move-support-resources.md#microsoftnetwork).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Bir ExpressRoute bağlantı hattını Klasikten Resource Manager'a taşıma
ExpressRoute işlem hattına bağlı kaynakları geçirmeyi denemeden önce bir ExpressRoute bağlantı hattı Klasikten Resource Manager ortamına taşımanız gerekir. Bu görevi gerçekleştirmek için aşağıdaki makalelere bakın:

* Altında sağlanan bilgileri gözden [bir ExpressRoute bağlantı hattını Klasikten Resource Manager'a taşıma](expressroute-move.md).
* [Bir devreyi Klasikten Azure PowerShell kullanarak Resource Manager'a taşıma](expressroute-howto-move-arm.md).
* Azure Hizmet Yönetim Portalı'nı kullanın. İş akışını izleyerek [yeni bir ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-portal-resource-manager.md) ve içeri aktarma seçeneğini belirleyin. 

Bu işlem, kapalı kalma süresi gerektirmez. Geçiş devam ederken, şirket içi ile Microsoft arasında veri aktarmak devam edebilirsiniz.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Sanal ağlar, ağ geçitleri ve ilişkili dağıtımları geçirme

Kaynaklarınızı aynı abonelik, farklı Aboneliklerde veya her ikisini de olan geçirmek için izleyeceğiniz adımlar bağlıdır.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Sanal ağlar, ağ geçitleri ve ExpressRoute bağlantı hattı ile aynı abonelikte ilişkilendirilmiş dağıtımları geçirme
Bu bölümde, bir sanal ağ, ağ geçidi ve ExpressRoute bağlantı hattı ile aynı abonelikte ilişkilendirilmiş dağıtımları geçirmek için izlenmesi gereken adımlar açıklanmaktadır. Kapalı kalma süresi olmadan, bu geçiş ile ilişkilidir. Tüm kaynaklar geçiş sürecinde kullanmaya devam edebilirsiniz. Geçiş devam ederken yönetim düzlemi kilitli. 

1. ExpressRoute bağlantı hattı Klasikten Resource Manager ortamına taşındığından emin olun.
2. Sanal ağı geçiş için uygun şekilde hazırlıklarını emin olun.
3. Kaynağın geçiş için aboneliğinizi kaydedin. Aboneliğiniz için kaynak geçişi kaydetmek için aşağıdaki PowerShell kod parçacığını kullanın:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Doğrulama, hazırlama ve geçirme. Sanal ağ taşımak için aşağıdaki PowerShell kod parçacığını kullanın:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Ayrıca aşağıdaki PowerShell cmdlet'ini çalıştırarak geçişi iptal edebilirsiniz:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Sonraki adımlar
* [Iaas kaynaklarının Klasik modelden Azure Resource Manager'a Platform destekli geçiş](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Klasik modelden Azure Resource Manager’a platform destekli geçişe ayrıntılı teknik bakış](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [SSS: Iaas kaynaklarının Klasik modelden Azure Resource Manager'a Platform destekli geçişe](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [En sık karşılaşılan Geçiş hataları ve risk azaltma işlemleri gözden geçirin](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
