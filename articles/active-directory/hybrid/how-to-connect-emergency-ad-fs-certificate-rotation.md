---
title: AD FS sertifikalarının acil durum dönüşü | Microsoft Docs
description: Bu makalede AD FS sertifikalarının hemen nasıl iptal edileceği ve güncelleştirilmesi açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 9035c0a91bbbd7493437c692540fcbb3136a094e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613130"
---
# <a name="emergency-rotation-of-the-ad-fs-certificates"></a>AD FS sertifikalarının acil durum dönüşü
AD FS sertifikalarını hemen döndürmenizi gerektiren olayda, bu bölümde aşağıda özetlenen adımları izleyebilirsiniz.

> [!IMPORTANT]
> AD FS ortamda aşağıdaki adımları yürütmek, eski sertifikaları hemen iptal eder.  Bu işlem anında yapıldığından, genellikle Federasyon iş ortaklarınızın yeni sertifikanızı kullanması için izin verilen normal zaman geçirilir. Bu, yeni sertifikaları kullanmak için güven güncelleştirmesi olan bir hizmet kesintisine neden olabilir.  Bu, tüm Federasyon ortakları yeni sertifikalara sahip olduktan sonra çözümlenmelidir.

> [!NOTE]
> Microsoft, sertifikaları korumak ve güvenli hale getirmek için bir donanım güvenlik modülü (HSM) kullanmanızı çok öneriyor.
> Daha fazla bilgi için, AD FS güvenli hale getirmek için en iyi yöntemler altında [donanım güvenlik modülüne](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm) bakın

## <a name="determine-your-token-signing-certificate-thumbprint"></a>Belirteç Imzalama sertifikası parmak izinizi belirleme
AD FS Şu anda kullandığı eski belirteç Imzalama sertifikasını iptal etmek için, belirteç sigbu sertifikanın parmak izini belirlemeniz gerekir.  Bunu yapmak için aşağıdaki adımları kullanın:

 1. Microsoft Online hizmetine bağlanma `PS C:\>Connect-MsolService`
 2. Hem şirket içi hem de bulut belirteci Imzalama sertifikası parmak izinizi ve sona erme tarihlerini belgeleyin.
`PS C:\>Get-MsolFederationProperty -DomainName <domain>` 
 3.  Parmak izini kopyalayın.  Daha sonra mevcut sertifikaları kaldırmak için kullanılır.

Ayrıca, AD FS Yönetimi kullanarak parmak izini alabilir, hizmet/sertifikalara giderek sertifikaya sağ tıklayıp sertifikayı görüntüle ' yi ve ardından Ayrıntılar ' ı seçebilirsiniz. 

## <a name="determine-whether-ad-fs-renews-the-certificates-automatically"></a>AD FS sertifikaların otomatik olarak mi yenilemediğini belirleme
Varsayılan olarak, AD FS otomatik olarak belirteç imzalama ve belirteç şifre çözme sertifikaları oluşturmak üzere yapılandırılır ve bu, hem ilk yapılandırma zamanında hem de sertifikaların sona erme tarihine yaklaştığı zaman.

Aşağıdaki Windows PowerShell komutunu çalıştırabilirsiniz: `PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*` .

