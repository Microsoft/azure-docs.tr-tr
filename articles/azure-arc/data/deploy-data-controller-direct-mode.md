---
title: Azure Arc Data Controller 'ı dağıtma | Doğrudan bağlama modu
description: Veri denetleyicisinin doğrudan bağlanma modunda nasıl dağıtılacağını açıklar.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/06/2021
ms.topic: overview
ms.openlocfilehash: 220618f167237d5937766eb5e28b9b6569cef76a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031495"
---
#  <a name="deploy-azure-arc-data-controller--direct-connect-mode"></a>Azure Arc Data Controller 'ı dağıtma | Doğrudan bağlama modu

Bu makalede, bu özelliğin geçerli önizlemesi sırasında Azure Arc veri denetleyicisinin doğrudan bağlanma modunda nasıl dağıtılacağı açıklanır. 

Şu anda Azure portal Azure Arc veri denetleyicisi oluşturabilirsiniz. Azure Arc etkin veri Hizmetleri için diğer araçlar, veri denetleyicisinin doğrudan bağlanma modunda oluşturulmasını desteklemez. Ayrıntılar için bkz. [bilinen sorunlar-Azure Arc etkin veri Hizmetleri (Önizleme)](known-issues.md).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="complete-prerequisites"></a>Tüm önkoşulları

Başlamadan önce, [veri denetleyicisi dağıtma-doğrudan bağlanma modu-Önkoşullar](deploy-data-controller-direct-mode-prerequisites.md)' daki önkoşulları tamamladığınızdan emin olun.

Bu makalede, yüksek düzeyde bu görevlerin nasıl tamamlanacağı açıklanmaktadır:

1. Azure Arc etkin bir veri Hizmetleri uzantısı oluşturun. 
1. Özel bir konum oluşturun.
1. Veri denetleyicisini portaldan dağıtın.

## <a name="create-an-azure-arc-enabled-data-services-extension"></a>Azure Arc etkin veri Hizmetleri uzantısı oluşturma

Bir veri Hizmetleri Uzantısı oluşturmak için k8s-Extension CLı kullanın.

### <a name="set-environment-variables"></a>Ortam değişkenlerini belirleme

Sonraki adımda kullanılacak olan aşağıdaki ortam değişkenlerini ayarlayın.

#### <a name="linux"></a>Linux

``` terminal
# where you want the connected cluster resource to be created in Azure 
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export resourceName=<name of your connected kubernetes cluster>
export location=<Azure location>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
```

### <a name="create-the-arc-data-services-extension"></a>Arc veri Hizmetleri uzantısını oluşturma

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

> [!NOTE]
> Arc Data Services uzantısı yüklemesinin tamamlanması birkaç dakika sürebilir.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Arc veri hizmetleri uzantısının oluşturulduğunu doğrulama

Yayı etkinleştirilmiş veri hizmetleri uzantısının portaldan oluşturulup oluşturulmediğinizi veya doğrudan yay etkin Kubernetes kümesine bağlanarak emin olabilirsiniz. 

#### <a name="azure-portal"></a>Azure portalı
1. Azure portal oturum açın ve Kubernetes bağlı küme kaynağının bulunduğu kaynak grubuna gidin.
1. Uzantının dağıtıldığı Kubernetes kümesini (Type = "Kubernetes-Azure Arc") seçin.
1. Sol taraftaki Gezinti bölmesinde, **Ayarlar** altında "uzantılar (Önizleme)" öğesini seçin.
1. Daha önce "yüklü" bir durumda oluşturulmuş olan uzantıyı görmeniz gerekir.

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="Uzantılar panosu":::

#### <a name="kubectl-cli"></a>kubectl CLı

1. Bir Terminal penceresi aracılığıyla Kubernetes kümenize bağlanın.
1. Aşağıdaki komutu çalıştırın ve yukarıda belirtilen (1) ad alanının oluşturulduğundan ve (2) Pod 'ın bir `bootstrapper` sonraki adıma geçmeden önce ' çalışıyor ' durumunda olduğundan emin olun.

``` console
kubectl get pods -n <name of namespace used in the json template file above>
```

Örneğin, aşağıdaki, ad alanından Pod 'yi alır `arc` .

