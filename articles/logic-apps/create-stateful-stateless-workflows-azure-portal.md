---
title: Azure portal Logic Apps önizlemesi iş akışları oluşturun
description: Azure portal Azure Logic Apps önizlemesiyle Otomasyon ve tümleştirme senaryoları için iş akışları oluşturun ve çalıştırın.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: ff938d29d998b6fcf0b2cfae72a9a9e685a10dc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563973"
---
# <a name="create-stateful-and-stateless-workflows-in-the-azure-portal-with-azure-logic-apps-preview"></a>Azure Logic Apps önizlemesiyle Azure portal durum bilgisiz ve durum bilgisi olmayan iş akışları oluşturma

> [!IMPORTANT]
> Bu özellik genel önizleme aşamasındadır, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure Logic Apps önizlemesi](logic-apps-overview-preview.md)sayesinde, yeni **mantıksal uygulama (Önizleme)** kaynak türü ile başlayarak, Azure Portal [ *durum* *bilgisiz ve durum bilgisi* olmayan iş akışlarını](logic-apps-overview-preview.md#stateful-stateless) içeren mantıksal uygulamalar oluşturup çalıştırarak, uygulamalar, veriler, bulut hizmetleri ve sistemler arasında Otomasyon ve tümleştirme çözümleri oluşturabilirsiniz. Bu yeni mantıksal uygulama türü ile, yalnızca Azure, ancak Docker Kapsayıcıları değil çeşitli barındırma ortamlarında dağıtım ve çalıştırma için taşınabilirlik, daha iyi performans ve esneklik sağlayan, yeniden tasarlanan Azure Logic Apps Önizleme çalışma zamanı tarafından desteklenen birden çok iş akışı oluşturabilirsiniz. Yeni mantıksal uygulama türü hakkında daha fazla bilgi edinmek için bkz. [Azure Logic Apps önizlemesi Için genel bakış](logic-apps-overview-preview.md).

!["Logic App (Önizleme)" kaynağı için iş akışı Tasarımcısı ile Azure portal gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/azure-portal-logic-apps-overview.png)

Azure portal yeni bir **mantıksal uygulama (Önizleme)** kaynağı oluşturarak başlayabilirsiniz. Ayrıca, [Azure Logic Apps (Önizleme) uzantısıyla Visual Studio Code bir proje oluşturarak](create-stateful-stateless-workflows-visual-studio-code.md)da başlatabileceğiniz gibi, her iki yaklaşım da mantıksal uygulamanızı aynı barındırma ortamları türlerinde dağıtmanıza ve çalıştırmanıza olanak sağlar.

Bu arada, özgün mantıksal uygulama türünü yine de oluşturabilirsiniz. Portalda geliştirme deneyimleri orijinal ve yeni mantıksal uygulama türleri arasında farklılık gösterse de Azure aboneliğiniz her iki türü de içerebilir. Azure aboneliğinizde dağıtılan tüm mantıksal uygulamaları görüntüleyebilir ve bunlara erişebilirsiniz, ancak uygulamalar kendi kategorilerine ve bölümlerine göre düzenlenir.

Bu makalede **mantıksal uygulama (Önizleme)** kaynak türünü kullanarak ve bu üst düzey görevleri gerçekleştirerek Azure Portal mantıksal uygulamanızı ve iş akışınızı nasıl oluşturabileceğiniz gösterilmektedir:

* Yeni mantıksal uygulama kaynağını oluşturun ve boş bir iş akışı ekleyin.

* Tetikleyici ve eylem ekleyin.

* İş akışı çalıştırmasını tetikleyin.

* İş akışının çalıştırma ve tetikleme geçmişini görüntüleyin.

* Dağıtımdan sonra Application Insights etkinleştirin veya açın.

* Durum bilgisiz iş akışları için çalıştırma geçmişini etkinleştirin.

> [!NOTE]
> Bilinen güncel sorunlar hakkında daha fazla bilgi için [GitHub 'Daki bilinen sorunlar Logic Apps Genel Önizleme sayfasını](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure hesabı ve aboneliği Aboneliğiniz yoksa, [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* **Mantıksal uygulama (Önizleme)** kaynağı Azure işlevleri tarafından desteklenmediğinden ve [işlev uygulamalarına benzer depolama gereksinimlerine](../azure-functions/storage-considerations.md)sahip olduğu için bir [Azure depolama hesabı](../storage/common/storage-account-overview.md) . Mevcut bir depolama hesabını kullanabilir veya mantıksal uygulama oluşturma sırasında önceden veya bir depolama hesabı oluşturabilirsiniz.

  > [!NOTE]
  > [Durum bilgisi olan Logic Apps](logic-apps-overview-preview.md#stateful-stateless) , tablolarda ve bloblarda iş akışı durumlarını zamanlamak ve depolamak için kuyrukları kullanma gibi depolama işlemleri gerçekleştirir. Bu işlemler [Azure depolama ücretlerine](https://azure.microsoft.com/pricing/details/storage/)neden olacak. Durum bilgisi olan Logic Apps 'in verileri dış depolamaya nasıl depolamalarına ilişkin daha fazla bilgi için bkz. [durum bilgisiz](logic-apps-overview-preview.md#stateful-stateless)ve

* Bir Docker kapsayıcısına dağıtmak için, mevcut bir Docker kapsayıcı görüntüsüne ihtiyacınız vardır. Örneğin, bu görüntüyü [Azure Container Registry](../container-registry/container-registry-intro.md), [App Service](../app-service/overview.md)veya [Azure Container Instance](../container-instances/container-instances-overview.md)aracılığıyla oluşturabilirsiniz. 

* Bu makalede aynı örnek mantıksal uygulamayı oluşturmak için, oturum açmak için Microsoft iş veya okul hesabı kullanan bir Office 365 Outlook e-posta hesabınız olması gerekir.

  Outlook.com veya [Gmail](../connectors/connectors-google-data-security-privacy-policy.md)gibi [Azure Logic Apps tarafından desteklenen farklı bir e-posta Bağlayıcısı](/connectors/)kullanmayı tercih ediyorsanız, örneği yine de izleyebilirsiniz ve genel adımlar aynıdır, ancak Kullanıcı arabiriminiz ve seçenekleriniz bazı yollarla farklılık gösterebilir. Örneğin, Outlook.com bağlayıcısını kullanıyorsanız, oturum açmak için kişisel Microsoft hesabı kullanın.

* Bu makalede oluşturduğunuz örnek mantıksal uygulamayı test etmek için, örnek Logic App 'teki ilk adım olan Istek tetikleyicisine çağrılar gönderebilen bir araca ihtiyacınız vardır. Böyle bir aracınız yoksa [Postman](https://www.postman.com/downloads/)'ı indirebilir, yükleyebilir ve kullanabilirsiniz.

* Mantıksal uygulamanızı [Application Insights](../azure-monitor/app/app-insights-overview.md)kullanmayı destekleyen ayarlarla oluşturursanız, mantıksal uygulamanız için isteğe bağlı olarak tanılama günlüğü ve izlemeyi etkinleştirebilirsiniz. Bunu, mantıksal uygulamanızı oluştururken veya dağıtımdan sonra yapabilirsiniz. Bir Application Insights örneğiniz olması gerekir, ancak mantıksal uygulamanızı oluştururken ya da dağıtımdan sonra bu kaynağı [önceden](../azure-monitor/app/create-workspace-resource.md)oluşturabilirsiniz.

## <a name="create-the-logic-app-resource"></a>Mantıksal uygulama kaynağı oluşturma

1. Azure hesabınızın kimlik bilgileriyle [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure portal arama kutusuna `logic app preview` **mantıksal uygulama (Önizleme)** öğesini girin ve seçin.

   !["Mantıksal uygulama önizlemesi" arama terimi ve "Logic App (Önizleme)" kaynağının seçili olduğu Azure portal arama kutusunu gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/find-logic-app-resource-template.png)

1. **Mantıksal uygulama (Önizleme)** sayfasında **Ekle**' yi seçin.

1. **Mantıksal uygulama oluştur (Önizleme)** sayfasında, **temel bilgiler** sekmesinde, mantıksal uygulamanızla ilgili bu bilgileri sağlayın.

   | Özellik | Gerekli | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Abonelik** | Yes | <*Azure-abonelik-adı*> | Mantıksal uygulamanız için kullanılacak Azure aboneliği. |
   | **Kaynak grubu** | Yes | <*Azure-Resource-Group-Name*> | Mantıksal uygulamanızı ve ilgili kaynaklarınızı oluşturduğunuz Azure Kaynak grubu. Bu kaynak adı bölgeler genelinde benzersiz olmalıdır ve yalnızca harf, rakam, kısa çizgi ( **-** ), alt çizgi (**_**), parantez (**()**) ve nokta (**.**) içerebilir. <p><p>Bu örnek adlı bir kaynak grubu oluşturur `Fabrikam-Workflows-RG` . |
   | **Mantıksal uygulama adı** | Yes | <*Logic-App-adı*> | Mantıksal uygulamanız için kullanılacak ad. Bu kaynak adı bölgeler genelinde benzersiz olmalıdır ve yalnızca harf, rakam, kısa çizgi ( **-** ), alt çizgi (**_**), parantez (**()**) ve nokta (**.**) içerebilir. <p><p>Bu örnek adlı bir mantıksal uygulama oluşturur `Fabrikam-Workflows` . <p><p>**Note**: mantıksal uygulama `.azurewebsites.net` **(Önizleme)** kaynağı, aynı uygulama adlandırma kuralını kullanan Azure işlevleri tarafından desteklenen mantıksal uygulamanızın adı otomatik olarak alınır. |
   | **Yayımla** | Yes | <*Dağıtım-ortam*> | Mantıksal uygulamanız için dağıtım hedefi. **Iş akışı** veya **Docker kapsayıcısı**' nı seçerek Azure 'a dağıtım yapabilirsiniz. <p><p>Bu örnek, **mantıksal uygulama (Önizleme)** kaynağını Azure Portal dağıtan **iş akışını** kullanır. <p><p>**Note**: **Docker kapsayıcısını** seçmeden önce, Docker kapsayıcı görüntünüzü oluşturma ' nın seçildiğinden emin olun. Örneğin, bu görüntüyü [Azure Container Registry](../container-registry/container-registry-intro.md), [App Service](../app-service/overview.md)veya [Azure Container Instance](../container-instances/container-instances-overview.md)aracılığıyla oluşturabilirsiniz. Bu şekilde, **Docker kapsayıcısını** seçtikten sonra [mantıksal uygulamanızın ayarlarında kullanmak istediğiniz kapsayıcıyı belirtebilirsiniz](#set-docker-container). |
   | **Bölge** | Yes | <*Azure-bölge*> | Kaynak grubunuz ve kaynaklarınız oluşturulurken kullanılacak Azure bölgesi. <p><p>Bu örnek **Batı ABD** kullanır. |
   |||||

   Aşağıda bir örnek verilmiştir:

   ![Azure portal ve "mantıksal uygulama oluşturma (Önizleme)" sayfasını gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/create-logic-app-resource-portal.png)

1. Daha sonra **barındırma** sekmesinde, mantıksal uygulamanız için kullanılacak depolama çözümü ve barındırma planı hakkında bu bilgileri sağlayın.

   | Özellik | Gerekli | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Depolama hesabı** | Yes | <*Azure-Storage-Account-Name*> | Depolama işlemleri için kullanılacak [Azure depolama hesabı](../storage/common/storage-account-overview.md) . Bu kaynak adı bölgeler genelinde benzersiz olmalıdır ve yalnızca rakamlar ve küçük harflerden oluşan 3-24 karakter içermelidir. Mevcut bir hesap seçin veya yeni hesap oluşturun. <p><p>Bu örnek adlı bir depolama hesabı oluşturur `fabrikamstorageacct` . |
   | **Plan türü** | Yes | <*Azure-barındırma-plan*> | Mantıksal uygulamanızı dağıtmak için kullanılan [barındırma planı](../app-service/overview-hosting-plans.md) , Premium veya [ **App Service planı** (adanmış)](../azure-functions/dedicated-plan.md) [**işlevleridir**](../azure-functions/functions-premium-plan.md) . Seçiminiz, daha sonra kullanabileceğiniz özellikleri ve fiyatlandırma katmanlarını etkiler. <p><p>Bu örnek, **App Service planını** kullanır. <p><p>**Note**: Azure işlevlerine benzer şekilde, **mantıksal uygulama (Önizleme)** kaynak türü bir barındırma planı ve fiyatlandırma katmanı gerektirir. Tüketim planları bu kaynak türü için desteklenmiyor veya kullanılamıyor. Daha fazla bilgi için şu konuları gözden geçirin: <p><p>- [Azure Işlevleri ölçeklendirme ve barındırma](../azure-functions/functions-scale.md) <br>- [App Service fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/app-service/) <p><p>Örneğin, Premium plan Işlevleri, mantıksal uygulamalarınızı oluştururken ve dağıtırken Azure Işlevlerine benzer şekilde, Azure sanal ağlarla özel olarak bağlanma ve tümleştirme gibi ağ özelliklerine erişim sağlar. Daha fazla bilgi için şu konuları gözden geçirin: <p><p>- [Azure Işlevleri ağ seçenekleri](../azure-functions/functions-networking-options.md) <br>- [Azure Logic Apps önizlemesiyle her yerde ağ olasılıklarını çalıştırmak Azure Logic Apps](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047) |
   | **Windows Planı** | Yes | <*Plan adı*> | Kullanılacak plan adı. Mevcut bir planı seçin ya da yeni bir plan için adı belirtin. <p><p>Bu örnek, adını kullanır `Fabrikam-Service-Plan` . |
   | **SKU ve boyut** | Yes | <*Fiyatlandırma Katmanı*> | Mantıksal uygulamanızı barındırmak için kullanılacak [fiyatlandırma katmanı](../app-service/overview-hosting-plans.md) . Seçenekleriniz, daha önce seçtiğiniz plan türünden etkilenir. Varsayılan katmanı değiştirmek için **boyutu Değiştir**' i seçin. Daha sonra, ihtiyacınız olan iş yüküne göre diğer fiyatlandırma katmanlarını seçebilirsiniz. <p><p>Bu örnek, **geliştirme ve test** iş yükleri Için ücretsiz **F1 fiyatlandırma katmanını** kullanır. Daha fazla bilgi için [App Service fiyatlandırma ayrıntılarını](https://azure.microsoft.com/pricing/details/app-service/)gözden geçirin. |
   |||||

1. Daha sonra, oluşturma ve dağıtım ayarlarınız [Application Insights](../azure-monitor/app/app-insights-overview.md)kullanmayı destekliyorsa, mantıksal uygulamanız için isteğe bağlı olarak tanılama günlüğü ve izlemeyi etkinleştirebilirsiniz.

   1. **İzleme** sekmesinde, **Application Insights** altında, henüz seçili değilse, **Etkinleştir Application Insights** seçeneğini **Evet** olarak ayarlayın.

   1. **Application Insights** ayarı için, var olan bir Application Insights örneğini seçin ya da yeni bir örnek oluşturmak Istiyorsanız **Yeni oluştur** ' u seçin ve kullanmak istediğiniz adı belirtin.

1. Azure mantıksal uygulamanızın ayarlarını doğruladıktan sonra, **gözden geçir + oluştur** sekmesinde **Oluştur**' u seçin.

   Örnek:

   ![Azure portal ve yeni mantıksal uygulama kaynak ayarlarını gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/check-logic-app-resource-settings.png)

   > [!TIP]
   > **Oluştur**' u seçtikten sonra doğrulama hatası alırsanız, hata ayrıntılarını açın ve gözden geçirin. Örneğin, seçili bölgeniz oluşturmaya çalıştığınız kaynaklar için bir kotaya ulaşırsa, farklı bir bölge denemeniz gerekebilir.

   Azure dağıtımı tamamladıktan sonra mantıksal uygulamanız otomatik olarak canlı çalışır ve çalışır, ancak hiçbir iş akışı bulunmadığından hiçbir şey yapmaz.

1. Dağıtım tamamlama sayfasında, iş akışınızı oluşturmaya başlayabilmeniz **için kaynağa git** ' i seçin. Mantıksal uygulamanızı dağıtmak için **Docker kapsayıcısı** ' nı seçtiyseniz, [Bu Docker kapsayıcısı hakkında bilgi sağlamak için adımlarla](#set-docker-container)devam edin.

   ![Azure portal ve tamamlanmış dağıtımı gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="set-docker-container"></a>

## <a name="specify-docker-container-for-deployment"></a>Dağıtım için Docker kapsayıcısını belirtin

Bu adımlara başlamadan önce bir Docker kapsayıcı görüntüsüne ihtiyacınız vardır. Örneğin, bu görüntüyü [Azure Container Registry](../container-registry/container-registry-intro.md), [App Service](../app-service/overview.md)veya [Azure Container Instance](../container-instances/container-instances-overview.md)aracılığıyla oluşturabilirsiniz. Daha sonra mantıksal uygulamanızı oluşturduktan sonra Docker Kapsayıcınız hakkında bilgi sağlayabilirsiniz.

1. Azure portal, mantıksal uygulama kaynağınız ' ne gidin.

1. Mantıksal uygulama menüsünde, **Ayarlar** altında, **Dağıtım Merkezi**' ni seçin.

1. **Dağıtım Merkezi** bölmesinde, Docker kapsayıcının ayrıntılarını sağlamak ve yönetmek için yönergeleri izleyin.

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>Boş bir iş akışı ekleyin

1. Azure, kaynağı açtıktan sonra mantıksal uygulamanızın menüsünde **Iş akışları**' nı seçin. **Iş akışları** araç çubuğunda **Ekle**' yi seçin.

   ![Mantıksal uygulama kaynağı menüsünü "Iş akışları" seçiliyken gösteren ekran görüntüsü ve sonra araç çubuğunda "Ekle" seçilidir.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. **Yeni iş akışı** bölmesi açıldıktan sonra, iş akışınız için bir ad sağlayın ve [ **durum bilgisi olan** veya **durum bilgisiz**](logic-apps-overview-preview.md#stateful-stateless) iş akışı türünü seçin. İşiniz bittiğinde **Oluştur**'u seçin.

   Bu örnek adlı boş bir durum bilgisi içeren iş akışı ekler `Fabrikam-Stateful-Workflow` . Varsayılan olarak, iş akışı etkindir ancak bir tetikleyici ve eylem ekleyene kadar hiçbir şey yapmaz.

   ![Yeni eklenen boş durum bilgisi olan "Fabrikam-Stateful-Workflow" iş akışını gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. Sonra, bir tetikleyici ve eylem ekleyebilmeniz için tasarımcıda boş iş akışını açın.

   1. İş akışı listesinden boş iş akışını seçin.

   1. İş akışı menüsünde, **Geliştirici** altında **Tasarımcı**' yı seçin.

      Tasarımcı yüzeyinde **bir Işlem seçin** istemi zaten görünür ve varsayılan olarak seçilidir ve bu sayede **tetikleyici Ekle** bölmesi de açık görünür.

      ![Tasarımcı yüzeyinde "işlem seçin" seçiliyken açık iş akışı tasarımcısını gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>Tetikleyici ve eylem ekleme

Bu örnek, şu adımları içeren bir iş akışı oluşturur:

* Gelen çağrıları veya istekleri alan ve diğer hizmetlerin veya mantıksal uygulamaların çağırabileceği bir uç nokta oluşturan **BIR http isteği alındığında** yerleşik [istek tetikleyicisi](../connectors/connectors-native-reqres.md).

* [Office 365 Outlook eylemi](../connectors/connectors-create-api-office365-outlook.md), **bir e-posta gönderin**.

* Bir yanıt göndermek ve verileri çağırana geri döndürmek için kullandığınız yerleşik [yanıt eylemi](../connectors/connectors-native-reqres.md).

### <a name="add-the-request-trigger"></a>Istek tetikleyicisini ekleme

Boş bir iş akışına tetikleyici ekleyebilmek için önce iş akışı tasarımcısının açık olduğundan ve tasarımcı yüzeyinde **bir Işlem seçme** isteminin seçili olduğundan emin olun.

1. Tasarımcı yüzeyi ' nün yanındaki **tetikleyici Ekle** bölmesindeki **bir işlem araması seçin** kutusunda **yerleşik** sekmesinin seçili olduğundan emin olun. Bu sekmede Azure Logic Apps yerel olarak çalışan Tetikleyiciler gösterilmektedir.

1. **Bir işlem araması seçin** kutusunda `when a http request` , gırın ve **bir http Isteği alındığında** adlandırılan yerleşik istek tetikleyicisini seçin.

   ![Tasarımcıyı gösteren ekran görüntüsü ve * * "bir HTTP isteği alındığında" tetikleyicisi seçiliyken "bir tetikleyici Ekle * * bölmesi.](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger.png)

   Tetikleyici tasarımcıda göründüğünde, tetikleyicinin özellikler, ayarlar ve diğer eylemleri göstermek için tetikleyicinin Ayrıntılar bölmesi açılır.

   ![Tasarımcıyı "bir HTTP isteği alındığında" tetikleyicisi seçiliyken ve tetikleyici ayrıntıları bölmesini açan bir ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > Ayrıntılar bölmesi görünmezse, tetikleyicinin tasarımcıda seçili olduğundan emin olur.

1. Tasarımcıdan bir öğe silmeniz gerekiyorsa, [öğeleri tasarımcıdan silmek için aşağıdaki adımları izleyin](#delete-from-designer).

1. Çalışmanızı kaydetmek için tasarımcı araç çubuğunda **Kaydet**' i seçin.

   Bir iş akışını ilk kez kaydettiğinizde ve bu iş akışı bir Istek tetikleyicisi ile başlıyorsa, Logic Apps hizmeti, Istek tetikleyicisi tarafından oluşturulan bir uç nokta için otomatik olarak bir URL oluşturur. Daha sonra, iş akışınızı test ettiğinizde, bu URL 'ye bir istek gönderirsiniz ve bu da tetikleyiciyi harekete geçirilir ve iş akışı çalıştırmasını başlatır.

### <a name="add-the-office-365-outlook-action"></a>Office 365 Outlook eylemini ekleme

1. Tasarımcıda, eklediğiniz tetikleyicinin altında **yeni adım**' ı seçin.

   **Bir Işlem seçme** istemi tasarımcıda görünür ve bir sonraki eylemi seçebilmeniz Için **Eylem Ekle** bölmesi yeniden açılır.

   > [!NOTE]
   > **Eylem Ekle** bölmesi hata iletisini gösteriyorsa, ' ' ın tanımsız ' özelliği okunamıyor, iş akışınızı kaydedin, sayfayı yeniden yükleyin, iş akışınızı yeniden açın ve yeniden deneyin.

1. **Eylem Ekle** bölmesindeki **bir işlem seçin** arama kutusunda **Azure**' ı seçin. Bu sekmede, Azure 'da kullanılabilen ve dağıtılan yönetilen bağlayıcılar gösterilmektedir.

   > [!NOTE]
   > **Eylem Ekle** bölmesi hata iletisini gösteriyorsa, `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` iş akışınızı kaydedin, sayfayı yeniden yükleyin, iş akışınızı yeniden açın ve eylemi tekrar eklemeyi deneyin.

   Bu örnek, **e-posta gönder (v2)** adlı Office 365 Outlook eylemini kullanır.

   ![Tasarımcıyı ve * * eylem Ekle * * bölmesini gösteren ve Office 365 Outlook "e-posta gönder" eylemi seçili olan ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/find-send-email-action.png)

1. Eylem ayrıntıları bölmesindeki **bağlantı oluştur** sekmesinde, e-posta hesabınıza bir bağlantı oluşturabilmek Için **oturum aç** ' ı seçin.

   ![Tasarımcıyı ve "e-posta gönder (v2)" Ayrıntılar bölmesini gösteren ve "oturum aç" seçiliyken görüntülenen ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-sign-in.png)

1. E-posta hesabınıza erişmek için onay istendiğinde, hesap kimlik bilgilerinizle oturum açın.

   > [!NOTE]
   > Hata iletisini alırsanız, `Failed with error: 'The browser is closed.'. Please sign in again` tarayıcınızın üçüncü taraf tanımlama bilgilerini engellemesini denetleyip engelmediğini denetleyin. Bu tanımlama bilgileri engellenirse, `https://portal.azure.com` tanımlama bilgilerini kullanan siteler listesine eklemeyi deneyin. Inbilito modunu kullanıyorsanız, bu modda çalışırken üçüncü taraf tanımlama bilgilerinin engellenmediğinden emin olun.
   > 
   > Gerekirse, sayfayı yeniden yükleyin, iş akışınızı açın, e-posta eylemini yeniden ekleyin ve bağlantıyı oluşturmayı deneyin.

   Azure bağlantıyı oluşturduktan sonra, tasarımcı 'da **e-posta gönder** eylemi görünür ve varsayılan olarak seçilidir. Eylem seçilmezse, ayrıntılar bölmesinin da açık olması için eylemi seçin.

1. Eylemin Ayrıntılar bölmesindeki **Parametreler** sekmesinde, eylem için gerekli bilgileri sağlayın, örneğin:

   ![Tasarımcıyı ve "e-posta gönder" Ayrıntılar bölmesini gösteren ve "Parameters" sekmesi seçili olan ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-details.png)

   | Özellik | Gerekli | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Kime** | Yes | <*e-posta adresiniz*> | E-posta alıcısı, test amacıyla e-posta adresiniz olabilir. Bu örnek, kurgusal e-postayı kullanır `sophiaowen@fabrikam.com` . |
   | **Konu** | Yes | `An email from your example workflow` | E-posta konusu |
   | **Gövde** | Yes | `Hello from your example workflow!` | E-posta gövdesi içeriği |
   ||||

   > [!NOTE]
   > Ayarlar bölmesinde, **Ayarlar**, **statik sonuç** veya sekmeden **sonra çalışma** sırasında herhangi bir değişiklik yaparken, sekmeleri değiştirmeden veya odağı tasarımcıya değiştirmeden önce bu değişiklikleri kaydetmek için **bitti** ' yi seçtiğinizden emin olun. Aksi halde tasarımcı değişikliklerinizi tutamaz.

1. Çalışmanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

1. Ortamınızda trafiği sınırlayan katı ağ gereksinimleri veya güvenlik duvarları varsa, iş akışınızda mevcut olan herhangi bir tetikleyici veya eylem bağlantısı için izinleri ayarlamanız gerekir. Tam nitelikli olduğunu bulmak için 

   Aksi takdirde, iş akışınızı test etmek için [el ile bir çalıştırmayı tetikleyin](#trigger-workflow).

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>Güvenlik duvarı erişimi için etki alanı adlarını bulma

Mantıksal uygulamanızı dağıtmadan ve Azure portal iş akışınızı çalıştırmadan önce, ortamınızda trafiği sınırlayan katı ağ gereksinimleri veya güvenlik duvarları varsa, mantıksal uygulamanızda bulunan iş akışlarında herhangi bir tetikleyici veya eylem bağlantısı için ağ veya güvenlik duvarı izinleri ayarlamanız gerekir.

Bu bağlantılar için tam etki alanı adlarını (FQDN) bulmak için şu adımları izleyin:

1. Mantıksal uygulama menüsünde, **Iş akışları** altında **Bağlantılar**' ı seçin. **API bağlantıları** sekmesinde bağlantının kaynak adını seçin, örneğin:

   !["Connections" ve "offic365" Bağlantı kaynağı adı seçili olan Azure portal ve Logic App menüsünü gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connections.png)

1. Tarayıcınızın sağ üst köşesinde **JSON görünümü** göründüğünde **JSON görünümü**' nü seçerek tarayıcınızı yeterince genişletebilirsiniz.

   !["JSON görünümü" seçiliyken Azure portal ve API bağlantı bölmesini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-view-json.png)

1. `connectionRuntimeUrl`Güvenlik duvarınızı bu bilgilerle ayarlayabilmeniz için özellik değerini güvenli bir yerde bulun, kopyalayın ve kaydedin.

   ![Seçili "connectionRuntimeUrl" özellik değerini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-runtime-url.png)

1. Her bağlantı için ilgili adımları yineleyin.

<a name="trigger-workflow"></a>

## <a name="trigger-the-workflow"></a>İş akışını tetikleme

Bu örnekte, iş akışı, Istek tetikleyicisi tetikleyici tarafından oluşturulan uç noktanın URL 'sine gönderilen bir gelen istek aldığında çalışır. İş akışını ilk kez kaydettiğinizde, Logic Apps hizmeti bu URL 'YI otomatik olarak oluşturdu. Bu nedenle, bu isteği iş akışını tetikleyecek şekilde gönderebilmeniz için önce bu URL 'YI bulmanız gerekir.

1. İş akışı tasarımcısında, **BIR http isteği alındığında** adlandırılan istek tetikleyicisi ' ni seçin.

1. Ayrıntılar bölmesi açıldıktan sonra **Parametreler** SEKMESINDE **http post URL** özelliğini bulun. Oluşturulan URL 'yi kopyalamak için **URL 'Yi Kopyala** (dosya simgesini Kopyala) seçeneğini belirleyin ve URL 'yi şimdilik başka bir yere kaydedin. URL şu biçimdedir:

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   !["HTTP POST URL" özelliğindeki Istek tetikleyicisi ve uç nokta URL 'SI ile tasarımcıyı gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger-url.png)

   Bu örnekte URL şöyle görünür:

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > Ayrıca, uç nokta URL 'sini mantıksal uygulamanızın **genel bakış** bölmesinde **iş akışı URL** 'si özelliğinde bulabilirsiniz.
   >
   > 1. Kaynak menüsünde **genel bakış**' ı seçin.
   > 1. **Genel bakış** bölmesinde **Iş akışı URL 'si** özelliğini bulun.
   > 1. Uç nokta URL 'sini kopyalamak için, işaretçinizi uç nokta URL 'SI metninin sonuna taşıyın ve **Panoya Kopyala** (dosya simgesini Kopyala) seçeneğini belirleyin.

1. İstek göndererek URL 'YI sınamak için, istek oluşturma ve gönderme için [Postman](https://www.postman.com/downloads/) veya tercih ettiğiniz aracı açın.

   Bu örnek Postman kullanılarak devam eder. Daha fazla bilgi için bkz. [Postman Başlarken](https://learning.postman.com/docs/getting-started/introduction/).

   1. Postman araç çubuğunda **Yeni**' yi seçin.

      ![Yeni düğme seçiliyken Postman gösteren ekran görüntüsü](./media/create-stateful-stateless-workflows-azure-portal/postman-create-request.png)

   1. **Yeni oluştur** bölmesinde, **yapı taşları** altında **istek**' ı seçin.

   1. İsteği **Kaydet** penceresinde, **istek adı** altında, istek için bir ad sağlayın, örneğin, `Test workflow trigger` .

   1. **Kaydedilecek bir koleksiyon veya klasör seçin** altında **koleksiyon oluştur**' u seçin.

   1. **Tüm koleksiyonlar**' ın altında, isteklerinizi düzenlemek için oluşturulacak koleksiyon için bir ad sağlayın, ENTER tuşuna basın ve ***koleksiyon adı* > <için Kaydet**' i seçin. Bu örnek `Logic Apps requests` , koleksiyon adı olarak kullanılır.

      İstek tetikleyicisi için uç nokta URL 'sine bir istek gönderebilmeniz için Postman 'ın istek bölmesi açılır.

      ![Açılan istek bölmesi ile Postman gösteren ekran görüntüsü](./media/create-stateful-stateless-workflows-azure-portal/postman-request-pane.png)

   1. İstek bölmesinde, şu anda varsayılan istek yöntemi olarak **Al** ' ı gösteren Yöntem listesinin yanındaki Adres kutusunda, daha önce kopyaladığınız URL 'yi yapıştırın ve **Gönder**' i seçin.

      ![Gönder düğmesinin seçili olduğu adres kutusunda Postman ve Endpoint URL 'sini gösteren ekran görüntüsü](./media/create-stateful-stateless-workflows-azure-portal/postman-test-endpoint-url.png)

      Tetikleyici tetiklendiğinde örnek iş akışı çalışır ve bu örneğe benzer şekilde görünen bir e-posta gönderir:

      ![Örnekte açıklandığı gibi Outlook e-postasını gösteren ekran görüntüsü](./media/create-stateful-stateless-workflows-azure-portal/workflow-app-result-email.png)

<a name="view-run-history"></a>

## <a name="review-run-history"></a>Çalıştırma geçmişini gözden geçirme

Durum bilgisi olan bir iş akışı için, her iş akışı çalıştıktan sonra, her bir iş akışı için durum, tetikleyici için ve her eylem için giriş ve çıkışları dahil olmak üzere çalıştırma geçmişini görüntüleyebilirsiniz. Azure portal, çalışma geçmişi ve tetikleyici geçmişi, mantıksal uygulama düzeyi değil iş akışı düzeyinde görünür. Çalışma geçmişi bağlamı dışındaki tetikleyici geçmişini gözden geçirmek için bkz. [tetikleyici geçmişlerini gözden geçirme](#view-trigger-histories).

1. Azure portal iş akışınızın menüsünde, **İzle**' yi seçin.

   **İzleyici** bölmesi, bu iş akışı için çalıştırma geçmişini gösterir.

   ![İş akışının "Izleyici" bölmesini ve çalıştırma geçmişini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > En son çalıştırma durumu görünmezse, **izleyici** bölmesi araç çubuğunda **Yenile**' yi seçin. Karşılanmayan ölçütler veya veri bulma nedeniyle atlanan bir tetikleyici için çalıştırma gerçekleşilmedi.

   | Çalışma durumu | Description |
   |------------|-------------|
   | **İptal edildi** | Çalıştırma, dış sorunlar nedeniyle durdurulmuş veya bitmedi; Örneğin, bir sistem kesintisi veya bir Azure aboneliği. |
   | **Yürütüldükten** | Çalıştırma tetiklendi ve başlatıldı, ancak iptal isteği alındı. |
   | **Başarısız** | Çalıştırmada en az bir eylem başarısız oldu. Hata işlemek için iş akışında sonraki hiçbir eylem ayarlanmadı. |
   | **Çalışma** | Çalıştırma tetiklendi ve devam ediyor, ancak bu durum [eylem sınırları](logic-apps-limits-and-config.md) veya [geçerli fiyatlandırma planı](https://azure.microsoft.com/pricing/details/logic-apps/)nedeniyle kısıtlanan bir çalıştırma için de görünebilir. <p><p>**İpucu**: [tanılama günlüğü](monitor-logic-apps-log-analytics.md)ayarlarsanız, gerçekleşen tüm kısıtlama olayları hakkında bilgi edinebilirsiniz. |
   | **Başarılı** | Çalıştırma başarılı oldu. Herhangi bir eylem başarısız olursa, iş akışındaki sonraki bir eylem bu hatayı işledi. |
   | **Zaman aşımına uğradı** | Geçerli süre, [ **gün cinsinden çalışma geçmişi tutma** ayarı](logic-apps-limits-and-config.md#run-duration-retention-limits)tarafından denetlenen çalışma süresi sınırını aştığından çalıştırma zaman aşımına uğradı. Bir çalıştırmanın süresi, çalışma başlangıç zamanı ve çalıştırma süresi sınırı kullanılarak bu başlangıç saatinde hesaplanır. <p><p>**Note**: çalıştırmanın süresi Ayrıca, [ **gün cinsinden çalıştırma geçmişi tutma**](logic-apps-limits-and-config.md#run-duration-retention-limits)tarafından da denetlenen geçerli *çalışma geçmişi saklama sınırını* aşarsa, çalıştırma, günlük temizleme işi tarafından temizlenir. Çalışma zamanının zaman aşımına uğrayıp tamamlanmayacağı, saklama dönemi her zaman çalıştırmanın başlangıç zamanı ve *geçerli* saklama limiti kullanılarak hesaplanır. Bu nedenle, bir uçuş çalışmasının süre sınırını azaltmanız durumunda çalıştırma zaman aşımına uğrar. Ancak çalıştırma, çalıştırma geçmişinden, çalışma süresinin bekletme sınırını aşıp aşılmadığını temel alarak, çalışır durumda kalır veya temizlenir. |
   | **Bekleme** | Çalışan, hala çalışmakta olan önceki bir iş akışı örneği nedeniyle başlatılmış veya duraklatılmış. |
   |||

1. Bir çalıştırmada her adımın durumunu gözden geçirmek için, gözden geçirmek istediğiniz çalıştırmayı seçin.

   Çalışma Ayrıntıları görünümü açılır ve çalıştırmada her adımın durumunu gösterir.

   ![Çalışma akışındaki her adımın durumu ile çalıştırma ayrıntıları görünümünü gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/review-run-details.png)

   İşte iş akışındaki her adımın sahip olduğu olası durumlar şunlardır:

   | Eylem durumu | Simge | Description |
   |---------------|------|-------------|
   | **İptal edildi** | !["Durdurulan" eylem durumu simgesi][aborted-icon] | Bu eylem, dış sorunlar nedeniyle durdurulmuş veya bitmedi; Örneğin, bir sistem kesintisi veya bir Azure aboneliği. |
   | **Yürütüldükten** | !["Iptal edildi" eylem durumu simgesi][cancelled-icon] | Eylem çalışıyor ancak iptal isteği alındı. |
   | **Başarısız** | !["Başarısız" eylem durumu simgesi][failed-icon] | Eylem başarısız oldu. |
   | **Çalışma** | !["Çalışıyor" eylem durumu simgesi][running-icon] | Eylem şu anda çalışıyor. |
   | **Atlandı** | !["Atlanan" eylem durumu simgesi][skipped-icon] | Eylem, hemen önceki eylem başarısız olduğu için atlandı. Bir eylem, `runAfter` Geçerli eylemin çalıştırılabilmesi için önceki eylemin başarıyla çalışmasını gerektiren bir koşula sahiptir. |
   | **Başarılı** | !["Başarılı" eylem durumu simgesi][succeeded-icon] | Eylem başarılı oldu. |
   | **Yeniden denemeler ile başarılı oldu** | !["Yeniden denemeler tamamlandı" eylem durumu simgesi][succeeded-with-retries-icon] | Eylem, yalnızca bir veya daha fazla yeniden denemeden sonra başarılı oldu. Yeniden deneme geçmişini gözden geçirmek için, çalışma geçmişi Ayrıntıları görünümünde, giriş ve çıkışları görüntüleyebilmeniz için bu eylemi seçin. |
   | **Zaman aşımına uğradı** | !["Zaman aşımına uğradı" eylem durumu simgesi][timed-out-icon] | Eylem, bu eylemin ayarları tarafından belirtilen zaman aşımı sınırı nedeniyle durduruldu. |
   | **Bekleme** | !["Bekleniyor" eylem durumu simgesi][waiting-icon] | Çağırandan gelen istekleri bekleyen bir Web kancası eylemi için geçerlidir. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-azure-portal/waiting.png

1. Belirli bir adım için giriş ve çıkışları gözden geçirmek üzere bu adımı seçin.

   ![Seçili "e-posta gönder" eyleminde giriş ve çıkışları gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/review-step-inputs-outputs.png)

1. Bu adım için ham giriş ve çıkışları daha fazla gözden geçirmek için **Ham girdileri göster** veya **Ham çıkışları göster**' i seçin.

<a name="view-trigger-histories"></a>

## <a name="review-trigger-histories"></a>Tetikleyici geçmişlerini gözden geçirin

Durum bilgisi olan bir iş akışı için, [çalışma geçmişi bağlamından](#view-run-history)ayrı olarak, giriş ve çıkışlarla birlikte tetikleyici durumu da dahil olmak üzere her bir çalıştırmaya ilişkin tetikleyici geçmişini gözden geçirebilirsiniz. Azure portal, tetikleme geçmişi ve çalıştırma geçmişi mantıksal uygulama düzeyi değil iş akışı düzeyinde görünür. Bu geçmiş verileri bulmak için şu adımları izleyin:

1. Azure portal, iş akışınızın menüsünde, **Geliştirici** altında, **tetikleyici geçmişi**' ni seçin.

   **Tetikleyici geçmişi** bölmesi, iş akışınızın çalıştırmaları için tetikleyici geçmişlerini gösterir.

1. Belirli bir tetikleyici geçmişini gözden geçirmek için o çalıştırmanın KIMLIĞINI seçin.

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Dağıtımdan sonra Application Insights etkinleştir veya aç

İş akışı yürütülürken, mantıksal uygulamanız telemetri ve diğer olaylarla birlikte yayar. İş akışınızın ne kadar iyi görünebileceği ve Logic Apps çalışma zamanının çeşitli yollarla nasıl çalıştığı hakkında daha iyi görünürlük almak için bu Telemetriyi kullanabilirsiniz. İş akışınızı, neredeyse gerçek zamanlı telemetri (canlı ölçümler) sağlayan [Application Insights](../azure-monitor/app/app-insights-overview.md)kullanarak izleyebilirsiniz. Bu özellik sorunları tanılamak, uyarıları ayarlamak ve grafikler oluşturmak için bu verileri kullandığınızda hataları ve performans sorunlarını daha kolay bir şekilde araştırmanıza yardımcı olabilir.

Mantıksal uygulamanızın oluşturma ve dağıtım ayarları [Application Insights](../azure-monitor/app/app-insights-overview.md)kullanmayı destekliyorsa, mantıksal uygulamanız için isteğe bağlı olarak tanılama günlüğünü ve izlemeyi etkinleştirebilirsiniz. Azure portal mantıksal uygulamanızı oluşturduğunuzda veya dağıtımdan sonra yapabilirsiniz. Bir Application Insights örneğiniz olması gerekir, ancak mantıksal uygulamanızı oluştururken ya da dağıtımdan sonra bu kaynağı [önceden](../azure-monitor/app/create-workspace-resource.md)oluşturabilirsiniz.

Dağıtılan bir mantıksal uygulamada Application Insights etkinleştirmek veya zaten etkinse Application Insights panosunu açmak için şu adımları izleyin:

1. Azure portal, dağıtılan mantıksal uygulamanızı bulun.

1. Mantıksal uygulama menüsünde, **Ayarlar** altında **Application Insights**' ı seçin.

1. Application Insights etkinleştirilmemişse, **Application Insights** bölmesinde **Application Insights aç**' ı seçin. Bölmedeki güncelleştirmelerden sonra, alt kısımdaki **Uygula**' yı seçin.

   Application Insights etkinse, **Application Insights** bölmesinde **Application Insights verileri görüntüle**' yi seçin.

Application Insights açıldıktan sonra mantıksal uygulamanız için çeşitli ölçümleri gözden geçirebilirsiniz. Daha fazla bilgi için şu konuları gözden geçirin:

* [Azure Logic Apps Application Insights-Bölüm 1 ile her yerden Izleme çalıştırma](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Application Insights Bölüm 2 ile her yerden Izleme Azure Logic Apps](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Durum bilgisiz iş akışları için çalıştırma geçmişini etkinleştir

Durum bilgisi olmayan bir iş akışında daha kolay hata ayıklamak için bu iş akışı için çalıştırma geçmişini etkinleştirebilir ve sonra işiniz bittiğinde çalıştırma geçmişini devre dışı bırakabilirsiniz. Azure portal için bu adımları izleyin veya Visual Studio Code ' de çalışıyorsanız, bkz. [Visual Studio Code durum bilgisiz ve durum bilgisi olmayan iş akışları oluşturma](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

1. [Azure Portal](https://portal.azure.com), **mantıksal uygulama (Önizleme)** kaynağını bulup açın.

1. Mantıksal uygulamanın menüsünde, **Ayarlar**' ın altında **yapılandırma**' yı seçin.

1. **Uygulama ayarları** sekmesinde **Yeni uygulama ayarı**' nı seçin.

1. **Uygulama ayarı Ekle/Düzenle** bölmesinde, **ad** kutusuna bu işlem seçeneği adını girin: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. **Değer** kutusuna aşağıdaki değeri girin:`WithStatelessRunHistory`

   Örnek:

   ![Azure portal ve mantıksal uygulama (Önizleme) kaynağını "yapılandırma" > "yeni uygulama ayarı" < "uygulama ayarı Ekle/Düzenle" bölmesi açık ve "Iş akışları" olarak gösteren ekran görüntüsü. {yourWorkflowName}. OperationOptions "seçeneği" WithStatelessRunHistory "olarak ayarlandı.](./media/create-stateful-stateless-workflows-azure-portal/stateless-operation-options-run-history.png)

1. Bu görevi tamamlaması için **Tamam**' ı seçin. **Yapılandırma** bölmesi araç çubuğunda **Kaydet**' i seçin.

1. İşiniz bittiğinde çalıştırma geçmişini devre dışı bırakmak için `Workflows.{yourWorkflowName}.OperationOptions` özelliğini olarak ayarlayın `None` ya da özelliği ve değerini silin.

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Tasarımcıdan öğeleri silme

Tasarımcıdaki iş akışınızda bir öğeyi silmek için şu adımlardan herhangi birini izleyin:

* Öğeyi seçin, öğenin kısayol menüsünü açın (SHIFT + F10) ve **Sil**' i seçin. Onaylamak için **Tamam**’ı seçin.

* Öğeyi seçin ve DELETE tuşuna basın. Onaylamak için **Tamam**’ı seçin.

* Öğeyi, ayrıntılar bölmesinin bu öğe için açılmasını sağlayacak şekilde seçin. Bölmenin sağ üst köşesinde üç nokta (**...**) menüsünü açın ve **Sil**' i seçin. Onaylamak için **Tamam**’ı seçin.

  ![Açık Ayrıntılar bölmesiyle ve seçilen üç nokta düğmesi ve "Sil" komutu ile tasarımcıda seçili bir öğeyi gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > Üç nokta menüsü görünmüyorsa, ayrıntılar bölmesinin sağ üst köşedeki üç nokta (**...**) düğmesini gösterdiği şekilde tarayıcı pencerenizi yeterince geniş bir şekilde genişletin.

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>Sorunları ve hataları giderme

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Yeni Tetikleyiciler ve eylemler daha önce oluşturulan iş akışlarının tasarımcı seçicisinde yok

Azure Logic Apps önizlemesi, Azure Işlev Işlemleri, sıvı Işlemleri ve XML **doğrulaması** ve **dönüştürme XML** gibi XML işlemlerine yönelik yerleşik eylemleri destekler. Ancak, daha önce oluşturulmuş Logic Apps için, mantıksal uygulamanızın uzantı paketinin güncel olmayan bir sürümünü kullanıp kullanmadığını seçerken bu eylemler tasarımcıda görünmeyebilir `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Bu sorunu gidermek için, uzantı paketinin en son sürüme otomatik olarak güncelleştirebilmesi için eski sürümü silmek üzere bu adımları izleyin.

> [!NOTE]
> Bu özel çözüm, Visual Studio Code ve Azure Logic Apps (Önizleme) uzantısı kullanılarak oluşturduğunuz ve dağıttığınız Logic Apps değil, yalnızca Azure portal kullanarak oluşturduğunuz **mantıksal uygulama (Önizleme)** kaynakları için geçerlidir. [Visual Studio Code içindeki tasarımcıda desteklenen Tetikleyiciler ve eylemler eksik](create-stateful-stateless-workflows-visual-studio-code.md#missing-triggers-actions).

1. Azure portal mantıksal uygulamanızı durdurun.

   1. Mantıksal uygulama menüsünde **genel bakış**' ı seçin.

   1. **Genel bakış** bölmesinin araç çubuğunda **Durdur**' u seçin.

1. Mantıksal uygulama menünüzde **geliştirme araçları** altında **Gelişmiş Araçlar**' ı seçin.

1. **Gelişmiş Araçlar** bölmesinde, mantıksal uygulamanız Için kudu ortamını açan **Git**' i seçin.

1. Kudu araç çubuğunda, **hata ayıklama konsolu** menüsünü açın ve **cmd**' yi seçin. 

   Komut istemi 'ni kullanarak paket klasörüne gözatabilmeniz için bir konsol penceresi açılır. Ya da konsol penceresi görüntülenen dizin yapısına gidebilirsiniz.

1. Mevcut paket için sürümlenmiş klasörleri içeren aşağıdaki klasöre gidin:

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Mevcut paket için sürüm klasörünü silin. Konsol penceresinde, var olan sürümle değiştirdiğiniz bu komutu çalıştırabilirsiniz `{bundle-version}` :

   `rm -rf {bundle-version}`

   Örnek: `rm -rf 1.1.3`

   > [!TIP]
   > "İzin reddedildi" veya "kullanımda olan dosya" gibi bir hata alırsanız, tarayıcınızda sayfayı yenileyin ve klasör silinene kadar önceki adımları tekrar deneyin.

1. Azure portal, mantıksal uygulamanızın **genel bakış** sayfasına dönün ve **Yeniden Başlat**' ı seçin.

   Portal, en son paketi otomatik olarak alır ve kullanır.

## <a name="next-steps"></a>Sonraki adımlar

Bu genel önizlemede deneyimlerinizi öğrenmek istiyoruz!

* Hatalar veya sorunlar için [sorunlarınızı GitHub ' da oluşturun](https://github.com/Azure/logicapps/issues).
* Sorular, istekler, açıklamalar ve diğer geri bildirimler için [Bu geri bildirim formunu kullanın](https://aka.ms/lafeedback).
