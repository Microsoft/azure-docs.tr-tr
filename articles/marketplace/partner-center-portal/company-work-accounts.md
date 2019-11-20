---
title: Şirket iş hesapları ve Iş Ortağı Merkezi
description: Şirketinizin Microsoft ile ayarlanmış bir iş hesabına sahip olup olmadığını kontrol edin, yeni bir iş hesabı oluşturun veya iş ortağı merkezi ile kullanmak üzere birden çok iş hesabı ayarlayın.
author: ChJenk
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 4783d3ac8ef78929621179d87f9c8948f6dd079c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811990"
---
# <a name="company-work-accounts-and-partner-center"></a>Şirket iş hesapları ve Iş Ortağı Merkezi

İş Ortağı Merkezi, birden çok kullanıcı için hesap erişimini yönetmek, izinleri denetlemek, konak grupları ve uygulamaları denetlemek ve profil verilerini korumak için Azure Active Directory (AD) kiracılar olarak da bilinen şirket iş hesaplarını kullanır. Şirketinizin iş e-posta hesabı etki alanını Iş Ortağı Merkezi hesabınıza bağlayarak, şirketinizin çalışanları kendi iş hesabı kullanıcı adlarını ve parolalarını kullanarak Market tekliflerini yönetmek için Iş Ortağı Merkezi ' nde oturum açabilir.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Şirketinizin zaten bir iş hesabına sahip olup olmadığını denetleyin

Şirketiniz Azure, Microsoft Intune veya Office 365 gibi bir Microsoft bulut hizmetine abone olduysa, iş ortağı merkezi ile kullanılabilecek bir iş e-posta hesabı etki alanınız (Azure Active Directory kiracısı olarak da bilinir) zaten vardır.

Denetlemek için şu adımları izleyin:
1. https://portal.azure.comadresindeki Azure Yönetim Portalı ' nda oturum açın.
2. Sol gezinti menüsünden **Azure Active Directory** seçin ve ardından **özel etki alanı adları**' nı seçin.
3. Zaten bir iş hesabınız varsa, etki alanı adınız listelenecektir.

Şirketinizde zaten bir iş hesabı yoksa, iş ortağı merkezi kayıt işlemi sırasında sizin için bir tane oluşturulur.

## <a name="set-up-multiple-work-accounts"></a>Birden çok iş hesabı ayarlama

Mevcut bir iş hesabını kullanmaya karar vermeden önce iş hesabındaki kaç kullanıcının iş ortağı merkezi 'ne erişmesi gerektiğini göz önünde bulundurun. İş hesabında iş ortağı merkezi 'ne erişmesi gerekmeyen kullanıcılarınız varsa, yalnızca iş ortağı merkezi 'ne erişmesi gereken kullanıcıların belirli bir hesapta temsil edilebilmesi için birden çok iş hesabı oluşturmayı düşünmek isteyebilirsiniz.

## <a name="create-a-new-work-account"></a>Yeni bir iş hesabı oluşturun

Şirketiniz için yeni bir iş hesabı oluşturmak için aşağıdaki adımları izleyin. Şirketinizin Microsoft Azure hesabınızda Yönetici izinlerine sahip olduğunuzda yardım istemeniz gerekebilir.

1. [Microsoft Azure portalında](https://portal.azure.com) oturum açın.
2. Sol gezinti menüsünden **Azure Active Directory** -> **kullanıcıları**' nı seçin.
3. **Yeni Kullanıcı** ' yı seçin ve bir ad ve e-posta adresi girerek yeni bir Azure iş hesabı oluşturun. **Dizin rolünün** **Kullanıcı** olarak ayarlandığından emin olun ve en alttaki **parolayı göster** onay kutusunu seçerek otomatik olarak oluşturulan parolayı görebilirsiniz.
4. Yeni kullanıcıyı kaydetmek için **Oluştur** ' u seçin.

Kullanıcı hesabının e-posta adresi, dizininizde doğrulanmış bir etki alanı adı olmalıdır. Sol gezinti menüsünde **özel etki alanı adları** -> **Azure Active Directory** seçerek dizininizdeki tüm doğrulanmış etki alanlarını listeleyebilirsiniz.

Azure Active Directory özel etki alanları ekleme hakkında daha fazla bilgi edinmek için bkz. [Azure AD 'de etki alanı ekleme veya ilişkilendirme](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>İş e-postasına yönelik oturum açma sorunlarını giderme

İş hesabınızda oturum açarken sorun yaşıyorsanız (Azure AD kiracınız olarak da bilinir), aşağıdaki diyagramda, durumunuza en iyi eşleşen senaryoyu bulun ve önerilen adımları izleyin.

![İş hesabı oturum açma sorunlarını giderme diyagramı](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Iş Ortağı Merkezi 'nde ticari Market hesabınızı yönetme](./manage-account.md) 
