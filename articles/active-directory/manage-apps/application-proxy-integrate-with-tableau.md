---
title: Azure Active Directory Uygulama Ara Sunucusu ve Tableau | Microsoft Docs
description: Tableau dağıtımınız için uzaktan erişim sağlamak üzere Azure Active Directory (Azure AD) uygulama proxy 'Sini nasıl kullanacağınızı öğrenin.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6720a5ad963bc73e11ef7b46150e946521928c01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258634"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory Uygulama Ara Sunucusu ve Tableau 

Azure Active Directory uygulama ara sunucusu ve Tableau, uygulama ara sunucusunu, Tableau dağıtımınız için uzaktan erişim sağlamak üzere kolayca kullanabildiğinizden emin olmak için kurdu 'e sahiptir. Bu makalede, bu senaryonun nasıl yapılandırılacağı açıklanmaktadır.  

## <a name="prerequisites"></a>Önkoşullar 

Bu makaledeki senaryo şunları olduğunu varsayar:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) yapılandırıldı. 

- Bir [uygulama proxy Bağlayıcısı](application-proxy-add-on-premises-application.md) yüklendi. 

 
## <a name="enabling-application-proxy-for-tableau"></a>Tableau için uygulama proxy 'Si etkinleştiriliyor 

Uygulama proxy 'Si, Tableau 'in düzgün çalışması için gerekli olan OAuth 2,0 verme akışını destekler. Bu, artık bu uygulamayı etkinleştirmek için gereken özel adımların, aşağıdaki yayımlama adımlarını izleyerek yapılandırma dışında, daha fazla olmadığı anlamına gelir.


## <a name="publish-your-applications-in-azure"></a>Uygulamalarınızı Azure 'da yayımlayın 

Tableau yayımlamak için, Azure portalında bir uygulama yayımlamanız gerekir.

Şunun için:

- 1-8 adımlarına yönelik ayrıntılı yönergeler, bkz. [Azure AD uygulama ara sunucusu kullanarak uygulama yayımlama](application-proxy-add-on-premises-application.md). 
- Uygulama proxy 'Si alanları için Tableau değerlerinin nasıl bulunacağı hakkında bilgi için lütfen Tableau belgelerine bakın.  

**Uygulamanızı yayımlamak için**: 


1. [Azure Portal](https://portal.azure.com) bir uygulama Yöneticisi olarak oturum açın. 

2. **Kurumsal uygulamalar > Azure Active Directory** seçin. 

3. Dikey pencerenin üst kısmında **Ekle** ' yi seçin. 

4. **Şirket içi uygulama '** yı seçin. 

5. Gerekli alanları yeni uygulamanız hakkındaki bilgilerle doldurun. Ayarlar için aşağıdaki kılavuzu kullanın: 

    - **Iç URL**: Bu uygulamanın Tableau URL 'si olan BIR Iç URL 'si olmalıdır. Örneğin, `https://adventure-works.tableau.com`. 

    - **Ön kimlik doğrulama yöntemi**: Azure Active Directory (önerilir, ancak gerekli değildir). 

6. Dikey pencerenin üst kısmında **Ekle** ' yi seçin. Uygulamanız eklenir ve hızlı başlangıç menüsü açılır. 

7. Hızlı Başlangıç menüsünde, **test için Kullanıcı ata**' yı seçin ve uygulamaya en az bir kullanıcı ekleyin. Bu sınama hesabının şirket içi uygulamaya erişimi olduğundan emin olun. 

8. Test Kullanıcı atamasını kaydetmek için **ata** ' yı seçin. 

9. Seçim Uygulama Yönetimi sayfasında, **Çoklu oturum açma**' yı seçin. Açılan menüden **Tümleşik Windows kimlik doğrulaması** ' nı seçin ve Tableau yapılandırmanıza göre gerekli alanları doldurun. **Kaydet**’i seçin. 

 

## <a name="testing"></a>Test Etme 

Uygulamanız şimdi test etmeye hazırdır. Tableau yayımlamak için kullandığınız dış URL 'ye erişin ve her iki uygulamaya atanmış bir kullanıcı olarak oturum açın.



## <a name="next-steps"></a>Sonraki adımlar

Azure AD Uygulama Ara Sunucusu hakkında daha fazla bilgi için bkz. Şirket [içi uygulamalara güvenli uzaktan erişim sağlama](application-proxy.md).

