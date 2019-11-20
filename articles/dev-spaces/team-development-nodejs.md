---
title: Node. js ve VS Code ile Azure Dev Spaces takım geliştirme
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Azure’da kapsayıcılar ve mikro hizmetlerle hızlı Kubernetes geliştirme
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar, Held, hizmet ağı, hizmet kafesi yönlendirme, kubectl, k8s '
ms.openlocfilehash: d3eecce7e5704752e0a5ea18b2a5d0f668978028
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129290"
---
# <a name="team-development-with-azure-dev-spaces-using-nodejs-and-vs-code"></a>Node. js ve VS Code kullanarak Azure Dev Spaces takım geliştirme

Bu öğreticide, geliştiricilerin bir ekibinin geliştirme alanlarını kullanarak aynı Kubernetes kümesinde eşzamanlı olarak nasıl işbirliği yapabildiği hakkında bilgi edineceksiniz.

## <a name="learn-about-team-development"></a>Ekip geliştirmesi hakkında bilgi edinme
Şu ana kadar uygulamanızın kodunu uygulama üzerinde çalışan tek geliştiriciymişsiniz gibi çalıştırıyordunuz. Bu bölümde, Azure Dev Spaces’ın ekip geliştirmesini nasıl kolaylaştırdığını öğreneceksiniz:
* Bir geliştiricilerin bir ekibini, paylaşılan bir geliştirme alanında ya da gerektiğinde ayrı geliştirme alanlarında çalışarak aynı ortamda çalışmasını sağlama.
* Her geliştiricinin yalıtılmış olarak ve başkalarını bölme korkusu olmadan kodlarını yinelemelerini destekler.
* Kod işlemeden önce sahtelerini yaratmaya veya bağımlılıkları benzetmeye gerek kalmadan kodu uçtan uca test edin.

### <a name="challenges-with-developing-microservices"></a>Mikro hizmet geliştirme zorlukları
Örnek uygulamanız şu an için pek karmaşık değil. Ancak gerçek dünyada geliştirme sırasında daha çok hizmet ekledikçe ve geliştirme ekibi büyüdükçe zorluklar kısa sürede ortaya çıkar. Geliştirme için her şeyi yerel olarak çalıştırmak pek mantıklı olmayabilir.

* Geliştirme makinenizin her seferinde ihtiyacınız olan her hizmeti çalıştırmak için yeterli kaynak bulunmayabilir.
* Bazı hizmetlerin herkese açık bir şekilde erişilebilir olması gerekebilir. Örneğin, bir hizmetin bir Web kancasına yanıt veren bir uç noktaya sahip olması gerekebilir.
* Hizmetlerin bir alt kümesini çalıştırmak istiyorsanız, tüm hizmetlerinize ilişkin tam bağımlılık hiyerarşisini bilmeniz gerekir. Özellikle hizmet numaranız artdıkça bu durum zor olabilir.
* Bazı geliştiriciler, hizmet bağımlılıklarının birçoğunu benzetmeye veya bu bağımlılıkların sahtelerini oluşturmaya başvurur. Bu yaklaşım yardımcı olabilir ancak bu hareketleri yönetmek, geliştirme maliyetini yakında etkileyebilir. Ayrıca, bu yaklaşım, üretim ortamından çok farklı arayan geliştirme ortamınız üzerinde gezinerek, hafif hataların yayılmasını sağlar.
* Her türlü tümleştirme testini yapmak zor olur. Tümleştirme testi yalnızca yürütme sonrası gerçekleşebilir, bu da geliştirme döngüsünün sonraki aşamalarında sorunlarla karşılaşacağınız anlamına gelir.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Paylaşılan geliştirme alanında çalışma
Azure Dev Spaces ile, Azure’da *paylaşılan* bir geliştirme alanı ayarlayabilirsiniz. Her geliştirici, uygulamanın yalnızca kendisine ayrılan kısmıyla ilgilenebilir ve senaryolarının bağımlı olduğu diğer tüm hizmetleri ve bulut kaynaklarını barındırmakta olan bir geliştirme alanında yinelemeli olarak *yürütme öncesi kod* geliştirebilir. Bağımlılıklar her zaman günceldir ve geliştiriciler üretimi yansıtan bir şekilde çalışır.

