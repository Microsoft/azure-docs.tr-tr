---
title: Visual Studio ile depolama öykünücüsünü yapılandırma ve kullanma | Microsoft Docs
description: Yerel geliştirme makinenizde Azure 'da bulunan BLOB, kuyruk ve tablo depolama hizmetlerini taklit eden bir yardımcı program olan depolama öykünücüsünü yapılandırma ve kullanma.
services: visual-studio-online
author: ghogen
manager: jillfra
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: aca4bf5017a4ee23d69016b937673443c1a0e200
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73935843"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Visual Studio ile depolama öykünücüsünü yapılandırma ve kullanma
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Genel Bakış
Azure SDK geliştirme ortamı, yerel geliştirme makinenizde Azure 'da bulunan BLOB, kuyruk ve tablo depolama hizmetlerini taklit eden bir yardımcı program olan depolama öykünücüsünü içerir. Azure depolama hizmetlerini kullanan bir bulut hizmeti oluşturuyorsanız veya depolama hizmetlerini çağıran herhangi bir dış uygulama yazıyorsanız, kodunuzu depolama öykünücüsünde yerel olarak test edebilirsiniz. Microsoft Visual Studio için Azure Araçları, depolama öykünücüsünün yönetimini Visual Studio 'ya tümleştirmelidir. Azure araçları ilk kullanımda depolama öykünücü veritabanını başlatır, Visual Studio 'da kodunuzu çalıştırdığınızda veya hata ayıkladığınızda depolama öykünücü hizmetini başlatır ve Azure Depolama Gezgini aracılığıyla depolama öykünücü verilerine salt okuma erişimi sağlar.

Depolama öykünücüsü hakkında sistem gereksinimleri ve özel yapılandırma yönergeleri dahil ayrıntılı bilgiler için bkz. [geliştirme ve test Için Azure depolama öykünücüsünü kullanma](storage/common/storage-use-emulator.md).

> [!NOTE]
> Depolama öykünücüsü simülasyonu ve Azure depolama hizmetleri arasındaki işlevlerde bazı farklılıklar vardır. Belirli farklılıklar hakkında bilgi için Azure SDK belgelerindeki [depolama öykünücüsü Ile Azure depolama hizmetleri arasındaki farklara](storage/common/storage-use-emulator.md) bakın.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Depolama öykünücüsü için bağlantı dizesi yapılandırma
Bir rol içindeki koddan depolama öykünücüye erişmek için, depolama öykünücüsüne işaret eden ve daha sonra bir Azure depolama hesabına işaret edecek şekilde değiştirilebilen bir bağlantı dizesi yapılandırmak isteyeceksiniz. Bağlantı dizesi, rolünüzün bir depolama hesabına bağlanmak için çalışma zamanında okuyamayacağı bir yapılandırma ayarıdır. Bağlantı dizeleri oluşturma hakkında daha fazla bilgi için bkz. [Azure Storage bağlantı dizelerini yapılandırma](/azure/storage/common/storage-configure-connection-string).

> [!NOTE]
> **Developmentstorageaccount** özelliğini kullanarak, kodunuzda depolama öykünücü hesabına bir başvuru döndürebilirsiniz. Bu yaklaşım, kodunuzun depolama öykünücüsüyle erişmek istiyorsanız doğru çalışır, ancak uygulamanızı Azure 'da yayımlamayı planlıyorsanız, Azure depolama hesabınıza erişmek için bir bağlantı dizesi oluşturmanız ve kodunuzu bu bağlantıyı kullanacak şekilde değiştirmeniz gerekir yayımlamadan önce dize. Depolama öykünücü hesabı ve bir Azure depolama hesabı arasında sık sık geçiş yapıyorsanız, bir bağlantı dizesi bu işlemi basitleştirir.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>Depolama öykünücüsünü başlatma ve çalıştırma
Visual Studio 'da hizmetinizi çalıştırdığınız veya hata ayıkladığınızda, Visual Studio 'Nun depolama öykünücüsünü otomatik olarak başlattığında emin olabilirsiniz. Çözüm Gezgini ' de, **Azure** projeniz için kısayol menüsünü açın ve **Özellikler**' i seçin. **Geliştirme** sekmesinde, **Azure depolama öykünücüsü** ' nü Başlat listesinde, **doğru** (zaten bu değere ayarlanmamışsa) seçeneğini belirleyin.

Visual Studio 'da hizmetinize ilk kez çalıştırdığınızda veya hata ayıkladığınızda, depolama öykünücüsü bir başlatma işlemi başlatır. Bu işlem, depolama öykünücüsü için yerel bağlantı noktalarını ayırır ve depolama öykünücü veritabanını oluşturur. İşlem tamamlandıktan sonra, depolama öykünücü veritabanı silinmediği sürece bu işlemin yeniden çalıştırılması gerekmez.

> [!NOTE]
> Azure araçlarının Haziran 2012 sürümü ile başlayarak, depolama öykünücü, varsayılan olarak SQL Express LocalDB 'de çalışır. Azure araçlarının önceki sürümlerinde, depolama öykünücü, Azure SDK 'yı yükleyebilmeniz için önce yüklenmesi gereken varsayılan bir SQL Express 2005 veya 2008 örneğine göre çalışır. Depolama öykünücüsünü SQL Express 'in adlandırılmış bir örneğine veya Microsoft SQL Server adlandırılmış ya da varsayılan örneğine karşı çalıştırabilirsiniz. Depolama öykünücüsünü varsayılan örnek dışında bir örneğe karşı çalışacak şekilde yapılandırmanız gerekiyorsa bkz. [geliştirme ve test Için Azure depolama öykünücüsünü kullanma](storage/common/storage-use-emulator.md).
> 
> 

Depolama öykünücüsü, yerel depolama hizmetlerinin durumunu görüntülemek ve bunları başlatmak, durdurmak ve sıfırlamak için bir kullanıcı arabirimi sağlar. Depolama öykünücüsü hizmeti başlatıldıktan sonra, Windows görev çubuğundaki Microsoft Azure Öykünücüsü bildirim alanı simgesine sağ tıklayarak Kullanıcı arabirimini görüntüleyebilir veya hizmeti başlatabilir veya durdurabilirsiniz.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Sunucu Gezgini 'de depolama öykünücü verilerini görüntüleme
Sunucu Gezgini ' deki Azure depolama düğümü, depolama Emulator da dahil olmak üzere depolama hesaplarınızdaki blob ve tablo verileri için verileri görüntülemenize ve ayarları değiştirmenize olanak sağlar. Daha fazla bilgi için bkz. [Depolama Gezgini Ile Azure Blob depolama kaynaklarını yönetme](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) .

