---
title: Azure SQL Veritabanları için rezervasyon indirimini anlama | Microsoft Docs
description: Çalışmakta olan Azure SQL Veritabanlarına bir rezervasyon indiriminin nasıl uygulanacağını öğrenin.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2019
ms.author: banders
ms.openlocfilehash: d7a1a451bf40a09b1bd9b9d823e0778d335a690b
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71701782"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-databases"></a>Azure SQL Veritabanlarına rezervasyon indiriminin uygulanması

Bir Azure SQL Veritabanı ayrılmış kapasitesi satın almanızın ardından, rezervasyonun öznitelikleriyle ve miktarıyla eşleşen SQL Veritabanlarına otomatik olarak rezervasyon indirimi uygulanır. Rezervasyon, SQL Veritabanınızın işlem maliyetlerini kapsar. Yazılım, depolama ve ağ iletişimi için normal fiyatlarla ücretlendirilirsiniz. [Azure Hibrit Avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) ile SQL Veritabanı için lisanslama maliyetlerini kapsayabilirsiniz.

Ayrılmış Sanal Makine Örnekleri için bkz. [Azure Ayrılmış Sanal Makine Örnekleri indirimini anlama](billing-understand-vm-reservation-charges.md).

## <a name="how-reservation-discount-is-applied"></a>Rezervasyon indiriminin uygulanması

Rezervasyon indirimi “*kullanılmadığı takdirde hakkı kaybedilen*” bir özelliktir. Bu nedenle, herhangi bir saat için eşleşen kaynaklarınız yoksa, o saate ait rezervasyon miktarını kaybedersiniz. Kullanılmayan ayrılmış saatleri devredemezsiniz.

Bir kaynağı kapattığınızda rezervasyon indirimi, belirtilen kapsamdaki başka bir eşleşen kaynağa otomatik olarak uygulanır. Belirtilen kapsamda eşleşen kaynak bulunamazsa, ayrılan saatler *kaybedilir*.

## <a name="discount-applied-to-sql-databases"></a>SQL Veritabanlarına uygulanan indirim

 SQL Veritabanı ayrılmış kapasite indirimi, çalışmakta olan SQL Veritabanlarına saat bazında uygulanır. Satın aldığınız rezervasyon, çalışmakta olan SQL Veritabanları tarafından gösterilen işlem kullanımı ile eşleştirilir. Saatin tamamı boyunca çalışmayan SQL Veritabanları olursa rezervasyon, rezervasyon öznitelikleriyle eşleşen diğer SQL veritabanlarına otomatik olarak uygulanır. Eş zamanlı olarak çalışan SQL Veritabanları için de indirim geçerli olabilir. Rezervasyon öznitelikleriyle eşleşen ve saatin tamamı boyunca çalışan bir SQL Veritabanınız yoksa, ilgili saat için rezervasyon indiriminden tam olarak yararlanmazsınız.

Aşağıdaki örneklerde, satın aldığınız çekirdek sayısına ve çalıştırılma zamanına bağlı olarak SQL Veritabanı ayrılmış kapasite indiriminin nasıl uygulanacağı gösterilmektedir.

- Senaryo 1: 8 çekirdekli bir SQL Veritabanı için SQL Veritabanı ayrılmış kapasitesi satın aldınız. Rezervasyon özniteliklerinin geri kalanıyla eşleşen 16 çekirdekli bir SQL Veritabanı çalıştırıyorsunuz. 8 çekirdekli SQL Veritabanı işlem kullanımı için kullandıkça öde fiyatıyla ücretlendirilirsiniz. Bir saatlik 8 çekirdekli SQL Veritabanı işlem kullanımı için rezervasyon indirimi elde edersiniz.

Bu örneklerin geri kalanında, satın aldığınız SQL Veritabanı ayrılmış kapasitesinin 16 çekirdekli SQL Veritabanı için olduğunu ve rezervasyon özniteliklerinin geri kalanının, çalışmakta olan SQL Veritabanları ile eşleştiğini varsayın.

- Senaryo 2: Her bir saat için 8’er çekirdekle iki SQL Veritabanı çalıştırıyorsunuz. 16 çekirdekli rezervasyon indirimi her iki 8 çekirdekli SQL Veritabanı için işlem kullanımına uygulanır.
- Senaryo 3: 13:00’dan 13:30’a kadar tek bir 16 çekirdekli SQL Veritabanı çalıştırıyorsunuz. 13:30’dan 14:00’a kadar başka bir 16 çekirdekli SQL Veritabanı çalıştırıyorsunuz. Her ikisi de rezervasyon indirimi kapsamındadır.
- Senaryo 4: 13:00’dan 13:45’e kadar tek bir 16 çekirdekli SQL Veritabanı çalıştırıyorsunuz. 13:30’dan 14:00’a kadar başka bir 16 çekirdekli SQL Veritabanı çalıştırıyorsunuz. 15 dakikalık çakışma için kullandıkça öde fiyatıyla ücretlendirilirsiniz. Rezervasyon indirimi, geri kalan süre boyunca işlem kullanımına uygulanır.

Faturalama kullanım raporlarında Azure rezervasyonlarınızın uygulamasını anlamak ve görüntülemek için bkz. [Azure rezervasyon kullanımınızı anlama](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure Ayrılmış Sanal Makine Örnekleri nedir?](billing-save-compute-costs-reservations.md)
- [Azure Ayrılmış VM Örnekleri ile Sanal Makinelere ön ödeme yapma](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Veritabanı ayrılmış kapasitesi ile SQL Veritabanı işlem kaynakları için ön ödeme yapma](../sql-database/sql-database-reserved-capacity.md)
- [Azure Ayırmalarını yönetme](billing-manage-reserved-vm-instance.md)
- [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage.md)
- [Kurumsal kaydınız için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage-ea.md)
- [CSP abonelikleri için rezervasyon kullanımını anlama](/partner-center/azure-reservations)
