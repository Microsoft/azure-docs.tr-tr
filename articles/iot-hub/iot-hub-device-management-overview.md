---
title: Azure IoT Hub cihaz yönetimine genel bakış | Microsoft Docs
description: Azure IoT hu 'de cihaz yönetimine genel bakış--kurumsal cihaz yaşam döngüsü ve, yeniden başlatma, fabrika sıfırlaması, üretici yazılımı güncelleştirmesi, yapılandırma, cihaz TWINS, sorgular, işler gibi cihaz yönetimi desenleri.
author: bzurcher
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 9ff8ebeaf7c4a219bc403c81dfea62f4657a1fd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148349"
---
# <a name="overview-of-device-management-with-iot-hub"></a>IoT Hub ile cihaz yönetimine genel bakış

Azure IoT Hub, cihaz ve arka uç geliştiricilerinin güçlü cihaz yönetimi çözümleri oluşturmasını sağlayan özellikler ve bir genişletilebilirlik modeli sunar. Cihazlar, kısıtlı algılayıcılardan tek amaçlı mikro denetleyicilere ve cihaz grupları için iletişimi yönlendiren güçlü ağ geçitlerine varan çeşitler barındırır.  Ayrıca, kullanım örnekleri ve IoT operatörlerinin gereksinimleri sektörler arasında farklılık gösterir.  Bu farklılığa rağmen, IoT Hub ile cihaz yönetimi çok çeşitli cihaz ve son kullanıcılara uygun özellikler, desenler ve kod kitaplıkları sağlar.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Başarılı bir kurumsal IoT çözümü oluşturmanın önemli bir kısmı, operatörlerin cihaz koleksiyonu için devam eden yönetimi nasıl gerçekleştirdiğine ilişkin bir strateji sağlanmasıdır. IoT operatörleri, işlerinin daha stratejik yönlerine odaklanmalarını sağlayan basit ve güvenilir araç ve uygulamalar gerektirir. Bu makalede aşağıdakiler sunulmaktadır:

* Azure IoT Hub cihaz yönetimi yaklaşımına kısa bir genel bakış.
* Genel cihaz yönetimi ilkelerinin açıklaması.
* Cihaz yaşam döngüsü açıklaması.
* Genel cihaz yönetimi desenlerine genel bakış.

## <a name="device-management-principles"></a>Cihaz yönetimi ilkeleri

IoT bir dizi benzersiz cihaz yönetimi zorluğunu beraberinde getirir ve kurumsal sınıftaki her çözüm aşağıdaki ilkeleri hesaba katmalıdır:

![Cihaz yönetimi ilkeleri grafiği](media/iot-hub-device-management-overview/image4.png)

* **Ölçek ve otomasyon**: IoT çözümleri, rutin görevleri otomatik hale getirebilen ve oldukça küçük bir operasyon ekibinin milyonlarca cihazı yönetmesine olanak tanıyabilen basit araçlar gerektirir. Operatörler toplu cihaz işlemlerini günlük olarak uzaktan gerçekleştirmeyi ve yalnızca doğrudan dikkat gerektiren sorunlar oluştuğunda uyarılmayı beklemektedir.

* **Açıklık ve uyumluluk**: Cihaz ekosistemi olağanüstü çeşitliliğe sahiptir. Yönetim araçları çok sayıda cihaz sınıfına, platforma ve protokole uyum sağlayacak şekilde uyarlanmalıdır. Operatörler en kısıtlı katıştırılmış tek işlemli yongalardan güçlü ve tam işlevsel bilgisayarlara kadar çok sayıda cihaz türünü destekleyebilmelidir.

* **Bağlam tanıma**: IoT ortamları dinamik ve sürekli değişen yapıdadır. Hizmet güvenilirliği üst düzey öneme sahiptir. Cihaz yönetimi işlemleri bakım kapalı kalma süresinin kritik iş işlemlerini etkilemediğinden veya tehlikeli koşullar oluşturmadığından emin olmak için aşağıdaki faktörleri göz önünde bulundurmalıdır:

    * SLA bakım pencereleri
    * Ağ ve güç durumları
    * Kullanım koşulları
    * Cihaz coğrafi konumu

