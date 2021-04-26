---
title: include dosyası
description: include dosyası
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 1bbc549a9ba457d5f515df25d6fdce0c2ede6f2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105582444"
---
## <a name="for-users-in-your-directory"></a>Dizininizdeki kullanıcılar için

Dizininizdeki kullanıcıların bu erişim paketini isteyebilmesini sağlamak istiyorsanız bu adımları izleyin. İstek ilkesini tanımlarken, bireysel kullanıcıları veya daha yaygın kullanıcı gruplarını belirtebilirsiniz. Örneğin, kuruluşunuz **tüm çalışanlar** gibi bir gruba zaten sahip olabilir.  Bu grup, erişim isteyebilen kullanıcılar için ilkeye eklenirse, o grubun herhangi bir üyesi daha sonra erişim isteğinde bulunabilir.

1. **Erişim isteyebilen kullanıcılar** bölümünde, **dizininizdeki kullanıcılar için** öğesine tıklayın.

    Bu seçeneği belirlediğinizde, dizininizdeki kim tarafından bu erişim paketini isteyebileceğiniz konusunda daha fazla belirginleştirmek için yeni seçenekler görünür.

    ![Erişim paketi Istekleri-dizininizdeki kullanıcılar Için](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Aşağıdaki seçeneklerden birini belirtin:

    |  | Description |
    | --- | --- |
    | **Belirli kullanıcılar ve gruplar** | Yalnızca dizininizdeki Kullanıcı ve grupların bu erişim paketini isteyebilmesini istiyorsanız bu seçeneği belirleyin. |
    | **Tüm Üyeler (konuklar hariç)** | Dizininizdeki tüm üye kullanıcılarının bu erişim paketini isteyebilmesini istiyorsanız bu seçeneği belirleyin. Bu seçenek, dizininize davet ettiğiniz tüm konuk kullanıcılarını içermez. |
    | **Tüm kullanıcılar (konuklar dahil)** | Dizininizdeki tüm üye kullanıcılarının ve konuk kullanıcıların bu erişim paketini isteyebilmesini istiyorsanız bu seçeneği belirleyin. |

    Konuk kullanıcılar, [Azure AD B2B](../articles/active-directory/external-identities/what-is-b2b.md)ile dizininize davet edilen dış kullanıcılara başvurur. Üye kullanıcılar ve Konuk kullanıcılar arasındaki farklar hakkında daha fazla bilgi için bkz. [Azure Active Directory varsayılan kullanıcı Izinleri nelerdir?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. **Belirli kullanıcılar ve gruplar**' ı seçtiyseniz, **Kullanıcı ve Grup Ekle**' ye tıklayın.

1. Kullanıcıları ve grupları seç bölmesinde, eklemek istediğiniz kullanıcıları ve grupları seçin.

    ![Erişim paketi Istekleri-kullanıcıları ve grupları seçin](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Kullanıcıları ve grupları eklemek için **Seç** ' e tıklayın.

1. [Onay](#approval) bölümüne atlayın.

## <a name="for-users-not-in-your-directory"></a>Dizininizde bulunmayan kullanıcılar için

 **Dizininizde olmayan kullanıcılar** , başka BIR Azure ad dizini veya etki alanındaki kullanıcılara başvurur. Bu kullanıcılar henüz dizininize davet edilmemiş olabilir. Azure AD dizinleri **işbirliği kısıtlamalarında** davetlere izin verecek şekilde yapılandırılmalıdır. Daha fazla bilgi için bkz. [B2B dış Işbirliğini etkinleştirme ve konukları kimlerin davet edebileceğini yönetme](../articles/active-directory/external-identities/delegate-invitations.md).

> [!NOTE]
> Henüz, isteğiniz onaylanmış veya otomatik olarak onaylanan dizininizde olmayan bir kullanıcı için bir Konuk Kullanıcı hesabı oluşturulur. Konuk davet edilecek, ancak davet e-postası almacaktır. Bunun yerine, bunlara erişim paketi ataması teslim edildiğinde bir e-posta gönderilir. Varsayılan olarak, Konuk kullanıcının artık herhangi bir erişim paketi ataması olmadığında, son atamasının süresi dolduğunda veya iptal edildiğinden, bu Konuk Kullanıcı hesabının oturum açması engellenir ve daha sonra silinir. Konuk kullanıcılarınızın, hiçbir erişim paketi ataması olmasa bile dizininizden sonsuza kadar kalmasını istiyorsanız, Yetkilendirme Yönetimi yapılandırmanızın ayarlarını değiştirebilirsiniz. Konuk Kullanıcı nesnesi hakkında daha fazla bilgi için, [Azure ACTIVE DIRECTORY B2B işbirliği kullanıcısının özelliklerine](../articles/active-directory/external-identities/user-properties.md)bakın.

Dizininizdeki kullanıcıların bu erişim paketini istemesi için izin vermek istiyorsanız aşağıdaki adımları izleyin:

1. **Erişim isteyebilen kullanıcılar** bölümünde, **dizininizde olmayan kullanıcılar için** öğesine tıklayın.

    Bu seçeneği belirlediğinizde yeni seçenekler görüntülenir.

    ![Erişim paketi Istekleri-dizininizde olmayan kullanıcılar Için](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Aşağıdaki seçeneklerden birini belirtin:

    |  | Description |
    | --- | --- |
    | **Belirli bağlı kuruluşlar** | Yöneticinizin daha önce eklediği kuruluşların listesinden seçim yapmak istiyorsanız bu seçeneği belirleyin. Seçili kuruluşların tüm kullanıcıları bu erişim paketini isteyebilir. |
    | **Tüm bağlı kuruluşlar** | Bağlı Kuruluşlarınızın tüm kullanıcıları bu erişim paketini talep edebilir ise bu seçeneği belirleyin. |
    | **Tüm kullanıcılar (tüm bağlı kuruluşlar + tüm yeni dış kullanıcılar)** | Tüm bağlı kuruluşlarınızdaki tüm kullanıcılar bu erişim paketini isteyebilse ve B2B izin verme veya reddetme listesi ayarlarının tüm yeni dış kullanıcılar için öncelikli olması durumunda bu seçeneği belirleyin. |

    Bağlı bir kuruluş, ile ilişkiniz olan bir dış Azure AD dizini veya etki alanıdır.

1. **Belirli bağlı kuruluşları** seçtiyseniz, yöneticinizin daha önce eklediği bağlı kuruluşların listesinden seçmek Için **Dizin Ekle** ' ye tıklayın.

1. Daha önce bağlı bir kuruluşun aranacağı adı veya etki alanı adını yazın.

    ![Erişim paketi-Istekler-Dizin Seçme](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    İşbirliği yapmak istediğiniz kuruluş listede yoksa, yöneticinizden onu bağlı bir kuruluş olarak eklemesini isteyebilirsiniz. Daha fazla bilgi için bkz. [bağlı kuruluş ekleme](../articles/active-directory/governance/entitlement-management-organization.md).

1. Bağlı Kuruluşlarınızın tümünü seçtikten sonra **Seç**' e tıklayın.

    > [!NOTE]
    > Seçili bağlı kuruluşların tüm kullanıcıları bu erişim paketini isteyebilecektir. Bu, Azure AD 'deki kullanıcıları, bu etki alanları Azure B2B izin verilenler veya reddetme listesi tarafından engellenmediği durumlar dışında, kuruluş ile ilişkili tüm alt etki alanlarından de içerir. Daha fazla bilgi için, bkz. [belirli kuruluşlardan B2B kullanıcılarına Izin verme veya bu kullanıcıların davetlerini engelleme](../articles/active-directory/external-identities/allow-deny-list.md).

1. [Onay](#approval) bölümüne atlayın.

## <a name="none-administrator-direct-assignments-only"></a>Hiçbiri (yalnızca yönetici doğrudan atamaları)

Erişim isteklerini atlamak ve yöneticilerin bu erişim paketine belirli kullanıcıları doğrudan atamasını sağlamak istiyorsanız bu adımları izleyin. Kullanıcıların erişim paketini istemesi gerekmez. Yaşam döngüsü ayarlarını ayarlamaya devam edebilirsiniz, ancak hiçbir istek ayarı yoktur.

1. **Erişim Isteyebilen kullanıcılar** bölümünde Hiçbiri ' ne tıklayın **(yalnızca yönetici doğrudan atamaları**).

    ![Erişim paketi-Istekler-hiçbiri yönetici yalnızca doğrudan atamalar](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Erişim paketini oluşturduktan sonra, erişim paketine belirli iç ve dış kullanıcıları doğrudan atayabilirsiniz. Bir dış Kullanıcı belirtirseniz, dizininizde bir Konuk Kullanıcı hesabı oluşturulur. Bir kullanıcıyı doğrudan atama hakkında daha fazla bilgi için bkz. [bir erişim paketi için atamaları görüntüleme, ekleme ve kaldırma](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. [Istekleri etkinleştir](#enable-requests) bölümüne atlayın.

## <a name="approval"></a>Onay

Onay bölümünde, kullanıcılar bu erişim paketini istemesi durumunda bir onayın gerekli olup olmadığını belirtirsiniz. Onay ayarları aşağıdaki şekilde çalışır:

- Seçili onaylayanlardan veya geri dönüş onaylayanlardan yalnızca birinin tek aşamalı onay için bir isteği onaylaması gerekir. 
- Her bir aşamanın seçili onaylayanlardan yalnızca birinin, 2 aşamalı onay için bir isteği onaylaması gerekir.
- Onaylayan, ilkenin erişimi hangi kişiye göre yapılandırdığına bağlı olarak bir yönetici, dahili sponsor veya harici sponsor olabilir.
- Tek veya 2 aşamalı onay için seçili her Onaylayandan onay gerekli değildir.
- Onay kararı, onaylayan kullanıcının isteği gözden geçirmelerine göre belirlenir.

Bir istek ilkesine onaylayanlar ekleme hakkında bir gösterim için aşağıdaki videoyu izleyin:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Bir istek ilkesine çok aşamalı onay ekleme hakkında bir gösterim için aşağıdaki videoyu izleyin:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

Erişim paketine yönelik isteklerin onay ayarlarını belirtmek için şu adımları izleyin:

1. Seçili kullanıcılardan gelen isteklere onay gerektirmek için **onay gerektir** seçeneğini **Evet** olarak ayarlayın. Ya da isteklerin otomatik olarak onaylanmasını sağlamak için, geçiş seçeneğini **Hayır** olarak ayarlayın.

1. Kullanıcıların erişim paketi istemek için bir gerekçe sağlamasını gerektirmek için, **istek sahibine iste** modunu **Evet** olarak ayarlayın.
    
1. Şimdi isteklerin tek veya 2 aşamalı onay gerektirmesine göre belirlenir. Tek aşamalı onay için **1** ' e geçiş yapma veya 2 aşamalı onay için **2** ' **ye geçişi ayarlama** .

    ![Erişim paketi Istekleri-onay ayarları](./media/active-directory-entitlement-management-request-policy/approval.png)

Kaç aşamanın gerekli olduğunu seçtikten sonra onaylayanları eklemek için aşağıdaki adımları kullanın: 

### <a name="single-stage-approval"></a>Tek aşamalı onay

1. **Ilk onaylayanı** ekleyin:
    
    İlke, dizininizdeki kullanıcılar için erişimi yönetecek şekilde ayarlandıysa, yönetici ' yi **onaylayan olarak** seçebilirsiniz. Ya da, belirli bir **kullanıcıyı ekleme ' yi seçtikten sonra,** açılan menüden belirli onaylayanları Seç ' i tıklatarak ekleyebilirsiniz.
    
    ![Erişim paketi-Istekler-Dizin-Ilk onaylayanın kullanıcıları Için](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Bu ilke, dizininizde olmayan kullanıcılar için erişimi yönetecek şekilde ayarlandıysa, **Harici sponsor** veya **dahili sponsor** seçeneğini belirleyebilirsiniz. Ya da belirli onaylayanlara seç altında **Onaylayanlar Ekle** veya gruplar ' a tıklayarak belirli bir kullanıcıyı ekleyin.
    
    ![Erişim paketi-Istekler-Dizin Ilk onaylayan kullanıcılar Için](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. **Yöneticiyi** ilk onaylayan olarak seçtiyseniz, geri dönüş **Ekle** ' ye tıklayarak dizininizdeki bir veya daha fazla kullanıcıyı veya grubu geri dönüş onaylayıcısı olacak şekilde seçin. Yetkilendirme Yönetimi, erişim isteyen kullanıcı için yönetici bulamazsa, geri dönüş onaylayanları isteği alır.

    Yönetici, **yönetici** özniteliği kullanılarak Yetkilendirme Yönetimi tarafından bulunur. Öznitelik, kullanıcının Azure AD 'deki profilidir. Daha fazla bilgi için, bkz. [Azure Active Directory kullanarak kullanıcının profil bilgilerini ekleme veya güncelleştirme](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. **Belirli onaylayanları Seç**' i seçtiyseniz, dizininizdeki bir veya daha fazla Kullanıcı ya da grubun onaylayanlarını seçmek Için **onaylayan Ekle** ' ye tıklayın.

1. Kararın altındaki kutuda **kaç gün içinde yapılmalıdır?**, bir onaylayanın bu erişim paketi için bir isteği gözden geçirmesi gereken gün sayısını belirtin.

    Bu süre içinde bir istek onaylanmamışsa, otomatik olarak reddedilir. Kullanıcının erişim paketi için başka bir istek göndermesi gerekir.

1. Onaylayanlara kararların bir gerekçe vermesini gerektirmek için, onaylayan gerekçe ıste seçeneğini **Evet** olarak ayarlayın.

    Gerekçe diğer onaylayanlara ve istek sahibine görünür.

### <a name="2-stage-approval"></a>2 aşamalı onay

2 aşamalı onay seçtiyseniz ikinci bir onaylayan eklemeniz gerekir.

1. **Ikinci onaylayanı** ekleyin: 
    
    Kullanıcılar dizininizdeki ise, belirli onaylayanları seçin altında **onaylayan Ekle** ' ye tıklayarak ikinci onaylayan olarak belirli bir kullanıcıyı ekleyin.

    ![Erişim paketi Istekleri-Dizin-Ikinci onaylayan kullanıcılar Için](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Kullanıcılar dizininizden değilse, ikinci onaylayan olarak **dahili sponsor** veya **External sponsoru** seçin. Onaylayanı seçtikten sonra geri dönüş onaylayanları ekleyin.

    ![Erişim paketi Istekleri-Ikinci Dizin Için olan kullanıcılar Için-Ikinci onaylayan](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. İkinci onaylayanın onay kutusundaki isteği onaylaması gereken gün sayısını **kaç gün içinde yapmanız gerektiğini** belirtin. 

1. Onaylayan ıste gerekçe **Evet** veya **Hayır** olarak ayarlanmalıdır.

### <a name="alternate-approvers"></a>Alternatif onaylayanlar

İstekleri onaylayabilecek birinci ve ikinci onaylayanları belirtmeye benzer şekilde alternatif onaylayanlar belirtebilirsiniz. Alternatif onaylayanlar olması, isteklerin süresi dolmadan önce onaylanmış veya reddedildiğinden emin olmaya yardımcı olur (zaman aşımı). İlk onaylayanın ve ikinci onaylayanın 2 aşamalı onay için alternatif onaylayanlar listeleyebilirsiniz. 

Alternatif onaylayanlar belirterek, ilk veya ikinci onaylayanlardan isteği onaylayamayan veya reddetmeyen olayda, ilke kurulumu sırasında belirttiğiniz iletme zamanlaması başına bekleyen istek alternatif onaylayanlara iletilir. Bekleyen isteği onaylamak veya reddetmek için bir e-posta alırlar.

İstek alternatif onaylayanlara iletildikten sonra, ilk veya ikinci onaylayanlar isteği yine de onaylayabilir veya reddedebilir. Alternatif onaylayanlar, bekleyen isteği onaylamak veya reddetmek için aynı erişim sitemi kullanır.

Kişiler veya kişi gruplarını onaylayan ve alternatif onaylayanlar olacak şekilde listeliyoruz. Lütfen birinci, ikinci ve alternatif onaylayanlar olacak farklı kişi kümelerini listediğinizden emin olun.
Örneğin, Gamze ve emre 'yi Ilk onaylayan olarak listeleniyorsa, alternatif onaylayanlar olarak Carol ve Davde listesini listeleyin. Bir erişim paketine alternatif onaylayanlar eklemek için aşağıdaki adımları kullanın:

1. Ilk onaylayanın, Ikinci onaylayan veya her ikisinde de **Gelişmiş istek ayarlarını göster**' e tıklayın.

    ![Erişim paketi-Ilke-Gelişmiş istek ayarlarını göster](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. **Herhangi bir eylem yapılmadı, alternatif onaylayanlara ilet mi?** **Evet** olarak değiştirin.

1. **Alternatif onaylayanlar Ekle** ' ye tıklayın ve listeden alternatif onaylayanı seçin.

    ![Erişim paketi-Ilke-alternatif onaylayanlar ekleme](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

    Ilk onaylayan için yöneticiyi onaylayan olarak seçerseniz, alternatif onaylayan alanındaki **ikinci düzey yönetici alternatif onaylayan olarak** ek bir seçeneğe sahip olursunuz. Bu seçeneği belirlerseniz, sistemin ikinci düzey yöneticiyi bulamadığı durumlarda isteği iletmek için bir geri dönüş onaylayan eklemeniz gerekir.

1. **Kaç gün sonra diğer onaylayanlara ilet** kutusunda, onaylayanın bir isteği onaylaması veya reddetmesi için sahip olduğu gün sayısına koyun. Onaylayan, istek süresinden önce isteği onaylamadıysa veya reddetmezse, isteğin süresi dolar (zaman aşımı) ve kullanıcının erişim paketi için başka bir istek göndermesi gerekecektir. 

    İstekler yalnızca, istek süresi yarım hayata geçtikten sonra, ana onaylayanın kararının en az 4 günden sonra zaman aşımına uğrar. İstek zaman aşımı 3 ' ten küçükse ya da daha büyükse, isteği alternatif onaylaya iletmek için yeterli zaman yoktur. Bu örnekte, istek süresi 14 gündür. Bu nedenle, istek süresi 7. günde yarı yaşam süresine ulaşır. Bu nedenle, istek 8. günden daha önce iletilemez. Ayrıca, istekler istek süresinin son gününde iletilemez. Bu nedenle, isteğin iletilebilmesi için en son, gün 13 ' dir.

## <a name="enable-requests"></a>İstekleri etkinleştir

1. Erişim paketinin istek ilkesindeki kullanıcıların istemesi için hemen kullanılabilir olmasını istiyorsanız, etkinleştir seçeneğini **Evet** olarak taşıyın.

    Erişim paketini oluşturma işlemi bittikten sonra bunu gelecekte etkinleştirebilirsiniz.

    Hiçbiri ' ni seçtiyseniz **(yalnızca yönetici doğrudan atamaları)** ve Etkinleştir **' i ayarlarsanız, Yöneticiler** bu erişim paketini doğrudan atayamaz.

    ![Yeni istekleri ve atamaları etkinleştirme seçeneğini gösteren ekran görüntüsü.](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. **İleri**’ye tıklayın.

## <a name="add-requestor-information-preview-to-an-access-package"></a>Bir erişim paketine Istek sahibi bilgilerini (Önizleme) ekleme

1. **Istek sahibi bilgileri** sekmesine gidin ve **sorular** alt sekmesine tıklayın.
 
1. **Soru** kutusundaki soru için, istek sahibine, görüntüleme dizesi olarak da bilinen olarak da bilinen yazın.

    ![Erişim paketi-Ilke-Istek sahibi bilgilerini etkinleştir ayarı](./media/active-directory-entitlement-management-request-policy/add-requestor-info-question.png)

1. Kendi yerelleştirme seçeneklerinizi eklemek isterseniz, **Yerelleştirme Ekle**' ye tıklayın.
    1. **Soru için yerelleştirmeler Ekle** bölmesinde, soruyu yerelleştirçalıştığınız dilin **dil kodunu** seçin.
    1. Yapılandırdığınız dilde soruyu **yerelleştirilmiş metin** kutusuna yazın.
    1. Gerekli tüm yerelleştirmeleri ekledikten sonra **Kaydet**' e tıklayın.

    ![Erişim paketi-Ilke-yerelleştirilmiş metni yapılandırın](./media/active-directory-entitlement-management-request-policy/add-localization-question.png)

1. İstek sahipleri 'ın yanıtlamasını istediğiniz **Yanıt biçimini** seçin. Yanıt biçimleri şunlardır: *kısa metin*, *Çoklu seçim* ve *uzun metin*.
 
    ![Erişim paketi-Ilke-birden çok seçim yanıtı biçimini görüntüle ve Düzenle ' yi seçin](./media/active-directory-entitlement-management-request-policy/answer-format-view-edit.png)
 
1. Birden çok seçim seçilirse, yanıt seçeneklerini yapılandırmak için **görüntüle ve Düzenle** düğmesine tıklayın.
    1. Görüntüleme ve düzenleme sorusunu seçtikten sonra, **görünüm/düzenleme sorusu** bölmesini açar.
    1. **Yanıt değerleri** kutularında soruyu yanıtlarken istek sahibine vermek istediğiniz yanıt seçeneklerini yazın.
    1. İhtiyaç duyduğunuz sayıda yanıtı yazın ve **Kaydet**' e tıklayın.
    
    ![Erişim paketi-Ilke-birden çok seçim seçeneği girin](./media/active-directory-entitlement-management-request-policy/answer-multiple-choice.png)
  
1. Bir erişim paketine erişim isteğinde bulunmalarını talep etmek için **gerekli** onay kutusuna tıklayın.

1. İleri’ye tıklayın