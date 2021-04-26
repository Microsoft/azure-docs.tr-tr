---
title: 'Öğretici: Azure purview giriş sayfasında gezinme ve varlık arama'
description: Bu öğreticide, Azure purview giriş sayfasında özelliklerin nasıl kullanılacağı ve katalogda arama açıklanır.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 02/22/2020
ms.openlocfilehash: 5285d5b2c7cbe6845fb6043fa1096f94254602e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677834"
---
# <a name="tutorial-navigate-the-azure-purview-preview-home-page-and-search-for-an-asset"></a>Öğretici: Azure purview (Önizleme) giriş sayfasına gidin ve bir varlık arayın

> [!IMPORTANT]
> Azure takip görünümü şu anda ÖNIZLEME aşamasındadır. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) , Beta, önizleme veya başka bir şekilde genel kullanıma sunulmayan Azure özelliklerine uygulanan ek yasal koşulları içerir.

Bu öğreticide, ana sayfanın özelliklerine giderek ve katalogdaki bir varlık arayarak Azure purview hakkında bilgi sahibi olursunuz.

Bu makale, Azure purview kullanarak veri yönetimini yönetme temellerini öğrenilen *beş bölümlü bir öğretici serisinin 2. parçasıdır* . Bu öğretici, [1. Bölüm: Azure purview ile veri tarama](tutorial-scan-data.md) ' da tamamladığınız çalışmayı oluşturur

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
>
> * Azure purview giriş sayfasına gidin.
> * Varlık arayın.
> * Varlık sahibi ekleme.

## <a name="prerequisites"></a>Önkoşullar

* Tüm [öğretici: Azure purview ile veri tarama](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="navigate-the-azure-purview-home-page"></a>Azure purview giriş sayfasında gezinme

Aşağıdaki adımlar, Azure purview giriş sayfasında size yol gösterir.

1. Azure portal Azure purview kaynağına gidin ve **purview Studio 'Yu aç**' ı seçin. Otomatik olarak purview Studio 'nun giriş sayfasına yönlendirilirsiniz.

   Giriş sayfasının en üst kısmında, kataloğunuzun adı ve bir katalog Analizi kümesi görüntülenir. Kaynak, veri varlık ve sözlük terimi sayısıdır. Özetle, toplam ve 113 sözlük koşullarında 200 ' den fazla varlık olduğunu görebilirsiniz. Bu sayı, kuruluşunuz taradığı şekilde güncelleştirilir ve Azure purview 'a daha fazla terim ekler.

   :::image type="content" source="./media/tutorial-asset-search/purview-home-page.png" alt-text="Azure purview giriş sayfasını gösteren ekran görüntüsü.":::

1. Tüm varlıklarınızı görmek için **varlıkları** inceleyin ' i seçin.

## <a name="search-for-an-asset"></a>Varlık arama

Başlamadan önce, bazı temel terminoloji için hızlı Yenileyici aşağıda verilmiştir:

* **Varlık**: katalogdaki tek bir nesne, kısa olan ve veri Emlak içindeki herhangi bir varlığın tanımını içerir. Varlık örnekleri arasında bir SQL tablosu, Power BI raporu ve Veri Fabrikası etkinliğinizi vardır.
  
* **Şema**: bir sütun veya öznitelik olarak da bilinen şema, bir varlığın veya kaynak kümesinin yapısını temsil eder.

* **Kaynak kümesi**: katalogdaki, depolamada çok sayıda fiziksel nesneyi temsil eden tek bir nesne. Bu nesneler genellikle ortak bir şemayı ve çoğu durumda bir adlandırma kuralını veya klasör yapısını paylaşır. Örneğin, tarih biçimi *yyyy/aa/gg* şeklindedir. Daha fazla bilgi için bkz. [kaynak kümelerini anlama](concept-resource-sets.md).

* **Varlık türü**: genellikle veri platformunun adı ile eşleşen bir mantıksal ad altında varlık ve kaynak kümesi gruplandırması.

Bir varlık aramak ve kendinizi sahip olarak işaretlemek için aşağıdaki adımları izleyin:

1. Giriş sayfanızdaki **Katalog ara** kutusuna, verilerinizi içeren kaynak grubunun adını girin (önceki öğreticide oluşturduğunuz kaynak grubu). Önerilerin bir listesi görüntülenir.

1. **Arama sonuçlarını görüntüle**' yi seçin. Tüm varlıklarınız kaynak grubunuzun adı ile başlamadığı için, bunların hepsi arama sonuçlarında görünür. Bu öğreticinin dışında, kaynak grupları değil belirli varlık adlarını ararsınız.

    :::image type="content" source="./media/tutorial-asset-search/search-suggestions.png" alt-text="Katalog Arama önerilerinin listesini gösteren ekran görüntüsü.":::

1. Sol gezinti bölmesindeki filtreleri varlık türüne, sınıflandırmaya, kişiye, etikete ve sözlük terimine göre filtrelemek için kullanabilirsiniz.

1. Bir kaynak kümesi aramak için, küme adını yazmaya başlayın. Doğru anahtar sözcüklere sahip arama önerilerinin listesi görüntülenir. Mutlak adları tırnak içine alarak da arama yapabilirsiniz.

   :::image type="content" source="media/tutorial-asset-search/keyword-search.png" alt-text="Azure purview anahtar sözcük varlık araması":::

## <a name="edit-an-asset"></a>Bir varlığı düzenleme

1. Arama sonuçlarınızdaki varlıklardan birini seçin. Sonra **Düzenle** 'yi seçin

1. **Genel bakış** sekmesinde bir açıklama ekleyebilirsiniz.

    :::image type="content" source="./media/tutorial-asset-search/overview-tab.png" alt-text="Genel Bakış sekmesindeki Açıklama alanını gösteren ekran görüntüsü.":::

1. **Kişiler** sekmesini seçin. **Sahipler**' in yanında, **Kullanıcı veya Grup Seç**' de şirket e-posta adresinizi yazmaya başlayın.

    :::image type="content" source="./media/tutorial-asset-search/contacts-tab.png" alt-text="Doldurulmuş alanları gösteren ekran görüntüsü.":::

    Ad önerisi otomatik olarak görüntülenir.

1. **Uzmanlar**' ın yanındaki **Kullanıcı veya Grup Seç**' de bir ad girin (örneğin, yöneticinizin adı) ve ardından **Kaydet**' i seçin.

    Açıklama, sahip adı ve uzman ad alanları artık doldurulur.

## <a name="next-steps"></a>Sonraki adımlar

Bu serideki bir sonraki öğreticiye geçmeden önce, kendi kendinize ait Azure takip görünümü giriş sayfasını araştırmak için biraz ek zaman atın. Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
>
> * Azure purview giriş sayfasına gidin.
> * Varlık arayın.
> * Varlık sahibi ekleme.

Azure 'da varlıklara nasıl gözatacağınızı öğrenmek ve varlık kökenini bulma hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Varlıklara gözatıp kökenini görüntüleyin](tutorial-browse-and-view-lineage.md)
