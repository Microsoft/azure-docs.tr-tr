---
title: Azure ile zamanlama tabanlı Otomasyon iş akışları oluşturun
description: Öğretici-Azure Logic Apps kullanarak bulut hizmetleri arasında tümleşen zamanlama tabanlı, yinelenen bir Otomasyon iş akışı oluşturun.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: 0dab2f49043f3a59dd4f06633e3f0228fc39a424
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792102"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>Öğretici: Azure Logic Apps ile zamanlamaya dayalı ve yinelenen Otomasyon iş akışları oluşturma

Bu öğreticide, yinelenen bir zamanlamaya göre çalışan bir iş akışını otomatikleştiren örnek bir [mantık uygulamasının](../logic-apps/logic-apps-overview.md) nasıl oluşturulacağı gösterilmektedir. Özellikle, bu örnek mantıksal uygulama, iki konum arasında trafik dahil olmak üzere seyahat süresini denetler ve haftanın her günü sabah çalışır. Süre belirli bir sınırı aşarsa, mantıksal uygulama size seyahat süresini ve hedefe ulaşmak için gereken ek süreyi içeren bir e-posta gönderir. İş akışı, bir zamanlama tabanlı tetikleyiciyle başlayan ve ardından bir Bing Haritalar eylemi, bir veri işlemleri eylemi, bir denetim akışı eylemi ve bir e-posta bildirim eylemi tarafından başlayan çeşitli adımlar içerir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Boş bir mantıksal uygulama oluşturma.
> * Mantıksal uygulamanızın zamanlamasını belirten bir yinelenme tetikleyicisi ekleyin.
> * Bir rota için seyahat süresini alan bir Bing Haritalar eylemi ekleyin.
> * Bir değişken oluşturan, seyahat süresini saniyelerden dakikaya dönüştüren ve bu sonucu değişkende depolayan bir eylem ekleyin.
> * Seyahat süresini belirtilen bir sınırla karşılaştıran bir koşul ekleme.
> * Seyahat süresi sınırı aşarsa size e-posta gönderen bir eylem ekleyin.

İşlemi tamamladığınızda, mantıksal uygulamanız bu yüksek düzeyli iş akışı gibi görünür:

