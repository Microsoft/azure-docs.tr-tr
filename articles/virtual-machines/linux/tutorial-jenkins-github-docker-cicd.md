---
title: Öğretici-Jenkins ile Azure 'da bir geliştirme işlem hattı oluşturma
description: Bu öğreticide, Azure’da işlenen her kodu GitHub’dan çeken ve uygulamanızı çalıştırmak için yeni bir Docker kapsayıcısı oluşturan bir Jenkins sanal makinesi oluşturmayı öğrenirsiniz.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6b755ac015095e537134f1ff5c076c23a432ec91
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034480"
---
# <a name="tutorial-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>Öğretici: Azure’da Jenkins, GitHub ve Docker ile bir Linux sanal makinesi üzerinde geliştirme altyapısı oluşturma

Uygulama geliştirme sürecinin derleme ve test aşamasını otomatikleştirmek için bir sürekli tümleştirme ve dağıtım (CI/CD) işlem hattı kullanabilirsiniz. Bu öğreticide, aşağıdakileri öğrenerek bir Azure sanal makinesinde CI/CD işlem hattı oluşturursunuz:

> [!div class="checklist"]
> * Jenkins sanal makinesi oluşturma
> * Jenkins’i yükleme ve yapılandırma
> * GitHub ile Jenkins arasında web kancası tümleştirmesi oluşturma
> * GitHub işlemelerinden Jenkins derleme işleri oluşturma ve tetikleme
> * Uygulamanız için bir Docker görüntüsü oluşturma
> * GitHub işlemelerinin yeni Docker görüntüsü oluşturduğunu ve çalışmakta olan uygulamayı güncelleştirdiğini doğrulama

Bu öğretici, en son sürüme sürekli olarak güncellenen [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)içindeki CLI 'yi kullanır. Cloud Shell açmak için herhangi bir kod bloğunun en üstünden **deneyin** ' i seçin.

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.30 veya sonraki bir sürümünü çalıştırmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).

