---
title: Azure Kubernetes Service (AKS) kümesi üzerinde açık özgürlük veya WebSphere özgürlük ile Java uygulaması dağıtma
description: Azure Kubernetes Service (AKS) kümesi üzerinde açık özgürlük veya WebSphere özgürlük ile bir Java uygulaması dağıtın.
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: Java, jakartaee, JavaEE, mikro profil, açık-Liberty, WebSphere-Liberty, aks, Kubernetes
ms.openlocfilehash: d0e6f2fea6894378da736ba83a90ee28402ec7f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100007146"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) kümesi üzerinde açık özgürlük veya WebSphere özgürlük ile Java uygulaması dağıtma

Bu makalede nasıl yapılacağı gösterilmektedir:  
* Açık özgürlük veya WebSphere Liberty çalışma zamanında Java, Java EE, Jakarta veya mikro profil uygulamanızı çalıştırın.
* Açık serbest yer kapsayıcısı görüntülerini kullanarak uygulama Docker görüntüsünü oluşturun.
* Açık Liberty Işlecini kullanarak Kapsayıcılı uygulamayı bir AKS kümesine dağıtın.   

Açık özgürlük Işleci, Kubernetes kümelerinde çalışan uygulamaların dağıtımını ve yönetimini basitleştirir. Açık serbest bir Işleçle, izleme ve dökümleri toplama gibi daha gelişmiş işlemler de yapabilirsiniz. 

