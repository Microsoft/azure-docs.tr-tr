---
title: "Azure’da Docker kapsayıcı kümesi dağıtma | Microsoft Docs"
description: "Kubernetes, DC/OS veya Docker Swarm çözümünü, Azure portalı veya bir Resource Manager şablonu kullanarak Azure Container Service’e dağıtın."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Kapsayıcılar, Mikro hizmetler, Mesos, Azure, dcos, swarm, kubernetes, azure container service, acs"
ms.assetid: 696a736f-9299-4613-88c6-7177089cfc23
ms.service: container-service
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 2e8ff5304d4ccc63f92567b22c0508e3fc7b9bcf
ms.lasthandoff: 04/03/2017

---
# <a name="deploy-a-docker-container-hosting-solution-using-the-azure-portal"></a>Azure portalını kullanarak Docker kapsayıcısı barındırma çözümü dağıtma



Azure Kapsayıcı Hizmeti popüler açık kaynak kapsayıcı kümeleme ve düzenleme çözümlerinin hızlı dağıtımını sağlar. Bu belgede Azure portalı veya bir Azure Resource Manager hızlı başlangıç şablonu kullanarak Azure Container Service kümesini dağıtma işlemi gösterilir. 

Bir Azure Container Service kümesini ayrıca [Azure CLI 2.0](container-service-create-acs-cluster-cli.md) veya Azure Container Service API’leri kullanarak dağıtabilirsiniz.

