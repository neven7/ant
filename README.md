# ant-report-testcaseinfo
自定义ant junit report报告，通过用例新增注解@TCInfo描述用例信息，report中展示这些用例信息。

打包：在ant工程目录下，直接运行sh build.sh， 在ant/build/lib目录下，会生成对应的jar，我们只需ant-junit.jar; 将ant-junit.jar发布到自己的私有仓库：

```
mvn deploy:deploy-file -DgroupId=com.weibo -DartifactId=ant-junit -Dversion=0.0.1 -Dpackaging=jar -Durl=http://10.13.1.139:30000/nexus/content/repositories/weiboqa/ -Dfile=/users/hugang/git/ant/build/lib/ant-junit.jar -DrepositoryId=weiboqa -s /Users/hugang/.m2/settings.xml

```


在测试工程pom.xml中依赖该jar包

```
       <dependency>
			<groupId>com.weibo.qa</groupId>
			<artifactId>ant-junit</artifactId>
			<version>0.0.1</version>
		</dependency>
```

在测试用例中新增@TCInfo信息

```
    @TCInfo(caseId="LabTest-01N01", caseDesc="实验测试1")
	@Test
	public void testSingleExposureBizId() {
	   ...
	}

```


使用ant build.xml执行用例，生成的xml中，新增：casedesc, caseid信息

```
<testcase casedesc="实验测试1" caseid="LabTest-01N01" classname="com.weibo.extend.lab.LabTest" name="testSingleExposureBizId" time="7.354">
```


#### 修改XSL

ant使用XSLT将XML转成HTML，report有2种形式: "frames" or "noframes"
>The frames format uses a stylesheet which is generating output only by redirecting.

>The noframes format does not use redirecting and generates one file called junit-noframes.html.

>Custom versions of junit-frames.xsl or junit-noframes.xsl must adhere to the above conventions.

分别对应junit-frames.xsl or junit-noframes.xsl

本文只针对noframes，故只需修改junit-noframes.xsl（<http://download.csdn.net/download/neven7/9381080>），默认xsl在apache-ant-1.9.6/etc目录下，如需自定义，则需要在report指定styledir路径，该路径保存自定义的junit-frames.xsl or junit-noframes.xsl
在执行的build中指定自定义xsl目录styedir:

```
<target name="junitreport" depends="testAll">
                  <junitreport todir="${report}">
                          <fileset dir="${report}/${mytime}">
                                  <include name="TEST-*.xml" />
                          </fileset>
                          <report styledir="/data1/WeTest/junitreport/" format="noframes" todir="${report}/${mytime}" />
                  </junitreport>
</target>
```

#### 结果展示

根据XML通过XSLT转换成自定义结果，将@Test中添加了@TCInfo注解的用例描述添加到report中


![这里写图片描述](http://img.blog.csdn.net/20151228220452351)

