---
title: Azure Data Factory veri akışları için rezervasyonlar iskontosunu anlayın | Microsoft Docs
description: ADF veri akışlarını çalıştırmak için bir rezervasyon iskontosunun nasıl uygulanacağını öğrenin. İndirim bu veri akışlarına saatlik olarak uygulanır.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 12b640fd97f48e293320593b33ab2fdc54980c0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101716304"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Bir rezervasyon iskontosunun Azure Data Factory veri akışlarına nasıl uygulandığı

ADF veri akışı ayrılmış kapasitesini satın aldıktan sonra, rezervasyon indirimi, rezervasyonun işlem türü ve çekirdek sayısıyla eşleşen bir Azure tümleştirme çalışma zamanı kullanılarak veri akışlarına otomatik olarak uygulanır.

## <a name="how-reservation-discount-is-applied"></a>Rezervasyon indiriminin uygulanması

Rezervasyon indirimi “*kullanılmadığı takdirde hakkı kaybedilen*” bir özelliktir. Bu nedenle, herhangi bir saat için eşleşen Azure tümleştirme kaynaklarınız yoksa, bu saat için bir rezervasyon miktarı kaybedersiniz. Kullanılmayan ayrılmış saatleri devredemezsiniz.

Veri akışları için tümleştirme çalışma zamanının kullanımını durdurduğunuzda, rezervasyon iskontosu otomatik olarak belirtilen kapsamdaki başka bir eşleşen kaynak için geçerli olur. Belirtilen kapsamda eşleşen kaynak bulunamazsa, ayrılan saatler *kaybedilir*.

## <a name="discount-applied-to-adf-data-flows"></a>ADF veri akışlarına uygulanan indirim

ADF veri akışı ayrılmış kapasite indirimi, tümleştirme çalışma zamanlarını saatlik olarak yürütmek için uygulanır. Satın aldığınız rezervasyon, veri akışlarınızın tümleştirme çalışma zamanları tarafından kullanılan işlem türü ile eşleştirilir. Tam saati çalıştırmayan veri akışları için rezervasyon, rezervasyon öznitelikleriyle eşleşen diğer veri akışlarına otomatik olarak uygulanır. Ayrıca, aynı anda çalışan veri akışları için de indirim uygulanabilir. Ayırma öznitelikleriyle eşleşen tam saat için çalışan veri akışları yoksa, bu saat için rezervasyon iskontosunun tüm avantajını elde etmeyin.

Aşağıdaki örneklerde, ADF veri akışı ayrılmış kapasite iskontosunun, satın aldığınız çekirdek sayısına ve ne zaman çalıştığına bağlı olarak nasıl uygulandığı gösterilmektedir.

- Senaryo 1: bellek için iyileştirilmiş işlem türü miktarı olarak 80 ' i girerek 1 saat 80 çekirdekli bellek için en iyi duruma getirilmiş işlem için ADF veri akışı ayırması satın alabilirsiniz. Bir saat için en iyileştirilmiş 144 çekirdek bellek olarak ayarlanmış bir Azure tümleştirme çalışma zamanı ile veri akışı çalıştırırsınız. Bir saat boyunca veri akışı kullanımının 64 çekirdekleri için Kullandıkça Öde fiyatı üzerinden ücretlendirilirsiniz. Bellek için iyileştirilmiş kullanım için bir saatlik 80 çekirdeğe yönelik rezervasyon indirimi elde edersiniz.
- Senaryo 2: genel amaçlı işlem türü miktarı olarak 32 ' i girerek 1 saatlik 32 çekirdeğe yönelik bir ADF veri akışı ayırması satın alabilirsiniz. Genel işlem Azure tümleştirme çalışma zamanının 32 çekirdeğini kullanarak 1 saat boyunca veri akışlarınızın hatalarını ayıklayın. Tüm kullanım saati için rezervasyon indirimi elde edersiniz.

Faturalama kullanım raporlarında Azure rezervasyonlarınızın uygulamasını anlamak ve görüntülemek için bkz. [Azure rezervasyon kullanımınızı anlama](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

Azure ayırmaları hakkında daha fazla bilgi edinmek için aşağıdaki makaleye bakın:

- [Azure Ayrılmış Sanal Makine Örnekleri nedir?](../cost-management-billing/reservations/save-compute-costs-reservations.md)