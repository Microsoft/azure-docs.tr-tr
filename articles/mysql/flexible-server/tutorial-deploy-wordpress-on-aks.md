---
title: 'Öğretici: Azure CLı kullanarak AKS kümesinde MySQL esnek sunucusuyla WordPress dağıtma'
description: MySQL için Azure veritabanı-esnek sunucu ile AKS 'de WordPress 'i hızla oluşturmayı ve dağıtmayı öğrenin.
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: vc, devx-track-azurecli
ms.openlocfilehash: 0c6211f4cd647addd6f1d18a153695d16a9d9952
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770192"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>Öğretici: MySQL için Azure veritabanı-esnek sunucu ile AKS 'de WordPress uygulaması dağıtma

Bu hızlı başlangıçta, Azure CLı kullanarak MySQL için Azure veritabanı (Önizleme) ile Azure Kubernetes Service (AKS) kümesine bir WordPress uygulaması dağıtırsınız. 
**[Aks](../../aks/intro-kubernetes.md)** , kümeleri hızlı bir şekilde dağıtmanıza ve yönetmenize olanak tanıyan bir yönetilen Kubernetes hizmetidir. **[MySQL Için Azure veritabanı-esnek sunucu (Önizleme)](overview.md)** , veritabanı yönetim işlevleri ve yapılandırma ayarları üzerinde daha ayrıntılı denetim ve esneklik sağlamak için tasarlanmış, tam olarak yönetilen bir veritabanı hizmetidir. Şu anda esnek sunucu önizlemededir.

> [!NOTE]
> - MySQL için Azure veritabanı esnek sunucu şu anda genel önizlemede
> - Bu hızlı başlangıçta, Kubernetes kavramlarını, WordPress ve MySQL 'in temel bir şekilde anlaşıldığı varsayılır.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin en son sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

> [!NOTE]
> Bu hızlı başlangıçta komutları yerel olarak çalıştırıyorsanız (Azure Cloud Shell yerine), komutları yönetici olarak çalıştırın.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği mantıksal bir gruptur. *Eastus* konumunda [az Group Create] [az-Group-Create] komutunu kullanarak *WordPress-Project* kaynak grubu oluşturalım.

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> Kaynak grubunun konumu, kaynak grubu meta verilerinin depolandığı yerdir. Ayrıca, kaynak oluşturma sırasında başka bir bölge belirtmezseniz kaynaklarınızın Azure 'da çalıştığı yerdir.

Aşağıdaki örnek çıktıda başarıyla oluşturulan kaynak grubu gösterilmektedir:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/wordpress-project",
  "location": "eastus",
  "managedBy": null,
  "name": "wordpress-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>AKS kümesi oluşturma

