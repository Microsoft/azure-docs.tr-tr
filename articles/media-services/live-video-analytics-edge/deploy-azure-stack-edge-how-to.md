---
title: Azure Stack Edge üzerinde canlı video analizi dağıtma
description: Bu makalede, Azure Stack Kenarunuzda canlı video analizlerini dağıtmanıza yardımcı olacak adımlar listelenmektedir.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: b5be85e93b81f5cf50284533f21e688384558494
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561160"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>Azure Stack Edge üzerinde canlı video analizi dağıtma

Bu makalede, Azure Stack Kenarunuzda canlı video analizlerini dağıtmanıza yardımcı olacak adımlar listelenmektedir. Cihaz kurulduktan ve etkinleştirildikten sonra, canlı video analizi dağıtımı için de hazırlanın. 

Canlı video analizi için IoT Hub aracılığıyla dağıtılacak, ancak Azure Stack Edge kaynakları bir Kubernetes API 'SI kullanıma sunacak ve bu da müşterinin canlı video analiziyle arabirim oluşturacak IoT Hub daha fazla farkında olmayan çözümler dağıtmasına olanak tanır. 

> [!TIP]
> Özel dağıtım için Kubernetes (K8s) API 'sinin kullanılması, gelişmiş bir durumdur. Müşterinin Edge modülleri oluşturması ve Kubernetes API 'sini kullanmak yerine her Azure Stack Edge kaynağına IoT Hub aracılığıyla dağıtılması önerilir. Bu makalede, IoT Hub kullanarak canlı video analizi modülünü dağıtma adımları gösterilecektir.

## <a name="prerequisites"></a>Önkoşullar

