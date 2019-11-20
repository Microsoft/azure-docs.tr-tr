---
title: Azure IoT Hub cihaz sağlama hizmeti 'nde hizmet kavramları | Microsoft Docs
description: Cihaz sağlama hizmeti ile cihazlara özgü hizmet sağlama kavramlarını açıklar ve IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 51486da6b34c0ff1e9b6d05558c2132a416913e9
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104371"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Cihaz sağlama hizmeti kavramlarını IoT Hub

IoT Hub cihaz sağlama hizmeti, belirli bir IoT Hub 'ına sıfır Touch cihaz sağlamayı yapılandırmak için kullandığınız IoT Hub yardımcı hizmettir. Cihaz sağlama hizmeti ile milyonlarca cihazı güvenli ve ölçeklenebilir bir şekilde [Otomatik](concepts-auto-provisioning.md) olarak sağlayabilirsiniz.

Cihaz sağlama iki bölümden oluşan bir işlemdir. İlk bölüm, cihazı *kaydederek* cihaz ile IoT çözümü arasında ilk bağlantıyı kurmaya yönelik olur. İkinci bölüm, çözümün belirli gereksinimlerine bağlı olarak uygun *yapılandırmayı* cihaza uyguluyor. Her iki adım tamamlandıktan sonra cihaz tam olarak *sağlanmış*demektir. Cihaz Sağlama Hizmeti, bu adımların ikisini de otomatikleştirerek cihaz için sorunsuz bir sağlama deneyimi sağlar.

