---
title: Görevleri Visual Studio Code otomatikleştirin Azure Logic Apps
description: Visual Studio Code kullanarak mantıksal uygulama temel alınan JSON tanımlarını oluşturma veya düzenleme (VS Code)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, deli, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/25/2019
ms.openlocfilehash: 96551d3c7f0d37bc7ed8696666b130fa83dae41b
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063502"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>Hızlı başlangıç: Visual Studio Code kullanarak mantıksal uygulama iş akışı tanımlarını oluşturma ve yönetme

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve Visual Studio Code sayesinde, kuruluşlar ve kuruluşlar genelinde uygulamaları, verileri, sistemleri ve Hizmetleri tümleştirmek için görevleri, iş akışlarını ve işlemleri otomatikleştirmenize yardımcı olan Logic Apps oluşturabilir ve yönetebilirsiniz. Bu hızlı başlangıçta, kod tabanlı bir deneyim aracılığıyla Logic Apps için JavaScript Nesne Gösterimi (JSON) kullanan temel alınan iş akışı tanımlarını nasıl oluşturabileceğiniz ve düzenleyebileceği gösterilmektedir. Ayrıca, Azure 'a zaten dağıtılan mevcut Logic Apps üzerinde de çalışabilirsiniz.

Aynı görevleri [Azure Portal](https://portal.azure.com) ve Visual Studio 'da gerçekleştirebilmenize karşın, Logic App tanımlarını zaten öğreniyor ve doğrudan kodda çalışmak istediğinizde Visual Studio Code daha hızlı bir şekilde çalışmaya başlayın. Örneğin, önceden oluşturulmuş mantıksal uygulamaları devre dışı bırakabilir, etkinleştirebilir, silebilir ve yenileyebilirsiniz. Ayrıca, Linux, Windows ve Mac gibi Visual Studio Code çalıştığı tüm geliştirme platformlarından Logic Apps ve tümleştirme hesapları üzerinde çalışabilirsiniz.

Bu makalede, temel kavramlara daha fazla odaklanan olan bu [hızlı](../logic-apps/quickstart-create-first-logic-app-workflow.md)başlangıçta aynı mantıksal uygulamayı oluşturabilirsiniz. Visual Studio Code, mantıksal uygulama şu örneğe benzer şekilde görünür:

![Örnek mantıksal uygulama iş akışı tanımı](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

Başlamadan önce şunlara sahip olduğunuzdan emin olun:

* Azure hesabınız ve aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* [Mantıksal uygulama iş akışı tanımları](../logic-apps/logic-apps-workflow-definition-language.md) ve JSON ile açıklandığı şekilde yapısı hakkında temel bilgi

  Logic Apps yeni başladıysanız, Azure portal ilk mantıksal uygulamalarınızı oluşturan ve temel kavramlara odaklanmakta olan bu [hızlı](../logic-apps/quickstart-create-first-logic-app-workflow.md)başlangıcı deneyin.

* Azure 'da ve Azure aboneliğinizde oturum açmak için Web 'e erişin

* Henüz yoksa şu araçları indirip yükleyin:

  * [Visual Studio Code sürüm 1.25.1 veya üzeri](https://code.visualstudio.com/), ücretsiz

  * Azure Logic Apps için Visual Studio Code uzantısı

    Bu uzantıyı [Visual Studio Market](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) indirebilir ve doğrudan Visual Studio Code içinden yükleyebilirsiniz. Yükledikten sonra Visual Studio Code yeniden yüklediğinizden emin olun.

    !["Azure Logic Apps için Visual Studio Code uzantısını bul"](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Uzantının doğru şekilde yüklenip yüklenmediğini denetlemek için Visual Studio Code araç çubuğinizdeki görünen Azure simgesini seçin.

    ![Uzantının doğru yüklendiğini onaylayın](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    Daha fazla bilgi için bkz. [uzantı marketi](https://code.visualstudio.com/docs/editor/extension-gallery). Bu uzantının açık kaynaklı sürümüne katkıda bulunmak için [GitHub 'daki Visual Studio Code Azure Logic Apps uzantısını](https://github.com/Microsoft/vscode-azurelogicapps)ziyaret edin.

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Azure'da oturum açın

1. Visual Studio Code'u açın. Visual Studio Code araç çubuğunda Azure simgesini seçin.

   ![Visual Studio Code araç çubuğunda Azure simgesini seçin](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. Azure penceresinde, **Logic Apps**altında **Azure 'da oturum aç**' ı seçin. Microsoft oturum açma sayfası sizi isterse, Azure hesabınızla oturum açın.

   !["Azure 'da oturum aç" ı seçin](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. Oturum açma normalden uzun sürerse Visual Studio Code, size bir cihaz kodu sağlayarak bir Microsoft kimlik doğrulama Web sitesinde oturum açmanızı ister. Bunun yerine kodla oturum açmak için **Cihaz kodunu kullan**' ı seçin.

      ![Bunun yerine cihaz koduyla devam et](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. Kodu kopyalamak için **kopyala & aç**' ı seçin.

      ![Azure oturum açma için kodu kopyalayın](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. Yeni bir tarayıcı penceresi açmak ve kimlik doğrulama Web sitesine devam etmek için **bağlantıyı aç**' ı seçin.

      ![Bir tarayıcı açmayı ve kimlik doğrulama Web sitesine giderek onaylayın](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. **Hesabınızda oturum açın** sayfasında, kimlik doğrulama kodunuzu girin ve **İleri**' yi seçin.

      ![Azure oturum açma için kimlik doğrulama kodunu girin](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Azure hesabınızı seçin. Oturum açtıktan sonra tarayıcınızı kapatabilir ve Visual Studio Code geri dönebilirsiniz.

   Azure bölmesinde **Logic Apps** ve **tümleştirme hesapları** bölümleri artık hesabınızla ilişkili Azure aboneliklerini gösterir. Ancak, istediğiniz abonelikleri görmüyorsanız veya bölümler çok fazla abonelik gösterdiğinde, şu adımları izleyin:

   1. İşaretçinizi **Logic Apps** etiketinin üzerine taşıyın. Araç çubuğu göründüğünde, **abonelikleri Seç** (filtre simgesi) öğesini seçin.

      ![Azure abonelikleri bulma veya filtreleme](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. Görüntülenen listeden, görünmesini istediğiniz abonelikleri seçin.

1. **Logic Apps**altında istediğiniz aboneliği seçin. Abonelik düğümü genişletilir ve bu abonelikte bulunan tüm mantıksal uygulamaları gösterir.

   ![Azure aboneliğinizi seçin](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > **Tümleştirme hesapları**altında, aboneliğinizi seçtiğinizde söz konusu abonelikte bulunan tümleştirme hesapları gösterilir.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>Yeni mantıksal uygulama oluştur

1. Azure hesabınızda ve aboneliğinizden Visual Studio Code içinden henüz oturum açmadıysanız, [önceki adımları izleyerek hemen oturum açın](#sign-in-azure).

1. Visual Studio Code ' un altında, **Logic Apps**altında, aboneliğinizin kısayol menüsünü açın ve **mantıksal uygulama oluştur**' u seçin.

   ![Abonelik menüsünden "mantıksal uygulama oluştur" u seçin](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   Bir liste görünür ve aboneliğinizdeki tüm Azure kaynak gruplarını gösterir.

1. Kaynak grubu listesinden, **Yeni bir kaynak grubu** veya var olan bir kaynak grubu oluştur ' u seçin. Bu örnek için yeni bir kaynak grubu oluşturun.

   ![Yeni bir Azure kaynak grubu oluşturma](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Azure Kaynak grubunuz için bir ad girin ve ENTER tuşuna basın.

   ![Azure Kaynak grubunuz için ad belirtin](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Mantıksal uygulamanızın meta verilerini kaydetmek istediğiniz Azure bölgesini seçin.

   ![Mantıksal uygulama meta verilerini kaydetmek için Azure konumunu seçin](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Mantıksal uygulamanız için bir ad girin ve ENTER tuşuna basın.

   ![Mantıksal uygulamanız için ad belirtin](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Azure penceresinde, Azure aboneliğiniz altında yeni ve boş mantıksal uygulamanız görüntülenir. Visual Studio Code ayrıca mantıksal uygulamanız için bir iskelet iş akışı tanımı içeren bir JSON (. logicapp. JSON) dosyası açar. Artık mantıksal uygulamanızın iş akışı tanımını bu JSON dosyasında el ile yazmaya başlayabilirsiniz. Bir iş akışı tanımının yapısı ve sözdizimi hakkında teknik başvuru için, [Azure Logic Apps Için Iş akışı Tanım Dili şemasına](../logic-apps/logic-apps-workflow-definition-language.md)bakın.

   ![Boş mantıksal uygulama iş akışı tanımı JSON dosyası](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   Örneğin, bir RSS tetikleyicisi ve Office 365 Outlook eylemiyle başlayan örnek bir mantıksal uygulama iş akışı tanımı aşağıda verilmiştir. Genellikle, JSON öğeleri her bölümde alfabetik olarak görünür. Ancak bu örnek, bu öğeleri kabaca, mantıksal uygulamanın adımlarının tasarımcıda göründüğü sırayla gösterir.

   > [!IMPORTANT]
   > Bu örnek mantıksal uygulama tanımını yeniden kullanmak istiyorsanız, bir Office 365 Kurumsal hesabına ihtiyacınız vardır, örneğin @fabrikam.com. Kurgusal e-posta adresini kendi e-posta adresiniz ile değiştirdiğinizden emin olun. Outlook.com veya Gmail gibi farklı bir e-posta Bağlayıcısı kullanmak için `Send_an_email_action` eylemini [Azure Logic Apps tarafından desteklenen bir e-posta bağlayıcısından](../connectors/apis-list.md)erişilebilen benzer bir eylemle değiştirin.

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email_(V2)": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "<p>Title: @{triggerBody()?['title']}<br>\n<br>\nDate published: @{triggerBody()?['updatedOn']}<br>\n<br>\nLink: @{triggerBody()?['primaryLink']}</p>",
                  "Subject": "RSS item: @{triggerBody()?['title']}",
                  "To": "sophia-owen@fabrikam.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/v2/Mail"
            }
         }
      },
      "outputs": {}
   }
   ```

1. İşiniz bittiğinde, mantıksal uygulamanızın iş akışı tanımını kaydedin. (Dosya menüsü > Kaydet veya CTRL + S tuşlarına basın)

1. Mantıksal uygulamanızı Azure aboneliğinize yüklemeniz istendiğinde **karşıya yükle**' yi seçin.

   Bu adım, mantıksal uygulamanızı [Azure Portal](https://portal.azure.com)yayımlar, bu, mantığınızı Azure 'da canlı ve çalışır hale getirir.

   ![Yeni mantıksal uygulamayı Azure aboneliğinize yükleme](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>Tasarımcıda mantıksal uygulamayı görüntüleme

Visual Studio Code, mantıksal uygulamanızı salt okuma Tasarım görünümünde açabilirsiniz. Mantıksal uygulamanızı tasarımcıda düzenleyemeseniz de, tasarımcı görünümünü kullanarak mantıksal uygulamanızın iş akışını görsel olarak kontrol edebilirsiniz.

Azure penceresinde, **Logic Apps**altında, mantıksal uygulamanızın kısayol menüsünü açın ve **tasarımcıda aç**' ı seçin.

Salt okuma Tasarımcısı ayrı bir pencerede açılır ve mantıksal uygulamanızın iş akışını gösterir, örneğin:

![Salt okuma tasarımcısında mantıksal uygulamayı görüntüleme](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Azure portal içinde görüntüle

Azure portal ' de mantıksal uygulamanızı gözden geçirmek için şu adımları izleyin:

1. Mantıksal uygulamanızla ilişkili aynı Azure hesabı ve aboneliği kullanarak [Azure Portal](https://portal.azure.com) oturum açın.

1. Azure portal arama kutusuna mantıksal uygulamalarınızın adını girin. Sonuçlar listesinden mantıksal uygulamanızı seçin.

   ![Yeni mantıksal uygulamanız Azure portal](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>Mantıksal uygulamayı devre dışı bırakma veya etkinleştirme

Visual Studio Code, yayımlanmış bir mantıksal uygulamayı düzenlerseniz ve değişikliklerinizi kaydederseniz, önceden dağıtılmış uygulamanızın *üzerine yazarsınız* . Mantıksal uygulamanızı üretimde bozmamak ve kesintiyi en aza indirmek için öncelikle mantıksal uygulamanızı devre dışı bırakın. Mantıksal uygulamanızı hala işe yarar doğruladıktan sonra mantıksal uygulamanızı yeniden etkinleştirebilirsiniz.

1. Azure hesabınızda ve aboneliğinizden Visual Studio Code içinden henüz oturum açmadıysanız, [önceki adımları izleyerek hemen oturum açın](#sign-in-azure).

1. Azure penceresinde, **Logic Apps**altında, Bu abonelikteki tüm mantıksal uygulamaları görüntüleyebilmeniz için Azure aboneliğinizi genişletin.

   1. İstediğiniz mantıksal uygulamayı devre dışı bırakmak için mantıksal uygulamanın menüsünü açın ve **devre dışı bırak**' ı seçin.

      ![Mantıksal uygulamanızı devre dışı bırakma](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. Mantıksal uygulamanızı yeniden etkinleştirmeye hazırsanız, mantıksal uygulamanın menüsünü açın ve **Etkinleştir**' i seçin.

      ![Mantıksal uygulamanızı etkinleştirme](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>Dağıtılan mantıksal uygulamayı Düzenle

Visual Studio Code, Azure 'da zaten dağıtılmış bir mantıksal uygulama için iş akışı tanımını açabilir ve düzenleyebilirsiniz.

> [!IMPORTANT] 
> Üretim ortamında etkin bir şekilde çalışan bir mantıksal uygulamayı düzenlemeden önce, mantıksal uygulamayı bozma ve [öncelikle mantıksal uygulamanızı devre dışı bırakarak](#disable-enable-logic-app)kesintiyi en aza indirme riskinden kaçının.

1. Azure hesabınızda ve aboneliğinizden Visual Studio Code içinden henüz oturum açmadıysanız, [önceki adımları izleyerek hemen oturum açın](#sign-in-azure).

1. Azure penceresinde, **Logic Apps**altında Azure aboneliğinizi genişletin ve istediğiniz mantıksal uygulamayı seçin.

1. Mantıksal uygulamanızın menüsünü açın ve **düzenleyicide aç**' ı seçin. Ya da mantıksal uygulamanızın adının yanında Düzenle simgesini seçin.

   ![Mevcut mantıksal uygulama için düzenleyiciyi aç](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code mantıksal uygulamanızın iş akışı tanımını görüntüleyebilmeniz için yerel geçici klasörünüzde. logicapp. json dosyasını açar.

   ![Yayımlanan mantıksal uygulama için iş akışı tanımını görüntüle](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. Mantıksal uygulamanın iş akışı tanımında yaptığınız değişiklikleri yapın.

1. İşiniz bittiğinde yaptığınız değişiklikleri kaydedin. (Dosya menüsü > Kaydet veya CTRL + S tuşlarına basın)

1. Değişikliklerinizi karşıya yüklemeniz ve Azure portal mevcut mantıksal uygulamanızın *üzerine yazmak* isteyip Istemediğiniz sorulduğunda **karşıya yükle**' yi seçin.

   Bu adım, güncelleştirmelerinizi [Azure Portal](https://portal.azure.com)mantıksal uygulamanıza yayımlar.

   ![Azure 'da Logic App tanımına yapılan düzenlemeleri karşıya yükleme](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>Diğer sürümleri görüntüleyin veya yükseltin

Visual Studio Code, mantıksal uygulamanız için önceki sürümleri açabilir ve gözden geçirebilirsiniz. Ayrıca, önceki bir sürümü güncel sürüme de yükseltebilirsiniz.

> [!IMPORTANT] 
> Üretim ortamında etkin bir şekilde çalışan bir mantıksal uygulamayı değiştirmeden önce, mantıksal uygulamayı bozma ve [öncelikle mantıksal uygulamanızı devre dışı bırakarak](#disable-enable-logic-app)kesintiyi en aza indirme riskinden kaçının.

1. Azure penceresinde, **Logic Apps**altında, Bu abonelikteki tüm mantıksal uygulamaları görüntüleyebilmeniz için Azure aboneliğinizi genişletin.

1. Aboneliğiniz altında mantıksal uygulamanızı genişletin ve **sürümler**' i genişletin.

   **Sürümler** listesi, varsa mantıksal uygulamanızın önceki sürümlerini gösterir.

   ![Mantıksal uygulamanızın önceki sürümleri](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. Önceki bir sürümü görüntülemek için, iki adımdan birini seçin:

   * JSON tanımını görüntülemek için, **sürümler**altında, bu tanım için sürüm numarasını seçin. Ya da, söz konusu sürümün kısayol menüsünü açın ve **düzenleyicide aç**' ı seçin.

     Yerel bilgisayarınızda yeni bir dosya açılır ve bu sürümün JSON tanımını gösterir.

   * Sürümü salt okuma tasarımcı görünümünde görüntülemek için, bu sürümün kısayol menüsünü açın ve **tasarımcıda aç**' ı seçin.

1. Önceki bir sürümü güncel sürüme yükseltmek için aşağıdaki adımları izleyin:

   1. **Sürümler**altında, önceki sürümün kısayol menüsünü açın ve **Yükselt**' i seçin.

      ![Önceki sürümü yükselt](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Visual Studio Code onay istemini tamamladıktan sonra devam etmek için **Evet**' i seçin.

      ![Önceki sürümü yükseltme Onayla](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      Visual Studio Code seçili sürümü geçerli sürüme yükseltir ve yükseltilen sürüme yeni bir sayı atar. Daha önce geçerli sürüm, yükseltilen sürüm altında görüntülenir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Visual Studio ile mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)