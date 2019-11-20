---
title: Azure Active Directory | grup adlandırma ilkesini zorla | Microsoft Docs
description: Azure Active Directory 'de Office 365 grupları için adlandırma ilkesi ayarlama
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3a9300148f4ac2adf6b95ef0afb500af5bc9284
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027047"
---
# <a name="enforce-a-naming-policy-on-office-365-groups-in-azure-active-directory"></a>Azure Active Directory 'de Office 365 gruplarında bir adlandırma ilkesi zorlaması

Kullanıcılarınız tarafından oluşturulan veya düzenlenen Office 365 grupları için tutarlı adlandırma kuralları zorlamak için, Azure Active Directory (Azure AD) içinde kiracılar için bir grup adlandırma ilkesi ayarlayın. Örneğin, bir grup, üyelik, coğrafi bölge ya da grubu oluşturan bir işlev ile iletişim kurmak için adlandırma ilkesini kullanabilirsiniz. Ayrıca, adres defterindeki grupların sınıflandırmasına yardımcı olması için adlandırma ilkesini de kullanabilirsiniz. İlkeyi, belirli sözcüklerin Grup adlarında ve diğer adlarla kullanılmasını engellemek için kullanabilirsiniz.

> [!IMPORTANT]
> Office 365 grupları için Azure AD adlandırma ilkesi kullanmak, bir veya daha fazla Office 365 grubunun üyesi olan her benzersiz kullanıcı için Azure Active Directory Premium P1 lisansı veya Azure AD Temel EDU lisansı atamanız gerekir.

Adlandırma ilkesi, iş yükleri genelinde oluşturulan grupları oluşturmaya veya düzenlemesine (örneğin, Outlook, Microsoft ekipleri, SharePoint, Exchange veya Planner) uygulanır. Hem Grup adına hem de grup diğer adına uygulanır. Azure AD 'de Adlandırma ilkenizi ayarlarsanız ve mevcut bir Exchange grup adlandırma ilkeniz varsa, kuruluşunuzda Azure AD adlandırma ilkesi zorlanır.

## <a name="naming-policy-features"></a>Adlandırma ilkesi özellikleri

Gruplar için adlandırma ilkesini iki farklı şekilde zorunlu kılabilirsiniz:

- **Ön ek-sonek adlandırma ilkesi** Gruplandırmada bir adlandırma kuralını zorlamak için otomatik olarak eklenen ön ekleri veya son ekleri tanımlayabilir (örneğin, Grup adı "GRP\_Japonya\_grupum\_Mühendisliği", GRP\_Japonya\_ önek ve \_Mühendisliği sonektir). 

- **Özel engellenen sözcükler** Kullanıcılar tarafından oluşturulan gruplarda engellenecek bir engellenen sözcük kümesini (örneğin, "CEO, bordro, HR") karşıya yükleyebilirsiniz.

### <a name="prefix-suffix-naming-policy"></a>Ön ek-sonek adlandırma ilkesi

Adlandırma kuralının genel yapısı ' prefix [GroupName] sonekidir '. Birden çok önek ve sonek tanımlayabilmeniz sırasında, ayarında yalnızca bir [GroupName] örneği olabilir. Ön ekler veya sonekler, grubu oluşturan kullanıcıya göre değiştirilen \[departmanı\] gibi sabit dizeler ya da Kullanıcı öznitelikleri olabilir. Önek ve sonek dizeleriniz için izin verilen toplam karakter sayısı 53 karakterdir. 

Ön ekler ve sonekler, Grup adı ve grup diğer adında desteklenen özel karakterler içerebilir. Önek veya Sonekte, grup diğer adında desteklenmeyen herhangi bir karakter hala Grup adında uygulanır, ancak grup diğer adından kaldırılır. Bu kısıtlama nedeniyle, Grup adına uygulanan ön ekler ve sonekler, grup diğer adına uygulandıklarından farklı olabilir. 

#### <a name="fixed-strings"></a>Sabit dizeler

Genel adres listesinde ve grup iş yüklerinin sol gezinti bağlantılarında grupları taramayı ve ayırt edilmesini kolaylaştırmak için dizeleri kullanabilirsiniz. Ortak öneklerden bazıları ' GRP\_adı ', '\#adı ', '\_Name ' gibi anahtar kelimelerdir

#### <a name="user-attributes"></a>Kullanıcı öznitelikleri

