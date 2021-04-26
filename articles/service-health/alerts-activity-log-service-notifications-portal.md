---
title: Azure portal kullanarak Azure hizmet bildirimlerinde etkinlik günlüğü uyarıları alma
description: Azure portal kullanarak hizmet durumu bildirimlerine yönelik etkinlik günlüğü uyarılarını ayarlamak için Azure portal nasıl kullanacağınızı öğrenin.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 48126d923cb0baa33058c6fd55e48f31d793fade
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570177"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>Azure portal kullanarak hizmet bildirimlerinde etkinlik günlüğü uyarıları oluşturma
## <a name="overview"></a>Genel Bakış

Bu makalede, Azure portal kullanarak hizmet durumu bildirimlerine yönelik etkinlik günlüğü uyarılarını ayarlamak için Azure portal nasıl kullanılacağı gösterilmektedir.  

Hizmet durumu bildirimleri [Azure etkinlik günlüğünde](../azure-monitor/essentials/platform-logs-overview.md)depolanır. Etkinlik günlüğünde depolanan büyük bilgi hacmi verildiğinde, hizmet durumu bildirimlerinde uyarıları görüntülemeyi ve ayarlamayı kolaylaştıran ayrı bir kullanıcı arabirimi vardır. 

Azure, Azure aboneliğinize hizmet durumu bildirimleri gönderdiğinde bir uyarı alabilirsiniz. Uyarıyı temel alarak yapılandırabilirsiniz:

- Hizmet durumu bildirimi sınıfı (hizmet sorunları, planlı bakım, sağlık danışmanları, Güvenlik Danışma belgeleri).
- Abonelik etkilendi.
- Hizmet (ler) i etkilendi.
- Bu bölgeler etkilendi.

> [!NOTE]
> Hizmet durumu bildirimleri, kaynak sistem durumu olayları için uyarı göndermez.

Ayrıca, uyarının kime gönderileceğini de yapılandırabilirsiniz:

- Mevcut bir eylem grubu seçin.
- Yeni bir eylem grubu oluşturun (gelecekteki uyarılar için kullanılabilir).

Eylem grupları hakkında daha fazla bilgi edinmek için bkz. [Eylem grupları oluşturma ve yönetme](../azure-monitor/alerts/action-groups.md).

Azure Resource Manager şablonları kullanarak hizmet durumu bildirim uyarılarını yapılandırma hakkında daha fazla bilgi için, bkz. [Kaynak Yöneticisi Templates](../azure-monitor/alerts/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>İlk Azure Service Health uyarısını ayarlama hakkında bir video izleyin

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="create-service-health-alert-using-azure-portal"></a>Azure portal kullanarak hizmet durumu uyarısı oluşturma
1. [Portalda](https://portal.azure.com) **hizmet durumu**' nu seçin.

    !["Hizmet durumu" hizmeti](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. **Uyarılar** bölümünde **sistem durumu uyarıları**' nı seçin.

    !["Sistem durumu uyarıları" sekmesi](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. **Hizmet durumu uyarısı Ekle** ' yi seçin ve alanları girin.

    !["Hizmet sistem durumu uyarısı oluştur" komutu](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Uyarılmak istediğiniz **abonelik**, **hizmet** ve **bölgeleri** seçin.

    [!["Etkinlik günlüğü uyarısı Ekle" iletişim kutusu](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png#lightbox)

> [!NOTE]
>Bu abonelik, etkinlik günlüğü uyarısını kaydetmek için kullanılır. Uyarı kaynağı bu aboneliğe dağıtılır ve etkinlik günlüğündeki olayları izler.

5. Uyarı almak istediğiniz **olay türlerini** seçin: *hizmet sorunu*, *Planlı bakım*, *sağlık belgeleri* ve *Güvenlik Danışmanlığı*.

6. Mevcut bir eylem grubunu seçmek veya yeni bir eylem grubu oluşturmak için **Eylem grubunu Seç** ' e tıklayın. Eylem grupları hakkında daha fazla bilgi için [Azure Portal eylem grupları oluşturma ve yönetme](../azure-monitor/alerts/action-groups.md)konusuna bakın.


7. Uyarı **kuralları adı** ve **açıklaması** girerek uyarı ayrıntılarınızı tanımlayın.

8. Uyarının kaydedilmesini istediğiniz **kaynak grubunu** seçin.



Birkaç dakika içinde, uyarı etkin olur ve oluşturma sırasında belirttiğiniz koşullara göre tetiklemeye başlar.

[Mevcut sorun yönetimi sistemleri için Web kancası bildirimlerinin nasıl yapılandırılacağını](service-health-alert-webhook-guide.md)öğrenin. Etkinlik günlüğü uyarıları için Web kancası şeması hakkında bilgi için bkz. [Azure etkinlik günlüğü uyarıları Için Web kancaları](../azure-monitor/alerts/activity-log-alerts-webhook.md).


## <a name="next-steps"></a>Sonraki adımlar
- [Azure hizmet durumu uyarılarını ayarlamaya yönelik en iyi uygulamalar](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)hakkında bilgi edinin.
- [Azure hizmet durumu için mobil anında iletme bildirimleri ayarlamayı](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)öğrenin.
- [Mevcut sorun yönetimi sistemleri için Web kancası bildirimlerinin nasıl yapılandırılacağını](service-health-alert-webhook-guide.md)öğrenin.
- [Hizmet durumu bildirimleri](service-notifications.md)hakkında bilgi edinin.
- [Bildirim hızı sınırlaması](../azure-monitor/alerts/alerts-rate-limiting.md)hakkında bilgi edinin.
- [Etkinlik günlüğü uyarısı Web kancası şemasını](../azure-monitor/alerts/activity-log-alerts-webhook.md)gözden geçirin.
- [Etkinlik günlüğü uyarılarına genel bir bakış](../azure-monitor/alerts/alerts-overview.md)elde edin ve uyarıları alma hakkında bilgi edinin.
- [Eylem grupları](../azure-monitor/alerts/action-groups.md)hakkında daha fazla bilgi edinin.