```console
#Example:
kubectl get pods -n arc
```

## <a name="create-a-custom-location-using-custom-location-cli-extension"></a>Özel konum CLı uzantısı kullanarak özel konum oluşturma

Özel konum bir Kubernetes kümesindeki bir ad alanına eşdeğer bir Azure kaynağıdır.  Özel konumlar, Azure 'a veya Azure 'dan kaynak dağıtmak için hedef olarak kullanılır. [Azure Arc etkin Kubernetes belgelerinin üzerine özel konumlarda](../kubernetes/conceptual-custom-locations.md)özel konumlar hakkında daha fazla bilgi edinin.

### <a name="set-environment-variables"></a>Ortam değişkenlerini belirleme

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation
export clNamespace=arc
export hostClusterId=$(az connectedk8s show -g ${resourceGroup} -n ${resourceName} --query id -o tsv)
export extensionId=$(az k8s-extension show -g ${resourceGroup} -c ${resourceName} --cluster-type connectedClusters --name ${ADSExtensionName} --query id -o tsv)

az customlocation create -g ${resourceGroup} -n ${clName} --namespace ${clNamespace} \
  --host-resource-id ${hostClusterId} \
  --cluster-extension-ids ${extensionId} --location eastus2euap
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"
$ENV:hostClusterId = az connectedk8s show -g "$ENV:resourceGroup" -n "$ENV:resourceName" --query id -o tsv
$ENV:extensionId = az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --cluster-type connectedClusters --name "$ENV:ADSExtensionName" --query id -o tsv

az customlocation create -g "$ENV:resourceGroup" -n "$ENV:clName" --namespace "$ENV:clNamespace" --host-resource-id "$ENV:hostClusterId" --cluster-extension-ids "$ENV:extensionId"
```

## <a name="validate--the-custom-location-is-created"></a>Özel konumun oluşturulduğunu doğrula

Terminalden, özel konumları listelemek için aşağıdaki komutu çalıştırın ve **sağlama durumunun** başarılı olduğunu doğrulayın:

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>Azure Arc veri denetleyicisi oluşturma

Uzantı ve özel konum oluşturulduktan sonra, Azure Arc veri denetleyicisi 'ni dağıtmak için Azure portal ilerleyin.

1. Azure portalında oturum açın.
1. Azure Marketi 'nde "Azure Arc veri denetleyicisi" ni arayın ve akış oluşturma işlemini başlatın.
1. **Önkoşullar** bölümünde, Azure Arc 'ın Kubernetes kümesinin (doğrudan mod) etkin olduğundan emin olun ve sonraki adıma geçin.
1. **Veri denetleyicisi ayrıntıları** bölümünde bir abonelik ve kaynak grubu seçin.
1. Veri denetleyicisi için bir ad girin.
1. Dağıttığınız Kubernetes dağıtım sağlayıcısına dayalı bir yapılandırma profili seçin.
1. Önceki adımda oluşturduğunuz özel konumu seçin.
1. Veri denetleyicisi yönetici oturumu ve parolasının ayrıntılarını sağlayın.
1. Azure nesnelerini oluşturmak için kullanılacak hizmet sorumlusu için ClientID, Tenantıd ve Istemci gizli anahtarı ayrıntılarını sağlayın. Hizmet sorumlusu hesabı ve hesap için verilmesi gereken roller oluşturma hakkında ayrıntılı yönergeler için bkz. [ölçümleri karşıya yükleme](upload-metrics-and-logs-to-azure-monitor.md) .
1. **İleri**' ye tıklayın, tüm ayrıntılar için Özet sayfasını gözden geçirin ve **Oluştur**' a tıklayın.

## <a name="monitor-the-creation"></a>Oluşturmayı izleme

Dağıtım durumu Azure portal dağıtımı başarıyla gösterdiğinde, kümedeki yay veri denetleyicisi dağıtımının durumunu aşağıdaki gibi denetleyebilirsiniz:

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure Arc özellikli PostgreSQL Hiper Ölçek sunucu grubu oluşturma](create-postgresql-hyperscale-server-group.md)

[Azure Arc üzerinde Azure SQL yönetilen örneği oluşturma](create-sql-managed-instance.md)
