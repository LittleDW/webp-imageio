# Forked repository
This is a fork from [luciad/webp-imageio](https://bitbucket.org/luciad/webp-imageio/)

--------------

# Description
[Java Image I/O](http://docs.oracle.com/javase/7/docs/api/javax/imageio/package-summary.html) reader and writer for the
[Google WebP](https://developers.google.com/speed/webp/) image format.

# License
webp-imageio is distributed under the [Apache Software License](https://www.apache.org/licenses/LICENSE-2.0) version 2.0.

# Usage
- Add webp-imageio.jar to the classpath of your application
- Ensure libwebp-imageio.so, libwebp-imageio.dylib or webp-imageio.dll is accessible on the Java native library path (java.library.path system property)
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
- Create a directory called `build` in the root of the project
- Open a terminal and navigate to the newly created 'build' directory
- Run `cmake ..` in the 'build' directory to generate the build scripts for your system.
- `cmake --build .` to compile the library
- The compiled library can be found under the directory `build/src/main/c`

## Compiling the native library for Android
- Install the Android NDK.
- Run ndk-build in the `src/android` directory
- After completion `libwebp-imageio.so` can be found under `src/android/libs/<abi>`

## Compiling the Java library

### Using Gradle
- Run `./gradlew build -x test` in the root of the project
- The compiled Java library can be found under the `build` directory
