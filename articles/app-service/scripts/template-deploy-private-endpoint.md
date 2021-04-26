---
title: Bir Web uygulaması için özel bir uç nokta dağıtmak üzere Azure Resource Manager şablonu kullanma
description: Web uygulamanız için özel bir uç nokta dağıtmak üzere ARM şablonunu nasıl kullanacağınızı öğrenin.
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: df71c1a92840ae0e7fa263e2ababcf5b3e059789
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832545"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak App Service uygulaması oluşturma ve özel uç nokta dağıtma

Bu hızlı başlangıçta, bir Web uygulaması oluşturmak ve özel bir uç nokta ile sunmak için bir Azure Resource Manager (ARM) şablonu kullanırsınız.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Önkoşul

Etkin aboneliği olan bir Azure hesabınızın olması gerekir. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-endpoint"></a>Özel uç nokta oluşturma

Bu şablon, bir Azure Web uygulaması için özel bir uç nokta oluşturur.

### <a name="review-the-template"></a>Şablonu gözden geçirme

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>Şablonu dağıtma

Azure Resource Manager şablonunu Azure 'a dağıtma:

1. Azure 'da oturum açmak ve şablonu açmak için şu bağlantıyı seçin:  [Azure 'A dağıtın](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json). Şablon, sanal ağ, Web uygulaması, Özel uç nokta ve özel DNS bölgesi oluşturur.
2. Kaynak grubunuzu seçin veya oluşturun.
3. Web uygulamanızın adını, Azure App Service planınızı ve özel uç noktasını girin.
5. Hüküm ve koşullar hakkındaki ifadeyi okuyun. Kabul ediyorsanız, satın alma sırasında **belirtilen hüküm ve koşulları kabul** ediyorum ' u seçin  >  . Dağıtımın tamamlanması birkaç dakika sürebilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Özel uç nokta ile oluşturduğunuz kaynaklara artık ihtiyacınız kalmadığında, kaynak grubunu silin. Bu, Özel uç noktayı ve ilgili tüm kaynakları kaldırır.

Kaynak grubunu silmek için `Remove-AzResourceGroup` cmdlet 'ini çağırın:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Sonraki adımlar

- [ARM şablon örneklerinde](../samples-resource-manager-templates.md)Azure App Service Web uygulamaları için daha fazla Azure Resource Manager şablonu bulabilirsiniz.
