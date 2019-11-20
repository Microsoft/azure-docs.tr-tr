---
title: Azure Container Instance tarif
titleSuffix: Azure Cognitive Services
description: Azure Container Instance üzerinde Bilişsel Hizmetleri kapsayıcıları dağıtma hakkında bilgi edinin
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 288894705e1108d6dd511b60cd2bc3bcee4c6d41
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704366"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Dağıtma ve Azure Container Instance üzerinde kapsayıcı çalıştırma

Azure ile kolayca bulut uygulamalarında Azure Bilişsel hizmetler aşağıdaki adımlarla ölçeklendirme [kapsayıcı örneği](https://docs.microsoft.com/azure/container-instances/). Bu altyapıyı yönetmek yerine uygulamalarınıza oluşturmaya odaklanmanıza yardımcı olur.

## <a name="prerequisites"></a>Önkoşullar

Bu çözüm, tüm Bilişsel hizmetler kapsayıcısı ile çalışır. Bilişsel Hizmet kaynağı Azure portalında bu tarif kullanmadan önce oluşturulması gerekir. Yükleme ve yapılandırma için bir kapsayıcı hizmeti için özel bir "nasıl yükleneceği" Belge kapsayıcıları destekleyen her Bilişsel hizmet içerir. Bazı hizmetler kapsayıcısı için giriş olarak bir dosya veya dosyalar kümesi gerektirdiği için anlamak ve bu çözümü kullanarak önce kapsayıcı başarıyla kullandınız önemlidir.

* Azure portalında oluşturulan bir Bilişsel Hizmet kaynağı.
* Bilişsel hizmet **uç nokta URL'si** -", belirli hizmetin nasıl yükleneceği" için kapsayıcı gözden geçirin, uç nokta URL'sini gelen Azure portalı ve hangi içinde olduğu bulmak için bir doğru örnek URL'sini benzer. Hizmetten hizmete tam biçimini değiştirebilirsiniz.
* Bilişsel hizmet **anahtarı** -anahtarları bulunan **anahtarları** Azure kaynak sayfası. Yalnızca iki anahtarlarından biri gerekir. Anahtar 32 alfasayısal karakter, bir dizedir.
* Tek bir Bilişsel hizmetler kapsayıcısı yerel ana bilgisayarınızda (bilgisayar). Emin olun, şunları yapabilirsiniz:
  * Görüntüyü aşağı çekmek bir `docker pull` komutu.
  * Yerel kapsayıcı ile tüm gerekli yapılandırma ayarlarıyla başarıyla çalıştırılmış bir `docker run` komutu.
  * Kapsayıcının uç noktası, 2xx yanıtı ve bir JSON yanıtı geri alma çağırın.

Açılı ayraçlar içinde tüm değişkenlerinin `<>`, kendi değerlerinizle değiştirilmesi gerekebilir. Bu değişiklik, açılı ayraçlar içerir.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Kapsayıcı örneğini kullan

1. Seçin **genel bakış** ve IP adresini kopyalayın. Sayısal bir IP adresi gibi olacaktır `55.55.55.55`.
1. Örneğin, IP adresi kullanın ve yeni bir tarayıcı sekmesi açın `http://<IP-address>:5000 (http://55.55.55.55:5000`). Kapsayıcıyı çalıştıran tamamlanamayacağını kapsayıcının giriş sayfasını görürsünüz.

1. Seçin **hizmet API açıklaması** kapsayıcısı için swagger sayfasını görüntülemek için.

1. Herhangi bir **POST** API'ler ve select **denemek**.  Giriş dahil olmak üzere parametreler görüntülenir. Parametrelerini doldurun.

1. Seçin **yürütme** kapsayıcı Örneğinize isteği göndermek için.

    Başarıyla oluşturulan ve Bilişsel hizmetler, kapsayıcılar, Azure Container Instance üzerinde kullanılır.
