---
title: Şirket içi yönetim konsolunda uyarılarla çalışma
description: Ağınızdaki son tehditlere yönelik kurumsal bir görünüm edinmek ve algılayıcı kullanıcılarının bunları nasıl ele aldığı hakkında daha iyi anlamak için şirket içi yönetim konsolunu kullanın.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: 604650f0cb08eac4a3ab1cfd3fdcbf2e7ff0d19e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105032149"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>Şirket içi yönetim konsolunda uyarılarla çalışma 

Yönetim konsolundaki **Uyarılar** penceresinde şunları yapabilirsiniz:

- Uyarı filtreleriyle çalışma

- Uyarı sayaçlarıyla çalışma

- Uyarı bilgilerini görüntüle

- Uyarı olaylarını yönetme

- Uyarı dışlama kuralları oluşturma

- Dış sistemlerden uyarı dışlama kuralları tetikleyin

- Uyarı gruplarıyla olay iş akışını hızlandırma

## <a name="view-alerts-in-the-on-premises-management-console"></a>Şirket içi yönetim konsolunda uyarıları görüntüleme

Şirket içi yönetim konsolu, tüm bağlı sensörlerden gelen uyarıları toplar. Bu, ağınızdaki son tehditlere yönelik kurumsal bir görünüm sağlar ve algılayıcı kullanıcılarının bunları nasıl ele aldığı hakkında daha iyi anlamanıza yardımcı olur.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="Uyarılar penceresinin ekran görüntüsü.":::

### <a name="work-with-alert-filters"></a>Uyarı filtreleriyle çalışma

**Uyarılar** penceresi, şirket içi yönetim konsolunuza bağlı sensörlerden oluşturulan uyarıları görüntüler. Bağlı sensörler için tüm uyarıları görüntüleyebilir veya belirli bir sunucudan gönderilen uyarıları görebilirsiniz:

- Site

- Bölge

- Cihaz

- Algılayıcısı

Tüm uyarıları görüntülemek için **filtreleri temizle** ' yi seçin.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="Filtreleri temizle düğmesini seçerek filtrelerinizi temizleyin.":::

### <a name="work-with-alert-counters"></a>Uyarı sayaçlarıyla çalışma

Uyarı sayaçları, uyarıların önem derecesine ve onaylanan duruma göre dökümünü sağlar.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="Uyarı sayacı, kaç uyarı olduğunu gösterir.":::

Uyarı sayacında aşağıdaki önem düzeyleri görüntülenir:

- **Kritik**: hemen işlenmesi gereken kötü amaçlı bir saldırı olduğunu gösterir.

- **Büyük**: ele almak için önemli bir güvenlik tehdidi gösterir.

- **İkincil**: temel davranıştaki bir güvenlik tehdidi içerebilen bazı sapmayı gösterir.

- **Uyarı**: temel davranıştaki bir güvenlik tehdidi olmadan bazı sapmayı gösterir.

Önem düzeyleri önceden tanımlanmıştır.

Sayacı, onaylanan ve kabul edilmemiş uyarıları temel alarak sayı sağlamak üzere ayarlayabilirsiniz. Hazırlanmamış uyarılar, IoT algılayıcısı için Defender 'da tetiklendi, ancak henüz sensörde işleçlere göre incelendi.

Kabul edilen **uyarıları göster** seçeneği belirlendiğinde, kabul edilen tüm uyarılar **Uyarılar** penceresinde görüntülenir.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="Onaylanan uyarılarınızı görüntüleyin.":::

### <a name="view-alert-information"></a>Uyarı bilgilerini görüntüle

Uyarı aşağıdaki bilgileri sunar:

- Uyarı olayının Özeti.

- Uyarı önem derecesi.

- Algılanan sensörde uyarı bağlantısı.

- Bir uyarı UUID 'SI. UUID, sensörde algılanan uyarı olayından ilgili uyarı KIMLIĞINDEN oluşur ve bir kısa çizgiyle ayırarak benzersiz bir sistem KIMLIĞI numarası gelir.

**Şirket içi yönetim konsolu uyarı UUID 'SI**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="Bir cihaz bağlı ancak yetkilendirilmemiş.":::

**Algılayıcı uyarısı KIMLIĞI**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="Algılayıcı uyarı KIMLIĞI.":::

UUID 'ler ile çalışmak, şirket içi yönetim konsolunda görüntülenen her uyarının aranabilir ve benzersiz bir sayı ile tanımlanabilir olmasını sağlar. Birden çok sensörden oluşturulan uyarılar aynı uyarı KIMLIĞINI oluşturabileceğinden bu gereklidir.

> [!NOTE]
> Varsayılan olarak, UUID 'ler kuralları iletme, aşağıdaki iş ortağı sistemlerinde görüntülenir: Arcgörüş, syslog sunucuları, QRadar, Sentinel ve Nettanık. Kurulum gerekmez.

