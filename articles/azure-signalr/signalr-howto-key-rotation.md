---
title: Azure SignalR Hizmeti için erişim anahtarını döndürme
description: Müşterinin neden erişim tuşlarını düzenli olarak döndürme ve bunu Azure portal GUI ve Azure CLı ile nasıl yaptığı hakkında genel bir bakış.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: cd03720b65883c5f43b11837d4a88a705209fb10
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764108"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Azure SignalR Hizmeti için erişim anahtarını döndürme

Her Azure SignalR hizmeti örneğinin, birincil ve Ikincil anahtarlar adlı bir dizi erişim anahtarı vardır. İstekler hizmete yapıldığında, SignalR istemcilerinin kimliğini doğrulamak için kullanılır. Anahtarlar örnek uç nokta URL 'si ile ilişkilendirilir. Anahtarlarınızı güvende tutun ve düzenli olarak döndürün. İki erişim anahtarı ile sunulmaktadır. böylece, diğerini yeniden oluştururken bir anahtar kullanarak bağlantıları koruyabilirsiniz.

## <a name="why-rotate-access-keys"></a>Erişim anahtarları neden döndürülemiyor?

Güvenlik nedenleriyle ve uyumluluk gereksinimlerinde, erişim anahtarlarınızı düzenli olarak döndürün.

## <a name="regenerate-access-keys"></a>Erişim anahtarlarını yeniden oluştur

1. [Azure Portal](https://portal.azure.com/)gidin ve kimlik bilgilerinizle oturum açın.

1. Azure SignalR hizmeti örneğindeki **anahtarlar** bölümünü, yeniden oluşturmak istediğiniz anahtarlarla bulun.

1. Gezinti menüsünde **anahtarlar** ' ı seçin.

1. **Birincil anahtarı yeniden oluştur** veya **Ikincil anahtarı yeniden oluştur**' u seçin.

   Yeni bir anahtar ve karşılık gelen bağlantı dizesi oluşturulur ve görüntülenir.

   ![Anahtarları yeniden oluştur](media/signalr-howto-key-rotation/regenerate-keys.png)

Ayrıca [Azure CLI](/cli/azure/signalr/key#az_signalr_key_renew)kullanarak anahtarları yeniden oluşturabilirsiniz.

## <a name="update-configurations-with-new-connection-strings"></a>Yeni bağlantı dizeleri ile güncelleştirme yapılandırması

1. Yeni oluşturulan bağlantı dizesini kopyalayın.

1. Tüm konfigürasyonları yeni bağlantı dizesini kullanacak şekilde güncelleştirin.

1. Uygulamayı gerektiği gibi yeniden başlatın.

## <a name="forced-access-key-regeneration"></a>Zorunlu erişim anahtarı yeniden oluşturma

Azure SignalR hizmeti, bazı durumlarda zorunlu bir erişim anahtarı yeniden oluşturma işlemi uygulayabilir. Hizmet, müşterileri e-posta ve Portal bildirimi aracılığıyla bilgilendirir. Bu iletişimi alırsanız veya bir erişim anahtarı nedeniyle hizmet hatası yaşarsanız, bu kılavuzdaki yönergeleri izleyerek anahtarları döndürün.

## <a name="next-steps"></a>Sonraki adımlar

Erişim anahtarlarınızı düzenli olarak iyi bir güvenlik uygulaması olarak döndürün.

Bu kılavuzda, erişim anahtarlarını yeniden oluşturmayı öğrendiniz. OAuth ile veya Azure Işlevleri ile kimlik doğrulama hakkında sonraki öğreticilere devam edin.

> [!div class="nextstepaction"]
> [ASP.NET Core kimliğiyle tümleştirin](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Kimlik doğrulamasıyla sunucusuz gerçek zamanlı bir uygulama oluşturma](./signalr-tutorial-authenticate-azure-functions.md)