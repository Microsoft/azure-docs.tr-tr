---
title: Karma FIDO2 güvenlik anahtarı dağıtımı için SSS-Azure Active Directory
description: Azure Active Directory kullanarak parolasız karma FIDO2 güvenlik anahtarı oturum açma için sık sorulan bazı sorular hakkında bilgi edinin
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
ms.openlocfilehash: ca4943293f9474d4089267d05460d6d8766b79e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646393"
---
# <a name="deployment-frequently-asked-questions-faqs-for-hybrid-fido2-security-keys-in-azure-ad"></a>Azure AD 'de karma FIDO2 güvenlik anahtarları için dağıtım hakkında sık sorulan sorular (SSS) 

Bu makalede, karma Azure AD 'ye katılmış cihazlar için dağıtım sık sorulan sorular (SSS) ve şirket içi kaynaklarda daha az oturum açma izni yer almaktadır. Bu passwordless özelliği sayesinde, FIDO2 güvenlik anahtarlarını kullanarak karma Azure AD 'ye katılmış cihazlar için Windows 10 cihazlarında Azure AD kimlik doğrulamasını etkinleştirebilirsiniz. Kullanıcılar, FIDO2 anahtarları gibi modern kimlik bilgileriyle Windows 'da oturum açabilir ve şirket içi kaynaklara sorunsuz bir çoklu oturum açma (SSO) deneyimi ile geleneksel Active Directory Domain Services (AD DS) tabanlı kaynaklara erişebilir.

Karma ortamdaki kullanıcılar için aşağıdaki senaryolar desteklenir:

* FIDO2 güvenlik anahtarlarını kullanarak hibrit Azure AD 'ye katılmış cihazlarda oturum açın ve şirket içi kaynaklara SSO erişimi alın.
* FIDO2 güvenlik anahtarlarını kullanarak Azure AD 'ye katılmış cihazlarda oturum açın ve şirket içi kaynaklara SSO erişimi alın.

FIDO2 güvenlik anahtarlarını ve şirket içi kaynaklara karma erişimi kullanmaya başlamak için aşağıdaki makalelere bakın:

* [Parolasız FIDO2 güvenlik anahtarları](howto-authentication-passwordless-security-key.md)
* [Parolasız Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Parolasız şirket içi](howto-authentication-passwordless-security-key-on-premises.md)

## <a name="security-keys"></a>Güvenlik anahtarları

