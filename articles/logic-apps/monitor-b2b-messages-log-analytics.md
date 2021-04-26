---
title: Azure Izleyici 'yi kullanarak B2B iletilerini izleme
description: Azure Izleyici günlüklerini ayarlayıp Azure Logic Apps için tanılama verileri toplayarak AS2, x12 ve EDIOLGU iletilerinin sorunlarını giderin
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 765c15897bd5d435503d3bef07e76a93b148971c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596866"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Azure Logic Apps'te Azure İzleyici günlüklerini ayarlama ve B2B iletileri için tanılama verilerini toplama

Tümleştirme hesabınızdaki ticari iş ortakları arasında B2B iletişimini ayarladıktan sonra, bu iş ortakları AS2, x12 ve EDIOLGU gibi protokolleri kullanarak iletiler değiş tokuş edebilir. Bu iletişimin istediğiniz şekilde çalıştığından emin olmak için, tümleştirme hesabınız için [Azure izleyici günlüklerini](../azure-monitor/logs/data-platform-logs.md) ayarlayabilirsiniz. [Azure izleyici](../azure-monitor/overview.md) , kullanılabilirliğini ve performansını daha kolay koruyabilmeniz için bulutunuzu ve şirket içi ortamlarınızı izlemenize yardımcı olur. Azure Izleyici günlüklerini kullanarak, bir [Log Analytics çalışma alanında](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)olayları tetikleme, çalıştırma olayları ve eylem olayları gibi çalışma zamanı verileri ve olayları hakkında verileri kaydedebilir ve kaydedebilirsiniz. İletiler için günlüğe kaydetme, şu gibi bilgiler de toplar:

* İleti sayısı ve durumu
* Durum bildirimleri
* Mesajlar ve bildirimler arasındaki bağıntılar
* Hatalara ilişkin ayrıntılı hata açıklamaları

Azure Izleyici, bu bilgileri bulmanıza ve izlemenize yardımcı olması için [günlük sorguları](../azure-monitor/logs/log-query-overview.md) oluşturmanızı sağlar. Bu tanılama verilerini Azure depolama ve Azure Event Hubs gibi [diğer Azure hizmetleriyle de kullanabilirsiniz](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data).

