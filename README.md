https://storage.googleapis.com/downloads.webmproject.org/releases/webp/index.html



![Travis build status](https://api.travis-ci.org/sejda-pdf/webp-imageio.svg?branch=master)

# Forked repository
This is a fork from [luciad/webp-imageio](https://bitbucket.org/luciad/webp-imageio/)

# Changes
- Native libs are bundled in the jar
- Published to Maven Central (`org.sejda.imageio`:`webp-imageio` artifact)
- Android support unknown (have not tested)

# Supported platforms
- windows (32, 64 bit)
- linux (64 bit)
- mac (64 bit)

--------------

# Description
[Java Image I/O](http://docs.oracle.com/javase/7/docs/api/javax/imageio/package-summary.html) reader and writer for the
[Google WebP](https://developers.google.com/speed/webp/) image format.

# License
webp-imageio is distributed under the [Apache Software License](https://www.apache.org/licenses/LICENSE-2.0) version 2.0.

# Usage
- Add Maven dependency `org.sejda.imageio`:`webp-imageio` to your application
- The WebP reader and writer can be used like any other Image I/O reader and writer.

## Decoding

WebP images can be decoded using default settings as follows.

```
BufferedImage image = ImageIO.read(new File("input.webp"));
```

To customize the WebP decoder settings you need to create instances of ImageReader and WebPReadParam.

```
// Obtain a WebP ImageReader instance
ImageReader reader = ImageIO.getImageReadersByMIMEType("image/webp").next();

// Configure decoding parameters
WebPReadParam readParam = new WebPReadParam();
readParam.setBypassFiltering(true);

// Configure the input on the ImageReader
reader.setInput(new FileImageInputStream(new File("input.webp")));

// Decode the image
BufferedImage image = reader.read(0, readParam);
```

## Encoding

Encoding is done in a similar way to decoding.

You can either use the Image I/O convenience methods to encode using default settings.

```
// Obtain an image to encode from somewhere
BufferedImage image = ImageIO.read(new File("input.png"));

// Encode it as webp using default settings
ImageIO.write(image, "webp", new File("output.webp"));
```

Or you can create an instance of ImageWriter and WebPWriteParam to use custom settings.

```
// Obtain an image to encode from somewhere
BufferedImage image = ImageIO.read(new File("input.png"));

// Obtain a WebP ImageWriter instance
ImageWriter writer = ImageIO.getImageWritersByMIMEType("image/webp").next();

// Configure encoding parameters
WebPWriteParam writeParam = new WebPWriteParam(writer.getLocale());
writeParam.setCompressionMode(ImageWriteParam.MODE_EXPLICIT);
writeParam.setCompressionType(p.getCompressionTypes()[WebPWriteParam.LOSSLESS_COMPRESSION]);

// Configure the output on the ImageWriter
writer.setOutput(new FileImageOutputStream(new File("output.webp")));

// Encode
writer.write(null, new IIOImage(image, null, null), writeParam);
```

# Compiling

## Compiling the native library for Java SE
- Install CMake 2.8 or newer. CMake can be downloaded from www.cmake.org or installed using
  your systems package manager.
- Install VS 2017 Community and make sure the C++/Cmake dev tools modules are installed
- Create a directory called `build` in the root of the project
- Open a terminal and navigate to the newly created 'build' directory
- Run `cmake ..` in the 'build' directory to generate the build scripts for your system.
On Windows 64 bit run `cmake -DCMAKE_GENERATOR_PLATFORM=x64 ..` to get a 64 bit dll.
- `cmake --build . --config Release` to compile the library
- The compiled library can be found under the directory `build/src/main/c`
- Copy the compiled library to `src/main/resources/native/$platform/$bits`

## Compiling the native library for Android
- Install the Android NDK.
- Run ndk-build in the `src/android` directory
- After completion `libwebp-imageio.so` can be found under `src/android/libs/<abi>`

## Compiling the Java library

### Using Maven
- Run `mvn clean test` in the root of the project
- The compiled Java library can be found under the `build` directory
- Run `mvn release:prepare release:perform -Prelease -Darguments="-Dgpg.passphrase=secret123 -DstagingDescription=sambox"` to upload to OSS Nexus, then [login](https://oss.sonatype.org/#stagingRepositories) and publish to Maven Central




webp图片格式在java下的支持

半瓶水monster
0.052
2018.01.21 12:54:43
字数 412
阅读 4,101
webp是Google公司旨在统一图片格式标准，结束gif, png, jpg 3分天下格局的一种图片格式。图片的大小要比其他格式的要更小，亲测png转化后可以节省80%。在图片比较多的场景下，统一用webp可以极大的节省流量和访问速度，收益可观。目前 js, php都有很好的库加以支持，但是在java中，imageio库目前还是不支持，需要我们自己去编译动态链接库和jar包。

PHP下支持
php最新的gd库已经支持webp的转化了,转化比较简单。但是存在一个小bug, 图片的大小为奇数时候，需要手动padding \0 到文件末尾。

$created = imagewebp(imagecreatefrompng($originImageUrl), targetFile);
if (1 == filesize(targetFile) % 2) {
      file_put_contents(targetFile, "\0", FILE_APPEND);
}
Java下支持
webp-imageio
开源库 下载压缩包到本地进行编译jar，工程用gradle来维护。

libwebp
google-developer下载压缩包到本地编译动态链接库-不同平台的动态链接库编译出来是不同的。mac下是.dylib, windowns下是.dll, unix/linux下是.so. 我在mac和linux下编译过，记录下linux下编译过程。

compiling

libwebp-0.6.1.tar.gz luciad-webp-imageio-8f9b44b41902.zip

tar -xzvf libwebp-0.6.1.tar.gz
unzip luciad-webp-imageio-8f9b44b41902.zip
mv libwebp-0.6.1 ./luciad-webp-imageio-8f9b44b41902/libwebp

cd luciad-webp-imageio-8f9b44b41902
cmake .
cmake --build .
cmake如果装的话，请自行下载安装官网 安装用法

./gradlew build -x test
java project
将libwebp-imageio.so放入工程的当前目录，因为默认的java.library.path含工程当前路径。同时引入打好的luciad-webp-imageio-8f9b44b41902-0.6.0-SNAPSHOT.jar引入工程中。如果是maven工程的话，可以把jar先推到远程的仓库里。
java transfer
image = ImageIO.read(new URL(imageUrl));
// Encode it as webp using default settings
boolean result = ImageIO.write(image, "webp", bos);
