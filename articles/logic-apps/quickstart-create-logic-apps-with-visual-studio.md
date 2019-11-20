---
title: Visual Studio 'da görev iş akışlarını otomatikleştirin-Azure Logic Apps
description: Azure Logic Apps ve Visual Studio kullanarak kurumsal tümleştirme için yinelenen iş akışları oluşturma, zamanlama ve çalıştırma
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.manager: carmonm
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.openlocfilehash: e7ce31f489ae274eddb02c1bfb7e57eb486f5851
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796848"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Hızlı başlangıç: Azure Logic Apps Visual Studio ile otomatikleştirilmiş görevler, süreçler ve iş akışları oluşturma

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve Visual Studio ile uygulama, veri, sistem ve hizmetleri kurum ve kuruluşlar arasında otomatik hale getiren iş akışları oluşturabilirsiniz. Bu hızlı başlangıçta, Visual Studio 'da mantıksal uygulamalar oluşturarak ve bu uygulamaları Azure 'a dağıtarak bu iş akışlarını nasıl tasarlayıp oluşturabileceğiniz gösterilmektedir. Bu görevleri Azure portal gerçekleştirmenize rağmen, Visual Studio, mantıksal uygulamalarınızı kaynak denetimine eklemenize, farklı sürümler yayımlamanıza ve farklı dağıtım ortamları için Azure Resource Manager şablonları oluşturmanıza olanak sağlar.

Azure Logic Apps yeni başladıysanız ve yalnızca temel kavramları istiyorsanız, [Azure Portal bir mantıksal uygulama oluşturmaya yönelik hızlı](../logic-apps/quickstart-create-first-logic-app-workflow.md)başlangıcı deneyin. Logic App Designer, hem Azure portal hem de Visual Studio 'da benzer şekilde çalışır.

Bu hızlı başlangıçta, Azure portal hızlı başlangıç olarak Visual Studio ile aynı mantıksal uygulamayı oluşturacaksınız. Bu mantıksal uygulama, bir Web sitesinin RSS akışını izler ve söz konusu akıştaki her yeni öğe için e-posta gönderir. Tamamlanmış mantıksal uygulamanız şu üst düzey iş akışı gibi görünür:

