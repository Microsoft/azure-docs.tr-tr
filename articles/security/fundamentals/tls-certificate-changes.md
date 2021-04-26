---
title: Azure TLS sertifikası değişiklikleri
description: Azure TLS sertifikası değişiklikleri
services: security
author: msmbaldwin
tags: azure-resource-manager
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 11/10/2020
ms.author: mbaldwin
ms.openlocfilehash: 9e4edbcfd9e4180e727cd885902d9f0150a967ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389408"
---
# <a name="azure-tls-certificate-changes"></a>Azure TLS sertifikası değişiklikleri  

Microsoft, Azure hizmetlerini farklı bir kök sertifika yetkilisi (CA) kümesinden TLS sertifikaları kullanacak şekilde güncelleştiriyor. Geçerli CA sertifikaları CA/tarayıcı Forum temel gereksinimlerinden biriyle uyumlu olmadığından ve 15 Şubat 2021 ' de iptal edilecek olduğundan bu değişiklik yapılıyor.

## <a name="when-will-this-change-happen"></a>Bu değişiklik ne zaman meydana gelir?

Mevcut Azure uç noktaları, 13 Ağustos 2020 ' den itibaren aşamalı bir şekilde geçiyor. Yeni oluşturulan tüm Azure TLS/SSL uç noktaları, yeni kök CA 'Lara zincirleme olan güncelleştirilmiş sertifikaları içerir.

Tüm Azure hizmetleri bu değişiklikten etkilenir. Belirli hizmetler için bazı ek ayrıntılar aşağıda verilmiştir:

- [Azure Active Directory](../../active-directory/index.yml) (Azure AD) Hizmetleri, bu geçişe 7 Temmuz 2020 tarihinde başlamıştır.
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) ve [DPS](../../iot-dps/index.yml) , Baltimore CyberTrust kök CA 'da kalacak, ancak ara CA 'ları değişecektir. [Ayrıntılar için buraya tıklayın](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456).
- [Azure depolama](../../storage/index.yml) , Baltimore CyberTrust kök CA 'da kalacak, ancak ara CA 'ları değişecektir. [Ayrıntılar için buraya tıklayın](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).
- [Redsıs Için Azure önbelleği](../../azure-cache-for-redis/index.yml) , Baltimore CyberTrust kök CA 'da kalacak, ancak ara CA 'ları değişecektir. [Ayrıntılar için buraya tıklayın](../../azure-cache-for-redis/cache-whats-new.md).
- Azure Instance Metadata Service, Baltimore CyberTrust kök CA 'da kalacak, ancak ara CA 'Ları değişecektir. [Ayrıntılar için buraya tıklayın](/answers/questions/172717/action-required-for-attested-data-tls-with-azure-i.html).

> [!IMPORTANT]
> Müşterilerin, Azure hizmetlerine bağlanmaya çalışırken bağlantı başarısızlıklarını engellemek için bu değişiklikten sonra uygulama (ler) i güncelleştirmeleri güncelleştirmesi gerekebilir.

## <a name="what-is-changing"></a>Ne değişiyor?

Bugün, Azure hizmetleri zinciri tarafından aşağıdaki kök CA 'ya kadar kullanılan TLS sertifikalarının çoğu bugün:

