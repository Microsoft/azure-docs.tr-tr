---
title: Azure Depolama Gezgini destek yaşam döngüsü | Microsoft Docs
description: Azure Depolama Gezgini için destek ilkesine ve yaşam döngüsüne genel bakış
services: storage
author: MRayermannMSFT
manager: jinglouMSFT
ms.service: storage
ms.topic: article
ms.date: 07/10/2020
ms.author: marayerm
ms.openlocfilehash: dc89631208c6a72c4b48d49260f584149d6c4260
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97679947"
---
# <a name="azure-storage-explorer-support-lifecycle-and-policy"></a>Azure Depolama Gezgini destek yaşam döngüsü ve ilke

Bu belge Azure Depolama Gezgini için destek yaşam döngüsünü ve ilkesini içerir.

## <a name="update-schedule-and-process"></a>Zamanlamayı ve işlemi Güncelleştir

Azure Depolama Gezgini yılda dört ile altı kez serbest bırakılır. Ayrıca, kritik sorunlara yönelik düzeltmeleri yayınlamak için Microsoft bu zamanlamayı atlayabilir.

Depolama Gezgini her saat güncelleştirmeleri denetler ve kullanılabilir olduğunda onları indirir. Ancak, Kullanıcı kabulü, yüklemeyi başlatmak için gereklidir. Kullanıcılar farklı bir zamanda güncelleştirmeyi tercih ederseniz, güncelleştirmeleri el ile denetleyebilir. Windows ve Linux 'ta kullanıcılar, **Yardım** menüsünde **Güncelleştirmeleri denetle** ' yi seçerek güncelleştirmeleri kontrol edebilir. MacOS 'ta, **uygulama menüsünde** **Güncelleştirmeleri denetle** ' nin altında bulunabilir. Kullanıcılar ayrıca en son sürümü indirmek ve yüklemek için [Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) indirme sayfasına doğrudan gidebilir.

Depolama Gezgini en son sürümlerini her zaman kullanmanızı kesinlikle öneririz. Bir sorun Depolama Gezgini en son sürümüne güncelleştirmenizi engelliyorsa [GitHub](https://github.com/microsoft/AzureStorageExplorer)' da bir sorun açın.

## <a name="support-lifecycle"></a>Destek yaşam döngüsü

Depolama Gezgini [modern yaşam döngüsü ilkesine](https://support.microsoft.com/help/30881/modern-lifecycle-policy)tabidir. Kullanıcıların Depolama Gezgini yüklemelerini güncel tutmalarına sahip olması beklenmektedir. Güncel kalarak, kullanıcıların en son özellikler, performans geliştirmeleri, güvenlik ve hizmet güvenilirliği olmasını sağlar.

Sürüm 1.14.1 'den başlayarak, 12 aydan daha eski olan tüm Depolama Gezgini sürümler destek dışında kabul edilir. 1.14.1 öncesindeki tüm yayınlar 14 Temmuz 2021 tarihinden itibaren destek dışında kabul edilecek. Destek dışı olan sürümlerin artık tam olarak tasarlanan ve beklenen şekilde çalışması garanti edilmez. Tüm yayınların, yayımlanma tarihinin ve destek tarihinin son tarihleri için bkz. [yayınlar](#releases).

Sürüm 1.13.0 ile başlayarak, bir sürüm yaklaşık bir ayda, destek dışında bir ay sonra bir uygulama içi uyarı görüntülenir. Uyarı, kullanıcıların Depolama Gezgini en son sürümüne güncelleştirmesini teşvik eder. Bir sürüm destek dışı olduktan sonra her bir başlangıç üzerinde uygulama içi uyarı görüntülenir.

## <a name="releases"></a>Yayınlar

Bu tabloda, Azure Depolama Gezgini her sürümü için sürüm tarihi ve destek bitiş tarihi açıklanmaktadır.

| Depolama Gezgini sürümü  | Sürüm tarihi       | Destek tarihi sonu |
|:-------------------------:|:------------------:|:-------------------:|
| v 1.17.0                   | 15 Aralık 2020  | 15 Aralık 2021   |
| v 1.16.0                   | 10 Kasım 2020  | 10 Kasım 2021   |
| v 1.15.1                   | 2 Eylül 2020  | 2 Eylül 2021   |
| v 1.15.0                   | 27 Ağustos 2020    | 27 Ağustos 2021     |
| v 1.14.2                   | 16 Temmuz 2020      | 16 Temmuz 2021       |
| v 1.14.1                   | 14 Temmuz 2020      | 14 Temmuz 2021       |
| v 1.14.0                   | 24 Haziran 2020      | 14 Temmuz 2021       |
| v 1.13.1                   | 18 Mayıs 2020       | 14 Temmuz 2021       |
| v 1.13.0                   | 1 Mayıs 2020        | 14 Temmuz 2021       |
| v 1.12.0                   | 16 Ocak 2020   | 14 Temmuz 2021       |
| v 1.11.2                   | 17 Aralık 2019  | 14 Temmuz 2021       |
| v 1.11.1                   | 20 Kasım 2019  | 14 Temmuz 2021       |
| v 1.11.0                   | 4 Kasım 2019   | 14 Temmuz 2021       |
| v 1.10.1                   | 19 Eylül 2019 | 14 Temmuz 2021       |
| v 1.10.0                   | 12 Eylül 2019 | 14 Temmuz 2021       |
| v 1.9.0                    | 1 Temmuz 2019       | 14 Temmuz 2021       |
| v 1.8.1                    | 10 Mayıs 2019       | 14 Temmuz 2021       |
| v 1.8.0                    | 2 Mayıs 2019        | 14 Temmuz 2021       |
| v 1.7.0                    | 5 Mart 2019      | 14 Temmuz 2021       |
| v 1.6.2                    | 8 Ocak 2019    | 14 Temmuz 2021       |
| v 1.6.1                    | 18 Aralık 2018  | 14 Temmuz 2021       |
| v 1.6.0                    | 4 Aralık 2018   | 14 Temmuz 2021       |
| v 1.5.0                    | 29 Ekim 2018   | 14 Temmuz 2021       |
| v 1.4.4                    | 15 Ekim 2018   | 14 Temmuz 2021       |
| v 1.4.2                    | Eylül 24, 2018 | 14 Temmuz 2021       |
| v 1.4.1                    | 28 Ağustos 2018    | 14 Temmuz 2021       |
| v 1.3.1                    | 11 Temmuz 2018      | 14 Temmuz 2021       |
| v 1.2.0                    | 12 Haziran 2018      | 14 Temmuz 2021       |
| v 1.1.0                    | 9 Mayıs 2018        | 14 Temmuz 2021       |
| v 1.0.0 (ve üzeri)        | 16 Nisan 2018     | 14 Temmuz 2021       |
