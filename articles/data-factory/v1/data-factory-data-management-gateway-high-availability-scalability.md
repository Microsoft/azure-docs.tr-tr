---
title: Azure Data Factory 'de veri yönetimi ağ geçidi ile yüksek kullanılabilirlik
description: Bu makalede, bir düğüm üzerinde çalışabilecek eşzamanlı iş sayısını artırarak daha fazla düğüm ekleyerek ve ölçeği ölçeklendirerek bir veri yönetimi ağ geçidini nasıl ölçeklendirebileceğiniz açıklanır.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: c3428019fe23e3f206e763249a18e7774bab149b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682692"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Veri Yönetimi ağ geçidi-yüksek kullanılabilirlik ve ölçeklenebilirlik (Önizleme)
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [içinde şirket içinde barındırılan tümleştirme çalışma zamanı](../create-self-hosted-integration-runtime.md). 


Bu makale, Veri Yönetimi ağ geçidi/tümleştirme ile yüksek kullanılabilirlik ve ölçeklenebilirlik çözümünü yapılandırmanıza yardımcı olur.    

> [!NOTE]
> Bu makalede, Integration Runtime (önceki Veri Yönetimi ağ geçidi) temel bilgileri hakkında zaten bilgi sahibi olduğunuz varsayılır. Değilseniz, [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md)' ne bakın.
> 
> **Bu önizleme özelliği, 2.12. xxxx. x ve üzeri veri yönetimi Gateway sürümünde resmi olarak desteklenir**. Lütfen 2.12. xxxx. x sürümünü kullandığınızdan emin olun. Veri Yönetimi Gateway 'in en son sürümünü [buradan](https://www.microsoft.com/download/details.aspx?id=39717)indirin.

## <a name="overview"></a>Genel Bakış
Portaldan tek bir mantıksal ağ geçidine sahip birden çok şirket içi makinede yüklü olan veri yönetimi ağ geçitlerini ilişkilendirebilirsiniz. Bu makinelere **düğüm**denir. Bir mantıksal ağ geçidiyle ilişkili en fazla **dört düğümünüz** olabilir. Mantıksal bir ağ geçidi için birden çok düğüme (ağ geçidine sahip şirket içi makineler) sahip olmanın avantajları şunlardır:  

- Şirket içi ve bulut veri depoları arasında veri taşıma performansını geliştirir.  
- Düğümlerden biri bir nedenle daha fazla olursa, diğer düğümler de verilerin taşınması için kullanılabilir. 
- Düğümlerden birinin bakım için çevrimdışına alınması gerekiyorsa, diğer düğümler de verilerin taşınmasında kullanılabilir.

Ayrıca, şirket içi ve bulut veri depoları arasında veri taşıma yeteneğini ölçeklendirmek için bir düğümde çalışabilecek **eşzamanlı veri taşıma işlerinin** sayısını yapılandırabilirsiniz. 

Azure portal kullanarak, bu düğümlerin durumunu izleyebilirsiniz. Bu, mantıksal ağ geçidine bir düğüm eklenip ekleyemeyeceğine karar vermenize yardımcı olur. 

## <a name="architecture"></a>Mimari 
Aşağıdaki diyagramda Veri Yönetimi ağ geçidinin ölçeklenebilirlik ve kullanılabilirlik özelliğine ilişkin mimariye genel bakış sunulmaktadır: 

