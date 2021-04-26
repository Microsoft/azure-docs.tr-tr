---
title: Şirket içinde barındırılan ağ geçidini Docker 'a dağıt | Microsoft Docs
description: Azure API Management şirket içinde barındırılan bir ağ geçidi bileşenini Docker 'a dağıtmayı öğrenin
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/19/2021
ms.author: apimpm
ms.openlocfilehash: 531421726bc1e081d85eca9d535267520d3fea5f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725628"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Azure API Management şirket içinde barındırılan bir ağ geçidini Docker 'a dağıtma

Bu makalede, Azure API Management şirket içinde barındırılan ağ geçidi bileşenini bir Docker ortamına dağıtma adımları sağlanmaktadır.

> [!NOTE]
> Docker 'da şirket içinde barındırılan ağ geçidini barındırmak, değerlendirme ve geliştirme kullanım durumları için idealdir. Kubernetes, üretim kullanımı için önerilir. Kendi kendine barındırılan ağ geçidini Kubernetes 'e dağıtmayı öğrenmek için [Bu](how-to-deploy-self-hosted-gateway-kubernetes.md) belgeye bakın.

## <a name="prerequisites"></a>Önkoşullar

- Şu hızlı başlangıcı doldurun: [Azure API Management örneği oluşturma](get-started-create-service-instance.md)
- Docker ortamı oluşturun. [Masaüstü Için Docker](https://www.docker.com/products/docker-desktop) , geliştirme ve değerlendirme amaçları için iyi bir seçenektir. Tüm Docker sürümleri, özellikleri ve Docker üzerinde kapsamlı belgeler hakkında bilgi için bkz. [Docker belgeleri](https://docs.docker.com) .
- [API Management örneğiniz için bir ağ geçidi kaynağı sağlama](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Şirket içinde barındırılan ağ geçidi, x86-64 Linux tabanlı bir Docker kapsayıcısı olarak paketlenmiştir.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Şirket içinde barındırılan ağ geçidini Docker 'a dağıtma

1. **Dağıtım ve altyapı** altında **ağ geçitleri** ' ni seçin.
2. Dağıtmayı düşündüğünüz ağ geçidi kaynağını seçin.
3. **Dağıtım**' ı seçin.
4. **Belirteç** metin kutusundaki bir erişim belirtecinin, varsayılan **süre sonu** ve **gizli anahtar** değerleri kullanılarak sizin için otomatik olarak oluşturulduğunu unutmayın. Gerekirse, yeni bir belirteç oluşturmak için, ya da her iki denetimin içindeki istenen değerleri seçin.
5. **Dağıtım betikleri** altında **Docker** ' ın seçildiğinden emin olun.
6. Dosyayı indirmek için **ortamın** yanındaki **env. conf** dosya bağlantısını seçin.
7. Docker komutunu panoya kopyalamak için **Çalıştır** metin kutusunun sağ ucunda bulunan **Kopyala** simgesini seçin.
8. Komutu Terminal (veya komut) penceresine yapıştırın. Bağlantı noktası eşlemelerini ve kapsayıcı adını gereken şekilde ayarlayın. Komutun, indirilen ortam dosyasının geçerli dizinde bulunduğunu varsaydığını unutmayın.
   ```
       docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
   ```
9. Komutu yürütün. Bu komut, Docker ortamınızdan Microsoft Container Registry indirilen [kapsayıcı görüntüsünü](https://aka.ms/apim/sputnik/dhub) kullanarak kapsayıcıyı çalıştırmasını ve kapsayıcının HTTP (8080) ve HTTPS (8081) bağlantı noktalarını konakta 80 ve 443 bağlantı noktalarına eşlemenizi ister.
10. Ağ Geçidi kapsayıcısının çalışıp çalışmadığını denetlemek için aşağıdaki komutu çalıştırın:
    ```console
    docker ps
    CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
    895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
    ```
10. Azure portal geri dönüp **genel bakış** ' a tıklayın ve yeni dağıttığınız şirket içinde barındırılan ağ geçidi kapsayıcısının sağlıklı bir durum raporladığı konusunda emin olun.

    ![Ağ Geçidi durumu](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> <code>console docker container logs <gateway-name></code>Şirket içinde barındırılan ağ geçidi günlüğünün anlık görüntüsünü görüntülemek için komutunu kullanın.
>
> <code>docker container logs --help</code>Tüm günlük görüntüleme seçeneklerini görmek için komutunu kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* Şirket içinde barındırılan ağ geçidi hakkında daha fazla bilgi edinmek için bkz. [Azure API Management şirket içinde barındırılan ağ geçidine genel bakış](self-hosted-gateway-overview.md).
* [Şirket içinde barındırılan ağ geçidi için özel etki alanı adını yapılandırın](api-management-howto-configure-custom-domain-gateway.md).
