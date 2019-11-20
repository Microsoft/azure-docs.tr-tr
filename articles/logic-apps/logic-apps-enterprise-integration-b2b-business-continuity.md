---
title: Tümleştirme hesapları için olağanüstü durum kurtarma-Azure Logic Apps
description: Azure Logic Apps tümleştirme hesapları için çapraz bölge olağanüstü durum kurtarmayı ayarlama
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 321bfb673bab748176d75db7bcf21d76ddf0c819
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680409"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Azure Logic Apps tümleştirme hesapları için çapraz bölge olağanüstü durum kurtarmayı ayarlama

B2B iş yükleri, siparişler ve faturalar gibi para işlemleri içerir. Bir olağanüstü durum olayı sırasında iş ortakları ile ilgili kabul edilen iş düzeyi SLA 'Ları karşılamak için bir işletmenin hızla kurtarılması önemlidir. Bu makalede, B2B iş yükleri için iş sürekliliği planının nasıl oluşturulacağı gösterilmektedir. 

* Olağanüstü durum kurtarma hazırlığı 
* Olağanüstü durum olayı sırasında ikincil bölgeye yük devretmek 
* Bir olağanüstü durum olayından sonra birincil bölgeye geri dön

## <a name="disaster-recovery-readiness"></a>Olağanüstü durum kurtarma hazırlığı  

1. İkincil bölgeyi belirleyip, ikincil bölgede bir [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) oluşturun.

2. Çalışma durumunun ikincil bölge tümleştirme hesabına çoğaltılması gereken gerekli ileti akışları için iş ortakları, şemalar ve anlaşmalar ekleyin.

   > [!TIP]
   > Tümleştirme hesabı yapıtının adlandırma kuralı ' nda bölgeler arasında tutarlılık bulunduğundan emin olun. 

3. Birincil bölgeden çalışma durumunu çekmek için, ikincil bölgede bir mantıksal uygulama oluşturun. 

   Bu mantıksal uygulama bir *tetikleyici* ve bir *eyleme*sahip olmalıdır. 
   Tetikleyici birincil bölge tümleştirme hesabına bağlanmalıdır ve eylem ikincil bölge tümleştirme hesabına bağlanmalıdır. 
   Tetikleyici, zaman aralığına göre birincil bölge çalışma durumu tablosunu yoklar ve varsa yeni kayıtları çeker. Eylem bunları ikincil bölge tümleştirme hesabına güncelleştirir. 
   Bu, birincil bölgeden ikincil bölgeye artımlı çalışma zamanı durumu almaya yardımcı olur.

4. Logic Apps tümleştirme hesabındaki iş sürekliliği, B2B protokollerine göre destek için tasarlanmıştır-x12, AS2 ve EDIOLGU. Ayrıntılı adımları bulmak için ilgili bağlantıları seçin.

5. Bu öneri, tüm birincil bölge kaynaklarını ikincil bir bölgede da dağıtmaktır. 

   Birincil bölge kaynakları, Azure SQL veritabanı veya Azure Cosmos DB, Azure Service Bus ve Event Hubs mesajlaşma, Azure API Management ve Azure Logic Apps özelliği için kullanılan Azure içerir.   

6. Birincil bölgeden ikincil bölgeye bağlantı kurun. Birincil bir bölgeden çalışma durumunu çekmek için, ikincil bir bölgede bir mantıksal uygulama oluşturun. 

   Mantıksal uygulamanın bir tetikleyicisi ve bir eylemi olmalıdır. 
   Tetikleyici bir birincil bölge tümleştirme hesabına bağlanmalıdır. 
   Eylem bir ikincil bölge tümleştirme hesabına bağlanmalıdır. 
   Tetikleyici, zaman aralığına göre birincil bölge çalışma durumu tablosunu yoklar ve varsa yeni kayıtları çeker. 
   Eylem bunları bir ikincil bölge tümleştirme hesabına güncelleştirir. 
   Bu işlem, birincil bölgeden ikincil bölgeye artımlı çalışma zamanı durumu almaya yardımcı olur.

