---
title: NXLog LinuxAudit verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: NXLog LinuxAudit veri bağlayıcısını kullanarak LinuxAudit günlüklerini Azure Sentinel 'e çekme hakkında bilgi edinin. Çalışma kitaplarında LinuxAudit verilerini görüntüleyin, uyarılar oluşturun ve araştırmayı geliştirebilirsiniz.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 2010b21a3cdb81f2b2aa4180f87857862cd02bf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700937"
---
# <a name="connect-your-nxlog-linuxaudit-to-azure-sentinel"></a>NXLog LinuxAudit dosyanızı Azure Sentinel 'e bağlama

> [!IMPORTANT]
> NXLog LinuxAudit Bağlayıcısı Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

[Nxlog LinuxAudit](https://nxlog.co/documentation/nxlog-user-guide/im_linuxaudit.html) Bağlayıcısı, Linux güvenlik olaylarını gerçek zamanlı olarak Azure Sentinel 'e kolayca vermenize olanak tanıyarak, kuruluşunuzun tamamında etki alanı adı sunucu etkinliğine ilişkin bir fikir verir. NXLog LinuxAudit modülü, özel denetim kurallarını destekler ve sessiz veya başka bir kullanıcı alanı yazılımı olmadan günlükleri toplar. IP adresleri ve grup/kullanıcı kimlikleri, [Linux denetim](https://nxlog.co/documentation/nxlog-user-guide/linux-audit.html) günlüklerini güvenlik analistlerine daha okunaklı hale getiren ilgili adlarına çözülür. NXLog LinuxAudit ve Azure Sentinel arasındaki tümleştirme REST API aracılığıyla kolaylaştıralınmıştır.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="configure-and-connect-nxlog-linuxaudit"></a>NXLog LinuxAudit öğesini yapılandırma ve bağlama

NXLog, olayları JSON biçiminde doğrudan Azure Sentinel 'e gönderecek şekilde yapılandırılabilir.

1. Azure Sentinel portalında, **veri bağlayıcıları** ' na tıklayın ve **Nxlog linuxaudit** bağlayıcısını seçin.

1. **Bağlayıcı sayfasını aç**' ı seçin.

1. REST API aracılığıyla yönlendirmeyi yapılandırmak için *Nxlog Kullanıcı Kılavuzu* tümleştirme konusundaki adım adım yönergeleri Izleyin [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) .

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler *LinuxAudit_CL* tablosundaki **özel Günlükler** bölümünün altındaki **günlüklerde** görüntülenir.

## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Günlüklerinizin Log Analytics görünmeye başlaması 20 dakikaya kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Azure Sentinel 'e Linux güvenlik olaylarını almak için NXLog LinuxAudit kullanmayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .
