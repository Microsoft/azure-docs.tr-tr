---
title: Uyarı olaylarını yönetme
description: Ağınızda algılanan uyarı olaylarını yönetin.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: 2995ff0d2246929efb534bc21d888bad3a2cf24a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781798"
---
# <a name="manage-alert-events"></a>Uyarı olaylarını yönetme

Uyarı olaylarını yönetmek için aşağıdaki seçenekler kullanılabilir:

 | Eylem | Açıklama |
 |--|--|
 | **Learn** | Algılanan olayı Yetkilendir. Daha fazla bilgi için bkz. [öğrenme ve öğrenme olayları hakkında](#about-learning-and-unlearning-events). |
 | **Kabul et** | Algılanan olay için uyarıyı bir kez gizleyin. Olay tekrar algılanırsa uyarı tekrar tetiklenir. Daha fazla bilgi için bkz. bildirimleri ele [ve geri bildirimleri kaldırma hakkında](#about-acknowledging-and-unacknowledging-events). |
 | **Mikrofon** | Aynı cihazlarla ve karşılaştırılabilir trafikle etkinlikleri sürekli olarak yoksayın. Daha fazla bilgi için bkz. for [hareketlerle ve Unmuting olayları](#about-muting-and-unmuting-events). |
 
Uyarı bilgilerini de dışarı aktarabilirsiniz.
## <a name="about-learning-and-unlearning-events"></a>Öğrenme ve öğrenimi olmayan olaylar hakkında

Öğrenilen ağın sapmalarını gösteren olaylar geçerli ağ değişikliklerini yansıtabilir. Örneklerde, ağa katılmış veya yetkili bir bellenim güncelleştirmesine katılmış yeni bir yetkili cihaz bulunabilir.

**Öğren**' i seçtiğinizde, algılayıcı trafiği, konfigürasyonları veya etkinliği geçerli kabul eder. Bu, olay için uyarıların artık tetiklenmeyeceği anlamına gelir. Ayrıca, algılayıcı risk değerlendirmesi, saldırı vektörü ve diğer raporlar oluşturduğunda olayın hesaplanmayacağı anlamına gelir.

Örneğin, bir ağ tarayıcısının daha önce tanımlamadığını bir cihazdaki adres tarama etkinliğini belirten bir uyarı alırsınız. Bu cihaz, tarama amacına göre ağa eklendiyse, cihazı bir tarama cihazı olarak öğrenme talimatını verebilirsiniz.

:::image type="content" source="media/how-to-work-with-alerts-sensor/detected.png" alt-text="Adres Tarama penceresini algıladı.":::

Öğrenilmiş olaylar açıklanbilinmiş olabilir. Algılayıcı olayları öğrendiğinde, bu olayla ilgili uyarıları geri alacak.

## <a name="about-acknowledging-and-unacknowledging-events"></a>Bildirimleri ele al ve geri bildirimleri kaldırma hakkında

Belirli durumlarda, bir sensör algılanan bir olayı öğrenmek istemiyor ya da seçenek kullanılamayabilir. Bunun yerine, olay azaltma gerektirebilir. Örnek:

- **Ağ yapılandırmasını veya cihazı azaltma**: ağda yeni bir cihazın algılandığını belirten bir uyarı alırsınız. Araştırırken, cihazın yetkisiz bir ağ aygıtı olduğunu fark edersiniz. Cihazın ağla bağlantısını keserek olayı işleyebilirsiniz.
- **Bir algılayıcı yapılandırmasını güncelleştirme**: bir sunucunun aşırı sayıda uzak bağlantı başlattığını belirten bir uyarı alırsınız. Bu uyarı, algılayıcı anomali eşikleri belirli bir sayıdaki oturumun üzerindeki uyarıları bir dakika içinde tetiklemek üzere tanımlandığından tetiklendi. Eşikleri güncelleştirerek olayı işleyebilirsiniz.

Azaltma veya araştırma gerçekleştirdikten sonra, sensör ' ı seçerek uyarıyı gizleyebileceğini **söyleyebilirsiniz.** Olay tekrar algılanırsa, uyarı retriggered olacaktır.

Uyarıyı temizlemek için:

  - **Onayla**' yı seçin.

Uyarıyı yeniden görüntülemek için:

  - Uyarıya erişin ve **bildirim kaldır**' ı seçin.

Daha fazla araştırma gerekliyse uyarıların kabul edilmemiş olduğunu.

## <a name="about-muting-and-unmuting-events"></a>Etkinlikleri kapatma ve geri açma hakkında

Belirli koşullar altında, sensörizin ağınızda belirli bir senaryoyu yoksayacak şekilde söylemek isteyebilirsiniz. Örnek:

  - **Anomali** altyapısı iki cihaz arasındaki bant genişliğindeki bir uyarıyı tetikler, ancak bu cihazlar için ani artış geçerlidir.

  - **Protokol ihlali** altyapısı iki cihaz arasında algılanan bir protokol sapsında bir uyarı tetikler, ancak bu sapma cihazlar arasında geçerlidir.

Bu durumlarda öğrenimi yoktur. Öğrenme gerçekleştirilemiyor ve riskleri ve saldırı vektörlerini hesaplarken uyarıyı bastırmak ve cihazı kaldırmak istiyorsanız, bunun yerine uyarı olayının sesini kapatabilirsiniz.

> [!NOTE] 
> Bir internet cihazının kaynak veya hedef olarak tanımlandığı olayları sessize alamazsınız.

### <a name="what-alert-activity-is-muted"></a>Hangi uyarı etkinliği kapalı?

Bir kapalı senaryo, bir olay için algılanan ağ cihazlarını ve trafiği içerir. Uyarı başlığı, kapalı olan trafiği açıklar.

Kapalı durumda olan cihaz veya cihazlar, uyarıda bir görüntü olarak görüntülenir. İki cihaz gösterilirse, aralarında uyarı verilen belirli trafik kapalı olur.

**Örnek 1**

Bir olay sessize eklendiğinde, birincil (kaynak) ikincil (hedef), satıcı tarafından tanımlanan geçersiz bir işlev kodunu gönderdiği zaman yok sayılır.

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="İkincil cihaz alındı.":::

**Örnek 2**

Bir olay kapalı olduğunda, kaynağın hedefe bakılmaksızın geçersiz içeriğe sahip bir HTTP üstbilgisi göndermesi durumunda bu yok sayılır.

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="Geçersiz HTTP üstbilgisi içeriğinin ekran görüntüsü.":::

**Bir olay sessize alındıktan sonra:**

- Uyarı, geri alınana kadar, **onaylanan** uyarı görünümünde erişilebilir olacaktır.

- Sessiz eylem **olay zaman çizelgesinde** görüntülenir.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="Algılanan ve kapalı olay.":::

- Algılayıcı, risk değerlendirmesi, saldırı vektörü ve diğer raporlar oluştururken cihazları yeniden hesaplar. Örneğin, bir cihazda kötü amaçlı trafik algılanan bir uyarıyı kapatırsanız, bu cihaz risk değerlendirmesi raporunda hesaplanmaz.

**Bir uyarının sesini kapatmak ve açmak için:**

- Uyarı üzerinde **sessiz** simgesini seçin.

**Kapalı uyarıları görüntülemek için:**

1. **Uyarı** ana ekranını **kabul** edilen seçeneğini belirleyin.

2. Sesinin kapalı olup olmadığını görmek için bir uyarının üzerine gelin.  

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
1. Birden çok cihazı içeren her uyarı için uyarı bilgilerini ayrı satırlarda dışarı aktarmak için genişletilmiş uyarıları dışarı aktar ' ı seçin. Genişletilmiş uyarıları dışa aktarma seçildiğinde,. csv dosyası her satırdaki benzersiz öğelerle birlikte uyarı olayının yinelenen bir satırını oluşturur. Bu seçeneğin kullanılması, dışarıya aktarılmış uyarı olaylarını araştırmanızı kolaylaştırır.

## <a name="see-also"></a>Ayrıca bkz.

[Hangi trafiğin izleneceğini denetleme](how-to-control-what-traffic-is-monitored.md)
