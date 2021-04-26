---
title: "Öğretici: Azure 'da varlıklara gözatıp kökenini görüntüleme"
description: Bu öğreticide, katalogdaki varlıklara nasıl gözatabileceğinizi ve verileri kökenini görüntülemeyi açıklanmaktadır.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 7ffbe2ded44ded4f580655f6ae9e98391490f94a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696096"
---
# <a name="tutorial-browse-assets-in-azure-purview-preview-and-view-their-lineage"></a>Öğretici: Azure purview (Önizleme) içindeki varlıklara gözatıp kökenini görüntüleyin

> [!IMPORTANT]
> Azure takip görünümü şu anda ÖNIZLEME aşamasındadır. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) , Beta, önizleme veya başka bir şekilde genel kullanıma sunulmayan Azure özelliklerine uygulanan ek yasal koşulları içerir.

Bu öğreticide, Azure purview ' de varlıklara nasıl gözatabilmeniz ve kökenini gibi önemli ayrıntıların nasıl görüntüleneceği gösterilmektedir.

Bu öğretici, Azure purview kullanarak bir veri yönetiminde veri yönetimini yönetme temellerini öğrentiğinizde *beş bölümlü bir öğretici serisinin 3. parçasıdır* . Bu öğretici [2. Bölüm: Azure purview (Önizleme) giriş sayfasında gezinin ve bir varlık arayın](tutorial-asset-search.md).

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
>
> * Katalogdaki varlıklara gözatamazsınız.
> * Varlıkların kökenini görüntüleyin.

## <a name="prerequisites"></a>Önkoşullar

* Tüm [öğretici: Azure purview (Önizleme) giriş sayfasına gidin ve bir varlık arayın](tutorial-asset-search.md).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="browse-for-assets-in-the-catalog"></a>Katalogdaki varlıklara gözatamıyorum

Önceki öğreticide, aramanın Azure purview kataloğunda varlıkları keşfetmenize nasıl yardımcı olduğunu öğrendiniz. Katalogdaki varlıkları bulmaya yönelik başka bir yol da kataloğun gözatma deneyimini kullanmaktır.

Bu bölümde, Azure purview kataloğunuza nasıl gözatabilmeniz gösterilmektedir.

1. Azure portal Azure purview kaynağına gidin ve **purview Studio 'Yu aç**' ı seçin. Otomatik olarak purview Studio 'nun giriş sayfasına yönlendirilirsiniz.

1. **Giriş** sayfasında **varlıkları görüntüle**' yi seçin.

   Kataloğunuzdaki tüm varlık türlerinin özetini görüntüleyen **varlıkları görüntüle** sayfası görüntülenir.

1. Kataloğunuzda bulunan çeşitli Azure Data Lake Gen2-Type varlıklarını araştırmak için **Azure Data Lake Gen2** kutucuğunu seçin.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/browse-by-asset-type.png" alt-text="Azure Data Lake Gen2 seçiliyken varlık türüne göre gözden geçirme sayfasını gösteren ekran görüntüsü.":::

1. Birden çok varlık varsa, varlıkları alfabetik olarak sıralamak için **ad** sütun başlığını seçebilirsiniz. Bu öğreticide yalnızca bir Azure Data Lake Storage 2. varlığı vardır.

1. Varlığın adını seçin.

1. **Contoso_GrossProfit_ {N}. SSV** kaynak kümesini seçin. Bu varlık kataloğunuzda yoksa, başka bir tane seçin.

   :::image type="content" source="media/tutorial-browse-and-view-lineage/view-adls-assets.png" alt-text="Azure Data Lake Storage 2. kaynaklarının listesi":::

## <a name="view-the-lineage-of-assets"></a>Varlıkların kökenini görüntüleme

Varlık ayrıntıları sayfasında, verilerin kaynağını araştırın.

1. **Contoso_GrossProfit_ {N}. SSV** kaynak kümesinin **kökenini** sekmesini seçin.

   Seçtiğiniz varlık görüntülenir. Gördüğünüz kökenini bilgileri, bu kaynak kümesinin Azure Blob Storage hesabınızdan Azure Data Lake Storage 2. olarak kopyalandığını gösterir.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/view-lineage.png" alt-text="Varlık için kökenini görünümünü gösteren ekran görüntüsü.":::

1. Bu sayfadaki blob varlığını seçin ve **varlık bağlantısını değiştir** bağlantısını seçin.

   Pencerenin, özgün Azure Data Lake Storage 2. kaynağı olan Azure Blob kaynak kümesine geçti olduğunu gözlemleyin.

1. Varlığı araştırmak ve ayrıntılarını onaylamak için **genel bakış** sekmesini seçin.

Azure blob ile Azure Data Lake Storage 2. kaynak kümesi arasında Azure Data Factory veri akışı etkinliği oluşturma ve kökenini gözlemleme hakkında daha fazla bilgi için bkz. [Azure Data Factory veri akışı etkinliği](../data-factory/concepts-data-flow-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
>
> * Katalogdaki varlıklara gözatamazsınız.
> * Varlıkların kökenini görüntüleyin.

Kaynak kümeleri, varlık ayrıntıları, şemalar ve sınıflandırmalar hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Kaynak kümeleri, varlık ayrıntıları, şemalar ve sınıflandırmalar](tutorial-schemas-and-classifications.md)
