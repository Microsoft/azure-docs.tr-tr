---
title: include dosyası
description: include dosyası
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/01/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45bdd569741dc13181bcaf9e8587a02b3d02c621
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728842"
---
1. [Firebase konsolunda](https://firebase.google.com/console/) oturum açın. Henüz bir tane yoksa yeni bir Firebase projesi oluşturun.
2. Projenizi oluşturduktan sonra **Firebase’i Android uygulamanıza ekleyin**’i seçin. 

    ![Firebase’i Android uygulamanıza ekleyin](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. **Android uygulamanıza Firebase ekleme** sayfasında, aşağıdaki adımları uygulayın: 
    1. **Android paket adı**için paketiniz için bir ad girin. Örneğin: `tutorials.tutoria1.xamarinfcmapp` 

        ![Paket adını belirtin](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. **Uygulamayı kaydet**' i seçin.  
    1. **Google-Services. JSON indir**öğesini seçin. Sonra dosyayı projenizin klasörüne kaydedin ve **İleri**' yi seçin. Henüz Visual Studio projesini oluşturmadıysanız, projeyi oluşturduktan sonra bu adımı yapabilirsiniz. 

        ![Google-Services. JSON indirin](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. **İleri**’yi seçin. 
    7. **Bu adımı atla**' yı seçin. 

        ![Son adımı atlayın](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Firebase konsolunda projenizin dişli simgesini seçin. Sonra, **Proje Ayarları**’nı seçin.

    ![Proje Ayarları’nı seçin](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. **Google-Services. JSON** dosyasını indirmediyseniz bu sayfada indirebilirsiniz. 

    ![Genel sekmesinden Google-Services. json ' i indirin](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. En üstteki **bulut mesajlaşma** sekmesine geçin. Daha sonra kullanmak üzere **sunucu anahtarını** kopyalayın ve kaydedin. Bu değeri, Bildirim Hub 'ınızı yapılandırmak için kullanırsınız.

    ![Sunucu anahtarını Kopyala](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
