---
title: Birden çok Azure hizmeti ile görevleri otomatikleştirme-Azure Logic Apps
description: Öğretici-e-postaları Azure Logic Apps, Azure depolama ve Azure Işlevleri ile işlemek için otomatik iş akışları oluşturma
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/20/2019
ms.openlocfilehash: 52c9a23e3e00075e934b9f9f22a835090e02f1b9
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820215"
---
# <a name="tutorial-automate-tasks-to-process-emails-by-using-azure-logic-apps-azure-functions-and-azure-storage"></a>Öğretici: Azure Logic Apps, Azure Işlevleri ve Azure Storage kullanarak e-postaları işlemek için görevleri otomatikleştirme

Azure Logic Apps, Azure hizmetleri, Microsoft hizmetleri ve diğer hizmet olarak yazılım (SaaS) uygulamalarının yanı sıra şirket içi sistemler üzerindeki verileri tümleştirmenize ve iş akışlarını otomatikleştirmenize yardımcı olur. Bu öğretici, gelen e-postaları ve ekleri işleyen bir [mantıksal uygulamayı](../logic-apps/logic-apps-overview.md) nasıl oluşturabileceğinizi gösterir. Bu mantıksal uygulama, e-posta içeriğini analiz eder, içeriği Azure depolama 'ya kaydeder ve bu içeriği gözden geçirmeye yönelik bildirimler gönderir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Kayıtlı e-postaları ve ekleri denetlemek için [Azure Depolama](../storage/common/storage-introduction.md)’yı ve Depolama Gezgini’ni ayarlama.
> * E-postalardan HTML’yi kaldıran bir [Azure işlevi](../azure-functions/functions-overview.md) oluşturma. Bu öğretici, bu işlev için kullanabileceğiniz kodu içerir.
> * Boş bir mantıksal uygulama oluşturma.
> * E-postalardaki ekleri izleyen bir tetikleyici ekleme.
> * E-postalarda ek olup olmadığını denetleyen bir koşul ekleme.
> * Bir e-posta ek içerdiğinde Azure işlevini çağıran bir eylem ekleme.
> * E-postalar ve ekler için depolama blobları oluşturan bir eylem ekleme.
> * E-posta bildirimleri gönderen bir eylem ekleme.

İşlemi tamamladığınızda, mantıksal uygulamanız bu yüksek düzeyli iş akışı gibi görünür:

