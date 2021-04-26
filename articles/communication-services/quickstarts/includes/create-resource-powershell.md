---
ms.openlocfilehash: cc628b1f1fcae5e837f7f61db584c8747100f353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644721"
---
## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/dotnet/).
- [Azure az PowerShell modülünü](https://docs.microsoft.com/powershell/azure/) yükler

## <a name="create-azure-communication-resource"></a>Azure Iletişim kaynağı oluşturma

Azure Communication Services kaynağı oluşturmak için [Azure CLI 'da oturum açın](/cli/azure/authenticate-azure-cli). Bunu, ```Connect-AzAccount``` komutunu kullanarak ve kimlik bilgilerinizi sunarak terminalden yapabilirsiniz. Kaynağı oluşturmak için aşağıdaki komutu çalıştırın:

```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global
```

Belirli bir abonelik seçmek isterseniz, bayrağı da belirtebilir ```--subscription``` ve ABONELIK kimliğini sağlayabilirsiniz.
```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global -SubscriptionId SubscriptionID
```

Iletişim Hizmetleri kaynağınızı aşağıdaki seçeneklerle yapılandırabilirsiniz:

* Kaynak grubu
* Iletişim Hizmetleri kaynağının adı
* Kaynağın ilişkilendirileceği Coğrafya

Sonraki adımda, kaynağa etiketler atayabilirsiniz. Etiketler, Azure kaynaklarınızı düzenlemek için kullanılabilir. Etiketler hakkında daha fazla bilgi için bkz. [kaynak etiketleme belgeleri](../../../azure-resource-manager/management/tag-resources.md) .

## <a name="manage-your-communication-services-resource"></a>Iletişim Hizmetleri kaynağınızı yönetme

Iletişim Hizmetleri kaynağına Etiketler eklemek için aşağıdaki komutları çalıştırın. Belirli bir aboneliği de hedefleyebilirsiniz.

```PowerShell
PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"}

PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"} -SubscriptionId SubscriptionID
```

Belirli bir abonelikteki tüm Azure Iletişim Hizmetleri kaynaklarınızı listelemek için aşağıdaki komutu kullanın:

```PowerShell
PS C:\> Get-AzCommunicationService -SubscriptionId SubscriptionID
```

Belirli bir kaynaktaki tüm bilgileri listelemek için aşağıdaki komutu kullanın:

```PowerShell
PS C:\> Get-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1
```
