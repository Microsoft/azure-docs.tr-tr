---
title: Azure Resource Manager şablonu örnekleri - App Service | Microsoft Docs
description: App Service için Azure Resource Manager şablonu örnekleri
services: app-service
documentationcenter: app-service
author: tfitzmac
tags: azure-service-management
ms.service: app-service
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 83bb357544d9069da1c86f583eaca5a470953ce8
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066481"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>App Service için Azure Resource Manager Şablonlar

Aşağıdaki tabloda, Azure App Service için Azure Resource Manager şablonlarının bağlantıları yer almaktadır. Uygulama şablonları oluştururken sık karşılaşılan hataların önlenmesiyle ilgili öneriler için bkz. [Azure Resource Manager şablonlarıyla uygulama dağıtma Kılavuzu](deploy-resource-manager-template.md).

App Services kaynakları için JSON sözdizimi ve özellikleri hakkında bilgi edinmek için bkz. [Microsoft. Web kaynak türleri](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Uygulama dağıtma**||
| [App Service planı ve temel Linux uygulaması](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Linux için yapılandırılmış bir App Service uygulaması dağıtır. |
| [App Service planı ve temel Windows uygulaması](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Windows için yapılandırılmış bir App Service uygulamasını dağıtır. |
| [GitHub deposuna bağlı uygulama](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| GitHub 'dan kod çeken bir App Service uygulaması dağıtır. |
| [Özel dağıtım yuvaları olan uygulama](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Özel dağıtım yuvaları/ortamları ile bir App Service uygulaması dağıtır. |
|**Uygulama yapılandırma**||
| [Key Vault 'den uygulama sertifikası](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Bir Azure Key Vault gizli dizi App Service uygulama sertifikası dağıtır ve SSL bağlaması için bunu kullanır. |
| [Özel etki alanı olan uygulama](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Özel ana bilgisayar adına sahip bir App Service uygulaması dağıtır. |
| [Özel etki alanı ve SSL içeren uygulama](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| , Özel ana bilgisayar adına sahip bir App Service uygulaması dağıtır ve SSL bağlaması için Key Vault bir uygulama sertifikası alır. |
| [GoLang uzantılı uygulama](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Golang site uzantısıyla bir App Service uygulaması dağıtır. Daha sonra Azure’da Golang üzerinde geliştirilmiş web uygulamalarını çalıştırabilirsiniz. |
| [Java 8 ve Tomcat 8 ile uygulama](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Java 8 ve Tomcat 8 etkin bir App Service uygulaması dağıtır. Daha sonra Azure'da Java uygulamaları çalıştırabilirsiniz. |
|**Bağlı kaynaklarla Linux uygulaması**||
| [MySQL ile Linux üzerinde uygulama](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Linux üzerinde MySQL için Azure veritabanı ile bir App Service uygulaması dağıtır. |
| [Linux üzerinde PostgreSQL ile uygulama](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Linux üzerinde PostgreSQL için Azure veritabanı ile bir App Service uygulaması dağıtır. |
|**Bağlı kaynaklarla uygulama**||
| [MySQL ile uygulama](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Windows üzerinde MySQL için Azure veritabanı ile bir App Service uygulaması dağıtır. |
| [PostgreSQL ile uygulama](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Windows üzerinde PostgreSQL için Azure veritabanı ile bir App Service uygulaması dağıtır. |
| [SQL veritabanı ile uygulama](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Temel hizmet düzeyinde bir App Service uygulaması ve SQL veritabanı dağıtır. |
| [BLOB depolama bağlantısı olan uygulama](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Azure Blob depolama bağlantı dizesiyle bir App Service uygulaması dağıtır. Bundan sonra, uygulamadan BLOB depolama alanını kullanabilirsiniz. |
| [Redsıs için Azure önbelleği olan uygulama](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Redsıs için Azure önbelleği ile bir App Service uygulaması dağıtır. |
|**PowerApps için App Service Ortamı**||
| [App Service ortamı v2 oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Sanal ağınızda bir App Service ortamı v2 oluşturur. |
| [ILB adresli bir App Service ortamı v2 oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Sanal ağınızda özel bir iç yük dengeleyici adresine sahip App Service ortamı v2 oluşturur. |
| [Bir ILB App Service ortamı veya ILB App Service ortamı v2 için varsayılan SSL sertifikasını yapılandırma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Bir ILB App Service ortamı veya ILB App Service ortamı v2 için varsayılan SSL sertifikasını yapılandırır. |
| | |
