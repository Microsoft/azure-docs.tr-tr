---
title: Windows AWS EC2 örneğini Azure 'a taşıma
description: Bir Amazon Web Services (AWS) EC2 Windows örneğini bir Azure sanal makinesine taşıyın.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 86d3e67b121bf7d66651e1e823e5f137e918a171
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550799"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Windows VM 'yi Amazon Web Services (AWS) 'den Azure sanal makinesine taşıma

İş yüklerinizi barındırmak için Azure sanal makinelerini değerlendiriyorsanız, mevcut bir Amazon Web Services (AWS) EC2 Windows sanal makıne örneğini dışa aktarabilir ve ardından sanal sabit diski (VHD) Azure 'a yükleyebilirsiniz. VHD karşıya yüklendikten sonra, VHD 'den Azure 'da yeni bir VM oluşturabilirsiniz. 

Bu makalede, AWS 'den Azure 'a tek bir VM 'nin taşınması ele alınmaktadır. AWS 'den Azure 'a sanal makineleri ölçekli olarak taşımak istiyorsanız, bkz. [Amazon Web Services (AWS) sanal makinelerini Azure Site Recovery Azure 'A geçirme](../../site-recovery/migrate-tutorial-aws-azure.md).

## <a name="prepare-the-vm"></a>VM’yi hazırlama 
 
Hem Genelleştirilmiş hem de özelleştirilmiş VHD 'leri Azure 'a yükleyebilirsiniz. Her tür, VM 'leri AWS 'den dışarı aktarmadan önce hazırlamanızı gerektirir. 

- **GENELLEŞTIRILMIŞ VHD** -GENELLEŞTIRILMIŞ bir VHD, tüm kişisel hesap bilgilerinizin Sysprep kullanılarak kaldırıldığını içeriyordu. VHD 'yi ' den yeni VM 'Ler oluşturmak için bir görüntü olarak kullanmayı düşünüyorsanız şunları yapmalısınız: 
 
    * [Windows VM 'Yi hazırlayın](prepare-for-upload-vhd-image.md).  
    * Sysprep kullanarak sanal makineyi genelleştirin.  

 
- **ÖZELLEŞTIRILMIŞ VHD** -ÖZELLEŞTIRILMIŞ bir VHD, özgün VM 'nizden Kullanıcı hesaplarını, uygulamaları ve diğer durum verilerini korur. Yeni bir VM oluşturmak için VHD 'YI kullanmak istiyorsanız, aşağıdaki adımların tamamlandığından emin olun.  
    * [Bir WINDOWS VHD 'Yi Azure 'a yüklemek Için hazırlayın](prepare-for-upload-vhd-image.md). Sysprep 'ı kullanarak VM 'yi **genelleştirmeyin** . 
    * VM 'de yüklü olan tüm konuk sanallaştırma araçlarını ve aracılarını (örneğin, VMware araçları) kaldırın. 
    * VM 'nin IP adresini ve DNS ayarlarını DHCP aracılığıyla çekmek üzere yapılandırıldığından emin olun. Bu, sunucu başlatıldığında sanal ağ içinde bir IP adresi elde edilmesini sağlar.  


## <a name="export-and-download-the-vhd"></a>VHD 'YI dışarı ve indirin 

EC2 örneğini bir Amazon S3 demetini içindeki bir VHD 'ye aktarın. [Sanal makine içeri/dışarı aktarma kullanarak bir ÖRNEĞI VM olarak dışarı](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) aktarmak için Amazon belge makalesindeki adımları IZLEYIN ve EC2 ÖRNEĞINI bir VHD dosyasına aktarmak için [Create-Instance-Export-Task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) komutunu çalıştırın. 

İçe aktarılmış VHD dosyası, belirttiğiniz Amazon S3 demet içine kaydedilir. VHD 'YI dışa aktarmaya yönelik temel söz dizimi aşağıda yer tutucu metnin yerine bilgilerinizi yazmanız yeterlidir \<brackets> .

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

VHD verildiğinde, S3 demetini 'nden VHD dosyasını indirmek için [S3 demetini nasıl bir nesne indiririm?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) bölümündeki yönergeleri izleyin. 

> [!IMPORTANT]
> AWS, VHD 'YI indirmek için veri aktarımı ücretleri ücretlendirir. Daha fazla bilgi için bkz. [Amazon S3 fiyatlandırması](https://aws.amazon.com/s3/pricing/) .


## <a name="next-steps"></a>Sonraki adımlar

Şimdi de VHD 'yi Azure 'a yükleyebilir ve yeni bir VM oluşturabilirsiniz. 

- Vermeden önce **genelleştirmek** için kaynağınız üzerinde Sysprep 'i çalıştırdıysanız, bkz. [genelleştirilmiş bir VHD 'yi karşıya yükleme ve Azure 'Da yeni VM 'ler oluşturmak için kullanma](upload-generalized-managed.md)
- Vermeden önce Sysprep 'ı çalıştırmadınız, VHD **özelleştirilmiş** olarak kabul edilir, bkz. [özelleştirilmiş bir VHD 'Yi Azure 'a yükleme ve yeni bir VM oluşturma](create-vm-specialized.md)

 
