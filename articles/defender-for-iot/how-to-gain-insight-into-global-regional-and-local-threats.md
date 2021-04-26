---
title: Küresel, bölgesel ve yerel tehditlere yönelik içgörü elde etme
description: Şirket içi yönetim konsolundaki site haritasını kullanarak genel, bölgesel ve yerel tehditlerle ilgili Öngörüler elde edin.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: db3b9fbca9acd6c4ce1cfe137a4024f66d8a6292
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784127"
---
# <a name="gain-insight-into-global-regional-and-local-threats"></a>Küresel, bölgesel ve yerel tehditlere yönelik içgörü elde etme

Şirket içi yönetim konsolundaki site haritası, ağınızı iş topolojinizi yansıtan coğrafi ve mantıksal kesimlere ayırarak tam güvenlik kapsamı elde etmenize yardımcı olur:

- **Coğrafi tesis düzeyi**: bir site, haritada sunulan coğrafi bir konuma göre gruplanmış sayıda cihazı yansıtır. IoT için Azure Defender, varsayılan olarak size bir dünya haritası sağlar. Haritayı kurumsal veya iş yapınızı yansıtacak şekilde güncelleştirebilirsiniz. Örneğin, belirli bir ülke, şehir veya endüstriyel kampüs genelinde siteleri yansıtan bir harita kullanın. Haritada site rengi değiştiğinde, SOC ekibine, Tesis içindeki kritik sistem durumu hakkında bir gösterge sağlar.

  Harita etkileşimlidir ve her bir sitenin açılmasına ve bu sitenin bilgilerine karşı delmesine izin verilir.

- **Genel mantıksal katman**: iş birimi, kuruluşunuzu belirli sektörlere göre mantıksal kesimlere bölmek için bir yoldur. Bunu yaptığınızda, iş topolojiniz haritada yansıtılır.

  Örneğin, Cam fabrika, plastik fabrika ve otomobil fabrikası içeren küresel bir şirket, üç farklı iş birimi olarak yönetilebilir. Tora 'da bulunan bir fiziksel site, üç farklı cam üretim satırı, plastik üretim hattı ve kamyon motoru üretim hattını içerir. Bu nedenle, bu sitenin üç iş biriminin temsilcileri vardır.

- **Coğrafi bölge düzeyi**: küresel bir kuruluşu coğrafi bölgelere bölmek için bölgeler oluşturun. Örneğin, açıklandığımız şirket Kuzey Amerika, Batı Avrupa ve Doğu Avrupa bölgelerini kullanabilir. Kuzey Amerika, üç iş biriminden oluşan fabrikalar içerir. Batı Avrupa, otomobil fabrikaları ve cam fabrikaları ve Doğu Avrupa 'da yalnızca plastik fabrikalar vardır.

- **Yerel mantıksal kesim düzeyi**: bölge, bir site içindeki bir işlevsel alanı veya üretim satırını tanımlayan bir mantıksal kesimdir. Bölgelerle çalışma, bölge tanımıyla ilgili güvenlik ilkelerinin zorlanmasını sağlar. Örneğin, beş üretim satırı içeren bir site beş bölgeye ayrılabilir.

- **Yerel görünüm düzeyi**: tek bir algılayıcı yüklemesinin yerel görünümü, bağlı cihazların işletimsel ve güvenlik durumuyla ilgili öngörüler sağlar.

## <a name="work-with-site-map-views"></a>Site Haritası görünümleriyle çalışma

Şirket içi yönetim konsolu, endüstriyel ağınızın bağlama ile ilgili bir haritada genel bir görünümünü sağlar. Genel harita görünümü, kuruluşunuzun genel haritasını her sitenin coğrafi konumuyla birlikte sunar.

:::image type="content" source="media/how-to-work-with-maps/enterprise.png" alt-text="Kurumsal harita görünümünün ekran görüntüsü.":::

### <a name="color-coded-map-views"></a>Renk kodlu harita görünümleri

**Yeşil**: güvenlik olaylarının sayısı, IoT Için Defender 'ın sisteminiz için tanımladığı eşiğin altında. Eylem gerekmiyor.

**Sarı**: güvenlik olaylarının sayısı, IoT Için Defender 'ın sisteminiz için tanımladığı eşiğe eşittir. Olayları araştırmak için göz önünde bulundurun.  

**Kırmızı**: güvenlik olaylarının sayısı, IoT Için Defender 'ın sisteminiz için tanımladığı eşiğin ötesinde olur. Anında işlem yapın.

### <a name="risk-level-map-views"></a>Risk düzeyi harita görünümleri

**Risk değerlendirmesi**: risk değerlendirmesi görünümü, site riskleri hakkındaki bilgileri görüntüler. Risk bilgileri, güvenlik geliştirmelerini planlamak için azaltma önceliklendirmenize ve bir yol haritası oluşturmanıza yardımcı olur.

**Olay yanıtı**: Kurumsal genelindeki her bir sitede, kabul edilmemiş tüm uyarıların merkezi bir görünümünü alın. Belirli bir sitede algılanan uyarıların detayına gidebilir ve bunları yönetebilirsiniz.

:::image type="content" source="media/how-to-work-with-maps/incident-responses.png" alt-text="Olay yanıtıyla kurumsal harita görünümünün ekran görüntüsü.":::

**Kötü amaçlı etkinlik**: kötü amaçlı yazılım algılanırsa, site kırmızı renkte görünür. Bu, hemen işlem yapmanız gerektiğini gösterir.

:::image type="content" source="media/how-to-work-with-maps/malicious-activity.png" alt-text="Kötü amaçlı etkinlikle kurumsal harita görünümünün ekran görüntüsü.":::

**Işletimsel uyarılar**: ot sistemleri için bu harita görünümü, PLC duraklar, bellenim karşıya yükleme ve program yükleme gibi işlem olayları hakkında daha iyi bir anlayışı sağlar.

:::image type="content" source="media/how-to-work-with-maps/operational-alerts.png" alt-text="Işlemsel uyarılarla kurumsal harita görünümünün ekran görüntüsü.":::

Bir harita görünümü seçmek için:

1. Haritadan **varsayılan görünüm** ' ü seçin.
2. Bir görünüm seçin.

:::image type="content" source="media/how-to-work-with-maps/map-views.png" alt-text="Site Haritası varsayılan görünümünün ekran görüntüsü.":::

## <a name="update-the-site-map-image"></a>Site Haritası görüntüsünü güncelleştirme

IoT için Defender varsayılan bir dünya haritası sağlar. Bunu kuruluşunuzu yansıtacak şekilde değiştirebilirsiniz: Örneğin, bir ülke haritası veya şehir haritası. 

Haritayı değiştirmek için:

1. Sol bölmede **sistem ayarları**' nı seçin.

2. **Site haritasını Değiştir** ' i seçin ve varsayılan eşlemeyi değiştirmek için grafik dosyasını karşıya yükleyin.

## <a name="next-step"></a>Sonraki adım

[Uyarıları görüntüleme](how-to-view-alerts.md)
