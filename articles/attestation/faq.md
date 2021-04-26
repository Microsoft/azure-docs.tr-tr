---
title: Sık sorulan sorular
description: Microsoft Azure kanıtlama hakkında sık sorulan soruların yanıtları
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 9b15a336e97cad1fb03a63ec34f563a4453755ff
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504201"
---
# <a name="frequently-asked-questions-for-microsoft-azure-attestation"></a>Microsoft Azure kanıtlama için sık sorulan sorular

Bu makalede, [Azure kanıtlama](overview.md)ile ilgili en sık sorulan soruların yanıtları sağlanmaktadır.

Azure sorununuz bu makalede giderilmemişse Azure Destek [sayfasında](https://azure.microsoft.com/support/options/)de bir Azure destek isteği gönderebilirsiniz.

## <a name="what-is-azure-pck-caching-service-and-its-role-in-enclave-attestation"></a>Azure PCK önbelleğe alma hizmeti ve bu hizmetin rolü, şifreleme kanıtlaması

Azure PCK önbelleğe alma hizmeti, [Azure 'Da Azure gizli bilgi işlem (ACC)](../confidential-computing/overview.md) düğümlerine yönelik Azure Güvenlik taban çizgisini tanımlar ve verileri önbelleğe alır. Önbelleğe alınan bilgiler, Azure kanıtlama tarafından, güvenilen yürütme ortamlarını (TEEs) doğrulanırken daha fazla kullanılacaktır.  

Azure PCK önbelleğe alma hizmeti:
   - Yüksek kullanılabilirlik sunar 
   - Dışarıdan barındırılan hizmetlerin ve internet bağlantılarının bağımlılıklarını azaltır.
   - Intel sertifikaları, CRL 'Ler, güvenilir bilgi Işlem tabanı (TCB) bilgilerinin en son sürümlerini getirir ve Intel 'ten ACC düğümlerinin kuşve kimlik bilgilerini tırnak içine alarak. Bu nedenle, bu hizmet Azure Güvenlik taban çizgisini, TEEs doğrulanırken Azure Güvenlik taban çizgisini doğrular ve Intel sertifikalarının doğrulanması veya iptal edilmesi nedeniyle kanıtlama başarısızlıklarını büyük ölçüde azaltır  

## <a name="is-sgx-attestation-supported-by-azure-attestation-in-non-azure-environments"></a>Azure olmayan ortamlarda Azure kanıtlama tarafından desteklenen SGX kanıtlama

Hayır. Azure kanıtlama, TEEs doğrulamak için Azure PCK önbelleğe alma hizmeti tarafından belirtilen güvenlik temeline bağımlıdır. Azure PCK önbelleğe alma hizmeti şu anda yalnızca Azure gizli bilgi işlem düğümlerini destekleyecek şekilde tasarlandı. 

## <a name="what-validations-does-azure-attestation-perform-for-attesting-sgx-enclaves"></a>Azure kanıtlama, bir attest SGX için hangi doğrulamaları gerçekleştirir?

Azure kanıtlama, farklı türlerde TEEs uzaktan test etmeye yönelik Birleşik bir çerçevedir. Azure kanıtlama:

   - İmzalı bir şifreleme teklifinin güvenilir kökünün Intel 'e ait olup olmadığını doğrular.
   - Şifreleme teklifinin Azure PCK önbelleğe alma hizmeti tarafından tanımlanan Azure Güvenlik taban çizgisini karşılayıp karşılamadığını doğrular.
   - Kanıtlama isteği nesnesindeki SHA256 'in tuttuğu verileri (EHD) karmasının, şifreleme teklifinde reportData alanının ilk 32 baytı ile eşleşip eşleşmediğini doğrular.
   - Müşterilerin bir kanıtlama sağlayıcısı oluşturmalarına ve özel bir ilke yapılandırmasına izin verir. Yukarıdaki doğrulamaların yanı sıra Azure kanıtlama, şifreleme teklifini ilkeye göre değerlendirir. İlkeler, şifreleme için yetkilendirme kuralları tanımlar ve ayrıca kanıtlama belirtecini oluşturmak için verme kuralları da dikte edin. Hedeflenen yazılımın bir kuşın içinde çalışıp çalışmadığını doğrulamak için müşteriler, şifreleme kuralları ekleyebilir ve bu da, şifreli **imza** ve **isşifte** alanları, müşteri ikililerinin değerleriyle eşleşip eşleşmediğini doğrular.

## <a name="how-can-a-verifier-obtain-the-collateral-for-sgx-attestation-supported-by-azure-attestation"></a>Bir doğrulayıcı Azure kanıtlama tarafından desteklenen SGX kanıtlama için yardımcı malzemeleri nasıl alabilir

Genel olarak, bir güven kökü olarak Intel ile kanıtlama modelleri için kanıtlama istemcisi, şifreleme kanıtlarını getirmek için API 'Leri şifreleme ile görüşecek. Şifreleme API 'Leri, bir şekilde Intel PCK önbelleğe alma hizmetini arayarak, bu düğümün Intel sertifikalarını almak için Sertifikalar, uzaktan attestable yardımcı malzemeleri oluşturmak için şifreleme kanıtlarını imzalamak için kullanılır.  

Aynı işlem Azure kanıtlama için de uygulanabilir. Ancak, Azure PCK önbelleğe alma hizmeti 'nin sunduğu avantajlardan yararlanmak için, ACC sanal makinesini yükledikten sonra [Azure DCAP kitaplığı](https://www.nuget.org/packages/Microsoft.Azure.DCAP)'nı yüklemeniz önerilir. Intel ile anlaşmayı temel alan Azure DCAP kitaplığı yüklendiğinde, şifreleme kanıtı oluşturma istekleri Intel PCK önbelleğe alma hizmetinden Azure PCK önbelleğe alma hizmeti 'ne yönlendirilir. Azure DCAP kitaplığı, Windows ve Linux tabanlı ortamlarda desteklenir.

## <a name="how-to-shift-to-azure-attestation-from-other-attestation-models"></a>Diğer kanıtlama modellerinden Azure kanıtlama 'na kaydırma

- Azure gizli bilgi işlem sanal makinesini yükledikten sonra Azure PCK önbelleğe alma hizmeti tarafından sunulan avantajlardan yararlanmak için Azure DCAP kitaplığı 'nı ([Windows/](https://www.nuget.org/packages/Microsoft.Azure.DCAP/) [Linux](https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/a/az-dcap-client/)) kurun.
- Uzak kanıtlama istemcisi 'nin yazılması gerekir, bu da şifreleme kanıtlamayı alabilir ve Azure kanıtlama 'na istek gönderebilir. Başvuru için [kod örneklerine](/samples/browse/?expanded=azure&terms=attestation) bakın 
- Kanıtlama istekleri, varsayılan sağlayıcıların veya özel kanıtlama sağlayıcılarının REST API uç noktasına gönderilebilir 
- Azure kanıtlama API 'Leri, Azure AD kimlik doğrulaması tarafından korunur. Bu nedenle, onaylamasını sağlar API 'lerini çağıran istemci, kanıtlama isteğinde geçerli bir Azure AD erişim belirteci edinip geçirebilmelidir 

## <a name="how-can-the-relying-party-verify-the-integrity-of-attestation-token"></a>Bağlı olan taraf, kanıtlama belirtecinin bütünlüğünü nasıl doğrulayabilirler

Azure kanıtlama tarafından oluşturulan kanıtlama belirteci, kendinden imzalı bir sertifika kullanılarak imzalandı. Sertifikalar bir [OpenID meta veri uç noktası](/rest/api/attestation/metadataconfiguration/get)aracılığıyla sunulur. Bağlı olan taraf, bu uç noktadan imzalama sertifikalarını alabilir ve kanıtlama belirtecinin imza doğrulamasını gerçekleştirebilir. Kanıtlama belirtecinin geçerlilik süresi 8 saattir. 

## <a name="how-to-identify-the-certificate-to-be-used-for-signature-verification-from-the-openid-metadata-endpoint"></a>OpenID meta veri uç noktasından imza doğrulaması için kullanılacak sertifikayı belirleme

OpenID meta veri uç noktasında kullanıma sunulan birden çok sertifika, Azure kanıtlama tarafından desteklenen farklı kullanım durumlarına (ör. SGX kanıtlama) karşılık gelir. [RFC 7515](https://tools.ietf.org/html/rfc7515)tarafından belirtilen standartlara göre, kanıtlama belirteci üstbilgisindeki *çocuklama* PARAMETRESI ile eşleşen anahtar kimliği (KID) olan sertifika, imza doğrulaması için kullanılır. Eşleşen bir **çocuk** bulunamazsa, OpenID meta veri uç noktası tarafından sunulan tüm sertifikaları denemek beklenir.

## <a name="is-it-possible-for-the-relying-party-to-share-secrets-with-the-validated-trusted-execution-environments-tees"></a>Bağlı olan tarafın, doğrulanan güvenilir yürütme ortamları (TEEs) ile gizli dizileri paylaşması mümkündür

Bir kuşatma içinde oluşturulan ortak anahtar, istemci tarafından Azure kanıtlama için gönderilen kanıtlama isteği nesnesinin şifreleme verileri (EHD) özelliğinde ifade edilebilir. SHA256 karmasının EHD 'nin, teklifin reportData alanında ifade edildiği doğruladıktan sonra, Azure kanıtlama, kanıtlama belirtecinde EHD 'yi içerir. Bağlı olan taraf, gizli dizileri şifrelemek ve şifreleme ile paylaşmak için doğrulanan kanıtlama yanıtından EHD 'yi kullanabilir. Daha fazla bilgi için bkz. [Azure kanıtlama temel kavramları](basic-concepts.md) .
