---
title: Azure geçişini bir ISV aracı/Movere ile çalışacak şekilde hazırlama
description: Bu makalede, Azure geçişi 'nin bir ISV aracı veya Movere ile çalışacak şekilde nasıl hazırlanacağı ve sonra aracın nasıl kullanılacağına nasıl başlayabileceğiniz açıklanır.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: 1716db0476169e12822b3f47f7199bf6e2c4ee92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753782"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>ISV aracıyla veya Movere ile çalışmaya hazırlanma

Bir Azure geçişi projesine bir [ISV aracı](migrate-services-overview.md#isv-integration) veya Movere eklediyseniz, aracı paylaşmadan ve Azure geçişi 'ne veri gönderebilmeniz için izlemeniz gereken birkaç adım vardır. 

## <a name="check-azure-ad-permissions"></a>Azure AD izinlerini denetle

Azure Kullanıcı hesabınız bu izinlere ihtiyaç duyuyor:

- Azure kiracınızla Azure Active Directory (Azure AD) uygulaması kaydetme izni
- Abonelik düzeyinde Azure AD uygulamasına bir rol ayırma izni


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Azure AD uygulaması kaydetme izinlerini ayarlama

1. Azure AD 'de hesabınızın rolünü kontrol edin.
2. Kullanıcı rolüne sahipseniz, sol taraftaki **Kullanıcı ayarları** ' nı seçin ve kullanıcıların uygulamaları kaydedip edemeyeceğini doğrulayın. **Evet** olarak ayarlanırsa, Azure AD kiracısındaki tüm kullanıcılar bir uygulamayı kaydedebilir. **Hayır** olarak ayarlanırsa, uygulamaları yalnızca yönetici kullanıcılar kaydedebilir.   
3. İzinleriniz yoksa, Yönetici Kullanıcı Kullanıcı hesabınıza [Uygulama Yöneticisi](../active-directory/roles/permissions-reference.md#application-administrator) rolünü sağlayabilir, böylece uygulamayı kaydedebilirsiniz.
4. Araç Azure geçişi ile bağlandıktan sonra, yönetici hesabınızdan rolü kaldırabilir.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Azure AD uygulamasına rol atama izinlerini ayarlama
 
Azure aboneliğinizde hesabınız, Azure AD uygulamasına bir rol atamak için **Microsoft. Authorization/*/Write** erişimi gerektirir. 

1. Azure portalında **Abonelikler**’i açın.
2. Uygun aboneliği seçin. Bunu görmüyorsanız **genel abonelikler filtresini** seçin. 
3. **Izinlerim**' i seçin. Ardından, **bu aboneliğin tüm erişim ayrıntılarını görüntülemek için buraya tıklayın '** ı seçin.
4. **Rol atamaları**  >  **görünümünde**, izinleri denetleyin. Hesabınızın izinleri yoksa, abonelik yöneticisinden sizi [Kullanıcı erişimi Yöneticisi](../role-based-access-control/built-in-roles.md#user-access-administrator) rolüne veya [sahip](../role-based-access-control/built-in-roles.md#owner) rolüne eklemesini isteyin.

## <a name="allow-access-to-urls"></a>URL 'lere erişime izin ver

ISV araçları ve Azure veritabanı Geçiş Yardımcısı için, tabloda özetlenen genel bulut URL 'Lerine erişime izin verin. İnternet 'e bağlanmak için URL tabanlı bir ara sunucu kullanıyorsanız, proxy 'nin URL 'Leri ararken alınan CNAME kayıtlarını çözümlediği emin olun. 

**URL** | **Ayrıntılar**
--- | ---
*.portal.azure.com  | Azure portalına gidin. 
*.windows.net<br/> *.msftauth.net<br/> *.msauth.net <br/> *.microsoft.com<br/> *.live.com   | Azure aboneliğinizde oturum açın. 
*.microsoftonline.com<br/> *.microsoftonline-p.com | Gereç için Azure geçişi ile iletişim kurmak üzere Azure Active Directory (AD) uygulamalar oluşturun. 
management.azure.com | Azure geçişi projesine Azure Resource Manager çağrısı yapın.
*.servicebus.windows.net | İletileri göndermek için Gereç ve EventHub arasındaki iletişim.


## <a name="start-using-the-tool"></a>Aracı kullanmaya başlayın

1. Araç için henüz bir lisansa veya ücretsiz deneme sürümü yoksa, **Kaydet** bölümünde, **daha fazla bilgi** için Azure geçişi ' ni seçin.
2. Araçta, aracından Azure geçişi projesine bağlantı sağlamak ve Azure geçişi 'ne veri göndermek için yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Azure geçişi 'ne veri göndermek için ISV veya Movere 'inizdeki yönergeleri izleyin.

   