### <a name="work-in-your-own-space"></a>Kendi alanınızda çalışma
Hizmetiniz için kod geliştirirken ve kodu iade etmeye hazır olmadan önce, kodun iyi durumda olmadığı zamanlar olacaktır. Hala yinelemeli olarak şekillendiriyor, test ediyor ve çözümlerle deney yapıyorsunuz. Azure Dev Spaces, ekip üyelerinizi bölme korkusu olmadan yalıtılmış olarak çalışmanızı sağlayan **alan** kavramını sunar.

## <a name="use-dev-spaces-for-team-development"></a>Takım geliştirmesi için geliştirme alanlarını kullanma
*Web ön* ucu -> *mywebapi* örnek uygulamamız kullanarak bu fikirleri somut bir örnekle gösterelim. Bir geliştiricinin, Scott, *mywebapi* hizmetinde değişiklik yapması gereken bir senaryo ve *yalnızca* bu hizmet. *Web ön* ucu Scott 'ın güncelleştirmesinin bir parçası olarak değişikliğe gerek kalmaz.

Geliştirici _alanlarını kullanmadan Scott_ , bu güncelleştirmeyi geliştirmek ve test etmek için bazı yöntemlere sahiptir ve bunlardan hiçbiri idealdir:
* Tüm bileşenleri yerel olarak çalıştırın. Bu, Docker 'ın yüklü olduğu daha güçlü bir geliştirme makinesi ve potansiyel MiniKube gerektirir.
* Tüm bileşenleri Kubernetes kümesinde yalıtılmış bir ad alanında çalıştırın. *Web ön* ucu değişmediğinden, bu küme kaynaklarının bir atık bir kümesidir.
* YALNıZCA *mywebapi*' i çalıştırın ve El Ile yapılan Rest çağrılarını test edin. Bu, uçtan uca akışın tamamını test etmez.
* Geliştiricilerin farklı *mywebapi*örneğine istek göndermesini sağlayan *Web ön* ucunda geliştirme odaklı kod ekleyin. Bu, *webön uç* hizmetini karmaşıklaştırır.

### <a name="set-up-your-baseline"></a>Taban çizgisini ayarlama
İlk olarak hizmetlerimizin temelini dağıtmamız gerekecektir. Bu dağıtım, "bilinen son iyi" kullanımı temsil eder; böylece yerel kodunuzun davranışını ve iade edilen sürümü ile kolayca karşılaştırabilirsiniz. Daha sonra bu taban çizgisine göre bir alt alan oluşturacak ve daha büyük bir uygulama bağlamında *mywebapi* 'teki değişiklikleri test edebilmeniz için

