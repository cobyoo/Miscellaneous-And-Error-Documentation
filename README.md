# Miscellaneous And Error Documentation

## Index
- [Hadoop 설치시 발생하는 오류 해결](#Hadoop-설치시-발생하는-오류-해결)
- [spark-shell 실행시 발생할 수 있는 경고](#spark-shell-실행시-발생할-수-있는-경고)
- [Yarn 관리하는 Memory 설정](#Yarn-관리하는-Memory-설정)     
- [Terminal 디렉토리 및 파일 색상 변경](#Terminal-디렉토리-및-파일-색상-변경)  

## Hadoop 설치시 발생하는 오류 해결

    1. 원인 : master에서 active standby 변경할 시 backup에서 바꾸는 경우.
       해결 : backup에서 namenode를 삭제하고, 다시 master에서 hdfs haadmin -getAllServiceState를 입력하고, 
              다시 backup에서 namenode를 실행하면 해결됨.
    
      
    2. 원인 1 : master에서 namenode 및 datanode가 실행이 안 될 경우
       원인 2 : namenode 알고있는 namenode id와 datanode 알고있는 namenode id가 서로 달라서 에러가 뜨는 것임.
       해결 : namenode format이 namenode, datanode가 실행되지 않을 때만 해야됨.
      
      
    3. 원인 : 갑자기 namenode가 동작하지 않을 경우.
       해결 : hadoop-namenode-master.log를 보면 됨.
              data/hdfs/namenode/current/VERSION 이 파일 권한이 없다는 경우가 많음.
              권한은 sudo chown -R user:user ./name
              그럼 chown 명령어로 ./name 밑에 있는 파일들을 전부 user 계정으로 사용자 및 그룹을 변경 시켜줌 
      
      
    4. 원인 : hdfs haadmin -getServiceState nn1 standby
              hdfs haadmin -getServiceState nn2 standby일 경우.
       해결 : 어떠한 방법을 해도 nn1이 active가 안되면 hdfs zkfc -formatZK을 입력하면 해결됨.
       
    5. 원인 : hdfs dfsadmin -report not working
    
       해결 : systemctl stop firewalld 방화벽을 해제 시 문제해결 


## spark-shell 실행시 발생할 수 있는 경고 

    1. 경고 메시지 :  Failed to created SparkJLineReader
       원인 : spark-shell을 실행시키는 user 홈 디렉토리에 Permission denied 경우 접근 권한이 없는 경우 발생함.
       해결 : spark-shell user 홈 디렉토리 chmod로 권한 추가함
       
       
    2. 경고 메시지 :  Service 'SparkUI' could not bind on port 4040. Attemption port 4041.
       원인 : Default로 기본 포트는 4040으로 시작하며 4041이 뜨는 이유는 
              spark-shell 실행 중이면 1증가로 4041포트로 접근하겠다는 경고임.
       해결 : 에러가 발생한 것이 아니기 때문에 신경을 안써도 됨.


## Yarn 관리하는 Memory 설정
    1. yarn.nodemanager.resource.cpu-vcores
    2. yarn.nodemanager.resource.memory-mb
    3. yarn.scheduler.minimum-allocation-mb
    4. yarn.scheduler.maximum-allocation-mb
    
    
## Terminal 디렉토리 및 파일 색상 변경
    export LS_COLORS="di=00;36:fi=00;37" 입력 후 source ~/.bashrc 해서 적용시키면 해결함. 
       
