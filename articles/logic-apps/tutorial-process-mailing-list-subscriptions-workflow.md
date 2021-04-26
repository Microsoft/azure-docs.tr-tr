---
title: Onay tabanlı otomatik iş akışları oluşturun
description: Öğretici-Azure Logic Apps kullanarak posta listesi aboneliklerini işleyen onay tabanlı bir otomatik iş akışı oluşturma
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: c6186e6e9f60e852f77943834bcd1ae3d526491d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777284"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Öğretici: Azure Logic Apps kullanarak otomatik onay tabanlı iş akışları oluşturma

Bu öğreticide, onay tabanlı bir iş akışını otomatikleştiren örnek bir [mantık uygulamasının](../logic-apps/logic-apps-overview.md) nasıl oluşturulacağı gösterilmektedir. Özellikle, bu örnek mantıksal uygulama, [MailChimp](https://mailchimp.com/) hizmeti tarafından yönetilen bir posta listesi için abonelik isteklerini işler. Bu mantıksal uygulama, istekler için bir e-posta hesabını izleyerek başlayan çeşitli adımlar içerir, bu istekleri onaya gönderir, isteğin onay alıp alınmadığını denetler, onaylanan üyeleri posta listesine ekler ve listeye yeni üyelerin eklenip eklenmeyeceğini onaylar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
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

* Bir Azure hesabı ve aboneliği Aboneliğiniz yoksa, [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Mantıksal uygulamanızın onaylanan Üyeler için e-posta adresi ekleyebildiği "test-Members-ML" adlı bir listeden daha önce oluşturduğunuz bir MailChimp hesabı. Hesabınız yoksa, [ücretsiz hesap için kaydolun](https://login.mailchimp.com/signup/)ve ardından [bir MailChimp listesi oluşturmayı](https://us17.admin.mailchimp.com/lists/#)öğrenin.

* Office 365 Outlook, Outlook.com veya Gmail gibi Logic Apps tarafından desteklenen bir e-posta sağlayıcısından e-posta hesabı. Diğer sağlayıcılar için [buradaki bağlayıcı listesini inceleyin](/connectors/). Bu hızlı başlangıç, Office 365 Outlook 'U iş veya okul hesabıyla kullanır. Farklı bir e-posta hesabı kullanırsanız, genel adımlar aynı kalır, ancak kullanıcı arabirimi biraz farklı görünebilir.

* Office 365 Outlook veya Outlook.com 'de onay iş akışlarını destekleyen bir e-posta hesabı. Bu öğreticide Office 365 Outlook kullanılmaktadır. Farklı bir e-posta hesabı kullanırsanız genel adımlar aynı kalır, ancak kullanıcı arabiriminiz biraz farklı görünebilir.

* Mantıksal uygulamanızın trafiği belirli IP adresleriyle sınırlayan bir güvenlik duvarı üzerinden iletişim kurması gerekiyorsa, söz konusu güvenlik duvarının, mantıksal uygulamanızın bulunduğu Azure bölgesindeki Logic Apps hizmeti veya çalışma zamanı tarafından kullanılan [gelen](logic-apps-limits-and-config.md#inbound) ve [giden](logic-apps-limits-and-config.md#outbound) IP *adresleri için erişime* izin verilmesi gerekir. Mantıksal uygulamanız ayrıca Office 365 Outlook Bağlayıcısı veya SQL Bağlayıcısı gibi [yönetilen bağlayıcılar](../connectors/managed.md)kullanıyorsa veya [özel bağlayıcılar](/connectors/custom-connectors/)kullanıyorsa, güvenlik duvarının ayrıca mantıksal uygulamanızın Azure bölgesindeki *Tüm* [yönetilen bağlayıcı giden IP adresleri](logic-apps-limits-and-config.md#outbound) için erişime izin verilmesi gerekir.

## <a name="create-your-logic-app"></a>Mantıksal uygulamanızı oluşturma

1. Azure hesabınızın kimlik bilgileriyle [Azure portalında](https://portal.azure.com) oturum açın. Azure giriş sayfasında, **kaynak oluştur**' u seçin.

1. Azure Marketi menüsünde **tümleştirme**  >  **mantıksal uygulaması**' nı seçin.

   !["Tümleştirme" ve "mantıksal uygulama" seçiliyken Azure Marketi menüsünü gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. **Mantıksal uygulama** bölmesinde, oluşturmak istediğiniz mantıksal uygulama hakkında burada açıklanan bilgileri sağlayın.

   ![Mantıksal uygulama oluşturma bölmesini ve yeni mantıksal uygulama için sağlanacak bilgileri gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Abonelik** | <*Azure-abonelik-adı*> | Azure abonelik adınız. Bu örnekte `Pay-As-You-Go` kullanılmıştır. |
   | **Kaynak grubu** | LA-MailingList-RG | İlgili kaynakları düzenlemek için kullanılan [Azure Kaynak grubunun](../azure-resource-manager/management/overview.md)adı. Bu örnek adlı yeni bir kaynak grubu oluşturur `LA-MailingList-RG` . |
   | **Ad** | LA-MailingList | Mantıksal uygulamanızın adı, yalnızca harf, sayı, kısa çizgi ( `-` ), alt çizgi ( `_` ), parantez ( `(` , `)` ) ve nokta ( `.` ) içerebilir. Bu örnekte `LA-MailingList` kullanılmıştır. |
   | **Konum** | Batı ABD | Mantıksal uygulama bilgilerinizin depolanacağı bölge. Bu örnekte `West US` kullanılmıştır. |
   | **Log Analytics** | Kapalı | Tanılama günlüğüne kaydetme ayarını **Kapalı** durumda bırakın. |
   ||||

1. İşiniz bittiğinde, **gözden geçir + oluştur**' u seçin. Azure mantıksal uygulamanız hakkındaki bilgileri doğruladıktan sonra **Oluştur**' u seçin.

1. Azure uygulamanızı dağıtduktan sonra **Kaynağa Git**' i seçin.

   Azure, giriş videosunu, yaygın olarak kullanılan Tetikleyicileri ve mantıksal uygulama şablonu düzenlerini gösteren Logic Apps şablonu seçim bölmesini açar.

1. Video ve ortak Tetikleyiciler bölümlerinin ardından **Şablonlar** bölümüne gidip **boş mantıksal uygulama**' yı seçin.

   !["Boş mantıksal uygulama" seçiliyken Logic Apps şablonu seçim bölmesini gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Sonra, abonelik istekleri olan gelen e-postaları dinleyen bir Outlook [tetikleyicisi](../logic-apps/logic-apps-overview.md#logic-app-concepts) ekleyin. Her mantıksal uygulama, belirli bir olay gerçekleştiğinde veya yeni veriler belirli bir koşulu karşıladığında tetiklenen bir tetikleyiciyle başlamalıdır. Daha fazla bilgi için bkz. [İlk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>E-postaları izlemek için tetikleyici ekleme

1. Logic Apps tasarımcı arama kutusuna girin `when email arrives` ve **Yeni bir e-posta geldiğinde** adlı tetikleyiciyi seçin.

   * Azure iş veya okul hesapları için **Office 365 Outlook** girişini seçin.
   * Kişisel Microsoft hesapları için **Outlook.com** girişini seçin.

   Bu örnek, Office 365 Outlook seçilerek devam eder.

   !["E-posta geldiğinde" arama terimini içeren ve "yeni bir e-posta geldiğinde" tetikleyicisinin seçili olduğu Logic Apps tasarımcı arama kutusunu gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. Henüz bir bağlantınız yoksa, oturum açın ve istendiğinde e-posta hesabınıza erişim kimliğini doğrulayın.

   Azure Logic Apps, e-posta hesabınıza bir bağlantı oluşturur.

1. Tetikleyicide, yeni e-posta denetimi ölçütlerini belirtin.

   1. E-postaların denetlenmesi için klasörü belirtin ve diğer özellikleri varsayılan değerlerine ayarlayın.

      !["Yeni bir e-posta geldiğinde" eylemi ve "klasör" "gelen kutusu" olarak ayarlandığında tasarımcıyı gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. E-postaları konu satırına göre filtreleyebilmeniz için Tetikleyicinin **Konu filtresi** özelliğini ekleyin. **Yeni parametre Ekle** listesini açın ve **Konu filtresi**' ni seçin.

      !["Konu filtresi" seçiliyken açılan "yeni parametre Ekle" listesini gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Bu tetikleyicinin özellikleri hakkında daha fazla bilgi için bkz. [Office 365 Outlook Bağlayıcısı başvurusu](/connectors/office365/) veya [Outlook.com bağlayıcı başvurusu](/connectors/outlook/).

   1. Özelliği tetikleyicide görüntülendikten sonra şu metni girin: `subscribe-test-members-ML`

      !["Subject filtresi" özelliğini "Subscribe-test-Members-ML" metni ile gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Tetikleyicinin ayrıntılarını şimdilik gizlemek için şeklin başlık çubuğunun içine tıklayarak şekli daraltın.

   ![Daraltılan tetikleyici şeklini gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Mantıksal uygulamanız çalışıyor ancak gelen e-postanızı denetleme dışında bir işlem gerçekleştirmiyor. Şimdi, tetikleyici etkinleştirildiğinde gerçekleştirilecek bir eylem ekleyin.

## <a name="send-approval-email"></a>Onay e-postası gönderme

Artık tetikleyiciniz olduğuna göre, isteği onaylamak veya reddetmek üzere e-posta gönderen bir [eylem](../logic-apps/logic-apps-overview.md#logic-app-concepts) ekleyin.

1. Logic Apps tasarımcısında, **Yeni bir e-posta geldiğinde** tetikleme bölümünde **yeni adım**' ı seçin.

1. **Işlem seçin** altında, arama kutusuna girin `send approval` ve **onay e-postası gönder** adlı eylemi seçin.

   !["Onay" eylemleri ve "onay e-postası gönder" eylemi seçili "bir işlem seçin" listesinin gösterildiği ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Şimdi gösterilen belirtilen özelliklerin değerlerini girin ve burada açıklanmıştır. Diğerlerinin varsayılan değerlerinde ayrılın. Bu özellikler hakkında daha fazla bilgi için bkz. [Office 365 Outlook Bağlayıcısı başvurusu](/connectors/office365/) veya [Outlook.com bağlayıcı başvurusu](/connectors/outlook/).

   !["Onay e-postası gönder" özelliklerini gösteren ekran görüntüsü](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Kime** | <*onay-e-posta adresi*> | Onaylayanın e-posta adresi. Test için kendi adresinizi kullanabilirsiniz. Bu örnek kurgusal `sophiaowen@fabrikam.com` e-posta adresini kullanır. |
   | **Konu** | `Approve member request for test-members-ML` | Açıklayıcı bir e-posta konusu |
   | **Kullanıcı Seçenekleri** | `Approve, Reject` | Bu özelliğin varsayılan olarak **onaylama** veya **reddetme** olan, onaylayanın seçim yapabileceği yanıt seçeneklerini belirttiğinden emin olun. |
   ||||

   > [!NOTE]
   > Bazı düzenleme kutularının içine tıkladığınızda, dinamik içerik listesi görünür ve bu, şimdilik yoksayabilirsiniz. Bu liste, iş akışınızda sonraki eylemlere giriş olarak seçebileceğiniz önceki eylemlerden çıkışları gösterir.
 
1. Mantıksal uygulamanızı kaydedin.

Sonra, onaylayanın seçili yanıtını denetleyen bir koşul ekleyin.

## <a name="check-approval-response"></a>Onay yanıtını denetleme

1. **Onay e-postası gönder** eyleminin altında **yeni adım**' ı seçin.

1. **Işlem seçin** altında, **yerleşik**' i seçin. Arama kutusuna girin `condition` ve **koşul** adlı eylemi seçin.

   !["Koşul" eylemi seçili durumdayken arama terimi olarak "yerleşik" seçili ve "koşul" içeren "işlem seçin" arama kutusunu gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. **Koşul** başlık çubuğunda **üç nokta** (**...**) düğmesini ve ardından **Yeniden Adlandır**' ı seçin. Koşulu şu açıklama ile yeniden adlandırın: `If request approved`

   !["Ayarlar" listesinin açıldığı ve "yeniden adlandır" komutunun seçildiği üç nokta düğmesini gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. Onaylayanın **seçili olup** olmadığını denetleyen bir koşul oluşturun.

   1. Koşulun sol tarafında, **bir değer seçin** kutusunun içine tıklayın.

   1. Görünen dinamik içerik listesinden **onay e-postası gönder** altında **SelectedOption** özelliğini seçin.

      !["Onay e-postası gönder" bölümünde yer alan dinamik içerik listesini gösteren ekran görüntüsü, "SelectedOption" çıkışının seçili olduğunu gösterir.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. Orta karşılaştırma kutusunda, **eşittir** işlecini seçin.

   1. Koşulun sağ tarafında, **bir değer seçin** kutusunda, metnini girin `Approve` .

      İşiniz bittiğinde, koşul şu örneğe benzer şekilde görünür:

      ![Onaylanan istek örneği için tamamlanmış koşulu gösteren ekran görüntüsü](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Mantıksal uygulamanızı kaydedin.

Ardından, gözden geçiren bir isteği onayladığında mantıksal uygulamanızın gerçekleştireceği eylemi belirtin. 

## <a name="add-member-to-mailchimp-list"></a>MailChimp listesine üye ekleme

Şimdi, onaylanan üyeyi posta listenize ekleyen bir eylem ekleyin.

1. Koşulun **doğru** dalında **Eylem Ekle**' yi seçin.

1. İşlem araması **seçin** arama kutusunda **Tümü**' nü seçin. Arama kutusuna girin `mailchimp` ve ardından **üye Ekle listesine** adlı eylemi seçin.

   !["MailChimp" arama terimi ve "listeye üye Ekle" eylemi seçili olan "işlem seç" kutusunu gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Henüz MailChimp hesabınızla bağlantınız yoksa oturum açmanız istenir.

1. **Listeye üye Ekle** eyleminde, aşağıda gösterildiği gibi bilgileri girin ve burada açıklanmıştır:

   !["Listeye üye Ekle" eylem bilgilerini gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Özellik | Gerekli | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Liste Kimliği** | Yes | <*posta-liste-adı*> | MailChimp posta listenizin adını seçin. Bu örnekte `test-members-ML` kullanılmıştır. |
   | **E-posta adresi** | Yes | <*yeni-üye-e-posta adresi*> | Açılan dinamik içerik listesinde, **Yeni bir e-posta geldiğinde** bölümünden **Çıkış ' ı seçin ve** yeni üyenin e-posta adresini belirtin. |
   | **Durum** | Yes | <*üye-abonelik-durum*> | Yeni üye için ayarlanacak abonelik durumunu seçin. Bu örnek seçer `subscribed` . <p>Daha fazla bilgi için bkz. [MailChimp API'siyle aboneleri yönetme](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   |||||

   **Listeye üye Ekle** eylem özellikleri hakkında daha fazla bilgi Için, [MailChimp bağlayıcı başvurusuna](/connectors/mailchimp/)bakın.

1. Mantıksal uygulamanızı kaydedin.

Ardından, yeni üyenin posta listenize başarıyla katılıp katılmadığını denetlerken kullanabileceğiniz bir koşul ekleyin. Bu şekilde, mantıksal uygulamanız bu işlemin başarılı veya başarısız olup olmadığını size bildirebilir.

## <a name="check-for-success-or-failure"></a>Başarı veya başarısızlık durumunu denetleme

1. **Doğru** dalda, **listeye üye Ekle** eyleminin altında **Eylem Ekle**' yi seçin.

1. **Işlem seçin** altında, **yerleşik**' i seçin. Arama kutusuna girin `condition` ve **koşul** adlı eylemi seçin.

1. Koşulu şu açıklama ile yeniden adlandırın: `If add member succeeded`

1. Onaylanan üyenin posta listenize katılımının başarılı mı yoksa başarısız mı olduğunu denetleyen bir koşul oluşturun:

   1. Koşulun sol tarafında, **bir değer seçin** kutusunun içine tıklayın. Görüntülenen dinamik içerik listesinden **üye listeye ekle** bölümünde **durum** özelliğini seçin.

      Örneğin, koşulunuz şu örnekteki gibi görünür:

      ![Koşulun sol tarafını "durum" girilmiş "bir değer Seç" kutusunu gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. Orta karşılaştırma kutusunda, **eşittir** işlecini seçin.

   1. Koşulun sağ tarafında, **bir değer seçin** kutusuna şu metni girin: `subscribed`

      İşiniz bittiğinde, koşul şu örneğe benzer şekilde görünür:

      ![Başarılı veya başarısız aboneliği denetlemeye yönelik tamamlandı koşulunu gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Ardından, onaylanan üye e-posta listenize katılırken başarılı veya başarısız olduğunda gönderilecek e-postaları ayarlayın.

## <a name="send-email-if-member-added"></a>Üye eklendiyse e-posta gönderme

1. **Üye ekleme başarılı** durumu seçeneğinin altında, **doğru** dalda **Eylem Ekle**' yi seçin.

   !["Üye ekleme başarılı oldu" koşulunun "bir eylem Ekle" seçiliyken "doğru" dalını gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. İşlem araması **seçin** arama kutusuna girin `outlook send email` ve **e-posta gönder** adlı eylemi seçin.

   !["Outlook Gönder e-postası" ve bildirim için "e-posta gönder" eylemi seçili "bir işlem seçin" arama kutusunu gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Eylemi şu açıklama ile yeniden adlandırın: `Send email on success`

1. **Başarı durumunda e-posta gönder** eyleminde, aşağıda gösterildiği gibi bilgileri sağlayın ve burada açıklanmıştır:

   !["Başarılı olduğunda e-posta gönder" eylemini ve başarı e-postası için belirtilen bilgileri gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Özellik | Gerekli | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Gövde** | Yes | <*başarılı-e-posta gövdesi*> | Başarı e-postasının gövde içeriği. Bu öğretici için aşağıdaki adımları izleyin: <p>1. bu metni sonundaki bir boşluk ile girin: `New member has joined "test-members-ML":` <p>2. görüntülenen dinamik içerik listesinden **e-posta adresi** özelliğini seçin. <p>**Note**: Bu özellik görünmezse **listede üye Ekle** bölüm başlığının yanındaki **daha fazla göster**' i seçin. <p>3. sonraki satırda, bu metni sonundaki bir boşluk ile girin: `Member opt-in status: ` <p>4. dinamik içerik listesinden **üye Ekle** altında, **durum** özelliğini seçin. |
   | **Konu** | Yes | <*başarılı-e-posta-konu*> | Başarı e-postasının konusu. Bu öğretici için aşağıdaki adımları izleyin: <p>1. bu metni sonundaki bir boşluk ile girin: `Success! Member added to "test-members-ML": ` <p>2. dinamik içerik listesinden **üye Ekle**' nin altında, **e-posta adresi** özelliğini seçin. |
   | **Kime** | Yes | <*e-posta adresiniz*> | Başarı e-postasının gönderileceği e-posta adresi. Test için kendi e-posta adresinizi kullanabilirsiniz. |
   |||||

1. Mantıksal uygulamanızı kaydedin.

## <a name="send-email-if-member-not-added"></a>Üye eklenmediyse e-posta gönderme

1. **Üye ekleme başarılı** durumu seçeneğinin altında, **yanlış** dalda **Eylem Ekle**' yi seçin.

   !["Üye ekleme başarılı" koşulun "yanlış" dalını "Eylem Ekle" seçiliyken gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. İşlem araması **seçin** arama kutusuna girin `outlook send email` ve **e-posta gönder** adlı eylemi seçin.

   !["Outlook Gönder e-postası" girilmiş ve "e-posta gönder" eylemi seçili olan "işlem seçin" arama kutusunu gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Eylemi şu açıklama ile yeniden adlandırın: `Send email on failure`

1. Burada gösterildiği ve açıklandığı gibi bu eylemle ilgili bilgi sağlayın:

   !["Hata üzerinde e-posta gönder" eylemini ve hata e-postası için belirtilen bilgileri gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Özellik | Gerekli | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Gövde** | Yes | <*hata gövdesi-e-posta*> | Başarısızlık e-postasının gövde içeriği. Bu öğretici için şu metni girin: <p>`Member might already exist. Check your MailChimp account.` |
   | **Konu** | Yes | <*sorun-e-posta*> | Başarısızlık e-postasının konusu. Bu öğretici için aşağıdaki adımları izleyin: <p>1. bu metni sonundaki bir boşluk ile girin: `Failed, member not added to "test-members-ML": ` <p>2. dinamik içerik listesinden **üye Ekle**' nin altında, **e-posta adresi** özelliğini seçin. |
   | **Kime** | Yes | <*e-posta adresiniz*> | Başarısızlık e-postasının gönderileceği e-posta adresi. Test için kendi e-posta adresinizi kullanabilirsiniz. |
   |||||

1. Mantıksal uygulamanızı kaydedin. 

Ardından mantıksal uygulamanızı test edin; mantıksal uygulamanız şu örnek gibi görünür:

![Örnek mantıksal uygulama iş akışını gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Mantıksal uygulamanızı çalıştırın

1. Posta listenize katılmak için kendinize bir e-posta isteği gönderin. İsteğin gelen kutunuzda gösterilmesini bekleyin.

1. Mantıksal uygulamanızı el ile başlatmak için, Tasarımcı araç çubuğunda **Çalıştır**' ı seçin. 

   E-postanızın konusu tetikleyicinin konu filtresiyle eşleşiyorsa, mantıksal uygulamanız abonelik isteğini onaylamak için size bir e-posta gönderir.

1. Aldığınız onay e-postasında **Onayla**' yı seçin.

1. Abonenin e-posta adresi posta listenizde yoksa, mantıksal uygulamanız bu kişinin e-posta adresini ekler ve size şu örnekteki gibi bir e-posta gönderir:

   ![Başarılı bir abonelik için örnek e-postasını gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Mantıksal uygulamanız aboneyi ekleyemezse, şu örnekteki gibi bir e-posta alırsınız:

   ![Hatalı bir abonelik için örnek e-postasını gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > E-posta gelmezse istenmeyen e-posta klasörüne bakın. E-postanızın istenmeyen posta filtresi bu tür postaları yeniden yönlendirebilir. Mantıksal uygulamanızın düzgün bir şekilde çalışıp çalışmadığından emin değilseniz bkz. [Mantıksal uygulama sorunlarını giderme](../logic-apps/logic-apps-diagnosing-failures.md).

Tebrikler, Azure, Microsoft hizmetleri ve diğer SaaS uygulamaları arasında bilgileri tümleştiren bir mantıksal uygulama oluşturdunuz ve çalıştırdınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Mantıksal uygulamanız, uygulamayı devre dışı bırakana veya silme yapılıncaya kadar çalışmaya devam eder. Örnek mantıksal uygulamaya artık ihtiyaç duymadığınızda, mantıksal uygulamanızı ve ilgili kaynakları içeren kaynak grubunu silin.

1. Azure portal arama kutusuna oluşturduğunuz kaynak grubunun adını girin. Sonuçlardan **kaynak grupları**' nın altında kaynak grubunu seçin.

   Bu örnek adlı kaynak grubunu oluşturdu `LA-MailingList-RG` .

   !["La-mailinglist-RG" girilmiş ve * * LA-MailingList-RG * * seçili olan Azure Arama kutusunu gösteren ekran görüntüsü.](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > Azure giriş sayfasında **son kaynaklar** altında kaynak grubu görünüyorsa, giriş sayfasından grubu seçebilirsiniz.

1. Kaynak grubu menüsünde, **genel bakış** ' ın seçili olduğunu denetleyin. **Genel bakış** bölmesinin araç çubuğunda **kaynak grubunu sil**' i seçin.

   ![Kaynak grubunun "genel bakış" bölmesini gösteren ekran görüntüsü ve bölmenin araç çubuğunda "kaynak grubunu sil" seçilidir.](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Görüntülenen onay bölmesinde, kaynak grubu adını girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, posta listesi istekleri için onayları işleyen bir mantıksal uygulama oluşturdunuz. Şimdi, Azure Depolama ve Azure İşlevleri gibi Azure hizmetlerini tümleştirerek e-posta eklerini işleyen ve depolayan bir mantıksal uygulama oluşturmayı öğrenin.

> [!div class="nextstepaction"]
> [E-posta eklerini işleme](../logic-apps/tutorial-process-email-attachments-workflow.md)
