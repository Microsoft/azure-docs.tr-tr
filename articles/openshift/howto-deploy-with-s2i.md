---
title: Kaynaktan Azure Red Hat OpenShift 'e uygulama dağıtma
description: Kaynak koddan görüntü (S2I) derleme stratejisini kullanarak bir uygulamayı Azure Red Hat Openshıft 'e dağıtmayı öğrenin
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: Aro, OpenShift, Red hat, s2i, kaynak to Image
ms.openlocfilehash: 270e5d915e2a77cdb4377a616abc97a836a09710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559245"
---
# <a name="deploy-an-application-from-source-to-azure-red-hat-openshift"></a>Kaynaktan Azure Red Hat OpenShift 'e uygulama dağıtma

Bu makalede, kaynak koddan bir Azure Red Hat OpenShift kümesine, kaynak-görüntü (S2I) derlemesi kullanarak bir uygulamayı dağıtırsınız. [Kaynaktan görüntü (S2I)](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html#builds-strategy-s2i-build_understanding-image-builds) , kaynak koddan tekrarlanabilir kapsayıcı görüntüleri oluşturmak için bir derleme işlemidir. S2I, ekleme kaynak kodu tarafından bir kapsayıcı görüntüye göre hazırlama öncesi görüntüler üretir ve kapsayıcının bu kaynak kodu yürütmeye hazırlamasına izin verir. OpenShift 'in kaynaktan bir uygulama oluşturmasını sağlayabilirsiniz, bu nedenle her değişiklik ile bir kapsayıcı oluşturmanız gerekmez. OpenShift daha sonra kaynak kodu değiştiğinde yeni sürümleri otomatik olarak oluşturup dağıtabilir.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

## <a name="create-a-project"></a>Proje oluşturma

Adlı yeni bir proje oluşturmak için şu `demoproject` komutu çalıştırın:

```azurecli-interactive
oc new-project demoproject
```

Aşağıdakine benzer bir çıktı görmeniz gerekir:

```output
Now using project "demoproject" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

## <a name="launch-the-web-console"></a>Web konsolunu Başlat

Aşağıdakileri çalıştırarak küme Web Konsolu URL 'sini öğrenin:

```azurecli-interactive
 az aro show \
    --name <cluster name> \
    --resource-group <resource group> \
    --query "consoleProfile.url" -o tsv
```

Buna benzer bir URL almalısınız.

```output
https://console-openshift-console.apps.wzy5hg7x.eastus.aroapp.io/
```

Konsol URL 'sini bir tarayıcıda başlatın ve kimlik bilgilerini kullanarak oturum açın `kubeadmin` .

:::image type="content" source="media/login.png" alt-text="Azure Red Hat OpenShift oturum açma ekranı":::

Sol taraftaki menüdeki *yönetici* perspektifi yerine *Geliştirici* perspektifine geçin ve `demoproject` Proje listesinden öğesini seçin. Daha sonra projenin *topoloji* sayfasında olmalısınız.

:::image type="content" source="media/s2i/project-topology.png" alt-text="Azure Red Hat OpenShift proje topolojisi":::

Proje boş olduğu için hiçbir iş yükü bulunamamalıdır ve bir uygulamayı nasıl dağıtacağınızı gösteren çeşitli seçeneklerle karşılaşırsınız.

## <a name="deploying-using-the-web-console"></a>Web konsolunu kullanarak dağıtma

Uygulamayı dağıtmak için sunulan seçeneklerden *Git*' i seçin. Bu, sizi *Git 'Ten Içeri aktar* sayfasından bu işlemi kullanacaktır. `https://github.com/sclorg/django-ex.git` **Git deposu URL 'si** olarak kullanın. Örnek Web uygulaması, Python programlama dili kullanılarak uygulanır.

:::image type="content" source="media/s2i/from-git.png" alt-text="Git 'ten Azure Red Hat OpenShift projesi":::

> [!NOTE]
> OpenShift bu bir Python projesi olduğunu algılar ve uygun Oluşturucu görüntüsünü seçer.

*Gelişmiş seçeneklere* ilerleyin ve **uygulamaya bir yol oluştur** seçeneğinin işaretli olduğundan emin olun. Bu eylem, bir hizmeti dışarıdan erişilebilen bir ana bilgisayar adına sunarak bir hizmeti açığa çıkarmak için bir OpenShift rotası oluşturur.

:::image type="content" source="media/s2i/from-git-route.png" alt-text="Git-Route kurulumundan Azure Red Hat OpenShift projesi":::

Hazırsanız, sayfanın alt kısmındaki **Oluştur**' a tıklayın. Bu, uygulamanın yapısını ve dağıtımını yönetmek için kaynaklar oluşturur. Ardından, proje için topolojiye genel bakış 'a yönlendirilirsiniz.

:::image type="content" source="media/s2i/demo-app-topology.png" alt-text="Git topolojisinden Azure Red Hat OpenShift projesi":::

Topolojiye genel bakış, dağıttığınız uygulamanın görsel bir gösterimini sağlar. Bu görünümle, genel uygulama yapısını görebilirsiniz.

Git simgesine, uygulamayı kaynak kodunun oluşturulduğu git deposuna götürebilmeniz için tıklanmış olabilirsiniz. Sol alt tarafta gösterilen simge, uygulamanın yapı durumunu gösterir. Bu simgeye tıkladığınızda derleme ayrıntıları bölümüne götürür. Uygulamanın yolları açığa çıkarmış olması halinde, üst sağ taraftaki simgeye tıklanınca, oluşturulan uygulama yolunun URL 'sini açabilirsiniz.

Uygulama ölçeklendiriliyor veya azaltılken, piyasaya çıkarma ve pods 'yi yeniden oluşturmak için topoloji görünümündeki uygulama temsili, ne olduğunu gösteren gerçek zamanlı bir görünüm sağlayacak şekilde canlandırılır.

Uygulama simgesine tıklandığında aşağıda gösterildiği gibi daha fazla ayrıntı sunulacaktır.

:::image type="content" source="media/s2i/demo-app-details.png" alt-text="Git 'ten Azure Red Hat OpenShift projesi-Ayrıntılar":::

## <a name="viewing-the-builder-logs"></a>Oluşturucu günlüklerini görüntüleme

Derleme başladıktan sonra, *kaynaklar* panelinde gösterilen *günlükleri görüntüle* bağlantısına tıklayın.

:::image type="content" source="media/s2i/demo-app-build-logs.png" alt-text="Git-derleme günlüklerinden Azure Red Hat OpenShift projesi":::

Bu işlem, çalışırken yapı ilerlemesini izlemenize olanak sağlar. Bu durumda Python görüntüsü olan Python, uygulama kaynak kodunu OpenShift iç görüntü kayıt defterine göndermeden önce son görüntüye ekleyecektir. "Gönderme başarılı" iletisinin son iletisini gördüğünüzde yapı başarıyla tamamlanacaktır.

## <a name="accessing-the-application"></a>Uygulamaya erişme

Uygulama görüntüsünü oluşturma işlemi tamamlandıktan sonra, dağıtılır.

Projenin topoloji görünümüne dönmek için sol taraftaki menü çubuğunda *topoloji* ' ye tıklayın. Web konsolunu kullanarak uygulamayı oluşturduğunuzda, uygulama için otomatik olarak bir *yol* oluşturulur ve küme dışında kullanıma sunulacaktır. Web tarayıcısından uygulamaya erişmek için kullanılabilecek URL, daha önce görüntülediğiniz uygulamanın *kaynaklar* sekmesinde görünür.

Topoloji görünümünden, halkasının sağ üst simgesine tıklayarak dağıtılan uygulamanın URL 'sine ulaşabilirsiniz. Dağıtım tamamlandığında, simgeye tıklayın ve dağıttığınız uygulamayı görmeniz gerekir.

:::image type="content" source="media/s2i/demo-app-browse.png" alt-text="Git 'ten Azure Red Hat OpenShift projesi-tarama uygulaması":::

## <a name="deploying-using-the-command-line"></a>Komut satırı kullanarak dağıtma

Web konsolunu kullanarak bir uygulamayı nasıl dağıtacağınızı öğrendiniz, şimdi aynı Web uygulamasını dağıtmanıza izin verir, ancak bu kez `oc` komut satırı aracını kullanarak.

Projeyi silmek ve baştan başlamak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
oc delete project demoproject
```

Silindiğini belirten bir onay iletisi almalısınız `demoproject` .

```output
project.project.openshift.io "demoproject" deleted
```

Şunu `demoproject` çalıştırarak yeniden oluşturun:

```azurecli-interactive
oc new-project demoproject
```

Proje içinde, GitHub 'da kaynaktan yeni bir uygulama oluşturun ve belirtilen Python 'un en son sürümü için S2I oluşturucusunu belirtin.

```azurecli-interactive
oc new-app python:latest~https://github.com/sclorg/django-ex.git
```

Bunun şuna benzer bir çıktı görüntülemesi gerekir:

```output
--> Found image 8ec6f0d (4 weeks old) in image stream "openshift/python" under tag "latest" for "python:latest"

    Python 3.8
    ----------
   [...]

    Tags: builder, python, python38, python-38, rh-python38

    * A source build using source code from https://github.com/sclorg/django-ex.git will be created
      * The resulting image will be pushed to image stream tag "django-ex:latest"
      * Use 'oc start-build' to trigger a new build
    * This image will be deployed in deployment config "django-ex"
    * Port 8080/tcp will be load balanced by service "django-ex"
      * Other containers can access this service through the hostname "django-ex"

--> Creating resources ...
    imagestream.image.openshift.io "django-ex" created
    buildconfig.build.openshift.io "django-ex" created
    deploymentconfig.apps.openshift.io "django-ex" created
    service "django-ex" created
--> Success
    Build scheduled, use 'oc logs -f bc/django-ex' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/django-ex'
    Run 'oc status' to view your app.
```

Openshıft, uygulamanın adı olarak git deposunun adını kullanacaktır. Aşağıdakileri çalıştırarak derleme ve dağıtım durumunu gözden geçirin:

```azurecli-interactive
oc status
```

Derleme ve dağıtım tamamlandığında, için benzer bir çıktı görmeniz gerekir.

```output
In project demoproject on server https://api.wzy5hg7x.eastus.aroapp.io:6443

svc/django-ex - 172.30.200.50:8080
  dc/django-ex deploys istag/django-ex:latest <-
    bc/django-ex source builds https://github.com/sclorg/django-ex.git on openshift/python:latest
    deployment #1 deployed about a minute ago - 1 pod


2 infos identified, use 'oc status --suggest' to see details.
```

Uygulamayı OpenShift kümesi dışında kullanıma sunmak için şunu çalıştırarak bir yol oluşturmanız gerekir:

```azurecli-interactive
oc expose service/django-ex
```

Bir onay almalısınız.

```output
route.route.openshift.io/django-ex exposed
```

Şunu çalıştırarak URL 'YI alın:

```azurecli-interactive
oc get route django-ex
```

Dağıtılan uygulamaya gitmek için kullanabileceğiniz yola atanan ana bilgisayar adını geri almalısınız.

```output
NAME        HOST/PORT                                              PATH   SERVICES    PORT       TERMINATION   WILDCARD
django-ex   django-ex-demoproject.apps.wzy5hg7x.eastus.aroapp.io          django-ex   8080-tcp                 None
```

## <a name="triggering-a-new-binary-build"></a>Yeni bir ikili derleme tetikleniyor

Uygulama üzerinde çalışırken büyük olasılıkla değişiklikler yapmak ve bunların dağıtıldığını görmek isteyeceksiniz. Her kod işlemesinde yeni bir derlemeyi ve dağıtımı tetikleyecek bir Web kancasını kolayca ayarlayabilirsiniz. Ancak, bazı durumlarda her kod değişikliğini depoya göndermek zorunda kalmadan değişiklikleri görmek isteyebilirsiniz.

Değişiklikler üzerinde hızlı bir şekilde yineleme yaptığınız durumlarda, ikili derleme olarak adlandırılan öğeleri kullanabilirsiniz. Bu senaryoyu göstermek için, şunu çalıştırarak uygulamanın Git deposunu yerel olarak kopyalayın:

```azurecli-interactive
git clone https://github.com/sclorg/django-ex.git
```

Bu, `django-ex` uygulamanın kaynak kodunu içeren bir alt dizin oluşturur:

```output
Cloning into 'django-ex'...
remote: Enumerating objects: 980, done.
remote: Total 980 (delta 0), reused 0 (delta 0), pack-reused 980
Receiving objects: 100% (980/980), 276.23 KiB | 4.85 MiB/s, done.
Resolving deltas: 100% (434/434), done.
```

Alt dizine geçin:

```azurecli-interactive
cd django-ex
```

Tümleşik Azure Cloud Shell düzenleyicisini açın:

```azurecli-interactive
code welcome/templates/welcome/index.html
```

Aşağı kaydırın ve söyleyerek görüntülenen çizgiyi değiştirin `Welcome to your Django application on OpenShift` `Welcome to Azure Red Hat OpenShift` . Dosyayı kaydedin ve `...` sağ üstteki menüde bulunan düzenleyiciyi kapatın.

:::image type="content" source="media/s2i/cloudshell-editor.png" alt-text="Azure Red Hat OpenShift projesi git 'ten Azure Cloud Shell düzenleyicide uygulamayı düzenleme":::

Komutu çalıştırarak yeni bir derleme başlatın:

```azurecli-interactive
oc start-build django-ex --from-dir=. --wait
```

`--from-dir=.`Bayrağı geçirerek, OpenShift komut satırı, belirtilen dizinden kaynak kodu karşıya yükleyecek ve ardından derleme ve dağıtım işlemini başlatacak. Aşağıdakine benzer bir çıktı almalısınız ve birkaç dakika sonra, derleme tamamlanmalıdır.

```output
Uploading directory "." as binary input for the build ...
.
Uploading finished
build.build.openshift.io/django-ex-2 started
```

Tarayıcıyı uygulamayla yenilerseniz, güncelleştirilmiş başlığı görmeniz gerekir.

:::image type="content" source="media/s2i/demo-app-browse-updated.png" alt-text="Git 'ten Azure Red Hat OpenShift projesi-güncelleştirilmiş uygulamaya Gözat":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Uygulamayı tamamladığınızda, projeyi silmek için aşağıdaki komutu çalıştırabilirsiniz:

```azurecli-interactive
oc delete project demoproject
```

Ayrıca, [öğretici: Azure Red Hat OpenShift 4 kümesini silme](./tutorial-delete-cluster.md)bölümündeki yönergeleri izleyerek kümeyi silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda şunları öğrendiniz:
> [!div class="checklist"]
>
> * Proje oluşturma
> * Web konsolunu kullanarak kaynak koddan uygulama dağıtma
> * OpenShift komut satırını kullanarak kaynak koddan bir uygulama dağıtma
> * OpenShift komut satırını kullanarak ikili derleme tetikleyin

Kaynak-görüntü ve [diğer yapı stratejilerini](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html)kullanarak uygulama oluşturma ve dağıtma hakkında daha fazla bilgi edinin.
