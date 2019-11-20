---
title: Application Gateway ile LetsEncrypt.org sertifikalarını kullanma
description: Bu makalede, LetsEncrypt.org adresinden sertifika alma ve AKS kümeleri için Application Gateway kullanma hakkında bilgi verilmektedir.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 92e9747865f1a0910c8bae4001cc597ae9ea3da6
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73957979"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>AKS kümeleri için Application Gateway on LetsEncrypt.org ile Sertifikalar kullanma

Bu bölüm, [LetsEncrypt.org](https://letsencrypt.org/) 'ten yararlanmak ve etki alanınız için otomatik olarak bir TLS/SSL sertifikası almak üzere aks 'leri yapılandırır. Sertifika, AKS kümeniz için SSL/TLS sonlandırmasını gerçekleştirecek Application Gateway yüklenir. Burada açıklanan kurulum, sertifikaların oluşturulmasını ve yönetilmesini otomatikleştiren, [CERT-Manager](https://github.com/jetstack/cert-manager) Kubernetes eklentisini kullanır.

Mevcut AKS kümenize [CERT-Manager](https://docs.cert-manager.io) yüklemek için aşağıdaki adımları izleyin.

1. Helb grafiği

    `cert-manager` Held grafiğini yüklemek için aşağıdaki betiği çalıştırın. Şunları yapmak gerekir:

    - AKS 'unuzda yeni bir `cert-manager` ad alanı oluşturun
    - Şu CRD 'leri oluşturun: sertifika, sınama, Kümeverenin, veren, sipariş
    - CERT-Manager grafiğini ( [docs.CERT-Manager.io)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps) yükler

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. Clusterıssuer kaynağı

    `ClusterIssuer` kaynağı oluşturun. İmzalı sertifikaların elde edildiği `Lets Encrypt` sertifika yetkilisini temsil etmesi için `cert-manager` gereklidir.

    Sertifika Yöneticisi, gösterilemez olmayan `ClusterIssuer` kaynağını kullanarak birden çok ad alanından tüketilen sertifikalar verecek. `Let’s Encrypt`, belirli bir etki alanı adını kontrol ettiğini ve size bir sertifika verildiğinizi doğrulamak için ACME protokolünü kullanır. `ClusterIssuer` [özelliklerini yapılandırma](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html)hakkında daha fazla bilgi. `ClusterIssuer`, test için kullanılan `Lets Encrypt` hazırlama ortamını (tarayıcı/istemci güven depolarında bulunmayan kök sertifika) kullanarak sertifika vermesini `cert-manager` yönlendirir.

    Aşağıdaki YAML 'deki varsayılan sınama türü `http01`. Diğer sorunlar [letsencrypt.org-Challenge türlerinde](https://letsencrypt.org/docs/challenge-types/) belgelenmiştir

    > [!IMPORTANT] 
    > Aşağıdaki YAML 'de `<YOUR.EMAIL@ADDRESS>` güncelleştirin

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. Uygulama dağıtma

    Application Gateway kullanarak `guestbook` uygulamayı şifreli sertifikaya Izin verir olarak göstermek için bir giriş kaynağı oluşturun.

    Application Gateway bir DNS adına sahip genel ön uç IP yapılandırmasına sahip olduğunuzdan emin olun (varsayılan `azure.com` etki alanını kullanarak veya bir `Azure DNS Zone` hizmeti sağlayın ve kendi özel etki alanınızı atayın).
    Ek açıklamanın `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`not edin. Bu, CERT Manager 'ın etiketli giriş kaynağını işlemesini söyler.

    > [!IMPORTANT] 
    > Aşağıdaki YAML 'deki `<PLACEHOLDERS.COM>`, kendi etki alanınız (veya Application Gateway bir, örneğin ' kh-aks-ingress.westeurope.cloudapp.azure.com ') ile güncelleştirin

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    Birkaç saniye sonra, otomatik olarak verilen **hazırlama** `Lets Encrypt` sertifikasını kullanarak Application Gateway https URL 'si aracılığıyla `guestbook` hizmetine erişebilirsiniz.
    Tarayıcınız size geçersiz bir sertifika yetkilisi tarafından uyarı verebilir. Hazırlama sertifikası `CN=Fake LE Intermediate X1`tarafından verilir. Bu, sistemin beklendiği gibi çalıştığı ve üretim sertifikanız için size hazırlandığının göstergesidir.

4. Üretim sertifikası

    Hazırlama sertifikanız başarıyla kurulduktan sonra bir üretim ACME sunucusuna geçiş yapabilirsiniz:
    1. Giriş kaynağınızın hazırlama ek açıklamasını şu ile değiştirin: `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Önceki adımda oluşturduğunuz mevcut hazırlama `ClusterIssuer` silin ve ACME sunucusunu yukarıdaki Clusterıssuer YAML 'den, `https://acme-v02.api.letsencrypt.org/directory` ile değiştirerek yeni bir tane oluşturun

5. Sertifika süre sonu ve yenileme

    `Lets Encrypt` sertifikasının süresi dolmadan önce, `cert-manager` Kubernetes gizli deposundaki sertifikayı otomatik olarak güncelleştirir. Bu noktada, Application Gateway giriş denetleyicisi, Application Gateway yapılandırmak için kullandığı giriş kaynaklarında başvurulan güncelleştirilmiş gizliliği uygular.
