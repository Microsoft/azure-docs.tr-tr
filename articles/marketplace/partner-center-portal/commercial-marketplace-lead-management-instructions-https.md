---
title: HTTPS uç noktası | Azure Marketi
description: Bir HTTPS uç noktası için lider yönetimini yapılandırın.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: evansma
ms.openlocfilehash: 83efb9cfd1ee7464a334ebc4064dbfaa20ab30de
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812283"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>HTTPS uç noktası kullanarak müşteri adayı yönetimini yapılandırma

Müşteri Ilişkileri yönetimi (CRM) sisteminiz, Azure Marketi ve AppSource müşteri adaylarını almak üzere Iş Ortağı Merkezi 'nde açık bir şekilde desteklenmiyorsa, bu müşteri adaylarını işlemek için MS akışında bir HTTPS uç noktası kullanabilirsiniz. HTTPS uç noktası ile bu müşteri adayları bir e-posta bildirimi olarak gönderilebilir veya MS Flow tarafından desteklenen bir Müşteri Ilişkileri yönetimi (CRM) sistemine yazılabilir. Bu makaledeki yönergeler, Microsoft Flow kullanarak yeni akış oluşturma konusunda size yol gösterir. Bu,, sağlama Yönetim > **https uç noktası URL 'si** alanı için yayımlama PORTALıNA GIRECEĞINIZ http post URL 'sini oluşturur. Ayrıca, dahil edilen çevrimiçi olarak bulunan [Postman](https://www.getpostman.com/downloads/) adlı bir aracın yardımıyla akışınızı nasıl test edebilirsiniz hakkındaki yönergeler de mevcuttur.

## <a name="create-a-flow-using-microsoft-flow"></a>Microsoft Flow kullanarak akış oluşturma

