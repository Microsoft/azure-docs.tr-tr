---
title: Sık sorulan sorular-Azure doğrulanabilir kimlik bilgileri (Önizleme)
description: Doğrulanabilen kimlik bilgileri hakkında sık sorulan soruların yanıtlarını bulun
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 3c43cfb537c84fb25a6bf879d32a8034342ff605
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280726"
---
# <a name="frequently-asked-questions-faq"></a>Sık Sorulan Sorular (SSS)

Bu sayfa, doğrulanabilen kimlik bilgileri ve merkezi olmayan kimlik hakkında sık sorulan sorular içerir. Sorular aşağıdaki bölümlerde düzenlenmiştir.

- [Sözlük ve temel bilgiler](#the-basics)
- [Merkezileşmemiş kimlik hakkında kavramsal sorular](#conceptual-questions)
- [Doğrulanabilir kimlik bilgileri Önizlemeyi kullanma hakkında sorular](#using-the-preview)

> [!IMPORTANT]
> Azure Active Directory doğrulanabilir kimlik bilgileri şu anda genel önizlemede.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="the-basics"></a>Temel bilgiler

### <a name="what-is-a-did"></a>Ne oldu? 

Merkezi olmayan tanımlayıcıların (DIB 'Ler), kaynaklara erişimin güvenliğini sağlamak, kimlik bilgilerini imzalamak ve doğrulamak ve uygulama veri değişimini kolaylaştırmak için kullanılabilen tanımlayıcılardır. Geleneksel Kullanıcı adları ve e-posta adreslerinden farklı olarak, DIP 'ler varlığın kendisine aittir ve denetlenir (bir kişi, cihaz veya şirket). Dıds, herhangi bir dış kuruluştan veya güvenilir bir aracıya bağımsız olarak mevcuttur. [W3C merkezi olmayan tanımlayıcı belirtimi](https://www.w3.org/TR/did-core/) bunu daha ayrıntılı bir şekilde açıklamaktadır.

### <a name="why-do-we-need-a-did"></a>Neden bir neden gerekir?

Dijital güven temelde, katılımcıların kendilerine ait kimliklerini ve kimliklerini denetlemesini gerektirir ve kimlik tanımlayıcıda başlar.
Her gün, merkezi Tanımlayıcıdaki büyük ölçekli sistem ihlallerinin ve saldırılarının bir çağına göre, kimlik oluşturma, müşteriler ve işletmeler için kritik bir güvenlik gereksinimi haline geliyor.
Kimliklerinin sahibi olan ve denetleyen bireyler doğrulanabilir verileri ve provaları değiş tokuş edebilir. Dağıtılmış bir kimlik bilgisi ortamı, el ile ve işgücü yoğun olan çok sayıda iş işleminin otomatikleştirilmesine olanak tanır.

### <a name="what-is-a-verifiable-credential"></a>Doğrulanabilir kimlik bilgileri nedir? 

Kimlik bilgileri günlük hayatımızın bir parçasıdır; sürücü lisansları, bir motor aracı 'nı çalıştırabilme yeteneğine sahip olduğumuz için, University of Eğitim düzeyimizi sağlamak için kullanılabilir ve devlet dışı bir Passport, ülkeler arasında gezinmemizi sağlar. Doğrulanabilir kimlik bilgileri, Web üzerinde bu tür kimlik bilgilerini şifreli olarak güvenli, gizlilik ve makine doğrulanabilir bir şekilde ifade etmek için bir mekanizma sağlar. [W3C doğrulanabilir kimlik bilgileri belirtimi](https://www.w3.org/TR/vc-data-model//) bunu daha ayrıntılı bir şekilde açıklamaktadır.


## <a name="conceptual-questions"></a>Kavramsal sorular

### <a name="what-happens-when-a-user-loses-their-phone-can-they-recover-their-identity"></a>Kullanıcı telefonlarını kaybettiğinde ne olur? Kimliklerini kurtarabilir miyim?

Kullanıcılara, her biri kendi avantajları olan bir kurtarma mekanizması sunan birden çok yolu vardır. Şu anda seçenekleri değerlendiriyoruz ve kurtarmaya yönelik bir kullanıcının gizliliğini ve kendi kendine bağımsız bir şekilde sahip olması durumunda kolaylık ve güvenlik sağlayan yaklaşımları tasarlıyoruz.

### <a name="how-can-a-user-trust-a-request-from-an-issuer-or-verifier-how-do-they-know-a-did-is-the-real-did-for-an-organization"></a>Kullanıcı bir veren veya doğrulayıcı için bir isteğe nasıl güvenebilir? Bir kuruluşun gerçek bir mi olduğunu nasıl anlarım?

Son derece bilinen mevcut bir sisteme, etki alanı adına bir DıD bağlamak için, merkezi olarak [kullanılan Identity Foundation 'ın Iyi bilinen bir yapılandırma belirtimini](https://identity.foundation/.well-known/resources/did-configuration/) uyguladık. Azure Active Directory doğrulanabilir kimlik bilgileri kullanılarak oluşturulan her biri, DıD belgesinde kodlanacak bir kök etki alanı adı ekleme seçeneğine sahiptir. Daha fazla bilgi edinmek için [etki alanınızı dağıtılmış Tanımlayıcıize bağlama](how-to-dnsbind.md) başlıklı makaleyi izleyin.  

### <a name="why-does-the-verifiable-credential-preview-use-ion-as-its-did-method-and-therefore-bitcoin-to-provide-decentralized-public-key-infrastructure"></a>Doğrulanabilir kimlik bilgisi önizlemesi neden, kendi DıD yöntemi olarak kullanım için ve bu nedenle merkezi olmayan ortak anahtar altyapısı sağlamak için Bitpara.

İyon, üzerine bitpara ile çalışan, ucuz, sınırsız ve ölçeklenebilir bir tanımlayıcı katman 2 ağı. Özel bir cryptovarlık belirteci, güvenilir doğrulayıcılar veya merkezi konsensus mekanizmaları eklemeden ölçeklenebilirlik elde eder. Ana katman 1 alt tabakası için Bitpara, bir kronolojik olay kaydı sistemi için yüksek ölçüde dengeszlik düzeyi sağlamak üzere, merkezi olmayan ağın gücü nedeniyle kullanırız.

## <a name="using-the-preview"></a>Önizlemeyi kullanma

### <a name="why-must-i-use-nodejs-for-the-verifiable-credentials-preview-any-plans-for-other-programming-languages"></a>Doğrulanabilen kimlik bilgileri önizlemesi için neden NodeJS kullanmalıyım? Diğer programlama dilleri için herhangi bir plan yapılsın mı? 

Uygulama geliştiricileri için çok popüler bir platform olduğundan NodeJS seçtik. Geliştiricilerin kimlik bilgilerini vermesini ve doğrulamasını sağlayacak bir REST API 'SI yayımlayacağız. 

### <a name="is-any-of-the-code-used-in-the-preview-open-source"></a>Önizleme açık kaynağında kullanılan koddan herhangi biri mi?

Evet! Aşağıdaki depolar, hizmetlerimizin açık kaynaklı bileşenleridir.

1. [GitHub 'da dışarıdan ağaç](https://github.com/decentralized-identity/sidetree)
2. [GitHub üzerinde node Için VC SDK 'sı](https://github.com/microsoft/VerifiableCredentials-Verification-SDK-Typescript)
3. [GitHub 'da, merkezileşmemiş kimlik cezliği oluşturmak için bir Android SDK](https://github.com/microsoft/VerifiableCredential-SDK-Android)
4. [GitHub 'da, merkezileşmemiş kimlik cezliği oluşturmaya yönelik bir IOS SDK 'sı](https://github.com/microsoft/VerifiableCredential-SDK-iOS)


## <a name="what-are-the-licensing-requirements"></a>Lisanslama gereksinimleri nelerdir?

Doğrulanabilir kimlik bilgilerinin önizlemesini kullanmak için bir Azure AD P2 lisansı gerekir. Bu hizmet için fiyatlandırmayı kullanım temel alınarak faturalandırılacaksa bu geçici bir gereksinimdir. 


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory doğrulanabilir kimlik bilgilerinizi özelleştirme](credential-design.md)
