---
title: 'Öğretici: Azure CLı kullanarak AKS kümesine PostgreSQL esnek sunucusu ile Docgo dağıtma'
description: PostgreSQL için Azure veritabanı-esnek sunucu ile AKS üzerinde hızlı bir şekilde Docgo oluşturmayı ve dağıtmayı öğrenin.
ms.service: postgresql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 12/10/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 9315e6fd7dd9880d20108e3f0ed28cd32904f1a3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791544"
---
# <a name="tutorial-deploy-django-app-on-aks-with-azure-database-for-postgresql---flexible-server"></a>Öğretici: PostgreSQL için Azure veritabanı ile AKS 'de Docgo uygulaması dağıtma-esnek sunucu

Bu hızlı başlangıçta, Azure CLı 'yı kullanarak Azure Kubernetes Service (AKS) kümesi için Azure veritabanı ' nı PostgreSQL için Azure veritabanı-esnek sunucu (Önizleme) ile birlikte dağıtırsınız.

**[Aks](../../aks/intro-kubernetes.md)** , kümeleri hızlı bir şekilde dağıtmanıza ve yönetmenize olanak tanıyan bir yönetilen Kubernetes hizmetidir. **[PostgreSQL Için Azure veritabanı-esnek sunucu (Önizleme)](overview.md)** , veritabanı yönetim işlevleri ve yapılandırma ayarları üzerinde daha ayrıntılı denetim ve esneklik sağlamak için tasarlanan tam olarak yönetilen bir veritabanı hizmetidir.

> [!NOTE]
> - PostgreSQL için Azure veritabanı esnek sunucusu şu anda genel önizlemede
> - Bu hızlı başlangıçta, Kubernetes kavramlarını, Depgo ve PostgreSQL hakkında temel bilgiler yer aldığını varsayar.

## <a name="pre-requisites"></a>Ön koşullar
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

