---
title: "Hızlı Başlangıç: Azure Sentinel'i kullanmaya başlama"
description: Azure Sentinel kullanarak ortamınızda neler olduğunu hızlı bir şekilde görüntülemeyi ve izlemeyi öğrenmek için bu hızlı başlangıcı kullanın.
services: sentinel
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: c6c99dc08c701116fa8bc522128f625c69bab4f5
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063857"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Hızlı Başlangıç: Azure Sentinel'i kullanmaya başlama




Bu hızlı başlangıçta, Azure Sentinel kullanarak ortamınızda neler olduğunu nasıl görüntüleyebileceğinizi ve izleyebileceğinizi öğreneceksiniz. Veri kaynaklarınızı Azure Sentinel 'e bağladıktan sonra, tüm bağlı veri kaynaklarınızda neler olduğunu bilmeniz için verilerin anında görselleştirilmesini ve analizini edinirsiniz. Azure Sentinel size, Azure 'da zaten sunulan araçların tam gücünden ve günlüklerinizi ve sorgulara yönelik analiz sağlamak üzere yerleşik olarak bulunan tabloları ve grafikleri sağlayan çalışma kitapları sağlar. Yerleşik çalışma kitaplarını kullanabilir veya yeni bir çalışma kitabını, sıfırdan veya var olan bir çalışma kitabına göre kolayca oluşturabilirsiniz. 

## <a name="get-visualization"></a>Görselleştirmeyi al

Ortamınızda neler olduğunu görselleştirmek ve analiz etmek için öncelikle, kuruluşunuzun güvenlik duruşunu görmek üzere genel bakış panosuna göz atın. Bu kutucukların her öğesine tıklayarak oluşturuldukları ham verilerin detayına gidebilirsiniz. Azure Sentinel, paraziti azaltmanıza ve inceleyecek ve araştırmanız gereken uyarı sayısını en aza indirmenize yardımcı olmak için, uyarıları olaylar ile ilişkilendirmek için bir Fusion tekniği kullanır. **Olaylar** , birlikte araştırıp çözebileceğiniz bir işlem yapılabilir olay oluşturan ilgili uyarı gruplarıdır.

