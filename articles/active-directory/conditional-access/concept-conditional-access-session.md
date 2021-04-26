---
title: Koşullu erişim ilkesindeki oturum denetimleri-Azure Active Directory
description: Azure AD koşullu erişim ilkesinde oturum denetimleri nelerdir?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a0089d246169ad4215075662500794e7143940e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90601818"
---
# <a name="conditional-access-session"></a>Koşullu erişim: oturum

Bir yönetici, koşullu erişim ilkesinde, belirli bulut uygulamalarında sınırlı deneyimleri etkinleştirmek için oturum denetimlerini kullanabilir.

![Multi-Factor Authentication gerektiren bir izin denetimi olan koşullu erişim ilkesi](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Uygulama tarafından zorlanan kısıtlamalar

Kuruluşlar, Azure AD 'nin seçili bulut uygulamalarına cihaz bilgilerini geçmesini gerektirmek için bu denetimi kullanabilir. Cihaz bilgileri, bulut uygulamalarının bir bağlantının uyumlu veya etki alanına katılmış bir cihazdan başlatılıp başlatılmayacağını bilmesini sağlar. Bu denetim yalnızca seçili bulut uygulamaları olarak SharePoint Online ve Exchange Online 'ı destekler. Seçildiğinde, bulut uygulaması, cihaz durumuna bağlı olarak, sınırlı veya tam bir deneyimle, cihaz bilgilerini Kullanıcı sağlamak için kullanır.

Uygulama tarafından zorlanan kısıtlamaların kullanımı ve yapılandırılması hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [SharePoint Online ile sınırlı erişimi etkinleştirme](/sharepoint/control-access-from-unmanaged-devices)
- [Exchange Online ile sınırlı erişimi etkinleştirme](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Koşullu erişim uygulama denetimi

Koşullu Erişim Uygulama Denetimi, ters bir ara sunucu mimarisi kullanır ve Azure AD koşullu erişim ile benzersiz şekilde tümleşiktir. Azure AD koşullu erişimi, kuruluşunuzun uygulamalarında belirli koşullara göre erişim denetimleri zorlamanıza olanak sağlar. Koşullar, kimlerin (Kullanıcı veya Kullanıcı grubu) ve ne (bulut uygulamaları) ve koşullu erişim ilkesinin hangi konuma uygulanacağını tanımlar. Koşulları belirledikten sonra, erişim ve oturum denetimleri uygulayarak Koşullu Erişim Uygulama Denetimi verileri koruyabileceğiniz [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) kullanıcıları yönlendirebilirsiniz.

Koşullu Erişim Uygulama Denetimi, erişim ve oturum ilkelerine bağlı olarak, Kullanıcı uygulaması erişiminin ve oturumlarının gerçek zamanlı olarak izlenmesini ve denetlenmesini sağlar. Erişim ve oturum ilkeleri, filtreleri daha fazla iyileştirmek ve bir kullanıcıya alınacak eylemleri ayarlamak için Cloud App Security portalında kullanılır. Erişim ve oturum ilkeleriyle şunları yapabilirsiniz:

- Veri engellemeyi önleyin: Örneğin yönetilmeyen cihazlarda hassas belgeleri indirme, kesme, kopyalama ve yazdırma işlemi yapabilirsiniz.
- İndirme sırasında koru: hassas belgelerin indirilmesini engellemek yerine, belgelerin etiketlenmesi ve Azure Information Protection korumalı olmasını zorunlu kılabilirsiniz. Bu eylem, belgenin korunmasını ve Kullanıcı erişiminin potansiyel riskli bir oturumda kısıtlanmasını sağlar.
- Etiketsiz dosyaları karşıya yüklemeyi engelle: hassas bir dosya karşıya yüklenmeden, dağıtılmadan ve diğer kullanıcılar tarafından kullanılmadan önce, dosyanın doğru etikete ve korumaya sahip olduğundan emin olmak önemlidir. Gizli içeriğe sahip Etiketlenmemiş dosyaların, Kullanıcı içeriği sınıflandırana kadar karşıya yüklenmesi engellenmiş olduğundan emin olabilirsiniz.
- Uyumluluk için kullanıcı oturumlarını izleme: riskli kullanıcılar, uygulamalarda oturum açtıklarında izlenir ve bu işlemler oturum içinden günlüğe kaydedilir. Nerede olduğunu anlamak için Kullanıcı davranışını araştırıp çözümleyebilir ve hangi koşullarda oturum ilkelerinin gelecekte uygulanacağını anlayabiliriz.
- Erişimi engelle: birkaç risk etmenlerine bağlı olarak belirli uygulamalar ve kullanıcılar için erişimi çok fazla engelleyebilirsiniz. Örneğin, istemci sertifikalarını cihaz yönetiminin bir formu olarak kullanıyorsa bunları engelleyebilirsiniz.
- Özel etkinlikleri engelle: bazı uygulamalarda risk taşıyan benzersiz senaryolar vardır. Örneğin, Microsoft ekipleri veya bolluk gibi uygulamalarda hassas içeriğe sahip iletiler gönderme. Bu tür senaryolarda, hassas içerik için iletileri tarayabilir ve gerçek zamanlı olarak engelleyebilirsiniz.

Daha fazla bilgi için bkz. [öne çıkan uygulamalar için koşullu erişim uygulama denetimi dağıtma](/cloud-app-security/proxy-deployment-aad)makalesi.

## <a name="sign-in-frequency"></a>Oturum açma sıklığı

Oturum açma sıklığı, bir kullanıcıdan bir kaynağa erişmeye çalışırken yeniden oturum açması istenmeden önce geçen süreyi tanımlar.

Oturum açma sıklığı ayarı, standartlara göre OAUTH2 veya OıDC protokollerini uygulamış olan uygulamalarla birlikte çalışarak. Aşağıdaki Web uygulamaları dahil olmak üzere Windows, Mac ve mobil için Microsoft Native uygulamaların çoğu ayarla uyumlu değil.

- Word, Excel, PowerPoint online
- OneNote Online
- Office.com
- Yönetici portalı Microsoft 365
- Exchange Online
- SharePoint ve OneDrive
- Takımlar Web istemcisi
- Dynamics CRM Online
- Azure portalı

Daha fazla bilgi için, [koşullu erişimle kimlik doğrulama oturumu yönetimini yapılandırma](howto-conditional-access-session-lifetime.md#user-sign-in-frequency)makalesine bakın.

## <a name="persistent-browser-session"></a>Kalıcı tarayıcı oturumu

Kalıcı bir tarayıcı oturumu, kullanıcıların tarayıcı pencerelerini kapatıp yeniden açtıktan sonra oturum açmasına olanak tanır.

Daha fazla bilgi için, [koşullu erişimle kimlik doğrulama oturumu yönetimini yapılandırma](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions)makalesine bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md)

- [Yalnızca rapor modu](concept-conditional-access-report-only.md)
