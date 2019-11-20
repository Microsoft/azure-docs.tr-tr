---
title: Azure Stream Analytics ile gerçek zamanlı Twitter yaklaşım Analizi
description: Bu makalede, Stream Analytics gerçek zamanlı Twitter yaklaşım analizi için nasıl kullanılacağı açıklanır. Canlı bir panodaki olay üretimi verilerine yönelik adım adım yönergeler.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 8561789d53c3c1b00ac1477909bcbe356fe6a85d
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173164"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure Stream Analytics 'de gerçek zamanlı Twitter yaklaşım Analizi

Gerçek zamanlı Twitter olaylarını Azure Event Hubs 'a taşıyarak sosyal medya analizine yönelik yaklaşım çözümleme çözümü oluşturmayı öğrenin. Sonra verileri çözümlemek için Azure Stream Analytics bir sorgu yazın ve daha sonra kullanmak üzere sonuçları depolayın veya gerçek zamanlı Öngörüler sağlamak için bir [Power BI](https://powerbi.com/) panosu oluşturun.

Sosyal Medya Analizi araçları kuruluşların popüler konuları anlamasına yardımcı olur. Popüler konular sosyal medyada yüksek hacimlerin bulunduğu konularla ve avların bulunduğu konulardır. Düşünce *madenciliği*olarak da adlandırılan yaklaşım analizi, sosyal medya analizi araçlarını kullanarak bir ürüne veya fikre yaklaşmayı belirlemektir. 

Gerçek zamanlı Twitter eğilim analizi, bir analiz aracına yönelik harika bir örnektir çünkü diyez etiketi abonelik modeli belirli anahtar sözcükleri (hashtags) dinlemenize ve akışın yaklaşım analizini geliştirmenize olanak sağlar.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Senaryo: Gerçek zamanlı sosyal medya yaklaşım Analizi

News medya web sitesi olan bir şirket, okuyucular üzerinde anında ilgili site içeriği sunarak rakiplerinin avantajlarından faydalanmaya ilgileniyor. Şirket, Twitter verilerinin gerçek zamanlı yaklaşım analizini yaparak okuyucularla ilgili konularda sosyal medya analizini kullanır.

