---
title: Azure ve tek başına Service Fabric kümelerine genel bakış
description: Windows Server veya Linux çalıştıran tüm VM 'lerde veya bilgisayarlarda Service Fabric kümeleri oluşturabilirsiniz. Bu, şirket içinde, Microsoft Azure veya herhangi bir bulut sağlayıcısıyla birbirine bağlı bir Windows Server veya Linux bilgisayar kümesine sahip olduğunuz herhangi bir ortamda Service Fabric uygulamaları dağıtabileceğiniz ve çalıştırabileceğiniz anlamına gelir.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: d83ec139c367d9a539db173a42ae909f57d5d71a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576137"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Windows Server ve Linux 'ta Azure ve tek başına Service Fabric kümelerini karşılaştırma

Service Fabric küme, mikro hizmetlerinizin dağıtıldığı ve yönetildiği, ağa bağlı bir sanal veya fiziksel makine kümesidir. Bir kümenin parçası olan makineye veya VM 'ye küme düğümü denir. Kümeler, binlerce düğüme ölçeklendirebilir. Kümeye yeni düğümler eklerseniz, hizmet bölümü çoğaltmaları ve örneklerinin artan düğüm sayısı genelinde yeniden dengelenmesi Service Fabric. Genel uygulama performansı, bellek düşüşlerine erişim için gelişir ve çekişmeyi geliştirir. Kümedeki düğümler verimli bir şekilde kullanılmıyorsa, kümedeki düğümlerin sayısını azaltabilirsiniz. Service Fabric, her düğümdeki donanımın daha iyi kullanılmasını sağlamak için bölüm çoğaltmalarını ve örnekleri, azaltılmış düğüm sayısı genelinde yeniden dengeler.

Service Fabric, Windows Server veya Linux çalıştıran tüm VM 'lerde veya bilgisayarlarda Service Fabric kümelerinin oluşturulmasına izin verir. Bu, birbirine bağlı bir Windows Server veya Linux bilgisayar kümesi, şirket içi, Microsoft Azure veya herhangi bir bulut sağlayıcısına sahip olduğunuz herhangi bir ortamda Service Fabric uygulamaları dağıtabileceğiniz ve çalıştırabileceğiniz anlamına gelir.

## <a name="benefits-of-clusters-on-azure"></a>Azure 'da kümelerin avantajları

Azure 'da, diğer Azure özellikleri ve hizmetleriyle tümleştirme sağlıyoruz. Bu, kümenin işlemlerini ve yönetimini daha kolay ve güvenilir hale getirir.

* **Azure Portal:** Azure portal kümeleri oluşturmayı ve yönetmeyi kolaylaştırır.
* **Azure Resource Manager:** Azure Resource Manager kullanımı, küme tarafından kullanılan tüm kaynakların birim olarak kolay yönetilmesini sağlar ve maliyet izlemeyi ve faturalandırmayı basitleştirir.
* **Azure kaynağı olarak Service Fabric kümesi** Service Fabric kümesi bir Azure kaynağıdır, bu nedenle Azure 'daki diğer kaynakları istediğiniz gibi modelleyebilirsiniz.
* **Azure altyapısıyla tümleştirme** , Uygulamalarınızın kullanılabilirliğini ve güvenilirliğini artırmak için işletim sistemi, ağ ve diğer yükseltmelere yönelik temel Azure altyapısıyla birlikte koordinatları Service Fabric.  
* **Tanılama:** Azure 'da Azure tanılama ve Azure Izleyici günlükleri ile tümleştirme sağlıyoruz.
* **Otomatik ölçeklendirme:** Azure üzerindeki kümeler için, sanal makine ölçek kümeleri aracılığıyla yerleşik otomatik ölçeklendirme işlevselliği sağlıyoruz. Şirket içi ve diğer bulut ortamlarında kendi otomatik ölçeklendirme özelliğini oluşturmanız veya Service Fabric kümeleri ölçeklendirmeye yönelik API 'Leri kullanarak el ile ölçeklendirmeniz gerekir.

## <a name="benefits-of-standalone-clusters"></a>Tek başına kümelerin avantajları

* Kümenizi barındırmak için herhangi bir bulut sağlayıcısı seçebilirsiniz.
* Service Fabric uygulamalar, yazıldıktan sonra birden çok barındırma ortamında, hiçbir değişiklik yapmadan çalıştırılabilir.
* Service Fabric uygulamaları oluşturma hakkında bilgi, bir barındırma ortamından diğerine taşır.
* Service Fabric kümelerinin çalıştırılmasına ve yönetilmesine yönelik işletimsel deneyim, bir ortamdan diğerine devredilme deneyimi sağlar.
* Geniş müşteri erişim, ortam kısıtlamalarını barındırarak sınırsız bir şekilde yapılır.
* Bir veri merkezinde veya bulut sağlayıcısında bir kesinti varsa Hizmetleri başka bir dağıtım ortamına taşıyabilmeniz için çok sayıda güvenilirlik ve koruma daha yaygın kesintilere karşı koruma vardır.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure 'da Service Fabric kümelerine](service-fabric-azure-clusters-overview.md) genel bakış konusunu okuyun
* [Tek başına Service Fabric kümelerine](service-fabric-standalone-clusters-overview.md) Genel Bakış bölümünü okuyun
* [Service Fabric destek seçenekleri](service-fabric-support.md) hakkında bilgi edinin