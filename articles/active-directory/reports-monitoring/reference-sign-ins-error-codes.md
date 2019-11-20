---
title: Azure Active Directory portalındaki hata kodları | Microsoft Docs
description: Oturum açma etkinlik raporu hata kodları başvurusu.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/08/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e9d484da1b59c456b30cf0b465ec6de5af77d1
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014306"
---
# <a name="sign-in-activity-report-error-codes"></a>Oturum açma Etkinliği raporu hata kodları 

[Kullanıcı oturum açma işlemleri raporu](concept-sign-ins.md)tarafından verilen bilgiler ile, şu gibi soruların yanıtlarını bulabilirsiniz:

- Uygulamamda kim oturum açtı?
- Hangi uygulamalara oturum açıldı?
- Hangi oturum açma işlemleri başarısız oldu ve neden?

Bir oturum açma başarısız olduğunda, hataya karşılık gelen bir hata kodu görürsünüz. Bu makalede, ilgili olduğu yerlerde önerilen bir eylem ile birlikte hata kodları ve açıklamaları listelenmektedir. 

## <a name="how-can-i-display-failed-sign-ins"></a>Başarısız oturum açma girişimlerini nasıl görüntüleyebilirim? 

[Azure Portal](https://portal.azure.com) [oturum açma raporuna](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns) gidin.

![Oturum açma etkinliği](./media/reference-sign-ins-error-codes/61.png "Oturum açma etkinliği")

**Oturum açma durumu** açılan kutusunda **hata** ' u seçerek, tüm başarısız oturum açma işlemlerini göstermek için raporu filtreleyin.

![Oturum açma etkinliği](./media/reference-sign-ins-error-codes/06.png "Oturum açma etkinliği")

Filtrelenmiş listeden bir öğe seçildiğinde, **etkinlik ayrıntıları: oturum açma** dikey penceresi açılır. Bu görünüm, **oturum açma hata kodu** ve **hata nedeni**dahil olmak üzere, başarısız oturum açma olayı hakkında ek bilgiler sağlar.

![Oturum açma etkinliği](./media/reference-sign-ins-error-codes/05.png "Oturum açma etkinliği")

Ayrıca, [Raporlama API](concept-reporting-api.md)'sini kullanarak oturum açma verilerine programlı bir şekilde erişebilirsiniz.

## <a name="error-codes"></a>Hata kodları


|Hata|Açıklama|
|---|---|
|16000|Bu, bir hata koşulu değil, iç uygulama ayrıntısıyla belirlenir. Bu başvuruyu güvenle yoksayabilirsiniz.|
|20001|Federasyon Kimlik Sağlayıcısı ile ilgili bir sorun var. Bu sorunu çözmek için IDP’ye başvurun.|
|20012|Federasyon Kimlik Sağlayıcısı ile ilgili bir sorun var. Bu sorunu çözmek için IDP’ye başvurun.|
|20033|Federasyon Kimlik Sağlayıcısı ile ilgili bir sorun var. Bu sorunu çözmek için IDP’ye başvurun.|
|40008|Federasyon Kimlik Sağlayıcısı ile ilgili bir sorun var. Bu sorunu çözmek için IDP’ye başvurun.|
|40009|Federasyon Kimlik Sağlayıcısı ile ilgili bir sorun var. Bu sorunu çözmek için IDP’ye başvurun.|
|40014|Federasyon Kimlik Sağlayıcısı ile ilgili bir sorun var. Bu sorunu çözmek için IDP’ye başvurun.|
|50000|Oturum açma hizmetimizle ilgili bir sorun var. Bu sorunu çözmek için bir [destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md).|
|50001|Hizmet asıl adı bu kiracıda bulunamadı. Bu durum, uygulama kiracının Yöneticisi tarafından yüklenmediyse veya kaynak sorumlusu dizinde bulunmazsa veya geçersizse geçersizdir.|
|50002|Kiracıda kısıtlı proxy erişimi nedeniyle oturum açma başarısız oldu. Kendi kiracı ilkeniz varsa, bu sorunu giderecek şekilde kısıtlı kiracı ayarlarınızı değiştirebilirsiniz.|
|50003|Eksik anahtar veya sertifika nedeniyle oturum açma başarısız oldu. Bunun nedeni, uygulamada yapılandırılmış bir imzalama anahtarının olmaması olabilir. [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured) sayfasında belirtilen çözümlere bakın. Sorun devam ederse, uygulama sahibine veya uygulama yöneticisine başvurun.|
|50005|Kullanıcı şu anda koşullu erişim ilkesi aracılığıyla desteklenmeyen bir platformdan cihazda oturum açmaya çalıştı.|
|50006| Geçersiz imza nedeniyle imza doğrulaması başarısız oldu. [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery) sayfasında belirtilen çözüme bakın. Sorun devam ederse, uygulama sahibine veya uygulama yöneticisine başvurun.|
|50007|Bu uygulama için iş ortağı şifreleme sertifikası bulunamadı. Bunu sağlamak için Microsoft ile [bir destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md) .|
|50008|SAML onayı eksik veya belirteçte yanlış yapılandırılmış. Federasyon sağlayıcınıza başvurun.|
|50010|Belirteç hedef kitlesi yapılandırılmadığından uygulama için doğrulaması başarısız oldu. Çözüm için uygulama sahibine başvurun.|
|50011|Yanıt adresi eksik, yanlış yapılandırılmış veya uygulama için yapılandırılan yanıt adresleriyle eşleşmiyor. [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application)' de listelenen çözümlemeyi deneyin. Sorun devam ederse, uygulama sahibine veya uygulama yöneticisine başvurun.|
|50012| Bu, kimlik doğrulamasının başarısız olduğunu gösteren genel bir hata iletisidir. Bu durum, istekte eksik veya geçersiz kimlik bilgileri veya talepler gibi nedenlerle ortaya çıkabilir. İsteğin doğru kimlik bilgileri ve talepler ile gönderildiğinden emin olun. |
|50013|Onaylama işlemi çeşitli nedenlerden dolayı geçersiz. Örneğin, belirteç veren, API sürümüyle geçerli zaman aralığında eşleşmez, belirtecin süresi dolmuştur veya hatalı biçimlendirilmiş ya da onaylamanın yenileme belirteci birincil yenileme belirteci değildir.|
|50017|Sertifika doğrulaması aşağıdaki nedenlerden dolayı başarısız oldu:<ul><li>Güvenilir sertifika listesinde verme sertifikası bulunamıyor</li><li>Beklenen CrlSegment bulunamıyor</li><li>Güvenilir sertifika listesinde verme sertifikası bulunamıyor</li><li>Delta CRL dağıtım noktası, karşılık gelen bir CRL dağıtım noktası olmadan yapılandırılmış</li><li>Zaman aşımı sorunu nedeniyle geçerli CRL segmentleri alınamıyor</li><li>CRL indirilemiyor</li></ul>Kiracı yöneticisine başvurun.|
|50020|Kullanıcı aşağıdaki nedenlerden biri için yetkilendirilmemiş.<ul><li>Kullanıcı v1 uç noktasıyla bir MSA hesabıyla oturum açmaya çalışıyor</li><li>Kullanıcı kiracıya yok.</li></ul> Uygulama sahibine başvurun.|
|50027|Aşağıdaki nedenlerden dolayı geçersiz JWT belirteci:<ul><li>Bir kerelik anahtar talebi içermiyor, alt talep</li><li>konu tanımlayıcısı uyuşmazlığı</li><li>idToken taleplerinde yinelenen talep</li><li>beklenmeyen veren</li><li>beklenmeyen hedef kitle</li><li>geçerli zaman aralığı içinde değil </li><li>belirteç biçimi doğru değil</li><li>Verenin dış kimlik belirteci imza doğrulaması başarısız oldu.</li></ul>Uygulama sahibine başvurun|
|50029|Geçersiz URI - etki alanı adı geçersiz karakterler içeriyor. Kiracı yöneticisine başvurun.|
|50034|Kullanıcı dizinde yok. Kiracı yöneticinize başvurun.|
|50042|Bir ikili tanımlayıcı oluşturmak için gereken anahtar, prensibi içinde eksik. Kiracı yöneticisine başvurun.|
|50048|Konu, istemci onayındaki Veren talebi ile uyuşmuyor. Kiracı yöneticisine başvurun.|
|50050|İstek yanlış biçimlendirilmiş. Uygulama sahibine başvurun.|
|50053|Kullanıcı, yanlış Kullanıcı KIMLIĞI veya parolasıyla çok fazla kez oturum açmaya çalıştığı için hesap kilitli.|
|50055|Geçersiz parola, süresi dolmuş parola girildi.|
|50056|Geçersiz veya null parola-bu kullanıcı için depoda parola yok.|
|50057|Kullanıcı hesabı devre dışı bırakıldı. Hesap bir yönetici tarafından devre dışı bırakıldı.|
|50058|Uygulama sessiz bir oturum açma gerçekleştirdi ve kullanıcı sessiz bir şekilde oturum açamadı. Uygulamanın, kullanıcılara oturum açma seçeneği sunan etkileşimli bir akış başlatması gerekir. Uygulama sahibine başvurun.|
|50059|Kullanıcı dizinde yok. Kiracı yöneticinize başvurun.|
|50061|Oturumu kapatma isteği geçersiz. Uygulama sahibine başvurun.|
|50072|Kullanıcının iki faktörlü kimlik doğrulamasına (etkileşimli) kaydolması gerekir.|
|50074|Kullanıcı MFA testini geçemedi.|
|50076|Kullanıcı MFA Challenge (etkileşimli olmayan) iletmedi.|
|50079|Kullanıcının iki öğeli kimlik doğrulaması için (etkileşimli olmayan oturum açma) kaydolması gerekir.|
|50085|Yenileme belirteci için sosyal IDP oturum açma bilgileri gerekiyor. Kullanıcının Kullanıcı adı ve parolasıyla oturum açmasını yeniden denemesini sağlayabilirsiniz.|
|50089|Akış belirtecinin zaman aşımına uğradı-kimlik doğrulaması başarısız oldu. Kullanıcının Kullanıcı adı ve parolasıyla oturum açmayı yeniden denemesini deneyin|
|50097|Cihaz kimlik doğrulaması gerekiyor. Bu durum, DeviceID veya Devicealtsecıd talepleri **null**olduğu veya cihaz tanımlayıcısına karşılık gelen bir cihaz bulunmadığı için oluşabilir.|
|50099|JWT imzası geçersiz. Uygulama sahibine başvurun.|
|50105|Oturum açmış kullanıcı, oturum açmış uygulama için bir role atanmadı. Kullanıcıyı uygulamaya atayın. Daha fazla bilgi için: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|İstenen federasyon bölgesi nesnesi yok. Kiracı yöneticisine başvurun.|
|50120|JWT üst bilgisi ile ilgili sorun var. Kiracı yöneticisine başvurun.|
|50124|Talep Dönüşümü, geçersiz giriş parametresi içeriyor. İlkeyi güncelleştirmek için kiracı yöneticisine başvurun.|
|50125|Parola sıfırlama veya parola kayıt girdisi nedeniyle oturum açma işlemi kesildi.|
|50126|Geçersiz Kullanıcı adı veya parola, ya da şirket içi Kullanıcı adı veya parola geçersiz.|
|50127|Kullanıcının bu içeriğe erişim kazanmak için bir aracı uygulaması yüklemesi gerekir.|
|50128|Geçersiz etki alanı adı-istekte bulunan veya belirtilen kimlik bilgileri tarafından kapsanan hiçbir kiracı tanımlama bilgisi bulunamadı.|
|50129|Cihaz çalışma alanına katılmış değil; cihazı kaydetmek için **Workplace JOIN** gereklidir.|
|50130|Talep değeri bilinen auth yöntemi olarak yorumlanamıyor.|
|50131|Çeşitli koşullu erişim hatalarında kullanılır. Örneğin Hatalı Windows cihazı durumu, şüpheli etkinlik nedeniyle istek engellendi, erişim ilkesi ve güvenlik ilkesi kararları.|
|50132|Kimlik bilgileri aşağıdaki nedenlerden dolayı iptal edildi:<ul><li>SSO Yapıtı geçersiz veya süresi dolmuş</li><li>Oturum, uygulama için yeterince yeni değil</li><li>Sessiz oturum açma isteği gönderildi ancak kullanıcının Azure AD oturumu geçersiz veya süresi dolmuş.</li></ul>|
|50133|Süresi dolduğu veya yakın zamanda parola değiştirildiği için oturum geçersiz.|
|50135|Hesap riski nedeniyle parola değişikliği gereklidir.|
|50136|MSA oturumunu uygulamaya yönlendir-tek MSA oturumu algılandı. |
|50140|Bu hata, kullanıcı oturum açtığında "Oturumumu açık bırak" kesintisi nedeniyle oluştu. Daha fazla bilgi almak için Bağıntı Kimliği, İstek Kimliği ve Hata kodu ile [bir destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md). |
|50143|Oturum uyumsuzluğu-Kullanıcı kiracısı farklı kaynak nedeniyle etki alanı ipucuyla eşleşmediğinden oturum geçersiz. Daha fazla ayrıntı edinmek için bağıntı KIMLIĞI, Istek KIMLIĞI ve hata kodu ile  [bir destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md) .|
|50144|Kullanıcının Active Directory parolasının süresi doldu. Kullanıcı için yeni bir parola oluşturun veya son kullanıcıya self servis sıfırlama aracı 'nı kullanın.|
|50146|Bu uygulamanın, uygulamaya özel bir imzalama anahtarı ile yapılandırılması gerekiyor. Bu şekilde yapılandırılmamış veya anahtarın süresi dolmuş ya da anahtar henüz geçerli değil. Uygulama sahibine başvurun.|
|50148|code_verifier, PKCE yetkilendirme isteğinde belirtilen code_challenge ile eşleşmiyor. Uygulama geliştiriciyle iletişim kurun. |
|50155|Bu Kullanıcı için cihaz kimlik doğrulaması başarısız oldu.|
|50158|Dış Güvenlik sınaması karşılanmadı.|
|50161|Dış sağlayıcı tarafından gönderilen talepler yeterli değil veya dış sağlayıcıya istenen talep eksik.|
|50166|İstek, talep sağlayıcısına gönderilemedi.|
|50169|Bölge geçerli hizmet ad alanının yapılandırılmış bir bölgesi değil.|
|50172|Dış talep sağlayıcısı onaylanmadı. Kiracı yöneticisine başvurun|
|50173|Yeni kimlik doğrulama belirteci gereklidir. Kullanıcının yeni kimlik bilgilerini kullanarak tekrar oturum açmasını sağlayabilirsiniz.|
|50177|Dışarıdan sınama, geçiş kullanıcıları için desteklenmez.|
|50178|Geçiş kullanıcıları için oturum denetimi desteklenmez.|
|50180|Windows Tümleşik kimlik doğrulaması gerekli. Sorunsuz SSO için kiracıyı etkinleştirin.|
|50181|Oturum açma sırasında OTP ile ilgili hata. |
|50201|Bu ileti istemi kesmesi, kullanıcıya ek bilgi sağlanması gerektiğinde oturum açma sırasında kullanıcıya gösterilir.|
|51001|Şirket içi güvenlik tanımlayıcısı-şirket Içi UPN ile etki alanı Ipucu yok.|
|51004|Kullanıcı hesabı dizinde yok.|
|51006|Windows Tümleşik kimlik doğrulaması gerekli. Kullanıcı, talep aracılığıyla eksik olan oturum belirtecini kullanarak oturum açtı. Kullanıcıdan yeniden oturum açmasını isteyin.|
|52004|Kullanıcı, LinkedIn kaynaklarına erişim izni sağlamadı. |
|53000|Koşullu Erişim ilkesi uyumlu bir cihaz gerektiriyor ve cihaz uyumlu değil. Kullanıcının cihazlarını Intune gibi onaylanmış bir MDM sağlayıcısıyla kaydetmesini sağlayabilirsiniz.|
|53001|Koşullu Erişim ilkesi, etki alanına katılmış bir cihaz gerektiriyor ve cihaz etki alanına katılmamış. Kullanıcının etki alanına katılmış bir cihaz kullanmasını sağlayabilirsiniz.|
|53002|Kullanılan uygulama, koşullu erişim için onaylanan bir uygulama değil. Kullanıcının erişim elde etmesi için kullanılacak onaylı uygulamalar listesinden bir uygulama seçmesi gerekir.|
|53003|Koşullu erişim ilkeleri nedeniyle erişim engellendi.|
|53004|Kullanıcının bu içeriğe erişmeden önce çok faktörlü kimlik doğrulaması işlemini tamamlaması gerekiyor. Kullanıcının çok faktörlü kimlik doğrulamasına kaydolması gerekir.|
|65001|X uygulamasının Y uygulamasına erişim izni yok veya erişim izni iptal edildi. Veya Kullanıcı ya da yönetici X kimliğiyle uygulamanın kullanılmasını onaylamadı. Bu kullanıcı veya kaynak için etkileşimli yetkilendirme isteği gönderin. Veya Kullanıcı ya da yönetici X kimliğiyle uygulamanın kullanılmasını onaylamadı. Kaynak: Z için Uygulama: Y adına işlem yapmak üzere kiracı yöneticinize bir yetkilendirme isteği gönderin.|
|65004|Kullanıcı, uygulamaya erişmeyi reddetti. Kullanıcıdan oturum açmayı yeniden denemesini ve uygulamaya izin vermesini isteyin|
|65005|Uygulamaya gereken kaynak erişim listesi, kaynak tarafından bulunabilen uygulamaları içermiyor veya İstemci uygulaması kendi gerekli kaynak erişim listesinde belirtilmemiş bir kaynağa erişim isteğinde bulundu veya Graph hizmeti hatalı istek döndürdü veya kaynak bulunamadı. Uygulama SAML desteği içeriyorsa, yanlış Tanımlayıcı (Varlık) ile uygulamayı yapılandırmış olabilirsiniz. Aşağıdaki bağlantıyı kullanarak SAML için listelenen çözümü deneyin: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)|
|70000|Aşağıdaki nedenlerden dolayı geçersiz izin:<ul><li>İstenen SAML 2.0 onay deyimi geçersiz Konu Onay Yöntemine sahip</li><li>Uygulama OnBehalfOf akışı V2’de desteklenmiyor</li><li>Birincil yenileme belirteci, oturum anahtarı ile imzalanmamış</li><li>Geçersiz dış yenileme belirteci</li><li>Farklı bir kiracı için erişim izni elde edildi.</li></ul>|
|70001|X adlı uygulama Y adlı kiracıda bulunamadı. Tanımlayıcısı X olan uygulama, kiracının yöneticisi tarafından yüklenmediyse veya kiracıdaki herhangi bir kullanıcı tarafından onaylanmadıysa bu durum ortaya çıkabilir. Uygulamanın tanımlayıcı değerini yanlış yapılandırılmış veya kimlik doğrulama isteğinizi yanlış kiracıya göndermiş olabilirsiniz.|
|70002|Uygulama geçersiz istemci kimlik bilgileri döndürdü. Uygulama sahibine başvurun.|
|70003|Uygulama desteklenmeyen bir izin verme türü döndürdü. Uygulama sahibine başvurun.|
|70004|Uygulama geçersiz bir yeniden yönlendirme URI'si döndürdü. İstemci tarafından belirtilen yeniden yönlendirme adresi, yapılandırılmış bir adresle veya OIDC onay listesindeki herhangi bir adresle eşleşmiyor. Uygulama sahibine başvurun.|
|70005|Uygulama, aşağıdaki nedenlerden dolayı desteklenmeyen bir yanıt türü döndürdü:<ul><li>uygulama için 'token' yanıt türü etkin değil</li><li>'id_token' yanıt türü 'OpenID' kapsamı gerektiriyor -kodlanmış wctx içinde desteklenmeyen bir OAuth parametre değeri içeriyor</li></ul>Uygulama sahibine başvurun.|
|70007|Uygulama bir belirteç isterken desteklenmeyen bir 'response_mode' değeri döndürdü. Uygulama sahibine başvurun.|
|70008|Belirtilen yetkilendirme kodu veya yenileme belirtecinin süresi doldu ya da iptal edildi. Kullanıcının oturum açmasını yeniden denemesini sağlayabilirsiniz.|
|70011|Uygulama tarafından istenen kapsam geçersiz. Uygulama sahibine başvurun.|
|70012|MSA (tüketici) kullanıcısının kimliği doğrulanırken bir sunucu hatası oluştu. Oturum açmayı yeniden deneyin ve sorun devam ederse [bir destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Kullanıcının cihaz kod akışı için yanlış kullanıcı kodu girmesi nedeniyle geçersiz doğrulama kodu. Yetkilendirme onaylanmamış.|
|70019|Doğrulama kodunun süresi doldu. Kullanıcının oturum açma işlemini yeniden denemesini sağlayabilirsiniz.|
|70037|Hatalı sınama yanıtı sağlandı. Uzaktan kimlik doğrulama oturumu reddedildi.|
|70043|Azure koşullu erişim oturumu yönetimi, oturumu sona ermeden zorlar|
|70044|Azure koşullu erişim oturumu yönetimi, oturumu sona ermeden zorlar|
|75001|SAML iletisi bağlama sırasında bir hata oluştu.|
|75003|Uygulama, desteklenmeyen Bağlama ile ilgili bir hata döndürdü (SAML protokolü yanıtı HTTP POST dışındaki bağlamalar üzerinden gönderilemiyor). Uygulama sahibine başvurun.|
|75005|Azure AD, uygulama tarafından Çoklu oturum açma için gönderilen SAML İsteğini desteklemiyor. Uygulama sahibine başvurun.|
|75008|SAML isteği beklenmeyen bir hedefe sahip olduğundan, uygulamadan alınan istek reddedildi. Uygulama sahibine başvurun.|
|75011|Kullanıcının hizmette kimlik doğrulaması yaparken kullandığı kimlik doğrulama yöntemi, istenen kimlik doğrulama yöntemi ile eşleşmiyor. Uygulama sahibine başvurun.|
|75016|SAML2 Kimlik Doğrulama İsteği geçersiz NameIdPolicy değerine sahip. Uygulama sahibine başvurun.|
|80001|Kimlik Doğrulama Aracısı Active Directory'ye bağlanamadı. Kimlik doğrulama aracısının, kullanıcının oturum açma isteğine hizmet edebilen bir CD ile görüş hattı olan, etki alanına katılmış bir makinede yüklü olduğundan emin olun.|
|80002|İç hata. Parola doğrulama isteği zaman aşımına uğradı. Kimlik doğrulama isteğini iç karma kimlik hizmetine gönderemedik. Hata hakkında daha fazla bilgi almak için [bir destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md).|
|80003|Kimlik Doğrulama Aracısı tarafından geçersiz yanıt alındı. Şirket içi Active Directory ile kimlik doğrulaması denenirken bilinmeyen bir hata oluştu. Hata hakkında daha fazla bilgi almak için [bir destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md).|
|80005|Kimlik Doğrulama Aracısı: Kimlik Doğrulama Aracısının yanıtı işlenirken bilinmeyen bir hata oluştu. Hata hakkında daha fazla bilgi almak için [bir destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md).|
|80007|Kimlik Doğrulama Aracısı kullanıcının parolasını doğrulayamıyor.|
|80010|Kimlik Doğrulama Aracısı parolanın şifresini çözemedi. |
|80011|Kimlik Doğrulama Aracısı şifreleme anahtarını alamıyor.|
|80012|Kullanıcılar izin verilen saatlerin dışında oturum açmaya çalıştı (AD 'de belirtilir).|
|80013|Kimlik doğrulama aracısını ve AD’yi çalıştıran makineler arasındaki zaman dengesizliği nedeniyle kimlik doğrulaması girişimi tamamlanamadı. Zaman eşitleme sorunlarını düzeltin|
|80014|Kimlik doğrulama Aracısı zaman aşımına uğradı. Bu hatayla ilgili daha fazla bilgi edinmek için hata kodu, bağıntı KIMLIĞI ve Tarih/saatteki [bir destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md) .|
|81001|Kullanıcının Kerberos anahtarı fazla büyük. Bu durum, kullanıcı çok fazla grupta ise ve sonuç olarak Kerberos bileti çok fazla grup üyeliği içeriyorsa gerçekleşebilir. Kullanıcının grup üyeliklerini azaltın ve yeniden deneyin.|
|81005|Kimlik doğrulama paketi desteklenmiyor.|
|81007|Kiracı, sorunsuz SSO için etkinleştirilmemiş.|
|81012|Bu bir hata koşulu değildir. Azure AD 'de oturum açmaya çalışan kullanıcının, cihazda oturum açmış olan kullanıcıdan farklı olduğunu gösterir. Günlüklerde bu kodu güvenle yoksayabilirsiniz.|
|90010|İstek çeşitli nedenlerle desteklenmez. Örneğin, istek desteklenmeyen bir istek yöntemi kullanılarak yapılır (yalnızca POST yöntemi desteklenir) veya istenen belirteç imzalama algoritması desteklenmez. Uygulama geliştiricisine başvurun.|
|90014| Protokol iletisi için gerekli bir alan eksikti, uygulama sahibine başvurun. Uygulama sahibiyseniz, oturum açma isteği için gerekli tüm parametrelere sahip olduğunuzdan emin olun. |
|90051| Geçersiz temsili belirteci. Geçersiz National Cloud ID ({Cloudıd}) belirtildi.|
|90072| Hesabın, önce kiracıya bir dış Kullanıcı olarak eklenmesi gerekir. Oturumu kapatın ve farklı bir Azure AD hesabıyla tekrar oturum açın.|
|90094| Verme yönetici izinleri gerektirir. Kiracı yöneticinizden bu uygulama için onay sağlamasını isteyin.|
|500021|Kiracı, şirket proxy 'si ile kısıtlıdır. Kaynak erişimini reddetme.|
|500121| Tanımlayıcı kimlik doğrulama isteği sırasında kimlik doğrulaması başarısız oldu.|
|500133| Onaylama işlemi geçerli zaman aralığı içinde değil. Kullanıcı onayı için kullanılmadan önce erişim belirtecinin dolmadığından emin olun veya yeni bir belirteç isteyin.|
|530021|Uygulama, koşullu erişim onaylı uygulama gereksinimlerini karşılamıyor.|
|530032|Güvenlik ilkesi tarafından engellendi.| 
|700016|' {Appıdentifier} ' tanımlayıcılı uygulama ' {tenantName} ' dizininde bulunamadı. Bu durum, uygulama kiracının Yöneticisi tarafından yüklenmediyse veya kiracının kiracısındaki herhangi bir kullanıcı tarafından ' a onaylı olması durumunda gerçekleşebilir. Kimlik doğrulama isteğinizi yanlış kiracıya göndermiş olabilirsiniz.|
|900432|Çapraz bulut isteğinde gizli Istemci desteklenmez.|
|7000218|İstek gövdesi şu parametreyi içermelidir: ' client_assertion ' veya ' client_secret '.|


## <a name="next-steps"></a>Sonraki adımlar

* [Oturum açma raporlarına genel bakış](concept-sign-ins.md)
* [Azure AD raporlarına programlı erişim](concept-reporting-api.md)
