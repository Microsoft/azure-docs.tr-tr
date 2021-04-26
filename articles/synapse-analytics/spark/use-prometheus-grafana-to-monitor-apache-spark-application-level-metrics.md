---
title: Öğretici-Prometheus ve Grafana ile uygulama düzeyi ölçümlerini Izleme Apache Spark
description: Öğretici-Azure Kubernetes Service (AKS) kümesine Apache Spark uygulama ölçümleri çözümünü dağıtmayı ve Grafana panolarını tümleştirmeyi öğrenin.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: 6a0b63dc7fda25e3911ae713a0bea7ae7a0969f9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602307"
---
# <a name="tutorial-monitor-apache-spark-application-level-metrics-with-prometheus-and-grafana"></a>Öğretici: Prometheus ve Grafana ile uygulama düzeyi ölçümlerini Izleme Apache Spark

## <a name="overview"></a>Genel Bakış

Bu öğreticide Azure Kubernetes Service (AKS) kümesine Apache Spark uygulama ölçümleri çözümünü dağıtmayı öğrenecek ve Grafana panolarını tümleştirmeyi öğreneceksiniz.

Gerçek zamanlı Apache Spark ölçüm verilerini toplamak ve sorgulamak için bu çözümü kullanabilirsiniz. Tümleşik Grafana panoları Apache Spark uygulamanızı tanılamanıza ve izlemenize olanak tanır. Kaynak kodu ve Konfigürasyonlar GitHub üzerinde açıktır.

## <a name="prerequisites"></a>Önkoşullar

