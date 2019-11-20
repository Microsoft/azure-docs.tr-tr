---
title: VMware VM’lerini Azure Backup Sunucusu ile geri yükleme
description: VMware vCenter/ESXi sunucusunda çalışan VMware VM 'lerini geri yüklemek için Azure Backup Sunucusu (MABS) kullanın.
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: 7c93c3100d8756fd9faf8cf02152a870bd0c106c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171918"
---
# <a name="restore-vmware-virtual-machines"></a>VMware sanal makinelerini geri yükleme

Bu makalede, VMware VM kurtarma noktalarını geri yüklemek için Microsoft Azure Backup sunucusu 'nun (MABS) nasıl kullanılacağı açıklanmaktadır. Verileri kurtarmak üzere MABS kullanımına genel bakış için bkz. [Korumalı verileri kurtarma](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server). MABS Yönetici Konsolu, kurtarılabilir verileri bulmanın iki yolu vardır-arama veya tarama. Verileri kurtarırken, verileri veya bir VM 'yi aynı konuma geri yüklemek isteyebilirsiniz. Bu nedenle, MABS, VMware VM yedeklemeleri için üç kurtarma seçeneğini destekler:

* **Özgün konum kurtarma (olr)** -KORUMALı bir VM 'yi özgün konumuna geri yüklemek için olr 'yi kullanın. Bir VM 'yi yalnızca bir disk eklenmediyse veya silinmediyse, yedekleme gerçekleşdiğinden, özgün konumuna geri yükleyebilirsiniz. Diskler eklendiyse veya silinirse, alternatif bir konum kurtarma kullanmanız gerekir.

* **Alternatif Konum Kurtarma (ALR)** -özgün VM eksik olduğunda veya özgün VM 'yi rahatsız etmek ISTEMIYORSANıZ, VM 'yi alternatif bir konuma kurtarın. Bir VM 'yi alternatif bir konuma kurtarmak için bir ESXi konağının, kaynak havuzunun, klasörün ve depolama veri deposunun ve yolun konumunu sağlamanız gerekir. Geri yüklenen VM 'nin orijinal VM 'den ayırt edilmesine yardımcı olmak için MABS, "-Kurtarılan" öğesini VM adına ekler.

* **Tek dosya konumu kurtarma (ıLR)** -korunan VM bir Windows Server sanal makinesi Ise, sanal makine içindeki bireysel dosyalar/klasörler mabs 'nin ILR özelliği kullanılarak kurtarılabilir. Tek tek dosyaları kurtarmak için bu makalenin ilerleyen kısımlarında yer alarak bulunan yordama bakın.

## <a name="restore-a-recovery-point"></a>Kurtarma noktasını geri yükleme

1. MABS Yönetici Konsolu, kurtarma görünümü ' ne tıklayın.

