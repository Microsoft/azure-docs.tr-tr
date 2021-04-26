---
title: SSL/TLS bağlantısı-MariaDB için Azure veritabanı
description: MariaDB için Azure veritabanı 'nı ve ilişkili uygulamaları düzgün şekilde SSL bağlantıları kullanacak şekilde yapılandırma bilgileri
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 6c4ee8c6d12a6350c319e40bbe30046af4932311
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550649"
---
# <a name="ssltls-connectivity-in-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı 'nda SSL/TLS bağlantısı
MariaDB için Azure veritabanı, veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuzla istemci uygulamalarınız arasında SSL bağlantılarının zorunlu tutulması, sunucuya uygulamanız arasındaki veri akışını şifreleyerek "bağlantıyı izinsiz izleme" saldırılarına karşı korumaya yardımcı olur.

>[!NOTE]
> Müşterilerin geri bildirimlerine bağlı olarak, var olan Baltidaha fazla kök CA 'sı için 15 Şubat 2021 (02/15/2021) tarihine kadar kök sertifikayı kullanımdan kaldırmayı genişlettik.

> [!IMPORTANT] 
> SSL kök sertifikası 15 Şubat 2021 tarihinden itibaren sona ermek üzere ayarlanmıştır (02/15/2021). Lütfen uygulamanızı [yeni sertifikayı](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)kullanacak şekilde güncelleştirin. Daha fazla bilgi için bkz. [planlı sertifika güncelleştirmeleri](concepts-certificate-rotation.md)

## <a name="default-settings"></a>Varsayılan ayarlar
Varsayılan olarak, veritabanı hizmeti MariaDB 'ye bağlanırken SSL bağlantıları gerektirecek şekilde yapılandırılmalıdır.  Mümkün olduğunda SSL seçeneğini devre dışı bırakmaktan kaçınmanızı öneririz.

Azure portal ve CLı aracılığıyla MariaDB sunucusu için yeni bir Azure veritabanı sağlarken, SSL bağlantılarını zorlama varsayılan olarak etkinleştirilir.

Bazı durumlarda, uygulamalar güvenli bir şekilde bağlanmak için güvenilir bir sertifika yetkilisi (CA) sertifika dosyasından oluşturulan yerel bir sertifika dosyası gerektirir. Şu anda müşteriler, ' de bulunan MariaDB sunucusu için Azure veritabanı 'na bağlanmak üzere yalnızca önceden tanımlanmış sertifikayı **kullanabilir** https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . 

Benzer şekilde, aşağıdaki bağlantılar, sogeign bulutlarındaki sunucular için sertifikaları işaret ediyor: [Azure Kamu](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure Çin](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)ve [Azure Almanya](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

Çeşitli programlama dilleri için bağlantı dizeleri Azure portal gösterilmektedir. Bu bağlantı dizeleri, veritabanınıza bağlanmak için gerekli SSL parametrelerini içerir. Azure portal, sunucunuzu seçin. **Ayarlar** başlığı altında **bağlantı dizelerini** seçin. SSL parametresi bağlayıcıya göre değişir; Örneğin, "SSL = true" veya "sslmode = gerektir" veya "sslmode = Required" ve diğer Çeşitlemeler.

Uygulama geliştirirken SSL bağlantısını etkinleştirme veya devre dışı bırakma hakkında bilgi edinmek için bkz. [SSL 'yi yapılandırma](howto-configure-ssl.md).

## <a name="tls-enforcement-in-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı 'nda TLS zorlaması

MariaDB için Azure veritabanı, Aktarım Katmanı Güvenliği (TLS) kullanarak veritabanı sunucunuza bağlanan istemciler için şifrelemeyi destekler. TLS, veritabanı sunucunuz ile istemci uygulamalarınız arasında güvenli ağ bağlantıları sağlayan ve uyumluluk gereksinimlerine bağlı olmanızı sağlayan bir endüstri standardı protokolüdür.

### <a name="tls-settings"></a>TLS ayarları

MariaDB için Azure veritabanı, istemci bağlantıları için TLS sürümünü zorlama olanağı sağlar. TLS sürümünü zorlamak için **En düşük TLS sürümü** seçenek ayarını kullanın. Bu seçenek ayarı için aşağıdaki değerlere izin verilir:

|  Minimum TLS ayarı             | İstemci TLS sürümü destekleniyor                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (varsayılan) | TLS gerekli değil                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 ve üzeri         |
| TLS1_1                           | TLS 1,1, TLS 1,2 ve üzeri              |
| TLS1_2                           | TLS sürüm 1,2 ve üzeri                  |


Örneğin, minimum TLS ayarı sürümü değerinin TLS 1,0 olarak ayarlanması, sunucunuzun TLS 1,0, 1,1 ve 1.2 + kullanarak istemcilerden gelen bağlantılara izin vermeyeceği anlamına gelir. Alternatif olarak, bunu 1,2 olarak ayarlamak yalnızca TLS 1.2 + kullanan istemcilerden gelen bağlantılara izin vermek ve TLS 1,0 ve TLS 1,1 ile tüm bağlantıların reddedilecektir.

> [!Note] 
> Varsayılan olarak, MariaDB için Azure veritabanı en düşük TLS sürümünü (ayarı `TLSEnforcementDisabled` ) zorlamaz.
>
> En düşük TLS sürümünü zorladıktan sonra, en düşük sürüm zorlamayı devre dışı bırakabilirsiniz.

MariaDB için Azure veritabanınız için TLS ayarını ayarlama hakkında bilgi edinmek için bkz. [TLS ayarlarını yapılandırma](howto-tls-configurations.md).

## <a name="cipher-support-by-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı tarafından şifre desteği

SSL/TLS iletişiminin bir parçası olarak, şifre paketleri onaylanır ve veritabanı sunucusuyla iletişim kurmasına yalnızca şifre cinslarını destekler. Şifre paketi doğrulaması, [ağ geçidi katmanında](concepts-connectivity-architecture.md#connectivity-architecture) denetlenir ve doğrudan düğümün kendisinde değildir. Şifre paketleri aşağıda listelenen paketlerden biriyle eşleşmiyorsa, gelen istemci bağlantıları reddedilir.

### <a name="cipher-suite-supported"></a>Desteklenen şifre paketi

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Sonraki adımlar
- [Sunucu güvenlik duvarı kuralları](concepts-firewall-rules.md) hakkında daha fazla bilgi
- [SSL 'yi yapılandırmayı](howto-configure-ssl.md) öğrenin
- [TLS Yapılandırma](howto-tls-configurations.md) hakkında bilgi edinin
