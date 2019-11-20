---
title: Hızlı başlangıç-Azure Container Instances-portala Docker kapsayıcısını dağıtma
description: Bu hızlı başlangıçta, yalıtılmış bir Azure Container örneğinde çalışan kapsayıcılı bir Web uygulamasını hızlıca dağıtmak için Azure portal kullanırsınız
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: bf3bea07fa2fcb67a467d4087ea9e2ccbfd95206
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325793"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Hızlı Başlangıç: Azure 'da bir kapsayıcı örneğini Azure portal kullanarak dağıtma

Azure 'da sunucusuz Docker kapsayıcılarını basitlik ve hızla çalıştırmak için Azure Container Instances kullanın. Azure Kubernetes hizmeti gibi tam kapsayıcı düzenleme platformu gerekmiyorsa, bir uygulamayı isteğe bağlı olarak bir kapsayıcı örneğine dağıtın.

Bu hızlı başlangıçta, yalıtılmış bir Docker kapsayıcısını dağıtmak ve uygulamayı tam etki alanı adı (FQDN) ile kullanılabilir hale getirmek için Azure portal kullanırsınız. Birkaç ayarı yapılandırdıktan ve kapsayıcıyı dağıttıktan sonra çalışan uygulamaya göz atabilirsiniz:

![Azure Container Instances hizmetine dağıtılmış uygulamanın tarayıcıdaki görüntüsü][aci-portal-07]

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com ) adresinden Azure portalında oturum açın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap][azure-free-account] oluşturun.

## <a name="create-a-container-instance"></a>Kapsayıcı örneği oluşturma

**Kaynak oluştur** > **Kapsayıcılar** > **Container Instances** seçeneğini belirleyin.

![Azure portalında yeni bir kapsayıcı örneği oluşturmaya başlama][aci-portal-01]

**Temel bilgiler** sayfasında, **kaynak grubu**, **kapsayıcı adı**ve **kapsayıcı görüntüsü** metin kutularına aşağıdaki değerleri girin. Diğer değerleri varsayılan değerlerinde bırakın ve **Tamam**’ı seçin.

* Kaynak grubu: **Yeni oluştur** > `myresourcegroup`
* Kapsayıcı adı: `mycontainer`
* Kapsayıcı görüntüsü: `mcr.microsoft.com/azuredocs/aci-helloworld`

![Azure portalında yeni bir kapsayıcı örneği için temel ayarları yapılandırma][aci-portal-03]

Bu hızlı başlangıçta, genel Microsoft `aci-helloworld` görüntüsünü dağıtmak için **genel** varsayılan **görüntü türü** ayarını kullanın. Bu Linux görüntüsü, statik bir HTML sayfasına hizmet veren Node. js ' de yazılmış küçük bir Web uygulamasını paketler.

**Ağ** sayfasında, Kapsayıcınız Için bir **DNS ad etiketi** belirtin. Ad, kapsayıcı örneğini oluşturduğunuz Azure bölgesi içinde benzersiz olmalıdır. Kapsayıcınız `<dns-name-label>.<region>.azurecontainer.io` konumunda genel kullanıma sunulacaktır. "DNS ad etiketi kullanılamıyor" hata iletisiyle karşılaşırsanız farklı bir DNS ad etiketi deneyin.

![Azure portalında yeni bir kapsayıcı örneği yapılandırma][aci-portal-04]

Diğer ayarları varsayılan olarak bırakın ve ardından **gözden geçir + oluştur**' u seçin.

Doğrulama tamamlandığında, kapsayıcı ayarlarının bir özeti gösterilir. Kapsayıcı dağıtım isteğinizi göndermek için **Oluştur** ' u seçin.

![Azure portalında yeni bir kapsayıcı örneği için ayarların özeti][aci-portal-05]

Dağıtım başladığında devam ettiğini gösteren bir bildirim görüntülenir. Kapsayıcı grubu dağıtıldığında yeni bir bildirim daha görüntülenir.

**Kaynak grupları** > **myresourcegroup** > **myContainer**' a giderek kapsayıcı grubu için genel bakışı açın. Kapsayıcı örneğinin **Durum**’u ile birlikte **FQDN**’sini (tam etki alanı adı) not edin.

![Azure portalında kapsayıcı grubuna genel bakış][aci-portal-06]

*Çalışıyor* **Durumunda** iken tarayıcınızda kapsayıcının FQDN’sine gidin.

![Azure Container Instances kullanılarak dağıtılmış uygulama tarayıcıda görüntüleniyor][aci-portal-07]

Tebrikler! Yalnızca birkaç ayarı yapılandırarak, Azure Container Instances’ta genel olarak erişilebilir bir uygulama dağıttınız.

## <a name="view-container-logs"></a>Kapsayıcı günlüklerini görüntüleme

Kapsayıcı örneğinin günlüklerini görüntülemek, kapsayıcınızın veya çalıştırdığı uygulamanın sorunlarını gidermede yardımcı olur.

Kapsayıcının günlüklerini görüntülemek için **Ayarlar** altında **Kapsayıcılar**’ı ve ardından **Günlükler**’i seçin. Uygulamayı tarayıcınızda görüntülediğinizde HTTP GET isteğinin oluşturulduğunu görmeniz gerekir.

![Azure portalında kapsayıcı günlükleri][aci-portal-11]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kapsayıcıyla işiniz bittiğinde, *mycontainer* kapsayıcı örneğine ait **Genel bakış**’ı ve ardından **Sil**’i seçin.

![Azure portalında kapsayıcı örneğini silme][aci-portal-09]

Onay iletişim kutusu görüntülendiğinde **Evet**’i seçin.

![Azure portalında kapsayıcı örneği onayını silme][aci-portal-10]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, genel Microsoft görüntüsünden bir Azure Kapsayıcı örneği oluşturdunuz. Kapsayıcı görüntüsünü oluşturup özel bir Azure kapsayıcı kayıt defterinden dağıtmak istiyorsanız Azure Container Instances öğreticisine geçin.

> [!div class="nextstepaction"]
> [Azure Container Instances öğreticisi](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png
[aci-portal-11]: ./media/container-instances-quickstart-portal/qs-portal-11.png

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/