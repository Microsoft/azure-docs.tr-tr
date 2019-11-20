---
title: Dağıtım kimlik bilgilerini Yapılandırma-Azure App Service | Microsoft Docs
description: Azure App Service dağıtım kimlik bilgilerini nasıl kullanacağınızı öğrenin.
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/14/2019
ms.author: cephalin
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: fc9445b64baae0e625b62356fee381329b01e8fd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098491"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Azure App Service için dağıtım kimlik bilgilerini yapılandırma
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) , [Yerel git dağıtımı](deploy-local-git.md) ve [FTP/S dağıtımı](deploy-ftp.md)için iki tür kimlik bilgilerini destekler. Bu kimlik bilgileri, Azure aboneliğiniz kimlik bilgilerinizle aynı değildir.

* **Kullanıcı düzeyi kimlik bilgileri**: tüm Azure hesabı için bir kimlik bilgileri kümesi. Azure hesabının erişim iznine sahip olduğu herhangi bir abonelikte her türlü uygulama için App Service dağıtmak üzere kullanılabilir. Bu, Portal GUI 'de (uygulamanın [kaynak sayfasının](../azure-resource-manager/manage-resources-portal.md#manage-resources) **genel bakış** ve **özellikleri** gibi) ortaya çıkacak olan varsayılan bir kümesidir. Bir kullanıcıya rol tabanlı Access Control (RBAC) veya coadmin (izinleri aracılığıyla uygulama erişimi verildiğinde, bu kullanıcı, erişim iptal edilene kadar kendi Kullanıcı düzeyi kimlik bilgilerini kullanabilir. Bu kimlik bilgilerini diğer Azure kullanıcılarıyla paylaşmayın.

* **Uygulama düzeyi kimlik bilgileri**: her bir uygulama için bir kimlik bilgileri kümesi. Yalnızca bu uygulamaya dağıtmak için kullanılabilir. Her uygulama için kimlik bilgileri, uygulama oluşturma sırasında otomatik olarak oluşturulur. Bunlar el ile yapılandırılamaz, ancak her zaman sıfırlanabilir. Bir kullanıcının uygulama düzeyi kimlik bilgilerine (RBAC) aracılığıyla erişim izni verilmesi için, bu kullanıcının uygulamada katkıda bulunması veya daha yüksek olması gerekir. Okuyucuların bu kimlik bilgilerine yayımlamasına ve bu kimlik bilgilerine erişemediğine izin verilmez.

## <a name="userscope"></a>Kullanıcı düzeyi kimlik bilgilerini yapılandırma

Kullanıcı düzeyi kimlik bilgilerinizi, herhangi bir uygulamanın [kaynak sayfasında](../azure-resource-manager/manage-resources-portal.md#manage-resources)yapılandırabilirsiniz. Bu kimlik bilgilerini yapılandırdığınız uygulamayı ne olursa olsun, tüm uygulamalar ve Azure hesabınızdaki tüm abonelikler için geçerlidir. 

### <a name="in-the-cloud-shell"></a>Cloud Shell

[Cloud Shell](https://shell.azure.com)dağıtım kullanıcısını yapılandırmak için [az WebApp Deployment User set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) komutunu çalıştırın. Kullanıcı \<adı > ve \<parola > bir dağıtım kullanıcısı Kullanıcı adı ve parolasıyla değiştirin. 

- Kullanıcı adı Azure içinde benzersiz olmalıdır ve yerel git gönderimleri için ' @ ' sembolünü içermemelidir. 
- Parola en az sekiz karakter uzunluğunda olmalıdır ve şu üç öğeden ikisi vardır: harfler, rakamlar ve semboller. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON çıktısı parolayı olarak `null`gösterir. `'Conflict'. Details: 409` hatası alırsanız kullanıcı adını değiştirin. `'Bad Request'. Details: 400` hatası alırsanız daha güçlü bir parola kullanın. 

### <a name="in-the-portal"></a>Portalda

Azure portal, dağıtım kimlik bilgileri sayfasına erişebilmek için en az bir uygulamanız olması gerekir. Kullanıcı düzeyi kimlik bilgilerinizi yapılandırmak için:

1. [Azure Portal](https://portal.azure.com), sol menüden **uygulama hizmetleri** >  **\<any_app >**  > **Deployment Center** > FTPpanosu > ' nu seçin.

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Ya da git dağıtımını zaten yapılandırdıysanız **App Services** >  >  **&lt;any_app >** **Dağıtım Merkezi** > **FTP/kimlik bilgileri**' ni seçin.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. **Kullanıcı kimlik bilgileri**' ni seçin, Kullanıcı adını ve parolayı yapılandırın ve ardından **kimlik bilgilerini kaydet**' i seçin.

Dağıtım kimlik bilgilerinizi ayarladıktan sonra, *Git* dağıtım Kullanıcı adını uygulamanızın **genel bakış** sayfasında bulabilirsiniz,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Git dağıtımı yapılandırılırsa, sayfada bir **Git/dağıtım Kullanıcı adı**gösterilir; Aksi takdirde, bir **FTP/Deployment Kullanıcı adı**.

> [!NOTE]
> Azure, Kullanıcı düzeyi dağıtım parolanızı göstermez. Parolayı unutursanız, bu bölümdeki adımları izleyerek kimlik bilgilerinizi sıfırlayabilirsiniz.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>FTP/FTPS ile Kullanıcı düzeyi kimlik bilgilerini kullanma

Kullanıcı düzeyindeki kimlik bilgilerini kullanarak bir FTP/FTPS uç noktası için kimlik doğrulaması, aşağıdaki biçimde bir Kullanıcı adı talep ediyor:`<app-name>\<user-name>`

Kullanıcı düzeyi kimlik bilgileri, belirli bir kaynak değil kullanıcıya bağlı olduğundan, oturum açma işlemini doğru uygulama uç noktasına yönlendirmek için Kullanıcı adının bu biçimde olması gerekir.

## <a name="appscope"></a>Uygulama düzeyi kimlik bilgilerini edinme ve sıfırlama
Uygulama düzeyi kimlik bilgilerini almak için:

1. [Azure Portal](https://portal.azure.com), sol menüden **App Services** >  **&lt;any_app >**  > **Deployment Center** > **FTP/Credentials**' ı seçin.

2. **Uygulama kimlik bilgilerini**seçin ve Kullanıcı adını veya parolayı kopyalamak için **Kopyala** bağlantısını seçin.

Uygulama düzeyi kimlik bilgilerini sıfırlamak için aynı iletişim kutusunda **kimlik bilgilerini Sıfırla** ' yı seçin.

## <a name="next-steps"></a>Sonraki adımlar

Uygulamanızı [Yerel git](deploy-local-git.md) 'Ten veya [FTP/S](deploy-ftp.md)kullanarak dağıtmak için bu kimlik bilgilerini nasıl kullanacağınızı öğrenin.
