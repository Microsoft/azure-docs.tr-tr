---
title: Uyarıları ayarlama
titleSuffix: Azure Digital Twins
description: Bkz. Azure dijital TWINS ölçümleri üzerinde uyarıları etkinleştirme.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 461d7a82854ce62ee99eef1227c13c7a8f0371e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100594877"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Azure dijital TWINS sorunlarını giderme: uyarılar

Azure dijital TWINS, kaynaklarınızın durumu hakkında bilgi veren hizmet örneğiniz için [ölçümleri](troubleshoot-metrics.md) toplar. Bu ölçümleri, Azure Digital TWINS hizmetinin genel sistem durumunu ve ona bağlı kaynakları değerlendirmek için kullanabilirsiniz.

**Uyarılar** , ölçüm verilerinizde önemli koşullar bulunduğunda size önceden bildirim gönderir. Bunlar, sisteminizin kullanıcıları tarafından bildirilmeksizin sorunları tanımlamanızı ve adreslerinizi belirlemenizi sağlar. Uyarılar hakkında daha fazla bilgiyi [*Microsoft Azure içindeki uyarılara genel bakış*](../azure-monitor/alerts/alerts-overview.md)bölümünde bulabilirsiniz.

## <a name="turn-on-alerts"></a>Uyarıları aç

Azure dijital TWINS örneğiniz için uyarıların nasıl etkinleştirileceği aşağıda verilmiştir:

1. [Azure Portal](https://portal.azure.com) oturum açın ve Azure dijital TWINS örneğinize gidin. Bunu, adını Portal arama çubuğuna yazarak bulabilirsiniz. 

2. Menüden **Uyarı** ' ı ve sonra **+ Yeni uyarı kuralı**' nı seçin.

3. Aşağıdaki *Uyarı kuralı oluştur* sayfasında, koşulları, tetiklenecek eylemleri ve uyarı ayrıntılarını belirlemek için istemleri takip edebilirsiniz.     
    * **Kapsam** ayrıntıları, örneğinizin ayrıntıları ile otomatik olarak doldurulmalıdır
    * Uyarı tetikleyicilerini ve yanıtlarını özelleştirmek için **koşul** ve **eylem grubu** ayrıntılarını tanımlayacaksınız
    * **Uyarı kuralı ayrıntıları** bölümünde, kuralınız için bir ad ve isteğe bağlı bir açıklama girin. Uyarının, oluşturulduktan hemen sonra etkin hale gelmesini istiyorsanız, _oluşturulduktan sonra uyarı kuralını etkinleştir_ onay kutusunu seçebilirsiniz.
        - Bu Ayrıca, bir _kaynak grubu_ ve _önem_ düzeyi düzeyini seçtiğiniz yerdir.

4. Uyarı kuralınızı oluşturmak için _Uyarı kuralı oluştur_ düğmesini seçin.

:::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="Kapsam, koşul, eylem grubu ve uyarı kuralı ayrıntılarının bölümleri ile uyarı kuralı oluştur sayfasını gösteren ekran görüntüsü" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

Bu alanları doldurmaya yönelik kılavuzlu bir anlatım için bkz. [*Microsoft Azure uyarılara genel bakış*](../azure-monitor/alerts/alerts-overview.md). Aşağıda, adımların Azure dijital TWINS için nasıl görüneceğine ilişkin bazı örnekler verilmiştir.

### <a name="select-conditions"></a>Koşulları seçin

Azure dijital TWINS için hangi tür uyarı sinyallerinin kullanılabildiğini gösteren *Select Condition* işleminden alıntı aşağıda verilmiştir. Bu sayfada, sinyal türünü filtreleyebilir ve listeden istediğiniz sinyali seçebilirsiniz.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="İlk sinyal mantığını Yapılandır sayfasını gösteren ekran görüntüsü. Ölçüm veya etkinlik günlüklerini seçmek için sinyal türü kutusunda bir vurgu vardır ve bunun altında seçilebilirler.":::

Bir sinyal seçtikten sonra uyarının mantığını yapılandırmanız istenir. Bir boyut üzerinde filtre uygulayabilir, uyarılarınız için bir eşik değeri ayarlayabilir ve koşul için denetim sıklığını ayarlayabilirsiniz. Ortalama yönlendirme hatası oranı ölçüsünün %5 ' inin üzerinde ne zaman geçgireceğini bildiren bir uyarı ayarlamaya bir örnek aşağıda verilmiştir.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="İkinci sinyal mantığını Yapılandır sayfasını gösteren ekran görüntüsü. Sayfa, ölçüm geçmişini gösterir, Event Grid işlemler gibi bir boyut üzerinde filtrelemeye yönelik bir alana sahiptir ve ' ortalama, 5 ' ten büyük olan uyarı mantığını tanımlamaya yönelik bir bölümdür":::

### <a name="verify-success"></a>Başarıyı doğrula

Uyarıları ayarladıktan sonra, örneğiniz için *Uyarılar* sayfasında geri görünür.
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="Uyarı sayfasını ve eklenecek düğmeyi gösteren ekran görüntüsü. Yapılandırılmış bir uyarı var" lightbox="media/troubleshoot-alerts/alerts-post.png":::

## <a name="next-steps"></a>Sonraki adımlar

* Azure Izleyici ile uyarılar hakkında daha fazla bilgi için bkz. [*Microsoft Azure uyarılara genel bakış*](../azure-monitor/alerts/alerts-overview.md).
* Azure dijital TWINS ölçümleri hakkında daha fazla bilgi için bkz. [*sorun giderme: Azure izleyici ile ölçümleri görüntüleme*](troubleshoot-metrics.md).
* Ölçümleriniz için tanılama günlüğünü etkinleştirme hakkında bilgi için bkz. [*sorun giderme: tanılamayı ayarlama*](troubleshoot-diagnostics.md).