- Bash ortamından yararlanarak [Azure Cloud Shell](../../cloud-shell/quickstart.md)’i kullanın.

   [![Ekli başlatma](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell’i başlatma")](https://shell.azure.com)  
- Tercih ederseniz, CLı başvuru komutlarını çalıştırmak için Azure CLı 'yı [yükleyebilirsiniz](/cli/azure/install-azure-cli) .
  - Yerel yükleme kullanıyorsanız [az login](/cli/azure/reference-index#az_login) komutunu kullanarak Azure CLI ile oturum açın.  Kimlik doğrulama işlemini tamamlamak için terminalinizde görüntülenen adımları izleyin.  Ek oturum açma seçenekleri için bkz. [Azure CLI ile oturum açma](/cli/azure/authenticate-azure-cli).
  - İstendiğinde, ilk kullanımda Azure CLI uzantılarını yükleyin.  Uzantılar hakkında daha fazla bilgi için bkz. [Azure CLI ile uzantıları kullanma](/cli/azure/azure-cli-extensions-overview).
  - Yüklü sürümü ve bağımlı kitaplıkları bulmak için [az version](/cli/azure/reference-index?#az_version) komutunu çalıştırın. En son sürüme yükseltmek için [az upgrade](/cli/azure/reference-index?#az_upgrade) komutunu çalıştırın. Bu makale, Azure CLı 'nin en son sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

> [!NOTE]
> Bu hızlı başlangıçta komutları yerel olarak çalıştırıyorsanız (Azure Cloud Shell yerine), komutları yönetici olarak çalıştırın.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği mantıksal bir gruptur. *Eastus* konumunda [az Group Create] [az-Group-Create] komutunu kullanarak *docgo-Project* kaynak grubu oluşturalım.

```azurecli-interactive
az group create --name django-project --location eastus
```

> [!NOTE]
> Kaynak grubunun konumu, kaynak grubu meta verilerinin depolandığı yerdir. Ayrıca, kaynak oluşturma sırasında başka bir bölge belirtmezseniz kaynaklarınızın Azure 'da çalıştığı yerdir.

Aşağıdaki örnek çıktıda başarıyla oluşturulan kaynak grubu gösterilmektedir:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/django-project",
  "location": "eastus",
  "managedBy": null,
  
  "name": "django-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>AKS kümesi oluşturma

AKS kümesi oluşturmak için [az aks create](/cli/azure/aks#az_aks_create) komutunu kullanın. Aşağıdaki örnekte, bir düğüm ile *myAKSCluster* adlı bir küme oluşturulmuştur. Bu işlem birkaç dakika sürer.

```azurecli-interactive
az aks create --resource-group django-project --name djangoappcluster --node-count 1 --generate-ssh-keys
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
az aks get-credentials --resource-group django-project --name djangoappcluster
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

## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>PostgreSQL için Azure veritabanı oluşturma-esnek sunucu
[Az postgreSQL esnek-sunucu oluştur](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create)komutuyla esnek bir sunucu oluşturun. Aşağıdaki komut, Azure CLı 'nın yerel bağlamından hizmet varsayılanlarını ve değerlerini kullanarak bir sunucu oluşturur:

```azurecli-interactive
az postgres flexible-server create --public-access <YOUR-IP-ADDRESS>
```

Oluşturulan sunucu aşağıdaki özniteliklere sahiptir:
- Sunucu ilk sağlandığında yeni boş bir veritabanı ```postgres``` oluşturulur. Bu hızlı başlangıçta, bu veritabanını kullanacağız.
- Otomatik olarak sunucu adı, Yönetici Kullanıcı adı, yönetici parolası, kaynak grubu adı (zaten yerel bağlamda belirtilmemişse) ve kaynak grubunuzda aynı konumda
- Kalan sunucu yapılandırmalarının hizmet Varsayılanları: İşlem Katmanı (Genel Amaçlı), işlem boyutu/SKU 'SU (2Vçekirdekler kullanan Standard_D2s_v3), yedekleme bekletme süresi (7 gün) ve PostgreSQL sürümü (12)
- Ortak erişim bağımsız değişkenini kullanmak, güvenlik duvarı kuralları tarafından korunan genel erişime sahip bir sunucu oluşturmanıza imkan tanır. IP adresinizi, istemci makinenizden erişime izin vermek için güvenlik duvarı kuralı eklemek üzere sunarak.
- Komut yerel bağlamı kullandığından, sunucuyu kaynak grubunda ```django-project``` ve bölgesinde oluşturacaktır ```eastus``` .


## <a name="build-your-django-docker-image"></a>Docgo Docker görüntünüzü oluşturma

Yeni bir [docgo uygulaması](https://docs.djangoproject.com/en/3.1/intro/) oluşturun veya var olan docgo projenizi kullanın. Kodunuzun bu klasör yapısında olduğundan emin olun.

```
└───my-djangoapp
    └───views.py
    └───models.py
    └───forms.py
    ├───templates
          . . . . . . .
    ├───static
         . . . . . . .
└───my-django-project
    └───settings.py
    └───urls.py
    └───wsgi.py
        . . . . . . .
    └─── Dockerfile
    └─── requirements.txt
    └─── manage.py
    
```
```ALLOWED_HOSTS``` ```settings.py``` Docgo uygulamasının Kubernetes uygulamasına atanan dış IP 'yi kullandığından emin olmak için ' de güncelleştirin.

```python
ALLOWED_HOSTS = ['*']
```

```DATABASES={ }```Dosyadaki güncelleştirme bölümü ```settings.py``` . Aşağıdaki kod parçacığı, Kubernetes bildirim dosyasından veritabanı konağını, Kullanıcı adını ve parolayı okuyor.

```python
DATABASES={
   'default':{
      'ENGINE':'django.db.backends.postgresql_psycopg2',
      'NAME':os.getenv('DATABASE_NAME'),
      'USER':os.getenv('DATABASE_USER'),
      'PASSWORD':os.getenv('DATABASE_PASSWORD'),
      'HOST':os.getenv('DATABASE_HOST'),
      'PORT':'5432',
      'OPTIONS': {'sslmode': 'require'}
   }
}
```

### <a name="generate-a-requirementstxt-file"></a>requirements.txt dosyası oluşturma
```requirements.txt```Docgo uygulamasının bağımlılıklarını listelemek için bir dosya oluşturun. Örnek bir dosya aşağıda verilmiştir ```requirements.txt``` . [``` pip freeze > requirements.txt```](https://pip.pypa.io/en/stable/reference/pip_freeze/)Mevcut uygulamanız için bir requirements.txt dosyası oluşturmak için kullanabilirsiniz.

``` text
Django==2.2.17
postgres==3.0.0
psycopg2-binary==2.8.6
psycopg2-pool==1.1
pytz==2020.4
```

### <a name="create-a-dockerfile"></a>Dockerfile oluşturma
Adlı yeni bir dosya oluşturun ```Dockerfile``` ve aşağıdaki kod parçacığını kopyalayın. Bu Dockerfile, Python 3,8 ' i ayarlama ve requirements.txt dosyasında listelenen tüm gereksinimleri yükleme.

```docker
# Use the official Python image from the Docker Hub
FROM python:3.8.2

# Make a new directory to put our code in.
RUN mkdir /code

# Change the working directory.
WORKDIR /code

# Copy to code folder
COPY . /code/

# Install the requirements.
RUN pip install -r requirements.txt

# Run the application:
CMD python manage.py runserver 0.0.0.0:8000
```

### <a name="build-your-image"></a>Görüntünüzü oluşturma
```my-django-app```Komutunu kullanarak bir terminalde dizinde olduğunuzdan emin olun ```cd``` . Bültenin Pano görüntünüzü derlemek için aşağıdaki komutu çalıştırın:

``` bash

docker build --tag myblog:latest .

```

Görüntünüzü [Docker Hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) veya [Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md)'ye dağıtın.

> [!IMPORTANT]
>Azure Container regdistry (ACR) kullanıyorsanız, ```az aks update``` AKS KÜMESIYLE ACR hesabı eklemek için komutunu çalıştırın.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g django-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Kubernetes bildirim dosyası oluşturma

Bir Kubernetes bildirim dosyası, küme için, hangi kapsayıcı görüntülerinin çalıştırılacağı gibi istenen durumu tanımlar. Aşağıdaki YAML tanımında adlı bir bildirim dosyası oluşturalım ```djangoapp.yaml``` ve kopyalama.

>[!IMPORTANT]
> - ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]```Örneğin, gerçek Docgo Docker görüntü adı ve etiketiyle değiştirin ```docker-hub-user/myblog:latest``` .
> - ```env```Aşağıdaki bölümü, ```SERVERNAME``` ```YOUR-DATABASE-USERNAME``` ```YOUR-DATABASE-PASSWORD``` Postgres esnek sunucunuzun,, ile güncelleştirin.


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: django-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: django-app
  template:
    metadata:
      labels:
        app: django-app
    spec:
      containers:
      - name: django-app
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.postgres.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "postgres"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - django-app
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: python-svc
spec:
  type: LoadBalancer
  ports:
    - port: 8000
  selector:
    app: django-app
```

## <a name="deploy-django-to-aks-cluster"></a>AKS kümesine Docgo dağıtma
[Kubectl Apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) komutunu kullanarak uygulamayı dağıtın ve YAML bildiriminizde adı belirtin:

```console
kubectl apply -f djangoapp.yaml
```

Aşağıdaki örnek çıktıda başarıyla oluşturulan dağıtımlar ve hizmetler gösterilmektedir:

```output
deployment "django-app" created
service "python-svc" created
```

Bir dağıtım ```django-app``` , dağıtımınız üzerinde uygulama için hangi görüntülerin kullanılacağı, Pod ve pod yapılandırmasının sayısı gibi bilgileri açıklar. Uygulamayı bir ```python-svc``` dış IP aracılığıyla göstermek için bir hizmet oluşturulur.

## <a name="test-the-application"></a>Uygulamayı test edin

Uygulama çalıştığında, bir Kubernetes hizmeti, uygulamanın ön ucuna internet 'e koyar. Bu işlemin tamamlanması birkaç dakika sürebilir.

İlerlemeyi izlemek için, [kubectl Get Service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) komutunu bağımsız değişkeniyle birlikte kullanın `--watch` .

```azurecli-interactive
kubectl get service django-app --watch
```

Başlangıçta *docgo-App* hizmeti IÇIN *dış IP* , *Beklemede* olarak gösterilir.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
django-app   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

*Dış IP* adresi *bekliyor* durumundan gerçek ortak IP adresi olarak değiştiğinde, `CTRL-C` izleme işlemini durdurmak için kullanın `kubectl` . Aşağıdaki örnek çıktıda, hizmete atanmış geçerli bir genel IP adresi gösterilmektedir:

```output
django-app  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Şimdi, hizmetinizin dış IP adresi için bir Web tarayıcısı açın Docgo uygulamasını görüntüleyin.  

>[!NOTE]
> - Şu anda Docgo sitesi HTTPS kullanmıyor. [Kendi sertifikalarınız Ile TLS etkinleştirilmesi](../../aks/ingress-own-tls.md)önerilir.
> - Kümeniz için [http yönlendirmeyi](../../aks/http-application-routing.md) etkinleştirebilirsiniz. Http yönlendirme etkinleştirildiğinde, AKS kümenizde bir giriş denetleyicisi yapılandırır. Uygulamalar dağıtıldığında, çözüm uygulama uç noktaları için genel olarak erişilebilen DNS adları da oluşturur.

## <a name="run-database-migrations"></a>Veritabanı geçişlerini Çalıştır

Herhangi bir docgo uygulaması için veritabanı geçişini çalıştırmanız veya statik dosyalar toplamanız gerekir. Kullanarak bu docgo kabuğu komutlarını çalıştırabilirsiniz ```$ kubectl exec <pod-name> -- [COMMAND]``` .  Komutu çalıştırmadan önce, kullanarak Pod adını bulmanız gerekir ```kubectl get pods``` . 

```bash
$ kubectl get pods
```

Aşağıdakine benzer bir çıktı görürsünüz
```output
NAME                             READY   STATUS          RESTARTS   AGE
django-app-5d9cd6cd8-l6x4b     1/1     Running              0       2m
```

Pod adı bulunduktan sonra, komutuyla docgo veritabanı geçişlerini çalıştırabilirsiniz ```$ kubectl exec <pod-name> -- [COMMAND]``` . Burada ```/code/``` , proje için çalışma dizini ```Dockerfile``` yukarıda tanımlanır.

```bash
$ kubectl exec django-app-5d9cd6cd8-l6x4b -- python /code/manage.py migrate
```

Çıktı şöyle görünür 
```output 
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  . . . . . . 
```

Sorunlarla karşılaşırsanız, ```kubectl logs <pod-name>```  uygulamanız tarafından hangi özel durumun olduğunu görmek için lütfen çalıştırın. Uygulama başarıyla çalışıyorsa, çalışırken aşağıdakine benzer bir çıktı görürsünüz ```kubectl logs``` .

```output
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
December 08, 2020 - 23:24:14
Django version 2.2.17, using settings 'django_postgres_app.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

## <a name="clean-up-the-resources"></a>Kaynakları temizleme

Azure ücretlerinden kaçınmak için gereksiz kaynakları temizlemeniz gerekir.  Kümeye artık ihtiyacınız yoksa [az group delete](/cli/azure/group#az_group_delete) komutunu kullanarak kaynak grubunu, kapsayıcı hizmetini ve ilgili tüm kaynakları kaldırın.

```azurecli-interactive
az group delete --name django-project --yes --no-wait
```

> [!NOTE]
> Kümeyi sildiğinizde, AKS kümesi tarafından kullanılan Azure Active Directory hizmet sorumlusu kaldırılmaz. Hizmet sorumlusunu kaldırma adımları için bkz. [AKS hizmet sorumlusuyla ilgili önemli noktalar ve silme](../../aks/kubernetes-service-principal.md#additional-considerations). Yönetilen bir kimlik kullandıysanız, kimlik platform tarafından yönetilir ve kaldırma gerektirmez.

## <a name="next-steps"></a>Sonraki adımlar

- AKS kümeniz için [Kubernetes web panosuna erişme](../../aks/kubernetes-dashboard.md) hakkında bilgi edinin
- [Sürekli dağıtımı nasıl etkinleştireceğinizi](../../aks/deployment-center-launcher.md) öğrenin
- [Kümenizi ölçeklendirmeyi](../../aks/tutorial-kubernetes-scale.md) öğrenin
- [Postgres esnek sunucunuzu](./quickstart-create-server-cli.md) yönetme hakkında bilgi edinin
- Veritabanı sunucunuz için [sunucu parametrelerini yapılandırmayı](./howto-configure-server-parameters-using-cli.md) öğrenin.
