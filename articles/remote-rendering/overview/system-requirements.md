---
title: Sistem Gereksinimleri
description: Azure uzaktan Işleme için sistem gereksinimlerini listeler
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.custom: references_regions
ms.openlocfilehash: 789233ce1ede751276f965143716694c6feca3ca
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105032811"
---
# <a name="system-requirements"></a>Sistem Gereksinimleri

Bu bölümde, *Azure uzaktan işleme* (ARR) ile çalışmak için en düşük sistem gereksinimleri listelenir.

## <a name="development-pc"></a>Geliştirme BILGISAYARı

* Windows 10 sürüm 1903 veya üzeri.
* Güncel grafik sürücüleri.
* İsteğe bağlı: uzaktan işlenmiş içeriğin yerel önizlemesini (örneğin Unity) kullanmak istiyorsanız, [h265 Hardware video kod çözücüsü](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7).

> [!IMPORTANT]
> Windows Update her zaman en son GPU sürücülerini teslim etmez, en son sürücüler için GPU üreticinizin Web sitesini kontrol edin:
>
> * [AMD Sürücüler](https://www.amd.com/en/support)
> * [Intel sürücüleri](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVıDıA sürücüleri](https://www.nvidia.com/Download/index.aspx)

Aşağıdaki tabloda, hangi GPU 'Ların h265 donanım video kodunu çözmesinin desteklediği listelenmiştir.

| GPU üreticisi | Desteklenen modeller |
|-----------|:-----------|
| NVIDIA | [Bu sayfanın en altındaki](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix) **NVDEC destek matrisini** kontrol edin. GPU 'larınızın **H. 265 4:2:0 8-bit** sütununda Yes olması gerekir. |
| AMD | En az AMD 'nin [Birleşik video kod çözücüsünün](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)6 sürümüne sahip GPU 'lar. |
| Intel | Ufuk Gölü ve daha yeni CPU 'Lar |

Doğru h265 codec bileşeni yüklenebilse de, codec dll 'Lerinde güvenlik özellikleri codec başlatma hatalarıyla karşılaşabilir. [Sorun giderme kılavuzu](../resources/troubleshoot.md#h265-codec-not-available) , bu sorunu çözme adımlarını açıklamaktadır. DLL sorunu yalnızca hizmet, Unity içindeki örneğin bir masaüstü uygulamasında kullanılırken olabilir.

## <a name="devices"></a>Cihazlar

Azure uzaktan Işleme Şu anda yalnızca bir hedef cihaz olarak **HoloLens 2** ve Windows Masaüstü 'nü desteklemektedir. [Platform sınırlamaları](../reference/limits.md#platform-limitations) bölümüne bakın.

Daha yeni sürümlerde gecikme süresi bakımından önemli iyileştirmeler olduğu için en son HEVC codec bileşeni kullanılması önemlidir. Cihazınızda hangi sürümün yüklü olduğunu denetlemek için:

1. **Microsoft Store** başlatın.
1. Sağ üst köşedeki **"..."** düğmesine tıklayın.
1. **İndirmeler ve Güncelleştirmeler '** i seçin.
1. **Cihaz üreticisinden HEVC video uzantıları** listesinde arama yapın. Bu öğe güncelleştirmeler altında listelenmiyorsa, en son sürüm zaten yüklüdür.
1. Listelenen codec bileşeninin en az sürüm **1.0.21821.0** olduğundan emin olun.
1. **Güncelleştirmeleri al** düğmesine tıklayın ve yüklemesinin tamamlanmasını bekleyin.

## <a name="network"></a>Ağ

İyi bir kullanıcı deneyimi için kararlı, düşük gecikmeli bir ağ bağlantısı kritik öneme sahiptir.

Bkz. [ağ gereksinimleri](../reference/network-requirements.md)için adanmış bölüm.

Ağ sorunlarını gidermek için [sorun giderme kılavuzuna](../resources/troubleshoot.md#unstable-holograms)bakın.

### <a name="network-firewall"></a>Ağ güvenlik duvarı

### <a name="sdk-version--0176"></a>SDK sürümü >= 0.1.76

Uzaktan Işleme sanal makineleri aşağıdaki IP aralıklarından Paylaşılan IP adreslerini kullanır:

| Name             | Region         | IP öneki         |
|------------------|:---------------|:------------------|
| Doğu Avustralya   | australiaeast  | 20.53.44.240/28   |
| Doğu ABD          | eastus         | 20.62.129.224/28  |
| Doğu ABD 2        | eastus2        | 20.49.103.240/28  |
| Doğu Japonya       | japaneast      | 20.191.165.112/28 |
| Kuzey Avrupa     | northeurope    | 52.146.133.64/28  |
| Orta Güney ABD | southcentralus | 20.65.132.80/28   |
| Güneydoğu Asya   | southeastasia  | 20.195.64.224/28  |
| Güney Birleşik Krallık         | uksouth        | 51.143.209.144/28 |
| West Europe      | westeurope     | 20.61.99.112/28   |
| Batı ABD 2        | westus2        | 20.51.9.64/28     |

Güvenlik duvarlarınızın (cihazda, yönlendiricilerin içinde, vb.) bu IP aralıklarını ve aşağıdaki bağlantı noktası aralıklarını engellemediğinden emin olun:

| Bağlantı noktası              | Protokol  | İzin Ver    |
|-------------------|---------- |----------|
| 49152-65534       | TCP/UDP | Tarafına |

#### <a name="sdk-version--0176"></a>SDK sürümü < 0.1.76

Güvenlik duvarlarınızın (cihazda, yönlendiricilerin içinde, vb.) aşağıdaki bağlantı noktalarını engellemediğinden emin olun:

| Bağlantı noktası              | Protokol | İzin Ver    | Description |
|-------------------|----------|----------|-------------|
| 50051             | TCP      | Tarafına | İlk bağlantı (HTTP el sıkışma) |
| 8266              | UDP      | Tarafına | Veri aktarımı |
| 5000, 5433, 8443  | TCP      | Tarafına | [ArrInspector aracı](../resources/tools/arr-inspector.md) için gerekli|


## <a name="software"></a>Yazılım

Aşağıdaki yazılım yüklü olmalıdır:

* **Visual Studio 2019** ' un en son sürümü [(indirme)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Karma gerçeklik Için Visual Studio Araçları](/windows/mixed-reality/install-the-tools). Özellikle, aşağıdaki *Iş yükü* yüklemeleri zorunludur:
  * **C++ ile masaüstü geliştirme**
  * **Evrensel Windows Platformu (UWP) geliştirme**
* **Windows SDK 10.0.18362.0** [(İndir)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [(İndir)](https://git-scm.com/downloads)
* İsteğe bağlı: bir masaüstü bılgısayar üzerindeki sunucudan video akışını görüntülemek Için **HEVC video uzantılarına** [(Microsoft Store bağlantı)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)ihtiyacınız vardır. Depodaki güncelleştirmeleri denetleyerek en son sürümün yüklü olduğundan emin olun.

## <a name="unity"></a>Unity

Unity ile geliştirme için, Unity 2019,3 veya 2019,4 LTS 'nin [(indirme)](https://unity3d.com/get-unity/download)güncel bir sürümünü yükleyin. Yüklemeleri yönetmek için Unity hub kullanmanızı öneririz.
Unity yüklemenize aşağıdaki modülleri eklediğinizden emin olun:
* **UWP** -Evrensel Windows platformu derleme desteği
* **IL2CPP** -Windows derleme DESTEĞI (IL2CPP)

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç: Unity ile model Işleme](../quickstarts/render-model.md)