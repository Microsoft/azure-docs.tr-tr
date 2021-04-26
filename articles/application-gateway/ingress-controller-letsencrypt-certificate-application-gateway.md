---
title: Application Gateway ile LetsEncrypt.org sertifikalarını kullanma
description: Bu makalede, LetsEncrypt.org adresinden sertifika alma ve AKS kümeleri için Application Gateway kullanma hakkında bilgi verilmektedir.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 638b8f24f8cf72f5c6a594a3c5a6eaacf469df8f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056683"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>AKS kümeleri için Application Gateway on LetsEncrypt.org ile Sertifikalar kullanma

Bu bölüm, [LetsEncrypt.org](https://letsencrypt.org/) 'ten yararlanmak ve etki alanınız için otomatik olarak bir TLS/SSL sertifikası almak üzere aks 'leri yapılandırır. Sertifika, AKS kümeniz için SSL/TLS sonlandırmasını gerçekleştirecek Application Gateway yüklenir. Burada açıklanan kurulum, sertifikaların oluşturulmasını ve yönetilmesini otomatikleştiren, [CERT-Manager](https://github.com/jetstack/cert-manager) Kubernetes eklentisini kullanır.

Mevcut AKS kümenize [CERT-Manager](https://docs.cert-manager.io) yüklemek için aşağıdaki adımları izleyin.

1. Helb grafiği

    Held grafiğini yüklemek için aşağıdaki betiği çalıştırın `cert-manager` . Şunları yapmak gerekir:

    - `cert-manager`AKS 'unuzda yeni bir ad alanı oluşturun
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
    # Helm v3+
    helm install \
      cert-manager jetstack/cert-manager \
      --namespace cert-manager \
      --version v1.0.4 \
      # --set installCRDs=true

    # Helm v2
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v1.0.4 \
      jetstack/cert-manager \
      # --set installCRDs=true
      
    #To automatically install and manage the CRDs as part of your Helm release, 
    #   you must add the --set installCRDs=true flag to your Helm installation command.
    ```

2. Clusterıssuer kaynağı

    Bir `ClusterIssuer` kaynak oluşturun. `cert-manager` `Lets Encrypt` İmzalı sertifikaların elde edildiği sertifika yetkilisini göstermek için bu gereklidir.

    `ClusterIssuer`Sertifika Yöneticisi, gösterilemez olmayan kaynağı kullanarak birden çok ad alanından tüketilen sertifikalar verecek. `Let’s Encrypt` , belirli bir etki alanı adını kontrol ettiğini ve size bir sertifika verildiğinizi doğrulamak için ACME protokolünü kullanır. Burada özellikleri yapılandırma hakkında daha fazla bilgi `ClusterIssuer` . [](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html) `ClusterIssuer``cert-manager`, `Lets Encrypt` sınama için kullanılan hazırlama ortamını (tarayıcı/istemci güven depolarında bulunmayan kök sertifika) kullanarak sertifika vermesini yönlendirir.

    Aşağıdaki YAML 'de varsayılan sınama türü ' dir `http01` . Diğer sorunlar [letsencrypt.org-Challenge türlerinde](https://letsencrypt.org/docs/challenge-types/) belgelenmiştir

    > [!IMPORTANT] 
    > `<YOUR.EMAIL@ADDRESS>`Aşağıdaki YAML 'de Güncelleştir

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

    `guestbook`Application Gateway kullanarak uygulamayı şifrelemeyi sağlayan bir giriş kaynağı oluşturun.

    Application Gateway bir DNS adına sahip genel ön uç IP yapılandırmasına sahip olduğunuzdan emin olun (varsayılan `azure.com` etki alanını kullanarak veya bir hizmet sağlayın `Azure DNS Zone` ve kendi özel etki alanınızı atayın).
    `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`, CERT Manager 'ın etiketli giriş kaynağını işlemesini söyleyen ek açıklamayı not edin.

    > [!IMPORTANT] 
    > `<PLACEHOLDERS.COM>`Aşağıdaki YAML 'de kendi etki alanınızı (veya Application Gateway bir, örneğin ' KH-aks-ingress.westeurope.cloudapp.Azure.com ') güncelleştirin

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

    Birkaç saniye sonra, `guestbook` otomatik olarak verilen **hazırlama** SERTIFIKASıNı kullanarak Application Gateway https URL 'si aracılığıyla hizmete erişebilirsiniz `Lets Encrypt` .
    Tarayıcınız size geçersiz bir sertifika yetkilisi tarafından uyarı verebilir. Hazırlama sertifikası tarafından verilir `CN=Fake LE Intermediate X1` . Bu, sistemin beklendiği gibi çalıştığı ve üretim sertifikanız için size hazırlandığının göstergesidir.

4. Üretim sertifikası

    Hazırlama sertifikanız başarıyla kurulduktan sonra bir üretim ACME sunucusuna geçiş yapabilirsiniz:
    1. Giriş kaynağınızın hazırlama ek açıklamasını ile değiştirin: `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Önceki adımda oluşturduğunuz var olan hazırlama ' yı silin `ClusterIssuer` ve yukarıdaki ' i sunucu ile yukarıdaki Clusterıssuer YAML 'den değiştirerek yeni bir tane oluşturun. `https://acme-v02.api.letsencrypt.org/directory`

5. Sertifika süre sonu ve yenileme

    `Lets Encrypt`Sertifikanın süresi dolmadan önce, `cert-manager` Kubernetes gizli deposundaki sertifikayı otomatik olarak güncelleştirir. Bu noktada, Application Gateway giriş denetleyicisi, Application Gateway yapılandırmak için kullandığı giriş kaynaklarında başvurulan güncelleştirilmiş gizliliği uygular.
