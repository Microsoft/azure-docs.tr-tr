---
title: include dosyası
description: include dosyası
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ccb5e40738680181e7339b8652d029597c7d0bd4
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935153"
---
1. [Firebase konsolunda](https://firebase.google.com/console/) oturum açın. Henüz bir tane yoksa yeni bir Firebase projesi oluşturun.
2. Projenizi oluşturduktan sonra **Firebase’i Android uygulamanıza ekleyin**’i seçin. 

    ![Firebase’i Android uygulamanıza ekleyin](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. **Android uygulamanıza Firebase ekleme** sayfasında, aşağıdaki adımları uygulayın: 
    1. **Android paket adı**için uygulamanızın Build. Gradle dosyasına **ApplicationId** değerini kopyalayın. Bu örnekte, `com.fabrikam.fcmtutorial1app`. 

        ![Paket adını belirtin](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. **Uygulamayı kaydet**' i seçin. 
4. **Google-Services. json dosyasını indir**' i seçin, dosyayı projenizin **uygulama** klasörüne kaydedin ve sonra **İleri**' yi seçin. 

    ![Google-Services. JSON indirin](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Android Studio projenizde aşağıdaki **yapılandırma değişikliklerini** yapın. 
    1.  Proje düzeyi Build. Gradle dosyanızda (&lt;Project&gt;/Build.Gradle), **Bağımlılıklar** bölümüne aşağıdaki ifadeyi ekleyin. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. Uygulama&lt;düzeyi Build. Gradle dosyanızda (Project/&gt;&lt;App-Module&gt;/Build.Gradle), aşağıdaki deyimlerini **Bağımlılıklar** bölümüne ekleyin. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. Bağımlılıklar bölümünden sonra uygulama düzeyi Build. Gradle dosyasının sonuna aşağıdaki satırı ekleyin. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Araç çubuğunda **Şimdi Eşitle** ' yi seçin. 
 
        ![Build. Gradle yapılandırma değişiklikleri](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. **İleri**’yi seçin. 
7. **Bu adımı atla**' yı seçin. 

    ![Son adımı atlayın](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Firebase konsolunda projenizin dişli simgesini seçin. Sonra, **Proje Ayarları**’nı seçin.

    ![Proje Ayarları’nı seçin](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Google-Services. json dosyasını Android Studio projenizin **uygulama** klasörüne indirmediyseniz bu sayfada yapabilirsiniz. 
5. En üstteki **bulut mesajlaşma** sekmesine geçin. 
6. Daha sonra kullanmak üzere **sunucu anahtarını** kopyalayın ve kaydedin. Bu değeri, hub 'ınızı yapılandırmak için kullanırsınız.
