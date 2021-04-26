---
title: Azure AD Yetkilendirme Yönetimi 'nde bir erişim paketinin onay ayarlarını değiştirme-Azure Active Directory
description: Azure Active Directory yetkilendirme yönetimi 'nde erişim paketi için onay ve istek sahibi bilgi ayarlarını değiştirmeyi öğrenin.
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
ms.openlocfilehash: 48b5260e883d85899953240f6ee4f83127681c9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97591315"
---
# <a name="change-approval-and-requestor-information-preview-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD yetkilendirme yönetiminde bir erişim paketinin onay ve istek sahibi bilgileri (Önizleme) ayarlarını değiştirme

Bir erişim paketi Yöneticisi olarak, mevcut bir ilkeyi düzenleyerek veya yeni bir ilke ekleyerek bir erişim paketinin onay ve istek sahibi bilgileri ayarlarını dilediğiniz zaman değiştirebilirsiniz.

Bu makalede, var olan bir erişim paketinin onay ve istek sahibi bilgileri ayarlarının nasıl değiştirileceği açıklanır.

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


## <a name="change-approval-settings-of-an-existing-access-package"></a>Var olan bir erişim paketinin onay ayarlarını değiştir

Erişim paketine yönelik isteklerin onay ayarlarını belirtmek için şu adımları izleyin:

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik** Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. Düzenlenecek ilkeyi seçin ya da erişim paketine yeni bir ilke ekleyin
    1. **İlkeler** ' e tıklayın ve ardından yeni bir ilke oluşturmak Istiyorsanız **ilke ekleyin** .
    1. Düzenlemek istediğiniz ilkeye tıklayın ve ardından **Düzenle**' ye tıklayın.

1. **İstek** sekmesine gidin.

1. Seçili kullanıcılardan gelen isteklere onay gerektirmek için **onay gerektir** seçeneğini **Evet** olarak ayarlayın. Ya da isteklerin otomatik olarak onaylanmasını sağlamak için, geçiş seçeneğini **Hayır** olarak ayarlayın.

1. Kullanıcıların erişim paketi istemek için bir gerekçe sağlamasını gerektirmek için, **istek sahibine iste** modunu **Evet** olarak ayarlayın.
    
1. Şimdi isteklerin tek veya 2 aşamalı onay gerektirmesine göre belirlenir. Tek aşamalı onay için **1** ' e geçiş yapma veya 2 aşamalı onay için **2** ' **ye geçişi ayarlama** .

    ![Erişim paketi Istekleri-onay ayarları](./media/entitlement-management-access-package-approval-policy/approval.png)


Kaç aşamanın gerekli olduğunu seçtikten sonra onaylayanları eklemek için aşağıdaki adımları kullanın: 

### <a name="single-stage-approval"></a>Tek aşamalı onay

1. **Ilk onaylayanı** ekleyin:
    
    İlke, dizininizdeki kullanıcılar için erişimi yönetecek şekilde ayarlandıysa, yönetici ' yi **onaylayan olarak** seçebilirsiniz. Ya da, belirli bir **kullanıcıyı ekleme ' yi seçtikten sonra,** açılan menüden belirli onaylayanları Seç ' i tıklatarak ekleyebilirsiniz.
    
    ![Erişim paketi-Istekler-Dizin-Ilk onaylayanın kullanıcıları Için](./media/entitlement-management-access-package-approval-policy/approval-single-stage-first-approver-manager.png)

    Bu ilke, dizininizde olmayan kullanıcılar için erişimi yönetecek şekilde ayarlandıysa, **Harici sponsor** veya **dahili sponsor** seçeneğini belirleyebilirsiniz. Ya da belirli onaylayanlara seç altında **Onaylayanlar Ekle** veya gruplar ' a tıklayarak belirli bir kullanıcıyı ekleyin.
    
    ![Erişim paketi-Istekler-Dizin Ilk onaylayan kullanıcılar Için](./media/entitlement-management-access-package-approval-policy/out-directory-first-approver.png)
    
