---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1c2525b352c25f470814ce909a8d10ff821d9e32
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961612"
---
CA sertifikası oluşturun.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

CA sertifikasını Base64 biçiminde yazdır. Bu, Azure tarafından desteklenen biçimdir. Bu sertifikayı, [P2S yapılandırma adımlarının](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)bir parçası olarak Azure 'a yüklersiniz.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```

Kullanıcı sertifikası oluşturun.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```

Kullanıcı sertifikasını içeren bir P12 paketi oluşturun. Bu paket, istemci yapılandırma dosyalarıyla çalışırken sonraki adımlarda kullanılacaktır.

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```