---
title: Azure Active Directory kayıtlı uygulamanızı bir Kullanıcı kümesiyle sınırlayın
titleSuffix: Microsoft identity platform
description: Azure AD 'de kayıtlı uygulamalarınıza erişimi seçili bir kullanıcı kümesine kısıtlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: kalyankrishna1
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c768730a00d424333e735bb4f6a385d294f38513
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803957"
---
# <a name="how-to-restrict-your-app-to-a-set-of-users"></a>Nasıl yapılır: uygulamanızı bir Kullanıcı kümesiyle kısıtlama

Bir Azure Active Directory (Azure AD) kiracısında kayıtlı uygulamalar, varsayılan olarak, başarıyla kimlik doğrulayan kiracının tüm kullanıcıları tarafından kullanılabilir.

Benzer şekilde, [çok kiracılı](howto-convert-app-to-be-multi-tenant.md) bir uygulama söz konusu olduğunda, bu uygulamanın SAĞLANDıĞı Azure AD kiracısındaki tüm kullanıcılar ilgili kiracısında başarıyla kimlik doğrulamasından sonra bu uygulamaya erişebilir.

Kiracı yöneticileri ve geliştiricileri, genellikle bir uygulamanın belirli bir Kullanıcı kümesiyle kısıtlanması gereken gereksinimlere sahiptir. Geliştiriciler rol tabanlı Access Control (RBAC) gibi popüler yetkilendirme düzenlerini kullanarak aynısını gerçekleştirebilir, ancak bu yaklaşım geliştiricinin bir bölümünde önemli miktarda iş gerektirir.

Azure AD, kiracı yöneticilerinin ve geliştiricilerin bir uygulamayı Kiracıdaki belirli bir kullanıcı veya güvenlik grubu kümesiyle kısıtlamasına olanak sağlar.

## <a name="supported-app-configurations"></a>Desteklenen uygulama konfigürasyonları

Bir uygulamayı bir Kiracıdaki belirli bir kullanıcı veya güvenlik grubu kümesiyle kısıtlama seçeneği aşağıdaki uygulama türleri ile birlikte kullanılabilir:

- SAML tabanlı kimlik doğrulaması ile federe çoklu oturum açma için yapılandırılmış uygulamalar
- Azure AD ön kimlik doğrulaması kullanan uygulama proxy uygulamaları
- Bir kullanıcı veya yönetici bu uygulamaya alındıktan sonra, OAuth 2.0/OpenID Connect kimlik doğrulaması kullanan doğrudan Azure AD uygulama platformunda oluşturulan uygulamalar.

     > [!NOTE]
     > Bu özellik yalnızca Web uygulaması/Web API 'SI ve kurumsal uygulamalar için kullanılabilir. [Yerel](quickstart-v1-integrate-apps-with-azure-ad.md) olarak kaydedilen uygulamalar, Kiracıdaki bir grup kullanıcı veya güvenlik grubu ile kısıtlanamaz.

## <a name="update-the-app-to-enable-user-assignment"></a>Kullanıcı atamasını etkinleştirmek için uygulamayı güncelleştirme

Etkin Kullanıcı atamasına sahip bir uygulama oluşturmanın iki yolu vardır. Biri **genel yönetici** rolü gerektirir, ikincisi değildir.

### <a name="enterprise-applications-requires-the-global-adminstrator-role"></a>Kurumsal uygulamalar (genel yönetici rolü gerektirir)

