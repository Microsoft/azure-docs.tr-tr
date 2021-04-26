---
title: Doğrudan bağlantı Azure Depolama Gezgini | Microsoft Docs
description: Azure Depolama Gezgini doğrudan bağlantısının belgeleri
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 121df1e1c3ab6d0741d3e4da1144a86938da70ed
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582337"
---
# <a name="azure-storage-explorer-direct-link"></a>Doğrudan bağlantı Azure Depolama Gezgini

Windows ve macOS 'ta, Depolama Gezgini protokolü olan URI 'Leri destekler `storageexplorer://` . Bu URI 'Ler doğrudan bağlantılar olarak adlandırılır. Doğrudan bağlantı, Depolama Gezgini bir Azure Storage kaynağına işaret eder. Bir doğrudan bağlantıyı izleyen Depolama Gezgini açıp işaret ettiği kaynağa gitmeniz gerekir. Bu makalede, doğrudan bağlantıların nasıl çalıştığı ve bunları nasıl kullanabileceğiniz açıklanır.

## <a name="how-direct-links-work"></a>Doğrudan bağlantılar nasıl çalışır?

Depolama Gezgini, Azure 'daki kaynakları görselleştirmek için ağaç görünümünü kullanır. Doğrudan bağlantı, ağaçtaki bağlantılı kaynak düğümü için hiyerarşik bilgileri içerir. Doğrudan bağlantı sonrasında, Depolama Gezgini açılır ve parametreleri doğrudan bağlantı içinde alır. Depolama Gezgini ardından bu parametreleri ağaç görünümündeki bağlantılı kaynağa gitmek için kullanır.

> [!IMPORTANT]
> Oturum açmış olmanız ve doğrudan bir bağlantının çalışması için bağlantılı kaynağa erişmek için gerekli izinlere sahip olmanız gerekir.

## <a name="parameters"></a>Parametreler

Depolama Gezgini doğrudan bağlantısı her zaman protokolle başlar `storageexplorer://` . Aşağıdaki tabloda, bir doğrudan bağlantı içindeki olası parametrelerin her biri açıklanmaktadır.

Parametre | Açıklama
:---------| :---------
`v`         | Doğrudan bağlantı protokolünün sürümü.
`accountid` | Bağlı kaynak için depolama hesabının Azure Resource Manager kaynak KIMLIĞI. Bağlantılı kaynak bir depolama hesabı ise bu KIMLIK, bu depolama hesabının Azure Resource Manager kaynak KIMLIĞI olur. Aksi takdirde bu KIMLIK, bağlantılı kaynağın ait olduğu depolama hesabının Azure Resource Manager kaynak KIMLIĞI olur.
`resourcetype` | İsteğe bağlı. Yalnızca bağlantılı kaynak bir blob kapsayıcısı, bir dosya paylaşma, kuyruk veya tablo olduğunda kullanılır. "Azure. BlobContainer", "Azure. FileShare", "Azure. Queue", "Azure. FileShare" seçeneklerinden biri olmalıdır.
`resourcename` | İsteğe bağlı. Yalnızca bağlantılı kaynak bir blob kapsayıcısı, bir dosya paylaşma, kuyruk veya tablo olduğunda kullanılır. Bağlantılı kaynağın adı.

Örnek olarak bir blob kapsayıcısına doğrudan bağlantı verilmiştir. 
`storageexplorer://v=1&accountid=/subscriptions/the_subscription_id/resourceGroups/the_resource_group_name/providers/Microsoft.Storage/storageAccounts/the_storage_account_name&subscriptionid=the_subscription_id&resourcetype=Azure.BlobContainer&resourcename=the_blob_container_name`

## <a name="get-a-direct-link-from-storage-explorer"></a>Depolama Gezgini doğrudan bağlantı al

Bir kaynağın doğrudan bağlantısını almak için Depolama Gezgini kullanabilirsiniz. Ağaç görünümünde kaynak için düğümün bağlam menüsünü açın. Sonra doğrudan bağlantısını panoya kopyalamak için "doğrudan bağlantıyı kopyala" eylemini kullanın.

## <a name="direct-link-limitations"></a>Doğrudan bağlantı sınırlamaları

Doğrudan bağlantılar yalnızca abonelik düğümleri kapsamındaki kaynaklar için desteklenir. Ayrıca, yalnızca aşağıdaki kaynak türleri desteklenir:

- Depolama Hesapları
- Blob Kapsayıcıları
- Kuyruklar
- Dosya Paylaşımları
- Tables
