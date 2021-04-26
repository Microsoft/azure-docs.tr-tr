---
title: Azure portal ile Azure dosya paylaşımlarını yönetme hızlı başlangıcı
description: Azure portal Azure dosya paylaşımlarını oluşturma ve yönetme hakkında bilgi için bkz.. Bir depolama hesabı oluşturun, bir Azure dosya paylaşma oluşturun ve Azure dosya paylaşımınızı kullanın.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d7867a4bd60bb179c18fff49691072d683660f7e
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717879"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>Hızlı Başlangıç: Azure portal ile Azure dosya paylaşımları oluşturma ve yönetme 
[Azure Dosyaları](storage-files-introduction.md), Microsoft’un kullanımı kolay bulut dosya sistemidir. Azure dosya paylaşımları, Windows, Linux ve macOS platformlarına bağlanabilir. Bu kılavuzda, [Azure portalını](https://portal.azure.com/) kullanarak Azure dosya paylaşımlarıyla çalışmanın temel bilgileri gösterilmektedir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Azure dosya paylaşımı oluşturma
Azure dosya paylaşımı oluşturmak için:

1. Panonuzdan depolama hesabını seçin.
1. Depolama hesabı sayfasında, **Hizmetler** bölümünden **Dosyalar**’ı seçin.
    
    ![Depolama hesabının veri depolama bölümünün ekran görüntüsü; dosya paylaşımları ' nı seçin.](media/storage-how-to-use-files-portal/create-file-share-1.png)

1. **Dosya hizmeti** sayfasının en üstündeki menüde **dosya paylaşma**' ya tıklayın. **Yeni dosya paylaşımı** sayfası aşağı doğru açılır.
1. **Ad** türü *myshare*' da, bir alıntı girin ve **Katmanlar** için **işlem için iyileştirilmiş** ' ı seçili bırakın.
1. Azure dosya paylaşımından oluşturmak için **Oluştur** ' u seçin.

Paylaşım adları tümüyle küçük harf, sayı ve tek kısa çizgilerden oluşmalı ve kısa çizgiyle başlamamalıdır. Dosya paylaşımlarını ve dosyaları adlandırma hakkında tüm ayrıntılar için bkz. [adlandırma ve başvuru paylaşımları, dizinler, dosyalar ve meta veriler](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Azure dosya paylaşımınızı kullanma
Azure dosyaları, Azure dosya paylaşımınızdaki dosya ve klasörlerle çalışan üç yöntem sağlar: endüstri standardı [sunucu Ileti bloğu (SMB) protokolü](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview), ağ dosya SISTEMI (NFS) Protokolü (Önizleme) ve [Dosya REST Protokolü](/rest/api/storageservices/file-service-rest-api). 

Bir dosya paylaşımını SMB ile bağlayabilmeniz için işletim sisteminize göre aşağıdaki belgeye bakın:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Azure portalda Azure dosya paylaşımını kullanma
Azure portalı üzerinde gelen tüm istekler Dosya REST API ile yapılır; böylelikle SMB erişimi olmadan istemcilerdeki dosyaları ve dizinleri oluşturabilir, değiştirebilir ve silebilirsiniz. Dosya REST protokolüyle doğrudan çalışmak mümkündür (yani, el ile REST HTTP çağrılarını kendiniz), ancak dosya REST protokolünü kullanmak için en yaygın yol (Azure portal kullanmanın ötesinde), hepsi, seçtiğiniz komut dosyası/programlama dilinde Dosya REST Protokolü etrafında iyi bir sarmalayıcı sağlayan [Azure PowerShell modülünü](storage-how-to-use-files-powershell.md), [Azure CLI](storage-how-to-use-files-cli.md)'Yi veya bir Azure depolama SDK 'sını kullanmaktır. 

Azure dosyalarının çoğu kullanıcının SMB protokolü üzerinden Azure dosya paylaşımlarıyla birlikte çalışmak istediğini umuz, bu sayede bunların kullanabilmesini bekledikleri mevcut uygulamaları ve araçları kullanmasına izin veriyor, ancak SMB yerine dosya REST API kullanmak için avantajlı olmasının çeşitli nedenleri vardır, örneğin:

- SMB erişiminizin olmadığı bir dizüstü bilgisayar, tablet veya mobil cihaz vb. ile hareket halindeyken Azure dosya paylaşımınızda hızlı bir değişiklik yapmanız gerekir.
- SMB paylaşımını bağlayamayan istemcilerden; örneğin 445 numaralı bağlantı noktası engeli kaldırılmamış şirket içi bir istemciden bir betik veya uygulama yürütmeniz gerekiyorsa.
- [Azure İşlevleri](../../azure-functions/functions-overview.md) gibi sunucusuz kaynaklardan yararlanıyorsanız. 

Aşağıdaki örnekler, Azure portalın Azure dosya paylaşımınızı Dosya REST protokolü ile yönetmek için nasıl kullanılacağını göstermektedir. 

Artık Azure dosya paylaşımını oluşturduğunuza göre, [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) veya [macOS](storage-how-to-use-files-mac.md)'ta SMB ile dosya paylaşımını bağlayabilirsiniz. Alternatif olarak, Azure dosya paylaşımınızla Azure portalını kullanarak çalışabilirsiniz. 

#### <a name="create-a-directory"></a>Dizin oluşturma
Azure dosya paylaşımınızın kökünde *myDirectory* adlı yeni bir dizin oluşturmak için:

1. **Dosya Hizmeti** sayfasında **myshare** dosya paylaşımını seçin. Dosya paylaşımınızın sayfası açılır.
1. Sayfanın en üstündeki menüden **+ Dizin ekle**’yi seçin. **Yeni dizin** sayfası aşağı doğru açılır.
1. *myDirectory* yazın ve **Tamam**’a tıklayın.

#### <a name="upload-a-file"></a>Dosyayı karşıya yükleme 
Bir dosyayı karşıya yüklemeyi göstermek için önce karşıya yüklenecek bir dosya oluşturmanız veya seçmeniz gerekir. Uygun gördüğünüz herhangi bir yolla bunu yapabilirsiniz. Karşıya yüklemek istediğiniz dosyayı seçtikten sonra:

1. **myDirectory** dizinine tıklayın. **myDirectory** paneli açılır.
1. En üstteki menüde **Karşıya Yükle**'yi seçin. **Dosyaları karşıya yükleme** paneli açılır.  
    
    ![Dosyaları karşıya yükleme panelinin ekran görüntüsü](media/storage-how-to-use-files-portal/upload-file-1.png)

1. Yerel dosyalarınıza göz atmak amacıyla bir pencereyi açmak için klasör simgesine tıklayın. 
1. Bir dosya seçin ve **Aç**’a tıklayın. 
1. **Dosyaları karşıya yükleme** sayfasında, dosya adını doğrulayın ve **Karşıya Yükle**’ye tıklayın.
1. Tamamlandığında, dosyanın **myDirectory** sayfasındaki listede gösterilmesi gerekir.

#### <a name="download-a-file"></a>Dosya indirme
Dosyaya sağ tıklayarak, karşıya yüklediğiniz dosyanın bir kopyasını indirebilirsiniz. İndir düğmesine tıklandıktan sonra olacak işlemler, kullandığınız işletim sistemine ve tarayıcıya bağlıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Dosyalar nedir?](storage-files-introduction.md)