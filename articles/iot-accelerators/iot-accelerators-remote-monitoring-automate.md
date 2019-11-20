---
title: Cihaz sorunlarını algılamak için uzaktan Izleme çözümünü kullanma-Azure | Microsoft Docs
description: Bu öğreticide Uzaktan İzleme çözümündeki eşik değer tabanlı cihaz sorunlarını otomatik olarak algılama amacıyla kuralların ve eylemlerin nasıl kullanılacağı gösterilmektedir.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 676f1133a516174478a456a97bc467e7770fe6e7
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888884"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>Öğretici: İzleme çözümünüze bağlı cihazlarla sorunları algılama

Bu öğreticide bağlı IoT cihazlarınızla ilgili sorunları algılamak için Uzaktan İzleme çözümü hızlandırıcısını yapılandıracaksınız. Cihazlarınızla ilgili sorunları algılamak için çözüm panosunda uyarı oluşturan kurallar ekleyeceksiniz.

Öğreticide kurallar ve uyarılar için sanal bir soğutucu cihazı kullanılmaktadır. Soğutucu, Contoso adlı bir kuruluş tarafından yönetilmektedir ve Uzaktan İzleme çözümü hızlandırıcısına bağlıdır. Contoso, soğutucu içindeki basınç 298 PSI değerinin üzerine çıktığında kritik uyarı oluşturan bir kurala sahiptir. Contoso'da çalışan bir operatör olarak ilk basınç artışlarına bakarak sensörleri sorunlu olabilecek soğutucu cihazlarını belirlemek istiyorsunuz. Bu cihazları tanımlamak için soğutucu içindeki basınç 150 PSI seviyesinin üzerine çıktığında uyarı oluşturan bir kural ekleyeceksiniz.

Aynı zamanda son beş dakika içinde cihaz içindeki ortalama nem oranı %80'den fazla olan ve cihaz sıcaklığı 75 Fahrenhayt seviyesinin üzerine çıkan soğutucular için kritik uyarı oluşturulması yönünde bir istek aldınız.

Bu öğreticide şunları yaptınız:

>[!div class="checklist"]
> * Çözümünüzdeki kuralları görüntüleme
> * Kural oluşturma
> * Birden fazla koşula sahip kural oluşturma
> * Var olan kuralı düzenleme
> * Kuralları açma ve kapatma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="review-the-existing-rules"></a>Var olan kuralları gözden geçirme

Çözüm hızlandırıcısının **Rules** (Kurallar) sayfasında geçerli kuralların listesi görüntülenir:

