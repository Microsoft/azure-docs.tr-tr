---
title: Azure AD Yetkilendirme Yönetimi 'nde erişim paketi için değişiklik isteği ayarları-Azure Active Directory
description: Azure Active Directory yetkilendirme yönetimi 'nde erişim paketi için istek ayarlarını değiştirme hakkında bilgi edinin.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87792b8469c78e20a8716bd925d71933f9d97c5e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560497"
---
# <a name="change-request-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD yetkilendirme yönetiminde bir erişim paketi için değişiklik isteği ayarları

Erişim paketi Yöneticisi olarak, ilkeyi düzenleyerek veya yeni bir ilke ekleyerek herhangi bir zamanda erişim paketi talep edebilen kullanıcıları değiştirebilirsiniz. Bu makalede, var olan bir erişim paketinin istek ayarlarının nasıl değiştirileceği açıklanır.

## <a name="choose-between-one-or-multiple-policies"></a>Bir veya birden çok ilke arasında seçim yapın

Bir ilke ile bir erişim paketi talep edebilen kim tarafından belirtilme yöntemi. Yeni bir ilke oluşturmadan veya bir erişim paketinde mevcut bir ilkeyi düzenlemeden önce, erişim paketinin kaç ilke için gerekli olduğunu belirlemeniz gerekir. 

Bir erişim paketi oluşturduğunuzda, ilke oluşturan istek ayarını belirtirsiniz. Çoğu erişim paketi tek bir ilkeye sahip olur, ancak tek bir erişim paketinde birden çok ilke olabilir. Farklı Kullanıcı kümelerine farklı istek ve onay ayarları içeren atamalar verilmesini sağlamak istiyorsanız, bir erişim paketi için birden çok ilke oluşturabilirsiniz. 

Örneğin, tek bir ilke, aynı erişim paketine iç ve dış kullanıcı atamak için kullanılamaz. Ancak, biri iç kullanıcılar ve diğeri dış kullanıcılar için aynı erişim paketinde iki ilke oluşturabilirsiniz. Bir kullanıcı için uygulanan birden çok ilke varsa, kendilerine atanması gereken ilkeyi seçme istekleri sırasında istenir. Aşağıdaki diyagramda, iki ilkeye sahip bir erişim paketi gösterilmektedir.

