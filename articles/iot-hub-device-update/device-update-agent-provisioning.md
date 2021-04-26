---
title: Azure IoT Hub Aracısı için cihaz güncelleştirmesini sağlama | Microsoft Docs
description: Azure IoT Hub Aracısı için cihaz güncelleştirmesi sağlama
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 812de4850c6c3577346915a0072ea11c60f7ba73
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365459"
---
# <a name="device-update-agent-provisioning"></a>Cihaz Güncelleştirme Aracısı sağlama

Cihaz güncelleştirme modülü Aracısı, diğer sistem işlemleriyle ve IoT Hub aynı mantıksal cihazın bir parçası olarak bağlanan [IoT Edge modüllerle](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) birlikte çalışabilir. Bu bölümde, cihaz güncelleştirme aracısının bir modül kimliği olarak nasıl sağlanacağı açıklanmaktadır. 


## <a name="module-identity-vs-device-identity"></a>Modül kimliği ile cihaz kimliği karşılaştırması

IoT Hub, her bir cihaz kimliği altında, en fazla 50 modül kimliği oluşturabilirsiniz. Her modül kimliği dolaylı olarak ikizi bir modül üretir. Cihaz tarafında IoT Hub cihaz SDK 'Ları, her birinin IoT Hub için bağımsız bir bağlantı açtığı modüller oluşturmanızı sağlar. Modül kimliği ve modül ikizi, benzer özellikleri cihaz kimliği ve cihaz ikizi olarak sağlar, ancak daha ayrıntılı bir ayrıntı düzeyine sahiptir. [IoT Hub modül kimlikleri hakkında daha fazla bilgi edinin](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)


## <a name="support-for-device-update"></a>Cihaz güncelleştirme desteği

Şu IoT cihaz türleri şu anda cihaz güncelleştirmesiyle destekleniyor:

* Linux cihazları (IoT Edge ve IoT Edge olmayan cihazlar):
    * Görüntü A/B güncelleştirmesi:
        - ARM64 (başvuru görüntüsü), gerektiğinde diğer mimaride [kendi görüntülerini oluşturmak](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent) için açık kaynak üzerinden genişletilebilir.
        - Ubuntu 18,04 simülatör
       
    * Aşağıdaki platformlar/mimariler için paket Aracısı tarafından desteklenen derlemeler:
        - Ubuntu Server 18,04 x64 paket Aracısı 
        - Debian 9 
        
* Kısıtlanmış cihazlar:
    * AzureRTOS Cihaz Güncelleştirme Aracısı örnekleri: [Azure Için azure IoT Hub öğreticisi Için cihaz güncelleştirmesi-gerçek zamanlı Işletim sistemi](device-update-azure-real-time-operating-system.md)

