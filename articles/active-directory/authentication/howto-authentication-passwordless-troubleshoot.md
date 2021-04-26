---
title: Karma FIDO2 güvenlik anahtarlarına yönelik bilinen sorunlar ve sorun giderme-Azure Active Directory
description: Azure Active Directory kullanarak, passwordless karma FIDO2 güvenlik anahtarı oturum açma sorunlarını gidermeye yönelik bazı bilinen sorunlar ve yolları hakkında bilgi edinin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 435b27255ce58a3541d6b0d3a76bdf4080aa3962
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648790"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad"></a>Azure AD 'de FIDO2 güvenlik anahtarlarının karma dağıtımları için sorun giderme 

Bu makalede, karma Azure AD 'ye katılmış cihazlar için sık sorulan sorular ve şirket içi kaynaklarda daha az oturum açma izni yer almaktadır. Bu passwordless özelliği sayesinde, FIDO2 güvenlik anahtarlarını kullanarak karma Azure AD 'ye katılmış cihazlar için Windows 10 cihazlarında Azure AD kimlik doğrulamasını etkinleştirebilirsiniz. Kullanıcılar, FIDO2 anahtarları gibi modern kimlik bilgileriyle Windows 'da oturum açabilir ve şirket içi kaynaklara sorunsuz bir çoklu oturum açma (SSO) deneyimi ile geleneksel Active Directory Domain Services (AD DS) tabanlı kaynaklara erişebilir.

Karma ortamdaki kullanıcılar için aşağıdaki senaryolar desteklenir:

* FIDO2 güvenlik anahtarlarını kullanarak hibrit Azure AD 'ye katılmış cihazlarda oturum açın ve şirket içi kaynaklara SSO erişimi alın.
* FIDO2 güvenlik anahtarlarını kullanarak Azure AD 'ye katılmış cihazlarda oturum açın ve şirket içi kaynaklara SSO erişimi alın.

FIDO2 güvenlik anahtarlarını ve şirket içi kaynaklara karma erişimi kullanmaya başlamak için aşağıdaki makalelere bakın:

* [Parolasız güvenlik anahtarları](howto-authentication-passwordless-security-key.md)
* [Parolasız Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Parolasız şirket içi](howto-authentication-passwordless-security-key-on-premises.md)

## <a name="known-issues"></a>Bilinen sorunlar

