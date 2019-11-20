---
title: Azure altyapı kullanılabilirliği-Azure güvenliği
description: Bu makalede, Microsoft 'un Azure altyapısını güvenli hale getirme ve müşterilerin verilerinin maksimum kullanılabilirliğini sağlama hakkında bilgi sağlanır.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: c50c4faf47caf0a7519d61fdc8989ec9fd809d78
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727225"
---
# <a name="azure-infrastructure-availability"></a>Azure altyapı kullanılabilirliği
Bu makalede, Microsoft 'un Azure altyapısını güvenli hale getirme ve müşterilerin verilerinin maksimum kullanılabilirliğini sağlama hakkında bilgi sağlanır. Azure, Sanallaştırma teknolojisiyle elde edilen kapsamlı artıklık temelinde sağlam kullanılabilirlik sağlar.

## <a name="temporary-outages-and-natural-disaster"></a>Geçici kesintiler ve doğal olağanüstü durum
Microsoft Bulut altyapısı ve Işlemler ekibi, bulut altyapısının güvenliğini tasarlar, oluşturur, çalıştırır ve geliştirir. Bu ekip, Azure altyapısının yüksek kullanılabilirlik ve güvenilirlik, yüksek verimlilik ve akıllı ölçeklenebilirlik sunmasını sağlar. Takım daha güvenli, özel ve güvenilir bir bulut sağlar.

Kesintisiz güç kaynakları ve büyük pil kümeleri, kısa süreli bir güç kesintisi oluşursa elektrik 'nin sürekli kalmasını sağlar. Acil durumlar, genişletilmiş kesintiler ve planlı bakım için yedekleme gücü sağlar. Doğal bir olağanüstü durum oluşursa, veri merkezi yerinde yakıt rezervlerini kullanabilir.

Yüksek hızlı ve sağlam fiber optik ağlar, veri merkezlerini diğer büyük hub 'lar ve internet kullanıcıları ile birbirine bağlanır. İşlem düğümleri ana bilgisayar iş yüklerini, gecikme süresini azaltmak, coğrafi yedeklilik sağlamak ve genel hizmet dayanıklılığı artırmak için kullanıcılara yaklaşarak barındırır. Mühendislerin bir ekibi, hizmetin sürekli olarak kullanılabilir olmasını sağlamak için saatin etrafında çalışmaktadır.

Microsoft, Gelişmiş izleme ve olay yanıtı, hizmet desteği ve yedekleme yük devretme özelliği aracılığıyla yüksek kullanılabilirlik sağlar. Coğrafi olarak dağıtılmış Microsoft Operations Centers 24/7/365 çalışır. Azure ağı, dünyanın en büyük yanından biridir. Fiber optik ve içerik dağıtım ağı, yüksek performans ve güvenilirlik sağlamak için veri merkezlerini ve kenar düğümlerini bağlar.

## <a name="disaster-recovery"></a>Olağanüstü durum kurtarma
Azure, verilerinizi iki konumda sürekli olarak tutar. Yedekleme sitesinin konumunu seçebilirsiniz. Her iki konumda de Azure sürekli olarak verilerinizin üç sağlıklı çoğaltmasını tutar.

## <a name="database-availability"></a>Veritabanı kullanılabilirliği
Azure, bir veritabanının sürekli veritabanı kullanılabilirliğine sahip bir internet ağ geçidi aracılığıyla internet 'e erişilebilir olmasını sağlar. Değerlendirir, etkin veritabanlarının durum ve durumunu beş dakikalık zaman aralıklarında izleme.

## <a name="storage-availability"></a>Depolama kullanılabilirliği
Azure, bağlantı uç noktaları sağlayan, yüksek düzeyde ölçeklenebilir ve dayanıklı bir depolama hizmeti aracılığıyla depolama alanı sunar. Bu, bir uygulamanın depolama hizmetine doğrudan erişebileceği anlamına gelir. Depolama hizmeti, işlem bütünlüğü ile gelen depolama isteklerini verimli bir şekilde işler.

## <a name="next-steps"></a>Sonraki adımlar
Microsoft 'un Azure altyapısını güvenli hale getirmeye yardımcı olma hakkında daha fazla bilgi edinmek için bkz.:

- [Azure tesisler, şirket içi ve fiziksel güvenlik](physical-security.md)
- [Azure Information System bileşenleri ve sınırları](infrastructure-components.md)
- [Azure ağ mimarisi](infrastructure-network.md)
- [Azure üretim ağı](production-network.md)
- [Azure SQL veritabanı güvenlik özellikleri](infrastructure-sql.md)
- [Azure üretim işlemleri ve yönetimi](infrastructure-operations.md)
- [Azure altyapı izleme](infrastructure-monitoring.md)
- [Azure altyapı bütünlüğü](infrastructure-integrity.md)
- [Azure müşteri verileri koruması](protection-customer-data.md)
