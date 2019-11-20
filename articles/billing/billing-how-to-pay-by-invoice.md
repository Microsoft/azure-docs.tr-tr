---
title: Azure abonelikleri için fatura ile ödeme
description: Azure abonelikleri için fatura ile nasıl ödeme yapılacağını açıklar.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: banders
ms.openlocfilehash: 9e4e05acd88e9b0f0c17d4dd4caf5eb5a883d63d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70012587"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Azure abonelikleriniz için fatura ile ödeme

Faturayla ödemeye geçmeniz, faturanızı fatura tarihini izleyen 30 gün içinde çek/havale ile ödeyeceğiniz anlamına gelir. Azure aboneliğinizi faturayla ödemeye uygun duruma gelmek için, Azure desteğine bir istek gönderin. İsteğiniz onaylandıktan sonra [Azure portalında](https://portal.azure.com) fatura ödemesine (çek/havale) geçiş yapabilirsiniz.

> [!IMPORTANT]
> * Fatura ödemesi (çek/havale) yalnızca kurumsal hesaplar için kullanılabilir.
> * Fatura ödemesine geçiş yapmadan önce tüm kalan ücretleri ödeyin.
> * Şu anda fatura ödemesi Çin’de Küresel Azure için desteklenmemektedir.

## <a name="request-to-pay-by-invoice"></a>Fatura ile ödeme isteği gönderme

1. [Azure Portal](https://portal.azure.com/) oturum açın. **Yardım + destek** > **Yeni destek isteği** seçeneğini belirleyin.

    ![Yardım ve destek bağlantısı](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. **Sorun türü** olarak **Faturalama**’yı seçin. *Sorun türü*, destek isteği kategorisidir. Sırayla, fatura ile ödeme yapmak istediğiniz aboneliği, bir destek planını ve **İleri**’yi seçin.

3. **Sorun Türü** olarak **Ödeme**’yi seçin. *Sorun türü*, destek isteği alt kategorisidir.

4. **Sorun alt türü** olarak **Fatura ile Ödemeye Geçiş Yapma**’yı seçin

5. **Ayrıntılar** kutusuna aşağıdaki bilgileri girin ve sonra **İleri**‘yi seçin.

         New or existing customer:
         If existing, current payment method:
         Order ID (requesting for invoice option):
         Account Admins Live ID (or Org ID) (should be company domain):
         Commerce Account ID:
         Company Name (as registered under VAT or Government Website):
         Company Address (as registered under VAT or Government Website):
         Company Website:
         Country:
         TAX ID/ VAT ID:
         Company Established on (Year):
         Any prior business with Microsoft:
         Contact Name:
         Contact Phone:
         Contact Email:
         Justification on why you prefer Invoice option over credit card:

        For cores increase, provide the following additional information:

         (Old quota) Existing Cores:
         (New quota) Requested cores:
         Specific region & series of Subscription:

    - **Şirket adı** ve **Şirket adresi**, Azure hesabı için sağladığınız bilgilerle aynı olmalıdır. Bilgileri görüntülemek veya güncelleştirmek için bkz. [Azure hesap profili bilgilerinizi değiştirme](billing-how-to-change-azure-account-profile.md).
    - Kredi limitinin onaylanabilmesi için önce Azure portalında faturalama iletişim bilgilerinizi ekleyin. İletişim ayrıntıları, şirketin Borç Hesapları veya Finans bölümü ile ilişkili olmalıdır. Faturalama iletişim bilgilerini güncelleştirmek için [Azure Hesap Merkezi](https://account.azure.com/Profile)‘ne gidin.

6. İletişim bilgilerinizi ve tercih edilen iletişim yöntemini seçip **Oluştur**'a tıklayın.

İhtiyaç duyduğunuz kredi tutarı nedeniyle bir kredi denetimi çalıştırmamız gerekirse size bir kredi denetimi uygulaması göndeririz.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Fatura ödemesine (çek/havale) geçiş yapma

Fatura ile ödeme yapmanız onaylandıktan sonra, Azure portalında fatura ödemesine (çek/havale) geçiş yapabilirsiniz.

Microsoft Online Services Program hesabınız varsa, Azure aboneliğinizi çek/havale yoluyla ödemeye geçirebilirsiniz. Microsoft Müşteri Sözleşmesi ile, faturalama profilinizi çek/havale yoluyla ödemeye geçirebilirsiniz. [Hesap türünüzü denetlemeyi öğrenin](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Azure aboneliğini çek/havale yoluyla ödemeye geçirme

Azure aboneliğinizi fatura ödemesine (çek/havale) geçirmek için aşağıdaki adımları izleyin. **Fatura ödemesine (çek/havale) geçiş yaptıktan sonra kredi kartına geri dönemezsiniz**.

1. [Azure portalında](https://portal.azure.com) Hesap Yöneticisi olarak oturum açın.
1. **Maliyet Yönetimi + Faturalama** araması yapın.

    ![Aramayı gösteren ekran görüntüsü](./media/billing-how-to-pay-by-invoice/search.png)

1. Faturalı ödemeye geçirmek istediğiniz aboneliği seçin.
1. **Ödeme yöntemleri**'ni seçin.
1. Komut çubuğunda **Fatura ile ödeme** düğmesine tıklayın.

    ![Fatura ile ödeme düğmesini gösteren ekran görüntüsü](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Faturalama profilini çek/havale yoluyla ödemeye geçirme

Faturalama profilini çek/havale yoluyla ödemeye geçirmek için aşağıdaki adımları izleyin. Yalnızca Azure’a kaydolan kişi bir faturalama profilinin varsayılan ödeme yöntemini değiştirebilir.

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. **Maliyet Yönetimi + Faturalama** araması yapın.
1. Soldaki menüde **Faturalama profili**’ne tıklayın.

    ![menüde faturalama profilini gösteren ekran görüntüsü](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. Faturalama profilini seçin.
1. Soldaki menüden **Ödeme yöntemleri**’ni seçin.

   ![Menüde ödeme yöntemlerini gösteren ekran görüntüsü](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. Çek/havale yoluyla ödeme yapmaya uygun olduğunuzu belirten mavi başlığa tıklayın.

    ![Çek/havale yoluyla ödemeye geçiş yapmak için mavi başlığı gösteren ekran görüntüsü](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmesi’ne erişimi denetleme
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

- Gerekirse, [Azure Hesap Merkezi](https://account.azure.com/Profile)’nde faturalama iletişim bilgilerinizi güncelleştirin.
