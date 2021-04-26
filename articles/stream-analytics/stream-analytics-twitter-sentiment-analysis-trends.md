---
title: Azure Stream Analytics ile Sosyal Medya Analizi
description: Bu makalede, Twitter istemci API 'sini kullanarak sosyal medya analizinin Stream Analytics nasıl kullanılacağı açıklanır. Canlı bir panodaki olay üretimi verilerine yönelik adım adım yönergeler.
services: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: e2369e8f4349a3b4a83c1729c6414d319dd8419e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589676"
---
# <a name="social-media-analysis-with-azure-stream-analytics"></a>Azure Stream Analytics ile Sosyal Medya Analizi

Bu makalede, gerçek zamanlı Twitter olaylarını Azure Event Hubs 'a taşıyarak sosyal medya yaklaşımı çözümleme çözümünü nasıl oluşturabileceğiniz öğretilir. Verileri analiz etmek ve daha sonra kullanmak üzere sonuçları depolamak ve gerçek zamanlı Öngörüler sağlamak için bir [Power BI](https://powerbi.com/) panosu oluşturmak üzere bir Azure Stream Analytics sorgusu yazın.

Sosyal Medya Analizi araçları kuruluşların popüler konuları anlamasına yardımcı olur. Popüler konular sosyal medyada yüksek hacimlerin bulunduğu konularla ve avların bulunduğu konulardır. Düşünce *madenciliği* olarak da adlandırılan yaklaşım analizi, sosyal medya analizi araçlarını kullanarak bir ürüne veya fikre yaklaşmayı belirlemektir. 

Gerçek zamanlı Twitter eğilim analizi, bir analiz aracına yönelik harika bir örnektir çünkü diyez etiketi abonelik modeli belirli anahtar sözcükleri (hashtags) dinlemenize ve akışın yaklaşım analizini geliştirmenize olanak sağlar.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Senaryo: gerçek zamanlı sosyal medya yaklaşım Analizi

News medya web sitesi olan bir şirket, okuyucular üzerinde anında ilgili site içeriği sunarak rakiplerinin avantajlarından faydalanmaya ilgileniyor. Şirket, Twitter verilerinin gerçek zamanlı yaklaşım analizini yaparak okuyucularla ilgili konularda sosyal medya analizini kullanır.

Şirket içi konuları Twitter 'da gerçek zamanlı olarak belirlemek için, şirketin önemli konular hakkında tweet hacim ve yaklaşım hakkında gerçek zamanlı analizler olması gerekir.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunda, Twitter 'a bağlanan ve belirli bir hashtags olan (ayarlayabileceğiniz) bir istemci uygulaması kullanırsınız. Uygulamayı çalıştırmak ve Azure Akış Analizi 'ni kullanarak fazla doldurulabilir olması için aşağıdakilere sahip olmanız gerekir:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

