---
title: Microsoft Azure Stack Edge Pro sistem gereksinimleri | Microsoft Docs
description: Azure Stack Edge Pro için yazılım ve ağ gereksinimleri hakkında bilgi edinin
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/05/2021
ms.author: alkohli
ms.openlocfilehash: c87c8cfd40903f6296f0a36f5c83c0b5c5cb7818
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831590"
---
# <a name="azure-stack-edge-pro-system-requirements"></a>Azure Stack Edge Pro sistem gereksinimleri

Bu makalede Microsoft Azure Stack Edge Pro çözümünüz ve Azure Stack Edge Pro 'ya bağlanan istemciler için önemli sistem gereksinimleri açıklanmaktadır. Azure Stack Edge Pro 'Yu dağıtmadan önce bilgileri dikkatlice incelemenizi öneririz. Dağıtım ve sonraki işlemler sırasında gerektiğinde bu bilgilere geri başvurabilirsiniz.

Azure Stack Edge Pro için sistem gereksinimleri şunlardır: 

- **Konaklar Için yazılım gereksinimleri** -desteklenen platformları, yerel yapılandırma kullanıcı arabirimi için TARAYıCıLARı, SMB istemcilerini ve cihaza erişen istemciler için ek gereksinimleri açıklar.
- **Cihaz Için ağ gereksinimleri** -fiziksel cihazın çalışması için tüm ağ gereksinimleri hakkında bilgi sağlar.

## <a name="supported-os-for-clients-connected-to-device"></a>Cihaza bağlı istemciler için desteklenen işletim sistemi

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Cihaza erişen istemciler için desteklenen protokoller

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Desteklenen depolama hesapları

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Desteklenen depolama türleri

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Yerel Web Kullanıcı arabirimi için desteklenen tarayıcılar

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Ağ bağlantı noktası gereksinimleri

### <a name="port-requirements-for-azure-stack-edge-pro"></a>Azure Stack Edge Pro için bağlantı noktası gereksinimleri

Aşağıdaki tabloda SMB, bulut veya Yönetim trafiğine izin vermek için güvenlik duvarınızda açılması gereken bağlantı noktaları listelenmektedir. Bu tabloda, veya *gelen* *içinde* , gelen istemci, cihazınıza erişim talep ettiği yöne başvurur. *Out* veya *Outbound* , Azure Stack Edge Pro cihazınızın verileri dışarıdan gönderdiği yönü (örneğin, internet 'e giden) ifade eder.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>IoT Edge için bağlantı noktası gereksinimleri

Azure IoT Edge, desteklenen IoT Hub protokolleri kullanılarak şirket içi sınır cihazından Azure bulutuna giden iletişimin yapılmasına izin verir. Gelen iletişim yalnızca Azure IoT Hub 'nin iletileri Azure IoT Edge cihazına (örneğin, buluttan cihaza mesajlaşma 'Ya) gönderilmesi gereken belirli senaryolar için gereklidir.

Azure IoT Edge çalışma zamanını barındıran sunucular için bağlantı noktası yapılandırması için aşağıdaki tabloyu kullanın:

| Bağlantı noktası No. | Dışarı veya dışarı | Bağlantı noktası kapsamı | Gerekli | Rehber |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Out       | WAN        | Yes      | IoT Edge sağlama için giden açık. El ile betik veya Azure IoT cihaz sağlama hizmeti (DPS) kullanılırken bu yapılandırma gereklidir.|

Tüm bilgiler için [IoT Edge dağıtımı Için güvenlik duvarı ve bağlantı noktası yapılandırma kuralları](../iot-edge/troubleshoot.md)' na gidin.

## <a name="url-patterns-for-firewall-rules"></a>Güvenlik duvarı kuralları için URL desenleri

Ağ yöneticileri, genellikle gelen ve giden trafiği filtrelemek için URL desenlerine göre gelişmiş güvenlik duvarı kuralları yapılandırabilir. Azure Stack Edge Pro cihazınız ve hizmet, Azure Service Bus, Azure Active Directory Access Control, depolama hesapları ve Microsoft Update sunucuları gibi diğer Microsoft uygulamalarına bağımlıdır. Bu uygulamalarla ilişkili URL desenleri güvenlik duvarı kurallarını yapılandırmak için kullanılabilir. Bu uygulamalarla ilişkili URL desenlerinin değiştirebileceğini anlamak önemlidir. Bu değişiklikler, ağ yöneticisinin Azure Stack Edge Pro ve gerektiğinde güvenlik duvarı kurallarını izlemesini ve güncelleştirmesini gerektirir.

