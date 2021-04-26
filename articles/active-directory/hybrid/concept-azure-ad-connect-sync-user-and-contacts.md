---
title: 'Azure AD Connect eşitleme: kullanıcıları, grupları ve kişileri anlama | Microsoft Docs'
description: Azure AD Connect eşitlemede kullanıcıları, grupları ve kişileri açıklar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e015f7937db6788aa4473a8a04434121299901e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861791"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Azure AD Connect eşitleme: kullanıcıları, grupları ve kişileri anlama
Birden çok Active Directory ormanına sahip olmanız ve birkaç farklı dağıtım topolojisi vardır. Ortak modeller bir birleşme & alımı sonrasında bir hesap kaynağı dağıtımı ve GAL ile eşitleme ormanları içerir. Ancak saf modeller olsa da, karma modeller de ortaktır. Azure AD Connect eşitlemede varsayılan yapılandırma belirli bir modeli kabul etmez, ancak yükleme kılavuzunda Kullanıcı eşleşmesinin nasıl seçildiğine bağlı olarak farklı davranışlar gözlemlenebilir.

Bu konu başlığında, varsayılan yapılandırmanın belirli topolojilerde nasıl davrandığını öğreneceksiniz. Yapılandırmayı gözden geçirin ve yapılandırmaya bakmak için eşitleme kuralları Düzenleyicisi kullanılabilir.

Yapılandırmanın varsaydığı birkaç genel kural vardır:
* Kaynak Active Directory 'lerden hangi sırada içeri aktardığımızda, son sonuç her zaman aynı olmalıdır.
* Etkin bir hesap, her zaman **userPrincipalName** ve **sourcetutturucu** dahil olmak üzere oturum açma bilgilerine katkıda bulunur.
* Devre dışı bırakılmış bir hesap, bağlı bir posta kutusu olmadıkça, bulunamayan bir etkin hesap yoksa, userPrincipalName ve Sourcetutturucuya katkıda bulunur.
* Bağlı bir posta kutusuna sahip bir hesap, userPrincipalName ve Sourcetutturucu için hiçbir şekilde kullanılmaz. Etkin bir hesabın daha sonra bulunabilecektir.
* Bir kişi nesnesi Azure AD 'ye bir kişi veya Kullanıcı olarak sağlanabilir. Tüm kaynak Active Directory ormanları işlenene kadar gerçekten emin değilsiniz.

## <a name="groups"></a>Gruplar
Grupları Active Directory 'den Azure AD 'ye eşitlerken önemli noktalara dikkat edin:

* Azure AD Connect, yerleşik güvenlik gruplarını Dizin eşitlemeden dışlar.

* Azure AD Connect, [birincil grup üyeliklerini](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771489(v=ws.11)) Azure AD 'ye eşitlemeyi desteklemez.

* Azure AD Connect, [dinamik dağıtım grubu üyeliklerini](/Exchange/recipients/dynamic-distribution-groups/dynamic-distribution-groups) Azure AD 'ye eşitlemeyi desteklemez.

* Bir Active Directory grubunu Azure AD ile posta etkin bir grup olarak eşzamanlı hale getirmek için:

    * Grubun *ProxyAddress* özniteliği boşsa, *posta* özniteliği bir değere sahip olmalıdır

    * Grubun *ProxyAddress* özniteliği boş değilse, en az bir SMTP proxy adresi değeri içermesi gerekir. İşte bazı örnekler:
    
      * ProxyAddress özniteliğinin değeri *{"X500:/0 = contoso. com/OU = Users/CN = testgroup"}* olan bir Active Directory grubu Azure AD 'de posta etkin olmayacak. SMTP adresi yok.
      
      * ProxyAddress özniteliği, *{"X500:/0 = contoso. com/OU = Users/CN = testgroup", "SMTP: johntikan \@ contoso.com"}* değerlerini içeren bir ACTIVE DIRECTORY grubu Azure AD 'de posta etkin olacak.
      
      * ProxyAddress özniteliğinde yer aldığı bir Active Directory grubu *{"X500:/0 = contoso. com/OU = Users/CN = testgroup", "SMTP: johntikan \@ contoso.com"}* DEĞERLERININ de Azure AD 'de posta etkin olacak.

