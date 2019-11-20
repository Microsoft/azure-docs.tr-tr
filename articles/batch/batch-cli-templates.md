---
title: Şablonları kullanarak işleri uçtan uca Çalıştır-Azure Batch | Microsoft Docs
description: Şablon dosyaları ve Azure CLı ile Batch havuzları, işler ve Görevler oluşturun.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 4733cf1a83bec472baae42f2ac29636bff5fc324
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095307"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Azure Batch CLı şablonlarını ve dosya aktarımını kullanın

Azure CLı için Azure Batch uzantısı kullanarak, Batch işlerini kod yazmadan çalıştırmak mümkündür.

Batch havuzları, işler ve görevler oluşturmak için Azure CLı ile JSON şablon dosyaları oluşturun ve kullanın. İş giriş dosyalarını Batch hesabıyla ilişkili depolama hesabına kolayca yüklemek ve iş çıktı dosyalarını indirmek için CLı uzantı komutlarını kullanın.

## <a name="overview"></a>Genel Bakış

Azure CLı uzantısı, toplu Işin, geliştiriciler olmayan kullanıcılar tarafından uçtan uca kullanılmasını sağlar. Yalnızca CLı komutlarıyla, bir havuz oluşturabilir, giriş verilerini karşıya yükleyebilir, işler ve ilişkili görevler oluşturabilir ve elde edilen çıkış verilerini indirebilirsiniz. Ek kod gerekmez. CLı komutlarını doğrudan çalıştırın veya betiklerin ile tümleştirin.

