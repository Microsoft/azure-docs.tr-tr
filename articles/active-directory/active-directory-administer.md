---
title: "Azure AD dizininizi yönetme | Microsoft Belgeleri"
description: "Azure AD kiracısının ne olduğu ve Azure&quot;ın, Azure Active Directory üzerinden nasıl yönetileceği açıklanmaktadır."
services: active-directory
documentationcenter: 
author: curtand
writer: markvi
manager: femila
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/06/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 001ffc0f9c7465552392a9848ef1487a4c0eafce
ms.lasthandoff: 12/07/2016


---
# <a name="administer-your-azure-ad-directory"></a>Azure AD dizinini yönetme
## <a name="what-is-an-azure-ad-tenant"></a>Azure AD kiracısı nedir?
Fiziksel çalışma alanında, kiracı kelimesi bir binada hizmet veren grup veya şirket olarak tanımlanabilir. Örneğin, kuruluşunuzun bir binada ofisi olabilir. Bu bina, diğer kuruluşların da olduğu bir sokakta olabilir. Kuruluşunuz, bu binanın kiracısı olarak kabul edilir. Bu bina, kuruluşunuzun bir varlığıdır; güvenliğinizi ve işlerinizi güvenli şekilde yapabilmenizi sağlar. Ayrıca, sokağınızdaki diğer işletmelerden ayrıdır. Bu, kuruluşunuzun ve kuruluşunuzda bulunan varlıkların diğer kuruluşlardan ayrı tutulmasını sağlar.

Bulutun etkin olduğu çalışma alanında kiracı, bulut hizmetinin belirli bir örneğine sahip olan ve o örneği yöneten bir istemci veya kuruluş olarak tanımlanabilir. Microsoft Azure tarafından sağlanan kimlik platformunda kiracı, kuruluşunuzun Azure veya Office 365 gibi bir Microsoft bulut hizmetinde oturum açtığında aldığı ve sahip olduğu bir Azure Active Directory (Azure AD) adanmış örneğidir.

Her Azure AD dizini, diğer Azure AD dizinlerinden farklı ve ayrıdır. Kurumsal ofis binasının yalnızca kuruluşunuza özel güvenilir bir varlık olması gibi, Azure AD dizini de yalnızca sizin kuruluşunuz tarafından kullanılmak üzere tasarlanan güvenilir bir varlıktır. Azure AD mimarisi, müşteri verilerini ve kimlik bilgilerini ortak karıştırma alanından yalıtır. Bu, bir Azure AD dizinindeki kullanıcıların ve yöneticilerin yanlışlıkla veya kötü amaçlı olarak başka bir dizindeki verilere erişemeyeceği anlamına gelir.

