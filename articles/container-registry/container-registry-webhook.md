---
title: Kayıt defteri eylemlerine yanıt vermek için Web kancaları
description: Kayıt defteri depolarınızda gönderme veya çekme eylemleri gerçekleştiğinde olayları tetiklemek için Web kancalarını nasıl kullanacağınızı öğrenin.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 4f6fb719f8d9d51429a19616aa5548b32a2687e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773409"
---
# <a name="using-azure-container-registry-webhooks"></a>Azure Container Registry Web kancalarını kullanma

Azure kapsayıcısı kayıt defteri, Docker Hub’ın genel Docker görüntülerini depolama yöntemine benzer şekilde özel Docker kapsayıcı görüntülerini depolar ve yönetir. Ayrıca, Kubernetes 'e uygulama dağıtmak için bir paketleme biçimi olan [hele grafikleri](container-registry-helm-repos.md) (Önizleme) için depolar da barındırabilir. Kayıt defteri depolarınızdan birinde belirli işlemler gerçekleşirken olayları tetiklemek için Web kancalarını kullanabilirsiniz. Web kancaları, kayıt defteri düzeyindeki olaylara yanıt verebilir veya belirli bir depo etiketine göre kapsam yapılabilir. Coğrafi olarak  [çoğaltılan](container-registry-geo-replication.md) bir kayıt defteriyle, her Web kancasını belirli bir bölgesel çoğaltmada olaylara yanıt verecek şekilde yapılandırırsınız.

Bir Web kancası için uç nokta, kayıt defterinden herkese açık bir şekilde erişilebilir olmalıdır. Kayıt defteri Web kancası isteklerini, güvenli bir uç noktada kimlik doğrulaması yapacak şekilde yapılandırabilirsiniz.

Web kancası istekleri hakkında daha fazla bilgi için bkz. [Azure Container Registry Web kancası şeması başvurusu](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Önkoşullar

* Azure kapsayıcısı kayıt defteri -Azure aboneliğinizde bir kapsayıcı kayıt defteri oluşturun. Örneğin, [Azure Portal](container-registry-get-started-portal.md) veya [Azure CLI](container-registry-get-started-azure-cli.md)'yi kullanın. [Azure Container Registry hizmet katmanlarında](container-registry-skus.md) farklı Web kancaları kotaları vardır.
* Docker CLI - Yerel bilgisayarınızı bir Docker konağı olarak ayarlamak ve Docker CLI komutlarına erişmek için [Docker Engine](https://docs.docker.com/engine/installation/)’i yükleyin.

## <a name="create-webhook---azure-portal"></a>Web kancası oluşturma-Azure portal

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Web kancası oluşturmak istediğiniz kapsayıcı kayıt defterine gidin.
1. **Hizmetler** altında **Web kancaları**' nı seçin.
1. Web kancası araç çubuğunda **Ekle** ' yi seçin.
1. *Web kancası oluştur* formunu aşağıdaki bilgilerle doldurun:

| Değer | Açıklama |
|---|---|
| Web kancası adı | Web kancasına vermek istediğiniz ad. Yalnızca harf ve rakam içerebilir ve 5-50 karakter uzunluğunda olmalıdır. |
| Konum | Coğrafi olarak [çoğaltılan](container-registry-geo-replication.md) bir kayıt defteri için kayıt defteri çoğaltmasının Azure bölgesini belirtin. 
| Hizmet URI 'SI | Web kancasının POST bildirimleri gönderebilmesi gereken URI. |
| Özel üst bilgiler | POST isteğiyle birlikte geçirmek istediğiniz üst bilgiler. Bunlar "anahtar: değer" biçiminde olmalıdır. |
| Tetikleyici eylemleri | Web kancasını tetikleyen eylemler. Görüntü gönderme, görüntü silme, Helu grafik gönderimi, Held grafik silme ve görüntü karantinasında eylemler bulunur. Web kancasını tetiklemek için bir veya daha fazla eylem seçebilirsiniz. |
| Durum | Web kancası oluşturulduktan sonra durumu. Varsayılan olarak etkindir. |
| Kapsam | Web kancasının çalıştığı kapsam. Belirtilmezse, kapsam kayıt defterindeki tüm olaylar içindir. Bir depo altındaki tüm etiketler için "Repository: Tag" veya "Repository: *" biçimi kullanılarak bir depo veya etiket için belirtilebilir. |

Örnek Web kancası formu:

![Azure portal ACR Web kancası oluşturma U ı 'nı gösteren ekran görüntüsü.](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Web kancası oluşturma-Azure CLı

Azure CLı kullanarak bir Web kancası oluşturmak için [az ACR Web kancası Create](/cli/azure/acr/webhook#az_acr_webhook_create) komutunu kullanın. Aşağıdaki komut, kayıt defteri *mycontainerregistry* içindeki tüm görüntü silme olayları için bir Web kancası oluşturur:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Web kancasını test et

### <a name="azure-portal"></a>Azure portalı

Web kancasını kullanmadan önce **ping** düğmesini kullanarak test edebilirsiniz. Ping, belirtilen uç noktaya genel bir POST isteği gönderir ve yanıtı günlüğe kaydeder. Ping özelliğinin kullanılması, Web kancasını doğru şekilde yapılandırdığınızdan emin olmanıza yardımcı olabilir.

1. Test etmek istediğiniz Web kancasını seçin.
2. Üstteki araç çubuğunda **ping**' i seçin.
3. **Http durum** sütunundaki bitiş noktasının yanıtını kontrol edin.

![Azure portal ACR Web kancası oluşturma kullanıcı arabirimi](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Bir ACR Web kancasını Azure CLı ile test etmek için [az ACR Web kancası ping](/cli/azure/acr/webhook#az_acr_webhook_ping) komutunu kullanın.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Sonuçları görmek için [az ACR Web kancası List-Events](/cli/azure/acr/webhook) komutunu kullanın.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Web kancasını Sil

### <a name="azure-portal"></a>Azure portalı

Her Web kancası, Web kancası ve ardından Azure portal **Sil** düğmesi seçilerek silinebilir.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Sonraki adımlar

### <a name="webhook-schema-reference"></a>Web kancası şema başvurusu

Azure Container Registry tarafından oluşturulan JSON olay yüklerin biçimi ve özellikleri hakkında ayrıntılı bilgi için bkz. Web kancası şeması başvurusu:

[Web kancası şeması başvurusunu Azure Container Registry](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Event Grid olaylar

Bu makalede ele alınan yerel kayıt defteri Web kancası olaylarının yanı sıra, Azure Container Registry Event Grid için olayları yayabilir:

[Hızlı başlangıç: kapsayıcı kayıt defteri olaylarını Event Grid gönder](container-registry-event-grid-quickstart.md)
