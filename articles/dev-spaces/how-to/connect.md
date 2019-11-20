---
title: " Geliştirme makinenizi bir AKS kümesine bağlama"
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 11/04/2019
ms.topic: conceptual
description: Geliştirme makinenizi Azure Dev Spaces ile bir AKS kümesine bağlamayı öğrenin
keywords: Azure Dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar
ms.openlocfilehash: 15e46fba19d92e08a9c87a63f6f01ec6b0836910
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888917"
---
# <a name="connect-your-development-machine-to-an-aks-cluster-preview"></a>Geliştirme makinenizi bir AKS kümesine bağlama (Önizleme)

Azure Dev Spaces, geliştirme makinenizde bir kapsayıcı ile veya bu kodla hata ayıklamanıza olanak sağlarken, hala uygulamanızın veya hizmetlerinizin geri kalanı ile Kubernetes kümenize bağlanır. Geliştirme makinenizi kümenize bağlamak, herhangi bir Docker veya Kubernetes yapılandırması oluşturmaya gerek kalmadan uygulamanızı hızlı bir şekilde geliştirmenize ve uçtan uca test gerçekleştirmenize yardımcı olur. Ayrıca, diğer iş yüklerini veya aynı kümeyi kullanabilecek kullanıcıları etkilemeden AKS kümenize bağlanabilirsiniz.

Azure Dev Spaces bağlı AKS kümeniz ile geliştirme makineniz arasında trafiği yeniden yönlendirir. Bu trafik yeniden yönlendirme, AKS kümenizde çalışan geliştirme makinenizde ve hizmetinizdeki kodların aynı AKS kümesinde olduklarından farklı iletişim kurmasına olanak tanır. Kodunuz geliştirme makinenizde çalıştığından, bu kodu çalıştırmak ve hatalarını ayıklamak için kullandığınız geliştirme araçlarında esneklik de vardır. Azure Dev Spaces Ayrıca, geliştirme makinenizdeki AKS kümenizdeki ortam değişkenlerini ve takılı dosyaları bir arada çoğaltmak için bir yol sağlar.

Bu kılavuzda şunların nasıl yapıldığını öğreneceksiniz:

* Azure 'da yönetilen bir Kubernetes kümesinde Azure Dev Spaces ayarlayın.
* Birden fazla mikro hizmet ile bir geliştirme alanına büyük bir uygulama dağıtın.
* Geliştirme makinenizde çalışan AKS kümeniz ve kodunuz arasında trafiği yeniden yönlendirmek için Azure Dev Spaces kullanın.

