# 4_4_Hadoop_intro1
## Введение в Hadoop Задание1

Развернут контейнер.
В файловой системе hdfs создана личная папка /user/user_one

### cmd командная строка Windows
#### Сливаем тома в один
copy voyna-i-mir*.txt war-n-peace.txt
#### Создаем папку в контейнере
docker exec docker-hadoop-hive-parquet-datanode-1 touch /import
#### Копируем файл в созданную папку
docker cp war_n_peace.txt docker-hadoop-hive-parquet-datanode-1:/import/war_n_peace.txt  
Successfully copied 3.05MB to docker-hadoop-hive-parquet-datanode-1:/import/war_n_peace.txt

### docker exec
#### копируем файл в hdfs
hadoop fs -put docker-hadoop-hive-parquet-datanode-1:/import/war_n_peace.txt /user/user_one/war_n_peace.txt    
#### содержимое папки
hadoop fs -ls /user/user_one  
Found 1 items  
-rw-r--r--   3 root user_one    3048009 2023-12-06 10:29  
/user/user_one/war_n_peace.txt  
#### меняем права  
hadoop fs -chmod 755 /user/user_one/war_n_peace.txt  
hadoop fs -ls /user/user_one  
Found 1 items  
-rwxr-xr-x   3 root user_one    3048009 2023-12-06 10:29  
/user/user_one/war_n_peace.txt  
#### Размер файла  
hdfs dfs -du -s -h /user/user_one  
2.9 M  /user/user_one  
#### Меняем фактор репликации  
hadoop fs -setrep 2 /user/user_one/war_n_peace.txt  
#### Проверяем  
hadoop fs -ls /user/user_one  
Found 1 items  
-rwxr-xr-x   2 root user_one    3048009 2023-12-06 10:29  
/user/user_one/war_n_peace.txt  

___репликации не происходит___  
___проверяем:___  
hdfs fsck /user/user_one/war_n_peace.txt  
Connecting to namenode via http://namenode:50070/fsck?ugi=root&path=%2Fuser%2Fuser_one%2Fwar_n_peace.txt  
FSCK started by root (auth:SIMPLE) from /172.21.0.6 for path /user/user_one/war_n_peace.txt at Wed Dec 06 14:22:24 UTC 2023  
.
/user/user_one/war_n_peace.txt:  Under replicated BP-518321949-172.19.0.7-1701698256959:blk_1073741843_1019.   
__Target Replicas is 2 but found 1 replica(s).__
Status: HEALTHY  
 Total size:    3048009 B  
 Total dirs:    0  
 Total files:   1  
 Total symlinks:                0  
 Total blocks (validated):      1 (avg. block size 3048009 B)  
 Minimally replicated blocks:   1 (100.0 %)  
 Over-replicated blocks:        0 (0.0 %)  
 Under-replicated blocks:       1 (100.0 %)  
 Mis-replicated blocks:         0 (0.0 %)  
 Default replication factor:    3  
 Average block replication:     1.0  
 Corrupt blocks:                0  
 Missing replicas:              1 (50.0 %)  
 Number of data-nodes:          1  
 Number of racks:               1  
FSCK ended at Wed Dec 06 14:22:24 UTC 2023 in 14 milliseconds  
___Ожидается репликация, но выполнится не может. Вероятно не достаточно датанодов___

#### Подсчитываем количество строк   
hadoop fs -cat /user/user_one/war_n_peace.txt | wc -l  
10272