![Örnek bir mantıksal uygulama iş akışı için üst düzey genel bakışı gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure hesabı ve aboneliği Aboneliğiniz yoksa, [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Office 365 Outlook, Outlook.com veya Gmail gibi Logic Apps tarafından desteklenen bir e-posta sağlayıcısından e-posta hesabı. Diğer sağlayıcılar için [buradaki bağlayıcı listesini inceleyin](/connectors/). Bu hızlı başlangıç, Office 365 Outlook 'U iş veya okul hesabıyla kullanır. Farklı bir e-posta hesabı kullanırsanız, genel adımlar aynı kalır, ancak kullanıcı arabirimi biraz farklı görünebilir.

  > [!IMPORTANT]
  > Gmail bağlayıcısını kullanmak istiyorsanız, mantıksal uygulamalarda kısıtlama olmadan yalnızca G-Suite iş hesapları bu bağlayıcıyı kullanabilir. Gmail tüketicisi hesabınız varsa, bu bağlayıcıyı yalnızca belirli Google onaylı hizmetlerle kullanabilirsiniz veya [Gmail Bağlayıcınız ile kimlik doğrulaması için kullanmak üzere bir Google istemci uygulaması oluşturabilirsiniz](/connectors/gmail/#authentication-and-bring-your-own-application). Daha fazla bilgi için, bkz. [Azure Logic Apps Google bağlayıcıları Için veri güvenliği ve gizlilik ilkeleri](../connectors/connectors-google-data-security-privacy-policy.md).

* Bir rotaya ilişkin seyahat süresini almak için, Bing Haritalar API’sinin erişim anahtarı gerekir. Bu anahtarı almak için [Bing Haritalar anahtarını alma](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) adımlarını izleyin.

* Mantıksal uygulamanızın trafiği belirli IP adresleriyle sınırlayan bir güvenlik duvarı üzerinden iletişim kurması gerekiyorsa, söz konusu güvenlik duvarının, mantıksal uygulamanızın bulunduğu Azure bölgesindeki Logic Apps hizmeti veya çalışma zamanı tarafından kullanılan [gelen](logic-apps-limits-and-config.md#inbound) ve [giden](logic-apps-limits-and-config.md#outbound) IP *adresleri için erişime* izin verilmesi gerekir. Mantıksal uygulamanız ayrıca Office 365 Outlook Bağlayıcısı veya SQL Bağlayıcısı gibi [yönetilen bağlayıcılar](../connectors/managed.md)kullanıyorsa veya [özel bağlayıcılar](/connectors/custom-connectors/)kullanıyorsa, güvenlik duvarının ayrıca mantıksal uygulamanızın Azure bölgesindeki *Tüm* [yönetilen bağlayıcı giden IP adresleri](logic-apps-limits-and-config.md#outbound) için erişime izin verilmesi gerekir.

## <a name="create-your-logic-app"></a>Mantıksal uygulamanızı oluşturma

1. Azure hesabınızın kimlik bilgileriyle [Azure portalında](https://portal.azure.com) oturum açın. Azure giriş sayfasında, **kaynak oluştur**' u seçin.

1. Azure Marketi menüsünde **tümleştirme**  >  **mantıksal uygulaması**' nı seçin.

   !["Tümleştirme" ve "mantıksal uygulama" seçiliyken Azure Marketi menüsünü gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. **Mantıksal uygulama** bölmesinde, oluşturmak istediğiniz mantıksal uygulama hakkında burada açıklanan bilgileri sağlayın.

   ![Mantıksal uygulama oluşturma bölmesini ve yeni mantıksal uygulama için sağlanacak bilgileri gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Abonelik** | <*Azure-abonelik-adı*> | Azure abonelik adınız. Bu örnekte `Pay-As-You-Go` kullanılmıştır. |
   | **Kaynak grubu** | LA-TravelTime-RG | İlgili kaynakları düzenlemek için kullanılan [Azure Kaynak grubunun](../azure-resource-manager/management/overview.md)adı. Bu örnek adlı yeni bir kaynak grubu oluşturur `LA-TravelTime-RG` . |
   | **Ad** | LA-TravelTime | Mantıksal uygulamanızın adı, yalnızca harf, sayı, kısa çizgi ( `-` ), alt çizgi ( `_` ), parantez ( `(` , `)` ) ve nokta ( `.` ) içerebilir. Bu örnekte `LA-TravelTime` kullanılmıştır. |
   | **Konum** | Batı ABD | Mantıksal uygulama bilgilerinizin depolanacağı bölge. Bu örnekte `West US` kullanılmıştır. |
   | **Log Analytics** | Kapalı | Tanılama günlüğüne kaydetme ayarını **Kapalı** durumda bırakın. |
   ||||

1. İşiniz bittiğinde, **gözden geçir + oluştur**' u seçin. Azure mantıksal uygulamanız hakkındaki bilgileri doğruladıktan sonra **Oluştur**' u seçin.

1. Azure uygulamanızı dağıtduktan sonra **Kaynağa Git**' i seçin.

   Azure, giriş videosunu, yaygın olarak kullanılan Tetikleyicileri ve mantıksal uygulama şablonu düzenlerini gösteren Logic Apps şablonu seçim bölmesini açar.

1. Video ve ortak Tetikleyiciler bölümlerinin ardından **Şablonlar** bölümüne gidip **boş mantıksal uygulama**' yı seçin.

   !["Boş mantıksal uygulama" seçiliyken Logic Apps şablonu seçim bölmesini gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Ardından, belirtilen bir zamanlamaya göre iş akışını çalıştıran yinelenme [tetikleyicisini](../logic-apps/logic-apps-overview.md#logic-app-concepts)ekleyin. Her mantıksal uygulama, belirli bir olay gerçekleştiğinde veya yeni veriler belirli bir koşulu karşıladığında tetiklenen bir tetikleyiciyle başlamalıdır. Daha fazla bilgi için bkz. [İlk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Yinelenme tetikleyicisini ekleme

1. Logic Apps tasarımcı arama kutusuna girin `recurrence` ve **yinelenme** adlı tetikleyiciyi seçin.

   !["Yinelenme" arama terimini ve "Tetikleyiciler" listesinde "yinelenme" tetikleyicisi içeren Logic Apps tasarımcı arama kutusunu gösteren ekran görüntüsü, "yinelenme" tetikleyicisi seçili görünür.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. **Yineleme** şekli üzerinde **üç nokta** (**...**) düğmesini ve ardından **Yeniden Adlandır**' ı seçin. Tetikleyiciyi şu açıklama ile yeniden adlandırın: `Check travel time every weekday morning`

   ![Üç nokta düğmesi seçili, "Ayarlar" listesinin açık ve "yeniden adlandır" komutunun seçildiği ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Tetikleyici içinde, bu özellikleri açıklandığı ve burada gösterilen şekilde değiştirin.

   ![Tetikleyicinin Aralık ve sıklığının değişikliklerinin gösterildiği ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Özellik | Gerekli | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Aralık** | Yes | 1 | Denetimler arasında beklenecek aralık sayısı |
   | **Sıklık** | Yes | Hafta | Yinelenme için kullanılacak zaman birimi |
   |||||

1. **Aralık** ve **Sıklık** altında **yeni parametre Ekle** listesini açın ve tetikleyiciye eklemek için bu özellikleri seçin.

   * **Şu günlerde**
   * **Şu saatlerde**
   * **Şu dakikalarda**

   ![Açılan "yeni parametre Ekle" listesinin ve bu seçili özelliklerin "Bu günlerde", "bu saatlere" ve "Bu dakikalar içinde" açılan ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Şimdi aşağıda gösterildiği gibi ek özelliklerin değerlerini ayarlayın ve burada açıklanmıştır.

   ![Aşağıdaki tabloda açıklandığı gibi değerlere ayarlanan ek özellikleri gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Şu günlerde** | Pazartesi,Salı,Çarşamba,Perşembe,Cuma | Bu ayar yalnızca **sıklığı** **hafta** olarak ayarladığınızda kullanılabilir. |
   | **Şu saatlerde** | 7,8,9 | Bu ayar yalnızca **sıklığı** **hafta** veya **gün** olarak ayarladığınızda kullanılabilir. Bu yinelenme için günün saatlerini seçin. Bu örnek `7` ,, `8` ve `9` -saat işaretleri üzerinde çalışır. |
   | **Şu dakikalarda** | 0,15,30,45 | Bu ayar yalnızca **sıklığı** **hafta** veya **gün** olarak ayarladığınızda kullanılabilir. Bu yinelenme için günün dakikalarını seçin. Bu örnek, sıfır saat işaretiyle başlar ve 15 dakikada bir çalışır. |
   ||||

   Bu tetikleyici hafta içi her gün 7:00’da başlayıp 9:45’e kadar her 15 dakikada bir tetiklenir. **Önizleme** kutusu, yinelenme zamanlamasını gösterir. Daha fazla bilgi için bkz. [Görevleri ve iş akışlarını zamanlama](../connectors/connectors-native-recurrence.md) ve [İş akışı eylemleri ve tetikleyicileri](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Tetikleyicinin ayrıntılarını şimdilik gizlemek için şeklin başlık çubuğunun içine tıklayarak şekli daraltın.

   ![Daraltılan tetikleyici şeklini gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Mantıksal uygulamanız artık Azure portal canlı olmakla kalmaz, belirtilen zamanlamaya göre tetikleyici dışında bir şey yapmaz. Şimdi, tetikleyici etkinleştirildiğinde gerçekleştirilecek bir eylem ekleyin.

## <a name="get-the-travel-time-for-a-route"></a>Bir rota için seyahat süresi alma

Şimdi bir tetikleyiciniz olduğuna göre artık iki yer arasındaki seyahat süresini alan bir [eylem](../logic-apps/logic-apps-overview.md#logic-app-concepts) ekleyebilirsiniz. Logic Apps, bu bilgileri kolayca alabilmeniz için Bing Haritalar API’si için bir bağlayıcı sağlar. Bu göreve başlamadan önce, bu öğreticinin önkoşullarında açıklandığı şekilde Bing Haritalar API anahtarına sahip olduğunuzdan emin olun.

1. Mantıksal uygulama Tasarımcısı ' nda, yineleme tetikleyicisi altında **yeni adım**' ı seçin.

1. **Işlem seçin** altında **Standart**' ı seçin. Arama kutusuna girin `bing maps` ve **Get Route** adlı eylemi seçin.

   !["Bing haritalar" eylemleri ile filtrelenmiş "işlem seçin" listesinin ve "yol al" eyleminin seçildiği ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Bing Haritalar bağlantınız yoksa bir bağlantı oluşturmanız istenir. Gösterildiği ve açıklandığı gibi bağlantı ayrıntılarını girip **Oluştur**' u seçin.

   ![Belirtilen bağlantı adı ve Bing Haritalar API anahtarı ile Bing Haritalar bağlantı kutusunu gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Özellik | Gerekli | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Bağlantı adı** | Yes | BingMapsConnection | Bağlantınıza bir ad verin. Bu örnekte `BingMapsConnection` kullanılmıştır. |
   | **API Anahtarı** | Yes | <*Bing-Haritalar-API-Key*> | Daha önce aldığınız Bing Haritalar API anahtarını girin. Bing Haritalar anahtarınız yoksa [nasıl anahtar alacağınızı](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) öğrenin. |
   |||||

1. Eylemi şu açıklama ile yeniden adlandırın: `Get route and travel time with traffic` .

1. Eylemde **yeni parametre Ekle listesini** açın ve bu özellikleri seçin.

   * **Getirileceğini**
   * **Mesafe birimi**
   * **Seyahat modu**

   !["Rotayı al..." Ifadesini gösteren ekran görüntüsü "optimize", "uzaklık birimi" ve "seyahat modu" özellikleri seçili olan eylem.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Şimdi gösterilen özellikler için değerleri girin ve burada açıklanmıştır.

   !["Yol al" eylemi için ek özellik değerlerini gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Özellik | Gerekli | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Güzergah noktası 1** | Yes | <*başlangıç konumu*> | Yol başlangıcının kaynağı. Bu örnek, başlangıç adresini bir örnek belirtir. |
   | **Güzergah noktası 2** | Yes | <*Son konum*> | Yönlendirmenize ait hedef. Bu örnek, bir örnek hedef adresi belirtir. |
   | **Getirileceğini** | No | timeWithTraffic | Rotanızı iyileştirmeye yönelik bir parametre; örneğin, mesafe, mevcut trafik ile seyahat süresi vb. **Timewithtraffic** parametre değerini seçin. |
   | **Mesafe birimi** | No | <*tercih edin*> | Rotanız için mesafe birimi. Bu örnek birim olarak **mil** kullanır. |
   | **Seyahat modu** | No | Sürüş | Rotanız için seyahat modu. **İtici** modu ' nu seçin. |
   |||||

   Bu parametreler ve değerler hakkında daha fazla bilgi için bkz. [Rota hesaplama](/bingmaps/rest-services/routes/calculate-a-route).

1. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Ardından, geçerli seyahat süresini saniyeler olarak değil, dakikalar olarak dönüştürebilmeniz ve depolayabilmeniz için bir değişken oluşturun. Böylece dönüştürmeyi yinelemekten kaçınabilir ve sonraki adımlarda değeri daha kolayca kullanabilirsiniz. 

## <a name="create-a-variable-to-store-travel-time"></a>Seyahat süresini depolamak için bir değişken oluşturma

Bazen iş akışınızda veriler üzerinde işlemler çalıştırmak ve sonra sonuçları daha sonra kullanmak isteyebilirsiniz. Bu sonuçları, kolayca yeniden kullanabilmeniz veya başvurabilmeniz için kaydetmek üzere, işlemeden sonra bu sonuçları depolayan değişkenler oluşturabilirsiniz. Yalnızca mantıksal uygulamanızın en üst düzeyinde değişkenler oluşturabilirsiniz.

Varsayılan olarak, **yolu al** eylemi, geçerli seyahat süresini, **seyahat süresi trafik** özelliğinden saniye cinsinden trafik ile döndürür. Bunun yerine bu değeri dönüştürüp depolayarak daha sonra yeniden dönüştürme olmadan değerin yeniden kullanımını kolaylaştırırsınız.

1. Tasarımcıda **yol al** eyleminin altında **yeni adım**' ı seçin.

1. **Işlem seçin** altında, **yerleşik**' i seçin. Arama kutusuna girin `variables` ve **Initialize Variable** adlı eylemi seçin.

   !["Değişken Başlat" eyleminin seçildiği ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Bu eylemi şu açıklama ile yeniden adlandırın: `Create variable to store travel time`

1. Bu tabloda ve tablonun altındaki adımlarda, değişkeniniz için bu bilgileri sağlayın:

   | Özellik | Gerekli | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Ad** | Yes | travelTime | Değişkeninizin adı. Bu örnekte `travelTime` kullanılmıştır. |
   | **Tür** | Yes | Tamsayı | Değişkeninizin veri türü |
   | **Değer** | No | Geçerli seyahat süresini saniyeden dakikaya dönüştürdüğü bir ifade (Bu tablodaki adımlara bakın). | Değişkeninizin ilk değeri |
   |||||

   1. **Değer** özelliği için ifade oluşturmak üzere, dinamik içerik listesinin görünmesi için kutunun içine tıklayın. Gerekirse, dinamik liste görünene kadar tarayıcınızı genişletebilirsiniz. Dinamik içerik listesinde ifade düzenleyicisini gösteren **ifade**' ı seçin.

      ![Dinamik içerik listesini açan "Value" özelliğinin içindeki imlece birlikte "değişken Başlat" eylemini gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Dinamik içerik listesi, iş akışınızda sonraki eylemlere giriş olarak seçebileceğiniz önceki eylemlerden çıkışları gösterir. Dinamik içerik listesi, ifadenizde işlem yapan işlevleri seçmek için kullanabileceğiniz bir ifade Düzenleyicisi içerir. Bu ifade Düzenleyicisi yalnızca dinamik içerik listesinde kullanılabilir.

   1. İfade düzenleyicisinde şu ifadeyi girin: `div(,60)`

      ![İfade düzenleyicisini "div (, 60)" ifadesi girilmiş olarak gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. İfade içinde imlecinizi sol parantez (**(**) ve virgül (**,**) arasına koyun ve **dinamik içerik**' i seçin.

      ![İmlecin "dinamik içerik" seçiliyken "div (, 60)" ifadesinde imlecin nereye yerleştirileceğini gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Dinamik içerik listesinde, özellik değeri, **seyahat süresi trafiği**' ni seçin.

      !["Seyahat süresi trafiği" özellik değerinin seçili olduğunu gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Özellik değeri ifadenin içinde çözümlendikten sonra **Tamam**' ı seçin.

      !["Tamam" düğmesini seçili olarak gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      **Value** özelliği artık burada gösterildiği gibi görünür:

      ![Çözümlenen ifadeye "Value" özelliğini gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Mantıksal uygulamanızı kaydedin.

Ardından, geçerli seyahat süresinin belirli bir sınırdan büyük olup olmadığını denetleyen bir koşul ekleyin.

## <a name="compare-the-travel-time-with-limit"></a>Seyahat süresini sınırla karşılaştırın

1. **Seyahat süresini depolamak için değişken Oluştur** eylemini altında **yeni adım**' ı seçin.

1. **Işlem seçin** altında, **yerleşik**' i seçin. Arama kutusuna `condition` yazın. Eylemler listesinden **koşul** adlı eylemi seçin.

   !["Koşul" eyleminin seçili olduğunu gösteren ekran görüntüsü](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Koşulu şu açıklama ile yeniden adlandırın: `If travel time exceeds limit`

1. Burada açıklandığı gibi, **Seyahatsaati** Özellik değerinin belirtilen limiti aşıp aşmayacağını denetleyen bir koşul oluşturun:

   1. Koşulda, koşulun sol tarafında **bir değer seçin** kutusunun içine tıklayın.

   1. Görüntülenen dinamik içerik listesinden, **değişkenler** altında, **seyahattime** adlı özelliği seçin.

      ![Dinamik içerik listesi açık ve "Seyahattime" özelliği seçili olan koşulun sol tarafındaki "bir değer Seç" kutusunu gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. Orta karşılaştırma kutusunda adlı işleci **değerinden büyüktür**' i seçin.

   1. Koşulun sağ tarafında, **bir değer seçin** kutusuna şu sınırı girin: `15`

      İşiniz bittiğinde, koşul şu örneğe benzer şekilde görünür:

      ![Seyahat süresini belirtilen sınıra göre karşılaştırmak için tamamlanan koşulu gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Mantıksal uygulamanızı kaydedin.

Sonra, seyahat süresi sınırınızı aştığında çalıştırılacak eylemi ekleyin.

## <a name="send-email-when-limit-exceeded"></a>Sınır aşıldığında e-posta gönder

Şimdi seyahat süresi sınırınızı aştığında size e-posta gönderen bir eylem ekleyin. Bu e-posta, geçerli seyahat süresini ve belirtilen rotada seyahat etmek için gerekli ek süreyi içerir.

1. Koşulun **doğru** dalında **Eylem Ekle**' yi seçin.

1. **Işlem seçin** altında **Standart**' ı seçin. Arama kutusuna `send email` yazın. Listede çok fazla sonuç döndürüldüğünden, listeyi filtrelemenize yardımcı olması için önce istediğiniz e-posta bağlayıcısını seçin.

   Örneğin, bir Outlook e-posta hesabınız varsa, hesap türü için bağlayıcıyı seçin:

   * Azure iş veya okul hesapları için **Office 365 Outlook** girişini seçin.
   * Kişisel Microsoft hesapları için **Outlook.com** girişini seçin.

   Bu örnek, Office 365 Outlook seçilerek devam eder.

   !["Standart" kategorisi ve "Office 365 Outlook" Bağlayıcısı seçiliyken "işlem listesi seç" i gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. Bağlayıcının eylemleri görüntülendiğinde, e-posta gönderen eylemi seçin, örneğin:

   !["E-posta gönder" eyleminin seçildiği ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Henüz bir bağlantınız yoksa, oturum açın ve istendiğinde e-posta hesabınıza erişim kimliğini doğrulayın.

   Azure Logic Apps, e-posta hesabınıza bir bağlantı oluşturur.

1. Eylemi şu açıklama ile yeniden adlandırın: `Send email with travel time`

1. **To** özelliği için alıcının e-posta adresini girin. Sınama amacıyla e-posta adresinizi kullanabilirsiniz.

1. **Konu** özelliği için, e-postanın konusunu belirtin ve aşağıdaki adımları Izleyerek **seyahatsaat** değişkenini ekleyin:

   1. Sonunda boşluk olacak şekilde `Current travel time (minutes):` metnini girin. Dinamik içerik listesi açık kalacak şekilde imlecinizi **Konu** kutusunda saklayın.

   1. Dinamik içerik listesinden, **değişkenler** başlığında, **Seyahat saati** adlı değişkenin görünmesi için **daha fazla gör** ' ü seçin.

      !["Değişkenler" bölümü ve "daha fazla gör" seçili olan dinamik içerik listesini gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > **Konu** özelliği bir dize değeri beklediği Için, **Seyahat saati** bir tamsayı değeri olduğu için dinamik Içerik listesi, **seyahatsaat** değişkenini otomatik olarak göstermez.

      !["Seyahattime" değişkeni seçili olan dinamik içerik listesini gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. **Body** özelliği için aşağıdaki adımları izleyerek e-posta gövdesinin içeriğini belirtin:

   1. Sonunda boşluk olacak şekilde `Add extra travel time (minutes):` metnini girin. Dinamik içerik listesi açık kalacak şekilde imlecinizi **gövde** kutusunda saklayın.

   1. Dinamik içerik listesinde ifade düzenleyicisini gösteren **ifade**' ı seçin.

      !["Expression" seçiliyken dinamik içerik listesini gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. Sınır düzenleyicisinde, `sub(,15)` sınırınızı aşan dakika sayısını hesaplayabilmeniz için yazın: 

      !["Sub (, 15)" ifadesi girilmiş ifade düzenleyicisini gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. İfade içinde imlecinizi sol parantez (**(**) ve virgül (**,**) arasına koyun ve **dinamik içerik**' i seçin.

      ![İmlecin "alt (, 15)" ifadesinde "dinamik içerik" seçiliyken nereye yerleştirileceğini gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. **Değişkenler** bölümünde **travelTime** seçeneğini belirleyin.

      !["Seyahattime" değişkeni seçili olan dinamik içerik listesini gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Özellik ifadenin içinde çözümlendikten sonra **Tamam**' ı seçin.

      ![Dinamik içerik listesini ve "Tamam" seçili olduğunu gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      **Body** özelliği şimdi burada gösterildiği gibi görünür:

      ![E-posta eyleminin "Body" özelliğinde çözümlenen ifadeye sahip dinamik içerik listesini gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Mantıksal uygulamanızı kaydedin.

Ardından, aşağıdaki örneğe benzer şekilde mantıksal uygulamanızı test edin ve çalıştırın:

![Tamamlanmış örnek mantıksal uygulama iş akışını gösteren ekran görüntüsü](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Mantıksal uygulamanızı çalıştırın

Mantıksal uygulamanızı el ile başlatmak için, Tasarımcı araç çubuğunda **Çalıştır**' ı seçin.

* Geçerli seyahat süresi sınırınızda kalırsa, mantıksal uygulamanız başka bir şey yapmaz ve yeniden denetlemeden önce bir sonraki aralığı bekler. 

* Geçerli seyahat süresi sınırınızı aşarsa, geçerli seyahat süresi ve sınırınızı aşan dakika sayısı ile bir e-posta alırsınız. Mantıksal uygulamanızın gönderdiği örnek bir e-posta şöyledir:

  ![Geçerli seyahat süresini ve belirtilen sınırınızı aşan ek seyahat süresini raporlayan örnek bir e-posta gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > E-posta gelmezse istenmeyen e-posta klasörüne bakın. E-postanızın istenmeyen posta filtresi bu tür postaları yeniden yönlendirebilir. Mantıksal uygulamanızın düzgün bir şekilde çalışıp çalışmadığından emin değilseniz bkz. [Mantıksal uygulama sorunlarını giderme](../logic-apps/logic-apps-diagnosing-failures.md).

Tebrikler, şimdi zamanlama tabanlı yinelenen bir mantıksal uygulama oluşturup çalıştırdınız. 

**Yinelenme** tetikleyicisini kullanan diğer mantıksal uygulamalar oluşturmak için, bir mantıksal uygulama oluşturduktan sonra bu şablonlara göz atın:

* Size gönderilen günlük anımsatıcıları alın.
* Eski Azure bloblarını silin.
* Azure Depolama kuyruğuna bir ileti gönderin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Mantıksal uygulamanız, uygulamayı devre dışı bırakana veya silme yapılıncaya kadar çalışmaya devam eder. Örnek mantıksal uygulamaya artık ihtiyaç duymadığınızda, mantıksal uygulamanızı ve ilgili kaynakları içeren kaynak grubunu silin.

1. Azure portal arama kutusuna oluşturduğunuz kaynak grubunun adını girin. Sonuçlardan **kaynak grupları**' nın altında kaynak grubunu seçin.

   Bu örnek adlı kaynak grubunu oluşturdu `LA-TravelTime-RG` .

   !["La-seyahat-saat-RG" girilen ve * * LA-Seyahatsaati-RG * * seçili olan Azure Arama kutusunu gösteren ekran görüntüsü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > Azure giriş sayfasında **son kaynaklar** altında kaynak grubu görünüyorsa, giriş sayfasından grubu seçebilirsiniz.

1. Kaynak grubu menüsünde, **genel bakış** ' ın seçili olduğunu denetleyin. **Genel bakış** bölmesinin araç çubuğunda **kaynak grubunu sil**' i seçin.

   ![Kaynak grubunun "genel bakış" bölmesini gösteren ekran görüntüsü ve bölmenin araç çubuğunda "kaynak grubunu sil" seçilidir.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Görüntülenen onay bölmesinde, kaynak grubu adını girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, belirli bir zamanlamaya göre (hafta içi sabahları) trafiği denetleyen bir mantıksal uygulama oluşturdunuz ve seyahat süresi belirtilen sınırı aştığında işlem (bir e-posta gönderir). Şimdi, Azure Hizmetleri, Microsoft Hizmetleri ve diğer hizmet olarak yazılım (SaaS) uygulamalarını tümleştirerek, onay için posta listesi istekleri gönderen bir mantıksal uygulama oluşturmayı öğrenin.

> [!div class="nextstepaction"]
> [Posta listesi isteklerini yönetme](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
