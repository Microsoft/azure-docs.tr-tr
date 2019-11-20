---
title: Azure Izleyici 'de özel Günlükler toplayın | Microsoft Docs
description: Azure Izleyici, hem Windows hem de Linux bilgisayarlardaki metin dosyalarından olayları toplayabilir.  Bu makalede, Azure Izleyici 'de oluşturdukları kayıtların yeni bir özel günlüğü ve ayrıntılarının nasıl tanımlanacağı açıklanmaktadır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/26/2019
ms.openlocfilehash: 3bd40e9a266305ac94ed53806bf394891e89c125
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932506"
---
# <a name="custom-logs-in-azure-monitor"></a>Azure Izleyici 'de özel Günlükler

Azure Izleyici 'deki özel Günlükler veri kaynağı, hem Windows hem de Linux bilgisayarlarındaki metin dosyalarından olayları toplamanıza olanak tanır. Birçok uygulama, Windows olay günlüğü veya Syslog gibi standart günlüğe kaydetme hizmetleri yerine metin dosyalarına bilgi kaydeder. Toplandıktan sonra, sorgulardaki tek tek alanlara verileri ayrıştırıp verileri toplama sırasında tek tek alanlara ayıklayabilirsiniz.

![Özel günlük koleksiyonu](media/data-sources-custom-logs/overview.png)

Toplanacak günlük dosyaları aşağıdaki ölçütlere uymalıdır.

- Günlüğün her satırda tek bir girişi olması veya her girdinin başlangıcında aşağıdaki biçimlerden biriyle eşleşen bir zaman damgası kullanması gerekir.

    YYYY-AA-GG SS: DD: SS<br>M/D/YYYY HH: MM: SS/PM<br>Mon gg, YYYY HH: MM: SS<br />YYAAGG HH: mm: ss<br />ddMMyy HH: mm: ss<br />MMM d hh: mm: ss<br />gg/aaa/yyyy: ss: DD: ss zzz<br />yyyy-MM-ddTHH: mm: ssK

- Günlük dosyası, yeni girişlerle dosyanın üzerine yazıldığı döngüsel günlüğe veya günlük döngüsüne izin vermelidir.
- Günlük dosyası ASCII veya UTF-8 kodlaması kullanmalıdır.  UTF-16 gibi diğer biçimler desteklenmez.

>[!NOTE]
> Günlük dosyasında yinelenen girişler varsa, Azure Izleyici bunları toplar. Ancak, filtre sonuçlarının sonuç sayısından daha fazla olay göstereceği durumlarda sorgu sonuçları tutarsız olur. Bunu oluşturan uygulamanın bu davranışa neden olup olmadığını ve özel günlük toplama tanımı oluşturmadan önce mümkünse bu davranışa yol açıp açmadığını öğrenmek için günlüğü doğrulamanız önemli olacaktır.  
>

>[!NOTE]
> Bir Log Analytics çalışma alanı aşağıdaki limitleri destekler:
> 
> * Yalnızca 500 özel günlük oluşturulabilir.
> * Tablo yalnızca en fazla 500 sütunu destekler. 
> * Sütun adı için en fazla karakter sayısı 500 ' dir. 
>

>[!IMPORTANT]
>Özel günlük koleksiyonu, günlük dosyasını yazan uygulamanın günlük içeriğini düzenli olarak diske boşaltmaları gerekir. Bunun nedeni, özel günlük toplamanın izlenmekte olan günlük dosyası için dosya sistemi değişiklik bildirimlerine dayanmasına bağlıdır.

## <a name="defining-a-custom-log"></a>Özel bir günlük tanımlama
Özel bir günlük dosyası tanımlamak için aşağıdaki yordamı kullanın.  Özel günlük ekleme örneğine ilişkin bir anlatım için bu makalenin sonuna gidin.

### <a name="step-1-open-the-custom-log-wizard"></a>1\. Adım Özel günlük Sihirbazı 'Nı açın
Özel günlük Sihirbazı Azure portal çalışır ve toplanacak yeni bir özel günlük tanımlamanızı sağlar.

