---
title: Azure kaynaklarını yönetmek ve izlemek için otomasyon görevleri oluşturma
description: Azure Logic Apps çalışan iş akışları oluşturarak Azure kaynaklarını yönetmenize ve maliyetleri izlemenize yardımcı olan otomatikleştirilmiş görevleri ayarlayın.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/05/2021
ms.openlocfilehash: 0a98f9e4b108d2498fa19bc0b041f9d52272c7d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774945"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Otomasyon görevleri (Önizleme) oluşturarak Azure kaynaklarını yönetme ve maliyetleri izleme

> [!IMPORTANT]
> Bu özellik genel önizleme aşamasındadır, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure kaynaklarını](../azure-resource-manager/management/overview.md#terminology) daha kolay bir şekilde yönetmenize yardımcı olmak için, kaynak türüne bağlı olarak kullanılabilirlik açısından değişen Otomasyon görev şablonlarını kullanarak belirli bir kaynak veya kaynak grubu için otomatik yönetim görevleri oluşturabilirsiniz. Örneğin, bir [Azure depolama hesabı](../storage/common/storage-account-overview.md)için, size bu depolama hesabı için aylık maliyeti gönderen bir Otomasyon görevi ayarlayabilirsiniz. Bir [Azure sanal makinesi](https://azure.microsoft.com/services/virtual-machines/)için, bu sanal makineyi önceden tanımlanmış bir zamanlamaya göre etkinleştiren veya kapatan bir Otomasyon görevi oluşturabilirsiniz.

Arka planda, bir Otomasyon görevi aslında [Azure Logic Apps](../logic-apps/logic-apps-overview.md) hizmeti üzerinde çalışan ve aynı [fiyatlandırma fiyatları](https://azure.microsoft.com/pricing/details/logic-apps/) ve [fiyatlandırma modeli](../logic-apps/logic-apps-pricing.md)kullanılarak faturalandırılan bir iş akışıdır. Görevi oluşturduktan sonra, mantıksal uygulama Tasarımcısı ' nda görevi açarak temel alınan iş akışını görüntüleyebilir ve düzenleyebilirsiniz. Bir görev en az bir çalıştırmayı tamamladıktan sonra, her bir çalıştırmaya ilişkin durum, geçmiş, giriş ve çıkışları gözden geçirebilirsiniz.

Bu önizlemede Şu anda kullanılabilir olan görev şablonları aşağıda verilmiştir:

| Kaynak türü | Otomasyon görev şablonları |
|---------------|---------------------------|
| Azure kaynak grupları | **Kaynak silindiğinde** |
| Tüm Azure kaynakları | **Kaynak için aylık maliyet gönder** |
| Azure sanal makineleri | Ek olarak: <p>- **Sanal makineyi kapat** <br>- **Sanal makineyi Başlat** |
| Azure Depolama hesapları | Ek olarak: <p>- **Eski Blobları Sil** |
| Azure Cosmos DB | Ayrıca <p>- **Sorgu sonucunu e-postayla gönder** |
|||

Bu makalede, aşağıdaki görevlerin nasıl tamamlanacağı gösterilmektedir:

* Belirli bir Azure kaynağı için [bir Otomasyon görevi oluşturun](#create-automation-task) .

* Çalışma durumunu, girişleri, çıkışları ve diğer geçmiş bilgileri içeren [bir görevin geçmişini gözden geçirin](#review-task-history).

* Görevi güncelleştirebilmeniz için görevi [düzenleyin](#edit-task) veya mantıksal uygulama Tasarımcısı 'nda görevin temel alınan iş akışını özelleştirin.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>Otomasyon görevleri Azure Otomasyonu 'ndan farklı midir?

Şu anda yalnızca kaynak düzeyinde bir Otomasyon görevi oluşturabilir, görevin çalışma geçmişini görüntüleyebilir ve görevin temel alınan mantıksal uygulama iş akışını düzenleyerek [Azure Logic Apps](../logic-apps/logic-apps-overview.md) hizmeti tarafından desteklenir. Otomasyon görevleri, [Azure Otomasyonu](../automation/automation-intro.md)'ndan daha basit ve hafif.

Azure Otomasyonu, karşılaştırmaya göre Azure ve Azure dışı ortamlarınızda tutarlı Yönetimi destekleyen bulut tabanlı bir Otomasyon ve yapılandırma hizmetidir. Hizmet, [runbook 'lar](../automation/automation-runbook-execution.md), [değişiklik izleme ve stok](../automation/change-tracking/overview.md), güncelleştirme yönetimi, paylaşılan yetenekler ve heterojen özelliklerle [işlemleri yönetmek için işlem otomasyonu](../automation/automation-intro.md#process-automation) 'nu kapsar. Otomasyon, dağıtım, işlemler ve iş yüklerinin ve kaynakların yetkisini alma sırasında komple denetim sağlar.

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure hesabı ve aboneliği Aboneliğiniz yoksa, [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Yönetmek istediğiniz Azure kaynağı. Bu makale örnek olarak bir Azure depolama hesabı kullanır.

* Office 365 Outlook kullanarak size e-posta gönderen örnek ile birlikte izlemek istiyorsanız Office 365 hesabı.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Otomasyon görevi oluşturma

1. [Azure Portal](https://portal.azure.com), yönetmek istediğiniz kaynağı bulun.

1. Kaynak menüsünde, **Otomasyon** bölümüne kaydırın ve **Görevler** ' i seçin.

   !["Otomasyon" bölümünde "görevler" menü öğesinin seçili olduğu Azure portal ve bir depolama hesabı kaynak menüsünü gösteren ekran görüntüsü.](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. **Görevler** bölmesinde, bir görev şablonu seçebilmeniz için **Ekle** ' yi seçin.

   ![Araç çubuğunun "Ekle" seçiliyken depolama hesabı "görevler" bölmesini gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. **Görev Ekle** bölmesinde, **şablon seç**' in altında, oluşturmak istediğiniz görevin şablonunu seçin. Sonraki sayfa görünmezse, **İleri: kimlik doğrulaması**' nı seçin.

   Bu örnek, **kaynak için aylık maliyet gönder** görev şablonu seçilerek devam eder.

   !["Kaynak için aylık maliyet gönder" ve "Ileri: kimlik doğrulaması" seçimlerini gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. **Kimlik doğrulama**' nın altında, **Bağlantılar** bölümünde, tüm bağlantılarda kimlik doğrulama kimlik bilgilerini sağlayabilmeniz için görevde görüntülenen her bağlantı için **Oluştur** ' u seçin. Her görevdeki bağlantı türleri, göreve göre farklılık gösterir.

   Bu örnek, bu görev için gereken bağlantılardan yalnızca birini gösterir.

   ![Azure Resource Manager bağlantısı için seçili "Oluştur" seçeneğini gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. İstendiğinde, Azure hesabı kimlik bilgilerinizle oturum açın.

   ![Seçimi gösteren ekran görüntüsü, "oturum aç"](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Kimliği başarıyla doğrulanan her bağlantı şu örneğe benzer şekilde görünür:

   ![Başarıyla oluşturulan bağlantıyı gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. Tüm bağlantıların kimliklerini doğruladıktan sonra, ileri ' yi seçin. sonraki sayfada görünmüyorsa **yapılandırma** ' ya tıklayın.

1. **Yapılandırma** altında, görev için bir ad ve görev için gereken diğer bilgileri girin. İşiniz bittiğinde **Oluştur**'u seçin.

   > [!NOTE]
   > Oluşturulduktan sonra görev adını değiştiremezsiniz, bu nedenle [temel alınan iş akışını düzenlerseniz](#edit-task-workflow)hala geçerli olan bir ad düşünün. Temel alınan iş akışında yaptığınız değişiklikler, görev şablonunu değil, yalnızca sizin oluşturduğunuz göreve uygulanır.
   >
   > Örneğin, görevinizi adlandırın `Send monthly cost` , ancak daha sonra temel alınan iş akışını haftalık olarak çalışacak şekilde düzenlerseniz, görevin adını olarak değiştiremezsiniz `Send weekly cost` .

   E-posta bildirimleri gönderen görevler bir e-posta adresi gerektirir.

   ![Seçili görev için gerekli bilgileri gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   Otomatik olarak canlı ve çalışır durumdaki oluşturduğunuz görev, artık **otomasyon görevleri** listesinde görünür.

   ![Otomasyon görevleri listesini gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Görev hemen görünmezse, görev listesini yenilemeyi deneyin veya yenilemeden önce biraz bekleyin. Araç çubuğunda **Yenile**' yi seçin.

   Seçili görev çalıştıktan sonra şu örneğe benzer bir e-posta alırsınız:

   ![Görev tarafından gönderilen e-posta bildirimini gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Görev geçmişini gözden geçirme

Görevin çalışma geçmişini, durumları, girişleri, çıkışları ve diğer bilgilerle birlikte görüntülemek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com), gözden geçirmek istediğiniz görev geçmişinin bulunduğu kaynağı bulun.

1. Kaynağın menüsünde, **Ayarlar**' ın altında **otomasyon görevleri**' ni seçin.

1. Görevler listesinde, gözden geçirmek istediğiniz görevi bulun. Bu görevin **çalıştırmalar** sütununda **görüntüle**' yi seçin.

   ![Bir görevi ve seçili "Görünüm" seçeneğini gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   Çalıştırma **geçmişi** bölmesi, görev için tüm çalıştırmaları, durumları, başlangıç zamanları, tanımlayıcılar ve Çalıştırma süreleri ile birlikte gösterir.

   ![Görevin çalıştırmalarını, durumlarını ve diğer bilgilerini gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   İşte bir çalıştırma için olası durumlar:

   | Durum | Açıklama |
   |--------|-------------|
   | **Yürütüldükten** | Görev çalışırken iptal edildi. |
   | **Başarısız** | Görevde en az bir hatalı eylem var, ancak hatayı işlemek için sonraki eylem yok. |
   | **Çalışma** | Görev şu anda çalışıyor. |
   | **Başarılı** | Tüm eylemler başarılı oldu. Bir eylem başarısız olursa bir görev başarıyla bitirirebilir, ancak hatayı işlemek için sonraki bir eylem vardı. |
   | **Bekleme** | Çalıştırma henüz başlatılmadı ve görevin daha önceki bir örneği çalışmaya devam ettiğinden duraklatıldı. |
   |||

   Daha fazla bilgi için bkz. [çalıştırma geçmişini gözden geçirme](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. Çalışma içindeki her adımın durumlarını ve diğer bilgilerini görüntülemek için o çalıştırmayı seçin.

   **Mantıksal uygulama çalıştırma** bölmesi açılır ve çalıştıran temel alınan iş akışını gösterir.

   * Bir iş akışı her zaman bir [*tetikleyici*](../connectors/apis-list.md#triggers)ile başlar. Bu görev için, iş akışı [ **yinelenme** tetikleyicisiyle](../connectors/connectors-native-recurrence.md)başlar.

   * Her adım durumunu ve çalıştırma süresini gösterir. 0 saniyelik süreleri olan adımların çalıştırılması 1 saniyeden daha az sürdü.

   ![Çalıştırma, durum ve çalışma süresi içindeki her adımı gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Her adımın giriş ve çıkışlarını gözden geçirmek için, öğesini genişleterek adımını seçin.

   Bu örnekte, yalnızca iş akışının ne zaman çalıştığı ve sonraki eylemlerin işlemesi için çıkış sağladığı için çıkış olmayan yineleme tetikleyicisinin girişleri gösterilmektedir.

   ![Genişletilen tetikleyiciyi ve girişleri gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   Buna karşılık, **e-posta gönder** eyleminin iş akışındaki ve çıkışlarındaki önceki eylemlerden girişler vardır.

   ![Genişletilmiş bir eylem, giriş ve çıkışları gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Uygulamaları, verileri, hizmetleri ve sistemleri Azure kaynakları için Otomasyon görevlerinin bağlamından ayrı olarak tümleştirebilmeniz için kendi otomatikleştirilmiş iş akışlarınızı nasıl oluşturabileceğinizi öğrenmek için bkz. [hızlı başlangıç: Azure Logic Apps Azure Portal kullanarak ilk Tümleştirme iş akışınızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="edit-task"></a>

## <a name="edit-the-task"></a>Görevi Düzenle

Bir görevi değiştirmek için şu seçeneklere sahipsiniz:

* Görevin bağlantı bilgileri veya yapılandırma bilgileri gibi özelliklerini değiştirebilmek için ["satır içi" görevini düzenleyin](#edit-task-inline) (örneğin, e-posta adresiniz).

* [Görevin temel alınan iş akışını](#edit-task-workflow) mantıksal uygulama Tasarımcısı ' nda düzenleyin.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>Görevi satır içi Düzenle

1. [Azure Portal](https://portal.azure.com), güncelleştirmek istediğiniz görevin bulunduğu kaynağı bulun.

1. Kaynağın menüsünde, **Otomasyon** altında **Görevler**' i seçin.

1. Görevler listesinde, güncelleştirmek istediğiniz görevi bulun. Görevin üç nokta (**...**) menüsünü açın ve **satır içi Düzenle**' yi seçin.

   ![Açılan üç nokta menüsünü ve seçili "satırı Düzenle" seçeneğini gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   Varsayılan olarak, **kimlik doğrulama** sekmesi görünür ve var olan bağlantıları gösterir.

1. Yeni kimlik doğrulama kimlik bilgileri eklemek veya bir bağlantının farklı mevcut kimlik doğrulama kimlik bilgilerini seçmek için bağlantının üç nokta (**...**) menüsünü açın ve **Yeni bağlantı ekle** ya da varsa farklı kimlik doğrulama kimlik bilgileri ' ni seçin.

   ![Kimlik doğrulama sekmesini, var olan bağlantıları ve seçili üç nokta menüsünü gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. Diğer görev özelliklerini güncelleştirmek için **İleri: yapılandırma**' yı seçin.

   Bu örnekteki görev için, düzenleme için kullanılabilecek tek özellik e-posta adresidir.

   ![Yapılandırma sekmesini gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. İşiniz bittiğinde **Kaydet**'i seçin.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>Görevin temel aldığı iş akışını Düzenle

Otomasyon görevi için temel alınan iş akışını değiştirdiğinizde değişiklikleriniz yalnızca oluşturduğunuz görev örneğini etkiler, görevi oluşturan şablon değildir. Değişikliklerinizi yaptıktan ve kaydettikten sonra, özgün göreviniz için verdiğiniz ad artık görevi doğru şekilde açıklamayabilir, bu nedenle görevi farklı bir adla yeniden oluşturmanız gerekebilir.

> [!TIP]
> En iyi uygulama olarak, onun yerine kopyalanmış sürümü düzenleyebilmeniz için temel alınan iş akışını klonlayın. Bu şekilde, orijinal Otomasyon görevi çalışmaya devam ederken veya mevcut işlevselliği bozmadan çalışırken yaptığınız değişiklikleri kopyalama ve test etme işlemlerini yapabilirsiniz. Değişikliklerinizi tamamladıktan ve yeni sürümün başarıyla çalışması karşılandıktan sonra, orijinal Otomasyon görevini devre dışı bırakabilir veya silebilir ve otomasyon göreviniz için kopyalanmış sürümü kullanabilirsiniz. Aşağıdaki adımlarda, iş akışınızı kopyalama hakkında bilgiler yer alır.

1. [Azure Portal](https://portal.azure.com), güncelleştirmek istediğiniz görevin bulunduğu kaynağı bulun.

1. Kaynağın menüsünde, **Otomasyon** altında **Görevler**' i seçin.

1. Görevler listesinde, güncelleştirmek istediğiniz görevi bulun. Görevin üç nokta (**...**) menüsünü açın ve **Logic Apps aç**' ı seçin.

   ![Açılan üç nokta menüsünü ve seçili "Logic Apps aç" seçeneğini gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   Görevin temel aldığı iş akışı Azure Logic Apps hizmetinde açılır ve görev için kullanılabilen çalışma geçmişini görüntüleyebileceğiniz **genel bakış** bölmesini gösterir.

   ![Görevin genel bakış bölmesi seçiliyken Azure Logic Apps görünümünde görevi gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Mantıksal uygulama Tasarımcısı 'nda temel alınan iş akışını açmak için mantıksal uygulama menüsünde **mantıksal uygulama Tasarımcısı**' nı seçin.

   !["Logic App Designer" menü seçeneğinin seçili olduğunu ve temel alınan iş akışıyla tasarımcı yüzeyini gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   Artık iş akışının tetikleyicisi ve eylemlerinin özelliklerini düzenleyebilir, ayrıca iş akışının kendisini tanımlayan tetikleyiciyi ve eylemleri düzenleyebilirsiniz. Bununla birlikte, en iyi uygulama olarak, özgün iş akışı çalışmaya devam ederken ve çalışırken değişikliklerinizi bir kopyada yapabilmek için iş akışınızı kopyalamak için adımları izleyin.

1. İş akışınızı kopyalamak ve bunun yerine kopyalanmış sürümü düzenlemek için aşağıdaki adımları izleyin:

   1. Mantıksal uygulama iş akışı menüsünde **genel bakış**' ı seçin.

   1. Genel Bakış bölmesinin araç çubuğunda, **Kopyala**' yı seçin.

   1. Mantıksal uygulama oluşturma bölmesinde, **ad**' ın altında, kopyalanmış mantık uygulaması iş akışınız için yeni bir ad girin.

      **Mantıksal uygulama durumu** dışında, diğer özellikler düzenlenmek üzere kullanılamaz. 
      
   1. **Mantıksal uygulama durumu**' nun altında, kopyalanmış iş akışının değişikliklerinizi yaparken çalışması Için **devre dışı** ' yı seçin. Değişikliklerinizi test etmeye hazırsanız iş akışını etkinleştirebilirsiniz.

   1. Azure kopyalanmış iş akışınızı sağlamayı tamamladıktan sonra, mantıksal uygulama tasarımcısında bu iş akışını bulup açın.

1. Tetikleyicinin veya bir eylemin özelliklerini görüntülemek için, bu tetikleyiciyi veya eylemi genişletin.

   Örneğin, yinelenme tetikleyicisini ayda haftalık olarak çalışacak şekilde değiştirebilirsiniz.

   ![Kullanılabilir sıklık seçeneklerini göstermek için sıklık listesi açık olan genişletilmiş yinelenme tetikleyicisini gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   Yinelenme tetikleyicisi hakkında daha fazla bilgi için bkz. [yineleme tetikleyicisi ile yinelenen görevler ve iş akışları oluşturma, zamanlama ve çalıştırma](../connectors/connectors-native-recurrence.md). Kullanabileceğiniz diğer Tetikleyiciler ve eylemler hakkında daha fazla bilgi için bkz. [bağlayıcılar Azure Logic Apps](../connectors/apis-list.md).

1. Değişikliklerinizi kaydetmek için tasarımcı araç çubuğunda **Kaydet**' i seçin.

   ![Tasarımcı araç çubuğunu ve seçili "Kaydet" komutunu gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. Güncelleştirilmiş iş akışını test etmek ve çalıştırmak için, Tasarımcı araç çubuğunda **Çalıştır**' ı seçin.

   Çalıştırma bittikten sonra tasarımcı, iş akışının çalışma ayrıntılarını gösterir.

   ![Tasarımcıda iş akışının çalışma ayrıntılarını gösteren ekran görüntüsü](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Görevin çalışmaya devam edebilmesi için iş akışını devre dışı bırakmak için, bkz. [Azure Portal Logic Apps 'ı yönetme](../logic-apps/manage-logic-apps-with-azure-portal.md).

## <a name="provide-feedback"></a>Geribildirim gönderme

Görüşlerinizi öğrenmek istiyoruz! Hataları raporlamak, geri bildirim sağlamak veya bu önizleme özelliğiyle ilgili soru sormak için [Azure Logic Apps ekibine başvurun](mailto:logicappspm@microsoft.com).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure portal Logic Apps 'i yönetme](../logic-apps/manage-logic-apps-with-azure-portal.md)