1.  [Azure CLI](/cli/azure/install-azure-cli)
2.  [Helb istemcisi 3.30 +](https://github.com/helm/helm/releases)
3.  [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
4.  [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/en-us/services/kubernetes-service/)

Veya Azure CLı, helk istemcisi ve kubectl 'yi kutudan zaten içeren [Azure Cloud Shell](https://shell.azure.com/)kullanın.

## <a name="log-in-to-azure"></a>Azure'da oturum açma

```bash
az login
az account set --subscription "<subscription_id>"
```

## <a name="create-an-azure-kubernetes-service-instance-aks"></a>Azure Kubernetes hizmet örneği (AKS) oluşturma

Aboneliğinizde bir Kubernetes kümesi oluşturmak için Azure CLı komutunu kullanın.

```
az aks create --name <kubernetes_name> --resource-group <kubernetes_resource_group> --location <location> --node-vm-size Standard_D2s_v3
az aks get-credentials --name <kubernetes_name> --resource-group <kubernetes_resource_group>
```

Note: bir AKS kümeniz zaten varsa, bu adım atlanabilir.

## <a name="create-a-service-principal-and-grant-permission-to-synapse-workspace"></a>Hizmet sorumlusu oluşturma ve SYNAPSE çalışma alanına izin verme

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Sonuç şöyle görünmelidir:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

AppID, parola ve Tenantıd ' yi aklınızda edin.

[![ekran görüntüsü izin ver srtzya](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png#lightbox)

1. [Azure SYNAPSE Analytics çalışma alanınızda](https://web.azuresynapse.net/) SYNAPSE Administrator olarak oturum açın

2. SYNAPSE Studio 'da, sol taraftaki bölmede **yönet > erişim denetimi** ' ni seçin.

3. **Rol ataması eklemek** için sol üstteki Ekle düğmesine tıklayın

4. Kapsam için **çalışma alanı** ' nı seçin.

5. Rol için **SYNAPSE Işlem işletmeni** seçin

6. Kullanıcı Seç için **<service_principal_name>** girin ve hizmet sorumlusuna tıklayın

7. **Uygula** ' ya tıklayın (iznin etkili olması için 3 dakika bekleyin.)

## <a name="install-connector-prometheus-server-grafana-dashboard"></a>Install Connector, Prometheus Server, Grafana Dashboard

1. Hele istemcisine SYNAPSE-grafik deposu ekleyin.

```bash
helm repo add synapse-charts https://github.com/microsoft/azure-synapse-spark-metrics/releases/download/helm-chart
```

2.  Held istemcisi aracılığıyla bileşenleri yükler:

```bash
helm install spo synapse-charts/synapse-prometheus-operator --create-namespace --namespace spo \
    --set synapse.workspaces[0].workspace_name="<workspace_name>" \
    --set synapse.workspaces[0].tenant_id="<tenant_id>" \
    --set synapse.workspaces[0].service_principal_name="<service_principal_app_id>" \
    --set synapse.workspaces[0].service_principal_password="<service_principal_password>" \
    --set synapse.workspaces[0].subscription_id="<subscription_id>" \
    --set synapse.workspaces[0].resource_group="<workspace_resource_group_name>"
```

 - workspace_name: SYNAPSE çalışma alanı adı.
 - subscription_id: SYNAPSE çalışma alanı abonelik KIMLIĞI.
 - workspace_resource_group_name: SYNAPSE çalışma alanı kaynak grubu adı.
 - tenant_id: SYNAPSE Workspace kiracı KIMLIĞI.
 - service_principal_app_id: hizmet sorumlusu "AppID"
 - service_principal_password: oluşturduğunuz hizmet sorumlusu parolası.

## <a name="log-in-to-grafana"></a>Grafana 'de oturum aç

Varsayılan Grafana parolasını ve adresini alın. Grafana Settings ' de parolayı değiştirebilirsiniz.

```bash
kubectl get secret --namespace spo spo-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl -n spo get svc spo-grafana
```

Hizmet IP 'si alın &, dış IP 'yi tarayıcıya yapıştırın ve "admin" Kullanıcı adı ve parolasıyla oturum açın.

## <a name="use-grafana-dashboards"></a>Grafana panoları kullanma

Grafana sayfasının sol üst köşesinde bulunan SYNAPSE panosunu bulun (Home-> SYNAPSE Workspace/SYNAPSE uygulaması), SYNAPSE Studio 'da örnek bir kod çalıştırmayı deneyin ve ölçümler çekme için birkaç saniye bekleyin.

Ayrıca, çalışma alanınıza ve Apache Spark havuzlarınıza genel bir bakış almak için "SYNAPSE Workspace/Workspace" ve "SYNAPSE Workspace/Spark havuzları" panoları kullanabilirsiniz.

## <a name="uninstall"></a>Kaldır

Aşağıdaki gibi, Held komutuna göre bileşenleri kaldırın.

```bash
helm delete <release_name> -n <namespace>
```

AKS kümesini silin.

```bash
az aks delete --name <kubernetes_cluster_name> --resource-group <kubernetes_cluster_rg>
```

## <a name="components-introduction"></a>Bileşenlere giriş

Azure SYNAPSE Analytics, Prometheus operatörü ve SYNAPSE Prometheus bağlayıcısını temel alan bir [Held grafiği](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm) sağlar. Helb grafiği, uygulama düzeyi ölçümlerinin Apache Spark için Prometheus Server, Grafana Server ve Grafana panoları içerir. Bu ölçümleri neredeyse gerçek zamanlı olarak toplamak ve görselleştirme için [Grafana](https://github.com/grafana/grafana) kullanmak üzere popüler bir açık kaynaklı izleme sistemi olan [Prometheus](https://prometheus.io/)'ı kullanabilirsiniz.

### <a name="synapse-prometheus-connector"></a>SYNAPSE Prometheus Bağlayıcısı

SYNAPSE Prometheus Bağlayıcısı, Azure SYNAPSE Apache Spark havuzunu ve Prometheus sunucunuzu bağlamaya yardımcı olur. Şunları uygular:

1.  Kimlik doğrulaması: AAD tabanlı kimlik doğrulamasıdır ve uygulama bulma, ölçüm alımı ve diğer işlevler için hizmet sorumlusunun AAD belirtecini otomatik olarak yenileyebilirler.
2.  Uygulama bulmayı Apache Spark: hedef çalışma alanında uygulamalar gönderdiğinizde, SYNAPSE Prometheus Bağlayıcısı bu uygulamaları otomatik olarak bulabilir.
3.  Uygulama meta verileri Apache Spark: temel uygulama bilgilerini toplayıp verileri temin etmek için dışa aktarır.

SYNAPSE Prometheus Bağlayıcısı, [Microsoft Container Registry](https://github.com/microsoft/containerregistry)'de barındırılan bir Docker görüntüsü olarak yayımlanmıştır. Açık kaynaktır ve [Azure SYNAPSE Spark uygulama ölçümlerinde](https://github.com/microsoft/azure-synapse-spark-metrics)bulunur.

### <a name="prometheus-server"></a>Prometheus sunucusu

Prometheus, açık kaynaklı bir izleme ve uyarı araç setidir. Bulut Yerel Bilgi Işlem altyapısı 'ndan (CNCF) derecelendirilmiş ve bulutta yerel izleme için standart bir standart haline geldi. Prometheus, büyük miktarlarda zaman serisi verisi toplamamıza, sorgumıza ve depolamanıza yardımcı olabilir ve Grafana ile kolayca tümleştirilebilir. Bu çözümde, helk grafiğine göre Prometheus bileşenini dağıyoruz.

### <a name="grafana-and-dashboards"></a>Grafana ve panolar

Grafana açık kaynak görselleştirme ve analiz yazılımıdır. Ölçümleri sorgulamanızı, görselleştirmenizi, uyarıları görmenizi ve araştırmanızı sağlar. Azure SYNAPSE Analytics, uygulama düzeyi ölçümlerini Apache Spark görselleştirmek için bir varsayılan Grafana panoları kümesi sağlar.

"SYNAPSE Workspace/Workspace" panosu, tüm Apache Spark havuzları, uygulama sayıları, CPU çekirdekleri, vb. için çalışma alanı düzeyinde bir görünüm sağlar.

[![ekran görüntüsü Pano çalışma alanı](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png#lightbox)

"SYNAPSE Workspace/Spark havuzları" panosu, zaman aralığı boyunca seçili Apache Spark havuzunda çalışan Apache Spark uygulamalarının ölçümlerini içerir.

[![ekran görüntüsü Pano Mini Havuzu](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png#lightbox)

"SYNAPSE Workspace/Spark uygulaması" panosu seçili Apache Spark uygulamasını içerir.

[![ekran görüntüsü pano uygulaması](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png#lightbox)

Yukarıdaki Pano şablonları [Azure SYNAPSE Spark uygulama ölçümlerinde](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm/synapse-prometheus-operator/grafana_dashboards)açık kaynaklıdır.