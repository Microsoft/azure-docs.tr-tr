---
title: Azure IoT Edge nedir | Microsoft Docs
description: Azure IoT Edge hizmetine genel bakış
author: kgremban
manager: philmea
ms.reviewer: chipalost
ms.service: iot-edge
services: iot-edge
ms.topic: overview
ms.date: 10/28/2019
ms.author: kgremban
ms.custom: mvc
ms.openlocfilehash: b9b768d7af276b8bff0db2acd00d27eca03023c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026743"
---
# <a name="what-is-azure-iot-edge"></a>Azure IoT Edge nedir?

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge bulut analizini ve özel iş mantığını cihazlara taşıyarak kuruluşunuzun veri yönetimi yerine iş öngörülerine odaklanmasını sağlar. İş mantığınızı standart kapsayıcılara paketleyerek IoT çözümünüzü ölçeklendirin, bu kapsayıcıları cihazlarınızdan birine dağıtabilir ve tüm buluttan izleyebilirsiniz.

Analizler IoT çözümlerinin iş değerini artırır, aman tüm analizlerin bulutta olması gerekmez. Acil durum hızını mümkün olduğunca hızlı bir şekilde yanıtlamak isterseniz anomali algılama iş yüklerini kenarda çalıştırabilirsiniz. Bant genişliği maliyetlerini azaltmak ve terabaytlarca ham verilerin aktarılmasını önlemek istiyorsanız, verileri yerel olarak temizleyebilir ve toplayabilir, ardından yalnızca analiz için öngörüleri buluta gönderebilirsiniz.

Azure IoT Edge üç bileşenden oluşur:

* **IoT Edge modüller** , Azure Hizmetleri, üçüncü taraf hizmetleri veya kendi kodunuzu çalıştıran kapsayıcılardır. Modüller IoT Edge cihazlarına dağıtılır ve bu cihazlarda yerel olarak yürütülür.
* **IoT Edge çalışma zamanı** her bir IoT Edge cihazında çalışır ve her cihaza dağıtılan modülleri yönetir.
* **Bulut tabanlı bir arabirim** , IoT Edge cihazları uzaktan izlemenize ve yönetmenize olanak sağlar.

>[!NOTE]
>Azure IoT Edge, IoT Hub’ın ücretsiz ve standart katmanında kullanılabilir. Ücretsiz katman yalnızca test etme ve değerlendirme içindir. Temel ve standart katmanlar hakkında daha fazla bilgi için bkz. [Doğru IoT Hub katmanını seçme](../iot-hub/iot-hub-scaling.md).

## <a name="iot-edge-modules"></a>IoT Edge modülleri

IoT Edge modüller, iş mantığınızı kenarda çalıştıran, Docker uyumlu kapsayıcılar olarak uygulanan yürütme birimleridir. Birbiriyle iletişim kuracak birden çok modül yapılandırabilir ve böylelikle veri işleme için bir işlem hattı oluşturabilirsiniz. Çevrimdışı ve uçta öngörüler sağlayan kendi modüllerinizi geliştirebilir veya bazı Azure hizmetlerini modüller halinde paketleyebilirsiniz.

### <a name="artificial-intelligence-at-the-edge"></a>Kenarda yapay zeka

Azure IoT Edge, şirket içinde yazmadan karmaşık olay işleme, makine öğrenimi, görüntü tanıma ve diğer yüksek değerli AI dağıtmanızı sağlar. Azure Işlevleri, Azure Stream Analytics ve Azure Machine Learning gibi Azure Hizmetleri, Azure IoT Edge aracılığıyla şirket içinde çalıştırılabilir. Ancak Azure hizmetleriyle sınırlı değilsiniz. Herkes, AI modülleri oluşturabilir ve bunları Azure Marketi aracılığıyla kullanılmak üzere topluluk için kullanılabilir hale getirir.

### <a name="bring-your-own-code"></a>Kendi kodunuzu getirin

