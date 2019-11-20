---
title: Azure müşterileri için Açık Bankacılık (P2D2) ve Güçlü Müşteri Kimlik Doğrulaması (SCA)
description: Bu makalede bazı Azure satın almalarında çok faktörlü kimlik doğrulamasının neden gerekli olduğu ve kimlik doğrulamasının nasıl tamamlanacağı açıklanır.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: banders
ms.openlocfilehash: 629a14a08b523016981300181dc4cad4549c798f
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174518"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>Azure müşterileri için Açık Bankacılık (P2D2) ve Güçlü Müşteri Kimlik Doğrulaması (SCA)

14 Eylül 2019'dan itibaren [Avrupa Ekonomik Alanı](https://en.wikipedia.org/wiki/European_Economic_Area)'nın 31 ülkesinde yer alan bankalarda çevrimiçi alışveriş yapan müşterinin ödemesi işlenmeden önce kimliğini doğrulaması gerekmektedir. Bu doğrulama, çevrimiçi satın alma işlemlerinizin güvenliğinden ve koruma altında olduğundan emin olmak için çok faktörlü kimlik doğrulaması gerektirir. Bu doğrulama gereksiniminin başlangıç tarihi bazı ülkelerde geciktirilecektir. Daha fazla bilgi için bkz. [PSD2 hakkında Microsoft SSS](https://support.microsoft.com/en-us/help/4517854?preview).

## <a name="what-psd2-means-for-azure-customers"></a>Azure müşterileri açısından PSD2 ne anlama gelir?

Azure için ödeme yaparken [Avrupa Ekonomik Alanı](https://en.wikipedia.org/wiki/European_Economic_Area)'ndaki bir bankanın kredi kartını kullanıyorsanız, hesabınızın ödeme yöntemi için çok faktörlü kimlik doğrulamasını tamamlamanız gerekebilir. Azure hesabınıza kaydolurken veya Azure hesabınızı yükseltirken (o sırada alışveriş yapmıyor olsanız bile) çok faktörlü kimlik doğrulaması sınamasını tamamlamanız istenebilir. Ayrıca Azure hesabınızın ödeme yöntemini değiştirirken, harcama limitinizi kaldırırken veya Azure portalından anlık bir ödeme yaparken de (ödenmemiş bakiyeyi kapatma veya Azure kredisi satın alma gibi) çok faktörlü kimlik doğrulaması sağlamanız istenebilir.

Bankanız aylık Azure ücretlerinizi reddederse, Azure'dan durumu düzeltme yönergelerini de içeren bir vadesi geçmiş ödeme e-postası alırsınız. Azure portalında çok faktörlü kimlik doğrulaması sınamasını tamamlayabilir ve ödenmemiş borçları ödeyebilirsiniz.

## <a name="complete-multi-factor-authentication-in-the-azure-portal"></a>Azure portalında çok faktörlü kimlik doğrulamasını tamamlama

Aşağıdaki bölümlerde Azure portalında çok faktörlü kimlik doğrulamasının nasıl tamamlanacağı açıklanır. Size uygun olan bilgileri kullanın.

### <a name="change-the-active-payment-method-of-your-azure-account"></a>Azure hesabınızın etkin ödeme yöntemini değiştirme

Şu adımları izleyerek Azure hesabınızın etkin ödeme yöntemini değiştirebilirsiniz:

1. [Azure portalında](https://portal.azure.com) Hesap Yöneticisi olarak oturum açın ve **Maliyet Yönetimi + Faturalama**'ya gidin.
2. **Genel Bakış** sayfasında, **Aboneliklerim** kılavuzundan ilgili aboneliği seçin.
3. 'Faturalama' bölümünde **Ödeme yöntemleri**'ni seçin. Abonelik için etkin ödeme yöntemi olarak yeni kredi kartı ekleyebilir veya mevcut kartı ayarlayabilirsiniz. Bankanız çok faktörlü kimlik doğrulaması gerektiriyorsa, işlem sırasında kimlik doğrulaması sınamasını tamamlamanız istenir.

Diğer ayrıntılar için bkz. [Azure için kredi kartını ekleme, güncelleştirme veya kaldırma](billing-how-to-change-credit-card.md).

### <a name="settle-outstanding-charges-for-azure-services"></a>Azure hizmetleri için ödenmemiş borçları ödeme

Banka ücretleri reddederse Azure portalında Azure hesabınızın durumu **Süresi geçti** olarak değişir. Şu adımları izleyerek hesabınızın durumunu denetleyebilirsiniz:

1. [Azure portalında](https://portal.azure.com/) Hesap Yöneticisi olarak oturum açın.
2. **Maliyet Yönetimi + Faturalama** araması yapın.
3. **Maliyet Yönetimi + Faturalama** **Genel Bakış** sayfasında, **Aboneliklerim** kılavuzundaki durum sütununu gözden geçirin.
4. Aboneliğiniz **Süresi geçti** olarak etiketlendiyse **Bakiyeyi ödeyin** bağlantısına tıklayın. İşlem sırasında çok faktörlü kimlik doğrulamasını tamamlamanız istenir.

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>Market ve rezervasyon satın almaları için ödenmemiş ücretleri ödeme

Market ve rezervasyon satın almaları, Azure hizmetlerinden ayrı faturalanır. Bankanız Market veya rezervasyon ücretlerini reddederse, Azure portalında fatura durumunuz **Süresi geçti** olarak gösterilir. Şu adımları izleyerek Market ve rezervasyon faturalarınızın durumunu denetleyebilirsiniz:

1. [Azure portalında](https://portal.azure.com/) Hesap Yöneticisi olarak oturum açın.
2. **Maliyet Yönetimi + Faturalama** araması yapın.
3. 'Faturalama'nın altında **Faturalar**'ı seçin.
4. Sağ tarafta **Azure Market ve rezervasyonlar** sekmesine tıklayın.
5. İlgili aboneliği seçin.
6. Faturalar kılavuzunda durum sütununu gözden geçirin. Fatura **Bitiş tarihi** veya **Süresi geçti** durumundaysa **Şimdi ödeyin** öğesine tıklayın. İşlem sırasında çok faktörlü kimlik doğrulamasını tamamlamanız istenir.

## <a name="next-steps"></a>Sonraki adımlar
- Azure faturasını ödemeniz gerekiyorsa bkz. [Azure aboneliğinizin süresi geçen bakiye sorununu çözme](billing-azure-subscription-past-due-balance.md).
