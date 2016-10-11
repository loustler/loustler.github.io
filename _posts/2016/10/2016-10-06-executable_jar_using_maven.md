---
layout: post
title:  "maven을 이용해 executable jar(실행가능한 jar) 만들기"
date:   2016-10-06 22:30:00 +0900
categories: [languages, etc]
tags : [java, tools, maven]
---
`executable jar`를 `maven`을 통해 어떻게 만들까?

<!--more-->

회사에서 `executable jar`를 만들 일이 있어

방법을 찾아보다 `maven`을 통해 해결하곤 포스트를 쓴다.

사실 `executable jar`를 만들 일이 없었기도 했고

만들어본 적이 없었다.

첫 회사인 이곳에서는 주로 `PHP`를 했었다.

`Java`를 공부하고 배워서 입사하여 `PHP`를 처음 사용하고 회사에서 주로 사용하게 되었다.

곧 `Java`만 사용하겠지만..

바로 본론으로 가겠다.

`executable jar`를 만들 때 두 가지 방법을 고려했었다.

- `IDE`를 통해 만드는 방법
- `CLI`를 통해 만드는 방법

이 두가지를 주로 고민을 했었는데

이 프로젝트도 `maven`으로 `build`를 한 건데 그럼 `maven`을 사용하면 되지 않을까 생각을 했다.<br>
~~IDE인 IntelliJ를 사용하려다가 자꾸 안된 것도 있지만~~

그럼 `maven`을 통해 `executable jar`를 만들어보자.

먼저 `maven`을 설치하자.

필자가 `mac`을 사용하다 보니 `mac`을 위주로 설명하겠다.

먼저 `homebrew`에서 `maven`을 다운받자.

커맨드는 다음과 같다.

{% highlight shell %}
  brew install maven
{% endhighlight %}

`brew`를 `maven`을 설치했는가?

그럼 프로젝트에 가서 `maven`의 설정 파일인 `pom.xml`을 열자.

그리고 아래와 같이 넣어보자

{% highlight xml %}
<!-- <project> 보통 윗부분에 넣는 프로젝트 설정이다. package를 jar로 명시적으로 넣어주자. </project> -->
<packaging>jar</packaging>
~~~~~~
 생략
~~~~~~
<!-- <build> 이 사이에 넣자 </build>  -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-assembly-plugin</artifactId>
    <configuration>
        <!-- 프로젝트 루트에 jar를 만들기 위해 아래의 변수를 설정했다. -->
        <outputDirectory>${project.basedir}</outputDirectory>
        <archive>
            <manifest>
                  <!-- FQCN으로 public static void main(String[] args)가 있는 메인 클래스를 넣자 -->
                <mainClass>io.loustler.application.MainApplication</mainClass>
            </manifest>
        </archive>
        <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
        </descriptorRefs>
    </configuration>
    <executions>
        <execution>
            <id>make-assembly</id>
            <phase>package</phase>
            <goals>
                <goal>single</goal>
            </goals>
        </execution>
    </executions>
</plugin>
~~~~~~
 생략
~~~~~~
{% endhighlight %}

위의 `XML`이 바로 `maven-assembly-plugin`이다.

이 `plugin`을 이용해서 `dependency lib`를 곧 만들 `executable jar`에

넣어서 만들어버리는 것이다.

이렇게 만들면 `dependency lib`가 `jar`안에 포함되어 있어 실행 에러가 나지않는다.

자 이제 `maven`을 이용해 `executable jar`를 만들자

아래의 과정을 따라해보자.

- 먼저 `terminal`을 연다.
- 프로젝트가 있는 디렉토리로 이동한다
{% highlight shell %}
 cd /path/to/project/directory
{% endhighlight %}
- 프로젝트 루트 위치 즉, `pom.xml`이 위치한 곳에서 아래의 커맨드를 입력한다
{% highlight shell %}
 mvn clean
{% endhighlight %}
- `mvn clean`이 완료되었다면 아래의 커맨드를 입력하자.
{% highlight shell %}
 mvn package -DskipTests
{% endhighlight %}

자 그러면 프로젝트 루트 디렉토리에 `jar-with-dependencies`가 포함된 `.jar`가 생성되었을 것이다.

`<build> </build>` 이 사이에 설정한 `<finalName>`에 `-jar-with-dependencies`가 뒤에 추가된 `.jar`가 생성될 것이다.

{% highlight shell %}
 java -jar *-jar-with-dependencies.jar
{% endhighlight %}

위와 같은 식으로 실행해보자

실행이 잘 될 것이다.