Uyarı bilgilerini görüntülemek için:

- Uyarı listesinden bir uyarı seçin.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="Uyarı bilgilerinin ekran görüntüsü.":::

Uyarıyı sensörde görüntülemek için:

- Uyarıdan **Açık algılayıcı** ' ı seçin.

Bir bölge eşlemesindeki cihazları görüntülemek için:

- Uyarı olan cihazda ve ona bağlı tüm cihazlara odaklanmış bir cihaz haritasını görüntülemek için **cihazları göster**' i seçin.

## <a name="manage-alert-events"></a>Uyarı olaylarını yönetme

Şirket içi yönetim konsolundan uyarı olaylarının yönetilmesi için çeşitli seçenekler mevcuttur.

- Uyarı olaylarını öğrenin veya kabul edin. Yetkilendirildiği tüm uyarı olaylarını öğrenmek ve şu anda kabul edilmedi olan tüm uyarı olaylarını doğrulamak için **& bildirim alın** ' ı seçin.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="Tümünü öğrenmek için & Onayla ' yı seçin.":::

- Uyarı olaylarını susturma ve aç.

Bilgi edinme ve uyarı olaylarını ele alma hakkında daha fazla bilgi edinmek için bkz. algılayıcı [uyarı olaylarını yönetme](how-to-manage-the-alert-event.md) makalesi.

## <a name="export-alert-information"></a>Uyarı bilgilerini dışarı aktar

Uyarı bilgilerini bir. csv dosyasına aktarın. Filtrelenmiş görünüme göre algılanan tüm uyarıların veya dışarı aktarma bilgilerinin bilgilerini dışarı aktarabilirsiniz. Aşağıdaki bilgiler verilir:

- Kaynak adres
- Hedef adres
- Uyarı başlığı
- Uyarı önem derecesi
- Uyarı iletisi
- Ek bilgiler
- Onaylanan durum
- PCAP kullanılabilirliği

Dışarı aktarmak için:

1. Yan menüden Uyarılar ' ı seçin.
1. Export (Dışarı aktar) öğesini seçin.
1. Birden çok cihazı içeren her uyarı için uyarı bilgilerini ayrı satırlarda dışarı aktarmak için genişletilmiş uyarıları dışarı aktar ' ı seçin. Genişletilmiş uyarıları dışa aktarma seçildiğinde,. csv dosyası her satırdaki benzersiz öğelerle birlikte uyarının yinelenen bir satırını oluşturur. Bu seçeneğin kullanılması, dışarıya aktarılmış uyarı olaylarını araştırmanızı kolaylaştırır.  

## <a name="create-alert-exclusion-rules"></a>Uyarı dışlama kuralları oluşturma

Defender 'ın IoT için, uyarı tetikleyicilerini şunu temel alarak yoksaymasını bildirin:

- Saat dilimleri ve zaman dönemleri

- Cihaz adresi (IP, MAC, alt ağ)

- Uyarı adları

- Belirli bir algılayıcı

Defender 'ın bir uyarı tetikleyecek etkinliği yoksaymasını istediğinizde uyarı dışlama kuralları oluşturun.

Örneğin, belirli bir algılayıcı tarafından izlenen tüm OT cihazlarının iki gün boyunca bakım yordamlarına geçeceğimizi biliyorsanız, bu algılayıcı için Defender 'ın, önceden tanımlanmış dönemde bu algılayıcı tarafından algılanan uyarıları bastırmasını bildiren bir dışlama kuralı tanımlayabilirsiniz.

### <a name="alert-exclusion-logic"></a>Uyarı dışlama mantığı

Uyarı kuralı mantığı `AND` tabanlıdır. Bu, bir uyarının yalnızca tüm kural koşulları karşılandığında tetiklenecek anlamına gelir.

Bir kural koşulu tanımlanmazsa, koşul tüm seçenekleri içerir. Örneğin, kurala bir algılayıcı adı eklemezseniz, bu, tüm sensöre uygulanır.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="Çıkarma kuralı oluşturma görünümünün ekran görüntüsü.":::

Kural özetleri **Dışlama kuralı** penceresinde görünür.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="Dışlama kuralı Özet görünümünün ekran görüntüsü.":::

Dışlama kuralları ile çalışmanın yanı sıra, uyarıları kaldırarak da gizleyebilirsiniz.

### <a name="create-exclusion-rules"></a>Dışlama kuralları oluşturma

Dışlama kuralları oluşturmak için:

