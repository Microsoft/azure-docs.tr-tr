---
title: Azure Dosyaları'na giriş | Microsoft Docs
description: Endüstri standardı SMB protokolünü kullanarak bulutta ağ dosya paylaşımları oluşturmanıza ve bunları kullanmanıza olanak tanıyan Azure Dosyaları hizmetine genel bir bakış.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7279c5578d969db9178ce85734e3a43f9eccdf8d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780056"
---
# <a name="what-is-azure-files"></a>Azure Dosyalar nedir?
Azure dosyaları, bulutta sektör standart [sunucu Ileti bloğu (SMB) protokolü](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) veya [ağ dosya sistemi (NFS) protokolü](https://en.wikipedia.org/wiki/Network_File_System)aracılığıyla erişilebilen tam olarak yönetilen dosya paylaşımları sunar. Azure dosya paylaşımları, bulut veya şirket içi dağıtımlar tarafından eşzamanlı olarak bağlanabilir. Azure dosyaları SMB dosya paylaşımlarına Windows, Linux ve macOS istemcilerinden erişilebilir. Azure dosyaları NFS dosya paylaşımlarına Linux veya macOS istemcilerinden erişilebilir. Ayrıca, Azure dosyaları SMB dosya paylaşımları, verilerin kullanıldığı yerden neredeyse hızlı erişim için Azure Dosya Eşitleme ile Windows sunucularında önbelleğe alınabilir.

Azure dosyalarının genel kullanım durumlarında bazı videolar aşağıda verilmiştir:
* [Dosya sunucunuzu sunucusuz bir Azure dosya paylaşımıyla değiştirin](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
* [AD kimlik doğrulamasını kullanarak Windows sanal masaüstündeki Azure dosyalarında FSLogix profil kapsayıcıları ile çalışmaya başlama](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="why-azure-files-is-useful"></a>Azure Dosyaları neden faydalıdır?
Azure dosya paylaşımları şunları yapmak için kullanılabilir:

* **Şirket içi dosya sunucularını değiştirme veya destekleme**:  
    Azure Dosyaları geleneksel şirket içi dosya sunucularını veya NAS cihazlarını tamamen değiştirmek veya desteklemek için kullanılabilir. Windows, macOS ve Linux gibi yaygın işletim sistemleri, dünyanın neresinde olursa olsun Azure dosya paylaşımlarına doğrudan bağlanabilir. Azure dosya SMB dosya paylaşımları ayrıca, kullanıldığı verilerin performansı ve dağıtılmış önbelleği için şirket içinde veya bulutta Azure Dosya Eşitleme Windows Server 'Lar ile de çoğaltılabilir. Azure dosya SMB dosya paylaşımları en son sürümü [sayesinde, erişim](storage-files-active-directory-overview.md)denetimi için şirket IÇINDE barındırılan ad ile çalışmaya devam edebilir. 

* **"Kaldırma ve kaydırma" uygulamaları**:  
    Azure Dosyaları dosya uygulamasını veya kullanıcı verilerini saklamak için bir dosya paylaşımı gerektiren uygulamaların buluta taşınmasını kolaylaştırır. Azure Dosyaları, uygulamanın ve verilerinin Azure'a taşındığı "klasik" taşıma senaryosunun yanı sıra uygulama verilerinin Azure Dosyaları'na taşındığı ve uygulamanın şirket içi ortamda çalışmaya devam ettiği "hibrit" taşıma senaryosunu destekler. 

* **Bulut geliştirmeyi basitleştirme**:  
    Azure Dosyaları ayrıca yeni bulut geliştirme projelerini kolaylaştırma amacıyla farklı şekillerde kullanılabilir. Örnek:
    * **Paylaşılan uygulama ayarları**:  
        Dağıtılmış uygulamalar için yaygın bir düzen, yapılandırma dosyalarının birçok uygulama örneğinin erişebildiği merkezi bir konumda tutulmasıdır. Uygulama örnekleri yapılandırmalarını Dosya REST API'si üzerinden yükleyebilir ve kullanıcılar bu verilere SMB paylaşımını yerel ortama bağlayarak erişebilir.

    * **Tanılama paylaşma**:  
        Azure dosya paylaşımı, bulut uygulamalarının günlük, ölçüm ve kilitlenme bilgi dökümünü yazmaları için kullanışlı bir yerdir. Günlükler uygulama örnekleri tarafından Dosya REST API'si aracılığıyla yazılabilir ve geliştiriciler dosya paylaşımını yerel makinelerine bağlayarak bu verilere erişebilir. Bu durum geliştiricilerin bildikleri ve sevdikleri araçları bırakmak zorunda kalmadan bulut geliştirmeye geçmelerini sağladığından önemli bir esneklik sunar.

    * **Geliştirme/Test/Hata Ayıklama**:  
        Geliştiriciler veya yöneticiler bulutta sanal makinelerle çalışırken, sıklıkla bir dizi araca veya yardımcı programa ihtiyaçları olur. Bu tür yardımcı programları ve araçları tüm sanal makineleri kopyalamak uzun zaman alabilir. Bir Azure dosya paylaşımını sanal makinelere yerel olarak bağlayan geliştirici ve yöneticiler kopyalamaya gerek duymadan araçlarına ve yardımcı programlarına erişebilirler.
* **Kapsayıcılama**:  
    Azure dosya paylaşımları, durum bilgisi olmayan kapsayıcılar için kalıcı birimler olarak kullanılabilir. Kapsayıcılar, geliştiricilerin yeniliklerini hızlandırmasını sağlayan "bir kez derleyin, her yerde Çalıştır" özellikleri sunar. Her başlangıçta ham verilere erişen kapsayıcılar için, bu kapsayıcıların hangi örneği çalıştırdıklarından bağımsız olarak dosya sistemine erişmesine izin vermek üzere paylaşılan bir dosya sistemi gerekir.

## <a name="key-benefits"></a>Önemli avantajlar
* **Paylaşılan erişim**. Azure dosya paylaşımları, sektör standardı SMB ve NFS protokollerini destekler, yani şirket içi dosya paylaşımlarınızı uygulama uyumluluğuna endişelenmeden Azure dosya paylaşımlarıyla sorunsuz bir şekilde değiştirebilirsiniz. Dosya sistemini birden çok makine, uygulama/örnek arasında paylaşabilmek, paylaşılabilirlik gerektiren uygulamalarda Azure Dosyaları'nın önemli bir avantaj olmasını sağlar. 
* **Tam olarak yönetilir**. Azure dosya paylaşımları donanım veya işletim sistemi yönetmeye gerek kalmadan oluşturulabilir. Bu, sunucu işletim sistemine kritik güvenlik yükseltmeleri için yama eklemekle veya arızalı sabit diskleri değiştirmekle uğraşmanız gerekmediği anlamına gelir.
* **Betik oluşturma ve araç**. PowerShell cmdlet 'leri ve Azure CLı, Azure uygulamalarının yönetiminin bir parçası olarak Azure dosya paylaşımlarını oluşturmak, bağlamak ve yönetmek için kullanılabilir. Azure portal ve Azure Depolama Gezgini kullanarak Azure dosya paylaşımlarını oluşturabilir ve yönetebilirsiniz. 
* **Dayanıklılık**. Azure Dosyaları, baştan sonra her zaman kullanılabilir olacak şekilde hazırlanmıştır. Şirket içi dosya paylaşımlarını Azure Dosyaları ile değiştirmek, artık kalkıp bölgesel elektrik kesintileriyle veya ağ sorunlarıyla uğraşmak zorunda kalmayacağınız anlamına gelir. 
* **Tanıdık programlama**. Azure’da çalışan uygulamalar paylaşımdaki verilere [dosya sistemi G/Ç API’leri](/dotnet/api/system.io.file) yoluyla erişebilir. Böylece geliştiriciler mevcut uygulamalarını taşımak üzere kullandıkları kodlar ve yeteneklerden yararlanabilir. Sistem G/Ç API’lerine ek olarak, [Azure Depolama İstemcisi Kitaplıkları](/previous-versions/azure/dn261237(v=azure.100)) veya [Azure Depolama REST API’si](/rest/api/storageservices/file-service-rest-api) de kullanılabilir.

## <a name="next-steps"></a>Sonraki Adımlar
* [Kullanılabilir dosya paylaşma protokolleri hakkında bilgi edinin](storage-files-compare-protocols.md)
* [Azure dosya paylaşma oluşturma](storage-how-to-create-file-share.md)
* [Windows üzerinde bir SMB paylaşımında bağlantı kurmak ve bağlama](storage-how-to-use-files-windows.md)
* [Linux üzerinde bir SMB paylaşımında bağlantı oluşturma ve bağlama](storage-how-to-use-files-linux.md)
* [MacOS 'ta SMB paylaşımıyla bağlantı kurmak ve bağlama](storage-how-to-use-files-mac.md)
* [NFS paylaşma oluşturma](storage-files-how-to-create-nfs-shares.md)
