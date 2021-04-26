---
title: Soru-Cevap Oluşturma uygulamayı yönetme-Soru-Cevap Oluşturma
description: Soru-Cevap Oluşturma, bir Bilgi Bankası 'nda birden çok kişinin işbirliği yapmasına izin verir. Soru-Cevap Oluşturma, etkin öğrenme ile bilgi Bankalarınızın kalitesini artırmak için bir özellik sunar. Bunlardan biri, mevcut soruları kaldırmadan veya değiştirmeden gözden geçirebilir, kabul edebilir veya reddedebilir ve ekleyebilir.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 49bfca118e53bbe3e4287b2ce25e5baffa717175
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102217338"
---
# <a name="manage-qna-maker-app"></a>Soru-Cevap Oluşturma uygulamayı yönetme

Soru-Cevap Oluşturma, işbirmci rolüne bağlı olarak ortak çalışan erişimini kısıtlamak için bir yetenek sunarak farklı yazarlar ve içerik düzenleyicilerle işbirliği yapmanızı sağlar.
[Soru-cevap oluşturma ortak çalışan kimlik doğrulama kavramları](../Concepts/role-based-access-control.md)hakkında daha fazla bilgi edinin.

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Azure rol tabanlı erişim denetimi (Azure RBAC) ekleme

Soru-Cevap Oluşturma, birden fazla kişinin aynı Soru-Cevap Oluşturma kaynaktaki tüm bilgi tabanlarında işbirliği yapmasına izin verir. Bu özellik [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../../role-based-access-control/role-assignments-portal.md)ile birlikte sağlanır.

## <a name="access-at-the-qna-maker-resource-level"></a>Soru-Cevap Oluşturma kaynak düzeyinde erişim

Soru-Cevap Oluşturma hizmetinde belirli bir Bilgi Bankası 'nı paylaşamazsınız. Daha ayrıntılı erişim denetimi istiyorsanız bilgi tabanlarınızı farklı Soru-Cevap Oluşturma kaynaklarına dağıtmayı ve ardından her kaynağa roller eklemeyi düşünün.

## <a name="add-a-role-to-a-resource"></a>Kaynağa rol ekleme

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Soru-Cevap Oluşturma kaynağına bir kullanıcı hesabı ekleme

Aşağıdaki adımlarda ortak çalışan rolü kullanılır, ancak bu adımlar kullanılarak roller eklenebilir

1. [Azure](https://portal.azure.com/) portalında oturum açın ve soru-cevap oluşturma kaynağına gidin.

    ![Soru-Cevap Oluşturma kaynak listesi](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. **Access Control (IAM)** sekmesine gidin.

    ![Soru-Cevap Oluşturma ıAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. **Add (Ekle)** seçeneğini belirleyin.

    ![Soru-Cevap Oluşturma ıAM ekleme](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Aşağıdaki listeden bir rol seçin:

    |Rol|
    |--|
    |Sahip|
    |Katılımcı|
    |Bilişsel hizmetler Soru-Cevap Oluşturma okuyucu|
    |Bilişsel hizmetler Soru-Cevap Oluşturma Düzenleyicisi|
    |Bilişsel hizmetler kullanıcısı|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="Soru-Cevap Oluşturma ıAM rol Ekle.":::

1. Kullanıcının e-posta adresini girin ve **Kaydet**' e basın.

    ![Soru-Cevap Oluşturma ıAM e-posta Ekle](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Bilgi temellerini görüntüleyin Soru-Cevap Oluşturma

Soru-Cevap Oluşturma hizmetinizi paylaştığınız kişi [soru-cevap oluşturma portalında](https://qnamaker.ai)oturum açtıklarında, bu hizmet içindeki tüm bilgi temellerini kendi rollerine göre görebilirler.

Bir Bilgi Bankası seçerken, bu Soru-Cevap Oluşturma kaynaktaki geçerli rolleri Bilgi Bankası adının yanında görünür.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Web sayfasının sol üst köşesinde bulunan Bilgi Bankası adı ' nın yanında, parantez içinde rol adı olan düzenleme modunda Bilgi Bankası 'nın ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bilgi Bankası oluşturma](./manage-knowledge-bases.md)
