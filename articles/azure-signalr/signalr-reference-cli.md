---
title: Azure CLI Örnekleri - Azure SignalR Hizmeti
description: Azure SignalR hizmeti için Azure CLı 'yı öğrenmek üzere gerçek örnekleri izleyin. Diğer Azure hizmetleriyle SignalR hizmeti oluşturmayı öğreneceksiniz.
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 3c28781362e60d22b0d9a4e3754daaa24e5b61ee
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158135"
---
# <a name="azure-cli-reference"></a>Azure CLI başvurusu

Aşağıdaki tablo, Azure CLI kullanılarak Azure SignalR Hizmeti için bash betiklerine yönelik bağlantılar içerir.

| | |
|-|-|
|**Oluşturma**||
| [Yeni bir SignalR Hizmeti ve kaynak grubu oluşturma](scripts/signalr-cli-create-service.md) | Rasgele bir ad ile yeni bir kaynak grubunda yeni bir Azure SignalR Hizmeti kaynağı oluşturur.  |
|**Tümleştirme**||
| [SignalR kullanmak üzere yapılandırılmış yeni bir SignalR Hizmeti ve Web Uygulaması oluşturma](scripts/signalr-cli-create-with-app-service.md) | Rasgele bir ad ile yeni bir kaynak grubunda yeni bir Azure SignalR Hizmeti kaynağı oluşturur. Ayrıca, SignalR hizmetini kullanan bir ASP.NET Core Web uygulamasını barındırmak için yeni bir Web uygulaması ve App Service planı ekler. Web uygulaması, yeni SignalR hizmet kaynağına bağlanmak için bir Uygulama Ayarı ile yapılandırılır. |
| [SignalR ve GitHub OAuth kullanmak üzere yapılandırılmış yeni bir SignalR Hizmeti ve Web Uygulaması oluşturma](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Rasgele bir ad ile yeni bir kaynak grubunda yeni bir Azure SignalR Hizmeti kaynağı oluşturur. Ayrıca, SignalR hizmetini kullanan bir ASP.NET Core Web uygulamasını barındırmak için yeni bir Azure Web uygulaması ve barındırma planı da ekler. Web uygulaması, [kimlik doğrulaması öğreticisinde](https://developer.github.com/v3/guides/basics-of-authentication/) gösterildiği gibi, [GitHub kimlik doğrulamasını](signalr-concept-authenticate-oauth.md) desteklemek amacıyla yeni SignalR hizmeti kaynağının bağlantı dizesi ve istemci gizli dizisi için uygulama ayarlarıyla yapılandırılır. Web uygulaması ayrıca bir yerel git deposu dağıtım kaynağını kullanacak şekilde de yapılandırılır. |
| | |