1. **Yöneticiyi** ilk onaylayan olarak seçtiyseniz, geri dönüş **Ekle** ' ye tıklayarak dizininizdeki bir veya daha fazla kullanıcıyı veya grubu geri dönüş onaylayıcısı olacak şekilde seçin. Yetkilendirme Yönetimi, erişim isteyen kullanıcı için yönetici bulamazsa, geri dönüş onaylayanları isteği alır.

    Yönetici, **yönetici** özniteliği kullanılarak Yetkilendirme Yönetimi tarafından bulunur. Öznitelik, kullanıcının Azure AD 'deki profilidir. Daha fazla bilgi için, bkz. [Azure Active Directory kullanarak kullanıcının profil bilgilerini ekleme veya güncelleştirme](../fundamentals/active-directory-users-profile-azure-portal.md).

1. **Belirli onaylayanları Seç**' i seçtiyseniz, dizininizdeki bir veya daha fazla Kullanıcı ya da grubun onaylayanlarını seçmek Için **onaylayan Ekle** ' ye tıklayın.

1. Kararın altındaki kutuda **kaç gün içinde yapılmalıdır?**, bir onaylayanın bu erişim paketi için bir isteği gözden geçirmesi gereken gün sayısını belirtin.

    Bu süre içinde bir istek onaylanmamışsa, otomatik olarak reddedilir. Kullanıcının erişim paketi için başka bir istek göndermesi gerekir.

1. Onaylayanlara kararların bir gerekçe vermesini gerektirmek için, onaylayan gerekçe ıste seçeneğini **Evet** olarak ayarlayın.

    Gerekçe diğer onaylayanlara ve istek sahibine görünür.

### <a name="2-stage-approval"></a>2 aşamalı onay

2 aşamalı onay seçtiyseniz ikinci bir onaylayan eklemeniz gerekir.

1. **Ikinci onaylayanı** ekleyin: 
    
    Kullanıcılar dizininizdeki ise, belirli onaylayanları seçin altında **onaylayan Ekle** ' ye tıklayarak ikinci onaylayan olarak belirli bir kullanıcıyı ekleyin.

    ![Erişim paketi Istekleri-Dizin-Ikinci onaylayan kullanıcılar Için](./media/entitlement-management-access-package-approval-policy/in-directory-second-approver.png)

    Kullanıcılar dizininizden değilse, ikinci onaylayan olarak **dahili sponsor** veya **External sponsoru** seçin. Onaylayanı seçtikten sonra geri dönüş onaylayanları ekleyin.

    ![Erişim paketi Istekleri-Ikinci Dizin Için olan kullanıcılar Için-Ikinci onaylayan](./media/entitlement-management-access-package-approval-policy/out-directory-second-approver.png) 

1. İkinci onaylayanın onay kutusundaki isteği onaylaması gereken gün sayısını **kaç gün içinde yapmanız gerektiğini** belirtin. 

1. Onaylayan ıste gerekçe **Evet** veya **Hayır** olarak ayarlanmalıdır.

### <a name="alternate-approvers"></a>Alternatif onaylayanlar

İstekleri onaylayabilecek birinci ve ikinci onaylayanları belirtmeye benzer şekilde alternatif onaylayanlar belirtebilirsiniz. Alternatif onaylayanlar olması, isteklerin süresi dolmadan önce onaylanmış veya reddedildiğinden emin olmaya yardımcı olur (zaman aşımı). İlk onaylayanın ve ikinci onaylayanın 2 aşamalı onay için alternatif onaylayanlar listeleyebilirsiniz. 

Alternatif onaylayanlar belirterek, ilk veya ikinci onaylayanlardan isteği onaylayamayan veya reddetmeyen olayda, ilke kurulumu sırasında belirttiğiniz iletme zamanlaması başına bekleyen istek alternatif onaylayanlara iletilir. Bekleyen isteği onaylamak veya reddetmek için bir e-posta alırlar.

İstek alternatif onaylayanlara iletildikten sonra, ilk veya ikinci onaylayanlar isteği yine de onaylayabilir veya reddedebilir. Alternatif onaylayanlar, bekleyen isteği onaylamak veya reddetmek için aynı erişim sitemi kullanır.

