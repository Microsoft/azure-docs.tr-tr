---
title: Hızlı başlangıç-ilk Logic Apps iş akışınızı oluşturma-Azure portal
description: Bu hızlı başlangıç kılavuzunu kullanarak Azure portal ilk otomatik Logic Apps iş akışınızı oluşturun. Logic Apps 'de sistem tümleştirme ve kuruluş uygulaması Tümleştirme (EAı) çözümlerinin temellerini öğrenin.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: d05566c0734f95e14335c6165de0b2104fa19bc6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764864"
---
# <a name="quickstart-create-your-first-logic-apps-workflow---azure-portal"></a>Hızlı başlangıç: ilk Logic Apps iş akışınızı oluşturma-Azure portal

Bu hızlı başlangıçta, [Azure Portal](https://portal.azure.com)üzerinden [Azure Logic Apps](logic-apps-overview.md) ilk iş akışınızı nasıl oluşturabileceğiniz açıklanmaktadır. Bu giriş Kılavuzu Ayrıca, yeni bir mantıksal uygulama oluşturma, mantıksal uygulamanıza bir tetikleyici ve eylem ekleme ve mantıksal uygulamanızı test etme dahil olmak üzere Logic Apps hizmetinin temel kavramlarını açıklar. Düzenli olarak bir RSS akışını denetleyen ve yeni öğeler için bir e-posta bildirimi gönderen örnek bir mantık uygulaması oluşturmak için bu hızlı başlangıcı izleyin. Aşağıdaki ekran görüntüsünde, bu örnek mantıksal uygulamanın üst düzey iş akışı gösterilmektedir:

![Tetikleyicinin bir RSS akışı olduğu ve eylemin bir e-posta gönderdiği örnek mantıksal uygulamayı gösteren Logic Apps Designer 'ın ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

<a name="prerequisites"></a>

İlk mantıksal uygulamanızı diğer arabirimler ve uygulamalar aracılığıyla oluşturmayı ve yönetmeyi öğrenmek isterseniz, aşağıdaki diğer Logic Apps hızlı başlangıçlara bakın: 

* [Azure Command-Line arabirimini (Azure CLı) kullanarak Logic Apps oluşturma ve yönetme](quickstart-logic-apps-azure-cli.md)
* [Visual Studio Code 'de Logic Apps oluşturma ve yönetme](quickstart-create-logic-apps-visual-studio-code.md)
* [Visual Studio 'da mantıksal uygulamalar oluşturma ve yönetme](quickstart-create-logic-apps-with-visual-studio.md)

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure hesabı ve aboneliği Bir hesabınız yoksa, [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Logic Apps tarafından desteklenen hizmetten (Office 365 Outlook veya Outlook.com gibi) bir e-posta hesabı. Desteklenen diğer e-posta sağlayıcıları için [bağlayıcı listesini gözden geçirin](/connectors/).

    > [!IMPORTANT]
    > [Gmail bağlayıcısını](/connectors/gmail/)kullanıyorsanız, yalnızca G Suite hesaplarının Logic Apps kısıtlaması olmadan bu bağlayıcıyı kullanabileceğini unutmayın. Bir tüketici Gmail hesabınız varsa, [Gmail Bağlayıcınız ile kimlik doğrulaması için kullanmak üzere bir Google istemci uygulaması oluşturmadığınız](/connectors/gmail/#authentication-and-bring-your-own-application)müddetçe bu bağlayıcıyı yalnızca belirli Google onaylı hizmetlerle kullanabilirsiniz. Daha fazla bilgi için, bkz. [Azure Logic Apps Google bağlayıcıları Için veri güvenliği ve gizlilik ilkeleri](../connectors/connectors-google-data-security-privacy-policy.md).

* Mantıksal uygulamanızın trafiği belirli IP adresleriyle sınırlayan bir güvenlik duvarı üzerinden iletişim kurması gerekiyorsa, söz konusu güvenlik duvarının, mantıksal uygulamanızın bulunduğu Azure bölgesindeki Logic Apps hizmeti veya çalışma zamanı tarafından kullanılan [gelen](logic-apps-limits-and-config.md#inbound) ve [giden](logic-apps-limits-and-config.md#outbound) IP *adresleri için erişime* izin verilmesi gerekir. Mantıksal uygulamanız ayrıca Office 365 Outlook Bağlayıcısı veya SQL Bağlayıcısı gibi [yönetilen bağlayıcılar](../connectors/managed.md)kullanıyorsa veya [özel bağlayıcılar](/connectors/custom-connectors/)kullanıyorsa, güvenlik duvarının ayrıca mantıksal uygulamanızın Azure bölgesindeki *Tüm* [yönetilen bağlayıcı giden IP adresleri](logic-apps-limits-and-config.md#outbound) için erişime izin verilmesi gerekir.

<a name="create-logic-app"></a>

## <a name="create-your-logic-app"></a>Mantıksal uygulamanızı oluşturma

1. Azure hesabınızın kimlik bilgileriyle [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure portal arama kutusuna girin `logic apps` ve **Logic Apps** öğesini seçin.

   ![Arama terimi olarak "Logic Apps" ve seçilen arama sonucu olarak "Logic Apps" içeren arama kutusunu gösteren Azure portal ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. **Logic Apps** sayfasında **Ekle**' yi seçin.

   ![Mantıksal uygulamalar listesini ve seçili "" Ekle "düğmesini gösteren Azure portal Logic Apps hizmet sayfasının ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. **Mantıksal uygulama** bölmesinde mantıksal uygulamanız için temel ayrıntıları ve ayarları sağlayın. Bu örnek mantıksal uygulamanın amaçları doğrultusunda yeni bir [kaynak grubu](../azure-resource-manager/management/overview.md#terminology) oluşturun.

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Ad** | <*Logic-App-adı*> | Mantıksal uygulamanızın adı, bölgeler arasında benzersiz olmalıdır. Ad yalnızca harf, sayı, kısa çizgi ( `-` ), alt çizgi ( `_` ), parantez ( `(` , `)` ) ve nokta () içerebilir `.` . Bu örnek "My-First-Logic-App" kullanır. |
   | **Abonelik** | <*Azure-abonelik-adı*> | Azure aboneliğinizin adı. |
   | **Kaynak grubu** | <*Azure-Resource-Group-Name*> | Mantıksal uygulamayı oluşturmakta olduğunuz [Azure Kaynak grubunun](../azure-resource-manager/management/overview.md#terminology) adı. Kaynak grubunun adı bölgeler arasında benzersiz olmalıdır. Bu örnek "My-First-LA-RG" kullanır. |
   | **Konum** | <*Azure-bölge*> | Mantıksal uygulama bilgilerinizin depolanacağı Azure bölgesi. Bu örnek, "Batı ABD" kullanır. |
   | **Log Analytics** | Kapalı | Varsayılan olarak **kapalı** olan tanılama günlüğü ayarı. Bu örnek için **kapalı** ayarını değiştirmeyin. |
   ||||

   ![Yeni mantıksal uygulama ayrıntılarının bulunduğu bölmeyi gösteren Logic Apps oluşturma sayfasının ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

1. Hazırsanız, **gözden geçir + oluştur**' u seçin. Verdiğiniz ayrıntıları onaylayın ve **Oluştur**' u seçin.

1. Azure uygulamanızı başarıyla dağıtduktan sonra **Kaynağa Git**' i seçin. Ya da, arama kutusuna adı yazarak mantıksal uygulamanızı bulabilir ve seçebilirsiniz.

   ![Kaynak dağıtım sayfasının, seçili düğmeyi gösteren ekran görüntüsü, "kaynağa git".](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Logic Apps Tasarımcısı açılır ve bir tanıtım videosu ile sık kullanılan tetikleyicilerin bulunduğu bir sayfa görüntülenir. **Şablonlar** altında **Boş Mantıksal Uygulama**'yı seçin.

   ![Şablon galerisini ve seçili şablonu "boş mantıksal uygulama" gösteren Logic Apps Designer 'ın ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Sonra [mantıksal uygulamanıza bir tetikleyici ekleyin](#add-rss-trigger).

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>RSS tetikleyicisi ekleme

Her mantıksal uygulama, belirli bir olay gerçekleştiğinde veya belirli bir koşul karşılandığında tetiklenen bir [tetikleyiciyle](../logic-apps/logic-apps-overview.md#how-do-logic-apps-work)başlamalıdır. Tetikleyicinin her yeni öğe bulduğu her seferinde harekete geçirilir ve Logic Apps altyapısı, iş akışınızı başlatan ve çalıştıran bir mantıksal uygulama örneği oluşturur. Tetikleyici yeni öğeler bulamazsa, tetikleyici harekete geçmez ve bir örnek oluşturmaz veya iş akışını bu denetim üzerinde çalıştırmaz.

Bu hızlı başlangıç örneğinde, [bir mantıksal uygulama](#create-your-logic-app)oluşturduktan sonra bir RSS akışındaki yeni öğeleri denetleyen bir tetikleyici ekler ve yeni öğeler olduğunda ateşlenir. Ayrıca, [otomatik onay tabanlı iş akışları oluşturmak](tutorial-process-mailing-list-subscriptions-workflow.md)için öğreticide olduğu gibi farklı tür tetikleyicilerle Logic Apps de oluşturabilirsiniz.

1. **Mantıksal uygulama Tasarımcısı**' nda, arama kutusunda, **Tümü**' nü seçin.

1. RSS bağlayıcısını bulmak için arama kutusuna girin `rss` . **Tetikleyiciler** listesinden, **bir AKıŞ öğesi yayımlandığında** RSS tetikleyicisini seçin.

   ![Arama kutusunda "RSS" ile birlikte Logic Apps tasarımcı ve seçilen RSS tetikleyicisi "bir akış öğesi yayımlandığında" gösteren ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Tetikleyicinizin RSS akışı URL 'sini sağlayın. Sonra, zaman aralığını ve sıklığı ayarlayarak tetikleyicinizin zamanlamasını tanımlayın.

   | Özellik | Değer | Açıklama |
   | -------- | ----- | ----------- |
   | **RSS akışının URL'si** | <*RSS-Feed-URL*> | İzlemek istediğiniz RSS akışı URL 'SI. Bu örnek, adresindeki duvar Caddesi günlüğü RSS akışını kullanır `https://feeds.a.dj.com/rss/RSSMarketsMain.xml` . Ancak, bu örneğin amaçları doğrultusunda, HTTP yetkilendirmesi gerektirmeyen herhangi bir RSS akışını kullanabilirsiniz. Sık yayımlanan bir RSS akışı seçin, böylece mantıksal uygulamanızı daha sonra kolayca test edebilirsiniz. |
   | **Aralık** | 1 | RSS akışı denetimleri arasında beklenecek Aralık sayısı. Bu örnekte 1 dakikalık aralıklar kullanılmaktadır. |
   | **Sıklık** | Dakika | RSS akışı denetimleri arasındaki her Aralık için zaman birimi. Bu örnekte 1 dakikalık aralıklar kullanılmaktadır. |
   ||||

   ![RSS URL 'SI, sıklık ve Aralık dahil olmak üzere RSS tetikleyici ayarları ile Logic Apps tasarımcısını gösteren ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

1. Kendi başlık çubuğunun içine tıklayarak tetikleyicinin ayrıntılarını şimdilik daraltın.

   ![Daraltılmış Logic Apps şekliyle Logic Apps tasarımcı gösteren ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Tasarımcı araç çubuğunda **Kaydet** ' i seçerek mantıksal uygulamanızı kaydedin.

Mantıksal uygulamanız artık canlı, ancak RSS akışını kontrol dışında bir şey yapmaz. Sonra, Tetikleyiciniz tetiklendiğinde ne olacağını tanımlamak için [bir eylem ekleyin](#add-email-action) .

<a name="add-email-action"></a>

## <a name="add-the-send-email-action"></a>"E-posta gönder" eylemini ekleyin

[Mantıksal uygulamanız için bir tetikleyici ekledikten](#add-rss-trigger)sonra MANTıKSAL uygulamanız RSS akışını denetlediğinde ve yeni bir öğe göründüğünde yanıtı belirleyecek bir [eylem](../logic-apps/logic-apps-overview.md#logic-app-concepts) eklemeniz gerekir. Ayrıca, [Logic Apps, Azure işlevleri ve Azure depolama ile e-postaları işlemeye](./tutorial-process-email-attachments-workflow.md)yönelik öğreticide olduğu gibi çok daha karmaşık eylemlerle de mantıksal uygulamalar oluşturabilirsiniz.

> [!NOTE]
> Bu örnekte e-posta hizmeti olarak Office 365 Outlook kullanılmaktadır. Mantıksal uygulamanızda desteklenen başka bir e-posta hizmeti kullanıyorsanız, Kullanıcı arabirimi farklı görünebilir. Ancak, başka bir e-posta hizmetine bağlanmak için temel kavramlar aynı kalır.

1. **Bir akış öğesi yayımlandığında** tetikleyicisi bölümünde **yeni adım**' ı seçin.

   ![Seçili düğmeye sahip bir iş akışını gösteren Logic Apps Tasarımcısı 'nın ekran görüntüsü, "yeni adım".](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. **Eylem Seç** ' in altında, arama kutusu ' nun altında **Tümü**' nü seçin.

1. Arama kutusuna, `send an email` Bu eylemi sunan bağlayıcıları bulabilmeniz için yazın. Eylemler listesini belirli bir uygulama veya hizmete göre filtrelemek için, önce bu uygulamayı veya hizmeti seçebilirsiniz.

   Örneğin, bir Microsoft iş veya okul hesabı kullanıyorsanız ve Office 365 Outlook 'u kullanmak istiyorsanız **office 365 Outlook**' u seçin. Ya da kişisel Microsoft hesabı kullanıyorsanız, Outlook.com ' yi seçebilirsiniz. Bu örnek, Office 365 Outlook ile devam eder:

   ![Seçili e-posta Bağlayıcısı, "Office 365 Outlook" ile eylem adımını gösteren Logic Apps Tasarımcısı 'nın ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   Artık kullanmak istediğiniz eylemi daha kolay bulabilir ve seçebilirsiniz, örneğin `send an email` :

   ![E-posta Bağlayıcısı için filtrelenmiş eylemlerin listesini gösteren Logic Apps Tasarımcısı 'nın ekran görüntüsü, "Office 365 Outlook".](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Seçtiğiniz e-posta Bağlayıcınız kimliğinizin kimliğini doğrulamanızı isterse, bu adımı şimdi tamamlayabilirsiniz. Bu örneğin çalışması için mantıksal uygulamanız ile e-posta hizmetiniz arasında bir bağlantı oluşturmanız gerekir. 

    > [!NOTE]
    > Bu örnekte, Office 365 Outlook Bağlayıcısı için el ile kimlik doğrulaması gösterilmektedir. Ancak, diğer bağlayıcılar farklı kimlik doğrulama türlerini destekleyebilir.
    > Ayrıca, kullanım durumunuza bağlı olarak mantıksal uygulamalarınızın kimlik doğrulamasını farklı yollarla işleyebilirsiniz. Örneğin, dağıtım için Azure Resource Manager şablonlarını kullan ' ı kullanırsanız, bağlantı ayrıntıları gibi sık değişen girişler üzerindeki güvenliği artırmak için parametreleştirebilirsiniz. Daha fazla bilgi için şu konulara bakın:
   > * [Dağıtım için şablon parametreleri](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [OAuth bağlantılarını yetkilendir](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Yönetilen kimliklerle erişimin kimliğini doğrulama](../logic-apps/create-managed-service-identity.md)
   > * [Mantıksal uygulama dağıtımı için bağlantı kimlik doğrulaması](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. **E-posta gönder** eyleminde, e-posta bildirimine hangi bilgilerin ekleneceğini belirtin.

   1. **Alıcı** kutusuna alıcının e-posta adresini girin. Bu örnek için e-posta adresinizi kullanın.

        > [!NOTE]
        > **Dinamik Içerik Ekle** **listesi, '** ın Içine tıkladığınızda ve Logic Apps tasarımcısında başka bazı giriş kutularında görünür. Bu örnek, daha sonraki bir adımda dinamik içerik kullanır. **Dinamik Içerik Ekle** listesi, önceki adımda bulunan ve geçerli eylem için giriş olarak kullanabileceğiniz tüm kullanılabilir çıktıları gösterir.

   1. **Konu** kutusuna e-posta bildirimin konusunu girin. Bu örnek için, sonundaki boş alana sahip aşağıdaki metni girin: `New RSS item: `

      !["Konu" özellik kutusunun içindeki "e-posta gönder" eylemini ve imleci gösteren Logic Apps tasarımcısının ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. **Dinamik Içerik Ekle** listesinden, **bir akış öğesi yayımlandığında** tetikleyiciden çıktı olan **akış başlığı**' nı seçin. E-posta bildirimidir RSS öğesinin başlığını almak için bu çıktıyı kullanır.

      ![Açık dinamik içerik listesi ve seçili çıkış, "akış başlığı" olan "konu" özellik kutusunun içindeki "e-posta gönder" eylemini ve imleci gösteren Logic Apps tasarımcısının ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > Dinamik içerik listesinde, **bir akış öğesi yayımlandığında bir** çıkış görünmüyorsa, eylem üstbilgisinin yanında, **daha fazla göster**' i seçin.
      > 
      > ![Açık dinamik içerik listesini gösteren ve tetikleyici için "daha fazla gör" seçilmiş Logic Apps tasarımcısının ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      İşlem tamamlandığında e-posta konusu şu örnekteki gibi görünür:

      !["E-posta gönder" eylemini ve dahil edilen "akış başlığı" özelliği ile örnek bir e-posta konusunu gösteren Logic Apps tasarımcısının ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Tasarımcı üzerinde "for each" döngüsü görünürse, dizi için bir belirteç seçtiniz; Örneğin, **Kategoriler-öğe** belirteci. Bu tür belirteçler için tasarımcı otomatik olarak ilgili belirtece başvuran eylemin etrafına bu döngüyü ekler. Bu şekilde mantıksal uygulamanız dizideki tüm öğeler için aynı eylemi gerçekleştirir. Döngüyü kaldırmak için döngünün başlık çubuğundaki **üç nokta** (**...**) simgesini seçin ve **Sil**' i seçin.

   1. **Gövde** kutusuna e-posta gövdesinin içeriğini girin. Bu örnekte, içerik her biri için açıklayıcı metin içeren üç özellik içerir: `Title:` , **akış başlığı** özelliği; `Date published:` , özellikte **Yayınlanan akış** ve `Link:` **birincil akış bağlantısı** özelliği. Bir düzenleme kutusuna boş satır eklemek için Shift + Enter tuşlarını kullanın.

      | Özellik | Açıklama |
      |----------|-------------|
      | **Akış başlığı** | Öğenin başlığı |
      | **Akış yayımlanma zamanı** | Öğenin yayımlandığı tarih ve saat |
      | **Birincil akış bağlantısı** | Öğenin URL'si |
      |||

      !["E-posta gönder" eylemini ve "Body" kutusunun içindeki seçili özellikleri gösteren Logic Apps tasarımcısının ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı menüsünde **Kaydet** ' i seçin.

Daha sonra [mantıksal uygulamanızın çalışıp çalışmadığını test](#test-logic-app)edin.

<a name="test-logic-app"></a>

## <a name="run-your-logic-app"></a>Mantıksal uygulamanızı çalıştırın

Örnek mantıksal uygulamanızı oluşturduktan sonra, iş akışınızın doğru şekilde yapılandırıldığını onaylayın. Mantıksal uygulamanın, belirtilen zamanlamaya göre RSS akışınızı denetlemesini bekleyebilirsiniz. Ya da, aşağıdaki ekran görüntüsünde gösterildiği gibi Logic Apps Tasarımcı araç çubuğunda **Çalıştır** ' ı seçerek mantıksal uygulamanızı el ile çalıştırabilirsiniz. 

RSS akışında yeni öğeler olduğunda mantıksal uygulamanız her yeni öğe için bir e-posta gönderir. Aksi takdirde, mantıksal uygulamanız RSS akışını yeniden denetlemek için bir sonraki aralığa kadar bekler. 

![Tasarımcı araç çubuğunda "Çalıştır" düğmesinin seçili olduğunu gösteren Logic Apps tasarımcısının ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

Aşağıdaki ekran görüntüsünde, bu örnek mantıksal uygulamadan örnek bir e-posta bildirimi gösterilmektedir. E-posta, tasarımcıda seçilen her RSS akışı öğesinin ayrıntılarını ve her biri için eklenen açıklayıcı metni içerir.

![Öğe başlığı, Yayımlanma tarihi ve bağlantısı ile yeni RSS Akış öğesi göründüğünde alınan örnek e-posta iletisini gösteren Outlook ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Mantıksal uygulamadan beklenen şekilde bildirim e-postaları almadıysanız:

* İletinin yanlış filtrelenme olasılığına karşı e-posta hesabınızın önemsiz veya istenmeyen posta klasörünü denetleyin.
* Kullandığınız RSS akışı, son zamanlanan veya el ile yapılan denetim öğesinden sonra öğeleri yayımladığından emin olun.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu örnek mantıksal uygulamayı test etmeyi tamamladıktan sonra, bu örnek için oluşturduğunuz kaynak grubunu silerek mantıksal uygulamayı ve ilgili kaynakları temizleyin.

> [!NOTE]
> [Bir mantıksal uygulamayı sildiğinizde](manage-logic-apps-with-azure-portal.md#delete-logic-apps), hiçbir yeni çalıştırma örneği oluşturulur. Devam eden ve bekleme durumunda olan tüm çalıştırmalar iptal edilir. Binlerce çalıştırma varsa iptal işleminin tamamlanması zaman alabilir.

1. Azure Arama kutusuna girin `resource groups` ve ardından **kaynak grupları**' nı seçin.

   ![Arama terimi "kaynak grupları" olan Azure portal arama kutusunu gösteren ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Mantıksal uygulamanızın kaynak grubunu bulun ve seçin. **Genel bakış** bölmesinde **kaynak grubunu sil**' i seçin.

   !["Kaynak grubunu sil" için seçili kaynak grubu ve düğmeyle birlikte Azure portal gösteren ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Onay bölmesi göründüğünde, kaynak grubu adını girin ve **Sil**' i seçin.

   ![Onay bölmesinin Azure portal ve silinecek kaynak grubu adının gösterildiği ekran görüntüsü.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir zamanlamaya göre güncelleştirmeler için RSS akışını denetlemek ve her yeni akış öğesi hakkında bir e-posta bildirimi göndermek için Azure portal ilk mantıksal uygulamanızı oluşturdunuz. 

Logic Apps daha gelişmiş zamanlama tabanlı iş akışları oluşturmayı öğrenmek için aşağıdaki öğreticiye bakın:

> [!div class="nextstepaction"]
> [Zamanlama tabanlı mantıksal uygulama ile trafiği denetleme](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)