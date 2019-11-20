---
title: Zamanlama tabanlı otomatik iş akışları oluşturun-Azure Logic Apps
description: Öğretici-Azure Logic Apps kullanarak zamanlamaya dayalı, yinelenen ve otomatikleştirilmiş iş akışı oluşturma
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: eae2319e8d1c162969a04f8dafa18eec671ee1d0
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034684"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Öğretici: Azure Logic Apps kullanarak otomatik, zamanlamaya dayalı ve yinelenen iş akışları oluşturma

Bu öğreticide, bir [mantıksal uygulamanın](../logic-apps/logic-apps-overview.md) nasıl oluşturulacağı ve bir zamanlamaya göre çalışan yinelenen bir iş akışının nasıl otomatikleştirilmesi gösterilmektedir. Özellikle, bu örnek mantıksal uygulama her gün sabah günü çalışır ve iki konum arasında trafik dahil olmak üzere seyahat süresini denetler. Zaman belirli bir sınırı aşarsa mantıksal uygulama, hedefiniz için seyahat süresini ve gerekli ek süreyi içeren bir e-posta gönderir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Boş bir mantıksal uygulama oluşturma.
> * Mantıksal uygulamanızın zamanlamasını belirten bir yinelenme tetikleyicisi ekleyin.
> * Bir rota için seyahat süresini alan bir Bing Haritalar eylemi ekleyin.
> * Bir değişken oluşturan, seyahat süresini saniyelerden dakikaya dönüştüren ve bu sonucu değişkende depolayan bir eylem ekleyin.
> * Seyahat süresini belirtilen bir sınırla karşılaştıran bir koşul ekleme.
> * Seyahat süresi sınırı aşarsa size e-posta gönderen bir eylem ekleyin.

İşlemi tamamladığınızda, mantıksal uygulamanız bu yüksek düzeyli iş akışı gibi görünür:

![Üst düzey mantıksal uygulama iş akışına genel bakış](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/) .