* **Çok sayıda servis rolü**: Benzersiz iş akışları ve IoT işlemleri için destek çok önemlidir. Operasyon personeli, şirket içi BT bölümlerinin belirtilen kısıtlamalarıyla uyumlu bir şekilde çalışmalıdır.  Ayrıca, denetçilere ve diğer iş yönetimi rollerine gerçek zamanlı cihaz çalışma bilgilerini sunmanın uygun yollarını bulmalıdır.

## <a name="device-lifecycle"></a>Cihaz yaşam döngüsü
Tüm kurumsal IoT projelerinde ortak olan genel cihaz yönetimi aşamaları vardır. Azure IoT içinde, cihaz yaşam döngüsünün beş aşaması vardır:

![Beş Azure IoT cihaz yaşam döngüsü aşaması şunlardır: planlama, sağlama, yapılandırma, izleme, devre dışı bırakma](./media/iot-hub-device-management-overview/image5.png)

Bu beş aşamanın her birinde, tam bir çözüm sağlamak için yerine getirilmesi gereken birkaç cihaz operatörü gereksinimi vardır:

* **Plan**: Operatörlerin toplu yönetim işlemleri için bir cihaz grubunu kolayca ve doğru bir şekilde sorgulamasına ve hedeflemesine olanak tanıyan cihaz meta verileri düzeni oluşturmasını sağlar. Bu cihaz meta verilerini etiketler ve özellikler halinde depolamak için cihaz ikizlerini kullanabilirsiniz.
  
    *Daha fazla okuma*: 
    * [Cihaz ikizlerini kullanmaya başlama](iot-hub-node-node-twin-getstarted.md)
    * [Cihaz ikizlerini anlama](iot-hub-devguide-device-twins.md)
    * [Cihaz ikizi özelliklerini kullanma](tutorial-device-twins.md)
    * [IoT çözümünde cihaz yapılandırması için en iyi uygulamalar](iot-hub-configuration-best-practices.md)

