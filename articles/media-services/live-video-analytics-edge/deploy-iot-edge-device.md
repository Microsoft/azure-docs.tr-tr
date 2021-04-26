---
title: IoT Edge cihazında canlı video analizi dağıtma-Azure
description: Bu makalede, IoT Edge cihazınızda canlı video analizlerini dağıtmanıza yardımcı olacak adımlar listelenmektedir. Örneğin, yerel bir Linux makinesine erişiminiz varsa ve/veya daha önce bir Azure Media Services hesabı oluşturduysanız, bunu yapabilirsiniz.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: 466cc236c1482b2ae6a31b40b6f3461a2c2fbfc1
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280301"
---
# <a name="deploy-live-video-analytics-on-an-iot-edge-device"></a>IoT Edge cihazda canlı video analizi dağıtma

Bu makalede, IoT Edge cihazınızda canlı video analizlerini dağıtmanıza yardımcı olacak adımlar listelenmektedir. Örneğin, yerel bir Linux makinesine erişiminiz varsa ve/veya daha önce bir Azure Media Services hesabı oluşturduysanız, bunu yapabilirsiniz.

> [!NOTE]
> ARM64 cihazlarına yönelik destek, IoT Edge yapılar ve daha yeni sürümlerde canlı video analizi 'nde bulunabilir `1.0.4` .
> ARM64 cihazlarında Azure IoT Edge Runtime çalıştırmaya yönelik destek [genel önizlemededir](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

* [Desteklenen Linux işletim sistemlerinden](../../iot-edge/support.md#operating-systems) birini çalıştıran bir x86-64 veya bir ARM64 cihazı
* [Sahip olduğunuz ayrıcalıklara](../../role-based-access-control/built-in-roles.md#owner) sahip olduğunuz Azure aboneliği
* [Oluşturma ve kurulum IoT Hub](../../iot-hub/iot-hub-create-through-portal.md)
* [IoT Edge cihazı kaydetme](../../iot-edge/how-to-register-device.md)
* [Debian tabanlı Linux sistemlerine Azure IoT Edge çalışma zamanını yükleme](../../iot-edge/how-to-install-iot-edge.md)
* [Azure Media Services hesabı oluşturma](../latest/account-create-how-to.md)

    * Şu bölgelerden birini kullanın: Doğu ABD 2, Doğu ABD, Orta ABD, Orta Kuzey ABD, Japonya Doğu, Batı ABD, Batı ABD 2, Orta Batı ABD, Kanada Doğu, UK Güney, Fransa orta, Fransa Güney, İsviçre Kuzey, İsviçre Batı ve Japonya Batı.
    * Genel amaçlı v2 (GPv2) depolama hesapları kullanmanız önerilir

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>Canlı video analizlerini kullanmak için Azure kaynaklarını yapılandırma

### <a name="create-custom-azure-resource-manager-role"></a>Özel Azure Resource Manager rolü oluşturma

Bkz. [özel Azure Resource Manager rolü oluşturma](create-custom-azure-resource-manager-role-how-to.md) ve canlı video analizi kullanımı için hizmet sorumlusuna atama.

### <a name="set-up-a-premium-streaming-endpoint"></a>Premium akış uç noktası ayarlama

Videoyu buluta sürekli kaydetmek için canlı video analizi kullanmayı ve sonra yeniden oynamadan önce [sorgu API 'lerini](playback-recordings-how-to.md#query-api) kullanmayı düşünüyorsanız, medya hizmetinizi bir [Premium akış uç noktası](../latest/stream-streaming-endpoint-concept.md#types)kullanacak şekilde güncelleştirmenizi öneririz.  

Bu isteğe bağlı bir adımdır. Bunu yapmak için bu Azure CLı komutunu kullanabilirsiniz:

```azurecli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

Akış uç noktasını başlatmak için bu komutu kullanabilirsiniz 

> [!IMPORTANT]
> Aboneliğiniz bu noktada faturalandırılmaya başlayacak.

```azurecli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

Media Service API 'Lerine erişmek için kimlik bilgilerini almak üzere bu makaledeki adımları izleyin: [medya hizmeti API 'lerine erişin](../latest/access-api-howto.md?tabs=portal) ve Portal sekmesini seçin.

## <a name="create-and-use-local-user-account-for-deployment"></a>Dağıtım için yerel kullanıcı hesabı oluşturma ve kullanma
IoT Edge modülünde canlı video analizlerini çalıştırmak için, olabildiğince az ayrıcalığa sahip bir yerel kullanıcı hesabı oluşturun. Örnek olarak, Linux makinenizde aşağıdaki komutları çalıştırın:

```
sudo groupadd -g 1010 localusergroup
sudo useradd --home-dir /home/edgeuser --uid 1010 --gid 1010 lvaedgeuser
```

## <a name="granting-permissions-to-device-storage"></a>Cihaz depolamaya izinler veriliyor

Artık yerel bir kullanıcı hesabı oluşturduğunuza göre, 

* Uygulama yapılandırma verilerini depolamak için yerel bir klasöre ihtiyacınız olacak. Aşağıdaki komutları kullanarak bir klasör oluşturun ve YerelKullanıcı hesabına bu klasöre yazma izinleri verin:

```
sudo mkdir -p /var/lib/azuremediaservices
sudo chown -R lvaedgeuser /var/lib/azuremediaservices
```

* Ayrıca, [yerel bir dosyaya video kaydetmek](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)için bir klasör gerekir. Aynı şekilde yerel bir klasör oluşturmak için aşağıdaki komutları kullanın:

```
sudo mkdir -p /var/media
sudo chown -R lvaedgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>Canlı video analizi kenar modülünü dağıtma

IoT Edge üzerindeki canlı video analizi, Module [ikizi yapılandırma şemasında](module-twin-configuration-schema.md)belgelenen modül ikizi özelliklerini kullanıma sunar. 

### <a name="deploy-using-the-azure-portal"></a>Azure portalını kullanarak dağıtma

Azure portal, dağıtım bildirimi oluşturma ve dağıtımı bir IoT Edge cihazına gönderme işlemlerinde size rehberlik eder.  
#### <a name="select-your-device-and-set-modules"></a>Cihazınızı seçme ve modülleri ayarlama

1. [Azure Portal](https://ms.portal.azure.com/) oturum açın ve IoT Hub 'ınıza gidin.
1. Menüden **IoT Edge** seçin.
1. Cihaz listesinden hedef cihazın KIMLIĞINE tıklayın.
1. **Modülleri Ayarlama**'yı seçin.

#### <a name="configure-a-deployment-manifest"></a>Dağıtım bildirimi yapılandırma

Dağıtım bildirimi, hangi modüllerin dağıtılacağını, modüller arasında verilerin nasıl akacağını ve modül TWINS 'in istenen özelliklerini tanımlayan bir JSON belgesidir. Azure portal, dağıtım bildirimi oluşturma konusunda size yol gösteren bir sihirbaza sahiptir. Sekmelerde düzenlenmiş üç adım vardır: **modüller**, **rotalar** ve **Gözden geçirme + oluştur**.

#### <a name="add-modules"></a>Modül ekle

1. Sayfanın **IoT Edge modüller** **bölümünde açılan menü ekle '** ye tıklayın ve **IoT Edge modülü** ' nü seçerek **IoT Edge modülü Ekle** sayfasını görüntüleyin.
1. **Modül ayarları** sekmesinde, modül için bir ad sağlayın ve ardından KAPSAYıCı görüntüsü URI 'sini belirtin:   
    Örnekler:
    
    * **IoT Edge modül adı**: lvaedge
    * **Görüntü URI 'si**: MCR.Microsoft.com/Media/Live-video-Analytics:2.0    
    
    ![Ekran görüntüsü modül ayarları sekmesini gösterir.](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > Bu yordamda açıklandığı gibi **Modül ayarlarında**, **kapsayıcı oluşturma seçeneklerinde** ve **module ikizi Settings** sekmelerinde değer belirtene kadar **Ekle** ' yi seçmeyin.
    
    > [!WARNING]
    > Modüller için çağrılar yaptığınızda büyük/küçük harfe duyarlıdır Azure IoT Edge. Modül adı olarak kullandığınız tam dizeyi unutmayın. '

1. **Ortam değişkenleri** sekmesini açın.
   
   Ortam değişkenlerini gördüğünüz giriş kutularına aşağıdaki değerleri ekleyin ![](./media/deploy-iot-edge-device/environment-variables.png) 

1. **Kapsayıcı oluşturma seçenekleri** sekmesini açın.

    ![Kapsayıcı oluşturma seçenekleri](./media/deploy-iot-edge-device/container-create-options.png)
 
    Modül tarafından oluşturulan günlük dosyalarının boyutunu sınırlamak için aşağıdaki JSON 'ı kopyalayıp kutuya yapıştırın.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
               "/var/media:/var/media"
            ]
        }
    }
    ````
   
   JSON 'daki "bağlamalar" bölümünün 2 girişi vardır:
   1. "/var/lib/azudüzeltici aservices:/var/lib/azudüzeltici aservices": Bu, kalıcı uygulama yapılandırma verilerini kapsayıcıdan bağlamak ve Edge cihazında saklamak için kullanılır.
   1. "/var/Media:/var/Media": Bu, uç cihazla kapsayıcı arasında medya klasörlerini bağlar. Bu, uç cihazda video kliplerin depolanmasını destekleyen bir medya grafiği topolojisi çalıştırdığınızda video kayıtlarını depolamak için kullanılır.
   
1. **Modül Ikizi ayarları** sekmesinde, aşağıdaki JSON 'u kopyalayın ve kutuya yapıştırın.
 
    ![İkizi ayarları](./media/deploy-iot-edge-device/twin-settings.png)

    IoT Edge üzerindeki canlı video analizi, [module ikizi yapılandırma şemasında](module-twin-configuration-schema.md)listelendiği gibi, çalıştırılacak bir zorunlu ikizi özellikleri kümesi gerektirir.  

    Module Ikizi Settings düzenleme kutusuna girmeniz gereken JSON şöyle görünür:    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    Bunlar için **gereken** Özellikler ve yukarıdaki JSON için  
    * {SubscriptionID}-Azure abonelik KIMLIĞINIZ
    * {resourceGroupName}-medya hizmeti hesabınızın ait olduğu kaynak grubu
    * {AMS-Account-Name}-Media Services hesabınızın adıdır
    
    Diğer değerleri almak için bkz. [erişim Azure Media Services API 'si](../latest/access-api-howto.md?tabs=portal) ve Portal sekmesini seçme.  
    * Aadtenantıd-kiracınızın KIMLIĞIDIR ve Yukarıdaki bağlantıdan "Aadtenantıd" ile aynıdır.
    * Aadserviceprincıpalappıd-bu, medya hizmeti hesabınız için hizmet sorumlusunun uygulama KIMLIĞIDIR ve Yukarıdaki bağlantıdan "Aadclitıd" ile aynıdır.
    * Aadservicesprincipalsecret-bu, hizmet sorumlusunun parolasıdır ve Yukarıdaki bağlantıdan "AadSecret" ile aynıdır.

    Aşağıda, JSON 'a eklenebilecek bazı ek **Önerilen** Özellikler verilmiştir ve modülün izlenmesine yardımcı olur. Daha fazla bilgi için bkz. [izleme ve günlüğe kaydetme](monitoring-logging.md):
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    JSON içine ekleyebileceğiniz bazı **isteğe bağlı** özellikler aşağıda verilmiştir:
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```

   > [!Note]
   > İkizi özelliği **Allowunsecuredendpoints** , öğreticiler ve hızlı başlangıçlar amacıyla doğru olarak ayarlanır.   
   Üretim ortamında çalışırken bu özelliği **false** olarak ayarlamanız gerekir. Bu, uygulamanın tüm güvenli olmayan uç noktaları engellemesini ve Graf topolojilerini çalıştırmak için geçerli bağlantı kimlik bilgilerinin gerekli olacağını sağlar.  
   
    Module ikizi özelliklerini eklemek için Ekle ' yi seçin.
1. **İleri ' yi seçin:** rotalar bölümüne devam etmek için yollar.
    Rotaları belirtin.

Varsayılan yolları koruyun ve Ileri ' yi seçin: Gözden geçirme bölümüne devam etmek için, İnceleme **+ Oluştur** seçeneğini belirleyin.

#### <a name="review-deployment"></a>Dağıtımı gözden geçir

İnceleme Bölümü, önceki iki bölümdeki seçimlerinize göre oluşturulan JSON dağıtım bildirimini gösterir. Ayrıca, eklemediğiniz iki modül de vardır: $edgeAgent ve $edgeHub. Bu iki modül IoT Edge çalışma zamanını yapar ve her dağıtımda gerekli varsayılanlar olur.

Dağıtım bilgilerinizi gözden geçirin ve ardından Oluştur ' u seçin.

### <a name="verify-your-deployment"></a>Dağıtımınızı doğrulama

Dağıtımı oluşturduktan sonra IoT Hub 'ınızın IoT Edge sayfasına dönersiniz.

1. Bilgilerini açmak için dağıtıma hedeflenmiş IoT Edge cihazı seçin.
2. Cihaz ayrıntılarında, BLOB depolama modülünün hem dağıtım hem de **cihaz tarafından bildirilen** olarak listelendiğini doğrulayın.

Modülün cihazda başlatılması ve sonra IoT Hub geri bildirilmesi birkaç dakika sürebilir. Güncelleştirilmiş durumu görmek için sayfayı yenileyin.
Durum kodu: 200 – OK [, IoT Edge çalışma zamanının](../../iot-edge/iot-edge-runtime.md) sağlıklı ve sorunsuz bir şekilde çalıştığı anlamına gelir.

![Ekran görüntüsü IoT Edge çalışma zamanı için bir durum değeri gösterir.](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>Doğrudan yöntem çağırma

Sonra, bir doğrudan yöntemi çağırarak örneği test etmenizi sağlar. LvaEdge modülizin tarafından sunulan tüm doğrudan yöntemleri anlamak için [IoT Edge üzerindeki canlı video analizinin doğrudan yöntemlerini](direct-methods.md) okuyun.

1. Oluşturduğunuz Edge modülüne tıkladığınızda sizi yapılandırma sayfasına götürür.  

    ![Ekran görüntüsü, bir kenar modülünün yapılandırma sayfasını gösterir.](./media/deploy-iot-edge-device/modules.png)
1. Doğrudan yöntem menü seçeneğine tıklayın.

    > [!NOTE] 
    > Geçerli sayfada görebileceğiniz gibi bağlantı dizesi bölümlerine bir değer eklemeniz gerekir. **Ayar adı** bölümünde herhangi bir şeyi gizlemenize veya değiştirmenize gerek yoktur. Bu, herkese açık olmasına izin vermek için Tamam.

    ![Direct yöntemi](./media/deploy-iot-edge-device/module-details.png)
1. Sonra, yöntem adı kutusuna "Graphtopologyılist" yazın.
1. Sonra, aşağıdaki JSON yükünü kopyalayın ve yük kutusuna yapıştırın.
    
    ```
    {
        "@apiVersion" : "2.0"
    }
    ```
1. Sayfanın üst kısmındaki "yöntemi çağır" seçeneğine tıklayın

    ![Doğrudan yöntemler](./media/deploy-iot-edge-device/direct-method.png)
1. Sonuç kutusunda bir durum 200 iletisi görmeniz gerekir

    ![Durum 200 iletisi](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>Sonraki adımlar

[Hızlı başlangıç: Başlarken-canlı video analizlerini deneme IoT Edge](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)

> [!TIP]
> Yukarıdaki hızlı başlangıç ile devam ederseniz, Visual Studio Code kullanarak doğrudan yöntemleri çağırırken, varsayılan yerine bu makale aracılığıyla IoT Hub eklenen cihazı kullanacaksınız `lva-sample-device` .