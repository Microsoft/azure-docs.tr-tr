---
title: Azure Log Analytics 'de depolanan kişisel veriler için rehberlik | Microsoft Docs
description: Bu makalede, Azure Log Analytics 'de depolanan kişisel verilerin ve bunları belirleme ve kaldırma yöntemlerinde nasıl yönetileceği açıklanır.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: 03c4babc8fa70c951d80b720c4d4693968011532
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772272"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Log Analytics ve Application Insights'da depolanan kişisel veriler için kılavuz

Log Analytics kişisel verilerin bulunma olasılığı olan bir veri deposudur. Application Insights verilerini Log Analytics bir bölümde depolar. Bu makale, Log Analytics nerede olduğunu ve bu verilerin tipik olarak Application Insights olduğunu ve bu tür verileri işleyebileceğiniz özellikleri tartışacaktır.

> [!NOTE]
> Bu makalenin _günlük verileri_ , bir Log Analytics çalışma alanına gönderilen verileri ifade ederken, _uygulama verileri_ Application Insights tarafından toplanan verilere başvurur. Çalışma alanı tabanlı bir Application Insights kaynağı kullanıyorsanız, günlük verileri hakkında bilgiler geçerli olur ancak klasik Application Insights kaynağını kullanıyorsanız, uygulama verileri uygulanır.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="strategy-for-personal-data-handling"></a>Kişisel veri işleme stratejisi

Siz ve şirketiniz size özel verilerinizi işleyeceği stratejiyi (Eğer varsa) son olarak belirleyemiyorsa, aşağıdakiler olası yaklaşımlardır. Bu kişiler, en az tercih edilen teknik bir görünümün tercih sırasına göre listelenmiştir:

* Mümkün olduğunda, toplamayı durdurun, bir araya getirebilir, Anonimleştir veya aksi takdirde, toplanan verileri "Private" olarak kabul edilmeden hariç tutun. Bu, tercih edilen yaklaşımdan _yararlanarak_ çok maliyetli ve kesin bir veri işleme stratejisi oluşturmanız için ihtiyaç duymaktır.
* Mümkün olmadığı durumlarda veri platformu ve performans üzerindeki etkiyi azaltmak için verileri normalleştirmeye çalışın. Örneğin, bir açık kullanıcı KIMLIĞINI günlüğe kaydetmek yerine, Kullanıcı adını ve ayrıntılarını daha sonra günlüğe kaydedilebilir bir iç KIMLIKLE bağıntılı bir arama verileri oluşturun. Bu şekilde, kullanıcılarınızın kişisel bilgilerini silmenizi istemesi gerekir, yalnızca kullanıcıya karşılık gelen arama tablosundaki satırı silmek mümkündür. 
* Son olarak, özel verilerin toplanması gerekiyorsa, bir kullanıcıyla ilişkili tüm özel verileri dışarı ve silmeye yönelik tüm yükümlülükleri karşılamak üzere Temizleme API 'si yolu ve var olan sorgu API 'SI yolu etrafında bir işlem oluşturun.

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Log Analytics özel verilerin nerede aranacağı?

Log Analytics, verilerinize bir şemayı etkilemeden, her alanı özel değerlerle geçersiz kılmanıza olanak sağlayan esnek bir depodır. Ayrıca, herhangi bir özel şema alınabilir. Bu nedenle, özel verilerin özel çalışma alanınızda nerede bulunacağı kesin bir şekilde söylemek olanaksızdır. Ancak, aşağıdaki konumlar envanterinizdeki başlangıç noktalarından iyidir:

### <a name="log-data"></a>Günlük verileri

* *IP adresleri*: Log Analytics birçok farklı tablo arasında çeşitli IP bilgilerini toplar. Örneğin, aşağıdaki sorgu, son 24 saat içinde IPv4 adreslerinin toplandığı tüm tabloları gösterir:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Kullanıcı kimlikleri*: Kullanıcı kimlikleri, çok çeşitli çözüm ve tablolarda bulunur. Arama komutunu kullanarak, tüm veri kümeniz genelinde belirli bir kullanıcı adına bakabilirsiniz:
    ```
    search "[username goes here]"
    ```
  Yalnızca insan tarafından okunabilen kullanıcı adlarına değil, ayrıca doğrudan belirli bir kullanıcıya geri görünebilirler.
