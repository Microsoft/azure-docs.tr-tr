---
title: Azure AD Yetkilendirme Yönetimi 'ne bağlı bir kuruluş ekleme-Azure Active Directory
description: Kuruluşunuz dışındaki kişilerin, projelerde işbirliği yapabilmeniz için erişim paketleri istemesine nasıl izin vereceğinizi öğrenin.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/11/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44b4e4bccde07d078c9749ee76c1653e6d431e63
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532089"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Azure AD Yetkilendirme Yönetimi 'nde bağlı bir kuruluş ekleme

Azure Active Directory (Azure AD) yetkilendirme yönetimi sayesinde, kuruluşunuzun dışındaki kişilerle işbirliği yapabilirsiniz. Bir dış Azure AD dizini veya etki alanındaki kullanıcılarla sık sık işbirliği yaptıysanız, bunları bağlı bir kuruluş olarak ekleyebilirsiniz. Bu makalede, kuruluşunuzun dışındaki kullanıcıların dizininizde kaynak istemesine izin vermek için bağlı bir kuruluşun nasıl ekleneceği açıklanmaktadır.

## <a name="what-is-a-connected-organization"></a>Bağlı kuruluş nedir?

Bağlı bir kuruluş, ile ilişkiniz olan başka bir kuruluştur.  Bu kuruluştaki kullanıcıların, SharePoint Online siteleriniz veya uygulamalarınız gibi kaynaklarınıza erişebilmeleri için bu dizindeki kullanıcıların söz konusu dizinde bir gösterimine sahip olmanız gerekir.  Çoğu durumda bu kuruluştaki kullanıcılar zaten Azure AD dizininizde olmadığından, gerektiğinde Azure AD dizininize getirmek için yetkilendirme yönetimini kullanabilirsiniz.  

Yetkilendirme yönetiminin, bağlı bir kuruluş oluşturan kullanıcıları belirtmenize izin veren üç yolu vardır.  Olabilir

* başka bir Azure AD dizinindeki kullanıcılar,
* doğrudan Federasyon için yapılandırılmış başka bir Azure dışı AD dizinindeki kullanıcılar veya
* e-posta adresi hepsi ortak olarak aynı etki alanı adına sahip olan başka bir Azure dışı AD dizininde bulunan kullanıcılar.

Örneğin, Woodgrove Bank 'ta çalıştığınızı ve iki harici kurum ile işbirliği yapmak istediğinizi varsayalım. Bu iki kuruluş farklı yapılandırmalara sahiptir:

- Grafik tasarımı Enstitüsü, Azure AD 'yi kullanır ve kullanıcıların *graphicdesigninstitute.com* ile biten bir Kullanıcı asıl adı vardır.
- Contoso henüz Azure AD kullanmıyor. Contoso kullanıcıları, *contoso.com* ile biten bir Kullanıcı asıl adına sahiptir.

Bu durumda, iki bağlı kuruluş yapılandırabilirsiniz. Grafik Tasarım Enstitüsü ve contoso için bir bağlı kuruluş oluşturursunuz. Daha sonra, iki bağlı kuruluş bir ilkeye eklerseniz, her bir kuruluştan, ilkeyle eşleşen bir Kullanıcı asıl adına sahip kullanıcılar erişim paketleri isteyebilir. Contoso.com etki alanına sahip bir Kullanıcı asıl adına sahip kullanıcılar, contoso bağlantılı kuruluşla eşleştirebilir ve ayrıca paket istemesine izin verilir. *Graphicdesigninstitute.com* etki alanına sahip bir Kullanıcı asıl adına sahip kullanıcılar, grafik tasarımı Enstitüsü bağlantılı kuruluşla eşleşir ve istek göndermesine izin verilir. Ayrıca, grafik tasarımı Enstitüsü Azure AD 'yi kullandığından, *graphicdesignınstitute. example* gibi, kiracılarına eklenen [doğrulanmış bir etki alanıyla](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) eşleşen bir asıl adı olan tüm kullanıcılar aynı ilkeyi kullanarak erişim paketleri isteyebilecektir. [E-posta bir kerelik geçiş kodu (OTP) kimlik doğrulaması](../external-identities/one-time-passcode.md) açıksa, bu etki alanlarından henüz, kaynaklarınıza erişirken e-posta OTP kullanarak kimlik doğrulaması yapacak Azure AD hesaplarına sahip olmayan kullanıcıları içerir. 

