---
title: Yalnızca rapor modunda bir koşullu erişim ilkesi Yapılandırma-Azure Active Directory
description: Benimseme yardımcı olması için koşullu erişimde yalnızca rapor modunu kullanma
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3cd57c09c08ab4c86feeca27915639123b439d0
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180309"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>Yalnızca rapor modunda bir koşullu erişim ilkesi yapılandırma (Önizleme)

Yalnızca rapor modunda bir koşullu erişim ilkesi yapılandırmak için:

1. **Azure Portal** , koşullu erişim Yöneticisi, güvenlik yöneticisi veya genel yönetici olarak oturum açın.
1. **Koşullu erişim** > **Azure Active Directory** > **Güvenliğe** gidin.
1. **Yeni ilke**' yi seçin.
1. İlke koşullarını ve gereken izin denetimlerini gerekli şekilde yapılandırın.
1. **Ilkeyi etkinleştir** altında, değiştirme modunu **yalnızca rapor** moduna ayarlayın.
1. **Kaydet**’i seçin

> [!TIP]
> Mevcut bir ilkenin **ilke** durumunu **Açık** iken **yalnızca rapor** olarak düzenleyebilirsiniz ancak bunu yaptığınızda ilke zorlaması devre dışı bırakılır. 

Yalnızca raporu görüntüle-Azure AD oturum açma günlüklerinde yalnızca sonuç elde edin.

Belirli bir oturum açma için yalnızca rapor ilkesinin sonucunu görüntülemek için:

1. **Azure Portal** bir rapor okuyucu, güvenlik okuyucusu, güvenlik yöneticisi veya genel yönetici olarak oturum açın.
1. **Azure Active Directory** > **oturum açma**işlemlerini inceleyin.
1. Bir oturum açma seçin veya dar sonuçlara filtre ekleyin.
1. **Ayrıntılar** çekmecede, oturum açma sırasında değerlendirilen ilkeleri görüntülemek Için **yalnızca rapor (Önizleme)** sekmesini seçin.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Azure AD ile Azure Izleyici tümleştirmesini ayarlama

Koşullu erişim ilkelerinin yeni koşullu erişim öngörüleri çalışma kitabını kullanarak toplam etkisini görüntülemek için Azure Izleyici 'yi Azure AD ile tümleştirmeli ve oturum açma günlüklerini dışarı aktarmanız gerekir. Bu tümleştirmeyi ayarlamak için iki adım vardır: 

1. [Azure izleyici aboneliğine kaydolun ve bir çalışma alanı oluşturun](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).
1. [Azure AD 'de oturum açma günlüklerini Azure izleyici 'ye aktarın](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Azure Izleyici fiyatlandırması hakkında daha fazla bilgi için [Azure izleyici fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/monitor/)bulabilirsiniz. Maliyetleri tahmin etme, günlük üst sınır ayarlama veya veri saklama süresini özelleştirme için kaynaklar, [Azure Izleyici günlükleri ile kullanım ve maliyetleri yönetme](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment)makalesinde bulunabilir.

## <a name="view-conditional-access-insights-workbook"></a>Koşullu erişim öngörüleri çalışma kitabını görüntüle

Azure AD günlüklerinizi Azure Izleyici ile tümleştirdikten sonra, koşullu erişim ilkelerinin etkilerini yeni koşullu erişim öngörüleri çalışma kitaplarını kullanarak izleyebilirsiniz.

1. **Azure Portal** bir güvenlik yöneticisi veya genel yönetici olarak oturum açın.
1. **Azure Active Directory** > **çalışma kitaplarına**gidin.
1. **Koşullu erişim öngörülerini (Önizleme)** seçin.
1. **Koşullu erişim ilkesi** açılan listesinden bir veya daha fazla ilke seçin. Etkinleştirilen tüm ilkeler varsayılan olarak seçilidir.
1. Bir zaman aralığı seçin (zaman aralığı kullanılabilir veri kümesini aşarsa, rapor tüm kullanılabilir verileri gösterir). **Koşullu erişim ilkesini** ve **zaman aralığı** parametrelerini ayarladıktan sonra rapor yüklenir.
   1. İsteğe bağlı olarak, rapor kapsamını daraltmak için bireysel **kullanıcıları** veya **uygulamaları** arayın.
1. Zaman aralığındaki verileri kullanıcı sayısına veya oturum açma sayısına göre görüntüleme arasında seçim yapın.
1. **Veri görünümüne**bağlı olarak, **etki Özeti** seçilen parametrelerin kapsamındaki Kullanıcı veya oturum açma sayısını, toplam sayı, **başarı**, **hata**, **Kullanıcı eylemi gerekli**ve uygulanmamış olarak gruplanmış şekilde görüntüler. Belirli bir sonuç türünün oturum açma işlemlerini incelemek için bir kutucuk seçin. 
   1. Çalışma kitabı parametrelerini değiştirdiyseniz, daha sonra kullanılmak üzere bir kopyasını kaydetmeyi seçebilirsiniz. Raporun üst kısmındaki Kaydet simgesini seçin ve kaydedilecek bir ad ve konum belirtin.
1. Her koşul için oturum açma dökümlerinin dökümünü görüntülemek için aşağı kaydırın.
1. Yukarıdaki seçimlere göre filtrelenmiş tek tek oturum açma olaylarını araştırmak için raporun altındaki **oturum açma ayrıntılarını** görüntüleyin.

> [!TIP] 
> Belirli bir sorguda detaya gitme veya oturum açma ayrıntılarını dışarı aktarma gerekiyor mu? Sorguyu Log Analytics açmak için herhangi bir sorgunun sağındaki düğmeyi seçin. CSV veya Power BI dışarı aktarmak için dışarı aktar ' ı seçin.

## <a name="common-problems-and-solutions"></a>Yaygın sorunlar ve çözümleri

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Çalışma kitabındaki sorgular neden başarısız oluyor?

Müşteriler, çalışma kitabıyla yanlış veya birden çok çalışma alanı ilişkilendirilirse sorguların bazen başarısız olduğunu fark etmiş olur. Bu sorunu gidermek için, çalışma kitabının en üstünde **Düzenle** ' ye ve ardından ayarlar dişli ' a tıklayın. Çalışma kitabıyla ilişkilendirilmemiş çalışma alanlarını seçin ve kaldırın. Her çalışma kitabıyla ilişkili yalnızca bir çalışma alanı olmalıdır.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Koşullu erişim Ilkeleri açılan parametresi neden ilkelerime sahip değil?

Koşullu erişim Ilkeleri açılan menüsü, en son oturum açma işlemlerinin 4 saat boyunca sorgulanarak doldurulur. Bir kiracının son 4 saat içinde oturum açma işlemleri yoksa, açılan menü boş olur. Bu gecikme, seyrek erişimli oturum açma işlemleri içeren küçük kiracılar gibi kalıcı bir sorun ise, Yöneticiler koşullu erişim Ilkeleri açılan listesini düzenleyebilir ve sorgu süresini 4 saatten uzun bir süre uzatır.

## <a name="next-steps"></a>Sonraki adımlar

[Koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md)

Azure AD çalışma kitapları hakkında daha fazla bilgi için [Azure izleyici çalışma kitaplarını Azure Active Directory raporları için kullanma](../reports-monitoring/howto-use-azure-monitor-workbooks.md)makalesine bakın.