* *Cihaz kimlikleri*: Kullanıcı kimlikleri gibi, cihaz kimlikleri bazen "özel" olarak kabul edilir. Bunun sorun olabileceği tabloları belirlemek için Kullanıcı kimlikleri için yukarıda listelenen yaklaşımı kullanın. 
* *Özel veriler*: Log Analytics, koleksiyonu çeşitli yöntemlerle sağlar: özel Günlükler ve özel alanlar, [http veri toplayıcı API 'si](../logs/data-collector-api.md) ve sistem olay günlüklerinin bir parçası olarak toplanan özel veriler. Bunların tümü özel verileri içerecek şekilde açıktır ve bu tür verilerin mevcut olup olmadığını doğrulamak için incelenmelidir.
* *Çözüm-yakalanan veriler*: çözüm mekanizması bir açık sonlandırdığı için, uyumluluk sağlamak için çözümler tarafından oluşturulan tüm tabloları gözden geçirmeyi öneririz.

### <a name="application-data"></a>Uygulama verileri

* *IP adresleri*: Application Insights, varsayılan olarak tüm IP adresi alanlarını "0.0.0.0" olarak saklar, oturum bilgilerini sürdürmek için bu değeri gerçek Kullanıcı IP 'si ile geçersiz kılan oldukça yaygın bir modeldir. Aşağıdaki analiz sorgusu, IP adresi sütununda, son 24 saat içinde "0.0.0.0" dışındaki değerleri içeren herhangi bir tabloyu bulmak için kullanılabilir:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Kullanıcı kimlikleri*: Application Insights, varsayılan olarak, Kullanıcı ve oturum izleme için rastgele oluşturulan kimlikleri kullanır. Bununla birlikte, bir KIMLIĞI uygulamayla ilgili daha uygun bir şekilde depolamak için bu alanların geçersiz kılındığından, yaygın bir şekilde görüntülenir. Örneğin: Kullanıcı adları, AAD GUID 'Leri, vb. Bu kimlikler genellikle kişisel veri olarak kapsam içi olarak değerlendirilir ve bu nedenle uygun şekilde işlenmelidir. Önerimiz, bu kimlikleri gizleme veya Anonimleştir her zaman denenmektir. Bu değerlerin genellikle bulunduğu alanlar session_Id, user_Id, user_AuthenticatedId, user_AccountId ve customDimensions içerir.
* *Özel veriler*: Application Insights tüm veri türlerine bir dizi özel boyut eklemenizi sağlar. Bu boyutlar *herhangi bir* veri olabilir. Son 24 saat içinde toplanan özel boyutları belirlemek için aşağıdaki sorguyu kullanın:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Bellek içi ve aktarım verileri*: Application Insights özel durumları, istekleri, bağımlılık çağrılarını ve izlemeleri izler. Özel veriler genellikle kod ve HTTP çağrı düzeyinde toplanabilir. Bu tür verileri belirlemek için özel durumları, istekleri, bağımlılıkları ve izleme tablolarını gözden geçirin. Bu verileri gizleme olasılığı bulunan [telemetri başlatıcıları](../app/api-filtering-sampling.md) 'nı kullanın.
* *Snapshot Debugger yakalamaları*: Application Insights [Snapshot Debugger](../app/snapshot-debugger.md) özelliği, uygulamanızın üretim örneğinde bir özel durum yakalandığında hata ayıklama anlık görüntülerini toplamanıza olanak tanır. Anlık görüntüler, yığının her adımında yerel değişkenlerin değerlerinin yanı sıra özel durumlar için tam yığın izlemenin önde gelen listesini ortaya çıkarır. Ne yazık ki, bu özellik yapışma noktalarının seçmeli silinmesine veya anlık görüntü içindeki verilere programlı erişime izin vermez. Bu nedenle, varsayılan anlık görüntü saklama oranı uyumluluk gereksinimlerinizi karşılamıyorsa, özelliği kapatmanız önerilir.

## <a name="how-to-export-and-delete-private-data"></a>Özel verileri dışarı aktarma ve silme

[Kişisel veri işleme stratejisinde](#strategy-for-personal-data-handling) daha önce bahsedildiği gibi, veri toplama ilkenizi özel verilerin toplanmasını devre dışı bırakmak, verileri belirsizleştirmeniz veya anonimleştirmeye ya da başka bir şekilde değiştirmek için "özel" olarak __kabul edilir.__ Verilerin işlenmesi, sizin ve ekibinizin bir strateji tanımlayıp otomatikleştirilmesi için size maliyetleri, müşterilerinizin verileriyle etkileşim kurması için bir arabirim oluşturmak ve devam eden bakım maliyetlerine neden olur. Ayrıca, Log Analytics ve Application Insights açısından hesaplama maliyetlidir ve çok sayıda eşzamanlı sorgu veya temizleme API çağrısı, diğer tüm etkileşimi Log Analytics işlevselliğiyle olumsuz yönde etkilemenize olanak sağlar. Yani, özel verilerin toplanması gereken bazı geçerli senaryolar vardır. Bu gibi durumlarda, verilerin bu bölümde açıklandığı gibi işlenmesi gerekir.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Görüntüle ve dışarı aktar

Veri görüntüleme ve dışarı aktarma istekleri için [Log Analytics sorgu API 'si](https://dev.loganalytics.io/) veya  [Application Insights sorgu API 'si](https://dev.applicationinsights.io/quickstart) kullanılmalıdır. Kullanıcılarınıza teslim etmek üzere verilerin şeklini uygun bir şekilde dönüştürme mantığı, uygulamanız için size uygun olacaktır. [Azure işlevleri](https://azure.microsoft.com/services/functions/) bu tür mantığı barındırmak için harika bir yer sunar.

> [!IMPORTANT]
>  Temizleme işlemlerinin büyük çoğunluğu SLA 'dan çok daha hızlı tamamlanabilir, ancak **Temizleme işlemlerinin tamamlanmasına yönelik RESMI SLA** , kullanılan veri platformunda ağır etkileri nedeniyle 30 gün içinde ayarlanır. Bu SLA GDPR gereksinimlerini karşılar. Bu otomatikleştirilmiş bir işlemdir, böylece bir işlemin daha hızlı işlenmesini istemek için bir yol yoktur. 

### <a name="delete"></a>Sil

> [!WARNING]
> Log Analytics, bozucu ve geri alınamaz bir şekilde silinir! Lütfen yürütmesinde çok dikkatli olun.

Bir *Temizleme* API 'si yolunu işlemenin bir parçası olarak kullanıma sunulmuştur. Bu yol, sorun ile ilişkili risk, olası performans etkisi ve Log Analytics verilerinizin diğer yönlerini, ölçümlerinin ve diğer yönlerini eğmek için potansiyel olarak kullanılmalıdır. Özel verileri işlemeye yönelik alternatif yaklaşımlar için [kişisel veri Işleme stratejisi](#strategy-for-personal-data-handling) bölümüne bakın.

> [!NOTE]
> Temizleme işlemi gerçekleştirildikten sonra, [Temizleme işlemi durumu](/rest/api/loganalytics/workspacepurge/getpurgestatus) *beklenirken* verilere erişilemez. 

Temizleme, Azure 'daki hiçbir uygulamanın veya kullanıcının (kaynak sahibi dahil) Azure Resource Manager içinde açıkça bir rol verilmeden yürütme izinlerine sahip olacağı yüksek ayrıcalıklı bir işlemdir. Bu rol _veri_ kaybı olur ve veri kaybı nedeniyle dikkatli bir şekilde temsil edilmelidir. 

> [!IMPORTANT]
> Sistem kaynaklarını yönetmek için, temizleme istekleri saat başına 50 istekte azaltılır. Tek bir komut göndererek Temizleme isteklerinin yürütülmesi, koşulu temizleme gerektiren tüm Kullanıcı kimliklerini içerir. Birden çok kimlik belirtmek için [ın işlecini](/azure/kusto/query/inoperator) kullanın. Sonuçların beklendiğini doğrulamak için Temizleme isteğini yürütmeden önce sorguyu çalıştırmalısınız. 



Azure Resource Manager rolü atandıktan sonra iki yeni API yolu mevcuttur: 

#### <a name="log-data"></a>Günlük verileri

* [Temizleme sonrası](/rest/api/loganalytics/workspacepurge/purge) -Silinecek verilerin parametrelerini belirten bir nesne alır ve bir başvuru GUID 'si döndürür 
* Temizleme durumunu Al-Temizleme API 'sinin durumunu öğrenmek için çağırabileceğiniz bir URL 'YI içeren bir ' x-MS-Status-Location ' üst bilgisi döndürür. Örnek:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperationalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Temizleme işlemlerinin büyük çoğunluğunun SLA 'ümüzden çok daha hızlı tamamlanmasını bekliyoruz, çünkü Log Analytics tarafından kullanılan veri platformunda ağır etkileri nedeniyle, **Temizleme işlemlerinin tamamlanmasına yönelik resmi SLA 'sı 30 gün olarak ayarlanır**. 

#### <a name="application-data"></a>Uygulama verileri

* [Temizleme sonrası](/rest/api/application-insights/components/purge) -Silinecek verilerin parametrelerini belirten bir nesne alır ve bir başvuru GUID 'si döndürür
* Temizleme durumunu Al-Temizleme API 'sinin durumunu öğrenmek için çağırabileceğiniz bir URL 'YI içeren bir ' x-MS-Status-Location ' üst bilgisi döndürür. Örnek:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Temizleme işlemlerinin büyük çoğunluğu SLA 'dan çok daha hızlı tamamlanabilir, çünkü Application Insights tarafından kullanılan veri platformunda ağır etkileri nedeniyle, **Temizleme işlemlerinin tamamlanmasına yönelik resmi SLA 'sı 30 gün olarak ayarlanır**.

## <a name="next-steps"></a>Sonraki adımlar
- Log Analytics verilerinin nasıl toplandığı, işlendiği ve güvenliğinin sağlandığı hakkında daha fazla bilgi edinmek için bkz. [Log Analytics veri güvenliği](../logs/data-security.md).
- Application Insights verilerinin nasıl toplandığı, işlendiği ve güvenliğinin sağlandığı hakkında daha fazla bilgi edinmek için bkz. [Application Insights veri güvenliği](../app/data-retention-privacy.md).