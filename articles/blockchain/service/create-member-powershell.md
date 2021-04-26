---
title: Azure blok zinciri hizmeti üyesi oluşturma-Azure PowerShell
description: Azure PowerShell kullanarak bir blok zinciri Konsorsiyumu için Azure blok zinciri hizmeti üyesi oluşturun.
ms.reviewer: ravastra
ms.date: 9/22/2020
ms.topic: quickstart
ms.custom:
- references_regions
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9087bda9c1979a98f4bbf9f8343d012c4cf3098c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529638"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak bir Azure blok zinciri hizmeti blok zinciri üyesi oluşturma

Bu hızlı başlangıçta, Azure PowerShell kullanarak Azure blok zinciri hizmeti 'ne yeni bir blok zinciri üyesi ve konsorsiyu dağıtırsınız.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

PowerShell 'i yerel olarak kullanmayı seçerseniz, bu makale az PowerShell modülünü yüklemenizi ve [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanmanızı gerektirir. Az PowerShell modülünü yükleme hakkında daha fazla bilgi için bkz. [yükleme Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> **Az. Blockzincirine** ait PowerShell modülü önizlemedeyken, cmdlet 'Ini kullanarak az PowerShell modülünden bu uygulamayı ayrı olarak yüklemelisiniz `Install-Module` . Bu PowerShell modülü genel kullanıma sunulduğunda, bu, gelecekteki az PowerShell modülü sürümlerinin bir parçası haline gelir ve Azure Cloud Shell içinden yerel olarak kullanılabilir.

```azurepowershell-interactive
Install-Module -Name Az.Blockchain
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

## <a name="register-resource-provider"></a>Kaynak sağlayıcısını kaydetme

Azure blok zinciri hizmetini ilk kez kullanıyorsanız, **Microsoft. blockzincirin** kaynak sağlayıcısını kaydetmeniz gerekir.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Blockchain
```

## <a name="choose-a-specific-azure-subscription"></a>Belirli bir Azure aboneliği seçin

Birden çok Azure aboneliğiniz varsa, kaynakların faturalandırılması gereken uygun aboneliği seçin. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 'ini kullanarak belirli bir abonelik seçin.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Değişkenleri tanımlama

Birkaç bilgi parçasını sürekli olarak kullanacaksınız. Bilgileri depolamak için değişkenler oluşturun.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'eastus'
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 'ini kullanarak bir [Azure Kaynak grubu](../../azure-resource-manager/management/overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek, değişkeninde belirtilen bölgedeki ada göre bir kaynak grubu oluşturur `$resourceGroupName` `$location` .

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-blockchain-member"></a>Blok zinciri üyesi oluşturma

Bir Azure blok zinciri hizmeti üyesi, özel bir konsorsiyum blok zinciri ağında bir blok zinciri düğümüdür.
Bir üyeyi sağlarken bir konsorsiyum ağı oluşturabilir veya katabilirsiniz. Bir konsorsiyum ağı için en az bir üyeye ihtiyacınız vardır. Katılımcılar tarafından gereken blok zinciri üyelerinin sayısı senaryonuza bağlıdır. Konsorsiyum katılımcıları bir veya daha fazla blok zinciri üyesine sahip olabilir veya diğer katılımcılarla üyeleri paylaşabilir. Consorda hakkında daha fazla bilgi için bkz. [Azure blok zinciri hizmeti Consortium](consortium.md).

Geçirmeniz gereken birkaç parametre ve özellik vardır. Örnek parametreleri değerlerinizle değiştirin.

```azurepowershell-interactive
$passwd = Read-Host -Prompt 'Enter the members default transaction node password' -AsSecureString
$csPasswd = Read-Host -Prompt 'Enter  the consortium account password' -AsSecureString

$memberParams = @{
  Name = 'myblockchainmember'
  ResourceGroupName = $resourceGroupName
  Consortium = 'myconsortium'
  ConsortiumManagementAccountPassword = $csPasswd
  Location = $location
  Password = $passwd
  Protocol = 'Quorum'
  Sku = 'S0'
}
New-AzBlockchainMember @memberParams
```

| Parametre | Açıklama |
|---------|-------------|
| **ResourceGroupName** | Azure blok zinciri hizmeti kaynaklarının oluşturulduğu kaynak grubu adı. Önceki bölümde oluşturduğunuz kaynak grubunu kullanın.
| **Ad** | Azure blok zinciri hizmeti blok zinciri üyesini tanımlayan benzersiz bir ad. Ad, genel uç nokta adresi için kullanılır. Örneğin, `myblockchainmember.blockchain.azure.com`.
| **Konum** | Blok zinciri üyesinin oluşturulduğu Azure bölgesi. Örneğin, `westus2`. Kullanıcılarınıza veya diğer Azure uygulamalarınıza en yakın konumu seçin. Özellikler, bazı bölgelerde kullanılamayabilir. Azure blok zinciri Veri Yöneticisi şu Azure bölgelerinde kullanılabilir: Doğu ABD ve Batı Avrupa.
| **Parola** | Üyenin varsayılan işlem düğümü için parola. Blok zinciri üyesinin varsayılan işlem düğümü genel uç noktasına bağlanırken temel kimlik doğrulaması için parolayı kullanın.
| **Protokol** | Blok zinciri protokolü. Şu anda _çekirdek_ protokol destekleniyor.
| **Konsorsiyum** | Katılacak veya oluşturulacak konsorsiyumun adı. Consorda hakkında daha fazla bilgi için bkz. [Azure blok zinciri hizmeti Consortium](consortium.md).
| **ConsortiumManagementAccountPassword** | Konsorsiyum hesabı parolası üye hesap parolası olarak da bilinir. Üye hesabı parolası, üyelik için oluşturulan Ethereum hesabının özel anahtarını şifrelemek için kullanılır. Konsorsiyum yönetimi için üye hesabı ve üye hesabı parolasını kullanırsınız.
| **İsteyin** | Katman türü. Standart için **S0** veya temel için **B0** . Geliştirme, test ve kavram kanıtı için _temel_ katmanı kullanın. Üretim sınıfı dağıtımları için _Standart_ katmanı kullanın. Ayrıca, blok Veri Yöneticisi Zinciri kullanıyorsanız veya yüksek hacimli özel işlemler gönderiyorsanız _Standart_ katmanı da kullanmanız gerekir. Üye oluşturulduktan sonra temel ve standart arasındaki fiyatlandırma katmanını değiştirmek desteklenmez.

Blok zinciri üyesini ve destekleyici kaynakları oluşturmak yaklaşık 10 dakika sürer.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki hızlı başlangıç veya öğretici için oluşturduğunuz blok zinciri üyesini kullanabilirsiniz. Artık gerekli değilse, `myResourceGroup` hızlı başlangıç için oluşturduğunuz kaynak grubunu silerek kaynakları silebilirsiniz.

> [!CAUTION]
> Aşağıdaki örnek, belirtilen kaynak grubunu ve içinde yer alan tüm kaynakları siler.
> Bu makalenin kapsamı dışındaki kaynaklar belirtilen kaynak grubunda mevcutsa, bunlar da silinir.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir Azure blok zinciri hizmeti üyesi ve yeni bir konsorsiyu dağıttınız. Azure blok zinciri hizmeti üyesine eklemek için Ethereum için Azure blok zinciri geliştirme seti 'ni kullanmak üzere bir sonraki hızlı başlangıcı deneyin.

> [!div class="nextstepaction"]
> [Azure blok zinciri hizmetine bağlanmak için Visual Studio Code kullanma](connect-vscode.md)
