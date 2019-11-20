---
title: Bekleyen veriler için Azure depolama şifrelemesi | Microsoft Docs
description: Azure depolama, verilerinizi buluta kalıcı yapmadan önce otomatik olarak şifreleyerek korur. Depolama hesabınızın şifrelenmesi için Microsoft tarafından yönetilen anahtarları kullanabilir veya kendi anahtarınızla şifrelemeyi yönetebilirsiniz.
services: storage
author: tamram
ms.service: storage
ms.date: 10/02/2019
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: cfac7fdbbdbf06ae74385fbc33e61d11cb99ff87
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74066309"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Bekleyen veriler için Azure depolama şifrelemesi

Azure depolama, verilerinizi buluta kalıcı hale geldiğinde otomatik olarak şifreler. Şifreleme, verilerinizi korur ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olur. Azure depolama 'daki veriler, 256 bit [AES şifrelemesi](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)kullanılarak şifrelenmiş ve şifresi çözülür, en güçlü blok şifrelemeleri KULLANILABILIR ve FIPS 140-2 uyumludur. Azure depolama şifrelemesi, Windows 'da BitLocker şifrelemeye benzer.

Azure depolama şifrelemesi tüm yeni depolama hesapları için etkin ve devre dışı bırakılamaz. Verileriniz varsayılan olarak güvenli hale getirildiğinden, Azure depolama şifrelemesi 'nin avantajlarından yararlanmak için kodunuzu veya uygulamalarınızı değiştirmeniz gerekmez.

Depolama hesapları, performans katmanlarından (Standart veya Premium) veya dağıtım modelinden (Azure Resource Manager veya klasik) bağımsız olarak şifrelenir. Tüm Azure depolama artıklığı seçenekleri şifrelemeyi destekler ve bir depolama hesabının tüm kopyaları şifrelenir. Blob 'lar, diskler, dosyalar, kuyruklar ve tablolar dahil olmak üzere tüm Azure depolama kaynakları şifrelenir. Tüm nesne meta verileri de şifrelenir.

Şifreleme, Azure depolama performansını etkilemez. Azure depolama şifrelemesi için ek bir maliyet yoktur.

