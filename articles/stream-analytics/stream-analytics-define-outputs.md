---
title: Azure Stream Analytics çıkışlarını anlayın
description: Bu makalede, analiz sonuçları için Power BI dahil olmak üzere Azure Stream Analytics ' de kullanılabilen veri çıktısı seçenekleri açıklanmaktadır.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: 6f04ccf216edb4e6a654c83c6220451bfccfe6ac
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488508"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Azure Stream Analytics çıkışlarını anlayın

Bu makalede bir Azure Stream Analytics işi için kullanılabilir çıkış türleri açıklanmaktadır. Çıktılar Stream Analytics işinin sonuçlarını depolamanızı ve kaydetmenizi sağlar. Çıktı verilerini kullanarak, daha fazla iş analizi ve verilerinizin veri depolama alanınızı gerçekleştirebilirsiniz.

Stream Analytics sorgunuzu tasarlarken, [from yan tümcesini](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)kullanarak çıkışın adına bakın. Sorgudaki çoklu yan tümceleri sağlayarak iş başına tek bir çıktı veya akış işi başına birden fazla çıkış (varsa) kullanabilirsiniz.

Stream Analytics iş çıktıları oluşturmak, düzenlemek ve test etmek için [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure POWERSHELL](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)ve [Visual Studio 'yu](stream-analytics-quick-create-vs.md)kullanabilirsiniz.

Bazı çıkışlar türleri [bölümlemeyi](#partitioning)destekler. [Çıkış toplu işlem boyutları](#output-batch-size) , üretilen işi iyileştirmek için farklılık gösterir.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Stream Analytics, [Azure Data Lake Storage Gen 1 ' i](../data-lake-store/data-lake-store-overview.md)destekler. Azure Data Lake Storage, büyük veri analizi iş yükleri için kurumsal çapta, hiper ölçekli bir depodur. İşletimsel ve araştırmacı analizler için herhangi bir boyut, tür ve Alım hızıyla ilgili verileri depolamak üzere Data Lake Storage kullanabilirsiniz. Stream Analytics Data Lake Storage erişmek için yetkilendirilmiş olması gerekir.

Stream Analytics çıkış Azure Data Lake Storage Şu anda Azure Çin 21Vianet ve Azure Almanya (T-Systems Uluslararası) bölgelerinde kullanılamıyor.

Aşağıdaki tabloda Data Lake Storage Gen 1 çıktı yapılandırmak için özellik adları ve açıklamaları listelenmektedir.   

| Özellik adı | Açıklama |
| --- | --- |
| Çıktı diğer adı | Sorgu çıkışını Data Lake Store yönlendirmek için sorgularda kullanılan kolay bir ad. |
| Abonelik | Azure Data Lake Storage hesabınızı içeren abonelik. |
| Hesap adı | Çıktımız Data Lake Store hesabının adı. Aboneliğinizde kullanılabilir Data Lake Store hesapların bir açılır listesi sunulur. |
| Yol ön eki stili | Belirtilen Data Lake Store hesabı içinde dosyalarınızı yazmak için kullanılan dosya yolu. {Date} ve {Time} değişkenlerinin bir veya daha fazla örneğini belirtebilirsiniz:<br /><ul><li>Örnek 1: Klasör1/logs/{Date}/{Time}</li><li>Örnek 2: Klasör1/logs/{Date}</li></ul><br />Oluşturulan klasör yapısının zaman damgası UTC ve yerel saate göre değil.<br /><br />Dosya yolu deseninin sonunda eğik çizgi (/) yoksa, dosya yolundaki son model bir dosya adı öneki olarak değerlendirilir. <br /><br />Yeni dosyalar şu koşullarda oluşturulur:<ul><li>Çıkış şemasında değişiklik</li><li>Bir işin dış veya iç yeniden başlatılması</li></ul> |
| Tarih biçimi | İsteğe bağlı. Ön ek yolunda Tarih belirteci kullanılıyorsa, dosyalarınızın düzenlendiği tarih biçimini seçebilirsiniz. Örnek: YYYY/AA/GG |
|Saat biçimi | İsteğe bağlı. Ön ek yolunda zaman belirteci kullanılıyorsa, dosyalarınızın düzenlendiği saat biçimini belirtin. Şu anda desteklenen tek değer HH 'dir. |
| Olay serileştirme biçimi | Çıkış verileri için serileştirme biçimi. JSON, CSV ve avro desteklenir.|
| Encoding | CSV veya JSON biçimi kullanıyorsanız, bir kodlama belirtilmesi gerekir. Şu anda desteklenen tek kodlama biçimi UTF-8 ' i destekler.|
| Ayırıcı | Yalnızca CSV serileştirme için geçerlidir. Stream Analytics CSV verilerini seri hale getirmek için bir dizi ortak sınırlayıcıları destekler. Desteklenen değerler virgül, noktalı virgül, boşluk, sekme ve dikey çubuklardır.|
| Biçimlendir | Yalnızca JSON serileştirme için geçerlidir. **Satır ayrımı** , çıktının her bir JSON nesnesine yeni bir satırla ayrılmış şekilde biçimlendirildiğini belirtir. **Dizi** , çıktının JSON nesneleri dizisi olarak biçimlendirildiğini belirtir. Bu dizi yalnızca iş durdurulduğunda veya Stream Analytics bir sonraki zaman penceresine taşındığında kapatılır. Genel olarak, çıkış dosyası hala üzerine yazılırken özel bir işleme gerektirmediğinden, hat ayrımı olan JSON kullanılması tercih edilir.|
| Kimlik doğrulaması modu | [Yönetilen kimlik](stream-analytics-managed-identities-adls.md) veya kullanıcı belirtecini kullanarak Data Lake Storage hesabınıza erişim yetkisi verebilirsiniz. Erişim izni verdiğinizde, Kullanıcı hesabı parolasını değiştirerek, bu iş için Data Lake Storage çıkışını silerek veya Stream Analytics işini silerek erişimi iptal edebilirsiniz. |

## <a name="sql-database"></a>SQL Veritabanı

[Azure SQL veritabanını](https://azure.microsoft.com/services/sql-database/) , doğası veya ilişkisel bir veritabanında barındırılmakta olan içeriğe bağlı uygulamalar için bir çıktı olarak kullanabilirsiniz. Stream Analytics işleri SQL veritabanında var olan bir tabloya yazar. Tablo şemasının, iş çıkışındaki alanlarla ve türleriyle tam olarak eşleşmesi gerekir. [Azure SQL veri ambarı](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) 'Nı SQL veritabanı çıkış seçeneği aracılığıyla bir çıktı olarak da belirtebilirsiniz. Yazma verimini geliştirme yolları hakkında bilgi edinmek için bkz. [Azure SQL veritabanı ile çıkış makalesi Stream Analytics](stream-analytics-sql-output-perf.md) .

[Azure SQL veritabanı yönetilen örneğini](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) çıkış olarak da kullanabilirsiniz. [Azure SQL veritabanı yönetilen örneği 'nde genel uç noktasını yapılandırmanız](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) ve ardından Azure Stream Analytics aşağıdaki ayarları el ile yapılandırmanız gerekir. SQL Server çalıştıran bir veritabanı ile birlikte çalışan Azure sanal makinesi, aşağıdaki ayarları el ile yapılandırarak de desteklenir.

Aşağıdaki tabloda, bir SQL veritabanı çıkışı oluşturmak için özellik adları ve bunların açıklamaları listelenmektedir.

| Özellik adı | Açıklama |
| --- | --- |
| Çıktı diğer adı |Sorgu çıkışını bu veritabanına yönlendirmek için sorgularda kullanılan kolay bir ad. |
| Database | Çıktlarınızı gönderdiğiniz veritabanının adı. |
| Sunucu adı | SQL veritabanı sunucu adı. Azure SQL veritabanı yönetilen örneği için 3342 numaralı bağlantı noktasını belirtmeniz gerekir. Örneğin, *sampleserver. public. Database. Windows. net, 3342* |
| Kullanıcı adı | Veritabanına yazma erişimi olan Kullanıcı adı. Stream Analytics yalnızca SQL kimlik doğrulamasını destekler. |
| Parola | Veritabanına bağlanmak için parola. |
| Tablo | Çıktının yazıldığı tablo adı. Tablo adı büyük/küçük harfe duyarlıdır. Bu tablonun şeması, alan sayısı ve iş çıktılarınızın oluşturduğu türleri ile tam olarak eşleşmelidir. |
|Bölüm düzenini devralma| Tabloya birden çok yazıcı ile tam paralel topolojiyi etkinleştirmek için önceki sorgu adımlarınızın bölümleme şemasını devralma seçeneği. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'na Azure Stream Analytics çıktısı](stream-analytics-sql-output-perf.md).|
|En fazla toplu iş sayısı| Her toplu ekleme hareketiyle gönderilen kayıt sayısı için önerilen üst sınır.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>BLOB depolama ve Azure Data Lake Gen2

Data Lake Storage 2. Azure Storage 'ı Azure 'da kurumsal veri oluşturma için temel oluşturur. Başlangıçtan itibaren yüzlerce Gigabit işleme petabaytlarca, çok büyük miktarlarda veriyi kolay bir şekilde yönetmenize olanak tanılarken, Data Lake Storage 2. birden fazla bilgi için başlangıç sürümünden tasarlanan. Data Lake Storage 2. temel bir parçası, blob depolamaya hiyerarşik bir ad alanının eklenmesinin bir parçasıdır.

Azure Blob depolama, büyük miktarlarda yapılandırılmamış verileri bulutta depolamak için uygun maliyetli ve ölçeklenebilir bir çözüm sunar. BLOB depolama ve kullanım kullanımıyla ilgili bir giriş için bkz. [Azure Portal blob 'Ları yükleme, indirme ve listeleme](../storage/blobs/storage-quickstart-blobs-portal.md).

Aşağıdaki tabloda, bir blob veya ADLS 2. çıkışı oluşturmak için özellik adları ve bunların açıklamaları listelenmektedir.

| Özellik adı       | Açıklama                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Çıktı diğer adı        | Sorgu çıkışını bu blob depolamaya yönlendirmek için sorgularda kullanılan kolay bir ad. |
| Depolama hesabı     | Çıktlarınızı gönderdiğiniz depolama hesabının adı.               |
| Depolama hesabı anahtarı | Depolama hesabıyla ilişkili gizli anahtar.                              |
| Depolama kapsayıcısı   | Azure Blob hizmetinde depolanan BLOB 'lar için mantıksal gruplama. Blob hizmetine bir blob yüklediğinizde, o blob için bir kapsayıcı belirtmeniz gerekir. |
| Yol deseni | İsteğe bağlı. Belirtilen kapsayıcı içinde bloblarınızı yazmak için kullanılan dosya yolu deseninin. <br /><br /> Yol modelinde, Blobların yazıldığı sıklığı belirtmek için tarih ve saat değişkenlerinin bir veya daha fazla örneğini kullanmayı seçebilirsiniz: <br /> {date}, {Time} <br /><br />Blob 'ları bölümlemek için olay verilerinize özel bir {Field} adı belirtmek için özel blob bölümlemesini kullanabilirsiniz. Alan adı alfasayısal olur ve boşluk, kısa çizgi ve alt çizgi içerebilir. Özel alanlardaki kısıtlamalar şunlardır: <ul><li>Alan adları büyük/küçük harfe duyarlı değildir. Örneğin, hizmet "ID" sütununu ve "ID" sütununu ayırt edemiyor.</li><li>İç içe alanlara izin verilmez. Bunun yerine, alanı "düzleştirmek" için iş sorgusunda bir diğer ad kullanın.</li><li>İfadeler alan adı olarak kullanılamaz.</li></ul> <br />Bu özellik, yoldaki özel tarih/saat biçimi belirleyici yapılandırmalarının kullanılmasını mümkün. Özel tarih ve saat biçimleri tek seferde belirtilmelidir ve {DateTime:\<belirleyicisi >} anahtar sözcüğü ile alınmıştır. \<belirleyici > için izin verilen girişler yyyy, MM, M, gg, d, HH, H, mm, m, SS veya s şeklindedir. {DateTime:\<belirleyicisi >} anahtar sözcüğü, özel tarih/saat yapılandırması oluşturmak için yolda birden çok kez kullanılabilir. <br /><br />Örnekler: <ul><li>Örnek 1: Cluster1/logs/{Date}/{Time}</li><li>Örnek 2: Cluster1/logs/{Date}</li><li>Örnek 3: Cluster1/{client_id}/{Date}/{Time}</li><li>Örnek 4: Cluster1/{DateTime: ss}/{myField} burada sorgunun olduğu yer: Data. myField ' ı girişte myField olarak BELIRLEYIN;</li><li>Örnek 5: Cluster1/Year = {DateTime: yyyy}/ay = {DateTime: DD}/gün = {DateTime: dd}</ul><br />Oluşturulan klasör yapısının zaman damgası UTC ve yerel saate göre değil.<br /><br />Dosya adlandırma aşağıdaki kuralı kullanır: <br /><br />{Path önek deseninin}/Schemahashcode_guid_number.exe<br /><br />Örnek çıkış dosyaları:<ul><li>Mgınput/20170901/00/45434_gguid_1. csv</li>  <li>Mgınput/20170901/01/45434_gguid_1. csv</li></ul> <br />Bu özellik hakkında daha fazla bilgi için bkz. [özel blob çıkış bölümlendirme Azure Stream Analytics](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Tarih biçimi | İsteğe bağlı. Ön ek yolunda Tarih belirteci kullanılıyorsa, dosyalarınızın düzenlendiği tarih biçimini seçebilirsiniz. Örnek: YYYY/AA/GG |
| Saat biçimi | İsteğe bağlı. Ön ek yolunda zaman belirteci kullanılıyorsa, dosyalarınızın düzenlendiği saat biçimini belirtin. Şu anda desteklenen tek değer HH 'dir. |
| Olay serileştirme biçimi | Çıkış verileri için serileştirme biçimi. JSON, CSV, avro ve Parquet destekleniyor. |
|En az satır (yalnızca Parquet)|Toplu iş başına en az satır sayısı. Parquet için her Batch yeni bir dosya oluşturur. Geçerli varsayılan değer 2.000 satırdır ve izin verilen en fazla 10.000 satır olur.|
|En uzun süre (yalnızca Parquet)|Toplu iş başına en fazla bekleme süresi. Bu süreden sonra, en düşük satır gereksinimi karşılanmasa bile toplu iş çıktıya yazılır. Geçerli varsayılan değer 1 dakikadır ve izin verilen en fazla 2 saattir. Blob çıktınızdan yol deseninin sıklığı varsa, bekleme süresi bölüm saat aralığından daha yüksek olamaz.|
| Encoding    | CSV veya JSON biçimi kullanıyorsanız, bir kodlama belirtilmesi gerekir. Şu anda desteklenen tek kodlama biçimi UTF-8 ' i destekler. |
| Ayırıcı   | Yalnızca CSV serileştirme için geçerlidir. Stream Analytics CSV verilerini seri hale getirmek için bir dizi ortak sınırlayıcıları destekler. Desteklenen değerler virgül, noktalı virgül, boşluk, sekme ve dikey çubuklardır. |
| Biçimlendir      | Yalnızca JSON serileştirme için geçerlidir. **Satır ayrımı** , çıktının her bir JSON nesnesine yeni bir satırla ayrılmış şekilde biçimlendirildiğini belirtir. **Dizi** , çıktının JSON nesneleri dizisi olarak biçimlendirildiğini belirtir. Bu dizi yalnızca iş durdurulduğunda veya Stream Analytics bir sonraki zaman penceresine taşındığında kapatılır. Genel olarak, çıkış dosyası hala üzerine yazılırken özel bir işleme gerektirmediğinden, hat ayrımı olan JSON kullanılması tercih edilir. |

Blob depolamayı çıkış olarak kullanırken, blob 'da aşağıdaki durumlarda yeni bir dosya oluşturulur:

* Dosya izin verilen maksimum blok sayısını aşarsa (Şu anda 50.000). İzin verilen en fazla blok sayısına ulaşmadan izin verilen en fazla blok sayısına ulaşmanız gerekir. Örneğin, çıkış oranı yüksekse, blok başına daha fazla bayt görebilirsiniz ve dosya boyutu daha büyüktür. Çıkış hızı düşükse, her bloğun daha az verileri vardır ve dosya boyutu daha küçüktür.
* Çıktıda bir şema değişikliği varsa ve çıkış biçimi sabit şema (CSV ve avro) gerektirir.
* Bir iş yeniden başlatılırsa, bir kullanıcı tarafından durduruluyor ve başlatıldığında ya da sistem bakımı veya hata kurtarma için dahili olarak.
* Sorgu tamamen bölümlenmiş ise ve her bir çıkış bölümü için yeni bir dosya oluşturulur.
* Kullanıcı bir dosyayı veya depolama hesabının kapsayıcısını silerse.
* Çıkış zaman yol ön eki düzenine göre bölümlenmiş ise ve sorgu bir sonraki saate geçerse yeni bir blob kullanılır.
* Çıkış özel bir alanla bölümlense ve yoksa, bölüm anahtarı başına yeni bir blob oluşturulur.
* Çıkış, bölüm anahtarı kardinalitesi 8.000 ' i aşarsa ve bölüm anahtarı başına yeni bir blob oluşturulduysa özel bir alanla bölümlenmiştir.

## <a name="event-hubs"></a>Event Hubs

[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) hizmeti, yüksek düzeyde ölçeklenebilir bir yayınla-abone ol olay alma olayıdır. Saniyede milyonlarca olay toplayabilirler. Bir olay hub 'ının çıkış olarak bir kullanımı, bir Stream Analytics işinin çıktısının başka bir akış işinin girişi haline geldiği zaman olur. En büyük ileti boyutu ve toplu işlem boyutu iyileştirmesi hakkında daha fazla bilgi için bkz. [çıktı toplu iş boyutu](#output-batch-size) bölümü.

Olay Hub 'larından çıkış olarak veri akışlarını yapılandırmak için birkaç parametreye ihtiyacınız vardır.

| Özellik adı | Açıklama |
| --- | --- |
| Çıktı diğer adı | Sorgu çıkışını bu olay hub 'ına yönlendirmek için sorgularda kullanılan kolay bir ad. |
| Olay hub’ı ad alanı | Bir mesajlaşma varlıkları kümesi için kapsayıcı. Yeni bir olay hub 'ı oluşturduğunuzda bir olay hub 'ı ad alanı da oluşturmuş olursunuz. |
| Olay hub'ı adı | Olay Hub 'ınız çıktısının adı. |
| Olay Hub 'ı ilke adı | Olay Hub 'ının **Yapılandır** sekmesinde oluşturabileceğiniz paylaşılan erişim ilkesi. Her paylaşılan erişim ilkesinin adı, sizin ayarladığınız izinler ve anahtarlara erişim vardır. |
| Olay Hub 'ı ilke anahtarı | Olay Hub 'ı ad alanına erişimin kimliğini doğrulamak için kullanılan paylaşılan erişim anahtarı. |
| Bölüm anahtarı sütunu | İsteğe bağlı. Olay Hub 'ı çıkışı için bölüm anahtarını içeren bir sütun. |
| Olay serileştirme biçimi | Çıkış verileri için serileştirme biçimi. JSON, CSV ve avro desteklenir. |
| Encoding | CSV ve JSON için, UTF-8 şu anda desteklenen tek kodlama biçimidir. |
| Ayırıcı | Yalnızca CSV serileştirme için geçerlidir. Stream Analytics CSV biçimindeki verileri serileştirmek için bir dizi ortak sınırlayıcıları destekler. Desteklenen değerler virgül, noktalı virgül, boşluk, sekme ve dikey çubuklardır. |
| Biçimlendir | Yalnızca JSON serileştirme için geçerlidir. **Satır ayrımı** , çıktının her bir JSON nesnesine yeni bir satırla ayrılmış şekilde biçimlendirildiğini belirtir. **Dizi** , çıktının JSON nesneleri dizisi olarak biçimlendirildiğini belirtir.  |
| Özellik sütunları | İsteğe bağlı. Yük yerine giden iletinin Kullanıcı özellikleri olarak eklenmesi gereken virgülle ayrılmış sütunlar. Bu özellik hakkında daha fazla bilgi, [Çıkış Için özel meta veri özellikleri](#custom-metadata-properties-for-output)bölümünde bulunur. |

## <a name="power-bi"></a>Power BI

Analiz sonuçlarına yönelik zengin bir görselleştirme deneyimi sağlamak üzere bir Stream Analytics iş için çıkış olarak [Power BI](https://powerbi.microsoft.com/) kullanabilirsiniz. Bu özelliği işlemsel panolar, rapor oluşturma ve ölçüm temelli raporlama için kullanabilirsiniz.

Stream Analytics çıkış Power BI Şu anda Azure Çin 21Vianet ve Azure Almanya (T-Systems Uluslararası) bölgelerinde kullanılamıyor.

Aşağıdaki tablo Power BI çıktlarınızı yapılandırmak için özellik adlarını ve açıklamalarını listelemektedir.

| Özellik adı | Açıklama |
| --- | --- |
| Çıktı diğer adı |Sorgu çıkışını bu Power BI çıktısına yönlendirmek için sorgularda kullanılan kolay bir ad sağlayın. |
| Grup çalışma alanı |Diğer Power BI kullanıcılarla veri paylaşmayı etkinleştirmek için Power BI hesabınızda grupları seçebilir veya bir gruba yazmak istemiyorsanız **çalışma alanım** ' ı seçebilirsiniz. Mevcut bir grubun güncelleştirilmesi için Power BI kimlik doğrulamasının yenilenmesi gerekir. |
| Veri kümesi adı |Power BI çıkışının kullanmasını istediğiniz bir veri kümesi adı belirtin. |
| Tablo adı |Power BI çıkışının veri kümesi altında bir tablo adı sağlayın. Şu anda, Stream Analytics işlerden alınan Power BI çıktısı bir veri kümesinde yalnızca bir tablo içerebilir. |
| Bağlantıyı yetkilendir | Çıkış ayarlarınızı yapılandırmak için Power BI yetkilendirme yapmanız gerekir. Bu çıkış Power BI panonuza erişim izni verdiğinizde, Kullanıcı hesabı parolasını değiştirerek, iş çıktısını silerek veya Stream Analytics işini silerek erişimi iptal edebilirsiniz. | 

Power BI çıktısını ve panoyu yapılandırmaya yönelik bir anlatım için, [Azure Stream Analytics ve Power BI](stream-analytics-power-bi-dashboard.md) öğreticisine bakın.

> [!NOTE]
> Veri kümesini ve tabloyu Power BI panosunda açıkça oluşturmayın. Veri kümesi ve tablo, iş başlatıldığında otomatik olarak doldurulur ve iş, Power BI ' a çıktı olarak başlatılır. İş sorgusu herhangi bir sonuç oluşturmazsa, veri kümesi ve tablo oluşturulmaz. Power BI zaten bu Stream Analytics işinde belirtilen adla aynı ada sahip bir veri kümesi ve tablo varsa, varolan verilerin üzerine yazılır.
>

### <a name="create-a-schema"></a>Şema oluşturma
Azure Stream Analytics, zaten mevcut değilse Kullanıcı için bir Power BI veri kümesi ve tablo şeması oluşturur. Diğer tüm durumlarda, tablo yeni değerlerle güncellenir. Şu anda bir veri kümesi içinde yalnızca bir tablo bulunabilir. 

Power BI ilk kez ilk çıkar (FıFO) bekletme ilkesini kullanır. Veriler, 200.000 satır isabetlerine kadar bir tabloda toplanır.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Stream Analytics bir veri türünü Power BI Dönüştür
Azure Stream Analytics, çıkış şeması değişirse veri modelini çalışma zamanında dinamik olarak güncelleştirir. Sütun adı değişiklikleri, sütun türü değişiklikleri ve sütun ekleme veya kaldırma işlemlerinin tümü izlenir.

Bu tablo, bir Power BI veri kümesi ve tablo yoksa [Stream Analytics veri türlerinden](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) Power BI [varlık veri modeli (EDM) türlerine](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)veri türü dönüştürmelerini içerir.

Stream Analytics 'den | Power BI için
-----|-----
bigint | Int64
nvarchar (max) | Dize
datetime | Hem
float | Çift
Kayıt dizisi | Dize türü, sabit değer "IRecord" veya "IArray"

### <a name="update-the-schema"></a>Şemayı güncelleştirme
Stream Analytics, veri modeli şemasını çıkışdaki ilk olay kümesine göre algılar. Daha sonra, gerekirse, veri modeli şeması özgün şemaya sığmayan gelen olayları kapsayacak şekilde güncelleştirilir.

Satırlarda dinamik şema güncelleştirmesini önlemek için `SELECT *` sorgusundan kaçının. Olası performans etkilerine ek olarak, sonuçlar için geçen sürenin açıklanmasına neden olabilir. Power BI panosunda gösterilmesi gereken tam alanları seçin. Ayrıca, veri değerleri seçili veri türüyle uyumlu olmalıdır.


Önceki/geçerli | Int64 | Dize | Hem | Çift
-----------------|-------|--------|----------|-------
Int64 | Int64 | Dize | Dize | Çift
Çift | Çift | Dize | Dize | Çift
Dize | Dize | Dize | Dize | Dize 
Hem | Dize | Dize |  Hem | Dize

## <a name="table-storage"></a>Table Storage

[Azure Tablo depolama](../storage/common/storage-introduction.md) , yüksek oranda kullanılabilir ve yüksek düzeyde ölçeklenebilir depolama sunarak, bir uygulamanın kullanıcı talebini karşılayacak şekilde otomatik olarak ölçeklenebilmesini sağlar. Tablo depolama, Microsoft 'un NoSQL anahtar/öznitelik deposudur ve bu, şema üzerinde daha az kısıtlama sunan yapılandırılmış veriler için kullanabileceğiniz bir veritabanıdır. Azure Tablo depolaması, verileri Kalıcılık ve verimli bir şekilde depolamak için kullanılabilir.

Aşağıdaki tabloda, tablo çıktısı oluşturmaya yönelik özellik adları ve açıklamaları listelenmektedir.

| Özellik adı | Açıklama |
| --- | --- |
| Çıktı diğer adı |Sorgu çıkışını bu tablo depolamasına yönlendirmek için sorgularda kullanılan kolay bir ad. |
| Depolama hesabı |Çıktlarınızı gönderdiğiniz depolama hesabının adı. |
| Depolama hesabı anahtarı |Depolama hesabıyla ilişkilendirilmiş erişim anahtarı. |
| Tablo adı |Tablonun adı. Tablo yoksa oluşturulur. |
| Bölüm anahtarı |Bölüm anahtarını içeren çıkış sütununun adı. Bölüm anahtarı, bir varlığın birincil anahtarının ilk kısmını oluşturan tablo içindeki bölüm için benzersiz bir tanımlayıcıdır. Boyutu 1 KB 'a kadar olabilecek bir dize değeridir. |
| Satır anahtarı |Satır anahtarını içeren çıkış sütununun adı. Satır anahtarı, bir bölüm içindeki bir varlık için benzersiz bir tanımlayıcıdır. Bir varlığın birincil anahtarının ikinci bölümünü oluşturur. Satır anahtarı, boyutu 1 KB 'a kadar olabilecek bir dize değeridir. |
| Toplu iş boyutu |Bir toplu işlem için kayıt sayısı. Varsayılan (100) çoğu iş için yeterlidir. Bu ayarı değiştirme hakkında daha fazla bilgi için bkz. [tablo toplu işlem belirtimi](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation) . |

## <a name="service-bus-queues"></a>Service Bus kuyrukları

[Service Bus kuyruklar](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) , bir veya daha fazla rakip TÜKETICIYE bir FIFO ileti teslimi sunar. Genellikle, iletiler kuyruğa eklendikleri zamana bağlı sırada alıcılar tarafından alınır ve işlenir. Her ileti yalnızca bir ileti tüketicisi tarafından alınır ve işlenir.

Aşağıdaki tabloda, bir kuyruk çıkışı oluşturmaya yönelik özellik adları ve açıklamaları listelenmektedir.

| Özellik adı | Açıklama |
| --- | --- |
| Çıktı diğer adı |Sorgu çıkışını bu Service Bus kuyruğuna yönlendirmek için sorgularda kullanılan kolay bir ad. |
| Service Bus ad alanı |Bir mesajlaşma varlıkları kümesi için kapsayıcı. |
| Kuyruk adı |Service Bus kuyruğunun adı. |
| Kuyruk ilkesi adı |Bir kuyruk oluşturduğunuzda, sıranın **Yapılandır** sekmesinde paylaşılan erişim ilkeleri de oluşturabilirsiniz. Her paylaşılan erişim ilkesinin adı, sizin ayarladığınız izinler ve anahtarlara erişim vardır. |
| Kuyruk İlkesi anahtarı |Service Bus ad alanına erişimin kimliğini doğrulamak için kullanılan paylaşılan erişim anahtarı. |
| Olay serileştirme biçimi |Çıkış verileri için serileştirme biçimi. JSON, CSV ve avro desteklenir. |
| Encoding |CSV ve JSON için, UTF-8 şu anda desteklenen tek kodlama biçimidir. |
| Ayırıcı |Yalnızca CSV serileştirme için geçerlidir. Stream Analytics CSV biçimindeki verileri serileştirmek için bir dizi ortak sınırlayıcıları destekler. Desteklenen değerler virgül, noktalı virgül, boşluk, sekme ve dikey çubuklardır. |
| Biçimlendir |Yalnızca JSON türü için geçerlidir. **Satır ayrımı** , çıktının her bir JSON nesnesine yeni bir satırla ayrılmış şekilde biçimlendirildiğini belirtir. **Dizi** , çıktının JSON nesneleri dizisi olarak biçimlendirildiğini belirtir. |
| Özellik sütunları | İsteğe bağlı. Yük yerine giden iletinin Kullanıcı özellikleri olarak eklenmesi gereken virgülle ayrılmış sütunlar. Bu özellik hakkında daha fazla bilgi, [Çıkış Için özel meta veri özellikleri](#custom-metadata-properties-for-output)bölümünde bulunur. |
| Sistem özelliği sütunları | İsteğe bağlı. Sistem özelliklerinin anahtar değer çiftleri ve yük yerine giden iletiye eklenmesi gereken karşılık gelen sütun adları. Bu özellik hakkında daha fazla bilgi, [Service Bus kuyruğu ve konu çıkışları Için sistem özellikleri](#system-properties-for-service-bus-queue-and-topic-outputs) bölümünde yer almaktadır  |

Bölüm sayısı [SERVICE Bus SKU 'sunu ve boyutunu temel alır](../service-bus-messaging/service-bus-partitioning.md). Bölüm anahtarı, her bölüm için benzersiz bir tamsayı değeridir.

## <a name="service-bus-topics"></a>Service Bus Konuları
Service Bus kuyruklar, gönderenden alıcıya bire bir iletişim yöntemi sağlar. [Service Bus konular](https://msdn.microsoft.com/library/azure/hh367516.aspx) bire çok bir iletişim biçimi sağlar.

Aşağıdaki tabloda, Service Bus konu çıkışı oluşturmaya yönelik özellik adları ve açıklamaları listelenmektedir.

| Özellik adı | Açıklama |
| --- | --- |
| Çıktı diğer adı |Sorgu çıkışını bu Service Bus konusuna yönlendirmek için sorgularda kullanılan kolay bir ad. |
| Service Bus ad alanı |Bir mesajlaşma varlıkları kümesi için kapsayıcı. Yeni bir olay hub 'ı oluşturduğunuzda bir Service Bus ad alanı da oluşturmuş olursunuz. |
| Konu adı |Konular, Olay Hub 'larına ve kuyruklara benzer şekilde mesajlaşma varlıklarıdır. Bunlar, cihazlardan ve hizmetlerden olay akışları toplamak üzere tasarlanmıştır. Bir konu oluşturulduğunda, belirli bir ad da verilir. Bir konuya gönderilen iletiler bir abonelik oluşturulmadığı takdirde kullanılabilir değildir, bu nedenle konunun altında bir veya daha fazla abonelik olduğundan emin olun. |
| Konu ilkesi adı |Bir Service Bus konu oluşturduğunuzda, konunun **Yapılandır** sekmesinde paylaşılan erişim ilkeleri de oluşturabilirsiniz. Her paylaşılan erişim ilkesinin adı, sizin ayarladığınız izinler ve anahtarlara erişim vardır. |
| Konu İlkesi anahtarı |Service Bus ad alanına erişimin kimliğini doğrulamak için kullanılan paylaşılan erişim anahtarı. |
| Olay serileştirme biçimi |Çıkış verileri için serileştirme biçimi. JSON, CSV ve avro desteklenir. |
| Encoding |CSV veya JSON biçimi kullanıyorsanız, bir kodlama belirtilmesi gerekir. Şu anda desteklenen tek kodlama biçimi UTF-8 ' i destekler. |
| Ayırıcı |Yalnızca CSV serileştirme için geçerlidir. Stream Analytics CSV biçimindeki verileri serileştirmek için bir dizi ortak sınırlayıcıları destekler. Desteklenen değerler virgül, noktalı virgül, boşluk, sekme ve dikey çubuklardır. |
| Özellik sütunları | İsteğe bağlı. Yük yerine giden iletinin Kullanıcı özellikleri olarak eklenmesi gereken virgülle ayrılmış sütunlar. Bu özellik hakkında daha fazla bilgi, [Çıkış Için özel meta veri özellikleri](#custom-metadata-properties-for-output)bölümünde bulunur. |
| Sistem özelliği sütunları | İsteğe bağlı. Sistem özelliklerinin anahtar değer çiftleri ve yük yerine giden iletiye eklenmesi gereken karşılık gelen sütun adları. Bu özellik hakkında daha fazla bilgi, [Service Bus kuyruğu ve konu çıkışları Için sistem özellikleri](#system-properties-for-service-bus-queue-and-topic-outputs) bölümünde yer almaktadır |

Bölüm sayısı [SERVICE Bus SKU 'sunu ve boyutunu temel alır](../service-bus-messaging/service-bus-partitioning.md). Bölüm anahtarı, her bölüm için benzersiz bir tamsayı değeridir.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) , dünya genelinde sınırsız esnek ölçeklendirme, zengin sorgu ve şema belirsiz veri modelleri üzerinden otomatik dizin oluşturma olanağı sunan, küresel olarak dağıtılmış bir veritabanı hizmetidir. Stream Analytics için Azure Cosmos DB kapsayıcı seçenekleri hakkında bilgi edinmek için, [çıkış olarak Azure Cosmos DB Stream Analytics](stream-analytics-documentdb-output.md) bakın.

Stream Analytics çıkış Azure Cosmos DB Şu anda Azure Çin 21Vianet ve Azure Almanya (T-Systems Uluslararası) bölgelerinde kullanılamıyor.

> [!Note]
> Şu anda Azure Stream Analytics yalnızca SQL API 'sini kullanarak Azure Cosmos DB bağlantısını destekler.
> Diğer Azure Cosmos DB API 'Leri henüz desteklenmiyor. Diğer API 'lerle oluşturulan Azure Cosmos DB hesaplara Azure Stream Analytics işaret ediyorsanız, veriler düzgün şekilde depolanmayabilir.

Aşağıdaki tabloda Azure Cosmos DB çıktısı oluşturma özellikleri açıklanmaktadır.

| Özellik adı | Açıklama |
| --- | --- |
| Çıktı diğer adı | Stream Analytics sorgunuzda bu çıktıyı ifade eden bir diğer ad. |
| Havuz | Azure Cosmos DB. |
| İçeri aktarma seçeneği | **Aboneliğinizden Cosmos DB seçin** ya da **Cosmos DB ayarları el ile sağlayın**.
| Hesap Kimliği | Azure Cosmos DB hesabının adı veya uç nokta URI 'SI. |
| Hesap anahtarı | Azure Cosmos DB hesabının paylaşılan erişim anahtarı. |
| Database | Azure Cosmos DB veritabanı adı. |
| Kapsayıcı adı | Cosmos DB olması gereken, kullanılacak kapsayıcı adı. Örnek:  <br /><ul><li> _MyContainer_: "myContainer" adlı bir kapsayıcı var olmalıdır.</li>|
| Belge KIMLIĞI |İsteğe bağlı. Ekleme veya güncelleştirme işlemlerinin dayandığı birincil anahtarı belirtmek için kullanılan çıkış olaylarında alanın adı.

## <a name="azure-functions"></a>Azure İşlevleri
Azure Işlevleri, altyapıyı açıkça sağlamak veya yönetmek zorunda kalmadan kodu isteğe bağlı olarak çalıştırmak için kullanabileceğiniz sunucusuz bir işlem hizmetidir. Azure veya iş ortağı hizmetlerinde gerçekleşen olaylar tarafından tetiklenen kodu uygulamanıza olanak tanır. Azure Işlevlerinin tetikleyicilere yanıt vermesi bu özelliği Azure Stream Analytics için doğal bir çıkış yapar. Bu çıkış bağdaştırıcısı, kullanıcıların Azure Işlevlerine Stream Analytics bağlanmasına ve çeşitli olaylara yanıt olarak bir betiği ya da kod parçasını çalıştırmasına olanak sağlar.

Azure Işlevleri Stream Analytics çıkışı, Azure Çin 21Vianet ve Azure Almanya (T-Systems Uluslararası) bölgelerinde Şu anda kullanılamıyor.

Azure Stream Analytics HTTP Tetikleyicileri aracılığıyla Azure Işlevlerini çağırır. Azure Işlevleri çıkış bağdaştırıcısı, aşağıdaki yapılandırılabilir özelliklerle kullanılabilir:

| Özellik adı | Açıklama |
| --- | --- |
| İşlev uygulaması |Azure Işlevleri uygulamanızın adı. |
| İşlev |Azure Işlevleri uygulamanızdaki işlevin adı. |
| Anahtar |Başka bir abonelikteki bir Azure Işlevi kullanmak istiyorsanız, işlevinizin erişimine yönelik anahtarı sağlayarak bunu yapabilirsiniz. |
| En fazla toplu iş boyutu |Azure işlevinizde gönderilen her çıkış toplu işi için en büyük boyutu ayarlamanızı sağlayan bir özellik. Giriş birimi bayt cinsinden. Varsayılan olarak, bu değer 262.144 bayttır (256 KB). |
| En fazla toplu iş sayısı  |Azure Işlevlerine gönderilen her bir yığında en fazla olay sayısını belirtmenize olanak tanıyan bir özellik. Varsayılan değer 100’dür. |

Azure Stream Analytics bir Azure işlevinden 413 ("http Istek varlığı çok büyük") özel durumu aldığında, Azure Işlevlerine gönderdiği toplu işlerin boyutunu azaltır. Azure işlev kodunuzda Azure Stream Analytics büyük bir toplu iş göndermediğinden emin olmak için bu özel durumu kullanın. Ayrıca, işlevde kullanılan en büyük toplu iş sayısı ve boyut değerlerinin Stream Analytics portalına girilen değerlerle tutarlı olduğundan emin olun.

> [!NOTE]
> Sınama bağlantısı sırasında, Stream Analytics iki çalışma arasındaki bağlantının test olması için Azure Işlevlerine boş bir toplu işlem gönderir. Test bağlantısının başarılı olduğundan emin olmak için Işlevler uygulamanızın boş toplu iş isteklerini işlediğinizden emin olun.

Ayrıca, bir zaman penceresinde olay sahanlığı olmadığı durumlarda, hiçbir çıkış oluşturulmaz. Sonuç olarak, **Computeresult** işlevi çağrılmaz. Bu davranış yerleşik pencere toplamı işlevleriyle tutarlıdır.

## <a name="custom-metadata-properties-for-output"></a>Çıkış için özel meta veri özellikleri 

Sorgu sütunlarını, giden iletilerinize Kullanıcı özellikleri olarak ekleyebilirsiniz. Bu sütunlar yük içine gitmez. Özellikler, çıkış iletisindeki bir sözlük biçiminde bulunur. *Anahtar* , sütun adı ve *değer* Özellikler sözlüğündeki sütun değeridir. Tüm Stream Analytics veri türleri, kayıt ve dizi dışında desteklenir.  

Desteklenen çıkışlar: 
* Service Bus kuyruğu 
* Service Bus konusu 
* Olay hub'ı 

Aşağıdaki örnekte, iki alanı `DeviceId` ve meta verilere `DeviceStatus` ekleyeceğiz. 
* Sorgu: `select *, DeviceId, DeviceStatus from iotHubInput`
* Çıkış yapılandırması: `DeviceId,DeviceStatus`

![Özellik sütunları](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Aşağıdaki ekran görüntüsünde, [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer)aracılığıyla EventHub ' de incelenen çıkış iletisi özellikleri gösterilmektedir.

![Olay özel özellikleri](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Service Bus kuyruğu ve konu çıkışları için sistem özellikleri 
Sorgu sütunlarını, giden hizmet veri yolu kuyruğunuza veya konu iletilerinize [Sistem Özellikleri](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) olarak iliştirebilirsiniz. Bu sütunlar, karşılık gelen BrokeredMessage [sistem özelliği](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) sorgu sütunu değerleriyle doldurulduğundan yük içine gitmez.
Bu sistem özellikleri desteklenir-`MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`.
Bu sütunların dize değerleri karşılık gelen sistem özelliği değer türü olarak ayrıştırılır ve Ayrıştırma hataları veri hatası olarak değerlendirilir.
Bu alan JSON nesne biçimi olarak sağlanır. Bu biçimle ilgili ayrıntılar aşağıdaki gibidir.
* Küme ayraçları {}çevrelenmiş.
* Anahtar/değer çiftlerinde yazılmıştır.
* Anahtarlar ve değerler dize olmalıdır.
* Anahtar, sistem özelliği adıdır ve değer sorgu sütunu adıdır.
* Anahtarlar ve değerler iki nokta üst üste ile ayrılır.
* Her anahtar/değer çifti virgülle ayrılır.

Bu özelliğin nasıl kullanılacağını gösterir –

* Sorgu: `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Sistem özelliği sütunları: `{ "MessageId": "column1", "PartitionKey": "column2"}`

Bu, `column1`değerleri ve PartitionKey ile Service Bus kuyruğu iletilerinin `MessageId` `column2`değerleriyle ayarlanır.

## <a name="partitioning"></a>Bölümleme

Aşağıdaki tabloda, her bir çıkış türü için bölüm desteği ve çıkış yazıcısı sayısı özetlenmektedir:

| Çıkış türü | Bölümlendirme desteği | Bölüm anahtarı  | Çıkış yazıcı sayısı |
| --- | --- | --- | --- |
| Azure Data Lake Store | Evet | Yol öneki düzeninde {Date} ve {Time} belirteçlerini kullanın. YYYY/AA/GG, GG/AA/YYYY veya AA-GG-YYYY gibi bir tarih biçimi seçin. SS, saat biçimi için kullanılır. | [Tam paralelleştirilebilir sorgular](stream-analytics-scale-jobs.md)için giriş bölümlemesini izler. |
| Azure SQL Veritabanı | Evet, etkin olmalıdır. | Sorgudaki bölüm BY yan tümcesine göre. | Bölümleme seçeneği etkin olduğunda, [tam paralelleştirilebilir sorgular](stream-analytics-scale-jobs.md)için giriş bölümlemesini izler. Azure SQL veritabanı 'na veri yüklerken daha iyi yazma performansı elde etmek hakkında daha fazla bilgi edinmek için bkz. [Azure SQL veritabanı 'na Azure Stream Analytics çıktı](stream-analytics-sql-output-perf.md). |
| Azure Blob depolama | Evet | Yol deseninin olay alanlarınızın {Date} ve {Time} belirteçlerini kullanın. YYYY/AA/GG, GG/AA/YYYY veya AA-GG-YYYY gibi bir tarih biçimi seçin. SS, saat biçimi için kullanılır. Blob çıktısı, {FieldName} veya {DateTime:\<belirleyicisi >} tek bir özel olay özniteliğiyle bölümlenebilir. | [Tam paralelleştirilebilir sorgular](stream-analytics-scale-jobs.md)için giriş bölümlemesini izler. |
| Azure Event Hubs | Evet | Evet | Bölüm hizalamasına göre değişir.<br /> Olay Hub 'ı çıkışı için bölüm anahtarı, yukarı akış (önceki) sorgu adımıyla eşit olarak hizalandığında, yazıcı sayısı, Olay Hub 'ı çıkışındaki bölüm sayısıyla aynıdır. Her yazıcı, olayları belirli bir bölüme göndermek için [Eventhubsender sınıfını](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) kullanır. <br /> Olay Hub 'ı çıkışı için bölüm anahtarı, yukarı akış (önceki) sorgu adımıyla hizalanmazsa, yazıcı sayısı önceki adımdaki bölüm sayısıyla aynıdır. Her yazıcı, olayları tüm çıkış bölümlerine göndermek için **Eventhubclient** Içindeki [sendbatchasync sınıfını](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) kullanır. |
| Power BI | Hayır | None | Uygulanamaz. |
| Azure Tablo depolama | Evet | Herhangi bir çıkış sütunu.  | [Tam paralelleştirilmiş sorgular](stream-analytics-scale-jobs.md)için giriş bölümlemesini izler. |
| Azure Service Bus konusu | Evet | Otomatik olarak seçildi. Bölüm sayısı [SERVICE Bus SKU 'sunu ve boyutunu](../service-bus-messaging/service-bus-partitioning.md)temel alır. Bölüm anahtarı, her bölüm için benzersiz bir tamsayı değeridir.| Çıktı konusundaki bölüm sayısıyla aynı.  |
| Azure Service Bus kuyruğu | Evet | Otomatik olarak seçildi. Bölüm sayısı [SERVICE Bus SKU 'sunu ve boyutunu](../service-bus-messaging/service-bus-partitioning.md)temel alır. Bölüm anahtarı, her bölüm için benzersiz bir tamsayı değeridir.| Çıktı sırasındaki bölüm sayısıyla aynı. |
| Azure Cosmos DB | Evet | Sorgudaki bölüm BY yan tümcesine göre. | [Tam paralelleştirilmiş sorgular](stream-analytics-scale-jobs.md)için giriş bölümlemesini izler. |
| Azure İşlevleri | Evet | Sorgudaki bölüm BY yan tümcesine göre. | [Tam paralelleştirilmiş sorgular](stream-analytics-scale-jobs.md)için giriş bölümlemesini izler. |

Çıktı yazıcılarının sayısı Sorgunuzdaki `INTO <partition count>` [(bkz.](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) yan tümcesi kullanılarak denetlenebilir ve bu da istenen iş topolojisini elde etmek için yararlı olabilir. Çıkış bağdaştırıcınız bölümlendirilmemişse, bir giriş bölümünde verilerin bulunmaması, geç varış süresinin sonuna kadar gecikmeye neden olur. Bu gibi durumlarda, çıkış tek bir yazıcı ile birleştirilir ve bu da işlem hattınızda performans sorunlarına neden olabilir. Geç alma ilkesi hakkında daha fazla bilgi için bkz. [Azure Stream Analytics olay sırası konuları](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Çıkış toplu iş boyutu
Azure Stream Analytics, olayları işlemek ve çıkışlara yazmak için değişken boyutlu toplu işler kullanır. Genellikle Stream Analytics altyapısı tek seferde bir ileti yazmaz ve verimlilik için toplu işler kullanır. Hem gelen hem de giden olayların oranı yüksek olduğunda Stream Analytics daha büyük toplu işler kullanır. Çıkış hızı düşük olduğunda, gecikme süresini düşük tutmak için daha küçük toplu işler kullanır.

Aşağıdaki tabloda çıktı toplu işleme ilgili bazı hususlar açıklanmaktadır:

| Çıkış türü | En büyük ileti boyutu | Toplu işlem boyutu iyileştirmesi |
| :--- | :--- | :--- |
| Azure Data Lake Store | [Data Lake Storage sınırlara](../azure-subscription-service-limits.md#data-lake-store-limits)bakın. | Yazma işlemi başına en fazla 4 MB kullanın. |
| Azure SQL Veritabanı | En fazla toplu iş sayısı kullanılarak yapılandırılabilir. Varsayılan olarak, tek toplu ekleme başına en fazla 10.000 ve 100 en az satır.<br />Bkz. [Azure SQL sınırları](../sql-database/sql-database-resource-limits.md). |  Her toplu iş ilk olarak en fazla toplu iş sayısı ile toplu olarak eklenir. Batch, SQL 'deki yeniden denenebilir hatalara göre yarı (en az toplu işlem sayısına kadar) yarıya bölünür. |
| Azure Blob depolama | Bkz. [Azure depolama sınırları](../azure-subscription-service-limits.md#storage-limits). | En büyük blob blok boyutu 4 MB 'tır.<br />Maksimum blob Bock sayısı 50.000 ' dir. |
| Azure Event Hubs  | ileti başına 256 KB veya 1 MB. <br />[Event Hubs sınırlara](../event-hubs/event-hubs-quotas.md)bakın. |  Giriş/çıkış bölümleme hizalı olmadığında, her olay `EventData` tek tek paketlenmiştir ve en fazla ileti boyutuna bir toplu işte gönderilir. [Özel meta veri özellikleri](#custom-metadata-properties-for-output) kullanılıyorsa bu da olur. <br /><br />  Giriş/çıkış bölümlendirme hizalandığında, birden çok olay tek bir `EventData` örneğine paketlenmiştir, en fazla ileti boyutuna kadar ve gönderilir. |
| Power BI | Bkz. [Power BI REST API sınırları](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Tablo depolama | Bkz. [Azure depolama sınırları](../azure-subscription-service-limits.md#storage-limits). | Varsayılan değer tek bir işlem başına 100 varlıkdır. Gerektiğinde daha küçük bir değere yapılandırabilirsiniz. |
| Azure Service Bus kuyruğu   | Standart katman için ileti başına 256 KB, Premium katman için 1MB.<br /> [Service Bus sınırlara](../service-bus-messaging/service-bus-quotas.md)bakın. | İleti başına tek bir olay kullanın. |
| Azure Service Bus konusu | Standart katman için ileti başına 256 KB, Premium katman için 1MB.<br /> [Service Bus sınırlara](../service-bus-messaging/service-bus-quotas.md)bakın. | İleti başına tek bir olay kullanın. |
| Azure Cosmos DB   | [Azure Cosmos DB sınırlara](../azure-subscription-service-limits.md#azure-cosmos-db-limits)bakın. | Toplu iş boyutu ve yazma sıklığı Azure Cosmos DB yanıtlara göre dinamik olarak ayarlanır. <br /> Stream Analytics için önceden tanımlanmış bir sınırlama yoktur. |
| Azure İşlevleri   | | Varsayılan yığın boyutu 262.144 bayttır (256 KB). <br /> Batch başına varsayılan olay sayısı 100 ' dir. <br /> Toplu iş boyutu yapılandırılabilir ve Stream Analytics [çıktı seçeneklerinde](#azure-functions)artırılabilir veya azaltılabilir.

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> 
> [Hızlı başlangıç: Azure portal kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
