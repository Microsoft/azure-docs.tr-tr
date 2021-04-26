---
title: Azure Izleyici çalışma kitabı bileşik çubuk Oluşturucusu
description: Tüm Azure Izleyici çalışma kitabı bileşik çubuk Oluşturucu görselleştirmesi hakkında bilgi edinin.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.author: lagayhar
ms.openlocfilehash: 9a02299853174192c6963cbb382ceb1aa06ac088
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101728612"
---
# <a name="composite-bar-renderer"></a>Bileşik çubuk Oluşturucusu

Çalışma kitabı, birden çok çubuktan oluşan bir çubuk olan bileşik çubuğu kullanarak veri işlemeye olanak sağlar.

Aşağıdaki görüntüde, kaç tane sunucunun çevrimiçi, çevrimdışı ve kurtarma durumunda olduğunu gösteren veritabanı durumu için Birleşik çubuk gösterilmektedir.

![Veritabanı durumu için bileşik çubuğun ekran görüntüsü.](./media/workbooks-composite-bar/database-status.png)

Birleşik çubuk Oluşturucu kılavuzlar, kutucuklar ve grafik görselleştirmeleri için desteklenir.

## <a name="adding-composite-bar-renderer"></a>Bileşik çubuk Oluşturucusu ekleniyor

1. Araç çubuğu öğesini *Düzenle* seçeneğini belirleyerek çalışma kitabını düzenleme moduna geçirin.
2. *Ekle* ' yi ve ardından *Sorgu Ekle*' yi seçin
3. *Veri kaynağını* "JSON" ve *görselleştirme* olarak "Grid" olarak ayarlayın.
4. Aşağıdaki JSON verilerini ekleyin.

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. Sorguyu çalıştırın.
6. Ayarları açmak için **sütun ayarları** ' nı seçin.
7. *Sütunlardan* "Toplam" ı seçin ve *sütun Oluşturucu* için "bileşik çubuk" seçeneğini belirleyin.
8. *Birleşik çubuk ayarları* altında aşağıdaki ayarları ayarlayın.

| Sütun adı | Renk        |
|-------------|--------------|
| çevrimiçi      | Yeşil        |
| kurtar  | Yellow       |
| çevrimdışı     | Kırmızı (parlak) |

9. Etiket ekle:`["online"] of ["total"] are healthy`
10. Çevrimiçi, çevrimdışı ve kurtarmayla ilgili sütun ayarları ' nda, sütun oluşturucuyu "gizli" (Isteğe bağlı) olarak ayarlayabilirsiniz.
11. En üstteki *etiketleri* seçin ve toplam sütunun etiketini "veritabanı durumu" (isteğe bağlı) olarak güncelleştirin.
12. **Uygula** ' yı seçin

Birleşik çubuk ayarları aşağıdaki ekran görüntüsüne benzer şekilde görünür:

![Yukarıda açıklanan ayarları içeren Birleşik çubuk sütun ayarlarının ekran görüntüsü.](./media/workbooks-composite-bar/composite-bar-settings.png)

Yukarıdaki ayarlarla Birleşik çubuk:

