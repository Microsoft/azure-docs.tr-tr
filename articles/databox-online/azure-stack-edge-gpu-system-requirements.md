---
title: Microsoft Azure Stack Edge sistem gereksinimleri | Microsoft Docs
description: Microsoft Azure Stack Edge çözümünüz ve Azure Stack Edge 'e bağlanan istemciler için sistem gereksinimleri hakkında bilgi edinin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alkohli
ms.custom: contperf-fy21q3
ms.openlocfilehash: b6984f27ae0ae81c3bf8d98efcce5fb23909c36d
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226099"
---
# <a name="system-requirements-for-azure-stack-edge-pro-with-gpu"></a>GPU ile Azure Stack Edge Pro için sistem gereksinimleri 

Bu makalede Microsoft Azure Stack Edge Pro GPU çözümünüz ve Azure Stack Edge Pro 'ya bağlanan istemciler için önemli sistem gereksinimleri açıklanmaktadır. Azure Stack Edge Pro 'Yu dağıtmadan önce bilgileri dikkatlice incelemenizi öneririz. Dağıtım ve sonraki işlemler sırasında gerektiğinde bu bilgilere geri başvurabilirsiniz.

Azure Stack Edge Pro için sistem gereksinimleri şunlardır:

- **Konaklar Için yazılım gereksinimleri** -desteklenen platformları, yerel yapılandırma kullanıcı arabirimi için TARAYıCıLARı, SMB istemcilerini ve cihaza erişen istemciler için ek gereksinimleri açıklar.
- **Cihaz Için ağ gereksinimleri** -fiziksel cihazın çalışması için tüm ağ gereksinimleri hakkında bilgi sağlar.

## <a name="supported-os-for-clients-connected-to-device"></a>Cihaza bağlı istemciler için desteklenen işletim sistemi

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Cihaza erişen istemciler için desteklenen protokoller

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-azure-storage-accounts"></a>Desteklenen Azure depolama hesapları

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-edge-storage-accounts"></a>Desteklenen Edge depolama hesapları

