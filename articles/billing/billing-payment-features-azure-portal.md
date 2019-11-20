---
title: Azure portalındaki Hesap Yöneticisi görevleri
description: Azure portalında ödeme işlemlerinin nasıl gerçekleştirileceğini açıklar
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: banders
ms.openlocfilehash: 510765778166f007501cfcb21e242c539200ce2f
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73722151"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Azure portalındaki Hesap Yöneticisi görevleri

Bu makalede aşağıdaki görevlerin Azure portalında nasıl gerçekleştirileceği anlatılmaktadır:
- Aboneliğinizin ödeme yöntemlerini yönetme
- Aboneliğinizin harcama limitini kaldırma
- Open ile Azure aboneliğinize kredi ekleme

Bu görevleri gerçekleştirmek için Hesap Yöneticisi olmanız gerekir. 

## <a name="navigate-to-your-subscriptions-payment-methods"></a>Aboneliğinizin ödeme yöntemlerine gitme

1. [Azure portalında](https://portal.azure.com) Hesap Yöneticisi olarak oturum açın.
1. **Maliyet Yönetimi + Faturalama** araması yapın.
    
    ![Maliyet yönetimi + faturalama aramasını gösteren ekran görüntüsü ](./media/billing-payment-features-azure-portal/search-bar.png)

1. **Aboneliklerim** listesinde kredi kartını eklemek istediğiniz aboneliği seçin.
    ![Aboneliklerim listesine genel bakışı gösteren ekran görüntüsü](./media/billing-payment-features-azure-portal/cost-management-billing-overview-x.png)

1. **Ödeme yöntemleri**'ni seçin.

    ![Seçilen dikey pencerede ödeme yöntemlerini gösteren ekran görüntüsü.](./media/billing-payment-features-azure-portal/subscription-payment-methods-blade.png)

Burada yeni bir kredi kartı ekleyebilir, etkin ödeme yöntemini değiştirebilir, kredi kartı ayrıntılarını düzenleyebilir ve kredi kartlarını silebilirsiniz. 

### <a name="change-active-payment-method"></a>Etkin ödeme yöntemi değiştirme

Yeni bir kredi kartı ekleyerek veya kayıtlı olan kartlardan birini seçerek etkin ödeme yöntemini değiştirebilirsiniz. Etkin ödeme yöntemini yeni bir kredi kartı olarak değiştirmek için:

1. Sol üst köşede "+" işaretini seçerek bir kredi kartı ekleyin.
    
    ![Artı işaretini gösteren ekran görüntüsü](./media/billing-payment-features-azure-portal/subscription-payment-methods-plus.png)

1. Sağ taraftaki forma kredi kartı bilgilerini girin.

    ![Kredi kartı formunu gösteren ekran görüntüsü.](./media/billing-payment-features-azure-portal/subscription-add-payment-method-x.png)

1. Bu kartın etkin ödeme yönteminiz olmasını sağlamak için, formun üst kısmında **Bunu etkin ödeme yöntemim yap** seçeneğinin yanındaki kutuyu işaretleyin. Bu kart, seçilen abonelik olarak aynı kartı kullanan tüm aboneliklerde etkin ödeme aracınız olur.

    ![Kartın etkin ödeme yöntemi olarak belirlenmesini sağlayan onay kutusunu gösteren ekran görüntüsü.](./media/billing-payment-features-azure-portal/subscription-make-active-payment-method-x.png)

1. **İleri**’yi seçin.

Etkin ödeme yöntemini önceden kaydedilmiş bir kredi kartı olarak değiştirmek için:

1. Etkin ödeme yöntemi olmasını istediğiniz kartın yanındaki kutuyu seçin.

    ![Kredi kartının yanındaki işaretlenmiş olan kutuyu gösteren ekran görüntüsü](./media/billing-payment-features-azure-portal/subscription-checked-payment-method-x.png)

1. Komut çubuğunda **Etkin olarak ayarla**'ya tıklayın.

    ![Etkin olarak ayarla düğmesini gösteren ekran görüntüsü](./media/billing-payment-features-azure-portal/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>Kredi kartı bilgilerini girme

Son kullanma tarihi veya adres gibi kredi kartı bilgilerini düzenlemek için değişiklik yapacağınız kredi kartına tıklayın. Sağ tarafta bir kredi kartı formu görüntülenir.

![Kredi kartının seçili olduğunu gösteren ekran görüntüsü](./media/billing-payment-features-azure-portal/subscription-edit-payment-method-x.png)

Kredi kartı bilgilerini güncelleştirin ve **Kaydet**'e tıklayın.

### <a name="remove-a-credit-card-from-the-account"></a>Hesaptan kredi kartını kaldırma

1. Silmek istediğiniz kartın yanındaki kutuyu seçin.

    ![Kredi kartının yanındaki işaretlenmiş olan kutuyu gösteren ekran görüntüsü](./media/billing-payment-features-azure-portal/subscription-checked-payment-method-x.png)

1. Komut çubuğunda **Sil**’e tıklayın.

    ![Sil düğmesini gösteren ekran görüntüsü](./media/billing-payment-features-azure-portal/subscription-checked-payment-method-delete.png)

Kredi kartınız herhangi bir Microsoft aboneliğiniz için etkin ödeme yöntemiyse onu Azure hesabınızdan kaldıramazsınız. Bu kredi kartına bağlı tüm abonelikler için etkin ödeme yöntemini değiştirip yeniden deneyin.

### <a name="switch-to-invoice-payment"></a>Fatura ile ödemeye geçiş yapma

Fatura (çek/havale) ile ödemeye uygunsanız Azure portalında aboneliğinizi faturayla (çek/havale) yapacak şekilde değiştirebilirsiniz.

1. Komut çubuğunda **Fatura ile ödeme**'yi seçin.

    ![Seçilen dikey pencerede ödeme yöntemlerini gösteren ekran görüntüsü.](./media/billing-payment-features-azure-portal/subscription-payment-methods-pay-by-invoice.png)

1. Fatura ile ödeme yöntemine ait adresi girin.
1. **İleri**’ye tıklayın.

Fatura ile ödeme yapmak için onay almak isterseniz bkz. [Fatura ile ödeme yapmayı öğrenme](billing-how-to-pay-by-invoice.md).

### <a name="edit-invoice-payment-address"></a>Fatura ile ödeme adresini düzenleme

Fatura ödeme yönteminizin adresini düzenlemek için aboneliğinize ait ödeme yöntemleri listesinde **Fatura**'ya tıklayın. Sağ tarafta adres formu açılır. 

## <a name="remove-spending-limit"></a>Harcama limitini kaldırma

Azure’daki harcama limiti, harcamanızın kredi tutarınızın limitini aşmasını engeller. Azure aboneliğinizle ilişkili geçerli bir ödeme yöntemi olduğu sürece, harcama limitini istediğiniz zaman kaldırabilirsiniz. Visual Studio Enterprise ve Visual Studio Professional gibi birden çok aya yayılan krediler içeren abonelik türleri için, bir sonraki faturalama döneminizin başlangıcında harcama limitini yeniden etkinleştirebilirsiniz.

Harcama limiti, taahhüt planları veya kullandıkça öde fiyatlandırmasının geçerli olduğu abonelikler için kullanılamaz. [Tüm Azure abonelik türlerini ve harcama limiti kullanılabilirliğini gösteren listeye](https://azure.microsoft.com/support/legal/offer-details/) göz atın.

1. [Azure portalında](https://portal.azure.com) Hesap Yöneticisi olarak oturum açın.
1. **Maliyet Yönetimi + Faturalama** araması yapın.

    ![Maliyet yönetimi + faturalama aramasını gösteren ekran görüntüsü ](./media/billing-payment-features-azure-portal/search-bar.png)

1. **Aboneliklerim** listesinde Visual Studio Enterprise aboneliğinizi seçin.
    
    ![Aboneliklerim listesine genel bakışı gösteren ekran görüntüsü](./media/billing-payment-features-azure-portal/cost-management-overview-msdn-x.png)

1. Harcama limitini kaldırmak için Abonelik genel görünümünde turuncu renkli başlığa tıklayın.
    
    ![Harcama limitini kaldırma başlığını gösteren ekran görüntüsü](./media/billing-payment-features-azure-portal/msdn-remove-spending-limit-banner-x.png)

1. Harcama limitini süresiz olarak mı yoksa yalnızca geçerli fatura dönemi için mi kaldırmak istediğinizi seçin.

   ![Harcama limitini kaldırma dikey penceresini gösteren ekran görüntüsü](./media/billing-payment-features-azure-portal/remove-spending-limit-blade-x.png)

1. Aboneliğiniz için bir ödeme yöntemi seçmek üzere **Ödeme yöntemini seçin**'e tıklayın. Bu yöntem, aboneliğinizin etkin ödeme yöntemi olur.

1. **Son**'a tıklayın.

## <a name="add-credits-to-azure-in-open-subscription"></a>Open ile Azure aboneliğine kredi ekleme

Open ile Azure Lisansı aboneliğiniz varsa Azure portalında bir ürün anahtarını kullanarak veya kredi kartı ile kredi satın alarak aboneliğinize kredi ekleyebilirsiniz.

1. [Azure portalında](https://portal.azure.com) Hesap Yöneticisi olarak oturum açın.
1. **Maliyet Yönetimi + Faturalama** araması yapın.

    ![Maliyet yönetimi + faturalama aramasını gösteren ekran görüntüsü ](./media/billing-payment-features-azure-portal/search-bar.png)

1. **Aboneliklerim** listesinde Open ile Azure aboneliğinizi seçin.
   
    ![Aboneliklerim listesine genel bakışı gösteren ekran görüntüsü](./media/billing-payment-features-azure-portal/cost-management-overview-aio-x.png)

1. **Kredi geçmişi**'ni seçin.
    
    ![Kredi geçmişini gösteren ekran görüntüsü](./media/billing-payment-features-azure-portal/aio-credit-history-blade.png)

1. Sol üst köşede "+" işaretini seçerek kredi ekleyin.

    ![Kredi ekleme düğmesini gösteren ekran görüntüsü](./media/billing-payment-features-azure-portal/aio-credit-history-plus.png)

1. Açılan kutudan bir ödeme yöntemi türü seçin. Ürün anahtarı ekleyebilir veya kredi kartıyla kredi satın alabilirsiniz.
    
    ![Kredi ekleme dikey penceresindeki ödeme yöntemini gösteren ekran görüntüsü](./media/billing-payment-features-azure-portal/add-credits-select-payment-method.png)

1. Ürün anahtarını tercih ederseniz:
    - Ürün anahtarını girin.
    - **Doğrula**'ya tıklayın.

1. Kredi kartını tercih ederseniz:
    - **Ödeme yöntemini seçin**'e tıklayarak kredi kartı ekleyin veya mevcut kartlardan birini seçin.
    - Eklemek istediğiniz kredi miktarını belirtin.

1. **Uygula**'ya tıklayın.

## <a name="troubleshooting"></a>Sorun giderme
Sanal veya ön ödemeli kartları desteklemiyoruz. Geçerli bir kredi kartı eklerken veya güncelleştirirken hata alıyorsanız tarayıcınızı gizli modda açmayı deneyin.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure portalında beklenmeyen maliyetleri analiz etme ve önleme](billing-getting-started.md) hakkında daha fazla bilgi edinin
