# Thumbnailator — LLM_readme

LLM-facing API documentation. Generated from the API surface, project profile, and distilled docs.

## API Test: `AbsoluteSize`

### Signature
```java
public AbsoluteSize(Dimension size)
public AbsoluteSize(int width, int height)
```
_Source: source/src/main/java/net/coobird/thumbnailator/geometry/AbsoluteSize.java:64  (+1 more definition site/overload)_

_Source doc:_ Instantiates an object which indicates size of an object. @param width		Width of the enclosed object. @param height	Height of the enclosed object. @throws IllegalArgumentException		If the width and/or height is less than or equal to {@code 0}.

### Goal
Provides a concrete, fixed-dimension definition for image processing operations such as defining source regions for cropping or specifying target dimensions.

### Parameters
- `width` (`int`): The width of the region or object in pixels; must be greater than 0.
- `height` (`int`): The height of the region or object in pixels; must be greater than 0.

### Input
The caller must provide positive integer values for dimensions. When used within a `sourceRegion` operation, the input must be a `BufferedImage` that is compatible with the requested dimensions (i.e., the region must not exceed the bounds of the source image).

### Output
Returns an `AbsoluteSize` object representing the specified dimensions, which is used by Thumbnailator's geometry-aware methods to define fixed-size boundaries.

### Valid Call Patterns
```java
// Defining a source region for a crop operation
BufferedImage thumbnail = Thumbnails.of(img)
    .sourceRegion(new Coordinate(0, 0), new AbsoluteSize(50, 50))
    .size(50, 50)
    .asBufferedImage();
```

### LLM Instruction Prompt
- Use `AbsoluteSize` when a fixed pixel dimension is required for `sourceRegion` or similar geometry-based operations.
- Ensure both `width` and `height` are strictly greater than 0 to avoid `IllegalArgumentException`.
- When using the `Dimension` overload, ensure the `java.awt.Dimension` object is initialized with positive values.

### Prompt Snippet
```text
new AbsoluteSize(width, height)
```

### Common Failure Modes
- **IllegalArgumentException**: Occurs if `width` or `height` is passed as 0 or a negative integer.
- **OutOfBoundsException**: Occurs if the `AbsoluteSize` defined for a `sourceRegion` exceeds the actual pixel dimensions of the source `BufferedImage`.

### Fix Code Hint
```java
// Ensure dimensions are positive and within image bounds
int w = Math.max(1, requestedWidth);
int h = Math.max(1, requestedHeight);
new AbsoluteSize(w, h);
```

## API Test: `BicubicResizer`

### Signature
```java
public BicubicResizer()
public BicubicResizer(Map<RenderingHints.Key, Object> hints)
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/BicubicResizer.java:51  (+1 more definition site/overload)_

_Source doc:_ Instantiates a {@link BicubicResizer} with the specified rendering hints. @param hints		Additional rendering hints to apply.

### Goal
Provides a high-quality image resizing implementation using the bicubic interpolation algorithm within the Thumbnailator processing pipeline.

### Parameters
- `hints` (`Map<RenderingHints.Key, Object>`): A map of Java 2D `RenderingHints` used to configure the underlying `Graphics2D` object during the resizing process.

### Input
- **Data:** Requires a `BufferedImage` as the source for the resizing operation.
- **Preconditions:** The source image must be a valid format supported by the host JVM's `ImageIO`. The resizer is typically invoked internally by the `Thumbnails` builder when configured for specific scaling tasks.

### Output
Returns `void` (constructor); the instance is used by the library to perform the transformation of the source `BufferedImage` into the target dimensions.

### Valid Call Patterns
```java
// Inferred from signature: Instantiating with custom rendering hints
Map<RenderingHints.Key, Object> hints = new HashMap<RenderingHints.Key, Object>();
hints.put(RenderingHints.KEY_INTERPOLATION, RenderingHints.VALUE_INTERPOLATION_BICUBIC);
BicubicResizer resizer = new BicubicResizer(hints);
```

### LLM Instruction Prompt
- Use `BicubicResizer` when high-quality scaling is required and the default resizer settings are insufficient.
- Always pass a `Map<RenderingHints.Key, Object>` if specific Java 2D rendering behavior is required; otherwise, use the no-argument constructor.
- Ensure the environment is headless, as this class performs in-memory image processing without UI dependencies.

### Prompt Snippet
```text
BicubicResizer resizer = new BicubicResizer(hintsMap);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `hints` map is passed as `null` to the constructor.
- **UnsupportedFormatException:** If the input `BufferedImage` type is not compatible with the `Graphics2D` operations performed by the resizer.
- **Performance Overhead:** Bicubic interpolation is computationally more expensive than bilinear methods; avoid using it in high-throughput batch processing if performance is a bottleneck.

### Fix Code Hint
```java
// Ensure hints map is initialized before passing to the constructor
Map<RenderingHints.Key, Object> hints = new HashMap<RenderingHints.Key, Object>();
hints.put(RenderingHints.KEY_RENDERING, RenderingHints.VALUE_RENDER_QUALITY);
BicubicResizer resizer = new BicubicResizer(hints);
```

## API Test: `BilinearResizer`

### Signature
```java
public BilinearResizer(Map<RenderingHints.Key, Object> hints)
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/BilinearResizer.java_

_Source doc:_ Instantiates a {@link BilinearResizer} with the specified rendering hints. @param hints		Additional rendering hints to apply.

### Goal
Provides a resizer implementation that uses bilinear interpolation to scale images, allowing for custom Java 2D rendering hints to be applied during the transformation process.

### Parameters
- `hints` (`Map<RenderingHints.Key, Object>`): A map containing `RenderingHints` keys and their corresponding values to configure the underlying Java 2D graphics engine during the resizing operation.

### Input
- **Data:** Requires a `BufferedImage` source (typically handled internally by the Thumbnailator pipeline).
- **Preconditions:** The input must be a valid image format supported by the host JVM's `ImageIO`. The caller must ensure the `Map` provided for `hints` contains valid `RenderingHints.Key` constants.

### Output
Returns `BilinearResizer` — An instance of the resizer configured with the provided rendering hints, used by the Thumbnailator pipeline to perform the scaling operation.

### Valid Call Patterns
```java
// Example inferred from signature
Map<RenderingHints.Key, Object> hints = new HashMap<RenderingHints.Key, Object>();
hints.put(RenderingHints.KEY_INTERPOLATION, RenderingHints.VALUE_INTERPOLATION_BILINEAR);
BilinearResizer resizer = new BilinearResizer(hints);
```

### LLM Instruction Prompt
- When using `BilinearResizer`, ensure it is passed to the `ThumbnailParameter` or used within a custom resizing workflow. Always provide a non-null `Map` for the `hints` parameter to avoid `NullPointerException` if the implementation expects an initialized map.

### Prompt Snippet
```text
new BilinearResizer(Map<RenderingHints.Key, Object> hints)
```

### Common Failure Modes
- **NullPointerException:** Passing `null` instead of an initialized `Map` to the constructor.
- **Unsupported Hints:** Providing `RenderingHints` keys that are not supported by the underlying `Graphics2D` implementation, which may lead to ignored settings or unexpected rendering artifacts.
- **Environment Constraints:** Attempting to use this in an environment where `ImageIO` cannot decode the source image format.

### Fix Code Hint
```java
// Ensure the map is initialized before passing to the constructor
Map<RenderingHints.Key, Object> hints = new HashMap<>();
hints.put(RenderingHints.KEY_RENDERING, RenderingHints.VALUE_RENDER_QUALITY);
BilinearResizer resizer = new BilinearResizer(hints);
```

## API Test: `BufferedImageBuilder`

### Signature
```java
public BufferedImageBuilder(int width, int height, int imageType)
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/BufferedImageBuilder.java_

_Source doc:_ Instantiates a {@code BufferedImageBuilder} with the specified size and image type. @param width			The width of the {@link BufferedImage} to build. @param height		The height of the {@link BufferedImage} to build. @param imageType		The image type of the {@link BufferedImage} to build.

### Goal
Provides a utility for instantiating `BufferedImage` objects with specific dimensions and image types to serve as canvases for thumbnail processing or in-memory image manipulation.

### Parameters
- `width` (`int`): The horizontal dimension of the image in pixels.
- `height` (`int`): The vertical dimension of the image in pixels.
- `imageType` (`int`): The integer constant representing the `BufferedImage` type (e.g., `BufferedImage.TYPE_INT_ARGB` or `BufferedImage.TYPE_INT_RGB`).

### Input
The caller must provide valid positive integers for `width` and `height`. The `imageType` must correspond to a valid constant defined in the `java.awt.image.BufferedImage` class.

### Output
Returns a `BufferedImageBuilder` instance, which provides a `.build()` method to return the configured `BufferedImage`.

### Valid Call Patterns
```java
// Creating a new BufferedImage for processing
BufferedImage img = new BufferedImageBuilder(200, 200, BufferedImage.TYPE_INT_ARGB).build();
```

### LLM Instruction Prompt
- Use `BufferedImageBuilder` when you need to create a blank `BufferedImage` in-memory before passing it to `Thumbnailator.createThumbnail` or other pipeline operations.
- Always chain the `.build()` method after the constructor to retrieve the actual `BufferedImage` object.
- Ensure the `imageType` matches the requirements of your target output (e.g., use `TYPE_INT_ARGB` if transparency is required).

### Prompt Snippet
```text
new BufferedImageBuilder(width, height, imageType).build()
```

### Common Failure Modes
- **Negative Dimensions:** Providing negative values for `width` or `height` will result in an `IllegalArgumentException` when calling `.build()`.
- **Invalid Image Type:** Passing an unsupported or invalid integer for `imageType` may result in an `IllegalArgumentException` or an improperly initialized image buffer.
- **Memory Exhaustion:** Attempting to build extremely large `BufferedImage` objects may lead to `OutOfMemoryError` in constrained headless environments.

### Fix Code Hint
```java
// Ensure dimensions are positive and type is valid
int width = 200;
int height = 200;
int type = BufferedImage.TYPE_INT_ARGB;
BufferedImage img = new BufferedImageBuilder(width, height, type).build();
```

## API Test: `BufferedImageSource`

### Signature
```java
public BufferedImageSource(BufferedImage img)
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/BufferedImageSource.java:54_

_Source doc:_ Instantiates a {@link BufferedImageSource} object with the {@link BufferedImage} that should be used as the source image for making thumbnails. @param img		The source image. @throws NullPointerException		If the image is null.

### Goal
Provides an in-memory `BufferedImage` as a source input for a `ThumbnailTask`, enabling headless image processing pipelines.

### Parameters
- `img` (`BufferedImage`): The source image to be processed; must be a valid, non-null `BufferedImage` instance.

### Input
The caller must provide a `BufferedImage` object. The image must be fully initialized and non-null. No specific file format is required as the input is already decoded into memory.

### Output
Returns `null` (constructor). The object represents a source provider that encapsulates the `BufferedImage` for use within a `SourceSinkThumbnailTask`.

### Valid Call Patterns
```java
BufferedImageSource source = new BufferedImageSource(myBufferedImage);
```

### LLM Instruction Prompt
- Use `BufferedImageSource` when the thumbnail pipeline requires an in-memory `BufferedImage` as the input source rather than a file or stream.
- Always ensure the `BufferedImage` passed to the constructor is not null to avoid a `NullPointerException`.
- Wrap the `BufferedImageSource` in a `SourceSinkThumbnailTask` when performing manual task execution.

### Prompt Snippet
```text
BufferedImageSource source = new BufferedImageSource(sourceImage);
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `img` argument passed to the constructor is `null`.
- **Invalid Image State**: Passing a `BufferedImage` that has been disposed or is otherwise invalid may lead to errors during the subsequent `ThumbnailTask` execution.

### Fix Code Hint
```java
if (sourceImage != null) {
    BufferedImageSource source = new BufferedImageSource(sourceImage);
} else {
    // Handle null source image appropriately
}
```

## API Test: `Canvas`

### Signature
```java
public Canvas(int width, int height, Position position)
public Canvas(int width, int height, Position position, boolean crop)
public Canvas(int width, int height, Position position, Color fillColor)
public Canvas(int width, int height, Position position, boolean crop, Color fillColor)
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Canvas.java:144  (+3 more definition site/overload)_

_Source doc:_ Instantiates a {@code Canvas} filter. @param width			The width of the filtered image. @param height		The height of the filtered image. @param position		The position to place the enclosed image. @param crop			Whether or not to crop the enclosed image if the enclosed image has dimensions which are larger than the specified {@code width} and {@code height}. @param fillColor		The color to fill portions of the image which is not covered by the enclosed image. Portions of the image which is transparent will be filled with the specified color as well.

### Goal
The `Canvas` filter allows for resizing the image container to specific dimensions while positioning the original image within that space and filling any remaining background area with a specified color.

### Parameters
- `width` (`int`): The target width of the resulting image canvas.
- `height` (`int`): The target height of the resulting image canvas.
- `position` (`Position`): The alignment strategy (e.g., `Positions.CENTER`) used to place the original image within the new canvas dimensions.
- `crop` (`boolean`): If `true`, the original image is cropped to fit the canvas dimensions if it exceeds them; if `false`, the image is placed within the canvas without cropping.
- `fillColor` (`Color`): The `java.awt.Color` used to fill areas of the canvas not covered by the original image, including transparent regions.

### Input
The filter operates on a `BufferedImage`. The input image must be a valid `BufferedImage` object. Preconditions include ensuring the image is not null and is in a format supported by the host JVM's `ImageIO`.

### Output
Returns `void` (the filter modifies the `BufferedImage` in-place or is applied via the `ImageFilter` interface). The resulting object is a `BufferedImage` with the new dimensions and background fill.

### Valid Call Patterns
```java
// Using the Position constructor
ImageFilter filter = new Canvas(100, 100, Positions.CENTER);

// Using the Position and crop constructor
ImageFilter filter = new Canvas(100, 100, Positions.CENTER, true);
```

### LLM Instruction Prompt
- When using `Canvas`, always provide a `Position` object from the `net.coobird.thumbnailator.geometry` package.
- If `fillColor` is not specified, the default behavior is to leave uncovered areas as transparent or default background.
- Ensure the `width` and `height` are positive integers.

### Prompt Snippet
```text
new Canvas(width, height, Positions.CENTER, crop, Color.WHITE)
```

### Common Failure Modes
- Passing `null` for the `Position` argument will result in a `NullPointerException`.
- Providing negative values for `width` or `height` may lead to `IllegalArgumentException` or invalid image dimensions.
- Attempting to apply the filter to a null `BufferedImage` will cause a `NullPointerException`.

### Fix Code Hint
```java
// Ensure valid dimensions and non-null position
int w = 200;
int h = 200;
Position pos = Positions.CENTER;
ImageFilter filter = new Canvas(w, h, pos, true, Color.BLACK);
filter.apply(myBufferedImage);
```

## API Test: `Caption`

### Signature
```java
public Caption(String caption, Font font, Color c, float alpha, Position position, int insets)
public Caption(String caption, Font font, Color c, Position position, int insets)
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Caption.java:92  (+1 more definition site/overload)_

_Source doc:_ Instantiates a filter which adds a text caption to an image. @param caption	The text of the caption. @param font		The font of the caption. @param c			The color of the caption. @param alpha		The opacity level of caption. <p> The value should be between {@code 0.0f} and {@code 1.0f}, where {@code 0.0f} is completely transparent, and {@code 1.0f} is completely opaque. @param position	The position of the caption. @param insets	The inset size around the caption. Cannot be negative.

### Goal
Instantiates an `ImageFilter` that overlays a specified text string onto an image at a defined position with configurable font, color, opacity, and padding.

### Parameters
- `caption` (`String`): The text content to be rendered onto the image.
- `font` (`Font`): The `java.awt.Font` object defining the typeface, style, and size of the caption text.
- `c` (`Color`): The `java.awt.Color` used to render the caption text.
- `alpha` (`float`): The opacity level of the caption, ranging from `0.0f` (fully transparent) to `1.0f` (fully opaque).
- `position` (`Position`): A `net.coobird.thumbnailator.geometry.Position` implementation defining where the caption is placed relative to the image bounds.
- `insets` (`int`): The size of the margin (in pixels) around the caption; must be a non-negative integer.

### Input
The filter operates on `BufferedImage` objects. The input image must be a valid `BufferedImage` supported by the Java 2D API. No specific file format is required as the filter processes in-memory pixel data.

### Output
Returns an instance of `Caption`, which implements the `ImageFilter` interface. This object is intended to be passed to the `addFilter` method of a `ThumbnailParameter` or used within a `Thumbnails` pipeline.

### Valid Call Patterns
```java
// Using the 6-argument constructor
ImageFilter filter = new Caption(
    "My Caption", 
    new Font("Monospaced", Font.PLAIN, 12), 
    Color.WHITE, 
    0.5f, 
    Positions.BOTTOM_RIGHT, 
    10
);

// Using the 5-argument constructor (alpha defaults to 1.0f)
ImageFilter filter = new Caption(
    "My Caption", 
    new Font("Monospaced", Font.PLAIN, 12), 
    Color.WHITE, 
    Positions.BOTTOM_RIGHT, 
    0
);
```

### LLM Instruction Prompt
- When generating code using `Caption`, ensure the `insets` parameter is always a non-negative integer.
- If the `alpha` parameter is used, ensure the value is strictly between `0.0f` and `1.0f`.
- Always import `net.coobird.thumbnailator.filters.Caption` and `net.coobird.thumbnailator.geometry.Positions`.
- The `Caption` object must be applied to a `Thumbnails` pipeline via the `.addFilter()` method.

### Prompt Snippet
```text
// Example of adding a caption to a thumbnail pipeline
Thumbnails.of(sourceImage)
    .size(200, 200)
    .addFilter(new Caption("Draft", new Font("Arial", Font.BOLD, 20), Color.RED, 0.8f, Positions.CENTER, 5))
    .toFile("output.jpg");
```

### Common Failure Modes
- **Negative Insets:** Providing a negative integer for the `insets` parameter will result in an `IllegalArgumentException`.
- **Invalid Alpha:** Providing an `alpha` value outside the `0.0f` to `1.0f` range may lead to unexpected rendering or runtime exceptions.
- **Font/Color Nulls:** Passing `null` for `font` or `c` (Color) will result in a `NullPointerException` during filter application.

### Fix Code Hint
```java
// Ensure insets are non-negative
int padding = Math.max(0, userProvidedPadding);
ImageFilter captionFilter = new Caption("Text", font, color, 1.0f, Positions.CENTER, padding);
```

## API Test: `Colorize`

### Signature
```java
public Colorize(Color c)
public Colorize(Color c, float alpha)
public Colorize(Color c, int alpha)
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Colorize.java:69  (+2 more definition site/overload)_

_Source doc:_ Instantiates this filter with the color to use to tint the target image with and the transparency level provided as a {@code float} ranging from {@code 0.0f} to {@code 1.0f}, where {@code 0.0f} indicates completely transparent, and {@code 1.0f} indicates completely opaque. @param c				Color to tint with. @param alpha			The opacity of the tint.

### Goal
Applies a color tint to a `BufferedImage` at a specified opacity level to create stylized thumbnail effects.

### Parameters
- `c` (`Color`): The `java.awt.Color` to be used as the tinting agent.
- `alpha` (`float`): The transparency level of the tint, ranging from `0.0f` (completely transparent) to `1.0f` (completely opaque).

### Input
- **Data:** A `BufferedImage` object.
- **Preconditions:** The image must be a valid `BufferedImage` supported by the host JVM's `ImageIO`. The filter is typically applied within a `Thumbnailator` pipeline or directly via the `ImageFilter` interface.

### Output
Returns `void` — The filter modifies the `BufferedImage` in-place when the `apply(BufferedImage)` method is invoked.

### Valid Call Patterns
```java
// Applying a blue tint with 50% opacity
ImageFilter filter = new Colorize(Color.blue, 0.5f);
filter.apply(bufferedImage);
```

### LLM Instruction Prompt
- When using `Colorize`, ensure the `alpha` value is a `float` between `0.0f` and `1.0f`.
- If only the `Color` is provided, the filter defaults to a specific opacity (refer to implementation defaults).
- Always ensure the target `BufferedImage` is mutable before applying the filter.

### Prompt Snippet
```text
new Colorize(Color.RED, 0.75f)
```

### Common Failure Modes
- **NullPointerException:** Passing a `null` `Color` object to the constructor.
- **Alpha Out of Bounds:** Providing an `alpha` value outside the `0.0f` to `1.0f` range may result in unexpected rendering behavior or `IllegalArgumentException` depending on the internal implementation.
- **Immutable Images:** Attempting to apply the filter to a `BufferedImage` that is read-only or backed by an immutable data buffer.

### Fix Code Hint
```java
// Ensure the image is a copy if the original must remain unaltered
BufferedImage mutableImage = new BufferedImage(width, height, BufferedImage.TYPE_INT_ARGB);
// ... draw original into mutableImage ...
new Colorize(Color.BLUE, 0.5f).apply(mutableImage);
```

## API Test: `ConsecutivelyNumberedFilenames`

### Signature
```java
public ConsecutivelyNumberedFilenames(File dir, String format, int start)
```
_Source: source/src/main/java/net/coobird/thumbnailator/name/ConsecutivelyNumberedFilenames.java_

_Source doc:_ <p> Instantiates an {@code ConsecutivelyNumberedFilenames} object which returns {@link File}s with file names which are based on a format string, located in the directory specified. The numbering will be consecutive from the specified value. </p> <p> The format string should contain the string {@code %d} which will be replaced with a consecutively counted number. Additional formatting can be applied. For more details, please refer to the section on <em>Numeric</em> formatting in the Java API specification for the {@link Formatter} class. </p> <p><strong>File name sequence</strong></p> <p> For a case where the parent directory is {@code /foo/bar/}, and the specified value is {@code 5}, with format string {@code image-%d}: </p> <ol> <li><code>/foo/bar/image-5</code></li> <li><code>/foo/bar/image-6</code></li> <li><code>/foo/bar/image-7</code></li> <li><code>/foo/bar/image-8</code></li> </ol> <p> and so on. </p> @param dir			The directory in which the files are to be located. @param format		The format string to use. @param start			The value from which to start counting. @throws IOException	If the specified directory path is not a directory, or if does not exist.

### Goal
Provides a naming strategy for batch thumbnail operations that generates unique, sequentially numbered filenames within a specified directory.

### Parameters
- `dir` (`File`): The target directory where the generated thumbnail files will be saved.
- `format` (`String`): A format string containing `%d` (e.g., `"image-%d.png"`) that defines the naming pattern for the output files.
- `start` (`int`): The initial integer value to use for the first file in the sequence.

### Input
- **Preconditions:** The `dir` argument must point to an existing, valid directory on the filesystem.
- **Format:** The `format` string must be compatible with Java's `java.util.Formatter` syntax, specifically requiring a `%d` placeholder for the sequence number.

### Output
Returns an instance of `ConsecutivelyNumberedFilenames` used by `toFiles` or `asFiles` methods to resolve output file paths during batch processing.

### Valid Call Patterns
```java
// Using the constructor to define output naming for a batch operation
Thumbnails.of(f1)
    .size(50, 50)
    .toFiles(new ConsecutivelyNumberedFilenames(temporaryFolder.getRoot(), "temp-%d.png"));
```

### LLM Instruction Prompt
- When generating code for batch thumbnail output, use `ConsecutivelyNumberedFilenames` as the argument for `toFiles()` or `asFiles()`.
- Ensure the `dir` parameter is a `File` object representing an existing directory.
- Always include the `%d` placeholder in the `format` string to ensure the sequence number is correctly injected.

### Prompt Snippet
```text
new ConsecutivelyNumberedFilenames(directoryFile, "output-%d.jpg", 0)
```

### Common Failure Modes
- **IOException:** Thrown if the provided `dir` does not exist or is not a directory.
- **Missing Placeholder:** If the `format` string does not contain `%d`, the `Formatter` will throw an `IllegalFormatException` during the file naming process.
- **Permission Denied:** If the application lacks write permissions for the specified `dir`.

### Fix Code Hint
```java
// Ensure directory exists before passing to the constructor
File outputDir = new File("path/to/dir");
if (!outputDir.exists()) {
    outputDir.mkdirs();
}
// Use the constructor with the required format string
new ConsecutivelyNumberedFilenames(outputDir, "thumb-%d.jpg", 1);
```

## API Test: `Coordinate`

### Signature
```java
public Coordinate(int x, int y)
```
_Source: source/src/main/java/net/coobird/thumbnailator/geometry/Coordinate.java:56_

_Source doc:_ Instantiates an object which calculates the position of an image, using the given coordinates. @param x			The horizontal component of the top-left corner of the image to be enclosed. @param y			The vertical component of the top-left corner of the image to be enclosed.

### Goal
Defines an absolute point in an image to serve as the top-left anchor for cropping or region selection operations.

### Parameters
- `x` (`int`): The horizontal pixel coordinate (0-indexed) representing the left edge of the target region.
- `y` (`int`): The vertical pixel coordinate (0-indexed) representing the top edge of the target region.

### Input
The caller must provide integer values representing valid pixel coordinates within the bounds of the source `BufferedImage`. Precondition: The coordinates must be non-negative and within the dimensions of the image being processed to avoid unexpected behavior during region extraction.

### Output
Returns an instance of `Coordinate` which encapsulates the specified position for use in `Region` definitions or `sourceRegion` pipeline configurations.

### Valid Call Patterns
```java
// Used within a sourceRegion definition
Thumbnails.of(img)
    .sourceRegion(new Region(new Coordinate(0, 0), new AbsoluteSize(50, 50)))
    .size(50, 50)
    .asBufferedImage();

// Used directly in the sourceRegion builder method
Thumbnails.of(img)
    .sourceRegion(new Coordinate(0, 0), new AbsoluteSize(50, 50))
    .size(50, 50)
    .asBufferedImage();
```

### LLM Instruction Prompt
- When using `Coordinate`, always pair it with an `AbsoluteSize` object inside a `Region` or the `sourceRegion` method to define a complete rectangular area.
- Ensure the `x` and `y` values are within the bounds of the source image dimensions to prevent invalid cropping.

### Prompt Snippet
```text
new Coordinate(x, y)
```

### Common Failure Modes
- Providing negative integers for `x` or `y`, which may result in an `IllegalArgumentException` or an empty/invalid region depending on the downstream processing logic.
- Specifying coordinates that exceed the source image width or height, leading to an `ImagingOpException` or `RasterFormatException` when the library attempts to extract the sub-image.

### Fix Code Hint
```java
// Ensure coordinates are within image bounds
int x = Math.max(0, Math.min(requestedX, image.getWidth() - 1));
int y = Math.max(0, Math.min(requestedY, image.getHeight() - 1));
new Coordinate(x, y);
```

## API Test: `FileImageSink`

### Signature
```java
public FileImageSink(File destinationFile)
public FileImageSink(File destinationFile, boolean allowOverwrite)
public FileImageSink(String destinationFilePath)
public FileImageSink(String destinationFilePath, boolean allowOverwrite)
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/FileImageSink.java:127  (+3 more definition site/overload)_

_Source doc:_ Instantiates a {@link FileImageSink} with the file to which the thumbnail should be written to. <p> The output format to use will be determined from the file extension. If another format should be used, then the {@link #setOutputFormatName(String)} should be called with the desired output format name. @param destinationFile		The destination file. @param allowOverwrite		Whether or not the {@code FileImageSink} should overwrite the destination file if it already exists. @throws NullPointerException	If the specified file is {@code null}.

### Goal
`FileImageSink` acts as a destination handler that writes processed thumbnail images to a specified file system location, automatically inferring the output format from the file extension.

### Parameters
- `destinationFile` (`File`): The target file object where the thumbnail will be saved.
- `allowOverwrite` (`boolean`): A flag indicating whether the sink is permitted to overwrite an existing file at the destination path.

### Input
The caller must provide a valid `File` object or a `String` representing a file path. The output format is determined by the file extension (e.g., .jpg, .png). If a specific format is required that does not match the extension, `setOutputFormatName(String)` must be called after instantiation.

### Output
Returns `null` (constructor). The object acts as a sink for the Thumbnailator pipeline to write image data.

### Valid Call Patterns
```java
// Using File object
File f = new File("test.png");
FileImageSink sink = new FileImageSink(f);

// Using String path
String path = "test.png";
FileImageSink sink = new FileImageSink(path);
```

### LLM Instruction Prompt
- When using `FileImageSink`, always ensure the destination path is writable.
- If the file extension does not match the desired output format, explicitly invoke `setOutputFormatName` on the sink instance before processing.
- Use the `allowOverwrite` parameter to prevent accidental data loss if the target file already exists.

### Prompt Snippet
```text
// Example of initializing a FileImageSink
FileImageSink sink = new FileImageSink(new File("output.jpg"), true);
```

### Common Failure Modes
- `NullPointerException`: Occurs if the `destinationFile` argument is `null`.
- `IOException`: May occur if the application lacks write permissions to the target directory or if the disk is full.
- Format Mismatch: If the file extension is missing or unsupported by the host JVM's `ImageIO`, the write operation may fail or produce an invalid file.

### Fix Code Hint
```java
// Ensure the file is not null and handle potential format issues
File target = new File("path/to/image.jpg");
FileImageSink sink = new FileImageSink(target, true);
// If forcing a specific format regardless of extension:
sink.setOutputFormatName("jpg");
```

## API Test: `FileImageSource`

### Signature
```java
public FileImageSource(File sourceFile)
public FileImageSource(String sourceFilePath)
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/FileImageSource.java:86  (+1 more definition site/overload)_

_Source doc:_ Instantiates a {@link FileImageSource} with the specified file as the source image. @param sourceFile		The source image file. @throws NullPointerException	If the image is null.

### Goal
Provides a file-based input source for the Thumbnailator pipeline, allowing the library to read and process image data directly from the local filesystem.

### Parameters
- `sourceFile` (`File`): The `File` object representing the image to be processed.

### Input
- **Data:** A valid image file accessible by the JVM.
- **Formats:** Supported formats are determined by the host JVM's `ImageIO` capabilities.
- **Preconditions:** The file must exist and be readable. The input must be a valid image format recognized by the underlying Java Image I/O API.

### Output
Returns `null` (Constructor). The instance acts as a provider for the `ThumbnailTask` to read the image data during the execution phase.

### Valid Call Patterns
```java
File sourceFile = new File("path/to/image.jpg");
FileImageSource source = new FileImageSource(sourceFile);
```

### LLM Instruction Prompt
- Use `FileImageSource` when the thumbnail pipeline requires a local file as the input source.
- Always ensure the `File` object is initialized with a valid path before passing it to the constructor.
- When using the `String` overload, ensure the path is correctly formatted for the host operating system.

### Prompt Snippet
```text
// Initialize the source for a thumbnail task
FileImageSource source = new FileImageSource(new File("input.jpg"));
```

### Common Failure Modes
- **`NullPointerException`**: Occurs if the `sourceFile` argument is `null`.
- **`FileNotFoundException`**: Occurs during the `read()` operation if the file does not exist at the specified path.
- **`IOException`**: Occurs if the file exists but is not a valid image format supported by the JVM's `ImageIO` or if there are insufficient read permissions.

### Fix Code Hint
```java
// Ensure file existence before instantiation
File file = new File("path/to/image.jpg");
if (file.exists()) {
    FileImageSource source = new FileImageSource(file);
    // Proceed with task...
}
```

## API Test: `FileThumbnailTask`

### Signature
```java
public FileThumbnailTask(ThumbnailParameter param, File sourceFile, File destinationFile)
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/FileThumbnailTask.java:61_

_Source doc:_ Creates a {@link ThumbnailTask} in which image data is read from the specified {@link File} and is output to a specified {@link File}, using the parameters provided in the specified {@link ThumbnailParameter}. @param param				The parameters to use to create the thumbnail. @param sourceFile		The {@link File} from which image data is read. @param destinationFile	The {@link File} to which thumbnail is written. @throws NullPointerException		If the parameter is {@code null}.

### Goal
`FileThumbnailTask` encapsulates the logic for reading an image from a source file and writing a processed thumbnail to a destination file based on defined `ThumbnailParameter` configurations.

### Parameters
- `param` (`ThumbnailParameter`): The configuration object defining scaling, quality, image type, and filters to apply during the transformation.
- `sourceFile` (`File`): The filesystem path to the source image to be processed.
- `destinationFile` (`File`): The filesystem path where the resulting thumbnail image will be saved.

### Input
- **Data:** A `File` object pointing to a valid image file.
- **Formats:** Supported formats are determined by the host JVM's `ImageIO` capabilities.
- **Preconditions:** The `sourceFile` must exist and be readable. The `param` object must not be `null`.

### Output
Returns `void` (the constructor initializes the task; the actual processing is triggered by calling the `read()` method on the task instance).

### Valid Call Patterns
```java
ThumbnailParameter param = new ThumbnailParameter(
        new Dimension(50, 50), null, true, "jpg", 
        ThumbnailParameter.DEFAULT_FORMAT_TYPE, 
        ThumbnailParameter.DEFAULT_QUALITY, 
        BufferedImage.TYPE_INT_ARGB, null, 
        Resizers.PROGRESSIVE, true, true
);

FileThumbnailTask task = new FileThumbnailTask(param, inputFile, outputFile);
task.read();
```

### LLM Instruction Prompt
- When using `FileThumbnailTask`, always ensure the `ThumbnailParameter` is instantiated with valid dimensions and settings before passing it to the constructor.
- Remember that the constructor only prepares the task; you must invoke the `read()` method on the resulting object to execute the file I/O and image processing.
- Ensure the `sourceFile` is a valid image format supported by the JVM.

### Prompt Snippet
```text
FileThumbnailTask task = new FileThumbnailTask(param, sourceFile, destinationFile);
task.read();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `param` argument is `null`.
- **IOException:** Occurs if the `sourceFile` is inaccessible, does not exist, or is not a valid image format supported by the JVM's `ImageIO`.
- **SecurityException:** Occurs if the application lacks read/write permissions for the specified file paths in the headless environment.

### Fix Code Hint
```java
// Ensure parameters are initialized and files exist before task creation
if (param != null && sourceFile.exists()) {
    FileThumbnailTask task = new FileThumbnailTask(param, sourceFile, destinationFile);
    task.read();
}
```

## API Test: `FixedResizerFactory`

### Signature
```java
public FixedResizerFactory(Resizer resizer)
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/FixedResizerFactory.java:49_

_Source doc:_ Creates an instance of the {@link FixedResizerFactory} which returns the speicifed {@link Resizer} under all circumstances. @param resizer		The {@link Resizer} instance that is to be returned under all circumstances.

### Goal
Provides a deterministic `ResizerFactory` that forces the use of a specific `Resizer` implementation regardless of the source or destination image dimensions.

### Parameters
- `resizer` (`Resizer`): The specific scaling algorithm (e.g., `Resizers.BICUBIC`, `Resizers.BILINEAR`) to be used for all thumbnail operations performed by this factory.

### Input
- The caller must provide a valid `Resizer` instance.
- Preconditions: The provided `Resizer` must be compatible with the `ImageIO` operations performed during the thumbnail pipeline.

### Output
Returns an instance of `FixedResizerFactory`. When queried via `getResizer()`, it returns the `Resizer` instance provided during construction.

### Valid Call Patterns
```java
// Using a specific Resizer implementation
Resizer resizer = Resizers.BICUBIC;
ResizerFactory factory = new FixedResizerFactory(resizer);
```

### LLM Instruction Prompt
- Use `FixedResizerFactory` when you need to override Thumbnailator's default automatic `Resizer` selection logic to ensure consistent image quality or performance characteristics across all operations.
- Always pass a valid `Resizer` instance from the `net.coobird.thumbnailator.resizers` package.

### Prompt Snippet
```text
// Example of forcing a specific resizer
ResizerFactory factory = new FixedResizerFactory(Resizers.PROGRESSIVE_BILINEAR);
```

### Common Failure Modes
- Passing a `null` value to the constructor, which will result in a `NullPointerException` when the factory is later invoked to retrieve a `Resizer`.
- Assuming the factory will adapt to image dimensions; this factory is "fixed" and ignores dimension-based optimization logic.

### Fix Code Hint
```java
// Ensure the resizer is non-null before instantiation
if (myResizer != null) {
    ResizerFactory factory = new FixedResizerFactory(myResizer);
}
```

## API Test: `FixedSizeThumbnailMaker`

### Signature
```java
public FixedSizeThumbnailMaker()
public FixedSizeThumbnailMaker(int width, int height)
public FixedSizeThumbnailMaker(int width, int height, boolean aspectRatio)
public FixedSizeThumbnailMaker(int width, int height, boolean aspectRatio, boolean fit)
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/FixedSizeThumbnailMaker.java:158  (+3 more definition site/overload)_

_Source doc:_ Creates a {@link FixedSizeThumbnailMaker} which creates thumbnails with the specified size. Whether or not the aspect ratio of the original image should be preserved by the thumbnail, and whether to fit the thumbnail within the given dimensions is also specified at instantiation. @param width			The width of the thumbnail to produce. @param height		The height of the thumbnails to produce. @param aspectRatio	Whether or not to maintain the aspect ratio in the thumbnail the same as the original image. <p> If {@code true} is specified, then the thumbnail image will have the same aspect ratio as the original image. @param fit			Whether or not to fit the thumbnail within the specified dimensions. <p> If {@code true} is specified, then the thumbnail will be sized to fit within the specified {@code width} and {@code height}.

### Goal
Produces a thumbnail image constrained to specific dimensions by applying resizing logic that optionally respects aspect ratio and fitting requirements.

### Parameters
- `width` (`int`): The target width of the thumbnail in pixels.
- `height` (`int`): The target height of the thumbnail in pixels.
- `aspectRatio` (`boolean`): If `true`, the thumbnail maintains the original image's aspect ratio.
- `fit` (`boolean`): If `true`, the thumbnail is scaled to fit within the specified width and height boundaries.

### Input
Requires a `BufferedImage` as input to the `make(BufferedImage)` method. The input image must be a valid format supported by the host JVM's `ImageIO`.

### Output
Returns a `BufferedImage` representing the processed thumbnail.

### Valid Call Patterns
```java
// Note: The following patterns are inferred from the constructor signatures.
// Instances must be initialized with dimensions before calling make().
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker(100, 100, true, true);
BufferedImage thumbnail = maker.make(sourceImage);
```

### LLM Instruction Prompt
- Always initialize `FixedSizeThumbnailMaker` with the required dimensions (`width`, `height`) and boolean flags (`aspectRatio`, `fit`) before invoking the `make()` method.
- Do not use the no-argument or two-argument constructors if the intention is to produce a valid thumbnail, as these result in uninitialized states that fail at runtime.

### Prompt Snippet
```text
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker(width, height, aspectRatio, fit);
BufferedImage result = maker.make(sourceImage);
```

### Common Failure Modes
- **Uninitialized State:** Calling `make()` on an instance created via the no-argument or two-argument constructor will result in an exception, as the internal dimensions are not properly configured.
- **Unsupported Format:** Providing an input `BufferedImage` that cannot be processed by the underlying `ImageIO` implementation.

### Fix Code Hint
```java
// Ensure all parameters are provided to the constructor
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker(200, 200, true, true);
BufferedImage thumbnail = maker.make(sourceImage);
```

## API Test: `IfdStructure`

### Signature
```java
public IfdStructure(int tag, int type, int count, int offsetValue)
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdStructure.java:49_

_Source doc:_ Instantiates a IFD with the given attributes. @param tag			The tag element. @param type			The type element. @param count			The count of values. @param offsetValue	The offset or value.

### Goal
Instantiates an Image File Directory (IFD) structure used for parsing or representing EXIF metadata tags within image files.

### Parameters
- `tag` (`int`): The specific EXIF tag identifier.
- `type` (`int`): The data type identifier for the tag value.
- `count` (`int`): The number of values associated with the tag.
- `offsetValue` (`int`): The actual value of the tag if it fits within 4 bytes, or the offset to the value location in the file.

### Input
The caller must provide four integer values representing the raw components of an IFD entry. This is typically used when manually processing or inspecting EXIF metadata structures extracted from image headers.

### Output
Returns an instance of `IfdStructure` representing the parsed or constructed IFD entry.

### Valid Call Patterns
```java
// Example inferred from signature (not verified by test suite)
IfdStructure ifd = new IfdStructure(0x0112, 3, 1, 1);
```

### LLM Instruction Prompt
- Use `IfdStructure` to represent individual EXIF metadata entries when performing low-level image header analysis.
- Ensure all integer inputs correspond to valid EXIF specification values for tags, types, and counts.
- Note that this class is a utility for metadata handling and does not perform image transformations directly.

### Prompt Snippet
```text
IfdStructure ifd = new IfdStructure(tag, type, count, offsetValue);
```

### Common Failure Modes
- Providing invalid tag or type identifiers that do not conform to the EXIF specification, leading to incorrect metadata interpretation.
- Passing an `offsetValue` that is misinterpreted as a direct value when it should have been treated as a pointer to a data buffer.

### Fix Code Hint
```java
// Ensure the tag and type match the expected EXIF standard for the specific image format
IfdStructure ifd = new IfdStructure(0x0112, 3, 1, 1); // Example: Orientation tag
```

## API Test: `InputStreamImageSource`

### Signature
```java
public InputStreamImageSource(InputStream is)
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/InputStreamImageSource.java:78_

_Source doc:_ Instantiates an {@link InputStreamImageSource} with the {@link InputStream} which will be used to read the source image. @param is		The {@link InputStream} which is to be used to obtain the source image. @throws NullPointerException		If the {@link InputStream} is {@code null}.

### Goal
Provides a mechanism to read source image data from an `InputStream` for use within a Thumbnailator processing task.

### Parameters
- `is` (`InputStream`): The `InputStream` containing the raw image data to be processed.

### Input
- **Data:** A valid `InputStream` containing image data.
- **Formats:** The stream must contain an image format supported by the host JVM's `ImageIO` capabilities.
- **Preconditions:** The `InputStream` must not be `null`. The caller is responsible for ensuring the stream is open and contains valid image data before passing it to the constructor.

### Output
Returns `void` (Constructor). The object represents an image source abstraction that can be passed to a `SourceSinkThumbnailTask`.

### Valid Call Patterns
```java
InputStream is = TestUtils.getResourceStream("Thumbnailator/grid.png");
InputStreamImageSource source = new InputStreamImageSource(is);
```

### LLM Instruction Prompt
- When using `InputStreamImageSource`, ensure the `InputStream` is initialized and points to a valid image format.
- Always wrap the instantiation in logic that handles potential `NullPointerException` if the stream source is unreliable.
- Use this class specifically when integrating with `SourceSinkThumbnailTask` for custom task pipelines.

### Prompt Snippet
```text
InputStreamImageSource source = new InputStreamImageSource(inputStream);
```

### Common Failure Modes
- **`NullPointerException`**: Occurs if the provided `InputStream` is `null`.
- **`IOException`**: May occur during the subsequent processing phase if the `InputStream` is closed prematurely or contains corrupted/unsupported image data.
- **Format Unsupported**: If the `InputStream` contains a format not registered with the JVM's `ImageIO`, the task will fail during execution.

### Fix Code Hint
```java
// Ensure the stream is valid before instantiation
if (inputStream != null) {
    InputStreamImageSource source = new InputStreamImageSource(inputStream);
    // Proceed with task...
}
```

## API Test: `NullResizer`

### Signature
```java
public NullResizer()
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/NullResizer.java:48_

_Source doc:_ Instantiates the {@code NullResizer} which draws the source image at the origin of the destination image.

### Goal
The `NullResizer` provides a non-scaling operation that draws the source `BufferedImage` directly onto the destination `BufferedImage` starting at the (0,0) coordinate without performing any interpolation or resizing.

### Parameters
_None._

### Input
The caller must provide two `BufferedImage` objects to the `resize(BufferedImage, BufferedImage)` method. The source image must be a valid image format supported by the JVM's `ImageIO`. The destination image must be pre-allocated with sufficient dimensions to accommodate the source image if a full copy is intended.

### Output
Returns `void`. The operation modifies the destination `BufferedImage` in-place by drawing the source image onto it.

### Valid Call Patterns
```java
// Direct usage as demonstrated in the test suite
BufferedImage srcImage = getImageFromResource("Thumbnailator/grid.png");
BufferedImage destImage = new BufferedImage(100, 100, BufferedImage.TYPE_INT_ARGB);

new NullResizer().resize(srcImage, destImage);
```

### LLM Instruction Prompt
- When using `NullResizer`, always ensure the destination `BufferedImage` is initialized before calling the `resize` method.
- Do not expect any scaling or resampling; the source image is drawn at the origin (0,0) of the destination.
- If the destination image is smaller than the source, the source image will be clipped to the bounds of the destination.

### Prompt Snippet
```text
// Example of using NullResizer to copy a source image to a destination buffer
NullResizer resizer = new NullResizer();
resizer.resize(sourceImage, destinationImage);
```

### Common Failure Modes
- **NullPointerException:** Occurs if either the source or destination `BufferedImage` passed to the `resize` method is null.
- **Clipping:** If the destination `BufferedImage` is smaller than the source `BufferedImage`, the `NullResizer` will only draw the portion of the source that fits within the destination's dimensions, effectively cropping the image.
- **Incompatible Types:** While `NullResizer` handles drawing, ensure the destination `BufferedImage` type is compatible with the source image's color model to avoid unexpected color shifts or rendering artifacts.

### Fix Code Hint
```java
// Ensure destination is at least as large as the source to avoid clipping
BufferedImage dest = new BufferedImage(src.getWidth(), src.getHeight(), BufferedImage.TYPE_INT_ARGB);
new NullResizer().resize(src, dest);
```

## API Test: `OutputStreamImageSink`

### Signature
```java
public OutputStreamImageSink(OutputStream os)
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/OutputStreamImageSink.java:66_

_Source doc:_ Instantiates an {@link OutputStreamImageSink} with the {@link OutputStream} to which the thumbnail should be written to. @param os		The {@link OutputStream} to write the thumbnail to. @throws NullPointerException		If the {@link OutputStream} is {@code null}.

### Goal
Provides a destination sink for Thumbnailator to write processed thumbnail image data directly into a provided `OutputStream`.

### Parameters
- `os` (`OutputStream`): The target stream where the resulting thumbnail image data will be serialized.

### Input
- **Data:** A valid `OutputStream` (e.g., `ByteArrayOutputStream`, `FileOutputStream`).
- **Preconditions:** The `OutputStream` must not be `null`. The caller is responsible for managing the lifecycle (opening/closing) of the stream. The image format written to the stream is determined by the `ThumbnailParameter` configuration used in the `ThumbnailTask`.

### Output
Returns `null` (constructor). The sink acts as a destination for the `Thumbnailator.createThumbnail` task, which writes the processed image bytes to the provided `OutputStream`.

### Valid Call Patterns
```java
ByteArrayOutputStream os = new ByteArrayOutputStream();
OutputStreamImageSink destination = new OutputStreamImageSink(os);
```

### LLM Instruction Prompt
- Use `OutputStreamImageSink` when the thumbnail output must be directed to a stream rather than a file or memory object.
- Always pair this sink with a `ThumbnailTask` (e.g., `SourceSinkThumbnailTask`) and a `ThumbnailParameter` that defines the desired output format.
- Ensure the `OutputStream` is initialized before passing it to the constructor.

### Prompt Snippet
```text
// Example of using OutputStreamImageSink in a task
OutputStreamImageSink destination = new OutputStreamImageSink(outputStream);
Thumbnailator.createThumbnail(new SourceSinkThumbnailTask<InputStream, OutputStream>(param, source, destination));
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `OutputStream` passed to the constructor is `null`.
- **IOException:** May occur during the execution of the thumbnail task if the underlying `OutputStream` is closed or becomes unreachable during the write process.
- **Format Mismatch:** If the `ThumbnailParameter` is not configured with a specific format, the output may default to a format not supported by the destination stream's requirements.

### Fix Code Hint
```java
// Ensure the stream is valid and non-null
if (os != null) {
    OutputStreamImageSink sink = new OutputStreamImageSink(os);
    // Proceed with task execution
}
```

## API Test: `Pipeline`

### Signature
```java
public Pipeline(ImageFilter... filters)
public Pipeline(List<ImageFilter> filters)
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Pipeline.java_

_Source doc:_ Instantiates a new {@link Pipeline} with an array of {@link ImageFilter}s to apply. @param filters		An array of {@link ImageFilter}s to apply.

### Goal
The `Pipeline` class provides a mechanism to chain multiple `ImageFilter` operations, allowing them to be applied sequentially to a `BufferedImage` in a single execution step.

### Parameters
- `filters` (`ImageFilter...`): A variable-length array or a `List` of `ImageFilter` objects that define the sequence of image processing transformations to be performed.

### Input
- **Data:** A `BufferedImage` object.
- **Preconditions:** The input `BufferedImage` must be initialized and valid. The `ImageFilter` objects provided must be compatible with the `apply(BufferedImage)` method defined by the `ImageFilter` interface.

### Output
Returns `void` (the `apply` method modifies the provided `BufferedImage` in-place).

### Valid Call Patterns
```java
// Using array constructor
ImageFilter filter1 = new SomeImageFilter();
Pipeline pipeline = new Pipeline(filter1);
pipeline.apply(img);

// Using List constructor
Pipeline pipeline = new Pipeline(Arrays.asList(filter1, filter2));
pipeline.apply(img);
```

### LLM Instruction Prompt
- When constructing a `Pipeline`, ensure all provided objects implement the `ImageFilter` interface.
- Use the `Pipeline` to group multiple filters when you need to perform batch transformations on a single `BufferedImage` instance.
- Remember that `Pipeline` itself implements `ImageFilter`, allowing pipelines to be nested or treated as a single filter unit.

### Prompt Snippet
```text
// Example of creating a pipeline with multiple filters
Pipeline pipeline = new Pipeline(filter1, filter2);
pipeline.apply(bufferedImage);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `filters` array or list contains a `null` element, or if the `filters` argument itself is `null`.
- **Unsupported Image Type:** If an `ImageFilter` in the pipeline is not compatible with the specific `BufferedImage` type (e.g., attempting to apply a filter requiring alpha channels to an `TYPE_BYTE_BINARY` image), the filter may throw an exception or produce unexpected visual artifacts.
- **Modification Errors:** Since `apply` modifies the image in-place, ensure the `BufferedImage` is not being accessed by other threads during the pipeline execution to avoid race conditions.

### Fix Code Hint
```java
// Ensure filters are initialized and non-null before passing to the constructor
ImageFilter[] filters = { filter1, filter2 };
if (filters != null) {
    new Pipeline(filters).apply(image);
}
```

## API Test: `ProgressiveBilinearResizer`

### Signature
```java
public ProgressiveBilinearResizer()
public ProgressiveBilinearResizer(Map<RenderingHints.Key, Object> hints)
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/ProgressiveBilinearResizer.java:70  (+1 more definition site/overload)_

_Source doc:_ Instantiates a {@link ProgressiveBilinearResizer} with the specified rendering hints. @param hints		Additional rendering hints to apply.

### Goal
Provides a high-quality image resizing implementation that uses a progressive bilinear scaling approach to reduce aliasing artifacts when downscaling images.

### Parameters
- `hints` (`Map<RenderingHints.Key, Object>`): A map of Java 2D `RenderingHints` used to configure the underlying `Graphics2D` operations during the resizing process.

### Input
- **Data:** A `BufferedImage` source object.
- **Preconditions:** The input `BufferedImage` must be a valid, non-null image object. The resizer is typically invoked via a `ResizerFactory` within a `ThumbnailMaker` pipeline.

### Output
Returns `void` — The `resize` method performs an in-place or destination-based transformation, writing the scaled image data into the provided destination `BufferedImage`.

### Valid Call Patterns
```java
// Using the default constructor
Resizer resizer = new ProgressiveBilinearResizer();

// Using the constructor with rendering hints
Map<RenderingHints.Key, Object> hints = new HashMap<RenderingHints.Key, Object>();
hints.put(RenderingHints.KEY_INTERPOLATION, RenderingHints.VALUE_INTERPOLATION_BILINEAR);
Resizer resizer = new ProgressiveBilinearResizer(hints);
```

### LLM Instruction Prompt
- When generating code for `ProgressiveBilinearResizer`, always instantiate it as a `Resizer` interface type.
- Ensure the resizer is used within the context of a `ThumbnailMaker` (e.g., `FixedSizeThumbnailMaker` or `ScaledThumbnailMaker`) by passing it through a `ResizerFactory`.
- Do not attempt to use this class for upscaling; it is optimized for progressive downscaling.

### Prompt Snippet
```text
// Example of integrating ProgressiveBilinearResizer into a pipeline
Resizer resizer = new ProgressiveBilinearResizer();
// Use the resizer within a maker's resizerFactory() method
```

### Common Failure Modes
- **NullPointerException:** Occurs if the source or destination `BufferedImage` passed to the `resize` method is null.
- **Incompatible Dimensions:** Attempting to resize to non-positive dimensions will result in an `IllegalArgumentException` during the `resize` operation.
- **Unsupported Image Type:** If the source `BufferedImage` type is not supported by the underlying `Graphics2D` context, the resulting thumbnail may be blank or corrupted.

### Fix Code Hint
```java
// Ensure the destination image is initialized with the correct dimensions and type
BufferedImage dest = new BufferedImage(width, height, BufferedImage.TYPE_INT_ARGB);
new ProgressiveBilinearResizer().resize(sourceImage, dest);
```

## API Test: `Region`

### Signature
```java
public Region(Position position, Size size)
```
_Source: source/src/main/java/net/coobird/thumbnailator/geometry/Region.java:59_

_Source doc:_ Instantiates a representation of a region from a {@link Position} and {@link Size}. @param position		Position of the region. @param size			Size of the region. @throws NullPointerException		When the position and/or the size is {@code null}.

### Goal
Defines a specific rectangular area within an image by combining a coordinate-based `Position` and a dimension-based `Size` for use in cropping operations.

### Parameters
- `position` (`Position`): The anchor point or coordinate strategy (e.g., `Coordinate` or `Positions` constants) defining the top-left corner of the region.
- `size` (`Size`): The dimensions (e.g., `AbsoluteSize`) defining the width and height of the region.

### Input
The caller must provide valid implementations of the `Position` and `Size` interfaces. These are typically used in conjunction with the `sourceRegion(Region)` method in the `Thumbnails.Builder` pipeline to define the area of the source image to be processed.

### Output
Returns an instance of `Region` representing the defined spatial area.

### Valid Call Patterns
```java
BufferedImage thumbnail = Thumbnails.of(img)
    .sourceRegion(new Region(new Coordinate(0, 0), new AbsoluteSize(50, 50)))
    .size(50, 50)
    .asBufferedImage();
```

### LLM Instruction Prompt
- When generating code using `Region`, always wrap it within the `.sourceRegion()` method of a `Thumbnails.Builder` pipeline.
- Ensure the `Position` and `Size` arguments are instantiated with concrete implementations like `Coordinate` and `AbsoluteSize` respectively.
- Do not pass `null` values to the constructor, as this will trigger a `NullPointerException`.

### Prompt Snippet
```text
// Use this pattern to define a crop region:
.sourceRegion(new Region(new Coordinate(x, y), new AbsoluteSize(width, height)))
```

### Common Failure Modes
- **NullPointerException**: Occurs if either the `position` or `size` argument is passed as `null`.
- **Out of Bounds**: If the defined `Region` exceeds the dimensions of the source image, the resulting thumbnail may contain empty or undefined areas depending on the underlying `ImageIO` implementation.
- **Type Mismatch**: Attempting to pass objects that do not implement the `Position` or `Size` interfaces.

### Fix Code Hint
```java
// Ensure non-null, valid coordinate and size objects are provided
Region region = new Region(new Coordinate(0, 0), new AbsoluteSize(100, 100));
Thumbnails.of(image).sourceRegion(region).size(100, 100).toFile("output.jpg");
```

## API Test: `RelativeSize`

### Signature
```java
public RelativeSize(double scalingFactor)
```
_Source: source/src/main/java/net/coobird/thumbnailator/geometry/RelativeSize.java:52_

_Source doc:_ Instantiates an object which calculates the size of an object, using the given scaling factor. @param scalingFactor		The scaling factor to use to determine the size of the enclosing object. @throws IllegalArgumentException		When the scaling factor is not within the range of {@code 0.0d} and {@code 1.0d}, inclusive.

### Goal
Calculates the dimensions of an image or region based on a percentage of the enclosing object's size.

### Parameters
- `scalingFactor` (`double`): A value between 0.0 and 1.0 (inclusive) representing the ratio of the target size relative to the source size.

### Input
- **Data:** A `double` value representing the scaling factor.
- **Preconditions:** The provided `scalingFactor` must be within the range `[0.0, 1.0]`. Values outside this range will trigger an `IllegalArgumentException`.

### Output
Returns an instance of `RelativeSize` which implements the `Size` interface, used by Thumbnailator to determine dimensions during image processing tasks.

### Valid Call Patterns
```java
// Inferred from signature:
RelativeSize size = new RelativeSize(0.5);
```

### LLM Instruction Prompt
- When using `RelativeSize`, ensure the `scalingFactor` is a `double` between `0.0` and `1.0`.
- Use this class when defining custom sizing logic within the Thumbnailator fluent pipeline where a relative scale is required rather than absolute pixel dimensions.

### Prompt Snippet
```text
new RelativeSize(0.5)
```

### Common Failure Modes
- **IllegalArgumentException:** Occurs if the `scalingFactor` is less than `0.0` or greater than `1.0`.
- **Type Mismatch:** Attempting to pass an integer or float without casting to `double` may cause compilation errors depending on the context.

### Fix Code Hint
```java
// Ensure the value is a double and within bounds
double factor = 0.75; 
if (factor >= 0.0 && factor <= 1.0) {
    RelativeSize size = new RelativeSize(factor);
}
```

## API Test: `ScaledThumbnailMaker`

### Signature
```java
public ScaledThumbnailMaker()
public ScaledThumbnailMaker(double factor)
public ScaledThumbnailMaker(double widthFactor, double heightFactor)
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ScaledThumbnailMaker.java:128  (+2 more definition site/overload)_

_Source doc:_ Creates an instance of {@code ScaledThumbnailMaker} with the specified scaling factors for the width and height. @param widthFactor		The scaling factor to apply to the width when resizing an image to create a thumbnail. @param heightFactor		The scaling factor to apply to the height when resizing an image to create a thumbnail. @since	0.3.10

### Goal
Provides a mechanism to resize images by applying specific scaling factors to the width and height dimensions.

### Parameters
- `widthFactor` (`double`): The multiplier applied to the original image width to determine the thumbnail width.
- `heightFactor` (`double`): The multiplier applied to the original image height to determine the thumbnail height.

### Input
Requires a `BufferedImage` as input to the `make(BufferedImage)` method. The input image must be a valid format supported by the host JVM's `ImageIO`.

### Output
Returns a `BufferedImage` representing the resized thumbnail.

### Valid Call Patterns
```java
// Scaling by a uniform factor
BufferedImage thumbnail = new ScaledThumbnailMaker(0.5).make(img);

// Scaling by distinct width and height factors
BufferedImage thumbnail = new ScaledThumbnailMaker(0.5, 0.8).make(img);
```

### LLM Instruction Prompt
- Use `ScaledThumbnailMaker` when you need to resize an image based on relative scaling factors rather than absolute pixel dimensions.
- Always provide the scaling factor(s) in the constructor; the no-argument constructor is uninitialized and will result in a failure when `make()` is called.
- Ensure the input is a `BufferedImage` object.

### Prompt Snippet
```text
// Example of using ScaledThumbnailMaker for 50% scaling
BufferedImage result = new ScaledThumbnailMaker(0.5, 0.5).make(sourceImage);
```

### Common Failure Modes
- **Uninitialized Instance:** Calling `make()` on an instance created with the no-argument constructor will fail, as the internal state is not prepared for processing.
- **Invalid Input:** Passing a null `BufferedImage` or an image format not supported by the JVM's `ImageIO` will result in processing errors.

### Fix Code Hint
```java
// Ensure the maker is initialized with valid double factors
BufferedImage thumbnail = new ScaledThumbnailMaker(0.5, 0.5).make(sourceImage);
```

## API Test: `SourceSinkThumbnailTask`

### Signature
```java
public SourceSinkThumbnailTask(ThumbnailParameter param, ImageSource<S> source, ImageSink<D> destination)
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/SourceSinkThumbnailTask.java:77_

_Source doc:_ Creates a {@link ThumbnailTask} in which an image is retrived from the specified {@link ImageSource} and written to the specified {@link ImageSink}, using the parameters provided in the specified {@link ThumbnailParameter}. @param param				The parameters to use to create the thumbnail. @param source			The source from which the image is retrieved or read from. @param destination		The destination to which the thumbnail is stored or written to. @throws NullPointerException		If either the parameter, {@link ImageSource} or {@link ImageSink} is {@code null}.

### Goal
Encapsulates the execution logic for a thumbnail generation task by coordinating the retrieval of an image from a source and the writing of the processed result to a destination sink.

### Parameters
- `param` (`ThumbnailParameter`): The configuration object defining scaling, rotation, quality, and format settings for the thumbnail.
- `source` (`ImageSource<S>`): The provider of the raw image data (e.g., `FileImageSource`, `InputStreamImageSource`).
- `destination` (`ImageSink<D>`): The target for the processed image data (e.g., `FileImageSink`, `OutputStreamImageSink`).

### Input
- **Data:** An image accessible via the provided `ImageSource`.
- **Formats:** Must be a format supported by the host JVM's `ImageIO` capabilities.
- **Preconditions:** The `ThumbnailParameter`, `ImageSource`, and `ImageSink` must not be `null`. The source must be readable and the destination must be writable.

### Output
Returns `void` (the task is executed by passing this object to `Thumbnailator.createThumbnail(task)`).

### Valid Call Patterns
```java
ThumbnailParameter param = new ThumbnailParameterBuilder()
    .size(50, 50)
    .format(ThumbnailParameter.DETERMINE_FORMAT)
    .build();

Thumbnailator.createThumbnail(
    new SourceSinkThumbnailTask(param, source, destination)
);
```

### LLM Instruction Prompt
- Use `SourceSinkThumbnailTask` only when manually orchestrating a task outside of the fluent `Thumbnails.of(...)` builder.
- Always ensure the `ThumbnailParameter` is fully configured before instantiation.
- The task must be passed to `Thumbnailator.createThumbnail()` to trigger the processing pipeline.

### Prompt Snippet
```text
new SourceSinkThumbnailTask(param, source, destination)
```

### Common Failure Modes
- **NullPointerException:** Occurs if any of the three constructor arguments are `null`.
- **UnsupportedFormatException:** Occurs if the `ImageSource` provides an image format that the JVM's `ImageIO` cannot decode, or if the `ImageSink` cannot encode the requested format.
- **IOException:** Occurs if the source cannot be read or the destination cannot be written to (e.g., file permission issues or closed streams).

### Fix Code Hint
```java
// Ensure the source and destination are initialized and the parameter is valid
try {
    SourceSinkThumbnailTask task = new SourceSinkThumbnailTask(param, source, destination);
    Thumbnailator.createThumbnail(task);
} catch (NullPointerException e) {
    // Handle missing configuration
} catch (IOException e) {
    // Handle I/O errors during processing
}
```

## API Test: `StreamThumbnailTask`

### Signature
```java
public StreamThumbnailTask(ThumbnailParameter param, InputStream is, OutputStream os)
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/StreamThumbnailTask.java:66_

_Source doc:_ Creates a {@link ThumbnailTask} in which streamed image data from the specified {@link InputStream} is output to a specified {@link OutputStream}, using the parameters provided in the specified {@link ThumbnailParameter}. @param param		The parameters to use to create the thumbnail. @param is		The {@link InputStream} from which to obtain image data. @param os		The {@link OutputStream} to send thumbnail data to. @throws NullPointerException		If the parameter is {@code null}.

### Goal
Creates a task that processes image data from an `InputStream` and writes the resulting thumbnail to an `OutputStream` based on the provided `ThumbnailParameter` configuration.

### Parameters
- `param` (`ThumbnailParameter`): The configuration object defining scaling, rotation, watermarking, and output format settings.
- `is` (`InputStream`): The source stream containing the raw image data to be processed.
- `os` (`OutputStream`): The destination stream where the processed thumbnail data will be written.

### Input
The caller must provide a valid `InputStream` containing image data supported by the host JVM's `ImageIO` capabilities. The `ThumbnailParameter` must be initialized with valid dimensions or scaling factors.

### Output
Returns `void` (constructor). The task performs the transformation and writes the result to the provided `OutputStream` when the task is executed.

### Valid Call Patterns
```java
ThumbnailParameter param = new ThumbnailParameter(
        new Dimension(50, 50),
        null,
        true,
        "png",
        ThumbnailParameter.DEFAULT_FORMAT_TYPE,
        ThumbnailParameter.DEFAULT_QUALITY,
        BufferedImage.TYPE_INT_ARGB,
        null,
        Resizers.PROGRESSIVE,
        true,
        true
);

StreamThumbnailTask task = new StreamThumbnailTask(param, inputStream, outputStream);
```

### LLM Instruction Prompt
- When using `StreamThumbnailTask`, ensure the `ThumbnailParameter` is not null, as this will trigger a `NullPointerException`.
- Ensure the `InputStream` is open and contains a valid image format recognized by the JVM.
- The task does not automatically close the `InputStream` or `OutputStream`; manage stream lifecycles externally.

### Prompt Snippet
```text
new StreamThumbnailTask(param, inputStream, outputStream)
```

### Common Failure Modes
- `NullPointerException`: Occurs if the `ThumbnailParameter` argument is passed as `null`.
- `IOException`: Occurs if the `InputStream` is invalid, inaccessible, or does not contain a supported image format.
- `IllegalArgumentException`: May occur if the `ThumbnailParameter` contains invalid dimensions or unsupported configuration values.

### Fix Code Hint
```java
// Ensure parameter is initialized and streams are valid
if (param != null) {
    StreamThumbnailTask task = new StreamThumbnailTask(param, is, os);
    // Execute task logic
}
```

## API Test: `ThumbnailMaker`

### Signature
```java
public ThumbnailMaker()
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ThumbnailMaker.java:163_

_Source doc:_ Creates and initializes an instance of {@link ThumbnailMaker}.

### Goal
Initializes a `ThumbnailMaker` instance to facilitate the programmatic creation of thumbnails from source images within the Thumbnailator pipeline.

### Parameters
_None._

### Input
No parameters are required for initialization. However, the resulting instance expects image data to be processed in accordance with the JVM's `ImageIO` capabilities. Preconditions include ensuring the source image is in a format supported by the host JVM and that the environment remains headless, as the library prohibits desktop UI dependencies.

### Output
Returns an instance of `ThumbnailMaker`. This object represents a builder-style component used to manage the transformation and generation of thumbnail artifacts.

### Valid Call Patterns
```java
// Inferred from signature:
ThumbnailMaker maker = new ThumbnailMaker();
```

### LLM Instruction Prompt
- When using `ThumbnailMaker`, ensure the instance is used within the context of the Thumbnailator library's fluent API.
- Do not attempt to pass arguments to the constructor, as it is a parameterless initialization.
- Ensure that all image processing operations performed by the maker are executed in a headless environment, as the library is designed for non-UI, server-side, or batch processing tasks.

### Prompt Snippet
```text
ThumbnailMaker maker = new ThumbnailMaker();
```

### Common Failure Modes
- **Unsupported Format:** Attempting to process an image format not supported by the host JVM's `ImageIO` implementation will result in runtime exceptions during the processing phase.
- **Environment Constraints:** Attempting to invoke UI-related classes or network-dependent operations within the same pipeline will violate the library's headless design constraints.
- **Initialization Errors:** Failure to properly configure the `ThumbnailParameter` before invoking the maker's processing methods may lead to default behavior that does not match the desired output quality or dimensions.

### Fix Code Hint
```java
// Ensure the environment supports the image format via ImageIO
// and use the fluent API to configure the maker before execution.
ThumbnailMaker maker = new ThumbnailMaker();
// Proceed to configure parameters and apply to source image
```

## API Test: `ThumbnailParameter`

### Signature
```java
public ThumbnailParameter( Dimension thumbnailSize, Region sourceRegion, boolean keepAspectRatio, String outputFormat, String outputFormatType, float outputQuality, int imageType, List<ImageFilter> filters, Resizer resizer, boolean fitWithinDimensions, boolean useExifOrientation
public ThumbnailParameter( double widthScalingFactor, double heightScalingFactor, Region sourceRegion, boolean keepAspectRatio, String outputFormat, String outputFormatType, float outputQuality, int imageType, List<ImageFilter> filters, Resizer resizer, boolean fitWithinDimensions,
public ThumbnailParameter( Dimension thumbnailSize, Region sourceRegion, boolean keepAspectRatio, String outputFormat, String outputFormatType, float outputQuality, int imageType, List<ImageFilter> filters, ResizerFactory resizerFactory, boolean fitWithinDimensions, boolean useExifOrientation
public ThumbnailParameter( double widthScalingFactor, double heightScalingFactor, Region sourceRegion, boolean keepAspectRatio, String outputFormat, String outputFormatType, float outputQuality, int imageType, List<ImageFilter> filters, ResizerFactory resizerFactory, boolean fitWithinDimensions,
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:437  (+3 more definition site/overload)_

_Source doc:_ Creates an object holding the parameters needed in order to make a thumbnail. @param thumbnailSize		The size of the thumbnail to generate. @param sourceRegion		The region of the source image to use when creating a thumbnail. A value of {@code null} indicates that the entire source image should be used to create the thumbnail. @param keepAspectRatio	Indicates whether or not the thumbnail should maintain the aspect ratio of the original image. @param outputFormat		A string indicating the compression format that should be applied on the thumbnail. A value of {@link ThumbnailParameter#ORIGINAL_FORMAT} should be provided if the same image format as the original should	be used for the thumbnail. A value of {@link ThumbnailParameter#DETERMINE_FORMAT} should be provided if the output format of the thumbnail should be the determined from the information available, such as the output file name of the thumbnail. @param outputFormatType	A string indicating the compression type that should be used when writing the thumbnail. A value of {@link ThumbnailParameter#DEFAULT_FORMAT_TYPE} should be provided if the thumbnail should be written using the default compression type of the codec specified in {@code outputFormat}. @param outputQuality		A value from {@code 0.0f} to {@code 1.0f} which indicates the quality setting to use for the compression of the thumbnail. {@code 0.0f} indicates the lowest quality, {@code 1.0f} indicates the highest quality setting for the compression. {@link ThumbnailParameter#DEFAULT_QUALITY} should be specified when the codec's default compression quality settings should be used. @param imageType 		The {@link BufferedImage} image type of the thumbnail. A value of {@link ThumbnailParameter#DEFAULT_IMAGE_TYPE} should be specified when the default image type should be used when creating the thumbnail. @param filters			The {@link ImageFilter}s to apply to the thumbnail. A value of {@code null} will be recognized as no filters are to be applied. The filters are applied after the original image has been resized. @param resizer			The {@link Resizer} to use when performing the resizing operation to create a thumbnail. @param fitWithinDimensions	Whether or not to fit the thumbnail within the specified dimensions. <p> If {@code true} is specified, then the thumbnail will be sized to fit within the specified dimensions, if the thumbnail is going to exceed those dimensions. @param useExifOrientation	Whether or not to use the Exif metadata to determine the orientation of the thumbnail. <p> If {@code true} is specified, then the Exif metadata will be used to determine the orientation of the thumbnail. @throws IllegalArgumentException 	If size is {@code null} or if the dimensions are negative, or if the {@link Resizer} is null. @since	0.4.3

### Goal
`ThumbnailParameter` encapsulates the configuration settings required to define a thumbnail generation task, including dimensions, quality, format, and image processing filters.

### Parameters
- `thumbnailSize` (`Dimension`): The target dimensions for the generated thumbnail.
- `sourceRegion` (`Region`): The specific area of the source image to process; pass `null` to use the entire image.
- `keepAspectRatio` (`boolean`): If `true`, preserves the original image's aspect ratio during resizing.
- `outputFormat` (`String`): The compression format (e.g., "jpg", "png"). Use `ThumbnailParameter.ORIGINAL_FORMAT` or `ThumbnailParameter.DETERMINE_FORMAT`.
- `outputFormatType` (`String`): The specific compression type for the codec; use `ThumbnailParameter.DEFAULT_FORMAT_TYPE` for defaults.
- `outputQuality` (`float`): Compression quality from `0.0f` (lowest) to `1.0f` (highest). Use `ThumbnailParameter.DEFAULT_QUALITY`.
- `imageType` (`int`): The `BufferedImage` type; use `ThumbnailParameter.DEFAULT_IMAGE_TYPE` or `ThumbnailParameter.ORIGINAL_IMAGE_TYPE`.
- `filters` (`List<ImageFilter>`): A list of filters to apply post-resize; pass `null` for no filters.
- `resizer` (`Resizer`): The scaling algorithm implementation (e.g., `Resizers.PROGRESSIVE`).
- `fitWithinDimensions` (`boolean`): If `true`, ensures the thumbnail does not exceed the specified `thumbnailSize`.
- `useExifOrientatio` (`boolean`): If `true`, automatically adjusts the image orientation based on EXIF metadata.

### Input
The caller must provide valid `Dimension` or scaling factors, a `Resizer` instance, and valid image format strings supported by the host JVM's `ImageIO`.

### Output
Returns an instance of `ThumbnailParameter` used to configure a `ThumbnailTask`.

### Valid Call Patterns
```java
ThumbnailParameter param = new ThumbnailParameter(
    new Dimension(50, 50),
    null,
    true,
    "jpg",
    ThumbnailParameter.DEFAULT_FORMAT_TYPE,
    ThumbnailParameter.DEFAULT_QUALITY,
    BufferedImage.TYPE_INT_ARGB,
    null,
    Resizers.PROGRESSIVE,
    true,
    true
);
```

### LLM Instruction Prompt
- Always use `ThumbnailParameter.DEFAULT_IMAGE_TYPE` or `ThumbnailParameter.ORIGINAL_IMAGE_TYPE` for the `imageType` parameter unless a specific `BufferedImage` type is required.
- Ensure the `Resizer` is never `null` to avoid `IllegalArgumentException`.
- When using `outputFormat`, prefer `ThumbnailParameter.DETERMINE_FORMAT` if the output is intended to be inferred from a file path.

### Prompt Snippet
```text
// Configure parameters for a 50x50 thumbnail with progressive resizing
ThumbnailParameter param = new ThumbnailParameter(
    new Dimension(50, 50), null, true, "jpg", 
    ThumbnailParameter.DEFAULT_FORMAT_TYPE, ThumbnailParameter.DEFAULT_QUALITY, 
    BufferedImage.TYPE_INT_ARGB, null, Resizers.PROGRESSIVE, true, true
);
```

### Common Failure Modes
- Passing `null` for the `Resizer` argument, which triggers an `IllegalArgumentException`.
- Providing negative values for `thumbnailSize` dimensions, which triggers an `IllegalArgumentException`.
- Using an unsupported `outputFormat` string that the host JVM's `ImageIO` cannot encode.

### Fix Code Hint
```java
// Ensure dimensions are positive and resizer is initialized
if (width > 0 && height > 0) {
    ThumbnailParameter param = new ThumbnailParameter(new Dimension(width, height), ...);
}
```

## API Test: `ThumbnailParameterBuilder`

### Signature
```java
public ThumbnailParameterBuilder()
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:105_

_Source doc:_ Creates an instance of a {@link ThumbnailParameterBuilder}.

### Goal
Provides a fluent builder interface to construct `ThumbnailParameter` objects, which encapsulate configuration settings such as scaling, resizing algorithms, and image filters for thumbnail generation tasks.

### Parameters
_None._

### Input
No input parameters are required to instantiate the builder. However, the resulting `ThumbnailParameter` object (produced via `.build()`) requires valid configuration settings (e.g., size or scale) before being passed to a `ThumbnailTask`.

### Output
Returns an instance of `ThumbnailParameterBuilder`. This object is used to chain configuration methods (e.g., `.size()`, `.resizerFactory()`, `.scale()`) before calling `.build()` to produce a `ThumbnailParameter` instance.

### Valid Call Patterns
```java
ThumbnailParameter param = new ThumbnailParameterBuilder()
        .size(100, 100)
        .resizerFactory(DefaultResizerFactory.getInstance())
        .build();
```

### LLM Instruction Prompt
- Use `ThumbnailParameterBuilder` to create a configuration object when you need fine-grained control over the thumbnail process that is not covered by the standard `Thumbnails.of()` fluent API.
- Always chain the `.build()` method at the end of the builder sequence to obtain the required `ThumbnailParameter` object.
- Ensure that either a size or a scale is defined within the builder before calling `.build()`.

### Prompt Snippet
```text
ThumbnailParameter param = new ThumbnailParameterBuilder()
    .size(width, height)
    .build();
```

### Common Failure Modes
- **Incomplete Configuration:** Calling `.build()` without setting a size or scale will result in a `ThumbnailParameter` that may cause errors during the execution of a `ThumbnailTask`.
- **Type Mismatch:** Attempting to use a `ThumbnailParameter` with an incompatible `ThumbnailTask` (e.g., passing a parameter configured for a specific resizer to a task that does not support it).
- **Null Configuration:** Passing a null `ResizerFactory` or other null components to the builder methods will trigger a `NullPointerException` during the build process or task execution.

### Fix Code Hint
```java
// Ensure the builder is fully configured before use
ThumbnailParameter param = new ThumbnailParameterBuilder()
    .size(100, 100)
    .resizerFactory(DefaultResizerFactory.getInstance())
    .build();
```

## API Test: `Transparency`

### Signature
```java
public Transparency(float alpha)
public Transparency(double alpha)
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Transparency.java:58  (+1 more definition site/overload)_

_Source doc:_ Instantiates a {@link Transparency} filter with the specified opacity. @param alpha		The opacity of the resulting image. The value should be between {@code 0.0f} (transparent) to {@code 1.0f} (opaque), inclusive. @throws IllegalArgumentException	If the specified opacity is outside of the range specified above.

### Goal
Applies a uniform opacity level to an image, allowing for the adjustment of transparency across the entire pixel data.

### Parameters
- `alpha` (`float`): The opacity level to apply. Must be a value between `0.0f` (fully transparent) and `1.0f` (fully opaque), inclusive.

### Input
- **Data:** A `BufferedImage` object.
- **Preconditions:** The image must be in a format that supports transparency (e.g., `TYPE_INT_ARGB`). While the filter accepts any `BufferedImage`, the visual effect of transparency is dependent on the underlying image type's ability to represent alpha channels.

### Output
Returns `void` — The `Transparency` class implements `ImageFilter`, meaning it modifies the provided `BufferedImage` in-place when the `apply(BufferedImage)` method is invoked.

### Valid Call Patterns
```java
// Using float constructor
ImageFilter filter = new Transparency(0.5f);
filter.apply(image);

// Using double constructor
ImageFilter filter = new Transparency(0.5);
filter.apply(image);
```

### LLM Instruction Prompt
- When using `Transparency`, ensure the `alpha` value is strictly within the range `[0.0, 1.0]`.
- Always instantiate the filter before calling the `apply` method on a `BufferedImage`.
- Note that this filter modifies the image in-place; if the original image must be preserved, create a copy using `BufferedImages.copy(originalImage)` before applying the filter.

### Prompt Snippet
```text
ImageFilter filter = new Transparency(0.5f);
filter.apply(myBufferedImage);
```

### Common Failure Modes
- **IllegalArgumentException:** Thrown if the `alpha` value provided is less than `0.0f` or greater than `1.0f`.
- **Visual Ineffectiveness:** If the input `BufferedImage` is of a type that does not support an alpha channel (e.g., `TYPE_INT_RGB`), the transparency filter may not produce the expected visual result as the alpha information cannot be stored.

### Fix Code Hint
```java
// Ensure alpha is within bounds
float alpha = 0.75f;
if (alpha >= 0.0f && alpha <= 1.0f) {
    new Transparency(alpha).apply(image);
} else {
    throw new IllegalArgumentException("Alpha must be between 0.0 and 1.0");
}
```

## API Test: `URLImageSource`

### Signature
```java
public URLImageSource(URL url)
public URLImageSource(String url)
public URLImageSource(URL url, Proxy proxy)
public URLImageSource(String url, Proxy proxy)
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/URLImageSource.java:99  (+3 more definition site/overload)_

_Source doc:_ Instantiates an {@link URLImageSource} with the URL from which the source image should be retrieved from, along with the proxy to use to connect to the aforementioned URL. @param url		URL to the source image. @param proxy		Proxy to use to connect to the URL. @throws NullPointerException		If the URL and or the proxy is null

### Goal
`URLImageSource` provides a mechanism to fetch and load an image from a remote network location into the Thumbnailator pipeline for processing.

### Parameters
- `url` (`URL`): The location of the source image to be retrieved. Can be provided as a `URL` object or a `String` representation.
- `proxy` (`Proxy`): The `java.net.Proxy` configuration to use when establishing the network connection to the specified URL.

### Input
The caller must provide a valid `URL` or `String` pointing to an image resource. The resource must be in a format supported by the host JVM's `ImageIO` capabilities. Note that while Thumbnailator is designed for headless operation, this class requires network access to retrieve the remote resource.

### Output
Returns an instance of `URLImageSource` which acts as a data provider for thumbnail tasks. Calling the `.read()` method on this instance returns a `BufferedImage` containing the decoded image data.

### Valid Call Patterns
```java
// Using a URL and a specific Proxy
Proxy proxy = Proxy.NO_PROXY;
URLImageSource source = new URLImageSource(new URL("http://example.com/image.png"), proxy);
BufferedImage img = source.read();
```

### LLM Instruction Prompt
- When using `URLImageSource`, ensure the `url` argument is not null to avoid a `NullPointerException`.
- If a `proxy` is provided, it must be a valid `java.net.Proxy` instance.
- Always verify that the target environment has network access, as this class is intended for remote resource retrieval.

### Prompt Snippet
```text
URLImageSource source = new URLImageSource(url, proxy);
BufferedImage image = source.read();
```

### Common Failure Modes
- `NullPointerException`: Thrown if the `url` or `proxy` argument is passed as `null`.
- `IOException`: Occurs if the network connection fails, the URL is unreachable, or the content at the URL is not a valid image format supported by `ImageIO`.
- `IllegalArgumentException`: May occur if the provided string is not a valid URL format.

### Fix Code Hint
```java
// Ensure the URL is valid and the proxy is initialized before instantiation
try {
    URL url = new URL("http://example.com/image.jpg");
    URLImageSource source = new URLImageSource(url, Proxy.NO_PROXY);
    BufferedImage img = source.read();
} catch (MalformedURLException e) {
    // Handle invalid URL format
} catch (IOException e) {
    // Handle network or decoding errors
}
```

## API Test: `UnsupportedFormatException`

### Signature
```java
public UnsupportedFormatException(String formatName, String s)
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/UnsupportedFormatException.java_

_Source doc:_ Instantiates a {@link UnsupportedFormatException} with the unsupported format and a detailed message. @param formatName	Format name. @param s				A message detailing the exception.

### Goal
Signals that an image processing task was attempted with an image format that is not supported by the underlying Java Image I/O API.

### Parameters
- `formatName` (`String`): The name of the image format that caused the exception (e.g., "bmp", "tiff").
- `s` (`String`): A descriptive message providing additional context regarding the failure.

### Input
The caller must provide a `String` representing the unsupported format name and a `String` containing the error details. This exception is typically thrown when the library encounters an input source or requested output format that the host JVM's `ImageIO` cannot process.

### Output
Returns `null` (this is a constructor for an Exception class).

### Valid Call Patterns
```java
// Inferred from signature:
throw new UnsupportedFormatException("tiff", "The tiff format is not supported by the current ImageIO configuration.");
```

### LLM Instruction Prompt
- Use this exception when the library detects an image format that is incompatible with the host JVM's `ImageIO` capabilities.
- Always provide the specific `formatName` that triggered the failure to assist in debugging environment-specific format support.

### Prompt Snippet
```text
new UnsupportedFormatException(formatName, message)
```

### Common Failure Modes
- **Environment Mismatch:** The host JVM lacks the necessary `ImageIO` plugins to read or write the requested format.
- **Format Detection Failure:** Attempting to process a file where the format cannot be determined, often resulting in an empty or null format string being passed to the constructor.

### Fix Code Hint
```java
try {
    Thumbnails.of("image.unknown").size(100, 100).toFile("out.jpg");
} catch (UnsupportedFormatException e) {
    // Verify if the format is supported by the current JVM ImageIO
    System.err.println("Format not supported: " + e.getMessage());
}
```

## API Test: `Watermark`

### Signature
```java
public Watermark(Position position, BufferedImage watermarkImg, float opacity, int insets)
public Watermark(Position position, BufferedImage watermarkImg, float opacity)
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Watermark.java:76  (+1 more definition site/overload)_

_Source doc:_ Instantiates a filter which applies a watermark to an image. @param position			The position of the watermark. @param watermarkImg		The watermark image. @param opacity			The opacity of the watermark. <p> The value should be between {@code 0.0f} and {@code 1.0f}, where {@code 0.0f} is completely transparent, and {@code 1.0f} is completely opaque. @param insets			Inset size around the watermark. Cannot be negative.

### Goal
Applies a secondary `BufferedImage` as a watermark overlay onto a target image at a specified geometric position with configurable transparency and padding.

### Parameters
- `position` (`Position`): An implementation of the `net.coobird.thumbnailator.geometry.Position` interface (e.g., `Positions.CENTER`, `Positions.BOTTOM_RIGHT`) defining where the watermark is placed.
- `watermarkImg` (`BufferedImage`): The source image to be used as the watermark overlay.
- `opacity` (`float`): A value between `0.0f` (completely transparent) and `1.0f` (completely opaque).
- `insets` (`int`): The size of the margin in pixels to be applied around the watermark; must be a non-negative integer.

### Input
- **Data:** Requires a valid `BufferedImage` for the watermark overlay.
- **Preconditions:** The watermark image must be loaded into memory. The `position` must be a valid `Position` object from the `net.coobird.thumbnailator.geometry` package.

### Output
Returns an instance of `Watermark`, which implements `ImageFilter` and can be passed to the `addFilter` method of a `ThumbnailParameter` or used within the fluent `Thumbnails` pipeline.

### Valid Call Patterns
```java
// Using the 4-argument constructor
ImageFilter filter = new Watermark(Positions.BOTTOM_RIGHT, watermarkImage, 0.5f, 10);

// Using the 3-argument constructor (inferred from test suite)
Watermark watermark = new Watermark(position, WATERMARK_IMAGE, 1f);
```

### LLM Instruction Prompt
- When using `Watermark`, ensure the `opacity` is a `float` between `0.0f` and `1.0f`.
- Always use `net.coobird.thumbnailator.geometry.Positions` constants for the `position` parameter.
- If using the 4-argument constructor, ensure the `insets` value is non-negative.

### Prompt Snippet
```text
new Watermark(Positions.BOTTOM_RIGHT, watermarkImage, 0.5f, 10)
```

### Common Failure Modes
- **Negative Insets:** Providing a negative integer for the `insets` parameter will result in an `IllegalArgumentException`.
- **Opacity Out of Bounds:** Providing an `opacity` value outside the `0.0f` to `1.0f` range may lead to unexpected rendering or runtime exceptions.
- **Null Images:** Passing a `null` `watermarkImg` will cause a `NullPointerException` when the filter is applied.

### Fix Code Hint
```java
// Ensure insets are non-negative and opacity is within [0.0, 1.0]
int safeInsets = Math.max(0, requestedInsets);
float safeOpacity = Math.max(0.0f, Math.min(1.0f, requestedOpacity));

Watermark watermark = new Watermark(Positions.BOTTOM_RIGHT, watermarkImg, safeOpacity, safeInsets);
```

## API Test: `add`

### Signature
```java
public void add(ImageFilter filter)
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Pipeline.java:99_

_Source doc:_ Adds an {@code ImageFilter} to the pipeline. @param filter		An {@code ImageFilter}.

### Goal
Registers an `ImageFilter` to the `Pipeline` to be applied sequentially to a `BufferedImage` during the thumbnail processing task.

### Parameters
- `filter` (`ImageFilter`): An implementation of the `ImageFilter` interface (e.g., `Watermark`, `Caption`, `Flip`, or `Rotation`) that defines a specific image transformation.

### Input
The caller must provide a valid `ImageFilter` object. The `Pipeline` expects that the `BufferedImage` passed to its `apply` method is non-null and compatible with the registered filters.

### Output
Returns `void` — The method modifies the internal state of the `Pipeline` instance by appending the provided filter to the processing queue.

### Valid Call Patterns
```java
Pipeline pipeline = new Pipeline();
ImageFilter filter1 = new SomeImageFilter();
pipeline.add(filter1);
```

### LLM Instruction Prompt
- When using `add`, ensure the `Pipeline` instance is initialized before adding filters.
- Multiple filters can be added to the same `Pipeline` instance; they will be executed in the order they were added.
- Ensure the `ImageFilter` implementation is compatible with the `BufferedImage` type being processed.

### Prompt Snippet
```text
// Adding a filter to a pipeline
pipeline.add(myImageFilter);
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `filter` argument passed to `add` is `null`.
- **Incompatible Image Type**: If the added `ImageFilter` requires a specific `BufferedImage` type (e.g., `TYPE_INT_ARGB`) and the input image does not match, the filter may throw an exception or produce unexpected visual artifacts during `pipeline.apply()`.

### Fix Code Hint
```java
// Ensure the filter is not null before adding
if (filter != null) {
    pipeline.add(filter);
}
```

## API Test: `addAll`

### Signature
```java
public void addAll(List<ImageFilter> filters)
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Pipeline.java:125_

_Source doc:_ Adds a {@code List} of {@code ImageFilter}s to the pipeline. @param filters			A list of filters to add to the pipeline.

### Goal
Appends a collection of `ImageFilter` objects to the current image processing pipeline to be applied sequentially to a `BufferedImage`.

### Parameters
- `filters` (`List<ImageFilter>`): A list of `ImageFilter` implementations (such as `Watermark`, `Caption`, `Flip`, or `Rotation`) to be processed by the pipeline.

### Input
The caller must provide a `List` containing valid `ImageFilter` objects. The pipeline expects these filters to be compatible with the `BufferedImage` being processed. Preconditions include ensuring the `Pipeline` instance is initialized and that the filters are ready for application via the `apply(BufferedImage)` method.

### Output
Returns `void` — The method modifies the internal state of the `Pipeline` object by appending the provided filters to its execution queue.

### Valid Call Patterns
```java
Pipeline pipeline = new Pipeline();
List<ImageFilter> filterList = Arrays.asList(filter1, filter2);
pipeline.addAll(filterList);
```

### LLM Instruction Prompt
- When using `addAll`, ensure the `List<ImageFilter>` is not null.
- The filters are applied in the order they appear in the list.
- Ensure the `Pipeline` instance is properly instantiated before calling `addAll`.

### Prompt Snippet
```text
// Example of adding filters to a pipeline
List<ImageFilter> myFilters = new ArrayList<>();
myFilters.add(new Flip(Flip.HORIZONTAL));
pipeline.addAll(myFilters);
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `filters` list passed to the method is null.
- **Unsupported Filter Type**: Attempting to add an object that does not implement `ImageFilter`.
- **Pipeline State**: Calling `addAll` on a pipeline that has already been applied to an image may lead to unexpected results if the pipeline is intended to be immutable or single-use.

### Fix Code Hint
```java
// Ensure the list is initialized and contains valid filters
List<ImageFilter> filters = new ArrayList<ImageFilter>();
filters.add(new Rotation(90));
if (pipeline != null) {
    pipeline.addAll(filters);
}
```

## API Test: `addFilter`

### Signature
```java
public Builder<T> addFilter(ImageFilter filter)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1993_

_Source doc:_ Adds a {@link ImageFilter} to apply to the thumbnail. <p> This method can be called multiple times to apply multiple filters. <p> If multiple filters are to be applied, the filters will be applied in the order that this method is called. <p> Calling this method to set this parameter is optional. @param filter		An image filter to apply to the thumbnail. @return				Reference to this object.

### Goal
Applies a custom or library-provided `ImageFilter` to the thumbnail during the processing pipeline to perform operations such as orientation correction, watermarking, or color manipulation.

### Parameters
- `filter` (`ImageFilter`): An implementation of the `ImageFilter` interface that defines the transformation to be applied to the `BufferedImage`.

### Input
- **Data:** A `BufferedImage` source provided via the `Thumbnails.of()` builder.
- **Preconditions:** The input image must be successfully loaded into memory. If using filters like `ExifFilterUtils`, the input must be a `BufferedImage` that retains metadata or has been processed to account for orientation.
- **Formats:** Supports any image format compatible with the host JVM's `ImageIO` capabilities.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further chaining of pipeline operations (e.g., `.size()`, `.rotate()`, or `.toFile()`).

### Valid Call Patterns
```java
// Applying an orientation filter to a BufferedImage
BufferedImage result = Thumbnails.of(sourceImage)
    .addFilter(ExifFilterUtils.getFilterForOrientation(Orientation.typeOf(orientation)))
    .size(width, height)
    .asBufferedImage();
```

### LLM Instruction Prompt
- When using `addFilter`, ensure the filter is applied after the builder is initialized via `Thumbnails.of()`.
- If multiple filters are required, chain multiple `.addFilter()` calls; the library will execute them in the exact order they are invoked in the fluent pipeline.
- Ensure the `ImageFilter` implementation is compatible with the `BufferedImage` type being processed.

### Prompt Snippet
```text
.addFilter(new YourImageFilterImplementation())
```

### Common Failure Modes
- **NullPointerException:** Passing a `null` filter to the method.
- **Ordering Issues:** Applying a filter that expects specific image dimensions or color models before the image has been resized or converted.
- **Unsupported Formats:** Attempting to apply filters to images that failed to load due to `ImageIO` format limitations.

### Fix Code Hint
```java
// Ensure the filter is not null and applied in the correct sequence
if (myFilter != null) {
    builder.addFilter(myFilter);
}
```

## API Test: `addFilters`

### Signature
```java
public Builder<T> addFilters(List<ImageFilter> filters)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2016_

_Source doc:_ Adds multiple {@link ImageFilter}s to apply to the thumbnail. <p> This method can be called multiple times to apply multiple filters. <p> If multiple filters are to be applied, the filters will be applied in the order that this method is called. <p> Calling this method to set this parameter is optional. @param filters		A list of filters to apply to the thumbnail. @return				Reference to this object.

### Goal
Applies a sequence of custom `ImageFilter` implementations to the thumbnail pipeline to perform complex image transformations.

### Parameters
- `filters` (`List<ImageFilter>`): A list of objects implementing the `ImageFilter` interface, which will be applied to the image in the order they appear in the list.

### Input
- **Data:** A `List` containing one or more `ImageFilter` instances.
- **Preconditions:** The `Thumbnails.Builder` must be initialized via `Thumbnails.of(...)`. Filters are applied to the image currently held in the pipeline. Ensure that the `ImageFilter` implementations are compatible with the `BufferedImage` type being processed.

### Output
Returns `Builder<T>` — A reference to the current `Builder` instance, allowing for further chaining of pipeline operations.

### Valid Call Patterns
```java
// Inferred from signature: Applying a list of custom filters to a pipeline
List<ImageFilter> myFilters = new ArrayList<ImageFilter>();
myFilters.add(new MyCustomFilter());

Thumbnails.of("image.jpg")
    .size(200, 200)
    .addFilters(myFilters)
    .toFile("thumbnail.jpg");
```

### LLM Instruction Prompt
- When using `addFilters`, ensure the `List<ImageFilter>` is instantiated and populated before passing it to the builder.
- Remember that filters are applied in the order of the list provided, and if `addFilters` is called multiple times, the filters from subsequent calls are appended to the existing filter chain.

### Prompt Snippet
```text
.addFilters(Arrays.asList(filter1, filter2))
```

### Common Failure Modes
- **NullPointerException:** Passing a `null` list to the `addFilters` method.
- **Ordering Issues:** Assuming filters are applied in a different order than the list sequence or the order of method calls.
- **Incompatible Filters:** Providing filters that perform operations not supported by the current `BufferedImage` color model or type.

### Fix Code Hint
```java
// Ensure the list is not null and contains valid ImageFilter instances
List<ImageFilter> filters = new ArrayList<ImageFilter>();
filters.add(new SomeImageFilter());
builder.addFilters(filters);
```

## API Test: `addFirst`

### Signature
```java
public void addFirst(ImageFilter filter)
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Pipeline.java:112_

_Source doc:_ Adds an {@code ImageFilter} to the beginning of the pipeline. @param filter		An {@code ImageFilter}.

### Goal
Prepends an `ImageFilter` to the current processing pipeline, ensuring the filter is executed before any previously added filters.

### Parameters
- `filter` (`ImageFilter`): An implementation of the `ImageFilter` interface that defines a specific image transformation to be applied to the `BufferedImage`.

### Input
- **Data:** A `BufferedImage` object is required when the pipeline is eventually executed.
- **Preconditions:** The `ImageFilter` must be a valid implementation (e.g., `Watermark`, `Caption`, `Flip`, or `Rotation`). The pipeline must be initialized before calling this method.

### Output
Returns `void` — This method modifies the internal state of the `Pipeline` object by updating the order of the filter chain.

### Valid Call Patterns
```java
Pipeline pipeline = new Pipeline();
pipeline.addFirst(new Watermark(Positions.CENTER, watermarkImage, 0.5f));
```

### LLM Instruction Prompt
- When composing a `Pipeline`, use `addFirst` to ensure a specific transformation (like a color filter or orientation fix) occurs before subsequent operations.
- Always ensure the `ImageFilter` implementation is compatible with the `BufferedImage` type being processed.

### Prompt Snippet
```text
// Example of adding a filter to the start of a pipeline
pipeline.addFirst(filterInstance);
```

### Common Failure Modes
- **NullPointerException:** Passing a `null` filter to the method will result in an exception during pipeline execution.
- **Pipeline Order Confusion:** Developers may incorrectly assume the order of operations; remember that `addFirst` shifts the execution priority to the very beginning of the chain, regardless of when `add` was called.
- **Incompatible Image Types:** Applying a filter that expects a specific color model (e.g., `TYPE_INT_ARGB`) to an incompatible `BufferedImage` may cause runtime errors or unexpected visual artifacts.

### Fix Code Hint
```java
// Ensure the filter is instantiated before adding to the pipeline
ImageFilter myFilter = new MyCustomFilter();
pipeline.addFirst(myFilter);
```

## API Test: `allowOverwrite`

### Signature
```java
public Builder<T> allowOverwrite(boolean allowOverwrite)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1265_

_Source doc:_ Specifies whether or not to overwrite files which already exist if they have been specified as destination files. <p> This method will change the output behavior of the following methods: <ul> <li>{@link #toFile(File)}</li> <li>{@link #toFile(String)}</li> <li>{@link #toFiles(Iterable)}</li> <li>{@link #toFiles(Rename)}</li> <li>{@link #asFiles(Iterable)}</li> <li>{@link #asFiles(Rename)}</li> </ul> The behavior of methods which are not listed above will not be affected by calling this method. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. @param allowOverwrite	If {@code true} then existing files will be overwritten if specified as a destination. If {@code false}, then the existing files will not be altered. For specific behavior, please refer to the specific output methods listed above. @return					Reference to this object. @since 	0.3.7

### Goal
Configures the thumbnail pipeline to permit or prevent the overwriting of existing destination files during file-based output operations.

### Parameters
- `allowOverwrite` (`boolean`): Set to `true` to permit the library to overwrite existing files at the destination path; set to `false` to prevent modification of existing files.

### Input
The caller must provide a boolean value. This method is only applicable when using file-based output methods (`toFile`, `toFiles`, `asFiles`). It does not affect stream-based or in-memory `BufferedImage` output.

### Output
Returns `Builder<T>` — The current `Thumbnails.Builder` instance, allowing for further method chaining in the fluent pipeline.

### Valid Call Patterns
```java
Thumbnails.of(originalFile)
    .size(50, 50)
    .allowOverwrite(true)
    .toFiles(Collections.singletonList(outputFile));
```

### LLM Instruction Prompt
- When generating code that writes thumbnails to the filesystem, always check if `allowOverwrite` is required to handle potential collisions.
- Ensure `allowOverwrite` is called exactly once per builder instance to avoid `IllegalStateException`.
- Only apply this method when the pipeline terminates in a file-based output method.

### Prompt Snippet
```text
.allowOverwrite(true)
```

### Common Failure Modes
- **IllegalStateException**: Occurs if `allowOverwrite` is invoked more than once on the same `Builder` instance.
- **Silent Failure/No-op**: If `allowOverwrite` is set to `false` (or omitted) and the destination file already exists, the library will not overwrite the file, which may lead to unexpected results if the developer assumes the file was updated.
- **Misapplication**: Calling this method when outputting to `OutputStream` or `asBufferedImage()`, where the setting has no effect.

### Fix Code Hint
```java
// Ensure only one call to allowOverwrite exists in the chain
Thumbnails.of(source)
    .size(100, 100)
    .allowOverwrite(true) // Call this once
    .toFile("output.jpg");
```

## API Test: `alphaInterpolation`

### Signature
```java
public Builder<T> alphaInterpolation(AlphaInterpolation config)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1379_

_Source doc:_ Sets the alpha interpolation mode when performing the resizing operation to generate the thumbnail. <p> Calling this method to set this parameter is optional. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. <p> This method cannot be called in conjunction with the {@link #resizerFactory(ResizerFactory)} method. @param config		The alpha interpolation mode. @return				Reference to this object.

### Goal
Configures the rendering hint for alpha channel interpolation during the image resizing process to balance between processing speed and visual quality.

### Parameters
- `config` (`AlphaInterpolation`): The interpolation mode to apply; typically `AlphaInterpolation.SPEED` or `AlphaInterpolation.QUALITY`.

### Input
- **Data:** Requires a `Thumbnails.Builder` instance initialized via `Thumbnails.of(...)`.
- **Preconditions:** The input must be a valid image format supported by the JVM's `ImageIO`. This method must not be called if `resizerFactory(ResizerFactory)` has already been invoked in the same pipeline.

### Output
Returns `Builder<T>` — A reference to the current `Builder` instance, allowing for further method chaining in the fluent pipeline.

### Valid Call Patterns
```java
BufferedImage thumbnail = Thumbnails.of(img)
    .size(50, 50)
    .alphaInterpolation(AlphaInterpolation.SPEED)
    .asBufferedImage();
```

### LLM Instruction Prompt
- When using `alphaInterpolation`, ensure it is called only once per builder instance.
- Do not use this method if the pipeline already includes a `resizerFactory` call, as these are mutually exclusive.
- Always provide a non-null `AlphaInterpolation` constant to avoid a `NullPointerException`.

### Prompt Snippet
```text
.alphaInterpolation(AlphaInterpolation.QUALITY)
```

### Common Failure Modes
- **`IllegalStateException`**: Thrown if `alphaInterpolation` is called more than once on the same builder.
- **`IllegalStateException`**: Thrown if `alphaInterpolation` is called after `resizerFactory`.
- **`NullPointerException`**: Thrown if the `config` argument passed is `null`.

### Fix Code Hint
```java
// Ensure mutual exclusivity: remove resizerFactory if using alphaInterpolation
Thumbnails.of(source)
    .size(100, 100)
    .alphaInterpolation(AlphaInterpolation.QUALITY) // Correct
    .toFile("output.jpg");
```

## API Test: `antialiasing`

### Signature
```java
public Builder<T> antialiasing(Antialiasing config)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1425_

_Source doc:_ Sets the antialiasing mode when performing the resizing operation to generate the thumbnail. <p> Calling this method to set this parameter is optional. <p> Calling this method multiple times will result in an {@link IllegalStateException}. <p> This method cannot be called in conjunction with the {@link #resizerFactory(ResizerFactory)} method. @param config		The antialiasing mode. @return				Reference to this object.

### Goal
Configures the antialiasing rendering hint used during the image resizing process to improve visual quality and reduce aliasing artifacts.

### Parameters
- `config` (`Antialiasing`): The specific antialiasing mode to apply (e.g., `Antialiasing.DEFAULT`, `Antialiasing.ON`, or `Antialiasing.OFF`).

### Input
The caller must provide a valid `Antialiasing` enum constant. The input image must be a valid `BufferedImage` or a source supported by `ImageIO` (File, InputStream, or URL). Precondition: The pipeline must not have already called `resizerFactory(ResizerFactory)`.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further fluent method chaining.

### Valid Call Patterns
```java
BufferedImage thumbnail = Thumbnails.of(img)
        .size(50, 50)
        .antialiasing(Antialiasing.DEFAULT)
        .asBufferedImage();
```

### LLM Instruction Prompt
- When using `antialiasing`, ensure it is called only once per builder instance.
- Do not use `antialiasing` if the pipeline already includes a call to `resizerFactory`.
- Always pass a non-null `Antialiasing` enum value to avoid `NullPointerException`.

### Prompt Snippet
```text
.antialiasing(Antialiasing.ON)
```

### Common Failure Modes
- `IllegalStateException`: Thrown if `antialiasing` is called more than once on the same builder instance.
- `IllegalStateException`: Thrown if `antialiasing` is called after `resizerFactory` has been invoked.
- `NullPointerException`: Thrown if the `config` argument is `null`.

### Fix Code Hint
```java
// Ensure only one configuration method is used and avoid nulls
Thumbnails.of(source)
    .size(100, 100)
    .antialiasing(Antialiasing.ON) // Call once, do not mix with resizerFactory
    .toFile("output.jpg");
```

## API Test: `apply`

### Signature
```java
public String apply(String name, ThumbnailParameter param)
public String apply(String fileName, ThumbnailParameter param)
public abstract String apply(String name, ThumbnailParameter param)
public BufferedImage apply(BufferedImage img)
```
_Source: source/src/main/java/net/coobird/thumbnailator/name/Rename.java:51  (+16 more definition site/overload)_

### Goal
Applies a naming transformation strategy to a file name based on the provided thumbnail creation parameters.

### Parameters
- `name` (`String`): The original file name to be transformed. Note: The file name should not include the directory path in which the file resides.
- `param` (`ThumbnailParameter`): The configuration parameters used to create the thumbnail, which may influence the naming logic.

### Input
- **Data:** A `String` representing the source file name and a `ThumbnailParameter` object.
- **Preconditions:** The `name` must represent a valid file name without directory components. The `ThumbnailParameter` must be initialized according to the desired thumbnail output settings.

### Output
Returns `String` — The transformed file name after the `Rename` function has been applied.

### Valid Call Patterns
```java
Rename customRenamer = new Rename() {
    @Override
    public String apply(String name, ThumbnailParameter param) {
        return "thumb_" + name;
    }
};
String newName = customRenamer.apply("image.jpg", new ThumbnailParameter());
```

### LLM Instruction Prompt
- When implementing a custom `Rename` strategy, always override the `apply(String, ThumbnailParameter)` method.
- Ensure the logic does not include directory paths in the returned `String`.
- Use the `ThumbnailParameter` object if the naming convention needs to be conditional based on output format or quality settings.

### Prompt Snippet
```text
// Example of implementing a custom Rename strategy
new Rename() {
    @Override
    public String apply(String name, ThumbnailParameter param) {
        return "prefix-" + name;
    }
}
```

### Common Failure Modes
- **Path Inclusion:** Including directory paths in the `name` argument, which violates the requirement that the name should not include the directory.
- **Unsupported Formats:** If the `Rename` logic generates an extension that the underlying `ImageIO` cannot write (e.g., forcing a `.foobar` extension), the thumbnail creation process will fail with an `UnsupportedFormatException`.
- **Null Parameters:** Passing a `null` `ThumbnailParameter` if the custom implementation logic attempts to access its properties (e.g., `param.getOutputFormat()`).

### Fix Code Hint
```java
// Ensure the returned string is a valid filename and does not contain path separators
@Override
public String apply(String name, ThumbnailParameter param) {
    String baseName = new File(name).getName();
    return "thumb_" + baseName;
}
```

## API Test: `asBufferedImage`

### Signature
```java
public BufferedImage asBufferedImage()
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2342_

_Source doc:_ <p> Creates a thumbnail and returns it as a {@link BufferedImage}. </p> <p> To call this method, the thumbnail must have been created from a single source. </p> <p> If you intend to write the {@link BufferedImage} to a file, you're strongly encouraged to use one of the {@code toFile(...)} methods instead. See "Notes on image types" for details. </p> <p><strong>Notes on image types</strong></p> <p> The image type of the {@link BufferedImage} depends on the type of the input image. (Specifically, the type emitted by the {@link javax.imageio.ImageReader} associated for the file type.) Thumbnailator will attempt to use the same image type as the input if it is not specified through {@link #imageType(int)}. </p> <p> This has implications when writing {@code BufferedImage} to files using the default JPEG writer bundled with Java. There are issues with the JPEG writer <a href="https://bugs.openjdk.java.net/browse/JDK-8041459">outputting wrong colors</a> or <a href="https://bugs.openjdk.java.net/browse/JDK-8204188">throwing exceptions</a> when writing a image containing an alpha channel. </p> <p> If outputting to a file, consider using one of the {@link #toFile(File)} or {@link #toFile(String)} methods instead, as it contains workarounds to prevent issues mentioned in the previous paragraph. </p> <p> For more information on {@code BufferedImage} types, refer to {@link BufferedImage#getType()}. </p> @return		A thumbnail as a {@link BufferedImage}. @throws IOException					If a problem occurred during the reading of the original image. @throws IllegalArgumentException		If multiple original images are specified.

### Goal
Generates a thumbnail from a single source image and returns the result as an in-memory `BufferedImage` object.

### Parameters
_None._

### Input
The pipeline must be initialized with a single source (e.g., `File`, `InputStream`, `URL`, or `BufferedImage`). The input must be a valid image format supported by the host JVM's `ImageIO`.

### Output
Returns `BufferedImage` — A processed thumbnail image. The internal image type defaults to the source image's type unless explicitly overridden via the `.imageType(int)` builder method.

### Valid Call Patterns
```java
// Basic in-memory processing
BufferedImage thumbnail = Thumbnails.of(sourceImage)
    .size(160, 160)
    .rotate(90)
    .asBufferedImage();

// With explicit output format
BufferedImage thumbnail = Thumbnails.of(img)
    .size(100, 100)
    .outputFormat("png")
    .asBufferedImage();
```

### LLM Instruction Prompt
- Always ensure the `Thumbnails` builder is initialized with a single source before calling `asBufferedImage()`.
- If the resulting `BufferedImage` is intended to be saved to a file, warn the user to prefer `toFile()` methods to avoid known Java JPEG writer issues with alpha channels.
- Do not use this method if the pipeline is configured for batch processing (multiple sources), as it will throw an `IllegalArgumentException`.

### Prompt Snippet
```text
// Ensure single source input
Thumbnails.of(source)
    .size(w, h)
    .asBufferedImage();
```

### Common Failure Modes
- `IllegalArgumentException`: Occurs if the builder was initialized with multiple source images (e.g., a directory or list of files).
- `IOException`: Occurs if the source image cannot be read or is corrupted.
- Color/Exception issues: Occurs when writing the returned `BufferedImage` to a JPEG file if the image contains an alpha channel, due to limitations in the default Java JPEG writer.

### Fix Code Hint
```java
// Instead of:
BufferedImage img = Thumbnails.of(files).asBufferedImage(); // Fails for multiple files

// Use:
Thumbnails.of(files).toFiles(Rename.PREFIX_DOT_THUMBNAIL); // Use batch output for multiple sources
```

## API Test: `asBufferedImages`

### Signature
```java
public List<BufferedImage> asBufferedImages()
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2277_

_Source doc:_ <p> Create the thumbnails and return as a {@link List} of {@link BufferedImage}s. </p> <p> If you intend to write these {@link BufferedImage}s to files, you're strongly encouraged to use one of the {@code toFiles(...)} or {@code asFiles(...)} methods instead. See "Notes on image types" for details. </p> <p><strong>Notes on image types</strong></p> <p> The image type of the {@link BufferedImage} depends on the type of the input image. (Specifically, the type emitted by the {@link javax.imageio.ImageReader} associated for the file type.) Thumbnailator will attempt to use the same image type as the input if it is not specified through {@link #imageType(int)}. </p> <p> This has implications when writing {@code BufferedImage} to files using the default JPEG writer bundled with Java. There are issues with the JPEG writer <a href="https://bugs.openjdk.java.net/browse/JDK-8041459">outputting wrong colors</a> or <a href="https://bugs.openjdk.java.net/browse/JDK-8204188">throwing exceptions</a> when writing a image containing an alpha channel. </p> <p> If outputting to a file, consider using one of the {@link #toFiles(Iterable)}, {@link #toFiles(Rename)}, {@link #toFiles(File, Rename)} {@link #asFiles(Iterable)}, {@link #asFiles(Rename)} or {@link #asFiles(File, Rename)} methods instead, as it contains workarounds to prevent issues mentioned in the previous paragraph. </p> <p> For more information on {@code BufferedImage} types, refer to {@link BufferedImage#getType()}. </p> <p><strong>Note about performance</strong></p> <p> This method will process all inputs and create the corresponding thumbnails at once before returning them as in a list. Therefore, if many thumbnails are generated at once, it is possible that the Java virtual machine's heap space will run out and an {@link OutOfMemoryError} could be thrown. </p> <p> If many thumbnails are being processed at once, then using the {@link #iterableBufferedImages()} method would be preferable. </p> @return		A list of thumbnails. @throws IOException					If a problem occurred during the reading of the original images.

### Goal
Processes all input images through the defined pipeline and returns the resulting thumbnails as a `List` of `BufferedImage` objects.

### Parameters
_None._

### Input
The pipeline must be initialized with valid input sources (e.g., `File`, `InputStream`, `URL`, or `BufferedImage`) supported by the JVM's `ImageIO`. Preconditions include ensuring the input images are readable and that the pipeline operations (size, crop, rotate, etc.) are configured.

### Output
Returns `List<BufferedImage>` — A list containing the generated thumbnails. The `BufferedImage` type defaults to the input image's type unless explicitly overridden via `imageType(int)`.

### Valid Call Patterns
```java
// Basic usage with a single BufferedImage
List<BufferedImage> thumbnails = Thumbnails.of(img)
    .size(100, 100)
    .asBufferedImages();

// Usage with explicit output format
List<BufferedImage> thumbnails = Thumbnails.of(img)
    .size(100, 100)
    .outputFormat("png")
    .asBufferedImages();
```

### LLM Instruction Prompt
- Always use `asBufferedImages()` only when the resulting `BufferedImage` objects are needed for further in-memory processing.
- If the goal is to save thumbnails to disk, instruct the user to use `toFiles(...)` or `asFiles(...)` instead to avoid known Java JPEG writer issues with alpha channels.
- Warn the user about `OutOfMemoryError` risks when processing large batches of images.

### Prompt Snippet
```text
// Use asBufferedImages() for in-memory manipulation
List<BufferedImage> results = Thumbnails.of(source).size(w, h).asBufferedImages();
```

### Common Failure Modes
- `OutOfMemoryError`: Occurs when processing a large number of images simultaneously, as all thumbnails are generated and stored in memory before the list is returned.
- `IOException`: Thrown if the input image cannot be read or is in an unsupported format.
- Color/Exception issues: Occurs when writing the resulting `BufferedImage` to a JPEG file if the image contains an alpha channel (due to limitations in the default Java JPEG writer).

### Fix Code Hint
```java
// If memory is an issue, use iterableBufferedImages() instead:
Iterable<BufferedImage> thumbnails = Thumbnails.of(files)
    .size(100, 100)
    .iterableBufferedImages();

// If writing to files, avoid asBufferedImages() and use:
Thumbnails.of(files)
    .size(100, 100)
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

## API Test: `asFiles`

### Signature
```java
public List<File> asFiles(Iterable<File> iterable)
public List<File> asFiles(Rename rename)
public List<File> asFiles(File destinationDir, Rename rename)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2519  (+2 more definition site/overload)_

_Source doc:_ Creates thumbnails and stores them to files in the directory specified by the given {@link File} object, and using the {@link Rename} function to determine the filenames. The thubnail files are returned as a {@link List}. <p> When the destination file exists, and overwriting files has been disabled by calling the {@link #allowOverwrite(boolean)} method with {@code false}, then the thumbnail with the destination file already existing will not be written and the corresponding {@code File} object will not be included in the {@code List} returned by this method. <p> Extra caution should be taken when using this method, as there are no protections in place to prevent file name collisions resulting from creating thumbnails from files in separate directories but having the same name. In such a case, the behavior will be depend on the behavior of the {@link #allowOverwrite(boolean)} as described in the previous paragraph. <p> To call this method, the thumbnails must have been creates from files by calling the {@link Thumbnails#of(File...)} method. @param destinationDir	The destination directory to which the thumbnails should be written to. @param rename			The rename function which is used to determine the filenames of the thumbnail files to write. @return					A list of {@link File}s of the thumbnails which were created. @throws IOException		If a problem occurs while reading the original images or writing the thumbnails to files. @throws IllegalStateException		If the original images are not from files. @throws IllegalArgumentException		If the destination directory is not a directory. @since 	0.4.7

### Goal
Processes a batch of images through the Thumbnailator pipeline and writes the resulting thumbnails to the filesystem using specified naming conventions or destination directories.

### Parameters
- `destinationDir` (`File`): The target directory where the generated thumbnail files will be saved. Must be a valid directory.
- `rename` (`Rename`): A naming strategy (e.g., `Rename.PREFIX_DOT_THUMBNAIL`) used to generate the output filename based on the original source file.

### Input
- **Preconditions:** The pipeline must be initialized using `Thumbnails.of(File...)` or `Thumbnails.fromFiles(...)`.
- **Formats:** Supports image formats compatible with the host JVM's `ImageIO` capabilities.
- **Constraint:** If using `asFiles(File, Rename)`, the input source must be files; otherwise, an `IllegalStateException` is thrown.

### Output
Returns `List<File>` — A list containing the `File` objects of the successfully created thumbnails. If overwriting is disabled and a file exists, that file is omitted from the returned list.

### Valid Call Patterns
```java
// Using a Rename strategy
List<File> results = Thumbnails.fromFiles(getFileSources())
    .size(100, 100)
    .asFiles(Rename.PREFIX_DOT_THUMBNAIL);

// Using explicit output files
List<File> results = Thumbnails.fromInputStreams(getSources())
    .size(100, 100)
    .asFiles(Arrays.asList(outFile1, outFile2));
```

### LLM Instruction Prompt
- Always ensure the pipeline is initialized with `Thumbnails.of(File...)` when using the `Rename` or `destinationDir` overloads.
- Use `allowOverwrite(boolean)` to explicitly define behavior when target files already exist.
- When using `asFiles(Iterable<File>)`, ensure the number of provided files matches the number of input images.

### Prompt Snippet
```text
// Example of batch processing with Rename
Thumbnails.of(files)
    .size(100, 100)
    .asFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

### Common Failure Modes
- `IllegalStateException`: Occurs if the pipeline was initialized with `InputStream` or `URL` sources but `asFiles(Rename)` is called.
- `IllegalArgumentException`: Occurs if the provided `destinationDir` is not a directory.
- `IOException`: Occurs if the JVM lacks write permissions or if the source image is corrupted/unreadable.
- File Name Collisions: Occurs when processing files from different directories that share the same name; behavior depends on `allowOverwrite` settings.

### Fix Code Hint
```java
// Ensure input is from files for Rename-based output
Thumbnails.of(new File("image.jpg"))
    .size(50, 50)
    .asFiles(new File("output/dir"), Rename.PREFIX_DOT_THUMBNAIL);
```

## API Test: `build`

### Signature
```java
public BufferedImage build()
public ThumbnailParameter build()
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/BufferedImageBuilder.java:110  (+1 more definition site/overload)_

_Source doc:_ Generates a new {@code BufferedImage}. @return		Returns a newly created {@link BufferedImage} from the parameters set in the {@link BufferedImageBuilder}.

### Goal
Generates a new `BufferedImage` or `ThumbnailParameter` instance based on the configuration previously defined within a builder object.

### Parameters
_None._

### Input
The caller must have previously initialized a `BufferedImageBuilder` with valid dimensions (width and height) and an image type (e.g., `BufferedImage.TYPE_INT_ARGB`). No external file or stream input is required for the `BufferedImageBuilder.build()` variant, as it constructs the image in-memory.

### Output
Returns `BufferedImage` — A newly created `BufferedImage` object initialized with the dimensions and image type specified during the builder's configuration.

### Valid Call Patterns
```java
BufferedImage img = new BufferedImageBuilder(200, 200, BufferedImage.TYPE_INT_ARGB).build();
```

### LLM Instruction Prompt
- When using `BufferedImageBuilder`, always call `build()` as the final step after specifying dimensions and image type.
- Ensure the `BufferedImageBuilder` is instantiated with valid integer dimensions; negative values or zero may result in an `IllegalArgumentException` during the underlying `BufferedImage` construction.

### Prompt Snippet
```text
BufferedImage img = new BufferedImageBuilder(width, height, type).build();
```

### Common Failure Modes
- **Invalid Image Type:** Providing an unsupported or invalid integer constant for the image type will cause the underlying `BufferedImage` constructor to throw an `IllegalArgumentException`.
- **Zero or Negative Dimensions:** Attempting to build an image with non-positive width or height will result in an `IllegalArgumentException`.
- **Memory Exhaustion:** Attempting to build extremely large `BufferedImage` objects may lead to an `OutOfMemoryError` in constrained headless environments.

### Fix Code Hint
```java
// Ensure dimensions are positive and type is a valid BufferedImage constant
BufferedImage img = new BufferedImageBuilder(200, 200, BufferedImage.TYPE_INT_ARGB).build();
```

## API Test: `calculate`

### Signature
```java
public Dimension calculate(int width, int height)
public Point calculate( int enclosingWidth, int enclosingHeight, int width, int height, int insetLeft, int insetRight, int insetTop, int insetBottom )
public Point calculate(int enclosingWidth, int enclosingHeight, int width, int height, int insetLeft, int insetRight, int insetTop, int insetBottom)
public Rectangle calculate( int outerWidth, int outerHeight, boolean flipHorizontal, boolean flipVertical, boolean swapDimensions )
```
_Source: source/src/main/java/net/coobird/thumbnailator/geometry/Position.java:58  (+14 more definition site/overload)_

_Source doc:_ Calculates the position of an object enclosed by an enclosing object. @param enclosingWidth		The width of the enclosing object that is to contain the enclosed object. @param enclosingHeight		The height of the enclosing object that is to contain the enclosed object. @param width					The width of the object that is to be placed inside an enclosing object. @param height				The height of the object that is to be placed inside an enclosing object. @param insetLeft				The inset on the left-hand side of the object to be enclosed. @param insetRight			The inset on the right-hand side of the object to be enclosed. @param insetTop				The inset on the top side of the object to be enclosed. @param insetBottom			The inset on the bottom side of the object to be enclosed. @return						The position to place the object.

### Goal
Calculates the coordinate position of an object within an enclosing container, accounting for specified padding (insets) to determine the final placement.

### Parameters
- `enclosingWidth` (`int`): The width of the container object.
- `enclosingHeight` (`int`): The height of the container object.
- `width` (`int`): The width of the object to be placed.
- `height` (`int`): The height of the object to be placed.
- `insetLeft` (`int`): The padding distance from the left edge.
- `insetRight` (`int`): The padding distance from the right edge.
- `insetTop` (`int`): The padding distance from the top edge.
- `insetBottom` (`int`): The padding distance from the bottom edge.

### Input
The method requires integer dimensions for both the container and the object, along with integer inset values. These values are typically derived from the `ThumbnailParameter` or the dimensions of the source/destination `BufferedImage` objects being processed in the pipeline.

### Output
Returns `Point` — The calculated `(x, y)` coordinates representing the top-left corner where the object should be positioned within the enclosing container.

### Valid Call Patterns
```java
// Inferred from signature: usage via a Position implementation
Point p = position.calculate(enclosingWidth, enclosingHeight, width, height, insetLeft, insetRight, insetTop, insetBottom);
```

### LLM Instruction Prompt
- When calculating positions for watermarks or crops, always use the `calculate` method provided by the `Position` interface implementations (e.g., `Positions.CENTER`).
- Ensure all integer inputs are non-negative, as negative dimensions or insets may lead to undefined behavior in the coordinate calculation.

### Prompt Snippet
```text
Point position = myPositionImpl.calculate(enclosingWidth, enclosingHeight, width, height, insetLeft, insetRight, insetTop, insetBottom);
```

### Common Failure Modes
- Passing negative values for dimensions or insets, which may result in invalid coordinate calculations outside the bounds of the enclosing object.
- Mismatching the `enclosing` dimensions with the actual `BufferedImage` dimensions, leading to incorrect placement of watermarks or cropped regions.

### Fix Code Hint
```java
// Ensure dimensions are validated before calculation
if (enclosingWidth >= width && enclosingHeight >= height) {
    Point p = position.calculate(enclosingWidth, enclosingHeight, width, height, 0, 0, 0, 0);
}
```

## API Test: `clear`

### Signature
```java
static void clear()
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/Configurations.java:130_

_Source doc:_ Clears the internal cache of properties. <p> This is intended only to be called from tests.

### Goal
Resets the internal property cache of the Thumbnailator library to ensure a clean state during test execution.

### Parameters
_None._

### Input
No input parameters are required. This method is intended for use exclusively within test environments to reset library-level configurations or cached properties.

### Output
Returns `void` — The method performs a state-clearing operation and returns no value.

### Valid Call Patterns
```java
// Inferred from signature:
net.coobird.thumbnailator.util.Configurations.clear();
```

### LLM Instruction Prompt
- Use `net.coobird.thumbnailator.util.Configurations.clear()` only when writing unit or integration tests for Thumbnailator.
- Do not use this method in production code, as it is intended solely for test-harness cleanup.

### Prompt Snippet
```text
net.coobird.thumbnailator.util.Configurations.clear();
```

### Common Failure Modes
- **Production Usage:** Calling this method in production code may lead to unexpected behavior or performance degradation by clearing necessary internal caches.
- **Visibility:** Depending on the specific package structure of the version being used, ensure the `net.coobird.thumbnailator.util.Configurations` class is accessible within the test classpath.

### Fix Code Hint
```java
// Ensure this is only called within a @Before or @After test lifecycle method
@After
public void tearDown() {
    net.coobird.thumbnailator.util.Configurations.clear();
}
```

## API Test: `copy`

### Signature
```java
public static BufferedImage copy(BufferedImage img)
public static BufferedImage copy(BufferedImage img, int imageType)
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/BufferedImages.java:63  (+1 more definition site/overload)_

_Source doc:_ Returns a {@link BufferedImage} with the specified image type, where the graphical content is a copy of the specified image. @param img		The image to copy. @param imageType	The image type for the image to return. @return			A copy of the specified image.

### Goal
Creates a deep copy of a `BufferedImage` to ensure that subsequent image processing operations do not mutate the original source image.

### Parameters
- `img` (`BufferedImage`): The source image to be copied.
- `imageType` (`int`): The `BufferedImage` type constant (e.g., `BufferedImage.TYPE_INT_ARGB`) for the resulting copy.

### Input
The caller must provide a valid `BufferedImage` object. When using the two-argument version, the `imageType` must be a valid constant defined in the `BufferedImage` class. This utility is typically used before applying destructive filters to preserve the integrity of the original image data.

### Output
Returns `BufferedImage` — A new `BufferedImage` instance containing the same graphical content as the source image, rendered in the specified `imageType`.

### Valid Call Patterns
```java
// Copying an image while preserving its original type
BufferedImage copyImage = BufferedImages.copy(originalImage);

// Copying an image and forcing a specific image type
BufferedImage copyImage = BufferedImages.copy(originalImage, BufferedImage.TYPE_INT_ARGB);
```

### LLM Instruction Prompt
- Use `BufferedImages.copy(img)` when you need a safe, independent copy of an image to prevent side effects during filter application.
- Always prefer this utility over manual `Graphics2D` drawing to ensure deterministic behavior across different JVM environments.
- Ensure the `imageType` provided is compatible with the intended downstream operations.

### Prompt Snippet
```text
// Use BufferedImages.copy to create a safe copy before applying filters
BufferedImage safeCopy = BufferedImages.copy(sourceImage);
filter.apply(safeCopy);
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `img` parameter is `null`.
- **IllegalArgumentException**: May occur if an invalid or unsupported `imageType` is provided to the `BufferedImage` constructor internally.
- **Memory Exhaustion**: Copying very large `BufferedImage` objects repeatedly in a loop can lead to `OutOfMemoryError` in memory-constrained headless environments.

### Fix Code Hint
```java
// Ensure the source image is not null before copying
if (originalImage != null) {
    BufferedImage safeCopy = BufferedImages.copy(originalImage);
    // Proceed with processing
}
```

## API Test: `createOutputStream`

### Signature
```java
OutputStream createOutputStream(File destinationFile)
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/FileImageSink.java:326_

### Goal
Creates and returns an `OutputStream` directed to the specified `File` to facilitate the writing of processed image data.

### Parameters
- `destinationFile` (`File`): The target file on the filesystem where the image data will be written.

### Input
- **Data:** A valid `File` object representing the path where the thumbnail should be saved.
- **Preconditions:** The caller must have appropriate filesystem write permissions for the specified path. The parent directory of the `destinationFile` must exist.

### Output
Returns `OutputStream` — An open `FileOutputStream` connected to the `destinationFile`, used by the `FileImageSink` to stream encoded image bytes.

### Valid Call Patterns
```java
// Inferred from FileImageSink test suite usage
File outputFile = new File("path/to/output.png");
FileImageSink sink = new FileImageSink(outputFile);
OutputStream os = sink.createOutputStream(outputFile);
```

### LLM Instruction Prompt
- Use `createOutputStream` only when implementing or extending custom `ImageSink` logic within the Thumbnailator framework.
- Ensure the `File` object passed is non-null and points to a writable location.
- Always ensure the returned `OutputStream` is properly closed after the write operation to prevent resource leaks.

### Prompt Snippet
```text
// Example of creating an output stream for a FileImageSink
File destination = new File("output.jpg");
FileImageSink sink = new FileImageSink(destination);
OutputStream os = sink.createOutputStream(destination);
```

### Common Failure Modes
- **`IOException`**: Occurs if the `destinationFile` is a directory, cannot be created, or if the process lacks write permissions.
- **Resource Leaks**: Failing to close the `OutputStream` after the image processing task completes can lead to file handle exhaustion.
- **Invalid Format**: If the underlying `ImageIO` cannot write to the file path due to unsupported extensions or locked files, an `IOException` will be thrown during the subsequent write process.

### Fix Code Hint
```java
try (OutputStream os = sink.createOutputStream(destinationFile)) {
    // Perform image writing operations
} catch (IOException e) {
    // Handle stream creation or write errors
}
```

## API Test: `createThumbnail`

### Signature
```java
public static void createThumbnail( InputStream is, OutputStream os, String format, int width, int height )
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnailator.java_

_Source doc:_ Creates a thumbnail from image data streamed from an {@link InputStream} and streams the data out to an {@link OutputStream}, with the specified format for the output data. @param is			The {@link InputStream} from which to obtain image data. @param os			The {@link OutputStream} to send thumbnail data to. @param format		The image format to use to store the thumbnail data. @param width			The width of the thumbnail. @param height		The height of the thumbnail. @throws IOException	Thrown when a problem occurs when reading from {@code File} representing an image file. @throws IllegalArgumentException		If the specified output format is not supported.

### Goal
Provides a direct, stream-based utility to generate a resized thumbnail from an input image stream to an output stream using a specified image format.

### Parameters
- `is` (`InputStream`): The source stream containing the raw image data to be processed.
- `os` (`OutputStream`): The destination stream where the resulting thumbnail data will be written.
- `format` (`String`): The image format (e.g., "jpg", "png") to use for the output data.
- `width` (`int`): The target width of the thumbnail in pixels.
- `height` (`int`): The target height of the thumbnail in pixels.

### Input
The caller must provide a valid `InputStream` containing image data compatible with the host JVM's `ImageIO` capabilities. The `format` string must be a format supported by the underlying `ImageIO` implementation.

### Output
Returns `void` — The method writes the processed thumbnail data directly to the provided `OutputStream`.

### Valid Call Patterns
```java
// Inferred from signature:
Thumbnailator.createThumbnail(inputStream, outputStream, "jpg", 100, 100);
```

### LLM Instruction Prompt
- Use `Thumbnailator.createThumbnail` only when performing direct stream-to-stream transformations without the need for complex pipeline operations like watermarking or rotation.
- Ensure the `format` string matches standard `ImageIO` format names.
- Always wrap the call in a try-catch block to handle `IOException` and `IllegalArgumentException`.

### Prompt Snippet
```text
Thumbnailator.createThumbnail(is, os, "png", 200, 200);
```

### Common Failure Modes
- `NullPointerException`: Occurs if the `InputStream` or `OutputStream` provided is `null`.
- `IllegalArgumentException`: Occurs if the specified `format` is not supported by the current JVM environment.
- `IOException`: Occurs if the input stream does not contain valid image data or if there is an error writing to the output stream.

### Fix Code Hint
```java
try {
    Thumbnailator.createThumbnail(is, os, "jpg", 100, 100);
} catch (IOException e) {
    // Handle read/write errors
} catch (IllegalArgumentException e) {
    // Handle unsupported format errors
}
```

## API Test: `createThumbnails`

### Signature
```java
public static void createThumbnails( Collection<? extends File> files, Rename rename, int width, int height )
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnailator.java:453_

_Source doc:_ Creates thumbnails from a specified {@code Collection} of {@code File}s. The filenames of the resulting thumbnails are determined by applying the specified {@code Rename} function. @param files			A {@code Collection} containing {@code File} objects of image files. @param rename		The renaming function to use. @param width			The width of the thumbnail. @param height		The height of the thumbnail. @throws IOException	Thrown when a problem occurs when reading from {@code File} representing an image file. @deprecated		This method has been deprecated in favor of using the {@link Thumbnails#fromFiles(Iterable)} interface. This method will be removed in 0.5.0, and will not be further maintained.

### Goal
Performs batch thumbnail generation by resizing a collection of image files to specified dimensions and applying a naming strategy to the output files.

### Parameters
- `files` (`Collection<? extends File>`): A collection of `File` objects representing the source images to be processed.
- `rename` (`Rename`): A `Rename` strategy (e.g., `Rename.PREFIX_DOT_THUMBNAIL`) that determines the filename of the generated thumbnail relative to the source file.
- `width` (`int`): The target width of the thumbnail in pixels.
- `height` (`int`): The target height of the thumbnail in pixels.

### Input
The input must be a `Collection` of `File` objects pointing to valid image formats supported by the host JVM's `ImageIO` (e.g., JPEG, PNG). Preconditions include ensuring the files exist and are readable.

### Output
Returns `void`. The method writes the generated thumbnails directly to the filesystem in the same directory as the source files, using the naming convention defined by the `rename` parameter.

### Valid Call Patterns
```java
// Example using a collection of files and a standard rename strategy
List<File> files = Arrays.asList(new File("image1.jpg"), new File("image2.jpg"));
Thumbnailator.createThumbnails(files, Rename.PREFIX_DOT_THUMBNAIL, 50, 50);
```

### LLM Instruction Prompt
- Use `Thumbnailator.createThumbnails` only for legacy batch processing tasks.
- Always prefer the modern `Thumbnails.fromFiles(Iterable)` fluent API for new development.
- Ensure the `files` collection is not null and contains valid `File` objects.
- Ensure the `rename` parameter is not null.

### Prompt Snippet
```text
Thumbnailator.createThumbnails(files, Rename.PREFIX_DOT_THUMBNAIL, width, height);
```

### Common Failure Modes
- `NullPointerException`: Occurs if the `files` collection or the `rename` strategy is passed as `null`.
- `IOException`: Occurs if the source files cannot be read or if the output files cannot be written to the destination directory.
- `IllegalArgumentException`: May occur if the provided dimensions are invalid (e.g., non-positive values).

### Fix Code Hint
```java
// Modern replacement for the deprecated createThumbnails method:
Thumbnails.fromFiles(files)
    .size(width, height)
    .toFiles(rename);
```

## API Test: `createThumbnailsAsCollection`

### Signature
```java
public static Collection<File> createThumbnailsAsCollection( Collection<? extends File> files, Rename rename, int width, int height )
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnailator.java:400_

_Source doc:_ Creates thumbnails from a specified {@link Collection} of {@link File}s. The filenames of the resulting thumbnails are determined by applying the specified {@link Rename}. <p> The order of the thumbnail {@code File}s in the returned {@code Collection} will be the same as the order as the source list. @param files			A {@code Collection} containing {@code File} objects of image files. @param rename		The renaming function to use. @param width			The width of the thumbnail. @param height		The height of the thumbnail. @throws IOException	Thrown when a problem occurs when reading from {@code File} representing an image file. @return 			A collection of {@code File}s to the thumbnails. @deprecated		This method has been deprecated in favor of using the {@link Thumbnails#fromFiles(Iterable)} interface. This method will be removed in 0.5.0, and will not be further maintained.

### Goal
Performs batch thumbnail generation for a collection of image files, applying a specific naming strategy and fixed dimensions to each output.

### Parameters
- `files` (`Collection<? extends File>`): A collection of `File` objects representing the source images to be processed.
- `rename` (`Rename`): A `Rename` strategy (e.g., `Rename.PREFIX_DOT_THUMBNAIL`) used to determine the output filename for each generated thumbnail.
- `width` (`int`): The target width of the resulting thumbnails in pixels.
- `height` (`int`): The target height of the resulting thumbnails in pixels.

### Input
The caller must provide a `Collection` of valid image files supported by the host JVM's `ImageIO` capabilities. Preconditions include ensuring the files exist and are readable. Note that this method is deprecated; users should prefer the `Thumbnails.fromFiles(Iterable)` fluent API for new development.

### Output
Returns `Collection<File>` — A collection containing the `File` references to the newly created thumbnails, maintaining the same order as the input collection.

### Valid Call Patterns
```java
// Example using a list of files and a standard Rename strategy
List<File> files = Arrays.asList(new File("image1.jpg"), new File("image2.jpg"));
Collection<File> thumbnails = Thumbnailator.createThumbnailsAsCollection(
    files, 
    Rename.PREFIX_DOT_THUMBNAIL, 
    50, 
    50
);
```

### LLM Instruction Prompt
- Use `Thumbnailator.createThumbnailsAsCollection` only for legacy maintenance.
- Ensure the `files` collection is not null and contains valid image files.
- Ensure the `rename` parameter is not null.
- Always wrap calls in a try-catch block to handle `IOException` during file I/O.

### Prompt Snippet
```text
Thumbnailator.createThumbnailsAsCollection(files, rename, width, height)
```

### Common Failure Modes
- `NullPointerException`: Occurs if the `files` collection or the `rename` strategy is passed as `null`.
- `IOException`: Occurs if the source files are inaccessible, corrupted, or if the JVM lacks the necessary `ImageIO` plugins to read the specific image format.
- `IllegalArgumentException`: May occur if dimensions are non-positive.

### Fix Code Hint
```java
// Migrate deprecated calls to the fluent API:
Thumbnails.fromFiles(files)
    .size(width, height)
    .toFiles(rename);
```

## API Test: `crop`

### Signature
```java
public Builder<T> crop(Position position)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1226_

_Source doc:_ Crops the thumbnail at the position specified by {@link Position}. This method must be used along with the {@link #size(int, int)} method to specify the size of the thumbnail. <p>This method will guarantee that the thumbnail will be exactly the dimensions specified in the {@link #size(int, int)} method. <p>Internally, the resizing is performed in two steps: <p>First, the thumbnail will be sized so that one of the dimensions will be exactly the dimension specified in the {@code size} method. The other dimension may overhang the specified dimension. For example, if the {@code .size(200, 200)} is called and the source image is 400 x 600, the image will be resized to 200 x 300, internally. <p>Next, the resized image will be cropped to the dimensions specified in the {@code size} method, positioned using the specified {@link Position} object. Continuing the example from the previous paragraph, the 200 x 300 image will be cropped to 200 x 200 using the specified positioning. <p>Once this method is called, calling the {@link #scale(double)} or {@link #scale(double, double)} method will throw an {@link IllegalStateException}. <p>Calling this method multiple times will throw an {@link IllegalStateException}. @param position		The position to which the thumbnail should be cropped to. For example, if {@link Positions#CENTER} is specified, the resulting thumbnail will be made by cropping to the center of the image. @return				Reference to this object. @throws NullPointerException		If the position is {@code null}. @since 	0.4.0

### Goal
Performs a deterministic crop operation on an image by resizing it to cover the target dimensions and then extracting the specified region to ensure the final output matches the exact dimensions defined by the `size` method.

### Parameters
- `position` (`Position`): An implementation of the `Position` interface (e.g., `Positions.CENTER`, `Positions.TOP_LEFT`) that defines the anchor point for the crop operation.

### Input
- **Data:** A valid `BufferedImage`, `File`, `InputStream`, or `URL` representing an image format supported by the host JVM's `ImageIO`.
- **Preconditions:** The `size(int, int)` method must be called in the pipeline to define the target dimensions.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further chaining of operations like `watermark` or `toFile`.

### Valid Call Patterns
```java
// Using the builder pattern with a predefined position
BufferedImage thumbnail = Thumbnails.of(img)
    .size(100, 50)
    .crop(Positions.CENTER)
    .asBufferedImage();
```

### LLM Instruction Prompt
- Always pair `crop` with a `size(int, int)` call.
- Do not use `scale` methods in the same pipeline as `crop`, as this will trigger an `IllegalStateException`.
- Ensure the `position` argument is not `null`.
- Only call `crop` once per pipeline.

### Prompt Snippet
```text
.size(width, height).crop(Positions.CENTER)
```

### Common Failure Modes
- **IllegalStateException:** Occurs if `scale()` is called in the same pipeline or if `crop()` is called more than once.
- **NullPointerException:** Occurs if the `position` argument passed is `null`.
- **Missing Size:** The pipeline will fail if `size(int, int)` is not defined, as the crop logic relies on these dimensions to calculate the internal resize and crop bounds.

### Fix Code Hint
```java
// Ensure size is set and scale is omitted
Thumbnails.of(source)
    .size(200, 200)
    .crop(Positions.TOP_LEFT)
    .toFile("output.jpg");
```

## API Test: `defaultImageType`

### Signature
```java
public ThumbnailMaker defaultImageType()
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ThumbnailMaker.java:240_

_Source doc:_ Sets the type of the {@link BufferedImage} to be the default type. @return				A reference to this object.

### Goal
Configures the thumbnail generation process to use the library's default `BufferedImage` type for the resulting image.

### Parameters
_None._

### Input
No specific input parameters are required. The caller must ensure the `ThumbnailMaker` instance is in a valid state for configuration. The default image type used is `ThumbnailParameter.DEFAULT_IMAGE_TYPE` (value: 2).

### Output
Returns `ThumbnailMaker` — A reference to the current `ThumbnailMaker` instance, allowing for method chaining in the fluent pipeline.

### Valid Call Patterns
```java
// Inferred from signature:
thumbnailMaker.defaultImageType();
```

### LLM Instruction Prompt
- Use `defaultImageType()` when you need to reset or explicitly set the output `BufferedImage` type to the library's default (value 2) within a `ThumbnailMaker` pipeline.
- This method returns the `ThumbnailMaker` instance, so it should be chained with other configuration methods or the final processing call.

### Prompt Snippet
```text
.defaultImageType()
```

### Common Failure Modes
- **Pipeline State:** Calling this method on a `ThumbnailMaker` that has already been used to produce an image may lead to unexpected behavior if the maker is not intended to be reused.
- **Type Conflict:** If a specific image type (e.g., `TYPE_INT_ARGB`) was previously set, calling `defaultImageType()` will overwrite that configuration with the library default.

### Fix Code Hint
```java
// Ensure the maker is configured correctly before processing
ThumbnailMaker maker = new ThumbnailMaker();
maker.defaultImageType();
maker.make(sourceImage);
```

## API Test: `defaultResizer`

### Signature
```java
public ThumbnailMaker defaultResizer()
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ThumbnailMaker.java:263_

_Source doc:_ Sets the {@link Resizer} to use the default {@link Resizer}. @return				A reference to this object.

### Goal
Configures the `ThumbnailMaker` to utilize the library's default scaling algorithm for image resizing operations.

### Parameters
_None._

### Input
No specific input parameters are required. The caller must ensure the `ThumbnailMaker` instance is properly initialized within a fluent pipeline or task abstraction. The underlying image data must be a valid format supported by the host JVM's `ImageIO`.

### Output
Returns `ThumbnailMaker` — A reference to the current `ThumbnailMaker` instance, allowing for method chaining in the fluent API.

### Valid Call Patterns
```java
// Inferred from signature:
thumbnailMaker.defaultResizer();
```

### LLM Instruction Prompt
- When using `defaultResizer`, ensure it is called on an instance of `ThumbnailMaker` before the thumbnail generation process is triggered.
- This method is used to explicitly reset or set the scaling algorithm to the library's default implementation if a custom `Resizer` was previously assigned.

### Prompt Snippet
```text
.defaultResizer()
```

### Common Failure Modes
- Calling this method on a null `ThumbnailMaker` reference, resulting in a `NullPointerException`.
- Attempting to use this method in an environment where the `ImageIO` subsystem is restricted or misconfigured, which may lead to runtime exceptions during the subsequent image processing phase.

### Fix Code Hint
```java
// Ensure the maker is initialized before configuration
ThumbnailMaker maker = new ThumbnailMaker();
maker.defaultResizer();
```

## API Test: `defaultResizerFactory`

### Signature
```java
public ThumbnailMaker defaultResizerFactory()
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ThumbnailMaker.java:290_

_Source doc:_ Sets the {@link ResizerFactory} to use {@link DefaultResizerFactory}. @return				A reference to this object. @since	0.4.0

### Goal
Configures the `ThumbnailMaker` to use the `DefaultResizerFactory` for determining the scaling algorithm used during the thumbnail generation process.

### Parameters
_None._

### Input
No input parameters are required. The caller must ensure the `ThumbnailMaker` instance is properly initialized within the fluent pipeline before invoking this method.

### Output
Returns `ThumbnailMaker` — A reference to the current `ThumbnailMaker` object, allowing for method chaining in the fluent API.

### Valid Call Patterns
```java
// Inferred from signature:
thumbnailMaker.defaultResizerFactory();
```

### LLM Instruction Prompt
- Use `defaultResizerFactory()` when you need to explicitly reset or set the resizer strategy to the library's default implementation within a `ThumbnailMaker` pipeline.
- This method returns the `ThumbnailMaker` instance, so it should be used as part of a fluent chain.

### Prompt Snippet
```text
// Example of setting the default resizer factory in a pipeline
ThumbnailMaker maker = new ThumbnailMaker();
maker.defaultResizerFactory();
```

### Common Failure Modes
- Calling this method on a null `ThumbnailMaker` reference will result in a `NullPointerException`.
- Attempting to use this method in an environment where the `ThumbnailMaker` has already been finalized or the output has been generated may lead to unexpected state behavior.

### Fix Code Hint
```java
// Ensure the maker is instantiated before configuration
ThumbnailMaker maker = new ThumbnailMaker();
maker.defaultResizerFactory();
// Proceed with further configuration or processing
```

## API Test: `determineOutputFormat`

### Signature
```java
public Builder<T> determineOutputFormat()
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1720_

_Source doc:_ Indicates that the output format should be determined from the available information when writing the thumbnail image. <p> For example, calling this method will cause the output format to be determined from the file extension if thumbnails are written to files. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. @return				Reference to this object. @since	0.4.0

### Goal
Configures the thumbnail pipeline to automatically detect the desired output image format based on the destination context, such as the file extension of the target output file.

### Parameters
_None._

### Input
The caller must ensure that the destination sink (e.g., a `File`) provides sufficient information for the Java Image I/O API to infer a format. Preconditions include having a valid image source processed through the `Thumbnails.Builder` pipeline.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further method chaining in the fluent pipeline.

### Valid Call Patterns
```java
Thumbnails.of(sourceFile)
    .size(10, 10)
    .determineOutputFormat()
    .toFile(destFile);
```

### LLM Instruction Prompt
- Use `determineOutputFormat()` only when the output format is not explicitly set via `outputFormat(String)`.
- Ensure this method is called exactly once in the builder chain; calling it multiple times will trigger an `IllegalStateException`.
- This method is intended for use when writing to files where the extension can serve as the format indicator.

### Prompt Snippet
```text
.determineOutputFormat()
```

### Common Failure Modes
- **IllegalStateException**: Occurs if `determineOutputFormat()` is invoked more than once on the same builder instance.
- **Format Detection Failure**: If the output destination does not provide a clear indicator (e.g., a file path without an extension), the underlying Java Image I/O may fail to determine the format, potentially resulting in an `IOException` during the write operation.

### Fix Code Hint
```java
// Ensure only one format-setting method is called
Thumbnails.of(source)
    .size(100, 100)
    .determineOutputFormat() // Do not call .outputFormat("png") after this
    .toFile("output.jpg");
```

## API Test: `dithering`

### Signature
```java
public Builder<T> dithering(Dithering config)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1402_

_Source doc:_ Sets the dithering mode when performing the resizing operation to generate the thumbnail. <p> Calling this method to set this parameter is optional. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. <p> This method cannot be called in conjunction with the {@link #resizerFactory(ResizerFactory)} method. @param config		The dithering mode. @return				Reference to this object.

### Goal
Configures the dithering algorithm used during the image resizing process to control color quantization artifacts in the generated thumbnail.

### Parameters
- `config` (`Dithering`): The specific dithering mode to apply (e.g., `Dithering.DEFAULT`, `Dithering.ENABLE`, or `Dithering.DISABLE`).

### Input
The caller must provide a valid `Dithering` enum constant. The input image must be a valid format supported by the host JVM's `ImageIO`. This method is intended for use within the `Thumbnails.Builder` fluent pipeline.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further chaining of thumbnail operations.

### Valid Call Patterns
```java
// Standard usage within a fluent pipeline
BufferedImage thumbnail = Thumbnails.of(img)
        .size(50, 50)
        .dithering(Dithering.DEFAULT)
        .asBufferedImage();
```

### LLM Instruction Prompt
- When using `dithering`, ensure it is called only once per builder instance.
- Do not use `dithering` if the pipeline already calls `resizerFactory(ResizerFactory)`, as these are mutually exclusive.
- Always pass a non-null `Dithering` enum value to avoid a `NullPointerException`.

### Prompt Snippet
```text
.dithering(Dithering.ENABLE)
```

### Common Failure Modes
- **`IllegalStateException`**: Thrown if `dithering` is called more than once on the same builder instance.
- **`IllegalStateException`**: Thrown if `dithering` is called in the same pipeline as `resizerFactory`.
- **`NullPointerException`**: Thrown if the `config` argument passed is `null`.

### Fix Code Hint
```java
// Ensure mutual exclusivity: remove resizerFactory if using dithering
Thumbnails.of(source)
    .size(100, 100)
    .dithering(Dithering.ENABLE) // Correct: only one call, no resizerFactory
    .toFile("output.jpg");
```

## API Test: `filters`

### Signature
```java
public ThumbnailParameterBuilder filters(List<ImageFilter> filters)
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:267_

_Source doc:_ Sets the {@link ImageFilter}s to apply to the thumbnail. <p> These filters will be applied after the original image is resized. @param filters		The output format type of the thumbnail. @return				A reference to this object.

### Goal
Applies a sequence of custom `ImageFilter` implementations to the thumbnail after the resizing operation has been performed.

### Parameters
- `filters` (`List<ImageFilter>`): A list of `ImageFilter` objects to be applied sequentially to the image.

### Input
- **Data:** A `List` containing instances of `net.coobird.thumbnailator.filters.ImageFilter`.
- **Preconditions:** The `ThumbnailParameterBuilder` must be configured for a valid image processing task. Filters are applied post-resize; ensure that the image state after resizing is compatible with the specific filters being applied.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, allowing for fluent method chaining.

### Valid Call Patterns
```java
// Inferred from signature:
List<ImageFilter> myFilters = new ArrayList<ImageFilter>();
myFilters.add(new SomeImageFilter());

ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.filters(myFilters);
```

### LLM Instruction Prompt
- When using `filters`, ensure the argument is a `List<ImageFilter>`. 
- Note that these filters are applied strictly after the resizing phase of the thumbnail pipeline.
- Do not attempt to pass individual `ImageFilter` objects directly; they must be wrapped in a `List`.

### Prompt Snippet
```text
.filters(Arrays.asList(filter1, filter2))
```

### Common Failure Modes
- **NullPointerException:** Passing a `null` list to the `filters` method.
- **Incompatible Filter State:** Applying filters that expect specific color models or dimensions that may have been altered or restricted by previous resizing operations.
- **Ordering Issues:** Assuming filters are applied before resizing; the API documentation explicitly states they are applied *after* the original image is resized.

### Fix Code Hint
```java
// Ensure the list is initialized and contains valid ImageFilter instances
List<ImageFilter> filterList = new ArrayList<ImageFilter>();
filterList.add(new Flip(Flip.HORIZONTAL));
builder.filters(filterList);
```

## API Test: `fitWithinDimenions`

### Signature
```java
public boolean fitWithinDimenions()
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:980_

_Source doc:_ Returns whether or not to fit the thumbnail within the specified dimensions. @return		{@code true} is returned when the thumbnail should be sized to fit within the specified dimensions, if the thumbnail is going to exceed those dimensions. @since	0.4.0

### Goal
Determines whether the thumbnail generation process is configured to constrain the output image dimensions to fit within the specified bounds if the source image exceeds them.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No external file or stream inputs are required for this getter method, as it queries the internal state of the parameter configuration.

### Output
Returns `boolean` — `true` if the thumbnail is configured to be resized to fit within the specified dimensions when the source exceeds those bounds; `false` otherwise.

### Valid Call Patterns
```java
// Inferred from signature:
boolean isFitting = thumbnailParameter.fitWithinDimenions();
```

### LLM Instruction Prompt
- Use `fitWithinDimenions()` only when inspecting a `ThumbnailParameter` instance.
- Do not confuse this with a setter; this is a read-only property check.
- Note that the method name contains a typo ("dimenions" instead of "dimensions") which must be preserved exactly as written to ensure compilation.

### Prompt Snippet
```text
// Check if the current ThumbnailParameter is set to constrain dimensions
if (param.fitWithinDimenions()) {
    // Logic for constrained resizing
}
```

### Common Failure Modes
- **Typo Mismatch:** Attempting to call `fitWithinDimensions()` (with the correct spelling) will result in a compilation error; the method name is strictly `fitWithinDimenions`.
- **Null Pointer:** Calling this on a `null` `ThumbnailParameter` instance will throw a `NullPointerException`.
- **Context Misunderstanding:** Expecting this method to perform the actual resizing; it is a configuration query, not an execution method. The actual resizing occurs during the `Thumbnails.Builder` pipeline execution.

### Fix Code Hint
```java
// Ensure the method name matches the API exactly, including the typo
if (param.fitWithinDimenions()) {
    // Proceed with logic knowing the parameter is set to fit
}
```

## API Test: `fitWithinDimensions`

### Signature
```java
public ThumbnailParameterBuilder fitWithinDimensions(boolean fit)
public FixedSizeThumbnailMaker fitWithinDimensions(boolean fit)
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:330  (+1 more definition site/overload)_

_Source doc:_ Sets whether or not the thumbnail should fit within the specified dimensions. @param fit		{@code true} if the thumbnail should be sized to fit within the specified dimensions, if the thumbnail is going to exceed those dimensions. @return			A reference to this object. @since	0.4.0

### Goal
Configures the thumbnail generation process to ensure the resulting image dimensions do not exceed the specified bounds while maintaining the aspect ratio.

### Parameters
- `fit` (`boolean`): If `true`, the thumbnail is constrained to fit within the defined dimensions; if `false`, the image may exceed those dimensions depending on other scaling configurations.

### Input
The caller must provide a valid `BufferedImage` or image source supported by the JVM's `ImageIO`. This method is typically used in conjunction with `keepAspectRatio(true)` to ensure the image is scaled down to fit within the target width and height without distortion.

### Output
Returns `ThumbnailParameterBuilder` or `FixedSizeThumbnailMaker` — A reference to the builder or maker object, allowing for fluent method chaining.

### Valid Call Patterns
```java
// Using FixedSizeThumbnailMaker
BufferedImage thumbnail = new FixedSizeThumbnailMaker(100, 100)
        .keepAspectRatio(true)
        .fitWithinDimensions(true)
        .make(img);

// Using three-argument constructor
BufferedImage thumbnail = new FixedSizeThumbnailMaker(100, 100, true)
        .fitWithinDimensions(true)
        .make(img);
```

### LLM Instruction Prompt
- When using `fitWithinDimensions(true)`, ensure that `keepAspectRatio(true)` is also set to prevent image distortion, as the primary use case for fitting within dimensions is to scale an image down to a bounding box.

### Prompt Snippet
```text
.keepAspectRatio(true).fitWithinDimensions(true)
```

### Common Failure Modes
- **Ignoring Aspect Ratio:** If `keepAspectRatio` is set to `false`, the image may be stretched or squashed to fit the dimensions exactly, ignoring the intended visual proportions.
- **Invalid Image Source:** Passing a null or unsupported image format to the `make()` method will result in an `IllegalArgumentException` or `IOException` during the processing phase.

### Fix Code Hint
```java
// Ensure aspect ratio is preserved when fitting within dimensions
new FixedSizeThumbnailMaker(width, height)
    .keepAspectRatio(true)
    .fitWithinDimensions(true)
    .make(sourceImage);
```

## API Test: `forceSize`

### Signature
```java
public Builder<T> forceSize(int width, int height)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:940_

_Source doc:_ Sets the size of the thumbnail. <p> The thumbnails will be forced to the specified size, therefore, the aspect ratio of the original image will not be preserved in the thumbnails. Calling this method will be equivalent to calling the {@link #size(int, int)} method in conjunction with the {@link #keepAspectRatio(boolean)} method with the value {@code false}. <p> Once this method is called, calling the {@link #scale(double)} method will result in an {@link IllegalStateException}. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. @param width			The width of the thumbnail. @param height		The height of the thumbnail. @return				Reference to this object. @since 	0.3.2

### Goal
Forces the thumbnail to a specific pixel dimension, ignoring the original image's aspect ratio.

### Parameters
- `width` (`int`): The target width of the thumbnail in pixels.
- `height` (`int`): The target height of the thumbnail in pixels.

### Input
The input must be a valid image source (e.g., `File`, `InputStream`, `URL`, or `BufferedImage`) supported by the JVM's `ImageIO`. The pipeline must be initialized via `Thumbnails.of(...)`.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further chaining of operations such as `rotate`, `watermark`, or output methods.

### Valid Call Patterns
```java
BufferedImage thumbnail = Thumbnails.of(img)
    .forceSize(50, 50)
    .asBufferedImage();
```

### LLM Instruction Prompt
- Use `forceSize(int, int)` when the exact output dimensions are required regardless of image distortion.
- Do not chain `forceSize` with `scale(double)` or call `forceSize` more than once in the same pipeline, as both actions trigger an `IllegalStateException`.
- Remember that this method implicitly sets `keepAspectRatio` to `false`.

### Prompt Snippet
```text
.forceSize(width, height)
```

### Common Failure Modes
- **IllegalStateException**: Occurs if `forceSize` is called more than once in the same builder chain.
- **IllegalStateException**: Occurs if `scale(double)` is called in the same builder chain as `forceSize`.
- **IllegalArgumentException**: May occur if negative dimensions are provided (depending on underlying `ImageIO` and `Resizer` implementation constraints).

### Fix Code Hint
```java
// Ensure forceSize is called exactly once and not combined with scale()
Thumbnails.of(sourceImage)
    .forceSize(100, 100)
    .toFile("output.jpg");
```

## API Test: `format`

### Signature
```java
public ThumbnailParameterBuilder format(String format)
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:243_

_Source doc:_ Sets the output format of the thumbnail. @param format		The output format of the thumbnail. @return				A reference to this object.

### Goal
Specifies the image file format (e.g., "jpg", "png") for the generated thumbnail output.

### Parameters
- `format` (`String`): The informal name of the image format (e.g., "jpg", "png", "gif"). An empty string is the default.

### Input
The caller must provide a string representing a format supported by the host JVM's `ImageIO` capabilities. Precondition: The underlying `ImageIO` implementation must have a registered `ImageWriter` for the specified format.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, allowing for method chaining in the fluent pipeline.

### Valid Call Patterns
```java
// Inferred from signature: setting the output format on a builder
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.format("png");
```

### LLM Instruction Prompt
- Use `format` only when configuring a `ThumbnailParameterBuilder` instance.
- Ensure the string passed to `format` corresponds to a format supported by the host JVM's `ImageIO` (e.g., "jpg", "png").
- Do not assume all formats are available; availability depends on the host environment.

### Prompt Snippet
```text
.format("jpg")
```

### Common Failure Modes
- **Unsupported Format:** Passing a format string (e.g., "webp") that the host JVM's `ImageIO` does not support, resulting in an `IIOException` or failure during the write phase.
- **Null Argument:** Passing `null` may result in a `NullPointerException` depending on the internal implementation of the builder.
- **Environment Mismatch:** Relying on formats like "gif" which may not be available in older Java environments (e.g., Java 5).

### Fix Code Hint
```java
// Ensure the format is supported by the current JVM
try {
    builder.format("png");
} catch (Exception e) {
    // Handle unsupported format or configuration error
}
```

## API Test: `formatType`

### Signature
```java
public ThumbnailParameterBuilder formatType(String formatType)
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:254_

_Source doc:_ Sets the output format type of the thumbnail. @param formatType	The output format type of the thumbnail. @return				A reference to this object.

### Goal
Sets the specific compression or format type (e.g., "JPEG" or "PNG") for the thumbnail output, allowing for fine-grained control over the encoding process.

### Parameters
- `formatType` (`String`): The name of the format type to be used for the thumbnail (e.g., "JPEG"). Defaults to an empty string if not specified.

### Input
The caller must provide a `String` representing a valid format type supported by the underlying Java Image I/O implementation. Precondition: The `ThumbnailParameterBuilder` must be in a state where the output format has been established, as the validity of the `formatType` is dependent on the host JVM's `ImageIO` capabilities.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, enabling fluent method chaining.

### Valid Call Patterns
```java
// Inferred from signature:
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.formatType("JPEG");
```

### LLM Instruction Prompt
- Use `formatType` only when configuring a `ThumbnailParameterBuilder` instance.
- Ensure the string passed to `formatType` corresponds to a valid format type supported by the host JVM's `ImageIO` (e.g., "JPEG", "PNG").
- Always chain this method with other builder configuration methods to construct a complete `ThumbnailParameter` object.

### Prompt Snippet
```text
.formatType("JPEG")
```

### Common Failure Modes
- Passing an unsupported format type string, which may result in an `IllegalArgumentException` or an `IIOException` during the actual image writing process, depending on the JVM's `ImageIO` provider.
- Providing an empty string or null, which may lead to unexpected behavior or default encoding settings depending on the implementation.

### Fix Code Hint
```java
// Ensure the format type is supported by the JVM
builder.formatType("JPEG"); 
```

## API Test: `fromFilenames`

### Signature
```java
public static Builder<File> fromFilenames(Iterable<String> files)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:292_

_Source doc:_ Indicate to make thumbnails for images with the specified filenames. @param files		File names of image files for which thumbnails are to be produced for. @return			Reference to a builder object which is used to specify the parameters for creating the thumbnail. @throws NullPointerException		If the argument is {@code null}. @throws IllegalArgumentException	If the argument is an empty collection. @since 	0.3.1

### Goal
Initializes a fluent thumbnail processing pipeline for a batch of image files specified by their absolute or relative file system paths.

### Parameters
- `files` (`Iterable<String>`): An `Iterable` collection of strings representing the file paths of the source images to be processed.

### Input
- **Data:** A collection of valid file paths pointing to image files.
- **Formats:** The files must be in a format supported by the host JVM's `ImageIO` (e.g., JPEG, PNG).
- **Preconditions:** The files must exist and be readable by the application. The collection must not be null or empty.

### Output
Returns `Builder<File>` — A builder instance configured to process the specified files, allowing for the chaining of operations like `size()`, `rotate()`, and `watermark()` before outputting to a destination.

### Valid Call Patterns
```java
// Processing a single file path
Thumbnails.fromFilenames(Arrays.asList(f))
    .size(50, 50)
    .toFile(outFile);

// Processing multiple file paths
Thumbnails.fromFilenames(Arrays.asList(f, f))
    .size(50, 50)
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

### LLM Instruction Prompt
- Use `Thumbnails.fromFilenames(Iterable<String>)` when the input source is a collection of file paths.
- Always wrap the file path list in `Arrays.asList(...)` or another `Iterable` implementation.
- Ensure the subsequent builder methods (e.g., `.size()`) are called before the terminal output method (e.g., `.toFile()` or `.toFiles()`).

### Prompt Snippet
```text
Thumbnails.fromFilenames(Arrays.asList("path/to/image1.jpg", "path/to/image2.jpg"))
```

### Common Failure Modes
- **`NullPointerException`**: Occurs if the `files` argument passed to the method is `null`.
- **`IllegalArgumentException`**: Occurs if the provided `Iterable` is empty.
- **`IOException`**: Occurs if the files at the provided paths do not exist, are not readable, or are not valid image formats supported by `ImageIO`.

### Fix Code Hint
```java
// Ensure the list is not empty and paths are valid
List<String> filePaths = Arrays.asList("image1.jpg", "image2.jpg");
if (!filePaths.isEmpty()) {
    Thumbnails.fromFilenames(filePaths)
        .size(100, 100)
        .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
}
```

## API Test: `fromFiles`

### Signature
```java
public static Builder<File> fromFiles(Iterable<File> files)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:309_

_Source doc:_ Indicate to make thumbnails from the specified {@link File}s. @param files		{@link File} objects of image files for which thumbnails are to be produced for. @return			Reference to a builder object which is used to specify the parameters for creating the thumbnail. @throws NullPointerException		If the argument is {@code null}. @throws IllegalArgumentException	If the argument is an empty collection. @since 	0.3.1

### Goal
Initializes a fluent thumbnail processing pipeline for a collection of image files, enabling batch transformations such as resizing, rotation, and watermarking.

### Parameters
- `files` (`Iterable<File>`): A collection of `File` objects representing the source images to be processed.

### Input
- **Data:** Must be valid image files supported by the host JVM's `ImageIO` capabilities.
- **Preconditions:** The `Iterable` must not be `null` and must contain at least one `File` object. Files must be accessible and readable by the application.

### Output
Returns `Builder<File>` — A builder instance configured for batch processing, allowing for the chaining of transformation operations before outputting to files or streams.

### Valid Call Patterns
```java
// Batch processing using a list of files
List<File> sources = Arrays.asList(new File("image1.jpg"), new File("image2.jpg"));
Thumbnails.fromFiles(sources)
    .size(100, 100)
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

### LLM Instruction Prompt
- Use `Thumbnails.fromFiles(Iterable<File>)` as the entry point for batch processing tasks.
- Always chain a terminal operation (e.g., `.toFiles(...)` or `.asFiles(...)`) to execute the pipeline.
- Ensure the input collection is not empty to avoid `IllegalArgumentException`.

### Prompt Snippet
```text
Thumbnails.fromFiles(myFileCollection)
    .size(width, height)
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

### Common Failure Modes
- **`NullPointerException`**: Occurs if the passed `Iterable` is `null`.
- **`IllegalArgumentException`**: Occurs if the passed `Iterable` is empty.
- **`IOException`**: Occurs if the underlying `ImageIO` cannot read a specific file in the collection (e.g., unsupported format or corrupted file).
- **`UnsupportedFormatException`**: Occurs if the JVM lacks the necessary codec for the specific image file type provided.

### Fix Code Hint
```java
// Ensure the collection is populated and non-null before passing to fromFiles
if (files != null && !files.isEmpty()) {
    Thumbnails.fromFiles(files).size(100, 100).toFiles(Rename.PREFIX_DOT_THUMBNAIL);
}
```

## API Test: `fromImages`

### Signature
```java
public static Builder<BufferedImage> fromImages(Iterable<BufferedImage> images)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:364_

_Source doc:_ Indicate to make thumbnails from the specified {@link BufferedImage}s. @param images	{@link BufferedImage}s for which thumbnails are to be produced for. @return			Reference to a builder object which is used to specify the parameters for creating the thumbnail. @throws NullPointerException		If the argument is {@code null}. @throws IllegalArgumentException	If the argument is an empty collection. @since 	0.3.1

### Goal
Initializes a thumbnail processing pipeline for a collection of in-memory `BufferedImage` objects.

### Parameters
- `images` (`Iterable<BufferedImage>`): An iterable collection of `BufferedImage` objects to be processed as the source images for thumbnail generation.

### Input
- **Data:** `Iterable<BufferedImage>`.
- **Preconditions:** The collection must not be `null` and must contain at least one `BufferedImage`.
- **Format:** Images must be valid `BufferedImage` instances already loaded into memory.

### Output
Returns `Builder<BufferedImage>` — A fluent builder instance configured to process the provided images, allowing for subsequent chaining of operations like `size()`, `rotate()`, or `watermark()`.

### Valid Call Patterns
```java
// Single image processing
BufferedImage thumbnail = Thumbnails.fromImages(Arrays.asList(img))
    .size(100, 100)
    .asBufferedImage();
```

### LLM Instruction Prompt
- Use `Thumbnails.fromImages()` when the source data is already present as a collection of `BufferedImage` objects in memory.
- Always wrap the source images in an `Iterable` (e.g., `Arrays.asList()`).
- Note that while this method accepts multiple images, attempting to output a single thumbnail (e.g., via `.asBufferedImage()`) from a collection containing more than one image will result in an `IllegalArgumentException`.

### Prompt Snippet
```text
Thumbnails.fromImages(Iterable<BufferedImage>)
```

### Common Failure Modes
- **`NullPointerException`**: Thrown if the `images` argument is `null`.
- **`IllegalArgumentException`**: Thrown if the provided collection is empty.
- **`IllegalArgumentException`**: Thrown if the pipeline attempts to produce a single output (like `.asBufferedImage()`) when the input collection contains multiple images.

### Fix Code Hint
```java
// Ensure the collection is not empty and handle multiple images correctly
List<BufferedImage> myImages = ...;
if (myImages != null && !myImages.isEmpty()) {
    // If processing multiple images, use toFiles() or toStreams() instead of asBufferedImage()
    Thumbnails.fromImages(myImages)
        .size(100, 100)
        .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
}
```

## API Test: `fromInputStreams`

### Signature
```java
public static Builder<InputStream> fromInputStreams(Iterable<? extends InputStream> inputStreams)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:347_

_Source doc:_ Indicate to make thumbnails for images obtained from the specified {@link InputStream}s. <p> Note that the {@link InputStream#close()} method will not be called upon reading the source image from the {@link InputStream}. @param inputStreams		{@link InputStream}s which provide images for which thumbnails are to be produced. @return			Reference to a builder object which is used to specify the parameters for creating the thumbnail. @throws NullPointerException		If the argument is {@code null}. @throws IllegalArgumentException	If the argument is an empty collection. @since 	0.3.1

### Goal
Initializes a thumbnail processing pipeline for a collection of image data streams, allowing for fluent transformation and output configuration.

### Parameters
- `inputStreams` (`Iterable<? extends InputStream>`): An iterable collection of `InputStream` objects, each containing image data to be processed.

### Input
- **Data:** Must be valid image formats supported by the host JVM's `ImageIO` capabilities.
- **Preconditions:** The caller is responsible for managing the lifecycle of the `InputStream` objects, as Thumbnailator will not close them after processing.
- **Constraints:** The collection must not be null or empty.

### Output
Returns `Builder<InputStream>` — A fluent builder object used to chain operations (e.g., `.size()`, `.rotate()`) and define the final output sink.

### Valid Call Patterns
```java
// Single stream processing
BufferedImage thumbnail = Thumbnails.fromInputStreams(Arrays.asList(is))
    .size(100, 100)
    .asBufferedImage();
```

### LLM Instruction Prompt
- Use `Thumbnails.fromInputStreams` when the source image data is provided via an `InputStream` (e.g., from a network socket or file stream).
- Always wrap the `InputStream` in an `Iterable` (e.g., `Arrays.asList(is)`).
- Do not attempt to process multiple streams into a single output; the builder expects a single source image for the final output operation.

### Prompt Snippet
```text
Thumbnails.fromInputStreams(Collections.singletonList(inputStream))
```

### Common Failure Modes
- **`IllegalArgumentException`:** Occurs if the provided collection is empty or if an attempt is made to generate a single output from multiple input streams.
- **`NullPointerException`:** Occurs if the `inputStreams` argument is null.
- **`IOException`:** May occur if the `InputStream` contains data that is not a valid image format supported by the JVM.

### Fix Code Hint
```java
// Ensure the collection is not empty and contains valid image data
List<InputStream> streams = new ArrayList<>();
streams.add(myInputStream);
Thumbnails.fromInputStreams(streams)
          .size(width, height)
          .toFile(outputFile);
```

## API Test: `fromURLs`

### Signature
```java
public static Builder<URL> fromURLs(Iterable<URL> urls)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:326_

_Source doc:_ Indicate to make thumbnails for images with the specified {@link URL}s. @param urls		URLs of the images for which thumbnails are to be produced. @return			Reference to a builder object which is used to specify the parameters for creating the thumbnail. @throws NullPointerException		If the argument is {@code null}. @throws IllegalArgumentException	If the argument is an empty collection. @since 	0.3.1

### Goal
Initializes a thumbnail processing pipeline for a collection of images sourced from remote or local URLs.

### Parameters
- `urls` (`Iterable<URL>`): An iterable collection of `URL` objects pointing to the source images to be processed.

### Input
- **Data:** A valid `Iterable` containing `URL` objects.
- **Formats:** The URLs must point to image formats supported by the host JVM's `ImageIO` capabilities.
- **Preconditions:** The collection must not be null and must contain at least one `URL`.

### Output
Returns `Builder<URL>` — A fluent builder instance used to configure subsequent operations (e.g., `size`, `rotate`, `watermark`) and define the output destination.

### Valid Call Patterns
```java
// Single URL processing
URL url = TestUtils.getResource("Thumbnailator/grid.png");
BufferedImage thumbnail = Thumbnails.fromURLs(Arrays.asList(url))
    .size(100, 100)
    .asBufferedImage();
```

### LLM Instruction Prompt
- Use `Thumbnails.fromURLs(Iterable<URL>)` to start a pipeline when the source images are provided as a collection of URLs.
- Ensure the input is wrapped in an `Iterable` (e.g., `Arrays.asList(...)`).
- Note that this method is intended for single-image processing pipelines; attempting to output a single result from multiple URLs will trigger an `IllegalArgumentException`.

### Prompt Snippet
```text
Thumbnails.fromURLs(Arrays.asList(url))
```

### Common Failure Modes
- **`NullPointerException`**: Thrown if the `urls` argument is `null`.
- **`IllegalArgumentException`**: Thrown if the provided collection is empty.
- **`IllegalArgumentException`**: Thrown during terminal operations if the pipeline contains multiple URLs but attempts to produce a single output (e.g., `asBufferedImage()`).
- **`IOException`**: Occurs if the provided URL is unreachable or points to an unsupported/corrupt image format.

### Fix Code Hint
```java
// Ensure the collection is not empty and contains valid image URLs
List<URL> urlList = new ArrayList<>();
urlList.add(myUrl);
Thumbnails.fromURLs(urlList).size(100, 100).toFile("output.jpg");
```

## API Test: `getAlpha`

### Signature
```java
public float getAlpha()
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Transparency.java:113_

_Source doc:_ Returns the opacity of this filter. @return		The opacity in the range of {@code 0.0f} (transparent) to {@code 1.0f} (opaque).

### Goal
Retrieves the current opacity level configured for a `Transparency` filter used within the Thumbnailator image processing pipeline.

### Parameters
_None._

### Input
The caller must have an instance of a `Transparency` filter (or a subclass/implementation that exposes this method). No external file formats or specific image data are required to query the filter's internal state.

### Output
Returns `float` — The opacity value of the filter, represented as a float in the range of `0.0f` (fully transparent) to `1.0f` (fully opaque).

### Valid Call Patterns
```java
// Inferred from signature:
float opacity = transparencyFilter.getAlpha();
```

### LLM Instruction Prompt
- Use `getAlpha()` only when interacting with an instance of a `Transparency` filter.
- Ensure the returned `float` is treated as a normalized opacity value between 0.0 and 1.0.
- Do not assume this method exists on general `ImageFilter` types unless the specific instance is known to be a `Transparency` filter.

### Prompt Snippet
```text
// Example of checking filter opacity
Transparency filter = new Transparency(0.5f);
float currentAlpha = filter.getAlpha();
```

### Common Failure Modes
- **NullPointerException**: Calling `getAlpha()` on a null filter reference.
- **Type Mismatch**: Attempting to call `getAlpha()` on a generic `ImageFilter` interface that does not implement the `Transparency` logic.
- **Logic Errors**: Assuming the returned value represents pixel-level alpha data rather than the filter's configured opacity setting.

### Fix Code Hint
```java
// Ensure the object is of the correct type before calling
if (filter instanceof Transparency) {
    float alpha = ((Transparency) filter).getAlpha();
}
```

## API Test: `getBoolean`

### Signature
```java
public boolean getBoolean()
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/Configurations.java:143_

_Source doc:_ Returns whether the specified configuration is enabled or not. @return  {@code true} if the configuration is enabled, {@code false} otherwise.

### Goal
Determines the current enabled or disabled state of a specific `Configurations` enum constant within the Thumbnailator utility settings.

### Parameters
_None._

### Input
The caller must invoke this method on an instance of the `net.coobird.thumbnailator.util.Configurations` enum. No external file formats or image data are required as this method operates on internal library configuration state.

### Output
Returns `boolean` — `true` if the specific configuration setting is currently enabled, and `false` if it is disabled.

### Valid Call Patterns
```java
// Iterating through available configurations
for (Configurations config : Configurations.values()) {
    boolean isEnabled = config.getBoolean();
}
```

### LLM Instruction Prompt
- Use `getBoolean()` only when interacting with instances of the `net.coobird.thumbnailator.util.Configurations` enum.
- Do not attempt to pass arguments to this method, as it takes no parameters.
- Ensure the context of the call is within the library's internal utility usage, as this is not a method for general image processing pipelines.

### Prompt Snippet
```text
// Check if a specific configuration is active
boolean status = Configurations.SOME_CONFIG.getBoolean();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the method is called on a null reference of a `Configurations` object.
- **Misuse:** Attempting to use this method to check the status of image processing tasks (e.g., `ThumbnailParameter` or `Thumbnails.Builder`); this method is strictly for the `Configurations` utility enum.

### Fix Code Hint
```java
// Ensure the receiver is a valid enum constant
Configurations config = Configurations.SOME_CONFIG;
if (config != null) {
    boolean enabled = config.getBoolean();
}
```

## API Test: `getCount`

### Signature
```java
public int getCount()
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdStructure.java:82_

_Source doc:_ Returns the count element in the IFD structure, indicating the number of values the value field.. @return		A count indicating the number of values.

### Goal
Retrieves the count element from an IFD (Image File Directory) structure, which specifies the number of values contained within the associated value field of the metadata.

### Parameters
_None._

### Input
The caller must have an initialized `IfdStructure` object, typically obtained during the parsing of image metadata (such as EXIF data) from an input source like a `File` or `InputStream`.

### Output
Returns `int` — The number of values present in the IFD value field.

### Valid Call Patterns
```java
// Inferred from signature:
int count = ifdStructure.getCount();
```

### LLM Instruction Prompt
- Use `getCount()` only when interacting with an instance of `net.coobird.thumbnailator.util.exif.IfdStructure`.
- Do not assume the return value represents the total number of tags in the IFD; it specifically refers to the count element of a single IFD structure.

### Prompt Snippet
```text
// Accessing the count of values in an IFD structure
int valueCount = ifdStructure.getCount();
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `IfdStructure` instance has not been properly initialized or is null.
- **Invalid Metadata Context**: Calling this on an `IfdStructure` that does not correspond to a valid or parsed image metadata segment may return unexpected or default integer values.

### Fix Code Hint
```java
if (ifdStructure != null) {
    int count = ifdStructure.getCount();
    // Proceed with processing based on the count
}
```

## API Test: `getDestination`

### Signature
```java
public D getDestination()
public File getDestination()
public abstract D getDestination()
public OutputStream getDestination()
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/SourceSinkThumbnailTask.java:126  (+3 more definition site/overload)_

### Goal
Retrieves the designated output target (file, stream, or object) where the processed thumbnail will be stored or written.

### Parameters
_None._

### Input
The caller must ensure that the task has been initialized with a valid destination sink. The destination must be compatible with the underlying Java Image I/O requirements for the chosen output format.

### Output
Returns `D` — The destination object, which may be a `File`, `OutputStream`, or another supported sink type depending on the specific implementation of the task.

### Valid Call Patterns
```java
// Inferred from signature:
D destination = task.getDestination();
```

### LLM Instruction Prompt
- Use `getDestination()` only when interacting with a `SourceSinkThumbnailTask` or its subclasses.
- Do not assume the return type is always `File`; check the specific task implementation to determine if it returns a `File`, `OutputStream`, or generic `D`.
- This method does not perform the write operation; it only returns the reference to the configured destination.

### Prompt Snippet
```text
// Example of retrieving the destination from a task
Object destination = task.getDestination();
```

### Common Failure Modes
- **NullPointerException**: Occurs if the task has not been properly initialized with a destination sink before calling this method.
- **ClassCastException**: May occur if the caller assumes a specific return type (e.g., `File`) when the task is configured to output to a different type (e.g., `OutputStream`).
- **Invalid State**: Calling this method on a task that has not yet been configured with an output sink will return null or throw an exception depending on the specific task implementation.

### Fix Code Hint
```java
// Ensure the task is fully configured before accessing the destination
if (task.getDestination() != null) {
    // Proceed with logic using the destination
}
```

## API Test: `getExifOrientation`

### Signature
```java
public static Orientation getExifOrientation(ImageReader reader, int imageIndex)
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/ExifUtils.java:69_

_Source doc:_ Returns the orientation obtained from the Exif metadata. @param reader		An {@link ImageReader} which is reading the target image. @param imageIndex	The index of the image from which the Exif metadata should be read from. @return				The orientation information obtained from the Exif metadata, as a {@link Orientation} enum. Returns {@code null} if no orientation is found. @throws IOException				When an error occurs during reading. @throws IllegalArgumentException	If the {@link ImageReader} does not have the target image set, or if the reader does not have a JPEG open.

### Goal
Retrieves the EXIF orientation metadata from a specified image within an `ImageReader` to determine if the image requires rotation or flipping for correct display.

### Parameters
- `reader` (`ImageReader`): An initialized `ImageReader` instance that has been configured with an input source (e.g., `ImageInputStream`).
- `imageIndex` (`int`): The zero-based index of the image within the file from which to extract metadata.

### Input
- **Data:** Requires an `ImageReader` that has successfully opened a JPEG image.
- **Preconditions:** The `ImageReader` must have its input set (via `setInput`) before calling this method. The underlying image format must support EXIF metadata (specifically JPEG).

### Output
Returns `Orientation` — An `Orientation` enum representing the EXIF orientation tag value. Returns `null` if no EXIF orientation metadata is present in the specified image.

### Valid Call Patterns
```java
ImageReader reader = ImageIO.getImageReadersByFormatName("jpg").next();
reader.setInput(ImageIO.createImageInputStream(new File("image.jpg")));
Orientation orientation = ExifUtils.getExifOrientation(reader, 0);
```

### LLM Instruction Prompt
- Always ensure the `ImageReader` is initialized with a JPEG source before invoking `getExifOrientation`.
- Handle the `null` return value, as not all JPEG images contain EXIF orientation metadata.
- Wrap the call in a try-catch block to handle `IOException` and `IllegalArgumentException`.

### Prompt Snippet
```text
ExifUtils.getExifOrientation(reader, index)
```

### Common Failure Modes
- `IllegalArgumentException`: Thrown if the `ImageReader` has not had its input set or if the reader is not currently processing a JPEG image.
- `IOException`: Thrown if an error occurs while reading the image metadata from the stream.
- `IndexOutOfBoundsException`: May occur if the `imageIndex` provided is invalid for the given `ImageReader`.

### Fix Code Hint
```java
try {
    Orientation orientation = ExifUtils.getExifOrientation(reader, 0);
    if (orientation != null) {
        // Apply transformation based on orientation
    }
} catch (IOException e) {
    // Handle read error
} catch (IllegalArgumentException e) {
    // Handle invalid reader state or non-JPEG format
}
```

## API Test: `getFilterForOrientation`

### Signature
```java
public static ImageFilter getFilterForOrientation(Orientation orientation)
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/ExifFilterUtils.java:56_

_Source doc:_ Returns a {@link ImageFilter} which will perform the transformations required to properly orient the thumbnail according to the Exif orientation. @param orientation	The Exif orientation @return				{@link ImageFilter}s required to properly orient the image.

### Goal
Provides an `ImageFilter` that applies the necessary geometric transformations to correct an image's orientation based on its EXIF metadata.

### Parameters
- `orientation` (`Orientation`): The specific EXIF orientation state (e.g., rotated or flipped) that the filter should counteract.

### Input
- **Data:** Requires an `Orientation` object, typically derived from image metadata using `Orientation.typeOf(int)`.
- **Preconditions:** The caller must have identified the EXIF orientation value from the source image. The resulting `ImageFilter` is intended to be passed to the `addFilter` method of a `Thumbnails.Builder` pipeline.

### Output
Returns `ImageFilter` — A filter implementation that, when applied to a `BufferedImage` within a Thumbnailator pipeline, performs the necessary rotation or flipping to normalize the image orientation.

### Valid Call Patterns
```java
BufferedImage result =
        Thumbnails.of(sourceImage)
                .addFilter(ExifFilterUtils.getFilterForOrientation(Orientation.typeOf(orientationValue)))
                .size(width, height)
                .asBufferedImage();
```

### LLM Instruction Prompt
- When correcting image orientation, always use `ExifFilterUtils.getFilterForOrientation` in conjunction with `Orientation.typeOf(int)`.
- Ensure the filter is added to the `Thumbnails.Builder` pipeline using the `.addFilter()` method before the final output operation.
- Do not attempt to manually rotate images if EXIF orientation data is available; use this utility to ensure deterministic, library-standard transformation.

### Prompt Snippet
```text
ExifFilterUtils.getFilterForOrientation(Orientation.typeOf(orientationValue))
```

### Common Failure Modes
- **Null Orientation:** Passing a null `Orientation` object will result in a `NullPointerException`.
- **Incorrect Orientation Value:** Providing an invalid integer to `Orientation.typeOf()` may result in unexpected orientation behavior or runtime exceptions depending on the implementation of the `Orientation` enum.
- **Pipeline Order:** Adding the filter after the image has already been processed or resized may lead to unexpected coordinate transformations; always add the filter to the builder pipeline before executing the thumbnail generation.

### Fix Code Hint
```java
// Ensure the orientation value is valid before creating the filter
Orientation orientation = Orientation.typeOf(exifValue);
if (orientation != null) {
    builder.addFilter(ExifFilterUtils.getFilterForOrientation(orientation));
}
```

## API Test: `getFilters`

### Signature
```java
public List<ImageFilter> getFilters()
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Pipeline.java:140_

_Source doc:_ Returns a list of {@link ImageFilter}s which will be applied by this {@link Pipeline}. @return					A list of filters which are applied by this pipeline.

### Goal
Retrieves the ordered collection of `ImageFilter` objects currently registered to be executed by the `Pipeline` during image processing.

### Parameters
_None._

### Input
The `Pipeline` instance must be initialized with one or more `ImageFilter` implementations (such as `Watermark`, `Caption`, `Flip`, or `Rotation`). No specific file formats are required as input, as this method operates on the internal state of the `Pipeline` object.

### Output
Returns `List<ImageFilter>` — A list containing the `ImageFilter` objects in the order they will be applied to the image during the pipeline execution.

### Valid Call Patterns
```java
// Retrieving filters from a pipeline initialized with a list
Pipeline pipeline = new Pipeline(filters);
List<ImageFilter> returned = pipeline.getFilters();

// Retrieving filters from a pipeline initialized with varargs
Pipeline pipeline = new Pipeline(filter1, filter2);
List<ImageFilter> returned = pipeline.getFilters();
```

### LLM Instruction Prompt
- Use `getFilters()` only on an instance of `net.coobird.thumbnailator.filters.Pipeline`.
- Do not assume the returned list is mutable; treat the returned `List<ImageFilter>` as a read-only view of the pipeline's current filter configuration.
- Ensure the `Pipeline` has been instantiated before calling this method to avoid `NullPointerException`.

### Prompt Snippet
```text
// Example of checking pipeline configuration
List<ImageFilter> activeFilters = pipeline.getFilters();
if (activeFilters.isEmpty()) {
    // Handle case where no filters are applied
}
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `Pipeline` object itself has not been initialized.
- **Unexpected Ordering**: The returned list reflects the order of application; modifying the list externally (if the implementation allows) will not affect the `Pipeline`'s internal execution order unless the `Pipeline` is re-instantiated.

### Fix Code Hint
```java
// Ensure the pipeline is properly instantiated before accessing filters
Pipeline pipeline = new Pipeline(myFilterList);
List<ImageFilter> filters = pipeline.getFilters();
```

## API Test: `getFormatName`

### Signature
```java
public String getFormatName()
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/UnsupportedFormatException.java:80_

_Source doc:_ Returns the format name which is not supported. @return			Format name.

### Goal
Retrieves the name of the image format that triggered an `UnsupportedFormatException` during a thumbnail processing task.

### Parameters
_None._

### Input
The caller must have caught an `UnsupportedFormatException` instance thrown by the Thumbnailator library when attempting to process an image format not supported by the host JVM's `ImageIO` capabilities.

### Output
Returns `String` — The name of the unsupported image format (e.g., "gif", "bmp") that caused the exception.

### Valid Call Patterns
```java
try {
    Thumbnails.of("image.unknown")
        .size(100, 100)
        .toFile("output.jpg");
} catch (UnsupportedFormatException e) {
    String format = e.getFormatName();
    System.err.println("Format not supported: " + format);
}
```

### LLM Instruction Prompt
- Use `getFormatName()` only when handling an `UnsupportedFormatException` caught during a Thumbnailator pipeline execution.
- Do not assume the returned string corresponds to a valid format; it represents the specific format that the underlying `ImageIO` implementation failed to process.

### Prompt Snippet
```text
// Example of accessing the unsupported format name
catch (UnsupportedFormatException e) {
    String unsupported = e.getFormatName();
    // ... handle error
}
```

### Common Failure Modes
- **Null Pointer Exception:** Calling `getFormatName()` on an object that is not an instance of `UnsupportedFormatException`.
- **Environment Mismatch:** Assuming a format is "unsupported" globally when it is only unsupported on the specific host JVM (e.g., GIF support in early Java 5 environments).
- **Misinterpretation:** Treating the returned string as a valid MIME type; the return value is the format name string as identified by the library's internal task abstraction.

### Fix Code Hint
```java
// Ensure the exception is caught before accessing the format name
catch (UnsupportedFormatException e) {
    logger.error("The format '{}' is not supported by the current JVM.", e.getFormatName());
}
```

## API Test: `getHeightScalingFactor`

### Signature
```java
public double getHeightScalingFactor()
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:840_

_Source doc:_ Returns the scaling factor to apply to the height when creating the thumbnail. <p> Returns {@link Double#NaN} if the thumbnail size is set rather than the scaling factor. @return		The height scaling factor for the thumbnail. @since	0.3.10

### Goal
Retrieves the vertical scaling factor configured for a thumbnail operation to determine the height transformation ratio.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No external file formats or preconditions are required for this getter, as it operates on the internal state of the parameter configuration.

### Output
Returns `double` — The height scaling factor as a decimal value. Returns `Double.NaN` if the thumbnail dimensions were defined using an absolute size (e.g., `.size(width, height)`) rather than a scaling factor.

### Valid Call Patterns
```java
// Example derived from ThumbnailParameterBuilderTest
ThumbnailParameter param = new ThumbnailParameterBuilder()
        .size(100, 100)
        .build();

double heightFactor = param.getHeightScalingFactor();
```

### LLM Instruction Prompt
- Use `getHeightScalingFactor()` only when inspecting a `ThumbnailParameter` instance.
- Always check the return value against `Double.isNaN()` before using it in calculations, as it will return `NaN` if the thumbnail was configured via `.size()` instead of a scaling factor.

### Prompt Snippet
```text
// Check if scaling factor is set before use
if (!Double.isNaN(param.getHeightScalingFactor())) {
    double factor = param.getHeightScalingFactor();
    // perform scaling logic
}
```

### Common Failure Modes
- **Misinterpreting `NaN`:** Assuming the method returns a numeric value when the `ThumbnailParameter` was configured using `.size(w, h)` instead of `.scale(w, h)`.
- **Null Pointer Exception:** Calling this method on a `null` `ThumbnailParameter` reference.

### Fix Code Hint
```java
// If NaN is returned, the parameter is using absolute sizing.
// Use param.getSize() to retrieve the dimensions instead.
if (Double.isNaN(param.getHeightScalingFactor())) {
    Dimension d = param.getSize();
    // Handle absolute size case
}
```

## API Test: `getImageFilters`

### Signature
```java
public List<ImageFilter> getImageFilters()
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:919_

_Source doc:_ Returns the list of {@link ImageFilter}s which are applied to the thumbnail. <p> These filters are applied after the original image has been resized. @return		The {@link ImageFilter}s which are applied to the thumbnail.

### Goal
Retrieves the ordered list of `ImageFilter` objects configured to be applied to the thumbnail after the initial resizing operation.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No specific input data is required for this getter, but the `ThumbnailParameter` must have been previously configured with filters (e.g., via `ThumbnailParameterBuilder`) to return a non-empty list.

### Output
Returns `List<ImageFilter>` — A list containing the `ImageFilter` implementations (such as `Watermark`, `Caption`, `Flip`, or `Rotation`) that will be processed in the order they appear in the list.

### Valid Call Patterns
```java
// Inferred from ThumbnailParameterBuilderTest.java
ThumbnailParameter param = new ThumbnailParameterBuilder()
        .size(100, 100)
        .build();

List<ImageFilter> filters = param.getImageFilters();
```

### LLM Instruction Prompt
- Use `getImageFilters()` only on an instance of `ThumbnailParameter`.
- Note that the returned list represents filters applied *after* the image has been resized.
- If no filters have been added to the `ThumbnailParameter`, the method returns an empty list (`Collections.emptyList()`).

### Prompt Snippet
```text
// Example of checking filters on a parameter object
List<ImageFilter> filters = parameter.getImageFilters();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `ThumbnailParameter` instance itself is null.
- **Empty List Assumption:** Developers may assume the list contains filters when it is empty; always check `isEmpty()` or the list size before iterating.
- **Modification:** Attempting to modify the returned list may result in an `UnsupportedOperationException` depending on the underlying implementation of the list returned by the `ThumbnailParameter`.

### Fix Code Hint
```java
List<ImageFilter> filters = param.getImageFilters();
if (filters != null && !filters.isEmpty()) {
    // Process filters
}
```

## API Test: `getInputFormatName`

### Signature
```java
public String getInputFormatName()
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/FileImageSource.java:149  (+2 more definition site/overload)_

### Goal
Retrieves the format name of the source image currently held by the image source object.

### Parameters
_None._

### Input
The caller must ensure that the image source has been initialized and that the image data has been read. The format name is determined by the underlying Java Image I/O API based on the source file, stream, or image provided to the `ImageSource`.

### Output
Returns `String` — The name of the image format (e.g., "jpg", "png"). If no image format information is available, the method returns `null`.

### Valid Call Patterns
```java
// Example inferred from test suite usage:
ImageSource source = ...; // An initialized ImageSource
String formatName = source.getInputFormatName();
```

### LLM Instruction Prompt
- Always verify that the `ImageSource` has been read before calling `getInputFormatName`.
- Use this method when you need to determine the original format of an image to perform conditional logic, such as preserving the original format during a transformation task.

### Prompt Snippet
```text
// Ensure the source is read before accessing format metadata
if (source != null) {
    String format = source.getInputFormatName();
}
```

### Common Failure Modes
- **IllegalStateException**: Thrown if the method is called before the source image has been read (e.g., before `read()` is invoked on the `ImageSource`).
- **Null Return**: If the image source does not contain format metadata, the method returns `null`, which may cause a `NullPointerException` if not handled before string manipulation.

### Fix Code Hint
```java
try {
    String format = source.getInputFormatName();
    if (format != null) {
        // Proceed with format-specific logic
    }
} catch (IllegalStateException e) {
    // Handle case where source has not been read yet
}
```

## API Test: `getInstance`

### Signature
```java
public static SwapDimensions getInstance()
public static ResizerFactory getInstance()
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/SwapDimensions.java:40  (+1 more definition site/overload)_

### Goal
Retrieves a singleton instance of the specified class to facilitate image dimension swapping or to provide a factory for image resizing strategies.

### Parameters
_None._

### Input
No input parameters are required. For `ResizerFactory.getInstance()`, ensure the environment is prepared for headless image processing as per the `ImageIO` capabilities of the host JVM.

### Output
Returns `ResizerFactory` or `SwapDimensions` — An instance of the requested class, used to configure thumbnail generation parameters or apply dimension-swapping filters.

### Valid Call Patterns
```java
// Obtaining a ResizerFactory for use in a ThumbnailParameter
ResizerFactory resizerFactory = DefaultResizerFactory.getInstance();

// Obtaining a SwapDimensions instance
SwapDimensions swapper = SwapDimensions.getInstance();
```

### LLM Instruction Prompt
- When using `ResizerFactory.getInstance()`, always assign the result to a `ResizerFactory` type and pass it to the `resizerFactory()` method of a `ThumbnailParameterBuilder` to customize the scaling algorithm.
- Do not attempt to instantiate these classes via `new`; always use the `getInstance()` static factory method.

### Prompt Snippet
```text
ResizerFactory factory = DefaultResizerFactory.getInstance();
ThumbnailParameter param = new ThumbnailParameterBuilder()
    .resizerFactory(factory)
    .build();
```

### Common Failure Modes
- **Type Mismatch:** Attempting to use the `SwapDimensions` instance where a `ResizerFactory` is expected.
- **Environment Constraints:** Relying on specific `Resizer` performance characteristics that are not documented or guaranteed across different JVM implementations.
- **Null Configuration:** Failing to set the `ResizerFactory` in the `ThumbnailParameter` when custom scaling behavior is required, leading to the use of default library settings.

### Fix Code Hint
```java
// Ensure the correct factory is used for the task
ResizerFactory myFactory = DefaultResizerFactory.getInstance();
ThumbnailParameter param = new ThumbnailParameterBuilder()
    .size(100, 100)
    .resizerFactory(myFactory)
    .build();
```

## API Test: `getKey`

### Signature
```java
public RenderingHints.Key getKey()
public Key getKey()
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/configurations/ResizerConfiguration.java:44  (+4 more definition site/overload)_

_Source doc:_ Returns a rendering hint key. @return		Rendering hint key.

### Goal
Retrieves the `RenderingHints.Key` associated with a specific resizer configuration to allow for fine-grained control over Java 2D rendering behavior during thumbnail generation.

### Parameters
_None._

### Input
No input parameters are required. The caller must ensure the object instance (e.g., a `ResizerConfiguration` implementation) is properly initialized within the context of a `ThumbnailParameter` or a custom `Resizer` pipeline.

### Output
Returns `RenderingHints.Key` — The specific key used by the Java 2D `RenderingHints` engine to identify the configuration setting (such as interpolation or antialiasing) applied during the image resizing process.

### Valid Call Patterns
```java
// Inferred from signature:
RenderingHints.Key key = resizerConfiguration.getKey();
```

### LLM Instruction Prompt
- When generating code that interacts with `Thumbnailator` resizer configurations, use `getKey()` to retrieve the hint key required for manual `RenderingHints` map construction.
- Ensure the object calling `getKey()` is a valid `ResizerConfiguration` instance.

### Prompt Snippet
```text
// Example of retrieving a rendering hint key from a configuration
RenderingHints.Key hintKey = myResizerConfiguration.getKey();
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `ResizerConfiguration` instance is null or not properly instantiated before calling `getKey()`.
- **Type Mismatch**: Attempting to use the returned `RenderingHints.Key` with an incompatible `RenderingHints` value type, which may lead to runtime exceptions when the `Graphics2D` object processes the hints.

### Fix Code Hint
```java
// Ensure the configuration is not null before accessing the key
if (resizerConfiguration != null) {
    RenderingHints.Key key = resizerConfiguration.getKey();
    // Proceed with using the key in a RenderingHints map
}
```

## API Test: `getOffsetValue`

### Signature
```java
public int getOffsetValue()
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdStructure.java:93_

_Source doc:_ Returns either the offset or value of the IFD. @return		Either the offset or value. The type of the returned value can be determined by the return of the {@link #isOffset()} or {@link #isValue()} method.

### Goal
Retrieves the raw integer data stored within an Image File Directory (IFD) structure, which represents either a direct value or a memory offset depending on the IFD entry type.

### Parameters
_None._

### Input
The caller must ensure the `IfdStructure` instance has been correctly initialized from an image's EXIF metadata. No external file formats are required as input to this method, but the instance must be in a valid state derived from an image processing task involving EXIF data.

### Output
Returns `int` — The integer value or memory offset contained within the IFD structure. The semantic meaning of this integer is context-dependent; the caller must verify the nature of the data using `isOffset()` or `isValue()` before interpreting the result.

### Valid Call Patterns
```java
// Inferred from signature:
int data = ifdStructure.getOffsetValue();
```

### LLM Instruction Prompt
- When calling `getOffsetValue()`, always precede the call with a check using `isOffset()` or `isValue()` to determine how to interpret the returned integer. Do not assume the return value is a direct pixel coordinate or dimension without verifying the IFD entry type.

### Prompt Snippet
```text
if (ifdStructure.isOffset()) {
    int offset = ifdStructure.getOffsetValue();
    // Handle as memory offset
} else if (ifdStructure.isValue()) {
    int value = ifdStructure.getOffsetValue();
    // Handle as direct value
}
```

### Common Failure Modes
- **Misinterpretation of Data:** Treating a returned offset as a literal value (or vice versa) leads to incorrect metadata parsing.
- **Null Pointer Exception:** Calling this method on an uninitialized or null `IfdStructure` object.
- **Invalid State:** Attempting to access the value before the EXIF metadata has been successfully parsed from the source image.

### Fix Code Hint
```java
// Always verify the type before usage
if (ifdStructure.isValue()) {
    int val = ifdStructure.getOffsetValue();
    // Process value safely
}
```

## API Test: `getOrientationFromExif`

### Signature
```java
public static Orientation getOrientationFromExif(byte[] exifData)
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/ExifUtils.java:109_

_Source doc:_ Returns the orientation obtained from the Exif metadata. @param exifData		A byte array containing Exif data. @return				The orientation information obtained from the Exif metadata, as a {@link Orientation} enum. Returns {@code null} if no orientation is found.

### Goal
Extracts the image orientation metadata from a raw byte array of Exif data to facilitate automatic rotation correction in thumbnail processing.

### Parameters
- `exifData` (`byte[]`): A byte array containing the extracted Exif metadata segment from an image file.

### Input
- **Data:** The caller must provide a valid byte array representing the Exif metadata segment.
- **Preconditions:** The input must be correctly extracted from an image file that contains Exif metadata. This method does not parse the entire image file; it expects only the Exif data segment.

### Output
Returns `Orientation` — An `Orientation` enum value representing the orientation specified in the metadata. Returns `null` if no orientation information is found within the provided byte array.

### Valid Call Patterns
```java
// Inferred from signature:
Orientation orientation = ExifUtils.getOrientationFromExif(exifData);
```

### LLM Instruction Prompt
- When calling `getOrientationFromExif`, ensure the input `byte[]` is the raw Exif metadata segment. If the source is an image file, use appropriate I/O utilities to isolate the Exif segment before passing it to this method. Handle the potential `null` return value, which indicates that no orientation metadata was present.

### Prompt Snippet
```text
ExifUtils.getOrientationFromExif(byte[] exifData)
```

### Common Failure Modes
- **Null Input:** Passing a `null` byte array will result in a `NullPointerException`.
- **Invalid Data:** Passing a byte array that does not contain valid Exif data may result in an inability to parse the orientation, returning `null` or potentially throwing an exception depending on the internal parsing logic.
- **Missing Metadata:** If the image does not contain Exif orientation tags, the method returns `null`; ensure the calling code checks for this to avoid `NullPointerException` when accessing the enum.

### Fix Code Hint
```java
Orientation orientation = ExifUtils.getOrientationFromExif(exifData);
if (orientation != null) {
    // Apply rotation based on orientation
}
```

## API Test: `getOutputFormat`

### Signature
```java
public String getOutputFormat()
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:878_

_Source doc:_ Returns the output format for the thumbnail. <p> If the output format is to use the same compression format as the original image, this method will return {@link ThumbnailParameter#ORIGINAL_FORMAT}. <p> If the output format should be determined from the information available such as the file name of the thumbnail, then this method will return {@link ThumbnailParameter#DETERMINE_FORMAT}. @return 		The output format for the thumbnail.

### Goal
Retrieves the configured output format string for a thumbnail task, which dictates the compression format used during the image writing process.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No specific input arguments are required for this method, as it acts as a getter for the internal state of the parameter configuration.

### Output
Returns `String` — The output format identifier. This is typically a format name (e.g., "jpg", "png"), or one of the special constants: `ThumbnailParameter.ORIGINAL_FORMAT` (to match the source image) or `ThumbnailParameter.DETERMINE_FORMAT` (to infer the format from the output file extension).

### Valid Call Patterns
```java
ThumbnailParameter param = new ThumbnailParameterBuilder()
        .size(100, 100)
        .build();

String format = param.getOutputFormat();
```

### LLM Instruction Prompt
- When generating code that inspects thumbnail settings, use `getOutputFormat()` to verify the current format configuration.
- Always compare the returned value against `ThumbnailParameter.ORIGINAL_FORMAT` or `ThumbnailParameter.DETERMINE_FORMAT` if you need to handle dynamic format logic.
- Do not assume the return value is a standard file extension without checking against the special constants provided by the `ThumbnailParameter` class.

### Prompt Snippet
```text
// Check the output format of the current ThumbnailParameter
String currentFormat = parameter.getOutputFormat();
if (currentFormat.equals(ThumbnailParameter.ORIGINAL_FORMAT)) {
    // Handle original format logic
}
```

### Common Failure Modes
- **Null Pointer Exception:** Calling `getOutputFormat()` on a null `ThumbnailParameter` instance.
- **Misinterpretation of Constants:** Assuming the return value is a standard format string (like "jpg") without accounting for the `DETERMINE_FORMAT` constant, which may cause issues if the logic expects a concrete format name before the file path is known.
- **Environment Dependency:** Relying on the returned format string to be supported by the host JVM; while `getOutputFormat()` returns the configuration, the actual writing process will fail if the underlying `ImageIO` does not support the specific format returned.

### Fix Code Hint
```java
// Ensure the parameter is initialized before calling the getter
if (param != null) {
    String format = param.getOutputFormat();
    // Proceed with format-specific logic
}
```

## API Test: `getOutputFormatType`

### Signature
```java
public String getOutputFormatType()
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:891_

_Source doc:_ Returns the output format type for the thumbnail. <p> If the default compression type of the compression format is to be used, then this method will return {@link ThumbnailParameter#DEFAULT_FORMAT_TYPE}. @return 		The output format type for the thumbnail.

### Goal
Retrieves the specific compression or format type configuration currently assigned to a `ThumbnailParameter` instance for the thumbnail output.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No specific input arguments are required for this method, as it acts as a getter for the internal state of the parameter configuration.

### Output
Returns `String` — The output format type (e.g., a specific compression type for a format like JPEG). If no specific type has been set, it returns `ThumbnailParameter.DEFAULT_FORMAT_TYPE`.

### Valid Call Patterns
```java
ThumbnailParameter param = new ThumbnailParameterBuilder()
        .size(100, 100)
        .build();

String formatType = param.getOutputFormatType();
```

### LLM Instruction Prompt
- Use `getOutputFormatType()` only on an instance of `ThumbnailParameter`.
- Do not assume the returned string is a file extension; it represents the internal format type/compression setting used by the underlying `ImageWriter`.
- When comparing the result, use `ThumbnailParameter.DEFAULT_FORMAT_TYPE` to check if the default configuration is active.

### Prompt Snippet
```text
// Check if the thumbnail parameter is using the default format type
if (param.getOutputFormatType().equals(ThumbnailParameter.DEFAULT_FORMAT_TYPE)) {
    // Logic for default compression
}
```

### Common Failure Modes
- **NullPointerException:** Calling this method on a null `ThumbnailParameter` reference.
- **Misinterpretation:** Confusing the "format type" (e.g., compression type) with the "output format" (e.g., "jpg", "png"), which is retrieved via `getOutputFormat()`.

### Fix Code Hint
```java
// Ensure the parameter object is instantiated before calling the getter
ThumbnailParameter param = builder.build();
if (param != null) {
    String type = param.getOutputFormatType();
}
```

## API Test: `getOutputQuality`

### Signature
```java
public float getOutputQuality()
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:907_

_Source doc:_ Returns the compression quality settings for the thumbnail. <p> The value is in the range of {@code 0.0f} to {@code 1.0f}, where {@code 0.0f} is for the lowest quality setting and {@code 1.0f} for the highest quality setting. <p> If the default compression quality is to be used, then this method will return {@link ThumbnailParameter#DEFAULT_QUALITY}. @return 		The compression quality settings for the thumbnail.

### Goal
Retrieves the compression quality setting currently configured for a `ThumbnailParameter` instance to determine the level of lossy compression applied during thumbnail generation.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. If the parameter object was created via `ThumbnailParameterBuilder` without explicitly setting the quality, the value may be `Float.NaN`.

### Output
Returns `float` — A value between `0.0f` (lowest quality) and `1.0f` (highest quality), or `ThumbnailParameter.DEFAULT_QUALITY` if the default is used.

### Valid Call Patterns
```java
// Inferred from ThumbnailParameter usage
ThumbnailParameter param = new ThumbnailParameterBuilder().size(100, 100).build();
float quality = param.getOutputQuality();
```

### LLM Instruction Prompt
- When calling `getOutputQuality`, ensure the `ThumbnailParameter` instance has been properly initialized. 
- Note that if the quality has not been explicitly set, the method may return `Float.NaN` rather than a valid quality range value.
- Use this method only to inspect the configuration of a `ThumbnailParameter` object, not to set the quality (use the builder or setter for configuration).

### Prompt Snippet
```text
ThumbnailParameter param = ...;
float currentQuality = param.getOutputQuality();
```

### Common Failure Modes
- **NaN Handling:** If the `ThumbnailParameter` was built without specifying quality, `getOutputQuality()` returns `Float.NaN`. Failing to check for `Float.isNaN()` before using this value in calculations or UI displays can lead to unexpected behavior.
- **Context Misunderstanding:** Attempting to call this on a `Thumbnails.Builder` object directly; this method belongs to the `ThumbnailParameter` configuration object, not the fluent pipeline builder.

### Fix Code Hint
```java
float quality = param.getOutputQuality();
if (Float.isNaN(quality)) {
    // Handle default or uninitialized state
    quality = 0.75f; 
}
```

## API Test: `getParam`

### Signature
```java
public ThumbnailParameter getParam()
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/FileThumbnailTask.java:81  (+2 more definition site/overload)_

### Goal
Retrieves the `ThumbnailParameter` configuration object associated with a `ThumbnailTask`, which defines the settings used for the thumbnail generation operation.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailTask` (such as `FileThumbnailTask` or `StreamThumbnailTask`). No specific input is required for the method itself, as it acts as a getter for the parameters already encapsulated within the task instance.

### Output
Returns `ThumbnailParameter` — The configuration object containing settings such as target dimensions, image type, quality, and the `Resizer` implementation used during the thumbnail generation process.

### Valid Call Patterns
```java
// Example from FileThumbnailTaskTest
FileThumbnailTask task = new FileThumbnailTask(param, inputFile, outputFile);
ThumbnailParameter retrievedParam = task.getParam();

// Example from StreamThumbnailTaskTest
StreamThumbnailTask task = new StreamThumbnailTask(param, is, os);
ThumbnailParameter retrievedParam = task.getParam();
```

### LLM Instruction Prompt
- When generating code involving `ThumbnailTask` subclasses, use `getParam()` to verify or inspect the configuration of a task before execution.
- Ensure the `ThumbnailParameter` object was correctly initialized with valid dimensions and `Resizer` settings before calling this method to retrieve it.

### Prompt Snippet
```text
ThumbnailParameter params = task.getParam();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `ThumbnailTask` instance is null.
- **Task State Misconception:** Attempting to modify the returned `ThumbnailParameter` object may not affect the task if the task has already begun processing or if the parameter object is treated as immutable by the specific task implementation.
- **Contextual Mismatch:** Calling `getParam()` on a task that has not been properly initialized with a `ThumbnailParameter` (if the constructor allows nulls) will return null.

### Fix Code Hint
```java
// Ensure the task is initialized before accessing parameters
if (task != null) {
    ThumbnailParameter params = task.getParam();
    // Proceed with parameter inspection
}
```

## API Test: `getPosition`

### Signature
```java
public Position getPosition()
```
_Source: source/src/main/java/net/coobird/thumbnailator/geometry/Region.java:77_

_Source doc:_ Returns the position of the region. @return 				Position of the region.

### Goal
Retrieves the `Position` object defining the placement or alignment of a region within an image, typically used for cropping or watermarking operations.

### Parameters
_None._

### Input
The caller must ensure the object instance (e.g., a `Region` object) has been properly initialized with a valid `Position` implementation, such as those provided by `net.coobird.thumbnailator.geometry.Positions`.

### Output
Returns `Position` — An object representing the coordinate-based or alignment-based position (e.g., `Positions.CENTER`, `Positions.BOTTOM_RIGHT`) assigned to the region.

### Valid Call Patterns
```java
// Inferred from signature:
Position pos = region.getPosition();
```

### LLM Instruction Prompt
- Use `getPosition()` only when interacting with objects that implement the `Region` interface or class.
- Do not assume the returned `Position` is a specific implementation (like `Positions` enum) without checking, as it may be a custom implementation of the `Position` interface.

### Prompt Snippet
```text
// Example of retrieving the position from a region object
Position currentPosition = region.getPosition();
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `Region` object itself is null.
- **Logic Errors**: Assuming the returned `Position` is a specific enum constant when it may be a custom implementation of the `Position` interface.

### Fix Code Hint
```java
// Ensure the region is not null before accessing its position
if (region != null) {
    Position pos = region.getPosition();
    // Proceed with logic
}
```

## API Test: `getProxy`

### Signature
```java
public Proxy getProxy()
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/URLImageSource.java:180_

_Source doc:_ Returns the proxy to use when connecting to the URL to retrieve the source image. @return the proxy	The proxy used to connect to a URL.

### Goal
Retrieves the `java.net.Proxy` configuration currently assigned to a `URLImageSource` for fetching remote images.

### Parameters
_None._

### Input
The caller must ensure the `URLImageSource` instance has been previously configured with a proxy setting. Note that while Thumbnailator is designed for headless operation, this method specifically pertains to instances where a `URL` is used as the image source.

### Output
Returns `Proxy` — The `java.net.Proxy` object used to establish the network connection to the source URL, or `null` if no proxy has been configured.

### Valid Call Patterns
```java
// Inferred from signature:
Proxy proxy = urlImageSource.getProxy();
```

### LLM Instruction Prompt
- When generating code that interacts with `URLImageSource`, use `getProxy()` to verify the network configuration before attempting to retrieve an image from a remote URL.
- Ensure the `Proxy` object is handled according to standard `java.net` conventions.

### Prompt Snippet
```text
Proxy currentProxy = urlImageSource.getProxy();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `URLImageSource` instance is null.
- **Uninitialized Proxy:** If no proxy was explicitly set on the `URLImageSource`, this method returns `null`, which may cause issues if the calling code expects a valid proxy object to be present.
- **Network Constraints:** Although this method returns a `Proxy`, remember that the project is designed for headless environments; ensure that the environment's security policy permits network access if a proxy is being utilized.

### Fix Code Hint
```java
Proxy proxy = urlImageSource.getProxy();
if (proxy != null) {
    // Proceed with proxy-aware logic
} else {
    // Handle case where no proxy is configured
}
```

## API Test: `getRenderingHints`

### Signature
```java
public Map<RenderingHints.Key, Object> getRenderingHints()
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/AbstractResizer.java:146_

_Source doc:_ Returns the rendering hints that the resizer uses. <p> The keys and values used for the rendering hints are those defined in the {@link RenderingHints} class. @see RenderingHints @return		Rendering hints used when resizing the image.

### Goal
Retrieves the current configuration of `RenderingHints` applied by a specific `Resizer` implementation to control the quality and performance of image scaling operations.

### Parameters
_None._

### Input
The caller must have an initialized `Resizer` instance (e.g., `BicubicResizer`, `BilinearResizer`, or `ProgressiveBilinearResizer`). No specific input parameters are required to invoke this method.

### Output
Returns `Map<RenderingHints.Key, Object>` — A map containing the key-value pairs defined in the `java.awt.RenderingHints` class that dictate how the resizer performs image interpolation and rendering.

### Valid Call Patterns
```java
// Inferred from signature:
Resizer resizer = new ProgressiveBilinearResizer();
Map<RenderingHints.Key, Object> hints = resizer.getRenderingHints();
```

### LLM Instruction Prompt
- Use `getRenderingHints()` only on instances of classes extending `AbstractResizer`.
- Do not attempt to modify the returned `Map` directly if the implementation treats it as immutable or internal state.
- Ensure the context involves Java 2D image processing where `RenderingHints` are relevant to the scaling algorithm.

### Prompt Snippet
```text
// Accessing resizer configuration
Map<RenderingHints.Key, Object> hints = resizerInstance.getRenderingHints();
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `Resizer` instance is null.
- **Unsupported Operation**: Attempting to cast or modify the returned map may result in `UnsupportedOperationException` if the underlying implementation returns an unmodifiable map.
- **Context Mismatch**: Calling this on a custom resizer that does not extend `AbstractResizer` or does not implement the expected rendering hint logic.

### Fix Code Hint
```java
// Ensure the resizer is properly instantiated before accessing hints
if (resizer != null) {
    Map<RenderingHints.Key, Object> hints = resizer.getRenderingHints();
    // Use hints for logging or verification purposes
}
```

## API Test: `getResizer`

### Signature
```java
public Resizer getResizer()
public Resizer getResizer(Dimension originalSize, Dimension thumbnailSize)
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/DefaultResizerFactory.java:126  (+6 more definition site/overload)_

### Goal
Returns a suitable `Resizer` implementation based on the dimensions of the original image and the target thumbnail to ensure optimal scaling quality.

### Parameters
- `originalSize` (`Dimension`): The dimensions of the source image being processed.
- `thumbnailSize` (`Dimension`): The target dimensions for the resulting thumbnail.

### Input
The caller must provide valid `Dimension` objects representing the source and target sizes. The method is typically invoked by the `Thumbnailator` internal task execution logic when a `ResizerFactory` is configured in the `ThumbnailParameter`.

### Output
Returns `Resizer` — A concrete implementation of the `Resizer` interface (e.g., `BicubicResizer`, `BilinearResizer`, or `ProgressiveBilinearResizer`) selected by the factory to perform the resizing operation.

### Valid Call Patterns
```java
// Using the DefaultResizerFactory instance to obtain a resizer
ResizerFactory factory = DefaultResizerFactory.getInstance();
Resizer resizer = factory.getResizer(new Dimension(200, 200), new Dimension(100, 100));
```

### LLM Instruction Prompt
- When generating code that requires custom resizing logic, use `DefaultResizerFactory.getInstance()` to obtain a `ResizerFactory`.
- Always pass `Dimension` objects representing the source and target sizes to `getResizer` to ensure the factory selects the most appropriate algorithm for the scaling ratio.
- Do not attempt to instantiate `Resizer` implementations directly; rely on the factory pattern provided by Thumbnailator.

### Prompt Snippet
```text
// Obtain a resizer for a specific scaling operation
Resizer resizer = DefaultResizerFactory.getInstance().getResizer(originalDim, thumbDim);
```

### Common Failure Modes
- **Null Dimensions:** Passing `null` for `originalSize` or `thumbnailSize` will result in a `NullPointerException`.
- **Invalid Dimensions:** Providing dimensions with zero or negative values may lead to unexpected behavior or `IllegalArgumentException` depending on the specific `Resizer` implementation returned.
- **Factory Misconfiguration:** If a custom `ResizerFactory` is used, ensure it is correctly registered via `ThumbnailParameterBuilder.resizerFactory()` to avoid using default logic when specific scaling behavior is required.

### Fix Code Hint
```java
// Ensure dimensions are positive and non-null before calling
if (originalSize != null && thumbnailSize != null && originalSize.width > 0 && thumbnailSize.width > 0) {
    Resizer resizer = DefaultResizerFactory.getInstance().getResizer(originalSize, thumbnailSize);
}
```

## API Test: `getResizerFactory`

### Signature
```java
public ResizerFactory getResizerFactory()
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:942_

_Source doc:_ Returns the {@link ResizerFactory} for obtaining a {@link Resizer} which is to be used when performing the resizing operation to create a thumbnail. @return		The {@link ResizerFactory} to use to obtain the {@link Resizer}.

### Goal
Retrieves the `ResizerFactory` instance currently configured within a `ThumbnailParameter` object to determine the scaling algorithm used during thumbnail generation.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object, typically constructed via `ThumbnailParameterBuilder`. No specific file formats or external data are required as input to this method, as it acts as a getter for the internal configuration state.

### Output
Returns `ResizerFactory` — The factory responsible for providing a `Resizer` implementation (such as `BicubicResizer` or `ProgressiveBilinearResizer`) based on the dimensions of the source and destination images.

### Valid Call Patterns
```java
// Retrieving the factory from a configured ThumbnailParameter
ThumbnailParameter param = new ThumbnailParameterBuilder()
    .scale(0.5)
    .resizer(Resizers.BICUBIC)
    .build();

ResizerFactory factory = param.getResizerFactory();
```

### LLM Instruction Prompt
- When generating code that inspects or modifies thumbnail parameters, use `getResizerFactory()` to verify the current scaling strategy.
- Ensure the `ThumbnailParameter` object has been fully built using `ThumbnailParameterBuilder` before calling this method to avoid unexpected null or default states.
- Do not assume the returned `ResizerFactory` is a specific implementation; treat it as the `ResizerFactory` interface.

### Prompt Snippet
```text
// Accessing the ResizerFactory from a ThumbnailParameter instance
ResizerFactory factory = thumbnailParameter.getResizerFactory();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `ThumbnailParameter` instance is null or if the `ThumbnailParameter` was not properly initialized via the builder.
- **Unexpected Default:** If no specific resizer was set during the builder phase, the factory may return a default implementation that might not meet specific performance or quality requirements for high-throughput headless environments.
- **Type Mismatch:** Attempting to cast the returned `ResizerFactory` to a specific concrete class (e.g., `FixedResizerFactory`) without checking the type, which may lead to `ClassCastException`.

### Fix Code Hint
```java
// Ensure the parameter is initialized before accessing the factory
if (param != null) {
    ResizerFactory factory = param.getResizerFactory();
    // Proceed with logic using the factory
}
```

## API Test: `getSink`

### Signature
```java
public OutputStream getSink()
public T getSink()
public File getSink()
public BufferedImage getSink()
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/OutputStreamImageSink.java:268  (+3 more definition site/overload)_

### Goal
Retrieves the destination object (sink) where the processed thumbnail has been stored by a `ThumbnailTask`.

### Parameters
_None._

### Input
The caller must ensure that a thumbnail processing task has been successfully executed and written to the specific sink implementation (e.g., `BufferedImageSink`, `FileImageSink`, or `OutputStreamImageSink`). No input parameters are required for the retrieval method itself.

### Output
Returns the sink object (e.g., `BufferedImage`, `File`, or `OutputStream`) that holds the resulting thumbnail data.

### Valid Call Patterns
```java
// Retrieving a BufferedImage after task execution
BufferedImageSink destination = new BufferedImageSink();
// ... perform task ...
BufferedImage thumbnail = destination.getSink();
```

### LLM Instruction Prompt
- Use `getSink()` only after a `ThumbnailTask` has been performed on the corresponding sink instance.
- Ensure the sink type matches the expected return type (e.g., `BufferedImageSink` returns `BufferedImage`).
- Do not call `getSink()` on a sink that has not yet received image data.

### Prompt Snippet
```text
// Ensure the task is completed before calling getSink()
Thumbnailator.createThumbnail(task);
BufferedImage result = destination.getSink();
```

### Common Failure Modes
- **IllegalStateException**: Thrown if `getSink()` is called before a thumbnail has been written to the sink.
- **Type Mismatch**: Attempting to cast the return value to an incorrect type if the wrong sink implementation was used in the pipeline.

### Fix Code Hint
```java
// Ensure the task is executed first
Thumbnailator.createThumbnail(task);

// Then safely retrieve the result
try {
    BufferedImage thumbnail = destination.getSink();
} catch (IllegalStateException e) {
    // Handle case where task was not executed
}
```

## API Test: `getSize`

### Signature
```java
public Dimension getSize()
public Size getSize()
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:808  (+1 more definition site/overload)_

_Source doc:_ Returns the size of the thumbnail. <p> Returns {@code null} if the scaling factor is set rather than the explicit thumbnail size. @return		The size of the thumbnail.

### Goal
Retrieves the explicitly defined dimensions of the thumbnail currently configured within a `ThumbnailParameter` instance.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. Precondition: The thumbnail size must have been explicitly set via the pipeline configuration; if a scaling factor (e.g., `scale(double)`) was used instead of an explicit size, this method will return `null`.

### Output
Returns `Dimension` — The width and height of the thumbnail as a `java.awt.Dimension` object, or `null` if the thumbnail size is determined by a scaling factor rather than explicit dimensions.

### Valid Call Patterns
```java
// Inferred from test suite usage (ThumbnailatorTest.java)
Dimension dim = thumbnailParameter.getSize();
```

### LLM Instruction Prompt
- When calling `getSize()`, always verify if the `ThumbnailParameter` was configured with an explicit size (e.g., `.size(width, height)`) rather than a scaling factor.
- If the pipeline uses scaling, expect a `null` return value and handle it accordingly to avoid `NullPointerException`.
- Use this method primarily when inspecting the configuration of a `ThumbnailParameter` object during batch processing or pipeline validation.

### Prompt Snippet
```text
// Example of checking thumbnail size configuration
ThumbnailParameter param = ...;
Dimension size = param.getSize();
if (size != null) {
    System.out.println("Thumbnail size: " + size.width + "x" + size.height);
} else {
    System.out.println("Thumbnail size is determined by scaling factor.");
}
```

### Common Failure Modes
- **NullPointerException:** Occurs if the code assumes `getSize()` always returns a `Dimension` object when the pipeline was actually configured using a scaling factor.
- **Logic Errors:** Misinterpreting a `null` return as a zero-dimension image rather than a scaling-based configuration.

### Fix Code Hint
```java
// Always check for null before accessing Dimension properties
Dimension dim = param.getSize();
if (dim != null) {
    int w = dim.width;
    int h = dim.height;
} else {
    // Handle scaling-based configuration
}
```

## API Test: `getSource`

### Signature
```java
public S getSource()
public File getSource()
public T getSource()
public URL getSource()
```
_Source: net.coobird.thumbnailator.tasks.io.FileImageSource, net.coobird.thumbnailator.tasks.io.URLImageSource, and related task abstractions._

### Goal
Retrieves the original source object (such as a `File`, `URL`, or stream) from which the image data is being read for the thumbnail processing task.

### Parameters
_None._

### Input
The caller must have initialized an image source object (e.g., `FileImageSource`, `URLImageSource`) within a thumbnail task. The source must be a valid image format supported by the host JVM's `ImageIO` capabilities.

### Output
Returns `S` — The source object (e.g., `File`, `URL`, or `InputStream`) that was provided during the initialization of the image source handler.

### Valid Call Patterns
```java
// Example from FileImageSourceTest.java
File inputFile = new File("path/to/image.png");
FileImageSource source = new FileImageSource(inputFile);
source.read();
File retrievedSource = source.getSource();
```

### LLM Instruction Prompt
- Use `getSource()` only on initialized image source objects within the `net.coobird.thumbnailator.tasks.io` package.
- Do not assume the return type is always `File`; check the specific implementation of the source handler (e.g., `URLImageSource` returns `URL`).
- Ensure `read()` has been called or the source has been properly instantiated before invoking `getSource()`.

### Prompt Snippet
```text
// Retrieve the source from an existing FileImageSource instance
File sourceFile = fileImageSource.getSource();
```

### Common Failure Modes
- **NullPointerException**: Occurs if the source handler was not properly initialized before calling `getSource()`.
- **Type Mismatch**: Attempting to cast the return value to `File` when the source was initialized with a `URL` or `InputStream`.
- **Post-Read State**: While `getSource()` remains valid after a `read()` operation, ensure that the underlying resource (like a file) has not been deleted or moved if further processing is required.

### Fix Code Hint
```java
// Ensure the source is checked for the correct type before casting
Object source = imageSource.getSource();
if (source instanceof File) {
    File file = (File) source;
    // Proceed with file-specific logic
}
```

## API Test: `getSourceRegion`

### Signature
```java
public Region getSourceRegion()
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:967_

_Source doc:_ Returns the region of the source image to use when creating a thumbnail, represented by a {@link Region} object. @return		The {@code Region} object representing the source region to use when creating a thumbnail. <p> A value of {@code null} indicates that the entire source image should be used to create the thumbnail.

### Goal
Retrieves the defined cropping area or sub-region of the source image that will be processed to generate the thumbnail.

### Parameters
_None._

### Input
The caller must ensure the `ThumbnailParameter` object has been initialized. No specific input parameters are required for this getter method.

### Output
Returns `Region` — A `Region` object defining the specific portion of the source image to be used. If the returned value is `null`, it indicates that the entire source image is used for the thumbnail generation process.

### Valid Call Patterns
```java
// Inferred from signature:
Region region = thumbnailParameter.getSourceRegion();
```

### LLM Instruction Prompt
- When calling `getSourceRegion`, always check if the returned `Region` object is `null`.
- If the result is `null`, treat the operation as using the full dimensions of the source image.
- This method is a member of `ThumbnailParameter`; ensure the instance is properly configured before retrieval.

### Prompt Snippet
```text
// Check if a specific source region is defined
Region region = param.getSourceRegion();
if (region == null) {
    // Use full image
}
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `ThumbnailParameter` instance itself is `null` when attempting to call the method.
- **Misinterpretation of Null:** Failing to handle the `null` return value, which signifies that no specific crop region is applied, leading to unexpected processing of the entire image.

### Fix Code Hint
```java
Region region = param.getSourceRegion();
if (region != null) {
    // Apply logic for specific region
} else {
    // Fallback to full image processing
}
```

## API Test: `getSupportedOutputFormatTypes`

### Signature
```java
public static List<String> getSupportedOutputFormatTypes(String format)
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/ThumbnailatorUtils.java:97_

_Source doc:_ Returns a {@link List} of supported output formats types for a specified output format. @param format	The output format. @return		A {@link List} of supported output formats types. If no formats types are supported, or if compression is not supported for the specified format, then an empty list is returned.

### Goal
Retrieves a list of supported compression types or format-specific subtypes available for a given image format (e.g., "jpg" or "png") to assist in configuring output parameters.

### Parameters
- `format` (`String`): The name of the image format (e.g., "jpg", "png", "bmp") for which to query supported compression types.

### Input
The caller must provide a valid image format string recognized by the host JVM's `ImageIO` registry. The availability of these types is strictly dependent on the underlying Java environment's image processing capabilities.

### Output
Returns `List<String>` — A list of strings representing the supported compression types or format subtypes. If the format is unsupported, or if the format does not support specific compression types, the method returns an empty list.

### Valid Call Patterns
```java
// Inferred from signature:
List<String> jpgTypes = ThumbnailatorUtils.getSupportedOutputFormatTypes("jpg");
```

### LLM Instruction Prompt
- When calling `getSupportedOutputFormatTypes`, ensure the `format` string matches standard `ImageIO` format names.
- Use the returned `List<String>` to validate or populate UI/configuration options for image compression settings.
- Do not assume the list will be non-empty; always handle the empty list case as a valid response indicating no specific compression types are available.

### Prompt Snippet
```text
ThumbnailatorUtils.getSupportedOutputFormatTypes("jpg")
```

### Common Failure Modes
- **Empty List:** Providing a format that does not support compression (e.g., "png" in some environments) will return an empty list; this is expected behavior, not an error.
- **JVM Dependency:** The results are not hardcoded in Thumbnailator but are queried from the host JVM's `ImageIO` SPI; results may vary between different Java versions or environments.
- **Null/Invalid Input:** Passing a null or unrecognized format string may result in an empty list or unexpected behavior depending on the underlying `ImageIO` implementation.

### Fix Code Hint
```java
List<String> types = ThumbnailatorUtils.getSupportedOutputFormatTypes("jpg");
if (!types.isEmpty()) {
    // Proceed with selecting a compression type from the list
} else {
    // Fallback to default compression or handle as unsupported
}
```

## API Test: `getSupportedOutputFormats`

### Signature
```java
public static List<String> getSupportedOutputFormats()
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/ThumbnailatorUtils.java:55_

_Source doc:_ Returns a {@link List} of supported output formats. @return		A {@link List} of supported output formats. If no formats are supported, an empty list is returned.

### Goal
Retrieves a list of image format names that the current environment's Java Image I/O implementation is capable of writing.

### Parameters
_None._

### Input
No input parameters are required. The availability of formats is strictly dependent on the host JVM's `ImageIO` capabilities. Precondition: The environment must be a valid Java 8 runtime (targeting Java 5 bytecode) capable of executing headless image processing.

### Output
Returns `List<String>` — A list of strings representing the names of supported output formats (e.g., "jpg", "png"). If the underlying `ImageIO` registry contains no writers, an empty list is returned.

### Valid Call Patterns
```java
// Inferred from signature:
List<String> formats = net.coobird.thumbnailator.util.ThumbnailatorUtils.getSupportedOutputFormats();
```

### LLM Instruction Prompt
- Use this method to verify if a specific output format is supported by the current environment before attempting to write a thumbnail.
- Always handle the returned `List<String>` as potentially empty.
- Do not assume specific formats (like "jpg" or "png") are present, as support is host-JVM dependent.

### Prompt Snippet
```text
net.coobird.thumbnailator.util.ThumbnailatorUtils.getSupportedOutputFormats()
```

### Common Failure Modes
- **Environment Mismatch:** If the host JVM is configured with a restricted security manager or a headless environment lacking standard `ImageIO` service providers, the list may be unexpectedly empty.
- **Format Assumption:** Assuming a format is supported without checking the list can lead to `javax.imageio.IIOException` or `UnsupportedFormatException` when calling `.toFile()` or `.toOutputStream()` in the `Thumbnails` pipeline.

### Fix Code Hint
```java
List<String> supported = ThumbnailatorUtils.getSupportedOutputFormats();
if (supported.contains("jpg")) {
    Thumbnails.of("input.png").size(100, 100).outputFormat("jpg").toFile("output.jpg");
} else {
    // Fallback to a guaranteed format or handle error
}
```

## API Test: `getTag`

### Signature
```java
public int getTag()
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdStructure.java:63_

_Source doc:_ Returns the tag element in the IFD structure. @return		An integer representation of the tag element. Should be a value between 0x00 to 0xFF.

### Goal
Retrieves the tag element identifier from an Image File Directory (IFD) structure used during EXIF metadata processing.

### Parameters
_None._

### Input
The caller must have an initialized instance of an IFD structure (e.g., `IfdStructure`) that has been populated with metadata from an image source. No external file formats or streams are required as arguments, as the state is held within the object instance.

### Output
Returns `int` — An integer representation of the tag element, expected to be a value between 0x00 and 0xFF.

### Valid Call Patterns
```java
// Inferred from signature:
int tag = ifdStructure.getTag();
```

### LLM Instruction Prompt
- When calling `getTag`, ensure the receiver is an instance of `net.coobird.thumbnailator.util.exif.IfdStructure`.
- Do not pass any arguments to the method.
- Treat the returned integer as a raw tag identifier within the range 0x00 to 0xFF.

### Prompt Snippet
```text
// Example usage of getTag
IfdStructure ifd = ...; // Obtained from EXIF processing
int tagValue = ifd.getTag();
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `IfdStructure` instance has not been properly initialized or is null.
- **Unexpected Range**: While the documentation specifies a range of 0x00 to 0xFF, logic relying on this value should account for potential values outside this range if the underlying IFD structure is malformed or corrupted.

### Fix Code Hint
```java
// Ensure the IfdStructure is not null before accessing the tag
if (ifdStructure != null) {
    int tag = ifdStructure.getTag();
    // Process tag...
}
```

## API Test: `getType`

### Signature
```java
public int getType()
public IfdType getType()
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:850  (+1 more definition site/overload)_

_Source doc:_ Returns the type of image. The value returned is the constant used for image types of {@link BufferedImage}. @return		The type of the image.

### Goal
Retrieves the image type constant associated with a `BufferedImage` or thumbnail configuration to identify the underlying pixel data structure.

### Parameters
_None._

### Input
The caller must have an initialized `BufferedImage` or a configured `ThumbnailParameter` object. The image must be a valid format supported by the JVM's `ImageIO`.

### Output
Returns `int` — The integer constant corresponding to the `BufferedImage` image type (e.g., `BufferedImage.TYPE_INT_ARGB`, `BufferedImage.TYPE_BYTE_INDEXED`).

### Valid Call Patterns
```java
// Retrieving type from a BufferedImage
int type = thumbnail.getType();

// Verifying type in a test context
assertEquals(BufferedImage.TYPE_INT_ARGB, thumbnail.getType());
```

### LLM Instruction Prompt
- Use `getType()` only when you need to verify or inspect the pixel format of a `BufferedImage` object or a `ThumbnailParameter` instance.
- Do not assume the returned integer is a custom Thumbnailator type; it maps directly to standard `java.awt.image.BufferedImage` constants.
- When comparing the result, use the `BufferedImage.TYPE_*` constants.

### Prompt Snippet
```text
// Example of checking image type
int imageType = myBufferedImage.getType();
if (imageType == BufferedImage.TYPE_INT_ARGB) {
    // Handle ARGB images
}
```

### Common Failure Modes
- **NullPointerException**: Calling `getType()` on a null `BufferedImage` reference.
- **Type Mismatch**: Assuming the returned `int` corresponds to a specific color space without checking against `BufferedImage` constants.
- **Misinterpretation**: Confusing the returned `int` with `ThumbnailParameter` constants like `ThumbnailParameter.ORIGINAL_IMAGE_TYPE` (-1), which may be returned if the parameter is configured to preserve the source type.

### Fix Code Hint
```java
// Ensure the image is not null before checking the type
if (thumbnail != null) {
    int type = thumbnail.getType();
    // Proceed with type-specific logic
}
```

## API Test: `getValue`

### Signature
```java
public Object getValue()
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/configurations/ResizerConfiguration.java:51  (+4 more definition site/overload)_

_Source doc:_ Returns a rendering hint value. @return		Rendering hint value.

### Goal
Retrieves the specific rendering hint value associated with a configuration object used within the Thumbnailator image processing pipeline.

### Parameters
_None._

### Input
No input parameters are required. The caller must ensure the object instance (e.g., a `ResizerConfiguration` or related hint-holding class) has been properly initialized within the context of a `ThumbnailParameter` or resizer configuration.

### Output
Returns `Object` — The rendering hint value, which corresponds to the specific configuration setting (such as interpolation or rendering quality) defined by the implementation.

### Valid Call Patterns
```java
// Inferred from signature: Accessing a hint value from a configuration instance
Object hintValue = resizerConfiguration.getValue();
```

### LLM Instruction Prompt
- Use `getValue()` only when interacting with objects that implement rendering hint configurations (e.g., `ResizerConfiguration` implementations).
- Do not assume the returned `Object` is a specific type; cast it only if the specific implementation context is known.
- This method is typically used for internal inspection of `ThumbnailParameter` settings or resizer configurations.

### Prompt Snippet
```text
// Accessing the rendering hint value from a configuration object
Object hint = configuration.getValue();
```

### Common Failure Modes
- **NullPointerException**: Occurs if the configuration object itself is null.
- **ClassCastException**: May occur if the caller assumes a specific return type (e.g., `Integer` or `RenderingHints.Key`) without verifying the actual object returned by the specific implementation.
- **Contextual Misuse**: Calling this on an object that does not hold a valid rendering hint, leading to unexpected or null return values.

### Fix Code Hint
```java
// Ensure the configuration object is initialized before calling getValue
if (resizerConfiguration != null) {
    Object hint = resizerConfiguration.getValue();
    // Handle the hint value appropriately
}
```

## API Test: `getWidthScalingFactor`

### Signature
```java
public double getWidthScalingFactor()
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:826_

_Source doc:_ Returns the scaling factor to apply to the width when creating the thumbnail. <p> Returns {@link Double#NaN} if the thumbnail size is set rather than the scaling factor. @return		The width scaling factor for the thumbnail. @since	0.3.10

### Goal
Retrieves the horizontal scaling factor configured for a thumbnail transformation, allowing developers to inspect whether a specific ratio or an absolute dimension was defined for the operation.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No specific file formats or external inputs are required, as this method operates on the internal state of the parameter configuration.

### Output
Returns `double` — The width scaling factor as a decimal value. Returns `Double.NaN` if the thumbnail size was defined using absolute dimensions (e.g., `.size(width, height)`) rather than a scaling factor.

### Valid Call Patterns
```java
// Inferred from ThumbnailParameterBuilderTest.java
ThumbnailParameter param = new ThumbnailParameterBuilder()
        .size(100, 100)
        .build();

double widthFactor = param.getWidthScalingFactor();
```

### LLM Instruction Prompt
- Use `getWidthScalingFactor()` only on instances of `ThumbnailParameter`.
- Always check the return value against `Double.isNaN()` before using it in calculations, as it will return `NaN` if the thumbnail was configured via absolute size rather than a scaling factor.

### Prompt Snippet
```text
// Check if scaling factor is set, otherwise handle absolute size
if (Double.isNaN(param.getWidthScalingFactor())) {
    // Handle absolute size case
} else {
    double factor = param.getWidthScalingFactor();
}
```

### Common Failure Modes
- **Misinterpreting `NaN`:** Assuming the method returns `0.0` or `1.0` when no scaling factor is set; the method explicitly returns `Double.NaN` in these cases.
- **Context Confusion:** Attempting to call this on a `Thumbnails.Builder` object directly; it must be called on the `ThumbnailParameter` object retrieved from the builder or configuration.

### Fix Code Hint
```java
// Ensure you are checking for NaN to avoid logic errors
double factor = param.getWidthScalingFactor();
if (!Double.isNaN(factor)) {
    System.out.println("Scaling width by: " + factor);
} else {
    System.out.println("Absolute size is being used instead of a scaling factor.");
}
```

## API Test: `height`

### Signature
```java
public Builder<T> height(int height)
public BufferedImageBuilder height(int height)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:905  (+1 more definition site/overload)_

_Source doc:_ Sets the height of the thumbnail. <p> The thumbnail will have the dimensions constrained by the specified height, and the aspect ratio of the original image will be preserved by the thumbnail. <p> Once this method is called, calling the {@link #size(int, int)} or the {@link #scale(double)} method will result in an {@link IllegalStateException}. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. @param height		The height of the thumbnail. @return				Reference to this object. @since 	0.3.5

### Goal
Sets the target height for the thumbnail while automatically preserving the original image's aspect ratio.

### Parameters
- `height` (`int`): The desired height of the thumbnail in pixels. Must be a positive integer.

### Input
- **Data:** An image source provided via `Thumbnails.of(...)` (e.g., `File`, `InputStream`, `URL`, or `BufferedImage`).
- **Preconditions:** The input must be a valid image format supported by the host JVM's `ImageIO`. The builder must not have already had `size(int, int)` or `scale(double)` called upon it.

### Output
Returns `Builder<T>` — A reference to the current builder instance, allowing for further method chaining in the fluent pipeline.

### Valid Call Patterns
```java
Thumbnails.of(sourceImage)
    .height(50)
    .toFile("thumbnail.jpg");
```

### LLM Instruction Prompt
- When using `height(int)`, ensure that `size(int, int)` and `scale(double)` are not called in the same pipeline, as this will trigger an `IllegalStateException`.
- Only call `height` once per builder instance.

### Prompt Snippet
```text
.height(int height)
```

### Common Failure Modes
- **IllegalStateException:** Occurs if `height` is called more than once, or if it is called after `size(int, int)` or `scale(double)` has already been invoked on the same builder.
- **IllegalArgumentException:** Occurs if the provided `height` is zero or negative.

### Fix Code Hint
```java
// Ensure height is set only once and no conflicting sizing methods are used
Thumbnails.of(image)
    .height(100)
    .toFile("output.png");
```

## API Test: `if`

### Signature
```java
else if (thumbWidth > origWidth && thumbHeight > origHeight)
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/DefaultResizerFactory.java:139_

### Goal
Evaluates whether the requested thumbnail dimensions exceed the original image dimensions to determine if an upscaling operation is required.

### Parameters
- `origHeight` (`thumbWidth > origWidth && thumbHeight >`): A logical condition comparing the target thumbnail width/height against the source image's original width/height.

### Input
The caller must provide valid `thumbWidth` and `thumbHeight` values (integers representing pixels) and have access to the `origWidth` and `origHeight` metadata of the source image. This check is typically performed internally by the `DefaultResizerFactory` during the pipeline execution.

### Output
Returns `else` — The control flow branch taken when the condition is false, indicating that the requested thumbnail size is equal to or smaller than the original image dimensions.

### Valid Call Patterns
```java
// Inferred from internal usage in DefaultResizerFactory.java
if (thumbWidth <= origWidth && thumbHeight <= origHeight) {
    // Proceed with standard resizing
} else if (thumbWidth > origWidth && thumbHeight > origHeight) {
    // Handle upscaling logic
}
```

### LLM Instruction Prompt
- When generating code involving Thumbnailator resizing logic, do not call this `if` block directly as it is an internal control structure within `DefaultResizerFactory`. Instead, use the fluent `Thumbnails.of(...).size(w, h)` API, which handles these dimension checks automatically.

### Prompt Snippet
```text
// Use the fluent API to handle resizing logic rather than manual dimension checks
Thumbnails.of(sourceImage)
    .size(width, height)
    .toFile(outputFile);
```

### Common Failure Modes
- **Logic Errors:** Manually attempting to replicate this check outside the library can lead to inconsistent behavior if the `ThumbnailParameter` settings (such as scaling mode) are not accounted for.
- **Headless Environment:** Attempting to perform these checks on null image objects or invalid file paths will result in `NullPointerException` or `IOException` before the resizing logic is reached.

### Fix Code Hint
```java
// Ensure the source image is loaded and valid before defining size
if (sourceImage != null) {
    Thumbnails.of(sourceImage).size(w, h).asBufferedImage();
}
```

## API Test: `imageType`

### Signature
```java
public Builder<T> imageType(int type)
public BufferedImageBuilder imageType(int imageType)
public ThumbnailParameterBuilder imageType(int type)
public ThumbnailMaker imageType(int imageType)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1283  (+3 more definition site/overload)_

_Source doc:_ Sets the image type of the thumbnail. <p> Calling this method to set this parameter is optional. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. @param type			The image type of the thumbnail. @return				Reference to this object.

### Goal
Configures the specific `BufferedImage` type (e.g., `TYPE_INT_RGB`, `TYPE_BYTE_GRAY`) for the generated thumbnail output.

### Parameters
- `type` (`int`): An integer constant representing the desired `BufferedImage` type, typically sourced from the `java.awt.image.BufferedImage` class constants.

### Input
- **Data:** A valid `BufferedImage` type constant.
- **Preconditions:** The input must be a valid integer constant supported by the `BufferedImage` class. If not explicitly set, the library defaults to `ThumbnailParameter.DEFAULT_IMAGE_TYPE` (value: 2). Use `ThumbnailParameter.ORIGINAL_IMAGE_TYPE` (value: -1) to preserve the source image's type.

### Output
Returns `Builder<T>` — A reference to the current builder object, allowing for further method chaining in the thumbnail pipeline.

### Valid Call Patterns
```java
BufferedImage thumbnail = Thumbnails.of(img)
    .size(200, 200)
    .imageType(BufferedImage.TYPE_BYTE_GRAY)
    .asBufferedImage();
```

### LLM Instruction Prompt
- When configuring the thumbnail pipeline, use `imageType` to explicitly define the output color model or bit depth.
- Ensure `imageType` is called at most once per builder instance to avoid runtime exceptions.
- Prefer `BufferedImage` constants for the `type` argument to ensure type safety.

### Prompt Snippet
```text
.imageType(BufferedImage.TYPE_INT_RGB)
```

### Common Failure Modes
- **IllegalStateException:** Occurs if `imageType` is invoked more than once on the same builder instance.
- **Invalid Type:** Passing an unsupported or undefined integer constant may result in unexpected behavior or errors during the `BufferedImage` creation phase.

### Fix Code Hint
```java
// Ensure the method is called only once in the chain
Thumbnails.of(source)
    .size(100, 100)
    .imageType(BufferedImage.TYPE_INT_ARGB) // Call once
    .toFile("output.png");
```

## API Test: `init`

### Signature
```java
static void init()
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/Configurations.java:108_

_Source doc:_ This method will initialize configurations from {@code thumbnailator.properties} on the classpath. <p> Implementation note: {@code thumbnailator.properties} is searched from the class loader associated with the current thread, rather than the system class loader. <p> This is intended only to be called from tests.

### Goal
Initializes Thumbnailator library configurations by loading settings from a `thumbnailator.properties` file located on the classpath.

### Parameters
_None._

### Input
The caller must ensure a file named `thumbnailator.properties` exists on the classpath. The method specifically searches for this file using the class loader associated with the current thread.

### Output
Returns `void` — This method performs a side-effect operation to update internal library configurations; it does not return a value.

### Valid Call Patterns
```java
// Inferred from signature:
net.coobird.thumbnailator.util.Configurations.init();
```

### LLM Instruction Prompt
- Use this method only within test environments to reset or apply custom configurations.
- Do not call this in production code, as it is explicitly intended for test-time configuration management.
- Ensure the `thumbnailator.properties` file is present in the test resources directory before invocation.

### Prompt Snippet
```text
net.coobird.thumbnailator.util.Configurations.init();
```

### Common Failure Modes
- **Missing Configuration File:** If `thumbnailator.properties` is not found on the current thread's class loader, the initialization will fail to apply custom settings.
- **Class Loader Mismatch:** If the environment uses a complex class loader hierarchy, the method may fail to locate the properties file because it specifically targets the thread-associated class loader rather than the system class loader.
- **Production Usage:** Calling this in a production environment may lead to unexpected behavior or performance overhead, as it is designed strictly for test harnesses.

### Fix Code Hint
```java
// Ensure the properties file is in src/test/resources
try {
    net.coobird.thumbnailator.util.Configurations.init();
} catch (Exception e) {
    // Handle potential configuration loading errors during test setup
    e.printStackTrace();
}
```

## API Test: `isKeepAspectRatio`

### Signature
```java
public boolean isKeepAspectRatio()
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:861_

_Source doc:_ Returns whether or not the thumbnail is to maintain the aspect ratio of the source image when creating the thumbnail. @return 		{@code true} if the thumbnail is to maintain the aspect ratio of the original image, {@code false} otherwise.

### Goal
Determines whether the current `ThumbnailParameter` configuration is set to preserve the source image's aspect ratio during the resizing process.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No specific file formats or external data are required as this is a state-check method on the parameter configuration object.

### Output
Returns `boolean` — `true` if the thumbnail generation process is configured to maintain the aspect ratio of the original image, `false` if it is configured to ignore the aspect ratio (e.g., stretching to fit specific dimensions).

### Valid Call Patterns
```java
// Inferred from signature:
ThumbnailParameter param = new ThumbnailParameter(...);
boolean keepRatio = param.isKeepAspectRatio();
```

### LLM Instruction Prompt
- Use `isKeepAspectRatio()` only when inspecting a `ThumbnailParameter` instance.
- Do not assume the return value dictates the final output dimensions without also checking the `getSize()` or `getWidth()`/`getHeight()` parameters, as aspect ratio maintenance is a scaling constraint, not a dimension result.

### Prompt Snippet
```text
ThumbnailParameter param = ...;
boolean isMaintainingRatio = param.isKeepAspectRatio();
```

### Common Failure Modes
- **NullPointerException**: Calling this method on a `null` `ThumbnailParameter` reference.
- **Misinterpretation**: Assuming this method returns the actual aspect ratio value (e.g., 16:9); it only returns a boolean flag indicating if the constraint is enabled.

### Fix Code Hint
```java
// Ensure the parameter object is initialized before checking the flag
if (param != null) {
    boolean isMaintaining = param.isKeepAspectRatio();
    // Proceed with logic based on the boolean state
}
```

## API Test: `isOffset`

### Signature
```java
public boolean isOffset()
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdStructure.java:122_

_Source doc:_ Returns whether the value returned by the {@link #getOffsetValue()} method is an offset value. @return		{@code true} if the value returned by the {@link #getOffsetValue()} method is a offset value, {@code false} otherwise.

### Goal
Determines if the data stored within an IFD (Image File Directory) structure represents an offset pointer to additional metadata rather than a direct data value.

### Parameters
_None._

### Input
The caller must have an initialized `IfdStructure` object, typically obtained during the parsing of EXIF metadata from an image file. No external parameters are required for this method.

### Output
Returns `boolean` — `true` if the value retrieved via `getOffsetValue()` is an offset to further data, `false` if the value is the actual data itself.

### Valid Call Patterns
```java
// Inferred from signature:
boolean isOffset = ifdStructure.isOffset();
```

### LLM Instruction Prompt
- When working with EXIF metadata parsing in Thumbnailator, use `isOffset()` to verify the nature of the data returned by `getOffsetValue()` before attempting to interpret the result as a direct value or a memory address.

### Prompt Snippet
```text
if (ifdStructure.isOffset()) {
    // Handle as a pointer/offset
} else {
    // Handle as a direct value
}
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `IfdStructure` instance has not been properly initialized or is null.
- **Misinterpretation**: Treating a direct value as an offset (or vice versa) if the return value of `isOffset()` is ignored, leading to incorrect metadata extraction.

### Fix Code Hint
```java
// Always check the type before accessing the offset value
if (ifdStructure.isOffset()) {
    long offset = ifdStructure.getOffsetValue();
    // Proceed with offset-based logic
}
```

## API Test: `isSupportedOutputFormat`

### Signature
```java
public static boolean isSupportedOutputFormat(String format)
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/ThumbnailatorUtils.java:72_

_Source doc:_ Returns whether a specified format is supported for output. @param format	The format to check whether it is supported or not. @return			{@code true} if the format is supported, {@code false} otherwise.

### Goal
Determines if a specific image format string is compatible with the host JVM's `ImageIO` capabilities for thumbnail output operations.

### Parameters
- `format` (`String`): The informal name of the image format (e.g., "JPEG", "PNG").

### Input
The caller must provide a `String` representing an image format. The availability of specific formats is strictly dependent on the host JVM's `ImageIO` configuration. There are no internal hardcoded lists; the method queries the underlying Java environment.

### Output
Returns `boolean` — `true` if the provided format is registered and supported for writing by the current JVM environment, `false` otherwise.

### Valid Call Patterns
```java
// Checking for a standard format
boolean isSupported = ThumbnailatorUtils.isSupportedOutputFormat("JPEG");
```

### LLM Instruction Prompt
- Use `ThumbnailatorUtils.isSupportedOutputFormat(String)` to verify format compatibility before attempting to write thumbnails to disk or stream.
- Do not assume specific formats like "JPEG" or "PNG" are always available; always check the environment if the application requires dynamic format support.

### Prompt Snippet
```text
ThumbnailatorUtils.isSupportedOutputFormat("format_name")
```

### Common Failure Modes
- **Environment Dependency:** The method returns `false` for formats that are not supported by the specific JVM running the application, even if they are common image formats.
- **Case Sensitivity:** While `ImageIO` is generally flexible, passing null or empty strings may result in unexpected behavior or `false` returns depending on the JVM implementation.
- **Misinterpretation:** Developers may assume this method validates the *content* of an image file; it only validates the *format capability* of the environment.

### Fix Code Hint
```java
String format = "webp";
if (ThumbnailatorUtils.isSupportedOutputFormat(format)) {
    Thumbnails.of("input.jpg").outputFormat(format).toFile("output.webp");
} else {
    // Fallback to a guaranteed format like PNG or JPEG
    Thumbnails.of("input.jpg").outputFormat("png").toFile("output.png");
}
```

## API Test: `isSupportedOutputFormatType`

### Signature
```java
public static boolean isSupportedOutputFormatType(String format, String type)
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/ThumbnailatorUtils.java:130_

_Source doc:_ Returns whether a specified format type is supported for a specified output format. @param format	The format to check whether it is supported or not. @param type		The format type to check whether it is supported or not. @return			{@code true} if the format type is supported by the specified supported format, {@code false} otherwise.

### Goal
Determines if a specific image format type is compatible with a given output format within the current Java Image I/O environment.

### Parameters
- `format` (`String`): The name of the image format (e.g., "JPEG", "PNG") to check for support.
- `type` (`String`): The specific format type or subtype to validate against the provided format.

### Input
The caller must provide valid `String` representations of image formats and types. This method relies on the underlying Java Image I/O API capabilities of the host JVM. No specific file or stream is required as input, as this is a metadata validation utility.

### Output
Returns `boolean` — `true` if the specified `type` is supported by the `format` in the current environment, `false` otherwise.

### Valid Call Patterns
```java
// Checking if JPEG is a supported type for the JPEG format
boolean isSupported = ThumbnailatorUtils.isSupportedOutputFormatType("JPEG", "JPEG");
```

### LLM Instruction Prompt
- Use `ThumbnailatorUtils.isSupportedOutputFormatType(String, String)` to verify format compatibility before attempting to write thumbnails.
- Always pass the format and type as `String` objects.
- Note that support is dependent on the host JVM's `ImageIO` capabilities; do not assume all formats are available across all environments.

### Prompt Snippet
```text
ThumbnailatorUtils.isSupportedOutputFormatType(format, type)
```

### Common Failure Modes
- **Environment Mismatch:** Passing a format or type that is not registered in the host JVM's `ImageIO` service provider registry will return `false`.
- **Case Sensitivity:** While many `ImageIO` implementations are case-insensitive, it is recommended to use standard uppercase format names (e.g., "JPEG", "PNG") to ensure consistent results.
- **Invalid Type Strings:** Providing arbitrary strings (e.g., "foobar") as the `type` argument will correctly return `false` if they do not map to a valid supported type for the given format.

### Fix Code Hint
```java
// Ensure the format and type are valid before proceeding with output operations
if (ThumbnailatorUtils.isSupportedOutputFormatType(format, type)) {
    // Proceed with thumbnail generation
} else {
    // Handle unsupported format/type error
}
```

## API Test: `isValue`

### Signature
```java
public boolean isValue()
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdStructure.java:105_

_Source doc:_ Returns whether the value returned by the {@link #getOffsetValue()} method is an actual value. @return		{@code true} if the value returned by the {@link #getOffsetValue()} method is a value, {@code false} otherwise.

### Goal
Determines if the data associated with an IFD (Image File Directory) structure represents an actual value rather than an offset pointer.

### Parameters
_None._

### Input
The caller must have an initialized `IfdStructure` object, typically obtained during the parsing of EXIF metadata from an image file. No specific file format is required as input to this method, but the object must be in a valid state following metadata extraction.

### Output
Returns `boolean` — `true` if the `getOffsetValue()` method returns an actual value, and `false` if it returns an offset to another data location.

### Valid Call Patterns
```java
// Inferred from signature:
boolean isActualValue = ifdStructure.isValue();
```

### LLM Instruction Prompt
- When working with EXIF metadata structures in Thumbnailator, use `isValue()` to check if the data retrieved via `getOffsetValue()` is the literal value or a pointer to a memory offset. Do not assume `getOffsetValue()` always returns the final data without first verifying with `isValue()`.

### Prompt Snippet
```text
if (ifdStructure.isValue()) {
    // Process the value directly
} else {
    // Handle the offset pointer
}
```

### Common Failure Modes
- Calling `isValue()` on an uninitialized or null `IfdStructure` object, which will result in a `NullPointerException`.
- Misinterpreting the return value: assuming the data is a direct value when `isValue()` returns `false`, leading to incorrect parsing of EXIF metadata offsets.

### Fix Code Hint
```java
if (ifdStructure != null && ifdStructure.isValue()) {
    Object data = ifdStructure.getOffsetValue();
    // Proceed with data
}
```

## API Test: `iterableBufferedImages`

### Signature
```java
public Iterable<BufferedImage> iterableBufferedImages()
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2213_

_Source doc:_ <p> Create the thumbnails and return as a {@link Iterable} of {@link BufferedImage}s. </p> <p> Thumbnail for a given input is lazily evaluated as the {@link Iterator#next()} method is called on the iterator. </p> <p> For situations where multiple thumbnails are being generated, this method is preferred over the {@link #asBufferedImages()} method, as (1) the processing does not have to complete before the method returns and (2) the thumbnails can be retrieved one at a time, potentially reducing the number of thumbnails which need to be retained in the heap memory, potentially reducing the chance of {@link OutOfMemoryError}s from occurring. </p> <p> If an {@link IOException} occurs during the processing of the thumbnail, the {@link Iterable} will return a {@code null} for that element. </p> <p><strong>Notes on image types</strong></p> <p> The image type of the {@link BufferedImage} depends on the type of the input image. (Specifically, the type emitted by the {@link javax.imageio.ImageReader} associated for the file type.) Thumbnailator will attempt to use the same image type as the input if it is not specified through {@link #imageType(int)}. </p> <p> This has implications when writing {@code BufferedImage} to files using the default JPEG writer bundled with Java. There are issues with the JPEG writer <a href="https://bugs.openjdk.java.net/browse/JDK-8041459">outputting wrong colors</a> or <a href="https://bugs.openjdk.java.net/browse/JDK-8204188">throwing exceptions</a> when writing a image containing an alpha channel. </p> <p> If outputting to a file, consider using one of the {@link #toFiles(Iterable)}, {@link #toFiles(Rename)}, {@link #toFiles(File, Rename)} {@link #asFiles(Iterable)}, {@link #asFiles(Rename)} or {@link #asFiles(File, Rename)} methods instead, as it contains workarounds to prevent issues mentioned in the previous paragraph. </p> <p> For more information on {@code BufferedImage} types, refer to {@link BufferedImage#getType()}. </p> @return		An {@link Iterable} which will provide an {@link Iterator} which returns thumbnails as {@link BufferedImage}s.

### Goal
Provides a memory-efficient, lazy-evaluated mechanism to generate multiple thumbnails as `BufferedImage` objects one at a time.

### Parameters
_None._

### Input
The `Thumbnails.Builder` must be initialized with valid input sources (e.g., `File`, `InputStream`, `URL`, or `BufferedImage`) supported by the JVM's `ImageIO`. Preconditions include ensuring the pipeline is configured with necessary operations (e.g., `.size()`) before calling this method.

### Output
Returns `Iterable<BufferedImage>` — An iterable collection of thumbnails. Each `BufferedImage` is generated lazily upon calling `Iterator.next()`. If an `IOException` occurs during processing, the iterator returns `null` for that specific element.

### Valid Call Patterns
```java
// Basic usage with a single image
Iterable<BufferedImage> thumbnails = Thumbnails.of(img)
    .size(50, 50)
    .iterableBufferedImages();

// Usage with output format specification
Iterable<BufferedImage> thumbnails = Thumbnails.of(img)
    .size(50, 50)
    .outputFormat("png")
    .iterableBufferedImages();
```

### LLM Instruction Prompt
- Use `iterableBufferedImages()` only when processing multiple images or when memory constraints (heap usage) are a concern.
- Do not use this method if the goal is to write directly to files; use `toFiles()` or `asFiles()` instead to avoid known Java JPEG writer issues with alpha channels.
- Always iterate through the returned `Iterable` to trigger the lazy processing.

### Prompt Snippet
```text
// Use for memory-efficient batch processing
Iterable<BufferedImage> results = Thumbnails.of(files).size(100, 100).iterableBufferedImages();
for (BufferedImage thumb : results) {
    if (thumb != null) { /* process thumbnail */ }
}
```

### Common Failure Modes
- **OutOfMemoryError:** While this method reduces heap pressure, processing extremely large batches or very high-resolution images may still exceed heap limits if the resulting `BufferedImage` objects are held in memory simultaneously.
- **Null Elements:** If an `IOException` occurs during the processing of a specific image in a batch, the iterator will return `null` for that element; failure to check for `null` will result in a `NullPointerException`.
- **JPEG Color/Exception Issues:** Using the resulting `BufferedImage` with the default Java JPEG writer may cause color distortion or exceptions if the image contains an alpha channel.

### Fix Code Hint
```java
// If writing to files, avoid iterableBufferedImages() and use:
Thumbnails.of(files)
    .size(100, 100)
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

## API Test: `iterator`

### Signature
```java
public Iterator<File> iterator()
```
_Source: source/src/main/java/net/coobird/thumbnailator/name/ConsecutivelyNumberedFilenames.java:343_

_Source doc:_ Returns an iterator which generates file names according to the rules specified by this object. @return		An iterator which generates file names.

### Goal
Provides a mechanism to sequentially generate and retrieve `File` objects based on predefined naming conventions, typically used during batch thumbnail processing operations.

### Parameters
_None._

### Input
The caller must ensure that the object instance (e.g., `ConsecutivelyNumberedFilenames`) has been initialized with the required naming pattern, base directory, and file extension constraints. No external image data is required as input to the iterator itself, as it operates on file path generation logic.

### Output
Returns `Iterator<File>` — An iterator that yields `File` objects representing the generated filenames according to the configured naming strategy.

### Valid Call Patterns
```java
// Example derived from standard usage of naming strategy objects
Iterator<File> iter = namingStrategy.iterator();
while (iter.hasNext()) {
    File nextFile = iter.next();
    // Process file...
}
```

### LLM Instruction Prompt
- Use this method only when working with objects that implement naming strategies (such as `ConsecutivelyNumberedFilenames`).
- Do not assume the iterator will perform file I/O; it is strictly for generating path references.
- Always check `hasNext()` before calling `next()` to avoid `NoSuchElementException`.

### Prompt Snippet
```text
// To generate a sequence of filenames for batch output:
Iterator<File> fileIterator = myNamingStrategy.iterator();
```

### Common Failure Modes
- **NoSuchElementException**: Occurs if `next()` is called when `hasNext()` returns `false`.
- **IllegalStateException**: May occur if the underlying naming strategy object has not been properly configured with a base directory or naming pattern before the iterator is requested.
- **NullPointerException**: If the naming strategy object itself is null.

### Fix Code Hint
```java
if (namingStrategy != null) {
    Iterator<File> iter = namingStrategy.iterator();
    while (iter.hasNext()) {
        File f = iter.next();
        // Safe to use f
    }
}
```

## API Test: `keepAspectRatio`

### Signature
```java
public Builder<T> keepAspectRatio(boolean keep)
public ThumbnailParameterBuilder keepAspectRatio(boolean keep)
public FixedSizeThumbnailMaker keepAspectRatio(boolean keep)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1491  (+2 more definition site/overload)_

_Source doc:_ Sets whether or not to keep the aspect ratio of the original image for the thumbnail. <p> Calling this method without first calling the {@link #size(int, int)} method will result in an {@link IllegalStateException} to be thrown. <p> If this method is not called when, by default the aspect ratio of the original image is preserved for the thumbnail. <p> Calling this method after calling the {@link #scale(double)} method or the {@link #scale(double, double)} method will result in a {@link IllegalStateException}. @param keep			{@code true} if the thumbnail is to maintain the aspect ratio of the original image, {@code false} otherwise. @return				Reference to this object. @throws IllegalStateException	If <ol> <li>the {@link #size(int, int)} has not yet been called to specify the size of the thumbnail, or</li> <li>the {@link #scale(double)} method has been called, or</li> <li>the {@link #scale(double, double)} method has been called, or</li> <li>the {@link #width(int)} and/or {@link #height(int)} has been called and not preserving the aspect ratio is desired.</li> </ol>

### Goal
Configures the thumbnail pipeline to either maintain or ignore the original image's aspect ratio when resizing to a target size.

### Parameters
- `keep` (`boolean`): Set to `true` to preserve the original aspect ratio (default behavior); set to `false` to force the thumbnail to match the exact dimensions specified by `size(int, int)`.

### Input
- **Preconditions:** The `size(int, int)` method must be called prior to `keepAspectRatio`.
- **Compatibility:** This method cannot be used if `scale(double)` or `scale(double, double)` has already been invoked in the builder chain.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnailator` builder, allowing for further method chaining.

### Valid Call Patterns
```java
// Maintaining aspect ratio (default)
BufferedImage thumbnail = Thumbnails.of(img)
    .size(120, 50)
    .keepAspectRatio(true)
    .asBufferedImage();

// Forcing exact dimensions (ignoring aspect ratio)
BufferedImage thumbnail = Thumbnails.of(img)
    .size(120, 50)
    .keepAspectRatio(false)
    .asBufferedImage();
```

### LLM Instruction Prompt
- Always ensure `size(int, int)` is called before `keepAspectRatio`.
- Do not use `keepAspectRatio` if the pipeline uses `scale()` methods.
- Remember that `keepAspectRatio(true)` is the default behavior if the method is omitted.

### Prompt Snippet
```text
.size(width, height).keepAspectRatio(booleanValue)
```

### Common Failure Modes
- **IllegalStateException:** Occurs if `keepAspectRatio` is called before `size(int, int)`.
- **IllegalStateException:** Occurs if `keepAspectRatio` is called after `scale(double)` or `scale(double, double)`.
- **IllegalStateException:** Occurs if `width(int)` or `height(int)` is used in conjunction with `keepAspectRatio(false)`.

### Fix Code Hint
```java
// Ensure size is defined first
Thumbnails.of(source)
    .size(100, 100)
    .keepAspectRatio(false)
    .toFile("output.jpg");
```

## API Test: `make`

### Signature
```java
public abstract BufferedImage make(BufferedImage img)
public BufferedImage make(BufferedImage img)
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ThumbnailMaker.java:179  (+2 more definition site/overload)_

_Source doc:_ Makes a thumbnail. @param img		The source image. @return			The thumbnail created from the source image, using the parameters set by the {@link ThumbnailMaker}.

### Goal
Generates a thumbnail from a source `BufferedImage` by applying the specific transformation parameters configured within a `ThumbnailMaker` instance.

### Parameters
- `img` (`BufferedImage`): The source image to be processed into a thumbnail.

### Input
The caller must provide a valid `BufferedImage` object. The image must be compatible with the Java 2D API. Preconditions include ensuring the `ThumbnailMaker` instance (e.g., `FixedSizeThumbnailMaker`) has been properly initialized with required dimensions or parameters before calling `make`, as uninitialized makers will fail.

### Output
Returns `BufferedImage` — The resulting thumbnail image, processed according to the internal state of the `ThumbnailMaker`.

### Valid Call Patterns
```java
// Example inferred from signature and test suite usage
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker(100, 100);
BufferedImage thumbnail = maker.make(sourceImage);
```

### LLM Instruction Prompt
- When using `make`, ensure the `ThumbnailMaker` implementation (e.g., `FixedSizeThumbnailMaker`) is fully initialized with required parameters (like dimensions) before invocation.
- Always pass a non-null `BufferedImage` as the argument.
- Do not use `make` on an uninitialized maker instance, as this will result in an exception.

### Prompt Snippet
```text
ThumbnailMaker maker = new FixedSizeThumbnailMaker(width, height);
BufferedImage result = maker.make(sourceImage);
```

### Common Failure Modes
- **IllegalStateException**: Occurs if the `ThumbnailMaker` is used without being properly initialized (e.g., calling `make` on a `FixedSizeThumbnailMaker` created with a no-arg constructor).
- **NullPointerException**: Occurs if the provided `BufferedImage` argument is `null`.
- **Unsupported Image Type**: If the source `BufferedImage` uses a color model or image type not supported by the underlying Java 2D rendering pipeline, processing may fail or produce unexpected artifacts.

### Fix Code Hint
```java
// Ensure the maker is initialized with dimensions before calling make
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker(100, 100);
if (sourceImage != null) {
    BufferedImage thumbnail = maker.make(sourceImage);
}
```

## API Test: `newRotator`

### Signature
```java
public static Rotator newRotator(final double angle)
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Rotation.java:78_

_Source doc:_ Creates a new instance of {@code Rotator} which rotates an image at the specified angle. <p> When the {@link Rotator} returned by this method is applied, the image will be rotated clockwise by the specified angle. @param angle			The angle at which the instance of {@code Rotator} is to rotate a image it acts upon. @return				An instance of {@code Rotator} which will rotate a given image.

### Goal
Creates a `Rotator` filter instance that, when applied to a `BufferedImage`, rotates the image clockwise by the specified degree.

### Parameters
- `angle` (`final double`): The clockwise rotation angle in degrees.

### Input
- **Data:** A `BufferedImage` object.
- **Preconditions:** The image must be a valid `BufferedImage` supported by the host JVM's `ImageIO`. The `Rotator` is typically applied as part of a filter chain or via the `ImageFilter` interface.

### Output
Returns `Rotator` — An instance of the `Rotator` class configured to perform the specified clockwise rotation.

### Valid Call Patterns
```java
// Using the Rotation class directly to create a filter
ImageFilter filter = Rotation.newRotator(45);
```

### LLM Instruction Prompt
- When using `newRotator`, always qualify the call with the `Rotation` class.
- Ensure the `angle` is provided as a `double`.
- Note that this method returns an `ImageFilter` implementation; it does not perform the rotation immediately but prepares the filter for application to a `BufferedImage`.

### Prompt Snippet
```text
Rotation.newRotator(angle)
```

### Common Failure Modes
- **Immutability Misconception:** The `apply` method of the `Rotator` modifies the image in-place; if the original image must be preserved, ensure a copy is made before applying the filter.
- **Type Mismatch:** Passing an integer instead of a double may cause compilation issues if the context expects a specific type, though Java handles the promotion; ensure the value represents degrees.
- **Empty/Null Images:** Passing a null `BufferedImage` to the resulting `Rotator.apply()` method will result in a `NullPointerException`.

### Fix Code Hint
```java
// To rotate an image while preserving the original:
BufferedImage original = ...;
BufferedImage copy = BufferedImages.copy(original);
Rotation.newRotator(90).apply(copy);
```

## API Test: `of`

### Signature
```java
public static Builder<File> of(String... files)
public static Builder<File> of(File... files)
public static Builder<URL> of(URL... urls)
public static Builder<? extends InputStream> of(InputStream... inputStreams)
public static Builder<BufferedImage> of(BufferedImage... images)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:208  (+4 more definition site/overload)_

_Source doc:_ Indicate to make thumbnails for images with the specified filenames. @param files		File names of image files for which thumbnails are to be produced for. @return			Reference to a builder object which is used to specify the parameters for creating the thumbnail. @throws NullPointerException		If the argument is {@code null}. @throws IllegalArgumentException	If the argument is an empty array.

### Goal
Initializes a fluent thumbnail processing pipeline by specifying the source image(s) to be transformed.

### Parameters
- `files` (`String...`): A variable-length array of file paths (as Strings) representing the image files to be processed.

### Input
- **Data:** Accepts `String` paths, `File` objects, `URL` objects, `InputStream` instances, or `BufferedImage` objects.
- **Formats:** Relies on the host JVM's `ImageIO` capabilities; ensure the input is a valid image format supported by the environment.
- **Preconditions:** The input must not be `null` and must contain at least one element.

### Output
Returns `Builder<File>` (or the corresponding generic type for other overloads) — A reference to a `Thumbnails.Builder` object used to chain subsequent operations like `size()`, `rotate()`, or `watermark()`.

### Valid Call Patterns
```java
// File-based batch processing
Thumbnails.of(new File("path/to/directory").listFiles())
    .size(640, 480)
    .outputFormat("jpg")
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);

// In-memory processing
BufferedImage thumbnail = Thumbnails.of(sourceImage)
    .size(160, 160)
    .rotate(90)
    .asBufferedImage();
```

### LLM Instruction Prompt
- Always use `Thumbnails.of(...)` as the entry point for the fluent builder pattern.
- Match the input type (String, File, URL, InputStream, or BufferedImage) to the appropriate `of` overload.
- Ensure the pipeline terminates with a terminal operation such as `toFile()`, `toFiles()`, `asBufferedImage()`, or `asBufferedImages()`.

### Prompt Snippet
```text
Thumbnails.of(source).size(width, height).toFile(destination);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the input argument is `null`.
- **IllegalArgumentException:** Occurs if an empty array is passed to the method.
- **UnsupportedFormatException:** Occurs if the provided file or stream format is not recognized by the JVM's `ImageIO`.
- **Headless Environment Issues:** While Thumbnailator is designed for headless use, ensure the JVM has sufficient heap memory for the image dimensions being processed.

### Fix Code Hint
```java
// Ensure input is not null and array is not empty
if (files != null && files.length > 0) {
    Thumbnails.of(files).size(100, 100).toFiles(Rename.PREFIX_DOT_THUMBNAIL);
}
```

## API Test: `outputFormat`

### Signature
```java
public Builder<T> outputFormat(String format)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1658_

_Source doc:_ Sets the compression format to use when writing the thumbnail. <p> For example, to set the output format to JPEG, the following code can be used: <pre><code> Thumbnails.of(image) .size(640, 480) .outputFormat("JPEG") .toFile(thumbnail); </code></pre> or, alternatively: <pre><code> Thumbnails.of(image) .size(640, 480) .outputFormat("jpg") .toFile(thumbnail); </code></pre> <p> Currently, whether or not the compression format string is valid dependents on whether the Java Image I/O API recognizes the string as a format that it supports for output. (Valid format names can be obtained by calling the {@link ImageIO#getWriterFormatNames()} method.) <p> Calling this method to set this parameter is optional. <p> Calling this method in conjunction with {@link #asBufferedImage()} or {@link #asBufferedImages()} will not result in any changes to the final result. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. @param format		The compression format to use when writing the thumbnail. @return				Reference to this object. @throws IllegalArgumentException	If an unsupported format is specified.

### Goal
Specifies the image file format (e.g., "jpg", "png") to be used when writing the processed thumbnail to an output sink.

### Parameters
- `format` (`String`): A string representing the desired image format; must be a format supported by the host JVM's `ImageIO` implementation (e.g., "JPEG", "png").

### Input
- **Data:** A `String` identifier for the image format.
- **Preconditions:** The format string must be recognized by `ImageIO.getWriterFormatNames()`. The input source must be a valid image format supported by the JVM.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further method chaining in the pipeline.

### Valid Call Patterns
```java
// Standard file output
Thumbnails.of(new File("path/to/directory").listFiles())
    .size(640, 480)
    .outputFormat("jpg")
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);

// In-memory processing
BufferedImage thumbnail = Thumbnails.of(img)
    .size(100, 100)
    .outputFormat("png")
    .asBufferedImage();
```

### LLM Instruction Prompt
- Use `outputFormat` only when writing to a file or stream; it has no effect when using `asBufferedImage()` or `asBufferedImages()`.
- Ensure the format string is a valid `ImageIO` writer format.
- Do not call this method more than once in a single builder chain.

### Prompt Snippet
```text
.outputFormat("jpg")
```

### Common Failure Modes
- **`IllegalStateException`**: Thrown if `outputFormat` is called more than once on the same builder instance.
- **`IllegalArgumentException`**: Thrown if the provided format string is not supported by the underlying Java `ImageIO` implementation.
- **No-op behavior**: Calling this method when using `asBufferedImage()` or `asBufferedImages()` will not affect the output, as these methods return raw `BufferedImage` objects rather than encoded files.

### Fix Code Hint
```java
// Ensure only one call to outputFormat exists in the chain
Thumbnails.of(source)
    .size(100, 100)
    .outputFormat("png") // Correct
    .toFile("output.png");
```

## API Test: `outputFormatType`

### Signature
```java
public Builder<T> outputFormatType(String formatType)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1762_

_Source doc:_ Sets the compression format type of the thumbnail to write. <p> If the default type for the compression codec should be used, a value of {@link ThumbnailParameter#DEFAULT_FORMAT_TYPE} should be used. <p> Calling this method to set this parameter is optional. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. <p> Furthermore, if this method is called, then calling the {@link #outputFormat} method is disabled, in order to prevent cases where the output format type does not exist in the format specified for the {@code outputFormat} method. <p> <em> Implementation note: Compression format type settings are ignored when the underlying image writer does not support compression. This behavior is subject to change in the future. </em> @param formatType	The compression format type @return				Reference to this object. @throws IllegalArgumentException	If an unsupported format type is specified for the current output format type. Or, if the output format has not been specified before this method was called.

### Goal
Configures the specific compression format type (e.g., "JPEG") for the thumbnail output to control how the underlying codec encodes the image data.

### Parameters
- `formatType` (`String`): The compression format type string. Use `ThumbnailParameter.DEFAULT_FORMAT_TYPE` to utilize the default type for the selected codec.

### Input
- **Preconditions:** The `outputFormat` must be specified prior to calling this method. The underlying image writer must support the specified compression format type.
- **Data:** A `String` representing a valid compression type supported by the `ImageWriter` for the chosen output format.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance to allow for method chaining.

### Valid Call Patterns
```java
// Standard usage with a supported format type
Thumbnails.of(img)
    .size(50, 50)
    .outputFormat("JPEG")
    .outputFormatType("JPEG");
```

### LLM Instruction Prompt
- When using `outputFormatType`, ensure that `outputFormat` has been called previously in the builder chain.
- Do not call `outputFormatType` more than once on the same builder instance, as it will trigger an `IllegalStateException`.
- Verify that the `formatType` provided is compatible with the `outputFormat` previously defined; otherwise, an `IllegalArgumentException` will be thrown.

### Prompt Snippet
```text
.outputFormat("JPEG").outputFormatType("JPEG")
```

### Common Failure Modes
- **`IllegalStateException`**: Occurs if `outputFormatType` is invoked more than once in the same pipeline.
- **`IllegalArgumentException`**: Occurs if `outputFormat` was not called before `outputFormatType`, or if the provided `formatType` is unsupported by the current output format's image writer (e.g., attempting to set a JPEG compression type on a PNG output).

### Fix Code Hint
```java
// Ensure outputFormat is set first and only call outputFormatType once
Thumbnails.of(source)
    .size(100, 100)
    .outputFormat("JPEG")
    .outputFormatType(ThumbnailParameter.DEFAULT_FORMAT_TYPE)
    .toFile("output.jpg");
```

## API Test: `outputQuality`

### Signature
```java
public Builder<T> outputQuality(float quality)
public Builder<T> outputQuality(double quality)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1550  (+1 more definition site/overload)_

_Source doc:_ Sets the output quality of the compression algorithm used to compress the thumbnail when it is written to an external destination such as a file or output stream. <p> The value is a {@code float} between {@code 0.0f} and {@code 1.0f} where {@code 0.0f} indicates the minimum quality and {@code 1.0f} indicates the maximum quality settings should be used for by the compression codec. <p> Calling this method to set this parameter is optional. <p> Calling this method in conjunction with {@link #asBufferedImage()} or {@link #asBufferedImages()} will not result in any changes to the final result. <p> Calling this method multiple times, or the {@link #outputQuality(double)} in conjunction with this method will result in an {@link IllegalStateException} to be thrown. <p> <em> Implementation note: Compression quality settings are ignored when the underlying image writer does not support compression. This behavior is subject to change in the future. </em> @param quality		The compression quality to use when writing the thumbnail. @return				Reference to this object. @throws IllegalArgumentException	If the argument is less than {@code 0.0f} or is greater than {@code 1.0f}.

### Goal
Configures the compression level for the thumbnail's output codec to balance file size and visual fidelity when writing to files or streams.

### Parameters
- `quality` (`float`): A value between `0.0f` (minimum quality/highest compression) and `1.0f` (maximum quality/lowest compression).

### Input
- **Data:** Requires a valid image source (File, InputStream, URL, or BufferedImage).
- **Preconditions:** The output destination must be an external sink (File or OutputStream). Compression settings are only effective if the underlying Java Image I/O writer for the chosen format supports compression.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance to allow for method chaining in the fluent pipeline.

### Valid Call Patterns
```java
// Using float for explicit quality control
Thumbnails.of(is)
    .size(50, 50)
    .outputFormat("jpg")
    .outputQuality(0.5f)
    .toOutputStream(os);
```

### LLM Instruction Prompt
- Use `outputQuality` only when writing to a file or output stream.
- Do not use this method if the pipeline terminates with `asBufferedImage()` or `asBufferedImages()`, as it has no effect.
- Ensure the `quality` argument is strictly within the `[0.0, 1.0]` range.
- Never call `outputQuality` more than once in a single builder chain.

### Prompt Snippet
```text
.outputQuality(0.8f)
```

### Common Failure Modes
- **IllegalStateException:** Thrown if `outputQuality` is called more than once, or if both `outputQuality(float)` and `outputQuality(double)` are used in the same chain.
- **IllegalArgumentException:** Thrown if the provided `quality` value is outside the `0.0f` to `1.0f` range.
- **Ignored Setting:** The quality setting will have no effect if the output format (e.g., some BMP configurations) does not support compression via the host JVM's `ImageIO`.

### Fix Code Hint
```java
// Ensure only one call is made and values are within bounds
Thumbnails.of(source)
    .size(100, 100)
    .outputQuality(0.75f) // Set once
    .toFile("output.jpg");
```

## API Test: `preferredOutputFormatName`

### Signature
```java
public String preferredOutputFormatName()
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/AbstractImageSink.java:72  (+2 more definition site/overload)_

### Goal
Retrieves the output format name associated with the destination sink to determine the appropriate encoding for the thumbnail.

### Parameters
_None._

### Input
The caller must ensure the `ImageSink` has been initialized or configured with a destination (e.g., a file path or stream) that allows the underlying implementation to infer a format. If the format cannot be determined, the implementation should return `ThumbnailParameter.ORIGINAL_FORMAT`.

### Output
Returns `String` — The name of the preferred output format (e.g., "jpg", "png"). If the format is indeterminate, it returns the constant `ThumbnailParameter.ORIGINAL_FORMAT`.

### Valid Call Patterns
```java
// As seen in SourceSinkThumbnailTaskTest.java
String format = destination.preferredOutputFormatName();
```

### LLM Instruction Prompt
- Use `preferredOutputFormatName()` only when interacting with an `ImageSink` object to query its intended output format.
- Do not assume the returned string is a valid file extension without validation, as it reflects the format name recognized by the underlying Java Image I/O API.
- If the returned value is `ThumbnailParameter.ORIGINAL_FORMAT`, the system should fall back to using the input image's format name.

### Prompt Snippet
```text
// Example of querying the sink's preferred format
ImageSink destination = ...;
String format = destination.preferredOutputFormatName();
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `ImageSink` instance is null.
- **Indeterminate Format**: If the sink is not associated with a file or stream that provides format metadata, the method may return `ThumbnailParameter.ORIGINAL_FORMAT`, which requires the caller to handle the format resolution logic manually.
- **Unsupported Format**: The returned string may represent a format not supported by the current JVM's `ImageIO` registry, leading to failures during the subsequent `write` operation.

### Fix Code Hint
```java
String format = destination.preferredOutputFormatName();
if (ThumbnailParameter.ORIGINAL_FORMAT.equals(format)) {
    format = source.getInputFormatName();
}
destination.setOutputFormatName(format);
```

## API Test: `quality`

### Signature
```java
public ThumbnailParameterBuilder quality(float quality)
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:232_

_Source doc:_ Sets the compression quality setting of the thumbnail. <p> An acceptable value is in the range of {@code 0.0f} to {@code 1.0f}, where {@code 0.0f} is for the lowest quality setting and {@code 1.0f} for the highest quality setting. <p> If the default compression quality is to be used, then the value {@link ThumbnailParameter#DEFAULT_QUALITY} should be used. @param quality		The compression quality setting of the thumbnail. @return				A reference to this object.

### Goal
Configures the compression quality setting for the output thumbnail to balance file size and visual fidelity.

### Parameters
- `quality` (`float`): A value between `0.0f` (lowest quality/highest compression) and `1.0f` (highest quality/lowest compression). Use `ThumbnailParameter.DEFAULT_QUALITY` to revert to the library default.

### Input
- The caller must provide a `float` value within the specified range.
- Precondition: The output format must support compression (e.g., JPEG). If the output format does not support quality settings, this parameter may be ignored by the underlying `ImageIO` writer.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the builder instance, allowing for method chaining in the fluent pipeline.

### Valid Call Patterns
```java
// Inferred from signature:
builder.quality(0.85f);

// Using the default constant:
builder.quality(ThumbnailParameter.DEFAULT_QUALITY);
```

### LLM Instruction Prompt
- When using `quality`, ensure the value is a `float` between `0.0f` and `1.0f`.
- If the user does not specify a quality, do not assume a value; use `ThumbnailParameter.DEFAULT_QUALITY` if explicit configuration is required.
- Always chain this method within a `ThumbnailParameterBuilder` context.

### Prompt Snippet
```text
.quality(0.75f)
```

### Common Failure Modes
- Passing a value outside the `0.0f` to `1.0f` range, which may result in `IllegalArgumentException` depending on the underlying `ImageWriter` implementation.
- Applying `quality` to formats that do not support compression settings (e.g., some BMP or uncompressed TIFF configurations), leading to the setting being ignored.

### Fix Code Hint
```java
// Ensure the value is clamped or explicitly set to the default constant
float targetQuality = Math.max(0.0f, Math.min(1.0f, inputQuality));
builder.quality(targetQuality);
```

## API Test: `read`

### Signature
```java
public BufferedImage read()
public abstract BufferedImage read()
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/SourceSinkThumbnailTask.java:94  (+8 more definition site/overload)_

### Goal
Reads the source image data into a `BufferedImage` object to facilitate subsequent thumbnail processing or transformation.

### Parameters
_None._

### Input
The input source must be a valid image format supported by the host JVM's `ImageIO` (e.g., JPEG, PNG). The source must be initialized and accessible (e.g., a valid `File`, `InputStream`, `URL`, or `BufferedImage`) before calling this method.

### Output
Returns `BufferedImage` — An in-memory representation of the source image, ready for processing within the Thumbnailator pipeline.

### Valid Call Patterns
```java
// Inferred from signature:
BufferedImage image = task.read();
```

### LLM Instruction Prompt
- Use `read()` only when an instance of a task (such as `SourceSinkThumbnailTask`) is available.
- Ensure the source image is accessible and in a format supported by the JVM's `ImageIO` before invocation.
- Note that this method is subject to future changes to support tile-by-tile reading for large images.

### Prompt Snippet
```text
// Example of reading an image via a task
BufferedImage sourceImage = task.read();
```

### Common Failure Modes
- **IOException**: Occurs if the input stream is closed, the file is inaccessible, or the image data is corrupted.
- **UnsupportedFormatException**: Occurs if the source image format is not recognized by the underlying Java `ImageIO` implementation.
- **Memory Constraints**: Large images may trigger `OutOfMemoryError` as `read()` loads the entire image into memory.

### Fix Code Hint
```java
try {
    BufferedImage img = task.read();
} catch (IOException e) {
    // Handle read error (e.g., log or rethrow)
    e.printStackTrace();
}
```

## API Test: `region`

### Signature
```java
public ThumbnailParameterBuilder region(Region sourceRegion)
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:200_

_Source doc:_ Sets the region of the source image to use when creating a thumbnail. @param sourceRegion		The region of the source image to use when creating a thumbnail. @return			A reference to this object. @since	0.3.4

### Goal
Defines a specific rectangular area of the source image to be used as the input for the thumbnail generation process, effectively cropping the source before scaling.

### Parameters
- `sourceRegion` (`Region`): A `Region` object defining the area to extract, typically composed of a `Position` (e.g., `Positions.TOP_LEFT`) and a `Size` (e.g., `AbsoluteSize`).

### Input
- **Data:** A valid `BufferedImage` or image source supported by the JVM's `ImageIO`.
- **Preconditions:** The `Region` must be defined such that it is compatible with the source image dimensions. If the region exceeds the source image boundaries, the library will attempt to constrain the operation to the available image area.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, allowing for method chaining in the fluent pipeline.

### Valid Call Patterns
```java
new ThumbnailParameterBuilder()
    .region(new Region(Positions.TOP_LEFT, new AbsoluteSize(40, 40)))
    .size(20, 20)
    .build();
```

### LLM Instruction Prompt
- When using `region`, always instantiate a `Region` object using a valid `Position` and `Size` implementation from the `net.coobird.thumbnailator.geometry` package.
- Ensure the `region` call is chained within a `ThumbnailParameterBuilder` before passing the parameter to a source or task.

### Prompt Snippet
```text
.region(new Region(Positions.CENTER, new AbsoluteSize(width, height)))
```

### Common Failure Modes
- **Boundary Mismatch:** Providing a `Region` that is larger than the source image may result in unexpected cropping behavior or `RasterFormatException` if the underlying `getSubimage` call is invoked with invalid coordinates.
- **Null Argument:** Passing `null` as the `sourceRegion` will result in a `NullPointerException`.
- **Incorrect Geometry:** Using incompatible `Size` implementations that do not resolve to pixel dimensions when the pipeline expects absolute values.

### Fix Code Hint
```java
// Ensure the region is within the bounds of the source image
Region validRegion = new Region(Positions.TOP_LEFT, new AbsoluteSize(100, 100));
builder.region(validRegion);
```

## API Test: `rendering`

### Signature
```java
public Builder<T> rendering(Rendering config)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1448_

_Source doc:_ Sets the rendering mode when performing the resizing operation to generate the thumbnail. <p> Calling this method to set this parameter is optional. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. <p> This method cannot be called in conjunction with the {@link #resizerFactory(ResizerFactory)} method. @param config		The rendering mode. @return				Reference to this object.

### Goal
Configures the specific rendering hints used by the Java 2D pipeline during the image resizing process to balance quality and performance.

### Parameters
- `config` (`Rendering`): The rendering mode to apply, typically chosen from `Rendering.DEFAULT`, `Rendering.QUALITY`, or `Rendering.SPEED`.

### Input
- **Data:** Requires a valid `Rendering` enum constant.
- **Preconditions:** The input image must be a `BufferedImage` or a source supported by `ImageIO`. This method must not be called if `resizerFactory(ResizerFactory)` has already been invoked in the builder chain.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further method chaining in the fluent pipeline.

### Valid Call Patterns
```java
BufferedImage thumbnail = Thumbnails.of(img)
        .size(50, 50)
        .rendering(Rendering.DEFAULT)
        .asBufferedImage();
```

### LLM Instruction Prompt
- When using `rendering`, ensure it is called only once per builder instance.
- Do not combine `rendering` with `resizerFactory` in the same pipeline.
- Always provide a non-null `Rendering` constant to avoid `NullPointerException`.

### Prompt Snippet
```text
.rendering(Rendering.QUALITY)
```

### Common Failure Modes
- **`IllegalStateException`**: Thrown if `rendering` is called more than once on the same builder instance.
- **`IllegalStateException`**: Thrown if `rendering` is called after `resizerFactory` has been set.
- **`NullPointerException`**: Thrown if the `config` argument passed is `null`.

### Fix Code Hint
```java
// Ensure rendering is set only once and not with resizerFactory
Thumbnails.of(sourceImage)
    .size(100, 100)
    .rendering(Rendering.QUALITY) // Set once
    .toFile("output.jpg");
```

## API Test: `resize`

### Signature
```java
public void resize(BufferedImage srcImage, BufferedImage destImage)
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/BilinearResizer.java:72  (+6 more definition site/overload)_

### Goal
Performs a low-level resize operation by scaling the source `BufferedImage` and drawing the result into the provided destination `BufferedImage`.

### Parameters
- `srcImage` (`BufferedImage`): The source image to be resized.
- `destImage` (`BufferedImage`): The destination image where the resized content will be drawn.

### Input
The caller must provide two initialized `BufferedImage` objects. The `srcImage` must be a valid image loaded into memory. The `destImage` should be pre-allocated with the desired dimensions for the output. Both images must be non-null.

### Output
Returns `void` — The operation modifies the `destImage` in-place by rendering the scaled `srcImage` into it.

### Valid Call Patterns
```java
// Example derived from test suite usage (e.g., BilinearResizer or ProgressiveBilinearResizer)
resizer.resize(srcImage, destImage);
```

### LLM Instruction Prompt
- When using `resize`, ensure both `srcImage` and `destImage` are instantiated `BufferedImage` objects.
- Do not pass `null` values, as this will trigger a `NullPointerException`.
- This method is typically invoked by a `Resizer` implementation; ensure the `destImage` has been allocated with the target dimensions before calling.

### Prompt Snippet
```text
// Ensure images are initialized before calling resize
BufferedImage src = ...;
BufferedImage dest = new BufferedImage(width, height, BufferedImage.TYPE_INT_ARGB);
resizer.resize(src, dest);
```

### Common Failure Modes
- **NullPointerException**: Occurs if either `srcImage` or `destImage` is `null`.
- **IllegalArgumentException**: May occur if the `destImage` is not compatible with the graphics context or if dimensions are invalid (depending on the specific `Resizer` implementation).
- **Inconsistent Dimensions**: If `destImage` is not properly sized for the intended output, the resulting thumbnail may appear distorted or clipped.

### Fix Code Hint
```java
if (srcImage != null && destImage != null) {
    resizer.resize(srcImage, destImage);
} else {
    throw new IllegalArgumentException("Source and destination images must not be null.");
}
```

## API Test: `resizer`

### Signature
```java
public Builder<T> resizer(Resizer resizer)
public ThumbnailParameterBuilder resizer(Resizer resizer)
public ThumbnailMaker resizer(Resizer resizer)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1323  (+2 more definition site/overload)_

_Source doc:_ Sets the resizing operation to use when creating the thumbnail. <p> Calling this method to set this parameter is optional. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. <p> This method cannot be called in conjunction with the {@link #resizerFactory(ResizerFactory)} method. @param resizer		The scaling operation to use. @return				Reference to this object.

### Goal
Configures the specific scaling algorithm used to perform the image resizing operation within the thumbnail pipeline.

### Parameters
- `resizer` (`Resizer`): An implementation of the `Resizer` interface (e.g., `BicubicResizer`, `BilinearResizer`, or `ProgressiveBilinearResizer`) that defines the mathematical approach for scaling the source image.

### Input
- **Data:** A `BufferedImage` provided via the `Thumbnails.of()` builder.
- **Preconditions:** The input image must be a valid `BufferedImage` supported by the JVM. The `resizer` method must not be called if `resizerFactory` has already been configured in the same builder chain.

### Output
Returns `Builder<T>` — A reference to the current builder instance, allowing for further chaining of thumbnail operations.

### Valid Call Patterns
```java
// Using a specific Resizer implementation from net.coobird.thumbnailator.resizers
BufferedImage thumbnail = Thumbnails.of(img)
    .size(200, 200)
    .resizer(Resizers.PROGRESSIVE)
    .asBufferedImage();
```

### LLM Instruction Prompt
- When generating code using `resizer`, ensure it is called exactly once per builder chain.
- Do not combine `resizer()` with `resizerFactory()` in the same pipeline.
- Use implementations provided in `net.coobird.thumbnailator.resizers` (e.g., `Resizers.PROGRESSIVE`, `Resizers.BICUBIC`) as the argument.

### Prompt Snippet
```text
.resizer(Resizers.PROGRESSIVE)
```

### Common Failure Modes
- **IllegalStateException:** Thrown if `resizer()` is called more than once on the same builder instance.
- **IllegalStateException:** Thrown if `resizer()` is called after `resizerFactory()` has already been invoked.
- **NullPointerException:** Occurs if the `resizer` argument passed is `null`.

### Fix Code Hint
```java
// Ensure only one resizer is set and avoid mixing with resizerFactory
Thumbnails.of(sourceImage)
    .size(100, 100)
    .resizer(Resizers.BICUBIC) // Set once
    .toFile("output.jpg");
```

## API Test: `resizerFactory`

### Signature
```java
public Builder<T> resizerFactory(ResizerFactory resizerFactory)
public ThumbnailParameterBuilder resizerFactory(ResizerFactory resizerFactory)
public ThumbnailMaker resizerFactory(ResizerFactory resizerFactory)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1348  (+2 more definition site/overload)_

_Source doc:_ Sets the {@link ResizerFactory} object to use to decide what kind of resizing operation is to be used when creating the thumbnail. <p> Calling this method to set this parameter is optional. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. <p> This method cannot be called in conjunction with the {@link #resizer(Resizer)} method. @param resizerFactory		The scaling operation to use. @return						Reference to this object. @since	0.4.0

### Goal
Configures the thumbnail pipeline to use a specific `ResizerFactory` implementation to dynamically determine the optimal scaling algorithm based on the source and destination image dimensions.

### Parameters
- `resizerFactory` (`ResizerFactory`): An implementation of the `ResizerFactory` interface (e.g., `DefaultResizerFactory.getInstance()`) that provides the `Resizer` logic for the transformation.

### Input
- **Data:** A valid `ResizerFactory` instance.
- **Preconditions:** The input image must be loaded and accessible to the pipeline. The caller must ensure that this method is not called if `.resizer(Resizer)` has already been invoked in the same builder chain.

### Output
Returns `Builder<T>` — A reference to the current builder instance, allowing for further method chaining in the fluent pipeline.

### Valid Call Patterns
```java
// Using ThumbnailParameterBuilder
ThumbnailParameter param = new ThumbnailParameterBuilder()
        .size(100, 100)
        .resizerFactory(DefaultResizerFactory.getInstance())
        .build();
```

### LLM Instruction Prompt
- When using `resizerFactory`, ensure it is never paired with `.resizer(Resizer)` in the same builder chain.
- Only call this method once per builder instance to avoid `IllegalStateException`.
- Use `DefaultResizerFactory.getInstance()` if no custom scaling logic is required.

### Prompt Snippet
```text
.resizerFactory(DefaultResizerFactory.getInstance())
```

### Common Failure Modes
- **IllegalStateException:** Thrown if `resizerFactory` is called more than once on the same builder.
- **IllegalStateException:** Thrown if the builder has already been configured with a specific `Resizer` via the `.resizer(Resizer)` method.
- **NullPointerException:** Occurs if the passed `resizerFactory` argument is `null`.

### Fix Code Hint
```java
// Ensure mutual exclusivity: use either resizer() OR resizerFactory()
Thumbnails.of("input.jpg")
    .size(100, 100)
    .resizerFactory(DefaultResizerFactory.getInstance()) // Correct
    .toFile("output.jpg");
```

## API Test: `rotate`

### Signature
```java
public Builder<T> rotate(double angle)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1969_

_Source doc:_ Sets the amount of rotation to apply to the thumbnail. <p> The thumbnail will be rotated clockwise by the angle specified. <p> This method can be called multiple times to apply multiple rotations. <p> If multiple rotations are to be applied, the rotations will be applied in the order that this method is called. <p> Calling this method to set this parameter is optional. @param angle			Angle in degrees. @return				Reference to this object.

### Goal
Applies a clockwise rotation to the thumbnail image within the fluent processing pipeline.

### Parameters
- `angle` (`double`): The rotation angle in degrees. Positive values rotate the image clockwise.

### Input
The input must be a valid image source (e.g., `File`, `InputStream`, `URL`, or `BufferedImage`) already initialized within a `Thumbnails.Builder` pipeline. The image must be in a format supported by the host JVM's `ImageIO`.

### Output
Returns `Builder<T>` — A reference to the current `Builder` instance, allowing for further chaining of thumbnail operations.

### Valid Call Patterns
```java
// Inferred from signature and project usage patterns
BufferedImage thumbnail = Thumbnails.of(sourceImage)
    .size(160, 160)
    .rotate(90.0)
    .asBufferedImage();
```

### LLM Instruction Prompt
- When using `rotate`, ensure it is chained within a `Thumbnails.of(...)` builder pipeline.
- Multiple calls to `rotate` are permitted; the rotations are cumulative and applied in the order they appear in the code.
- The `angle` parameter is a `double` representing degrees; ensure the value is provided as a numeric literal or variable.

### Prompt Snippet
```text
.rotate(double angle)
```

### Common Failure Modes
- **Invalid Input Source:** Providing a null or unsupported image format to the initial `Thumbnails.of(...)` call will cause the pipeline to fail during execution.
- **Cumulative Rotation Errors:** Calling `rotate` multiple times with incorrect angles may result in unexpected orientations if the order of operations is not carefully managed.
- **Headless Environment Constraints:** While `rotate` is headless-safe, ensure the environment has sufficient heap memory to handle the image transformation, especially for large source images.

### Fix Code Hint
```java
// Ensure the builder is initialized and the rotation is applied before output
Thumbnails.of(imageFile)
    .size(100, 100)
    .rotate(45.0) // Rotate 45 degrees clockwise
    .rotate(45.0) // Rotate an additional 45 degrees (total 90)
    .toFile("output.jpg");
```

## API Test: `scale`

### Signature
```java
public Builder<T> scale(double scale)
public Builder<T> scale(double scaleWidth, double scaleHeight)
public ThumbnailParameterBuilder scale(double scalingFactor)
public ThumbnailParameterBuilder scale(double widthScalingFactor, double heightScalingFactor)
public ScaledThumbnailMaker scale(double factor)
public ScaledThumbnailMaker scale(double widthFactor, double heightFactor)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1023  (+5 more definition site/overload)_

_Source doc:_ Sets the scaling factor for the width and height of the thumbnail. <p> If the scaling factor for the width and height are not equal, then the thumbnail will not preserve the aspect ratio of the original image. <p> For example, to create thumbnails which are 50% the width of the original, while 75% the height of the original, the following code can be used: <pre><code> Thumbnails.of(image) .scale(0.5, 0.75) .toFile(thumbnail); </code></pre> <p> Once this method is called, calling the {@link #size(int, int)} method, or the {@link #scale(double)} method, or the {@link #keepAspectRatio(boolean)} method will result in an {@link IllegalStateException}. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. @param scaleWidth	The scaling factor to use for the width when creating a thumbnail. <p> The value must be a {@code double} which is greater than {@code 0.0}, and not {@link Double#POSITIVE_INFINITY}. @param scaleHeight	The scaling factor to use for the height when creating a thumbnail. <p> The value must be a {@code double} which is greater than {@code 0.0}, and not {@link Double#POSITIVE_INFINITY}. @return				Reference to this object. @since 	0.3.10

### Goal
Sets the scaling factor for the width and height of the thumbnail to transform the original image dimensions by the specified multipliers.

### Parameters
- `scaleWidth` (`double`): The scaling factor for the width; must be greater than 0.0 and not `Double.POSITIVE_INFINITY`.
- `scaleHeight` (`double`): The scaling factor for the height; must be greater than 0.0 and not `Double.POSITIVE_INFINITY`.

### Input
The input must be a valid image source (File, InputStream, URL, or BufferedImage) supported by the JVM's `ImageIO`. The pipeline must be initialized via `Thumbnails.of(...)`.

### Output
Returns `Builder<T>` — A reference to the current builder object, allowing for further chaining of operations before final output.

### Valid Call Patterns
```java
// Scaling width and height independently
Thumbnails.of(image)
    .scale(0.5, 0.75)
    .toFile(thumbnail);
```

### LLM Instruction Prompt
- When using `scale`, ensure it is called exactly once in the builder chain.
- Do not combine `scale` with `size(int, int)`, `keepAspectRatio(boolean)`, or another `scale` call, as these will trigger an `IllegalStateException`.
- Ensure scaling factors are positive, non-infinite `double` values.

### Prompt Snippet
```text
.scale(widthFactor, heightFactor)
```

### Common Failure Modes
- **IllegalStateException**: Occurs if `scale` is called more than once, or if it is called after `size(int, int)`, `scale(double)`, or `keepAspectRatio(boolean)`.
- **IllegalArgumentException**: Occurs if the provided scaling factor is less than or equal to 0.0 or is `Double.POSITIVE_INFINITY`.

### Fix Code Hint
```java
// Ensure only one scaling method is used in the chain
Thumbnails.of(source)
    .scale(0.5, 0.5) // Correct: single call
    .toFile("output.jpg");
```

## API Test: `scalingMode`

### Signature
```java
public Builder<T> scalingMode(ScalingMode config)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1300_

_Source doc:_ Sets the resizing scaling mode to use when creating the thumbnail. <p> Calling this method to set this parameter is optional. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. @param config		The scaling mode to use. @return				Reference to this object.

### Goal
Configures the specific scaling algorithm used by the `Thumbnails.Builder` to perform image resizing operations.

### Parameters
- `config` (`ScalingMode`): The specific `ScalingMode` implementation to apply (e.g., `ScalingMode.PROGRESSIVE_BILINEAR`), which dictates the trade-off between image quality and processing speed.

### Input
- **Data:** A `BufferedImage` or image source previously initialized via `Thumbnails.of()`.
- **Preconditions:** The input must be a valid image format supported by the JVM's `ImageIO`. The `scalingMode` method must be called only once in the builder chain.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further method chaining in the fluent pipeline.

### Valid Call Patterns
```java
BufferedImage img = new BufferedImageBuilder(200, 200).build();

Thumbnails.of(img)
    .size(200, 200)
    .scalingMode(ScalingMode.PROGRESSIVE_BILINEAR)
    .asBufferedImage();
```

### LLM Instruction Prompt
- When using `scalingMode`, ensure it is called exactly once in the builder chain.
- Always pass a valid `ScalingMode` constant from the `net.coobird.thumbnailator.resizers` package.
- Do not attempt to call this method after the thumbnail has already been processed or output.

### Prompt Snippet
```text
.scalingMode(ScalingMode.PROGRESSIVE_BILINEAR)
```

### Common Failure Modes
- **`IllegalStateException`**: Occurs if `scalingMode` is invoked more than once on the same `Builder` instance.
- **NullPointerException**: Occurs if the `config` argument passed is `null`.
- **Incompatible Resizer**: Attempting to use a scaling mode that is not supported by the current JVM environment or image type.

### Fix Code Hint
```java
// Ensure only one scaling mode is set per builder instance
Thumbnails.of(sourceImage)
    .size(100, 100)
    .scalingMode(ScalingMode.BICUBIC) // Correct: single invocation
    .toFile("output.jpg");
```

## API Test: `setOutputFormatName`

### Signature
```java
public void setOutputFormatName(String format)
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/AbstractImageSink.java:54  (+3 more definition site/overload)_

### Goal
Sets the output format of the resulting image for an `ImageSink`, determining the encoding used when the image is written to the destination.

### Parameters
- `format` (`String`): The file format name (e.g., "jpg", "png") with which to store the image.

### Input
- **Data:** A `String` representing a format supported by the host JVM's `ImageIO` capabilities.
- **Preconditions:** The `ImageSink` must be initialized. Note that for `ImageSink` implementations that store raw images, the format name specified by this method may be ignored.

### Output
Returns `void` — this method performs a state change on the `ImageSink` instance to configure the output encoding.

### Valid Call Patterns
```java
// Inferred from test suite usage (SourceSinkThumbnailTaskTest.java)
destination.setOutputFormatName("42");
```

### LLM Instruction Prompt
- When using `setOutputFormatName`, ensure the `format` string corresponds to a format supported by the underlying Java `ImageIO` environment.
- If the goal is to preserve the original format, use `ThumbnailParameter.ORIGINAL_FORMAT`.
- If the goal is to allow automatic format detection, use `ThumbnailParameter.DETERMINE_FORMAT`.
- Note that this method is typically called internally by the `Thumbnailator` task execution pipeline; manual invocation is required only when interacting directly with an `ImageSink`.

### Prompt Snippet
```text
// Example of setting output format on an ImageSink
ImageSink destination = ...; // An implementation of ImageSink
destination.setOutputFormatName("png");
```

### Common Failure Modes
- **Unsupported Format:** Providing a format string that the host JVM's `ImageIO` cannot encode (e.g., requesting "gif" on a restricted environment).
- **Ignored Configuration:** Calling this on an `ImageSink` that handles raw image data, where the format name is ignored by design.
- **Null/Empty String:** Passing an invalid or empty string may result in `ImageIO` failing to locate a suitable `ImageWriter` during the `write` operation.

### Fix Code Hint
```java
// Ensure the format is supported by the JVM
try {
    destination.setOutputFormatName("jpg");
} catch (Exception e) {
    // Handle potential format incompatibility
}
```

## API Test: `setThumbnailParameter`

### Signature
```java
public void setThumbnailParameter(ThumbnailParameter param)
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/AbstractImageSink.java:58  (+6 more definition site/overload)_

### Goal
Configures the image processing task with specific parameters, such as scaling, cropping, or quality settings, to be applied during the image writing process.

### Parameters
- `param` (`ThumbnailParameter`): A `ThumbnailParameter` object containing the configuration settings (e.g., source region, output size, image type) to be used when storing the image.

### Input
- **Data:** A `ThumbnailParameter` instance, typically constructed via `ThumbnailParameterBuilder`.
- **Preconditions:** The `ThumbnailParameter` must be fully configured before being passed to the sink. If defining a source region, ensure the region coordinates and dimensions are within the bounds of the source image to avoid `RasterFormatException` during the read operation.

### Output
Returns `void` — This method updates the internal state of the image sink to prepare for the subsequent write operation.

### Valid Call Patterns
```java
BufferedImageSource source = new BufferedImageSource(sourceImage);
source.setThumbnailParameter(
    new ThumbnailParameterBuilder()
        .region(new Region(Positions.TOP_LEFT, new AbsoluteSize(40, 40)))
        .size(20, 20)
        .build()
);
```

### LLM Instruction Prompt
- When calling `setThumbnailParameter`, ensure the `ThumbnailParameter` object is instantiated using `ThumbnailParameterBuilder`.
- Always verify that the `ThumbnailParameter` settings (like `region` or `size`) are compatible with the source image dimensions before invoking the task.

### Prompt Snippet
```text
// Example of setting parameters on a source
source.setThumbnailParameter(new ThumbnailParameterBuilder().size(width, height).build());
```

### Common Failure Modes
- **`RasterFormatException`**: Occurs if the `Region` defined in the `ThumbnailParameter` exceeds the boundaries of the source image.
- **Null Pointer Exception**: Occurs if a `null` value is passed as the `param` argument.
- **Invalid Image Type**: Providing an unsupported image type in the `ThumbnailParameter` may lead to errors during the `ImageIO` write phase.

### Fix Code Hint
```java
// Ensure the region is within bounds
ThumbnailParameter param = new ThumbnailParameterBuilder()
    .region(new Region(Positions.TOP_LEFT, new AbsoluteSize(source.getWidth(), source.getHeight())))
    .size(targetWidth, targetHeight)
    .build();
source.setThumbnailParameter(param);
```

## API Test: `size`

### Signature
```java
public Builder<T> size(int width, int height)
public BufferedImageBuilder size(int width, int height)
public ThumbnailParameterBuilder size(Dimension size)
public ThumbnailParameterBuilder size(int width, int height)
public int size()
public FixedSizeThumbnailMaker size(int width, int height)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:843  (+5 more definition site/overload)_

_Source doc:_ Sets the size of the thumbnail. <p> For example, to create thumbnails which should fit within a bounding rectangle of 640 x 480, the following code can be used: <pre><code> Thumbnails.of(image) .size(640, 480) .toFile(thumbnail); </code></pre> <p> In the above code, the thumbnail will preserve the aspect ratio of the original image. If the thumbnail should be forced to the specified size, the {@link #forceSize(int, int)} method can be used instead of this method. <p> Once this method is called, calling the {@link #scale(double)} method will result in an {@link IllegalStateException}. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. @param width			The width of the thumbnail. @param height		The height of the thumbnail. @return				Reference to this object.

### Goal
Defines the target dimensions for a thumbnail, ensuring the output image fits within the specified bounding box while preserving the original image's aspect ratio.

### Parameters
- `width` (`int`): The target width of the thumbnail in pixels.
- `height` (`int`): The target height of the thumbnail in pixels.

### Input
The caller must provide a valid image source (e.g., `File`, `InputStream`, `URL`, or `BufferedImage`) supported by the JVM's `ImageIO`. The input must be prepared as part of a `Thumbnails.of(...)` fluent pipeline.

### Output
Returns `Builder<T>` — A reference to the current builder object, allowing for further chaining of operations like `rotate`, `watermark`, or output methods.

### Valid Call Patterns
```java
// Basic fluent pipeline usage
Thumbnails.of(new File("path/to/directory").listFiles())
    .size(640, 480)
    .outputFormat("jpg")
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);

// In-memory processing
BufferedImage thumbnail = Thumbnails.of(sourceImage)
    .size(160, 160)
    .asBufferedImage();
```

### LLM Instruction Prompt
- Always use `size(int, int)` within a `Thumbnails.of(...)` builder chain.
- Do not call `size` more than once in a single pipeline.
- Do not combine `size` with `scale` in the same pipeline, as this will trigger an `IllegalStateException`.
- If the exact dimensions are required regardless of aspect ratio, suggest `forceSize` instead.

### Prompt Snippet
```text
// Example of setting thumbnail size in a pipeline:
Thumbnails.of(sourceImage).size(width, height).toFile(outputFile);
```

### Common Failure Modes
- `IllegalStateException`: Thrown if `size` is called more than once on the same builder instance.
- `IllegalStateException`: Thrown if `size` is called after `scale` has already been invoked in the pipeline.
- `IllegalArgumentException`: May occur if non-positive dimensions are provided.

### Fix Code Hint
```java
// Ensure size is called exactly once and not mixed with scale
Thumbnails.of(image)
    .size(800, 600) // Correct: sets dimensions once
    .toFile("output.jpg");
```

## API Test: `sourceRegion`

### Signature
```java
public Builder<T> sourceRegion(Region sourceRegion)
public Builder<T> sourceRegion(Position position, Size size)
public Builder<T> sourceRegion(int x, int y, int width, int height)
public Builder<T> sourceRegion(Position position, int width, int height)
public Builder<T> sourceRegion(Rectangle region)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1117  (+4 more definition site/overload)_

_Source doc:_ Specifies the region of the source image where the thumbnail will be created from. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. @param x				The horizontal-component of the top left-hand corner of the source region. @param y				The vertical-component of the top left-hand corner of the source region. @param width			Width of the source region. @param height		Height of the source region. @return				Reference to this object. @throws IllegalArgumentException	If the width and/or height is less than or equal to {@code 0}. @since 	0.3.4

### Goal
Defines a specific rectangular sub-region of the source image to be used as the input for the thumbnail generation process, effectively cropping the image before any resizing or filtering occurs.

### Parameters
- `x` (`int`): The horizontal coordinate (in pixels) of the top-left corner of the source region.
- `y` (`int`): The vertical coordinate (in pixels) of the top-left corner of the source region.
- `width` (`int`): The width of the source region in pixels; must be greater than 0.
- `height` (`int`): The height of the source region in pixels; must be greater than 0.

### Input
The caller must provide a valid source image (via `Thumbnails.of(...)`) such as a `File`, `InputStream`, `URL`, or `BufferedImage`. The source region coordinates must fall within the bounds of the source image dimensions.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further chaining of operations like `.size()`, `.rotate()`, or `.toFile()`.

### Valid Call Patterns
```java
// Using explicit coordinates
BufferedImage thumbnail = Thumbnails.of(sourceImage)
    .sourceRegion(0, 0, 50, 50)
    .size(50, 50)
    .asBufferedImage();

// Using a Region object
BufferedImage thumbnail = Thumbnails.of(img)
    .sourceRegion(new Region(new Coordinate(0, 0), new AbsoluteSize(50, 50)))
    .size(50, 50)
    .asBufferedImage();
```

### LLM Instruction Prompt
- Always ensure `width` and `height` are positive integers.
- Do not call `sourceRegion` more than once in a single pipeline, as it will trigger an `IllegalStateException`.
- Ensure the `sourceRegion` coordinates are within the bounds of the source image to avoid unexpected cropping behavior.

### Prompt Snippet
```text
.sourceRegion(x, y, width, height)
```

### Common Failure Modes
- `IllegalStateException`: Thrown if `sourceRegion` is invoked multiple times on the same builder instance.
- `IllegalArgumentException`: Thrown if `width` or `height` is less than or equal to 0.
- `NullPointerException`: Thrown if a `null` object (e.g., `Region` or `Rectangle`) is passed to the overloaded methods.

### Fix Code Hint
```java
// Ensure only one sourceRegion call exists in the chain
Thumbnails.of(image)
    .sourceRegion(0, 0, 100, 100) // Correct
    .size(50, 50)
    .toFile("output.jpg");
```

## API Test: `toFile`

### Signature
```java
public void toFile(File outFile)
public void toFile(String outFilepath)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2652  (+1 more definition site/overload)_

_Source doc:_ Create a thumbnail and writes it to a {@link File}. <p> When the destination file exists, and overwriting files has been disabled by calling the {@link #allowOverwrite(boolean)} method with {@code false}, then an {@link IllegalArgumentException} will be thrown. <p> To call this method, the thumbnail must have been created from a single source. @param outFile			The file to which the thumbnail is to be written to. @throws IOException		If a problem occurs while reading the original images or writing the thumbnails to files. @throws IllegalArgumentException		If multiple original image files are	specified, or if the destination file exists, and overwriting files is disabled.

### Goal
Writes the processed thumbnail image to a specified file system location, completing the fluent thumbnail pipeline.

### Parameters
- `outFile` (`File`): The destination file object where the thumbnail will be saved.

### Input
- **Data:** A single source image (provided via `Thumbnails.of(...)` using `File`, `InputStream`, `URL`, or `BufferedImage`).
- **Preconditions:** The thumbnail pipeline must be initialized with a single source. The host JVM's `ImageIO` must support the output format (determined by file extension or explicit `.outputFormat()` call).
- **Constraints:** If the destination file exists, overwriting is enabled by default; if `allowOverwrite(false)` was called, an `IllegalArgumentException` will occur.

### Output
Returns `void` — The method performs a side-effect by writing the image data to the specified file path.

### Valid Call Patterns
```java
// Writing to a File object
Thumbnails.of(sourceFile)
    .size(100, 100)
    .toFile(new File("path/to/thumbnail.png"));

// Writing to a String path
Thumbnails.of("image.jpg")
    .size(200, 200)
    .toFile("thumbnail.jpg");
```

### LLM Instruction Prompt
- Use `toFile` only when the pipeline is configured for a single source image.
- Ensure the destination path is writable.
- If the output format is not explicitly set via `.outputFormat()`, the library infers the format from the file extension provided in the `toFile` argument.

### Prompt Snippet
```text
Thumbnails.of(source).size(width, height).toFile(destinationPath);
```

### Common Failure Modes
- **`IllegalArgumentException`**: Occurs if multiple source images were provided to the builder or if the destination file exists while `allowOverwrite(false)` is set.
- **`IOException`**: Occurs if there are permission issues, disk space limitations, or if the source image is corrupted/unreadable.
- **Format Mismatch**: If the file extension does not match the intended format, `ImageIO` may fail to write the file correctly.

### Fix Code Hint
```java
// Ensure overwrite is allowed if the file might exist
Thumbnails.of(source)
    .size(100, 100)
    .allowOverwrite(true)
    .toFile(new File("output.jpg"));
```

## API Test: `toFiles`

### Signature
```java
public void toFiles(Iterable<File> iterable)
public void toFiles(Rename rename)
public void toFiles(File destinationDir, Rename rename)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2627  (+2 more definition site/overload)_

_Source doc:_ Creates thumbnails and stores them to files in the directory specified by the given {@link File} object, and using the {@link Rename} function to determine the filenames. <p> When the destination file exists, and overwriting files has been disabled by calling the {@link #allowOverwrite(boolean)} method with {@code false}, then the thumbnail with the destination file already existing will not be written. <p> Extra caution should be taken when using this method, as there are no protections in place to prevent file name collisions resulting from creating thumbnails from files in separate directories but having the same name. In such a case, the behavior will be depend on the behavior of the {@link #allowOverwrite(boolean)} as described in the previous paragraph. <p> To call this method, the thumbnails must have been creates from files by calling the {@link Thumbnails#of(File...)} method. @param destinationDir	The destination directory to which the thumbnails should be written to. @param rename			The rename function which is used to determine the filenames of the thumbnail files to write. @throws IOException		If a problem occurs while reading the original images or writing the thumbnails to files. thumbnails to files. @throws IllegalStateException		If the original images are not from files. @throws IllegalArgumentException		If the destination directory is not a directory. @since 	0.4.7

### Goal
`toFiles` performs batch processing of image files, applying the configured thumbnail pipeline and writing the resulting images to the local filesystem using specified naming strategies or explicit file paths.

### Parameters
- `destinationDir` (`File`): The target directory where generated thumbnails will be saved. Must be a valid, existing directory.
- `rename` (`Rename`): A naming strategy (e.g., `Rename.PREFIX_DOT_THUMBNAIL`) used to generate output filenames based on the original source filenames.

### Input
- **Data:** Requires the pipeline to be initialized with `Thumbnails.of(File...)` or `Thumbnails.fromFiles(List<File>)`.
- **Formats:** Supports standard image formats supported by the host JVM's `ImageIO`.
- **Preconditions:** The input must consist of `File` objects. If the pipeline was initialized with `InputStream` or `URL`, `toFiles` will throw an `IllegalStateException`.

### Output
Returns `void` — The method writes image files to the disk. The format of the output file is determined by the pipeline configuration (e.g., `.outputFormat("jpg")`) or inferred from the source if not specified.

### Valid Call Patterns
```java
// Using a Rename strategy
Thumbnails.of(new File("path/to/directory").listFiles())
    .size(640, 480)
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);

// Using an explicit list of destination files
Thumbnails.fromFiles(sources)
    .size(100, 100)
    .toFiles(Arrays.asList(outFile1, outFile2));
```

### LLM Instruction Prompt
- Always verify that the `Thumbnails` builder was initialized with `File` inputs before suggesting `toFiles`.
- If using `Rename`, ensure the constant is valid (e.g., use `Rename.PREFIX_DOT_THUMBNAIL`).
- If the user needs to prevent overwriting existing files, remind them to call `.allowOverwrite(false)` on the builder before invoking `toFiles`.

### Prompt Snippet
```text
// Ensure the builder is initialized with files
Thumbnails.of(file1, file2)
    .size(width, height)
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

### Common Failure Modes
- **`IllegalStateException`**: Occurs if the pipeline was initialized with `InputStream` or `URL` instead of `File`.
- **`IllegalArgumentException`**: Occurs if the provided `destinationDir` is not a directory or does not exist.
- **`IOException`**: Occurs if there are permission issues, disk space limitations, or if the source image is corrupted.
- **File Collisions**: If multiple source files have the same name (from different directories), they may overwrite each other in the destination directory unless `allowOverwrite(false)` is set.

### Fix Code Hint
```java
// If you get an IllegalStateException, ensure you are using Thumbnails.of(File...)
// If you get an IllegalArgumentException, verify the directory exists:
File dir = new File("path/to/dir");
if (!dir.exists()) dir.mkdirs();
builder.toFiles(dir, Rename.PREFIX_DOT_THUMBNAIL);
```

## API Test: `toOutputStream`

### Signature
```java
public void toOutputStream(OutputStream os)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2728_

_Source doc:_ Create a thumbnail and writes it to a {@link OutputStream}. <p> To call this method, the thumbnail must have been created from a single source. <p> Note that the {@link OutputStream#close()} method will not be called upon the completion of the thumbnail being written to the {@link OutputStream}. @param os				The output stream to which the thumbnail is to be written to. @throws IOException		If a problem occurs while reading the original images or writing the thumbnails. @throws IllegalArgumentException		If multiple original image files are	specified. @throws IllegalStateException		If the output format has not been specified through the {@link #outputFormat(String)} method.

### Goal
Writes the processed thumbnail image data directly to a provided `OutputStream` without closing the stream upon completion.

### Parameters
- `os` (`OutputStream`): The destination stream where the encoded thumbnail image bytes will be written.

### Input
- **Data:** A single source image (e.g., `BufferedImage`, `File`, `InputStream`, or `URL`) processed via the `Thumbnails.Builder` pipeline.
- **Preconditions:** The output format must be explicitly defined using the `.outputFormat(String)` method before calling `toOutputStream`. The input must consist of a single source image.

### Output
Returns `void` — The method writes the image data to the provided `OutputStream` in the format specified by the pipeline configuration.

### Valid Call Patterns
```java
// Writing a processed image to an OutputStream
Thumbnails.of(img)
    .size(50, 50)
    .outputFormat("png")
    .toOutputStream(os);
```

### LLM Instruction Prompt
- Always ensure `.outputFormat(String)` is called in the builder chain before `toOutputStream`.
- Do not assume the `OutputStream` will be closed by the library; the caller is responsible for resource management.
- Ensure the pipeline is initialized with a single source image.

### Prompt Snippet
```text
// Ensure output format is set before calling toOutputStream
Thumbnails.of(source)
    .size(width, height)
    .outputFormat("jpg")
    .toOutputStream(outputStream);
```

### Common Failure Modes
- **`IllegalStateException`**: Occurs if the output format is not specified via `.outputFormat(String)`.
- **`IllegalArgumentException`**: Occurs if the builder was initialized with multiple source files or images.
- **`IOException`**: Occurs if there is an error reading the source image or writing to the provided `OutputStream`.

### Fix Code Hint
```java
// Add the missing output format configuration
Thumbnails.of(source)
    .size(100, 100)
    .outputFormat("png") // Required for toOutputStream
    .toOutputStream(os);
```

## API Test: `toOutputStreams`

### Signature
```java
public void toOutputStreams(Iterable<? extends OutputStream> iterable)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2777_

_Source doc:_ Creates the thumbnails and writes them to {@link OutputStream}s provided by the {@link Iterable}. <p> Note that the {@link OutputStream#close()} method will not be called upon the completion of the thumbnail being written to the {@link OutputStream}. @param iterable			An {@link Iterable} which returns an {@link Iterator} which returns the output stream which should be assigned to each thumbnail. @throws IOException		If a problem occurs while reading the original images or writing the thumbnails. @throws IllegalStateException		If the output format has not been specified through the {@link #outputFormat(String)} method.

### Goal
Writes the processed thumbnail image(s) to a collection of provided `OutputStream` objects.

### Parameters
- `iterable` (`Iterable<? extends OutputStream>`): An `Iterable` collection of `OutputStream` instances where the generated thumbnail data will be written.

### Input
- **Data:** The pipeline must contain one or more source images (e.g., `BufferedImage`, `File`, `InputStream`, or `URL`).
- **Preconditions:** The output format must be explicitly defined using the `.outputFormat(String)` method before calling `toOutputStreams`.
- **Formats:** The output format must be a format supported by the host JVM's `ImageIO` (e.g., "png", "jpg").

### Output
Returns `void` — The method performs a side-effect by writing the processed image data to the provided `OutputStream`s. Note that the library does not close the streams after writing.

### Valid Call Patterns
```java
// Example: Writing a single thumbnail to a ByteArrayOutputStream
Thumbnails.of(img)
    .size(50, 50)
    .outputFormat("png")
    .toOutputStreams(Arrays.asList(os));
```

### LLM Instruction Prompt
- Always ensure `.outputFormat(String)` is called in the builder chain before `toOutputStreams`.
- Ensure the input to `toOutputStreams` is an `Iterable` (e.g., `List` or `Set`) containing `OutputStream` objects.
- Do not assume the `OutputStream` will be closed by the library; manage stream lifecycle externally.

### Prompt Snippet
```text
.outputFormat("png").toOutputStreams(Arrays.asList(outputStream))
```

### Common Failure Modes
- **`IllegalStateException`**: Occurs if `.outputFormat(String)` was not called prior to `toOutputStreams`.
- **`IOException`**: Occurs if there is an error reading the source image or writing to the provided `OutputStream`.
- **Stream Lifecycle**: Failing to close the `OutputStream` manually after the operation completes, as the library explicitly leaves this to the caller.

### Fix Code Hint
```java
// Ensure format is set and handle potential exceptions
try {
    Thumbnails.of(source)
        .size(100, 100)
        .outputFormat("jpg")
        .toOutputStreams(Collections.singletonList(outputStream));
} catch (IllegalStateException e) {
    // Handle missing output format configuration
} catch (IOException e) {
    // Handle I/O errors
}
```

## API Test: `typeOf`

### Signature
```java
public static Orientation typeOf(int value)
public static IfdType typeOf(int value)
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/Orientation.java:124  (+1 more definition site/overload)_

_Source doc:_ Returns the {@link Orientation} corresponding to the given orientation value. @param value		The orientation value. @return			{@link Orientation} corresponding to the orientation value. Return {@code null} if the given value does not correspond to a	valid {@link Orientation}.

### Goal
Maps an integer value to its corresponding `Orientation` or `IfdType` enumeration to facilitate EXIF metadata processing and image transformation.

### Parameters
- `value` (`int`): The integer representation of an EXIF orientation or IFD type tag.

### Input
The caller must provide an integer value corresponding to valid EXIF orientation tags (typically 1-8). This is typically extracted from image metadata before being passed to `ExifFilterUtils` for image correction.

### Output
Returns `Orientation` (or `IfdType`): The enumeration constant matching the provided integer. Returns `null` if the integer does not map to a valid defined type.

### Valid Call Patterns
```java
// Using Orientation.typeOf to resolve EXIF orientation for filter application
BufferedImage result = Thumbnails.of(image)
    .addFilter(ExifFilterUtils.getFilterForOrientation(Orientation.typeOf(orientationValue)))
    .size(width, height)
    .asBufferedImage();
```

### LLM Instruction Prompt
- Use `Orientation.typeOf(int)` when processing EXIF metadata to determine the correct rotation or flip filter.
- Always check for `null` returns if the integer value is derived from external, untrusted image metadata.
- Ensure the resulting `Orientation` object is passed to `ExifFilterUtils.getFilterForOrientation()` to generate the appropriate `ImageFilter`.

### Prompt Snippet
```text
Orientation.typeOf(int_value)
```

### Common Failure Modes
- Passing an integer outside the valid range of EXIF orientation tags (1-8), resulting in a `null` return value.
- Attempting to use the returned `null` value in `ExifFilterUtils.getFilterForOrientation()`, which may trigger a `NullPointerException`.

### Fix Code Hint
```java
Orientation orientation = Orientation.typeOf(value);
if (orientation != null) {
    // Proceed with filter application
    builder.addFilter(ExifFilterUtils.getFilterForOrientation(orientation));
}
```

## API Test: `useExifOrientation`

### Signature
```java
public boolean useExifOrientation()
public Builder<T> useExifOrientation(boolean useExifOrientation)
public ThumbnailParameterBuilder useExifOrientation(boolean use)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1700  (+2 more definition site/overload)_

_Source doc:_ Sets whether or not to use the Exif metadata when orienting the thumbnail. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. @param useExifOrientation	{@code true} if the Exif metadata should be used to determine the orientation of the thumbnail, {@code false} otherwise. @return						Reference to this object. @since	0.4.3

### Goal
Configures the thumbnail pipeline to automatically rotate or flip the output image based on the EXIF orientation metadata embedded in the source image.

### Parameters
- `useExifOrientation` (`boolean`): Set to `true` to enable automatic orientation correction based on EXIF data; set to `false` to ignore EXIF orientation tags and process the image as-is.

### Input
- **Data:** Requires image files (e.g., JPEG) that contain valid EXIF metadata.
- **Preconditions:** The input source must be a format supported by the host JVM's `ImageIO` that preserves EXIF tags.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further chaining of pipeline operations.

### Valid Call Patterns
```java
Thumbnails.of(source1, source2)
    .size(100, 100)
    .useExifOrientation(true)
    .toOutputStreams(Arrays.asList(baos1, baos2));
```

### LLM Instruction Prompt
- When constructing a thumbnail pipeline, use `useExifOrientation(true)` if the source images are captured from cameras or mobile devices where orientation tags are common.
- Ensure this method is called exactly once per builder instance to avoid `IllegalStateException`.

### Prompt Snippet
```text
.useExifOrientation(true)
```

### Common Failure Modes
- **`IllegalStateException`**: Occurs if `useExifOrientation` is invoked more than once on the same `Builder` instance.
- **Metadata Absence**: If the source image lacks EXIF orientation metadata, enabling this flag will have no effect on the output orientation.
- **Format Limitations**: If the input format does not support EXIF metadata, the orientation will remain unchanged regardless of the boolean value provided.

### Fix Code Hint
```java
// Ensure the method is called only once in the fluent chain
Thumbnails.of(file)
    .size(100, 100)
    .useExifOrientation(true) // Call once
    .toFile(output);
```

## API Test: `useOriginalFormat`

### Signature
```java
public Builder<T> useOriginalFormat()
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1680_

_Source doc:_ Sets the compression format to use the same format as the original image. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. @return				Reference to this object. @since	0.4.0

### Goal
Configures the thumbnail pipeline to automatically detect and apply the image format of the source input to the generated output.

### Parameters
_None._

### Input
The input must be a valid image source (e.g., `File`, `InputStream`, `URL`, or `BufferedImage`) supported by the host JVM's `ImageIO`. The format is determined dynamically from the source image metadata during the processing pipeline.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further fluent chaining of operations.

### Valid Call Patterns
```java
Thumbnails.of(sourceFile)
    .size(10, 10)
    .useOriginalFormat()
    .toFile(destFile);
```

### LLM Instruction Prompt
- Use `useOriginalFormat()` only once per builder chain.
- Ensure the builder is initialized with a valid source before calling this method.
- Do not call this method if a specific output format has already been explicitly set via `outputFormat()`, as this may lead to configuration conflicts.

### Prompt Snippet
```text
.useOriginalFormat()
```

### Common Failure Modes
- **IllegalStateException**: Occurs if `useOriginalFormat()` is invoked more than once on the same `Builder` instance.
- **Format Mismatch**: If the source format is not supported by the underlying `ImageIO` for writing, the operation may fail during the final output stage.
- **File Extension Discrepancy**: If the destination file path provided to `toFile()` has an extension that does not match the original image's format, the library will still encode the image in the original format, which may result in files with misleading extensions.

### Fix Code Hint
```java
// Ensure only one format-setting method is called
Thumbnails.of(source)
    .size(100, 100)
    .useOriginalFormat() // Correct: called once
    .toFile("output.png");
```

## API Test: `useOriginalImageType`

### Signature
```java
public boolean useOriginalImageType()
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:953_

_Source doc:_ Returns whether or not the original image type should be used for the thumbnail. @return		{@code true} if the original image type should be used, {@code false} otherwise.

### Goal
Determines if the `ThumbnailParameter` configuration is set to preserve the image type of the source image during the thumbnail generation process.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No external data or file formats are required as input for this method, as it purely inspects the internal state of the parameter configuration.

### Output
Returns `boolean` — `true` if the configuration is set to use the original image type (matching `ThumbnailParameter.ORIGINAL_IMAGE_TYPE`), and `false` if a specific image type or the default type is being used.

### Valid Call Patterns
```java
// Inferred from signature:
ThumbnailParameter param = new ThumbnailParameter(...);
boolean isOriginal = param.useOriginalImageType();
```

### LLM Instruction Prompt
- When generating code that inspects `ThumbnailParameter` settings, use `useOriginalImageType()` to verify if the current configuration intends to maintain the source image's color model and bit depth.
- Do not assume the return value is `true` by default; the default is `ThumbnailParameter.DEFAULT_IMAGE_TYPE` (value: 2).

### Prompt Snippet
```text
ThumbnailParameter param = ...;
if (param.useOriginalImageType()) {
    // Logic for preserving original image type
}
```

### Common Failure Modes
- **NullPointerException**: Calling this method on a `null` `ThumbnailParameter` instance.
- **Misinterpretation of Default State**: Assuming the method returns `true` when the parameter has not been explicitly configured to use the original type; the default behavior is to use `ThumbnailParameter.DEFAULT_IMAGE_TYPE`.

### Fix Code Hint
```java
// Ensure the parameter is initialized before checking
if (param != null && param.useOriginalImageType()) {
    // Proceed with original type logic
}
```

## API Test: `value`

### Signature
```java
public int value()
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdType.java:102_

_Source doc:_ Returns the IFD type as a type value. @return	IFD type as a type value.

### Goal
Retrieves the integer representation of an Image File Directory (IFD) type used during EXIF metadata processing.

### Parameters
_None._

### Input
The caller must be operating on an instance of an IFD type enumeration or class that implements this method. No external data or file formats are required as input to this method.

### Output
Returns `int` — The integer value corresponding to the specific IFD type, used internally by Thumbnailator for identifying EXIF metadata structures.

### Valid Call Patterns
```java
// Inferred from signature:
int typeValue = ifdTypeInstance.value();
```

### LLM Instruction Prompt
- Use the `value()` method only when interacting with IFD type objects within the `net.coobird.thumbnailator.util.exif` package.
- Do not attempt to pass arguments to this method, as it takes none.
- Treat the returned integer as an internal identifier for metadata processing; do not assume it maps to standard Java Image I/O constants unless explicitly documented.

### Prompt Snippet
```text
// Example of retrieving the IFD type value
int ifdValue = someIfdType.value();
```

### Common Failure Modes
- **NullPointerException**: Occurs if the method is called on a null reference of an IFD type object.
- **Context Misuse**: Attempting to use the returned integer as a general image format constant (e.g., `BufferedImage` type) will lead to incorrect image processing behavior, as this value is specific to EXIF IFD structures.

### Fix Code Hint
```java
// Ensure the object is initialized before calling value()
if (ifdType != null) {
    int val = ifdType.value();
}
```

## API Test: `watermark`

### Signature
```java
public Builder<T> watermark(Watermark w)
public Builder<T> watermark(BufferedImage image, float opacity)
public Builder<T> watermark(Position position, BufferedImage image, float opacity)
public Builder<T> watermark(Position position, BufferedImage image, float opacity, int insets)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java_

_Source doc:_ Sets the image, opacity, position and insets for the watermark to apply on to the thumbnail. <p> This method can be called multiple times to apply multiple watermarks. <p> If multiple watermarks are to be applied, the watermarks will be applied in the order that this method is called. <p> Calling this method to set this parameter is optional. @param position		The position of the watermark. @param image			The image of the watermark. @param opacity		The opacity of the watermark. <p> The value should be between {@code 0.0f} and {@code 1.0f}, where {@code 0.0f} is completely transparent, and {@code 1.0f} is completely opaque. @param insets		Inset size around the watermark. Cannot be negative. @return				Reference to this object.

### Goal
Applies a visual overlay (watermark) onto the thumbnail image at a specified position with defined transparency and padding.

### Parameters
- `position` (`Position`): Defines the placement of the watermark (e.g., `Positions.CENTER`, `Positions.BOTTOM_RIGHT`).
- `image` (`BufferedImage`): The source image to be used as the watermark overlay.
- `opacity` (`float`): The transparency level of the watermark, ranging from `0.0f` (fully transparent) to `1.0f` (fully opaque).
- `insets` (`int`): The size of the margin in pixels to be applied around the watermark; must be a non-negative integer.

### Input
The caller must provide a valid `BufferedImage` for the watermark. The primary thumbnail image must be initialized via `Thumbnails.of(...)`. All operations are performed in-memory; ensure the JVM has sufficient heap space for the combined image buffers.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for fluent chaining of additional operations.

### Valid Call Patterns
```java
// Standard usage with position, image, and opacity
Thumbnails.of("image.jpg")
    .size(200, 200)
    .watermark(Positions.BOTTOM_RIGHT, watermarkImage, 0.5f)
    .toFile("thumbnail.jpg");

// Chaining multiple watermarks
Thumbnails.of(sourceImage)
    .size(100, 100)
    .watermark(Positions.TOP_LEFT, logo1, 0.8f)
    .watermark(Positions.BOTTOM_RIGHT, logo2, 0.5f)
    .asBufferedImage();
```

### LLM Instruction Prompt
- When using `watermark`, ensure the `opacity` is a `float` between `0.0f` and `1.0f`.
- If `insets` are provided, ensure the value is non-negative.
- Always chain `watermark` after defining the thumbnail size or crop to ensure the watermark is applied to the final dimensions.
- Multiple calls to `watermark` are permitted and will be applied in the order they appear in the pipeline.

### Prompt Snippet
```text
.watermark(Positions.CENTER, watermarkImage, 0.5f)
```

### Common Failure Modes
- **Negative Insets:** Providing a negative integer for `insets` will result in an `IllegalArgumentException`.
- **Null Images:** Passing a `null` `BufferedImage` for the watermark will cause a `NullPointerException`.
- **Opacity Out of Bounds:** Providing an `opacity` value outside the `0.0f` to `1.0f` range may lead to unexpected rendering or exceptions.
- **Memory Exhaustion:** Applying multiple high-resolution watermarks to large images in-memory can exceed JVM heap limits.

### Fix Code Hint
```java
// Ensure opacity is within valid range
float opacity = Math.max(0.0f, Math.min(1.0f, userProvidedOpacity));
builder.watermark(Positions.CENTER, watermarkImage, opacity);
```

## API Test: `width`

### Signature
```java
public Builder<T> width(int width)
public BufferedImageBuilder width(int width)
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:872  (+1 more definition site/overload)_

_Source doc:_ Sets the width of the thumbnail. <p> The thumbnail will have the dimensions constrained by the specified width, and the aspect ratio of the original image will be preserved by the thumbnail. <p> Once this method is called, calling the {@link #size(int, int)} or the {@link #scale(double)} method will result in an {@link IllegalStateException}. <p> Calling this method multiple times will result in an {@link IllegalStateException} to be thrown. @param width			The width of the thumbnail. @return				Reference to this object. @since 	0.3.5

### Goal
Sets the target width for the thumbnail while automatically calculating the height to preserve the original image's aspect ratio.

### Parameters
- `width` (`int`): The desired width of the thumbnail in pixels. Must be a positive integer.

### Input
- **Data:** An image source provided via `Thumbnails.of(...)` (e.g., `File`, `InputStream`, `URL`, or `BufferedImage`).
- **Preconditions:** The input must be a valid image format supported by the host JVM's `ImageIO`. The builder must not have already had `size(int, int)` or `scale(double)` called upon it.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further chaining of operations like `rotate`, `watermark`, or output methods.

### Valid Call Patterns
```java
// Basic usage in a fluent pipeline
Thumbnails.of(sourceImage)
    .width(50)
    .toFile("thumbnail.jpg");
```

### LLM Instruction Prompt
- When using `width`, ensure it is the sole sizing method called in the pipeline.
- Do not chain `width` with `size` or `scale`, as this will trigger an `IllegalStateException`.
- Ensure the input image is already loaded or accessible via the `Thumbnails.of` entry point.

### Prompt Snippet
```text
// Example of setting width in a Thumbnailator pipeline:
Thumbnails.of(imageFile).width(100).toFile(outputFile);
```

### Common Failure Modes
- **IllegalStateException:** Occurs if `width` is called more than once, or if it is called after `size(int, int)` or `scale(double)`.
- **IllegalArgumentException:** Occurs if the provided `width` is zero or negative.
- **UnsupportedFormatException:** Occurs if the input source is not a format recognized by the JVM's `ImageIO`.

### Fix Code Hint
```java
// To fix an IllegalStateException, ensure only one sizing method is used:
Thumbnails.of(img)
    .width(50) // Correct: only one sizing method
    .toFile("output.jpg");
```

## API Test: `write`

### Signature
```java
public void write(BufferedImage img)
public abstract void write(BufferedImage img)
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/SourceSinkThumbnailTask.java:102  (+8 more definition site/overload)_

### Goal
Writes the processed thumbnail image to the configured destination sink.

### Parameters
- `img` (`BufferedImage`): The `BufferedImage` object containing the processed thumbnail data to be written to the output destination.

### Input
- **Data:** A `BufferedImage` instance.
- **Preconditions:** The destination sink (e.g., `File`, `OutputStream`) must be initialized and accessible. The image format must be supported by the host JVM's `ImageIO` capabilities.
- **Format:** The output format is determined by the configuration of the `ThumbnailParameter` or the destination sink's requirements.

### Output
Returns `void` — The method performs a side-effect operation by writing the image data to the destination; no value is returned.

### Valid Call Patterns
```java
// Inferred from API signature:
// Assuming 'task' is an instance of a class implementing the write operation
task.write(thumbnailImage);
```

### LLM Instruction Prompt
- When using `write`, ensure the `BufferedImage` has been fully processed by the Thumbnailator pipeline.
- Always wrap calls to `write` in a `try-catch` block to handle `IOException` and `UnsupportedFormatException`.
- Verify that the destination sink is ready for writing before invoking this method.

### Prompt Snippet
```text
try {
    task.write(myBufferedImage);
} catch (IOException e) {
    // Handle I/O error
}
```

### Common Failure Modes
- **`IOException`**: Thrown when an I/O problem occurs during the write process (e.g., disk full, permission denied, or stream closed).
- **`UnsupportedFormatException`**: Thrown when the target image format is not supported by the underlying Java Image I/O API.
- **Null Pointer**: Occurs if the `BufferedImage` passed to the method is `null`.

### Fix Code Hint
```java
// Ensure the image is not null and handle potential I/O exceptions
if (thumbnail != null) {
    try {
        task.write(thumbnail);
    } catch (IOException e) {
        logger.error("Failed to write thumbnail: " + e.getMessage());
    }
}
```

