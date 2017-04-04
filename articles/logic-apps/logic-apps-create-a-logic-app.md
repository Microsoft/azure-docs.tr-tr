---
title: "İlk Azure Mantıksal Uygulamanız ile iş akışları oluşturma | Microsoft Docs"
description: "İlk Mantıksal Uygulamanız ile uygulamaları ve SaaS hizmetlerini bağlamaya başlama"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 221f1b9f0985bbaf0553f6ca01f0f048b9976315
ms.lasthandoff: 03/28/2017


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>SaaS hizmetlerine bağlanan yeni bir mantıksal uygulama oluşturma
Bu konuda [Azure Logic Apps](logic-apps-what-are-logic-apps.md)’i kullanmaya birkaç dakika içinde nasıl başlayabileceğiniz gösterilmektedir. E-postanıza ilgi çekici tweet’ler göndermenize imkan tanıyan basit bir iş akışı gösterilecektir.

Bu senaryoyu kullanmak için aşağıdakiler gereklidir:

* Bir Azure aboneliği
* Bir Twitter hesabı
* Bir Outlook.com veya Office 365 Outlook hesabı

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Tweet’leri e-posta ile göndermek için yeni bir mantıksal uygulama oluşturma

1. [Azure Portal](https://portal.azure.com) oturum açın. 

2. Sol menüden **Yeni** > **Kurumsal Tümleştirme** > **Mantıksal Uygulama**’yı seçin.

    Ayrıca **Yeni**’yi seçebilir, ardından arama kutusuna `logic app` yazıp Enter tuşuna basabilirsiniz. **Mantıksal Uygulama** > **Oluştur**’u seçin.

3. Mantıksal uygulamanız için bir ad girin, Azure aboneliğinizi seçin, bir Azure kaynak grubu oluşturun veya seçin, bir konum seçin ve **Oluştur**’u seçin.

    **Panoya Sabitle**’yi seçerseniz, mantıksal uygulama dağıtımdan sonra otomatik olarak açılır.

4. Mantıksal uygulamanızı ilk kez açtığınızda başlamak için bir şablondan seçim yapabilirsiniz.
Bunu sıfırdan oluşturmak için şimdilik **Boş Mantıksal Uygulama**’ya tıklayın. 

5. Tetikleyici oluşturmanız için gereken ilk öğedir. Mantıksal uygulamanızı başlatan olay budur. Arama kutusunda **twitter** araması yapın ve **Yeni bir tweet gönderildiğinde** öğesini seçin. Twitter hesabınızın kullanıcı adı ve parolasıyla oturum açın.

6. Şimdi mantıksal uygulamanızı tetikleyecek bir arama terimi yazın.

   ![Twitter araması](media/logic-apps-create-a-logic-app/twittersearch.png)

    **Sıklık** ve **Aralık**, mantıksal uygulamanızın tweet’leri ne sıklıkla denetleyeceğini belirler ve bu süre boyunca tüm tweet’leri döndürür.

7. **Yeni adım**’ı ve ardından **Eylem ekle** veya **Koşul ekle** öğesini seçin.

    **Eylem Ekle** öğesini seçtiğinizde bir eylem seçmek üzere [kullanılabilir bağlayıcılar](../connectors/apis-list.md) içinde arama yapabilirsiniz. 

8. Arama kutusunda **outlook** araması yapın ve Outlook hesabınızdan belirtilen herhangi bir e-posta adresine e-posta göndermek için **E-posta gönder**’i seçin.

   ![Eylemler](media/logic-apps-create-a-logic-app/actions.png)

9. Bundan sonra istediğiniz e-posta için parametreleri doldurmanız gerekir:

   ![Parametreler](media/logic-apps-create-a-logic-app/parameters.png)

10. Son olarak, mantıksal uygulamanızı canlı hale getirmek için **Kaydet**’i seçebilirsiniz.

## <a name="manage-your-logic-app-after-creation"></a>Oluşturduktan sonra mantıksal uygulamanızı yönetme

Mantıksal uygulamanız artık çalışır durumdadır. Girilen arama terimini içeren tweet’ler düzenli olarak denetlenir. Eşleşen bir tweet bulunduğunda size bir e-posta gönderilir. Son olarak, uygulamayı devre dışı bırakmayı veya nasıl çalıştığını görürsünüz.

1. [Azure Portal](https://portal.azure.com) gidin.

2. Sol menüdeki **Diğer hizmetler**’e tıklayın. **Kurumsal Tümleştirme** altında **Logic Apps**’ı seçin. Mantıksal uygulamanızı seçin.

    *    Uygulamanızın durumunu, yürütme geçmişini ve genel bilgilerini görüntülemek için, mantıksal uygulama menüsünde **Genel Bakış**’ı seçin. Beklediğiniz verileri bulamazsanız, komut çubuğunda **Yenile**’yi seçin.

    *    Uygulamanızı düzenlemek için, mantıksal uygulama menüsünde **Mantıksal Uygulama Tasarımcısı**’nı seçin.

    *    Uygulamanızı geçici olarak kapatmak için, mantıksal uygulama menüsünde **Genel Bakış**’ı seçin. Komut çubuğunda **Devre Dışı Bırak**’ı seçin.

    *    Uygulamanızı silmek için, mantıksal uygulama menüsünde **Genel Bakış**’ı seçin. 
    Komut çubuğunda **Sil**’i seçin. Mantıksal uygulamanızın adını girip **Sil**’i seçin.

5 dakikadan daha kısa bir süre içinde bulutta çalışan basit bir mantıksal uygulama ayarlayabildiniz. Logic Apps özelliklerini kullanma hakkında daha fazla bilgi için bkz. [Logic Apps özelliklerini kullanma]. Mantıksal Uygulama tanımları hakkında bilgi edinmek için bkz. [Mantıksal Uygulama tanımları yazma](../logic-apps/logic-apps-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Logic Apps özelliklerini kullanma]: logic-apps-create-a-logic-app.md