* [Kuruluşumun kaynaklara erişmesi için iki öğeli kimlik doğrulaması gerekir. Bu gereksinimi desteklemek için ne yapabilirim?](#my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement)
* [Uyumlu FIDO2 güvenlik anahtarlarını nerede bulabilirim?](#where-can-i-find-compliant-fido2-security-keys)
* [Güvenlik Anahtarımı kaybedersem ne yapmam gerekiyor?](#what-if-i-lose-my-security-key)
* [Veriler FIDO2 güvenlik anahtarında nasıl korunuyor?](#how-is-the-data-protected-on-the-fido2-security-key)
* [FIDO2 güvenlik anahtarlarının kaydı nasıl çalışır?](#how-does-the-registering-of-fido2-security-keys-work)
* [Yöneticilerin kullanıcılara doğrudan anahtar sağlaması için bir yol var mı?](#is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly)

### <a name="my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Kuruluşum, kaynaklara erişmek için çok faktörlü kimlik doğrulaması gerektiriyor. Bu gereksinimi desteklemek için ne yapabilirim?

FIDO2 güvenlik anahtarları çeşitli form faktörleri halinde gelir. Cihazların bir PIN veya biyometri ile ikinci bir faktör ile nasıl etkinleştirilenebileceği hakkında tartışmak için cihaz üreticisine başvurun. Desteklenen sağlayıcıların listesi için bkz. [FIDO2 Security Keys Providers](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="where-can-i-find-compliant-fido2-security-keys"></a>Uyumlu FIDO2 güvenlik anahtarlarını nerede bulabilirim?

Desteklenen sağlayıcıların listesi için bkz. [FIDO2 Security Keys Providers](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="what-if-i-lose-my-security-key"></a>Güvenlik Anahtarımı kaybedersem ne yapmalıyım?

**Güvenlik bilgileri** sayfasına gıderek ve FIDO2 güvenlik anahtarını kaldırarak Azure Portal anahtarlarını kaldırabilirsiniz.

### <a name="how-is-the-data-protected-on-the-fido2-security-key"></a>Veriler FIDO2 güvenlik anahtarında nasıl korunuyor?

FIDO2 güvenlik anahtarlarının, bunlarda depolanan özel anahtarları koruyan güvenli şifreleri vardır. Bir FIDO2 güvenlik anahtarı, içinde yerleşik olarak bulunan ve özel anahtarı ayıklayabileceğiniz Windows Hello gibi gerçekçi bir merme özelliği de vardır.

### <a name="how-does-the-registering-of-fido2-security-keys-work"></a>FIDO2 güvenlik anahtarlarının kaydı nasıl çalışır?

FIDO2 güvenlik anahtarlarını kaydetme ve kullanma hakkında daha fazla bilgi için bkz. [passwordless güvenlik anahtarı oturum açma 'Yı etkinleştirme](howto-authentication-passwordless-security-key.md).

### <a name="is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly"></a>Yöneticilerin kullanıcılara doğrudan anahtar sağlaması için bir yol var mı?

Hayır, şu anda değil.

## <a name="prerequisites"></a>Önkoşullar

* [Internet bağlantısı yoksa bu özellik çalışır mı?](#does-this-feature-work-if-theres-no-internet-connectivity)
* [Azure AD 'de açık olması gereken belirli uç noktaları nelerdir?](#what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad)
* [Windows 10 cihazım için etki alanına katılım türünü (Azure AD 'ye katılmış veya hibrit Azure AD 'ye katılmış) belirlemek Nasıl yaparım?.](#how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device)
* [Düzeltme Eki eklenecek DC sayısına ilişkin öneri nedir?](#whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched)
* [FIDO2 Credential sağlayıcısını yalnızca şirket içi bir cihaza dağıtabilir miyim?](#can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device)
* [FIDO2 güvenlik anahtarı oturum açma, etki alanı yöneticisi veya diğer yüksek ayrıcalıklı hesaplar için çalışmıyor. Kaydol?](#fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why)

### <a name="does-this-feature-work-if-theres-no-internet-connectivity"></a>Internet bağlantısı yoksa bu özellik çalışır mı?

Internet bağlantısı, bu özelliği etkinleştirmek için bir ön özelliktir. Bir Kullanıcı FIDO2 güvenlik anahtarlarını kullanarak ilk kez oturum açtığında, Internet bağlantısına sahip olmaları gerekir. Sonraki oturum açma olayları için, önbelleğe alınan oturum açma çalışır ve kullanıcının internet bağlantısı olmadan kimlik doğrulaması yapmasına izin verir.

Tutarlı bir deneyim için, cihazların DC 'lere internet erişimi ve görüş satırı olduğundan emin olun.

### <a name="what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad"></a>Azure AD 'de açık olması gereken belirli uç noktaları nelerdir?

Kayıt ve kimlik doğrulama için aşağıdaki uç noktalar gereklidir:

* **. microsoftonline.com*
* **. microsoftonline-p.com*
* **. msauth.net*
* **. msauthimages.net*
* **. msecnd.net*
* **. msftauth.net*
* **. msftauthimages.net*
* **. phonefactor.net*
* *enterpriseregistration.windows.net*
* *management.azure.com*
* *policykeyservice.dc.ad.msft.net*
* *secure.aadcdn.microsoftonline-p.com*

Microsoft Online ürünlerini kullanmak için gereken uç noktaların tam listesi için bkz. [Office 365 URL 'leri ve IP adresi aralıkları](/microsoft-365/enterprise/urls-and-ip-address-ranges).

### <a name="how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device"></a>Windows 10 cihazım için etki alanına katılım türünü (Azure AD 'ye katılmış veya hibrit Azure AD 'ye katılmış) belirlemek Nasıl yaparım?.

Windows 10 istemci cihazının doğru etki alanına katılması türü olup olmadığını denetlemek için aşağıdaki komutu kullanın:

```console
Dsregcmd/status
```

Aşağıdaki örnek çıktıda, *Azureadkatıldığında* 'un *Evet* olarak AYARLANDıĞı, cihazın Azure AD 'ye katılmış olduğu gösterilmektedir:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: NO
```

Aşağıdaki örnek çıktıda, *Domainedkatıldığında* 'un da *Evet* olarak ayarlandığı, cihazın karma Azure AD 'ye katılmış olduğu gösterilmektedir. *DomainName* de gösterilir:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: YES
DomainName: CONTOSO
```

Windows Server 2016 veya 2019 etki alanı denetleyicisinde aşağıdaki düzeltme eklerinin uygulandığından emin olun. Gerekirse, yüklemek için Windows Update çalıştırın:

* Windows Server 2016- [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019- [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

İstemci cihazından, yüklü düzeltme ekleriyle uygun bir etki alanı denetleyicisine bağlantıyı doğrulamak için aşağıdaki komutu çalıştırın:

```console
nltest /dsgetdc:<domain> /keylist /kdc
```

### <a name="whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched"></a>Düzeltme Eki eklenecek DC sayısına ilişkin öneri nedir?

Kuruluşunuzun kimlik doğrulama isteği yükünü işleyebilmelerini sağlamak için, düzeltme ekiyle Windows Server 2016 veya 2019 etki alanı denetleyicilerinizin büyük bir bölümünü düzeltme eki uygulamanızı öneririz.

Windows Server 2016 veya 2019 etki alanı denetleyicisinde aşağıdaki düzeltme eklerinin uygulandığından emin olun. Gerekirse, yüklemek için Windows Update çalıştırın:

* Windows Server 2016- [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019- [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device"></a>FIDO2 Credential sağlayıcısını yalnızca şirket içi bir cihaza dağıtabilir miyim?

Hayır, bu özellik yalnızca şirket içi cihaz için desteklenmiyor. FIDO2 kimlik bilgisi sağlayıcısı gösterilmez.

### <a name="fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why"></a>FIDO2 güvenlik anahtarı oturum açma, etki alanı yöneticisi veya diğer yüksek ayrıcalıklı hesaplar için çalışmıyor. Neden?

Varsayılan güvenlik ilkesi, şirket içi kaynaklara yönelik yüksek ayrıcalıklı hesapları imzalamak için Azure AD izni vermez.

Hesapların engellemesini kaldırmak için, *Azure AD Kerberos bilgisayar nesnesinin (CN = AzureADKerberos, OU = etki alanı denetleyicileri, \<domain-DN> )* *msDS-NeverRevealGroup* özelliğini değiştirmek üzere **Kullanıcı ve bilgisayar Active Directory** kullanın.

## <a name="under-the-hood"></a>Başlık altında

* [Azure AD Kerberos, şirket içi Active Directory Domain Services ortamlarıma nasıl bağlanır?](#how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment)
* [AD 'de oluşturulan ve Azure AD 'de yayınlanan bu Kerberos Sunucusu nesnelerini nereden görüntüleyebilirim?](#where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad)
* [İnternet 'te hiçbir bağımlılığı olmadığından, şirket içi AD DS ortak anahtara kaydolmuyoruz.](#why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet)
* [Anahtarlar Kerberos Sunucusu nesnesinde nasıl döndürülür?](#how-are-the-keys-rotated-on-the-kerberos-server-object)
* [Neden Azure AD Connect gerekir? AD DS Azure AD 'ye geri bilgi yazar mı?](#why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad)
* [PRT + kısmi TGT istenirken HTTP isteği/yanıtı nasıl görünür?](#what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt)

### <a name="how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment"></a>Azure AD Kerberos, şirket içi Active Directory Domain Services ortamlarıma nasıl bağlanır?

İki bölümden oluşur-şirket içi AD DS ortamı ve Azure AD kiracısı.

**Active Directory Etki Alanı Hizmetleri (AD DS)**

Azure AD Kerberos sunucusu, bir şirket içi AD DS ortamında etki alanı denetleyicisi (DC) nesnesi olarak temsil edilir. Bu DC nesnesi birden çok nesneden oluşur:

* *CN = AzureADKerberos, OU = etki alanı denetleyicileri,\<domain-DN>*
    
    AD DS bir Read-Only etki alanı denetleyicisini (RODC) temsil eden bir *bilgisayar* nesnesi. Bu nesneyle ilişkili bilgisayar yok. Bunun yerine, bir DC 'nin mantıksal bir gösterimidir.

* *CN = krbtgt_AzureAD, CN = kullanıcılar,\<domain-DN>*

    RODC Kerberos Anahtar verme anahtarı (TGT) şifreleme anahtarını temsil eden bir *Kullanıcı* nesnesi.

* *CN = 900274c4-b7d2-43c8-90EE-00a9f650e335, CN = AzureAD, CN = System,\<domain-DN>*

    Azure AD Kerberos sunucu nesneleriyle ilgili meta verileri depolayan bir *serviceConnectionPoint* nesnesi. Yönetim Araçları bu nesneyi kullanarak Azure AD Kerberos sunucu nesnelerini tanımlayabilir ve bulur.

**Azure Active Directory**

Azure AD Kerberos sunucusu, Azure AD 'de bir *KerberosDomain* nesnesi olarak temsil edilir. Her şirket içi AD DS ortamı, Azure AD kiracısında tek bir *KerberosDomain* nesnesi olarak temsil edilir.

Örneğin, *contoso.com* ve *fabrikam.com* gibi iki etki alanına sahip bir AD DS ormanına sahip olabilirsiniz. Azure AD 'nin tüm orman için Kerberos Anahtar verme biletlerini (TGT 'ler) vermesine izin verirseniz, Azure AD 'de iki *KerberosDomain* nesnesi vardır; *contoso.com* için bir nesne ve *fabrikam.com* için bir tane.

Birden çok AD DS ormanınızda, her ormandaki her etki alanı için bir *KerberosDomain* nesneniz vardır.

### <a name="where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad"></a>AD DS oluşturulan ve Azure AD 'de yayınlanan bu Kerberos Sunucusu nesnelerini nerede görüntüleyebilirim?

Tüm nesneleri görüntülemek için Azure AD Connect en son sürümüne dahil olan Azure AD Kerberos Server PowerShell cmdlet 'lerini kullanın.

Nesneleri görüntüleme yönergeleri dahil daha fazla bilgi için bkz. [Kerberos sunucu nesneleri oluşturma](howto-authentication-passwordless-security-key-on-premises.md#create-kerberos-server-object).

### <a name="why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet"></a>İnternet 'te hiçbir bağımlılığı olmadığından, şirket içi AD DS ortak anahtara kaydolmuyoruz.

Iş için Windows Hello dağıtım modelinin karmaşıklığı etrafında geri bildirimde bulunduk. bu nedenle, sertifikaları ve PKI 'yı kullanmak zorunda kalmadan dağıtım modelini basitleştirmek (FIDO2 sertifikaları kullanmaz).

### <a name="how-are-the-keys-rotated-on-the-kerberos-server-object"></a>Anahtarlar Kerberos Sunucusu nesnesinde nasıl döndürülür?

Diğer tüm DC 'ler gibi Azure AD Kerberos sunucu şifrelemesi *krbtgt* anahtarları düzenli olarak döndürülmelidir. Tüm diğer AD DS *krbtgt* anahtarlarını döndürmek için kullandığınız zamanlamanın aynısını izlemeniz önerilir.

> [!NOTE]
> *Krbtgt* anahtarlarını döndürmek için başka araçlar da olsa da, Azure AD Kerberos sunucunuzun [ *krbtgt* anahtarlarını döndürmek için PowerShell cmdlet 'lerini kullanmanız](howto-authentication-passwordless-security-key-on-premises.md#rotating-the-azure-ad-kerberos-server-key) gerekir. Bu yöntem, anahtarların hem şirket içi AD DS ortamında hem de Azure AD 'de güncelleştirildiğinden emin olmanızı sağlar.

### <a name="why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad"></a>Neden Azure AD Connect gerekir? AD DS Azure AD 'ye geri bilgi yazar mı?

Azure AD Connect, bilgileri Azure AD 'den AD DS 'a geri yazmıyor. Yardımcı programı, AD DS içinde Kerberos Sunucusu nesnesini oluşturmak ve Azure AD 'de yayımlamak için PowerShell modülünü içerir.

### <a name="what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt"></a>PRT + kısmi TGT istenirken HTTP isteği/yanıtı nasıl görünür?

HTTP isteği, standart bir birincil yenileme belirteci (PRT) isteği. Bu PRT isteği, Kerberos Anahtar verme bileti 'nin (TGT) gerekli olduğunu belirten bir talep içerir.

| İste | Değer | Açıklama                             |
|-------|-------|-----------------------------------------|
| dikten   | true  | Talep, istemcinin TGT 'ye ihtiyacı olduğunu gösterir. |

Azure AD, şifreli istemci anahtarını ve ileti arabelleğini, ek özellikler olarak PRT yanıtıyla birleştirir. Yük, Azure AD cihaz oturum anahtarı kullanılarak şifrelenir.

| Alan              | Tür   | Description  |
|--------------------|--------|--------------|
| tgt_client_key     | string | Base64 kodlamalı istemci anahtarı (gizli). Bu anahtar, TGT 'yi korumak için kullanılan istemci sırrı ' dir. Bu passwordless senaryosunda, istemci parolası her TGT isteğinin bir parçası olarak sunucu tarafından oluşturulur ve ardından yanıtta istemciye döndürülür. |
| tgt_key_type       | int    | KERB_MESSAGE_BUFFER dahil olan istemci anahtarı ve Kerberos oturum anahtarı için kullanılan şirket içi AD DS anahtar türü. |
| tgt_message_buffer | string | Base64 kodlamalı KERB_MESSAGE_BUFFER. |

## <a name="next-steps"></a>Sonraki adımlar

FIDO2 güvenlik anahtarlarını ve şirket içi kaynaklara karma erişimi kullanmaya başlamak için aşağıdaki makalelere bakın:

* [Parolasız FIDO2 güvenlik anahtarları](howto-authentication-passwordless-security-key.md)
* [Parolasız Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Parolasız şirket içi](howto-authentication-passwordless-security-key-on-premises.md)