1. [Geliştirici alanları örnek uygulamasını](https://github.com/Azure/dev-spaces)kopyalayın: `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Uzak dalı *azds_updates*kullanıma alın: `git checkout -b azds_updates origin/azds_updates`
1. _Geliştirme_ alanını seçin: `azds space select --name dev`. Bir üst dev alanı seçmeniz istendiğinde _\<yok\>_ ' ı seçin.
1. _Mywebapi_ dizinine gidin ve şunu çalıştırın: `azds up -d`
1. _Webön uç_ dizinine gidin ve şunu çalıştırın: `azds up -d`
1. _Web ön_ ucu için genel uç noktasını görmek üzere `azds list-uris` yürütün

> [!TIP]
> Yukarıdaki adımlar el ile bir taban çizgisi ayarlar, ancak ekiplerin ayrılmış kodla otomatik olarak güncel tutulması için CI/CD kullanmasını öneririz.
>
> Aşağıdaki diyagrama benzer bir iş akışı oluşturmak için [Azure DevOps Ile CI/CD ayarlamaya yönelik kılavuzumuza](how-to/setup-cicd.md) göz atın.
>
> ![Örnek CI/CD diyagramı](media/common/ci-cd-complex.png)

Bu noktada taban çizgisi çalışıyor olmalıdır. `azds list-up --all` komutunu çalıştırdığınızda aşağıdakine benzer bir çıktı görürsünüz:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

DevSpace sütunu her iki hizmetin _dev_adlı bir alanda çalıştığını gösterir. Ortak URL 'YI açan ve Web uygulamasına giden herkes, her iki hizmet arasında çalışan iade kodu yolunu çağıracaktır. Şimdi _mywebapi_geliştirmeye devam etmek istediğinizi varsayalım. Nasıl kod değişiklikleri yapabilir ve bunları test edebilir, öte yandan da geliştirme ortamını kullanan diğer geliştiricilerin işlemini kesintiye uğratmazsınız? Bunu yapmak için kendi alanınızı ayarlarsınız.

### <a name="create-a-dev-space"></a>Geliştirme alanı oluşturma
Kendi _mywebapi_ sürümünüzü _dev_dışında bir alanda çalıştırmak için aşağıdaki komutu kullanarak kendi alanınızı oluşturabilirsiniz:

```cmd
azds space select --name scott
```

İstendiğinde, **üst geliştirme alanı**olarak _dev_ ' ı seçin. Bu, yeni alanım, _dev/Scott_, Space _dev_'dan türetecektir. Bu durumun test sırasında bize nasıl yardımcı olacağını birazdan göreceksiniz.

Giriş kuramsal sitemizi sayesinde, eşlerin _Bu adı yeni_ alan için kullandık ve bu nedenle, eşler üzerinde kimin çalıştığını tanımlayabiliriz. Ancak, istediğiniz herhangi bir şey olarak adlandırılır ve _sprint4_ veya _demo_gibi ne anlama geldiğini esnek hale getirebilirsiniz. Her ne olursa olsun, _dev_ bu uygulamanın bir parçası üzerinde çalışan tüm geliştiriciler için taban çizgisi görevi görür:

![](media/common/ci-cd-space-setup.png)

Geliştirme ortamındaki tüm alanların listesini görmek için `azds space list` komutunu çalıştırın. _Seçili_ sütun şu anda hangi alanı seçtiğinizi gösterir (true/false). Bu durumda, _geliştirme/Scott_ adlı alan oluşturulduğunda otomatik olarak seçilmiştir. İstediğiniz zaman `azds space select` komutuyla başka bir alan seçebilirsiniz.

Şimdi nasıl çalıştığını görelim.

### <a name="make-a-code-change"></a>Kod değişikliği yapma
`mywebapi` için VS Code penceresine gidin ve `server.js`' de varsayılan GET `/` işleyicisine bir kod düzenleme yapın, örneğin:

```javascript
app.get('/', function (req, res) {
    res.send('mywebapi now says something new');
});
```

### <a name="run-the-service"></a>Hizmeti çalıştırma

Hizmeti çalıştırmak için, hizmeti çalıştırmak için F5 tuşuna basın (veya Terminal penceresinde `azds up` yazın). Hizmet, yeni seçilen Space _dev/Scott_içinde otomatik olarak çalışacaktır. `azds list-up`çalıştırarak hizmetinizin kendi alanında çalıştığını doğrulayın:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Şimdi _dev/Scott_ alanında *mywebapi* örneğinin çalıştığını unutmayın. _Geliştirme_ aşamasında çalışan sürüm hala çalışıyor ancak listede yok.

`azds list-uris`çalıştırarak geçerli alanın URL 'Lerini listeleyin.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

*Web ön* ucu için genel erişim noktası URL 'sinin *Scott. s*ile ön ek olduğuna dikkat edin. Bu URL, _dev/Scott_ alanı için benzersizdir. Bu URL öneki, giriş denetleyicisine istekleri bir hizmetin _geliştirme/Scott_ sürümüne yönlendirmasını söyler. Bu URL 'ye sahip bir istek dev Spaces tarafından işlendiği zaman, giriş denetleyicisi ilk olarak isteği _dev/Scott_ alanındaki *webön uç* hizmetine yönlendirmeye çalışır. Bu işlem başarısız olursa, istek _geliştirme_ alanındaki *webön uç* hizmetine geri dönüş olarak yönlendirilir. Ayrıca, Kubernetes *bağlantı noktası-iletme* işlevini kullanarak hizmete localhost üzerinden erişmek için bir localhost URL 'si olduğuna dikkat edin. Azure Dev Spaces URL 'Ler ve yönlendirme hakkında daha fazla bilgi için, bkz. [Azure dev Spaces nasıl çalıştığını ve nasıl yapılandırıldığını](how-dev-spaces-works.md)öğrenin.

![Boşluk yönlendirme](media/common/Space-Routing.png)

Azure Dev Spaces’ın bu yerleşik özelliği, her geliştiricinin alanlarındaki hizmetlerin tam yığınını yeniden oluşturmasına gerek kalmadan kodu paylaşılan bir alanda test etmenize olanak sağlar. Bu yönlendirme, bu kılavuzun önceki adımında gösterildiği gibi uygulama kodunuzun yayma üst bilgilerini iletmesini gerektirir.

### <a name="test-code-in-a-space"></a>Alanda kodu test etme
*Web ön*ucu ile yeni *mywebapi* sürümünüzü test etmek için tarayıcınızı *Web ön* ucu için genel erişim noktası URL 'sine açın ve hakkında sayfasına gidin. Yeni iletinizin görüntülendiğini görmelisiniz.

Şimdi, URL'nin "scott.s." bölümünü kaldırın ve tarayıcıyı yenileyin. Eski davranışı görmeniz gerekir ( _dev_sürümünde çalışan *mywebapi* sürümü ile).

En son değişiklerinizi her zaman içeren bir _geliştirme_ alanınız varsa ve uygulamanızın bu öğretici bölümünde açıklandığı gibi devspace 'in alan tabanlı yönlendirmesinden yararlanmak üzere tasarlandığını varsayarsak, geliştirme alanlarının, daha büyük uygulama bağlamında yeni özellikleri test ederken büyük ölçüde nasıl yardımcı olduğunu görmek çok kolay hale gelir. _Tüm_ Hizmetleri özel alanınıza dağıtmak zorunda kalmak yerine, _geliştirme_ve gerçekten üzerinde çalıştığınız hizmetlerden oluşan özel bir alan oluşturabilirsiniz. Geliştirme alanları yönlendirme altyapısı, bulma sırasında özel alanınızda çok sayıda hizmetten yararlanarak bu geri kalanı işleyerek, _geliştirme_ alanında çalışan en son sürüme doğru bir şekilde geri dönüş yapabilir. Daha iyi bir şekilde, _birden çok_ geliştirici, birbirini kesintiye uğratmadan kendi alanında aynı anda farklı hizmetler geliştirebilir.

### <a name="well-done"></a>Bravo!
Başlangıç kılavuzunu tamamladınız! Şunları öğrendiniz:

> [!div class="checklist"]
> * Azure’da yönetilen bir Kubernetes ile Azure Dev Spaces’ı ayarlayın.
> * Kapsayıcılarda yinelemeli kod geliştirin.
> * İki ayrı hizmeti bağımsız olarak geliştirin ve Kubernetes’in DNS hizmet bulma yöntemini kullanarak başka bir hizmete çağrı yapın.
> * Kodunuzu bir ekip ortamında verimli bir şekilde geliştirip test edin.
> * Daha büyük bir mikro hizmet uygulaması bağlamı içindeki yalıtılmış değişiklikleri kolayca test etmek için geliştirme alanlarını kullanarak bir işlev temeli oluşturun

Azure Dev Spaces araştırdığınıza [göre, geliştirme alanınızı bir takım üyesi ile paylaşır](how-to/share-dev-spaces.md) ve işbirliği yapmaya başlayın.

## <a name="clean-up"></a>Temizleme
Geliştirme alanları ve içinde çalışan hizmetler dahil olmak üzere bir kümedeki Azure Dev Spaces örneğini tamamen silmek için `az aks remove-dev-spaces` komutunu kullanın. Bu eylemin geri alınamayacağını unutmayın. İleride kümeye yeniden Azure Dev Spaces desteği ekleyebilirsiniz ancak sıfırdan başlamış gibi olursunuz. Eski hizmetleriniz ve alanlarınız geri yüklenmez.

Aşağıdaki örnek etkin aboneliğinizdeki Azure Dev Spaces denetleyicilerini listeler ve ardından 'myaks-rg' kaynak grubundaki 'myaks' AKS kümesiyle ilişkilendirilmiş Azure Dev Spaces denetleyicisini siler.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
