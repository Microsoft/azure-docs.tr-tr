---
title: Azure Izleyici veri toplayıcı API 'SI ile veri işlem hattı oluşturma | Microsoft Docs
description: REST API çağırabileceğiniz herhangi bir istemciden Log Analytics çalışma alanına JSON verisi gönder eklemek için Azure Izleyici HTTP Veri Toplayıcı API 'sini kullanabilirsiniz. Bu makalede, dosyalarda depolanan verilerin otomatik bir şekilde nasıl yükleneceği açıklanır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 08/09/2018
ms.openlocfilehash: 8b739d86ec557ca8c7de7e0999c905b51d1d97a7
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932625"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Veri Toplayıcı API 'SI ile veri işlem hattı oluşturma

[Azure Izleyici veri toplayıcı API 'si](data-collector-api.md) , tüm özel günlük verilerini Azure izleyici 'de bir Log Analytics çalışma alanına aktarmanıza olanak tanır. Tek gereksinimler, verilerin JSON ile biçimlendirilmesi ve 30 MB veya daha az kesimle bölünmesi olabilir. Bu, pek çok şekilde takılan tamamen esnek bir mekanizmadır: doğrudan uygulamanızdan, tek kapalı geçici karşıya yüklemelere. Bu makalede, yaygın bir senaryo için bazı başlangıç noktaları ana hatlarıyla gösterilir: dosyalarda depolanan verileri düzenli ve otomatik olarak karşıya yükleme ihtiyacı. Burada sunulan işlem hattı en iyi duruma getirilmeyecek veya başka bir şekilde iyileştirilse de, kendi kendinize ait bir üretim işlem hattı oluşturmak için bir başlangıç noktası olarak kullanılmaya yöneliktir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Örnek sorun
Bu makalenin geri kalanında, Application Insights sayfa görünümü verilerini inceleyeceğiz. Kuramsal senaryolarımızda, Application Insights SDK tarafından toplanan coğrafi bilgilerin, dünyanın her yerindeki ülke/bölgenin popülasyonu içeren özel verilere ilişkilendirilmesi ve nerede harcamamız gerektiğini belirleme amacını almak istiyoruz en çok pazarlama dolar. 

Bu amaçla [Dünya çapında popülasyon aday adayları](https://esa.un.org/unpd/wpp/) gibi bir genel veri kaynağı kullanıyoruz. Veriler aşağıdaki basit şemaya sahip olacaktır:

![Örnek basit şema](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

Bizim örneğimizde, yeni bir dosyayı en son yılın verileriyle birlikte karşıya yükleyeceğiz.

## <a name="general-design"></a>Genel tasarım
Ardışık düzen tasarlamanızı tasarlamak için klasik bir ETL-Type mantığı kullanıyoruz. Mimari şöyle görünür:

![Veri toplama işlem hattı mimarisi](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Bu makalede, verilerin nasıl oluşturulacağı veya [bir Azure Blob depolama hesabına nasıl yüklenebileceğiniz](../../storage/blobs/storage-upload-process-images.md)ele alınacaktır. Bunun yerine, bloba yeni bir dosya yüklendikten hemen sonra akışı seçer. Buradan:

1. Bir işlem, yeni verilerin karşıya yüklendiğini algılar.  Örneğimiz bir blob 'a karşıya yüklenen yeni verileri algılamaya yönelik bir tetikleyici sunan [Azure Logic App](../../logic-apps/logic-apps-overview.md)kullanır.

2. Bir işlemci bu yeni verileri okur ve bunu JSON 'a dönüştürür, bu örnekte Azure Izleyici için gereken biçim, işleme kodumuzu yürütmenin hafif ve ekonomik bir yolu olarak bir [Azure işlevi](../../azure-functions/functions-overview.md) kullanıyoruz. İşlev, yeni verileri algılamak için kullandığımız aynı Logic App tarafından açılır.

3. Son olarak, JSON nesnesi kullanılabilir olduğunda Azure Izleyici 'ye gönderilir. Aynı mantıksal uygulama, yerleşik Log Analytics veri toplayıcı etkinliğini kullanarak verileri Azure Izleyici 'ye gönderir.

BLOB depolama, mantıksal uygulama veya Azure Işlevinin ayrıntılı kurulumu bu makalede özetlendiği sırada, belirli ürünlerin sayfalarında ayrıntılı yönergeler sunulmaktadır.

Bu işlem hattını izlemek için, Azure Işlevi [ayrıntılarımızı](../../azure-functions/functions-monitoring.md)burada izlemek üzere Application Insights kullanırız ve mantıksal uygulama [ayrıntılarımızı burada](../../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)izlemek için Azure izleyici 'yi kullanırız. 

## <a name="setting-up-the-pipeline"></a>İşlem hattını ayarlama
İşlem hattını ayarlamak için, önce blob Kapsayıcınızın oluşturulmuş ve yapılandırılmış olduğundan emin olun. Benzer şekilde, verileri göndermek istediğiniz Log Analytics çalışma alanının oluşturulduğundan emin olun.

## <a name="ingesting-json-data"></a>JSON verilerini geri ödeme
JSON verilerinin alınması Logic Apps ve herhangi bir dönüştürme gerçekleşmek zorunda olmadığından, tüm işlem hattının tek bir mantıksal uygulamada büyük bir bir uygulama olabilir. Hem blob kapsayıcısı hem de Log Analytics çalışma alanı yapılandırıldıktan sonra, yeni bir mantıksal uygulama oluşturun ve bunu aşağıdaki şekilde yapılandırın:

![Logic Apps iş akışı örneği](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Mantıksal uygulamanızı kaydedin ve test edin.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>XML, CSV veya diğer veri biçimlerini geri ödeme
Logic Apps bugün, XML, CSV veya diğer türleri JSON biçimine kolayca dönüştürmek için yerleşik yetenekler içermez. Bu nedenle, bu dönüştürmeyi tamamlamaya yönelik başka bir yol da kullanmanız gerekir. Bu makalede, Azure Işlevlerinin sunucusuz işlem yeteneklerini, bunu yapmanın çok basit ve uygun maliyetli bir yolu olarak kullanırız. 

Bu örnekte, bir CSV dosyası ayrıştırıyoruz, ancak diğer dosya türleri benzer şekilde işlenebilir. Belirli veri türü için doğru mantığı yansıtmak üzere Azure Işlevinin seri durumdan çıkarma kısmını değiştirmeniz yeterlidir.

1.  İstendiğinde, çalışma zamanı v1 ve tüketim tabanlı Işlevleri kullanarak yeni bir Azure Işlevi oluşturun.  Sizin için gerekli olan bağlamaları yapılandıran bir C# başlangıç noktası olarak hedeflenen **http tetikleyici** şablonunu seçin. 
2.  Sağ bölmedeki **dosyaları görüntüle** sekmesinde, **Project. JSON** adlı yeni bir dosya oluşturun ve kullanmakta olduğumuz NuGet paketlerinden aşağıdaki kodu yapıştırın:

    ![Azure Işlevleri örnek projesi](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. Sağ bölmeden **. CSX komutunu çalıştırın** ve varsayılan kodu aşağıdaki kodla değiştirin. 

    >[!NOTE]
    >Projeniz için, kayıt modelini ("PopulationRecord" sınıfı) kendi veri şemanız ile değiştirmeniz gerekir.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. İşlevinizi kaydedin.
5. Kodun doğru çalıştığından emin olmak için işlevi test edin. Testi aşağıdaki şekilde yapılandırarak sağ bölmedeki **Test** sekmesine geçin. **İstek gövdesi** metin kutusuna örnek verilerle bir Blobun bağlantı koyun. **Çalıştır**' a tıkladıktan sonra **çıktı** kutusunda JSON çıkışını görmeniz gerekir:

    ![İşlev uygulamaları test kodu](./media/create-pipeline-datacollector-api/functions-test-01.png)

Şimdi geri dönmemiz ve daha önce oluşturmaya başladığımız mantıksal uygulamayı değiştirmemiz gerekir.  Görünüm Tasarımcısı 'nı kullanarak aşağıdaki şekilde yapılandırın ve mantıksal uygulamanızı kaydedin:

![Logic Apps iş akışı tamamlanma örneği](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>İşlem hattını test etme
Şimdi, daha önce yapılandırılan blob 'a yeni bir dosya yükleyebilir ve mantıksal uygulamanız tarafından izlenmesini sağlayabilirsiniz. Kısa süre içinde, mantıksal uygulama başlatma 'nın yeni bir örneğini görmeniz, Azure işlevinizde çağrı yapmanız ve sonra verileri Azure Izleyici 'ye başarıyla göndermeniz gerekir. 

>[!NOTE]
>Verilerin Azure Izleyici 'de ilk kez yeni bir veri türü gönderilişinde görünmesi 30 dakika sürebilir.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Log Analytics ve Application Insights diğer verilerle ilişkilendirme
Özel veri kaynağınızdan alınan popülasyon verileriyle Application Insights sayfa görünümü verilerini içeren bir şeyi gerçekleştirmek için, Application Insights analiz pencereniz veya Log Analytics çalışma alanından aşağıdaki sorguyu çalıştırın:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

Çıktıda, artık katılmış iki veri kaynağı gösterilmelidir.  

![Bir arama sonucu örneğinde bağlantılı verileri ilişkilendirme](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Bir üretim işlem hattı için önerilen geliştirmeler
Bu makalede, doğru bir üretim kalitesi çözümüne uygulanabilecek mantıksal bir örnek olan çalışan bir prototip sunuluyordu. Bu tür bir üretim kalitesi çözümü için aşağıdaki iyileştirmeler önerilir:

* Mantıksal uygulamanızda ve Işlevinizde hata işleme ve yeniden deneme mantığı ekleyin.
* 30MB/tek Log Analytics alma API çağrısı sınırının aşılmadığından emin olmak için mantık ekleyin. Gerekirse verileri daha küçük parçalara ayırın.
* Blob depolamada bir temizleme İlkesi ayarlayın. Log Analytics çalışma alanına başarıyla gönderildikten sonra ham verilerin arşivleme amacıyla kullanılabilmesini sağlamak istemiyorsanız, depolamayı depolamaya devam etmek zorunda kalmadığınız durumlar yoktur. 
* Tam işlem hattında izlemenin etkin olduğunu doğrulayın, izleme noktaları ve uyarıları uygun şekilde ekleyin.
* İşlevinizin ve mantıksal uygulamanızın kodunu yönetmek için kaynak denetiminden yararlanın.
* Şema değişirse doğru değişiklik yönetimi ilkesinin izlendiğinden emin olun, örneğin, işlev ve Logic Apps buna uygun şekilde değiştirilir.
* Birden çok farklı veri türü yüklüyorsanız, bunları blob kabınızda ayrı klasörlere ayırır ve veri türüne göre mantığı denemek için mantık oluşturun. 


## <a name="next-steps"></a>Sonraki adımlar
Veri [Toplayıcı API 'si](data-collector-api.md) hakkında daha fazla bilgi edinmek için REST API istemcisinden Log Analytics çalışma alanına veri yazma.
