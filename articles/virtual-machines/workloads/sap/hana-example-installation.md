---
title: Azure 'da SAP HANA HANA 'ya nasıl yüklenir (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA HANA 'yı (büyük örnekler) nasıl yükleyeceğiniz.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d266f458894d93540977c995ff7e8ab71414083f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101272"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Azure 'da SAP HANA HANA 'yı (büyük örnekler)

Azure 'da (büyük örnekler) SAP HANA HANA 'yı yüklemek için, önce aşağıdakileri yapmanız gerekir:
- Microsoft 'un dağıtım için tüm verileri SAP HANA büyük bir örnekte sağlamanız gerekir.
- Microsoft 'tan SAP HANA büyük bir örnek alırsınız.
- Şirket içi ağınıza bağlı bir Azure sanal ağı oluşturursunuz.
- HANA büyük örnekleri için ExpressRoute devresini aynı Azure sanal ağına bağlanırsınız.
- HANA büyük örnekler için bir sıçrama kutusu olarak kullandığınız bir Azure sanal makinesini yüklersiniz.
- Geçiş kutusundan HANA büyük örnek birimize bağlanabildiğinizden ve bunun tersini elde edebilirsiniz.
- Gerekli tüm paketlerin ve düzeltme eklerinin yüklü olup olmadığını kontrol edersiniz.
- Kullanmakta olduğunuz işletim sisteminde HANA yüklemesiyle ilgili SAP notlarını ve belgelerini okuyabilirsiniz. Tercih edilen HANA sürümünün işletim sistemi sürümünde desteklendiğinden emin olun.

Sonraki bölümde, HANA yükleme paketlerinin geçiş kutusu sanal makinesine indirilmesiyle bir örnek gösterilmektedir. Bu durumda, işletim sistemi Windows olur.

## <a name="download-the-sap-hana-installation-bits"></a>SAP HANA yükleme bitlerini indirin
HANA büyük örnek birimleri doğrudan internet 'e bağlı değildir. Yükleme paketlerini SAP 'den HANA büyük örnek sanal makinesine doğrudan indiremez. Bunun yerine, paketleri doğrudan geçiş kutusu sanal makinesine indirirler.

SAP Market 'e erişmenize izin veren bir SAP S-User veya başka bir kullanıcı olması gerekir.

1. Oturum açın ve [SAP hizmeti marketi](https://support.sap.com/en/index.html)' ne gidin.  >  **Yazılım** > yüklemelerini indir ve**alfabetik dizine göre** **Yükselt '** i seçin. Ardından **H – SAP HANA platform Edition** > **SAP HANA platform Edition 2,0** > **yüklemesi**altında öğesini seçin. Aşağıdaki ekran görüntüsünde gösterilen dosyaları indirin.

   ![İndirilecek dosyaların ekran görüntüsü](./media/hana-installation/image16_download_hana.PNG)

2. Bu örnekte SAP HANA 2,0 yükleme paketi indiriyoruz. Azure geçiş kutusu sanal makinesinde, kendiliğinden ayıklanan arşivleri aşağıda gösterildiği gibi dizine genişletin.

   ![Kendiliğinden ayıklanan arşivin ekran görüntüsü](./media/hana-installation/image17_extract_hana.PNG)

3. Arşivler ayıklandığında, ayıklama tarafından oluşturulan dizini (Bu örnekte 51052030) kopyalayın. HANA büyük örnek birimi/Hana/Shared Volume ' den dizini oluşturduğunuz bir dizine kopyalayın.

   > [!Important]
   > Yükleme paketlerini kök veya önyükleme LUN 'una kopyalamayın, çünkü alan sınırlıdır ve diğer işlemlerin de kullanılması gerekir.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>HANA büyük örnek birimine SAP HANA yüklemesi
SAP HANA yüklemek için, Kullanıcı kökü olarak oturum açın. SAP HANA yüklemek için yalnızca kök yeterli izinlere sahip. Üzerine kopyaladığınız dizin üzerindeki izinleri/Hana/sharedolarak ayarlayın.

```
chmod –R 744 <Installation bits folder>
```

Grafik Kullanıcı arabirimi kurulumunu kullanarak SAP HANA yüklemek istiyorsanız, GTK2 paketinin HANA büyük örneklerine yüklenmesi gerekir. Yüklü olup olmadığını denetlemek için aşağıdaki komutu çalıştırın:

```
rpm –qa | grep gtk2
```

(Sonraki adımlarda, grafik kullanıcı arabirimiyle SAP HANA kurulumunu gösteriyoruz.)

Yükleme dizinine gidin ve HDB_LCM_LINUX_X86_64 alt dizinine gidin. 

Bu dizinden önce şunu başlatın:

```
./hdblcmgui 
```
Bu noktada, yükleme için verileri sağladığınız bir dizi ekranda ilerlemeniz gerekir. Bu örnekte, SAP HANA veritabanı sunucusunu ve SAP HANA istemci bileşenlerini yüklüyorsunuz. Bu nedenle, seçimimiz **SAP HANA veritabanıdır**.

![SAP HANA veritabanı seçiliyken SAP HANA yaşam döngüsü yönetimi ekranının ekran görüntüsü](./media/hana-installation/image18_hana_selection.PNG)

Sonraki ekranda, **Yeni sistem 'ı yükler**' i seçin.

![Yeni Sistem Kur seçiliyken SAP HANA yaşam döngüsü yönetimi ekranının ekran görüntüsü](./media/hana-installation/image19_select_new.PNG)

Sonra, yükleyebileceğiniz birkaç ek bileşen arasından seçim yapın.

![Ek bileşenler listesiyle SAP HANA yaşam döngüsü yönetimi ekranının ekran görüntüsü](./media/hana-installation/image20_select_components.PNG)

Burada SAP HANA Istemcisi ve SAP HANA Studio seçeceğiz. Ayrıca bir ölçek artırma örneği de yükledik. Sonra **tek konak sistemi**' ni seçin. 

![Tek konak sistemi seçiliyken SAP HANA yaşam döngüsü yönetimi ekranının ekran görüntüsü](./media/hana-installation/image21_single_host.PNG)

Daha sonra bazı verileri sağlayın.

![SAP HANA yaşam döngüsü yönetimi ekranının ekran görüntüsü, sistem özellikleri alanları tanımlanacak](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> HANA sistem KIMLIĞI (SID) olarak, HANA büyük örnek dağıtımını sipariş ettiğinizde Microsoft 'u sağladığınız aynı SID 'yi sağlamanız gerekir. Farklı bir SID 'nin seçilmesi, farklı birimlerdeki erişim izni sorunları nedeniyle yüklemenin başarısız olmasına neden olur.

Yükleme yolu için,/Hana/Shared dizinini kullanın. Bir sonraki adımda, HANA veri dosyaları ve HANA günlük dosyaları için konumlar sağlarsınız.


![Veri ve günlük alanı alanlarıyla SAP HANA yaşam döngüsü yönetimi ekranının ekran görüntüsü](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Sistem özelliklerini tanımladığınızda belirttiğiniz SID (iki ekran önce), bağlama noktalarının SID 'SI ile eşleşmelidir. Bir uyuşmazlık varsa, geri dönün ve SID 'yi bağlama noktalarında sahip olduğunuz değere ayarlayın.

Sonraki adımda, ana bilgisayar adını gözden geçirin ve sonuç olarak düzeltin. 

![Ana bilgisayar adı ile SAP HANA yaşam döngüsü yönetimi ekranının ekran görüntüsü](./media/hana-installation/image24_review_host_name.PNG)

Bir sonraki adımda, HANA büyük örnek dağıtımını sipariş ettiğinizde Microsoft 'a verdiğiniz verileri de almanız gerekir. 

![SAP HANA yaşam döngüsü yönetiminin ekran görüntüsü, Sistem Yöneticisi alanları tanımlama](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Birim dağıtımını sıralamayla, Microsoft 'a sağladığınız aynı **Sistem Yöneticisi Kullanıcı kimliğini** ve **kimliğini** belirtin. Aksi halde, HANA büyük örnek birimine SAP HANA yüklemesi başarısız olur.

Sonraki iki ekran burada gösterilmez. Bu kişiler SAP HANA veritabanının SISTEM kullanıcısının parolasını ve sapadm kullanıcısının parolasını sağlamanıza olanak tanır. İkincisi, SAP HANA veritabanı örneğinin bir parçası olarak yüklenen SAP konak Aracısı için kullanılır.

Parolayı tanımladıktan sonra bir onay ekranı görürsünüz. listelenen tüm verileri denetleyin ve yükleme işlemine devam edin. Aşağıdaki gibi, yükleme ilerlemesini belgeleyen bir ilerleme ekranına ulaşabilirsiniz:

![SAP HANA yaşam döngüsü yönetimi ekranının ekran görüntüsü, yükleme ilerleme göstergesi](./media/hana-installation/image27_show_progress.PNG)

Yükleme tamamlandığında aşağıdakine benzer bir ekran görmeniz gerekir:

![Yükleme bittiğini belirten SAP HANA yaşam döngüsü yönetimi ekranının ekran görüntüsü](./media/hana-installation/image28_install_finished.PNG)

SAP HANA örnek artık çalışır durumda olmalıdır ve kullanıma hazır hale gelir. SAP HANA Studio 'dan bu sunucuya bağlanmanız gerekir. Ayrıca, en son güncelleştirmeleri denetleyip uygulamayı seçtiğinizden emin olun.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure 'da yüksek kullanılabilirlik ve olağanüstü durum kurtarma SAP HANA Büyük Örnekleri](hana-overview-high-availability-disaster-recovery.md)