![Erişim paketinde birden çok ilke](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

### <a name="how-many-policies-will-i-need"></a>Kaç ilkeye ihtiyacım olacak?

| Senaryo | İlke sayısı |
| --- | --- |
| Dizinimde tüm kullanıcıların bir erişim paketi için aynı istek ve onay ayarlarına sahip olmasını istiyorum | Bir |
| Belirli bağlı kuruluşlardaki tüm kullanıcıların bir erişim paketi isteyebilmesini istiyorum | Bir |
| Dizinimde bulunan kullanıcılara ve ayrıca dizinimin dışındaki kullanıcıların erişim paketi istemesine izin vermek istiyorum | Birden çok |
| Bazı kullanıcılar için farklı onay ayarları belirtmek istiyorum | Birden çok |
| Diğer kullanıcıların erişimini genişletecek bazı kullanıcıların paket atamalarına erişmesini istiyorum | Birden çok |

Birden çok ilke uyguladığınızda kullanılan öncelik mantığı hakkında daha fazla bilgi için bkz. [birden çok ilke](entitlement-management-troubleshoot.md#multiple-policies
).

## <a name="open-an-existing-access-package-and-add-a-new-policy-of-request-settings"></a>Var olan bir erişim paketini açın ve istek ayarları ' nın yeni bir ilkesini ekleyin

Farklı istek ve onay ayarlarına sahip olması gereken bir kullanıcı kümesine sahipseniz, muhtemelen yeni bir ilke oluşturmanız gerekecektir. Var olan bir erişim paketine yeni bir ilke eklemeye başlamak için şu adımları izleyin:

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik** Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. **İlkeler** ' e ve ardından **ilke Ekle**' ye tıklayın.

1. **Temel bilgiler** sekmesinde başlayacaksınız. İlke için bir ad ve açıklama yazın.

    ![Ad ve açıklama ile ilke oluştur](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. **İleri** ' ye tıklayarak **istekler** sekmesini açın.

1. Erişim ayarı **talep edebilen kullanıcıları** değiştirin. Aşağıdaki seçeneklerden biriyle ayarı değiştirmek için aşağıdaki bölümlerdeki adımları kullanın: 
    - [Dizininizdeki kullanıcılar için](#for-users-in-your-directory) 
    - [Dizininizde bulunmayan kullanıcılar için](#for-users-not-in-your-directory)
    - [Hiçbiri (yalnızca yönetici doğrudan atamaları)](#none-administrator-direct-assignments-only)

## <a name="for-users-in-your-directory"></a>Dizininizdeki kullanıcılar için

Dizininizdeki kullanıcıların bu erişim paketini isteyebilmesini sağlamak istiyorsanız bu adımları izleyin. İstek ilkesini tanımlarken, bireysel kullanıcıları veya daha yaygın kullanıcı gruplarını belirtebilirsiniz. Örneğin, kuruluşunuz **tüm çalışanlar** gibi bir gruba zaten sahip olabilir.  Bu grup, erişim isteyebilen kullanıcılar için ilkeye eklenirse, o grubun herhangi bir üyesi daha sonra erişim isteğinde bulunabilir.

1. **Erişim isteyebilen kullanıcılar** bölümünde, **dizininizdeki kullanıcılar için** öğesine tıklayın.

    Bu seçeneği belirlediğinizde, dizininizdeki kim tarafından bu erişim paketini isteyebileceğiniz konusunda daha fazla belirginleştirmek için yeni seçenekler görünür.

    ![Erişim paketi Istekleri-dizininizdeki kullanıcılar Için](./media/entitlement-management-access-package-request-policy/for-users-in-your-directory.png)

1. Aşağıdaki seçeneklerden birini belirtin:

    |  | Description |
    | --- | --- |
    | **Belirli kullanıcılar ve gruplar** | Yalnızca dizininizdeki Kullanıcı ve grupların bu erişim paketini isteyebilmesini istiyorsanız bu seçeneği belirleyin. |
    | **Tüm Üyeler (konuklar hariç)** | Dizininizdeki tüm üye kullanıcılarının bu erişim paketini isteyebilmesini istiyorsanız bu seçeneği belirleyin. Bu seçenek, dizininize davet ettiğiniz tüm konuk kullanıcılarını içermez. |
    | **Tüm kullanıcılar (konuklar dahil)** | Dizininizdeki tüm üye kullanıcılarının ve konuk kullanıcıların bu erişim paketini isteyebilmesini istiyorsanız bu seçeneği belirleyin. |

    Konuk kullanıcılar, [Azure AD B2B](../external-identities/what-is-b2b.md)ile dizininize davet edilen dış kullanıcılara başvurur. Üye kullanıcılar ve Konuk kullanıcılar arasındaki farklar hakkında daha fazla bilgi için bkz. [Azure Active Directory varsayılan kullanıcı Izinleri nelerdir?](../fundamentals/users-default-permissions.md).

1. **Belirli kullanıcılar ve gruplar**' ı seçtiyseniz, **Kullanıcı ve Grup Ekle**' ye tıklayın.

1. Kullanıcıları ve grupları seç bölmesinde, eklemek istediğiniz kullanıcıları ve grupları seçin.

    ![Erişim paketi Istekleri-kullanıcıları ve grupları seçin](./media/entitlement-management-access-package-request-policy/select-users-groups.png)

1. Kullanıcıları ve grupları eklemek için **Seç** ' e tıklayın.

1. Onay gerektirmek istiyorsanız, onay ayarlarını yapılandırmak için [Azure AD Yetkilendirme Yönetimi 'nde erişim paketi için onay ayarlarını değiştirme](entitlement-management-access-package-approval-policy.md) bölümündeki adımları kullanın.

1. [Istekleri etkinleştirme](#enable-requests) bölümüne gidin.
 
## <a name="for-users-not-in-your-directory"></a>Dizininizde bulunmayan kullanıcılar için

 **Dizininizde olmayan kullanıcılar** , başka BIR Azure ad dizini veya etki alanındaki kullanıcılara başvurur. Bu kullanıcılar henüz dizininize davet edilmemiş olabilir. Azure AD dizinleri **işbirliği kısıtlamalarında** davetlere izin verecek şekilde yapılandırılmalıdır. Daha fazla bilgi için bkz. [B2B dış Işbirliğini etkinleştirme ve konukları kimlerin davet edebileceğini yönetme](../external-identities/delegate-invitations.md).

> [!NOTE]
> Henüz, isteğiniz onaylanmış veya otomatik olarak onaylanan dizininizde olmayan bir kullanıcı için bir Konuk Kullanıcı hesabı oluşturulur. Konuk davet edilecek, ancak davet e-postası almacaktır. Bunun yerine, bunlara erişim paketi ataması teslim edildiğinde bir e-posta gönderilir. Varsayılan olarak, Konuk kullanıcının artık herhangi bir erişim paketi ataması olmadığında, son atamasının süresi dolduğunda veya iptal edildiğinden, bu Konuk Kullanıcı hesabının oturum açması engellenir ve daha sonra silinir. Konuk kullanıcılarınızın, hiçbir erişim paketi ataması olmasa bile dizininizden sonsuza kadar kalmasını istiyorsanız, Yetkilendirme Yönetimi yapılandırmanızın ayarlarını değiştirebilirsiniz. Konuk Kullanıcı nesnesi hakkında daha fazla bilgi için, [Azure ACTIVE DIRECTORY B2B işbirliği kullanıcısının özelliklerine](../external-identities/user-properties.md)bakın.

Dizininizdeki kullanıcıların bu erişim paketini istemesi için izin vermek istiyorsanız aşağıdaki adımları izleyin:

1. **Erişim isteyebilen kullanıcılar** bölümünde, **dizininizde olmayan kullanıcılar için** öğesine tıklayın.

    Bu seçeneği belirlediğinizde yeni seçenekler görüntülenir.

    ![Erişim paketi Istekleri-dizininizde olmayan kullanıcılar Için](./media/entitlement-management-access-package-request-policy/for-users-not-in-your-directory.png)

1. Aşağıdaki seçeneklerden birini belirtin:

    |  | Description |
    | --- | --- |
    | **Belirli bağlı kuruluşlar** | Yöneticinizin daha önce eklediği kuruluşların listesinden seçim yapmak istiyorsanız bu seçeneği belirleyin. Seçili kuruluşların tüm kullanıcıları bu erişim paketini isteyebilir. |
    | **Tüm yapılandırılmış bağlı kuruluşlar** | Tüm yapılandırılmış bağlı kuruluşlarınızın tüm kullanıcıları bu erişim paketini talep edebilir ise bu seçeneği belirleyin. Yalnızca yapılandırılan bağlı kuruluşların kullanıcıları, yapılandırılmış tüm kuruluşlardan kullanıcılara gösterilen erişim paketleri isteyebilir. |
    | **Tüm kullanıcılar (tüm bağlı kuruluşlar + tüm yeni dış kullanıcılar)** | İnternet üzerindeki herhangi bir kullanıcının bu erişim paketini isteyebilmesi gerekiyorsa bu seçeneği belirleyin.  Dizininizdeki bağlı bir kuruluşa ait değilse, paket istediklerinde bu kullanıcılar için bağlı bir kuruluş otomatik olarak oluşturulur. Otomatik olarak oluşturulan bağlı kuruluş, **Önerilen** bir durumda olacaktır. Önerilen durum hakkında daha fazla bilgi için bkz. [bağlı kuruluşların durum özellikleri](entitlement-management-organization.md#state-properties-of-connected-organizations). |

    Bağlı bir kuruluş, ile ilişkiniz olan bir dış Azure AD dizini veya etki alanıdır.

1. **Belirli bağlı kuruluşları** seçtiyseniz, yöneticinizin daha önce eklediği bağlı kuruluşların listesinden seçmek Için **Dizin Ekle** ' ye tıklayın.

1. Daha önce bağlı bir kuruluşun aranacağı adı veya etki alanı adını yazın.

    ![Erişim paketi-Istekler-Dizin Seçme](./media/entitlement-management-access-package-request-policy/select-directories.png)

    İşbirliği yapmak istediğiniz kuruluş listede yoksa, yöneticinizden onu bağlı bir kuruluş olarak eklemesini isteyebilirsiniz. Daha fazla bilgi için bkz. [bağlı kuruluş ekleme](entitlement-management-organization.md).

1. Bağlı Kuruluşlarınızın tümünü seçtikten sonra **Seç**' e tıklayın.

    > [!NOTE]
    > Seçili bağlı kuruluşların tüm kullanıcıları bu erişim paketini isteyebilecektir. Bu, Azure AD 'deki kullanıcıları, bu etki alanları Azure B2B izin verilenler veya reddetme listesi tarafından engellenmediği durumlar dışında, kuruluş ile ilişkili tüm alt etki alanlarından de içerir. Daha fazla bilgi için, bkz. [belirli kuruluşlardan B2B kullanıcılarına Izin verme veya bu kullanıcıların davetlerini engelleme](../external-identities/allow-deny-list.md).

1. Onay gerektirmek istiyorsanız, onay ayarlarını yapılandırmak için [Azure AD Yetkilendirme Yönetimi 'nde erişim paketi için onay ayarlarını değiştirme](entitlement-management-access-package-approval-policy.md) bölümündeki adımları kullanın.
 
1. [Istekleri etkinleştirme](#enable-requests) bölümüne gidin.

## <a name="none-administrator-direct-assignments-only"></a>Hiçbiri (yalnızca yönetici doğrudan atamaları)

Erişim isteklerini atlamak ve yöneticilerin bu erişim paketine belirli kullanıcıları doğrudan atamasını sağlamak istiyorsanız bu adımları izleyin. Kullanıcıların erişim paketini istemesi gerekmez. Yaşam döngüsü ayarlarını ayarlamaya devam edebilirsiniz, ancak hiçbir istek ayarı yoktur.

1. **Erişim Isteyebilen kullanıcılar** bölümünde Hiçbiri ' ne tıklayın **(yalnızca yönetici doğrudan atamaları**).

    ![Erişim paketi-Istekler-hiçbiri yönetici yalnızca doğrudan atamalar](./media/entitlement-management-access-package-request-policy/none-admin-direct-assignments-only.png)

    Erişim paketini oluşturduktan sonra, erişim paketine belirli iç ve dış kullanıcıları doğrudan atayabilirsiniz. Bir dış Kullanıcı belirtirseniz, dizininizde bir Konuk Kullanıcı hesabı oluşturulur. Bir kullanıcıyı doğrudan atama hakkında daha fazla bilgi için bkz. [bir erişim paketi için atamaları görüntüleme, ekleme ve kaldırma](entitlement-management-access-package-assignments.md).

1. [Istekleri etkinleştir](#enable-requests) bölümüne atlayın.


## <a name="open-and-edit-an-existing-policy-of-request-settings"></a>İstek ayarlarının var olan bir ilkesini açın ve düzenleyin

Bir erişim paketinin istek ve onay ayarlarını değiştirmek için, ilgili ilkeyi açmanız gerekir. Bir erişim paketinin istek ayarlarını açmak ve düzenlemek için aşağıdaki adımları izleyin:

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik** Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. **İlkeler** ' e tıklayın ve ardından düzenlemek istediğiniz ilkeye tıklayın.

    Ilke ayrıntıları bölmesi sayfanın altında açılır.

    ![Erişim paketi-Ilke ayrıntıları bölmesi](./media/entitlement-management-shared/policy-details.png)

1. İlkeyi düzenlemek için **Düzenle** ' ye tıklayın.

    ![Erişim paketi-ilkeyi Düzenle](./media/entitlement-management-shared/policy-edit.png)

1. İstek ayarlarını açmak için **istekler** sekmesine tıklayın.

1. İstek ayarlarını gerektiği şekilde değiştirmek için önceki bölümlerdeki adımları kullanın.

1. [Istekleri etkinleştirme](#enable-requests) bölümüne gidin.

## <a name="enable-requests"></a>İstekleri etkinleştir

1. Erişim paketinin istek ilkesindeki kullanıcıların istemesi için hemen kullanılabilir olmasını istiyorsanız, etkinleştir seçeneğini **Evet** olarak taşıyın.

    Erişim paketini oluşturma işlemi bittikten sonra bunu gelecekte etkinleştirebilirsiniz.

    Hiçbiri ' ni seçtiyseniz **(yalnızca yönetici doğrudan atamaları)** ve Etkinleştir **' i ayarlarsanız, Yöneticiler** bu erişim paketini doğrudan atayamaz.

    ![Erişim paketi-Ilke-ilke ayarını etkinleştir](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. **İleri**’ye tıklayın.

1. Bir erişim paketine erişim isteğinde bulunulmak üzere istek sahibine ek bilgi sağlamak istiyorsanız, istek sahibi bilgilerini (Önizleme) yapılandırmak için [Azure AD Yetkilendirme Yönetimi 'nde bir erişim paketinin onay ve istek sahibi bilgilerini değiştirme (Önizleme) ayarlarını değiştirin](entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview) bölümündeki adımları kullanın.

1. Yaşam döngüsü ayarlarını yapılandırın.

1. Bir ilkeyi düzenliyorsanız **Güncelleştir**' e tıklayın. Yeni bir ilke ekliyorsanız **Oluştur**' a tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Erişim paketinin onay ayarlarını değiştirme](entitlement-management-access-package-approval-policy.md)
- [Erişim paketi için yaşam döngüsü ayarlarını değiştirme](entitlement-management-access-package-lifecycle-policy.md)
- [Erişim paketi isteklerini görüntüleme](entitlement-management-access-package-requests.md)
