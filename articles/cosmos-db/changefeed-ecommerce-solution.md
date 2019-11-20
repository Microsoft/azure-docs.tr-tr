---
title: Kullanım Azure Cosmos DB değişiklik akışı, gerçek zamanlı veri analizi görselleştirmek için
description: Değişiklik akışı bir perakende şirketi tarafından nasıl kullanılabileceğini kullanıcı desenlerini öğrenir, gerçek zamanlı veri analizi ve görselleştirme gerçekleştirmek için bu makalede açıklanır.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: 86d4dd706b097891db155214e4edb7e85e054858
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616942"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Kullanım Azure Cosmos DB değişiklik akışı, gerçek zamanlı veri analizi görselleştirmek için

Azure Cosmos DB değişiklik akışı, bu kayıtlar oluşturulduğu veya değiştirildiği için Azure Cosmos kapsayıcısından sürekli ve artımlı bir kayıt akışı almanın bir mekanizmadır. Değişiklik, herhangi bir değişiklik kapsayıcıya dinleyerek destek works akış. Ardından, değiştirilmiş olan sırayla değiştirilen belgelerin sıralanmış listesini çıkarır. Değişiklik akışı hakkında daha fazla bilgi için bkz: [değişiklik akışı ile çalışma](change-feed.md) makalesi. 

Değişiklik akışı bir e-ticaret şirket tarafından nasıl kullanılabileceğini kullanıcı desenlerini öğrenir, gerçek zamanlı veri analizi ve görselleştirme gerçekleştirmek için bu makalede açıklanır. Bir kullanıcı bir öğeyi görüntüleme, kendi sepetine öğe ekleme veya bir öğe satın alma gibi olayları analiz eder. Bu olaylardan biri oluştuğunda, yeni bir kayıt oluşturulur ve kayıt günlükleri değişiklik akışı. Değişiklik, ardından bir dizi adım etkinlik ve şirket performansı analiz ölçümleri görselleştirmede kaynaklanan Tetikleyicileri akış. Gelir, benzersiz bir site ziyaretçilerinin, en popüler öğeleri görselleştirebilir miyim örnek ölçümler içerir ve karşı bir sepet karşı eklenen görüntülenen öğelerin ortalama fiyat satın. Bu örnek ölçümler, site popüler değerlendirmek, reklam ve fiyatlandırma stratejilerini geliştirin ve yatırım yapmaya hangi envanteri ile ilgili kararlar bir e-ticaret şirket yardımcı olabilir.

Başlarken önce çözüm ilgili bir video bakın şu videoyu izlerken ilgi:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Çözüm bileşenleri
Aşağıdaki diyagramda, veri akışı ve çözümde ilgili bileşenleri temsil eder:

