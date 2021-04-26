---
title: Dinamik grup oluşturma veya düzenleme ve durum edinme-Azure AD | Microsoft Docs
description: Azure portal bir grup üyeliği kuralı oluşturma veya güncelleştirme ve işlem durumunu denetleme.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8525e9a76fa2439692cdb26b36c0bb761d63177c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550238"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Azure Active Directory içinde dinamik grup oluşturma veya güncelleştirme

Azure Active Directory (Azure AD) ' de, Kullanıcı veya cihaz özelliklerine göre grup üyeliğini Belirleme kurallarını kullanabilirsiniz. Bu makalede, Azure portal bir dinamik grup için nasıl bir kural ayarlanacağı açıklanır.
Dinamik üyelik güvenlik grupları veya Microsoft 365 grupları için desteklenir. Bir grup üyeliği kuralı uygulandığında, Kullanıcı ve cihaz öznitelikleri üyelik kuralıyla eşleşmeler için değerlendirilir. Bir kullanıcı veya cihaz için bir öznitelik değiştiğinde, kuruluştaki tüm dinamik grup kuralları üyelik değişiklikleri için işlenir. Kullanıcılar ve cihazlar bir grubun koşullarını karşılıyorsa eklenir veya kaldırılır. Güvenlik grupları, cihazlar veya kullanıcılar için kullanılabilir, ancak Microsoft 365 Gruplar yalnızca Kullanıcı grupları olabilir. Dinamik grupları kullanmak için Azure AD Premium P1 lisansı gerekir. Daha fazla ayrıntı için bkz. [gruplar Için dinamik üyelik kuralları](./groups-dynamic-membership.md) . 

## <a name="rule-builder-in-the-azure-portal"></a>Azure portal kural Oluşturucu

Azure AD, önemli kurallarınızı daha hızlı bir şekilde oluşturmak ve güncelleştirmek için bir kural Oluşturucusu sağlar. Kural Oluşturucu, en fazla beş ifadeye kadar oluşturmayı destekler. Kural Oluşturucu, birkaç basit ifadeye sahip bir kural oluşturulmasını kolaylaştırır, ancak her kuralı yeniden oluşturmak için kullanılamaz. Kural Oluşturucu oluşturmak istediğiniz kuralı desteklemiyorsa, metin kutusunu kullanabilirsiniz.

Aşağıda, metin kutusunu kullanarak oluşturmanızı önerdiğimiz gelişmiş kuralların veya sözdizimi örnekleri verilmiştir:

