---
title: Dağıtım kimlik bilgilerini yapılandırma
description: Azure App Service dağıtım kimlik bilgileri türlerini ve bunların nasıl yapılandırılacağını ve kullanılacağını öğrenin.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 008bfa58c117fc1b43227ba73902d921cec25795
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830584"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Azure App Service için dağıtım kimlik bilgilerini yapılandırma
Yerel bir bilgisayardan uygulama dağıtımını güvenli hale getirmek için [Azure App Service](./overview.md) , [Yerel git dağıtımı](deploy-local-git.md) ve [FTP/S dağıtımı](deploy-ftp.md)için iki tür kimlik bilgilerini destekler. Bu kimlik bilgileri, Azure aboneliğiniz kimlik bilgilerinizle aynı değildir.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

> [!NOTE]
> Eski dağıtım deneyimi olan Azure portal **Geliştirme Merkezi (klasik)** sayfası mart, 2021 ' de kullanım dışı olacaktır. Bu değişiklik uygulamanızdaki mevcut dağıtım ayarlarını etkilemez ve **Dağıtım Merkezi** sayfasında uygulama dağıtımını yönetmeye devam edebilirsiniz.

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>Kullanıcı kapsamı kimlik bilgilerini yapılandırma

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[Az WebApp Deployment User set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) komutunu çalıştırın. \<username>Ve öğesini \<password> bir dağıtım kullanıcısı Kullanıcı adı ve parolasıyla değiştirin. 

