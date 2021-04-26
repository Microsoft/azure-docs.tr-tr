---
title: Özel parametrelerle Azure Izleyici çalışma kitapları
description: Önceden oluşturulmuş ve özel parametreli çalışma kitapları ile karmaşık raporlamayı kolaylaştırın
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 2cb284e1978ad6c890835318c51c6095891397cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723019"
---
# <a name="interactive-workbooks"></a>Etkileşimli Çalışma Kitapları

Çalışma kitapları yazarların tüketicilere yönelik etkileşimli raporlar ve deneyimler oluşturmalarına olanak tanır. Etkileşim, çeşitli yollarla desteklenir.

## <a name="parameter-changes"></a>Parametre değişiklikleri

Bir çalışma kitabı kullanıcısı bir parametreyi güncelleştirdiğinde, parametresini kullanan herhangi bir denetim otomatik olarak yenilenir ve yeni durumu yansıtacak şekilde yeniden çizer. Azure portal raporlarının çoğu etkileşimi destekler. Çalışma kitapları bunu, en az Kullanıcı çabalarıyla basit bir şekilde sunar.

[Çalışma kitaplarında parametreler](workbooks-parameters.md) hakkında daha fazla bilgi edinin

## <a name="grid-tile-chart-selections"></a>Izgara, kutucuk, grafik seçimleri

Çalışma kitapları yazarların bir kılavuzdaki bir satırı tıklatmak, izleyen grafikleri satır içeriğine göre güncelleştirmesine olanak tanır.

Örneğin, bir Kullanıcı bir istek listesini ve hata sayısı gibi bazı istatistikleri gösteren bir kılavuza sahip olabilir. Bu, bir isteğe karşılık gelen bir satıra tıklanmayacak şekilde ayarlanabileceğinden, yalnızca bu isteğe filtre uygulamak için ayrıntılı grafiklerin güncelleştirilmesine neden olur.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Kılavuz satırı üzerinde etkileşim kurma

1. _Düzenleme_ araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir günlük sorgusu denetimi eklemek için _Sorgu Ekle_ bağlantısını kullanın.
3. Sorgu türünü _günlük_, kaynak türü (örneğin, Application Insights) ve hedeflenecek kaynakları seçin.
4. Analiz etmek için KQL 'i girmek üzere sorgu düzenleyicisini kullanın

    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc
    ```

5. `Run query` sonuçları görmek için
6. Sorgu altbilgisinde _Gelişmiş ayarlar_ simgesini seçin (simge dişli gibi görünür). Bu, Gelişmiş ayarlar bölmesini açar.
7. Ayarı denetleyin: `When an item is selected, export a parameter` .
8. Seçtiğiniz ayar altında *parametre Ekle* ' yi seçin ve aşağıdaki bilgilerle doldurun.
    1. Dışarı aktarılacak alan: `Request`
    2. Parametre adı: `SelectedRequest`
    3. Varsayılan değer: `All requests`
9. Kaydet'i seçme

    ![Alanları parametre olarak dışa aktarma ayarlarına sahip gelişmiş düzenleyiciyi gösteren ekran görüntüsü.](./media/workbooks-interactive/export-parameters-add.png)

10. `Done Editing` öğesini seçin.
11. 2 ve 3. adımları kullanarak başka bir sorgu denetimi ekleyin.
12. Analizinize yönelik KQL 'yi girmek için sorgu düzenleyicisini kullanın.
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
13. `Run query` sonuçları görmek için.
14. _Görselleştirmeyi_ olarak değiştirin `Area chart` .
15. İlk kılavuzda seçilecek bir satır seçin. Aşağıdaki alan grafiğinin seçili istek için nasıl filtreleyeceğini aklınızda bulabilirsiniz.

Elde edilen rapor, düzenleme modunda şöyle görünür:

![Düzenleme modundaki ilk iki sorgunun ekran görüntüsü.](./media/workbooks-interactive//interactivity-grid-create.png)

Aşağıdaki görüntüde, aynı ilkelere bağlı olarak okuma modunda daha ayrıntılı bir etkileşimli rapor gösterilmektedir. Raporda, parametreleri dışarı aktarmak için tıklama tıklamaları kullanılır. Bu, sırasıyla iki grafikte ve bir metin bloğunda kullanılır.

![Okuma modunda kılavuz tıklamaları kullanan bir rapor ekran görüntüsü.](./media/workbooks-interactive/interactivity-grid-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Tüm satırın içeriğini dışa aktarma

Bazı durumlarda, yalnızca belirli bir sütun yerine seçili satırın tüm içeriğinin dışarı aktarılması tercih edilir. Bu gibi durumlarda, `Field to export` yukarıdaki 7,1 adımında özelliği ayarını yok olarak bırakın. Çalışma kitapları, tüm satır içeriğini parametresine JSON olarak dışa aktarır.

Başvuran KQL denetiminde, `todynamic` JSON 'u ayrıştırmak ve ayrı sütunlara erişmek için işlevini kullanın.

## <a name="grid-cell-clicks"></a>Izgara hücresi tıklamaları

Çalışma kitapları, yazarlara, adlı özel bir ızgara sütun işleyicisi türü aracılığıyla etkileşim eklemesine olanak tanır `link renderer` . Bağlantı Oluşturucu, bir kılavuz hücresini hücrenin içeriğine göre köprüye dönüştürür. Çalışma kitapları, kaynak genel bakış dikey pencereleri, özellik paketi görüntüleyicileri, uygulama öngörüleri arama, kullanım, işlem izleme vb. gibi birçok bağlantı Oluşturucu türünü destekler.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Kılavuz hücre tıklamalarını kullanarak etkileşimi ayarlama

1. _Düzenleme_ araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir günlük sorgusu denetimi eklemek için _Sorgu Ekle_ bağlantısını kullanın.
3. Sorgu türünü _günlük_, kaynak türü (örneğin, Application Insights) ve hedeflenecek kaynakları seçin.
4. Analiz etmek için KQL 'i girmek üzere sorgu düzenleyicisini kullanın

    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```

