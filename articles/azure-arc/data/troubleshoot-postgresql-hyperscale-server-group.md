---
title: PostgreSQL hiper ölçek sunucu grupları sorunlarını giderme
description: Jupyter Notebook ile PostgreSQL hiper ölçek sunucu grupları sorunlarını giderme
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: caaab07200a8631935a2b5d5368a0c16ea9a60c5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "92320224"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>PostgreSQL hiper ölçek sunucu grupları sorunlarını giderme
Bu makalede, sunucu grubunuzun sorunlarını gidermek için kullanabileceğiniz bazı teknikler açıklanmaktadır. Bu makaleye ek olarak, sunucu grubunuz hakkında ölçümleri görselleştirmek için [kibana](monitor-grafana-kibana.md) kullanarak günlükleri nasıl arayacağını veya [Grafana](monitor-grafana-kibana.md) kullanacağınızı okumak isteyebilirsiniz. 

## <a name="getting-more-details-about-the-execution-of-an-azdata-command"></a>Azdata komutunun yürütülmesi hakkında daha fazla ayrıntı alma
Yürütmeniz için **--Debug** parametresini ekleyebilirsiniz. Bunun yapılması, konsolunuza bu komutun yürütülmesi hakkında ek bilgiler gösterilir. Bu komutun davranışını anlamanıza yardımcı olacak ayrıntıları almak için yararlı bulacaksınız.
Örneğin,
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

veya
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

Ayrıca, belirli bir komutun bazı yardım ve parametre listesini göstermek için herhangi bir azdata komutunda Help parametresini kullanabilirsiniz. Örnek:
```console
azdata arc postgres server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>Veri denetleyicisinin ve sunucu gruplarınızın günlüklerini toplama
[Azure Arc etkin veri Hizmetleri için günlükleri alma](troubleshooting-get-logs.md) hakkında makaleyi okuyun



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Azure Data Studio 'de jupi Notebook ile etkileşimli sorun giderme
Not defterleri, ne yapılacağını/nasıl yapılacağını açıklamaya yönelik markdown içeriğini ekleyerek yordamları belgeleyebilir. Ayrıca yordamı otomatikleştirmek için yürütülebilir kod da sağlayabilir.  Bu model, standart işletim yordamlarında sorun giderme kılavuzlarına kadar her şeyde yararlı olur.

Örneğin, Azure Data Studio kullanarak bazı sorunlar olabilecek bir PostgreSQL hiper ölçek sunucu grubunun sorunlarını giderelim.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### <a name="install-tools"></a>Araçları yükleme

`kubectl` [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Azure Data Studio ' de not defterini çalıştırmak için kullandığınız istemci makineye Azure Data Studio ve ' yi yükleyebilirsiniz. Bunu yapmak için lütfen [istemci araçları 'Nı yüklemek](install-client-tools.md) üzere yönergeleri izleyin

### <a name="update-the-path-environment-variable"></a>PATH ortam değişkenini güncelleştirme

Bu araçların bu istemci makinede herhangi bir yerden çağrılabileceğini doğrulayın. Örneğin, bir Windows istemci makinesinde, yol sistemi ortam değişkenini güncelleştirin ve kubectl 'yi yüklediğiniz klasörü ekleyin.

### <a name="sign-in-with-azure-data-cli-azdata"></a>İle oturum açın [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

Arc veri denetleyicinizde bu istemci makinesinden ve Azure Data Studio başlamadan önce oturum açın. Bunu yapmak için aşağıdaki gibi bir komut çalıştırın:

```console
azdata login --endpoint https://<IP address>:<port>
```

`<IP address>`Kubernetes KÜMENIZIN IP adresiyle ve `<port>` Kubernetes 'in dinlediği bağlantı noktası ile değiştirin. Sizden Kullanıcı adı ve parola istenir. Daha fazla ayrıntı görmek için şunu çalıştırın: _

```console
azdata login --help
```

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Kubectl ile Kubernetes kümenizde oturum açma

Bunu yapmak için, [Bu](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) blog gönderisine sunulan örnek komutları kullanmak isteyebilirsiniz.
Şunun gibi komutları çalıştırın:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>Sorun giderme Not defteri

Azure Data Studio başlatın ve sorun giderme Not defterini açın. 

[033-Manage-Postgres-with-AzureDataStudio.MD](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) ' de açıklanan adımları uygulayın:

1. Arc veri denetleyicinize bağlanma
2. Postgres örneğinizi sağ seçin ve **[Yönet]** öğesini seçin
3. **[Sorunları Tanıla ve çöz] panosunu** seçin
4. **[Sorun gider] bağlantısını** seçin

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio-PostgreSQL sorun giderme Not defteri":::

**TSG100-Azure Arc etkin PostgreSQL hiper ölçek sorun giderici Not defteri** açılır: :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio-PostgreSQL sorun giderme Not defterini kullanma":::

#### <a name="run-the-scripts"></a>Betikleri çalıştırma
Not defterini tek seferde yürütmek için en üstteki ' Tümünü Çalıştır ' düğmesini seçin veya her bir kod hücresini birer birer seçerek yürütün.

Olası sorunlar için kod hücrelerinin yürütülmesindeki çıktıyı görüntüleyin.

Yaygın sorunların nasıl tanınabileceği ve nasıl çözülecek hakkında zaman içindeki not defterine daha fazla ayrıntı ekleyeceğiz.

## <a name="next-step"></a>Sonraki adım
- [Azure Arc etkin veri Hizmetleri için günlükleri alma](troubleshooting-get-logs.md) hakkında bilgi edinin
- [Kibana ile günlükleri arama](monitor-grafana-kibana.md) hakkında bilgi edinin
- [Grafana ile izleme](monitor-grafana-kibana.md) hakkında bilgi edinin
- Kendi not defterlerinizi oluşturma
