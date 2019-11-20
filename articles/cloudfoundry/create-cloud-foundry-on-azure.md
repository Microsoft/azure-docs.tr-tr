---
title: Azure 'da bir özetleme Cloud Foundry kümesi oluşturma
description: Azure 'da bir özetleme Cloud Foundry (PCF) kümesi sağlamak için gereken parametreleri ayarlamayı öğrenin
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 5d4ac5435281f521c71556123f77d737ee6916e9
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161786"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Azure 'da bir özetleme Cloud Foundry kümesi oluşturma

Bu öğreticide, Azure 'da bir Özet Cloud Foundry (PCF) kümesi sağlamak için gereken parametreleri oluşturmak ve oluşturmak için hızlı adımlar sağlanmaktadır. Özet Cloud Foundry çözümünü bulmak için Azure [marketi](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)'nde bir arama gerçekleştirin.

![Azure 'da özette Cloud Foundry arama](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>SSH ortak anahtarı oluşturma

Windows, Mac veya Linux kullanarak genel bir güvenli kabuk (SSH) anahtarı oluşturmak için birkaç yol vardır.

```Bash
ssh-keygen -t rsa -b 2048
```

Daha fazla bilgi için bkz. [Azure 'Da Windows Ile SSH anahtarlarını kullanma](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

## <a name="create-a-service-principal"></a>Hizmet sorumlusu oluşturma

> [!NOTE]
>
> Hizmet sorumlusu oluşturmak için, sahip hesabı izninizin olması gerekir. Ayrıca hizmet sorumlusu oluşturmayı otomatikleştirmek için bir betik yazabilirsiniz. Örneğin, Azure CLı [az ad SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest)' i kullanabilirsiniz.

1. Azure hesabınızda oturum açın.

    `az login`

    ![Azure CLı oturum açma](media/deploy/az-login-output.png )
 
    **ABONELIK kimliğiniz**olarak "ID" değerini kopyalayın ve "tenantıd" değerini daha sonra kullanmak üzere kopyalayın.

2. Bu yapılandırma için varsayılan aboneliğinizi ayarlayın.

    `az account set -s {id}`

3. PCF 'niz için bir Azure Active Directory uygulaması oluşturun. Benzersiz bir alfasayısal parola belirtin. Daha sonra kullanmak için parolayı **ClientSecret** olarak depolayın.

    `az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Çıkışta "AppID" değerini, daha sonra kullanmak üzere **ClientID** 'niz olarak kopyalayın.

    > [!NOTE]
    >
    > Kendi uygulama giriş sayfanızı ve tanımlayıcı URI 'nizi seçin; örneğin, http\:/www\.contoso.com.

4. Yeni uygulama KIMLIĞINIZLE bir hizmet sorumlusu oluşturun.

    `az ad sp create --id {appId}`

5. Hizmet sorumlunuz için izin rolünü katkıda bulunan olarak ayarlayın.

    `az role assignment create --assignee "{enter-your-homepage}" --role "Contributor"`

    Ya da kullanabilirsiniz

    `az role assignment create --assignee {service-principal-name} --role "Contributor"`

    ![Hizmet sorumlusu rol ataması](media/deploy/svc-princ.png )

6. Uygulama KIMLIĞI, parola ve kiracı KIMLIĞINI kullanarak hizmet sorumlusunda başarıyla oturum açabildiğinizi doğrulayın.

    `az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}`

7. Aşağıdaki biçimde bir. JSON dosyası oluşturun. Daha önce kopyaladığınız **ABONELIK kimliği**, **tenantıd**, **ClientID**ve **ClientSecret** değerlerini kullanın. Dosyayı kaydedin.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Özetleme ağ belirtecini al

1. Özet [ağ](https://network.pivotal.io) hesabınızda kaydolun veya oturum açın.
2. Sayfanın sağ üst köşesinde profil adınızı seçin. **Profili Düzenle**' yi seçin.
3. Sayfanın alt kısmına ilerleyin ve **eskı API belirteci** değerini kopyalayın. Bu değer, daha sonra kullandığınız **özetleme ağ belirteci** değeridir.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Azure 'da Cloud Foundry kümenizi sağlama

Artık [Azure 'da özet Cloud Foundry kümenizi](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)sağlamak için ihtiyacınız olan tüm parametrelere sahipsiniz.
Parametreleri girin ve PCF kümenizi oluşturun.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Dağıtımı doğrulayın ve özetleme Ops Manager 'da oturum açın

1. PCF kümeniz bir dağıtım durumu gösterir.

    ![Azure dağıtım durumu](media/deploy/deployment.png )

2. PCF Ops yöneticinizin kimlik bilgilerini almak için sol taraftaki gezinmede yer alan **dağıtımlar** bağlantısını seçin. Sonraki sayfada **dağıtım adını** seçin.
3. Soldaki gezinmede, PCF Ops Manager için URL, Kullanıcı adı ve parolayı göstermek üzere **çıktılar** bağlantısını seçin. "OPSMAN-FQDN" değeri URL 'dir.
 
    ![Cloud Foundry dağıtım çıkışı](media/deploy/deploy-outputs.png )
 
4. URL 'YI bir Web tarayıcısında başlatın. Oturum açmak için önceki adımdan kimlik bilgilerini girin.

    ![Özette oturum açma sayfası](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Internet Explorer tarayıcısı bir "site güvenli değil" uyarı iletisi nedeniyle başarısız olursa, **daha fazla bilgi** ' yi seçin ve Web sayfasına gidin. Firefox için **İleri** ' yi seçin ve devam etmek için sertifikayı ekleyin.

5. PCF Ops yöneticiniz dağıtılan Azure örneklerini görüntüler. Artık uygulamalarınızı dağıtıp yönetebilirsiniz.
               
    ![Azure örneği özette dağıtıldı](media/deploy/ops-mgr.png )
 