1. Azure portal, **Gelişmiş ayarlar**> çalışma alanınız > **Log Analytics çalışma alanları** ' nı seçin.
2. **Data** > **özel günlüklerine**tıklayın.
3. Varsayılan olarak, tüm yapılandırma değişiklikleri otomatik olarak tüm aracılara gönderilir. Linux aracıları için bir yapılandırma dosyası, Floentd veri toplayıcısına gönderilir.
4. Özel günlük Sihirbazı 'Nı açmak için **Ekle +** ' ye tıklayın.

### <a name="step-2-upload-and-parse-a-sample-log"></a>2\. Adım Örnek günlük yükleme ve ayrıştırma
Özel günlüğün bir örneğini karşıya yükleyerek başlayabilirsiniz.  Sihirbaz, doğrulamanız için bu dosyadaki girişleri ayrıştırır ve görüntüler.  Azure Izleyici, her bir kaydı tanımlamak için belirttiğiniz sınırlayıcıyı kullanır.

**Yeni satır** varsayılan sınırlayıcıdır ve her satırda tek bir girişi olan günlük dosyaları için kullanılır.  Satır, kullanılabilir biçimlerden birindeki tarih ve saat ile başlıyorsa, birden fazla satıra yayılan girdileri destekleyen bir **zaman damgası** sınırlayıcısı belirtebilirsiniz.

Zaman damgası sınırlayıcısı kullanılıyorsa, Azure Izleyici 'de depolanan her bir kaydın TimeGenerated özelliği, günlük dosyasında bu girdi için belirtilen tarih/saat ile doldurulur.  Yeni bir satır sınırlayıcısı kullanılırsa, TimeGenerated, Azure Izleyici 'nin girişi topladığı tarih ve saat ile doldurulur.

1. **Araştır** ' a tıklayın ve örnek bir dosyaya gidin.  Bu düğme, bazı tarayıcılarda **Dosya Seç** ' in etiketlenmiş olabileceğini unutmayın.
2. **İleri**’ye tıklayın.
3. Özel günlük Sihirbazı dosyayı karşıya yükler ve tanımladığı kayıtları listeler.
4. Yeni bir kaydı tanımlamak için kullanılan sınırlayıcıyı değiştirin ve günlük dosyanızdaki kayıtları en iyi şekilde tanımlayan sınırlayıcıyı seçin.
5. **İleri**’ye tıklayın.

### <a name="step-3-add-log-collection-paths"></a>3\. Adım Günlük koleksiyonu yolları Ekle
Aracıda özel günlüğü bulabilecekleri bir veya daha fazla yol tanımlamalısınız.  Günlük dosyası için belirli bir yol ve ad sağlayabilir ya da ad için joker karakter içeren bir yol belirtebilirsiniz. Bu, her gün yeni bir dosya oluşturan uygulamaları veya bir dosya belirli bir boyuta ulaştığında destekler. Tek bir günlük dosyası için birden çok yol da sağlayabilirsiniz.

Örneğin, bir uygulama, log20100316. txt ' de olduğu gibi ada dahil edilen tarihle her gün bir günlük dosyası oluşturabilir. Bu tür bir günlük için bir düzen, uygulamanın adlandırma düzenini takip eden herhangi bir günlük dosyasına uygulanacak *log\*.txt* olabilir.

Aşağıdaki tabloda farklı günlük dosyaları belirtmek için geçerli desenlerin örnekleri verilmiştir.

| Açıklama | Yol |
|:--- |:--- |
| *C:\logs* içindeki tüm dosyalar Windows aracısında. txt uzantısıyla |C:\Logs\\\*. txt |
| *C:\logs* içindeki tüm dosyalar, Windows aracısında log ve. txt uzantısıyla başlayan bir ada sahip. |C:\Logs\ log\*.txt |
| Linux aracısında. txt uzantısıyla */var/log/Audit* içindeki tüm dosyalar |/var/log/Audit/*. txt |
| */Var/log/Audit* içindeki tüm dosyalar, log ve Linux aracısında bir. txt uzantısıyla başlayan bir ada sahip. |/var/log/Audit/log\*.txt |

