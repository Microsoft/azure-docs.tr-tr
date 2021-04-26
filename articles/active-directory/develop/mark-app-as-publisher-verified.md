---
title: Uygulamayı yayımcı doğrulandı olarak işaretleme-Microsoft Identity platform | Mavisi
description: Uygulamanın yayımcı tarafından doğrulanmış olarak nasıl işaretleneceğini açıklar. Bir uygulama, yayımcı doğrulanmış olarak işaretlendiğinde, yayımcının doğrulama işlemini tamamlamış bir Microsoft İş Ortağı Ağı hesabı kullanarak kimliğini doğruladığını ve bu MPN hesabını uygulama kaydıyla ilişkilendirdiğini anlamına gelir.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 2b658cdc58777274bb14f9e8069cef2facdb0479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92363462"
---
# <a name="mark-your-app-as-publisher-verified"></a>Uygulamanızı yayımcısı doğrulandı olarak işaretleme

Bir uygulama kaydının doğrulanmış bir yayımcısı olduğunda, uygulamanın yayımcısının Microsoft İş Ortağı Ağı (MPN) hesabını kullanarak kimliğini [doğruladığını](/partner-center/verification-responses) ve bu MPN hesabını uygulama kaydıyla ilişkilendirdiğini gösterir. Bu makalede, [Yayımcı doğrulama](publisher-verification-overview.md) işleminin nasıl tamamlanacağı açıklanır.

## <a name="quickstart"></a>Hızlı Başlangıç
Microsoft İş Ortağı Ağı (MPN) zaten kaydolduysanız ve [önkoşulları](publisher-verification-overview.md#requirements)karşıladıysanız, hemen kullanmaya başlayabilirsiniz: 

1. [Multi-Factor Authentication](../fundamentals/concept-fundamentals-mfa-get-started.md) kullanarak [uygulama kayıt portalı](https://aka.ms/PublisherVerificationPreview) 'nda oturum açma

1. Bir uygulama seçin ve **marka**' e tıklayın. 

1. Yayımcıyı doğrulamak ve listelenen gereksinimleri gözden geçirmek **için MPN kimliği Ekle** ' ye tıklayın.

1. MPN KIMLIĞINIZI girip **Doğrula ve Kaydet**' e tıklayın.

Belirli avantajlar, gereksinimler ve sık sorulan sorular hakkında daha fazla ayrıntı için [genel bakış](publisher-verification-overview.md)bölümüne bakın.


## <a name="mark-your-app-as-publisher-verified"></a>Uygulamanızı yayımcısı doğrulandı olarak işaretleme
[Önkoşulları](publisher-verification-overview.md#requirements)karşıladığınızdan emin olun, sonra uygulamanızı yayımcı doğrulanmış olarak işaretlemek için aşağıdaki adımları izleyin.  

1. Yayımcı doğrulanmış olarak işaretlemek istediğiniz uygulamalarda ve Iş Ortağı Merkezi 'nde MPN hesabında değişiklik yapma yetkisi olan bir kuruluş (Azure AD) hesabına [Multi-Factor Authentication](../fundamentals/concept-fundamentals-mfa-get-started.md) 'ı kullanarak oturum açtığınızdan emin olun.

    - Azure AD 'de bu kullanıcı şu [rollerden](../roles/permissions-reference.md)birine üye olmalıdır: uygulama Yöneticisi, bulut uygulaması Yöneticisi, genel yönetici. 

    - Iş Ortağı Merkezi 'nde bu kullanıcının şu [rollere](/partner-center/permissions-overview)sahip olması gerekir: MPN Yöneticisi, hesaplar Yöneticisi veya genel yönetici (Bu, Azure AD 'de ana kopyalı bir paylaşılan roldür). 

1. Uygulama kayıt portalına gidin:  

1. Yayımcı doğrulanmış olarak işaretlemek istediğiniz uygulamaya tıklayın ve marka dikey penceresini açın. 

1. Uygulamanın [Yayımcı etki alanının](howto-configure-publisher-domain.md) ayarlandığından emin olun. 

1. Kiracıdaki yayımcı etki alanının veya DNS tarafından doğrulanan [özel etki alanının](../fundamentals/add-custom-domain.md) , MPN hesabınız için doğrulama işlemi sırasında kullanılan e-posta adresi etki alanıyla eşleştiğinden emin olun.

1. Bir yayımcıyı sayfanın en altına yakın bir **şekilde doğrulamak için MPN kimliği Ekle** ' ye tıklayın. 

1. **MPN kimliğinizi** girin. Bu MPN KIMLIĞI için olmalıdır: 

    - Doğrulama işlemini tamamlamış geçerli bir Microsoft İş Ortağı Ağı hesabı.  

    - Kuruluşunuz için Iş ortağı küresel hesabı (PGA). 

1. **Doğrula ve Kaydet**' e tıklayın. 

1. İsteğin işlenmesi için bekleyin, bu işlem birkaç dakika sürebilir. 

1. Doğrulama başarılı olduysa, yayımcı doğrulama penceresi kapanır ve bu da marka dikey penceresine geri döner. Doğrulanmış **yayımcının görünen adının** yanında mavi doğrulanmış bir rozet görürsünüz. 

1. Uygulamanızı onaylaması istenmeden kullananlar, bu işlemi başarılı bir şekilde tamamladıktan hemen sonra, bu işlem, sistem genelinde çoğaltılmasının biraz zaman alabilir. 

1. Uygulamanızda oturum açarak ve doğrulanmış rozet 'nin onay ekranında görünür olmasını sağlamak için bu işlevselliği test edin. Uygulamaya izin vermiş olan bir kullanıcı olarak oturum açtıysanız, bir onay istemi zorlamak için *Prompt = onay* sorgu parametresini kullanabilirsiniz. Bu parametre yalnızca test için kullanılmalıdır ve uygulamanızın isteklerine asla sabit olarak kodlanmamalıdır.

1. Bu işlemi, rozetinin görüntülenmesini istediğiniz diğer uygulamalar için gerektiği şekilde tekrarlayın. Bunu toplu olarak daha hızlı yapmak için Microsoft Graph kullanabilirsiniz ve PowerShell cmdlet 'leri yakında kullanılabilir olacaktır. Daha fazla bilgi için bkz. [MICROSOFT API Graph çağrıları yapma](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) . 

Bu kadar! Süreç, sonuçlar veya genel olarak özelliği hakkında herhangi bir geribildirim varsa bize bilgi verin. 

## <a name="next-steps"></a>Sonraki adımlar
Sorun yaşıyorsanız, [sorun giderme bilgilerini](troubleshoot-publisher-verification.md)okuyun.
