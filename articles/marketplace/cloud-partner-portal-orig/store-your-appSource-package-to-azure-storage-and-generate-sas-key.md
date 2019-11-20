---
title: AppSource paketinizi Azure depolama 'ya depolayın ve SAS anahtarıyla bir URL oluşturun
description: Bir AppSource paketini karşıya yüklemek ve korumak için gereken adımların ayrıntılarını alın.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pabutler
ms.openlocfilehash: 5f1a09244697a6771ad1b499f3d7c36eb7297067
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827663"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>AppSource paketinizi Azure depolama 'ya depolayın ve SAS anahtarıyla bir URL oluşturun
=============================================================================

Dosyalarınızın güvenliğini sağlamak için, tüm iş ortaklarının AppSource paket dosyalarını bir Azure Blob depolama hesabında depolaması ve bir SAS anahtarı kullanması gerekir. Sertifika için Azure Storage konumundan paket dosyasını alacak ve bunu AppSource denemeleri için kullanacak şekilde kullanacaksınız.

Paketinizi blob depolamaya yüklemek için aşağıdaki adımları kullanın:

1. <https://azure.microsoft.com> gidin ve ücretsiz bir deneme veya faturalandırılan hesap oluşturun.

2. [Azure Portal](https://portal.azure.com/)’da oturum açın.

3. **+ Yeni** ' ye tıklayıp **veri + depolama** hesabına giderek yeni bir depolama hesabı oluşturun.

   ![Microsoft Azure portalında veri + depolama dikey penceresi](media/CRMScreenShot7.png)

4. Bir **ad** ve **kaynak grubu** adı girin ve **Oluştur** düğmesine tıklayın.

   ![Microsoft Azure portalında depolama hesabı oluşturma](media/CRMScreenShot8.png)

5. Yeni oluşturduğunuz kaynak grubunuza gidin ve yeni bir blob kapsayıcısı oluşturun.

   ![Microsoft Azure portalını kullanarak paketi blob olarak karşıya yükleme](media/CRMScreenShot9.png)

6. Daha önce yapmadıysanız, Microsoft [Azure Depolama Gezgini](https://storageexplorer.com/)indirin ve yükleyin.

7. Depolama Gezgini açın ve simgesini kullanarak Azure depolama hesabınıza bağlanın.

8. Oluşturduğunuz blob kapsayıcısına gidin ve paket ZIP dosyanızı eklemek için **karşıya yükle** ' ye tıklayın.

   ![Microsoft Depolama Gezgini kullanarak paketi karşıya yükle](media/CRMScreenShot10.png)

9. Dosyanıza sağ tıklayıp **paylaşılan erişim Imzasını al**' ı seçin.

   ![Azure dosyasının paylaşılan erişim imzasını al](media/CRMScreenShot11.png)

10. SAS 'yi bir ay boyunca etkin hale getirmek için **süre sonu süresini** değiştirin, ardından **Oluştur**' a tıklayın.

    ![Bir Azure dosyasının SAS süre sonu tarihini değiştirme](media/CRMScreenShot12.png)

11. URL alanını kopyalayın ve daha sonra kaydetmek üzere kaydedin. İlişkili teklifi oluştururken bu URL 'YI girmeniz gerekecektir. 

    ![Bir Azure dosyasının SAS URL 'sini kopyalama](media/CRMScreenShot13.png)

