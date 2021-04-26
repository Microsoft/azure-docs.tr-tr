---
title: Şirket içi Azure AD parola korumasını etkinleştirme
description: Şirket içi Active Directory Domain Services ortamı için Azure AD parola korumasını etkinleştirmeyi öğrenin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e11fa1bb9f1d3a3986a19da8ed44229829ec81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741788"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Şirket içi Azure Active Directory parola korumasını etkinleştir

Kullanıcılar genellikle okul, spor ekibi veya çok büyük kişi gibi yaygın yerel sözcükleri kullanan parolalar oluşturur. Bu parolalar kolayca tahmin edilir ve sözlük tabanlı saldırılara karşı zayıftır. Kuruluşunuzda güçlü parolalar zorlamak için Azure Active Directory (Azure AD) parola koruması, genel ve özel yasaklanmış parola listesi sağlar. Bu yasaklanmış parola listesinde bir eşleşme varsa parola değiştirme isteği başarısız olur.

Şirket içi Active Directory Domain Services (AD DS) ortamınızı korumak için Azure AD parola koruması 'nı şirket içi DC 'niz ile çalışacak şekilde yükleyip yapılandırabilirsiniz. Bu makalede, şirket içi ortamınız için Azure AD parola korumasının nasıl etkinleştirileceği gösterilmektedir.

Azure AD parola korumasının şirket içi bir ortamda nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Windows Server Için Azure AD parola korumasını zorlama Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, şirket içi ortamınız için Azure AD parola korumasının nasıl etkinleştirileceği gösterilmektedir. Bu makaleyi tamamlamadan önce, [Azure AD parola koruma proxy hizmetini ve DC aracılarını](howto-password-ban-bad-on-premises-deploy.md) şirket içi AD DS ortamınıza yükleyip kaydettirin.

## <a name="enable-on-premises-password-protection"></a>Şirket içi parola korumasını etkinleştir

1. [Azure Portal](https://portal.azure.com) oturum açın ve   >  **güvenlik**  >  **kimlik doğrulama yöntemleri**  >  **parola korumasına** Azure Active Directory gidin.
1. **Windows Server Active Directory parola korumasını etkinleştir** seçeneğini *Evet* olarak ayarlayın.

    Bu ayar *Hayır* olarak ayarlandığında, tüm DAĞıTıLAN Azure AD parola koruma DC aracıları tüm parolaların olduğu gibi kabul edildiği bir quiescent moduna geçer. Doğrulama etkinliği gerçekleştirilmez ve denetim olayları oluşturulmaz.

1. Başlangıçta **modunun** *Denetim* olarak ayarlanması önerilir. Özelliği ve kuruluşunuzdaki kullanıcılar üzerindeki etkisini rahat hale getiriyorsanız, **modu** *Zorlanmış* olarak geçirebilirsiniz. Daha fazla bilgi için, [işlem modlarında](#modes-of-operation)aşağıdaki bölüme bakın.
1. Hazırlanıyor, **Kaydet**' i seçin.

    [![Azure portal kimlik doğrulama yöntemleri altında şirket içi parola korumasını etkinleştirin](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png)](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>İşlem modları

Şirket içi Azure AD parola korumasını etkinleştirdiğinizde, *Denetim* modunu veya *zorlama* modunu kullanabilirsiniz. İlk dağıtımın ve testin her zaman denetim modunda başlamasını öneririz. Daha sonra, var olan bir işlem işleminin *zorla* modu etkinleştirildikten sonra olumsuz olup olmayacağını tahmin etmek için olay günlüğündeki girişlerin izlenmesi gerekir.

### <a name="audit-mode"></a>Denetim modu

*Denetim* modu, "durum" modunda yazılımı çalıştırmanın bir yolu olarak hazırlanmıştır. Her Azure AD parola koruması DC Aracısı hizmeti, geçerli etkin ilkeye göre gelen bir parolayı değerlendirir.

Geçerli ilke denetim modunda olacak şekilde yapılandırıldıysa, "kötü" parolalar olay günlüğü iletilerine neden olur ancak işlenir ve güncelleştirilir. Bu davranış, denetim ve zorlama modu arasındaki tek farktır. Diğer tüm işlemler aynı şekilde çalışır.

### <a name="enforced-mode"></a>Zorlanan mod

*Zorlanan* mod, son yapılandırma olarak tasarlanmıştır. Denetim modunda olduğu gibi, her Azure AD parola koruması DC Aracısı hizmeti, gelen parolaları Şu anda etkin olan ilkeye göre değerlendirir. Ancak, zorlanan mod etkinleştirildiğinde, ilkeye göre güvenli olmayan olarak kabul edilen bir parola reddedilir.

Azure AD parola koruması DC Aracısı tarafından zorlanan modda bir parola reddedildiğinde, Son Kullanıcı, parolasının geleneksel şirket içi parola karmaşıklığı zorlaması tarafından reddedildiğini görmek gibi benzer bir hata görür. Örneğin, bir Kullanıcı Windows oturum açma veya parola değiştirme ekranında aşağıdaki geleneksel hata iletisini görebilir:

*"Parola güncelleştirilemiyor. Yeni parola için girilen değer, etki alanının uzunluk, karmaşıklık veya geçmiş gereksinimlerini karşılamıyor. "*

Bu ileti birkaç olası sonuç için yalnızca bir örnektir. Belirli bir hata iletisi, güvenli olmayan bir parola ayarlamaya çalışan gerçek yazılıma veya senaryoya bağlı olarak değişebilir.

Etkilenen son kullanıcıların, yeni gereksinimleri anlamak ve güvenli parolalar seçmek için BT personeli ile çalışması gerekebilir.

> [!NOTE]
> Zayıf bir parola reddedildiğinde, Azure AD parola korumasının istemci makine tarafından görüntülenmekte olan belirli bir hata iletisi üzerinde denetimi yoktur.

## <a name="next-steps"></a>Sonraki adımlar

Kuruluşunuzun yasaklanmış parola listesini özelleştirmek için bkz. [Azure AD parola koruması özel yasaklanmış parola listesini yapılandırma](tutorial-configure-custom-password-protection.md).

Şirket içi olayları izlemek için bkz. [on-Prem Azure AD parola korumasını izleme](howto-password-ban-bad-on-premises-monitor.md).