1. Hangi yol biçimini ekleyecekseniz belirlemek için Windows veya Linux ' u seçin.
2. Yolu yazın ve **+** düğmesine tıklayın.
3. İşlemi ek yollar için yineleyin.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>4\. Adım. Günlük için bir ad ve açıklama girin
Belirttiğiniz ad, yukarıda açıklandığı gibi, günlük türü için kullanılacaktır.  Özel bir günlük olarak ayırt etmek için her zaman _CL ile sona bitecektir.

1. Günlük için bir ad yazın.  **\_CL** son eki otomatik olarak sağlanır.
2. İsteğe bağlı bir **Açıklama**ekleyin.
3. Özel günlük tanımını kaydetmek için **İleri** ' ye tıklayın.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>5\. Adım. Özel günlüklerin toplandığını doğrulama
Yeni bir özel günlükteki ilk verilerin Azure Izleyici 'de görünmesi bir saate kadar sürebilir.  Bu işlem, özel günlüğü tanımladığınız noktadan belirttiğiniz yolda bulunan günlüklerdeki girdileri toplamaya başlar.  Bu, özel günlük oluşturma sırasında karşıya yüklediğiniz girdileri korumaz, ancak bulduğu günlük dosyalarında zaten var olan girdileri toplar.

Azure Izleyici özel günlüğünden toplamaya başladıktan sonra, kayıtları bir günlük sorgusuyla birlikte kullanılabilir.  Sorgunuzda **tür** olarak özel günlüğü verdiğiniz adı kullanın.

> [!NOTE]
> Sorgudaki RawData özelliği eksikse, tarayıcınızı kapatıp yeniden açmanız gerekebilir.

### <a name="step-6-parse-the-custom-log-entries"></a>6\. Adım. Özel günlük girdilerini ayrıştırma
Tüm günlük girdisi **rawData**adlı tek bir özellikte depolanacak.  Büyük olasılıkla her bir girişteki farklı bilgi parçalarını her kayıt için ayrı ayrı özelliklere ayırmak isteyeceksiniz. **RawData** 'ı birden çok özelliğe ayrıştırma seçenekleri Için [Azure izleyici 'de metin verileri ayrıştırma](../log-query/parse-text.md) bölümüne bakın.

## <a name="removing-a-custom-log"></a>Özel günlüğü kaldırma
Daha önce tanımladığınız özel bir günlüğü kaldırmak için Azure portal aşağıdaki işlemi kullanın.

1. Çalışma alanınızın **Gelişmiş ayarlarındaki** **veri** menüsünde özel **Günlükler** ' i seçerek tüm özel günlüklerinizi listeleyin.
2. Kaldırmak için özel günlüğün yanındaki **Kaldır** ' a tıklayın.

## <a name="data-collection"></a>Veri toplama
Azure Izleyici, her bir özel günlüğün her 5 dakikada bir yeni girişler toplar.  Aracı, topladığı her günlük dosyasında yerini kaydeder.  Aracı bir süre çevrimdışı kalırsa, bu girdiler aracı çevrimdışıyken oluşturulsa bile, Azure Izleyici son kaldığınız yerden girişler toplar.

