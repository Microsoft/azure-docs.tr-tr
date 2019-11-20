---
title: Bağlı fabrika çözümü SSS-Azure | Microsoft Docs
description: Bu makalede bağlı fabrika çözümü Hızlandırıcısı için sık sorulan sorular yanıtlanmaktadır. GitHub deposunun bağlantılarını içerir.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: c84452ff71fa34a65b2e56ec753b68bf551c7e35
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826271"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Bağlı fabrika çözümü Hızlandırıcısı için sık sorulan sorular

Ayrıca bkz. IoT Çözüm Hızlandırıcıları için genel [SSS](iot-accelerators-faq.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Çözüm hızlandırıcısının kaynak kodunu nerede bulabilirim?

Kaynak kodu aşağıdaki GitHub deposunda depolanır:

* [Bağlı Fabrika çözüm hızlandırıcısı](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>OPC UA nedir?

2008 ' de yayınlanan OPC Birleşik mimarisi (UA), platformdan bağımsız, hizmet odaklı birlikte çalışabilirlik standardıdır. OPC UA, sektör bilgisayarları, PLCs ve algılayıcılar gibi çeşitli endüstriyel sistemler ve cihazlar tarafından kullanılır. OPC UA, OPC klasik belirtimlerinin işlevselliğini yerleşik güvenliği olan bir Genişletilebilir çerçevede tümleştirir. OPC Foundation tarafından yönetilen bir standarttır. [OPC Foundation](https://opcfoundation.org/) , 440 'den fazla üyesi olan bir kar amacı gütmeyen bir kuruluştur. Kuruluşun amacı, aracılığıyla çok satıcılı, çok platformlu, güvenli ve güvenilir birlikte çalışabilirliği kolaylaştırmak için OPC belirtimlerini kullanmaktır:

* Altyapı
* Belirtimler
* Teknoloji
* İşlemler

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Neden Microsoft bağlı Factory Çözüm Hızlandırıcısı için OPC UA 'yi seçmi?

Microsoft, açık, özel olmayan, platformdan bağımsız, sektörde tanınan ve kendini kanıtlamış bir standart olduğundan OPC UA 'yi seçti. Bu, geniş bir üretim işlemi ve ekipman kümesi arasında birlikte çalışabilirlik sağlayan Industrie 4,0 (RAMPAMı 4.0) başvuru mimarisi çözümleri için bir gereksinimdir. Microsoft, Industrie 4,0 çözümleri oluşturmak için müşterilerinin taleplerini görür. OPC UA desteği, müşterilerin hedeflerine ulaşmasını ve bunlara anında iş değeri sağlar.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Nasıl yaparım? simülasyon VM 'sine genel bir IP adresi eklensin mi?

IP adresini eklemek için iki seçeneğiniz vardır:

* [Depodaki](https://github.com/Azure/azure-iot-connected-factory)`Simulation/Factory/Add-SimulationPublicIp.ps1` PowerShell betiğini kullanın. Dağıtım adınızı parametre olarak geçirin. Yerel bir dağıtım için `<your username>ConnFactoryLocal`kullanın. Betik, sanal makinenin IP adresini yazdırır.

* Azure portal, dağıtımınızın kaynak grubunu bulun. Yerel dağıtım haricinde, kaynak grubunun çözüm veya dağıtım adı olarak belirttiğiniz adı vardır. Derleme betiği kullanılarak yerel bir dağıtım için, kaynak grubunun adı `<your username>ConnFactoryLocal`. Şimdi kaynak grubuna yeni bir **genel IP adresi** kaynağı ekleyin.

> [!NOTE]
> Her iki durumda da [Ubuntu Web sitesinde](https://wiki.ubuntu.com/Security/Upgrades)yer alan yönergeleri izleyerek en son düzeltme eklerini yüklediğinizden emin olun. VM 'nizin genel bir IP adresi üzerinden erişilebilir olduğu sürece yüklemeyi güncel tutun.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Nasıl yaparım?, genel IP adresini benzetim sanal makinesine kaldırmak istiyor musunuz?

IP adresini kaldırmak için iki seçeneğiniz vardır:

* [Deponun](https://github.com/Azure/azure-iot-connected-factory)PowerShell betiği simülasyon/Factory/Remove-SimulationPublicIp. ps1 öğesini kullanın. Dağıtım adınızı parametre olarak geçirin. Yerel bir dağıtım için `<your username>ConnFactoryLocal`kullanın. Betik, sanal makinenin IP adresini yazdırır.

* Azure portal, dağıtımınızın kaynak grubunu bulun. Yerel dağıtım haricinde, kaynak grubunun çözüm veya dağıtım adı olarak belirttiğiniz adı vardır. Derleme betiği kullanılarak yerel bir dağıtım için, kaynak grubunun adı `<your username>ConnFactoryLocal`. Bundan sonra **genel IP adresi** kaynağını kaynak grubundan kaldırın.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Benzetim sanal makinesinde oturum Nasıl yaparım? mı?

Simülasyon VM 'de oturum açmak yalnızca çözümünüzü [depodaki](https://github.com/Azure/azure-iot-connected-factory)`build.ps1` PowerShell betiği kullanarak dağıttıysanız desteklenir.

Çözümü www.azureiotsolutions.com adresinden dağıttıysanız, sanal makinede oturum açmanız gerekmez. Parola rastgele oluşturulduğundan ve sıfırlayamadığı için oturum açılamıyor.

1. VM 'ye bir genel IP adresi ekleyin. [BENZETIM sanal makinesine genel IP adresi eklemek nasıl yaparım? bakın mi?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. VM 'nize sanal makinenin IP adresini kullanarak bir SSH oturumu oluşturun.
1. Kullanılacak Kullanıcı adı: `docker`.
1. Kullanılacak parola, dağıtmak için kullandığınız sürüme bağlıdır:
    * Build. ps1 betiği kullanılarak dağıtılan çözümler 1 Haziran 2017 tarihinden önce, parola: `Passw0rd`.
    * 1 Haziran 2017 ' den sonra Build. ps1 betiği kullanılarak dağıtılan çözümler için `<name of your deployment>.config.user` dosyasında parolayı bulabilirsiniz. Parola **Vmadminpassword** ayarında depolanır. Parola, `build.ps1` betik parametresini kullanarak belirtmediğiniz takdirde dağıtım sırasında rastgele oluşturulur `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Nasıl yaparım? simülasyon VM 'deki tüm Docker süreçlerini durdurup başlatın mi?

1. Benzetim VM 'de oturum açın. Bkz [. BENZETIM VM 'de oturum nasıl yaparım? mi?](#how-do-i-sign-in-to-the-simulation-vm)
1. Hangi kapsayıcıların etkin olduğunu denetlemek için şunu çalıştırın: `docker ps`.
1. Tüm simülasyon kapsayıcılarını durdurmak için, şunu çalıştırın: `./stopsimulation`.
1. Tüm simülasyon kapsayıcılarını başlatmak için:
    * **IOTHUB_CONNECTIONSTRING**adıyla bir Shell değişkenini dışarı aktarın. `<name of your deployment>.config.user` dosyasında **ıothubownerconnectionstring** ayarının değerini kullanın. Örneğin:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * `./startsimulation` öğesini çalıştırın.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>VM 'deki benzetimi güncelleştirmek Nasıl yaparım? mı?

Simülasyonda herhangi bir değişiklik yaptıysanız, `updatedimulation` komutunu kullanarak [depodaki](https://github.com/Azure/azure-iot-connected-factory) `build.ps1` PowerShell betiğini kullanabilirsiniz. Bu betik tüm simülasyon bileşenlerini oluşturur, sanal makinenin benzetimini sonlandırır, yükler, yükler ve başlatır.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Çözümünüz tarafından kullanılan IoT Hub 'ına ait bağlantı dizesini mi Nasıl yaparım??

Çözümünüzü [depodaki](https://github.com/Azure/azure-iot-connected-factory)`build.ps1` betiğiyle dağıttıysanız bağlantı dizesi, `<name of your deployment>.config.user` dosyasında **ıothubownerconnectionstring** değeridir.

Azure portal kullanarak bağlantı dizesini de bulabilirsiniz. Dağıtımınızın kaynak grubundaki IoT Hub kaynağında, bağlantı dizesi ayarlarını bulun.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Bağlı fabrika simülasyonu hangi IoT Hub cihazları kullanır?

Simülasyon, aşağıdaki cihazları kaydeder:

* Proxy. Pekin. Corp. contoso
* Proxy. Capetown. Corp. contoso
* Proxy. Mumbai. Corp. contoso
* Proxy. munich0. Corp. contoso
* Proxy. RIO. Corp. contoso
* Proxy. Seattle. Corp. contoso
* Publisher. Pekin. Corp. contoso
* Publisher. Capetown. Corp. contoso
* Publisher. Mumbai. Corp. contoso
* Publisher. munich0. Corp. contoso
* Publisher. RIO. Corp. contoso
* Publisher. Seattle. Corp. contoso

[Azure CLI Için](https://github.com/Azure/azure-iot-cli-extension) [deviceexplorer 'ı](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) veya IoT uzantısı 'nı kullanarak, çözümünüzün kullandığı IoT Hub 'ına hangi cihazların kaydedildiğini kontrol edebilirsiniz. Cihaz Gezginini kullanmak için dağıtımınızdaki IoT Hub 'ı için bağlantı dizesine ihtiyacınız vardır. Azure CLı için IoT uzantısını kullanmak üzere IoT Hub adına ihtiyacınız vardır.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Simülasyon bileşenlerinden günlük verilerini nasıl alabilirim?

Simülasyon günlüğü içindeki tüm bileşenler günlük dosyalarına kaydedilir. Bu dosyalar, `home/docker/Logs`klasöründeki VM 'de bulunabilir. Günlükleri almak için, [depodaki](https://github.com/Azure/azure-iot-connected-factory)`Simulation/Factory/Get-SimulationLogs.ps1` PowerShell betiğini kullanabilirsiniz.

Bu betiğin VM 'de oturum açması gerekir. Oturum açma için kimlik bilgilerini sağlamanız gerekebilir. Kimlik bilgilerini bulmak için bkz. [BENZETIM VM 'de oturum açma nasıl yaparım?](#how-do-i-sign-in-to-the-simulation-vm) .

Betik, bir genel IP adresini henüz yoksa VM 'ye ekler/kaldırır ve kaldırır. Betik tüm günlük dosyalarını bir arşive koyar ve arşivi geliştirme iş istasyonunuza indirir.

Alternatif olarak, SSH aracılığıyla VM 'de oturum açın ve çalışma zamanında günlük dosyalarını inceleyin.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Simülasyonun buluta veri gönderip göndermesinin nasıl kontrol edebilirim?

[Deviceexplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) veya [Azure IoT CLI uzantısı izleyici-olayları](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) komutuyla, belirli cihazlardan IoT Hub gönderilen verileri inceleyebilirsiniz. Bu araçları kullanmak için, dağıtımınızdaki IoT Hub 'ı için bağlantı dizesini bilmeniz gerekir. [Çözümünüz tarafından kullanılan IoT Hub 'ı bağlantı dizesini öğrenmek nasıl yaparım? bakın mi?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Yayımcı cihazlarından biri tarafından gönderilen verileri inceleyin:

* Publisher. Pekin. Corp. contoso
* Publisher. Capetown. Corp. contoso
* Publisher. Mumbai. Corp. contoso
* Publisher. munich0. Corp. contoso
* Publisher. RIO. Corp. contoso
* Publisher. Seattle. Corp. contoso

IoT Hub gönderilen bir veri görürseniz simülasyonda bir sorun var. İlk analiz adımı olarak simülasyon bileşenlerinin günlük dosyalarını çözümlemeniz gerekir. Bkz [. benzetim bileşenlerinden günlük verilerini nasıl alabilirim?](#how-can-i-get-log-data-from-the-simulation-components) Ardından, benzetimi durdurup başlatmaya çalışın ve hala veri gönderilmezse benzetimi tamamen güncelleştirin. Bkz. [VM 'deki benzetimi güncelleştirmek nasıl yaparım? mi?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Bağlı fabrika çözümümüzde etkileşimli Haritayı etkinleştirmek Nasıl yaparım??

Bağlı fabrika çözümünüzde etkileşimli Haritayı etkinleştirmek için bir Azure haritalar hesabınızın olması gerekir.

Dağıtım işlemi, [www.azureiotsolutions.com](https://www.azureiotsolutions.com)'den dağıtıldığında, çözüm Hızlandırıcı hizmetlerini içeren kaynak grubuna bir Azure Maps hesabı ekler.

Bağlı fabrika GitHub deposunda `build.ps1` betiği kullanarak dağıtırken, ortam değişkenini derleme penceresinde [Azure haritalar hesabınızın anahtarına](../azure-maps/how-to-manage-account-keys.md)`$env:MapApiQueryKey` ayarlayın. Etkileşimli harita daha sonra otomatik olarak etkinleştirilir.

Ayrıca, dağıtımdan sonra çözüm hızlandırıcısına bir Azure haritalar hesap anahtarı ekleyebilirsiniz. Azure portal gidin ve bağlı fabrika dağıtımınızdaki App Service kaynağına erişin. Bölüm **uygulama ayarlarını**bulduğunuz **uygulama ayarları**' na gidin. **MapApiQueryKey** 'ı [Azure haritalar hesabınızın anahtarına](../azure-maps/how-to-manage-account-keys.md)ayarlayın. Ayarları kaydedin ve **genel bakış** ' a gidin ve App Service yeniden başlatın.

### <a name="how-do-i-create-an-azure-maps-account"></a>Nasıl yaparım? Azure Maps hesabı mı oluşturulsun?

Bkz. [Azure Maps hesabınızı ve anahtarlarınızı yönetme](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Azure haritalar hesap anahtarınızı edinme

Bkz. [Azure Maps hesabınızı ve anahtarlarınızı yönetme](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Yerel olarak hata ayıklarken etkileşimli Haritayı nasıl etkinleştireceğinizi?

Yerel olarak hata ayıklarken etkileşimli Haritayı etkinleştirmek için, `MapApiQueryKey` ayarın değerini `local.user.config` ve dağıtımınızın kökündeki `<yourdeploymentname>.user.config`, daha önce kopyaladığınız **queryKey** değerine ayarlayın.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Nasıl yaparım? panoumun ana sayfasında farklı bir görüntü kullanmak mı istiyorsunuz?

GÇ gösterilen statik görüntüyü değiştirmek için panonun ana sayfasında, görüntünün `WebApp\Content\img\world.jpg`değiştirin. Sonra WebApp 'i yeniden derleyin ve dağıtın.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Bağlı fabrika ile OPC UA cihazları kullanmak Nasıl yaparım? mı?

OPC UA cihazlarından bağlı fabrikaya telemetri verileri göndermek için:

1. `ContosoTopologyDescription.json` dosyasındaki [bağlı fabrika topolojisinde yeni bir Istasyon yapılandırın](iot-accelerators-connected-factory-configure.md) .

1. Bağlı fabrika uyumlu JSON biçiminde telemetri verilerini alma:

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. `<timestamp>` biçimi: `2017-12-08T19:24:51.886753Z`

1. Bağlı fabrika App Service yeniden başlatın.

### <a name="next-steps"></a>Sonraki adımlar

IoT çözüm hızlandırıcılarının diğer özellik ve yeteneklerinden bazılarını da keşfedebilirsiniz:

* [Tahmine Dayalı Bakım çözüm hızlandırıcısına genel bakış](iot-accelerators-predictive-overview.md)
* [Bağlı fabrika çözüm Hızlandırıcısını dağıtma](quickstart-connected-factory-deploy.md)
* [Sıfırdan IoT güvenliği](/azure/iot-fundamentals/iot-security-ground-up)
