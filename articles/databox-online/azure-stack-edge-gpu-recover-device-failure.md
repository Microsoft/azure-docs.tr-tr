---
title: Azure Stack Edge Pro cihaz hatasından kurtarma
description: Azure Stack Edge Pro başarısız bir cihazdan nasıl kurtarılacağı açıklanmaktadır.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: b1bfbda007619bf5bd94d47297845881758037bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102636649"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>Başarısız Azure Stack Edge Pro GPU cihazından kurtarma 

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Bu makalede Azure Stack Edge Pro GPU cihazınızdan toleransız olmayan bir hatadan nasıl kurtarılacağı açıklanır. Azure Stack Edge Pro GPU cihazındaki toleransız bir hata, cihazın değiştirilmesini gerektirir.

## <a name="before-you-begin"></a>Başlamadan önce

Bilgisayarınızda yüklü olduğundan emin olun:

- Cihaz hatası ile ilgili olarak Microsoft Desteği ile iletişim kurar ve cihaz değişikliği önerilir. 
- Cihaz yapılandırmanızı [bir cihaz hatası Için hazırlama](azure-stack-edge-gpu-prepare-device-failure.md)bölümünde anlatıldığı şekilde yedekledi.


## <a name="configure-replacement-device"></a>Değiştirme cihazını yapılandırma

Cihazınız toleransız bir hata ile karşılaştığında, bir değiştirme cihazı sipariş etmeniz gerekir. Değiştirme cihazının yapılandırma adımları aynı kalır. 

Başarısız olan cihazdan yedeklediğiniz cihaz yapılandırma bilgilerini alın. Bu bilgileri, değiştirme cihazını yapılandırmak için kullanacaksınız.  

Değiştirme cihazını yapılandırmak için aşağıdaki adımları izleyin:

