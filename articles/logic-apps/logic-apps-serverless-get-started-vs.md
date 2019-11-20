---
title: Visual Studio 'da ilk sunucusuz uygulamayı derleme-Azure Logic Apps
description: Visual Studio 'da Azure Logic Apps ve Azure Işlevleri 'ni kullanarak sunucusuz bir uygulama oluşturun, dağıtın ve yönetin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: vs-azure
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 76ab76440ddc25f4b1cf51f7d540a6e21614541d
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680122"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Visual Studio 'da Azure Logic Apps ve Azure Işlevleri 'ni kullanarak ilk sunucusuz uygulamanızı derleme

Azure 'daki [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [Azure işlevleri](../azure-functions/functions-overview.md)gibi sunucusuz araçları ve özellikleri kullanarak bulut uygulamalarını hızlı bir şekilde geliştirebilir ve dağıtabilirsiniz. Bu makalede, Visual Studio 'da bir Azure işlevi çağıran bir mantıksal uygulama kullanan sunucusuz bir uygulama oluşturmaya nasıl başlayabileceği gösterilmektedir. Azure 'da sunucusuz çözümler hakkında daha fazla bilgi edinmek için bkz. [Azure sunucusuz WITH Functions and Logic Apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Önkoşullar

Visual Studio 'da sunucusuz bir uygulama oluşturmak için şunlar gerekir:

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Aşağıdaki araçlar. Henüz yoksa, indirip yükleyin.

  * [Visual Studio 2019, 2017 veya 2015 (topluluk veya diğer sürüm)](https://aka.ms/download-visual-studio). 
  Bu hızlı başlangıçta ücretsiz olan Visual Studio Community 2017 kullanılmaktadır.

    > [!IMPORTANT]
    > Visual Studio 2019 veya 2017 yüklediğinizde, **Azure geliştirme** iş yükünü seçtiğinizden emin olun.

  * [.NET için Microsoft Azure SDK (sürüm 2.9.1 veya üzeri)](https://azure.microsoft.com/downloads/). 
  [Azure SDK for .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet) hakkında daha fazla bilgi edinin.

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * İstediğiniz Visual Studio sürümü için Azure Logic Apps araçları:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Azure Logic Apps Araçlarını doğrudan Visual Studio Market’ten indirip yükleyebilir veya [bu uzantıyı Visual Studio’nun içinden yükleme](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions) hakkında bilgi edinebilirsiniz. 
    Yükleme işlemini tamamladıktan sonra Visual Studio’yu yeniden başlattığınızdan emin olun.

  * Yerel olarak hata ayıklama Işlevleri için [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) .

* Katıştırılmış mantıksal uygulama Tasarımcısı kullanılırken Web 'e erişin.

  Tasarımcının Azure'da kaynak oluşturması ve mantıksal uygulamanızdaki bağlayıcılardan özellik ve verileri okuması için İnternet bağlantısı gerekir. 
  Örneğin, Dynamics CRM Online bağlayıcısını kullanıyorsanız, tasarımcı CRM örneğinizdeki varsayılan ve özel kullanılabilir özellikleri denetler.

## <a name="create-a-resource-group-project"></a>Kaynak grubu projesi oluşturma

Başlamak için sunucusuz uygulamanız için bir [Azure Kaynak grubu projesi](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) oluşturun. Azure 'da, tek bir varlık olarak bir uygulamanın tamamına yönelik kaynakları organize etmek, yönetmek ve dağıtmak için kullandığınız mantıksal bir koleksiyon olan *kaynak grubu*içinde kaynaklar oluşturursunuz. Azure 'da sunucusuz bir uygulama için kaynak grubunuz hem Azure Logic Apps hem de Azure Işlevleri için kaynaklar içerir. [Azure kaynak grupları ve kaynakları](../azure-resource-manager/resource-group-overview.md) hakkında daha fazla bilgi edinin.

1. Visual Studio 'Yu başlatın ve Azure hesabınızı kullanarak oturum açın.

1. **Dosya** menüsünde **Yeni** > **Proje**’yi seçin.

   ![Visual Studio 'da yeni proje oluştur](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. **Yüklü** altında **Visual C#** veya **Visual Basic**’i seçin. Ardından **bulut**  > **Azure Kaynak grubu**' nu seçin.

   > [!NOTE]
   > **Bulut** kategorisi veya **Azure Kaynak grubu** projesi yoksa, Visual Studio için Azure SDK 'sını yüklediğinizden emin olun.

   Visual Studio 2019 kullanıyorsanız, şu adımları izleyin:

   1. **Yeni proje oluştur** kutusunda, Visual C# veya Visual Basic için **Azure Kaynak grubu** proje şablonunu seçin ve ardından **İleri**' yi seçin.

   1. Azure Kaynak grubu için kullanmak istediğiniz adı ve diğer proje bilgilerini girin. İşiniz bittiğinde **Oluştur**’u seçin.

1. Projenize bir ad ve konum verin ve ardından **Tamam**' ı seçin.

   Visual Studio, şablonlar listesinden bir şablon seçmenizi ister. 
   Bu örnek bir Azure hızlı başlangıç şablonu kullanarak bir mantıksal uygulama ve bir Azure işlevi çağrısı içeren sunucusuz bir uygulama oluşturabilirsiniz.

   > [!TIP]
   > Çözümünüzü bir Azure Kaynak grubuna önceden dağıtmak istemediğiniz senaryolarda boş **mantıksal uygulama** şablonunu kullanarak yalnızca boş bir mantıksal uygulama oluşturur.

1. **Bu konumdan şablonları göster** listesinden **Azure hızlı başlangıç (GitHub.com/Azure/Azure-QuickStart-Templates)** seçeneğini belirleyin.

1. Arama kutusuna filtreniz olarak "Logic-App" yazın. Sonuçlardan **101-Logic-App-ve-Function-App** şablonunu seçin.

   ![Azure hızlı başlangıç şablonunu seçin](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio, kaynak grubu projeniz için bir çözüm oluşturur ve açar. 
   Seçtiğiniz Azure hızlı başlangıç şablonu, kaynak grubu projeniz içinde azuredeploy. JSON adlı bir dağıtım şablonu oluşturur. Bu dağıtım şablonu, bir HTTP isteği tarafından tetiklenen basit bir mantıksal uygulama tanımını içerir, bir Azure işlevi çağırır ve sonucu HTTP yanıtı olarak döndürür.

   ![Yeni sunucusuz çözüm](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Sonra, çözümünüzü Azure 'a dağıtın. Dağıtım şablonunu açabilmek ve sunucusuz uygulamanızın kaynaklarını gözden geçirebilmeniz için bunu yapmanız gerekir.

## <a name="deploy-your-solution"></a>Çözümünüzü dağıtın

Mantıksal uygulamanızı Visual Studio 'daki Logic App Designer 'da açabilmek için önce Azure 'da zaten dağıtılmış bir Azure Kaynak grubunuz olması gerekir. Tasarımcı daha sonra mantıksal uygulamanızdaki kaynak ve hizmetlere bağlantılar oluşturabilir. Bu görev için, Visual Studio 'dan Azure portal çözümünüzü dağıtmak üzere aşağıdaki adımları izleyin:

1. Çözüm Gezgini, kaynak projenizin kısayol menüsünde**yeni** >  **Dağıt** ' ı seçin.

   ![Kaynak grubu için yeni dağıtım oluştur](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Henüz seçili değilse, Azure aboneliğinizi ve dağıtmak istediğiniz kaynak grubunu seçin. Ardından **Dağıt**' ı seçin.

   ![Dağıtım ayarları](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. **Parametreleri Düzenle** kutusu görüntülenirse, mantıksal uygulamanız ve dağıtım sırasında Azure işlev uygulamanız için kullanılacak kaynak adlarını sağlayın ve ardından ayarlarınızı kaydedin. İşlev uygulamanız için genel olarak benzersiz bir ad kullandığınızdan emin olun.

   ![Mantıksal uygulamanız ve işlev uygulamanız için adlar sağlayın](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Visual Studio, belirtilen kaynak grubunuza dağıtım başlattığında, çözümünüzün dağıtım durumu Visual Studio **çıktı** penceresinde görünür. 
   Dağıtım bittikten sonra mantıksal uygulamanız Azure portal canlı olur.

## <a name="edit-your-logic-app-in-visual-studio"></a>Visual Studio 'da mantıksal uygulamanızı düzenleme

Mantıksal uygulamanızı dağıtımdan sonra düzenlemek için, mantıksal uygulamanızı Visual Studio 'daki mantıksal uygulama Tasarımcısı ' nı kullanarak açın.

1. Çözüm Gezgini, azuredeploy. json dosyasının kısayol menüsünde, **Logic App Designer Ile aç**' ı seçin.

   ![Logic App Designer 'da azuredeploy. JSON açın](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Visual Studio 2019 ' de bu komuta sahip değilseniz, Visual Studio için en son güncelleştirmelere sahip olup olmadığınızı kontrol edin.

1. **Mantıksal uygulama özellikleri** kutusu görüntülendikten sonra, **abonelik**' ın altında, henüz seçili değilse Azure aboneliğinizi seçin. **Kaynak grubu**altında, çözümünüzü dağıttığınız kaynak grubunu ve konumu seçin ve ardından **Tamam**' ı seçin.

   ![Mantıksal uygulama özellikleri](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Mantıksal uygulama Tasarımcısı açıldıktan sonra adım ekleme veya iş akışını değiştirme ve güncelleştirmelerinizi kaydetme işlemine devam edebilirsiniz.

   ![Mantıksal uygulama tasarımcısında açılan mantıksal uygulama](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Azure Işlevleri projenizi oluşturma

JavaScript, Python, F#, PowerShell, Batch veya Bash kullanarak işlevler projenizi ve işlevinizi oluşturmak Için [Azure Functions Core Tools çalışma](../azure-functions/functions-run-local.md)içindeki adımları izleyin. Çözümünüz içinde kullanarak C# Azure işlevinizi geliştirmek için bir C# sınıf kitaplığı kullanın, bir [.NET sınıf kitaplığı işlev uygulaması olarak yayımlama](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)içindeki adımları izleyin.

## <a name="deploy-functions-from-visual-studio"></a>Visual Studio 'dan işlevleri dağıtma

Dağıtım şablonunuz, çözümünüzde bulunan tüm Azure işlevlerini azuredeploy. JSON dosyasındaki değişkenlerle belirtilen git deposundan dağıtır. Çözümünüzde Işlevlerinizi oluşturup yazardıysanız, söz konusu projeyi git kaynak denetimi (örneğin, GitHub veya Azure DevOps) olarak denetleyebilir ve sonra `repo` değişkenini, şablonun Azure işlevinizi dağıtabilmesi için güncelleştirebilirsiniz.

## <a name="manage-logic-apps-and-view-run-history"></a>Mantıksal uygulamaları yönetme ve çalıştırma geçmişini görüntüleme

Azure 'da zaten dağıtılmış olan Logic Apps için, bu uygulamaları Visual Studio 'dan düzenleyebilir, yönetebilir, görüntüleyebilirsiniz ve devre dışı bırakabilirsiniz.

1. Visual Studio 'daki **Görünüm** menüsünde, **Cloud Explorer**' ı açın.

1. **Tüm abonelikler**' in altında, yönetmek istediğiniz Logic Apps Ile ilişkili Azure aboneliğini seçin ve ardından **Uygula**' yı seçin.

1. **Logic Apps**altında mantıksal uygulamanızı seçin. Bu uygulamanın kısayol menüsünde, **mantıksal uygulama Düzenleyicisi Ile aç**' ı seçin.

   > [!TIP]
   > Visual Studio 2019 ' de bu komuta sahip değilseniz, Visual Studio için en son güncelleştirmelere sahip olup olmadığınızı kontrol edin.

Artık yayımlanmış olan mantıksal uygulamayı kaynak grubu projenize indirebilirsiniz. Bu nedenle, Azure portal bir mantıksal uygulama başlatmış olsanız da bu uygulamayı Visual Studio 'da içeri ve daha sonra yönetmeye devam edebilirsiniz. Daha fazla bilgi için bkz. [Visual Studio ile mantıksal uygulamaları yönetme](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Visual Studio ile mantıksal uygulamaları yönetme](manage-logic-apps-with-visual-studio.md)
