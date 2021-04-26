---
title: Önceden oluşturulmuş şablonlar kullanarak mantıksal uygulama iş akışlarını daha hızlı oluşturma
description: Azure Logic Apps tarafından sunulan önceden oluşturulmuş şablonları kullanarak mantıksal uygulama iş akışlarını hızlıca oluşturun
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 42c592e6aede4537dc983fd2cff043a878f81f1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593107"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Önceden oluşturulmuş şablonlardan mantıksal uygulama iş akışları oluşturma

İş akışlarını daha hızlı oluşturmaya başlamanızı sağlamak için Logic Apps, yaygın olarak kullanılan desenleri izleyen önceden oluşturulmuş mantıksal uygulamalar olan şablonlar sağlar. Bu şablonları belirtilen şekilde kullanın veya senaryonuza uyacak şekilde düzenleyin.

Bazı Şablon kategorileri şunlardır:

| Şablon türü | Description | 
| ------------- | ----------- | 
| Kurumsal bulut şablonları | Azure blob, Dynamics CRM, Salesforce, Box ve Kurumsal bulut gereksinimleriniz için diğer bağlayıcılar dahil olmak üzere. Örneğin, bu şablonları iş adaylarını düzenlemek veya kurumsal dosya verilerinizi yedeklemek için kullanabilirsiniz. | 
| Kişisel üretkenlik şablonları | Günlük anımsatıcıları ayarlayarak, önemli iş öğelerini Yapılacaklar listelerine açıp uzun görevleri tek bir Kullanıcı onay adımına otomatik hale getirerek kişisel verimliliği artırın. | 
| Tüketici bulutu şablonları | Twitter, bolluk ve e-posta gibi sosyal medya hizmetlerini tümleştirmek için. Sosyal medya pazarlama girişimlerini güçlendirerek faydalıdır. Bu şablonlar, bulut kopyalama gibi görevleri de içerir, bu da geleneksel olarak yinelenen görevlerde zaman tasarrufu yaparak üretkenliği artırır. | 
| Enterprise Integration Pack şablonları | VETER 'ı yapılandırmak için (doğrulama, ayıklama, dönüştürme, zenginleştirme, yönlendirme) işlem hatları, AS2 üzerinde bir x12 EDI belgesi alma, XML 'e dönüştürme ve x12, EDIOLGU ve AS2 iletilerini işleme. | 
| Protokol deseninin şablonları | HTTP üzerinden istek yanıtı ve FTP ve SFTP arasında tümleştirmeler gibi protokol desenleri uygulamak için. Bu şablonları, belirtilen şekilde kullanın veya karmaşık protokol desenleri için oluşturun. | 
||| 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). Mantıksal uygulama oluşturma hakkında daha fazla bilgi için bkz. [mantıksal uygulama oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Şablonlardan mantıksal uygulamalar oluşturma

1. Henüz yapmadıysanız [Azure Portal](https://portal.azure.com "Azure portalı")oturum açın.

2. Ana Azure menüsünden **kaynak oluştur**  >  **kurumsal tümleştirme**  >  **mantıksal uygulama**' yı seçin.

   ![Azure portalı, Yeni, Kurumsal Tümleştirme, Mantıksal Uygulama](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Bu görüntünün altındaki tabloda yer alan ayarlarla mantıksal uygulamanızı oluşturun:

   ![Mantıksal uygulama ayrıntılarını sağlayın](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Ayar | Değer | Açıklama | 
   | ------- | ----- | ----------- | 
   | **Ad** | *mantıksal uygulama adınız* | Mantıksal uygulama için benzersiz bir ad girin. | 
   | **Abonelik** | *your-Azure-subscription-name* | Kullanmak istediğiniz Azure aboneliğini seçin. | 
   | **Kaynak grubu** | *Azure-kaynak-grubunuzun-adı* | Bu mantıksal uygulama için bir [Azure Kaynak grubu](../azure-resource-manager/management/overview.md) oluşturun veya seçin ve bu uygulamayla ilişkili tüm kaynakları düzenleyin. | 
   | **Konum** | *Azure-veri-merkezi-bölgeniz* | Batı ABD gibi mantıksal uygulamanızın dağıtılacağı veri merkezi bölgesini seçin. | 
   | **Log Analytics** | **Kapalı** (varsayılan) veya **Açık** | [Azure izleyici günlüklerini](../azure-monitor/logs/log-query-overview.md)kullanarak mantıksal uygulamanız için [tanılama günlüğü](../logic-apps/monitor-logic-apps-log-analytics.md) ayarlayın. Zaten bir Log Analytics çalışma alanına sahip olmanızı gerektirir. | 
   |||| 

4. Hazır olduğunuzda **Panoya sabitle**'yi seçin. Bu şekilde mantıksal uygulamanız otomatik olarak Azure panonuzda görüntülenir ve dağıtımdan sonra açılır. **Oluştur**' a tıklayın.

   > [!NOTE]
   > Mantıksal uygulamanızı sabitlemek istemezseniz öğreticiye devam edebilmek için dağıtım sonrasında mantıksal uygulamanızı el ile bulup açmanız gerekir.

   Azure mantıksal uygulamanızı dağıttıktan sonra Logic Apps Tasarımcısı açılır ve tanıtım videosu bulunan bir sayfa görüntülenir. 
   Videonun altında sık kullanılan mantıksal uygulama desenlerine ait şablonları bulabilirsiniz. 

5. Tanıtım videosunu ve ortak Tetikleyicileri **şablonlara** göre kaydırın. Önceden oluşturulmuş bir şablon seçin. Örnek:

   ![Mantıksal uygulama şablonu seçin](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Mantıksal uygulamanızı sıfırdan oluşturmak için **boş mantıksal uygulama**' yı seçin.

   Önceden oluşturulmuş bir şablon seçtiğinizde, bu şablon hakkında daha fazla bilgi görüntüleyebilirsiniz. 
   Örnek:

   ![Önceden oluşturulmuş bir şablon seçin](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Seçili şablonla devam etmek için **Bu şablonu kullan**' ı seçin. 

7. Şablondaki bağlayıcılar temelinde, şu adımlardan herhangi birini gerçekleştirmeniz istenir:

   * Şablon tarafından başvurulan sistemler veya hizmetlerle ilgili kimlik bilgilerinizle oturum açın.

   * Şablon tarafından başvurulan tüm hizmet veya sistemler için bağlantı oluşturun. Bir bağlantı oluşturmak için, bağlantınız için bir ad sağlayın ve gerekirse, kullanmak istediğiniz kaynağı seçin. 

   * Bu bağlantıları zaten ayarladıysanız **devam**' ı seçin.

   Örnek:

   ![Bağlantı oluşturma](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   İşiniz bittiğinde mantıksal uygulamanız açılır ve Logic Apps tasarımcısında görünür.

   > [!TIP]
   > Şablon görüntüleyicisine dönmek için tasarımcı araç çubuğunda **Şablonlar** ' ı seçin. Bu eylem kaydedilmemiş değişiklikleri atar, bu nedenle isteğinizi onaylamak için bir uyarı iletisi görünür.

8. Mantıksal uygulamanızı oluşturmaya devam edin.

   > [!NOTE] 
   > Birçok şablon, önceden doldurulmuş gerekli özelliklere sahip olabilecek bağlayıcıları içerir. Ancak, mantıksal uygulamayı düzgün bir şekilde dağıtabilmeniz için bazı şablonlar yine de değer sağlamanızı gerektirebilir. Eksik özellik alanlarını tamamlamadan dağıtmayı denerseniz, bir hata iletisi alırsınız. 

## <a name="update-logic-apps-with-templates&quot;></a>Mantıksal uygulamaları şablonlarla güncelleştirme

1. [Azure Portal](https://portal.azure.com &quot;Azure portalı"), mantıksal uygulama Tasarımcısı ' nda mantıksal uygulamanızı bulun ve açın.

2. Tasarımcı araç çubuğunda **Şablonlar**' ı seçin. Bu eylem kaydedilmemiş değişiklikleri atar, bu nedenle devam etmek istediğinizi doğrulayabilmeniz için bir uyarı iletisi görüntülenir. Onaylamak için **Tamam**' ı seçin. Örnek:

   !["Şablonlar" ı seçin](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Tanıtım videosunu ve ortak Tetikleyicileri **şablonlara** göre kaydırın. Önceden oluşturulmuş bir şablon seçin. Örnek:

   ![Mantıksal uygulama şablonu seçin](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Önceden oluşturulmuş bir şablon seçtiğinizde, bu şablon hakkında daha fazla bilgi görüntüleyebilirsiniz. 
   Örnek:

   ![Önceden oluşturulmuş bir şablon seçin](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Seçili şablonla devam etmek için **Bu şablonu kullan**' ı seçin. 

5. Şablondaki bağlayıcılar temelinde, şu adımlardan herhangi birini gerçekleştirmeniz istenir:

   * Şablon tarafından başvurulan sistemler veya hizmetlerle ilgili kimlik bilgilerinizle oturum açın.

   * Şablon tarafından başvurulan tüm hizmet veya sistemler için bağlantı oluşturun. Bir bağlantı oluşturmak için, bağlantınız için bir ad sağlayın ve gerekirse, kullanmak istediğiniz kaynağı seçin. 

   * Bu bağlantıları zaten ayarladıysanız **devam**' ı seçin.

   ![Bağlantı oluşturma](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Mantıksal uygulamanız artık açılır ve Logic Apps tasarımcısında görünür.

8. Mantıksal uygulamanızı oluşturmaya devam edin. 

   > [!TIP]
   > Değişikliklerinizi kaydetmediyseniz, çalışmanızı atabilir ve önceki mantıksal uygulamanıza dönebilirsiniz. Tasarımcı araç çubuğunda, **at**' ı seçin.

> [!NOTE] 
> Birçok şablon, önceden doldurulmuş gerekli özelliklere sahip olabilecek bağlayıcıları içerir. Ancak, mantıksal uygulamayı düzgün bir şekilde dağıtabilmeniz için bazı şablonlar yine de değer sağlamanızı gerektirebilir. Eksik özellik alanlarını tamamlamadan dağıtmayı denerseniz, bir hata iletisi alırsınız.

## <a name="deploy-logic-apps-built-from-templates"></a>Şablonlardan oluşturulan Logic Apps 'i dağıtma

Şablonda yaptığınız değişiklikleri yaptıktan sonra değişikliklerinizi kaydedebilirsiniz. Bu eylem ayrıca mantıksal uygulamanızı otomatik olarak yayımlar.

Tasarımcı araç çubuğunda **Kaydet**'i seçin.

![Mantıksal Uygulamanızı kaydetme ve yayımlama](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Destek alın

* Sorular için, [Azure Logic Apps Için Microsoft Q&soru sayfasını](/answers/topics/azure-logic-apps.html)ziyaret edin.
* Özelliklerle ilgili fikirlerinizi göndermek veya gönderilmiş olanları oylamak için [Logic Apps kullanıcı geri bildirimi sitesini](https://aka.ms/logicapps-wish) ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

Örnekler, senaryolar, müşteri hikayeleri ve izlenecek yollar aracılığıyla mantıksal uygulamalar oluşturma hakkında bilgi edinin.

> [!div class="nextstepaction"]
> [Mantıksal uygulama örneklerini, senaryoları ve izlenecek yolları gözden geçirin](../logic-apps/logic-apps-examples-and-scenarios.md)
