---
title: 'Nasıl yapılır: veri kataloğunu arama'
description: Bu makale, veri kataloğu arama hakkında genel bakış sunar.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 178604335968c3664bde51c144759c1c040c359d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564922"
---
# <a name="search-the-azure-purview-data-catalog"></a>Azure purview Veri Kataloğu 'Nda arama

Veri bulma bir veri analizinin veya veri tüketicileri için veri yönetimi iş yükünün ilk adımıdır. Veri bulma zaman alabilir, çünkü istediğiniz verileri nerede bulabileceğinizi bilmiyor olabilirsiniz. Verileri bulduktan sonra bile, verilere güvenip güvenmeyeceğinizi ve buna bir bağımlılık alıp vermeyeceğinizi da görebilirsiniz.

Azure purview 'da aramanın hedefi, önemli verileri hızlı bir şekilde bulmak için veri bulma sürecini hızlandırmaktır. Bu makalede, aradığınız verileri hızlı bir şekilde bulmak için Azure purview Veri Kataloğu 'nun nasıl aranacağı özetlenmektedir.

## <a name="search-the-catalog-for-assets"></a>Katalogda varlıkları ara

Azure purview 'da arama çubuğu, purview Studio UX ' in en üstünde bulunur.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Azure purview arama çubuğunun konumunu gösteren ekran görüntüsü" border="true":::

Arama çubuğuna tıkladığınızda, son arama geçmişinizi ve en son erişilen varlıkları görebilirsiniz. Son görüntülenen varlıkların tümünü görmek için "tümünü göster" i seçin.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Herhangi bir anahtar sözcük girildikten önce arama çubuğunu gösteren ekran görüntüsü" border="true":::

Varlığınızın adı, veri türü, sınıflandırmalar ve Sözlük terimleri gibi tanımlanmasına yardımcı olan anahtar sözcükleri girin. İstediğiniz varlığınıza ilişkin anahtar sözcüklere girerken Azure purview, arama yapılacak ve potansiyel varlık eşleştirmelerinin önerilerini görüntüler. Aramanızı gerçekleştirmek için "arama sonuçlarını görüntüle" ye tıklayın veya "Enter" tuşuna basın.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Anahtar sözcüklere bir kullanıcı girdiğinde arama çubuğunu gösteren ekran görüntüsü" border="true":::

Arama sonuçları sayfası, ilgi sırasına göre belirtilen anahtar sözcüklerle eşleşen varlıkların bir listesini gösterir. Bir varlığın uygunluk Puanını etkileyebilecek çeşitli faktörler vardır. Aradığınız varlık için uygulanan belirli veri depolarını, sınıflandırmaları, kişileri, etiketleri ve sözlük koşullarını seçerek listeyi daha fazla filtreleyebilirsiniz.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Bir aramanın sonuçlarını gösteren ekran görüntüsü" border="true":::

 Şema, kökenini ve varlık sahipleri dahil özellikleri görüntüleyebileceğiniz varlık ayrıntıları sayfasını görüntülemek için istediğiniz varlığa tıklayın.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Varlık ayrıntıları sayfasını gösteren ekran görüntüsü" border="true":::

## <a name="search-query-syntax"></a>Arama sorgusu sözdizimi

Tüm arama sorguları, anahtar sözcüklerden ve işleçlerden oluşur. Anahtar sözcüğü, bir varlığın özelliklerinin parçası olacak bir şeydir. Olası anahtar sözcükler bir sınıflandırma, sözlük terimi, varlık açıklaması veya bir varlık adı olabilir. Anahtar sözcüğü, yalnızca eşleştirmek istediğiniz özelliğin bir parçası olabilir. Azure purview 'ın Aradığınız varlıkları döndürdüğünden emin olmak için anahtar kelimeleri ve aşağıda listelenen işleçleri kullanın. 

Aşağıda, bir arama sorgusu oluşturmak için kullanılabilen işleçler verilmiştir. İşleçler, tek bir sorguda gerek duyduğu kadar birleştirilebilir.

| Operatör | Tanım | Örnek |
| -------- | ---------- | ------- |
| VEYA | Bir varlığın iki anahtar kelimelerden en az birine sahip olması gerektiğini belirtir. Tümü büyük harf olmalıdır. Boşluk da bir veya işleçtir.  | Sorgu `hive OR database` ' Hive ' veya ' Database ' veya her ikisini de içeren varlıkları döndürür. |
| AND | Bir varlığın her iki anahtar sözcüğe sahip olması gerektiğini belirtir. Tüm büyük harfler olmalıdır | Sorgu `hive AND database` hem ' Hive ' hem de ' Database ' içeren varlıkları döndürür. |
| NOT | Bir varlığın NOT yan tümcesinin sağ tarafındaki anahtar sözcüğü içeremeyeceği belirtir | Sorgu `hive NOT database` ' Hive ' içeren varlıkları döndürür, ancak ' Database ' değil. |
| () | Bir anahtar sözcük ve işleç kümesini birlikte gruplandırır. Birden çok işleç birleştirilirken, parantez işlem sırasını belirtir. | Sorgu `hive AND (database OR warehouse)` ' Hive ' ve ' Database ' ya da ' Warehouse ' veya her ikisini de içeren varlıkları döndürür. |
| "" | Sorgunun eşleşmesi gereken bir tümceciğin tam içeriğini belirtir. | Sorgu, `"hive database"` özelliklerinde "Hive veritabanı" ifadesini içeren varlıkları döndürür |
| * | Bire çok karakterle eşleşen bir joker karakter. Anahtar kelimesinin ilk karakteri olamaz. | Sorgu, ' `dat*` Data ' veya ' Database ' gibi ' dat ' ile başlayan özellikleri olan varlıkları döndürür. |
| ? | Tek bir karakterle eşleşen bir joker karakter. Anahtar kelimesinin ilk karakteri olamaz | Sorgu, `dat?` ' dat ' ile başlayan ve ' Date ' veya ' Data ' gibi dört harf olan varlıkları döndürür. |

> [!Note]
> Her zaman tüm büyük harfler içinde Boole işleçleri (**ve** **veya**, **değil**) belirtin. Aksi takdirde, büyük/küçük harf ek boşluklar değildir.

## <a name="next-steps"></a>Sonraki adımlar

- [Sözlük terimleri oluşturma, içeri aktarma ve dışarı aktarma](how-to-create-import-export-glossary.md)
- [İş sözlüğü için terim şablonlarını yönetme](how-to-manage-term-templates.md)
