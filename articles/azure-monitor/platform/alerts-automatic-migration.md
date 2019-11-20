---
title: Azure Monitor klasik uyarılarınız için otomatik geçiş işleminin nasıl çalıştığını anlayın
description: Otomatik geçiş işleminin nasıl çalıştığını öğrenin.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: e0ac349554fa580c4ac88b26e76d0bea1ecf738b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932705"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Klasik uyarı kurallarınız için otomatik geçiş işlemini anlayın

[Daha önce duyurulduğu](monitoring-classic-retirement.md)gibi, Azure izleyici 'deki klasik uyarılar Eylül 2019 ' de kullanımdan kaldırılıyor (ilk olarak 2019 Temmuz idi). Kullanımdan kaldırma işleminin bir parçası olarak, müşterilerin geçişi tetikleyebilmesi için Azure portal [bir geçiş aracı](alerts-using-migration-tool.md) vardır. Geçiş Aracı 'nı 31 Ağustos 2019 ' e kadar kullanmadıysanız Azure Izleyici, 1 Eylül 2019 ' den itibaren klasik uyarılarınızı otomatik geçirme işlemini başlatacak.
Bu makale, otomatik geçiş sürecinde size yol gösterir ve karşılaşabileceğiniz tüm sorunları çözmenize yardımcı olur.

  > [!NOTE]
  > Bu makale yalnızca Azure genel bulutu için geçerlidir. Azure için kullanımdan kaldırma işlemi Azure Kamu bulutundaki klasik uyarılar ve Azure Çin 21Vianet, gelecekteki tarihte duyurulacaktır.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>Otomatik geçiş işlemi sırasında ne olur?

- Müşteriler, **1 eylül 2019**' den itibaren [belirli ölçümler](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)dışında yeni bir klasik uyarı kuralı oluşturamayacak.
  - Müşteri özel durumlar için, yeni klasik uyarı kuralları oluşturmaya ve 2020 Haziran tarihine kadar klasik uyarılarını kullanmaya devam edebilir.
- Klasik uyarıların geçirilmesi, **1 eylül 2019**' den itibaren klasik uyarılara sahip müşteriler için toplu olarak tetiklenecektir.
- Gönüllü geçiş aracında olduğu gibi, geçirilip geçirilemeyeceğini denetleyin olmayan bazı klasik uyarı kuralları oldukları gibi kalır. Bu klasik uyarı kuralları, Haziran 2020 ' e kadar desteklenmeye devam edecektir. Ancak, tüm geçersiz klasik uyarı kuralları işlevsel olmadıkları için silinir.
Silinen hedef kaynakları izleyen ve [artık desteklenmeyen ölçümlerde](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) bulunan tüm klasik uyarı kuralları geçersiz olarak kabul edilir.
- Aboneliğiniz için geçiş başladıktan sonra herhangi bir sorun olmadıkça, geçiş bir saat içinde tamamlanmalıdır. Müşteriler, [Azure izleyici 'deki geçiş dikey](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)penceresinde geçişin durumunu izleyebilir.
- Abonelik sahipleri, geçişin başarıyla tamamlandığını bir e-posta alacak.
- Geçiş sırasında herhangi bir sorun varsa, abonelik sahipleri bunlara aynı şekilde bir e-posta gönderilir. Müşteriler, sorunun tüm ayrıntılarını görmek için geçiş dikey penceresini kullanabilir.
- Müşterinin bir kaynak kilidini geçici olarak devre dışı bırakma veya bir ilke atamasını değiştirme gibi bir eyleme ihtiyaç duyulmanız durumunda, müşterilerin sorunları 31 Ekim 2019 ' e kadar çözmesi gerekir. Sorunlar daha sonra çözülemezse, klasik uyarılarınızın başarılı geçirilmesi garanti edilemez.

    > [!NOTE]
    > Otomatik geçiş işleminin başlamasını beklemek istemiyorsanız geçiş aracını kullanarak geçiş işlemini yine de tetikleyebilirsiniz.

## <a name="important-things-to-note"></a>Dikkat edilmesi gereken önemli noktalar

Geçiş işlemi, klasik uyarı kurallarını yeni, denk uyarı kurallarına dönüştürür ve eylem gruplarını oluşturur. Hazırlık bölümünde aşağıdaki noktalara dikkat edin:

- Yeni uyarı kuralları için bildirim yükü biçimleri, daha fazla özelliği desteklediklerinden, klasik uyarı kurallarından farklıdır. Klasik uyarı kuralı tarafından tetiklenen Logic Apps, runbook 'lar veya Web kancaları varsa, bildirim yüklerindeki farklılıklar nedeniyle, geçiş işlemi tamamlandıktan sonra beklendiği gibi çalışmayı durdurabilir. [Geçişe hazırlanma hakkında bilgi edinin](alerts-prepare-migration.md).

- Bazı klasik uyarı kuralları araç kullanılarak geçirilemez. [Hangi kuralların geçirilemeyeceğini ve bunlarla ne yapılacağını öğrenin](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > Geçiş işlemi, klasik uyarı kurallarınızın değerlendirmesini etkilemez. Bunlar geçirilmeden ve yeni uyarı kuralları etkinleşene kadar uyarıları çalıştırmaya ve gönderilmeye devam ederler.

## <a name="what-if-the-automatic-migration-fails"></a>Otomatik geçiş başarısız olursa ne olur?

Otomatik geçiş işlemi başarısız olduğunda, abonelik sahipleri sorunu bildiren bir e-posta alır. Sorunun tüm ayrıntılarını görmek için Azure Izleyici 'deki geçiş dikey penceresini kullanabilirsiniz.

Geçiş sırasında karşılaşabileceğiniz sorunlar hakkında yardım almak için [sorun giderme kılavuzuna](alerts-understand-migration.md#common-problems-and-remedies) bakın.

  > [!NOTE]
  > Müşterinin bir kaynak kilidini geçici olarak devre dışı bırakma veya bir ilke atamasını değiştirme gibi bir eyleme ihtiyaç duyulmanız durumunda, müşterilerin sorunları 31 Ekim 2019 ' e kadar çözmesi gerekir. Sorunlar daha sonra çözülemezse, klasik uyarılarınızın başarılı geçirilmesi garanti edilemez.

## <a name="next-steps"></a>Sonraki adımlar

- [Geçişe hazırlanma](alerts-prepare-migration.md)
- [Geçiş aracının nasıl çalıştığını anlama](alerts-understand-migration.md)
