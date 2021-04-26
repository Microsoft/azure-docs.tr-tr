---
title: Azure ayırmalarını görüntüleme ve yönetme izinleri
description: Azure portal Azure ayırmalarını görüntülemeyi ve yönetmeyi öğrenin.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: fe2f36b08f98ceb2a5f6085510b589a712ff194d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780470"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Azure ayırmalarını görüntüleme ve yönetme izinleri

Bu makalede, rezervasyon izinlerinin nasıl çalıştığı ve kullanıcıların Azure portal Azure ayırmalarını nasıl görüntüleyebileceği ve yönetebileceği açıklanmaktadır.

## <a name="who-can-manage-a-reservation-by-default"></a>Bir rezervasyonu varsayılan olarak kim yönetebilir?

Varsayılan olarak, aşağıdaki kullanıcılar rezervasyonları görüntüleyebilir ve yönetebilir:

- Rezervasyonu satın alan kişi ve rezervasyonu satın almak için kullanılan faturalama aboneliğinin hesap yöneticisi rezervasyon siparişine eklenir.
- Kurumsal Anlaşma ve Microsoft Müşteri Anlaşması faturalandırma yöneticileri.
- Tüm Azure aboneliklerini ve Yönetim gruplarını yönetmek için yükseltilmiş erişimi olan kullanıcılar

Ayırma yaşam döngüsü bir Azure aboneliğinden bağımsızdır, bu nedenle ayırma Azure aboneliği kapsamında bir kaynak değildir. Bunun yerine, kendi Azure RBAC izni aboneliklerden ayrı olan kiracı düzeyinde bir kaynaktır. Rezervasyonlar, satın alma işleminden sonraki aboneliklerden izinleri almıyor.

## <a name="how-billing-administrators-can-view-or-manage-reservations"></a>Faturalandırma yöneticileri, ayırmaları nasıl görüntüleyebilir veya yönetebilir

Faturalandırma yöneticisiyseniz, tüm ayırmaları ve rezervasyon işlemlerini görüntülemek ve yönetmek için aşağıdaki adımları kullanın.

1. [Azure Portal](https://portal.azure.com) oturum açın ve **maliyet yönetimi + faturalandırma**' a gidin.
    - EA yöneticisiyseniz, sol taraftaki menüden **faturalandırma kapsamları** ' nı seçin ve ardından Faturalandırma kapsamları listesinden bir tane seçin.
    - Microsoft Müşteri Sözleşmesi faturalama profili sahibiyseniz, sol taraftaki menüden **faturalandırma profilleri**' ni seçin. Faturalandırma profilleri listesinde bir tane seçin.
1. Sol menüde, **Ürünler + hizmet**  >  **ayırmaları**' ni seçin.
1. EA kaydınız veya faturalandırma profiliniz için rezervasyonların tüm listesi gösterilir.
1. Faturalandırma yöneticileri, bir ayırmayı seçip açılan pencerede **erişim ver** ' i seçerek bir ayırmanın sahipliğini alabilir.

### <a name="how-to-add-billing-administrators"></a>Faturalandırma yöneticileri ekleme

Bir Kullanıcıyı bir Kurumsal Anlaşma veya bir Microsoft Müşteri Anlaşmasına faturalandırma yöneticisi olarak ekleme:

- Kurumsal Anlaşma için, Kurumsal Anlaşma uygulanan tüm rezervasyon siparişlerini görüntülemek ve yönetmek üzere _Kuruluş Yöneticisi_  rolüne sahip kullanıcıları ekleyin. Kurumsal Yöneticiler, **maliyet yönetimi + faturalandırma** bölümünde rezervasyonları görüntüleyebilir ve yönetebilir.
    - _Kuruluş Yöneticisi (salt okuma)_ rolüne sahip kullanıcılar yalnızca **maliyet yönetimi + faturalandırma** üzerinden rezervasyon görüntüleyebilir. 
    - Bölüm yöneticileri ve hesap sahipleri, erişim denetimi (IAM) kullanılarak bunlara açıkça eklenmedikleri _takdirde ayırmaları_ görüntüleyemez. Daha fazla bilgi için bkz. [Azure Enterprise rollerini yönetme](../manage/understand-ea-roles.md).
- Bir Microsoft Müşteri Anlaşması için, faturalandırma profili sahibi veya faturalandırma profili katılımcısı rolüne sahip kullanıcılar, faturalandırma profilini kullanarak yapılan tüm rezervasyon satın almalarını yönetebilir. Faturalandırma profili okuyucuları ve fatura yöneticileri, faturalandırma profiliyle ilgili ödenen tüm rezervasyonları görüntüleyebilir. Ancak, rezervasyonlar üzerinde değişiklik yapamaz.
    Daha fazla bilgi için bkz. [Faturalandırma profili rolleri ve görevleri](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="view-reservations-with-azure-rbac-access"></a>Azure RBAC erişimi ile rezervasyonları görüntüleme

Ayırmayı satın aldıysanız veya bir ayırmaya eklediyseniz, ayırmaları görüntülemek ve yönetmek için aşağıdaki adımları kullanın.

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. **Tüm Hizmetler** > **Rezervasyon**'u seçerek erişim sahibi olduğunuz rezervasyonları listeleyin.

## <a name="users-with-elevated-access-can-manage-all-azure-subscriptions-and-management-groups"></a>Yükseltilmiş erişimi olan kullanıcılar, tüm Azure aboneliklerini ve Yönetim gruplarını yönetebilir

[Tüm Azure aboneliklerini ve Yönetim gruplarını yönetmek için](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json)bir kullanıcının erişimini yükseltebilir.

Erişimi yükseltdikten sonra:

1.   >  Kiracıdaki tüm rezervasyonları görmek için tüm hizmet **rezervasyonuna** gidin.
1. Ayırmaya değişiklikler yapmak için, erişim denetimi (ıAM) kullanarak kendi rezervasyon sırasının sahibi olarak kendinizi ekleyin.

## <a name="give-users-azure-rbac-access-to-individual-reservations"></a>Kullanıcılara Azure RBAC 'e tek tek ayırmalar erişimi verme

Rezervasyonlar ve faturalandırma yöneticileri üzerinde sahip erişimi olan kullanıcılar, ayrı bir rezervasyon siparişi için erişim yönetimini temsil edebilir.

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. **Tüm Hizmetler** > **Rezervasyon**'u seçerek erişim sahibi olduğunuz rezervasyonları listeleyin.
1. Diğer kullanıcılara erişim vermek istediğiniz rezervasyonu seçin.
1. Rezervasyon ayrıntılarından rezervasyon siparişini seçin.
1. **Erişim denetimi (IAM)** öğesini seçin.
1. **Rol ataması ekle** > **Rol** > **Sahip**'i seçin. Sınırlı erişim vermek istiyorsanız farklı bir rol seçin.
1. Sahip olarak eklemek istediğiniz kullanıcının e-posta adresini yazın.
1. Kullanıcıyı ve ardından **Kaydet**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Rezervasyonları’nı yönetin](manage-reserved-vm-instance.md).