Bu makale, *hizmetin*yönetilmesi için en uygun sağlama kavramlarıyla ilgili genel bir bakış sunar. Bu makale, bir cihazı dağıtıma hazırlamak için [bulut kurulumu adımından](about-iot-dps.md#cloud-setup-step) sorumlu olan personbuna uygundur.

## <a name="service-operations-endpoint"></a>Hizmet işlemleri uç noktası

Hizmet işlemleri uç noktası, hizmet ayarlarını yönetmek ve kayıt listesini sürdürmek için uç noktasıdır. Bu uç nokta yalnızca hizmet yöneticisi tarafından kullanılır; cihazlar tarafından kullanılmaz.

## <a name="device-provisioning-endpoint"></a>Cihaz sağlama uç noktası

Cihaz sağlama uç noktası, tüm cihazların otomatik sağlama için kullanacağı tek uç noktadır. Bu URL, sağlama zinciri senaryolarında yeni bağlantı bilgileriyle Flash cihazları yeniden yazma gereksinimini ortadan kaldırmak için tüm sağlama hizmeti örnekleri için aynıdır. KIMLIK kapsamı, kiracı yalıtımı sağlar.

## <a name="linked-iot-hubs"></a>IoT hub'larına bağlanıldı

Cihaz sağlama hizmeti cihazları yalnızca onunla bağlantılı olan IoT Hub 'larına sağlayabilir. IoT Hub 'ı cihaz sağlama hizmeti örneğine bağlamak, IoT Hub 'ın cihaz kayıt defterine yönelik okuma/yazma izinleri verir; bağlantıyla birlikte cihaz sağlama hizmeti cihaz KIMLIĞINI kaydedebilir ve cihaz ikizi ilk yapılandırmasını ayarlayabilir. Bağlantılı IoT Hub 'ları herhangi bir Azure bölgesinde olabilir. Diğer aboneliklerdeki hub 'ları, sağlama hizmetinize bağlayabilirsiniz.

## <a name="allocation-policy"></a>Ayırma ilkesi

Cihaz sağlama hizmeti 'nin cihazları bir IoT Hub 'ına nasıl atayabileceğini belirleyen hizmet düzeyi ayarı. Desteklenen üç ayırma ilkesi vardır:

* **Eşit ağırlıklı dağıtım**: bağlantılı IoT Hub 'ları, cihazları kendisine temin etmek için büyük olasılıkla eşit değildir. Varsayılan ayar. Yalnızca bir IoT hub'a aygıtları sağlıyorsanız bu ayarı değiştirmeyebilirsiniz.

* **En düşük gecikme süresi**: cihazlar cihaza en düşük gecikme süresine sahip bir IoT Hub 'ına sağlanır. Birden çok bağlı IoT Hub 'ı aynı en düşük gecikme süresini sağlayabilirseniz, sağlama hizmeti cihazları bu hub 'larda karma hale getirir

* **Kayıt listesi aracılığıyla statik yapılandırma**: kayıt listesindeki istenen IoT Hub 'ının belirtimi, hizmet düzeyi ayırma ilkesine göre önceliklidir.

## <a name="enrollment"></a>Kayıt

Kayıt, otomatik sağlama ile kaydedebileceği cihazların veya cihaz gruplarının kaydıdır. Kayıt kaydı, cihaz veya cihaz grubu hakkında aşağıdakiler de dahil olmak üzere bilgiler içerir:
- cihaz tarafından kullanılan [kanıtlama mekanizması](concepts-security.md#attestation-mechanism)
- isteğe bağlı ilk istenen yapılandırma
- istenen IoT Hub 'ı
- istenen cihaz KIMLIĞI

Cihaz sağlama hizmeti tarafından desteklenen iki tür kayıt vardır:

### <a name="enrollment-group"></a>Kayıt grubu

Kayıt grubu, belirli bir kanıtlama mekanizmasını paylaşan bir cihaz grubudur. Kayıt grupları hem X. 509.440 hem de simetrik olarak desteklenir. X. 509.952 kayıt grubundaki tüm cihazlar aynı kök veya ara sertifika yetkilisi (CA) tarafından imzalanmış X. 509.440 sertifikalarını sunar. Simetrik anahtar kayıt grubundaki her bir cihaz, Grup simetrik anahtarından türetilmiş SAS belirteçleri sunar. Kayıt grubu adı ve sertifika adı alfasayısal, küçük harf olmalı ve kısa çizgi içerebilir.

> [!TIP]
> İstenen ilk yapılandırmayı paylaşan çok sayıda cihaz için veya hepsi aynı kiracıya giden cihazlar için bir kayıt grubu kullanmanızı öneririz.

### <a name="individual-enrollment"></a>Bireysel kayıt

Tek bir kayıt, kaydedebilen tek bir cihaz için giriştir. Bireysel kayıtlar, kanıtlama mekanizması olarak X. 509.440 yaprak sertifikalarını veya SAS belirteçlerini (fiziksel veya sanal TPM 'den) kullanabilir. Tek bir kayıt içindeki kayıt KIMLIĞI alfasayısal, küçük harf, ve kısa çizgi içerebilir. Bireysel kayıtlar için istenen IoT hub cihazı kimliği belirtilmiş olabilir.

> [!TIP]
> Benzersiz ilk yapılandırma gerektiren cihazlarda veya yalnızca TPM kanıtlama aracılığıyla SAS belirteçlerini kullanarak kimlik doğrulayabilecek cihazlarda bireysel kayıtları kullanmanızı öneririz.

## <a name="registration"></a>Kayıt

Kayıt, cihaz sağlama hizmeti aracılığıyla bir IoT Hub başarıyla kaydolduğunu/sağlamayı sağlayan bir cihazın kaydıdır. Kayıt kayıtları otomatik olarak oluşturulur; Bunlar silinebilir, ancak güncelleştirilemez.

## <a name="operations"></a>İşlemler

İşlemler, cihaz sağlama hizmetinin fatura birimidir. Tek bir işlem, hizmete yönelik bir yönergenin başarıyla tamamlanmasıyla aynıdır. Cihaz kayıtları ve yeniden kayıtları işlemlere dahildir. Ayrıca kayıt liste girdilerini ekleme ve güncelleştirme gibi hizmet tarafı değişiklikleri de işlemlere dahildir.