AutoCertificateRollover özelliği, AD FS belirteç imzalama ve belirteç şifre çözme sertifikalarını otomatik olarak yenilemek için yapılandırılıp yapılandırılmadığını açıklar.  AutoCertificateRollover değeri TRUE olarak ayarlanırsa, [AutoCertificateRollover TRUE olarak ayarlandıysa, yeni otomatik olarak imzalanan sertifika oluşturma "bölümünde özetlenen yönergeleri izleyin.  Eğer AutoCertificateRollover FALSE olarak ayarlandıysa, [AutoCertificateRollover FALSE olarak ayarlandıysa yeni sertifikalar el ile üretiliyor "bölümünde belirtilen yönergeleri izleyin.


## <a name="generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true"></a>AutoCertificateRollover TRUE olarak ayarlandıysa, otomatik olarak imzalanan yeni sertifika oluşturuluyor
Bu bölümde, **iki** belirteç imzalama sertifikası oluşturacaksınız.  İlki **-acil** bayrağını kullanır, bu da geçerli birincil sertifikanın hemen yerini alır.  İkinci sertifika ikincil sertifika için kullanılacaktır.  

>[!IMPORTANT]
>İki sertifika oluşturmamız nedeni, Azure 'un önceki sertifikayla ilgili bilgiler üzerinde tutmasıdır.  İkinci bir tane oluşturarak, Azure 'u eski sertifikayla ilgili bilgileri serbest bırakmaya ve ikinci sertifikayla ilgili bilgilerle değiştirmeye zorlıyoruz.
>
>İkinci sertifikayı oluşturup Azure ile güncelleştirirseniz, kullanıcıların kimliğini doğrulamak için eski belirteç imzalama sertifikası mümkün olabilir.

Yeni belirteç imzalama sertifikalarını oluşturmak için aşağıdaki adımları kullanabilirsiniz.

 1. Birincil AD FS sunucuda oturum açtığınızdan emin olun.
 2. Windows PowerShell'i yönetici olarak açın. 
 3. AutoCertificateRollover 'in true olarak ayarlandığından emin olun.
`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`
 4. Yeni bir belirteç imzalama sertifikası oluşturmak için: `Update-ADFSCertificate –CertificateType token-signing -Urgent` .
 5. Aşağıdaki komutu çalıştırarak güncelleştirmeyi doğrulayın: `Get-ADFSCertificate –CertificateType token-signing`
 6. Şimdi ikinci belirteç imzalama sertifikasını üretin: `Update-ADFSCertificate –CertificateType token-signing` .
 7. Aşağıdaki komutu yeniden çalıştırarak güncelleştirmeyi doğrulayabilirsiniz: `Get-ADFSCertificate –CertificateType token-signing`


## <a name="generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false"></a>AutoCertificateRollover FALSE olarak ayarlandıysa yeni sertifikalar el ile üretiliyor
Otomatik olarak oluşturulan, otomatik olarak imzalanan belirteç imzalama ve belirteç şifre çözme sertifikalarını kullanmıyorsanız, bu sertifikaları el ile yenilemeniz ve yapılandırmanız gerekir.  Bu, iki yeni belirteç imzalama sertifikası oluşturulmasını ve bunların içeri aktarılmasını içerir.  Ardından, bir tane birincili yükseltir, eski sertifikayı iptal edip ikinci sertifikayı ikincil sertifika olarak yapılandıracaksınız.

İlk olarak, Sertifika yetkilinizden iki yeni sertifika edinmeniz ve bunları her federasyon sunucusundaki yerel makine kişisel sertifika deposuna aktarmanız gerekir. Yönergeler için bkz. [sertifika alma](https://technet.microsoft.com/library/cc754489.aspx) makalesi.

>[!IMPORTANT]
>İki sertifika oluşturmamız nedeni, Azure 'un önceki sertifikayla ilgili bilgiler üzerinde tutmasıdır.  İkinci bir tane oluşturarak, Azure 'u eski sertifikayla ilgili bilgileri serbest bırakmaya ve ikinci sertifikayla ilgili bilgilerle değiştirmeye zorlıyoruz.
>
>İkinci sertifikayı oluşturup Azure ile güncelleştirirseniz, kullanıcıların kimliğini doğrulamak için eski belirteç imzalama sertifikası mümkün olabilir.

### <a name="to-configure-a-new-certificate-as-a-secondary-certificate"></a>Yeni bir sertifikayı ikincil sertifika olarak yapılandırmak için
Ardından, ikincil AD FS belirteç imzalama veya şifre çözme sertifikası olarak bir sertifika yapılandırmanız ve ardından birincili olarak yükseltmeniz gerekir

1. Sertifikayı içeri aktardıktan sonra. **AD FS Yönetim** konsolunu açın.
2. **Hizmet** ' i genişletin ve ardından **Sertifikalar**' ı seçin.
3. Eylemler bölmesinde **Token-Signing sertifikası Ekle**' ye tıklayın.
4. Görüntülenmiş sertifikalar listesinden yeni sertifikayı seçin ve ardından Tamam ' a tıklayın.

### <a name="to-promote-the-new-certificate-from-secondary-to-primary"></a>Yeni sertifikayı ikincilden birinciye yükseltmek için
Yeni sertifika içeri aktarıldığından ve AD FS göre yapılandırıldığına göre, birincil sertifika olarak ayarlamanız gerekir.
1. **AD FS Yönetim** konsolunu açın.
2. **Hizmet** ' i genişletin ve ardından **Sertifikalar**' ı seçin.
3. İkincil belirteç imzalama sertifikasına tıklayın.
4. **Eylemler** bölmesinde **birincil olarak ayarla**' ya tıklayın. Onay isteminde Evet ' e tıklayın.
5. Yeni sertifikayı birincil sertifika olarak yükselttikten sonra, hala kullanılabilir olduğu için eski sertifikayı kaldırmanız gerekir. Aşağıdaki [eski sertifikalarınızı kaldırma](#remove-your-old-certificates) bölümüne bakın.  

### <a name="to-configure-the-second-certificate-as-a-secondary-certificate"></a>İkinci sertifikayı ikincil sertifika olarak yapılandırmak için
İlk sertifikayı ekledığınıza ve birincisini kaldırmış olduğunuza göre, ikinci sertifikayı içeri aktarın.  Ardından sertifikayı ikincil AD FS belirteç imzalama sertifikası olarak yapılandırmanız gerekir

1. Sertifikayı içeri aktardıktan sonra. **AD FS Yönetim** konsolunu açın.
2. **Hizmet** ' i genişletin ve ardından **Sertifikalar**' ı seçin.
3. Eylemler bölmesinde **Token-Signing sertifikası Ekle**' ye tıklayın.
4. Görüntülenmiş sertifikalar listesinden yeni sertifikayı seçin ve ardından Tamam ' a tıklayın.

## <a name="update-azure-ad-with-the-new-token-signing-certificate"></a>Azure AD 'yi yeni belirteç imzalama sertifikasıyla güncelleştirme
Windows PowerShell için Microsoft Azure Active Directory Modülü açın. Alternatif olarak, Windows PowerShell 'i açın ve ardından komutunu çalıştırın `Import-Module msonline`

Şu komutu çalıştırarak Azure AD 'ye bağlanın: `Connect-MsolService` , ve ardından Genel yönetici kimlik bilgilerinizi girin.

>[!Note]
> Bu komutları birincil federasyon sunucusu olmayan bir bilgisayarda çalıştırıyorsanız, önce şu komutu girin: `Set-MsolADFSContext –Computer <servername>` . <servername>AD FS sunucusunun adıyla değiştirin. İstendiğinde AD FS sunucusu için yönetici kimlik bilgilerini girin.

İsteğe bağlı olarak, Azure AD 'de geçerli sertifika bilgilerini denetleyerek bir güncelleştirmenin gerekli olup olmadığını doğrulayın. Bunu yapmak için şu komutu çalıştırın: `Get-MsolFederationProperty` . İstendiğinde, Federasyon etki alanının adını girin.

Azure AD 'deki sertifika bilgilerini güncelleştirmek için aşağıdaki komutu çalıştırın: `Update-MsolFederatedDomain` ve istendiğinde etki alanı adını girin.

>[!Note]
> Bu komutu çalıştırırken bir hata görürseniz, aşağıdaki komutu çalıştırın: `Update-MsolFederatedDomain –SupportMultipleDomain` ve istendiğinde etki alanı adını girin.

## <a name="replace-ssl-certificates"></a>SSL sertifikalarını değiştirme
Bir uzlaşmaya karşı belirteç imzalama sertifikanızı değiştirmeniz gereken olayda, AD FS ve WAP sunucularınızın SSL sertifikalarını da iptal etmeniz ve değiştirmeniz gerekir.  

SSL sertifikalarınızı iptal etmek sertifikayı veren sertifika yetkilisi (CA) üzerinde yapılmalıdır.  Bu sertifikalar genellikle GoDaddy gibi 3. taraf sağlayıcılar tarafından verilir.  Bir örnek için bkz. (sertifikayı Iptal etme | SSL sertifikaları-GoDaddy yardım ABD).  Daha fazla bilgi için bkz. [sertifika Iptali nasıl çalıştığını öğrenin](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619754(v=ws.10)?redirectedfrom=MSDN).

Eski SSL sertifikası iptal edildikten ve yeni bir tane verildikten sonra, SSL sertifikalarını değiştirebilirsiniz. Daha fazla bilgi için bkz. [AD FS SSL sertifikasını değiştirme](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs).


## <a name="remove-your-old-certificates"></a>Eski sertifikalarınızı kaldırın
Eski sertifikalarınızı değiştirdikten sonra, hala kullanılabilir olduğu için eski sertifikayı kaldırmanız gerekir. . Bunu yapmak için aşağıdaki adımları izleyin:.  Bunu yapmak için aşağıdaki adımları izleyin:

1. Birincil AD FS sunucuda oturum açtığınızdan emin olun.
2. Windows PowerShell'i yönetici olarak açın. 
4. Eski belirteç imzalama sertifikasını kaldırmak için: `Remove-ADFSCertificate –CertificateType token-signing -thumbprint <thumbprint>` .

## <a name="updating-federation-partners-who-can-consume-federation-metadata"></a>Federasyon meta verilerini kullanabilecek Federasyon ortakları güncelleştiriliyor
Yeni bir belirteç imzalama veya belirteç şifre çözme sertifikası yenilediyseniz ve yapılandırırsanız, tüm Federasyon iş ortaklarınızın (bağlı olan taraf güvenleri ve talep sağlayıcı güvenleri tarafından AD FS temsil edilen kaynak kuruluş veya hesap kuruluş iş ortaklarının) yeni sertifikaları seçtiğinizden emin olmanız gerekir.

## <a name="updating-federation-partners-who-can-not-consume-federation-metadata"></a>Federasyon meta verilerini tüketmeyen Federasyon iş ortakları güncelleştiriliyor
Federasyon iş ortaklarınız Federasyon meta verilerinizi tüketmez, yeni belirteç imzalama/belirteç şifre çözme sertifikanızın ortak anahtarını el ile göndermeniz gerekir. Yeni sertifika ortak anahtarınızı (. cer dosyası veya. p7b ' i tüm zinciri dahil etmek istiyorsanız) tüm kaynak kuruluşunuza veya hesap kuruluş iş ortaklarınıza (bağlı olan taraf güvenleri ve talep sağlayıcı güvenleri tarafından AD FS gösterilir) gönderin. İş ortaklarının yeni sertifikalara güvenmek için tarafında değişiklikler uygulayıp uygulamalarını sağlayabilirsiniz.



## <a name="revoke-refresh-tokens-via-powershell"></a>PowerShell ile yenileme belirteçlerini iptal et
Şimdi bu kullanıcılara sahip olabilecek kullanıcılar için yenileme belirteçlerini iptal etmek ve yeni belirteçleri yeniden oturum açmaya zorlamak istiyoruz.  Bu, kullanıcıları telefonlarından, geçerli Web postasından oturumlarından ve belirteçleri kullanan diğer öğelerle birlikte günlüğe kaydeder.  Bilgiler [burada](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true) bulunabilir ve ayrıca [Azure Active Directory Kullanıcı erişimini nasıl iptal](../../active-directory/enterprise-users/users-revoke-access.md)edebilme de başvurabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Windows Server 2016 ' de AD FS ve WAP 'da SSL sertifikalarını yönetme](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)
- [AD FS için belirteç Imzalama ve belirteç şifre çözme sertifikaları edinin ve yapılandırın](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn781426(v=ws.11)#updating-federation-partners)
- [Microsoft 365 ve Azure Active Directory için Federasyon sertifikalarını yenileme](how-to-connect-fed-o365-certs.md)



