1. [Dağıtım denetim listesinde](azure-stack-edge-gpu-deploy-checklist.md)gereken bilgileri toplayın. Önceki cihaz yapılandırmasından kaydettiğiniz bilgileri kullanabilirsiniz. 
1. Başarısız olan yapılandırmayla aynı yapılandırmaya sahip yeni bir cihaz sıralayın.  Bir sipariş yerleştirmek için, Azure portal [Yeni bir Azure Stack Edge kaynağı oluşturun](azure-stack-edge-gpu-deploy-prep.md#) .
1. [Paketi](azure-stack-edge-gpu-deploy-install.md#unpack-the-device)açın, [raf takın](azure-stack-edge-gpu-deploy-install.md#rack-the-device) ve [cihazınızı bağlayın](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Cihazın yerel kullanıcı arabirimine bağlanın](azure-stack-edge-gpu-deploy-connect.md).
1. Ağı, eski cihazınız için kullandığınız IP adreslerini kullanarak yapılandırın. Aynı IP adreslerini kullanmak ortamınızda kullanılan tüm istemci makinelerde etkiyi en aza indirir. Bkz. [ağ ayarlarını yapılandırma](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Eski cihazınız ile aynı cihaz adı ve DNS etki alanını atayın. Bu şekilde, istemcileriniz yeni cihazla konuşmak için aynı cihaz adını kullanabilir. Bkz. [cihaz ayarlarını yapılandırma](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Yeni cihazdaki sertifikaları eski cihazla yaptığınız şekilde yapılandırın. Yeni cihazın yeni bir düğüm seri numarası olduğunu aklınızda bulundurun. Eski cihazda kendi sertifikalarınızı kullandıysanız yeni bir düğüm sertifikası almanız gerekecektir. Bkz. [sertifikaları yapılandırma](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Azure portal etkinleştirme anahtarını alın ve yeni cihazı etkinleştirin. Bkz. [cihazı etkinleştirme](azure-stack-edge-gpu-deploy-activate.md).

Artık eski cihazda çalıştırdığınız iş yüklerini dağıtmaya hazırsınız.

## <a name="restore-edge-cloud-shares"></a>Uç bulut paylaşımlarını geri yükleme

Cihazınızdaki uç bulut paylaşımlarındaki verileri geri yüklemek için şu adımları izleyin:

1. Daha önce başarısız olan cihazda oluşturulmuş aynı paylaşım adlarına sahip [Paylaşımlar ekleyin](azure-stack-edge-gpu-manage-shares.md#add-a-share) . Paylaşımlar oluştururken, **BLOB kapsayıcısı** ' nın **var olan seçeneği kullanacak** şekilde ayarlandığından emin olun ve ardından önceki cihazla kullanılan kapsayıcıyı seçin.
1. Önceki cihaza erişimi olan [kullanıcıları ekleyin](azure-stack-edge-gpu-manage-users.md#add-a-user) .
1. Daha önce cihazda bulunan paylaşımlarla ilişkili [depolama hesaplarını ekleyin](azure-stack-edge-gpu-manage-storage-accounts.md#add-an-edge-storage-account) . Sınır depolama hesapları oluştururken, mevcut bir kapsayıcı arasından seçim yapın ve önceki cihazda eşlenen Azure depolama hesabıyla eşleştirilmiş kapsayıcıyı işaret edin. Cihazdan önceki cihazdaki Edge depolama hesabına yazılan tüm veriler, eşlenen Azure depolama hesabındaki seçili depolama kapsayıcısına yüklendi.
1. Azure 'daki [Share verilerini yenileyin](azure-stack-edge-gpu-manage-shares.md#refresh-shares) . Bu, mevcut kapsayıcıdan tüm bulut verilerini paylaşımlara çeker.

## <a name="restore-edge-local-shares"></a>Uç yerel paylaşımları geri yükleme

Olası bir cihaz hatasına hazırlanmak için, Kubernetes veya IoT iş yüklerinizde Yerel paylaşım verilerini korumak üzere aşağıdaki yedekleme çözümlerini dağıtmış olabilirsiniz:

| Üçüncü taraf yazılım           | Çözüme başvuru                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Ayrıntılar için Cohesity başvurun.          |
| Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Ayrıntılar için, Commkasaya başvurun. |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Ayrıntılar için VERITAS ile iletişim kurun.   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Ayrıntılar için, Veead ile iletişim kurun. |

Değiştirme aygıtı tam olarak yapılandırıldıktan sonra, cihazı yerel depolama için etkinleştirin. 

Yerel paylaşımlardaki verileri kurtarmak için şu adımları izleyin:

1. [Cihazda Işlem yapılandırma](azure-stack-edge-gpu-deploy-configure-compute.md).
1. [Yerel bir paylaşımdan geri ekleyin](azure-stack-edge-gpu-manage-shares.md#add-a-local-share) .
1. Tercih edilen veri koruma çözümü tarafından sunulan kurtarma yordamını çalıştırın. Yukarıdaki tablodaki başvurulara bakın.

## <a name="restore-vm-files-and-folders"></a>VM dosyalarını ve klasörlerini geri yükleme

Potansiyel bir cihaz hatasına hazırlanmak için, VM 'lerdeki verileri korumak üzere aşağıdaki Yedekleme çözümlerinden birini dağıttıysanız:



| Yedekleme çözümleri        | Desteklenen İşletim Sistemi   | Başvuru                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Azure Backup için Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı | Windows        | [MARS aracısı hakkında](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows, Linux | [Microsoft Azure tümleştirme, yedekleme & kurtarma çözümü kısa](https://www.cohesity.com/solution/cloud/azure) <br>Ayrıntılar için Cohesity başvurun.                          |
| Commvault               | Windows, Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Ayrıntılar için, Commkasaya başvurun.
| Veritas                 | Windows, Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370) <br> Ayrıntılar için VERITAS ile iletişim kurun.                    |
| Veeam                   | Windows, Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Ayrıntılar için, Veead ile iletişim kurun. |

Değiştirme aygıtı tam olarak yapılandırıldıktan sonra VM 'Leri daha önce kullanılan VM görüntüsü ile yeniden dağıtabilirsiniz. 

VM 'lerdeki verileri kurtarmak için şu adımları izleyin:
 
1. Cihazdaki [VM görüntüsünden BIR VM dağıtın](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) . 
1. SANAL makineye tercih edilen veri koruma çözümünü yükler.
1. Tercih edilen veri koruma çözümü tarafından sunulan kurtarma yordamını çalıştırın. Yukarıdaki tablodaki başvurulara bakın.

## <a name="restore-a-kubernetes-deployment"></a>Kubernetes dağıtımını geri yükleme

Kubernetes dağıtımınızı Azure Arc aracılığıyla gerçekleştirdiyseniz, toleransı olmayan bir cihaz hatasından sonra dağıtımı geri yükleyebilirsiniz. Müşteri uygulamasını/kapsayıcıları `git` uygulama tanımının depolandığı depodan yeniden dağıtmanız gerekir. [Azure Arc ile Kubernetes dağıtma hakkında bilgi](./azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)<!--Original text: Kubernetes deployments can be restored from a non-tolerated failure with the device when deployed with Azure Arc. Customer application/containers deployed onto a Kubernetes on Azure Stack Edge via Azure Arc can be redeployed from the git repository where the application definition is. Here is a link to the article to deploy Kubernetes with Arc -->
 
## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Pro cihazının nasıl döneceğinizi](azure-stack-edge-return-device.md)öğrenin.