![Bağlı kuruluş örneği](./media/entitlement-management-organization/connected-organization-example.png)

Azure AD dizininden veya etki alanındaki kullanıcıların kimlik doğrulaması, kimlik doğrulama türüne bağlıdır. Bağlı kuruluşların kimlik doğrulama türleri şunlardır:

- Azure AD
- [Doğrudan Federasyon](../external-identities/direct-federation.md)
- [Bir kerelik geçiş kodu](../external-identities/one-time-passcode.md) (etki alanı)

Bağlı bir kuruluşun nasıl ekleneceğini gösteren bir gösterim için aşağıdaki videoyu izleyin:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Bağlı kuruluş ekleme

Dış bir Azure AD dizini veya etki alanını bağlı bir kuruluş olarak eklemek için, bu bölümdeki yönergeleri izleyin.

**Önkoşul rolü**: *genel yönetici* veya *Kullanıcı Yöneticisi*

1. Azure portal **Azure Active Directory**' i seçin ve ardından **Identity idare**' ı seçin.

1. Sol bölmede **bağlı kuruluşlar**' ı seçin ve ardından **bağlı kuruluş Ekle**' yi seçin.

    !["Bağlı kuruluş Ekle" düğmesi](./media/entitlement-management-organization/connected-organization.png)

1. **Temel bilgiler** sekmesini seçin ve ardından kuruluş için bir görünen ad ve açıklama girin.

    !["Bağlı kuruluş Ekle" temelleri bölmesi](./media/entitlement-management-organization/organization-basics.png)