## <a name="create-jenkins-instance"></a>Jenkins örneği oluşturma
[İlk önyüklemede Linux sanal makinelerini özelleştirme](tutorial-automate-vm-deployment.md) konulu önceki bir öğreticide, cloud-init ile VM özelleştirmeyi nasıl otomatikleştirebileceğinizi öğrendiniz. Bu öğreticide, bir VM’ye Jenkins ve Docker yüklemek için cloud-init dosyası kullanılır. Jenkins, sürekli tümleştirme (CI) ve sürekli teslimi (CD) etkinleştirmek için Azure ile sorunsuz bir şekilde tümleştirilen popüler bir açık kaynak otomasyon sunucusudur. Jenkins kullanmayla ilgili diğer öğreticiler için bkz. [Azure’da Jenkins merkezi](https://docs.microsoft.com/azure/jenkins/).

Geçerli kabuğunuzda *cloud-init-jenkins.txt* adlı bir dosya oluşturup aşağıdaki yapılandırmayı yapıştırın. Örneğin, dosyayı yerel makinenizde değil Cloud Shell’de oluşturun. Dosyayı oluşturmak ve kullanılabilir düzenleyicilerin listesini görmek için `sensible-editor cloud-init-jenkins.txt` adını girin. Başta birinci satır olmak üzere cloud-init dosyasının tamamının doğru bir şekilde kopyalandığından emin olun:

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - apt install openjdk-8-jre-headless -y
  - wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
  - sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

VM oluşturabilmek için önce [az group create](/cli/azure/group) ile bir kaynak grubu oluşturun. Aşağıdaki örnekte, *eastus* konumunda *myResourceGroupJenkins* adlı bir kaynak grubu oluşturulur:

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Şimdi [az vm create](/cli/azure/vm) ile bir VM oluşturun. `--custom-data` parametresini kullanarak cloud-init yapılandırma dosyanızı geçirin. Dosyayı geçerli çalışma dizininizin dışına kaydettiyseniz *cloud-init-jenkins.txt* dosyasının tam yolunu belirtin.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

Sanal makinenin oluşturulup yapılandırılması birkaç dakika sürer.

VM’nize web trafiğinin ulaşmasına izin vermek için [az vm open-port](/cli/azure/vm) komutunu kullanarak Jenkins trafiği için *8080* bağlantı noktasını, Node.js uygulaması için örnek uygulama çalıştırmaya yönelik *1337* bağlantı noktasını açın:

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Jenkins’i yapılandırma
Jenkins örneğinize erişmek için VM’nizin genel IP adresini edinin:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Güvenlik nedeniyle, Jenkins yüklemesini başlatmak için VM’nizde bir metin dosyasında saklanan ilk yönetici parolasını girmeniz gerekir. Önceki adımda edinilen genel IP adresini kullanarak VM’niz ile SSH bağlantısı kurun:

```bash
ssh azureuser@<publicIps>
```

`service` komutunu kullanarak Jenkins 'nin çalıştığını doğrulayın:

```bash
$ service jenkins status
● jenkins.service - LSB: Start Jenkins at boot time
   Loaded: loaded (/etc/init.d/jenkins; generated)
   Active: active (exited) since Tue 2019-02-12 16:16:11 UTC; 55s ago
     Docs: man:systemd-sysv-generator(8)
    Tasks: 0 (limit: 4103)
   CGroup: /system.slice/jenkins.service

Feb 12 16:16:10 myVM systemd[1]: Starting LSB: Start Jenkins at boot time...
...
```

Jenkins yüklemenizin `initialAdminPassword` değerini görüntüleyin ve kopyalayın:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Dosya henüz kullanılamıyorsa cloud-init tarafından Jenkins ve Docker yüklemesinin tamamlanması için birkaç dakika daha bekleyin.

Şimdi bir web tarayıcısı açıp `http://<publicIps>:8080` adresine gidin. Aşağıdaki adımları uygulayarak ilk Jenkins kurulumunu tamamlayın:

- **Yüklenecek eklentileri seçin** seçeneğini belirleyin
- Üstteki metin kutusundan *GitHub* araması yapın. *GitHub* kutusunu işaretleyin ve **Yükle**’yi seçin
- İlk yönetici kullanıcıyı oluşturun. **admin** gibi bir kullanıcı adı girin ve sonra kendi güvenli parolanızı belirtin. Son olarak, bir tam ad ve e-posta adresi yazın.
- **Kaydet ve Bitir**’i seçin
- Jenkins hazır olduktan sonra **Jenkins kullanmaya başla**’yı seçin
  - Jenkins kullanmaya başladığınızda web tarayıcınız boş bir sayfa görüntülerse, Jenkins hizmetini yeniden başlatın. SSH oturumundan `sudo service jenkins restart` yazın ve web tarayıcınızı yenileyin.
- Gerekirse, Jenkins 'de oluşturduğunuz Kullanıcı adı ve parolayla oturum açın.


## <a name="create-github-webhook"></a>GitHub web kancası oluşturma
GitHub tümleştirmesini yapılandırmak için Azure örnek deposundan [Node.js Hello World örnek uygulamasını](https://github.com/Azure-Samples/nodejs-docs-hello-world) açın. Depoyu kendi GitHub hesabınızla çatallamak için sağ üst köşedeki **Çatal Oluştur** düğmesini seçin.

Oluşturduğunuz çatalın içinde bir web kancası oluşturun:

- **Ayarlar**' ı seçin ve ardından sol taraftaki **Web kancaları** ' nı seçin.
- **Web kancası Ekle**' yi seçin ve ardından, filtre kutusuna *Jenkins* yazın.
- **Yük URL 'si**için `http://<publicIps>:8080/github-webhook/`girin. Sondaki / karakterini eklemeyi unutmayın
- **İçerik türü**için *Application/x-www-form-urlencoded*öğesini seçin.
- **Bu Web kancasını tetiklemek istediğiniz olayları seçin?** *yalnızca anında iletme olayını seçin.*
- **Etkin** olarak işaretlendi olarak ayarlayın.
- **Web kancası Ekle**' ye tıklayın.

![GitHub web kancasını çatalı oluşturulan deponuza ekleyin](media/tutorial-jenkins-github-docker-cicd/github_webhook.png)


## <a name="create-jenkins-job"></a>Jenkins işi oluşturma
Jenkins’in GitHub’daki kod işleme gibi olaylara yanıt vermesini sağlamak için bir Jenkins işi oluşturun. Kendi GitHub çatalınız için URL’leri kullanın.

Jenkins web sitenizde, giriş sayfasından **Yeni iş oluştur**’u seçin:

- İş adı olarak *HelloWorld* adını girin. **Serbest tarzda proje**’yi seçip **Tamam**’ı seçin.
- **Genel** bölümünden **GitHub projesi**’ni seçip çatalı oluşturulan deponuzun URL’sini *https://github.com/cynthn/nodejs-docs-hello-world* şeklinde girin
- **Kaynak kodu yönetimi** bölümünden **Git**’i seçip çatalı oluşturulan deponuzun *.git* URL’sini *https://github.com/cynthn/nodejs-docs-hello-world.git* şeklinde girin
- **Derleme Tetikleyicileri** bölümünden **GITScm yoklaması için GitHub kanca tetikleyicisi**’ni seçin.
- **Derleme** bölümünden **Derleme adımı ekle**’yi seçin. **Kabuğu yürüt**’ü seçin ve komut penceresine `echo "Test"` ifadesini girin.
- İşler penceresinin en altından **Kaydet**’i belirleyin.


## <a name="test-github-integration"></a>GitHub tümleştirmesini test etme
Jenkins ile GitHub tümleştirmesini test etmek için çatalınızda bir değişiklik işleyin. 

GitHub web kullanıcı arabirimine dönerek çatalı oluşturulan deponuzu seçip **index.js** dosyasını seçin. Kalem simgesini seçerek bu dosyayı 6. satırda şu yazacak şekilde düzenleyin:

```javascript
response.end("Hello World!");
```

Değişikliklerinizi işlemek için alttaki **Değişiklikleri işle** düğmesini seçin.

Jenkins’de işinizin bulunduğu sayfanın sol alt köşesindeki **Derleme geçmişi** bölümünün altında yeni bir derleme başlar. Derleme numarası bağlantısını seçip sol taraftan **Konsol çıktısı**’nı seçin. Kodunuz GitHub’dan çekilirken ve derleme eylemi tarafından konsola çıktı olarak `Test` mesajı iletilirken Jenkins’in uyguladığı adımları görebilirsiniz. GitHub’da her işleme gerçekleştirildiğinde web kancası bu şekilde Jenkins’e ulaşır ve yeni bir derleme tetikler.


## <a name="define-docker-build-image"></a>Docker derleme görüntüsünü tanımlama
Node.js uygulamasının GitHub işlemelerinize bağlı olarak çalışmasını görmek için uygulamayı çalıştırmaya yönelik bir Docker görüntüsü oluşturalım. Görüntü, uygulamayı çalıştıran kapsayıcının nasıl yapılandırılacağını tanımlayan bir Dockerfile’dan oluşturulur. 

VM’niz ile kurulan SSH bağlantısından, önceki adımlardan birinde oluşturduğunuz işin adını verdiğiniz Jenkins çalışma dizinine geçin. Bu örnekte *HelloWorld* olarak adlandırılmıştı.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

`sudo sensible-editor Dockerfile` ile bu çalışma alanı dizininde bir dosya oluşturun ve aşağıdaki içeriği yapıştırın. Başta birinci satır olmak üzere Dockerfile’ın tamamının doğru bir şekilde kopyalandığından emin olun:

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

Bu Dockerfile, Alpine Linux kullanan temel Node.js görüntüsünü kullanır, Hello World uygulamasının üzerinde çalıştığı 1337 bağlantı noktasını kullanıma açar ve sonra uygulama dosyalarını kopyalayıp uygulamayı başlatır.


## <a name="create-jenkins-build-rules"></a>Jenkins derleme kuralları oluşturma
Önceki adımlardan birinde, çıktı olarak konsola mesaj gönderen temel bir Jenkins derleme kuralı oluşturmuştunuz. Dockerfile’ımızı kullanmak için derleme adımını oluşturalım ve uygulamayı çalıştıralım.

Jenkins örneğine dönerek önceki adımlardan birinde oluşturduğunuz işi seçin. Sol taraftan **Yapılandır**’ı seçin ve aşağı kaydırarak **Derleme** bölümüne gidin:

- Mevcut `echo "Test"` derleme adımınızı kaldırın. Mevcut derleme adımı kutusunun sağ üst köşesindeki kırmızı çarpı işaretini seçin.
- **Derleme adımı ekle**’yi seçip **Kabuğu yürüt**’ü seçin
- **Komut** kutusuna aşağıdaki Docker komutlarını girip **Kaydet**’i seçin:

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

Docker derleme adımları bir görüntü oluşturur ve görüntülerin geçmişini tutabilmeniz için bunu Jenkins derleme numarasıyla etiketler. Uygulamayı çalıştıran mevcut kapsayıcılar varsa bunlar durdurulup kaldırılır. Daha sonra, yeni bir kapsayıcı görüntüyü kullanmaya başlar ve GitHub’daki en son işlemeleri temel alarak Node.js uygulamanızı çalıştırır.


## <a name="test-your-pipeline"></a>İşlem hattınızı test etme
İşlem hattının tamamını iş başında görmek için çatalı oluşturulan GitHub deponuzdaki *index.js* dosyasını yeniden düzenleyin ve **Değişikliği işle**’yi seçin. Jenkins’de GitHub’a yönelik web kancasını temel alan yeni bir iş başlatılır. Docker görüntüsünün oluşturulup uygulamanızın yeni bir kapsayıcıda başlatılması birkaç saniye alır.

Gerekirse VM’nizin genel IP adresini yeniden edinin:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Bir web tarayıcısı açıp `http://<publicIps>:1337` adresini girin. Node.js uygulamanız görüntülenir ve GitHub çatalınızdaki son işlemeleri aşağıdaki gibi gösterir:

![Node.js uygulaması çalıştırma](media/tutorial-jenkins-github-docker-cicd/running_nodejs_app.png)

Şimdi GitHub’daki *index.js* dosyasında bir düzenleme işlemi daha yapın ve değişikliği işleyin. Jenkins’deki işin tamamlanması için birkaç saniye bekleyin, sonra uygulamanızın güncelleştirilen sürümünün aşağıdaki gibi yeni bir kapsayıcıda çalıştığını görmek için web tarayıcınızı yenileyin:

![Başka bir GitHub işlemesinden sonra Node.js uygulamasını çalıştırma](media/tutorial-jenkins-github-docker-cicd/another_running_nodejs_app.png)


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, GitHub’ı her kod işlemesinde bir Jenkins derleme işi çalıştıracak ve sonra uygulamanızı test etmek için bir Docker kapsayıcısı dağıtacak şekilde yapılandırdınız. Şunları öğrendiniz:

> [!div class="checklist"]
> * Jenkins sanal makinesi oluşturma
> * Jenkins’i yükleme ve yapılandırma
> * GitHub ile Jenkins arasında web kancası tümleştirmesi oluşturma
> * GitHub işlemelerinden Jenkins derleme işleri oluşturma ve tetikleme
> * Uygulamanız için bir Docker görüntüsü oluşturma
> * GitHub işlemelerinin yeni Docker görüntüsü oluşturduğunu ve çalışmakta olan uygulamayı güncelleştirdiğini doğrulama

Jenkins’i Azure DevOps Services ile tümleştirme hakkında daha fazla bilgi edinmek için bir sonraki öğretici ilerleyin.

> [!div class="nextstepaction"]
> [Jenkins ve Azure DevOps Services ile uygulama dağıtma](tutorial-build-deploy-jenkins.md)
