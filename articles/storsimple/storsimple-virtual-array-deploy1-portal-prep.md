---
title: StorSimple Sanal dizisi için Portal hazırlığı | Microsoft Docs
description: StorSimple Sanal dizisi dağıtmaya yönelik ilk öğreticide Azure portal hazırlanması gerekir
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ccdd234f951327aeb5baa6aabcac4314b9de7b34
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517098"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>StorSimple Sanal dizisi dağıtma-Azure portal hazırlama

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)


## <a name="overview"></a>Genel Bakış

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Bu, Kaynak Yöneticisi modelini kullanarak sanal dizinizi bir dosya sunucusu veya bir Iscsı sunucusu olarak tamamen dağıtmak için gereken dağıtım öğreticilerinin serisinin ilk makaledir. Bu makalede, bir sanal diziyi sağlamadan önce StorSimple Aygıt Yöneticisi hizmetinizi oluşturmak ve yapılandırmak için gereken hazırlık açıklanır. Bu makale ayrıca bir dağıtım yapılandırma denetim listesi ve yapılandırma önkoşulları ile bağlantı sağlar.

Kurulum ve yapılandırma işlemini tamamlamak için yönetici ayrıcalıkları gerekir. Başlamadan önce dağıtım yapılandırma denetim listesini incelemenizi öneririz. Portal hazırlığı 10 dakikadan kısa sürer.

Bu makalede yayımlanan bilgiler, Azure portal ve Microsoft Azure Kamu bulutta StorSimple Sanal dizilerinin dağıtımına yöneliktir.

### <a name="get-started"></a>başlarken
Dağıtım iş akışı, portalı hazırlamaya, sanallaştırılmış ortamınızda bir sanal dizi sağlamaya ve kurulumu tamamlamaktan oluşur. StorSimple Sanal dizi dağıtımını bir dosya sunucusu veya bir Iscsı sunucusu olarak kullanmaya başlamak için aşağıdaki tablo halinde bulunan kaynaklara başvurmanız gerekir.

#### <a name="deployment-articles"></a>Dağıtım makaleleri

StorSimple Sanal dizinizi dağıtmak için, önceden tanımlanmış sırada aşağıdaki makalelere bakın.

