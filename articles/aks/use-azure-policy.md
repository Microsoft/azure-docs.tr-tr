---
title: Kümenizin güvenliğini sağlamak için Azure Ilkesini kullanma
description: Azure Kubernetes hizmeti (AKS) kümesini güvenli hale getirmek için Azure Ilkesini kullanın.
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 6462c2987155925b7df5241d8fb6aa13c1e37b89
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777734"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Azure Ilkesiyle kümenizin güvenliğini sağlama

Azure Kubernetes Service (AKS) kümenizin güvenliğini artırmak için, Azure Ilkesi 'ni kullanarak kümenizde yerleşik güvenlik ilkeleri uygulayabilir ve uygulamayı uygulayabilirsiniz. [Azure ilkesi][azure-policy] , kurumsal standartları zorunlu kılmaya ve üstün ölçekli uyumluluğu değerlendirmenize yardımcı olur. [AKS Için Azure Ilke eklentisini][kubernetes-policy-reference]yükledikten sonra, kümenize tek tek ilke tanımları veya girişim (policysets olarak da bilinir) adlı ilke tanımlarının gruplarını uygulayabilirsiniz. AKS ilkesinin ve girişim tanımlarının tam bir listesi için bkz. [Azure ilkesi yerleşik tanımları][aks-policies] .

Bu makale, kümenize ilke tanımlarının nasıl uygulanacağını ve bu atamaların zorlandığını doğrulamanızı gösterir.

## <a name="prerequisites"></a>Önkoşullar

- Mevcut bir AKS kümesi. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].
- AKS kümesinde yüklü AKS için Azure Ilke eklentisi. [Azure Ilke eklentisini yüklemek için bu adımları][azure-policy-addon]izleyin.

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Yerleşik ilke tanımı veya girişim atama

Bir ilke tanımı veya girişimi uygulamak için Azure portal kullanın.

1. Azure portal Azure Ilke hizmetine gidin.
1. Azure Ilkesi sayfasının sol bölmesinde **tanımlar**' ı seçin.
1. **Kategoriler** ' in altında öğesini seçin `Kubernetes` .
1. Uygulamak istediğiniz ilke tanımını veya girişimi seçin. Bu örnek için `Kubernetes cluster pod security baseline standards for Linux-based workloads` girişimi seçin.
1. **Ata**’yı seçin.
1. **Kapsamı** , Azure ilke eklentisi etkinken aks kümesinin kaynak grubu olarak ayarlayın.
1. **Parametreler** sayfasını seçin ve  `audit` `deny` temel girişimi ihlal eden yeni dağıtımları engellemek Için ' den ' a kadar etkisini güncelleştirin. Değerlendirmeden dışlamak için ek ad alanları da ekleyebilirsiniz. Bu örnek için varsayılan değerleri tutun.
1. İlke atamasını **göndermek Için** **gözden geçir + oluştur** ' u seçin.

## <a name="validate-a-azure-policy-is-running"></a>Bir Azure Ilkesinin çalıştığını doğrulama

Aşağıdakiler çalıştırılarak, ilke atamalarının kümenize uygulanacağını onaylayın:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> İlke atamalarının her bir kümeyle [eşitlenmesi 20 dakikaya kadar][azure-policy-assign-policy] sürebilir.

Çıktının şuna benzer olması gerekir:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              23m
k8sazureallowedusersgroups               23m
k8sazureblockhostnamespace               23m
k8sazurecontainerallowedimages           23m
k8sazurecontainerallowedports            23m
k8sazurecontainerlimits                  23m
k8sazurecontainernoprivilege             23m
k8sazurecontainernoprivilegeescalation   23m
k8sazureenforceapparmor                  23m
k8sazurehostfilesystem                   23m
k8sazurehostnetworkingports              23m
k8sazurereadonlyrootfilesystem           23m
k8sazureserviceallowedports              23m
```

### <a name="validate-rejection-of-a-privileged-pod"></a>Ayrıcalıklı Pod 'ın ret olduğunu doğrulama

' In güvenlik bağlamı ile pod zamanladığınızda, ilk test edelim `privileged: true` . Bu güvenlik bağlamı Pod 'un ayrıcalıklarını ilerletir. Girişim ayrıcalıklı bir pods 'ye izin vermez, bu nedenle istek reddedilir ve dağıtım reddedilmekte olur.

Adlı bir dosya oluşturun `nginx-privileged.yaml` ve aşağıdaki YAML bildirimini yapıştırın:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

[Kubectl Apply][kubectl-apply] komutuyla Pod oluşturun ve YAML bildiriminizde adı belirtin:

```console
kubectl apply -f nginx-privileged.yaml
```

Beklenen şekilde, aşağıdaki örnek çıktıda gösterildiği gibi Pod 'un zamanlanması başarısız olur:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Pod, zamanlama aşamasına ulaşmıyor, bu nedenle, üzerinde geçiş yapmadan önce silinecek kaynak yok.

### <a name="test-creation-of-an-unprivileged-pod"></a>Ayrıcalıksız Pod 'ın test oluşturması

Önceki örnekte kapsayıcı görüntüsü, NGıNX 'i 80 numaralı bağlantı noktasına bağlamak için otomatik olarak kök kullanmaya çalıştı. Bu istek ilke girişimi tarafından reddedildi, bu yüzden Pod başlatılamadı. Artık, ayrıcalıklı erişim olmadan aynı NGıNX Pod 'u çalıştırmayı deneyelim.

Adlı bir dosya oluşturun `nginx-unprivileged.yaml` ve aşağıdaki YAML bildirimini yapıştırın:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

[Kubectl Apply][kubectl-apply] komutunu kullanarak Pod 'ı oluşturun ve YAML bildiriminizde adı belirtin:

```console
kubectl apply -f nginx-unprivileged.yaml
```

Pod başarıyla zamanlandı. [Kubectl Get Pod][kubectl-get] komutunu kullanarak Pod 'un durumunu denetlediğinizde, Pod *çalışır*:

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

Bu örnekte, yalnızca koleksiyondaki ilkeleri ihlal eden dağıtımları etkileyen temel girişim gösterilmektedir. İzin verilen dağıtımlar çalışmaya devam eder.

[Kubectl Delete][kubectl-delete] komutunu kullanarak NGINX unayrıcalıklı Pod öğesini SILIN ve YAML bildiriminizde adı belirtin:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>Bir ilkeyi veya girişimi devre dışı bırakma

Temel girişim 'yi kaldırmak için:

1. Azure portal Ilke bölmesine gidin.
1. Sol bölmeden **atamalar** ' ı seçin.
1. Girişim yanındaki **...** düğmesine tıklayın `Kubernetes cluster pod security baseline standards for Linux-based workloads` .
1. **Atamayı Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Ilkesinin nasıl çalıştığı hakkında daha fazla bilgi için:

- [Azure İlkesi'ne Genel Bakış][azure-policy]
- [AKS için Azure Ilke girişimleri ve ilkeleri][aks-policies]
- [Azure Ilke eklentisini][azure-policy-addon-remove]kaldırın.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs

<!-- LINKS - internal -->
[aks-policies]: policy-reference.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-policy]: ../governance/policy/overview.md
[azure-policy-addon]: ../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks
[azure-policy-addon-remove]: ../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks
[azure-policy-assign-policy]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
