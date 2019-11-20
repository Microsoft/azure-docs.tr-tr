---
title: Azure AD Connect AD FS Yönetimi ve özelleştirmesi | Microsoft Docs
description: Azure AD Connect ve PowerShell ile Kullanıcı AD FS oturum açma deneyimini Azure AD Connect ve özelleştirmesiyle AD FS Yönetimi.
keywords: AD FS, ADFS, AD FS Yönetimi, AAD Connect, Bağlan, oturum açma, AD FS özelleştirme, onarım güveni, O365, Federasyon, bağlı olan taraf
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7249f2077666530964afa16ef47d69731cee846a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085220"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Active Directory Federasyon Hizmetleri (AD FS) Azure AD Connect kullanarak yönetin ve özelleştirin
Bu makalede Azure Active Directory (Azure AD) Connect kullanılarak Active Directory Federasyon Hizmetleri (AD FS) (AD FS) yönetimi ve özelleştirmeyi açıklanmaktadır. Ayrıca, bir AD FS grubunun tüm yapılandırması için yapmanız gerekebilecek diğer ortak AD FS görevlerini de içerir.

| Konu | Ne içerir? |
|:--- |:--- |
| **AD FS Yönet** | |
| [Güveni onarma](#repairthetrust) |Office 365 ile Federasyon güvenini onarma. |
| [Alternatif oturum açma KIMLIĞINI kullanarak Azure AD ile federasyon oluşturma](#alternateid) | Alternatif oturum açma KIMLIĞI kullanarak Federasyonu yapılandırma  |
| [AD FS sunucusu ekleme](#addadfsserver) |AD FS grubunu ek bir AD FS sunucusu ile genişletme. |
| [AD FS Web uygulaması ara sunucusu ekleme](#addwapserver) |Bir AD FS grubunu ek Web uygulaması ara sunucusu (WAP) sunucusu ile genişletme. |
| [Federasyon etki alanı ekleme](#addfeddomain) |Federasyon etki alanı ekleme. |
| [SSL sertifikasını güncelleştirme](how-to-connect-fed-ssl-update.md)| AD FS grubu için SSL sertifikasını güncelleştirme. |
| **AD FS özelleştirme** | |
| [Özel bir şirket logosu veya çizimi ekleme](#customlogo) |Şirket logosu ve çizimi ile AD FS oturum açma sayfasını özelleştirme. |
| [Oturum açma açıklaması ekle](#addsignindescription) |Oturum açma sayfası açıklaması ekleme. |
| [AD FS talep kurallarını değiştirme](#modclaims) |Çeşitli Federasyon senaryolarında AD FS taleplerini değiştirme. |

## <a name="manage-ad-fs"></a>AD FS Yönet
Azure AD Connect Sihirbazı 'nı kullanarak en az kullanıcı müdahalesi ile Azure AD Connect çeşitli AD FS ilgili görevleri gerçekleştirebilirsiniz. Sihirbazı çalıştırarak Azure AD Connect yüklemeyi tamamladıktan sonra ek görevleri gerçekleştirmek için Sihirbazı yeniden çalıştırabilirsiniz.

## <a name="repairthetrust"></a>Güveni onarma 
Azure AD Connect kullanarak, AD FS ve Azure AD güveninin geçerli durumunu denetleyebilir ve güveni onarmak için gerekli işlemleri gerçekleştirebilirsiniz. Azure AD 'nizi ve AD FS güvenini onarmak için aşağıdaki adımları izleyin.

1. Ek görevler listesinden **AAD ve ADFS güvenini Onar ' ı** seçin.
   ![AAD ve ADFS güvenini Onar](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. **Azure AD 'ye Bağlan** sayfasında, Azure AD için genel yönetici kimlik bilgilerinizi girin ve **İleri**' ye tıklayın.
   ![Azure AD'ye Bağlanma](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. **Uzaktan erişim kimlik bilgileri** sayfasında, etki alanı yöneticisinin kimlik bilgilerini girin.

   ![Uzaktan erişim kimlik bilgileri](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    **İleri**' ye tıkladıktan sonra, Azure AD Connect sertifika durumunu denetler ve tüm sorunları gösterir.

    ![Sertifikaların durumu](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    **Yapılandırmaya hazırlanma** sayfası, güveni onarmak için gerçekleştirilecek eylemlerin listesini gösterir.

    ![Yapılandırma için hazır](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Güveni onarmak için, **yükler** ' e tıklayın.

> [!NOTE]
> Azure AD Connect yalnızca kendinden imzalı sertifikaları onarabilir veya üzerinde işlem yapabilir. Azure AD Connect üçüncü taraf sertifikaları onaramaz.

## <a name="alternateid"></a>AlternateId kullanarak Azure AD ile federasyona ekleme 
Şirket içi Kullanıcı asıl adı (UPN) ve bulut Kullanıcı asıl adının aynı tutulması önerilir. Şirket içi UPN, yönlendirilemeyen bir etki alanı (örn.) kullanıyorsa. Contoso. Local) veya yerel uygulama bağımlılıkları nedeniyle değiştirilemez, alternatif oturum açma KIMLIĞI ayarlamayı öneririz. Alternatif oturum açma KIMLIĞI, kullanıcıların, e-posta gibi UPN dışında bir öznitelikle oturum açbilecekleri bir oturum açma deneyimi yapılandırmanıza olanak tanır. Azure AD Connect Kullanıcı asıl adı seçimi Active Directory ' deki userPrincipalName özniteliğine varsayılan olarak sahiptir. Kullanıcı asıl adı için başka bir öznitelik seçer ve AD FS kullanarak Federasyonun, Azure AD Connect alternatif oturum KIMLIĞI için AD FS yapılandırır. Kullanıcı asıl adı için farklı bir öznitelik seçme örneği aşağıda gösterilmiştir:

![Alternatif KIMLIK öznitelik seçimi](./media/how-to-connect-fed-management/attributeselection.png)

AD FS için alternatif oturum açma KIMLIĞINI yapılandırmak iki ana adımdan oluşur:
1. **Doğru verme talepleri kümesini yapılandırın**: Azure AD bağlı olan taraf güveninde verme talep kuralları, seçilen UserPrincipalName özniteliğini kullanıcının alternatif KIMLIĞI olarak kullanacak şekilde değiştirilir.
2. **AD FS yapılandırmasında alternatif oturum açma kimliğini etkinleştirin**: AD FS yapılandırması, AD FS alternatif KIMLIĞI kullanarak uygun ormanlardaki kullanıcıları arayabilmesi için güncelleştirilir. Bu yapılandırma Windows Server 2012 R2 'deki AD FS (KB2919355 ile) veya sonraki sürümlerde desteklenir. AD FS sunucuları 2012 R2 ise, gerekli KB 'nin varlığını denetler Azure AD Connect. KB algılanmazsa, yapılandırma tamamlandıktan sonra aşağıda gösterildiği gibi bir uyarı görüntülenir:

    ![2012R2 ' de eksik KB uyarısı](./media/how-to-connect-fed-management/kbwarning.png)

    Eksik KB durumunda yapılandırmayı yeniden düzeltmek için gerekli [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) ' yi yükleyip, ardından [AAD 'Yi onar ve güveni AD FS güven](#repairthetrust)' i kullanarak güveni onarın.

> [!NOTE]
> AlternateId ve el ile yapılandırma adımları hakkında daha fazla bilgi için [Alternatif oturum açma kimliğini yapılandırma](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id) makalesini okuyun

## <a name="addadfsserver"></a>AD FS sunucusu ekleme 

> [!NOTE]
> AD FS bir sunucu eklemek için PFX sertifikası Azure AD Connect gerekir. Bu nedenle, bu işlemi yalnızca Azure AD Connect kullanarak AD FS grubunu yapılandırdıysanız gerçekleştirebilirsiniz.

1. **Ek bir federasyon sunucusu dağıt**' ı seçin ve **İleri**' ye tıklayın.

   ![Ek federasyon sunucusu](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. **Azure AD 'ye Bağlan** sayfasında, Azure AD için genel yönetici kimlik bilgilerinizi girin ve **İleri**' ye tıklayın.

   ![Azure AD'ye bağlan](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Etki alanı yöneticisi kimlik bilgilerini sağlayın.

   ![Etki alanı yöneticisi kimlik bilgileri](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Azure AD Connect, yeni AD FS grubunuzu Azure AD Connect yapılandırırken verdiğiniz PFX dosyasının parolasını ister. PFX dosyasının parolasını sağlamak için **parolayı girin** ' e tıklayın.

   ![Sertifika parolası](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![SSL sertifikasını belirtin](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. **AD FS sunucuları** sayfasında, AD FS grubuna eklenecek sunucu adını veya IP adresini girin.

   ![AD FS sunucuları](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. **İleri**' ye tıklayın ve son **yapılandırma** sayfasını ziyaret edin. Azure AD Connect sunucuları AD FS grubuna eklemeyi tamamladıktan sonra, bağlantıyı doğrulama seçeneği verilir.

   ![Yapılandırma için hazır](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Yükleme Tamam](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>AD FS WAP sunucusu ekleme 

> [!NOTE]
> WAP sunucusu eklemek için, Azure AD Connect PFX sertifikasını gerektirir. Bu nedenle, AD FS grubunu yalnızca Azure AD Connect kullanarak yapılandırdıysanız bu işlemi gerçekleştirebilirsiniz.

1. Kullanılabilir görevler listesinden **Web uygulaması proxy 'Si dağıt** ' ı seçin.

   ![Web uygulaması ara sunucusu dağıtma](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Azure genel yönetici kimlik bilgilerini sağlayın.

   ![Azure AD'ye bağlan](./media/how-to-connect-fed-management/wapserver2.PNG)

3. **SSL sertifikası belirtin** sayfasında, Azure AD Connect AD FS grubunu YAPıLANDıRDıĞıNıZDA sağladığınız PFX dosyasının parolasını girin.
   ![Sertifika parolası](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![SSL sertifikasını belirtin](./media/how-to-connect-fed-management/WapServer4.PNG)

4. WAP sunucusu olarak eklenecek sunucuyu ekleyin. WAP sunucusu etki alanına katılamadığından, sihirbaz eklenmekte olan sunucuya yönetici kimlik bilgilerini ister.

   ![Yönetim sunucusu kimlik bilgileri](./media/how-to-connect-fed-management/WapServer5.PNG)

5. **Proxy güveni kimlik bilgileri** sayfasında, proxy güvenini yapılandırmak ve AD FS grubundaki birincil sunucuya erişmek için yönetici kimlik bilgilerini sağlayın.

   ![Ara sunucu güveni kimlik bilgileri](./media/how-to-connect-fed-management/WapServer6.PNG)

6. **Yapılandırmaya hazırlanma** sayfasında, sihirbaz gerçekleştirilecek eylemlerin listesini gösterir.

   ![Yapılandırma için hazır](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Yapılandırmayı sona erdirmesi için, **Kur** ' a tıklayın. Yapılandırma tamamlandıktan sonra sihirbaz, sunucularla olan bağlantıyı doğrulama seçeneği sunar. Bağlantıyı denetlemek için **Doğrula** ' ya tıklayın.

   ![Yükleme Tamam](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Federasyon etki alanı ekleme 

Azure AD Connect kullanarak Azure AD ile Federasyon oluşturulacak bir etki alanı kolayca eklenebilir. Azure AD Connect, Federasyon için etki alanı ekler ve Azure AD ile federe birden fazla etki alanınız olduğunda sertifikayı vereni doğru şekilde yansıtmak için talep kurallarını değiştirir.

1. Bir Federasyon etki alanı eklemek için, **ek bir Azure AD etki alanı Ekle**görevini seçin.

   ![Ek Azure AD etki alanı](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Sihirbazın sonraki sayfasında, Azure AD için genel yönetici kimlik bilgilerini sağlayın.

   ![Azure AD'ye bağlan](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. **Uzaktan erişim kimlik bilgileri** sayfasında, etki alanı yöneticisi kimlik bilgilerini sağlayın.

   ![Uzaktan erişim kimlik bilgileri](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Bir sonraki sayfada, sihirbaz şirket içi dizininizi federasyona barındırabileceğiniz Azure AD etki alanlarının bir listesini sağlar. Listeden etki alanını seçin.

   ![Azure AD etki alanı](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Etki alanını seçtikten sonra sihirbaz, sihirbazın gerçekleştirebileceği diğer eylemler ve yapılandırmanın etkisi hakkında uygun bilgileri sağlar. Bazı durumlarda, Azure AD 'de henüz doğrulanmamış bir etki alanını seçerseniz, sihirbaz, etki alanını doğrulamanıza yardımcı olacak bilgiler sağlar. Daha fazla bilgi için bkz. [Azure Active Directory için özel etki alanı adınızı ekleme](../active-directory-domains-add-azure-portal.md) .

5. **İleri**'ye tıklayın. **Yapılandırmaya hazırlanma** sayfası Azure AD Connect gerçekleştireceği eylemlerin listesini gösterir. Yapılandırmayı sona erdirmesi için, **Kur** ' a tıklayın.

   ![Yapılandırma için hazır](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Eklenen Federasyon etki alanındaki kullanıcıların Azure AD 'ye oturum açabilmek için önce eşitlenmesi gerekir.

## <a name="ad-fs-customization"></a>AD FS özelleştirme
Aşağıdaki bölümler, AD FS oturum açma sayfanızı özelleştirirken gerçekleştirmeniz gerekebilecek bazı yaygın görevlerden ilgili ayrıntıları sağlar.

## <a name="customlogo"></a>Özel bir şirket logosu veya çizimi ekleme 
**Oturum açma** sayfasında görüntülenen şirketin logosunu değiştirmek Için aşağıdaki Windows PowerShell cmdlet 'ini ve sözdizimini kullanın.

> [!NOTE]
> Logo için önerilen boyutlar 260 x 35 \@ 96 dpi şeklindedir ve dosya boyutu 10 KB 'tan fazla değildir.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> *TargetName* parametresi gereklidir. AD FS ile yayınlanan varsayılan tema varsayılan olarak adlandırılır.

## <a name="addsignindescription"></a>Oturum açma açıklaması ekle 
**Oturum açma sayfasına**bir oturum açma sayfası açıklaması eklemek Için aşağıdaki Windows PowerShell cmdlet 'ini ve sözdizimini kullanın.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>AD FS talep kurallarını değiştirme 
AD FS, özel talep kuralları oluşturmak için kullanabileceğiniz bir zengin talep dilini destekler. Daha fazla bilgi için bkz. [talep kuralı dili rolü](https://technet.microsoft.com/library/dd807118.aspx).

Aşağıdaki bölümlerde, Azure AD ve AD FS Federasyonu ile ilgili bazı senaryolar için nasıl özel kurallar yazacağınız açıklanır.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Öznitelikte bulunan bir değer üzerinde sabit KIMLIK koşulu
Azure AD Connect, nesneler Azure AD ile eşitlendiğinde kaynak bağlantısı olarak kullanılacak bir öznitelik belirtmenize olanak tanır. Özel öznitelikteki değer boş değilse, sabit bir KIMLIK talebi vermek isteyebilirsiniz.

Örneğin, kaynak bağlayıcının özniteliği olarak **MS-DS-** IBU GUID ' i seçebilir ve özniteliğin bir değere sahip olması durumunda **ImmutableID** as **MS-DS-** ımıse GUID olarak ayarlayabilirsiniz. Özniteliğe karşı bir değer yoksa, sabit KIMLIK olarak **Objectguıd** ' ı yayınlayın. Aşağıdaki bölümde açıklandığı gibi özel talep kuralları kümesi oluşturabilirsiniz.

**Kural 1: Sorgu öznitelikleri**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Bu kuralda, kullanıcının Active Directory için **MS-DS-ımlıguıd** ve **Objectguıd** değerlerini sorgulıyoruz. Mağaza adını AD FS dağıtımınızda uygun bir mağaza adıyla değiştirin. Ayrıca, **Objectguıd** ve **MS-DS-ımıbu GUID**için tanımlanan şekilde, talep türünü Federasyonun uygun bir talep türüyle değiştirin.

Ayrıca, **Ekle** ve Hayır ' ıkullanarak varlık için giden bir sorun eklemekten kaçının ve değerleri ara değer olarak kullanabilir. Sabit KIMLIK olarak kullanılacak değeri belirledikten sonra talebi sonraki bir kuralda verirsiniz.

**Kural 2: Kullanıcı için ms-DS-ımfdsguıd olup olmadığını denetleyin**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Bu kural, Kullanıcı için doldurulmuş bir **MS-DS-ımıbu GUID** yoksa **useguıd** olarak ayarlanan **ıdflag** adlı geçici bir bayrak tanımlar. Bunun arkasındaki mantık AD FS, boş talepler için izin vermediği bir olgu olur. Bu nedenle, talepler http://contoso.com/ws/2016/02/identity/claims/objectguid eklediğinizde ve http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid kural 1 ' de, yalnızca değerin Kullanıcı için doldurulması durumunda bir **msdsıse, GUID** talebi ile sona erdir. Doldurulmamışsa, AD FS boş bir değere sahip olacağını görür ve hemen bırakır. Tüm nesneler **Objectguıd**değerini alacak, bu nedenle kural 1 yürütüldükten sonra talep her zaman olur.

**Kural 3: Varsa sabit KIMLIK olarak ms-DS-ımıdnguıd olarak verme**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);

Bu örtük bir denetim . Talep için değer varsa, bu değeri sabit KIMLIK olarak verin. Önceki örnek, **NameIdentifier** talebini kullanır. Bunu, ortamınızdaki sabit KIMLIK için uygun talep türü olarak değiştirmeniz gerekir.

**Kural 4: Ms-DS-ımıdnguıd yoksa sabit KIMLIK olarak Objectguıd olarak sorun**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);

Bu kuralda, yalnızca geçici bayrağı **ıdflag**' u kontrol edersiniz. Talebin değerine göre mi verilmeyeceğine karar verirsiniz.

> [!NOTE]
> Bu kuralların sırası önemlidir.

### <a name="sso-with-a-subdomain-upn"></a>Alt etki alanı UPN 'si ile SSO

[Yeni bir Federasyon etki alanı ekleme](how-to-connect-fed-management.md#addfeddomain)bölümünde açıklandığı gibi Azure AD Connect kullanarak Federasyon oluşturulacak birden fazla etki alanı ekleyebilirsiniz. Azure AD Connect Version 1.1.553.0 ve latest, ıssuerıd için otomatik olarak doğru talep kuralını oluşturur. Azure AD Connect Version 1.1.553.0 veya latest sürümünü kullandıysanız, Azure AD bağlı olan taraf güveni için doğru talep kuralları oluşturmak ve ayarlamak üzere [Azure AD RPT talep kuralları](https://aka.ms/aadrptclaimrules) aracının kullanılması önerilir.

## <a name="next-steps"></a>Sonraki adımlar
[Kullanıcı oturum açma seçenekleri](plan-connect-user-signin.md)hakkında daha fazla bilgi edinin.
