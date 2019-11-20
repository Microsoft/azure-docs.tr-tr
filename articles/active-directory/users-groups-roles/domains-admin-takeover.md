---
title: Yönetilmeyen bir dizinin Yöneticisi-Azure AD | Microsoft Docs
description: Yönetilmeyen bir Azure AD kuruluşunda (gölge kiracı) bir DNS etki alanı adı alma.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a0697e151c50b9722fef908eeb2c7498503b8c0
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027382"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Azure Active Directory yönetilmeyen bir dizinden yönetici olarak yararlanın

Bu makalede, Azure Active Directory (Azure AD) içinde yönetilmeyen bir dizindeki DNS etki alanı adını almanın iki yolu açıklanmaktadır. Bir self servis kullanıcısı, Azure AD kullanan bir bulut hizmetine kaydolduğunda bu kullanıcı, e-posta etki alanına göre yönetilmeyen bir Azure AD dizinine eklenir. Self servis veya "viral" bir hizmete kaydolma hakkında daha fazla bilgi için bkz. [Azure Active Directory için self servis kaydolma nedir?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Yönetilmeyen bir dizini nasıl almak istediğinize karar verin
Yönetici devralma işlemi sırasında, [Azure AD’ye özel etki alanı adı ekleme](../fundamentals/add-custom-domain.md) bölümünde açıklandığı gibi sahipliği kanıtlayabilirsiniz. Sonraki bölümlerde, yönetici deneyimi daha ayrıntılı şekilde açıklanmaktadır, ancak bir özeti aşağıda verilmiştir:

* Yönetilmeyen bir Azure dizininin ["iç" yönetici devralma işlemini](#internal-admin-takeover) gerçekleştirdiğinizde, yönetilmeyen dizinin genel yöneticisi olarak eklendiniz. Herhangi bir kullanıcı, etki alanı veya hizmet planı, yönettiğiniz diğer dizine geçirilmiyor.

* Yönetilmeyen bir Azure dizininin ["dış" yönetici devralma işlemini](#external-admin-takeover) gerçekleştirdiğinizde, yönetilmeyen dizininizin DNS etki alanı adını, yönetilen Azure dizininize eklersiniz. Etki alanı adını eklediğinizde, kullanıcıların kesinti olmadan hizmetlere erişmeye devam edebilmesi için yönetilen Azure dizininizde, kullanıcıların kaynaklara bir eşlemesi oluşturulur. 

## <a name="internal-admin-takeover"></a>İç yönetici yönetim

SharePoint ve OneDrive içeren bazı ürünler (Office 365 gibi) dışarıdan devralmayı desteklemez. Bu senaryonuz varsa veya bir yöneticileriniz varsa ve self servis kaydolma kullanan kullanıcılar tarafından yönetilmeyen ya da "gölge" kiracıyı almak istiyorsanız bunu bir iç yönetici ile yapabilirsiniz.

1. Power BI için kaydolduktan sonra yönetilmeyen kiracıda bir kullanıcı bağlamı oluşturun. Örneğin, bu adımlar bu yolu kabul eder.

2. [Power BI sitesini](https://powerbi.com) açın ve **ücretsiz Başlat**' ı seçin. Kuruluşun etki alanı adını kullanan bir kullanıcı hesabı girin; Örneğin, `admin@fourthcoffee.xyz`. Doğrulama kodunu girdikten sonra, onay kodu için e-postanızı kontrol edin.

3. Power BI 'den onay e-postasında, **Evet '** i seçin.

4. [Microsoft 365 Yönetim merkezinde](https://portal.office.com/admintakeover) Power BI kullanıcı hesabıyla oturum açın. Yönetilmeyen kiracıda zaten doğrulanmış olan etki alanı adının **yöneticisi olmak** üzere size yönlendiren bir ileti alırsınız. **Evet, yönetici olmak istiyorum ' u**seçin.
  
   ![Yönetici olacak ilk ekran görüntüsü](./media/domains-admin-takeover/become-admin-first.png)
  
5. Etki alanı adı kayıt şirketinizde **mathcoffee. xyz** etki alanı adına sahip olduğunuzu KANıTLAMAK için TXT kaydını ekleyin. Bu örnekte, GoDaddy.com ' dir.
  
   ![Etki alanı adı için bir TXT kaydı ekleyin](./media/domains-admin-takeover/become-admin-txt-record.png)

DNS TXT kayıtları, etki alanı adı kaydedicisinde doğrulandığında Azure AD kiracısını yönetebilirsiniz.

Yukarıdaki adımları tamamladığınızda, artık Office 365 ' de dördüncü kahve kiracının genel yöneticisi olursunuz. Etki alanı adını diğer Azure hizmetlerinize tümleştirmek için, bunu Office 365 'den kaldırabilir ve Azure 'da farklı bir yönetilen kiracıya ekleyebilirsiniz.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Azure AD 'de yönetilen bir kiracıya etki alanı adı ekleme

1. [Microsoft 365 Yönetim merkezini](https://admin.microsoft.com)açın.
2. **Kullanıcılar** sekmesini seçin ve özel etki alanı adı kullanmayan *Kullanıcı\@fourthcoffeexyz.onmicrosoft.com* gibi bir ada sahip yeni bir kullanıcı hesabı oluşturun. 
3. Yeni Kullanıcı hesabının Azure AD kiracısı için genel yönetici ayrıcalıklarına sahip olduğundan emin olun.
4. Microsoft 365 Yönetim merkezinde **etki alanları** sekmesini açın, etki alanı adını seçin ve **Kaldır**' ı seçin. 
  
   ![etki alanı adını Office 365 ' dan kaldırma](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Office 365 ' de kaldırılan etki alanı adına başvuran herhangi bir kullanıcı veya grup varsa,. onmicrosoft.com etki alanına yeniden adlandırılması gerekir. Etki alanı adını silmeye zorlarsanız, bu örnekte *kullanıcı\@fourthcoffeexyz.onmicrosoft.com*için tüm kullanıcılar otomatik olarak yeniden adlandırılır.
  
6. Azure AD kiracısı için genel yönetici olan bir hesapla [Azure AD Yönetim merkezinde](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) oturum açın.
  
7. **Özel etki alanı adları**' nı seçin ve ardından etki alanı adını ekleyin. Etki alanı adının sahipliğini doğrulamak için DNS TXT kayıtlarını girmeniz gerekir. 
  
   ![Azure AD 'ye eklenen etki alanı doğrulandı](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Office 365 kiracısında atanmış lisanslarına sahip Power BI veya Azure Rights Management hizmeti kullanıcılarının, etki alanı adı kaldırılırsa panoları kaydetmesi gerekir. Kullanıcı *\@fourthcoffeexyz.onmicrosoft.com* gibi kullanıcı adıyla oturum açması gerekir, *mathcoffee. xyz Kullanıcı\@*

## <a name="external-admin-takeover"></a>Dış yönetici

Zaten Azure hizmetleri veya Office 365 ile bir kiracı yönetiyorsanız, başka bir Azure AD kiracısında zaten doğrulandıysa, özel bir etki alanı adı ekleyemezsiniz. Ancak, Azure AD 'de yönetilen kiracınızdan, yönetilmeyen bir kiracının dışarıdan bir dış yönetici tarafından ele geçirmesine sağlayabilirsiniz. Genel yordam, [Azure AD 'ye özel etki alanı ekleme](../fundamentals/add-custom-domain.md)makalesini izler.

Etki alanı adının sahipliğini doğruladıktan sonra, Azure AD etki alanı adını yönetilmeyen kiracıdan kaldırır ve mevcut kiracınıza taşır. Yönetilmeyen bir dizinin dışarıdan yönetimi, iç yönetici tarafından aynı DNS TXT doğrulama işlemini gerektirir. Bunun farkı, aşağıdakilerin etki alanı adı ile de taşınabileceği bir addır:

- Kullanıcılar
- Abonelikler
- Lisans atamaları

### <a name="support-for-external-admin-takeover"></a>Dış yönetici için destek
Dış yönetici yönetim aşağıdaki çevrimiçi hizmetler tarafından desteklenir:

- Azure Rights Management
- Exchange Online

Desteklenen hizmet planları şunları içerir:

- Ücretsiz PowerApps
- PowerFlow ücretsiz
- Bireyler için RMS
- Microsoft Stream
- Dynamics 365 ücretsiz deneme sürümü

Dış yönetici, SharePoint, OneDrive veya Skype Kurumsal içeren hizmet planlarına sahip herhangi bir hizmette desteklenmez; Örneğin, Office ücretsiz aboneliği aracılığıyla. 

İsteğe bağlı olarak, etki alanı adını yönetilmeyen kiracıdan kaldırmak ve istenen kiracı üzerinde doğrulamak için [ **Forcetakeover** seçeneğini](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) kullanabilirsiniz. 

#### <a name="more-information-about-rms-for-individuals"></a>Bireyler için RMS hakkında daha fazla bilgi

[Kişiler Için RMS](/azure/information-protection/rms-for-individuals), yönetilmeyen kiracı sahip olduğunuz kiracı ile aynı bölgedeyse, otomatik olarak oluşturulan [Azure Information Protection kiracı anahtarı](/azure/information-protection/plan-implement-tenant-key) ve [varsayılan koruma şablonları](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) da etki alanı adıyla birlikte taşınır.

Yönetilmeyen kiracı farklı bir bölgedeyse, anahtar ve şablonlar üzerinden taşınmaz. Örneğin, yönetilmeyen kiracı Avrupa 'daysa ve sahip olduğunuz kuruluş Kuzey Amerika.

Bireyler için RMS, korunan içeriği açmak üzere Azure AD kimlik doğrulamasını destekleyecek şekilde tasarlansa da, kullanıcıların da içeriği korumasını engellemez. Kullanıcılar, kişiler için RMS aboneliği ile içerik koruduktan sonra anahtar ve şablonlar üzerine taşınmadığından, bu içeriğe etki alanı devralındıktan sonra erişilemez.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>ForceTakeover seçeneği için Azure AD PowerShell cmdlet 'leri
[PowerShell örneğinde](#powershell-example)kullanılan bu cmdlet 'leri görebilirsiniz.

cmdlet | Kullanım
------- | -------
`connect-msolservice` | İstendiğinde, yönetilen kiracınızda oturum açın.
`get-msoldomain` | Geçerli kiracı ile ilişkili etki alanı adlarınızı gösterir.
`new-msoldomain –name <domainname>` | Etki alanı adını kiracıya doğrulanmamış olarak ekler (henüz DNS doğrulaması gerçekleştirilmedi).
`get-msoldomain` | Etki alanı adı artık yönetilen kiracınızla ilişkili etki alanı adları listesine dahil edilmiştir, ancak **doğrulanmamış**olarak listelenir.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Etki alanı için yeni DNS TXT kaydına (MS = xxxxx) konacak bilgileri sağlar. Doğrulama, TXT kaydının yayılması biraz zaman alacağından, **-forcetakeover** seçeneğini düşünmeden önce birkaç dakika bekleyin. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Etki alanı adınız hala doğrulanmıyorsa, **-forcetakeover** seçeneğiyle devam edebilirsiniz. Bu, TXT kaydının oluşturulduğunu doğrular ve devralma işlemini devre dışı bırakır.<li>**-Forcetakeover** seçeneği, yalnızca, yönetilmeyen kiracı tarafından devralmayı engelleyen Office 365 hizmetlerine sahip olduğu durumlarda olduğu gibi, cmdlet 'e yalnızca bir dış yönetici tarafından zorlanırken eklenmelidir.
`get-msoldomain` | Etki alanı listesi artık etki alanı adını **doğrulanmış**olarak gösterir.

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
3. Bir sınama oluşturmak için Get-Msoldomaindoğrulamaları Icationdns cmdlet 'ini çalıştırın:
   ```powershell
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Bu komuttan döndürülen değeri (zorluk) kopyalayın. Örneğin:
   ```powershell
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. Genel DNS ad alanında, önceki adımda kopyaladığınız değeri içeren bir DNS TXT kaydı oluşturun. Bu kaydın adı üst etki alanının adıdır, bu nedenle Windows Server 'dan DNS rolünü kullanarak bu kaynak kaydını oluşturursanız, kayıt adını boş bırakın ve yalnızca değeri metin kutusuna yapıştırın.
6. Sınamayı doğrulamak için Onayla-MsolDomain cmdlet 'ini çalıştırın:
  
   ```powershell
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
   ```
  
   Örneğin:
  
   ```powershell
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
   ```

Başarılı bir sınama sizi hata olmadan isteme geri döndürür.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD 'ye özel etki alanı adı ekleme](../fundamentals/add-custom-domain.md)
* [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet Başvurusu](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