- Kullanıcı adı Azure içinde benzersiz olmalıdır ve yerel git gönderimleri için ' @ ' sembolünü içermemelidir. 
- Parola en az sekiz karakter uzunluğunda olmalıdır ve şu üç öğeden ikisi vardır: harfler, rakamlar ve semboller. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON çıktısı parolayı olarak gösterir `null` .

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Kullanıcı kapsamı kimlik bilgilerini Azure PowerShell ile yapılandıramazsınız. Farklı bir yöntem kullanın veya [uygulama kapsamı kimlik bilgilerini](#appscope)kullanmayı göz önünde bulundurun. 

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Kullanıcı kapsamı kimlik bilgilerinizi, herhangi bir uygulamanın [kaynak sayfasında](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)yapılandırabilirsiniz. Bu kimlik bilgilerini yapılandırdığınız uygulamayı ne olursa olsun, Azure hesabınızdaki tüm abonelikler için tüm uygulamalara uygulanır. 

[Azure Portal](https://portal.azure.com), dağıtım kimlik bilgileri sayfasına erişebilmek için en az bir uygulamanız olması gerekir. Kullanıcı kapsamı kimlik bilgilerinizi yapılandırmak için:

1. Uygulamanızın sol menüsünde, > **Deployment Center**  >  **FTPS kimlik bilgileri** veya **Yerel git/FTPS kimlik bilgileri**' ni seçin.

    ![Azure Uygulama Hizmetleri 'nde dağıtım merkezi 'nden FTP panosunu nasıl seçkullanabileceğinizi gösterir.](./media/app-service-deployment-credentials/access-no-git.png)

2. **Kullanıcı kapsamına** aşağı kaydırın, Kullanıcı **adını** ve **parolayı** yapılandırın ve ardından **Kaydet**' i seçin.

Dağıtım kimlik bilgilerinizi ayarladıktan sonra, *Git* dağıtım Kullanıcı adını uygulamanızın **genel bakış** sayfasında bulabilirsiniz,

![Uygulamanızın genel bakış sayfasında git dağıtım Kullanıcı adının nasıl bulunacağını gösterir.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Git dağıtımı yapılandırılırsa, sayfada bir **Git/dağıtım Kullanıcı adı** gösterilir; Aksi takdirde, bir **FTP/Deployment Kullanıcı adı**.

> [!NOTE]
> Azure, kullanıcı kapsamı dağıtım parolanızı göstermez. Parolayı unutursanız, bu bölümdeki adımları izleyerek kimlik bilgilerinizi sıfırlayabilirsiniz.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>FTP/FTPS ile kullanıcı kapsamı kimlik bilgilerini kullanma

Kullanıcı kapsamı kimlik bilgilerini kullanan bir FTP/FTPS uç noktası kimlik doğrulaması için aşağıdaki biçimde bir Kullanıcı adı gerekir: `<app-name>\<user-name>`

Kullanıcı kapsamı kimlik bilgileri, belirli bir kaynak değil kullanıcıya bağlı olduğundan, oturum açma işlemini doğru uygulama uç noktasına yönlendirmek için Kullanıcı adının bu biçimde olması gerekir.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>Uygulama kapsamı kimlik bilgilerini al

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[Az WebApp Deployment List-Publishing-Profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) komutunu kullanarak uygulama kapsamı kimlik bilgilerini alın. Örnek:

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

[Yerel git dağıtımı](deploy-local-git.md)için, uygulamanız Için bir GIT uzak URI 'sini zaten katıştırılmış uygulama kapsamı kimlik bilgileri ile almak için [az WebApp Deployment List-Publishing-Credentials](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) komutunu da kullanabilirsiniz. Örnek:

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) komutunu kullanarak uygulama kapsamı kimlik bilgilerini alın. Örnek:

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

1. Uygulamanızın sol menüsünde, **Deployment Center**  >  **FTPS kimlik bilgileri** veya **Yerel git/FTPS kimlik bilgileri**' ni seçin.

    ![Azure Uygulama Hizmetleri 'nde dağıtım merkezi 'nden FTP panosunu nasıl seçkullanabileceğinizi gösterir.](./media/app-service-deployment-credentials/access-no-git.png)

2. **Uygulama kapsamı** bölümünde, Kullanıcı adını veya parolayı kopyalamak için **Kopyala** bağlantısını seçin.

-----

## <a name="reset-application-scope-credentials"></a>Uygulama kapsamı kimlik bilgilerini sıfırlama

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[Az Resource Invoke-Action](/cli/azure/resource#az_resource_invoke_action) komutunu kullanarak uygulama kapsamı kimlik bilgilerini sıfırlayın:

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) komutunu kullanarak uygulama kapsamı kimlik bilgilerini sıfırlayın:

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

1. Uygulamanızın sol menüsünde, **Deployment Center**  >  **FTPS kimlik bilgileri** veya **Yerel git/FTPS kimlik bilgileri**' ni seçin.

    ![Azure Uygulama Hizmetleri 'nde dağıtım merkezi 'nden FTP panosunu nasıl seçkullanabileceğinizi gösterir.](./media/app-service-deployment-credentials/access-no-git.png)

2. **Uygulama kapsamı** bölümünde **Sıfırla**' yı seçin.

-----

## <a name="disable-basic-authentication"></a>Temel kimlik doğrulamasını devre dışı bırak

Bazı kuruluşların güvenlik gereksinimlerini karşılaması gerekir ve FTP veya WebDeploy aracılığıyla erişimi devre dışı bırakır. Bu şekilde, kuruluşun üyeleri uygulama hizmetlerine yalnızca Azure Active Directory (Azure AD) tarafından denetlenen API 'Ler aracılığıyla erişebilir.

### <a name="ftp"></a>FTP

Siteye FTP erişimini devre dışı bırakmak için aşağıdaki CLı komutunu çalıştırın. Yer tutucuları kaynak grubunuz ve site adınızla değiştirin. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

FTP erişiminin engellendiğini onaylamak için FileZilla gibi bir FTP istemcisi kullanarak kimlik doğrulaması yapmayı deneyebilirsiniz. Yayımlama kimlik bilgilerini almak için sitenizin genel bakış dikey penceresine gidin ve yayımlama profilini Indir ' e tıklayın. Kimlik doğrulaması için dosyanın FTP ana bilgisayar adını, Kullanıcı adını ve parolasını kullanın ve yetkili olmadığını belirten bir 401 hata yanıtı alırsınız.

### <a name="webdeploy-and-scm"></a>WebDeploy ve SCM

WebDeploy bağlantı noktası ve SCM sitesine temel kimlik doğrulaması erişimini devre dışı bırakmak için aşağıdaki CLı komutunu çalıştırın. Yer tutucuları kaynak grubunuz ve site adınızla değiştirin. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

WebDeploy 'de yayımlama profili kimlik bilgilerinin engellendiğini onaylamak için, [Visual Studio 2019 kullanarak bir Web uygulaması yayımlamayı](/visualstudio/deployment/quickstart-deploy-to-azure)deneyin.

### <a name="disable-access-to-the-api"></a>API 'ye erişimi devre dışı bırakma

Önceki bölümde yer alan API, desteklenen Azure rol tabanlı erişim denetimi (Azure RBAC), [özel bir rol oluşturabileceğiniz](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) ve diğer sitelerde temel kimlik doğrulamasını etkinleştiremeyecek şekilde role daha düşük öncelikli kullanıcılar atayabilecekleri anlamına gelir. Özel rolü yapılandırmak için [Bu yönergeleri izleyin](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

[Azure izleyici](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) 'yi, tüm başarılı kimlik doğrulama isteklerini denetlemek ve [Azure ilkesi](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) 'ni kullanarak aboneliğinizdeki tüm siteler için bu yapılandırmayı zorunlu kılmak için de kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Uygulamanızı [Yerel git](deploy-local-git.md) 'Ten veya [FTP/S](deploy-ftp.md)kullanarak dağıtmak için bu kimlik bilgilerini nasıl kullanacağınızı öğrenin.
