---
title: Microsoft Identity platform kimlik doğrulama kitaplıkları | Mavisi
description: Microsoft Identity platform ile uyumlu istemci kitaplıkları ve ara yazılım listesi. Uygulamalarınıza Kullanıcı oturum açma (kimlik doğrulama) ve korumalı Web API erişimi (yetkilendirme) için destek eklemek üzere bu kitaplıkları kullanın.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 03/30/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 321a5c473df30dd6f00bbcd1294d48ce8da34009
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060389"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft Identity platform kimlik doğrulama kitaplıkları

Aşağıdaki tablolarda çeşitli uygulama türleri için Microsoft kimlik doğrulama kitaplığı desteği gösterilmektedir. Bunlar kitaplık kaynak koduna bağlantılar, örneğin, uygulamanızın projesi için paketin alınacağı ve kitaplığın Kullanıcı oturum açma (kimlik doğrulama), korumalı Web API 'Lerine (yetkilendirme) veya her ikisine de destekleyip desteklemediğini içerir.

Microsoft Identity platformu, OpenID Foundation tarafından [sertifikalı bir OpenID sağlayıcısı](https://openid.net/certification/)olarak onaylandı. Microsoft kimlik doğrulama kitaplığı (MSAL) veya Microsoft tarafından desteklenen başka bir kitaplık dışında bir kitaplık kullanmayı tercih ederseniz, [sertifikalı bir OpenID Connect uygulamasıyla](https://openid.net/developers/certified/)bir tane seçin.

[OAuth 2,0 veya OpenID Connect 1,0](active-directory-v2-protocols.md)' nin protokol düzeyi uygulamanızı tek başına kodlayabilirsiniz, her bir standart belirtimindeki güvenlik konularına yakın dikkat edin ve [Microsoft SDL][Microsoft-SDL]gibi bir yazılım GELIŞTIRME yaşam döngüsü (SDL) metodolojisini izleyin.

## <a name="single-page-application-spa"></a>Tek sayfalı uygulama (SPA)

Tek sayfalı bir uygulama tamamen tarayıcıda çalışır ve sayfa verilerini (HTML, CSS ve JavaScript) dinamik olarak veya uygulama yükleme zamanında getirir. Arka uç veri kaynaklarıyla etkileşim kurmak için Web API 'Lerini çağırabilir.

SPA 'nın kodu tamamen tarayıcıda çalıştığından, gizli dizileri güvenli bir şekilde depolayamadığı *ortak bir istemci* olarak kabul edilir.

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="web-application"></a>Web uygulaması

Web uygulaması, bir kullanıcının Web tarayıcısına işlenmesi için HTML, CSS ve JavaScript üreten ve gönderen bir sunucuda kodu çalıştırır. Kullanıcının kimliği, kullanıcının tarayıcısı (ön uç) ve Web sunucusu (arka uç) arasında bir oturum olarak tutulur.

Bir Web uygulamasının kodu Web sunucusunda çalıştığı için gizli dizileri güvenli bir şekilde depolayabilen *gizli bir istemci* olarak kabul edilir.

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

## <a name="desktop-application"></a>Masaüstü uygulaması

Bir masaüstü uygulaması, genellikle bir kullanıcı arabirimini görüntüleyen ve kullanıcının masaüstünde çalışması amaçlanan ikili (derlenmiş) koddur.

Bir masaüstü uygulaması kullanıcının masaüstünde çalıştığından, gizli dizileri güvenli bir şekilde depolayamadığı *ortak bir istemci* olarak kabul edilir.

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="mobile-application"></a>Mobil uygulama

Bir mobil uygulama, genellikle bir kullanıcı arabirimini görüntüleyen ve kullanıcının mobil cihazında çalışmak üzere tasarlanan ikili (derlenmiş) koddur.

Bir mobil uygulama kullanıcının mobil cihazında çalıştığından, gizli dizileri güvenli bir şekilde depolayamadığı *ortak bir istemci* olarak kabul edilir.

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="service--daemon"></a>Hizmet/Daemon

Hizmetler ve Daemon 'ları, genellikle sunucudan sunucuya ve diğer katılımsız (bazen *gözetimsiz* olarak adlandırılır) iletişim için kullanılır. Klavyede kimlik bilgileri veya kaynak erişimine onay girmeye yönelik bir Kullanıcı bulunmadığından, bu uygulamalar, bir Web API 'sinin kaynaklarına yetkili erişim isteğinde bulunduğunda Kullanıcı değil, kendileri olarak kimlik doğrular.

Sunucuda çalışan bir hizmet veya Daemon, gizli dizilerini güvenli bir şekilde depolayabilen bir *Gizli istemci* olarak kabul edilir.

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="next-steps"></a>Sonraki adımlar

Microsoft kimlik doğrulama Kitaplığı hakkında daha fazla bilgi için bkz. [Microsoft kimlik doğrulama kitaplığı 'Na genel bakış (msal)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
