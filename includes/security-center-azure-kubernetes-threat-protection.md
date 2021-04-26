---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 04/07/2021
ms.topic: include
ms.openlocfilehash: 73886b966676af75cc74484ccdc0632f080b041a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029154"
---
Azure Defender, Kapsayıcılı ortamlarınız için gerçek zamanlı tehdit koruması sağlar ve şüpheli etkinlikler için uyarı oluşturur. Bu bilgileri kullanarak güvenlik sorunlarını hızlı bir şekilde çözebilir ve kapsayıcılarınızın güvenlik düzeyini artırabilirsiniz.

Azure Defender, farklı düzeylerde tehdit koruması sağlar: 

* **Konak düzeyi (sunucular Için Azure Defender tarafından verilmiştir)** -Güvenlik Merkezi 'Nin diğer VM 'lerde kullandığı aynı Log Analytics aracısını kullanarak, Azure Defender, Linux Kubernetes düğümlerinizi Web kabuğu algılama ve BILINEN şüpheli IP adresleriyle bağlantı gibi şüpheli etkinlikler için izler. Aracı Ayrıca, ayrıcalıklı kapsayıcı oluşturma, API sunucularına şüpheli erişim ve bir Docker kapsayıcısı içinde çalışan Secure Shell (SSH) sunucuları gibi kapsayıcıya özel analizler için de izler.

    Aracıları konaklarınıza yüklememeyi seçerseniz, yalnızca tehdit koruması avantajları ve güvenlik uyarılarının bir alt kümesini alırsınız. Hala kötü amaçlı sunucularla ağ analizi ve iletişimlerle ilgili uyarılar alacaksınız.

    >[!IMPORTANT]
    > Şu anda sanal makine ölçek kümelerinde çalışan Azure Kubernetes hizmet kümelerine Log Analytics aracısının yüklenmesini desteklemiyoruz.

    Küme düzeyi uyarılarının bir listesi için, [Uyarı tablosuna](../articles/security-center/alerts-reference.md#alerts-containerhost)bakın.


* **Küme düzeyi (Kubernetes Için Azure Defender tarafından sağlanır)** -küme düzeyinde tehdit koruması, Kubernetes 'ın denetim günlüklerini çözümlemeye dayanır. Bu **aracısız** izlemeyi etkinleştirmek Için Azure Defender 'ı etkinleştirin. Kümeniz şirket içinde veya başka bir bulut sağlayıcısında ise, [Arc etkin Kubernetes ve Azure Defender uzantısını](../articles/security-center/defender-for-kubernetes-azure-arc.md)etkinleştirin.

    Bu düzeyde uyarı oluşturmak için, Azure Defender kümelerinizin günlüklerini izler. Bu düzeydeki olay örnekleri, sunulan Kubernetes panoları, yüksek ayrıcalıklı rollerin oluşturulmasını ve hassas takmaları oluşturmayı içerir.

    >[!NOTE]
    > Azure Defender, aboneliğinizde Kubernetes planı için Defender 'ı etkinleştirdikten sonra gerçekleşen eylemler ve dağıtımlar için güvenlik uyarıları oluşturur. 

    Küme düzeyi uyarılarının bir listesi için, [Uyarı tablosuna](../articles/security-center/alerts-reference.md#alerts-akscluster)bakın.

Ayrıca, güvenlik araştırmacıları küresel takımımız tehdidi sürekli olarak izler. Bulundukları gibi kapsayıcıya özgü uyarıları ve güvenlik açıklarını ekler.

> [!TIP]
> [Bu blog gönderisine](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)ait yönergeleri izleyerek kapsayıcı uyarılarının benzetimini yapabilirsiniz.