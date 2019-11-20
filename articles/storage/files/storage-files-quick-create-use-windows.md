---
title: Azure hızlı başlangıç-Windows VM 'lerinde Azure dosya paylaşma oluşturma ve kullanma | Microsoft Docs
description: Bu hızlı başlangıçta Azure portal bir Azure dosya paylaşımından kurulum ve bunu bir Windows sanal makinesine bağlama. Dosya paylaşımıyla bağlantı kurmak için dosyalar dosyasına bir dosya yükleyin. Ardından dosyalar paylaşımının anlık görüntüsünü alır, dosya paylaşımındaki dosyayı değiştirebilir ve dosya paylaşımının önceki bir anlık görüntüsünü geri yükleyebilirsiniz.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 95a5df4ca14f4be8553c83dfe8b4d09d00341102
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260390"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Hızlı Başlangıç: Windows sanal makineleri ile Azure dosyaları paylaşma oluşturma ve yönetme

Makalesinde, bir Azure dosya paylaşımının oluşturulmasına ve kullanılmasına yönelik temel adımlar gösterilmektedir. Bu hızlı başlangıçta, bu vurgu hızlı bir şekilde Azure dosya paylaşımının kurulması ve bu sayede hizmetin nasıl çalıştığı ile karşılaşabilirsiniz. Azure dosya paylaşımlarını kendi ortamınızda oluşturmaya ve kullanmaya yönelik daha ayrıntılı yönergeler gerekiyorsa bkz. [Windows Ile Azure dosya paylaşımı kullanma](storage-how-to-use-files-windows.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure Portal](https://portal.azure.com) oturum açın.

## <a name="prepare-your-environment"></a>Ortamınızı hazırlama

Bu hızlı başlangıçta, aşağıdaki öğeleri ayarlarsınız:

- Azure depolama hesabı ve Azure dosya paylaşma
- Bir Windows Server 2016 Datacenter VM

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Bir Azure dosya paylaşımıyla çalışabilmeniz için önce bir Azure depolama hesabı oluşturmanız gerekir. Genel amaçlı v2 depolama hesabı, tüm Azure depolama hizmetlerine erişim sağlar: Bloblar, dosyalar, kuyruklar ve tablolar. Hızlı başlangıç, genel amaçlı v2 depolama hesabı oluşturur, ancak herhangi bir depolama hesabı türü oluşturma adımları benzerdir. Bir depolama hesabında sınırsız sayıda paylaşım olabilir. Bir paylaşım, depolama hesabının kapasite limitlerine kadar sınırsız sayıda dosyayı depolayabilir.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Azure dosya paylaşımı oluşturma

Sonra, bir dosya paylaşımı oluşturacaksınız.

1. Azure depolama hesabı dağıtımı tamamlandığında **Kaynağa Git**' i seçin.
1. Depolama hesabı bölmesinden **dosyalar** ' ı seçin.

    ![Dosya Seç](./media/storage-files-quick-create-use-windows/click-files.png)

1. **Dosya paylaşma**' yı seçin.

    ![Dosya paylaşma Ekle düğmesini seçin](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Yeni dosya paylaşımı *qsfileshare* ' ı adlandırın > **Kota** için "1" girin > **Oluştur**' u seçin. Kota en fazla 5 TiB olabilir, ancak bu hızlı başlangıç için yalnızca 1 GiB gerekir.
1. Yerel makinenizde *Qstestfile* adlı yeni bir txt dosyası oluşturun.
1. Yeni dosya payını seçin, sonra dosya paylaşma konumunda **karşıya yükle**' yi seçin.

    ![Karşıya dosya yükle](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. . Txt dosyanızı oluşturduğunuz konuma gidin > *Qstestfile. txt* ' yi seçin > **karşıya yükle**' yi seçin.

Şimdiye kadar, Azure 'da bir Azure depolama hesabı ve bu dosyada bir dosya paylaşma oluşturdunuz. Ardından, bu hızlı başlangıçta şirket içi sunucuyu göstermek için Windows Server 2016 Datacenter ile Azure VM oluşturacaksınız.

### <a name="deploy-a-vm"></a>VM'yi dağıtma

1. Ardından, portalın sol tarafındaki menüyü genişletin ve Azure portalın sol üst köşesindeki **Kaynak oluştur**’u seçin.
1. **Azure Market** kaynaklarının listesi üzerindeki arama kutusunda, **Windows Server 2016 Datacenter**’ı arayıp seçin, ardından **Oluştur**’u seçin.
1. **Temel bilgiler** sekmesinde, **proje ayrıntıları**' nın altında, bu hızlı başlangıç için oluşturduğunuz kaynak grubunu seçin.

   ![Portal dikey penceresinde VM’niz ile ilgili temel bilgileri girin](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. **Örnek ayrıntıları**' nın altında, VM *qsvm*'yi adlandırın.
1. **Bölge**, **Kullanılabilirlik seçenekleri**, **Görüntü** ve **Boyut** için varsayılan ayarları değiştirmeden bırakın.
1. **Yönetici hesabı**altında *Vmadmin* **Kullanıcı adı** olarak ekleyin ve VM için bir **parola** girin.
1. **Gelen bağlantı noktası kuralları** altında **Seçilen bağlantı noktalarına izin ver**'i, sonra aşağı açılan listeden **RDP (3389)** ve **HTTP** değerlerini seçin.
1. **İncele ve oluştur**’u seçin.
1. **Oluştur**’u seçin. Yeni bir sanal makinenin oluşturulması birkaç dakika sürebilir.

1. VM dağıtımınız tamamlandıktan sonra **Kaynağa Git**' i seçin.

Bu noktada yeni bir sanal makine oluşturdunuz ve bir veri diskini kullanıma açtınız. Şimdi VM’ye bağlanmanız gerekir.

### <a name="connect-to-your-vm"></a>Sanal makinenize bağlanma

1. Sanal makine özellikleri sayfasında **Bağlan** ' ı seçin.

   ![Portaldan bir Azure sanal makinesine bağlanma](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. **Sanal makineye bağlan** sayfasında, **ıp adresine** göre *3389* **numaralı bağlantı noktası** üzerinden bağlanmak için varsayılan seçenekleri tutun ve **RDP dosyasını indir**' i seçin.
1. İndirilen RDP dosyasını açın ve istendiğinde **Bağlan** ' ı seçin.
1. **Windows Güvenliği** penceresinde **Diğer seçenekler**'i ve ardından **Başka bir hesap kullanın**'ı seçin. Kullanıcı adını, sanalmakine için oluşturduğunuz &lt;VM&gt; Yönetici Kullanıcı adı olan localhost\username olarak yazın. Sanal makine için oluşturduğunuz parolayı girin ve ardından **Tamam**' ı seçin.

   ![Diğer seçenekler](./media/storage-files-quick-create-use-windows/local-host2.png)

1. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. bağlantıyı oluşturmak için **Evet** ' i veya **devam et** ' i seçin.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Azure dosya paylaşımından bir Windows sürücüsüyle eşleme

1. Azure portal *qsfileshare* FileShare öğesine gidin ve **Bağlan**' ı seçin.
1. İkinci kutunun içeriğini kopyalayıp **Not defteri**'ne yapıştırın.

   ![Azure Dosyaları Bağlan bölmesinden UNC adı](./media/storage-files-quick-create-use-windows/portal_netuse_connect2.png)

1. VM 'de **Dosya Gezgini** 'ni açın ve pencerede **Bu bilgisayarı** seçin. Bu seçim Şeritteki kullanılabilir menüleri değiştirecek. **Bilgisayar** menüsünde, **Ağ Sürücüsüne Bağlan**' ı seçin.
1. Sürücü harfini seçin ve UNC adını girin. Bu hızlı başlangıçta adlandırma önerilerini izlediyseniz, **Not defteri**'nden  *\\qsstorageacct. File. Core. Windows. net\qsfileshare* ' yi kopyalayın.

   Her iki onay kutusu işaretli olduğundan emin olun.

   ![“Ağ Sürücüsüne Bağlan” iletişim kutusunun ekran görüntüsü](./media/storage-files-quick-create-use-windows/mountonwindows10.png)

1. **Son**’u seçin.
1. **Windows güvenliği** iletişim kutusunda:

   - Not defteri ' nden, depolama hesabı adı ' nı AZURE \ ' a kopyalayın ve Kullanıcı adı olarak **Windows güvenliği** iletişim kutusuna yapıştırın. Bu hızlı başlangıçta adlandırma önerilerini izlediyseniz, *AZURE\qsstorageacct*kopyalayın.
   - Not defteri 'nde, depolama hesabı anahtarını kopyalayın ve parola olarak **Windows güvenliği** iletişim kutusuna yapıştırın.

      ![Azure Dosyaları Bağlan bölmesinden UNC adı](./media/storage-files-quick-create-use-windows/portal_netuse_connect3.png)

## <a name="create-a-share-snapshot"></a>Paylaşma anlık görüntüsü oluşturma

Sürücüyü eşleştirdikten sonra bir anlık görüntü oluşturabilirsiniz.

1. Portalda dosya paylaşımınıza gidin ve **anlık görüntü oluştur**' u seçin.

   ![Anlık görüntü oluşturma](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. VM 'de *qstestfile. txt* dosyasını açın ve "Bu dosya değiştirilmiştir" yazın > dosyayı kaydedin ve kapatın.
1. Başka bir anlık görüntü oluşturun.

## <a name="browse-a-share-snapshot"></a>Bir paylaşma anlık görüntüsüne gözatın

1. Dosya paylaşımınızda **anlık görüntüleri görüntüle**' yi seçin.
1. **Dosya paylaşma anlık görüntüleri** bölmesinde listedeki ilk anlık görüntüyü seçin.

   ![Zaman damgaları listesinde seçilen anlık görüntü](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. Bu anlık görüntünün bölmesinde *Qstestfile. txt*' yi seçin.

## <a name="restore-from-a-snapshot"></a>Anlık görüntüden geri yükleme

1. Dosya paylaşma anlık görüntüsü dikey penceresinde *Qstestfile*öğesine sağ tıklayın ve **geri yükle** düğmesini seçin.
1. **Özgün dosyanın üzerine yaz**seçeneğini belirleyin.

   ![Düğmeleri indir ve geri yükle](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. VM 'de dosyasını açın. Değiştirilmemiş sürüm geri yüklendi.

## <a name="delete-a-share-snapshot"></a>Paylaşım anlık görüntüsünü silme

1. Dosya paylaşımınızda **anlık görüntüleri görüntüle**' yi seçin.
1. **Dosya paylaşma anlık görüntüleri** bölmesinde, listedeki son anlık görüntüyü seçin ve **Sil**' e tıklayın.

   ![Sil düğmesi](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Windows 'da bir paylaşma anlık görüntüsü kullanma

Şirket içi VSS anlık görüntüleriyle tıpkı olduğu gibi, önceki sürümler sekmesini kullanarak bağlı Azure dosya paylaşımınızdan anlık görüntüleri de görüntüleyebilirsiniz.

1. Dosya Gezgini 'nde, bağlanan paylaşımın yerini bulun.

   ![Dosya Gezgini 'nde bağlı olan paylaşımda](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. *Qstestfile. txt* ' yi seçin ve > sağ tıklayıp menüden **Özellikler** ' i seçin.

   ![Seçilen dizin için sağ tıklama menüsü](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Bu dizine ait paylaşım anlık görüntülerinin listesini görmek için **Önceki Sürümler**'i seçin.

1. Anlık görüntüyü açmak için **Aç** ' ı seçin.

   ![Önceki Sürümler sekmesi](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Önceki sürümü geri yükleme

1. **Geri yükle**' yi seçin. Bu eylem, paylaşımın anlık görüntüsünün oluşturulduğu sırada tüm dizinin içeriğini özyinelemeli olarak özgün konuma kopyalar.

   ![Uyarı iletisindeki Geri Yükle düğmesi](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure dosya paylaşımını Windows'da kullanma](storage-how-to-use-files-windows.md)
