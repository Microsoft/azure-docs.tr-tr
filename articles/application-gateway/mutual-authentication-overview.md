---
title: Azure Application Gateway karşılıklı kimlik doğrulamasına genel bakış
description: Bu makale Application Gateway üzerinde karşılıklı kimlik doğrulamasına genel bakış.
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.date: 03/30/2021
ms.topic: conceptual
ms.author: caya
ms.openlocfilehash: a63697c2c9c32dfb48e77248a5e7a601677b8b3e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231621"
---
# <a name="overview-of-mutual-authentication-with-application-gateway-preview"></a>Application Gateway ile karşılıklı kimlik doğrulamasına genel bakış (Önizleme)

Karşılıklı kimlik doğrulaması veya istemci kimlik doğrulaması, Application Gateway istemci gönderme isteklerinin kimliğini doğrulamasına izin verir. Genellikle yalnızca istemci Application Gateway kimlik doğrulaması yapılır; karşılıklı kimlik doğrulama hem istemcinin hem de Application Gateway birbirlerinin kimliğini doğrulayabilmesi için izin verir. 

> [!NOTE]
> TLS 1,2, gelecekte uygulanan olacağı için TLS 1,2 ' i karşılıklı kimlik doğrulaması ile kullanmanızı öneririz. 

## <a name="mutual-authentication"></a>Karşılıklı kimlik doğrulama

Application Gateway, güvenilir bir istemci CA sertifikalarını Application Gateway karşıya yükleyebileceğiniz sertifika tabanlı karşılıklı kimlik doğrulamasını destekler ve ağ geçidi, ağ geçidine istek gönderen istemcinin kimliğini doğrulamak için bu sertifikayı kullanır. IoT genelinde IoT kullanım durumlarında ve artırılmış güvenlik gereksinimlerinden dolayı, karşılıklı kimlik doğrulama, hangi istemcilerin Application Gateway konuştuğunu yönetmenizi ve denetlemenizi sağlayan bir yol sağlar. 

Karşılıklı kimlik doğrulamasını yapılandırmak için, bir SSL profilinin istemci kimlik doğrulama bölümünün bir parçası olarak bir güvenilen istemci CA sertifikasının yüklenmesi gerekir. Bu durumda, karşılıklı kimlik doğrulamasının yapılandırılmasını tamamlaması için SSL profilinin bir dinleyiciyle ilişkilendirilmesi gerekir. Karşıya yüklediğiniz istemci sertifikasında her zaman bir kök CA sertifikası olmalıdır. Bir sertifika zincirini da karşıya yükleyebilirsiniz ancak zincir, istediğiniz sayıda ara CA sertifikasına ek olarak bir kök CA sertifikası içermelidir. 

Örneğin, istemci sertifikanız bir kök CA sertifikası, birden çok ara CA sertifikası ve bir yaprak sertifikası içeriyorsa, kök CA sertifikasının ve tüm ara CA sertifikalarının tek bir dosyada Application Gateway üzerine yüklendiğinden emin olun. Güvenilen istemci CA sertifikasını ayıklama hakkında daha fazla bilgi için bkz. [Güvenilen ISTEMCI CA sertifikalarını ayıklama](./mutual-authentication-certificate-management.md).

Kök CA ve ara CA sertifikaları içeren bir sertifika zinciri yüklüyorsanız, sertifika zinciri ağ geçidine ped veya CER dosyası olarak yüklenmelidir. 

> [!NOTE] 
> Karşılıklı kimlik doğrulaması yalnızca Standard_v2 ve WAF_v2 SKU 'Larında kullanılabilir. 

### <a name="certificates-supported-for-mutual-authentication"></a>Karşılıklı kimlik doğrulaması için desteklenen sertifikalar

Application Gateway aşağıdaki sertifika türlerini destekler:

- CA (sertifika yetkilisi) sertifikası: CA sertifikası, bir sertifika yetkilisi (CA) tarafından verilen dijital bir sertifikadır
- Otomatik olarak imzalanan CA sertifikaları: Istemci tarayıcıları bu sertifikalara güvenmez ve kullanıcıyı sanal hizmetin sertifikasının bir güven zincirinin parçası olmadığı konusunda uyarır. Otomatik olarak imzalanan CA sertifikaları, yöneticilerin istemcileri denetladığı ve tarayıcının güvenlik uyarılarını güvenle atlayabileceği test veya ortamlar için uygundur. Üretim iş yükleri asla otomatik olarak imzalanan CA sertifikaları kullanmamalıdır.

Karşılıklı kimlik doğrulamasını ayarlama hakkında daha fazla bilgi için bkz. [Application Gateway karşılıklı kimlik doğrulamayı yapılandırma](./mutual-authentication-portal.md).

