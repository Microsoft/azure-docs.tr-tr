---
title: Azure faturalama ve maliyet yönetimi bütçe senaryosu | Microsoft Docs
description: Azure Otomasyonu’nu kullanarak VM’leri belirli bütçe eşiklerine göre kapatma hakkında bilgi edinin.
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
editor: ''
tags: billing
ms.assetid: db93f546-6b56-4b51-960d-1a5bf0274fc8
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: bc35ba74acb2ce807597230fee0685606a93a775
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958356"
---
# <a name="manage-costs-with-azure-budgets"></a>Azure Budgets ile maliyetleri yönetme

Maliyet denetimi, buluttaki yatırımınızın değerini en üst düzeye çıkarmak için kritik bir bileşendir. Maliyet görünürlüğü, raporlama ve maliyet tabanlı düzenlemenin devam eden işletme operasyonları için kritik öneme sahip olduğu çeşitli senaryolar vardır. [Azure Maliyet Yönetimi API’leri](https://docs.microsoft.com/rest/api/consumption/), bu senaryoların her birini desteklemek için bir dizi API sağlar. API’ler kullanım ayrıntılarını sağlayarak örnek düzeyinde maliyetlerinizi ayrıntılı olarak görüntülemenize olanak tanır.

Bütçeler genellikle maliyet denetiminin bir parçası olarak kullanılır. Bütçelerin kapsamı Azure’da belirlenebilir. Örneğin, bütçe görünümünüzü abonelik, kaynak grupları veya kaynak koleksiyonu temelinde daraltabilirsiniz. Bütçe eşiğine ulaşıldığında e-posta yoluyla bildirim almak için bütçeler API'sini kullanmanın yanı sıra, bütçe olayının sonucu olarak düzenlenmiş bir eylemler kümesini tetiklemek için [Azure İzleyici eylem gruplarını](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) kullanabilirsiniz.

Kritik olmayan iş yükü çalıştıran bir müşteriye yönelik yaygın bir bütçe senaryosu, müşteri, iş yükünü bütçeye göre yönetmek ve aynı zamanda aylık faturaya bakarken öngörülebilir bir maliyete ulaşmak istediğinde gerçekleşebilir. Bu senaryo, Azure ortamının bir parçası olan kaynakların maliyet tabanlı düzenlemesini gerektirir. Bu senaryoda, abonelik için 1000 ABD doları aylık bütçe ayarlanır. Ayrıca, bildirim eşikleri birkaç düzenlemeyi tetikleyecek şekilde ayarlanır. Bu senaryo, **İsteğe Bağlı** kaynak grubundaki tüm VM’leri durduracak olan %80’lik maliyet eşiği ile başlar. Ardından, %100 maliyet eşiğine ulaşıldığında tüm VM örnekleri durdurulur.
Bu senaryoyu yapılandırmak için, bu öğreticinin her bir bölümünde belirtilen adımları izleyerek aşağıdaki eylemleri tamamlayacaksınız.

Bu öğreticide yer alan eylemler şunları yapmanıza olanak sağlar:

- Web kancalarını kullanarak VM’leri durdurmak için bir Azure Otomasyonu Runbook'u oluşturma.
- Bütçe eşiği değerine göre tetiklenecek bir Azure mantıksal uygulaması oluşturma ve runbook’u doğru parametrelerle çağırma.
- Bütçe eşiğine ulaşıldığında Azure mantıksal uygulamasını tetikleyecek şekilde yapılandırılacak bir Azure İzleyici Eylem Grubu oluşturma.
- İstenen eşiklerle Azure bütçesini oluşturma ve eylem grubuna bağlama.

## <a name="create-an-azure-automation-runbook"></a>Azure Otomasyonu Runbook’u oluşturma

[Azure Otomasyonu](https://docs.microsoft.com/azure/automation/automation-intro), kaynak yönetimi görevlerinizin çoğu için betik oluşturmanıza ve bu görevleri zamanlanmış ya da isteğe bağlı olarak çalıştırmanıza olanak tanıyan bir hizmettir. Bu senaryonun bir parçası olarak, VM’leri durdurmak için kullanılacak bir [Azure Otomasyonu runbook'u](https://docs.microsoft.com/azure/automation/automation-runbook-types) oluşturacaksınız. Bu senaryoyu oluşturmak için [galerideki](https://docs.microsoft.com/azure/automation/automation-runbook-gallery) [Azure V2 VM’lerini Durdur](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) grafik runbook’unu kullanacaksınız. Bu runbook’u Azure hesabınıza aktarıp yayımlayarak, bir bütçe eşiğine ulaşıldığında VM’leri durdurabilirsiniz.

### <a name="create-an-azure-automation-account"></a>Azure Otomasyonu hesabı oluşturma

1. Azure hesabınızın kimlik bilgileriyle [Azure portalında](https://portal.azure.com/) oturum açın.
2. Azure’ın sol üst köşesinde bulunan **Kaynak oluştur** düğmesine tıklayın.
3. **Yönetim Araçları** > **Otomasyon**’u seçin.
   > [!NOTE]
   > Azure hesabınız yoksa [ücretsiz hesap](https://azure.microsoft.com/free/) oluşturabilirsiniz.
4. Hesap bilgilerinizi girin. Azure’da kimlik doğrulamasını kolaylaştırmak için gereken ayarları otomatik olarak etkinleştirmek üzere **Azure Farklı Çalıştır hesabı oluştur** alanında **Evet**’i seçin.
5. İşlemi tamamladığınızda Otomasyon hesabı dağıtımını başlatmak için **Oluştur**'a tıklayın.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Azure V2 VM’lerini Durdur runbook’unu içeri aktarma

[Azure Otomasyonu runbook’unu](https://docs.microsoft.com/azure/automation/automation-runbook-types) kullanarak, galeriden [Azure V2 VM’lerini Durdur](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) grafik runbook’unu içeri aktarın.

1.  Azure hesabınızın kimlik bilgileriyle [Azure portalında](https://portal.azure.com/) oturum açın.
2.  **Tüm hizmetler** > **Otomasyon Hesapları**’nı seçerek Otomasyon hesabınızı açın. Ardından Otomasyon Hesabınızı seçin.
3.  **İşlem Otomasyonu** bölümünden **Runbook galerisi**’ne tıklayın.
4.  **Galeri Kaynağı**’nı **Betik Merkezi** olarak ayarlayıp **Tamam**’ı seçin.
5.  Azure portalındaki [Azure V2 VM’lerini Durdur](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) galeri öğesini bulup seçin.
6.  **İçeri Aktar** düğmesine tıklayarak **İçeri Aktar** dikey penceresini görüntüleyin ve **Tamam**’ı seçin. Runbook’a genel bakış dikey penceresi görüntülenir.
7.  Runbook, içeri aktarma işlemini tamamladıktan sonra **Düzenle**’yi seçerek grafik runbook düzenleyicisini ve yayımlama seçeneğini görüntüleyin.

    ![Azure - Grafik runbook’u düzenleme](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Runbook’u yayımlamak için **Yayımla** düğmesine tıklayın ve sorulduğunda **Evet**’i seçin. Runbook yayımladığınızda, yayımlanmış herhangi bir sürümü taslak sürümle geçersiz kılarsınız. Örneğimizde, runbook’u siz oluşturduğunuz için yayımlanmış sürümünüz yoktur.

    Runbook yayımlama hakkında daha fazla bilgi için bkz. [Grafik runbook oluşturma](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Runbook için web kancaları oluşturma

[Azure V2 VM’lerini durdur](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) grafik runbook’unu kullanarak, tek bir HTTP isteği ile Azure Otomasyonu’nda runbook’u başlatmak için iki Web kancası oluşturacaksınız. İlk web kancası, parametre olarak kaynak grubu adını kullanarak %80 bütçe eşiğinde runbook’u çağırır ve isteğe bağlı VM’lerin durdurulmasına olanak tanır. Ardından, ikinci web kancası, runbook’u bir parametre olmadan (%100’de) çağırır, bu da kalan tüm VM örneklerini durdurur.

1. [Azure portalındaki](https://portal.azure.com/) **Runbook’lar** sayfasında, runbook’a genel bakış dikey penceresini gösteren **StopAzureV2Vm** runbook’una tıklayın.
2. Sayfanın üst kısmındaki **Web Kancası**’na tıklayarak **Web Kancası Ekle** dikey penceresini açın.
3. **Yeni web kancası oluştur**’a tıklayarak **Yeni web kancası oluştur** dikey penceresini açın.
4. Web kancasının **Ad** değerini **İsteğe Bağlı** olarak ayarlayın. **Etkin** özelliği **Evet** olmalıdır. **Süre sonu** değerinin değiştirilmesi gerekmez. Web kancası özellikleri hakkında daha fazla bilgi için bkz. [Web kancasının ayrıntıları](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook).
5. URL değerinin yanındaki Kopyala simgesine tıklayarak web kancası URL’sini kopyalayın.
   > [!IMPORTANT]
   > **İsteğe Bağlı** adlı web kancasının URL’sini güvenli bir yere kaydedin. URL’yi bu öğreticinin sonraki bölümlerinde kullanacaksınız. Güvenlik nedeniyle, web kancasını oluşturduktan sonra URL’yi tekrar görüntüleyemezsiniz veya alamazsınız.
6. Yeni web kancasını oluşturmak için **Tamam**’a tıklayın.
7. Runbook’un parametre değerlerini görüntülemek için **Parametreleri yapılandır ve ayarları çalıştır**’a tıklayın.
   > [!NOTE]
   > Runbook’un zorunlu parametreleri varsa, değer sağlanmaması durumunda web kancasını oluşturamazsınız.
8. Web kancası parametre değerlerini kabul etmek için **Tamam**’a tıklayın.
9. Web kancasını oluşturmak için **Oluştur**’a tıklayın.
10. Ardından, yukarıdaki adımları izleyerek **Tam** adlı ikinci bir web kancası oluşturun.
    > [!IMPORTANT]
    > Bu öğreticinin sonraki bölümlerinde kullanmak üzere her iki web kancası URL’sini de kaydettiğinizden emin olun. Güvenlik nedeniyle, web kancasını oluşturduktan sonra URL’yi tekrar görüntüleyemezsiniz veya alamazsınız.

Artık her birine, kaydettiğiniz URL’ler kullanılarak ulaşılabilen iki web kancanız yapılandırılmış olmalıdır.

![Web kancaları - İsteğe Bağlı ve Tam](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Azure Otomasyonu kurulumunu tamamladınız. Web kancalarının çalıştığını doğrulamak için basit bir Postman testi uygulayabilirsiniz. Daha sonra, düzenleme için mantıksal uygulamayı oluşturmanız gerekir.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Düzenleme için bir Azure mantıksal uygulaması oluşturma

Logic Apps, farklı kurum veya kuruluşlardaki uygulamaları, verileri, sistemleri ve hizmetleri tümleştirebilmeniz için işlemleri iş akışları olarak oluşturmanıza, zamanlamanıza ve otomatikleştirmenize yardımcı olur. Bu senaryoda, oluşturduğunuz [mantıksal uygulama](https://docs.microsoft.com/azure/logic-apps/) yalnızca oluşturduğunuz otomasyon web kancasını çağırmaktan biraz daha fazlasını yapacaktır.

Bütçeler, belirtilen bir eşiğe ulaşıldığında bildirim tetikleyecek şekilde ayarlanabilir. Bildirim almak için birden fazla eşik belirtebilirsiniz. Mantıksal uygulama, ulaşılan eşiğe göre farklı eylemler gerçekleştirmenize olanak tanır. Bu örnekte, iki bildirim alacağınız bir senaryo ayarlayacaksınız. İlk bildirim bütçenin %80’ine ulaşıldığında, ikinci bildirim ise bütçenin %100’üne ulaşıldığında gönderilir. Mantıksal uygulama, kaynak grubundaki tüm VM’leri kapatmak için kullanılacaktır. İlk olarak, %80’de **İsteğe Bağlı** eşiğine, daha sonra ise abonelikteki tüm VM’lerin kapatılacağı ikinci eşiğe ulaşılır.

Mantıksal uygulamalar, HTTP tetikleyicisi için örnek bir şema sağlamanıza olanak tanır ancak **Content-Type** üst bilgisini ayarlamanızı gerektirir. Eylem grubu, web kancası için özel üst bilgilere sahip olmadığından yükü ayrı bir adımda ayrıştırmanız gerekir. **Ayrıştır** eylemini kullanarak ve bir örnek yük sağlayacaksınız.

### <a name="create-the-logic-app"></a>Mantıksal uygulamayı oluşturma

Mantıksal uygulama çeşitli eylemler gerçekleştirir. Aşağıdaki liste, mantıksal uygulamanın gerçekleştireceği yüksek düzeyli bir dizi eylem sağlar:
- Bir HTTP isteği alındığında bunu algılar
- Ulaşılan eşik değerini belirlemek için geçirilen JSON verilerini ayrıştırma
- Eşik miktarının %100 veya daha yüksek olmayacak şekilde, bütçenin %80 veya daha yüksek bir oranına ulaşıp ulaşmadığını denetlemek için koşullu bir ifade kullanın.
    - Bu eşik miktarına ulaşıldıysa, **İsteğe Bağlı** adlı web kancasını kullanarak bir HTTP POST gönderin. Bu işlem "İsteğe Bağlı" grubundaki VM’leri kapatır.
- Eşik miktarına ulaşılıp ulaşılmadığını veya bütçe değerinin %100’ünün aşılıp aşılmadığını denetlemek için bir koşullu deyim kullanın.
    - Eşik miktarına ulaşıldıysa, **Tam** adlı web kancasını kullanarak bir HTTP POST gönderin. Bu eylem kalan tüm VM’leri kapatır.

Yukarıdaki adımları gerçekleştirecek mantıksal uygulamayı oluşturmak için aşağıdaki adımların uygulanması gerekir:

1.  [Azure portalında](https://portal.azure.com/) **Kaynak oluştur** > **Tümleştirme** > **Mantıksal Uygulama**’yı seçin.

    ![Azure - Mantıksal uygulama kaynağını seçme](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  **Mantıksal uygulama oluştur** dikey penceresinde, mantıksal uygulamanızı oluşturmak için gereken ayrıntıları sağlayın, **Panoya sabitle**’yi seçin ve **Oluştur**’a tıklayın.

    ![Azure - Mantıksal uygulama oluşturma](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Mantıksal uygulamanız Azure tarafından dağıtıldıktan sonra **Logic Apps Tasarımcısı** açılır ve bir tanıtım videosu ile sık kullanılan tetikleyicilerin bulunduğu bir dikey pencere görüntülenir.

### <a name="add-a-trigger"></a>Tetikleyici ekleme

Her mantıksal uygulama, belirli bir olay gerçekleştiğinde ya da belirli bir koşul karşılandığında tetiklenen bir tetikleyiciyle başlamalıdır. Tetikleyici her etkinleştirildiğinde Logic Apps altyapısı iş akışınızı başlatan ve çalıştıran bir mantıksal uygulama örneği oluşturur. Eylemler, tetikleyiciden sonra gerçekleşen tüm adımlardır.

1.  **Logic Apps Tasarımcısı** dikey penceresinin **Şablonlar** bölümünün altında **Boş Mantıksal Uygulama**’yı seçin.
2.  Bir [tetikleyici](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) eklemek için, **Logic Apps Tasarımcısı** arama kutusuna "http isteği" yazarak **İstek – Bir HTTP isteği alındığında** adlı tetikleyiciyi bulup seçin.

    ![Azure - Mantıksal uygulama - Http tetikleyicisi](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
3.  **Yeni adım** > **Eylem ekle**’yi seçin.

    ![Azure - Yeni adım - Eylem ekle](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
4.  **Logic Apps Tasarımcısı** arama kutusuna "JSON ayrıştırma" yazarak **Veri İşlemleri - JSON Ayrıştırma** [eylemini](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) bulup seçin.

    ![Azure - Mantıksal uygulama - JSON ayrıştırma eylemi ekleme](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
5.  JSON Ayrıştırma yükü için **İçerik** adı olarak "Yük" yazın veya dinamik içerikten "Gövde" etiketini kullanın.
6.  **JSON Ayrıştırma** kutusunda **Şema oluşturmak için örnek yük kullan** seçeneğini belirleyin.

    ![Azure - Mantıksal uygulama - Şema oluşturmak için örnek JSON verilerini kullanma](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
7.  Aşağıdaki JSON örnek yükünü metin kutusuna yapıştırın: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`

    Metin kutusu aşağıdaki gibi görünür:

    ![Azure - Mantıksal uygulama - Örnek JSON yükü](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
8.  **Bitti**’ye tıklayın.

### <a name="add-the-first-conditional-action"></a>İlk koşullu eylemi ekleme

Eşik miktarının %100 veya daha yüksek olmayacak şekilde, bütçenin %80 veya daha yüksek bir oranına ulaşıp ulaşmadığını denetlemek için koşullu bir ifade kullanın. Bu eşik miktarına ulaşıldıysa, **İsteğe Bağlı** adlı web kancasını kullanarak bir HTTP POST gönderin. Bu işlem **İsteğe Bağlı** grubundaki VM’leri kapatır.

1.  **Yeni adım** > **Koşul ekle**'yi seçin.

    ![Azure - Mantıksal uygulama - Koşul ekleme](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
2.  **Koşul** kutusunda **Değer seçin** ifadesini içeren metin kutusuna tıklayarak kullanılabilir değerlerin bir listesini görüntüleyin.

    ![Azure - Mantıksal uygulama - Koşul kutusu](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)

3.  Listenin en üstünde bulunan **İfade**’ye tıklayın ve ifade düzenleyicisine şu ifadeyi girin: `float()`

    ![Azure - Mantıksal uygulama - Float ifadesi](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)

4.  **Dinamik içerik**’i seçin, imleci parantez () içine yerleştirin ve tam ifadeyi doldurmak için listeden **NotificationThresholdAmount** öğesini seçin.

    İfade aşağıdaki gibi olacaktır:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  İfadeyi ayarlamak için **Tamam**’ı seçin.
6.  **Koşul** açılır kutusundaki **büyüktür veya eşittir** öğesini seçin.
7.  Koşulun **Değer seçin** kutusuna `.8` değerini girin.

    ![Azure - Mantıksal uygulama - Değeri olan Float ifadesi](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)

8.  Koşul kutusunda **Ekle** > **Satır ekle**’ye tıklayarak koşulun başka bir kısmını ekleyin.
9.  **Koşul** kutusunda **Değer seçin** ifadesini içeren metin kutusuna tıklayın.
10. Listenin en üstünde bulunan **İfade**’ye tıklayın ve ifade düzenleyicisine şu ifadeyi girin: `float()`
11. **Dinamik içerik**’i seçin, imleci parantez () içine yerleştirin ve tam ifadeyi doldurmak için listeden **NotificationThresholdAmount** öğesini seçin.
12. İfadeyi ayarlamak için **Tamam**’ı seçin.
13. **Koşul** açılır kutusunda **küçüktür**’ü seçin.
14. Koşulun **Değer seçin** kutusuna `1` değerini girin.

    ![Azure - Mantıksal uygulama - Değeri olan Float ifadesi](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)

15. **True ise** kutusunda **Eylem ekle** seçeneğini belirleyin. İsteğe bağlı VM’leri kapatacak bir HTTP POST eylemi ekleyeceksiniz.

    ![Azure - Mantıksal uygulama - Eylem ekleme](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)

16. HTTP eylemini aramak için **HTTP** yazın ve **HTTP – HTTP** eylemini seçin.

    ![Azure - Mantıksal uygulama - HTTP eylemi ekleme](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)

17. **Yöntem** değeri için **Post** seçeneğini belirleyin.
18. **Uri** değeri olarak, bu öğreticide daha önce oluşturduğunuz **İsteğe Bağlı** adlı web kancasının URL’sini girin.

    ![Azure - Mantıksal uygulama - HTTP eylemi URI’si](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)

19. **True ise** kutusunda **Eylem ekle** seçeneğini belirleyin. Alıcıya, isteğe bağlı VM’lerin kapatıldığını bildiren bir e-posta gönderecek olan e-posta eylemini ekleyeceksiniz.
20. "E-posta gönder" ifadesini arayın ve kullandığınız e-posta hizmetine bağlı olarak bir *e-posta gönder* eylemi seçin.

    ![Azure - Mantıksal uygulama - E-posta gönder eylemi](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    Kişisel Microsoft hesapları için **Outlook.com** girişini seçin. Azure iş veya okul hesapları için **Office 365 Outlook** girişini seçin. Önceden bir bağlantınız yoksa e-posta hesabınızda oturum açmanız istenir. Logic Apps, e-posta hesabınıza bir bağlantı oluşturur.

    Mantıksal uygulamanın e-posta bilgilerinize erişmesine izin vermeniz gerekir.

    ![Azure - Mantıksal uygulama - Erişim bildirimi](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)

21. Alıcıya, isteğe bağlı VM’lerin kapatıldığını bildiren e-postaya **Kime**, **Konu** ve **Gövde** metnini ekleyin. Konu ve gövde alanlarını doldurmak için **BudgetName** ve **NotificationThresholdAmount** dinamik içeriğini kullanın.

    ![Azure - Mantıksal uygulama - E-posta ayrıntıları](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>İkinci koşullu eylemi ekleme

Eşik miktarına ulaşılıp ulaşılmadığını veya bütçe değerinin %100’ünün aşılıp aşılmadığını denetlemek için bir koşullu deyim kullanın. Eşik miktarına ulaşıldıysa, **Tam** adlı web kancasını kullanarak bir HTTP POST gönderin. Bu eylem kalan tüm VM’leri kapatır.

1.  **Yeni adım** > **Koşul Ekle**'yi seçin.

    ![Azure - Mantıksal uygulama - Eylem ekleme](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)

2.  **Koşul** kutusunda **Değer seçin** ifadesini içeren metin kutusuna tıklayarak kullanılabilir değerlerin bir listesini görüntüleyin.
3.  Listenin en üstünde bulunan **İfade**’ye tıklayın ve ifade düzenleyicisine şu ifadeyi girin: `float()`
4.  **Dinamik içerik**’i seçin, imleci parantez () içine yerleştirin ve tam ifadeyi doldurmak için listeden **NotificationThresholdAmount** öğesini seçin.

    İfade aşağıdaki gibi olacaktır:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  İfadeyi ayarlamak için **Tamam**’ı seçin.
6.  **Koşul** açılır kutusundaki **büyüktür veya eşittir** öğesini seçin.
7.  Koşulun **Değer seçin** kutusuna `1` değerini girin.

    ![Azure - Mantıksal uygulama - Koşul değerini ayarlama](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)

8.  **True ise** kutusunda **Eylem ekle** seçeneğini belirleyin. Kalan tüm VM’leri kapatacak bir HTTP POST eylemi ekleyeceksiniz.

    ![Azure - Mantıksal uygulama - Eylem ekleme](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)

9.  HTTP eylemini aramak için **HTTP** yazın ve **HTTP – HTTP** eylemini seçin.
10. **Yöntem** değeri için **Post** seçeneğini belirleyin.
11. **Uri** değeri olarak, bu öğreticide daha önce oluşturduğunuz **Tam** adlı web kancasının URL’sini girin.

    ![Azure - Mantıksal uygulama - Eylem ekleme](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)

12. **True ise** kutusunda **Eylem ekle** seçeneğini belirleyin. Alıcıya, kalan VM’lerin kapatıldığını bildiren bir e-posta gönderecek olan e-posta eylemini ekleyeceksiniz.
13. "E-posta gönder" ifadesini arayın ve kullandığınız e-posta hizmetine bağlı olarak bir *e-posta gönder* eylemi seçin.
14. Alıcıya, isteğe bağlı VM’lerin kapatıldığını bildiren e-postaya **Kime**, **Konu** ve **Gövde** metnini ekleyin. Konu ve gövde alanlarını doldurmak için **BudgetName** ve **NotificationThresholdAmount** dinamik içeriğini kullanın.

    ![Azure - Mantıksal uygulama - E-posta gönderme ayrıntıları](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)

15. **Mantıksal Uygulama Tasarımcısı** dikey penceresinin en üstünde bulunan **Kaydet**’e tıklayın.

### <a name="logic-app-summary"></a>Mantıksal uygulama özeti

İşiniz bittiğinde mantıksal uygulamanız aşağıdaki gibi görünür. Eşik temelli düzenlemeye gerek duymadığınız en basit senaryolarda, otomasyon betiğini doğrudan **İzleyici**’den çağırabilir ve **Mantıksal Uygulama** adımını atlayabilirsiniz.

   ![Azure - Mantıksal uygulama - Tam görünüm](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Mantıksal uygulamanızı kaydettiğinizde, çağırabileceğiniz bir URL oluşturulmuştur. Bu URL’yi öğreticinin bir sonraki bölümünde kullanacaksınız.

## <a name="create-an-azure-monitor-action-group"></a>Azure İzleyici Eylem Grubu oluşturma

Eylem grubu, tanımladığınız bir bildirim tercihleri koleksiyonudur. Bir uyarı tetiklendiğinde, belirli bir eylem grubu bilgilendirilerek uyarıyı alabilir. Azure uyarısı, belirli koşullara göre ileriye dönük bir bildirim oluşturur ve eylem yapma fırsatı sağlar. Uyarıda, ölçümler ve günlükler de dahil olmak üzere birden çok kaynaktaki veriler kullanılabilir.

Eylem grupları, bütçenizle tümleştireceğiniz tek uç noktadır. Bildirimleri birkaç kanalda ayarlayabilirsiniz ancak bu senaryo için, daha önce bu öğreticide oluşturduğunuz mantıksal uygulamaya odaklanacaksınız.

### <a name="create-an-action-group-in-azure-monitor"></a>Azure İzleyici’de eylem grubu oluşturma

Eylem grubu oluştururken, bu öğreticide daha önce oluşturduğunuz mantıksal uygulamayı işaret edeceksiniz.

1.  [Azure portalında](https://portal.azure.com/) henüz oturum açmadıysanız oturum açın ve **Tüm hizmetler** > **İzleyici**’yi seçin.
2.  **Uyarılar**'ı ve ardından **Eylemleri yönet**'i seçin.
3.  **Eylem grupları** dikey penceresinden **Eylem grubu ekle**’yi seçin.
4.  Aşağıdaki öğeleri ekleyin ve doğrulayın:
    - Eylem grubu adı
    - Kısa ad
    - Abonelik
    - Kaynak grubu

    ![Azure - Mantıksal uygulama - Eylem grubu ekleme](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)

5.  **Eylem grubu ekle** bölmesinde bir LogicApp eylemi ekleyin. Eylemi **Budget-BudgetLA** olarak adlandırın. **Mantıksal Uygulama** bölmesinden **Abonelik** ve **Kaynak grubu**’nu seçin. Ardından, bu öğreticide daha önce oluşturduğunuz **mantıksal uygulamayı** seçin.
6.  Mantıksal uygulamayı ayarlamak için **Tamam**’a tıklayın. Ardından, eylem grubunu oluşturmak için **Eylem grubu oluştur** bölmesindeki **Tamam**’ı seçin.

Bütçenizi etkin bir şekilde düzenlemek için gereken tüm destekleyici bileşenleri tamamladınız. Şimdi tüm yapmanız gereken, bütçeyi oluşturup oluşturduğunuz eylem grubunu kullanacak şekilde yapılandırmaktır.

## <a name="create-the-azure-budget"></a>Azure Bütçesi Oluşturma

Maliyet Yönetimi’nde [Bütçe özelliğini](../cost-management/tutorial-acm-create-budgets.md) kullanarak Azure portalında bir bütçe oluşturabilirsiniz. Ya da REST API’leri, PowerShell cmdlet’leri veya CLI kullanarak bir bütçe oluşturabilirsiniz. Aşağıdaki yordamda REST API’si kullanılmıştır. REST API’sini çağırmadan önce bir yetkilendirme belirtecine ihtiyacınız olacaktır. Yetkilendirme belirteci oluşturmak için [ARMClient](https://github.com/projectkudu/ARMClient) projesini kullanabilirsiniz. **ARMClient**, Azure Resource Manager’da kimliğinizi doğrulamanıza ve API’leri çağırmak için bir belirteç almanıza olanak tanır.

### <a name="create-an-authentication-token"></a>Kimlik doğrulaması belirteci oluşturma

1.  GitHub üzerinde [ARMClient](https://github.com/projectkudu/ARMClient) projesine gidin.
2.  Yerel bir kopya almak için depoyu klonlayın.
3.  Projeyi Visual Studio’da açın ve derleyin.
4.  Derleme başarılı olduktan sonra yürütülebilir dosya *\bin\debug* klasöründe olmalıdır.
5.  ARMClient’ı çalıştırın. Bir komut istemi açın ve proje kökünden *\bin\debug* klasörüne gidin.
6.  Oturum açmak ve kimlik doğrulaması yapmak için komut istemine aşağıdaki komutu girin:<br>
    `ARMClient login prod`
7.  Çıktıdan **abonelik guid’si** değerini kopyalayın.
8.  Yetkilendirme belirtecini panonuza kopyalamak için komut istemine aşağıdaki komutu girin, ancak yukarıdaki adımda kopyaladığınız abonelik kimliğini kullandığınızdan emin olun: <br>
    `ARMClient token <subscription GUID from previous step>`

    Yukarıdaki adımı tamamladıktan sonra aşağıdakileri görürsünüz:<br>
    **Belirteç başarıyla panoya kopyalandı.**
9.  Bu öğreticinin sonraki bölümünde yer alan adımlar için kullanılacak belirteci kaydedin.

### <a name="create-the-budget"></a>Bütçe Oluşturma

Ardından, Azure Tüketim REST API’lerini çağırarak bütçe oluşturmak için **Postman**’i yapılandıracaksınız. Postman bir API Geliştirme ortamıdır. Ortamı ve koleksiyon dosyalarını Postman’e aktaracaksınız. Koleksiyon, Azure Tüketim REST API’lerini çağıran HTTP isteklerinin gruplandırılmış tanımlarını içerir. Ortam dosyası, koleksiyon tarafından kullanılan değişkenleri içerir.

1.  REST API’lerini yürütmek için [Postman REST istemcisini](https://www.getpostman.com/) indirip açın.
2.  Postman’de yeni bir istek oluşturun.

    ![Postman - Yeni istek oluşturma](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)

3.  Yeni isteği koleksiyon olarak kaydedin, böylece üzerinde hiçbir şey olmaz.

    ![Postman - Yeni isteği kaydetme](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)

4.  İsteği `Get` eyleminden `Put` eylemine değiştirin.
5.  `{subscriptionId}` değerini bu öğreticinin bir önceki bölümünde kullandığınız **Abonelik Kimliği** ile değiştirin. Ayrıca, URL’yi `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31` değeri olarak "SampleBudget" içerecek şekilde değiştirin
6.  Postman içindeki **Üst Bilgiler** sekmesini seçin.
7.  "Yetkilendirme" adlı yeni bir **Anahtar** ekleyin.
8.  **Değer**’i son bölümün sonundaki ArmClient kullanılarak oluşturulan belirtece ayarlayın.
9.  Postman içindeki **Gövde** sekmesini seçin.
10. **Ham** düğme seçeneğini belirleyin.
11. Metin kutusuna aşağıdaki örnek bütçe tanımını yapıştırın, ancak **subscriptionid**, **budgetname** ve **actiongroupname** parametrelerini abonelik kimliğiniz, bütçeniz için benzersiz bir ad ve hem URL’de hem de istek gövdesinde oluşturduğunuz eylem grubu adı ile değiştirmeniz gerekir:

    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {
                },
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [
                    ],
                    "contactRoles": [
                    ],
                    "contactGroups": [
                    "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}
                    ]
                },
            "Actual_EqualTo_100_Percent": {
                    "operator": "EqualTo",
                    "threshold": 100,
                    "contactGroups": [
                "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                }
            }
        }
    ```
12. İsteği göndermek için **Gönder**’e basın.

Artık [Bütçeler API](https://docs.microsoft.com/rest/api/consumption/budgets)'sini çağırmak için ihtiyacınız olan tüm öğelere sahipsiniz. Bütçeler API başvurusu, aşağıdakiler de dahil olmak üzere belirli istekler hakkında ek ayrıntılar içerir:
    - **budgetName**: Birden çok bütçe desteklenir.  Bütçe adları benzersiz olmalıdır.
    - **category**: **Maliyet** veya **Kullanım** olmalıdır. API hem maliyet hem de kullanım bütçelerini destekler.
    - **timeGrain**: Aylık, üç aylık veya yıllık bütçe. Süre sonunda miktar sıfırlanır.
    - **filters**: Filtreler, bütçeyi seçili kapsam içindeki belirli bir kaynak kümesine daraltmanıza olanak tanır. Örneğin, filtre, abonelik düzeyindeki bir bütçe için kaynak grupları koleksiyonu olabilir.
    - **notifications**: Bildirim ayrıntılarını ve eşiklerini belirler. Birden çok eşik ayarlayabilir ve bildirim almak için bir e-posta adresi veya bir eylem grubu belirtebilirsiniz.

## <a name="summary"></a>Özet

Bu öğreticiyi takip ederek şunları öğrendiniz:
- VM’leri durdurmak için Azure Otomasyonu Runbook’u oluşturma.
- Bütçe eşiği değerlerine göre tetiklenecek bir Azure mantıksal uygulaması oluşturma ve runbook’u doğru parametrelerle çağırma.
- Bütçe eşiğine ulaşıldığında Azure mantıksal uygulamasını tetikleyecek şekilde yapılandırılmış bir Azure İzleyici Eylem Grubu oluşturma.
- İstenen eşiklerle Azure bütçesini oluşturma ve eylem grubuna bağlama.

Artık aboneliğiniz için, yapılandırılmış bütçe eşiklerinize ulaştığınızda sanal makinelerinizi kapatacak tamamen çalışan bir bütçeniz var.

## <a name="next-steps"></a>Sonraki adımlar

- Azure faturalama senaryoları hakkında daha fazla bilgi için bkz. [Faturalandırma ve maliyet yönetimi otomasyonu senaryoları](billing-cost-management-automation-scenarios.md).