![Veri Yönetimi ağ geçidi-yüksek kullanılabilirlik ve ölçeklenebilirlik](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

**Mantıksal ağ geçidi** , Azure Portal bir veri fabrikasına eklediğiniz ağ geçidindir. Daha önce, bir mantıksal ağ geçidiyle yüklenmiş Veri Yönetimi ağ geçidi ile yalnızca bir şirket içi Windows makinesini ilişkilendirebiliyordunuz. Bu şirket içi ağ geçidi makinesine düğüm denir. Şimdi, mantıksal bir ağ geçidi ile en fazla **dört fiziksel düğüm** ilişkilendirebilirsiniz. Birden çok düğüme sahip bir mantıksal ağ geçidine **çok düğümlü ağ geçidi**denir.  

Tüm bu düğümler **etkindir**. Bunlar, verileri şirket içi ve bulut veri depoları arasında taşımak için veri taşıma işlerini işleyebilir. Düğümlerden biri hem dağıtıcı hem de çalışan işlevi görür. Gruplardaki diğer düğümler çalışan düğümlerdir. Bir **dağıtıcı** düğümü, bulut hizmetinden veri taşıma görevleri/işleri çeker ve bunları çalışan düğümlerine (kendisi dahil) dağıtır. Bir **çalışan** düğümü, verileri şirket içi ve bulut veri depoları arasında taşımak için veri taşıma işlerini yürütür. Tüm düğümler çalışanlardır. Yalnızca bir düğüm hem dağıtım hem de çalışan olabilir.    

Genellikle bir düğüm ile başlayabilir ve varolan düğüm (ler) veri taşıma yüküne göre daha fazla düğüm eklemek için **ölçeği ölçeklendirmeniz** gerekebilir. Ayrıca, düğümde çalışmasına izin verilen eşzamanlı iş sayısını artırarak bir ağ geçidi düğümünün veri taşıma özelliğini de **ölçeklendirebilirsiniz** . Bu özellik, tek düğümlü bir ağ geçidiyle (ölçeklenebilirlik ve kullanılabilirlik özelliği etkinleştirilmediği halde) da kullanılabilir. 

Birden çok düğüme sahip bir ağ geçidi, veri deposu kimlik bilgilerini tüm düğümlerde eşitlenmiş halde tutar. Düğümden düğüme bağlantı sorunu varsa, kimlik bilgileri eşitlenmemiş olabilir. Bir ağ geçidi kullanan bir şirket içi veri deposu için kimlik bilgilerini ayarladığınızda, kimlik bilgilerini dağıtıcı/çalışan düğümünde kaydeder. Dağıtıcı düğümü diğer çalışan düğümleriyle eşitlenir. Bu işlem **kimlik bilgileri eşitleme**olarak bilinir. Düğümler arasındaki iletişim kanalı, ortak bir SSL/TLS sertifikası ile **şifrelenebilir** . 

## <a name="set-up-a-multi-node-gateway"></a>Çok düğümlü bir ağ geçidi ayarlama
Bu bölümde, aşağıdaki iki makale veya bu makalelerdeki kavramlarla ilgili bilgi sahibi olduğunuz varsayılır: 

- [Veri yönetimi Gateway](data-factory-data-management-gateway.md) -ağ geçidine ayrıntılı bir genel bakış sağlar.
- Şirket [içi ve bulut veri depoları arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) -tek bir düğüme sahip bir ağ geçidi kullanmak için adım adım yönergeler içeren bir yönerge içerir.  

> [!NOTE]
> Bir veri yönetimi ağ geçidini şirket içi bir Windows makinesine yüklemeden önce, [ana makalede](data-factory-data-management-gateway.md#prerequisites)listelenen Önkoşullar bölümüne bakın.

1. [İzlenecek yolda](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), mantıksal bir ağ geçidi oluştururken **yüksek kullanılabilirlik & ölçeklenebilirlik** özelliğini etkinleştirin. 

    ![Veri Yönetimi ağ geçidi-yüksek kullanılabilirliği ve ölçeklenebilirliği etkinleştirin](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. **Yapılandırma** sayfasında, ilk düğüme (Şirket içi Windows makinesi) bir ağ geçidi yüklemek Için **Hızlı Kurulum** veya **el ile kurulum** bağlantısı ' nı kullanın.

    ![Veri Yönetimi ağ geçidi-hızlı veya el ile kurulum](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Hızlı kurulum seçeneğini kullanırsanız, düğümden düğüme iletişim şifrelenmeden yapılır. Düğüm adı makine adı ile aynı. Düğüm düğümü iletişiminin şifrelenmesi gerekiyorsa veya tercih ettiğiniz bir düğüm adını belirtmek istiyorsanız, el ile kurulumu kullanın. Düğüm adları daha sonra düzenlenemez.
3. **Hızlı Kurulum** seçeneğini belirlerseniz
    1. Ağ Geçidi başarıyla yüklendikten sonra aşağıdaki iletiyi görürsünüz:

        ![Veri Yönetimi ağ geçidi-hızlı kurulum başarısı](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. [Bu yönergeleri](data-factory-data-management-gateway.md#configuration-manager)izleyerek ağ geçidi için veri yönetimi Configuration Manager başlatın. Ağ geçidi adı, düğüm adı, durum vb. görüntülenir.

        ![Veri Yönetimi ağ geçidi-yükleme başarılı](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. **El ile kurulum**' ı seçerseniz:
    1. Yükleme paketini Microsoft Indirme Merkezi ' nden indirin, makinenizde ağ geçidini yüklemek için bu paketi çalıştırın.
    2. Ağ geçidini kaydetmek için **Yapılandır** sayfasından **kimlik doğrulama anahtarını** kullanın.
    
        ![Veri Yönetimi ağ geçidi-yükleme başarılı](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. **Yeni ağ geçidi düğümü** sayfasında, ağ geçidi düğümüne özel bir **ad** sağlayabilirsiniz. Varsayılan olarak, düğüm adı makine adıyla aynıdır.    

        ![Veri Yönetimi ağ geçidi-ad belirtin](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Sonraki sayfada, **düğümden düğüme iletişim için şifrelemeyi etkinleştirip etkinleştirmeyeceğinizi**seçebilirsiniz. Şifrelemeyi devre dışı bırakmak için **Atla** ' ya tıklayın (varsayılan).

        ![Veri Yönetimi ağ geçidi-şifrelemeyi etkinleştir](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Şifreleme modunun değiştirilmesi yalnızca mantıksal ağ geçidinde tek bir ağ geçidi düğümünüz olduğunda desteklenir. Bir ağ geçidi birden çok düğüme sahip olduğunda şifreleme modunu değiştirmek için şu adımları uygulayın: bir düğüm hariç tüm düğümleri silin, şifreleme modunu değiştirin ve ardından düğümleri yeniden ekleyin.
        > 
        > TLS/SSL sertifikası kullanma gereksinimlerinin listesi için bkz. [TLS/SSL sertifika gereksinimleri](#tlsssl-certificate-requirements) bölümü. 
    5. Ağ Geçidi başarıyla yüklendikten sonra Configuration Manager Başlat ' a tıklayın:
    
        ![El ile kurulum-yapılandırma yöneticisini başlatma](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. bağlantı durumu, **ağ geçidi adı**ve **düğüm adı**' nı gösteren düğümde (Şirket içi Windows makinesi) veri yönetimi ağ geçidi Configuration Manager görürsünüz.  

        ![Veri Yönetimi ağ geçidi-yükleme başarılı](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Ağ geçidini bir Azure VM 'de sağlıyorsanız, [bu Azure Resource Manager şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway)kullanabilirsiniz. Bu betik bir mantıksal ağ geçidi oluşturur, Veri Yönetimi Gateway yazılımının yüklü olduğu VM 'Leri ayarlar ve bunları mantıksal ağ geçidine kaydeder. 
6. Azure portal, **ağ geçidi** sayfasını başlatın: 
    1. Portalın Data Factory giriş sayfasında, **bağlı hizmetler**' e tıklayın.
    
        ![Data factory giriş sayfası](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. **ağ geçidi** sayfasını görmek için **ağ geçidini** seçin:
    
        ![Data factory giriş sayfası](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. **Ağ geçidi** sayfasını görürsünüz:   

        ![Tek düğümlü görünümü olan ağ geçidi](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Mantıksal ağ geçidine bir düğüm eklemek için araç çubuğunda **düğüm Ekle** ' ye tıklayın. Hızlı kurulum kullanmayı planlıyorsanız, ağ geçidine düğüm olarak eklenecek şirket içi makineden bu adımı izleyin. 

    ![Veri Yönetimi ağ geçidi-düğüm Ekle menüsü](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Adımlar ilk düğümü ayarlamaya benzerdir. El ile yükleme seçeneğini belirlerseniz Configuration Manager Kullanıcı arabirimi, düğüm adını ayarlamanıza olanak sağlar: 

    ![Configuration Manager-ikinci ağ geçidini yükler](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Ağ Geçidi, düğüme başarıyla yüklendikten sonra, Configuration Manager aracı aşağıdaki ekranı görüntüler:  

    ![Configuration Manager-ikinci ağ geçidini başarıyla yüklein](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Portalda **ağ geçidi** sayfasını açarsanız, şu anda iki ağ geçidi düğümü görürsünüz: 

    ![Portalda iki düğüme sahip ağ geçidi](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Bir ağ geçidi düğümünü silmek için araç çubuğunda **düğümü sil** ' e tıklayın, silmek istediğiniz düğümü seçin ve ardından araç çubuğundan **Sil** ' e tıklayın. Bu eylem seçili düğümü gruptan siler. Bu eylemin veri yönetimi ağ geçidi yazılımını düğümden (Şirket içi Windows makinesi) kaldırmadığını unutmayın. Ağ geçidini kaldırmak için Şirket içindeki Denetim Masası 'ndaki **Program Ekle veya Kaldır** 'ı kullanın. Ağ geçidini düğümden kaldırdığınızda Portal 'da otomatik olarak silinir.   

## <a name="upgrade-an-existing-gateway"></a>Var olan bir ağ geçidini yükseltme
Yüksek kullanılabilirlik ve ölçeklenebilirlik özelliğini kullanmak için var olan bir ağ geçidini yükseltebilirsiniz. Bu özellik yalnızca sürüm > = 2.12. xxxx olan veri yönetimi ağ geçidine sahip düğümlerle birlikte kullanılabilir. Veri Yönetimi ağ geçidi Configuration Manager **Yardım** sekmesinde bir makinede yüklü olan veri yönetimi ağ geçidi sürümünü görebilirsiniz. 

1. Şirket içi makinedeki ağ geçidini, [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=39717)' nden bir MSI kurulum paketini indirip çalıştırarak aşağıdaki şekilde en son sürüme güncelleştirin. Ayrıntılar için [yükleme](data-factory-data-management-gateway.md#installation) bölümüne bakın.  
2. Azure portal gidin. Veri fabrikanızın **Data Factory sayfasını** başlatın. Bağlı hizmetler bölmesini başlatmak için bağlı hizmetler kutucuğuna **tıklayın.** **Ağ geçidi sayfasını**başlatmak için ağ geçidini seçin. Aşağıdaki görüntüde gösterildiği gibi **Önizleme özelliğini** tıklatın ve etkinleştirin: 

    ![Veri Yönetimi ağ geçidi-önizleme özelliğini etkinleştirme](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Portalda önizleme özelliği etkinleştirildikten sonra tüm sayfalar ' ı kapatın. Yeni önizleme Kullanıcı arabirimini (UI) görmek için **ağ geçidi sayfasını** yeniden açın.
 
    ![Veri Yönetimi ağ geçidi-önizleme özelliğini etkinleştirme başarısı](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Veri Yönetimi ağ geçidi-önizleme Kullanıcı arabirimi](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Yükseltme sırasında, ilk düğümün adı makinenin adıdır. 
3. Şimdi bir düğüm ekleyin. **Ağ geçidi** sayfasında, **düğüm Ekle**' ye tıklayın.  

    ![Veri Yönetimi ağ geçidi-düğüm Ekle menüsü](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Düğümü ayarlamak için önceki bölümdeki yönergeleri izleyin. 

### <a name="installation-best-practices"></a>En iyi yükleme uygulamaları

- Makinenin hazırda bekletmeden olmaması için, ağ geçidinin konak makinesinde güç planını yapılandırın. Ana makine hazırda beklemesi durumunda, ağ geçidi veri isteklerine yanıt vermez.
- Ağ geçidiyle ilişkili sertifikayı yedekleyin.
- İdeal performans için tüm düğümlerin benzer yapılandırma (önerilir) olduğundan emin olun. 
- Yüksek kullanılabilirlik sağlamak için en az iki düğüm ekleyin.  

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL sertifikası gereksinimleri
Tümleştirme çalışma zamanı düğümleri arasındaki iletişimin güvenliğini sağlamak için kullanılan TLS/SSL sertifikasına yönelik gereksinimler şunlardır:

- Sertifika, genel olarak güvenilen bir x509 v3 sertifikası olmalıdır. Ortak (üçüncü taraf) sertifika yetkilisi (CA) tarafından verilen sertifikaları kullanmanızı öneririz.
- Her Integration Runtime düğümü, kimlik bilgileri Yöneticisi uygulamasını çalıştıran istemci makineye ve bu sertifikaya güvenmelidir. 
  > [!NOTE]
  > Kimlik bilgisi Yöneticisi uygulaması, kopyalama Sihirbazı/Azure portalından kimlik bilgileri güvenli bir şekilde ayarlanırken kullanılır. Bu, şirket içi/özel veri deposuyla aynı ağ içindeki herhangi bir makineden tetiklenebilir.
- Joker karakter sertifikaları desteklenir. FQDN adınız **node1.domain.contoso.com**ise, sertifikanın konu adı olarak * **. domain.contoso.com** kullanabilirsiniz.
- Yalnızca konu diğer adlarının son öğesi kullanıldığından ve geçerli kısıtlama nedeniyle diğerleri yok sayılacak olduğundan, SAN sertifikaları önerilmez. Örneğin SAN **node1.domain.contoso.com** ve **node2.DOMAIN.contoso.com**olan bir San sertifikanız varsa, bu sertifikayı yalnızca FQDN 'si **node2.domain.contoso.com**olan makine üzerinde kullanabilirsiniz.
- SSL sertifikaları için Windows Server 2012 R2 tarafından desteklenen anahtar boyutunu destekler.
- CNG anahtarlarını kullanan sertifika desteklenmiyor.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>SSS: Bu şifrelemeyi ne zaman etkinleştirebilirim?
Şifrelemeyi etkinleştirme, altyapınıza (sahip olan ortak sertifikaya) belirli bir maliyeti ekleyebilir, bu nedenle aşağıdaki durumlarda şifrelemeyi etkinleştirmeyi atlayabilirsiniz:
- Tümleştirme çalışma zamanı güvenilen bir ağda veya IP/sn gibi saydam şifrelemeye sahip bir ağ üzerinde çalışırken. Bu kanal iletişimi yalnızca güvenilen ağınız dahilinde sınırlı olduğundan, ek şifrelemeye ihtiyacınız olmayabilir.
- Tümleştirme çalışma zamanı bir üretim ortamında çalışmadığı zaman. Bu, TLS/SSL sertifika maliyetini azaltmaya yardımcı olabilir.


## <a name="monitor-a-multi-node-gateway"></a>Çok düğümlü bir ağ geçidini izleme
### <a name="multi-node-gateway-monitoring"></a>Çok düğümlü ağ geçidi izleme
Azure portal, her düğümde (CPU, bellek, ağ (gelen/giden) ve ağ geçidi düğümlerinin durumuyla birlikte, kaynak kullanımının neredeyse gerçek zamanlı anlık görüntüsünü görüntüleyebilirsiniz. 

![Veri Yönetimi ağ geçidi-birden çok düğüm izleme](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Ağ Geçidi sayfasındaki **Gelişmiş ayarları** , ağ **geçidi sorunlarını** ayıklama konusunda yararlı olan **ağ**(ın/out), **rol & kimlik bilgisi durumu**ve **eşzamanlı işler** (çalıştırma/sınırlama) gibi gelişmiş ölçümleri görmek için etkinleştirebilirsiniz ), performans ayarlaması sırasında değiştirilebilen/değiştirilebilen bir şekilde değiştirilebilir. Aşağıdaki tabloda **ağ geçidi düğümleri** listesindeki sütunların açıklamaları verilmiştir:  

İzleme özelliği | Açıklama
:------------------ | :---------- 
Ad | Ağ geçidiyle ilişkili mantıksal ağ geçidinin ve düğümlerin adı.  
Durum | Mantıksal ağ geçidinin ve ağ geçidi düğümlerinin durumu. Örnek: çevrimiçi/çevrimdışı/sınırlı/vb. Bu durumlar hakkında daha fazla bilgi için bkz. [ağ geçidi durumu](#gateway-status) bölümü. 
Sürüm | Mantıksal ağ geçidi ve her ağ geçidi düğümünün sürümünü gösterir. Mantıksal ağ geçidinin sürümü, gruptaki düğümlerin çoğunluğu sürümüne göre belirlenir. Mantıksal ağ geçidi kurulumunda farklı sürümlere sahip düğümler varsa, yalnızca mantıksal ağ geçidi ile aynı sürüm numarasına sahip düğümler düzgün şekilde çalışır. Diğerleri sınırlı moddadır ve el ile güncelleştirilmesi gerekir (yalnızca büyük/küçük harfe otomatik güncelleştirme başarısız olur). 
Available memory | Bir ağ geçidi düğümündeki kullanılabilir bellek. Bu değer, neredeyse gerçek zamanlı anlık görüntüsüdür. 
CPU utilization | Bir ağ geçidi düğümünün CPU kullanımı. Bu değer, neredeyse gerçek zamanlı anlık görüntüsüdür. 
Networking (In/Out) | Ağ Geçidi düğümünün ağ kullanımı. Bu değer, neredeyse gerçek zamanlı anlık görüntüsüdür. 
Concurrent Jobs (Running/ Limit) | Her düğümde çalışan iş veya görev sayısı. Bu değer, neredeyse gerçek zamanlı anlık görüntüsüdür. Limit her düğüm için en fazla eş zamanlı işi belirtir. Bu değer makine boyutuna göre tanımlanır. CPU/bellek/ağın kullanıldığı, ancak etkinliklerin zaman aşımına uğramasının gerektiği Gelişmiş senaryolarda, eşzamanlı iş yürütmeyi ölçeklendirmeye yönelik sınırı artırabilirsiniz. Bu özellik, tek düğümlü bir ağ geçidiyle (ölçeklenebilirlik ve kullanılabilirlik özelliği etkinleştirilmediği halde) da kullanılabilir. Daha fazla bilgi için bkz. [Ölçek değerlendirmeleri](#scale-considerations) bölümü. 
Rol | İki tür rol – dağıtıcı ve çalışan vardır. Tüm düğümler çalışanlardır, yani işleri yürütmek için hepsi kullanılabilirler. Bulut hizmetlerinden görevler/işler çekmek ve bunları farklı çalışan düğümlerine (kendisi dahil) göndermek için kullanılan yalnızca bir dağıtıcı düğümü vardır. 

![Veri Yönetimi ağ geçidi-gelişmiş birden çok düğüm izleme](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Ağ Geçidi durumu

Aşağıdaki tabloda **ağ geçidi düğümünün**olası durumları verilmiştir: 

Durum  | Açıklamalar/senaryolar
:------- | :------------------
Çevrimiçi | Data Factory hizmetine bağlı düğüm.
Offline | Düğüm çevrimdışı.
Yükseltmenin | Düğüm otomatik olarak güncelleştiriliyor.
Sınırlı | Bağlantı sorunu nedeniyle. HTTP bağlantı noktası 8050 sorunu, Service Bus bağlantı sorunu veya kimlik bilgisi eşitleme sorunu olabilir. 
Olmadan | Düğüm, diğer çoğunluk düğümlerin yapılandırmasından farklı bir yapılandırmadır.<br/><br/> Düğüm, diğer düğümlere bağlanamıyorsa devre dışı olabilir. 


Aşağıdaki tabloda, **mantıksal bir ağ geçidinin**olası durumları verilmiştir. Ağ Geçidi, ağ geçidi düğümlerinin durumlarına bağlıdır. 

Durum | Yorumlar
:----- | :-------
Kayıt gerekiyor | Henüz bu mantıksal ağ geçidine kayıtlı düğüm yok
Çevrimiçi | Ağ Geçidi düğümleri çevrimiçi
Offline | Çevrimiçi durumda düğüm yok.
Sınırlı | Bu ağ geçidinde düğümlerin hepsi sağlıklı durumda değil. Bu durum, bir düğümün kapatılmış olabileceğini belirten bir uyarıdır! <br/><br/>Dağıtıcı/çalışan düğümündeki kimlik bilgisi eşitleme sorunundan kaynaklanıyor olabilir. 

### <a name="pipeline-activities-monitoring"></a>İşlem hattı/etkinlik izleme
Azure portal, parçalı düğüm düzeyi ayrıntılarla bir işlem hattı izleme deneyimi sağlar. Örneğin, hangi etkinliklerin hangi düğümde çalıştığını gösterir. Bu bilgiler, belirli bir düğümdeki performans sorunlarını anlamak için, ağ azaltmasından dolayı yararlı olabilir. 

![Veri Yönetimi ağ geçidi-işlem hatları için birden çok düğüm izleme](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Veri Yönetimi ağ geçidi-işlem hattı ayrıntıları](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Ölçek konuları

### <a name="scale-out"></a>Ölçeği genişletme
**Kullanılabilir bellek yetersiz** olduğunda ve **CPU kullanımı yüksek**olduğunda, yeni bir düğüm eklemek makineler arasında yükün ölçeğini belirlemenize yardımcı olur. Etkinlikler, zaman aşımı veya ağ geçidi düğümünün çevrimdışı olması nedeniyle başarısız olursa, ağ geçidine bir düğüm eklemenize yardımcı olur.
 
### <a name="scale-up"></a>Ölçeği artırma
Kullanılabilir bellek ve CPU iyi kullanılmadığında, ancak boşta kapasitesi 0 ise, bir düğümde çalışabilecek eşzamanlı iş sayısını artırarak ölçeği arttırmalısınız. Ayrıca, ağ geçidi aşırı yüklendiği için etkinlikler zaman aşımına uğradığından ölçeğini ölçeklendirmek isteyebilirsiniz. Aşağıdaki görüntüde gösterildiği gibi, bir düğüm için maksimum kapasiteyi artırabilirsiniz. İle başlamak için katlama öneririz.  

![Veri Yönetimi ağ geçidi-ölçek konuları](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Bilinen sorunlar/son değişiklikler

- Şu anda, tek bir mantıksal ağ geçidi için en fazla dört fiziksel ağ geçidi düğümünüz olabilir. Performans nedenleriyle dörtten fazla düğüme ihtiyacınız varsa [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com)adresine bir e-posta gönderin.
- Geçerli mantıksal ağ geçidinden geçiş yapmak için başka bir mantıksal ağ geçidindeki kimlik doğrulama anahtarı ile bir ağ geçidi düğümünü yeniden kaydetmeniz gerekmez. Yeniden kaydetmek için, düğümden ağ geçidini kaldırın, ağ geçidini yeniden yükleyin ve diğer mantıksal ağ geçidinin kimlik doğrulama anahtarına kaydolun. 
- Tüm ağ geçidi düğümleriniz için HTTP proxy gerekliyse, diahost. exe. config ve diawp. exe. config dosyasındaki proxy 'yi ayarlayın ve tüm düğümlerin aynı diahost. exe. config ve diawıp. exe. config dosyasına sahip olduğundan emin olmak için Sunucu Yöneticisi 'ni kullanın. Ayrıntılar için bkz. [proxy ayarlarını yapılandırma](data-factory-data-management-gateway.md#configure-proxy-server-settings) bölümü. 
- Ağ Geçidi Configuration Manager 'de düğümden düğüme iletişim için şifreleme modunu değiştirmek için, portaldaki tüm düğümleri hariç bir şekilde silin. Ardından, şifreleme modunu değiştirdikten sonra düğümleri geri ekleyin.
- Düğümden düğüme iletişim kanalını şifrelemeyi seçerseniz resmi bir SSL sertifikası kullanın. Otomatik olarak imzalanan sertifika, diğer makinelerdeki sertifika yetkilisi listesinde aynı sertifikaya güvenilmeyebilir ve bağlantı sorunlarına neden olabilir. 
- Düğüm sürümü mantıksal ağ geçidi sürümünden düşük olduğunda bir ağ geçidi düğümünü mantıksal bir ağ geçidine kaydedemezsiniz. Daha düşük bir sürüm düğümünü (indirgeme) kaydedebilmeniz için portaldan mantıksal ağ geçidinin tüm düğümlerini silin. Mantıksal bir ağ geçidinin tüm düğümlerini silerseniz, bu mantıksal ağ geçidine el ile yeni düğümler yükleyip kaydedin. Bu durumda hızlı kurulum desteklenmez.
- Mevcut bir mantıksal ağ geçidine düğümleri yüklemek için hızlı kurulum 'u kullanamazsınız. Bu, hala bulut kimlik bilgilerini kullanıyor. Ayarlar sekmesinde, kimlik bilgilerinin ağ geçidi Configuration Manager nerede depolandığını kontrol edebilirsiniz.
- Düğüm-düğüm Şifrelemesi etkin olan mevcut bir mantıksal ağ geçidine düğümleri yüklemek için hızlı kurulum 'u kullanamazsınız. Şifreleme modunun el ile sertifika ekleme ile aynı olması durumunda hızlı yüklemeye daha fazla seçenek yoktur. 
- Şirket içi ortamdan bir dosya kopyası için, localhost veya yerel sürücüye tüm düğümler aracılığıyla erişilemese de \\localhost veya C:\Dosyaları kullanmamalısınız. Bunun yerine, dosyaların konumunu belirtmek için \\Server\\files komutunu kullanın.


## <a name="rolling-back-from-the-preview"></a>Önizlemeden geri alınıyor 
Önizlemeye geri dönmek için tüm düğümleri, ancak bir düğümü silin. Hangi düğümleri sildiğinizden bağımsız olarak, mantıksal ağ geçidinde en az bir düğümünüz olduğundan emin olun. Makinede ağ geçidini kaldırarak veya Azure portal kullanarak bir düğümü silebilirsiniz. Azure portal, **Data Factory** sayfasında **, bağlı hizmetler sayfasını başlatmak** için bağlı hizmetler ' e tıklayın. **Ağ** geçidi sayfasını başlatmak için ağ geçidini seçin. Ağ Geçidi sayfasında, ağ geçidiyle ilişkili düğümleri görebilirsiniz. Sayfa, ağ geçidinden bir düğümü silmenize olanak sağlar.
 
Sildikten sonra, aynı Azure portal sayfasındaki **Önizleme özellikleri** ' ne tıklayın ve önizleme özelliğini devre dışı bırakın. Ağ geçidinizi bir Node GA (genel kullanılabilirlik) ağ geçidine sıfırladınız.


## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makaleleri gözden geçirin:
- [Veri yönetimi Gateway](data-factory-data-management-gateway.md) -ağ geçidine ayrıntılı bir genel bakış sağlar.
- Şirket [içi ve bulut veri depoları arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) -tek bir düğüme sahip bir ağ geçidi kullanmak için adım adım yönergeler içeren bir yönerge içerir. 
