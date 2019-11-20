---
title: Azure Izleyici çalışma kitapları ile etkileşimli raporlar oluşturma | Parametreler oluşturuluyor | Microsoft docs
description: Önceden oluşturulmuş ve özel parametreli çalışma kitapları ile karmaşık raporlamayı kolaylaştırın
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c221abf423a21d424bd5198696a61d7ec83521e9
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165764"
---
# <a name="workbook-parameters"></a>Çalışma kitabı parametreleri

Parametreler, çalışma kitabı yazarlarının tüketicilerden giriş toplamasına ve çalışma kitabının diğer bölümlerine başvurmasına izin verir – genellikle sonuç kümesini kapsam veya doğru görseli ayarlama. Yazarların etkileşimli raporlar ve deneyimler oluşturmasına izin veren bir temel yetenektir. 

Çalışma kitapları, parametre denetimlerinizin tüketicilere nasıl sunulduğunu denetlemenizi sağlar – metin kutusu-açılan, tek-ve çoklu seçim, metin, JSON, KQL veya Azure Kaynak Grafının vb. değerleri.  

Desteklenen parametre türleri şunlardır:
* [Zaman](workbooks-time.md) -kullanıcının önceden doldurulan zaman aralıklarında seçmesine izin verir veya özel bir Aralık seçebilir
* [Aşağı açılan](workbooks-dropdowns.md) -kullanıcının bir değerden veya değer kümesinden seçmesine izin verir
* [Metin](workbooks-text.md) -kullanıcının rastgele metin girmelerini sağlar
* [Kaynak](workbooks-resources.md) -kullanıcının bir veya daha fazla Azure kaynağı seçmesine izin verir
* [Abonelik](workbooks-resources.md) -kullanıcının bir veya daha fazla Azure abonelik kaynağı seçmesine izin verir
* Kaynak türü-kullanıcının bir veya daha fazla Azure Kaynak türü değeri seçmesine izin verir
* Konum-kullanıcının bir veya daha fazla Azure konum değeri seçmesine izin verir

Bu parametre değerlerine, çalışma kitaplarının diğer bölümlerinde bağlamalar veya değer genişletmeleri aracılığıyla başvurulabilir.

## <a name="creating-a-parameter"></a>Parametre oluşturma
1. Düzenleme modunda boş bir çalışma kitabıyla başlayın.
2. Çalışma kitabı içindeki bağlantılardan _parametre Ekle_ ' yi seçin.
3. Mavi _parametre Ekle_ düğmesine tıklayın.
4. Açılır yeni parametre bölmesinde şunu girin:
    1. Parametre adı: `TimeRange` *(parametre __adlarının__ boşluk veya özel karakter **içeremediğini** unutmayın)*
    2. Görünen ad: `Time Range` *(ancak __görünen adlar__ boşluk, özel karakterler, emoji, vb.) içerebilir.*
    2. Parametre türü: `Time range picker`
    3. Gerekli: `checked`
    4. Kullanılabilir zaman aralıkları: son saat, son 12 saat, son 24 saat, son 48 saat, son 3 gün, son 7 gün ve özel zaman aralığı seçimine Izin ver
5. Parametresini oluşturmak için araç çubuğundan ' Kaydet ' seçeneğini belirleyin.

   ![Zaman aralığı parametresinin oluşturulmasını gösteren resim](./media/workbooks-parameters/time-settings.png)

Bu, çalışma kitabının "Pills" stilinde okuma modunda nasıl görüneceğine ilişkin bir şekilde görünür.

   ![Okuma modundaki bir zaman aralığı parametresini gösteren resim](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Parametreye başvurma
### <a name="via-bindings"></a>Bağlamalar aracılığıyla
1. Çalışma kitabına bir sorgu denetimi ekleyin ve bir Application Insights kaynağı seçin.
2. _Zaman aralığı_ açılan ' yı açın ve alttaki parametreler bölümünde `Time Range` seçeneğini belirleyin.
3. Bu, zaman aralığı parametresini grafiğin zaman aralığına bağlar. Örnek sorgunun zaman kapsamı son 24 saattir.
4. Sonuçları görmek için sorguyu çalıştırın

    ![Bağlamalar aracılığıyla başvurulan zaman aralığı parametresini gösteren resim](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>KQL 'de
1. Çalışma kitabına bir sorgu denetimi ekleyin ve bir Application Insights kaynağı seçin.
2. KQL 'de, şu parametreyi kullanarak bir zaman kapsamı filtresi girin: `| where timestamp {TimeRange}`
3. Bu, parametrenin zaman aralığı değeri olan `| where timestamp > ago(1d)`için sorgu değerlendirmesi süresini genişletir.
4. Sonuçları görmek için sorguyu çalıştırın

    ![KQL 'de başvurulan zaman aralığını gösteren resim](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>Metinde 
1. Çalışma kitabına bir metin denetimi ekleyin.
2. Markaşağı içinde `The chosen time range is {TimeRange:label}` girin
3. _Düzenle bitti_ 'yi seçin
4. Metin denetimi metin gösterecektir: _Seçilen zaman aralığı son 24 saat_

## <a name="parameter-options"></a>Parametre seçenekleri
_In metin_ bölümü, değeri yerine parametresinin `label` kullandı. Parametreler, türüne göre çeşitli seçenekler sunar. Örneğin, zaman aralığı seçiciler değer, etiket, sorgu, başlangıç, bitiş ve grede izin verir.

Parametrgenizin seçeneklerini görmek için _parametre Düzenle_ bölmesinin `Previews` bölümünü kullanın:

![Zaman aralığı parametre seçeneklerini gösteren resim](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Sonraki adımlar

* Çok sayıda zengin görselleştirmeler seçeneği hakkında daha fazla [bilgi edinmeye başlayın](workbooks-visualizations.md) .
* Çalışma kitabı kaynaklarınıza erişimi [denetleme](workbooks-access-control.md) ve paylaşma.