![Bileşik çubuğun ekran görüntüsü.](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>Bileşik çubuk ayarları

Sütun adı ' nı ve ilgili rengi bileşik çubuğun bir parçası olarak işlemek için karşılık gelen rengi seçin. Satırları yukarı ve aşağı ekleyebilir, silebilir ve taşıyabilirsiniz.

### <a name="label"></a>Etiketle

Bileşik çubuk etiketi, bileşik çubuğun üstünde görüntülenir. Statik metin, sütun ve parametre karışımından yararlanabilirsiniz.  Etiket boşsa, geçerli sütunların değeri etiket olarak görüntülenir. Önceki örnekte, etiket alanını siyah olarak bıraktıysanız toplam sütun değeri görüntülenir.

İle sütunlara bakın `["columnName"]` .

İle parametrelere bakın `{paramName}` .

Hem sütun adı hem de parametre adı büyük/küçük harfe duyarlıdır. Ayrıca "Bu öğeyi bağlantı olarak yap" seçeneğini belirleyip bağlantı ayarlarını ekleyerek etiketleri bir bağlantı haline getirebilirsiniz.

### <a name="aggregation"></a>Toplama

Toplamalar, görselleştirmelere göre ağaç/grup için faydalıdır. Grup satırı için bir sütunun verileri, bu sütunun toplama kümesi tarafından karardır. Bileşik çubuklar için geçerli olan üç tür toplama vardır: None, Sum ve Inherit.

Ayarları gruba göre eklemek için:

1. Sütun ayarları ' nda, ayarları eklemek istediğiniz sütuna gidin.
2. Ağaç *türü* altındaki *Ayarlar ve grupla* öğesine göre **Gruplandır** ' ı seçin.
3. Gruplandırmak istediğiniz alanı seçin.

![Ayarlara göre gruplandırma ekran görüntüsü.](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>Yok

None toplama, Grup satırları için bu sütun için sonuç göstermeyeceği anlamına gelir.

![None toplamasının bulunduğu bileşik çubuğun ekran görüntüsü.](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>Sum

Toplama, Sum olarak ayarlandıysa, Grup satırındaki sütun, onu işlemek için kullanılan sütunların toplamını kullanarak bileşik çubuğu gösterir. Etiket, içinde başvurulan sütunların toplamını da kullanacaktır.

Çevrimiçi, çevrimdışı, ve tümünün kurtarmasının en fazla toplama için ayarlanmış olması ve toplam sütununun toplamı toplamın toplamıdır.

![Toplam toplama içeren bileşik çubuğun ekran görüntüsü.](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>Devralınır

Toplama, devralma olarak ayarlandıysa, Grup satırındaki sütun, toplama çubuğunu, Kullanıcı tarafından işlemek için kullanılan sütunlar için toplama kümesi kullanılarak gösterir. Etikette kullanılan sütunlar ayrıca Kullanıcı tarafından ayarlanan toplamayı kullanır. Geçerli sütun Oluşturucu Birleşik çubuk ise ve etikette (yukarıdaki örnekteki "Toplam" gibi) başvuru varsa, Sum bu sütun için toplama olarak kullanılır.

Aşağıdaki örnekte, çevrimiçi, çevrimdışı ve tümünün kurtarmasının maksimum toplamı kendileri olarak ayarlanmıştır ve toplam sütununun toplamı devralınır.

![Toplamayı devralma ile bileşik çubuğun ekran görüntüsü.](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>Sıralama

Kılavuz görselleştirmeleri için, bileşik çubuk işleyiciyle sütun için satırları sıralama, Birleşik çubuk bilgisayarı dinamik olarak oluşturmak için kullanılan sütunların toplamı olan değere göre geçerlidir. Önceki örneklerde, sıralama için kullanılan değer çevrimiçi, kurtarma ve bu belirli satır için çevrimdışı sütunların toplamıdır.

## <a name="tiles-visualization"></a>Kutucuk görselleştirmesi

1. **Ekle** ve *Sorgu Ekle*' yi seçin.
2. Veri kaynağını JSON olarak değiştirme [önceki örnekteki](#adding-composite-bar-renderer)verileri girin.
3. Görselleştirmeyi *döşemeler* olarak değiştirin.
4. Sorguyu çalıştırın.
5. **Kutucuk ayarları**' nı seçin.
6. Döşeme alanlarında *sol* ' u seçin.
7. Aşağıdaki ayarları *alan ayarları* altında girin.
    1. "Sunucu" sütununu kullanın.
    2. Sütun Oluşturucusu: "metin".
8. Döşeme alanlarında *alt* ' i seçin.
9. Aşağıdaki ayarları *alan ayarları* altında girin.
    1. Sütun kullan: "Toplam".
    2. Sütun Oluşturucusu: "bileşik çubuk".
    3. "Bileşik çubuk ayarları" altında aşağıdaki ayarları ayarlayın yazın.

    | Sütun adı | Renk        |
    |-------------|--------------|
    | çevrimiçi      | Yeşil        |
    | kurtar  | Yellow       |
    | çevrimdışı     | Kırmızı (parlak) |

    4. Etiket ekle: `["online"] of ["total"] are healthy` .
10. **Uygula**’yı seçin.

Kutucuklar için bileşik çubuk ayarları:

![Yukarıda açıklanan ayarları içeren Birleşik çubuk kutucuk ayarlarının ekran görüntüsü.](./media/workbooks-composite-bar/tiles-settings.png)

Yukarıdaki ayarlarla kutucuklar için Birleşik çubuk görünümü şöyle görünür:

![Birleşik çubuk kutucuklarının ekran görüntüsü.](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>Grafik görselleştirmesi

Grafik görselleştirmesi için bir bileşik çubuk Oluşturucu oluşturmak için (Hive kümeleri yazın), aşağıdaki yönergeleri izleyin.

1. **Ekle** ve *Sorgu Ekle*' yi seçin.
2. Veri kaynağını JSON olarak değiştirme [önceki örnekteki](#adding-composite-bar-renderer)verileri girin.
3. Görselleştirmeyi *grafik* olarak değiştirin.
4. Sorguyu çalıştırın.
5. **Grafik ayarları**' nı seçin.
6. Düğüm biçimi ayarlarında *Merkezi içerik* ' i seçin.
7. Aşağıdaki ayarları *alan ayarları* altında girin.
    1. Sütun kullan: "Toplam".
    2. Sütun Oluşturucusu: "bileşik çubuk".
    3. *Birleşik çubuk ayarları* altında aşağıdaki ayarları girin.

    |Sütun adı  |     Renk    |
    |-------------|--------------|
    | çevrimiçi      | Yeşil        |
    | kurtar  | Yellow       |
    | çevrimdışı     | Kırmızı (parlak) |

   4. Etiket ekle: `["online"] of ["total"] are healthy` .
9. Aşağıdaki ayarları *Düzen ayarları* altında girin.
    1. Grafik türü: **Hive kümeleri**.
    2. Düğüm KIMLIĞI seçin: "sunucu".
    3. Alana göre gruplandır: "none".
    4. Düğüm boyutu: 100.
    5. Altıgenler arasındaki kenar boşluğu: 5.
    6. Renklendirme türü türü: **yok**.
1. **Uygula**’yı seçin.
    
Grafikler için bileşik çubuk ayarları:

![Yukarıda açıklanan ayarları içeren Birleşik çubuk grafik ayarlarının ekran görüntüsü.](./media/workbooks-composite-bar/graphs-settings.png)

Yukarıdaki ayarlarla Graph için Birleşik çubuk görünümü şöyle görünür:

![Hive kümelerine sahip bileşik çubuk grafiklerin ekran görüntüsü.](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>Sonraki adımlar

* Azure Resource Manager çalışma kitaplarını [dağıtın](../visualize/workbooks-automate.md) .
* Çalışma kitabı kaynaklarınıza erişimi [denetleme](./workbooks-access-control.md) ve paylaşma.