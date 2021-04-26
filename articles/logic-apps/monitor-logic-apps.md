---
title: Durumu izleme, geçmişi görüntüleme ve uyarıları ayarlama
description: Çalışma durumunu denetleyerek, tetikleyici geçmişini inceleyerek ve Azure Logic Apps uyarıları etkinleştirerek Logic Apps sorunlarını giderin
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 3c3d1930234c178a56227830ef0702450ddf4a8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580676"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Azure Logic Apps için çalışma durumunu izleme, tetikleyici geçmişini gözden geçirme ve uyarı ayarlama

[Bir mantıksal uygulama oluşturup çalıştırdıktan](../logic-apps/quickstart-create-first-logic-app-workflow.md)sonra, bu mantıksal uygulamanın çalışma durumunu, [geçmişi](#review-runs-history), [tetikleyici geçmişini](#review-trigger-history)ve performansı kontrol edebilirsiniz. Hataları veya olası diğer sorunlar hakkında bildirim almak için [uyarıları](#add-azure-alerts)ayarlayın. Örneğin, "Beş ' dan fazla çalıştırma başarısız olduğunda" bir uyarı oluşturabilirsiniz.

Gerçek zamanlı olay izleme ve daha zengin hata ayıklama için, [Azure izleyici günlüklerini](../azure-monitor/overview.md)kullanarak mantıksal uygulamanız için tanılama günlüğü ayarlayın. Bu Azure hizmeti, kullanılabilirliğini ve performansını daha kolay koruyabilmeniz için bulutunuzu ve şirket içi ortamlarınızı izlemenize yardımcı olur. Daha sonra olayları tetikleme, olayları çalıştırma ve eylem olayları gibi olayları bulabilir ve görüntüleyebilirsiniz. Bu bilgileri [Azure izleyici günlüklerinde](../azure-monitor/logs/data-platform-logs.md)depolayarak, bu bilgileri bulmanıza ve çözümlemenize yardımcı olan [günlük sorguları](../azure-monitor/logs/log-query-overview.md) oluşturabilirsiniz. Bu tanılama verilerini Azure depolama ve Azure Event Hubs gibi diğer Azure hizmetleriyle de kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure izleyici kullanarak mantıksal uygulamaları izleme](../logic-apps/monitor-logic-apps-log-analytics.md).