Arka plan bilgileri için bkz. [Azure Container Service'e giriş](container-service-intro.md).


## <a name="prerequisites"></a>Ön koşullar

* **Azure aboneliği**: Aboneliğiniz yoksa [ücretsiz deneme](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935) sürümüne kaydolun. 

* **SSH RSA ortak anahtarı**: Portal veya Azure hızlı başlangıç şablonlarından biri ile dağıtım yaparken, Azure Container Service sanal makinelerinde kimlik doğrulamak için ortak anahtar belirtmeniz gerekir. Güvenli Kabuk (SSH) RSA anahtarları oluşturmak için [OS X ve Linux](../virtual-machines/linux/mac-create-ssh-keys.md) veya [Windows](../virtual-machines/linux/ssh-from-windows.md) kılavuzuna bakın. 

* **Hizmet sorumlusu istemci kimliği ve parolası** (yalnızca Kubernetes): Azure Active Directory Hizmet sorumlusu oluşturma hakkında bilgi ve yönergeler için bkz. [Kubernetes kümelerinde hizmet sorumlusu hakkında](container-service-kubernetes-service-principal.md).



## <a name="create-a-cluster-by-using-the-azure-portal"></a>Azure portalını kullanarak küme oluşturma
1. Azure portalında oturum açın, **Yeni**’yi seçin ve Azure Market’te **Azure Container Service**’i arayın.

    ![Market’te Azure Container Service](media/container-service-deployment/acs-portal1.png)  <br />

2. **Azure Container Service**'e ve ardından **Oluştur**'a tıklayın.

3. **Temel bilgiler** dikey penceresinde aşağıdaki bilgileri girin:

    * **Düzenleyici**: Kümede dağıtmak üzere kapsayıcı düzenleyicilerinden birini seçin.
        * **DC/OS**: DC/OS kümesi dağıtır.
        * **Swarm** Docker Swarm kümesi dağıtır.
        * **Kubernetes**: Bir Kubernetes kümesi dağıtır.
    * **Abonelik**: Bir Azure aboneliği seçin.
    * **Kaynak grubu**: Dağıtım için yeni bir kaynak grubu adı girin.
    * **Konum**: Azure Container Service dağıtımı için Azure bölgesini seçin. Kullanım durumu için bkz. [Bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/regions/services/).
    
    ![Temel ayarlar](media/container-service-deployment/acs-portal3.png)  <br />
    
    Devam etmeye hazır olduğunuzda **Tamam**’a tıklayın.

4. **Ana yapılandırma** dikey penceresinde kümedeki Linux ana düğümü veya düğümleri için aşağıdaki ayarları girin (bazı ayarlar her bir düzenleyiciye özeldir):

    * **Ana DNS adı**: Ana DNS için benzersiz bir tam etki alanı adı (FQDN) oluşturma amacıyla kullanılan ön ektir. Ana FQDN, *önek*mgmt.*konum*.cloudapp.azure.com şeklindedir.
    * **Kullanıcı adı**: Kümedeki Linux sanal makinelerin tümünde bulunan hesabın kullanıcı adıdır.
    * **SSH RSA ortak anahtarı**: Linux sanal makinelerinde kimlik doğrulaması için kullanılacak ortak anahtarı ekleyin. Bu anahtarın satır sonu içermemesi ve `ssh-rsa` ön ekini içermesi gerekir. `username@domain` son eki isteğe bağlıdır. Anahtar, şunun gibi görünmelidir: **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. 
    * **Hizmet sorumlusu**: Kubernetes düzenleyicisini seçtiyseniz Azure Active Directory **Hizmet sorumlusu istemci kimliği** (appId olarak da adlandırılır) ve **Hizmet sorumlusu istemci parolası** (parola) değerlerini girin. Daha fazla bilgi için bkz. [Bir Kubernetes kümesi için hizmet sorumlusu hakkında](container-service-kubernetes-service-principal.md).
    * **Ana sunucu sayısı**: Kümedeki ana sunucu sayısı.
    * **VM tanılama**: Bazı düzenleyiciler için ana sunucularda VM tanılamayı etkinleştirebilirsiniz.

    ![Ana sunucu yapılandırması](media/container-service-deployment/acs-portal4.png)  <br />

    Devam etmeye hazır olduğunuzda **Tamam**’a tıklayın.

5. **Aracı yapılandırması** dikey penceresinde aşağıdaki bilgileri girin:

    * **Aracı sayısı**: Docker Swarm ve Kubernetes için bu değer, aracı ölçek grubundaki aracıların başlangıçtaki sayısıdır. DC/OS için bu, özel ölçek grubundaki başlangıç aracıları sayısıdır. Ayrıca, DC/OS için önceden belirlenen sayıda aracı içeren bir ortak ölçek kümesi oluşturulur. Bu ortak ölçek kümesindeki aracıların sayısı, kümedeki ana sunucu sayısına göre belirlenir: bir ana sunucu için bir ortak aracı ve üç ya da beş ana sunucu için iki ortak sunucu.
    * **Aracı sanal makine boyutu**: Aracı sanal makinelerinin boyutudur.
    * **İşletim sistemi**: Bu ayarı yalnızca Kubernetes düzenleyicisini seçtiğinizde kullanabilirsiniz. Aracılarda çalıştırmak üzere bir Linux dağıtımı veya Windows Server işletim sistemi seçin. Bu ayar, kümenizin kapsayıcı uygulamalarında Linux mu yoksa Windows mu çalıştırabileceğini belirler. 

        > [!NOTE]
        > Windows kapsayıcı desteği Kubernetes kümeleri için önizleme sürümündedir. DC/OS ve Swarm kümelerinde Azure Container Service şu an için yalnızca Linux aracıları desteklemektedir.

    * **Aracı kimlik bilgileri**: Windows işletim sistemini seçtiyseniz aracı VM'ler için yönetici yetkilerine sahip **Kullanıcı adı** ve **Parola** girin. 

    ![Aracı yapılandırması](media/container-service-deployment/acs-portal5.png)  <br />

    Devam etmeye hazır olduğunuzda **Tamam**’a tıklayın.

6. Hizmet doğrulama tamamlandıktan sonra **Tamam**'a tıklayın.

    ![Doğrulama](media/container-service-deployment/acs-portal6.png)  <br />

7. Koşulları gözden geçirin. Dağıtım işlemini başlatmak için **Oluştur**'a tıklayın.

    Azure portalda dağıtımı sabitlemeyi seçtiyseniz dağıtım durumunu görebilirsiniz.

    ![Dağıtım durumu](media/container-service-deployment/acs-portal8.png)  <br />

Dağıtımın tamamlanması birkaç dakika sürer. Tamamlandıktan sonra, Azure Container Service kümesi kullanıma hazır duruma gelir.


## <a name="create-a-cluster-by-using-a-quickstart-template"></a>Hızlı başlangıç şablonu kullanarak küme oluşturma
Azure hızlı başlangıç şablonları, Azure Container Service’te küme dağıtmak için kullanılabilir. Sunulan hızlı başlangıç şablonu, ek veya gelişmiş Azure yapılandırmalarını dahil edecek şekilde değiştirilebilir. Azure hızlı başlangıç şablonu kullanarak Azure Container Service kümesi oluşturmak için bir Azure aboneliği gereklidir. Bir aboneliğiniz yoksa [ücretsiz deneme](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935) sürümüne kaydolun. 

Bir şablon ve Azure CLI 2.0 kullanarak küme dağıtmak için bu adımları izleyin (bkz. [yükleme ve kurulum yönergeleri](/cli/azure/install-az-cli2.md)).

> [!NOTE] 
> Bir Windows sistemi kullanıyorsanız, Azure PowerShell kullanarak şablon dağıtmak için benzer adımları kullanabilirsiniz. Bu bölümün sonraki kısımlarında verilen adımlara bakın. Ayrıca [portalı](../azure-resource-manager/resource-group-template-deploy-portal.md) veya diğer yöntemleri kullanarak da şablon dağıtabilirsiniz.

1. DC/OS, Docker Swarm veya Kubernetes kümesi dağıtmak için GitHub’da aşağıdaki hızlı başlangıç şablonlarından birini seçin. Kısmi bir liste görüntülenir. DC/OS ve Swarm şablonları, varsayılan düzenleyici seçimi dışında aynıdır.

    * [DC/OS şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Swarm şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Kubernetes şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Azure hesabınızda (`az login`) oturum açın ve Azure CLI’nin Azure aboneliğinize bağlı olduğundan emin olun. Aşağıdaki komutu kullanarak varsayılan aboneliği görebilirsiniz:

    ```azurecli
    az account show
    ```
    
    Birden fazla aboneliğiniz varsa ve farklı bir varsayılan abonelik ayarlamanız gerekiyorsa, `az account set --subscription` komutunu çalıştırıp abonelik kimliği ile adını belirtin.

3. En iyi uygulama olarak, dağıtım için yeni bir kaynak grubu kullanın. Bir kaynak grubu oluşturmak için `az group create` komutunu kullanarak bir kaynak grubu adı ve konumu belirtin: 

    ```azurecli
    az group create --name "RESOURCE_GROUP" --location "LOCATION"
    ```

4. Gerekli şablon parametrelerini içeren bir JSON dosyası oluşturun. GitHub’da `azuredeploy.json` Azure Container Service şablonuna eşlik eden `azuredeploy.parameters.json` adlı parametre dosyasını indirin. Kümeniz için gerekli parametre değerlerini girin. 

    Örneğin, [DC/OS şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) kullanmak için `dnsNamePrefix` ve `sshRSAPublicKey` parametre değerlerini belirtin. `azuredeploy.json` içindeki açıklamalara ve diğer parametrelerin seçeneklerine bakın.  
 

5. Dağıtım parametre dosyasını aşağıdaki komutla geçirerek bir Container Service kümesi oluşturun. Bu komutta:

    * **RESOURCE_GROUP** önceki adımda oluşturduğunuz kaynak grubunun adıdır.
    * **DEPLOYMENT_NAME** (isteğe bağlı), dağıtıma verdiğiniz addır.
    * **TEMPLATE_URI**, `azuredeploy.json` dağıtım dosyasının konumudur. Bu URI, GitHub kullanıcı arabirimi işaretçisi değil Ham dosya olmalıdır. Bu URI’yi bulmak için GitHub’da `azuredeploy.json` dosyasını seçin ve **Ham** düğmesine tıklayın.  

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters @azuredeploy.parameters.json
    ```

    Ayrıca, parametreleri komut satırında JSON biçimli bir dize olarak belirtebilirsiniz. Aşağıdakine benzer bir komut kullanın:

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters "{ \"param1\": {\"value1\"} … }"
    ```

    > [!NOTE]
    > Dağıtımın tamamlanması birkaç dakika sürer.
    > 

### <a name="equivalent-powershell-commands"></a>Eşdeğer PowerShell komutları
Azure Container Service küme şablonu dağıtmak için PowerShell de kullanabilirsiniz. Bu belge [Azure PowerShell modülü](https://azure.microsoft.com/blog/azps-1-0/) sürüm 1.0’ı temel alır.

1. DC/OS, Docker Swarm veya Kubernetes kümesi dağıtmak için GitHub’da aşağıdaki hızlı başlangıç şablonlarından birini seçin. Kısmi bir liste görüntülenir. DC/OS ve Swarm şablonlarının, varsayılan düzenleyici seçimi dışında aynı olduğunu unutmayın.

    * [DC/OS şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Swarm şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Kubernetes şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Azure aboneliğinizde küme oluşturmadan önce, PowerShell oturumunuz için Azure’da oturum açıldığını doğrulayın. Bunu `Get-AzureRMSubscription` komutuyla yapabilirsiniz.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Azure’da oturum açmanız gerekiyorsa `Login-AzureRMAccount` komutunu kullanın:

    ```powershell
    Login-AzureRmAccount
    ```

4. En iyi uygulama olarak, dağıtım için yeni bir kaynak grubu kullanın. Bir kaynak grubu oluşturmak için `New-AzureRmResourceGroup` komutunu kullanın ve kaynak grubu adı ile hedef bölgesini belirtin:

    ```powershell
    New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
    ```

5. Bir kaynak grubu oluşturduktan sonra, kümenizi aşağıdaki komutla oluşturabilirsiniz. İstenen şablon URI'si `-TemplateUri` parametresiyle belirtilir. Bu komutu çalıştırdığınızda, PowerShell sizden dağıtım parametre değerlerini ister.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

#### <a name="provide-template-parameters"></a>Şablon parametrelerini belirtin
PowerShell hakkında bilginiz varsa, eksi işareti (-) yazarak ve ardından SEKME tuşuna basarak bir cmdlet için kullanılabilir parametrelerde gezinebileceğinizi bilirsiniz. Bu işlev şablonunuzda tanımladığınız parametreler için de geçerlidir. Şablon adını yazmanızın hemen ardından, cmdlet şablonu getirir, parametreleri ayrıştırır ve şablon parametrelerini dinamik olarak komuta ekler. Bu, şablon parametre değerlerini belirtmeyi kolaylaştırır. Ve gerekli parametre değerini unutursanız, PowerShell sizden değeri ister.

Burada parametreleri içeren tam komut verilmiştir. Kaynakların adları için kendi değerlerinizi sağlayın.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Sonraki adımlar
Artık çalışan bir kümeniz olduğuna göre, bağlantı ve yönetim ayrıntıları için aşağıdaki belgelere bakın:

* [Azure Container Service kümesine bağlanma](container-service-connect.md)
* [Azure Container Service ve DC/OS ile çalışma](container-service-mesos-marathon-rest.md)
* [Azure Container Service ve Docker Swarm ile çalışma](container-service-docker-swarm.md)
* [Azure Container Service ve Kubernetes ile çalışma](container-service-kubernetes-walkthrough.md)

