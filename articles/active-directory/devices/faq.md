---
title: Azure Active Directory cihaz yönetimi SSS | Microsoft Docs
description: Cihaz yönetimi hakkında SSS Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec7730dc1143586eb4c5c05fd475b8412546b7a6
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809264"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory cihaz yönetimi SSS

## <a name="general-faq"></a>Genel SSS

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>S: cihazı yakın zamanda kaydettiniz. Azure portal cihazı neden Kullanıcı Bilgilerim altında göremiyorum? Ya da cihaz sahibi, karma Azure Active Directory (Azure AD) ile birleştirilmiş cihazlar için N/A olarak işaretlendi mi?

Y **:** Karma Azure AD 'ye katılmış Windows 10 cihazları **Kullanıcı cihazları**altında gösterilmez.
Azure portal **tüm cihazlar** görünümünü kullanın. PowerShell [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet 'ini de kullanabilirsiniz.

Yalnızca aşağıdaki cihazlar **Kullanıcı cihazları**altında listelenir:

- Hibrit Azure AD 'ye katılmamış tüm kişisel cihazlar. 
- Windows olmayan tüm 10 veya Windows Server 2016 cihazları.
- Tüm Windows dışı cihazlar. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>S: istemcinin cihaz kayıt durumunun ne olduğunu Nasıl yaparım?.

Y **:** Azure portal, **tüm cihazlar**' a gidin. Cihaz KIMLIĞINI kullanarak cihazı arayın. JOIN türü sütununun altındaki değeri denetleyin. Bazen, cihazın sıfırlanması veya yeniden oluşturulması gerekebilir. Bu nedenle cihazdaki cihaz kayıt durumunu da denetlemek gereklidir:

- Windows 10 ve Windows Server 2016 veya üzeri cihazlarda `dsregcmd.exe /status`çalıştırın.
- Alt düzey işletim sistemi sürümleri için `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`çalıştırın.

Y **:** Sorun giderme bilgileri için şu makalelere bakın:
- [Dsregcmd komutunu kullanan cihazların sorunlarını giderme](troubleshoot-device-dsregcmd.md)
- [Karma Azure Active Directory katılmış Windows 10 ve Windows Server 2016 cihazlarında sorun giderme](troubleshoot-hybrid-join-windows-current.md)
- [Karma Azure Active Directory katılmış alt düzey cihazlarda sorun giderme](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>S: Azure portal Kullanıcı bilgileri altında cihaz kaydını görüyorum. Ve durumu cihazda kayıtlı olarak görüyorum. Koşullu erişimi kullanmak için doğru şekilde ayarlandım mı?

Y **:** **DeviceID**'nin gösterdiği cihaz JOIN durumu, Azure AD 'deki durumla eşleşmelidir ve koşullu erişim için herhangi bir değerlendirme ölçütünü karşılamaları gerekir. Daha fazla bilgi için bkz. [koşullu erişim ile Cloud App erişimi için yönetilen cihazlar gerektirme](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices-"></a>S: Kullanıcılarım, "kuruluşunuz cihazı sildi" veya "kuruluşunuz cihazı devre dışı bırakmış" iletisini Windows 10 cihazlarında neden alıyorum?

Y **:** Azure AD 'ye katılmış veya kayıtlı Windows 10 cihazlarında, kullanıcılara çoklu oturum açma özelliği sağlayan bir [birincil yenileme belirteci (PRT)](concept-primary-refresh-token.md) verilir. PRT 'nin geçerliliği, cihazın kararlılığını temel alır. Cihaz, cihazın kendisinden eylemi başlatmadan Azure AD 'de silinmiş veya devre dışı bırakılmışsa bu iletiyi görür. Azure AD 'de aşağıdaki senaryolardan birinde bir cihaz silinebilir veya devre dışı bırakılabilir: 

- Kullanıcı, cihazı uygulamalarım portalından devre dışı bırakır. 
- Yönetici (veya Kullanıcı) Azure portal veya PowerShell kullanarak cihazı siler veya devre dışı bırakır
- Karma Azure AD 'ye katılmış: bir yönetici, Azure AD 'den silinmekte olan cihazların, eşitleme kapsamının dışına çıkmasına neden olan cihazları kaldırır
- AAD 'yi yükseltmek 1.4. xx. x sürümüne bağlanın. [Azure AD Connect 1.4. xx. x ve cihaz görünümlerini anlama](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-device-disappearance).


Bu eylemlerin nasıl geri alınacağını aşağıda görebilirsiniz.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>S: Azure portal veya Windows PowerShell kullanarak cihazımı devre dışı veya sildim. Ancak cihazdaki yerel durum, hala kayıtlı olduğunu söylüyor. Ne yapmam gerekir?

Y **:** Bu işlem tasarıma göre yapılır. Bu durumda, cihazın buluttaki kaynaklara erişimi yoktur. Yöneticiler, yetkisiz erişimi engellemek için eski, kaybolan veya çalınan cihazlar için bu eylemi gerçekleştirebilir. Bu eylem istenmeden gerçekleştirildiyse, cihazı aşağıda açıklandığı gibi yeniden etkinleştirmeniz veya yeniden kaydetmeniz gerekir

- Cihaz Azure AD 'de devre dışı bırakılmışsa, yeterli ayrıcalıklara sahip bir yönetici tarafından Azure AD portalından izin verebilir  
  > [!NOTE]
  > Azure AD Connect kullanarak cihazları eşitlebiliyorsanız, karma Azure AD 'ye katılmış cihazlar sonraki eşitleme çevrimi sırasında otomatik olarak yeniden etkinleştirilecek. Bu nedenle, karma bir Azure AD 'ye katılmış cihazı devre dışı bırakmanız gerekirse, şirket içi AD 'nizden devre dışı bırakmanız gerekir

 - Cihaz Azure AD 'de silinirse, cihazı yeniden kaydetmeniz gerekir. Yeniden kaydolmak için cihazda el ile işlem yapmanız gerekir. Cihaz durumuna göre yeniden kayda yönelik yönergeler için aşağıya bakın. 

      Hibrit Azure AD 'ye katılmış Windows 10 ve Windows Server 2016/2019 cihazlarını yeniden kaydetmek için aşağıdaki adımları uygulayın:

      1. Komut istemi 'ni yönetici olarak açın.
      1. `dsregcmd.exe /debug /leave` yazın.
      1. Oturumu kapatın ve Azure AD ile cihazı kaydeden zamanlanmış görevi tetiklemek için oturum açın. 

      Karma Azure AD 'ye katılmış olan alt düzey Windows işletim sistemi sürümleri için aşağıdaki adımları uygulayın:

      1. Komut istemi 'ni yönetici olarak açın.
      1. `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"` yazın.
      1. `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"` yazın.

      Azure AD 'ye katılmış cihazlar Windows 10 cihazlarında aşağıdaki adımları uygulayın:

      1. Komut istemi 'ni yönetici olarak açın
      1. `dsregcmd /forcerecovery` girin (Note: Bu eylemi gerçekleştirmek için bir yönetici olmanız gerekir).
      1. Açılan iletişim kutusunda "oturum aç" a tıklayın ve oturum açma işlemine devam edin.
      1. Oturumu kapatın ve kurtarmayı gerçekleştirmek için cihaza yeniden oturum açın.

      Azure AD kayıtlı Windows 10 cihazları için aşağıdaki adımları uygulayın:

      1. **İş veya okula erişim** > **Ayarlar** > **hesaplar** ' a gidin. 
      1. Hesabı seçin ve **bağlantıyı kes**' i seçin.
      1. "+ Bağlan" düğmesine tıklayın ve oturum açma işlemine giderek cihazı yeniden kaydedin.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>S: Azure portal neden yinelenen cihaz girdileri görüyorum?

**C:**

- Windows 10 ve Windows Server 2016 için, yinelenen katılmayı kaldırma ve aynı cihaza yeniden bağlanma girişimi yinelenen girişlere neden olabilir. 
- **İş veya okul hesabı ekle** ' nin kullanıldığı her bir Windows kullanıcısı, aynı cihaz adına sahip yeni bir cihaz kaydı oluşturur.
- Şirket içi Azure Directory etki alanına katılmış alt düzey Windows işletim sistemi sürümleri için otomatik kayıt, cihazda oturum açan her bir etki alanı kullanıcısı için aynı cihaz adına sahip yeni bir cihaz kaydı oluşturur. 
- Aynı ada sahip silinen, yeniden yüklenen ve yeniden katılan bir Azure AD 'ye katılmış makine, aynı cihaz adına sahip başka bir kayıt olarak gösterilir.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>S: Azure AD 'de Windows 10 cihaz kaydı FIPS modunda TPMs 'yi destekliyor mu?

Y **:** Hayır, şu anda tüm cihaz durumları için Windows 10 ' da cihaz kaydı-hibrit Azure AD JOIN, Azure AD JOIN ve Azure AD kaydedildi-FIPS modunda TPMs 'yi desteklemez. Azure AD 'ye başarıyla katılmanız veya kaydolmak için, bu cihazlarda TPMs 'lerin FIPS modunun kapalı olması gerekir

---

**S: Kullanıcı neden Azure portal devre dışı bırakıldığım bir cihazdan kaynaklara erişmeye devam edebilir?**

Y **:** İptal etme işlemi, Azure AD cihazının devre dışı olarak işaretlenme zamanından itibaren uygulanması için bir saat kadar sürer.

>[!NOTE] 
>Kayıtlı cihazlar için, kullanıcıların kaynaklara erişemese emin olmak için cihazı kaldırmanızı öneririz. Daha fazla bilgi için bkz. [cihaz kaydı nedir?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>S: neden Azure portal KAYıTLı sütun altında "bekliyor" olarak işaretlenmiş cihazlar var mı?

**A**: bekliyor, cihazın kayıtlı olmadığını gösterir. Bu durum, bir cihazın şirket içi AD 'den Azure AD Connect kullanılarak eşitlendiğinden ve cihaz kaydı için hazırlandığını gösterir. Bu cihazların KATıLıM türü "hibrit Azure AD 'ye katılmış" olarak ayarlanmıştır. [Karma Azure Active Directory JOIN uygulamanızın nasıl planlanacağı](hybrid-azuread-join-plan.md)hakkında daha fazla bilgi edinin.

>[!NOTE]
>Bir cihaz, kayıtlı durumunun "bekliyor" olarak değiştirilmesini de değiştirebilir
>* Bir cihaz önce Azure AD 'den silinirse ve şirket içi AD 'den yeniden eşitlendiğinde.
>* Bir cihaz Azure AD Connect bir eşitleme kapsamından kaldırılırsa ve geri eklendiyse.
>
>Her iki durumda da, bu cihazların her birinde cihazı el ile yeniden kaydetmeniz gerekir. Cihazın daha önce kayıtlı olup olmadığını gözden geçirmek için, [dsregcmd komutunu kullanarak cihazlarda sorun giderme](troubleshoot-device-dsregcmd.md)yapabilirsiniz.

---
## <a name="azure-ad-join-faq"></a>Azure AD 'ye ekleme hakkında SSS

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>S: Azure AD 'ye katılmış bir cihazın cihazdan yerel olarak katılmasını Nasıl yaparım? mı?

Y **:** Saf Azure AD 'ye katılmış cihazlarda, çevrimdışı bir yerel yönetici hesabınız olduğundan emin olun veya bir tane oluşturun. Azure AD Kullanıcı kimlik bilgileriyle oturum açamazsınız. Ardından, **ayarlar** > **hesaplara** gidin. **iş veya okul erişimi** > . Hesabınızı seçin ve **bağlantıyı kes**' i seçin. İstemleri izleyin ve istendiğinde yerel yönetici kimlik bilgilerini sağlayın. Birleştirmeyi kaldırma işlemini tamamlaması için cihazı yeniden başlatın.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>S: Kullanıcılarım, Azure AD 'de silinen veya devre dışı bırakılan Azure AD 'ye katılmış cihazlarda oturum açabilir mi?

**Y:** Evet. Windows, daha önce oturum açan kullanıcıların ağ bağlantısı olmadan bile masaüstüne hızlı erişmesini sağlayan önbelleğe alınmış bir Kullanıcı adı ve parola özelliğine sahiptir. 

Azure AD 'de bir cihaz silindiğinde veya devre dışı bırakıldığında, Windows cihazı tarafından tanınmaz. Böylece daha önce oturum açan kullanıcılar, önbelleğe alınmış Kullanıcı adı ve parolasıyla masaüstüne erişmeye devam eder. Ancak, cihaz silindiği veya devre dışı bırakıldığı için, kullanıcılar cihaz tabanlı koşullu erişim ile korunan kaynaklara erişemez. 

Daha önce oturum açmış olan kullanıcılar cihaza erişemez. Önbelleğe alınmış Kullanıcı adı ve parola etkin değil. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>S: devre dışı bırakılmış veya silinmiş bir Kullanıcı Azure AD 'ye katılmış cihazlarda oturum açabilir

Y **:** Evet, ancak yalnızca sınırlı bir süre için. Azure AD 'de bir Kullanıcı silindiğinde veya devre dışı bırakıldığında, bu, Windows cihazı tarafından hemen bilinmez. Böylece, daha önce oturum açan kullanıcılar, önbelleğe alınmış Kullanıcı adı ve parolayla masaüstüne erişebilir. 

Genellikle, cihaz Kullanıcı durumunun dört saatten az olduğunu biliyor. Ardından Windows bu kullanıcıların masaüstüne erişimini engeller. Azure AD 'de Kullanıcı silindiğinden veya devre dışı bırakıldığı için, tüm belirteçleri iptal edilir. Bu nedenle herhangi bir kaynağa erişemez. 

Daha önce oturum açmış olmayan silinen veya devre dışı olan kullanıcılar bir cihaza erişemez. Önbelleğe alınmış Kullanıcı adı ve parola etkin değil. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>S: Kullanıcılarım, UPN 'sini değiştirdikten sonra neden Azure AD 'ye katılmış cihazlarda sorun yaşıyor?

Y **:** Şu anda, UPN değişiklikleri Azure AD 'ye katılmış cihazlarda tam olarak desteklenmez. Bu nedenle, Azure AD ile kimlik doğrulaması, UPN değişikliklerinden sonra başarısız olur. Sonuç olarak, kullanıcıların cihazlarında SSO ve koşullu erişim sorunları vardır. Bu durumda, kullanıcıların bu sorunu çözmek için yeni UPN 'sini kullanarak "diğer Kullanıcı" kutucuğunda Windows 'da oturum açması gerekir. Şu anda bu sorunu gidermeye çalışıyoruz. Ancak, Iş için Windows Hello ile oturum açan kullanıcılar bu sorunu etkilemez. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>S: Kullanıcılarım Azure AD 'ye katılmış cihazlardan yazıcı arayamıyor. Bu cihazlardan yazdırmayı nasıl etkinleştirebilirim?

Y **:** Azure AD 'ye katılmış cihazlara yönelik yazıcıları dağıtmak için bkz. [ön kimlik doğrulama Ile Windows Server hibrit bulut yazdırma](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Hibrit bulut yazdırma dağıtımı için bir şirket içi Windows Server gerekir. Şu anda bulut tabanlı yazdırma hizmeti kullanılamıyor. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>S: uzak bir Azure AD 'ye katılmış cihazına Nasıl yaparım? mı?

Y **:** Bkz. [uzaktan Azure Active Directory KATıLMıŞ bilgisayara bağlanma](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>S: Kullanıcılarım neden *burada*buradan görümsin?

Y **:** Belirli koşullu erişim kurallarını belirli bir cihaz durumu gerektirecek şekilde yapılandırdınız mı? Cihaz ölçütlere uymazsa, kullanıcılar engellenir ve bu iletiyi görür. Koşullu erişim ilkesi kurallarını değerlendirin. İletinin önüne geçmek için cihazın ölçütlere uyduğundan emin olun.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>S: Kullanıcılarım Azure AD 'ye katılmış cihazlarda neden Azure Multi-Factor Authentication istemleri alamıyor?

Y **:** Kullanıcı Multi-Factor Authentication kullanarak bir cihazı Azure AD 'ye katabilir veya kaydedebilir. Daha sonra cihazın kendisi bu kullanıcı için güvenilir bir ikinci faktör haline gelir. Aynı kullanıcı cihazda oturum açtığında ve bir uygulamaya eriştiğinde Azure AD, cihazı ikinci bir faktör olarak değerlendirir. Bu kullanıcının ek Multi-Factor Authentication istemler olmadan uygulamalara sorunsuz şekilde erişmesini sağlar. 

Bu davranış:

- Azure AD 'ye katılmış ve Azure AD 'ye kayıtlı cihazlar için geçerlidir, ancak karma Azure AD 'ye katılmış cihazlar için değildir.
- Bu cihazda oturum açan diğer kullanıcılar için geçerli değildir. Bu nedenle, bu cihaza erişen diğer tüm kullanıcılar Multi-Factor Authentication bir zorluk alır. Daha sonra, Multi-Factor Authentication gerektiren uygulamalara erişebilirler.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>S: Azure AD 'ye katıldığım bir cihaz için neden bir *Kullanıcı adı veya parola yanlış* ileti alıyorum?

Y **:** Bu senaryonun yaygın nedenleri şunlardır:

- Kullanıcı kimlik bilgileriniz artık geçerli değil.
- Bilgisayarınız Azure Active Directory ile iletişim kuramıyor. Herhangi bir ağ bağlantısı sorunu olup olmadığını denetleyin.
- Federasyon oturum açma işlemleri, Federasyon sunucunuzun etkin ve erişilebilir olan WS-Trust uç noktalarını desteklemesini gerektirir. 
- Doğrudan kimlik doğrulamasını etkinleştirdiniz. Bu nedenle, oturum açtığınızda geçici Parolanızın değiştirilmesi gerekir.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>S: *neden... bir hata oluştu!* Azure AD 'de Bilgisayarıma katılmayı deneydiğimde iletişim kutusu

Y **:** Bu hata, Intune ile Azure Active Directory kayıt ayarladığınızda oluşur. Azure AD katılımaya çalışan kullanıcıya doğru Intune lisansı atandığından emin olun. Daha fazla bilgi için bkz. [Windows cihazları için kayıt ayarlama](https://docs.microsoft.com/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>S: herhangi bir hata bilgisi almadığım halde, neden Azure AD 'ye katılmayı bir BILGISAYARA devredim?

Y **:** Olası bir neden, yerel yerleşik yönetici hesabını kullanarak cihazda oturum açmadır. Kurulumu tamamlayacak Azure Active Directory JOIN 'i kullanmadan önce farklı bir yerel hesap oluşturun. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q:Windows 10 cihazlarımızda bulunan MS-Organization-P2P-erişim sertifikaları nelerdir?

Y **:** MS-Organization-P2P-erişim sertifikaları Azure AD tarafından hem Azure AD 'ye katılmış hem de karma Azure AD 'ye katılmış cihazlara verilir. Bu sertifikalar, uzak masaüstü senaryoları için aynı Kiracıdaki cihazlar arasında güveni etkinleştirmek üzere kullanılır. Cihaza bir sertifika verilir ve kullanıcıya başka bir sertifika verilir. Cihaz Sertifikası `Local Computer\Personal\Certificates` vardır ve bir gün için geçerlidir. Bu sertifika, cihazın Azure AD 'de hala etkin olması halinde yenilenir (yeni bir sertifika yayımlayarak). Kullanıcı sertifikası `Current User\Personal\Certificates` vardır ve bu sertifika bir gün için de geçerlidir, ancak bir Kullanıcı başka bir Azure AD 'ye katılmış cihaza Uzak Masaüstü oturumu denediğinde talep üzerine verilir. Süre sonu ile yenilenmez. Bu sertifikaların her ikisi de `Local Computer\AAD Token Issuer\Certificates`bulunan MS-Organization-P2P-erişim sertifikası kullanılarak verilir. Bu sertifika, cihaz kaydı sırasında Azure AD tarafından verilir. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Windows 10 cihazlarımızda MS-Organization-P2P-Access tarafından gönderilen birden fazla süresi geçen sertifikayı neden görüyorum? Bunları nasıl silebilirim?

Y **:** Windows 10 sürüm 1709 ' de tanımlanan bir sorun oluştu ve süresi dolmakta olan MS-Organization-P2P-erişim sertifikalarının, şifreleme sorunları nedeniyle bilgisayar deposunda mevcut olmaya devam ettiğinden daha düşük. Büyük/veya çok sayıda sertifikayı işleyemeyen herhangi bir VPN istemcisini (örneğin, Cisco AnyConnect) kullanıyorsanız, kullanıcılarınız ağ bağlantısıyla ilgili sorunlar yaşıyor olabilir. Bu sorun, süresi geçen tüm MS-Organization-P2P-erişim sertifikalarını otomatik olarak silmek için Windows 10 1803 sürümünde düzeltilmiştir. Cihazlarınızı Windows 10 1803 ' a güncelleştirerek bu sorunu çözebilirsiniz. Güncelleştiremezsiniz, olumsuz bir etkisi olmadan bu sertifikaları silebilirsiniz.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Karma Azure AD 'ye ekleme hakkında SSS

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>S: karma Azure AD 'ye katılmış bir cihazın cihazdan yerel olarak katılmasını Nasıl yaparım? mı?

Y **:** Hibrit Azure AD 'ye katılmış cihazlar için otomatik kaydı kapatmayı unutmayın. Zamanlanan görev, cihazı yeniden kaydetmez. Sonra, yönetici olarak bir komut istemi açın ve `dsregcmd.exe /debug /leave`girin. Veya toplu olarak birleştirmeyi kaldırmak için bu komutu çeşitli cihazlarda bir komut dosyası olarak çalıştırın.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>S: karma Azure AD JOIN başarısızlıklarını tanılamak için sorun giderme bilgilerini nerede bulabilirim?

Y **:** Sorun giderme bilgileri için şu makalelere bakın:

- [Karma Azure Active Directory katılmış Windows 10 ve Windows Server 2016 cihazlarında sorun giderme](troubleshoot-hybrid-join-windows-current.md)
- [Karma Azure Active Directory katılmış alt düzey cihazlarda sorun giderme](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>S: Azure AD cihazları listesinde Windows 10 karma Azure AD 'ye katılmış cihazım için neden yinelenen bir Azure AD kaydı görüyorum?

Y **:** Kullanıcılarınız etki alanına katılmış bir cihazdaki hesaplarını uygulamalara eklerken, **Windows 'a hesap ekleme** istenebilir. İstem üzerine **Evet** girerseniz, CIHAZ Azure AD 'ye kaydolur. Güven türü, Azure AD kayıtlı olarak işaretlenir. Kuruluşunuzda karma Azure AD katılmasını etkinleştirdikten sonra cihaz, karma Azure AD 'ye katılmış olarak da alınır. Daha sonra aynı cihaz için iki cihaz durumu gösterilir. 

Karma Azure AD katılımı, Azure AD kayıtlı durumuna göre önceliklidir. Bu nedenle, cihazınız herhangi bir kimlik doğrulaması ve koşullu erişim değerlendirmesi için karma Azure AD 'ye katılmış olarak değerlendirilir. Azure AD kayıtlı cihaz kaydını Azure AD portalından güvenle silebilirsiniz. [Windows 10 makinesinde bu iki durumu kullanmaktan kaçının veya temizleyeceğinizi](hybrid-azuread-join-plan.md#review-things-you-should-know)öğrenin. 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>S: Kullanıcılarım, UPN 'sini değiştirdikten sonra neden Windows 10 karma Azure AD 'ye katılmış cihazlarda sorun yaşıyor?

Y **:** Şu anda UPN değişiklikleri karma Azure AD 'ye katılmış cihazlar ile tam olarak desteklenmez. Kullanıcılar cihazda oturum açabilirler ve şirket içi uygulamalarına erişebilirken, bir UPN değişikliğinden sonra Azure AD ile kimlik doğrulaması başarısız olur. Sonuç olarak, kullanıcıların cihazlarında SSO ve koşullu erişim sorunları vardır. Bu durumda, sorunu çözmek için cihazın Azure AD 'den (yükseltilmiş ayrıcalıklarla "dsregcmd/Leave" komutunu çalıştırın) ve yeniden katılırsanız (otomatik olarak gerçekleşir) açmanız gerekir. Şu anda bu sorunu gidermeye çalışıyoruz. Ancak, Iş için Windows Hello ile oturum açan kullanıcılar bu sorunu etkilemez. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>S: Windows 10 karma Azure AD 'ye katılmış cihazlar, bulut kaynaklarına erişim sağlamak için etki alanı denetleyicisine bir görüş satırı gerektirir mi?

Y **:** Kullanıcı parolasının değiştiği durumlar dışında, hayır. Windows 10 hibrit Azure AD birleşimi tamamlandıktan ve Kullanıcı en az bir kez oturum açtıktan sonra, cihaz, bulut kaynaklarına erişmek için etki alanı denetleyicisine bir görüş satırı gerektirmez. Windows 10, bir parolanın değiştirilmesi dışında internet bağlantısı olan her yerden Azure AD uygulamalarına çoklu oturum açma işlemi gerçekleştirebilir. Iş için Windows Hello ile oturum açan kullanıcılar, etki alanı denetleyiciyle ilgili bir hatta olmasalar bile, bir parola değişikliğinden sonra bile Azure AD uygulamalarına çoklu oturum açma işlemine devam eder. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>S: Kullanıcı parolasını değiştirdiğinde ve Windows 10 karma Azure AD katılmış cihazında şirket ağı dışında oturum açmaya çalışırsa ne olur?

Y **:** Şirket ağı dışında bir parola değiştirilirse (örneğin, Azure AD SSPR kullanarak), Kullanıcı yeni parolayla oturum açıp başarısız olur. Karma Azure AD 'ye katılmış cihazlarda, şirket içi Active Directory birincil yetkilisdir. Bir cihazın etki alanı denetleyicisine bir görüş satırı yoksa, yeni parolayı doğrulayamaz. Bu nedenle, kullanıcının yeni parolalarıyla cihazda oturum açabilmeden önce, etki alanı denetleyicisiyle bağlantı kurması (VPN aracılığıyla veya şirket ağında olduğu gibi) gerekir. Aksi takdirde, Windows 'da önbelleğe alınmış oturum açma özelliği nedeniyle yalnızca eski parolalarıyla oturum açabilir. Ancak, eski parola, belirteç istekleri sırasında Azure AD tarafından geçersiz kılınır ve bu nedenle, çoklu oturum açmayı önler ve cihaz tabanlı koşullu erişim ilkelerine başarısız olur. Iş için Windows Hello kullanıyorsanız bu sorun oluşmaz. 

---

## <a name="azure-ad-register-faq"></a>Azure AD kaydı hakkında SSS

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>S: bir cihazın Azure AD kayıtlı durumunu yerel olarak kaldırmak Nasıl yaparım? istiyor musunuz?

**C:** 
- Windows 10 Azure AD kayıtlı cihazlar için, **iş veya okul erişimi** > **Ayarlar** > **hesaplar** ' a gidin. Hesabınızı seçin ve **bağlantıyı kes**' i seçin. Cihaz kaydı, Windows 10 ' da Kullanıcı profili başına.
- İOS ve Android için, **cihaz kaydı** > Microsoft Authenticator uygulama **ayarlarını** kullanabilir ve **cihazı Sil**' i seçebilirsiniz.
- MacOS için Microsoft Intune Şirket Portalı uygulamasını kullanarak cihazın yönetimden kaydını kaldırın ve kaydı kaldırabilirsiniz. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>S: Kurumsal Windows 10 Cihazlarıma kullanıcıların ek iş hesapları (Azure AD kaydı) eklemelerini nasıl engelleyebilirim?

Y **:** Kullanıcılarınızın kurumsal etki alanına katılmış, Azure AD 'ye katılmış veya karma Azure AD 'ye katılmış Windows 10 cihazlarına ek iş hesapları eklemesini engellemek için aşağıdaki kayıt defterini etkinleştirin. Bu ilke, etki alanına katılmış makinelerin yanlışlıkla aynı kullanıcı hesabıyla kayıtlı olan Azure AD 'yi engellemek için de kullanılabilir. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>S: Android veya iOS BYOD cihazlarını kaydedebilir miyim?

Y **:** Evet, ancak yalnızca Azure cihaz kayıt hizmeti ve hibrit müşteriler ile. Active Directory Federasyon Hizmetleri (AD FS) (AD FS) içindeki şirket içi cihaz kayıt hizmeti ile desteklenmez.

---
### <a name="q-how-can-i-register-a-macos-device"></a>S: macOS cihazını nasıl kaydedebilirim?

Y **:** Aşağıdaki adımları uygulayın:

1.  [Uyumluluk ilkesi oluşturma](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
1.  [MacOS cihazları için koşullu erişim ilkesi tanımlama](../active-directory-conditional-access-azure-portal.md) 

**Açıklamalarının**

- Koşullu erişim ilkenize dahil olan kullanıcılar, kaynaklara erişmek için [macOS 'un desteklenen bir Office sürümüne](../conditional-access/technical-reference.md#client-apps-condition) ihtiyaç duyar. 
- İlk erişim girişimi sırasında, kullanıcılarınızın Şirket portalı 'nı kullanarak cihazı kaydetmesi istenir.

---
## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD kayıtlı cihazlar](concept-azure-ad-register.md) hakkında daha fazla bilgi edinin
- [Azure AD 'ye katılmış cihazlar](concept-azure-ad-join.md) hakkında daha fazla bilgi edinin
- [Karma Azure AD 'ye katılmış cihazlar](concept-azure-ad-join-hybrid.md) hakkında daha fazla bilgi edinin
