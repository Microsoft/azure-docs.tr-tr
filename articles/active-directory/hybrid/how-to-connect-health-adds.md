---
title: Azure AD Connect Health'i AD DS ile Kullanma| Microsoft Belgeleri
description: Bu Azure AD Connect Health sayfasında AD DS’nin nasıl izleneceği ele alınmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 19e3cf15-f150-46a3-a10c-2990702cd700
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0686a4dafae17bafc65a03c985a65aecba70e60c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98017026"
---
# <a name="using-azure-ad-connect-health-with-ad-ds"></a>Azure AD Connect Health'i AD DS ile Kullanma
Aşağıdaki belgeler Azure AD Connect Health ile Active Directory Etki Alanı Hizmetleri’nin izlenmesine özgüdür. AD DS'nin desteklenen sürümleri şunlardır: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 ve Windows Server 2016.

Azure Connect Health ile AD FS'yi izleme hakkında daha fazla bilgi almak için bkz. [Azure AD Connect Health'i AD FS ile kullanma](how-to-connect-health-adfs.md). Ek olarak, Azure AD Connect’i (Eşitleme) Azure AD Connect Health ile izleme hakkında bilgi için bkz. [Eşitleme için Azure AD Connect Health Kullanma](how-to-connect-health-sync.md).

![AD DS için Azure AD Connect Health](./media/how-to-connect-health-adds/domainservicesnapshot.PNG)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>AD DS için Azure AD Connect Health uyarıları
AD DS için Azure AD Connect Health içindeki Uyarılar bölümünde, etki alanı denetleyicileriniz ile ilgili olarak etkin ve çözümlenmiş uyarıların bir listesi verilir. Etkin veya çözümlenmiş bir uyarının seçilmesi, çözüm adımlarıyla birlikte ek bilgileri ve destekleyici belgelerin bağlantılarını içeren yeni bir dikey pencere açar. Her uyarı türünün, ilgili uyarıdan etkilenen etki alanı denetleyicilerinin her birine karşılık gelen bir veya daha fazla örneği olabilir. Uyarı dikey penceresinin alt kısmında etkilenen bir etki alanı denetleyicisine çift tıklayarak, uyarı örneğine ilişkin daha fazla ayrıntı içeren bir ek dikey pencere açabilirsiniz.

Bu dikey pencerede uyarılar için e-posta bildirimlerini etkinleştirebilir ve görüntülenen zaman aralığını değiştirebilirsiniz. Zaman aralığının genişletilmesi daha önce çözümlenen uyarıları görmenize imkan tanır.

![Azure AD Connect eşitleme hatası](./media/how-to-connect-health-adds/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>Etki Alanı Denetleyicileri Panosu
Bu pano teme çalışma ölçümleri ve izlenen etki alanlarınızın her birinin sistem durumu ile birlikte ortamınızın topolojik bir görünümünü sağlar. Sunulan ölçümler daha fazla araştırma gerektirebilecek herhangi bir etki alanı denetleyicisini hızlıca tanımlamanıza yardımcı olur. Varsayılan olarak, yalnızca bir sütun alt kümesi görüntülenir. Ancak, sütun komutuna çift tıklayarak kullanılabilir sütunların tamamını bulabilirsiniz. En fazla önemsediğiniz sütunların seçilmesi bu panoyu AD DS ortamınızın durumunu görüntülemek için tek ve kolay bir yer haline getirir.

![Etki Alanı Denetleyicileri](./media/how-to-connect-health-adds/aadconnect-health-adds-domainsandsites-dashboard.png)

Etki alanı denetleyicileri ilgili etki alanlarına ya da sitelerine göre gruplandırılabilir; bunun yapılması ortam topolojisinin anlaşılması için yararlıdır. Son olarak, dikey pencere üst bilgisine çift tıklarsanız pano mevcut ekrandan yararlanmak üzere genişletilir. Daha geniş olan bu görünüm birden fazla sütun gösterilirken yararlıdır.

## <a name="replication-status-dashboard"></a>Çoğaltma Durumu Panosu
Bu pano, izlenen etki alanı denetleyicilerinizin çoğaltma durumuna ve çoğaltma topolojisine ilişkin görünümü sağlar. En son çoğaltma girişiminin durumu, bulunan herhangi bir hataya ilişkin yararlı belgelerle birlikte listelenir. Hata içeren bir etki alanı denetleyicisine çift tıklayarak şu bilgileri içeren yeni bir dikey pencere açabilirsiniz: hata ayrıntıları, önerilen çözüm adımları ve sorun giderme belgelerinin bağlantıları.

![Çoğaltma Durumu](./media/how-to-connect-health-adds/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>İzleme
Bu özellik, izlenen her bir etki alanından sürekli olarak toplanan farklı performans sayaçlarının grafik eğilimlerini verir. Bir etki alanı denetleyicisinin performansı, ormanınızdaki diğer tüm izlenen etki alanı denetleyicileri arasında kolayca karşılaştırılabilir. Ayrıca, ortamınızdaki sorunları gidermede yardımcı olmak üzere çeşitli performans sayaçlarını yan yana görebilirsiniz.

![İzleme](./media/how-to-connect-health-adds/aadconnect-health-adds-monitoring.png)

Varsayılan olarak, dört performans sayacı önceden seçilmiştir; ancak, filtre komutuna tıklayarak ve istediğiniz performans sayaçlarını seçerek ya da seçimini kaldırarak başkalarını ekleyebilirsiniz. Ayrıca, bir performans sayacı grafiğine çift tıklayarak, izlenen etki alanı denetleyicilerinin her birine ait veri noktalarını içeren yeni bir dikey pencere açabilirsiniz.

## <a name="related-links"></a>İlgili bağlantılar
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Aracı yüklemesini Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health İşlemleri](how-to-connect-health-operations.md)
* [Azure AD Connect Health'i AD FS ile Kullanma](how-to-connect-health-adfs.md)
* [Eşitleme için Azure AD Connect Health kullanma](how-to-connect-health-sync.md)
* [Azure AD Connect Health ile ilgili SSS](reference-connect-health-faq.md)
* [Azure AD Connect Health sürümü geçmişi](reference-connect-health-version-history.md)