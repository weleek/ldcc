### MAVEN 빌드 설정

###### 제반지식

> classpath와 path의 차이로 path는 기본적인 환경변수로 javac의 위치와 javac가 컴파일하기 위한 라이브러리의 경로를 찾는데 필요하며 classpath의 경우 jvm이 해당 자바클래스파일을 실행하기 위해 필요하다.

##### 단순 의존성 설정 구성시

- 각 모듈 끼리 의존성 설정만 하여 사용하는 경우 (부모-자식 X)

> 단순 의존성 참조 이므로 클래스에 대한 참조만 가능하다.

##### 모듈 의존성 설정 구성시

- 라이브러리의 의존성 설정을 지우고 단순 프로젝트 끼리 의존성을 엮어주었기에 툴에서는 설정이 먹히지만 maven 을 이용한 컴파일 시에는 알수가 없게 됨.
  따라서 툴에서의 빌드는 동작하나 maven을 통한 package 등의 라이프사이클은 동작이 되지 않는다. (A프로젝트에서 B프로젝트의 리소스등을 참조할 수 없다.)

```xml
<!-- 라이브러리 처럼 참조되는 core 프로젝트 pom.xml -->
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.jade</groupId>
    <artifactId>core</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <springframework-version>4.1.7.RELEASE</springframework-version>
        <slf4j-version>1.7.6</slf4j-version>
    </properties>

    <build>
        <plugins>
            <!-- compile -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                    <encoding>${project.build.sourceEncoding}</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${springframework-version}</version>
            <exclusions>
                <exclusion>
                    <groupId>commons-logging</groupId>
                    <artifactId>commons-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${springframework-version}</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${springframework-version}</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework/spring-test -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${springframework-version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>jcl-over-slf4j</artifactId>
            <version>${slf4j-version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j-version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j-version}</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>

<!-- 메인 클래스가 존재하는 jar 형태로 배포되는 프로젝트의 pom.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <groupId>com.jade</groupId>
    <artifactId>cli</artifactId>
    <version>1.0-SNAPSHOT</version>
    <!-- packaging 방식 선언 -->
    <packaging>jar</packaging>
    <!-- 이름 -->
    <name>cli</name>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <springframework-version>4.1.7.RELEASE</springframework-version>
        <slf4j-version>1.7.6</slf4j-version>
    </properties>

    <build>
        <!-- java 소스 경로 위치 명시 -> jar에 class 위치하도록 -->
        <sourceDirectory>src/main/java</sourceDirectory>
        <!-- resource 경로는 다른 프로젝트에서 설명 -->
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <!-- 포함할 파일 명시 (mybatise.xml 파일들) -->
                    <include>**/*.xml</include>
                    <include>**/*.properties</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
        <plugins>
            <plugin>
                <!-- mavne jar plugin -->
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>2.3.2</version>
                <!-- 설정 정보 -->
                <configuration>
                    <archive>
                        <manifest>
                            <!-- jar로 package 될 때 classpath 추가 옵션 -->
                            <addClasspath>true</addClasspath>
                            <!-- public static void main() 함수가 있는 클래스 지정 -->
                            <mainClass>com.jade.CliApplication</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${springframework-version}</version>
            <exclusions>
                <exclusion>
                    <groupId>commons-logging</groupId>
                    <artifactId>commons-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${springframework-version}</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${springframework-version}</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework/spring-test -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${springframework-version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>jcl-over-slf4j</artifactId>
            <version>${slf4j-version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j-version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j-version}</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.24</version>
            <scope>compile</scope>
        </dependency>
    </dependencies>

</project>
```

###### 멀티 모듈 구성시

- 소스 폴더 구조를 하나의 폴더에서 구성하는게 제일 쉬운 방법이지만 굳이 같은 폴더에 있지 않아도 의존관계를 설정 할수 있으며 아래와 같이 자식 프로젝트에서 부모 프로젝트의 resource 파일을 직접 참조 가능한 점등이 다르다.

```xml
<!-- 부모  -->
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.jade</groupId>
    <artifactId>core</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <springframework-version>4.1.7.RELEASE</springframework-version>
        <slf4j-version>1.7.6</slf4j-version>
    </properties>

    <build>
        <plugins>
            <!-- compile -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                    <encoding>${project.build.sourceEncoding}</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${springframework-version}</version>
            <exclusions>
                <exclusion>
                    <groupId>commons-logging</groupId>
                    <artifactId>commons-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${springframework-version}</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${springframework-version}</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework/spring-test -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${springframework-version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>jcl-over-slf4j</artifactId>
            <version>${slf4j-version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j-version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j-version}</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>

<!-- 자식 프로젝트 pom.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <parent>
        <artifactId>core</artifactId>
        <groupId>com.jade</groupId>
        <version>1.0-SNAPSHOT</version>
        <relativePath>../core/pom.xml</relativePath>
    </parent>

    <modelVersion>4.0.0</modelVersion>
    <groupId>com.jade</groupId>
    <artifactId>cli</artifactId>
    <version>1.0-SNAPSHOT</version>
    <!-- packaging 방식 선언 -->
    <packaging>jar</packaging>
    <!-- 이름 -->
    <name>cli</name>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <springframework-version>4.1.7.RELEASE</springframework-version>
        <slf4j-version>1.7.6</slf4j-version>
    </properties>

    <build>
        <!-- java 소스 경로 위치 명시 -> jar에 class 위치하도록 -->
        <sourceDirectory>src/main/java</sourceDirectory>
        <!-- resource 경로는 다른 프로젝트에서 설명 -->
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <!-- 포함할 파일 명시 (mybatise.xml 파일들) -->
                    <include>**/*.xml</include>
                    <include>**/*.properties</include>
                </includes>
                <filtering>true</filtering>
            </resource>
            <resource>
                <directory>../core/src/main/resources</directory>
                <includes>
                    <!-- 포함할 파일 명시 (mybatise.xml 파일들) -->
                    <include>**/*.xml</include>
                    <include>**/*.properties</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
        <plugins>

            <plugin>
                <!-- mavne jar plugin -->
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>2.3.2</version>
                <!-- 설정 정보 -->
                <configuration>
                    <archive>
                        <manifest>
                            <!-- jar로 package 될 때 classpath 추가 옵션 -->
                            <addClasspath>true</addClasspath>
                            <!-- public static void main() 함수가 있는 클래스 지정 -->
                            <mainClass>com.jade.CliApplication</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <dependencies>

        <dependency>
            <groupId>com.jade</groupId>
            <artifactId>core</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.24</version>
            <scope>compile</scope>
        </dependency>

    </dependencies>

</project>
```

###### 사용 가능 명령어 모음

```bash
# maven을 이용해 프로젝트 구조 생성시 아래와 같이 생성
D:\develop\utils\apache-maven-3.5.4\bin\mvn archetype:generate -DgroupId=com.jade -DartifactId=core
```
