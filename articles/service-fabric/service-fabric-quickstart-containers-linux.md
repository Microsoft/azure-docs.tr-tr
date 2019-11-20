---
title: Azure'da Service Fabric üzerinde Linux kapsayıcı uygulaması oluşturma | Microsoft Docs
description: Bu hızlı başlangıçta uygulamanızla bir Docker görüntüsü oluşturacak, görüntüyü bir kapsayıcı kayıt defterine iletecek ve kapsayıcınızı bir Service Fabric kümesine dağıtacaksınız.
services: service-fabric
documentationcenter: linux
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 0f6e24d2790f78ab0ec7292640fda3ab30e03a03
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599406"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>Hızlı Başlangıç: Linux kapsayıcıları Service Fabric'e dağıtma

Azure Service Fabric; ölçeklenebilir ve güvenilir mikro hizmetleri ve kapsayıcıları dağıtmayı ve yönetmeyi sağlayan bir dağıtılmış sistemler platformudur.

Bu hızlı başlangıçta, Linux kapsayıcılarını Azure 'da bir Service Fabric kümesine dağıtma gösterilmektedir. Tamamladığınızda Service Fabric kümesinde çalışan Python web ön ucu ve Redis arka ucundan oluşan bir oy verme uygulamasına sahip olacaksınız. Ayrıca, bir uygulamanın yükünü devretme ve kümenizde bir uygulamayı ölçeklendirme hakkında da bilgi edineceksiniz.

![Oylama uygulaması web sayfası][quickstartpic]

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için:

1. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

2. [Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)'yı yükleme