* [Sahip olduğunuz ayrıcalıklara](../../role-based-access-control/built-in-roles.md#owner)sahip olduğunuz Azure aboneliği.
* [Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) kaynağı
   
* [IoT Hub'ı](../../iot-hub/iot-hub-create-through-portal.md)
* Canlı video analizi modülü için bir [hizmet sorumlusu](./create-custom-azure-resource-manager-role-how-to.md#create-service-principal) .

   IoT Hub kullanılabildiği şu bölgelerden birini kullanın: Doğu ABD 2, Orta ABD, Orta Kuzey ABD, Japonya Doğu, Batı ABD 2, Orta Batı ABD, Kanada Doğu, UK Güney, Fransa Orta, Fransa Güney, İsviçre Kuzey, İsviçre Batı ve Japonya Batı.
* Depolama hesabı

    Genel amaçlı v2 (GPv2) depolama hesapları kullanmanız önerilir.  
    [Genel amaçlı v2 depolama hesabı](../../storage/common/storage-account-upgrade.md?tabs=azure-portal)hakkında daha fazla bilgi edinin.
* Geliştirme makinenizde [Visual Studio Code](https://code.visualstudio.com/) . [Azure IoT araçları uzantısına](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)sahip olduğunuzdan emin olun.
* Geliştirme makinenizin bağlı olduğu ağın bağlantı noktası 5671 üzerinden gelişmiş Ileti kuyruğu protokolüne izin vermek için bağlı olduğundan emin olun. Bu kurulum, Azure IoT araçlarının Azure IoT Hub ile iletişim kurmasını sağlar.

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>Canlı video analizlerini kullanmak için Azure Stack Edge 'i yapılandırma

Azure Stack Edge, ağ veri aktarımı özelliklerine sahip bir hizmet olarak donanım ve bir AI özellikli sınır bilgi işlem aygıtıdır. [Azure Stack Edge ve ayrıntılı kurulum yönergeleri](../../databox-online/azure-stack-edge-deploy-prep.md)hakkında daha fazla bilgi edinin. Başlamak için aşağıdaki bağlantılardaki yönergeleri izleyin:

* [Azure Stack Edge/Data Box Gateway kaynak oluşturma](../../databox-online/azure-stack-edge-deploy-prep.md)
* [Yükleme ve kurulum](../../databox-online/azure-stack-edge-deploy-install.md)
* [Bağlantı ve etkinleştirme](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md)
* [Azure Stack Edge 'e IoT Hub iliştirme](../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute)
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Azure Stack Edge Yerel Kullanıcı arabiriminde Işlem önkoşullarını etkinleştirme

Devam etmeden önce şunları yaptığınızdan emin olun:

* Azure Stack Edge kaynağını etkinleştirdiniz.
* Azure Stack Edge kaynağına erişmek için PowerShell 5,0 veya sonraki bir sürümü çalıştıran bir Windows istemci sistemine erişiminiz var.
* Bir Kubernetes kümesi dağıtmak için, Azure Stack Edge kaynağınızı [Yerel Web Kullanıcı arabirimi](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup)aracılığıyla yapılandırmanız gerekir. 
    
    * İşlemi etkinleştirmek için cihazınızın yerel Web Kullanıcı arabiriminde Işlem sayfasına gidin.
    
        * İşlem için etkinleştirmek istediğiniz bir ağ arabirimi seçin. Etkinleştir’i seçin. İşlem, bu ağ arabirimindeki cihazınızda sanal anahtar oluşturulmasına neden olur.
        * Kubernetes test düğümünü IP 'Leri ve Kubernetes dış hizmetler IP 'lerini boş bırakın.
        * Uygula ' yı seçin-Bu işlem yaklaşık 2 dakika sürer.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text=" Azure Stack Edge Yerel Kullanıcı arabiriminde işlem önkoşulları":::

        * DNS, Kubernetes API 'SI ve Azure Stack Edge kaynağı için yapılandırılmamışsa, pencerenizin ana bilgisayar dosyasını güncelleştirebilirsiniz.
        
            * Bir metin düzenleyicisini yönetici olarak açma
            * ' ' To C:\Windows\System32\drivers\etc\hosts ' dosyasını açın
            * Kubernetes API cihaz adının IPv4 ve ana bilgisayar adını dosyaya ekleyin. (Bu bilgi, cihazlar bölümünün altındaki Azure Stack Edge portalında bulunabilir.)
            * Kaydet ve kapat

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>Azure portal kullanarak canlı video analizi kenar modülünü dağıtma

Bunun için yalnızca [IoT Hub aracılığıyla canlı video analizinden dağıtım](deploy-iot-edge-device.md)yapmanız gereken belirli adımları ele veriyoruz.

1. Kullanıcı ve grup oluşturma adımlarını atlayın ve [canlı video analizi uç modülünü dağıtma](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module) ' ya gidin. Burada bahsedilen adımları izleyin.
1. Kapsayıcı oluşturma seçeneklerinde ortam değişkenlerini ayarlamanız gerekmez. Bu nedenle, bu adımı atlayın.
1. Kapsayıcı oluşturma seçenekleri sekmesini açın.

   * Modül tarafından oluşturulan günlük dosyalarının boyutunu sınırlamak için aşağıdaki JSON 'ı kopyalayıp kutuya yapıştırın.
    
      ```json
        "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                        "max-size": "10m",
                        "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > GRPC protokolünü paylaşılan bellek aktarımına kullanıyorsanız, canlı video analizi ve çıkarım çözümleri arasında paylaşılan bellek erişimi için konak IPC modunu kullanın.
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > JSON 'daki "bağlamalar" bölümünün 2 girişi vardır. Yukarıdaki bağlama bölümünde bahsedilen dizinler LVA tarafından otomatik olarak oluşturulur.  
        Sınır cihaz bağlamalarını güncelleştirmeyi ücretsiz olarak kullanabilirsiniz, ancak bunu yaparsanız, bu dizinlerin cihazda bulunduğundan emin olun.
    
    * "/var/lib/azudüzeltici aservices:/var/lib/azudüzeltici aservices": Bu, kalıcı uygulama yapılandırma verilerini kapsayıcıdan bağlamak ve Edge cihazında saklamak için kullanılır.
    * "/var/Media:/var/Media": Bu, uç cihazla kapsayıcı arasında medya klasörlerini bağlar. Bu, uç cihazda video kliplerin depolanmasını destekleyen bir medya grafiği topolojisi çalıştırdığınızda video kayıtlarını depolamak için kullanılır.
        
1. Belgedeki adımlara devam edin ve modül Ikizi ayarları ' nı girin.
1. **İleri ' yi** seçin: rotalar bölümüne devam etmek için yollar. Rotaları belirtin.

    Varsayılan yolları koruyun ve Ileri ' yi seçin: Gözden geçirme bölümüne devam etmek için, İnceleme + oluştur seçeneğini belirleyin.
1. [Dağıtımınızı gözden geçirin ve doğrulayın](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a>Seçim Docker birimi Takicisini ayarla

Çalışma dizinlerindeki verileri görüntülemek istiyorsanız, başlamadan önce Docker birimi takmaları ayarlamak için aşağıdaki adımları izleyin. 

Bu adımlar, bir ağ geçidi kullanıcısı oluşturmayı ve canlı video analizi çalışma dizini ve canlı video analizi medya klasörünün içeriğini görüntülemek için dosya paylaşımları ayarlamayı kapsar.

> [!NOTE]
> Bağlama takmaları desteklenir, ancak birim bağlama, verilerin görüntülenebilmesine ve uzaktan kopyalanması durumunda verilerin görüntülenmesini sağlar. Hem bağlama hem de birim bağlamayı kullanmak mümkündür, ancak aynı kapsayıcı yolunu işaret edemez.

1. Azure portal açın ve Azure Stack Edge kaynağına gidin.
1. Paylaşımlara erişebilen bir **ağ geçidi kullanıcısı** oluşturun.
    
    1. Sol gezinti bölmesinde, **bulut depolama ağ geçidi**' ne tıklayın.
    1. Sol gezinti bölmesindeki **Kullanıcılar** ' a tıklayın.
    1. Kullanıcı adını ve parolayı ayarlamak için iyon **+ Kullanıcı Ekle** ' ye tıklayın. (Önerilen: `lvauser` ).
    1. **Ekle**'ye tıklayın.
    
1. Canlı video analizi kalıcılığı için **yerel bir paylaşılan** oluşturun.

    1. **Bulut depolama ağ geçidi >paylaşımları**' na tıklayın.
    1. **+ Paylaşımlar Ekle**' ye tıklayın.
    1. Bir paylaşma adı belirleyin. (Önerilen: `lva` ).
    1. Share türünü SMB olarak tutun.
    1. **Kenar ile paylaşımın kullanılması** işaretli olduğundan emin olun.
    1. **Sınır yerel paylaşımının** işaretli olduğundan emin olun.
    1. Kullanıcı ayrıntıları ' nda, son oluşturulan kullanıcı için paylaşıma erişim izni verin.
    1. **Oluştur**'a tıklayın.
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="Yerel paylaşma":::  

    > [!TIP]
    > Azure Stack ucuna bağlı Windows istemcinizi kullanarak, [Bu belgede bahsedilen](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share)ADıMLARı izleyerek SMB paylaşımlarına bağlanın.    

1. Dosya eşitleme depolaması için bir uzak paylaşma oluşturun.

    1. Önce **bulut depolama ağ geçidi >depolama hesaplarına** tıklayarak aynı bölgede bir BLOB depolama hesabı oluşturun.
    1. **Bulut depolama ağ geçidi >paylaşımları**' na tıklayın.
    1. **+ Paylaşımlar Ekle**' ye tıklayın.
    1. Bir paylaşma adı belirleyin. (Önerilen: medya).
    1. Share türünü SMB olarak tutun.
    1. **Kenar ile paylaşımın kullanılması** işaretli olduğundan emin olun.
    1. **Uç yerel paylaşımının** denetlenmediğinden emin olun.
    1. Son oluşturulan depolama hesabını seçin.
    1. Bir kapsayıcı adı ayarlayın.
    1. Depolama türünü Blok Blobu olarak ayarlayın.
    1. Kullanıcı ayrıntıları ' nda, son oluşturulan kullanıcı için paylaşıma erişim izni verin.
    1. **Oluştur**'a tıklayın.    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="Uzak paylaşma":::
    
    
1. Canlı video analizi kenar modülünün kapsayıcı oluşturma seçeneklerini (bkz. [Modül Ekle belgesinde](deploy-iot-edge-device.md#add-modules)nokta 4) kullanarak birim bağlamayı kullanın.

   ```json
      "createOptions": 
         {
             "HostConfig": 
             {
                 "Binds": 
                 [
                     "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                 ],
                 "Mounts": 
                 [
                     {
                         "Target": "/var/media",
                         "Source": "media",
                         "Type": "volume"
                     }
                 ]
             }
         }
    ```

### <a name="verify-that-the-module-is-running"></a>Modülün çalıştığını doğrulama

Son adım modülün bağlı olduğundan ve beklendiği gibi çalıştığından emin olmak. Modülün çalışma zamanı durumu, IoT Hub kaynağında IoT Edge cihazınız için çalışıyor olmalıdır.

Modülün çalıştığını doğrulamak için aşağıdakileri yapın:

1. Azure portal, Azure Stack Edge kaynağına geri dönün
1. Modüller kutucuğunu seçin. Bu sizi modüller dikey penceresine götürür. Modüller listesinde, dağıttığınız modülü ayırt edin. Eklediğiniz modülün çalışma zamanı durumu çalışıyor olmalıdır.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="Özel modül":::

### <a name="configure-the-azure-iot-tools-extension"></a>Azure IoT araçları uzantısını yapılandırma

Azure IoT araçları uzantısını kullanarak IoT Hub 'ınıza bağlanmak için bu yönergeleri izleyin.

1. Visual Studio Code ' de görünüm > Gezgini ' ni seçin. Ya da CTRL + SHIFT + E ' yi seçin.
1. Gezgin sekmesinin sol alt köşesinde Azure IoT Hub ' yi seçin.
1. Bağlam menüsünü görmek için diğer seçenekler simgesini seçin. Sonra IoT Hub bağlantı dizesi ayarla ' yı seçin.
1. Bir giriş kutusu göründüğünde IoT Hub bağlantı dizenizi girin. 

   * Bağlantı dizesini almak için, Azure portal IoT Hub gidin ve sol gezinti bölmesindeki paylaşılan erişim ilkeleri ' ne tıklayın.
   * İothubowner öğesine tıklayarak paylaşılan erişim anahtarlarını alın.
   * Bağlantı dizesini – birincil anahtarı kopyalayın ve VSCode 'daki giriş kutusuna yapıştırın.

   Bağlantı dizesi şöyle görünür:<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Bağlantı başarılı olursa Edge cihazlarının listesi görüntülenir. Azure Stack kenarını görmeniz gerekir. Artık IoT Edge cihazlarınızı yönetebilir ve bağlam menüsü aracılığıyla Azure IoT Hub etkileşim kurabilirsiniz. Sınır cihazında dağıtılan modülleri görüntülemek için, Azure Stack cihaz altında modüller düğümünü genişletin.
    
## <a name="troubleshooting"></a>Sorun giderme

* **Kubernetes API erişimi (kubectl)**

    * Makinenizi [Kubernetes kümesine erişmek](../../databox-online/azure-stack-edge-gpu-create-kubernetes-cluster.md)üzere yapılandırmak için belgeleri izleyin.
    * Dağıtılan tüm IoT Edge modülleri `iotedge` ad alanını kullanır. Kubectl kullanırken bunu eklediğinizden emin olun.  

* **Modül günlükleri**

    Bu `iotedge` araca günlükleri almak için erişilemiyor. Bir dosyaya yönelik günlükleri veya kanalı görüntülemek için [kubectl günlüklerini](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs)  kullanmanız gerekir. Örnek: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`  

* **Pod ve düğüm ölçümleri**

    Pod ve Node ölçümlerini görmek için [kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)  kullanın.
    <br/>`kubectl top pods -n iotedge` 

* **Modül ağı**   
Azure Stack Edge 'de modül keşfi için, modülün konak bağlantı noktası bağlamasının createOptions içinde olması gerekir. Modül daha sonra adreslendirilecektir `moduleName:hostport` .
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* **Birim bağlama**

    Kapsayıcı, bir birimi var olan ve boş olmayan bir dizine bağlamaya çalışıyorsa bir modül başlatılamaz.

* **GRPC kullanılırken paylaşılan bellek**

    Azure Stack Edge kaynaklarında paylaşılan bellek, konak IPC kullanılarak herhangi bir ad alanında yer alan bir ad alanı genelinde desteklenir.
    IoT Hub aracılığıyla dağıtım için bir uç modülünde paylaşılan bellek yapılandırma.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    //(Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
* **Ileri Pod ortak konumu**

    K8s kullanarak, gRPC aracılığıyla canlı video analizi ile iletişim kuran özel çıkarım çözümlerini dağıtmak için, yığınların canlı video analizi modülleriyle aynı düğümlere dağıtıldığından emin olmanız gerekir.

    * **Seçenek 1** -ortak konum Için Düğüm benzeşimini ve yerleşik düğüm etiketlerini kullanın.

    Şu anda NodeSelector özel yapılandırması, kullanıcıların düğümlerde etiket ayarlamak için erişimi olmadığı bir seçenek olarak görünmüyor. Bununla birlikte, müşterinin topolojisine ve adlandırma kurallarına bağlı olarak, [yerleşik düğüm etiketlerini](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels)kullanabiliyor olabilirler. Canlı video analiziyle Azure Stack Edge kaynaklarına başvuran bir Nodebenzeşim bölümü, birlikte bulundurmaya ulaşmak için çıkarım Pod bildirimine eklenebilir.
    * **Seçenek 2** -ortak konum Için Pod benzeşimini kullanın (önerilir).
Kubernetes, aynı düğümde Pod 'yi zamanlayabilir bir [Pod benzeşimi](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)  desteğine sahiptir. Canlı video analizi modülüne başvuran bir Podadinity bölümü, birlikte bulundurmaya ulaşmak için çıkarım Pod bildirimine eklenebilir.

    ```json   
    // Example Live Video Analytics module deployment match labels
    selector:
      matchLabels:
        net.azure-devices.edge.deviceid: dev-ase-1-edge
        net.azure-devices.edge.module: mediaedge
    
    // Example Inference deployment manifest pod affinity
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: net.azure-devices.edge.module
                operator: In
                values:
                - mediaedge
            topologyKey: "kubernetes.io/hostname"
    ```
* **Modül kullanılırken 404 hata kodu `rtspsim`**  
Kapsayıcı, bir kapsayıcı içinde tam olarak bir klasörden videoları okur. Bir dış klasörü kapsayıcı görüntüde zaten var olan bir klasöre eşler/bağlarsanız, Docker kapsayıcı görüntüsünde bulunan dosyaları gizler.  
 
    Örneğin, hiçbir bağlama olmadan kapsayıcıda bu dosyalar olabilir:  
    ```
    root@rtspsim# ls /live/mediaServer/media  
    /live/mediaServer/media/camera-300s.mkv  
    /live/mediaServer/media/win10.mkv  
    ```
     
    Ve ana bilgisayarınız şu dosyalara sahip olabilir:
    ```    
    C:\MyTestVideos> dir
    Test1.mkv
    Test2.mkv
    ```
     
    Ancak, dağıtım bildirim dosyasına aşağıdaki bağlama eklendiğinde Docker, ana bilgisayar üzerindeki ile eşleşecek şekilde/live/mediaServer/media içeriğinin üzerine yazar.
    `C:\MyTestVideos:/live/mediaServer/media`
    
    ```
    root@rtspsim# ls /live/mediaServer/media
    /live/mediaServer/media/Test1.mkv
    /live/mediaServer/media/Test2.mkv
    ```

## <a name="next-steps"></a>Sonraki adımlar

Doğrudan yöntemleri çağırarak canlı video akışlarını çözümlemek için modülünü kullanabilirsiniz. Modüldeki [doğrudan metotları çağırın](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls) .