Kişiler veya kişi gruplarını onaylayan ve alternatif onaylayanlar olacak şekilde listeliyoruz. Lütfen birinci, ikinci ve alternatif onaylayanlar olacak farklı kişi kümelerini listediğinizden emin olun.
Örneğin, Gamze ve emre 'yi Ilk onaylayan olarak listeleniyorsa, alternatif onaylayanlar olarak Carol ve Davde listesini listeleyin. Bir erişim paketine alternatif onaylayanlar eklemek için aşağıdaki adımları kullanın:

1. Ilk onaylayanın, Ikinci onaylayan veya her ikisinde de **Gelişmiş istek ayarlarını göster**' e tıklayın.

    :::image type="content" source="media/entitlement-management-access-package-approval-policy/alternate-approvers-click-advanced-request.png" alt-text="Erişim paketi-Ilke-Gelişmiş istek ayarlarını göster":::

1. **Herhangi bir eylem yapılmadı, alternatif onaylayanlara ilet mi?** **Evet** olarak değiştirin.

1. **Alternatif onaylayanlar Ekle** ' ye tıklayın ve listeden alternatif onaylayanı seçin.

    ![Erişim paketi-Ilke-alternatif onaylayanlar ekleme](./media/entitlement-management-access-package-approval-policy/alternate-approvers-add.png)

    Ilk onaylayan için yöneticiyi onaylayan olarak seçerseniz, alternatif onaylayan alanındaki **ikinci düzey yönetici alternatif onaylayan olarak** ek bir seçeneğe sahip olursunuz. Bu seçeneği belirlerseniz, sistemin ikinci düzey yöneticiyi bulamadığı durumlarda isteği iletmek için bir geri dönüş onaylayan eklemeniz gerekir.

1. **Kaç gün sonra diğer onaylayanlara ilet** kutusunda, onaylayanın bir isteği onaylaması veya reddetmesi için sahip olduğu gün sayısına koyun. Onaylayan, istek süresinden önce isteği onaylamadıysa veya reddetmezse, isteğin süresi dolar (zaman aşımı) ve kullanıcının erişim paketi için başka bir istek göndermesi gerekecektir. 

    İstekler yalnızca, istek süresi yarım hayata geçtikten sonra, ana onaylayanın kararının en az 4 günden sonra zaman aşımına uğrar. İstek zaman aşımı 3 ' ten küçükse ya da daha büyükse, isteği alternatif onaylaya iletmek için yeterli zaman yoktur. Bu örnekte, istek süresi 14 gündür. Bu nedenle, istek süresi 7. günde yarı yaşam süresine ulaşır. Bu nedenle, istek 8. günden daha önce iletilemez. Ayrıca, istekler istek süresinin son gününde iletilemez. Bu nedenle, isteğin iletilebilmesi için en son, gün 13 ' dir.

## <a name="enable-requests"></a>İstekleri etkinleştir

