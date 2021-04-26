---
title: Kubernetes için Azure Defender-avantajlar ve Özellikler
description: Kubernetes için Azure Defender 'ın avantajları ve özellikleri hakkında bilgi edinin.
author: memildin
ms.author: memildin
ms.date: 04/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c500c7b7afb36ffbe04fb63551c3a7d17c1347d9
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029089"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Kubernetes için Azure Defender 'a giriş

Kubernetes için Azure Defender, çalıştıkları yerde Kubernetes kümeleriniz için koruma sağlayan Azure Defender planındır. 

İçindeki kümeleri savunabilirsiniz:

- **Azure Kubernetes hizmeti (AKS)** -Microsoft 'un Kapsayıcılı uygulamalar geliştirmeye, dağıtmaya ve yönetmeye yönelik yönetilen hizmeti

- **Şirket içi ve çoklu bulut ortamları** - [yay etkin Kubernetes için uzantı](defender-for-kubernetes-azure-arc.md) kullanma

Azure Güvenlik Merkezi ve AKS, [Güvenlik Merkezi 'Nde kapsayıcı güvenliği](container-security.md)kapsamında özetlenen ortam sağlamlaştırma, iş yükü koruması ve çalışma zamanı koruması ile bulut Yerel bir Kubernetes güvenlik teklifi oluşturur.

Linux AKS düğümleriniz için ana bilgisayar düzeyinde tehdit algılama, sunucular ve onun Log Analytics Aracısı [Için Azure Defender](defender-for-servers-introduction.md) ' ı etkinleştirdiğinizde kullanılabilir. Ancak, kümeniz bir sanal makine ölçek kümesi üzerinde dağıtılmışsa, Log Analytics Aracısı Şu anda desteklenmemektedir.



## <a name="availability"></a>Kullanılabilirlik

|Görünüş|Ayrıntılar|
|----|:----|
|Yayın durumu:|Genel kullanılabilirlik (GA)|
|Fiyat|**Kubernetes Için Azure Defender** , [Güvenlik Merkezi fiyatlandırması](https://azure.microsoft.com/pricing/details/security-center/) 'nda gösterildiği gibi faturalandırılır|
|Gerekli roller ve izinler:|**Güvenlik Yöneticisi** , uyarıları kapatabilir.<br>**Güvenlik okuyucusu** bulguları görüntüleyebilir.|
|Larının|![Yes](./media/icons/yes-icon.png) Ticari bulutlar<br>![Yes](./media/icons/yes-icon.png) Ulusal/Sogeign (US Gov, Çin gov, diğer gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Kubernetes için Azure Defender 'ın yararları nelerdir?

Kubernetes için Azure Defender, kümelerinizin günlüklerini izleyerek **küme düzeyi tehdit koruması** sağlar.

Azure Defender 'ın Kubernetes izleyicilerine yönelik güvenlik olayları örnekleri, sunulan Kubernetes panoları, yüksek ayrıcalıklı rollerin oluşturulmasını ve hassas takmaları oluşturmayı içerir. Küme düzeyi uyarılarının tam listesi için, [Uyarı tablosuna](alerts-reference.md#alerts-akscluster)bakın.

> [!TIP]
> [Bu blog gönderisine](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)ait yönergeleri izleyerek kapsayıcı uyarılarının benzetimini yapabilirsiniz.

Ayrıca, güvenlik araştırmacıları küresel takımımız tehdidi sürekli olarak izler. Bulundukları gibi kapsayıcıya özgü uyarıları ve güvenlik açıklarını ekler.

>[!NOTE]
> Azure Defender, aboneliğinizde Kubernetes planı için Defender 'ı etkinleştirdikten sonra gerçekleşen eylemler ve dağıtımlar için güvenlik uyarıları oluşturur.




## <a name="azure-defender-for-kubernetes---faq"></a>Kubernetes için Azure Defender-SSS

### <a name="can-i-still-get-cluster-protections-without-the-log-analytics-agent"></a>Log Analytics Aracısı olmadan küme korumalarını almaya devam edebilir miyim?

**Kubernetes planı Için Azure Defender** , küme düzeyinde korumalar sağlar. Ayrıca, **sunucular Için Azure Defender**'ın Log Analytics aracısını dağıtırsanız, bu planla birlikte sunulan düğümleriniz için tehdit koruması alacaksınız. [Sunucular Için Azure Defender 'A giriş](defender-for-servers-introduction.md)hakkında daha fazla bilgi edinin.

Mümkün olan en iyi koruma için her ikisini de dağıtmanız önerilir.

Aracıyı konaklarınıza yüklememeyi seçerseniz, yalnızca tehdit koruması avantajları ve güvenlik uyarılarının bir alt kümesini alacaksınız. Hala kötü amaçlı sunucularla ağ analizi ve iletişimlerle ilgili uyarılar alacaksınız.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>AKS, AKS düğümlerine özel VM uzantıları yüklememe izin veriyor mu?
Azure Defender 'ın AKS düğümlerinizi izlemesi için Log Analytics aracısını çalıştırması gerekir. 

AKS yönetilen bir hizmettir ve Log Analytics Aracısı Microsoft tarafından yönetilen bir uzantıdır, ayrıca AKS kümelerinde de desteklenir.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Kümem zaten kapsayıcılar Aracısı için bir Azure Izleyici çalıştırıyorsa, Log Analytics aracısına da ihtiyacım var mı?
Azure Defender 'ın düğümlerinizi izlemesi için Log Analytics aracısını çalıştırması gerekir.

Kümeleriniz zaten kapsayıcı Aracısı için Azure Izleyicisini çalıştırıyorsa, Log Analytics aracısını da yükleyebilirsiniz ve iki aracı herhangi bir sorun olmadan başka bir şekilde çalışabilir.

[Kapsayıcılar Için Azure izleyici Aracısı hakkında daha fazla bilgi edinin](../azure-monitor/containers/container-insights-manage-agent.md).


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Kubernetes için Azure Defender dahil güvenlik merkezi 'nin Kubernetes koruması hakkında bilgi edindiniz. 

> [!div class="nextstepaction"]
> [Azure Defender’ı etkinleştirme](enable-azure-defender.md)

İlgili malzemeler için aşağıdaki makalelere bakın: 

- [Uyarıları bir SıEM, SOAR veya BT hizmet yönetimi çözümüne akış](export-to-siem.md)
- [Uyarıların başvuru tablosu](alerts-reference.md)
