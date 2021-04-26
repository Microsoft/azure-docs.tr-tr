---
title: Azure AD 'de Log Analytics Sihirbazı 'nı yapılandırma | Microsoft Docs
description: Log Analytics 'i yapılandırmayı öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: markvi
author: MarkusVi
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca84fa57cb3a26337038275d1b7491154915c90e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574374"
---
# <a name="tutorial-configure-the-log-analytics-wizard"></a>Öğretici: Log Analytics Sihirbazı 'nı yapılandırma


Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Denetim ve oturum açma günlüklerinizi için bir Log Analytics çalışma alanı yapılandırma
> * Kusto sorgu dilini (KQL) kullanarak sorguları çalıştırma
> * Belirli bir hesap kullanıldığında uyarı gönderen bir uyarı kuralı oluşturma
> * Hızlı başlangıç şablonunu kullanarak özel çalışma kitabı oluşturma
> * Varolan çalışma kitabı şablonuna sorgu ekleme

## <a name="prerequisites"></a>Önkoşullar

- En az bir P1 lisanslı Yöneticisi olan bir Azure aboneliği. Azure aboneliğiniz yoksa, [ücretsiz deneme için kaydolabilirsiniz](https://azure.microsoft.com/free/).

- Azure AD kiracısı.

- Azure AD kiracısında genel yönetici veya güvenlik yöneticisi olan bir kullanıcı.


Bu makalelerle ilgili bilgi edinin:

- [Öğretici: Azure kaynağından kaynak günlüklerini toplayın ve çözümleyin](../../azure-monitor/essentials/tutorial-resource-logs.md)

- [Etkinlik günlüklerini Log Analytics ile tümleştirme](./howto-integrate-activity-logs-with-log-analytics.md)

- [Azure AD 'de acil durum erişim hesabını yönetme](../roles/security-emergency-access.md)

- [KQL hızlı başvurusu](/azure/data-explorer/kql-quick-reference)

- [Azure Izleyici çalışma kitapları](../../azure-monitor/visualize/workbooks-overview.md)



## <a name="configure-a-workspace"></a>Çalışma alanı yapılandırma 

Bu yordamda, denetim ve oturum açma günlüklerinizi için bir Log Analytics çalışma alanının nasıl yapılandırılacağı özetlenmektedir.
Bir Log Analytics çalışma alanının yapılandırılması iki ana adımdan oluşur:
 
1. Log Analytics çalışma alanı oluşturma
2. Tanılama ayarlarını ayarlama

**Çalışma alanını yapılandırmak için:** 


1. [Azure portalında](https://portal.azure.com) genel yönetici olarak oturum açın.

2. **Log Analytics çalışma alanlarını** arayın.

    ![Arama kaynakları Hizmetleri ve belgeleri](./media/tutorial-log-analytics-wizard/search-services.png)

3. Log Analytics çalışma alanları sayfasında **Ekle**' ye tıklayın.

    ![Ekran görüntüsü, Log Analytics çalışma alanları sayfasındaki Ekle düğmesini gösterir.](./media/tutorial-log-analytics-wizard/add.png)

4.  **Log Analytics çalışma alanı oluştur** sayfasında, aşağıdaki adımları uygulayın:

    ![Log analytics çalışma alanı oluşturma](./media/tutorial-log-analytics-wizard/create-log-analytics-workspace.png)

    1. Aboneliğinizi seçin.

    2. Kaynak grubunu seçin.
 
    3. **Ad** metin kutusuna bir ad yazın (ör.: MytestWorkspace1).

    4. Bölgenizi seçin.

5. **Gözden Geçir ve Oluştur**’a tıklayın.

    ![Gözden geçir ve oluştur](./media/tutorial-log-analytics-wizard/review-create.png)

6. **Oluştur** ' a tıklayın ve dağıtımın başarılı olmasını bekleyin. Yeni çalışma alanını görmek için sayfayı yenilemeniz gerekebilir.

    ![Oluştur](./media/tutorial-log-analytics-wizard/create-workspace.png)

7. **Azure Active Directory** aratın.

    ![Ekran görüntüsünde Azure Search 'te Azure Active Directory gösterilmektedir.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

8. **İzleme** bölümünde **Tanılama ayarı**' na tıklayın.

    ![Ekran görüntüsü, Izleme tarafından seçilen tanılama ayarlarını gösterir.](./media/tutorial-log-analytics-wizard/diagnostic-settings.png)

9. **Tanılama ayarları** sayfasında, **Tanılama ayarı Ekle**' ye tıklayın.

    ![Tanılama ayarı Ekle](./media/tutorial-log-analytics-wizard/add-diagnostic-setting.png)

10. **Tanılama ayarı** sayfasında, aşağıdaki adımları uygulayın:

    ![Tanılama ayarlarını seçin](./media/tutorial-log-analytics-wizard/select-diagnostics-settings.png)

    1. **Kategori ayrıntıları**' nın altında **Auditlogs** ve **signınlogs**' u seçin.

    2. **Hedef ayrıntıları** altında **Log Analytics gönder**' i seçin ve ardından yeni Log Analytics çalışma alanınızı seçin. 
   
    3. **Kaydet**’e tıklayın. 

## <a name="run-queries"></a>Sorgu çalıştırma  

Bu yordam, **kusto sorgu dili (KQL)** kullanılarak sorguların nasıl çalıştırılacağını gösterir.


**Bir sorgu çalıştırmak için:**


1. [Azure portalında](https://portal.azure.com) genel yönetici olarak oturum açın.

2. **Azure Active Directory** aratın.

    ![Ekran görüntüsünde Azure Search 'te Azure Active Directory gösterilmektedir.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. **İzleme** bölümünde **Günlükler**' e tıklayın.

4. **Günlükler** sayfasında, **kullanmaya başlayın**' a tıklayın.

5. **Arama* metin kutusuna sorgunuzu yazın.

6. **Çalıştır**'a tıklayın.  


### <a name="kql-query-examples"></a>KQL sorgu örnekleri

Giriş verilerinden 10 rastgele girdi alın:

`SigninLogs | take 10`

Koşullu erişimin başarılı olduğu oturum açma işlemlerini inceleyin

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus` 


Kaç başarı olduğunu say

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus | count`


Kullanıcıya göre başarılı oturum açma işlemlerinin toplam sayısı:

`SigninLogs | where ConditionalAccessStatus == "success" | summarize SuccessfulSign-ins = count() by UserDisplayName, bin(TimeGenerated, 1d)` 


Bir kullanıcının belirli bir dönemde belirli bir işlemi kaç kez kullandığını görüntüleyin:

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | summarize count() by OperationName, Identity`


İşlem adı üzerinde sonuçları özetleyin

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | project OperationName, Identity | evaluate pivot(OperationName)`


Bir iç birleşim kullanarak denetim ve oturum açma günlüklerini birlikte birleştirin:

`AuditLogs |where OperationName contains "Add User" |extend UserPrincipalName = tostring(TargetResources[0].userPrincipalName) | |project TimeGenerated, UserPrincipalName |join kind = inner (SigninLogs) on UserPrincipalName |summarize arg_min(TimeGenerated, *) by UserPrincipalName |extend SigninDate = TimeGenerated` 


İstemci uygulama türüne göre işaret sayısını görüntüle:

`SigninLogs | summarize count() by ClientAppUsed`

Oturum açma işlemlerini güne göre say:

`SigninLogs | summarize NumberOfEntries=count() by bin(TimeGenerated, 1d)`

5 rastgele giriş yapın ve sonuçlarda görmek istediğiniz sütunları projeye alın:

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `


En iyi 5 azalan sırada ve görmek istediğiniz sütunları Project 'e göre yapın

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `

Değerleri iki sütuna birleştirerek yeni bir sütun oluşturun:

`SigninLogs | limit 10 | extend RiskUser = strcat(RiskDetail, "-", Identity) | project RiskUser, ClientAppUsed`




## <a name="create-an-alert-rule"></a>Uyarı kuralı oluşturma  

Bu yordamda, breakcam hesabı kullanıldığında nasıl uyarı gönderileceğini gösterilmektedir.

**Bir uyarı kuralı oluşturmak için:**


1. [Azure portalında](https://portal.azure.com) genel yönetici olarak oturum açın.

2. **Azure Active Directory** aratın.

    ![Ekran görüntüsünde Azure Search 'te Azure Active Directory gösterilmektedir.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. **İzleme** bölümünde **Günlükler**' e tıklayın.

4. **Günlükler** sayfasında, **kullanmaya başlayın**' a tıklayın.

5. **Arama** metin kutusuna şunu yazın:`SigninLogs |where UserDisplayName contains "BreakGlass" | project UserDisplayName`

6. **Çalıştır**'a tıklayın.  

7. Araç çubuğunda **Yeni uyarı kuralı**' nı tıklatın.

    ![Yeni uyarı kuralı](./media/tutorial-log-analytics-wizard/new-alert-rule.png)

8. **Uyarı kuralı oluştur** sayfasında, kapsamın doğru olduğundan emin olun.

9. **Koşul** altında, **Ortalama özel günlük araması sayıdan büyük <logic undefined>** olduğunda öğesine tıklayın.

    ![Varsayılan koşul](./media/tutorial-log-analytics-wizard/default-condition.png)

10. **Sinyal mantığını Yapılandır** sayfasında, **Uyarı mantığı** bölümünde aşağıdaki adımları uygulayın:

    ![Uyarı mantığı](./media/tutorial-log-analytics-wizard/alert-logic.png)

    1. **Temelinde**, **sonuç sayısı**' nı seçin.

    2. As **işleci**, **büyüktür**' i seçin.

    3. **Eşik değeri** olarak **0**' ı seçin. 

11. **Sinyal mantığını Yapılandır** sayfasında, **değerlendirilen temel** bölümünde aşağıdaki adımları uygulayın:

    ![Temel alınarak değerlendirilen](./media/tutorial-log-analytics-wizard/evaluated-based-on.png)

    1. **Süre (dakika cinsinden)** olarak **5**' i seçin.

    2. **Sıklık (dakika)** olarak **5**' i seçin.

    3. **Bitti**’ye tıklayın. 

12. **Eylem grubu** altında **eylem grubu seç**' e tıklayın. 

    ![Eylem grubu](./media/tutorial-log-analytics-wizard/action-group.png)

13. **Bu uyarı kuralına iliştirilecek bir eylem grubu seçin** sayfasında, **eylem grubu oluştur**' a tıklayın. 

    ![Eylem grubu oluşturma](./media/tutorial-log-analytics-wizard/create-action-group.png)

14. **Eylem grubu oluştur** sayfasında, aşağıdaki adımları uygulayın:

    ![Örnek ayrıntıları](./media/tutorial-log-analytics-wizard/instance-details.png)

    1. **Eylem grubu adı** metin kutusuna **eylem grubumu** yazın.

    2. **Görünen ad** metin kutusuna **eylemin** yazın.

    3. **Gözden geçir ve oluştur**’a tıklayın. 

    4. **Oluştur**’a tıklayın.


15. **Özelleştirme eylemi** altında aşağıdaki adımları gerçekleştirin:

    ![Eylemleri özelleştirme](./media/tutorial-log-analytics-wizard/customize-actions.png)

    1. **E-posta konusu** seçin.

    2. **Konu satırı** metin kutusuna şunu yazın:`Breakglass account has been used`

16. **Uyarı kuralı ayrıntıları**' nın altında, aşağıdaki adımları uygulayın:

    ![Uyarı kuralı ayrıntıları](./media/tutorial-log-analytics-wizard/alert-rule-details.png)

    1. **Uyarı kuralı adı** metin kutusuna şunu yazın:`Breakglass account`

    2. **Açıklama** metin kutusuna şunu yazın:`Your emergency access account has been used`

17. **Uyarı kuralı oluştur**’a tıklayın.   


## <a name="create-a-custom-workbook"></a>Özel çalışma kitabı oluşturma

Bu yordamda, hızlı başlangıç şablonunu kullanarak nasıl yeni bir çalışma kitabı oluşturacağınız gösterilmektedir.




1. [Azure portalında](https://portal.azure.com) genel yönetici olarak oturum açın.

2. **Azure Active Directory** aratın.

    ![Ekran görüntüsünde Azure Search 'te Azure Active Directory gösterilmektedir.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. **İzleme** bölümünde, **çalışma kitapları**' nı tıklatın.

    ![Ekran görüntüsü Azure portal menüsündeki çalışma kitaplarının seçili olduğunu gösterir.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. **Hızlı başlangıç** bölümünde **boş**' a tıklayın.

    ![Hızlı başlangıç](./media/tutorial-log-analytics-wizard/quick-start.png)

5. **Ekle**'ye tıklayın.

    ![Çalışma kitabı Ekle](./media/tutorial-log-analytics-wizard/add-workbook.png)

6. **Metin ekle**' ye tıklayın.

    ![Metin ekle](./media/tutorial-log-analytics-wizard/add-text.png)


7. Metin kutusuna: yazın `# Client apps used in the past week` ve ardından **Düzenle**' ye tıklayın.

    ![Çalışma kitabı metni](./media/tutorial-log-analytics-wizard/workbook-text.png)

8. Yeni çalışma kitabında **Ekle**' ye ve ardından **Sorgu Ekle**' ye tıklayın.

    ![Sorgu Ekle](./media/tutorial-log-analytics-wizard/add-query.png)

9. Sorgu metin kutusuna şunu yazın: `SigninLogs | where TimeGenerated > ago(7d) | project TimeGenerated, UserDisplayName, ClientAppUsed | summarize count() by ClientAppUsed`

10. **Sorguyu Çalıştır**' a tıklayın.

    ![Ekran görüntüsü Sorguyu Çalıştır düğmesini gösterir.](./media/tutorial-log-analytics-wizard/run-workbook-query.png)

11. Araç çubuğunda, **görselleştirme** altında, **pasta grafiği**' ne tıklayın.

    ![Pasta grafiği](./media/tutorial-log-analytics-wizard/pie-chart.png)

12. **Düzenlemenizi bitti**' ye tıklayın.

    ![Düzenlenmek bitti](./media/tutorial-log-analytics-wizard/done-workbook-editing.png)



## <a name="add-a-query-to-a-workbook-template"></a>Çalışma kitabı şablonuna sorgu ekleme  

Bu yordam, var olan bir çalışma kitabı şablonuna nasıl sorgu ekleneceğini gösterir. Örnek, koşullu erişim başarısının hatalara dağıtımını gösteren bir sorguyu temel alır.


1. [Azure portalında](https://portal.azure.com) genel yönetici olarak oturum açın.

2. **Azure Active Directory** aratın.

    ![Ekran görüntüsünde Azure Search 'te Azure Active Directory gösterilmektedir.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. **İzleme** bölümünde, **çalışma kitapları**' nı tıklatın.

    ![Ekran görüntüsü, çalışma kitaplarının seçili olduğu menüdeki Izlemeyi gösterir.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. **Koşullu erişim** bölümünde, **koşullu erişim öngörüleri ve raporlama**' ya tıklayın.

    ![Ekran görüntüsü koşullu erişim öngörülerini ve raporlama seçeneğini gösterir.](./media/tutorial-log-analytics-wizard/conditional-access-template.png)

5. Araç çubuğunda **Düzenle**' ye tıklayın.

    ![Ekran görüntüsü Düzenle düğmesini gösterir.](./media/tutorial-log-analytics-wizard/edit-workbook-template.png)

6. Araç çubuğunda üç noktaya ve ardından **Ekle**' ye ve ardından **Sorgu Ekle**' ye tıklayın.

    ![Çalışma kitabı sorgusu Ekle](./media/tutorial-log-analytics-wizard/add-custom-workbook-query.png)

7. Sorgu metin kutusuna şunu yazın: `SigninLogs | where TimeGenerated > ago(20d) | where ConditionalAccessPolicies != "[]" | summarize dcount(UserDisplayName) by bin(TimeGenerated, 1d), ConditionalAccessStatus`

8. **Sorguyu Çalıştır**' a tıklayın.

    ![Ekran görüntüsü bu sorguyu çalıştırmak için sorguyu Çalıştır düğmesini gösterir.](./media/tutorial-log-analytics-wizard/run-workbook-insights-query.png)

9. **Zaman aralığı**' na tıklayın ve ardından **sorguda ayarla**' yı seçin.

10. **Görselleştirme**' e tıklayın ve ardından **çubuk grafik**' i seçin. 

11. Grafik başlığı olarak **Gelişmiş ayarlar**' a tıklayın, yazın `Conditional Access status over the last 20 days` ve ardından **Düzenle bitti**' ye tıklayın. 

    ![Grafik başlığını ayarla](./media/tutorial-log-analytics-wizard/set-chart-title.png)








## <a name="next-steps"></a>Sonraki adımlar

Azure portal kullanarak cihaz kimliklerini yönetmeyi öğrenmek için sonraki makaleye ilerleyin.
> [!div class="nextstepaction"]
> [İzleme](overview-monitoring.md)