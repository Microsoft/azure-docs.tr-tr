---
title: Azure API Management ilkelerini ayarlama veya düzenleme | Microsoft Docs
description: Bu konu başlığı altında, Azure API Management ilkelerinin nasıl ayarlanacağı veya düzenleneceği gösterilmektedir.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 2df57477ae5270405a1774b7a4f04ed185fea396
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071694"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Azure API Management ilkelerini ayarlama veya düzenleme

İlke tanımı, bir dizi gelen ve giden deyimleri açıklayan bir XML belgesidir. XML doğrudan tanım penceresinde düzenlenebilir. Ayrıca, ilke penceresinin sağında belirtilen listeden önceden tanımlanmış bir ilke seçebilirsiniz. Geçerli kapsam için geçerli olan deyimler etkinleştirilir ve vurgulanır. Etkin bir ifadeye tıkladığınızda, tanım görünümündeki İmlecin konumuna uygun XML eklenir. 

İlkeler hakkında ayrıntılı bilgi için bkz. [Azure API Management ilkeleri](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>İlke ayarlama veya düzenleme

Bir ilkeyi ayarlamak veya düzenlemek için aşağıdaki adımları izleyin:

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. APIM örneğinize göz atın.
3. **API'ler** sekmesine tıklayın.

    ![İlkeyi düzenle](./media/set-edit-policies/code-editor.png)

4. Daha önce içeri aktardığınız API'lerden birini seçin.
5. **Tasarım** sekmesini seçin.
6. İlkeyi uygulamak istediğiniz bir işlem seçin. İlkeyi tüm işlemlere uygulamak istiyorsanız **tüm işlemler**' i seçin.
7. **Gelen işlem** veya **giden işleme** bölümünde **(koddüzenleyici)simgesiniseçin.</>**
8. İstenen ilke kodunu uygun bloklarından birine yapıştırın.

    ```XML
    <policies>
        <inbound>
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```
 
## <a name="configure-scope"></a>Kapsamı Yapılandır

İlkeler küresel olarak veya bir ürün, API ya da Işlem kapsamında yapılandırılabilir. Bir ilkeyi yapılandırmaya başlamak için, önce ilkenin uygulanması gereken kapsamı seçmeniz gerekir.

İlke kapsamları aşağıdaki sırayla değerlendirilir:

1. Genel kapsam
2. Ürün kapsamı
3. API kapsamı
4. İşlem kapsamı

İlkeler içindeki deyimler, varsa `base` öğesinin yerleşimine göre değerlendirilir. Genel ilkenin üst ilkesi yoktur ve `<base>` öğesinde öğesinin kullanılması etkisizdir.

İlke düzenleyicisinde geçerli kapsamdaki ilkeleri görmek için, **Seçili kapsam için etkin Ilkeyi yeniden hesapla**' ya tıklayın.

### <a name="global-scope"></a>Genel kapsam

Genel kapsam, APıM örneğiniz içindeki **tüm API 'ler** için yapılandırılır.

1. [Azure Portal](https://portal.azure.com/) oturum açın ve APIM örneğinize gidin.
2. **Tüm API 'ler**' e tıklayın.

    ![Genel kapsam](./media/api-management-howto-policies/global-scope.png)

3. Üçgen simgesine tıklayın.
4. **Kod düzenleyicisi**’ni seçin.
5. İlke ekleyin veya düzenleyin.
6. **Kaydet**’e basın. 

    Değişiklikler API Management ağ geçidine hemen dağıtılır.

### <a name="product-scope"></a>Ürün kapsamı

Ürün kapsamı, seçilen ürün için yapılandırıldı.

1. **Ürünler**' e tıklayın.

    ![Ürün kapsamı](./media/api-management-howto-policies/product-scope.png)

2. İlkeleri uygulamak istediğiniz ürünü seçin.
3. **İlkeler**' e tıklayın.
4. İlke ekleyin veya düzenleyin.
5. **Kaydet**’e basın. 

### <a name="api-scope"></a>API kapsamı

API kapsamı, seçili API 'nin **tüm işlemleri** için yapılandırıldı.

1. İlkelerini uygulamak istediğiniz **API** 'yi seçin.

    ![API kapsamı](./media/api-management-howto-policies/api-scope.png)

2. **Tüm işlemler**’i seçin
3. Üçgen simgesine tıklayın.
4. **Kod düzenleyicisi**’ni seçin.
5. İlke ekleyin veya düzenleyin.
6. **Kaydet**’e basın. 

### <a name="operation-scope"></a>İşlem kapsamı 

İşlem kapsamı seçili işlem için yapılandırıldı.

1. Bir **API**seçin.
2. İlkeleri uygulamak istediğiniz işlemi seçin.

    ![İşlem kapsamı](./media/api-management-howto-policies/operation-scope.png)

3. Üçgen simgesine tıklayın.
4. **Kod düzenleyicisi**’ni seçin.
5. İlke ekleyin veya düzenleyin.
6. **Kaydet**’e basın. 

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki ilgili konulara bakın:

+ [API dönüştürme](transform-api.md)
+ İlke deyimlerinin ve ayarlarının tam listesi için [Ilke başvurusu](api-management-policy-reference.md)
+ [İlke örnekleri](policy-samples.md)
