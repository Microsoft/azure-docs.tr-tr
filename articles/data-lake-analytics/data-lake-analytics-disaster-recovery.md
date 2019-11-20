---
title: Azure Data Lake Analytics için olağanüstü durum kurtarma Kılavuzu
description: Azure Data Lake Analytics hesaplarınız için olağanüstü durum kurtarmayı nasıl planlayacağınızı öğrenin.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f9b22e6b806f76189134ec63c83d48f48bf95587
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889772"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics için olağanüstü durum kurtarma Kılavuzu

Azure Data Lake Analytics, büyük verileri kolaylaştıran, isteğe bağlı bir analiz işi hizmetidir. Donanım dağıtma, yapılandırma ve ayarlama işlemleri yerine, verilerinizi dönüştürmek ve değerli öngörüleri ayıklamak için sorgular yazarsınız. Analiz hizmeti sadece ne kadar güce ihtiyacınız olduğunu ayarlayarak her ölçekteki işin üstesinden gelmenizi sağlar. Yalnızca işiniz çalıştırıldığında ücret ödersiniz; bu da hizmeti uygun maliyetli kılar. Bu makalede, işlerinizi nadir bölge genelinde kesintiler veya yanlışlıkla silme işlemlerini nasıl koruyabileceğiniz hakkında yönergeler sunulmaktadır.

## <a name="disaster-recovery-guidance"></a>Olağanüstü durum kurtarma kılavuzu

Azure Data Lake Analytics kullanırken, kendi olağanüstü durum kurtarma planınızı hazırlamanız önemlidir. Bu makale bir olağanüstü durum kurtarma planı oluşturmanıza yardımcı olur. Kendi planınızı oluşturmanıza yardımcı olabilecek ek kaynaklar vardır:
- [Azure uygulamaları için hata ve olağanüstü durum kurtarma](/azure/architecture/reliability/disaster-recovery)
- [Azure dayanıklılık teknik kılavuzu](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>En iyi uygulamalar ve senaryo Kılavuzu

Aynı bir U-SQL işini, U-SQL tablolarının yanı sıra yapılandırılmamış verileri okuyan ve yazan bir bölgedeki ADLA hesabında çalıştırabilirsiniz.  Aşağıdaki adımları uygulayarak olağanüstü duruma hazırlanın:

1. Bir kesinti sırasında kullanılacak ikincil bölgede ADLA ve ADLS hesapları oluşturun.

   > [!NOTE]
   > Hesap adları genel olarak benzersiz olduğundan, hangi hesabın ikincil olduğunu gösteren tutarlı bir adlandırma düzeni kullanın.

2. Yapılandırılmamış veriler için [Azure Data Lake Storage 1. 'deki veriler Için olağanüstü durum kurtarma Kılavuzu](../data-lake-store/data-lake-store-disaster-recovery-guidance.md) ' na başvurun

3. ADLA tablolarında ve veritabanlarında depolanan yapılandırılmış veriler için veritabanları, tablolar, tablo değerli işlevler ve derlemeler gibi meta veri yapılarının kopyalarını oluşturun. Üretimde değişiklikler olduğunda düzenli aralıklarla bu yapıtları yeniden eşitlemeniz gerekir. Örneğin, yeni eklenen verilerin, verileri kopyalayarak ve ikincil tabloya eklenerek ikincil bölgeye çoğaltılması gerekir.

   > [!NOTE]
   > Bu nesne adları ikincil hesap kapsamlandırılır ve genel olarak benzersiz değildir, bu nedenle birincil üretim hesabındaki adlara sahip olabilirler.

Kesinti sırasında, giriş yollarının ikincil uç noktaya işaret edebilmesi için betikleri güncelleştirmeniz gerekir. Ardından kullanıcılar işlerini ikincil bölgedeki ADLA hesabına gönderir. Daha sonra işin çıktısı, İkincil bölgedeki ADLA ve ADLS hesabına yazılır.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Data Lake Storage 1. veri için olağanüstü durum kurtarma Kılavuzu](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