Cihazlarınıza kendi kodunuzu dağıtmak istediğinizde, Azure IoT Edge bunu da destekler. Azure IoT Edge, diğer Azure IoT hizmetleriyle alnı programlama modeline sahiptir. Aynı kodu bir cihazda veya bulutta çalıştırabilirsiniz. Azure IoT Edge hem Linux'ı hem de Windows'u desteklediğinden, kendi seçtiğiniz platform için kod yazabilirsiniz. Geliştiricilerinizin zaten bildiğiniz bir dilde kodlamasına ve mevcut iş mantığını kullanmasına olanak sağlamak için Java, .NET Core 2,0, Node.js, C ve Python 'yi destekler.

## <a name="iot-edge-runtime"></a>IoT Edge çalışma zamanı

Azure IoT Edge çalışma zamanı, IoT Edge cihazlarında özel mantığa ve bulut mantığına olanak tanır. Çalışma zamanı IoT Edge cihazda bulunur ve yönetim ve iletişim işlemleri gerçekleştirir. Çalışma zamanı çeşitli işlevler gerçekleştirir:

* Cihazdaki iş yüklerini yükleyip güncelleştirir.
* Cihazda Azure IoT Edge güvenlik standartlarını korur.
* IoT Edge modüllerinin her zaman çalıştırılmasını güvence altına alır.
* Uzaktan izleme için modül durumunu buluta bildirir.
* , Bir IoT Edge cihazdaki modüller arasında ve bir IoT Edge cihaz ile bulut arasında aşağı akış yaprak cihazları ve bir IoT Edge cihazı arasındaki iletişimi yönetir.

![IoT Edge çalışma zamanı öngörüleri ve raporları IoT Hub'ına gönderir](./media/about-iot-edge/runtime.png)

Azure IoT Edge bir cihazı nasıl kullanacağınızı öğrenin. Çalışma zamanı genellikle, diğer şirket içi cihazlardan gelen verileri toplayan ve işleyen ağ geçidi cihazlarına AI dağıtmak için kullanılır, ancak bu dağıtım modeli yalnızca bir seçenektir.

Azure IoT Edge çalışma zamanı, çok çeşitli yollarla kullanılmasına izin veren büyük bir IoT cihazları kümesi üzerinde çalışır. Hem Linux hem de Windows işletim sistemlerini destekler ve donanım ayrıntılarını soyutlar. Çok fazla veri işlemediğiniz veya Kaynak yoğunluklu iş yüklerini çalıştırmak için bir endüstriyel sunucu kullanarak, bir Raspberry PI 3 ' ten daha küçük bir cihaz kullanın.

## <a name="iot-edge-cloud-interface"></a>IoT Edge bulut arabirimi

Genellikle farklı hale getirir ve modeller veya coğrafi olarak dağınık olan Milyonlarca IoT cihazı için yazılım yaşam döngüsünü yönetmek zordur. Belirli bir cihaz türü için iş yüklerinin oluşturulması ve yapılandırılması, tüm cihazlarınıza dağıtılması ve hatalı davranan cihazları yakalamak için izleme yapılması gerekir. Bu işlemler tek tek cihazlar temelinde yapılamaz, belirli bir ölçekte yapılmalıdır.

Azure IoT Edge, Azure IoT çözüm hızlandırıcıları ile rahatça tümleştirildiğinden, çözümünüzün gereksinimlerini karşılayacak tek bir denetim düzlemi sağlar. Cloud Services şunları yapmanıza olanak sağlar:

* Belirli bir tür cihaz üzerinde çalıştırılacak bir iş yükü oluşturabilir ve yapılandırılabilir.
* İş yükünü bir dizi cihaza gönderebilir.
* Sahadaki cihazlarda çalıştırılan iş yüklerini izleyebilir.

![Cihaz telemetrisi ve eylemleri bulutla birlikte düzenlenir](./media/about-iot-edge/cloud-interface.png)

## <a name="next-steps"></a>Sonraki adımlar

İlk IoT Edge modülünüzü bir cihaza dağıtarak bu kavramları deneyin:

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

* [Bir Linux IoT Edge cihaza modül dağıtma](quickstart-linux.md)
* [Windows IoT Edge cihazına modül dağıtma](quickstart.md)

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

[IoT Edge cihaza modül dağıtma](quickstart-linux.md)

:::moniker-end
