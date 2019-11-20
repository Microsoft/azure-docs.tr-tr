---
title: Azure dağıtım sonrası görevlerinde OpenShift kapsayıcı platformu 3,11
description: OpenShift kapsayıcı platformu 3,11 kümesi dağıtıldıktan sonra için ek görevler.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: d3008e2e2d720b67fcf0846c27d2fed1ef7db307
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035502"
---
# <a name="post-deployment-tasks"></a>Dağıtım sonrası görevler

Bir OpenShift kümesi dağıttıktan sonra ek öğeleri yapılandırabilirsiniz. Bu makalede ele alınmıştır:

- Azure Active Directory kullanarak çoklu oturum açmayı yapılandırma (Azure AD)
- OpenShift 'i izlemek için Azure Izleyici günlüklerini yapılandırma
- Ölçümleri ve günlüğü yapılandırma
- Azure için açık Hizmet Aracısı nasıl yüklenir (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Azure Active Directory kullanarak çoklu oturum açmayı yapılandırma

Kimlik doğrulaması için Azure Active Directory kullanmak için önce bir Azure AD uygulama kaydı oluşturmanız gerekir. Bu işlem iki adımdan oluşur: uygulama kaydını oluşturma ve izinleri yapılandırma.

### <a name="create-an-app-registration"></a>Uygulama kaydı oluşturma

Bu adımlar, uygulama kaydını oluşturmak için Azure CLı ve izinleri ayarlamak için GUI (portal) kullanır. Uygulama kaydını oluşturmak için aşağıdaki beş bilgi parçasına ihtiyacınız vardır:

- Görünen ad: uygulama kaydı adı (örneğin, OCPAzureAD)
- Giriş sayfası: OpenShift konsol URL 'SI (örneğin, https://masterdns343khhde.westus.cloudapp.azure.com/console)
- Tanımlayıcı URI: OpenShift konsol URL 'SI (örneğin, https://masterdns343khhde.westus.cloudapp.azure.com/console)
- Yanıt URL 'SI: Ana genel URL ve uygulama kaydı adı (örneğin, https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD)
- Parola: güvenli parola (güçlü bir parola kullanın)

Aşağıdaki örnek, önceki bilgileri kullanarak bir uygulama kaydı oluşturur:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Komut başarılı olursa şuna benzer bir JSON çıkışı alırsınız:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Sonraki bir adım için komuttan döndürülen AppID özelliğini bir yere göz atın.

Azure portalında:

1. **Azure Active Directory** > **uygulama kaydı**' nı seçin.
2. Uygulama kaydınızı arayın (örneğin, OCPAzureAD).
3. Sonuçlarda uygulama kaydı ' na tıklayın.
4. **Ayarlar**altında **gerekli izinler**' i seçin.
5. **Gerekli izinler**altında **Ekle**' yi seçin.

   ![Uygulama kaydı](media/openshift-post-deployment/app-registration.png)

6. 1\. Adım: API 'yi seçin ve ardından **Windows Azure Active Directory (Microsoft. Azure. ActiveDirectory)** öğesine tıklayın. En altta **Seç** ' e tıklayın.

   ![Uygulama kaydı API 'SI seçme](media/openshift-post-deployment/app-registration-select-api.png)

7. 2\. Adım: Izinleri seçin sayfasında oturum aç ' ı seçin ve **temsilci izinleri**altında **Kullanıcı profilini okuyun** ve ardından **Seç**' e tıklayın.

   ![Uygulama kaydı erişimi](media/openshift-post-deployment/app-registration-access.png)

8. **Done** (Bitti) öğesini seçin.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Azure AD kimlik doğrulaması için OpenShift yapılandırma

OpenShift 'i Azure AD 'yi bir kimlik doğrulama sağlayıcısı olarak kullanacak şekilde yapılandırmak için,/etc/Origin/Master/Master-config.exe adlı tüm ana düğümlerde düzenlenmelidir.

Aşağıdaki CLı komutunu kullanarak kiracı KIMLIĞINI bulun:

```azurecli
az account show
```

YAML dosyasında aşağıdaki satırları bulun:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Önceki satırlardan hemen sonra aşağıdaki satırları ekleyin:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Metnin IdentityProviders altında doğru şekilde hizalandığından emin olun. Aşağıdaki CLı komutunu kullanarak kiracı KIMLIĞINI bulun: ```az account show```

Openshıft ana hizmetlerini tüm ana düğümlerde yeniden başlatın:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

OpenShift konsolunda, kimlik doğrulaması için şu iki seçenek görürsünüz: htpasswd_auth ve [uygulama kaydı].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Azure Izleyici günlükleri ile OpenShift 'i izleme

Log Analytics aracısını OpenShift 'e eklemenin üç yolu vardır.
- Her OpenShift düğümüne Linux için Log Analytics aracısını doğrudan yüklemeyi
- Her OpenShift düğümünde Azure Izleyici VM uzantısını etkinleştirin
- Log Analytics aracısını OpenShift daemon olarak kurma

Daha fazla ayrıntı için tam [yönergeleri](https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift) okuyun.

## <a name="configure-metrics-and-logging"></a>Ölçümleri ve günlüğü yapılandırma

Dala bağlı olarak, OpenShift kapsayıcı platformu ve OKD için Azure Resource Manager şablonları, yükleme işleminin bir parçası olarak ölçümleri ve günlüğe kaydetmeyi etkinleştirmek için giriş parametreleri sağlayabilir.

OpenShift kapsayıcı platformu marketi teklifi, küme yüklemesi sırasında ölçümleri ve günlüğü etkinleştirmek için de bir seçenek sağlar.

Küme yüklemesi sırasında ölçümler/günlüğe kaydetme etkinleştirilmemişse, bu, aslında kolayca etkinleştirilebilir.

### <a name="azure-cloud-provider-in-use"></a>Azure bulut sağlayıcısı kullanımda

Dağıtım sırasında belirtilen kimlik bilgilerini kullanarak savunma düğümüne veya ilk ana düğüme (kullanılan şablona ve kullanılan dala göre) SSH. Aşağıdaki komutu verin:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Azure bulut sağlayıcısı kullanımda değil

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Azure için Open Hizmet Aracısı 'yi (OSBA) yükler

Azure için Hizmet Aracısı açın veya OSBA, doğrudan OpenShift 'ten Azure Cloud Services sağlamanıza olanak tanır. OSBA Azure için açık bir Hizmet Aracısı API uygulamasıdır. Açık Hizmet Aracısı API 'SI, bulut Yerel uygulamalarının, bulut hizmetlerini kilitleme olmadan yönetmek için kullanabileceği bulut sağlayıcıları için ortak bir dil tanımlayan bir belirtimdir.

OpenShift üzerinde OSBA yüklemek için burada bulunan yönergeleri izleyin: https://github.com/Azure/open-service-broker-azure#openshift-project-template. 
> [!NOTE]
> Tüm yükleme bölümünün değil, yalnızca OpenShift proje şablonu bölümündeki adımları uygulayın.

## <a name="next-steps"></a>Sonraki adımlar

- [OpenShift kapsayıcı platformu ile çalışmaya başlama](https://docs.openshift.com)