1. [**Azure Portal**](https://portal.azure.com/) gidin ve **genel yönetici**olarak oturum açın.
1. Üstteki çubukta, oturum açma hesabı ' nı seçin. 
1. **Dizin**altında, uygulamanın KAYDEDILECEĞI Azure AD kiracısını seçin.
1. Sol taraftaki Gezinti bölmesinde **Azure Active Directory**' yi seçin. Gezinti bölmesinde Azure Active Directory yoksa, aşağıdaki adımları izleyin:

    1. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçin.
    1. Filtre arama kutusuna **Azure Active Directory** yazın ve sonra sonuçtan **Azure Active Directory** öğesini seçin.

1. **Azure Active Directory** bölmesinde, **Azure Active Directory** sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' ı seçin.
1. Tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

     Burada görünmesini istediğiniz uygulamayı görmüyorsanız, listeyi kısıtlamak için **tüm uygulamalar** listesinin en üstündeki çeşitli filtreleri kullanın veya uygulamanızı bulmak için listeyi aşağı kaydırın.

1. Listeden bir kullanıcı veya güvenlik grubu atamak istediğiniz uygulamayı seçin.
1. Uygulamanın **genel bakış** sayfasında, uygulamanın sol taraftaki gezinti menüsünden **Özellikler** ' i seçin.
1. **Kullanıcı atamasının gerekli** ayarını bulun ve **Evet**olarak ayarlayın. Bu seçenek **Evet**olarak ayarlandığında, kullanıcıların bu uygulamaya erişebilmesi için önce bu uygulamaya atanması gerekir.
1. Bu yapılandırma değişikliğini kaydetmek için **Kaydet** ' i seçin.

### <a name="app-registration"></a>Uygulama kaydı

1. [**Azure Portal**](https://portal.azure.com/)gidin.
1. Üstteki çubukta, oturum açma hesabı ' nı seçin. 
1. **Dizin**altında, uygulamanın KAYDEDILECEĞI Azure AD kiracısını seçin.
1. Sol taraftaki Gezinti bölmesinde **Azure Active Directory**' yi seçin.
1. **Azure Active Directory** bölmesinde, **Azure Active Directory** sol taraftaki gezinti menüsünden **uygulama kayıtları** ' nı seçin.
1. Yönetmek istediğiniz uygulamayı oluşturun veya seçin. Bu uygulama kaydının **sahibi** olmanız gerekir.
1. Uygulamanın **genel bakış** sayfasında, sayfanın üst kısmındaki temel parçalar altında bulunan **Yerel Dizin bağlantısındaki yönetilen uygulamayı** izleyin. Bu, sizi uygulama kaydlarınızın _yönetilen kurumsal uygulamasına_ götürür.
1. Sol taraftaki Gezinti dikey penceresinden **Özellikler**' i seçin.
1. **Kullanıcı atamasının gerekli** ayarını bulun ve **Evet**olarak ayarlayın. Bu seçenek **Evet**olarak ayarlandığında, kullanıcıların bu uygulamaya erişebilmesi için önce bu uygulamaya atanması gerekir.
1. Bu yapılandırma değişikliğini kaydetmek için **Kaydet** ' i seçin.

## <a name="assign-users-and-groups-to-the-app"></a>Uygulamaya Kullanıcı ve Grup atama

Uygulamanızı Kullanıcı atamasını etkinleştirecek şekilde yapılandırdıktan sonra, uygulamaya gidip kullanıcıları ve grupları uygulamaya atayabilirsiniz.

1. Uygulamanın sol taraftaki gezinti menüsünde **Kullanıcılar ve gruplar** bölmesini seçin.
1. **Kullanıcılar ve gruplar** listesinin en üstünde, **atama Ekle** bölmesini açmak için **Kullanıcı Ekle** düğmesini seçin.
1. **Atama Ekle** bölmesinden **Kullanıcılar** seçicisini seçin. 

     Kullanıcı ve güvenlik gruplarının listesi, belirli bir kullanıcı veya grubu aramak ve bulmak için bir metin kutusuyla birlikte gösterilir. Bu ekran, tek bir go içinde birden çok kullanıcı ve grup seçmenizi sağlar.

1. Kullanıcıları ve grupları seçmeyi tamamladıktan sonra, sonraki bölüme geçmek için alt kısımdaki **Seç** düğmesine basın.
1. Uygulamanın kullanıcı ve grupların atamalarını sona erdirmeyi sağlamak için alt kısımdaki **ata** düğmesine basın. 
1. Eklediğiniz kullanıcı ve grupların, güncelleştirilmiş **Kullanıcılar ve gruplar** listesinde görüntülendiğini doğrulayın.