1. Erişim paketinin istek ilkesindeki kullanıcıların istemesi için hemen kullanılabilir olmasını istiyorsanız, etkinleştir seçeneğini **Evet** olarak taşıyın.

    Erişim paketini oluşturma işlemi bittikten sonra bunu gelecekte etkinleştirebilirsiniz.

    Hiçbiri ' ni seçtiyseniz **(yalnızca yönetici doğrudan atamaları)** ve Etkinleştir **' i ayarlarsanız, Yöneticiler** bu erişim paketini doğrudan atayamaz.

    ![Erişim paketi-Ilke-ilke ayarını etkinleştir](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. **İleri**’ye tıklayın.

## <a name="collect-additional-requestor-information-for-approval-preview"></a>Onay için ek istek sahibi bilgileri toplama (Önizleme)

Kullanıcıların doğru erişim paketlerine erişim elde etmeleri için, istek sırasında özel metin alanı veya birden çok seçim sorusunun yanıt vermesi gerekir. İlke başına 20 sorudan ve çoklu seçim sorularına yönelik 25 yanıt sınırına sahip bir sınır vardır. Daha sonra bu sorular bir karar vermek için onaylayanlara gösterilir.

1. **Istek sahibi bilgileri** sekmesine gidin ve **sorular** alt sekmesine tıklayın.
 
1. **Soru** kutusundaki soru için, istek sahibine, görüntüleme dizesi olarak da bilinen olarak da bilinen yazın.

    ![Erişim paketi-Ilke-Istek sahibi bilgilerini etkinleştir ayarı](./media/entitlement-management-access-package-approval-policy/add-requestor-info-question.png)

1. Erişim paketine erişmesi gereken kullanıcıların topluluğunun hiçbir ortak tercih edilen dili yoksa, myaccess.microsoft.com üzerinde erişim isteyen kullanıcılar için deneyimi geliştirebilirsiniz. Deneyimi geliştirmek için, farklı diller için alternatif görüntüleme dizeleri sağlayabilirsiniz. Örneğin, bir kullanıcının Web tarayıcısı Ispanyolca olarak ayarlandıysa ve Ispanyolca görüntüleme dizeleri yapılandırılmışsa, bu dizeler istekte bulunan kullanıcıya görüntülenecektir. İstekleri için yerelleştirmeyi yapılandırmak için **Yerelleştirme Ekle**' ye tıklayın.
    1. **Soru için yerelleştirmeler Ekle** bölmesinde, soruyu yerelleştirçalıştığınız dilin **dil kodunu** seçin.
    1. Yapılandırdığınız dilde soruyu **yerelleştirilmiş metin** kutusuna yazın.
    1. Gerekli tüm yerelleştirmeleri ekledikten sonra **Kaydet**' e tıklayın.

    ![Erişim paketi-Ilke-yerelleştirilmiş metni yapılandırın](./media/entitlement-management-access-package-approval-policy/add-localization-question.png)

1. İstek sahipleri 'ın yanıtlamasını istediğiniz **Yanıt biçimini** seçin. Yanıt biçimleri şunlardır: *kısa metin*, *Çoklu seçim* ve *uzun metin*.
 
    ![Erişim paketi-Ilke-birden çok seçim yanıtı biçimini görüntüle ve Düzenle ' yi seçin](./media/entitlement-management-access-package-approval-policy/answer-format-view-edit.png)
 
1. Birden çok seçim seçilirse, yanıt seçeneklerini yapılandırmak için **görüntüle ve Düzenle** düğmesine tıklayın.
    1. Görüntüleme ve düzenleme sorusunu seçtikten sonra, **görünüm/düzenleme sorusu** bölmesini açar.
    1. **Yanıt değerleri** kutularında soruyu yanıtlarken istek sahibine vermek istediğiniz yanıt seçeneklerini yazın.
    1. İhtiyaç duyduğunuz sayıda yanıtı yazın ve **Kaydet**' e tıklayın.
    
    ![Erişim paketi-Ilke-birden çok seçim seçeneği girin](./media/entitlement-management-access-package-approval-policy/answer-multiple-choice.png)
  
1. Bir erişim paketine erişim isteğinde bulunmalarını talep etmek için **gerekli** onay kutusuna tıklayın.

1. Gereksinimlerinize göre kalan sekmeleri (örn. yaşam döngüsü) doldurun.

Erişim paketi ilkenizde istek sahibi bilgilerini yapılandırdıktan sonra, istek sahibinin sorulara verdiği yanıtları görüntüleyebilir. İstek sahibi bilgilerini görme hakkında rehberlik için bkz. [istek sahibinin soruların yanıtlarını görüntüleme (Önizleme)](entitlement-management-request-approve.md#view-requestors-answers-to-questions-preview).

## <a name="next-steps"></a>Sonraki adımlar
- [Erişim paketi için yaşam döngüsü ayarlarını değiştirme](entitlement-management-access-package-lifecycle-policy.md)
- [Erişim paketi isteklerini görüntüleme](entitlement-management-access-package-requests.md)