Aşağıdaki uç depolama hesapları, cihazın REST arabirimiyle desteklenir. Uç depolama hesapları cihazda oluşturulur. Daha fazla bilgi için bkz. [Edge depolama hesapları](azure-stack-edge-gpu-manage-storage-accounts.md#about-edge-storage-accounts).

|Tür  |Depolama hesabı  |Yorumlar  |
|---------|---------|---------|
|Standart     |GPv1: Blok Blobu         |         |

* Sayfa Blobları ve Azure dosyaları şu anda desteklenmiyor.

## <a name="supported-local-azure-resource-manager-storage-accounts"></a>Desteklenen yerel Azure Resource Manager depolama hesapları

Bu depolama hesapları, yerel Azure Resource Manager bağlanırken cihaz yerel API 'Leri aracılığıyla oluşturulur. Aşağıdaki depolama hesapları desteklenir:

|Tür  |Depolama hesabı  |Yorumlar  |
|---------|---------|---------|
|Standart     |GPv1: Blok Blobu, Sayfa Blobu        | SKU türü Standard_LRS       |
|Premium     |GPv1: Blok Blobu, Sayfa Blobu        | SKU türü Premium_LRS        |


## <a name="supported-storage-types"></a>Desteklenen depolama türleri

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>Yerel Web Kullanıcı arabirimi için desteklenen tarayıcılar

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Ağ bağlantı noktası gereksinimleri

### <a name="port-requirements-for-azure-stack-edge-pro"></a>Azure Stack Edge Pro için bağlantı noktası gereksinimleri

Aşağıdaki tabloda SMB, bulut veya Yönetim trafiğine izin vermek için güvenlik duvarınızda açılması gereken bağlantı noktaları listelenmektedir. Bu tabloda, veya *gelen* *içinde* , gelen istemci, cihazınıza erişim talep ettiği yöne başvurur. *Out* veya *Outbound* , Azure Stack Edge Pro cihazınızın verileri dışarıdan gönderdiği yönü (örneğin, internet 'e giden) ifade eder.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

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

[!INCLUDE [URL patterns for firewall](../../includes/azure-stack-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>İşlem özelliği için URL desenleri

| URL kalıbı                      | Bileşen veya işlevsellik                     |   
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https:// \* . CDN.mscr.io | Microsoft Container Registry (gerekli)               |
| https:// \* . azurecr.io                     | Kişisel ve üçüncü taraf kapsayıcı kayıt defterleri (isteğe bağlı) | 
| https:// \* . Azure-Devices.net              | IoT Hub erişimi (gerekli)                             | 

### <a name="url-patterns-for-monitoring"></a>İzleme için URL desenleri

Linux için Log Analytics aracısının Kapsayıcılı sürümünü kullanıyorsanız Azure Izleyici için aşağıdaki URL düzenlerini ekleyin.

| URL kalıbı | Bağlantı noktası | Bileşen veya işlevsellik |
|-------------|-------------|----------------------------|
| http:// \* ods.opinsights.Azure.com | 443 | Veri alımı |
| http:// \* . OMS.opinsights.Azure.com | 443 | Operations Management Suite (OMS) ekleme |
| http:// \* . DC.Services.VisualStudio.com | 443 | Azure genel bulut Application Insights kullanan aracı telemetrisi |

Daha fazla bilgi için bkz. [kapsayıcı öngörülerini izlemek Için ağ güvenlik duvarı gereksinimleri](../azure-monitor/containers/container-insights-onboard.md#network-firewall-requirements).

### <a name="url-patterns-for-gateway-for-azure-government"></a>Azure Kamu için ağ geçidi için URL desenleri

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/azure-stack-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Azure Kamu için işlem URL desenleri

| URL kalıbı                      | Bileşen veya işlevsellik                     |  
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https:// \* . CDN.mscr.com | Microsoft Container Registry (gerekli)               |
| https:// \* . Azure-Devices.us              | IoT Hub erişimi (gerekli)           |
| https:// \* . azurecr.us                    | Kişisel ve üçüncü taraf kapsayıcı kayıt defterleri (isteğe bağlı) | 

### <a name="url-patterns-for-monitoring-for-azure-government"></a>Azure Kamu için izleme için URL desenleri

Linux için Log Analytics aracısının Kapsayıcılı sürümünü kullanıyorsanız Azure Izleyici için aşağıdaki URL düzenlerini ekleyin.

| URL kalıbı | Bağlantı noktası | Bileşen veya işlevsellik |
|-------------|-------------|----------------------------|
| http:// \* ods.opinsights.Azure.us | 443 | Veri alımı |
| http:// \* . OMS.opinsights.Azure.us | 443 | Operations Management Suite (OMS) ekleme |
| http:// \* . DC.Services.VisualStudio.com | 443 | Azure genel bulut Application Insights kullanan aracı telemetrisi |


## <a name="internet-bandwidth"></a>Internet bant genişliği

[!INCLUDE [Internet bandwidth](../../includes/azure-stack-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>İşlem boyutlandırma değerlendirmeleri

Azure Stack Edge Pro cihazınızda yeterli kapasiteye sahip olduğunuzdan emin olmak için Çözümünüzü geliştirirken ve test ederken deneyiminizi kullanın ve cihazınızdan en iyi performansı elde edin.

Dikkate almanız gereken faktörler şunlardır:

- **Kapsayıcı özellikleri** -aşağıdakiler hakkında düşünün.

    - Kapsayıcının parmak izi nedir? Kapsayıcınız ne kadar bellek, depolama alanı ve CPU kullanıyor?
    - İş yükünüzün kaç tane kapsayıcısı var? Birçok basit kapsayıcıyla ve çok sayıda yoğun kaynak kullanımına sahip olabilirsiniz.
    - Bu kapsayıcılara ayrılan kaynaklar, tükettiği kaynaklar (parmak izi) ile aynıdır.
    - Kapsayıcılarınız kaç katman paylaşır? Kapsayıcı görüntüleri bir katman yığınında düzenlenmiş bir dosya paketleridir. Kapsayıcı görüntünüz için, kaynak tüketimini hesaplamak için kaç katmanın ve ilgili boyutların kullanılacağını saptayın.
    - Kullanılmayan kapsayıcılar var mı? Durdurulmuş bir kapsayıcı yine de disk alanı kaplar.
    - Kapsayıcınız hangi dilde yazılmıştır?
- **İşlenen verilerin boyutu** -kapsayıcılarınızın ne kadar veri işlemesi gerekir? Bu veriler disk alanını tüketir veya veriler bellekte işlenir mi?
- **Beklenen performans** -çözümünüzün istenen performans özellikleri nelerdir? 

Çözümünüzün performansını anlamak ve iyileştirmek için şunu kullanabilirsiniz:

- Azure portal kullanılabilir işlem ölçümleri. Azure Stack Edge kaynağına giderek **izleme > ölçümleri**' ne gidin. Kullanılabilir kaynakları ve kaynakların nasıl tüketiğini anlamak için **Edge hesaplama-bellek kullanımı** ve **Edge hesaplama-yüzde CPU** bölümüne bakın.
- İşlem modüllerini izlemek ve sorunlarını gidermek için [Kubernetes sorunlarını ayıklama](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge)sayfasına gidin.

Son olarak, çözümünüzü veri kümeniz üzerinde doğruladığınızdan ve üretimde dağıtım yapmadan önce Azure Stack Edge Pro performansını belirttiğinizden emin olun.

## <a name="next-step"></a>Sonraki adım

- [Azure Stack Edge Pro 'Yu dağıtma](azure-stack-edge-gpu-deploy-prep.md)