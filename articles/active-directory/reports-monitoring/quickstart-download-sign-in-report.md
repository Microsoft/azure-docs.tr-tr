---
title: Hızlı Başlangıç Azure portalı kullanarak bir oturum açma raporunu indirme | Microsoft Docs
description: Azure portalı kullanarak bir oturum açma raporunun nasıl indirileceğini öğrenme
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
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
ms.openlocfilehash: 7cbd71696c3508a464b1343d552cba4a3391066f
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989667"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Hızlı Başlangıç: Azure portal kullanarak bir oturum açma raporunu indirin

Bu hızlı başlangıçta, son 24 saat boyunca kiracınız için oturum açma verilerinin nasıl indirileceğini öğreneceksiniz. Azure portal en fazla 250.000 kaydı indirebilirsiniz. Kayıtlar en güncel olarak sıralanmıştır, varsayılan olarak en son 250.000 kayıtları alırsınız. 

## <a name="prerequisites"></a>Önkoşullar

Gerekenler:

* Oturum açma etkinlik raporunu görüntülemek için Premium lisansına sahip Azure Active Directory kiracısı. Azure Active Directory sürümünüzü yükseltmek için bkz. [Azure Active Directory Premium kullanmaya](../fundamentals/active-directory-get-started-premium.md) başlama. Yükseltmeden önce herhangi bir etkinlik veriniz yoksa, Premium bir lisansa yükselttikten sonra verilerin raporlarda gösterilmesi birkaç gün sürecek şekilde değişir.
* Kiracı için **Güvenlik Yöneticisi**, **güvenlik okuyucu**, **rapor okuyucu** veya **genel yönetici** rolünde olan bir kullanıcı. Ayrıca, kiracıdaki tüm kullanıcılar kendi oturum açma işlemlerine erişebilir.

## <a name="quickstart-download-a-sign-in-report"></a>Hızlı Başlangıç: Oturum açma raporunu indirme

1. [Azure portalına](https://portal.azure.com) gidin.
2. Sol gezinti bölmesinden **Azure Active Directory**’yi seçin ve **Dizini değiştir** düğmesini kullanarak etkin dizininizi seçin.
3. Panodan **Azure Active Directory**’yi ve sonra **Oturum açma işlemleri**’ni seçin. 
4. **Tarih** filtre açılır listesinden **son 24 saat**’i seçin ve sonra **Uygula**’yı seçerek son 24 saatteki oturum açma işlemlerini görüntüleyin. 
5. **İndir** düğmesini seçin, dosya biçimi olarak **CSV** ' yi seçin ve FILTRELENMIŞ kayıtları içeren bir CSV dosyasını indirmek için bir dosya adı belirtin. 

![Raporlanıyor](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Active Directory portaldaki oturum açma etkinlik raporları](concept-sign-ins.md)
* [Azure Active Directory raporlama elde tutma](reference-reports-data-retention.md)
* [Azure Active Directory raporlama gecikmeleri](reference-reports-latencies.md)
