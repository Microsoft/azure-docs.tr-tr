---
title: Azure Notification Hubs Microsoft anında bildirim hizmeti 'ni yapılandırma | Microsoft Docs
description: Bir Azure Notification Hub 'ı için Microsoft anında bildirim hizmeti ayarlarını yapılandırmayı öğrenin.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 6784331dccd779390ace7dc632d54fe54aedc458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87761000"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Azure portal Microsoft anında Iletme bildirimi hizmeti (MPNS) ayarlarını yapılandırma

Bu makalede, Azure portal kullanarak bir Azure Notification Hub 'ı için Microsoft anında Iletme bildirimi hizmeti (MPNS) ayarlarının nasıl yapılandırılacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Henüz bir Bildirim Hub 'ı oluşturmadıysanız, şimdi bir tane oluşturun. Daha fazla bilgi için [Azure Portal Azure Notification Hub 'ı oluşturma](create-notification-hub-portal.md)konusuna bakın.

## <a name="configure-microsoft-push-notification-service-mpns"></a>Microsoft anında bildirim hizmeti 'ni (MPNS) yapılandırma

Aşağıdaki yordamda, bir Bildirim Hub 'ı için Microsoft anında Iletme bildirimi hizmeti (MPNS) ayarlarının nasıl yapılandırılacağı açıklanmaktadır:

1. Azure portal, **Bildirim Hub** 'ı sayfasında sol menüdeki **Windows Phone (MPNS)** öğesini seçin.
2. Kimliği doğrulanmamış veya kimliği doğrulanmış anında iletme bildirimlerini etkinleştir:

   a. Kimliği doğrulanmamış anında iletme bildirimlerini etkinleştirmek için, **kimliği doğrulanmamış gönderimi etkinleştir**' i seçin  >  .

      ![Kimliği doğrulanmamış anında iletme bildirimlerinin nasıl etkinleştirileceğini gösteren ekran görüntüsü](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Kimliği doğrulanmış anında iletme bildirimlerini etkinleştirmek için:
      * Araç çubuğunda **sertifikayı karşıya yükle**' yi seçin.
      * Dosya simgesini seçin ve ardından sertifika dosyasını seçin.
      * Sertifika için parola belirtin.
      * **Tamam**’ı seçin.
      * **Windows Phone (MPNS)** sayfasında **Kaydet**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Notification Hubs ve Microsoft anında Iletme bildirimi hizmeti 'ni (MPNS) kullanarak Windows Phone cihazlara bildirim göndermeye yönelik adım adım yönergeler için, bkz. [Notification Hubs kullanarak Windows Phone uygulamalara anında iletme bildirimleri gönderme](notification-hubs-windows-mobile-push-notifications-mpns.md).