Şirket içi konuları Twitter 'da gerçek zamanlı olarak belirlemek için, şirketin önemli konular hakkında tweet hacim ve yaklaşım hakkında gerçek zamanlı analizler olması gerekir.

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunda, Twitter 'a bağlanan ve belirli bir hashtags olan (ayarlayabileceğiniz) bir istemci uygulaması kullanırsınız. Uygulamayı çalıştırmak ve Azure Akış Analizi 'ni kullanarak fazla doldurulabilir olması için aşağıdakilere sahip olmanız gerekir:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* [Twitter](https://twitter.com) hesabı.
* Twitter akışını okuyan, dallı bir Istemci uygulaması. Bu uygulamayı almak için, GitHub 'da [dallı bir Client. zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) dosyasını indirin ve ardından paketi bilgisayarınızdaki bir klasöre ayıklayın. Kaynak kodu görmek ve uygulamayı bir hata ayıklayıcıda çalıştırmak istiyorsanız, kaynak kodu [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient)' dan alabilirsiniz. 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Akış Analizi girişi için bir olay hub 'ı oluşturma

Örnek uygulama, olayları oluşturur ve bunları bir Azure Olay Hub 'ına gönderir. Azure Olay Hub 'ları, Stream Analytics için tercih edilen olay alma yöntemidir. Daha fazla bilgi için bkz. [Azure Event Hubs belgeleri](../event-hubs/event-hubs-what-is-event-hubs.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Olay Hub 'ı ad alanı ve Olay Hub 'ı oluşturma
Bir olay hub 'ı ad alanı oluşturun ve ardından bu ad alanına bir olay hub 'ı ekleyin. Olay Hub 'ı ad alanları, ilgili olay veri yolu örneklerini mantıksal olarak gruplamak için kullanılır. 

1. Azure Portal oturum açın ve**Olay Hub** **nesnelerin interneti** >  **kaynak** > oluştur ' a tıklayın. 

2. **Ad alanı oluştur** dikey penceresinde, gibi bir ad alanı adı `<yourname>-socialtwitter-eh-ns`girin. Ad alanı için herhangi bir ad kullanabilirsiniz, ancak ad bir URL için geçerli olmalıdır ve Azure genelinde benzersiz olmalıdır. 
    
3. Bir abonelik seçin ve bir kaynak grubu oluşturun veya seçin, ardından **Oluştur**' a tıklayın. 

    ![Olay hub'ı ad alanı oluşturma](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Ad alanı dağıtımı bitirdiğinde, Azure kaynakları listenizde Olay Hub 'ı ad alanını bulun. 

5. Yeni ad alanına tıklayın ve ad alanı Dikey penceresinde **+&nbsp;Olay Hub**' ı tıklatın. 

    ![Yeni bir olay hub 'ı oluşturmak için Olay Hub 'ı Ekle düğmesi](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Yeni Olay Hub 'ını `socialtwitter-eh`adlandırın. Farklı bir ad kullanabilirsiniz. Bunu yaparsanız, bu adı daha sonra gerekli olduğunuzdan emin olun. Olay Hub 'ı için başka herhangi bir seçenek ayarlamanıza gerek yoktur.

    ![Yeni bir olay hub 'ı oluşturmak için dikey pencere](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. **Oluştur**'a tıklayın.


### <a name="grant-access-to-the-event-hub"></a>Olay Hub 'ına erişim izni verme

Bir işlemin bir olay hub 'ına veri gönderebilmesi için, Olay Hub 'ının uygun erişime izin veren bir ilkesi olması gerekir. Erişim ilkesi, yetkilendirme bilgilerini içeren bir bağlantı dizesi oluşturur.

1.  Olay ad alanı Dikey penceresinde, **Event Hubs** ' a ve ardından yeni Olay Hub 'ınızın adına tıklayın.

2.  Olay Hub 'ı dikey penceresinde, **paylaşılan erişim ilkeleri** ' ne ve ardından **+&nbsp;Ekle**' ye tıklayın.

    >[!NOTE]
    >Olay Hub 'ı ad alanı değil, Olay Hub 'ı ile çalıştığınızdan emin olun.

3.  Ve talep için adlı `socialtwitter-access` bir ilkeekleyin, **Yönet**' i seçin.

    ![Yeni Olay Hub 'ı erişim ilkesi oluşturmak için dikey pencere](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  **Oluştur**'a tıklayın.

5.  İlke dağıtıldıktan sonra, paylaşılan erişim ilkeleri listesinde buna tıklayın.

6.  **Bağlantı dizesi-BIRINCIL anahtar** etiketli kutuyu bulun ve bağlantı dizesinin yanındaki Kopyala düğmesine tıklayın. 
    
    ![Erişim ilkesinden birincil bağlantı dizesi anahtarı kopyalanıyor](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Bağlantı dizesini bir metin düzenleyicisine yapıştırın. Bu bağlantı dizesine, bazı küçük düzenlemeler yaptıktan sonra bir sonraki bölüm için ihtiyacınız vardır.

    Bağlantı dizesi şuna benzer:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Bağlantı dizesinin, noktalı virgülle ayrılmış birden çok anahtar-değer çifti içerdiğini `Endpoint`unutmayın: `SharedAccessKey`, `SharedAccessKeyName`, ve `EntityPath`.  

    > [!NOTE]
    > Güvenlik için örnekteki bağlantı dizesinin parçaları kaldırılmıştır.

8.  Metin düzenleyicisinde, bağlantıyı bağlantı dizesinden kaldırın `EntityPath` (bundan önce gelen noktalı virgülü kaldırmayı unutmayın). İşiniz bittiğinde, bağlantı dizesi şöyle görünür:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Twitter istemci uygulamasını yapılandırma ve başlatma
İstemci uygulaması doğrudan Twitter 'dan Tweet olaylarını alır. Bunu yapmak için Twitter akış API 'Lerini çağırmak için izne ihtiyacı vardır. Bu izni yapılandırmak için Twitter 'da benzersiz kimlik bilgileri üreten (bir OAuth belirteci gibi) bir uygulama oluşturursunuz. Daha sonra, istemci uygulamasını, API çağrıları yaptığında bu kimlik bilgilerini kullanacak şekilde yapılandırabilirsiniz. 

### <a name="create-a-twitter-application"></a>Twitter uygulaması oluşturma
Bu nasıl yapılır Kılavuzu için kullanabileceğiniz bir Twitter uygulamanız yoksa, bir tane oluşturabilirsiniz. Zaten bir Twitter hesabınız olmalıdır.

> [!NOTE]
> Bir uygulama oluşturmak ve anahtarları, parolaları ve belirteci almak için Twitter 'daki tam işlem değişebilir. Bu yönergeler Twitter sitesinde gördükleriyle eşleşmiyorsa Twitter geliştirici belgelerine bakın.

1. Bir Web tarayıcısından, [geliştiriciler Için Twitter](https://developer.twitter.com/en/apps)' a gidin ve **uygulama oluştur**' u seçin. Twitter geliştirici hesabı için uygulamanız gerektiğini söyleyen bir ileti görebilirsiniz. Bu işlemi ücretsiz yapın ve uygulamanız onaylandıktan sonra bir onay e-postası görmeniz gerekir. Bir geliştirici hesabının onaylanması birkaç gün sürebilir.

   ![Twitter geliştirici hesabı onayı](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-dev-confirmation.png "Twitter geliştirici hesabı onayı")

   ![Twitter uygulaması ayrıntıları](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Twitter uygulaması ayrıntıları")

2. **Uygulama oluşturun** sayfasında yeni uygulamaya ilişkin ayrıntıları sağlayın ve **Kendi Twitter uygulamanızı oluşturun**’u seçin.

   ![Twitter uygulaması ayrıntıları](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Twitter uygulaması ayrıntıları")

3. Uygulama sayfasında **anahtarlar ve belirteçler** sekmesini seçin ve **Tüketici API anahtarı** ve **Tüketici API 'si gizli anahtarı**için değerleri kopyalayın. Ayrıca, erişim belirteçlerini oluşturmak için erişim belirteci altında **Oluştur** **ve erişim belirteci gizli anahtarı** ' nı seçin. **Erişim Belirteci** ve**Erişim Belirteci Parolası** değerlerini kopyalayın.

    ![Twitter uygulaması ayrıntıları](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-app-key-secret.png "Twitter uygulaması ayrıntıları")

Twitter uygulaması için aldığınız değerleri kaydedin. Daha sonra, nasıl yapılır ile değerler gerekir.

>[!NOTE]
>Twitter uygulaması için anahtarlar ve gizli diziler Twitter hesabınıza erişim sağlar. Bu bilgileri, Twitter parolanızla yaptığınız gibi hassas olarak değerlendirin. Örneğin, bu bilgileri başkalarına verdiğiniz bir uygulamaya gömmeyin. 


### <a name="configure-the-client-application"></a>İstemci uygulamasını yapılandırma
Belirli bir konu kümesiyle ilgili Tweet olayları toplamak için [Twitter 'Nın akış API 'lerini](https://dev.twitter.com/streaming/overview) kullanarak Twitter verilerine bağlanan bir istemci uygulaması oluşturduk. Uygulama, her Tweet aşağıdaki yaklaşım değerini atayan [Sentiment140](http://help.sentiment140.com/) açık kaynak aracı 'nı kullanır:

* 0 = negatif
* 2 = nötr
* 4 = pozitif

Tweet olaylarına bir yaklaşım değeri atandıktan sonra, bunlar daha önce oluşturduğunuz Olay Hub 'ına gönderilir.

Uygulama çalışmadan önce, Twitter anahtarları ve Olay Hub 'ı bağlantı dizesi gibi belirli bilgileri sizin için de gerektirir. Yapılandırma bilgilerini şu yollarla sağlayabilirsiniz:

* Uygulamayı çalıştırın ve ardından uygulama kullanıcı arabirimini kullanarak anahtarları, parolaları ve bağlantı dizesini girin. Bunu yaparsanız, yapılandırma bilgileri geçerli oturumunuz için kullanılır, ancak kaydedilmez.
* Uygulamanın. config dosyasını düzenleyin ve değerleri burada ayarlayın. Bu yaklaşım yapılandırma bilgilerini sürdürür, ancak bu potansiyel olarak hassas bilgilerin bilgisayarınızda düz metin olarak depolandığı anlamına gelir.

Aşağıdaki yordam her iki yaklaşımı de belgelemektedir. 

1. Ön koşullar bölümünde listelendiği gibi, dallı hale getirme [istemci. zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) uygulamasını indirdiğinizden ve sıkıştırdığınızdan emin olun.

2. Çalışma zamanında değerleri ayarlamak için (ve yalnızca geçerli oturum için), `TwitterWPFClient.exe` uygulamayı çalıştırın. Uygulama sizi isterse, aşağıdaki değerleri girin:

    * Twitter tüketici anahtarı (API anahtarı).
    * Twitter tüketicisi gizli anahtarı (API parolası).
    * Twitter erişim belirteci.
    * Twitter erişim belirteci parolası.
    * Daha önce kaydettiğiniz bağlantı dizesi bilgileri. `EntityPath` Anahtar-değer çiftini Kaldırdığınız bağlantı dizesini kullandığınızdan emin olun.
    * Yaklaşımını öğrenmek istediğiniz Twitter anahtar sözcükleri.

   ![Üzerinde çalışan ve örtülü ayarları gösteren dallı bir Istemci uygulaması](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Değerleri kalıcı olarak ayarlamak için, bir metin düzenleyicisini kullanarak, dallı bir. exe. config dosyasını açın. `<appSettings>` Ardından öğesinde şunu yapın:

   * Twitter `oauth_consumer_key` tüketici anahtarına (API anahtarı) ayarlayın. 
   * Twitter `oauth_consumer_secret` tüketicisi gizli anahtarı (API gizli) olarak ayarlanır.
   * Twitter `oauth_token` erişim belirtecine ayarlayın.
   * Twitter `oauth_token_secret` erişim belirteci parolası olarak ayarlayın.

     `<appSettings>` Öğesinde daha sonra şu değişiklikleri yapın:

   * Olay `EventHubName` hub 'ı adına (yani, varlık yolunun değerine) ayarlayın.
   * Bağlantı `EventHubNameConnectionString` dizesine ayarlayın. `EntityPath` Anahtar-değer çiftini Kaldırdığınız bağlantı dizesini kullandığınızdan emin olun.

     Bu `<appSettings>` bölüm aşağıdaki örneğe benzer şekilde görünür. (Açıklık ve güvenlik için bazı satırları sarmaladık ve bazı karakterleri kaldırdık.)

     ![Twitter anahtar ve gizli dizilerini ve Olay Hub 'ı bağlantı dizesi bilgilerini gösteren bir metin düzenleyicisinde dallı bir Istemci uygulama yapılandırma dosyası](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Uygulamayı henüz başlatmadıysanız, şimdi dallı bir Client. exe ' yi çalıştırın. 

5. Sosyal yaklaşım toplamak için yeşil Başlat düğmesine tıklayın. Olay Hub 'ınıza gönderilen **Createdat**, **konu başlığı**ve **Sentimentscore** değerlerinin Tweet olaylarını görürsünüz.

    ![Çalışan bir listesini gösteren, çalışan dallı bir Istemci uygulaması](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Hata görürseniz ve pencerenin alt bölümünde görüntülenmekte olan bir KAG akışı görmüyorsanız, anahtarları ve gizli dizileri çift kontrol edin. Ayrıca bağlantı dizesini denetleyin ( `EntityPath` anahtar ve değeri içermediğinden emin olun.)


## <a name="create-a-stream-analytics-job"></a>Akış Analizi işi oluşturma

Artık Tweet olayları Twitter 'dan gerçek zamanlı olarak akışa alındığından, bu olayları gerçek zamanlı olarak çözümlemek için bir Stream Analytics işi ayarlayabilirsiniz.

1. Azure Portal,**Stream Analytics iş** **nesnelerin interneti** >  **kaynak** > oluştur ' a tıklayın.

2. İşi `socialtwitter-sa-job` adlandırın ve bir abonelik, kaynak grubu ve konum belirtin.

    En iyi performans için işi ve Olay Hub 'ını aynı bölgeye yerleştirmeniz ve bu sayede verileri bölgeler arasında aktarmaya ödeme yapmanız iyi bir fikirdir.

    ![Yeni bir Stream Analytics işi oluşturma](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. **Oluştur**'a tıklayın.

    İş oluşturulur ve Portal iş ayrıntılarını görüntüler.


## <a name="specify-the-job-input"></a>İş girişini belirtin

1. Stream Analytics işte iş **topolojisi** altında iş dikey penceresinin altında **girişler**' e tıklayın. 

2. **Girişler** dikey penceresinde **+&nbsp;Ekle** ' ye tıklayın ve ardından dikey pencereyi şu değerlerle doldurun:

   * **Giriş diğer adı**: Adı `TwitterStream`kullanın. Farklı bir ad kullanırsanız, daha sonra ihtiyacınız olduğu için bunu bir yere unutmayın.
   * **Kaynak türü**: **Veri akışını**seçin.
   * **Kaynak**: **Olay Hub 'ını**seçin.
   * **Içeri aktarma seçeneği**: **Geçerli abonelikteki Olay Hub 'ını kullan ' ı**seçin. 
   * **Service Bus ad alanı**: Daha önce oluşturduğunuz (`<yourname>-socialtwitter-eh-ns`) Olay Hub 'ı ad alanını seçin.
   * **Olay Hub 'ı**: Daha önce oluşturduğunuz Olay Hub 'ını (`socialtwitter-eh`) seçin.
   * **Olay Hub 'ı ilke adı**: Daha önce oluşturduğunuz erişim ilkesini seçin (`socialtwitter-access`).

     ![Akış Analizi işi için yeni giriş oluştur](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. **Oluştur**'a tıklayın.


## <a name="specify-the-job-query"></a>İş sorgusunu belirtin

Stream Analytics, dönüşümleri açıklayan basit, bildirime dayalı bir sorgu modelini destekler. Dil hakkında daha fazla bilgi edinmek için bkz. [Azure Stream Analytics sorgu dili başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).  Bu nasıl yapılır Kılavuzu, Twitter verileri üzerinde birkaç sorguyu yazmanıza ve test etmenize yardımcı olur.

Konular arasındaki bahsetmeler sayısını karşılaştırmak için, her beş saniyede bir konuya göre bahsetmeler sayısını almak üzere atlayan bir [pencere](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) kullanabilirsiniz.

1. Henüz yapmadıysanız **giriş** dikey penceresini kapatın.

2. **Genel bakış** dikey penceresinde sorgu kutusunun sağ üst köşesindeki **Sorguyu Düzenle** ' ye tıklayın. Azure, iş için yapılandırılmış girişleri ve çıkışları listeler ve giriş akışını çıkışa gönderilirken dönüştürmenizi sağlayan bir sorgu oluşturmanızı sağlar.

3. Dallı hale getirme Istemci uygulamasının çalıştığından emin olun. 

3. **Sorgu** dikey penceresinde, `TwitterStream` girişin yanındaki noktalara tıklayın ve ardından **girişten örnek veriler**' i seçin.

    ![Akış Analizi işi girişi için örnek verileri kullanmak üzere menü seçenekleri "girişten gelen örnek veriler" seçilir](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Bu, giriş akışının ne kadar süreceğine ilişkin olarak tanımlanan, ne kadar örnek veri alınacağını belirtmenize imkan tanıyan bir dikey pencere açar.

4. **Dakikaları** 3 olarak ayarlayın ve ardından **Tamam**' a tıklayın. 
    
    !["3 dakika" seçiliyken giriş akışını örnekleme seçenekleri.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Azure, giriş akışından 3 dakikalık bir değer verir ve örnek veriler ne zaman hazırlandığını size bildirir. (Bu kısa bir süre sürer.) 

    Örnek veriler geçici olarak depolanır ve sorgu penceresi açıkken kullanılabilir. Sorgu penceresini kapatırsanız, örnek veriler atılır ve yeni bir örnek veri kümesi oluşturmanız gerekir. 

5. Kod düzenleyicisinde sorguyu aşağıdaki gibi değiştirin:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Giriş için diğer `TwitterStream` ad olarak kullanmadıysa, `TwitterStream` diğer adınızı sorguda yerine koyun.  

    Bu sorgu, zamana bağlı hesaplamada kullanılacak yükün zaman damgası alanını belirtmek için anahtar sözcük **Ile zaman damgası** kullanır. Bu alan belirtilmemişse, her olayın olay hub 'ına ulaştığı süre kullanılarak Pencereleme işlemi gerçekleştirilir. [Stream Analytics sorgu başvurusunun](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)"varış saati vs uygulama süresi" bölümünde daha fazla bilgi edinin.

    Bu sorgu ayrıca **System. Timestamp** özelliğini kullanarak her pencerenin sonuna yönelik bir zaman damgasına erişir.

5. **Test**' e tıklayın. Sorgu, örneklediğiniz verilere karşı çalışır.
    
6. **Kaydet**’e tıklayın. Bu, sorguyu Akış Analizi işinin bir parçası olarak kaydeder. (Örnek verileri kaydetmez.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Akıştan farklı alanlar kullanmayı deneyin 

Aşağıdaki tabloda JSON akış verilerinin parçası olan alanlar listelenmektedir. Sorgu Düzenleyicisi ' nde denemeler yapmak ücretsizdir.

|JSON özelliği | Tanım|
|--- | ---|
|CreatedAt | Tweet oluşturulduğu zaman|
|Konu | Belirtilen anahtar sözcükle eşleşen konu|
|SentimentScore | Sentiment140 'in yaklaşım puanı|
|Yazar | Tweet gönderen Twitter tanıtıcısı|
|Text | Tweet tam gövdesi|


## <a name="create-an-output-sink"></a>Çıkış havuzu oluşturma

Artık bir olay akışı, olayları almak için bir olay hub 'ı girişi ve Stream üzerinde bir dönüştürme gerçekleştirmek için bir sorgu tanımladınız. Son adım iş için bir çıkış havuzu tanımlamaktır.  

Bu nasıl yapılır kılavuzunda, toplanan Tweet olaylarını iş sorgusundan Azure Blob depolama alanına yazarsınız.  Ayrıca, uygulamanızın ihtiyaçlarına bağlı olarak Azure SQL veritabanı, Azure Tablo depolama, Event Hubs veya Power BI sonuçlarınızı de gönderebilirsiniz.

## <a name="specify-the-job-output"></a>İş çıktısını belirtin

1. **Iş topolojisi** bölümünde **Çıkış** kutusuna tıklayın. 

2. **Çıktılar** dikey penceresinde **+&nbsp;Ekle** ' ye tıklayın ve ardından dikey pencereyi şu değerlerle doldurun:

   * **Çıkış diğer adı**: Adı `TwitterStream-Output`kullanın. 
   * **Havuz**: **Blob depolama**'yı seçin.
   * **Içeri aktarma seçenekleri**: **Geçerli abonelikten blob depolamayı kullan**' ı seçin.
   * **Depolama hesabı**. **Yeni depolama hesabı oluştur** ' u seçin.
   * **Depolama hesabı** (ikinci kutu). Ad veya başka bir benzersiz dize girin `YOURNAMEsa`. `YOURNAME` Ad yalnızca küçük harflerden ve rakamlardan kullanabilirsiniz ve Azure genelinde benzersiz olmalıdır. 
   * **Kapsayıcı**. `socialtwitter` yazın.
     Depolama hesabı adı ve kapsayıcı adı, blob depolaması için aşağıdaki gibi bir URI sağlamak üzere birlikte kullanılır: 

     `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
     ![Stream Analytics işi için "yeni çıkış" dikey penceresi](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. **Oluştur**'a tıklayın. 

    Azure, depolama hesabı oluşturur ve otomatik olarak bir anahtar oluşturur. 

5. **Çıktılar** dikey penceresini kapatın. 


## <a name="start-the-job"></a>İşi başlatma

Bir iş girişi, sorgu ve çıkış belirtilir. Stream Analytics işi başlamaya hazırlanıyor.

1. Dallı hale getirme Istemci uygulamasının çalıştığından emin olun. 

2. İş dikey penceresinde **Başlat**' a tıklayın.

    ![Stream Analytics işini Başlat](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. **Işi Başlat** dikey penceresinde, **iş çıkışı başlangıç zamanı**için **Şimdi** ' yi seçin ve ardından **Başlat**' a tıklayın. 

    ![Stream Analytics işi için "işi Başlat" dikey penceresi](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    İş başladığında Azure size bildirir ve iş dikey penceresinde durum **çalışıyor**olarak görüntülenir.

    ![İş çalışıyor](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Yaklaşım analizi için çıktıyı görüntüleme

İşiniz çalışmaya başladıktan ve gerçek zamanlı Twitter akışını işledikten sonra, yaklaşım analizi için çıktıyı görüntüleyebilirsiniz.

İş çıktlarınızı gerçek zamanlı olarak görüntülemek için [Azure Depolama Gezgini](https://storageexplorer.com/) veya [Azure Explorer](https://www.cerebrata.com/products/azure-explorer/introduction) gibi bir araç kullanabilirsiniz. Buradan, uygulamanızı aşağıdaki ekran görüntüsünde gösterildiği gibi özelleştirilmiş bir pano içerecek şekilde genişletmek için [Power BI](https://powerbi.com/) kullanabilirsiniz:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Popüler konuları belirlemek için başka bir sorgu oluşturun

Twitter yaklaşımını anlamak için kullanabileceğiniz başka bir sorgu, [kayan bir pencereye](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)göre belirlenir. Popüler konuları belirlemek için, belirli bir süre içinde bahsetmeler için eşik değeri geçen konuları arayın.

Bu nasıl yapılır amacıyla, son 5 saniye içinde 20 kereden fazla bahsedilen konuları kontrol edersiniz.

1. İşi durdurmak için iş dikey penceresinde **Durdur** ' a tıklayın. 

2. **Iş topolojisi** bölümünde **sorgu** kutusuna tıklayın. 

3. Sorguyu aşağıdaki şekilde değiştirin:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. **Kaydet**’e tıklayın.

5. Dallı hale getirme Istemci uygulamasının çalıştığından emin olun. 

6. Yeni sorguyu kullanarak işi yeniden başlatmak için **Başlat** ' a tıklayın.


## <a name="get-support"></a>Destek alın
Daha fazla yardım için deneyin bizim [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)