* Bağlantısı kesilen cihazlar: 
    * [Bağlantısı kesilen cihaz güncelleştirmesi desteğini anlama](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>Önkoşullar  

[Paket tabanlı güncelleştirmeler](https://docs.microsoft.com/azure/iot-hub-device-update/understand-device-update#support-for-a-wide-range-of-update-artifacts)için IoT cihazı/IoT Edge cihazını ayarlıyorsanız, aşağıdaki adımları izleyerek makinenizin depolarına Packages.Microsoft.com ekleyin:

1. Cihaz güncelleştirme aracısını yüklemeyi planladığınız makinede veya IoT cihazında oturum açın.

1. Bir Terminal penceresi açın.

1. Cihazınızın işletim sistemiyle eşleşen depo yapılandırmasını yükler.
    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. Oluşturulan listeyi sources. List. d dizinine kopyalayın.
    ```shell
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```
    
1. Microsoft GPG ortak anahtarını yükler.
    ```shell
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    ```

    ```shell
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>Cihaz güncelleştirme aracısını modül kimliği olarak sağlama

Bu bölümde, cihaz güncelleştirme aracısının IoT Edge etkinleştirilmiş cihazlarda, uç olmayan IoT cihazlarında ve diğer IoT cihazlarında modül kimliği olarak nasıl sağlanacağı açıklanmaktadır.


### <a name="on-iot-edge-enabled-devices"></a>IoT Edge etkinleştirilmiş cihazlarda

Cihaz güncelleştirme aracısını [IoT Edge etkinleştirilmiş cihazlara](https://docs.microsoft.com/azure/iot-edge)sağlamak için bu yönergeleri izleyin.

1. [Azure IoT Edge çalışma zamanını yüklemek ve sağlamak](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?view=iotedge-2020-11&preserve-view=true)için yönergeleri izleyin.

1. Cihaz güncelleştirme görüntüsü güncelleştirme aracısını yükler
    - Yansıma güncelleştirme piyasaya çıkarma ' yi bir temel görüntü (ADU-Base-Image) ve bir güncelleştirme görüntüsü (ADU-Update-Image) kullanarak farklı sürümlere denemek için [yapıtlar](https://github.com/Azure/iot-hub-device-update/releases) içinde örnek görüntüler sunuyoruz. [Görüntünün IoT Hub cihazınıza nasıl yanıp sönegösteren](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image)örneğe bakın.  

1. Cihaz güncelleştirme paketi güncelleştirme aracısını yükler  
    - Packages.miscrosoft.com ' dan en son Aracı sürümleri için: cihazınızdaki paket listelerini güncelleştirin ve aşağıdakileri kullanarak cihaz Güncelleştirme Aracısı paketini ve bağımlılıklarını yüklersiniz:   
    ```shell
    sudo apt-get update
    ```
    
    ```shell
    sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
    ```
    
    - [Yapıtlardan](https://github.com/Azure/iot-hub-device-update/releases) yaklaşan sürüm adayı sürümleri:. dep dosyasını, cihaz güncelleştirme aracısını yüklemek istediğiniz makineye indirin ve ardından:
     ```shell
    Sudo apt-get install -y ./"<PATH TO FILE>"/"<.DEP FILE NAME>"
     ```
    
1. Artık IoT Edge cihazınızda cihaz güncelleştirme aracısını başlatmaya hazırsınız. 

### <a name="on-non-edge-iot-linux-devices"></a>Edge olmayan IoT Linux cihazlarda

IoT Linux cihazlarınızda cihaz güncelleştirme aracısını sağlamak için bu yönergeleri izleyin.

1. IoT kimlik hizmetini yükleyip IoT cihazınıza en son sürümü ekleyin. 
    1. Makinede veya IoT cihazında oturum açın.
    1. Bir terminal penceresi açın.
    1.  Şu komutu kullanarak IoT cihazınıza en son [IoT kimlik hizmetini](https://github.com/Azure/iot-identity-service/blob/main/docs/packaging.md#installing-and-configuring-the-package) yüklersiniz:
    
        ```shell
        sudo apt-get install aziot-identity-service
        ```
        
1. IoT cihaz bilgilerini almak için IoT kimlik hizmeti sağlanıyor.
    * Sağlama bilgilerini ekleyebilmemiz için yapılandırma şablonunun özel bir kopyasını oluşturun. Bir terminalde aşağıdaki komutu girin.
      
        ```shell
        sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
        ```
   
1. Ardından yapılandırma dosyasını, bu cihaz veya makine için hazırlayıcı olarak davranmak istediğiniz cihazın bağlantı dizesini içerecek şekilde düzenleyin. Bir terminalde aşağıdaki komutu girin.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. Aşağıdaki örnekte olduğu gibi bir ileti görmeniz gerekir:

    :::image type="content" source="media/understand-device-update/config.png" alt-text="IoT kimlik hizmeti yapılandırma dosyası diyagramı." lightbox="media/understand-device-update/config.png":::

    1. Aynı nano penceresinde, "bağlantı dizesiyle El Ile sağlama" ile bloğu bulun.
    1. Pencerede ' sağlama ' ' nın önünde "#" simgesini silin
    1. Pencerede, "#" simgesini ' Source 'un önünde Sil 
    1. Pencerede, "#" simgesini ' connection_string ' önünde Sil
    1. Pencerede, tırnak içindeki dizeyi ' connection_string ' öğesinin sağına silin ve ardından Bağlantı dizenizi buraya ekleyin 
    1. Değişikliklerinizi dosyaya ' Ctrl + X ' ve ardından ' Y ' ile kaydedin ve ' Enter ' tuşuna basın. 
    
1.  Şimdi aşağıdaki komutla IoT kimlik hizmetini uygulayın ve yeniden başlatın. Şimdi bir "bitti!" görmeniz gerekir Bu, IoT kimlik hizmetini başarıyla yapılandırdığınız anlamına gelir.

    > [!Note]
    > IoT kimlik hizmeti, şu anda simetrik anahtarlar kullanarak modül kimliklerini IoT Hub kaydeder.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1.  Son olarak cihaz güncelleştirme aracısını yükler. Yansıma güncelleştirme piyasaya çıkarma ' yi bir temel görüntü (ADU-Base-Image) ve bir güncelleştirme görüntüsü (ADU-Update-Image) kullanarak farklı sürümlere denemek için [yapıtlar](https://github.com/Azure/iot-hub-device-update/releases) içinde örnek görüntüler sunuyoruz. [Görüntünün IoT Hub cihazınıza nasıl yanıp sönegösteren](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image)örneğe bakın.

1.  Artık IoT cihazınızda cihaz güncelleştirme aracısını başlatmaya hazırsınız. 

### <a name="other-iot-devices"></a>Diğer IoT cihazları

Cihaz Güncelleştirme Aracısı Ayrıca, test için veya kısıtlı cihazlarda IoT kimlik hizmeti olmadan da yapılandırılabilir. Bir bağlantı dizesi (modülünden veya cihazdan) kullanarak cihaz güncelleştirme aracısını sağlamak için aşağıdaki adımları izleyin.

1.  Yansıma güncelleştirme piyasaya çıkarma ' yi bir temel görüntü (ADU-Base-Image) ve bir güncelleştirme görüntüsü (ADU-Update-Image) kullanarak farklı sürümlere denemek için [yapıtlar](https://github.com/Azure/iot-hub-device-update/releases) içinde örnek görüntüler sunuyoruz. [Görüntünün IoT Hub cihazınıza nasıl yanıp sönegösteren](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image)örneğe bakın.

1.  Makinede veya IoT Edge cihaz/IoT cihazında oturum açın.
    
1.  Bir terminal penceresi açın.

1.  Bağlantı dizesini [cihaz güncelleştirme yapılandırma dosyasına](device-update-configuration-file.md)ekleyin:
    1. Terminal penceresine aşağıdan şunu girin:
        - [Paket güncelleştirmeleri](device-update-ubuntu-agent.md) kullanımı: sudo nano/etc/ADU/adu-conf.txt
        - [Görüntü güncelleştirmeleri](device-update-raspberry-pi.md) şunu kullanır: sudo nano/ADU/adu-conf.txt
       
    1. İçindeki bir metinle birlikte açık bir pencere görmeniz gerekir. IoT cihazında Cihaz Güncelleştirme Aracısı 'nı ilk kez sağladığınızda ' connection_String = ' izleyen tüm dizeyi silin. Yalnızca tutucu metin yerleştirir.
    
    1. Terminalde, "<-Connection-String>" değerini, cihaz Güncelleştirme Aracısı örneğiniz için aygıtın bağlantı dizesiyle değiştirin.
    
        > [!Important]
        > Bağlantı dizesinin etrafına tırnak eklemeyin.
        ```shell
        connection_string=<ADD CONNECTION STRING HERE>
        ```
       
    1. Girin ve kaydedin.
    
1.  Artık IoT cihazınızda cihaz güncelleştirme aracısını başlatmaya hazırsınız. 


## <a name="how-to-start-the-device-update-agent"></a>Cihaz güncelleştirme aracısını başlatma

Bu bölümde, IoT cihazınızda başarıyla çalışan bir modül kimliği olarak cihaz güncelleştirme aracısının nasıl başlatılacağı ve doğrulanması açıklanmaktadır.

1.  Cihaz güncelleştirme aracısının yüklü olduğu makinede veya cihazda oturum açın.

1.  Bir Terminal penceresi açın ve aşağıdaki komutu girin.
    ```shell
    sudo systemctl restart adu-agent
    ```
    
1.  Aşağıdaki komutu kullanarak aracının durumunu kontrol edebilirsiniz. Herhangi bir sorun görürseniz, bu [sorun giderme kılavuzuna](troubleshoot-device-update.md)bakın.
    ```shell
    sudo systemctl status adu-agent
    ```
    
    Durum Tamam ' ı görmeniz gerekir.

1.  Cihaz güncelleştirme aracısında yapılandırdığınız cihazı bulmak için IoT Hub portalında IoT cihazı veya IoT Edge cihazlar ' a gidin. Cihaz Güncelleştirme Aracısı 'nı bir modül olarak çalıştırmayı göreceksiniz. Örnek:

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="Cihaz güncelleştirme modülü adı diyagramı." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>Cihaz güncelleştirme aracısını derleme ve çalıştırma

Ayrıca kendi müşteri aygıtı güncelleştirme aracınızı oluşturabilir ve değiştirebilirsiniz.

Cihaz güncelleştirme aracısını kaynaktan [derlemek](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) için yönergeleri izleyin.

Aracı başarıyla oluşturulduktan sonra aracıyı [çalıştırmaya](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) zaman atalım.

Şimdi, aracının yansımanıza dahil etmek için gerekli değişiklikleri yapın.  Yönergeler için cihaz güncelleştirme aracısını [değiştirme](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) bölümüne bakın.


## <a name="troubleshooting-guide"></a>Sorun giderme kılavuzu

Sorunlarla karşılaşırsanız, olası sorunların engelini kaldırmaya ve Microsoft 'a sağlamak üzere gerekli bilgileri toplamanıza yardımcı olmak için IoT Hub [sorun giderme kılavuzu](troubleshoot-device-update.md) Için cihaz güncelleştirmesini gözden geçirin.


## <a name="next-steps"></a>Sonraki adımlar

IoT Hub yönelik basit bir cihaz güncelleştirmesi gösterimi için önceden oluşturulmuş aşağıdaki görüntüleri ve ikili dosyaları kullanabilirsiniz:

- [Görüntü güncelleştirme: Raspberry Pi 3 B + Ile çalışmaya](device-update-raspberry-pi.md) başlama, daha fazla mimaride sizin için kendi görüntülerini oluşturmak üzere açık kaynak aracılığıyla Genişletilebilir görüntü.

- [Ubuntu (18,04 x64) simülatörü başvuru Aracısı 'Nı kullanmaya başlama](device-update-simulator.md)

- [Paket güncelleştirmesi: Ubuntu Server 18,04 x64 paket Aracısı 'nı kullanmaya başlama](device-update-ubuntu-agent.md)

- [Azure için Azure IoT Hub öğreticisi için cihaz güncelleştirmesi-gerçek zamanlı Işletim sistemi](device-update-azure-real-time-operating-system.md)

