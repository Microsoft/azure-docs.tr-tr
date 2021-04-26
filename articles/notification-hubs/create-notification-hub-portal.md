---
title: Azure portal kullanarak bir Azure Notification Hub 'ı oluşturun | Microsoft Docs
description: Bu öğreticide, Azure portal kullanarak bir Azure Notification Hub 'ı oluşturmayı öğreneceksiniz.
services: notification-hubs
author: sethmanheim
ms.author: sethm
manager: femila
ms.reviewer: thsomasu
ms.date: 08/04/2020
ms.lastreviewed: 02/14/2019
ms.topic: quickstart
ms.service: notification-hubs
ms.workload: mobile
ms.custom:
- mode-portal
ms.openlocfilehash: 4381c9259788982832a02ecbe2c87c6f6e723772
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533596"
---
# <a name="quickstart-create-an-azure-notification-hub-in-the-azure-portal"></a>Hızlı başlangıç: Azure portal bir Azure Notification Hub 'ı oluşturma

Azure Notification Hubs, herhangi bir arka uçtan (bulut ya da şirket içi) herhangi bir platforma (iOS, Android, Windows, Kindle, Baidu vb.) bildirim göndermenize olanak tanıyan, kullanımı kolay ve ölçeği artırılmış bir gönderme altyapısı sağlar. Hizmet hakkında daha fazla bilgi için bkz. [Azure Notification Hubs nedir?](notification-hubs-push-notification-overview.md).

Bu hızlı başlangıçta Azure portal bir Bildirim Hub 'ı oluşturacaksınız. İlk bölüm, bu ad alanında bir Notification Hubs ad alanı ve hub oluşturma adımları sağlar. İkinci bölüm, mevcut bir Notification Hubs ad alanında bir Bildirim Hub 'ı oluşturma adımları sağlar.

## <a name="create-a-namespace-and-a-notification-hub"></a>Ad alanı ve Bildirim Hub 'ı oluşturma

Bu bölümde ad alanında bir ad alanı ve bir hub oluşturursunuz.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Mevcut bir ad alanında Bildirim Hub 'ı oluşturma

Bu bölümde, mevcut bir ad alanında bir Bildirim Hub 'ı oluşturursunuz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol menüdeki **tüm hizmetler** ' i seçin, **Bildirim Hub**'ı ara ' yı seçin, Bildirim Hub 'ı ad alanları ' nın yanındaki **yıldız** () ' i seçerek `*` Sol menüdeki **Sık Kullanılanlar** bölümüne ekleyin.  **Bildirim Hub 'ı ad alanları**' nı seçin.

      ![Azure portal-Bildirim Hub 'ı ad alanlarını seçin](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. **Bildirim Hub 'ı ad alanları** sayfasında, listeden ad alanınızı seçin.

      ![Listeden ad alanınızı seçin](./media/create-notification-hub-portal/select-namespace.png)
4. **Bildirim Hub 'ı ad alanı** sayfasında, araç çubuğunda **hub Ekle** ' yi seçin.

      ![Bildirim Hub 'ı ad alanları-hub Ekle düğmesi](./media/create-notification-hub-portal/add-hub-button.png)
5. **Yeni Bildirim Hub** 'ı sayfasında, Bildirim Hub 'ı için bir ad girin ve **Tamam**' ı seçin.

      ![Yeni Bildirim Hub 'ı sayfası-> hub 'ınız için bir ad girin](./media/create-notification-hub-portal/new-notification-hub-page.png)
6. Yeni hub 'ın dağıtımının durumunu görmek için üst kısımdaki **bildirimleri** (zil simgesi) seçin. Bildirim penceresini kapatmak için sağ köşedeki **X** seçeneğini belirleyin.

      ![Dağıtım bildirimi](./media/create-notification-hub-portal/deployment-notification.png)
7. Yeni hub 'ınızı listede görmek için **Bildirim Hub 'ı ad alanları** Web sayfasını yenileyin.

      ![Bildirim Hub 'ı ad alanları Web sayfasını listede yeni hub ile gösteren ekran görüntüsü.](./media/create-notification-hub-portal/new-hub-in-list.png)
8. Bildirim Hub 'ınızı giriş sayfasını görmek için **Bildirim Hub** 'ınızı seçin.

      ![Bildirim Hub 'ınız için giriş sayfasını gösteren ekran görüntüsü.](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Bildirim Hub 'ı oluşturdunuz. Hub 'ı platform bildirim sistemi (PNS) ayarlarıyla yapılandırma hakkında bilgi edinmek için bkz. [PNS ayarları ile bir Bildirim Hub 'ı yapılandırma](configure-notification-hub-portal-pns-settings.md).