* Office 365 Outlook, Outlook.com veya Gmail gibi Logic Apps tarafından desteklenen bir e-posta sağlayıcısından e-posta hesabı. Diğer sağlayıcılar için [buradaki bağlayıcı listesini inceleyin](https://docs.microsoft.com/connectors/). Bu hızlı başlangıç, Office 365 Outlook hesabını kullanır. Farklı bir e-posta hesabı kullanırsanız, genel adımlar aynı kalır, ancak kullanıcı arabirimi biraz farklı görünebilir.

* Bir rotaya ilişkin seyahat süresini almak için, Bing Haritalar API’sinin erişim anahtarı gerekir. Bu anahtarı almak için [Bing Haritalar anahtarını alma](https://docs.microsoft.com/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) adımlarını izleyin.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Azure hesabınızın kimlik bilgileriyle [Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-your-logic-app"></a>Mantıksal uygulamanızı oluşturma

1. Ana Azure menüsünden **kaynak oluştur** > **tümleştirme** > **mantıksal uygulama**' yı seçin.

   ![Mantıksal uygulama kaynağınızı oluşturma](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. **Mantıksal uygulama oluştur** bölümünde, gösterildiği ve açıklandığı gibi mantıksal uygulamanızla ilgili bu bilgileri sağlayın. İşiniz bittiğinde **Oluştur**’u seçin.

   ![Mantıksal uygulamanız hakkında bilgi sağlayın](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Adı** | LA-TravelTime | Mantıksal uygulamanızın adı; yalnızca harfler, rakamlar, tireler (`-`), alt çizgiler (`_`), parantezler (`(`, `)`) ve noktalar (`.`) içerebilir. Bu örnekte "LA-Seyahatsaati" kullanılmaktadır. |
   | **Abonelik** | <*your-Azure-subscription-name*> | Azure abonelik adınız |
   | **Kaynak grubu** | LA-TravelTime-RG | İlgili kaynakları düzenlemek için kullanılan [Azure Kaynak grubunun](../azure-resource-manager/resource-group-overview.md)adı. Bu örnek "LA-Seyahattime-RG" kullanır. |
   | **Konum** | Batı ABD | Mantıksal uygulama bilgilerinizin depolanacağı bölge. Bu örnek, "Batı ABD" kullanır. |
   | **Log Analytics** | Kapalı | Tanılama günlüğüne kaydetme ayarını **Kapalı** durumda bırakın. |
   ||||

1. Azure 'un uygulamanızı dağıtmasından sonra Azure araç çubuğunda **bildirimler** >  dağıtılan mantıksal uygulamanız Için**Kaynağa Git** ' i seçin.

   ![Yeni mantıksal uygulama kaynağına gidin](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   Ya da, arama kutusuna adı yazarak mantıksal uygulamanızı bulabilir ve seçebilirsiniz.

   Logic Apps Tasarımcısı açılır ve bir tanıtım videosu ile sık kullanılan Tetikleyiciler ve mantıksal uygulama desenlerine sahip bir sayfa gösterir. **Şablonlar** altında **Boş Mantıksal Uygulama**'yı seçin.

   ![Boş mantıksal uygulama şablonu seçin](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Ardından, belirtilen bir zamanlamaya göre tetiklenen yinelenme [tetikleyicisini](../logic-apps/logic-apps-overview.md#logic-app-concepts)ekleyin. Her mantıksal uygulama, belirli bir olay gerçekleştiğinde veya yeni veriler belirli bir koşulu karşıladığında tetiklenen bir tetikleyiciyle başlamalıdır. Daha fazla bilgi için bkz. [İlk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Yinelenme tetikleyicisini ekleme

1. Mantıksal uygulama Tasarımcısı ' nda, arama kutusuna filtreniz olarak "yinelenme" yazın. **Tetikleyiciler** listesinden **yinelenme** tetikleyicisini seçin.

   !["Yinelenme" tetikleyicisi Ekle](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. **Yineleme** şekli üzerinde **üç nokta** ( **...** ) düğmesini ve ardından **Yeniden Adlandır**' ı seçin. Tetikleyiciyi şu açıklama ile yeniden adlandırın: `Check travel time every weekday morning`

   ![Yinelenme tetikleyicisi açıklamasını yeniden adlandırma](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Tetikleyici içinde, bu özellikleri değiştirin.

   ![Yinelenme tetikleyicisinin aralığını ve sıklığını değiştirme](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Özellik | Gereklidir | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Aralık** | Yes | 1 | Denetimler arasında beklenecek aralık sayısı |
   | **Sıklık** | Yes | Hafta | Yinelenme için kullanılacak zaman birimi |
   |||||

1. **Aralık** ve **Sıklık**altında **yeni parametre Ekle** listesini açın ve tetikleyiciye eklemek için bu özellikleri seçin.

   * **Şu günlerde**
   * **Şu saatlerde**
   * **Şu dakikalarda**

   ![Yineleme tetikleyicisi için özellikler ekleme](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Şimdi aşağıda gösterildiği gibi ek özelliklerin değerlerini ayarlayın ve burada açıklanmıştır.

   ![Zamanlama ve yinelenme ayrıntılarını sağlayın](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Şu günlerde** | Pazartesi,Salı,Çarşamba,Perşembe,Cuma | Yalnızca **Sıklık** "Hafta" olarak ayarlandığında kullanılabilir |
   | **Şu saatlerde** | 7,8,9 | Yalnızca **Sıklık** "Hafta" veya "Gün" olarak ayarlandığında kullanılabilir. Bu yinelenmenin çalıştırılacağı günün saatlerini seçin. Bu örnek, 7, 8 ve 9 saat işaretlerinde çalıştırılır. |
   | **Şu dakikalarda** | 0,15,30,45 | Yalnızca **Sıklık** "Hafta" veya "Gün" olarak ayarlandığında kullanılabilir. Bu yinelenmenin çalıştırılacağı günün dakikalarını seçin. Bu örnek, sıfır saat işaretinden başlayarak her 15 dakikada bir çalıştırılır. |
   ||||

   Bu tetikleyici hafta içi her gün 7:00’da başlayıp 9:45’e kadar her 15 dakikada bir tetiklenir. **Önizleme** kutusu, yinelenme zamanlamasını gösterir. Daha fazla bilgi için bkz. [Görevleri ve iş akışlarını zamanlama](../connectors/connectors-native-recurrence.md) ve [İş akışı eylemleri ve tetikleyicileri](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Tetikleyicinin ayrıntılarını şimdilik gizlemek için şeklin başlık çubuğunun içine tıklayın.

   ![Ayrıntıları gizlemek için şekli daraltın](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Şimdi mantıksal uygulamanız çalışıyor ancak yinelenme dışında bir işlem gerçekleştirmiyordur. Şimdi, tetikleyici etkinleştirildiğinde gerçekleştirilecek bir eylem ekleyin.

## <a name="get-the-travel-time-for-a-route"></a>Bir rota için seyahat süresi alma

Şimdi bir tetikleyiciniz olduğuna göre artık iki yer arasındaki seyahat süresini alan bir [eylem](../logic-apps/logic-apps-overview.md#logic-app-concepts) ekleyebilirsiniz. Logic Apps, bu bilgileri kolayca alabilmeniz için Bing Haritalar API’si için bir bağlayıcı sağlar. Bu göreve başlamadan önce, bu öğreticinin önkoşullarında açıklandığı şekilde Bing Haritalar API anahtarına sahip olduğunuzdan emin olun.

1. Mantıksal uygulama Tasarımcısı ' nda, tetikleyicinizin altında **yeni adım**' ı seçin.

1. **Eylem seçin**altında **Standart**' ı seçin. Arama kutusuna filtreniz olarak "Bing haritalar" yazın ve **yol al** eylemini seçin.

   !["Yol al" eylemini seçin](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Bing Haritalar bağlantınız yoksa bir bağlantı oluşturmanız istenir. Bu bağlantı ayrıntılarını sağlayın ve **Oluştur**' u seçin.

   ![Bing Haritalar API 'sine bağlantı oluşturma](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Özellik | Gereklidir | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Bağlantı Adı** | Yes | BingMapsConnection | Bağlantınıza bir ad verin. Bu örnek "BingMapsConnection" kullanır. |
   | **API Anahtarı** | Yes | <*your-Bing-Maps-key*> | Daha önce aldığınız Bing Haritalar anahtarını girin. Bing Haritalar anahtarınız yoksa [nasıl anahtar alacağınızı](https://msdn.microsoft.com/library/ff428642.aspx) öğrenin. |
   |||||

1. Eylemi şu açıklama ile yeniden adlandırın: `Get route and travel time with traffic`

1. Eylemin içinde **yeni parametre Ekle listesini**açın ve eyleme eklemek için bu özellikleri seçin.

   * **İyileştir**
   * **Mesafe birimi**
   * **Seyahat modu**

   !["Yol al" eylemine özellikler ekleme](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Şimdi, aşağıda gösterildiği gibi eylemin özelliklerinin değerlerini ayarlayın ve burada açıklanmıştır.

   !["Yol al" eylemi için ayrıntıları sağlayın](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Özellik | Gereklidir | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Güzergah noktası 1** | Yes | <*start-location*> | Rotanızın başlangıç noktası |
   | **Güzergah noktası 2** | Yes | <*end-location*> | Rotanızın hedefi |
   | **İyileştir** | Hayır | timeWithTraffic | Rotanızı iyileştirmeye yönelik bir parametre; örneğin, mesafe, mevcut trafik ile seyahat süresi vb. "TimeWithTraffic" parametresini seçin. |
   | **Mesafe birimi** | Hayır | <*your-preference*> | Rotanız için mesafe birimi. Bu örnek birim olarak "mil" kullanır. |
   | **Seyahat modu** | Hayır | Sürüş | Rotanız için seyahat modu. "Itici" modunu seçin. |
   ||||

   Bu parametreler hakkında daha fazla bilgi için bkz. [Rota hesaplama](https://docs.microsoft.com/bingmaps/rest-services/routes/calculate-a-route).

1. Mantıksal uygulamanızı kaydedin.

Ardından, geçerli seyahat süresini saniyeler olarak değil, dakikalar olarak dönüştürebilmeniz ve depolayabilmeniz için bir değişken oluşturun. Böylece dönüştürmeyi yinelemekten kaçınabilir ve sonraki adımlarda değeri daha kolayca kullanabilirsiniz. 

## <a name="create-a-variable-to-store-travel-time"></a>Seyahat süresini depolamak için bir değişken oluşturma

Bazen iş akışınızda veriler üzerinde işlemler çalıştırmak ve sonra sonuçları daha sonra kullanmak isteyebilirsiniz. Kolayca bu sonuçları yeniden kullanabilmek veya bu sonuçlara başvurabilmek amacıyla sonuçları kaydetmek için, sonuçları işledikten sonra depolamak üzere değişkenler oluşturabilirsiniz. Yalnızca mantıksal uygulamanızın en üst düzeyinde değişkenler oluşturabilirsiniz.

Varsayılan olarak, önceki **yol al** eylemi, **seyahat süresi trafik** özelliğinden saniye cinsinden geçerli seyahat süresini döndürür. Bunun yerine bu değeri dönüştürüp depolayarak daha sonra yeniden dönüştürme olmadan değerin yeniden kullanımını kolaylaştırırsınız.

1. **Yol al** eyleminin altında **yeni adım**' ı seçin.

1. **Eylem seçin**altında, **yerleşik**' i seçin. Arama kutusuna "değişkenler" yazın ve **değişken Başlat** eylemini seçin.

   !["Değişken Başlat" eylemini seçin](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Bu eylemi şu açıklama ile yeniden adlandırın: `Create variable to store travel time`

1. Burada açıklandığı gibi değişkeniniz için ayrıntıları sağlayın:

   | Özellik | Gereklidir | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | **Adı** | Yes | travelTime | Değişkeninizin adı. Bu örnek "Seyahattime" kullanır. |
   | **Tür** | Yes | Tamsayı | Değişkeninizin veri türü |
   | **Değer** | Hayır| Geçerli seyahat süresini saniyelerden dakikalara dönüştüren bir ifade (bu tablonun altındaki adımlara bakın). | Değişkeninizin ilk değeri |
   ||||

   1. **Değer** özelliği için ifade oluşturmak üzere, dinamik içerik listesinin görünmesi için kutunun içine tıklayın. Gerekirse liste görüntüleninceye kadar tarayıcınızı genişletin. Dinamik içerik listesinde **ifade**' yi seçin.

      !["Değişken başlatma" eylemi için bilgi sağlayın](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Bazı düzenleme kutularının içine tıkladığınızda, dinamik içerik listesi görüntülenir. Bu liste, iş akışınızda giriş olarak kullanabileceğiniz önceki eylemlerin tüm özelliklerini gösterir. Dinamik içerik listesi, işlemleri çalıştırmak için işlevler seçebileceğiniz bir ifade düzenleyicisine sahiptir. Bu ifade düzenleyicisi yalnızca dinamik içerik listesinde görüntülenir.

   1. İfade düzenleyicisinde şu ifadeyi girin: `div(,60)`

      ![Şu ifadeyi girin: "div(,60)"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. İmlecinizi, sol parantez ( **(** ) ile virgül ( **,** ) arasındaki ifadeye getirin. 
   **dinamik içerik**' i seçin.

      ![Konum imleci, "dinamik içerik" i seçin](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Dinamik içerik listesinden **Seyahat Süresi Trafik** seçeneğini belirleyin.

      !["Seyahat süresi trafiği" özelliğini seçin](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Özellik değeri ifadenin içinde çözümlendikten sonra **Tamam**' ı seçin.

      ![İfadeyi oluşturmayı tamamlaması için "Tamam" ı seçin](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      **Value** özelliği artık burada gösterildiği gibi görünür:

      !["Value" özelliği çözümlenmiş ifadeyle görüntülenir](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Mantıksal uygulamanızı kaydedin.

Ardından, geçerli seyahat süresinin belirli bir sınırdan büyük olup olmadığını denetleyen bir koşul ekleyin.

## <a name="compare-the-travel-time-with-limit"></a>Seyahat süresini sınırla karşılaştırın

1. Önceki eylem altında **yeni adım**' ı seçin.

1. **Eylem seçin**altında, **yerleşik**' i seçin. Arama kutusuna filtreniz olarak "koşul" yazın. Eylemler listesinden **koşul** eylemini seçin.

   !["Koşul" eylemini seçin](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Koşulu şu açıklama ile yeniden adlandırın: `If travel time exceeds limit`

1. Burada açıklandığı gibi, **Seyahatsaati** Özellik değerinin belirtilen limiti aşıp aşmayacağını denetleyen bir koşul oluşturun:

   1. Koşulda, koşulun sol tarafındaki **bir değer seçin** kutusunun içine tıklayın.

   1. Görüntülenen dinamik içerik listesinden, **değişkenler**altında, **seyahattime** özelliğini seçin.

      ![Koşulun sol tarafını oluşturma](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. Orta karşılaştırma kutusunda, işleç ' **den büyüktür** ' i seçin.

   1. Koşulun sağ tarafındaki **bir değer seçin** kutusunda şu sınırı girin: `15`

      İşiniz bittiğinde, koşul şu örneğe benzer şekilde görünür:

      ![Seyahat süresini denetlemek için tamamlandı koşulu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Mantıksal uygulamanızı kaydedin.

Sonra, seyahat süresi sınırınızı aştığında çalıştırılacak eylemi ekleyin.

## <a name="send-email-when-limit-exceeded"></a>Sınır aşıldığında e-posta gönder

Şimdi seyahat süresi sınırınızı aştığında size e-posta gönderen bir eylem ekleyin. Bu e-posta, geçerli seyahat süresini ve belirtilen rotada seyahat etmek için gerekli ek süreyi içerir.

1. Koşulun **true ise** dalında **Eylem Ekle**' yi seçin.

1. **Eylem seçin**altında **Standart**' ı seçin. Arama kutusuna "e-posta gönder" yazın. Listede çok fazla sonuç döndürüldüğünden, öncelikle istediğiniz e-posta bağlayıcısını seçin, örneğin:

   ![İstediğiniz e-posta bağlayıcısını seçin](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Azure iş veya okul hesapları için **Office 365 Outlook** girişini seçin.
   * Kişisel Microsoft hesapları için **Outlook.com** girişini seçin.

1. Bağlayıcının eylemleri görüntülendiğinde, kullanmak istediğiniz "e-posta eylemi gönder" seçeneğini belirleyin, örneğin:

   !["E-posta gönder" eylemini seçin](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Önceden bir bağlantınız yoksa e-posta hesabınızda oturum açmanız istenir.

   Logic Apps, e-posta hesabınıza bir bağlantı oluşturur.

1. Eylemi şu açıklama ile yeniden adlandırın: `Send email with travel time`

1. **Alıcı** kutusuna alıcının e-posta adresini girin. Test amacıyla e-posta adresinizi kullanın.

1. **Konu** kutusunda e-postanın konusunu belirtin ve **travelTime** değişkenini dahil edin.

   1. Sonunda boşluk olacak şekilde `Current travel time (minutes):` metnini girin. 

   1. Dinamik içerik listesinde, **değişkenler**altında, **daha fazla göster**' i seçin.

      !["Seyahattime" değişkenini bul](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. **Seyahat saati** **değişkenler**altında görüntülendikten sonra, **seyahatsaat**' i seçin.

      ![Konu metnini ve seyahat süresini döndüren ifadeyi girin](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. **Gövde** kutusunda e-posta gövdesi için içeriği belirtin.

   1. Sonunda boşluk olacak şekilde `Add extra travel time (minutes):` metnini girin.

   1. Dinamik içerik listesinde **ifade**' yi seçin.

      ![E-posta gövdesi için ifade oluşturma](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. İfade düzenleyicisinde, sınırınızı aşan dakika sayısını hesaplayabilmeniz için şu ifadeyi girin: ```sub(,15)```

      ![Ek seyahat süresi dakikasını hesaplamak için ifade girin](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. İmlecinizi, sol parantez ( **(** ) ile virgül ( **,** ) arasındaki ifadeye getirin. **Dinamik içerik**' i seçin.

      ![Ek seyahat süresi dakikasını hesaplamak için ifade oluşturmaya devam edin](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. **Değişkenler** bölümünde **travelTime** seçeneğini belirleyin.

      ![İfadede kullanmak için "Seyahattime" özelliğini seçin](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Özellik ifadenin içinde çözümlendikten sonra **Tamam**' ı seçin.

      !["Body" özelliği çözümlendikten sonra "Tamam" ı seçin](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      **Body** özelliği şimdi burada gösterildiği gibi görünür:

      ![İfadede "Body" özelliği çözümlendi](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Mantıksal uygulamanızı kaydedin.

Ardından mantıksal uygulamanızı test edin; mantıksal uygulamanız şu örnek gibi görünür:

![Tamamlanmış örnek mantıksal uygulama iş akışı](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Mantıksal uygulamanızı çalıştırın

Mantıksal uygulamanızı el ile başlatmak için tasarımcı araç çubuğu çubuğunda **Çalıştır**' ı seçin.

* Geçerli seyahat süresi sınırınızda kalırsa, mantıksal uygulamanız başka bir şey yapmaz ve yeniden denetlemeden önce bir sonraki aralığı bekler. 

* Geçerli seyahat süresi sınırınızı aşarsa, geçerli seyahat süresi ve sınırınızı aşan dakika sayısı ile bir e-posta alırsınız. Mantıksal uygulamanızın gönderdiği örnek bir e-posta şöyledir:

![Seyahat süresini gösteren örnek bir e-posta gönderildi](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

E-posta gelmezse istenmeyen e-posta klasörüne bakın. E-postanızın istenmeyen posta filtresi bu tür postaları yeniden yönlendirebilir. Mantıksal uygulamanızın düzgün bir şekilde çalışıp çalışmadığından emin değilseniz bkz. [Mantıksal uygulama sorunlarını giderme](../logic-apps/logic-apps-diagnosing-failures.md).

Tebrikler, şimdi zamanlama tabanlı yinelenen bir mantıksal uygulama oluşturup çalıştırdınız. 

**Yinelenme** tetikleyicisini kullanan diğer mantıksal uygulamalar oluşturmak için, bir mantıksal uygulama oluşturduktan sonra bu şablonlara göz atın:

* Size gönderilen günlük anımsatıcıları alın.
* Eski Azure bloblarını silin.
* Azure Depolama kuyruğuna bir ileti gönderin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Örnek mantıksal uygulamaya artık ihtiyaç duymadığınızda, mantıksal uygulamanızı ve ilgili kaynakları içeren kaynak grubunu silin. 

1. Ana Azure menüsünde **Kaynak grupları**’na gidin ve mantıksal uygulamanızın kaynak grubunu seçin.

1. Kaynak grubu menüsünde **genel bakış** > **kaynak grubunu sil**' i seçin. 

   !["Genel Bakış" > "Kaynak grubunu sil"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Kaynak grubu adını onay olarak girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, belirtilen bir zamanlama (hafta içi sabahları) temelinde trafiği denetleyen ve seyahat süresi belirtilen bir sınırı aştığında eylem uygulayan (e-posta gönderen) bir mantıksal uygulama oluşturdunuz. Şimdi, Azure hizmetlerini, Microsoft hizmetlerini ve diğer SaaS uygulamalarını tümleştirerek posta listesi isteklerini onaya gönderen bir mantıksal uygulamanın nasıl oluşturulacağını öğreneceksiniz.

> [!div class="nextstepaction"]
> [Posta listesi isteklerini yönetme](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
