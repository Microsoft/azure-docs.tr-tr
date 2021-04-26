---
title: Pagerharcı ile Azure hizmet durumu uyarılarını gönderin
description: Pagerharcı örneğiniz için hizmet sistem durumu olayları hakkında kişiselleştirilmiş bildirimler alın.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 7ae5141f33ebf4ec6e32029625af3a199a04d67b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594529"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Web kancalarını kullanarak Pagerharcı ile Azure hizmet durumu uyarıları gönderme

Bu makalede, bir Web kancası kullanarak Pagerharcı aracılığıyla Azure hizmet durumu bildirimlerini ayarlama konusu gösterilmektedir. [Pagerharcı](https://www.pagerduty.com/)'ın özel Microsoft Azure tümleştirme türünü kullanarak, yeni veya mevcut pagerharcı hizmetlerine hizmet durumu uyarılarını kolayca ekleyebilirsiniz.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Pagerharcı 'de hizmet durumu tümleştirme URL 'SI oluşturma
1.  Kaydolduğunuzdan ve [Pagerharcı](https://www.pagerduty.com/) hesabınızda oturum açtığınızdan emin olun.

1.  Pagerharcı içindeki **Hizmetler** bölümüne gidin.

    ![Pagerharcı 'teki "Hizmetler" bölümü](./media/webhook-alerts/pagerduty-services-section.png)

1.  **Yeni hizmet ekle** ' yi seçin veya ayarlamış olduğunuz mevcut bir hizmeti açın.

1.  **Tümleştirme ayarları**' nda, aşağıdakileri seçin:

    a. **Tümleştirme türü**: Microsoft Azure

    b. **Tümleştirme adı**: \<Name\>

    ![Pagerharcı 'de "tümleştirme ayarları"](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Tüm gerekli alanları doldurun ve **Ekle**' yi seçin.

1.  Bu yeni tümleştirmeyi açın ve **TÜMLEŞTIRME URL**'sini kopyalayıp kaydedin.

    ![Pagerharcı 'de "tümleştirme URL 'SI"](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Azure portal Pagerharcı kullanarak uyarı oluşturma
### <a name="for-a-new-action-group"></a>Yeni bir eylem grubu için:
1. [Azure Portal kullanarak yeni bir eylem grubu için hizmet durumu bildiriminde uyarı oluşturma](./alerts-activity-log-service-notifications-portal.md)bölümünde 1 ile 8 arasındaki adımları izleyin.

1. **Eylem** listesinde tanımlayın:

    a. **Eylem türü:** *Web kancası*

    b. **Ayrıntılar:** Daha önce kaydettiğiniz Pagerharcı **tümleştirme URL 'si** .

    c. **Ad:** Web kancası adı, diğer ad veya tanımlayıcı.

1. Uyarıyı oluşturmak için işiniz bittiğinde **Kaydet** ' i seçin.

### <a name="for-an-existing-action-group"></a>Mevcut bir eylem grubu için:
1. [Azure Portal](https://portal.azure.com/), **İzle**' yi seçin.

1. **Ayarlar** bölümünde **eylem grupları**' nı seçin.

1. Düzenlemek istediğiniz eylem grubunu bulun ve seçin.

1. **Eylem** listesine ekle:

    a. **Eylem türü:** *Web kancası*

    b. **Ayrıntılar:** Daha önce kaydettiğiniz Pagerharcı **tümleştirme URL 'si** .

    c. **Ad:** Web kancası adı, diğer ad veya tanımlayıcı.

1. Eylem grubunu güncelleştirmek için işiniz bittiğinde **Kaydet** ' i seçin.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Web kancası tümleştirmenizi bir HTTP POST isteği aracılığıyla test etme
1. Göndermek istediğiniz hizmet durumu yükünü oluşturun. [Azure etkinlik günlüğü uyarıları Için Web kancalarında](../azure-monitor/alerts/activity-log-alerts-webhook.md)örnek bir hizmet durumu Web kancası yükü bulabilirsiniz.

1. Aşağıdaki gibi bir HTTP POST isteği oluşturun:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. `202 Accepted`"Olay kimliğinizi" içeren bir ileti içeren bir iletisi almalısınız.

1. Tümleştirmenin başarıyla ayarlandığını onaylamak için [Pagerharcı](https://www.pagerduty.com/) 'e gidin.

## <a name="next-steps"></a>Sonraki adımlar
- [Mevcut sorun yönetimi sistemleri için Web kancası bildirimlerinin nasıl yapılandırılacağını](service-health-alert-webhook-guide.md)öğrenin.
- [Etkinlik günlüğü uyarısı Web kancası şemasını](../azure-monitor/alerts/activity-log-alerts-webhook.md)gözden geçirin. 
- [Hizmet durumu bildirimleri](./service-notifications.md)hakkında bilgi edinin.
- [Eylem grupları](../azure-monitor/alerts/action-groups.md)hakkında daha fazla bilgi edinin.
