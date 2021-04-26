---
title: storage-files-create-storage-account-portal
description: Azure Dosyaları için depolama hesabı oluşturun.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33159ab6dd014a153c8fd317fd291aeca033d6e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717878"
---
Depolama hesabı, Azure dosya paylaşımını veya bloblar veya sorgular gibi diğer depolama kaynaklarını dağıtabileceğiniz, paylaşılan bir depolama havuzudur. Bir depolama hesabında sınırsız sayıda paylaşım olabilir. Bir paylaşım, depolama hesabının kapasite limitlerine kadar sınırsız sayıda dosyayı depolayabilir.

Depolama hesabı oluşturmak için:

1. Sol taraftaki menüden **+** kaynak oluşturmak için öğesini seçin.
1. Depolama hesabı **oluşturmak için depolama hesabı '** nı seçin.

    :::image type="content" source="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png" alt-text="Kaynak oluşturma dikey penceresinde depolama hesabı seçeneğinin ekran görüntüsü." lightbox="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png":::

1. **Ad** alanına *mystorageacct* komutunu yazın ve benzersiz bir ad olduğunu belirten yeşil onay işareti görüntüleninceye kadar komutun sonuna birkaç rastgele sayı yazın. Depolama hesabı adı tamamen küçük harflerden oluşmalı ve genel olarak benzersiz olmalıdır. Depolama hesabınızın adını not edin. Daha sonra bu adı kullanacaksınız. 
1. **Performans** altında varsayılan değeri **Standart** olarak tutun.
1. **Çoğaltma** bölümünde **Yerel olarak yedekli depolama (LRS)** seçeneğini belirleyin.
1. **Abonelik** bölümünde, depolama hesabını oluşturmak için kullanılan aboneliği seçin. Yalnızca bir aboneliğiniz varsa bu, varsayılan değer olmalıdır.
1. **Kaynak grubu**’nda **Yeni oluştur**’u seçin. Ad için *myResourceGroup* girin.
1. **Konum** bölümünde **Doğu ABD**’yi seçin.
1. İşleminiz bittiğinde dağıtımı başlatmak için **Oluştur**’a tıklayın.