- Beşten fazla ifadeye sahip kural
- Doğrudan raporlar kuralı
- [İşleç önceliğini](groups-dynamic-membership.md#operator-precedence) ayarlama
- [Karmaşık Ifadelerle kurallar](groups-dynamic-membership.md#rules-with-complex-expressions); Örneğin `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Kural Oluşturucu, metin kutusunda oluşturulan bazı kuralları görüntüleyemeyebilir. Kural Oluşturucu kuralı görüntüleyebilmediğinden bir ileti görebilirsiniz. Kural Oluşturucu, desteklenen sözdizimini, doğrulamayı veya dinamik grup kurallarının işlenmesini herhangi bir şekilde değiştirmez.

!["Kuralları Yapılandır" sekmesindeki "ifade Ekle" eylemi içeren "dinamik üyelik kuralları" sayfasını gösteren ekran görüntüsü.](./media/groups-create-rule/update-dynamic-group-rule.png)

Bir üyelik kuralı için sözdizimi, desteklenen özellikler, işleçler ve değerler örnekleri için bkz. [Azure Active Directory gruplar Için dinamik üyelik kuralları](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Bir grup üyeliği kuralı oluşturmak için

1. Azure AD kuruluşunda genel yönetici, Intune Yöneticisi veya Kullanıcı Yöneticisi rolündeki bir hesapla [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) oturum açın.
1. **Grupları** arayın ve seçin.
1. **Tüm gruplar**' ı seçin ve **Yeni Grup**' u seçin.

   ![Yeni grup eklemek için komutu seçin](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. **Grup** sayfasında, yeni grup için bir ad ve açıklama girin. Kullanıcılar veya cihazlar için bir **üyelik türü** seçin ve ardından **Dinamik sorgu Ekle**' yi seçin. Kural Oluşturucusu en fazla beş ifadeyi destekler. Beşten fazla ifade eklemek için metin kutusunu kullanmanız gerekir.

   !["Yeni grup" eyleminin seçildiği "tüm gruplar" sayfasını gösteren ekran görüntüsü.](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Üyelik sorgunuz için kullanılabilen özel uzantı özelliklerini görmek için:
   1. **Özel uzantı özelliklerini al** seçeneğini belirleyin
   1. Uygulama KIMLIĞI ' ni girin ve ardından **özellikleri Yenile**' yi seçin.
1. Kuralı oluşturduktan sonra **Kaydet**' i seçin.
1. Grubu oluşturmak için **Yeni Grup** sayfasında **Oluştur** ' u seçin.

Girdiğiniz kural geçerli değilse, kuralın neden işlenmediği hakkında bir açıklama portalda bir Azure bildiriminde görüntülenir. Kuralı nasıl düzelteceğinizi anlamak için dikkatle okuyun.

## <a name="to-update-an-existing-rule"></a>Mevcut bir kuralı güncelleştirmek için

1. Azure AD kuruluşunda genel yönetici, Grup Yöneticisi, Intune Yöneticisi veya Kullanıcı Yöneticisi rolündeki bir hesapla [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) oturum açın.
1. **Gruplar**  >  **tüm gruplar**' ı seçin.
1. Profilini açmak için bir grup seçin.
1. Grubun profil sayfasında **dinamik üyelik kuralları**' nı seçin. Kural Oluşturucusu en fazla beş ifadeyi destekler. Beşten fazla ifade eklemek için metin kutusunu kullanmanız gerekir.

   ![Dinamik bir grup için üyelik kuralı ekle](./media/groups-create-rule/update-dynamic-group-rule.png)

1. Üyelik kuralınız için kullanılabilen özel uzantı özelliklerini görmek için:
   1. **Özel uzantı özelliklerini al** seçeneğini belirleyin
   1. Uygulama KIMLIĞI ' ni girin ve ardından **özellikleri Yenile**' yi seçin.
1. Kuralı güncelleştirdikten sonra **Kaydet**' i seçin.

## <a name="turn-on-or-off-welcome-email"></a>Hoş geldiniz e-postasını aç veya kapat

Yeni bir Microsoft 365 Grubu oluşturulduğunda, gruba eklenen kullanıcılar bir hoş geldiniz e-posta bildirimi gönderilir. Daha sonra, bir kullanıcının veya cihazın herhangi bir özniteliği değişirse, kuruluştaki tüm dinamik grup kuralları üyelik değişiklikleri için işlenir. Ayrıca eklenen kullanıcılar da hoş geldiniz bildirimini alır. Bu davranışı [Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup)'de devre dışı bırakabilirsiniz.

## <a name="check-processing-status-for-a-rule"></a>Bir kural için işleme durumunu denetleme

Grubun **genel bakış** sayfasında Üyelik işleme durumu ve son güncelleştirilme tarihi ' ni görebilirsiniz.
  
  ![dinamik grup durumunu görüntüleme](./media/groups-create-rule/group-status.png)

**Üyelik işleme** durumu için aşağıdaki durum iletileri görüntülenebilir:

- **Değerlendiriliyor**: Grup değişikliği alındı ve güncelleştirmeler değerlendiriliyor.
- **İşleniyor**: güncelleştirmeler işleniyor.
- **Güncelleştirme tamamlandı**: işleme tamamlandı ve geçerli tüm güncelleştirmeler yapıldı.
- **İşlem hatası**: üyelik kuralını değerlendirirken bir hata nedeniyle işlem tamamlanamadı.
- **Güncelleştirme duraklatıldı**: dinamik üyelik kuralı güncelleştirmeleri yönetici tarafından duraklatıldı. MembershipRuleProcessingState, "duraklatıldı" olarak ayarlandı.

**Üyelik son güncelleştirilme** durumu için aşağıdaki durum iletileri görüntülenebilir:

- &lt;**Tarih ve saat** &gt; : üyeliğin güncelleştirildiği son zaman.
- **Devam** ediyor: Şu anda güncelleştirmeler devam ediyor.
- **Bilinmiyor**: son güncelleştirme saati alınamıyor. Grup yeni olabilir.

Belirli bir grup için üyelik kuralı işlenirken bir hata oluşursa, grubun **Genel Bakış sayfasının** üst kısmında bir uyarı gösterilir. 24 saatten uzun bir sürede kuruluştaki tüm gruplar için bekleyen dinamik üyelik güncelleştirmeleri işlenemezse, **tüm grupların** en üstünde bir uyarı gösterilir.

![işlem hata iletisi uyarıları](./media/groups-create-rule/processing-error.png)

Bu makaleler Azure Active Directory gruplar hakkında ek bilgiler sağlar.

- [Var olan grupları görme](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Yeni grup oluşturma ve üye ekleme](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Bir grubun ayarlarını yönetme](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Bir grubun üyeliklerini yönetme](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Bir gruptaki kullanıcılar için dinamik kuralları yönetme](groups-dynamic-membership.md)