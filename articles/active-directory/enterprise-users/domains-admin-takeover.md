---
title: Yönetilmeyen bir dizinin Yöneticisi-Azure AD | Microsoft Docs
description: Yönetilmeyen bir Azure AD kuruluşunda (gölge kiracı) bir DNS etki alanı adı alma.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 04/18/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 816f4645626675ae19a462ac8707e995c3b4045e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739388"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Azure Active Directory’de yönetilmeyen bir dizini yönetici olarak devralma

Bu makalede, Azure Active Directory (Azure AD) içinde yönetilmeyen bir dizindeki DNS etki alanı adını almanın iki yolu açıklanmaktadır. Bir self servis kullanıcısı, Azure AD kullanan bir bulut hizmetine kaydolduğunda bu kullanıcı, e-posta etki alanına göre yönetilmeyen bir Azure AD dizinine eklenir. Self servis veya "viral" bir hizmete kaydolma hakkında daha fazla bilgi için bkz. [Azure Active Directory için self servis kaydolma nedir?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Yönetilmeyen bir dizini nasıl almak istediğinize karar verin
Yönetici devralma işlemi sırasında, [Azure AD’ye özel etki alanı adı ekleme](../fundamentals/add-custom-domain.md) bölümünde açıklandığı gibi sahipliği kanıtlayabilirsiniz. Sonraki bölümlerde, yönetici deneyimi daha ayrıntılı şekilde açıklanmaktadır, ancak bir özeti aşağıda verilmiştir:

* Yönetilmeyen bir Azure dizininin ["iç" yönetici devralma işlemini](#internal-admin-takeover) gerçekleştirdiğinizde, yönetilmeyen dizinin genel yöneticisi olarak eklendiniz. Hiçbir kullanıcı, etki alanı veya hizmet planı, yönettiğiniz başka bir dizine geçirilmez.

* Yönetilmeyen bir Azure dizininin ["dış" yönetici devralma işlemini](#external-admin-takeover) gerçekleştirdiğinizde, yönetilmeyen dizininizin DNS etki alanı adını, yönetilen Azure dizininize eklersiniz. Etki alanı adını eklediğinizde, kullanıcıların kesinti olmadan hizmetlere erişmeye devam edebilmesi için yönetilen Azure dizininizde, kullanıcıların kaynaklara bir eşlemesi oluşturulur. 

## <a name="internal-admin-takeover"></a>İç yönetici devralması

Microsoft 365 gibi SharePoint ve OneDrive içeren bazı ürünler dışarıdan devralmayı desteklemez. Bu senaryonuz varsa veya bir yöneticileriniz varsa ve self servis kaydolma kullanan kullanıcılar tarafından yönetilen bir yönetilmeyen veya "gölge" Azure AD kuruluşu üzerinden yararlanmak istiyorsanız, bunu bir iç yönetici ile gerçekleştirebilirsiniz.

1. Power BI için kaydolup yönetilmeyen kuruluşta bir kullanıcı bağlamı oluşturun. Örneğin, bu adımlar bu yolu kabul eder.

2. [Power BI sitesini](https://powerbi.com) açın ve **ücretsiz Başlat**' ı seçin. Kuruluşun etki alanı adını kullanan bir kullanıcı hesabı girin; Örneğin, `admin@fourthcoffee.xyz` . Doğrulama kodunu girdikten sonra, onay kodu için e-postanızı kontrol edin.

3. Power BI 'den onay e-postasında, **Evet '** i seçin.

4. [Microsoft 365 Yönetim merkezinde](https://portal.office.com/admintakeover) Power BI kullanıcı hesabıyla oturum açın. Yönetilmeyen kuruluşta zaten doğrulanmış olan etki alanı adının **yöneticisi olmak** üzere size yönlendiren bir ileti alırsınız. **Evet, yönetici olmak istiyorum ' u** seçin.
  
   ![Yönetici olacak ilk ekran görüntüsü](./media/domains-admin-takeover/become-admin-first.png)
  
5. Etki alanı adı kayıt şirketinizde **fourthcoffee.xyz** etki alanı adını sahip olduğunuzu KANıTLAMAK için TXT kaydını ekleyin. Bu örnekte, GoDaddy.com ' dir.
  
   ![Etki alanı adı için bir TXT kaydı ekleyin](./media/domains-admin-takeover/become-admin-txt-record.png)

DNS TXT kayıtları, etki alanı adı kaydedicisinde doğrulandığında Azure AD organizasyonunu yönetebilirsiniz.

Yukarıdaki adımları tamamladığınızda, artık Microsoft 365 dördüncü kahve kuruluşunun genel yöneticisi olursunuz. Etki alanı adını diğer Azure hizmetlerinize tümleştirebilmek için Microsoft 365 ' dan kaldırabilir ve Azure 'da farklı bir yönetilen kuruluşa ekleyebilirsiniz.

### <a name="adding-the-domain-name-to-a-managed-organization-in-azure-ad"></a>Azure AD 'de yönetilen bir kuruluşa etki alanı adı ekleme

1. [Microsoft 365 Yönetim merkezini](https://admin.microsoft.com)açın.
2. **Kullanıcılar** sekmesini seçin ve özel etki alanı adı kullanmayan *user \@ fourthcoffeexyz.onmicrosoft.com* gibi bir ada sahip yeni bir kullanıcı hesabı oluşturun. 
3. Yeni Kullanıcı hesabının Azure AD kuruluşu için genel yönetici ayrıcalıklarına sahip olduğundan emin olun.
4. Microsoft 365 Yönetim merkezinde **etki alanları** sekmesini açın, etki alanı adını seçin ve **Kaldır**' ı seçin. 
  
   ![Microsoft 365 etki alanı adını kaldır](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Microsoft 365 ' de, kaldırılan etki alanı adına başvuran herhangi bir kullanıcı veya grubunuz varsa,. onmicrosoft.com etki alanı olarak yeniden adlandırılması gerekir. Etki alanı adını silmeye zorlarsanız, bu örnekte *user \@ fourthcoffeexyz.onmicrosoft.com* olarak tüm kullanıcılar otomatik olarak yeniden adlandırılır.
  
6. Azure AD [Yönetim merkezinde](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) Azure AD kuruluşu için genel yönetici olan bir hesapla oturum açın.
  
7. **Özel etki alanı adları**' nı seçin ve ardından etki alanı adını ekleyin. Etki alanı adının sahipliğini doğrulamak için DNS TXT kayıtlarını girmeniz gerekir. 
  
   ![Azure AD 'ye eklenen etki alanı doğrulandı](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Microsoft 365 kuruluşta atanmış lisanslarına sahip tüm Power BI veya Azure Rights Management hizmet kullanıcıları, etki alanı adı kaldırılırsa panoları kaydetmelidir. Kullanıcı *\@ fourthcoffee.xyz* yerine user *\@ fourthcoffeexyz.onmicrosoft.com* gibi bir Kullanıcı adı ile oturum açması gerekir.

## <a name="external-admin-takeover"></a>Dış yönetici devralması

Bir kuruluşu zaten Azure hizmetleri veya Microsoft 365 ile yönetiyorsanız, başka bir Azure AD kuruluşunda zaten doğrulandıysa, özel bir etki alanı adı ekleyemezsiniz. Ancak, Azure AD 'de yönetilen kuruluşunuzda, yönetilmeyen bir kuruluştan dışarıdan bir dış yönetici tarafından yararlanalabilirsiniz. Genel yordam, [Azure AD 'ye özel etki alanı ekleme](../fundamentals/add-custom-domain.md)makalesini izler.

Etki alanı adının sahipliğini doğruladıktan sonra, Azure AD etki alanı adını yönetilmeyen kuruluştan kaldırır ve mevcut kuruluşunuza taşır. Yönetilmeyen bir dizinin dışarıdan yönetimi, iç yönetici tarafından aynı DNS TXT doğrulama işlemini gerektirir. Bunun farkı, aşağıdakilerin etki alanı adı ile de taşınabileceği bir addır:

- Kullanıcılar
- Abonelikler
- Lisans atamaları

### <a name="support-for-external-admin-takeover"></a>Dış yönetici için destek
Dış yönetici yönetim aşağıdaki çevrimiçi hizmetler tarafından desteklenir:

- Azure Hak Yönetimi
- Exchange Online

Desteklenen hizmet planları şunları içerir:

- Ücretsiz PowerApps
- PowerFlow ücretsiz
- Kişiler için RMS
- Microsoft Stream
- Dynamics 365 ücretsiz deneme sürümü

Dış yönetici, SharePoint, OneDrive veya Skype Kurumsal içeren hizmet planlarına sahip herhangi bir hizmette desteklenmez; Örneğin, Office ücretsiz aboneliği aracılığıyla. 

İsteğe bağlı olarak, etki alanı adını yönetilmeyen kuruluştan kaldırmak ve istenen kuruluşta doğrulamak için [ **Forcetakeover** seçeneğini](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) kullanabilirsiniz. 

#### <a name="more-information-about-rms-for-individuals"></a>Bireyler için RMS hakkında daha fazla bilgi

[Kişiler Için RMS](/azure/information-protection/rms-for-individuals), yönetilmeyen kuruluş sahip olduğunuz kuruluşla aynı bölgedeyse, otomatik olarak oluşturulan [Azure Information Protection kuruluş anahtarı](/azure/information-protection/plan-implement-tenant-key) ve [varsayılan koruma şablonları](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) da etki alanı adıyla birlikte taşınır.

Yönetilmeyen kuruluş farklı bir bölgedeyse, anahtar ve şablonlar üzerinden taşınmaz. Örneğin, yönetilmeyen kuruluş Avrupa 'daysa ve sahip olduğunuz kuruluş Kuzey Amerika.

Bireyler için RMS, korunan içeriği açmak üzere Azure AD kimlik doğrulamasını destekleyecek şekilde tasarlansa da, kullanıcıların da içeriği korumasını engellemez. Kullanıcılar, kişiler için RMS aboneliği ile içerik koruduktan sonra anahtar ve şablonlar üzerine taşınmadığından, bu içeriğe etki alanı devralındıktan sonra erişilemez.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>ForceTakeover seçeneği için Azure AD PowerShell cmdlet 'leri
[PowerShell örneğinde](#powershell-example)kullanılan bu cmdlet 'leri görebilirsiniz.

cmdlet | Kullanım
------- | -------
`connect-msolservice` | İstendiğinde, yönetilen kuruluşunuzda oturum açın.
`get-msoldomain` | Geçerli kuruluşla ilişkili etki alanı adlarınızı gösterir.
`new-msoldomain –name <domainname>` | Etki alanı adını kuruluşa doğrulanmamış olarak ekler (henüz bir DNS doğrulaması gerçekleştirilmedi).
`get-msoldomain` | Etki alanı adı artık yönetilen kuruluşunuzla ilişkili etki alanı adları listesine dahil edilmiştir, ancak **doğrulanmamış** olarak listelenir.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Etki alanı için yeni DNS TXT kaydına (MS = xxxxx) konacak bilgileri sağlar. Doğrulama, TXT kaydının yayılması biraz zaman alacağından, **-forcetakeover** seçeneğini düşünmeden önce birkaç dakika bekleyin. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Etki alanı adınız hala doğrulanmıyorsa, **-forcetakeover** seçeneğiyle devam edebilirsiniz. Bu, TXT kaydının oluşturulduğunu doğrular ve devralma işlemini devre dışı bırakır.<li>**-Forcetakeover** seçeneği, yönetilmeyen kuruluşun devralmayı engellediği Microsoft 365 hizmetleri olduğu durumlarda olduğu gibi, cmdlet 'e yalnızca bir dış yönetici tarafından zorlanırken eklenmelidir.
`get-msoldomain` | Etki alanı listesi artık etki alanı adını **doğrulanmış** olarak gösterir.

> [!NOTE]
> Yönetilmeyen Azure AD organizasyonu, dış devralma zorla seçeneğini gerçekleştirdikten sonra 10 gün sonra silinir.

### <a name="powershell-example"></a>PowerShell örneği

1. Self Servis sunumuna yanıt vermek için kullanılan kimlik bilgilerini kullanarak Azure AD 'ye bağlanın:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Etki alanlarının bir listesini alın:
  
   ```powershell
   Get-MsolDomain
   ```
3. Bir sınama oluşturmak için Get-MsolDomainVerificationDns cmdlet 'ini çalıştırın:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Örnek:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Bu komuttan döndürülen değeri (zorluk) kopyalayın. Örnek:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. Genel DNS ad alanında, önceki adımda kopyaladığınız değeri içeren bir DNS TXT kaydı oluşturun. Bu kaydın adı üst etki alanının adıdır, bu nedenle Windows Server 'dan DNS rolünü kullanarak bu kaynak kaydını oluşturursanız, kayıt adını boş bırakın ve yalnızca değeri metin kutusuna yapıştırın.
6. Sınamayı doğrulamak için Confirm-MsolDomain cmdlet 'ini çalıştırın:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Örnek:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

Başarılı bir sınama sizi hata olmadan isteme geri döndürür.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD’ye özel etki alanı adı ekleme](../fundamentals/add-custom-domain.md)
* [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure Cmdlet Başvurusu](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0&preserve-view=true)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
