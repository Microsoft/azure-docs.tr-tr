---
title: WebSocket sunucusunu Application Gateway kullanıma sunma
description: Bu makalede, AKS kümeleri için giriş denetleyicisi ile Application Gateway bir WebSocket sunucusunu kullanıma sunma hakkında bilgi verilmektedir.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 01fde82e69917f59f6519524c4c8828feb84a4f9
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795959"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>WebSocket sunucusunu Application Gateway kullanıma sunma

Application Gateway v2 belgelerinde açıklandığı gibi [, WebSocket ve http/2 protokolleri için yerel destek sağlar](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic). Hem Application Gateway hem de Kubernetes girişi için, WebSocket desteğini seçmeli olarak etkinleştirmek veya devre dışı bırakmak için Kullanıcı tarafından yapılandırılabilir bir ayar olmadığına lütfen unutmayın.

Aşağıdaki Kubernetes dağıtımı YAML, bir WebSocket sunucusunu dağıtmak için kullanılan en düşük yapılandırmayı gösterir ve bu, normal bir Web sunucusunu dağıtmaya benzer:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

Tüm önkoşulların yerine getirildiğini ve AKS 'larınızın bir Kubernetes girişi tarafından denetlenen bir Application Gateway varsa yukarıdaki dağıtım, Application Gateway genel IP 'nizin 80 numaralı bağlantı noktasında kullanıma sunulan bir WebSockets sunucusu ve `ws.contoso.com` alanını.

Aşağıdaki kıvrımlı komutu WebSocket sunucu dağıtımını test edecektir:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket sistem durumu araştırmaları

Dağıtımınız açıkça sistem durumu araştırmalarını tanımlamıyorsa, Application Gateway WebSocket sunucu uç noktanıza HTTP GET denemesi yapılır.
Sunucu uygulamasına bağlı olarak ([bir çok sevdiğimiz](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)) WebSocket 'e özgü üstbilgiler gerekli olabilir (örneğin`Sec-Websocket-Version`).
Application Gateway WebSocket üst bilgilerini eklemediğinden, Application Gateway 'ın WebSocket sunucusundan gelen sistem durumu araştırma yanıtı `400 Bad Request`büyük olasılıkla çok büyük olur.
Sonuç olarak Application Gateway, ayırımlarınızı sağlıksız olarak işaretleyecek ve sonuç olarak WebSocket sunucusunun tüketicileri için bir `502 Bad Gateway` neden olacak.
Bundan kaçınmak için, sunucunuza bir sistem durumu denetimi için bir HTTP GET işleyicisi (örneğin, `200 OK`döndüren`/health`) eklemeniz gerekebilir.
