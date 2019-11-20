---
title: Kullanım ve Öngörüler raporu | Microsoft Docs
description: Azure Active Directory portalındaki Kullanım ve Öngörüler raporuna giriş
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b3db86137207ae726c7befc393f62590fd1456d7
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008259"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Azure Active Directory portalındaki Kullanım ve Öngörüler raporu

Kullanım ve Öngörüler raporuyla, oturum açma verilerinizin uygulama odaklı bir görünümünü elde edebilirsiniz. Aşağıdaki sorulara cevaplar bulabilirsiniz:

*   Kuruluşumdaki en iyi kullanılan uygulamalar nelerdir?
*   En fazla oturum açma işlemi başarısız olan uygulamalar nelerdir? 
*   Her uygulama için en iyi oturum açma hataları nelerdir?

## <a name="prerequisites"></a>Önkoşullar 

Kullanım ve Öngörüler raporundan verilere erişmek için şunlar gerekir:

* Bir Azure AD kiracısı
* Oturum açma verilerini görüntülemek için bir Azure AD Premium (P1/P2) lisansı
* Genel yönetici, Güvenlik Yöneticisi, güvenlik okuyucusu veya rapor okuyucu rollerinde bir kullanıcı. Ayrıca, herhangi bir Kullanıcı (yönetici olmayanlar) kendi oturum açma işlemleri için de erişebilir. 

## <a name="access-the-usage-and-insights-report"></a>Kullanım ve Öngörüler raporuna erişin

1. [Azure portalına](https://portal.azure.com) gidin.
2. Doğru dizini seçin ve **Azure Active Directory** seçin ve **Kurumsal uygulamalar**' ı seçin.
3. **Etkinlik** bölümünde, raporu açmak için **kullanım & Öngörüler** ' i seçin. 

![Kullanım ve İçgörüler raporu](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Raporu kullanma

Kullanım ve Öngörüler raporu, bir veya daha fazla oturum açma girişiminde bulunan uygulamaların listesini gösterir ve başarılı oturum açma sayısı, başarısız oturum açma işlemleri ve başarı oranı ile sıralama yapmanıza olanak sağlar.

Listenin en altında bulunan Yükle ' ye tıklamak sayfada ek uygulamalar görüntülemenize olanak sağlar. Aralık içinde kullanılmış olan tüm uygulamaları görüntülemek için tarih aralığını seçebilirsiniz.

Odağı belirli bir uygulama için de ayarlayabilirsiniz. Uygulamanın zaman içindeki oturum açma etkinliğini ve en üstteki hataları görmek için **oturum açma etkinliğini görüntüle** ' yi seçin.  

Uygulama kullanımı grafiğinde bir gün seçtiğinizde, uygulama için oturum açma etkinliklerinin ayrıntılı bir listesini alırsınız.  

![Kullanım ve İçgörüler raporu](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Oturum açma işlemleri raporu](concept-sign-ins.md)