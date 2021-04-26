---
title: Azure Uygulama teklifiniz için Önizleme hedef kitlesi ekleme
description: Iş Ortağı Merkezi 'nde Azure Uygulama teklifiniz için Önizleme izleyiciyi nasıl ekleyeceğinizi öğrenin.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 1d41e9dc39b50b92351fe38a86587a67811c5eec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370461"
---
# <a name="how-to-add-a-preview-audience-for-your-azure-application-offer"></a>Azure Uygulama teklifiniz için Önizleme hedef kitlesi ekleme

Bu makalede, ticari Market 'teki bir Azure uygulaması teklifi için Önizleme kitlelerinin nasıl yapılandırılacağı açıklanır. Teklif listelemenizi canlı olmadan önce inceleyebilir bir önizleme hedef kitlesi tanımlamanız gerekir.

## <a name="define-a-preview-audience"></a>Önizleme izleyiciyi tanımlama

**Izleyiciyi Önizle** sayfasında, daha geniş Market hedef kitlesi için canlı yayımlamadan önce Azure uygulama teklifinizi inceleyebilir sınırlı bir hedef kitle tanımlayabilirsiniz. Azure abonelik kimliklerini kullanarak önizleme kitleyi, her biri için isteğe bağlı bir açıklama ile tanımlarsınız. Bu alanlardan hiçbiri müşteriler tarafından görülenebilir. Azure abonelik KIMLIĞINIZI Azure portal içindeki **abonelikler** sayfasında bulabilirsiniz.

Teklifinizi canlı olarak yayımlanmadan önce kimin önizlemesini düzenleyebilecek bir CSV dosyası (en fazla 10) veya bir CSV dosyasını karşıya yükleyerek (100 'e kadar) en az bir adet Azure abonelik kimliği ekleyin. Teklifiniz zaten canlı ise, teklifinizdeki değişiklikleri veya güncelleştirmeleri test etmek için bir önizleme izleyiciyi yine de tanımlayabilirsiniz.

> [!NOTE]
> Önizleme hedef kitlesi, özel bir hedef kitleye göre farklılık gösterir. Bir önizleme hedef kitlesi, çevrimiçi mağazalarda canlı olarak yayımlanmadan önce teklifinizin erişimine izin verilir. Teklifinizin Market 'e tamamen yayımlandıktan sonra yalnızca özel bir hedef kitle için kullanılabilir olacağını da içeren tüm planları görebilir ve doğrulayabilir. Planı yalnızca özel bir hedef kitle için kullanılabilir hale getirebilirsiniz. Özel bir hedef kitle (bir planın **kullanılabilirlik** sekmesinde tanımlanan) belirli bir plana özel erişim sağlar.

### <a name="add-email-addresses-manually"></a>E-posta adreslerini el ile ekleme

1. **Izleyiciyi Önizle** sayfasında, sunulan kutulara tek bir Azure abonelik kimliği ve isteğe bağlı bir açıklama ekleyin.
1. Başka bir e-posta adresi eklemek için **Kimlik Ekle (en fazla 10)** bağlantısını seçin.
1. Sonraki sekmeye geçmeden önce **Taslağı kaydet** ' i seçin: teknik yapılandırma.
1. [Sonraki adımlara](#next-steps)gidin.

### <a name="add-email-addresses-using-the-csv-file"></a>CSV dosyasını kullanarak e-posta adresleri ekleme

1. **Izleyiciyi Önizle** sayfasında, **izleyiciyi dışarı aktar (CSV)** bağlantısını seçin.
1. Öğesini açın. Microsoft Excel gibi bir uygulamadaki CSV dosyası.
1. İçinde. CSV dosyası, **kimlik** sütununda, önizleme hedef kitlesi için eklemek Istediğiniz Azure abonelik kimliklerini girin.
1. **Açıklama** sütununda, isteğe bağlı olarak her bir e-posta adresi için bir açıklama ekleyebilirsiniz.
1. **Tür** sütununda, bir e-posta adresi olan her satıra **SubscriptionID** ekleyin.
1. Dosyayı olarak kaydedin. CSV dosyası.
1. **Izleyiciyi Önizle** sayfasında, **izleyiciyi içeri aktar (CSV)** bağlantısını seçin.
1. **Onayla** Iletişim kutusunda **Evet**' i seçin.
1. Öğesini seçin. CSV dosyası ve sonra **Aç**' ı seçin.
1. Sonraki sekmeye geçmeden önce **Taslağı kaydet** ' i seçin: teknik yapılandırma.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Uygulama teklifiniz için teknik ayrıntılar ekleme](create-new-azure-apps-offer-technical.md)
