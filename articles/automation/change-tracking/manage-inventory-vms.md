---
title: VM 'lerden Azure Otomasyonu envanter toplamayı yönetme | Microsoft Docs
description: Bu makalede, VM 'lerden envanter toplamayı yönetme konusu açıklanır.
services: automation
ms.subservice: change-inventory-management
keywords: stok, otomasyon, değişiklik, izleme
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2acf22ba76acdfa6152ef8966f4000aa325a9e91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92210396"
---
# <a name="manage-inventory-collection-from-vms"></a>VM’lerden stok koleksiyonunu yönetme

Makinenin kaynak sayfasından bir Azure VM için stok izlemeyi etkinleştirebilirsiniz. Bilgisayarlarınızda aşağıdaki envanter bilgilerini toplayabilir ve görüntüleyebilirsiniz:

- Windows güncelleştirmeleri, Windows uygulamaları, hizmetler, dosyalar ve kayıt defteri anahtarları
- Linux yazılım paketleri, Daemon 'ları ve dosyalar

Azure Otomasyonu Değişiklik İzleme ve envanteri, envanter toplamayı ayarlamaya ve yapılandırmaya yönelik tarayıcı tabanlı bir kullanıcı arabirimi sağlar.

## <a name="before-you-begin"></a>Başlamadan önce

