---
title: Azure Anahtar Kasası erişim ilkesi sorunlarını giderme
description: Azure Anahtar Kasası erişim ilkesi sorunlarını giderme
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: c5fab8b856ff9c82a0de887dc9c322dbf541348b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791416"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Azure Anahtar Kasası erişim ilkesi sorunlarını giderme

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="i-am-not-able-to-list-or-get-secretskeyscertificate-i-am-seeing-something-went-wrong-error"></a>Gizli dizileri/anahtarları/sertifikaları listeedemiyorum veya alamıyoruz. "Bir sorun oluştu.." görüyorum Hatayla.
Gizli anahtarı listeleme/alma/oluşturma veya erişimle ilgili sorun yaşıyorsanız, bu işlemi gerçekleştirmek için tanımlanmış erişim ilkesine sahip olduğunuzdan emin olun: [Key Vault erişim ilkeleri](./assign-access-policy-cli.md)

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Anahtar kasalarına nasıl ve ne zaman erişildiğini nasıl tanımlayabilirim?

Bir veya daha fazla Anahtar Kasası oluşturduktan sonra muhtemelen anahtar kasalarınızın nasıl ve ne zaman erişildiğini ve kim tarafından yapılacağını izlemek isteyeceksiniz. Azure Key Vault günlük kaydını etkinleştirerek izleme yapabilirsiniz, günlük kaydını etkinleştirmek için adım adım kılavuz için [daha fazla bilgi edinin](./logging.md).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Kasa kullanılabilirliğini, hizmet gecikme sürelerini veya Anahtar Kasası için diğer performans ölçümlerini nasıl izleyebilirim?

Hizmetinizi ölçeklendirmeye başladığınızda, anahtar kasanıza gönderilen isteklerin sayısı artacaktır. Bu talep, isteklerinizin gecikme süresini artırmak ve olağanüstü durumlarda, hizmetinizin performansını etkileyecek olan isteklerinizin kısıtlanmasına neden olur. Anahtar Kasası performans ölçümlerini izleyebilir ve belirli eşiklere göre uyarı alabilir, izlemeyi yapılandırmaya yönelik adım adım kılavuz için [daha fazla](./alert.md)bilgi edinebilirsiniz.

### <a name="i-am-not-able-to-modify-access-policy-how-can-it-be-enabled"></a>Erişim ilkesini değiştiremem mümkün değil mi?
Kullanıcının erişim ilkesini değiştirmek için yeterli AAD izinleri olması gerekir. Bu durumda, kullanıcının daha yüksek katkıda bulunan rolüne sahip olması gerekir.

### <a name="i-am-seeing-unknown-policy-error-what-does-that-mean"></a>' Bilinmeyen Ilke ' hatası görüyorum. Bu ne anlama gelir?
Erişim ilkesini bilinmeyen bölümde görmekten iki farklı olasılık vardır:
* Kullanıcının mevcut olmadığı bir nedenden dolayı, erişimi olan önceki bir kullanıcı olabilir.
* Erişim ilkesi PowerShell aracılığıyla eklenirse ve hizmet sorumlusu yerine uygulama ObjectID için erişim ilkesi eklendiyse.

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Anahtar Kasası nesnesine göre erişim denetimini nasıl atayabilirim? 

Gizli/anahtar/sertifika erişim denetimi özelliğinin kullanılabilirliğine buradan bildirim verilecektir, [daha fazla bilgi edinin](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Erişim denetimi ilkesini kullanarak Anahtar Kasası kimlik doğrulamasını nasıl sağlayabilirim?

Key Vault için bulut tabanlı bir uygulamanın kimlik doğrulamasının en kolay yolu, yönetilen bir kimlikle; Ayrıntılar için bkz. [Azure Key Vault Için kimlik doğrulama](authentication.md) .
Şirket içi bir uygulama oluşturuyorsanız, yerel geliştirme yapıyor veya yönetilen bir kimlik kullanmıyorsanız, bunun yerine bir hizmet sorumlusunu el ile kaydedebilir ve bir erişim denetim ilkesi kullanarak anahtar kasanıza erişim sağlayabilirsiniz. Bkz. [erişim denetimi Ilkesi atama](assign-access-policy-portal.md).

### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>AD grubuna Anahtar Kasası erişimine nasıl izin veririm?

Azure CLı `az keyvault set-policy` komutunu veya Azure PowerShell Set-AzKeyVaultAccessPolicy cmdlet 'ini kullanarak anahtar kasanıza ad grubu izinleri verin. Bkz. [erişim Ilkesi atama-CLI](assign-access-policy-cli.md) ve [erişim Ilkesi atama-PowerShell](assign-access-policy-powershell.md).

Uygulamanın Ayrıca anahtar kasasına atanmış en az bir kimlik ve erişim yönetimi (ıAM) rolü olması gerekir. Aksi takdirde, oturum açamayacak ve aboneliğe erişmek için yetersiz haklarla başarısız olacak. Yönetilen kimlikleri olan Azure AD grupları belirteçleri yenilemek için sekiz saate kadar sürebilir ve geçerli hale gelir.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Mevcut erişim ilkelerini silmeden ARM şablonuyla Key Vault nasıl yeniden dağırım?

Key Vault yeniden dağıtımı, Key Vault erişim ilkesini siler ve ARM şablonundaki erişim ilkesiyle değiştirir. Key Vault erişim ilkeleri için artımlı bir seçenek yoktur. Key Vault erişim ilkelerini korumak için, Key Vault içindeki mevcut erişim ilkelerini okumanız ve ARM şablonunu bu ilkelerle doldurmanız gerekir.

Bu senaryo için yardımcı olabilecek başka bir seçenek, erişim ilkelerine alternatif olarak Azure RBAC ve rolleri kullanıyor. Azure RBAC ile, ilkeyi yeniden belirtmeden anahtar kasasını yeniden dağıtabilirsiniz. Bu [çözümü daha fazla bulabilirsiniz.](./rbac-guide.md)

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Aşağıdaki hata türleri için önerilen sorun giderme adımları

* HTTP 401: Kimliği Doğrulanmamış İstek - [Sorun giderme adımları](rest-error-codes.md#http-401-unauthenticated-request)
* HTTP 403: Yetersiz İzinler - [Sorun giderme adımları](rest-error-codes.md#http-403-insufficient-permissions)
* HTTP 429: Çok Fazla İstek Var - [Sorun giderme adımları](rest-error-codes.md#http-429-too-many-requests)
* Anahtar Kasası için erişim silme iznine sahip olup olmadığını denetleyin: bkz. [erişim Ilkesi atama-CLI](assign-access-policy-cli.md), [erişim Ilkesi atama-PowerShell](assign-access-policy-powershell.md)veya [erişim ilkesi atama-Portal](assign-access-policy-portal.md).
* Kodda anahtar kasasının kimliğini doğrulama sorununuz varsa [Kimlik Doğrulaması SDK’sını](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html) kullanın

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Anahtar Kasası kısıtlanarak uygulamam gereken en iyi uygulamalar nelerdir?
[Burada](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits) belgelenen en iyi uygulamaları izleyin

## <a name="next-steps"></a>Sonraki Adımlar

Anahtar Kasası kimlik doğrulama hatalarıyla ilgili sorunları giderme hakkında bilgi edinin: [Key Vault sorun giderme kılavuzu](rest-error-codes.md).