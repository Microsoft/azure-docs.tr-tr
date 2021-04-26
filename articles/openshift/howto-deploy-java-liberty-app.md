---
title: Azure Red Hat OpenShift 4 kümesinde açık Liberty/WebSphere özgürlük ile Java uygulaması dağıtma
description: Azure Red Hat OpenShift 4 kümesinde açık Liberty/WebSphere özgürlük ile bir Java uygulaması dağıtın.
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: Java, jakartaee, JavaEE, mikro profil, açık-Liberty, WebSphere-Liberty, Aro, OpenShift, Red Hat
ms.openlocfilehash: 2a308c7de754f395a3ef8a1bd97ed2441d27d21d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783584"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Azure Red Hat OpenShift 4 kümesinde açık Liberty/WebSphere özgürlük ile Java uygulaması dağıtma

Bu kılavuzda, açık serbest bir/Web Sphere serbest çalışma zamanında Java, Java EE, [Jakarta](https://jakarta.ee/)veya [mikro profil](https://microprofile.io/) uygulamanızın nasıl çalıştırılacağı ve sonra kapsayıcı uygulamayı açık serbest bırakma Işlecini kullanarak bir Azure Red Hat OpenShift (ARO) 4 kümesine dağıtma işlemlerinin nasıl yapılacağı gösterilir. Bu makale, bir serbest uygulama hazırlama, uygulama Docker görüntüsünü oluşturma ve bir ARO 4 kümesinde Kapsayıcılı uygulamayı çalıştırma konusunda size kılavuzluk eder.  Açık özgürlük hakkında daha fazla bilgi için, bkz. [serbest bir proje açın sayfası](https://openliberty.io/). IBM WebSphere Liberty hakkında daha fazla bilgi için bkz. [WebSphere Liberty ürün sayfası](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu kılavuzda başarıyla gezinmek için aşağıdaki önkoşulları doldurun.

> [!NOTE]
> Azure Red Hat OpenShift, bir OpenShift kümesi oluşturmak ve çalıştırmak için en az 40 çekirdek gerektirir. Yeni bir Azure aboneliği için varsayılan Azure Kaynak kotası bu gereksinimi karşılamıyor. Kaynak sınırınıza bir artış istemek için bkz. [Standart kota: VM serisine göre limitleri artırma](../azure-portal/supportability/per-vm-quota-requests.md). Ücretsiz deneme aboneliğinin bir kota artışı için uygun olmadığını, bir kota artışı istenmeden önce [Kullandıkça Öde aboneliğine yükseldiğini](../cost-management-billing/manage/upgrade-azure-subscription.md) unutmayın.

1. UNIX benzeri işletim sistemi yüklü bir yerel makine hazırlayın (örneğin, Ubuntu, macOS).
1. Bir Java CE uygulamasını (örneğin, [Benimsetopenjdk OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)) yükler.
1. [Maven](https://maven.apache.org/download.cgi) 3.5.0 veya üstünü yükler.
1. IŞLETIM sisteminize [Docker](https://docs.docker.com/get-docker/) 'yi yüklemeyin.
1. [Azure CLI](/cli/azure/install-azure-cli) 2.0.75 veya üstünü yükler.
1. [`envsubst`](https://command-not-found.com/envsubst)İşletim sisteminizde önceden yüklenmemişse denetleyin ve yükleme yapın.
1. Bu örnek için kodu yerel sisteminizde kopyalayın. Örnek [GitHub](https://github.com/Azure-Samples/open-liberty-on-aro)' dır.
1. [Azure Red Hat OpenShift 4 kümesi oluşturma](./tutorial-create-cluster.md)bölümündeki yönergeleri izleyin.

   "Red hat çekme gizli dizisi al" adımı isteğe bağlı olarak etiketlidir olsa da **Bu makale için gereklidir**.  Çekme gizli dizisi, Azure Red Hat Openshıft kümenizin açık özgürlük Işlecini bulmasını sağlar.

   Kümede yoğun bellek gerektiren uygulamalar çalıştırmayı planlıyorsanız, parametresini kullanarak çalışan düğümlerinin doğru sanal makine boyutunu belirtin `--worker-vm-size` . Örneğin, `Standard_E4s_v3` bir kümeye Elaun Search işlecini yüklemek için en düşük sanal makine boyutudur. Daha fazla bilgi için bkz.

   * [Küme oluşturmak için Azure CLı](/cli/azure/aro#az_aro_create)
   * [Bellek için iyileştirilmiş desteklenen sanal makine boyutları](./support-policies-v4.md#memory-optimized)
   * [Elaun Search Işlecini yükleme önkoşulları](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. [Azure Red Hat OpenShift 4 kümesine bağlanma](./tutorial-connect-cluster.md)bölümündeki adımları izleyerek kümeye bağlanın.
   * Bu makalenin ilerleyen kısımlarında komutunu kullanacağımız için "OpenShift CLı 'yı Install" bölümündeki adımları izlediğinizden emin olun `oc` .
   * Gibi görünen küme konsolu URL 'sini yazın `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` .
   * Kimlik bilgilerini bir yere göz atın `kubeadmin` .

1. Kullanıcı belirtecine sahip OpenShift CLı 'de oturum açıp açmadiğinizi doğrulayın `kubeadmin` .

### <a name="enable-the-built-in-container-registry-for-openshift"></a>OpenShift için yerleşik kapsayıcı kayıt defterini etkinleştirin

Bu öğreticideki adımlar, OpenShift ile erişilebilen bir kapsayıcı kayıt defterine itilmiş olması gereken bir Docker görüntüsü oluşturur. En basit seçenek, OpenShift tarafından sağlanmış yerleşik kayıt defterini kullanmaktır. Yerleşik kapsayıcı kayıt defterini etkinleştirmek için [Azure Red Hat OpenShift 4 ' te yerleşik kapsayıcı kayıt defterini yapılandırma](built-in-container-registry.md)bölümündeki adımları izleyin. Bu adımlardan üç öğe bu makalede kullanılır.

* OpenShift Web konsolunda oturum açmak için Azure AD kullanıcısının Kullanıcı adı ve parolası.
* `oc whoami`OpenShift CLI 'da oturum açmak için adımları takip ettikten sonra çıkışı.  Bu değere **AAD-User** adı verilir.
* Kapsayıcı kayıt defteri URL 'SI.

Yerleşik kapsayıcı kayıt defterini etkinleştirme adımlarını tamamladıktan sonra bu öğeleri aşağı aklınızda edin.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Java uygulaması için bir OpenShift ad alanı oluşturma

1. Kimlik bilgilerini kullanarak tarayıcınızdan OpenShift Web konsolunda oturum açın `kubeadmin` .
2. **Yönetim**  >  **ad alanları**  >  **ad alanı oluştur**' a gidin.
3. `open-liberty-demo` **Ad** için girin ve daha sonra gösterildiği gibi **Oluştur**' u seçin.

   ![ad alanı oluştur](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Demo projesi için yönetici oluşturma

Görüntü yönetiminin yanı sıra, **AAD-User** Ayrıca, Aro 4 kümesinin demo projesindeki kaynakları yönetmek için de yönetim izinleri verilecektir.  OpenShift CLı ' de oturum açın ve bu adımları izleyerek **AAD-User** ' a gerekli ayrıcalıkları verin.

1. Kimlik bilgilerini kullanarak tarayıcınızdan OpenShift Web konsolunda oturum açın `kubeadmin` .
1. Web konsolunun sağ üst kısmında, oturum açmış kullanıcının bağlam menüsünü genişletin ve ardından **login komutunu Kopyala**' yı seçin.
1. Gerekirse, aynı kullanıcıyla yeni bir sekme penceresinde oturum açın.
1. **Görüntüleme belirtecini** seçin.
1. Aşağıda gösterildiği gibi, aşağıda belirtilen değeri **Bu belirteçle** panoya kopyalayın ve bir kabukta çalıştırın.
1. `admin`Ad alanında **AAD-User** için rol vermek üzere aşağıdaki komutları yürütün `open-liberty-demo` .

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Açık Liberty OpenShift Işlecini yükle

Kümeye oluşturup bağlandıktan sonra, açık serbest bağlantı Işlecini yüklersiniz.  Açık özgürlük Işlecinin ana başlangıç sayfası [GitHub](https://github.com/OpenLiberty/open-liberty-operator)üzerinde.

1. Kimlik bilgilerini kullanarak tarayıcınızdan OpenShift Web konsolunda oturum açın `kubeadmin` .
2. **İşleçler**  >  **OperatorHub** ' e gidin ve **Açık serbest bir operatör işleci** arayın.
3. Arama sonuçlarından **serbest bir Işleç açın** ' ı seçin.
4. **Yükle**'yi seçin.
5. Açılan menü **Işleci oluşturma** öğesinde, **yükleme modu** için **kümedeki tüm ad alanlarını (varsayılan)** , **güncelleştirme kanalının** **Beta** ve **onay stratejisi** için **Otomatik** ' i işaretleyin:

   ![Açık serbest bir operatör Işleci için işleç aboneliği oluştur](./media/howto-deploy-java-liberty-app/install-operator.png)
6. **Abone ol** ' u seçin ve açık serbest bir operatör görüntülenene kadar bir dakika veya iki dakika bekleyin.
7. Açık serbest bir Işleci "başarılı" durumuyla gözlemleyin.  Bunu yapmazsanız, devam etmeden önce sorunu tanılayın ve çözümleyin.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Açık özgürlük gösteren yüklü Işleçler yüklendi.":::

## <a name="prepare-the-liberty-application"></a>Serbest uygulamayı hazırlama

Bu kılavuzda örneğimiz olarak bir Java EE 8 uygulaması kullanacağız. Açık özgürlük, [Java EE 8 tam profille](https://javaee.github.io/javaee-spec/javadocs/) uyumlu bir sunucu olduğundan, uygulamayı kolayca çalıştırabilirler.  Açık özgürlük Ayrıca, [Cakarta Ee 8 tam profil uyumludur](https://jakarta.ee/specifications/platform/8/apidocs/).

### <a name="run-the-application-on-open-liberty"></a>Açık özgürlük üzerinde uygulamayı çalıştırın

Uygulamayı açık serbest durumda çalıştırmak için, [Liberty Maven eklentisinin](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) uygulamayı dağıtıma paketleyebilir olması için açık bir serbest sunucu yapılandırma dosyası oluşturmanız gerekir. Liberty Maven eklentisi, uygulamayı OpenShift 'e dağıtmak için gerekli değildir.  Ancak, bunu bu örnekte açık özgürlük geliştiricisi (geliştirme) moduyla kullanacağız.  Geliştirici modu, uygulamayı yerel olarak kolayca çalıştırmanıza olanak sağlar. Yerel bilgisayarınızda aşağıdaki adımları izleyin.

1. `2-simple/src/main/liberty/config/server.xml` `1-start/src/main/liberty/config` Var olan sıfır uzunluklu dosyanın üzerine yazarak öğesine kopyalayın. Bu `server.xml` , açık serbest sunucu sunucusunu Java EE özellikleriyle yapılandırır.
1. Buraya `2-simple/pom.xml` kopyalayın `1-start/pom.xml` .  Bu adım, öğesini `liberty-maven-plugin` Pod 'ye ekler.
1. Dizini `1-start` Yerel kopyanızda değiştirin.
1. `mvn clean package`Dizinde bir War paketi oluşturmak için bir konsolunda çalıştırın `javaee-cafe.war` `./target` .
1. `mvn liberty:dev`Geliştirme modunda açık özgürlük başlatmak için çalıştırın.
1. Sunucu başlatılana kadar bekleyin. Konsol çıktısı aşağıdaki iletiyle bitmelidir:

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. `http://localhost:9080/`Uygulama giriş sayfasını ziyaret etmek için tarayıcınızda açın. Uygulama aşağıdaki resme benzer şekilde görünür:

   ![JavaEE Cafe Web Kullanıcı arabirimi](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Uygulamayı durdurmak ve özgürlük sunucusunu açmak için **Control-C** tuşlarına basın.

`2-simple`Yerel kopyanızda Dizin, yukarıdaki değişikliklerle zaten uygulanmış Maven projesini gösterir.

## <a name="prepare-the-application-image"></a>Uygulama görüntüsünü hazırlama

Liberty uygulamanızı bir ARO 4 kümesine dağıtmak ve çalıştırmak için, [Açık serbest yer kapsayıcısı görüntülerini](https://github.com/OpenLiberty/ci.docker) veya [WebSphere serbest bir kapsayıcı görüntülerini](https://github.com/WASdev/ci.docker)kullanarak uygulamanızı bir Docker görüntüsü olarak kapsayıtdırın.

### <a name="build-application-image"></a>Uygulama görüntüsü oluştur

Uygulama görüntüsünü derlemek için aşağıdaki adımları izleyin:

1. Dizini `2-simple` Yerel kopyanızda değiştirin.
2. `mvn clean package`Uygulamayı paketlemek için çalıştırın.
3. Uygulama görüntüsünü derlemek için aşağıdaki komutlardan birini çalıştırın.
   * Açık serbest bir temel görüntüyle derleme:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * WebSphere serbest ana görüntüsü ile derleme:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Uygulamayı Docker ile yerel olarak çalıştırma

Kapsayıcılı uygulamayı uzak bir kümeye dağıtmadan önce, çalışıp çalışmadığını doğrulamak için yerel Docker ile çalıştırın:

1. `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0`Konsolunuza çalıştırın.
2. Serbest bir sunucunun başlamasını ve uygulamanın başarıyla dağıtılmasını bekleyin.
3. `http://localhost:9080/`Uygulama giriş sayfasını ziyaret etmek için tarayıcınızda açın.
4. Uygulamayı ve serbest sunucuyu durdurmak için **Control-C** tuşlarına basın.

### <a name="push-the-image-to-the-container-image-registry"></a>Görüntüyü kapsayıcı görüntüsü kayıt defterine gönderme

Uygulamanın durumunu tatmin ediyorsanız, aşağıdaki yönergeleri izleyerek yerleşik kapsayıcı görüntüsü kayıt defterine gönderin.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>OpenShift CLı 'de Azure AD kullanıcısı olarak oturum açma

1. Azure AD kullanıcısının kimlik bilgilerini kullanarak, tarayıcınızdan OpenShift Web konsolunda oturum açın.

   1. Konsolda oturum açmak için InPrivate, ınbilito veya diğer eşdeğer tarayıcı penceresi özelliğini kullanın.
   1. **OpenID** seçin

   > [!NOTE]
   > Burada oturum açmak için kullandığınız kullanıcı adını ve parolayı bir yere göz atın. Bu Kullanıcı adı ve parola, bu ve diğer makalelerdeki diğer eylemler için yönetici olarak çalışır.
1. Aşağıdaki adımları kullanarak OpenShift CLı ile oturum açın.  Tartışma için bu işlem olarak bilinir `oc login` .
   1. Web konsolunun sağ üst kısmında, oturum açmış kullanıcının bağlam menüsünü genişletin ve ardından **login komutunu Kopyala**' yı seçin.
   1. Gerekirse, aynı kullanıcıyla yeni bir sekme penceresinde oturum açın.
   1. **Görüntüleme belirtecini** seçin.
   1. Aşağıda gösterildiği gibi, aşağıda belirtilen değeri **Bu belirteçle** panoya kopyalayın ve bir kabukta çalıştırın.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>OpenShift için kapsayıcı görüntüsünü kapsayıcı kayıt defterine gönderme

Bu komutları, OpenShift için görüntüyü kapsayıcı kayıt defterine göndermek için yürütün.

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

Başarılı çıktı aşağıdakine benzer olacaktır.

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

Aşağıdaki komutla görüntüyü yerleşik kapsayıcı görüntüsü kayıt defterine gönderin.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>Uygulamayı ARO 4 kümesinde dağıtma

Artık, ön koşullar aracılığıyla çalışırken oluşturduğunuz Azure Red Hat OpenShift 4 kümesine örnek serbest bir uygulamayı dağıtabilirsiniz.

### <a name="deploy-the-application-from-the-web-console"></a>Web konsolundan uygulamayı dağıtma

Serbest uygulamaları yönetmek için açık serbest bir operatör kullandığımızda, "Openlibertince" türünde *özel kaynak tanımının* bir örneğini oluşturuyoruz. Işleci daha sonra dağıtım için gerekli olan OpenShift kaynaklarını yönetmenin tüm yönlerini ele alır.

1. Azure AD kullanıcısının kimlik bilgilerini kullanarak, tarayıcınızdan OpenShift Web konsolunda oturum açın.
1. **Giriş**' i genişletin, **Projeler**  >  **Açık-Liberty-demo**' i seçin.
1. **Operatörler**  >  **yüklü işleçlere** gidin.
1. Sayfanın ortasında, **serbest bir Işleç aç**' ı seçin.
1. Sayfanın ortasında, **serbest bir uygulama aç**' ı seçin.  Kullanıcı arabirimindeki öğelerin gezinmesi, kullanımdaki teknolojilerin gerçek kapsama hiyerarşisini yansıtır.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![ARO Java kapsama](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. **Openlibertfail oluştur** ' u seçin
1. Oluşturulan YAML 'yi, ' de bulunan sizinkilerle değiştirin `<path-to-repo>/2-simple/openlibertyapplication.yaml` .
1. **Oluştur**’u seçin. OpenLibertyApplications listesine döndürülürsünüz.
1. **JavaEE-Café-Simple**' ı seçin.
1. Sayfanın ortasında **kaynaklar**' ı seçin.
1. Tabloda, **yol** **türüyle** **JavaEE-Café-Simple** bağlantısını seçin.
1. Açılan sayfada, **konumun** altındaki bağlantıyı seçin.

Uygulama giriş sayfasını tarayıcıda açıldığını görürsünüz.

### <a name="delete-the-application-from-the-web-console"></a>Web konsolundan uygulamayı silme

Uygulama ile işiniz bittiğinde, uygulamayı açık vardiyadan silmek için aşağıdaki adımları izleyin.

1. Sol gezinti bölmesinde **işleçler** için girişi genişletin.
1. **Yüklü işleçler**' ı seçin.
1. **Açık serbest bir işleç** seçin.
1. Sayfanın ortasında, **serbest bir uygulama aç**' ı seçin.
1. Dikey üç nokta (üç dikey nokta) seçin ve **OpenLiberty uygulamasını Sil**' i seçin.

### <a name="deploy-the-application-from-cli"></a>Uygulamayı CLı 'dan dağıtma

Web Konsolu GUI 'sini kullanmak yerine, uygulamayı CLı 'dan dağıtabilirsiniz. Daha önce yapmadıysanız, `oc` [CLI Ile çalışmaya başlarken](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html)Red Hat belgelerini izleyerek komut satırı aracını indirip yükleyin.

1. Azure AD kullanıcısının kimlik bilgilerini kullanarak, tarayıcınızdan OpenShift Web konsolunda oturum açın.
2. OpenShift CLı 'de Azure AD kullanıcısına yönelik belirteçle oturum açın.
3. Dizini `2-simple` Yerel kopyanızda değiştirin ve ardından, Liberty UYGULAMANıZı Aro 4 kümesine dağıtmak için aşağıdaki komutları çalıştırın.  Komut çıktısı Ayrıca satır içi olarak gösterilir.

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. `1/1`Devam etmeden önce sütunun altında görmek için işaretleyin `READY` .  Aksi takdirde, devam etmeden önce sorunu araştırın ve çözümleyin.
5. `oc get route`Burada gösterildiği gibi, komutuyla uygulamanın yolunu bulun.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Serbest uygulama çalışır duruma getirildikten sonra uygulama giriş sayfasını ziyaret etmek için tarayıcınızda **yol ana bilgisayarının** çıkışını açın.

### <a name="delete-the-application-from-cli"></a>CLı 'dan uygulamayı silme

Bu komutu yürüterek uygulamayı CLı 'dan silin.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

[Öğretici: Azure Red Hat OpenShift 4 kümesini silme öğreticisindeki](./tutorial-delete-cluster.md) adımları IZLEYEREK, Aro kümesini silin

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda şunları öğrendiniz:
> [!div class="checklist"]
>
> * Serbest uygulamayı hazırlama
> * Uygulama görüntüsünü oluşturma
> * GUI ve CLı kullanarak bir ARO 4 kümesinde Kapsayıcılı uygulamayı çalıştırma

Bu kılavuzda kullanılan başvurulardan daha fazla bilgi edinebilirsiniz:

* [Özgürlük aç](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Serbest bir Işleç aç](https://github.com/OpenLiberty/open-liberty-operator)
* [Serbest sunucu yapılandırmasını aç](https://openliberty.io/docs/ref/config/)
* [Liberty Maven eklentisi](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Serbest kapsayıcı resimlerini aç](https://github.com/OpenLiberty/ci.docker)
* [WebSphere serbest kapsayıcı görüntüleri](https://github.com/WASdev/ci.docker)