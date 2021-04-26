---
title: Visual Studio Code içinde Logic Apps önizlemesi iş akışları oluşturma
description: Azure Logic Apps (Önizleme) uzantısıyla Visual Studio Code ' de Otomasyon ve tümleştirme senaryoları için iş akışları oluşturun ve çalıştırın.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: 4010f7e2d0d20216107a45109056478694c940ca
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772514"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Azure Logic Apps (Önizleme) uzantısıyla Visual Studio Code durum bilgisiz ve durum bilgisi olmayan iş akışları oluşturma

> [!IMPORTANT]
> Bu özellik genel önizleme aşamasındadır, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure Logic Apps önizleme](logic-apps-overview-preview.md)sayesinde uygulamalar, veriler, bulut hizmetleri ve sistemler arasında, Azure Logic Apps (Önizleme) uzantısı kullanılarak Visual Studio Code [ *durum bilgisi* olan ve *olmayan* iş akışları](logic-apps-overview-preview.md#stateful-stateless) içeren mantıksal uygulamalar oluşturup çalıştırarak Otomasyon ve tümleştirme çözümleri oluşturabilirsiniz. Bu yeni mantıksal uygulama türünü kullanarak, yalnızca Azure, ancak Docker Kapsayıcıları değil çeşitli barındırma ortamlarında dağıtım ve çalıştırma için taşınabilirlik, daha iyi performans ve esneklik sağlayan, yeniden tasarlanan Azure Logic Apps Önizleme çalışma zamanı tarafından desteklenen birden çok iş akışı oluşturabilirsiniz. Yeni mantıksal uygulama türü hakkında daha fazla bilgi edinmek için bkz. [Azure Logic Apps önizlemesi Için genel bakış](logic-apps-overview-preview.md).

![Visual Studio Code, Logic App projesini ve iş akışını gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

Visual Studio Code, Azure Logic Apps (Önizleme) uzantısını kullanarak mantıksal uygulamanızın iş akışlarını geliştirme ortamınızda *yerel olarak* oluşturup çalıştırabileceğiniz bir proje oluşturarak başlayabilirsiniz. [Azure Portal yeni bir **mantıksal uygulama (Önizleme)** kaynağı oluşturarak](create-stateful-stateless-workflows-azure-portal.md)da başlatabileceğiniz gibi, her iki yaklaşım da mantıksal uygulamanızı aynı türde barındırma ortamlarında dağıtmanıza ve çalıştırmanıza olanak sağlar.

Bu arada, özgün mantıksal uygulama türünü yine de oluşturabilirsiniz. Visual Studio Code ' deki geliştirme deneyimleri, orijinal ve yeni mantıksal uygulama türleri arasında farklılık gösterebilse de Azure aboneliğiniz her iki türü de içerebilir. Azure aboneliğinizde dağıtılan tüm mantıksal uygulamaları görüntüleyebilir ve bunlara erişebilirsiniz, ancak uygulamalar kendi kategorilerine ve bölümlerine göre düzenlenir.

Bu makalede, Azure Logic Apps (Önizleme) uzantısını kullanarak ve bu üst düzey görevleri gerçekleştirerek Visual Studio Code mantıksal uygulamanızı ve bir iş akışını nasıl oluşturacağınız gösterilmektedir:

* Mantıksal uygulamanız ve iş akışınız için bir proje oluşturun.

* Tetikleyici ve eylem ekleyin.

* Çalıştırma geçmişini yerel olarak çalıştırın, test edin, hata ayıklayın ve inceleyin.

* Güvenlik duvarı erişimi için etki alanı adı ayrıntılarını bulun.

* İsteğe bağlı olarak Application Insights etkinleştirmeyi içeren Azure 'a dağıtın.

* Visual Studio Code ve Azure portal dağıtılan mantıksal uygulamanızı yönetin.

* Durum bilgisiz iş akışları için çalıştırma geçmişini etkinleştirin.

* Dağıtımdan sonra Application Insights etkinleştirin veya açın.

* Dilediğiniz yerde çalıştırabileceğiniz bir Docker kapsayıcısına dağıtın.

> [!NOTE]
> Bilinen güncel sorunlar hakkında daha fazla bilgi için [GitHub 'Daki bilinen sorunlar Logic Apps Genel Önizleme sayfasını](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

### <a name="access-and-connectivity"></a>Erişim ve bağlantı

* Gereksinimleri indirebilmeniz, Azure hesabınıza Visual Studio Code bağlanabilmek ve Visual Studio Code Azure 'a, Docker kapsayıcısına veya diğer bir ortama yayımlamanız için internet 'e erişin.

* Bir Azure hesabı ve aboneliği Aboneliğiniz yoksa, [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Bu makalede aynı örnek mantıksal uygulamayı oluşturmak için, oturum açmak için Microsoft iş veya okul hesabı kullanan bir Office 365 Outlook e-posta hesabınız olması gerekir.

  Outlook.com veya [Gmail](../connectors/connectors-google-data-security-privacy-policy.md)gibi [Azure Logic Apps tarafından desteklenen farklı bir e-posta Bağlayıcısı](/connectors/)kullanmayı tercih ediyorsanız, örneği yine de izleyebilirsiniz ve genel adımlar aynıdır, ancak Kullanıcı arabiriminiz ve seçenekleriniz bazı yollarla farklılık gösterebilir. Örneğin, Outlook.com bağlayıcısını kullanıyorsanız, oturum açmak için kişisel Microsoft hesabı kullanın.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Depolama gereksinimleri

#### <a name="windows"></a>Windows

Windows kullanırken Visual Studio Code mantıksal uygulama projenizi yerel olarak derlemek ve çalıştırmak için Azure depolama öykünücüsünü ayarlamak üzere aşağıdaki adımları izleyin:

1. [Azure Storage öykünücüsü 5,10](https://go.microsoft.com/fwlink/p/?linkid=717179)'yi indirip yükleyin.

1. Henüz bir tane yoksa, öykünücü çalışabilmesi için ücretsiz [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658)gibi BIR yerel SQL DB yüklemeniz olması gerekir.

   Daha fazla bilgi için bkz. [geliştirme ve test Için Azure depolama öykünücüsünü kullanma](../storage/common/storage-use-emulator.md).

1. Projenizi çalıştırmadan önce öykünücüyü başlattığınızdan emin olun.

   ![Çalışan Azure Storage öykünücüsü 'nü gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS ve Linux

MacOS veya Linux kullanırken Visual Studio Code mantıksal uygulama projenizi yerel olarak derlemek ve çalıştırmak için, Azure depolama hesabı oluşturmak ve ayarlamak için aşağıdaki adımları izleyin.

> [!NOTE]
> Şu anda Visual Studio Code tasarımcı Linux IŞLETIM sisteminde çalışmaz, ancak yine de Linux tabanlı sanal makinelere Logic Apps Preview çalışma zamanını kullanan oluşturma, çalıştırma ve dağıtım uygulamaları çalıştırabilirsiniz. Şimdilik, Logic Apps 'i Windows veya macOS üzerinde Visual Studio Code oluşturabilir ve ardından Linux tabanlı bir sanal makineye dağıtabilirsiniz.

1. [Azure Portal](https://portal.azure.com)oturum açın ve [Azure işlevleri için bir önkoşul](../azure-functions/storage-considerations.md)olan [bir Azure depolama hesabı oluşturun](../storage/common/storage-account-create.md?tabs=azure-portal).

1. Depolama hesabı menüsündeki **Ayarlar** altında **erişim anahtarları**' nı seçin.

1. **Erişim tuşları** bölmesinde, depolama hesabının bağlantı dizesini bulun ve kopyalayın, bu örneğe benzer şekilde görünür:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Depolama hesabı erişim anahtarları ve bağlantı dizesinin kopyalandığı Azure portal gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Daha fazla bilgi için [depolama hesabı anahtarlarını yönetme](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)konusunu gözden geçirin.

1. Bağlantı dizesini güvenli bir yere kaydedin. Visual Studio Code ' de mantıksal uygulama projenizi oluşturduktan sonra, dizeyi projenizin kök düzeyi klasöründeki **local.settings.js** dosyasına eklemeniz gerekir.

   > [!IMPORTANT]
   > Bir Docker kapsayıcısına dağıtmayı planlıyorsanız, bu bağlantı dizesini dağıtım için kullandığınız Docker dosyası ile birlikte kullanmanız gerekir. Üretim senaryolarında, örneğin bir anahtar kasasını kullanarak, bu gizli bilgileri ve gizli bilgileri koruyun ve güvenli hale getirin.
  
### <a name="tools"></a>Araçlar

* [Visual Studio Code 1.30.1 (ocak 2019) veya üzeri bir sürümü](https://code.visualstudio.com/)ücretsizdir. Ayrıca, şu anda yoksa Visual Studio Code için bu araçları indirip yükleyin:

  * Visual Studio Code ' deki diğer tüm Azure uzantıları için tek bir genel Azure oturum açma ve abonelik filtreleme deneyimi sunan [Azure Hesap uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

  * [Visual Studio Code uzantısı Için C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), mantıksal uygulamanızı çalıştırmak için F5 işlevinin kullanılmasına olanak sağlar.

  * Microsoft Installer (MSI) sürümünü kullanarak [3.0.3245 veya üzeri Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245) `func-cli-3.0.3245-x*.msi` .

    Bu araçlar, önizleme uzantısının Visual Studio Code içinde kullandığı Azure Işlevleri çalışma zamanı 'nı destekleyen aynı çalışma zamanının bir sürümünü içerir.

    > [!IMPORTANT]
    > Bu sürümlerden daha önceki bir yüklemeniz varsa, önce bu sürümü kaldırın ya da PATH ortam değişkeninin indirip yüklediğiniz sürümü işaret ettiğini doğrulayın.

  * [Visual Studio Code için Azure Logic Apps (Önizleme) uzantısı](https://go.microsoft.com/fwlink/p/?linkid=2143167). Bu uzantı, Visual Studio Code yerel olarak çalışan ve bu Logic Apps 'i doğrudan Azure 'a veya Docker kapsayıcılarına dağıtabileceğiniz, durum bilgisiz ve durum bilgisi olmayan iş akışlarını oluşturabileceğiniz Mantıksal uygulamalar oluşturmanıza olanak sağlar.

    Şu anda, hem özgün Azure Logic Apps uzantısının hem de genel önizleme uzantısının Visual Studio Code ' de yüklü olmasını sağlayabilirsiniz. Geliştirme deneyimleri, uzantılar arasında bazı yollarla farklılık gösterebilse de Azure aboneliğiniz, uzantılarla oluşturduğunuz her iki mantıksal uygulama türünü de içerebilir. Visual Studio Code, Azure aboneliğinizde dağıtılan tüm mantıksal uygulamaları gösterir, ancak onları uzantı adlarına, **Logic Apps** ve **Azure Logic Apps (Önizleme)** göre farklı bölümlere düzenler.

    > [!IMPORTANT]
    > Daha önceki özel önizleme uzantısıyla Logic App projeleri oluşturduysanız, bu projeler genel önizleme uzantısıyla çalışmaz. Ancak, özel önizleme uzantısını kaldırdıktan sonra, ilişkili dosyaları sildikten ve genel önizleme uzantısını yükledikten sonra bu projeleri geçirebilirsiniz. Ardından Visual Studio Code yeni bir proje oluşturun ve daha önce oluşturduğunuz mantıksal uygulamanın **Workflow. Definition** dosyasını yeni projenize kopyalayın. Daha fazla bilgi için bkz. [özel önizleme uzantısından geçiş](#migrate-private-preview).
    > 
    > Daha önceki genel önizleme uzantısıyla Logic App projeleri oluşturduysanız, bu projeleri herhangi bir geçiş adımı olmadan kullanmaya devam edebilirsiniz.

    ****Azure Logic Apps (Önizleme)** uzantısını yüklemek için şu adımları izleyin:**

    1. Visual Studio Code, sol araç çubuğunda **Uzantılar**' ı seçin.

    1. Uzantılar arama kutusuna girin `azure logic apps preview` . Sonuçlar listesinden **Azure Logic Apps (Önizleme)** **>** **yüklemesi**' ni seçin.

       Yükleme tamamlandıktan sonra, önizleme uzantısı **Uzantılar: yüklü** listesinde görünür.

       ![Visual Studio Code yüklü uzantılar listesini "Azure Logic Apps (Önizleme)" uzantısıyla altı çizili olarak gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > Uzantı yüklü listede görünmezse Visual Studio Code yeniden başlatmayı deneyin.

* JavaScript çalıştıran [satır Içi kod işlemleri eylemini](../logic-apps/logic-apps-add-run-inline-code.md) kullanmak için [ 10. x. x, 11. x. x veya 12. x. xNode.js sürümlerini](https://nodejs.org/en/download/releases/)yükler.

  > [!TIP] 
  > Windows için MSI sürümünü indirin. Bunun yerine ZIP sürümünü kullanırsanız, işletim sisteminiz için bir yol ortam değişkeni kullanarak Node.js el ile kullanılabilir hale getirebilirsiniz.

* [YERLEŞIK http Web kancası tetikleyicisi](../connectors/connectors-native-webhook.md)gibi Web kancası tabanlı Tetikleyicileri ve eylemleri yerel olarak çalıştırmak için, Visual Studio Code, [geri çağırma URL 'si için iletmeyi ayarlamanız](#webhook-setup)gerekir.

* Bu makalede oluşturduğunuz örnek mantıksal uygulamayı test etmek için, örnek Logic App 'teki ilk adım olan Istek tetikleyicisine çağrılar gönderebilen bir araca ihtiyacınız vardır. Böyle bir aracınız yoksa [Postman](https://www.postman.com/downloads/)'ı indirebilir, yükleyebilir ve kullanabilirsiniz.

* Mantıksal uygulamanızı oluşturup [Application Insights](../azure-monitor/app/app-insights-overview.md)kullanmayı destekleyen ayarlarla dağıtıyorsanız, isteğe bağlı olarak, mantıksal uygulamanız için tanılama günlüğü ve izlemeyi etkinleştirebilirsiniz. Mantıksal uygulamanızı Visual Studio Code veya dağıtımdan sonra dağıtırken bunu yapabilirsiniz. Bir Application Insights örneğiniz olması gerekir, ancak mantıksal uygulamanızı dağıtırken ya da dağıtımdan sonra bu kaynağı [önceden](../azure-monitor/app/create-workspace-resource.md)oluşturabilirsiniz.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>Özel Önizleme uzantısından geçir

**Azure Logic Apps (özel Önizleme)** uzantısıyla oluşturduğunuz mantıksal uygulama projeleri, genel önizleme uzantısıyla birlikte çalışmaz. Ancak, aşağıdaki adımları izleyerek bu projeleri yeni projelere geçirebilirsiniz:

1. Özel Önizleme uzantısını kaldırın.

1. Bu konumlardaki ilişkili Uzantı paketi ve NuGet paket klasörlerini silin:

   * Önceki uzantı paketlerini içeren ve burada yol üzerinde yer alan **Microsoft. Azure. Functions. Extensiondemeti. Iş akışları** klasörü:

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * Özel Önizleme uzantısı için [NuGet](/nuget/what-is-nuget) önbelleği olan ve bu yolda bulunan **Microsoft. Azure. iş akışları. WebJobs. Extension** klasörü:

     `C:\Users\{userName}\.nuget\packages`

1. **Azure Logic Apps (Önizleme)** uzantısını yükler.

1. Visual Studio Code yeni bir proje oluşturun.

1. Önceden oluşturulmuş mantıksal uygulamanızın **Workflow. Definition** dosyasını yeni projenize kopyalayın.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Visual Studio Code'u ayarlama

1. Tüm uzantıların doğru yüklendiğinden emin olmak için Visual Studio Code yeniden yükleyin veya yeniden başlatın.

1. Visual Studio Code, önizleme uzantınızın en son güncelleştirmeleri alması için uzantı güncelleştirmelerini otomatik olarak bulmasını ve yüklemesini doğrulayın. Aksi takdirde, eski sürümü el ile kaldırmanız ve en son sürümü kurmanız gerekir.

   1. **Dosya** menüsünde **Tercihler** **>** **Ayarlar**' a gidin.

   1. **Kullanıcı** sekmesinde, **Özellikler** **>** **uzantıları**' na gidin.

   1. **Otomatik denetim güncelleştirmeleri** ve **Otomatik güncelleştirme** 'nin seçili olduğunu onaylayın.

Ayrıca, varsayılan olarak, Logic Apps önizleme uzantısı için aşağıdaki ayarlar etkinleştirilir ve ayarlanır:

* **Azure Logic Apps v2:** sürüm **~ 3** olarak ayarlanan proje çalışma zamanı

  > [!NOTE]
  > Bu sürüm, [satır Içi kod işlemleri eylemlerini](../logic-apps/logic-apps-add-run-inline-code.md)kullanmak için gereklidir.

* **Azure Logic Apps v2:** Visual Studio Code en son tasarımcıyı sağlayan deneysel Görünüm Yöneticisi. Tasarımcı üzerinde öğe sürükleme ve bırakma gibi sorunlarla karşılaşırsanız, bu ayarı kapatın.

Bu ayarları bulmak ve onaylamak için aşağıdaki adımları izleyin:

1. **Dosya** menüsünde **Tercihler** **>** **Ayarlar**' a gidin.

1. **Kullanıcı** sekmesinde **>** **Uzantılar** **>** **Azure Logic Apps (Önizleme)** bölümüne gidin.

   Örneğin, burada **Azure Logic Apps v2: proje çalışma zamanı** ayarını bulabilir veya diğer ayarları bulmak için arama kutusunu kullanabilirsiniz:

   !["Azure Logic Apps (Önizleme)" uzantısının Visual Studio Code ayarlarını gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Azure hesabınıza bağlanma

1. Visual Studio Code etkinlik çubuğunda Azure simgesini seçin.

   ![Etkinlik çubuğunu ve seçili Azure simgesini gösteren Visual Studio Code ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Azure bölmesinde Azure **: Logic Apps (Önizleme)** altında **Azure 'da oturum aç**' ı seçin. Visual Studio Code kimlik doğrulama sayfası göründüğünde Azure hesabınızla oturum açın.

   ![Azure bölmesi ve Azure oturum açma için Seçili bağlantıyı gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Oturum açtıktan sonra Azure bölmesinde Azure hesabınızdaki abonelikler gösterilir. Ayrıca, genel olarak yayımlanmış uzantıya sahipseniz, bu uzantıyla oluşturduğunuz mantıksal uygulamaları **Logic Apps (Önizleme)** bölümünde değil **Logic Apps** bölümünde bulabilirsiniz.
   
   Beklenen abonelikler görünmezse veya bölmenin yalnızca belirli abonelikleri göstermesini istiyorsanız aşağıdaki adımları izleyin:

   1. Abonelikler listesinde, **abonelikleri Seç** düğmesi (filtre simgesi) görünene kadar işaretçinizi ilk aboneliğin yanına taşıyın. Filtre simgesini seçin.

      ![Azure bölmesini ve Seçili filtre simgesini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Ya da Visual Studio Code durum çubuğunda Azure hesabınızı seçin. 

   1. Başka bir abonelik listesi göründüğünde, istediğiniz abonelikleri seçin ve **Tamam**' ı seçtiğinizden emin olun.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Yerel proje oluşturma

Mantıksal uygulamanızı oluşturabilmeniz için önce, Visual Studio Code mantıksal uygulamanızı yönetebilmeniz, çalıştırabilmeniz ve dağıtabilmeniz için yerel bir proje oluşturun. Temel alınan proje, işlev uygulaması projesi olarak da bilinen bir Azure Işlevleri projesine benzerdir. Ancak, bu proje türleri birbirinden ayrıdır, bu nedenle Logic Apps ve işlev uygulamaları aynı projede bulunamaz.

1. Bilgisayarınızda, daha sonra Visual Studio Code oluşturacağınız proje için kullanmak üzere *boş* bir yerel klasör oluşturun.

1. Visual Studio Code, tüm açık klasörleri kapatın.

1. Azure bölmesinde **Azure: Logic Apps (Önizleme)**' nin yanındaki **Yeni proje oluştur** ' u (bir klasör ve şimşek sürgüsü gösteren simge) seçin.

   !["Yeni proje oluştur" seçiliyken Azure bölmesi araç çubuğunu gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Windows Defender güvenlik duvarı, için Visual Studio Code için ağ erişimi vermenizi isterse, `Code.exe` Azure Functions Core Tools için, `func.exe` **Ev veya iş ağım** **>** **erişime izin ver** gibi özel ağlar ' ı seçin.

1. Proje klasörünüzü oluşturduğunuz konuma gidin, bu klasörü seçin ve devam edin.

   ![Yeni oluşturulan proje klasörüyle ve "Seç" düğmesinin seçili olduğu "klasör seç" iletişim kutusunu gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Görüntülenen şablonlar listesinden **durum bilgisi olan Iş akışı** veya **durum bilgisiz iş akışı**' nı seçin. Bu örnek, **durum bilgisi olan Iş akışını** seçer.

   !["Durum bilgisi olan Iş akışı" seçiliyken iş akışı şablonları listesini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. İş akışınız için bir ad girin ve ENTER tuşuna basın. Bu örnek `Fabrikam-Stateful-Workflow` , ad olarak kullanır.

   !["Yeni durum bilgisi olan Iş akışı (3/4)" kutusu ve "Fabrikam-Stateful-Workflow" öğesini iş akışı adı olarak gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code projenizi oluşturma işlemini tamamlar ve kod düzenleyicisinde iş akışınız için **workflow.js** dosyasını açar.

   > [!NOTE]
   > Projenizin nasıl açılacağını seçmeniz istenirse, projenizi geçerli Visual Studio Code penceresinde açmak istiyorsanız **geçerli pencerede aç** ' ı seçin. Visual Studio Code yeni bir örnek açmak için **Yeni pencerede aç**' ı seçin.

1. Henüz açık değilse, Visual Studio araç çubuğundan Gezgin bölmesini açın.

   Gezgin bölmesinde, projenizi, artık otomatik olarak oluşturulan proje dosyalarını içeren bir şekilde gösterir. Örneğin, projenin iş akışınızın adını gösteren bir klasörü vardır. Bu klasörün içinde **workflow.js** dosya, iş akışınızın temel alınan JSON tanımını içerir.

   ![Proje klasörü, iş akışı klasörü ve "workflow.json" dosyası ile Gezgin bölmesini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. MacOS veya Linux kullanıyorsanız, projenizi yerel olarak çalıştırmak için gerekli adımları izleyerek depolama hesabınıza erişimi ayarlayın:

   1. Projenizin kök klasöründe **local.settings.jsdosya üzerinde** açın.

      ![Gezgin bölmesini ve projenizdeki ' local.settings.json ' dosyasını gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. `AzureWebJobsStorage`Özellik değerini daha önce kaydettiğiniz depolama hesabının bağlantı dizesiyle değiştirin, örneğin:

      Önce:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "UseDevelopmentStorage=true",
            "FUNCTIONS_WORKER_RUNTIME": "dotnet"
          }
      }
      ```

      Sonra:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net",
           "FUNCTIONS_WORKER_RUNTIME": "dotnet"
         }
      }
      ```

      > [!IMPORTANT]
      > Üretim senaryolarında, örneğin bir anahtar kasasını kullanarak, bu gizli bilgileri ve gizli bilgileri koruyun ve güvenli hale getirin.

   1. İşiniz bittiğinde, değişikliklerinizi kaydettiğinizden emin olun.

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>Yerleşik bağlayıcı yazmayı etkinleştir

[Önizleme sürümünün genişletilebilirlik çerçevesini](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)kullanarak ihtiyacınız olan herhangi bir hizmet için kendi yerleşik bağlayıcılarınızı oluşturabilirsiniz. Azure Service Bus ve SQL Server gibi yerleşik bağlayıcılara benzer şekilde, bu bağlayıcılar daha yüksek aktarım hızı, düşük gecikme süresi, yerel bağlantı sağlar ve Önizleme çalışma zamanı ile aynı işlemde yerel olarak çalışır.

Yazma özelliği şu anda yalnızca Visual Studio Code ' de kullanılabilir, ancak varsayılan olarak etkinleştirilmemiştir. Bu bağlayıcıları oluşturmak için, önce projenizi paket tabanlı (Node.js), NuGet paket tabanlı (.NET) ' e dönüştürmeniz gerekir.

> [!IMPORTANT]
> Bu eylem, geri alamayacaksınız tek yönlü bir işlemdir.

1. Gezgin bölmesinde, projenizin kökünde, fare işaretçinizi diğer tüm dosya ve klasörlerin altındaki boş bir alana taşıyın, kısayol menüsünü açın ve **NuGet tabanlı mantıksal uygulama projesine Dönüştür**' ü seçin.

   ![Projenin kısayol menüsünün proje penceresindeki boş bir alandan açıldığını gösteren Gezgin bölmesini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/convert-logic-app-project.png)

1. İstem göründüğünde, proje dönüştürmeyi onaylayın.

1. Devam etmek için, [her yerde yerleşik bağlayıcı genişletilebilirliği çalıştırmak Azure Logic Apps](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)makaledeki adımları gözden geçirin ve uygulayın.

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>Tasarımcıda iş akışı Tanım dosyasını açın

1. Şu komutu çalıştırarak bilgisayarınızda yüklü olan sürümleri kontrol edin:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   .NET Core SDK 5. x varsa, bu sürüm, tasarımcıda mantıksal uygulamanın temel alınan iş akışı tanımını açmanızı önleyebilir. Bu sürümü kaldırmak yerine, projenizin kök klasöründe, 3.1.201 ' den daha yeni olan .NET Core Runtime 3. x sürümüne başvuran bir **global.js** oluşturun, örneğin:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > **global.js** dosyanın içindeki kök klasördeki dosyayı açık olarak Visual Studio Code eklediğinizden emin olun. Aksi halde tasarımcı açılmaz.

1. İş akışınız için proje klasörünü genişletin. Dosyanın kısayol menüsünde **workflow.js** açın ve **tasarımcıda aç**' ı seçin.

   !["Tasarımcıda aç" seçiliyken dosyada workflow.jsiçin Gezgin bölmesini ve kısayol penceresini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. Azure **'da bağlayıcıları etkinleştir** listesinde Azure 'da bulunan ve yalnızca Azure hizmetleri için bağlayıcılar değil, Azure 'da bulunan ve dağıtılan tüm yönetilen bağlayıcılar için geçerli olan **Azure 'da bağlayıcılar kullanın**' ı seçin.

   !["Azure 'da bağlayıcıları etkinleştir" listesinin açık ve "Azure 'dan bağlayıcılar kullan" seçiliyken Gezgin bölmesini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Durum bilgisiz iş akışları Şu anda yalnızca Azure 'da dağıtılan ve tetikleyici olmayan [yönetilen bağlayıcılar](../connectors/managed.md)için *eylemleri* desteklemektedir. Azure 'da bağlayıcıları durum bilgisiz iş akışınız için etkinleştirme seçeneğine sahip olsanız da tasarımcı, seçtiğiniz herhangi bir yönetilen bağlayıcı tetikleyicisi göstermez.

1. **Abonelik Seç** listesinde, mantıksal uygulama projeniz Için kullanılacak Azure aboneliğini seçin.

   !["Abonelik Seç" kutusu ve aboneliğiniz seçili Gezgin bölmesini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. Kaynak grupları listesinden **Yeni kaynak grubu oluştur**' u seçin.

   ![Kaynak grupları listesi ve "yeni kaynak grubu oluştur" seçiliyken Gezgin bölmesini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Kaynak grubu için bir ad girin ve ENTER tuşuna basın. Bu örnekte `Fabrikam-Workflows-RG` kullanılmıştır.

   ![Gezgin bölmesi ve kaynak grubu adı kutusunu gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Konumlar listesinden, kaynak grubunuz ve kaynaklarınızı oluştururken kullanılacak Azure bölgesini bulun ve seçin. Bu örnek **Orta Batı ABD** kullanır.

   ![Konum listesi ve "Orta Batı ABD" seçili Gezgin bölmesini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Bu adımı gerçekleştirdikten sonra Visual Studio Code iş akışı tasarımcısını açar.

   > [!NOTE]
   > Visual Studio Code iş akışı tasarım zamanı API 'sini başlattığında, başlatmanın birkaç saniye sürebileceğini belirten bir ileti alabilirsiniz. Bu iletiyi yoksayabilir veya Tamam ' **ı** seçebilirsiniz.
   >
   > Tasarımcı açılmazsa, sorun giderme bölümünü gözden geçirin, [Tasarımcı açılamıyor](#designer-fails-to-open).

   Tasarımcı görüntülendikten sonra, tasarımcı üzerinde **bir Işlem seçme** istemi görüntülenir ve varsayılan olarak seçilidir ve **Eylem Ekle** bölmesini gösterir.

   ![İş akışı tasarımcısını gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Sonra, iş akışınıza [bir tetikleyici ve eylem ekleyin](#add-trigger-actions) .

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Tetikleyici ve eylem ekleme

Tasarımcıyı açtıktan sonra, tasarımcı üzerinde **bir Işlem seçme** istemi görüntülenir ve varsayılan olarak seçilidir. Artık, bir tetikleyici ve eylemler ekleyerek iş akışınızı oluşturmaya başlayabilirsiniz.

Bu örnekteki iş akışı bu tetikleyiciyi ve bu eylemleri kullanır:

* Gelen çağrıları veya istekleri alan ve diğer hizmetlerin veya mantıksal uygulamaların çağırabileceği bir uç nokta oluşturan **BIR http isteği alındığında** yerleşik [istek tetikleyicisi](../connectors/connectors-native-reqres.md).

* [Office 365 Outlook eylemi](../connectors/connectors-create-api-office365-outlook.md), **bir e-posta gönderin**.

* Bir yanıt göndermek ve verileri çağırana geri döndürmek için kullandığınız yerleşik [yanıt eylemi](../connectors/connectors-native-reqres.md).

### <a name="add-the-request-trigger"></a>Istek tetikleyicisini ekleme

1. Tasarımcı 'nın yanında, **bir tetikleyici Ekle** bölmesindeki bir **işlem araması seçin** kutusunda, yerel olarak çalışan bir tetikleyiciyi seçebilmeniz için **yerleşik** ' ın seçildiğinden emin olun.

1. **Bir işlem araması seçin** kutusunda `when a http request` , gırın ve **bir http Isteği alındığında** adlandırılan yerleşik istek tetikleyicisini seçin.

   ![İş akışı tasarımcısını gösteren ekran görüntüsü ve * * "HTTP isteği alındığında bir tetikleyici Ekle" tetikleyicisi seçili.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Tetikleyici tasarımcıda göründüğünde, tetikleyicinin özellikler, ayarlar ve diğer eylemleri göstermek için tetikleyicinin Ayrıntılar bölmesi açılır.

   !["HTTP isteği alındığında" tetikleyicisi seçiliyken ve tetikleyici ayrıntıları bölmesini açık olan iş akışı tasarımcısını gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Ayrıntılar bölmesi görünmezse, tetikleyicinin tasarımcıda seçili olduğundan emin olur.

1. Tasarımcıdan bir öğe silmeniz gerekiyorsa, [öğeleri tasarımcıdan silmek için aşağıdaki adımları izleyin](#delete-from-designer).

### <a name="add-the-office-365-outlook-action"></a>Office 365 Outlook eylemini ekleme

1. Tasarımcıda, eklediğiniz tetikleyicinin altında **yeni adım**' ı seçin.

   **Bir Işlem seçme** istemi tasarımcıda görünür ve bir sonraki eylemi seçebilmeniz Için **Eylem Ekle** bölmesi yeniden açılır.

1. **Eylem Ekle** bölmesinde, **bir işlem seçin** arama kutusunun **altında Azure ' u seçerek Azure** 'da dağıtılan bir yönetilen bağlayıcı için bir eylem bulabilir ve seçebilmeniz gerekir.

   Bu örnek, Office 365 Outlook eylemini seçer ve kullanır, **e-posta gönder (v2)**.

   ![İş akışı tasarımcısını gösteren ekran görüntüsü ve * * Office 365 Outlook "e-posta gönder" eylemi seçiliyken * * bir eylem Ekle * * bölmesi.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. Eylem ayrıntıları bölmesinde, e-posta hesabınıza bir bağlantı oluşturabilmek için **oturum aç** ' ı seçin.

   !["Oturum aç" seçiliyken, iş akışı tasarımcısını ve * * e-posta gönder (v2) * * bölmesini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Visual Studio Code, e-posta hesabınıza erişmek için onay isteminde bulunduğunda **Aç**' ı seçin.

   ![Erişime izin vermek için Visual Studio Code isteminin gösterildiği ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Gelecekteki istemlere engel olmak için, **güvenilir etki alanlarını Yapılandır** ' ı seçerek kimlik doğrulama sayfasını güvenilen bir etki alanı olarak ekleyebilirsiniz.

1. Oturum açmak, erişime izin vermek ve Visual Studio Code dönmeye izin vermek için sonraki istemleri izleyin.

   > [!NOTE]
   > İstekleri tamamlamadan önce çok fazla zaman geçerse, kimlik doğrulama işlemi zaman aşımına uğrar ve başarısız olur. Bu durumda, tasarımcıya geri dönüp bağlantıyı oluşturmak için oturum açmayı yeniden deneyin.

1. Azure Logic Apps (Önizleme) uzantısı, e-posta hesabınıza erişmek için onay girmenizi isterse, **Aç**' ı seçin. Erişime izin vermek için sonraki istemi izleyin.

   ![Erişime izin vermek için Önizleme uzantısı isteminin gösterildiği ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Gelecekteki istemlerin oluşmasını engellemek için, **Bu uzantı için bir daha sorma** seçeneğini belirleyin.

   Visual Studio Code bağlantınız oluşturulduktan sonra bazı bağlayıcılar bu iletiyi gösterir `The connection will be valid for {n} days only` . Bu zaman sınırı, Visual Studio Code ' de mantıksal uygulamanızı yazarken yalnızca süre için geçerlidir. Dağıtımdan sonra, mantıksal uygulamanız otomatik olarak etkinleştirilmiş [sistem tarafından atanan yönetilen kimliği](../logic-apps/create-managed-service-identity.md)kullanarak çalışma zamanında kimlik doğrulayabildiğinden, bu sınır artık geçerli olmaz. Bu yönetilen kimlik, bağlantı oluştururken kullandığınız kimlik doğrulama kimlik bilgilerinden veya bağlantı dizesinden farklıdır. Bu sistem tarafından atanan yönetilen kimliği devre dışı bırakırsanız, bağlantılar çalışma zamanında çalışmaz.

1. Tasarımcıda **e-posta gönder** eylemi seçili görünmüyorsa, bu eylemi seçin.

1. Eylemin Ayrıntılar bölmesindeki **Parametreler** sekmesinde, eylem için gerekli bilgileri sağlayın, örneğin:

   ![İş akışı Tasarımcısı 'nı Office 365 Outlook "e-posta gönder" eylemi hakkında ayrıntılarla gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Özellik | Gerekli | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Kime** | Yes | <*e-posta adresiniz*> | E-posta alıcısı, test amacıyla e-posta adresiniz olabilir. Bu örnek, kurgusal e-postayı kullanır `sophiaowen@fabrikam.com` . |
   | **Konu** | Yes | `An email from your example workflow` | E-posta konusu |
   | **Gövde** | Yes | `Hello from your example workflow!` | E-posta gövdesi içeriği |
   ||||

   > [!NOTE]
   > Ayrıntılar bölmesinde **Ayarlar**, **statik sonuç** veya **sonra Çalıştır** sekmesinden herhangi bir değişiklik yapmak istiyorsanız, sekmelere geçiş yapmadan veya odağı tasarımcıya değiştirmeden önce bu değişiklikleri kaydetmek için **bitti** ' yi seçtiğinizden emin olun. Aksi takdirde, Visual Studio Code değişikliklerinizi tutamayacaktır. Daha fazla bilgi için [GitHub 'Daki bilinen sorunlar Logic Apps Genel Önizleme sayfasını](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)gözden geçirin.

1. Tasarımcıda **Kaydet**' i seçin.

> [!IMPORTANT]
> [YERLEŞIK http Web kancası tetikleyicisi veya eylemi](../connectors/connectors-native-webhook.md)gibi Web kancası tabanlı tetikleyici veya eylemler kullanan bir iş akışını yerel olarak çalıştırmak için, [Web kancasının geri arama URL 'si için iletme ayarlayarak](#webhook-setup)bu yeteneği etkinleştirmeniz gerekir.

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Yerel olarak çalışan Web kancalarını etkinleştir

Azure 'da çalışan bir mantıksal uygulama ile **http Web kancası** gibi bir Web kancası tabanlı tetikleyici veya eylem kullandığınızda Logic Apps çalışma zamanı, bu uç nokta ile bir geri çağırma URL 'si oluşturup kaydederek hizmet uç noktasına abone olur. Tetikleyici veya eylem, hizmet uç noktasının URL 'YI aramasını bekler. Ancak, Visual Studio Code çalışırken, oluşturulan geri çağırma URL 'SI ile başlar `http://localhost:7071/...` . Bu URL, hizmet uç noktasının bu URL 'YI çağıramaması için özel olan localhost sunucunuz içindir.

Web kancası tabanlı Tetikleyicileri ve eylemleri Visual Studio Code yerel olarak çalıştırmak için, localhost sunucunuzu kullanıma sunan ve çağrıları hizmet uç noktasından Web kancası geri çağırma URL 'sine güvenli bir şekilde ileten genel bir URL ayarlamanız gerekir. Localhost bağlantı noktasına bir HTTP tüneli açan ve kendi aracınızı kullanabileceğiniz [**ngrok**](https://ngrok.com/)gibi bir iletme hizmeti ve aracı kullanabilirsiniz.

#### <a name="set-up-call-forwarding-using-ngrok"></a>**Ngrok** kullanarak çağrı iletmeyi ayarlama

1. [Hesabınız yoksa bir **ngrok** hesabına kaydolun](https://dashboard.ngrok.com/signup) . Aksi takdirde [hesabınızda oturum açın](https://dashboard.ngrok.com/login).

1. Kişisel kimlik doğrulama belirtecinizi alın ve bu, **ngrok** istemcinizin hesabınıza erişim ve kimlik doğrulaması için gereken kimlik doğrulamasını gerektirir.

   1. [Kimlik doğrulama belirtecinizi](https://dashboard.ngrok.com/auth/your-authtoken)bulmak için, hesap panosu menüsünde **kimlik doğrulama**' yı genişletin ve **authToken**' ı seçin.

   1. **AuthToken** kutudan, belirteci güvenli bir konuma kopyalayın.

1. [ **Ngrok** indirme sayfasından](https://ngrok.com/download) veya [Hesap panonuzdan](https://dashboard.ngrok.com/get-started/setup)istediğiniz **ngrok** sürümünü indirin ve. zip dosyasını ayıklayın. Daha fazla bilgi için bkz. [1. Adım: sıkıştırmayı açmak için](https://ngrok.com/download).

1. Bilgisayarınızda komut istemi aracınızı açın. **ngrok.exe** dosyanın bulunduğu konuma gidin.

1. Aşağıdaki komutu çalıştırarak **ngrok** istemcisini **ngrok** hesabınıza bağlayın. Daha fazla bilgi için bkz. [2. Adım: hesabınızı bağlama](https://ngrok.com/download).

   `ngrok authtoken <your_auth_token>`

1. Aşağıdaki komutu çalıştırarak localhost bağlantı noktası 7071 için HTTP tüneli açın. Daha fazla bilgi için bkz. [3. Adım: Yangın](https://ngrok.com/download).

   `ngrok http 7071`

1. Çıktıdan aşağıdaki satırı bulun:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Şu biçimdeki URL 'YI kopyalayın ve kaydedin: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>Uygulama ayarlarınızda iletme URL 'sini ayarlama

1. Visual Studio Code, tasarımcıda **http + Web kancası** tetikleyicisi veya eylemi ekleyin.

1. Ana bilgisayar uç noktası konumu için istem belirdiğinde, daha önce oluşturduğunuz iletme (yeniden yönlendirme) URL 'sini girin.

   > [!NOTE]
   > İstemi yoksaymak, iletme URL 'sini sağlamanız gereken bir uyarının görünmesine neden olur, bu nedenle **Yapılandır**' ı seçin ve URL 'yi girin. Bu adımı tamamladıktan sonra, bir sonraki Web kancası Tetikleyicileri veya ekleyebileceğiniz eylemler için istem yeniden görüntülenir.
   >
   > İstemi yeniden görünür hale getirmek için, projenizin kök düzeyinde, dosyanın kısayol menüsünde **local.settings.js** açın ve **Web kancası yeniden yönlendirme uç noktasını yapılandır**' ı seçin. İleti iletme URL 'sini sağlayabilmeniz için artık görüntülenir.

   Visual Studio Code, iletme URL 'sini projenizin kök klasöründeki **local.settings.js** dosyasına ekler. `Values`Nesnesinde, şimdi adlı özelliği `Workflows.WebhookRedirectHostUri` görüntülenir ve iletme URL 'sine ayarlanır, örneğin:
   
   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "node",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         <...>
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

Bir yerel hata ayıklama oturumu başlattığınızda veya iş akışını hata ayıklamadan çalıştırdığınızda, Logic Apps çalışma zamanı iş akışını hizmet uç noktasıyla kaydeder ve Web kancası işlemlerine bildirimde bulunmak için bu uç noktaya abone olur. İş akışınız bir sonraki sefer çalıştığında, abonelik kaydı yerel depolamada zaten mevcut olduğundan çalışma zamanı kayıt veya yeniden iade edilmeyecek.

Yerel Web kancası tabanlı Tetikleyicileri veya eylemleri kullanan bir iş akışı çalıştırmasının hata ayıklama oturumunu durdurduğunuzda, mevcut abonelik kayıtları silinmez. Kaydı silmek için abonelik kayıtlarını el ile kaldırmanız veya silmeniz gerekir.

> [!NOTE]
> İş akışınız çalışmaya başladıktan sonra, Terminal penceresinde şu örnekte olduğu gibi hatalar gösterilebilir:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> Bu durumda, projenin kök klasöründeki **local.settings.jsdosya üzerinde** açın ve özelliğinin şu şekilde ayarlandığından emin olun `true` :
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Hata ayıklama için kesme noktalarını yönetme

Mantıksal uygulama iş akışınızı bir hata ayıklama oturumu başlatarak çalıştırmadan ve test etmeden önce, her bir iş akışı için dosyadaki **workflow.js** [kesme noktaları](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) ayarlayabilirsiniz. Başka kurulum gerekmez. 

Şu anda, kesme noktaları yalnızca eylemler için desteklenir, Tetikleyiciler için desteklenmez. Her eylem tanımında bu kesme noktası konumları vardır:

* Eylemin adını gösteren satırdaki başlangıç kesme noktasını ayarlayın. Hata ayıklama oturumu sırasında bu kesme noktası isabet edildiğinde, eylemin değerlendirilmeden önce giriş girdilerini gözden geçirebilirsiniz.

* Eylemin kapanış küme ayracı (**}**) gösteren satırdaki bitiş kesme noktasını ayarlayın. Hata ayıklama oturumu sırasında bu kesme noktası isabet edildiğinde, eylem tamamlanmadan önce eylemin sonuçlarını gözden geçirebilirsiniz.

Kesme noktası eklemek için aşağıdaki adımları izleyin:

1. Hata ayıklamak istediğiniz iş akışı için dosyada **workflow.js** açın.

1. Kesme noktasını ayarlamak istediğiniz satırda, sol sütunda bu sütunun içinde öğesini seçin. Kesme noktasını kaldırmak için bu kesme noktasını seçin.

   Hata ayıklama oturumunuzu başlattığınızda, kod penceresinin sol tarafında çalıştırma görünümü görünür, ancak hata ayıklama araç çubuğu üst kısımdaki yakınında görünür.

   > [!NOTE]
   > Çalıştırma görünümü otomatik olarak görünmezse, CTRL + SHIFT + D tuşlarına basın.

1. Kesme noktası isabet edildiğinde kullanılabilir bilgileri gözden geçirmek için, Çalıştır görünümünde, **değişkenler** bölmesini inceleyin.

1. İş akışı yürütmeye devam etmek için, hata ayıklama araç çubuğunda **devam** ' ı (Play Button) seçin.

İş akışı çalıştırması sırasında istediğiniz zaman kesme noktaları ekleyebilir ve kaldırabilirsiniz. Ancak, çalıştırma başladıktan sonra dosya **workflow.js** güncelleştirirseniz, kesme noktaları otomatik olarak güncelleştirmez. Kesme noktalarını güncelleştirmek için mantıksal uygulamayı yeniden başlatın.

Genel bilgi için bkz. [kesme noktaları-Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints).

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Yerel olarak çalıştırın, test edin ve hata ayıklayın

Mantıksal uygulamanızı test etmek için aşağıdaki adımları izleyerek bir hata ayıklama oturumu başlatın ve Istek tetikleyicisi tarafından oluşturulan uç noktanın URL 'sini bulun. Daha sonra bu uç noktaya bir istek gönderebilmeniz için bu URL 'ye ihtiyacınız vardır.

1. Durum bilgisi olmayan bir iş akışında daha kolay hata ayıklamak için [Bu iş akışı için çalıştırma geçmişini etkinleştirebilirsiniz](#enable-run-history-stateless).

1. Visual Studio Code etkinlik çubuğunda, **Çalıştır** menüsünü açın ve **hata ayıklamayı Başlat** (F5) seçeneğini belirleyin.

   Hata ayıklama oturumunu gözden geçirebilmeniz için **Terminal** penceresi açılır.

   > [!NOTE]
   > Hata alırsanız, **"preLaunchTask ' generateDebugSymbols ' ' çalıştırıldıktan sonra hata var**. sorun giderme bölümüne bakın, [hata ayıklama oturumunun başlatılması başarısız olur](#debugging-fails-to-start).

1. Şimdi Istek tetikleyicisinde uç nokta için geri çağırma URL 'sini bulun.

   1. Projenizi görüntüleyebilmeniz için Gezgin bölmesini yeniden açın.

   1. Dosyanın kısayol menüsünde **workflow.js** **genel bakış**' ı seçin.

      !["Genel bakış" seçiliyken dosya workflow.jsiçin Gezgin bölmesini ve kısayol penceresini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Örnek Istek tetikleyicisi için bu URL 'ye benzer şekilde görünen **geri çağırma URL 'si** değerini bulun:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Geri çağırma URL 'SI ile iş akışınızın genel bakış sayfasını gösteren ekran görüntüsü](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Mantıksal uygulama iş akışını tetikleyerek geri çağırma URL 'sini test etmek için, istek oluşturma ve gönderme için [Postman](https://www.postman.com/downloads/) veya tercih ettiğiniz aracı açın.

   Bu örnek Postman kullanılarak devam eder. Daha fazla bilgi için bkz. [Postman Başlarken](https://learning.postman.com/docs/getting-started/introduction/).

   1. Postman araç çubuğunda **Yeni**' yi seçin.

      ![Yeni düğme seçiliyken Postman gösteren ekran görüntüsü](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. **Yeni oluştur** bölmesinde, **yapı taşları** altında **istek**' ı seçin.

   1. İsteği **Kaydet** penceresinde, **istek adı** altında, istek için bir ad sağlayın, örneğin, `Test workflow trigger` .

   1. **Kaydedilecek bir koleksiyon veya klasör seçin** altında **koleksiyon oluştur**' u seçin.

   1. **Tüm koleksiyonlar**' ın altında, isteklerinizi düzenlemek için oluşturulacak koleksiyon için bir ad sağlayın, ENTER tuşuna basın ve ***koleksiyon adı* > <için Kaydet**' i seçin. Bu örnek `Logic Apps requests` , koleksiyon adı olarak kullanılır.

      İstek tetikleyicisi için geri çağırma URL 'sine bir istek gönderebilmeniz için Postman 'ın istek bölmesi açılır.

      ![Açılan istek bölmesi ile Postman gösteren ekran görüntüsü](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Visual Studio Code dön. iş akışının genel bakış sayfasından **geri çağırma URL 'si** özellik değerini kopyalayın.

   1. Postman 'a geri dönün. İstek bölmesinde, şu anda varsayılan istek yöntemi olarak **Al** ' ı gösteren Yöntem listesinin ardından, daha önce Adres kutusuna kopyaladığınız geri arama URL 'sini yapıştırın ve **Gönder**' i seçin.

      ![Gönder düğmesinin seçili olduğu adres kutusunda Postman ve geri arama URL 'sini gösteren ekran görüntüsü](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      Örnek mantıksal uygulama iş akışı, bu örneğe benzer şekilde görünen bir e-posta gönderir:

      ![Örnekte açıklandığı gibi Outlook e-postasını gösteren ekran görüntüsü](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. Visual Studio Code, iş akışınızın genel bakış sayfasına dönün.

   Durum bilgisi olan bir iş akışı oluşturduysanız, gönderdiğiniz istek iş akışını tetikler, Genel Bakış sayfasında iş akışının çalışma durumu ve geçmişi gösterilir.

   > [!TIP]
   > Çalıştırma durumu görünmezse, **Yenile**' yi seçerek genel bakış sayfasını yenilemeyi deneyin. Karşılanmayan ölçütler veya veri bulma nedeniyle atlanan bir tetikleyici için çalıştırma gerçekleşilmedi.

   ![Çalışma durumu ve geçmişi olan iş akışının genel bakış sayfasını gösteren ekran görüntüsü](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | Çalışma durumu | Description |
   |------------|-------------|
   | **İptal edildi** | Çalıştırma, dış sorunlar nedeniyle durdurulmuş veya bitmedi; Örneğin, bir sistem kesintisi veya bir Azure aboneliği. |
   | **Yürütüldükten** | Çalıştırma tetiklendi ve başlatıldı, ancak bir iptal isteği alındı. |
   | **Başarısız** | Çalıştırmada en az bir eylem başarısız oldu. Hata işlemek için iş akışında sonraki hiçbir eylem ayarlanmadı. |
   | **Çalışma** | Çalıştırma tetiklendi ve devam ediyor, ancak bu durum [eylem sınırları](logic-apps-limits-and-config.md) veya [geçerli fiyatlandırma planı](https://azure.microsoft.com/pricing/details/logic-apps/)nedeniyle kısıtlanan bir çalıştırma için de görünebilir. <p><p>**İpucu**: [tanılama günlüğü](monitor-logic-apps-log-analytics.md)ayarlarsanız, gerçekleşen tüm kısıtlama olayları hakkında bilgi edinebilirsiniz. |
   | **Başarılı** | Çalıştırma başarılı oldu. Herhangi bir eylem başarısız olursa, iş akışındaki sonraki bir eylem bu hatayı işledi. |
   | **Zaman aşımına uğradı** | Geçerli süre, [ **gün cinsinden çalışma geçmişi tutma** ayarı](logic-apps-limits-and-config.md#run-duration-retention-limits)tarafından denetlenen çalışma süresi sınırını aştığından çalıştırma zaman aşımına uğradı. Bir çalıştırmanın süresi, çalışma başlangıç zamanı ve çalıştırma süresi sınırı kullanılarak bu başlangıç saatinde hesaplanır. <p><p>**Note**: çalıştırmanın süresi Ayrıca, [ **gün cinsinden çalıştırma geçmişi tutma**](logic-apps-limits-and-config.md#run-duration-retention-limits)tarafından da denetlenen geçerli *çalışma geçmişi saklama sınırını* aşarsa, çalıştırma, günlük temizleme işi tarafından temizlenir. Çalışma zamanının zaman aşımına uğrayıp tamamlanmayacağı, saklama dönemi her zaman çalıştırmanın başlangıç zamanı ve *geçerli* saklama limiti kullanılarak hesaplanır. Bu nedenle, bir uçuş çalışmasının süre sınırını azaltmanız durumunda çalıştırma zaman aşımına uğrar. Ancak çalıştırma, çalıştırma geçmişinden, çalışma süresinin bekletme sınırını aşıp aşılmadığını temel alarak, çalışır durumda kalır veya temizlenir. |
   | **Bekleme** | Çalışan, hala çalışmakta olan önceki bir iş akışı örneği nedeniyle başlatılmış veya duraklatılmış. |
   |||

1. Belirli bir çalıştırmada ve adımın giriş ve çıktılarında her adımın durumlarını gözden geçirmek için, bu çalıştırma için üç nokta (**...**) düğmesini seçin ve **çalıştırmayı göster**' i seçin.

   ![İş akışınızın çalışma geçmişi satırını üç nokta düğmesi ve "çalışmayı göster" seçiliyken gösteren ekran görüntüsü](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code izleme görünümünü açar ve çalıştırmada her adımın durumunu gösterir.

   ![İş akışı çalıştırındaki her adımı ve bunların durumunu gösteren ekran görüntüsü](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Bir çalıştırma başarısız olursa ve izleme görünümündeki bir adım `400 Bad Request` hata gösteriyorsa, bu sorun temel alınan Tekdüzen Kaynak tanımlayıcısı 'nın (URI) varsayılan karakter sınırını aşmasına neden olan daha uzun bir tetikleyici adı veya eylem adından kaynaklanabilir. Daha fazla bilgi için bkz. ["400 hatalı istek"](#400-bad-request).

   İşte iş akışındaki her adımın sahip olduğu olası durumlar şunlardır:

   | Eylem durumu | Simge | Description |
   |---------------|------|-------------|
   | **İptal edildi** | !["Durdurulan" eylem durumu simgesi][aborted-icon] | Bu eylem, dış sorunlar nedeniyle durdurulmuş veya bitmedi; Örneğin, bir sistem kesintisi veya bir Azure aboneliği. |
   | **Yürütüldükten** | !["Iptal edildi" eylem durumu simgesi][cancelled-icon] | Eylem çalışıyor ancak iptal etmek için bir istek alındı. |
   | **Başarısız** | !["Başarısız" eylem durumu simgesi][failed-icon] | Eylem başarısız oldu. |
   | **Çalışma** | !["Çalışıyor" eylem durumu simgesi][running-icon] | Eylem şu anda çalışıyor. |
   | **Atlandı** | !["Atlanan" eylem durumu simgesi][skipped-icon] | Eylem, hemen önceki eylem başarısız olduğu için atlandı. Bir eylem, `runAfter` Geçerli eylemin çalıştırılabilmesi için önceki eylemin başarıyla çalışmasını gerektiren bir koşula sahiptir. |
   | **Başarılı** | !["Başarılı" eylem durumu simgesi][succeeded-icon] | Eylem başarılı oldu. |
   | **Yeniden denemeler ile başarılı oldu** | !["Yeniden denemeler tamamlandı" eylem durumu simgesi][succeeded-with-retries-icon] | Eylem, yalnızca bir veya daha fazla yeniden denemeden sonra başarılı oldu. Yeniden deneme geçmişini gözden geçirmek için, çalışma geçmişi Ayrıntıları görünümünde, giriş ve çıkışları görüntüleyebilmeniz için bu eylemi seçin. |
   | **Zaman aşımına uğradı** | !["Zaman aşımına uğradı" eylem durumu simgesi][timed-out-icon] | Eylem, bu eylemin ayarları tarafından belirtilen zaman aşımı sınırı nedeniyle durduruldu. |
   | **Bekleme** | !["Bekleniyor" eylem durumu simgesi][waiting-icon] | Çağırandan gelen istekleri bekleyen bir Web kancası eylemi için geçerlidir. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/waiting.png

1. Her adımın giriş ve çıkışlarını gözden geçirmek için, incelemek istediğiniz adımı seçin.

   ![İş akışındaki her adımın durumunu ve genişletilmiş "e-posta gönder" eyleminde giriş ve çıkışları gösteren ekran görüntüsü](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Bu adım için ham giriş ve çıkışları daha fazla gözden geçirmek için **Ham girdileri göster** veya **Ham çıkışları göster**' i seçin.

1. Hata ayıklama oturumunu durdurmak için, **Çalıştır** menüsünde, **hata ayıklamayı Durdur** (SHIFT + F5) öğesini seçin.

<a name="return-response"></a>

## <a name="return-a-response"></a>Yanıt döndürün

Mantıksal uygulamanıza bir istek gönderen arayana bir yanıt döndürmek için, Istek tetikleyicisiyle başlayan bir iş akışı için yerleşik [Yanıt eylemini](../connectors/connectors-native-reqres.md) kullanabilirsiniz.

1. İş akışı tasarımcısında, **e-posta gönder** eyleminin altında **yeni adım**' ı seçin.

   **Bir Işlem seçme** istemi tasarımcıda görünür ve bir sonraki eylemi seçebilmeniz Için **Eylem Ekle bölmesi** yeniden açılır.

1. **Eylem Ekle** bölmesinde, **eylem** Ekle arama kutusunda, **yerleşik** ' ın seçili olduğundan emin olun. Arama kutusuna girin `response` ve **Yanıt** eylemini seçin.

   ![Yanıt eylemi seçili olan iş akışı tasarımcısını gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Tasarımcı üzerinde **Yanıt** eylemi göründüğünde, eylemin Ayrıntılar bölmesi otomatik olarak açılır.

   !["Yanıt" eyleminin Ayrıntılar bölmesine sahip iş akışı tasarımcısını ve "e-posta gönder" eyleminin "Body" özellik değerini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. **Parametreler** sekmesinde çağırmak istediğiniz işlev için gerekli bilgileri sağlayın.

   Bu örnek, **e-posta gönder** eyleminden çıktı olan **gövde** özelliği değerini döndürür.

   1. Dinamik içerik listesinin görünmesi için **Body** özellik kutusunun içine tıklayın ve yukarıdaki tetikleyiciden ve iş akışındaki eylemlerden kullanılabilir çıkış değerlerini gösterir.

      ![Dinamik içerik listesinin görünmesi için "gövde" özelliğinin içindeki fare işaretçisi içindeki "yanıt" eyleminin Ayrıntılar bölmesini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Dinamik içerik listesinde, **e-posta gönder**' ın altında **gövde**' yi seçin.

      ![Açık dinamik içerik listesini gösteren ekran görüntüsü. Listede, "e-posta gönder" üstbilgisinin altında "gövde" çıkış değeri seçilidir.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      İşiniz bittiğinde, yanıt eyleminin **gövde** özelliği artık **e-posta gönder** eyleminin **gövde** çıkış değeri olarak ayarlanır.

      ![İş akışındaki her adımın durumunu ve genişletilmiş "yanıt" eyleminde giriş ve çıkışları gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. Tasarımcıda **Kaydet**' i seçin.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Mantıksal uygulamanızı yeniden test etme

Mantıksal uygulamanızda güncelleştirme yaptıktan sonra, hata ayıklayıcıyı Visual Studio 'da yeniden çalıştırarak ve güncelleştirilmiş mantıksal uygulamanızı tetiklemek için başka bir istek göndererek, [yerel olarak çalıştırma, test etme ve hata ayıklama](#run-test-debug-locally)içindeki adımlara benzer bir test çalıştırabilirsiniz.

1. Visual Studio Code etkinlik çubuğunda, **Çalıştır** menüsünü açın ve **hata ayıklamayı Başlat** (F5) seçeneğini belirleyin.

1. İstek oluşturmaya ve göndermeye yönelik Postman veya aracınız içinde, iş akışınızı tetiklemek için başka bir istek gönderin.

1. Durum bilgisi olan bir iş akışı oluşturduysanız, iş akışının genel bakış sayfasında en son çalıştırmanın durumunu kontrol edin. Söz konusu çalıştırmada her adımın durumunu, girişlerini ve çıkışlarını görüntülemek için, bu çalıştırma için üç nokta (**...**) düğmesini seçin ve **çalıştırmayı göster**' i seçin.

   Örneğin, örnek iş akışı yanıt eylemiyle güncelleştirildikten sonra bir çalıştırmanın adım adım durumu aşağıda verilmiştir.

   ![Güncelleştirilmiş iş akışındaki her adımın durumunu ve genişletilmiş "yanıt" eyleminde giriş ve çıkışları gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Hata ayıklama oturumunu durdurmak için, **Çalıştır** menüsünde, **hata ayıklamayı Durdur** (SHIFT + F5) öğesini seçin.

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>Güvenlik duvarı erişimi için etki alanı adlarını bulma

Mantıksal uygulama iş akışınızı Azure portal dağıtmadan ve çalıştırmadan önce, ortamınızda trafiği sınırlayan katı ağ gereksinimleri veya güvenlik duvarları varsa, iş akışınızda mevcut olan herhangi bir tetikleyici veya eylem bağlantısı için izinleri ayarlamanız gerekir.

Bu bağlantılar için tam etki alanı adlarını (FQDN) bulmak için şu adımları izleyin:

1. Mantıksal uygulama projenizde, iş akışınıza ilk bağlantı tabanlı tetikleyiciyi veya eylemi ekledikten sonra oluşturulan **connections.js** dosyasını açın ve `managedApiConnections` nesneyi bulun.

1. Oluşturduğunuz her bağlantı için, `connectionRuntimeUrl` Bu bilgilerle güvenlik duvarınızı ayarlayabilmeniz için özellik değerini güvenli bir yerde bulun, kopyalayın ve kaydedin.

   Bu örnek **connections.js** dosyada iki bağlantı, bir AS2 bağlantısı ve şu değerlere sahip bir Office 365 bağlantısı bulunmaktadır `connectionRuntimeUrl` :

   * AS2 `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

   * Office 365: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f`

   ```json
   {
      "managedApiConnections": {
         "as2": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/as2"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         },
         "office365": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/office365"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         }
      }
   }
   ```

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Azure’a dağıtın

Visual Studio Code, projenizi doğrudan Azure 'da yayımlayabilirsiniz. Bu, yeni **mantıksal uygulama (Önizleme)** kaynak türünü kullanarak mantıksal uygulamanızı dağıtır. Azure Işlevlerinde işlev uygulaması kaynağına benzer şekilde, bu yeni kaynak türüne yönelik dağıtım, dağıtım sırasında ayarlayabileceğiniz bir [barındırma planı ve fiyatlandırma katmanı](../app-service/overview-hosting-plans.md)seçmenizi gerektirir. Barındırma planları ve fiyatlandırma hakkında daha fazla bilgi için şu konuları gözden geçirin:

* [Azure App Service ölçeği büyütme](../app-service/manage-scale-up.md)
* [Azure İşlevleri’ni ölçeklendirme ve barındırma](../azure-functions/functions-scale.md)

Mantıksal uygulamanızı, [işlev uygulaması gereksinimlerine benzer şekilde, Azure depolama hesabı](../azure-functions/storage-considerations.md)gibi gerekli kaynakları otomatik olarak oluşturan yeni bir kaynak olarak yayımlayabilirsiniz. Ya da mantıksal uygulamanızı daha önce dağıtılan bir **mantıksal uygulama (Önizleme)** kaynağına yayımlayabilirsiniz ve bu mantıksal uygulamanın üzerine yazar.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>Yeni bir mantıksal uygulama (Önizleme) kaynağına Yayımla

1. Visual Studio Code etkinlik çubuğunda Azure simgesini seçin.

1. **Azure: Logic Apps (Önizleme)** bölmesi araç çubuğunda, **mantıksal uygulamaya dağıt**' ı seçin.

   !["Mantıksal uygulamaya dağıt" seçiliyken "Azure: Logic Apps (Önizleme)" bölmesinin ve bölmesinin araç çubuğunun gösterildiği ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. İstenirse, mantıksal uygulama dağıtımınız için kullanılacak Azure aboneliğini seçin.

1. Visual Studio Code açılan listeden aşağıdaki seçeneklerden birini belirleyin:

   * **Azure 'da yeni mantıksal uygulama (Önizleme) oluşturma** (hızlı)
   * **Azure gelişmiş 'de yeni mantıksal uygulama (Önizleme) oluşturma**
   * Varsa, önceden dağıtılan bir **mantıksal uygulama (Önizleme)** kaynağı

   Bu örnek, **Azure Gelişmiş ' te yeni mantıksal uygulama (Önizleme) oluşturma** ile devam eder.

   !["Azure 'da yeni mantıksal uygulama (Önizleme) oluşturma" seçiliyken "Azure: Logic Apps (Önizleme)" bölmesini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Yeni **mantıksal uygulama (Önizleme)** kaynağını oluşturmak için aşağıdaki adımları izleyin:

   1. **Mantıksal uygulama (Önizleme)** kaynağı için kullanılacak ad olan yeni mantıksal uygulamanız için genel olarak benzersiz bir ad sağlayın. Bu örnekte `Fabrikam-Workflows-App` kullanılmıştır.

      !["Azure: Logic Apps (Önizleme)" bölmesinin ve oluşturulacak yeni mantıksal uygulama için bir ad sağlamak üzere bir istem gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. [ **App Service planı** (adanmış)](../azure-functions/dedicated-plan.md) veya [**Premium**](../azure-functions/functions-premium-plan.md)olan yeni mantıksal uygulamanız için bir [barındırma planı](../app-service/overview-hosting-plans.md) seçin.

      > [!IMPORTANT]
      > Tüketim planları bu kaynak türü için desteklenmiyor veya kullanılamıyor. Seçtiğiniz plan, daha sonra kullanabileceğiniz özellikleri ve fiyatlandırma katmanlarını etkiler. Daha fazla bilgi için şu konuları gözden geçirin: 
      >
      > * [Azure İşlevleri’ni ölçeklendirme ve barındırma](../azure-functions/functions-scale.md)
      > * [App Service fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > Örneğin, Premium planı, mantıksal uygulamalarınızı oluştururken ve dağıtırken Azure Işlevlerine benzer şekilde, Azure sanal ağlarla özel olarak bağlanma ve tümleştirme gibi ağ özelliklerine erişim sağlar. 
      > Daha fazla bilgi için şu konuları gözden geçirin:
      > 
      > * [Azure İşlevleri ağ seçenekleri](../azure-functions/functions-networking-options.md)
      > * [Azure Logic Apps önizlemesiyle her yerde ağ olasılıklarını çalıştırmak Azure Logic Apps](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

      Bu örnek **App Service planını** kullanır.

      !["Azure: Logic Apps (Önizleme)" bölmesinin ve "App Service planı" veya "Premium" seçilecek bir istem gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Yeni bir App Service planı oluşturun veya var olan bir planı seçin. Bu örnek **yeni App Service planı oluştur**' a seçer.

      !["Azure: Logic Apps (Önizleme)" bölmesini ve "Yeni App Service planı oluşturma" istemi ve var olan bir App Service planı seçme istemi gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. App Service planınız için bir ad girin ve ardından plan için bir [fiyatlandırma katmanı](../app-service/overview-hosting-plans.md) seçin. Bu örnek **F1 ücretsiz** planını seçer.

      !["Azure: Logic Apps (Önizleme)" bölmesinin ve bir fiyatlandırma katmanını seçmek için bir istem gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. En iyi performans için, dağıtım için projenizle aynı kaynak grubunu bulun ve seçin.

      > [!NOTE]
      > Farklı bir kaynak grubu oluşturabilseniz veya kullanabilseniz de, bu durum performansı etkileyebilir. Farklı bir kaynak grubu oluşturur veya seçer, ancak onay istemi görüntülendikten sonra iptal edilirse, dağıtımınız de iptal edilir.

   1. Durum bilgisi olan iş akışları için **Yeni depolama hesabı** veya mevcut bir depolama hesabı oluştur ' u seçin.

      !["Azure: Logic Apps (Önizleme)" bölmesini gösteren ekran görüntüsü ve bir depolama hesabı oluşturmak veya seçmek için bir istem.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Mantıksal uygulamanızın oluşturma ve dağıtım ayarları [Application Insights](../azure-monitor/app/app-insights-overview.md)kullanmayı destekliyorsa, mantıksal uygulamanız için isteğe bağlı olarak tanılama günlüğünü ve izlemeyi etkinleştirebilirsiniz. Mantıksal uygulamanızı Visual Studio Code veya dağıtımdan sonra dağıtırken bunu yapabilirsiniz. Bir Application Insights örneğiniz olması gerekir, ancak mantıksal uygulamanızı dağıtırken ya da dağıtımdan sonra bu kaynağı [önceden](../azure-monitor/app/create-workspace-resource.md)oluşturabilirsiniz.

      Günlüğe kaydetmeyi ve izlemeyi şimdi etkinleştirmek için şu adımları izleyin:

      1. Mevcut bir Application Insights kaynağı seçin ya da **yeni Application Insights kaynağı oluşturun**.

      1. [Azure portal](https://portal.azure.com)Application Insights kaynağına gidin.

      1. Kaynak menüsünde **genel bakış**' ı seçin. **Izleme anahtarı** değerini bulup kopyalayın.

      1. Visual Studio Code, projenizin kök klasöründe **local.settings.jsdosya üzerinde** açın.

      1. Nesnesinde, `Values` `APPINSIGHTS_INSTRUMENTATIONKEY` özelliğini ekleyin ve değeri izleme anahtarına ayarlayın, örneğin:

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "UseDevelopmentStorage=true",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet",
               "APPINSIGHTS_INSTRUMENTATIONKEY": <instrumentation-key>
            }
         }
         ```

         > [!TIP]
         > Tetikleyici ve Eylem adlarının Application Insights Örneğinizde doğru şekilde görünüp görünmediğini kontrol edebilirsiniz.
         >
         > 1. Azure portal Application Insights kaynağına gidin.
         >
         > 2. Kaynak kaynağı menüsündeki **Araştır** altında **uygulama Haritası**' nı seçin.
         >
         > 3. Haritada görüntülenen işlem adlarını gözden geçirin.
         >
         > Yerleşik tetikleyicilerden gelen bazı gelen istekler, uygulama haritasında yinelenen olarak görünebilir. 
         > Biçimini kullanmak yerine `WorkflowName.ActionName` , bu yinelemeler iş akışı adını işlem adı olarak kullanır ve Azure işlevleri ana bilgisayarında yapılır.

      1. Daha sonra, mantıksal uygulamanızın topladığı ve Application Insights örneğine gönderdiği izleme verileri için isteğe bağlı olarak önem derecesini ayarlayabilirsiniz.

         İş akışı ile ilgili bir olay olduğunda (örneğin, bir iş akışı tetiklendiğinde veya bir eylem çalıştırıldığında), çalışma zamanı çeşitli izlemeleri yayar. Bu izlemeler iş akışının ömrünü kapsar ve aşağıdaki olay türlerini içerir, ancak bunlarla sınırlı değildir:

         * Başlatma, durdurma ve hatalar gibi hizmet etkinliği.
         * İşler ve dağıtıcı etkinliği.
         * Tetikleyici, eylem ve çalıştırma gibi iş akışı etkinliği.
         * Başarılı veya başarısız gibi depolama isteği etkinliği.
         * Gelen, giden, başarı ve başarısızlık gibi HTTP isteği etkinliği.
         * Hata ayıklama iletileri gibi tüm geliştirme izlemeleri.

         Her olay türü bir önem düzeyine atanır. Örneğin, düzey `Trace` en ayrıntılı iletileri yakalar, ancak `Information` mantıksal uygulamanız, iş akışınız, Tetikleyiciniz ve eylemler başlatıldığında ve durdurulduğunda olduğu gibi, düzey iş akışınızda genel etkinlik yakalar. Bu tablo önem düzeylerini ve izleme türlerini açıklar:

         | Önem derecesi | İzleme türü |
         |----------------|------------|
         | Kritik | Mantıksal uygulamanızda kurtarılamaz bir hatayı tanımlayan Günlükler. |
         | Hata Ayıklama | Geliştirme sırasında, örneğin gelen ve giden HTTP çağrılarında İnceleme için kullanabileceğiniz Günlükler. |
         | Hata | İş akışı yürütmesindeki bir hata olduğunu gösteren Günlükler, ancak mantıksal uygulamanızda genel bir hata değildir. |
         | Bilgi | Mantıksal uygulamanızda veya iş akışınızda genel etkinliği izleyen Günlükler, örneğin: <p><p>-Bir tetikleyici, eylem veya çalıştırma başladığında ve sona erdiğinde. <br>-Mantıksal uygulamanız başlatıldığında veya sona erdiğinde. |
         | İzleme | En ayrıntılı iletileri (örneğin, depolama istekleri veya dağıtıcı etkinliği) ve iş akışı yürütme etkinliğiyle ilgili tüm iletileri içeren Günlükler. |
         | Uyarı | Mantıksal uygulamanızda olağan dışı bir durumu vurgulayan ancak çalışmasını engellemez olan Günlükler. |
         |||

         Önem düzeyini ayarlamak için, projenizin kök düzeyinde **host.jsdosya üzerinde** açın ve `logging` nesneyi bulun. Bu nesne, mantıksal uygulamanızdaki tüm iş akışlarının günlük filtrelemesini denetler ve [günlük türü filtrelemesi için ASP.NET Core düzeni](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering)izler.

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               }
            }
         }
         ```

         Nesne, `logging` özelliği içeren bir `logLevel` nesne içermiyorsa `Host.Triggers.Workflow` , bu öğeleri ekleyin. Özelliğini istediğiniz izleme türünün önem derecesi düzeyine ayarlayın, örneğin:

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               },
               "logLevel": {
                  "Host.Triggers.Workflow": "Information"
               }
            }
         }
         ```

   Dağıtım adımlarıyla işiniz bittiğinde, Visual Studio Code mantıksal uygulamanızı yayımlamak için gerekli kaynakları oluşturmaya ve dağıtmaya başlar.

1. Dağıtım sürecini gözden geçirmek ve izlemek için, **Görünüm** menüsünde **Çıkış**' ı seçin. Çıkış penceresi araç çubuğu listesinden **Azure Logic Apps**' yi seçin.

   ![Dağıtım ilerleme durumu ve durumları ile birlikte araç çubuğu listesinde seçili olan "Azure Logic Apps" ile çıkış penceresini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Visual Studio Code mantıksal uygulamanızı Azure 'a dağıtma işlemi tamamlandığında aşağıdaki ileti görüntülenir:

   ![Azure 'a dağıtımın başarıyla tamamlandığını belirten bir ileti gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Tebrikler, mantıksal uygulamanız artık Azure 'da canlı ve varsayılan olarak etkinleştirilmiştir.

Daha sonra, bu görevleri nasıl gerçekleştireceğinizi öğrenebilirsiniz:

* [Projenize boş bir iş akışı ekleyin](#add-workflow-existing-project).

* [Dağıtılan mantıksal uygulamaları Visual Studio Code](#manage-deployed-apps-vs-code) veya [Azure Portal](#manage-deployed-apps-portal)kullanarak yönetin.

* [Durum bilgisiz iş akışlarında çalıştırma geçmişini etkinleştirin](#enable-run-history-stateless).

* [Dağıtılan bir mantıksal uygulama için Azure Portal izleme görünümünü etkinleştirin](#enable-monitoring).

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Projeye boş iş akışı Ekle

Mantıksal uygulama projenizde birden çok iş akışı olabilir. Projenize boş bir iş akışı eklemek için şu adımları izleyin:

1. Visual Studio Code etkinlik çubuğunda Azure simgesini seçin.

1. Azure bölmesinde **Azure: Logic Apps (Önizleme)** yanındaki **iş akışı oluştur** ' u (Azure Logic Apps için simge) seçin.

1. Eklemek istediğiniz iş akışı türünü seçin: **durum bilgisi olan** veya **durumsuz**

1. İş akışınız için bir ad sağlayın.

İşiniz bittiğinde, projenizde yeni bir iş akışı klasörü, iş akışı tanımı için bir **workflow.js** dosyası ile birlikte görüntülenir.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Visual Studio Code dağıtılan mantıksal uygulamaları yönetme

Visual Studio Code, özgün **Logic Apps** veya **mantıksal uygulama (Önizleme)** kaynak türü olup olmadıkları ve bu mantıksal uygulamaları yönetmenize yardımcı olan görevleri seçmek üzere Azure aboneliğinizde dağıtılan tüm mantıksal uygulamaları görüntüleyebilirsiniz. Ancak, her iki kaynak türüne de erişmek için, Visual Studio Code için hem **Azure Logic Apps** hem de **Azure Logic Apps (Önizleme)** uzantılarına sahip olmanız gerekir.

1. Sol araç çubuğunda Azure simgesini seçin. **Azure: Logic Apps (Önizleme)** bölmesinde, bu abonelik için dağıtılan tüm mantıksal uygulamaları gösteren aboneliğinizi genişletin.

1. Yönetmek istediğiniz mantıksal uygulamayı açın. Mantıksal uygulamanın kısayol menüsünde, gerçekleştirmek istediğiniz görevi seçin.

   Örneğin, dağıtılan mantıksal uygulamanızı durdurma, başlatma, yeniden başlatma veya silme gibi görevleri seçebilirsiniz.

   ![Açılan "Azure Logic Apps (Önizleme)" uzantı bölmesi ve dağıtılan iş akışı ile Visual Studio Code gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Mantıksal uygulamadaki tüm iş akışlarını görüntülemek için mantıksal uygulamanızı genişletin ve sonra **Iş akışları** düğümünü genişletin.

1. Belirli bir iş akışını görüntülemek için, iş akışının kısayol menüsünü açın ve **tasarımcıda aç**' ı seçerek iş akışını salt okunurdur modda açın.

   İş akışını düzenlemek için şu seçeneklere sahipsiniz:

   * Visual Studio Code, projenizin iş akışı tasarımcısında dosya **workflow.js** açın, düzenlemelerinizi yapın ve mantıksal uygulamanızı Azure 'a yeniden dağıtın.

   * Azure portal, [mantıksal uygulamanızı bulun ve açın](#manage-deployed-apps-portal). İş akışını bulun, düzenleyin ve kaydedin.

1. Azure portal dağıtılan mantıksal uygulamayı açmak için mantıksal uygulamanın kısayol menüsünü açın ve **portalda aç**' ı seçin.

   Azure portal tarayıcınızda açılır, Visual Studio Code oturumu açtıysanız ve mantıksal uygulamanızı gösteriyorsa, portalda otomatik olarak oturum açar.

   ![Visual Studio Code ' de mantıksal uygulamanızın Azure portal sayfasını gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Ayrıca, Azure portal ayrı olarak oturum açabilir, mantıksal uygulamanızı bulmak için Portal arama kutusunu kullanabilir ve sonra sonuçlar listesinden mantıksal uygulamanızı seçebilirsiniz.

   ![Azure portal ve arama çubuğunu, dağıtılan mantıksal uygulama için arama sonuçlarıyla gösteren ve seçili görüntülenen ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Portalda dağıtılan mantıksal uygulamaları yönetme

Azure portal, Azure aboneliğinizdeki tüm dağıtılan mantıksal uygulamaları, özgün **Logic Apps** kaynak türü veya **mantıksal uygulama (Önizleme)** kaynak türü olup olmadıkları gibi görüntüleyebilirsiniz. Şu anda her kaynak türü Azure 'da ayrı kategoriler olarak düzenlenir ve yönetilir. **Mantıksal uygulama (Önizleme)** kaynak türüne sahip mantıksal uygulamaları bulmak için şu adımları izleyin:

1. Azure portal arama kutusuna girin `logic app preview` . Sonuçlar listesi göründüğünde, **Hizmetler** altında **mantıksal uygulama (Önizleme)** öğesini seçin.

   !["Mantıksal uygulama önizlemesi" arama metniyle Azure portal arama kutusunu gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. **Mantıksal uygulama (Önizleme)** bölmesinde, Visual Studio Code dağıttığınız mantıksal uygulamayı bulun ve seçin.

   ![Azure 'da dağıtılan Azure portal ve mantıksal uygulama (Önizleme) kaynaklarını gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Azure portal, seçilen mantıksal uygulamanın tek tek kaynak sayfasını açar.

   ![Mantıksal uygulama iş akışınızın kaynak sayfasını Azure portal gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Bu mantıksal uygulamadaki iş akışlarını görüntülemek için mantıksal uygulamanın menüsünde **Iş akışları**' nı seçin.

   **Iş akışları** bölmesi, geçerli mantıksal uygulamadaki tüm iş akışlarını gösterir. Bu örnek, Visual Studio Code oluşturduğunuz iş akışını gösterir.

   !["Iş akışları" bölmesi açık olan "Logic App (Önizleme)" kaynak sayfasını ve dağıtılan iş akışını gösteren ekran görüntüsü](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Bir iş akışını görüntülemek için, iş **akışları** bölmesinde o iş akışını seçin.

   İş akışı bölmesi açılır ve bu iş akışında gerçekleştirebileceğiniz daha fazla bilgi ve görev gösterir.

   Örneğin, iş akışındaki adımları görüntülemek için **Tasarımcı**' yı seçin.

   ![Seçilen iş akışının "genel bakış" bölmesini gösteren ekran görüntüsü, iş akışı menüsü seçili "tasarımcı" komutunu gösterir.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   İş akışı Tasarımcısı açılır ve Visual Studio Code içinde derlediniz iş akışını gösterir. Artık Azure portal bu iş akışında değişiklik yapabilirsiniz.

   ![İş akışı tasarımcısını ve Visual Studio Code dağıtılan iş akışını gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Portalda başka bir iş akışı ekleme

Azure portal aracılığıyla, Visual Studio Code dağıttığınız bir **mantıksal uygulama (Önizleme)** kaynağına boş iş akışları ekleyebilir ve bu iş akışlarını Azure Portal oluşturabilirsiniz.

1. [Azure Portal](https://portal.azure.com), dağıtılan **mantıksal uygulama (Önizleme)** kaynağını bulup seçin.

1. Mantıksal uygulama menüsünde **Iş akışları**' nı seçin. **Iş akışları** bölmesinde **Ekle**' yi seçin.

   ![Seçilen mantıksal uygulamanın "Iş akışları" bölmesini ve araç çubuğunu "Ekle" komutu seçiliyken gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. **Yeni iş akışı** bölmesinde iş akışı için ad belirtin. **Durum bilgisi olan** veya **durum bilgisiz** **>** **Oluştur** seçeneğini belirleyin.

   Azure yeni iş akışınızı dağıtduktan sonra, iş **akışları** bölmesinde görünen iş akışını, tasarımcı veya kod görünümünü açma gibi diğer görevleri yönetebilmeniz ve gerçekleştirebilmek için seçin.

   ![Seçili iş akışını yönetim ve gözden geçirme seçenekleriyle gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Örneğin, yeni bir iş akışı için tasarımcıyı açmak boş bir tuval gösterir. Artık Azure portal bu iş akışını oluşturabilirsiniz.

   ![İş akışı tasarımcısını ve boş bir iş akışını gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Durum bilgisiz iş akışları için çalıştırma geçmişini etkinleştir

Durum bilgisi olmayan bir iş akışında daha kolay hata ayıklamak için bu iş akışı için çalıştırma geçmişini etkinleştirebilir ve sonra işiniz bittiğinde çalıştırma geçmişini devre dışı bırakabilirsiniz. Visual Studio Code için bu adımları izleyin veya Azure portal çalışıyorsanız, bkz. [Azure Portal durum bilgisiz ve durum bilgisi olmayan iş akışları oluşturma](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless).

1. Visual Studio Code projenizde, **iş akışı-tasarımsaati** klasörünü genişletin ve **local.settings.jsdosya üzerinde** açın.

1. Özelliği ekleyin `Workflows.{yourWorkflowName}.operationOptions` ve değeri olarak ayarlayın `WithStatelessRunHistory` , örneğin:

   **Windows**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

   **macOS veya Linux**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct; \
             AccountKey=<access-key>;EndpointSuffix=core.windows.net",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. İşiniz bittiğinde çalıştırma geçmişini devre dışı bırakmak için `Workflows.{yourWorkflowName}.OperationOptions` özelliğini olarak ayarlayın `None` ya da özelliği ve değerini silin.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Azure portal izleme görünümünü etkinleştir

Visual Studio Code bir **mantıksal uygulama (Önizleme)** kaynağını Azure 'a dağıttıktan sonra, söz konusu iş akışı için Azure Portal ve **izleme** deneyimini kullanarak bu kaynaktaki bir iş akışının tüm kullanılabilir çalışma geçmişini ve ayrıntılarını gözden geçirebilirsiniz. Ancak, önce bu mantıksal uygulama kaynağında **izleyici** görünümü özelliğini etkinleştirmeniz gerekir.

1. [Azure Portal](https://portal.azure.com)dağıtılan **mantıksal uygulama (Önizleme)** kaynağını bulup seçin.

1. Bu kaynağın menüsünde, **API** altında **CORS**' yi seçin.

1. **CORS** bölmesinde, **izin verilen** kaynaklar ' ın altında, joker karakter (*) ekleyin.

1. İşiniz bittiğinde, **CORS** araç çubuğundan **Kaydet**' i seçin.

   ![Dağıtılan bir mantıksal uygulama (Önizleme) kaynağıyla Azure portal gösteren ekran görüntüsü. "CORS" kaynak menüsünde "Izin verilen kaynaklar" joker karakter "*" karakterine ayarlanmış yeni bir girdiyle birlikte seçilir.](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Dağıtımdan sonra Application Insights etkinleştir veya aç

İş akışı yürütülürken, mantıksal uygulamanız telemetri ve diğer olaylarla birlikte yayar. İş akışınızın ne kadar iyi görünebileceği ve Logic Apps çalışma zamanının çeşitli yollarla nasıl çalıştığı hakkında daha iyi görünürlük almak için bu Telemetriyi kullanabilirsiniz. İş akışınızı, neredeyse gerçek zamanlı telemetri (canlı ölçümler) sağlayan [Application Insights](../azure-monitor/app/app-insights-overview.md)kullanarak izleyebilirsiniz. Bu özellik sorunları tanılamak, uyarıları ayarlamak ve grafikler oluşturmak için bu verileri kullandığınızda hataları ve performans sorunlarını daha kolay bir şekilde araştırmanıza yardımcı olabilir.

Mantıksal uygulamanızın oluşturma ve dağıtım ayarları [Application Insights](../azure-monitor/app/app-insights-overview.md)kullanmayı destekliyorsa, mantıksal uygulamanız için isteğe bağlı olarak tanılama günlüğünü ve izlemeyi etkinleştirebilirsiniz. Mantıksal uygulamanızı Visual Studio Code veya dağıtımdan sonra dağıtırken bunu yapabilirsiniz. Bir Application Insights örneğiniz olması gerekir, ancak mantıksal uygulamanızı dağıtırken ya da dağıtımdan sonra bu kaynağı [önceden](../azure-monitor/app/create-workspace-resource.md)oluşturabilirsiniz.

Dağıtılan bir mantıksal uygulamada Application Insights etkinleştirmek veya zaten etkinleştirildiğinde Application Insights verileri gözden geçirmek için şu adımları izleyin:

1. Azure portal, dağıtılan mantıksal uygulamanızı bulun.

1. Mantıksal uygulama menüsünde, **Ayarlar** altında **Application Insights**' ı seçin.

1. Application Insights etkinleştirilmemişse, **Application Insights** bölmesinde **Application Insights aç**' ı seçin. Bölmedeki güncelleştirmelerden sonra, alt kısımdaki **Uygula**' yı seçin.

   Application Insights etkinse, **Application Insights** bölmesinde **Application Insights verileri görüntüle**' yi seçin.

Application Insights açıldıktan sonra mantıksal uygulamanız için çeşitli ölçümleri gözden geçirebilirsiniz. Daha fazla bilgi için şu konuları gözden geçirin:

* [Azure Logic Apps Application Insights-Bölüm 1 ile her yerden Izleme çalıştırma](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Application Insights Bölüm 2 ile her yerden Izleme Azure Logic Apps](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Docker 'a dağıt

[.Net CLI](/dotnet/core/tools/)kullanarak mantıksal uygulamanızı barındırma ortamı olarak bir [Docker kapsayıcısına](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) dağıtabilirsiniz. Bu komutlarla mantıksal uygulamanızın projesini derleyebilir ve yayımlayabilirsiniz. Daha sonra, mantıksal uygulamanızı dağıtmak için hedef olarak Docker kapsayıcınızı derleyip çalıştırabilirsiniz.

Docker hakkında bilgi sahibi değilseniz şu konuları gözden geçirin:

* [Docker nedir?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [Kapsayıcılar ve Docker’a Giriş](/dotnet/architecture/microservices/container-docker-introduction/)
* [.NET ve Docker’a Giriş](/dotnet/core/docker/introduction)
* [Docker kapsayıcıları, görüntüleri ve kayıt defterleri](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [Öğretici: Docker ile çalışmaya başlama (Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>Gereksinimler

* Mantıksal uygulamanızın dağıtım için kullandığı Azure depolama hesabı

* Docker kapsayıcınızı oluştururken kullandığınız iş akışı için bir Docker dosyası

  Örneğin, bu örnek Docker dosyası bir Logic App dağıtır ve mantıksal uygulamayı Azure portal yayımlamak için kullanılan Azure depolama hesabı için erişim anahtarını içeren bağlantı dizesini belirtir. Bu dizeyi bulmak için bkz. [depolama hesabı bağlantı dizesi al](#find-storage-account-connection-string). Daha fazla bilgi için [Docker dosyalarını yazmak Için en iyi yöntemleri](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)gözden geçirin.
  
  > [!IMPORTANT]
  > Üretim senaryolarında, örneğin bir anahtar kasasını kullanarak, bu gizli bilgileri ve gizli bilgileri koruyun ve güvenli hale getirin. Docker dosyaları için özel olarak, [BuildKit Ile derleme görüntülerini](https://docs.docker.com/develop/develop-images/build_enhancements/) gözden geçirin ve [Docker gizli dizileri Ile hassas verileri yönetin](https://docs.docker.com/engine/swarm/secrets/).

   ```text
   FROM mcr.microsoft.com/azure-functions/node:3.0

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY . /home/site/wwwroot

   RUN cd /home/site/wwwroot
   ```

<a name="find-storage-account-connection-string"></a>

### <a name="get-storage-account-connection-string"></a>Depolama hesabı bağlantı dizesi al

Docker kapsayıcı görüntünüzü derleyip çalıştırmadan önce, depolama hesabınıza erişim anahtarını içeren bağlantı dizesini almanız gerekir. Daha önce, bu depolama hesabını, uzantıyı macOS veya Linux 'ta kullanmak için ya da mantıksal uygulamanızı Azure portal dağıtırken oluşturdunuz.

Bu bağlantı dizesini bulmak ve kopyalamak için aşağıdaki adımları izleyin:

1. Azure portal, depolama hesabı menüsündeki **Ayarlar** altında **erişim anahtarları**' nı seçin. 

1. **Erişim tuşları** bölmesinde, depolama hesabının bağlantı dizesini bulun ve kopyalayın, bu örneğe benzer şekilde görünür:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Depolama hesabı erişim anahtarları ve bağlantı dizesinin kopyalandığı Azure portal gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Daha fazla bilgi için [depolama hesabı anahtarlarını yönetme](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)konusunu gözden geçirin.

1. Bu dizeyi dağıtım için kullandığınız Docker dosyasına ekleyebilmek için bağlantı dizesini güvenli bir yere kaydedin. 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>Depolama hesabı için ana anahtar bul

İş akışınız bir Istek tetikleyicisi içerdiğinde, Docker kapsayıcı görüntünüzü derleyip çalıştırdıktan sonra [tetikleyicinin geri çağırma URL 'sini almanız](#get-callback-url-request-trigger) gerekir. Bu görevde Ayrıca, dağıtım için kullandığınız depolama hesabı için ana anahtar değerini belirtmeniz gerekir.

1. Bu ana anahtarı bulmak için, projenizde **Azure-WebJobs-gizlilikler/{Deployment-Name}/host.js** dosyasını açın.

1. Özelliğini bulun `AzureWebJobsStorage` ve anahtar değerini bu bölümden kopyalayın:

   ```json
   {
      <...>
      "masterKey": {
         "name": "master",
         "value": "<master-key>",
         "encrypted": false
      },
      <...>
   }
   ```

1. Daha sonra kullanmak için bu anahtar değerini güvenli bir yere kaydedin.

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>Docker kapsayıcı görüntünüzü derleyin ve çalıştırın

1. Docker dosyanızı kullanarak Docker kapsayıcı görüntünüzü oluşturun ve şu komutu çalıştırın:

   `docker build --tag local/workflowcontainer .`

   Daha fazla bilgi için bkz. [Docker Build](https://docs.docker.com/engine/reference/commandline/build/).

1. Şu komutu kullanarak kapsayıcıyı yerel olarak çalıştırın:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Daha fazla bilgi için bkz. [Docker Run](https://docs.docker.com/engine/reference/commandline/run/).

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>Istek tetikleyicisi için geri çağırma URL 'SI al

Istek tetikleyicisini kullanan bir iş akışı için, bu isteği göndererek tetikleyicisinin geri çağırma URL 'sini alın:

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

`{trigger-name}`Değer, iş AKıŞıNıN JSON tanımında görüntülenen istek tetikleyicisinin adıdır. `{master-key}`Değer `AzureWebJobsStorage` , **Azure-WebJobs-gizlilikler/{Deployment-name}/host.js** dosyadaki özelliği için ayarladığınız Azure depolama hesabında tanımlanmıştır. Daha fazla bilgi için bkz. [depolama hesabı ana anahtarını bulma](#find-storage-account-master-key).

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Tasarımcıdan öğeleri silme

Tasarımcıdaki iş akışınızda bir öğeyi silmek için şu adımlardan herhangi birini izleyin:

* Öğeyi seçin, öğenin kısayol menüsünü açın (SHIFT + F10) ve **Sil**' i seçin. Onaylamak için **Tamam**’ı seçin.

* Öğeyi seçin ve DELETE tuşuna basın. Onaylamak için **Tamam**’ı seçin.

* Öğeyi, ayrıntılar bölmesinin bu öğe için açılmasını sağlayacak şekilde seçin. Bölmenin sağ üst köşesinde üç nokta (**...**) menüsünü açın ve **Sil**' i seçin. Onaylamak için **Tamam**’ı seçin.

  ![Açık Ayrıntılar bölmesiyle ve seçilen üç nokta düğmesi ve "Sil" komutu ile tasarımcıda seçili bir öğeyi gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Üç nokta menüsü görünmüyorsa, ayrıntılar bölmesinin sağ üst köşedeki üç nokta (**...**) düğmesini gösterdiği şekilde, Visual Studio Code pencere genelinde ' ı genişletin.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Hata ve sorunları giderme

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>Tasarımcı açılamıyor

Tasarımcıyı açmaya çalıştığınızda, **"Iş akışı tasarım zamanı başlatılamadı"** hatasını alırsınız. Daha önce tasarımcıyı açmayı deneyip projeyi sonlandırdıysanız veya sildiyseniz, Uzantı paketi doğru şekilde indirilemeyebilir. Bu nedenin sorun olup olmadığını denetlemek için şu adımları izleyin:

  1. Visual Studio Code, çıkış penceresini açın. **Görünüm** menüsünde **Çıkış**' ı seçin.

  1. Uzantı içindeki çıktıyı gözden geçirebilmeniz için çıkış penceresinin başlık çubuğundaki listeden **Azure Logic Apps (Önizleme)** öğesini seçin. Örneğin:

     !["Azure Logic Apps" seçiliyken çıkış penceresini gösteren ekran görüntüsü.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. Çıktıyı gözden geçirin ve bu hata iletisinin görüntülenip görüntülenmeyeceğini denetleyin:

     ```text
     A host error has occurred during startup operation '{operationID}'.
     System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
     ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.7\bin\
     DurableTask.AzureStorage.dll' already exists.
     Value cannot be null. (Parameter 'provider')
     Application is shutting down...
     Initialization cancellation requested by runtime.
     Stopping host...
     Host shutdown completed.
     ```

   Bu hatayı çözmek için, bu konumdaki **Extensiondemeti** klasörünü silin **. ..\Users \{ -UserName} \Appdata\local\temp\functions\extensionpaketlerinizi** açın ve tasarımcıda **workflow.js** dosyayı açmayı yeniden deneyin.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Yeni Tetikleyiciler ve eylemler daha önce oluşturulan iş akışlarının tasarımcı seçicisinde yok

Azure Logic Apps önizlemesi, Azure Işlev Işlemleri, sıvı Işlemleri ve XML **doğrulaması** ve **dönüştürme XML** gibi XML işlemlerine yönelik yerleşik eylemleri destekler. Ancak, daha önce oluşturulmuş Logic Apps için, Visual Studio Code uzantı paketinin güncel olmayan bir sürümünü kullandığını belirlemek için bu eylemler tasarımcı seçicisinde görünmeyebilir `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Ayrıca, mantıksal uygulamanızı oluştururken **Azure 'dan bağlayıcılar kullan** ' ı etkinleştirmediğiniz veya seçmediyseniz, **Azure işlev işlemleri** Bağlayıcısı ve eylemleri tasarımcı seçicisinde görünmez. Azure tarafından dağıtılan bağlayıcıları uygulama oluşturma zamanında etkinleştirmediyseniz, Visual Studio Code projenizden etkinleştirebilirsiniz. Kısayol menüsünde **workflow.js** açın ve **Azure 'dan bağlayıcıları kullan**' ı seçin.

Güncel olmayan paketi onarmak için, uzantı paketini otomatik olarak en son sürüme güncellemek Visual Studio Code süresi geçmiş paketi silmek için aşağıdaki adımları izleyin.

> [!NOTE]
> Bu çözüm, Azure portal kullanarak oluşturduğunuz mantıksal uygulamalardan değil, Azure Logic Apps (Önizleme) uzantısıyla Visual Studio Code kullanarak oluşturup dağıttığınız Logic Apps için geçerlidir. [Azure Portal tasarımcıda desteklenen Tetikleyiciler ve eylemler eksik](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions).

1. Kaybetmek istemediğiniz işleri kaydedin ve Visual Studio 'Yu kapatın.

1. Bilgisayarınızda, mevcut paket için sürümlenmiş klasörleri içeren aşağıdaki klasöre gidin:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Önceki paket için sürüm klasörünü silme Örneğin, sürüm 1.1.3 için bir klasörünüz varsa, bu klasörü silin.

1. Şimdi, gerekli NuGet paketi için sürümlenmiş klasörleri içeren aşağıdaki klasöre gidin:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Önceki paketin sürüm klasörünü silin, örneğin, sürüm 1.0.0.8-Önizleme için bir klasörünüz varsa, bu klasörü silin.

1. Visual Studio Code, projenizi ve **workflow.jsüzerindeki** dosyayı tasarımcıda yeniden açın.

Eksik Tetikleyiciler ve eylemler artık tasarımcıda görüntülenir.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>tetikleyici veya eylemde "400 Hatalı Istek" görüntülenir

Bir çalıştırma başarısız olduğunda ve çalışmayı izleme görünümünde inceleyecekseniz, bu hata daha uzun bir ada sahip bir tetikleyici veya eylemde görünebilir ve bu, temel alınan Tekdüzen Kaynak tanımlayıcısı 'nın (URI) varsayılan karakter sınırını aşmasına neden olur.

Bu sorunu gidermek ve daha uzun URI 'yi ayarlamak için `UrlSegmentMaxCount` `UrlSegmentMaxLength` aşağıdaki adımları izleyerek bilgisayarınızdaki ve kayıt defteri anahtarlarını düzenleyin. Bu anahtarın varsayılan değerleri, [ Windows Için kayıt defteri ayarlarıHttp.sys](/troubleshoot/iis/httpsys-registry-windows)bu konuda açıklanmaktadır.

> [!IMPORTANT]
> Başlamadan önce, çalışmanızı kaydettiğinizden emin olun. Bu çözüm, değişikliklerin etkili olabilmesi için işiniz bittiğinde bilgisayarınızı yeniden başlatmanızı gerektirir.

1. Bilgisayarınızda, **Çalıştır** penceresini açın ve `regedit` kayıt defteri düzenleyicisini açan komutunu çalıştırın.

1. Bilgisayarınızda yaptığınız değişikliklere izin vermek için **Kullanıcı hesabı denetim** kutusunda **Evet** ' i seçin.

1. Sol bölmedeki **bilgisayar** altında, yol üzerindeki düğümleri genişletin, **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters** ve ardından **Parametreler**' i seçin.

1. Sağ bölmede, `UrlSegmentMaxCount` ve `UrlSegmentMaxLength` kayıt defteri anahtarlarını bulun.

1. URI 'Lerin kullanmak istediğiniz adları barındırabilmesi için bu anahtar değerlerini yeterince artırın. Bu anahtarlar yoksa, aşağıdaki adımları izleyerek bunları **Parameters** klasörüne ekleyin:

   1. **Parametreler** kısayol menüsünde **Yeni**  >  **DWORD (32-bit) değeri**' ni seçin.

   1. Görüntülenen düzenleme kutusunda `UrlSegmentMaxCount` Yeni anahtar adı olarak girin.

   1. Yeni anahtarın kısayol menüsünü açın ve **Değiştir**' i seçin.

   1. Görüntülenen **Dize Düzenle** kutusunda, onaltılık veya ondalık biçimde istediğiniz **değer** anahtarı değerini girin. Örneğin, `400` onaltılı değerinde Decimal ile eşdeğerdir `1024` .

   1. `UrlSegmentMaxLength`Anahtar değerini eklemek için bu adımları tekrarlayın.

   Bu anahtar değerlerini artırdıktan veya ekledikten sonra, kayıt defteri Düzenleyicisi Şu örneğe benzer şekilde görünür:

   ![Kayıt defteri düzenleyicisini gösteren ekran görüntüsü.](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. Hazırsanız, değişikliklerin etkili olabilmesi için bilgisayarınızı yeniden başlatın.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>Hata ayıklama oturumu başlatılamadı

Bir hata ayıklama oturumu başlatmaya çalıştığınızda, **"preLaunchTask ' generateDebugSymbols ' ' çalıştırıldıktan sonra hata var**. Bu sorunu çözmek için, sembol oluşturmayı atlamak üzere projenizdeki dosyadaki **tasks.js** düzenleyin.

1. Projenizde, **. vscode** klasörünü genişletin ve dosyada **tasks.js** açın.

1. Aşağıdaki görevde satırı, `"dependsOn: "generateDebugSymbols"` önceki satırı sonlandıran virgülden birlikte silin, örneğin:

   Önce:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true,
      "dependsOn": "generateDebugSymbols"
    }
   ```

   Sonra:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true
    }
   ```

## <a name="next-steps"></a>Sonraki adımlar

Azure Logic Apps (Önizleme) uzantısıyla deneyimleriniz hakkında duymak istiyoruz!

* Hatalar veya sorunlar için [sorunlarınızı GitHub ' da oluşturun](https://github.com/Azure/logicapps/issues).
* Sorular, istekler, açıklamalar ve diğer geri bildirimler için [Bu geri bildirim formunu kullanın](https://aka.ms/lafeedback).