Logic Apps tümleştirme hesabındaki iş sürekliliği, B2B protokollerini temel alan destek sağlar x12, AS2 ve EDIOLGU. X12 ve AS2 kullanma hakkında ayrıntılı adımlar için bu makaledeki [x12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) ve [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) bölümüne bakın.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Olağanüstü durum olayı sırasında ikincil bölgeye yük devretme

Bir olağanüstü durum olayı sırasında, birincil bölge iş sürekliliği için kullanılamadığı zaman, ikincil bölgeye doğrudan trafik. İkincil bir bölge, bir işletmenin iş ortakları tarafından kabul edilen RPO/RTO karşılamak için işlevleri hızla kurtarmasına yardımcı olur. Ayrıca, bir bölgeden başka bir bölgeye yük devretme çabaları da en aza indirir. 

Birincil bir bölgeden ikincil bölgeye denetim numaraları kopyalanırken beklenen bir gecikme süresi var. Bir olağanüstü durum olayı sırasında iş ortaklarına yinelenen üretilmiş denetim numaraları gönderilmesini önlemek için, [PowerShell cmdlet 'lerini](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0)kullanarak ikincil bölge anlaşmalarındaki denetim numaralarını artırmaktır.

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Birincil bölgeye geri dönme-olağanüstü durum olayı

Kullanılabilir olduğunda bir birincil bölgeye geri dönmek için şu adımları izleyin:

1. İkincil bölgedeki iş ortaklarından gelen iletileri kabul etmeyi durdurun.  

2. [PowerShell cmdlet 'lerini](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0)kullanarak tüm birincil bölge sözleşmeleri için oluşturulan denetim numaralarını artırın.  

3. İkincil bölgeden birincil bölgeye doğrudan trafik.

4. Birincil bölgeden çalışma durumunu çekmek için ikincil bölgede oluşturulan mantıksal uygulamanın etkin olduğundan emin olun.

## <a name="x12"></a>X12 

EDI x12 belgeleri için iş sürekliliği, denetim numaralarına dayalıdır:

> [!TIP]
> Mantıksal uygulamalar oluşturmak için [x12 hızlı başlangıç şablonunu](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) da kullanabilirsiniz. Birincil ve ikincil tümleştirme hesapları oluşturmak, şablonu kullanmak için önkoşullardır. Şablon, biri alınan denetim numaraları ve diğeri oluşturulan denetim numaraları için olmak üzere iki mantıksal uygulama oluşturmaya yardımcı olur. İlgili Tetikleyiciler ve eylemler mantıksal uygulamalarda oluşturulur, tetikleyiciyi birincil tümleştirme hesabına ve ikinci tümleştirme hesabına yönelik eyleme bağlanıyor.

**Önkoşullar**

Gelen iletilerde olağanüstü durum kurtarmayı etkinleştirmek için x12 sözleşmesinin alma ayarlarındaki yinelenen denetim ayarlarını seçin.

![Yinelenen denetim ayarlarını seçin](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. İkincil bölgede bir [mantıksal uygulama](../logic-apps/quickstart-create-first-logic-app-workflow.md) oluşturun.    

2. **X12**üzerinde arama yapın ve **bir denetim numarası değiştirildiğinde x12-** seçeneğini belirleyin.   

   ![X12 arayın](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Tetikleyici, bir tümleştirme hesabıyla bağlantı oluşturmanızı ister. 
   Tetikleyici bir birincil bölge tümleştirme hesabına bağlanmalıdır.

3. Bir bağlantı adı girin, listeden *birincil bölge tümleştirme hesabınızı* seçin ve **Oluştur**' u seçin.   

   ![Birincil bölge tümleştirme hesabı adı](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. **Denetim numarası eşitleme ayarının başlatılacağı tarih/saat** isteğe bağlıdır. **Sıklık** , **gün**, **saat**, **dakika**veya **saniye** olarak bir aralıkla ayarlanabilir.   

   ![Tarih/saat ve sıklık](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. **Yeni adım** > **Eylem ekle**’yi seçin.

   ![Yeni adım, eylem ekleme](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. **X12**üzerinde arama yapın ve **x12-denetim numaralarını Ekle veya Güncelleştir**' i seçin.   

   ![Denetim numaraları ekleme veya güncelleştirme](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Bir eylemi ikincil bölge tümleştirme hesabına bağlamak için, kullanılabilir tümleştirme hesaplarının bir listesi için **bağlantıyı değiştir**  > **Yeni bağlantı ekle** ' yi seçin. Bir bağlantı adı girin, listeden *İkincil bölge tümleştirme hesabınızı* seçin ve **Oluştur**' u seçin. 

   ![İkincil bölge tümleştirme hesabı adı](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Sağ üst köşedeki simgeye tıklayarak ham girdilere geçiş yapın.

   ![Ham girişlere geç](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Dinamik içerik seçicisinden gövde ' yi seçin ve mantıksal uygulamayı kaydedin.

   ![Dinamik içerik alanları](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Tetikleyici, zaman aralığına göre alınan birincil bölge denetim numarası tablosunu yoklar ve yeni kayıtları çeker. 
   Eylem, ikincil bölge tümleştirme hesabındaki kayıtları güncelleştirir. 
   Güncelleştirme yoksa, tetikleyici durumu **atlandı**olarak görünür.   

   ![Denetim numarası tablosu](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Zaman aralığına bağlı olarak, artımlı çalışma zamanı durumu birincil bir bölgeden ikincil bir bölgeye çoğaltılır. Bir olağanüstü durum olayı sırasında, birincil bölge kullanılabilir olmadığında iş sürekliliği için ikincil bölgeye doğrudan trafik yönlendirin. 

## <a name="edifact"></a>EDIFACT 

EDI EDIOLGU belgelerinin iş sürekliliği, denetim numaralarını temel alır.

**Önkoşullar**

Gelen iletilerde olağanüstü durum kurtarmayı etkinleştirmek için, EDIOLGU sözleşmenizin alma ayarlarındaki yinelenen denetim ayarlarını seçin.

![Yinelenen denetim ayarlarını seçin](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. İkincil bölgede bir [mantıksal uygulama](../logic-apps/quickstart-create-first-logic-app-workflow.md) oluşturun.    

2. **Ediolgu**üzerinde arama yapın ve **bir denetim numarası değiştirildiğinde ediolgu**' ı seçin.

   ![EDIOLGU için arama](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Tetikleyici, bir tümleştirme hesabıyla bağlantı oluşturmanızı ister. 
   Tetikleyici bir birincil bölge tümleştirme hesabına bağlanmalıdır. 

3. Bir bağlantı adı girin, listeden *birincil bölge tümleştirme hesabınızı* seçin ve **Oluştur**' u seçin.    

   ![Birincil bölge tümleştirme hesabı adı](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. **Denetim numarası eşitleme ayarının başlatılacağı tarih/saat** isteğe bağlıdır. **Sıklık** , **gün**, **saat**, **dakika**veya **saniye** olarak bir aralıkla ayarlanabilir.    

   ![Tarih/saat ve sıklık](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. **Yeni adım** > **Eylem ekle**’yi seçin.    

   ![Yeni adım, eylem ekleme](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. **Ediolgu**üzerinde arama yapın ve **ediolgu-denetim numaralarını Ekle veya Güncelleştir**' i seçin.   

   ![Denetim numaraları ekleme veya güncelleştirme](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Bir eylemi ikincil bölge tümleştirme hesabına bağlamak için, kullanılabilir tümleştirme hesaplarının bir listesi için **bağlantıyı değiştir**  > **Yeni bağlantı ekle** ' yi seçin. Bir bağlantı adı girin, listeden *İkincil bölge tümleştirme hesabınızı* seçin ve **Oluştur**' u seçin.

   ![İkincil bölge tümleştirme hesabı adı](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Sağ üst köşedeki simgeye tıklayarak ham girdilere geçiş yapın.

   ![Ham girişlere geç](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Dinamik içerik seçicisinden gövde ' yi seçin ve mantıksal uygulamayı kaydedin.   

   ![Dinamik içerik alanları](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Tetikleyici, zaman aralığına göre alınan birincil bölge denetim numarası tablosunu yoklar ve yeni kayıtları çeker.
   Eylem kayıtları ikincil bölge tümleştirme hesabı olarak güncelleştirir. 
   Güncelleştirme yoksa, tetikleyici durumu **atlandı**olarak görünür.

   ![Denetim numarası tablosu](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Zaman aralığına bağlı olarak, artımlı çalışma zamanı durumu birincil bir bölgeden ikincil bir bölgeye çoğaltılır. Bir olağanüstü durum olayı sırasında, birincil bölge kullanılabilir olmadığında iş sürekliliği için ikincil bölgeye doğrudan trafik yönlendirin. 

## <a name="as2"></a>AS2 

AS2 protokolünü kullanan belgeler için iş sürekliliği ileti KIMLIĞI ve MıC değerine göre belirlenir.

> [!TIP]
> Mantıksal uygulamalar oluşturmak için [AS2 hızlı başlangıç şablonunu](https://github.com/Azure/azure-quickstart-templates/pull/3302) da kullanabilirsiniz. Birincil ve ikincil tümleştirme hesapları oluşturmak, şablonu kullanmak için önkoşullardır. Şablon, tetikleyici ve eylem içeren bir mantıksal uygulama oluşturulmasına yardımcı olur. Mantıksal uygulama bir tetikleyiciden birincil tümleştirme hesabına bir bağlantı ve ikincil tümleştirme hesabı için bir eylem oluşturur.

1. İkincil bölgede bir [mantıksal uygulama](../logic-apps/quickstart-create-first-logic-app-workflow.md) oluşturun.  

2. **AS2**üzerinde arama yapın ve **AS2-bir MIC değeri oluşturulduğunda**seçin.   

   ![AS2 arayın](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Tetikleyici bir tümleştirme hesabıyla bağlantı oluşturmanızı ister. 
   Tetikleyici bir birincil bölge tümleştirme hesabına bağlanmalıdır. 
   
3. Bir bağlantı adı girin, listeden *birincil bölge tümleştirme hesabınızı* seçin ve **Oluştur**' u seçin.

   ![Birincil bölge tümleştirme hesabı adı](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. **MIC değeri eşitleme ayarının başlatılacağı tarih/saat** isteğe bağlıdır. **Sıklık** , **gün**, **saat**, **dakika**veya **saniye** olarak bir aralıkla ayarlanabilir.   

   ![Tarih/saat ve sıklık](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. **Yeni adım** > **Eylem ekle**’yi seçin.  

   ![Yeni adım, eylem ekleme](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. **AS2**üzerinde arama yapın ve **AS2-MIC içeriğini Ekle veya Güncelleştir**' i seçin.  

   ![MıC ekleme veya güncelleştirme](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Bir eylemi ikincil tümleştirme hesabına bağlamak için, kullanılabilir tümleştirme hesaplarının bir listesi için **bağlantıyı değiştir**  > **Yeni bağlantı ekle** ' yi seçin. Bir bağlantı adı girin, listeden *İkincil bölge tümleştirme hesabınızı* seçin ve **Oluştur**' u seçin.

   ![İkincil bölge tümleştirme hesabı adı](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Sağ üst köşedeki simgeye tıklayarak ham girdilere geçiş yapın.

   ![Ham girişlere geç](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Dinamik içerik seçicisinden gövde ' yi seçin ve mantıksal uygulamayı kaydedin.   

   ![Dinamik içerik](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Tetikleyici, zaman aralığına göre birincil bölge tablosunu yoklar ve yeni kayıtları çeker. Eylem bunları ikincil bölge tümleştirme hesabına güncelleştirir. 
   Güncelleştirme yoksa, tetikleyici durumu **atlandı**olarak görünür.  

   ![Birincil bölge tablosu](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Zaman aralığına bağlı olarak, artımlı çalışma zamanı durumu birincil bölgeden ikincil bölgeye çoğaltılır. Bir olağanüstü durum olayı sırasında, birincil bölge kullanılabilir olmadığında iş sürekliliği için ikincil bölgeye doğrudan trafik yönlendirin. 

## <a name="next-steps"></a>Sonraki adımlar

[B2B iletilerini izleme](logic-apps-monitor-b2b-message.md)

