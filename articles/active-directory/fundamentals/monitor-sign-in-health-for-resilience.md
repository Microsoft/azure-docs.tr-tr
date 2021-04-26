---
title: Azure Active Directory 'de esnekliği için uygulama oturum açma durumunu izleme
description: Uygulamalarınızın oturum açma durumunu izlemek için sorgular ve bildirimler oluşturun.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7c32940d5fc40249257d1d0bb38ef67c12dc2ef
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029697"
---
# <a name="monitoring-application-sign-in-health-for-resilience"></a>Esnekliği için uygulama oturum açma durumunu izleme

Altyapı esnekliği artırmak için, önemli uygulamalarınız için uygulama oturum açma durumunu izlemeyi ayarlayarak, etkileyen bir olay oluşursa bir uyarı alırsınız. Bu çabayla ilgili size yardımcı olmak için, oturum açma sistem durumu çalışma kitabına göre uyarıları yapılandırabilirsiniz. 

Bu çalışma kitabı, yöneticilerin kiracınızdaki uygulamalar için kimlik doğrulama isteklerini izlemesini sağlar. Aşağıdaki temel özellikleri sağlar:

* Çalışma kitabını, neredeyse gerçek zamanlı verilere sahip tüm veya bağımsız uygulamaları izlemek üzere yapılandırın.

* İnceleme ve işlem yapmak için kimlik doğrulama desenleri değiştiğinde sizi bilgilendirmek üzere uyarılar yapılandırın.

* Çalışma kitabının varsayılan ayarı olan hafta boyunca haftaya yönelik eğilimleri karşılaştırın.

