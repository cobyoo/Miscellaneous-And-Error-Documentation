# Error-Cleanup-Documentation

## Index
- [Hadoop 설치 시 발생하는 오류 해결](#Hadoop-설치-시-발생하는-오류-해결)


## Hadoop 설치시 발생하는 오류 해결
    1. master에서 active standby 변경할 시 backup에서 바꾸는 경우에는 backup에서 namenode를 삭제하고, 
       다시 master에서 hdfs haadmin -getAllServiceState를 입력하고 다시 backup에서 namenode를 실행하면 해결됨.
    
      
    2. master에서 namenode format은 namenode, datanode가 실행되지 않을 때만 해야 되며,
       datanode가 실행되지 않는 경우 datanode에서 다른 2개의 datanode 프로세스들은 실행이 되는 반면,
       1개의datanode 프로세스가 실행이 안된다. 
       그럴 경우에는 대부분 namenode가 알고 있는 namenode id와 datanode가 알고 있는 namenode id가 서로 달라서 에러가 뜨는 것임.
      
      
    3. 갑자기 namenode가 동작하지 않을 경우에는 hadoop-namenode-master.log를 보면 됨.
       data/hdfs/namenode/current/VERSION 이 파일 권한이 없다는 경우가 많음.
       권한은 sudo chown -R user:user ./name
       그럼 chown 명령어로 ./name 밑에 있는 파일들을 전부 user 계정으로 사용자 및 그룹을 변경 시켜줌 
      
      
    4. hdfs haadmin -getServiceState nn1 standby
       hdfs haadmin -getServiceState nn2 standby
       일 경우 어떠한 방법을 해도 nn1이 active가 안되면 
       hdfs zkfc -formatZK을 입력하면 해결됨.