1. Yeni bağlı bir kuruluş oluşturduğunuzda durum otomatik olarak **yapılandırılacak** şekilde ayarlanır. Durum özellikleri hakkında daha fazla bilgi için bkz. [bağlı kuruluşların durum özellikleri](#state-properties-of-connected-organizations)

1. **Dizin + etki alanı** sekmesini seçin ve ardından **dizin + etki alanı Ekle**' yi seçin.

    **Dizinleri seçin + etki alanları** bölmesi açılır.

1. Arama kutusuna Azure AD dizinini veya etki alanını aramak için bir etki alanı adı girin. Tüm etki alanı adını girdiğinizden emin olun.

1. Kuruluş adının ve kimlik doğrulama türünün doğru olduğundan emin olun. Kullanıcıların oturum açması, kimlik doğrulama türüne bağlıdır.

    !["Dizinleri seçin + etki alanları" bölmesi](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Azure AD dizinini veya etki alanını eklemek için **Ekle** ' yi seçin. Şu anda, bağlı kuruluşa göre yalnızca bir Azure AD dizini veya etki alanı ekleyebilirsiniz.

    > [!NOTE]
    > Azure AD dizini veya etki alanındaki tüm kullanıcılar bu erişim paketini isteyebilecektir. Bu, Azure AD iş 'ten iş (B2B) izin verme veya reddetme listesi tarafından engellenmediği takdirde, bu etki alanları, dizinle ilişkili tüm alt etki alanlarından Azure AD 'deki kullanıcıları içerir. Daha fazla bilgi için, bkz. [belirli kuruluşlardan B2B kullanıcılarına Izin verme veya bu kullanıcıların davetlerini engelleme](../external-identities/allow-deny-list.md).

1. Azure AD dizinini veya etki alanını ekledikten sonra **Seç**' i seçin.

    Kuruluş listede görüntülenir.

    !["Dizin + etki alanı" bölmesi](./media/entitlement-management-organization/organization-directory-domain.png)

1. **Sponsorlar** sekmesini seçin ve ardından bu bağlı kuruluş için isteğe bağlı sponsorlar ekleyin.

    Sponsorlar, dizininizde bulunan ve bu bağlı kuruluşla ilişki için iletişim noktası olan iç veya dış kullanıcılardır. İç sponsorlar, dizininizdeki üye kullanıcılardır. Dış sponsorlar, daha önce davet edilmiş ve dizininizde zaten olan bağlı kuruluştan Konuk kullanıcılardır. Bu bağlı kuruluştaki kullanıcılar bu erişim paketine erişim isteğinde bulunduğunda, sponsorlar olarak kullanılabilir. Bir konuk kullanıcıyı dizininize davet etme hakkında daha fazla bilgi için, bkz. [Azure portal Azure ACTIVE DIRECTORY B2B işbirliği kullanıcıları ekleme](../external-identities/add-users-administrator.md).

    **Ekle/Kaldır**' ı seçtiğinizde, içinde iç veya dış sponsorları seçebileceğiniz bir bölme açılır. Bölmesinde, dizininizdeki kullanıcıların ve grupların filtrelenmemiş bir listesi görüntülenir.

    ![Sponsorlar bölmesi](./media/entitlement-management-organization/organization-sponsors.png)

1. **Gözden geçir + oluştur** sekmesini seçin, kuruluş ayarlarınızı gözden geçirin ve ardından **Oluştur**' u seçin.

    !["Gözden geçir + oluştur" bölmesi](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Bağlı bir kuruluşu güncelleştirme 

Bağlı kuruluş farklı bir etki alanına değişirse, kuruluşun adı değişir veya sponsorları değiştirmek istiyorsanız, bu bölümdeki yönergeleri izleyerek bağlı organizasyonu güncelleştirebilirsiniz.

**Önkoşul rolü**: *genel yönetici* veya *Kullanıcı Yöneticisi*

1. Azure portal **Azure Active Directory**' i seçin ve ardından **Identity idare**' ı seçin.

1. Sol bölmede **bağlı kuruluşlar**' ı seçin ve sonra açmak için bağlı kuruluş ' u seçin.

1. Bağlı kuruluşun Genel Bakış bölmesinde, kuruluş adını, açıklamasını veya durumunu değiştirmek için **Düzenle** ' yi seçin.  

1. **Dizin + etki** alanı bölmesinde, farklı bir dizin veya etki alanına geçmek için **dizin + etki alanını güncelleştir** ' i seçin.

1. **Sponsorlar** bölmesinde, **dahili sponsorlar Ekle** ' yi seçin veya **Harici sponsorlar ekleyerek** bir kullanıcıyı sponsor olarak ekleyin. Sponsor öğesini kaldırmak için sponsor öğesini seçin ve sağ bölmedeki **Sil**' i seçin.


## <a name="delete-a-connected-organization"></a>Bağlı bir kuruluşu silme

Artık dış Azure AD dizini veya etki alanı ile bir ilişkiniz yoksa bağlı kuruluşu silebilirsiniz.

**Önkoşul rolü**: *genel yönetici* veya *Kullanıcı Yöneticisi*

1. Azure portal **Azure Active Directory**' i seçin ve ardından **Identity idare**' ı seçin.

1. Sol bölmede **bağlı kuruluşlar**' ı seçin ve sonra açmak için bağlı kuruluş ' u seçin.

1. Bağlı kuruluşun genel bakış **bölmesinde Sil ' i seçerek silin** .

    ![Bağlı kuruluş Sil düğmesi](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>Bağlı bir kuruluşu program aracılığıyla yönetme

Ayrıca Microsoft Graph kullanarak bağlı kuruluşlar oluşturabilir, listeleyebilir, güncelleştirebilir ve silebilirsiniz. Temsilci izni olan bir uygulamayla uygun bir roldeki kullanıcı `EntitlementManagement.ReadWrite.All` , [connectedorganization](/graph/api/resources/connectedorganization?view=graph-rest-beta&preserve-view=true) nesnelerini yönetmek ve bunlar için sponsorları ayarlamak üzere API 'yi çağırabilir.

## <a name="state-properties-of-connected-organizations"></a>Bağlı kuruluşların durum özellikleri

Azure AD yetkilendirme yönetiminde bağlı kuruluşlar için şu anda iki farklı tür durum özelliği bulunur, yapılandırıldı ve önerilir: 

- Yapılandırılmış bağlı bir kuruluş, bu kuruluştaki kullanıcıların paketlere erişim sağlamasına izin veren tamamen işlevsel bağlı bir kuruluştur. Yönetici Azure portal yeni bir bağlı kuruluş oluşturduğunda, yönetici tarafından oluşturulduğundan ve bu bağlı kuruluşu kullanmak istediğinde, varsayılan olarak **yapılandırılan** durumda olur. Ayrıca, API aracılığıyla bağlı bir kuruluş program aracılığıyla oluşturulduğunda, başka bir durum açık olarak ayarlanmadığı takdirde varsayılan durum **yapılandırılmalıdır** . 

    Yapılandırılan bağlı kuruluşlar, bağlı kuruluşların seçicileri içinde görünür ve "tüm" bağlı kuruluşları hedefleyen tüm ilkeler için kapsam içinde yer alacak.

- Önerilen bağlı bir kuruluş, otomatik olarak oluşturulan, ancak bir yöneticisinin kuruluş oluşturup onaylaması olmayan bağlı bir kuruluştur. Bir Kullanıcı yapılandırılmış bağlı bir kuruluşun dışında bir erişim paketine kaydolduğunda, kiracı tarafından ayarlanan hiçbir yönetici olmadığından, otomatik olarak oluşturulan bağlı kuruluşlar **Önerilen** durumda olur. 
    
    Önerilen bağlı kuruluşlar, herhangi bir ilke üzerinde "tüm yapılandırılmış bağlı kuruluşlar" ayarının kapsamında değildir ancak yalnızca belirli kuruluşları hedefleyen ilkeler için ilkeler içinde kullanılabilir. 

Yalnızca yapılandırılan bağlı kuruluşların kullanıcıları, yapılandırılan tüm kuruluşların kullanıcıları tarafından kullanılabilen erişim paketleri isteyebilir. Önerilen bağlı kuruluşların kullanıcıları, bu etki alanı için bağlı bir kuruluş olmadığı için bir deneyim yaşar. yalnızca belirli kuruluşlarının kapsamındaki veya herhangi bir kullanıcının kapsamına alınmış erişim paketlerini görebilir ve bunlara izin verebilir.

> [!NOTE]
> Bu yeni özelliği kullanıma almanın bir parçası olarak, 09/09/20 öncesinde oluşturulan tüm bağlı kuruluşlar **yapılandırılmış** olarak değerlendirilir. Herhangi bir kuruluştan kullanıcıların kaydolmasına izin veren bir erişim paketiniz varsa, hiçbirinin **yapılandırılmış** olarak yanlış kategorilere ayrılmadığından emin olmak için bu tarihten önce oluşturulan bağlı kuruluşların listesini gözden geçirmeniz gerekir.  Yönetici, **durum** özelliğini uygun şekilde güncelleştirebilir. Rehberlik için bkz. [bağlı kuruluşu güncelleştirme](#update-a-connected-organization).

## <a name="next-steps"></a>Sonraki adımlar

- [Dış kullanıcılara yönelik idare erişimi](./entitlement-management-external-users.md)
- [Dizininizde bulunmayan kullanıcılar için erişimi yönetir](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
