---
title: Azure Izleyici çalışma kitabı metin görselleştirmeleri
description: Tüm Azure Izleyici çalışma kitabı metin görselleştirmeleri hakkında bilgi edinin.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: e8f2d9495484b781b26962c2946b5bada6c38b4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100622243"
---
# <a name="text-visualizations"></a>Metin görselleştirmeleri

Çalışma kitapları yazarların çalışma kitaplarına metin blokları eklemesine izin verir. Metin, kullanıcıların verilerinizi yorumlamasını, Bölüm başlıklarınızı vb. sağlamak için Telemetriyi insan olarak analiz edebilir.

[![Apdex metin tablosunun ekran görüntüsü](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

Metin, tam biçimlendirme denetimi sağlayan bir markı denetimi aracılığıyla eklenir. Bunlar farklı başlık ve yazı tipi stilleri, köprüler, tablolar vb. içerir.

Düzenleme modu:

![Düzenleme modundaki bir metin adımının ekran görüntüsü.](./media/workbooks-text-visualizations/text-edit-mode.png)

Önizleme modu:

![Önizleme sekmesinde düzenleme modundaki bir metin adımının ekran görüntüsü.](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>Metin denetimi ekleme

1. **Düzenleme** araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir metin denetimi eklemek için **metin ekle** bağlantısını kullanın.
3. Düzenleyici alanına Markaşağı ekleyin.
4. Azure portal standart bilgi/uyarı/başarı/hata stili ile Sarmalanan düz markaşağı ve markaşağı arasında geçiş yapmak için *metin stili* seçeneğini kullanın.
5. İçeriğinizi nasıl görüneceğine bakmak için **Önizleme** sekmesini kullanın. Düzenlenirken, önizleme, içeriğini boyutunu sınırlamak için bir kaydırma çubuğu alanının içindeki içeriği gösterir; Ancak, çalışma zamanında markaşağı içeriği, gereken alanı kaydırma çubuğu olmadan dolduracak şekilde genişletilir.
6. Adımı düzenlemenizi tamamlamaya yönelik **Düzenle** düğmesini seçin.

> [!TIP]
> Farklı biçimlendirme seçenekleri hakkında bilgi edinmek için bu [Markaşağı](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) ile bu sayfayı kullanın.

## <a name="text-styles"></a>Metin stilleri

Aşağıdaki metin stilleri metin adımı için kullanılabilir:

| Stil     | Açıklama                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | Başka biçimlendirme uygulanmaz.                                                      |
| `info`    | Portalın "bilgi" stili,  `ℹ` ya da benzer bir simge ve genellikle mavi arka plan.      |
| `error`   | Portalın "hata" stili, ya da benzer bir `❌` simgeyle ve genellikle kırmızı bir arka planla.     |
| `success` | Portalın "başarı" stili, ya da benzer bir `✔` simgeyle ve genel olarak yeşil arkaplanıyla.  |
| `upsell`  | Portalın "yukarı satış" stili, bir `🚀` veya benzer bir simge ve genellikle mor arka plan. |
| `warning` | Portalın "uyarı" stili, `⚠` ya da benzer bir simge ve genellikle mavi arka plan.   |

Belirli bir stili seçmek yerine, stilin kaynağı olarak bir metin parametresi de seçebilirsiniz. Parametre değeri yukarıdaki metin değerlerinden biri olmalıdır. Bir değerin veya tanınmayan değerin yokluğu, stil olarak kabul edilir `plain` .

Bilgi stili örneği:

![Bilgi stilinin neye benzediğinin ekran görüntüsü.](./media/workbooks-text-visualizations/text-preview-info-style.png)

Uyarı stili örneği:

![Uyarı stilinin nasıl göründüğünü ekran görüntüsü.](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Çalışma kitaplarında grafik](workbooks-chart-visualizations.md)oluşturmayı öğrenin.
* [Çalışma kitaplarında kılavuz](workbooks-grid-visualizations.md)oluşturmayı öğrenin.
