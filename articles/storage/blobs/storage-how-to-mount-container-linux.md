---
title: Linux 'ta bir dosya sistemi olarak Azure Blob depolamayı bağlama | Microsoft Docs
description: Linux üzerinde sanal bir dosya sistemi sürücüsü olan blobsigortası ile bir Azure Blob depolama kapsayıcısını nasıl bağlayacağınızı öğrenin.
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.date: 2/1/2019
ms.author: tamram
ms.reviewer: twooley
ms.openlocfilehash: 002e8650a5555b70caf09179e03ce1bad1acdef5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737549"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Blob depolamayı blobsigortası ile dosya sistemi olarak bağlama

## <a name="overview"></a>Genel Bakış
[Blobsigortası](https://github.com/Azure/azure-storage-fuse) , Azure Blob depolama için bir sanal dosya sistemi sürücüsüdür. Blobsigortası, Linux dosya sistemi aracılığıyla Depolama hesabınızdaki mevcut Blok Blobu verilerinize erişmenizi sağlar. Blobsigortası, bir sınırlayıcı olarak eğik çizgiyle '/' olan sanal dizin düzenini kullanır.  

Bu kılavuzda, blobsigortası kullanma ve Linux üzerinde bir BLOB depolama kapsayıcısı bağlama ve verilere erişme işlemlerinin nasıl yapılacağı gösterilir. Blobsigortası hakkında daha fazla bilgi edinmek için, [blobsigortası deposundaki](https://github.com/Azure/azure-storage-fuse)ayrıntıları okuyun.

> [!WARNING]
> Blobsigortası yalnızca istekleri [BLOB REST API 'lerine](/rest/api/storageservices/blob-service-rest-api)çevirdiğinden %100 POSIX uyumluluğunu garanti etmez. Örneğin, yeniden adlandırma işlemleri POSIX içinde atomik, ancak blobsigortası içinde değildir.
> Yerel dosya sistemi ve blobsigortası arasındaki farkların tam listesi için [blobsigortası kaynak kodu deposunu](https://github.com/azure/azure-storage-fuse)ziyaret edin.
> 

## <a name="install-blobfuse-on-linux"></a>Linux 'ta blobsigortası 'yi yükler
Blobsigortası ikilileri, Ubuntu, de, SUSE, CentoOS, Oracle Linux ve RHEL dağıtımları için [Linux Için Microsoft yazılım depoları](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) 'nda bulunur. Bu dağıtımlara blobsigortası yüklemek için, listeden depolardan birini yapılandırın. Ayrıca, dağıtım için bir ikili dosya yoksa, [Azure depolama yükleme adımlarını](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) izleyerek kaynak koddan ikili dosyaları da oluşturabilirsiniz.

Blobsigortası, Ubuntu sürümlerinde yüklemeyi destekler: 16,04, 18,04 ve 20,04, RHELversions: 7,5, 7,8, 8,0, 8,1, 8,2, CentOS sürümleri: 7,0, 8,0, debir sürümler: 9,0, 10,0, SUSE Version: 15, OracleLinux 8,1. Dağıtılan sürümlerden birine sahip olduğunuzdan emin olmak için bu komutu çalıştırın:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Microsoft paket deposunu yapılandırma
[Linux paket deposunu Microsoft ürünleri için](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)yapılandırın.

Örnek olarak, bir Enterprise Linux 8 dağıtımında:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/8/packages-microsoft-prod.rpm
```

Benzer şekilde, URL 'yi `.../rhel/7/...` bir Enterprise Linux 7 dağıtımına işaret olacak şekilde değiştirin.

Ubuntu 20,04 dağıtımında başka bir örnek:
```bash
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Benzer şekilde, URL 'yi `.../ubuntu/16.04/...` `.../ubuntu/18.04/...` başka bir Ubuntu sürümüne başvuracak şekilde değiştirin.

### <a name="install-blobfuse"></a>Blobsigortası 'yi yükler

Ubuntu/debir dağıtım üzerinde:
```bash
sudo apt-get install blobfuse
```

Enterprise Linux dağıtımında:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Bağlama için hazırlanma
Blobsigortası, dosya sistemindeki geçici bir yolun arabelleğe girmesini ve açık dosyaları önbelleğe almak için yerel benzeri performans sağlar. Bu geçici yol için en iyi performansa sahip diski seçin veya en iyi performansı elde etmek için bir Ramdisk kullanın. 

> [!NOTE]
> Blobsigortası tüm açık dosya içeriğini geçici yolda depolar. Tüm açık dosyaları barındırmak için yeterli alana sahip olduğunuzdan emin olun. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>Seçim Geçici yol için bir Ramdisk kullanın
Aşağıdaki örnek, bir Ramdisk 16 GB ve blobsigortası için bir dizin oluşturur. Gereksinimlerinize göre boyutu seçin. Bu Ramdisk, blobsigortası 'nin boyut olarak 16 GB 'a kadar dosya açmasına olanak sağlar. 
```bash
sudo mkdir /mnt/ramdisk
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Geçici yol olarak SSD kullanma
Azure 'da, blobsigortası için düşük gecikmeli bir arabellek sağlamak üzere sanal makinelerinizdeki kullanılabilir kısa ömürlü diskleri (SSD) kullanabilirsiniz. Ubuntu dağıtımları içinde bu kısa ömürlü disk '/mnt ' üzerine bağlanır. Red Hat ve CentOS dağıtımları ' nda disk '/mnt/Resource/' üzerine bağlanır.

Kullanıcının geçici yola erişimi olduğundan emin olun:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Depolama hesabı kimlik bilgilerinizi yapılandırma
Blobsigortası, kimlik bilgilerinizin bir metin dosyasında aşağıdaki biçimde depolanmasını gerektirir: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
, `accountName` Tam URL değil, depolama hesabınızın ön ekidir.

Bu dosyayı kullanarak oluşturun:

```
touch ~/fuse_connection.cfg
```

Bu dosyayı oluşturup düzenledikten sonra diğer kullanıcıların okuyamaması için erişimi kısıtladığınızdan emin olun.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Yapılandırma dosyasını Windows üzerinde oluşturduysanız, `dos2unix` dosyayı temizleme ve UNIX biçimine dönüştürme için çalıştırdığınızdan emin olun. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Bağlama için boş bir dizin oluşturun
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Bağlama

> [!NOTE]
> Bağlama seçeneklerinin tam listesi için [blobsigortası deposuna](https://github.com/Azure/azure-storage-fuse#mount-options)bakın.  
> 

Blobsigortası bağlamak için, Kullanıcı ile aşağıdaki komutu çalıştırın. Bu komut '/Path/to/fuse_connection. cfg ' içinde belirtilen kapsayıcıyı '/myContainer ' konumuna bağlar.

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Artık, normal dosya sistemi API 'Leri aracılığıyla blok bloblarınıza erişiminizin olması gerekir. Dizini oluşturan kullanıcı, varsayılan olarak erişime güvenlik altına alan tek kişidir. Tüm kullanıcılara erişim izni vermek için, seçeneğini kullanarak bağlayabilirsiniz ```-o allow_other``` . 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Sonraki adımlar

* [Blobsigortası giriş sayfası](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Blobsigortası sorunlarını raporla](https://github.com/Azure/azure-storage-fuse/issues)
