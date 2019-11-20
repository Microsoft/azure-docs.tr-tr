---
title: Şirket içinde barındırılan Azure API Management ağ geçidini Docker 'a dağıtma | Microsoft Docs
description: Şirket içinde barındırılan bir Azure API Management ağ geçidini Docker 'a dağıtmayı öğrenin
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: fc7e0f7e4e0e80a030a437bc4f0f13360595f32e
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747696"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Azure API Management şirket içinde barındırılan bir ağ geçidini Docker 'a dağıtma

Bu makalede, şirket içinde barındırılan Azure API Management ağ geçidini bir Docker ortamına dağıtma adımları sağlanmaktadır.

> [!NOTE]
> Şirket içinde barındırılan ağ geçidi özelliği önizlemededir. Önizleme sırasında, şirket içinde barındırılan ağ geçidi, yalnızca geliştirici ve Premium katmanlarda ek ücret ödemeden kullanılabilir. Geliştirici katmanı, tek bir şirket içinde barındırılan ağ geçidi dağıtımıyla sınırlıdır.

## <a name="prerequisites"></a>Ön koşullar

- Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md)
- Docker ortamı oluşturun. [Masaüstü Için Docker](https://www.docker.com/products/docker-desktop) , geliştirme ve değerlendirme amaçları için iyi bir seçenektir. Tüm Docker sürümleri, özellikleri ve Docker üzerinde kapsamlı belgeler hakkında bilgi için bkz. [Docker belgeleri](https://docs.docker.com) .
- [API Management örneğiniz için bir ağ geçidi kaynağı sağlama](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Şirket içinde barındırılan ağ geçidi, Linux tabanlı bir Docker kapsayıcısı olarak paketlenmiştir.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Şirket içinde barındırılan ağ geçidini Docker 'a dağıtma

1. **Ayarlar**altında **ağ geçitleri** ' ni seçin.
2. Dağıtmayı düşündüğünüz ağ geçidi kaynağını seçin.
3. **Dağıtım**' ı seçin.
4. Varsayılan **süre sonu** ve **gizli anahtar** değerlerini kullanarak, **belirteç** metin kutusundaki yeni bir belirtecin sizin için otomatik olarak oluşturulduğunu unutmayın. İsterseniz veya her ikisini de ayarlayın ve yeni bir belirteç oluşturmak için **Oluştur** ' u seçin.
4. **Dağıtım betikleri**altında **Docker** ' ın seçildiğinden emin olun.
5. Dosyayı indirmek için **ortamın** yanındaki **env. conf** dosya bağlantısını seçin.
6. Docker komutunu panoya kaydetmek için **Çalıştır** metin kutusunun sağ ucunda bulunan **Kopyala** simgesini seçin.
7. Komutu Terminal (veya komut) penceresine yapıştırın. Bağlantı noktası eşlemelerini ve kapsayıcı adını gereken şekilde ayarlayın. Komutun indirilen ortam dosyasının geçerli dizinde mevcut olmasını beklediğini unutmayın.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Komutu yürütün. Bu komut, Docker ortamınızı kapsayıcıyı çalıştırmak, şirket içinde barındırılan ağ geçidinin Microsoft Container Registry indirilen görüntüsünü kullanmak ve kapsayıcının HTTP (8080) ve HTTPS (8081) bağlantı noktalarını konakta 80 ve 443 bağlantı noktalarıyla eşleştirmek için yönlendirir.
9. Ağ Geçidi Pod 'un çalıştığını denetlemek için aşağıdaki komutu çalıştırın:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Azure portal 'e geri dönün ve az önce dağıttığınız ağ geçidi düğümünün sağlıklı durumda olduğunu doğrulayın.

![Ağ Geçidi durumu](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Şirket içinde barındırılan ağ geçidi günlüğünün anlık görüntüsünü görüntülemek için <code>console docker container logs <gateway-name></code> komutunu kullanın.
>
> Tüm günlük görüntüleme seçeneklerini görmek için <code>docker container logs --help</code> komutunu kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* Şirket içinde barındırılan ağ geçidi hakkında daha fazla bilgi edinmek için bkz. [Azure API Management şirket içinde barındırılan ağ geçidine genel bakış](self-hosted-gateway-overview.md).
* [Şirket içinde barındırılan ağ geçidi için özel etki alanı adını yapılandırın](api-management-howto-configure-custom-domain-gateway.md).
