---
layout: post
title: Ammonite Setup behind corporate proxy
date: 2021-05-22 19:20:23 +0900
category: scala
---

Ammonite provides a wonderful bash shell like environment written in Scala. It is the creation of Li Haoyi and provides powerful REPL environment to end users to do various day-to-day operations in the file system quickly and efficiently. This is quite similar to Jupyter notebooks providing you instant results or precise error messages of your expression evaluation. I do go by the creators opinion that it is a modern bash shell of the 21st century. 

Here I explain how to set up Ammonite on your Windows OS environment in a corporate enterprise set up.

# Install Coursier

- Coursier is a pure scala arifact fetching tool. Inorder to set it up download teh coursier jar file from <https://get-coursier.io/>. I prefer using the JAR launcher as the native exe launcher did not work consistently. 

```
> bitsadmin /transfer downloadCoursierCli https://git.io/coursier-cli "%cd%\coursier"
> bitsadmin /transfer downloadCoursierBat https://git.io/coursier-bat "%cd%\coursier.bat"
```

- Open command prompt and navigate to the downloaded location. 
Set the followng environment variables (Normally all internet traffic is routed through a prxy server)

```
-Xmx1024m
-Dhttps.proxyHost=<your proxy server>
-Dhttps.proxyPort=<your proxy server port>
-Dhttp.proxyHost=<your proxy server>
-Dhttp.proxyPort=<your proxy server port>
-Dhttp.proxyUser=<your username>
-Dhttp.proxyPassword=<your password>
-Djavax.net.ssl.trustStore=<location to you Java trust store>
```

If you want to know the steps for creating Java truststore please refer <https://vijayemmanuel.github.io/scala/2018/04/18/SBTSetup.html>


# Install Ammonite using Coursier
Now I explain I few coursier commands which will help you understand them as well as install Ammonite eventually

- Allow one to complete Maven coordinates. Here we get all the available versions of ammonite

```
> coursier complete com.lihaoyi:ammonite_2.13
```

- Lists the transitive dependencies of one or more dependencies. Here we get all the dependencis of ammonite in a tree format

```
> coursier resolve -t com.lihaoyi:ammonite_2.13:2.3.8
```

- Fetches the artifacts of one or more dependencies. We get the ammonite library to our local cache. Note the :: which implies get the build of ammonite corresponding to scala version

```
> coursier fetch com.lihaoyi::ammonite:2.3.8
```

- Checks if your system has a JVM and the standard Scala applications, and installs them if needed.

```
> coursier setup
```

- In order to update specific applications you can use the following 

```
> coursier update
```

# Running Ammonite and customizing
- Start ammonite (amm is the shorthand for ammonite) Ammonite does a remote logging. To stop doing it use the flag below

```
> amm --no-remote-logging 
```

- We can customise the start up behaviour by adding a file named predef.sc in $HOME/.ammonite folder

```
import $ivy.`com.lihaoyi::ammonite-ops:2.3.8`, ammonite.ops._ //, ammonite.ops.Implicit.Wd._
import $ivy.`com.lihaoyi::ammonite-shell:2.3.8`, ammonite.shell._
import $ivy.`org.scalatest::scalatest:3.2.9`, org.scalatest.Assertions._ //for intercept. usage intercept[InteractiveShelloutException] 
//val shellSession = ammonite.ShellSession()
//import shellSession._

import scala.collection.mutable

import ammonite.runtime.tools.{grep, time}
import ammonite.repl.tools.desugar
import ammonite.ops.{ % -> _, %% => _, _}

lazy val % = new ammonite.ops.Command (
    Vector("cmd","/c"),
    Map.empty,
    ammonite.ops.Shellout.executeInteractive _
)
lazy val %% = new ammonite.ops.Command (
    Vector("cmd","/c"),
    Map.empty,
    ammonite.ops.Shellout.executeIStream _
)

//Switch reference of `current` directory
object cd {
    import ammonite.util.Ref

    val focus :Ref[Path] : Ref(pwd)
    //override def toString = focus().toString

    //Change current directory of this repl session to given [[Path]]
    def apply(arg : Path): Unit = {
        if (exists! arg)
           if(arg == root || arg.isDir) {
               focus() == arg
           } else {
               println (s"$arg is not a directory")
           }
           else println(s"$arg does not exist")
    }

    def ~ = apply(home)
    def / (relpath: RelPath) = apply(focus()/relpath)
}

implicit def wd :Path = cd.focus()
repl.prompt.bind(wd.segments.toList.lastOption.getOrElse("") + " @ ")

//To change the default height of pprint in REPL
//repl.pprinter() = repl.pprintter().copy(defaultHeight = 5)
```

Thats it!!

Use Ammonite like your default bash shell and more...

- Example with Json parsing
Consider the following JSON file which needs to be parsed. 

```
{
    "ResourceTagMappingList" : [
        {
            "ResourceARN" : "arn:aws:cloudformation:eu-west-1:123456789:stack/jenkins/uuid",
            "Tags" : [
                {
                    "Key" : "owner",
                    "Value": "Devops"
                },
                {
                    "Key" : "project",
                    "Value": "CI"
                },
                {
                    "Key" : "aspire",
                    "Value": "yolo"
                }

            ]
        },
        {
            "ResourceARN" : "arn:aws:kms:eu-west-1:123456789:key/uuid",
            "Tags" : [
                {
                    "Key" : "owner",
                    "Value": "Dev"
                },
                {
                    "Key" : "project",
                    "Value": "Feature1"
                }

            ]
        },

    ]
}
```

Ammonite Way to read Json with Circe

```
@ import $ivy.`io.circe::circe-optics:0.14.1`
@ import $ivy.`io.circe::circe-parser:0.14.1`
@ import io.circe.parser._
@ val parsed = parse(read wd/ "aws.json"))

//Optics
@ root.ResourceTagMappingList.each.ResourceARN.string.getAll(parsed.right.get)

//Generics
@ import $ivy.`io.circe::circe-generic:0.14.1`
@ import io.circe._, io.circe.generic.semiauto._
@ case class Tag(Key: String, Value: String)
@ case class Resource(ResourceARN: String, Tags: List[Tag])
@ case class ResourceList(ResourceTagMappingList: List[Resource])

@ implicit val tag : Decoder[Tag] = deriveDecoder[Tag]
@ implicit val resource : Decoder[Resource] = deriveDecoder[Resource]
@ implicit val resourcelist : Decoder[ResourceList] = deriveDecoder[ResourceList]

@ val decoded = decode[ResourceList](parsed.right.get.toString) match {
    case Right(decodedJson) => decodedJson.asInstanceOf[ResourceList]
    case Left(_) => ???
}
@ decoded.ResourceTagMappingList.map( x => x.ResourceARN, x.Tags.filter ( y => y.Key == "owner" || y.Key == "project")))

```




Credits <https://gist.github.com/lettenj61>