---
title: Azure Notification Hubs Microsoft anında bildirim hizmeti 'ni yapılandırma | Microsoft Docs
description: Bir Azure Notification Hub 'ı için Microsoft anında bildirim hizmeti ayarlarını yapılandırmayı öğrenin.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 0d7bf5410e05bb74a215fb7a95c704673b764b93
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212483"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>Azure portal bir Bildirim Hub 'ı için Microsoft anında Iletme bildirimi hizmeti (MPNS) ayarlarını yapılandırma
Bu makalede, Azure portal kullanarak bir Azure Notification Hub 'ı için Microsoft anında Iletme bildirimi hizmeti (MPNS) ayarlarının nasıl yapılandırılacağı gösterilmektedir. 

## <a name="prerequisites"></a>Önkoşullar
Henüz bir Bildirim Hub 'ı oluşturmadıysanız, şimdi bir tane oluşturun. Daha fazla bilgi için [Azure Portal Azure Notification Hub 'ı oluşturma](create-notification-hub-portal.md)konusuna bakın. 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Microsoft anında bildirim hizmeti 'ni (MPNS) yapılandırma

Aşağıdaki yordam bir Bildirim Hub 'ı için Microsoft anında Iletme bildirimi hizmeti (MPNS) ayarlarını yapılandırma adımları sunar: 

1. Azure portal, **Bildirim Hub** 'ı sayfasında sol menüdeki **Windows Phone (MPNS)** öğesini seçin.
1. Kimliği doğrulanmamış veya kimliği doğrulanmış anında iletme bildirimlerini etkinleştir:

   a. Kimliği doğrulanmamış**anında iletme bildirimlerini**etkinleştirmek için, **kimliği doğrulanmamış gönderimi** > etkinleştir ' i seçin.

      ![Kimliği doğrulanmamış anında iletme bildirimlerinin nasıl etkinleştirileceğini gösteren ekran görüntüsü](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Kimliği doğrulanmış anında iletme bildirimlerini etkinleştirmek için:
      * Araç çubuğunda **sertifikayı karşıya yükle**' yi seçin.
      * Dosya simgesini seçin ve ardından sertifika dosyasını seçin.
      * Sertifika için parola belirtin.
      * **Tamam**’ı seçin.
      * **Windows Phone (MPNS)** sayfasında **Kaydet**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar
Azure Notification Hubs ve Microsoft anında Iletme bildirimi hizmeti 'ni (MPNS) kullanarak Windows Phone cihazlara bildirim iletme hakkında adım adım yönergeler için, bkz. [Notification Hubs kullanarak Windows Phone uygulamalara anında iletme bildirimleri gönderme](notification-hubs-windows-mobile-push-notifications-mpns.md).