> [!NOTE]
> Logic Apps, bir [iç erişim uç noktası](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)kullanmak üzere oluşturulmuş bir [tümleştirme hizmeti ortamında (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) çalışıyorsa, Logic App 'in çalıştırma geçmişinden gelen giriş ve çıkışları *yalnızca sanal ağınızın* içinden görüntüleyebilir ve erişebilirsiniz. Özel uç noktalar ve çalıştırma geçmişine erişmek istediğiniz bilgisayar arasında ağ bağlantısına sahip olduğunuzdan emin olun. Örneğin, istemci bilgisayarınız ıSE 'nin sanal ağı içinde veya ıSE 'nin sanal ağına bağlı bir sanal ağ içinde (örneğin, eşleme veya bir sanal özel ağ) bulunabilir. Daha fazla bilgi için bkz. [Ise uç noktası erişimi](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Çalıştırma geçmişini gözden geçirme

Tetikleyici bir öğe veya olay için her tetiklendiğinde, Logic Apps altyapısı her öğe veya olay için ayrı bir iş akışı örneği oluşturur ve çalıştırır. Varsayılan olarak, her iş akışı örneği paralel olarak çalışarak bir iş akışının bir çalıştırmaya başlamadan önce beklemesi gerekmez. İş akışındaki her adımın durumu, her adımın giriş ve çıkışları dahil olmak üzere, çalışma sırasında ne olduğunu inceleyebilirsiniz.

1. [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı bulun ve açın.

   Mantıksal uygulamanızı bulmak için, ana Azure Arama kutusuna girin `logic apps` ve ardından **Logic Apps**' yi seçin.

   !["Logic Apps" hizmetini bul ve Seç](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure portal, Azure aboneliklerinizle ilişkili tüm mantıksal uygulamaları gösterir. Bu listeyi ad, abonelik, kaynak grubu, konum ve benzeri bir konuma göre filtreleyebilirsiniz.

   ![Aboneliklerle ilişkili Logic Apps 'i görüntüleme](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Mantıksal uygulamanızı seçin ve **genel bakış**' ı seçin.

   Genel Bakış bölmesinde, **çalışma geçmişi** altında, mantıksal uygulamanız için tüm geçmiş, geçerli ve bekleme çalıştırmaları görüntülenir. Listede birçok çalıştırma görünüyorsa ve istediğiniz girişi bulamıyorsanız listeyi filtrelemeyi deneyin.

   > [!TIP]
   > Çalıştırma durumu görünmezse, **Yenile**' yi seçerek genel bakış sayfasını yenilemeyi deneyin. Karşılanmayan ölçütler veya veri bulma nedeniyle atlanan bir tetikleyici için çalıştırma gerçekleşilmedi.

   ![Genel bakış, çalışma geçmişi ve diğer mantıksal uygulama bilgileri](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Olası çalıştırma durumları şunlardır:

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

1. Belirli bir çalıştırmaya ilişkin adımları ve diğer bilgileri gözden geçirmek için, çalışma **geçmişi** altında, o çalıştırmayı seçin.

   ![Gözden geçirmek için belirli bir çalıştırma seçin](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   **Mantıksal uygulama çalıştırma** bölmesi, seçilen çalıştırmada her bir adımı, her adımın çalışma durumunu ve her adımın çalışması için geçen süreyi gösterir, örneğin:

   ![Belirli bir çalıştırmada her eylem](./media/monitor-logic-apps/logic-app-run-pane.png)

   Bu bilgileri liste formunda görüntülemek için, **mantıksal uygulama çalıştırma** araç çubuğunda, **Çalıştır ayrıntıları**' nı seçin.

   ![Araç çubuğunda "ayrıntıları Çalıştır" ı seçin](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   Çalışma Ayrıntıları görünümü her adımı, durumlarını ve diğer bilgileri gösterir.

   ![Çalıştırmada her bir adımla ilgili ayrıntıları gözden geçirin](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Örneğin, [Logic Apps için REST API](/rest/api/logic)kullandığınızda ihtiyacınız olabilecek ÇALıŞTıRMANıN **bağıntı kimliği** özelliğini alabilirsiniz.

1. Belirli bir adım hakkında daha fazla bilgi edinmek için iki seçenekten birini belirleyin:

   * **Mantıksal uygulama çalıştırma** bölmesinde, şeklin genişedebilmesi için adımı seçin. Artık girişler, çıktılar ve bu adımda gerçekleşen hatalar gibi bilgileri görüntüleyebilirsiniz, örneğin:

     ![Mantıksal uygulama çalıştırma bölmesinde, başarısız adımı görüntüleyin](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * **Mantıksal uygulama çalıştırma ayrıntıları** bölmesinde istediğiniz adımı seçin.

     ![Çalışma ayrıntıları bölmesinde, başarısız adımı görüntüle](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     Artık bu adım için giriş ve çıkış gibi bilgileri görüntüleyebilirsiniz, örneğin:

   > [!NOTE]
   > Tüm çalışma zamanı ayrıntıları ve olayları Logic Apps hizmeti içinde şifrelenir. Yalnızca bir Kullanıcı bu verileri görüntülemek istediğinde şifresi çözülür. [Azure rol tabanlı erişim denetimi 'ni (Azure RBAC)](../role-based-access-control/overview.md)kullanarak, [çalıştırma geçmişinde girişleri ve çıkışları gizleyebilir](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) veya bu bilgilere Kullanıcı erişimini denetleyebilirsiniz.

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Tetikleyici geçmişini gözden geçirme

Her mantıksal uygulama çalıştırması bir tetikleyici ile başlar. Tetikleyici geçmişi, mantıksal uygulamanızın yaptığı tüm tetikleyici girişimlerini ve her tetikleyici girişimine ait girişler ve çıktılar hakkında bilgi listeler.

1. [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı bulun ve açın.

   Mantıksal uygulamanızı bulmak için, ana Azure Arama kutusuna girin `logic apps` ve ardından **Logic Apps**' yi seçin.

   !["Logic Apps" hizmetini bul ve Seç](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure portal, Azure aboneliklerinizle ilişkili tüm mantıksal uygulamaları gösterir. Bu listeyi ad, abonelik, kaynak grubu, konum ve benzeri bir konuma göre filtreleyebilirsiniz.

   ![Aboneliklerle ilişkili Logic Apps 'i görüntüleme](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Mantıksal uygulamanızı seçin ve **genel bakış**' ı seçin.

1. Mantıksal uygulamanızın menüsünde **genel bakış**' ı seçin. **Özet** bölümünde, **değerlendirme** altında, **tetikleyici geçmişini göster**' i seçin.

   ![Mantıksal uygulamanız için tetikleyici geçmişini görüntüleme](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   Tetikleyici geçmişi bölmesi, mantıksal uygulamanızın yaptığı tüm tetikleme girişimlerini gösterir. Tetikleyici bir öğe veya olay için her tetiklendiğinde Logic Apps altyapısı, iş akışını çalıştıran ayrı bir mantıksal uygulama örneği oluşturur. Varsayılan olarak, her örnek paralel olarak çalışır, böylece bir çalıştırması başlatmadan önce herhangi bir iş akışının beklemesi gerekmez. Bu nedenle, mantıksal uygulamanız aynı anda birden çok öğede tetiklerse, her öğe için aynı tarih ve saate sahip bir tetikleyici girişi görünür.

   ![Farklı öğeler için birden çok tetikleyici denemesi](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Olası tetikleyici deneme durumları şunlardır:

   | Tetikleyici durumu | Description |
   |----------------|-------------|
   | **Başarısız** | Bir hata oluşmuştur. Başarısız bir tetikleyici için oluşturulan hata iletilerini gözden geçirmek için, bu tetikleyici denemesini seçin ve **çıktılar**' i seçin. Örneğin, geçerli olmayan girişler bulabilirsiniz. |
   | **Atlandı** | Tetikleyici bitiş noktasını denetledi ancak belirtilen ölçütlere uyan hiçbir veri bulamadı. |
   | **Başarılı** | Tetikleyici bitiş noktasını denetledi ve kullanılabilir verileri buldu. Genellikle, bu durum yanında **tetiklenen** bir durum da görünür. Aksi takdirde, tetikleyici tanımında karşılanmayan bir koşul veya `SplitOn` komut olabilir. <p><p>Bu durum el ile tetikleyici, yineleme tetikleyicisi veya yoklama tetikleyicisi için uygulanabilir. Bir tetikleyici başarıyla çalıştırılabilir, ancak eylemler işlenmeyen hatalar üretmediğinde, çalıştırma yine de başarısız olabilir. |
   |||

   > [!TIP]
   > Bir sonraki tekrarmayı beklemeden Tetikleyiciyi yeniden denetleyebilirsiniz. Genel Bakış araç çubuğunda **tetikleyiciyi Çalıştır**' ı seçin ve sonra da bir denetimi zorlayan tetikleyiciyi seçin. Ya da Logic Apps Tasarımcı araç çubuğunda **Çalıştır** ' ı seçin.

1. Belirli bir tetikleyici girişimi hakkında bilgileri görüntülemek için, tetikleyici bölmesinde, bu tetikleyici olayını seçin. Listede çok sayıda tetikleme denemesi görünüyorsa ve istediğiniz girişi bulamazsanız, listeyi filtrelemeyi deneyin. Beklediğiniz verileri bulamazsanız, araç çubuğunda **Yenile** ' yi seçmeyi deneyin.

   ![Belirli tetikleyici denemesini görüntüleme](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   Artık seçili tetikleyici olayı hakkındaki bilgileri gözden geçirebilirsiniz, örneğin:

   ![Belirli tetikleyici bilgilerini görüntüleme](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>İzleme uyarılarını ayarlama

Belirli ölçümleri temel alan veya mantıksal uygulamanızın eşiklerini aşan uyarıları almak için [Azure izleyici 'de uyarıları](../azure-monitor/alerts/alerts-overview.md)ayarlayın. [Azure 'da ölçümler](../azure-monitor/data-platform.md)hakkında bilgi edinin. [Azure izleyici](../azure-monitor/logs/log-query-overview.md)'yi kullanmadan uyarıları ayarlamak için aşağıdaki adımları izleyin.

1. Mantıksal uygulama menünüzde **izleme** altında **Uyarılar**  >  **Yeni uyarı kuralı**' nı seçin.

   ![Mantıksal uygulamanız için bir uyarı ekleyin](./media/monitor-logic-apps/add-new-alert-rule.png)

1. **Kural oluştur** bölmesindeki **kaynak** altında, henüz seçili değilse mantıksal uygulamanızı seçin. **Koşul**' ın altında, uyarıyı tetikleyen koşulu tanımlayabilmeniz için **Ekle** ' yi seçin.

   ![Kural için bir koşul ekleyin](./media/monitor-logic-apps/add-condition-for-rule.png)

1. **Sinyal mantığını Yapılandır** bölmesinde, uyarı almak istediğiniz sinyali bulun ve seçin. Arama kutusunu kullanabilir veya sinyalleri alfabetik olarak sıralamak için **sinyal adı** sütun başlığını seçin.

   Örneğin, bir tetikleyici başarısız olduğunda bir uyarı göndermek istiyorsanız aşağıdaki adımları izleyin:

   1. **Sinyal adı** sütununda, **başarısız Tetikleyiciler** sinyalini bulun ve seçin.

      ![Uyarı oluşturmak için sinyal seçin](./media/monitor-logic-apps/find-and-select-signal.png)

   1. Seçilen sinyal için açılan bilgi bölmesinde, **Uyarı mantığı** altında, koşulınızı ayarlayın, örneğin:

   1. **İşleci** için **büyüktür veya eşittir**' i seçin.

   1. **Toplama türü** Için, **sayım**' ı seçin.

   1. **Eşik değeri** için girin `1` .

   1. **Koşul önizlemesi** altında, koşullarınızın doğru göründüğünü onaylayın.

   1. **Temelinde değerlendirilen** altında, uyarı kuralını çalıştırmaya yönelik aralığı ve sıklığı ayarlayın. **Toplama ayrıntı düzeyi (süre)** için verileri gruplandırmak için dönemi seçin. **Değerlendirme sıklığı** için, koşulu ne sıklıkta denetlemek istediğinizi seçin.

   1. Hazırsanız, **bitti**' yi seçin.

   İşte bu durum:

   ![Uyarı için koşul ayarlama](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   **Kural oluştur** sayfası artık oluşturduğunuz koşulu ve bu uyarının çalıştırılması maliyetini gösterir.

   !["Kural oluşturma" sayfasında yeni uyarı](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Uyarınız için bir ad, isteğe bağlı açıklama ve önem düzeyi belirtin. **Oluşturma sırasında kuralı etkinleştir** ayarını açık bırakın ya da kuralı etkinleştirmeye hazırsanız devre dışı bırakın.

1. İşiniz bittiğinde **Uyarı kuralı oluştur**' u seçin.

> [!TIP]
> Bir uyarıdan mantıksal uygulama çalıştırmak için, bu örnekler gibi görevler gerçekleştirmenize olanak sağlayan [istek tetikleyicisini](../connectors/connectors-native-reqres.md) iş akışınıza dahil edebilirsiniz:
> 
> * [Bolluk 'e gönder](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Kısa mesaj gönderme](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Kuyruğa ileti ekleme](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Izleyici 'yi kullanarak mantıksal uygulamaları izleme](../logic-apps/monitor-logic-apps-log-analytics.md)
