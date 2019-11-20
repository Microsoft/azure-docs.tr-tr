---
title: Azure Stream Analytics kullanarak gerçek zamanlı sahtekarlık algılama
description: Stream Analytics ile gerçek zamanlı bir sahtekarlık algılama çözümü oluşturmayı öğrenin. Gerçek zamanlı olay işleme için bir olay hub 'ı kullanın.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 19c9448b6a743302eb81bb208444336d6435f114
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947054"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Azure Stream Analytics kullanmaya başlayın: Gerçek zamanlı sahtekarlık algılama

Bu öğreticide Azure Stream Analytics kullanımı için uçtan uca bir çizim sunulmaktadır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz: 

* Akış olaylarını bir Azure Event Hubs örneğine taşıyın. Bu öğreticide, mobil telefon meta verileri kayıtlarının akışını taklit eden bir uygulama kullanacaksınız.

* Verileri dönüştürmek, bilgi toplamak veya desenler aramak için SQL benzeri Stream Analytics sorguları yazın. Gelen akışı incelemek ve sahte olabilecek çağrıları aramak için bir sorgu kullanmayı öğreneceksiniz.

* Sonuçları, ek Öngörüler için çözümleyebilmeniz için bir çıkış havuzuna (depolama) gönderin. Bu durumda, şüpheli çağrı verilerini Azure Blob depolama alanına gönderirsiniz.

Bu öğretici, telefon araması verilerine göre gerçek zamanlı sahtekarlık algılama örneğini kullanır. Gösterilen teknik, kredi kartı sahtekarlığı veya kimlik hırsızlığı gibi diğer sahtekarlık algılama türleri için de uygundur. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Senaryo: Gerçek zamanlı telekomünikasyon ve SIM sahtekarlık algılaması

Bir telekomünikasyon şirketi gelen çağrılar için büyük hacimde veri içerir. Şirket, müşterileri bilgilendirmek veya belirli bir numara için hizmeti kapatmak üzere sahte aramaları gerçek zamanlı olarak algılamak istiyor. Bir tür SIM dolandırıcılığı aynı kimliğin çevresindeki aynı kimliğe sahip birden fazla çağrı içerir, ancak coğrafi olarak farklı konumlarda. Bu tür dolandırıcılığın algılanması için şirketin gelen telefon kayıtlarını incelemesi ve belirli desenleri (Bu durumda, farklı ülkelerde/bölgelerde aynı zamanda yapılan çağrılar için) araması gerekir. Bu kategoriye giren tüm telefon kayıtları, sonraki analize yönelik depolama alanına yazılır.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide, örnek telefon araması meta verileri üreten bir istemci uygulaması kullanarak telefon araması verilerinin benzetimini yapacaksınız. Uygulamanın ürettiği bazı kayıtlar, sahte çağrılar gibi görünüyor. 

Başlamadan önce şunlara sahip olduğunuzdan emin olun:

* Bir Azure hesabı.
* Microsoft Indirme merkezi 'nden indirilebilen çağrı olayı Oluşturucu uygulaması, [Telcogenerator. zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip). Bu paketi bilgisayarınızdaki bir klasöre ayıklayın. Kaynak kodunu görmek ve uygulamayı bir hata ayıklayıcıda çalıştırmak istiyorsanız, uygulama kaynak kodunu [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)' dan edinebilirsiniz. 

    >[!NOTE]
    >Windows, indirilen. zip dosyasını engelleyebilirler. Bu dosyayı açmak için dosyaya sağ tıklayın ve **Özellikler**' i seçin. "Bu dosya başka bir bilgisayardan geldi ve bu bilgisayarın korunmasına yardımcı olmak için engelleniyor olabilir" iletisi görürseniz, **Engellemeyi kaldır** seçeneğini belirleyip **Uygula**' ya tıklayın.

