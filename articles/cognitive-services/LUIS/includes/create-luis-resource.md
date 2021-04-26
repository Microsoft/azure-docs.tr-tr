---
title: LUSıS kaynağı oluşturma
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2020
ms.author: aahi
ms.openlocfilehash: ee7fd384a198c5eff672b14b6cb479aac26cfe54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95972527"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-the-azure-portal"></a>Azure portal LUSıS kaynakları oluşturma

1. Azure portal LUSıS kaynaklarını oluşturmaya başlamak için [Bu bağlantıyı](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) kullanın.

1. Tüm gerekli ayarları girin:

    |Name|Amaç|
    |--|--|
    |Abonelik | Kaynak için faturalandırılacak abonelik.|
    |Kaynak grubu| Seçtiğiniz veya oluşturduğunuz özel bir kaynak grubu adı. Kaynak grupları, erişim ve yönetim için Azure kaynaklarını gruplandıreklemenize olanak tanır.|
    |Name| Seçtiğiniz özel bir ad. Bu, yazma ve tahmin uç noktası sorguları için özel alt etki alanı olarak kullanılır.|
    |Yazma konumu|Modelinizle ilişkili bölge.|
    |Fiyatlandırma Katmanı yazma|Saniyede en fazla işlem sayısını ve ayı belirler.|
    |Tahmin konumu|Yayımlanmış tahmin uç noktası çalışma zamanı ile ilişkili bölge.|
    |Tahmin Fiyatlandırma Katmanı|Saniyede en fazla işlem sayısını ve ayı belirler.|

    > [!div class="mx-imgBorder"]
    > [![Oluştur altında temel bilgiler sekmesini gösteren ekran görüntüsü.](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. **Gözden geçir + oluştur**' u seçin ve kaynağın oluşturulmasını bekleyin.
1. Her iki kaynak de oluşturulduktan sonra, Azure portal yeni yazma kaynağını seçin. Ardından program aracılığıyla yazma için **uç nokta URL 'sini** ve **anahtarını** almak Için **anahtarlar ve uç nokta '** ı seçin.

> [!TIP]
> Kaynakları kullanmak için, LUO portalında [kaynakları atayın](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).
