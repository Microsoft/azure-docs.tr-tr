---
title: Azure Stack Edge Pro GPU cihazını dağıtmaya yönelik dağıtım denetim listesi | Microsoft Docs
description: Bu makalede, Azure Stack Edge Pro GPU cihazınızı dağıtmadan önce toplanacak bilgiler açıklanmaktadır.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/24/2021
ms.author: alkohli
ms.openlocfilehash: 13de2b024bf4541c6234dd6bfba601597de59434
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101716236"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU cihazınız için dağıtım denetim listesi  

Bu makalede, Azure Stack Edge Pro cihazınızın gerçek dağıtımının önüne toplanacak bilgiler açıklanmaktadır. 

Azure Stack Edge Pro cihazı için sipariş verdikten sonra ve cihazı almadan önce bu bilgilere sahip olduğunuzdan emin olmak için aşağıdaki denetim listesini kullanın. 

## <a name="deployment-checklist"></a>Dağıtım denetim listesi 

| Aşama                             | Parametre                                                                                                                                                                                                                           | Ayrıntılar                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Cihaz yönetimi               | <li>Azure aboneliği</li><li>Kaynak sağlayıcıları kaydedildi</li><li>Azure Storage hesabı</li>|<li>Azure Stack Edge Pro/Data Box Gateway, sahip veya katkıda bulunan erişim için etkinleştirildi.</li><li>Azure portal, **abonelik > kaynak sağlayıcılarına > giriş > abonelikleri**' ne gidin. Araması yapın `Microsoft.DataBoxEdge` ve kaydedin. `Microsoft.Devices`IoT iş yüklerini dağıtıyorsanız için tekrarlayın.</li><li>Erişim kimlik bilgilerine ihtiyacınız</li> |
| Cihaz yüklemesi               | Paketteki güç kabloları. <br>ABD için, 125 V için derecelendirilen bir SVE 18/3 kablosu ve NEMA 5-15P ile C13 (çıktı girişi) Bağlayıcısı ile 15 AMPS dağıtılır. | Daha fazla bilgi için [ülkeye göre desteklenen güç](azure-stack-edge-technical-specifications-power-cords-regional.md) 'lerin listesine bakın  |
|                                   | <li>Bağlantı noktası 1 için en az 1 X 1-GbE RJ-45 ağ kablosu  </li><li> Bağlantı noktası 3, bağlantı noktası 4, bağlantı noktası 5 veya bağlantı noktası 6 için en az 1 X 25-GbE SFP + bakır kablo</li>| Müşterinin bu kabloları sağlaması gerekir.<br>Cihaz ağ kartları için desteklenen ağ kabloları, anahtarlar ve alıcı vericiler tam listesi için bkz. [Cavium Fastlınq 41000 serisi birlikte çalışabilirlik matrisi](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) ve [Mellanox Dual Port 25G ConnectX-4 kanal ağ bağdaştırıcısı uyumlu ürünler](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).| 
| İlk kez cihaz bağlantısı      | <li>IPv4 ayarlarını değiştirilebilen dizüstü bilgisayar. Bu dizüstü bilgisayar bir anahtar veya USB-Ethernet bağdaştırıcısı aracılığıyla bağlantı noktası 1 ' e bağlanır.  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| Cihaz oturum açma                      | Aşağıdaki karakter türlerinden üçünü de içeren, 8 ila 16 karakter arasında cihaz yöneticisi parolası: büyük harf, küçük harf, sayısal ve özel karakterler.                                            | Varsayılan parola, ilk oturum açma sırasında süresi dolan *Parola1*'dir.                                                     |
| Ağ ayarları                  | Cihaz 2 x 1-GbE, 4 x 25-GbE ağ bağlantı noktalarıyla gelir. <li>Bağlantı noktası 1 yalnızca yönetim ayarlarını yapılandırmak için kullanılır. Bir veya daha fazla veri bağlantı noktası bağlanabilir ve yapılandırılabilir. </li><li> Bağlantı noktası 2-bağlantı noktası 6 arasındaki en az bir veri ağı arabiriminin Internet 'e bağlanması gerekir (Azure bağlantısı ile).</li><li> DHCP ve statik IPv4 yapılandırması desteklenir. | Statik IPv4 yapılandırması için IP, DNS sunucusu ve varsayılan ağ geçidi gerekir.   |
| İşlem ağ ayarları     | <li>, Kubernetes düğümleri için 2 boş, statik, bitişik IP ve IoT Edge hizmeti için 1 statik IP gerektirir.</li><li>Dağıtacağınız her ek hizmet veya modül için bir ek IP iste.</li>| Yalnızca statik IPv4 yapılandırması desteklenir.|
| Seçim Web proxy ayarları     | <li>Web proxy sunucusu IP/FQDN, bağlantı noktası </li><li>Web Proxy Kullanıcı adı, parola</li> |  |
| Güvenlik Duvarı ve bağlantı noktası ayarları        | Güvenlik duvarı kullanıyorsanız, [listelenen URL düzenlerine ve bağlantı noktalarına](azure-stack-edge-system-requirements.md#networking-port-requirements) cihaz IP 'leri için izin verildiğinden emin olun. |  |
| Önerilen Zaman ayarları       | Saat dilimini, birincil NTP sunucusunu, ikincil NTP sunucusunu yapılandırın. | Yerel ağ üzerinde birincil ve ikincil NTP sunucusunu yapılandırın.<br>Yerel sunucu kullanılabilir değilse, genel NTP sunucuları yapılandırılabilir.                                                    |
| Seçim Sunucu ayarlarını Güncelleştir | <li>Yerel ağ üzerinde güncelleştirme sunucusu IP adresi, WSUS sunucusu için yol gerektir. </li> | Varsayılan olarak, genel Windows Update sunucusu kullanılır.|
| Cihaz ayarları | <li>Cihaz tam etki alanı adı (FQDN) </li><li>DNS etki alanı</li> | |
| Seçim Sertifika  | Üretim dışı iş yüklerini test etmek için, [sertifika oluştur seçeneğini](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates) kullanın <br><br> İmza Zinciri de dahil olmak üzere kendi sertifikalarınızı getiruyorsanız, sertifikaları uygun biçimde [ekleyin](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) .| Yalnızca cihaz adını ve/veya DNS etki alanını değiştirirseniz sertifikaları yapılandırın. |
| Etkinleştirme  | Azure Stack Edge Pro/Data Box Gateway kaynağından etkinleştirme anahtarı gerektir.    | Oluşturulduktan sonra anahtarın 3 gün içinde süresi dolar. |

<!--
| (Optional) MAC Address            | If MAC address needs to be on the allowed list, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>Sonraki adımlar

[Azure Stack Edge Pro cihazınızı](azure-stack-edge-gpu-deploy-prep.md)dağıtmaya hazırlanın.