![Proje visual](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **Veri oluşturma:** Veri simülatörü, bir kullanıcının bir öğeyi görüntüleme, sepetine bir öğe ekleme ve bir öğe satın alma gibi olayları temsil eden perakende verileri oluşturmak için kullanılır. Veri oluşturucuyu kullanarak büyük örnek veri kümesi oluşturabilirsiniz. Oluşturulan örnek veriler, aşağıdaki biçimde belgeleri içerir:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB:** Oluşturulan veriler bir Azure Cosmos kapsayıcısında depolanır.  

3. **Değişiklik akışı:** Değişiklik akışı, Azure Cosmos kapsayıcısındaki değişiklikleri dinler. Her seferinde yeni bir belge (yani gibi bir öğe görüntüleyen bir kullanıcı bir olay meydana geldiğinde bunların sepetine öğe ekleme veya bir öğe satın alma) koleksiyona eklendiğinde, değişiklik akışı tetikleyen bir [Azure işlevi](../azure-functions/functions-overview.md).  

4. **Azure Işlevi:** Azure Işlevi, yeni verileri işler ve bunu bir [Azure Olay Hub 'ına](../event-hubs/event-hubs-about.md)gönderir.  

5. **Olay Hub 'ı:** Azure Olay Hub 'ı bu olayları depolar ve daha fazla analiz yapmak için bunları [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) gönderir.  

6. **Azure Stream Analytics:** Azure Stream Analytics olayları işlemek ve gerçek zamanlı veri analizi gerçekleştirmek için sorguları tanımlar. Bu veriler daha sonra gönderilir [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop).  

7. **Power BI:** Power BI, Azure Stream Analytics tarafından gönderilen verileri görselleştirmek için kullanılır. Ölçümleri gerçek zamanlı olarak nasıl değiştiğini görmek için bir Pano oluşturabilirsiniz.  

## <a name="prerequisites"></a>Önkoşullar

* Microsoft .NET Framework 4.7.1 veya üzeri

* Microsoft .NET Core 2.1 (veya üzeri)

* Visual Studio ile Evrensel Windows platformu geliştirme, .NET Masaüstü uygulama geliştirme ve ASP.NET ve web geliştirme iş yükleri

* Microsoft Azure aboneliği

* Microsoft Power BI hesabı

* İndirme [Azure Cosmos DB değişiklik akışı Laboratuvar](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) github'dan. 

## <a name="create-azure-resources"></a>Azure kaynakları oluşturma 

Azure kaynakları - Azure Cosmos DB, depolama hesabı, olay hub'ı Stream Analytics çözüm için gerekli oluşturun. Bu kaynakları Azure Resource Manager şablonu aracılığıyla dağıtır. Bu kaynakları dağıtmak için aşağıdaki adımları kullanın: 

1. Windows PowerShell yürütme İlkesi ayarlamak **Kısıtlanmamış**. Bunu yapmak için açık **Windows PowerShell'i yönetici olarak** ve aşağıdaki komutları çalıştırın:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Önceki adımda indirdiğiniz GitHub deposundan gidin **Azure Resource Manager** klasör ve dosya adında açın **parameters.json** dosya.  

3. Değerleri cosmosdbaccount_name eventhubnamespace_name, storageaccount_name, parametreler için belirtilen şekilde sağlayın **parameters.json** dosya. Her kaynaklarınızın daha sonra size adlarını kullanmanız gerekir.  

4. Gelen **Windows PowerShell**, gitmek **Azure Resource Manager** klasörü ve aşağıdaki komutu çalıştırın:

   ```powershell
   .\deploy.ps1
   ```
5. İstendiğinde Azure girin **abonelik kimliği**, **changefeedlab** kaynak grubu adı için ve **Run1 olarak** dağıtım adı. Dağıtılacak kaynaklar başladıktan sonra tamamlanması 10 dakikaya kadar beklemeniz gerekebilir.

## <a name="create-a-database-and-the-collection"></a>Bir veritabanı ve koleksiyonu oluşturma

Şimdi e-ticaret sitesi olayları tutmak için bir koleksiyon oluşturacaksınız. Bir kullanıcı bir öğeyi görüntüler, kendi karta bir öğe ekler veya öğeyi satın koleksiyonu eylemi ("görüntülenenler", "eklenmesi" veya "satın alınan") içeren bir kaydı alır, öğenin adını dahil, katılan öğe ve kullanıcı sepet i kimlik numarasını fiyatı nvolved.

1. Git [Azure portalı](https://portal.azure.com/) ve bulma **Azure Cosmos DB hesabı** şablon dağıtımı tarafından oluşturulur.  

2. Gelen **Veri Gezgini** bölmesinde **yeni koleksiyon** ve aşağıdaki ayrıntılarla formu doldurun:  

   * İçin **veritabanı kimliği** alanın, Seç **Yeni Oluştur**, enter **changefeedlabdatabase**. Bırakın **sağlama veritabanı aktarım hızını** kutusunu işaretlenmemiş.  
   * İçin **koleksiyon** kimliği girin **changefeedlabcollection**.  
   * İçin **bölüm anahtarı** alanına **/Item**. Emin doğru girmeniz için büyük/küçük harfe, budur.  
   * İçin **aktarım hızı** alanına **10000**.  
   * **Tamam** düğmesini seçin.  

3. Ardından adlı başka bir koleksiyon oluşturun **kiraları** değişiklik akışı işleme için. Değişiklik arasında birden fazla çalışana akışı işleme kiralamalar koleksiyonunu düzenler. Ayrı bir koleksiyon ile bir kira bölüm başına kiraları depolamak için kullanılır.  

4. Geri dönüp **Veri Gezgini** bölmesi ve select **yeni koleksiyon** ve aşağıdaki ayrıntılarla formu doldurun:

   * İçin **veritabanı kimliği** alanın, Seç **var olanı kullan**, enter **changefeedlabdatabase**.  
   * İçin **koleksiyon kimliği** alanına **kiraları**.  
   * İçin **depolama kapasitesi**seçin **sabit**.  
   * Bırakın **aktarım hızı** alanını varsayılan değerine ayarlayın.  
   * **Tamam** düğmesini seçin.

## <a name="get-the-connection-string-and-keys"></a>Anahtarlar ve bağlantı dizesini alma

### <a name="get-the-azure-cosmos-db-connection-string"></a>Azure Cosmos DB bağlantı dizesini alın

1. Git [Azure portalı](https://portal.azure.com/) ve bulma **Azure Cosmos DB hesabı** şablon dağıtımı tarafından oluşturulur.  

2. Gidin **anahtarları** bölmesinde PRIMARY CONNECTION STRING'i kopyalayın ve not defterine veya başka bir belgeye erişimi Laboratuvar boyunca olacağını kopyalayın. Etiket **Cosmos DB bağlantı dizesi**. Dize kodunuza daha sonra kopyalayın, bu nedenle bir yere not alın ve burada depoladığını unutmayın gerekecektir.

### <a name="get-the-storage-account-key-and-connection-string"></a>Depolama hesabı anahtar ve bağlantı dizesini alma

Azure depolama hesapları, kullanıcıların verileri depolamak izin verin. Bu laboratuvarda bir Azure işlevi tarafından kullanılan verileri depolamak için bir depolama hesabı kullanır. Koleksiyona bir değişiklik yapıldığında Azure işlevi tetiklenir.

1. Kaynak grubunuzun dönün ve daha önce oluşturduğunuz depolama hesabı açın  

2. Seçin **erişim anahtarları** sol taraftaki menüden.  

3. Altında değerleri kopyalayın **anahtar 1** bir Not Defteri'ne veya başka bir belgede Laboratuvar boyunca erişim gerekir. Etiket **anahtarı** olarak **depolama anahtarı** ve **bağlantı dizesi** olarak **depolama bağlantı dizesi**. Bu dizeler kodunuza daha sonra kopyalayın, bu nedenle bir yere not alın ve bunları nereye depoladığını unutmayın gerekir.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Olay hub'ı ad alanı bağlantı dizesini alma

Bir Azure olay hub'ı alır olay verileri, depolar, işlemler ve veri iletir. Yeni bir olay her gerçekleştiğinde bir belge bu laboratuvarda, Azure olay hub'ı alır (yani bir öğe tarafından bir kullanıcı, bir kullanıcının sepetine eklendi veya bir kullanıcı tarafından satın alınan) ve ardından bu belgede Azure Stream Analytics'e iletir.

1. Kaynak grubunuzun döndürür ve açın **olay hub'ı Namespace** oluşturduğunuz ve içinde prelab adlı.  

2. Seçin **paylaşılan erişim ilkeleri** sol taraftaki menüden.  

3. Seçin **RootManageSharedAccessKey**. Kopyalama **bağlantı dizesi-birincil anahtar** bir Not Defteri'ne veya başka bir belgede Laboratuvar boyunca erişim gerekir. Etiket **olay hub'ı Namespace** bağlantı dizesi. Dize kodunuza daha sonra kopyalayın, bu nedenle bir yere not alın ve burada depoladığını unutmayın gerekecektir.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Azure işlevi değişiklik akışını okumak için ayarlama

Yeni bir belge oluşturulduğunda veya Cosmos kapsayıcısında geçerli bir belge değiştirildiğinde, değişiklik akışı otomatik olarak bu değiştirilen belgeyi koleksiyon değişikliklerinin geçmişine ekler. Şimdi yapı ve değişiklik akışı işleyen bir Azure işlevi çalıştırın. Bir belgede oluşturulduğunda veya değiştirildiğinde, oluşturduğunuz koleksiyonda, değişiklik akışı ile Azure işlevi tetiklenir. Ardından Azure işlevi değiştirilmiş belge olay Hub'ına gönderir.

1. Cihazınızda kopyalanmış depoya döndürür.  

2. Adlı dosyayı sağ **ChangeFeedLabSolution.sln** seçip **açık ile Visual Studio**.  

3. Gidin **local.settings.json** Visual Studio'da. Ardından, daha önce kaydettiğiniz boşlukları doldurmak için değerleri kullanın.  

4. Gidin **ChangeFeedProcessor.cs**. Parametrelerde **çalıştırma** işlev, aşağıdaki eylemleri gerçekleştirin:  

   * Metni Değiştir **burada bilgisayarınızı KOLEKSİYON adı** koleksiyonunuzun ada sahip. Önceki yönergeleri izlediyseniz, koleksiyonunuzu changefeedlabcollection adıdır.  
   * Metni Değiştir **bilgisayarınızı KİRALARI KOLEKSİYON adını buraya** kiraları koleksiyonunuzun ada sahip. Önceki yönergeleri izlediyseniz, kiralarını koleksiyonuna adıdır **kiraları**.  
   * Visual Studio'nun üstünde sol tarafında yeşil ok başlangıç projesi kutusuna yazıp yazmadığını emin **ChangeFeedFunction**.  
   * Seçin **Başlat** programı çalıştırmak için sayfanın üst kısmındaki  
   * Konsol uygulaması "proje konak started" ifadesini içeren zaman işlevin çalıştığını doğrulayabilirsiniz.

## <a name="insert-data-into-azure-cosmos-db"></a>Azure Cosmos DB'ye veri ekleme 

Görmek için nasıl değişiklik akışı bir e-ticaret sitesinde yeni eylemler işler, ürün kataloğu öğeleri görüntüleme, kendi sepetleri için öğeleri ekleyerek ve kendi sepetleri öğeleri satın kullanıcıları temsil eden veri benzetimini yapmak vardır. Bu rastgele verilerdir ve bir e-ticaret verileri hangi çoğaltmak amacıyla site gibi görünür.

1. Dosya Gezgini'nde depoyu geri gidin ve sağ tıklayarak **ChangeFeedFunction.sln** tekrar yeni bir Visual Studio penceresi açın.  

2. **App. config** dosyasına gidin. Bloğu içinde, daha önce aldığınız Azure Cosmos DB hesabınızın uç noktasını ve benzersiz **birincil anahtarını** ekleyin. `<appSettings>`  

3. Ekleme **koleksiyon** ve **veritabanı** adları. (Bu adlar olmalıdır **changefeedlabcollection** ve **changefeedlabdatabase** sizinki farklı ad seçmediğiniz sürece.)

   ![Bağlantı dizelerini güncelleştir](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. Düzenlenen tüm dosyalarda değişiklikleri kaydedin.  

5. Visual Studio üstüne emin **başlangıç projesi** kutusunun sol tarafında yeşil ok diyor **DataGenerator**. Ardından **Başlat** programı çalıştırmak için sayfanın üst kısmındaki.  
 
6. Programı çalıştırmak için bekleyin. Yıldız veri geldiğinden emin anlamına gelir! Çalışan bir program koruyun - büyük miktarda veri toplanır önemlidir.  

7. İçin giderseniz, [Azure portalı](https://portal.azure.com/) , ardından Cosmos DB için hesap, kaynak grubu içinde ardından için **Veri Gezgini**, uygulamasında alınan rastgele verileri görebilir,  **changefeedlabcollection** .
 
   ![Portalda oluşturulan verileri](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>Bir stream analytics işi ayarlayın

Azure Stream Analytics, akış verilerini gerçek zamanlı işleme için bir tam olarak yönetilen bir bulut hizmetidir. Bu laboratuvarda, akış analizi, görselleştirme için Power BI'a şirketlerde (yani öğenin görüntülenebilir, bir karta eklendiğinde veya satın olduğunda), olay hub'ı yeni olayları işlemeye ve olaylar gerçek zamanlı veri analizi dahil edilip derecelendirilir için kullanır.

1. Gelen [Azure portalı](https://portal.azure.com/), kaynak grubuna gidin **streamjob1** (prelab içinde oluşturduğunuz stream analytics işi).  

2. Seçin **girişleri** aşağıda gösterildiği gibi.  

   ![Girdi oluşturma](./media/changefeed-ecommerce-solution/create-input.png)

3. Seçin **+ akış Girişi Ekle**. Ardından **olay hub'ı** aşağı açılan menüden.  

4. Yeni giriş formunu aşağıdaki ayrıntılarla doldurun:

   * İçinde **giriş** diğer ad alanına, **giriş**.  
   * Seçeneğini **aboneliklerinizden olay Hub'ı seçin**.  
   * Ayarlama **abonelik** aboneliğinize alan.  
   * İçinde **olay hub'ı ad alanı** prelab sırasında oluşturduğunuz, olay hub'ı Namespace adını girin.  
   * İçinde **olay hub'ı adı** alan, seçeneğini **var olanı kullan** ve **olay hub1** aşağı açılan menüden.  
   * Bırakın **olay hub'ı ilke** ad alanını varsayılan değerine ayarlayın.  
   * Bırakın **olay serileştirme biçimi** olarak **JSON**.  
   * Bırakın **Encoding alanı** kümesine **UTF-8**.  
   * Bırakın **olay sıkıştırma türü** alan kümesine **hiçbiri**.  
   * **Kaydet** düğmesini seçin.

5. Stream analytics işi sayfasına geri gidin ve **çıkışları**.  

6. **+ Ekle** öğesini seçin. Ardından **Power BI** aşağı açılan menüden.  

7. Ortalama fiyat görselleştirmek için yeni bir Power BI çıktı oluşturmak için aşağıdaki eylemleri gerçekleştirin:

   * İçinde **çıkış diğer adı** alanına **averagePriceOutput**.  
   * Bırakın **Grup çalışma** alan kümesine **çalışma alanları yüklemek için bağlantıyı yetkilendirin**.  
   * İçinde **veri kümesi adı** alanına **averagePrice**.  
   * İçinde **tablo adı** alanına **averagePrice**.  
   * Seçin **Authorize** düğmesine ve ardından Power bı'a bağlantıyı yetkilendirmek için yönergeleri izleyin.  
   * **Kaydet** düğmesini seçin.  

8. Ardından dönün **streamjob1** seçip **sorguyu Düzenle**.

   ![Sorguyu düzenle](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. Sorgu penceresine aşağıdaki sorguyu yapıştırın. **Ortalama fiyat** sorgu hesaplar kullanıcılar, kullanıcıların sepetleri için eklenen tüm öğelerin ortalama fiyat ve kullanıcılar tarafından satın alınan tüm öğelerin ortalama fiyat tarafından görüntülenen tüm öğelerin ortalama fiyat. Bu ölçüm, e-ticaret şirketlerin hangi fiyatları öğe ve hangi yatırım yapmaya stok satmak için karar yardımcı olabilir. Görüntülenen öğelerin ortalama fiyat satın alınan öğelerin ortalama fiyattan daha yüksek ise, örneğin, sonra şirket, envantere ucuz öğeleri eklemek seçebilirsiniz.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Ardından **Kaydet** sol üst köşesinde.  

11. Şimdi geri dönüp **streamjob1** seçip **Başlat** sayfanın üstünde düğme. Azure Stream Analytics başlatılması birkaç dakika sürebilir, ancak bunu "Başlangıç" öğesinden "Çalışıyor" değiştirmek sonunda görürsünüz.

## <a name="connect-to-power-bi"></a>Power BI'a Bağlan

Power BI, verileri analiz edip öngörü paylaşmaya yönelik İş analizi araçları paketidir. Analiz edilen verileri ne stratejik görselleştirebilirsiniz, harika bir örnektir.

1. Power BI'da oturum açın ve gidin **çalışma Alanım** sayfanın sol tarafındaki menüyü açarak.  

2. Seçin **+ Oluştur** seçin ve sağ üst köşedeki **Pano** Pano oluşturma.  

3. Seçin **+ kutucuk Ekle** sağ üst köşedeki.  

4. Seçin **özel akış verileri**, ardından **sonraki** düğmesi.  
 
5. Seçin **averagePrice** gelen **bilgisayarınızı veri KÜMELERİ**, ardından **sonraki**.  

6. İçinde **görselleştirme türünü** alan öğesini **kümelenmiş çubuk grafik** aşağı açılan menüden. Altında **eksen**, eylem ekleme. Skip **gösterge** eklemeden herhangi bir şey. Ardından, sonraki bölümde hükme adlı **değer**, ekleme **ortalama**. Seçin **sonraki**, grafik başlık ve seçin **Uygula**. Panonuza yeni bir grafik görmeniz gerekir!  

7. Daha fazla ölçümlerini görselleştirmek isterseniz şimdi geri gidebilirsiniz **streamjob1** ve üç daha fazla çıkış şu alanlar oluşturun.

   a. **Çıkış diğer adı:** incomingRevenueOutput, veri kümesi adı: incomingRevenue, tablo adı: incomingRevenue  
   b. **Çıkış diğer adı:** top5Output, veri kümesi adı: top5, tablo adı: top5  
   c. **Çıkış diğer adı:** uniqueVisitorCountOutput, veri kümesi adı: uniqueVisitorCount, tablo adı: uniqueVisitorCount

   Ardından **sorguyu Düzenle** ve aşağıdaki sorguları yapıştırın **yukarıda** bir zaten yazdığınız.

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   İLK 5 sorgu satın aldığınız kez sayısına göre sıralanmış ilk 5 öğeleri hesaplar. Bu ölçüm, hangi öğelerin en popüler ve şirketin reklam, fiyatlandırmayı etkiler ve kararları stok değerlendirmek e-ticaret şirketler yardımcı olabilir.

   GELİR sorgu gelir her dakika satın alınan tüm öğeleri fiyatlarını toplayarak hesaplar. Bu ölçüm, finansal performansını değerlendirmek ve ayrıca hangi durum için çoğu gelir gün saatler katkıda bulunduğunu anlamak e-ticaret şirketlerin yardımcı olabilir. Bu, özellikle pazarlama genel şirket stratejisi etkileyebilir.

   Kaç tane benzersiz ziyaretçi sitede olduğu benzersiz ZİYARETÇİ sorgu hesaplar 5 saniyede benzersiz olan algılama sepet olarak kimliğin Bu ölçüm, kendi site etkinliği değerlendirmek ve daha fazla müşteri edinme konusunda danışmanlık e-ticaret şirketlerin yardımcı olabilir.

8. Şimdi de bu veri kümeleri için kutucuklar da ekleyebilirsiniz.

   * İlk 5 için bir kümelenmiş sütun grafik eksen olarak öğeleriyle ve sayı değeri olarak yapmak için anlamlı olacaktır.  
   * Gelir, bir çizgi grafiğin zaman ekseni ve fiyatlar toplamını değeri olarak yapmak için anlamlı olmaz. Görüntülenecek zaman penceresi, teslim etmek mümkün olduğunca fazla bilgi için olası en büyük olmalıdır.  
   * Benzersiz ziyaretçi için bir kart görselleştirmesi benzersiz ziyaretçi sayısı ile değeri olarak yapmak için anlamlı olacaktır.

   Örnek pano ile bu grafikleri nasıl göründüğünü budur:

   ![görselleştirmeler](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>İsteğe bağlı: Bir E-ticaret sitesiyle görselleştirin

Artık, bir gerçek e-ticaret sitesi ile bağlanmak için yeni veri analizi aracı nasıl kullanabileceğinizi gözlemleyeceksiniz. E-ticaret sitesini derlemek için bir Azure Cosmos veritabanını kullanarak ürün kategorilerinin listesini (kadınlar, Erkek, Unisex), ürün kataloğunu ve en popüler öğelerin bir listesini depolayın.

1. Geri gidin [Azure portalı](https://portal.azure.com/), ardından, **Cosmos DB hesabı**, ardından **Veri Gezgini**.  

   Altında iki koleksiyon Ekle **changefeedlabdatabase** - **ürünleri** ve **kategorileri** sahip sabit depolama kapasitesi.

   Başka bir koleksiyon Ekle **changefeedlabdatabase** adlı **topItems** ve **/Item** bölüm anahtarı olarak.

2. Seçin **topItems** koleksiyonu ve altında **ölçek ve ayarlar** ayarlamak **yaşam süresi** olmasını **30 saniye** bu topItems güncelleştirmeleri için Her 30 saniyede.

   ![Yaşam süresi](./media/changefeed-ecommerce-solution/time-to-live.png)

3. Doldurmak için **topItems** koleksiyonuyla en sık öğeleri, satın alınan geri gidin **streamjob1** ve yeni bir **çıkış**. Seçin **Cosmos DB**.

4. Aşağıdaki resimdeki gibi gerekli alanları doldurun.

   ![Cosmos çıkış](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. İsteğe bağlı ilk 5 sorgu laboratuvarı önceki bölümünde eklediyseniz, bölümü 5a için devam edin. Aksi durumda, bölümü 5b için devam edin.

   5a. İçinde **streamjob1**seçin **sorguyu Düzenle** ve ilk 5 sorgu aşağıda ancak rest sorgu üzerinde Azure Stream Analytics sorgu Düzenleyicisi'nde aşağıdaki sorguyu yapıştırın.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. İçinde **streamjob1**seçin **sorguyu Düzenle** ve aşağıdaki sorguyu Azure Stream Analytics sorgu Düzenleyicisi'nde tüm diğer sorgular yapıştırın.

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. Açık **EcommerceWebApp.sln** gidin **Web.config** dosyası **Çözüm Gezgini**.  

7. İçinde `<appSettings>` engelleme, ekleme **URI** ve **birincil anahtar** daha önce kaydettiğiniz yeri belirten **URI'niz burada** ve **birincil anahtarınızı Buraya**. Eklemek, **veritabanı adı** ve **koleksiyon adı** belirtti. (Bu adlar olmalıdır **changefeedlabdatabase** ve **changefeedlabcollection** sizinki farklı ad seçmediyseniz.)

   Doldurun, **ürünleri koleksiyon adı**, **kategorileri koleksiyon adı**, ve **üst öğe koleksiyonu adı** belirtti. (Bu adlar olmalıdır **ürünleri, kategoriler ve topItems** sizinki farklı ad seçmediyseniz.)  

8. Gidin ve açmak **kullanıma alma klasörü** içinde **EcommerceWebApp.sln.** Açılacağını **Web.config** bu klasördeki dosyaya.  

9. İçinde `<appSettings>` engelleme, ekleme **URI** ve **birincil anahtar** daha önce belirtilen yerlerde kaydedildi. Eklemek, **veritabanı adı** ve **koleksiyon adı** belirtti. (Bu adlar olmalıdır **changefeedlabdatabase** ve **changefeedlabcollection** sizinki farklı ad seçmediyseniz.)  

10. Tuşuna **Başlat** programı çalıştırmak için sayfanın üst kısmındaki.  

11. Artık, e-ticaret sitesinde oynayabilirsiniz. Bu olaylar, bir öğeyi görüntülemek, öğeyi sepetinize ekleyin, sepetinize içinde bir öğe miktarını değiştirebilir veya bir öğe satın, Cosmos DB ile geçirilir değişiklik olay hub'ı, ASA ve ardından Power BI akışı. Önemli web trafiği verilerini oluşturmak ve "Sık erişimli ürünleri" gerçekçi bir dizi sağlamak için DataGenerator çalışmaya devam eder e-ticaret sitesinde öneririz.

## <a name="delete-the-resources"></a>Kaynakları Sil

Bu Laboratuvar sırasında oluşturduğunuz kaynakları silmek için kaynak grubuna gidin [Azure portalı](https://portal.azure.com/), ardından **kaynak grubunu Sil** sayfanın üst kısmındaki menüden ve yönergeleri izleyin sağlanan.

## <a name="next-steps"></a>Sonraki adımlar 
  
* Değişiklik akışı hakkında daha fazla bilgi için bkz: [Azure Cosmos DB'de çalışma değişiklik akışı desteği](change-feed.md) 
* [Değişiklik akışı Bildirimi çözümü](change-feed-hl7-fhir-logic-apps.md) Azure Cosmos DB kullanarak sağlık hizmeti kuruluşunda için.
