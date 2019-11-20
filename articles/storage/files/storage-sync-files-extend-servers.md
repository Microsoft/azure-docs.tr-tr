---
title: Öğretici-Windows dosya sunucularını Azure Dosya Eşitleme genişletme | Microsoft Docs
description: Baştan sona Azure Dosya Eşitleme Windows dosya sunucularını genişletmeyi öğrenin.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f71a27ea4da6bce5832287e948e0731672280196
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699498"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Öğretici: Windows dosya sunucularını Azure Dosya Eşitleme ile genişletme

Makalesinde, Azure Dosya Eşitleme kullanarak bir Windows Server 'ın depolama kapasitesini genişletmeye yönelik temel adımlar gösterilmektedir. Öğretici, Windows Server 'ı bir Azure sanal makinesi (VM) olarak sunmakla birlikte, bu işlemi genellikle şirket içi sunucularınız için de yapmanız gerekir. [Dağıtım Azure dosya eşitleme](storage-sync-files-deployment-guide.md) makalesinde kendi ortamınızda Azure dosya eşitleme dağıtmaya yönelik yönergeleri bulabilirsiniz.

> [!div class="checklist"]
> * Depolama Eşitleme Hizmetini Dağıtma
> * Windows Server’ı Azure Dosya Eşitleme ile kullanmaya hazırlama
> * Azure Dosya Eşitleme aracısını yükleme
> * Windows Server 'ı depolama eşitleme hizmeti ile kaydetme
> * Bir eşitleme grubu ve bir bulut uç noktası oluşturma
> * Sunucu uç noktası oluşturma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure Portal](https://portal.azure.com) oturum açın.

## <a name="prepare-your-environment"></a>Ortamınızı hazırlama

Bu öğreticide, Azure Dosya Eşitleme dağıtabilmeniz için önce aşağıdakileri yapmanız gerekir:

- Azure depolama hesabı ve dosya paylaşma oluşturma
- Windows Server 2016 Datacenter VM ayarlama
- Windows Server VM 'sini Azure Dosya Eşitleme için hazırlama

### <a name="create-a-folder-and-txt-file"></a>Klasör ve .txt dosyası oluşturma

Yerel bilgisayarınızda _EşitlenecekDosyalar_ adlı yeni bir klasör oluşturun ve _testbelgem.txt_ adlı bir metin dosyası ekleyin. Bu öğreticinin ilerleyen kısımlarında bu dosyayı dosya paylaşımında karşıya yüklersiniz.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Dosya paylaşımı oluşturma

Bir Azure depolama hesabı dağıttıktan sonra bir dosya paylaşma oluşturursunuz.

1. Azure portal **Kaynağa Git**' i seçin.
1. Depolama hesabı bölmesinden **dosyalar** ' ı seçin.

    ![Dosya Seç](./media/storage-sync-files-extend-servers/click-files.png)

1. **+ Dosya paylaşma**' yı seçin.

    ![Dosya paylaşma Ekle düğmesini seçin](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Yeni dosya paylaşımı _afsfileshare_olarak adlandırın. **Kota**için "1" girin ve ardından **Oluştur**' u seçin. Kota en fazla 5 TiB olabilir, ancak bu öğretici için yalnızca 1 GB gerekir.

    ![Yeni dosya paylaşımı için bir ad ve kota belirtin](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Yeni dosya payını seçin. Dosya paylaşma konumunda **karşıya yükle**' yi seçin.

    ![Karşıya dosya yükle](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. . Txt dosyanızı oluşturduğunuz _Filestosync_ klasörüne gidin, _mytestdoc. txt_ dosyasını seçin ve **karşıya yükle**' yi seçin.

    ![Dosya paylaşımına göz atın](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Bu noktada, bir depolama hesabı ve dosyada bir dosya paylaşma oluşturdunuz. Ardından, bu öğreticide şirket içi sunucuyu temsil etmek üzere Windows Server 2016 Datacenter ile bir Azure VM 'yi dağıtırsınız.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>VM dağıtma ve veri diskini kullanıma açma

1. Azure portal gidin ve soldaki menüyü genişletin. Sol üst köşedeki **kaynak oluştur ' a** tıklayın.
1. **Azure Marketi** kaynakları listesinin üzerindeki arama kutusunda **Windows Server 2016 veri merkezini** arayın ve sonuçlarda seçin. **Oluştur**’u seçin.
1. **Temel bilgiler** sekmesine gidin. **Proje ayrıntıları**' nın altında, bu öğretici için oluşturduğunuz kaynak grubunu seçin.

   ![Portal dikey penceresinde VM 'niz hakkındaki temel bilgileri girin](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. **Örnek ayrıntıları**' nın altında bir VM adı belirtin. Örneğin, _Myvm_' yi kullanın.
1. **Bölge**, **kullanılabilirlik seçenekleri**, **görüntü**ve **Boyut**için varsayılan ayarları değiştirmeyin.
1. **Yönetici hesabı** altında VM için bir **Kullanıcı adı** ve **Parola** girin.
1. **Gelen bağlantı noktası kuralları**altında **Seçili bağlantı noktalarına izin ver** ' i seçin ve ardından açılan menüden **RDP (3389)** ve **http** ' yi seçin.

1. VM’yi oluşturmadan önce bir veri diski oluşturmanız gerekir.

   1. **İleri ' yi seçin: diskler**.

      ![Veri diski ekleme](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. **Diskler** sekmesinde, **Disk seçenekleri**altında, varsayılan değerleri bırakın.
   1. **VERI diskleri**altında **Oluştur ve yeni bir disk Ekle**' yi seçin.

   1. Bu öğretici için **1 GB** olarak değiştirebileceğiniz **Boyut (gib)** dışında varsayılan ayarları kullanın.

      ![Veri diski ayrıntıları](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. **Tamam**’ı seçin.
1. **İncele ve oluştur**’u seçin.
1. **Oluştur**’u seçin.

   **Dağıtım ilerlemesini**Izlemek için **Bildirimler** simgesini seçebilirsiniz. Yeni bir VM oluşturma işleminin tamamlanması birkaç dakika sürebilir.

1. VM dağıtımınız tamamlandıktan sonra **Kaynağa Git**' i seçin.

   ![Kaynağa git](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

Bu noktada yeni bir sanal makine oluşturdunuz ve bir veri diskini kullanıma açtınız. Daha sonra VM 'ye bağlanırsınız.

### <a name="connect-to-your-vm"></a>Sanal makinenize bağlanma

1. Azure portal, sanal makine özellikleri sayfasında **Bağlan** ' ı seçin.

   ![Portaldan bir Azure sanal makinesine bağlanma](./media/storage-sync-files-extend-servers/connect-vm.png)

1. **Sanal makineye bağlan** sayfasında, bağlantı noktası 3389 üzerinden **IP adresine** göre bağlanmak için varsayılan seçenekleri değiştirmeyin. **RDP dosyasını indir**'i seçin.

   ![RDP dosyasını indirme](./media/storage-sync-files-extend-servers/download-rdp.png)

1. İndirilen RDP dosyasını açın ve istendiğinde **Bağlan** ' ı seçin.
1. **Windows Güvenliği** penceresinde **Diğer seçenekler**'i ve ardından **Başka bir hesap kullanın**'ı seçin. Kullanıcı adını *localhost\username*olarak yazın, sanal makine için oluşturduğunuz parolayı girin ve ardından **Tamam**' ı seçin.

   ![Diğer seçenekler](./media/storage-sync-files-extend-servers/local-host2.png)

1. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Bağlantıyı oluşturmak için **Evet** ' i veya **devam et** ' i seçin.

### <a name="prepare-the-windows-server"></a>Windows Server 'ı hazırlama

Windows Server 2016 Datacenter Server için Internet Explorer Artırılmış güvenlik yapılandırmasını devre dışı bırakın. Bu adım yalnızca ilk sunucu kaydı için gereklidir. Sunucu kaydedildikten sonra özelliği yeniden etkinleştirebilirsiniz.

Windows Server 2016 Datacenter VM 'de Sunucu Yöneticisi otomatik olarak açılır.  Sunucu Yöneticisi varsayılan olarak açılmazsa dosyayı dosya Gezgini 'nde arayın.

1. **Sunucu Yöneticisi**, **yerel sunucu**' yı seçin.

   ![Sunucu Yöneticisi kullanıcı arabiriminin sol tarafındaki "Yerel Sunucu"](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. **Özellikler** bölmesinde, **IE artırılmış güvenlik yapılandırması**bağlantısını seçin.  

    ![Sunucu Yöneticisi kullanıcı arabirimindeki "IE Artırılmış Güvenlik Yapılandırması" bölmesi](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. **Internet Explorer Artırılmış Güvenlik Yapılandırması** iletişim kutusunda, **Yöneticiler** ve **Kullanıcılar** için **Kapalı** seçeneğini belirleyin.

    !["Kapalı" seçeneğinin işaretli olduğu Internet Explorer Artırılmış Güvenlik Yapılandırması açılır penceresi](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

Şimdi VM’ye veri diski ekleyebilirsiniz.

### <a name="add-the-data-disk"></a>Veri diski ekleme

1. Hala **Windows Server 2016 Datacenter** VM 'de **Dosyalar ve depolama hizmetleri** > **birimleri** > **diskleri**' ni seçin.

    ![Veri diski](media/storage-sync-files-extend-servers/your-disk.png)

1. **MSFT sanal disk** ADLı 1 GB diske sağ tıklayın ve **Yeni birim**' i seçin.
1. Sihirbazı tamamlayın. Varsayılan ayarları kullanın ve atanan sürücü harfini aklınızda yapın.
1. **Oluştur**’u seçin.
1. **Kapat**' ı seçin.

   Bu noktada, diski çevrimiçi duruma getirdiniz ve bir birim oluşturdunuz. Son eklenen veri diskinin varlığını onaylamak için Windows Server VM 'de dosya Gezgini 'ni açın.

1. VM 'deki dosya Gezgini ' nde, **Bu bilgisayarı** genişletin ve yeni sürücüyü açın. Bu örnekte yeni sürücü F: sürücüsüdür.
1. Sağ tıklayıp **Yeni** > **Klasör**’ü seçin. Klasörü _EşitlenecekDosyalar_ olarak adlandırın.
1. **Filestosync** klasörünü açın.
1. Sağ tıklayıp **Yeni** > **Metin Belgesi**’ni seçin. Metin dosyasını _TestDosyam_ olarak adlandırın.

    ![Yeni metin dosyası ekleme](media/storage-sync-files-extend-servers/new-file.png)

1. **Dosya Gezgini** 'ni ve **Sunucu Yöneticisi**kapatın.

### <a name="download-the-azure-powershell-module"></a>Azure PowerShell modülünü indirin

Ardından, Windows Server 2016 Datacenter VM 'de, Azure PowerShell modülünü sunucuya yüklersiniz.

1. VM 'de yükseltilmiş bir PowerShell penceresi açın.
1. Şu komutu çalıştırın:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Sürümünüz 2.8.5.201 'den eski bir NuGet sürümüne sahipseniz, NuGet 'in en son sürümünü indirip yüklemek isteyip istemediğiniz sorulur.

   PowerShell galerisi varsayılan olarak PowerShellGet için güvenilir depo olarak yapılandırılmamıştır. PSGallery 'yi ilk kez kullandığınızda, aşağıdaki istemi görürsünüz:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Yüklemeye devam etmek için tümüne **Evet** veya **Evet** yanıtını verin.

`Az` modülü, Azure PowerShell cmdlet’leri için toplu bir modüldür. Bu modülü yüklediğinizde kullanılabilir durumdaki tüm Azure Resource Manager modülleri indirilir ve cmdlet’leri kullanıma sunulur.

Bu noktada, ortamınızı öğretici için ayarlarsınız. Depolama eşitleme hizmeti 'ni dağıtmaya hazırsınız.

## <a name="deploy-the-service"></a>Hizmeti dağıtma

Azure Dosya Eşitleme dağıtmak için, önce seçtiğiniz abonelik için bir kaynak grubuna bir **depolama eşitleme hizmeti** kaynağı yerleştirebilirsiniz. Depolama eşitleme hizmeti, erişim izinlerini aboneliğinden ve kaynak grubundan devralır.

1. Azure portal, **kaynak oluştur** ' u seçin ve ardından **Azure dosya eşitleme**arayın.
1. Arama sonuçlarında **Azure Dosya Eşitleme**’yi seçin.
1. **Oluştur**’u seçerek **Depolama Eşitleme’yi Dağıt** sekmesini açın.

   ![Depolama Eşitleme’yi Dağıtma](media/storage-sync-files-extend-servers/afs-info.png)

   Açılan bölmeye aşağıdaki bilgileri girin:

   | Değer | Açıklama |
   | ----- | ----- |
   | **Ad** | Depolama Eşitleme Hizmeti için benzersiz bir ad (abonelik başına).<br><br>Bu öğretici için _afssyncservice02_ kullanın. |
   | **Abonelik** | Bu öğretici için kullandığınız Azure aboneliği. |
   | **Kaynak grubu** | Depolama eşitleme hizmetini içeren kaynak grubu.<br><br>Bu öğretici için _afsresgroup101918_ kullanın. |
   | **Location** | Doğu ABD |

1. İşiniz bittiğinde **Oluştur**’u seçerek **Depolama Eşitleme Hizmeti**’ni dağıtın.
1. **Kaynak sayfasına gitmek**> **Bildirimler** sekmesini seçin.

## <a name="install-the-agent"></a>Aracıyı yükleme

Azure Dosya Eşitleme aracısı, Windows Server’ın bir Azure dosya paylaşımı ile eşitlenmesini sağlayan indirilebilir bir pakettir.

1. **Windows Server 2016 Datacenter** VM 'de **Internet Explorer**'ı açın.
1. [Microsoft İndirme Merkezi](https://go.microsoft.com/fwlink/?linkid=858257)’ne gidin. **Azure dosya eşitleme Aracısı** bölümüne gidin ve **İndir**' i seçin.

   ![Eşitleme aracısını indirme](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. StorageSyncAgent_V3_WS2016 onay kutusunu seçin **. EXE** ve **Ileri ' yi**seçin.

   ![Aracı seçme](media/storage-sync-files-extend-servers/select-agent.png)

1. **Açık** **çalışmaya** > başladıktansonraizinver'i > seçin.
1. Henüz yapmadıysanız, PowerShell penceresini kapatın.
1. **Depolama Eşitleme Aracısı Kurulum Sihirbazı**’nda varsayılan ayarları kabul edin.
1. **Yükle**’yi seçin.
1. **Son**’u seçin.

Azure Eşitleme Hizmeti dağıttıysanız ve aracıyı Windows Server 2016 Datacenter VM 'ye yüklediniz. Artık VM 'yi depolama eşitleme hizmeti ile kaydetmeniz gerekir.

## <a name="register-windows-server"></a>Windows Server’ı kaydetme

Windows Server 'ı bir depolama eşitleme hizmeti ile kaydettirmek, sunucunuz (veya kümeniz) ile depolama eşitleme hizmeti arasında bir güven ilişkisi kurar. Bir sunucu yalnızca bir depolama Eşitleme hizmetine kaydedilebilir. Bu, bu depolama eşitleme hizmeti ile ilişkili diğer sunucularla ve Azure dosya paylaşımlarıyla eşitlenebilir.

Sunucu kayıt kullanıcı arabirimi, Azure Dosya Eşitleme aracısını yükledikten sonra otomatik olarak açılmalıdır. Değilse, dosya konumundan el ile açabilirsiniz:`C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. VM 'de sunucu kaydı kullanıcı arabirimi açıldığında **Tamam**' ı seçin.
1. Başlamak için **oturum aç '** ı seçin.
1. Azure hesabı kimlik bilgilerinizle oturum açın ve **oturum aç**' ı seçin.
1. Şu bilgileri belirtin:

   ![Sunucu Kaydı kullanıcı arabiriminin ekran görüntüsü](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Değer | Açıklama |
   | **Azure Aboneliği** | Bu öğretici için Depolama Eşitleme Hizmetini içeren abonelik. |
   | **Kaynak Grubu** | Depolama eşitleme hizmetini içeren kaynak grubu. Bu öğretici için _afsresgroup101918_ kullanın. |
   | **Depolama Eşitleme Hizmeti** | Depolama eşitleme hizmeti adı. Bu öğretici için _afssyncservice02_ kullanın. |

1. Sunucu kaydını gerçekleştirmek için **Kaydol** ' u seçin.
1. Kayıt sürecinin bir parçası olarak, sizden ek oturum açmanız istenir. Oturum açın ve **İleri ' yi**seçin.
1. **Tamam**’ı seçin.

## <a name="create-a-sync-group"></a>Eşitleme grubu oluşturma

Eşitleme grubu, bir dosya kümesi için eşitleme topolojisini tanımlar. Bir eşitleme grubu, bir Azure dosya paylaşımının temsil ettiği bir bulut uç noktası içermelidir. Bir eşitleme grubu aynı zamanda bir veya daha fazla sunucu uç noktası içermelidir. Sunucu uç noktası, kayıtlı bir sunucu üzerindeki bir yolu temsil eder. Bir eşitleme grubu oluşturmak için:

1. [Azure Portal](https://portal.azure.com/), depolama eşitleme hizmeti 'Nden **+ eşitleme grubu** ' nu seçin. Bu öğretici için *afssyncservice02* kullanın.

   ![Azure portalda yeni bir eşitleme grubu oluşturma](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Bulut uç noktası ile eşitleme grubu oluşturmak için aşağıdaki bilgileri girin:

   | Değer | Açıklama |
   | ----- | ----- |
   | **Eşitleme grubu adı** | Bu ad Depolama Eşitleme Hizmetinde benzersiz olmalıdır, ancak size mantıklı gelen herhangi bir ad olabilir. Bu öğretici için *afssyncgroup* kullanın.|
   | **Abonelik** | Bu öğretici için Depolama Eşitleme Hizmetini dağıttığınız abonelik. |
   | **Depolama hesabı** | **Depolama hesabı Seç ' i**seçin. Görüntülenen bölmede, oluşturduğunuz Azure dosya paylaşımının bulunduğu depolama hesabını seçin. Bu öğretici için *afsstoracct101918* kullanın. |
   | **Azure dosya paylaşımı** | Oluşturduğunuz Azure dosya paylaşımının adı. Bu öğretici için *afsfileshare* kullanın. |

1. **Oluştur**’u seçin.

Eşitleme grubunuzu seçerseniz, artık bir **bulut uç noktanızın** olduğunu görebilirsiniz.

## <a name="add-a-server-endpoint"></a>Sunucu uç noktası ekleme

Sunucu uç noktası, kayıtlı bir sunucuda belirli bir konumu temsil eder. Örneğin, sunucu birimindeki bir klasör. Sunucu uç noktası eklemek için:

1. Yeni oluşturulan eşitleme grubunu seçin ve ardından **sunucu uç noktası Ekle**' yi seçin.

   ![Eşitleme grubu bölmesine yeni bir sunucu uç noktası ekleme](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. Sunucu **uç noktası Ekle** bölmesinde, sunucu uç noktası oluşturmak için aşağıdaki bilgileri girin:

   | | |
   | ----- | ----- |
   | Value | Açıklama |
   | **Kayıtlı sunucu** | Oluşturduğunuz sunucunun adı. Bu öğretici için *afsvm101918* kullanın. |
   | **Yolu** | Oluşturduğunuz sürücünün Windows Server yolu. Bu öğreticide *f:\filestosync* kullanın. |
   | **Bulutta Katmanlama** | Bu öğretici için devre dışı bırakın. |
   | **Birim Boş Alanı** | Bu öğretici için boş bırakın. |

1. **Oluştur**’u seçin.

Dosyalarınız artık Azure dosya paylaşımında ve Windows Server’da eşitlenmiş durumdadır.

![Azure Depolama başarıyla eşitlendi](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Dosya Eşitleme kullanarak bir Windows Server 'ın depolama kapasitesini uzatmak için temel adımları öğrendiniz. Azure Dosya Eşitleme dağıtımını planlamaya daha kapsamlı bir bakış için, bkz:

> [!div class="nextstepaction"]
> [Azure Dosya Eşitleme dağıtımı planlama](./storage-sync-files-planning.md)
