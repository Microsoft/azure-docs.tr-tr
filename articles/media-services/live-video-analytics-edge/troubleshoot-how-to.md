---
title: IoT Edge-Azure 'da canlı video analizlerinin sorunlarını giderme
description: Bu makalede, IoT Edge üzerinde canlı video analizi için sorun giderme adımları ele alınmaktadır.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 12/04/2020
ms.openlocfilehash: d766843f58bc2cdd0dcdddfad337b23fefb28768
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698748"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>IoT Edge 'da canlı video analizi sorunlarını giderme

Bu makalede, Azure IoT Edge üzerindeki canlı video analizi (LVA) için sorun giderme adımları ele alınmaktadır.

## <a name="troubleshoot-deployment-issues"></a>Dağıtım sorunlarını giderme

### <a name="diagnostics"></a>Tanılama

Canlı video analizi dağıtımınızın bir parçası olarak, IoT Hub ve IoT Edge cihazları gibi Azure kaynaklarını ayarlarsınız. Sorunları tanılamaya yönelik ilk adım olarak, aşağıdaki yönergeleri izleyerek sınır cihazının düzgün şekilde ayarlandığından emin olun:

1. [ `check` Komutunu çalıştırın](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [IoT Edge sürümünüzü denetleyin](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [IoT Edge Güvenlik Yöneticisi ve günlüklerinin durumunu kontrol edin](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [IoT Edge hub 'ı üzerinden giden Iletileri görüntüleyin](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Kapsayıcıları yeniden başlatın](../../iot-edge/troubleshoot.md#restart-containers).
1. [Güvenlik duvarı ve bağlantı noktası yapılandırma kurallarınızı denetleyin](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>Dağıtım öncesi sorunlar

Kenar altyapısı düzgün çalışıyorsa, dağıtım bildirim dosyası ile ilgili sorunları arayabilirsiniz. Canlı video analizlerini diğer IoT modüllerinin yanı sıra IoT Edge cihazında IoT Edge modüle dağıtmak için, IoT Edge hub, IoT Edge Aracısı ve diğer modülleri ve bunların özelliklerini içeren bir dağıtım bildirimi kullanırsınız. Bildirim dosyasını dağıtmak için aşağıdaki komutu kullanabilirsiniz:

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```
JSON kodu düzgün biçimlendirilmemiş ise, şu hatayı alabilirsiniz:   
&nbsp;&nbsp;&nbsp;' **<deployment manifest.json> Content ' bağımsız değişkeni için ' ' dosyasından JSON ayrıştırılamadı: "ek veriler: satır 101 sütun 1 (karakter 5325)"**

Bu hatayla karşılaşırsanız, eksik parantezler veya dosyanın yapısıyla ilgili diğer sorunlar için JSON 'ı denetlemeniz önerilir. Dosya yapısını doğrulamak için, [JSON Görüntüleyici eklentisi Ile Notepad + +](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) gibi bir Istemciyi veya [JSON biçimlendirici & Doğrulayıcı](https://jsonformatter.curiousconcept.com/)gibi bir çevrimiçi aracı kullanabilirsiniz.

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Dağıtım sırasında: doğrudan medya Graf yöntemi ile tanılama yöntemleri 

IoT Edge modülündeki canlı video analizi IoT Edge cihazında doğru dağıtıldıktan sonra, [doğrudan Yöntemler](direct-methods.md)' i çağırarak medya grafiğini oluşturabilir ve çalıştırabilirsiniz.  
>[!NOTE]
>  Doğrudan Yöntem çağrıları **`lvaEdge`** yalnızca modüle yapılmalıdır.

Doğrudan yöntemler kullanarak medya grafiğinin bir tanılamasını çalıştırmak için Azure portal kullanabilirsiniz:

1. Azure portal, IoT Edge cihazınıza bağlı olan IoT Hub 'ına gidin.

1. **Otomatik cihaz yönetimini** arayın ve ardından **IoT Edge**' yi seçin.  

1. Sınır aygıtları listesinde, tanılamak istediğiniz cihazı seçin.  
         
    ![Sınır cihazlarının listesini görüntüleyen Azure portal ekran görüntüsü](./media/troubleshoot-how-to/lva-sample-device.png)


1. Yanıt kodu *200-Tamam* olup olmadığını kontrol edin. [IoT Edge çalışma zamanına](../../iot-edge/iot-edge-runtime.md) yönelik diğer yanıt kodları şunlardır:
    * 400-dağıtım yapılandırması hatalı biçimlendirilmiş veya geçersiz.
    * 417-cihazda bir dağıtım yapılandırma kümesi yok.
    * 412-dağıtım yapılandırmasındaki şema sürümü geçersiz.
    * 406-IoT Edge cihaz çevrimdışı veya durum raporları göndermiyor.
    * 500-IoT Edge çalışma zamanında bir hata oluştu.

    > [!TIP]
    > Ortamınızda Azure IoT Edge modüller çalıştıran sorunlarla karşılaşırsanız, sorun giderme ve Tanılama için kılavuz olarak **[Azure IoT Edge standart tanılama adımlarını](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** kullanın.
### <a name="post-deployment-direct-method-error-code"></a>Dağıtım sonrası: doğrudan yöntem hata kodu
1. Bir durum alırsanız `501 code` doğrudan yöntem adının doğru olduğundan emin olun. Yöntem adı ve istek yükü doğru ise, başarılı kod = 200 ile birlikte sonuçları almalısınız. 
1. İstek yükü doğru değilse, `400 code` doğrudan yöntem çağrla ilgili sorunu tanılamanıza yardımcı olması gereken hata kodunu ve iletiyi gösteren bir durum ve yanıt yükü alacaksınız.
    * Bildirilen ve Desired özelliklerinin denetlenmesi, modül özelliklerinin dağıtımla eşitlenip eşitlenmediğini anlamanıza yardımcı olabilir. Aksi takdirde, IoT Edge cihazınızı yeniden başlatabilirsiniz. 
    * Özellikle Graphtopologyılist gibi basit olan birkaç yöntemi çağırmak için [doğrudan Yöntemler](direct-methods.md) kılavuzunu kullanın. Kılavuz ayrıca beklenen istek ve yanıt yüklerini ve hata kodlarını da belirtir. Basit doğrudan Yöntemler başarılı olduktan sonra, canlı video analizi IoT Edge modülünün işlevsel olarak tamam olduğundan emin olabilirsiniz.
        
       ![IoT Edge modülü için "doğrudan yöntem" bölmesinin ekran görüntüsü.](./media/troubleshoot-how-to/direct-method.png) 

1. **Dağıtımda belirtilen** ve **cihaz sütunları tarafından rapor** edildiğinde, IoT Edge modülündeki canlı video *analizine doğrudan* yöntemleri çağırabilirsiniz. İstenen ve bildirilen özellikleri denetleyebilir ve doğrudan yöntemleri çağırabileceğiniz bir sayfaya gitmek için modülü seçin. Aşağıdakileri göz önünde bulundurun: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Dağıtım sonrası: çalıştırma sırasında sorunlar için günlükleri tanılama 

IoT Edge modülünüzün kapsayıcı günlükleri modül çalışma zamanı sırasında sorunlarınızı ayıklamanıza yardımcı olmak için tanılama bilgileri içermelidir. Sorun [için kapsayıcı günlüklerini denetleyebilir](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) ve sorunu kendi kendine tanılayabilirsiniz. 

Önceki tüm denetimleri çalıştırırsanız ve sorun yaşamaya devam ediyorsanız, Azure ekibi tarafından daha fazla [analiz için IoT Edge `support bundle` ](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) cihazdan günlükleri toplayın. Destek için [bizimle iletişime geçerek](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) toplanan günlükleri gönderebilirsiniz.

## <a name="common-error-resolutions"></a>Genel hata çözümleri

Canlı video analizi, IoT Edge cihazında bir IoT Edge modülü olarak dağıtılır ve IoT Edge Aracısı ve hub modülleriyle işbirliği içinde çalışmaktadır. Canlı video analizi dağıtımıyla karşılaşacağınız bazı yaygın hatalardan bazıları, temel IoT altyapısından kaynaklanan sorunlardan kaynaklanır. Hatalar şunlardır:

* [IoT Edge Aracısı yaklaşık bir dakika sonra durdu](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [IoT Edge Aracısı bir modülün görüntüsüne erişemiyor (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [IoT Edge aracı modülü "boş yapılandırma dosyası" raporlar ve cihazda hiçbir modül başlatılmaz](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [IoT Edge hub 'ı başlatılamıyor](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [IoT Edge güvenlik arka plan programı geçersiz bir ana bilgisayar adıyla başarısız oluyor](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [Canlı video analizi veya başka bir özel IoT Edge modülü, 404 hatası ile uç hub 'ına ileti gönderemediğinde](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [IoT Edge modülü başarıyla dağıtıldı ve sonra cihazdan kayboluyor](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

    > [!TIP]
    > Ortamınızda Azure IoT Edge modüller çalıştıran sorunlarla karşılaşırsanız, sorun giderme ve Tanılama için kılavuz olarak **[Azure IoT Edge standart tanılama adımlarını](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** kullanın.

**[Canlı video analizi kaynakları kurulum betiğini](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)** çalıştırırken da sorunlarla karşılaşabilirsiniz. Bazı yaygın sorunlar şunlardır:

* Sahip ayrıcalıklarınız olmayan bir abonelik kullanma. Bu, betiğin bir **ForbiddenError** veya **AuthorizationFailed** hatasıyla başarısız olmasına neden olur.
    * Bu sorunu daha fazla almak için, kullanmayı planladığınız abonelik için **sahip** ayrıcalıklarına sahip olduğunuzdan emin olun. Kendiniz bunu yapaamadıysanız, doğru ayrıcalıklara izin vermek için lütfen abonelik yöneticisine ulaşın.
* **İlke ihlali nedeniyle şablon dağıtımı başarısız oldu.**
    * Bu sorunu geçebilmeniz için lütfen BT yöneticinizle birlikte çalışarak SSH kimlik doğrulamasını engellemeyi atlamak için sanal makine oluşturmak üzere yapılan çağrı (ler) in olduğundan emin olun. Azure kaynaklarıyla iletişim kurmak için Kullanıcı adı ve parola gerektiren bir güvenli Savunma Ağı kullandığımızda bu gerekli olmayacaktır. Bu kimlik bilgileri, sanal makine başarıyla oluşturulduğunda, dağıtıldıktan ve IoT Hub eklendikten sonra Cloud Shell **~/CloudDrive/LVA-Sample/vm-edge-device-credentials.txt** dosyasında depolanır.
* Kurulum betiği, hizmet sorumlusu ve/veya Azure kaynakları oluşturamaz.
    * Bu sorunu daha fazla almak için lütfen aboneliğinizin ve Azure kiracınızın en yüksek hizmet sınırlarına ulaşmadığını kontrol edin. [Azure ad hizmeti sınırları ve kısıtlamaları](../../active-directory/enterprise-users/directory-service-limits-restrictions.md) ile [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../../azure-resource-manager/management/azure-subscription-service-limits.md) hakkında daha fazla bilgi edinin.

> [!TIP]
> Yardım etmeniz gerekebilecek ek sorunlar varsa, lütfen **[günlükleri toplayın ve bir destek bileti gönderebilirsiniz](#collect-logs-for-submitting-a-support-ticket)**. Bize e-posta göndererek bize de ulaşabilirsiniz **[amshelp@microsoft.com](mailto:amshelp@microsoft.com)** .
### <a name="live-video-analytics-working-with-external-modules"></a>Canlı video analizi dış modüllerle çalışıyor

Medya grafiği uzantısı işlemcileri ile canlı video analizi, medya grafiğinin HTTP veya gRPC protokollerini kullanarak diğer IoT Edge modüllerden veri göndermesini ve almasını sağlayabilir. Belirli bir [örnek](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)olarak, bu medya grafiği, video çerçevelerini Yolo v3 gibi bir dış çıkarım MODÜLÜNE ve http protokolünü kullanarak JSON tabanlı analiz sonuçları almaya gönderebilir. Böyle bir topolojide, olayların hedefi çoğunlukla IoT Hub 'idir. Hub 'da çıkarım olaylarını görmeme durumlarında, aşağıdakileri denetleyin:

* Medya grafiğinin hub 'ına ve incelediğiniz hub 'ın aynı olup olmadığını kontrol edin. Birden çok dağıtım oluştururken, birden çok hub ile bitemez ve olaylar için yanlış hub 'ı yanlışlıkla kontrol edebilirsiniz.
* Azure portal, dış modülün dağıtılıp dağıtılmadığını ve çalışıp çalışmadığını denetleyin. Buradaki örnek görüntüde, rtspsim, yolov3, tinyyolov3 ve Loganalticsagent, lvaEdge modülüne harici olarak çalışan IoT Edge modüllerdir.

    [![Azure IoT Hub 'de modüllerin çalışma durumunu gösteren ekran görüntüsü. ](./media/troubleshoot-how-to/iot-hub-azure.png)](./media/troubleshoot-how-to/iot-hub-azure.png#lightbox)

* Doğru URL uç noktasına olay gönderiyor olup olmadığınızı denetleyin. Dış AI kapsayıcısı, üzerinden bir URL ve bir bağlantı noktası kullanıma sunar ve POST isteklerinden verileri döndürür. Bu URL `endpoint: url` , http uzantısı işlemcisi için bir özellik olarak belirtilir. [TOPOLOJI URL](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json)'sinde görüldüğü gibi, uç nokta, ıncobir URL parametresi olarak ayarlanır. Parametresi için varsayılan değerin veya geçirilen değerin doğru olduğundan emin olun. Istemci URL 'SI (kıvrımlı) kullanarak çalışıp çalışmadığını görmek için test edebilirsiniz.  

    Örnek olarak, 172.17.0.3 IP adresine sahip yerel makinede çalışan bir Yolo v3 kapsayıcısı aşağıda verilmiştir.  
    
    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Sonuç döndürüldü:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```
    > [!TIP]
    > Makinenin IP adresini bulmak için **[Docker İnceleme komutunu](https://docs.docker.com/engine/reference/commandline/inspect/)** kullanın.
    
* Medya grafiği uzantısı işlemcisini kullanan bir grafiğin bir veya daha fazla örneğini çalıştırıyorsanız, `samplingOptions` video akışın saniye başına kare (fps) oranını yönetmek için alanını kullanmanız gerekir. 

   * Belirli durumlarda, uç makinenin CPU veya belleğinin yüksek oranda kullanıldığı durumlar için bazı çıkarım olaylarını kaybedebilirsiniz. Bu sorunu gidermek için alanın özelliği için düşük bir değer ayarlayın `maximumSamplesPerSecond` `samplingOptions` . Grafiği her bir örneğinde 0,5 ("maximumSamplesPerSecond": "0,5") olarak ayarlayabilir ve ardından hub üzerindeki çıkarım olaylarını denetlemek için örneği yeniden çalıştırabilirsiniz.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Paralel olarak birden çok doğrudan yöntem – timeout hatası 

IoT Edge üzerindeki canlı video analizi, birden fazla topoloji ve birden çok grafik örneği ayarlamanıza olanak tanıyan doğrudan Yöntem tabanlı bir programlama modeli sağlar. Topoloji ve grafik kurulumunun bir parçası olarak, IoT Edge modülünde birden çok doğrudan yöntem çağrısı çağırılır. Bu birden çok yöntem çağrılarını paralel olarak çağırırsanız, özellikle de grafikleri başlatıp durdurmuş gibi, aşağıdaki gibi bir zaman aşımı hatası yaşayabilirsiniz: 

Derleme başlatma yöntemi Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync özel durum oluşturdu. Microsoft. Azure. Devices. Common. Exceptions. ıothubexception: Microsoft. Azure. Devices. Common. Exceptions. ıothubexception:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Paralel olarak doğrudan yöntemleri *çağırmamalıdır* . Bunları sırayla çağırın (yani, yalnızca önceki bir yöntem bittikten sonra bir doğrudan yöntem çağrısı yapın).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Destek bileti göndermek için günlükleri toplayın

Kendi kendine destekli sorun giderme adımları sorununuzu gidermezse, Azure portal gidin ve [bir destek bileti açın](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Günlükler, IP adresiniz gibi kişisel olarak tanımlanabilir bilgiler (PII) içerebilir. Günlüklerin tüm yerel kopyaları, bunları inceledikten ve destek biletini kapatdığımız anda silinir.  

Bilet içine eklenmesi gereken ilgili günlükleri toplamak için, destek isteğinin **Ayrıntılar** bölmesine sırasıyla aşağıdaki yönergeleri izleyin ve günlük dosyalarını karşıya yükleyin.  
1. [Canlı video analizi modülünü ayrıntılı günlükleri toplayacak şekilde yapılandırma](#configure-live-video-analytics-module-to-collect-verbose-logs)
1. [Hata ayıklama günlüklerini aç](#live-video-analytics-debug-logs)
1. Sorunu yeniden oluşturun
1. Portaldaki **IoT Hub** sayfasından sanal makineye bağlanma
    1. *Hata günlüğü* klasöründeki tüm dosyaları ZIP.

       > [!NOTE]
       > Bu günlük dosyaları kendi kendine Tanılama için tasarlanmamıştır. Bunlar, Azure mühendislik ekibinin sorunlarınızı analiz edebilmesi için tasarlanmıştır.

       * Aşağıdaki komutta **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** , **Adım 2**' de daha önce ayarladığınız uç cihazdaki hata ayıklama günlüklerinin konumuyla değiştirdiğinizden emin olun.  

           ```
           sudo apt install zip unzip  
           zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
           ```

    1. *debugLogs.zip* dosyasını destek bileti 'ne ekleyin.
1. [Destek paketi komutunu](#use-the-support-bundle-command)çalıştırın, günlükleri toplayın ve destek bileti 'ne ekleyin.

### <a name="configure-live-video-analytics-module-to-collect-verbose-logs"></a>Ayrıntılı günlükleri toplamak için canlı video analizi modülünü yapılandırma
Canlı video analizi modülünüzü, `logLevel` ve aşağıdaki gibi ayarlayarak ayrıntılı günlükleri toplayacak şekilde yapılandırın `logCategories` :
```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

Bunu aşağıdakilerden birini yapabilirsiniz:
* **Azure Portal**, canlı video analizi modülü [ ![ kimlik Ikizi ](media/troubleshoot-how-to/module-twin.png)](media/troubleshoot-how-to/module-twin.png#lightbox) özellikleri ' nin modül kimliği ikizi özelliklerini güncelleştirerek.    
* Ya da **dağıtım bildirimi** dosyanızda, bu girdileri canlı video analizi modülünün Özellikler düğümüne ekleyebilirsiniz

### <a name="use-the-support-bundle-command"></a>Support-demeti komutunu kullanma

IoT Edge cihazdan Günlükler toplamanız gerektiğinde, en kolay yöntem `support-bundle` komutunu kullanmaktır. Bu komut şunu toplar:

- Modül günlükleri
- IoT Edge Güvenlik Yöneticisi ve kapsayıcı altyapısı günlükleri
- JSON çıkışını IoT Edge denetleme
- Yararlı hata ayıklama bilgileri

1. `support-bundle`Günlüklerinizin ne kadar zaman kapsamasını istediğinizi belirtmek için *--since* bayrağıyla komutunu çalıştırın. Örneğin, 2H, son iki saat için günlükleri alacak. Bu bayrağın değerini farklı dönemler için Günlükler içerecek şekilde değiştirebilirsiniz.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Bu komut, komutu çalıştırdığınız dizinde *support_bundle.zip* adlı bir dosya oluşturur. 
   
1. *support_bundle.zip* dosyasını destek bileti 'ne ekleyin.

### <a name="live-video-analytics-debug-logs"></a>Canlı video analizi hata ayıklama günlükleri

IoT Edge modülündeki canlı video analizlerini hata ayıklama günlükleri oluşturacak şekilde yapılandırmak için aşağıdakileri yapın:

1. [Azure Portal](https://portal.azure.com)oturum açın ve IoT Hub 'ınıza gidin.
1. Sol bölmede **IoT Edge**' yi seçin.
1. Cihazlar listesinde hedef cihazın KIMLIĞINI seçin.
1. Bölmenin üst kısmında, **modülleri ayarla**' yı seçin.

   ![Azure portal "modülleri ayarlama" düğmesinin ekran görüntüsü.](media/troubleshoot-how-to/set-modules.png)

1. **IoT Edge modüller** bölümünde, **lvaedge**' i bulup seçin.
1. **Kapsayıcı oluşturma seçeneklerini** belirleyin.
1. **Bağlamalar** bölümünde aşağıdaki komutu ekleyin:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Bu komut, uç aygıtı ve kapsayıcısı arasındaki Günlükler klasörlerini bağlar. Günlükleri farklı bir konumda toplamak istiyorsanız, aşağıdaki komutu kullanın ve **$LOG _LOCATION_ON_EDGE_DEVICE** , kullanmak istediğiniz konumla değiştirin: `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. **Güncelleştir**’i seçin.
1. **Gözden geçir + Oluştur**’u seçin. Başarılı bir doğrulama iletisi yeşil bir başlık altında gönderilir.
1. **Oluştur**’u seçin.
1. **Modül kimliği ikizi** öğesini, günlüklerin toplandığı dizine Işaret eden DebugLogsDirectory parametresini işaret etmek üzere güncelleştirin:

    a. **Modüller** tablosu altında **lvaedge**' i seçin.  
    b. Bölmenin üst kısmında **modül kimliği ikizi**' nı seçin. Düzenlenebilir bir bölme açılır.  
    c. **İstenen anahtar** altında aşağıdaki anahtar/değer çiftini ekleyin:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Bu komut, uç aygıtı ve kapsayıcısı arasındaki Günlükler klasörlerini bağlar. Günlükleri cihazda farklı bir konumda toplamak istiyorsanız:
    > 1. **Bağlamalar** bölümündeki hata ayıklama günlüğü konumu için bir bağlama oluşturun, **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** ve **$Debug _LOG_LOCATION** istediğiniz konumla değiştirin:`/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. **$DEBUG _LOG_LOCATION** önceki adımda kullanılan konum ile değiştirerek aşağıdaki komutu kullanın:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. **Kaydet**’i seçin.


1. **Modül kimliği ikizi** içindeki değeri *null* olarak ayarlayarak günlük toplamayı durdurabilirsiniz. **Modül kimliği ikizi** sayfasına dönün ve aşağıdaki parametreyi şu şekilde güncelleştirin:

    `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>Günlüğe kaydetme etrafında en iyi uygulamalar

[İzleme ve günlüğe kaydetme](monitoring-logging.md) , taksonomiyi anlamak ve LVA sorunlarını ayıklamada yardımcı olacak günlükleri oluşturmak için yardımcı olmalıdır. 

GRPC sunucu uygulamasının diller arasında farklılık gösterdiği gibi, sunucuda oturum açma eklemenin standart bir yolu yoktur.  

Örnek olarak, .NET Core kullanarak bir gRPC sunucusu oluşturursanız gRPC hizmeti, **GRPC** kategorisi altına Günlükler ekler. GRPC 'den ayrıntılı günlükleri etkinleştirmek için, günlüğe kaydetme sırasında LogLevel alt bölümüne aşağıdaki öğeleri ekleyerek, GRPC öneklerini dosya appsettings.jshata ayıklama düzeyine yapılandırın: 

```
{ 
  "Logging": { 
    "LogLevel": { 
      "Default": "Debug", 
      "System": "Information", 
      "Microsoft": "Information", 
      "Grpc": "Debug" 
       } 
  } 
} 
``` 

Bunu başlangıç. cs dosyasında, ConfigureLogging ile de yapılandırabilirsiniz: 

```
public static IHostBuilder CreateHostBuilder(string[] args) => 
    Host.CreateDefaultBuilder(args) 
        .ConfigureLogging(logging => 
        { 

           logging.AddFilter("Grpc", LogLevel.Debug); 
        }) 
        .ConfigureWebHostDefaults(webBuilder => 
        { 
            webBuilder.UseStartup<Startup>(); 
        }); 

``` 

[GRPC 'de .net üzerinde günlüğe kaydetme ve tanılama](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1) , bir GRPC sunucusundan bazı tanılama günlüklerini toplamak için bazı yönergeler sağlar. 

### <a name="a-failed-grpc-connection"></a>Başarısız bir gRPC bağlantısı 

Grafik etkin ve kameradan akışa alıyorsa, bağlantı canlı video analizi tarafından korunur. 

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>Bu kaynaklar performans sorunlarına yol geldiğinde CPU ve GPU kaynaklarının yükünü izleme ve Dengeleme

Canlı video analizi, herhangi bir donanım kaynağı izlemeyi izlemez veya sağlamıyor. Geliştiricilerin donanım üreticileri izleme çözümlerini kullanması gerekecektir. Ancak, Kubernetes kapsayıcıları kullanıyorsanız, [Kubernetes panosunu](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)kullanarak cihazı izleyebilirsiniz. 

.NET Core belgelerindeki gRPC, [performans En Iyi uygulamaları](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1) ve [Yük Dengelemesi](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1#load-balancing)hakkında bazı değerli bilgileri de paylaşır.  

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>Bir çıkarım sunucusunda herhangi bir çerçeve almadığı ve aldığınız zaman bir "Bilinmeyen" protokol hatası olduğunda sorun giderme 

Sorun hakkında daha fazla bilgi almak için yapabileceğiniz birkaç şey vardır.  

* Canlı video analizi modülünün istenen özelliklerine "**Ediapipeline** günlük kategorisini ekleyin ve günlük düzeyinin olarak ayarlandığından emin olun `Information` .  
* Ağ bağlantısını test etmek için Edge cihazından aşağıdaki komutu çalıştırabilirsiniz. 

   ```
   sudo docker exec lvaEdge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>” 
   ```

   Komut, içe geçmiş bir metnin kısa bir dizesini çıktısa, Telnet, çıkarım sunucunuza bir bağlantı açmak ve bir ikili gRPC kanalı açmak için başarıyla yapılır. Bunu görmüyorsanız, Telnet bir ağ hatası rapor eder. 
* Çıkarım sunucunuzda, gRPC kitaplığında ek günlük kaydını etkinleştirebilirsiniz. Bu, gRPC kanalının kendisi hakkında daha fazla bilgi verebilir. Bunu yapmak dile göre farklılık [gösterir.](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1) 

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>İlk arabellek için geri sonuç göndermeksizin gRPC arabelleğinden daha fazla görüntü seçme

GRPC veri aktarımı sözleşmesinin bir parçası olarak, canlı video analizinin gRPC Infer sunucusuna gönderdiği tüm iletiler onaylanır. Bir görüntü çerçevesinin alındığını hiçbir şekilde ele alınmadığından, veri anlaşması kesilir ve istenmeyen durumlara yol açabilir.  

GRPC sunucunuzu canlı video analiziyle kullanmak için, paylaşılan bellek en iyi performans için kullanılabilir. Bunun için programlama dili/ortamı tarafından kullanıma sunulan Linux paylaşılan bellek yeteneklerini kullanmanız gerekir. 

1. Linux paylaşılan bellek tanıtıcısını açın.
1. Bir çerçeve alındıktan sonra, paylaşılan bellek içindeki adres uzaklığa erişin.
1. Gerçek zamanlı video analizi tarafından bellek kazanılabilmesi için çerçeve işleme tamamlamayı kabul edin.

   > [!NOTE]
   > Çerçevenin canlı video analizlerine uzun bir süre alındığını bildiren bir gecikme yaparsanız, paylaşılan belleğin dolu olmasına ve veri kaybına neden olabilir.
1. Her çerçeveyi, anahtar oluşturma sunucusunda seçtiğiniz bir veri yapısında (liste, dizi, vb.) depolayın.
1. Daha sonra istediğiniz görüntü çerçevesi sayısına sahipseniz işlem mantığınızı çalıştırabilirsiniz.
1. Hazırlık sırasında, etkin video analizinin Insa sınırlaması sonucunu geri döndürün.

## <a name="next-steps"></a>Sonraki adımlar

[Öğretici: bulutta buluta ve kayıttan yürütmeye yönelik olay tabanlı video kaydı](event-based-video-recording-tutorial.md)