---
title: Azure Service Fabric Explorer kullanarak kümenizi görselleştirme | Microsoft Docs
description: Service Fabric Explorer, bir Microsoft Azure Service Fabric kümesindeki bulut uygulamalarını ve düğümlerini incelemek ve yönetmek için bir uygulamadır.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 9b873b5a68979b8225c44c32e0b9494408e35ac1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927173"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Service Fabric Explorer ile kümenizi görselleştirme

Service Fabric Explorer (SFX), Azure Service Fabric kümelerini İnceleme ve yönetmeye yönelik açık kaynaklı bir araçtır. Service Fabric Explorer, Windows, macOS ve Linux için bir masaüstü uygulamasıdır.

## <a name="service-fabric-explorer-download"></a>Service Fabric Explorer indir

Service Fabric Explorer Masaüstü uygulaması olarak indirmek için aşağıdaki bağlantıları kullanın:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Service Fabric Explorer Masaüstü sürümü küme desteğiyle daha fazla veya daha az özelliğe sahip olabilir. Tam özellik uyumluluğunu sağlamak için kümeye dağıtılan Service Fabric Explorer sürüme geri dönebilirsiniz.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Kümeden Service Fabric Explorer çalıştırma

Service Fabric Explorer, bir Service Fabric kümesinin HTTP yönetim uç noktasında da barındırılır. SFX 'i bir Web tarayıcısında başlatmak için, herhangi bir tarayıcıdan kümenin HTTP yönetim uç noktasına gidin; örneğin, https:\//clusterFQDN: 19080.

Geliştirici iş istasyonu kurulumu için https://localhost:19080/Explorergiderek yerel kümenizde Service Fabric Explorer başlatabilirsiniz. [Geliştirme ortamınızı hazırlamak](service-fabric-get-started.md)için bu makaleye bakın.

> [!NOTE]
> Kümenizin otomatik olarak imzalanan bir sertifikayla güvenliği varsa, Web tarayıcısından "Bu site güvenli değil" bir hata iletisi alırsınız. Yalnızca, uyarı geçersiz kılarak en modern web tarayıcıları üzerinde ilerleyebilirsiniz. Bir üretim ortamında, kümenizin ortak ad ve sertifika yetkilisi tarafından verilen bir sertifika kullanılarak güvenliği sağlanmalıdır. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Service Fabric kümesine bağlanma
Bir Service Fabric kümesine bağlanmak için kümeler yönetim uç noktası (FQDN/IP) ve HTTP yönetim uç noktası bağlantı noktası (varsayılan olarak 19080) gereklidir. Örneğin, https\://mysfcluster.westus.cloudapp.azure.com:19080. İş istasyonunuzda yerel bir kümeye bağlanmak için "localhost 'a Bağlan" onay kutusunu kullanın.

### <a name="connect-to-a-secure-cluster"></a>Güvenli bir kümeye bağlanma
Service Fabric kümenize, sertifikalarla veya Azure Active Directory (AAD) kullanarak istemci erişimini denetleyebilirsiniz.

Güvenli bir kümeye bağlanmaya çalışırsanız ve sonra kümenin yapılandırmasına bağlı olarak, bir istemci sertifikası sunabilir veya AAD kullanarak oturum açmanız gerekecektir.

## <a name="understand-the-service-fabric-explorer-layout"></a>Service Fabric Explorer yerleşimini anlayın
Sol taraftaki ağacı kullanarak Service Fabric Explorer gezinebilirsiniz. Ağacın kökünde, küme panosu, uygulamanızın ve düğüm durumunun özeti dahil olmak üzere kümenize genel bir bakış sağlar.