| CA 'nın ortak adı | Parmak izi (SHA1) |
|--|--|
| [Baltimore Sitrust kökü](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Azure hizmetleri tarafından kullanılan TLS sertifikaları aşağıdaki kök CA 'Lardan birine zincirlenir:

| CA 'nın ortak adı | Parmak izi (SHA1) |
|--|--|
| [DigiCert genel kök G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert genel kök CA 'sı](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore Sitrust kökü](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST kök sınıfı 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA kök sertifika yetkilisi 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft ECC kök sertifika yetkilisi 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crt) | 999a64c37ff47d9fab95f14769891460eec4c3c5 |

## <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Eski ara parmak izini devre dışı bırakabilirim?

Geçerli CA sertifikaları 15 Şubat 2021 ' ye kadar *iptal edilmez.* Bu tarihten sonra, eski parmak izlerini kodınızdan kaldırabilirsiniz.

Bu tarih değişirse, yeni iptal tarihi hakkında bildirim alırsınız.

## <a name="will-this-change-affect-me"></a>Bu değişiklik beni etkiler mi? 

**Azure müşterilerinin çoğu** etkilenmeyeceğini umuz.  Ancak, kabul edilebilir ca 'ların bir listesini açıkça belirtiyorsa uygulamanız etkilenebilir. Bu uygulama, sertifika sabitleme olarak bilinir.

Uygulamanızın etkilenip etkilendiğinin algılanması için bazı yollar şunlardır:

- Kaynak kodunuzda, bir Microsoft IT TLS CA 'sının parmak izi, ortak ad ve diğer CERT özellikleri için [burada](https://www.microsoft.com/pki/mscorp/cps/default.htm)bulunan bir arama yapın. Bir eşleşme varsa uygulamanız etkilenecektir. Bu sorunu çözmek için, kaynak kodu güncelleştirme yeni CA 'Ları içerir. En iyi uygulama olarak, CA 'Ların kısa bildirimde eklenebildiğinden veya düzenlenebildiğinden emin olun. Sektör düzenlemeleri, CA sertifikalarının yedi gün içinde değiştirilmesini gerektirir ve bu nedenle sabitleme ile ilgili olan müşterilerin da bir şekilde tepki sağlaması gerekir.

- Azure API 'Leri veya diğer Azure hizmetleriyle tümleşen bir uygulamanız varsa ve sertifika sabitleme kullanıyorsa emin değilseniz, uygulama satıcısına danışın.

- Azure hizmetleriyle iletişim kuran farklı işletim sistemleri ve dil çalışma zamanları, sertifika zincirini bu yeni köklerle doğru şekilde oluşturmak için ek adımlar gerektirebilir:
    - **Linux**: birçok dağıtım,/etc/SSL/certsa CA eklemenizi gerektirir. Belirli yönergeler için dağıtım belgelerine bakın.
    - **Java**: Java anahtar deposunun yukarıda listelenen CA 'ları içerdiğinden emin olun.
    - **Bağlantısı kesilmiş ortamlarda çalışan Windows**: bağlantısı kesilmiş ortamlarda çalışan sistemlerin, güvenilen kök sertifika yetkilileri deposuna yeni köklerin ve ara sertifika yetkilileri deposuna eklenmiş olan hammaddeleri olması gerekir.
    - **Android**: cihazınızın ve Android sürümünün belgelerini denetleyin.
    - **Diğer donanım aygıtları, özellikle IoT**: cihaz üreticisine başvurun.

- Güvenlik Duvarı kurallarının yalnızca belirli sertifika Iptal listesi (CRL) indirme ve/veya çevrimiçi sertifika durumu Protokolü (OCSP) doğrulama konumlarına giden çağrılara izin verecek şekilde ayarlandığı bir ortamınız varsa. Aşağıdaki CRL ve OCSP URL 'Lerine izin vermeniz gerekir:

    - http://crl3&#46;d ıgicert&#46;com
    - http://crl4&#46;d ıgicert&#46;com
    - http://ocsp&#46;d ıgicert&#46;com
    - http://www&#46;d güveni&#46;net
    - http://root-c3-ca2-2009&#46; OCSP&#46;d-Trust&#46;net
    - http://crl&#46; Microsoft&#46;com
    - http://oneocsp&#46; Microsoft&#46;com
    - http://ocsp&#46; msocsp&#46;com
    - http://www&#46; Microsoft&#46;com/pkiops

## <a name="next-steps"></a>Sonraki adımlar

Başka sorularınız varsa [destek](https://azure.microsoft.com/support/options/)aracılığıyla bizimle iletişim kurun.
