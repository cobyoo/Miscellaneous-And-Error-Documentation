# Miscellaneous And Error Documentation

## Index
### Miscellaneous
- [Spark Configurations Yarn](#Spark-Configurations-Yarn)      
- [Terminal 디렉토리 및 파일 색상 변경](#Terminal-디렉토리-및-파일-색상-변경)  
- [Ubuntu Server 사용중인 노드 및 특정포트 종료](#Ubuntu-Server-사용중인-노드-및-특정포트-종료)
- [MySQL 명령어](#MySQL-명령어)
- [Ubuntu Server 설정](#Ubuntu-Server-설정)
- [Java 실행 Option](#Java-실행-Option)
- [JVM Memory 영역에 대한 설명](#JVM-Memory-영역에-대한-설명)
- [VSCode Git 연동](#VSCode-Git-연동)
- [Spark 실행 명령어](#Spark-실행-명령어) 
- [Spark에서 local file 로드 방법](#Spark에서-local-file-로드-방법)
- [10G 해당 네트워크 인터페이스의 속도를 설정하는 데 문제가 발생 해결](#10G-해당-네트워크-인터페이스의-속도를-설정하는-데-문제가-발생-해결)
- [Spark Iceberg Minio](#Spark-Iceberg-Minio)
- [Tmux 명령어](#Tmux-명령어)
- [MLOps Pipeline Document](#MLOps-Pipeline-Document)
- [Pinecone](#Pinecone)

### Error Documentation 
- [Hadoop 설치시 발생하는 오류 해결](#Hadoop-설치시-발생하는-오류-해결)
- [spark-shell 실행시 발생할 수 있는 경고](#spark-shell-실행시-발생할-수-있는-경고)
- [Cassandra 설정시 발생하는 오류 해결](#Cassandra-설정시-발생하는-오류-해결)   
- [Ignite Off-Heap Memory 부족 해결](#Ignite-Off-Heap-Memory-부족-해결)
- [Ignite Java Heap Space 부족 해결](#Ignite-JAVA-Heap-Space-부족-해결)

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


## Spark Configurations Yarn 

    1. spark.yarn.am.(cores/memory) - Client Mode 
        - AM 코어/메모리 수 (Client Mode에서는 Spark Driver가 Yarn Cluster에서 수행되지 않음)
        - spark.driver.cores/memory - Cluster Mode
    2. spark.yarn.max.executor.failures
        - 애플리케이션 Fail을 결정짓는 Executor Fail 수 (기본 값: numExecutors * 2, minimum 3)
    3. spark.executor.instances (컨테이너 수)
        - Static allocation에서 Executor의 수 (기본 값 2)
        - spark.dynamicAllocation.enabled에서는 이 값을 초기값으로 할당 후 동적으로 증가
    4. spark.executor.cores (컨테이너 내 스레드 수)
        - Executor에 할당할 코어 수 (기본 값 Yarn: 1, Standalone/Mesos: 사용 가능한 전체 코어 수)
    5. spark.yarn.(executor/driver/am).memoryOverhead
        - Executor당 할당할 off-heap 메모리 크기
        - executorMemory * 0.10 (minimum 384MB)
    
## Cassandra 설정시 발생하는 오류 해결
    
    1. 경고 메시지 : Failed to connect to '127.0.0.1:7199': Connection refused (Connection refused)
       원인 : cassandra.yaml 구성 파일 오류 
       해결 : sudo rm -rf /var/lib/cassandra/data/system/*
    
    
## Terminal 디렉토리 및 파일 색상 변경
    export LS_COLORS="di=00;36:fi=00;37" 입력 후 source ~/.bashrc 해서 적용시키면 해결함. 
    
## Ubuntu Server 사용중인 노드 및 특정포트 종료
    sudo lsof -i :특정포트
    sudo kill -9 [프로세스ID]
    killall node 노드 서버 모두 종료 
       
## MySQL Error
    에러 : Data truncated for column
    원인 : 데이터의 일부분이 잘렸을 때 나오는 메세지로 컬럼 자료형의 범위를 초과 하였을 때 발생함. 
    해결 : DATE_FORMAT(sysdate(),'%Y%m%d')

## Ubuntu Server 설정  
    - ubuntu server (sshd server active) 
      Since it is private, ssh server is disabled by default.
      ssh server 설치 명령어 : sudo apt install openssh-server 
      ssh 서버 시작 명령어 : 
                             - sudo systemctl enable sshd 
                             - sudo systemctl restart sshd 

## Ignite Off-Heap Memory 부족 해결
    원인 : Ignite 서버에서 start시 사용하는 Config용 XML파일에서 설정들을 로그 정보에서 제시해 준 3가지 방법으로 해결 
    해결 : 
            1. DataRegionConfiguration 설정 중 maxSize값을 늘려주면 해결 
            
            2. DataRegionConfiguration 설정 중 persistenceEnabled값을 추가해주면 해결 
               <property name="defaultDataRegionConfiguration">
                    <bean class="org.apache.ignite.configuration.DataRegionConfiguration">
                        <!-- 10 GB initial size. -->
                        <property name="initialSize" value="#{10L * 1024 * 1024 * 1024}"/>
                        <!-- 50 GB maximum size. -->
                        <property name="maxSize" value="#{50L * 1024 * 1024 * 1024}"/>
                        <property name="persistenceEnabled" value="true"/>
                        <!-- Increasing the buffer size to 1 GB. -->
                        <property name="checkpointPageBufferSize" value="#{1024L * 1024 * 1024}"/>
                    </bean>
               </property>
                    
            3. eviction 또는 expiration 정책을 설정 
               - eviction : 공간이 필요할 때 데이터를 특정 알고리즘에 따라 지우는 설정
               - expiration : 특정 기간 이후의 데이터를 지우는 설정

## Ignite Java Heap Space 부족 해결
    원인 : Ignite 서버에 과도한 요청이나 일정 범위를 벗어나는 데이터 요청이 들어오는 경우 Ignite에서 발생하는 에러 
    해결 : JVM 할당 메모리를 늘려주면 당장은 해결되지만, 리소스를 무한정 늘릴 수만은 없으니 서버 리소스 맞게 변경
           Ignite.sh 파일을 열어 JVM_OPTS 값을 변경
           
           vi Ignite.sh 
           JVM_OPTS="-Xms4g -Xmx8g -server -XX:MaxMetaspaceSize=4G" 서버 리소스에 맞게 할당하는 용량 늘려줌
           
## Java 실행 Option
    1. -X Option 
       -Xms: 초기 Heap size 설정
       -Xmx: 최대 Heap size 설정
       -Xss: 각 Thread에 할당되는 Stack size 설정
       -Xmn: New 영역을 위한 Heap size 설정 
    
    2. -XX Option
       -XX:PermSize: 초기 Permanent size 설정
       -XX:MaxPerSize: 최대 Permanent size 설정 
       
    3. Heap Size 구하기 
       long heapSize = Runtime.getRuntime().totalMemory();
       String haepSizeMB = (heapSize / (1024*1024)) + "MB";
       
## JVM Memory 영역에 대한 설명 
    - Method Area : 메소드와 클래스 변수를 저장하기 위한 공간, 모든 프로그램에 의해 공유
    - Heap Area : 사용자가 생성하는 Java Object들이 저장되는 공간, 동적으로 할당하여 사용되어짐
    - Stack Area : 메소드 호출시 해당 메소드의 매개변수, 지역변수, 임시변수 등을 저장하기 위한 Stack 구조의 메모리
    - Native Haep Area : Java Object가 아닌 Native Object들이 거주하는 공간, OS 차원에서 결정
    - Permanent Space : Class에 대한 Meta 정보를 저장하는 공간(Java Heap의 하위 영역)
       
## VSCode Git 연동
    - 1. git --version 
    - 2. git init
    - 3. git 레퍼지토리 생성 후 레퍼지토리 주소를 복사 
    - 4. git remote add origin '복사한 주소'
    - 5. git add .
    - 6. git commit -m '메세지'
    - 7. git push origin master 
    
    - 레퍼지토리를 private로 설정하셨다면, Github의 아이디와 비밀번호를 확인하는 창이 나옴 
    - 아이디와 비밀번호를 입력하면 로그인 됨 
    - 8. Github에서 파일 확인 
    
## Spark 실행 명령어
    - 1. 경로 : /spark/sbin 
    - 2. 실행 명령어 : 
            ./start-master.sh (마스터 노드 실행)
            ./start-workers.sh (워커 노드 실행)
            
## Spark에서 local file 로드 방법
    - Spark Context 에서 textFile 을 호출 할때는 모든 익스큐터가 접근할 수 있는 동일 경로에 파일이 있어야 함. 
    - 이것은 로딩 속도를 고려한 것으로 대용량 파일을 로딩 할때 익스큐터들이 동시에 파일의 일부를 로딩할 수 있도록 하는 방법.
    - 문제는 익스큐터들이 수백, 수천이 되는 경우가 많으므로 각 익스큐터가 접근할 수 있는 로컬 파일 시스템에 동일 디렉토리를 만들고 파일을 복사하는 것은 매우 비효율적.  
    - 따라서 NFS, S3 등 공유가 가능한 분산 파일시스템 또는 클라우드 파일 시스템을 사용. HDFS를 사용할때는 이런 제약이 없음.
    - 이를 피하기 위해서는 드라이버에서 로컬 파일을 Spark Context 바깥에서 (즉, 일반적인 scala 파일 열기 및 읽기 함수 이용) 읽어들인후 Spark Context의 Parallelize() 를 이용해 RDD 생성하면 됨.  
    - 다만 로딩 속도가 길어지는 것은 어쩔 수 업음.
    - 결론적으로 textFile()을 이용한 로컬 파일 시스템의 파일을 읽을 때는 모든 익스큐터가 접근가능한 동일 경로에 동일 파일을 복사해야 함.
    - spark에서 로컬파일 시스템의 데이터를 그냥 로드하는 경우는 없고 대부분 HDFS, S3 등의 분산 파일 시스템을 이용하므로 사실 크게 신경 쓸 부분은 아님.

## 10G 해당 네트워크 인터페이스의 속도를 설정하는 데 문제가 발생 해결
    - ethtool enp26s0f1 : 우선, 네트워크 인터페이스가 해당 속도를 지원하는지 확인하려면 다음 명령어를 실행.
    - 이 명령어는 네트워크 인터페이스의 현재 상태를 보여줍니다. "Supported link modes" 섹션을 확인하면 해당 인터페이스가 지원하는 링크 모드를 볼 수 있음. 이 부분에 10000baseT/Full 이 있는지 확인.
    - 만약 10000baseT/Full 이 지원되지 않는다면, 네트워크 인터페이스의 속도를 10000Mb/s로 설정하는 것은 불가능. 이 경우, 다른 가능한 속도를 선택해야 함.
    - 또한, 다음과 같이 autoneg를 먼저 끄고 속도를 설정.
    - sudo ethtool -s enp26s0f1 autoneg off
    - sudo ethtool -s enp26s0f1 speed 10000 duplex full

## Spark Iceberg Minio
![Image](https://github.com/user-attachments/assets/7f1cf0d1-a063-4c4c-8c34-0231af0c9a34)
![Image](https://github.com/user-attachments/assets/1e724bbd-dd88-4b3b-9cd6-56ae8a749eab)
![Image](https://github.com/user-attachments/assets/b368dc68-5a6d-4676-a951-3b9317251e21)

## Tmux-명령어
    - tmux ls 
    - tmux attach -t cloud-datahub
    - Ctrl + b 방향키 - 이동 
    - Ctrl + b + d 세션 나가기 ( detach )

## MLOps Pipeline Document
        
    1. MLOps Pipeline Structure 
    
    Pipeline for Continuous Deployment and Automation of Machine Learning Models.
![Image](https://github.com/user-attachments/assets/e6d8fe77-2293-4a0f-87d9-f689c386b564)

    Platform Description
    
    Apache Kafka - ⁠Apache Kafka
    
    What is Apache Kafka? 
    Answer Apache Kafka is an open-source distributed streaming platform.
    It is designed for building real-time data pipelines and streaming applications, offering high throughput, fault-tolerance and horizontal scalability.
    Kafka is widely utilized for various use cases including real-time analytics, log aggregation, and event-driven architectures.
    
    
    Apache JenkinsWhat is Apache Jenkins?
    AnswerApache Jenkins is an open-source automation server for continuous integration and continuous delivery(CI/CD).
    It automates the software development process, enabling developers to test and deploy code changes rapidly.
    Jenkins boasts a rich plugin ecosystem, allowing seamless integration with various development environments and tools. 
    
    
    MLFlow What is MLFlow?
    Answer MLFlow is an open-source platform for managing the end-to-end machine learning lifecycle.
    It provides tools for tracking experiments, packaging code into reproducible runs, and sharing and deloying models.
    MLFlow supports various machine learning libraries and frameworks, promoting collaboration and reproducibility in ML projects. 
    
    
    Git LabWhat is Git Lab?
    Answer GitLab is a comprehensive DevOps platform that provides source code collaboration, CI/CD, issue tracking, and code review on a single platform.
    With flexible deployment options and integrated security features, GitLab simplifies and strengthens the software development prcess.
    
    
    Delta Lake What is Delta Lake?
    Answer Delta Lake is an open-source storage layer that brings reliability to data lakes.
    It provides ACID transactions, schema enforcement, and time travel capabilities on top of existing data lakes. 
    Delta Lake enables data engineering and data science teams to build robust and scalable data pipelines with ease.

    2. How to use it 
    
    First, the AI engineer writes the code to train the AI model.
    The written code is git push/ git pull to GitLab.
    
<img width="991" alt="Image" src="https://github.com/user-attachments/assets/b5b30d26-b06d-475d-b3f0-a1f6599da9a3" />   

    This is the configuration screen in Jenkins for connecting to GitLab.

<img width="1489" alt="Image" src="https://github.com/user-attachments/assets/1caf62de-db9d-41e6-94cc-2c7f4e98403b" />

    Create the project in Jenkins and pipeline it with Git Lab.
    Select 'Build when a change is pushed to Git' under Build Triggers.

![Image](https://github.com/user-attachments/assets/ecf29aba-eb3c-4b73-ac97-93aad14d6d4d)

![Image](https://github.com/user-attachments/assets/000240a2-81a4-4886-9331-2d640cc491d7)

    - Below is an example of setting up a Jenkins pipeline using a Jenkinsfile.
    
    pipeline {
        agent any
        
        stages {
            stage('Install dependencies') {
                steps {
                    script {
                        // Install necessary Python dependencies
                        sh 'pip install mlflow torch transformers pandas sqlalchemy psycopg2'
                    }
                }
            }
            
            stage('MLflow run') {
                steps {
                    script {
                        // Run MLflow project
                        sh '/usr/bin/python3 model.py'
                    }
                }
            }
        }
        
        post {
            success {
                echo 'Build completed successfully.'
            }
            failure {
                echo 'Build failed!'
            }
        }
    }

    
    This pipeline script performs the following actions.
    
    Install dependencies : Install necessary Python dependencies using pip.
    MLFlow run : Executes the MLFlow project by running the 'model.py' script.
    Post-build actions : 
    
    'success' : Displays a message if the build completes successfully.
    'failure' : Displays a message if the build fails.

    
    Jenkins Status Screen - ⁠⁠⁠Jenkins LinksWhen a build is executed, the final result appears as follows.
    You can perform builds via Jenkinsfile stored in GitLab and manage versions with MLflow

![Image](https://github.com/user-attachments/assets/c86db319-8296-469d-b675-8211d7bd28b9)

![Image](https://github.com/user-attachments/assets/2d05309d-2a26-406c-afd7-c20706e83f8b)
    
    MLflow Tracking - UIInformation such as Run Command, Duration, Parameters, Metrics, Server, etc., can be viewed.

<img width="1502" alt="Image" src="https://github.com/user-attachments/assets/eac7208c-c29a-40e1-9412-e26f0f62b823" />

    
    3. Data Stored in Delta Lake
    
    View in Jupyter Notebook - Data Stored in Delta Lake 

<img width="1502" alt="Image" src="https://github.com/user-attachments/assets/60a80f00-b5dd-4a86-822c-ef525b68ea3e" />

## Pinecone
    Billing 
![Image](https://github.com/user-attachments/assets/c7954a84-3a53-427d-bcc2-c489c745c02c)

    Index
![Image](https://github.com/user-attachments/assets/a6816315-530f-4e19-afa2-c669072886a8)
![Image](https://github.com/user-attachments/assets/74d326a6-7ebe-4d07-9ec1-0362dae5df8e)
    