AKS kümesi oluşturmak için [az aks create](/cli/azure/aks#az_aks_create) komutunu kullanın. Aşağıdaki örnekte, bir düğüm ile *myAKSCluster* adlı bir küme oluşturulmuştur. Bu işlem birkaç dakika sürer.

```azurecli-interactive
az aks create --resource-group wordpress-project --name myAKSCluster --node-count 1 --generate-ssh-keys
```

Birkaç dakika sonra komut tamamlanır ve küme hakkında JSON biçimli bilgileri döndürür.

> [!NOTE]
> AKS kümesi oluştururken, AKS kaynaklarını depolamak için otomatik olarak ikinci bir kaynak grubu oluşturulur. Bkz. [neden AKS ile iki kaynak grubu oluşturulur?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Kümeye bağlanma

Kubernetes kümesini yönetmek için Kubernetes komut satırı istemcisi olan [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)'yi kullanırsınız. Azure Cloud Shell kullanıyorsanız, `kubectl` zaten yüklüdür. `kubectl`Yerel olarak yüklemek için [az aks install-cli](/cli/azure/aks#az_aks_install_cli) komutunu kullanın:

```azurecli-interactive
az aks install-cli
```

`kubectl` istemcisini Kubernetes kümenize bağlanacak şekilde yapılandırmak için [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials) komutunu kullanın. Bu komut, kimlik bilgilerini indirir ve Kubernetes CLı 'yi bunları kullanacak şekilde yapılandırır.

```azurecli-interactive
az aks get-credentials --resource-group wordpress-project --name myAKSCluster
```

> [!NOTE]
> Yukarıdaki komut, [Kubernetes yapılandırma dosyası](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)için varsayılan konumu kullanır `~/.kube/config` . *--File* kullanarak Kubernetes yapılandırma dosyanız için farklı bir konum belirtebilirsiniz.

Kümenize bağlantıyı doğrulamak için [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) komutunu kullanarak küme düğümleri listesini alın.

```azurecli-interactive
kubectl get nodes
```

Aşağıdaki örnekte önceki adımlarda oluşturulan tek düğüm gösterilmiştir. Düğüm *durumunun olduğundan emin olun:*

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>MySQL için Azure veritabanı-esnek sunucu oluşturma
[Az MySQL esnek-sunucu oluştur](/cli/azure/mysql/flexible-server)komutuyla esnek bir sunucu oluşturun. Aşağıdaki komut, Azure CLı 'nın yerel bağlamından hizmet varsayılanlarını ve değerlerini kullanarak bir sunucu oluşturur:

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

Oluşturulan sunucu aşağıdaki özniteliklere sahiptir:
- Sunucu ilk sağlandığında yeni boş bir veritabanı ```flexibleserverdb``` oluşturulur. Bu hızlı başlangıçta, bu veritabanını kullanacağız.
- Otomatik olarak sunucu adı, Yönetici Kullanıcı adı, yönetici parolası, kaynak grubu adı (zaten yerel bağlamda belirtilmemişse) ve kaynak grubunuzda aynı konumda
- Kalan sunucu yapılandırmalarının hizmet Varsayılanları: İşlem Katmanı (Burstable), işlem boyutu/SKU (B1MS), yedekleme saklama süresi (7 gün) ve MySQL sürümü (5,7)
- Ortak erişim bağımsız değişkenini kullanmak, güvenlik duvarı kuralları tarafından korunan genel erişime sahip bir sunucu oluşturmanıza imkan tanır. IP adresinizi, istemci makinenizden erişime izin vermek için güvenlik duvarı kuralı eklemek üzere sunarak.
- Komut yerel bağlamı kullandığından, sunucuyu kaynak grubunda ```wordpress-project``` ve bölgesinde oluşturacaktır ```eastus``` .


### <a name="build-your-wordpress-docker-image"></a>WordPress Docker görüntünüzü oluşturma

[En son WordPress](https://wordpress.org/download/) sürümünü indirin. Projeniz için yeni bir dizin oluşturun ```my-wordpress-app``` ve bu basit klasör yapısını kullanın

```
└───my-wordpress-app
    └───public
        ├───wp-admin
        │   ├───css
        . . . . . . .
        ├───wp-content
        │   ├───plugins
        . . . . . . .
        └───wp-includes
        . . . . . . .
        ├───wp-config-sample.php
        ├───index.php
        . . . . . . .
    └─── Dockerfile

```


```wp-config-sample.php``` ```wp-config.php``` Bu kod parçacığı ile 21 ile 32 arasındaki satırları yeniden adlandırın ve değiştirin. Aşağıdaki kod parçacığı, Kubernetes bildirim dosyasından veritabanı konağını, Kullanıcı adını ve parolayı okuyor.

```php
//Using environment variables for DB connection information

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */

$connectstr_dbhost = getenv('DATABASE_HOST');
$connectstr_dbusername = getenv('DATABASE_USERNAME');
$connectstr_dbpassword = getenv('DATABASE_PASSWORD');

/** MySQL database name */
define('DB_NAME', 'flexibleserverdb');

/** MySQL database username */
define('DB_USER', $connectstr_dbusername);

/** MySQL database password */
define('DB_PASSWORD',$connectstr_dbpassword);

/** MySQL hostname */
define('DB_HOST', $connectstr_dbhost);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/** SSL*/
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
```

### <a name="create-a-dockerfile"></a>Dockerfile oluşturma
Yeni bir Dockerfile oluşturun ve bu kod parçacığını kopyalayın. Bu Dockerfile, PHP ile Apache Web sunucusunu ayarlama ve mysqli uzantısını etkinleştirme.

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

### <a name="build-your-docker-image"></a>Docker görüntünüzü oluşturma
```my-wordpress-app```Komutunu kullanarak bir terminalde dizinde olduğunuzdan emin olun ```cd``` . Görüntüyü derlemek için aşağıdaki komutu çalıştırın:

``` bash

docker build --tag myblog:latest .

```

Görüntünüzü [Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) veya [Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md)'ye dağıtın.

> [!IMPORTANT]
>Azure Container regdistry (ACR) kullanıyorsanız, ```az aks update``` AKS KÜMESIYLE ACR hesabı eklemek için komutunu çalıştırın.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Kubernetes bildirim dosyası oluşturma

Bir Kubernetes bildirim dosyası, küme için, hangi kapsayıcı görüntülerinin çalıştırılacağı gibi istenen durumu tanımlar. Aşağıdaki YAML tanımında adlı bir bildirim dosyası oluşturalım `mywordpress.yaml` ve kopyalama.

>[!IMPORTANT]
> - ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]```Örnek olarak gerçek WordPress Docker görüntü adı ve etiketiyle değiştirin ```docker-hub-user/myblog:latest``` .
> - ```env```Aşağıdaki bölümü, ```SERVERNAME``` ```YOUR-DATABASE-USERNAME``` MySQL esnek sunucunuzun,, ile güncelleştirin ```YOUR-DATABASE-PASSWORD``` .

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-blog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-blog
  template:
    metadata:
      labels:
        app: wordpress-blog
    spec:
      containers:
      - name: wordpress-blog
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.mysql.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "flexibleserverdb"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - wordpress-blog
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: php-svc
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: wordpress-blog
```

## <a name="deploy-wordpress-to-aks-cluster"></a>AKS kümesine WordPress dağıtma
[Kubectl Apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) komutunu kullanarak uygulamayı dağıtın ve YAML bildiriminizde adı belirtin:

```console
kubectl apply -f mywordpress.yaml
```

Aşağıdaki örnek çıktıda başarıyla oluşturulan dağıtımlar ve hizmetler gösterilmektedir:

```output
deployment "wordpress-blog" created
service "php-svc" created
```

## <a name="test-the-application"></a>Uygulamayı test edin

Uygulama çalıştığında, bir Kubernetes hizmeti, uygulamanın ön ucuna internet 'e koyar. Bu işlemin tamamlanması birkaç dakika sürebilir.

İlerlemeyi izlemek için, [kubectl Get Service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) komutunu bağımsız değişkeniyle birlikte kullanın `--watch` .

```azurecli-interactive
kubectl get service wordpress-blog --watch
```

Başlangıçta *WordPress-blog* hizmeti IÇIN *dış IP* , *Beklemede* olarak gösterilir.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

*Dış IP* adresi *bekliyor* durumundan gerçek ortak IP adresi olarak değiştiğinde, `CTRL-C` izleme işlemini durdurmak için kullanın `kubectl` . Aşağıdaki örnek çıktıda, hizmete atanmış geçerli bir genel IP adresi gösterilmektedir:

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>WordPress 'e gözatamıyorum

WordPress yükleme sayfanızı görmek için hizmetinizin dış IP adresine bir Web tarayıcısı açın.

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="AKS ve MySQL esnek sunucusunda WordPress yükleme başarısı":::

>[!NOTE]
> - WordPress sitesi şu anda HTTPS kullanmıyor. [Kendi sertifikalarınız Ile TLS etkinleştirilmesi](../../aks/ingress-own-tls.md)önerilir.
> - Kümeniz için [http yönlendirmeyi](../../aks/http-application-routing.md) etkinleştirebilirsiniz.

## <a name="clean-up-the-resources"></a>Kaynakları temizleme

Azure ücretlerinden kaçınmak için gereksiz kaynakları temizlemeniz gerekir.  Kümeye artık ihtiyacınız yoksa [az group delete](/cli/azure/group#az_group_delete) komutunu kullanarak kaynak grubunu, kapsayıcı hizmetini ve ilgili tüm kaynakları kaldırın.

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> Kümeyi sildiğinizde, AKS kümesi tarafından kullanılan Azure Active Directory hizmet sorumlusu kaldırılmaz. Hizmet sorumlusunu kaldırma adımları için bkz. [AKS hizmet sorumlusuyla ilgili önemli noktalar ve silme](../../aks/kubernetes-service-principal.md#additional-considerations). Yönetilen bir kimlik kullandıysanız, kimlik platform tarafından yönetilir ve kaldırma gerektirmez.

## <a name="next-steps"></a>Sonraki adımlar

- AKS kümeniz için [Kubernetes web panosuna erişme](../../aks/kubernetes-dashboard.md) hakkında bilgi edinin
- [Kümenizi ölçeklendirmeyi](../../aks/tutorial-kubernetes-scale.md) öğrenin
- [MySQL esnek sunucunuzu](./quickstart-create-server-cli.md) yönetme hakkında bilgi edinin
- Veritabanı sunucunuz için [sunucu parametrelerini yapılandırmayı](./how-to-configure-server-parameters-cli.md) öğrenin.
