---
title: "Azure AD Connect: Azure AD için Federasyondan PHS 'e geçiş | Microsoft Docs"
description: Bu makalede, karma kimlik ortamınızı Federasyondan Parola karması eşitlemesine taşıma hakkında bilgi bulunur.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9603cdf11373891aaa3541330cb7f65c09352496
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818900"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Azure Active Directory için Federasyondan Parola karması eşitlemesine geçiş

Bu makalede, kuruluş etki alanlarınızı Active Directory Federasyon Hizmetleri (AD FS) (AD FS) ' dan Parola karması eşitlemesine nasıl taşıyacağınız açıklanır.

[Bu makaleyi indirebilirsiniz](https://aka.ms/ADFSTOPHSDPDownload).

## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Parola karması eşitlemesine geçiş için Önkoşullar

AD FS kullanarak parola karması eşitlemesini kullanarak geçiş yapmak için aşağıdaki Önkoşullar gereklidir.

### <a name="update-azure-ad-connect"></a>Güncelleştirme Azure AD Connect

Parola karması eşitlemesine geçiş adımlarını başarılı bir şekilde gerçekleştirmek için gereken en düşük düzeyde [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0 olmalıdır. Bu sürüm, oturum açma dönüştürme işleminin gerçekleştirilmesi için önemli değişiklikler içerir ve federasyondan bulut kimlik doğrulamasına kadar olan süreyi dakika olarak geçirmek için genel süreyi azaltır.


> [!IMPORTANT]
> Etki alanlarını Federasyon kimliğinden yönetilen kimliğe dönüştürürken, Kullanıcı dönüştürmesinin gerekli olduğu güncel olmayan belge, araç ve bloglarda okuyabilirsiniz. *Kullanıcıları dönüştürme* artık gerekli değildir. Microsoft bu değişikliği yansıtmak için belge ve araçları güncelleştirmek üzere çalışmaktadır.

Azure AD Connect güncelleştirmek için Azure AD Connect şu adımları uygulayın [: en son sürüme yükseltme](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="password-hash-synchronization-required-permissions"></a>Parola karması eşitleme gerekli izinler

Azure AD Connect, hızlı ayarları veya özel bir yüklemeyi kullanarak yapılandırabilirsiniz. Özel yükleme seçeneğini kullandıysanız, Parola karması eşitleme için [gerekli izinler](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) yerinde olmayabilir.

Azure AD Connect Active Directory Domain Services (AD DS) hizmet hesabı, parola karmalarını eşitlemeye yönelik aşağıdaki izinleri gerektirir:

* Dizin Değişikliklerini Çoğalt
* Dizin değişikliklerini çoğaltma

Bu izinlerin ormandaki tüm etki alanları için hazır olduğunu doğrulamak için artık iyi bir zamandır.

### <a name="plan-the-migration-method"></a>Geçiş yöntemini planlayın

Federal Kimlik yönetiminden Parola karması eşitlemesine ve sorunsuz çoklu oturum açma (SSO) ' ya geçiş yapmak için iki yöntemden birini tercih edebilirsiniz. Kullandığınız yöntem, AD FS örneğinizin ilk olarak nasıl yapılandırıldığına bağlıdır.

* **Azure AD Connect**. Başlangıçta Azure AD Connect kullanarak AD FS yapılandırdıysanız Azure AD Connect Sihirbazı 'nı kullanarak parola karması eşitlemesine geçiş yapmanız *gerekir* .

   Azure AD Connect, Kullanıcı oturum açma yöntemini değiştirdiğinizde **Set-MsolDomainAuthentication** cmdlet 'ini otomatik olarak çalıştırır. Azure AD Connect, Azure AD kiracınızdaki tüm doğrulanmış Federasyon etki alanlarını otomatik olarak kaldırır.

   > [!NOTE]
   > Şu anda, AD FS yapılandırmak için ilk olarak Azure AD Connect kullandıysanız, Kullanıcı oturum açma parolasını Parola karması eşitlemesi olarak değiştirdiğinizde kiracınızdaki tüm etki alanlarını Federasyondan kurtulabilirsiniz. ‎
* **PowerShell ile Azure AD Connect**. Bu yöntemi yalnızca, Azure AD Connect kullanarak AD FS ilk olarak yapılandırmazsanız kullanabilirsiniz. Bu seçenek için yine de Azure AD Connect Sihirbazı aracılığıyla Kullanıcı oturum açma yöntemini değiştirmeniz gerekir. Bu seçenekle ilgili temel fark, sihirbazın **Set-MsolDomainAuthentication** cmdlet 'ini otomatik olarak çalıştırmaz. Bu seçenekle, hangi etki alanlarının ve hangi sırada dönüştürüldüğü üzerinde tam denetime sahip olursunuz.

Hangi yöntemi kullanacağınızı anlamak için aşağıdaki bölümlerde bulunan adımları uygulayın.

#### <a name="verify-current-user-sign-in-settings"></a>Geçerli Kullanıcı oturum açma ayarlarını doğrulama

Geçerli Kullanıcı oturum açma ayarlarınızı doğrulamak için:

1. Genel yönetici hesabı kullanarak [Azure AD portalında](https://aad.portal.azure.com/) oturum açın.
2. **Kullanıcı oturum açma** bölümünde aşağıdaki ayarları doğrulayın:
   * **Federasyon** , **etkin**olarak ayarlandı.
   * **Kesintisiz çoklu oturum açma** **devre dışı**olarak ayarlanmıştır.
   * **Geçişli kimlik doğrulaması** **devre dışı**olarak ayarlanır.

   ![Azure AD Connect Kullanıcı oturum açma bölümündeki ayarların ekran görüntüsü](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Azure AD Connect yapılandırmasını doğrulama

1. Azure AD Connect sunucunuzda Azure AD Connect açın. **Yapılandır**' ı seçin.
2. **Ek görevler** sayfasında, **geçerli yapılandırmayı görüntüle**' yi seçin ve ardından **İleri**' yi seçin.<br />

   ![Ek görevler sayfasında seçilen geçerli yapılandırmayı görüntüle seçeneğinin ekran görüntüsü](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. **Çözümünüzü gözden geçirin** sayfasında **Parola karması eşitleme** durumunu aklınızda edin.<br /> 

   * **Parola karması eşitlemesi** **devre dışı**olarak ayarlandıysa, etkinleştirmek için bu makaledeki adımları izleyin.
   * **Parola karması eşitlemesi** **etkin**olarak ayarlandıysa, bu makaledeki **1. Adım: parola karma eşitlemesini etkinleştirme** bölümüne atlayabilirsiniz.
4. **Çözümünüzü gözden geçirin** sayfasında Active Directory Federasyon Hizmetleri (AD FS) ' ye kaydırın **(AD FS)** .<br />

   * Bu bölümde AD FS yapılandırması görüntülenirse, AD FS özgün olarak Azure AD Connect kullanarak yapılandırıldığını varsayabilirsiniz. Azure AD Connect **Kullanıcı oturum açma** seçeneğini kullanarak etki alanlarınızı, Federasyon kimliğinden yönetilen kimliğe dönüştürebilirsiniz. İşlem, **A: Azure AD Connect kullanarak Federasyondan Parola karması eşitlemesine geçiş yapın**.
   * Geçerli ayarlarda AD FS listelenmemişse, PowerShell kullanarak etki alanlarınızı, Federasyon kimliğinden yönetilen kimliğe el ile dönüştürmeniz gerekir. Bu işlem hakkında daha fazla bilgi için, **B: Azure AD Connect ve PowerShell kullanarak Federasyondan Parola karması eşitlemesine geçiş**bölümüne bakın.

### <a name="document-current-federation-settings"></a>Geçerli Federasyon ayarlarını belgele

Geçerli Federasyon ayarlarınızı bulmak için **Get-MsolDomainFederationSettings** cmdlet 'ini çalıştırın:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Örnek:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Federasyon tasarımınız ve dağıtım belgeleriniz için özelleştirilmiş olabilecek tüm ayarları doğrulayın. Özellikle, **Preferredauthenticationprotocol**, **Supportsmfa**ve **promptloginbehavior**içindeki özelleştirmeleri arayın.

Daha fazla bilgi için şu makalelere bakın:

* [AD FS Prompt = oturum açma parametresi desteği](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> **Supportsmfa** **değeri true**olarak ayarlanırsa, Kullanıcı kimlik doğrulama akışına ikinci öğeli bir sınama eklemek için bir şirket içi Multi-Factor Authentication çözümü kullanıyorsunuz. Bu kurulum, bu etki alanını federe bilgisayardan yönetilen kimlik doğrulamasına dönüştürdükten sonra artık Azure AD kimlik doğrulama senaryolarında çalışmaz. Federasyonu devre dışı bıraktıktan sonra şirket içi Federasyonla ilişkiyi ve bu şirket içi MFA bağdaştırıcılarını içerir. 
>
> Bunun yerine, aynı işlevi gerçekleştirmek için Azure Multi-Factor Authentication bulut tabanlı hizmetini kullanın. Devam etmeden önce Multi-Factor Authentication gereksinimlerinizi dikkatle değerlendirin. Etki alanlarınızı dönüştürmeden önce Azure Multi-Factor Authentication, lisanslama etkilerini ve Kullanıcı kayıt işlemini nasıl kullanacağınızı anladığınızdan emin olun.

#### <a name="back-up-federation-settings"></a>Federasyon ayarlarını yedekleme

Bu makalede açıklanan süreçler sırasında AD FS grubunuzdaki diğer bağlı olan taraflardan hiçbir değişiklik yapılmasa da, geri yükleyebilmeniz için AD FS grubunuzun geçerli geçerli yedeğine sahip olmanız önerilir. Ücretsiz Microsoft [AD FS hızlı geri yükleme aracını](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool)kullanarak geçerli geçerli bir yedekleme oluşturabilirsiniz. Aracı AD FS yedeklemek ve var olan bir grubu geri yüklemek ya da yeni bir grup oluşturmak için kullanabilirsiniz.

En azından AD FS hızlı geri yükleme aracı 'nı kullanmayı tercih ederseniz, Microsoft Office 365 kimlik platformu bağlı olan taraf güvenini ve eklediğiniz tüm ilişkili özel talep kurallarını dışarı aktarmanız gerekir. Aşağıdaki PowerShell örneğini kullanarak bağlı olan taraf güvenini ve ilişkili talep kurallarını dışarı aktarabilirsiniz:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Dağıtım konuları ve AD FS kullanımı

Bu bölümde AD FS kullanımı ile ilgili dağıtım konuları ve ayrıntıları açıklanmaktadır.

### <a name="current-ad-fs-use"></a>Geçerli AD FS kullanımı

Federasyon kimliğinden yönetilen kimliğe dönüştürmeden önce Azure AD, Office 365 ve diğer uygulamalar (bağlı olan taraf güvenleri) için AD FS Şu anda nasıl kullantığınızdan yakından bakın. Özellikle, aşağıdaki tabloda açıklanan senaryoları göz önünde bulundurun:

| Eğer | Ni |
|-|-|
| Diğer uygulamalarla AD FS kullanmayı planlayın (Azure AD ve Office 365 dışında). | Etki alanlarınızı dönüştürdükten sonra hem AD FS hem de Azure AD 'yi kullanırsınız. Kullanıcı deneyimini göz önünde bulundurun. Bazı senaryolarda, kullanıcıların iki kez kimlik doğrulaması yapması gerekebilir: Azure AD 'ye (bir Kullanıcı, Office 365 gibi diğer uygulamalara SSO erişimi alır) ve hala bağlı olan taraf güveni olarak AD FS bağlı olan tüm uygulamalar için. |
| AD FS örneğiniz çok büyük bir özelleştirilmiş ve OnLoad. js dosyasındaki belirli özelleştirme ayarlarına bağımlıdır (örneğin, oturum açma deneyimini değiştirdiyseniz, kullanıcıların Kullanıcı sorumlusu yerine yalnızca Kullanıcı adı için **sAMAccountName** biçimi kullanması gerekir) Ad (UPN) veya kuruluşunuz, oturum açma deneyimini yoğun bir şekilde markalandırmıştır. OnLoad. js dosyası Azure AD 'de yinelenemez. | Devam etmeden önce Azure AD 'nin geçerli özelleştirme gereksinimlerinizi karşılayabildiğini doğrulamanız gerekir. Daha fazla bilgi ve yönergeler için AD FS marka ve AD FS özelleştirme bölümlerine bakın.|
| Kimlik doğrulama istemcilerinin önceki sürümlerini engellemek için AD FS kullanırsınız.| [Koşullu erişim denetimleri](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) ve [Exchange Online istemci erişim kurallarından](https://aka.ms/EXOCAR)oluşan bir birleşimi kullanarak, kimlik doğrulama istemcilerinin önceki sürümlerini engelleyen AD FS denetimlerini değiştirmeyi düşünün. |
| Kullanıcıların AD FS kimlik doğrulaması yapıldığında, şirket içi Multi-Factor Authentication sunucusu çözümüne karşı kullanıcıların Multi-Factor Authentication gerçekleştirmesini zorunlu kılabilirsiniz.| Yönetilen bir kimlik etki alanında, kimlik doğrulama akışına şirket içi Multi-Factor Authentication çözümü aracılığıyla çok faktörlü kimlik doğrulama sınaması ekleyemezsiniz. Ancak, etki alanı dönüştürüldükten sonra çok faktörlü kimlik doğrulaması için Azure Multi-Factor Authentication hizmetini kullanabilirsiniz.<br /><br /> Kullanıcılarınız Şu anda Azure Multi-Factor Authentication kullanmıyorsanız, bir kerelik Kullanıcı kaydı adımı gereklidir. Kullanıcılarınıza planlı kaydı hazırlamanız ve bu kayıtları iletmeli. |
| Office 365 erişimini denetlemek için şu anda AD FS 'de erişim denetim ilkeleri (AuthZ kuralları) kullanıyorsunuz.| Aynı Azure AD [koşullu erişim ilkeleri](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) ve [Exchange Online istemci erişim kuralları](https://aka.ms/EXOCAR)ile ilkeleri değiştirmeyi göz önünde bulundurun.|

### <a name="common-ad-fs-customizations"></a>Ortak AD FS özelleştirmeleri

Bu bölümde, yaygın AD FS özelleştirmeler açıklanmaktadır.

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork talebi

Kimliği doğrulanan kullanıcı kurumsal ağın içindeyse, AD FS **InsideCorporateNetwork** talebini yayınlar. Bu talep daha sonra Azure AD 'ye geçirilebilirler. Talep, kullanıcının ağ konumuna göre çok faktörlü kimlik doğrulamasını atlamak için kullanılır. AD FS ' de bu işlevselliğin etkin olup olmadığını belirlemeyi öğrenmek için bkz. [Federasyon kullanıcıları Için güvenilen IP 'ler](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud).

**InsideCorporateNetwork** talebi, etki alanlarınız Parola karması eşitlemesine dönüştürüldükten sonra kullanılamaz. Bu işlevi değiştirmek için [Azure AD 'de adlandırılmış konumlar](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) kullanabilirsiniz.

Adlandırılmış konumları yapılandırdıktan sonra, ağ **tüm güvenilen konumları** veya **MFA güvenilir IP** değerlerini dahil etmek veya hariç tutmak Için yapılandırılmış tüm koşullu erişim ilkelerini yeni adlandırılmış konumları yansıtacak şekilde güncelleştirmeniz gerekir.

Koşullu erişimde **konum** koşulu hakkında daha fazla bilgi için bkz. [Active Directory Koşullu erişim konumları](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Karma Azure AD 'ye katılmış cihazlar

Bir cihazı Azure AD 'ye katdığınızda, cihazların güvenlik ve uyumluluk için erişim standartlarınızı karşıladığından emin olan koşullu erişim kuralları oluşturabilirsiniz. Ayrıca, kullanıcılar kişisel hesap yerine kuruluş iş veya okul hesabı kullanarak bir cihazda oturum açabilirler. Karma Azure AD 'ye katılmış cihazları kullandığınızda Active Directory etki alanına katılmış cihazlarınızı Azure AD 'ye ekleyebilirsiniz. Federal ortamınız bu özelliği kullanacak şekilde ayarlanmış olabilir.

Etki alanlarınız, Windows 10 istemcileri için Parola karması eşitlemesine dönüştürüldükten sonra etki alanına katılmış tüm cihazlarda çalışmaya devam ettiğinden emin olmak için, Active Directory bilgisayarı eşitlemek üzere Azure AD Connect cihaz seçeneklerini kullanmanız gerekir hesapları Azure AD 'ye. 

Windows 8 ve Windows 7 bilgisayar hesaplarında, karma birleşimi bilgisayarı Azure AD 'ye kaydetmek için sorunsuz SSO kullanır. Windows 10 cihazları için yaptığınız gibi Windows 8 ve Windows 7 bilgisayar hesaplarını eşitlemeniz gerekmez. Ancak, güncelleştirilmiş bir workplacejoın. exe dosyasını (. msi dosyası aracılığıyla) Windows 8 ve Windows 7 istemcilerine dağıtmanız gerekir, böylece bunları sorunsuz SSO kullanarak kaydedebilirler. [. Msi dosyasını indirin](https://www.microsoft.com/download/details.aspx?id=53554).

Daha fazla bilgi için bkz. [karma Azure AD 'ye katılmış cihazları yapılandırma](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Olanağı

Kuruluşunuz, kuruluşa daha uygun bilgileri göstermek için [AD FS oturum açma sayfalarınızı özelleştirmiş](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) ve [Azure AD oturum açma sayfasında benzer özelleştirmeler](https://docs.microsoft.com/azure/active-directory/customize-branding)yapmayı düşünün.

Benzer özelleştirmeler kullanılabilir olsa da, oturum açma sayfalarındaki bazı görsel değişiklikler dönüştürmeden sonra beklenmelidir. Kullanıcılara yönelik iletişimlerdeki beklenen değişiklikler hakkında bilgi vermek isteyebilirsiniz.

> [!NOTE]
> Kuruluş markası yalnızca Azure Active Directory için Premium veya temel lisans satın aldıysanız veya Office 365 lisansınız varsa kullanılabilir.

## <a name="plan-deployment-and-support"></a>Dağıtım ve Destek planlaması

Dağıtım ve Destek planlaması yapmanıza yardımcı olması için bu bölümde açıklanan görevleri doldurun.

### <a name="plan-the-maintenance-window"></a>Bakım penceresini planlayın

Etki alanı dönüştürme işlemi nispeten hızlı olsa da, Azure AD, etki alanı dönüştürme işlemi tamamlandıktan sonra AD FS sunucularınıza en fazla dört saat sonra bazı kimlik doğrulama istekleri gönderilmeye devam edebilir. Bu dört saatlik pencere sırasında ve çeşitli hizmet tarafı önbelleklerine bağlı olarak, Azure AD bu kimlik doğrulamaları kabul etmeyebilir. Kullanıcılar bir hata alabilir. Kullanıcı hala AD FS göre kimlik doğrulaması yapabilir, ancak Azure AD artık kullanıcının verilen belirtecini kabul etmez çünkü bu, Federasyon güveni kaldırılmıştır.

Hizmet tarafı önbelleği temizlenmeden önce bu dönüştürme sonrası pencere sırasında hizmetlere yalnızca bir Web tarayıcısı üzerinden erişen kullanıcılar etkilenir. Exchange Online, kimlik bilgilerinin bir süre önbellekte tutulmasını sağladığından eski istemcilerin (Exchange ActiveSync, Outlook 2010/2013) etkilenmemesi beklenmez. Önbellek, kullanıcıyı sessizce yeniden kimlik doğrulaması yapmak için kullanılır. Kullanıcının AD FS döndürmesi gerekmez. Bu istemciler için cihazda depolanan kimlik bilgileri, önbelleğe alınmış bu onay edildikten sonra sessizce yeniden kimlik doğrulaması yapmak için kullanılır. Kullanıcıların, etki alanı dönüştürme işleminin sonucu olarak herhangi bir parola istemi alması beklenmez. 

Modern kimlik doğrulama istemcileri (Office 2016 ve Office 2013, iOS ve Android Uygulamaları) AD FS için geri dönmek yerine kaynaklara devam etmek için yeni erişim belirteçleri elde etmek üzere geçerli bir yenileme belirteci kullanır. Bu istemciler, etki alanı dönüştürme işleminden kaynaklanan tüm parola istemlerine karşı silinir. İstemciler ek yapılandırma olmadan çalışmaya devam edecektir.

> [!IMPORTANT]
> Tüm kullanıcıların bulut kimlik doğrulamasını kullanarak başarıyla kimlik doğrulaması yapabileceği doğrulayana kadar AD FS ortamınızı kapatmayın veya Office 365 bağlı olan taraf güvenini kaldırmayın.

### <a name="plan-for-rollback"></a>Geri alma planı

Hızlıca çözemeyeceğinize ilişkin önemli bir sorunla karşılaşırsanız, çözümü federasyona geri almayı seçebilirsiniz. Dağıtımınız istenen şekilde kullanıma sunulmazsa ne yapılacağını planlamanız önemlidir. Dağıtım sırasında etki alanının veya kullanıcıların dönüştürülmesi başarısız olursa veya federasyona geri dönmeniz gerekirse, herhangi bir kesinti gereksinimini hafifletmek ve kullanıcılarınızın etkisini azaltmak zorundasınız.

#### <a name="to-roll-back"></a>Geri almak için

Geri alma planlamak için, belirli dağıtım ayrıntılarınız için Federasyon tasarımı ve dağıtım belgelerini kontrol edin. İşlem şu görevleri içermelidir:

* **Convert-Msoldomaintofedere** cmdlet 'ini kullanarak yönetilen etki alanlarını Federasyon etki alanlarına dönüştürme.
* Gerekirse, ek talep kurallarını yapılandırın.

### <a name="plan-communications"></a>İletişimleri planlayın

Dağıtım ve desteğin planlanmasına ilişkin önemli bir bölüm, kullanıcılarınızın yaklaşan değişiklikler hakkında bilinçli olarak bilgilendirilmesi sağlamaktır. Kullanıcılar, ne yaşayabileceklerini ve bunların ne yapılması gerektiğini önceden bilmelidir. 

Parola karması eşitlemesi ve sorunsuz SSO dağıtıldıktan sonra, Office 365 ' a ve Azure AD değişiklikleri aracılığıyla kimliği doğrulanan diğer kaynaklara erişmek için Kullanıcı oturum açma deneyimi. Ağ dışındaki kullanıcılar yalnızca Azure AD oturum açma sayfasını görür. Bu kullanıcılar, dışarıdan yönelik Web uygulaması ara sunucuları tarafından sunulan form tabanlı sayfaya yönlendirilmez.

İletişim stratejinize aşağıdaki öğeleri ekleyin:

* Şunları kullanarak kullanıcılara yaklaşan ve yayınlanmış işlevleri bildirin:
   * E-posta ve diğer iç iletişim kanalları.
   * Posterler gibi görseller.
   * Executive, Live veya diğer iletişimler.
* Kimlerin iletişimleri ve iletişimleri kimin ne zaman özelleştireceğinizi ve ne zaman gönderileceğini saptayın.

## <a name="implement-your-solution"></a>Çözümünüzü uygulama

Çözümünüzü planladıktan sonra. Şimdi uygulamayı şimdi uygulayabilirsiniz. Uygulama aşağıdaki bileşenleri içerir:

* Parola karması eşitlemesi etkinleştiriliyor.
* Sorunsuz SSO için hazırlanıyor.
* Oturum açma yöntemini Parola karması eşitlemesine değiştirme ve sorunsuz SSO 'yu etkinleştirme.

### <a name="step-1-enable-password-hash-synchronization"></a>1\. Adım: parola karma eşitlemesini etkinleştirme

Bu çözümü uygulamak için ilk adım, Azure AD Connect Sihirbazı 'nı kullanarak parola karması eşitlemesini etkinleştirmektir. Parola karması eşitleme, Federasyon kullanan ortamlarda etkinleştirebilmeniz için isteğe bağlı bir özelliktir. Kimlik doğrulama akışı üzerinde hiçbir etkisi yoktur. Bu durumda Azure AD Connect, Federasyon kullanarak oturum açan kullanıcıları etkilemeden parola karmalarını eşitlemeye başlar.

Bu nedenle, etki alanının oturum açma yöntemini değiştirmeden önce bu adımı bir hazırlık görevi olarak tamamlamanızı öneririz. Daha sonra, Parola karması eşitlemesinin doğru şekilde çalıştığını doğrulamak için de bir zaman vardır.

Parola karması eşitlemesini etkinleştirmek için:

1. Azure AD Connect sunucusunda, Azure AD Connect Sihirbazı ' nı açın ve ardından **Yapılandır**' ı seçin.
2. **Eşitleme seçeneklerini Özelleştir**' i seçin ve ardından **İleri**' yi seçin.
3. **Azure AD 'ye Bağlan** sayfasında, bir genel yönetici hesabının kullanıcı adını ve parolasını girin.
4. **Dizinlerinizi bağlama** sayfasında **İleri**' yi seçin.
5. **Etki alanı ve OU filtreleme** sayfasında **İleri**' yi seçin.
6. **Isteğe bağlı özellikler** sayfasında **parola eşitleme**' yi seçin ve ardından **İleri**' yi seçin.
 
   ![Isteğe bağlı özellikler sayfasında seçilen parola eşitleme seçeneğinin ekran görüntüsü](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Kalan sayfalarda **İleri ' yi** seçin. Son sayfada **Yapılandır**' ı seçin.
8. Azure AD Connect, sonraki eşitlemede parola karmalarını eşitlemeye başlar.

Parola karması eşitlemesi etkinleştirildikten sonra, Azure AD Connect eşitleme kapsamındaki tüm kullanıcılar için parola karmaları yeniden karma hale getirilir ve Azure AD 'ye yazılır. Kullanıcı sayısına bağlı olarak, bu işlem dakika veya birkaç saat sürebilir.

Planlama amacıyla, yaklaşık 20.000 kullanıcının 1 saat içinde işlendiğini tahmin etmelisiniz.

Parola karma eşitlemesinin düzgün çalıştığını doğrulamak için Azure AD Connect sihirbazındaki **sorun giderme** görevini doldurun:

1. Yönetici olarak çalıştır seçeneğini kullanarak Azure AD Connect sunucunuzda yeni bir Windows PowerShell oturumu açın.
2. `Set-ExecutionPolicy RemoteSigned` veya `Set-ExecutionPolicy Unrestricted`çalıştırın.
3. Azure AD Connect Sihirbazı 'nı başlatın.
4. **Ek görevler** sayfasına gidin, **sorun gider**' i seçin ve ardından **İleri**' yi seçin.
5. **Sorun giderme** sayfasında, PowerShell 'de sorun giderme menüsünü başlatmak için **Başlat** ' ı seçin.
6. Ana menüde, **Parola karması eşitlemesini sorun gider**' i seçin.
7. Alt menüde **Parola karması eşitleme**' yi seçin.

Sorun giderme sorunları için bkz. [Azure AD Connect Sync ile parola karması eşitleme sorunlarını giderme](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization).

### <a name="step-2-prepare-for-seamless-sso"></a>2\. Adım: sorunsuz SSO için hazırlanma

Cihazlarınızın sorunsuz SSO kullanması için, Active Directory ' de bir Grup İlkesi kullanarak kullanıcıların intranet bölgesi ayarlarına bir Azure AD URL 'SI eklemeniz gerekir.

Varsayılan olarak, Web tarayıcıları bir URL 'den Internet veya intranet gibi doğru bölgeyi otomatik olarak hesaplar. Örneğin, **http:\/\/contoso/** intranet bölgesine eşlenir ve **http:\/\/intranet.contoso.com** Maps for Internet Zone (URL bir nokta içerdiğinden). Tarayıcılar, yalnızca URL 'YI tarayıcının intranet bölgesine eklediğinizde Azure AD URL 'SI gibi bir bulut uç noktasına Kerberos bileti gönderir.

Cihazlarınızda gerekli değişiklikleri [almak](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) için adımları izleyin.

> [!IMPORTANT]
> Bu değişikliğin yapılması, kullanıcılarınızın Azure AD 'de oturum açma biçimini değiştirmez. Ancak, devam etmeden önce bu yapılandırmayı tüm cihazlarınıza uygulamanız önemlidir. Bu yapılandırmayı almamış cihazlarda oturum açan kullanıcıların, Azure AD 'de oturum açmak için bir Kullanıcı adı ve parola girmesi gerekir.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>3\. Adım: oturum açma yöntemini Parola karması eşitlemesi olarak değiştirme ve sorunsuz SSO 'yu etkinleştirme

Parola karması eşitlemesi ve sorunsuz SSO 'yu etkinleştirmek için oturum açma yöntemini değiştirmek üzere iki seçeneğiniz vardır.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Seçenek A: Azure AD Connect kullanarak Federasyondan Parola karması eşitlemeye geçiş yapın

AD FS ortamınızı başlangıçta Azure AD Connect kullanarak yapılandırdıysanız bu yöntemi kullanın. AD FS *ortamınızı ilk olarak* Azure AD Connect kullanarak yapılandırmadıysanız bu yöntemi kullanamazsınız.

İlk olarak, oturum açma yöntemini değiştirin:

1. Azure AD Connect sunucusunda, Azure AD Connect sihirbazını açın.
2. **Kullanıcı oturumunu Değiştir**' i seçin ve ardından **İleri**' yi seçin. 

   ![Ek görevler sayfasında Kullanıcı oturum açma değiştirme seçeneğinin ekran görüntüsü](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. **Azure AD 'ye Bağlan** sayfasında, bir genel yönetici hesabının kullanıcı adını ve parolasını girin.
4. **Kullanıcı oturum açma** sayfasında **Parola karması eşitleme düğmesini**seçin. **Kullanıcı hesaplarını dönüştürmeyin** onay kutusunu seçtiğinizden emin olun. Seçeneği kullanım dışıdır. **Çoklu oturum açmayı etkinleştir**' i seçin ve ardından **İleri**' yi seçin.

   ![Çoklu oturum açmayı etkinleştir sayfasının ekran görüntüsü](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Azure AD Connect Version 1.1.880.0 ile başlayarak, **sorunsuz çoklu oturum açma** onay kutusu varsayılan olarak seçilidir.

   > [!IMPORTANT]
   > Kullanıcı dönüştürme ve tam Parola karması eşitlemesinin, Federasyondan bulut kimlik doğrulamasına dönüştürmek için gerekli adımları belirten uyarıları güvenle yoksayabilirsiniz. Bu adımların artık gerekli olmadığına unutmayın. Bu uyarıları hala görüyorsanız, en son Azure AD Connect sürümünü çalıştırdığınızdan ve bu kılavuzun en son sürümünü kullandığınızdan emin olun. Daha fazla bilgi için [güncelleştirme Azure AD Connect](#update-azure-ad-connect)bölümüne bakın.

5. **Çoklu oturum açmayı etkinleştir** sayfasında, etki alanı yöneticisi hesabının kimlik bilgilerini girin ve ardından **İleri**' yi seçin.

   ![Çoklu oturum açmayı etkinleştir sayfasının ekran görüntüsü](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > Sorunsuz SSO 'yu etkinleştirmek için etki alanı yönetici hesabı kimlik bilgileri gereklidir. İşlem bu yükseltilmiş izinleri gerektiren aşağıdaki eylemleri tamamlar. Etki alanı yönetici hesabı kimlik bilgileri Azure AD Connect veya Azure AD 'de depolanmaz. Etki alanı yöneticisi hesabı kimlik bilgileri yalnızca özelliği açmak için kullanılır. İşlem başarıyla tamamlandığında kimlik bilgileri atılır.
   >
   > 1. Şirket içi Active Directory örneğiniz için AZUREADSSOACC adlı bir bilgisayar hesabı (Azure AD 'yi temsil eder) oluşturulur.
   > 2. Bilgisayar hesabının Kerberos şifre çözme anahtarı Azure AD ile güvenli bir şekilde paylaşılır.
   > 3. Azure AD oturum açma sırasında kullanılan iki URL 'yi temsil eden iki Kerberos hizmet asıl adı (SPN) oluşturulur.

6. **Yapılandırmaya hazırlanma** sayfasında, **yapılandırma tamamlandığında eşitleme işlemini başlat** onay kutusunun işaretli olduğundan emin olun. Ardından **Yapılandır**' ı seçin.

      ![Yapılandırmaya hazırlanma sayfasının ekran görüntüsü](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > Bu noktada, tüm Federasyon etki alanlarınız yönetilen kimlik doğrulamasına göre değişir. Parola karması eşitleme, yeni kimlik doğrulama yöntemidir.

7. Azure AD portalında **Azure Active Directory** > **Azure AD Connect**' yı seçin.
8. Bu ayarları doğrulayın:
   * **Federasyon** **devre dışı**olarak ayarlandı.
   * **Kesintisiz çoklu oturum açma** **özelliği etkin**olarak ayarlanmıştır.
   * **Parola eşitleme** **etkin**olarak ayarlandı.<br /> 

   ![Kullanıcı oturum açma bölümündeki ayarları gösteren ekran görüntüsü](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

[Teste ve sonraki adımlara](#testing-and-next-steps)atlayın.

   > [!IMPORTANT]
   > Bölüm **seçeneği B: Azure AD Connect ve PowerShell kullanarak Federasyondan Parola karması eşitlemesine geçiş yapın**. Bu bölümdeki adımlar, oturum açma yöntemini Parola karması eşitlemesine değiştirmek ve sorunsuz SSO 'yu etkinleştirmek için seçeneğini belirlediyseniz uygulanmaz.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Seçenek B: Azure AD Connect ve PowerShell kullanarak Federasyondan Parola karması eşitlemesine geçiş yapma

İlk olarak Azure AD Connect kullanarak Federal etki alanlarınızı yapılandırmadıysanız bu seçeneği kullanın. Bu işlem sırasında, sorunsuz SSO 'yu etkinleştirir ve etki alanlarınızı federe 'dan yönetilene geçirebilirsiniz.

1. Azure AD Connect sunucusunda, Azure AD Connect sihirbazını açın.
2. **Kullanıcı oturumunu Değiştir**' i seçin ve ardından **İleri**' yi seçin.
3. **Azure AD 'ye Bağlan** sayfasında, bir genel yönetici hesabının kullanıcı adını ve parolasını girin.
4. **Kullanıcı oturum açma** sayfasında **Parola karması eşitleme** düğmesini seçin. **Çoklu oturum açmayı etkinleştir**' i seçin ve ardından **İleri**' yi seçin.

   Parola karma eşitlemesini etkinleştirmeden önce: Kullanıcı oturum açma sayfasında yapılandırmayın seçeneğini gösteren ekran görüntüsü ![](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   Parola karma eşitlemesini etkinleştirdikten sonra: Kullanıcı oturum açma sayfasında yeni seçenekleri gösteren ekran görüntüsü ![](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Azure AD Connect Version 1.1.880.0 ile başlayarak, **sorunsuz çoklu oturum açma** onay kutusu varsayılan olarak seçilidir.

5. **Çoklu oturum açmayı etkinleştir** sayfasında, bir etki alanı yönetici hesabının kimlik bilgilerini girin ve ardından **İleri**' yi seçin.

   > [!NOTE]
   > Sorunsuz SSO 'yu etkinleştirmek için etki alanı yönetici hesabı kimlik bilgileri gereklidir. İşlem bu yükseltilmiş izinleri gerektiren aşağıdaki eylemleri tamamlar. Etki alanı yönetici hesabı kimlik bilgileri Azure AD Connect veya Azure AD 'de depolanmaz. Etki alanı yöneticisi hesabı kimlik bilgileri yalnızca özelliği açmak için kullanılır. İşlem başarıyla tamamlandığında kimlik bilgileri atılır.
   >
   > 1. Şirket içi Active Directory örneğiniz için AZUREADSSOACC adlı bir bilgisayar hesabı (Azure AD 'yi temsil eder) oluşturulur.
   > 2. Bilgisayar hesabının Kerberos şifre çözme anahtarı Azure AD ile güvenli bir şekilde paylaşılır.
   > 3. Azure AD oturum açma sırasında kullanılan iki URL 'yi temsil eden iki Kerberos hizmet asıl adı (SPN) oluşturulur.

6. **Yapılandırmaya hazırlanma** sayfasında, **yapılandırma tamamlandığında eşitleme işlemini başlat** onay kutusunun işaretli olduğundan emin olun. Ardından **Yapılandır**' ı seçin.

   yapılandırmaya hazırlanma sayfasında Yapılandır düğmesini gösteren ekran görüntüsü ![](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   **Yapılandır** düğmesini seçtiğinizde, önceki adımda gösterildiği gıbı sorunsuz SSO yapılandırılır. Parola karması eşitleme yapılandırması daha önce etkinleştirildiğinden değiştirilmedi.

   > [!IMPORTANT]
   > Şu anda kullanıcıların oturum açması için herhangi bir değişiklik yapılmaz.

7. Azure AD portalında şu ayarları doğrulayın:
   * **Federasyon** , **etkin**olarak ayarlandı.
   * **Kesintisiz çoklu oturum açma** **özelliği etkin**olarak ayarlanmıştır.
   * **Parola eşitleme** **etkin**olarak ayarlandı.

   ![Kullanıcı oturum açma bölümündeki ayarları gösteren ekran görüntüsü](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Etki alanlarını federe 'dan yönetilene dönüştürme

Bu noktada, Federasyon hala etkin ve etki alanlarınız için çalışır durumda. Dağıtıma devam etmek için, her etki alanının, Parola karması eşitlemesi aracılığıyla Kullanıcı kimlik doğrulamasını zorlamak üzere federe 'dan yönetilene dönüştürülmesi gerekir.

> [!IMPORTANT]
> Tüm etki alanlarını aynı anda dönüştürmeniz gerekmez. Üretim kiracınızda bir test etki alanı ile başlatmayı veya en düşük Kullanıcı sayısı olan etki alanı ile başlatmayı seçebilirsiniz.

Azure AD PowerShell modülünü kullanarak dönüştürmeyi doldurun:

1. PowerShell 'de, genel yönetici hesabı kullanarak Azure AD 'de oturum açın.
2. İlk etki alanını dönüştürmek için aşağıdaki komutu çalıştırın:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. Azure AD portalında **Azure Active Directory** > **Azure AD Connect**' yı seçin.
4. Aşağıdaki komutu çalıştırarak etki alanının yönetilmek üzere dönüştürülmüş olduğunu doğrulayın:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Test ve sonraki adımlar

Parola karması eşitlemesini doğrulamak ve dönüştürme işlemini bitirebilmesi için aşağıdaki görevleri doldurun.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Parola karması eşitlemesini kullanarak kimlik doğrulamasını test etme 

Kiracınızda Federal Kimlik kullanılıyorsa, kullanıcılar Azure AD oturum açma sayfasından AD FS ortamınıza yeniden yönlendirilir. Artık kiracı, Federasyon kimlik doğrulaması yerine parola karması eşitlemesini kullanacak şekilde yapılandırıldığına göre, kullanıcılar AD FS 'a yönlendirilmez. Bunun yerine, kullanıcılar doğrudan Azure AD oturum açma sayfasında oturum açabilirler.

Parola karması eşitlemesini sınamak için:

1. Sorunsuz SSO 'nun oturumunuzu otomatik olarak açmasını sağlamak için Internet Explorer 'ı InPrivate modda açın.
2. Office 365 oturum açma sayfasına gidin ([https://portal.office.com](https://portal.office.com/)).
3. Bir Kullanıcı UPN 'si girin ve ardından **İleri**' yi seçin. Şirket içi Active Directory örneğinden eşitlenmiş bir karma kullanıcının UPN 'sini girdiğinizden ve daha önce federal kimlik doğrulamasını kullanmış olduğunuzdan emin olun. Kullanıcı adını ve parolayı girdiğiniz bir sayfa görüntülenir:

   ![Kullanıcı adı gireceğiniz oturum açma sayfasını gösteren ekran görüntüsü](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Parola girebileceğiniz oturum açma sayfasını gösteren ekran görüntüsü](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Parolayı girdikten ve **oturum aç**' ı seçtikten sonra Office 365 portalına yönlendirilirsiniz.

   ![Office 365 portalını gösteren ekran görüntüsü](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Sorunsuz SSO 'yu test etme

1. Şirket ağına bağlı olan, etki alanına katılmış bir makinede oturum açın.
2. Internet Explorer veya Chrome 'da aşağıdaki URL 'Lerden birine gidin ("contoso" yerine etki alanınızı değiştirin):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   Kullanıcı, "oturumunuzu açmaya çalışırken" iletisini gösteren Azure AD oturum açma sayfasına kısaca yönlendirilir. Kullanıcıdan Kullanıcı adı veya parola istenmez.<br />

   ![Azure AD oturum açma sayfasını ve iletisini gösteren ekran görüntüsü](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. Kullanıcı yeniden yönlendirildi ve erişim panelinde başarıyla oturum açıldı:

   > [!NOTE]
   > Sorunsuz SSO, etki alanı ipucunu (örneğin, myapps.microsoft.com/contoso.com) destekleyen Office 365 hizmetlerinde çalışmaktadır. Şu anda Office 365 Portalı (portal.office.com), etki alanı ipuçlarını desteklemez. Kullanıcıların bir UPN girmesi gerekir. Bir UPN girildikten sonra sorunsuz SSO Kullanıcı adına Kerberos bileti alır. Kullanıcı parola girmeden oturum açtı.

   > [!TIP]
   > Gelişmiş bir SSO deneyimi için [Windows 10 ' da Azure AD hibrit katılımı](https://docs.microsoft.com/azure/active-directory/device-management-introduction) dağıtımı yapmayı düşünün.

### <a name="remove-the-relying-party-trust"></a>Bağlı olan taraf güvenini kaldırma

Tüm kullanıcıların ve istemcilerin Azure AD aracılığıyla başarıyla kimlik doğruladığını doğruladıktan sonra, Office 365 bağlı olan taraf güvenini kaldırmak güvenlidir.

Başka amaçlar için AD FS kullanmıyorsanız (diğer bir deyişle, diğer bağlı olan taraf güvenleri için), bu noktada AD FS yetkisini almak güvenlidir.

### <a name="rollback"></a>Etkin

Büyük bir sorun keşfettiğiniz ve bunu hızlı çözümleyemezse, çözümü federasyona geri almayı seçebilirsiniz.

Dağıtım ayrıntılarınız için Federasyon tasarımı ve dağıtım belgelerine bakın. İşlem şu görevleri içermelidir:

* **Convert-Msoldomaintofedere** cmdlet 'ini kullanarak yönetilen etki alanlarını federal kimlik doğrulamasına dönüştürün.
* Gerekirse, ek talep kurallarını yapılandırın.

### <a name="sync-userprincipalname-updates"></a>UserPrincipalName güncelleştirmelerini Eşitle

Geçmişte, bu koşulların her ikisi de doğru olmadığı müddetçe, şirket içi ortamdan eşitleme hizmetini kullanan **userPrincipalName** özniteliğinde yapılan güncelleştirmeler engellenir:

* Kullanıcı yönetilen (Federe olmayan) bir kimlik etki alanıdır.
* Kullanıcıya bir lisans atanmadı.

Bu özelliği doğrulama veya açma hakkında bilgi edinmek için bkz: [userPrincipalName güncelleştirmelerini eşitleme](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

### <a name="troubleshooting"></a>Sorun giderme

Destek ekibiniz, sırasında veya Federasyondan yönetilene yapılan değişiklikten sonra ortaya çıkan tüm kimlik doğrulama sorunlarını nasıl giderebileceğinizi anlamalıdır. Destek ekibinizin, sorunu yalıtmak ve çözmek için ortak sorun giderme adımlarını ve uygun eylemleri öğrenmesini sağlamak için aşağıdaki sorun giderme belgelerini kullanın.

[Azure Active Directory parola karması eşitleme sorunlarını giderme](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Azure Active Directory sorunsuz çoklu oturum açma sorunlarını giderme](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Kusursuz SSO Kerberos şifre çözme anahtarını atla

Sıklıkla AZUREADSSOACC bilgisayar hesabının (Azure AD 'yi temsil eden) Kerberos şifre çözme anahtarını ele almanız önemlidir. AZUREADSSOACC Computer hesabı şirket içi Active Directory ormanınızda oluşturulur. Active Directory etki alanı üyelerinin parola değişikliklerini göndermesi için en az 30 günde bir Kerberos şifre çözme anahtarını almanızı kesinlikle öneririz. AZUREADSSOACC bilgisayar hesabı nesnesine bağlı ilişkili cihaz yok, bu nedenle geçişi el ile gerçekleştirmeniz gerekir.

Azure AD Connect çalıştıran şirket içi sunucuda sorunsuz SSO Kerberos şifre çözme anahtarının başa aktarımını başlatın.

Daha fazla bilgi için bkz. [AZUREADSSOACC Computer hesabının Kerberos şifre çözme anahtarı üzerinden nasıl yaparım? alma](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD Connect tasarım kavramları](plan-connect-design-concepts.md)hakkında bilgi edinin.
* [Doğru kimlik doğrulamasını](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)seçin.
* [Desteklenen topolojiler](plan-connect-design-concepts.md)hakkında bilgi edinin.