Açık özgürlük hakkında daha fazla bilgi için, bkz. [serbest bir proje açın sayfası](https://openliberty.io/). IBM WebSphere özgürlük hakkında daha fazla bilgi için bkz. [WebSphere Liberty ürün sayfası](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Bu makale, Azure CLı 'nin en son sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.
* Bu kılavuzdaki komutları yerel olarak çalıştırıyorsanız (Azure Cloud Shell yerine):
  * UNIX benzeri işletim sistemi yüklü bir yerel makine hazırlayın (örneğin, Ubuntu, macOS, Linux için Windows alt sistemi).
  * Bir Java CE uygulamasını (örneğin, [Benimsetopenjdk OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)) yükler.
  * [Maven](https://maven.apache.org/download.cgi) 3.5.0 veya üstünü yükler.
  * IŞLETIM sisteminize [Docker](https://docs.docker.com/get-docker/) 'yi yüklemeyin.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği mantıksal bir gruptur.  

*Eastus* konumundaki [az Group Create](/cli/azure/group#az_group_create) komutunu kullanarak *Java-Liberty-Project* adlı bir kaynak grubu oluşturun. Bu kaynak grubu daha sonra Azure Container Registry (ACR) örneği ve AKS kümesi oluşturmak için kullanılır. 

```azurecli-interactive
RESOURCE_GROUP_NAME=java-liberty-project
az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-an-acr-instance"></a>ACR örneği oluşturma

ACR örneğini oluşturmak için [az ACR Create](/cli/azure/acr#az_acr_create) komutunu kullanın. Aşağıdaki örnek, *youruniqueacrname* adlı bir ACR örneği oluşturur. *Youruniqueacrname* 'in Azure içinde benzersiz olduğundan emin olun.

```azurecli-interactive
REGISTRY_NAME=youruniqueacrname
az acr create --resource-group $RESOURCE_GROUP_NAME --name $REGISTRY_NAME --sku Basic --admin-enabled
```

Kısa bir süre sonra, şunu içeren bir JSON çıktısı görmeniz gerekir:

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>ACR örneğine bağlanma

Bir görüntüyü gönderebilmeniz için ACR örneğinde oturum açmanız gerekir. Bağlantıyı doğrulamak için aşağıdaki komutları çalıştırın:

```azurecli-interactive
LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

`Login Succeeded`ACR örneğinde başarıyla oturum açtıysanız komut çıkışının sonunda görmeniz gerekir.

## <a name="create-an-aks-cluster"></a>AKS kümesi oluşturma

AKS kümesi oluşturmak için [az aks create](/cli/azure/aks#az_aks_create) komutunu kullanın. Aşağıdaki örnekte, bir düğüm ile *myAKSCluster* adlı bir küme oluşturulmuştur. Bu işlem birkaç dakika sürer.

```azurecli-interactive
CLUSTER_NAME=myAKSCluster
az aks create --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --node-count 1 --generate-ssh-keys --enable-managed-identity
```

Birkaç dakika sonra komut tamamlanır ve aşağıdakiler dahil olmak üzere küme hakkında JSON biçimli bilgileri döndürür:

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>AKS kümesine bağlanma

Kubernetes kümesini yönetmek için Kubernetes komut satırı istemcisi olan [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)'yi kullanırsınız. Azure Cloud Shell kullanıyorsanız, `kubectl` zaten yüklüdür. `kubectl`Yerel olarak yüklemek için [az aks install-cli](/cli/azure/aks#az_aks_install_cli) komutunu kullanın:

```azurecli-interactive
az aks install-cli
```

`kubectl` istemcisini Kubernetes kümenize bağlanacak şekilde yapılandırmak için [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials) komutunu kullanın. Bu komut, kimlik bilgilerini indirir ve Kubernetes CLı 'yi bunları kullanacak şekilde yapılandırır.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --overwrite-existing
```

> [!NOTE]
> Yukarıdaki komut, [Kubernetes yapılandırma dosyası](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)için varsayılan konumu kullanır `~/.kube/config` . *--File* kullanarak Kubernetes yapılandırma dosyanız için farklı bir konum belirtebilirsiniz.

Kümenize bağlantıyı doğrulamak için [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) komutunu kullanarak küme düğümleri listesini alın.

```azurecli-interactive
kubectl get nodes
```

Aşağıdaki örnekte önceki adımlarda oluşturulan tek düğüm gösterilmiştir. Düğüm *durumunun olduğundan emin olun:*

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>Açık serbest bir Işleci yükle

Kümeye oluşturup bağlandıktan sonra, aşağıdaki komutları çalıştırarak [Açık özgürlük işlecini](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.0) yüklersiniz.

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>Uygulama görüntüsü oluştur

Özgürlük uygulamanızı AKS kümesinde dağıtmak ve çalıştırmak için, [Açık serbest yer kapsayıcısı görüntülerini](https://github.com/OpenLiberty/ci.docker) veya [WebSphere serbest bir kapsayıcı görüntülerini](https://github.com/WASdev/ci.docker)kullanarak uygulamanızı bir Docker görüntüsü olarak kapsayıcıya geçirin.

1. Bu kılavuz için örnek kodu kopyalayın. Örnek [GitHub](https://github.com/Azure-Samples/open-liberty-on-aks)' dır.
1. Dizini `javaee-app-simple-cluster` Yerel kopyanızda değiştirin.
1. `mvn clean package`Uygulamayı paketlemek için çalıştırın.
1. `mvn liberty:dev`Uygulamayı test etmek için ' i çalıştırın. `The defaultServer server is ready to run a smarter planet.`Başarılı olursa komut çıktısında görmeniz gerekir. `CTRL-C`Uygulamayı durdurmak için kullanın.
1. Uygulama görüntüsünü derlemek ve ACR örneğine göndermek için aşağıdaki komutlardan birini çalıştırın.
   * Açık özgürlük, hafif bir açık kaynak Java™ çalışma zamanı olarak kullanmayı tercih ediyorsanız açık serbest bir temel görüntüyle derleyin:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME .
     ```

   * Açık özgürlük ticari bir sürümünü kullanmayı tercih ediyorsanız, WebSphere serbest, ana görüntüsü ile derleyin:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>AKS kümesinde uygulama dağıtma

AKS kümesinde Liberty uygulamasını dağıtmak için aşağıdaki adımları izleyin.

1. AKS kümesinin, ACR örneğinden görüntü çekmek için kimlik doğrulamasından geçmek üzere bir çekme gizli anahtarı oluşturun.

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. Geçerli çalışma dizininin `javaee-app-simple-cluster` Yerel kopyanızda olduğunu doğrulayın.
1. Serbest bir uygulamanızı, AKS kümesine 3 çoğaltma ile dağıtmak için aşağıdaki komutları çalıştırın. Komut çıktısı Ayrıca satır içi olarak gösterilir.

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-app-simple-cluster"
   cat openlibertyapplication.yaml | sed -e "s/\${Container_Registry_URL}/${LOGIN_SERVER}/g" | sed -e "s/\${REPLICAS}/3/g" | kubectl apply -f -

   openlibertyapplication.openliberty.io/javaee-app-simple-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication javaee-app-simple-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-app-simple-cluster   youruniqueacrname.azurecr.io/javaee-cafe-simple:1.0.0             True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment javaee-app-simple-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-app-simple-cluster   0/3     3            0           20s
   ```

1. `3/3`Sütunun altında ve sütununun altında görene kadar bekleyin `READY` `3` `AVAILABLE` , `CTRL-C` izleme işlemini durdurmak için kullanın `kubectl` .

### <a name="test-the-application"></a>Uygulamayı test edin

Uygulama çalıştığında, bir Kubernetes Yük Dengeleyici Hizmeti, uygulamanın ön ucuna internet 'e koyar. Bu işlemin tamamlanması biraz zaman alabilir.

İlerlemeyi izlemek için, [kubectl Get Service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) komutunu bağımsız değişkeniyle birlikte kullanın `--watch` .

```azurecli-interactive
kubectl get service javaee-app-simple-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-app-simple-cluster   LoadBalancer   10.0.251.169   52.152.189.57   80:31732/TCP     68s
```

*Dış IP* adresi *bekliyor* durumundan gerçek ortak IP adresine değiştiğinde, `CTRL-C` izleme işlemini durdurmak için kullanın `kubectl` .

`52.152.189.57`Uygulama giriş sayfasını görmek için hizmetinizin dış IP adresine (Yukarıdaki örnek için) bir Web tarayıcısı açın. Sayfanın sol tarafında görünen uygulama çoğaltmalarınızın Pod adını görmeniz gerekir. Birkaç dakika bekleyin ve AKS kümesi tarafından sağlanmış yük dengelemesi nedeniyle farklı bir pod adı görmek için sayfayı yenileyin.

:::image type="content" source="./media/howto-deploy-java-liberty-app/deploy-succeeded.png" alt-text="Java Liberty uygulaması AKS 'de başarıyla dağıtıldı":::

>[!NOTE]
> - Şu anda uygulama HTTPS kullanmıyor. [Kendi sertifikalarınız Ile TLS etkinleştirilmesi](ingress-own-tls.md)önerilir.

## <a name="clean-up-the-resources"></a>Kaynakları temizleme

Azure ücretlerinden kaçınmak için gereksiz kaynakları temizlemeniz gerekir.  Küme artık gerekli olmadığında, [az Group Delete](/cli/azure/group#az_group_delete) komutunu kullanarak kaynak grubunu, kapsayıcı hizmetini, kapsayıcı kayıt defterini ve tüm ilgili kaynakları kaldırın.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME --yes --no-wait
```

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda kullanılan başvurulardan daha fazla bilgi edinebilirsiniz:

* [Azure Kubernetes Service](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Özgürlük aç](https://openliberty.io/)
* [Serbest bir Işleç aç](https://github.com/OpenLiberty/open-liberty-operator)
* [Serbest sunucu yapılandırmasını aç](https://openliberty.io/docs/ref/config/)
* [Liberty Maven eklentisi](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Serbest kapsayıcı resimlerini aç](https://github.com/OpenLiberty/ci.docker)
* [WebSphere serbest kapsayıcı görüntüleri](https://github.com/WASdev/ci.docker)
