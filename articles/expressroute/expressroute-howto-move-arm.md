---
title: 'Azure ExpressRoute: klasik devreleri Kaynak Yöneticisi taşıyın'
description: Bu sayfa, klasik bir bağlantı hattı PowerShell kullanılarak Resource Manager dağıtım modelinde taşımayı açıklar.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.openlocfilehash: 4e49a3bc803733f5e78207fa3573c93395924d6a
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080165"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>PowerShell kullanılarak Resource Manager dağıtım modelinde ExpressRoute devreleri Klasikten Taşı

Bir ExpressRoute bağlantı hattı Klasik ve Resource Manager dağıtım modelleri için kullanmak için Resource Manager dağıtım modeli için bağlantı hattını taşımanız gerekir. Aşağıdaki bölümlerde, PowerShell kullanarak bağlantı hattınızın taşımanıza yardımcı olur.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Bilgisayarınızda hem klasik hem de az Azure PowerShell modüllerini yüklediğinizi doğrulayın. Daha fazla bilgi için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/overview).
* Geçirdiğinizden emin emin [önkoşulları](expressroute-prerequisites.md), [yönlendirme gereksinimleri](expressroute-routing.md), ve [iş akışları](expressroute-workflows.md) yapılandırmaya başlamadan önce.
* Altında sağlanan bilgileri gözden [bir ExpressRoute bağlantı hattını Klasikten Resource Manager'a taşıma](expressroute-move.md). Tam olarak sınırlar ve sınırlamalar anladığınızdan emin olun.
* Bağlantı hattı Klasik dağıtım modelinde tam olarak işlevsel olduğunu doğrulayın.
* Resource Manager dağıtım modelinde oluşturulan bir kaynak grubu olduğundan emin olun.

## <a name="move-an-expressroute-circuit"></a>Bir ExpressRoute bağlantı hattı Taşı

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>1\. adım: Klasik dağıtım modelinden bağlantı hattına ayrıntıları toplayın

Azure Klasik ortamı için oturum açın ve hizmet anahtarı toplayın.

1. Azure hesabınızda oturum açın.

   ```powershell
   Add-AzureAccount
   ```

2. Uygun Azure aboneliğini seçin.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. PowerShell modülleri, Azure ve ExpressRoute için içeri aktarın.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Tüm ExpressRoute devrelerinizin hizmet anahtarlarını almak için aşağıdaki cmdlet'i kullanın. Anahtarları aldıktan sonra kopyalama **hizmet anahtarı** Resource Manager dağıtım modeli için taşımak istediğiniz bağlantı hattının.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>2\. adım: Oturum açın ve bir kaynak grubu oluşturun

Resource Manager ortamı için oturum açın ve yeni bir kaynak grubu oluşturun.

1. Azure Resource Manager ortamınız için oturum açın.

   ```powershell
   Connect-AzAccount
   ```

2. Uygun Azure aboneliğini seçin.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Bir kaynak grubu yoksa, yeni bir kaynak grubu oluşturmak için aşağıdaki kod parçacığında değiştirin.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>3\. adım: ExpressRoute bağlantı hattı Resource Manager dağıtım modeline taşıma.

ExpressRoute bağlantı hattı Klasik dağıtım modelinden Resource Manager dağıtım modeline taşıma artık hazırsınız. Devam etmeden önce sağlanan bilgileri gözden geçirin [bir ExpressRoute bağlantı hattını Klasikten Resource Manager dağıtım modeline taşıma](expressroute-move.md).

Bağlantı hattınız taşımak için değiştirin ve aşağıdaki kod parçacığını çalıştırın:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

Klasik modda bir bölgeye bağlı kavramı bir ExpressRoute bağlantı hattı yok. Ancak, Kaynak Yöneticisi'nde, her kaynak bir Azure bölgesine eşlenmesi gerekir. Move-Azexpressroutedevresi cmdlet 'inde belirtilen bölge Teknik olarak herhangi bir bölge olabilir. Kurumsal amaçlarla yakından eşleme konumunuzu temsil eden bir bölge seçin isteyebilirsiniz.

> [!NOTE]
> Taşıma tamamlandıktan sonra önceki cmdlet'inde listelenen yeni adı kaynak ele almak için kullanılır. Bağlantı hattı temelde yeniden adlandırılacak.
> 

## <a name="modify-circuit-access"></a>Bağlantı hattı erişim değiştirme

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Her iki dağıtım modeli için ExpressRoute bağlantı hattına erişimi etkinleştirmek için

Klasik ExpressRoute bağlantı hattı Resource Manager dağıtım modeline taşıdıktan sonra her iki dağıtım modeline erişimi etkinleştirebilir. Her iki dağıtım modeline erişimi etkinleştirmek için aşağıdaki cmdlet'leri çalıştırın:

1. Bağlantı hattı ayrıntılarını alın.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. "Klasik işlemlere izin TRUE" olarak ayarlayın.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Bağlantı hattı güncelleştirin. Bu işlem başarıyla tamamlandıktan sonra Klasik dağıtım modelinde bağlantı hattına görüntülemeniz mümkün olacaktır.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. ExpressRoute bağlantı hattı ayrıntılarını almak için aşağıdaki cmdlet'i çalıştırın. Listelenen hizmet anahtarı görebilmeniz gerekir.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Klasik dağıtım modeli komutları Resource Manager Sanal Ağları için Klasik sanal ağlar ve Resource Manager komutları kullanarak ExpressRoute bağlantı hattı bağlantıları artık yönetebilirsiniz. Aşağıdaki makaleler ExpressRoute bağlantı hattı bağlantıları yönetmenize yardımcı olur:

    * [Resource Manager dağıtım modelinde ExpressRoute bağlantı hattı için sanal ağınıza bağlama](expressroute-howto-linkvnet-arm.md)
    * [ExpressRoute bağlantı hattı Klasik dağıtım modelinde sanal ağınıza bağlama](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Klasik dağıtım modelinde ExpressRoute bağlantı hattı erişimi devre dışı bırakmak için

Klasik dağıtım modeline erişimi devre dışı bırakmak için aşağıdaki cmdlet'leri çalıştırın.

1. ExpressRoute bağlantı hattı ayrıntılarını alın.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. "Klasik işlemlere izin FALSE" olarak ayarlayın.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Bağlantı hattı güncelleştirin. Bu işlem başarıyla tamamlandıktan sonra bağlantı hattı Klasik dağıtım modelinde görüntülemek üzere mümkün olmayacaktır.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Sonraki adımlar

* [ExpressRoute bağlantı hattı için yönlendirme oluşturma ve değiştirme](expressroute-howto-routing-arm.md)
* [Sanal ağınız, ExpressRoute devresine bağlama](expressroute-howto-linkvnet-arm.md)
