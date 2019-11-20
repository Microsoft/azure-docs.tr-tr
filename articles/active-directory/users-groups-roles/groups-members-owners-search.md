---
title: Grup üyelerini ve sahiplerini arama ve filtreleme (Önizleme)-Azure Active Directory | Microsoft Docs
description: Grupların üyelerini ve sahiplerini aramak ve filtrelemek Azure portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 09/10/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d11f5a9f2de8683365d663e85c275c3729ebf8a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901509"
---
# <a name="search-groups--and-members-preview-in-azure-active-directory"></a>Azure Active Directory 'de grupları ve üyeleri (Önizleme) arayın

Bu makalede, bir grubun üyelerini ve sahibini aramanın yanı sıra arama filtrelerini de Azure Active Directory (Azure AD) portalındaki gruplar geliştirme önizlemesi 'nin bir parçası olarak nasıl kullanacağınız açıklanır. Gruplar, Üyeler ve sahipler dahil, hızlı ve kolay bir şekilde yönetmenize yardımcı olmak için, gruplar deneyimlerinde çok sayıda geliştirmeler vardır. Önizlemeler hakkında daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bu Önizlemedeki değişiklikler şunları içerir:

- Grup adlarında alt dize arama gibi yeni gruplar arama özellikleri
- Üye ve sahip listelerindeki yeni filtreleme ve sıralama seçenekleri
- Üye ve sahip listeleri için yeni arama özellikleri
- Büyük gruplar için daha doğru grup sayısı

## <a name="enabling-and-managing-the-preview"></a>Önizlemeyi etkinleştirme ve yönetme

Önizlemeye katılmayı kolay hale aldık:

  1. [Azure AD portalında](https://portal.azure.com)oturum açın ve **gruplar**' ı seçin.
  2. Gruplar – tüm gruplar sayfasında, önizlemeye katmak için sayfanın üst kısmındaki başlığı seçin.

**Tüm gruplar** sayfasında **Önizleme bilgileri** ' ni seçerek en son özellikleri ve geliştirmeleri de denetleyebilirsiniz. Önizlemeye katılırsanız, iyileştirmeler içeren ve önizlemenin bir parçası olan tüm grup sayfalarında önizleme etiketini görebilirsiniz. Her grup sayfası bu önizlemenin bir parçası olarak güncelleştirilmedi.

Herhangi bir sorun yaşıyorsanız, **tüm gruplar** sayfasının en üstündeki başlığı seçerek eski deneyimi geri geçirebilirsiniz. Deneyimimizi iyileştirebilmemiz için geri bildiriminiz bizim için teşekkür ederiz.

## <a name="group-search-and-sorting"></a>Grup Arama ve sıralama

Gruplar listesi araması, bir arama dizesi girebileceğiniz şekilde geliştirilmiştir. arama, grup adları listesinde otomatik olarak bir `startswith` ve alt dize araması gerçekleştirir. Alt dize araması yalnızca tüm sözcüklere yapılır ve özel karakterler içermez.

![Tüm Gruplar sayfasında yeni alt dize aramaları](./media/groups-members-owners-search/groups-search-preview.png)

Örneğin, "ilke" araması şimdi "MDM ilkesi – Batı" ve "Ilke grubu" olarak döndürülür. "New_policy" adlı bir grup döndürülemez.

- Aynı aramayı grup üyeliği listelerinde da gerçekleştirebilirsiniz.
- Artık listeyi artan veya azalan düzende sıralamak için ad sütun başlığının sağındaki okları kullanarak gruplar listesini ada göre sıralayabilirsiniz.

## <a name="group-member-search-and-filtering"></a>Grup üyesi arama ve filtreleme

### <a name="search-group-member-and-owner-lists"></a>Grup üyesi ve sahip listelerini ara

Artık belirli bir grubun üyelerini ada göre arayabilir ve aynı aramayı grubun sahipleri listesinde de gerçekleştirebilirsiniz. Yeni deneyimde, arama kutusuna bir dize girerseniz, bir StartsWith araması otomatik olarak gerçekleştirilir. Örneğin, "Scott" araması Scott Wilkinson döndürür.

![Yeni alt dize Grup üyeleri ve sahipler listelerinde arar](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Üye ve sahipler listesini filtrele

Aramaya ek olarak, artık üye ve sahip listelerini Kullanıcı türüne göre filtreleyebilirsiniz. Bu, listenin Kullanıcı türü sütununda bulunan bilgiler. Bu nedenle, grupta herhangi bir konuk olup olmadığını öğrenmek için listeyi üyelere ve konuklara göre filtreleyebilirsiniz.

### <a name="view-and-manage-membership"></a>Üyeliği görüntüleme ve yönetme

Belirli bir grubun doğrudan üyelerini görüntülemenin yanı sıra, artık Üyeler sayfasında grubun tüm üyelerinin listesini görüntüleyebilirsiniz. Üyeler listesi, tüm geçişli Üyeler dahil olmak üzere grubun tüm benzersiz üyelerini içerir.

Ayrıca, doğrudan Üyeler listesini ve tüm Üyeler listesini tek tek arayabilir ve filtreleyebilirsiniz. Tüm üyeler listesinin filtrelenmesi, doğrudan Üyeler listesinde uygulanan filtreleri etkilemez.

## <a name="improved-group-member-counts"></a>Geliştirilmiş grup üyesi sayıları

Gruba **genel bakış** sayfasını, her boyuttaki gruplar için grup üyesi sayısı sağlayacak şekilde geliştirdik. Üye sayılarını 1.000 taneden fazla üyesi olan gruplar için de görebilirsiniz. Artık bir grup için doğrudan üyelerin toplam sayısını ve toplam üyelik sayısını (geçişli Üyeler dahil olmak üzere grubun tüm benzersiz üyeleri) **genel bakış** sayfasında görebilirsiniz.

![Grup üyeliği sayılarında daha yüksek doğruluk](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu makaleler, Azure AD 'de gruplarla çalışma hakkında ek bilgiler sağlar.

- [Grupları ve üyeleri görüntüleme](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Grup üyeliğini yönetme](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Bir gruptaki kullanıcılar için dinamik kuralları yönetme](groups-create-rule.md)
- [Grup ayarlarınızı düzenleme](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Grupları kullanarak kaynaklara erişimi yönetme](../fundamentals/active-directory-manage-groups.md)
- [Grupları kullanarak SaaS uygulamalarına erişimi yönetme](groups-saasapps.md)
- [PowerShell komutlarını kullanarak grupları yönetme](groups-settings-v2-cmdlets.md)
- [Azure Active Directory bir Azure aboneliği ekleyin](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
