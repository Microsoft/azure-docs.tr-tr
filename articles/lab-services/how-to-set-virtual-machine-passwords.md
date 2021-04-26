---
title: Azure Lab Services VM 'Leri için parola ayarlama | Microsoft Docs
description: Azure Lab Services laboratuvarlarında sanal makineler (VM) için parolaları ayarlamayı ve sıfırlamayı öğrenin.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6ae577ee4c0c7e31760e0fb12afeaeac1ef8b7e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434235"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Sanal makine havuzunu ayarlama ve yönetme 
Bu makalede aşağıdaki görevlerin nasıl yapılacağı gösterilmektedir:

- Laboratuvardaki sanal makinelerin (VM) sayısını artırın
- Tüm VM 'Leri veya seçili VM 'Leri Başlat 
- VM 'Leri sıfırlama

## <a name="update-the-lab-capacity"></a>Laboratuvar kapasitesini güncelleştirme
Laboratuvar kapasitesini artırmak veya azaltmak için (bir laboratuvardaki sanal makinelerin sayısı), aşağıdaki adımları uygulayın:

1. **Sanal makine havuzu** sayfasında, **Laboratuvar kapasitesi: &lt; numara &gt; makineleri**' ni seçin.
2. Laboratuvarda istediğiniz yeni **VM sayısını** girin. Bu sayı, laboratuvarda kayıtlı Kullanıcı sayısından büyük veya buna eşit olmalıdır. 
3. Sonra **Kaydet**' i seçin. 

    !["Laboratuvar için en fazla makine" ve "Kaydet" düğmesinin seçili olduğu "Laboratuvar kapasitesi" penceresini gösteren ekran görüntüsü.](./media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Kapasiteyi arttırdıysanız, oluşturulmakta olan sanal makineyi veya VM 'Leri görebilirsiniz. Listede yeni VM 'yi görmüyorsanız, sayfayı yenileyin. 

    ![Oluşturulmakta olan VM](./media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>VM 'Leri başlatma

### <a name="start-ot-stop-all-vms"></a>Tüm VM 'Leri durdur
1. **Sanal makine havuzu** sayfasına geçin. 
2. Araç çubuğundan **Tümünü Başlat** ' ı seçin. 

    ![Tümünü Başlat düğmesi](./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Tüm VM 'Ler başlatıldıktan sonra, araç çubuğundaki **Tümünü Durdur** düğmesini seçerek tüm VM 'leri durdurabilirsiniz. 

    ![Tümünü Durdur düğmesi](./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Seçili VM 'Leri Başlat
Seçilen VM 'Leri başlatmak için iki yol vardır (bir veya daha fazla). İlk yöntem, listedeki VM 'yi veya VM 'Leri seçmek ve sonra araç çubuğundan **Başlat** ' ı seçmeniz gerekir. 

İkinci yöntem, listede bir veya daha fazla sanal makine seçmek ve **durum** sütunundaki düğmeyi değiştirmek. 

![Seçili VM 'Leri Başlat](./media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Benzer şekilde, **durum** sütunundaki düğmeyi değiştirerek veya araç çubuğunda **Durdur** ' u seçerek bir veya daha fazla VM 'yi durdurabilirsiniz. 

> [!NOTE]
> Bir eğitimci bir öğrenci sanal makinesini açtığında öğrencinin kotası etkilenmez. Bir kullanıcı için kota, kullanıcının zamanlanan sınıf süresi dışında kullanabileceği laboratuvar saatleri sayısını belirtir. Kotalar hakkında daha fazla bilgi için bkz. [kullanıcılar için kotaları ayarlama](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="reset-vms"></a>VM 'Leri sıfırlama

Bir veya daha fazla VM 'yi sıfırlamak için listeden bunları seçin ve ardından araç çubuğunda **Sıfırla** ' yı seçin. 

![Seçili VM 'Leri Sıfırla](./media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

**Sanal makineleri Sıfırla** Iletişim kutusunda **Sıfırla**' yı seçin. 

![VM 'yi Sıfırla iletişim kutusu](./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)

## <a name="set-password-for-vms"></a>VM 'Ler için parola ayarlama
Laboratuvar sahibi (eğiticator), Laboratuvar oluşturma (Laboratuvar oluşturma Sihirbazı) sırasında veya **şablon** sayfasında laboratuvar oluşturduktan sonra VM 'lerin parolasını ayarlayabilir/sıfırlayabilir. 

### <a name="set-password-at-the-time-of-lab-creation"></a>Laboratuvar oluşturma sırasında parola ayarla
Laboratuvar sahibi (eğiticator) Laboratuvar oluşturma sihirbazının **sanal makine kimlik bilgileri** sayfasında laboratuvardaki VM 'ler için bir parola ayarlayabilir.

![Yeni laboratuvar penceresi](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Bu sayfadaki **tüm sanal makineler için aynı parolayı kullan** seçeneğini etkinleştirip devre dışı bırakarak, bir eğitimci, laboratuvardaki tüm VM 'ler için aynı parolayı kullanmayı seçebilir veya öğrencilerin VM 'leri için parola değiştirmesine izin verebilir. Varsayılan olarak, bu ayar Ubuntu hariç tüm Windows ve Linux işletim sistemi görüntüleri için etkinleştirilmiştir. Bu ayar devre dışı bırakıldığında, sanal makineye ilk kez bağlanmaya çalıştıklarında öğrencilerden bir parola ayarlaması istenir. 

### <a name="reset-password-later"></a>Parolayı daha sonra Sıfırla

1. Laboratuvarın **şablon** sayfasında, araç çubuğunda **Parolayı Sıfırla** ' yı seçin. 
1. **Parolayı Sıfırla** iletişim kutusunda bir parola girin ve **Parolayı Sıfırla**' yı seçin.
    
    ![Parolayı ayarla iletişim kutusu](./media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Öğrenci VM 'lerine bağlanma
Aşağıdaki koşullar karşılandığında laboratuvar Oluşturucu (eğiticator) bir öğrenci VM 'sine bağlanabilir: 

- Laboratuvar oluşturulurken **tüm sanal makineler için aynı parolayı kullan** seçeneği seçildi
- VM çalışıyor 

 Öğrenci VM 'sine bağlanmak için, listedeki sanal makineye fare üzerine gelin ve bilgisayar düğmesini seçin.  

![Öğrenci VM 'sine bağlanma düğmesi](./media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Eğitimci VM 'yi başlatır ve buna bağlanır, öğrenci kotası bundan etkilenmez. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Sanal makinelerin listesini bir CSV dosyasına aktar

1. **Sanal makine havuzu** sekmesine geçin.
2. Araç çubuğunda **..** . (üç nokta) simgesini seçin ve ardından **CSV 'yi dışarı aktar**' ı seçin. 

    ![Sanal makinelerin listesini dışarı aktar](./media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>Sonraki adımlar
Sizin (Laboratuvar sahibi olarak) yapılandırabileceğiniz diğer öğrenci kullanım seçenekleri hakkında bilgi edinmek için şu makaleye bakın: [öğrenci kullanımını yapılandırma](how-to-configure-student-usage.md).

Öğrencilerin VM 'lerinin parolalarını nasıl sıfırlayacaklarını öğrenmek için bkz. [Labs 'de sanal makineler için parola ayarlama veya sıfırlama (öğrenciler)](how-to-set-virtual-machine-passwords-student.md).