5. `Run query` sonuçları görmek için
6. _Sütun ayarlarını_ seçerek ayarlar bölmesini açın.
7. _Sütunlar_ bölümünde, şunu ayarlayın:
    1. _Örnek_ sütun Oluşturucu: `Link` , açılacak görünüm: `Cell Details` , bağlantı etiketi: `Sample`
    2. _Count_ -Column işleyici: `Bar` , renk paleti: `Blue` , minimum değer: `0`
    3. _İstek_ sütunu işleyici: `Automatic`
    4. Değişiklikleri uygulamak için _Kaydet ve Kapat ' ı_ seçin

    ![Sütun ayarı sekmesinin ekran görüntüsü.](./media/workbooks-interactive/column-settings.png)

8. `Sample`Kılavuzdaki bağlantılardan birine tıklayın. Bu, Örneklenmiş bir isteğin ayrıntılarına sahip bir bölme açar.

    ![Örneklenmiş isteğin ayrıntı bölmesinin ekran görüntüsü.](./media/workbooks-interactive/details.png)

### <a name="link-renderer-actions"></a>Bağlantı Oluşturucu eylemleri

| Bağlantı eylemi | Tıklama eylemi |
|:------------- |:-------------|
| `Generic Details` | Özellik Kılavuzu bağlam sekmesindeki satır değerlerini gösterir |
| `Cell Details` | Bir özellik Kılavuzu bağlam sekmesinde hücre değerini gösterir. Hücre (örneğin, konum, rol örneği vs. gibi istek özelliklerine sahip JSON) içeren dinamik bir tür içerdiğinde yararlı olur. |
| `Cell Details` | Bir özellik Kılavuzu bağlam sekmesinde hücre değerini gösterir. Hücre (örneğin, konum, rol örneği vs. gibi istek özelliklerine sahip JSON) içeren dinamik bir tür içerdiğinde yararlı olur. |
| `Custom Event Details` | Hücrede özel olay KIMLIĞI () ile Application Insights arama ayrıntılarını açar `itemId` |
| `* Details` | Bağımlılıklar, özel durumlar, sayfa görünümleri, istekler ve izlemeler dışında özel olay ayrıntılarına benzer. |
| `Custom Event User Flows` | Hücredeki özel olay adında özetlenen Application Insights Kullanıcı Akışları deneyimini açar |
| `* User Flows` | Özel durum, sayfa görünümleri ve istekler hariç özel olay Kullanıcı Akışları benzer |
| `User Timeline` | Kullanıcı zaman çizelgesini hücrede Kullanıcı KIMLIĞI (user_Id) ile açar |
| `Session Timeline` | Hücredeki değer için Application Insights arama deneyimini açar (örneğin, ABC hücresindeki değer olan ' abc ' metnini arayın) |
| `Resource overview` | Hücredeki kaynak KIMLIĞI değerini temel alarak, kaynağın genel görünümünü portalda açın |

## <a name="conditional-visibility"></a>Koşullu görünürlük

Çalışma kitabı, kullanıcıların belirli denetimleri parametre değerlerine göre görünmesine veya kaybolmasına olanak sağlar. Bu, yazarların, kullanıcı girişine veya telemetri durumuna göre raporların farklı görünmesini sağlar. Örnek, tüketicilere yalnızca bir Özet gösterir, ancak yanlış bir şeyler olduğunda tam ayrıntıları gösterir.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Koşullu görünürlük kullanarak etkileşim kurma