Akış Analizi işinin sonuçlarını incelemek istiyorsanız, bir Azure Blob depolama kapsayıcısının içeriğini görüntülemek için de bir araç gerekir. Visual Studio kullanıyorsanız, Visual Studio veya [Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) [için Azure Araçları](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) 'nı kullanabilirsiniz. Alternatif olarak, [Azure Depolama Gezgini](https://storageexplorer.com/) veya [ceruyalın](https://www.cerebrata.com/products/cerulean/features/azure-storage)gibi tek başına araçları da yükleyebilirsiniz. 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Olayları almak için Azure Event Hubs oluşturma

Bir veri akışını analiz etmek için bunu Azure 'a alırsınız. Verileri almanın tipik bir yolu [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)kullanmaktır, bu da saniyede milyonlarca olayı almanızı ve olay bilgilerini işlemenize ve depolamanıza olanak tanır. Bu öğreticide, bir olay hub 'ı oluşturacak ve ardından çağrı olayı Oluşturucu uygulamasının bu olay hub 'ına çağrı verileri göndermesini sağlayabilirsiniz. Olay Hub 'ları hakkında daha fazla bilgi için [Azure Service Bus belgelerine](https://docs.microsoft.com/azure/service-bus/)bakın.

>[!NOTE]
>Bu yordamın daha ayrıntılı bir sürümü için, [Azure Portal kullanarak Event Hubs ad alanı ve Olay Hub 'ı oluşturma](../event-hubs/event-hubs-create.md)konusuna bakın. 

### <a name="create-a-namespace-and-event-hub"></a>Ad alanı ve olay hub'ı oluşturma
Bu yordamda, önce bir olay hub 'ı ad alanı oluşturun ve ardından bu ad alanına bir olay hub 'ı eklersiniz. Olay Hub 'ı ad alanları, ilgili olay veri yolu örneklerini mantıksal olarak gruplamak için kullanılır. 

1. Azure Portal oturum açın ve**Olay Hub** **nesnelerin interneti** >  **kaynak** > oluştur ' a tıklayın. 

2. **Ad alanı oluştur** bölmesinde, gibi bir ad alanı adı `<yourname>-eh-ns-demo`girin. Ad alanı için herhangi bir ad kullanabilirsiniz, ancak ad bir URL için geçerli olmalıdır ve Azure genelinde benzersiz olmalıdır. 
    
3. Bir abonelik seçin ve bir kaynak grubu oluşturun veya seçin, ardından **Oluştur**' a tıklayın.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

4. Ad alanı dağıtımı bitirdiğinde, Azure kaynakları listenizde Olay Hub 'ı ad alanını bulun. 

5. Yeni ad alanı ' na tıklayın ve ad alanı bölmesinde **Olay Hub**' ı tıklatın.

   ![Yeni bir olay hub 'ı oluşturmak için Olay Hub 'ı Ekle düğmesi](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Yeni Olay Hub 'ını `asa-eh-frauddetection-demo`adlandırın. Farklı bir ad kullanabilirsiniz. Bunu yaparsanız, bu adı daha sonra gerekli olduğunuzdan emin olun. Şu anda Olay Hub 'ı için başka herhangi bir seçenek ayarlamanıza gerek yoktur.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
 
7. **Oluştur**'a tıklayın.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Olay hub’ına erişim verme ve bir bağlantı dizesi alma

Bir işlemin bir olay hub 'ına veri gönderebilmesi için, Olay Hub 'ının uygun erişime izin veren bir ilkesi olması gerekir. Erişim ilkesi, yetkilendirme bilgilerini içeren bir bağlantı dizesi oluşturur.

1.  Olay ad alanı bölmesinde **Event Hubs** ' a ve ardından yeni Olay Hub 'ınızın adına tıklayın.

2.  Olay Hub 'ı bölmesinde, **paylaşılan erişim ilkeleri** ' ne ve ardından **+&nbsp;Ekle**' ye tıklayın.

    >[!NOTE]
    >Olay Hub 'ı ad alanı değil, Olay Hub 'ı ile çalıştığınızdan emin olun.

3.  Ve talep için adlı `sa-policy-manage-demo` bir ilkeekleyin, **Yönet**' i seçin.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4.  **Oluştur**'a tıklayın.

5.  İlke dağıtıldıktan sonra, paylaşılan erişim ilkeleri listesinde buna tıklayın.

6.  **Bağlantı dizesi-BIRINCIL anahtar** etiketli kutuyu bulun ve bağlantı dizesinin yanındaki Kopyala düğmesine tıklayın. 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7.  Bağlantı dizesini bir metin düzenleyicisine yapıştırın. Bu bağlantı dizesine, bazı küçük düzenlemeler yaptıktan sonra bir sonraki bölüm için ihtiyacınız vardır.

    Bağlantı dizesi şuna benzer:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    Bağlantı dizesinin, noktalı virgülle ayrılmış birden çok anahtar-değer çifti içerdiğini `Endpoint`unutmayın: `SharedAccessKey`, `SharedAccessKeyName`, ve `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Olay Oluşturucu uygulamasını yapılandırma ve başlatma

TelcoGenerator uygulamasını başlamadan önce, oluşturduğunuz Olay Hub 'ına çağrı kayıtları gönderecek şekilde yapılandırmanız gerekir.

### <a name="configure-the-telcogenerator-app"></a>TelcoGenerator uygulamasını yapılandırma

1. Bağlantı dizesini kopyaladığınız düzenleyicide, `EntityPath` değeri bir yere göz önüne alın ve sonra `EntityPath` çifti kaldırın (bundan sonra gelen noktalı virgülü kaldırmayı unutmayın). 

2. TelcoGenerator. zip dosyasını sıkıştırmadan indirdiğiniz klasörde, bir düzenleyicide telcodatagen. exe. config dosyasını açın. (Birden fazla. config dosyası var, bu nedenle, doğru olanı seçtiğinizden emin olun.)

3. `<appSettings>` Öğesinde:

   * `EventHubName` Anahtar değerini Olay Hub 'ı adı (yani, varlık yolunun değerine) olarak ayarlayın.
   * `Microsoft.ServiceBus.ConnectionString` Anahtarın değerini bağlantı dizesine ayarlayın. 

   `<appSettings>` Bölüm aşağıdaki örneğe benzer şekilde görünür. (Açıklık açısından, satırlar sarmalanır ve bazı karakterler yetkilendirme belirtecinden kaldırılmıştır.)

   ![TelcoGenerator yapılandırma dosyası, Olay Hub 'ı adını ve bağlantı dizesini gösterir](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4. Dosyayı kaydedin. 

### <a name="start-the-app"></a>Uygulamayı başlatma
1.  Bir komut penceresi açın ve TelcoGenerator uygulamasının sıkıştırunı olan klasöre geçin.

2.  Aşağıdaki komutu girin:

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   Parametreler şunlardır: 

   * Saat başına CDRs sayısı. 
   * SIM kart sahtekarlık olasılığı: Her ne kadar sıklıkla, tüm çağrıların yüzdesi olarak uygulamanın sahte bir çağrının benzetimini yapmanız gerekir. 0\.2 değeri arama kayıtlarının %20'sinin sahte görüneceğini anlamına gelir.
   * Saat cinsinden süre. Uygulamanın çalışması gereken saat sayısı. Komut satırında CTRL + C tuşlarına basarak da uygulamayı dilediğiniz zaman durdurabilirsiniz.

   Birkaç saniye sonra uygulama, telefon araması kayıtlarını olay hub'ına gönderirken ekranda bu kayıtları görüntülemeye başlar.

Bu gerçek zamanlı sahtekarlık algılama uygulamasında kullanacağınız bazı anahtar alanları şunlardır:

|**Kayıt**|**Tanım**|
|----------|--------------|
|`CallrecTime`|Arama başlangıç zamanı için zaman damgası. |
|`SwitchNum`|Aramayı bağlamak için kullanılan telefon anahtarı. Bu örnekte, anahtarlar menşei ülke/bölge (ABD, Çin, UK, Almanya veya Avustralya) temsil eden dizelerdir. |
|`CallingNum`|Arayanın telefon numarası. |
|`CallingIMSI`|Uluslararası Mobil Abone Kimliği (IMSI). Bu, çağıranın benzersiz tanımlayıcısıdır. |
|`CalledNum`|Arama alıcısının telefon numarası. |
|`CalledIMSI`|Uluslararası Mobil Abone Kimliği (IMSI). Bu, çağrı alıcısının benzersiz tanımlayıcısıdır. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Akış verilerini yönetmek için Stream Analytics işi oluşturma

Artık çağrı olaylarınızın bulunduğu bir akışa sahip olduğunuza göre Stream Analytics bir iş ayarlayabilirsiniz. İş, ayarladığınız olay hub 'ından verileri okur. 

### <a name="create-the-job"></a>İşi oluşturma 

1. Azure Portal,**Stream Analytics iş** **nesnelerin interneti** >  **kaynak** > oluştur ' a tıklayın.

2. İşi `asa_frauddetection_job_demo`adlandırın, bir abonelik, kaynak grubu ve konum belirtin.

    En iyi performans için işi ve Olay Hub 'ını aynı bölgeye yerleştirmeniz ve bu sayede verileri bölgeler arasında aktarmaya ödeme yapmanız iyi bir fikirdir.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. **Oluştur**'a tıklayın.

    İş oluşturulur ve Portal iş ayrıntılarını görüntüler. Henüz hiçbir şey çalışmıyor; ancak başlamadan önce işi yapılandırmanız gerekir.

### <a name="configure-job-input"></a>İş girişi yapılandırma

1. Panoda veya **tüm kaynaklar** bölmesinde `asa_frauddetection_job_demo` Stream Analytics işini bulun ve seçin. 
2. Stream Analytics işi bölmesinin **genel bakış** bölümünde **giriş** kutusuna tıklayın.

   ![Akış Analizi işi bölmesindeki topoloji altında giriş kutusu](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. **Akış girişi Ekle** ' ye tıklayın ve **Olay Hub 'ını**seçin. Ardından yeni giriş sayfasını aşağıdaki bilgilerle doldurabilirsiniz:

   |**Ayar**  |**Önerilen değer**  |**Açıklama**  |
   |---------|---------|---------|
   |Girdi diğer adı  |  CallStream   |  İşin girdisini tanımlamak için bir ad girin.   |
   |Subscription   |  \<Aboneliğiniz\> |  Oluşturduğunuz Olay Hub 'ına sahip Azure aboneliğini seçin.   |
   |Olay hub'ı ad alanı  |  asa-Eh-NS-demo |  Olay Hub 'ı ad alanının adını girin.   |
   |Olay Hub'ı adı  | asa-Eh-frauddetection-demo | Olay Hub 'ınızın adını seçin.   |
   |Olay Hub'ı ilke adı  | asa-ilke-Yönet-demo | Daha önce oluşturduğunuz erişim ilkesini seçin.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. **Oluştur**'a tıklayın.

## <a name="create-queries-to-transform-real-time-data"></a>Gerçek zamanlı verileri dönüştürmek için sorgular oluşturma

Bu noktada, gelen veri akışını okumak üzere ayarlanmış bir Stream Analytics işi vardır. Sonraki adım, verileri gerçek zamanlı olarak analiz eden bir sorgu oluşturmaktır. Stream Analytics, gerçek zamanlı işleme dönüştürmelerini açıklayan basit, bildirime dayalı bir sorgu modeli destekler. Sorgular, Stream Analytics özgü bazı uzantılara sahip olan SQL benzeri bir dil kullanır. 

Basit bir sorgu yalnızca tüm gelen verileri okuyabilir. Ancak, genellikle belirli verileri veya verilerdeki ilişkileri bulmak için sorgular oluşturursunuz. Öğreticinin bu bölümünde, bir giriş akışını Analize dönüştürebilmeniz için birkaç yol öğrenmek üzere birkaç sorgu oluşturup test edersiniz. 

Burada oluşturduğunuz sorgular ekranda yalnızca dönüştürülen verileri görüntüler. Daha sonraki bir bölümde, bir çıkış havuzu ve bu havuza dönüştürülen verileri yazan bir sorgu yapılandırırsınız.

Dil hakkında daha fazla bilgi edinmek için bkz. [Azure Stream Analytics sorgu dili başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="get-sample-data-for-testing-queries"></a>Sorgu testi için örnek verileri al

TelcoGenerator uygulaması, Olay Hub 'ına çağrı kayıtları gönderiyor ve Stream Analytics işiniz Olay Hub 'ından okumak üzere yapılandırılmış. İşi test etmek için bir sorgu kullanarak doğru okunduğunuzdan emin olun. Azure konsolunda bir sorguyu test etmek için örnek verilere ihtiyacınız vardır. Bu izlenecek yol için, Olay Hub 'ına gelen akıştan örnek verileri ayıklayacaksınız.

1. TelcoGenerator uygulamasının çalıştığından ve çağrı kayıtları üretgeldiğinden emin olun.
2. Portalda Akış Analizi işi bölmesine dönün. (Bölmeyi kapattıysanız `asa_frauddetection_job_demo` **tüm kaynaklar** bölmesinde arama yapın.)
3. **Sorgu** kutusuna tıklayın. Azure, iş için yapılandırılmış girişleri ve çıkışları listeler ve giriş akışını çıkışa gönderilirken dönüştürmenizi sağlayan bir sorgu oluşturmanızı sağlar.
4. **Sorgu** bölmesinde, `CallStream` girişin yanındaki noktalara tıklayın ve sonra **girişten örnek veriler**' i seçin.

   ![Akış Analizi işi girişi için örnek verileri kullanmak üzere menü seçenekleri "girişten gelen örnek veriler" seçilir](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. **Dakikaları** 3 olarak ayarlayın ve ardından **Tamam**' a tıklayın. 
    
   ![3 dakikalık seçili giriş akışını örnekleme seçenekleri](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure, giriş akışından 3 dakikalık bir değer verir ve örnek veriler ne zaman hazırlandığını size bildirir. (Bu kısa bir süre sürer.) 

Örnek veriler geçici olarak depolanır ve sorgu penceresi açıkken kullanılabilir. Sorgu penceresini kapatırsanız örnek veriler atılır ve yeni bir örnek veri kümesi oluşturmanız gerekir. 

Alternatif olarak, [GitHub 'dan](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)örnek veriler içeren bir. JSON dosyası alabilir ve ardından bu. json dosyasını `CallStream` giriş için örnek veri olarak kullanmak üzere karşıya yükleyebilirsiniz. 

### <a name="test-using-a-pass-through-query"></a>Doğrudan sorgu kullanarak test etme

Her olayı arşivlemek istiyorsanız, olay yükünde tüm alanları okumak için bir geçişli sorgu kullanabilirsiniz.

1. Sorgu penceresinde şu sorguyu girin:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >SQL 'de olduğu gibi, anahtar sözcükler büyük/küçük harfe duyarlı değildir ve boşluklar önemli değildir.

    Bu sorguda, `CallStream` girişi oluşturduğunuzda belirttiğiniz diğer addır. Farklı bir diğer ad kullandıysanız bunun yerine bu adı kullanın.

2. **Test**' e tıklayın.

    Stream Analytics işi sorguyu örnek verilere karşı çalıştırır ve çıktıyı pencerenin alt kısmında görüntüler. Sonuçlar, Olay Hub 'ının ve akış analizi işinin doğru şekilde yapılandırıldığını gösterir. (Belirtildiği gibi, daha sonra sorgunun veri yazabilmesi için bir çıkış havuzu oluşturacaksınız.)

   ![Stream Analytics iş çıkışı, üretilen 73 kaydı gösteriliyor](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Gördüğünüz kayıtların tam sayısı, 3 dakikalık örnekte kaç kaydın yakalandığına bağlıdır.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Sütun projeksiyonu kullanarak alan sayısını azaltma

Çoğu durumda, analizinizden giriş akışındaki tüm sütunlara gerek yoktur. Daha küçük bir döndürülen alan kümesini doğrudan geçiş sorgusundan proje yapmak için bir sorgu kullanabilirsiniz.

1. Kod düzenleyicisinde sorguyu aşağıdaki gibi değiştirin:

   ```SQL
   SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
   FROM 
       CallStream
   ```

2. **Test** ' e tıklayın. 

   ![Yansıtma için Stream Analytics iş çıktısı 25 kayıt gösterir](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Bölgeye göre gelen çağrıları say: Toplama ile atlayan pencere

Bölge başına gelen çağrı sayısını saymak istediğinizi varsayalım. Akış verileri ' nde, sayma gibi toplam işlevleri gerçekleştirmek istediğinizde, akışı zamana bağlı birimlere (veri akışının kendisi etkin bir şekilde sonsuz olduğundan) segmentetmeniz gerekir. Bunu bir akış analizi [pencere işlevi](stream-analytics-window-functions.md)kullanarak yapabilirsiniz. Daha sonra bu pencere içindeki verilerle birim olarak çalışabilirsiniz.

Bu dönüşüm için, her bir pencere, gruplandırmanız ve toplayabilmeniz gereken ayrı bir veri kümesine sahip olmak için, çakışmayacak bir dizi zamana bağlı pencere istiyorsunuz. Bu pencere türü, atlayan *pencere*olarak adlandırılır. Atlayan penceresinde, aramanın kaynaklandığı ülkeyi/bölgeyi temsil eden, tarafından `SwitchNum`gruplanmış gelen çağrıların sayısını alabilirsiniz. 

1. Kod düzenleyicisinde sorguyu aşağıdaki gibi değiştirin:

        ```SQL
        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
        ```

    Bu sorgu, iç `Timestamp By` içe geçmiş pencereyi `FROM` tanımlamak için kullanılacak giriş akışındaki zaman damgası alanını belirtmek için yan tümcesindeki anahtar sözcüğünü kullanır. Bu durumda pencere, verileri her kayıttaki `CallRecTime` alana göre segmentlere ayırır. (Alan belirtilmemişse, Pencereleme işlemi her bir olayın olay hub 'ına ulaştığı saati kullanır. [Stream Analytics sorgu dili başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)' nda "varış saati vs uygulama süresi" başlığına bakın. 

    Projeksiyon eklemeleri `System.Timestamp`, her pencerenin sonuna yönelik bir zaman damgası döndürür. 

    Atlayan bir pencere kullanmak istediğinizi belirtmek için, `GROUP BY` yan tümcesindeki [tumblingwindow](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) işlevini kullanın. İşlevinde, bir zaman birimi (bir mikro saniyenin bir güne kadar bir süre) ve pencere boyutu (kaç birim) belirtirsiniz. Bu örnekte, atlayan pencere 5 saniyelik aralıklardan oluşur; bu nedenle, her 5 saniyelik çağrı için ülkeye/bölgeye göre bir sayı alacaksınız.

2. **Test** ' e tıklayın. Sonuçlarda, **Windowend** altındaki zaman damgalarının 5 saniyelik artışlarla olduğunu unutmayın.

   ![13 kayıt gösteren toplama için iş çıktısını Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Kendi kendine birleşim kullanarak SIM sahteciliği Algıla

Bu örnekte, aynı kullanıcıdan kaynaklanan, ancak bir diğeri 5 saniyelik farklı konumlarda olan çağrılar için sahte kullanım yapmayı düşünün. Örneğin, bir kullanıcı mantıksal olarak aynı anda hem ABD’den hem de Avustralya’dan arama yapamaz. 

Bu durumları denetlemek için akış verilerinin kendine katılımını, `CallRecTime` değere göre akışa eklemek için kullanabilirsiniz. Daha sonra `CallingIMSI` değerin (kaynak numarası) aynı olduğu, `SwitchNum` ancak değer (ülke/kaynak bölge) aynı olmadığı durumlarda çağrı kayıtları arayabilirsiniz.

Akış verileriyle birleştirme kullandığınızda, birleştirme, eşleşen satırların zaman içinde ne kadar ayrılabileceği hakkında bazı sınırlar sağlamalıdır. (Daha önce belirtildiği gibi, akış verileri etkili bir şekilde sonsuz olur.) İlişki için zaman sınırları, `ON` `DATEDIFF` işlevi kullanılarak birleştirmenin yan tümcesi içinde belirtilir. Bu durumda, birleşimi 5 saniyelik çağrı verileri aralığını temel alır.

1. Kod düzenleyicisinde sorguyu aşağıdaki gibi değiştirin: 

        ```SQL
        SELECT  System.Timestamp as Time, 
            CS1.CallingIMSI, 
            CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, 
            CS1.SwitchNum as Switch1, 
            CS2.SwitchNum as Switch2 
        FROM CallStream CS1 TIMESTAMP BY CallRecTime 
            JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
            ON CS1.CallingIMSI = CS2.CallingIMSI 
            AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
        WHERE CS1.SwitchNum != CS2.SwitchNum
        ```

    Bu sorgu, birleşimdeki `DATEDIFF` işlev haricinde herhangi bir SQL JOIN gibi olur. Bu sürümü `DATEDIFF` , Stream Analytics 'e özeldir ve `ON...BETWEEN` yan tümcesinde görünmelidir. Parametreler bir zaman birimidir (Bu örnekteki saniyeler) ve JOIN için iki kaynağın diğer adları. Bu, standart SQL `DATEDIFF` işlevinden farklıdır.

    `WHERE` Yan tümcesi, sahte çağrıyı işaret eden koşulu içerir: kaynak Anahtarlar aynı değildir. 

2. **Test** ' e tıklayın. 

   ![Kendini birleştirmek için Stream Analytics iş çıkışı, oluşturulan 6 kayıt gösteriliyor](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Kendi kendine birleştirme sorgusunu Akış Analizi işinin bir parçası olarak kaydetmek için **Kaydet** ' e tıklayın. (Örnek verileri kaydetmez.)

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Dönüştürülmüş verileri depolamak için çıkış havuzu oluşturma

Bir olay akışı, olayları almak için bir olay hub 'ı girişi ve akış üzerinde bir dönüştürme gerçekleştirmek için bir sorgu tanımladınız. Son adım iş için bir çıkış havuzu tanımlamaktır — Yani, dönüştürülmüş akışın yazılacağı bir yer. 

Bir SQL Server veritabanı, tablo depolama, Data Lake depolama, Power BI ve hatta başka bir olay hub 'ı olmak üzere çok sayıda kaynağı çıkış havuzları olarak kullanabilirsiniz. Bu öğreticide, yapılandırılmamış verileri kullandığından daha sonra analize yönelik olay bilgilerinin toplanması için tipik bir seçenek olan Azure Blob depolama alanına Stream yazacaksınız.

Mevcut bir BLOB depolama hesabınız varsa bunu kullanabilirsiniz. Bu öğreticide, yeni bir depolama hesabı oluşturmayı öğreneceksiniz.

### <a name="create-an-azure-blob-storage-account"></a>Azure Blob depolama hesabı oluşturma

1. Azure portalının sol üst köşesinden **Kaynak oluştur** > **Depolama** > **Depolama hesabı**’nı seçin. **Ad** "asaehstorage" olarak ayarlanmış olan depolama hesabı işi sayfasını doldurun, **konum** "Doğu ABD" olarak ayarlanır, **kaynak grubu** "asa-Eh-NS-RG" olarak ayarlanır (daha yüksek performans için depolama hesabını akış işiyle aynı kaynak grubunda barındırın) . Diğer ayarlar varsayılan değerlerinde bırakılabilir.  

   ![Azure portal depolama hesabı oluşturma](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. Azure portal, Akış Analizi işi bölmesine dönün. (Bölmeyi kapattıysanız `asa_frauddetection_job_demo` **tüm kaynaklar** bölmesinde arama yapın.)

3. **Iş topolojisi** bölümünde **Çıkış** kutusuna tıklayın.

4. **Çıktılar** bölmesinde, **Ekle** ' ye tıklayın ve **BLOB depolama**' yı seçin. Ardından, yeni çıkış sayfasını aşağıdaki bilgilerle doldurun:

   |**Ayar**  |**Önerilen değer**  |**Açıklama**  |
   |---------|---------|---------|
   |Çıktı diğer adı  |  CallStream-FraudulentCalls   |  İşin çıktısını tanımlamak için bir ad girin.   |
   |Subscription   |  \<Aboneliğiniz\> |  Oluşturduğunuz depolama hesabını içeren Azure aboneliğini seçin. Depolama hesabı, aynı veya farklı bir abonelikte olabilir. Bu örnekte, aynı abonelikte depolama hesabı oluşturduğunuz varsayılır. |
   |Depolama hesabı  |  aşama ehstorage |  Oluşturduğunuz depolama hesabının adını girin. |
   |Kapsayıcı  | asa-fraudulentcalls-demo | Yeni Oluştur ' a tıklayın ve bir kapsayıcı adı girin. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. **Kaydet**’e tıklayın. 


## <a name="start-the-streaming-analytics-job"></a>Akış Analizi işini başlatma

İş artık yapılandırıldı. Bir giriş (Olay Hub 'ı), bir dönüşüm (sahte çağrılar için aranacak sorgu) ve bir çıkış (BLOB depolama) belirttiniz. Şimdi işi başlatabilirsiniz. 

1. TelcoGenerator uygulamasının çalıştığından emin olun.

2. İş bölmesinde **Başlat**' a tıklayın. **Işi Başlat** bölmesinde iş çıkışı başlangıç zamanı için **Şimdi**' yi seçin. 

   ![Stream Analytics işini Başlat](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Dönüştürülen verileri inceleyin

Artık tamamlanmış bir akış analizi işimize sahipsiniz. İş, telefon araması meta verileri akışını inceliyor, gerçek zamanlı sahte telefon çağrılarını arıyor ve depolama için bu sahte çağrılar hakkında bilgi yazıyor. 

Bu öğreticiyi tamamlayabilmeniz için, Akış Analizi işi tarafından yakalanan verilere bakmak isteyebilirsiniz. Veriler, öbeklerdeki (dosyalar) Azure blog depolamaya yazılır. Azure Blob depolamayı okuyan herhangi bir aracı kullanabilirsiniz. Önkoşullar bölümünde belirtildiği gibi, Visual Studio 'da Azure uzantıları 'nı kullanabilir veya [Azure Depolama Gezgini](https://storageexplorer.com/) ya da [ceruyalın](https://www.cerebrata.com/products/cerulean/features/azure-storage)gibi bir araç kullanabilirsiniz. 

BLOB depolama alanındaki bir dosyanın içeriğini incelediğinizde, aşağıdakine benzer bir şey görürsünüz:

   ![Akış Analizi çıkışı ile Azure Blob depolama](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sahtekarlık algılama senaryosuna devam eden ve bu öğreticide oluşturduğunuz kaynakları kullanan ek makaleler vardır. Devam etmek istiyorsanız, **sonraki adımlar**bölümündeki önerilere bakın.

Ancak, işiniz bittiğinde ve oluşturduğunuz kaynaklara ihtiyacınız yoksa, gereksiz Azure ücretlerine tabi kalmaması için bunları silebilirsiniz. Bu durumda, şunları yapmanız önerilir:

1. Akış Analizi işini durdurun. **İşler** bölmesinde en üstteki **Durdur** ' a tıklayın.
2. Telco Oluşturucu uygulamasını durdurun. Uygulamayı başlattığınız komut penceresinde CTRL + C tuşlarına basın.
3. Bu öğreticide yalnızca yeni bir BLOB depolama hesabı oluşturduysanız, silin. 
4. Akış Analizi işini silin.
5. Olay Hub 'ını silin.
6. Olay Hub 'ı ad alanını silin.

## <a name="get-support"></a>Destek alın

Daha fazla yardım için deneyin [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticiye aşağıdaki makaleyle devam edebilirsiniz:

* [Stream Analytics ve Power BI: Veri](stream-analytics-power-bi-dashboard.md)akışı için gerçek zamanlı analiz panosu. Bu makalede, Stream Analytics işin TelCo çıkışını gerçek zamanlı görselleştirme ve analizler için Power BI nasıl göndereceğiniz gösterilmektedir.

Genel olarak Stream Analytics hakkında daha fazla bilgi için şu makalelere bakın:

* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)
