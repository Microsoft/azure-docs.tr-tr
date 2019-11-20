---
title: Öğretici-Azure Red Hat OpenShift kümesi oluşturma | Microsoft Docs
description: Azure CLı kullanarak Microsoft Azure Red Hat OpenShift kümesi oluşturmayı öğrenin
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 11/04/2019
ms.openlocfilehash: 4a09a0fe4aa1f04e665aeb71ebece17a8b368090
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582383"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Öğretici: Azure Red Hat OpenShift kümesi oluşturma

Bu öğretici, bir serinin birinci bölümüdür. Azure CLı kullanarak Microsoft Azure Red Hat OpenShift kümesi oluşturmayı öğrenirsiniz, ölçeklendirerek kaynakları temizleyebilirsiniz.

Serinin birinci bölümünde şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
> * Azure Red Hat OpenShift kümesi oluşturun

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * Azure Red Hat OpenShift kümesi oluşturun
> * [Azure Red Hat OpenShift kümesini ölçeklendirme](tutorial-scale-cluster.md)
> * [Azure Red Hat OpenShift kümesini silme](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Ön koşullar

> [!IMPORTANT]
> Bu öğretici, Azure CLı 'nin sürüm 2.0.65 gerektirir.

Bu öğreticiye başlamadan önce:

[Geliştirme ortamınızı ayarladığınızdan](howto-setup-environment.md)emin olun ve şunları içerir:
- En son CLı 'yi yükleme (sürüm 2.0.65 veya üzeri)
- Henüz bir tane yoksa kiracı oluşturma
- Henüz yoksa bir Azure Uygulama nesnesi oluşturma
- Güvenlik grubu oluşturma
- Kümede oturum açmak için Active Directory Kullanıcı oluşturma.

## <a name="step-1-sign-in-to-azure"></a>1\. Adım: Azure 'da oturum açma

Azure CLı 'yi yerel olarak çalıştırıyorsanız, bir bash komut kabuğu açın ve Azure 'da oturum açmak için `az login` çalıştırın.

```bash
az login
```

 Birden çok aboneliğe erişiminiz varsa, `{subscription ID}` yerine kullanmak istediğiniz abonelikle değiştirin `az account set -s {subscription ID}` çalıştırın.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>2\. Adım: Azure Red Hat OpenShift kümesi oluşturma

Bash komut penceresinde aşağıdaki değişkenleri ayarlayın:

> [!IMPORTANT]
> Kümeniz için benzersiz olan bir ad seçin ve tüm küçük harfli veya küme oluşturma işlemi başarısız olur.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Kümenizi oluşturmak için bir konum seçin. Azure üzerinde OpenShift 'i destekleyen Azure bölgelerinin listesi için bkz. [Desteklenen bölgeler](supported-resources.md#azure-regions). Örneğin: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

[Azure AD uygulama kaydı oluşturma](howto-aad-app-configuration.md#create-an-azure-ad-app-registration)' nın 5. adımında kaydettiğiniz değere `APPID` ayarlayın.  

```bash
APPID=<app ID value>
```

' GROUPıD ' değerini, [Azure AD güvenlik grubu oluşturma](howto-aad-app-configuration.md#create-an-azure-ad-security-group)'nın 10. adımında kaydettiğiniz değere ayarlayın.

```bash
GROUPID=<group ID value>
```

`SECRET`, [bir istemci gizli anahtarı oluşturun](howto-aad-app-configuration.md#create-a-client-secret). Adım 8 ' de kaydettiğiniz değere ayarlayın.  

```bash
SECRET=<secret value>
```

[Yeni bir kiracı oluşturmak](howto-create-tenant.md#create-a-new-azure-ad-tenant) için 7. adımda KAYDETTIĞINIZ Kiracı kimliği değerine `TENANT` ayarlayın  

```bash
TENANT=<tenant ID>
```

Küme için kaynak grubu oluşturun. Yukarıdaki değişkenleri tanımlamak için kullandığınız Bash kabuğundan aşağıdaki komutu çalıştırın:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>İsteğe bağlı: kümenin sanal ağını var olan bir sanal ağa bağlama

Oluşturduğunuz kümenin sanal ağını (VNET) eşleme yoluyla mevcut bir VNET 'e bağlamanız gerekmiyorsa bu adımı atlayın.

Bu abonelikte, varsayılan aboneliğin dışındaki bir ağla eşleme yaptıysanız, Microsoft. ContainerService sağlayıcısını da kaydetmeniz gerekir. Bunu yapmak için bu abonelikte aşağıdaki komutu çalıştırın. Aksi takdirde, eşleymekte olduğunuz VNET aynı abonelikte bulunuyorsa, kayıt adımını atlayabilirsiniz. 

`az provider register -n Microsoft.ContainerService --wait`

İlk olarak, var olan VNET 'in tanımlayıcısını alın. Tanımlayıcı şu biçimde olacaktır: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Ağ adını veya var olan VNET 'in ait olduğu kaynak grubunu bilmiyorsanız, [sanal ağlar dikey penceresine](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) gidin ve sanal ağınıza tıklayın. Sanal ağ sayfası görünür ve ağın adını ve ait olduğu kaynak grubunu listeler.

BASH kabuğu 'nda aşağıdaki CLı komutunu kullanarak bir VNET_ID değişkeni tanımlayın:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Örneğin, `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Kümeyi oluşturma

Artık bir küme oluşturmaya hazırsınız. Aşağıdaki küme, belirtilen Azure AD kiracısında kümesi oluşturacak, bir güvenlik sorumlusu olarak kullanılacak Azure AD uygulama nesnesini ve parolayı ve kümeye yönetici erişimi olan üyeleri içeren güvenlik grubunu belirtir.

> [!IMPORTANT]
> Kümeyi oluşturmadan önce [burada ayrıntılı](howto-aad-app-configuration.md#add-api-permissions) olarak açıklandığı gıbı Azure AD uygulaması için uygun izinleri doğru şekilde eklediğinizden emin olun

Kümenizi bir sanal ağ **ile eşlemeden, aşağıdaki** komutu kullanın:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Kümenizi bir sanal ağ **ile eşediyorsanız** , `--vnet-peer` bayrağını ekleyen aşağıdaki komutu kullanın:
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> Ana bilgisayar adının kullanılamadığı bir hata alırsanız, bunun nedeni Küme adınızın benzersiz olmaması olabilir. Yeni bir Kullanıcı ve güvenlik grubu oluşturma adımını atlayarak [Yeni bir uygulama kaydı oluştur](howto-aad-app-configuration.md#create-an-azure-ad-app-registration)' da, özgün uygulama kaydınızı silmeyi ve farklı bir küme adıyla adımları yeniden yapmayı deneyin.

Birkaç dakika sonra `az openshift create` tamamlanır.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Kümeniz için oturum açma URL 'sini alın

Aşağıdaki komutu çalıştırarak kümenizde oturum açmak için URL 'YI alın:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Çıktıda `publicHostName` arayın, örneğin: `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

Kümeniz için oturum açma URL 'SI `https://` ve ardından `publicHostName` değer olacaktır.  Örneğin: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Bu URI 'yi, uygulama kaydı yeniden yönlendirme URI 'sinin bir parçası olarak bir sonraki adımda kullanacaksınız.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>3\. Adım: uygulama kaydı yeniden yönlendirme URI 'nizi güncelleştirme

Artık küme için oturum açma URL 'sine sahip olduğunuza göre, uygulama kaydı yeniden yönlendirme Kullanıcı arabirimini ayarlayın:

1. [Uygulama kayıtları dikey penceresini](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)açın.
2. Uygulama kayıt nesneniz ' na tıklayın.
3. **Yeniden yönlendirme URI 'Si Ekle**' ye tıklayın.
4. **Türün** **Web** olduğundan emin olun ve aşağıdaki KALıBı kullanarak **yeniden yönlendirme URI** 'sini ayarlayın: `https://<public host name>/oauth2callback/Azure%20AD`. Örneğin, `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. **Kaydet**’e tıklayın

## <a name="step-4-sign-in-to-the-openshift-console"></a>4\. Adım: OpenShift konsolunda oturum açma

Artık yeni kümeniz için OpenShift konsoluna oturum açmaya hazırsınız. [OpenShift Web Konsolu](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) , OpenShift projelerinizin içeriğini görselleştirmenize, gözatmanıza ve yönetmenize olanak sağlar.

Azure portal oturum açmak için normalde kullandığınız kimliği önbelleğe alınmamış yeni bir tarayıcı örneği gerekir.

1. Bir *ınbilito* pencere (Chrome) veya *InPrivate* pencere (Microsoft Edge) açın.
2. Yukarıda elde ettiğiniz oturum açma URL 'sine gidin, örneğin: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Adım 3 ' te [Yeni bir Azure Active Directory Kullanıcı oluşturma](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user)bölümünde oluşturduğunuz Kullanıcı adını kullanarak oturum açın.

**Istenen izinler** iletişim kutusu görüntülenir. **Kuruluşunuz adına izin** ' a ve ardından **kabul et**' e tıklayın.

Artık küme konsolu 'nda oturum açtınız.

![OpenShift küme konsolunun ekran görüntüsü](./media/aro-console.png)

 [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) belgelerindeki görüntüleri oluşturmak ve oluşturmak Için [OpenShift konsolunu kullanma](https://docs.openshift.com/aro/getting_started/developers_console.html) hakkında daha fazla bilgi edinin.

## <a name="step-5-install-the-openshift-cli"></a>5\. Adım: OpenShift CLı 'yı yüklemeyi

[OPENSHIFT CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (veya *OC araçları*), openshıft kümenizin çeşitli bileşenleriyle etkileşim kurmak için uygulamalarınızı ve alt düzey yardımcı programlarını yönetmeye yönelik komutlar sağlar.

OpenShift konsolunda, oturum açma adınızın sağ üst köşesinde bulunan soru işaretine tıklayın ve **komut satırı araçları**' nı seçin.  Linux, MacOS veya Windows için desteklenen OC CLı 'yi indirmek ve yüklemek üzere **en son sürüm** bağlantısını izleyin.

> [!NOTE]
> Sağ üst köşede soru işareti simgesini görmüyorsanız sol üst taraftaki açılan listeden *Hizmet kataloğu* veya *uygulama konsolu* ' nu seçin.
>
> Alternatif olarak, [OC CLI](https://www.okd.io/download.html) 'yı doğrudan indirebilirsiniz.

**Komut satırı araçları** sayfası `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`formun bir komutunu sağlar.  Bu komutu kopyalamak için *Panoya Kopyala* düğmesine tıklayın.  Bir Terminal penceresinde, OC araçları yerel yüklemenizi içerecek şekilde [yolunu ayarlayın](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) . Ardından, kopyaladığınız OC CLı komutunu kullanarak kümede oturum açın.

Yukarıdaki adımları kullanarak belirteç değerini alamazsanız, belirteç değeri: `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`alın.

## <a name="next-steps"></a>Sonraki adımlar

Öğreticinin bu bölümünde, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure Red Hat OpenShift kümesi oluşturun

Sonraki öğreticiye ilerleyin:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift kümesini ölçeklendirme](tutorial-scale-cluster.md)
