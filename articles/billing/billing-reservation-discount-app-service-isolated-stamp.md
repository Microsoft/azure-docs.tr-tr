---
title: Azure App Service Yalıtılmış Damga Pulları için rezervasyon indirimlerinin uygulanması
description: Azure App Service Yalıtılmış Damga Pulları için rezervasyon indirimlerinin nasıl uygulandığını öğrenin.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 759f83001353957f23b917440b68719b54690bea
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718815"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Azure App Service Yalıtılmış Damga Pulları için rezervasyon indirimlerinin uygulanması

App Service Yalıtılmış Damga Pulu Ücreti ayrılmış kapasitesini satın almanızın ardından, bir bölgedeki Damga Pulu Ücretine otomatik olarak rezervasyon indirimi uygulanır. Rezervasyon indirimi, Yalıtılmış Damga Pulu Ücreti ölçümü tarafından gösterilen kullanıma uygulanır. Çalışanlar, ek Ön Uçlar ve damga puluyla ilişkili diğer tüm kaynaklar normal fiyatla faturalandırılmaya devam eder.

## <a name="reservation-discount-application"></a>Rezervasyon indirimi uygulama

App Service Yalıtılmış Damga Pulu Ücreti indirimi, çalıştırılan damga pullarına saat bazında uygulanır. Bir saat için dağıtılmış bir damga pulunuz yoksa, o saat için ayrılmış kapasite boşa gider. Devredilmez.

Satın alma işleminden sonra, satın aldığınız rezervasyon, belirli bir bölgede çalıştırılan yalıtılmış bir damga puluyla eşleştirilir. Bu damga pulunu kapatırsanız, bölgede çalıştırılan diğer tüm damga pullarına otomatik olarak rezervasyon indirimleri uygulanır. Bir damga pulu yoksa, bölgede oluşturulan sonraki damgaya rezervasyon uygulanır.

Damga pulları tam bir saat boyunca çalışmadığında aynı saat boyunca aynı bölgedeki diğer eşleşen damga pullarına otomatik olarak rezervasyon uygulanır.

## <a name="choose-a-stamp-type---windows-or-linux"></a>Damga pulu türü seçme - Windows veya Linux

Boş bir Yalıtılmış Damga Pulu varsayılan olarak Windows damga pulu ölçümünü gösterir. Örneğin, herhangi bir çalışan dağıtılmadığında. Windows çalışanları dağıtıldığında ölçümü göstermeye devam eder. Linux çalışanı dağıtırsanız ölçüm, Linux damga pulu ölçümüne değişir. Damga pulu, hem Linux hem de Windows çalışanları dağıtıldığında Windows ölçümünü gösterir.

Sonuç olarak damga pulu ölçümü, damga pulunun ömrü boyunca Windows ve Linux arasında değişebilir. Bu arada rezervasyonlar işletim sistemine özgüdür. Damga pulunu dağıtmayı planladığınız çalışanları destekleyen bir rezervasyon satın almanız gerekir. Yalnızca Windows damga pulları ve karma damga pulları, Windows rezervasyonunu kullanır. Yalnızca Linux çalışanları olan damga pulları, Linux rezervasyonunu kullanır.

Yalnızca damga pulunda _sırf_ Linux çalışanları olmasını planladığınızda Linux rezervasyonu satın almanız gerekir.

## <a name="discount-examples"></a>İndirim örnekleri

Aşağıdaki örneklerde, dağıtıma bağlı olarak Yalıtılmış Damga Pulu Ücreti ayrılmış örneğinin nasıl uygulandığı gösterilmektedir.

- **Örnek 1**: App Service Yalıtılmış damga pulları olmayan bir bölgede tek bir Yalıtılmış Ayrılmış Damga Pulu kapasitesi örneği satın alırsınız. Bölgede yeni bir damga dağıtır ve bu damga için ayrılmış ücretler ödersiniz.
- **Örnek 2**: App Service Yalıtılmış damga pulu önceden dağıtılmış olan bir bölgede tek bir Yalıtılmış Ayrılmış Damga Pulu kapasitesi örneği satın alırsınız. Dağıtılan damga pulu için ayrılmış ücret almaya başlarsınız.
- **Örnek 3**: App Service Yalıtılmış damga pulu önceden dağıtılmış olan bir bölgede tek bir Yalıtılmış Ayrılmış Damga Pulu kapasitesi örneği satın alırsınız. Dağıtılan damga pulu için ayrılmış ücret almaya başlarsınız. Daha sonra damga pulunu silip yeni birini dağıtırsınız. Yeni damga pulu için ayrılmış ücret alırsınız. Dağıtılan damga pulları olmayan süreler için indirimler devredilmez.
- **Örnek 4**: Bir bölgede tek bir Yalıtılmış Linux Ayrılmış Damga Pulu kapasitesi satın alır ve sonra bu bölgeye yeni bir damga pulu dağıtırsınız. Damga pulu başlangıçta çalışanlar olmadan dağıtıldığında, Windows damga pulu ölçümünü gösterir. Bir indirim alınmaz. Damga puluna ilk Linux çalışanı dağıtıldığında, Linux Damga Pulu ölçümünü gösterir ve rezervasyon indirimi uygulanır. Daha sonra damga puluna bir Windows çalışanı dağıtılırsa, damga pulu ölçümü Windows’a geri döndürülür. Yalıtılmış Linux Ayrılmış Damga Pulu rezervasyonu için artık indirim almazsınız.

## <a name="next-steps"></a>Sonraki adımlar

- Rezervasyonu yönetme hakkında bilgi edinmek için bkz. [Azure Ayrılmış Sanal Makine Örnekleri’ni Yönetme](billing-manage-reserved-vm-instance.md).
- Para tasarrufu için önceden App Service Yalıtılmış Damga Pulu ayrılmış kapasitesi satın alma hakkında daha fazla bilgi edinmek için bkz. [Ayrılmış kapasiteli Azure App Service Yalıtılmış Damga Pulu Ücreti için önceden ödeme yapma](billing-prepay-app-service-isolated-stamp.md).
- Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
  - [Azure Ayrılmış Sanal Makine Örnekleri nedir?](billing-save-compute-costs-reservations.md)
  - [Azure’da Rezervasyonları Yönetme](billing-manage-reserved-vm-instance.md)
  - [Kullandıkça öde fiyatlarına tabi bir abonelik için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage.md)
  - [Kurumsal kaydınız için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage-ea.md)
