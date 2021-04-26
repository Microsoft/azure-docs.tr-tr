---
title: 'Öğretici: blob depolamada statik bir Web sitesi barındırma-Azure depolama'
description: Statik Web sitesi barındırma için bir depolama hesabı yapılandırmayı ve Azure depolama 'ya statik bir Web sitesi dağıtmayı öğrenin.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 36cdaa813e0eccb23563301052aee268ab61533a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94888513"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Öğretici: blob depolamada statik bir Web sitesi barındırma

Bu öğreticide, Azure depolama 'ya statik bir Web sitesi derleyip dağıtmayı öğreneceksiniz. İşiniz bittiğinde, kullanıcıların herkese açık bir şekilde erişebileceği statik bir Web siteniz olur. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Statik Web sitesi barındırmayı yapılandırma
> * Merhaba Dünya web sitesi dağıtma

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> Genel amaçlı v2 standart depolama hesabı oluşturduğunuzdan emin olun. Statik Web siteleri diğer depolama hesabı türleri için kullanılamaz.

Bu öğretici, bir programcılar için ücretsiz bir araç olan [Visual Studio Code](https://code.visualstudio.com/download)kullanır ve statik Web sitesini oluşturup bir Azure depolama hesabına dağıtabilir.

Visual Studio Code yükledikten sonra Azure depolama önizleme uzantısını yükleyebilirsiniz. Bu uzantı, Visual Studio Code ile Azure depolama yönetim işlevlerini tümleştirir. Bu uzantıyı kullanarak, statik Web sitenizi Azure depolama 'ya dağıtabilirsiniz. Uzantıyı yüklemek için:

1. Visual Studio Code'u başlatın.
2. Araç çubuğunda **Uzantılar**' a tıklayın. *Azure Storage*' ı arayın ve listeden **Azure Storage** uzantısı ' nı seçin. Ardından, uzantıyı yüklemek için **Install** düğmesine tıklayın.

    ![Azure Storage uzantısını VS Code ' de yüklemesi](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Başlamak için [Azure Portal](https://portal.azure.com/) oturum açın.

## <a name="configure-static-website-hosting"></a>Statik Web sitesi barındırmayı yapılandırma

İlk adım, depolama hesabınızı Azure portal statik bir Web sitesi barındıracak şekilde yapılandırmaktır. Hesabınızı statik Web sitesi barındırma için yapılandırdığınızda, Azure Storage otomatik olarak *$Web* adlı bir kapsayıcı oluşturur. *$Web* kapsayıcı, statik Web siteniz için dosyaları içerecektir. 

1. [Azure Portal](https://portal.azure.com/) Web tarayıcınızda açın. 
1. Depolama hesabınızı bulun ve hesaba genel bakış ' ı görüntüleyin.
1. Statik Web siteleri için yapılandırma sayfasını göstermek üzere **statik Web sitesi** ' ni seçin.
1. Depolama hesabı için statik Web sitesi barındırmayı etkinleştirmek üzere **etkin** ' i seçin.
1. **Dizin belgesi adı** alanında, *index.html*' nin varsayılan dizin sayfasını belirtin. Varsayılan dizin sayfası, bir Kullanıcı statik Web sitenizin köküne gittiğinde görüntülenir.  
1. **Hata belgesi yolu** alanında, *404.html*' nin varsayılan hata sayfasını belirtin. Varsayılan hata sayfası, bir Kullanıcı statik Web sitenizde mevcut olmayan bir sayfaya gitmeye çalıştığında görüntülenir.
1. **Kaydet**’e tıklayın. Azure portal artık statik Web sitesi uç noktanızı görüntülüyor. 

    ![Depolama hesabı için statik Web sitesi barındırmayı etkinleştirme](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Merhaba Dünya web sitesi dağıtma

Sonra, Visual Studio Code sahip bir Merhaba Dünya Web sayfası oluşturun ve Azure depolama hesabınızda barındırılan statik Web sitesine dağıtın.

1. Yerel dosya sisteminizde *mywebsite* adlı boş bir klasör oluşturun. 
1. Visual Studio Code başlatın ve **Gezgin** panelinden yeni oluşturduğunuz klasörü açın.

    ![Klasörü Visual Studio Code açın](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. *Mywebsite* klasöründe varsayılan dizin dosyasını oluşturun ve *index.html* olarak adlandırın.

    ![Visual Studio Code varsayılan dizin dosyasını oluşturun](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Düzenleyicide *index.html* 'yi açın, dosyaya aşağıdaki metni yapıştırın ve kaydedin:

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>Hello World!</h1>
      </body>
    </html>
    ```

1. Varsayılan hata dosyasını oluşturun ve *404.html* olarak adlandırın.
1. Düzenleyicide *404.html* 'yi açın, dosyaya aşağıdaki metni yapıştırın ve kaydedin:

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>404</h1>
      </body>
    </html>
    ```

1. **Gezgin** panelinde *mywebsite* klasörü altına sağ tıklayın ve Web sitenizi dağıtmak Için **statik Web sitesine dağıt...** seçeneğini belirleyin. Aboneliklerin listesini almak için Azure 'da oturum açmanız istenir.

1. Statik Web sitesi barındırmayı etkinleştirdiğiniz depolama hesabını içeren aboneliği seçin. Sonra, istendiğinde depolama hesabını seçin.

Visual Studio Code artık dosyalarınızı Web uç noktanıza yükleyecek ve başarı durum çubuğunu gösterecektir. Azure 'da görüntülemek için Web sitesini başlatın.

Eğitmeni başarıyla tamamladınız ve Azure 'a bir statik Web sitesi dağıttınız.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, statik Web sitesi barındırma için Azure depolama hesabınızı yapılandırmayı ve bir Azure uç noktasına statik bir Web sitesi oluşturmayı ve dağıtmayı öğrendiniz.

Ardından, statik Web siteniz ile özel bir etki alanı yapılandırmayı öğrenin.

> [!div class="nextstepaction"]
> [Özel bir etki alanını Azure Blob depolama uç noktasıyla eşleme](storage-custom-domain-name.md)
