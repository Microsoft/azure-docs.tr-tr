---
title: Azure IoT Hub Cihazı Sağlama Hizmeti’ne Genel Bakış | Microsoft Docs
description: Cihaz sağlama hizmeti (DPS) ve IoT Hub Azure 'da cihaz sağlamayı açıklar
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: e58028495da409df00db8e25adf68945a008bc9e
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228308"
---
# <a name="what-is-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Cihazı Sağlama Hizmeti nedir?
Microsoft Azure, tüm IoT çözüm ihtiyaçlarınız için zengin bir tümleşik genel bulut hizmetleri kümesi sağlar. IoT Hub cihaz sağlama hizmeti (DPS), doğru IoT Hub 'a, Kullanıcı müdahalesi gerektirmeden tam zamanında sağlama sağlayan, IoT Hub için bir yardımcı hizmettir. DPS, milyonlarca cihazın güvenli ve ölçeklenebilir bir şekilde sağlanması için izin vermez.

## <a name="when-to-use-device-provisioning-service"></a>Cihaz Sağlama Hizmeti’ni kullanma zamanı
DPS 'nin, IoT Hub için bağlı ve yapılandırılmış cihazları almak için mükemmel bir seçim olduğu birçok sağlama senaryosu vardır; örneğin:

* IoT Hub bağlantı bilgilerini fabrikada sabit kodlamaya gerek kalmadan tek bir IoT çözümüne dokunma olmadan sağlama (ilk kurulum)
* Birden çok hub genelinde cihazları Yük Dengeleme
* Cihazları kendi sahip olduğu IoT çözümüne, satış işlem verilerine göre bağlama (çok kiracılı)
* Cihazları kullanım durumuna göre belirli bir IoT çözümüne bağlama (çözüm yalıtımı)
* Bir cihazı en düşük gecikme süresine sahip IoT hub’a bağlama (coğrafi parçalama)
* Cihazdaki bir değişikliğe göre yeniden sağlama
* Cihaz tarafından IoT Hub’a bağlanmak için kullanılan anahtarları değiştirme (bağlanmak için X.509 sertifikaları kullanılmadığında)

## <a name="behind-the-scenes"></a>Arka planda
Önceki bölümde listelenen tüm senaryolar, aynı akışla sıfır dokunma sağlama için DPS kullanılarak yapılabilir. Geleneksel olarak sağlamaya dahil olan el ile yapılan adımların birçoğu, IoT cihazlarını dağıtma süresini azaltmak ve el ile hata riskini düşürmek için DPS ile otomatikleştirilir. Aşağıdaki bölümde bir cihazın sağlanması için arka planda gerçekleştirilen işlemler açıklanmaktadır. İlk adım el ile, sonraki adımların tümü ise otomatik olarak gerçekleştirilmektedir.

![Temel sağlama akışı](./media/about-iot-dps/dps-provisioning-flow.png)

