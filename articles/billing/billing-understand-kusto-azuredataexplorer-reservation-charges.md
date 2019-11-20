---
title: Rezervasyon indiriminin Azure Veri Gezgini'ne nasıl uygulandığını anlama
description: Rezervasyon indiriminin Azure Veri Gezgini kar payı ölçümüne nasıl uygulandığını öğrenin.
services: data-explorer
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: eba8a9b2a2774ba721a8442ca03035bd3801dc53
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585524"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-data-explorer"></a>Rezervasyon indiriminin Azure Veri Gezgini'ne nasıl uygulandığını anlama

Bir Azure Veri Gezgini ayrılmış kapasitesi satın almanızın ardından, rezervasyonun öznitelikleriyle ve miktarıyla eşleşen Azure Veri Gezgini'ne otomatik olarak rezervasyon indirimi uygulanır. Rezervasyon, Azure Veri Gezgini kar payı ücretlerini içerir. Azure Veri Gezgini kümesini çalıştırmak için kullanılan işlem, ağ, depolama veya diğer Azure kaynaklarını içermez. Bu kaynakların için rezervasyonlar ayrıca satın alınmalıdır.

## <a name="how-reservation-discount-is-applied"></a>Rezervasyon indiriminin uygulanması

Rezervasyon indirimi “*kullanılmadığı takdirde hakkı kaybedilen*” bir özelliktir. Bu nedenle, herhangi bir saat için eşleşen kaynaklarınız yoksa, o saate ait rezervasyon miktarını kaybedersiniz. Kullanılmayan ayrılmış saatleri devredemezsiniz.

Bir kaynağı kapattığınızda rezervasyon indirimi, belirtilen kapsamdaki başka bir eşleşen kaynağa otomatik olarak uygulanır. Belirtilen kapsamda eşleşen kaynak bulunamazsa, ayrılan saatler *kaybedilir*.

## <a name="reservation-discount-applied-to-azure-data-explorer-clusters"></a>Azure Veri Gezgini kümelerine uygulanan rezervasyon indirimi

Rezervasyon indirimi Azure Veri Gezgini kar payı tüketimine saat bazında uygulanır. Saatin tamamı boyunca çalıştırılmayan Azure Veri Gezgini kaynakları için, rezervasyon indirimi rezervasyon öznitelikleriyle eşleşen diğer Veri Gezgini kaynaklarına otomatik olarak uygulanır. İndirim, eş zamanlı olarak çalıştırılan Azure Veri Gezgini kaynaklarına uygulanabilir. Rezervasyon öznitelikleriyle eşleşen ve saatin tamamı boyunca çalıştırılan Azure Veri Gezgini kaynaklarınız yoksa, ilgili saat için rezervasyon indiriminden tam olarak yararlanmazsınız.

> [!NOTE]
> * Ayrılmış kapasite ile elde edilen tasarrufu en üst düzeye çıkarmak için Azure Veri Gezgini kümesi için kullanılan sanal makineler için de [ayrılmış kapasite](../virtual-machines/windows/prepay-reserved-vm-instances.md) satın almanız **kesinlikle önerilir**.
> * Rezervasyon satın alarak tüm bölgelerde indirimden faydalanabilirsiniz.

## <a name="examples"></a>Örnekler

Aşağıdaki örneklerde, satın aldığınız kar payı birimi sayısına ve çalıştırılma zamanına bağlı olarak Azure Veri Gezgini ayrılmış kapasite indiriminin nasıl uygulandığı gösterilmektedir.
Örneğin, şu altyapı kümesi boyutunu ele alalım: **2 D11_v2 VM** için toplam isteğe bağlı ücretleriniz, saatlik olarak hesaplanan dört Azure Veri Gezgini kar payı ölçümü birimi olur. 

**Senaryo 1** 

8 Azure Veri Gezgini kar payı birimi için Azure Veri Gezgini ayrılmış kapasitesi satın aldığınızı düşünelim. Saatte 16 Azure Veri Gezgini kar payı birimi gerektiren ve rezervasyonun diğer öznitelikleriyle eşleşen, toplam 16 çekirdeğe sahip olan iki D13_v2 VM'den oluşan bir altyapı kümesi çalıştırıyorsunuz. Sekiz Azure Veri Gezgini işlem kullanımı çekirdeği için kullandıkça öde fiyatları üzerinden ücretlendirilirsiniz ve bir saatlik sekiz çekirdek Azure Veri Gezgini kar payı birimi kullanımı için rezervasyon indiriminden faydalanırsınız.

Bu örneklerin geri kalanında, satın aldığınız Azure Veri Gezgini ayrılmış kapasitesinin 16 çekirdekli Azure Veri Gezgini kümesi için olduğunu ve rezervasyon özniteliklerinin geri kalanının, çalışmakta olan Azure Veri Gezgini kümesi ile eşleştiğini varsayın.

**Senaryo 2** 

İki farklı bölgede yer alan ve her biri sekiz çekirdeğe sahip olan iki Azure Veri Gezgini altyapı kümesini bir saat boyunca çalıştırıyorsunuz. 16 çekirdek rezervasyon indirimi hem kümeye hem de tüketilen 16 Azure Veri Gezgini kar payı birimine uygulanır.

**Senaryo 3** 

Saat 13:00 ile 13:30 arasında 16 çekirdekli bir Azure Veri Gezgini altyapı kümesi çalıştırıyorsunuz. Saat 13:30 ile 14:00 arasında 16 çekirdekli başka bir Azure Veri Gezgini altyapı kümesi çalıştırıyorsunuz. Her ikisi de rezervasyon indirimi kapsamındadır.

**Senaryo 4** 

Saat 13:00 ile 13:45 arasında 16 çekirdekli bir Azure Veri Gezgini altyapı kümesi çalıştırıyorsunuz. Saat 13:30 ile 14:00 arasında 16 çekirdekli başka bir Azure Veri Gezgini altyapı kümesi çalıştırıyorsunuz. 15 dakikalık çakışma için kullandıkça öde fiyatıyla ücretlendirilirsiniz. Rezervasyon indirimi, geri kalan süre boyunca Azure Veri Gezgini kar payı kullanımına uygulanır.

Faturalama kullanım raporlarında Azure rezervasyonlarınızın uygulamasını anlamak ve görüntülemek için bkz. [Azure rezervasyon kullanımınızı anlama](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

Azure rezervasyonları hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure Veri Gezgini ayrılmış kapasitesi ile Azure Veri Gezgini işlem kaynakları için önceden ödeme yapın](../data-explorer/pricing-reserved-capacity.md)  
* [Azure rezervasyonları nedir?](billing-save-compute-costs-reservations.md)  
* [Azure ayırmalarını yönetme](billing-manage-reserved-vm-instance.md)  
* [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage.md)
* [Kurumsal kaydınız için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage-ea.md)
* [CSP abonelikleri için rezervasyon kullanımını anlama](https://docs.microsoft.com/partner-center/azure-reservations)
