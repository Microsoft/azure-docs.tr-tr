---
title: Onay tabanlı otomatik iş akışları oluşturun-Azure Logic Apps
description: Öğretici-Azure Logic Apps kullanarak posta listesi aboneliklerini işleyen onay tabanlı bir otomatik iş akışı oluşturma
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: f720e22542533d17fc7ab581f8ba8d9c03a89570
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025574"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Öğretici: Azure Logic Apps kullanarak otomatik onay tabanlı iş akışları oluşturma

Bu öğreticide, onay tabanlı bir iş akışını otomatikleştiren bir [mantık uygulamasının](../logic-apps/logic-apps-overview.md) nasıl oluşturulacağı gösterilmektedir. Özellikle, bu mantıksal uygulama, [MailChimp](https://mailchimp.com/) hizmeti tarafından yönetilen bir posta listesi için abonelik isteklerini işler. Bu mantıksal uygulama e-posta hesabını bu istekler için izler, bu istekleri onaya gönderir ve onaylanan üyeleri posta listesine ekler.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Boş bir mantıksal uygulama oluşturma.
> * E-postalardaki abonelik isteklerini izleyen bir tetikleyici ekleme.
> * Bu istekleri onaylamak veya reddetmek için e-posta gönderen bir eylem ekleme.
> * Onay yanıtını denetleyen bir koşul ekleme.
> * Onaylanan üyeleri posta listesine ekleyen bir eylem ekleme.
> * Bu üyelerin listeye başarıyla katılıp katılmadığını denetleyen bir koşul ekleme.
> * Bu üyelerin listeye başarıyla katılıp katılmadığına ilişkin onay e-postaları gönderen bir eylem ekleme.

İşlemi tamamladığınızda, mantıksal uygulamanız bu yüksek düzeyli iş akışı gibi görünür:

![Üst düzey tamamlanmış mantıksal uygulamaya genel bakış](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/) .

* Mantıksal uygulamanızın onaylanan Üyeler için e-posta adresi ekleyebildiği "test-Members-ML" adlı bir liste içeren bir MailChimp hesabı. Hesabınız yoksa, [ücretsiz hesap için kaydolun](https://login.mailchimp.com/signup/)ve ardından [bir MailChimp listesi oluşturmayı](https://us17.admin.mailchimp.com/lists/#)öğrenin.

* Office 365 Outlook veya Outlook.com 'de onay iş akışlarını destekleyen bir e-posta hesabı. Bu makalede Office 365 Outlook kullanılır. Farklı bir e-posta hesabı kullanırsanız genel adımlar aynı kalır, ancak kullanıcı arabiriminiz biraz farklı görünebilir.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Azure hesabınızın kimlik bilgileriyle [Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-your-logic-app"></a>Mantıksal uygulamanızı oluşturma

1. Ana Azure menüsünden **kaynak oluştur** > **tümleştirme** > **mantıksal uygulama**' yı seçin.

   ![Yeni mantıksal uygulama kaynağınızı oluşturma](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. **Mantıksal uygulama oluştur** bölümünde, gösterildiği ve açıklandığı gibi mantıksal uygulamanızla ilgili bu bilgileri sağlayın. İşiniz bittiğinde **Oluştur**’u seçin.

   ![Mantıksal uygulamanız hakkında bilgi sağlayın](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Adı** | LA-MailingList | Mantıksal uygulamanızın adı, yalnızca harf, sayı, kısa çizgi (`-`), alt çizgi (`_`), parantezler (`(`, `)`) ve nokta (`.`) içerebilir. Bu örnekte "LA-MailingList" kullanılmaktadır. |
   | **Abonelik** | <*your-Azure-subscription-name*> | Azure abonelik adınız |
   | **Kaynak grubu** | LA-MailingList-RG | İlgili kaynakları düzenlemek için kullanılan [Azure Kaynak grubunun](../azure-resource-manager/resource-group-overview.md)adı. Bu örnek, "LA-MailingList-RG" kullanır. |
   | **Konum** | Batı ABD | Mantıksal uygulama bilgilerinizin depolanacağı bölge. Bu örnek, "Batı ABD" kullanır. |
   | **Log Analytics** | Kapalı | Tanılama günlüğüne kaydetme ayarını **Kapalı** durumda bırakın. |
   ||||

1. Azure 'un uygulamanızı dağıtmasından sonra Azure araç çubuğunda **bildirimler** >  dağıtılan mantıksal uygulamanız Için**Kaynağa Git** ' i seçin.

   ![Yeni mantıksal uygulama kaynağına gidin](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   Ya da, arama kutusuna adı yazarak mantıksal uygulamanızı bulabilir ve seçebilirsiniz.

   Logic Apps Tasarımcısı açılır ve bir tanıtım videosu ile sık kullanılan Tetikleyiciler ve mantıksal uygulama desenlerine sahip bir sayfa gösterir. **Şablonlar** altında **Boş Mantıksal Uygulama**'yı seçin.

   ![Boş mantıksal uygulama şablonu seçin](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Sonra abonelik isteklerinin bulunduğu gelen e-postaları dinleyen bir [tetikleyici](../logic-apps/logic-apps-overview.md#logic-app-concepts) ekleyin. Her mantıksal uygulama, belirli bir olay gerçekleştiğinde veya yeni veriler belirli bir koşulu karşıladığında tetiklenen bir tetikleyiciyle başlamalıdır. Daha fazla bilgi için bkz. [İlk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>E-postaları izlemek için tetikleyici ekleme

1. Mantıksal uygulama Tasarımcısı ' nda, ara kutusuna filtreniz olarak `when email arrives` girin. **Tetikleyiciler** listesinden, e-posta sağlayıcınız için **Yeni bir e-posta geldiğinde** tetikleyiciyi seçin.

   Bu örnek, Office 365 Outlook tetikleyicisini kullanır:

   ![E-posta sağlayıcısı için "yeni bir e-posta geldiğinde" tetikleyicisi seçeneğini belirleyin](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Azure iş veya okul hesapları için Office 365 Outlook girişini seçin.
   * Kişisel Microsoft hesapları için Outlook.com girişini seçin.

1. İstenirse, kimlik bilgilerinizle e-posta hesabınızda oturum açın Logic Apps e-posta hesabınızla bağlantı oluşturabilir.

1. Tetikleyicide, tüm yeni e-postaları denetleme ölçütlerini belirtin.

   1. E-postaları denetlemeye ilişkin klasörü, aralığı ve sıklığı belirtin.

      ![Postaları denetlemeye ilişkin klasörü, aralığı ve sıklığı belirtin](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Özellik | Değer | Açıklama |
      |----------|-------|-------------|
      | **Klasör** | `Inbox` | İzlenecek e-posta klasörü |
      | **Aralık** | `1` | Denetimler arasında beklenecek aralık sayısı |
      | **Sıklık** | `Hour` | Yinelenme için kullanılacak zaman birimi |
      ||||

   1. Şimdi e-posta konu satırına filtre yapabilmeniz için tetikleyicisine başka bir özellik ekleyin. **Yeni parametre Ekle listesini**açın ve **Konu filtresi** özelliğini seçin.

      ![Tetikleyiciye "konu filtresi" özelliği Ekle](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Bu tetikleyicinin özellikleri hakkında daha fazla bilgi için bkz. [Office 365 Outlook Bağlayıcısı başvurusu](https://docs.microsoft.com/connectors/office365/) veya [Outlook.com bağlayıcı başvurusu](https://docs.microsoft.com/connectors/outlook/).

   1. Özellik tetikleyicide görüntülendikten sonra şu metni girin: `subscribe-test-members-ML`

      !["Konu filtresi" özelliği için metin girin](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Tetikleyicinin ayrıntılarını şimdilik gizlemek için tetikleyicinin başlık çubuğuna tıklayın.

   ![Ayrıntıları gizlemek için şekli daraltın](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Mantıksal uygulamanız çalışıyor ancak gelen e-postanızı denetleme dışında bir işlem gerçekleştirmiyor. Şimdi, tetikleyici etkinleştirildiğinde gerçekleştirilecek bir eylem ekleyin.

## <a name="send-approval-email"></a>Onay e-postası gönderme

Artık tetikleyiciniz olduğuna göre, isteği onaylamak veya reddetmek üzere e-posta gönderen bir [eylem](../logic-apps/logic-apps-overview.md#logic-app-concepts) ekleyin.

1. Tetikleyici altında **yeni adım**' ı seçin. 

1. **Eylem seçin**altında, arama kutusuna filtreniz olarak `approval` girin. Eylemler listesinden, e-posta sağlayıcınız için **onay e-postası gönder** eylemini seçin. 

   Bu örnek, Office 365 Outlook eylemini kullanır:

   !["Onay e-postası gönder" eylemini seçin](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Bu eylemle ilgili bilgileri açıklandığı şekilde sağlayın: 

   ![Onay e-postası özellikleri gönder](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Alıcı** | <*eposta-adresiniz*> | Onaylayanın e-posta adresi. Test için kendi adresinizi kullanabilirsiniz. Bu örnek, kurgusal "sophia.owen@fabrikam.com" e-posta adresini kullanır. |
   | **Konu** | `Approve member request for test-members-ML` | Açıklayıcı bir e-posta konusu |
   | **Kullanıcı Seçenekleri** | `Approve, Reject` | Onaylayanın seçim yapabilir yanıt seçenekleri. Varsayılan olarak, onaylayan yanıt olarak "Onayla" veya "Reddet" seçeneğini belirleyebilir. |
   ||||

   Şimdilik, belirli düzenleme kutularının içine tıkladığınızda görüntülenen dinamik içerik listesini yoksayın. Bu liste, iş akışınızda giriş olarak kullanabileceğiniz önceki eylemlerden kullanılabilir çıktıyı seçmenizi sağlar.

   Bu eylemin özellikleri hakkında daha fazla bilgi için bkz. [Office 365 Outlook Bağlayıcısı başvurusu](https://docs.microsoft.com/connectors/office365/) veya [Outlook.com bağlayıcı başvurusu](https://docs.microsoft.com/connectors/outlook/).
 
1. Mantıksal uygulamanızı kaydedin.

Sonra, onaylayanın seçili yanıtını denetlemek için bir koşul ekleyin.

## <a name="check-approval-response"></a>Onay yanıtını denetleme

1. **Onay e-postası gönder** eyleminin altında **yeni adım**' ı seçin.

1. **Eylem seçin**altında, **yerleşik**' i seçin. Arama kutusuna filtreniz olarak `condition` girin. Eylemler listesinden **koşul** eylemini seçin.

   !["Koşul" eylemini bulup seçin](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Koşulu daha iyi bir açıklama ile yeniden adlandırın.

   1. Koşulun başlık çubuğunda, **Yeniden Adlandır**' > **üç nokta** ( **...** ) düğmesini seçin.

      ![Koşulun açıklamasını yeniden adlandır](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Koşulunuzu şu açıklama ile yeniden adlandırın: `If request approved`

1. Onaylayanın **seçili olup**olmadığını denetleyen bir koşul oluşturun.

   1. Koşulda, koşulun sol tarafındaki **bir değer seçin** kutusunun içine tıklayın.

   1. Görünen dinamik içerik listesinden **onay e-postası gönder**altında **SelectedOption** özelliğini seçin.

      ![Dinamik içerik listesinden "SelectedOption" öğesini seçin.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. Orta karşılaştırma kutusunda, **eşittir** işlecini seçin.

   1. Koşulun sağ tarafındaki **bir değer seçin** kutusunda şu metni girin: `Approve`

      İşiniz bittiğinde, koşul şu örneğe benzer şekilde görünür:

      ![Onaylanan örnek için tamamlandı koşulu](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Mantıksal uygulamanızı kaydedin.

Ardından, gözden geçiren bir isteği onayladığında mantıksal uygulamanızın gerçekleştireceği eylemi belirtin. 

## <a name="add-member-to-mailchimp-list"></a>MailChimp listesine üye ekleme

Şimdi, onaylanan üyeyi posta listenize ekleyen bir eylem ekleyin.

1. Koşulun **true ise** dalında **Eylem Ekle**' yi seçin.

1. **Eylem seçin**altında filtreniz olarak `mailchimp` girin ve **listeye üye Ekle** eylemini seçin.

   !["Listeye üye Ekle" eylemini seçin](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. MailChimp hesabınıza erişmeniz istenirse, MailChimp kimlik bilgilerinizle oturum açın.

1. Burada gösterildiği ve açıklandığı gibi bu eylemle ilgili bilgi sağlayın:

   !["Listeye üye ekle" için bilgileri sağlayın](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Özellik | Gereklidir | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Liste Kimliği** | Yes | `test-members-ML` | MailChimp posta listenizin adı. Bu örnek "test-Members-ML" kullanır. |
   | **Durum** | Yes | `subscribed` | Yeni üyenin abonelik durumunu seçin. Bu örnek, "abone olunmuş" kullanır. <p>Daha fazla bilgi için bkz. [MailChimp API'siyle aboneleri yönetme](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   | **E-posta Adresi** | Yes | <*yeni-üye-eposta-adresi*> | Dinamik içerik listesinden, yeni bir e- **posta geldiğinde**altında, yeni üyenin e-posta adresini geçen ' **ı seçin.** |
   ||||

   Bu eylemin özellikleri hakkında daha fazla bilgi için, [MailChimp bağlayıcı başvurusuna](https://docs.microsoft.com/connectors/mailchimp/)bakın.

1. Mantıksal uygulamanızı kaydedin.

Ardından, yeni üyenin posta listenize başarıyla katılıp katılmadığını denetlerken kullanabileceğiniz bir koşul ekleyin. Bu şekilde, mantıksal uygulamanız bu işlemin başarılı veya başarısız olduğunu size bildirir.

## <a name="check-for-success-or-failure"></a>Başarı veya başarısızlık durumunu denetleme

1. **If true** dalında, **listeye üye Ekle** eyleminin altında **Eylem Ekle**' yi seçin.

1. **Eylem seçin**altında, **yerleşik**' i seçin. Arama kutusuna filtreniz olarak `condition` girin. Eylemler listesinden **koşul**' ı seçin.

1. Koşulu şu açıklama ile yeniden adlandırın: `If add member succeeded`

1. Onaylanan üyenin posta listenize katılımının başarılı mı yoksa başarısız mı olduğunu denetleyen bir koşul oluşturun:

   1. Koşulda, koşulun sol tarafındaki **bir değer seçin** kutusunun içine tıklayın. Dinamik içerik listesinden **üye Ekle**' nin altında, **durum** özelliğini seçin.

      Örneğin, koşulunuz şu örnekteki gibi görünür:

      !["Listeye üye ekle" öğesinin altında "Durum" seçin](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. Orta karşılaştırma kutusunda, **eşittir** işlecini seçin.

   1. Koşulun sağ tarafındaki **bir değer seçin** kutusunda şu metni girin: `subscribed`

      İşiniz bittiğinde, koşul şu örneğe benzer şekilde görünür:

      ![Abone olunan örnek için tamamlandı koşulu](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Ardından, onaylanan üyenin posta listenize katılımının başarılı veya başarısız olması durumunda gönderilecek e-postaları ayarlayın.

## <a name="send-email-if-member-added"></a>Üye eklendiyse e-posta gönderme

1. **Üye ekleme başarılı** durumu seçeneğinin altında, **If true** dalında **Eylem Ekle**' yi seçin.

   !["Eğer true" dalında "Eylem Ekle" yi seçin.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. **Eylem seçin**altında, ara kutusuna filtreniz olarak `outlook send email` girin ve **e-posta gönder** eylemini seçin.

   !["E-posta gönder" eylemi Ekle](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Eylemi şu açıklama ile yeniden adlandırın: `Send email on success`

1. Bu eylem için gösterildiği ve açıklandığı gibi bilgi sağlayın:

   ![Başarılı oldu e-postası için bilgi sağlama](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Özellik | Gereklidir | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Alıcı** | Yes | <*eposta-adresiniz*> | Başarı e-postasının gönderileceği e-posta adresi. Test için kendi e-posta adresinizi kullanabilirsiniz. |
   | **Konu** | Yes | <*başarı-epostası-konusu*> | Başarı e-postasının konusu. Bu öğretici için şu metni girin: <p>`Success! Member added to "test-members-ML": ` <p>Dinamik içerik listesinden **üye Ekle**' nin altında, **e-posta adresi** özelliğini seçin. |
   | **Gövde** | Yes | <*başarı-e-postası-gövdesi*> | Başarı e-postasının gövde içeriği. Bu öğretici için şu metni girin: <p>`New member has joined "test-members-ML":` <p>Dinamik içerik listesinden **e-posta adresi** özelliğini seçin. <p>Sonraki satırda şu metni girin: `Member opt-in status: ` <p> Dinamik içerik listesinden **üye Ekle**' nin altında, **durum** özelliğini seçin. |
   |||||

1. Mantıksal uygulamanızı kaydedin.

## <a name="send-email-if-member-not-added"></a>Üye eklenmediyse e-posta gönderme

1. **Üye ekleme başarılı** durumu seçeneğinin altında, **IF false** dalında **Eylem Ekle**' yi seçin.

   !["If false" dalında "Eylem Ekle" yi seçin.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. **Eylem seçin**altında, ara kutusuna filtreniz olarak `outlook send email` girin ve **e-posta gönder** eylemini seçin.

   !["E-posta gönder" eylemini ekleme](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Eylemi şu açıklama ile yeniden adlandırın: `Send email on failure`

1. Burada gösterildiği ve açıklandığı gibi bu eylemle ilgili bilgi sağlayın:

   ![Başarısız oldu e-postası için bilgi sağlama](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Özellik | Gereklidir | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Alıcı** | Yes | <*eposta-adresiniz*> | Başarısızlık e-postasının gönderileceği e-posta adresi. Test için kendi e-posta adresinizi kullanabilirsiniz. |
   | **Konu** | Yes | <*başarısızlık-epostası-konusu*> | Başarısızlık e-postasının konusu. Bu öğretici için şu metni girin: <p>`Failed, member not added to "test-members-ML": ` <p>Dinamik içerik listesinden **üye Ekle**' nin altında, **e-posta adresi** özelliğini seçin. |
   | **Gövde** | Yes | <*başarısızlık-epostası-gövdesi*> | Başarısızlık e-postasının gövde içeriği. Bu öğretici için şu metni girin: <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Mantıksal uygulamanızı kaydedin. 

Ardından mantıksal uygulamanızı test edin; mantıksal uygulamanız şu örnek gibi görünür:

![Örnek mantıksal uygulama iş akışı tamamlandı](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Mantıksal uygulamanızı çalıştırın

1. Posta listenize katılmak için kendinize bir e-posta isteği gönderin. İsteğin gelen kutunuzda gösterilmesini bekleyin.

1. Mantıksal uygulamanızı el ile başlatmak için tasarımcı araç çubuğu çubuğunda **Çalıştır**' ı seçin. 

   E-postanızın konusu tetikleyicinin konu filtresiyle eşleşiyorsa, mantıksal uygulamanız abonelik isteğini onaylamak için size bir e-posta gönderir.

1. Onay e-postasında **Onayla**' yı seçin.

1. Abonenin e-posta adresi posta listenizde yoksa, mantıksal uygulamanız bu kişinin e-posta adresini ekler ve size şu örnekteki gibi bir e-posta gönderir:

   ![Örnek e-posta-başarılı abonelik](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Mantıksal uygulamanız aboneyi ekleyemezse, şu örnekteki gibi bir e-posta alırsınız:

   ![Örnek e-posta-başarısız abonelik](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   E-posta gelmezse istenmeyen e-posta klasörüne bakın. E-postanızın istenmeyen posta filtresi bu tür postaları yeniden yönlendirebilir. Mantıksal uygulamanızın düzgün bir şekilde çalışıp çalışmadığından emin değilseniz bkz. [Mantıksal uygulama sorunlarını giderme](../logic-apps/logic-apps-diagnosing-failures.md).

Tebrikler, Azure, Microsoft hizmetleri ve diğer SaaS uygulamaları arasında bilgileri tümleştiren bir mantıksal uygulama oluşturdunuz ve çalıştırdınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Örnek mantıksal uygulamaya artık ihtiyaç duymadığınızda, mantıksal uygulamanızı ve ilgili kaynakları içeren kaynak grubunu silin. 

1. Ana Azure menüsünde **Kaynak grupları**’na gidin ve mantıksal uygulamanızın kaynak grubunu seçin.

1. Kaynak grubu menüsünde **genel bakış** > **kaynak grubunu sil**' i seçin. 

   !["Genel Bakış" > "Kaynak grubunu sil"](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Kaynak grubu adını onay olarak girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, posta listesi istekleri için onayları yöneten bir mantıksal uygulama oluşturdunuz. Şimdi, Azure Depolama ve Azure İşlevleri gibi Azure hizmetlerini tümleştirerek e-posta eklerini işleyen ve depolayan bir mantıksal uygulama oluşturmayı öğrenin.

> [!div class="nextstepaction"]
> [E-posta eklerini işleme](../logic-apps/tutorial-process-email-attachments-workflow.md)
