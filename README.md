# RonnieUtils2
Ronnie的Android工具库
## 如何生成自己的远端库
### 1、生成远端存储库地址
1、github 创建一个public的 repository（存储仓库：例RonnieUtils2）
###2、克隆远端存储库到本地
2、本地找到一个目录（最好新建；例如：Users/mac用户名/RonnieGitHubWorkSapce）
3、将该项目clone到这个目录中 （克隆完：Users/mac用户名/RonnieGitHubWorkSapce/RonnieUtils2）

###3、编写自己的库
4、新建一个Android项目（app）
5、新建一个Library的module
6、在该library中编辑自己的想要封装的方法、类
7、在app中引用该library；查看相应功能是否使用正常

### 4、本地生成 aar包
8、在library的根目录新建 一个 gradle文件；（例如：plugin_push.gradle）
```
apply plugin: 'maven'

ext{
    //从Github上clone下来的项目的本地地址
    GITHUB_REPO_PATH="/Users/mac电脑名/RonnieGitHubWorkSapce/RonnieUtils"//这里指定的就是刚刚新建项目后clone下来的在本地的路径（生成的aar等相关文件会被输出到这里）
    PUBLISH_GROUP_ID = 'com.rn.yao'
    PUBLISH_ARTIFACT_ID = 'test-show'
    PUBLISH_VERSION = '1.0.0'
}

uploadArchives {

    repositories.mavenDeployer {

        def deployPath = file(project.GITHUB_REPO_PATH)

        repository(url: "file://${deployPath.absolutePath}")
        // pom属性
        pom.groupId = project.PUBLISH_GROUP_ID
        pom.artifactId = project.PUBLISH_ARTIFACT_ID
        pom.version = project.PUBLISH_VERSION
        pom.packaging ='aar'
        pom.project {
            licenses {
                license {
                    name 'The Apache Software License, Version 2.0'
                    url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                }
            }
        }
    }
}


// 源代码一起打包
task androidJavadocs(type: Javadoc) {
    source = android.sourceSets.main.java.srcDirs
    classpath += project.files(android.getBootClasspath().join(File.pathSeparator))
}
// 对 java doc进行打包
task androidJavadocsJar(type: Jar, dependsOn: androidJavadocs) {
    classifier = 'javadoc'
    from androidJavadocs.destinationDir
}
//对Java sources打包
task androidSourcesJar(type: Jar) {
    classifier = 'sources'
    from android.sourceSets.main.java.srcDirs
}
artifacts {
    archives androidSourcesJar
//    archives androidJavadocsJar  // 执行这个任务会报错
}
```

9、将该`plugin_push.gradle`文件引入到 library的`build.gradle`中(这两个文件是同级目录下)
```
apply from: './plugin_push.gradle'
```

10、在Android Studio中的命令中运行 `./gradlew uploadArchives`

`查看 clone的目录，生成相应的aar包`
【结果-1】
![enter image description here](https://github.com/AudioOnly/RonnieUtils2/blob/master/image1.jpg)

【结果-2】
![enter image description here](https://github.com/AudioOnly/RonnieUtils2/blob/master/image2.jpg)

###5、上传本地aar到远端
11、cd到对应的目录下（例如：Users/mac用户名/RonnieGitHubWorkSapce/RonnieUtils2）
11.1.	git add .
11.2.	git commit -m 'commit描述'
11.3.	 git push -u origin master
`产看远端是否 已经上传了对应的文件`


###6、如何使用自己的远端库

12、在Android项目的根目录的`build.gradle`中增加
```
allprojects {
    repositories {
//        google()
//        jcenter()
        maven {
            url 'https://maven.aliyun.com/repository/jcenter'
        }
        maven {
            url 'https://maven.aliyun.com/repository/google'
        }
        maven {
             url "https://raw.githubusercontent.com/GitHub用户名/仓库项目名/master"
             //url "https://raw.githubusercontent.com/AudioOnly/RonnieUtils2/master"
        }
    }
}
```
13、在Android项目需要使用该依赖的 module中添加
```
dependencies {
    ...............
    //自己的远端库
    implementation 'com.rn.yao:test-show:1.0.0'
    //这里的规范为：   pom.groupId：pom.artifactId：pom.version
}
```


## 结束

