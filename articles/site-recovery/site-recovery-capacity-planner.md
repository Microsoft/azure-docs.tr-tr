---
title: Azure Site Recovery Hyper-V olağanüstü durum kurtarma için kapasite planlayın
description: Azure Site Recovery hizmeti ile olağanüstü durum kurtarma ayarlanırken kapasiteyi tahmin etmek için bu makaleyi kullanın.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 843d5da26d6791cea880e5dfb654fe27b74f5d9f
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73936039"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Hyper-V VM olağanüstü durum kurtarma için kapasiteyi planlayın 

Hyper-V ' d e Azure dağıtımı için [Azure Site Recovery Dağıtım Planlayıcısı] (site-recovery-hyper-v-deployment-planner.md) şunları sağlar:

* Disk sayısı, disk boyutu, ıOPS, değişim ve birkaç VM özelliği temel alınarak VM uygunluk değerlendirmesi
* Ağ bant genişliği gereksinimini ve RPO değerlendirmesi
* Azure altyapı gereksinimleri
* Şirket içi altyapı gereksinimleri
* İlk çoğaltma toplu işlem kılavuzu
* Azure için tahmini toplam olağanüstü durum kurtarma maliyeti


Azure Site Recovery Capacity Planner, Hyper-V VM 'lerini Azure Site Recovery çoğalttığınızda kapasite gereksinimlerinizi belirlemenize yardımcı olur.