> [!NOTE]
> Tüm kullanılabilir çalışma kitaplarını ve bunları kullanmaya yönelik önkoşulları görmek için lütfen bkz. [Azure izleyici çalışma kitaplarını raporlar için kullanma](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

Etkileyen bir olay sırasında iki şey meydana gelebilir:

* Kullanıcılar oturum açabilmeniz için, bir uygulama için oturum açma işlemlerinin sayısı precipitously bırakabilir.

* Oturum açma hatalarının sayısı artabilir. 

Bu makalede, kullanıcılarınızın oturum açma işlemlerinde kesintiler izlemek için oturum açma sistem durumu çalışma kitabını ayarlama işlemi adım adım gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

* Azure AD kiracısı.

* Azure AD kiracısı için genel yönetici veya güvenlik yöneticisi rolüne sahip bir kullanıcı.

* Azure Izleyici günlüklerine Günlükler göndermek için Azure aboneliğinizdeki bir Log Analytics çalışma alanı. 

   * [Log Analytics çalışma alanı oluşturmayı](../../azure-monitor/logs/quick-create-workspace.md) öğrenin

* Azure Izleyici günlükleri ile tümleştirilmiş Azure AD günlükleri

   * Azure [ad oturum açma günlüklerini Azure Izleyici akışı Ile tümleştirmeyi öğrenin.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="configure-the-app-sign-in-health-workbook"></a>Uygulama oturum açma durumu çalışma kitabını yapılandırma 

Çalışma kitaplarına erişmek için **Azure Portal** açın, **Azure Active Directory**' i seçin ve ardından **çalışma kitapları**' nı seçin.

Kullanım, koşullu erişim ve sorun giderme altındaki çalışma kitaplarını görürsünüz. Uygulama oturum açma durumu çalışma kitabı kullanım bölümünde görüntülenir.

Çalışma kitabını kullandığınızda, son değiştirilen çalışma kitapları bölümünde görünebilir.

![Azure portal çalışma kitabı galerinin gösterildiği ekran görüntüsü.](./media/monitor-sign-in-health-for-resilience/sign-in-health-workbook.png)


Uygulama oturum açma sistem durumu çalışma kitabında, oturum açma işlemleri ile neler olduğunu görselleştirmenize olanak sağlar. 

Çalışma kitabı varsayılan olarak iki grafik gösterir. Bu grafikler, artık uygulamalarınız için neler olduğunu ve bir hafta önce aynı döneme karşı karşılaştırın. Mavi çizgiler geçerli ve turuncu çizgiler önceki haftadır.

![Oturum açma sistem durumu grafiklerini gösteren ekran görüntüsü.](./media/monitor-sign-in-health-for-resilience/sign-in-health-graphs.png)

**İlk grafik saatlik kullanımdır (başarılı Kullanıcı sayısı)**. Geçerli başarılı kullanıcı sayısını tipik bir kullanım süresine göre karşılaştırmak, bir kullanıma araştırma gerektirebilecek kullanımda olan bir bırakma oluşturmanıza yardımcı olur. Kullanım oranının başarıyla düşürülmesi, hata oranının performans ve kullanım sorunlarını tespit etmenize yardımcı olabilir. Örneğin, kullanıcılar oturum açmayı denemek için uygulamanıza ulaşamadıysanız, yalnızca kullanımda olan bir başarısızlık olmaz. Bu veriler için örnek sorgu aşağıdaki bölümde bulunabilir.

**İkinci grafik saat hatası oranına** sahiptir. Hata oranı 'nda ani artış, kimlik doğrulama mekanizmalarıyla ilgili bir sorun olduğunu gösterebilir. Hata oranı yalnızca kullanıcıların kimlik doğrulamaya çalışabilmesi durumunda ölçülebilir. Kullanıcılar, denemesi yapmak için erişim Sağlayamayabiliyorsa, başarısızlıklar gösterilmez.

Kullanım veya başarısızlık oranı belirtilen eşiği aştığında belirli bir gruba bildirimde bulunan bir uyarı yapılandırabilirsiniz. Bu veriler için örnek sorgu aşağıdaki bölümde bulunabilir.

## <a name="configure-the-query-and-alerts"></a>Sorgu ve Uyarıları yapılandırma

Azure Izleyici 'de uyarı kuralları oluşturur ve düzenli aralıklarla otomatik olarak kaydedilmiş sorguları veya özel günlük aramalarını çalıştırabilir.

Grafiklerde yansıtılan sorgulara göre e-posta uyarıları oluşturmak için aşağıdaki yönergeleri kullanın. Aşağıdaki örnek betikler, şu durumlarda bir e-posta bildirimi gönderir

* başarılı kullanım, önceki bölümde saatlik kullanım grafiğinde olduğu gibi, aynı saat iki gün önce %90 oranında düştir. 

* hata oranı, önceki bölümde yer aldığı saatlik başarısızlık oranı grafiğinde olduğu gibi, iki günden daha önce %90 oranında artar. 

 Temel alınan sorguyu yapılandırmak ve uyarıları ayarlamak için aşağıdaki adımları izleyin. Yapılandırma için temel olarak örnek sorgu kullanacaksınız. Bu bölümün sonunda sorgu yapısının bir açıklaması görüntülenir.

Azure Izleyici kullanarak günlük uyarılarını oluşturma, görüntüleme ve yönetme hakkında daha fazla bilgi için bkz. [günlük uyarılarını yönetme](../../azure-monitor/alerts/alerts-log.md).

1. Çalışma kitabında **Düzenle**' yi seçin, sonra grafiğin sağ tarafındaki **Sorgu simgesini** seçin.   

   [![Düzenleme çalışma kitabını gösteren ekran görüntüsü.](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)

   Sorgu günlüğü açılır.

   [![Sorgu günlüğünü gösteren ekran görüntüsü.](./media/monitor-sign-in-health-for-resilience/query-log.png)](./media/monitor-sign-in-health-for-resilience/query-log.png)
‎

2. Yeni bir kusto sorgusu için örnek betiklerin birini kopyalayın.  
   * [Hata oranı artışı için kusto sorgusu](#kusto-query-for-increase-in-failure-rate)
   * [Kullanımda bırakma için kusto sorgusu](#kusto-query-for-drop-in-usage)

3. Sorguyu pencereye yapıştırın ve **Çalıştır**' ı seçin. Aşağıdaki görüntüde gösterilen tamamlanmış iletiyi görtığınızdan ve bu iletinin altına sonuçtan emin olun.

   [![Sorgu sonuçlarını çalıştırmayı gösteren ekran görüntüsü.](./media/monitor-sign-in-health-for-resilience/run-query.png)](./media/monitor-sign-in-health-for-resilience/run-query.png)

4. Sorguyu vurgulayın ve + **Yeni uyarı kuralı**' nı seçin. 
 
   [![Yeni uyarı kuralı ekranını gösteren ekran görüntüsü.](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)


5. Uyarı koşullarını yapılandırın. Koşul bölümünde, **Ortalama özel günlük araması, Logic Defined Count değerinden fazla** olduğunda bağlantıyı seçin. Sinyal mantığını Yapılandır bölmesinde, uyarı mantığı ' na kaydırın.

   [![Uyarıları Yapılandır ekranını gösteren ekran görüntüsü.](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)
 
   * **Eşik değeri**: 0. Bu değer, herhangi bir sonucu uyarır.

   * **Değerlendirme süresi (dakika)**: 2880. Bu değer bir saat arar

   * **Sıklık (dakika)**: 60. Bu değer, değerlendirme süresini önceki saat için saat başına olacak şekilde ayarlar.

   * **Bitti** seçeneğini belirleyin.

6. **Eylemler** bölümünde, şu ayarları yapılandırın:  

    [![Uyarı kuralı oluştur sayfasını gösteren ekran görüntüsü.](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)

   * **Eylemler** altında **eylem grubu seç**' i seçin ve uyarı bildirilmesini istediğiniz grubu ekleyin.

   * **Özelleştirme eylemleri** altında **e-posta uyarıları**' nı seçin.

   * **Konu satırı** ekleyin.

7. **Uyarı kuralı ayrıntıları**' nın altında, şu ayarları yapılandırın:

   * Açıklayıcı bir ad ve açıklama ekleyin.

   * Uyarının ekleneceği **kaynak grubunu** seçin.

   * Uyarının varsayılan **önem derecesini** seçin.

   * **Oluşturma sonrasında uyarı kuralını etkinleştir** ' i seçin, hemen canlı olmasını Istiyorsanız **Uyarıları Gizle**' yi seçin.

8. **Uyarı kuralı oluşturma**’yı seçin.

9. **Kaydet**' i seçin, sorgu için bir ad girin, **uyarı kategorisi olan bir sorgu olarak kaydedin**. Sonra yeniden **Kaydet** ' i seçin.  

   [![Sorguyu Kaydet düğmesinin gösterildiği ekran görüntüsü.](./media/monitor-sign-in-health-for-resilience/save-query.png)](./media/monitor-sign-in-health-for-resilience/save-query.png)

### <a name="refine-your-queries-and-alerts"></a>Sorgularınızı ve uyarılarınızı daraltın

Sorgularınızı ve uyarılarınızı en yüksek verimlilik için değiştirin.

* Uyarılarınızı test ettiğinizden emin olun.

* Önemli bildirimler almanız için uyarı duyarlılığını ve sıklığını değiştirin. Yöneticiler çok fazla ediniyorlarsa ve önemli bir şeyi kaçırırsa Desensitized uyarılar verebilir. 

* Yöneticinin e-posta istemcilerinde gelen uyarıların izin verilen Gönderenler listesine eklendiğinden emin olun. Aksi takdirde, e-posta istemcinizdeki bir istenmeyen posta Filtresi nedeniyle bildirimleri kaçırırdınız. 

* Azure Izleyici 'de uyarılar sorgusu, yalnızca geçmiş 48 saatten sonuçları içerebilir. [Bu, tasarıma göre geçerli bir kısıtlamadır](https://github.com/MicrosoftDocs/azure-docs/issues/22637).

## <a name="sample-scripts"></a>Örnek betikler

### <a name="kusto-query-for-increase-in-failure-rate"></a>Hata oranı artışı için kusto sorgusu

   En alttaki oran gerektiği şekilde ayarlanabilir ve dün aynı saate kıyasla son bir saat içindeki trafikte yapılan yüzde değişikliğini temsil eder. 0,5, trafikte %50 fark olduğu anlamına gelir.

```kusto

let today = SigninLogs
| where TimeGenerated > ago(1h) // Query failure rate in the last hour 
| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")
// Optionally filter by a specific application
//| where AppDisplayName == **APP NAME**
| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate
| project TimeGenerated, failureRate = (failure * 1.0) / ((failure + success) * 1.0)
| sort by TimeGenerated desc
| serialize rowNumber = row_number();
let yesterday = SigninLogs
| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Query failure rate at the same time yesterday
| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")
// Optionally filter by a specific application
//| where AppDisplayName == **APP NAME**
| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate at same time yesterday
| project TimeGenerated, failureRateYesterday = (failure * 1.0) / ((failure + success) * 1.0)
| sort by TimeGenerated desc
| serialize rowNumber = row_number();
today
| join (yesterday) on rowNumber // join data from same time today and yesterday
| project TimeGenerated, failureRate, failureRateYesterday
// Set threshold to be the percent difference in failure rate in the last hour as compared to the same time yesterday
// Day variable is the number of days since the previous Sunday. Optionally ignore results on Sat, Sun, and Mon because large variability in traffic is expected.
| extend day = dayofweek(now())
| where day != time(6.00:00:00) // exclude Sat
| where day != time(0.00:00:00) // exclude Sun
| where day != time(1.00:00:00) // exclude Mon
| where abs(failureRate - failureRateYesterday) > 0.5

```

### <a name="kusto-query-for-drop-in-usage"></a>Kullanımda bırakma için kusto sorgusu

Aşağıdaki sorguda, Son saatteki trafiği dün ile aynı saate karşılaştırıyoruz.
Önceki güne ait aynı anda trafikte büyük bir değişkenlik olması beklendiğinden Cumartesi, Pazar ve Pazartesi 'yi dışlıyoruz. 

En alttaki oran gerektiği şekilde ayarlanabilir ve dün aynı saate kıyasla son bir saat içindeki trafikte yapılan yüzde değişikliğini temsil eder. 0,5, trafikte %50 fark olduğu anlamına gelir.

*Bu değerleri iş çalışma modelinize uyacak şekilde ayarlamanız gerekir*.

```Kusto
 let today = SigninLogs // Query traffic in the last hour
| where TimeGenerated > ago(1h)
| project TimeGenerated, AppDisplayName, UserPrincipalName
// Optionally filter by AppDisplayName to scope query to a single application
//| where AppDisplayName contains "Office 365 Exchange Online"
| summarize users = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr) // Count distinct users in the last hour
| sort by TimeGenerated desc
| serialize rn = row_number();
let yesterday = SigninLogs // Query traffic at the same hour yesterday
| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Count distinct users in the same hour yesterday
| project TimeGenerated, AppDisplayName, UserPrincipalName
// Optionally filter by AppDisplayName to scope query to a single application
//| where AppDisplayName contains "Office 365 Exchange Online"
| summarize usersYesterday = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)
| sort by TimeGenerated desc
| serialize rn = row_number();
today
| join // Join data from today and yesterday together
(
yesterday
)
on rn
// Calculate the difference in number of users in the last hour compared to the same time yesterday
| project TimeGenerated, users, usersYesterday, difference = abs(users - usersYesterday), max = max_of(users, usersYesterday)
| extend ratio = (difference * 1.0) / max // Ratio is the percent difference in traffic in the last hour as compared to the same time yesterday
// Day variable is the number of days since the previous Sunday. Optionally ignore results on Sat, Sun, and Mon because large variability in traffic is expected.
| extend day = dayofweek(now())
| where day != time(6.00:00:00) // exclude Sat
| where day != time(0.00:00:00) // exclude Sun
| where day != time(1.00:00:00) // exclude Mon
| where ratio > 0.7 // Threshold percent difference in sign-in traffic as compared to same hour yesterday

```

## <a name="create-processes-to-manage-alerts"></a>Uyarıları yönetmek için işlem oluşturma

Sorgu ve uyarıları ayarladıktan sonra, uyarıları yönetmek için iş süreçlerini oluşturun.

* Çalışma kitabını kim ne zaman izlecektir?

* Bir uyarı oluşturulduğunda, kimler araştıracaktır?

* İletişim ihtiyaçları nelerdir? İletişimleri kimler oluşturacak ve kimler alacak?

* Bir kesinti oluşursa, hangi iş süreçlerinin tetiklenmesi gerekir?

## <a name="next-steps"></a>Sonraki adımlar

[Çalışma kitapları hakkında daha fazla bilgi](../reports-monitoring/howto-use-azure-monitor-workbooks.md)
