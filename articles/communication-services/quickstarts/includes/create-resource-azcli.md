---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 69857915620eada94586754a6c934edaf0b294a9
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107881066"
---
## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/dotnet/).
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?tabs=azure-cli) 'yı yükler 

## <a name="create-azure-communication-resource"></a>Azure Iletişim kaynağı oluşturma

Azure Communication Services kaynağı oluşturmak için [Azure CLI 'da oturum açın](/cli/azure/authenticate-azure-cli). Bunu, ```az login``` komutunu kullanarak ve kimlik bilgilerinizi sunarak terminalden yapabilirsiniz. Kaynağı oluşturmak için aşağıdaki komutu çalıştırın:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Belirli bir abonelik seçmek isterseniz, bayrağı da belirtebilir ```--subscription``` ve ABONELIK kimliğini sağlayabilirsiniz.
```
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup> --subscription "<subscriptionID>"
```

Iletişim Hizmetleri kaynağınızı aşağıdaki seçeneklerle yapılandırabilirsiniz:

* Kaynak grubu
* Iletişim Hizmetleri kaynağının adı
* Kaynağın ilişkilendirileceği Coğrafya

Sonraki adımda, kaynağa etiketler atayabilirsiniz. Etiketler, Azure kaynaklarınızı düzenlemek için kullanılabilir. Etiketler hakkında daha fazla bilgi için bkz. [kaynak etiketleme belgeleri](../../../azure-resource-manager/management/tag-resources.md) .

## <a name="manage-your-communication-services-resource"></a>Iletişim Hizmetleri kaynağınızı yönetme

Iletişim Hizmetleri kaynağına Etiketler eklemek için aşağıdaki komutları çalıştırın. Belirli bir aboneliği de hedefleyebilirsiniz.

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal1" --resource-group "<resourceGroup>"

az communication update --name "<communicationName>" --tags newTag="newVal2" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"
```

Ek komutlar hakkında daha fazla bilgi için bkz. [az Communication](/cli/azure/communication).