[![Rules (Kurallar) Sayfası](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

Yalnızca soğutucular için geçerli olan kuralları görüntülemek için bir filtre uygulayın. Listedeki kurallardan birini seçerek hakkında daha fazla bilgi görüntüleyebilir ve düzenleyebilirsiniz:

[![Kural ayrıntılarını görüntüleme](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>Kural oluşturma

Bir soğutucu cihazın basıncı 150 PSI seviyesinin üzerine çıktığında uyarı oluşturan bir kural oluşturmak için **New rule** (Yeni kural) öğesine tıklayın. Kuralı oluşturmak için aşağıdaki değerleri kullanın:

| Ayar          | Değer                                 |
| ---------------- | ------------------------------------- |
| Kural adı        | Soğutucu uyarısı                       |
| Açıklama      | Soğutucu basıncı 150 PSI seviyesinin üzerine çıktı |
| Cihaz grubu     | **Soğutucular** cihaz grubu             |
| Hesaplama      | Anında                               |
| Koşul 1 Alanı| basınç                              |
| Koşul 1 işleci | Büyüktür                      |
| Koşul 1 değeri    | 150                               |
| Önem derecesi  | Uyarı                               |

[![Uyarı kuralı oluşturma](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

Yeni kuralı kaydetmek için **Apply** (Uygula) öğesine tıklayın.

Kuralın tetiklendiğini **Rules** (Kurallar) veya **Dashboard** (Pano) sayfasında görebilirsiniz:

[![Uyarı kuralı tetiklendi](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-an-advanced-rule"></a>Gelişmiş kural oluşturma

Son beş dakika içinde cihaz içindeki ortalama nem oranı %80'den fazla olan ve cihaz sıcaklığı 75 Fahrenhayt seviyesinin üzerine çıkan soğutucular için kritik uyarı oluşturan birden fazla koşula sahip bir kural oluşturmak için **New rule** (Yeni kural) öğesine tıklayın. Kuralı oluşturmak için aşağıdaki değerleri kullanın:

| Ayar          | Değer                                 |
| ---------------- | ------------------------------------- |
| Kural adı        | Soğutucuda kritik nem ve sıcaklık    |
| Açıklama      | Nem ve sıcaklık kritik düzeyde |
| Cihaz grubu     | **Soğutucular** cihaz grubu             |
| Hesaplama      | Ortalama                               |
| Süre      | 5                                     |
| Koşul 1 Alanı| Nem oranı                              |
| Koşul 1 işleci | Büyüktür                      |
| Koşul 1 değeri    | 80                                |
| Önem derecesi  | Kritik                              |

[![Birden fazla koşula sahip kural oluşturma birinci bölüm](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

İkinci koşulu eklemek için "+ Add condition" (Koşul ekle) öğesine tıklayın. Yeni koşul için aşağıdaki değerleri kullanın:

| Ayar          | Değer                                 |
| ---------------- | ------------------------------------- |
| Koşul 2 Alanı| sıcaklık                           |
| Koşul 2 işleci | Büyüktür                      |
| Koşul 2 değeri    | 75                                |

[![Birden fazla koşula sahip kural oluşturma ikinci bölüm](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

Yeni kuralı kaydetmek için **Apply** (Uygula) öğesine tıklayın.

Kuralın tetiklendiğini **Rules** (Kurallar) veya **Dashboard** (Pano) sayfasında görebilirsiniz:

[![Birden fazla koşula sahip kural tetiklendi](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>Var olan kuralı düzenleme

Var olan bir kuralı değiştirmek için kural listesinden seçip **Edit** (Düzenle) öğesine tıklayın:

[![Kuralı düzenleme](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>Kuralı devre dışı bırakma

Bir kuralı geçici olarak kapatmak için kural listesinden devre dışı bırakabilirsiniz. Devre dışı bırakılacak kuralı ve ardından **Devre Dışı Bırak**'ı seçin. Kuralın listedeki **Status** (Durum) alanı değişir ve kuralın devre dışı olduğunu belirtir. Aynı yordamı kullanarak önceden devre dışı bıraktığınız bir kuralı yeniden etkinleştirebilirsiniz.

[![Kuralı devre dışı bırakma](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

Listeden birden fazla kural seçerek aynı anda etkinleştirebilir ve devre dışı bırakabilirsiniz.

## <a name="delete-a-rule"></a>Kuralı silme

Bir kuralı kalıcı olarak silmek istiyorsanız kural listesinden silebilirsiniz. Silinecek kuralı ve ardından **Sil**'i seçin.

[![Kuralı silme](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-expanded.png#lightbox)

Kuralı silmek istediğinizi onayladıktan sonra **Bakım** sayfasından bu kural ile ilişkili tüm uyarıları silebilirsiniz.

[![Kuralı silme](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-expanded.png#lightbox)

Tek seferde yalnızca bir kuralı silebilirsiniz.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Uzaktan İzleme çözümü hızlandırıcısındaki **Rules** (Kurallar) Sayfasını kullanarak çözümdeki uyarıları tetikleyen kurallar oluşturmayı ve yönetmeyi gösterilmiştir. Çözüm hızlandırıcısını bağlı cihazlarınızı yönetme ve yapılandırma amacıyla kullanmayı öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [İzleme çözümünüze bağlı cihazları yapılandırma ve yönetme](iot-accelerators-remote-monitoring-manage.md)