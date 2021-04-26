---
title: Veri akışı parçacıklarını kullanarak satırları kaldırma ve null değerleri bulma
description: Veri akışlarında kod parçacıklarını kullanarak satırları kolayca kaldırma ve null değerleri bulma hakkında bilgi edinin
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 1b49b2584c4cb462c7c0f520fe8d1b5bf69c8674
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393675"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>Veri akışı parçacıklarını kullanarak satırları kaldırma ve null değerleri bulma

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Veri akışlarını eşleme bölümünde kod parçacıkları kullanarak, yinelenen verileri kaldırma ve null filtreleme gibi genel görevleri kolayca gerçekleştirebilirsiniz. Bu makalede, veri akışı komut parçacıkları kullanılarak bu işlevlerin işlem hatlarına kolayca eklenmesi açıklanmaktadır.
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma

1. **Yeni İşlem Hattı**’nı seçin.

1. Veri akışı etkinliği ekleyin.

1. **Kaynak ayarları** sekmesini seçin, bir kaynak dönüşümü ekleyin ve ardından veri kümelerinizin birine bağlayın.

    ![Kaynak türü eklemek için "kaynak ayarları" bölmesinin ekran görüntüsü.](media/data-flow/snippet-adf-2.png)

    Yinelenenleri kaldırma ve null denetim parçacıkları, veri akışı şema drlarından faydalanan genel desenler kullanır. Kod parçacıkları, veri kümenizdeki tüm şemalarla veya önceden tanımlanmış bir şemaya sahip olmayan veri kümeleriyle çalışır.

1. [Veri akışı betiğinin (DFS)](./data-flow-script.md#distinct-row-using-all-columns)"tüm sütunları kullanarak ayrı satır" bölümünde, DistinctRows için kod parçacığını kopyalayın.

1. [Veri akışı betiği belgeleri sayfasına gidin ve kod parçacığını ayrı satırlar için kopyalayın.](./data-flow-script.md#distinct-row-using-all-columns)

    ![Kaynak parçacığının ekran görüntüsü.](media/data-flow/snippet-adf-3.png)

1. Betiğinizdeki için, tanımından sonra `source1` ENTER tuşuna basın ve ardından kod parçacığını yapıştırın.

1. Aşağıdakilerden birini yapın:

   * Yapıştırılan kodun önüne **source1** yazarak bu yapıştırılan kod parçacığını grafikte daha önce oluşturduğunuz kaynak dönüşümüne bağlayın.

   * Alternatif olarak, grafikteki yeni dönüştürme düğümünden gelen akışı seçerek tasarımcıda yeni dönüşümü de bağlayabilirsiniz.

     !["Koşullu bölünmüş ayarlar" bölmesinin ekran görüntüsü.](media/data-flow/snippet-adf-4.png)

   Artık veri akışınız, tüm sütun değerlerinde genel bir karma kullanarak tüm satırlara göre gruplandıran toplam dönüşümü kullanarak kaynağınızdan yinelenen satırları kaldırır.
    
1. Verilerinizi null ve null olmayan bir akışa sahip bir akışa bölmek için bir kod parçacığı ekleyin. Bunun için:

1. [Kod parçacığı kitaplığına geri dönün ve bu sefer NULL denetimleri için kodu kopyalayın.](./data-flow-script.md#check-for-nulls-in-all-columns)

   b. Veri akışı tasarımcısında **betiği** yeniden seçin ve ardından bu yeni dönüştürme kodunu en alta yapıştırın. Bu eylem, bu dönüşümün adını yapıştırılan kod parçacığının önüne yerleştirerek betiği önceki dönüşümünüze bağlar.

   Veri akışı grafiğiniz artık şuna benzer görünmelidir:

    ![Veri akışı grafiğinin ekran görüntüsü.](media/data-flow/snippet-adf-1.png)

  Artık veri akışı betik kitaplığından mevcut kod parçacıklarını gerçekleştirerek ve bunları mevcut tasarımınıza ekleyerek genel yinelenenleri kaldırma ve null denetimleri içeren bir çalışma veri akışı oluşturdunuz.

## <a name="next-steps"></a>Sonraki adımlar

* Veri akışı [dönüştürmelerini](concepts-data-flow-overview.md)eşleme kullanarak veri akışı mantığınızın geri kalanını oluşturun.