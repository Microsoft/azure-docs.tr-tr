---
title: Azure Data Studio kullanarak Azure SQL yönetilen örneği oluşturma
description: Azure Data Studio kullanarak Azure SQL yönetilen örneği oluşturma
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 651811384a8e831c41c48ca8c4849e5ee3852054
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93280475"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>Azure Data Studio kullanarak SQL yönetilen örneği oluşturma-Azure Arc

Bu belge, Azure Data Studio kullanarak Azure SQL yönetilen örneği yükleme-Azure yayı yükleme adımlarında size kılavuzluk eder

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="log-in-to-the-azure-arc-data-controller"></a>Azure Arc veri denetleyicisinde oturum açma

Bir örnek oluşturabilmeniz için önce henüz oturum açmadıysanız Azure Arc veri denetleyicisinde oturum açın.

```console
azdata login
```

Daha sonra, veri denetleyicisinin oluşturulduğu ad alanı, denetleyicide oturum açmak için Kullanıcı adı ve parola sorulur.  

> Ad alanını doğrulamanız gerekiyorsa, ```kubectl get pods -A``` kümedeki tüm ad alanlarının listesini almak için öğesini çalıştırabilirsiniz.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Azure Arc üzerinde Azure SQL yönetilen örneği oluşturma

- Azure Data Studio Başlat
- Bağlantılar sekmesinde, sol üstteki üç noktaya tıklayın ve "yeni dağıtım" seçeneğini belirleyin.
- Dağıtım seçenekleri ' nden **Azure SQL yönetilen örneği-Azure Arc** ' ı seçin. 
  > [!NOTE]
  > [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]Şu anda yüklü değilse, buraya yüklenmesi istenebilir.
- Gizlilik ve lisans koşullarını kabul edin ve en altta **Seç** ' e tıklayın.



- Azure SQL yönetilen örneği dağıt-Azure yay dikey penceresinde aşağıdaki bilgileri girin:
  - SQL Server örneği için bir ad girin
  - SQL Server örneği için bir parola girin ve onaylayın
  - Veri için uygun depolama sınıfını seçin
  - Günlüklere uygun depolama sınıfını seçin

- **Dağıt** düğmesine tıklayın

- Bu, veri denetleyicisinde Azure SQL yönetilen örneği-Azure Arc oluşturmayı başlatmalıdır.

- Birkaç dakika içinde, oluşturma işleminin başarıyla tamamlanmalıdır

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>Azure SQL yönetilen örneği 'ne bağlanma-Azure Data Studio 'den Azure Arc

- Veri denetleyicisi için ad alanı, Kullanıcı adı ve parola sağlayarak Azure Arc veri denetleyicisinde oturum açın: 
```console
azdata login
```

- Sağlanan tüm Azure SQL yönetilen örneklerini aşağıdaki komutları kullanarak görüntüleyin:

```console
azdata arc sql mi list
```

Çıktı aşağıdaki gibi görünmelidir, sunucu uç noktasını (bağlantı noktası numarası dahil) buradan kopyalayın.

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- Azure Data Studio, **Bağlantılar** sekmesinde, **sunucular** görünümündeki **Yeni bağlantıya** tıklayın.
- **Bağlantı** dikey penceresinde ServerEndPoint komutunu sunucu metin kutusuna yapıştırın
- Kimlik doğrulama türü olarak **SQL oturum açma** seçin
- Kullanıcı adı olarak *sa* girin
- Hesap için parolayı girin `sa`
- İsteğe bağlı olarak, bağlanmak için belirli bir veritabanı adı girin
- İsteğe bağlı olarak, yeni sunucu grubunu uygun şekilde seçin/ekleyin
- Azure SQL yönetilen örneğine bağlanmak için **Bağlan** ' ı seçin-Azure Arc




## <a name="next-steps"></a>Sonraki Adımlar

Şimdi [SQL örneğinizi izlemeye](monitor-grafana-kibana.md) çalışın
