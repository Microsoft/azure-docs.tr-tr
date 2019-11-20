---
title: MySQL için Azure Veritabanına rezervasyon indiriminin uygulanması
description: MySQL için Azure Veritabanına rezervasyon indiriminin uygulanması
author: kummanish
ms.author: manishku
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.openlocfilehash: 905a0c77919e448a7c3387ca8fa35a8150780534
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608161"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mysql"></a>MySQL için Azure Veritabanına rezervasyon indiriminin uygulanması

Bir MySQL için Azure Veritabanı ayrılmış kapasitesi satın almanızın ardından, rezervasyonun öznitelikleriyle ve miktarıyla eşleşen MySQL sunucularına otomatik olarak rezervasyon indirimi uygulanır. Rezervasyon yalnızca MySQL için Azure Veritabanı işlem maliyetlerini kapsar. Depolama ve ağ iletişimi için normal fiyatlarla ücretlendirilirsiniz. 

## <a name="how-reservation-discount-is-applied"></a>Rezervasyon indiriminin uygulanması

Rezervasyon indirimi ***kullanılmadığı takdirde hakkı kaybedilen*** bir özelliktir. Bu nedenle, herhangi bir saat için eşleşen kaynaklarınız yoksa, o saate ait rezervasyon miktarını kaybedersiniz. Kullanılmayan ayrılmış saatleri devredemezsiniz.</br>

Bir kaynağı kapattığınızda rezervasyon indirimi, belirtilen kapsamdaki başka bir eşleşen kaynağa otomatik olarak uygulanır. Belirtilen kapsamda eşleşen kaynak bulunamazsa, ayrılan saatler kaybedilir.

## <a name="discount-applied-to-azure-database-for-mysql"></a>MySQL için Azure Veritabanına uygulanan indirim

MySQL için Azure Veritabanı ayrılmış kapasitesi indirimi, çalışan MySQL sunucularınıza saatlik olarak uygulanır. Satın aldığınız rezervasyon, çalışmakta olan MySQL için Azure Veritabanı sunucuları tarafından gösterilen işlem kullanımı ile eşleştirilir. Saatin tamamı boyunca çalışmayan MySQL sunucuları olursa rezervasyon, rezervasyon öznitelikleriyle eşleşen diğer MySQL için Azure Veritabanı sunucularına otomatik olarak uygulanır. İndirim eşzamanlı olarak çalışan MySQL için Azure Veritabanı sunucularına uygulanabilir. Rezervasyon öznitelikleriyle eşleşen ve saatin tamamı boyunca çalışan bir MySQL sunucunuz yoksa, ilgili saat için rezervasyon indiriminden tam olarak yararlanmazsınız.

Aşağıdaki örneklerde, satın aldığınız çekirdek sayısına ve çalıştırılma zamanına bağlı olarak MySQL için Azure Veritabanı ayrılmış kapasite indiriminin nasıl uygulanacağı gösterilmektedir.

* **Örnek 1**: 8 sanal çekirdek için MySQL için Azure Veritabanı ayrılmış kapasitesi satın alabilirsiniz. Rezervasyonun diğer öznitelikleriyle eşleşen 16 sanal çekirdekli MySQL için Azure Veritabanı sunucusu çalıştırıyorsanız MySQL sunucusu işlem kullanımınızın 8 sanal çekirdeği için kullandıkça öde fiyatları üzerinden ücretlendirilirsiniz ve 8 sanal çekirdek MySQL sunucusu işlem kullanımı için bir saatlik indirimden faydalanırsınız.</br>

Bu örneklerin geri kalan kısmında satın aldığınız MySQL için Azure Veritabanı ayrılmış kapasitesinin 16 sanal çekirdekli MySQL için Azure Veritabanı için olduğunu ve diğer rezervasyon özniteliklerinin çalışan MySQL sunucuları ile eşleştiğini kabul edelim.

* **Örnek 2**: Bir saat boyunca her biri 8 sanal çekirdeğe sahip olan iki MySQL için Azure Veritabanı sunucuları çalıştırıyorsunuz. 8 çekirdekli iki MySQL için Azure Veritabanı sunucusunun işlem kullanımına 16 sanal çekirdek rezervasyon indirimi uygulanır.

* **Örnek 3**: Saat 13:00 ile 13:30 arasında 16 sanal çekirdekli MySQL için Azure Veritabanı sunucusu çalıştırıyorsunuz. Saat 13:30 ile 14:00 arasında başka bir 16 sanal çekirdekli MySQL için Azure Veritabanı sunucusu çalıştırıyorsunuz. Her ikisi de rezervasyon indirimi kapsamındadır.

* **Örnek 4**: Saat 13:00 ile 13:45 arasında 16 sanal çekirdekli MySQL için Azure Veritabanı sunucusu çalıştırıyorsunuz. Saat 13:30 ile 14:00 arasında başka bir 16 sanal çekirdekli MySQL için Azure Veritabanı sunucusu çalıştırıyorsunuz. 15 dakikalık çakışma için kullandıkça öde fiyatıyla ücretlendirilirsiniz. Rezervasyon indirimi, geri kalan süre boyunca işlem kullanımına uygulanır.

Faturalama kullanım raporlarında Azure rezervasyonlarınızın uygulamasını anlamak ve görüntülemek için bkz. [Azure rezervasyon kullanımınızı anlama](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun
Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).