* **Sağlama**: IoT Hub’ına yeni cihazları güvenli bir şekilde sağlar ve operatörlerin cihaz özelliklerini hemen bulmasına olanak tanır.  Esnek cihaz kimlikleri ve kimlik bilgileri oluşturmanın yanı sıra bu işlemi bir iş kullanarak toplu halde gerçekleştirmek için IoT Hub kimlik kayıt defterini kullanın. Cihaz ikizindeki cihaz özellikleri aracılığıyla kapasite ve koşullarını raporlamak için cihazlar oluşturun.
  
    *Daha fazla okuma*: 
    * [Cihaz kimliklerini yönetme](iot-hub-devguide-identity-registry.md)
    * [Cihaz kimliklerinin toplu yönetimi](iot-hub-bulk-identity-mgmt.md)
    * [Cihaz ikizi özelliklerini kullanma](tutorial-device-twins.md)
    * [IoT çözümünde cihaz yapılandırması için en iyi uygulamalar](iot-hub-configuration-best-practices.md)
    * [Azure IoT Hub Cihazı Sağlama Hizmeti](https://azure.microsoft.com/documentation/services/iot-dps)

* **Yapılandırma**: Cihazların hem sistem durumunu hem de güvenliğini korurken toplu yapılandırma değişikliklerini ve üretici yazılımı güncelleştirmelerini kolaylaştırır. İstediğiniz özellikleri kullanarak ve doğrudan yöntemler ve yayın işleri ile bu cihaz yönetimi işlemlerini toplu olarak gerçekleştirin.
  
    *Daha fazla okuma*:
    * [Cihaz ikizi özelliklerini kullanma](tutorial-device-twins.md)
    * [IoT cihazlarını ölçekli olarak yapılandırma ve izleme](./iot-hub-automatic-device-management.md)
    * [IoT çözümünde cihaz yapılandırması için en iyi uygulamalar](iot-hub-configuration-best-practices.md)

* **İzleme**: Operatörleri dikkat gerektirebilecek sorunlar konusunda uyarmak için genel cihaz koleksiyonu durumunu ve devam eden işlemlerin durumunu izler.  Cihazların gerçek zamanlı çalışma koşullarını ve güncelleştirme işlemlerinin durumunu raporlamasına olanak tanımak üzere cihaz ikisi uygulayın. Cihaz ikizi sorgularını kullanarak en acil sorunları ortaya çıkaran güçlü pano raporları oluşturun.
  
    *Daha fazla okuma*: 
    * [Cihaz ikizi özelliklerini kullanma](tutorial-device-twins.md)
    * [Cihaz TWINS, işler ve ileti yönlendirme için sorgu dili IoT Hub](iot-hub-devguide-query-language.md)
    * [IoT cihazlarını ölçekli olarak yapılandırma ve izleme](./iot-hub-automatic-device-management.md)
    * [IoT çözümünde cihaz yapılandırması için en iyi uygulamalar](iot-hub-configuration-best-practices.md)

* **Devre dışı bırak**: bir hata, yükseltme döngüsü veya hizmet yaşam süresinin sonunda cihazları değiştirin veya yetkisini alın.  Fiziksel cihaz değiştiriliyorsa cihaz bilgilerini korumak veya kullanım dışı bırakılıyorsa cihaz bilgilerini arşivlemek için cihaz ikizini kullanın. Cihaz kimliklerini ve kimlik bilgilerini güvenli bir şekilde iptal etmek için IoT Hub kimlik kayıt defterini kullanın.
  
    *Daha fazla okuma*: 
    * [Cihaz ikizi özelliklerini kullanma](tutorial-device-twins.md)
    * [Cihaz kimliklerini yönetme](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Cihaz yönetimi modelleri

IoT Hub aşağıdaki cihaz yönetim modellerini sağlar. [Cihaz yönetimi öğreticileri](iot-hub-node-node-device-management-get-started.md), bu desenleri gerçek senaryonuza uygun hale getirme ve bu çekirdek şablonları temel alan yeni desenler tasarlama konularını daha ayrıntılı olarak gösterir.

* **Yeniden başlatma**: arka uç uygulaması, yeniden başlatma işleminin başlatıldığını bir doğrudan yöntem aracılığıyla cihaza bildirir.  Cihaz, bildirilen özellikleri kullanarak cihazın yeniden başlatma durumunu güncelleştirir.
  
    ![Cihaz yönetimi yeniden başlatma deseninin grafiği](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Fabrika Sıfırlaması**: arka uç uygulaması, fabrika sıfırlamasının başlatıldığını bir doğrudan yöntem aracılığıyla cihaza bildirir. Cihaz, bildirilen özellikleri kullanarak cihazın fabrika sıfırlaması durumunu güncelleştirir.
  
    ![Cihaz yönetimi fabrika sıfırlama deseninin grafiği](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Yapılandırma**: arka uç uygulaması, cihazda çalışan yazılımı yapılandırmak için istenen özellikleri kullanır. Cihaz, bildirilen özellikleri kullanarak cihazın yapılandırma durumunu güncelleştirir.
  
    ![Cihaz yönetimi yapılandırma deseninin grafiği](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **Üretici yazılımı güncelleştirmesi**: arka uç uygulaması, güncelleştirmeyi alacak cihazları seçmek, cihazları güncelleştirmeyi nerede bulacağınızı söylemek ve güncelleştirme işlemini izlemek için otomatik cihaz yönetimi yapılandırması kullanır. Cihaz, bellenim görüntüsünü indirmek, doğrulamak ve uygulamak için çok adımlı bir işlem başlatır ve ardından IoT Hub hizmetine yeniden bağlanmadan önce cihazı yeniden başlatır. Çok adımlı işlem boyunca cihaz, bildirilen özellikleri kullanarak cihazın ilerlemesini ve durumunu güncelleştirir.
  
    ![Cihaz yönetimi üretici yazılımı güncelleştirme deseninin grafiği](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **İlerleme ve durumu raporlama**: çözüm arka ucu, cihazlarda çalışan eylemlerin durum ve ilerlemesini raporlamak için cihaz ikizi sorgularını bir dizi cihaz genelinde çalıştırır.
  
    ![Cihaz yönetimi raporlama ilerleme ve durum deseninin grafiği](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>Sonraki Adımlar

IoT Hub’ın cihaz yönetimi için sağladığı özellik, desen ve kod kitaplıkları, her cihaz yaşam döngüsü aşamasında kurumsal IoT operatörünün gereksinimlerini yerine getiren IoT uygulamaları oluşturmanıza olanak sağlar.

IoT Hub’ında cihaz yönetimi özellikleri hakkında daha fazla bilgi almak için [Cihaz yönetimini kullanmaya başlama](iot-hub-node-node-device-management-get-started.md) eğitmenine bakın.