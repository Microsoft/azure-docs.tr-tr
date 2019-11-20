---
title: Azure Data Factory eşleme veri akışı dönüşümü seçme
description: Azure Data Factory eşleme veri akışı dönüşümü seçme
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 6ef9712dd2fd6b8d53fd4ad2c3e07e1d6c8f1aec
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72387194"
---
# <a name="mapping-data-flow-select-transformation"></a>Eşleme veri akışı dönüşüm seçme


Seçiciliği (sütun sayısını azaltma), diğer ad sütunları ve akış adları ve sütunları yeniden sıralama için bu dönüşümü kullanın.

## <a name="how-to-use-select-transformation"></a>Seçme dönüşümünü kullanma
Seçim dönüşümü, bir akışın tamamına veya bu akıştaki sütunlara diğer ad atamanıza, farklı adlar atamanıza (diğer adlar) ve ardından bu yeni adlara daha sonra veri akışınızda başvurmasına olanak tanır. Bu dönüşüm, kendine katılacak senaryolar için yararlıdır. ADF veri akışında kendi kendine katılmayı uygulama yolu bir akış almak, bunu "yeni dal" ile dallandırma ve sonra hemen sonra da "Select" dönüştürmesi eklemektir. Bu akış artık özgün akışa geri katılabilmek için kullanabileceğiniz yeni bir ada sahip olur ve kendi kendine katılması oluşturursunuz:

![Kendi kendine Birleştir](media/data-flow/selfjoin.png "Kendi kendine Birleştir")

Yukarıdaki diyagramda, select Transform en üstte. Bu, özgün akışın "OrigSourceBatting" olarak diğer adını alıyor. Aşağıdaki Vurgulanan birleşim dönüşümünden, bu Select diğer ad akışını sağ birleşim olarak kullandığımızdan, Iç birleştirmenin sağ & sağ tarafında aynı anahtara başvurmamızı sağlayabilirsiniz.

Ayrıca, veri akışınızdan sütunları seçmek için bir yöntem olarak da kullanılabilir. Örneğin, havuzunuzu tanımlanmış 6 sütunlarınız varsa, ancak yalnızca belirli bir 3 ' ü dönüştürmek ve havuza akışa almak istiyorsanız, select Transform 'u kullanarak yalnızca bu 3 ' ü seçebilirsiniz.

![Dönüşüm seçin](media/data-flow/newselect1.png "Diğer ad Seç")

## <a name="options"></a>Seçenekler
* "Select" için varsayılan ayar tüm gelen sütunları dahil etmek ve bu özgün adları tutmak içindir. Seçim dönüşümünün adını ayarlayarak akışa diğer ad verebilirsiniz.
* Tek tek sütunlara, "Tümünü Seç" seçimini kaldırın ve alt kısımdaki sütun eşlemesini kullanın.
* Giriş veya çıkış meta verilerinden yinelenen sütunları kaldırmak için Yinelenenleri atla ' yı seçin.

![Yinelenenleri atla](media/data-flow/select-skip-dup.png "Yinelenenleri atla")

* Yinelemeleri atlamayı seçtiğinizde, sonuçlar Inceleme sekmesinde görünür. ADF, sütunun ilk oluşumunu tutar ve aynı sütunun sonraki oluşumunun akışınızdan kaldırıldığını görürsünüz.

> [!NOTE]
> Eşleme kurallarını temizlemek için **Sıfırla** düğmesine basın.

## <a name="mapping"></a>Eşlemeleri
Varsayılan olarak, select dönüşümü tüm sütunları otomatik olarak eşleştirir. Bu, tüm gelen sütunları çıktıda aynı ada geçirecek şekilde otomatik olarak eşler. Seçim ayarları 'nda ayarlanan çıkış akışı adı, akış için yeni bir diğer ad tanımlar. Otomatik eşleme için seçim kümesini tutarsanız, tüm akış için aynı olan tüm akışı aynı şekilde diğer ad olarak kullanabilirsiniz.

![Dönüşüm kurallarını seçin](media/data-flow/rule2.png "Kural tabanlı eşleme")

Sütunları diğer ad, kaldırma, yeniden adlandırma veya yeniden sıralama yapmak istiyorsanız, önce "otomatik eşleme" seçeneğini kapatmanız gerekir. Varsayılan olarak, "tüm giriş sütunları" olarak adlandırılabilecek bir varsayılan kural görürsünüz. Tüm gelen sütunların çıktılarıyla aynı ada sahip olacak şekilde her zaman izin vermeyi düşünüyorsanız, bu kuralı yerinde bırakabilirsiniz.

Ancak, özel kurallar eklemek istiyorsanız, "eşleme Ekle" seçeneğine tıklamanız gerekir. Alan eşleme, eşleme ve diğer ad için gelen ve giden sütun adlarının bir listesini sağlar. "Kural tabanlı eşleme" yi seçerek model eşleştirme kuralları oluşturun.

## <a name="rule-based-mapping"></a>Kural tabanlı eşleme
Kural tabanlı eşleme ' yi seçtiğinizde, gelen model kurallarını eşleştirmek ve giden alan adlarını tanımlamak için eşleşen ifadenizi değerlendirmek üzere ADF 'yi öğreneceksiniz. Hem alan hem de kural tabanlı eşlemelerin birleşimini ekleyebilirsiniz. Daha sonra alan adları, kaynaktan gelen meta veriler temelinde ADF tarafından çalışma zamanında oluşturulur. Oluşturulan alanların adlarını hata ayıklama sırasında ve veri önizleme bölmesini kullanarak görüntüleyebilirsiniz.

Model eşleştirme hakkında daha fazla ayrıntı, [sütun deseninin belgelerinde](concepts-data-flow-column-pattern.md)bulunabilir.

## <a name="next-steps"></a>Sonraki adımlar
* Yeniden adlandırma, yeniden sıralama ve diğer ad Sütunlarını Seç ' i kullandıktan sonra, verileri bir veri deposuna eklemek için [Havuz dönüştürmeyi](data-flow-sink.md) kullanın.