* [Twitter](https://twitter.com) hesabı.

* Twitter akışını okuyan, dallı bir sanal çekirdek uygulaması. Bu uygulamayı almak için, [dallı Istemci çekirdeğini](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore)indirin.

* [.NET Core CLI](/dotnet/core/tools/?tabs=netcore2x) sürümünü 2.1.0 ' ü yükler.

Uygulayabileceğiniz çözüm mimarisi aşağıda verilmiştir.

   ![Çözümü oluşturmak için kullanılan farklı hizmet ve uygulama parçalarını gösteren bir diyagram.](./media/stream-analytics-twitter-sentiment-analysis-trends/solution-diagram.png "Çözüm diyagramı")

## <a name="create-an-event-hub-for-streaming-input"></a>Akış girişi için bir olay hub 'ı oluşturma

Örnek uygulama, olayları oluşturur ve bunları bir Azure Olay Hub 'ına gönderir. Azure Event Hubs, Stream Analytics için tercih edilen olay alma yöntemidir. Daha fazla bilgi için bkz. [Azure Event Hubs belgeleri](../event-hubs/event-hubs-about.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Olay Hub 'ı ad alanı ve Olay Hub 'ı oluşturma
Bu bölümde, bir olay hub 'ı ad alanı oluşturur ve bu ad alanına bir olay hub 'ı eklersiniz. Olay Hub 'ı ad alanları, ilgili olay veri yolu örneklerini mantıksal olarak gruplamak için kullanılır. 

1. Azure portal oturum açın ve **kaynak oluştur**' u seçin. Ni. **Event Hubs** arayın ve **Oluştur**' u seçin.

2. **Ad alanı oluştur** sayfasında, bir ad alanı adı girin. Ad alanı için herhangi bir ad kullanabilirsiniz, ancak ad bir URL için geçerli olmalıdır ve Azure genelinde benzersiz olmalıdır. 
    
3. Bir fiyatlandırma katmanı ve abonelik seçin ve bir kaynak grubu oluşturun veya seçin. Sonra bir konum seçin ve **Oluştur**' u seçin. 
 
4. Ad alanı dağıtımı tamamladığında, kaynak grubunuza gidin ve Azure kaynakları listenizde Olay Hub 'ı ad alanını bulun. 

5. Yeni ad alanında **+ &nbsp; Olay Hub**'ı ' nı seçin. 

6. Yeni Olay Hub 'ı *socialtwiur-Eh* olarak adlandırın. Farklı bir ad kullanabilirsiniz. Bunu yaparsanız, bu adı daha sonra gerekli olduğunuzdan emin olun. Olay Hub 'ı için başka herhangi bir seçenek ayarlamanıza gerek yoktur.
 
7. **Oluştur**’u seçin.

### <a name="grant-access-to-the-event-hub"></a>Olay Hub 'ına erişim izni verme

Bir işlemin bir olay hub 'ına veri gönderebilmesi için, Olay Hub 'ının erişime izin veren bir ilkeye ihtiyacı vardır. Erişim ilkesi, yetkilendirme bilgilerini içeren bir bağlantı dizesi oluşturur.

1.  Olay Hub 'larınızın sol tarafındaki gezinti çubuğunda, **varlıklar** bölümünde bulunan **Event Hubs**' yi seçin. Ardından, az önce oluşturduğunuz Olay Hub 'ını seçin.

2.  Sol taraftaki Gezinti çubuğunda, **Ayarlar**' ın altında bulunan **paylaşılan erişim ilkeleri** ' ni seçin.

    >[!NOTE]
    >Olay Hub 'ı ad alanı ve Olay Hub 'ı için altında bir paylaşılan erişim ilkeleri seçeneği vardır. Genel olay hub 'ı ad alanını değil, Olay Hub 'ınız bağlamında çalıştığınızdan emin olun.

3.  Erişim ilkesi sayfasında **+ Ekle**' yi seçin. Ardından, **ilke adı** için *socialtwiur-Access* girin ve **Yönet** onay kutusunu işaretleyin.
 
4.  **Oluştur**’u seçin.

5.  İlke dağıtıldıktan sonra, paylaşılan erişim ilkeleri listesinden ilkeyi seçin.

6.  **Bağlantı dizesi birincil anahtarı** etiketli kutuyu bulun ve bağlantı dizesinin yanındaki Kopyala düğmesini seçin.
 
7.  Bağlantı dizesini bir metin düzenleyicisine yapıştırın. Bazı küçük düzenlemeler yaptıktan sonra, sonraki bölümde bu bağlantı dizesine ihtiyacınız vardır.

   Bağlantı dizesi şuna benzer:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Bağlantı dizesinin, noktalı virgülle ayrılmış birden çok anahtar-değer çifti içerdiğini unutmayın: `Endpoint` ,, `SharedAccessKeyName` `SharedAccessKey` ve `EntityPath` .  

   > [!NOTE]
   > Güvenlik için örnekteki bağlantı dizesinin parçaları kaldırılmıştır.

## <a name="configure-and-start-the-twitter-client-application"></a>Twitter istemci uygulamasını yapılandırma ve başlatma

İstemci uygulaması doğrudan Twitter 'dan Tweet olaylarını alır. Bunu yapmak için Twitter akış API 'Lerini çağırmak için izne ihtiyacı vardır. Bu izni yapılandırmak için Twitter 'da benzersiz kimlik bilgileri üreten (bir OAuth belirteci gibi) bir uygulama oluşturursunuz. Daha sonra, istemci uygulamasını, API çağrıları yaptığında bu kimlik bilgilerini kullanacak şekilde yapılandırabilirsiniz. 

### <a name="create-a-twitter-application"></a>Twitter uygulaması oluşturma
Bu nasıl yapılır Kılavuzu için kullanabileceğiniz bir Twitter uygulamanız yoksa, bir tane oluşturabilirsiniz. Zaten bir Twitter hesabınız olmalıdır.

> [!NOTE]
> Bir uygulama oluşturmak ve anahtarları, parolaları ve belirteci almak için Twitter 'daki tam işlem değişebilir. Bu yönergeler Twitter sitesinde gördükleriyle eşleşmiyorsa Twitter geliştirici belgelerine bakın.

1. Web tarayıcısından, [geliştiriciler Için Twitter](https://developer.twitter.com/en/apps)' a gidin, bir geliştirici hesabı oluşturun ve **uygulama oluştur**' u seçin. Twitter geliştirici hesabı için uygulamanız gerektiğini söyleyen bir ileti görebilirsiniz. Bu işlemi ücretsiz yapın ve uygulamanız onaylandıktan sonra bir onay e-postası görmeniz gerekir. Bir geliştirici hesabının onaylanması birkaç gün sürebilir.

   ![Ekran görüntüsü, uygulama oluştur düğmesini gösterir.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Twitter uygulaması ayrıntıları")

2. **Uygulama oluşturun** sayfasında yeni uygulamaya ilişkin ayrıntıları sağlayın ve **Kendi Twitter uygulamanızı oluşturun**’u seçin.

   ![Ekran görüntüsü, uygulamanızın değerlerini girebileceğiniz uygulama ayrıntıları bölmesini gösterir.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Twitter uygulaması ayrıntıları")

3. Uygulama sayfasında **anahtarlar ve belirteçler** sekmesini seçin ve **Tüketici API anahtarı** ve **Tüketici API 'si gizli anahtarı** için değerleri kopyalayın. Ayrıca, erişim belirteçlerini oluşturmak için erişim belirteci altında **Oluştur** **ve erişim belirteci gizli anahtarı** ' nı seçin. **Erişim Belirteci** ve **Erişim Belirteci Parolası** değerlerini kopyalayın.

   Twitter uygulaması için aldığınız değerleri kaydedin. Değerlerin daha sonra olması gerekir.

> [!NOTE]
> Twitter uygulaması için anahtarlar ve gizli diziler Twitter hesabınıza erişim sağlar. Bu bilgileri, Twitter parolanızla yaptığınız gibi hassas olarak değerlendirin. Örneğin, bu bilgileri başkalarına verdiğiniz bir uygulamaya gömmeyin. 

### <a name="configure-the-client-application"></a>İstemci uygulamasını yapılandırma

Belirli bir konu kümesiyle ilgili Tweet olayları toplamak için [Twitter 'Nın akış API 'lerini](https://dev.twitter.com/streaming/overview) kullanarak Twitter verilerine bağlanan bir istemci uygulaması oluşturduk.

Uygulama çalışmadan önce, Twitter anahtarları ve Olay Hub 'ı bağlantı dizesi gibi belirli bilgileri sizin için de gerektirir.

1. Ön koşullar bölümünde listelendiği gibi, [dallı](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) , sanal çekirdek uygulamasını indirdiğinizden emin olun.

2. *App.config* dosyasını açmak için bir metin düzenleyicisi kullanın. Öğesinde aşağıdaki değişiklikleri yapın `<appSettings>` :

   * `oauth_consumer_key`Twitter tüketici anahtarına (API anahtarı) ayarlayın. 
   * `oauth_consumer_secret`Twitter tüketicisi gizli anahtarı (API gizli anahtarı) olarak ayarlanır.
   * `oauth_token`Twitter erişim belirtecine ayarlayın.
   * `oauth_token_secret`Twitter erişim belirteci parolası olarak ayarlayın.
   * `EventHubNameConnectionString`Bağlantı dizesine ayarlayın.
   * `EventHubName`Olay Hub 'ı adına (varlık yolunun değeri) ayarlayın.

3. Komut satırını açın ve sanal kuruluşunuzun çekirdek uygulamanızın bulunduğu dizine gidin. `dotnet build`Projeyi derlemek için komutunu kullanın. Ardından, `dotnet run` uygulamayı çalıştırmak için komutunu kullanın. Uygulama, Olay Hub 'ınıza çimler gönderir.

## <a name="create-a-stream-analytics-job"></a>Akış Analizi işi oluşturma

Artık Tweet olayları Twitter 'dan gerçek zamanlı olarak akışa alındığından, bu olayları gerçek zamanlı olarak çözümlemek için bir Stream Analytics işi ayarlayabilirsiniz.

1. Azure portal, kaynak grubunuza gidin ve **+ Ekle**' yi seçin. **Stream Analytics işi** arayın ve **Oluştur**' u seçin.

2. İşi adlandırın `socialtwitter-sa-job` ve bir abonelik, kaynak grubu ve konum belirtin.

    En iyi performans için işi ve Olay Hub 'ını aynı bölgeye yerleştirmeniz ve bu sayede verileri bölgeler arasında aktarmaya ödeme yapmanız iyi bir fikirdir.

3. **Oluştur**’u seçin. Ardından dağıtım tamamlandığında işinize gidin.

## <a name="specify-the-job-input"></a>İş girişini belirtin

1. Stream Analytics işiniz için, **Iş topolojisi** altında sol menüden **girişler** ' i seçin.

2. **+ &nbsp; Akış girişi**  >  **Olay Hub 'ı** Ekle ' yi seçin. **Yeni giriş** formunu aşağıdaki bilgilerle doldurun:

   |**Ayar**  |**Önerilen değer**  |**Açıklama**  |
   |---------|---------|---------|
   |Girdi diğer adı| *Dallı bir akış* | Giriş için bir diğer ad girin. |
   |Abonelik  | \<Your subscription\> |  Kullanmak istediğiniz Azure aboneliğini seçin. |
   |Olay hub'ı ad alanı | *asa-Twitter-eventhub* |
   |Olay Hub'ı adı | *socialtwiur-Eh* | *Mevcut olanı kullan*' ı seçin. Ardından oluşturduğunuz Olay Hub 'ını seçin.|
   |Olay sıkıştırma türü| GZip | Veri sıkıştırma türü.|

   Kalan varsayılan değerleri bırakın ve **Kaydet**' i seçin.

## <a name="specify-the-job-query"></a>İş sorgusunu belirtin

Stream Analytics, dönüşümleri açıklayan basit, bildirime dayalı bir sorgu modelini destekler. Dil hakkında daha fazla bilgi edinmek için bkz. [Azure Stream Analytics sorgu dili başvurusu](/stream-analytics-query/stream-analytics-query-language-reference). Bu nasıl yapılır Kılavuzu, Twitter verileri üzerinde birkaç sorguyu yazmanıza ve test etmenize yardımcı olur.

Konular arasındaki bahsetmeler sayısını karşılaştırmak için, her beş saniyede bir konuya göre bahsetmeler sayısını almak üzere atlayan bir [pencere](/stream-analytics-query/tumbling-window-azure-stream-analytics) kullanabilirsiniz.

1. İş **genel bakışınızda** sorgu kutusunun sağ üst köşesindeki **Sorguyu Düzenle** ' yi seçin. Azure, iş için yapılandırılmış girişleri ve çıkışları listeler ve giriş akışını çıkışa gönderildiği şekilde dönüştürmek için bir sorgu oluşturmanıza olanak sağlar.

2. Sorgu Düzenleyicisi 'ndeki sorguyu aşağıdaki gibi değiştirin:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. İletilerdeki olay verileri sorgunuzun altındaki **giriş önizleme** penceresinde görünmelidir. **Görünümün** **JSON** olarak ayarlandığından emin olun. Herhangi bir veri görmüyorsanız, veri oluşturucunun Olay Hub 'ınıza olay göndermesini ve girişin sıkıştırma türü olarak **gzip** 'i seçtiğinizden emin olun.

4. **Test sorgusu** ' nu seçin ve sorgunuzun altındaki **test sonuçları** penceresinde sonuçlara dikkat edin.

5. Kod düzenleyicisinde sorguyu aşağıdaki gibi değiştirin ve **Test sorgusunu** seçin:

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Bu sorgu, *Azure* anahtar sözcüğünü içeren tüm çsları döndürür.

## <a name="create-an-output-sink"></a>Çıkış havuzu oluşturma

Artık bir olay akışı, olayları almak için bir olay hub 'ı girişi ve Stream üzerinde bir dönüştürme gerçekleştirmek için bir sorgu tanımladınız. Son adım iş için bir çıkış havuzu tanımlamaktır.  

Bu nasıl yapılır kılavuzunda, toplanan Tweet olaylarını iş sorgusundan Azure Blob depolama alanına yazarsınız.  Ayrıca, uygulamanızın ihtiyaçlarına bağlı olarak Azure SQL veritabanı, Azure Tablo depolama, Event Hubs veya Power BI sonuçlarınızı de gönderebilirsiniz.

## <a name="specify-the-job-output"></a>İş çıktısını belirtin

1. Sol gezinti menüsündeki **Iş topolojisi** bölümünde, **çıktılar**' i seçin. 

2. **Çıktılar** sayfasında, **+ &nbsp; Ekle** ve **BLOB depolama/Data Lake Storage 2.**' a tıklayın:

   * **Çıkış diğer adı**: adı kullanın `TwitterStream-Output` . 
   * **Içeri aktarma seçenekleri**: **aboneliklerinizden depolamayı Seç**' i seçin.
   * **Depolama hesabı**. Depolama hesabınızı seçin.
   * **Kapsayıcı**. **Yeni oluştur** ' u seçin ve girin `socialtwitter` .
   
4. **Kaydet**’i seçin.   

## <a name="start-the-job"></a>İşi başlatma

Bir iş girişi, sorgu ve çıkış belirtilir. Stream Analytics işi başlamaya hazırlanıyor.

1. Sanal çekirdek uygulamanın çalıştığından emin olun. 

2. İş genel görünümünde **Başlat**' ı seçin.

3. İşi **Başlat** sayfasında, **iş çıkışı başlangıç zamanı** için **Şimdi** ' yi seçin ve ardından **Başlat**' ı seçin.

## <a name="get-support"></a>Destek alın
Daha fazla yardım için, [Azure Stream Analytics Için Microsoft Q&soru sayfasını](/answers/topics/azure-stream-analytics.html)deneyin.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream Analytics giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](/rest/api/streamanalytics/)
