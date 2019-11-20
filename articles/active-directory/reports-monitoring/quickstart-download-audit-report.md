---
title: Hızlı Başlangıç Azure portalı kullanarak denetim raporu indirme | Microsoft Docs
description: Azure portalı kullanarak bir denetim raporunun nasıl indirileceğini öğrenme
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f3e5dd1c42537ce6ff419d7d81d69d824242ec4
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989686"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Hızlı Başlangıç: Azure portal kullanarak bir denetim raporu indirin

Bu hızlı başlangıçta, kiracınızın son 24 saati için denetim günlüklerinin CSV dosyasını nasıl indirecek hakkında bilgi edineceksiniz. Azure portal en fazla 250.000 kaydı indirebilirsiniz. Kayıtlar en güncel olarak sıralanmıştır, varsayılan olarak en son 250.000 kayıtları alırsınız. 

## <a name="prerequisites"></a>Önkoşullar

Gerekenler:

* Bir Azure Active Directory kiracısı. 
* Kiracı için **Güvenlik Yöneticisi**, **güvenlik okuyucusu**veya **genel yönetici** rolünde olan bir kullanıcı. Ayrıca, kiracıdaki tüm kullanıcılar kendi denetim günlüklerine erişebilir.

## <a name="quickstart-download-an-audit-report"></a>Hızlı Başlangıç: Denetim raporunu indirme

1. [Azure portalına](https://portal.azure.com) gidin.
2. Sol gezinti bölmesinden **Azure Active Directory**’yi seçin ve **Dizini değiştir** düğmesini kullanarak etkin dizininizi seçin.
3. Panodan **Azure Active Directory**’yi ve sonra **Denetim günlükleri**’ni seçin. 
4. **Tarih aralığı** filtre açılır listesinden **son 24 saat**’i seçin ve sonra **Uygula**’yı seçerek son 24 saatteki denetim günlüklerini görüntüleyin. 
5. **İndir** düğmesini seçin, dosya biçimi olarak **CSV** ' yi seçin ve FILTRELENMIŞ kayıtları içeren bir CSV dosyasını indirmek için bir dosya adı belirtin. 

![Raporlanıyor](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Active Directory portaldaki oturum açma etkinlik raporları](concept-sign-ins.md)
* [Azure Active Directory raporlama elde tutma](reference-reports-data-retention.md)
* [Azure Active Directory raporlama gecikmeleri](reference-reports-latencies.md)