![Azure Active Directory'yi yönetme][1]

## <a name="how-can-i-get-an-azure-ad-directory"></a>Azure AD dizinine nasıl sahip olabilirim?
Azure AD, aşağıdakiler dahil olmak üzere çoğu Microsoft bulut hizmetinin dışında çekirdek dizin ve kimlik yönetimi özellikleri sağlar:

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Bu Microsoft bulut hizmetlerinden herhangi birine kaydolduğunuzda, bir Azure AD dizinine sahip olursunuz. Gerektikçe ek dizinler oluşturabilirsiniz. Örneğin, ilk dizininizi üretim dizini olarak tutup test veya hazırlama işlemleri için başka bir dizin oluşturabilirsiniz.

> [!NOTE]
> İlk hizmetiniz için kaydolduktan sonra, diğer Microsoft bulut hizmetleri için oturum açarken kuruluşunuz ile ilişkilendirilen aynı yönetici hesabını kullanmanızı öneririz.
> 
> 

İlk kez bir Microsoft bulut hizmeti için kaydolduğunuzda, kuruluşunuz ve kuruluşunuzun İnternet etki alanı adı kaydıyla ilgili bilgi sağlamanız istenir. Bu bilgiler daha sonra kuruluşunuz için yeni bir Azure AD dizini örneğinin oluşturulması için kullanılır. Birden fazla Microsoft bulut hizmetine abone olduğunuzda, oturum açma denemeleri sırasında kimlik doğrulaması yapmak için de aynı dizin kullanılır.

Ek hizmetler, kuruluşunuzun şirket içi kimlik altyapısı ve Azure AD arasındaki verimliliği artırmanıza yardımcı olması için yapılandırdığınız mevcut kullanıcı hesapları, ilkeler, ayarlar veya şirket içi dizin tümleştirmesinden tam anlamıyla faydalanır.

Örneğin, başlangıçta Microsoft Intune aboneliği için kaydolup dizin eşitleme ve/veya çoklu oturum açma sunucuları dağıtarak şirket içi Active Directory'nizi Azure AD diziniyle daha fazla tümleştirmeniz için gerekli adımları tamamladıysanız şu anda Microsoft Intune ile sahip olduğunuz aynı dizin tümleştirme avantajlarından faydalanabilecek olan başka bir Microsoft bulut hizmetine (Office 365 gibi) kaydolabilirsiniz.

Şirket içi dizininizi Azure AD ile tümleştirme hakkında daha fazla bilgi için bkz. [Dizin tümleştirme](active-directory-aadconnect.md).

### <a name="associate-an-azure-ad-directory-with-a-new-azure-subscription"></a>Azure AD dizinini yeni bir Azure aboneliğiyle ilişkilendirme
Yeni bir Azure aboneliği ile mevcut bir Office 365 veya Microsoft Intune aboneliği için oturum açma kimlik doğrulaması bir dizini ilişkilendirebilirsiniz. İş veya okul hesabınızı kullanarak Azure Yönetim Portalı'nda oturum açın. Yönetim Portalı, bu hesaba ilişkin herhangi bir aboneliğin bulunamadığını belirten bir ileti döndürür. **Azure'a Kaydol** seçeneğini belirlediğinizde dizininiz portalda yönetim için kullanılabilir. Daha fazla bilgi için bkz. [Azure'da Office 365 aboneliğinize ilişkin dizini yönetme](active-directory-how-subscriptions-associated-directory.md#manage-the-directory-for-your-office-365-subscription-in-azure).

Azure AD'ye ilişkin ortak kullanımla ilgili sorular hakkındaki video için bkz. [Azure Active Directory - Kaydolma, oturum açma ve kullanım ile ilgili ortak sorular](http://channel9.msdn.com/Series/Windows-Azure-Active-Directory/WAADCommonSignupsigninquestions).

### <a name="create-an-azure-ad-directory-by-signing-up-for-a-microsoft-cloud-service-as-an-organization"></a>Microsoft bulut hizmetine bir kuruluş olarak kaydolarak Azure AD dizini oluşturma
Henüz bir Microsoft bulut hizmeti aboneliğiniz yoksa kaydolmak için aşağıdaki bağlantılardan birini kullanın. İlk hizmetiniz için kaydolduktan sonra otomatik olarak bir Azure AD dizini oluşturulur.

* [Microsoft Azure](https://account.windowsazure.com/organization)
* [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
* [Microsoft Intune](https://account.manage.microsoft.com/Signup/MainSignUp.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&ali=1)

### <a name="manage-an-azure-provisioned-default-directory"></a>Azure tarafından sağlanan Varsayılan dizini yönetme
Şu anda Azure'a kaydolduğunuzda otomatik olarak bir dizin oluşturulur ve aboneliğiniz bu dizin ile ilişkilendirilir. Ancak Azure'a ilk olarak Ekim 2013'ten önce kaydolduysanız otomatik olarak oluşturulmuş bir dizininiz yoktur. Bu durumda Azure, Varsayılan bir dizin sağlayarak hesabınızın "açığını kapatmış" olabilir. Dolayısıyla aboneliğiniz bu Varsayılan dizin ile ilişkilendirilmiştir.

Dizin açıkları, Azure güvenlik modelinde yapılan genel iyileştirmenin bir parçası olarak Ekim 2013'te kapatıldı. Bu, tüm Azure müşterilerine kuruluşa özgü kimlik özelliklerinin sunulmasına yardımcı olmanın yanı sıra dizindeki bir kullanıcı bağlamında tüm Azure kaynaklarına erişilmesini sağlar. Azure'ı dizin olmadan kullanamazsınız. Bu nedenle 7 Temmuz 2013'ten önce kaydolan ancak dizini olmayan her kullanıcının bir dizin oluşturması gerekti. Zaten bir dizin oluşturduysanız aboneliğinizi bu dizinle ilişkilendirilmiştir.

Azure AD kullanımının herhangi bir maliyeti yoktur. Dizin ücretsiz bir kaynaktır. Ayrı olarak lisanslanan; şirket markası oluşturma ve self servis parola sıfırlama gibi ek özellikler sağlayan bir Azure Active Directory Premium katmanı da vardır.

Dizininizin görünen adını değiştirmek için portalda dizine ve **Yapılandır**'a tıklayın. Bu konu başlığının ilerleyen kısımlarında açıklandığı üzere yeni bir dizin ekleyebilir veya artık ihtiyaç duymadığınız bir dizini silebilirsiniz. Aboneliğinizi farklı bir dizin ile ilişkilendirmek için sol gezinti bölmesinde **Ayarlar** uzantısına tıklayın ve ardından **Abonelikler** sayfasının alt kısmında yer alan **Dizini Düzenle**'ye tıklayın. Varsayılan *.onmicrosoft.com etki alanı yerine, kaydettiğiniz bir DNS adını kullanarak özel bir etki alanı da oluşturabilirsiniz. SharePoint Online gibi bir hizmette bu seçenek tercih edilebilir.

## <a name="how-can-i-manage-directory-data"></a>Dizin verilerini nasıl yönetebilirim?
Bir veya daha fazla Microsoft bulut hizmeti aboneliğinin yöneticisi olarak, kuruluşlarınızın dizin verilerini yönetmek için Azure Yönetim Portalı'nı, Microsoft Intune hesap portalını veya Office 365 Yönetim Merkezi'ni kullanabilirsiniz. Ayrıca Azure AD'de depolanan verileri yönetmenize yardımcı olması için [Windows PowerShell için Microsoft Azure Active Directory Modülü](https://msdn.microsoft.com/library/azure/jj151815.aspx) cmdlet'lerini de indirip çalıştırabilirsiniz.

Bu portalları (veya cmdlet'leri) kullanarak şunlar yapabilirsiniz:

* Kullanıcı ve grup hesapları oluşturma ve yönetme
* Kuruluşunuzun abonesi olduğu bulut hizmetlerini yönetme
* Dizin hizmetinizle şirket içi tümleştirmeyi ayarlama

Azure Yönetim Portalı, Office 365 Yönetici Merkezi, Microsoft Intune hesap portalı ve Azure AD cmdlet'lerinin tümü, aşağıdaki çizimde gösterildiği gibi okuma ve yazma işlemlerini kuruluş dizininizle ilişkilendirilen tek bir paylaşılan Azure AD örneğinde gerçekleştirir. Böylece portallar (veya cmdlet'ler) dizin verilerinizi çeken ve/veya değiştiren bir ön uç arabirimi işlevi görür.

![][2]

Kullanıcıları ve grupları yönetmek için kullanılan bu hesap portalları ve ilişkili Azure AD PowerShell cmdlet'leri, Azure AD platformu üzerinde oluşturulur.

Bu hizmetlerden biri için oturumunuz açıkken, o sırada portallardan (veya cmdlet'lerden) herhangi birini kullanan kuruluş verilerinizde bir değişiklik yaptığınızda bu değişiklik, daha sonra aynı hizmet için oturum açtığınızda diğer portallarda da gösterilir. Bunun nedeni, verilerin abone olduğunuz Microsoft bulut hizmetleri arasında paylaşılıyor olmasıdır.
Örneğin, Office 365 Yönetim Merkezi'ni kullanarak bir kullanıcının oturum açmasını engellerseniz aynı kullanıcının kuruluşunuzun şu anda abone olduğu diğer hizmetlerde de oturum açması engellenir. Aynı kullanıcının hesabını Microsoft Intune hesap portalında görüntülediğinizde kullanıcının engellendiğini görürsünüz.

## <a name="how-can-i-add-and-manage-multiple-directories"></a>Birden fazla dizini nasıl ekleyip yönetebilirim?
Azure Yönetim Portalı'nda Azure AD dizini ekleyebilirsiniz. Soldaki **Active Directory** uzantısını seçin ve **Ekle**'ye tıklayın.

Her dizini tamamen bağımsız bir kaynak olarak yönetebilirsiniz: Her dizin eşdüzeyde, tam özellikli ve yönettiğiniz diğer dizinlerden mantıksal olarak bağımsızdır ve dizinler arasında üst-alt ilişkisi yoktur. Dizinler arasındaki bu bağımsızlığa kaynak bağımsızlığı, yönetim bağımsızlığı ve eşitleme bağımsızlığı dahildir.

* **Kaynak bağımsızlığı**. Aşağıda açıklandığı gibi, bir dizinde kaynak oluşturur veya silerseniz dış kullanıcılar hariç olmak üzere bu durumun başka bir dizindeki kaynaklar üzerinde herhangi bir etkisi yoktur. Bir dizinde "contoso.com" özel etki alanını kullanırsanız başka bir dizinde kullanamazsınız.
* **Yönetim bağımsızlığı**.  "Contoso" dizininin yönetici olmayan bir kullanıcısı "Test" adlı bir test dizini oluşturursa:
  
  * Verileri tek bir AD ormanıyla eşitlemek için dizin eşitleme aracı.
  * ‘Test’ yöneticisi özel olarak yetki vermediği sürece, ‘Contoso’ dizininin yöneticilerinin ‘Test’ dizini için doğrudan yönetim ayrıcalıkları yoktur. "Contoso" yöneticileri, "Test" dizinini oluşturan kullanıcı hesabını denetleyebildiği için "Test" dizinine erişimi denetleyebilir.
    
    Ayrıca dizinde bir kullanıcının yönetici rolünü değiştirirseniz (ekler veya kaldırırsanız) bu değişiklik, kullanıcının başka bir dizinde sahip olduğu herhangi bir yönetici rolünü etkilemez.
* **Eşitleme bağımsızlığı**. Her Azure AD'yi, verileri aşağıdakilerden herhangi birinin tek bir örneğinden eşitleyecek şekilde birbirinden bağımsız olarak yapılandırabilirsiniz:
  
  * Dizin eşitleme aracı (verileri tek bir AD ormanıyla eşitlemek için)
  * Forefront Identity Manager için Azure Active Directory Bağlayıcısı (verileri bir veya birden fazla şirket içi ormanıyla ve/veya AD dışı veri kaynağıyla eşitlemek için).

Ayrıca, diğer Azure kaynaklarının aksine dizinlerinizin bir Azure aboneliğinin alt kaynakları olmadığını unutmayın. Bu nedenle Azure aboneliğinizi iptal etseniz veya aboneliğinizin süresi dolsa bile Azure AD PowerShell'i, Azure Grafik API'sini veya Office 365 Yönetim Merkezi gibi diğer arabirimleri kullanarak dizin verilerinize erişmeye devam edersiniz. Ayrıca başka bir aboneliği dizinle ilişkilendirebilirsiniz.

## <a name="how-can-i-delete-an-azure-ad-directory"></a>Azure AD dizinini nasıl silebilirim?
Azure AD dizinini portaldan genel yönetici silebilir. Bir dizin silindiğinde dizinde bulunan tüm kaynaklar da silinir. Bu nedenle dizini silmeden önce o dizine artık ihtiyacınız olmadığından emin olun.

> [!NOTE]
> Kullanıcı, bir iş veya okul hesabıyla oturum açtıysa kendi giriş dizinini silmeye çalışmamalıdır. Örneğin, kullanıcı joe@contoso.onmicrosoft.com, olarak oturum açtıysa, varsayılan etki alanı contoso.onmicrosoft.com olan dizini silemez.
> 
> 

### <a name="conditions-that-must-be-met-to-delete-an-azure-ad-directory"></a>Azure AD dizinini silmek için sağlanması gereken koşullar
Azure AD, bir dizinin silinmesi için belirli koşulların sağlanmasını gerektirir. Bu, bir dizinin silinmesinin kullanıcılar ve uygulamalar üzerinde olumsuz bir etki oluşturması (örneğin, kullanıcıların Office 365'te oturum açamamaları veya Azure'daki kaynaklara erişememeleri) riskini azaltır. Örneğin, bir aboneliğe ilişkin dizin yanlışlıkla silinirse kullanıcılar o aboneliğe ilişkin Azure kaynaklarına erişemez.

Şu koşullar denetlenir:

* Dizin içinde dizini silebilecek tek kullanıcı genel yöneticidir. Dizin silinmeden önce diğer tüm kullanıcılar silinmelidir. Kullanıcılar şirket içinden eşitlendiyse eşitlemenin devre dışı bırakılması ve Yönetim Portalı veya Windows PowerShell için Azure modülü kullanılarak kullanıcıların bulut dizininden silinmesi gerekir. Grupların veya kişilerin (örneğin, Office 365 Yönetim Merkezi'nden eklenen kişiler) silinmesi gerekmez.
* Dizinde uygulama bulunamaz. Dizin silinmeden önce tüm uygulamaların silinmesi gerekir.
* Microsoft Çevrimiçi Hizmetlerine (dizinle ilişkili Azure AD Premium, Microsoft Azure veya Office 365 gibi) ilişkin hiçbir aboneliğin bulunmaması gerekir. Örneğin, sizin için Azure'da varsayılan bir dizin oluşturulduysa ve Azure aboneliğinizin kimlik doğrulaması için hâlâ bu dizini kullanıyor olması halinde bu dizini silemezsiniz. Benzer şekilde, başka bir kullanıcı dizinle bir aboneliği ilişkilendirdiyse o dizini silemezsiniz. Aboneliğinizi farklı bir dizin ile ilişkilendirmek için Azure Yönetim Portalı'nda oturum açın ve sol gezinti bölmesindeki **Ayarlar** seçeneğine tıklayın. Ardından **Abonelikler** sayfasının altındaki **Dizinleri Düzenle** seçeneğine tıklayın. Azure abonelikleri hakkında daha fazla bilgi için bkz.[Azure aboneliklerinin Azure AD ile ilişkisi](active-directory-how-subscriptions-associated-directory.md).

> [!NOTE]
> Kullanıcı, bir iş veya okul hesabıyla oturum açtıysa kendi giriş dizinini silmeye çalışmamalıdır. Örneğin, kullanıcı joe@contoso.onmicrosoft.com, olarak oturum açtıysa, varsayılan etki alanı contoso.onmicrosoft.com olan dizini silemez.
> 
> 

* Dizine herhangi bir Multi-Factor Authentication sağlayıcısı bağlanamaz.

## <a name="additional-resources"></a>Ek Kaynaklar
* [Azure AD Forumu](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
* [Azure Multi-Factor Authentication Forumu](https://social.msdn.microsoft.com/Forums/home?forum=windowsazureactiveauthentication)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/azure)
* [Azure’a kuruluş olarak kaydolma](sign-up-organization.md)
* [Windows PowerShell’i kullanarak Azure AD’yi yönetme](https://msdn.microsoft.com/library/azure/jj151815.aspx)
* [Azure AD’de yönetici rolü atama](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-administer/aad_portals.png
[2]: ./media/active-directory-administer/azure_tenants.png