Çoğu durumda serbest olan Azure Stack Edge Pro sabit IP adreslerine bağlı olarak, giden trafiğe yönelik güvenlik duvarı kurallarınızı ayarlamanızı öneririz. Bununla birlikte, güvenli ortamlar oluşturmak için gerekli olan gelişmiş güvenlik duvarı kurallarını ayarlamak için aşağıdaki bilgileri kullanabilirsiniz.

> [!NOTE]
> - Cihaz (kaynak) IP 'Leri her zaman bulut özellikli tüm ağ arabirimlerine ayarlanmalıdır.
> - Hedef IP 'Ler, [Azure veri MERKEZI IP aralıkları](https://www.microsoft.com/download/confirmation.aspx?id=41653)olarak ayarlanmalıdır.

### <a name="url-patterns-for-gateway-feature"></a>Ağ geçidi özelliği için URL desenleri

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>İşlem özelliği için URL desenleri

| URL kalıbı                      | Bileşen veya işlevsellik                     |   
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https:// \* . CDN.mscr.io | Microsoft Container Registry (gerekli)               |
| https:// \* . azurecr.io                     | Kişisel ve üçüncü taraf kapsayıcı kayıt defterleri (isteğe bağlı) | 
| https:// \* . Azure-Devices.net              | IoT Hub erişimi (gerekli)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Azure Kamu için ağ geçidi için URL desenleri

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Azure Kamu için işlem URL desenleri

| URL kalıbı                      | Bileşen veya işlevsellik                     |  
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https:// \* . CDN.mscr.com | Microsoft Container Registry (gerekli)               |
| https:// \* . Azure-Devices.us              | IoT Hub erişimi (gerekli)           |
| https:// \* . azurecr.us                    | Kişisel ve üçüncü taraf kapsayıcı kayıt defterleri (isteğe bağlı) | 

## <a name="internet-bandwidth"></a>Internet bant genişliği

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>İşlem boyutlandırma değerlendirmeleri

Azure Stack Edge Pro cihazınızda yeterli kapasiteye sahip olduğunuzdan emin olmak için Çözümünüzü geliştirirken ve test ederken deneyiminizi kullanın ve cihazınızdan en iyi performansı elde edin.

Dikkate almanız gereken faktörler şunlardır:

- **Kapsayıcı özellikleri** -aşağıdakiler hakkında düşünün.

    - İş yükünüzün kaç tane kapsayıcısı var? Birçok basit kapsayıcıyla ve çok sayıda yoğun kaynak kullanımına sahip olabilirsiniz.
    - Bu kapsayıcılara ayrılan kaynaklar, tükettiği kaynaklar ile karşılaştırıldığında nedir?
    - Kapsayıcılarınız kaç katman paylaşır?
    - Kullanılmayan kapsayıcılar var mı? Durdurulmuş bir kapsayıcı yine de disk alanı kaplar.
    - Kapsayıcınız hangi dilde yazılmıştır?
- **İşlenen verilerin boyutu** -kapsayıcılarınızın ne kadar veri işlemesi gerekir? Bu veriler disk alanını tüketir veya veriler bellekte işlenir mi?
- **Beklenen performans** -çözümünüzün istenen performans özellikleri nelerdir? 

Çözümünüzün performansını anlamak ve iyileştirmek için şunu kullanabilirsiniz:

- Azure portal kullanılabilir işlem ölçümleri. Azure Stack Edge kaynağına giderek **izleme > ölçümleri**' ne gidin. Kullanılabilir kaynakları ve kaynakların nasıl tüketiğini anlamak için **Edge hesaplama-bellek kullanımı** ve **Edge hesaplama-yüzde CPU** bölümüne bakın.
- Şu şekilde cihazın PowerShell arabirimi aracılığıyla kullanılabilen izleme komutları:

    - `dkr` kapsayıcı kaynak kullanımı istatistiklerinin canlı akışını almak için istatistikler. Komut CPU, bellek kullanımı, bellek sınırı ve ağ GÇ ölçümlerini destekler.
    - `dkr system df` kullanılan disk alanı miktarı hakkında bilgi almak için. 
    - `dkr image [prune]` kullanılmayan görüntüleri temizlemek ve boş alan boşaltmak için.
    - `dkr ps --size` çalışan bir kapsayıcının yaklaşık boyutunu görüntülemek için. 

    Kullanılabilir komutlar hakkında daha fazla bilgi için, [izleme ve sorun giderme işlem modülleri](azure-stack-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules)bölümüne gidin.

Son olarak, çözümünüzü veri kümeniz üzerinde doğruladığınızdan ve üretimde dağıtım yapmadan önce Azure Stack Edge Pro performansını belirttiğinizden emin olun.


## <a name="next-step"></a>Sonraki adım

- [Azure Stack Edge Pro 'Yu dağıtma](azure-stack-edge-deploy-prep.md)