![Tamamlanmış mantıksal uygulama](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği. Aboneliğiniz yoksa, [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Henüz yoksa şu araçları indirip yükleyin:

  * [Visual Studio 2019, 2017 veya 2015-Community sürümü veya üzeri](https://aka.ms/download-visual-studio). Bu hızlı başlangıçta Visual Studio Community 2017 kullanılmaktadır.

    > [!IMPORTANT]
    > Visual Studio 2019 veya 2017 yüklediğinizde, **Azure geliştirme** iş yükünü seçtiğinizden emin olun.

  * [.NET için Microsoft Azure SDK (2.9.1 veya üzeri)](https://azure.microsoft.com/downloads/). [Azure SDK for .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet) hakkında daha fazla bilgi edinin.

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * İstediğiniz sürüme yönelik Visual Studio uzantısı için en son Azure Logic Apps araçları:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Azure Logic Apps Araçlarını doğrudan Visual Studio Market’ten indirip yükleyebilir veya [bu uzantıyı Visual Studio’nun içinden yükleme](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions) hakkında bilgi edinebilirsiniz. Yükleme işlemini tamamladıktan sonra Visual Studio’yu yeniden başlattığınızdan emin olun.

* Ekli Mantıksal Uygulama Tasarımcısı kullanılırken web erişimi

  Tasarımcı Azure 'da kaynak oluşturmak ve mantıksal uygulamanızdaki bağlayıcılardan özellikleri ve verileri okumak için bir internet bağlantısı gerektirir. Örneğin, Dynamics CRM Online bağlantılarında, tasarımcı varsayılan ve özel özellikler için CRM örneğinizi denetler.

* Logic Apps tarafından desteklenen Office 365 Outlook, Outlook.com veya Gmail gibi bir e-posta hesabı. Diğer sağlayıcılar için [buradaki bağlayıcılar listesini](https://docs.microsoft.com/connectors/)gözden geçirin. Bu örnekte Office 365 Outlook kullanılmaktadır. Farklı bir sağlayıcı kullanıyorsanız genel adımlar aynıdır, ancak kullanıcı arabirimi biraz farklı olabilir.

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Azure kaynak grubu projesi oluşturma

Başlamak için bir [Azure Kaynak Grubu projesi](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) oluşturun. [Azure kaynak grupları ve kaynakları](../azure-resource-manager/resource-group-overview.md) hakkında daha fazla bilgi edinin.

1. Visual Studio’yu çalıştırın. Azure hesabınızla oturum açın.

1. **Dosya** menüsünde **Yeni** > **Proje**’yi seçin. (Klavye: CTRL + SHIFT + N)

   !["Dosya" menüsünde "Yeni" > "Proje" öğesini seçin](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. **Yüklü** altında **Visual C#** veya **Visual Basic**’i seçin. **Bulut** > **Azure Kaynak Grubu**’nu seçin. Projenizi adlandırın, örneğin:

   ![Azure Kaynak Grubu projesi oluşturma](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Kaynak grubu adları yalnızca harf, sayı, nokta (`.`), alt çizgi (`_`), tire (`-`) ve parantez (`(`, `)`) içerebilir, ancak nokta (`.`) ile *bitemez* .
   >
   > **Bulut** veya **Azure Kaynak grubu** görünmezse, VISUAL Studio için Azure SDK 'yi yüklediğinizden emin olun.

   Visual Studio 2019 kullanıyorsanız, şu adımları izleyin:

   1. **Yeni proje oluştur** kutusunda, Visual C# veya Visual Basic için **Azure Kaynak grubu** projesi seçin. **İleri**’yi seçin.

   1. Kullanmak istediğiniz Azure Kaynak grubu için bir ad ve diğer proje bilgilerini girin. **Oluştur**'u seçin.

1. Şablon listesinden **mantıksal uygulama** şablonu ' nu seçin. **Tamam**’ı seçin.

   ![Mantıksal Uygulama şablonunu seçme](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Visual Studio projenizi oluşturduktan sonra, Çözüm Gezgini açılır ve çözümünüzü gösterir. Çözümünüzde, **logicapp. JSON** dosyası yalnızca mantıksal uygulama tanımınızı depolamaz, aynı zamanda dağıtım için kullanabileceğiniz bir Azure Resource Manager şablonudur.

   ![Çözüm Gezgini yeni mantıksal uygulama çözümünü ve dağıtım dosyasını gösterir](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Boş mantıksal uygulama oluşturma

Azure Kaynak grubu projeniz olduğunda, mantıksal uygulamanızı **boş mantıksal uygulama** şablonuyla oluşturun.

1. Çözüm Gezgini, **Logicapp. JSON** dosyasının kısayol menüsünü açın. **Mantıksal Uygulama Tasarımcısı ile Aç**’ı seçin. (Klavye: Ctrl + L)

   ![Mantıksal Uygulama Tasarımcısı ile mantıksal uygulama .json dosyasını açma](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Visual Studio 2019 ' de bu komuta sahip değilseniz, Visual Studio için en son güncelleştirmelere sahip olup olmadığınızı kontrol edin.

   Visual Studio, mantıksal uygulamanız ve bağlantılarınız için kaynak oluşturmak ve dağıtmak üzere Azure aboneliğiniz ve bir Azure Kaynak grubu için sizi uyarır.

1. **Abonelik**için Azure aboneliğinizi seçin. **Kaynak grubu**Için **Yeni oluştur** ' u seçerek başka bir Azure Kaynak grubu oluşturun.

   ![Azur aboneliği, kaynak grubu ve kaynak konumu seçme](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Ayar | Örnek değer | Açıklama |
   | ------- | ------------- | ----------- |
   | Kullanıcı hesabı | Fabrikam <br> sophia-owen@fabrikam.com | Visual Studio 'da oturum açtığınızda kullandığınız hesap |
   | **Abonelik** | Kullandıkça Öde <br> (sophia-owen@fabrikam.com) | Azure aboneliğinizin ve ilişkili hesabın adı |
   | **Kaynak Grubu** | MyLogicApp-RG <br> (Batı ABD) | Mantıksal uygulamanızın kaynaklarını depolamaya ve dağıtmaya yönelik Azure Kaynak grubu ve konumu |
   | **Konum** | **Kaynak grubuyla aynı** | Mantıksal uygulamanızı dağıtmaya yönelik konum türü ve belirli bir konum. Konum türü bir Azure bölgesi veya var olan bir [tümleştirme hizmeti ortamıdır (ISE)](connect-virtual-network-vnet-isolated-environment.md). <p>Bu hızlı başlangıçta konum türünü **bölge** olarak ve konum ' u **kaynak grubuyla aynı**olarak ayarlanmış halde tutun. <p>**Note**: kaynak grubu projenizi oluşturduktan sonra [konum türünü ve konumunu değiştirebilirsiniz](manage-logic-apps-with-visual-studio.md#change-location), ancak farklı bir konum türü mantıksal uygulamanızı çeşitli yollarla etkiler. |
   ||||

1. Logic Apps tasarımcı, bir giriş videosunu ve yaygın olarak kullanılan Tetikleyicileri gösteren bir sayfa açar. Videoyu ve Tetikleyicileri **şablonlara**göre aşağı kaydırın ve **boş mantıksal uygulama**' yı seçin.

   !["Boş Mantıksal Uygulama" seçme](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Mantıksal uygulama iş akışı derleme

Sonra, yeni bir akış öğesi göründüğünde harekete çıkan bir RSS [tetikleyicisi](../logic-apps/logic-apps-overview.md#logic-app-concepts) ekleyin. Her mantıksal uygulama, belirli bir ölçüt karşılandığında tetiklenen bir tetikleyici ile başlar. Tetikleyici her etkinleştirildiğinde Logic Apps altyapısı iş akışınızı çalıştıran bir mantıksal uygulama örneği oluşturur.

1. Mantıksal uygulama Tasarımcısı ' nda, arama kutusunda, **Tümü**' nü seçin. Arama kutusuna "RSS" yazın. Tetikleyiciler listesinden şu tetikleyiciyi seçin: **bir akış öğesi yayımlandığında**

   ![Tetikleyici ve eylemler ekleyerek mantıksal uygulamanızı derleme](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Tetikleyici tasarımcıda görüntülendikten sonra, [Azure Portal Hızlı](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)başlangıcı ' nda iş akışı adımlarını izleyerek mantıksal uygulamayı oluşturmayı tamamladıktan sonra bu makaleye geri dönün. İşiniz bittiğinde mantıksal uygulamanız şu örnekteki gibi görünür:

   ![Tamamlanmış mantıksal uygulama](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Visual Studio çözümünüzü kaydedin. (Klavye: Ctrl + S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Mantıksal uygulamanızı Azure'a dağıtma

Mantıksal uygulamanızı çalıştırıp test etmeden önce, uygulamayı Visual Studio 'dan Azure 'a dağıtın.

1. Çözüm Gezgini'nde projenizin kısayol menüsünden **Dağıt** > **Yeni**'yi seçin. Sorulursa Azure hesabınızla oturum açın.

   ![Mantıksal uygulama dağıtımı oluşturma](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Bu dağıtım için, varsayılan Azure aboneliğini, kaynak grubunu ve diğer ayarları koruyun. **Dağıt**'ı seçin.

   ![Mantıksal uygulamayı Azure kaynak grubuna dağıtma](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. **Parametreleri Düzenle** kutusu görüntülenirse, mantıksal uygulamanız için bir kaynak adı girin. Ayarlarınızı kaydedin.

   ![Mantıksal uygulama için dağıtım adı belirtme](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Dağıtım başladığında uygulamanızın dağıtım durumu Visual Studio **Çıktı** penceresinde görünür. Durum görünmezse **Çıktıyı göster** listesini açıp Azure kaynak grubunuzu seçin.

   ![Dağıtım durumu çıktısı](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Seçili bağlayıcılarınızın sizin için giriş yapması gerekiyorsa, arka planda bir PowerShell penceresi açılır ve gerekli parola veya gizli anahtar tuşları ister. Bu bilgileri girdikten sonra dağıtım işlemi devam eder.

   ![PowerShell penceresi](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Dağıtım bittikten sonra mantıksal uygulamanız Azure portal canlı olur ve belirtilen zamanlamada (dakikada bir) çalışır. Tetikleyici yeni akış öğeleri bulursa, mantıksal uygulamanızın eylemlerini çalıştıran bir iş akışı örneği oluşturan tetikleyici ateşlenir. Mantıksal uygulamanız her yeni öğe için e-posta gönderir. Aksi takdirde, tetikleyici yeni öğeler bulamazsa, tetikleyici harekete geçmez ve "atlar" iş akışını yeniden oluşturmaz. Mantıksal uygulamanız, denetlemeden önce bir sonraki aralığa kadar bekler.

   Bu mantıksal uygulamanın gönderdiği örnek e-postalar aşağıda verilmiştir. E-posta gelmezse istenmeyen e-posta klasörüne bakın.

   ![Outlook her yeni RSS öğesi için e-posta gönderir](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Tebrikler, mantıksal uygulamanızı Visual Studio ile başarıyla derlediniz ve dağıttınız. Mantıksal uygulamanızı yönetmek ve çalıştırma geçmişini gözden geçirmek için bkz. [Visual Studio ile mantıksal uygulamaları yönetme](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="add-new-logic-app"></a>Yeni mantıksal uygulama ekle

Mevcut bir Azure Kaynak grubu projeniz varsa, JSON ana hattı penceresini kullanarak bu projeye yeni bir boş mantıksal uygulama ekleyebilirsiniz.

1. Çözüm Gezgini, `<logic-app-name>.json` dosyasını açın.

1. **Görünüm** menüsünde **diğer Windows** > **JSON ana hattını**seçin.

1. Şablon dosyasına bir kaynak eklemek için JSON ana hattı penceresinin üst kısmında **Kaynak Ekle** ' yi seçin. Ya da JSON ana hattı penceresinde, **kaynaklar** kısayol menüsünü açın ve **Yeni Kaynak Ekle**' yi seçin.

   ![JSON ana hat penceresi](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. **Kaynak Ekle** iletişim kutusunda, arama kutusunda, `logic app`bulun ve **mantıksal uygulama**' yı seçin. Mantıksal uygulamanızı adlandırın ve **Ekle**' yi seçin.

   ![Kaynak Ekle](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Mantıksal uygulamanızı tamamladığınızda, mantıksal uygulamanızı ve ilgili kaynakları içeren kaynak grubunu silin.

1. Mantıksal uygulamanızı oluşturmak için kullandığınız hesapla [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure menüsünde **Kaynak grupları**'nı seçin. Mantıksal uygulamanızın kaynak grubunu seçin ve **genel bakış**' ı seçin.

1. **Genel bakış** sayfasında, **kaynak grubunu sil**' i seçin. Kaynak grubu adını onay olarak girin ve **Sil**' i seçin.

   !["Kaynak grupları" > "Genel bakış" > "Kaynak grubunu sil"](./media/quickstart-create-logic-apps-with-visual-studio/delete-resource-group.png)

1. Visual Studio çözümünü yerel bilgisayarınızdan silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede Visual Studio kullanarak mantıksal uygulamanızı derlediniz, dağıttınız ve çalıştırdınız. Visual Studio ile mantıksal uygulamalar için gelişmiş dağıtımı yönetme ve gerçekleştirme hakkında bilgi edinmek için şu makalelere bakın:

> [!div class="nextstepaction"]
> [Visual Studio ile mantıksal uygulamaları yönetme](../logic-apps/manage-logic-apps-with-visual-studio.md)