1. Cihaz üreticisi, cihaz kayıt bilgilerini Azure portalındaki kayıt listesine ekler.
2. Cihaz, fabrikada ayarlanan DPS uç noktası ile iletişim kurar. Bu cihaz, kimliğini kanıtlamak için tanımlama bilgilerini DPS 'e geçirir.
3. DPS, anahtar nonce sınaması ([Güvenilir Platform Modülü](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) veya standart X. 509.440 doğrulaması (X. 509.440) kullanarak kayıt kimliği ve anahtarı kayıt listesi girişinde doğrulayarak aygıtın kimliğini doğrular.
4. DPS, cihazı bir IoT Hub 'ına kaydeder ve cihazın [istenen ikizi durumunu](../iot-hub/iot-hub-devguide-device-twins.md)doldurur.
5. IoT Hub 'ı, cihaz KIMLIĞI bilgilerini DPS 'e döndürür.
6. DPS, cihaza IoT Hub bağlantı bilgilerini döndürür. Cihaz artık doğrudan IoT hub’a veri göndermeye başlayabilir.
7. Cihaz IoT hub’a bağlanır.
8. Cihaz IoT hub’daki cihaz ikizinden istenen durumu alır.

## <a name="provisioning-process"></a>Sağlama işlemi
Bir cihazın dağıtım işleminde, DPS bağımsız olarak yapılabilecek bir bölümü alan iki ayrı adım vardır:

* Fabrikada cihazın oluşturulduğu ve hazırlandığı **üretim adımı** ve
* Cihaz Sağlama Hizmeti’nin otomatik sağlama için yapılandırıldığı **bulut kurulumu adımı**.

Bu adımların ikisi de mevcut üretim ve dağıtım işlemlerine sorunsuz olarak eklenebilir. DPS, cihaz üzerinde bağlantı bilgilerini almak için el ile çalışmayı içeren bazı dağıtım süreçlerini de basitleştirir.

### <a name="manufacturing-step"></a>Üretim adımı
Bu adım, üretim hattında gerçekleştirilen işlemleri kapsar. Bu adımda bulunan roller silikon tasarımcısı, silikon üreticisi, tümleştirici ve/veya cihazın son üreticisidir. Bu adım, donanımı oluşturmayla ilgilidir.

DPS, üretim sürecinde yeni bir adım sunmaz; Bunun yerine, ilk yazılımı yükleyen mevcut adıma ve (ideal olarak) cihaza HSM 'yi alır. Bu adımda cihaz kimliği oluşturma yerine cihaz, sağlama hizmeti bilgileriyle programlanarak açıldığında bağlantı bilgilerini/IoT çözümü atamasını almak üzere sağlama hizmetini araması sağlanır.

Ayrıca bu adımda üretici cihaz dağıtıcısına/operatöre kimlik anahtarı bilgilerini iletir. Bu bilgilerin sağlanması, tüm cihazların cihaz dağıtıcısı/operatörü tarafından sağlanan imzalama sertifikası ile oluşturulmuş olan bir X.509 sertifikasına sahip olduğunun onaylanması kadar basit veya her bir TPM cihazından TPM onay anahtarının genel bölümünün ayıklanması kadar karmaşık olabilir. Bu hizmetler günümüzde birçok silikon üreticisi tarafından sunulmaktadır.

### <a name="cloud-setup-step"></a>Bulut kurulumu adımı
Bu adımda bulut, uygun otomatik sağlama için yapılandırılır. Bulut kurulumu adımında genellikle iki tür kullanıcı vardır. Cihazların ilk kurulumunun nasıl olması gerektiğini bilen bir kullanıcı (cihaz operatörü) ve cihazların IoT hub’larına nasıl dağıtılması gerektiğini bilen bir kullanıcı (çözüm operatörü).

Sağlama süresinde genellikle çözüm operatörü tarafından gerçekleştirilen tek seferlik bir kurulum adımı vardır. Sağlama hizmeti yapılandırıldıktan sonra kullanım örneği değiştirilmediği sürece değiştirilmesi gerekmez.

Hizmet otomatik sağlama için yapılandırıldıktan sonra cihaz kaydına hazır duruma getirilmesi gerekir. Bu adım, cihazların istenen yapılandırmasını bilen ve IoT hub’ını arayan cihazın kimliğinin sağlama hizmeti tarafından uygun şekilde kanıtlayacağından emin olma konusunda yetkili olan cihaz operatörü tarafından gerçekleştirilir. Cihaz operatörü tanımlama anahtarını üreticiden alır ve kayıt listesine ekler. Yeni girişlerin eklenmesi veya var olan girişlerin cihazlar hakkındaki en son bilgilerle güncelleştirmesi nedeniyle kayıt listesinde daha sonradan güncelleştirme yapılabilir.

## <a name="registration-and-provisioning"></a>Kayıt ve sağlama
*Sağlama*, terimin kullanıldığı sektöre bağlı olarak farklı anlamlara gelebilir. IoT cihazlarını bulut çözümlerine sağlama bağlamında sağlama işlemi iki bölümden oluşan bir işlemdir:

1. İlk bölüm, cihazı kaydederek cihaz ile IoT çözümü arasındaki ilk bağlantıyı kurmaktır.
2. İkinci bölüm ise kaydedildiği çözümün gereksinimlerine bağlı olarak cihaz uygun yapılandırmanın uygulanmasıdır.

Bu adımların ikisi de tamamlandıktan sonra cihazın tam olarak sağlandığını söylemek mümkündür. Bazı bulut hizmetleri cihazları IoT çözümü uç noktasına kaydedip ilk yapılandırmayı gerçekleştirmeyerek sağlama işleminin yalnızca ilk bölümünü tamamlar. DPS, cihaz için sorunsuz bir sağlama deneyimi sağlamak üzere her iki adımı da otomatikleştirir.

## <a name="features-of-the-device-provisioning-service"></a>Cihaz Sağlama Hizmeti’nin özellikleri
DPS birçok özelliğe sahiptir ve bu, cihazları sağlamak için ideal hale getirir.

* **Güvenli kanıtlama**: Hem X.509 hem de TPM tabanlı kimlikler için destek.
* **Kayıt listesi**: Bir noktada kaydedilebilecek cihazların/cihaz gruplarının tam kaydını içerir. Kayıt listesi, kaydedilen cihazın istenen yapılandırması hakkında bilgi içerir ve istenen zamanda güncelleştirilebilir.
* Senaryolarınızı desteklemek için DPS Cihazları IoT Hub 'larına nasıl atayacağını denetlemek için **birden çok ayırma ilkesi** : en düşük gecikme süresi, eşit ağırlıklı dağıtım (varsayılan) ve kayıt listesi aracılığıyla statik yapılandırma. Gecikme süresi [Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md#performance)ile aynı yöntem kullanılarak belirlenir.
* **İzleme ve tanılama günlüğü**: Her şeyin düzgün çalıştığından emin olmanızı sağlar.
* **Çoklu Hub desteği** , DPS 'nin birden fazla IoT Hub 'ına cihaz atamasına izin verir. DPS, birden çok Azure aboneliği genelinde hub 'larla iletişim kurabilir.
* **Çapraz bölge desteği** , DPS 'nin diğer bölgelerde IoT Hub 'larına cihaz atamasını sağlar.
* **Bekleyen veriler Için şifreleme** , DPS 'nin 256 bit AES şifrelemesi kullanılarak şifrelenmiş ve şifresi çözülebilmesi, en güçlü blok şifrelemeleri mevcuttur ve FIPS 140-2 uyumludur.


Aynı bölümdeki diğer kavramsal konularla birlikte [DPS terminoloji](concepts-service.md) konusunu inceleyerek cihaz sağlama ile ilgili kavramlar ve özellikler hakkında daha fazla bilgi edinebilirsiniz.

## <a name="cross-platform-support"></a>Platformlar arası destek
Tüm Azure IoT hizmetlerinde olduğu gibi, DPS, çeşitli işletim sistemleriyle platformlar arası çalışıyor. Azure, cihazların bağlanmasını ve hizmetin yönetilmesini kolaylaştırmak amacıyla birçok [dilde](https://github.com/Azure/azure-iot-sdks) açık kaynak SDK’ları sunar. DPS, cihazları bağlamak için aşağıdaki protokolleri destekler:

* HTTPS
* AMQP
* Web yuvaları üzerinden AMQP
* MQTT
* Web yuvaları üzerinden MQTT

DPS, hizmet işlemleri için yalnızca HTTPS bağlantılarını destekler.

## <a name="regions"></a>Bölgeler
DPS birçok bölgede kullanılabilir. Tüm hizmetler için mevcut ve yeni duyurulan bölgelerin güncel listesi [Azure Bölgeleri](https://azure.microsoft.com/regions/) sayfasında yer almaktadır. Cihaz Sağlama Hizmeti’nin kullanılabilirliğini [Azure Durumu](https://azure.microsoft.com/status/) sayfasından kontrol edebilirsiniz.

> [!NOTE]
> DPS geneldir ve bir konum ile bağlantılı değildir. Ancak, DPS profilinizde ilişkilendirilen meta verilerin bulunacağı bir bölge belirtmeniz gerekir.

## <a name="availability"></a>Kullanılabilirlik
DPS için% 99,9 Hizmet Düzeyi Sözleşmesi vardır ve [SLA 'yı okuyabilirsiniz](https://azure.microsoft.com/support/legal/sla/iot-hub/). [Azure SLA](https://azure.microsoft.com/support/legal/sla/) şartları, Azure’un tamamının kullanılabilirlik garantisini açıklamaktadır.

## <a name="quotas"></a>Kotalar
Her Azure aboneliği varsayılan kota sınırları içerir ve bu sınırlar, IoT çözümünüzün kapsamını etkileyebilir. Abonelik başına geçerli sınır, 10 Cihaz Sağlama Hizmeti/abonelik şeklindedir.

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

Kota sınırları hakkındaki diğer ayrıntılar için:
* [Azure Aboneliği Hizmet Sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>İlgili Azure bileşenleri
DPS cihaz sağlamayı Azure IoT Hub otomatikleştirir. [IoT Hub](../iot-hub/index.yml)hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar
Artık Azure’da IoT cihazlarını sağlama hakkında genel bilgilere sahipsiniz. Bir sonraki adımda uçtan uca bir IoT senaryosunu tamamlamayı deneyeceksiniz.

[IoT Hub cihaz sağlama hizmeti 'ni Azure portal ile ayarlama](quick-setup-auto-provision.md)

[Sanal cihaz oluşturma ve sağlama](quick-create-simulated-device.md)

[Sağlama için cihazı ayarlama](tutorial-set-up-device.md)