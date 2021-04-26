---
title: Yönetilen hizmet teklifiniz için Önizleme hedef kitlesi ekleme
description: Microsoft Iş Ortağı Merkezi 'nde yönetilen hizmet teklifiniz için Önizleme izleyiciyi nasıl ekleyeceğinizi öğrenin.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 072f1d63e0b3df898ef170205b5c560432d870b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97918416"
---
# <a name="how-to-add-a-preview-audience-for-your-managed-service-offer"></a>Yönetilen hizmet teklifiniz için Önizleme hedef kitlesi ekleme

Bu makalede, Iş Ortağı Merkezi 'ni kullanarak ticari Market 'te yönetilen hizmet teklifi için Önizleme kitlelerinin nasıl yapılandırılacağı açıklanır. Önizleme hedef kitlesi, teklifinizi canlı olmadan önce gözden geçirebilir.

## <a name="define-a-preview-audience"></a>Önizleme izleyiciyi tanımlama

**Izleyiciyi Önizle** sayfasında, daha geniş Market hedef kitlesi için canlı yayımlamadan önce, yönetilen hizmet teklifinizi incelebilecek sınırlı bir hedef kitle tanımlayabilirsiniz. Azure abonelik kimliklerini kullanarak önizleme kitleyi, her biri için isteğe bağlı bir açıklama ile tanımlarsınız. Bu alanlardan hiçbiri müşteriler tarafından görülenebilir. Azure abonelik KIMLIĞINIZI, Azure portal **abonelikler** sayfasında bulabilirsiniz.

Teklifinizi canlı olarak yayımlanmadan önce kimin önizlemesini düzenleyebilecek bir CSV dosyası (en fazla 10) veya bir CSV dosyasını karşıya yükleyerek (100 'e kadar) en az bir Azure abonelik KIMLIĞI ekleyin. Teklifiniz zaten canlı ise, teklifiniz için güncelleştirmelerin test edilmesi için bir önizleme izleyiciyi de tanımlayabilirsiniz.

> [!NOTE]
> *Önizleme* hedef kitlesi, *özel* bir hedef kitleye göre farklılık gösterir. Bir önizleme hedef kitlesi, çevrimiçi mağazalarda canlı olarak yayımlanmadan önce teklifinizin erişimine izin verilir. Bu kişiler, yalnızca teklifiniz Market 'te tamamen yayımlandıktan sonra özel bir hedef kitle için kullanılabilecek olanlar da dahil olmak üzere tüm planları görebilir ve doğrulayabilir. Planı yalnızca özel bir hedef kitle için kullanılabilir hale getirebilirsiniz. Özel bir hedef kitle (bir planın kullanılabilirlik sekmesinde tanımlanan) belirli bir plana özel erişim sağlar.

## <a name="add-email-addresses-manually"></a>E-posta adreslerini el ile ekleme

1. **Izleyiciyi Önizle** sayfasında, sunulan kutulara tek bir Azure abonelik kimliği ve isteğe bağlı bir açıklama ekleyin.
2. Başka bir e-posta adresi eklemek için **Kimlik Ekle (en fazla 10)** bağlantısını seçin.
3. Sonraki sekmeye geçmeden önce **Taslağı kaydet** ' i seçin.

## <a name="add-email-addresses-using-a-csv-file"></a>CSV dosyası kullanarak e-posta adresleri ekleme

1. İzleyici izleyiciyi **Önizle izleyici** sayfasında, **izleyiciyi dışarı aktar (CSV)** bağlantısını seçin.
2. CSV dosyasını açın. **Kimlik** sütununda, önizleme hedef kitlesi için eklemek istediğiniz Azure abonelik kimliklerini girin.
3. **Açıklama** sütununda, her giriş için bir açıklama ekleme seçeneğiniz vardır.
4. **Tür** sütununda, kimliği olan her satıra **SubscriptionID** ekleyin.
5. Dosyayı bir CSV dosyası olarak kaydedin.
6. **Izleyiciyi Önizle** sayfasında, **izleyiciyi içeri aktar (CSV)** bağlantısını seçin.
7. **Onayla** Iletişim kutusunda **Evet**' i seçin ve ardından CSV dosyasını karşıya yükleyin.
8. Sonraki sekmeye geçmeden önce **Taslağı kaydet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Plan oluşturma](create-managed-service-offer-plans.md)
