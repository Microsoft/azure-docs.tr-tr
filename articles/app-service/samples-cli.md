---
title: CLI Örnekleri
description: Bazı yaygın App Service senaryolarından bazılarına yönelik Azure CLı örneklerini bulun. App Service dağıtımınızı veya yönetim görevlerinizi otomatikleştirmeyi öğrenin.
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 07/07/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 51cdd470c9ae970d5945f8d5399cea67a44037f0
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483393"
---
# <a name="cli-samples-for-azure-app-service"></a>Azure App Service için CLı örnekleri

Aşağıdaki tablo, Azure CLI’si kullanılarak oluşturulan bash komut dosyalarına yönelik bağlantılar içerir.

| Komut Dosyası | Açıklama |
|-|-|
|**Uygulama oluşturma**||
| [FTP ile uygulama oluşturma ve dosya dağıtma](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Bir App Service uygulaması oluşturur ve FTP kullanarak dosyaya bir dosya dağıtır. |
| [GitHub 'dan bir uygulama oluşturma ve kod dağıtma](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Bir App Service uygulaması oluşturur ve ortak bir GitHub deposundan kodu dağıtır. |
| [GitHub 'dan sürekli dağıtım ile uygulama oluşturma](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Sahip olduğunuz bir GitHub deposundan sürekli yayımlamaya sahip bir App Service uygulaması oluşturur. |
| [Yerel bir git deposundan uygulama oluşturma ve kod dağıtma](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Bir App Service uygulaması oluşturur ve yerel bir git deposundan kod gönderimi yapılandırır. |
| [Bir uygulama oluşturma ve hazırlama ortamına kod dağıtma](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Hazırlama kodu değişiklikleri için dağıtım yuvası olan bir App Service uygulaması oluşturur. |
| [Docker kapsayıcısında ASP.NET Core uygulaması oluşturma](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json) | Linux üzerinde bir App Service uygulaması oluşturur ve Docker Hub 'ından bir Docker görüntüsü yükler. |
| [Bir uygulama oluşturun ve özel bir uç noktayla sunun](./scripts/cli-deploy-privateendpoint.md?toc=%2fcli%2fazure%2ftoc.json) | Bir App Service uygulaması ve özel bir uç nokta oluşturur |
|**Uygulama yapılandırma**||
| [Özel bir etki alanını uygulamayla eşleme](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Bir App Service uygulaması oluşturur ve buna özel bir etki alanı adı eşleştirir. |
| [Uygulamaya özel bir TLS/SSL sertifikası bağlama](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Bir App Service uygulaması oluşturur ve özel bir etki alanı adının TLS/SSL sertifikasını buna bağlar. |
|**Uygulama ölçeklendirme**||
| [Uygulamayı el ile ölçeklendirme](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Bir App Service uygulaması oluşturur ve 2 örnek boyunca ölçeklendirir. |
| [Yüksek kullanılabilirlik mimarisi ile bir uygulamayı dünya genelinde ölçeklendirin](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | İki farklı coğrafi bölgede iki App Service uygulama oluşturur ve Azure Traffic Manager kullanarak tek bir uç nokta üzerinden kullanılabilir hale getirir. |
|**Uygulamayı koru**||
| [Azure Application Gateway ile tümleştirin](./scripts/cli-integrate-app-service-with-application-gateway.md?toc=%2fcli%2fazure%2ftoc.json) | App Service bir uygulama oluşturur ve hizmet uç noktası ve erişim kısıtlamalarını kullanarak Application Gateway ile tümleştirir. |
|**Uygulamayı kaynaklara bağlama**||
| [Bir uygulamayı SQL veritabanına bağlama](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Azure SQL veritabanı 'nda bir App Service uygulaması ve veritabanı oluşturur, ardından veritabanı bağlantı dizesini uygulama ayarlarına ekler. |
| [Bir uygulamayı depolama hesabına bağlama](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Bir App Service uygulaması ve depolama hesabı oluşturur, ardından depolama bağlantı dizesini uygulama ayarlarına ekler. |
| [Redsıs için bir uygulamayı Azure önbelleğine bağlama](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Redsıs için bir App Service uygulaması ve Azure önbelleği oluşturur, ardından uygulama ayarlarına redsıs bağlantı ayrıntılarını ekler.) |
| [Uygulamayı Cosmos DB bağlama](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | App Service bir uygulama ve bir Cosmos DB oluşturur ve Cosmos DB bağlantı ayrıntılarını uygulama ayarlarına ekler. |
|**Yedekleme ve geri yükleme uygulaması**||
| [Uygulama yedekleme](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Bir App Service uygulaması oluşturur ve bu uygulama için bir kerelik yedekleme oluşturur. |
| [Bir uygulama için zamanlanmış yedekleme oluşturma](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Bir App Service uygulaması oluşturur ve bu uygulama için zamanlanmış bir yedekleme oluşturur. |
| [Bir uygulamayı yedekten geri yükler](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Bir App Service uygulamasını yedekten geri yükler. |
|**Uygulama izleme**||
| [Web sunucusu günlükleri ile bir appÂ izleme](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | App Service bir uygulama oluşturur, günlüğe kaydetmeyi sağlar ve günlükleri yerel makinenize indirir. |
| | |
