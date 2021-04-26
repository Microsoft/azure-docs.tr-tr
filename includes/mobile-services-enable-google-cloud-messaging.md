---
title: include dosyası
description: include dosyası
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/06/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b32ff0926aa2b797bdb8dfcbb01cf845080a0ce9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93376057"
---
1. [Google Cloud Console](https://console.developers.google.com/cloud-resource-manager)’a gidin, Google hesabı kimlik bilgilerinizle giriş yapın. 
2. Araç çubuğunda **Proje Oluştur**’u seçin. 
   
    ![Yeni proje oluşturma](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
3. **Proje adı** olarak projeniz için bir ad girin ve **Oluştur**’a tıklayın.
4. Araç çubuğundaki **Uyarılar** düğmesini seçip listeden projenizi seçin. Projenizin panosunu görürsünüz. Dilerseniz şu URL'yi kullanarak doğrudan panoya da gidebilirsiniz: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

    ![Uyarılar bölümünden projenizi seçin](./media/mobile-services-enable-google-cloud-messaging/alert-new-project.png)
5. Panonun **Proje bilgileri** kutucuğundaki **Proje numarası**’nı kaydedin. 

    ![Proje Kimliği](./media/mobile-services-enable-google-cloud-messaging/project-number.png)
6. Panodaki **API’ler** kutucuğunda **API’lerin genel bakışına git**’i seçin. 

    ![API’ye genel bakış bağlantısı](./media/mobile-services-enable-google-cloud-messaging/go-to-api-overview.png)
7. **API** sayfasında **API’LERİ VE HİZMETLERİ ETKİNLEŞTİR**’i seçin. 

    ![Enable API’leri ve Hizmetleri Etkinleştir düğmesi](./media/mobile-services-enable-google-cloud-messaging/enable-api-services-button.png)
8. **Google Cloud Messaging**’i bulup seçin. 

    ![Google Cloud Messaging’i bulup seçin](./media/mobile-services-enable-google-cloud-messaging/search-select-gcm.png)
9. Projeye yönelik olarak Google Cloud Messaging’i etkinleştirmek için **ETKİNLEŞTİR**’i seçin.

    ![Google Cloud Messaging'i etkinleştirme](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-button.png)
10. Araç çubuğundan **Kimlik bilgilerini oluşturun**’u seçin. 

    ![Kimlik bilgilerini oluşturun düğmesi](./media/mobile-services-enable-google-cloud-messaging/create-credentials-button.png)
11. **Projenize kimlik bilgileri ekleyin** sayfasında **API anahtarı** bağlantısını seçin. 

    ![Kimlik bilgileri ekle](./media/mobile-services-enable-google-cloud-messaging/api-key-button.png)    
12. **API anahtarı** sayfasında **Oluştur/Kaydet**’i seçin. Aşağıdaki örnekte **IP adresleri** seçeneği belirlenmiş ve izin verilen IP adresleri için **0.0.0.0/0** girilmiştir. API anahtarınızı uygun şekilde kısıtlamanız gerekir. 

    ![API Anahtarı - Oluştur düğmesi](./media/mobile-services-enable-google-cloud-messaging/api-key-create-button.png)
13. **API anahtarını** panoya kopyalayın ve bir yere kaydedin. 

    ![API anahtarını kopyalama](./media/mobile-services-enable-google-cloud-messaging/copy-api-key.png)
   
    Azure’un GCM ile kimlik doğrulaması yapmasını ve uygulamanız adına anında iletme bildirimleri göndermesini etkinleştirmek için bu API anahtarını kullanın. Proje panosuna geri gitmek için şu URL'yi kullanın: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

