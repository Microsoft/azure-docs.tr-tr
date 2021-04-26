---
title: Azure CLı kullanarak Azure Content Delivery Network (CDN) profili ve uç noktası oluşturma
description: Azure CDN profili, uç nokta, kaynak grubu, kaynak ve özel etki alanı oluşturmak için Azure CLı örnek komut dosyaları.
author: asudbring
ms.author: allensu
manager: danielgi
ms.date: 03/09/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce01c1f851a5dbaedc85d848b12bf0b0831a16ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726935"
---
# <a name="create-an-azure-cdn-profile-and-endpoint-using-the-azure-cli"></a>Azure CLı kullanarak Azure CDN profili ve uç noktası oluşturma

Azure portal alternatif olarak, aşağıdaki CDN işlemlerini yönetmek için bu örnek Azure CLı betiklerini kullanabilirsiniz:

- Bir CDN profili oluşturun.
- CDN uç noktası oluşturma.
- Bir CDN kaynak grubu oluşturun ve varsayılan grup yapın.
- CDN kaynağı oluşturun.
- Özel bir etki alanı oluşturun ve HTTPS 'yi etkinleştirin.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-scripts"></a>Örnek betikler

CDN profiliniz için henüz bir kaynak grubunuz yoksa şu komutla oluşturun `az group create` :

```azurecli
# Create a resource group to use for the CDN.
az group create --name MyResourceGroup --location eastus

```

Aşağıdaki Azure CLı betiği bir CDN profili ve CDN uç noktası oluşturur:

```azurecli
# Create a CDN profile.
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint.
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com

```

Aşağıdaki Azure CLı betiği bir CDN kaynak grubu oluşturuyor, bir uç nokta için varsayılan kaynak grubunu ayarlıyor ve yeni bir kaynak oluşturuyor:

```azurecli
# Create an origin group.
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint.
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint.
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com

```

Aşağıdaki Azure CLı betiği bir CDN özel etki alanı oluşturur ve HTTPS 'yi sunar. Özel bir etki alanını Azure CDN bir uç noktasıyla ilişkilendirebilmeniz için önce, CDN uç noktanıza işaret etmek üzere Azure DNS veya DNS sağlayıcınıza sahip bir kurallı ad (CNAME) kaydı oluşturmanız gerekir. Daha fazla bilgi için bkz. [CNAME DNS kaydı oluşturma](../../../cdn/cdn-map-content-to-custom-domain.md#create-a-cname-dns-record).

```azurecli
# Associate a custom domain with an endpoint.
az cdn custom-domain create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain --hostname www.example.com

# Enable HTTPS on the custom domain.
az cdn custom-domain enable-https --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain

```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Örnek betikleri çalıştırmayı tamamladıktan sonra, kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komutu kullanın.

```azurecli
# Delete the resource group.
az group delete --name MyResourceGroup

```

## <a name="azure-cli-commands-used-in-this-article"></a>Bu makalede kullanılan Azure CLı komutları

- [az CDN Endpoint Create](/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [az CDN Endpoint Update](/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [az CDN Origin Create](/cli/azure/cdn/origin#az_cdn_origin_create)
- [az CDN Origin-Group Create](/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [az CDN profili oluşturma](/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az CDN Custom-Domain Create](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_create)
- [az CDN Custom-Domain Enable-https](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_enable_https)
