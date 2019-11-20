---
title: Azure MFA NPS uzantısını Yapılandırma-Azure Active Directory
description: NPS uzantısını yükledikten sonra, IP beyaz listesi ve UPN değiştirme gibi gelişmiş yapılandırma için bu adımları kullanın.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e156585ba063515bd8be573b5d99b41e7ce35d1
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932498"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Multi-Factor Authentication için NPS uzantısı için gelişmiş yapılandırma seçenekleri

Ağ Ilkesi sunucusu (NPS) uzantısı, bulut tabanlı Azure Multi-Factor Authentication özelliklerinizi şirket içi altyapınızla genişletir. Bu makalede, uzantının zaten yüklü olduğu varsayılır ve şimdi, uzantıyı gereksinimlerinize göre nasıl özelleştireceğinizi öğrenmek istiyorsunuz. 

## <a name="alternate-login-id"></a>Alternatif oturum açma KIMLIĞI

NPS uzantısı hem şirket içi hem de bulut dizinlerinize bağlandığından, şirket içi Kullanıcı asıl adlarınızın (UPN) buluttaki adlarla eşleşmediği bir sorunla karşılaşabilirsiniz. Bu sorunu çözmek için alternatif oturum açma kimlikleri kullanın. 

NPS uzantısı içinde, Azure Multi-Factor Authentication için UPN yerine kullanılacak bir Active Directory özniteliği belirleyebilirsiniz. Bu, şirket içi UPN 'larınızı değiştirmeden şirket içi kaynaklarınızı iki adımlı doğrulamayla korumanıza olanak sağlar. 

Alternatif oturum açma kimliklerini yapılandırmak için aşağıdaki kayıt `HKLM\SOFTWARE\Microsoft\AzureMfa` defteri değerlerini adresine gidin ve düzenleyin:

| Name | Type | Varsayılan değer | Açıklama |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | dize | Boş | UPN yerine kullanmak istediğiniz Active Directory özniteliğin adını belirleyin. Bu öznitelik AlternateLoginID özniteliği olarak kullanılır. Bu kayıt defteri değeri [geçerli bir Active Directory özniteliğine](https://msdn.microsoft.com/library/ms675090.aspx) ayarlandıysa (örneğin, posta veya DisplayName), kimlik doğrulaması IÇIN kullanıcının UPN 'si yerine özniteliğin değeri kullanılır. Bu kayıt defteri değeri boş veya yapılandırılmamışsa, AlternateLoginID devre dışıdır ve kullanıcının UPN 'si kimlik doğrulaması için kullanılır. |
| LDAP_FORCE_GLOBAL_CATALOG | boolean | False | AlternateLoginID aranırken LDAP aramaları için genel kataloğun kullanımını zorlamak üzere bu bayrağı kullanın. Bir etki alanı denetleyicisini genel katalog olarak yapılandırın, AlternateLoginID özniteliğini genel kataloğa ekleyin ve bu bayrağı etkinleştirin. <br><br> LDAP_LOOKUP_FORESTS yapılandırıldıysa (boş değil), kayıt defteri ayarı değeri ne olursa olsun, **Bu bayrak true olarak zorlanır**. Bu durumda, NPS uzantısı genel kataloğun her orman için AlternateLoginID özniteliğiyle yapılandırılmasını gerektirir. |
| LDAP_LOOKUP_FORESTS | dize | Boş | Aranacak ormanların noktalı virgülle ayrılmış bir listesini sağlayın. Örneğin, *contoso. com; foobar. com*. Bu kayıt defteri değeri yapılandırılmışsa, NPS uzantısı tüm ormanları listelendikleri sırayla arar ve ilk başarılı AlternateLoginID değerini döndürür. Bu kayıt defteri değeri yapılandırılmamışsa, AlternateLoginID araması geçerli etki alanıyla sınırlandırlanır.|

Alternatif oturum açma kimlikleriyle ilgili sorunları gidermek için, [Alternatif oturum açma kimliği hataları](howto-mfa-nps-extension-errors.md#alternate-login-id-errors)için önerilen adımları kullanın.

## <a name="ip-exceptions"></a>IP özel durumları

Sunucu kullanılabilirliğini izlemeniz gerekiyorsa, yük dengeleyiciler, iş yüklerini göndermeden önce hangi sunucuların çalıştığını doğruladıktan sonra bu denetimlerin doğrulama istekleri tarafından engellenmesini istemezsiniz. Bunun yerine, hizmet hesapları tarafından kullanıldığını bildiğiniz IP adreslerinin bir listesini oluşturun ve bu liste için Multi-Factor Authentication gereksinimleri devre dışı bırakın.

IP izin verilen listesini yapılandırmak için aşağıdaki kayıt defteri `HKLM\SOFTWARE\Microsoft\AzureMfa` değerini adresine gidin ve yapılandırın:

| Name | Type | Varsayılan değer | Açıklama |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | dize | Boş | IP adreslerinin noktalı virgülle ayrılmış bir listesini sağlar. NAS/VPN sunucusu gibi hizmet isteklerinin gerçekleştiği makinelerin IP adreslerini ekleyin. IP aralıkları ve alt ağları desteklenmez. <br><br> Örneğin, *10.0.0.1; 10.0.0.2; 10.0.0.3*.

> [!NOTE]
> Bu kayıt defteri anahtarı yükleyici tarafından varsayılan olarak oluşturulmaz ve hizmet yeniden başlatıldığında AuthZOptCh günlüğünde bir hata görünür. Günlükteki bu hata yoksayılabilir, ancak bu kayıt defteri anahtarı oluşturulup boş bırakılırsa, hata iletisi döndürmez.

Bir istek içinde bulunan bir IP adresinden `IP_WHITELIST`geldiğinde, iki adımlı doğrulama atlanır. IP listesi, RADIUS isteğinin *Clientnasıpaddress* ÖZNITELIĞINDE belirtilen IP adresiyle karşılaştırılır. Eğer bir RADIUS isteği, Eğer bir ıttrnasıpaddress özniteliği olmadan geliyorsa, aşağıdaki uyarı günlüğe kaydedilir: "P_WHITE_LIST_WARNING:: IP beyaz listesi, Nasıpaddress özniteliğinde RADIUS isteğindeki kaynak IP eksik olduğu için yoksayılıyor."

## <a name="next-steps"></a>Sonraki adımlar

[Azure Multi-Factor Authentication için NPS uzantısından alınan hata iletilerini çözme](howto-mfa-nps-extension-errors.md)