Kullanıcılarınızın, grubun oluşturulduğu departmanı, ofisi veya coğrafi bölgeyi belirlemesine yardımcı olabilecek öznitelikleri kullanabilirsiniz. Örneğin, Adlandırma ilkenizi `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"`olarak tanımlayabilir ve `User’s department = Engineering`, bir grup adı "GRP My Group Mühendisliği" olabilir. Desteklenen Azure AD öznitelikleri \[departman\], \[şirket\], \[Office\], \[Stateoril\], \[CountryOrRegion\]\[başlık\]. Desteklenmeyen Kullanıcı öznitelikleri sabit dizeler olarak kabul edilir; Örneğin, "\[PostaKodu\]". Uzantı öznitelikleri ve özel öznitelikler desteklenmez.

Kuruluşunuzdaki tüm kullanıcılar için doldurulmuş değerleri olan öznitelikleri kullanmanızı ve uzun değerleri olan öznitelikleri kullanmemenizi öneririz.

### <a name="custom-blocked-words"></a>Özel engellenen sözcükler

Engellenen bir sözcük listesi, Grup adlarında ve diğer adlarla engellenecek deyimlerin virgülle ayrılmış listesidir. Hiçbir alt dize araması yapılmaz. Bir hata tetiklenmesi için Grup adı ve bir veya daha fazla özel engellenen sözcükten tam eşleşme gereklidir. ' Lass ' engellenmiş bir sözcük olsa bile, kullanıcıların ' class ' gibi ortak kelimeleri kullanabilmesi için alt dize araması yapılmaz.

Engellenen sözcük listesi kuralları:

- Engellenen kelimeler büyük/küçük harfe duyarlı değildir.
- Bir Kullanıcı bir grup adının parçası olarak engellenen bir sözcüğe girdiğinde engellenen sözcüğe sahip bir hata iletisi görürler.
- Engellenen sözcüklerde hiçbir karakter kısıtlaması yok.
- Engellenen kelimeler listesinde yapılandırılabilen 5000 tümceciklerin üst sınırı vardır. 

### <a name="administrator-override"></a>Yönetici geçersiz kılma

Seçili Yöneticiler, tüm grup iş yükleri ve uç noktalarında bu ilkelerden muaf tutulur. böylece, engellenen kelimeleri ve kendi adlandırma kurallarını kullanarak gruplar oluşturabilirler. Grup adlandırma ilkesinden muaf tutulan Yönetici rollerinin listesi aşağıda verilmiştir.

- Genel yönetici
- İş ortağı katman 1 desteği
- İş ortağı katman 2 desteği
- Kullanıcı Yöneticisi
- Dizin yazarları

## <a name="configure-naming-policy-in-azure-portal"></a>Azure portal adlandırma ilkesini yapılandırma

