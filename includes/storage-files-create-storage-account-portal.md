---
title: storage-files-create-storage-account-portal
description: Azure Dosyaları için depolama hesabı oluşturun.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: a09e9206b3ecd23a6208572a88c16b149033f123
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/18/2019
ms.locfileid: "68699464"
---
Depolama hesabı, Azure dosya paylaşımını veya bloblar veya sorgular gibi diğer depolama kaynaklarını dağıtabileceğiniz, paylaşılan bir depolama havuzudur. Bir depolama hesabında sınırsız sayıda paylaşım olabilir. Bir paylaşım, depolama hesabının kapasite limitlerine kadar sınırsız sayıda dosyayı depolayabilir.

Bir depolama hesabı oluşturmak için:

1. Soldaki menüden **+** seçeneğini belirleyerek bir kaynak oluşturun.
2. Arama kutusuna **depolama hesabı** girin, **Depolama hesabı - blob, dosya, tablo, kuyruk** seçeneğini belirleyin ve **Oluştur**’u seçin.
    ![Kaynak araması iletişim kutusunda, depolama hesabı girişinin nasıl göründüğüne dair ekran görüntüsü](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. **Ad** alanına *mystorageacct* komutunu yazın ve benzersiz bir ad olduğunu belirten yeşil onay işareti görüntüleninceye kadar komutun sonuna birkaç rastgele sayı yazın. Depolama hesabı adı tamamen küçük harflerden oluşmalı ve genel olarak benzersiz olmalıdır. Depolama hesabınızın adını not edin. Daha sonra bu adı kullanacaksınız. 
4. **Dağıtım modeli** alanında varsayılan **Kaynak Yöneticisi** değerini değiştirmeden bırakın. Azure Resource Manager ve klasik dağıtım modeli arasındaki farklılıklar hakkında daha fazla bilgi edinmek için bkz. [Dağıtım modellerini ve kaynaklarınızın durumunu anlama](../articles/azure-resource-manager/resource-manager-deployment-model.md).
5. **Hesap türü** bölümünde **StorageV2** seçeneğini belirleyin. Farklı türlerde depolama hesapları hakkında daha fazla bilgi edinmek için bkz. [Azure depolama hesaplarını anlama](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
6. **Performans** bölümünde, varsayılan **Standart depolama** değerini değiştirmeden bırakın. Azure Dosyaları şu anda yalnızca standart depolama alanını destekler; Azure Premium Depolama’yı seçseniz de dosya paylaşımınız standart depolama alanında depolanır.
7. **Çoğaltma** bölümünde **Yerel olarak yedekli depolama (LRS)** seçeneğini belirleyin. 
8. **Güvenli aktarım gereklidir** bölümünde her zaman **Etkin** seçeneğini belirlemenizi öneririz. Bu seçenek hakkında daha fazla bilgi için bkz. [Aktarım sırasında şifrelemeyi anlama](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
9. **Abonelik** bölümünde, depolama hesabını oluşturmak için kullanılan aboneliği seçin. Yalnızca bir aboneliğiniz varsa bu, varsayılan değer olmalıdır.
10. **Kaynak grubu**’nda **Yeni oluştur**’u seçin. Ad için *myResourceGroup* girin.
11. **Konum** bölümünde **Doğu ABD**’yi seçin.
12. **Sanal ağlar** bölümünde varsayılan seçeneği **Devre dışı** olarak değiştirmeden bırakın. 
13. Depolama hesabının kolayca bulunması için **Panoya sabitle**’yi seçin.
14. İşleminiz bittiğinde dağıtımı başlatmak için **Oluştur**’a tıklayın.