Kaynak ortamınızı ve iş yüklerinizi çözümlemek için Site Recovery Capacity Planner kullanın. Bant genişliği ihtiyaçlarını, kaynak konumu için ihtiyaç duyduğunuz sunucu kaynaklarını ve hedef konumda ihtiyacınız olan kaynakları (VM 'Ler ve depolama gibi) tahmin etmenize yardımcı olur.

Aracı iki modda çalıştırabilirsiniz:

* **Hızlı planlama**: ağ ve sunucu projeksiyonlarını Ortalama sayıda VM, disk, depolama ve değişim oranı temelinde sağlar.
* **Ayrıntılı planlama**: VM düzeyinde her iş yükünün ayrıntılarını sağlar. VM uyumluluğunu çözümleyin ve ağ ve sunucu projeksiyonlarını alın.

## <a name="before-you-start"></a>Başlamadan önce

* Sanal makineler, VM başına diskler, disk başına depolama alanı dahil olmak üzere, ortamınız hakkında bilgi toplayın.
* Çoğaltılan veriler için günlük değişiklik (karmaşıklık) hızınızı belirler. Değişiklik hızını almak için [Hyper-V kapasite planlama aracı](https://www.microsoft.com/download/details.aspx?id=39057) 'nı indirin. Bu araç hakkında [daha fazla bilgi edinin](site-recovery-capacity-planning-for-hyper-v-replication.md). Ortalamaları yakalamak için bu aracı bir hafta boyunca çalıştırmanızı öneririz.


## <a name="run-the-quick-planner"></a>Hızlı planlayıcısı çalıştırın
1. [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel)indirin ve açın. Makrolar çalıştırmanız gerekir. İstendiğinde, düzenlemenin ve içeriğin etkinleştirilmesi için seçimler yapın.

2. **Planner türü seç** liste kutusunda **hızlı planlayıcısı**' nı seçin.

   ![Başlarken](./media/site-recovery-capacity-planner/getting-started.png)

3. **Capacity Planner** çalışma sayfasında, gerekli bilgileri girin. Aşağıdaki ekran görüntüsünde, tüm alanları kırmızı renkte daire içine girin:

   a. **Senaryonuzu seçin**bölümünde **Azure 'A Hyper-V** veya Azure için **VMware/fiziksel**' i seçin.

   b. **Günlük ortalama veri değişim oranı (%)** Içinde, [Hyper-V kapasite planlama aracı](site-recovery-capacity-planning-for-hyper-v-replication.md) 'nı veya [Site Recovery dağıtım planlayıcısı](./site-recovery-deployment-planner.md)kullanarak topladığınız bilgileri girin.

   c. **Sıkıştırma** ayarı, Hyper-V VM 'lerini Azure 'a çoğalttığınızda kullanılmaz. Sıkıştırma için, bir üçüncü taraf gereç kullanın, örneğin, Riveryatak.

   d. **Gün cinsinden bekletme**bölümünde, çoğaltmaların ne kadar süreyle tutulacağını belirtin.

   e. Her **bir sanal makine için ilk çoğaltmanın,** **ilk çoğaltma toplu işi başına sanal makine sayısını ve sayısını**tamamlaması gereken saat sayısı, ilk çoğaltma gereksinimlerini hesaplamak için kullanılan ayarları girin. Site Recovery dağıtıldığında, ilk veri kümesinin tamamı karşıya yüklenir.

   ![Girişler](./media/site-recovery-capacity-planner/inputs.png)

4. Kaynak ortam için değerleri girdikten sonra, görünen çıktı şunları içerir:

   * **Delta çoğaltma için gereken bant genişliği (megabit/sn cinsinden)** : Delta çoğaltma için ağ bant genişliği, günlük ortalama veri değişim oranı üzerinden hesaplanır.
   * **İlk çoğaltma için gereken bant genişliği (megabit/sn cinsinden)** : ilk çoğaltma için ağ bant genişliği, girdiğiniz ilk çoğaltma değerlerinde hesaplanır.
   * **Depolama gerekli (GB cinsinden)** : gereken toplam Azure depolama alanı.
   * **Standart depolamada toplam IOPS**: Bu sayı, toplam standart depolama HESAPLARıNDAKI 8k IOPS birim boyutu temel alınarak hesaplanır. Hızlı Planner için, numara tüm kaynak VM disklerine ve günlük veri değişim hızına göre hesaplanır. Ayrıntılı Planner için, numara standart Azure VM 'lerine eşlenmiş toplam sanal makine sayısına ve bu VM 'lerde veri değişim hızına göre hesaplanır.
   * **Gerekli standart depolama hesabı sayısı**: VM 'leri korumak için gereken standart depolama hesaplarının toplam sayısı. Standart depolama hesabı, standart depolama alanındaki tüm VM 'lerde en fazla 20.000 ıOPS tutabilir. Disk başına en fazla 500 ıOPS desteklenir.
   * **Gereken blob disk sayısı**: Azure depolamada oluşturulan disk sayısı.
   * **Gerekli Premium hesap sayısı**: VM 'leri korumak için gereken toplam Premium depolama hesabı sayısıdır. Yüksek ıOPS (20.000 'den büyük) içeren bir kaynak VM, Premium depolama hesabına ihtiyaç duyuyor. Premium depolama hesabı en fazla 80.000 ıOPS tutabilir.
   * **Premium depolamada toplam IOPS**: sayı, toplam Premium Depolama HESAPLARıNDA 256k IOPS birimi boyutu temel alınarak hesaplanır. Hızlı Planner için, numara tüm kaynak VM disklerine ve günlük veri değişim hızına göre hesaplanır. Ayrıntılı Planner için sayı, Premium Azure VM 'lerine (DS ve GS serisi) eşlenmiş toplam sanal makine sayısına ve bu VM 'lerde veri değişim hızına göre hesaplanır.
   * **Gerekli yapılandırma sunucusu sayısı**: dağıtım için kaç yapılandırma sunucusu gerektiğini gösterir.
   * **Gerekli ek Işlem sunucusu sayısı**: yapılandırma sunucusunda varsayılan olarak çalışan işlem sunucusuna ek olarak, ek işlem sunucularının gerekli olup olmadığını gösterir.
   * **kaynak üzerinde %100 ek depolama alanı**: kaynak konumda ek depolamanın gerekli olup olmadığını gösterir.

      ![Çıktı](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Ayrıntılı planlayıcısı çalıştırın

1. [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel)indirin ve açın. Makrolar çalıştırmanız gerekir. İstendiğinde, düzenlemenin ve içeriğin etkinleştirilmesi için seçimler yapın.

2. **Planner türü seç**' te, liste kutusundan **ayrıntılı Planner** ' ı seçin.

   ![Başlangıç kılavuzu](./media/site-recovery-capacity-planner/getting-started-2.png)

3. **Iş yükü niteliği** çalışma sayfasında, gerekli bilgileri girin. Tüm işaretli alanları doldurmanız gerekir.

   a. **Işlemci çekirdekleri**' nde, kaynak sunucudaki toplam çekirdek sayısını belirtin.

   b. **Bellek ayırma (MB cinsinden)** alanında, kaynak sunucunun RAM boyutunu belirtin.

   c. **NIC sayısı**' nda, bir kaynak sunucuda ağ bağdaştırıcılarının sayısını belirtin.

   d. **Toplam depolama alanı (GB cinsinden)** , VM depolamanın toplam boyutunu belirtin. Örneğin, kaynak sunucuda her biri 500 GB olan üç disk varsa, toplam depolama boyutu 1.500 GB 'dir.

   e. **Bağlı disk sayısı**bölümünde, kaynak sunucunun toplam disk sayısını belirtin.

   f. **Disk kapasitesi kullanımı (%)** bölümünde ortalama kullanımı belirtin.

   g. **Günlük veri değişim oranı (%)** içinde, bir kaynak sunucunun günlük veri değişim hızını belirtin.

   h. **Azure VM boyutunu eşleme**' de, eşlemek ISTEDIĞINIZ Azure VM boyutunu girin. Bunu el ile yapmak istemiyorsanız, **Işlem IaaS VM**'lerini seçin. El ile bir ayar girin ve ardından **IaaS VM 'leri**seçerseniz, el ile ayarının üzerine yazılabilir. İşlem işlemi, Azure VM boyutuyla en iyi eşleşmeyi otomatik olarak tanımlar.

   ![İş yükü niteliği çalışma sayfası](./media/site-recovery-capacity-planner/workload-qualification.png)

4. **Işlem IaaS VM 'leri**seçerseniz şunları yapın:

   * Zorunlu girişleri doğrular.
   * IOPS 'yi hesaplar ve Azure 'a çoğaltmaya uygun olan her VM için en iyi Azure VM boyutu eşleşmesini önerir. Uygun büyüklükte bir Azure VM 'si algılanmıyorsa bir hata görüntülenir. Örneğin, eklenen disk sayısı 65 ise bir Azure VM için en yüksek boyut 64 olduğundan bir hata görüntülenir.
   * Bir Azure VM için kullanılabilen bir depolama hesabı önerir.
   * İş yükü için gereken toplam standart depolama hesabı ve Premium depolama hesabı sayısını hesaplar. Azure depolama türünü ve kaynak sunucu için kullanılabilecek depolama hesabını görüntülemek için aşağı kaydırın.
   * , Bir VM için atanan gerekli depolama türünü (Standart veya Premium) ve eklenen disklerin sayısını temel alarak tablonun geri kalanını tamamlar ve sıralar. Azure gereksinimlerini karşılayan tüm VM 'Ler için, sütun **VM 'nin nitelikli mı?** **Evet**' i gösterir. Bir VM Azure 'a yedeklenmiyorsa bir hata görüntülenir.

AA-AE sütunları çıktı ve her VM için bilgi sağlar.

![Çıkış sütunları AA-AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Örnek
Örnek olarak, tabloda gösterilen altı VM için, araç en iyi Azure VM eşleşmesi ve Azure depolama gereksinimlerini hesaplar ve atar.

![İş yükü nitelendirme atamaları](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Örnek çıktıda aşağıdakilere göz önünde yer verilmiştir:

  * İlk sütun VM 'Ler, diskler ve dalgalanma yönelik bir doğrulama sütunudur.
  * Beş VM için iki standart depolama hesabı ve bir Premium depolama hesabı gereklidir.
  * VM3, bir veya daha fazla disk 1 TB 'den fazla olduğundan koruma için uygun değil.
  * VM1 ve VM2, ilk standart depolama hesabını kullanabilir
  * VM4, ikinci standart depolama hesabını kullanabilir.
  * VM5 ve VM6 için Premium depolama hesabı gerekir ve her ikisi de tek bir hesap kullanabilir.

    > [!NOTE]
    > Standart ve Premium depolamada ıOPS, disk düzeyinde değil, VM düzeyinde hesaplanır. Standart bir VM, disk başına 500 ıOPS 'ye kadar işleyebilir. Bir disk için ıOPS 500 ' den büyükse, Premium depolamaya ihtiyacınız vardır. Bir disk için ıOPS 500 ' den büyükse ancak toplam VM disklerinin ıOPS 'si destek standart Azure VM sınırları içindeyse, Planner DS veya GS serisini değil, standart bir VM seçer. (Azure VM sınırları, sanal makine boyutu, disk sayısı, bağdaştırıcı sayısı, CPU ve bellek.) Eşleme Azure boyut hücresini uygun DS veya GS serisi VM 'si ile el ile güncelleştirmeniz gerekir.


Tüm bilgiler girildikten sonra Capacity Planner açmak için **planlayıcısı aracına veri Gönder** ' i seçin. İş yükleri, koruma için uygun olup olmadığını göstermek üzere vurgulanır.

### <a name="submit-data-in-capacity-planner"></a>Capacity Planner veri gönderme
1. **Capacity Planner** çalışma sayfasını açtığınızda, belirttiğiniz ayarlara göre doldurulur. "Iş yükü" sözcüğü, girişin **Iş yükü niteliği** çalışma sayfası olduğunu göstermek Için **infra girişleri kaynak** hücresinde görüntülenir.

2. Değişiklik yapmak istiyorsanız, **Iş yükü niteliği** çalışma sayfasını değiştirmeniz gerekir. Sonra **verileri Ajanda aracına yeniden gönder** ' i seçin.

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Sonraki adımlar
Kapasite planlama aracının [nasıl çalıştırılacağını öğrenin](site-recovery-capacity-planning-for-hyper-v-replication.md) .
