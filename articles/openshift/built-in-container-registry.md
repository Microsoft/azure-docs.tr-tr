---
title: Azure Red Hat OpenShift 4 için yerleşik kapsayıcı kayıt defterini yapılandırma
description: Azure Red Hat OpenShift 4 için yerleşik kapsayıcı kayıt defterini yapılandırma
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: dda050b8d824f0ff0ac1c84d2f008387de55aedf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100636398"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 4 için yerleşik kapsayıcı kayıt defterini yapılandırma

Azure Red Hat OpenShift, isteğe bağlı olarak yeni görüntü depoları sağlama özelliğini ekleyen [OpenShift Container Registry (OCR)](https://docs.openshift.com/container-platform/4.6/registry/architecture-component-imageregistry.html) adlı tümleşik bir kapsayıcı görüntüsü kayıt defteri sağlar. Bu, kullanıcılara, uygulama yapılarına elde edilen görüntüleri göndermek için yerleşik bir konum sağlar.

Bu makalede, Azure Red Hat OpenShift (ARO) 4 kümesi için yerleşik kapsayıcı görüntüsü kayıt defterini yapılandıracaksınız. Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Azure AD kurulumu
> * OpenID Connect 'i ayarlama
> * Yerleşik kapsayıcı görüntüsü kayıt defterine erişin

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir ARO kümeniz olduğu varsayılır. Bir ARO kümesine ihtiyacınız varsa, bkz. ARO öğreticisi, [Azure Red Hat OpenShift 4 kümesi oluşturma](./tutorial-create-cluster.md). Bağımsız değişkeniyle küme oluşturmayı unutmayın `--pull-secret` `az aro create` .  Bu, Azure Active Directory kimlik doğrulaması ve yerleşik kapsayıcı kayıt defteri yapılandırmak için gereklidir.

Kümeniz olduktan sonra, [Azure Red Hat OpenShift 4 kümesine bağlanma](./tutorial-connect-cluster.md)bölümündeki adımları izleyerek kümeye bağlanın.
   * Bu makalenin ilerleyen kısımlarında komutunu kullanacağımız için "OpenShift CLı 'yı Install" bölümündeki adımları izlediğinizden emin olun `oc` .
   * Gibi görünen küme konsolu URL 'sini unutmayın `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . `<random>`Ve değerleri `<region>` Bu makalede daha sonra kullanılacaktır.
   * `kubeadmin`Kimlik bilgilerini aklınızda edin. Bunlar, bu makalenin ilerleyen kısımlarında de kullanılacaktır.

### <a name="configure-azure-active-directory-authentication"></a>Azure Active Directory kimlik doğrulamasını yapılandırma 

Azure Active Directory (Azure AD) OpenID Connect (OıDC) uygular. OıDC, ARO kümesinde oturum açmak için Azure AD 'yi kullanmanıza olanak sağlar. Kümenizi ayarlamak için [Azure Active Directory kimlik doğrulaması yapılandırma](configure-azure-ad-cli.md) bölümündeki adımları izleyin.

## <a name="access-the-built-in-container-image-registry"></a>Yerleşik kapsayıcı görüntüsü kayıt defterine erişin

Şimdi, ARO kümesine kimlik doğrulama yöntemlerini ayarladığınıza göre, yerleşik kayıt defterine erişimi etkinleştirelim.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Azure AD kullanıcısını yönetici olacak şekilde tanımlama

1. Azure AD kullanıcısının kimlik bilgilerini kullanarak, tarayıcınızdan OpenShift Web konsolunda oturum açın. Yönetici tanımlamak için OpenID kullanmak üzere Azure Active Directory karşı OpenShift OpenID kimlik doğrulamasından faydalanır.

   1. Konsolda oturum açmak için InPrivate, ınbilito veya diğer eşdeğer tarayıcı penceresi özelliğini kullanın. OıDC etkinleştirildikten sonra pencere farklı görünecektir.
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="OpenID Connect etkinleştirilmiş oturum açma penceresi.":::
   1. **OpenID** seçin

   > [!NOTE]
   > Burada oturum açmak için kullandığınız kullanıcı adını ve parolayı bir yere göz atın. Bu Kullanıcı adı ve parola, bu ve diğer makalelerdeki diğer eylemler için yönetici olarak çalışır.
2. Aşağıdaki adımları kullanarak OpenShift CLı ile oturum açın.  Tartışma için bu işlem olarak bilinir `oc login` .
   1. Web konsolunun sağ üst kısmında, oturum açmış kullanıcının bağlam menüsünü genişletin ve ardından **login komutunu Kopyala**' yı seçin.
   2. Gerekirse, aynı kullanıcıyla yeni bir sekme penceresinde oturum açın.
   3. **Görüntüleme belirtecini** seçin.
   4. Aşağıda gösterildiği gibi, aşağıda belirtilen değeri **Bu belirteçle** panoya kopyalayın ve bir kabukta çalıştırın.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. `oc whoami`Konsolunda çalıştırın ve çıktıyı farklı şekilde aklınızda yapın **\<aad-user>** .  Bu değeri makalenin ilerleyen kısımlarında kullanacağız.
4. OpenShift web konsolunun oturumunu kapatın. Tarayıcı penceresinin sağ üst köşesindeki düğmeyi seçin **\<aad-user>** ve ardından **Oturumu Kapat**' ı seçin.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Azure AD kullanıcısına kayıt defteri etkileşimi için gerekli rolleri verme

1. Kimlik bilgilerini kullanarak tarayıcınızdan OpenShift Web konsolunda oturum açın `kubeadmin` .
1. Yukarıdaki adımları izleyerek OpenShift CLı 'de belirteci ile oturum açın `kubeadmin` `oc login` , ancak ile Web konsolunda oturum açtıktan sonra bunu yapın `kubeadmin` .
1. **AAD-User** için yerleşik kayıt defterine erişimi etkinleştirmek üzere aşağıdaki komutları yürütün.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>Kapsayıcı kayıt defteri URL 'sini alma

`oc get route`Kapsayıcı kayıt defteri URL 'sini almak için, komutu aşağıda gösterildiği gibi kullanın.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > **CONTAINER REGISTRY URL**'sinin konsol çıkışını aklınızda edin. Bu kılavuzda ve sonraki bölümlerde tam kayıt defteri adı olarak kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

Yerleşik kapsayıcı görüntüsü kayıt defterini ayarladığınıza göre, OpenShift üzerinde bir uygulama dağıtarak çalışmaya başlayabilir. Java uygulamaları için, [Azure Red Hat OpenShift 4 kümesinde açık özgürlük/WebSphere özgürlük ile bir Java uygulaması dağıtın](howto-deploy-java-liberty-app.md).