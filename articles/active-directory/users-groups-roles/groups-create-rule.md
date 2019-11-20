---
title: Dinamik grup oluşturma ve durumu denetleme-Azure AD | Microsoft Docs
description: Azure portal bir grup üyeliği kuralı oluşturma, durumu denetleme.
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
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c487ed02abda652a384f2f295c3edac4b56fc654
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027238"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Dinamik grup oluşturma ve durumu denetleme

Azure Active Directory (Azure AD) ' de, Kullanıcı veya cihaz özelliklerine göre grup üyeliğini Belirleme kurallarını kullanabilirsiniz. Bu makalede, Azure portal bir dinamik grup için nasıl bir kural ayarlanacağı açıklanır.
Dinamik üyelik güvenlik grupları veya Office 365 grupları için desteklenir. Bir grup üyeliği kuralı uygulandığında, Kullanıcı ve cihaz öznitelikleri üyelik kuralıyla eşleşmeler için değerlendirilir. Bir kullanıcı veya cihaz için bir öznitelik değiştiğinde, kuruluştaki tüm dinamik grup kuralları üyelik değişiklikleri için işlenir. Kullanıcılar ve cihazlar bir grubun koşullarını karşılıyorsa eklenir veya kaldırılır. Güvenlik grupları, cihazlar veya kullanıcılar için kullanılabilir, ancak Office 365 grupları yalnızca Kullanıcı grupları olabilir.

## <a name="rule-builder-in-the-azure-portal"></a>Azure portal kural Oluşturucu

Azure AD, önemli kurallarınızı daha hızlı bir şekilde oluşturmak ve güncelleştirmek için bir kural Oluşturucusu sağlar. Kural Oluşturucu, en fazla beş ifadeye kadar oluşturmayı destekler. Kural Oluşturucu, birkaç basit ifadeye sahip bir kural oluşturulmasını kolaylaştırır, ancak her kuralı yeniden oluşturmak için kullanılamaz. Kural Oluşturucu oluşturmak istediğiniz kuralı desteklemiyorsa, metin kutusunu kullanabilirsiniz.

Aşağıda, metin kutusunu kullanarak oluşturmanızı önerdiğimiz gelişmiş kuralların veya sözdizimi örnekleri verilmiştir:

- Beşten fazla ifadeye sahip kural
- Doğrudan raporlar kuralı
- [İşleç önceliğini](groups-dynamic-membership.md#operator-precedence) ayarlama
- [Karmaşık Ifadelerle kurallar](groups-dynamic-membership.md#rules-with-complex-expressions); Örneğin `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Kural Oluşturucu, metin kutusunda oluşturulan bazı kuralları görüntüleyemeyebilir. Kural Oluşturucu kuralı görüntüleyebilmediğinden bir ileti görebilirsiniz. Kural Oluşturucu, desteklenen sözdizimini, doğrulamayı veya dinamik grup kurallarının işlenmesini herhangi bir şekilde değiştirmez.

![Dinamik bir grup için üyelik kuralı ekle](./media/groups-update-rule/update-dynamic-group-rule.png)

Bir üyelik kuralı için sözdizimi, desteklenen özellikler, işleçler ve değerler örnekleri için bkz. [Azure Active Directory gruplar Için dinamik üyelik kuralları](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Bir grup üyeliği kuralı oluşturmak için

1. [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) , Kiracıdaki genel yönetici, Intune Yöneticisi veya Kullanıcı Yöneticisi rolündeki bir hesapla oturum açın.
1. **Grupları**seçin.
1. **Tüm gruplar**' ı seçin ve **Yeni Grup**' u seçin.

   ![Yeni grup eklemek için komutu seçin](./media/groups-create-rule/new-group-creation.png)

1. **Grup** sayfasında, yeni grup için bir ad ve açıklama girin. Kullanıcılar veya cihazlar için bir **üyelik türü** seçin ve ardından **Dinamik sorgu Ekle**' yi seçin. Kural Oluşturucusu en fazla beş ifadeyi destekler. Beşten fazla ifade eklemek için metin kutusunu kullanmanız gerekir.

   ![Dinamik bir grup için üyelik kuralı ekle](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Üyelik sorgunuz için kullanılabilen özel uzantı özelliklerini görmek için:
   1. **Özel uzantı özelliklerini al** seçeneğini belirleyin
   1. Uygulama KIMLIĞI ' ni girin ve ardından **özellikleri Yenile**' yi seçin.
1. Kuralı oluşturduktan sonra **Kaydet**' i seçin.
1. Grubu oluşturmak için **Yeni Grup** sayfasında **Oluştur** ' u seçin.

Girdiğiniz kural geçerli değilse, kuralın neden işlenmediği hakkında bir açıklama portalda bir Azure bildiriminde görüntülenir. Kuralı nasıl düzelteceğinizi anlamak için dikkatle okuyun.

## <a name="turn-on-or-off-welcome-email"></a>Hoş geldiniz e-postasını aç veya kapat

Yeni bir Office 365 Grubu oluşturulduğunda, gruba eklenen kullanıcılar bir hoş geldiniz e-posta bildirimi gönderilir. Daha sonra, bir kullanıcının veya cihazın herhangi bir özniteliği değişirse, kuruluştaki tüm dinamik grup kuralları üyelik değişiklikleri için işlenir. Ayrıca eklenen kullanıcılar da hoş geldiniz bildirimini alır. Bu davranışı [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)'de devre dışı bırakabilirsiniz.

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

- &lt;**Tarih ve saat**&gt;: üyeliğin en son güncelleştirildiği zaman.
- **Devam**ediyor: Şu anda güncelleştirmeler devam ediyor.
- **Bilinmiyor**: son güncelleştirme saati alınamıyor. Grup yeni olabilir.

Belirli bir grup için üyelik kuralı işlenirken bir hata oluşursa, grubun **Genel Bakış sayfasının** üst kısmında bir uyarı gösterilir. Kiracıdaki tüm gruplar için bekleyen dinamik üyelik güncelleştirmeleri, daha sonra 24 saat boyunca işlenemezse, **tüm grupların**en üstünde bir uyarı gösterilir.

![işlem hata iletisi uyarıları](./media/groups-create-rule/processing-error.png)

Bu makaleler Azure Active Directory gruplar hakkında ek bilgiler sağlar.

- [Var olan grupları görme](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Yeni grup oluşturma ve üye ekleme](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Bir grubun ayarlarını yönetme](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Bir grubun üyeliklerini yönetme](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Bir gruptaki kullanıcılar için dinamik kuralları yönetme](groups-dynamic-membership.md)