1. [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) genel yönetici hesabıyla oturum açın.
1. **Gruplar**' ı seçin ve adlandırma ilkesi sayfasını açmak için **adlandırma ilkesi** ' ni seçin.

    ![Yönetim merkezinde adlandırma ilkesi sayfasını açın](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Ön ek adlandırma ilkesini görüntüleme veya düzenleme

1. **Adlandırma ilkesi** sayfasında **grup adlandırma ilkesi**' ni seçin.
1. Adlandırma ilkesinin bir parçası olarak zorlamak istediğiniz öznitelikleri veya dizeleri seçerek geçerli ön eki veya sonek adlandırma ilkelerini tek tek görüntüleyebilir veya düzenleyebilirsiniz.
1. Listeden bir ön eki veya soneki kaldırmak için ön eki veya soneki seçin, sonra **Sil**' i seçin. Aynı anda birden çok öğe silinebilir.
1. Yeni ilkeye ilişkin değişikliklerinizi kaydederek **Kaydet**' i seçerek devreye geçin.

### <a name="edit-custom-blocked-words"></a>Özel engellenen sözcükleri Düzenle

1. **Adlandırma ilkesi** sayfasında, **Engellenen sözcükler**' i seçin.

    ![adlandırma ilkesi için engellenen sözcükler listesini düzenle ve karşıya yükle](./media/groups-naming-policy/blockedwords.png)

1. **İndir**' i seçerek özel engellenen sözcüklerin geçerli listesini görüntüleyin veya düzenleyin.
1. Dosya simgesini seçerek özel engellenen sözcüklerin yeni listesini karşıya yükleyin.
1. Yeni ilkeye ilişkin değişikliklerinizi kaydederek **Kaydet**' i seçerek devreye geçin.

## <a name="install-powershell-cmdlets"></a>PowerShell cmdlet'lerini yükleme

PowerShell komutlarını çalıştırmadan önce Windows PowerShell Graph için Azure Active Directory PowerShell Modülünün eski sürümlerini kaldırın ve [Graph için Azure Active Directory PowerShell - Genel Önizleme Sürümünü 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) yükleyin.

1. Windows PowerShell uygulamasını yönetici olarak açın.
2. Eski AzureADPreview sürümlerini kaldırın.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   ```

3. En son AzureADPreview sürümünü yükleyin.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```

   Güvenilmeyen bir depoya erişmek isteyip istemediğiniz sorulursa **Y**girin. Yeni modülün yüklenmesi birkaç dakika sürebilir.

## <a name="configure-naming-policy-in-powershell"></a>PowerShell 'de adlandırma ilkesini yapılandırma

1. Bilgisayarınızda bir Windows PowerShell penceresi açın. Bunu yükseltilmiş ayrıcalıklar olmadan açabilirsiniz.

1. Ortamı cmdlet'leri çalıştırmaya hazır hale getirmek için aşağıdaki komutları çalıştırın.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   Açılan **Hesabınızda oturum açın** ekranında hizmetinizle bağlantı kurmak için yönetici hesabınızın adını ve parolasını girin **Oturum aç**'ı seçin.

1. Bu kiracının grup ayarlarını oluşturmak için [Grup ayarlarını yapılandırmak için Azure Active Directory cmdlet'leri](groups-settings-cmdlets.md) adımlarını izleyin.

### <a name="view-the-current-settings"></a>Geçerli ayarları görüntüleyin

1. Geçerli ayarları görüntülemek için geçerli adlandırma ilkesini getirin.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Geçerli grup ayarlarını görüntüleyin.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Adlandırma ilkesini ve özel engellenen sözcükleri ayarla

1. Azure AD PowerShell'de grup adı ön ve son eklerini ayarlayın. Özelliğin düzgün çalışması için, [GroupName] ayarına eklenmelidir.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Sınırlamak istediğiniz özel engellenen sözcükleri belirleyin. Aşağıdaki örnekte kendi özel sözcüklerinizi ekleme adımları gösterilmektedir.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Aşağıdaki örnekte olduğu gibi, yeni ilke için ayarları geçerli olacak şekilde kaydedin.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
İşte bu kadar. Adlandırma ilkenizi ayarlamış ve engellenen sözcüklerinizi eklediniz.

## <a name="export-or-import-custom-blocked-words"></a>Özel engellenen sözcükleri dışarı veya içeri aktarma

Daha fazla bilgi için, [Grup ayarlarını yapılandırmaya yönelik cmdlet 'leri Azure Active Directory](groups-settings-cmdlets.md)makalesine bakın.

Aşağıda, birden fazla engellenen sözcüğü dışarı aktarmak için bir PowerShell betiğine örnek verilmiştir:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Aşağıda, birden fazla engellenen sözcüğü içeri aktarmaya yönelik örnek bir PowerShell betiği verilmiştir:

``` PowerShell
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
```

## <a name="remove-the-naming-policy"></a>Adlandırma ilkesini kaldır

### <a name="remove-the-naming-policy-using-azure-portal"></a>Azure portal kullanarak adlandırma ilkesini kaldırma

1. **Adlandırma ilkesi** sayfasında **ilkeyi Sil**' i seçin.
1. Silme işlemini doğruladıktan sonra, tüm önek-sonek adlandırma ilkesi ve özel engellenen sözcükler dahil olmak üzere, adlandırma ilkesi kaldırılır.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Azure AD PowerShell kullanarak adlandırma ilkesini kaldırma

1. Azure AD PowerShell 'de grup adı öneklerini ve soneklerini boşaltın.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Özel engellenen kelimeleri boşaltın.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Ayarları kaydedin.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-office-365-apps"></a>Office 365 uygulamaları genelinde deneyim

Azure AD 'de bir grup adlandırma ilkesi ayarladıktan sonra, bir Kullanıcı Office 365 uygulamasında bir grup oluşturduğunda şunları görürler:

- Adlandırma ilkenize göre adın Önizlemesi (ön ekler ve son ekler), Kullanıcı Grup adında her türdedir
- Kullanıcı engellenen sözcükleri girerse, engellenen kelimeleri kaldırabilmeleri için bir hata iletisi görür.

İş yükü | Uyumluluk
----------- | -------------------------------
Azure Active Directory portalları | Azure AD portalı ve erişim paneli portalı, Kullanıcı bir grup oluştururken veya düzenlenirken bir grup adı yazdığında adlandırma ilkesi Zorlanmış adı gösterir. Kullanıcı özel engellenen bir sözcüğe girdiğinde, kullanıcının kaldırabilmesi için engellenen sözcüğe sahip bir hata mesajı görüntülenir.
Outlook Web Erişimi (OWA) | Kullanıcı bir grup adı veya grup diğer adı yazdığında Outlook Web Erişimi adlandırma ilkesi tarafından zorlanan adı gösterir. Bir kullanıcı özel engellenen bir sözcüğe girdiğinde, Kullanıcı arabirimini kaldırabilmesi için engellenen sözcükle birlikte Kullanıcı arabiriminde bir hata iletisi gösterilir.
Outlook masaüstü | Outlook masaüstünde oluşturulan gruplar, adlandırma ilkesi ayarlarıyla uyumludur. Outlook masaüstü uygulaması henüz zorlanan grup adının önizlemesini göstermez ve Kullanıcı Grup adına girdiğinde özel engellenen Word hatalarını döndürmez. Ancak, bir grup oluştururken veya düzenlenirken adlandırma ilkesi otomatik olarak uygulanır ve Grup adında veya diğer adda özel engellenen sözcükler varsa, kullanıcılar hata iletileri görür.
Microsoft Teams | Kullanıcı bir takım adı girdiğinde Microsoft ekipleri grup adlandırma ilkesi 'nin zorunlu kılınan adını gösterir. Kullanıcı özel engellenen bir sözcüğe girdiğinde, engellenen sözcükle birlikte kullanıcının kaldırabilmesi için bir hata iletisi gösterilir.
SharePoint  |  Kullanıcı bir site adı veya Grup e-posta adresi yazdığında, SharePoint adlandırma ilkesi tarafından zorlanan adı gösterir. Kullanıcı özel engellenen bir sözcüğe girdiğinde, engellenen sözcükle birlikte kullanıcının kaldırabilmesi için bir hata iletisi gösterilir.
Microsoft Stream | Microsoft Stream, Kullanıcı bir grup adı veya Grup e-posta diğer adı yazdığında grup adlandırma ilkesi Zorlanmış adı gösterir. Kullanıcı özel engellenen bir sözcüğe girdiğinde, engellenen sözcükle bir hata iletisi gösterilir, böylece Kullanıcı bunu kaldırabilir.
Outlook iOS ve Android uygulaması | Outlook uygulamalarında oluşturulan gruplar, yapılandırılan adlandırma ilkesiyle uyumludur. Outlook Mobile App henüz adlandırma ilkesi zorlanan adının önizlemesini göstermez ve Kullanıcı Grup adına girdiğinde özel engellenen Word hataları döndürmez. Ancak, ad ilkesi otomatik olarak oluştur/Düzenle öğesine tıklandıktan sonra Grup adında veya diğer adda özel engellenen sözcükler varsa hata iletilerini görürler.
Mobil uygulamayı gruplar | Gruplar mobil uygulamasında oluşturulan gruplar, adlandırma ilkesiyle uyumludur. Gruplar mobil uygulama, adlandırma ilkesinin önizlemesini göstermez ve Kullanıcı Grup adına girdiğinde özel engellenen Word hataları döndürmez. Ancak ad ilkesi, grup oluştururken veya düzenlenirken otomatik olarak uygulanır ve Grup adında veya diğer adda özel engellenen sözcükler varsa, kullanıcılar uygun hatalarla sunulur.
Planner | Planner, adlandırma ilkesiyle uyumludur. Planner, plan adını girerken adlandırma ilkesi önizlemesini gösterir. Kullanıcı özel engellenen bir sözcüğe girdiğinde, plan oluşturulurken bir hata iletisi gösterilir.
Müşteri Etkileşimi için Dynamics 365 | Müşteri katılımı için Dynamics 365, adlandırma ilkesiyle uyumludur. Dynamics 365, Kullanıcı bir grup adı veya Grup e-posta diğer adı yazdığında, adlandırma ilkesi tarafından zorlanan adı gösterir. Kullanıcı özel engellenen bir sözcüğe girdiğinde, kullanıcının kaldırabilmesi için engellenen sözcükle bir hata iletisi gösterilir.
Okul veri eşitleme (SDS) | SDS ile oluşturulan gruplar, adlandırma ilkesiyle uyumlu, ancak adlandırma ilkesi otomatik olarak uygulanmaz. SDS yöneticileri, grupların oluşturulması gereken sınıf adlarına ön ekleri ve sonekleri eklemek ve ardından SDS 'ye yüklenmesi gerekir. Grup oluşturma veya düzenleme işlemi, aksi takdirde başarısız olur.
Outlook Müşteri Yöneticisi (OCM) | Outlook müşteri yöneticisi, Outlook müşteri yöneticisi 'nde oluşturulan gruba otomatik olarak uygulanan adlandırma ilkesiyle uyumludur. Özel engellenen bir sözcük algılanırsa, OCM 'de Grup oluşturma engellenir ve kullanıcının OCM uygulamasını kullanımı engellenir.
Sınıf uygulaması | Sınıf uygulamasında oluşturulan gruplar, adlandırma ilkesiyle uyumlu değildir, ancak adlandırma ilkesi otomatik olarak uygulanmaz ve bir sınıf grubu adı girerken, adlandırma ilkesi önizlemesi kullanıcılara gösterilmez. Kullanıcılar, ön ekler ve sonekler ile zorlanan derslik grubu adını girmelidir. Aksi takdirde, sınıf grubu oluşturma veya düzenleme işlemi hatalarla başarısız olur.
Power BI | Power BI çalışma alanları, adlandırma ilkesiyle uyumludur.    
Yammer | Yammer 'da Azure Active Directory hesabıyla oturum açmış bir Kullanıcı bir grup oluşturur veya bir grup adını düzenlediğinde, Grup adı adlandırma ilkesiyle uyumlu olur. Bu, hem Office 365 bağlı grupları hem de diğer tüm Yammer grupları için geçerlidir.<br>Adlandırma ilkesi gerçekleşmeden önce Office 365 bağlantılı bir grup oluşturulduysa, Grup adı adlandırma ilkelerini otomatik olarak takip etmez. Kullanıcı, Grup adını düzenlediklerinde, ön eki ve soneki eklemesi istenir.
StaffHub  | Çalışan Merkez takımları, adlandırma ilkesini takip etmez, ancak temel alınan Office 365 Grubu bunu yapar. Ekip merkezi takım adı, önekleri ve sonekleri uygulamaz ve özel engellenen kelimeleri denetlemez. Ancak, çalışan Merkez, ön ekleri ve sonekleri uygular ve engellenen kelimeleri temel Office 365 grubundan kaldırır.
Exchange PowerShell | Exchange PowerShell cmdlet 'leri adlandırma ilkesiyle uyumludur. Kullanıcılar, önerilen ön ekler ve son ekler ve grup adı ve grup diğer adında (Mailrumuz) adlandırma ilkesini izolmadıkları takdirde özel engellenen sözcükler için uygun hata iletileri alırlar.
Azure Active Directory PowerShell cmdlet 'leri | Azure Active Directory PowerShell cmdlet 'leri adlandırma ilkesiyle uyumludur. Kullanıcılar, önerilen ön ekler ve sonekler ve grup adları ve grup diğer adında adlandırma kuralını izolmadıkları takdirde özel engellenen sözcükler için uygun hata iletileri alır.
Exchange Yönetim Merkezi | Exchange Yönetim Merkezi, adlandırma ilkesiyle uyumludur. Kullanıcılar, önerilen ön ekler ve son ekler ve grup adı ve grup diğer adındaki adlandırma kuralını izlerse, özel engellenen sözcükler için uygun hata iletileri alır.
Microsoft 365 Yönetim Merkezi | Microsoft 365 Yönetim Merkezi, adlandırma ilkesiyle uyumludur. Bir Kullanıcı Grup adlarını oluşturduğunda veya düzenlediklerinde, adlandırma ilkesi otomatik olarak uygulanır ve kullanıcılar, özel engellenen sözcükler girerken uygun hataları alırlar. Microsoft 365 Yönetim Merkezi henüz adlandırma ilkesinin önizlemesini göstermez ve Kullanıcı Grup adına girdiğinde özel engellenen Word hataları döndürmez.

## <a name="next-steps"></a>Sonraki adımlar

Bu makaleler, Azure AD grupları hakkında ek bilgiler sağlar.

- [Var olan grupları görme](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Office 365 grupları için süre sonu ilkesi](groups-lifecycle.md)
- [Bir grubun ayarlarını yönetme](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Bir grubun üyelerini yönetme](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Bir grubun üyeliklerini yönetme](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Bir gruptaki kullanıcılar için dinamik kuralları yönetme](groups-dynamic-membership.md)
