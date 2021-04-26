---
title: Azure IoT Hub cihaz sağlama hizmeti ile kullanılan terminoloji | Microsoft Docs
description: Cihaz sağlama hizmeti (DPS) ve IoT Hub ile kullanılan ortak terminolojiyi açıklar
author: wesmc7777
ms.author: wesmc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: b9fc37c6589cdd0bc6a5cdce7b7ebebe2c6e9a85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019455"
---
# <a name="iot-hub-device-provisioning-service-dps-terminology"></a>IoT Hub cihaz sağlama hizmeti (DPS) terminolojisi

IoT Hub cihaz sağlama hizmeti, belirli bir IoT Hub 'ına sıfır Touch cihaz sağlamayı yapılandırmak için kullandığınız IoT Hub yardımcı hizmettir. Cihaz sağlama hizmeti [ile milyonlarca cihazı](about-iot-dps.md#provisioning-process) güvenli ve ölçeklenebilir bir şekilde sağlayabilirsiniz.

Cihaz sağlama iki bölümden oluşan bir işlemdir. İlk bölüm, cihazı *kaydederek* cihaz ile IoT çözümü arasında ilk bağlantıyı kurmaya yönelik olur. İkinci bölüm, çözümün belirli gereksinimlerine bağlı olarak uygun *yapılandırmayı* cihaza uyguluyor. Her iki adım tamamlandıktan sonra cihaz tam olarak *sağlanmış* demektir. Cihaz Sağlama Hizmeti, bu adımların ikisini de otomatikleştirerek cihaz için sorunsuz bir sağlama deneyimi sağlar.

