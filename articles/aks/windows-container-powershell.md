---
title: PowerShell kullanarak AKS kümesinde Windows Server kapsayıcısı oluşturma
description: Hızlı bir şekilde bir Kubernetes kümesi oluşturmayı, PowerShell kullanarak Azure Kubernetes Service (AKS) içindeki bir Windows Server kapsayıcısında uygulamayı dağıtmayı öğrenin.
services: container-service
ms.topic: article
ms.date: 03/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b877ecbdca06ff73d152e1b491e993798a99f98a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103233523"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-powershell"></a>PowerShell kullanarak bir Azure Kubernetes Service (AKS) kümesi üzerinde Windows Server kapsayıcısı oluşturma

Azure Kubernetes hizmeti (AKS), kümelerinizi hızlı bir şekilde dağıtmanıza ve yönetmenize olanak tanıyan bir yönetilen Kubernetes hizmetidir. Bu makalede PowerShell kullanarak bir AKS kümesi dağıtırsınız. Ayrıca `ASP.NET` , bir Windows Server kapsayıcısında kümeye örnek bir uygulama da dağıtabilirsiniz.

![ASP.NET örnek uygulamasına göz atma görüntüsü](media/windows-container-powershell/asp-net-sample-app.png)

Bu makalede, Kubernetes kavramlarının temel bir şekilde anlaşıldığı varsayılır. Daha fazla bilgi için bkz. [Azure Kubernetes hizmeti (AKS) Için Kubernetes temel kavramları][kubernetes-concepts].

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

PowerShell 'i yerel olarak kullanmayı seçerseniz, bu makale az PowerShell modülünü yüklemenizi ve [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanmanızı gerektirir. Az PowerShell modülünü yükleme hakkında daha fazla bilgi için bkz. [yükleme Azure PowerShell][install-azure-powershell]. Az. aks PowerShell modülünü de yüklemelisiniz: 

