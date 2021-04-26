---
title: Azure HDInsight 'ta varsayılan Hive meta veri deposu dış meta yüklemeye geçirme
description: Azure HDInsight 'ta varsayılan Hive meta veri deposu dış meta yüklemeye geçirme
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 4a0258d5e448c59baa1cd63e98058fe7116a8485
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566124"
---
# <a name="migrate-default-hive-metastore-db-to-external-metastore-db"></a>Varsayılan Hive meta veri deposu DB 'yi dış meta veri deposu VERITABANıNA geçirme

Bu makalede, bir Hive için varsayılan meta veri [deposu DB](../hdinsight-use-external-metadata-stores.md#default-metastore) 'den HDInsight 'ta BIR dış SQL veritabanına nasıl meta verilerin geçirileceği gösterilmektedir. 

## <a name="why-migrate-to-external-metastore-db"></a>Neden dış meta veri deposu VERITABANıNA geçirilir?

* Varsayılan meta veri deposu DB temel SKU ile sınırlıdır ve üretim ölçeği iş yüklerini işleyemez.

* Dış meta veri deposu DB, aynı meta veri deposu DB 'yi paylaşan yeni HDInsight kümeleri ekleyerek müşterilerin Hive işlem kaynaklarını yatay olarak ölçeklendirmesine olanak sağlar.

* HDInsight 3,6 ' den 4,0 ' e geçiş için, Hive şema sürümünü yükseltmeden önce meta verilerin dış meta veri deposu VERITABANıNA geçirilmesi zorunludur. Bkz. [HDInsight 'tan iş yüklerini hdınsight 4,0 ' e geçirme 3,6](./apache-hive-migrate-workloads.md).

Varsayılan meta veri deposu DB 'nin sınırlı işlem kapasitesi olduğundan, meta verileri geçirirken kümedeki diğer işlerden düşük kullanım yapmanız önerilir.

Kaynak ve hedef veritabanları aynı HDInsight sürümünü ve aynı depolama hesabını kullanmalıdır. HDInsight sürümlerini 3,6 ' den 4,0 ' e yükseltiyorsanız, önce bu makaledeki adımları izleyin. Ardından, [buradaki](./apache-hive-migrate-workloads.md)resmi yükseltme adımlarını izleyin.

## <a name="prerequisites"></a>Önkoşullar

[Azure Data Lake Storage 1.](../overview-data-lake-storage-gen1.md)kullanıyorsanız, Hive tablo konumları büyük olasılıkla, Azure Data Lake Storage 1. için kümenin ıYapılandırma yapılandırmasına bağımlıdır. Bu konumları diğer kümelere taşınabilir hale getirmek için aşağıdaki betik eylemini çalıştırın. [Çalışan bir küme Için betik eylemi](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)' ne bakın.

Eylem, çözümlemeyin 'i tam yollarıyla değiştirmeye benzer.

|Özellik | Değer |
|---|---|
|Bash betiği URI 'SI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
|Düğüm türleri|Head|
|Parametreler|""|

## <a name="migrate-with-exportimport-using-sqlpackage"></a>SqlPackage kullanarak dışa/Içeri aktarma ile geçiş

An HDInsight kümesi 2020-10-15 yalnızca, kullanılarak oluşturulan Hive varsayılan meta veri deposu DB için SQL dışarı aktarma/Içeri aktarma Işlemini destekler `sqlpackage` .

1. [SqlPackage](/sql/tools/sqlpackage-download#get-sqlpackage-net-core-for-linux) 'i kümeye yükler.

2. Aşağıdaki komutu yürüterek varsayılan meta veri deposu VERITABANıNı BACPAC dosyasına dışarı aktarın.

    ```bash
    wget "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_metastore_tool.py"
    SQLPACKAGE_FILE='/home/sshuser/sqlpackage/sqlpackage'  # replace with sqlpackage location
    TARGET_FILE='hive.bacpac'
    sudo python hive_metastore_tool.py --sqlpackagefile $SQLPACKAGE_FILE --targetfile $TARGET_FILE
    ```

3. BACPAC dosyasını kaydedin. Aşağıda bir seçenek bulunur.

    ```bash
    hdfs dfs -mkdir -p /bacpacs
    hdfs dfs -put $TARGET_FILE /bacpacs/
    ```

4. BACPAC dosyasını [burada](../../azure-sql/database/database-import.md)listelenen adımlarla yeni bir veritabanına aktarın.

5. Yeni veritabanı, [Yeni bir HDInsight kümesinde dış meta veri deposu veritabanı olarak yapılandırılmaya](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)hazırlanmaktadır.

## <a name="migrate-using-hive-script"></a>Hive betiği kullanarak geçirme

2020-10-15 ' den önce oluşturulan kümeler, varsayılan meta veri deposu DB 'nin içeri/dışarı aktarılmasını desteklemez.

Bu tür kümeler için, [dış HIVE meta VERI deposu DB](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)ile ikinci bir küme kullanarak, [Hive tablolarını depolama hesapları genelinde kopyalama](./hive-migration-across-storage-accounts.md)Kılavuzu ' nu izleyin. İkinci küme aynı depolama hesabını kullanabilir, ancak yeni bir varsayılan dosya sistemi kullanması gerekir.

### <a name="option-to-shallow-copy"></a>"Yüzeysel" kopyalama seçeneği
Depolama alanı tüketimi, tablolar yukarıdaki kılavuz kullanılarak "derin" kopyalandıklarında Double olur. Kaynak depolama kapsayıcısındaki verileri el ile temizlemeniz gerekir.
Bunun yerine, "yÜzEySiz" tabloları, işlem dışı olmaları durumunda kopyalayabiliriz. HDInsight 3,6 ' deki tüm Hive tabloları varsayılan olarak işlem dışıdır, ancak yalnızca dış tablolar HDInsight 4,0 ' de işlemsel değildir. İşlem tablolarının derin kopyalanması gerekir. İşlemsel olmayan tabloları basit olarak kopyalamak için şu adımları izleyin:

1. Her Hive tablosu için ddl 'yi oluşturmak üzere kaynak kümesinin birincil baş düğümüne üzerinde betik [Hive-DDLS.sh](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-ddls.sh) yürütün.
2. DDL adlı yerel bir Hive betiğine yazılır `/tmp/hdi_hive_ddls.hql` . Bunu bir dış Hive meta veri deposu DB kullanan hedef kümede yürütün.

## <a name="verify-that-all-hive-tables-are-imported"></a>Tüm Hive tablolarının içeri aktarıldığını doğrulama

Aşağıdaki komut, tüm Hive tablolarını ve bunların veri konumlarını yazdırmak için meta veri deposu VERITABANıNDA bir SQL sorgusu kullanır. Yeni meta veri deposu VERITABANıNDA eksik bir tablo olmadığını doğrulamak için yeni ve eski kümeler arasındaki çıkışları karşılaştırın.

```bash
SCRIPT_FNAME='hive_metastore_tool.py'
SCRIPT="/tmp/$SCRIPT_FNAME"
wget -O "$SCRIPT" "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/$SCRIPT_FNAME"
OUTPUT_FILE='/tmp/hivetables.csv'
QUERY="SELECT DBS.NAME, TBLS.TBL_NAME, SDS.LOCATION FROM SDS, TBLS, DBS WHERE TBLS.SD_ID = SDS.SD_ID AND TBLS.DB_ID = DBS.DB_ID ORDER BY DBS.NAME, TBLS.TBL_NAME ASC;"
sudo python "$SCRIPT" --query "$QUERY" > $OUTPUT_FILE
```

## <a name="further-reading"></a>Daha fazla bilgi

* [HDInsight 3,6 ' den 4,0 ' e iş yüklerini geçirme](./apache-hive-migrate-workloads.md)
* [Depolama hesapları arasında Hive Iş yükü geçişi](./hive-migration-across-storage-accounts.md)
* [HDInsight üzerinde Beeline bağlantı](../hadoop/connect-install-beeline.md)
* [Sorun giderme Izin hatası tablo oluşturma](./interactive-query-troubleshoot-permission-error-create-table.md)