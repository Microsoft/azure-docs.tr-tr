---
title: Azure çoklu oturum açma (SSO) nedir?
description: Çoklu oturum açma (SSO) Azure Active Directory ile nasıl çalıştığını öğrenin. Kullanıcılardan her uygulama için parolaları hatırlamaları gerekmiyorsa, SSO kullanın. Ayrıca hesap yönetiminin yönetimini basitleştirmek için SSO kullanın.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: iangithinji
ms.reviewer: arvindh, japere
ms.custom: contperf-fy21q1
ms.openlocfilehash: 65e4b32c7c41eea6a885a09dd7ad772980595a9e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379511"
---
# <a name="what-is-single-sign-on-sso"></a>Çoklu oturum açma (SSO) nedir?

Çoklu oturum açma, kullanıcının kullandıkları her uygulamada oturum açması gerekmediği anlamına gelir. Kullanıcı bir kez oturum açar ve bu kimlik bilgileri diğer uygulamalar için de kullanılır.

Son bir kullanıcı ise, SSO ayrıntılarından çok önemli değildir. Parolanızı çok büyük bir şekilde yazmak zorunda kalmadan yalnızca üretken olmanızı sağlayan uygulamaları kullanmak istersiniz. Uygulamalarınızı şurada bulabilirsiniz: https://myapps.microsoft.com .
 
Bir Yöneticiyseniz veya BT uzmanı varsa, SSO ve Azure 'da nasıl uygulandığı hakkında daha fazla bilgi edinmek için okumaya devam edin.

## <a name="single-sign-on-basics"></a>Çoklu oturum açma temelleri
Çoklu oturum açma, kullanıcıların oturum açma ve uygulama kullanma biçiminde bir çok büyük paketlerini daha ileri sağlar. Çoklu oturum açma tabanlı kimlik doğrulama sistemleri genellikle "Modern kimlik doğrulaması" olarak adlandırılır. Modern kimlik doğrulaması ve çoklu oturum açma, kimlik ve erişim yönetimi (ıAM) adlı bir bilgi işlem kategorisine girer. Çoklu oturum açma olanağı olduğunu anlamak için bu videoyu inceleyin.

Kimlik doğrulama temelleri: temel bilgiler | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>Web uygulamalarıyla çoklu oturum açma
Web uygulamaları inanılmaz popüler. Web Apps çeşitli şirketler tarafından barındırılır ve hizmet olarak kullanılabilir hale getirilir. Bazı popüler web uygulamalarına örnek olarak Microsoft 365, GitHub ve Salesforce verilebilir ve binlerce başka kişi vardır. Kullanıcılar, bilgisayarında bir Web tarayıcısı kullanarak Web uygulamalarına erişir. Çoklu oturum açma, kullanıcıların birden çok kez oturum açmak zorunda kalmadan çeşitli web uygulamaları arasında gezinmesini mümkün kılar.

Çoklu oturum açma 'nın Web uygulamalarıyla nasıl çalıştığı hakkında bilgi edinmek için bu iki videoyu inceleyin.

Kimlik doğrulama temelleri: Web uygulamaları | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

Kimlik doğrulama temelleri: Web çoklu oturum açma | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>Buluta ve şirket içi barındırılan uygulamalara karşı
Çoklu oturum açmayı nasıl uygulayacağınızı, uygulamanın barındırıldığı yere bağlıdır. Ağ trafiğinin uygulamaya erişmek için yönlendirildiği şekilde barındırılması önemli bir uygulamadır. Bir uygulama, şirket içi uygulama olarak adlandırılan yerel ağınız üzerinden barındırılıyorsa ve erişiliyorsa, kullanıcıların uygulamayı kullanabilmesi için Internet 'e erişmesine gerek yoktur. Uygulama, bulutta barındırılan uygulama olarak adlandırılan başka bir yerde barındırılıyorsa, kullanıcıların uygulamayı kullanabilmesi için Internet 'e erişmesi gerekir.

> [!TIP]
> Bulutta barındırılan uygulamalar hizmet olarak yazılım (SaaS) uygulamaları olarak da adlandırılır. 

Bulutta barındırılan uygulamalar için çoklu oturum açma basittir. Kimlik sağlayıcısı 'nın uygulama için kullanılmakta olduğunu bilmesini sağlayabilirsiniz. Ardından, uygulamayı kimlik sağlayıcısına güvenmek üzere yapılandırırsınız. Azure AD 'yi bir uygulama için kimlik sağlayıcısı olarak kullanmayı öğrenmek için bkz. [uygulama yönetiminde hızlı başlangıç serisi](add-application-portal.md).

> [!TIP]
> Bulut ve Internet terimleri genellikle birbirinin yerine kullanılır. Bunun nedeni, ağ diyagramlarında yapmanız gerekidir. Her bileşeni çizmek uygun olmadığından büyük bilgisayar ağlarını bir diyagram üzerinde bir bulut şekliyle göstermek yaygındır. Internet, en iyi bilinen bir ağ olduğundan, bu koşulların yerine kullanılması kolay bir işlemdir. Ancak, herhangi bir bilgisayar ağı bir buluta dağıtılabilir.

Ayrıca, şirket içi tabanlı uygulamalar için çoklu oturum açma kullanabilirsiniz. Şirket içi SSO oluşturma teknolojisine uygulama proxy 'Si denir. Daha fazla bilgi edinmek için bkz. [Çoklu oturum açma seçenekleri](sso-options.md).

## <a name="multiple-identity-providers"></a>Birden çok kimlik sağlayıcısı
Çoklu kimlik sağlayıcıları arasında çalışmak için çoklu oturum açmayı ayarlarken, bu, Federasyon olarak adlandırılır. Federasyonun nasıl çalıştığını öğrenmek için bu videoya göz atın.

Kimlik doğrulama temelleri: Federasyon | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>Sonraki adımlar
* [Uygulama yönetiminde hızlı başlangıç serisi](view-applications-portal.md)
* [Çoklu oturum açma seçenekleri](sso-options.md)
