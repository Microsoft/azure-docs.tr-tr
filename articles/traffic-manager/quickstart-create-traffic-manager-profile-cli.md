---
title: 'Hızlı başlangıç: uygulamalar için bir profil oluşturma-Azure CLı-Azure Traffic Manager'
description: Bu hızlı başlangıç makalesinde, yüksek oranda kullanılabilir bir Web uygulaması oluşturmak için bir Traffic Manager profili oluşturma açıklanmaktadır.
services: traffic-manager
author: asudbring
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: allensu
ms.openlocfilehash: 8b8880e10f9b920a2ec077d4cc4c2239e6ea7438
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034213"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak yüksek oranda kullanılabilir bir Web uygulaması için Traffic Manager profili oluşturma

Bu hızlı başlangıçta, Web uygulamanız için yüksek kullanılabilirlik sunan bir Traffic Manager profilinin nasıl oluşturulacağı açıklanmaktadır.

Bu hızlı başlangıçta, bir Web uygulamasının iki örneğini oluşturacaksınız. Bunların her biri farklı bir Azure bölgesinde çalışmaktadır. [Uç nokta önceliğine](traffic-manager-routing-methods.md#priority)göre bir Traffic Manager profili oluşturacaksınız. Profil, Kullanıcı trafiğini Web uygulamasını çalıştıran birincil siteye yönlendirir. Traffic Manager Web uygulamasını sürekli izler. Birincil site kullanılamıyorsa, yedekleme sitesine otomatik yük devretme sağlar.

Azure aboneliğiniz yoksa şimdi [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.28 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
[az group create](https://docs.microsoft.com/cli/azure/group) ile bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager profili oluşturma

Kullanıcı trafiğini uç nokta önceliğine göre yönlendiren [az Network Traffic-Manager profili oluşturma](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) ' yı kullanarak bir Traffic Manager profili oluşturun.

Aşağıdaki örnekte, **< profile_name >** benzersiz bir Traffic Manager profili adıyla değiştirin.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Web uygulamaları oluşturma

Bu hızlı başlangıçta iki farklı Azure bölgesinde (*Doğu ABD* ve *Batı Avrupa*) dağıtılan bir Web uygulamasının iki örneğine ihtiyacınız olacaktır. Her biri, Traffic Manager için birincil ve yük devretme uç noktaları olarak görev yapar.

### <a name="create-web-app-service-plans"></a>Web App Service planları oluşturma
İki farklı Azure bölgesinde dağıtacağınız Web uygulamasının iki örneği için [az appservice plan Create](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) komutunu kullanarak Web App Service planları oluşturun.

Aşağıdaki örnekte, **< appspname_eastus >** ve **< appspname_westeurope >** benzersiz bir App Service plan adıyla değiştirin

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>App Service planında bir Web uygulaması oluşturma
*Doğu ABD* ve Azure bölgelerindeki *Batı Avrupa* App Service planlarında [az WebApp Create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) kullanarak Web uygulaması için iki örnek oluşturun.

Aşağıdaki örnekte, **< app1name_eastus >** ve **< app2name_westeurope** > benzersiz bir uygulama adıyla değiştirin ve **<** appspname_eastus > ve **<** appspname_westeurope >, önceki bölümde App Service planlarını oluşturmak için kullanılan adla değiştirin.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager uç noktalarını ekleme
Aşağıdaki şekilde, [az Network Traffic-Manager uç noktası oluştur ' a](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) Traffic Manager profile kullanarak iki Web Apps uç nokta Traffic Manager olarak ekleyin:

- Web uygulaması kimliğini belirleme ve *Doğu ABD* Azure bölgesinde bulunan Web uygulamasını, tüm Kullanıcı trafiğini yönlendirmek için birincil uç nokta olarak ekleyin. 
- Web uygulaması kimliğini kesin ve *Batı Avrupa* Azure bölgesinde bulunan Web uygulamasını yük devretme uç noktası olarak ekleyin. 

Birincil uç nokta kullanılamadığında, trafik otomatik olarak yük devretme uç noktasına yönlendirir.

Aşağıdaki örnekte, **< app1name_eastus >** ve **< app2name_westeurope** > önceki bölümde yer alan her bir bölge için oluşturulan uygulama adlarıyla değiştirin, **<** appspname_eastus > ve **<** appspname_westeurope > önceki bölümde App Service planlarını oluşturmak için kullanılan adla değiştirin ve **<** profile_name > önceki bölümde kullanılan profil adıyla değiştirin. 

**Doğu ABD uç noktası**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```
Uç noktayı eklemek için çıktıda görünen kimliği ve aşağıdaki komutta kullanın:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**Batı Avrupa uç noktası**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```
Uç noktayı eklemek için çıktıda görünen kimliği ve aşağıdaki komutta kullanın:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>Traffic Manager profilinizi test etme

Bu bölümde, Traffic Manager profilinizin etki alanı adını kontrol edeceksiniz. Ayrıca birincil uç noktayı kullanılamaz olarak yapılandıracaksınız. Son olarak, Web uygulamasının hala kullanılabilir olduğunu görmeniz gerekir. Bunun nedeni, trafiği yük devretme uç noktasına gönderiyor Traffic Manager.

Aşağıdaki örnekte, **< app1name_eastus >** ve **< app2name_westeurope** > önceki bölümde yer alan her bir bölge için oluşturulan uygulama adlarıyla değiştirin, **<** appspname_eastus > ve **<** appspname_westeurope > önceki bölümde App Service planlarını oluşturmak için kullanılan adla değiştirin ve **<** profile_name > önceki bölümde kullanılan profil adıyla değiştirin.

### <a name="determine-the-dns-name"></a>DNS adını belirleme

[Az Network Traffic-Manager profile Show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show)kullanılarak TRAFFIC Manager profilinin DNS adını saptayın.

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

**Relativednsname** değerini kopyalayın. Traffic Manager profilinizin DNS adı *http://<* relativednsname *>. trafficmanager. net*' dir. 

### <a name="view-traffic-manager-in-action"></a>Traffic Manager'ın nasıl çalıştığını görün
1. Web tarayıcısında, Web uygulamanızın varsayılan Web sitesini görüntülemek için Traffic Manager profilinizin DNS adını (*http://<* relativednsname *>. trafficmanager. net*) girin.

    > [!NOTE]
    > Bu hızlı başlangıç senaryosunda, tüm istekler birincil uç noktaya yönlendirir. **Öncelik 1**olarak ayarlanır.
2. Traffic Manager yük devretmeyi eylemde görüntülemek için [az Network Traffic-Manager Endpoint Update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)kullanarak birincil sitenizi devre dışı bırakın.

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Web sitesini yeni bir Web tarayıcısı oturumunda görüntülemek için Traffic Manager profilinizin DNS adını (*http://<* relativednsname *>. trafficmanager. net*) kopyalayın.
4. Web uygulamasının hala kullanılabilir olduğunu doğrulayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

İşiniz bittiğinde, [az Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete)kullanarak kaynak gruplarını, Web uygulamalarını ve tüm ilgili kaynakları silin.

```azurepowershell-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Web uygulamanız için yüksek kullanılabilirlik sağlayan bir Traffic Manager profili oluşturdunuz. Yönlendirme trafiği hakkında daha fazla bilgi edinmek için Traffic Manager öğreticilerine geçin.

> [!div class="nextstepaction"]
> [Traffic Manager öğreticileri](tutorial-traffic-manager-improve-website-response.md)