3. [SERVICE fabrıc SDK ve CLI](service-fabric-get-started-linux.md#installation-methods) 'yi yükler

4. [Git](https://git-scm.com/) 'i yükler


## <a name="get-the-application-package"></a>Uygulama paketini alma

Kapsayıcıları Service Fabric üzerinde dağıtmak için ayrı kapsayıcıları ve uygulamayı açıklayan bildirim dosyası (uygulama tanımı) kümesine ihtiyacınız vardır.

Bir konsolunda, uygulama tanımının bir kopyasını klonlamak için git ' i kullanın; ardından dizinleri kopyanızda `Voting` dizin olarak değiştirin.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Service Fabric kümesi oluşturma

Uygulamayı Azure'a dağıtmak için, uygulamayı çalıştıracak bir Service Fabric kümesine ihtiyacınız vardır. Aşağıdaki komutlar Azure 'da beş düğümlü bir küme oluşturur.  Komutlar Ayrıca otomatik olarak imzalanan bir sertifika oluşturur, bunu bir anahtar kasasına ekler ve sertifikayı yerel olarak indirir. Yeni sertifika, dağıtıldıktan sonra kümenin güvenliğini sağlamak için kullanılır ve istemcilerin kimliğini doğrulamak için kullanılır.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName="containertestcluster" 
ClusterName="containertestcluster" 
Location="eastus" 
Password="q6D7nN%6ck@6" 
Subject="containertestcluster.eastus.cloudapp.azure.com" 
VaultName="containertestvault" 
VmPassword="Mypa$$word!321"
VmUserName="sfadminuser"

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> Web ön ucu hizmeti, gelen trafik için 80 numaralı bağlantı noktasını dinlemek üzere yapılandırılmıştır. Varsayılan olarak, 80 numaralı bağlantı noktası, küme sanal makinelerinizdeki ve Azure Yük dengeleyicisinde açıktır.
>

## <a name="configure-your-environment"></a>Ortamınızı yapılandırma

Service Fabric, bir kümeyi ve uygulamalarını yönetmek için kullanabileceğiniz birçok araç sağlar:

- Tarayıcı tabanlı bir araç: Service Fabric Explorer.
- Azure CLI üzerinde çalışan Service Fabric Komut Satırı Arabirimi (CLI). 
- PowerShell komutları.

Bu hızlı başlangıçta Service Fabric CLı ve Service Fabric Explorer (bir Web tabanlı araç) kullanacaksınız. Service Fabric Explorer kullanmak için, sertifika PFX dosyasını tarayıcıya aktarmanız gerekir. Varsayılan olarak PFX dosyasının parolası yoktur.

Mozilla Firefox, Ubuntu 16,04 ' de varsayılan tarayıcıdır. Sertifikayı Firefox’a aktarmak için, tarayıcınızın sağ üst köşesindeki menü düğmesine ve ardından **Seçenekler**’e tıklayın. **Tercihler** sayfasında arama kutusunu kullanarak "sertifikalar" terimini arayın. **Sertifikaları Görüntüle**’ye tıklayın, **Sertifikalarınız** sekmesini seçin, **İçeri Aktar**’a tıklayın ve sertifikayı içeri aktarma istemlerini izleyin.

   ![Firefox’ta sertifika yükleme](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>Service Fabric uygulamasını dağıtma

1. CLı kullanarak Azure 'da Service Fabric kümesine bağlanın. Uç nokta, kümenizin yönetim uç noktasıdır. Önceki bölümde PEM dosyasını oluşturdunuz. 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. Yükleme betiğini kullanarak Oylama uygulaması tanımını kümeye kopyalayın, uygulama türünü kaydedin ve uygulamanın bir örneğini oluşturun.  PEK sertifika dosyası *install.sh* dosyası ile aynı dizinde bulunmalıdır.

    ```bash
    ./install.sh
    ```

3. Bir web tarayıcısı açın ve kümenizin Service Fabric Explorer uç noktasına gidin. Uç nokta biçimi şu şekildedir: **https://\<my-azure-service-fabric-cluster-url>:19080/Explorer**; örneğin, `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`. </br>

4. **Uygulamalar** düğümünü genişlettiğinizde oluşturduğunuz Oylama uygulama türü ve örneği için bir giriş oluşturulduğunu göreceksiniz.

    ![Service Fabric Explorer][sfx]

5. Çalışan kapsayıcıya bağlanmak için bir web tarayıcısı açın ve kümenizin URL'sine gidin; örneğin, `http://containertestcluster.eastus.cloudapp.azure.com:80`. Oy verme uygulamasını tarayıcıda görmeniz gerekir.

    ![Oylama uygulaması web sayfası][quickstartpic]

> [!NOTE]
> Docker compose ile Service Fabric uygulamaları da dağıtabilirsiniz. Örneğin, Docker Compose kullanarak uygulamayı kümeye dağıtıp yüklemek için aşağıdaki komut kullanılabilir.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Kümedeki bir kapsayıcıya yük devretme

Service Fabric, bir hata oluşması durumunda kapsayıcı örneklerinizin kümedeki diğer düğümlere otomatik olarak taşınmasını sağlar. Bir düğümü kapsayıcılar için el ile boşaltabilir ve kümedeki diğer düğümlere taşıyabilirsiniz. Service Fabric, hizmetlerinizi ölçeklendirmek için çeşitli yöntemler sağlar. Aşağıdaki adımlarda Service Fabric Explorer kullanacaksınız.

Ön uç kapsayıcısında yük devretmek için aşağıdaki adımları uygulayın:

1. Kümenizde Service Fabric Explorer'ı açın; örneğin, `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`.
2. Ağaç görünümünde **fabric:/Voting/azurevotefront** düğümüne tıklayın ve bölüm düğümünü (GUID ile gösterilir) genişletin. Ağaç görünümünde kapsayıcının üzerinde çalıştığı düğümleri gösteren düğüm adına dikkat edin; örneğin, `_nodetype_1`.
3. Ağaç görünümünde **Düğümler** düğümünü genişletin. Kapsayıcıyı çalıştıran düğümün yanındaki üç noktaya (...) tıklayın.
4. İlgili düğümü yeniden başlatmak için **Yeniden Başlat**'ı seçin ve yeniden başlatma eylemini onaylayın. Yeniden başlatma durumunda kapsayıcıdan kümedeki başka bir düğüme yük devretme gerçekleştirilir.

    ![Service Fabric Explorer'da düğüm görünümü][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Bir kümedeki uygulamaları ve hizmetleri ölçeklendirme

Hizmet yükünü karşılamak için bir kümedeki Service Fabric hizmetleri kolayca ölçeklendirilebilir. Kümede çalıştırılan örnek sayısını değiştirerek bir hizmeti ölçeklendirebilirsiniz.

Web ön uç hizmetini ölçeklendirmek için aşağıdaki adımları gerçekleştirin:

1. Kümenizde Service Fabric Explorer'ı açın; örneğin, `https://containertestcluster.eastus.cloudapp.azure.com:19080`.
2. Ağaç görünümünde **fabric:/Voting/azurevotefront** düğümünün yanındaki üç noktaya tıklayın ve **Hizmeti Ölçeklendir**'i seçin.

    ![Service Fabric Explorer hizmet ölçeklendirmeyi başlatma][containersquickstartscale]

    Şimdi web ön uç hizmetindeki örnek sayısını ölçeklendirebilirsiniz.

3. Rakamı **2** olarak değiştirin ve **Hizmeti Ölçeklendir**'e tıklayın.
4. Ağaç görünümünde **fabric:/Voting/azurevotefront** düğümüne tıklayın ve bölüm düğümünü (GUID ile gösterilir) genişletin.

    ![Service Fabric Explorer hizmet ölçeklendirme tamamlandı][containersquickstartscaledone]

    Hizmetin artık iki örneği olduğunu görebilirsiniz. Ağaç görünümünde örneklerin üzerinde çalıştığı düğümleri görebilirsiniz.

Bu basit yönetim görevi sayesinde ön uç hizmetinin kullanıcı yükünü işlemek için kullanabileceği kaynakları iki katına çıkarmış oldunuz. Bir hizmetin güvenilir bir şekilde çalışması için birden fazla örneğe ihtiyaç duymadığınızı anlamanız önemlidir. Bir hizmet başarısız olursa Service Fabric, kümede yeni bir hizmet örneği çalışmasını sağlar.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kümeden uygulama örneğini silmek ve uygulama türünün kaydını silmek için şablonda sağlanan kaldırma betiğini (uninstall.sh) kullanın. Bu betiğin örneği temizlemesi zaman alacağından betiği bu betikten hemen sonra çalıştırmamanız gerekir. Service Fabric Explorer'ı kullanarak örneğin ne zaman kaldırıldığını ve kaydı silinen uygulama türünü belirleyebilirsiniz.

```bash
./uninstall.sh
```

Kümeyi ve kullandığı tüm kaynakları silmenin en basit yolu, kaynak grubunun silinmesidir.

Azure 'da oturum açın ve kümeyi kaldırmak istediğiniz abonelik KIMLIĞINI seçin. Azure portal oturum açarak abonelik KIMLIĞINIZI bulabilirsiniz. [Az Group Delete komutunu](/cli/azure/group?view=azure-cli-latest)kullanarak kaynak grubunu ve tüm küme kaynaklarını silin.

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

Kümenizle çalışmayı tamamladıysanız, sertifikayı sertifika deposundan kaldırabilirsiniz. Örneğin:
- Windows'da: [SERTIFIKALAR MMC ek bileşenini](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)kullanın. Ek bileşeni eklerken **Kullanıcı hesabım**’ı seçtiğinizden emin olun. `Certificates - Current User\Personal\Certificates` sayfasına gidip sertifikayı kaldırın.
- Mac'te: Anahtarlık uygulamasını kullanın.
- Ubuntu 'da: Sertifikaları görüntülemek ve sertifikayı kaldırmak için kullandığınız adımları izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure’daki bir Service Fabric kümesine Linux kapsayıcı uygulaması dağıttınız, uygulama üzerinde bir yük devretme işlemi gerçekleştirdiniz ve uygulamayı kümede ölçeklendirdiniz. Service Fabric’te Linux kapsayıcılarıyla çalışma hakkında daha fazla bilgi için Linux kapsayıcı uygulamaları öğreticisiyle devam edin.

> [!div class="nextstepaction"]
> [Linux kapsayıcı uygulaması oluşturma](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
