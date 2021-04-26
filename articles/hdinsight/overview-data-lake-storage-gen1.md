---
title: HDInsight 'ta Azure Data Lake Storage 1. genel bakış
description: HDInsight 'ta Data Lake Storage 1. genel bakış.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 4ac1a48733d104e7674acbc13bfbb1e7a4cf02b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938874"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>HDInsight 'ta Azure Data Lake Storage 1. genel bakış

Azure Data Lake Storage 1., büyük veri analizi iş yükleri için kurumsal çapta bir hiper ölçek deposudur. Azure Data Lake kullanarak, herhangi bir boyut, tür ve Alım hızında veri yakalayabilirsiniz. İşlemsel ve araştırmacı analizler için tek bir yerde.

Web, uyumlu REST API 'Lerini kullanarak Hadoop 'tan (bir HDInsight kümesiyle kullanılabilir) Data Lake Storage 1. erişin. Data Lake Storage 1., depolanan verilerde analizler sağlamak için tasarlanmıştır ve veri analizi senaryolarında performans için ayarlanır. Gen1, gerçek dünya kurumsal kullanım örnekleri için gerekli olan özellikleri içerir. Bu yetenekler güvenlik, yönetilebilirlik, uyumluluk, güvenilirlik ve kullanılabilirliği içerir.

Azure Data Lake Storage 1. hakkında daha fazla bilgi için [Azure Data Lake Storage 1. ayrıntılı genel bakış](../data-lake-store/data-lake-store-overview.md)konusuna bakın.

Data Lake Storage 1. temel özellikleri şunlardır.

## <a name="compatibility-with-hadoop"></a>Hadoop ile uyumluluk

Data Lake Storage 1., bir Apache Hadoop dosya sistemidir ve bu, bir.  Webbir API kullanan HDInsight uygulamaları veya Hizmetleri Data Lake Storage 1. ile kolayca tümleştirilebilir. Data Lake Storage 1. Ayrıca, uygulamalar için bir Web, uyumlu REST arabirimi sunar.

Data Lake Storage 1. depolanan veriler, Hadoop analitik çerçeveleri kullanılarak kolayca çözümlenebilir. MapReduce veya Hive gibi çerçeveler. Azure HDInsight kümeleri, Data Lake Storage 1. depolanan verilere doğrudan erişmek için sağlanabilir ve yapılandırılabilir.

## <a name="unlimited-storage-petabyte-files"></a>Sınırsız depolama, petabayt boyutlu dosyalar

Data Lake Storage 1., sınırsız depolama sağlar ve analiz için farklı türlerde verileri depolamak için uygundur. Hesap boyutları veya dosya boyutları için sınır yoktur. Ya da bir veri Gölü içinde depolanabilecek veri miktarı. Tek tek dosyalar kilobayt ile petabaytlarca arasında, her türden veriyi depolamak için harika bir seçim Data Lake Storage 1.. Veriler birden çok kopya yapılarak durarak saklanır. Verilerin Veri Gölü üzerinde ne kadar süreyle depolanabileceğini de sınırlayabilirsiniz.

## <a name="performance-tuning-for-big-data-analytics"></a>Büyük veri analizi için performans ayarlama

Data Lake Storage 1., analitik sistemler için tasarlanmıştır. Büyük miktarlardaki verileri sorgulamak ve analiz etmek için büyük miktarlarda verimlilik gerektiren sistemler. Veri Gölü, bir dosyanın parçalarını birkaç ayrı depolama sunucusu üzerinden yayar. Verileri analiz edilirken, bu kurulum dosya paralel olarak okunduğunuz zaman okuma verimini geliştirir.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Kurumsal kullanıma hazır: yüksek kullanılabilirliğe sahip ve güvenli

Data Lake Storage 1. sektör standardı kullanılabilirliği ve güvenilirliği sağlar. Veri varlıkları, beklenmedik şekilde depolanır: yinelenen kopyalar, beklenmeyen hatalara karşı koruma. Kuruluşlar, kendi çözümlerinde Data Lake Storage 1., mevcut veri platformunun önemli bir parçası olarak kullanabilir.