1. [Flow](https://flow.microsoft.com/) Web sayfasını açın. **Oturum aç**' ı seçin veya henüz bir hesabınız yoksa ücretsiz **kaydolun** ' ı seçerek ücretsiz bir akış hesabı oluşturun.

2. Oturum açın ve menü çubuğunda **Akışlarım** ' ı seçin.

3. **Boş + otomatik**' i seçin.

    ![Akışlarım + otomatik-boş](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. *Otomatik akış oluştur* penceresinde **Atla**' yı seçin. 

    ![Derleme otomatik akışı-atla](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. **Bağlayıcıları ve Tetikleyicileri ara** alanına, istek bağlayıcısını bulmak için "istek" yazın.
6. *Tetikleyiciler*altında, **bir http isteği alındığında**öğesini seçin. 

    ![istek Bağlayıcısı-Tetikleyiciler](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. *BIR http isteği alındığında* penceresinde, aşağıdaki JSON şemasını Kopyala ve **Istek gövdesi JSON şeması** metin kutusuna yapıştırın. Bu şema Microsoft tarafından müşteri adayı verilerinizi içerecek şekilde kullanılır.

    ![istek Bağlayıcısı-Tetikleyiciler](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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

>[!Note]
>Yapılandırmanın bu noktasında, bir CRM sistemine bağlanmayı veya bir e-posta bildirimi yapılandırmayı seçebilirsiniz. Seçiminizi temel alarak geri kalan yönergeleri izleyin.

### <a name="to-connect-to-a-crm-system"></a>Bir CRM sistemine bağlanmak için

1. **+ Yeni adım**' ı seçin.
2. İstediğiniz CRM sistemini, *arama bağlayıcılar ve eylemler*' i arayarak seçin ve *Eylemler* bölümünün altında yeni bir kayıt oluşturma eylemine sahip seçin. Aşağıdaki ekran yakalama, **Dynamics 365** ' i gösterir. örnek olarak yeni bir kayıt oluşturur.

    ![Yeni bir kayıt oluştur](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. CRM sistemiyle ilişkili **kuruluş adını** belirtin. **Varlık adı** açılır listesinden **müşteri adayları** ' nı seçin.

    ![Müşteri adaylarını seçin](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flow, müşteri adayı bilgilerini sağlamak için bir form gösterir. Dinamik Içerik eklemeyi seçerek giriş isteğinden öğeleri eşleyebilirsiniz. Aşağıdaki ekran yakalama, bir örnek olarak **Offertitle** gösterir.

    ![Dinamik içerik Ekle](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. İstediğiniz alanları eşleyin ve sonra akışınızı kaydetmek için **Kaydet** ' i seçin. HTTP POST URL 'SI oluşturulur ve *BIR http isteği alındığında* penceresinde erişilebilir. HTTP POST URL 'sinin sağında bulunan kopyalama denetimini kullanarak bu URL 'yi kopyalayın. Bu, tüm URL 'nin herhangi bir bölümünü yanlışlıkla kaçırmamak için önemlidir. Bu URL 'YI, Yayımlama portalında lider yönetimini yapılandırırken ihtiyacınız olacak şekilde kaydedin.

    ![Bir HTTP isteği alındığında.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>E-posta bildirimi ayarlamak için

1. JSON şemasını tamamladığınıza göre **+ yeni adım**' ı seçin.
2. **Eylem seçin**altında **Eylemler**' i seçin.
3. **Eylemler**altında **e-posta gönder (Office 365 Outlook)** seçeneğini belirleyin.

    >[!Note]
    >Farklı bir e-posta sağlayıcısı araması kullanmak istiyorsanız, bunun yerine eylem olarak *bir e-posta bildirimi (posta) Gönder* ' i seçin.

    ![E-posta eylemi ekleme](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. **E-posta gönder** penceresinde, aşağıdaki gerekli alanları yapılandırın:

   - Müşteri adaylarının gönderileceği en az bir geçerli **e-posta** adresi girin.
   - **Konu** akışı, aşağıdaki ekran yakalamadaki **Leadsource** gibi dinamik içerik ekleme seçeneği sunar. Bir alan adını yazarak başlayın ve ardından açılan penceredeki dinamik Içerik seçme listesine tıklayın. 

        >[!Note] 
        > Alan adları eklerken, her bir ":" ile takip edebilir ve ardından yeni bir satır oluşturmak için girebilirsiniz. Alan adlarınızı ekledikten sonra, dinamik seçim listesinden ilgili her parametreyi ekleyebilirsiniz.

        ![Dinamik içerik kullanarak e-posta eylemi ekleme](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Gövde** -dinamik içerik seçme listesinden istediğiniz bilgileri e-postanın gövdesine ekleyin. Örneğin, soyadı, ad, e-posta ve şirket. <br> <br> E-posta bildirimini ayarlamayı tamamladığınızda, aşağıdaki ekran yakalamadaki örneğe benzer şekilde görünür.


       ![E-posta eylemi ekleme](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Akışınızı son vermek için **Kaydet** ' i seçin. HTTP POST URL 'SI oluşturulur ve *BIR http isteği alındığında* penceresinde erişilebilir. HTTP POST URL 'sinin sağında bulunan kopyalama denetimini kullanarak bu URL 'yi kopyalayın. Bu, tüm URL 'nin herhangi bir bölümünü yanlışlıkla kaçırmamak için önemlidir. Bu URL 'YI, Yayımlama portalında lider yönetimini yapılandırırken ihtiyacınız olacak şekilde kaydedin.

   ![HTTP POST URL 'SI ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Test Etme

Çevrimiçi olarak indirilebilen [Postman](https://app.getpostman.com/app/download/win64)adlı bir araç kullanarak aşağıdaki adımları kullanarak her şeyin beklendiği gibi çalıştığından test edebilirsiniz. Bu, Windows için kullanılabilir. 

1. Postman 'ı başlatın ve test aracınızı ayarlamak için **yeni** > **isteği** seçin. 

   ![Test aracınızı ayarlama isteği](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. *Isteği kaydet* formunu doldurup oluşturduğunuz klasöre **kaydedin** .

   ![Isteği Kaydet](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Açılan listeden **gönderi** ' ı seçin. 

   ![Akışımı test etme](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. MS Flow 'da oluşturduğunuz akıştan gelen HTTP POST URL 'sini, *istek URL 'Si girin*olarak yapıştırın.

   ![HTTP POST URL 'sini yapıştırın](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. [Akışa geri dönün ve akış](https://flow.microsoft.com/) menü çubuğundan **akışlarıma** giderek müşteri adaylarını göndermek için oluşturduğunuz akışı bulun.  Flow adının yanındaki 3 noktayı seçin ve **Düzenle**' yi seçin.

   ![Akışlarım-Düzenle](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Sağ üst köşedeki **Test** ' i seçin, "tetikleme eylemini gerçekleştirmem" seçeneğini belirleyip **Test**' i seçin. Ekranın üst kısmında testin başlatıldığını belirten bir gösterge görürsünüz

   ![Test akışı-tetikleyici](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Postman uygulamanıza geri dönün ve HTTPS URL 'sini yapıştırdığınız yerin yanına **Gönder** ' i seçin.

   ![Akışımı test et-gönder](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Akışınızı geri dönüp sonucu denetleyin. Her şey beklendiği gibi çalışıyorsa, başarılı olduğunu belirten bir ileti görürsünüz.

   ![Akış denetimi sonuçları](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Ayrıca bir e-posta almış olmanız gerekir. E-posta gelen kutunuzu denetleyin. 

    >[!Note] 
    >Testten bir e-posta görmüyorsanız, istenmeyen posta ve gereksiz klasörlerinizi kontrol edin. Aşağıda, e-posta bildirimini yapılandırırken yalnızca eklediğiniz alan etiketlerinin olduğunu fark edeceksiniz. Teklifinizden üretilmiş gerçek bir müşteri adayı olsaydı, gövdesinde ve konu satırında lider Kişağından gerçek bilgileri de görürsünüz.

   ![E-posta alındı](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Teklifinizi, HTTPS uç noktasına müşteri adayları gönderecek şekilde yapılandırma

Yayımlama portalında teklifiniz için müşteri adayı yönetim bilgilerini yapılandırmaya hazırsanız, aşağıdaki adımları izleyin:

1. Teklifiniz için **teklif kurulumu** sayfasına gidin.
2. Müşteri adayı Yönetimi bölümünde **Bağlan** ' ı seçin.
3. Bağlantı ayrıntıları açılır penceresinde, **müşteri adayı hedefi** Için **https uç noktası** ' nı seçin ve önceki ADıMLARı IZLEYEREK oluşturduğunuz akıştan http post URL 'sini **https uç noktası URL 'si** alanına yapıştırın.
4. **Kaydet**’i seçin. 

>[!Note] 
>Teklif için müşteri adaylarını almadan önce teklifin geri kalanını yapılandırmayı ve bunu yayımlamanız gerekir.

Müşteri adayları oluşturulduğunda, Microsoft, sizin yapılandırdığınız CRM sistemine veya e-posta adresine yönlendirilmek üzere müşteri adaylarını akışa gönderir.

![Lider yönetimi-Bağlan](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Bağlantı ayrıntıları](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Bağlantı ayrıntıları](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

