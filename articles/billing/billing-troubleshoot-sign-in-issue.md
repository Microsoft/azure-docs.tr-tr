---
title: Azure aboneliği oturum açma sorunlarını giderme
description: Azure portalında veya Azure hesap merkezinde oturum açamama sorunlarının çözülmesine yardımcı olur.
services: azure
author: v-miegge
manager: dcscontentpm
editor: na
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: e2cbaa5cf226eaf9835c1a63d21fae54c7093117
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795430"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Azure aboneliği oturum açma sorunlarını giderme

Bu kılavuz, Azure portalında veya Azure hesap merkezinde oturum açamama sorunlarının çözülmesine yardımcı olur. 

## <a name="issues"></a>Sorunlar

### <a name="page-hangs-in-the-loading-status"></a>Sayfa, yükleme durumunda yanıt vermemeye başlıyor

İnternet tarayıcınızın sayfası yanıt vermemeye başlıyorsa Azure portalına ulaşana kadar aşağıdaki adımlardan her birini deneyin.

- Sayfayı yenileyin.
- Farklı bir İnternet tarayıcısı kullanın.
- Tarayıcınızda özel gözatma modunu kullanın. Internet Explorer için: **Araçlar** > **Güvenlik** > **InPrivate Gözatma**’ya tıklayın ve sonra [Azure portalına](https://portal.azure.com/) veya [Azure hesap merkezine](https://account.azure.com/Subscriptions) gidip oturum açın.

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Otomatik şekilde farklı kullanıcı olarak oturum açtınız

İnternet tarayıcısında birden çok kullanıcı hesabı kullanıyorsanız bu sorun oluşabilir.

Sorunu çözmek için aşağıdaki yöntemlerden birini deneyin:

- Önbelleği temizleyin ve İnternet tanımlama bilgilerini silin. Internet Explorer’da **Araçlar** > **Internet Seçenekleri** > **Sil**’e tıklayın. Geçici dosyalar, tanımlama bilgileri, parola ve gözatma geçmişi onay kutularının seçildiğinden emin olun ve Sil’e tıklayın.
- Yaptığınız tüm kişisel ayarları geri almak için Internet Explorer ayarlarını sıfırlayın. **Araçlar** > **Internet Seçenekleri** > **Gelişmiş**’e tıklayın > **Kişisel ayarları sil** kutusu > **Sıfırla**’yı seçin.
- Tarayıcınızda özel gözatma modunu kullanın. Internet Explorer için:  **Araçlar** > **Güvenlik** > **InPrivate Gözatma**’ya tıklayın ve sonra [Azure portalına](https://portal.azure.com/) veya [Azure hesap merkezine](https://account.azure.com/Subscriptions) gidip oturum açın.

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Oturum açabiliyorum, ancak *Abonelik bulunamadı* iletisini görüyorum

Yanlış dizinde seçim yaptıysanız veya hesabınız yeterli izinlere sahip değilse bu sorun oluşur.

**Senaryo 1:** [Azure portalında](https://portal.azure.com/) hata iletisi alınır

Bu sorunu düzeltmek için:

- Sağ üst kısımda hesabınıza tıklayarak doğru Azure dizininin seçildiğinden emin olun.
- Doğru Azure dizini seçili olduğu halde hata iletisini almaya devam ediyorsanız hesabınızın [Sahip olarak eklenmesini](billing-add-change-azure-subscription-administrator.md) sağlayın.

**Senaryo 2:** [Azure Hesap Merkezi](https://account.windowsazure.com/Subscriptions)’nde hata iletisi alınır

Kullandığınız hesabın Hesap Yöneticisi olup olmadığını denetleyin. Hesap Yöneticisinin kim olduğunu doğrulamak için şu adımları izleyin:

1. [Azure portalındaki Abonelikler görünümünde](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) oturum açın.

2. Denetlemek istediğiniz aboneliği seçin ve **Ayarlar** bölümüne bakın.

3. **Özellikler**’i seçin. Aboneliğin hesap yöneticisi, **Hesap Yöneticisi** kutusunda görüntülenir.

## <a name="additional-help-resources"></a>Ek yardım kaynakları

Azure Faturalama ve Abonelikler için diğer sorun giderme makaleleri

- [Reddedilen kart](billing-troubleshoot-declined-card.md)
- [Abonelik kaydı sorunları](billing-troubleshoot-azure-sign-up.md)
- [Abonelik bulunamadı](billing-no-subscriptions-found.md)
- [Kurumsal maliyet görünümü devre dışı](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Yardım için bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Faturalama belgeleri](index.md)