1. Şirket içi yönetim konsolunun sol bölmesinden **Uyarı dışlama**' yi seçin.  :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: Açılan pencerenin sağ üst köşesindeki Ekle simgesini seçerek yeni bir çıkarma kuralı tanımlayın. **Dışlama kuralı oluştur** iletişim kutusu açılır.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="Bilgileri buraya doldurarak bir uyarı dışlama oluşturun.":::

2. **Ad** alanına bir kural adı girin. Ad tırnak işareti () içeremez `"` .

3. **Zaman dilimine/döneme göre** bölümünde, belirli bir saat dilimi içinde bir zaman aralığı girin. Bir zaman diliminde belirli bir zaman aralığı için bir dışlama kuralı oluşturulduğunda, ancak diğer saat dilimlerinde aynı anda uygulanması gereken durumlarda bu özelliği kullanın. Örneğin, üç farklı saat dilimlerinde 8:00 ÖÖ ve 10:00 arasında bir dışlama kuralı uygulamanız gerekebilir. Bu durumda, aynı zaman dilimini ve ilgili saat dilimini kullanan üç ayrı dışlama kuralı oluşturun.

4. **Ekle**' yi seçin. Dışlama süresi boyunca, bağlı sensörler üzerinde hiçbir uyarı oluşturulmaz.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="Zaman dönemi görünümünün ekran görüntüsü.":::

5. **Cihaz adresine göre** bölümünde şunu tanımlayın:

  - Dışlamak istediğiniz cihaz IP adresi, MAC adresi veya alt ağ adresi.

  - Dışlanan cihazlar, kaynak ve hedef için trafik yönü.

6. **Ekle**' yi seçin.

7. Uyarı başlığına **göre** bölümünde, uyarı başlığını yazmaya başlayın. Aşağı açılan listeden, bırakılacak uyarı başlığını veya başlıkları seçin.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="Uyarı başlığı görünümünün ekran görüntüsü.":::

8. **Ekle**' yi seçin.

9. **Algılayıcı adına göre** bölümünde algılayıcı adını yazmaya başlayın. Aşağı açılan listeden dışlamak istediğiniz algılayıcı veya sensöri seçin.

10. **Ekle**' yi seçin.

11. **Kaydet**' i seçin. Yeni kural, kurallar listesinde görünür.

Uyarıları kaldırarak veya uyarı dışlama kuralları oluşturarak uyarıları gizleyebilirsiniz. Bu bölümde her iki özellik için de potansiyel kullanım durumları açıklanmaktadır.

- **Dışlama kuralı**. Şu durumlarda bir dışlama kuralı yazın:

  - Olayı veritabanından dışlamak istediğiniz zaman bilirsiniz. Örneğin, belirli bir sensörde algılanan senaryonun ilgisiz uyarıları tetikleyeceğini bilirsiniz. Örneğin, belirli bir sitede Kurumsal PLCs üzerinde bakım işi gerçekleştirerek ve bu site için PLCs ile ilgili uyarıları bastırmak isteyeceksiniz.

  - Belirli bir zaman aralığı için Defender 'ın IoT 'yi (sistem bakım görevleri için) yoksaymasını istiyorsunuz.

  - Belirli bir alt ağdaki olayları yoksaymak istiyorsunuz.

  - Tek bir kuralla birkaç sensörden oluşturulan uyarı olaylarını denetlemek istiyorsunuz.

  - Uyarı dışlamasını olay günlüğünde bir olay olarak izlemek istemezsiniz.

- **Sessiz**. Şu durumlarda bir uyarının sesini kapat:

  - Kapalı olması gereken öğeler planlanmaz. Olayların ne zaman ilgisiz olacağını bilemezsiniz.

  - **Uyarıları** , uyarı penceresinden gizlemek istiyorsunuz, ancak yine de olay günlüğünde izlemek istiyorsunuz.

  - Belirli bir kanaldaki olayları yoksaymak istiyorsunuz.

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>Dış sistemlerden uyarı dışlama kuralları tetikleyin

Dış sistemlerden uyarı dışlama kuralları tetikleyin. Örneğin, Kurumsal anahtar sistemleri veya ağ bakım süreçlerini yöneten sistemlerden dışlama kurallarını yönetin.

Kuralı uygulamak için sensörler, altyapılar, başlangıç saati ve bitiş saatini tanımlayın. Daha fazla bilgi için bkz. [ıOT API algılayıcısı ve Yönetim Konsolu API 'leri Için Defender](references-work-with-defender-for-iot-apis.md).

API kullanarak oluşturduğunuz kurallar, **hariç tutma kuralı** penceresinde Ro olarak görünür.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="Dışlama kuralını Düzenle görünümünün ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki adımlar

[Sensörinizdeki uyarılarla çalışın](how-to-work-with-alerts-on-your-sensor.md).
[IoT altyapı uyarıları Için Defender 'ı](alert-engine-messages.md)inceleyin.