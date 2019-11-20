---
title: include dosyası
description: include dosyası
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 6f2b5eb96eeb1c4b7d07219d5fe54a8a0ca9e28a
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73413021"
---
## <a name="for-users-in-your-directory"></a>Dizininizdeki kullanıcılar için

Dizininizdeki kullanıcıların bu erişim paketini isteyebilmesini sağlamak istiyorsanız bu adımları izleyin. İstek ilkesini tanımlarken, bireysel kullanıcıları veya daha yaygın kullanıcı gruplarını belirtebilirsiniz. Örneğin, kuruluşunuz **tüm çalışanlar**gibi bir gruba zaten sahip olabilir.  Bu grup, erişim isteyebilen kullanıcılar için ilkeye eklenirse, o grubun herhangi bir üyesi daha sonra erişim isteğinde bulunabilir.

1. **Erişim isteyebilen kullanıcılar** bölümünde, **dizininizdeki kullanıcılar için**öğesine tıklayın.

    Bu seçeneği belirlediğinizde, dizininizdeki kim tarafından bu erişim paketini isteyebileceğiniz konusunda daha fazla belirginleştirmek için yeni seçenekler görünür.

    ![Erişim paketi Istekleri-dizininizdeki kullanıcılar Için](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Aşağıdaki seçeneklerden birini belirleyin:

    |  |  |
    | --- | --- |
    | **Belirli kullanıcılar ve gruplar** | Yalnızca dizininizdeki Kullanıcı ve grupların bu erişim paketini isteyebilmesini istiyorsanız bu seçeneği belirleyin. |
    | **Tüm Üyeler (konuklar hariç)** | Dizininizdeki tüm üye kullanıcılarının bu erişim paketini isteyebilmesini istiyorsanız bu seçeneği belirleyin. Bu seçenek, dizininize davet ettiğiniz herhangi bir konuk kullanıcıyı içermez. |
    | **Tüm kullanıcılar (konuklar dahil)** | Dizininizdeki tüm üye kullanıcılarının ve konuk kullanıcıların bu erişim paketini isteyebilmesini istiyorsanız bu seçeneği belirleyin. |

    Konuk kullanıcılar, [Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md)ile dizininize davet edilen dış kullanıcılara başvurur. Üye kullanıcılar ve Konuk kullanıcılar arasındaki farklar hakkında daha fazla bilgi için bkz. [Azure Active Directory varsayılan kullanıcı Izinleri nelerdir?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. **Belirli kullanıcılar ve gruplar**' ı seçtiyseniz, **Kullanıcı ve Grup Ekle**' ye tıklayın.

1. Kullanıcıları ve grupları seç bölmesinde, eklemek istediğiniz kullanıcıları ve grupları seçin.

    ![Erişim paketi Istekleri-kullanıcıları ve grupları seçin](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Kullanıcıları ve grupları eklemek için **Seç** ' e tıklayın.

1. [Onay](#approval) bölümüne atlayın.

## <a name="for-users-not-in-your-directory"></a>Dizininizde bulunmayan kullanıcılar için

Dizininizde bulunmayan kullanıcıların bu erişim paketini isteyebilmesini sağlamak istiyorsanız bu adımları izleyin. **Dizininizde olmayan kullanıcılar** , başka BIR Azure ad dizini veya etki alanındaki kullanıcılara başvurur ve henüz dizininizden davet edilmemiş olabilir. Azure AD dizinleri **işbirliği kısıtlamalarında**davetlere izin verecek şekilde yapılandırılmalıdır. Daha fazla bilgi için bkz. [B2B dış Işbirliğini etkinleştirme ve konukları kimlerin davet edebileceğini yönetme](../articles/active-directory/b2b/delegate-invitations.md).

> [!NOTE]
> Henüz, isteğiniz onaylanmış veya otomatik olarak onaylanan dizininizde olmayan bir kullanıcı için bir Konuk Kullanıcı hesabı oluşturulur. Konuk davet edilecek, ancak davet e-postası almacaktır. Bunun yerine, bunlara erişim paketi ataması teslim edildiğinde bir e-posta gönderilir. Varsayılan olarak, Konuk kullanıcının artık herhangi bir erişim paketi ataması olmadığında, son atamasının süresi dolduğunda veya iptal edildiğinden, bu Konuk Kullanıcı hesabının oturum açması engellenir ve daha sonra silinir. Konuk kullanıcılarınızın, hiçbir erişim paketi ataması olmasa bile dizininizden sonsuza kadar kalmasını istiyorsanız, Yetkilendirme Yönetimi yapılandırmanızın ayarlarını değiştirebilirsiniz. Konuk Kullanıcı nesnesi hakkında daha fazla bilgi için, [Azure ACTIVE DIRECTORY B2B işbirliği kullanıcısının özelliklerine](../articles/active-directory/b2b/user-properties.md)bakın.

1. **Erişim isteyebilen kullanıcılar** bölümünde, **dizininizde olmayan kullanıcılar için**öğesine tıklayın.

    Bu seçeneği belirlediğinizde yeni seçenekler görüntülenir.

    ![Erişim paketi Istekleri-dizininizde olmayan kullanıcılar Için](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Aşağıdaki seçeneklerden birini belirleyin:

    |  |  |
    | --- | --- |
    | **Belirli bağlı kuruluşlar** | Yöneticinizin daha önce eklediği kuruluşların listesinden seçim yapmak istiyorsanız bu seçeneği belirleyin. Seçili kuruluşların tüm kullanıcıları, bu erişim paketini isteyebilir. |
    | **Tüm bağlı kuruluşlar** | Tüm bağlı kuruluşlardaki tüm kullanıcıların bu erişim paketini isteyebilmesini istiyorsanız bu seçeneği belirleyin. |

    Bağlı bir kuruluş, ile ilişkiniz olan bir dış Azure AD dizini veya etki alanıdır.

1. **Belirli bağlı kuruluşları**seçtiyseniz, yöneticinizin daha önce eklediği bağlı kuruluşların listesinden seçmek Için **Dizin Ekle** ' ye tıklayın.

1. Daha önce bağlı bir kuruluşun aranacağı adı veya etki alanı adını yazın.

    ![Erişim paketi-Istekler-Dizin Seçme](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    İşbirliği yapmak istediğiniz kuruluş listede yoksa, yöneticinizden onu bağlı bir kuruluş olarak eklemesini isteyebilirsiniz. Daha fazla bilgi için bkz. [bağlı kuruluş ekleme](../articles/active-directory/governance/entitlement-management-organization.md).

1. Bağlı Kuruluşlarınızın tümünü seçtikten sonra **Seç**' e tıklayın.

    > [!NOTE]
    > Seçili bağlı kuruluşların tüm kullanıcıları bu erişim paketini isteyebilecektir. Bu, Azure AD 'deki kullanıcıları, bu etki alanları Azure B2B izin verilenler veya reddetme listesi tarafından engellenmediği durumlar dışında, kuruluş ile ilişkili tüm alt etki alanlarından de içerir. Daha fazla bilgi için, bkz. [belirli kuruluşlardan B2B kullanıcılarına Izin verme veya bu kullanıcıların davetlerini engelleme](../articles/active-directory/b2b/allow-deny-list.md).

1. [Onay](#approval) bölümüne atlayın.

## <a name="none-administrator-direct-assignments-only"></a>Hiçbiri (yalnızca yönetici doğrudan atamaları)

Erişim isteklerini atlamak ve yöneticilerin bu erişim paketine belirli kullanıcıları doğrudan atamasını sağlamak istiyorsanız bu adımları izleyin. Kullanıcıların erişim paketini istemesi gerekmez. Yaşam döngüsü ayarlarını ayarlamaya devam edebilirsiniz, ancak hiçbir istek ayarı yoktur.

1. **Erişim Isteyebilen kullanıcılar** bölümünde Hiçbiri ' ne tıklayın **(yalnızca yönetici doğrudan atamaları**).

    ![Erişim paketi-Istekler-hiçbiri yönetici yalnızca doğrudan atamalar](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Erişim paketini oluşturduktan sonra, erişim paketine belirli iç ve dış kullanıcıları doğrudan atayabilirsiniz. Bir dış Kullanıcı belirtirseniz, dizininizde bir Konuk Kullanıcı hesabı oluşturulur. Bir kullanıcıyı doğrudan atama hakkında daha fazla bilgi için bkz. [bir erişim paketi için atamaları görüntüleme, ekleme ve kaldırma](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. [Istekleri etkinleştir](#enable-requests) bölümüne atlayın.

## <a name="approval"></a>Onay

Onay bölümünde, kullanıcılar bu erişim paketini istemesi durumunda bir onayın gerekli olup olmadığını belirtirsiniz. Onay ayarları aşağıdaki şekilde çalışır:

- Seçili onaylayanlardan veya geri dönüş onaylayanlardan yalnızca birinin bir isteği onaylaması gerekir. Tüm onaylayanlardan onay gerekli değildir.
- Onay kararı, onaylayan kullanıcının isteği gözden geçirmelerine göre belirlenir.

Daha önce seçtiğiniz kullanıcıların onay ayarlarını belirtmek için bu adımları izleyin.

1. Seçili kullanıcılardan gelen isteklere onay gerektirmek için **onay gerektir** seçeneğini **Evet**olarak ayarlayın. İsteklerin otomatik olarak onaylanmasını sağlamak için, geçiş seçeneğini **Hayır**olarak ayarlayın.

1. Kullanıcıların erişim paketi istemek için bir gerekçe sağlamasını gerektirmek için, **istek sahibine iste** modunu **Evet**olarak ayarlayın.

    ![Erişim paketi Istekleri-onay ayarları](./media/active-directory-entitlement-management-request-policy/approval.png)

### <a name="single-stage-approval"></a>Tek aşamalı onay

1. Onaylayanlar için, **onaylayan olarak yönetici** ' yi seçin veya **belirli onaylayanları**seçin.

    ![Erişim paketi Istekleri-tek aşamalı ayarlar](./media/active-directory-entitlement-management-request-policy/approval-single-stage.png)

1. Yöneticiyi onaylayan olarak seçtiyseniz, yetkilendirme yönetiminin yöneticiyi bulamamasını sağlamak için dizininizdeki bir veya daha fazla Kullanıcı ya da Grup seçmek üzere **geri dönüş Ekle** ' ye tıklayın.

    Yönetici, kullanıcının Azure AD profilinde **yönetici** özniteliği tarafından belirlenir. Daha fazla bilgi için, bkz. [Azure Active Directory kullanarak kullanıcının profil bilgilerini ekleme veya güncelleştirme](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Belirli onaylayanları Seç ' i seçtiyseniz, dizininizdeki bir veya daha fazla Kullanıcı ya da grubun onaylayanlarını seçmek için **onaylayan Ekle** ' ye tıklayın.

1. **Kararın kaç gün içinde yapılması gerekir?** , bir onaylayanın bu erişim paketi için bir isteği gözden geçirmesi gereken gün sayısını belirtin.

    Bu süre içinde bir istek onaylanmamışsa, otomatik olarak reddedilir. Kullanıcının erişim paketi için başka bir istek göndermesi gerekir.

1. Kullanıcıların erişim paketini talep etmek için bir gerekçe sağlamasını gerektirmek için, **onaylayan gerekçe iste** seçeneğini **Evet**olarak ayarlayın.

    Bir gerekçe diğer onaylayanlara ve istek sahibine görünür.

### <a name="alternate-approvers"></a>Alternatif onaylayanlar

İstekleri onaylayabilecek birincil onaylayanları belirtmenin yanı sıra, alternatif onaylayanlar de belirtebilirsiniz. Bu, isteklerin süresi dolmadan önce onaylanmış veya reddedilmiş olduğundan emin olmaya yardımcı olur (zaman aşımı).

Alternatif onaylamalarda, birincil onaylayanın isteği onaylaması veya reddetmemiş olması durumunda, bekleyen istek, ilke kurulumu sırasında belirttiğiniz iletme zamanlaması başına alternatif onaylayanlarına iletilir. Bekleyen isteği onaylamak veya reddetmek için bir e-posta alırlar.

İstek alternatif onaylayanlara iletilirse, birincil onaylayanlar isteği yine de onaylayabilir veya reddedebilir. Alternatif onaylayanlar, bekleyen isteği onaylamak veya reddetmek için, birincil onaylayanlar olarak aynı erişim sitemin aynısını kullanır.

İnsanları veya gruplarını birincil onaylayanlar ve alternatif onaylayanlar olacak şekilde listeliyoruz. Lütfen birincil onaylayanlar ve alternatif onaylayanlar olacak farklı kişi kümelerini listediğinizden emin olun.
Örneğin, Gamze ve Bob 'u birincil onaylayan olarak listedıysanız, diğer onaylayan olarak Carol ve Davve listeleyin. Bir erişim paketine alternatif onaylayanlar eklemek için aşağıdaki adımları kullanın:

1. **Gelişmiş istek ayarlarını göster**' e tıklayın.

    ![Erişim paketi-Ilke-Gelişmiş istek ayarlarını göster](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. **Herhangi bir eylem yapılmadı, alternatif onaylayanlara ilet mi?** **Evet**olarak değiştirin.

1. **Alternatif onaylayanlar Ekle** ' ye tıklayın ve listeden alternatif onaylayanı seçin.

    ![Erişim paketi-Ilke-alternatif onaylayanlar ekleme](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. **Kaç gün sonra diğer onaylayanlara ilet** kutusunda, onaylayanın bir isteği onaylaması veya reddetmesi için sahip olduğu gün sayısına koyun. Onaylayan, istek süresinden önce isteği onaylamadıysa veya reddetmezse, isteğin süresi dolar (zaman aşımı) ve kullanıcının erişim paketi için başka bir istek göndermesi gerekecektir. 

    İstekler yalnızca, istek süresi yarım hayata geçtikten sonra bir gün daha, alternatif onaylayanlara iletilebilir. Bu örnekte, istek süresi 14 gündür. Bu, istek süresinin 7. gün yarı yaşam süresine eriştiği anlamına gelir. Bu nedenle istek, 8. günden daha önce değil iletilebilir. Ayrıca, istekler istek süresinin son gününde alternatif onaylayana iletilemez. Bu nedenle, isteğin iletilebilmesi için en son, gün 13 ' dir.

## <a name="enable-requests"></a>İstekleri etkinleştir

1. Erişim paketinin istek ilkesindeki kullanıcılar için hemen kullanılabilir olmasını istiyorsanız, etkinleştirmek için **Evet** ' i tıklatın.

    Erişim paketini oluşturma işlemi bittikten sonra bunu gelecekte etkinleştirebilirsiniz.

    Hiçbiri ' ni seçtiyseniz **(yalnızca yönetici doğrudan atamaları)** ve Etkinleştir ' i ayarlarsanız **, Yöneticiler**bu erişim paketini doğrudan atayamazlar.

    ![Erişim paketi-Ilke-ilke ayarını etkinleştir](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. **İleri**’ye tıklayın.
