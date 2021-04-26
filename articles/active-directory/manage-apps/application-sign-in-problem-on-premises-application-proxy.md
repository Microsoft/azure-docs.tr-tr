---
title: Azure AD uygulama proxy 'sini kullanarak şirket içi uygulamada oturum açma sorunu | Microsoft Docs
description: Azure AD Uygulama Ara Sunucusu kullanarak Azure AD ile tümleştirilmiş şirket içi bir uygulamada oturum açarken karşılaşılan yaygın sorunları giderme
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a92ce03b4a8ad241a21b29bbff4e7367d656fab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99260003"
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-ad-application-proxy"></a>Azure AD uygulama proxy 'sini kullanarak şirket içi bir uygulamada oturum açma sorunları

Şirket içi bir uygulamada oturum açarken sorun yaşıyorsanız, sorunu çözmek için aşağıdaki adımları takip edebilirsiniz.

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>Uygulamamı yükleyebiliyorum ama sayfadaki bir şey bozuk görünüyor

Aşağıdaki belgeler, bu kategoriye giren yaygın sorunların çoğunu çözmenize yardımcı olabilir.

  * [Uygulamama ulaşabiliyorum ama uygulama sayfası doğru görüntülenmiyor](application-proxy-page-appearance-broken-problem.md)
  * [Uygulamama ulaşabiliyorum ama uygulamanın yüklenmesi fazla uzun sürüyor](application-proxy-page-load-speed-problem.md)
  * [Uygulamama ulaşabiliyorum ama uygulama sayfasındaki bağlantılar çalışmıyor](application-proxy-page-links-broken-problem.md)

## <a name="im-having-a-connectivity-problem-my-application"></a>Uygulamamda bağlantı sorunu yaşıyorum
  Aşağıdaki belgeler, bu kategoriye giren yaygın sorunların çoğunu çözmenize yardımcı olabilir.
  * [Uygulamam için hangi bağlantı noktalarını açacağımı bilmiyorum](application-proxy-add-on-premises-application.md)
  * [Uygulamam için bağlayıcı grubunda çalışan bağlayıcı olmadığından sorunla karşılaştım](application-proxy-connectivity-no-working-connector.md)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>Yönetici portalında Azure AD Uygulama Ara Sunucusu'nu yapılandırırken sorun yaşıyorum
  Aşağıdaki belgeler, bu kategoriye giren yaygın sorunların çoğunu çözmenize yardımcı olabilir.
  * [Uygulama Proxy’si uygulamasını yapılandırırken zorluk sorun yaşıyorum](application-proxy-config-how-to.md)
  * [Uygulama Proxy’si uygulamamda çoklu oturum açmayı yapılandırmayı bilmiyorum](application-proxy-config-sso-how-to.md)
  * [Yönetim portalında uygulamamı oluştururken bir sorunla karşılaştım](application-proxy-config-problem.md)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>Uygulamama arka uç kimlik doğrulaması ayarlarken sorun yaşıyorum
  Aşağıdaki belgeler, bu kategoriye giren yaygın sorunların çoğunu çözmenize yardımcı olabilir.
  * [Kerberos Sınırlı Temsilini yapılandırmayı bilmiyorum](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
  * [Uygulamamı PingAccess’le nasıl yapılandıracağımı bilmiyorum](./application-proxy-ping-access-publishing-guide.md)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>Uygulamamda oturum açarken sorun yaşıyorum
  Aşağıdaki belgeler, bu kategoriye giren yaygın sorunların çoğunu çözmenize yardımcı olabilir.
  * ["Bu Şirket Uygulamasına Erişilemiyor" hatası alıyorum](application-proxy-sign-in-bad-gateway-timeout-error.md)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>Uygulama Ara Sunucusu Aracı Bağlayıcısı’nda sorun yaşıyorum
  Aşağıdaki belgeler, bu kategoriye giren yaygın sorunların çoğunu çözmenize yardımcı olabilir.
  * [Uygulama Ara Sunucusu Aracı Bağlayıcısı’nı yüklerken sorun yaşıyorum](application-proxy-connector-installation-problem.md)

## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi uygulamalara güvenli uzaktan erişim sağlama](application-proxy.md)