Tümleştirme hesabınız için günlük kaydı ayarlamak için Azure portal [Logic Apps B2B çözümünü kurun](#install-b2b-solution) . Bu çözüm, B2B ileti olayları için toplu bilgiler sağlar. Daha sonra, bu bilgiler için günlüğe kaydetmeyi etkinleştirmek ve sorgular oluşturmak için [Azure izleyici günlüklerini](#set-up-resource-logs)ayarlayın.

Bu makalede, tümleştirme hesabınız için Azure Izleyici günlüğe kaydetmenin nasıl etkinleştirileceği gösterilmektedir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Önkoşullar

* Log Analytics çalışma alanı. Log Analytics çalışma alanınız yoksa, [Log Analytics çalışma alanı oluşturmayı](../azure-monitor/logs/quick-create-workspace.md)öğrenin.

* Azure Izleyici günlüğe kaydetme ile ayarlanan ve bu bilgileri bir Log Analytics çalışma alanına Gönderen bir mantıksal uygulama. [Mantıksal uygulamanız Için Azure izleyici günlüklerini ayarlamayı](../logic-apps/monitor-logic-apps.md)öğrenin.

* Mantıksal uygulamanıza bağlı bir tümleştirme hesabı. [Tümleştirme hesabınızı mantıksal uygulamanıza bağlamayı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)öğrenin.

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Logic Apps B2B çözümü 'ni yükler

Azure Izleyici günlükleri mantıksal uygulamanız için B2B iletilerini izleyebilmeniz için, **Logic Apps B2B** çözümünü Log Analytics çalışma alanınıza ekleyin.

1. [Azure Portal](https://portal.azure.com)arama kutusuna girin `log analytics workspaces` ve sonra **Log Analytics çalışma alanları**' nı seçin.

   !["Log Analytics çalışma alanları" i seçin](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. **Log Analytics çalışma alanları** altında, çalışma alanınızı seçin.

   ![Log Analytics çalışma alanınızı seçin](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. Genel Bakış bölmesinde,   >  **izleme çözümlerini yapılandırma** Log Analytics kullanmaya başlama altında, **çözümleri görüntüle**' yi seçin.

   ![Genel Bakış bölmesinde "çözümleri görüntüle" seçeneğini belirleyin.](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. Genel Bakış bölmesinde **Ekle**' yi seçin.

   ![Genel Bakış bölmesinde yeni çözüm Ekle](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. **Market** açıldıktan sonra arama kutusuna girin `logic apps b2b` ve **Logic Apps B2B**' ı seçin.

   ![Market 'ten "Logic Apps yönetimi" ni seçin.](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. Çözüm açıklaması bölmesinde **Oluştur**' u seçin.

   !["Logic Apps B2B" çözümünü eklemek için "Oluştur" u seçin](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Çözümü yüklemek istediğiniz Log Analytics çalışma alanını gözden geçirip onaylayın ve yeniden **Oluştur** ' u seçin.

   !["Logic Apps B2B" için "Oluştur" u seçin](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Azure, çözümü Log Analytics çalışma alanınızı içeren Azure Kaynak grubuna dağıtduktan sonra, çözüm çalışma alanınızın Özet bölmesinde görünür. B2B iletileri işlendiğinde, bu bölmedeki ileti sayısı güncellenir.

   ![Çalışma alanı Özet bölmesi](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Azure Izleyici günlüklerini ayarlama

Azure Izleyici günlüğünü doğrudan tümleştirme hesabınızdan etkinleştirebilirsiniz.

1. [Azure Portal](https://portal.azure.com)tümleştirme hesabınızı bulun ve seçin.

   ![Tümleştirme hesabınızı bulun ve seçin](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. Tümleştirme hesabınızın menüsünde, **izleme** altında **Tanılama ayarları**' nı seçin. **Tanılama ayarı ekle**’yi seçin.

   !["Izleme" altında "Tanılama ayarları" nı seçin.](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. Ayarı oluşturmak için aşağıdaki adımları izleyin:

   1. Ayar için bir ad girin.

   1. **Log Analytics gönder**' i seçin.

   1. **Abonelik** için, Log Analytics çalışma alanınız Ile ilişkili Azure aboneliğini seçin.

   1. **Log Analytics çalışma alanı** için, kullanmak istediğiniz çalışma alanını seçin.

   1. **Günlük** altında, kaydetmek istediğiniz olay kategorisini belirten **ıntegrationaccounttrackingevents** kategorisini seçin.

   1. İşiniz bittiğinde **Kaydet**'i seçin.

   Örnek: 

   ![Tanılama verilerini toplamak için Azure Izleyici günlüklerini ayarlama](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>İleti durumunu görüntüle

Mantıksal uygulamanız çalıştıktan sonra, Log Analytics çalışma alanınızda bu iletilerle ilgili durumu ve verileri görüntüleyebilirsiniz.

1. [Azure Portal](https://portal.azure.com) arama kutusunda, Log Analytics çalışma alanınızı bulun ve açın.

1. Çalışma alanınızın menüsünde, **çalışma alanı Özeti**  >  **Logic Apps B2B** seçin.

   ![Çalışma alanı Özet bölmesi](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Logic Apps B2B kutucuğu bir çalıştırmadan sonra sonuçları hemen göstermezse, yeniden denemeden önce **Yenile** ' yi seçmeyi veya kısa bir süre beklemeyi deneyin.

   Varsayılan olarak **Logic Apps B2B** kutucuğu, verileri tek bir güne göre gösterir. Veri kapsamını farklı bir aralığa dönüştürmek için sayfanın üst kısmındaki kapsam denetimini seçin:

   ![Aralığı Değiştir](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. İleti durumu panosu görüntülendikten sonra, verileri tek bir güne göre gösteren belirli bir ileti türü için daha fazla ayrıntı görüntüleyebilirsiniz. **AS2**, **x12** veya **ediolgu** için kutucuğu seçin.

   ![İleti durumlarını görüntüleme](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   Seçtiğiniz kutucukta bir ileti listesi görüntülenir. Örneğin, AS2 ileti listesi şöyle görünebilir:

   ![AS2 iletileri için durumlar ve Ayrıntılar](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Her ileti türü için özellikler hakkında daha fazla bilgi edinmek için şu ileti özelliği açıklamalarını inceleyin:

   * [AS2 ileti özellikleri](#as2-message-properties)
   * [X12 ileti özellikleri](#x12-message-properties)
   * [EDIOLGU iletisi özellikleri](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../azure-monitor/logs/log-query-overview.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>AS2, x12 ve EDIOLGU iletileri için özellik açıklamaları ve ad biçimleri

Her ileti türü için, indirilen ileti dosyaları için özellik açıklamaları ve ad biçimleri aşağıda verilmiştir.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2 ileti özelliği açıklamaları

Her AS2 iletisi için özellik açıklamaları aşağıda verilmiştir.

| Özellik | Açıklama |
|----------|-------------|
| **Gönderen** | **Alma ayarlarında** belirtilen Konuk iş ortağı veya bir AS2 sözleşmesi Için **gönderme ayarları** 'nda belirtilen ana bilgisayar ortağı |
| **Alıcı** | **Alma ayarlarında** belirtilen ana bilgisayar ortağı veya bir AS2 sözleşmesi Için **gönderme ayarları** 'nda belirtilen Konuk iş ortağı |
| **Mantıksal Uygulama** | AS2 eylemlerinin ayarlandığı mantıksal uygulama |
| **Durum** | AS2 ileti durumu <br>Success = geçerli bir AS2 iletisi alındı veya gönderildi. Hiçbir MDN ayarlanmadı. <br>Success = geçerli bir AS2 iletisi alındı veya gönderildi. MDN ayarlanır ve alınır ya da MDN gönderilir. <br>Failed = geçersiz bir AS2 iletisi alındı. Hiçbir MDN ayarlanmadı. <br>Bekliyor = geçerli bir AS2 iletisi alındı veya gönderildi. MDN ayarlanmış ve MDN bekleniyor. |
| **ONAY** | MDN ileti durumu <br>Kabul edilen = pozitif MDN alındı veya gönderildi. <br>Bekliyor = bir MDN alınması veya gönderilmesi bekleniyor. <br>Reddedildi = negatif MDN alındı veya gönderildi. <br>Gerekli değildir = MDN sözleşmede ayarlanmadı. |
| **Yön** | AS2 ileti yönü |
| **İzleme KIMLIĞI** | Bir mantıksal uygulamadaki tüm Tetikleyicileri ve eylemleri karşılıklı yapan KIMLIK |
| **İleti Kimliği** | AS2 ileti başlıklarındaki AS2 ileti KIMLIĞI |
| **İlişkin** | AS2 eyleminin iletiyi işleme zamanı |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 ileti özelliği açıklamaları

Her x12 iletisi için özellik açıklamaları aşağıda verilmiştir.

| Özellik | Açıklama |
|----------|-------------|
| **Gönderen** | **Alma ayarlarında** belirtilen Konuk iş ortağı veya bir x12 sözleşmesi Için **gönderme ayarları** 'nda belirtilen ana bilgisayar ortağı |
| **Alıcı** | **Alma ayarlarında** belirtilen ana bilgisayar ortağı veya bir x12 sözleşmesi Için **gönderme ayarları** 'nda belirtilen Konuk iş ortağı |
| **Mantıksal Uygulama** | X12 eylemlerinin ayarlandığı mantıksal uygulama |
| **Durum** | X12 ileti durumu <br>Success = geçerli bir x12 iletisi alındı veya gönderildi. Ayarlanmış işlevsel ACK yok. <br>Success = geçerli bir x12 iletisi alındı veya gönderildi. İşlev ACK ayarlanır ve alınır ya da işlevsel bir ACK gönderilir. <br>Başarısız = geçersiz bir x12 iletisi alındı veya gönderildi. <br>Bekliyor = geçerli bir x12 iletisi alındı veya gönderildi. İşlevsel ack ayarlanır ve işlevsel bir ACK beklenmektedir. |
| **ONAY** | İşlev ACK (997) durumu <br>Kabul edildi = pozitif bir işlevsel ACK alındı veya gönderildi. <br>Reddedildi = negatif bir işlevsel ACK alındı veya gönderildi. <br>Bekliyor = işlevsel bir ACK bekleniyor ancak alınmadı. <br>Bekliyor = bir işlev ACK oluşturuldu ancak iş ortağına gönderilemiyor. <br>Gerekli değil = Işlev ACK ayarlanmadı. |
| **Yön** | X12 ileti yönü |
| **İzleme KIMLIĞI** | Bir mantıksal uygulamadaki tüm Tetikleyicileri ve eylemleri karşılıklı yapan KIMLIK |
| **İleti türü** | EDI x12 ileti türü |
| **ICN** | X12 iletisi için değişim denetim numarası |
| **TSCN** | X12 iletisi için Işlem kümesi denetim numarası |
| **İlişkin** | X12 eyleminin iletiyi işleme zamanı |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIOLGU iletisi özelliği açıklamaları

Her EDIOLGU iletisi için özellik açıklamaları aşağıda verilmiştir.

| Özellik | Açıklama |
|----------|-------------|
| **Gönderen** | **Alma ayarlarında** belirtilen Konuk iş ortağı veya BIR edıolgu sözleşmesi Için **gönderme ayarları** 'nda belirtilen ana bilgisayar ortağı |
| **Alıcı** | **Alma ayarlarında** belirtilen ana bilgisayar ortağı veya BIR edıolgu sözleşmesi Için **gönderme ayarları** 'nda belirtilen Konuk iş ortağı |
| **Mantıksal Uygulama** | EDIOLGU eylemlerinin ayarlandığı mantıksal uygulama |
| **Durum** | EDIOLGU iletisi durumu <br>Success = geçerli bir EDIOLGU iletisi alındı veya gönderildi. Ayarlanmış işlevsel ACK yok. <br>Success = geçerli bir EDIOLGU iletisi alındı veya gönderildi. İşlev ACK ayarlanır ve alınır ya da işlevsel bir ACK gönderilir. <br>Başarısız = geçersiz bir EDıOLGU iletisi alındı veya gönderildi <br>Bekliyor = geçerli bir EDIOLGU iletisi alındı veya gönderildi. İşlevsel ack ayarlanır ve işlevsel bir ACK beklenmektedir. |
| **ONAY** | İşlev ACK (conı) durumu <br>Kabul edildi = pozitif bir işlevsel ACK alındı veya gönderildi. <br>Reddedildi = negatif bir işlevsel ACK alındı veya gönderildi. <br>Bekliyor = işlevsel bir ACK bekleniyor ancak alınmadı. <br>Bekliyor = bir işlev ACK oluşturuldu ancak iş ortağına gönderilemiyor. <br>Gerekli değil = Işlev ACK ayarlanmadı. |
| **Yön** | EDIOLGU iletisi yönü |
| **İzleme KIMLIĞI** | Bir mantıksal uygulamadaki tüm Tetikleyicileri ve eylemleri karşılıklı yapan KIMLIK |
| **İleti türü** | EDIOLGU ileti türü |
| **ICN** | EDIOLGU iletisi için değişim denetim numarası |
| **TSCN** | EDIOLGU iletisi için Işlem kümesi denetim numarası |
| **İlişkin** | EDIOLGU eyleminin iletiyi işleme zamanı |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>Sonraki adımlar

* [İzleyici ve izleme sorguları oluşturma](../logic-apps/create-monitoring-tracking-queries.md)