Azure aboneliğiniz yoksa [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

Bu makalede, Değişiklik İzleme ve envanteriyle etkinleştirilecek bir sanal makinenizin olduğunu varsaymaktadır. Bir Azure VM 'si yoksa, [BIR VM oluşturabilirsiniz](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="enable-inventory-collection-from-the-vm-resource-page"></a>VM kaynak sayfasından envanter toplamayı etkinleştir

1. Azure portalının sol tarafında **Sanal makineler**'i seçin.
2. VM 'Ler listesinde bir makine seçin.
3. **Kaynak** menüsünde, **işlemler** altında **Envanter**' ı seçin.
4. Veri günlüklerinizi depolamak için bir Log Analytics çalışma alanı seçin.
    Bu bölge için kullanabileceğiniz bir çalışma alanı yoksa, varsayılan bir çalışma alanı ve otomasyon hesabı oluşturmanız istenir.
5. Bilgisayarınızı etkinleştirmeye başlamak için **Etkinleştir**' i seçin.

   ![Ekleme seçeneklerini görüntüleme](./media/manage-inventory-vms/inventory-onboarding-options.png)

    Bir durum çubuğu, Değişiklik İzleme ve stok özelliğinin etkinleştirildiğini bildirir. Bu işlem 15 dakika kadar sürebilir. Bu süre boyunca pencereyi kapatabilir veya açık tutabilirsiniz ve özellik etkinleştirildiğinde size bildirimde bulunur. Dağıtım durumunu bildirimler bölmesinden izleyebilirsiniz.

   ![Envanteri görüntüleme](./media/manage-inventory-vms/inventory-onboarded.png)

Dağıtım tamamlandığında durum çubuğu kaybolur. Sistem stok verilerini toplamaktadır ve veriler henüz görünmeyebilir. Tam bir veri toplama işlemi 24 saat sürebilir.

## <a name="configure-your-inventory-settings"></a>Stok ayarlarınızı yapılandırma

Varsayılan olarak yazılım, Windows hizmetleri ve Linux daemon'ları toplama işlemi için yapılandırılmıştır. Windows kayıt defteri ve dosya stoğunu toplamak için stok toplama ayarlarını yapılandırın.

1. Sayım sayfasında, sayfanın üst kısmındaki **Ayarları Düzenle** ' ye tıklayın.
2. Yeni bir koleksiyon ayarı eklemek için **Windows kayıt defteri**, **Windows dosyaları** veya **Linux dosyaları** sekmesini seçerek eklemek istediğiniz ayar kategorisine gidin.
3. Uygun kategoriyi seçin ve sayfanın üst kısmındaki **Ekle** ' ye tıklayın.

Aşağıdaki bölümlerde, çeşitli kategoriler için yapılandırılabilecek her bir özellik hakkında bilgi sağlanmaktadır.

### <a name="windows-registry"></a>Windows Kayıt Defteri

|Özellik  |Açıklama  |
|---------|---------|
|Etkin     | Ayarın uygulanmış olup olmadığını belirler        |
|Öğe Adı     | İzlenecek dosyanın kolay adı        |
|Grup     | Dosyaları mantıksal bir biçimde gruplandırmaya yönelik grup adı        |
|Windows Kayıt Defteri Anahtarı   | Dosyanın denetleneceği yol. Örneğin: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>Windows Dosyaları

|Özellik  |Açıklama  |
|---------|---------|
|Etkin     | Ayar uygulanmışsa true, aksi takdirde false.        |
|Öğe Adı     | İzlenecek dosyanın kolay adı.        |
|Grup     | Dosyaları mantıksal olarak gruplandırmak için bir grup adı.       |
|Yolu girin     | Dosyanın denetlenecek yol (örneğin, **c:\temp\myfile.txt**).

### <a name="linux-files"></a>Linux dosyaları

|Özellik  |Açıklama  |
|---------|---------|
|Etkin     | Ayar uygulanmışsa true, aksi takdirde false.        |
|Öğe Adı     | İzlenecek dosyanın kolay adı.        |
|Grup     | Dosyaları mantıksal olarak gruplandırmak için bir grup adı.        |
|Yolu girin     | Dosyayı denetlenecek yol, örneğin, **/etc/*. conf**.       |
|Yol Türü     | İzlenecek öğenin türü. Değerler dosya ve dizindir.        |
|Özyineleme     | Bu öğeyi izlenecek öğe ararken, aksi takdirde false ise true.        |
|Sudo kullan     | Eğer, öğe denetlenirken, aksi takdirde false olduğunda true.         |
|Bağlantılar     | Dizin aktarılırken sembolik bağlantıların nasıl ele alınacağını gösteren değer. Olası değerler şunlardır: <br> Yoksay - Sembolik bağlantıları yoksayar ve başvurulan dosyaları veya dizinleri içermez<br>İzle - Özyineleme sırasında sembolik bağlantıları izler ve başvurulan dosyaları veya dizinleri de içerir<br>Yönet - Sembolik bağlantıları izler ve döndürülen içeriğin işlenmesinde değişiklik yapılmasına olanak sağlar      |

## <a name="manage-machine-groups"></a>Makine gruplarını yönetme

Envanter, Azure Izleyici günlüklerinde makine grupları oluşturmanıza ve görüntülemenize olanak sağlar. Makine grupları, Azure Izleyici günlüklerinde bir sorgu tarafından tanımlanan makine koleksiyonlarıdır.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Makine gruplarınızı görüntülemek için, envanter sayfasında **makine grupları** sekmesini seçin.

![Envanter sayfasında makine gruplarını görüntüleme](./media/manage-inventory-vms/inventory-machine-groups.png)

Listeden bir makine grubu seçildiğinde makine grupları sayfası açılır. Bu sayfa makine grubuyla ilgili ayrıntıları gösterir. Bu ayrıntılar, grubu tanımlamak için kullanılan Azure Izleyici günlük sorgusunu içerir. Sayfanın alt kısmında, bu grubun parçası olan makinelerin Sayfalanmış bir listesidir.

![Makine grubu sayfasını görüntüle](./media/manage-inventory-vms/machine-group-page.png)

Makine grubunu kopyalamak için **+ Kopyala** ' ya tıklayın. Gruba grup için yeni bir ad ve diğer ad vermeniz gerekir. Tanım şu anda değiştirilebilir. Sorguyu değiştirdikten sonra seçilecek makineleri önizlemek için **sorguyu doğrula** ' ya tıklayın. Gruptan memnun olduğunuzda, makine grubunu oluşturmak için **Oluştur** ' a tıklayın.

Yeni bir makine grubu oluşturmak istiyorsanız **+ makine grubu oluştur ' a** tıklayın. Bu düğme, yeni grubunuzu tanımlayabileceğiniz **makine grubu oluştur** sayfasını açar. Grubu oluşturmak için **Oluştur**’a tıklayın.

![Yeni makine grubu oluştur](./media/manage-inventory-vms/create-new-group.png)

## <a name="disconnect-your-vm-from-management"></a>VM 'nizin yönetimden bağlantısını kesme

VM 'nizi Değişiklik İzleme ve stok yönetiminden kaldırmak için:

1. Azure portal sol bölmesinde **Log Analytics**' i seçin ve ardından VM 'nizi değişiklik izleme ve envanter için etkinleştirirken kullandığınız çalışma alanını seçin.
2. **Log Analytics** sayfasında, **kaynak** menüsünü açın.
3. **Çalışma alanı veri kaynakları** altında **sanal makineler** ' i seçin.
4. Listede, bağlantısını kesmek istediğiniz VM 'yi seçin. Makinenin, **OMS bağlantı** sütununda **Bu çalışma alanının** yanında yeşil bir onay işareti vardır.

   >[!NOTE]
   >Operations Management Suite (OMS) artık Azure Izleyici günlükleri olarak adlandırılır.

5. Sonraki sayfanın en üstünde **bağlantıyı kes**' e tıklayın.
6. Onay penceresinde, makinenin yönetimi bağlantısını kesmek için **Evet** ' i tıklatın.

>[!NOTE]
>Son 24 saat içinde Envantere kaydedilen tüm makinelere rapor verdiğimiz için, makineler kaydolduktan sonra gösterilmeye devam eder. Makinenin bağlantısı kesildikten sonra, artık listelenmemesi için 24 saat beklemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* Özelliği ile çalışma hakkında ayrıntılı bilgi için bkz. [Manage değişiklik izleme and Inventory](manage-change-tracking.md).
* Yazılım değişikliklerini izleme hakkında daha fazla bilgi edinmek için bkz. [ortamınızdaki yazılım değişikliklerini izleme değişiklik izleme](overview.md).
* Özellikle ilgili sorunları gidermek için bkz. [değişiklik izleme ve envanter sorunlarını giderme](../troubleshoot/change-tracking.md).