> [!IMPORTANT]
> Bu özellik şu anda önizleme sürümündedir. Önizlemeler, [ek kullanım koşullarını](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu kılavuz, geliştirme makinenizin bir AKS kümesine bağlanmasını göstermek için [Azure dev Spaces bisiklet paylaşımı örnek uygulamasını](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) kullanır. Örnek uygulamayı çalıştırmak için [Azure dev Spaces bisiklet paylaşımı örnek uygulama Benioku](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) dosyasındaki yönergeleri izleyin. Alternatif olarak, bir AKS kümesinde kendi uygulamanız varsa aşağıdaki adımları izleyebilirsiniz ve kendi hizmetlerinizin ve yığınlarınızın adlarını kullanabilirsiniz.

### <a name="limitations"></a>Sınırlamalar

* UDP Şu anda desteklenmiyor.

### <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz hesap](https://azure.microsoft.com/free) oluşturabilirsiniz.
* [Yüklü Azure CLI][azure-cli].
* MacOS veya Windows 10 ' da yüklü ve çalışan [Azure dev Spaces][azds-vs-code] uzantısı ile [Visual Studio Code][vs-code] .
* [Azure dev Spaces bisiklet paylaşımı örnek uygulaması](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) veya bir aks kümesinde çalışan kendi uygulamanız.

## <a name="connect-your-development-machine"></a>Geliştirme makinenizi bağlama

Visual Studio Code 'de *dev-Spaces/Samples/Bıkesharingapp/Bisiklet* açın ve Azure dev Spaces uzantısını kullanarak geliştirme makinenizi aks kümenize bağlayın.

Azure Dev Spaces uzantısını kullanmak için, *görüntüle* ve *komut paleti*' ne tıklayarak Visual Studio Code komut paleti ' ni açın. `Azure Dev Spaces: Redirect` yazmaya başlayın ve `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`ya da `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`seçeneğine tıklayın.

![Komutlar](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Yeniden yönlendirme seçeneği seçin

`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`çalıştırırsanız, var olan bir Kubernetes hizmeti seçmeniz istenir:

![Hizmet seçin](../media/how-to-connect/connect-choose-service.png)

Bu seçenek, bu hizmet için AKS kümesindeki tüm trafiği, geliştirme makinenizde çalışan uygulamanızın sürümüne yönlendirir. Bu hizmette AKS kümesinde çalışan birden fazla sayıda yer varsa, bu hizmetin tüm trafiği yalnızca geliştirme makinenize yönlendirilir. Azure Dev Spaces Ayrıca, uygulamanın tüm giden trafiğini AKS kümenize geri yönlendirir.

`Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`çalıştırırsanız, belirli bir pod seçmeniz istenir:

![Pod seçin](../media/how-to-connect/connect-choose-pod.png)

Bu seçenek, belirli bir pod 'a bağlanır. Bu seçenek, trafik gönderme veya alma ve sonlandırılan Pod 'yi çoğaltma gibi Pod ile etkileşim kurmak için yararlıdır. Pod, trafik gönderiyor ve alıyorsa, bu seçenek `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` benzer bir şekilde davranır ve seçilen Pod hizmeti ile ilgili tüm FID 'ler için AKS kümesindeki tüm trafiği yönlendirecektir.

`Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`çalıştırırsanız, var olan bir pod veya hizmet seçmeniz istenmez. Bu seçenek, geliştirme makinenizde çalışan uygulamadaki tüm giden trafiği AKS kümesine yönlendirir.

Bu örnek için `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` seçin ve *Bisiklet* hizmetini seçin.

### <a name="select-a-connection-mode"></a>Bir bağlantı modu seçin

Yeniden yönlendirme seçeneğinizi belirledikten sonra, bağlantıyı *Değiştir* veya *Kopyala* modunu seçmeniz istenir.

![Değiştir veya Kopyala](../media/how-to-connect/connect-replace-clone.png)

*Replace* seçeneği, aks kümesindeki geçerli Pod veya hizmetin yerini alır ve bu hizmet için tüm trafiği geliştirme makinenize yönlendirir. Bu seçenek, AKS kümenizde, uygulamayı geliştirme makinenizde başlatana kadar yönlendirireceğiniz hizmetle etkileşimde bulunan diğer hizmetlere yönelik karışıklığa neden olabilir. *Kopyalama* seçeneği, var olan bir alt geliştirme alanını seçmenizi veya bir pod veya hizmet için trafiği geliştirme makinenize yönlendirmek üzere yeni bir alt geliştirme alanı oluşturmanızı sağlar. Bu seçenek, yalnızca söz konusu alt dev alanının trafiği geliştirme makinenize yönlendirilirken, yalıtımta çalışmanıza ve diğer hizmetleri kesintiye uğramayacak şekilde izin verir. *Kopyalama* seçeneği, aks kümenizin Azure dev Spaces etkinleştirilmesini gerektirir.

Bu örnek için *Değiştir*' i seçin.

> [!NOTE]
> Var olan hizmetinizin Pod 'ınızda birden çok kapsayıcı varsa, uygulamanın kapsayıcısını de seçmeniz istenir.

### <a name="select-a-port-for-your-application"></a>Uygulamanız için bir bağlantı noktası seçin

Bağlantı kipini seçtikten sonra, yerel uygulamanızın TCP bağlantı noktasını girmeniz istenir. Uygulamanız birden çok bağlantı noktası açarsa, bunları virgülle ayırın, örneğin *80, 81*. Uygulamanız herhangi bir ağ isteğini kabul etmezse *0*girin. Bu örnek için *3000*girin.

![Bağlan bağlantı noktası seç](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Bağlı olduğunu onaylayın

Uygulamanızın TCP bağlantı noktasını seçtikten sonra, Azure Dev Spaces AKS kümesiyle bağlantı kurar. , AKS kümesi ile geliştirme makineniz arasında trafiği yeniden yönlendirmek için bir aracıyı AKS kümenize çıkartır Azure Dev Spaces. Bu bağlantının oluşturulması birkaç dakika sürebilir. Azure Dev Spaces, geliştirme makinenizdeki *Hosts* dosyasını değiştirmek için yönetici erişimi de ister.

> [!IMPORTANT]
> Azure Dev Spaces, AKS kümenize bir bağlantı kurduktan sonra, bağlantı modunu *Değiştir* ' i seçerseniz, aks kümenizdeki diğer hizmetler, geliştirme makinenizde hizmeti başlatana kadar düzgün çalışmayabilir. Yeniden yönlendirme için bir alt dev alanı oluşturmak ve üst alana herhangi bir kesinti yaşamamak *için bağlantı modunu* seçebilirsiniz. Ayrıca, hizmetinizin geliştirme makinenizde kullanılamayan bir bağımlılığı varsa, uygulamanızı değiştirmeniz veya [ek yapılandırma](#additional-configuration) sağlamanız gerekebilir

Azure Dev Spaces, AKS kümenize bir bağlantı kurduktan sonra *azds Connect-Bisiklet* adlı bir Terminal penceresi açar. Bu Terminal penceresinde, AKS kümenizde yapılandırılan tüm ortam değişkenleri ve DNS girişleri bulunur. Bu Terminal penceresinde çalıştırdığınız veya Visual Studio Code hata ayıklayıcıyı kullanan herhangi bir kod AKS kümesine bağlıdır.

![Den](../media/how-to-connect/connect-terminal.png)

Ayrıca Azure Dev Spaces, tüm çıktılarına sahip *dev Spaces Connect* adlı bir pencere oluşturur.

![Çıktı](../media/how-to-connect/connect-output.png)

Azure Dev Spaces Ayrıca, bağlantı durumunu gösteren bir durum çubuğu öğesi vardır.

![Durum](../media/how-to-connect/connect-status.png)

Durum çubuğunun *dev alanlarını gösterdiğini doğrulayın: yerel bağlantı noktası 3000 üzerinde geliştirme/Bisiklet 'e bağlanıldı*.

### <a name="configure-your-application-on-your-development-machine"></a>Uygulamanızı geliştirme makinenizde yapılandırma

*Azds Connect-Bisiklet* Terminal penceresini açın ve `npm install`çalıştırın:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```


*Hata Ayıkla* ' ya tıkladıktan sonra yapılandırma ' yı *açın* Bir ortam seçmek isteyip istemediğiniz sorulursa *Node. js*' yi seçin. Bu, bir `.vscode/launch.json` dosyası oluşturur. Bu dosyanın içeriğini aşağıdaki kodla değiştirin:

```json
{
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

[Package. JSON](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) dosyasını açın ve bir hata ayıklama betiği ekleyin:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-machine"></a>Uygulamanızı geliştirme makinenizde başlatın

Sol taraftaki *hata ayıklama* simgesine tıklayın ve en üstte *NPM aracılığıyla* Başlat ' ın yanındaki Başlat düğmesine tıklayın.

![NPM aracılığıyla Başlat](../media/how-to-connect/launch-npm.png)

Uygulamanız başlatılır ve Azure Dev Spaces, AKS kümeniz ile geliştirme makineniz arasında trafiği yeniden yönlendirir. *Hata ayıklama konsolunda*aşağıdakine benzer iletiler görürsünüz:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Azure Dev Spaces durum çubuğuna tıklayıp uygulamanızın genel URL 'sini seçerek *bıkesharingweb* hizmetine gidin. Ayrıca, daha önce çalıştırdığınız `azds list-uris` komutundan genel URL 'YI bulabilirsiniz. Kümenizde Azure Dev Spaces kullanmıyorsanız, kullandığınız ad alanı için IP veya uygulamanın URL 'sini kullanın. Yukarıdaki örnekte, *bıkesharingweb* hizmeti IÇIN genel URL `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Kullanıcı olarak *Aurelia Briggs (müşteri)* öğesini seçin ve ardından kiralamak istediğiniz bir bisiklet seçin.

### <a name="set-a-break-point"></a>Kesme noktası ayarlama

[Sunucu. js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) ' yi açın ve imlecinizi buraya yerleştirmek için satır 233 ' de bir yere tıklayın. *F9* tuşuna basarak bir kesme noktası ayarlayın veya *Hata Ayıkla* 'Yı tıklattıktan sonra *kesme noktasını değiştirin*.

Genel URL 'YI açarak *bıkesharingweb* hizmetine gidin. Kullanıcı olarak *Aurelia Briggs (müşteri)* öğesini seçin ve ardından kiralamak istediğiniz bir bisiklet seçin. Bisiklet görüntüsünün yüklenmediğine dikkat edin. Visual Studio Code dön ve 233 satırını gözlemle vurgulanır. Ayarladığınız kesme noktası 233 satırındaki hizmeti duraklattı. Hizmeti sürdürmek için *F5* 'e basın veya *Hata Ayıkla* 'Ya tıkladıktan sonra *devam edin*. Tarayıcınıza dönün ve bisiklet için bir yer tutucu görüntüsü gördiğinizi doğrulayın.

İmleci `server.js` satır 233 ' ye yerleştirerek ve *F9*tuşuna basarak kesme noktasını kaldırın.

### <a name="update-your-application"></a>Uygulamanızı güncelleştirme

232 ve 233 satırlarını kaldırmak için `server.js` düzenleyin:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Bölüm şu şekilde görünmelidir:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Değişikliklerinizi kaydedin ve *Hata Ayıkla* ' ya tıkladıktan sonra *hata ayıklamayı yeniden başlatın*. Tarayıcınızı yenileyin ve bisiklet için artık bir yer tutucu görüntüsü görmediğinizi doğrulayın.

Hata ayıklayıcıyı durdurmak için hata *Ayıkla* ve hata *ayıklamayı Durdur* ' a tıklayın. AKS kümesi bağlantısını kesmek için Azure Dev Spaces durum çubuğuna tıklayın.

## <a name="additional-configuration"></a>Ek yapılandırma

Azure Dev Spaces, yönlendirme trafiğini işleyebilir ve ek yapılandırma olmadan ortam değişkenlerini çoğaledebilir. Bir ConfigMap dosyası gibi AKS kümenizdeki kapsayıcıya bağlanmış herhangi bir dosyayı indirmeniz gerekiyorsa, bu dosyaları geliştirme makinenize indirmek için bir `azds-local.env` oluşturabilirsiniz.

Örnek `azds-local.env`aşağıda verilmiştir:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development machine,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development machine.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development machine
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

MYAPP1_SERVICE_HOST=${services.myapp1}

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.
```

## <a name="next-steps"></a>Sonraki adımlar

Çekme isteği deponuzun ana dalında birleştirilmeden önce doğrudan AKS 'teki bir çekme isteğinden yapılan değişiklikleri test etmek için Azure Dev Spaces ve GitHub eylemlerini nasıl kullanacağınızı öğrenin.

> [!div class="nextstepaction"]
> [Azure Kubernetes hizmeti & GitHub eylemleri][gh-actions]

[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download