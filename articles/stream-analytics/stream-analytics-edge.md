---
title: IoT Edge üzerinde Azure Stream Analytics
description: Edge işleri, Azure Stream Analytics'te oluşturmak ve bunları Azure IOT Edge çalıştıran cihazlara dağıtabilirsiniz.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 07/01/2019
ms.custom: seodec18
ms.openlocfilehash: 8e3b6d0fbefb8e3d3437fd5e24f929e453c573df
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621006"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>IoT Edge üzerinde Azure Stream Analytics
 
IOT Edge üzerinde Azure Stream Analytics (ASA), böylece cihaz tarafından üretilen verilerin tüm değerini açığa çıkarabilirsiniz yakın neredeyse gerçek zamanlı analitik zekayı IOT cihazlarına dağıtmak için geliştiricilerin güçlendirir. Azure Stream Analytics, düşük gecikme süresi, dayanıklılık, bant genişliği ve uyumluluk verimli kullanımı için tasarlanmıştır. Kuruluşlar artık endüstriyel işlemler yakın Denetim mantığı dağıtabilir ve bulutta yapılan büyük veri analizi tamamlar.  

IOT Edge üzerinde Azure Stream Analytics, çalışan içinde [Azure IOT Edge](https://azure.microsoft.com/campaigns/iot-edge/) framework. İçinde ASA işi oluşturulduktan sonra dağıtabilir ve IOT hub'ı kullanarak yönetebilirsiniz.

## <a name="scenarios"></a>Senaryolar
![IOT Edge üst düzey diyagramı](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Düşük gecikme süreli komut ve Denetim**: Örneğin, güvenlik sistemleri üretim işlem verilerine son derece düşük gecikme süresi ile yanıt vermelidir. IOT Edge üzerinde ASA ile algılayıcı verileri neredeyse gerçek zamanlı ve bir makine durdurmak veya uyarıları tetiklemek için anomalileri algılayın, komutları sorunu çözümleyebilirsiniz.
*   **Bulut bağlantısının sınırlı**: Uzaktan araştırma donanım, bağlı tekneler veya yurtdışında incelediğinizde gibi görev açısından kritik sistemleri analiz ve veri için bulut bağlantısı kesintili olduğunda bile react gerekir. ASA, akış mantığınızı ağ bağlantısı bağımsız olarak çalışır ve hangi buluta daha fazla işleme veya depolama için gönderdiğiniz seçebilirsiniz.
* **Sınırlı bant genişliği**: Veri hacmi jet motoru tarafından üretilen ya da bağlı arabalar veri filtre veya gereken ön işlemden buluta göndermeden önce çok büyük olabilir. ASA kullanarak filtreleyin veya buluta gönderilmesi gereken veri toplama.
* **Uyumluluk**: Yasal uyumluluk bazı verileri yerel olarak anonim hale getirilen veya buluta gönderilmeden önce toplanan gerektirebilir.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Azure Stream Analytics Edge işi
### <a name="what-is-an-edge-job"></a>"Edge" işi nedir?

ASA Edge işlerinizi dağıtılan kapsayıcılarında [Azure IOT Edge cihazları](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). İki bölümden oluşan bunlar:
1.  İş tanımı için sorumlu olduğu bir bulut bölümü: kullanıcılar, bulutta giriş, çıkış, sorgu ve diğer ayarları (sıralama dışı olaylar, vb.) tanımlayın.
2.  IOT cihazlarınızda modül. Bu, ASA altyapısı içerir ve iş tanımını buluttan alır. 

ASA, edge işleri aygıtlara dağıtmak için IOT hub'ı kullanır. Hakkında daha fazla bilgi [IOT Edge dağıtımı burada görülebilir](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Azure Stream Analytics Edge işi](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Yükleme yönergeleri
Üst düzey adımlar aşağıdaki tabloda açıklanmıştır. Daha fazla ayrıntı, aşağıdaki bölümlerde verilmiştir.

|      |Adım   | Notlar   |
| ---   | ---   |  ---      |
| 1   | **Bir depolama kapsayıcısı oluşturma**   | Depolama kapsayıcıları, IOT cihazlarınızı, burada erişilebilmelerini iş tanımınızı kaydetmek için kullanılır. <br>  Herhangi bir mevcut depolama kapsayıcısını yeniden kullanabilirsiniz.     |
| 2   | **ASA edge işi oluşturma**   |  Select yeni bir iş oluşturma **Edge** olarak **barındırma ortamı**. <br> Bu işlerin buluttan oluşturulan ve yönetilen ve kendi IOT Edge cihazlarında çalıştırın.     |
| 3   | **IOT Edge ortamınızda aygıtlarınızın Kurulumu**   | Yönergeler için [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) veya [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Aygıtlarınızın IOT Edge üzerinde ASA dağıtma**   |  ASA işi tanımı, daha önce oluşturduğunuz depolama kapsayıcısına dışarı aktarılır.       |

İzleyebileceğiniz [Bu adım adım öğretici](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) ilk IOT Edge üzerinde ASA işiniz dağıtılacak. Aşağıdaki videoda bir Stream Analytics işi bir IOT edge Cihazınızda çalıştırmak üzere işlemlerini anlamanıza yardımcı olması:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Bir depolama kapsayıcısı oluşturma
Bir depolama kapsayıcısı derlenmiş ASA sorgu ve işlem yapılandırmasını dışarı aktarmak için gereklidir. ASA Docker görüntüsü, belirli bir sorgu ile yapılandırmak için kullanılır. 
1. İzleyin [bu yönergeleri](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) Azure portalından bir depolama hesabı oluşturmak için. Tüm varsayılan seçenekleri ile ASA bu hesabı kullanmak için tutabilirsiniz.
2. Yeni oluşturulan depolama hesabında blob depolama kapsayıcısı oluşturun:
    1. Tıklayarak **Blobları**, ardından **+ kapsayıcı**. 
    2. Bir ad girin ve kapsayıcı olarak tutmak **özel**.

#### <a name="create-an-asa-edge-job"></a>ASA Edge işi oluşturma
> [!Note]
> Bu öğreticide Azure portalını kullanarak ASA işi oluşturma odaklanır. Ayrıca [ASA Edge işi oluşturmak için Visual Studio eklentisini kullanma](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. Azure portalında yeni bir "Stream Analytics işi" oluşturun. [Burada yeni bir ASA işi oluşturmak için doğrudan bağlantı](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Oluşturma ekranında seçin **Edge** olarak **barındırma ortamı** (aşağıdaki resme bakın)

   ![Edge üzerinde Stream Analytics işi oluşturma](media/stream-analytics-edge/create-asa-edge-job.png)
3. İş tanımı
    1. **Giriş pencereler tanımlamak**. İşiniz için bir veya birden çok giriş akışları tanımlayın.
    2. (İsteğe bağlı) başvuru verileri tanımlar.
    3. **Çıkış pencereler tanımlamak**. İşiniz için bir veya birden çok çıkış akışları tanımlayın. 
    4. **Sorgu tanımlama**. ASA sorgu değiştirici kullanarak bulutta tanımlayın. Derleyici, ASA edge için etkin söz dizimi otomatik olarak denetler. Örnek verileri karşıya yükleyerek, sorgunuzu test edebilirsiniz. 

4. Depolama kapsayıcısı bilgi kümesinde **IOT Edge ayarları** menüsü.

5. İsteğe bağlı ayarlar
    1. **Olay sıralama**. Portalda, sırası ilkesi yapılandırabilirsiniz. Belgelere [burada](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
    2. **Yerel ayar**. İnternalization biçimini ayarlayın.



> [!Note]
> Bir dağıtım oluşturduğunuzda, ASA bir depolama kapsayıcısına iş tanımını dışa aktarır. Bu iş tanımı aynı kalır bir dağıtım süresi boyunca. Sonuç olarak edge üzerinde çalışan bir iş güncelleştirmek istiyorsanız, ASA işi düzenleyin ve ardından IOT Hub'ında yeni bir dağıtım yaratmanız gerekir.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>IOT Edge ortamınızı aygıtlarınızın ayarlama
Edge işleri, Azure IOT Edge çalıştıran cihazlara dağıtılabilir.
Bunun için bu adımları izlemeniz gerekir:
- IOT Hub oluşturun.
- Docker ve IOT Edge çalışma zamanı edge cihazlarınıza yükleyebilir.
- Cihazlarınızı olarak ayarlamak **IOT Edge cihazları** IOT hub'ında.

IOT Edge belgeleri için bu adımları açıklanan [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) veya [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Aygıtlarınızın IOT Edge üzerinde ASA dağıtım
##### <a name="add-asa-to-your-deployment"></a>ASA dağıtıma ekleme
- Azure portalında, IOT hub'ı açın ve gidin **IOT Edge** ve bu dağıtım için hedeflemek istediğiniz cihaza tıklayın.
- Seçin **modülleri ayarlama**, ardından **+ Ekle** ve **Azure Stream Analytics Modülü**.
- Abonelik ve oluşturduğunuz ASA Edge işi seçin. Kaydet’e tıklayın.
![Dağıtımınızda ASA Modül Ekle](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Bu adım sırasında ASA (Bunu zaten mevcut değilse) depolama kapsayıcısında "EdgeJobs" adlı bir klasör oluşturur. Her bir dağıtım için "EdgeJobs" klasöründe yeni bir alt klasör oluşturulur.
> IOT Edge cihazlarına iş dağıttığınızda, ASA işi tanım dosyasını bir paylaşılan erişim imzası (SAS) oluşturur. SAS anahtarını, cihaz ikizi kullanarak IOT Edge cihazları güvenli bir şekilde aktarılır. Bu anahtarı süre sonu oluşturulduğu günden itibaren üç yıldır. IOT Edge işi güncelleştirdiğinizde, SAS değişir, ancak görüntü sürümü değişmez. Sonra **güncelleştirme**, dağıtım iş akışını izleyin ve cihazda bir güncelleştirme bildirimi günlüğe kaydedilir.


IOT Edge dağıtımları hakkında daha fazla bilgi için bkz [bu sayfayı](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Yolları Yapılandır
IOT Edge modülleri ve IOT hub'ı arasında ve modüller arasında iletileri bildirimli olarak yönlendirmek için bir yol sağlar. Tam söz dizimi açıklanmıştır [burada](https://docs.microsoft.com/azure/iot-edge/module-composition).
Giriş ve çıkışları ASA işi oluşturulan adlarını yönlendirme için uç noktalar olarak kullanılabilir.  

###### <a name="example"></a>Örnek

```json
{
    "routes": {
        "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
        "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream",
        "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")"
    }
}

```
Bu örnek aşağıdaki resimde açıklanan senaryo için yollar gösterir. Edge işi adlı içerdiği "**ASA**", adlı bir giriş ile "**sıcaklık**"ve adlı bir çıktı"**uyarı**".
![İleti yönlendirme diyagramı örneği](media/stream-analytics-edge/edge-message-routing-example.png)

Bu örnek aşağıdaki yolları tanımlar:
- Her ileti **tempSensor** adlı modül gönderilen **ASA** adlı giriş **sıcaklık**,
- Tüm çıkışları **ASA** modülü, bu cihazı (Yukarı Akış$), bağlı IOT hub'ına gönderilir
- Tüm çıkışları **ASA** modülü gönderilen **denetimi** uç noktasını **tempSensor**.


## <a name="technical-information"></a>Teknik bilgiler
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Bulut işlerine kıyasla IOT Edge işleri için geçerli sınırlamalar
Eşlik sahip olmaktır IOT Edge işleri ve bulut işleri arasında. SQL sorgu dil özelliklerinin çoğu hem bulut hem de IOT Edge üzerinde aynı mantığı çalıştırılacak etkinleştirme desteklenir.
Ancak aşağıdaki özellikleri, edge işleri için henüz desteklenmiyor:
* Kullanıcı tanımlı işlevler (UDF) JavaScript içinde. UDF kullanılabilir [ C# IOT Edge işleri için](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (Önizleme).
* Kullanıcı tanımlı toplamlarda (UDA).
* Azure ML işlevleri
* Tek bir adımda 14'ten fazla toplamaları kullanma.
* Giriş/Çıkış AVRO biçimi. Şu anda yalnızca CSV ve JSON desteklenir.
* Aşağıdaki SQL işleçleri:
    * BÖLÜMÜ
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Çalışma zamanı ve donanım gereksinimleri
IOT Edge üzerinde ASA çalıştırmak için çalıştırmak üzere cihazları gerekir [Azure IOT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA ve Azure IOT Edge'i kullanma **Docker** kapsayıcıları (Windows, Linux) birden çok konak işletim sistemlerinde çalışan taşınabilir bir çözüm sağlamak için.

IOT Edge üzerinde ASA, x86 64 veya ARM (Gelişmiş RISC makineler) mimarileri üzerinde çalışan Windows ve Linux görüntüleri olarak kullanılabilir. 


### <a name="input-and-output"></a>Giriş ve çıkış
#### <a name="input-and-output-streams"></a>Giriş ve çıkış akışları
ASA Edge işleri, giriş ve çıkışları, IOT Edge cihaz üzerinde çalışan diğer modüllerden alabilirsiniz. Gelen ve belirli modüller bağlanmak için dağıtım sırasında yönlendirme yapılandırması ayarlayabilirsiniz. Daha fazla bilgi açıklanır [IOT Edge modülü oluşturma belgeleri](https://docs.microsoft.com/azure/iot-edge/module-composition).

Giriş ve çıkışları için CSV ve JSON biçimleri desteklenir.

Her giriş ve çıkış akışına, ASA işi oluşturmak için dağıtılan modülünüzde karşılık gelen bir uç noktası oluşturulur. Bu uç noktaları, dağıtım yolları kullanılabilir.

Akış girişi AT var, yalnızca desteklenen ve Edge hub'ı stream çıktı türleri şunlardır. Başvuru girişi başvuru destekleyen dosya türü. Bulut işi aşağı yönde kullanarak diğer çıkışlar erişilebilir. Örneğin, Edge'de barındırılan bir Stream Analytics işi, daha sonra çıktı IOT Hub'ına gönderebilirsiniz Edge Hub'ına çıktısını gönderir. Power BI veya başka bir çıkış türü için IOT Hub ve çıkış girdilere sahip ikinci bir bulutta barındırılan Azure Stream Analytics işi'ni kullanabilirsiniz.



##### <a name="reference-data"></a>Başvuru verileri
Başvuru verileri (arama tablosu olarak da bilinir) statik veya yavaş doğası gereği değiştirme sınırlı bir veri kümesi var. Bir arama gerçekleştirme ya da, veri akışı ile ilişkilendirmek için kullanılır. Yapmak için Azure Stream Analytics işinizi başvuru verilerinde kullanımı, genel olarak kullanacağınız bir [başvuru veri birleştirme](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) sorgunuzda. Daha fazla bilgi için [kullanarak başvuru verilerini Stream analytics'te aramaları için](stream-analytics-use-reference-data.md).

Yalnızca yerel başvuru verileri desteklenir. IOT Edge cihazı için bir Proje dağıtıldığında, kullanıcı tanımlı dosya yolundan başvuru verileri yükler.

Başvuru verilerinde Edge ile bir iş oluşturmak için:

1. İşiniz için yeni bir giriş oluşturun.

2. Seçin **başvuru verileri** olarak **kaynak türü**.

3. Bir başvuru veri dosyasının hazır cihaza sahip. Bir Windows kapsayıcı için başvuru veri dosyasının yerel bir sürücüye yerleştirin ve yerel diske Docker kapsayıcısı ile paylaşın. Bir Linux kapsayıcı için Docker birim oluşturma ve birim veri dosyasına doldurun.

4. Dosya yolunu ayarlayın. Windows ana bilgisayar işletim sistemi ve Windows kapsayıcı için mutlak yol kullanın: `E:\<PathToFile>\v1.csv`. Bir Windows konak işletim sistemi ve Linux kapsayıcı veya bir Linux işletim sistemi ve Linux kapsayıcı için yol birimin kullanın: `<VolumeName>/file1.txt`.

![IOT Edge üzerinde Azure Stream Analytics işine ilişkin yeni başvuru veri girişi](./media/stream-analytics-edge/Reference-Data-New-Input.png)

IOT Edge güncelleştirme başvuru verilerinde bir dağıtım tarafından tetiklenir. ASA modülü tetiklenen sonra çalışan işlemi durdurmadan güncelleştirilmiş veriler seçer.

Başvuru verileri güncelleştirmek için iki yolu vardır:
* Azure portalından, ASA işi başvuru veri yoluna güncelleştirin.
* IOT Edge dağıtımı güncelleştirin.

## <a name="license-and-third-party-notices"></a>Lisans ve üçüncü taraf bildirimleri
* [IOT Edge üzerinde Azure Stream Analytics, lisans](https://go.microsoft.com/fwlink/?linkid=862827). 
* [IOT Edge üzerinde Azure Stream Analytics için üçüncü taraf bildirimi](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Azure Stream Analytics modülü görüntü bilgileri 

Bu sürüm bilgileri 2019-06-27 en son güncelleştirildiği:

- Görüntü: `asaedge.azurecr.io/public/azure-stream-analytics/azureiotedge:1.0.3-linux-amd64`
   - temel görüntü: microsoft/dotnet:2.1.6-runtime-alpine3.7
   - Platform:
      - Mimari: amd64
      - os: linux
  
- Görüntü: `asaedge.azurecr.io/public/azure-stream-analytics/azureiotedge:1.0.3-linux-arm32v7`
   - temel görüntü: microsoft/dotnet:2.1.6-runtime-bionic-arm32v7
   - Platform:
      - Mimari: arm
      - os: linux
  
- Görüntü: `asaedge.azurecr.io/public/azure-stream-analytics/azureiotedge:1.0.3-windows-amd64`
   - temel görüntü: microsoft/dotnet:2.1.6-runtime-nanoserver-1809
   - Platform:
      - Mimari: amd64
      - işletim sistemi: windows
      
      
## <a name="get-help"></a>Yardım alın
Daha fazla yardım için deneyin [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure IOT Edge hakkında daha fazla bilgi](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [Öğretici IOT Edge üzerinde ASA](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Visual Studio Araçları'nı kullanarak Stream Analytics Edge işlerini geliştirme](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [API'leri kullanarak Stream Analytics için CI/CD uygulayabileceğinizi](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
