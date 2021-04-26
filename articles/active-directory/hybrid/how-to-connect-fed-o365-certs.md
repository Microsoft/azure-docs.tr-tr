---
title: Microsoft 365 ve Azure AD kullanıcıları için sertifika yenileme | Microsoft Docs
description: Bu makalede, kullanıcılara bir sertifikayı yenilemeyi bildiren e-postalarla ilgili sorunları nasıl çözebileceğiniz Microsoft 365 açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/20/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e81cb9018d817fb206915a81fdc3bdd60f6b08c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611897"
---
# <a name="renew-federation-certificates-for-microsoft-365-and-azure-active-directory"></a>Microsoft 365 ve Azure Active Directory için Federasyon sertifikalarını yenileme
## <a name="overview"></a>Genel Bakış
Azure Active Directory (Azure AD) ve Active Directory Federasyon Hizmetleri (AD FS) (AD FS) arasında başarılı bir Federasyon için, Azure AD 'de güvenlik belirteçlerini imzalamak üzere AD FS tarafından kullanılan sertifikaların Azure AD 'de yapılandırıldıklarıyla aynı olması gerekir. Herhangi bir uyuşmazlık, kopuk güvene yol açabilir. Azure AD, AD FS ve Web uygulaması ara sunucusu (extranet erişimi için) dağıtırken bu bilgilerin eşitlenmiş durumda kalmasını sağlar.

> [!NOTE]
> Bu makalede, Federasyon sertifikalarınızın yönetimi hakkında bilgi sağlanır.  Acil durum dönüşüyle ilgili bilgi için bkz [. AD FS sertifikalarının acil bir dönüşü](how-to-connect-emergency-ad-fs-certificate-rotation.md)

Bu makalede, aşağıdaki durumlarda belirteç imzalama sertifikalarınızı yönetmek ve Azure AD ile eşitlenmiş halde tutmak için ek bilgiler sağlanmaktadır:

* Web uygulaması ara sunucusunu dağıtmıyor ve bu nedenle Federasyon meta verileri extranet 'te kullanılamıyor.
* Belirteç imzalama sertifikaları için AD FS varsayılan yapılandırmasını kullandeğilsiniz.
* Bir üçüncü taraf kimlik sağlayıcısı kullanıyorsunuz.