- Azure portal Azure Sentinel ' i seçin ve ardından izlemek istediğiniz çalışma alanını seçin.

  ![Azure Sentinel'e genel bakış](./media/qs-get-visibility/overview.png)

- Üstteki araç çubuğu, belirlenen zaman dilimine göre kaç olay olduğunu size bildirir ve bunu önceki 24 saat ile karşılaştırır. Araç çubuğu, bu olayları, tetiklenen uyarıları (küçük sayı, son 24 saat içindeki değişikliği temsil eder) ve ardından bu olayları, ne kadar açık, devam ediyor ve kapalı olduğunu bildirir. Olay sayısında önemli bir artış veya bırakma olmadığını kontrol edin. Bir bırakma işlemi varsa, bağlantı Azure Sentinel 'e raporlamayı durdurdu olabilir. Bir artış varsa şüpheli bir sorun oluşmuş olabilir. Yeni uyarılar olup olmadığını kontrol edin.

   ![Azure Sentinel sayaçları](./media/qs-get-visibility/funnel.png)

Genel Bakış sayfasının ana gövdesi, çalışma alanınızın güvenlik durumunu gösteren bir bakışta fikir verir:

- **Zaman Içindeki olaylar ve uyarılar**: olay sayısını ve bu olaylardan kaç uyarı oluşturulduğunu listeler. Olağan dışı bir ani artış görürseniz, bunun için uyarıları görmeniz gerekir. olaylarda ani bir artış olduğunda ancak uyarı görmezseniz, sorun oluşması gerekebilir.

- **Olası kötü amaçlı olaylar**: kötü amaçlı oldukları bilinen kaynaklardan trafik algılandığında, Azure Sentinel, haritada sizi uyarır. Turuncu görürseniz, bu gelen trafiktir: birisi kuruluşunuza bilinen bir kötü amaçlı IP adresinden erişmeyi deniyor. Giden (kırmızı) etkinlik görürseniz, ağınızdaki verilerin kuruluşunuzun dışında, bilinen bir kötü amaçlı IP adresine akışını sağlarsınız.

   ![Kötü amaçlı trafik Haritası](./media/qs-get-visibility/map.png)

- **Son olaylar**: son olaylarınızı, bunların önem derecesini ve olayla ilişkili uyarı sayısını görüntülemek için. Belirli bir uyarı türünde ani bir tepe görürseniz, çalışmakta olan etkin bir saldırı olduğu anlamına gelebilir. Örneğin, kimlik için Microsoft Defender 'dan (eski adıyla Azure ATP) en fazla 20 geçiş karma olay uygulamanız varsa, birisi şu anda sizi saldırılara çalışıyor olabilir.

- **Veri kaynağı Anomali**: Microsoft 'un veri analistleri için veri kaynaklarınızdan verileri sürekli olarak arama modellerini oluşturdu. Herhangi bir sorun yoksa, hiçbir şey görüntülenmez. Bozukluklar algılanırsa, ne olduğunu görmek için bunlara ayrıntılı bir bakış uygulamanız gerekir. Örneğin, Azure 'da ani artış ' a tıklayın. Ne zaman meydana geldiğini görmek için **grafiğe** tıklayabilir ve sonra ne olduğunu görmek için o zaman diliminde gerçekleşen etkinliklere filtre uygulayabilirsiniz.

   ![Anormal veri kaynakları](./media/qs-get-visibility/anomolies.png)

## <a name="use-built-in-workbooks"></a>Yerleşik çalışma kitaplarını kullanma<a name="dashboards"></a>

Yerleşik çalışma kitapları, bu hizmetlerde oluşturulan olaylara derinlemesine bir bakış sağlamak için bağlı veri kaynaklarınızdan tümleşik veriler sağlar. Yerleşik çalışma kitapları arasında Azure AD, Azure etkinlik olayları ve şirket içi, Windows olaylarına göre güvenlik duvarı trafik günlükleri, Office 365 ve güvenli olmayan protokoller dahil olmak üzere tüm üçüncü taraflardan sunuculardan gelen Windows olaylarından veri olabilir. Çalışma kitapları, kendi çalışma kitabınızı tasarlarken gelişmiş özelleştirme ve esneklik sağlamak için Azure Izleyici çalışma kitaplarını temel alır. Daha fazla bilgi için bkz. [çalışma kitapları](../azure-monitor/visualize/workbooks-overview.md).

1. **Ayarlar** altında **çalışma kitapları**' nı seçin. **Yüklü**' ın altında, tüm yüklü çalışma kitabınızı görebilirsiniz. **Tümü**'nün altında yüklenebilecek tüm yerleşik çalışma kitaplarını içeren galeriyi görebilirsiniz. 
2. Tüm liste ve her bir tekliflerinin açıklamasını görmek için belirli bir çalışma kitabı arayın. 
3. Azure AD 'yi kullandığınız varsayılarak, Azure Sentinel ile çalışmaya başlayın ve en azından aşağıdaki çalışma kitaplarını yüklemenizi öneririz:
   - **Azure AD**: aşağıdakilerden birini veya her ikisini kullanın:
       - **Azure AD oturum** açma işlemleri zaman içinde oturum açma işlemlerini çözümleyerek, bozukluklar olup olmadığını görebilir. Bu çalışma kitapları uygulamalar, cihazlar ve konumlar tarafından başarısız oturum açma işlemleri sunarak, olağan dışı bir durum meydana olursa bir bakışta fark edebilirsiniz. Birden çok başarısız oturum açma ile ilgilenmeyi ödeyin. 
       - **Azure AD denetim günlükleri** , kullanıcılar üzerindeki değişiklikler (ekleme, kaldırma, vs.), Grup oluşturma ve değişiklikler gibi yönetici etkinliklerini analiz eder.  

   - Güvenlik duvarınız için bir çalışma kitabı ekleyin. Örneğin, Palo Alto Alto çalışma kitabını ekleyin. Çalışma kitabı, güvenlik duvarı verileriniz ile tehdit olayları arasında bağıntılar sunarak ve varlıklar genelinde şüpheli olayları vurgulamaktadır. Çalışma kitapları, trafikte eğilimleri hakkında bilgi sağlar ve sonuçların ayrıntılarına inmenize ve sonuçları filtrelemenize imkan sağlar. 

      ![Palo Alto panosu](./media/qs-get-visibility/palo-alto-week-query.png)


Ana sorgu sorgusu düzenleme düğmesini düzenleyerek çalışma kitaplarını özelleştirebilirsiniz ![ ](./media/qs-get-visibility/edit-query-button.png) . ![ ](./media/qs-get-visibility/go-to-la-button.png) [Sorguyu düzenlemek için Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)gitmek üzere düğme Log Analytics düğmesine tıklayabilir ve üç nokta (...) simgesini seçip, ana saat filtresini düzenlemenizi veya belirli kutucukları çalışma kitabından kaldırmanızı sağlayan **kutucuk verilerini Özelleştir**' i seçebilirsiniz.

Sorgularla çalışma hakkında daha fazla bilgi için bkz [. Öğretici: Log Analytics Içindeki görsel veriler](../azure-monitor/visualize/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Yeni kutucuk Ekle

Yeni bir kutucuk eklemek istiyorsanız, onu oluşturduğunuz veya Azure Sentinel yerleşik çalışma kitabının bulunduğu mevcut bir çalışma kitabına ekleyebilirsiniz. 
1. Log Analytics, [öğretici: Log Analytics Içindeki görsel veriler](../azure-monitor/visualize/tutorial-logs-dashboards.md)' de bulunan yönergeleri kullanarak bir kutucuk oluşturun. 
2. Kutucuk oluşturulduktan sonra, **PIN** altında kutucuğun görünmesini istediğiniz çalışma kitabını seçin.

## <a name="create-new-workbooks"></a>Yeni çalışma kitapları oluşturma
Sıfırdan yeni bir çalışma kitabı oluşturabilir veya yeni çalışma kitabınız için temel olarak yerleşik çalışma kitabını kullanabilirsiniz.

1. Sıfırdan yeni bir çalışma kitabı oluşturmak için **çalışma kitapları** ' nı ve ardından **+ Yeni çalışma kitabı**' nı seçin.
2. Çalışma kitabının oluşturulduğu aboneliği seçin ve açıklayıcı bir ad verin. Her çalışma kitabı, birbirleriyle aynı şekilde bir Azure kaynağıdır ve kimlerin erişebileceğini tanımlamak ve sınırlamak için BT rolleri (Azure RBAC) atayabilirsiniz. 
3. Görselleştirmeleri ' a sabitlemek üzere çalışma kitaplarında görünmesini sağlamak için bunu paylaşmanız gerekir. **Paylaşma** ' ya ve ardından **Kullanıcıları Yönet**' e tıklayın. 
 
1. Diğer tüm Azure kaynakları için yaptığınız gibi erişim ve **rol atamalarını** **Denetle** öğesini kullanın. Daha fazla bilgi için bkz. Azure [RBAC kullanarak Azure çalışma kitaplarını paylaşma](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-workbook-examples"></a>Yeni çalışma kitabı örnekleri

Aşağıdaki örnek sorgu, trafik eğilimlerini haftalar içinde karşılaştırmanızı sağlar. Sorguyu çalıştırdığınız cihaz satıcısını ve veri kaynağını kolayca geçirebilirsiniz. Bu örnek, Windows 'da SecurityEvent kullanır, diğer tüm güvenlik duvarında AzureActivity veya CommonSecurityLog üzerinde çalışacak şekilde geçiş yapabilirsiniz.

```console
// week over week query
SecurityEvent
| where TimeGenerated > ago(14d)
| summarize count() by bin(TimeGenerated, 1d)
| extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)
```

Katları kaynaklardan veri içeren bir sorgu oluşturmak isteyebilirsiniz. Yeni oluşturulan yeni kullanıcılar için Azure Active Directory Denetim günlüklerine bakar ve sonra kullanıcının, 24 saat içinde rol ataması değişikliği yapıp başlatmadığını görmek için Azure günlüklerinizi denetleyerek bir sorgu oluşturabilirsiniz. Bu şüpheli etkinlik bu panoda görünür:

```console
AuditLogs
| where OperationName == "Add user"
| project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
| join (AzureActivity
| where OperationName == "Create role assignment"
| project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
| project-away user1
```

Verilere ve ne aradıklarını arayan kişilerin rolüne göre farklı çalışma kitapları oluşturabilirsiniz. Örneğin, ağ yöneticiniz için güvenlik duvarı verilerini içeren bir çalışma kitabı oluşturabilirsiniz. Ayrıca, günlük olarak gözden geçirmek istediğiniz bir şey olup olmadığı ve bir saatte bir kez kontrol etmek istediğiniz diğer öğelerin, örneğin, her saat için Azure AD oturum açma bilgilerinizi aramak istediğinizde, her seferinde bakmak istediğiniz zaman çalışma kitapları da oluşturabilirsiniz. 

## <a name="create-new-detections"></a>Yeni algılamalar oluştur

Kuruluşunuzdaki tehditleri araştırmak için [Azure Sentinel 'e bağladığınız veri kaynaklarında](connect-data-sources.md) algılamalar oluşturun.

Yeni bir algılama oluşturduğunuzda, Microsoft güvenlik araştırmacıları tarafından, bağlandığınız veri kaynaklarına uyarlanmış yerleşik algılamalardan yararlanın.

Kullanıma hazır tüm algılamaları görüntülemek için analizler **' e ve ardından** **kural şablonları**' na gidin. Bu sekme, tüm Azure Sentinel yerleşik kurallarını içerir.

   ![Azure Sentinel ile tehditleri bulmak için yerleşik algılamaları kullanın](media/tutorial-detect-built-in/view-oob-detections.png)

Kullanıma hazır algılamaları alma hakkında daha fazla bilgi için bkz. [öğretici: yerleşik analiz alma](tutorial-detect-threats-built-in.md).
 
## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta, Azure Sentinel 'i kullanmaya nasıl başladığınıza öğrendiniz. [Tehditleri algılayarak](tutorial-detect-threats-built-in.md)ilgili öğreticiye devam edin.
> [!div class="nextstepaction"]
> Tehditlere yönelik yanıtlarınızı otomatik hale getirmek için [özel tehdit algılama kuralları oluşturun](tutorial-detect-threats-custom.md) .