```azurepowershell-interactive
Install-Module Az.Aks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Birden çok Azure aboneliğiniz varsa, kaynakların faturalandırılması gereken uygun aboneliği seçin. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 'ini kullanarak belirli BIR abonelik kimliği seçin.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="limitations"></a>Sınırlamalar

Birden çok düğüm havuzunu destekleyen AKS kümelerini oluşturup yönetirken aşağıdaki sınırlamalar geçerlidir:

* İlk düğüm havuzunu silemezsiniz.

Windows Server düğüm havuzları için aşağıdaki ek sınırlamalar geçerlidir:

* AKS kümesinde en fazla 10 düğüm havuzu olabilir.
* AKS kümesi, her düğüm havuzunda en fazla 100 düğüme sahip olabilir.
* Windows Server düğüm havuzu adının 6 karakterlik bir sınırı vardır.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[Azure Kaynak grubu](../azure-resource-manager/management/overview.md) , Azure kaynaklarının dağıtıldığı ve yönetildiği mantıksal bir gruptur. Bir kaynak grubu oluştururken konum belirtmeniz istenir. Bu konum, kaynak grubu meta verilerinin depolandığı yerdir, kaynak oluşturma sırasında başka bir bölge belirtmezseniz kaynaklarınızın Azure 'da da çalıştığı yerdir. [New-AzResourceGroup][new-azresourcegroup] cmdlet 'ini kullanarak bir kaynak grubu oluşturun.

Aşağıdaki örnek **eastus** konumunda **myResourceGroup** adlı bir kaynak grubu oluşturur.

> [!NOTE]
> Bu makalede, bu öğreticideki komutlar için PowerShell sözdizimi kullanılmaktadır. Azure Cloud Shell kullanıyorsanız, Cloud Shell penceresinin sol üst kısmındaki açılan listenin **PowerShell** olarak ayarlandığından emin olun.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Aşağıdaki örnek çıktıda başarıyla oluşturulan kaynak grubu gösterilmektedir:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-an-aks-cluster"></a>AKS kümesi oluşturma

`ssh-keygen`BIR SSH anahtar çifti oluşturmak için komut satırı yardımcı programını kullanın. Daha fazla ayrıntı için bkz. [hızlı adımlar: Azure 'Da Linux VM 'ler IÇIN SSH genel-özel anahtar çifti oluşturma ve kullanma](../virtual-machines/linux/mac-create-ssh-keys.md).

Windows Server kapsayıcıları için düğüm havuzlarını destekleyen bir AKS kümesi çalıştırmak için, kümenizin [Azure CNI][azure-cni-about] (Gelişmiş) ağ eklentisini kullanan bir ağ ilkesi kullanması gerekir. Gerekli alt ağ aralıklarını ve ağ konularını planlamaya yardımcı olacak daha ayrıntılı bilgi için bkz. [Azure CNI ağını yapılandırma][use-advanced-networking]. **Myakscluster** adlı bir aks kümesi oluşturmak Için aşağıdaki [New-AzAks][new-azaks] cmdlet 'ini kullanın. Aşağıdaki örnek, mevcut değilse gerekli ağ kaynaklarını oluşturur.

> [!NOTE]
> Kümenizin güvenilir bir şekilde çalıştığından emin olmak için varsayılan düğüm havuzunda en az 2 (iki) düğüm çalıştırmalısınız.

```azurepowershell-interactive
$Username = Read-Host -Prompt 'Please create a username for the administrator credentials on your Windows Server containers: '
$Password = Read-Host -Prompt 'Please create a password for the administrator credentials on your Windows Server containers: ' -AsSecureString
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -NetworkPlugin azure -NodeVmSetType VirtualMachineScaleSets -WindowsProfileAdminUserName $Username -WindowsProfileAdminUserPassword $Password
```

> [!Note]
> Bu bölgede sürüm desteklenmediğinden AKS kümesini oluşturamadığı takdirde, `Get-AzAksVersion -Location eastus` Bu bölge için desteklenen sürüm listesini bulmak için komutunu kullanabilirsiniz.

Birkaç dakika sonra komut tamamlanır ve küme hakkında bilgi döndürür. Bazen kümenin sağlanması birkaç dakikadan uzun sürebilir. Bu durumlarda en fazla 10 dakika bekleyin.

## <a name="add-a-windows-server-node-pool"></a>Windows Server düğüm Havuzu Ekle

Varsayılan olarak, bir AKS kümesi, Linux kapsayıcıları çalıştırabilirler bir düğüm havuzuyla oluşturulur. `New-AzAksNodePool`Linux düğüm havuzunun yanı sıra Windows Server kapsayıcıları çalıştıran bir düğüm havuzu eklemek için cmdlet 'ini kullanın.

```azurepowershell-interactive
New-AzAksNodePool -ResourceGroupName myResourceGroup -ClusterName myAKSCluster -VmSetType VirtualMachineScaleSets -OsType Windows -Name npwin
```

Yukarıdaki komut, **npwin** adlı yeni bir düğüm havuzu oluşturur ve bunu **Myakscluster** öğesine ekler. Windows Server kapsayıcıları çalıştırmak için bir düğüm havuzu oluştururken, **VMSize** varsayılan değeri **Standard_D2s_v3**. **VMSize** parametresini ayarlamayı seçerseniz, [kısıtlı VM boyutları][restricted-vm-sizes]listesini kontrol edin. Önerilen en düşük boyut **Standard_D2s_v3**. Önceki komut, çalışırken oluşturulan varsayılan VNET 'in varsayılan alt ağını de kullanır `New-AzAks` .

## <a name="connect-to-the-cluster"></a>Kümeye bağlanma

Kubernetes kümesini yönetmek için Kubernetes komut satırı istemcisi olan [kubectl][kubectl]'yi kullanırsınız. Azure Cloud Shell kullanıyorsanız, `kubectl` zaten yüklüdür. `kubectl`Yerel olarak yüklemek için `Install-AzAksKubectl` cmdlet 'ini kullanın:

```azurepowershell-interactive
Install-AzAksKubectl
```

`kubectl`Kubernetes kümenize bağlanacak şekilde yapılandırmak Için [Import-AzAksCredential][import-azakscredential] cmdlet 'ini kullanın. Bu komut, kimlik bilgilerini indirir ve Kubernetes CLı 'yi bunları kullanacak şekilde yapılandırır.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

Kümenize bağlantıyı doğrulamak için [kubectl get][kubectl-get] komutunu kullanarak küme düğümleri listesini alın.

```azurepowershell-interactive
kubectl get nodes
```

Aşağıdaki örnek çıktı kümedeki tüm düğümleri gösterir. Tüm düğümlerin **durumunun olduğundan emin olun:**

```plaintext
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.7
aksnpwin987654                      Ready    agent   108s   v1.16.7
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Bir Kubernetes bildirim dosyası, küme için, hangi kapsayıcı görüntülerinin çalıştırılacağı gibi istenen durumu tanımlar. Bu makalede, bir Windows Server kapsayıcısında ASP.NET örnek uygulamasını çalıştırmak için gerekli tüm nesneleri oluşturmak için bir bildirim kullanılır. Bu bildirimde, ASP.NET örnek uygulaması için bir [Kubernetes dağıtımı][kubernetes-deployment] ve uygulamaya internet 'ten erişmek için bir dış [Kubernetes hizmeti][kubernetes-service] dahildir.

