---
title: Kimlik doğrulama ve yetkilendirme sorunlarını giderme-Azure Event Hubs
description: Bu makale, Azure Event Hubs kimlik doğrulama ve yetkilendirme sorunlarını giderme hakkında bilgi sağlar.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 368fd8efda1b828f99bc41da0743768989c1a601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92329619"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>Kimlik doğrulama ve yetkilendirme sorunlarını giderme-Azure Event Hubs
[Bağlantı sorunlarını giderme](troubleshooting-guide.md) makalesinde, Azure Event Hubs bağlantı sorunlarını gidermeye yönelik ipuçları verilmektedir. Bu makalede, Azure Event Hubs kimlik doğrulama ve yetkilendirme sorunlarını gidermeye yönelik ipuçları ve öneriler sağlanmaktadır. 

## <a name="if-you-are-using-azure-active-directory"></a>Azure Active Directory kullanıyorsanız
Azure Event Hubs kimlik doğrulaması yapmak ve yetkilendirmek için Azure Active Directory (Azure AD) kullanıyorsanız, Olay Hub 'ına erişen kimliğin doğru **kaynak kapsamında** (Tüketici grubu, Olay Hub 'ı, ad alanı, kaynak grubu veya abonelik) doğru **Azure rolünün** bir üyesi olduğunu doğrulayın.

### <a name="azure-roles"></a>Azure rolleri
- Event Hubs kaynaklara yönelik tüm erişim için [Azure Event Hubs veri sahibi](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) .
- Gönderme erişimi için [Azure Event Hubs veri gönderici](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) .
- Alma erişimi için [Azure Event Hubs veri alıcısı](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) .

Şema kayıt defteri yerleşik rolleri için bkz. [şema kayıt defteri rolleri](schema-registry-overview.md#azure-role-based-access-control).

### <a name="resource-scopes"></a>Kaynak kapsamları
- **Tüketici grubu**: Bu kapsamda, rol ataması yalnızca bu varlık için geçerlidir. Şu anda Azure portal, bu düzeyde bir güvenlik sorumlusuna Azure rolü atanmasını desteklemez. 
- **Olay Hub 'ı**: rol ataması, Olay Hub 'ı varlığı ve altındaki Tüketici grubu için geçerlidir.
- **Ad alanı**: rol ataması, ad alanı altındaki tüm Event Hubs topolojisine ve onunla ilişkili tüketici grubuna yayılır.
- **Kaynak grubu**: rol atama, kaynak grubu altındaki tüm Event Hubs kaynaklarına uygulanır.
- **Abonelik**: rol ataması, abonelikteki tüm kaynak gruplarındaki tüm Event Hubs kaynaklara uygulanır.

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Event Hubs kaynaklara erişmek için Azure Active Directory ile bir uygulamanın kimliğini doğrulama](authenticate-application.md)
- [Azure Active Directory kullanarak Event Hubs kaynaklarına erişim yetkisi verme](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>Paylaşılan erişim imzaları (SAS) kullanıyorsanız
[SAS](authenticate-shared-access-signature.md)kullanıyorsanız, şu adımları izleyin: 

- Kullandığınız SAS anahtarının doğru olduğundan emin olun. Aksi takdirde, doğru SAS anahtarını kullanın.
- Anahtarın doğru izinlere sahip olduğunu doğrulayın (gönderme, alma veya yönetme). Aksi takdirde, gerekli izne sahip bir anahtar kullanın. 
- Anahtarın süresi dolmuşsa emin olun. Süre sonundan önce SAS 'yı yenilemeniz önerilir. İstemci ile Event Hubs hizmet düğümleri arasında saat çarpıklığı varsa, istemci onu yeniden oluşturmadan önce kimlik doğrulama belirtecinin kullanım süresini dolabilirler. Geçerli uygulama hesapları saati 5 dakikaya kadar eğit, diğer bir deyişle istemci süresi dolmadan 5 dakika önce bu belirteci yeniler. Bu nedenle, saat çarpıklığı 5 dakikadan büyükse, istemci aralıklı kimlik doğrulama başarısızlıklarını gözlemleyebilirsiniz.
- **SAS başlangıç zamanı** **Şu anda** olarak ayarlandıysa, saat farkı (farklı makinelerde geçerli zaman farkları) nedeniyle ilk birkaç dakika boyunca aralıklı hatalarla karşılaşabilirsiniz. Başlangıç saatini geçmişte en az 15 dakika olacak şekilde ayarlayın veya hiç ayarlanmayın. Aynı genellikle süre sonu zamanı için de geçerlidir. 

Daha fazla bilgi için aşağıdaki makaleleri inceleyin: 

- [Paylaşılan erişim imzalarını (SAS) kullanarak kimlik doğrulaması](authenticate-shared-access-signature.md)yapın. 
- [Paylaşılan erişim Imzalarını kullanarak Event Hubs kaynaklarına erişimi yetkilendirme](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelere bakın:

* [Bağlantı sorunlarını giderme](troubleshooting-guide.md)
