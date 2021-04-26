---
title: Azure AD Domain Services için SKU 'YU değiştirme | Microsoft Docs
description: İş gereksinimleriniz değiştiğinde Azure AD Domain Services yönetilen bir etki alanı için SKU katmanını öğrenin
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 320bd87aa78d26cee44c48f27365febd1dd426ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620299"
---
# <a name="change-the-sku-for-an-existing-azure-active-directory-domain-services-managed-domain"></a>Mevcut Azure Active Directory Domain Services yönetilen bir etki alanı için SKU 'YU değiştirme

Azure Active Directory Domain Services (Azure AD DS) ' de, kullanılabilir performans ve Özellikler SKU türünü temel alır. Bu özellik farkları, tek yönlü giden orman güvenlerini yedekleme sıklığını veya en yüksek sayıda içerir.

Yönetilen etki alanını oluştururken bir SKU seçersiniz ve yönetilen etki alanı dağıtıldıktan sonra iş gereksinimleriniz değiştikçe STB 'leri yukarı veya aşağı doğru değiştirebilirsiniz. İş Gereksinimlerdeki değişiklikler, daha sık yedekleme ihtiyacını veya ek orman güvenleri oluşturmayı içerebilir. Farklı SKU 'ların sınırları ve fiyatları hakkında daha fazla bilgi için bkz. [azure AD DS SKU kavramlarına][concepts-sku] ve [Azure AD DS fiyatlandırma][pricing] sayfalarına bakın.

Bu makalede, Azure portal kullanarak mevcut Azure AD DS yönetilen etki alanı için SKU 'nun nasıl değiştirileceği gösterilmektedir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [yönetilen bir etki alanı oluşturmak ve yapılandırmak][create-azure-ad-ds-instance]için öğreticiyi doldurun.

## <a name="sku-change-limitations"></a>SKU değişiklik sınırlamaları

Yönetilen etki alanı dağıtıldıktan sonra SKU 'Ları yukarı veya aşağı doğru değiştirebilirsiniz. Ancak, bir kaynak ormanı kullanıyorsanız ve Azure AD DS şirket içi AD DS ortamına tek yönlü bir giden orman güveni oluşturduysanız, SKU değişiklik işlemi için bazı sınırlamalar vardır. *Premium* ve *Kurumsal* SKU 'lar, oluşturabileceğiniz güven sayısı için bir sınır tanımlar. Yapılandırılmış olan en düşük sınıra sahip bir SKU 'ya geçiş yapamazsınız.

Örnek:

* *Premium* SKU 'sunda iki orman güveni oluşturduysanız, *Standart* SKU 'ya geçiş yapamazsınız. *Standart* SKU, orman güvenlerini desteklemez.
* Veya *Premium* SKU 'sunda yedi güven oluşturduysanız, *Kurumsal* SKU 'ya geçiş yapamazsınız. *Enterprise* SKU 'su en fazla beş güveni destekler.

Bu limitlerin hakkında daha fazla bilgi için bkz. [Azure AD DS SKU özellikleri ve sınırları][concepts-sku].

## <a name="select-a-new-sku"></a>Yeni bir SKU seçin

Azure portal kullanarak yönetilen bir etki alanının SKU 'sunu değiştirmek için aşağıdaki adımları izleyin:

1. Azure portal en üstünde **Azure AD Domain Services**' i arayıp seçin. Listeden yönetilen etki alanınızı seçin, örneğin, *aaddscontoso.com*.
1. Azure AD DS sayfanın sol tarafındaki menüde, **SKU > ayarlar**' ı seçin.

    ![Azure portal Azure AD DS yönetilen etki alanınız için SKU menü seçeneğini seçin](media/change-sku/overview-change-sku.png)

1. Açılan menüden, yönetilen etki alanınız için istediğiniz SKU 'YU seçin. Bir kaynak ormanınız varsa, orman güvenleri yalnızca *Kurumsal* SKU veya daha yüksek sürümlerde kullanılabildiği sürece *Standart* SKU 'yu seçemezsiniz.

    Açılan menüden istediğiniz SKU 'yu seçin ve ardından **Kaydet**' i seçin.

    ![Azure portal, açılan menüden gerekli SKU 'yu seçin](media/change-sku/change-sku-selection.png)

SKU türünü değiştirmek için bir dakika veya iki işlem olabilir.

## <a name="next-steps"></a>Sonraki adımlar

Bir kaynak ormanınız varsa ve SKU değişikliğinden sonra ek güvenler oluşturmak istiyorsanız bkz. [Azure AD DS şirket içi etki alanına giden orman güveni oluşturma][create-trust].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
