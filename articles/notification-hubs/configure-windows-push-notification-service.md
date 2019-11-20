---
title: Azure Notification Hubs Windows anında bildirim hizmeti 'ni yapılandırma | Microsoft Docs
description: Bir Azure Notification Hub 'ı için Windows anında bildirim hizmeti ayarlarını yapılandırmayı öğrenin.
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
ms.openlocfilehash: a7f7734d97cd67c133ff0cedc3ef2376967bcdf4
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212416"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Azure portal bir Bildirim Hub 'ı için Windows anında bildirim hizmeti (WNS) ayarlarını yapılandırma
Bu makalede, Azure portal kullanarak bir Azure Notification Hub 'ı için Windows Notification hizmeti (WNS) ayarlarının nasıl yapılandırılacağı gösterilmektedir.  

## <a name="prerequisites"></a>Önkoşullar
Henüz bir Bildirim Hub 'ı oluşturmadıysanız, şimdi bir tane oluşturun. Daha fazla bilgi için [Azure Portal Azure Notification Hub 'ı oluşturma](create-notification-hub-portal.md)konusuna bakın. 

## <a name="configure-windows-push-notification-service-wns"></a>Windows anında Iletme Bildirimi hizmetini yapılandırma (WNS)

Aşağıdaki yordam bir Bildirim Hub 'ı için Windows anında bildirim hizmeti (WNS) ayarlarını yapılandırma adımları sunar: 

1. Azure portal, **Bildirim Hub** 'ı sayfasında, sol taraftaki menüden **Windows (WNS)** seçeneğini belirleyin.
2. **Paket SID 'si** ve **güvenlik anahtarı**için değerler girin.
3. **Kaydet**’i seçin.

   ![Paket SID ve güvenlik anahtarı kutularını gösteren ekran görüntüsü](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Sonraki adımlar
Azure Notification Hubs ve Windows anında bildirim hizmeti 'ni (WNS) kullanarak Evrensel Windows Platformu uygulamalara bildirim göndermeye yönelik adım adım yönergeler için, bkz. [Azure Notification kullanarak UWP uygulamalarına bildirim gönderme Hub 'lar](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