Günlük girişinin tüm içeriği **rawData**adlı tek bir özelliğe yazılır.  İçeri aktarılan her günlük girişini birden çok özelliğe ayrıştırmaya yönelik yöntemler için bkz. [Azure izleyici 'de metin verileri ayrıştırma](../log-query/parse-text.md) .

## <a name="custom-log-record-properties"></a>Özel günlük kaydı özellikleri
Özel günlük kayıtları, sağladığınız günlük adı ve aşağıdaki tablodaki özellikler içeren bir tür vardır.

| Özellik | Açıklama |
|:--- |:--- |
| TimeGenerated |Kaydın Azure Izleyici tarafından toplandığı tarih ve saat.  Günlük zaman tabanlı bir sınırlayıcı kullanıyorsa, bu, girdiden toplanan süredir. |
| SourceSystem |Kaydın toplandığı aracının türü. <br> OpsManager – Windows Aracısı, doğrudan bağlantı veya System Center Operations Manager <br> Linux – tüm Linux aracıları |
| RawData |Toplanan girişin tam metni. Büyük olasılıkla, [Bu verileri tek tek özelliklerde ayrıştırmaya](../log-query/parse-text.md)isteyeceksiniz. |
| ManagementGroupName |System Center Işlemleri için yönetim grubunun adı aracıları yönetme.  Diğer aracılar için bu, AOI-\<çalışma alanı KIMLIĞIDIR\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Özel günlük ekleme hakkında örnek yönergeler
Aşağıdaki bölümde, özel günlük oluşturma örneği gösterilmektedir.  Toplanmakta olan örnek günlük, bir tarih ve saat ile başlayan her satırda tek bir girdiye ve ardından kod, durum ve ileti için virgülle ayrılmış alanlara sahiptir.  Birkaç örnek girdi aşağıda gösterilmiştir.

    2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2019-08-27 01:38:22 302,Error,Application could not connect to database
    2019-08-27 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Örnek günlük yükleme ve ayrıştırma
Günlük dosyalarından birini sağlıyoruz ve bunların toplanacağı olayları görebilirler.  Bu durumda yeni satır yeterli bir sınırlayıcıdır.  Günlükteki tek bir giriş birden çok satıra yayılabilse de, zaman damgası sınırlayıcısı kullanılması gerekir.

![Örnek günlük yükleme ve ayrıştırma](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Günlük koleksiyonu yolları Ekle
Günlük dosyaları *C:\uygulama\logs*' de yer alır.  *AppYYYYMMDD. log*deseninin tarihini içeren bir ada sahip her gün yeni bir dosya oluşturulur.  Bu günlük için yeterli bir model *C:\myapp\logs\\\*. log*olacaktır.

![Günlük toplama yolu](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Günlük için bir ad ve açıklama girin
Bir *MyApp_CL* adı kullanıyoruz ve bir **Açıklama**yazın.

![Günlük adı](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Özel günlüklerin toplandığını doğrulama
Toplanan günlükteki tüm kayıtları döndürmek için basit bir *MyApp_CL* sorgusu kullanıyoruz.

![Özel alanları olmayan günlük sorgusu](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Özel günlüklerin alternatifleri
Verileriniz hakkında listelenen ölçütlere uyan özel Günlükler yararlı olmakla kalmaz, ancak aşağıdaki gibi başka bir stratejinin olması gereken durumlar vardır:

- Veriler, zaman damgasının farklı biçimde olması gibi gerekli yapıya uymuyor.
- Günlük dosyası, dosya kodlama veya desteklenmeyen bir klasör yapısı gibi gereksinimlere uymuyor.
- Veriler, koleksiyon öncesinde ön işleme veya filtreleme gerektirir. 

Verilerinizin özel günlüklerle toplanamadığı durumlarda, aşağıdaki alternatif stratejileri göz önünde bulundurun:

- Azure Izleyici tarafından toplanan [Windows olaylarına](data-sources-windows-events.md) veya [Syslog](data-sources-syslog.md) 'a veri yazmak için özel bir komut dosyası veya başka bir yöntem kullanın. 
- [Http veri toplayıcı API 'sini](data-collector-api.md)kullanarak verileri doğrudan Azure izleyici 'ye gönderin. 

## <a name="next-steps"></a>Sonraki adımlar
* İçeri aktarılan her günlük girişini birden çok özelliğe ayrıştırmaya yönelik yöntemler için bkz. [Azure izleyici 'de metin verileri ayrıştırma](../log-query/parse-text.md) .
* Veri kaynaklarından ve çözümlerinden toplanan verileri analiz etmek için [günlük sorguları](../log-query/log-query-overview.md) hakkında bilgi edinin.