> [!IMPORTANT]
> Microsoft, sertifikaları korumak ve güvenli hale getirmek için bir donanım güvenlik modülü (HSM) kullanmanızı çok öneriyor.
> Daha fazla bilgi için, AD FS güvenli hale getirmek için en iyi yöntemler altında [donanım güvenlik modülüne](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm) bakın

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Belirteç imzalama sertifikaları için AD FS varsayılan yapılandırması
Belirteç imzalama ve belirteç şifre çözme sertifikaları genellikle kendinden imzalı sertifikalardır ve bir yıl boyunca iyidir. Varsayılan olarak, AD FS **AutoCertificateRollover** adlı otomatik yenileme işlemini içerir. AD FS 2,0 veya sonraki bir sürümü kullanıyorsanız, Microsoft 365 ve Azure AD süresi dolmadan önce sertifikanızı otomatik olarak güncelleştirir.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Microsoft 365 Yönetim merkezinden veya bir e-postadaki yenileme bildirimi
> [!NOTE]
> Office sertifikanızı yenilemenizi isteyen bir e-posta veya Portal bildirimi aldıysanız, herhangi bir işlem yapmanız gerektiğini denetlemek için bkz. [belirteç imzalama sertifikalarındaki değişiklikleri yönetme](#managecerts) . Microsoft, herhangi bir işlem gerekmediği zaman bile, gönderilen sertifika yenileme bildirimlerine yol açabilecek olası bir sorunu biliyor.
>
>

Azure AD, Federasyon meta verilerini izlemeye çalışır ve bu meta verilerle gösterildiği gibi belirteç imzalama sertifikalarını güncelleştirebilir. belirteç imzalama sertifikalarının süresi dolduktan 30 gün önce, Azure AD, Federasyon meta verilerini yoklayarak yeni sertifikaların kullanılabilir olup olmadığını denetler.

* Federasyon meta verilerini başarılı bir şekilde yoklayabiliyorsanız ve yeni sertifikaları alamıyorsa, Microsoft 365 Yönetim Merkezi 'nde bir e-posta bildirimi veya uyarısı kullanıcıya verilmez.
* Federasyon meta verilerine erişilemediği veya otomatik sertifika geçişi etkinleştirilmediğinden, Azure AD, Microsoft 365 Yönetim merkezinde bir e-posta bildirimi ve uyarı verir.

![Office 365 Portalı bildirimi](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> AD FS kullanıyorsanız, iş sürekliliği sağlamak için, bilinen sorunlara yönelik kimlik doğrulama hatalarının gerçekleşmemesi için lütfen sunucularınızın aşağıdaki güncelleştirmelere sahip olduğunu doğrulayın. Bu, bu yenileme ve gelecekteki yenileme dönemlerinde bilinen AD FS proxy sunucusu sorunlarını azaltır:
>
> Server 2012 R2- [Windows Server 2014 Mayıs](https://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 ve 2012- [Windows Server 2012 veya windows 2008 R2 SP1 'de proxy aracılığıyla kimlik doğrulaması başarısız oluyor](https://support.microsoft.com/kb/3094446)
>
>

## <a name="check-if-the-certificates-need-to-be-updated"></a>Sertifikaların güncelleştirilip güncelleştirilmediğini denetle <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>1. Adım: AutoCertificateRollover durumunu denetleyin
AD FS sunucunuzda PowerShell ' i açın. AutoCertificateRollover değerinin true olarak ayarlandığından emin olun.

```azurepowershell-interactive
Get-Adfsproperties
```

![AutoCertificateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>AD FS 2,0 kullanıyorsanız, önce Microsoft. ADFS. PowerShell Add-Pssnapin çalıştırın.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>2. Adım: AD FS ve Azure AD 'nin eşitlenmiş olduğunu onaylayın
AD FS sunucunuzda MSOnline PowerShell komut istemi ' ni açın ve Azure AD 'ye bağlanın.

> [!NOTE]
> MSOL-Cmdlets, MSOnline PowerShell modülünün bir parçasıdır.
> MSOnline PowerShell modülünü doğrudan PowerShell Galerisi indirebilirsiniz.
> 
>

```azurepowershell-interactive
Install-Module MSOnline
```

MSOnline PowerShell-modülünü kullanarak Azure AD 'ye bağlanın.

```azurepowershell-interactive
Import-Module MSOnline
Connect-MsolService
```

AD FS ' de yapılandırılan sertifikaları ve belirtilen etki alanı için Azure AD Güven Özellikleri ' ni denetleyin.

```azurepowershell-interactive
Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate
```

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Parmak izleri her iki çıktıda eşleşiyorsa, sertifikalarınız Azure AD ile eşitlenir.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>3. Adım: sertifikanızın sona ermek üzere olup olmadığını denetleyin
Get-MsolFederationProperty veya Get-Adfscercertificate 'ın çıktısında, "sonra değil" altında tarihi kontrol edin. Tarih 30 günden azsa, işlem yapmanız gerekir.

| AutoCertificateRollover | Azure AD ile eşitlenmiş sertifikalar | Federasyon meta verileri herkese açık bir şekilde erişilebilir | Olacağı | Eylem |
|:---:|:---:|:---:|:---:|:---:|
| Yes |Yes |Yes |- |Eyleme gerek yok. Bkz. [belirteç imzalama sertifikasını otomatik olarak yenileme](#autorenew). |
| Yes |Hayır |- |15 günden az |Hemen yenileyin. Bkz. [belirteç imzalama sertifikasını el Ile yenileme](#manualrenew). |
| No |- |- |30 günden az |Hemen yenileyin. Bkz. [belirteç imzalama sertifikasını el Ile yenileme](#manualrenew). |

\[-] Önemi yoktur

## <a name="renew-the-token-signing-certificate-automatically-recommended"></a>Belirteç imzalama sertifikasını otomatik olarak Yenile (önerilen) <a name="autorenew"></a>
Aşağıdakilerin her ikisi de doğruysa, el ile herhangi bir adım gerçekleştirmeniz gerekmez:

* Web uygulaması proxy 'Si dağıttıysanız, extranet 'ten Federasyon meta verilerine erişim sağlayabilirsiniz.
* AD FS varsayılan yapılandırmayı kullanıyorsunuz (AutoCertificateRollover etkin).

Sertifikanın otomatik olarak güncelleştirilebileceğini onaylamak için aşağıdakileri denetleyin.

**1. AD FS özelliği AutoCertificateRollover true olarak ayarlanmalıdır.** Bu, AD FS, eski kullanım süreleri dolmadan önce otomatik olarak yeni belirteç imzalama ve belirteç şifre çözme sertifikaları üretecek anlamına gelir.

**2. AD FS Federasyon meta verileri herkese açık bir şekilde erişilebilir.** Ortak internet 'teki bir bilgisayardan aşağıdaki URL 'ye giderek, Federasyon meta verilerinizden genel olarak erişilebilir olup olmadığını denetleyin (şirket ağının dışında):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

burada, `(your_FS_name)` kuruluşunuzun kullandığı Federasyon Hizmeti ana bilgisayar adı (örneğin, FS.contoso.com) ile değiştirilmiştir.  Bu ayarların her ikisini de başarıyla doğrulayabiliyorsanız, başka bir şey yapmanız gerekmez.  

Örnek: `https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml`
## <a name="renew-the-token-signing-certificate-manually"></a>Belirteç imzalama sertifikasını el ile yenileme <a name="manualrenew"></a>
Belirteç imzalama sertifikalarını el ile yenilemeyi seçebilirsiniz. Örneğin, aşağıdaki senaryolar el ile yenileme için daha iyi çalışabilir:

* Belirteç imzalama sertifikaları otomatik olarak imzalanan sertifikalar değildir. Bunun en yaygın nedeni, kuruluşunuzun bir kuruluş sertifika yetkilisinden kaydedilen AD FS sertifikalarını yönettiğine yöneliktir.
* Ağ güvenliği, Federasyon meta verilerinin herkese açık bir şekilde izin vermez.

Bu senaryolarda, belirteç imzalama sertifikalarını her güncelleştirdiğinizde, Update-MsolFederatedDomain PowerShell komutunu kullanarak da Microsoft 365 etki alanınızı güncelleştirmeniz gerekir.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>1. Adım: AD FS yeni belirteç imzalama sertifikaları içerdiğinden emin olun
**Varsayılan olmayan yapılandırma**

AD FS varsayılan olmayan bir yapılandırma kullanıyorsanız ( **AutoCertificateRollover** **false** olarak ayarlandığında), büyük olasılıkla özel sertifikalar kullanıyorsunuz (otomatik olarak imzalanmamış). AD FS belirteç imzalama sertifikalarının nasıl yenileneceği hakkında daha fazla bilgi için bkz. [Federasyon sunucuları Için sertifika gereksinimleri](/windows-server/identity/ad-fs/design/certificate-requirements-for-federation-servers).

**Federasyon meta verileri herkese açık değil**

Diğer taraftan, **AutoCertificateRollover** **true** olarak ayarlanmışsa, ancak Federasyon meta verileriniz herkese açık olarak erişilemezse, önce yeni belirteç imzalama sertifikalarının AD FS tarafından oluşturulduğundan emin olun. Aşağıdaki adımları uygulayarak yeni belirteç imzalama sertifikalarınızın olduğunu doğrulayın:

1. Birincil AD FS sunucuda oturum açtığınızdan emin olun.
2. Bir PowerShell komut penceresi açarak ve aşağıdaki komutu çalıştırarak AD FS içindeki geçerli imzalama sertifikalarını denetleyin:

    PS C: \> Get-Adfscercertificate, – CertificateType token-signing

   > [!NOTE]
   > AD FS 2,0 kullanıyorsanız, önce Microsoft. ADFS. PowerShell Add-Pssnapin çalıştırmalısınız.
   >
   >
3. Listelenen tüm sertifikalarda komut çıktısına bakın. AD FS yeni bir sertifika oluşturmışsa, çıktıda iki sertifika görmeniz gerekir: **isprımary** değeri **true** **ve** NotAfter tarihi 5 gün **içinde ve**  **tarihi, gelecekte** bir yıl hakkında.
4. Yalnızca bir sertifika görürseniz ve **NotAfter** tarihi 5 gün içindeyse, yeni bir sertifika oluşturmanız gerekir.
5. Yeni bir sertifika oluşturmak için PowerShell komut isteminde aşağıdaki komutu yürütün: `PS C:\Update-ADFSCertificate –CertificateType token-signing` .
6. Aşağıdaki komutu yeniden çalıştırarak güncelleştirmeyi doğrulayın: PS C: \> Get-Adfscercertificate ate – CertificateType token-signing

Şu anda iki sertifika listelenmelidir, bunlardan biri gelecekte yaklaşık bir yıl **NotAfter** tarih ve **ısprımary** değeri **false** şeklindedir.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-microsoft-365-trust"></a>2. Adım: Microsoft 365 güvenin yeni belirteç imzalama sertifikalarını güncelleştirme
Microsoft 365, güven için kullanılacak yeni belirteç imzalama sertifikaları ile aşağıdaki gibi güncelleştirin.

1. Windows PowerShell için Microsoft Azure Active Directory Modülü açın.
2. $Cred = Get-Credential ' i çalıştırın. Bu cmdlet kimlik bilgileri girmenizi isterse, bulut hizmeti Yönetici hesabınızın kimlik bilgilerini yazın.
3. Connect-MsolService: Credential $cred çalıştırın. Bu cmdlet sizi bulut hizmetine bağlar. Araç tarafından yüklenen ek cmdlet 'lerden herhangi birini çalıştırmadan önce, sizi bulut hizmetine bağlayan bir bağlam oluşturmak gerekir.
4. Bu komutları AD FS birincil federasyon sunucusu olmayan bir bilgisayarda çalıştırıyorsanız, &lt; &gt; &lt; AD FS birincil sunucu birincil &gt; AD FS sunucusunun iç FQDN 'Si olan Set-MSOLAdfscontext-Computer AD FS birincil sunucusunu çalıştırın. Bu cmdlet, sizi AD FS bağlayan bir bağlam oluşturur.
5. Update-MSOLFederatedDomain – DomainName &lt; etki alanı çalıştırın &gt; . Bu cmdlet, AD FS ayarları bulut hizmetine güncelleştirir ve ikisi arasındaki güven ilişkisini yapılandırır.

> [!NOTE]
> Contoso.com ve fabrikam.com gibi birden çok üst düzey etki alanını desteklemeniz gerekiyorsa, bir cmdlet ile **supportmultipledomain** anahtarını kullanmanız gerekir. Daha fazla bilgi için bkz. [birden çok üst düzey etki alanı Için destek](how-to-connect-install-multiple-domains.md).
>


## <a name="repair-azure-ad-trust-by-using-azure-ad-connect"></a>Azure AD Connect kullanarak Azure AD güvenini onarın <a name="connectrenew"></a>
AD FS grubunuzu ve Azure AD güvenini Azure AD Connect kullanarak yapılandırdıysanız, belirteç imzalama sertifikalarınız için herhangi bir işlem yapmanız gerektiğini algılamak üzere Azure AD Connect kullanabilirsiniz. Sertifikaları yenilemeniz gerekiyorsa, bunu yapmak için Azure AD Connect kullanabilirsiniz.

Daha fazla bilgi için bkz. [güveni onarma](how-to-connect-fed-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>AD FS ve Azure AD sertifikası güncelleştirme adımları
Belirteç imzalama sertifikaları, Federasyon sunucusunun verdiği tüm belirteçleri güvenli bir şekilde imzalamak için kullanılan standart x509 sertifikalarıdır. Belirteç şifre çözme sertifikaları, gelen belirteçlerin şifresini çözmek için kullanılan standart x509 sertifikalardır. 

Varsayılan olarak, AD FS otomatik olarak belirteç imzalama ve belirteç şifre çözme sertifikaları oluşturmak üzere yapılandırılır ve bu, hem ilk yapılandırma zamanında hem de sertifikaların sona erme tarihine yaklaştığı zaman.

Azure AD, Federasyon hizmeti meta verilerinizden geçerli sertifikanın süresi dolmadan 30 gün önce yeni bir sertifika almaya çalışır. Bu sırada yeni bir sertifika kullanılamadığı durumlarda Azure AD, verileri düzenli aralıklarla izlemeye devam edecektir. Yeni sertifika meta verilerde kullanılabilir duruma geldiğinde, etki alanının Federasyon ayarları yeni sertifika bilgileriyle güncelleştirilir. `Get-MsolDomainFederationSettings`Yeni sertifikayı NextSigningCertificate/SigningCertificate içinde görmediğinizi doğrulamak için ' i kullanabilirsiniz.

AD FS 'de belirteç Imzalama sertifikaları hakkında daha fazla bilgi için bkz. [AD FS Için belirteç imzalama ve belirteç şifre çözme sertifikaları alma ve yapılandırma](/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