1. [Kılavuz satırında etkileşimi ayarlama](#setting-up-interactivity-on-grid-row-click) bölümündeki adımları izleyerek iki etkileşimli denetim ayarlayın.
2. En üste yeni bir parametre ekleyin:
    1. Ad: `ShowDetails`
    2. Parametre türü: `Drop down`
    3. Gerekli: `checked`
    4. Veri al: `JSON`
    5. JSON girişi: `["Yes", "No"]`
    6. Değişiklikleri uygulamak için Kaydet.

    ![Parametre Ekle düğmesini seçtikten sonra parametre Düzenle bölmesi görüntülenir.](./media/workbooks-interactive/edit-parameter.png)

3. Parametre değerini ayarla `Yes`

    ![Tamamlandı düzenlemesi düğmesinin üstünde, parametre değerini ayarlamanıza olanak tanıyan bir açılır](./media/workbooks-interactive/set-parameter.png)

4. Alan grafiği ile sorgu denetiminde, _Gelişmiş ayarlar_ simgesini (dişli simgesi) seçin.
5. Ayarı denetleyin `Make this item conditionally visible`
    1. Bu öğe `ShowDetails` parametre değeri `equals` ise görünür `Yes`
6. Değişiklikleri uygulamak için _düzenlemeleri bitti_ ' yi seçin.
7. Okuma moduna girmek için çalışma kitabı araç çubuğunda _Düzenle bitti_ ' yi seçin.
8. Parametresinin değerini `ShowDetails` olarak değiştirin `No` . Aşağıdaki grafiğin kaybolduğuna dikkat edin.

Aşağıdaki `ShowDetails` görüntüde olduğu gibi görünen durum gösterilmektedir `Yes`

![Grafiğin görünür olduğu koşullu görünürlüğü gösteren ekran görüntüsü](./media/workbooks-interactive/interactivity-conditional-visibility-visible.png)

Aşağıdaki görüntüde nerede olduğu gizli durum gösterilmektedir `ShowDetails``No`

![Grafiğin gizli olduğu koşullu görünürlüğü gösteren ekran görüntüsü](./media/workbooks-interactive/interactivity-conditional-visibility-invisible.png)

## <a name="interactivity-with-multiple-selections-in-grids-and-charts"></a>Kılavuzlar ve grafiklerde birden çok seçim ile etkileşim

Sorgu ve ölçüm adımları Ayrıca bir satır (veya birden çok satır) seçildiğinde bir veya daha fazla parametreyi dışarı aktarabilir.

![Birden çok parametreli parametreleri dışarı aktarma ayarlarını gösteren ekran görüntüsü. ](./media/workbooks-interactive/interactivity-export-parameters.png)

1. Kılavuzu görüntüleyen sorgu adımında Gelişmiş ayarlar ' a gidin.
2. Onay `When items are selected, export parameters` kutusunu işaretleyin. Ek denetimler görüntülenir.
3. Onay `allow selection of multiple values` kutusunu işaretleyin.
    1. Görüntülenmiş görselleştirme, çoklu seçim açılan menü parametreleri kullanılırken olduğu gibi, birden çok seçim ve verilen parametrenin değerlerinin değer dizileri olacak şekilde izin verir.
    2. İşaretlenmezse, görüntüleme görselleştirmesi yalnızca son seçili öğeye göre yapılır. Tek seferde tek bir değer dışarı aktarılıyor.
4. Dışarı aktarmak istediğiniz her parametre için *parametre Ekle* düğmesini kullanın. Verilecek parametrenin ayarlarını içeren bir açılan pencere görüntülenir.

Tek seçim etkinleştirildiğinde, yazar orijinal verilerin hangi alanı dışarı aktarılacağını belirtebilir. Alanlar, hiçbir şey seçilmezse parametre adı, parametre türü ve varsayılan değeri içerir (isteğe bağlı).

Çoklu seçim etkinleştirildiğinde, yazar orijinal verilerin hangi alanın dışarı aktarılacağını belirtir. Alanlar parametre adı, parametre türü, tırnak işareti ve sınırlayıcısı içerir. Bir sorguda değiştiriliyorsa, ve sınırlayıcı değerleri, ok değerlerini metne dönüştürmek için kullanılır. Çoklu seçim ' ın hiçbir değer seçilmezse, varsayılan değer boş bir dizidir.

> [!NOTE]
> Çoklu seçim için yalnızca benzersiz değerler verilecek, "1, 1, 2, 1" gibi çıkış dizisi değerlerini görmezsiniz, çıkış değerleri olarak "1, 2" alacaksınız.

Dışarı aktarma ayarlarında "dışarı aktarılacak alan" ayarını boş bırakabilirsiniz. Bunu yaparsanız, verilerdeki tüm kullanılabilir alanlar, anahtar: değer çiftlerinin bir strıngımış JSON nesnesi olarak aktaralınacaktır. Izgaralar ve başlıklar için bu kılavuz içindeki tüm alanlar olacaktır. Grafikler için, kullanılabilir alanlar x, y, seriler ve etiket olur (grafiğin türüne bağlı olarak).

Varsayılan davranış bir parametreyi metin olarak dışarı aktarmak olsa da, alanın bir abonelik veya kaynak KIMLIĞI olduğunu biliyorsanız bunu dışa aktarma parametre türü olarak kullanın. Bu, parametrenin bu tür parametreleri gerektiren konumlarda aşağı akış olarak kullanılmasına izin verir.

## <a name="next-steps"></a>Sonraki adımlar

* Çok sayıda zengin görselleştirmeler seçeneği hakkında daha fazla [bilgi edinmeye başlayın](./workbooks-overview.md#visualizations) .
* Çalışma kitabı kaynaklarınıza erişimi [denetleme](./workbooks-access-control.md) ve paylaşma.