---
title: Azure IoT Hub 'ı kopyalama
description: Azure IoT Hub 'ı kopyalama
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: robinsh
ms.openlocfilehash: 4d8771d49f30d94aeb6dfa855f5c2ef107076afb
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083278"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Azure IoT Hub 'ı başka bir bölgeye kopyalama

Bu makalede, bir IoT Hub kopyalamanın yolları incelenir ve başlamadan önce yanıtlamanız gereken bazı sorular sağlanmaktadır. IoT Hub 'ını kopyalamak isteyebileceğiniz birkaç neden şunlardır:
 
* Şirket, Avrupa 'dan Kuzey Amerika (veya tersi) gibi bir bölgeden diğerine taşınıyor ve kaynaklarınızın ve verilerinizin yeni konumunuza coğrafi olarak kapatılmasını istediğinizde, hub 'ınızı taşımanız gerekir.

* Geliştirme ve üretim ortamları için bir merkez ayarınızdan olursunuz.

* Çok merkezli yüksek kullanılabilirlik için özel bir uygulama kullanmak istiyorsunuz. Daha fazla bilgi için, [yüksek kullanılabilirlik ve olağanüstü durum kurtarma IoT Hub yer alan çapraz bölge ha bölümüne](iot-hub-ha-dr.md#achieve-cross-region-ha)bakın.

* Hub 'ınız için yapılandırılan [bölüm](iot-hub-scaling.md#partitions) sayısını artırmak istiyorsunuz. Bu, hub 'ınızı ilk oluşturduğunuzda ayarlanır ve değiştirilemez. Bu makaledeki bilgileri kullanarak, hub 'ınızı kopyalayabilir ve kopya oluşturulduğunda bölüm sayısını artırabilirsiniz.

Bir hub 'ı kopyalamak için özgün hub 'a yönetici erişimi olan bir abonelik gerekir. Yeni hub 'ı yeni bir kaynak grubuna ve bölgesine, özgün hub ile aynı abonelikte veya hatta yeni bir abonelikte koyabilirsiniz. Hub adının genel olarak benzersiz olması gerektiğinden, yalnızca aynı adı kullanamazsınız.

> [!NOTE]
> Şu anda, bir IoT Hub 'ını kopyalamaya yönelik birinci sınıf bir özellik yoktur. Bu aslında el ile gerçekleştirilen bir işlemdir ve bu nedenle oldukça hataya açıktır. Hub 'ın klonlanması karmaşıklığı, hub 'ın karmaşıklığı ile doğrudan orantılıdır. Örneğin, bir IoT Hub 'ını ileti yönlendirme olmadan kopyalamak oldukça basittir. İleti yönlendirmeyi yalnızca bir karmaşıklık olarak eklerseniz, hub 'ın kopyalanması en az bir büyüklük daha karmaşık hale gelir. Yönlendirme uç noktaları için kullanılan kaynakları da taşırsanız, daha karmaşık bir büyütece sırası daha karmaşıktır. 

## <a name="things-to-consider"></a>Dikkate alınması gereken noktalar

IoT Hub 'ını kopyalamadan önce göz önünde bulundurmanız gereken birkaç nokta vardır.

* Özgün konumda bulunan tüm özelliklerin yeni konumda de kullanılabildiğinden emin olun. Bazı hizmetler önizlemededir ve tüm özellikler her yerde kullanılamaz.

* Klonlanan sürümü oluşturmadan ve doğrulamadan önce orijinal kaynakları kaldırmayın. Bir hub 'ı kaldırdığınızda, bu, sonsuza kadar geçmiş olur ve hub 'ın doğru şekilde çoğaltıldığından emin olmak için ayarları veya verileri denetlemek üzere kurtarmanın bir yolu yoktur.

* Birçok kaynak genel benzersiz adlar gerektirir, bu nedenle klonlanan sürümler için farklı adlar kullanmanız gerekir. Ayrıca, kopyalanmış hub 'ın ait olduğu kaynak grubu için farklı bir ad kullanmanız gerekir. 

* Özgün IoT Hub 'ı için veriler geçirilmez. Buna telemetri iletileri, buluttan cihaza (C2D) komutları ve zamanlamalar ve geçmiş gibi işle ilgili bilgiler dahildir. Ölçümler ve günlüğe kaydetme sonuçları da geçirilmez. 

* Azure depolama 'ya yönlendirilen veriler veya iletilerde, verileri özgün depolama hesabında bırakabilir, bu verileri yeni bölgedeki yeni bir depolama hesabına aktarabilir veya eski verileri yerinde bırakabilir ve yeni veriler için yeni konumda yeni bir depolama hesabı oluşturabilirsiniz. Blob depolamada verileri taşıma hakkında daha fazla bilgi için bkz. [AzCopy ile çalışmaya başlama](../storage/common/storage-use-azcopy-v10.md).

* Event Hubs ve Service Bus konular ve kuyruklar için veriler geçirilemez. Bu, zaman içinde olan veriler ve iletiler işlendikten sonra depolanmaz.

* Geçiş için kapalı kalma süresi zamanlamanız gerekir. Cihazların yeni hub 'a kopyalanması zaman alır. Içeri/dışarı aktarma yöntemini kullanıyorsanız, kıyaslama testi, 500.000 cihazlarını taşımak için iki saat boyunca bir milyon cihazı taşımak için dört saat sürebilir. 

* Cihazları kapatmak veya değiştirmek zorunda kalmadan cihazları yeni hub 'a kopyalayabilirsiniz. 

    * Cihazların ilk olarak DPS kullanılarak sağlanması durumunda, yeniden sağlanması her cihazda depolanan bağlantı bilgilerini güncelleştirir. 
    
    * Aksi takdirde, cihazları taşımak için Içeri/dışarı aktarma yöntemini kullanmanız gerekir ve ardından cihazların yeni hub 'ı kullanacak şekilde değiştirilmesi gerekir. Örneğin, ikizi istenen özelliklerden IoT Hub ana bilgisayar adını kullanmak için cihazınızı ayarlayabilirsiniz. Cihaz bu IoT Hub ana bilgisayar adına sahip olur, cihazın eski hub bağlantısını keser ve yeni bir bağlantı kurar.
    
* Yeni kaynaklarla kullanabilmeniz için kullandığınız sertifikaları güncelleştirmeniz gerekir. Ayrıca, büyük olasılıkla bir DNS tablosunda tanımlı hub 'ınız vardır; bu DNS bilgilerini güncelleştirmeniz gerekir.

## <a name="methodology"></a>Yöntem

Bu, bir IoT Hub 'ını bir bölgeden diğerine taşımak için önerdiğimiz genel yöntemdir. İleti yönlendirme için bu, kaynakların yeni bölgeye taşınmadığını varsayar. Daha fazla bilgi için [Ileti yönlendirme konusundaki bölümüne](#how-to-handle-message-routing)bakın.

   1. Hub 'ı ve ayarlarını bir Kaynak Yöneticisi şablonuna dışarı aktarın. 
   
   1. Şablonda, kopyalanan Hub için adın ve konumun tüm tekrarlamalarını güncelleştirme gibi gerekli değişiklikleri yapın. Şablonda ileti yönlendirme uç noktaları için kullanılan herhangi bir kaynak için, bu kaynak için şablondaki anahtarı güncelleştirin.
   
   1. Şablonu yeni konumdaki yeni bir kaynak grubuna aktarın. Bu, kopyayı oluşturur.

   1. Gerektiğinde hata ayıklayın. 
   
   1. Şablona aktarılmamış bir şey ekleyin. 
   
       Örneğin, tüketici grupları şablona aktarılmaz. Tüketici gruplarını şablona el ile eklemeniz veya hub oluşturulduktan sonra [Azure Portal](https://portal.azure.com) kullanmanız gerekir. [IoT Hub ileti yönlendirmeyi yapılandırmak için bir Azure Resource Manager şablonu kullanma](tutorial-routing-config-message-routing-rm-template.md)makalesindeki bir şablona bir tüketici grubu ekleme örneği vardır.

       [İleti zenginleştirme](iot-hub-message-enrichments-overview.md) , şablona de aktarılmaz. Bunlar yönlendirme iletileriyle birlikte kullanılır ve ileti yönlendirme yapılandırması güncelleştirilirken yeni hub 'da el ile güncelleştirilmeleri gerekecektir.

   1. Cihazları orijinal hub 'ından kopyaya kopyalayın. Bu, [IoT Hub 'ına kayıtlı cihazları yönetme](#managing-the-devices-registered-to-the-iot-hub)bölümünde ele alınmıştır.

## <a name="how-to-handle-message-routing"></a>İleti yönlendirmeyi işleme

Hub 'ınız [özel yönlendirme](iot-hub-devguide-messages-read-custom.md)kullanıyorsa, Hub için şablonu dışarı aktarmak yönlendirme yapılandırmasını içerir, ancak kaynakları içermez. Yönlendirme kaynaklarını yeni konuma taşımayı veya yerinde bırakıp "olduğu gibi" kullanmaya devam etmeyi seçmeniz gerekir. 

Örneğin, bir depolama hesabına (ayrıca Batı ABD) iletileri yönlendiren Batı ABD hub 'ınız olduğunu ve hub 'ı Doğu ABD taşımak istediğinizi varsayalım. Hub 'ı taşıyabilir ve Batı ABD depolama hesabına hala ileti yönlendirmenize olanak sağlayabilir veya hub 'ı taşıyabilir ve ayrıca depolama hesabını taşıyabilirsiniz. Farklı bir bölgedeki bitiş noktası kaynaklarına ileti yönlendirirken küçük bir performans sonucu olabilir.

Yönlendirme uç noktaları için kullanılan kaynakları da taşıyamazsınız, ileti yönlendirmeyi kullanan bir hub 'ı kolayca taşıyabilirsiniz. 

Hub ileti yönlendirme kullanıyorsa iki seçeneğiniz vardır. 

1. Yönlendirme uç noktaları için kullanılan kaynakları yeni konuma taşıyın.

    * Yeni kaynakları, [Azure Portal](https://portal.azure.com) el ile ya da Kaynak Yöneticisi şablonlarının kullanımı ile kendiniz oluşturmanız gerekir. 

    * Bu kaynakları, genel olarak benzersiz adlara sahip oldukları için yeni bir konumda oluşturduğunuz zaman yeniden adlandırmanız gerekir. 
     
    * Yeni hub 'ı oluşturmadan önce, yeni merkez şablonundaki kaynak adlarını ve kaynak anahtarlarını güncelleştirmeniz gerekir. Yeni hub oluşturulduğunda kaynakların mevcut olması gerekir.

1. Yönlendirme uç noktaları için kullanılan kaynakları taşımayın. Bunları "yerinde" kullanın.

   * Şablonu düzenlediğiniz adımda, her bir yönlendirme kaynağı için anahtarları almanız ve yeni hub 'ı oluşturmadan önce bu kaynakları şablona yerleştirmeniz gerekir. 

   * Hub, özgün yönlendirme kaynaklarına başvurmakta ve iletileri yapılandırılmış olarak yönlendirmektedir.

   * Hub ve yönlendirme uç noktası kaynakları aynı konumda olmadığından, küçük bir performans okuması olur.

> [!NOTE]
> Hub 'ınız [ileti iyileştirmeleri](iot-hub-message-enrichments-overview.md)kullanıyorsa, Kaynak Yöneticisi şablonuyla aktarılmadığından, bunları yeni IoT Hub 'ında el ile ayarlamanız gerekir.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Hub 'ı başka bir bölgeye geçirmeye hazırlanma

Bu bölüm, hub 'ı geçirmeye yönelik özel yönergeler sağlar.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Özgün hub 'ı bulun ve bir kaynak şablonuna dışarı aktarın.

1. [Azure portalında](https://portal.azure.com) oturum açın. 

1. **Kaynak grupları** ' na gidin ve taşımak istediğiniz hub 'ı içeren kaynak grubunu seçin. Ayrıca **kaynaklar** ' a gidebilir ve bu şekilde hub 'ı bulabilirsiniz. Hub 'ı seçin.

1. Hub için özellikler ve ayarlar listesinden **şablonu dışarı aktar** ' ı seçin. 

   ![IoT Hub şablonu dışarı aktarma komutunu gösteren ekran görüntüsü.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Şablonu indirmek için **İndir** ' i seçin. Dosyayı yeniden bulabileceğiniz bir yere kaydedin. 

   ![IoT Hub şablonu indirme komutunu gösteren ekran görüntüsü.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>Şablonu görüntüleme 

1. Indirmeler klasörüne (veya şablonu aktardığınızda kullandığınız klasöre) gidin ve ZIP dosyasını bulun. ZIP dosyasını açın ve `template.json`adlı dosyayı bulun. Bunu seçin, sonra şablonu kopyalamak için CTRL + C ' yi seçin. ZIP dosyasında olmayan farklı bir klasöre gidin ve dosyayı (Ctrl + V) yapıştırın. Artık düzenleme yapabilirsiniz.
 
    Aşağıdaki örnek, Yönlendirme yapılandırması olmayan bir genel hub içindir. **Westus**bölgesinde **ContosoTestHub29358** adlı bir S1 katman hub 'ı (1 birim ile). Aşağıda, aktarılmış şablon verilmiştir.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>Şablonu düzenleme 

Yeni bölgede yeni hub oluşturmak için şablonu kullanabilmeniz için önce bazı değişiklikler yapmanız gerekir. Şablonu düzenlemek için [vs Code](https://code.visualstudio.com) veya metin düzenleyicisi kullanın.

#### <a name="edit-the-hub-name-and-location"></a>Hub adını ve konumunu düzenleme

1. En üstteki parametreler bölümünü kaldırın; birden çok parametreye sahip olduğumuz için hub adının kullanılması çok daha basittir. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Adı bir parametreden (önceki adımda kaldırdığınız) almak yerine gerçek (yeni) adı kullanacak şekilde değiştirin. 

    Yeni Hub için, yeni adı oluşturmak için özgün hub 'ın ve dize *kopyasının* adını kullanın. Hub adını ve konumunu temizleyip başlatın.
    
    Eski sürüm:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    Yeni sürüm: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    Daha sonra, **yol** değerlerinin eski hub adını içerdiğini göreceksiniz. Yeni birini kullanacak şekilde değiştirin. Bunlar, **olay** ve **Operationsmonitoringevents**adlı **eventhubendpoints** altındaki yol değerleridir.

    İşiniz bittiğinde, Olay Hub 'ı uç noktaları bölümleriniz şöyle görünmelidir:

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>Taşınmakta olmayan yönlendirme kaynakları için anahtarları güncelleştirme

Yönlendirme yapılandırılmış bir hub için Kaynak Yöneticisi şablonu dışarı aktardığınızda, bu kaynaklar için anahtarların dışarı aktarılmış şablonda sağlandığını görürsünüz; bunların yerleşimi yıldız işaretiyle gösterilir. Portalda bu kaynaklara giderek ve yeni hub 'ın şablonunu içeri aktarmadan ve hub 'ı oluşturmadan **önce** anahtarları alarak doldurmanız gerekir. 

1. Yönlendirme kaynaklarından herhangi biri için gereken anahtarları alın ve şablona koyun. [Azure Portal](https://portal.azure.com), anahtarı kaynak dosyadan alabilirsiniz. 

   Örneğin, iletileri bir depolama kapsayıcısına yönlendirçalışıyorsanız, portalda depolama hesabını bulun. Ayarlar bölümünde **erişim anahtarları**' nı seçin, sonra anahtarlardan birini kopyalayın. Bu, şablonu ilk dışa aktardığınızda anahtar şöyle görünür:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Depolama hesabı için hesap anahtarını aldıktan sonra, yıldız işareti yerine yan tümce `AccountKey=****` şablona koyun. 

1. Service Bus kuyrukları için, SharedAccessKeyName ile eşleşen paylaşılan erişim anahtarını alın. JSON içindeki anahtar ve `SharedAccessKeyName` aşağıda verilmiştir:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Aynı Service Bus konular ve Olay Hub 'ı bağlantıları için de geçerlidir.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Yeni konumda yeni yönlendirme kaynaklarını oluşturun

Bu bölüm yalnızca merkez tarafından yönlendirme uç noktaları için kullanılan kaynakları taşıyorsanız geçerlidir.

Yönlendirme kaynaklarını taşımak istiyorsanız, yeni konumdaki kaynakları el ile ayarlamanız gerekir. [Azure Portal](https://portal.azure.com)kullanarak veya ileti yönlendirme tarafından kullanılan her bir kaynak için Kaynak Yöneticisi şablonunu dışarı aktararak, bunları düzenleyerek ve içeri aktararak yönlendirme kaynaklarını oluşturabilirsiniz. Kaynaklar ayarlandıktan sonra, hub 'ın şablonunu (yönlendirme yapılandırmasını içerir) içeri aktarabilirsiniz.

1. Yönlendirme tarafından kullanılan her kaynağı oluşturun. Bunu [Azure Portal](https://portal.azure.com)kullanarak el ile yapabilir veya Kaynak Yöneticisi şablonlarını kullanarak kaynakları oluşturabilirsiniz. Şablonları kullanmak istiyorsanız, aşağıdaki adımları izlemeniz gerekir:

    1. Yönlendirme tarafından kullanılan her kaynak için Kaynak Yöneticisi şablona dışarı aktarın.
    
    1. Kaynağın adını ve konumunu güncelleştirin. 

    1. Kaynaklar arasındaki çapraz başvuruları güncelleştirin. Örneğin, yeni bir depolama hesabı için şablon oluşturursanız, bu şablondaki depolama hesabı adını ve ona başvuran diğer tüm şablonları güncelleştirmeniz gerekir. Çoğu durumda, Hub için şablondaki yönlendirme bölümü, kaynağa başvuran tek diğer şablondur. 

    1. Her bir kaynağı dağıtan her bir şablonu içeri aktarın.

    Yönlendirme tarafından kullanılan kaynaklar ayarlanır ve çalışır duruma getirildikten sonra devam edebilirsiniz.

1. IoT Hub 'ın şablonunda, her bir yönlendirme kaynağı adını yeni adıyla değiştirin ve gerekirse konumu güncelleştirin. 

Artık, yönlendirmeyi işlemeye nasıl karar verdiğinize bağlı olarak, eski hub gibi neredeyse tam olarak görünen yeni bir hub oluşturacak bir şablonunuz vardır.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Taşı--şablonu yükleyerek yeni bölgede yeni hub oluşturma

Yeni bir yerde şablonu kullanarak yeni hub 'ı oluşturun. Taşıyacağınız yönlendirme kaynaklarınız varsa, kaynakların yeni konumda ayarlanması ve şablondaki başvuruların eşleşecek şekilde güncelleştirilmiş olması gerekir. Yönlendirme kaynaklarını taşıdıysanız, güncelleştirilmiş anahtarlarla şablonda olmaları gerekir.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Seçin **kaynak Oluştur**. 

1. Arama kutusuna "şablon dağıtımı" koyun ve ENTER ' u seçin.

1. **Şablon dağıtımı seçin (özel şablonlar kullanarak dağıtın)** . Bu, sizi Şablon dağıtımı bir ekrana götürür. **Oluştur**'u seçin. Şu ekranı görürsünüz:

   ![Kendi şablonunuzu oluşturma komutunu gösteren ekran görüntüsü](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. Bir dosyadan şablonunuzu karşıya yüklemenizi sağlayan **düzenleyicide kendi şablonunuzu oluşturun**' i seçin. 

1. **Dosya Yükle**' yi seçin. 

   ![Şablon dosyası yükleme komutunu gösteren ekran görüntüsü](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Düzenlediğiniz yeni şablona gözatıp seçin ve sonra **Aç**' ı seçin. Şablonu düzenleme penceresinde yükler. **Kaydet**’i seçin. 

   ![Şablonu yüklemeyi gösteren ekran görüntüsü](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Aşağıdaki alanları doldurun.

   **Abonelik**: kullanılacak aboneliği seçin.

   **Kaynak grubu**: yeni bir konumda yeni bir kaynak grubu oluşturun. Önceden ayarlanmış yeni bir tane varsa, yeni bir tane oluşturmak yerine bu seçeneği belirleyebilirsiniz.

   **Konum**: var olan bir kaynak grubunu seçtiyseniz, bu, kaynak grubunun konumuyla eşleşmesi için doldurulur. Yeni bir kaynak grubu oluşturduysanız bu, konumu olacaktır.

   **Kabul ediyorum onay kutusu**: Bu, temelde oluşturmakta olduğunuz kaynaklar için ödeme yapmayı kabul ettiğinizi belirtir.

1. **Satın al** düğmesini seçin.

Portal şimdi şablonunuzu doğrular ve kopyalanmış hub 'ınızı dağıtır. Yönlendirme yapılandırma verileriniz varsa, bu, yeni hub 'a dahil edilir, ancak önceki konumdaki kaynaklara işaret eder.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>IoT Hub 'ına kayıtlı cihazları yönetme

Kopya oluşturup çalıştırdığınıza göre, tüm cihazları özgün hub 'ından kopyaya kopyalamanız gerekir. 

Bunu yapmanın birden çok yolu vardır. Cihazları sağlamak için başlangıçta [cihaz sağlama hizmeti 'ni (DPS)](/azure/iot-dps/about-iot-dps)kullandınız veya hiç yapmadıysanız. Yapmadıysanız, bu zor değildir. Yapmadıysanız, bu çok karmaşık olabilir. 

Cihazlarınızı sağlamak için DPS kullanmıyorsanız, sonraki bölümü atlayabilir ve [içeri/dışarı aktarma kullanarak cihazları yeni hub 'a taşıyabilirsiniz](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Yeni hub 'da cihazları yeniden sağlamak için DPS kullanma

Cihazları yeni konuma taşımak için DPS 'yi kullanmak için bkz. [cihazları yeniden sağlama](../iot-dps/how-to-reprovision.md). İşiniz bittiğinde, [Azure Portal](https://portal.azure.com) cihazları görüntüleyebilir ve bunların yeni konumda olduğunu doğrulayabilirsiniz.

[Azure Portal](https://portal.azure.com)kullanarak yeni hub 'a gidin. Hub 'ınızı seçip **IoT cihazları**' nı seçin. Kopyalanmış hub 'a yeniden sağlanmış olan cihazları görürsünüz. Klonlanan hub 'ın özelliklerini de görüntüleyebilirsiniz. 

Yönlendirme uyguladıysanız, test edin ve iletilerinizin kaynaklara doğru şekilde yönlendirildiğinden emin olun.

### <a name="committing-the-changes-after-using-dps"></a>DPS kullanarak değişiklikleri kaydetme

Bu değişiklik, DPS hizmeti tarafından kaydedildi.

### <a name="rolling-back-the-changes-after-using-dps"></a>DPS kullanarak değişiklikler geri alınıyor. 

Değişiklikleri geri almak istiyorsanız yeni hub 'dan eski bir cihazdan cihazları yeniden sağlayın.

Artık hub 'ınızı ve cihazlarını geçirmeyi tamamladınız. [Temizleme](#clean-up)işlemini atlayabilirsiniz.

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Içeri aktarma kullanarak cihazları yeni hub 'a taşıyın

Uygulama .NET Core 'u hedeflediğinden Windows veya Linux üzerinde çalıştırabilirsiniz. Örneği indirebilir, bağlantı dizelerinizi alabilir, çalıştırmak istediğiniz bitlerin bayraklarını ayarlayabilir ve çalıştırabilirsiniz. Kodu açmadan bunu yapabilirsiniz.

### <a name="downloading-the-sample"></a>Örnek indiriliyor

1. Bu sayfadaki IoT C# örneklerini kullanın: [Için C#Azure IoT örnekleri ](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). ZIP dosyasını indirin ve bilgisayarınıza ayıklayın. 

1. İlgili kod./IoT-Hub/Samples/Service/ımportexportdevicessample. Uygulamayı çalıştırmak için kodu görüntülemeniz veya düzenlemeniz gerekmez.

1. Uygulamayı çalıştırmak için üç bağlantı dizesi ve beş seçenek belirtin. Bu verileri komut satırı bağımsız değişkenleri olarak iletir veya ortam değişkenlerini kullanabilir ya da ikisinin birleşimini kullanabilirsiniz. İçindeki seçenekleri komut satırı bağımsız değişkenleri olarak geçiyoruz ve bağlantı dizeleri ortam değişkenleri olarak geçeceğiz. 

   Bunun nedeni, bağlantı dizelerinin uzun ve ungasız ve değişmememiş olmasından kaynaklanır, ancak seçenekleri değiştirmek ve uygulamayı birden çok kez çalıştırmak isteyebilirsiniz. Bir ortam değişkeninin değerini değiştirmek için, kullandığınız herhangi bir komut penceresini ve Visual Studio veya VS Code kapatmanız gerekir. 

### <a name="options"></a>Seçenekler

Uygulamayı çalıştırdığınızda belirttiğiniz beş seçenek aşağıda verilmiştir. Bunları komut satırına bir dakika içinde koyacağız.

*   **Adddevices** (bağımsız değişken 1)--sizin için oluşturulan sanal cihazları eklemek istiyorsanız bunu true olarak ayarlayın. Bunlar kaynak hub 'ına eklenir. Ayrıca, bir kaç cihaz eklemek istediğinizi belirtmek için **Numtoadd** (bağımsız değişken 2) seçeneğini belirleyin. Bir hub 'a kaydolabilmeniz için en fazla cihaz sayısı 1.000.000 ' dir. Bu seçeneğin amacı test amaçlıdır; belirli sayıda cihaz oluşturup bunları başka bir hub 'a kopyalayabilirsiniz.

*   **copydevices** (bağımsız değişken 3)--cihazları bir hub 'dan diğerine kopyalamak için bunu true olarak ayarlayın. 

*   **Deletesourcedevices** (bağımsız değişken 4)--kaynak hub 'ına kayıtlı tüm cihazları silmek için bunu true olarak ayarlayın. Bunu çalıştırmadan önce tüm cihazların aktarıldığından emin olmanız önerilir. Cihazları sildikten sonra geri alamazsınız.

*   **Deletedestdevices** (bağımsız değişken 5)--hedef hub 'a (kopya) kayıtlı tüm cihazları silmek için bunu true olarak ayarlayın. Cihazları birden çok kez kopyalamak istiyorsanız bunu yapmak isteyebilirsiniz. 

Temel komut *DotNet çalıştırması* olur; bu, .net 'in yerel csproj dosyasını oluşturmasını ve sonra çalıştırmasını söyler. Komut satırı bağımsız değişkenlerinizi çalıştırmadan önce sonuna eklersiniz. 

Komut satırlarınız şu örneklere benzer şekilde görünür:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Bağlantı dizeleri için ortam değişkenlerini kullanma

1. Örneği çalıştırmak için, eski ve yeni IoT Hub 'larına bağlantı dizelerine ve geçici iş dosyaları için kullanabileceğiniz bir depolama hesabına ihtiyacınız vardır. Bunların değerlerini ortam değişkenlerinde depolayacağız.

1. Bağlantı dizesi değerlerini almak için [Azure Portal](https://portal.azure.com)oturum açın. 

1. Not Defteri gibi, bağlantı dizelerini almak için bir yere yerleştirin. Aşağıdakileri kopyalarsanız, bağlantı dizelerini doğrudan gittikleri yere yapıştırabilirsiniz. Eşittir işaretinin etrafına boşluk eklemeyin veya değişken adını değiştirir. Ayrıca, bağlantı dizeleri etrafında çift tırnak işareti gerekmez. Depolama hesabı bağlantı dizesinin etrafına tırnak işareti koyarsanız, bu çalışmaz.

   Windows için, ortam değişkenlerini nasıl ayarlayabilmeniz gerekir:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Linux için ortam değişkenlerini nasıl tanımlayacaksınız:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. IoT Hub bağlantı dizeleri için portalda her bir hub 'a gidin. Hub için **kaynaklarda** arama yapabilirsiniz. Kaynak grubunu biliyorsanız, **kaynak grupları**' na gidebilir, kaynak grubunuzu seçebilir ve ardından bu kaynak grubundaki varlıklar listesinden hub 'ı seçebilirsiniz. 

1. Hub ayarlarından **paylaşılan erişim ilkeleri** ' ni seçin, ardından **iothubowner** ' ı seçin ve bağlantı dizelerinden birini kopyalayın. Hedef hub için de aynısını yapın. Bunları uygun SET komutlarına ekleyin.

1. Depolama hesabı bağlantı dizesi için, **kaynaklar** bölümünde veya **kaynak grubunda** depolama hesabını bulun ve açın. 
   
1. Ayarlar bölümünde **erişim anahtarları** ' nı seçin ve bağlantı dizelerinden birini kopyalayın. Uygun SET komutu için bağlantı dizesini metin dosyanıza koyun. 

Artık SET komutlarıyla bir dosyada ortam değişkenlerine sahipsiniz ve komut satırı bağımsız değişkenlerinizin ne olduğunu biliyoruz. Örneği çalıştıralım.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Örnek uygulamayı çalıştırma ve komut satırı bağımsız değişkenlerini kullanma

1. Bir komut istemi penceresi açın. Windows ' u seçin ve komut istemi penceresini almak için `command prompt` yazın.

1. Ortam değişkenlerini ayarlanmış komutları tek seferde kopyalayın ve komut istemi penceresine yapıştırın ve ENTER ' u seçin. İşiniz bittiğinde, ortam değişkenlerinizi ve bunların değerlerini görmek için komut istemi penceresine `SET` yazın. Bunları komut istemi penceresine kopyaladıktan sonra, yeni bir komut istemi penceresi açmadığınız müddetçe bunları yeniden kopyalamanız gerekmez.

1. Komut istemi penceresinde, ' ın./ımportexportdevicessample (ımportexportdevicessample. csproj dosyasının bulunduğu yer). Ardından aşağıdaki komutu yazın ve komut satırı bağımsız değişkenlerinizi ekleyin.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    DotNet komutu uygulamayı oluşturur ve çalıştırır. Uygulamayı çalıştırdığınızda seçenekleri geçirtiğinden, uygulamayı her çalıştırdığınızda bunların değerlerini değiştirebilirsiniz... Örneğin, bunu bir kez çalıştırmak ve yeni cihaz oluşturmak, sonra yeniden çalıştırmak ve bu cihazları yeni bir hub 'a kopyalamak ve bu şekilde devam etmek isteyebilirsiniz. Aynı çalıştırmada tüm adımları da gerçekleştirebilirsiniz, ancak kopyalama ile işiniz bitene kadar herhangi bir cihazı silmeyerek de yapmanız önerilir. Burada 1000 cihaz oluşturan ve daha sonra diğer hub 'a kopyalayan bir örnek verilmiştir.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Cihazların başarıyla kopyalandığını doğruladıktan sonra, cihazları kaynak hub 'ından şu şekilde kaldırabilirsiniz:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Visual Studio kullanarak örnek uygulamayı çalıştırma

1. Uygulamayı Visual Studio 'da çalıştırmak istiyorsanız, geçerli dizininizi ıothubservicesamples. sln dosyasının bulunduğu klasör olarak değiştirin. Ardından, Visual Studio 'da çözümü açmak için komut istemi penceresinde bu komutu çalıştırın. Bunu, ortam değişkenlerini ayarladığınız aynı komut penceresinde yapmanız gerekir, bu nedenle bu değişkenler bilinmektedir.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. *Importexportdevicessample* projesine sağ tıklayın ve **Başlangıç projesi olarak ayarla**' yı seçin.    
    
1. Beş seçenek için ımportexportdevicessample klasöründeki Program.cs en üstünde bulunan değişkenleri ayarlayın.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Uygulamayı çalıştırmak için F5 ' i seçin. Çalışmayı bitirdikten sonra sonuçları görüntüleyebilirsiniz.

### <a name="view-the-results"></a>Sonuçları görüntüleme 

[Azure Portal](https://portal.azure.com) cihazları görüntüleyebilir ve bunların yeni konumda olduğunu doğrulayabilirsiniz.

1. [Azure Portal](https://portal.azure.com)kullanarak yeni hub 'a gidin. Hub 'ınızı seçip **IoT cihazları**' nı seçin. Eski hub 'dan kopyalanmış hub 'a kopyaladığınız cihazları görürsünüz. Klonlanan hub 'ın özelliklerini de görüntüleyebilirsiniz. 

1. [Azure Portal](https://portal.azure.com) Azure Storage hesabına gidip `ImportErrors.log`için `devicefiles` kapsayıcısına bakarak içeri/dışarı aktarma hatalarını kontrol edin. Bu dosya boşsa (boyut 0 ise), hata yoktu. Aynı cihazı birden çok kez içeri aktarmaya çalışırsanız, cihazı ikinci kez reddeder ve günlük dosyasına bir hata iletisi ekler.

### <a name="committing-the-changes"></a>Değişiklikler yürütülüyor 

Bu noktada, hub 'ınızı yeni konuma kopyaladınız ve cihazları yeni kopyaya geçirdiniz. Şimdi, cihazların kopyalanmış hub ile çalışması için değişiklikler yapmanız gerekir.

Değişiklikleri uygulamak için yapmanız gereken adımlar şunlardır: 

* IoT Hub ana bilgisayar adını değiştirmek için her cihazı güncelleştirin IoT Hub ana bilgisayar adını yeni hub 'a işaret edin. Bunu, cihazı ilk kez hazırlandığında kullandığınız yöntemi kullanarak yapmanız gerekir.

* Yeni hub 'a işaret etmek için eski hub 'a başvuran tüm uygulamaları değiştirin.

* İşiniz bittiğinde, yeni merkez çalışır duruma gelmelidir. Eski hub 'da etkin cihaz olmaması ve bağlantısı kesilmiş durumda olmaması gerekir. 

### <a name="rolling-back-the-changes"></a>Değişiklikler geri alınıyor

Değişiklikleri geri alma kararı verirseniz, aşağıdaki adımları uygulayın:

* IoT Hub ana bilgisayar adını değiştirerek, eski Hub için IoT Hub ana bilgisayar adını gösterecek şekilde her cihazı güncelleştirin. Bunu, cihazı ilk kez hazırlandığında kullandığınız yöntemi kullanarak yapmanız gerekir.

* Eski hub 'a işaret etmek için yeni hub 'a başvuran tüm uygulamaları değiştirin. Örneğin, Azure Analytics kullanıyorsanız, [Azure Stream Analytics girişinizi](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)yeniden yapılandırmanız gerekebilir.

* Yeni hub 'ı silin. 

* Kaynak yönlendirseniz, eski hub üzerindeki yapılandırma yine doğru yönlendirme yapılandırmasını işaret etmelidir ve hub yeniden başlatıldıktan sonra bu kaynaklarla birlikte çalışmalıdır.

### <a name="checking-the-results"></a>Sonuçlar denetleniyor 

Sonuçları denetlemek için, IoT çözümünüzü yeni konumdaki hub 'ınıza işaret etmek üzere değiştirin ve çalıştırın. Diğer bir deyişle, önceki hub ile gerçekleştirdiğiniz yeni hub ile aynı eylemleri gerçekleştirin ve doğru çalıştıklarından emin olun. 

Yönlendirme uyguladıysanız, test edin ve iletilerinizin kaynaklara doğru şekilde yönlendirildiğinden emin olun.

## <a name="clean-up"></a>Temizle

Yeni hub 'ın çalışır duruma gelinceye ve cihazların doğru şekilde çalıştığından emin olana kadar temizleyin. Ayrıca, bu özelliği kullanıyorsanız yönlendirmeyi test ettiğinizden emin olun. Hazırsanız, aşağıdaki adımları gerçekleştirerek eski kaynakları temizleyin:

* Henüz yapmadıysanız, eski hub 'ı silin. Bu, tüm etkin cihazları hub 'ından kaldırır.

* Yeni konuma taşıdığınız yönlendirme kaynaklarınız varsa eski yönlendirme kaynaklarını silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Yeni bir bölgedeki bir IoT Hub 'ını yeni bir hub 'a Klonladığınız cihazlarla doldurun. IoT Hub kimlik kayıt defterine yönelik toplu işlemler gerçekleştirme hakkında daha fazla bilgi için bkz. [IoT Hub cihaz kimliklerini toplu olarak içeri ve dışarı aktarma](iot-hub-bulk-identity-mgmt.md).

Hub için IoT Hub ve geliştirme hakkında daha fazla bilgi için lütfen aşağıdaki makalelere bakın.

* [Geliştirici Kılavuzu IoT Hub](iot-hub-devguide.md)

* [IoT Hub yönlendirme öğreticisi](tutorial-routing.md)

* [IoT Hub cihaz yönetimine genel bakış](iot-hub-device-management-overview.md)

* Örnek uygulamayı dağıtmak istiyorsanız lütfen bkz. [.NET Core uygulama dağıtımı](https://docs.microsoft.com/dotnet/core/deploying/index).