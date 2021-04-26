---
title: ARM şablonu kullanarak Azure Maps hesabınızı oluşturun | Microsoft Azure haritaları
description: Azure Resource Manager (ARM) şablonu kullanarak Azure Maps hesabı oluşturmayı öğrenin.
author: philmea
ms.author: philmea
ms.date: 10/20/2020
ms.topic: how-to
ms.service: azure-maps
ms.openlocfilehash: c715c0639e962f76f669515c1d2c826c8cf6cc9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92525189"
---
# <a name="create-your-azure-maps-account-using-an-arm-template"></a>ARM şablonu kullanarak Azure Maps hesabınızı oluşturma

Azure haritalar hesabınızı bir Azure Resource Manager (ARM) şablonu kullanarak oluşturabilirsiniz. Hesabınızı aldıktan sonra, Web sitenizde veya mobil uygulamanızda API 'Leri uygulayabilirsiniz.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Bu makaleyi gerçekleştirmek için:

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-maps-create/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-maps-create/azuredeploy.json":::

Azure haritalar hesap kaynağı bu şablonda tanımlanmıştır:

* [**Microsoft. Maps/accounts**](/azure/templates/microsoft.maps/accounts): bir Azure Maps hesabı oluşturun.

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Aşağıdaki görüntüyü seçerek Azure'da oturum açıp bir şablon açın. Şablon bir Azure Maps hesabı oluşturur.

    [![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

2. Aşağıdaki değerleri seçin veya girin.

    ![ARM şablonu dağıtım portalı](./media/how-to-create-template/create-account-using-template-portal.png)

    Belirtilmediği takdirde, Azure Maps hesabınızı oluşturmak için varsayılan değeri kullanın.

    * **Abonelik**: Bir Azure aboneliği seçin.
    * **Kaynak grubu**: **Yeni oluştur**' u seçin, kaynak grubu için benzersiz bir ad girin ve ardından **Tamam**' a tıklayın.
    * **Konum**: Bir konum seçin. Örneğin, **Batı ABD 2**.
    * **Hesap adı**: Azure Maps hesabınız için genel olarak benzersiz olması gereken bir ad girin.
    * **Fiyatlandırma katmanı**: uygun fiyatlandırma katmanını seçin, şablon için varsayılan değer S0 ' dir.

3. **Gözden geçir ve oluştur**’u seçin. 
4. İnceleme sayfasında ayarlarınızı onaylayın ve **Oluştur**' a tıklayın. Azure haritalar başarıyla dağıtıldıktan sonra bir bildirim alırsınız:

    ![ARM şablonu dağıtım portalı bildirimi](./media/how-to-create-template/resource-manager-template-portal-deployment-notification.png)

Azure portal, şablonunuzu dağıtmak için kullanılır. Azure PowerShell, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Azure haritalar hesabınızı denetlemek ve anahtarlarınızı görüntülemek için Azure portal kullanabilirsiniz. Hesap anahtarlarınızı listelemek için aşağıdaki Azure CLı betiğini de kullanabilirsiniz.

```azurecli-interactive
az maps account keys list --name MyMapsAccount --resource-group MyResourceGroup
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, Azure Maps hesabını da silen kaynak grubunu silin. Azure CLı kullanarak kaynak grubunu silmek için:

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Azure haritalar ve Azure Resource Manager hakkında daha fazla bilgi edinmek için aşağıdaki makalelere devam edin.

- Azure haritalar [demo uygulaması](quick-demo-map-app.md) oluşturma
- [ARM şablonları](../azure-resource-manager/templates/overview.md) hakkında daha fazla bilgi