* [Windows Hello yüz çok hızlı olduğundan ve varsayılan oturum açma mekanizmasından dolayı kullanıcılar FIDO2 güvenlik anahtarlarını kullanarak oturum açamıyor](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [Kullanıcılar, karma bir Azure AD 'ye katılmış makine oluşturduktan hemen sonra FIDO2 güvenlik anahtarlarını kullanamaz](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [Kullanıcılar, bir FIDO2 güvenlik anahtarıyla oturum açtıktan ve kimlik bilgisi istemi aldıktan sonra NTLM ağı kaynağına SSO alamıyor](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>Windows Hello yüz çok hızlı olduğundan ve varsayılan oturum açma mekanizmasından dolayı kullanıcılar FIDO2 güvenlik anahtarlarını kullanarak oturum açamıyor

Windows Hello yüz, bir kullanıcının kaydolduğu bir cihaz için tasarlanan en iyi deneyimdir. FIDO2 güvenlik anahtarları, Paylaşılan cihazlarda kullanılmak üzere veya Iş için Windows Hello kaydı bir engel olarak hazırlanmıştır.

Windows Hello yüz, kullanıcıların FIDO2 güvenlik anahtarı oturum açma senaryosunu kaldırmasını engelliyorsa, **ayarlar > Sign-In seçenekler**' de yüz kaydını kaldırarak, kullanıcılar Merhaba oturumu kapatabilir.

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>Kullanıcılar, karma bir Azure AD 'ye katılmış makine oluşturduktan hemen sonra FIDO2 güvenlik anahtarlarını kullanamaz

Karma bir Azure AD 'ye katılmış makinenin temiz bir yüklemesi sırasında etki alanına katılma ve yeniden başlatma işleminden sonra, bir parola ile oturum açmanız ve oturum açmak için bir FIDO2 güvenlik anahtarı kullanmak üzere kullanabilmeniz için ilkenin eşitlenmesini beklemeniz gerekir.

Bu davranış, etki alanına katılmış cihazlar için bilinen bir kısıtlamadır ve FIDO2 güvenlik anahtarlarına özgü değildir.

Geçerli durumu denetlemek için `dsregcmd /status` komutunu kullanın. Hem *Azureadkatılmış* hem de *Domainkatılmış* 'in *Yes* olduğunu denetleyin.

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>Kullanıcılar, bir FIDO2 güvenlik anahtarıyla oturum açtıktan ve kimlik bilgisi istemi aldıktan sonra NTLM ağı kaynağına SSO alamıyor

Kaynak isteğinize hizmet vermek için yeterli sayıda DC 'nin düzeltme eki uygulanmış olduğundan emin olun. Özelliği çalıştıran bir sunucuyu görüp göregöremiyorsanız emin olmak için şu çıktıyı gözden geçirin: `nltest /dsgetdc:<dc name> /keylist /kdc`

Bu özelliğe sahip bir DC görürseniz, kullanıcının parolası oturum açtıktan sonra değişmiş olabilir ya da başka bir sorun var. Microsoft destek ekibinin hata ayıklamasıyla ilgili aşağıdaki bölümde günlükleri ayrıntılı olarak toplayın.

## <a name="troubleshoot"></a>Sorun giderme

Sorun gidermeye yönelik iki alan vardır- [pencere istemci sorunları](#windows-client-issues)veya [dağıtım sorunları](#deployment-issues).

### <a name="windows-client-issues"></a>Windows Istemci sorunları

Windows 'da oturum açma veya Windows 10 cihazlarındaki şirket içi kaynaklara erişme sorunlarını gidermeye yardımcı olan verileri toplamak için aşağıdaki adımları izleyin:

1. **Geri Bildirim Hub 'ı** uygulamasını açın. Adınızın uygulamanın sol alt kısmında olduğundan emin olun, sonra **Yeni bir geri bildirim öğesi oluştur**' u seçin.

    Geri bildirim öğesi türü için *sorun*' ı seçin.

1. *Güvenlik ve gizlilik* kategorisini, sonra *FIDO* alt kategorisini seçin.
1. *Ekli dosyaları ve tanılamayı Microsoft 'a geri bildirimlerimin yanında gönder* onay kutusunu değiştirin.
1. *Sorunlarımı yeniden oluştur*' u seçin ve ardından *yakalamayı başlatın*.
1. FIDO2 güvenlik anahtarıyla makineyi kilitler ve kilidini açın. Sorun oluşursa, diğer kimlik bilgileriyle kilit açmayı deneyin.
1. **Geri Bildirim Hub 'ına** dönün, **Yakalamayı Durdur**' u seçin ve geri bildiriminizi gönderin.
1. *Geri bildirim* sayfasına gidin ve *geri bildirim* sekmesine gidin. Son gönderdiğiniz geri bildirimlerinizi seçin.
1. Geri bildirimin bağlantısını almak için sağ üst köşedeki *paylaşma* düğmesini seçin. Bir destek talebi açtığınızda bu bağlantıyı ekleyin veya var olan bir destek örneğine atanan mühendisi yanıtlayın.

Aşağıdaki olay günlükleri ve kayıt defteri anahtarı bilgileri toplanır:

**Kayıt defteri anahtarları**

* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FIDO [ \* ]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PasswordForWork\* [ \* ]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Policies\PasswordForWork\* [ \* ]*

**Tanılama bilgileri**

* Canlı çekirdek dökümü
* AppX paket bilgilerini topla
* UııF bağlam dosyaları

**Olay günlükleri**

* *%SystemRoot%\system32\winevt\logs\microsoft-Windows-AAD%40operational,evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx*
* *%SystemRoot%\system32\winevt\logs\microsoft-Windows-helloforbusiness%40operational,evtx*

### <a name="deployment-issues"></a>Dağıtım sorunları

Azure AD Kerberos sunucusunu dağıtmayla ilgili sorunları gidermek için Azure AD Connect eklenen yeni PowerShell modülünü kullanın.

#### <a name="viewing-the-logs"></a>Günlükleri görüntüleme

Azure AD Kerberos Sunucusu PowerShell cmdlet 'leri, standart Azure AD Connect sihirbazıyla aynı günlüğü kullanır. Cmdlet 'lerden bilgi veya hata ayrıntılarını görüntülemek için aşağıdaki adımları izleyin:

1. Azure AD Connect sunucusunda öğesine gidin `C:\ProgramData\AADConnect\` . Bu klasör varsayılan olarak gizlidir.
1. Açın ve dizininde bulunan en son `trace-*.log` dosyayı görüntüleyin.

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>Azure AD Kerberos sunucu nesnelerini görüntüleme

Azure AD Kerberos sunucu nesnelerini görüntülemek ve bunların uygun sırada olduğunu doğrulamak için aşağıdaki adımları izleyin:

1. Azure AD Connect sunucusunda PowerShell ' i açın ve şuraya gidin `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Azure AD Kerberos sunucusunu hem Azure AD hem de şirket içi AD DS görüntülemek için aşağıdaki PowerShell komutlarını çalıştırın.

    *Corp.contoso.com* değerini şirket içi AD DS etki alanı adıyla değiştirin.

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

Komut Azure AD Kerberos sunucusunun özelliklerini hem Azure AD hem de şirket içi AD DS alır. Her şeyin iyi sırada olduğunu doğrulamak için özellikleri gözden geçirin. Özelliklerini doğrulamak için aşağıdaki tabloyu kullanın.

İlk özellik kümesi, şirket içi AD DS ortamındaki nesnelerden oluşur. İkinci yarım (* Cloud * * ile başlayan Özellikler), Azure AD 'deki Kerberos Sunucusu nesnesidir:

| Özellik           | Açıklama  |
|--------------------|--------------|
| Id                 | AD DS etki alanı denetleyicisi nesnesinin benzersiz *kimliği* . |
| DomainDnsName      | AD DS etki alanının DNS etki alanı adı. |
| Bilgisayar hesabı    | Azure AD Kerberos Sunucusu nesnesinin (DC) bilgisayar hesabı nesnesi. |
| UserAccount        | Azure AD Kerberos Sunucusu TGT şifreleme anahtarını tutan devre dışı Kullanıcı hesabı nesnesi. Bu hesabın DN 'si *CN = krbtgt_AzureAD, CN = kullanıcılar, <Domain-DN>* |
| KeyVersion         | Azure AD Kerberos Sunucusu TGT şifreleme anahtarının anahtar sürümü. Anahtar oluşturulduğunda sürüm atanır. Anahtar her döndürüldüğünde sürüm artar. Artımlar, çoğaltma meta verilerine dayalıdır ve büyük olasılıkla bunlardan büyük olacaktır.<br /><br /> Örneğin, ilk *Keyversion* *192272* olabilir. Anahtar ilk kez döndürüldüğünde, sürüm *212621*' e kadar ilerleyebilirsiniz.<br /><br /> Doğrulamanız gereken önemli şey, şirket içi nesne için *keyversion* ve bulut nesnesine ait *cloudkeyversion* 'un aynı olmasıdır. |
| KeyUpdatedOn       | Azure AD Kerberos Sunucusu TGT şifreleme anahtarının güncelleştirildiği veya oluşturulduğu tarih ve saat. |
| KeyUpdatedFrom     | Azure AD Kerberos Sunucusu TGT şifreleme anahtarının en son güncelleştirildiği DC. |
| Cloudıd            | Azure AD nesnesinden *kimliği* . Yukarıdaki *kimlikle* eşleşmelidir. |
| CloudDomainDnsName | Azure AD nesnesinden *Domaindnsname* . Yukarıdaki *Domaindnsname* ile aynı olmalıdır. |
| CloudKeyVersion    | Azure AD nesnesinden *Keyversion* . Yukarıdaki *Keyversion* ile aynı olmalıdır. |
| CloudKeyUpdatedOn  | Azure AD nesnesinden *Keyupdatedon* . Yukarıdaki *Keyupdatedile* eşleşmelidir. |

## <a name="next-steps"></a>Sonraki adımlar

FIDO2 güvenlik anahtarlarını ve şirket içi kaynaklara karma erişimi kullanmaya başlamak için aşağıdaki makalelere bakın:

* [Parolasız FIDO2 güvenlik anahtarları](howto-authentication-passwordless-security-key.md)
* [Parolasız Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Parolasız şirket içi](howto-authentication-passwordless-security-key-on-premises.md)
