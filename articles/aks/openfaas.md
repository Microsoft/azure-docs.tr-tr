---
title: Azure Kubernetes Service (AKS) ile OpenFaaS kullanma
description: Kapsayıcılarla sunucusuz işlevler oluşturmak için bir Azure Kubernetes Service (AKS) kümesi üzerinde OpenFaaS dağıtma ve kullanma hakkında bilgi edinin.
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 319107127b79383fc3b49f0eeb856a0e6c5b09f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747760"
---
# <a name="using-openfaas-on-aks"></a>AKS üzerinde OpenFaaS kullanma

[Openfaas][open-faas] , kapsayıcıların kullanımı aracılığıyla sunucusuz işlevler oluşturmaya yönelik bir çerçevedir. Açık kaynak proje olarak, topluluk içinde büyük ölçekli benimseme kazanımıştır. Bu belge, Azure Kubernetes Service (AKS) kümesine OpenFaas yükleme ve kullanma ayrıntılarını inceleyin.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlayabilmeniz için aşağıdakiler gerekir.

* Kubernetes 'in temel olarak anlaşılmasıdır.
* Geliştirme sisteminizde yapılandırılmış bir Azure Kubernetes hizmeti (AKS) kümesi ve AKS kimlik bilgileri.
* Azure CLı, geliştirme sisteminize yüklendi.
* Sisteminizde yüklü olan git komut satırı araçları.

## <a name="add-the-openfaas-helm-chart-repo"></a>OpenFaaS Held grafik deposu ekleme

[https://shell.azure.com](https://shell.azure.com)Tarayıcınızda Azure Cloud Shell açmak için bölümüne gidin.

OpenFaaS, en son değişikliklerle güncel kalmasını sağlamak için kendi hele grafiklerini saklar.

```console
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>OpenFaaS dağıtma

İyi bir uygulama olarak, OpenFaaS ve OpenFaaS işlevleri kendi Kubernetes ad alanı içinde depolanmalıdır.

OpenFaaS sistemi ve işlevleri için bir ad alanı oluşturun:

```console
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

OpenFaaS UI portalı ve REST API için bir parola oluşturun:

```console
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Gizli dizi değerini ile edinebilirsiniz `echo $PASSWORD` .

Burada oluşturduğumuz parola, bir bulut yük dengeleyici aracılığıyla Internet 'e sunulan OpenFaaS ağ geçidinde temel kimlik doğrulamasını etkinleştirmek için helk grafiği tarafından kullanılacaktır.

OpenFaaS için bir HELI grafiği kopyalanmış depoya dahil edilmiştir. AKS kümenize OpenFaaS dağıtmak için bu grafiği kullanın.

```console
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

Çıkış:

```output
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

```console
kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

OpenFaaS ağ geçidine erişmek için genel bir IP adresi oluşturulur. Bu IP adresini almak için [kubectl Get Service][kubectl-get] komutunu kullanın. IP adresinin hizmete atanması bir dakika sürebilir.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Çıktıların.

```output
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

OpenFaaS sistemini test etmek için, bu örnekte 8080 numaralı bağlantı noktasındaki dış IP adresine gidin `http://52.186.64.52:8080` . Oturum açmanız istenir. Parolanızı getirmek için girin `echo $PASSWORD` .

![OpenFaaS Kullanıcı arabirimi](media/container-service-serverless/openfaas.png)

Son olarak, OpenFaaS CLı 'yı yükler. Bu örnek Brew kullandı, daha fazla seçenek için [Openfaas CLI belgelerine][open-faas-cli] bakın.

```console
brew install faas-cli
```

`$OPENFAAS_URL`Yukarıda bulunan genel IP 'ye ayarlayın.

Azure CLı ile oturum açın:

```console
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>İlk işlev oluştur

OpenFaaS işletimsel olduğuna göre, OpenFaas portalını kullanarak bir işlev oluşturun.

**Yeni Işlev Dağıt ' a** tıklayın ve **figlet** için arama yapın. Figlet işlevini seçin ve **Dağıt**' a tıklayın.

![Ekran görüntüsü, arama satırında figlet metin ile yeni bir Işlev Dağıt iletişim kutusu gösterir.](media/container-service-serverless/figlet.png)

İşlevi çağırmak için kıvrımlı kullanın. Aşağıdaki örnekteki IP adresini OpenFaas ağ geçidinizin ile değiştirin.

```console
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Çıkış:

```output
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>İkinci işlev oluştur

Şimdi ikinci bir işlev oluşturun. Bu örnek, OpenFaaS CLı kullanılarak dağıtılır ve özel bir kapsayıcı görüntüsü içerir ve bir Cosmos DB verileri alır. İşlev oluşturulmadan önce birkaç öğenin yapılandırılması gerekir.

İlk olarak, Cosmos DB için yeni bir kaynak grubu oluşturun.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Türünün CosmosDB örneğini dağıtın `MongoDB` . Örneğin benzersiz bir adı olmalıdır ve `openfaas-cosmos` ortamınıza özgü bir öğe için güncelleştirin.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Cosmos veritabanı bağlantı dizesini alın ve bir değişkende depolayın.

`--resource-group`Bağımsız değişkeninin değerini kaynak grubunuzun adı ve `--name` bağımsız değişkeni Cosmos db adı olarak güncelleştirin.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Artık Cosmos DB test verileriyle doldurun. Aşağıdaki JSON içinde adlı bir dosya oluşturun `plans.json` ve kopyalayın.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

CosmosDB örneğini verilerle birlikte yüklemek için *mongoımport* aracını kullanın.

Gerekirse, MongoDB araçları 'nı yükler. Aşağıdaki örnek, Brew kullanarak bu araçları yükle, diğer seçeneklere yönelik [MongoDB belgelerine][install-mongo] bakın.

```console
brew install mongodb
```

Verileri veritabanına yükleyin.

```console
mongoimport --uri=$COSMOS -c plans < plans.json
```

Çıkış:

```output
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

İşlevi oluşturmak için aşağıdaki komutu çalıştırın. `-g`Bağımsız değişkenin değerini OpenFaaS ağ geçidi adresinizle güncelleştirin.

```console
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Dağıtıldıktan sonra, işlev için yeni oluşturduğunuz OpenFaaS uç noktasını görmeniz gerekir.

```output
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

İşlevi kıvrımlı kullanarak test edin. IP adresini OpenFaaS ağ geçidi adresiyle güncelleştirin.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Çıkış:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Ayrıca, işlevi OpenFaaS Kullanıcı arabirimi içinde de test edebilirsiniz.

![alternatif metin](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Sonraki Adımlar

Kendi GitHub bot ' ı oluşturma, gizli dizileri kullanma, ölçümleri görüntüleme ve otomatik ölçeklendirme gibi konuları kapsayan bir uygulamalı laboratuvar kümesi aracılığıyla OpenFaaS Workshop ile bilgi almaya devam edebilirsiniz.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
