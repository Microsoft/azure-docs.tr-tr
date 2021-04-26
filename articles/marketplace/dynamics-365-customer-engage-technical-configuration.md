---
title: Dynamics 365 for Customer Engagement for the PowerApps & Microsoft AppSource (Azure Marketi) üzerinde teknik yapılandırma sunma
description: "& PowerApps için Dynamics 365 ' i, Microsoft AppSource (Azure Marketi) üzerinde teknik yapılandırma sunmaktadır."
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.custom: references_regions
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: da5b6ffd420c2c51e04d3a194ad295089e811db4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820471"
---
# <a name="set-up-dynamics-365-for-customer-engagement--power-apps-offer-technical-configuration"></a>Müşteri katılımı için Dynamics 365 'yi ayarlama & Power Apps Teknik yapılandırma sunar

Bu sayfa, teklifiniz ile bağlantı kurmak için kullanılan teknik ayrıntıları tanımlar. Bu bağlantı, teklifi almayı tercih ettiklerinde, son müşteri için teklifinizi sağlamamızı sağlar.

## <a name="offer-information"></a>Teklif bilgileri

**Temel lisans modeli** , müşterilerin UYGULAMANıZ Için CRM Yönetim merkezinde nasıl atandığını belirler. Lisanslara kiracı başına bir tane atanırsa, örnek tabanlı lisanslama veya **Kullanıcı** için **kaynak** seçin.

**S2S giden ve CRM güvenli depo erişimi gerektirir** onay kutusu, CRM güvenli depolama veya sunucu-sunucu (S2S) giden erişiminin yapılandırılmasını sağlar. Bu özellik, sertifika aşamasında Dynamics 365 ekibinin özelleşmiş bir şekilde dikkate alınması gerekir. Microsoft, bu özelliği desteklemeye yönelik ek adımları tamamlayabilmeniz için sizinle iletişim kuracaktır.

**Uygulama yapılandırma URL 'sini** boş bırakın; gelecekte kullanılmak üzere.

## <a name="crm-package"></a>CRM paketi

**Paket konumlarınızın URL 'si** için KARŞıYA yüklenen CRM Package. zip dosyasını Içeren bir Azure Blob depolama hesabının URL 'sini girin. Microsoft 'un, doğrulamaya yönelik paketinizi görebilmesi için, URL 'ye bir salt okunurdur SAS anahtarı ekleyin.

> [!IMPORTANT]
> Bir yayımlama bloğundan kaçınmak için, blob depolamanın URL 'sindeki sona erme tarihinin süresinin dolmadığından emin olun. İlkenize erişerek tarihi gözden geçirebilir. **Süre sonu zamanının** gelecekte en az bir ay olması önerilir.

Uygunsa, **paket dosyası içinde birden çok CRM paketi var kutusunda '** ı seçin. Bu durumda, tüm paketleri. zip dosyanıza eklediğinizden emin olun.

Paketinizi oluşturma ve yapısını güncelleştirme hakkında ayrıntılı bilgi için bkz. [3. Adım: uygulamanız Için AppSource paketi oluşturma](/powerapps/developer/common-data-service/create-package-app-appsource).

## <a name="crm-package-availability"></a>CRM paketi kullanılabilirliği

CRM paketinizin müşteriler tarafından kullanılabileceği coğrafi bölgeleri belirtmek için **+ bölge Ekle** ' yi seçin. Aşağıdaki bağımsız bölgelerine dağıtım, sertifika işlemi sırasında özel izin ve doğrulama gerektirir: [Almanya](../germany/index.yml), [ABD kamu bulutu](../azure-government/documentation-government-welcome.md)ve ipucu.

Varsayılan olarak, yukarıda girdiğiniz **uygulama yapılandırma URL 'si** her bölge için kullanılacaktır. İsterseniz, bir veya daha fazla belirli bölge için ayrı bir uygulama yapılandırma URL 'SI girebilirsiniz.

Sol gezinti menüsündeki bir sonraki sekmeye devam etmeden önce **Taslağı kaydet** ' i seçin, **Microsoft ile ortak satış** yapın. Ortak satışı Microsoft ile ayarlama hakkında daha fazla bilgi için (isteğe bağlı) bkz. [ortak satış iş ortağı katılımı](marketplace-co-sell.md). Ortak satışı ayarlamadıysanız veya işiniz bittiğinde aşağıdaki **adımlarla** devam edin.

## <a name="next-steps"></a>Sonraki adımlar

- [Ek içerik yapılandırma](dynamics-365-customer-engage-supplemental-content.md)
