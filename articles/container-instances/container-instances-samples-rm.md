---
title: Azure Resource Manager şablonu örnekleri-Azure Container Instances
description: Farklı yapılandırmalarda Azure Container Instances dağıtmak için Azure Resource Manager şablon örnekleri bulma
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/07/2019
ms.author: danlep
ms.openlocfilehash: 7dead5cc83717a011120ef6c17b9268bd78ed171
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150147"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Container Instances için Azure Resource Manager Şablonlar

Aşağıdaki örnek şablonlar çeşitli yapılandırmalarda kapsayıcı örnekleri dağıtır.

Dağıtım seçenekleri için [dağıtım](#deployment) bölümüne bakın. Kendi şablonlarınızı oluşturmak isterseniz, Azure Container Instances [Kaynak Yöneticisi Şablon başvuru][ref] ayrıntıları şablon biçimi ve kullanılabilir özellikleri.

## <a name="sample-templates"></a>Örnek şablonlar

| | |
|-|-|
| **Uygulamalar** ||
| [WordPress][app-wp] | Bir bir WordPress web sitesi ve bir kapsayıcı grubunda MySQL veritabanı oluşturur. WordPress sitesi içeriği ve MySQL veritabanı, bir Azure dosya paylaşımında kalıcı hale getirilir. Ayrıca, WordPress 'e genel ağ erişimini açığa çıkarmak için bir uygulama ağ geçidi oluşturur. |
| [SQL Server ve IIS ile MS NAV][app-nav] | Tam özellikli bir yerleşik Dynamics NAV/Dynamics 365 Business Central ortamıyla tek bir Windows kapsayıcısını dağıtır. |
| **Dörtten** ||
| [emptyDir][vol-emptydir] | Bir emptyDir birimini paylaşan iki Linux kapsayıcısı dağıtır. |
| [gitRepo][vol-gitrepo] | GitHub deposunun klontığı bir Linux kapsayıcısını dağıtır ve onu bir birim olarak takar. |
| [gizlilikle][vol-secret] | Gizli bir birim olarak bağlanmış bir PFX sertifikası ile Linux kapsayıcısı dağıtır. |
| **Ağ** ||
| [UDP tarafından sunulan kapsayıcı][net-udp] | UDP bağlantı noktasını kullanıma sunan bir Windows veya Linux kapsayıcısı dağıtır. |
| [Ortak IP ile Linux kapsayıcısı][net-publicip] | Genel IP aracılığıyla erişilebilen tek bir Linux kapsayıcısını dağıtır. |
| [Sanal ağ ile bir kapsayıcı grubu dağıtma (Önizleme)][net-vnet] | Yeni bir sanal ağ, alt ağ, ağ profili ve kapsayıcı grubu dağıtır. |
| **Azure kaynakları** ||
| [Azure depolama hesabı ve dosya paylaşma oluşturma][az-files] | Bir depolama hesabı ve bir Azure dosya paylaşma oluşturmak için bir kapsayıcı örneğinde Azure CLı kullanır.

## <a name="deployment"></a>Dağıtım

Kaynak Yöneticisi şablonlarıyla kaynak dağıtmaya yönelik çeşitli seçenekleriniz vardır:

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure Portal][deploy-portal]

[REST API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
