---
title: Raporlar için Azure Izleyici çalışma kitapları | Microsoft Docs
description: Azure Active Directory raporları için Azure Izleyici çalışma kitaplarını nasıl kullanacağınızı öğrenin.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2e94d9f56a865999f9169650f621a6af892c27ae
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014370"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Azure Active Directory raporları için Azure Izleyici çalışma kitaplarını kullanma

> [!IMPORTANT]
> Bu çalışma kitabındaki temeldeki sorguları iyileştirmek için lütfen "Düzenle" ye tıklayın, ayarlar simgesine tıklayın ve bu sorguları çalıştırmak istediğiniz çalışma alanını seçin. Çalışma kitapları varsayılan olarak, Azure AD günlüklerinizi yönlendirçalıştığınız tüm çalışma alanlarını seçer. 

Şunları yapmak istiyor musunuz:

- [Koşullu erişim ilkelerinizin](../conditional-access/overview.md) Kullanıcı oturum açma deneyiminizde etkisini anlayın.

- Kuruluşunuzun oturum açma durumu hakkında daha iyi bir görünüm almak ve sorunları hızlıca çözmek için oturum açma hataları sorunlarını giderin.

- Ortamınızda oturum açmak için kimin eski kimlik doğrulamaları kullandığını öğrenin. ( [Eski kimlik doğrulamasını engelleyerek](../conditional-access/block-legacy-authentication.md)kiracınızın korumasını geliştirebilirsiniz.)

- Kiracınızdaki koşullu erişim ilkelerinin etkisini anlamanız gerekiyor mu?

- ' Nin gözden geçirilmesini sağlamak istiyor musunuz: oturum açma günlüğü sorguları, çalışma kitabı, erişim izni verilen veya reddedilen kullanıcı sayısı ve kaynaklara erişirken kaç kullanıcının koşullu erişim ilkelerini atladığını bildiriyor mu?

- Daha ayrıntılı bir şekilde anlaşılmakla ilgileniyor: bir ilkenin etkisinin cihaz platformu, cihaz durumu, istemci uygulaması, oturum açma riski, konum ve uygulama dahil olmak üzere koşula göre ayarlanabilir olması için her koşulda çalışma kitabı ayrıntıları.

- Oturum açma günlüğü sorguları hakkında daha derin Öngörüler elde edin. çalışma kitabı, kaç kullanıcının erişim izni verildiğini veya reddedildiğini, Ayrıca kaynaklara erişirken kaç kullanıcının koşullu erişim ilkelerini atladığını bildirir.

- Bu soruları ele almanıza yardımcı olmak Active Directory, izleme için çalışma kitapları sağlar. [Azure izleyici çalışma kitapları](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) metin, analiz sorguları, ölçümler ve parametreleri zengin etkileşimli raporlara birleştirir.



Bu makalede:

- [İzleme çalışma kitaplarını kullanarak etkileşimli raporlar oluşturma](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)hakkında bilgi sahibi olduğunuz varsayılır.

- Koşullu erişim ilkelerinizin etkisini anlamak, oturum açma hatalarıyla ilgili sorunları gidermek ve eski kimlik doğrulamaları belirlemek için çalışma kitaplarının nasıl kullanılacağını açıklar.
 


## <a name="prerequisites"></a>Önkoşullar

Izleme çalışma kitaplarını kullanmak için şunlar gerekir:

- Premium (P1 veya P2) lisansıyla bir Active Directory kiracısı. [Premium lisans alma](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)hakkında bilgi edinin.

