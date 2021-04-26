---
title: Azure Arc etkin Kubernetes aracı mimarisi
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Bu makalede, Azure Arc etkin Kubernetes aracılarına yönelik mimari bir genel bakış sunulmaktadır
keywords: Kubernetes, yay, Azure, kapsayıcılar
ms.openlocfilehash: f59a897e4868d7b16d0a50c28ce2142320992f71
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106442550"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Azure Arc etkin Kubernetes aracı mimarisi

[Kubernetes](https://kubernetes.io/) , kendi kendine karma ve çok bulut ortamlarında Kapsayıcılı iş yüklerini tutarlı bir şekilde dağıtabilir. Ancak, Azure Arc etkin Kubernetes, heterojen ortamlarında ilke, idare ve güvenliği yöneten merkezi, tutarlı bir denetim düzlemi olarak çalışmaktadır. Bu makalede aşağıdakiler sunulmaktadır:

* Bir kümeyi Azure yaya bağlamaya yönelik mimari genel bakış.
* Bağlantı deseninin ardından aracılar gelir.
* Küme ortamı ve Azure arasında değiş tokuş edilen verilerin açıklaması.

## <a name="deploy-agents-to-your-cluster"></a>Aracıları kümenize dağıtma

Çoğu şirket içi veri merkezi, ağ sınır güvenlik duvarında gelen iletişimi önleyen katı ağ kuralları uygular. Azure Arc etkin Kubernetes, güvenlik duvarında gelen bağlantı noktalarına gerek duyulmadığından ve yalnızca giden iletişim için seçmeli çıkış uç noktalarını etkinleştirerek bu kısıtlamalarla birlikte çalışmaktadır. Azure Arc etkin Kubernetes aracıları bu giden iletişimi başlatır. 

![Mimariye genel bakış](./media/architectural-overview.png)

### <a name="connect-a-cluster-to-azure-arc"></a>Bir kümeyi Azure yaya bağlama

1. Altyapınızda bir Kubernetes kümesi oluşturun (VMware vSphere, Amazon Web Services, Google Cloud Platform, vb.). 

    > [!NOTE]
    > Azure Arc etkin Kubernetes Şu anda yalnızca mevcut Kubernetes kümelerini Azure yaya eklemeyi desteklediğinden, müşterilerin Kubernetes kümesinin yaşam döngüsünü oluşturması ve yönetmesi gerekir.  

1. Azure CLı kullanarak kümeniz için Azure Arc kaydını başlatın.
    * Azure CLı, aracı Held grafiğini kümeye dağıtmak için Held kullanır.
    * Küme düğümleri [Microsoft Container Registry](https://github.com/microsoft/containerregistry) giden bir iletişim başlatır ve ad alanında aşağıdaki aracıları oluşturmak için gereken görüntüleri çeker `azure-arc` :

        | Aracı | Açıklama |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Azure Arc etkin Kubernetes Şu anda yalnızca [sistem tarafından atanan kimlikleri](../../active-directory/managed-identities-azure-resources/overview.md)desteklemektedir. `clusteridentityoperator` ilk giden iletişimi başlatır. Bu ilk iletişim, diğer aracıların Azure ile iletişim kurmak için kullandığı Yönetilen Hizmet Kimliği (MSI) sertifikasını getirir. |
        | `deployment.apps/config-agent` | Kümeye uygulanan kaynak denetimi yapılandırma kaynakları için bağlı kümeyi izler. Uyumluluk durumunu güncelleştirir. |
        | `deployment.apps/controller-manager` | Azure Arc bileşenleri arasındaki etkileşimleri düzenleyen bir işleç operatörü. |    
        | `deployment.apps/metrics-agent` | En iyi performansı doğrulamak için diğer yay aracılarının ölçümlerini toplar. |
        | `deployment.apps/cluster-metadata-operator` | Küme sürümü, düğüm sayısı ve Azure Arc aracı sürümü gibi küme meta verilerini toplar. |
        | `deployment.apps/resource-sync-agent` | Yukarıda belirtilen küme meta verilerini Azure 'a eşitler. |
        | `deployment.apps/flux-logs-agent` | Kaynak denetimi yapılandırmasının bir parçası olarak dağıtılan Flox işleçlerinden günlükleri toplar. |
        | `deployment.apps/extension-manager` | Uzantı helb grafiklerinin yaşam döngüsünü yükleyip yönetir |  
        | `deployment.apps/clusterconnect-agent` | Küme bağlama özelliğinin kümeye erişim sağlamasına olanak tanıyan ters proxy aracısı `apiserver` . Bu, yalnızca `cluster-connect` küme üzerinde etkin özelliği etkinse dağıtılan isteğe bağlı bir bileşendir   |
        | `deployment.apps/guard` | AAD RBAC özelliği için kullanılan kimlik doğrulaması ve yetkilendirme Web kancası sunucusu. Bu, yalnızca `azure-rbac` küme üzerinde etkin özelliği etkinse dağıtılan isteğe bağlı bir bileşendir   |

1. Tüm Azure yayı etkin Kubernetes Aracısı Pod 'nin durumu olduktan sonra `Running` , kümenizin Azure yaya bağlandığını doğrulayın. Şunları görmeniz gerekir:
    * [Azure Resource Manager](../../azure-resource-manager/management/overview.md)' de bir Azure Arc etkin Kubernetes kaynağı. Azure, bu kaynağı gerçek Kubernetes kümesinin kendisini değil, müşteri tarafından yönetilen Kubernetes kümesinin bir projeksiyonu olarak izler.
    * Küme meta verileri (Kubernetes sürümü, aracı sürümü ve düğüm sayısı gibi), Azure Arc etkinleştirilmiş Kubernetes kaynağında meta veri olarak görünür.

## <a name="data-exchange-between-cluster-environment-and-azure"></a>Küme ortamı ve Azure arasında veri değişimi

| Veri türü | Senaryo | İletişim modu |
| --------- | -------- | ------------------ |
| Kubernetes kümesi sürümü | Küme meta verileri | Aracı Azure 'a gönderim |
| Kümedeki düğümlerin sayısı | Küme meta verileri | Aracı Azure 'a gönderim |
| Aracı sürümü | Küme meta verileri | Aracı Azure 'a gönderim |
| Kubernetes dağıtım türü | Küme meta verileri | Azure CLı, Azure 'a gönderim |
| Altyapı türü (AWS/GCP/vSphere/...) | Küme meta verileri | Azure CLı, Azure 'a gönderim |
| kümedeki düğümlerin vCPU sayısı | Faturalandırma | Azure CLı, Azure 'a gönderim |
| Aracı sinyali | Kaynak Durumu | Aracı Azure 'a gönderim |
| Aracılara göre kaynak tüketimi (bellek/CPU) | Tanılama ve desteklenebilirlik | Aracı Azure 'a gönderim |
| Tüm aracı kapsayıcıları günlükleri | Tanılama ve desteklenebilirlik | Aracı Azure 'a gönderim |
| Aracı yükseltme kullanılabilirliği | Aracı yükseltme | Aracı Azure 'dan çeker |
| İstenen yapılandırma durumu: git deposu URL 'SI, Flox işleci parametreleri, özel anahtar, bilinen ana bilgisayar içeriği, HTTPS Kullanıcı adı, belirteç veya parola | Yapılandırma | Aracı Azure 'dan çeker |
| Flox operatörü yüklemesinin durumu | Yapılandırma | Aracı Azure 'a gönderim |
| Küme içinde Gatekeeper zorlanması gereken Azure Ilke atamaları | Azure İlkesi | Aracı Azure 'dan çeker |
| Küme içi ilke için denetim ve uyumluluk durumu | Azure İlkesi | Aracı Azure 'a gönderim |
| Müşteri iş yüklerinin ölçümleri ve günlükleri | Azure İzleyici | Aracı, müşterinin kiracısındaki ve aboneliğindeki Log Analytics çalışma alanı kaynağına gönderim |

## <a name="connectivity-status"></a>Bağlantı durumu

| Durum | Açıklama |
| ------ | ----------- |
| Bağlanmada | Azure Arc etkin Kubernetes kaynağı Azure Resource Manager oluşturuldu, ancak hizmet aracı sinyalini henüz almadı. |
| Bağlı | Azure Arc etkin Kubernetes hizmeti, önceki 15 dakikadan kısa bir süre içinde bir aracı sinyali aldı. |
| Çevrimdışı | Azure Arc etkin Kubernetes kaynağı daha önce bağlandı, ancak hizmet, 15 dakika boyunca aracı sinyali almadı. |
| Süresi doldu | MSI sertifikası, verildikten sonra 90 günlük bir süre sonu penceresine sahiptir. Bu sertifikanın süresi dolduktan sonra kaynak kabul edilir `Expired` ve yapılandırma, izleme ve ilke gibi tüm özellikler bu kümede çalışmayı durdurur. Kullanım dışı Azure Arc etkin Kubernetes kaynaklarını ele alma hakkında daha fazla bilgi için [SSS makalesinde](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources)bulabilirsiniz. |

## <a name="understand-connectivity-modes"></a>Bağlantı modlarını anlama

| Bağlantı modu | Açıklama |
| ----------------- | ----------- |
| Tam bağlantı | Aracılar, Gilar yapılandırmalarının yaymasına, Azure Ilke ve Gatekeeper ilkelerine zorlamaya ve Azure Izleyici 'de iş yükü ölçümlerinin ve günlüklerinin toplanmasını çok kısa bir gecikmeyle Azure ile sürekli olarak iletişim kurabilir. |
| Yarı bağlı | Tarafından çekilecek MSI sertifikası, `clusteridentityoperator` sertifikanın süresi dolmadan 90 gün önce geçerlidir. Süre dolduktan sonra, Azure Arc etkin Kubernetes kaynağı çalışmayı durduruyor. Kümedeki tüm Azure Arc özelliklerini yeniden etkinleştirmek için, Azure Arc etkin Kubernetes kaynak ve aracılarını silin ve yeniden oluşturun. 90 gün boyunca, kümeyi en az 30 günde bir bağlayın. |
| Bağlantı kesildi | Bağlantısı kesilen ortamlardaki Kubernetes kümeleri, Azure 'a erişilemiyor Kubernetes tarafından şu anda desteklenmiyor. Bu yetenek sizi ilgilendiği takdirde, [Azure Arc UserVoice forumuna](https://feedback.azure.com/forums/925690-azure-arc)bir fikir gönderin veya bu özelliği kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [Bir Kubernetes kümesini Azure yaya bağlamak](./quickstart-connect-cluster.md)için hızlı başlangıç deneyimimizi gözden geçir.
* [Azure Arc etkinleştirilmiş Kubernetes ile bir yapılandırma kaynağı](./conceptual-configurations.md)olarak kümeniz ve git deposu arasında bağlantı oluşturma hakkında daha fazla bilgi edinin.