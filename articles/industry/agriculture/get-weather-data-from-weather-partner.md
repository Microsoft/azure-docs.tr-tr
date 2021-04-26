---
title: Hava durumu ortaklarından Hava durumu verileri alın
description: Bu makalede, iş ortaklarından Hava durumu verilerinin nasıl alınacağı açıklanır.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: bb28c517e353af6b8c1ee0cad788ff41b971918c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460883"
---
# <a name="get-weather-data-from-weather-partners"></a>Hava durumu ortaklarından Hava durumu verileri alın

Azure Farmker, bir Docker tabanlı bağlayıcı çerçevesi kullanarak hava durumu veri sağlayıcılardan Hava durumu verilerini taşımanıza yardımcı olur. Bu çerçeveyi kullanarak, hava durumu veri sağlayıcıları Farmker 'Lar ile tümleştirilebilen bir Docker uygular. Şu anda, aşağıdaki Hava durumu veri sağlayıcısı desteklenmektedir.

  ![Farmtempts iş ortakları](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)
  
   [DTN](https://www.dtn.com/dtn-content-integration/)

Hava durumu verileri, eyleme dönüştürülebilir içgörüler oluşturmak ve Farmtempts 'de AI ya da ML modelleri oluşturmak için kullanılabilir.

## <a name="before-you-start"></a>Başlamadan önce

Hava durumu verilerini almak için, [Farmtts 'yi yüklediğinizden](./install-azure-farmbeats.md)emin olun. Hava durumu tümleştirmesi, 1.2.11 ve üzeri sürümlerde desteklenir. 

## <a name="enable-weather-integration-with-farmbeats"></a>Farmtempolar ile hava durumu tümleştirmesini etkinleştirme

Farmrets veri hub 'ında Hava durumu verilerini almaya başlamak için:

1. Farmrets veri hub Swagger 'nize gidin `https://farmbeatswebsite-api.azurewebsites.net/swagger` .

2. /Partner API 'sine gidin ve sonra bir POST isteği yapın. Aşağıdaki giriş yükünü kullanın:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Örneğin, DTN 'den Hava durumu verileri almak için aşağıdaki yükü kullanın. Ad ve açıklamayı tercihlerinize göre değiştirebilirsiniz.

   > [!NOTE]
   > Aşağıdaki adım bir API anahtarı gerektirir. DTN aboneliğiniz için bir anahtar almak üzere DTN ile iletişim kurun.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > İş ortağı nesnesi hakkında daha fazla bilgi için bu makaledeki [ek](get-weather-data-from-weather-partner.md#appendix) bölümüne bakın.

   Önceki adımda, Docker 'ın müşterinin Farmker ortamında çalışmasını sağlamak için kaynaklar sağlanacak.  

   Kaynakların sağlanması yaklaşık 10 ila 15 dakika sürer.

3. Önceki adımda oluşturduğunuz/partner nesnesinin durumunu denetleyin. Durumu denetlemek için,/partner API 'sinde bir GET isteği yapın ve iş ortağı nesnesinin durumunu kontrol edin. Farmtts, iş ortağını başarıyla sağlar, durum **etkin** olarak ayarlanır.

4. /JobType API 'sinde, bir GET isteği yapın. Daha önce oluşturduğunuz Hava durumu işlerini iş ortağı ek sürecinde denetleyin. Hava durumu işlerinde **ardışık düzen adı** alanı şu biçimdedir: **iş ortağı-name_partner-type_job-adı**.

      Artık Farmtts örneğinizin etkin bir hava durumu veri ortağı vardır. Belirli bir konum (Enlem ve Boylam) ve bir tarih aralığı için hava durumu verileri istemek üzere işleri çalıştırabilirsiniz. İş türleri, hava durumu işlerini çalıştırmak için gereken parametrelerin ayrıntılarına sahip olacaktır.

      Örneğin, DTN için aşağıdaki iş türleri oluşturulacaktır:
   
      - **get_dtn_daily_observations**: bir konum ve zaman aralığı için günlük gözlemlerini alın.
      - **get_dtn_daily_forecasts**: bir konum ve zaman aralığı için günlük tahminleri alın.
      - **get_dtn_hourly_observations**: bir konum ve zaman aralığı için saatlik gözlemler alın.
      - **get_dtn_hourly_forecasts**: bir konum ve zaman aralığı için saatlik tahminleri alın.

6. İş türlerinin KIMLIĞINI ve parametrelerini bir yere unutmayın.

7. /Jobs API 'sine gidin ve/Job üzerinde bir POST isteği yapın. Aşağıdaki giriş yükünü kullanın:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Örneğin, **get_dtn_daily_observations** çalıştırmak için aşağıdaki yükü kullanın:

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. Önceki adımda, iş ortağı Docker 'da tanımlanan ve hava durumu verilerinde, ham parçalara göre tanımlanan Hava durumu işleri çalıştırılır. /Job üzerinde bir GET isteği yaparak işin durumunu denetleyebilirsiniz. Yanıtta, **CurrentState** için arama yapın. Bitirdiğinizde, **CurrentState** **başarılı** olarak ayarlanır.

## <a name="query-ingested-weather-data"></a>Sorgu alınan hava durumu verileri

Hava durumu işleri tamamlandıktan sonra, Farmınts veri hub 'ı REST API 'Lerini kullanarak modeller veya eyleme dönüştürülebilir içgörüler oluşturmak için alınan hava durumu verilerini sorgulayabilirsiniz.

Bir Farmtts REST API kullanarak hava durumu verilerini sorgulamak için:

1. Farmrets Datahub [Swagger](https://yourdatahub.azurewebsites.net/swagger)' de,/dalgalı Iş datalocation API 'sine gidin ve bir get isteği yapın. Yanıt, işin çalıştırıldığı konum (Enlem ve Boylam) için oluşturulan/dalgalı therdatalocation nesnelerini içerir. Nesnelerin **kimliği** ve **dalgalı veri modukapağını** bir yere dikkat edin.

2. Daha önce yaptığınız gibi **dalgalı veri ModelId** için/dalgalı therdatamodel API 'SINDE bir get/{ID} isteği yapın. Hava durumu veri modeli, alınan hava durumu verileriyle ilgili tüm meta verileri ve ayrıntıları gösterir. Örneğin, hava durumu veri modeli nesnesinde, hava durumu ölçüsü, hava durumu bilgilerinin hangi türlerde ve birimlerde desteklenmekte olduğunu ayrıntılarıyla ayrıntılardır. Örnek:

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Hava durumu veri modeli için GET/{id} çağrısından gelen yanıtı bir yere unutmayın.

3. Telemetri API 'sine gidin ve bir POST isteği yapın. Aşağıdaki giriş yükünü kullanın:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

    Yanıt, belirtilen zaman aralığı için kullanılabilir hava durumu verilerini gösterir:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

Yukarıdaki örnekte, yanıt iki zaman damgalarına ait verileri gösterir. Ayrıca, iki zaman damgalarına bildirilen Hava durumu verilerinin ölçü adını (sıcaklık) ve değerlerini gösterir. Bildirilen değerlerin türünü ve birimini yorumlamak için ilgili hava durumu veri modeli ' ne bakın.

## <a name="troubleshoot-job-failures"></a>İş hatalarında sorun giderme

İş hatalarıyla ilgili sorunları gidermek için [iş günlüklerini denetleyin](troubleshoot-azure-farmbeats.md#weather-data-job-failures).


## <a name="appendix"></a>Ek

|        İş Ortağı   |  Ayrıntılar   |
| ------- | -------             |
|     DockerDetails-GörüntüAdı         |          Docker görüntü adı. Örneğin, docker.io/mydockerimage (görüntü hub.docker.com) veya myazureacr.azurecr.io/mydockerimage (Azure Container Registry resim) ve benzeri. Hiçbir kayıt defteri sağlanmazsa varsayılan değer hub.docker.com ' dir.      |
|          DockerDetails-ImageTag             |         Docker görüntüsünün etiket adı. Varsayılan değer "en son" dır.     |
|  DockerDetails-kimlik bilgileri      |  Özel Docker 'a erişim için kimlik bilgileri. İş ortağı kimlik bilgilerini sağlar.   |
|  DockerDetails-azureBatchVMDetails-batchVMSKU     |    Azure Batch VM SKU 'SU. Daha fazla bilgi için bkz. [tüm kullanılabilir Linux sanal makineleri](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <BR> <BR> Geçerli değerler şunlardır ' küçük ', ' Extrabüyük ', ' Large ', ' A8 ', ' A9 ', ' Medium ', ' a5 ', ' a6 ', ' a7 ', ' STANDARD_D1 ', ' STANDARD_D2 ', ' STANDARD_D3 ', ' STANDARD_D4 ', ' STANDARD_D11 ', ' STANDARD_D12 ', ' STANDARD_D13 ', ' STANDARD_D14 ', ' a10 ', ' a11 ', ' STANDARD_D1_V2 ', ' STANDARD_D2_V2 ', ' STANDARD_D3_V2 ', ' STANDARD_D4_V2 ', ' STANDARD_D11_V2 ', ' STANDARD_D12_V2 ', ' STANDARD_D13_V2 ', ' STANDARD_D14_V2 ', ' STANDARD_G1 ' , ' STANDARD_G5 ', ' STANDARD_D5_V2 ', ' BASIC_A1 ', ' BASIC_A2 ', ' BASIC_A3 ', ' BASIC_A4 ', ' STANDARD_A1 ', ' STANDARD_A2 ', ' STANDARD_A3 ', ' STANDARD_A4 ', ' STANDARD_A5 ', ' STANDARD_A6 ', ' STANDARD_A7 ', ' STANDARD_A8 ', ' STANDARD_A9 ', ' STANDARD_A10 ', ' STANDARD_A11 ', ' STANDARD_D15_V2 ', ' STANDARD_F1 ', ' STANDARD_F2 ', ' STANDARD_F4 ', ' STANDARD_F8 ', ' STANDARD_F16 ', ' STANDARD_NV6 ', ' STANDARD_NV12 ', ' STANDARD_NV24 ', ' STANDARD_NC6 ' , ' STANDARD_H8 ', ' STANDARD_H8m ', ' STANDARD_H16 ', ' STANDARD_H16m ', ' STANDARD_H16mr ', ' STANDARD_H16r ', ' STANDARD_A1_V2 ', ' STANDARD_A2_V2 ', ' STANDARD_A4_V2 ', ' STANDARD_A8_V2 ', ' STANDARD_A2m_V2 ', ' STANDARD_A4m_V2 ', ' STANDARD_A8m_V2 ', ' STANDARD_M64ms ', ' STANDARD_M128s ' ve ' STANDARD_D2_V3 '. *Varsayılan değer ' STANDARD_D2_V2 '.*  |
|    DockerDetails-azureBatchVMDetails ile ayrılmış bilgisayar düğümleri   |  Toplu işlem havuzu başına adanmış bilgisayar düğümü sayısı. Varsayılan değer 1’dir. |
|    DockerDetails-azureBatchVMDetails-nodeAgentSKUID          |    Azure Batch düğüm Aracısı SKU KIMLIĞI. Şu anda yalnızca "Batch. Node. Ubuntu 18,04" Batch düğüm Aracısı destekleniyor.    |
| DockerDetails-partnerCredentials | Docker 'da iş ortağı API 'sini çağırma için kimlik bilgileri. İş ortağı, desteklenen yetkilendirme mekanizmasına bağlı olarak bu bilgileri sağlar; Örneğin, Kullanıcı adı ve parola veya API anahtarları. |
| partnerType | "Hava durumu". Farmtts 'teki diğer iş ortağı türleri "algılayıcı" ve "Imagery" dir.  |
|  name   |   Farmtts sisteminde ortağın istenen adı.   |
|  açıklama |  Açıklama.   |

## <a name="next-steps"></a>Sonraki adımlar

Azure Farmtts örneğinizden sensör verilerini sorguladığınıza göre, gruplar için [haritalar oluşturmayı](generate-maps-in-azure-farmbeats.md#generate-maps) öğrenin.