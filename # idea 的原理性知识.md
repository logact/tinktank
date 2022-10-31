一、facts与artifacts
Facets：表示某个module有的特征，比如web、strtus2、spring、hibernate等；
Artifacts：它是maven中的一个概念，表示某个module要如何打包，例如war exploded、war、jar、ear等等这种打包形式；
                 一个module有了Artifacts就可以部署到应用服务器中了！

Artifacts：项目的打包部署设置，

选择web application exploded,这个是以文件夹形式（war Exploded）发布项目，选择这个，发布项目时就会自动生成文件夹在指定的output directory。如果选择eb application archive，就是war包形式，每次都会重新打包全部的，将项目打成一个war包在指定的位置。

二、运行tomcat时，idea默认开始做的事情
1）编译，IDEA在保存/自动保存后不会做编译，不像Eclipse的保存即编译，因此在运行server前会做一次编译。编译后class文件存放在指定的项目编译输出目录下；

2）根据artifact中的设定对目录结构进行创建；

3）拷贝web资源的根目录下的所有文件到artifact的目录下；

4）拷贝编译输出目录下的classes目录到artifact下的WEB-INF下；

5）拷贝lib目录下所需的jar包到artifact下的WEB_INF下；

6）运行server，运行成功后，如有需要，会自动打开浏览器访问指定url。
