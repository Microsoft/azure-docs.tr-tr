---
title: Görüntüleri Azure DevTest Labs kaydetme ve dağıtma | Microsoft Docs
description: Bu makale, Azure DevTest Labs ' de zaten oluşturulmuş sanal makinelerden (VM 'Ler) özel görüntüler kaydetme adımlarını sağlar.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5278626f8cdd4299912f3c952786422436fe916
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476249"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Özel görüntüleri birden çok laboratuvara kaydetme ve dağıtma
Bu makale, önceden oluşturulmuş sanal makinelerden (VM 'Ler) özel görüntüleri kaydetme adımlarını sağlar. Ayrıca, bu özel görüntülerin kuruluştaki diğer DevTest Labs 'e nasıl dağıtılacağını da ele alır.

## <a name="prerequisites"></a>Önkoşullar
Şu öğeler zaten yerinde olmalıdır:

- Azure DevTest Labs görüntü fabrikası için laboratuvar.
- Görüntü fabrikasını otomatikleştirmek için kullanılan bir Azure DevOps projesi.
- Betikleri ve yapılandırmayı içeren kaynak kodu konumu (örneğimizde, önceki adımda bahsedilen DevOps projesinde).
- Azure PowerShell görevlerini yönetmek için derleme tanımı.

Gerekirse, bu öğeleri oluşturmak veya ayarlamak için [Azure DevOps 'dan görüntü fabrikası çalıştırma](image-factory-set-up-devops-lab.md) ' daki adımları izleyin. 

## <a name="save-vms-as-generalized-vhds"></a>VM 'Leri Genelleştirilmiş VHD 'Ler olarak kaydetme
Mevcut VM 'Leri Genelleştirilmiş VHD 'Ler olarak kaydedin.  Mevcut VM 'Leri Genelleştirilmiş VHD 'Ler olarak kaydetmek için örnek bir PowerShell betiği vardır. Bunu kullanmak için, önce aşağıdaki görüntüde gösterildiği gibi, derleme tanımına başka bir **Azure PowerShell** görevi ekleyin:

![Azure PowerShell adımı ekle](./media/save-distribute-custom-images/powershell-step.png)

Listede yeni görev olduktan sonra, aşağıdaki görüntüde gösterildiği gibi tüm ayrıntıları doldurabilmeniz için öğeyi seçin: 

![PowerShell ayarları](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Genelleştirilmiş ve özelleştirilmiş özel görüntüler
[Azure Portal](https://portal.azure.com), bir sanal makineden özel bir görüntü oluştururken, Genelleştirilmiş veya özel bir özel görüntü oluşturmayı seçebilirsiniz.

- **Özelleştirilmiş özel görüntü:** Sysprep/deprovision makinede çalıştırılmadı. Bu, görüntünün var olan sanal makinede (bir anlık görüntü) işletim sistemi diskinin tam bir kopyası olduğu anlamına gelir.  Bu özel görüntüden yeni bir makine oluşturduğumuz aynı dosyalar, uygulamalar, Kullanıcı hesapları, bilgisayar adı vb. hepsi de mevcuttur.
- **Genelleştirilmiş özel görüntü:** Sysprep/deprovision makinede çalıştırıldı.  Bu işlem çalıştığında, Kullanıcı hesaplarını kaldırır, bilgisayar adını kaldırır, Kullanıcı kayıt defteri kovanlarını ve başka bir sanal makine oluşturulurken özelleştirilebilecek şekilde görüntüyü genelleştirerek, bu şekilde resmi kullanıma sunmaktır.  Bir sanal makineyi genelleştirdiğinizde (Sysprep çalıştırarak), işlem geçerli sanal makineyi yok eder; artık işlevsel olmayacaktır.

Görüntü fabrikasındaki özel görüntüleri yaslama betiği, önceki adımda oluşturulan tüm sanal makineler için VHD 'leri kaydeder (Azure 'da kaynak üzerindeki bir etikete göre tanımlanır).

## <a name="update-configuration-for-distributing-images"></a>Görüntü dağıtmak için yapılandırmayı güncelleştirme
İşlemdeki bir sonraki adım, görüntü fabrikası laboratuvarından özel görüntüleri, ihtiyaç duyulan diğer laboratuvarlara göndermek olacaktır. Bu işlemin temel bölümü yapılandırma dosyasında **labs.js** . Bu dosyayı görüntü fabrikasında bulunan **yapılandırma** klasöründe bulabilirsiniz.

Yapılandırma dosyasında labs.jslistelenen iki önemli nokta vardır:

- Abonelik KIMLIĞINI ve laboratuvar adını kullanarak belirli bir hedef Laboratuvarı benzersiz bir şekilde tanımlar.
- Yapılandırma köküne göreli yollar olarak laboratuvara itilmesi gereken belirli görüntü kümesi. Tüm klasörü (söz konusu klasördeki tüm görüntüleri almak için) de belirtebilirsiniz.

Aşağıda iki laboratuvarın bulunduğu dosyada bir örnek labs.jsverilmiştir. Bu durumda, görüntüleri iki farklı laboratuvara dağıtmaktan olursunuz.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Derleme görevi oluşturma
Bu makalede daha önce gördüğünüz adımların aynısını kullanarak, derleme tanımınıza ek bir **Azure PowerShell** derleme görevi ekleyin. Aşağıdaki görüntüde gösterildiği gibi ayrıntıları girin: 

![Görüntüleri dağıtmak için görev oluştur](./media/save-distribute-custom-images/second-build-task-powershell.png)

Parametreler şunlardır: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Bu görev, görüntü fabrikasında bulunan özel görüntüleri alır ve bunları dosyada Labs.jstanımlanan tüm laboratuvarlara gönderir.

## <a name="queue-the-build"></a>Derlemeyi kuyruğa al
Dağıtım Derleme görevi tamamlandıktan sonra her şeyin çalıştığından emin olmak için yeni bir derleme kuyruğa alın. Oluşturma işlemi başarıyla tamamlandıktan sonra, yeni özel görüntüler, yapılandırma dosyasında Labs.jsgirilen hedef laboratuvarda görünür.

## <a name="next-steps"></a>Sonraki adımlar
Serinin sonraki makalesinde, görüntü fabrikasını bir bekletme ilkesi ve temizleme adımları ile güncelleştirin: [bekletme Ilkesi ayarlama ve Temizleme betiklerini çalıştırma](image-factory-set-retention-policy-cleanup.md).