Data Lake Storage 1. Ayrıca, depolanan veriler için kurumsal düzeyde güvenlik sağlar. Daha fazla bilgi için bkz. [Azure Data Lake Storage 1. verileri güvenli hale getirme](#data-security-in-data-lake-storage-gen1).

## <a name="flexible-data-structures"></a>Esnek veri yapıları

Data Lake Storage 1., önceki Dönüştürmelere gerek kalmadan, tüm verileri yerel biçiminde, olduğu gibi depolayabilirler. Data Lake Storage 1., veriler yüklenmeden önce bir şemanın tanımlanmasını gerektirmez. Bireysel analitik çerçeve, verileri Yorumlar ve analiz sırasında bir şemayı tanımlar. Data Lake Storage 1., yapılandırılmış verileri işleyebilir. Ve yapılandırılmamış veriler.

Veriler için Data Lake Storage 1. kapsayıcılar temelde klasörler ve dosyalardır. SDK, Azure portal ve Azure PowerShell kullanarak depolanan veriler üzerinde işlem yapabilirsiniz. Bu arabirimler ve kapsayıcılarla depoya yerleştirilen veriler, herhangi bir veri türünü saklayabilir. Data Lake Storage 1., verilerin türüne göre herhangi bir özel veri işleme yapmaz.

## <a name="data-security-in-data-lake-storage-gen1"></a>Data Lake Storage 1. veri güvenliği

Data Lake Storage 1., kimlik doğrulaması için Azure Active Directory kullanır ve verilerinize erişimi yönetmek için erişim denetim listelerini (ACL 'Ler) kullanır.

| **Özellik** | **Açıklama** |
| --- | --- |
| Kimlik Doğrulaması |Data Lake Storage 1., Data Lake Storage 1. depolanan tüm veriler için kimlik ve erişim yönetimi için Azure Active Directory (Azure AD) ile tümleşir. Tümleştirme nedeniyle tüm Azure AD özelliklerinden faydaların Data Lake Storage 1.. Bu özellikler şunları içerir: çok faktörlü kimlik doğrulaması, koşullu erişim ve Azure rol tabanlı erişim denetimi. Ayrıca, uygulama kullanımı izleme, güvenlik izleme ve uyarı gibi. Data Lake Storage 1., REST arabirimi içinde kimlik doğrulaması için OAuth 2,0 protokolünü destekler. [Azure Active Directory kullanarak Azure Data Lake Storage 1. Içindeki kimlik doğrulamaya](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md) bakın|
| Erişim denetimi |Data Lake Storage 1., Webdapterprotocol tarafından sunulan POSIX stili izinleri destekleyerek erişim denetimi sağlar. ACL’ler kök klasörde, alt klasörlerde ve dosyalarda tek tek etkinleştirilebilir. ACL 'Lerin Data Lake Storage 1. bağlamında nasıl çalıştığı hakkında daha fazla bilgi için, bkz. [Data Lake Storage 1. Access Control](../data-lake-store/data-lake-store-access-control.md). |
| Şifreleme |Data Lake Storage 1., hesapta depolanan veriler için de şifreleme sağlar. Data Lake Storage 1. bir hesap oluştururken şifreleme ayarlarını belirtirsiniz. Verilerinizin şifrelenmesini veya şifrelemeyi kabul etmesinin tercih edilebilir. Daha fazla bilgi için bkz. [Data Lake Storage 1. şifreleme](../data-lake-store/data-lake-store-encryption.md). Şifrelemeyle ilgili yapılandırma sağlama yönergeleri için bkz. [Azure Portal kullanarak Azure Data Lake Storage 1. kullanmaya başlama](../data-lake-store/data-lake-store-get-started-portal.md). |

Data Lake Storage 1. verilerin güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure Data Lake Storage 1. depolanan verileri güvenli hale getirme](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Data Lake Storage 1. ile uyumlu uygulamalar

Data Lake Storage 1., Hadoop ortamında bulunan açık kaynaklı bileşenlerle uyumludur. Ayrıca diğer Azure hizmetleriyle sorunsuz şekilde tümleştirilir.  Data Lake Storage 1. hem açık kaynaklı bileşenler hem de diğer Azure hizmetleriyle nasıl kullanılabileceğinizi öğrenmek için aşağıdaki bağlantıları izleyin.

* [Azure Data Lake Storage 1. ile birlikte çalışan açık kaynak büyük veri uygulamaları](../data-lake-store/data-lake-store-compatible-oss-other-applications.md)bölümüne bakın.
* Daha geniş bir senaryoyu etkinleştirmek için diğer Azure hizmetleriyle Data Lake Storage 1. nasıl kullanacağınızı anlamak için bkz. [Azure Data Lake Storage 1. diğer Azure hizmetleriyle tümleştirme](../data-lake-store/data-lake-store-integrate-with-other-services.md) .
* [Büyük veri gereksinimleri için bkz. Azure Data Lake Storage 1. kullanma](../data-lake-store/data-lake-store-data-scenarios.md).

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage 1. dosya sistemi (adl://)

Hadoop ortamlarında, yeni dosya sistemi, AzureDataLakeFilesystem (adl://) aracılığıyla Data Lake Storage 1. erişebilirsiniz. Kullanan uygulamaların ve hizmetlerin performansı, `adl://` Şu anda webbir 'da kullanılamayan yollarla iyileştirilebilir. Sonuç olarak, önerilen adl://kullanarak en iyi performansı elde etmek için esneklik elde edersiniz. Ya da Webbir API 'yi doğrudan kullanmaya devam ederek mevcut kodu saklayın. Azure HDInsight, Data Lake Storage 1. en iyi performansı sağlamak için AzureDataLakeFilesystem 'in tam avantajlarından yararlanır.

Aşağıdaki URI 'yi kullanarak Data Lake Storage 1. verilerinize erişin:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Data Lake Storage 1. verilere erişme hakkında daha fazla bilgi için bkz. [depolanan verilerde kullanılabilir eylemler](../data-lake-store/data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Lake Storage 2. Nesil'e giriş](../storage/blobs/data-lake-storage-introduction.md)
* [Azure Depolama’ya giriş](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage 2. Nesil’e genel bakış](./overview-data-lake-storage-gen2.md)