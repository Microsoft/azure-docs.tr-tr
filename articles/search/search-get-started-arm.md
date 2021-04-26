---
title: Hizmetinizi dağıtmak için bir Azure Resource Manager şablonu kullanma
titleSuffix: Azure Cognitive Search
description: Azure Resource Manager şablonunu kullanarak bir Azure Bilişsel Arama hizmet örneğini hızlıca dağıtabilirsiniz.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 11/17/2020
ms.openlocfilehash: 88a081f1ebbda52653302384c06403e223128c60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668393"
---
# <a name="quickstart-deploy-cognitive-search-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak Bilişsel Arama dağıtma

Bu makalede, Azure portal bir Azure Bilişsel Arama kaynağı dağıtmak için bir Azure Resource Manager şablonu (ARM şablonu) kullanma işleminde size yol gösterilir.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-azure-search-create/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-azure-search-create/azuredeploy.json":::

Bu şablonda tanımlanan Azure kaynağı:

- [Microsoft. Search/searchServices](/azure/templates/Microsoft.Search/searchServices): Azure bilişsel arama hizmeti oluşturma

## <a name="deploy-the-template"></a>Şablonu dağıtma

Aşağıdaki görüntüyü seçerek Azure'da oturum açıp bir şablon açın. Şablon bir Azure Bilişsel Arama kaynağı oluşturur.

[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

Portal, kolayca parametre değerleri sağlamanıza olanak tanıyan bir form görüntüler. Bazı parametreler şablondaki varsayılan değerlerle önceden doldurulur. Aboneliğiniz, kaynak grubunuz, konumunuz ve hizmet adınızı sağlamanız gerekir. Bilişsel hizmetler 'i bir [AI zenginleştirme](cognitive-search-concept-intro.md) ardışık düzeninde kullanmak istiyorsanız, örneğin ikili resim dosyalarını metin için çözümlemek üzere, hem bilişsel arama hem de bilişsel hizmetler sunan bir konum seçin. Her iki hizmetin de AI zenginleştirme iş yükleri için aynı bölgede olması gerekir. Formu tamamladıktan sonra, hüküm ve koşulları kabul etmeniz ve sonra dağıtımınızı tamamlayabilmeniz için satın al düğmesini seçmeniz gerekir.

> [!div class="mx-imgBorder"]
> ![Şablon Azure portal görüntüleme](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Dağıtımınız tamamlandığında, yeni kaynak grubunuza ve portalda yeni arama hizmetinize erişebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer Bilişsel Arama hızlı başlangıçler ve öğreticiler bu hızlı başlangıç üzerine inşa. Sonraki hızlı başlangıç ve öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu kaynağı yerinde bırakmak isteyebilirsiniz. Artık gerekli değilse, Bilişsel Arama hizmetini ve ilgili kaynakları silen kaynak grubunu silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta ARM şablonunu kullanarak bir Bilişsel Arama hizmeti oluşturdunuz ve dağıtımı doğruladı. Bilişsel Arama ve Azure Resource Manager hakkında daha fazla bilgi edinmek için aşağıdaki makalelere devam edin.

- [Azure bilişsel arama genel bakışını](search-what-is-azure-search.md)okuyun.
- Arama hizmetiniz için [bir dizin oluşturun](search-get-started-portal.md) .
- Portal Sihirbazı 'nı kullanarak [bir demo uygulaması oluşturun](search-create-app-portal.md) .
- Verilerden bilgi ayıklamak için [bir beceri oluşturun](cognitive-search-quickstart-blob.md) .
