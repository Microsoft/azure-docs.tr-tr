---
title: Hızlı başlangıç-Docker kapsayıcısını kapsayıcı örneğine dağıtma-Portal
description: Bu hızlı başlangıçta, yalıtılmış bir Azure Container örneğinde çalışan kapsayıcılı bir Web uygulamasını hızlıca dağıtmak için Azure portal kullanırsınız
ms.date: 08/24/2020
ms.topic: quickstart
ms.custom:
- mvc
- devx-track-js
- mode-portal
ms.openlocfilehash: c0189bbd04e454205b34a6415ab6109f95b6f51a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536228"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak Azure 'da kapsayıcı örneği dağıtma

Azure 'da sunucusuz Docker kapsayıcılarını basitlik ve hızla çalıştırmak için Azure Container Instances kullanın. Azure Kubernetes hizmeti gibi tam kapsayıcı düzenleme platformu gerekmiyorsa, bir uygulamayı isteğe bağlı olarak bir kapsayıcı örneğine dağıtın.

Bu hızlı başlangıçta, yalıtılmış bir Docker kapsayıcısını dağıtmak ve uygulamayı tam etki alanı adı (FQDN) ile kullanılabilir hale getirmek için Azure portal kullanırsınız. Birkaç ayarı yapılandırdıktan ve kapsayıcıyı dağıttıktan sonra çalışan uygulamaya göz atabilirsiniz:

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Azure Container Instances kullanılarak dağıtılmış uygulama tarayıcıda görüntüleniyor":::

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap][azure-free-account] oluşturun.

## <a name="create-a-container-instance"></a>Kapsayıcı örneği oluşturma

Container Instances **kaynak kapsayıcıları oluştur**' u seçin  >    >  .

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-01.png" alt-text="Azure portalında yeni bir kapsayıcı örneği oluşturmaya başlama":::

**Temel bilgiler** sayfasında, **kaynak grubu**, **kapsayıcı adı** ve **kapsayıcı görüntüsü** metin kutularına aşağıdaki değerleri girin. Diğer değerleri varsayılan değerlerinde bırakın ve **Tamam**’ı seçin.

* Kaynak grubu: **Yeni oluştur** > `myresourcegroup`
* Kapsayıcı adı: `mycontainer`
* Görüntü kaynağı: **hızlı başlangıç görüntüleri**
* Kapsayıcı görüntüsü: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-03.png" alt-text="Azure portalında yeni bir kapsayıcı örneği için temel ayarları yapılandırma":::

Bu hızlı başlangıçta, genel Microsoft görüntüsünü dağıtmak için varsayılan ayarları kullanırsınız `aci-helloworld` . Bu örnek Linux görüntüsü, statik bir HTML sayfasına hizmet veren Node.js yazılmış küçük bir Web uygulamasını paketler. Ayrıca, Azure Container Registry, Docker Hub veya diğer kayıt defterlerinde depolanan kendi kapsayıcı görüntülerinizi de getirebilirsiniz.

**Ağ** sayfasında, Kapsayıcınız Için bir **DNS ad etiketi** belirtin. Ad, kapsayıcı örneğini oluşturduğunuz Azure bölgesi içinde benzersiz olmalıdır. Kapsayıcınız `<dns-name-label>.<region>.azurecontainer.io` konumunda genel kullanıma sunulacaktır. "DNS ad etiketi kullanılamıyor" hata iletisiyle karşılaşırsanız farklı bir DNS ad etiketi deneyin.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-04.png" alt-text="Azure portal yeni bir kapsayıcı örneği için ağ ayarlarını yapılandırma":::

Diğer ayarları varsayılan olarak bırakın ve ardından **gözden geçir + oluştur**' u seçin.

Doğrulama tamamlandığında, kapsayıcı ayarlarının bir özeti gösterilir. Kapsayıcı dağıtım isteğinizi göndermek için **Oluştur** ' u seçin.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-05.png" alt-text="Azure portalında yeni bir kapsayıcı örneği için ayarların özeti":::

Dağıtım başladığında, dağıtımın devam ettiğini belirten bir bildirim görüntülenir. Kapsayıcı grubu dağıtıldığında yeni bir bildirim daha görüntülenir.

**Kaynak grupları**  >  **myresourcegroup**  >  **myContainer**' a giderek kapsayıcı grubu için genel bakışı açın. Kapsayıcı örneğinin **Durum**’u ile birlikte **FQDN**’sini (tam etki alanı adı) not edin.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-06.png" alt-text="Azure portalında kapsayıcı grubuna genel bakış":::

*Çalışıyor***Durumunda** iken tarayıcınızda kapsayıcının FQDN’sine gidin.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Azure Container Instances kullanılarak dağıtılmış uygulama tarayıcıda görüntüleniyor":::

Tebrikler! Yalnızca birkaç ayarı yapılandırarak, Azure Container Instances’ta genel olarak erişilebilir bir uygulama dağıttınız.

## <a name="view-container-logs"></a>Kapsayıcı günlüklerini görüntüleme

Kapsayıcı örneğinin günlüklerini görüntülemek, kapsayıcınızın veya çalıştırdığı uygulamanın sorunlarını gidermede yardımcı olur.

Kapsayıcının günlüklerini görüntülemek için **Ayarlar** altında **Kapsayıcılar**’ı ve ardından **Günlükler**’i seçin. Uygulamayı tarayıcınızda görüntülediğinizde HTTP GET isteğinin oluşturulduğunu görmeniz gerekir.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-11.png" alt-text="Azure portalında kapsayıcı günlükleri":::


## <a name="clean-up-resources"></a>Kaynakları temizleme

Kapsayıcıyla işiniz bittiğinde, *mycontainer* kapsayıcı örneğine ait **Genel bakış**’ı ve ardından **Sil**’i seçin.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-09.png" alt-text="Azure portal] öğesinde kapsayıcı örneği siliniyor":::

Onay iletişim kutusu görüntülendiğinde **Evet**’i seçin.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-10.png" alt-text="Azure portal] içindeki bir kapsayıcı örneğinin onayını silme":::

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, genel Microsoft görüntüsünden bir Azure Kapsayıcı örneği oluşturdunuz. Kapsayıcı görüntüsünü oluşturup özel bir Azure kapsayıcı kayıt defterinden dağıtmak istiyorsanız Azure Container Instances öğreticisine geçin.

> [!div class="nextstepaction"]
> [Azure Container Instances öğreticisi](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/