> [!IMPORTANT]
> Karşılıklı kimlik doğrulaması kullanırken Application Gateway tüm güvenilen istemci CA sertifika zincirini karşıya yüklediğinizden emin olun. 

## <a name="additional-client-authentication-validation"></a>Ek istemci kimlik doğrulama doğrulaması

### <a name="verify-client-certificate-dn"></a>İstemci sertifikası DN 'sini doğrula

İstemci sertifikasının anında vereni doğrulama seçeneğiniz vardır ve yalnızca Application Gateway verene güvenmesini sağlar. Bu seçenekler varsayılan olarak kapalıdır, ancak bunu Portal, PowerShell veya Azure CLı aracılığıyla etkinleştirebilirsiniz. 

İstemci sertifikasının anında vereni doğrulamak için Application Gateway etkinleştirmeyi seçerseniz, istemci sertifikası verenin DN 'nin karşıya yüklenen sertifikalardan nasıl ayıklanıp ayıklanabileceği aşağıda verilmiştir. 
* **Senaryo 1:** Sertifika zinciri şunları içerir: kök sertifika-ara sertifika-yaprak sertifikası 
    * *Ara sertifikanın* konu adı, Application Gateway istemci sertifikası verenin DN 'si olarak ayıklanacaktır ve bu, doğrulanacak. 
* **Senaryo 2:** Sertifika zinciri şunları içerir: kök sertifika-intermediate1 Certificate-intermediate2 Certificate-yaprak sertifikası
    * *Intermediate2 sertifikanın* konu adı, istemci sertifikası verenin DN 'si olarak ayıklanacaktır ve doğrulanacak. 
* **Senaryo 3:** Sertifika zinciri şunları içerir: kök sertifika-yaprak sertifikası 
    * *Kök sertifikanın* konu adı ayıklanacaktır ve istemci SERTIFIKASı veren DN olarak kullanılacaktır.
* **Senaryo 4:** Aynı dosyada aynı uzunlukta birden çok sertifika zincirleri. Zincir 1 şunları içerir: kök sertifika-intermediate1 sertifika-yaprak sertifikası. 2. zincir şunları içerir: kök sertifika-intermediate2 sertifika-yaprak sertifikası. 
    * *Intermediate1 sertifikanın* konu adı, istemci sertifikası verenin DN 'si olarak ayıklanır.  
* **Senaryo 5:** Aynı dosyada farklı uzunluklardan oluşan birden çok sertifika zincirleri. Zincir 1 şunları içerir: kök sertifika-intermediate1 sertifika-yaprak sertifikası. 2. zincir, kök sertifika-intermediate2 Certificate-intermediate3 sertifika-yaprak sertifikası içerir. 
    * *Intermediate3 sertifikanın* konu adı, istemci sertifikası verenin DN 'si olarak ayıklanır. 

> [!IMPORTANT]
> Dosya başına yalnızca bir sertifika zinciri yüklemeniz önerilir. İstemci sertifikası DN doğrulamasını etkinleştirirseniz bu özellikle önemlidir. Tek bir dosyada birden çok sertifika zincirini karşıya yükleyerek, senaryo dört veya beş bir yerde sona acaksınız ve sunulan istemci sertifikası zincirlerden ayıklanan istemci sertifikası verenin DN Application Gateway 'si ile eşleşmediği zaman daha sonra bir sorunla karşılaşabilirsiniz. 

Güvenilen istemci CA sertifikası zincirlerinin nasıl ayıklanacağı hakkında daha fazla bilgi için bkz. [Güvenilen ISTEMCI CA sertifika zincirlerini ayıklama](./mutual-authentication-certificate-management.md).

## <a name="server-variables"></a>Sunucu değişkenleri 

Karşılıklı kimlik doğrulaması ile, istemci sertifikası hakkında bilgileri Application Gateway arkasındaki arka uç sunucularına geçirmek için kullanabileceğiniz ek sunucu değişkenleri vardır. Hangi sunucu değişkenlerinin kullanılabildiği ve bunların nasıl kullanılacağı hakkında daha fazla bilgi için [sunucu değişkenlerini](./rewrite-http-headers-url.md#mutual-authentication-server-variables-preview)inceleyin.

## <a name="next-steps"></a>Sonraki adımlar

Karşılıklı kimlik doğrulaması hakkında bilgi aldıktan sonra, karşılıklı kimlik doğrulaması kullanarak bir Application Gateway oluşturmak için [PowerShell 'de karşılıklı kimlik doğrulamasıyla Application Gateway yapılandırma](./mutual-authentication-powershell.md) bölümüne gidin. 

