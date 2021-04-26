---
title: Örnek ve kimlik doğrulaması ayarlama (PowerShell)
titleSuffix: Azure Digital Twins
description: Azure PowerShell kullanarak Azure dijital TWINS hizmeti örneğini ayarlama bölümüne bakın
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 024b238ef9a6330831ae6cf4dcd6bb72d72dcc74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98044280"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Azure dijital TWINS örneği ve kimlik doğrulaması (PowerShell) ayarlama

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Bu makalede, örneği oluşturma ve kimlik doğrulamasını ayarlama dahil olmak üzere **Yeni bir Azure dijital TWINS örneği ayarlama** adımları ele alınmaktadır. Bu makaleyi tamamladıktan sonra, programlamaya başlamak için kullanabileceğiniz bir Azure dijital TWINS örneğine sahip olursunuz.

Bu makalenin bu sürümü, bir diğeri [Azure PowerShell](/powershell/azure/new-azureps-module-az)kullanarak bu adımları el ile, tek tek gider.

* Azure portal kullanarak bu adımları el ile almak için, bu makalenin Portal sürümüne bakın: [*nasıl yapılır: bir örnek ve kimlik doğrulaması (portal) ayarlama*](how-to-set-up-instance-portal.md).
* Bir dağıtım betiği örneği kullanarak otomatikleştirilmiş bir kurulum aracılığıyla çalıştırmak için, bu makalenin betikleştirilmiş sürümüne bakın: [*nasıl yapılır: örnek ve kimlik doğrulaması ayarlama (komut dosyalı)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>Ortamınızı hazırlama

1. İlk olarak, bu makaledeki komutların çalıştırılacağı yeri seçin. Azure PowerShell komutlarını, yerel bir Azure PowerShell yüklemesi kullanarak veya [Azure Cloud Shell](https://shell.azure.com)kullanarak bir tarayıcı penceresinde çalıştırmayı tercih edebilirsiniz.
    * Azure PowerShell yerel olarak kullanmayı tercih ederseniz:
       1. [Az PowerShell modülünü yükler](/powershell/azure/install-az-ps).
       1. Makinenizde bir PowerShell penceresi açın.
       1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanın.
    * Azure Cloud Shell kullanmayı seçerseniz:
        1. Cloud Shell hakkında daha fazla bilgi için bkz. [Azure Cloud Shell Genel Bakış](../cloud-shell/overview.md) .
        1. Tarayıcınızda [Bu bağlantıyı](https://shell.azure.com) izleyerek Cloud Shell bir pencere açın.
        1. Cloud Shell simge çubuğunda, Cloud Shell PowerShell sürümünü çalıştıracak şekilde ayarlandığından emin olun.
    
            :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="PowerShell sürümünün seçimini gösteren pencere Cloud Shell":::
    
1. Birden çok Azure aboneliğiniz varsa, kaynakların faturalandırılması gereken uygun aboneliği seçin. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 'ini kullanarak belirli bir abonelik seçin.

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Bu abonelikle Azure dijital TWINS 'i ilk kez kullanıyorsanız, **Microsoft. DigitalTwins** kaynak sağlayıcısını kaydetmeniz gerekir. (Emin değilseniz, geçmişte bir zaman gerçekleştirseniz bile bu işlemi yeniden çalıştırmak daha iyi olur.)

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

1. **Az. DigitalTwins** PowerShell modülünü yüklemek için aşağıdaki komutu kullanın.
    ```azurepowershell-interactive
    Install-Module -Name Az.DigitalTwins
    ```

> [!IMPORTANT]
> **Az. DigitalTwins** PowerShell modülü önizlemedeyken, `Install-Module` yukarıda açıklandığı gibi cmdlet 'ini kullanarak ayrı olarak kurmanız gerekir. Bu PowerShell modülü genel kullanıma sunulduktan sonra, gelecekteki Az PowerShell modülü sürümlerinin bir parçası haline gelecek ve Azure Cloud Shell içinden varsayılan olarak sağlanacaktır.

## <a name="create-the-azure-digital-twins-instance"></a>Azure dijital TWINS örneğini oluşturma

Bu bölümde, Azure PowerShell kullanarak **Azure dijital TWINS 'in yeni bir örneğini oluşturacaksınız** .
Şunları sağlamanız gerekir:

* Örneğin dağıtılacağı bir [Azure Kaynak grubu](../azure-resource-manager/management/overview.md) . Zaten mevcut bir kaynak grubunuz yoksa, [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 'ini kullanarak bir tane oluşturabilirsiniz:

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* Dağıtım için bir bölge. Azure dijital TWINS 'i destekleyen bölgeleri görmek için [*bölgeye göre kullanılabilen Azure ürünlerini*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)ziyaret edin.
* Örneğiniz için bir ad. Yeni örneğin adı, aboneliğinizin bölgesi içinde benzersiz olmalıdır. Aboneliğiniz zaten belirtilen adı kullanan bölgede başka bir Azure dijital TWINS örneğine sahipse, farklı bir ad seçmeniz istenir.

Örneği oluşturmak için aşağıdaki komutta değerlerinizi kullanın:

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>Başarıyı doğrulama ve önemli değerleri toplama

Örnek başarıyla oluşturulduysa, sonuç, oluşturduğunuz kaynakla ilgili bilgiler içeren aşağıdaki çıktıya benzer şekilde görünür:

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

Ardından, aşağıdaki gibi çalıştırarak ve ayırarak yeni örneğinizin özelliklerini görüntüleyin `Get-AzDigitalTwinsInstance` `Select-Object -Property *` :

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

> [!TIP]
> Bu komutu dilediğiniz zaman örneğinizin tüm özelliklerini görmek için kullanabilirsiniz.

Azure dijital TWINS örneğinin **ana bilgisayar** adı, **adı** ve **ResourceGroup** değerini aklınızda edin. Bunlar, Azure dijital TWINS örneğiniz ile çalışmaya devam ederken kimlik doğrulamasını ve ilgili Azure kaynaklarını ayarlamak için ihtiyaç duyduğunuz önemli değerlerdir. Diğer kullanıcılar örnekle programlama yapacaktır, bu değerleri bunlarla paylaşmanız gerekir.

Artık hazır bir Azure dijital TWINS örneğiniz var. Daha sonra, uygun Azure Kullanıcı izinlerini yönetmek için size izin verirsiniz.

## <a name="set-up-user-access-permissions"></a>Kullanıcı erişim izinlerini ayarlama

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

İlk olarak, kullanıcının rolün atanması gereken Azure AD hesabı için **ObjectID** 'yi saptayın. Bu değeri [Get-AzAdUser](/powershell/module/az.resources/get-azaduser) cmdlet 'ini kullanarak, ObjectID 'yi (ve diğer Kullanıcı bilgilerini) almak IÇIN Azure AD hesabındaki Kullanıcı asıl adını geçirerek bulabilirsiniz. Çoğu durumda, Kullanıcı asıl adı kullanıcının Azure AD hesabındaki e-postalarıyla eşleştirecektir.

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

Sonra, rolü atamak için aşağıdaki komutta **ObjectID** öğesini kullanın. Bu komut, örneği oluştururken daha önce seçtiğiniz abonelik KIMLIĞINI, kaynak grubu adını ve Azure dijital TWINS örnek adını da girmenizi gerektirir. Komutun, Azure aboneliğinde [yeterli izinlere](#prerequisites-permission-requirements) sahip bir kullanıcı tarafından çalıştırılması gerekir.

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-id-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

Bu komutun sonucu oluşturulan rol ataması hakkında bilgi verilir.

### <a name="verify-success"></a>Başarıyı doğrula

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Artık bir Azure dijital TWINS örneğiniz var ve bunu yönetmek için izinler atandı.

## <a name="next-steps"></a>Sonraki adımlar

Bkz. kimlik doğrulama kodu ile bir istemci uygulamasını örneğinize bağlama:
* [*Nasıl yapılır: uygulama kimlik doğrulama kodunu yazma*](how-to-authenticate-client.md)