- [Log Analytics çalışma alanı](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- Log Analytics çalışma alanına [erişim](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions)
- Azure Active Directory ' de aşağıdaki roller (Azure Active Directory Portal üzerinden Log Analytics erişiyorsanız)
    - Güvenlik yöneticisi
    - Güvenlik okuyucusu
    - Rapor okuyucu
    - Genel yönetici

## <a name="roles"></a>Roller
Çalışma kitaplarını yönetmek için aşağıdaki rollerden birinde ve [temel alınan Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) çalışma alanına erişiminizin olması gerekir:
-   Genel yönetici
-   Güvenlik yöneticisi
-   Güvenlik okuyucusu
-   Rapor okuyucu
-   Uygulama Yöneticisi

## <a name="workbook-access"></a>Çalışma kitabı erişimi 

Çalışma kitaplarına erişmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. **Azure Active Directory** > **Izleme** > **çalışma kitaplarına**gidin. 

1. Bir rapor veya şablon seçin ya da araç çubuğunda **Aç**' ı seçin. 

![Azure AD 'de Azure Izleyici çalışma kitaplarını bulma](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Oturum açma Analizi

Oturum açma çözümleme çalışma kitabına erişmek için **kullanım** bölümünde **oturum açma**işlemleri ' ni seçin. 

Bu çalışma kitabı aşağıdaki oturum açma eğilimlerini gösterir:

- Tüm oturum açma işlemleri

- Başarılı

- Bekleyen Kullanıcı eylemi

- Hata

Her eğilimi aşağıdaki kategorilere göre filtreleyebilirsiniz:

- Zaman aralığı

- Uygulamalar

- Kullanıcılar

![Oturum açma Analizi](./media/howto-use-azure-monitor-workbooks/43.png)


Her eğilim için, aşağıdaki kategorilere göre bir döküm alırsınız:

- Konum

    ![Konuma göre oturum açma işlemleri](./media/howto-use-azure-monitor-workbooks/45.png)

- Cihaz

    ![Cihaza göre oturum açma işlemleri](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Eski kimlik doğrulaması kullanan oturum açma işlemleri 


[Eski kimlik doğrulaması](../conditional-access/block-legacy-authentication.md)kullanan oturum açma işlemleri için çalışma kitabına erişmek üzere, **kullanım** bölümünde **eski kimlik doğrulamasını kullanarak oturum açma**işlemlerini seçin. 

Bu çalışma kitabı aşağıdaki oturum açma eğilimlerini gösterir:

- Tüm oturum açma işlemleri

- Başarılı


Her eğilimi aşağıdaki kategorilere göre filtreleyebilirsiniz:

- Zaman aralığı

- Uygulamalar

- Kullanıcılar

- Protokoller

![Eski kimlik doğrulamasına göre oturum açma işlemleri](./media/howto-use-azure-monitor-workbooks/47.png)


Her eğilim için, uygulama ve protokole göre bir döküm alırsınız.

![Uygulama ve protokole göre eski-kimlik doğrulama oturum açma işlemleri](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Koşullu erişime göre oturum açma işlemleri 


Koşullu erişim [ilkelerine](../conditional-access/overview.md)göre oturum açma işlemleri için çalışma kitabına erişmek **üzere koşullu erişim bölümünde,** **koşullu erişime göre oturum açma**işlemlerini seçin. 

Bu çalışma kitabı, devre dışı bırakılan oturum açma eğilimlerini gösterir. Her eğilimi aşağıdaki kategorilere göre filtreleyebilirsiniz:

- Zaman aralığı

- Uygulamalar

- Kullanıcılar

![Koşullu Erişimle Oturum Açma İşlemleri](./media/howto-use-azure-monitor-workbooks/49.png)


Devre dışı bırakılan oturum açma işlemleri için, koşullu erişim durumuna göre bir döküm alırsınız.

![Koşullu erişim durumu](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Koşullu Erişim İçgörüleri

### <a name="overview"></a>Genel Bakış

Çalışma kitapları, BT yöneticilerinin kiracılarındaki koşullu erişim ilkelerinin etkilerini izlemelerine yardımcı olabilecek oturum açma günlüğü sorgularını içerir. Erişim izni verilen veya reddedilen kullanıcı sayısını rapor etme olanağınız vardır. Çalışma kitabı, oturum açma sırasında bu kullanıcıların özniteliklerine göre koşullu erişim ilkelerinin kaç Kullanıcı tarafından atlandığını açıklamaktadır. İlke, cihaz platformu, cihaz durumu, istemci uygulaması, oturum açma riski, konum ve uygulama dahil olmak üzere her koşulda ayarlanabilir hale gelecek şekilde durum ayrıntılarını içerir.

### <a name="instructions"></a>Yönergeler 
Koşullu erişim öngörüleri için çalışma kitabına erişmek üzere koşullu erişim bölümünde **koşullu erişim öngörüleri** çalışma kitabını seçin. Bu çalışma kitabı, kiracınızdaki her bir koşullu erişim ilkesinin beklenen etkisini gösterir. Aşağıdaki filtreleri uygulayarak, açılan listeden bir veya daha fazla koşullu erişim ilkesi seçin ve çalışma kitabının kapsamını daraltın: 

- **Zaman aralığı**

- **Kullanıcısını**

- **Gör**

- **Veri görünümü**

![Koşullu erişim durumu](./media/howto-use-azure-monitor-workbooks/access-insights.png)


Etki Özeti, seçilen ilkelerin belirli bir sonuç aldığı Kullanıcı veya oturum açma sayısını gösterir. Toplam, seçilen ilkelerin seçili zaman aralığında değerlendirildiği Kullanıcı veya oturum açma işlemlerinin sayısıdır. Çalışma kitabındaki verileri ilgili sonuç türü ile filtrelemek için bir kutucuğa tıklayın. 

![Koşullu erişim durumu](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

Bu çalışma kitabı, seçilen ilkelerin altı koşuldan her biri tarafından ayrılmış etkisini de gösterir: 
- **Cihaz durumu**
- **Cihaz platformu**
- **İstemci uygulamaları**
- **Oturum açma riski**
- **Konum**
- **Uygulamalar**

![Koşullu erişim durumu](./media/howto-use-azure-monitor-workbooks/device-platform.png)

Ayrıca, çalışma kitabında seçilen parametrelere göre filtreleyerek tek tek oturum açma işlemlerini de inceleyebilirsiniz. Bireysel kullanıcıları arayın, oturum açma sıklığına göre sıralanmış ve ilgili oturum açma olaylarını görüntüleyin. 

![Koşullu erişim durumu](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>Atama denetimlerine göre oturum açma işlemleri

Çalışma kitabına [denetimler](../conditional-access/controls.md)vererek oturum açma işlemleri için erişmek üzere, **koşullu erişim** bölümünde, **Denetim izni vererek oturum açma**işlemlerini seçin. 

Bu çalışma kitabı, aşağıdaki devre dışı oturum açma eğilimlerini gösterir:

- MFA gerektirme
 
- Kullanım koşullarını gerekli kılma

- Gizlilik bildirimi iste

- Diğer


Her eğilimi aşağıdaki kategorilere göre filtreleyebilirsiniz:

- Zaman aralığı

- Uygulamalar

- Kullanıcılar

![Atama denetimlerine göre oturum açma işlemleri](./media/howto-use-azure-monitor-workbooks/50.png)


Her eğilim için, uygulama ve protokole göre bir döküm alırsınız.

![Son oturum açma işlemlerinin dökümünü yapın](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Oturum açma hatası Analizi

Hataları gidermek için **oturum açma hatası analiz** çalışma kitabını kullanın:

- Oturum açma işlemleri
- Koşullu erişim ilkeleri
- Eski kimlik doğrulaması 


Koşullu erişim verilerine göre oturum açma işlemleri için, **sorun giderme** bölümünde, **eski kimlik doğrulamasını kullanarak oturum açma**işlemlerini seçin. 

Bu çalışma kitabı aşağıdaki oturum açma eğilimlerini gösterir:

- Tüm oturum açma işlemleri

- Başarılı

- Bekleyen eylem

- Hata


Her eğilimi aşağıdaki kategorilere göre filtreleyebilirsiniz:

- Zaman aralığı

- Uygulamalar

- Kullanıcılar

![Oturum açma sorunlarını giderme](./media/howto-use-azure-monitor-workbooks/52.png)


Azure Izleyici, oturum açma sorunlarını gidermenize yardımcı olmak için aşağıdaki kategorilere göre bir döküm sağlar:

- En önemli hatalar

    ![En üstteki hataların Özeti](./media/howto-use-azure-monitor-workbooks/53.png)

- Kullanıcı eylemini bekleyen oturum açma işlemleri

    ![Kullanıcı eylemi için bekleyen oturum açma işlemlerinin Özeti](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Sonraki adımlar

[İzleme çalışma kitaplarını kullanarak etkileşimli raporlar oluşturun](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
