---
title: OpenShift sunucusuz kullanarak Azure Red Hat OpenShift 'e uygulama dağıtma
description: OpenShift sunucusuz kullanarak bir uygulamayı Azure Red Hat OpenShift 'e dağıtmayı öğrenin
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: Aro, OpenShift, Red hat, sunucusuz
ms.openlocfilehash: 6db87e752745e3df919c93b2ffc8144e5886b319
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532820"
---
# <a name="deploy-an-application-to-azure-red-hat-openshift-using-openshift-serverless"></a>OpenShift sunucusuz kullanarak Azure Red Hat OpenShift 'e uygulama dağıtma

Bu makalede, [OpenShift sunucusuz](https://www.openshift.com/learn/topics/serverless)kullanarak bir Azure Red Hat OpenShift kümesine uygulama dağıtırsınız. OpenShift sunucusuz, geliştiricilerin isteğe bağlı olarak sıfıra ölçeklenebilen veya ölçeklendirecek uygulamalar dağıtmalarına ve çalıştırmasına yardımcı olur ve kullanımda olmadığında kaynak tüketimini ortadan kaldırır.

Uygulama kodu, uygun çalışma zamanları ile birlikte bir kapsayıcıda paketlenebilir ve sunucusuz işlevsellik, bir olay tarafından tetiklendiklerinde uygulama kapsayıcılarını başlatır. Uygulamalar, kendi uygulamalarınızdaki olaylar, birden çok sağlayıcıdan gelen bulut Hizmetleri, hizmet olarak yazılım (SaaS) sistemleri ve diğer hizmetler gibi çeşitli olay kaynakları tarafından tetiklenebilir.

Herhangi bir kapsayıcıyı sunucusuz bir biçimde dağıtmanın tüm yönlerini yönetmek doğrudan OpenShift arabirimine oluşturulmuştur. Geliştiriciler, Kapsayıcılı uygulamaların başlatılmasını hangi olayların, olay parametrelerini değiştirmek için birden çok yol ile görsel olarak tanımlayabilir. OpenShift sunucusuz uygulamalar, OpenShift işlem hatları, hizmet ağı ve Izleme gibi diğer OpenShift hizmetleriyle tümleştirilebilir ve tamamen sunucusuz uygulama geliştirme ve dağıtım deneyimi sunuyor.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

### <a name="install-the-knative-command-line-tool-kn"></a>Knative komut satırı aracını (kN) yükler

Makinenize uygun olan CLı 'nın en son sürümünü şuradan indirebilirsiniz <https://github.com/knative/client/releases/>

Azure Cloud Shell komutları çalıştırıyorsanız, Linux için en son Knative CLı 'yi indirin.

```azurecli-interactive
cd ~
wget https://github.com/knative/client/releases/download/v0.22.0/kn-linux-amd64

mkdir knative
chmod +x kn-linux-amd64
mv kn-linux-amd64 knative/kn
echo 'export PATH=$PATH:~/knative' >> ~/.bashrc && source ~/.bashrc
```

### <a name="launch-the-web-console"></a>Web konsolunu Başlat

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

## <a name="install-the-openshift-serverless-operator"></a>OpenShift sunucusuz işlecini yükler

OpenShift Web konsolunda oturum açtıktan sonra, *yönetici* perspektifinde olduğunuzdan emin olun. *Operatör hub 'ını* açın ve **OpenShift sunucusuz** işlecini bulun ve seçin.

![OpenShift sunucusuz işleci ara](media/serverless/serverless-operatorhub.png)

Sonra, **yükleme**' ye tıklayarak operatör yükleme sayfasını açın.

![İşleci yüklemek için Install 'a tıklayın](media/serverless/serverless-clickinstall.png)

Azure Red Hat OpenShift 'in küme sürümü için uygun *güncelleştirme kanalını* seçin ve işlecini `openshift-serverless` ad alanına yüklersiniz. Aşağı kaydırın ve **yüklensin**' e tıklayın.

![İşleç yükleme sayfası](media/serverless/serverless-installpage.png)

Birkaç dakika içinde, durum sayfası işlecin yüklendiğini ve kullanıma uygun olduğunu yansıtmalıdır. Devam etmek için **Görünüm işleci** düğmesine tıklayın.

![İşleç yüklendi ve kullanıma hazırlanıyor](media/serverless/serverless-installed.png)

## <a name="install-knative-serving"></a>Knative hizmeti 'ni yükler

OpenShift sunucusuz üzerinde herhangi bir kapsayıcıyı bir sunucusuz biçimde çalıştırma yeteneği, yukarı akış Knative kullanılarak mümkündür. Knative, Kubernetes 'i, sunucusuz metodolojisini kullanarak modern uygulamaları dağıtmaya, çalıştırmaya ve yönetmeye yönelik bir bileşen kümesi sağlar.

### <a name="create-an-instance-of-the-knative-serving"></a>Knative hizmet örneği oluşturma

`knative-serving`Sol üst taraftaki proje açılan listesine tıklayarak ad alanına geçin ve ardından, *belirtilen API 'ler* altında, *Knative hizmet* kartında **örnek oluştur** ' a tıklayın.

![Knative hizmet örneği oluşturmak için tıklayın](media/serverless/serverless-createknativeserving.png)

Varsayılanları koruyun ve **Oluştur** düğmesine tıklayarak *knative hizmet oluşturma* sayfasında aşağı kaydırın.

![Varsayılanları tut ve oluştur 'a tıklayın](media/serverless/serverless-createknativeserving2.png)

*Durum* sütunu **Ready** öğesine kadar bekleyin, OpenShift sunucusuz yüklenmelidir ve OpenShift sunucusuz bir proje oluşturmaya hazırsınız.

![Knative hizmeti hazırlanıyor](media/serverless/serverless-createknativeserving3.png)

## <a name="create-a-serverless-project"></a>Sunucusuz proje oluşturma

Adlı yeni bir proje oluşturmak için şu `demoserverless` komutu çalıştırın:

```azurecli-interactive
oc new-project demoserverless
```

Aşağıdakine benzer bir çıktı görmeniz gerekir:

```output
Now using project "demoserverless" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

Sol taraftaki menüdeki *yönetici* perspektifi yerine *Geliştirici* perspektifine geçin ve `demoserverless` Proje listesinden öğesini seçin. Daha sonra projenin *topoloji* sayfasında olmalısınız.

![Azure Red Hat OpenShift proje topolojisi](media/serverless/serverless-topology.png)

## <a name="deploying-using-the-web-console"></a>Web konsolunu kullanarak dağıtma

Uygulamayı dağıtmak için sunulan seçeneklerden *Git*' i seçin. Bu, sizi *Git 'Ten Içeri aktar* sayfasından bu işlemi kullanacaktır. `https://github.com/sclorg/django-ex.git` **Git deposu URL 'si** olarak kullanın. Örnek Web uygulaması, Python programlama dili kullanılarak uygulanır.

![Git 'ten Azure Red Hat OpenShift projesi](media/serverless/serverless-from-git.png)

> [!NOTE]
> OpenShift bu bir Python projesi olduğunu algılar ve uygun Oluşturucu görüntüsünü seçer.

*Kaynaklar* ' a gidin ve oluşturulacak kaynak türü olarak **knative Service** ' in seçildiğinden emin olun. Bu eylem, boşta olduğunda OpenShift sunucusuz ölçeklendirmeyi sağlayan bir tür dağıtım olan bir Knative hizmeti oluşturur.

![Azure Red Hat OpenShift projesi-Knative](media/serverless/serverless-knative.png)


Hazırsanız, sayfanın alt kısmındaki **Oluştur**' a tıklayın. Bu, uygulamanın yapısını ve dağıtımını yönetmek için kaynaklar oluşturur. Ardından, proje için topolojiye genel bakış 'a yönlendirilirsiniz.

Topolojiye genel bakış, dağıttığınız uygulamanın görsel bir gösterimini sağlar. Bu görünümle, genel uygulama yapısını görebilirsiniz.

Oluşturma işleminin tamamlanmasını bekleyin. Bu işlem birkaç dakika sürebilir. Derleme tamamlandıktan sonra, hizmetin sol alt köşesinde yeşil bir onay işareti görünür.

![Azure Red Hat OpenShift proje topolojisi-derleme başarılı](media/serverless/serverless-ready.png)

## <a name="see-your-application-scale"></a>Uygulamanızın ölçeğini görüntüleyin

Topoloji görünümünün en üstündeki *görüntüleme seçenekleri* listesinden *Pod sayısı*' na tıklayın. Pod sayısının sıfıra kadar ölçeğini daraltmak için bekleyin. Küçültme, birkaç dakika sürebilir.

![Azure Red Hat OpenShift proje topolojisi-sıfıra ölçeklendirildi](media/serverless/serverless-scaledtozero.png)

Knative Service panelinin sağ üst köşesindeki *URL 'Yi aç* simgesine tıklayın. Uygulama yeni bir sekmede açılır. Yeni tarayıcı sekmesini kapatın ve topoloji görünümüne geri dönün. Topoloji görünümünde, uygulamanızın isteğinize uyum sağlamak için bir pod 'a kadar ölçeklendirilmesine bakabilirsiniz. Birkaç dakika sonra uygulamanız, sıfıra doğru bir şekilde yeniden ölçeklenir.

![Azure Red Hat OpenShift proje topolojisi-tek ölçeklendirildi](media/serverless/serverless-scaledtoone.png)

## <a name="forcing-a-new-revision-and-setting-traffic-distribution"></a>Yeni bir düzeltme zorlama ve trafik dağıtımını ayarlama

Bir hizmetin yapılandırmasındaki her güncelleştirmeyle birlikte, hizmet için yeni bir düzeltme oluşturulur. Hizmet rotası, tüm trafiği varsayılan olarak en son hazır düzeltmeye yönlendirir. Hangi düzeltmenin trafiğin bir kısmını aldığından, bu davranışı değiştirebilirsiniz. Knative Hizmetleri trafik eşlemeye izin verir. Bu, bir hizmetin düzeltilme trafiğinin ayrılmış bir bölümüyle eşlenebileceği anlamına gelir. Trafik eşleme, belirli düzeltmeler için benzersiz URL 'Ler oluşturma seçeneği de sağlar.

*Topolojide*, ayrıntılarını görüntülemek için hizmetinizin içindeki düzeltmeye tıklayın. Pod halkasının altındaki ve ayrıntı panelinin en üstünde bulunan rozet olmalıdır `(REV)` . Yan bölmede, *kaynaklar* sekmesinde, aşağı kaydırarak hizmetinize ilişkin yapılandırmaya tıklayın.

![Azure Red Hat OpenShift proje topolojisi-Düzeltme](media/serverless/serverless-revdetails.png)

*YAML* sekmesine geçiş yaparak bir yapılandırma güncelleştirmesine zorlar ve değerini olarak düzenlemek için aşağı kaydırın `timeoutSeconds` `301` . Güncelleştirilmiş yapılandırmayı kaydetmek için **Kaydet** ' e tıklayın. Gerçek bir Dünya senaryosunda, bu yapılandırma güncelleştirmesi kapsayıcı görüntüsü etiketi güncelleştirilerek de tetiklenebilir.

![Yapılandırmayı güncelleştirerek yeni bir düzeltmeye zorla](media/serverless/serverless-confupdate.png)

*Topoloji* görünümüne geri dönerek yeni bir düzeltme dağıtıldığını görürsünüz. Rozet ile biten hizmete tıklayın `(KSVC)` ve **trafik dağıtımını ayarla** düğmesine tıklayın, artık trafiği hizmetin farklı düzeltmeleri arasında bölebilmelisiniz.

![Trafik dağıtımını ayarlama](media/serverless/serverless-trafficdist.png)

*Topoloji* görünümü artık trafiğin iki düzeltme arasında nasıl dağıtıldığını gösterir.

![Trafik dağıtımını gözden geçirme](media/serverless/serverless-trafficdist2.png)

## <a name="using-the-knative-command-line-tool-kn"></a>Knative komut satırı aracını kullanma (kN)

Önceki adımlarda, OpenShift sunucusuz ' a bir uygulama oluşturmak ve dağıtmak için OpenShift web konsolunu kullandınız. OpenShift sunucusuz, altında Knative çalıştırıyorsa, knative hizmetleri oluşturmak için Knative komut satırı aracını (kN) de kullanabilirsiniz.

> [!NOTE]
> `kn`CLI 'yi zaten yüklemediyseniz, bu makalenin Önkoşullar bölümündeki adımları izlediğinizden emin olun. Ayrıca OpenShift komut satırı aracını kullanarak oturum açtığınızdan emin olun `oc` .

Zaten üzerinde oluşturulmuş bir kapsayıcı görüntüsü kullanacağız `quay.io/rhdevelopers/knative-tutorial-greeter` .

### <a name="deploy-a-service"></a>Hizmeti dağıtma

Hizmeti dağıtmak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
kn service create greeter \
--image quay.io/rhdevelopers/knative-tutorial-greeter:quarkus \
--namespace demoserverless \
--revision-name greeter-v1
```

Aşağıdakine benzer bir çıktı görürsünüz.

```output
Creating service 'greeter' in namespace 'demoserverless':

  0.044s The Route is still working to reflect the latest desired specification.
  0.083s ...
  0.114s Configuration "greeter" is waiting for a Revision to become ready.
 10.420s ...
 10.489s Ingress has not yet been reconciled.
 10.582s Waiting for load balancer to be ready
 10.763s Ready to serve.

Service 'greeter' created to latest revision 'greeter-v1' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Şunu çalıştırarak projedeki yolların bir listesini alabilirsiniz:

```azurecli-interactive
kn route list
```

Ad alanındaki yolların bir listesini geri alacaksınız. Dağıtılan hizmeti görmek için URL 'YI bir tarayıcıda açın.

```output
NAME      URL                                                            READY
greeter   http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io   True
```

### <a name="deploy-a-new-version-of-the-service"></a>Hizmetin yeni bir sürümünü dağıtın

Aşağıdaki komutu çalıştırarak, `:latest` görüntü etiketini ve bir ortam değişkenini geçirerek uygulamanın yeni bir sürümünü dağıtın `MESSAGE_PREFIX` :

```azurecli-interactive
kn service update greeter \
 --image quay.io/rhdevelopers/knative-tutorial-greeter:latest \
 --namespace demoserverless \
 --env MESSAGE_PREFIX=GreeterV2 \
 --revision-name greeter-v2
```

Yeni bir düzeltmenin dağıtıldığı bir onayı geri alacaksınız `greeter-v2` .

```output
Updating Service 'greeter' in namespace 'demoserverless':

  5.029s Traffic is not yet migrated to the latest revision.
  5.086s Ingress has not yet been reconciled.
  5.190s Waiting for load balancer to be ready
  5.332s Ready to serve.

Service 'greeter' updated to latest revision 'greeter-v2' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Tüm düzeltmelerin ve bunların trafik dağıtımının bir listesini görüntülemek için aşağıdaki komutu çalıştırın:

```azurecli-interactive
kn revision list
```

Aşağıdakine benzer bir liste alacaksınız. Yeni düzeltmenin trafiğin %100 ' unun elde olduğunu unutmayın.

```output
NAME            SERVICE   TRAFFIC   TAGS   GENERATION   AGE     CONDITIONS   READY   REASON
greeter-v2      greeter   100%             2            90s     3 OK / 4     True
greeter-v1      greeter                    1            5m32s   3 OK / 4     True
```

### <a name="bluegreen-and-canary-deployments"></a>Mavi/yeşil ve kanarya dağıtımları

Varsayılan olarak, yeni bir düzeltme dağıtıldığında trafiğin %100 ' u alınır. Uygulamanın eski sürümüne hızlıca geri dönebileceğiniz mavi/yeşil bir dağıtım stratejisi uygulamak istediğinizi varsayalım. Knative bu kadar kolay hale gelir.

Trafiğin %100 ' üne gönderdiği üç trafik etiketi oluşturmak için hizmeti güncelleştirin.

- **geçerli**: geçerli dağıtılmış sürümdeki noktaları
- **Önceki: önceki** sürümdeki noktaları
- **en son**: her zaman en son sürüme işaret eder

```azurecli-interactive
kn service update greeter \
   --tag greeter-v2=current \
   --tag greeter-v1=prev \
   --tag @latest=latest
```

Aşağıdakine benzer bir onay alacaksınız.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  0.037s Ingress has not yet been reconciled.
  0.121s Waiting for load balancer to be ready
  0.287s Ready to serve.

Service 'greeter' with latest revision 'greeter-v2' (unchanged) is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Aşağıdaki komutu kullanarak rotaları listeleyin:

```azurecli-interactive
kn route describe greeter
```

Trafik dağıtımı ile birlikte her bir etiketlerin URL 'sini gösteren bir çıkış alırsınız.

```output
Name:       greeter
Namespace:  demoserverless
Age:        10m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
  100%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Önceki sürüme hızlıca geri dönmek istediğinizi varsayalım. trafik dağıtımını, önceki etikete trafiğin %100 ' ü gönderecek şekilde güncelleştirebilirsiniz:

```azurecli-interactive
kn service update greeter --traffic current=0 --traffic prev=100
```

Aşağıdaki komutu kullanarak yolları listeleyerek yeniden kontrol edin:

```azurecli-interactive
kn route describe greeter
```

Trafik dağıtımının %100 ' ının önceki sürüme gittiğini gösteren bir çıkış alırsınız.

```output
Name:       greeter
Namespace:  demoserverless
Age:        19m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
    0%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
  100%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Tarayıcınızda ana yolu (Bu durumda) yenilerken trafik dağıtımı ile gezinebilirsiniz `http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io` .

## <a name="clean-up-resources"></a>Kaynakları temizleme

Uygulamayı tamamladığınızda, projeyi silmek için aşağıdaki komutu çalıştırabilirsiniz:

```azurecli-interactive
oc delete project demoserverless
```

Ayrıca, [öğretici: Azure Red Hat OpenShift 4 kümesini silme](./tutorial-delete-cluster.md)bölümündeki yönergeleri izleyerek kümeyi silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda şunları öğrendiniz:
> [!div class="checklist"]
>
> * OpenShift sunucusuz operatörü ve Knative hizmeti 'ni yükler
> * Web konsolunu kullanarak sunucusuz bir proje dağıtma
> * Knative CLı (kN) kullanarak sunucusuz bir proje dağıtma
> * Knative CLI (kN) kullanarak mavi/yeşil dağıtımları ve kanarya dağıtımlarını yapılandırma

[OpenShift sunucusuz](https://www.openshift.com/learn/topics/serverless)kullanarak Azure Red Hat OpenShift 'te sunucusuz, olay odaklı uygulamalar oluşturma ve dağıtma hakkında daha fazla bilgi edinin, [OpenShift sunucusuz ile çalışmaya başlama](https://docs.openshift.com/container-platform/4.6/serverless/serverless-getting-started.html) ve [sunucusuz uygulamalar oluşturma ve yönetme](https://docs.openshift.com/container-platform/4.6/serverless/serving-creating-managing-apps.html) belgelerini izleyin.