![Service Fabric Explorer kümesi panosu][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Kümenin yerleşimini görüntüleme
Bir Service Fabric kümesindeki düğümler, hata etki alanları ve yükseltme etki alanları olmak üzere iki boyutlu bir kılavuza yerleştirilir. Bu yerleştirme, uygulamalarınızın donanım hatalarının ve Uygulama yükseltmelerinde kullanılabilir kalmasını sağlar. Küme eşlemesini kullanarak geçerli kümenin nasıl düzenlendiğini görebilirsiniz.

![Küme eşlemesi Service Fabric Explorer][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Uygulamaları ve hizmetleri görüntüle
Küme iki alt ağaç içerir: bir uygulamalar ve düğümler için bir diğeri.

Service Fabric mantıksal hiyerarşisinde gezinmek için uygulama görünümünü kullanabilirsiniz: uygulamalar, hizmetler, bölümler ve çoğaltmalar.

Aşağıdaki örnekte, **MyApp** uygulaması, **Mystatefulservice** ve **WebService**olmak üzere iki hizmetlerden oluşur. **Mystatefulservice** durum bilgisi olduğundan, bir birincil ve iki ikincil çoğaltmaya sahip bir bölüm içerir. Buna karşılık, WebSvcService durumsuz ve tek bir örnek içerir.

![Service Fabric Explorer uygulama görünümü][sfx-application-tree]

Ağacın her düzeyinde ana bölmede öğeyle ilgili ilgili bilgiler gösterilir. Örneğin, belirli bir hizmet için sistem durumu ve sürümünü görebilirsiniz.

![Service Fabric Explorer Essentials bölmesi][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Kümenin düğümlerini görüntüleme
Düğüm görünümü, kümenin fiziksel düzenini gösterir. Belirli bir düğümde, hangi uygulamalara kod dağıtıldığını denetleyebilirsiniz. Daha açık olarak, burada hangi çoğaltmaların çalıştığını görebilirsiniz.

## <a name="actions"></a>Eylemler
Service Fabric Explorer, kümenizin içindeki düğümlerde, uygulamalarda ve hizmetlerde eylemler çağırma için hızlı bir yol sunar.

Örneğin, bir uygulama örneğini silmek için soldaki ağaçtan uygulamayı seçin ve ardından **Uygulama sil** > **Eylemler** ' i seçin.

![Service Fabric Explorer bir uygulamayı silme][sfx-delete-application]

> [!TIP]
> Her öğenin yanındaki üç nokta simgesine tıklayarak aynı eylemleri gerçekleştirebilirsiniz.
>
> Service Fabric Explorer aracılığıyla gerçekleştirilebilecek her eylem, Otomasyonu etkinleştirmek için PowerShell veya REST API aracılığıyla da gerçekleştirilebilir.
>
>

Ayrıca, belirli bir uygulama türü ve sürümü için uygulama örnekleri oluşturmak üzere Service Fabric Explorer de kullanabilirsiniz. Ağaç görünümünde uygulama türü ' nü seçin, ardından sağ bölmede istediğiniz sürümün yanındaki **uygulama örneği oluştur** bağlantısına tıklayın.

![Service Fabric Explorer bir uygulama örneği oluşturma][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer, uygulama örnekleri oluşturulurken parametreleri desteklemez. Uygulama örnekleri varsayılan parametre değerlerini kullanır.
>
>

## <a name="event-store"></a>Olay deposu
EventStore, Service Fabric Explorer ve REST API aracılığıyla kullanılabilir Service Fabric platform olayları sağlayan platform tarafından sunulan bir özelliktir. Düğüm, hizmet, uygulama ve sorgu gibi her bir varlık için kümenizde neler olduğunu bir anlık görüntü görünümünü görebilirsiniz. Eventstore hakkında daha fazla bilgi için de [Eventstore 'A genel bakış konusuna](service-fabric-diagnostics-eventstore.md)bakabilirsiniz.   

![EventStore][sfx-eventstore]

>[!NOTE]
>Service Fabric sürümünden itibaren 6,4. EventStore varsayılan olarak etkinleştirilmemiştir ve Resource Manager şablonunda etkinleştirilmelidir

>[!NOTE]
>Service Fabric sürümünden itibaren 6,4. EventStore API 'Leri yalnızca yalnızca Azure üzerinde çalışan Windows kümelerinde kullanılabilir. Tek başına kümelerimizin yanı sıra bu işlevselliği Linux 'a taşıma konusunda çalışıyoruz.

## <a name="image-store-viewer"></a>Görüntü Deposu Görüntüleyici
Görüntü deposu Görüntüleyicisi, görüntü deposunun geçerli içeriğini görüntülemeye ve dosya ve klasör bilgilerini almaya izin veren yerel Görüntü Deposu kullanılıyorsa sunulan bir özelliktir.

![Küme eşlemesi Service Fabric Explorer][sfx-imagestore]

## <a name="backup-and-restore"></a>Yedekleme ve Geri Yükleme
Service Fabric Explorer [yedekleme ve geri yükleme](./service-fabric-reliable-services-backup-restore.md)ile arabirim özelliği sunar. SFX içindeki yedekleme ve geri yükleme özelliklerini görmek için Gelişmiş mod etkinleştirilmelidir.

![Gelişmiş modu etkinleştir][0]
 
Aşağıdaki işlemler mümkündür:

* Yedekleme Ilkesi oluşturun, düzenleyin ve silin.
* Bir uygulama, hizmet veya bölüm için yedeklemeyi etkinleştirin ve devre dışı bırakın.
* Uygulama, hizmet veya bölüm için yedeklemeyi askıya alma ve yeniden başlatma.
* Bölüm yedeklemesini tetikleme ve izleme.
* Bölüm için geri yüklemeyi tetikleme ve izleme.

Yedekleme ve geri yükleme hizmeti hakkında daha fazla bilgi için [REST API başvurusuna](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)bakın.
## <a name="next-steps"></a>Sonraki adımlar
* [Visual Studio 'da Service Fabric uygulamalarınızı yönetme](service-fabric-manage-application-in-visual-studio.md)
* [PowerShell kullanarak uygulama dağıtımını Service Fabric](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-dashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-map.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/sfx-application-tree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/sfx-service-essentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/sfx-delete-application.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/sfx-create-app-instance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
[sfx-imagestore]: ./media/service-fabric-visualizing-your-cluster/sfx-image-store.png
[0]: ./media/service-fabric-backuprestoreservice/advanced-mode.png