ASP.NET örnek uygulaması, [.NET Framework örneklerinin][dotnet-samples] bir parçası olarak sağlanır ve bir Windows Server kapsayıcısında çalıştırılır. AKS 'ler Windows Server kapsayıcıları 'nın **Windows server 2019** veya daha büyük görüntülerini temel alarak olmasını gerektirir. Kubernetes bildirim dosyası, AKS kümenizin, Windows Server kapsayıcıları çalıştırabilmiş bir düğümde ASP.NET örnek uygulamanızın Pod özelliğini çalıştırmasını söylemek için bir [düğüm seçici][node-selector] de tanımlamalıdır.

`sample.yaml`Aşağıdaki YAML tanımında adlı bir dosya oluşturun ve kopyalayın. Azure Cloud Shell kullanırsanız, bu dosya kullanılarak `vi` veya `nano` bir sanal veya fiziksel sistemde çalışırken oluşturulabilir:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

[Kubectl Apply][kubectl-apply] komutunu kullanarak uygulamayı dağıtın ve YAML bildiriminizde adı belirtin:

```azurepowershell-interactive
kubectl apply -f sample.yaml
```

Aşağıdaki örnek çıktıda dağıtım ve hizmet başarıyla oluşturuldu gösterilmektedir:

```plaintext
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Uygulamayı test edin

Uygulama çalıştığında bir Kubernetes hizmeti, uygulamanın ön uç noktasını Internet 'e sunar.
Bu işlemin tamamlanması birkaç dakika sürebilir. Bazen hizmetin sağlanması birkaç dakikadan uzun sürebilir. Bu durumlarda en fazla 10 dakika bekleyin.

İlerlemeyi izlemek için, [kubectl Get Service][kubectl-get] komutunu bağımsız değişkeniyle birlikte kullanın `--watch` .

```azurepowershell-interactive
kubectl get service sample --watch
```

Başlangıçta **örnek** hizmet IÇIN **dış IP** , **Beklemede** olarak gösterilir.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

**Dış IP** adresi **bekliyor** durumundan gerçek ortak IP adresi olarak değiştiğinde, `CTRL-C` izleme işlemini durdurmak için kullanın `kubectl` . Aşağıdaki örnek çıktıda, hizmete atanmış geçerli bir genel IP adresi gösterilmektedir:

```plaintext
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Örnek uygulamayı eylemde görmek için, hizmetinizin dış IP adresine bir Web tarayıcısı açın.

![ASP.NET örnek uygulamasına göz atma görüntüsü](media/windows-container-powershell/asp-net-sample-app.png)

> [!Note]
> Sayfayı yüklemeye çalışırken bir bağlantı zaman aşımı alırsanız, örnek uygulamanın aşağıdaki komutla birlikte hazırlanarak emin olmalısınız `kubectl get pods --watch` . Bazen Windows kapsayıcısı, dış IP adresiniz kullanılabilir olduğunda başlamacaktır.

## <a name="delete-cluster"></a>Kümeyi silme

Küme artık gerekli olmadığında, kaynak grubunu, kapsayıcı hizmetini ve ilgili tüm kaynakları kaldırmak için [Remove-AzResourceGroup][remove-azresourcegroup] cmdlet 'ini kullanın.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> Kümeyi sildiğinizde, AKS kümesi tarafından kullanılan Azure Active Directory hizmet sorumlusu kaldırılmaz. Hizmet sorumlusunu kaldırma adımları için bkz. [AKS hizmet sorumlusuyla ilgili önemli noktalar ve silme][sp-delete]. Yönetilen bir kimlik kullandıysanız, kimlik platform tarafından yönetilir ve kaldırma gerektirmez.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede bir Kubernetes kümesi dağıttınız ve bir `ASP.NET` Windows Server kapsayıcısında bir örnek uygulama dağıttınız. Oluşturduğunuz küme için [Kubernetes web panosuna erişin][kubernetes-dashboard] .

AKS hakkında daha fazla bilgi ve dağıtım örneği için tam kod açıklaması için Kubernetes küme öğreticisine geçin.

> [!div class="nextstepaction"]
> [AKS öğreticisi][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[use-advanced-networking]: configure-azure-cni.md
[new-azaks]: /powershell/module/az.aks/new-azaks
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