Azure depolama şifrelemesini temel alan şifreleme modülleri hakkında daha fazla bilgi için bkz. [şifreleme API 'si: yeni nesil](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Şifreleme anahtarı yönetimi hakkında

Depolama hesabınızın şifrelenmesi için Microsoft tarafından yönetilen anahtarları kullanabilir veya kendi anahtarınızla şifrelemeyi yönetebilirsiniz. Şifrelemeyi kendi anahtarlarınız ile yönetmeyi seçerseniz iki seçeneğiniz vardır:

- Depolama hesabındaki tüm verileri şifrelemek ve şifrelerini çözmek için kullanılacak *müşteri tarafından yönetilen bir anahtar* belirtebilirsiniz. Bir müşteri tarafından yönetilen anahtar, Depolama hesabınızdaki tüm hizmetlerde tüm verileri şifrelemek için kullanılır.
- BLOB depolama işlemlerinde, *müşteri tarafından sağlanmış bir anahtar* belirtebilirsiniz. Blob depolamaya karşı okuma veya yazma isteği yapan bir istemci, blob verilerinin şifrelenme ve şifresinin çözülmesi üzerinde ayrıntılı denetim isteğine yönelik bir şifreleme anahtarı içerebilir.

Aşağıdaki tabloda, Azure depolama şifrelemesi için anahtar yönetim seçenekleri karşılaştırılmaktadır.

|                                        |    Microsoft tarafından yönetilen anahtarlar                             |    Müşteri tarafından yönetilen anahtarlar                                                                                                                        |    Müşteri tarafından sunulan anahtarlar                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Şifreleme/şifre çözme işlemleri    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Desteklenen Azure depolama hizmetleri    |    Tümü                                                |    BLOB depolama, Azure dosyaları                                                                                                               |    Blob depolama                                                                  |
|    Anahtar depolama                         |    Microsoft anahtar deposu    |    Azure Key Vault                                                                                                                              |    Azure Key Vault veya başka bir anahtar deposu                                                                 |
|    Anahtar döndürme sorumluluğu         |    Microsoft                                          |    Müşteri                                                                                                                                     |    Müşteri                                                                      |
|    Anahtar kullanımı                           |    Microsoft                                          |    Azure portal, depolama kaynak sağlayıcısı REST API, Azure depolama yönetim kitaplıkları, PowerShell, CLı        |    Azure depolama REST API (BLOB depolama), Azure depolama istemci kitaplıkları    |
|    Anahtar erişimi                          |    Yalnızca Microsoft                                     |    Microsoft, müşteri                                                                                                                    |    Yalnızca müşteri                                                                 |

Aşağıdaki bölümlerde, anahtar yönetimiyle ilgili seçeneklerin her biri daha ayrıntılı olarak açıklanır.

## <a name="microsoft-managed-keys"></a>Microsoft tarafından yönetilen anahtarlar

Varsayılan olarak, depolama hesabınız Microsoft tarafından yönetilen şifreleme anahtarlarını kullanır. Aşağıdaki görüntüde gösterildiği gibi, [Azure Portal](https://portal.azure.com) **şifreleme** bölümünde depolama hesabınızın şifreleme ayarlarını görebilirsiniz.

![Microsoft tarafından yönetilen anahtarlarla şifrelenen hesabı görüntüle](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarlar

Azure depolama şifrelemesini, kendi Anahtarlarınıza sahip depolama hesabı düzeyinde yönetmeyi seçebilirsiniz. Depolama hesabı düzeyinde müşteri tarafından yönetilen bir anahtar belirttiğinizde, bu anahtar BLOB, kuyruk, dosya ve tablo verileri dahil olmak üzere depolama hesabındaki tüm verileri şifrelemek ve şifrelerini çözmek için kullanılır.  Müşteri tarafından yönetilen anahtarlar, erişim denetimlerini oluşturma, döndürme, devre dışı bırakma ve iptal etme için daha fazla esneklik sunar. Verilerinizi korumak için kullanılan şifreleme anahtarlarını da denetleyebilirsiniz.

Müşteri tarafından yönetilen anahtarlarınızın depolanması için Azure Key Vault kullanmanız gerekir. Kendi anahtarlarınızı oluşturabilir ve bunları bir anahtar kasasında saklayabilir veya Azure Key Vault API 'Lerini kullanarak anahtarlar oluşturabilirsiniz. Depolama hesabı ve Anahtar Kasası aynı bölgede olmalıdır, ancak farklı aboneliklerde olabilir. Azure Key Vault hakkında daha fazla bilgi için bkz. [Azure Key Vault nedir?](../../key-vault/key-vault-overview.md).

Bu diyagramda, Azure depolama 'nın, müşteri tarafından yönetilen anahtarı kullanarak istek yapmak için Azure Active Directory ve Azure Key Vault nasıl kullandığı gösterilmektedir:

![Müşteri tarafından yönetilen anahtarların Azure Storage 'da nasıl çalıştığını gösteren diyagram](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

Aşağıdaki listede, diyagramdaki numaralandırılmış adımlar açıklanmaktadır:

1. Azure Key Vault Yöneticisi, depolama hesabıyla ilişkili yönetilen kimliğe şifreleme anahtarları için izinler verir.
2. Azure Depolama Yöneticisi, depolama hesabı için müşteri tarafından yönetilen bir anahtarla şifrelemeyi yapılandırır.
3. Azure depolama, Azure Active Directory üzerinden Azure Key Vault erişimin kimliğini doğrulamak için depolama hesabıyla ilişkili yönetilen kimliği kullanır.
4. Azure depolama, hesap şifreleme anahtarını Azure Key Vault müşteri anahtarıyla sarmalanmış.
5. Okuma/yazma işlemleri için, Azure depolama, şifreleme ve şifre çözme işlemleri gerçekleştirmek üzere hesap şifreleme anahtarını sarmalamak ve sarmalamak için Azure Key Vault istek gönderir.

Depolama hesabındaki müşterinin yönettiği anahtarlara erişimi iptal etmek için bkz. PowerShell ve [Azure Key Vault clı](https://docs.microsoft.com/cli/azure/keyvault) [Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) . Erişimi iptal etmek, şifreleme anahtarına Azure depolama tarafından erişilemediğinden, depolama hesabındaki tüm verilere erişimi etkin bir şekilde engeller.

Müşteri tarafından yönetilen anahtarlar da genel önizleme olarak Azure yönetilen diskler için de kullanılabilir, müşteri tarafından yönetilen anahtarlar yönetilen diskler için depolama alanının geri kalanından biraz farklı çalışır. Ayrıntılar için [konudaki makalemize](../../virtual-machines/linux/disk-encryption.md#customer-managed-keys-public-preview)bakın.

Azure depolama ile müşteri tarafından yönetilen anahtarları kullanmayı öğrenmek için şu makalelerden birine bakın:

- [Azure portalından Azure Depolama şifrelemesi için müşteri tarafından yönetilen anahtarları yapılandırma](storage-encryption-keys-portal.md)
- [PowerShell'den Azure Depolama şifrelemesi için müşteri tarafından yönetilen anahtarları yapılandırma](storage-encryption-keys-powershell.md)
- [Azure CLı ile Azure depolama şifrelemesi ile müşteri tarafından yönetilen anahtarları kullanma](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Müşteri tarafından yönetilen anahtarlar, Azure Active Directory (Azure AD) bir özelliği olan Azure kaynakları için yönetilen kimliklere bağımlıdır. Azure portal müşteri tarafından yönetilen anahtarları yapılandırırken, yönetilen bir kimlik, kapakları altında depolama hesabınıza otomatik olarak atanır. Daha sonra aboneliği, kaynak grubunu veya depolama hesabını bir Azure AD dizininden diğerine taşırsanız, depolama hesabıyla ilişkili yönetilen kimlik yeni kiracıya aktarılmaz, bu nedenle müşterinin yönettiği anahtarlar artık çalışmayabilir. Daha fazla bilgi için bkz. SSS 'de **Azure AD dizinleri arasında bir abonelik aktarma** [ve Azure kaynakları için yönetilen kimliklerle ilgili bilinen sorunlar](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="customer-provided-keys-preview"></a>Müşteri tarafından sunulan anahtarlar (Önizleme)

Azure Blob depolamada istek yapan istemcilerin, tek bir istekte şifreleme anahtarı sağlama seçeneği vardır. İstek üzerine şifreleme anahtarı dahil olmak üzere, BLOB depolama işlemleri için şifreleme ayarları üzerinde ayrıntılı denetim sağlar. Müşteri tarafından sunulan anahtarlar (Önizleme), Azure Key Vault veya başka bir anahtar deposunda depolanabilir.

### <a name="encrypting-read-and-write-operations"></a>Okuma ve yazma işlemlerini şifreleme

İstemci uygulaması istekte bir şifreleme anahtarı sağlıyorsa, blob verilerini okurken ve yazarken Azure Storage şifreleme ve şifre çözme işlemlerini saydam bir şekilde gerçekleştirir. Şifreleme anahtarının SHA-256 karması bir blob 'un içeriğiyle birlikte yazılır ve BLOB 'un sonraki tüm işlemlerinin aynı şifreleme anahtarını kullanmasını doğrulamak için kullanılır. Azure depolama, istemcinin istekle birlikte gönderdiği şifreleme anahtarını depolamaz veya yönetemez. Şifreleme veya şifre çözme işlemi tamamlandıktan hemen sonra anahtar güvenle atılır.

Bir istemci, müşteri tarafından sağlanmış bir anahtar kullanarak bir blobu oluşturduğunda veya güncelleştir, sonra o blob için sonraki okuma ve yazma isteklerinin de anahtarı sağlaması gerekir. Anahtar, zaten müşteri tarafından sağlanmış bir anahtarla şifrelenen bir blob isteği üzerinde sağlanmazsa, istek 409 (çakışma) hata koduyla başarısız olur.

İstemci uygulaması istekte bir şifreleme anahtarı gönderirse ve depolama hesabı Microsoft tarafından yönetilen bir anahtar veya müşteri tarafından yönetilen anahtar kullanılarak da şifrelenirse Azure Storage, şifreleme ve şifre çözme isteğinde belirtilen anahtarı kullanır.

Şifreleme anahtarını isteğin bir parçası olarak göndermek için, bir istemcinin HTTPS kullanarak Azure depolama ile güvenli bir bağlantı kurması gerekir.

Her blob anlık görüntüsünün kendi şifreleme anahtarı olabilir.

### <a name="request-headers-for-specifying-customer-provided-keys"></a>Müşteri tarafından sunulan anahtarları belirtmek için istek üstbilgileri

İstemciler, REST çağrıları için aşağıdaki üst bilgileri kullanarak blob depolamaya yönelik bir istek üzerindeki şifreleme anahtarı bilgilerini güvenli bir şekilde geçirebilir:

|İstek üst bilgisi | Açıklama |
|---------------|-------------|
|`x-ms-encryption-key` |Hem yazma hem de okuma istekleri için gereklidir. Base64 kodlamalı AES-256 şifreleme anahtarı değeri. |
|`x-ms-encryption-key-sha256`| Hem yazma hem de okuma istekleri için gereklidir. Şifreleme anahtarının Base64 ile kodlanmış SHA256. |
|`x-ms-encryption-algorithm` | Yazma istekleri için gereklidir, okuma istekleri için isteğe bağlıdır. Verilen anahtar kullanılarak verileri şifrelerken kullanılacak algoritmayı belirtir. AES256 olmalıdır. |

İstekte şifreleme anahtarlarının belirtilmesi isteğe bağlıdır. Ancak, bir yazma işlemi için yukarıda listelenen üst bilgilerden birini belirtirseniz, bunların tümünü belirtmeniz gerekir.

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Müşteri tarafından sunulan anahtarları destekleyen BLOB depolama işlemleri

Aşağıdaki BLOB depolama işlemleri, bir istekte müşterinin sunduğu şifreleme anahtarlarını göndermeyi destekler:

- [Blobu koy](/rest/api/storageservices/put-blob)
- [Öbek listesini yerleştirme](/rest/api/storageservices/put-block-list)
- [Yerleştirme bloğu](/rest/api/storageservices/put-block)
- [Bloğu URL 'den koy](/rest/api/storageservices/put-block-from-url)
- [Yerleştirme sayfası](/rest/api/storageservices/put-page)
- [URL 'den sayfa koy](/rest/api/storageservices/put-page-from-url)
- [Ekleme bloğu](/rest/api/storageservices/append-block)
- [Blob özelliklerini ayarla](/rest/api/storageservices/set-blob-properties)
- [Blob meta verilerini ayarla](/rest/api/storageservices/set-blob-metadata)
- [Blob al](/rest/api/storageservices/get-blob)
- [Blob özelliklerini al](/rest/api/storageservices/get-blob-properties)
- [Blob meta verilerini al](/rest/api/storageservices/get-blob-metadata)
- [Anlık görüntü blobu](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>Müşteri tarafından sunulan anahtarları döndür

İstek üzerine geçirilen bir şifreleme anahtarını döndürmek için, blobu indirin ve yeni şifreleme anahtarıyla yeniden yükleyin.

> [!IMPORTANT]
> Azure portal, istekte sağlanmış bir anahtarla şifrelenen bir kapsayıcı veya Blobun okumak veya buradan yazmak için kullanılamaz.
>
> Azure Key Vault gibi güvenli bir anahtar deposunda blob depolamaya bir istekte sağladığınız şifreleme anahtarını koruduğunuzdan emin olun. Şifreleme anahtarı olmadan bir kapsayıcıda veya Blobun üzerinde yazma işlemi denerseniz, işlem başarısız olur ve nesneye erişiminizi kaybedersiniz.

### <a name="example-use-a-customer-provided-key-to-upload-a-blob-in-net"></a>Örnek: .NET ' te blob yüklemek için müşteri tarafından sağlanmış bir anahtar kullanın

Aşağıdaki örnek, bir müşteri tarafından sağlanmış anahtar oluşturur ve bir blobu karşıya yüklemek için bu anahtarı kullanır. Kod bir blok yükler ve ardından blok listesini kaydeder ve BLOB 'u Azure depolama 'ya yazar. Anahtar, [CustomerProvidedKey](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.customerprovidedkey) özelliği ayarlanarak [blobrequestoptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) nesnesinde sağlanır.

Anahtar [AesCryptoServiceProvider](/dotnet/api/system.security.cryptography.aescryptoserviceprovider) sınıfıyla oluşturulur. Kodunuzda bu sınıfın bir örneğini oluşturmak için, `System.Security.Cryptography` ad alanına başvuran bir `using` ifade ekleyin:

```csharp
public static void UploadBlobWithClientKey(CloudBlobContainer container)
{
    // Create a new key using the Advanced Encryption Standard (AES) algorithm.
    AesCryptoServiceProvider keyAes = new AesCryptoServiceProvider();

    // Specify the key as an option on the request.
    BlobCustomerProvidedKey customerProvidedKey = new BlobCustomerProvidedKey(keyAes.Key);
    var options = new BlobRequestOptions
    {
        CustomerProvidedKey = customerProvidedKey
    };

    string blobName = "sample-blob-" + Guid.NewGuid();
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);

    try
    {
        // Create an array of random bytes.
        byte[] buffer = new byte[1024];
        Random rnd = new Random();
        rnd.NextBytes(buffer);

        using (MemoryStream sourceStream = new MemoryStream(buffer))
        {
            // Write the array of random bytes to a block.
            int blockNumber = 1;
            string blockId = Convert.ToBase64String(Encoding.ASCII.GetBytes(string.Format("BlockId{0}",
                blockNumber.ToString("0000000"))));

            // Write the block to Azure Storage.
            blockBlob.PutBlock(blockId, sourceStream, null, null, options, null);

            // Commit the block list to write the blob.
            blockBlob.PutBlockList(new List<string>() { blockId }, null, options, null);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure depolama şifrelemesi ile disk şifrelemesi karşılaştırması

Azure depolama şifrelemesi, Azure sanal makine disklerini geri yükleyen sayfa bloblarını şifreler. Ayrıca, yerel geçici diskler dahil olmak üzere tüm Azure sanal makine diskleri, isteğe bağlı olarak [Azure disk şifrelemesi](../../security/azure-security-disk-encryption-overview.md)ile şifreli olabilir. Azure disk şifrelemesi, Azure Key Vault ile tümleştirilmiş işletim sistemi tabanlı şifreleme çözümleri sağlamak için Windows üzerinde sektör standardı [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) ve Linux üzerinde [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) kullanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Key Vault nedir?](../../key-vault/key-vault-overview.md)
- [Azure portalından Azure Depolama şifrelemesi için müşteri tarafından yönetilen anahtarları yapılandırma](storage-encryption-keys-portal.md)
- [PowerShell'den Azure Depolama şifrelemesi için müşteri tarafından yönetilen anahtarları yapılandırma](storage-encryption-keys-powershell.md)
- [Azure CLI'dan Azure Depolama şifrelemesi için müşteri tarafından yönetilen anahtarları yapılandırma](storage-encryption-keys-cli.md)
