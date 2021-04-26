---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 6d95e40623f17a39145778a2fc067dccc68fd872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95560178"
---
## <a name="steps-to-enable-single-sign-on"></a>Çoklu oturum açmayı etkinleştirme adımları
Bulut sağlama, çoklu oturum açma ile birlikte kullanılabilir.  Şu anda aracı yüklendiğinde SSO 'yu etkinleştirmek için bir seçenek yoktur, ancak aşağıdaki adımları kullanarak SSO 'yu etkinleştirebilir ve kullanabilirsiniz. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>1. Adım: Azure AD Connect dosyaları Indirme ve ayıklama
1.  İlk olarak, [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) en son sürümünü indirin
2.  Yönetici ayrıcalıklarını kullanarak bir komut istemi açın ve yeni indirdiğiniz MSI 'ye gidin.
3.  Aşağıdakileri çalıştırın:  `msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. FilePath ve extractfolder öğesini dosya yolunuza ve ayıklama klasörünüzün adına uyacak şekilde değiştirin.  İçerik artık ayıklama klasöründe olmalıdır.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>2. Adım: sorunsuz SSO PowerShell modülünü Içeri aktarma

1. [Azure AD PowerShell](/powershell/azure/active-directory/overview)'i indirin ve yükleyin.
2. `%programfiles%\Microsoft Azure Active Directory Connect`Klasöre gidin.
3. Bu komutu kullanarak sorunsuz SSO PowerShell modülünü içeri aktarın: `Import-Module .\AzureADSSO.psd1` .

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>3. Adım: sorunsuz SSO 'nun etkinleştirildiği Active Directory ormanların listesini alın

1. PowerShell'i yönetici olarak çalıştırın. PowerShell 'de, çağırın `New-AzureADSSOAuthenticationContext` . İstendiğinde, kiracınızın genel yönetici kimlik bilgilerini girin.
2. Çağrısı yapın `Get-AzureADSSOStatus` . Bu komut, bu özelliğin etkinleştirildiği Active Directory ormanların listesini ("etki alanları" listesine bakın) sağlar.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>4. Adım: her Active Directory orman için sorunsuz SSO 'yu etkinleştirme

1. Çağrısı yapın `Enable-AzureADSSOForest` . İstendiğinde, hedeflenen Active Directory ormanın etki alanı yönetici kimlik bilgilerini girin.

   > [!NOTE]
   >Etki alanı yöneticisi kimlik bilgileri Kullanıcı adı, SAM hesap adı biçiminde girilmelidir (contoso\johntikan veya contoso. com\johntikan). DNS kullanarak etki alanı yöneticisinin etki alanı denetleyicisini bulmak için Kullanıcı adının etki alanı bölümünü kullanıyoruz.

   >[!NOTE]
   >Kullanılan etki alanı yönetici hesabı, protected Users grubunun bir üyesi olmamalıdır. Öyleyse, işlem başarısız olur.

2. Özelliği ayarlamak istediğiniz her Active Directory ormanı için önceki adımı yineleyin.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>5. Adım. Kiracınızda özelliği etkinleştirin

Kiracınızdaki özelliği açmak için çağırın `Enable-AzureADSSO -Enable $true` .