| **#** | **Bu adımda** | **Bunu yapabilirsiniz...** | **Ve bu belgeleri kullanın.** |
| --- | --- | --- | --- |
| 1. |**Azure portal ayarlama** |StorSimple Sanal dizisi sağlamaktan önce StorSimple Aygıt Yöneticisi hizmetinizi oluşturun ve yapılandırın. |[Portalı hazırlama](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Sanal diziyi sağla** |Hyper-V için Windows Server 2012 R2, Windows Server 2012 veya Windows Server 2008 R2 üzerinde Hyper-V çalıştıran bir konak sisteminde StorSimple Sanal dizisine sağlama ve bağlanma. <br></br> <br></br> VMware için, VMware ESXi 5,0, 5,5, 6,0 veya 6,5 çalıştıran bir konak sisteminde StorSimple Sanal dizisine sağlayın ve bağlanın.<br></br> |[Hyper-V ' d a sanal dizi sağlama](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [VMware 'de sanal dizi sağlama](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Sanal diziyi ayarlama** |Dosya sunucunuz için ilk kurulum gerçekleştirin, StorSimple dosya sunucunuzu kaydedin ve cihaz kurulumunu doldurun. Ardından SMB paylaşımlarını sağlayabilirsiniz. <br></br> <br></br> Iscsı sunucunuz için ilk kurulum gerçekleştirin, StorSimple Iscsı sunucunuzu kaydedin ve cihaz kurulumunu doldurun. Daha sonra Iscsı birimleri sağlayabilirsiniz. |[Sanal diziyi dosya sunucusu olarak ayarlama](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Sanal diziyi Iscsı sunucusu olarak ayarlama](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Artık Azure portalını ayarlamaya başlayabilirsiniz.

## <a name="configuration-checklist"></a>Yapılandırma denetim listesi

Yapılandırma denetim listesi, yazılımı StorSimple Sanal diziniz üzerinde yapılandırmadan önce toplamanız gereken bilgileri açıklar. Bu bilgilerin bir süre önce hazırlanması, ortamınızdaki StorSimple cihazını dağıtma işlemini kolaylaştırmaya yardımcı olur. StorSimple Sanal diziniz bir dosya sunucusu veya bir Iscsı sunucusu olarak dağıtıldığına bağlı olarak, aşağıdaki denetim listelerine birini kullanmanız gerekir.

* [StorSimple Sanal dizisi dosya sunucusu yapılandırma denetim listesini](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf)indirin.
* [StorSimple Sanal dizisi Iscsı sunucusu yapılandırma denetim listesini](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf)indirin.

## <a name="prerequisites"></a>Önkoşullar

Burada, StorSimple Aygıt Yöneticisi hizmetiniz, StorSimple Sanal diziniz ve veri merkezi ağı için yapılandırma önkoşullarını bulabilirsiniz.

### <a name="for-the-storsimple-device-manager-service"></a>StorSimple Cihaz Yöneticisi hizmeti için

Başlamadan önce aşağıdakilerden emin olun:

* Erişim kimlik bilgilerine sahip bir Microsoft hesabınız var.
* Erişim kimlik bilgilerine sahip bir Microsoft Azure Storage hesabınız var.
* StorSimple Aygıt Yöneticisi hizmeti için Microsoft Azure aboneliğiniz etkinleştirilmelidir.

### <a name="for-the-storsimple-virtual-array"></a>StorSimple Sanal dizisi için

Bir sanal diziyi dağıtmadan önce, aşağıdakileri yaptığınızdan emin olun:

* Bir cihaz sağlamak için kullanılabilen Windows Server 2008 R2 veya üzeri ya da VMware (ESXi 5,0, 5,5, 6,0 veya 6,5) üzerinde Hyper-V çalıştıran bir konak sistemine erişiminiz var.
* Konak sistemi, sanal dizinizi sağlamak için aşağıdaki kaynakları ayırabiliyor:
  
  * En az 4 çekirdek.
  * En az 8 GB RAM. Sanal diziyi dosya sunucusu olarak yapılandırmayı planlıyorsanız, 8 GB 2.000.000 dosyayı destekler. 2-4 milyon dosyayı desteklemek için 16 GB RAM gerekir.
  * Bir ağ arabirimi.
  * Sistem verileri için 500 GB sanal disk.

### <a name="for-the-datacenter-network"></a>Veri merkezi ağı için

Başlamadan önce aşağıdakilerden emin olun:

* Veri merkezinizdeki ağ, StorSimple cihazınız için ağ gereksinimlerine göre yapılandırılır. Daha fazla bilgi için bkz. [StorSimple Sanal dizisi sistem gereksinimleri](storsimple-ova-system-requirements.md).
* StorSimple Sanal diziniz, her zaman ayrılmış 5 Mbps Internet bant genişliğine (veya daha fazlasına) sahiptir. Bu bant genişliği başka hiçbir uygulamayla paylaşılmamalıdır.

## <a name="step-by-step-preparation"></a>Adım adım hazırlık

Portalınızın StorSimple Aygıt Yöneticisi hizmetine hazırlanması için aşağıdaki adım adım yönergeleri kullanın.

## <a name="step-1-create-a-new-service"></a>1\. adım: Yeni hizmet oluşturma

StorSimple Aygıt Yöneticisi hizmetinin tek bir örneği birden çok StorSimple Sanal dizilerini yönetebilir. StorSimple Cihaz Yöneticisi hizmetinin bir örneğini oluşturmak için aşağıdaki adımları gerçekleştirin. Sanal dizlerinizi yönetmek için mevcut bir StorSimple aygıt yöneticisi hizmetiniz varsa, bu adımı atlayın ve 2. [adıma gidin: Hizmet kayıt anahtarını](#step-2-get-the-service-registration-key)alın.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Hizmetinizle birlikte bir depolama hesabının otomatik olarak oluşturulmasını etkinleştirmediyseniz, bir hizmeti başarıyla oluşturduktan sonra en az bir depolama hesabı oluşturmanız gerekir.
> 
> * Otomatik olarak bir depolama hesabı oluşturmadıysanız, ayrıntılı yönergeler için [Hizmet için yeni bir depolama hesabı yapılandırma](#optional-step-configure-a-new-storage-account-for-the-service) bölümüne gidin.
> * Bir depolama hesabının otomatik olarak oluşturulmasını etkinleştirdiyseniz 2. [adıma gidin: Hizmet kayıt anahtarını](#step-2-get-the-service-registration-key)alın.
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>2\. adım: Hizmet kayıt anahtarı alma

StorSimple Cihaz Yöneticisi hizmeti çalışır duruma geldikten sonra, hizmet kayıt anahtarını almanız gerekir. Bu anahtar StorSimple cihazınızı kaydetmek ve hizmete bağlamak için kullanılır.

[Azure portalında](https://portal.azure.com/) aşağıdaki adımları izleyin.

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> Hizmet kayıt anahtarı, StorSimple Aygıt Yöneticisi hizmetinize kaydolması gereken tüm StorSimple Aygıt Yöneticisi cihazlarını kaydetmek için kullanılır.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>3\. adım: Sanal dizi görüntüsünü indir

Hizmet kayıt anahtarını aldıktan sonra, ana bilgisayar sisteminizde bir sanal dizi sağlamak için uygun sanal dizi görüntüsünü indirmeniz gerekir. Sanal dizi görüntüleri, işletim sistemine özeldir ve Azure portal hızlı başlangıç sayfasından indirilebilir.

> [!IMPORTANT]
> StorSimple Sanal dizisinde çalışan yazılım yalnızca StorSimple Aygıt Yöneticisi hizmeti ile birlikte kullanılabilir.
> 
> 

[Azure portalında](https://portal.azure.com/) aşağıdaki adımları izleyin.

#### <a name="to-get-the-virtual-array-image"></a>Sanal dizi görüntüsünü almak için

1. [Azure portal](https://portal.azure.com/) oturum açın. 
2. Azure portal, **> StorSimple cihaz yöneticilerine gözatıp**' ye tıklayın.
3. Mevcut bir StorSimple Aygıt Yöneticisi hizmeti seçin. **StorSimple aygıt yöneticisi** dikey penceresinde **hızlı başlangıç**' e tıklayın. 
4. Microsoft Indirme Merkezi ' nden indirmek istediğiniz görüntüye karşılık gelen bağlantıya tıklayın. Görüntü dosyaları yaklaşık 4,8 GB'tır.
   
   * Windows Server 2012 ve üzeri üzerinde Hyper-V için VHDX
   * Windows Server 2008 R2 ve üzeri üzerinde Hyper-V için VHD
   * VMWare ESXi 5,0, 5,5, 6,0 veya 6,5 için VMDK
5. Dosyayı yerel sürücüye indirin ve sıkıştırmasını açın. Sıkıştırması açılan dosyanın konumunu not alın.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>İsteğe bağlı adım: Hizmet için yeni bir depolama hesabı yapılandırın

Bu adım isteğe bağlıdır ve yalnızca hizmetinizdeki bir depolama hesabının otomatik olarak oluşturulmasını etkinleştirmezseniz gerçekleştirilmelidir.

Farklı bir bölgede bir Azure depolama hesabı oluşturmanız gerekiyorsa, adım adım yönergeler için bkz. [depolama hesabı oluşturma](../storage/common/storage-quickstart-create-account.md) .

Mevcut bir Microsoft Azure Storage hesabı eklemek için StorSimple Aygıt Yöneticisi Hizmeti sayfasındaki [Azure Portal](https://ms.portal.azure.com/) aşağıdaki adımları gerçekleştirin.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Aygıt Yöneticisi hizmeti ile aynı Azure aboneliğine sahip bir depolama hesabı kimlik bilgisi eklemek için

1. Aygıt Yöneticisi hizmetinize gidin, seçin ve çift tıklayın. Bu, **genel bakış** dikey penceresini açar.
2. **Yapılandırma** bölümünde **depolama hesabı kimlik bilgilerini** seçin.
3.           **Ekle**'yi tıklatın.
4. **Depolama hesabı ekle** dikey penceresinde aşağıdakileri yapın:
   
   1. **Abonelik**için **geçerli**' i seçin.
   
   2. Azure depolama hesabınızın adını belirtin.
   
   3. StorSimple cihazınız ve bulut arasında ağ iletişimi için güvenli bir kanal oluşturmak üzere **Etkinleştir** ' i seçin. Yalnızca özel bir bulutta çalışıyorsanız **devre dışı bırak** ' ı seçin.
   
   4.           **Ekle**'yi tıklatın. Depolama hesabı başarıyla oluşturulduktan sonra bilgilendirilirsiniz.<br></br>
   
      ![Mevcut bir depolama hesabı kimlik bilgisi ekleyin](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Sonraki adım

Bir sonraki adım, StorSimple Sanal diziniz için bir sanal makine sağlamadır. Konak işletim sisteminize bağlı olarak, şuradaki ayrıntılı yönergelere bakın:

* [Hyper-V ' d a StorSimple Sanal dizisi sağlama](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [VMware 'de StorSimple Sanal dizisi sağlama](storsimple-virtual-array-deploy2-provision-vmware.md)

