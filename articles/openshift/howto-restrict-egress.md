---
title: Azure Red Hat OpenShift (ARO) kümesinde çıkış trafiğini kısıtlama
description: Azure Red Hat OpenShift (ARO) ' de çıkış trafiğini denetlemek için hangi bağlantı noktalarının ve adreslerin gerekli olduğunu öğrenin
author: sakthi-vetrivel
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 04/09/2021
ms.openlocfilehash: 24c4686306aff9d84fe7bf74ddfdccff987244d9
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368734"
---
# <a name="control-egress-traffic-for-your-azure-red-hat-openshift-aro-cluster-preview"></a>Azure Red Hat OpenShift (ARO) kümeniz için çıkış trafiğini denetleme (Önizleme)

Bu makalede, Azure Red Hat OpenShift kümenizdeki (ARO) giden trafiği güvenli hale getirmeye yönelik gerekli ayrıntılar sağlanmaktadır. Temel bir ARO dağıtımı için küme gereksinimlerini ve isteğe bağlı Red Hat ve üçüncü taraf bileşenleri için daha fazla gereksinimi içerir. Azure Güvenlik Duvarı ile bu gereksinimlerin nasıl yapılandırılacağı hakkında daha fazla bir [örnek](#private-aro-cluster-setup) sunulacaktır. Bu bilgileri Azure Güvenlik Duvarı 'na veya herhangi bir giden kısıtlama yöntemine ya da gereçine uygulayabileceğiniz göz önünde bulundurun.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede yeni bir küme oluşturduğunuzu varsayılmaktadır. Temel bir ARO kümesine ihtiyacınız varsa, bkz. [Aro hızlı](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster)başlangıcı.

> [!IMPORTANT]
> ARO Önizleme özellikleri self servis, kabul etme esasına göre sunulmaktadır. Önizlemeler "olduğu gibi" ve "kullanılabilir olarak" verilmiştir ve hizmet düzeyi anlaşmalarından ve sınırlı garantiden çıkarılır. ARO önizlemeleri, müşteri desteği tarafından kısmen bir en iyi performans kapsamında ele alınmıştır.

## <a name="minimum-required-fqdn--application-rules"></a>Gerekli en düşük FQDN/uygulama kuralları

Bu liste, OpenShift belgelerinden bulunan FQDN 'lerin listesini temel alır: https://docs.openshift.com/container-platform/4.6/installing/install_config/configuring-firewall.html

Aşağıdaki FQDN/uygulama kuralları gereklidir:

| Hedef FQDN | Bağlantı noktası | Kullanın |
| ----------- | ----------- | ------------- |
| **`quay.io`** | **HTTPS: 443** | Küme tarafından kullanılan yükleme için zorunlu. Bu, platform kapsayıcı görüntülerini indirmek için küme tarafından kullanılır. |
| **`registry.redhat.io`** | **HTTPS: 443** | Çekirdek eklentiler için zorunlu. Bu, geliştirme araçları, operatör tabanlı eklentiler ve Red Hat tarafından sunulan kapsayıcı görüntüleri gibi çekirdek bileşenleri indirmek için küme tarafından kullanılır.
| **`mirror.openshift.com`** | **HTTPS: 443** | Bu, VDı ortamında veya dizüstü bilgisayarınızda yansıtılmış yükleme içeriğine ve görüntülerine erişmek için gereklidir. Quay.io 'dan hangi görüntülerin çekeceğini belirlemek için kümede Platform sürüm imzalarını indirmesi gerekir. |
| **`api.openshift.com`** | **HTTPS: 443** | Görüntü imzalarını indirmeden önce kullanılabilir güncelleştirmeler olup olmadığını denetlemek için küme gereklidir. |
| **`arosvc.azurecr.io`** | **HTTPS: 443** | ARO Işleçleri için iç özel kayıt defteri.  Alt ağlarınızda Microsoft. ContainerRegistry hizmet uç noktalarına izin vermeyin gereklidir. |
| **`management.azure.com`** | **HTTPS: 443** | Bu, küme tarafından Azure API 'Lerine erişmek için kullanılır. |
| **`login.microsoftonline.com`** | **HTTPS: 443** | Bu, küme tarafından Azure 'da kimlik doğrulaması için kullanılır. |
| **`gcs.prod.monitoring.core.windows.net`** | **HTTPS: 443** | Bu, Microsoft Genfiliz Izleme için, ARO ekibinin müşterinin kümesini izleyebilmesi için kullanılır. |
| **`*.blob.core.windows.net`** | **HTTPS: 443** | Bu, Microsoft Genfiliz Izleme için, ARO ekibinin müşterinin kümesini izleyebilmesi için kullanılır. |
| **`*.servicebus.windows.net`** | **HTTPS: 443** | Bu, Microsoft Genfiliz Izleme için, ARO ekibinin müşterinin kümesini izleyebilmesi için kullanılır. |
| **`*.table.core.windows.net`** | **HTTPS: 443** | Bu, Microsoft Genfiliz Izleme için, ARO ekibinin müşterinin kümesini izleyebilmesi için kullanılır. |

> [!NOTE] 
> *. Blob, *. tablo ve diğer büyük adres alanlarını ortaya çıkaran birçok müşteri için olası bir veri savunma sorunu oluşturur. Kümede dağıtılan uygulamaların bu hedeflere ulaşmasını ve belirli uygulama ihtiyaçları için Azure özel bağlantısını kullanmasını sağlamak için [OpenShift çıkış güvenlik duvarını](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/configuring-egress-firewall.html) kullanmayı düşünmek isteyebilirsiniz.

---

## <a name="complete-list-of-required-and-optional-fqdns"></a>Gerekli ve isteğe bağlı FQDN 'lerin tümünü listeleme

### <a name="first-group-installing-and-downloading-packages-and-tools"></a>ILK GRUP: PAKETLERI VE ARAÇLARı YÜKLEME VE INDIRME

- **`quay.io`**: Küme tarafından kullanılan yükleme için zorunlu. Bu, platform kapsayıcı görüntülerini indirmek için küme tarafından kullanılır.
- **`registry.redhat.io`**: Çekirdek eklentiler için zorunludur. Bu, geliştirme araçları, operatör tabanlı eklentiler veya Red Hat tarafından sunulan ara yazılımlar gibi temel bileşenleri, Evrensel temel görüntüyü indirmek için küme tarafından kullanılır...
- **`sso.redhat.com`**: Bu, VDı ortamında veya dizüstü bilgisayarınızda cloud.redhat.com 'e bağlanmak için gereklidir. Bu, çekme gizliliğini indirebileceğimiz sitedir ve aboneliklerinizin, küme envanterinin ve geri ödeme raporlarınızın izlenmesini kolaylaştırmak için Red Hat 'te sunduğumuz SaaS çözümlerinin bazılarını kullanabilir.
- **`openshift.org`**: Bu bir tane VDı ortamında veya dizüstü bilgisayarınızda RH CoreOS görüntülerini indirmek üzere bağlanmak için gereklidir, ancak Azure 'da Market 'ten çekildiğinde, işletim sistemi görüntülerini indirmeniz gerekmez.

---

### <a name="second-group-telemetry"></a>IKINCI GRUP: TELEMETRI

Bu bölümün tümü kabul edilebilir, ancak nasıl olduğunu öğrendiğimiz için, lütfen ne olduğunu kontrol edin: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/about-remote-health-monitoring.html
- **`cert-api.access.redhat.com`**: VDı veya dizüstü ortamınızda kullanın.
- **`api.access.redhat.com`**: VDı veya dizüstü ortamınızda kullanın.
- **`infogw.api.openshift.com`**: VDı veya dizüstü ortamınızda kullanın.
- **`https://cloud.redhat.com/api/ingress`**: AaS Red Hat öngörüleri ile tümleşen Öngörüler operatörü için kümede kullanın.
OpenShift kapsayıcı platformunda müşteriler raporlama sistem durumu ve kullanım bilgilerini devre dışı bırakabilirsiniz. Ancak, bağlı kümeler Red Hat 'in sorunlara daha hızlı tepki vermesini ve müşterilerimizi daha iyi desteklemesi ve ürün yükseltmelerini daha iyi anlaması için izin verir. Ayrıntıları şurada denetle: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/opting-out-of-remote-health-reporting.html .

---

### <a name="third-group-cloud-apis"></a>ÜÇÜNCÜ Grup: bulut API 'Leri

- **`management.azure.com`**: Bu, küme tarafından Azure API 'Lerine erişmek için kullanılır.

---

### <a name="fourth-group-other-openshift-requirements"></a>DÖRDÜNCÜ GRUP: DIĞER OPENSHIFT GEREKSINIMLERI

- **`mirror.openshift.com`**: Bu durum, VDı ortamında veya dizüstü bilgisayarınızda, yansıtılmış yükleme içeriğine ve görüntülerine erişmek ve kümede gerekli olan görüntüleri belirlemek için küme tarafından kullanılan Platform sürüm imzalarını indirmek için gereklidir.
- **`storage.googleapis.com/openshift-release`**: Quay.io 'den hangi görüntülerin çekeceğini belirlemek için küme tarafından kullanılan platform yayın imzalarını indirmek için alternatif site.
- **`*.apps.<cluster_name>.<base_domain>`** (Veya eşdeğer ARO URL 'SI): allowlist, bu etki alanları, OpenShift 'te dağıtılan uygulamalara ulaşmak veya OpenShift konsoluna erişmek için şirket ağınızda kullanılır.
- **`api.openshift.com`**: Görüntü imzalarını indirmeden önce kullanılabilir güncelleştirmeler olup olmadığını denetlemek için küme gereklidir.
- **`registry.access.redhat.com`**: ODO CLı aracını kullanırken dev görüntülerini indirmek için VDı veya dizüstü ortamınızda kayıt defteri erişimi gereklidir. (Bu CLı Aracı, Kubernetes 'e alışkın olmayan geliştiriciler için alternatif bir CLı aracıdır.) https://docs.openshift.com/container-platform/4.6/cli_reference/developer_cli_odo/understanding-odo.html

---

### <a name="fifth-group-microsoft--red-hat-aro-monitoring-service"></a>BEŞINCI GRUP: MICROSOFT & RED HAT ARO IZLEME HIZMETI

- **`login.microsoftonline.com`**: Bu, küme tarafından Azure 'da kimlik doğrulaması için kullanılır.
- **`gcs.prod.monitoring.core.windows.net`**: Bu, ARO ekibinin müşterinin kümesini izleyebilmesi için Microsoft Genfiliz Izleme için kullanılır.
- **`*.blob.core.windows.net`**: Bu, ARO ekibinin müşterinin kümesini izleyebilmesi için Microsoft Genfiliz Izleme için kullanılır.
- **`*.servicebus.windows.net`**: Bu, ARO ekibinin müşterinin kümesini izleyebilmesi için Microsoft Genfiliz Izleme için kullanılır.
- **`*.table.core.windows.net`**: Bu, ARO ekibinin müşterinin kümesini izleyebilmesi için Microsoft Genfiliz Izleme için kullanılır.

## <a name="aro-integrations"></a>ARO tümleştirmeleri

### <a name="azure-monitor-for-containers"></a>Kapsayıcılar için Azure İzleyici

Kapsayıcılar için Azure Izleyici 'ye erişim sağlamanın iki seçeneği vardır. Azure Izleyici [Servicetag](../virtual-network/service-tags-overview.md#available-service-tags) 'e izin verebilir **veya** gerekli FQDN/uygulama kurallarına erişim sağlayabilirsiniz.  Azure Izleyici 'yi mevcut ARO kümenize nasıl ekleyeceğiniz hakkında [yönergeler](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-azure-redhat4-setup) aşağıda verilmiştir.

#### <a name="required-network-rules"></a>Gerekli ağ kuralları

Aşağıdaki FQDN/uygulama kuralları gereklidir:

| Hedef uç nokta                                                             | Protokol | Bağlantı noktası    | Kullanın  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Bu uç nokta, ölçüm verilerini ve günlükleri Azure Izleyici 'ye ve Log Analytics göndermek için kullanılır. |

#### <a name="required-fqdn--application-rules"></a>Gerekli FQDN/uygulama kuralları

Aşağıdaki FQDN/uygulama kuralları, kapsayıcılar için Azure Izleyicisi etkinleştirilmiş olan ARO kümeleri için gereklidir:

| FQDN                                    | Bağlantı noktası      | Kullanın      |
|-----------------------------------------|-----------|----------|
| **`dc.services.visualstudio.com`** | **`HTTPS:443`**    | Bu uç nokta, Azure Izleyici kullanarak ölçümler ve izleme telemetri için kullanılır. |
| **`*.ods.opinsights.azure.com`**    | **`HTTPS:443`**    | Bu uç nokta, Azure Izleyici tarafından günlük analizi verilerini almak için kullanılır. |
| **`*.oms.opinsights.azure.com`** | **`HTTPS:443`** | Bu uç nokta, Log Analytics hizmetinin kimliğini doğrulamak için kullanılan omsagent tarafından kullanılır. |
| **`*.monitoring.azure.com`** | **`HTTPS:443`** | Bu uç nokta, ölçüm verilerini Azure Izleyici 'ye göndermek için kullanılır. |


## <a name="private-aro-cluster-setup"></a>Özel ARO kümesi kurulumu
Hedef, çıkış trafiğini bir Azure Güvenlik Duvarı üzerinden yönlendirerek, ARO kümesinin güvenliğini sağlamaktır
### <a name="before"></a>Önce:
![Önce](media/concepts-networking/aro-private.jpg)
### <a name="after"></a>Sonra:
![Sonra](media/concepts-networking/aro-fw.jpg)

## <a name="create-a-private-aro-cluster"></a>Özel bir ARO kümesi oluşturma

### <a name="set-up-vars-for-your-environment"></a>Ortamınız için VARS ayarlama
```bash

CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster
AROVNET=aro-vnet # The name of your vnet from your created ARO cluster
JUMPSUBNET=jump-subnet
LOCATION=eastus # The location where ARO cluster is deployed

```

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
```bash
az group create -g "$RESOURCEGROUP" -l $LOCATION
```

### <a name="create-the-virtual-network"></a>Sanal ağı oluşturma
```bash
az network vnet create \
  -g $RESOURCEGROUP \
  -n $AROVNET \
  --address-prefixes 10.0.0.0/8
```

### <a name="add-two-empty-subnets-to-your-virtual-network"></a>Sanal ağınıza iki boş alt ağ ekleyin
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "$CLUSTER-master" \
    --address-prefixes 10.10.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry

  az network vnet subnet create \
    -g $RESOURCEGROUP \
    --vnet-name $AROVNET \
    -n "$CLUSTER-worker" \
    --address-prefixes 10.20.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```

### <a name="disable-network-policies-for-private-link-service-on-your-virtual-network-and-subnets-this-is-a-requirement-for-the-aro-service-to-access-and-manage-the-cluster"></a>Sanal ağınızda ve alt ağlarınızın özel bağlantı hizmeti için ağ ilkelerini devre dışı bırakın. Bu, ARO hizmetinin kümeye erişmesi ve onu yönetmesi için bir gereksinimdir.
```bash
az network vnet subnet update \
  -g "$RESOURCEGROUP" \
  --vnet-name $AROVNET \
  -n "$CLUSTER-master" \
  --disable-private-link-service-network-policies true
```
### <a name="create-a-firewall-subnet"></a>Güvenlik Duvarı alt ağı oluşturma
```bash
az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "AzureFirewallSubnet" \
    --address-prefixes 10.100.1.0/26
```

## <a name="create-a-jump-host-vm"></a>Bir geçiş ana bilgisayar VM 'si oluşturma
### <a name="create-a-jump-subnet"></a>Bir hızlı alt ağ oluşturma
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n $JUMPSUBNET \
    --address-prefixes 10.30.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```
### <a name="create-a-jump-host-vm"></a>Bir geçiş ana bilgisayar VM 'si oluşturma
```bash
VMUSERNAME=aroadmin

az vm create --name ubuntu-jump \
             --resource-group $RESOURCEGROUP \
             --ssh-key-values ~/.ssh/id_rsa.pub \
             --admin-username $VMUSERNAME \
             --image UbuntuLTS \
             --subnet $JUMPSUBNET \
             --public-ip-address jumphost-ip \
             --vnet-name $AROVNET 
```

## <a name="create-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift kümesi oluşturma
### <a name="get-a-red-hat-pull-secret-optional"></a>Red hat çekme gizli anahtarı alma (isteğe bağlı)

Red hat çekme gizli dizisi, kümenizin diğer içerikle birlikte Red Hat kapsayıcısı kayıt defterlerine erişmesine olanak sağlar. Bu adım isteğe bağlıdır, ancak önerilir.

1. **[Red Hat Openshıft Cluster Manager portala gidin](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) ve oturum açın.**

   Red Hat hesabınızda oturum açmanız veya iş e-postanızı kullanarak yeni bir Red Hat hesabı oluşturmanız ve hüküm ve koşulları kabul etmeniz gerekir.

2. **Çekme gizliliğini Indir ' e tıklayın.**

Kaydedilen `pull-secret.txt` dosyayı güvenli bir yerde tutun-bu, her küme oluşturmada kullanılacaktır.

`az aro create`Komutunu çalıştırırken, parametresini kullanarak çekme gizli dizinizi başvurabilirsiniz `--pull-secret @pull-secret.txt` . `az aro create`Dosyanızı depoladığınız dizinden yürütün `pull-secret.txt` . Aksi takdirde, `@pull-secret.txt` ile değiştirin `@<path-to-my-pull-secret-file` .

Çekme sırlarınızı kopyalıyorsunuz veya başka betiklerin içine başvuruyorsa, çekme gizli anahtarı geçerli bir JSON dizesi olarak biçimlendirilmelidir.

```bash
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet $AROVNET \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --apiserver-visibility Private \
  --ingress-visibility Private \
  --pull-secret @pull-secret.txt
```

## <a name="create-an-azure-firewall"></a>Azure Güvenlik Duvarı oluşturma

### <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma
```bash
az network public-ip create -g $RESOURCEGROUP -n fw-ip --sku "Standard" --location $LOCATION
```
### <a name="update-install-azure-firewall-extension"></a>Güncelleştirme yüklemesi Azure Güvenlik Duvarı uzantısı
```bash
az extension add -n azure-firewall
az extension update -n azure-firewall
```

### <a name="create-azure-firewall-and-configure-ip-config"></a>Azure Güvenlik Duvarı oluşturma ve IP yapılandırmasını yapılandırma
```bash
az network firewall create -g $RESOURCEGROUP -n aro-private -l $LOCATION
az network firewall ip-config create -g $RESOURCEGROUP -f aro-private -n fw-config --public-ip-address fw-ip --vnet-name $AROVNET

```

### <a name="capture-azure-firewall-ips-for-a-later-use"></a>Azure Güvenlik Duvarı IP 'lerini daha sonra kullanılmak üzere yakala
```bash
FWPUBLIC_IP=$(az network public-ip show -g $RESOURCEGROUP -n fw-ip --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RESOURCEGROUP -n aro-private --query "ipConfigurations[0].privateIpAddress" -o tsv)

echo $FWPUBLIC_IP
echo $FWPRIVATE_IP
```

### <a name="create-a-udr-and-routing-table-for-azure-firewall"></a>Azure Güvenlik Duvarı için UDR ve yönlendirme tablosu oluşturma
```bash
az network route-table create -g $RESOURCEGROUP --name aro-udr

az network route-table route create -g $RESOURCEGROUP --name aro-udr --route-table-name aro-udr --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP
```

### <a name="add-application-rules-for-azure-firewall"></a>Azure Güvenlik Duvarı için uygulama kuralları ekleme
Openshıft 'in bu [listeye](https://docs.openshift.com/container-platform/4.3/installing/install_config/configuring-firewall.html#configuring-firewall_configuring-firewall)göre çalışması için kural:
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'ARO' \
 --action allow \
 --priority 100 \
 -n 'required' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns 'registry.redhat.io' '*.quay.io' 'sso.redhat.com' 'management.azure.com' 'mirror.openshift.com' 'api.openshift.com' 'quay.io' '*.blob.core.windows.net' 'gcs.prod.monitoring.core.windows.net' 'registry.access.redhat.com' 'login.microsoftonline.com' '*.servicebus.windows.net' '*.table.core.windows.net' 'grafana.com'
```
Docker görüntüleri için isteğe bağlı kurallar:
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'Docker' \
 --action allow \
 --priority 200 \
 -n 'docker' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns '*cloudflare.docker.com' '*registry-1.docker.io' 'apt.dockerproject.org' 'auth.docker.io'
```

### <a name="associate-aro-subnets-to-fw"></a>ARO alt ağlarını FW ile ilişkilendir
```bash
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-master" --route-table aro-udr
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-worker" --route-table aro-udr
```

## <a name="test-the-configuration-from-the-jumpbox"></a>Atlama kutusundan yapılandırmayı test etme
Bu adımlar yalnızca Docker görüntüleri için kurallar eklediyseniz çalışır. 
### <a name="configure-the-jumpbox"></a>Atlama kutusunu yapılandırma
Bir sıçrama kutusu VM 'de oturum açın ve `azure-cli` ,, `oc-cli` ve `jq` yardımcı programları 'nı yükler. OpenShift-CLI yüklemesi için Red Hat Müşteri Portalı ' nı kontrol edin.
```bash
#Install Azure-cli
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
#Install jq
sudo apt install jq -y
```
### <a name="log-into-the-aro-cluster"></a>ARO kümesinde oturum açma
Küme kimlik bilgilerini Listele:
```bash

# Login to Azure
az login
# Set Vars in Jumpbox
CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster

#Get the cluster credentials
ARO_PASSWORD=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminPassword')
ARO_USERNAME=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminUsername')
```
API sunucusu uç noktası al:
```bash
ARO_URL=$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.apiserverProfile.url')
```

### <a name="download-the-oc-cli-to-the-jumpbox"></a>OC CLI 'yı sıçrama kutusu 'a indirin
```bash
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

Şunu kullanarak oturum açın `oc login` :
```bash
oc login $ARO_URL -u $ARO_USERNAME -p $ARO_PASSWORD
```

### <a name="run-centos-to-test-outside-connectivity"></a>Dış bağlantıyı test etmek için CentOS 'ı Çalıştır
Pod oluşturma
```bash
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: centos
spec:
  containers:
  - name: centos
    image: centos
    ports:
    - containerPort: 80
    command:
    - sleep
    - "3600"
EOF
```
Pod çalışır hale getirildikten sonra, BT 'ye özel ve bağlantı dışında test edin.

```bash
oc exec -it centos -- /bin/bash
curl microsoft.com
```

## <a name="access-the-web-console-of-the-private-cluster"></a>Özel kümenin web konsoluna erişin

### <a name="set-up-ssh-forwards-commands"></a>SSH iletme komutlarını ayarlama

```bash
sudo ssh -i $SSH_PATH -L 443:$CONSOLE_URL:443 aroadmin@$JUMPHOST

example:
sudo ssh -i /Users/jimzim/.ssh/id_rsa -L 443:console-openshift-console.apps.d5xm5iut.eastus.aroapp.io:443 aroadmin@104.211.18.56
```

### <a name="modify-the-etc-hosts-file-on-your-local-machine"></a>Yerel makinenizde vs. Hosts dosyasını değiştirme
```bash
##
# Host Database
#
127.0.0.1 console-openshift-console.apps.d5xm5iut.eastus.aroapp.io
127.0.0.1 oauth-openshift.apps.d5xm5iut.eastus.aroapp.io
```

### <a name="use-sshuttle-as-another-option"></a>Başka bir seçenek olarak SShuttle kullanın

[SSHuttle](https://github.com/sshuttle/sshuttle)


## <a name="clean-up-resources"></a>Kaynakları temizleme

```bash

# Clean up the ARO cluster, vnet, firewall and jumpbox

# Remove udr from master and worker subnets first or will get error when deleting ARO cluster
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-master -g $RESOURCEGROUP --route-table aro-udr --remove routeTable
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-worker -g $RESOURCEGROUP --route-table aro-udr --remove routeTable

# Remove ARO Cluster
az aro delete -n $CLUSTER -g $RESOURCEGROUP

# Remove the resource group that contains the firewall, jumpbox and vnet
az group delete -n $RESOURCEGROUP
```