![Üst düzey tamamlanmış mantıksal uygulama](./media/tutorial-process-email-attachments-workflow/overview.png)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Logic Apps tarafından desteklenen Office 365 Outlook, Outlook.com veya Gmail gibi bir e-posta sağlayıcıdan alınmış e-posta hesabı. Diğer sağlayıcılar için [buradaki bağlayıcı listesini inceleyin](https://docs.microsoft.com/connectors/).

  Bu mantıksal uygulama bir Office 365 Outlook hesabı kullanır. Farklı bir e-posta hesabı kullanırsanız genel adımlar aynı kalır, ancak kullanıcı arabiriminiz biraz farklı görünebilir.

* [Ücretsiz Microsoft Azure Depolama Gezgini](https://storageexplorer.com/)’ni indirip yükleyin. Bu araç, depolama kapsayıcınızın doğru şekilde ayarlanıp ayarlanmadığını denetlemenize yardımcı olur.

## <a name="sign-in-to-azure-portal"></a>Azure portalında oturum açın

Azure hesabınızın kimlik bilgileriyle [Azure portalında](https://portal.azure.com) oturum açın.

## <a name="set-up-storage-to-save-attachments"></a>Ekleri kaydetmek için depolamayı ayarlama

Gelen e-postaları ve ekleri, [Azure depolama kapsayıcısında](../storage/common/storage-introduction.md) bloblar olarak kaydedebilirsiniz.

1. Bir depolama kapsayıcısı oluşturabilmeniz için, Azure portal **temel bilgiler** sekmesinde bu ayarlarla [bir depolama hesabı oluşturun](../storage/common/storage-quickstart-create-account.md) :

   | Ayar | Değer | Açıklama |
   |---------|-------|-------------|
   | **Abonelik** | <*Azure-subscription-name*> | Azure aboneliğinizin adı |  
   | **Kaynak grubu** | <*Azure-Resource-group*> | İlgili kaynakları düzenlemek ve yönetmek için kullanılan [Azure kaynak grubunun](../azure-resource-manager/resource-group-overview.md) adı. Bu örnekte "LA-öğretici-RG" kullanılmaktadır. <p>**Not:** Bir kaynak grubu, belirli bir bölgenin içinde bulunur. Bu öğreticideki öğeler tüm bölgelerde kullanılamasa da mümkün olduğunca aynı bölgeyi kullanmayı deneyin. |
   | **Depolama hesabı adı** | <*Azure-depolama hesabı-adı* > | Depolama hesabınızın adı, 3-24 karakter içermeli ve yalnızca küçük harf ve rakam içermelidir. Bu örnek, "attachmentstorageacct" kullanır. |
   | **Konum** | <*Azure-bölge*> | Depolama hesabınızla ilgili bilgilerin depolanacağı bölge. Bu örnek, "Batı ABD" kullanır. |
   | **Performans** | Standart | Bu ayar, verileri depolamaya ilişkin medyayı ve desteklenen veri türlerini belirtir. Bkz. [Depolama hesabı türleri](../storage/common/storage-introduction.md#types-of-storage-accounts). |
   | **Hesap türü** | Genel amaç | [Depolama hesabı türü](../storage/common/storage-introduction.md#types-of-storage-accounts) |
   | **Çoğaltma** | Yerel olarak yedekli depolama (LRS) | Bu ayar, verilerinizin nasıl kopyalandığını, depolandığını, yönetildiğini ve eşitlendiğini belirtir. Bkz. [yerel olarak yedekli depolama (LRS): Azure depolama Için düşük maliyetli veri artıklığı](../storage/common/storage-redundancy-lrs.md). |
   | **Erişim katmanı (varsayılan)** | Geçerli ayarı koru. |
   ||||

   **Gelişmiş** sekmesinde, bu ayarı seçin:

   | Ayar | Değer | Açıklama |
   |---------|-------|-------------|
   | **Güvenli aktarım gerekir** | Devre dışı | Bu ayar, bağlantılardan gelen istekler için gerekli güvenliği belirtir. Bkz. [Güvenli aktarım gerektir](../storage/common/storage-require-secure-transfer.md). |
   ||||

   Depolama hesabınızı oluşturmak için [Azure PowerShell](../storage/common/storage-quickstart-create-storage-account-powershell.md) veya [Azure CLI](../storage/common/storage-quickstart-create-storage-account-cli.md) uygulamalarını da kullanabilirsiniz.

1. İşiniz bittiğinde, **gözden geçir + oluştur**' u seçin.

1. Azure depolama hesabınızı dağıtduktan sonra depolama hesabınızı bulun ve depolama hesabının erişim anahtarını alın:

   1. Depolama hesabı menünüzde **Ayarlar** bölümünde **Erişim anahtarları**’nı seçin.

   1. Depolama hesabı adınızı ve **KEY1**kopyalayın ve bu değerleri güvenli bir yere kaydedin.

      ![Depolama hesabı adını ve anahtarını kopyalayıp kaydedin](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   Depolama hesabınızın erişim anahtarını almak için [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) veya [Azure CLI](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest.md#az-storage-account-keys-list) uygulamalarını da kullanabilirsiniz.

1. E-posta ekleriniz için bir blob depolama kapsayıcısı oluşturun.

   1. Depolama hesabı menüsünde **Genel bakış**'ı seçin. **Hizmetler**' ın altında **kapsayıcılar**' ı seçin.

      ![Blob depolama kapsayıcısı ekleme](./media/tutorial-process-email-attachments-workflow/create-storage-container.png)

   1. **Kapsayıcılar** sayfası açıldıktan sonra araç çubuğunda **Kapsayıcı**'yı seçin.

   1. **Yeni kapsayıcı**altında, kapsayıcı adınız olarak `attachments` girin. **Genel erişim düzeyi**altında **kapsayıcı (kapsayıcılar ve Bloblar için anonim okuma erişimi)** seçeneğini belirleyin > **Tamam**' ı seçin.

      İşiniz bittiğinde, Azure portalında depolama hesabınızda depolama kapsayıcısını bulabilirsiniz:

      ![Tamamlanan depolama kapsayıcısı](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

   Bir depolama kapsayıcısı oluşturmak için [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstoragecontainer) veya [Azure CLI](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create)'yi de kullanabilirsiniz.

Sonra, Depolama Gezgini’ni depolama hesabınıza bağlayın.

## <a name="set-up-storage-explorer"></a>Depolama Gezgini’ni ayarlama

Şimdi, mantıksal uygulamanızın ekleri depolama kapsayıcınızda bloblar olarak doğru şekilde kaydettiğini onaylayabilmeniz için Depolama Gezgini’ni depolama hesabınıza bağlayın.

1. Microsoft Azure Depolama Gezgini başlatın.

   Depolama Gezgini, depolama hesabınızla bağlantı kurmanızı ister.

1. **Azure depolama 'Ya Bağlan** bölmesinde, **bir depolama hesabı adı ve anahtar > kullan** ' ı seçin.

   ![Depolama Gezgini - Depolama hesabına bağlanma](./media/tutorial-process-email-attachments-workflow/storage-explorer-choose-storage-account.png)

   > [!TIP]
   > Hiçbir istem görünürse, Depolama Gezgini araç çubuğunda **Hesap Ekle**' yi seçin.

1. **Görünen ad**' ın altında, bağlantınız için bir kolay ad sağlayın. **Hesap adı** bölümüne depolama hesabınızın adını girin. **Hesap anahtarı**altında, daha önce kaydettiğiniz erişim anahtarını girip **İleri**' yi seçin.

1. Bağlantı bilgilerinizi onaylayın ve sonra **Bağlan**' ı seçin.

   Depolama Gezgini bağlantı oluşturur ve **yerel & eklenmiş** > **depolama hesapları**altındaki Gezgin penceresinde depolama hesabınızı gösterir.

1. BLOB depolama kapsayıcınızı bulmak için, **depolama hesapları**altında, burada **attachmentstorageacct** olan depolama hesabınızı genişletin ve **ekler** kapsayıcısını bulduğunuz **BLOB kapsayıcılarını** genişletin, örneğin:

   ![Depolama Gezgini - depolama kapsayıcısını bulma](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

Sonra, gelen e-postadan HTML’yi kaldıran bir [Azure işlevi](../azure-functions/functions-overview.md) oluşturun.

## <a name="create-function-to-clean-html"></a>HTML’yi temizleme işlevini oluşturma

Şimdi gelen her bir e-postadan HTML’yi kaldıran Azure işlevi oluşturmak için bu adımlar tarafından sağlanan kod parçacığını kullanın. Böylece e-posta içeriği daha net ve işlemesi daha kolaydır. Sonra, mantıksal uygulamanızdan bu işlevi çağırabilirsiniz.

1. Bir işlev oluşturabilmeniz için önce şu ayarlarla [bir işlev uygulaması oluşturun](../azure-functions/functions-create-function-app-portal.md):

   | Ayar | Değer | Açıklama |
   | ------- | ----- | ----------- |
   | **Uygulama adı** | <*işlevi-uygulama adı* > | İşlev uygulamanızın adı, Azure genelinde genel olarak benzersiz olmalıdır. Bu örnek zaten "CleanTextFunctionApp" kullanıyor, bu nedenle "MyCleanTextFunctionApp-< *-name*>" gibi farklı bir ad sağlayın |
   | **Abonelik** | <*your-Azure-subscription-name*> | Daha önce kullandığınız Azure aboneliği |
   | **Kaynak Grubu** | LA-Tutorial-RG | Daha önce kullandığınız Azure kaynak grubu |
   | **OS** | *işletim sisteminizi* <> | En sevdiğiniz işlev programlama dilinizi destekleyen işletim sistemini seçin. Bu örnek için **Windows**' u seçin. |
   | **Barındırma Planı** | Tüketim Planı | Bu ayar, işlev uygulamanızı çalıştırmak için bilgi işlem gücü gibi kaynakların nasıl ayrılacağını ve ölçekleneceğini belirler. Bkz. [barındırma planları karşılaştırması](../azure-functions/functions-scale.md). |
   | **Konum** | Batı ABD | Daha önce kullandığınız bölge |
   | **Çalışma zamanı yığını** | Tercih edilen dil | En sevdiğiniz işlev programlama dilinizi destekleyen bir çalışma zamanı seçin. Ve işlevleri için C# .net F# ' i seçin. |
   | **Depolama** | cleantextfunctionstorageacct | İşlev uygulamanız için bir depolama hesabı oluşturun. Yalnızca küçük harfleri ve rakamları kullanın. <p>**Note:** Bu depolama hesabı, işlev uygulamalarınızı içerir ve e-posta ekleri için önceden oluşturulmuş depolama hesabınızdan farklıdır. |
   | **Application Insights** | Devre dışı bırakma | [Application Insights](../azure-monitor/app/app-insights-overview.md)ile uygulama izlemeyi etkinleştirir, ancak bu öğretici Için **devre dışı bırak** > **Uygula**' yı seçin. |
   ||||

   İşlev uygulamanız dağıtımdan sonra otomatik olarak açılmazsa, [Azure Portal](https://portal.azure.com) arama kutusunda **işlev uygulaması**bulun ve seçin. **İşlev uygulaması**altında, işlev uygulamanızı seçin.

   ![İşlev uygulaması seçme](./media/tutorial-process-email-attachments-workflow/select-function-app.png)

   Aksi halde Azure burada gösterilen şekilde işlev uygulamanızı otomatik olarak açar:

   ![Oluşturulan işlev uygulaması](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Bir işlev uygulaması oluşturmak için [Azure CLI](../azure-functions/functions-create-first-azure-function-azure-cli.md)veya [PowerShell ve Kaynak Yöneticisi şablonlarını](../azure-resource-manager/resource-group-template-deploy.md)da kullanabilirsiniz.

1. **Işlev uygulamaları** listesinde, henüz genişletilmemişse, işlev uygulamanızı genişletin. İşlev uygulamanız altında **işlevler**' i seçin. İşlevler araç çubuğunda **Yeni işlev**’i seçin.

   ![Yeni işlev oluşturma](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

1. **Aşağıdan bir şablon seçin veya hızlı başlangıç ' a gidin**, **http tetikleyici** şablonunu seçin.

   ![HTTP tetikleyici şablonu seç](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

   Azure, HTTP ile tetiklenen bir işlev için dile özgü bir şablon kullanarak bir işlev oluşturur.

1. **Yeni İşlev** bölmesinin **Ad** alanına `RemoveHTMLFunction` girin. **Yetkilendirme düzeyini** **işlev**olarak ayarlayın ve **Oluştur**' u seçin.

   ![İşlevinizi adlandırma](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

1. Düzenleyici açıldıktan sonra şablon kodunu bu örnek kodla değiştirin; böylece HTML kaldırılır ve sonuçlar çağırana döndürülür:

   ```CSharp
   #r "Newtonsoft.Json"

   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   using Newtonsoft.Json;
   using System.Text.RegularExpressions;

   public static async Task<IActionResult> Run(HttpRequest req, ILogger log) {

      log.LogInformation("HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await new StreamReader(req.Body).ReadToEndAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return (ActionResult)new OkObjectResult(new { updatedBody });
   }
   ```

1. İşiniz bittiğinde **Kaydet**'i seçin. İşlevinizi test etmek için, düzenleyicinin sağ kenarında, ok ( **<** ) simgesinin altında **Test**' i seçin.

   !["Test" bölmesini açma](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

1. **Test** bölmesinde, **İstek gövdesi**altında, bu satırı girin ve **Çalıştır**' ı seçin.

   `{"name": "<p><p>Testing my function</br></p></p>"}`

   ![İşlevinizi sınama](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   **Çıkış** penceresi, işlevin sonucunu gösterir:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

İşlevinizin çalıştığını denetledikten sonra mantıksal uygulamanızı oluşturun. Bu öğretici, e-postalardan HTML’yi kaldıran bir işlevin nasıl oluşturulacağını gösterse de, Logic Apps’in bir **HTML - Metin** bağlayıcısı da sunar.

## <a name="create-your-logic-app"></a>Mantıksal uygulamanızı oluşturma

1. Azure giriş sayfasında, arama kutusunda **Logic Apps**bulun ve seçin.

   !["Logic Apps" bul ve Seç](./media/tutorial-process-email-attachments-workflow/find-select-logic-apps.png)

1. **Logic Apps** sayfasında **Ekle**' yi seçin.

   ![Yeni mantıksal uygulama ekle](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. **Mantıksal uygulama oluştur** bölümünde, mantıksal uygulamanızın ayrıntılarını burada gösterildiği gibi sağlayın. İşiniz bittiğinde **Oluştur**' u seçin.

   ![Mantıksal uygulama bilgilerini sağlama](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Ayar | Değer | Açıklama |
   | ------- | ----- | ----------- |
   | **Adı** | LA-ProcessAttachment | Mantıksal uygulamanızın adı |
   | **Abonelik** | <*your-Azure-subscription-name*> | Daha önce kullandığınız Azure aboneliği |
   | **Kaynak grubu** | LA-Tutorial-RG | Daha önce kullandığınız Azure kaynak grubu |
   | **Konum** | Batı ABD | Daha önce kullandığınız bölge |
   | **Log Analytics** | Kapalı | Bu öğretici için **kapalı** ayarını seçin. |
   ||||

1. Azure 'u uygulamanızı dağıtduktan sonra, Azure araç çubuğunda Bildirimler simgesini seçin ve **Kaynağa Git**' i seçin.

   ![Azure bildirimleri listesinden "kaynağa git" i seçin](./media/tutorial-process-email-attachments-workflow/go-to-new-logic-app-resource.png)

1. Logic Apps tasarımcı açıldıktan sonra ortak mantıksal uygulama desenleri için tanıtım videosu ve şablonları içeren bir sayfa gösterir. **Şablonlar** altında **Boş Mantıksal Uygulama**'yı seçin.

   ![Boş mantıksal uygulama şablonu seçin](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Sonra ek içeren gelen e-postaları dinleyen bir [tetikleyici](../logic-apps/logic-apps-overview.md#logic-app-concepts) ekleyin. Her mantıksal uygulama, belirli bir olay gerçekleştiğinde veya yeni veriler belirli bir koşulu karşıladığında tetiklenen bir tetikleyiciyle başlamalıdır. Daha fazla bilgi için bkz. [İlk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="monitor-incoming-email"></a>Gelen e-postayı izleme

1. Arama kutusuna tasarımcıda filtreniz olarak `when new email arrives` girin. E-posta sağlayıcınız için bu tetikleyiciyi seçin: **Yeni bir e-posta geldiğinde - <*e-posta-sağlayıcınız*>**

   Örnek:

   ![E-posta sağlayıcısı için şu tetikleyiciyi seçin: "Yeni bir e-posta geldiğinde"](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Azure iş veya okul hesapları için Office 365 Outlook girişini seçin.

   * Kişisel Microsoft hesapları için Outlook.com girişini seçin.

1. Kimlik bilgileriniz istenirse Logic Apps’in e-posta hesabınıza bağlanabilmesi için e-posta hesabınızda oturum açın.

1. Şimdi tetikleyicinin yeni e-postaları filtrelemek için kullandığı ölçütleri sağlayın.

   1. E-postaları denetlemek için aşağıda açıklanan ayarları belirtin.

      ![Postaları denetlemeye ilişkin klasörü, aralığı ve sıklığı belirtin](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Ayar | Değer | Açıklama |
      | ------- | ----- | ----------- |
      | **Klasör** | Gelen Kutusu | Denetlenecek e-posta klasörü |
      | **Eki Var** | Yes | Yalnızca ek içeren e-postaları alın. <p>**Not:** Tetikleyici, hesabınızdaki e-postaları kaldırmaz, yalnızca yeni iletileri denetler ve yalnızca konu filtresiyle eşleşen e-postaları işler. |
      | **Ekleri Dahil Et** | Yes | Yalnızca ekleri denetlemek yerine, iş akışınız için giriş olarak ekleri alın. |
      | **Aralık** | 1 | Denetimler arasında beklenecek aralık sayısı |
      | **Sıklık** | Dakika | Denetimler arası her aralık için zaman birimi |
      ||||
  
   1. **Yeni parametre Ekle** listesinden **Konu filtresi**' ni seçin.

   1. Eylemde **Konu filtresi** kutusu göründükten sonra, konuyu burada listelenen şekilde belirtin:

      | Ayar | Değer | Açıklama |
      | ------- | ----- | ----------- |
      | **Konu Filtresi** | `Business Analyst 2 #423501` | E-posta konusunda bulunacak metin |
      ||||

1. Tetikleyicinin ayrıntılarını şimdilik gizlemek için tetikleyicinin başlık çubuğunun içine tıklayın.

   ![Ayrıntıları gizlemek için şekli daraltın](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

   Şimdi mantıksal uygulamanız çalışıyor ancak e-postalarınızı denetlemek dışında bir işlem gerçekleştirmiyor. Sonra iş akışını sürdürmek için ölçütleri belirten bir koşul ekleyin.

## <a name="check-for-attachments"></a>Ekleri denetleme

Şimdi yalnızca eki olan e-postaları seçen bir koşul ekleyin.

1. Tetikleyici altında **yeni adım**' ı seçin.

   !["Yeni adım"](./media/tutorial-process-email-attachments-workflow/add-condition-under-trigger.png)

1. **Eylem seçin**altında, arama kutusuna `condition`girin. Şu eylemi seçin: **koşul**

   !["Koşul" ı seçin](./media/tutorial-process-email-attachments-workflow/select-condition.png)

   1. Koşulu daha iyi bir açıklama ile yeniden adlandırın. Koşulun başlık çubuğunda, **Yeniden Adlandır**' > üç nokta ( **...** ) düğmesini seçin.

      ![Koşulu yeniden adlandırın](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

   1. Koşulunuzu şu açıklama ile yeniden adlandırın: `If email has attachments and key subject phrase`

1. E-postalarda ek olup olmadığını denetleyen bir koşul oluşturun.

   1. İlk satırda **Ve** başlığının altındaki sol kutunun içine tıklayın. Açılan dinamik içerik listesinden **Eki Var** özelliğini seçin.

      ![Koşul derleme](./media/tutorial-process-email-attachments-workflow/build-condition.png)

   1. Ortadaki kutuda **eşittir** işlecini tutun.

   1. Sağ kutuda, tetikleyiciden gelen **ek** özelliği değeri ile karşılaştırılacak değer olarak **true** değerini girin.

      ![Koşul derleme](./media/tutorial-process-email-attachments-workflow/finished-condition.png)

      Her iki değer de eşitse, e-posta en az bir ek içeriyordur, koşul başarılı olur ve iş akışı devam eder.

   Kod düzenleyici penceresinde görüntüleyebileceğiniz arka plandaki mantıksal uygulama tanımında bu koşul bu örneğe benzer:

   ```json
   "Condition": {
      "actions": { <actions-to-run-when-condition-passes> },
      "expression": {
         "and": [ {
            "equals": [
               "@triggerBody()?['HasAttachment']",
                 "true"
            ]
         } ]
      },
      "runAfter": {},
      "type": "If"
   }
   ```

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

### <a name="test-your-condition"></a>Koşulunuzu test etme

Şimdi koşulun düzgün çalışıp çalışmadığını test edin:

1. Mantıksal uygulamanız zaten çalışmıyorsa Tasarımcı araç çubuğunda **Çalıştır** ' ı seçin.

   Bu adım, belirttiğiniz zaman aralığı geçinceye kadar beklemeniz gerekmeden mantıksal uygulamanızı el ile başlatır. Ancak test e-postası gelen kutunuza düşene kadar herhangi bir şey olmaz.

1. Kendinize şu ölçütleri karşılayan bir e-posta gönderin:

   * E-postanızın konusu, tetikleyicinin **Konu filtresi** içinde belirttiğiniz metni içerir: `Business Analyst 2 #423501`

   * E-postanız bir ek içerir. Şimdilik boş bir metin dosyası oluşturun ve bu dosyayı e-postanıza ekleyin.

   E-posta geldiğinde mantıksal uygulamanız ekleri ve belirtilen konu metnini denetler. Koşul başarılı olursa tetikleyici tetiklenerek Logic Apps altyapısının bir mantıksal uygulama örneği oluşturmasına ve iş akışını başlatmasına neden olur.

1. Tetikleyicinin harekete geçirildiği ve mantıksal uygulamanın başarıyla çalıştırıldığından emin olmak için, mantıksal uygulama menüsünde **genel bakış**' ı seçin.

   ![Tetikleme ve çalıştırma geçmişini denetleme](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Mantıksal uygulamanız başarılı bir tetikleyiciye rağmen tetiklenmediyse veya çalıştırılmadıysa bkz. [Mantıksal uygulamanızla ilgili sorun giderme](../logic-apps/logic-apps-diagnosing-failures.md).

Sonra, **True ise** dalı için uygulanacak eylemleri tanımlayın. E-postayı eklerle birlikte kaydetmek için e-posta gövdesinden HTML’yi kaldırın, sonra e-posta ve ekler için depolama kapsayıcısında bloblar oluşturun.

> [!NOTE]
> E-posta ek içermediğinde mantıksal uygulamanızın **False ise** dalı için yapacağı bir şey yoktur. Bu öğreticiyi tamamladıktan sonra ek alıştırma olarak, **False ise** dalı için uygulamak istediğiniz uygun eylemi ekleyebilirsiniz.

## <a name="call-removehtmlfunction"></a>RemoveHTMLFunction işlevini çağırma

Bu adım, önceden oluşturduğunuz Azure işlevini mantıksal uygulamanıza ekler ve e-posta tetikleyicisindeki e-posta gövde içeriğini işlevinize iletir.

1. Mantıksal uygulama menüsünde **Mantıksal Uygulama Tasarımcısı**’nı seçin. **Eğer true ise** dalında **Eylem Ekle**' yi seçin.

   !["True ise" bölümünde eylem ekleyin](./media/tutorial-process-email-attachments-workflow/if-true-add-action.png)

1. Arama kutusuna "azure işlevleri" yazın ve bu eylemi seçin: **Azure işlevi seç - Azure İşlevleri**

   !["Azure işlevi seç" eylemini seçin](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

1. Daha önce oluşturduğunuz işlev uygulamanızı seçin, bu örnekte `CleanTextFunctionApp`:

   ![Azure işlevi uygulamanızı seçin](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

1. Şimdi **RemoveHTMLFunction** işlevinizi seçin

   ![Azure işlevinizi seçin](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

1. İşlev şeklinizi şu açıklama ile yeniden adlandırın: `Call RemoveHTMLFunction to clean email body`

1. Şimdi işlevinizde işlenecek girişi belirtin.

   1. **İstek Gövdesi** bölümüne şu metni girin ve arkasına bir boşluk bırakın:

      `{ "emailBody":`

      Sonraki adımlarda bu giriş üzerinde çalışırken girişiniz doğru JSON biçimine sahip olana kadar geçersiz JSON hatası görüntülenecektir. Daha önce bu işlevi test ettiğinizde bu işlev için belirtilen giriş, JavaScript Nesne Gösterimi’ni (JSON) kullanıyordu. Bu nedenle, istek gövdesi de aynı biçimi kullanmalıdır.

      Ayrıca imleciniz **İstek gövdesi** kutusunda olduğunda açılan dinamik içerik listesinden önceki eylemlerde kullanılan özellik değerlerini seçebilirsiniz.

   1. Dinamik içerik listesinde **Yeni bir e-posta geldiğinde** bölümünde **Gövde** özelliğini seçin. Bu özelliğin ardından kapanış küme ayracını eklemeyi unutmayın: `}`

      ![İşleve iletilecek istek gövdesini belirtin](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   İşlem tamamlandığında işlevinizin girişi şu örnekteki gibi görünür:

   ![İşlevinize iletilecek tamamlanmış istek gövdesi](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing-2.png)

1. Mantıksal uygulamanızı kaydedin.

Ardından e-posta gövdesini kaydedebilmek için depolama kapsayıcınızda blob oluşturan bir eylem ekleyin.

## <a name="create-blob-for-email-body"></a>E-posta gövdesi için blob oluşturma

1. **If true** bloğunda ve Azure işleviniz altında **Eylem Ekle**' yi seçin.

1. Arama kutusuna filtreniz olarak `create blob` girin ve şu eylemi seçin: **BLOB oluştur**

   ![E-posta gövdesine ilişkin blob oluşturmak için eylem ekleme](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

1. Burada gösterildiği ve açıklandığı gibi şu ayarlarla depolama hesabınıza bağlantı oluşturun. İşiniz bittiğinde **Oluştur**’u seçin.

   ![Depolama hesabına bağlantı oluşturma](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Ayar | Değer | Açıklama |
   | ------- | ----- | ----------- |
   | **Bağlantı Adı** | AttachmentStorageConnection | Bağlantı için açıklayıcı bir ad |
   | **Depolama Hesabı** | attachmentstorageacct | Ekleri kaydetmek için daha önce oluşturduğunuz depolama hesabının adı |
   ||||

1. **Blob oluştur** eylemini şu açıklama ile yeniden adlandırın: `Create blob for email body`

1. **Blob oluştur** eyleminde şu bilgileri sağlayın ve gösterilip açıklandığı gibi blob oluşturmak için şu alanları seçin:

   ![E-posta gövdesi için blob bilgilerini sağlayın](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Ayar | Değer | Açıklama |
   | ------- | ----- | ----------- |
   | **Klasör yolu** | /attachments | Daha önce oluşturduğunuz kapsayıcının yolu ve adı. Bu örnekte klasör simgesine tıklayın ve "/attachments" kapsayıcısını seçin. |
   | **Blob adı** | **Gönderen** alanı | Bu örnek için blob adı olarak gönderen adını kullanın. Bu kutunun içine tıklayın ve açılan dinamik içerik listesinde **Yeni bir e-posta geldiğinde** eyleminin altında **Gönderen** alanını seçin. |
   | **Blob içeriği** | **İçerik** alanı | Bu örnek için HTML içermeyen e-posta gövdesini blob içeriği olarak kullanın. Bu kutunun içine tıklayın ve açılan dinamik içerik listesinde **E-posta gövdesini temizlemek için RemoveHTMLFunction işlevini çağırma** eyleminin altında **Gövde** alanını seçin. |
   ||||

   İşlem tamamlandığında eylem şu örnekteki gibi görünür:

   ![Tamamlanmış "Blob oluştur" eylemi](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body-done.png)

1. Mantıksal uygulamanızı kaydedin.

### <a name="check-attachment-handling"></a>Ek işlemeyi denetleme

Şimdi mantıksal uygulamanızın e-postaları belirttiğiniz şekilde işleyip işlemediğini test edin:

1. Mantıksal uygulamanız zaten çalışmıyorsa Tasarımcı araç çubuğunda **Çalıştır** ' ı seçin.

1. Kendinize şu ölçütleri karşılayan bir e-posta gönderin:

   * E-postanızın konusu, tetikleyicinin **Konu filtresi** içinde belirttiğiniz metni içerir: `Business Analyst 2 #423501`

   * E-postanız en az bir ek içerir. Şimdilik yalnızca bir boş metin dosyası oluşturun ve bu dosyayı e-postanıza ekleyin.

   * E-postanız gövdede bazı test içeriğine sahiptir, örneğin: `Testing my logic app`

   Mantıksal uygulamanız başarılı bir tetikleyiciye rağmen tetiklenmediyse veya çalıştırılmadıysa bkz. [Mantıksal uygulamanızla ilgili sorun giderme](../logic-apps/logic-apps-diagnosing-failures.md).

1. Mantıksal uygulamanızın e-postayı doğru depolama kapsayıcısına kaydedip kaydetmediğini denetleyin.

   1. Depolama Gezgini ' de, **yerel & eklenmiş** > **depolama hesapları** > **attachmentstorageacct (Key)**  > **BLOB kapsayıcıları** > **ekler**' i genişletin.

   1. E-posta için **ekler** kapsayıcısını denetleyin.

      Bu noktada, mantıksal uygulama henüz ekleri işlemediğinden kapsayıcıda yalnızca e-posta görüntülenir.

      ![Depolama Gezgini’nde kayıtlı e-posta olup olmadığını denetleyin](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   1. İşiniz bittiğinde, Depolama Gezgini’nden e-postayı silin.

1. İsteğe bağlı olarak, şu anda herhangi bir şey yapmayan **False ise** dalını test etmek için ölçütleri karşılamayan bir e-posta gönderebilirsiniz.

Sonra, tüm e-posta eklerini işlemek için bir döngü ekleyin.

## <a name="process-attachments"></a>Ekleri işleme

E-postadaki her eki işlemek için mantıksal uygulamanızın iş akışına **her biri için** döngüsü ekleyin.

1. **E-posta gövdesi için blob oluştur** şeklinin altında **Eylem Ekle**' yi seçin.

   !["For each" döngüsünü ekleyin](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

1. **Eylem seçin**altında, arama kutusuna filtreniz olarak `for each` girin ve şu eylemi seçin: **her biri için**

   !["Her bir" ı seçin](./media/tutorial-process-email-attachments-workflow/select-for-each.png)

1. Döngünüzü şu açıklama ile yeniden adlandırın: `For each email attachment`

1. Şimdi işlenecek döngü için verileri belirtin. **Önceki adımlardan bir çıkış seçin** kutusunun içine tıklayın ve açılan dinamik içerik listesinden **Ekler**'i seçin.

   !["Ekler"i seçin](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   **Ekler** alanı, bir e-postaya iliştirilen tüm ekleri içeren bir dizi iletir. **For each** döngüsü, diziyle birlikte iletilen her öğede eylemleri yineler.

1. Mantıksal uygulamanızı kaydedin.

Sonra, her eki **ekler** depolama kapsayıcınızda blob olarak kaydeden eylemi ekleyin.

## <a name="create-blob-for-each-attachment"></a>Her bir ek için blob oluşturma

1. Her bir **e-posta eki için** , bulunan her ekte gerçekleştirilecek görevi belirtebileceğiniz **bir eylem Ekle** ' yi seçin.

   ![Döngüye eylem ekleme](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

1. Arama kutusuna filtreniz olarak `create blob` girin ve ardından şu eylemi seçin: **BLOB oluştur**

   ![Blob oluşturmak için eylem ekleme](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

1. **Blob oluştur 2** eylemini şu açıklama ile yeniden adlandırın: `Create blob for each email attachment`

1. **Her e-posta eki için blob oluştur** eyleminde şu bilgileri sağlayın ve gösterilip açıklandığı gibi oluşturmak istediğiniz her bir blog için özellikleri seçin:

   ![Blob bilgilerini sağlama](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Ayar | Değer | Açıklama |
   | ------- | ----- | ----------- |
   | **Klasör yolu** | /attachments | Daha önce oluşturduğunuz kapsayıcının yolu ve adı. Bu örnekte klasör simgesine tıklayın ve "/attachments" kapsayıcısını seçin. |
   | **Blob adı** | **Ad** alanı | Bu örnek için blob adı olarak ek adını kullanın. Bu kutunun içine tıklayın ve açılan dinamik içerik listesinde **Yeni bir e-posta geldiğinde** eyleminin altında **Ad** alanını seçin. |
   | **Blob içeriği** | **İçerik** alanı | Bu örnek için **İçerik** alanını blob içeriği olarak kullanın. Bu kutunun içine tıklayın ve açılan dinamik içerik listesinde **Yeni bir e-posta geldiğinde** eyleminin altında **İçerik** alanını seçin. |
   ||||

   İşlem tamamlandığında eylem şu örnekteki gibi görünür:

   ![Tamamlanmış "Blob oluştur" eylemi](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment-done.png)

1. Mantıksal uygulamanızı kaydedin.

### <a name="check-attachment-handling"></a>Ek işlemeyi denetleme

Sonra mantıksal uygulamanızın ekleri belirttiğiniz şekilde işleyip işlemediğini test edin:

1. Mantıksal uygulamanız zaten çalışmıyorsa Tasarımcı araç çubuğunda **Çalıştır** ' ı seçin.

1. Kendinize şu ölçütleri karşılayan bir e-posta gönderin:

   * E-postanız konusu, tetikleyicinin **Konu filtresi** özelliğinde belirttiğiniz metni içerir: `Business Analyst 2 #423501`

   * E-postanız en az iki ek içerir. Şimdilik iki boş metin dosyası oluşturun ve bu dosyaları e-postanıza ekleyin.

   Mantıksal uygulamanız başarılı bir tetikleyiciye rağmen tetiklenmediyse veya çalıştırılmadıysa bkz. [Mantıksal uygulamanızla ilgili sorun giderme](../logic-apps/logic-apps-diagnosing-failures.md).

1. Mantıksal uygulamanızın e-postayı ve ekleri doğru depolama kapsayıcısına kaydedip kaydetmediğini denetleyin.

   1. Depolama Gezgini ' de, **yerel & eklenmiş** > **depolama hesapları** > **attachmentstorageacct (Key)**  > **BLOB kapsayıcıları** > **ekler**' i genişletin.

   1. **Ekler** kapsayıcısında hem e-postayı hem de ekleri denetleyin.

      ![Kayıtlı e-posta ve ekleri denetleme](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   1. İşiniz bittiğinde, Depolama Gezgini’nden e-posta ve ekleri silin.

Sonra mantıksal uygulamanızın ekleri gözden geçirmek üzere e-posta göndermesi için bir eylem ekleyin.

## <a name="send-email-notifications"></a>E-posta bildirimleri gönderme

1. **Eğer true ise** dalında, **her e-posta eki Için** , **Eylem Ekle**' yi seçin.

   !["For each" döngüsünün altına eylem ekleme](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

1. Arama kutusuna filtreniz olarak `send email` girin ve ardından e-posta sağlayıcınız için "e-posta gönder" eylemini seçin.

   Eylem listesini belirli bir hizmeti içerek şekilde filtrelemek için önce bağlayıcıyı seçebilirsiniz.

   ![E-posta sağlayıcınız için "e-posta gönder" eylemini seçin](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Azure iş veya okul hesapları için Office 365 Outlook girişini seçin.

   * Kişisel Microsoft hesapları için Outlook.com girişini seçin.

1. Kimlik bilgileriniz istenirse, Logic Apps’in e-posta hesabınıza yönelik bir bağlantı oluşturması için e-posta hesabınızda oturum açın.

1. **E-posta gönder** eylemini şu açıklama ile yeniden adlandırın: `Send email for review`

1. Bu eylem için bilgileri sağlayın ve gösterilip açıklandığı gibi e-postaya dahil etmek istediğiniz alanları seçin. Bir düzenleme kutusuna boş satır eklemek için Shift + Enter tuşlarını kullanın.

   ![E-posta bildirimi gönderme](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Dinamik içerik listesinde beklenen bir alan bulamazsanız, **Yeni bir e-posta geldiğinde** **daha fazla göster** ' i seçin.

   | Ayar | Değer | Notlar |
   | ------- | ----- | ----- |
   | **Alıcı** | <*recipient-email-address*> | Test için kendi e-posta adresinizi kullanabilirsiniz. |
   | **Konu**  | ```ASAP - Review applicant for position:``` **Konu** | Dahil etmek istediğiniz e-posta konusu. Bu kutunun içine tıklayın, örnek metni girin ve dinamik içerik listesinin **Yeni bir e-posta geldiğinde** bölümünde **Konu** alanını seçin. |
   | **Gövde** | ```Please review new applicant:``` <p>```Applicant name:``` **Kimden** <p>```Application file location:``` **Yol** <p>```Application email content:``` **Gövde** | E-posta gövdesinin içeriği. Bu kutunun içine tıklayın, örnek metni girin ve dinamik içerik listesinden şu alanları seçin: <p>- **Yeni bir e-posta geldiğinde** bölümünde **Kimden** alanı </br>- **E-posta gövdesi için blob oluşturma** bölümünde **Yol** alanı </br>- **E-posta gövdesini temizlemek için RemoveHTMLFunction işlevini çağırma** bölümünde **Gövde** alanı |
   ||||

   > [!NOTE]
   > **İçerik** alanı gibi eklerin yer aldığı bir diziyi içeren alan seçerseniz tasarımcı eyleme otomatik olarak ilgili alana başvuran bir "For each" döngüsü ekler. Bu şekilde mantıksal uygulamanız ilgili eylemi dizideki tüm öğeler için gerçekleştirebilir. Döngüyü kaldırmak için, dizinin alanını kaldırın, başvuru eylemini döngünün dışına taşıyın, döngünün başlık çubuğundaki üç nokta ( **...** ) simgesini seçin ve **Sil**' i seçin.

1. Mantıksal uygulamanızı kaydedin.

Şimdi mantıksal uygulamanızı test edin. Mantıksal uygulamanız şu örnekteki gibi görünür:

![Tamamlanmış mantıksal uygulama](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>Mantıksal uygulamanızı çalıştırın

1. Kendinize şu ölçütleri karşılayan bir e-posta gönderin:

   * E-postanız konusu, tetikleyicinin **Konu filtresi** özelliğinde belirttiğiniz metni içerir: `Business Analyst 2 #423501`

   * E-postanız bir veya daha fazla eke sahip. Önceki testinizdeki boş metin dosyasını yeniden kullanabilirsiniz. Daha gerçekçi bir senaryo için bir özgeçmiş dosyası ekleyin.

   * E-posta gövdesi şu metni içerir (bu metni kopyalayıp yapıştırabilirsiniz):

     ```text

     Name: Jamal Hartnett

     Street address: 12345 Anywhere Road

     City: Any Town

     State or Country: Any State

     Postal code: 00000

     Email address: jamhartnett@outlook.com

     Phone number: 000-000-0000

     Position: Business Analyst 2 #423501

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  

     Certifications: Six Sigma Green Belt, Lean Project Management

     Language skills: English, Mandarin, Spanish

     Education: Master of Business Administration
     ```

1. Mantıksal uygulamanızı çalıştırın. Başarılı olursa, mantıksal uygulamanız size şu örneğe benzer şekilde görünen bir e-posta gönderir:

   ![Mantıksal uygulama tarafından gönderilen e-posta bildirimi](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   E-posta gelmezse istenmeyen e-posta klasörüne bakın. E-postanızın istenmeyen posta filtresi bu tür postaları yeniden yönlendirebilir. Mantıksal uygulamanızın düzgün bir şekilde çalışıp çalışmadığından emin değilseniz bkz. [Mantıksal uygulama sorunlarını giderme](../logic-apps/logic-apps-diagnosing-failures.md).

Tebrikler, farklı Azure hizmetleri arasında görevleri otomatikleştiren ve bazı özel kodları çağıran bir mantıksal uygulama oluşturup çalıştırdınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık bu örneğe ihtiyacınız yoksa mantıksal uygulamanızı ve ilgili kaynakları içeren kaynak grubunu silin.

1. Azure menüsünde **Kaynak grupları**'nı seçin. Kaynak grupları listesinde, Bu öğreticinin kaynak grubunu seçin. **Genel bakış** bölmesinde **kaynak grubunu sil**' i seçin.

   ![Mantıksal uygulama kaynak grubunu silme](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

1. Onay bölmesi göründüğünde, kaynak grubu adını girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Depolama ve Azure İşlevleri gibi Azure hizmetlerini tümleştirerek e-posta eklerini işleyen ve depolayan bir mantıksal uygulama oluşturdunuz. Artık mantıksal uygulamalar oluşturmak için kullanabileceğiniz diğer bağlayıcılar hakkında daha fazla bilgi edinebilirsiniz.

> [!div class="nextstepaction"]
> [Logic Apps için bağlayıcılar hakkında daha fazla bilgi edinin](../connectors/apis-list.md)
