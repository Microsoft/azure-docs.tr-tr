---
title: Azure Arc'a genel bakış
description: Azure Arc 'ın ne olduğu ve müşterilerin karma kaynaklarını diğer Azure hizmetleri ve özellikleriyle yönetimi ve idare etmesine nasıl yardımcı olduğunu öğrenin.
ms.date: 03/02/2021
ms.topic: overview
ms.openlocfilehash: 33c9d6ca87c3d8d2d8920ff429902f5876bbdc59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650201"
---
# <a name="azure-arc-overview"></a>Azure Arc'a genel bakış

Günümüzde, şirketler giderek daha fazla karmaşık ortamları kontrol etmek ve yönetmek için zorluk duymaktadır. Bu ortamlar veri merkezleri, birden çok bulut ve kenar genelinde genişletilir. Her ortam ve bulut, öğrenmek ve çalıştırmak için ihtiyacınız olan ayrık yönetim araçları kümesine sahip olur.

Paralel olarak, mevcut araçlar yeni bulut Yerel desenleri için destek sağlayamayacağı için, yeni DevOps ve Ila operasyonel modeller uygulamak zordur.

Azure Arc, tutarlı bir çok bulut ve şirket içi yönetim platformu sunarak idare ve yönetimi basitleştirir. Azure Arc, mevcut kaynaklarınızı Azure Resource Manager bir şekilde yansıtırken tek bir cam bölmeden tüm ortamınızı yönetmenizi sağlar. Artık sanal makineleri, Kubernetes kümelerini ve veritabanlarını Azure 'da çalışıyor gibi yönetebilirsiniz. Nerede yaşdıklarından bağımsız olarak tanıdık Azure hizmetlerini ve yönetim yeteneklerini de kullanabilirsiniz. Azure Arc, ortamınızda yeni bulut Yerel düzenlerini desteklemek için DevOps uygulamalarına giriş yaparken geleneksel ıdaki 'leri kullanmaya devam etmenize olanak sağlar.

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Azure Arc yönetim denetim düzlemi diyagramı" border="false":::

Azure Arc bugün Azure dışında barındırılan aşağıdaki kaynak türlerini yönetmenizi sağlar:

* Sunucular-Windows veya Linux çalıştıran fiziksel ve sanal makineler.
* Kubernetes kümeleri-çoklu Kubernetes dağıtımlarını destekleme.
* Azure veri Hizmetleri-Azure SQL veritabanı ve PostgreSQL hiper ölçek Hizmetleri.

## <a name="what-does-azure-arc-deliver"></a>Azure Arc ne sunuyor?

Azure Arc 'ın temel özellikleri şunlardır:

* Ortamınız genelinde sunucularınız için tutarlı envanter, yönetim, idare ve güvenlik uygulayın.

* Azure [VM uzantılarını](./servers/manage-vm-extensions.md) , sunucularınızı izlemek, güvenli hale getirmek ve güncelleştirmek için Azure Yönetim Hizmetleri 'ni kullanacak şekilde yapılandırın.

* Kubernetes kümelerini ölçekte yönetin ve yönetir.

* Git depolarındaki bir veya daha fazla kümede yapılandırma dağıtmak için Gilar 'ı kullanın.

*  Azure Ilkesini kullanarak Kubernetes kümeleriniz için sıfır dokunma uyumluluğu ve yapılandırması.

* Azure Data Services 'ı Azure 'da (özellikle Azure SQL yönetilen örneği ve PostgreSQL için Azure veritabanı), yükseltme, güncelleştirmeler, güvenlik ve izleme gibi avantajlarla çalışan bir Kubernetes ortamında çalıştırın. Esnek ölçek kullanın ve Azure 'a sürekli bağlantı olmadan bile uygulama kapalı kalma süresi olmadan güncelleştirmeleri uygulayın

* Azure portal, Azure CLı, Azure PowerShell veya Azure REST API kullanıp kullanmayacağınızı, Azure Arc etkin kaynaklarınızı görüntüleyen birleştirilmiş bir deneyim.

## <a name="how-much-does-azure-arc-cost"></a>Azure yay maliyeti ne kadar sürer?

Aşağıda, Azure Arc ile sunulan özelliklerin fiyatlandırma ayrıntıları verilmiştir.

### <a name="arc-enabled-servers"></a>Arc özellikli sunucular

Aşağıdaki Azure Arc denetim düzlemi işlevselliği ek bir ücret ödemeden sunulmaktadır:

* Azure Yönetim grupları ve Etiketler aracılığıyla kaynak organizasyonu.

* Azure Kaynak Grafiği aracılığıyla arama ve dizin oluşturma.

* Azure RBAC ve abonelikleri aracılığıyla erişim ve güvenlik.

* Şablonlar ve uzantılar aracılığıyla ortamlar ve otomasyon.

* Güncelleştirme yönetimi

Azure Güvenlik Merkezi veya Azure Izleyici gibi Arc etkin sunucularda kullanılan tüm Azure Hizmetleri, söz konusu hizmet için fiyatlandırmaya göre ücretlendirilir. Daha fazla bilgi için bkz. [Azure fiyatlandırma sayfası](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc özellikli Kubernetes

Arc etkin Kubernetes (örneğin, Azure Güvenlik Merkezi veya Azure Izleyici) üzerinde kullanılan herhangi bir Azure hizmeti, söz konusu hizmet için fiyatlandırmaya göre ücretlendirilecektir. Azure Arc etkin Kubernetes 'in üzerine gelen yapılandırmaların fiyatlandırması hakkında daha fazla bilgi için bkz. [Azure fiyatlandırma sayfası](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-data-services"></a>Azure Arc özellikli veri hizmetleri

Geçerli önizleme aşamasında, Azure Arc etkin veri Hizmetleri ek bir maliyet olmaksızın sunulur.

## <a name="next-steps"></a>Sonraki adımlar

* Yay etkin sunucular hakkında daha fazla bilgi edinmek için aşağıdaki [genel bakışa](./servers/overview.md) bakın

* Arc etkin Kubernetes hakkında daha fazla bilgi edinmek için aşağıdaki [genel bakışa](./kubernetes/overview.md) bakın

* Arc etkin veri hizmetleri hakkında daha fazla bilgi edinmek için aşağıdaki [genel bakışa](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/) bakın

* Prototip etkin Hizmetleri, kavram kanıtından [atlama kanıtı](https://azurearcjumpstart.io/azure_arc_jumpstart/)
