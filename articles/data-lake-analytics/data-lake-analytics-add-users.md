---
title: Azure Data Lake Analytics hesaba Kullanıcı ekleme
description: Kullanıcı Ekleme Sihirbazı 'Nı ve Azure PowerShell kullanarak Data Lake Analytics hesabınıza nasıl doğru şekilde kullanıcı ekleneceğini öğrenin.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 16c503fe2d584d5f8256c65bfc49825b300f6a36
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672723"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Azure portalında kullanıcı ekleme

## <a name="start-the-add-user-wizard"></a>Kullanıcı Ekleme Sihirbazı 'Nı başlatın
1. Azure Data Lake Analytics https://portal.azure.com aracılığıyla açın.
2. **Kullanıcı Ekleme Sihirbazı**' na tıklayın.
3. **Kullanıcı Seç** adımında, eklemek istediğiniz kullanıcıyı bulun. Tıklayın **seçin**.
4. **Rol Seç** adımı **Data Lake Analytics geliştirici**' yi seçin. Bu rol, U-SQL işlerini göndermek/izlemek/yönetmek için gereken en düşük izin kümesini içerir. Grup, Azure hizmetlerini yönetmeye yönelik değilse, bu role atayın.
5. **Katalog Izinlerini seçin** adımında, kullanıcının erişmesi gereken diğer veritabanlarını seçin. İşleri göndermek için ana veritabanına okuma ve yazma erişimi gereklidir. İşiniz bittiğinde **Tamam**’a tıklayın.
6. **Seçili Izinleri ata** adlı son adımda, sihirbazın yapabileceği değişiklikleri gözden geçirin. **Tamam**'ı tıklatın.


## <a name="configure-acls-for-data-folders"></a>Veri klasörleri için ACL 'Leri yapılandırma
Giriş verilerini ve çıkış verilerini içeren klasörlerde gerektiğinde "R-X" veya "RWX" verin.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>İsteğe bağlı olarak, kullanıcıyı Azure Data Lake Storage 1. rol **okuyucu** rolüne ekleyin.
1.  Azure Data Lake Storage 1. hesabınızı bulun.
2.  **Kullanıcılar**' a tıklayın.
3. **Ekle**'yi tıklatın.
4.  Bu grubu atamak için bir Azure RBAC rolü seçin.
5.  Okuyucu rolüne ata. Bu rol, ADLSGen1 içinde depolanan verileri taramak/yönetmek için gereken en düşük izin kümesine sahiptir. Grup, Azure hizmetlerini yönetmeye yönelik değilse, bu role atayın.
6.  Grubun adını yazın.
7.  **Tamam**'ı tıklatın.

## <a name="adding-a-user-using-powershell"></a>PowerShell kullanarak Kullanıcı ekleme

1. Bu kılavuzdaki yönergeleri izleyin: [Azure PowerShell nasıl yüklenir ve yapılandırılır](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. [Add-AdlaJobUser. ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell betiğini indirin.
3. PowerShell betiğini çalıştırın. 

Kullanıcı erişimini iş göndermeye, yeni iş meta verilerini görüntülemeye ve eski meta verileri görüntülemeye yönelik örnek komut:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Lake Analytics genel bakış](data-lake-analytics-overview.md)
* [Azure portal kullanarak Data Lake Analytics kullanmaya başlayın](data-lake-analytics-get-started-portal.md)
* [Azure PowerShell kullanarak Azure Data Lake Analytics yönetme](data-lake-analytics-manage-use-powershell.md)

