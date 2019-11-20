---
title: include dosyası
description: include dosyası
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2019
ms.custom: include file
ms.openlocfilehash: 832c0e6080b82f3c38beaf051669fbdacd37081c
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74101595"
---
>[!NOTE]
>Bu bölümde [Azure AD uygulama kaydı](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)için yönergeler sağlanmaktadır.

1. [Azure Portal](https://portal.azure.com), Genişletilebilir sol menüden **Azure Active Directory** açın ve **uygulama kayıtları** bölmesini açın. 

    [![Azure Active Directory bölmesini seçin](./media/digital-twins-permissions/select-aad-pane.png)](./media/digital-twins-permissions/select-aad-pane.png#lightbox)

1. **+ Yeni kayıt** düğmesini seçin.

    [![yeni kayıt düğmesini seçin](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. **Ad** kutusuna bu uygulama kaydı için kolay bir ad verin. **Yeniden yönlendirme URI 'si (isteğe bağlı)** bölümünde, sol taraftaki açılan menüden **ortak istemci/yerel (mobil & Masaüstü)** öğesini seçin ve sağdaki metin kutusuna `https://microsoft.com` girin. **Kaydol**’u seçin.

    [![oluştur bölmesi](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. [Uygulamanın bir **ortak istemci**olarak kaydedildiğinden](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)emin olmak için, uygulama kaydlarınızın **kimlik doğrulama** bölmesini açın ve bu bölmeyi aşağı kaydırın. **Varsayılan istemci türü** bölümünde, **uygulamayı ortak istemci olarak değerlendir**' **i seçin ve** **Kaydet**' i tıklayın.

    Manifest. JSON 'inizdeki **oauth2AllowImplicitFlow** ayarını etkinleştirmek için **erişim belirteçlerini** denetleyin.

    [![ortak istemci yapılandırma ayarı](./media/digital-twins-permissions/aad-public-client.png)](./media/digital-twins-permissions/aad-public-client.png#lightbox)

1.  Kayıtlı uygulamanızın **genel bakış** bölmesini açın ve aşağıdaki varlıkların değerlerini geçici bir dosyaya kopyalayın. Aşağıdaki bölümlerde örnek uygulamanızı yapılandırmak için bu değerleri kullanacaksınız.

    - **Uygulama (istemci) KIMLIĞI**
    - **Dizin (kiracı) KIMLIĞI**

    [![Azure Active Directory uygulama KIMLIĞI](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Uygulama kaydınız için **API izinleri** bölmesini açın. **+ Izin Ekle** düğmesini seçin. **API Izinleri iste** bölmesinde **Kuruluşumun kullandığı API 'leri** seçin ve ardından aşağıdakilerden birini arayın:
    
    1. `Azure Digital Twins`. **Azure dijital TWINS** API 'sini seçin.

        [![arama API 'SI veya Azure dijital TWINS](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Alternatif olarak, `Azure Smart Spaces Service`için arama yapın. **Azure akıllı boşluklar hizmeti** API 'sini seçin.

        [Azure akıllı alanları için Search API ![](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Görüntülenecek Azure AD API adı ve KIMLIĞI, kiracınıza bağlı olarak değişir:
    > * Test kiracının ve müşteri hesaplarının `Azure Digital Twins`araması gerekir.
    > * Diğer Microsoft hesaplarının `Azure Smart Spaces Service`araması gerekir.

1. Her iki API, aynı **istek API 'si izinleri** bölmesinde **Azure dijital TWINS** olarak gösterilir. **Oku** açılan seçeneğini belirleyin ve ardından **oku. yazma** onay kutusunu seçin. **Izin Ekle** düğmesini seçin.

    [![API izinleri ekleme](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Kuruluşunuzun ayarlarına bağlı olarak, bu API 'ye yönetici erişimi sağlamak için ek adımlar gerçekleştirmeniz gerekebilir. Daha fazla bilgi için yöneticinize başvurun. Yönetici erişimi onaylandıktan sonra, **API izinleri** bölmesindeki **yönetici onayı gerekli** sütunu, izinlerinizi görüntüler. 

    [Yönetici onay onayını ![](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    **Azure dijital TWINS** 'in göründüğünü doğrulayın.
