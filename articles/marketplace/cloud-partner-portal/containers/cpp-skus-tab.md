---
title: Azure kapsayıcıları görüntüsü için SKU 'Lar | Azure Marketi
description: Azure kapsayıcısı için SKU 'Ları yapılandırın.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 5b69eea8ad7fd4c62925b50434b653118890e280
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823158"
---
# <a name="container-skus-tab"></a>Kapsayıcı SKU 'Ları sekmesi

**Yeni teklif** sayfasının **SKU 'ları** sekmesi bir veya daha fazla SKU oluşturmanıza ve bunları yeni teklifinizle ilişkilendirmenize olanak sağlar.  Bir çözümü Özellik kümelerine, faturalandırma modellerine veya diğer özelliklerle ayırt etmek için farklı SKU 'Lar kullanabilirsiniz.

## <a name="sku-settings"></a>SKU ayarları

Yeni bir teklif oluşturmaya başladığınızda, teklifle ilişkili hiçbir SKU yoktur. Yeni bir SKU oluşturmak için aşağıdaki adımları izleyin:

1. SKU 'Lar sekmesinde **yenı SKU** ' yı seçin.

   ![Yeni SKU istemi](./media/containers-sku-settings.png)

2. Gerekli SKU ve kapsayıcı bilgilerini sağlayın. Her SKU bir kapsayıcı resmine karşılık gelir. SKU 'nun iki bölümü vardır:

    -   SKU meta verileri
    -   Kapsayıcı meta verileri


### <a name="sku-metadata"></a>SKU meta verileri

SKU meta verileri kapsayıcı listesi için storefront görüntü bilgilerini içerir.

![SKU meta verileri](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Kapsayıcı meta verileri

Kapsayıcı meta verileri Azure Container Registry (ACR) içindeki görüntü deposu ayrıntılarınızın başvuru bilgilerine sahiptir. Azure Marketi bu görüntüyü Market 'e özgü, ortak bir kayıt defterine kopyalar ve ardından görüntünün sertifikadan sonra müşterilere açık olmasını sağlar. Azure kullanıcısının Azure Marketi kapsayıcısı görüntüsünü tüketmesi için tüm isteklerin ACR değil, Market 'in ortak kayıt defterinden sunulması.

![Kapsayıcı meta verileri](./media/containers-image-repository.png)
    
Önceki ekran yakalamadaki **görüntü deposu ayrıntıları** aşağıdaki alanları içerir.  Gerekli alanlar bir yıldız işareti (*) ile tanımlanır.

-   **ABONELIK kimliği\*** -ACR 'nin mevcut olduğu Azure abonelik kimliği.
-   **Kaynak grubu adı\*** -ACR 'nin kaynak grubu adı.
-   **Kayıt defteri adı\*** -ACR adı.
-   **Depo adı\*** -Depo adı. Bu ad ayarlandıktan sonra bu değer değiştirilemez. Hesabınızdaki diğer tekliflerle çakışmayı önlemek için benzersiz bir ad kullanın.
-   **Kullanıcı adı\*** -ACR görüntüsüyle ilişkili Kullanıcı adı (Yönetici Kullanıcı adı).
-   **Parola\*** -ACR görüntüsüyle ilişkili parola.

    >[!NOTE]
    >İş ortaklarının yayımlama sürecinde belirtilen ACR 'ye erişimi olduğundan emin olmak için Kullanıcı adı ve parola gereklidir.


### <a name="image-version"></a>Görüntü Sürümü

Bir kapsayıcı görüntüsü yayımlarken bir veya daha fazla resim etiketi ve SHA digests sağlayabilirsiniz.

**Resim etiketi\* veya Özet**
 
- Bu etiket veya Özet, bir `latest` etiketi ve sürüm etiketi içermelidir (örneğin, xx bir sayı olduğu `xx.xx.xx-` başlayarak). Birden çok platformu hedeflemek için [bildirim etiketleri](https://github.com/estesp/manifest-tool) olmaları gerekir. Bir bildirim etiketi tarafından başvurulan tüm Etiketler de karşıya yüklenebilmemiz için eklenmelidir. 
- Etiketleri kullanarak kapsayıcının birkaç sürümünü ekleyebilirsiniz. Tüm bildirim etiketleri (`latest`hariç) `X.Y-` ya da X, Y, Z tamsayılardır olan `X.Y.Z-` başlamalıdır. <br/> Örneğin, bir `latest` etiketi `1.0.1-linux-x64`, `1.0.1-linux-arm32`ve `1.0.1-windows-arm32`işaret ediyorsa, bu etiketlerin buraya eklenmesi gerekir.

>[!NOTE]
>Test sırasında görüntüyü tanımlayabilmeniz için yansımanıza bir **sınama etiketi** eklemeyi unutmayın.


## <a name="next-steps"></a>Sonraki adımlar

Teklifiniz için Market açıklaması oluşturmak üzere [Market sekmesini](./cpp-marketplace-tab.md) kullanın. 
