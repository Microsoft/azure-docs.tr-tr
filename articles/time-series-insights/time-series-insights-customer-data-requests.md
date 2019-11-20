---
title: Müşteri verileri istek özellikleri-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights 'deki müşteri verileri isteği özellikleri hakkında bilgi edinin.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: d134c7cbe220e6aa6b1471b50aa23e17d09734d5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007019"
---
# <a name="summary-of-customer-data-request-features"></a>Müşteri verilerini talep özelliklerin özeti

Azure Time Series Insights, depolama, analiz ve görselleştirme bileşenleri olan, milyarlarca olayı eşzamanlı olarak alma, depolama, araştırma ve analiz etmeyi kolaylaştıran, yönetilen bir bulut hizmetidir.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Bir veri konusu isteğine tabi olabilecek kişisel verileri görüntülemek, dışarı aktarmak ve silmek için Azure Time Series Insights kiracı yöneticisi Azure portal veya REST API 'Lerini kullanabilir. Veri sahibinin isteklerine hizmet vermek için Azure portal kullanarak, çoğu kullanıcının tercih ettiği bu işlemleri gerçekleştirmek için daha az karmaşık bir yöntem sağlar.

## <a name="identifying-customer-data"></a>Müşteri verilerini tanımlama

Azure Time Series Insights, kişisel verileri yöneticilerle ve Time Series Insights kullanıcılarla ilişkili veriler olacak şekilde değerlendirir. Time Series Insights, ortama erişimi olan kullanıcıların Azure Active Directory nesne KIMLIĞINI depolar. Azure portal Kullanıcı e-posta adreslerini görüntüler, ancak bu e-posta adresleri Time Series Insights içinde depolanmaz, bu adresler Azure Active Directory içinde Azure Active Directory nesne KIMLIĞI kullanılarak dinamik olarak aranır.

## <a name="deleting-customer-data"></a>Müşteri verileri silme

Bir kiracı yöneticisi Azure portal kullanarak müşteri verilerini silebilir.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Ancak, Portal üzerinden müşteri verilerini silmeden önce, kullanıcının erişim ilkelerini Azure portal içindeki Time Series Insights ortamından kaldırmanız gerekir. Daha fazla bilgi için bkz. [Azure Portal kullanarak Time Series Insights ortamına veri erişimi verme](time-series-insights-data-access.md).

Ayrıca, REST API kullanarak erişim ilkelerinde silme işlemleri gerçekleştirebilirsiniz. Daha fazla bilgi için bkz. [erişim ilkeleri-Sil](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/delete).

Time Series Insights, Azure portal Ilke dikey penceresinde tümleşiktir. Hem Time Series Insights hem de Ilke dikey penceresi, hizmet içinde depolanan kullanıcı verilerini görüntülemenize, dışa aktarmaya ve silmenizi sağlar. Azure portal Ilke dikey penceresinde gerçekleştirilen silme işlemleri, Time Series Insights içindeki kullanıcı verilerinin silinmesine neden olur. Örneğin, bir kullanıcının kaydedilmiş bir kişisel sorgusu varsa, bu sorgu Time Series Insights Gezgini ' nden kalıcı olarak silinir. Kullanıcının kaydedilmiş bir paylaşılan sorgusu varsa sorgu devam ederse, ancak kullanıcı bilgileri kalıcı olarak silinir. Aşağıdaki notta, bu görevlerin nasıl yerine getirileceğini gösteren yönergeler yer almaktadır.

## <a name="exporting-customer-data"></a>Müşteri verilerini dışarı aktarma

Benzer şekilde, verileri silmenin yanı sıra, bir kiracı yöneticisi Azure portal Ilke dikey penceresinden Time Series Insights depolanan verileri görüntüleyebilir ve dışarı aktarabilir.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Kiracı yöneticisiyseniz, veri erişim ilkelerini Azure portal Time Series Insights ortamı içinde görüntüleyebilirsiniz. Daha fazla bilgi için bkz. [Azure Portal kullanarak Time Series Insights ortamına veri erişimi verme](time-series-insights-data-access.md).

Ayrıca, girilen REST API "ortama göre listele" işlemi kullanılarak erişim ilkelerinde dışarı aktarma işlemleri gerçekleştirmek mümkündür. Daha fazla bilgi için bkz. [erişim ilkeleri-ortama göre liste](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Time Series Insights içinde depolanan verileri silmek için

Kişisel veriler, Kullanıcı ve yönetici verilerinden farklı bir senaryo olan Time Series Insights depolama alanına yol açabilir. Time Series Insights ' de depolanan verileri kişisel veri olarak kabul ediyorsanız, aşağıdaki adımları kullanarak bu verileri dışarı aktarabilir ve silebilirsiniz:

**Verileri görüntüleme ve dışarı aktarma**

Time Series Insights içinde depolanan verileri görüntülemek ve dışarı aktarmak için, bu verileri aramanız gerekir. Verileri görüntülemek ve dışarı aktarmak için Time Series Insights Gezginini veya Time Series Insights sorgu API 'Lerini kullanabilirsiniz. Time Series Insights gezginini kullanarak verileri görüntülemek ve dışarı aktarmak için ilk olarak, söz konusu Kullanıcı verilerini bulmak için arama yapın. Aramadan sonra grafiğe sağ tıklayın ve **olayları keşfet**' i seçin. Olaylar Kılavuzu görünür ve verileri CSV ve JSON olarak dışa aktarma seçeneklerini sunar.

Daha fazla bilgi için bkz. [Azure Time Series Insights Explorer](time-series-insights-explorer.md).

**Verileri Sil**

Şu anda Time Series Insights, verilerin ayrıntılı silinmesini desteklemez. Ancak Time Series Insights, bekletme ilkelerini yapılandırarak Time Series Insights içinde depolanan müşteri verilerini kaldırma olanağı sağlar. Tüm Time Series Insights ortamının saklama süresini, silme gereksinimlerinizi desteklemek için istediğiniz sayıda güne ayarlayabilirsiniz.

Daha fazla bilgi için bkz. [Time Series Insights bekletme yapılandırma](time-series-insights-how-to-configure-retention.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Time Series Insights ortamınıza veri erişimi verme](./time-series-insights-data-access.md)hakkında daha fazla bilgi edinin.

* [Azure Time Series Insights Gezginini](time-series-insights-explorer.md)görüntüleyin.

* [Time Series Insights ' de bekletme yapılandırma](time-series-insights-how-to-configure-retention.md)hakkında bilgi edinin.