---
title: PostgreSQL için Azure veritabanı 'nda TLS/SSL kullanılarak şifrelenmiş bağlantı-esnek sunucu
description: PostgreSQL için Azure veritabanı 'nda TLS/SSL kullanarak bağlanma hakkında yönergeler ve bilgiler-esnek sunucu.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: cd9a74e9a05dad68a5d05fd8b9c92ed566c21766
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227441"
---
# <a name="encrypted-connectivity-using-transport-layer-security-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL için Azure veritabanı 'nda Aktarım Katmanı Güvenliği kullanılarak şifrelenmiş bağlantı-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

PostgreSQL için Azure veritabanı-esnek sunucu, daha önce Güvenli Yuva Katmanı (SSL) olarak bilinen Aktarım Katmanı Güvenliği (TLS) kullanarak istemci uygulamalarınızı PostgreSQL hizmetine bağlamayı destekler. TLS, veritabanı sunucunuz ile istemci uygulamalarınız arasında şifrelenmiş ağ bağlantıları sağlayan ve uyumluluk gereksinimlerine bağlı olmanızı sağlayan bir endüstri standardı protokolüdür.

PostgreSQL için Azure veritabanı-esnek sunucu, Aktarım Katmanı Güvenliği (TLS 1.2 +) kullanılarak şifrelenmiş bağlantıları destekler ve TLS 1,0 ve TLS 1,1 ile gelen tüm bağlantılar reddedilir. Tüm esnek sunucular için TLS bağlantıları zorlaması etkinleştirilmiştir ve esnek sunucuya bağlanmak için TLS/SSL 'yi devre dışı bırakayükleyemezsiniz.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>TLS/SSL bağlantısı için sertifika doğrulaması gerektiren uygulamalar
Bazı durumlarda, uygulamalar güvenli bir şekilde bağlanmak için güvenilir bir sertifika yetkilisi (CA) sertifika dosyasından oluşturulan yerel bir sertifika dosyası gerektirir. PostgreSQL için Azure veritabanı-esnek sunucu, *DigiCert genel kök CA 'sını* kullanır. [DigiCert küresel kök CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) 'dan SSL üzerinden iletişim kurmak için gereken bu sertifikayı indirin ve sertifika dosyasını tercih ettiğiniz konuma kaydedin. Örneğin, bu öğretici kullanır `c:\ssl` .


### <a name="connect-using-psql"></a>Psql kullanarak bağlanma
Esnek sunucunuzu *özel erişim (VNET tümleştirmesi)* ile oluşturduysanız, sunucunuza aynı VNET içindeki bir kaynaktan sunucunuza bağlanmanız gerekir. Bir sanal makine oluşturabilir ve bunu esnek sunucunuz ile oluşturulan VNet 'e ekleyebilirsiniz.

Esnek sunucunuzu *ortak erişim (izin VERILEN IP adresleri)* ile oluşturduysanız, sunucunuzdaki güvenlik duvarı kuralları lıstesıne yerel IP adresinizi ekleyebilirsiniz.

Aşağıdaki örnek, psql komut satırı arabirimini kullanarak sunucunuza nasıl bağlanılacağını gösterir. `sslmode=verify-full`TLS/SSL sertifika doğrulamasını zorlamak için bağlantı dizesi ayarını kullanın. Yerel sertifika dosya yolunu `sslrootcert` parametreye geçirin.

```bash
 psql "sslmode=verify-full sslrootcert=c:\ssl\DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=postgres user=myadmin"
```
> [!Note]
> Geçirilen değerin, `sslrootcert` kaydettiğiniz sertifikanın dosya yolu ile eşleştiğinden emin olun.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Uygulamanızın veya çerçevesinin TLS bağlantılarını desteklediğinden emin olun

Veritabanı Hizmetleri için PostgreSQL kullanan bazı uygulama çerçeveleri, yükleme sırasında varsayılan olarak TLS 'yi etkinleştirmez. PostgreSQL sunucunuz TLS bağlantıları uygular, ancak uygulama TLS için yapılandırılmamışsa, uygulama veritabanı sunucunuza bağlanamaz. TLS bağlantılarının nasıl etkinleştirileceğini öğrenmek için uygulamanızın belgelerine başvurun.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure CLI kullanarak PostgreSQL Için Azure veritabanı-esnek sunucu sanal ağı oluşturun ve yönetin](./how-to-manage-virtual-network-cli.md).
- [PostgreSQL Için Azure veritabanı 'nda ağ](./concepts-networking.md) hakkında daha fazla bilgi edinin-esnek sunucu
- [PostgreSQL Için Azure veritabanı-esnek sunucu güvenlik duvarı kuralları](./concepts-networking.md#public-access-allowed-ip-addresses) hakkında daha fazla bilgi edinin
