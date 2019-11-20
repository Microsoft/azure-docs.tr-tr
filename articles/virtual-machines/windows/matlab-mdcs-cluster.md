---
title: Sanal makinelerde MATLAB kümeleri
description: İşlem yoğunluklu paralel MATLAB iş yüklerinizi çalıştırmak için kümelerdeki dağıtılmış bilgi Işlem sunucusu kümeleri oluşturmak üzere Microsoft Azure sanal makineler kullanın
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: gwallace
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: a2fb2479f5544b869b51e796085fcb4d0b76121a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038136"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Azure VM 'lerinde MATLAB dağıtılmış bilgi Işlem sunucusu kümeleri oluşturma
İşlem yoğunluğu olan paralel MATLAB iş yüklerinizi çalıştırmak için bir veya daha fazla MATLAB dağıtılmış bilgi Işlem sunucusu kümesi oluşturmak üzere sanal makineleri Microsoft Azure kullanın. Kümelerinizi dağıtılmış bilgi Işlem sunucusu yazılımınızı, temel görüntü olarak kullanılacak bir VM 'ye yükler ve kümeyi dağıtmak ve yönetmek için bir Azure hızlı başlangıç şablonu veya Azure PowerShell betiği ( [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)' da kullanılabilir) kullanın. Dağıtımdan sonra, iş yüklerinizi çalıştırmak için kümeye bağlanın.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>MATLAB ve MATLAB dağıtılmış bilgi Işlem sunucusu hakkında
[MATLAB](https://www.mathworks.com/products/matlab/) platformu, mühendislik ve bilimsel sorunları çözmek için iyileştirilmiştir. Büyük ölçekli benzetimler ve veri işleme görevleri olan MATLAB kullanıcıları, işlem kümelerinin ve kılavuz hizmetlerinden yararlanarak işlem yoğunluklu iş yüklerini hızlandırmak için MathWorks paralel bilgi işlem ürünlerini kullanabilir. [Paralel bilgi Işlem araç kutusu](https://www.mathworks.com/products/parallel-computing/) , MATLAB kullanıcılarının uygulamaları paralel hale getirmek ve çok çekirdekli Işlemciler, GPU 'lar ve işlem kümelerinden yararlanmasını sağlar. [MATLAB dağıtılmış bilgi Işlem sunucusu](https://www.mathworks.com/products/distriben/) , MATLAB kullanıcılarının bir işlem kümesindeki birçok bilgisayarı kullanmasına olanak sağlar.

Azure sanal makinelerini kullanarak, paralel çalışmayı, etkileşimli işler, toplu işler, bağımsız görevler ve iletişim gibi şirket içi kümeler olarak göndermek için kullanılabilen tüm mekanizmaların bulunduğu MATLAB dağıtılmış bilgi Işlem sunucusu kümeleri oluşturabilirsiniz. görevlerinize. Azure 'un MATLAB platformu ile birlikte kullanılması, geleneksel şirket içi donanımların sağlanması ve kullanılması ile karşılaştırıldığında birçok avantaj sunar: bir dizi sanal makine boyutu, isteğe bağlı kümelerin oluşturulması, böylece yalnızca kullandığınız işlem kaynakları için ödeme yaparsınız ve modelleri ölçekli olarak test etme yeteneği.  

## <a name="prerequisites"></a>Önkoşullar
* **İstemci bilgisayar** -dağıtımdan sonra Azure ve MATLAB dağıtılmış bilgi işlem sunucusu kümesiyle iletişim kurmak için Windows tabanlı bir istemci bilgisayara ihtiyacınız vardır.
* **Azure PowerShell** -istemci bilgisayarınıza yüklemek için [Azure PowerShell yüklemeyi ve yapılandırmayı öğrenin](/powershell/azure/overview) .
* **Azure aboneliği** -aboneliğiniz yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturabilirsiniz. Daha büyük kümeler için Kullandıkça Öde aboneliğine veya diğer satın alma seçeneklerine göz önünde bulundurun.
* **vCPU kotası** -büyük bir küme veya bırden fazla MATLAB dağıtılmış bilgi işlem sunucusu kümesi dağıtmak Için vCPU kotasını artırmanız gerekebilir. Kotayı artırmak için, [çevrimiçi bir müşteri destek talebi](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) ücretsiz olarak açın.
* **MATLAB, paralel bilgi Işlem araç kutusu ve MATLAB dağıtılmış bilgi Işlem sunucusu lisansları** -betikler, [MathWorks barındırılan Lisans Yöneticisi](https://www.mathworks.com/help/install/license-management.html) 'nin tüm lisanslar için kullanıldığını varsayar.  
* **MATLAB dağıtılmış bilgi Işlem sunucusu yazılımı** -küme VM 'leri IÇIN temel VM görüntüsü olarak kullanılacak bir VM 'ye yüklenir.

## <a name="high-level-steps"></a>Üst düzey adımlar
MATLAB dağıtılmış bilgi Işlem sunucusu kümeleriniz için Azure sanal makinelerini kullanmak istiyorsanız, aşağıdaki üst düzey adımlar gereklidir. Ayrıntılı yönergeler, [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)'daki hızlı başlangıç şablonu ve betiklerine eşlik eden belgelerde yer alır.

1. **Temel VM görüntüsü oluşturma**  

   * Bu sanal makineye MATLAB dağıtılmış bilgi Işlem sunucusu yazılımını indirip yükleyin.

     > [!NOTE]
     > Bu işlem birkaç saat sürebilir, ancak bunu yalnızca kullandığınız her MATLAB sürümü için yapmanız yeterlidir.   
     >
     >
2. **Bir veya daha fazla küme oluşturun**  

   * Sağlanan PowerShell betiğini kullanın veya temel VM görüntüsünden bir küme oluşturmak için hızlı başlangıç şablonunu kullanın.   
   * Kümeleri listeetmenizi, duraklatmanızı, sürdürmeyi ve silmenizi sağlayan sağlanan PowerShell betiğini kullanarak kümeleri yönetin.

## <a name="cluster-configurations"></a>Küme yapılandırması
Şu anda, küme oluşturma betiği ve şablonu, tek bir MATLAB dağıtılmış bilgi Işlem sunucusu topolojisi oluşturmanıza imkan tanır. İsterseniz, bir veya daha fazla ek küme oluşturun, her kümeyle farklı sayıda çalışan sanal makinesi vardır, farklı VM boyutları ve bu şekilde devam eder.

### <a name="matlab-client-and-cluster-in-azure"></a>Azure 'da MATLAB istemcisi ve kümesi
MATLAB istemci düğümü, MATLAB Iş Zamanlayıcı düğümü ve MATLAB dağıtılmış bilgi Işlem sunucusu "Worker" düğümleri, aşağıdaki şekilde gösterildiği gibi bir sanal ağda Azure VM 'Ler olarak yapılandırılır.


* Kümeyi kullanmak için, uzak masaüstü ile istemci düğümüne bağlayın. İstemci düğümü, MATLAB istemcisini çalıştırır.
* İstemci düğümüne tüm çalışanlar tarafından erişilebilen bir dosya paylaşma vardır.
* MathWorks barındırılan Lisans Yöneticisi, tüm MATLAB yazılımları için lisans denetimleri için kullanılır.
* Varsayılan olarak, sanal CPU başına bir MATLAB dağıtılmış bilgi Işlem sunucusu çalışanı, çalışan VM 'lerde oluşturulur, ancak herhangi bir sayı belirtebilirsiniz.

## <a name="use-an-azure-based-cluster"></a>Azure tabanlı küme kullanma
Diğer tür MATLAB dağıtılmış bilgi Işlem sunucusu kümelerinde olduğu gibi, bir MATLAB Iş Zamanlayıcı kümesi profili oluşturmak için MATLAB istemcisinde (istemci VM 'de) küme profili yöneticisini kullanmanız gerekir.

![Küme profili Yöneticisi](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Sonraki adımlar
* Azure 'da MATLAB dağıtılmış bilgi Işlem sunucusu kümelerini dağıtma ve yönetme hakkında ayrıntılı yönergeler için, şablonları ve betikleri içeren [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) deposuna bakın.
* MATLAB ve MATLAB dağıtılmış bilgi Işlem sunucusu için ayrıntılı belgeler için [MathWorks sitesine](https://www.mathworks.com/) gidin.
