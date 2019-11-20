---
title: Azure IoT Central yeni bir Azure IoT Edge cihaz türü tanımlayın | Microsoft Docs
description: Bu öğreticide, Azure IoT Central uygulamanızda yeni bir Azure IoT Edge cihaz oluşturma, Oluşturucu olarak gösterilir. Türü için telemetri, durum, özellik ve komutları tanımlarsınız.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c205b4dd4871ed53e32dce72f12cc2dcfb3baf41
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893056"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Öğretici: Azure IoT Central uygulamanızda yeni bir Azure IoT Edge cihaz türü tanımlama (Önizleme özellikleri)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu öğreticide, bir Oluşturucu olarak, Azure IoT Central uygulamanızda yeni bir Azure IoT Edge cihaz türü tanımlamak için bir cihaz şablonunun nasıl kullanılacağı gösterilmektedir. 

Azure IoT Edge genel bir bakış almak için, [Bu makaleye bakın](overview-iot-central.md). 

Azure IoT Edge üç bileşenden oluşur:
* **IoT Edge modüller** , Azure Hizmetleri, üçüncü taraf hizmetleri veya kendi kodunuzu çalıştıran kapsayıcılardır. Modüller IoT Edge cihazlara dağıtılır ve yerel olarak bu cihazlarda yürütülür.
* **IoT Edge çalışma zamanı** her bir IoT Edge cihazında çalışır ve her cihaza dağıtılan modülleri yönetir.
* **Bulut tabanlı bir arabirim** , IoT Edge cihazları uzaktan izlemenize ve yönetmenize olanak sağlar. IoT Central bulut arabirimi olur.

**Azure IoT Edge** cihaz, Azure IoT Edge cihazına bağlanan aşağı akış cihazlarına sahip bir ağ geçidi cihazı olabilir. Bu öğreticide aşağı akış cihaz bağlantısı desenleri ele alınacaktır.

**Cihaz şablonu** , & IoT Edge modüllerinizin yeteneklerini tanımlar. Özellikler modülün gönderdiği Telemetriyi, modül özelliklerini ve bir modülün yanıt verdiği komutları içerir.

Bu öğreticide, bir **ortam algılayıcısı** cihaz şablonu oluşturacaksınız. Bir ortam algılayıcı cihazı:

* Sıcaklık gibi telemetri gönderir.
* Bulutta, telemetri gönderme aralığı gibi bir güncelleştirildiği zaman yazılabilir özelliklere yanıt verir.
* Sıcaklığı sıfırlama gibi komutlara yanıt verir.

Ayrıca, bu öğreticide bir **ortam ağ geçidi** cihaz şablonu oluşturacaksınız. Bir çevresel ağ geçidi cihazı:

* Sıcaklık gibi telemetri gönderir.
* Bulutta, telemetri gönderme aralığı gibi bir güncelleştirildiği zaman yazılabilir özelliklere yanıt verir.
* Sıcaklığı sıfırlama gibi komutlara yanıt verir.
* Diğer cihaz yetenek modelleriyle ilişkilerin yapılmasına izin verir


Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Yeni bir cihaz cihaz şablonu oluşturun Azure IoT Edge.
> * Dağıtım bildirimini karşıya yükleyin.
> * Her modül için telemetri, özellik ve komutları içeren yetenekler oluşturun
> * Modül telemetrisi için görselleştirme tanımlayın.
> * Aşağı akış cihaz şablonlarına ilişkiler ekleme
> * Cihaz şablonunuzu yayımlayın.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için bir Azure IoT Central uygulamanızın olması gerekir. [Azure IoT Central uygulaması oluşturmak](quick-deploy-iot-central.md)için bu hızlı başlangıcı izleyin.


## <a name="downstream-device-relationships-with-gateway--modules"></a>Ağ Geçidi & modüllerle aşağı akış cihaz ilişkileri

Aşağı akış cihazları, $edgeHub modülü aracılığıyla Azure IoT Edge Gateway cihazına bağlanabilir. Bu Azure IoT Edge cihaz bu senaryoda saydam bir ağ geçidi haline geliyor

