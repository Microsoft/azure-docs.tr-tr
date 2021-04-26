---
title: HTTPS uç noktası ile müşteri adayı Yönetimi-Microsoft ticari marketi
description: Microsoft AppSource ve Azure Marketi 'nden gelen müşteri adaylarını yönetmek için güç otomatikleştirmesini ve bir HTTPS uç noktasını nasıl kullanacağınızı öğrenin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 04/09/2021
ms.openlocfilehash: 0da1fd7b98b86ebd75926cc8a2d2bb5294d048ca
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308192"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>Ticari Market müşteri adaylarını yönetmek için bir HTTPS uç noktası kullanın

Müşteri ilişkileri yönetimi (CRM) sisteminiz Iş Ortağı Merkezi 'nde Microsoft AppSource ve Azure Market müşteri adaylarını almak üzere açıkça desteklenmiyorsa, bu müşteri adaylarını işlemek için [Power otomatikleştirmede](https://powerapps.microsoft.com/automate-processes/) bir HTTPS uç noktası kullanabilirsiniz. Bir HTTPS uç noktası ile, ticari Market müşteri adayları bir e-posta bildirimi olarak gönderilebilir veya Power otomatikleştirmede desteklenen bir CRM sistemine yazılabilirler.

Bu makalede, Iş Ortağı Merkezi 'nde müşteri adaylarını yapılandırmak için kullanacağınız HTTP POST URL 'sini oluşturmak üzere Power otomatikleştirmede nasıl yeni bir akış oluşturulacağı açıklanır. Ayrıca, akışınızı [Postman](https://www.getpostman.com/downloads/)ile test etme adımlarını da içerir.

>[!NOTE]
>Bu yönergelerde kullanılan güç otomatikleştirme Bağlayıcısı, Power otomatikleştirmek için ücretli bir abonelik gerektirir. Bu akışı yapılandırmadan önce bu hesabı seçtiğinizden emin olun.

## <a name="create-a-flow-by-using-power-automate"></a>Power otomatikleştir kullanarak akış oluşturma

1. [Power otomatikleştirir](https://flow.microsoft.com/) Web sayfasını açın. **Oturum aç**'ı seçin. Henüz bir hesabınız yoksa ücretsiz **kaydolun** ' ı seçerek ücretsiz bir Power otomatikleştir hesabı oluşturun.

1. Oturum açın ve menüden **Akışlarım** ' ı seçin.

    ![Akışlarım oturum aç](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. **+ Yeni** altında **+ Hızlı bulut akışı**' nı seçin.

    ![Akışlarım + otomatik--boş](./media/commercial-marketplace-lead-management-instructions-https/https-my-flows-create-from-blank.png)

1. Akışınızı adlandırın ve ardından **bu akışın nasıl tetikleneceğini seçin** altında **bir http isteği alındığında** öğesini seçin.

    ![Otomatik akış penceresi atlama düğmesi oluştur](./media/commercial-marketplace-lead-management-instructions-https/https-my-flows-pick-request-trigger.png)

1. Açmak için Flow adımına tıklayın.

    ![Flow adımını Genişlet](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. **Istek GÖVDESI JSON şemasını** yapılandırmak için aşağıdaki yöntemlerden birini kullanın:

    - JSON şemasını **Istek GÖVDESI JSON şeması** metin kutusuna kopyalayın.
    - **Şema oluşturmak için örnek yük kullanma** öğesini seçin. **Örnek JSON yükü girin veya yapıştırın** metın kutusunda JSON örneğine yapıştırın. Şemayı oluşturmak için **bitti** ' yi seçin.

    **JSON şeması**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "Description": {
          "id": "/properties/Description",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

    **JSON örneği**
    
    ```json
    {
      "UserDetails": {
        "FirstName": "Some",
        "LastName": "One",
        "Email": "someone@contoso.com",
        "Phone": "16175555555",
        "Country": "USA",
        "Company": "Contoso",
        "Title": "Esquire"
     },
      "LeadSource": "AzureMarketplace",
      "ActionCode": "INS",
      "OfferTitle": "Test Microsoft",
      "Description": "Test run through Power Automate"
    }
    ```

>[!NOTE]
>Yapılandırmanın bu noktasında, bir CRM sistemine bağlanmayı ya da bir e-posta bildirimi yapılandırmayı seçebilirsiniz. Seçiminizi temel alarak geri kalan yönergeleri izleyin.

### <a name="connect-to-a-crm-system"></a>Bir CRM sistemine bağlanma

1. **+ Yeni adım**’ı seçin.
1. **Arama bağlayıcılar ve Eylemler '** in bulunduğu yeri arayarak istediğiniz CRM sistemini seçin. Aşağıdaki ekran, örnek olarak yeni bir kayıt (Common Data Service) oluşturmayı gösterir.

    ![Yeni kayıt oluşturma](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. **Yeni kayıt oluşturma** eylemine sahip **Eylemler sekmesini** seçin.

    ![Yeni kayıt oluştur ' u seçin](./media/commercial-marketplace-lead-management-instructions-https/select-create-new-record.png)

1. CRM sistemiyle ilişkili **kuruluş adını** belirtin. **Varlık adı** açılır listesinden **müşteri adayları** ' nı seçin.

    ![Müşteri adaylarını seçin](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. Power otomatikleştirmek, müşteri adayı bilgilerini sağlamak için bir form gösterir. Dinamik içerik eklemeyi seçerek giriş isteğinden öğeleri eşleyebilirsiniz. Aşağıdaki ekranda bir örnek olarak **Offertitle** gösterilmektedir.

    ![Dinamik içerik ekle](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. İstediğiniz alanları eşleyin ve ardından akışınızı kaydetmek için **Kaydet** ' i seçin. HTTP POST URL 'SI oluşturulur ve **BIR http isteği alındığında** penceresinde erişilebilir. HTTP POST URL 'sinin sağında bulunan kopyalama denetimini kullanarak bu URL 'YI kopyalayın. Tüm URL 'nin herhangi bir bölümünü kaçırmamak için kopyalama denetiminin kullanılması önemlidir. Yayımlama portalında lider yönetimini yapılandırırken bu URL 'YI kaydedin.

    ![Bir HTTP isteği alındığında](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>E-posta bildirimini ayarlama

1. JSON şemasını tamamladığınıza göre **+ yeni adım**' ı seçin.
1. **Eylem seçin** altında **Eylemler**' i seçin.
1. **Eylemler** sekmesinde, **e-posta gönder (Office 365 Outlook)** seçeneğini belirleyin.

    >[!NOTE]
    >Farklı bir e-posta sağlayıcısı kullanmak istiyorsanız, bunun yerine eylem olarak **bir e-posta bildirimi (posta) Gönder** ' i arayın ve seçin.

    ![E-posta eylemi ekleme](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. **E-posta gönder** penceresinde, aşağıdaki gerekli alanları yapılandırın:

   - **Kime**: müşteri adaylarının gönderileceği en az bir geçerli e-posta adresi girin.
   - **Konu**: Power otomatikleştir, aşağıdaki ekranda gösterilen **leadsource** gibi dinamik içerik ekleme seçeneği sunar. Bir alan adı girerek başlayın. Ardından açılır penceredeki dinamik içerik seçme listesini seçin. 

        >[!NOTE] 
        > Alan adları eklediğinizde, her bir adı iki nokta üst üste (:)) takip edebilirsiniz. sonra yeni bir satır oluşturmak için **ENTER** ' u seçin. Alan adlarınızı ekledikten sonra, dinamik seçim listesinden ilgili her parametreyi ekleyebilirsiniz.

        ![Dinamik içerik kullanarak e-posta eylemi ekleme](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Gövde**: dinamik içerik seçim listesinden, e-postanın gövdesinde istediğiniz bilgileri ekleyin. Örneğin, soyadı, ad, e-posta ve şirket kullanın. E-posta bildirimini ayarlamayı tamamladığınızda, aşağıdaki ekranda örneğe benzer şekilde görünür.


       ![E-posta bildirimi örneği](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. Akışınızı son vermek için **Kaydet** ' i seçin. HTTP POST URL 'SI oluşturulur ve **BIR http isteği alındığında** penceresinde erişilebilir. HTTP POST URL 'sinin sağında bulunan kopyalama denetimini kullanarak bu URL 'YI kopyalayın. Tüm URL 'nin herhangi bir bölümünü kaçırmamak için bu denetimin kullanılması önemlidir. Yayımlama portalında lider yönetimini yapılandırırken bu URL 'YI kaydedin.

   ![HTTP POST URL 'SI](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Test Etme

Yapılandırmanızı [Postman](https://app.getpostman.com/app/download/win64)ile test edebilirsiniz. Çevrimiçi Postman indirmesi Windows için kullanılabilir. 

1. Postman 'ı başlatın ve   >  Test aracınızı ayarlamak için yeni **istek** ' ı seçin. 

   ![Test aracınızı ayarlama isteği](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. **Istek kaydet** formunu doldurup oluşturduğunuz klasöre kaydedin.

   ![Istek formunu Kaydet](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. Açılan listeden **gönderi** ' ı seçin. 

   ![Akışımı test etme](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Power otomatikleştirmek için oluşturduğunuz akıştan gelen HTTP POST URL 'sini, **istek URL 'Si girin** ifadesini yapıştırın.

   ![HTTP POST URL 'sini yapıştırın](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. [Power otomatikleştirmeye](https://flow.microsoft.com/)geri dönün. Power otomatikleştir menü çubuğundan **Akışlarıma** giderek müşteri adaylarını göndermek için oluşturduğunuz akışı bulun. Daha fazla seçenek görmek için akış adının yanındaki üç noktayı seçin ve **Düzenle**' yi seçin.


1. Sağ üst köşedeki **Test** ' i seçin, **tetikleme eylemini gerçekleştir**' i seçin ve ardından **Test**' i seçin. Ekranın en üstünde testin başlatıldığını belirten bir gösterge görürsünüz.

   ![Tetikleyici eylemi seçeneğini gerçekleştirmem](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Postman uygulamanıza geri dönün ve **Gönder**' i seçin.

   ![Gönder düğmesi](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Akışınızı geri dönüp sonucu denetleyin. Her şey beklendiği gibi çalışırsa, akışın başarılı olduğunu belirten bir ileti görürsünüz.

   ![Sonuçları denetle](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. Ayrıca bir e-posta almış olmanız gerekir. E-posta gelen kutunuzu denetleyin. 

    >[!NOTE] 
    >Testten bir e-posta görmüyorsanız, istenmeyen posta ve gereksiz klasörlerinizi kontrol edin. Aşağıdaki ekranda, e-posta bildirimini yapılandırdığınızda yalnızca eklediğiniz alan etiketlerini görürsünüz. Teklifinizden üretilmiş gerçek bir müşteri adayı olsaydı, gövdesinde ve konu satırında lider kişağından gerçek bilgileri de görürsünüz.

   ![E-posta alındı](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Teklifinizi, HTTPS uç noktasına müşteri adayları gönderecek şekilde yapılandırma

Yayımlama portalında teklifiniz için lider yönetimi bilgilerini yapılandırmaya hazırsanız, bu adımları izleyin.

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/home)' nde oturum açın.

1. Teklifinizi seçin ve **teklif kurulumu** sekmesine gidin.

1. **Müşteri müşteri adayları** bölümünde **Bağlan**' ı seçin.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-https/customer-leads.png" alt-text="Müşteri liderleri":::

1. **Bağlantı ayrıntıları** açılır penceresinde, **müşteri adayı hedefi** için **https uç noktası** ' nı seçin. Önceki adımları izleyerek oluşturduğunuz akıştan HTTP POST URL 'sini **https uç noktası URL 'si** alanına yapıştırın.
    ![Bağlantı ayrıntıları Iletişim e-postası](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. **İletişim e-postası** altında, şirketinizdeki kişilerin yeni bir müşteri adayı alındığında e-posta bildirimleri alması gereken e-posta adreslerini girin. Birden çok e-postayı noktalı virgülle ayırarak sağlayabilirsiniz.

1. **Tamam**’ı seçin.

Bir müşteri adayı hedefine başarıyla bağlandığınızdan emin olmak için **Doğrula** düğmesini seçin. Başarılı olursa, müşteri adayı hedefinde bir test liderine sahip olacaksınız.

>[!NOTE] 
>Teklif için müşteri adaylarını almadan önce teklifin geri kalanını yapılandırmayı ve bunu yayımlamanız gerekir.

Müşteri adayları oluşturulduğunda, Microsoft bu akışı akışa gönderir. Müşteri adayları, yapılandırdığınız CRM sistemine veya e-posta adresine yönlendirilir.