## <a name="contacts"></a>Kişiler
Bir kullanıcı farklı bir ormanda temsil eden kişilerin olması, bir GALSync çözümünün iki veya daha fazla Exchange ormanını köprülemesi durumunda bir birleşme & alma işleminden sonra yaygındır. İletişim nesnesi, posta özniteliğini kullanarak her zaman bağlayıcı alanından metadize 'ye katılıyor. Aynı posta adresine sahip bir kişi nesnesi veya Kullanıcı nesnesi zaten varsa, nesneler birlikte birleştirilir. Bu, **ad – Iletişim birleşimden içindeki** kuralında yapılandırılır. Ayrıca, **ad 'Den ' de** adlı bir kural vardır. bir öznitelik akışı ile, sabit **ilgili kişi** olan meta veri deposu özniteliğinin **Sourceobjecttype** özniteliği ile ortak iletişim kurun. Bu kural çok düşük önceliğe sahiptir, bu nedenle, herhangi bir kullanıcı nesnesi aynı meta veri deposu nesnesine katılırsa, **ad – Kullanıcı ortak ' dan** gelen kural, bu özniteliğe değer kullanıcısı katkıda bulunur. Bu kuralla, hiçbir Kullanıcı birleştirilmediyse ve en az bir kullanıcı bulunursa değer kullanıcısı varsa, bu özniteliğin değeri Ilgili kişisi olur.

Azure AD 'de bir nesne sağlamak için, AAD 'ye giden çıkış kuralı **– Contact JOIN** , meta veri deposu özniteliği **sourceobjecttype** **iletişim** olarak ayarlandıysa bir iletişim nesnesi oluşturacaktır. Bu öznitelik **Kullanıcı** olarak ayarlandıysa **AAD 'ye giden kural – Kullanıcı birleşimi** bunun yerine bir kullanıcı nesnesi oluşturur.
Daha fazla kaynak Active Directory içeri aktarılmışsa ve eşitlendiğinde bir nesne kişiden kullanıcıya yükseltilme olasılığı vardır.

Örneğin, bir GALSync topolojisinde, ilk ormanı içeri aktarırken ikinci ormandaki herkes için kişi nesneleri bulacaksınız. Bu, AAD bağlayıcısında yeni kişi nesneleri oluşturacak. İkinci ormanı daha sonra içeri aktarıp eşitleriz, gerçek kullanıcıları bulacak ve bunları var olan meta veri deposu nesnelerine birleştiririz. Daha sonra AAD 'deki kişi nesnesini silecek ve bunun yerine yeni bir kullanıcı nesnesi oluşturacağız.

Kullanıcıların kişiler olarak temsil ettiği bir topolojiniz varsa, yükleme kılavuzundaki mail özniteliğinde kullanıcıları eşleştirmeye seçtiğinizden emin olun. Başka bir seçenek belirlerseniz, sipariş bağımlı bir yapılandırmaya sahip olursunuz. İletişim nesneleri her zaman mail özniteliğinde birleşdirecektir, ancak bu seçenek yükleme kılavuzunda seçilirse, Kullanıcı nesneleri yalnızca posta özniteliğiyle birleşdirecektir. Daha sonra, kişi nesnesi kullanıcı nesnesinden önce içeri aktarıldıysa, aynı posta özniteliğiyle metadizesinde iki farklı nesneyle kaydolabilirsiniz. Azure AD 'ye dışarı aktarma sırasında bir hata oluşur. Bu davranış tasarım ve hatalı verileri gösterir ya da yükleme sırasında topolojinin doğru şekilde belirlenmediğini gösterir.

## <a name="disabled-accounts"></a>Devre dışı hesaplar
Devre dışı bırakılan hesapların Azure AD 'ye de eşitlenmesi. Devre dışı bırakılan hesaplar, örneğin konferans odaları gibi Exchange kaynaklarını temsil eden yaygındır. Özel durum bağlı bir posta kutusu olan kullanıcıdır; daha önce belirtildiği gibi, bunlar hiçbir şekilde Azure AD 'ye bir hesap sağlamacaktır.

Varsayım, devre dışı bir kullanıcı hesabı bulunursa daha sonra başka bir etkin hesap bulmayacak ve nesne Azure AD 'de userPrincipalName ve Sourcetutturucu ile sağlanacaktır. Başka bir etkin hesabın aynı meta veri deposu nesnesine katılması durumunda, bu, userPrincipalName ve Sourcetutturucu kullanılacaktır.

## <a name="changing-sourceanchor"></a>Sourcebağlayıcısını değiştirme
Bir nesne Azure AD 'ye aktarıldığında, daha sonra Sourcebağlayıcının değiştirilmesine izin verilmez. Nesne verildiğinde, **cloudsourcebağlayıcının** meta veri deposu ÖZNITELIĞI Azure AD tarafından kabul edilen **sourcetutturucu** değeri ile ayarlanır. **Sourcetutturucu** değiştirilirse ve **CLOUDSOURCETUTTURUCUYA** eşleşmezse, **AAD 'ye yönelik kural-Kullanıcı birleşimi** , **sourcetutturucu özniteliği tarafından değiştirilir**. Bu durumda, yapılandırma veya verilerin düzeltilmesi gerekir, böylece nesne yeniden eşitlenmeden önce meta veri deposunda aynı Sourcebağlayıcının bulunması gerekir.

## <a name="additional-resources"></a>Ek Kaynaklar
* [Azure AD Connect eşitleme: eşitleme seçeneklerini özelleştirme](how-to-connect-sync-whatis.md)
* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md)