2. Kurtarmak istediğiniz VM 'yi bulmak için, tarama bölmesini kullanın veya filtre uygulayın. Bir VM veya klasör seçtiğinizde, bölmedeki Kurtarma noktaları kullanılabilir kurtarma noktalarını görüntüler.

    ![Kullanılabilir kurtarma noktaları](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. **Kurtarma noktaları** alanında, bir kurtarma noktasının alındığı tarih seçmek için Takvim ve açılır menüleri kullanın. Kalın yazı tipinde takvim tarihleri kullanılabilir kurtarma noktalarına sahip.

4. Araç şeridinde, **Kurtarma Sihirbazı**'nı açmak için **kurtar** ' ı tıklatın.

    ![Kurtarma Sihirbazı, kurtarma seçimini ıncele](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. **Kurtarma seçeneklerini belirtin** ekranına Ilerlemek için **İleri** ' ye tıklayın.

6. **Kurtarma seçeneklerini belirtin** ekranında, ağ bant genişliği azaltmayı etkinleştirmek istiyorsanız **Değiştir**' e tıklayın. Ağ azaltmayı devre dışı bırakmak için **İleri**'ye tıklayın. Bu sihirbaz ekranında, VMware VM 'Leri için başka bir seçenek bulunmamaktadır. Ağ bant genişliği kısıtlaması ' nı değiştirmeyi seçerseniz, kısıtlama iletişim kutusunda **ağ bant genişliği kullanımını azaltmayı etkinleştir** ' i seçerek açın. Etkinleştirildikten sonra **ayarları** ve **iş zamanlamasını**yapılandırın.

7. **Kurtarma türü seçin** ekranında, özgün örneğe mi yoksa yeni bir konuma mi kurtarılacağı seçin ve **İleri**' ye tıklayın.

     * **Özgün örneğe kurtar**' ı seçerseniz, sihirbazda başka seçimler yapmanıza gerek kalmaz. Özgün örnek için veriler kullanılır.

     * **Herhangi bir konakta sanal makine olarak kurtar**' ı seçerseniz, **hedef belirtin** ekranında **ESXi Konağı, kaynak havuzu, klasör** ve **yol**bilgilerini girin.

      ![Kurtarma türünü seçin](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. **Özet** ekranında, ayarlarınızı gözden geçirin ve kurtarma işlemini başlatmak için **kurtar** ' ı tıklatın. **Kurtarma durumu** ekranı, kurtarma işleminin ilerlemesini gösterir.

## <a name="restore-an-individual-file-from-a-vm"></a>Bir VM 'den tek bir dosyayı geri yükleme

Korunan bir VM kurtarma noktasından tek tek dosyaları geri yükleyebilirsiniz. Bu özellik yalnızca Windows Server VM 'Leri için kullanılabilir. Tek tek dosyaları geri yükleme, VMDK 'ye gözatıp istediğiniz dosya (ler) i, kurtarma işlemini başlatmadan önce, tüm VM 'yi geri yüklemeye benzer. Tek bir dosyayı kurtarmak veya bir Windows Server VM 'sinden dosya seçmek için:

1. MABS Yönetici Konsolu, **Kurtarma** görünümü ' ne tıklayın.

2. Kurtarmak istediğiniz VM 'yi bulmak için, **tarama** bölmesini kullanın veya filtre uygulayın. Bir VM veya klasör seçtiğinizde, bölmedeki Kurtarma noktaları kullanılabilir kurtarma noktalarını görüntüler.

    ![Kullanılabilir kurtarma noktaları](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. **Için kurtarma noktaları:** bölmesinde, istenen kurtarma noktası (ler) i içeren tarihi seçmek için takvimi kullanın. Yedekleme ilkesinin nasıl yapılandırıldığına bağlı olarak, tarihler birden fazla kurtarma noktasına sahip olabilir. Kurtarma noktasının alındığı günü seçtikten sonra, doğru **kurtarma süresini**seçtiğinizden emin olun. Seçilen tarihin birden çok kurtarma noktası varsa, kurtarma zamanı açılır menüsünde bunu seçerek kurtarma noktanızı seçin. Kurtarma noktasını seçtikten sonra kurtarılabilir öğelerin listesi **yol:** bölmesinde görünür.

4. Kurtarmak istediğiniz dosyaları bulmak için, **yol** bölmesinde **kurtarılabilir öğe** sütunundaki öğeye çift tıklayarak açın. Kurtarmak istediğiniz dosya, dosya veya klasörleri seçin. Birden çok öğe seçmek için, her öğeyi seçerken **CTRL** tuşuna basın. **Kurtarılabilir öğe** sütununda görünen dosya veya klasör listesinde arama yapmak için **yol** bölmesini kullanın. **Aşağıdaki arama listesi** alt klasörlerde arama yapmaz. Alt klasörlerde arama yapmak için klasörü çift tıklayın. Alt bir klasörden üst klasöre geçmek için **yukarı** düğmesini kullanın. Birden çok öğe (dosya ve klasör) seçebilirsiniz, ancak aynı ana klasörde olmaları gerekir. Aynı kurtarma işinde birden çok klasörden öğe kurtaramazsınız.

5. Kurtarma için öğe (ler) i seçtiğinizde, Yönetici Konsolu araç şeridinde **Kurtarma Sihirbazı**'nı açmak için **kurtar** ' ı tıklatın. Kurtarma Sihirbazı 'nda, **Kurtarma seçimini İncele** ekranı, kurtarılacak seçili öğeleri gösterir.
    ![kurtarma seçimini gözden geçirin](./media/restore-azure-backup-server-vmware/review-recovery.png)

6. **Kurtarma seçeneklerini belirtin** ekranında, ağ bant genişliği azaltmayı etkinleştirmek istiyorsanız **Değiştir**' e tıklayın. Ağ azaltmayı devre dışı bırakmak için **İleri**'ye tıklayın. Bu sihirbaz ekranında, VMware VM 'Leri için başka bir seçenek bulunmamaktadır. Ağ bant genişliği kısıtlaması ' nı değiştirmeyi seçerseniz, kısıtlama iletişim kutusunda **ağ bant genişliği kullanımını azaltmayı etkinleştir** ' i seçerek açın. Etkinleştirildikten sonra **ayarları** ve **iş zamanlamasını**yapılandırın.
7. **Kurtarma türünü seçin** ekranında **İleri**' ye tıklayın. Yalnızca dosya veya klasör (ler) i bir ağ klasörüne kurtarabilirsiniz.
8. **Hedef belirtin** ekranında, dosyalarınız veya klasörleriniz için bir ağ konumu bulmak üzere **Araştır** ' a tıklayın. MABS, kurtarılan tüm öğelerin kopyalandığı bir klasör oluşturur. Klasör adı, MABS_day-ay-yıl ön ekine sahiptir. Kurtarılan dosyalar veya klasör için bir konum seçtiğinizde, bu konumun (hedef, hedef yol ve kullanılabilir alan) ayrıntıları sağlanır.

       ![Specify location to recover files](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. **Kurtarma seçeneklerini belirtin** ekranında, hangi güvenlik ayarının uygulanacağını seçin. Ağ bant genişliği kullanımını azaltmayı tercih edebilirsiniz, ancak daraltma varsayılan olarak devre dışıdır. Ayrıca, **San kurtarma** ve **bildirim** etkin değildir.
10. **Özet** ekranında, ayarlarınızı gözden geçirin ve kurtarma işlemini başlatmak için **kurtar** ' ı tıklatın. **Kurtarma durumu** ekranı, kurtarma işleminin ilerlemesini gösterir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Backup Sunucusu kullanırken sorun giderme sorunları için, [Azure Backup sunucusu için sorun giderme kılavuzunu](./backup-azure-mabs-troubleshoot.md)gözden geçirin.