Toplu şablonlar, havuz, işler, görevler ve diğer öğeleri oluştururken özellik değerlerini belirtmek için JSON dosyaları için [Azure CLI 'deki mevcut Batch desteğini](batch-cli-get-started.md#json-files-for-resource-creation) oluşturur. Batch şablonları aşağıdaki özellikleri ekleyin:

-   Parametreler tanımlanabilir. Şablon kullanıldığında, şablon gövdesinde belirtilen diğer öğe özelliği değerleriyle birlikte öğe oluşturmak için yalnızca parametre değerleri belirtilir. Batch ve Batch tarafından çalıştırılacak uygulamaları anlayan bir Kullanıcı, havuz, iş ve görev özelliği değerlerini belirterek şablonlar oluşturabilir. Batch ve/veya uygulamalar hakkında daha az bilgi sahibi olan bir kullanıcının yalnızca tanımlı parametrelerin değerlerini belirtmesi gerekir.

-   İş görevi fabrikaları, bir işle ilişkili bir veya daha fazla görev oluşturur ve çok sayıda görev tanımının oluşturulmasını ve iş gönderimini önemli ölçüde basitleştirir.


İşler genellikle girdi veri dosyalarını kullanır ve çıkış veri dosyaları üretir. Bir depolama hesabı, varsayılan olarak, her Batch hesabıyla ilişkilendirilir. CLı kullanarak bu depolama hesabına ve hiçbir kodlama ve depolama kimlik bilgisi olmadan dosya aktarın.

Örneğin, [FFmpeg](https://ffmpeg.org/) ses ve video dosyalarını işleyen popüler bir uygulamadır. Kaynak video dosyalarını farklı çözünürlüklere geçirmek üzere FFmpeg 'yi çağırmak için Azure Batch CLı ile ilgili adımlar aşağıda verilmiştir.

-   Havuz şablonu oluşturun. Şablonu oluşturan kullanıcı, FFmpeg uygulamasının ve gereksinimlerinin nasıl çağrılacağını bilir; uygun işletim sistemini, VM boyutunu, FFmpeg 'nin nasıl yüklendiğini (bir uygulama paketinden veya bir paket Yöneticisi kullanarak) ve diğer havuz özelliği değerlerini belirtirler. Parametreler, şablon kullanıldığında oluşturulur, yalnızca havuz KIMLIĞI ve VM sayısı belirtilmelidir.

-   Bir iş şablonu oluşturun. Şablonu oluşturan kullanıcı, bu kodun kaynak videosunu farklı bir çözüme dönüştürebilmesi ve görev komut satırını belirttiğinde, FFmpeg 'nin nasıl çağrılması gerektiğini bilir. Ayrıca, kaynak video dosyalarını içeren bir klasör olduğunu ve girdi dosyası başına gerekli bir görevi olduğunu da biliyoruz.

-   Video dosyaları kümesine sahip Son Kullanıcı ilk olarak havuz şablonunu kullanarak bir havuz oluşturur; yalnızca havuz KIMLIĞINI ve gereken VM sayısını belirtin. Daha sonra kaynak dosyalarını transkodla karşıya yükleyebilir. Daha sonra iş şablonu kullanılarak gönderilebilir ve yalnızca havuz KIMLIĞI ve yüklenen kaynak dosyalarının konumu belirtilebilir. Toplu iş, her giriş dosyası için bir görev oluşturulur. Son olarak, dönüştürülmüş kodlanmış çıkış dosyaları indirilebilir.

## <a name="installation"></a>Yükleme

Azure Batch CLı uzantısını yüklemek için, önce [Azure clı 2,0](/cli/azure/install-azure-cli)' i veya Azure clı 'yi [Azure Cloud Shell](../cloud-shell/overview.md)' de çalıştırın.

Aşağıdaki Azure CLı komutunu kullanarak toplu Iş uzantısının en son sürümünü yükler:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Batch CLı uzantısı ve ek yükleme seçenekleri hakkında daha fazla bilgi için bkz. [GitHub deposu](https://github.com/Azure/azure-batch-cli-extensions).


CLı uzantısı özelliklerini kullanmak için, bir Azure Batch hesabınız ve depolama alanına ve bu kaynaktan, bağlı bir depolama hesabından dosya aktarma komutları için gerekir.

Azure CLı ile Batch hesabında oturum açmak için bkz. [Azure CLI Ile Batch kaynaklarını yönetme](batch-cli-get-started.md).

## <a name="templates"></a>Şablonlar

Azure Batch şablonlar, işlev ve sözdizimi ' nde Azure Resource Manager şablonlarına benzerdir. Bunlar, öğe özelliği adları ve değerleri içeren JSON dosyalarıdır, ancak aşağıdaki ana kavramları ekler:

-   **Parametreler**

    -   Bir gövde bölümünde özellik değerlerinin belirtilmesine izin ver, ancak şablon kullanıldığında yalnızca parametre değerleri sağlanmalıdır. Örneğin, bir havuzun tüm tanımı gövdeye yerleştirilebilecek ve havuz kimliği için yalnızca bir parametre tanımlanmış olabilir. bir havuz oluşturmak için bu nedenle yalnızca bir havuz KIMLIĞI dizesinin sağlanması gerekir.
        
    -   Şablon gövdesi, Batch ve Batch tarafından çalıştırılacak uygulamalar hakkında bilgi sahibi olan birisi tarafından yazılabilir; Şablon kullanıldığında yalnızca yazar tanımlı parametrelerin değerlerinin sağlanması gerekir. Derinlemesine toplu Iş ve/veya uygulama bilgisi olmayan bir Kullanıcı bu nedenle şablonları kullanabilir.

-   **Değişkenler**

    -   Basit veya karmaşık parametre değerlerinin tek bir yerde belirtilmesine ve şablon gövdesinde bir veya daha fazla yerde kullanılmasına izin verin. Değişkenler, şablon boyutunu basitleştirecek ve azaltabilir, Ayrıca özellikleri değiştirmek için bir konuma sahip olmaya daha fazla bakım yapabilir.

-   **Üst düzey yapılar**

    -   Bazı daha yüksek düzey yapılar, Batch API 'Lerinde henüz kullanılamayan şablonda kullanılabilir. Örneğin, bir görev fabrikası, iş için birden çok görevi oluşturan bir iş şablonunda tanımlanabilir ve ortak bir görev tanımı kullanılır. Bu yapılar, her görev için bir dosya gibi birden çok JSON dosyasını dinamik olarak oluşturmak ve bir paket Yöneticisi aracılığıyla uygulama yüklemek için betik dosyaları oluşturmak için kod ihtiyacını ortadan kaldırmak zorunda kalmaz.

    -   Bu yapılar bir noktada Batch hizmetine eklenebilir ve Batch API 'Lerinde, UIS, vb. kullanılabilir.

### <a name="pool-templates"></a>Havuz şablonları

Havuz şablonları, parametrelerin ve değişkenlerin standart şablon özelliklerini destekler. Ayrıca, aşağıdaki üst düzey yapıyı da destekler:

-   **Paket başvuruları**

    -   İsteğe bağlı olarak, yazılımın paket yöneticileri kullanılarak havuz düğümlerine kopyalanmasına izin verir. Paket Yöneticisi ve paket KIMLIĞI belirtilmiş. Bir veya daha fazla paket bildirerek, gerekli paketleri alan, betiği yükleyen ve betiği her havuz düğümünde çalıştıran bir betik oluşturmaktan kaçınabilirsiniz.

Aşağıda, FFmpeg yüklenmiş bir Linux VM havuzu oluşturan bir şablon örneği verilmiştir. Kullanmak için, yalnızca bir havuz KIMLIĞI dizesi ve havuzdaki VM sayısını sağlayın:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Şablon dosyası _Pool-FFmpeg. JSON_olarak adlandırıldıysa, şablonu aşağıdaki gibi çağırın:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

CLI, `poolId` ve `nodeCount` parametreleri için değer vermenizi ister. Ayrıca, parametreleri bir JSON dosyasında da sağlayabilirsiniz. Örneğin:

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

Parameters JSON dosyası *Pool-Parameters. JSON*olarak adlandırıldıysa, aşağıdaki gibi şablonu çağırın:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>İş şablonları

İş şablonları, parametrelerin ve değişkenlerin standart şablon özelliklerini destekler. Ayrıca, aşağıdaki üst düzey yapıyı da destekler:

-   **Görev fabrikası**

    -   Bir görev tanımındaki bir iş için birden çok görev oluşturur. Üç tür görev fabrikası desteklenir – parametrik tarama, dosya başına görev ve görev koleksiyonu.

Aşağıda, FFmpeg ile iki alt çözünürlükte birine yönelik MP4 video dosyalarını transyönelik bir iş oluşturan bir şablon örneği verilmiştir. Kaynak video dosyası başına bir görev oluşturur. İş girişi ve çıkışı için dosya grupları hakkında daha fazla bilgi için bkz. [dosya grupları ve dosya aktarımı](#file-groups-and-file-transfer) .

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Şablon dosyası _iş-FFmpeg. JSON_olarak adlandırıldıysa, şablonu aşağıdaki gibi çağırın:

```azurecli
az batch job create --template job-ffmpeg.json
```

Daha önce olduğu gibi, CLı parametreler için değerler vermenizi ister. Ayrıca, parametreleri bir JSON dosyasında da sağlayabilirsiniz.

### <a name="use-templates-in-batch-explorer"></a>Batch Explorer Şablonlar kullanma

Batch bir CLı şablonunu [Batch Explorer](https://github.com/Azure/BatchExplorer) masaüstü uygulamasına (eski adıyla batchlabs) yükleyebilirsiniz. Ayrıca, Batch Explorer galerisinde önceden tanımlı havuz ve iş şablonları arasından seçim yapabilirsiniz.

Bir şablonu karşıya yüklemek için:

1. Batch Explorer ' de, **Galeri** > **Yerel Şablonlar**' ı seçin.

2. Yerel bir havuz veya iş şablonu seçin veya sürükleyip bırakın.

3. **Bu şablonu kullan**' ı seçin ve ekrandaki istemleri izleyin.

## <a name="file-groups-and-file-transfer"></a>Dosya grupları ve dosya aktarımı

Çoğu iş ve görev giriş dosyaları gerektirir ve çıkış dosyaları üretir. Genellikle, giriş dosyaları ve çıkış dosyaları istemciden düğüme ya da düğümden istemciye aktarılır. Azure Batch CLı uzantısı, dışarıda dosya aktarımını soyutlar ve her Batch hesabıyla ilişkilendirebileceğiniz depolama hesabını kullanır.

Bir dosya grubu, Azure depolama hesabında oluşturulan bir kapsayıcıya karşılık gelir. Dosya grubunun alt klasörleri olabilir.

Batch CLı uzantısı, dosyaları istemciden belirtilen bir dosya grubuna yüklemek ve dosyaları belirtilen dosya grubundan bir istemciye indirmek için komutlar sağlar.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Havuz ve iş şablonları, dosya gruplarında depolanan dosyaların havuz düğümlerine kopyalama veya havuz düğümlerinin bir dosya grubuna geri yüklenmesi için belirtilmesine izin verir. Örneğin, daha önce belirtilen iş şablonunda, dosya grubu *FFmpeg-Input* , kaynak video dosyalarının konumu Transkodlama için düğüme kopyalandıkları için, görev fabrikası için belirtilir. *FFmpeg-output* dosya grubu, dönüştürme kodlanmış çıkış dosyalarının her bir görevi çalıştıran düğümden kopyalandığı konumdur.

## <a name="summary"></a>Özet

Şablon ve dosya aktarımı desteği şu anda yalnızca Azure CLı 'ye eklenmiştir. Amaç, araştırmacılar ve BT kullanıcıları gibi Batch API 'Lerini kullanarak kod geliştirmeye gerek olmayan kullanıcılar için Batch kullanabilecek hedef kitleyi genişletmektir. Kodlama olmadan, Azure, Batch ve Batch tarafından çalıştırılacak uygulamalar hakkında bilgi sahibi olmayan kullanıcılar, havuz ve iş oluşturma için şablonlar oluşturabilir. Şablon parametreleriyle, toplu Iş hakkında ayrıntılı bilgi içermeyen ve uygulamalar şablonları kullanabilir.

Azure CLı için toplu Iş uzantısını deneyin ve bize bu makaleye ilişkin açıklamalarda ya da [Batch Community deposu](https://github.com/Azure/Batch)aracılığıyla geri bildirim veya öneri sağlayın.

## <a name="next-steps"></a>Sonraki adımlar

- Ayrıntılı yükleme ve kullanım belgeleri, örnekler ve kaynak kodu [Azure GitHub](https://github.com/Azure/azure-batch-cli-extensions)deposunda bulunabilir.

- Batch kaynaklarını oluşturmak ve yönetmek için [Batch Explorer](https://github.com/Azure/BatchExplorer) kullanma hakkında daha fazla bilgi edinin.