Bu makale, *hizmetin* yönetilmesi için en uygun sağlama kavramlarıyla ilgili genel bir bakış sunar. Bu makale, bir cihazı dağıtıma hazırlamak için [bulut kurulumu adımından](about-iot-dps.md#cloud-setup-step) sorumlu olan personbuna uygundur.

## <a name="service-operations-endpoint"></a>Hizmet işlemleri uç noktası

Hizmet işlemleri uç noktası, hizmet ayarlarını yönetmek ve kayıt listesini sürdürmek için uç noktasıdır. Bu uç nokta yalnızca hizmet yöneticisi tarafından kullanılır; cihazlar tarafından kullanılmaz.

## <a name="device-provisioning-endpoint"></a>Cihaz sağlama uç noktası

Cihaz sağlama uç noktası, tüm cihazların otomatik sağlama için kullanacağı tek uç noktadır. Bu URL, sağlama zinciri senaryolarında yeni bağlantı bilgileriyle Flash cihazları yeniden yazma gereksinimini ortadan kaldırmak için tüm sağlama hizmeti örnekleri için aynıdır. KIMLIK kapsamı, kiracı yalıtımı sağlar.

## <a name="linked-iot-hubs"></a>Bağlantılı IoT Hub 'ları

Cihaz sağlama hizmeti cihazları yalnızca onunla bağlantılı olan IoT Hub 'larına sağlayabilir. IoT Hub 'ı cihaz sağlama hizmeti örneğine bağlamak, IoT Hub 'ın cihaz kayıt defterine yönelik okuma/yazma izinleri verir; bağlantıyla birlikte cihaz sağlama hizmeti cihaz KIMLIĞINI kaydedebilir ve cihaz ikizi ilk yapılandırmasını ayarlayabilir. Bağlantılı IoT Hub 'ları herhangi bir Azure bölgesinde olabilir. Diğer aboneliklerdeki hub 'ları, sağlama hizmetinize bağlayabilirsiniz.


## <a name="allocation-policy"></a>Ayırma ilkesi

Cihaz sağlama hizmeti 'nin cihazları bir IoT Hub 'ına nasıl atayabileceğini belirleyen hizmet düzeyi ayarı. Desteklenen üç ayırma ilkesi vardır:

* **Eşit ağırlıklı dağıtım**: bağlantılı IoT Hub 'ları, cihazları kendisine temin etmek için büyük olasılıkla eşit değildir. Varsayılan ayar. Yalnızca bir IoT hub'a aygıtları sağlıyorsanız bu ayarı değiştirmeyebilirsiniz.

* **En düşük gecikme süresi**: cihazlar cihaza en düşük gecikme süresine sahip bir IoT Hub 'ına sağlanır. Birden çok bağlı IoT Hub 'ı aynı en düşük gecikme süresini sağlayabilirseniz, sağlama hizmeti cihazları bu hub 'larda karma hale getirir

* **Kayıt listesi aracılığıyla statik yapılandırma**: kayıt listesindeki istenen IoT Hub 'ının belirtimi, hizmet düzeyi ayırma ilkesine göre önceliklidir.

* **Özel (Azure Işlevini kullanın)**: özel bir ayırma ilkesi, cihazların bir IoT Hub 'ına nasıl atanabileceği konusunda daha fazla denetim sağlar. Bu, bir Azure Işlevindeki cihazları bir IoT Hub 'ına atamak için özel kod kullanılarak gerçekleştirilir. Cihaz sağlama hizmeti, cihazla ilgili tüm bilgileri ve kodunuza kaydetme bilgilerini sağlayan Azure Işlev kodunuzu çağırır. İşlev kodunuz yürütülür ve cihazı sağlamak için kullanılan IoT Hub bilgilerini döndürür.

## <a name="enrollment"></a>Kayıt

Kayıt, otomatik sağlama ile kaydedebileceği cihazların veya cihaz gruplarının kaydıdır. Kayıt kaydı, cihaz veya cihaz grubu hakkında aşağıdakiler de dahil olmak üzere bilgiler içerir:
- cihaz tarafından kullanılan [kanıtlama mekanizması](#attestation-mechanism)
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


## <a name="attestation-mechanism"></a>Kanıtlama mekanizması

Kanıtlama mekanizması, bir cihazın kimliğini onaylamak için kullanılan yöntemdir. Kanıtlama mekanizması bir kayıt girişinde yapılandırılır ve sağlama hizmetine, kayıt sırasında bir cihazın kimliği doğrulanırken hangi yöntemin kullanılacağını söyler.

> [!NOTE]
> IoT Hub, söz konusu hizmette benzer bir kavram için "kimlik doğrulama şeması" nı kullanır.

Cihaz sağlama hizmeti aşağıdaki kanıtlama biçimlerini destekler:
* Standart X. 509.440 sertifika kimlik doğrulama akışına göre **X. 509.440 sertifikaları** . Daha fazla bilgi için bkz. [X. 509.440 kanıtlama](concepts-x509-attestation.md).
* Anahtar bir paylaşılan erişim Imzası (SAS) belirteci sunmak için anahtarlar için TPM standardı kullanarak, bir kerelik anahtar sınamasını temel alan **Güvenilir Platform Modülü (TPM)** . Bu, cihazda fiziksel TPM gerektirmez, ancak hizmet [TPM belirtimi](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)başına onay anahtarını kullanarak test bulmayı bekler. Daha fazla bilgi için bkz. [TPM kanıtlama](concepts-tpm-attestation.md).
* Karma **anahtar** , karma bir imza ve katıştırılmış süre sonu içeren paylaşılan erişim IMZASı (SAS) [güvenlik belirteçlerini](../iot-hub/iot-hub-devguide-security.md#security-tokens)temel alır. Daha fazla bilgi için bkz. [simetrik anahtar kanıtlama](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Donanım güvenlik modülü

Donanım güvenlik modülü veya HSM, cihaz gizli dizileri için güvenli, donanım tabanlı depolamada kullanılır ve gizli depolama alanının en güvenli biçimidir. Hem X. 509.440 sertifikaları hem de SAS belirteçleri HSM 'de depolanabilir. HSM 'ler, sağlama hizmeti 'nin desteklediği kanıtlama mekanizmalarıyla birlikte kullanılabilir.

> [!TIP]
> Cihazlarınızda gizli dizileri güvenli bir şekilde depolamak için cihazlarla bir HSM kullanmanız önemle önerilir.

Cihaz gizli dizileri de yazılımda (bellek) depolanabilir, ancak bir HSM 'den daha az güvenli bir depolama biçimidir.



## <a name="id-scope"></a>KIMLIK kapsamı

KIMLIK kapsamı, Kullanıcı tarafından oluşturulduğunda bir cihaz sağlama hizmetine atanır ve cihazın kaydedileceği belirli sağlama hizmetini benzersiz şekilde tanımlamak için kullanılır. KIMLIK kapsamı, hizmet tarafından oluşturulur ve, benzersizliği garanti eden sabittir.

> [!NOTE]
> Benzersizlik, uzun süre çalışan dağıtım işlemleri ve merme ve Alım senaryoları için önemlidir.


## <a name="registration"></a>Kayıt

Kayıt, cihaz sağlama hizmeti aracılığıyla bir IoT Hub başarıyla kaydolduğunu/sağlamayı sağlayan bir cihazın kaydıdır. Kayıt kayıtları otomatik olarak oluşturulur; Bunlar silinebilir, ancak güncelleştirilemez.


## <a name="registration-id"></a>Kayıt KIMLIĞI

Kayıt KIMLIĞI cihaz sağlama hizmeti ile bir cihaz kaydını benzersiz şekilde tanımlamak için kullanılır. Cihaz KIMLIĞI, sağlama hizmeti [kimlik kapsamında](#id-scope)benzersiz olmalıdır. Her cihazın bir kayıt KIMLIĞI olmalıdır. Kayıt KIMLIĞI alfasayısal, büyük/küçük harfe duyarsız ve iki nokta, nokta, alt çizgi ve kısa çizgi gibi özel karakterler içerebilir.

* TPM söz konusu olduğunda kayıt KIMLIĞI TPM tarafından sağlanır.
* X. 509.440 tabanlı kanıtlama söz konusu olduğunda, kayıt KIMLIĞI sertifikanın konu adı olarak sağlanır.

## <a name="device-id"></a>Cihaz Kimliği

Cihaz KIMLIĞI, IoT Hub göründüğü şekliyle KIMLIĞIDIR. İstenen cihaz KIMLIĞI kayıt girişinde ayarlanabilir, ancak ayarlanması gerekli değildir. İstenen cihaz KIMLIĞI ayarı yalnızca bireysel kayıtlar 'da desteklenir. Kayıt listesinde istenen cihaz KIMLIĞI belirtilmemişse, cihaz kaydı sırasında cihaz KIMLIĞI olarak kayıt KIMLIĞI kullanılır. [IoT Hub cihaz kimlikleri](../iot-hub/iot-hub-devguide-identity-registry.md)hakkında daha fazla bilgi edinin.



## <a name="operations"></a>Operations

İşlemler, cihaz sağlama hizmetinin fatura birimidir. Tek bir işlem, hizmete yönelik bir yönergenin başarıyla tamamlanmasıyla aynıdır. İşlemler cihaz kayıtları ve yeniden kayıtlar içerir; işlemler, kayıt listesi girişleri ekleme ve kayıt listesi girişlerini güncelleştirme gibi hizmet tarafı değişiklikleri de içerir.
