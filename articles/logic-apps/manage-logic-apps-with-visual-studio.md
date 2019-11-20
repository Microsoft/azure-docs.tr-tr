---
title: Visual Studio ile mantıksal uygulamaları yönetme-Azure Logic Apps
description: Visual Studio 'Yu Cloud Explorer ile kullanarak mantıksal uygulamaları ve diğer Azure varlıklarını yönetme
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.custom: mvc
ms.date: 10/29/2019
ms.openlocfilehash: e10683bcd5612db788d6dd5675425fec4130ffeb
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796464"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Visual Studio ile mantıksal uygulamaları yönetme

[Azure Portal](https://portal.azure.com)Logic Apps oluşturabilir, düzenleyebilir, yönetebilir ve dağıtabileceğiniz halde, mantıksal uygulamalarınızı kaynak denetimine eklemek, farklı sürümler yayınlamak ve için [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) şablonları oluşturmak istediğinizde Visual Studio 'yu da kullanabilirsiniz. çeşitli dağıtım ortamları. Visual Studio Cloud Explorer ile mantıksal uygulamalarınızı diğer Azure kaynaklarıyla birlikte bulabilir ve yönetebilirsiniz. Örneğin, Azure portal zaten dağıtılmış olan Logic Apps 'i açabilir, indirebilir, düzenleyebilir, çalıştırabilir, çalıştırma geçmişini görüntüleyebilir, devre dışı bırakabilir ve etkinleştirebilirsiniz. Visual Studio 'da Azure Logic Apps ile çalışmaya yeni başladıysanız, [Visual Studio ile Logic Apps oluşturmayı](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)öğrenin.

> [!IMPORTANT]
> Visual Studio 'dan bir mantıksal uygulama dağıtmak veya yayımlamak, Azure portal o uygulamanın sürümünün üzerine yazar. Korumak istediğiniz Azure portal değişiklik yaparsanız, Visual Studio 'da bir dahaki sefer dağıtmadan veya yayımlamadan önce [Visual Studio 'daki mantıksal uygulamayı Azure Portal yenilediğinizden](#refresh) emin olun.

<a name="requirements"></a>

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Henüz yoksa şu araçları indirip yükleyin:

  * [Visual Studio 2019, 2017 veya 2015-Community sürümü veya üzeri](https://aka.ms/download-visual-studio). Bu hızlı başlangıçta ücretsiz olan Visual Studio Community 2017 kullanılmaktadır.

    > [!IMPORTANT]
    > Visual Studio 2019 veya 2017 yüklediğinizde, **Azure geliştirme** iş yükünü seçtiğinizden emin olun.
    > Daha fazla bilgi için bkz. [Visual Studio Cloud Explorer 'Da Azure hesaplarınızla ilişkili kaynakları yönetme](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    Visual Studio 2015 için Cloud Explorer 'ı yüklemek için [Visual Studio Market bulut Gezgini](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)' ni indirin. Daha fazla bilgi için bkz. [Visual Studio Cloud Explorer 'Da Azure hesaplarınızla ilişkili kaynakları yönetme (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Azure SDK (2.9.1 veya üzeri)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * İstediğiniz sürüme yönelik Visual Studio uzantısı için en son Azure Logic Apps araçları:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Azure Logic Apps Araçlarını doğrudan Visual Studio Market’ten indirip yükleyebilir veya [bu uzantıyı Visual Studio’nun içinden yükleme](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions) hakkında bilgi edinebilirsiniz. Yükleme işlemini tamamladıktan sonra Visual Studio’yu yeniden başlattığınızdan emin olun.

* Katıştırılmış Logic Apps tasarımcısını kullanırken Web 'e erişim

  Tasarımcının Azure'da kaynak oluşturması ve mantıksal uygulamanızdaki bağlayıcılardan özellik ve verileri okuması için İnternet bağlantısı gerekir. Örneğin, Dynamics CRM Online bağlayıcısını kullanıyorsanız, tasarımcı CRM örneğinizdeki varsayılan ve özel kullanılabilir özellikleri denetler.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Mantıksal uygulamalarınızı bulun

Visual Studio 'da, Azure aboneliğinizle ilişkili olan ve bulut Gezgini kullanılarak Azure portal dağıtılan tüm mantık uygulamalarını bulabilirsiniz.

1. Visual Studio’yu açın. **Görünüm** menüsünde **bulut Gezgini**' ni seçin.

1. Cloud Explorer 'da **Hesap yönetimi**' ni seçin. Logic Apps ile ilişkili Azure aboneliğini seçin ve ardından **Uygula**' yı seçin. Örneğin:

   !["Hesap yönetimi" ni seçin](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. **Kaynak grupları** veya **kaynak türlerine**göre arama yapıp etmeksizin, şu adımları izleyin:

   * **Kaynak grupları**: Azure aboneliğiniz altında, Cloud Explorer bu abonelikle ilişkili tüm kaynak gruplarını gösterir. Mantıksal uygulamanızı içeren kaynak grubunu genişletin ve ardından mantıksal uygulamanızı seçin.

   * **Kaynak türleri**: Azure aboneliğiniz altında **Logic Apps**' ı genişletin. Cloud Explorer, aboneliğinizle ilişkili tüm dağıtılan mantıksal uygulamaları doldurduktan sonra mantıksal uygulamanızı seçin.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Visual Studio 'da aç

Visual Studio 'da, doğrudan Azure portal ya da Visual Studio ile Azure Kaynak grubu projeleri olarak oluşturulan ve dağıtılan Logic Apps 'i açabilirsiniz.

1. Cloud Explorer 'ı açın ve mantıksal uygulamanızı bulun.

1. Mantıksal uygulamanın kısayol menüsünde, **mantıksal uygulama Düzenleyicisi Ile aç**' ı seçin.

   > [!TIP]
   > Visual Studio 2019 ' de bu komuta sahip değilseniz, Visual Studio için en son güncelleştirmelere sahip olup olmadığınızı kontrol edin.

   Bu örnek, mantıksal uygulamaları kaynak türüne göre gösterir, bu nedenle mantıksal uygulamalarınız **Logic Apps** bölümü altında görünür.

   ![Dağıtılan mantıksal uygulamayı Azure portal açın](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Mantıksal uygulama Logic Apps tasarımcısında açıldıktan sonra, tasarımcı 'nın en altında, temel alınan mantıksal uygulama tanımı yapısını gözden geçirebilmeniz için **kod görünümü** ' nü seçebilirsiniz. Mantıksal uygulama için bir dağıtım şablonu oluşturmak istiyorsanız, bu mantıksal uygulama için [bir Azure Resource Manager şablonu indirmeyi](#download-logic-app) öğrenin. [Kaynak Yöneticisi şablonları](../azure-resource-manager/template-deployment-overview.md)hakkında daha fazla bilgi edinin.

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Azure 'dan indir

[Azure Portal](https://portal.azure.com) Logic Apps 'i indirebilir ve [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) şablon olarak kaydedebilirsiniz. Daha sonra şablonları Visual Studio ile yerel olarak düzenleyebilir ve farklı dağıtım ortamları için mantıksal uygulamaları özelleştirebilirsiniz.  Logic Apps 'in indirilmesi, JavaScript Nesne Gösterimi (JSON) de kullanan [Kaynak Yöneticisi şablonlarının](../azure-resource-manager/template-deployment-overview.md)tanımlarını otomatik olarak *parametreleştirir* .

1. Visual Studio 'da Cloud Explorer ' ı açın. Azure 'dan indirmek istediğiniz mantıksal uygulamayı bulun ve seçin.

1. Bu uygulamanın kısayol menüsünde, **mantıksal uygulama Düzenleyicisi Ile aç**' ı seçin.

   > [!TIP]
   > Visual Studio 2019 ' de bu komuta sahip değilseniz, Visual Studio için en son güncelleştirmelere sahip olup olmadığınızı kontrol edin.

   Mantıksal uygulama Tasarımcısı açılır ve mantıksal uygulamayı gösterir. Mantıksal uygulamanın temel tanım ve yapısını gözden geçirmek için, tasarımcının alt kısmındaki **kod görünümü**' nü seçin.

1. Tasarımcı araç çubuğunda **İndir**' i seçin.

   ![Azure portal mantıksal uygulamayı indirin](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Bir konum istendiğinde, bu konuma gidin ve mantıksal uygulama tanımının Kaynak Yöneticisi şablonunu JSON (. JSON) dosya biçiminde kaydedin.

   Mantıksal uygulama tanımınız, Kaynak Yöneticisi şablonunun içindeki `resources` alt bölümünde görüntülenir. Artık mantıksal uygulama tanımını ve Kaynak Yöneticisi şablonu Visual Studio ile düzenleyebilirsiniz. Ayrıca, şablonu bir [Azure Kaynak grubu projesi](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) olarak bir Visual Studio çözümüne ekleyebilirsiniz. [Visual Studio 'da mantıksal uygulamalar Için Azure Kaynak grubu projeleri](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)hakkında bilgi edinin.

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Tümleştirme hesabı bağlantısı

İşletmeden işletmeye (B2B) kurumsal tümleştirme senaryolarına yönelik mantıksal uygulamalar oluşturmak için mantıksal uygulamanızı mantıksal uygulamanızla aynı bölgede bulunan daha önce oluşturulmuş bir [tümleştirme hesabına](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) bağlayabilirsiniz. Tümleştirme hesabı, ticari iş ortakları, sözleşmeler, şemalar ve haritalar gibi B2B yapıtları içerir ve mantıksal uygulamanızın XML doğrulaması ve düz dosya kodlama veya kod çözme için B2B bağlayıcıları kullanmasına olanak sağlar. [Bu bağlantıyı Azure Portal kullanarak oluşturabilseniz](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)de, [önkoşulları](#requirements)yükselttikten sonra Visual Studio 'yu da kullanabilirsiniz ve mantıksal uygulamanız bir [Azure Kaynak grubu projesinde](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)JSON (. JSON) dosyası olarak mevcuttur. [Visual Studio 'da mantıksal uygulamalar Için Azure Kaynak grubu projeleri](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project)hakkında bilgi edinin.

1. Visual Studio 'da mantıksal uygulamanızı içeren Azure Kaynak grubu projesini açın.

1. Çözüm Gezgini ' de, **< Logic-app-name >. JSON** dosyasının kısayol menüsünü açın ve **Logic App Designer ile aç**' ı seçin. (Klavye: Ctrl + L)

   ![Mantıksal uygulama Tasarımcısı ile mantıksal uygulamanın. json dosyasını açın](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Visual Studio 2019 ' de bu komuta sahip değilseniz, Visual Studio ve Azure Logic Apps araçları uzantısı için en son güncelleştirmelere sahip olup olmadığınızı kontrol edin.

1. Mantıksal uygulama Tasarımcısı 'nın, Özellikler penceresi mantıksal uygulamanızın **tümleştirme hesabı** özelliğini görebilmesi için tasarımcı sekmesini veya yüzeyini seçerek bu mantığı odakladığınızdan emin olun.

   ![Özellikler penceresi-"Integration Account" özelliği](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Özellikler penceresi zaten açık değilse, **Görünüm** menüsünden **Özellikler penceresi**' ni seçin. (Klavye: F4 tuşuna basın)

1. **Tümleştirme hesabı** Özellik listesini açın ve mantıksal uygulamanıza bağlamak istediğiniz tümleştirme hesabını seçin; örneğin:

   !["Tümleştirme hesabı" Özellik listesini aç](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. İşiniz bittiğinde, Visual Studio çözümünüzü kaydetmeyi unutmayın.

Visual Studio 'da **Integration Account** özelliğini ayarlayıp mantıksal uygulamanızı bir Azure Resource Manager şablonu olarak kaydettiğinizde, bu şablon seçili tümleştirme hesabı için de bir parametre bildirimi içerir. Şablon parametreleri ve Logic Apps hakkında daha fazla bilgi için bkz. [genel bakış: mantıksal uygulama dağıtımını otomatikleştirme](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Dağıtım konumunu değiştir

Visual Studio 'da, mantıksal uygulamanız dağıtımı otomatikleştirmek için kullandığınız bir [Azure Kaynak grubu PROJESINDE](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) JSON (. JSON) dosyası olarak mevcutsa, bu mantıksal uygulama bir konum türüne ve belirli bir konuma ayarlanır. Bu konum bir Azure bölgesi veya mevcut bir [tümleştirme hizmeti ortamıdır (ISE)](connect-virtual-network-vnet-isolated-environment.md).

Mantıksal uygulamanızın konum türünü veya konumunu değiştirmek için mantıksal uygulama Tasarımcısı ' nı kullanarak mantıksal uygulamanızın iş akışı Tanım (. JSON) dosyasını Çözüm Gezgini ' den açmanız gerekir. Bulut Gezgini 'ni kullanarak bu özellikleri değiştiremezsiniz.

> [!IMPORTANT]
> Konum türünü **bölgesinden** [**tümleştirme hizmeti ortamı**](connect-virtual-network-vnet-isolated-environment-overview.md) değiştirmek, mantıksal uygulamanızın faturalandırma, [sınırlar](logic-apps-limits-and-config.md#integration-account-limits), [tümleştirme hesabı desteği](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)vb. için kullanılan [fiyatlandırma modelini](logic-apps-pricing.md#fixed-pricing) etkiler. Farklı bir konum türü seçmeden önce, mantıksal uygulamanızda ortaya çıkan etkiyi anladığınızdan emin olun.

1. Visual Studio 'da mantıksal uygulamanızı içeren Azure Kaynak grubu projesini açın.

1. Çözüm Gezgini, `<logic-app-name>.json` dosyanın kısayol menüsünü açın ve **Logic App Designer Ile aç**' ı seçin. (Klavye: Ctrl + L)

   ![Mantıksal uygulama Tasarımcısı ile mantıksal uygulamanın. json dosyasını açın](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Visual Studio 2019 ' de bu komuta sahip değilseniz, Visual Studio ve Azure Logic Apps araçları uzantısı için en son güncelleştirmelere sahip olup olmadığınızı kontrol edin.

1. Mantıksal uygulama Tasarımcısı 'nın, Özellikler penceresi mantıksal uygulamanızın **konum türünü** ve **konum** özelliklerini seçmesini sağlamak üzere Tasarımcı sekmesini veya yüzeyini seçerek odağın olduğundan emin olun. Projenin konum türü **bölge** veya **tümleştirme hizmeti ortamı**olarak ayarlanır.

   ![Özellikler penceresi-"konum türünü seçin" & "konum" özellikleri](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Özellikler penceresi zaten açık değilse, **Görünüm** menüsünden **Özellikler penceresi**' ni seçin. (Klavye: F4 tuşuna basın)

1. Konum türünü değiştirmek için **konum türünü seçin** Özellik listesini açın ve istediğiniz konum türünü seçin.

   Örneğin, konum türü **tümleştirme hizmeti ortamı**, **bölge**' yi seçebilirsiniz.

   !["Konum türünü seçin" özelliği-konum türünü değiştir](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Belirli konumu değiştirmek için **konum** Özellik listesini açın. Konum türü temelinde, istediğiniz konumu seçin, örneğin:

   * Farklı bir Azure bölgesi seçin:

     !["Konum" Özellik listesini açın, başka bir Azure bölgesi seçin](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Farklı bir ıSE seçin:

     !["Konum" Özellik listesini açın, başka bir ıSE seçin](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. İşiniz bittiğinde, Visual Studio çözümünüzü kaydetmeyi unutmayın.

Visual Studio 'da konum türünü veya konumunu değiştirirken ve mantıksal uygulamanızı bir Azure Resource Manager şablonu olarak kaydettiğinizde, bu şablon o konum türü ve konumu için de parametre bildirimleri içerir. Şablon parametreleri ve Logic Apps hakkında daha fazla bilgi için bkz. [genel bakış: mantıksal uygulama dağıtımını otomatikleştirme](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Azure 'dan Yenile

Azure portal mantıksal uygulamanızı düzenler ve bu değişiklikleri tutmak istiyorsanız, Visual Studio 'da bu değişikliklerle bu uygulamanın sürümünü yenilediğinizden emin olun.

* Visual Studio 'da mantıksal uygulama Tasarımcısı araç çubuğunda **Yenile**' yi seçin.

  -veya-

* Visual Studio Cloud Explorer 'da mantıksal uygulamanızın kısayol menüsünü açın ve **Yenile**' yi seçin.

![Mantıksal uygulamayı güncelleştirmelerle Yenile](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Mantıksal uygulama güncelleştirmelerini Yayımla

Mantıksal uygulama güncelleştirmelerinizi Visual Studio 'dan Azure 'a dağıtmaya hazırsanız, mantıksal uygulama Tasarımcısı araç çubuğunda **Yayımla**' yı seçin.

![Güncelleştirilmiş mantıksal uygulamayı Azure portal yayımlayın](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Mantıksal uygulamanızı el ile çalıştırma

Azure 'da dağıtılan bir mantıksal uygulamayı Visual Studio 'dan el ile tetikleyebilirsiniz. Mantıksal uygulama Tasarımcısı araç çubuğunda **tetikleyiciyi Çalıştır**' ı seçin.

![Mantıksal uygulamanız için tetikleyiciyi el ile çalıştırma](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Çalıştırma geçmişini gözden geçirme

Mantıksal uygulama çalıştırmaları ile ilgili durumu denetlemek ve sorunları tanılamak için, Visual Studio 'da bu çalıştırmalar için girişler ve çıktılar gibi ayrıntıları gözden geçirebilirsiniz.

1. Cloud Explorer 'da mantıksal uygulamanızın kısayol menüsünü açın ve **çalıştırma geçmişini aç**' ı seçin.

   ![Mantıksal uygulamanız için çalıştırma geçmişini açma](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Belirli bir çalıştırmanın ayrıntılarını görüntülemek için, bir çalıştırmaya çift tıklayın. Örneğin:

   ![Belirli bir çalıştırma hakkındaki bilgileri görüntüleme](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Tabloyu özelliğe göre sıralamak için bu özelliğin sütun başlığını seçin.

1. Giriş ve çıkışları gözden geçirmek istediğiniz adımları genişletin, örneğin:

   ![Her adımın giriş ve çıkışlarını görüntüleme](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Mantıksal uygulamayı devre dışı bırakma veya etkinleştirme

Mantıksal uygulamanızı silmeden tetikleyiciyi, Tetikleme koşulu karşılandığında bir sonraki sefer tetiklemeden durdurabilirsiniz. Mantıksal uygulamanızı devre dışı bırakmak, Logic Apps altyapısının mantıksal uygulamanız için gelecekteki iş akışı örnekleri oluşturmasını ve çalıştırmasını engeller. Cloud Explorer 'da mantıksal uygulamanızın kısayol menüsünü açın ve **devre dışı bırak**' ı seçin.

![Cloud Explorer 'da mantıksal uygulamanızı devre dışı bırakma](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Bir mantıksal uygulamayı devre dışı bıraktığınızda, yeni çalıştırmalar örneği oluşturulur. Tüm devam eden ve bekleyen çalıştırmalar tamamlanana kadar devam eder, bu da tamamlanması zaman alabilir.

Mantıksal uygulamanızı yeniden etkinleştirmek için, Cloud Explorer 'da mantıksal uygulamanızın kısayol menüsünü açın ve **Etkinleştir**' i seçin.

![Bulut Gezgininde mantıksal uygulamayı etkinleştirme](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Mantıksal uygulamanızı silme

Mantıksal uygulamanızı Azure portal silmek için, Cloud Explorer 'da mantıksal uygulamanızın kısayol menüsünü açın ve **Sil**' i seçin.

![Azure portal mantıksal uygulamanızı silme](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Mantıksal uygulamayı sildiğinizde yeni çalıştırma başlatılmaz. Devam eden ve bekleme durumunda olan tüm çalıştırmalar iptal edilir. Binlerce çalıştırma varsa iptal işleminin tamamlanması zaman alabilir. 

## <a name="troubleshooting"></a>Sorun giderme

Logic Apps tasarımcısında mantıksal uygulama projenizi açtığınızda, Azure aboneliğinizi seçme seçeneğini de alamıyoruz. Bunun yerine, mantıksal uygulamanız kullanmak istediğiniz bir Azure aboneliği ile açılır. Bu davranış, bir mantıksal uygulamanın. json dosyasını açtıktan sonra, sonraki kullanımlar için seçili olan ilk aboneliği önbelleğe aldığından oluşur. Bu sorunu çözmek için şu adımlardan birini deneyin:

* Mantıksal uygulamanın. json dosyasını yeniden adlandırın. Abonelik önbelleği dosya adına bağlıdır.

* Çözümünüzdeki *Tüm* mantıksal uygulamalar için daha önce seçilen abonelikleri kaldırmak üzere çözümünüzün dizinindeki gizli Visual Studio ayarları klasörünü (. vs) silin. Bu konum, abonelik bilgilerinizi depolar.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, dağıtılan mantıksal uygulamaları Visual Studio ile nasıl yönetebileceğinizi öğrendiniz. Ardından, dağıtım için mantıksal uygulama tanımlarını özelleştirme hakkında bilgi edinin:

> [!div class="nextstepaction"]
> [JSON 'da mantıksal uygulama tanımları yazma](../logic-apps/logic-apps-author-definitions.md)