![Merkezi uygulama sayfası](./media/tutorial-define-edge-device-type/gateway-transparent.png)

Aşağı akış cihazları, özel bir modül aracılığıyla Azure IoT Edge Gateway cihazına bağlanabilir. Aşağıdaki senaryoda aşağı akış cihazların bir Modbus özel modülü ile bağlanması ve aşağı akış cihazlarının, $edgeHub modülü aracılığıyla Azure IoT Edge ağ geçidi cihazına bağlanabilmesi olabilir.

![Merkezi uygulama sayfası](./media/tutorial-define-edge-device-type/gateway-module.png)

Aşağı akış cihazları, özel bir modül aracılığıyla Azure IoT Edge Gateway cihazına bağlanabilir. Aşağıdaki senaryoda aşağı akış cihazları bir Modbus özel modülü aracılığıyla bağlanıyor. 

![Merkezi uygulama sayfası](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

Aşağı akış cihazları, birden çok özel modülle Azure IoT Edge ağ geçidi cihazına bağlanabilir. Aşağıdaki senaryoda aşağı akış cihazların bir Modbus özel modülü üzerinden bağlanması, uyumlu olmayan özel modül ve aşağı akış cihazları $edgeHub modülü aracılığıyla Azure IoT Edge ağ geçidi cihazına bağlanabilir. 

![Merkezi uygulama sayfası](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Şablon oluşturma

Bir Oluşturucu olarak, uygulamanızda Azure IoT Edge cihaz şablonları oluşturabilir ve düzenleyebilirsiniz. Bir cihaz şablonu yayımladıktan sonra, cihaz şablonunu uygulayan gerçek cihazlara bağlanabilirsiniz.

### <a name="select-device-template-type"></a>Cihaz şablonu türünü seçin 

Uygulamanıza yeni bir cihaz şablonu eklemek için **cihaz şablonları** sayfasına gidin. Bunu yapmak için sol bölmedeki **cihaz şablonları** sekmesini seçin.

![Merkezi uygulama sayfası](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Yeni bir cihaz şablonu oluşturmaya başlamak için **+ Yeni** seçeneğine tıklayın.

![Cihaz şablonları-yeni](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

Cihaz şablonu türü seçimi sayfasına tacaksınız. **Azure IoT Edge** Kutucuğu ' nı seçin ve **ardından İleri** ' ye tıklayın. altta

![Cihaz şablonu seçimi-Azure IoT Edge](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Cihaz şablonunu özelleştirme

Azure IoT Edge, iş mantığını modüller biçiminde dağıtmanıza ve yönetmenize olanak sağlar. **Azure IoT Edge modüller** , IoT Edge tarafından yönetilen en küçük hesaplama birimidir ve Azure hizmetleri (Azure Stream Analytics gibi) veya kendi çözüme özgü kodunuzla bulunabilir. Modüllerin nasıl geliştirildiği, dağıtıldığı ve korunan okuma [IoT Edge modüllerini](../../iot-edge/iot-edge-modules.md)anlamak için.

Yüksek düzeyde bir dağıtım bildirimi, istenen özellikleriyle yapılandırılmış bir modül TWINS listesidir. Dağıtım bildirimi, hangi modüllerin yükleneceğini ve bunların nasıl yapılandırılacağını IoT Edge bir cihaza (veya bir cihaz grubuna) bildirir. Dağıtım bildirimleri, her modül için istenen özellikleri içerir ikizi. IoT Edge cihazlar her bir modülün bildirilen özelliklerini geri bildirir.

Dağıtım bildirimi oluşturmak için Visual Studio Code kullanın. [Dağıtım bildirimi](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)oluşturma hakkındaki belgeleri izleyin.

Bu öğretici için kullanılacak örnek olarak bir modülle birlikte temel bir dağıtım bildirimi aşağıda verilmiştir. Aşağıdaki JSON 'ı kopyalayın ve. JSON dosyası olarak kaydedin. 

   ```JSON
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {
              "SendData": true,
              "SendInterval": 10
         }
       }
     }
   }
   ```

**Azure IoT Edge dağıtım bildirimini karşıya yükle**

**Araştır** düğmesine tıklayın 

![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Bir Azure IoT Edge ağ geçidi cihaz şablonu oluşturmayı planlıyorsanız, **ağ geçidi cihazını aşağı akış cihazları ile** seçtiğinizden emin olun onay kutusunu işaretleyin

![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-upload.png)

Bir dosya seçimi iletişim kutusu görüntülenir. Dağıtım bildirim dosyasını seçin ve **Aç** düğmesine tıklayın.

Dağıtım bildirim dosyası bir şemaya göre doğrulanacak. Başarılı doğrulama tamamlandıktan sonra **gözden geçir** düğmesine tıklayın

![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

IoT Central bir dağıtım bildirimi yaşam döngüsünün akışı aşağıda verilmiştir.

![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/dmflow.png)

İnceleme sayfası, dağıtım bildiriminin ayrıntıları ile görüntülenir. Dağıtım bildiriminin modül listesi İnceleme sayfasında görüntülenir. Bu öğreticide, SimulatedTemperatureSensor modülünü listelenmiş görürsünüz. **Oluştur** düğmesine tıklayın.

![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

Ağ geçidi cihazı ' nı seçtiyseniz, bu gözden geçirme sayfasını görürsünüz

![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-review.png)


Yeni bir cihaz şablonu oluşturma değer değiştirici, IoT Central içinde cihaz şablonunun oluşturulmakta olduğu şekilde görüntülenir.

Cihaz şablonu modül yetenek modelleriyle oluşturulur. Bu öğreticide, SimulatedTemperatureSensor Module yetenek modeli oluştur ' u görürsünüz. 

Cihaz şablonunun başlığını ortam algılayıcı cihaz şablonuna değiştirin.

![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

Azure IoT Edge cihaz Tak ve kullan modelleme, aşağıdaki gibi yapılır
* Her Azure IoT Edge cihaz şablonu bir **cihaz yetenek modeline** sahip olur
* Dağıtım bildiriminde listelenen her özel modül için bir **Modül yetenek modeli** oluşturulacaktır
* Her modül yetenek modeli ve cihaz yetenek modeli arasında bir **ilişki** kurulacaktır
* Modül yetenek modeli **Modül arabirimlerini** uygular
* Her modül arabirimi şunları içerir
   - Telemetri
   - Özellikler
   - Komutlar

![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

**Modül yetenek modeline özellikler ekleme**

SimulatedTemperatureSensor modülünden örnek bir çıkış aşağıda verilmiştir
```json
{

    "machine": {

        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

SimulatedTemperatureSensor modülüne, yukarıdaki JSON 'ı yansıtan yetenekler ekleyin. 

* SimulatedTemperatureSensor Module yetenek modelinin arabirimini **Yönet** ' e tıklayın. **Özellik Ekle**' ye tıklayın. 

    ![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
* Karmaşık bir tür olduğundan, bir nesne türü olarak makine ekleyin
  
    ![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

    **Tanımla**' ya tıklayın. Açılan pencerede kalıcı olarak nesne adını makineye değiştirin ve Özellikler sıcaklık, basınç ve **Uygula** ' yı oluşturun
  
    ![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
* Karmaşık bir tür olduğundan, nesne türü olarak ortam ekleme

    **Tanımla**' ya tıklayın. Açılan pencerede, nesne adını çevresel olarak değiştirin ve Özellikler sıcaklığını oluşturun ve **Uygula** ' ya tıklayın.
  
    ![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
* TimeCreated öğesini bir tarih saat türü olarak ekleyin ve **Kaydet** ' e tıklayın
  
    ![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>İlişki Ekle

Cihazı bir ağ geçidi cihazı olarak Azure IoT Edge seçtiyseniz, ağ geçidi cihazına bağlanacak cihazlar için cihaz yetenek modellerine aşağı akış ilişkileri ekleyebilirsiniz.
  
  ![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

İlişki, bir cihaza veya bir modüle eklenebilir.
  
  ![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


Bir aşağı akış cihaz yetenek modeli seçebilir veya yıldız seçeneğini belirleyebilirsiniz. 
  
  ![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  Bu öğreticide, herhangi bir aşağı akış ilişkisine izin verilecek olan bir yıldız, yani bir aşağı seçim olacaktır. **Kaydet**’e tıklayın

  ![Cihaz şablonu-Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Bulut özellikleri ekle

Bir cihaz şablonu, bulut özellikleri içerebilir. Bulut özellikleri yalnızca IoT Central uygulamasında bulunur ve bir cihaza hiçbir şekilde gönderilmez veya buradan alınmaz.

1. **Bulut özellikleri** ' ni seçin ve **+ bulut özelliği Ekle**' ye tıklayın. Cihaz şablonunuza bir bulut özelliği eklemek için aşağıdaki tablodaki bilgileri kullanın.

    | Görünen Ad      | Anlamsal tür | Şema |
    | ----------------- | ------------- | ------ |
    | Son Hizmet Tarihi | None          | Tarih   |
    | Müşteri adı     | None          | Dize |

2. Değişikliklerinizi kaydetmek için **Kaydet** ' i seçin:

  
    ![Bulut özellikleri-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Özelleştirmeler ekleme

Bir arabirimi değiştirmeniz veya cihaz yetenek modelinizi sürümünüzü gerektirmeyen bir işleve IoT Central özgü özellikler eklemeniz gerektiğinde özelleştirmeleri kullanın. Yetenek modeli taslak veya Yayımlanma durumundaysa alanları özelleştirebilirsiniz. Yalnızca arabirim uyumluluğunu kesen alanları özelleştirebilirsiniz. Örneğin, şunları yapabilirsiniz:

- Bir özelliğin görünen adını ve birimlerini özelleştirin.
- Değer bir grafikte göründüğünde kullanılacak varsayılan rengi ekleyin.
- Bir özellik için başlangıçtaki, minimum ve maksimum değerleri belirtin.

Yetenek adını veya yetenek türünü özelleştiremezsiniz. **Kaydet**’e tıklayın
  
![Özelleştirmeler-Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Görünüm oluşturma

Bir Oluşturucu olarak, uygulamayı bir işlecine çevresel algılayıcı cihazı hakkındaki ilgili bilgileri görüntüleyecek şekilde özelleştirebilirsiniz. Özelleştirmeleriniz, uygulamaya bağlı çevresel algılayıcı cihazlarını yönetmek için işlecini etkinleştirir. Cihazlarla etkileşim kurmak için kullanılacak bir operatör için iki tür görünüm oluşturabilirsiniz:

* Cihaz ve bulut özelliklerini görüntüleme ve düzenleme için formlar.
* Cihazları görselleştirmek için panolar.

### <a name="configure-a-view-to-visualize-devices"></a>Cihazları görselleştirmek için bir görünüm yapılandırma

Bir cihaz panosu, bir işlecin grafikleri ve ölçümleri kullanarak bir cihazı görselleştirmesine olanak sağlar. Oluşturucu olarak, bir cihaz panosunda hangi bilgilerin gösterileceğini tanımlayabilirsiniz. Cihazlar için birden çok Pano tanımlayabilirsiniz. Ortam algılayıcısı telemetrisini görselleştirmek üzere bir pano oluşturmak için, **Görünümler** ' i seçin ve ardından **cihazı görselleştirmeyi**seçin:

  
![Görünümler-Azure IoT Edge](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


Çevresel telemetri ve makine telemetrisi, grafik oluşturmak için aşağıdakileri yapın

Çevresel telemetri ' i sürükleyin ve çizgi grafiği ' ni seçin. 
  
![Görünümler-Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambientchart.png)

Yapılandır simgesine tıklayın ve verileri görselleştirmek ve **yapılandırma güncelleştirme** düğmesine tıklayarak sıcaklık ve nem ' ı seçin. 
  
![Görünümler-Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

Görünümünüzü kaydetmek için **Kaydet** ' i seçin:

Diğer özellikleri veya telemetri değerlerini gösteren daha fazla kutucuk ekleyebilirsiniz. Statik metin, bağlantılar ve görüntüler de ekleyebilirsiniz. Panodaki bir kutucuğu taşımak veya yeniden boyutlandırmak için fare işaretçisini döşemenin üzerine taşıyın ve kutucuğu yeni bir konuma sürükleyin veya yeniden boyutlandırın.
  
![Görünümler-Azure IoT Edge](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Bir cihaz formu Ekle

Bir cihaz formu, bir işlecin yazılabilir cihaz özelliklerini ve bulut özelliklerini düzenlemesini sağlar. Bir Oluşturucu olarak, birden çok form tanımlayabilir ve her formda hangi cihaz ve bulut özelliklerinin gösterileceğini seçebilirsiniz. Ayrıca, salt okunurdur cihaz özelliklerini bir formda görüntüleyebilirsiniz.

Ortam algılayıcısı özelliklerini görüntülemek ve düzenlemek için bir form oluşturmak için:

**Ortam algılayıcı** şablonundaki **görünümlere** gidin. Yeni bir görünüm eklemek için **cihazı ve bulut verilerini Düzenle** kutucuğunu seçin.
  
![Görünümler-Azure IoT Edge](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

**Çevresel algılayıcı özellikleri**form adını girin.

**Müşteri adı** ve **son hizmet tarihi** bulutu özelliklerini formdaki mevcut bölüme sürükleyin.
  
![Görünümler-Azure IoT Edge](./media/tutorial-define-edge-device-type/views-properties.png)

Görünümünüzü kaydetmek için **Kaydet** ' i seçin.

### <a name="generate-default-views"></a>Varsayılan görünümleri oluştur

Varsayılan görünümleri oluşturma işlevselliği Azure IoT Edge şablonlar için desteklenmez 

## <a name="publish-device-template"></a>Cihaz şablonunu Yayımla

Sanal bir ortam algılayıcısı oluşturmadan veya gerçek bir ortam sensöre bağlanmadan önce, cihaz şablonunuzu yayımlamanız gerekir.

Bir cihaz şablonunu yayımlamak için:

1. Cihaz **şablonları** sayfasından cihaz şablonunuza gidin.

2. **Yayımla**’yı seçin.
  
    ![Görünümler-Yayımla](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. **Cihaz şablonu Yayımla** Iletişim kutusunda **Yayımla**' yı seçin.
  
    ![Görünümler-Yayımla](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

Bir cihaz şablonu yayımlandıktan sonra **cihazlar** sayfasında ve işlecine görünür. Yayımlanmış bir cihaz şablonunda, yeni bir sürüm oluşturmadan bir cihaz yetenek modeli düzenleyemezsiniz. Ancak, bulut özellikleri, özelleştirmeler ve görünümlerde, sürüm oluşturmadan yayımlanmış bir cihaz şablonunda güncelleştirmeler yapabilirsiniz. Herhangi bir değişiklik yaptıktan sonra, bu değişiklikleri işletmenizin dışına göndermek için **Yayımla** ' yı seçin.
  
![Görünümler-Yayımla](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

* Yaprak cihaz şablonu olarak yeni bir kenar oluşturma
* Karşıya yüklenen bir dağıtım bildiriminden modüller oluşturma
* Karmaşık tür telemetrisi ve özellikler ekleme
* Bulut özellikleri oluşturun.
* Özelleştirmeler oluşturun.
* Cihaz telemetrisi için görselleştirme tanımlayın.
* Edge cihaz şablonunuzu yayımlayın.

Artık Azure IoT Central uygulamanızda bir cihaz şablonu oluşturduğunuza göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Cihaza Bağlan](./tutorial-connect-pnp-device.md)
