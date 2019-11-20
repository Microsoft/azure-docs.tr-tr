---
title: Application Gateway kullanarak bir AKS hizmetini HTTP veya HTTPS üzerinden kullanıma sunma
description: Bu makalede, Application Gateway kullanarak bir AKS hizmetini HTTP veya HTTPS üzerinden kullanıma sunma hakkında bilgi verilmektedir.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795581"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Application Gateway kullanarak bir AKS hizmetini HTTP veya HTTPS üzerinden kullanıma sunma 

Bu öğreticiler, HTTP veya HTTPS üzerinden [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) aracılığıyla örnek bir Kubernetes hizmeti sunmak Için [Kubernetes giriş kaynaklarının](https://kubernetes.io/docs/concepts/services-networking/ingress/) kullanımını göstermeye yardımcı olur.

## <a name="prerequisites"></a>Ön koşullar

- `ingress-azure` Held grafiği yüklendi.
  - [**Doğa alanı dağıtımı**](ingress-controller-install-new.md): sıfırdan başlatıyorsanız, aks kümesine Application Gateway ve uygulama ağ geçidi denetleyicisi yükleme adımlarını özetleyen bu yükleme yönergelerine bakın.
  - [**Brownfield dağıtımı**](ingress-controller-install-existing.md): mevcut bir aks kümeniz ve Application Gateway varsa, aks kümesine uygulama ağ geçidi denetleyicisi yüklemek için bu yönergelere başvurun.
- Bu uygulamada HTTPS kullanmak istiyorsanız, bir x509 sertifikasına ve özel anahtarına ihtiyacınız olacaktır.

## <a name="deploy-guestbook-application"></a>`guestbook` uygulaması dağıtma

Konuk defteri uygulaması, bir Web UI ön ucu, arka uç ve Redsıs veritabanını oluşturan kurallı bir Kubernetes uygulamasıdır. `guestbook`, varsayılan olarak, `80`bağlantı noktası üzerinde `frontend` adına sahip bir hizmet üzerinden uygulamayı kullanıma sunar. Kubernetes giriş kaynağı olmadan, hizmet AKS kümesi dışından erişilebilir değildir. Uygulamaya HTTP ve HTTPS aracılığıyla erişmek için uygulama ve Kurulum kaynakları 'nı kullanacağız.

Konuk defteri uygulamasını dağıtmak için aşağıdaki yönergeleri izleyin.

1. `guestbook-all-in-one.yaml` [buradan](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml) indirebilirsiniz
1. Şunu çalıştırarak AKS kümenize `guestbook-all-in-one.yaml` dağıtın

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Artık `guestbook` uygulaması dağıtıldı.

## <a name="expose-services-over-http"></a>Hizmetleri HTTP üzerinden kullanıma sunma

Konuk defteri uygulamasını kullanıma sunmak için aşağıdaki giriş kaynağını kullanacağız:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Bu giriş, `guestbook-all-in-one` dağıtımının `frontend` hizmetini Application Gateway varsayılan arka ucu olarak kullanıma sunar.

Yukarıdaki giriş kaynağını `ing-guestbook.yaml`olarak kaydedin.

1. Şunu çalıştırarak `ing-guestbook.yaml` dağıt:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Dağıtım durumu için giriş denetleyicisinin günlüğünü kontrol edin.

Artık `guestbook` uygulama kullanılabilir olmalıdır. Application Gateway genel adresini ziyaret ederek bunu kontrol edebilirsiniz.

## <a name="expose-services-over-https"></a>Hizmetleri HTTPS üzerinden kullanıma sunma

### <a name="without-specified-hostname"></a>Belirtilen ana bilgisayar adı olmadan

Ana bilgisayar adı belirtmeden, Konuk defteri hizmeti uygulama ağ geçidine işaret eden tüm ana bilgisayar adlarında kullanılabilir olacaktır.

1. Giriş dağıtılmadan önce sertifikayı ve özel anahtarı barındırmak için bir Kubernetes parolası oluşturmanız gerekir. Şunu çalıştırarak bir Kubernetes gizli dizisi oluşturabilirsiniz

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Aşağıdaki girişi tanımlayın. Giriş bölümünde, `secretName` bölümünde gizli dizi adını belirtin.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Yukarıdaki giriş kaynağındaki `<guestbook-secret-name>` gizli anahtar adıyla değiştirin. Yukarıdaki giriş kaynağını `ing-guestbook-tls.yaml`bir dosya adında depolayın.

1. Çalıştırarak--Konuk-TLS. YAML 'yi dağıtın

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Dağıtım durumu için giriş denetleyicisinin günlüğünü kontrol edin.

`guestbook` uygulama artık hem HTTP hem de HTTPS üzerinde kullanılabilir olacaktır.

### <a name="with-specified-hostname"></a>Belirtilen ana bilgisayar adı ile

Ayrıca, TLS yapılandırmalarının ve hizmetlerinin çoğullanması için giriş sayfasında ana bilgisayar adını belirtebilirsiniz.
Ana bilgisayar adı belirtilerek, Konuk defteri hizmeti yalnızca belirtilen konakta kullanılabilir.

1. Aşağıdaki girişi tanımlayın.
    Giriş bölümünde, `secretName` bölümünde gizli dizi adını belirtin ve `hosts` bölümündeki ana bilgisayar adını uygun şekilde değiştirin.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Çalıştırarak `ing-guestbook-tls-sni.yaml` dağıtma

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Dağıtım durumu için giriş denetleyicisinin günlüğünü kontrol edin.

Artık `guestbook` uygulama yalnızca belirtilen konakta (Bu örnekte`<guestbook.contoso.com>`) hem HTTP hem de HTTPS üzerinden kullanılabilir.

## <a name="integrate-with-other-services"></a>Diğer hizmetlerle tümleştirme

Aşağıdaki giriş, bu giriş içine ek yollar eklemenize ve bu yolları diğer hizmetlere yönlendirmenize olanak sağlayacak:

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      rules:
      - http:
          paths:
          - path: </other/*>
            backend:
              serviceName: <other-service>
              servicePort: 80
          - backend:
              serviceName: frontend
              servicePort: 80
    ```
