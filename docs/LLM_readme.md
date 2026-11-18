# Thumbnailator — LLM_readme

LLM-facing API documentation. Generated from the API surface, project profile, and distilled docs.

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter`

### Signature
```java
(double widthScalingFactor,double heightScalingFactor,Region sourceRegion,boolean keepAspectRatio,String outputFormat,String outputFormatType,float outputQuality,int imageType,List<ImageFilter> filters,Resizer resizer,boolean fitWithinDimensions,boolean useExifOrientation):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:548_

### Goal
Holds the configuration parameters required to execute a thumbnail generation task, defining how a source image is transformed, filtered, and encoded.

### Parameters
- `widthScalingFactor` (`double`): The scaling factor to apply to the width.
- `heightScalingFactor` (`double`): The scaling factor to apply to the height.
- `sourceRegion` (`Region`): The specific area of the source image to process; `null` indicates the entire image.
- `keepAspectRatio` (`boolean`): Whether to maintain the original image's aspect ratio during resizing.
- `outputFormat` (`String`): The compression format (e.g., "jpg"); use `ORIGINAL_FORMAT` or `DETERMINE_FORMAT` as needed.
- `outputFormatType` (`String`): The specific compression type; use `DEFAULT_FORMAT_TYPE` for codec defaults.
- `outputQuality` (`float`): Compression quality from `0.0f` (lowest) to `1.0f` (highest); use `DEFAULT_QUALITY` for codec defaults.
- `imageType` (`int`): The `BufferedImage` type; use `DEFAULT_IMAGE_TYPE` for standard handling.
- `filters` (`List<ImageFilter>`): A list of filters to apply after resizing; `null` indicates no filters.
- `resizer` (`Resizer`): The resizing algorithm implementation to use.
- `fitWithinDimensions` (`boolean`): If `true`, ensures the thumbnail fits within specified bounds if it would otherwise exceed them.
- `useExifOrientation` (`boolean`): Whether to automatically correct image orientation based on Exif metadata.

### Input
The caller must provide valid scaling factors (rational numbers > 0) and a non-null `Resizer`. This class is mutable and not thread-safe; it is intended for single-task configuration.

### Output
Returns `null` — This constructor initializes the parameter object used by `ThumbnailTask` implementations to perform the actual image processing.

### Valid Call Patterns
```java
// Inferred from signature:
new net.coobird.thumbnailator.ThumbnailParameter(
    0.5, 0.5, null, true, "jpg", "JPEG", 0.85f, 
    java.awt.image.BufferedImage.TYPE_INT_RGB, null, 
    net.coobird.thumbnailator.resizers.DefaultResizerFactory.getInstance().getResizer(), 
    true, true
);
```

### LLM Instruction Prompt
- When constructing `ThumbnailParameter`, ensure the `Resizer` is not null.
- Scaling factors must be positive rational numbers.
- Use `null` for `sourceRegion` if no cropping is required.
- Remember that this class is not thread-safe and should not be reused across multiple thumbnail tasks.

### Prompt Snippet
```text
new net.coobird.thumbnailator.ThumbnailParameter(widthScaling, heightScaling, region, keepAspect, format, formatType, quality, imgType, filters, resizer, fit, exif)
```

### Common Failure Modes
- `IllegalArgumentException`: Thrown if the `Resizer` is `null` or if scaling factors are non-positive.
- `IllegalStateException`: Occurs if the parameter object is reused across multiple tasks or if configuration methods are called in an invalid sequence.
- Memory issues: Using `asBufferedImages()` for large batches can lead to `OutOfMemoryError`; prefer `iterableBufferedImages()` or file-based sinks.

### Fix Code Hint
```java
// Ensure resizer is initialized via factory
Resizer resizer = DefaultResizerFactory.getInstance().getResizer();
ThumbnailParameter params = new ThumbnailParameter(0.5, 0.5, null, true, "jpg", "JPEG", 0.85f, BufferedImage.TYPE_INT_RGB, null, resizer, true, true);
```

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter.fitWithinDimenions`

### Signature
```java
():boolean
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:980_

### Goal
Determines whether the thumbnail generation process should constrain the output image dimensions to fit within the specified bounds if the calculated size would otherwise exceed them.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. This method is a getter for the internal state of the parameter configuration; it does not require external data or file formats as input.

### Output
Returns `boolean` — `true` if the thumbnail is configured to be resized to fit within the specified dimensions when it would otherwise exceed them; `false` otherwise.

### Valid Call Patterns
```java
// Inferred from API signature:
ThumbnailParameter param = new ThumbnailParameter(...);
boolean fits = param.fitWithinDimenions();
```

### LLM Instruction Prompt
- Use this method to verify the current constraint configuration of a `ThumbnailParameter` instance.
- Note that this method is part of the `ThumbnailParameter` class, which is mutable and not thread-safe; ensure the instance is fully configured before querying this state.
- The method name contains a typo ("Dimenions" instead of "Dimensions") which must be preserved exactly as defined in the API.

### Prompt Snippet
```text
// Check if the parameter is set to fit within dimensions
if (parameter.fitWithinDimenions()) {
    // Logic for constrained resizing
}
```

### Common Failure Modes
- **Instance Reuse:** Calling this on a `ThumbnailParameter` instance that is being modified by multiple threads, leading to inconsistent state reads.
- **Misinterpretation:** Assuming this method performs the resizing itself; it is a configuration getter, not a transformation method.
- **Typo Errors:** Attempting to call `fitWithinDimensions()` (correct spelling) will result in a compilation error; the API strictly requires `fitWithinDimenions()`.

### Fix Code Hint
```java
// Ensure the correct spelling is used to match the API definition
boolean isConstrained = parameter.fitWithinDimenions();
```

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter.getHeightScalingFactor`

### Signature
```java
():double
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:840_

### Goal
Retrieves the vertical scaling factor configured for a thumbnail generation task.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No specific preconditions are required to call this method, though the returned value depends on whether the task was configured using a scaling factor or a fixed size.

### Output
Returns `double` — The height scaling factor applied to the thumbnail. If the thumbnail size was set using absolute dimensions (e.g., `.size(width, height)`) rather than a scaling factor, this method returns `Double.NaN`.

### Valid Call Patterns
```java
// Inferred from API signature
double heightFactor = thumbnailParameter.getHeightScalingFactor();
```

### LLM Instruction Prompt
- Use `java:net.coobird.thumbnailator.ThumbnailParameter.getHeightScalingFactor` only when inspecting an existing `ThumbnailParameter` instance.
- Always check if the returned value is `Double.NaN` before using it in calculations, as this indicates the parameter is configured for fixed-size output rather than proportional scaling.

### Prompt Snippet
```text
// Example of checking the scaling factor
ThumbnailParameter param = ...;
double hScale = param.getHeightScalingFactor();
if (Double.isNaN(hScale)) {
    // Handle fixed-size configuration
} else {
    // Use hScale for logic
}
```

### Common Failure Modes
- **Misinterpretation of NaN:** Assuming a numeric value is returned when the `ThumbnailParameter` was configured via `.size()` instead of `.scale()`, leading to unexpected `Double.NaN` propagation in downstream math.
- **Thread Safety:** Attempting to call this method on a `ThumbnailParameter` instance that is being concurrently modified by another thread, as `ThumbnailParameter` is not thread-safe.

### Fix Code Hint
```java
// If Double.NaN is returned, verify if the parameter was set via .size()
if (Double.isNaN(param.getHeightScalingFactor())) {
    // Fallback logic for fixed-size tasks
}
```

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter.getImageFilters`

### Signature
```java
():List<ImageFilter>
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:919_

### Goal
Retrieves the list of `ImageFilter` objects currently configured to be applied to the thumbnail after the resizing process is complete.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` instance. No specific input arguments are required for this method. Note that `ThumbnailParameter` is mutable and not thread-safe; ensure the instance is fully configured before retrieving the filter list.

### Output
Returns `List<ImageFilter>` — A list containing the `ImageFilter` implementations (such as `Watermark`, `Caption`, `Rotation`, etc.) that will be applied to the image during the thumbnail generation process.

### Valid Call Patterns
```java
// Inferred from signature:
List<ImageFilter> filters = thumbnailParameter.getImageFilters();
```

### LLM Instruction Prompt
- Use this method to inspect the current transformation pipeline of a `ThumbnailParameter` object.
- Remember that these filters are applied sequentially *after* the image has been resized.
- Do not assume the returned list is modifiable; treat it as a read-only view of the current configuration.

### Prompt Snippet
```text
// Accessing the filter list from a ThumbnailParameter instance
List<ImageFilter> activeFilters = parameter.getImageFilters();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `ThumbnailParameter` instance itself is null.
- **Stale Configuration:** If the `ThumbnailParameter` is reused across multiple tasks, the returned list may contain filters from previous operations, leading to unexpected image transformations. Always use a fresh `ThumbnailParameter` for each task.
- **Thread Safety:** Accessing this method while another thread is modifying the `ThumbnailParameter` (e.g., via `addFilter`) may result in inconsistent state or `ConcurrentModificationException`.

### Fix Code Hint
```java
// Ensure you are working with a fresh parameter instance for each task
ThumbnailParameter param = new ThumbnailParameter(...);
param.addFilter(new Watermark(...));

// Safely retrieve and inspect the filters
List<ImageFilter> filters = param.getImageFilters();
if (filters.isEmpty()) {
    // Handle case with no filters
}
```

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter.getOutputFormat`

### Signature
```java
():String
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:878_

### Goal
Retrieves the configured output format for the thumbnail generation task, indicating whether the format is explicitly set, inherited from the original image, or determined dynamically.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` instance. No specific input arguments are required for this method, as it acts as a getter for the internal state of the parameter object.

### Output
Returns `String` — The output format identifier. It returns `ORIGINAL_FORMAT` if the task is configured to use the source image's compression format, `DETERMINE_FORMAT` if the format is to be inferred from destination metadata (e.g., file extension), or a specific format string (e.g., "jpg", "png").

### Valid Call Patterns
```java
// Inferred from API signature and ThumbnailParameter usage
ThumbnailParameter param = new ThumbnailParameter(...);
String format = param.getOutputFormat();
```

### LLM Instruction Prompt
- When calling `getOutputFormat`, ensure the `ThumbnailParameter` object has been fully configured via the fluent `Thumbnails` builder or direct setter methods.
- Do not assume the returned string is a standard MIME type; it may return internal constants like `ORIGINAL_FORMAT` or `DETERMINE_FORMAT`.
- Use this method only to inspect the current configuration state of a `ThumbnailParameter` instance.

### Prompt Snippet
```text
ThumbnailParameter param = ...;
String currentFormat = param.getOutputFormat();
```

### Common Failure Modes
- **NullPointerException:** Calling this method on a `ThumbnailParameter` instance that has not been properly initialized or is null.
- **Misinterpretation of Constants:** Assuming the return value is always a standard file extension (e.g., "jpg") without checking for the `ORIGINAL_FORMAT` or `DETERMINE_FORMAT` sentinel values.
- **Thread Safety:** Accessing `getOutputFormat` on a `ThumbnailParameter` instance that is being concurrently modified by another thread, as `ThumbnailParameter` is not thread-safe.

### Fix Code Hint
```java
// Check for special constants before using the format string
String format = param.getOutputFormat();
if (format.equals(ThumbnailParameter.ORIGINAL_FORMAT)) {
    // Handle original format logic
} else if (format.equals(ThumbnailParameter.DETERMINE_FORMAT)) {
    // Handle dynamic format detection logic
} else {
    // Use specific format string
}
```

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter.getOutputFormatType`

### Signature
```java
():String
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:891_

### Goal
Retrieves the output format type (e.g., compression type) currently configured for the thumbnail generation task.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No specific preconditions are required to call this method, though the returned value depends on prior configuration of the `ThumbnailParameter` instance.

### Output
Returns `String` — The output format type for the thumbnail. If the default compression type is to be used, this method returns `DEFAULT_FORMAT_TYPE`.

### Valid Call Patterns
```java
// Inferred from API signature; ThumbnailParameter is typically accessed via internal task processing
String formatType = thumbnailParameter.getOutputFormatType();
```

### LLM Instruction Prompt
- Use `java:net.coobird.thumbnailator.ThumbnailParameter.getOutputFormatType` to inspect the compression or format type settings of a `ThumbnailParameter` instance.
- Note that this method returns the constant `DEFAULT_FORMAT_TYPE` if no specific format type has been explicitly set.
- Do not assume this method returns the file extension (e.g., "jpg"); it refers to the format type/compression scheme.

### Prompt Snippet
```text
String formatType = parameter.getOutputFormatType();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `ThumbnailParameter` instance itself is null.
- **Misinterpretation:** Confusing the "output format type" (compression scheme) with the "output format" (file container, e.g., "jpg" or "png"). Use `outputFormat(String)` to set the container format and `getOutputFormatType()` to check the internal compression type.
- **State Dependency:** The value returned is only as meaningful as the configuration applied to the `ThumbnailParameter` object prior to the call.

### Fix Code Hint
```java
// Ensure the parameter object is initialized before checking the format type
if (parameter != null) {
    String type = parameter.getOutputFormatType();
    // Handle DEFAULT_FORMAT_TYPE if necessary
}
```

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter.getOutputQuality`

### Signature
```java
():float
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:907_

### Goal
Retrieves the current compression quality setting configured for a thumbnail generation task.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` instance. No specific preconditions are required to call this method, though the returned value reflects the state set during the configuration of the `ThumbnailParameter` object.

### Output
Returns `float` — A value in the range of `0.0f` (lowest quality) to `1.0f` (highest quality). If no specific quality has been set, the method returns `DEFAULT_QUALITY`.

### Valid Call Patterns
```java
// Inferred from API signature; assumes an existing ThumbnailParameter instance
float quality = thumbnailParameter.getOutputQuality();
```

### LLM Instruction Prompt
- Use `java:net.coobird.thumbnailator.ThumbnailParameter.getOutputQuality` to inspect the compression settings of a `ThumbnailParameter` object.
- Do not assume the returned value is a specific number unless it was explicitly set; check against `ThumbnailParameter.DEFAULT_QUALITY` if necessary.
- Ensure the `ThumbnailParameter` instance is not null before invoking this method.

### Prompt Snippet
```text
ThumbnailParameter param = ...;
float currentQuality = param.getOutputQuality();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `ThumbnailParameter` instance is null.
- **Misinterpretation of Default:** Assuming a specific numeric value (e.g., 0.75) when the parameter has not been explicitly set, rather than checking for `DEFAULT_QUALITY`.
- **Thread Safety:** `ThumbnailParameter` is not thread-safe; calling this method while another thread is modifying the same `ThumbnailParameter` instance may lead to inconsistent results.

### Fix Code Hint
```java
// Ensure the parameter object is initialized
if (param != null) {
    float quality = param.getOutputQuality();
    // Handle logic based on quality
}
```

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter.getResizer`

### Signature
```java
():Resizer
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:930_

### Goal
Retrieves the `Resizer` instance currently configured within the `ThumbnailParameter` to perform the image scaling operation.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No specific preconditions are required to call this method, as it returns the currently set `Resizer` (which defaults to the `DefaultResizerFactory` selection if not explicitly overridden).

### Output
Returns `Resizer` — The specific resizing algorithm implementation (e.g., `Bilinear`, `Bicubic`, `ProgressiveBilinear`) that will be used to process the image during the thumbnail generation task.

### Valid Call Patterns
```java
// Inferred from API signature
ThumbnailParameter param = new ThumbnailParameter(
    new Dimension(200, 200), null, true, "jpg", 0.85f, 0, null, null, null, null, null
);
Resizer resizer = param.getResizer();
```

### LLM Instruction Prompt
- Use `java:net.coobird.thumbnailator.ThumbnailParameter.getResizer` only when you need to inspect the current scaling strategy of a `ThumbnailParameter` instance.
- Do not assume the returned `Resizer` is mutable; it is intended for use in the internal `ThumbnailTask` execution.
- Ensure the `ThumbnailParameter` instance is fully configured before calling this, as the `Resizer` selection may depend on other parameters like dimensions or aspect ratio settings.

### Prompt Snippet
```text
ThumbnailParameter param = ...; // Existing parameter instance
Resizer currentResizer = param.getResizer();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `ThumbnailParameter` instance itself is null.
- **Misinterpretation of Default:** Users may assume a specific `Resizer` is active, but `ThumbnailParameter` defaults to the `DefaultResizerFactory` logic, which dynamically selects the algorithm based on the source and destination dimensions.
- **Thread Safety:** `ThumbnailParameter` is not thread-safe; calling `getResizer` while another thread is modifying the parameter object may lead to inconsistent results.

### Fix Code Hint
```java
// If you need to ensure a specific resizer is used, 
// set it explicitly on the ThumbnailParameter before retrieval:
param.resizer(Resizers.PROGRESSIVE_BILINEAR);
Resizer resizer = param.getResizer();
```

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter.getResizerFactory`

### Signature
```java
():ResizerFactory
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:942_

### Goal
Retrieves the `ResizerFactory` currently configured for a `ThumbnailParameter` instance, which determines the resizing algorithm used during the thumbnail generation process.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No specific preconditions are required to call this getter, though the returned `ResizerFactory` depends on the factory previously set via `setResizerFactory` or the default `DefaultResizerFactory` if none was specified.

### Output
Returns `ResizerFactory` — An implementation of the `ResizerFactory` interface, which is responsible for providing a `Resizer` (e.g., `Bilinear`, `Bicubic`, or `ProgressiveBilinear`) based on the source and destination image dimensions.

### Valid Call Patterns
```java
// Inferred from API signature:
ThumbnailParameter param = new ThumbnailParameter(...);
ResizerFactory factory = param.getResizerFactory();
```

### LLM Instruction Prompt
- Use `java:net.coobird.thumbnailator.ThumbnailParameter.getResizerFactory` only when you need to inspect the current resizing strategy assigned to a `ThumbnailParameter` instance.
- Do not assume the returned factory is a specific implementation; treat it as the `ResizerFactory` interface.
- Remember that `ThumbnailParameter` is not thread-safe; ensure the instance is not being modified by other threads during this call.

### Prompt Snippet
```text
ThumbnailParameter param = ...;
ResizerFactory currentFactory = param.getResizerFactory();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `ThumbnailParameter` instance itself is null.
- **Misinterpretation of Resizer:** Assuming the returned factory is a specific class (like `DefaultResizerFactory`) rather than the `ResizerFactory` interface, which may lead to `ClassCastException` if the factory was customized.
- **State Dependency:** Calling this on a `ThumbnailParameter` that has not been properly initialized or is being reused across multiple tasks, as `ThumbnailParameter` is mutable and not thread-safe.

### Fix Code Hint
```java
// Ensure the parameter object is initialized before accessing the factory
ThumbnailParameter param = new ThumbnailParameter(
    new Dimension(100, 100), null, true, "jpg", 0.85f, 0, 0, null, null, null, null
);
ResizerFactory factory = param.getResizerFactory();
```

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter.getSize`

### Signature
```java
():Dimension
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:808_

### Goal
Retrieves the explicit target dimensions configured for a thumbnail generation task.

### Parameters
_None._

### Input
The caller must ensure the `ThumbnailParameter` instance has been configured with an explicit size (e.g., via `size(int, int)`). If the task is configured using a scaling factor instead of an explicit size, this method will return `null`.

### Output
Returns `Dimension` — The `java.awt.Dimension` object representing the width and height of the thumbnail, or `null` if a scaling factor is currently set for the task.

### Valid Call Patterns
```java
// Inferred from API signature and ThumbnailParameter usage
Dimension dim = thumbnailParameter.getSize();
```

### LLM Instruction Prompt
- When calling `getSize()`, verify that the `ThumbnailParameter` instance is not configured for scaling (e.g., `scale(double)`), as the method returns `null` in that state.
- Use this method only when you need to inspect the fixed dimensions of a task; do not assume a non-null return value without checking.

### Prompt Snippet
```text
ThumbnailParameter param = ...; // Existing parameter instance
Dimension size = param.getSize();
if (size != null) {
    // Process fixed size
}
```

### Common Failure Modes
- **NullPointerException:** Occurs if the code assumes a `Dimension` object is returned when the task was actually configured using a scaling factor.
- **Logic Errors:** Calling this method on a `ThumbnailParameter` that has not yet had its size or scale defined may return unexpected results depending on the internal state of the builder.

### Fix Code Hint
```java
// Always check for null before accessing Dimension fields
Dimension size = param.getSize();
if (size != null) {
    int width = size.width;
    int height = size.height;
} else {
    // Handle the case where scaling factor is used instead of fixed size
}
```

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter.getSourceRegion`

### Signature
```java
():Region
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:967_

### Goal
Retrieves the defined source region configuration from a `ThumbnailParameter` instance, which specifies the rectangular area of the source image to be processed during thumbnail generation.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No specific preconditions are required to call this getter, though the returned `Region` object may be `null` if no specific source region has been configured for the task.

### Output
Returns `Region` — An object representing the rectangular area of the source image to be used. A return value of `null` indicates that the entire source image is intended for use in the thumbnail generation process.

### Valid Call Patterns
```java
// Inferred from API signature; assumes an existing ThumbnailParameter instance
Region region = parameter.getSourceRegion();
```

### LLM Instruction Prompt
- When generating code that inspects a `ThumbnailParameter`, use `getSourceRegion()` to determine if a crop or sub-region operation is active.
- Always check for a `null` return value, as this signifies that the full source image is being processed rather than a specific sub-region.
- Do not attempt to modify the returned `Region` object directly if it is intended to be immutable; `ThumbnailParameter` is mutable, but the state of the `Region` object should be handled according to its specific class definition.

### Prompt Snippet
```text
// Check if a specific source region is defined
Region region = parameter.getSourceRegion();
if (region != null) {
    // Process specific sub-region
} else {
    // Process entire image
}
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `ThumbnailParameter` instance itself is `null` before calling the method.
- **Logic Errors:** Assuming a non-null return value when the default behavior (using the entire image) is represented by `null`.
- **Thread Safety:** Attempting to access `ThumbnailParameter` (and its `getSourceRegion()` result) across multiple threads, as `ThumbnailParameter` is explicitly documented as not thread-safe.

### Fix Code Hint
```java
// Ensure the parameter object is initialized before access
if (parameter != null) {
    Region region = parameter.getSourceRegion();
    // Handle null case where full image is used
}
```

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter.getType`

### Signature
```java
():int
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:850_

### Goal
Retrieves the image type constant associated with the `ThumbnailParameter` configuration, corresponding to the standard `BufferedImage` type constants.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No specific preconditions are required to call this method, though the returned value reflects the configuration state of the parameter object.

### Output
Returns `int` — An integer representing the image type, which corresponds to the constants defined in the `java.awt.image.BufferedImage` class (e.g., `TYPE_INT_RGB`, `TYPE_INT_ARGB`).

### Valid Call Patterns
```java
ThumbnailParameter param = new ThumbnailParameter(
    new Dimension(100, 100), 
    null, 
    true, 
    "jpg", 
    0.85f, 
    BufferedImage.TYPE_INT_RGB, 
    null, 
    null
);
int type = param.getType();
```
*(Note: This call pattern is inferred from the API signature and standard usage of the `ThumbnailParameter` class.)*

### LLM Instruction Prompt
- Use `getType()` only when inspecting the configuration of an existing `ThumbnailParameter` instance.
- Do not assume the returned integer is a custom Thumbnailator type; treat it as a standard `java.awt.image.BufferedImage` type constant.
- Ensure the `ThumbnailParameter` instance is not null before invoking this method.

### Prompt Snippet
```text
int imageType = thumbnailParameter.getType();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `ThumbnailParameter` instance has not been initialized.
- **Misinterpretation of Value:** The returned integer is a `BufferedImage` constant; comparing it against non-`BufferedImage` type constants will lead to logic errors.
- **Thread Safety:** `ThumbnailParameter` is not thread-safe; calling `getType()` while another thread is modifying the parameter object may result in inconsistent state.

### Fix Code Hint
```java
// Ensure the parameter object is properly initialized before access
if (param != null) {
    int type = param.getType();
    // Use the type constant for BufferedImage operations
}
```

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter.getWidthScalingFactor`

### Signature
```java
():double
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:826_

### Goal
Retrieves the horizontal scaling factor configured for a thumbnail generation task.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. This method is intended for use when the thumbnail task is configured via scaling factors rather than fixed dimensions.

### Output
Returns `double` — The width scaling factor. Returns `Double.NaN` if the thumbnail size is defined by fixed dimensions rather than a scaling factor.

### Valid Call Patterns
```java
// Inferred from API signature; assumes an existing ThumbnailParameter instance
double widthFactor = parameter.getWidthScalingFactor();
```

### LLM Instruction Prompt
- Use this method only when inspecting a `ThumbnailParameter` instance to determine if a scaling-based transformation is active.
- Always check if the returned value is `Double.NaN` before using it in calculations, as this indicates the parameter is configured for fixed-size resizing instead.

### Prompt Snippet
```text
// Check if scaling is active
double scale = param.getWidthScalingFactor();
if (!Double.isNaN(scale)) {
    // Proceed with scaling logic
}
```

### Common Failure Modes
- **`IllegalStateException`**: While not thrown by this getter, the underlying `ThumbnailParameter` may be in an invalid state if accessed before configuration.
- **Logic Errors**: Treating `Double.NaN` as a valid scaling factor, which will cause downstream arithmetic errors or incorrect image dimensions.
- **Misinterpretation**: Assuming this returns a value when the `ThumbnailParameter` was configured using `.size(width, height)` instead of `.scale(factor)`.

### Fix Code Hint
```java
// Always validate the return value
double factor = param.getWidthScalingFactor();
if (Double.isNaN(factor)) {
    throw new IllegalStateException("ThumbnailParameter is configured for fixed size, not scaling.");
}
```

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter.isKeepAspectRatio`

### Signature
```java
():boolean
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:861_

### Goal
Determines whether the current `ThumbnailParameter` configuration is set to maintain the original image's aspect ratio during the thumbnail generation process.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No specific input arguments are required for this method.

### Output
Returns `boolean` — `true` if the thumbnail generation task is configured to preserve the aspect ratio of the source image, `false` otherwise.

### Valid Call Patterns
```java
// Inferred from API signature; ThumbnailParameter is typically accessed via the fluent API or task configuration
boolean keepRatio = thumbnailParameter.isKeepAspectRatio();
```

### LLM Instruction Prompt
- Use `isKeepAspectRatio()` to verify the aspect ratio constraint of a `ThumbnailParameter` instance before executing a `ThumbnailTask`.
- Do not assume the return value; always query the instance directly as `ThumbnailParameter` is mutable and state may change during the builder lifecycle.

### Prompt Snippet
```text
// Check if aspect ratio preservation is enabled
if (param.isKeepAspectRatio()) {
    // Proceed with aspect-ratio-aware logic
}
```

### Common Failure Modes
- **NullPointerException:** Calling this method on a `null` `ThumbnailParameter` reference.
- **Stale State:** Relying on a cached boolean value after the `ThumbnailParameter` has been modified by other configuration methods (e.g., `.keepAspectRatio(boolean)`), as `ThumbnailParameter` is mutable and not thread-safe.

### Fix Code Hint
```java
// Ensure the parameter instance is fully configured before checking the state
ThumbnailParameter param = new ThumbnailParameterBuilder().keepAspectRatio(true).build();
if (param.isKeepAspectRatio()) {
    // Logic for maintaining aspect ratio
}
```

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter.useExifOrientation`

### Signature
```java
():boolean
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:993_

### Goal
Determines whether the thumbnail generation process should automatically detect and apply orientation corrections based on the source image's Exif metadata.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` object. No specific input data is required for this getter method, but the configuration state of the `ThumbnailParameter` instance determines the return value.

### Output
Returns `boolean` — `true` if the library is configured to use Exif metadata to decide the orientation of the thumbnail, `false` otherwise.

### Valid Call Patterns
```java
// Inferred from signature:
ThumbnailParameter param = new ThumbnailParameter(...);
boolean isExifEnabled = param.useExifOrientation();
```

### LLM Instruction Prompt
- Use this method to verify the current orientation-handling configuration of a `ThumbnailParameter` instance.
- Note that this is a getter; it does not modify the parameter state.
- Ensure the `ThumbnailParameter` instance is not null before invoking this method.

### Prompt Snippet
```text
// Check if Exif orientation is enabled
if (parameter.useExifOrientation()) {
    // Logic for handling Exif-aware processing
}
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `ThumbnailParameter` instance is null.
- **Misinterpretation:** Assuming this method modifies the state; it is a read-only accessor. To change the behavior, use the corresponding setter (if available in the `ThumbnailParameter` API).
- **Contextual Ignorance:** The method only reports the configuration; it does not guarantee that the source image actually contains valid Exif data.

### Fix Code Hint
```java
// Ensure the parameter object is initialized before checking
if (param != null) {
    boolean enabled = param.useExifOrientation();
}
```

## API Test: `java:net.coobird.thumbnailator.ThumbnailParameter.useOriginalImageType`

### Signature
```java
():boolean
```
_Source: source/src/main/java/net/coobird/thumbnailator/ThumbnailParameter.java:953_

### Goal
Determines whether the thumbnail generation process should preserve the image type (e.g., color model and bit depth) of the source image.

### Parameters
_None._

### Input
The caller must have an initialized `ThumbnailParameter` instance. No specific preconditions are required to query this state, though the parameter object should be configured for a specific task to yield meaningful results.

### Output
Returns `boolean` — `true` if the thumbnail is configured to use the original image type, `false` if it is configured to use a default or alternative image type.

### Valid Call Patterns
```java
// Inferred from API signature
ThumbnailParameter param = new ThumbnailParameter(null, null, null, null, null, null, null, false);
boolean useOriginal = param.useOriginalImageType();
```

### LLM Instruction Prompt
- Use this method to check the current configuration of a `ThumbnailParameter` object regarding image type preservation.
- Do not attempt to set the image type using this method; it is a getter only.
- Ensure the `ThumbnailParameter` instance is not null before invoking this method to avoid `NullPointerException`.

### Prompt Snippet
```text
ThumbnailParameter param = ...;
boolean isOriginalType = param.useOriginalImageType();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `ThumbnailParameter` instance has not been initialized.
- **Misinterpretation:** Assuming this method modifies the image type; it only reports the current configuration state.
- **Contextual Irrelevance:** Calling this on a default-constructed `ThumbnailParameter` may return a default value that does not reflect the actual processing behavior if the parameter object is subsequently modified or replaced.

### Fix Code Hint
```java
// Ensure the parameter object is properly initialized before checking
if (param != null) {
    boolean useOriginal = param.useOriginalImageType();
    // Proceed with logic based on the boolean result
}
```

## API Test: `java:net.coobird.thumbnailator.Thumbnailator.createThumbnail`

### Signature
```java
(InputStream is,OutputStream os,String format,int width,int height):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnailator.java:356_

### Goal
Generates a thumbnail from image data provided via an `InputStream` and writes the result to an `OutputStream` in a specified image format.

### Parameters
- `is` (`InputStream`): The source stream containing the original image data.
- `os` (`OutputStream`): The destination stream where the processed thumbnail data will be written.
- `format` (`String`): The image format (e.g., "jpg", "png") to use for the output thumbnail.
- `width` (`int`): The target width of the thumbnail.
- `height` (`int`): The target height of the thumbnail.

### Input
The caller must provide a valid `InputStream` containing image data supported by the Java Image I/O API. The `format` string must correspond to a format supported by the current JRE's Image I/O writers (verify via `ThumbnailatorUtils.getSupportedOutputFormats()`). The method assumes the input stream is ready to be read and will not be closed by this method.

### Output
Returns `void` — The method performs the transformation and writes the resulting thumbnail directly to the provided `OutputStream`.

### Valid Call Patterns
```java
net.coobird.thumbnailator.Thumbnailator.createThumbnail(inputStream, outputStream, "jpg", 200, 200);
```
*(Note: This example is inferred from the signature as no direct test-suite example was provided.)*

### LLM Instruction Prompt
- Always ensure the `format` string is a valid, supported output format for the environment.
- Ensure the `InputStream` is initialized and contains valid image data before passing it to the method.
- Wrap the call in a `try-catch` block to handle `IOException` and `IllegalArgumentException`.

### Prompt Snippet
```text
net.coobird.thumbnailator.Thumbnailator.createThumbnail(is, os, "png", 100, 100);
```

### Common Failure Modes
- `IOException`: Occurs if there is an error reading from the `InputStream` or writing to the `OutputStream`.
- `IllegalArgumentException`: Occurs if the specified `format` is not supported by the underlying Java Image I/O implementation.
- `OutOfMemoryError`: May occur when processing very large images; consider using memory-efficient alternatives if processing large batches.

### Fix Code Hint
```java
try {
    net.coobird.thumbnailator.Thumbnailator.createThumbnail(is, os, "jpg", 200, 200);
} catch (IOException e) {
    // Handle I/O errors
} catch (IllegalArgumentException e) {
    // Handle unsupported format errors
}
```

## API Test: `java:net.coobird.thumbnailator.Thumbnailator.createThumbnails`

### Signature
```java
(Collection<? extends File> files,Rename rename,int width,int height):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnailator.java:453_

### Goal
Generates thumbnails for a collection of image files by resizing them to the specified dimensions and applying a naming strategy to the output files.

### Parameters
- `files` (`Collection<? extends File>`): A collection of `File` objects representing the source images to be processed.
- `rename` (`Rename`): A `Rename` strategy (e.g., `Rename.PREFIX_DOT_THUMBNAIL`) used to determine the filenames of the generated thumbnails.
- `width` (`int`): The target width of the thumbnail in pixels.
- `height` (`int`): The target height of the thumbnail in pixels.

### Input
- **Data:** A `Collection` of `File` objects.
- **Formats:** Supports any image format recognized by the Java Image I/O API.
- **Preconditions:** The files must exist and be readable. Note that this method is **deprecated**; it is recommended to use the `Thumbnails.fromFiles(Iterable)` fluent interface instead.

### Output
Returns `void` — The method performs file I/O operations directly, writing the generated thumbnails to the filesystem based on the provided `Rename` strategy.

### Valid Call Patterns
```java
import net.coobird.thumbnailator.Thumbnailator;
import net.coobird.thumbnailator.name.Rename;
import java.io.File;
import java.util.Arrays;
import java.util.List;

List<File> files = Arrays.asList(new File("image1.jpg"), new File("image2.jpg"));
Thumbnailator.createThumbnails(files, Rename.PREFIX_DOT_THUMBNAIL, 200, 200);
```

### LLM Instruction Prompt
- Use this method only for legacy codebases; prefer the `Thumbnails.of(...)` fluent API for new development.
- Ensure the `Rename` strategy is correctly imported from `net.coobird.thumbnailator.name.Rename`.
- Be aware that this method will be removed in version 0.5.0.

### Prompt Snippet
```text
Thumbnailator.createThumbnails(files, Rename.PREFIX_DOT_THUMBNAIL, width, height);
```

### Common Failure Modes
- **`IOException`:** Thrown if there is an error reading a source file or writing the output file.
- **`IllegalArgumentException`:** May occur if the provided dimensions are non-positive.
- **Deprecation Warning:** The method is deprecated and will be removed in 0.5.0; usage triggers compiler warnings.
- **Memory Issues:** Processing a very large `Collection` of files may lead to `OutOfMemoryError` if not handled carefully; consider the fluent `iterableBufferedImages()` approach for large batches.

### Fix Code Hint
```java
// Replace deprecated call with the recommended fluent API:
Thumbnails.fromFiles(files)
    .size(width, height)
    .toFiles(rename);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnailator.createThumbnailsAsCollection`

### Signature
```java
(Collection<? extends File> files,Rename rename,int width,int height):Collection<File>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnailator.java:400_

### Goal
Generates a collection of thumbnail files from a provided collection of source image files by applying a specified renaming strategy and fixed dimensions.

### Parameters
- `files` (`Collection<? extends File>`): A collection of `File` objects representing the source images to be processed.
- `rename` (`Rename`): A `Rename` strategy (e.g., `Rename.PREFIX_DOT_THUMBNAIL`) used to determine the output filename for each generated thumbnail.
- `width` (`int`): The target width of the thumbnail in pixels.
- `height` (`int`): The target height of the thumbnail in pixels.

### Input
- **Data:** A `Collection` of `File` objects pointing to valid image files.
- **Formats:** Supports any image format recognized by the Java Image I/O API.
- **Preconditions:** The caller must ensure the files exist and are readable. Note that this method is **deprecated**; it is recommended to use the `Thumbnails.fromFiles(Iterable)` fluent interface instead.

### Output
Returns `Collection<File>` — A collection of `File` objects pointing to the newly created thumbnails, maintaining the same order as the input collection.

### Valid Call Patterns
```java
// Inferred from signature and project documentation
Collection<File> sourceFiles = Arrays.asList(new File("img1.jpg"), new File("img2.jpg"));
Collection<File> thumbnails = net.coobird.thumbnailator.Thumbnailator.createThumbnailsAsCollection(
    sourceFiles, 
    Rename.PREFIX_DOT_THUMBNAIL, 
    200, 
    200
);
```

### LLM Instruction Prompt
- Use this method only for legacy codebases; prefer the `Thumbnails.of(...)` fluent API for new development.
- Ensure the `Rename` strategy is provided to avoid filename collisions.
- The method will throw an `IOException` if any file in the collection cannot be read or processed.

### Prompt Snippet
```text
net.coobird.thumbnailator.Thumbnailator.createThumbnailsAsCollection(files, rename, width, height)
```

### Common Failure Modes
- **Deprecated Status:** This method is marked for removal in version 0.5.0; usage will trigger compiler warnings.
- **IOException:** Occurs if the input files are inaccessible, corrupted, or if the output directory is not writable.
- **Memory Constraints:** Processing a very large `Collection` of files may lead to `OutOfMemoryError` if not handled carefully; consider the `iterableBufferedImages()` approach for large batches.

### Fix Code Hint
```java
// Recommended replacement for the deprecated method:
Thumbnails.fromFiles(files)
    .size(width, height)
    .toFiles(rename);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.addFilter`

### Signature
```java
(ImageFilter filter):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1993_

### Goal
Appends an `ImageFilter` to the thumbnail processing pipeline to perform custom image transformations such as watermarking, captioning, or color manipulation.

### Parameters
- `filter` (`ImageFilter`): An implementation of the `ImageFilter` interface (e.g., `Watermark`, `Caption`, `Rotation`, `Flip`, `Colorize`, or `Transparency`) that defines the specific image processing operation to be applied.

### Input
The caller must provide an object implementing `net.coobird.thumbnailator.filters.ImageFilter`. This method can be invoked multiple times; filters are applied sequentially in the order they are added to the builder. Preconditions include ensuring the `Thumbnails.Builder` has been initialized via `Thumbnails.of(...)` or equivalent entry points.

### Output
Returns `Builder<T>` — A reference to the current `Builder` instance, allowing for further method chaining in the fluent API.

### Valid Call Patterns
```java
Thumbnails.of("image.jpg")
    .size(200, 200)
    .addFilter(new Caption("My Caption", Font.decode("Arial"), Color.WHITE, Positions.BOTTOM_RIGHT, 10))
    .toFile("output.jpg");
```

### LLM Instruction Prompt
- When using `addFilter`, ensure the `ImageFilter` is instantiated with all required parameters (e.g., fonts, colors, or positions).
- Chain this method after defining the source and size/scale, but before the terminal output method (e.g., `toFile`, `asBufferedImage`).
- Remember that filters are applied in the order of addition; if multiple filters are used, order them logically (e.g., rotate before watermarking).

### Prompt Snippet
```text
// Example of adding a filter to a Thumbnailator pipeline
.addFilter(new net.coobird.thumbnailator.filters.Caption("Text", font, color, position, inset))
```

### Common Failure Modes
- **NullPointerException:** Passing a `null` filter object to the method.
- **Ordering Issues:** Applying filters in an incorrect sequence (e.g., applying a watermark before a rotation, which may result in the watermark being rotated unexpectedly).
- **Incompatible Filters:** Attempting to use custom filters that do not correctly implement the `ImageFilter` interface or that conflict with the internal `BufferedImage` type being processed.

### Fix Code Hint
```java
// Ensure the filter is not null and is added in the correct sequence
if (myFilter != null) {
    builder.addFilter(myFilter);
}
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.addFilters`

### Signature
```java
(List<ImageFilter> filters):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2016_

### Goal
Appends a collection of `ImageFilter` objects to the thumbnail processing pipeline to perform sequential image transformations such as watermarking, rotation, or color adjustment.

### Parameters
- `filters` (`List<ImageFilter>`): A list of objects implementing the `ImageFilter` interface, which will be applied to the image in the order they appear in the list.

### Input
- **Data:** A `List` containing one or more `ImageFilter` implementations (e.g., `Watermark`, `Caption`, `Rotation`, `Flip`, `Colorize`, `Transparency`).
- **Preconditions:** The builder must be initialized via `Thumbnails.of(...)`. Filters are applied after the primary resizing operation defined in the fluent pipeline.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further method chaining in the fluent pipeline.

### Valid Call Patterns
```java
List<ImageFilter> myFilters = new ArrayList<>();
myFilters.add(new Watermark(Positions.BOTTOM_RIGHT, ImageIO.read(new File("logo.png")), 0.5f));
myFilters.add(new Rotation(90));

Thumbnails.of("input.jpg")
    .size(200, 200)
    .addFilters(myFilters)
    .toFile("output.jpg");
```

### LLM Instruction Prompt
- When using `addFilters`, ensure the `List<ImageFilter>` is instantiated and populated before passing it to the builder.
- Remember that filters are applied in the order they are added to the list; if `addFilters` is called multiple times, subsequent lists are appended to the existing filter chain.
- Ensure all `ImageFilter` objects are compatible with the `BufferedImage` type being processed.

### Prompt Snippet
```text
// Example of adding multiple filters via a list
List<ImageFilter> filters = new ArrayList<>();
filters.add(new Rotation(180));
filters.add(new Flip(Flip.HORIZONTAL));
builder.addFilters(filters);
```

### Common Failure Modes
- **NullPointerException:** Passing a `null` list or a list containing `null` elements to the method.
- **Ordering Issues:** Expecting a filter to be applied before resizing; filters added via `addFilters` are applied to the thumbnail *after* the resizing logic has completed.
- **Type Mismatch:** Attempting to add objects that do not implement the `ImageFilter` interface.

### Fix Code Hint
```java
// Ensure the list is not null and contains valid ImageFilter instances
List<ImageFilter> filters = Arrays.asList(filter1, filter2);
if (filters != null) {
    builder.addFilters(filters);
}
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.allowOverwrite`

### Signature
```java
(boolean allowOverwrite):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1265_

### Goal
Configures the thumbnail generation pipeline to determine whether existing destination files should be overwritten during the output process.

### Parameters
- `allowOverwrite` (`boolean`): If `true`, existing files at the destination path will be overwritten. If `false`, existing files will not be altered.

### Input
- **Preconditions:** This method must be called on a `Thumbnails.Builder` instance before the terminal output method (e.g., `toFile`, `toFiles`) is invoked.
- **Scope:** This setting only affects file-based output methods: `toFile(File)`, `toFile(String)`, `toFiles(Iterable)`, `toFiles(Rename)`, `asFiles(Iterable)`, and `asFiles(Rename)`.

### Output
Returns `Builder<T>` — A reference to the current `Builder` instance, allowing for further method chaining in the fluent pipeline.

### Valid Call Patterns
```java
Thumbnails.of("input.jpg")
    .size(200, 200)
    .allowOverwrite(true)
    .toFile("output.jpg");
```

### LLM Instruction Prompt
- When generating code, ensure `allowOverwrite` is called only once per builder instance to avoid `IllegalStateException`.
- Only apply this method when the pipeline terminates in a file-based output operation.
- Do not use this method if the output destination is a `BufferedImage` or `OutputStream`, as it has no effect on those targets.

### Prompt Snippet
```text
// Example of enabling overwrite for a batch file operation
Thumbnails.of(files)
    .size(100, 100)
    .allowOverwrite(true)
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

### Common Failure Modes
- **IllegalStateException:** Thrown if `allowOverwrite` is called more than once on the same builder instance.
- **Logic Errors:** Expecting this method to affect `asBufferedImage()` or `toOutputStream()` operations; it is ignored for non-file destinations.
- **Pipeline Ordering:** Attempting to call this after the terminal output method has already been invoked (the builder is typically consumed by the terminal method).

### Fix Code Hint
```java
// Ensure only one call to allowOverwrite is present in the chain
Thumbnails.of(source)
    .size(50, 50)
    .allowOverwrite(false) // Set once
    .toFile(destination);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.alphaInterpolation`

### Signature
```java
(AlphaInterpolation config):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1379_

### Goal
Sets the alpha interpolation rendering hint used by the underlying Java 2D graphics engine during the thumbnail resizing operation.

### Parameters
- `config` (`AlphaInterpolation`): The alpha interpolation mode to apply (e.g., `AlphaInterpolation.DEFAULT`, `AlphaInterpolation.QUALITY`, or `AlphaInterpolation.SPEED`).

### Input
The caller must provide an `AlphaInterpolation` configuration object. This method is an optional configuration step in the fluent `Thumbnails.Builder` pipeline. It must be called before the terminal operation (e.g., `toFile`, `asBufferedImage`).

### Output
Returns `Builder<T>` — The current `Builder` instance, allowing for further method chaining in the fluent pipeline.

### Valid Call Patterns
```java
// Inferred from API documentation
Thumbnails.of("image.jpg")
    .size(200, 200)
    .alphaInterpolation(AlphaInterpolation.QUALITY)
    .toFile("thumbnail.jpg");
```

### LLM Instruction Prompt
- When calling `alphaInterpolation`, ensure it is part of a fluent chain initiated by `Thumbnails.of(...)`.
- Do not call this method more than once on the same builder instance.
- Do not use this method in combination with `resizerFactory(ResizerFactory)`, as they are mutually exclusive.

### Prompt Snippet
```text
.alphaInterpolation(AlphaInterpolation.QUALITY)
```

### Common Failure Modes
- **IllegalStateException**: Thrown if `alphaInterpolation` is called more than once on the same builder instance.
- **IllegalStateException**: Thrown if this method is called in conjunction with `resizerFactory(ResizerFactory)`.
- **NullPointerException**: Thrown if the `config` argument passed is `null`.

### Fix Code Hint
```java
// Ensure only one interpolation setting is applied and avoid resizerFactory
Thumbnails.of(source)
    .size(100, 100)
    .alphaInterpolation(AlphaInterpolation.SPEED)
    .toFile(destination);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.antialiasing`

### Signature
```java
(Antialiasing config):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1425_

### Goal
Sets the antialiasing mode to be applied during the image resizing operation to improve the visual quality of the generated thumbnail.

### Parameters
- `config` (`Antialiasing`): The antialiasing configuration to apply, typically selected from the `Antialiasing` enum (e.g., `Antialiasing.ON` or `Antialiasing.OFF`).

### Input
The caller must provide an `Antialiasing` configuration object. This method is an optional configuration step in the fluent `Thumbnails.Builder` pipeline. It must be called before the terminal output method (e.g., `toFile()`, `asBufferedImage()`).

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further method chaining.

### Valid Call Patterns
```java
Thumbnails.of("input.jpg")
    .size(200, 200)
    .antialiasing(Antialiasing.ON)
    .toFile("output.jpg");
```

### LLM Instruction Prompt
- When generating code, ensure `antialiasing` is called only once per builder instance.
- Do not use `antialiasing` in combination with `resizerFactory(ResizerFactory)`, as this will cause an error.
- Ensure the `Antialiasing` enum is imported from `net.coobird.thumbnailator.resizers.configurations.Antialiasing`.

### Prompt Snippet
```text
// Example of applying antialiasing in a Thumbnailator pipeline
Thumbnails.of(sourceFile)
    .size(width, height)
    .antialiasing(Antialiasing.ON)
    .toFile(destinationFile);
```

### Common Failure Modes
- **IllegalStateException**: Occurs if `antialiasing` is called more than once on the same builder instance.
- **IllegalStateException**: Occurs if `antialiasing` is called in conjunction with `resizerFactory(ResizerFactory)`.
- **NullPointerException**: Occurs if the `config` argument passed is `null`.

### Fix Code Hint
```java
// Ensure only one configuration method is used for antialiasing
// and avoid mixing with custom ResizerFactory.
Thumbnails.of(file)
    .size(100, 100)
    .antialiasing(Antialiasing.ON) // Correct
    .toFile(output);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.asBufferedImage`

### Signature
```java
():BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2342_

### Goal
Generates a thumbnail from a single source image and returns the result as an in-memory `BufferedImage` object.

### Parameters
_None._

### Input
The caller must provide a single source image (e.g., `File`, `InputStream`, `URL`, or `BufferedImage`) via the `Thumbnails.of()` or `Thumbnails.from...()` entry points. The builder must be configured with a valid transformation (e.g., `.size()`, `.scale()`) before calling this method.

### Output
Returns `BufferedImage` — The processed thumbnail image. The image type typically matches the input image type unless explicitly overridden via `imageType(int)`.

### Valid Call Patterns
```java
BufferedImage thumbnail = Thumbnails.of("image.jpg")
    .size(200, 200)
    .asBufferedImage();
```

### LLM Instruction Prompt
- Use `asBufferedImage()` only when the builder is configured with a single source image.
- Ensure the builder has a defined transformation (size or scale) before invocation.
- Do not use this method if the intent is to write the result directly to a file; use `toFile()` instead to avoid potential color issues with the default JPEG writer when handling alpha channels.

### Prompt Snippet
```text
// Example of generating a thumbnail in-memory
BufferedImage result = Thumbnails.of(sourceFile)
    .size(100, 100)
    .asBufferedImage();
```

### Common Failure Modes
- **`IllegalArgumentException`**: Thrown if the builder was initialized with multiple source images (e.g., a directory of files).
- **`IOException`**: Thrown if an error occurs during the reading of the original image.
- **`IllegalStateException`**: Thrown if both `size()` and `scale()` are called on the same builder instance.
- **Color/Alpha Issues**: Using the returned `BufferedImage` with the default Java JPEG writer may result in incorrect colors or exceptions if the image contains an alpha channel.

### Fix Code Hint
```java
// If writing to a file, prefer toFile() to handle color/alpha workarounds
Thumbnails.of("input.png")
    .size(100, 100)
    .toFile("output.png");
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.asBufferedImages`

### Signature
```java
():List<BufferedImage>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2277_

### Goal
Generates thumbnails from the configured input sources and returns them as a `List` of `BufferedImage` objects.

### Parameters
_None._

### Input
The builder must be initialized with one or more image sources (e.g., `File`, `InputStream`, `URL`, or `BufferedImage`) via `Thumbnails.of(...)` or `Thumbnails.from...(...)`. Configuration methods such as `.size()`, `.scale()`, or `.addFilter()` must be called prior to this method to define the transformation pipeline.

### Output
Returns `List<BufferedImage>` — A list containing the processed thumbnail images. Each `BufferedImage` retains the image type of the input unless explicitly overridden via `imageType(int)`.

### Valid Call Patterns
```java
List<BufferedImage> thumbnails = Thumbnails.of("image1.jpg", "image2.jpg")
    .size(100, 100)
    .asBufferedImages();
```

### LLM Instruction Prompt
- Use this method only when you require the resulting images in-memory as `BufferedImage` objects.
- Do not use this method if you intend to write the results directly to files; use `toFiles()` or `asFiles()` instead to benefit from built-in workarounds for JPEG alpha-channel issues.
- Ensure the builder is fully configured (e.g., size or scale is set) before calling this method.

### Prompt Snippet
```text
// Example of generating thumbnails in-memory
List<BufferedImage> results = Thumbnails.of(sourceFiles)
    .size(200, 200)
    .asBufferedImages();
```

### Common Failure Modes
- **OutOfMemoryError:** This method processes all inputs and stores all resulting `BufferedImage` objects in memory simultaneously. For large batches, use `iterableBufferedImages()` instead.
- **Color/JPEG Issues:** If the input image contains an alpha channel, writing the resulting `BufferedImage` to a JPEG file using standard Java `ImageIO` may result in incorrect colors or exceptions. Use `toFiles()` for file-based output to avoid this.
- **IllegalStateException:** Thrown if both `size()` and `scale()` are called on the same builder instance.

### Fix Code Hint
```java
// If memory is an issue, use the iterable approach:
for (BufferedImage thumbnail : Thumbnails.of(files).size(100, 100).iterableBufferedImages()) {
    // Process each thumbnail individually
}

// If writing to files, avoid asBufferedImages() and use:
Thumbnails.of(files).size(100, 100).toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.asFiles`

### Signature
```java
(File destinationDir,Rename rename):List<File>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2519_

### Goal
Generates thumbnails from a collection of source files and saves them into a specified directory using a defined naming strategy.

### Parameters
- `destinationDir` (`File`): The target directory where the generated thumbnail files will be saved.
- `rename` (`Rename`): A naming strategy (e.g., `Rename.PREFIX_DOT_THUMBNAIL`) used to determine the filename of each output thumbnail based on the original source filename.

### Input
- **Data:** Requires a `Thumbnails.Builder` initialized with source files (e.g., via `Thumbnails.of(File...)`).
- **Preconditions:** The source images must originate from files. The `destinationDir` must exist and be a valid directory.
- **Constraints:** If `allowOverwrite(false)` is set, existing files in the destination directory will not be overwritten, and those files will be excluded from the returned list.

### Output
Returns `List<File>` — A list containing the `File` objects of the successfully created thumbnails.

### Valid Call Patterns
```java
Thumbnails.of(new File("path/to/sourceDir").listFiles())
    .size(200, 200)
    .asFiles(new File("path/to/outputDir"), Rename.PREFIX_DOT_THUMBNAIL);
```

### LLM Instruction Prompt
- When using `asFiles(File, Rename)`, ensure the builder was initialized with `Thumbnails.of(File...)`.
- Always verify that the `destinationDir` is a directory before passing it to the method.
- Use `Rename` constants to define output naming conventions.

### Prompt Snippet
```text
// Example of batch processing with a specific destination directory
List<File> thumbnails = Thumbnails.of(files)
    .size(100, 100)
    .asFiles(new File("output/dir"), Rename.PREFIX_DOT_THUMBNAIL);
```

### Common Failure Modes
- `IllegalStateException`: Thrown if the builder was not initialized with file sources.
- `IllegalArgumentException`: Thrown if the provided `destinationDir` is not a directory.
- `IOException`: Thrown if there are issues reading source images or writing to the destination directory.
- **Name Collisions:** No internal protection exists for filename collisions when processing files from different source directories that share the same name.

### Fix Code Hint
```java
// Ensure the directory exists before processing
File outputDir = new File("output/dir");
if (!outputDir.exists()) {
    outputDir.mkdirs();
}
// Proceed with the fluent call
Thumbnails.of(sourceFiles)
    .size(200, 200)
    .asFiles(outputDir, Rename.PREFIX_DOT_THUMBNAIL);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.crop`

### Signature
```java
(Position position):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1226_

### Goal
Crops the thumbnail to the exact dimensions specified by the `size(int, int)` method, using the provided `Position` to determine the anchor point for the crop.

### Parameters
- `position` (`Position`): The geometric anchor point (e.g., `Positions.CENTER`, `Positions.TOP_LEFT`) used to determine which part of the resized image is retained during the crop.

### Input
- **Preconditions:** This method must be used in conjunction with `size(int, int)`. The `size` method must be called to define the target dimensions before or during the fluent chain.
- **Data:** The source image is processed internally by first resizing it so that one dimension matches the target size (potentially causing the other dimension to overhang), then cropping the overhanging portion based on the `position` argument.

### Output
Returns `Builder<T>` — A reference to the current `Builder` instance, allowing for further chaining of transformation or output methods.

### Valid Call Patterns
```java
Thumbnails.of("image.jpg")
    .size(200, 200)
    .crop(Positions.CENTER)
    .toFile("thumbnail.jpg");
```

### LLM Instruction Prompt
- When using `crop(Position)`, ensure the `size(int, int)` method is also called in the fluent chain.
- Do not call `scale(double)` or `scale(double, double)` in the same chain as `crop()`, as this will trigger an `IllegalStateException`.
- Ensure the `position` argument is not `null` to avoid a `NullPointerException`.

### Prompt Snippet
```text
// Example of cropping to the center of an image
Thumbnails.of(sourceFile)
    .size(width, height)
    .crop(Positions.CENTER)
    .toFile(outputFile);
```

### Common Failure Modes
- **IllegalStateException:** Occurs if `scale()` is called in the same chain as `crop()`, or if `crop()` is called more than once on the same builder.
- **NullPointerException:** Occurs if the `position` argument passed to the method is `null`.
- **Missing Size:** If `size(int, int)` is omitted, the crop operation cannot determine the target dimensions, leading to runtime errors during the build process.

### Fix Code Hint
```java
// Ensure size is set and scale is omitted
Thumbnails.of(file)
    .size(100, 100)
    .crop(Positions.TOP_LEFT)
    .toFile(output);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.determineOutputFormat`

### Signature
```java
():Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1720_

### Goal
Configures the thumbnail generation pipeline to automatically infer the output image format based on available metadata, such as the destination file extension.

### Parameters
_None._

### Input
The caller must have an active `Thumbnails.Builder` instance. This method is typically used when writing to files, where the library can inspect the target file path to determine the appropriate image format (e.g., ".jpg" or ".png").

### Output
Returns `Builder<T>` — A reference to the current `Builder` instance, allowing for further method chaining in the fluent API.

### Valid Call Patterns
```java
Thumbnails.of("input.jpg")
    .size(100, 100)
    .determineOutputFormat()
    .toFile("output.png");
```

### LLM Instruction Prompt
- Use `determineOutputFormat()` only when the output destination is a file and the format should be derived from the file extension.
- Do not call this method if an explicit `outputFormat(String)` has already been set in the builder chain.
- Ensure this method is called exactly once per builder instance to avoid state exceptions.

### Prompt Snippet
```text
// Example of using determineOutputFormat to infer format from file extension
Thumbnails.of(sourceFile)
    .size(200, 200)
    .determineOutputFormat()
    .toFile(destinationFile);
```

### Common Failure Modes
- **IllegalStateException:** Thrown if `determineOutputFormat()` is called more than once on the same builder instance.
- **Ambiguity:** If the output destination is not a file (e.g., an `OutputStream`), the library may be unable to determine the format, potentially leading to errors during the write phase.
- **Conflict:** Calling `determineOutputFormat()` after explicitly calling `outputFormat(String)` may lead to unexpected behavior or state conflicts.

### Fix Code Hint
```java
// Ensure only one format-setting method is used
Thumbnails.of(file)
    .size(100, 100)
    .determineOutputFormat() // Use this OR .outputFormat("jpg"), not both
    .toFile(outputFile);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.dithering`

### Signature
```java
(Dithering config):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1402_

### Goal
Sets the dithering mode to be applied during the image resizing operation to control color quantization artifacts in the generated thumbnail.

### Parameters
- `config` (`Dithering`): The dithering configuration to apply, typically selected from the `net.coobird.thumbnailator.resizers.configurations.Dithering` enum (e.g., `Dithering.ENABLE` or `Dithering.DISABLE`).

### Input
The caller must provide a valid `Dithering` enum constant. This method is an optional configuration step in the fluent `Thumbnails.Builder` pipeline. It must be called before the terminal output method (e.g., `toFile`, `asBufferedImage`).

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further method chaining.

### Valid Call Patterns
```java
Thumbnails.of("input.jpg")
    .size(200, 200)
    .dithering(Dithering.ENABLE)
    .toFile("output.jpg");
```

### LLM Instruction Prompt
- When using `dithering()`, ensure it is part of a fluent chain initiated by `Thumbnails.of()`.
- Do not call this method more than once in a single chain, as it will trigger an `IllegalStateException`.
- Do not use this method in combination with `resizerFactory()`, as they are mutually exclusive configurations.

### Prompt Snippet
```text
.dithering(Dithering.ENABLE)
```

### Common Failure Modes
- **IllegalStateException**: Occurs if `dithering()` is called more than once on the same builder instance.
- **IllegalStateException**: Occurs if `dithering()` is called in the same chain as `resizerFactory()`.
- **NullPointerException**: Occurs if the `config` argument passed is `null`.

### Fix Code Hint
```java
// Ensure dithering is set only once and not combined with a custom ResizerFactory
Thumbnails.of(source)
    .size(100, 100)
    .dithering(Dithering.ENABLE) // Correct
    .toFile(destination);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.forceSize`

### Signature
```java
(int width,int height):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:940_

### Goal
Forces the thumbnail to the specified dimensions, ignoring the original image's aspect ratio.

### Parameters
- `width` (`int`): The target width of the thumbnail in pixels.
- `height` (`int`): The target height of the thumbnail in pixels.

### Input
The caller must provide a `Thumbnails.Builder` instance initiated via `Thumbnails.of(...)`. This method is a configuration step in the fluent pipeline. It is mutually exclusive with `scale(double)` and `size(int, int)`.

### Output
Returns `Builder<T>` — A reference to the current builder instance, allowing for further chaining of transformation or output methods.

### Valid Call Patterns
```java
Thumbnails.of("image.jpg")
    .forceSize(200, 200)
    .toFile("thumbnail.jpg");
```

### LLM Instruction Prompt
- Use `forceSize(int, int)` only when the aspect ratio of the source image should be ignored.
- Do not call `scale(double)` or `size(int, int)` in the same builder chain, as this will trigger an `IllegalStateException`.
- Ensure this method is called exactly once per builder instance.

### Prompt Snippet
```text
// Example of forcing a specific size
Thumbnails.of(sourceFile)
    .forceSize(width, height)
    .toFile(destinationFile);
```

### Common Failure Modes
- **IllegalStateException**: Thrown if `scale(double)` has already been called on the builder, or if `forceSize` is called more than once.
- **Distortion**: Because this method ignores aspect ratio, the resulting thumbnail may appear stretched or squashed if the source dimensions do not match the target ratio.

### Fix Code Hint
```java
// If you need to maintain aspect ratio, use .size(width, height) instead of .forceSize(width, height)
Thumbnails.of(source)
    .size(200, 200) 
    .toFile(dest);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.height`

### Signature
```java
(int height):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:905_

### Goal
Sets the target height for the thumbnail, automatically preserving the original image's aspect ratio.

### Parameters
- `height` (`int`): The desired height of the thumbnail in pixels.

### Input
- **Preconditions:** The builder must not have already had `size(int, int)`, `scale(double)`, or `height(int)` called on it.
- **Data:** The input image source must be initialized via `Thumbnails.of(...)` or equivalent.
- **Formats:** Supports any image format recognized by the Java Image I/O API.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further fluent configuration of the thumbnail pipeline.

### Valid Call Patterns
```java
Thumbnails.of("input.jpg")
    .height(200)
    .toFile("output.jpg");
```

### LLM Instruction Prompt
- When using `height(int)`, ensure it is the sole sizing method invoked in the fluent chain.
- Do not chain `height(int)` with `size(int, int)` or `scale(double)`, as this will trigger an `IllegalStateException`.
- Ensure the builder is used in a single fluent chain and not stored in a variable for reuse, as `ThumbnailParameter` is mutable and not thread-safe.

### Prompt Snippet
```text
// Example of setting height while preserving aspect ratio
Thumbnails.of(sourceImage)
    .height(300)
    .toFile(destinationFile);
```

### Common Failure Modes
- **IllegalStateException:** Occurs if `height(int)` is called after `size(int, int)` or `scale(double)`, or if `height(int)` is called more than once on the same builder instance.
- **Aspect Ratio Misconception:** Users may expect to set both width and height; if both are required, use `size(int, int)` instead.
- **Memory Constraints:** For very large images or batch processing, ensure the pipeline terminates with a memory-efficient method like `toFiles()` rather than `asBufferedImages()` to avoid `OutOfMemoryError`.

### Fix Code Hint
```java
// Incorrect: Multiple sizing methods
Thumbnails.of("img.jpg").size(100, 100).height(200).toFile("out.jpg");

// Correct: Single sizing method
Thumbnails.of("img.jpg").height(200).toFile("out.jpg");
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.imageType`

### Signature
```java
(int type):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1283_

### Goal
Sets the specific `BufferedImage` type (e.g., `BufferedImage.TYPE_INT_ARGB`) for the generated thumbnail.

### Parameters
- `type` (`int`): An integer constant representing the desired `BufferedImage` type, as defined in the `java.awt.image.BufferedImage` class.

### Input
The caller must provide a valid integer constant corresponding to a `BufferedImage` type. This method is optional; if not called, the library will determine an appropriate type automatically.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further method chaining in the fluent pipeline.

### Valid Call Patterns
```java
// Example: Setting the image type to ARGB for transparency support
Thumbnails.of("input.jpg")
    .size(200, 200)
    .imageType(BufferedImage.TYPE_INT_ARGB)
    .toFile("output.png");
```

### LLM Instruction Prompt
- When using `imageType(int)`, ensure the integer provided is a valid constant from `java.awt.image.BufferedImage`.
- Only call this method once per builder instance.
- Chain this method within the fluent `Thumbnails` pipeline before the terminal output operation (e.g., `.toFile()`, `.asBufferedImage()`).

### Prompt Snippet
```text
.imageType(BufferedImage.TYPE_INT_RGB)
```

### Common Failure Modes
- **IllegalStateException**: Thrown if `imageType()` is called more than once on the same `Builder` instance.
- **Invalid Type**: Passing an unsupported or invalid integer constant may result in errors during the `BufferedImage` creation phase.

### Fix Code Hint
```java
// Ensure the method is called exactly once in the chain
Thumbnails.of(source)
    .size(100, 100)
    .imageType(BufferedImage.TYPE_INT_RGB) // Correct
    .toFile(destination);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.iterableBufferedImages`

### Signature
```java
():Iterable<BufferedImage>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2213_

### Goal
Provides a memory-efficient, lazy-evaluated mechanism to generate and retrieve multiple thumbnails as an `Iterable<BufferedImage>` from a configured `Thumbnails.Builder`.

### Parameters
_None._

### Input
The caller must have a `Thumbnails.Builder` instance configured with one or more input sources (e.g., `File`, `InputStream`, or `URL`). The builder must have transformation parameters (like `size()`, `scale()`, or `addFilter()`) defined prior to calling this method.

### Output
Returns `Iterable<BufferedImage>` — An iterable collection of thumbnails. Each `BufferedImage` is generated lazily upon calling `Iterator.next()`. If an `IOException` occurs during the processing of a specific thumbnail, the iterator will return `null` for that element.

### Valid Call Patterns
```java
Iterable<BufferedImage> thumbnails = Thumbnails.of(file1, file2, file3)
    .size(100, 100)
    .iterableBufferedImages();
```

### LLM Instruction Prompt
- Use `iterableBufferedImages()` when processing multiple images to prevent `OutOfMemoryError` by avoiding the retention of all thumbnails in heap memory simultaneously.
- Do not use this method if you require immediate completion of all tasks; processing is lazy and occurs during iteration.
- Ensure the builder is configured with valid source inputs before invocation.

### Prompt Snippet
```text
// Example of lazy processing
for (BufferedImage thumb : Thumbnails.of(files).size(50, 50).iterableBufferedImages()) {
    if (thumb != null) {
        // Process thumbnail
    }
}
```

### Common Failure Modes
- **OutOfMemoryError:** While this method mitigates memory pressure, processing extremely large batches or high-resolution images without sufficient heap space can still lead to memory exhaustion.
- **Null Elements:** If an `IOException` occurs during the processing of a specific image in the batch, the iterator returns `null` for that entry; failure to check for `null` will result in a `NullPointerException`.
- **JPEG Alpha Channel Issues:** If the input images contain alpha channels, the default Java JPEG writer may produce incorrect colors or throw exceptions. Use `toFiles()` or `asFiles()` with appropriate `Rename` strategies instead if writing to disk.

### Fix Code Hint
```java
// Check for null elements when iterating
for (BufferedImage thumb : builder.iterableBufferedImages()) {
    if (null != thumb) {
        // Safe to use
    }
}
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.keepAspectRatio`

### Signature
```java
(boolean keep):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1491_

### Goal
Configures whether the thumbnail generation process should maintain the original image's aspect ratio when resizing to a target dimension.

### Parameters
- `keep` (`boolean`): `true` to preserve the original aspect ratio (default behavior); `false` to allow the image to be stretched or squashed to fit the exact target dimensions.

### Input
- **Preconditions:** The `size(int, int)` method must be called prior to `keepAspectRatio`.
- **Compatibility:** This method cannot be used if `scale(double)` or `scale(double, double)` has already been invoked.
- **Default:** If this method is not called, the aspect ratio is preserved by default.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further fluent method chaining.

### Valid Call Patterns
```java
Thumbnails.of("image.jpg")
    .size(200, 200)
    .keepAspectRatio(true)
    .toFile("thumbnail.jpg");
```

### LLM Instruction Prompt
- When generating code, ensure `keepAspectRatio` is chained after `size(int, int)` and before any terminal output method (e.g., `toFile`, `asBufferedImage`). Do not call this method if `scale()` has been used in the same pipeline.

### Prompt Snippet
```text
// Example of preserving aspect ratio in a fluent pipeline
Thumbnails.of(sourceFile)
    .size(width, height)
    .keepAspectRatio(true)
    .toFile(outputFile);
```

### Common Failure Modes
- **IllegalStateException:** Thrown if `keepAspectRatio` is called before `size(int, int)`.
- **IllegalStateException:** Thrown if `keepAspectRatio` is called after `scale(double)` or `scale(double, double)`.
- **IllegalStateException:** Thrown if `width(int)` or `height(int)` has been called and the user attempts to disable aspect ratio preservation.

### Fix Code Hint
```java
// Ensure size is defined first
Thumbnails.of(file)
    .size(100, 100)
    .keepAspectRatio(false) // Now valid
    .toFile(output);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.outputFormat`

### Signature
```java
(String format):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1658_

### Goal
Sets the compression format to be used when writing the thumbnail to a destination file or stream.

### Parameters
- `format` (`String`): The name of the image format (e.g., "jpg", "png", "gif") as recognized by the Java Image I/O API.

### Input
The caller must provide a valid format string supported by the underlying JRE's `ImageIO` implementation. Valid format names can be verified by calling `javax.imageio.ImageIO.getWriterFormatNames()`. This method is optional; if omitted, the library may attempt to infer the format from the destination file extension.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further fluent configuration of the thumbnail pipeline.

### Valid Call Patterns
```java
Thumbnails.of(new File("path/to/directory").listFiles())
    .size(640, 480)
    .outputFormat("jpg")
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

### LLM Instruction Prompt
When using `outputFormat`, ensure it is chained within a `Thumbnails` fluent pipeline. Do not use this method when the terminal operation is `asBufferedImage()` or `asBufferedImages()`, as it has no effect on in-memory image objects. Ensure the format string matches a value returned by `ImageIO.getWriterFormatNames()`.

### Prompt Snippet
```text
.outputFormat("jpg")
```

### Common Failure Modes
- **IllegalStateException**: Thrown if `outputFormat` is called more than once on the same builder instance.
- **IllegalArgumentException**: Thrown if the provided format string is not supported by the Java Image I/O API.
- **No-op**: Calling this method when the terminal operation is `asBufferedImage()` or `asBufferedImages()` will not change the output, as these methods return `BufferedImage` objects rather than encoded files or streams.

### Fix Code Hint
```java
// Ensure only one format is set and the format is supported
try {
    Thumbnails.of("input.png")
        .size(100, 100)
        .outputFormat("jpg")
        .toFile("output.jpg");
} catch (IllegalArgumentException e) {
    // Handle unsupported format
} catch (IllegalStateException e) {
    // Handle multiple calls to outputFormat
}
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.outputFormatType`

### Signature
```java
(String formatType):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1762_

### Goal
Sets the specific compression format type (e.g., "JPEG", "LZW") for the thumbnail output to control the underlying image writer's compression behavior.

### Parameters
- `formatType` (`String`): The compression format type string supported by the underlying image writer; use `ThumbnailParameter.DEFAULT_FORMAT_TYPE` to revert to the codec's default.

### Input
- **Preconditions:** The output format must be specified (e.g., via `outputFormat()`) before calling this method.
- **Compatibility:** The specified `formatType` must be supported by the image writer for the chosen output format. If the underlying writer does not support compression, this setting is ignored.

### Output
Returns `Builder<T>` — The current `Builder` instance, allowing for further fluent configuration of the thumbnail pipeline.

### Valid Call Patterns
```java
Thumbnails.of("input.jpg")
    .size(200, 200)
    .outputFormat("jpg")
    .outputFormatType("JPEG")
    .toFile("output.jpg");
```

### LLM Instruction Prompt
- When calling `outputFormatType`, ensure the output format is already defined in the builder chain.
- Do not call this method more than once on the same builder instance.
- If the goal is to use the default compression for the chosen format, pass `ThumbnailParameter.DEFAULT_FORMAT_TYPE`.
- Note that calling this method disables the `outputFormat` method to prevent configuration conflicts.

### Prompt Snippet
```text
// Example of setting compression type
builder.outputFormat("jpg").outputFormatType("JPEG");
```

### Common Failure Modes
- `IllegalStateException`: Thrown if `outputFormatType` is called more than once on the same builder.
- `IllegalArgumentException`: Thrown if the `formatType` is unsupported by the current output format, or if `outputFormat` has not been set prior to calling this method.
- **Silent Ignorance:** The setting may be ignored if the underlying Java Image I/O writer does not support compression for the selected format.

### Fix Code Hint
```java
// Ensure outputFormat is set first, and call outputFormatType only once
Thumbnails.of(source)
    .size(100, 100)
    .outputFormat("tiff")
    .outputFormatType("LZW") // Valid only after outputFormat is set
    .toFile("output.tiff");
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.outputQuality`

### Signature
```java
(float quality):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1550_

### Goal
Sets the compression quality for the thumbnail output when writing to external destinations like files or streams.

### Parameters
- `quality` (`float`): A value between `0.0f` (minimum quality) and `1.0f` (maximum quality) that dictates the compression level applied by the codec.

### Input
- **Data:** Requires a `Thumbnails.Builder<T>` instance initiated via `Thumbnails.of(...)`.
- **Formats:** Applicable only when writing to formats that support compression (e.g., JPEG). If the underlying image writer does not support compression, this setting is ignored.
- **Preconditions:** Must be called before the terminal output method (e.g., `toFile()`).

### Output
Returns `Builder<T>` — A reference to the current builder instance, allowing for further method chaining.

### Valid Call Patterns
```java
Thumbnails.of("input.jpg")
    .size(200, 200)
    .outputQuality(0.8f)
    .toFile("output.jpg");
```

### LLM Instruction Prompt
- Use `outputQuality(float)` to control compression for file or stream outputs.
- Do not use this method when the terminal operation is `asBufferedImage()` or `asBufferedImages()`, as it has no effect on in-memory objects.
- Ensure the `quality` argument is a `float` between `0.0f` and `1.0f`.

### Prompt Snippet
```text
// Example of setting output quality to 80%
Thumbnails.of(sourceFile)
    .size(100, 100)
    .outputQuality(0.8f)
    .toFile(destinationFile);
```

### Common Failure Modes
- **`IllegalArgumentException`:** Thrown if the provided `quality` value is less than `0.0f` or greater than `1.0f`.
- **`IllegalStateException`:** Thrown if `outputQuality` is called more than once, or if both `outputQuality(float)` and `outputQuality(double)` are used in the same chain.
- **No-op:** The setting is ignored if the output format (e.g., PNG) does not support compression settings via the standard Java Image I/O writer.

### Fix Code Hint
```java
// Ensure only one quality method is called and the value is within [0.0, 1.0]
Thumbnails.of(file)
    .size(50, 50)
    .outputQuality(0.75f) // Correct usage
    .toFile(output);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.rendering`

### Signature
```java
(Rendering config):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1448_

### Goal
Sets the rendering hint configuration for the resizing operation to control the trade-off between image quality and processing speed during thumbnail generation.

### Parameters
- `config` (`Rendering`): The rendering mode to apply, typically selected from the `net.coobird.thumbnailator.resizers.configurations.Rendering` enum (e.g., `Rendering.QUALITY` or `Rendering.SPEED`).

### Input
- **Preconditions:** The builder must be in a valid state. This method is optional.
- **Compatibility:** This method cannot be used in conjunction with `resizerFactory(ResizerFactory)`. If a custom resizer factory is already set, this call will conflict.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further method chaining in the fluent pipeline.

### Valid Call Patterns
```java
// Example inferred from API signature and documentation
Thumbnails.of("input.jpg")
    .size(200, 200)
    .rendering(Rendering.QUALITY)
    .toFile("output.jpg");
```

### LLM Instruction Prompt
- When calling `rendering`, ensure it is part of a fluent chain initiated by `Thumbnails.of()` or `Thumbnails.from()`.
- Do not call `rendering` if `resizerFactory` has already been invoked in the same chain.
- Only call `rendering` once per builder instance to avoid `IllegalStateException`.

### Prompt Snippet
```text
// Example of setting rendering quality
.rendering(Rendering.QUALITY)
```

### Common Failure Modes
- **`IllegalStateException`:** Thrown if `rendering()` is called more than once on the same builder instance.
- **`IllegalStateException`:** Thrown if `rendering()` is called after `resizerFactory()` has already been configured in the chain.
- **Logic Error:** Attempting to set rendering modes that conflict with the underlying `Resizer` implementation chosen by the library.

### Fix Code Hint
```java
// Ensure rendering is set only once and not combined with resizerFactory
Thumbnails.of(file)
    .size(100, 100)
    .rendering(Rendering.SPEED) // Correct: single call
    .toFile(output);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.resizer`

### Signature
```java
(Resizer resizer):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1323_

### Goal
Sets a specific `Resizer` implementation to be used for the thumbnail generation process, overriding the default automatic selection.

### Parameters
- `resizer` (`Resizer`): The specific scaling algorithm to use (e.g., `Resizers.BILINEAR`, `Resizers.BICUBIC`, or `Resizers.PROGRESSIVE_BILINEAR`).

### Input
- **Data:** A valid `Resizer` instance.
- **Preconditions:** The builder must not have already had a `Resizer` set, and it must not have been configured with a `ResizerFactory` via `resizerFactory(ResizerFactory)`.
- **Formats:** Compatible with any image source supported by the `Thumbnails.Builder` (Files, Streams, URLs, or `BufferedImage`).

### Output
Returns `Builder<T>` — A reference to the current `Builder` instance, allowing for further fluent configuration of the thumbnail pipeline.

### Valid Call Patterns
```java
Thumbnails.of("input.jpg")
    .size(200, 200)
    .resizer(Resizers.BILINEAR)
    .toFile("output.jpg");
```

### LLM Instruction Prompt
- When using `resizer()`, ensure the builder has not previously called `resizer()` or `resizerFactory()`.
- Use this method only when a specific scaling algorithm is required; otherwise, rely on the library's default `DefaultResizerFactory` which automatically selects the optimal resizer based on dimensions.

### Prompt Snippet
```text
// Example of setting a specific resizer in a fluent chain
Thumbnails.of(source)
    .size(width, height)
    .resizer(Resizers.PROGRESSIVE_BILINEAR)
    .toFile(destination);
```

### Common Failure Modes
- **IllegalStateException:** Thrown if `resizer()` is called more than once on the same builder instance.
- **IllegalStateException:** Thrown if `resizer()` is called after `resizerFactory()` has already been invoked on the same builder.
- **NullPointerException:** Thrown if the provided `resizer` argument is `null`.

### Fix Code Hint
```java
// Ensure only one resizer configuration method is used
Thumbnails.of(file)
    .size(100, 100)
    // .resizerFactory(...) // Do not use this if calling .resizer()
    .resizer(Resizers.BICUBIC) 
    .toFile(output);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.resizerFactory`

### Signature
```java
(ResizerFactory resizerFactory):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1348_

### Goal
Sets the `ResizerFactory` used by the builder to determine the optimal scaling algorithm for the thumbnail generation task.

### Parameters
- `resizerFactory` (`ResizerFactory`): An implementation of `ResizerFactory` that provides the specific `Resizer` (e.g., `Bilinear`, `Bicubic`, `ProgressiveBilinear`) to be used during the resizing process.

### Input
- **Preconditions:** The `ResizerFactory` must be provided before the terminal operation (e.g., `toFile`, `asBufferedImage`) is called.
- **Compatibility:** This method is optional; if not called, the library uses the `DefaultResizerFactory`.
- **Constraints:** This method cannot be used in conjunction with the `resizer(Resizer)` method.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further method chaining.

### Valid Call Patterns
```java
Thumbnails.of("input.jpg")
    .size(200, 200)
    .resizerFactory(new DefaultResizerFactory())
    .toFile("output.jpg");
```

### LLM Instruction Prompt
- When using `resizerFactory`, ensure it is not chained with `.resizer(Resizer)`.
- Only call this method once per builder instance to avoid `IllegalStateException`.
- Use this to override the default resizing logic when specific performance or quality requirements (e.g., specific interpolation algorithms) are needed.

### Prompt Snippet
```text
// Example of setting a custom resizer factory
Thumbnails.of(sourceImage)
    .size(width, height)
    .resizerFactory(myCustomResizerFactory)
    .toFile(destination);
```

### Common Failure Modes
- **IllegalStateException:** Thrown if `resizerFactory` is called more than once on the same builder instance.
- **IllegalStateException:** Thrown if `resizerFactory` is called in the same chain as `resizer(Resizer)`.
- **NullPointerException:** Thrown if the provided `resizerFactory` argument is `null`.

### Fix Code Hint
```java
// Ensure only one resizer configuration method is used
Thumbnails.of(file)
    .size(100, 100)
    // .resizer(Resizers.BILINEAR) // Remove this if using resizerFactory
    .resizerFactory(DefaultResizerFactory.getInstance())
    .toFile(output);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.rotate`

### Signature
```java
(double angle):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1969_

### Goal
Applies a clockwise rotation to the thumbnail image by the specified number of degrees within the fluent processing pipeline.

### Parameters
- `angle` (`double`): The angle of rotation in degrees. Positive values result in clockwise rotation.

### Input
The caller must have an active `Thumbnails.Builder` instance initiated via `Thumbnails.of(...)` or similar entry points. This method can be called multiple times; if invoked repeatedly, the rotations are applied cumulatively in the order they are called.

### Output
Returns `Builder<T>` — A reference to the current `Builder` instance, allowing for further method chaining in the fluent API.

### Valid Call Patterns
```java
// Inferred from API documentation
Thumbnails.of("image.jpg")
    .size(200, 200)
    .rotate(90.0)
    .toFile("output.jpg");
```

### LLM Instruction Prompt
- When using `rotate(double angle)`, ensure it is chained within a `Thumbnails.Builder` pipeline.
- Remember that rotations are cumulative; calling `.rotate(45.0).rotate(45.0)` is equivalent to a 90-degree rotation.
- The rotation is applied as part of the transformation sequence defined by the builder.

### Prompt Snippet
```text
.rotate(angle)
```

### Common Failure Modes
- **Logic Errors:** Expecting the rotation to be absolute rather than cumulative when calling the method multiple times.
- **Pipeline Misuse:** Attempting to call `rotate` on a `Thumbnails.Builder` that has already been terminated by an output method (e.g., `.toFile()` or `.asBufferedImage()`).
- **Coordinate System:** Assuming counter-clockwise rotation for positive values; the library strictly performs clockwise rotation.

### Fix Code Hint
```java
// Ensure rotation is applied before the terminal output method
Thumbnails.of(source)
    .rotate(180.0) // Apply rotation
    .toFile(destination); // Terminate pipeline
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.scale`

### Signature
```java
(double scaleWidth,double scaleHeight):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1023_

### Goal
Sets the scaling factors for the width and height of the thumbnail, allowing for non-proportional resizing if the factors differ.

### Parameters
- `scaleWidth` (`double`): The scaling factor to apply to the image width; must be greater than 0.0 and not `Double.POSITIVE_INFINITY`.
- `scaleHeight` (`double`): The scaling factor to apply to the image height; must be greater than 0.0 and not `Double.POSITIVE_INFINITY`.

### Input
The caller must provide a `Thumbnails.Builder<T>` instance initiated via `Thumbnails.of(...)`. The input image source (File, URL, InputStream, or BufferedImage) is processed by the builder. Precondition: This method must not be called if `size(int, int)`, `scale(double)`, or `keepAspectRatio(boolean)` have already been invoked on the same builder instance.

### Output
Returns `Builder<T>` — A reference to the current builder instance, allowing for further fluent configuration of the thumbnail pipeline.

### Valid Call Patterns
```java
Thumbnails.of(image)
    .scale(0.5, 0.75)
    .toFile(thumbnail);
```

### LLM Instruction Prompt
- Use this method to define resizing via scaling factors rather than fixed pixel dimensions.
- Ensure the builder has not been configured with `size()` or `scale(double)` prior to this call.
- Always chain this method directly from the `Thumbnails.of()` entry point to maintain the fluent pipeline.

### Prompt Snippet
```text
Thumbnails.of(sourceImage).scale(widthFactor, heightFactor).toFile(destination);
```

### Common Failure Modes
- `IllegalStateException`: Thrown if `size(int, int)`, `scale(double)`, or `keepAspectRatio(boolean)` were previously called on the builder.
- `IllegalStateException`: Thrown if this method is called more than once on the same builder instance.
- `IllegalArgumentException`: Thrown if either `scaleWidth` or `scaleHeight` is less than or equal to 0.0, or if they are `Double.POSITIVE_INFINITY`.

### Fix Code Hint
```java
// Ensure only one sizing method is used in the chain
Thumbnails.of(image)
    .scale(0.5, 0.5) // Correct: replaces size() or single-arg scale()
    .toFile("output.jpg");
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.scalingMode`

### Signature
```java
(ScalingMode config):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1300_

### Goal
Configures the specific scaling algorithm or mode to be used during the thumbnail resizing process.

### Parameters
- `config` (`ScalingMode`): The `ScalingMode` configuration object that defines the interpolation or scaling strategy (e.g., `ScalingMode.BILINEAR`, `ScalingMode.BICUBIC`, `ScalingMode.PROGRESSIVE_BILINEAR`) to be applied to the image.

### Input
The caller must provide a valid `ScalingMode` instance. This method is typically called as part of the fluent `Thumbnails.Builder` chain after initializing the source (e.g., `Thumbnails.of(...)`) and before terminal operations like `toFile()` or `asBufferedImage()`.

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further method chaining.

### Valid Call Patterns
```java
// Example inferred from signature and API documentation
Thumbnails.of("image.jpg")
    .size(200, 200)
    .scalingMode(ScalingModes.BILINEAR)
    .toFile("thumbnail.jpg");
```

### LLM Instruction Prompt
- When calling `scalingMode`, ensure it is invoked only once per builder instance.
- The `ScalingMode` argument must be a valid member of the `ScalingModes` or a custom implementation of the `ScalingMode` interface.
- Do not call this method after the terminal operation has been triggered.

### Prompt Snippet
```text
// Configure the builder with a specific scaling mode
.scalingMode(ScalingModes.PROGRESSIVE_BILINEAR)
```

### Common Failure Modes
- **IllegalStateException**: Thrown if `scalingMode` is called more than once on the same `Builder` instance.
- **NullPointerException**: Occurs if the `config` argument passed is `null`.
- **Logic Error**: Attempting to set the scaling mode after the thumbnail has already been generated or the builder has been finalized.

### Fix Code Hint
```java
// Ensure scalingMode is called exactly once in the chain
Thumbnails.of(source)
    .size(100, 100)
    .scalingMode(ScalingModes.BICUBIC) // Call once
    .toFile(destination);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.size`

### Signature
```java
(int width,int height):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:843_

### Goal
Sets the target dimensions for the thumbnail, ensuring the output image fits within the specified bounding box while preserving the original image's aspect ratio.

### Parameters
- `width` (`int`): The maximum width of the thumbnail in pixels.
- `height` (`int`): The maximum height of the thumbnail in pixels.

### Input
The caller must have initiated a `Thumbnails.Builder` via `Thumbnails.of(...)` or `Thumbnails.from...(...)`. The input image source can be a `File`, `InputStream`, `URL`, or `BufferedImage`. Precondition: The builder must not have had `scale(double)` or `size(int, int)` called previously in the current chain.

### Output
Returns `Builder<T>` — A reference to the current builder instance, allowing for further fluent configuration of the thumbnail pipeline.

### Valid Call Patterns
```java
Thumbnails.of(new File("path/to/directory").listFiles())
    .size(640, 480)
    .outputFormat("jpg")
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

### LLM Instruction Prompt
When using `size(int, int)`, ensure it is called exactly once in the fluent chain. Do not combine it with `scale(double)` or `forceSize(int, int)` in the same builder instance, as these are mutually exclusive. The `width` and `height` parameters define a bounding box; the library will automatically maintain the aspect ratio of the source image.

### Prompt Snippet
```text
// Example of setting thumbnail size in a fluent pipeline
Thumbnails.of(sourceImage)
    .size(width, height)
    .toFile(destinationFile);
```

### Common Failure Modes
- `IllegalStateException`: Thrown if `size()` is called more than once, or if `scale()` has already been invoked on the same builder instance.
- `IllegalArgumentException`: Thrown if the provided `width` or `height` values are non-positive.
- Aspect Ratio Issues: If the image must be forced to the exact dimensions regardless of aspect ratio, `size()` will not suffice; `forceSize(int, int)` must be used instead.

### Fix Code Hint
```java
// If you previously called scale(), remove it before calling size()
Thumbnails.of(image)
    .size(200, 200) // Correct: only one sizing method
    .toFile("output.jpg");
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.sourceRegion`

### Signature
```java
(int x,int y,int width,int height):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1117_

### Goal
Specifies a rectangular sub-region of the source image to be used as the input for the thumbnail generation process, effectively cropping the image before resizing.

### Parameters
- `x` (`int`): The horizontal coordinate of the top-left corner of the source region.
- `y` (`int`): The vertical coordinate of the top-left corner of the source region.
- `width` (`int`): The width of the source region; must be greater than 0.
- `height` (`int`): The height of the source region; must be greater than 0.

### Input
The caller must provide a valid source image (e.g., `File`, `InputStream`, or `BufferedImage`) via the `Thumbnails.of(...)` entry point. The coordinates and dimensions must be within the bounds of the source image.

### Output
Returns `Builder<T>` — The current `Builder` instance, allowing for further fluent configuration of the thumbnail pipeline.

### Valid Call Patterns
```java
Thumbnails.of("image.jpg")
    .sourceRegion(0, 0, 100, 100)
    .size(50, 50)
    .toFile("thumbnail.jpg");
```

### LLM Instruction Prompt
- Use this method to perform a crop operation on the source image.
- Ensure `width` and `height` are positive integers.
- Do not call this method more than once in a single fluent chain.
- Chain this method after `Thumbnails.of(...)` and before terminal operations like `toFile(...)` or `asBufferedImage()`.

### Prompt Snippet
```text
// Example of cropping the top-left 100x100 pixels of an image
Thumbnails.of(sourceFile)
    .sourceRegion(0, 0, 100, 100)
    .size(50, 50)
    .toFile(outputFile);
```

### Common Failure Modes
- `IllegalStateException`: Thrown if `sourceRegion` is called more than once on the same builder instance.
- `IllegalArgumentException`: Thrown if `width` or `height` is less than or equal to 0.
- `NullPointerException`: Occurs if the source image was not properly initialized in the builder.
- Out-of-bounds cropping: If the specified region exceeds the source image dimensions, the behavior depends on the underlying `ImageIO` implementation; ensure coordinates are validated against source image metadata if necessary.

### Fix Code Hint
```java
// Ensure only one region is defined and dimensions are positive
if (width > 0 && height > 0) {
    Thumbnails.of(image)
        .sourceRegion(x, y, width, height)
        .size(targetW, targetH)
        .toFile(output);
}
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.toFile`

### Signature
```java
(File outFile):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2652_

### Goal
Generates a thumbnail from a single source image and writes the resulting image data to the specified filesystem `File` destination.

### Parameters
- `outFile` (`File`): The target file on the filesystem where the processed thumbnail image will be saved.

### Input
- **Data:** Requires a single source image (e.g., `File`, `InputStream`, `URL`, or `BufferedImage`) configured via the `Thumbnails.Builder` fluent pipeline.
- **Formats:** Supports any image format recognized by the Java Image I/O API.
- **Preconditions:** The builder must be configured with a single source. If multiple sources are provided (e.g., a list of files), this method will throw an `IllegalArgumentException`.

### Output
Returns `void` — The method performs a side-effect operation by writing the processed image to the disk.

### Valid Call Patterns
```java
Thumbnails.of("path/to/image.jpg")
    .size(200, 200)
    .toFile(new File("path/to/thumbnail.jpg"));
```

### LLM Instruction Prompt
- When using `toFile(File outFile)`, ensure the `Thumbnails.Builder` has been initialized with exactly one source image.
- Always chain this method as the terminal operation in the fluent pipeline.
- If the target file might already exist, consider calling `allowOverwrite(true)` on the builder if overwriting is intended, as the default behavior may vary or throw an exception if disabled.

### Prompt Snippet
```text
// Example of writing a thumbnail to a specific File object
Thumbnails.of(sourceFile)
    .size(100, 100)
    .toFile(new File("output/thumb.jpg"));
```

### Common Failure Modes
- **`IllegalArgumentException`:** Thrown if the builder was initialized with multiple source images or if the destination file exists and `allowOverwrite(false)` is set.
- **`IOException`:** Thrown if there is a failure during the reading of the source image or writing to the destination file (e.g., permission issues, disk full).
- **`IllegalStateException`:** Thrown if the builder configuration is invalid, such as calling `scale()` after `size()` has already been defined.

### Fix Code Hint
```java
// Ensure only one source is used and handle potential IO exceptions
try {
    Thumbnails.of(new File("input.jpg"))
        .size(160, 160)
        .allowOverwrite(true)
        .toFile(new File("output.jpg"));
} catch (IOException e) {
    // Handle file access or processing errors
    e.printStackTrace();
}
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.toFiles`

### Signature
```java
(File destinationDir,Rename rename):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2627_

### Goal
Generates thumbnails from a collection of source files and saves them into a specified directory using a defined naming strategy.

### Parameters
- `destinationDir` (`File`): The target directory where the generated thumbnail files will be saved.
- `rename` (`Rename`): A naming strategy (e.g., `Rename.PREFIX_DOT_THUMBNAIL`) used to determine the filename of each output thumbnail based on the original source filename.

### Input
- **Data:** Requires a `Thumbnails.Builder` initialized with source files (e.g., via `Thumbnails.of(File...)`).
- **Preconditions:** The source images must have been created from files. The `destinationDir` must exist and be a valid directory.
- **Constraints:** If `allowOverwrite(false)` is set, the method will skip writing files that already exist in the destination directory.

### Output
Returns `void` — The method performs the side effect of writing processed image files to the specified filesystem location.

### Valid Call Patterns
```java
Thumbnails.of(new File("path/to/sourceDir").listFiles())
    .size(640, 480)
    .toFiles(new File("path/to/outputDir"), Rename.PREFIX_DOT_THUMBNAIL);
```

### LLM Instruction Prompt
- When using this method, ensure the `Thumbnails.Builder` was initialized with `Thumbnails.of(File...)`.
- Always verify that the `destinationDir` is a directory before passing it to the method.
- Use `Rename` constants to define output naming conventions.

### Prompt Snippet
```text
// Ensure the builder is initialized with files
Thumbnails.of(files)
    .size(width, height)
    .toFiles(destinationDirectory, renameStrategy);
```

### Common Failure Modes
- `IllegalStateException`: Thrown if the builder was not initialized with source files (e.g., initialized with `InputStream` or `URL`).
- `IllegalArgumentException`: Thrown if the provided `destinationDir` is not a valid directory.
- `IOException`: Thrown if there are read/write errors, such as insufficient permissions or disk space.
- **Filename Collisions:** When processing files from different source directories that share the same name, the output files may overwrite each other depending on the `allowOverwrite` setting.

### Fix Code Hint
```java
File destDir = new File("output/path");
if (destDir.isDirectory()) {
    Thumbnails.of(sourceFiles)
        .size(100, 100)
        .toFiles(destDir, Rename.PREFIX_DOT_THUMBNAIL);
}
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.toOutputStream`

### Signature
```java
(OutputStream os):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2728_

### Goal
Writes the processed thumbnail image to the provided `OutputStream` using the configuration defined in the fluent `Thumbnails.Builder` pipeline.

### Parameters
- `os` (`OutputStream`): The destination stream where the generated thumbnail image data will be written. Note that the library does not close this stream upon completion.

### Input
- **Preconditions:** The builder must be configured with a single source image.
- **Format:** The output format must be explicitly specified using the `outputFormat(String)` method before calling this terminal operation.
- **Constraints:** This method is intended for single-image processing; it will throw an exception if multiple source images are specified in the builder.

### Output
Returns `void` — The method performs the side effect of writing the image data to the provided `OutputStream`.

### Valid Call Patterns
```java
ByteArrayOutputStream os = new ByteArrayOutputStream();
Thumbnails.of("image.jpg")
    .size(100, 100)
    .outputFormat("jpg")
    .toOutputStream(os);
```

### LLM Instruction Prompt
- When using `toOutputStream`, ensure the `Thumbnails.Builder` has been initialized with a single source (e.g., `Thumbnails.of(...)`) and that `outputFormat(String)` has been called. Do not assume the `OutputStream` will be closed by the library.

### Prompt Snippet
```text
// Ensure outputFormat is set before calling toOutputStream
Thumbnails.of(source)
    .size(width, height)
    .outputFormat("png")
    .toOutputStream(outputStream);
```

### Common Failure Modes
- **`IllegalArgumentException`**: Occurs if the builder was initialized with multiple source images (e.g., a directory list).
- **`IllegalStateException`**: Occurs if `outputFormat(String)` was not called to define the image encoding.
- **`IOException`**: Occurs if there is an error reading the source image or writing to the provided `OutputStream`.

### Fix Code Hint
```java
// If IllegalStateException occurs, verify output format:
Thumbnails.of(file)
    .size(100, 100)
    .outputFormat("jpg") // Required for toOutputStream
    .toOutputStream(os);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.toOutputStreams`

### Signature
```java
(Iterable<? extends OutputStream> iterable):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:2777_

### Goal
Generates thumbnails from the configured source and writes each resulting image to the corresponding `OutputStream` provided by the `Iterable`.

### Parameters
- `iterable` (`Iterable<? extends OutputStream>`): An `Iterable` that provides an `Iterator` of `OutputStream` objects, where each stream receives one thumbnail from the processing pipeline.

### Input
- **Data:** Requires a previously configured `Thumbnails.Builder` instance (e.g., via `Thumbnails.of(...)`).
- **Formats:** The output format must be explicitly specified using the `.outputFormat(String)` method before calling this terminal operation.
- **Preconditions:** The `OutputStream` objects provided by the `iterable` must be open and writable. Note that the library does not call `OutputStream.close()` upon completion.

### Output
Returns `void` — The method performs the side effect of writing image data to the provided streams.

### Valid Call Patterns
```java
List<OutputStream> streams = Arrays.asList(out1, out2);
Thumbnails.of("image1.jpg", "image2.jpg")
    .size(100, 100)
    .outputFormat("jpg")
    .toOutputStreams(streams);
```

### LLM Instruction Prompt
- When using `toOutputStreams`, ensure the `Thumbnails.Builder` has been initialized with multiple sources (e.g., a collection of files or URLs) to match the number of streams in the `Iterable`.
- Always call `.outputFormat(String)` prior to this method, as it is a mandatory configuration for stream-based output.
- Do not assume the library will close the provided `OutputStream` instances; manage stream lifecycle (closing/flushing) in the calling code.

### Prompt Snippet
```text
// Ensure output format is set before calling toOutputStreams
Thumbnails.of(sourceFiles)
    .size(width, height)
    .outputFormat("png")
    .toOutputStreams(outputStreamList);
```

### Common Failure Modes
- **`IllegalStateException`**: Thrown if the output format has not been specified via `.outputFormat(String)`.
- **`IOException`**: Thrown if an error occurs during image reading or writing to the provided streams.
- **Mismatched Counts**: If the number of sources does not align with the number of streams provided in the `Iterable`, the behavior depends on the underlying `Iterator` implementation.

### Fix Code Hint
```java
// Ensure the output format is defined to avoid IllegalStateException
Thumbnails.of(files)
    .size(200, 200)
    .outputFormat("jpg") // Required for toOutputStreams
    .toOutputStreams(myOutputStreamList);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.useExifOrientation`

### Signature
```java
(boolean useExifOrientation):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1700_

### Goal
Configures the thumbnail generation pipeline to automatically detect and apply orientation transformations based on the source image's Exif metadata.

### Parameters
- `useExifOrientation` (`boolean`): Set to `true` to enable automatic orientation correction based on Exif tags; `false` to ignore Exif orientation metadata.

### Input
- **Data:** Requires an image source (e.g., `File`, `InputStream`, or `URL`) that contains Exif metadata.
- **Preconditions:** The method must be called on a `Thumbnails.Builder` instance before the terminal output operation (e.g., `toFile`, `asBufferedImage`).

### Output
Returns `Builder<T>` — A reference to the current `Thumbnails.Builder` instance, allowing for further method chaining in the fluent pipeline.

### Valid Call Patterns
```java
// Inferred from API documentation
Thumbnails.of("image.jpg")
    .useExifOrientation(true)
    .size(200, 200)
    .toFile("thumbnail.jpg");
```

### LLM Instruction Prompt
- When generating code, ensure `useExifOrientation` is called only once per builder instance.
- Chain this method directly after the `Thumbnails.of(...)` entry point to ensure the orientation logic is applied during the processing pipeline.

### Prompt Snippet
```text
// Enable Exif orientation handling
.useExifOrientation(true)
```

### Common Failure Modes
- **IllegalStateException:** Thrown if `useExifOrientation` is called more than once on the same `Builder` instance.
- **Metadata Absence:** If the source image lacks Exif orientation tags, enabling this feature will have no effect on the output image.
- **Pipeline Order:** While it can be placed anywhere in the chain, it is best practice to define it early to ensure the builder state is correctly configured before transformation.

### Fix Code Hint
```java
// Ensure the method is called exactly once
Thumbnails.of(source)
    .useExifOrientation(true) // Correct
    .size(100, 100)
    .toFile(destination);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.useOriginalFormat`

### Signature
```java
():Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1680_

### Goal
Configures the thumbnail generation task to output the resulting image in the same format as the source image.

### Parameters
_None._

### Input
The caller must have an active `Thumbnails.Builder` instance. This method does not require specific input parameters, but it assumes the source image format is readable by the Java Image I/O API.

### Output
Returns `Builder<T>` — A reference to the current `Builder` instance, allowing for further method chaining in the fluent pipeline.

### Valid Call Patterns
```java
Thumbnails.of("input.png")
    .size(100, 100)
    .useOriginalFormat()
    .toFile("output");
```
*(Note: Example inferred from API signature and documentation.)*

### LLM Instruction Prompt
- When generating code using `useOriginalFormat()`, ensure it is chained within a `Thumbnails.Builder` pipeline.
- Do not call this method more than once in a single chain, as it will trigger an `IllegalStateException`.
- Use this method when the desired output format must match the source file format (e.g., preserving PNG transparency or JPEG compression).

### Prompt Snippet
```text
// Example of using useOriginalFormat in a pipeline
Thumbnails.of(sourceFile)
    .size(width, height)
    .useOriginalFormat()
    .toFile(destinationFile);
```

### Common Failure Modes
- **IllegalStateException:** Occurs if `useOriginalFormat()` is called more than once on the same `Builder` instance.
- **Format Incompatibility:** If the source format is not supported by the Java Image I/O writer, the final output operation may fail.
- **Pipeline Conflict:** Calling `useOriginalFormat()` while also calling `outputFormat(String)` may lead to ambiguous behavior; prioritize one or the other.

### Fix Code Hint
```java
// Ensure only one format-setting method is used
Thumbnails.of(file)
    .size(100, 100)
    .useOriginalFormat() // Do not add .outputFormat("jpg") here
    .toFile(output);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.watermark`

### Signature
```java
(Position position,BufferedImage image,float opacity,int insets):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:1944_

### Goal
Applies a watermark image to the thumbnail at a specified position with defined opacity and inset padding.

### Parameters
- `position` (`Position`): The geometric placement of the watermark (e.g., `Positions.CENTER`, `Positions.BOTTOM_RIGHT`).
- `image` (`BufferedImage`): The source image to be used as the watermark.
- `opacity` (`float`): The transparency level of the watermark, ranging from `0.0f` (completely transparent) to `1.0f` (completely opaque).
- `insets` (`int`): The padding size in pixels to apply around the watermark; must be a non-negative integer.

### Input
The caller must provide a valid `BufferedImage` for the watermark. The `position` should be an implementation of the `Position` interface (typically from the `Positions` enum). The `opacity` must be within the `[0.0f, 1.0f]` range, and `insets` must be `0` or greater. This method can be chained multiple times to apply multiple watermarks in the order they are called.

### Output
Returns `Builder<T>` — The fluent builder instance, allowing for further configuration or terminal output operations.

### Valid Call Patterns
```java
// Inferred from signature and documentation
Thumbnails.of("input.jpg")
    .size(200, 200)
    .watermark(Positions.BOTTOM_RIGHT, watermarkImage, 0.5f, 10)
    .toFile("output.jpg");
```

### LLM Instruction Prompt
- When calling `watermark`, ensure the `opacity` is a `float` between `0.0f` and `1.0f`.
- Ensure `insets` is a non-negative `int`.
- Use `Positions` enum constants for the `position` argument.
- Chain this method within a `Thumbnails.of(...)` pipeline.

### Prompt Snippet
```text
.watermark(Positions.BOTTOM_RIGHT, watermarkImage, 0.5f, 10)
```

### Common Failure Modes
- **Negative Insets:** Passing a negative value for `insets` will result in an `IllegalArgumentException`.
- **Invalid Opacity:** Values outside the `0.0f` to `1.0f` range may cause unexpected rendering or exceptions depending on the underlying implementation.
- **Null Arguments:** Passing `null` for `position` or `image` will result in a `NullPointerException`.

### Fix Code Hint
```java
// Ensure insets are non-negative and opacity is within bounds
int padding = Math.max(0, userProvidedPadding);
float alpha = Math.min(1.0f, Math.max(0.0f, userProvidedOpacity));
builder.watermark(Positions.BOTTOM_RIGHT, watermarkImage, alpha, padding);
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.Builder.width`

### Signature
```java
(int width):Builder<T>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:872_

### Goal
Sets the target width for the thumbnail, automatically calculating the height to preserve the original image's aspect ratio.

### Parameters
- `width` (`int`): The desired width of the thumbnail in pixels. Must be a positive integer.

### Input
The caller must have an active `Thumbnails.Builder` instance initiated via `Thumbnails.of(...)`. This method is a configuration step in the fluent pipeline and must be called before the terminal output method (e.g., `toFile()`, `asBufferedImage()`).

### Output
Returns `Builder<T>` — A reference to the current `Builder` instance, allowing for further method chaining in the fluent API.

### Valid Call Patterns
```java
Thumbnails.of("input.jpg")
    .width(200)
    .toFile("thumbnail.jpg");
```

### LLM Instruction Prompt
- Use `width(int)` only when you want to constrain the image by width while maintaining the original aspect ratio.
- Do not use `width(int)` in conjunction with `size(int, int)` or `scale(double)` in the same builder chain, as this will trigger an `IllegalStateException`.
- Ensure the builder is initialized with a valid source (file, stream, or `BufferedImage`) before calling this method.

### Prompt Snippet
```text
// Example of setting width in a fluent pipeline
Thumbnails.of(sourceImage)
    .width(300)
    .toFile(outputFile);
```

### Common Failure Modes
- **IllegalStateException**: Occurs if `width()` is called after `size()` or `scale()`, or if `width()` is called more than once on the same builder instance.
- **Invalid Dimensions**: Providing a non-positive integer for `width` may result in runtime errors during the processing phase.
- **Pipeline Misuse**: Attempting to call `width()` on a builder that has already been terminated by an output method (e.g., `toFile()`).

### Fix Code Hint
```java
// Correct: Use width() exclusively for dimension constraints
Thumbnails.of("image.jpg")
    .width(150)
    .toFile("thumb.jpg");

// Incorrect: Mixing size() and width()
Thumbnails.of("image.jpg")
    .size(100, 100)
    .width(150) // Throws IllegalStateException
    .toFile("thumb.jpg");
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.fromFilenames`

### Signature
```java
(Iterable<String> files):Builder<File>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:292_

### Goal
Initializes a fluent thumbnail generation pipeline for a collection of image files specified by their filesystem paths.

### Parameters
- `files` (`Iterable<String>`): An iterable collection of strings representing the absolute or relative paths to the source image files.

### Input
- **Data:** A collection of file paths (e.g., `List<String>` or `Set<String>`).
- **Formats:** Supports any image format recognized by the underlying Java Image I/O API.
- **Preconditions:** The provided `Iterable` must not be `null` and must not be empty. The files referenced by the strings must exist and be readable by the application.

### Output
Returns `Builder<File>` — A fluent builder object configured to process the specified files, allowing for further chaining of transformation methods (e.g., `.size()`, `.rotate()`) and terminal output operations (e.g., `.toFiles()`).

### Valid Call Patterns
```java
List<String> filePaths = Arrays.asList("image1.jpg", "image2.jpg");
Thumbnails.fromFilenames(filePaths)
    .size(200, 200)
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

### LLM Instruction Prompt
- Use `net.coobird.thumbnailator.Thumbnails.fromFilenames` when the input source is a collection of file paths as `String` objects.
- Always chain this method with a terminal operation like `.toFiles()` or `.asFiles()` to execute the pipeline.
- Ensure the input `Iterable` is non-empty to avoid runtime exceptions.

### Prompt Snippet
```text
Thumbnails.fromFilenames(listOfFilePaths)
    .size(width, height)
    .toFiles(renameStrategy);
```

### Common Failure Modes
- **`NullPointerException`**: Thrown if the `files` argument is `null`.
- **`IllegalArgumentException`**: Thrown if the `files` collection is empty.
- **`IOException`**: Occurs if the specified file paths are invalid, inaccessible, or if the files are not valid image formats supported by the JRE.
- **`IllegalStateException`**: Thrown if conflicting transformation methods (e.g., `scale()` and `size()`) are called on the same builder instance.

### Fix Code Hint
```java
// Ensure the list is populated and paths are valid before passing to the builder
if (filePaths != null && !filePaths.isEmpty()) {
    Thumbnails.fromFilenames(filePaths)
        .size(100, 100)
        .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
}
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.fromFiles`

### Signature
```java
(Iterable<File> files):Builder<File>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:309_

### Goal
Initializes a fluent thumbnail generation pipeline for a collection of image files, allowing for batch processing and transformation of multiple source images.

### Parameters
- `files` (`Iterable<File>`): An `Iterable` collection of `File` objects representing the source images to be processed.

### Input
- **Data:** Must be a non-null, non-empty `Iterable` (e.g., `List<File>` or `Set<File>`) containing valid image files.
- **Formats:** Supports any image format recognized by the underlying Java Image I/O API.
- **Preconditions:** The files must exist and be readable by the application. If the collection is empty, an `IllegalArgumentException` is thrown. If the argument is `null`, a `NullPointerException` is thrown.

### Output
Returns `Builder<File>` — A fluent builder object used to configure subsequent operations (e.g., `.size()`, `.rotate()`, `.watermark()`) and define the destination for the processed thumbnails.

### Valid Call Patterns
```java
// Processing a list of files from a directory
List<File> imageFiles = Arrays.asList(new File("img1.jpg"), new File("img2.jpg"));
Thumbnails.fromFiles(imageFiles)
    .size(200, 200)
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

### LLM Instruction Prompt
- Use `net.coobird.thumbnailator.Thumbnails.fromFiles` when the input is a collection of `File` objects.
- Always chain this method with a terminal output method like `.toFiles()` or `.asFiles()` to execute the pipeline.
- Ensure the input `Iterable` is not empty to avoid runtime exceptions.

### Prompt Snippet
```text
Thumbnails.fromFiles(myFileCollection)
    .size(width, height)
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

### Common Failure Modes
- **`NullPointerException`**: Occurs if the passed `Iterable` is `null`.
- **`IllegalArgumentException`**: Occurs if the passed `Iterable` is empty.
- **`IllegalStateException`**: Occurs if conflicting sizing methods (e.g., `size()` and `scale()`) are called on the resulting builder.
- **I/O Errors**: Occurs if the files in the collection are inaccessible, locked, or not valid image formats supported by the JRE.

### Fix Code Hint
```java
// Ensure the collection is populated and non-null before passing
if (files != null && files.iterator().hasNext()) {
    Thumbnails.fromFiles(files).size(100, 100).toFiles(Rename.NO_CHANGE);
}
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.fromImages`

### Signature
```java
(Iterable<BufferedImage> images):Builder<BufferedImage>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:364_

### Goal
Initiates a fluent thumbnail generation pipeline for a collection of in-memory `BufferedImage` objects.

### Parameters
- `images` (`Iterable<BufferedImage>`): An iterable collection of `BufferedImage` objects to be processed as sources for thumbnail generation.

### Input
- **Data:** Must provide an `Iterable` (e.g., `List`, `Set`) containing one or more `BufferedImage` instances.
- **Preconditions:** The collection must not be `null` and must not be empty.
- **Environment:** Designed for headless environments; ensures no UI components are invoked during processing.

### Output
Returns `Builder<BufferedImage>` — A fluent builder object used to configure subsequent transformation parameters (e.g., `.size()`, `.rotate()`, `.watermark()`) and define the terminal output destination.

### Valid Call Patterns
```java
List<BufferedImage> images = Arrays.asList(img1, img2);
Thumbnails.fromImages(images)
    .size(100, 100)
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

### LLM Instruction Prompt
- Use `Thumbnails.fromImages(Iterable<BufferedImage>)` when the source images are already loaded into memory as `BufferedImage` objects.
- Always chain this call with a size or scaling method (e.g., `.size()`, `.scale()`) before invoking a terminal output method (e.g., `.toFile()`, `.asBufferedImage()`).
- Ensure the input collection is not empty to avoid runtime exceptions.

### Prompt Snippet
```text
Thumbnails.fromImages(myBufferedImageList)
    .size(width, height)
    .toFile(destinationFile);
```

### Common Failure Modes
- **`NullPointerException`**: Occurs if the `images` argument passed to the method is `null`.
- **`IllegalArgumentException`**: Occurs if the provided `Iterable` is empty.
- **`IllegalStateException`**: Occurs if conflicting sizing methods (e.g., both `size()` and `scale()`) are called on the resulting builder.
- **Memory Exhaustion**: Processing a very large `Iterable` of high-resolution `BufferedImage` objects may lead to `OutOfMemoryError`; consider processing in smaller batches if memory is constrained.

### Fix Code Hint
```java
// Ensure the collection is populated and non-null
if (images != null && images.iterator().hasNext()) {
    Thumbnails.fromImages(images)
        .size(200, 200)
        .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
}
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.fromInputStreams`

### Signature
```java
(Iterable<? extends InputStream> inputStreams):Builder<InputStream>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:347_

### Goal
Initializes a fluent thumbnail generation pipeline for a collection of image data streams, allowing for batch processing of images provided via `InputStream` objects.

### Parameters
- `inputStreams` (`Iterable<? extends InputStream>`): An iterable collection of `InputStream` objects, each containing the raw data of an image to be processed.

### Input
- **Data:** The `InputStream` objects must point to valid image data in a format supported by the underlying Java Image I/O API.
- **Preconditions:** The `Iterable` must not be `null` and must not be empty.
- **Lifecycle:** The library does not close the provided `InputStream` objects after reading; the caller is responsible for managing the lifecycle and closing these streams.

### Output
Returns `Builder<InputStream>` — A fluent builder object that allows for the configuration of thumbnail parameters (e.g., size, rotation, watermarking) and the specification of the output destination.

### Valid Call Patterns
```java
List<InputStream> streams = Arrays.asList(new FileInputStream("a.jpg"), new FileInputStream("b.jpg"));
Thumbnails.fromInputStreams(streams)
    .size(100, 100)
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

### LLM Instruction Prompt
- Use `Thumbnails.fromInputStreams` when processing multiple image sources provided as a collection of `InputStream` objects.
- Always chain the builder methods (e.g., `.size()`, `.toFiles()`) directly to the call.
- Ensure the `Iterable` passed is not empty to avoid runtime exceptions.
- Remember that the library will not close the streams; ensure your code handles stream closure if necessary.

### Prompt Snippet
```text
Thumbnails.fromInputStreams(myInputStreamIterable)
    .size(width, height)
    .toFiles(renameStrategy);
```

### Common Failure Modes
- **`NullPointerException`**: Occurs if the `inputStreams` argument is `null`.
- **`IllegalArgumentException`**: Occurs if the provided `Iterable` is empty.
- **`IOException`**: May occur if the `InputStream` is closed prematurely or contains invalid image data that the Java Image I/O API cannot decode.
- **Resource Leaks**: Failing to close the `InputStream` objects after the thumbnail task is complete, as Thumbnailator does not perform this cleanup.

### Fix Code Hint
```java
// Ensure the collection is not empty and handle stream closure
List<InputStream> streams = ...;
if (streams != null && !streams.isEmpty()) {
    try {
        Thumbnails.fromInputStreams(streams).size(100, 100).toFiles(Rename.PREFIX_DOT_THUMBNAIL);
    } finally {
        for (InputStream is : streams) {
            is.close();
        }
    }
}
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.fromURLs`

### Signature
```java
(Iterable<URL> urls):Builder<URL>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:326_

### Goal
Initializes a fluent thumbnail generation pipeline for a collection of images retrieved from the provided `URL` sources.

### Parameters
- `urls` (`Iterable<URL>`): An iterable collection of `java.net.URL` objects pointing to the source images to be processed.

### Input
- **Data:** A collection of `URL` objects.
- **Formats:** Supports any image format recognized by the underlying Java Image I/O API.
- **Preconditions:** The `Iterable` must not be `null` and must not be empty. The environment must allow network access if the URLs point to remote resources, though the library itself is designed for headless operation.

### Output
Returns `Builder<URL>` — A fluent builder instance configured to process the specified URLs, allowing for subsequent chaining of transformation methods (e.g., `.size()`, `.rotate()`) and terminal output operations (e.g., `.toFiles()`).

### Valid Call Patterns
```java
List<URL> urlList = Arrays.asList(new URL("http://example.com/image1.jpg"), new URL("http://example.com/image2.jpg"));
Thumbnails.fromURLs(urlList)
    .size(160, 160)
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

### LLM Instruction Prompt
- Use `Thumbnails.fromURLs(Iterable<URL>)` as the entry point when the source images are identified by a collection of URLs.
- Always chain this call with a size or scaling method (e.g., `.size(w, h)` or `.scale(s)`) and a terminal output method (e.g., `.toFiles()`, `.toOutputStream()`, or `.asBufferedImages()`).
- Ensure the input collection is not empty to avoid runtime exceptions.

### Prompt Snippet
```text
Thumbnails.fromURLs(myUrlCollection)
    .size(width, height)
    .toFiles(renameStrategy);
```

### Common Failure Modes
- **`NullPointerException`**: Thrown if the `urls` argument is `null`.
- **`IllegalArgumentException`**: Thrown if the `urls` collection is empty.
- **`IOException`**: Occurs if the provided URLs are unreachable, invalid, or point to non-image resources that the Java Image I/O API cannot decode.
- **`IllegalStateException`**: Thrown if multiple conflicting sizing methods (e.g., both `size()` and `scale()`) are called on the builder.

### Fix Code Hint
```java
// Ensure the collection is populated and non-null before passing to the builder
if (urlCollection != null && !urlCollection.isEmpty()) {
    Thumbnails.fromURLs(urlCollection)
        .size(100, 100)
        .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
}
```

## API Test: `java:net.coobird.thumbnailator.Thumbnails.of`

### Signature
```java
(String... files):Builder<File>
```
_Source: source/src/main/java/net/coobird/thumbnailator/Thumbnails.java:208_

### Goal
Initiates a fluent thumbnail generation pipeline by specifying one or more image file paths as the source for processing.

### Parameters
- `files` (`String...`): A variable-length array of strings representing the filesystem paths of the image files to be processed.

### Input
- **Data:** Valid filesystem paths to image files.
- **Formats:** Supports any image format recognized by the underlying Java Image I/O API.
- **Preconditions:** The provided strings must point to existing, readable image files. The array must not be null and must contain at least one element.

### Output
Returns `Builder<File>` — A fluent builder object that allows for the chaining of transformation methods (e.g., `.size()`, `.rotate()`, `.watermark()`) and terminal output operations (e.g., `.toFile()`, `.toFiles()`).

### Valid Call Patterns
```java
// Basic single file processing
Thumbnails.of("path/to/image.jpg")
    .size(200, 200)
    .toFile("path/to/thumbnail.jpg");

// Batch processing multiple files
Thumbnails.of("image1.jpg", "image2.jpg")
    .size(100, 100)
    .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
```

### LLM Instruction Prompt
- Use `net.coobird.thumbnailator.Thumbnails.of` as the entry point for file-based thumbnail tasks.
- Always chain a terminal operation (e.g., `.toFile()`, `.toFiles()`, `.asFiles()`) to the builder returned by this method.
- Ensure the input array is not empty to avoid `IllegalArgumentException`.

### Prompt Snippet
```text
// Example of starting a pipeline with file paths
Thumbnails.of("input.jpg")
    .size(160, 160)
    .toFile("output.jpg");
```

### Common Failure Modes
- **`NullPointerException`**: Occurs if the `files` argument passed to the method is `null`.
- **`IllegalArgumentException`**: Occurs if an empty array is passed to the method.
- **`IllegalStateException`**: Occurs if conflicting transformation methods (e.g., calling both `scale()` and `size()`) are chained on the returned builder.
- **`IOException`**: Occurs if the specified file paths are invalid, inaccessible, or if the file format is not supported by the JRE's Image I/O providers.

### Fix Code Hint
```java
// Ensure paths are valid and the array is not empty
String[] paths = {"img1.jpg", "img2.jpg"};
if (paths != null && paths.length > 0) {
    Thumbnails.of(paths)
        .size(200, 200)
        .toFiles(Rename.PREFIX_DOT_THUMBNAIL);
}
```

## API Test: `java:net.coobird.thumbnailator.builders.BufferedImageBuilder`

### Signature
```java
(int width,int height,int imageType):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/BufferedImageBuilder.java:99_

### Goal
Instantiates a `BufferedImageBuilder` to facilitate the creation of `BufferedImage` instances with specific dimensions and image types for use in image processing tasks.

### Parameters
- `width` (`int`): The width of the `BufferedImage` to be built.
- `height` (`int`): The height of the `BufferedImage` to be built.
- `imageType` (`int`): The image type of the `BufferedImage` (e.g., `BufferedImage.TYPE_INT_ARGB`), as defined in the `java.awt.image.BufferedImage` class.

### Input
The caller must provide valid integer dimensions (width and height > 0) and a valid `imageType` constant from `java.awt.image.BufferedImage`. This builder is typically used as a precursor to calling the `.build()` method to obtain the actual `BufferedImage` object.

### Output
Returns `null` (the constructor itself does not return the image; the `build()` method on the resulting `BufferedImageBuilder` instance returns the `BufferedImage`).

### Valid Call Patterns
```java
BufferedImage sourceImage = new net.coobird.thumbnailator.builders.BufferedImageBuilder(400, 400, java.awt.image.BufferedImage.TYPE_INT_ARGB).build();
```

### LLM Instruction Prompt
- When using `BufferedImageBuilder`, always follow the constructor call with a call to the `.build()` method to retrieve the `BufferedImage` instance.
- Ensure the `imageType` provided matches the requirements of the intended image processing task (e.g., use `TYPE_INT_ARGB` if transparency is required).

### Prompt Snippet
```text
net.coobird.thumbnailator.builders.BufferedImageBuilder builder = new net.coobird.thumbnailator.builders.BufferedImageBuilder(width, height, imageType);
BufferedImage image = builder.build();
```

### Common Failure Modes
- Providing non-positive values for `width` or `height`, which may result in an `IllegalArgumentException` during the `build()` phase.
- Using an invalid or unsupported `imageType` constant, which may cause issues when the `BufferedImage` is later used in `Graphics2D` operations or `ImageIO` writes.
- Attempting to use the builder without calling `.build()`, resulting in a reference to the builder object rather than the image.

### Fix Code Hint
```java
// Ensure dimensions are positive and type is valid
int width = 200;
int height = 200;
int type = java.awt.image.BufferedImage.TYPE_INT_RGB;
BufferedImage img = new net.coobird.thumbnailator.builders.BufferedImageBuilder(width, height, type).build();
```

## API Test: `java:net.coobird.thumbnailator.builders.BufferedImageBuilder.build`

### Signature
```java
():BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/BufferedImageBuilder.java:110_

### Goal
Generates a new `BufferedImage` instance based on the dimensions and image type parameters previously configured in the `BufferedImageBuilder`.

### Parameters
_None._

### Input
The caller must have previously configured the `BufferedImageBuilder` instance using its setter methods (e.g., `width(int)`, `height(int)`, `imageType(int)`) before invoking `build()`.

### Output
Returns `BufferedImage` — A newly instantiated `BufferedImage` object created according to the specifications defined in the builder.

### Valid Call Patterns
```java
BufferedImage sourceImage = new BufferedImageBuilder(400, 400).build();
```

### LLM Instruction Prompt
- When using `BufferedImageBuilder.build()`, ensure the builder has been initialized with dimensions (width and height) and an optional image type.
- Do not attempt to pass arguments to the `build()` method, as it takes no parameters.
- Use this method only when you require a blank `BufferedImage` for manual manipulation or as a destination for `Resizer` operations.

### Prompt Snippet
```text
// Example of creating a BufferedImage using the builder
BufferedImage img = new net.coobird.thumbnailator.builders.BufferedImageBuilder(width, height)
    .imageType(BufferedImage.TYPE_INT_ARGB)
    .build();
```

### Common Failure Modes
- **Uninitialized Builder:** Calling `build()` without setting dimensions may result in a `BufferedImage` with default or zero-sized dimensions, which can cause `IllegalArgumentException` or `RasterFormatException` in subsequent image processing tasks.
- **Invalid Image Type:** Providing an unsupported or invalid integer constant for the image type may lead to unexpected behavior during rendering or saving.

### Fix Code Hint
```java
// Ensure dimensions are set before building
BufferedImageBuilder builder = new BufferedImageBuilder();
builder.width(200);
builder.height(200);
builder.imageType(BufferedImage.TYPE_INT_RGB);
BufferedImage img = builder.build();
```

## API Test: `java:net.coobird.thumbnailator.builders.BufferedImageBuilder.height`

### Signature
```java
(int height):BufferedImageBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/BufferedImageBuilder.java:167_

### Goal
Sets the vertical dimension (height) for the `BufferedImage` instance being constructed by the `BufferedImageBuilder`.

### Parameters
- `height` (`int`): The height of the image to be created, measured in pixels.

### Input
The caller must provide an integer representing the desired pixel height. This method is typically called as part of a fluent configuration chain on a `BufferedImageBuilder` instance before the `build()` method is invoked.

### Output
Returns `BufferedImageBuilder` — The current `BufferedImageBuilder` instance, allowing for further method chaining (e.g., setting the width or image type).

### Valid Call Patterns
```java
// Inferred from API usage patterns
BufferedImage image = new BufferedImageBuilder()
    .width(200)
    .height(200)
    .build();
```

### LLM Instruction Prompt
- Use this method only when configuring a `BufferedImageBuilder` instance.
- Ensure the `height` value is a positive integer.
- Chain this method with `width(int)` and optionally `imageType(int)` before calling `build()` to ensure the resulting `BufferedImage` is fully defined.

### Prompt Snippet
```text
new BufferedImageBuilder().height(int_value)
```

### Common Failure Modes
- **Negative or Zero Height:** Providing a non-positive integer may result in an `IllegalArgumentException` or an invalid `BufferedImage` state during the `build()` phase.
- **Incomplete Configuration:** Calling `build()` without setting both `width` and `height` may result in an image with default or invalid dimensions depending on the builder's internal state.
- **Type Mismatch:** Passing a non-integer type will cause a compilation error.

### Fix Code Hint
```java
// Ensure dimensions are set before building
BufferedImage img = new BufferedImageBuilder()
    .width(800)
    .height(600)
    .build();
```

## API Test: `java:net.coobird.thumbnailator.builders.BufferedImageBuilder.imageType`

### Signature
```java
(int imageType):BufferedImageBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/BufferedImageBuilder.java:122_

### Goal
Sets the image type for the `BufferedImage` to be constructed by the `BufferedImageBuilder`.

### Parameters
- `imageType` (`int`): The integer constant representing the image type (e.g., `BufferedImage.TYPE_INT_ARGB`, `BufferedImage.TYPE_INT_RGB`). If `BufferedImage.TYPE_CUSTOM` is provided, it will be substituted with `BufferedImage.TYPE_INT_ARGB`.

### Input
The caller must provide a valid `int` constant corresponding to a `BufferedImage` type. This method is typically called as part of a builder chain before invoking `.build()`.

### Output
Returns `BufferedImageBuilder` — The current builder instance, allowing for further configuration or the final call to `.build()`.

### Valid Call Patterns
```java
BufferedImage image = new BufferedImageBuilder(200, 200)
    .imageType(BufferedImage.TYPE_INT_ARGB)
    .build();
```

### LLM Instruction Prompt
- When using `BufferedImageBuilder`, always chain the `imageType` method before calling `build()` if a specific color model or transparency support is required.
- Ensure the `int` passed to `imageType` is a valid constant from `java.awt.image.BufferedImage`.

### Prompt Snippet
```text
// Example of configuring a BufferedImageBuilder with a specific image type
BufferedImageBuilder builder = new BufferedImageBuilder(width, height);
builder.imageType(BufferedImage.TYPE_INT_RGB);
BufferedImage img = builder.build();
```

### Common Failure Modes
- Passing an invalid integer that does not correspond to a valid `BufferedImage` type constant, which may lead to unexpected behavior during the `build()` phase.
- Assuming `BufferedImage.TYPE_CUSTOM` will be preserved; the library explicitly overrides this to `BufferedImage.TYPE_INT_ARGB`.

### Fix Code Hint
```java
// Use standard constants instead of custom integers
builder.imageType(BufferedImage.TYPE_INT_RGB); 
```

## API Test: `java:net.coobird.thumbnailator.builders.BufferedImageBuilder.size`

### Signature
```java
(int width,int height):BufferedImageBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/BufferedImageBuilder.java:138_

### Goal
Sets the target dimensions for a `BufferedImage` instance being constructed by the `BufferedImageBuilder`.

### Parameters
- `width` (`int`): The desired width of the image in pixels.
- `height` (`int`): The desired height of the image in pixels.

### Input
The caller must provide two positive integer values representing the pixel dimensions. This method is typically called as part of a fluent configuration chain on a `BufferedImageBuilder` instance before invoking the `build()` method.

### Output
Returns `BufferedImageBuilder` — The current `BufferedImageBuilder` instance, allowing for further configuration or the final construction of the image.

### Valid Call Patterns
```java
BufferedImage image = new BufferedImageBuilder(400, 400)
    .size(200, 200)
    .build();
```

### LLM Instruction Prompt
- Use `BufferedImageBuilder.size(int, int)` only when configuring a new `BufferedImage` instance.
- Ensure the `BufferedImageBuilder` is instantiated before calling `size`.
- Always chain `size` before calling `build()` to ensure the dimensions are applied to the resulting image.

### Prompt Snippet
```text
// Example of setting dimensions for a new BufferedImage
BufferedImageBuilder builder = new BufferedImageBuilder();
builder.size(width, height);
BufferedImage img = builder.build();
```

### Common Failure Modes
- **Invalid Dimensions:** Providing negative or zero values for width or height may result in an `IllegalArgumentException` during the `build()` phase.
- **Missing Build:** Calling `size()` without subsequently calling `build()` will result in the builder configuration being ignored and no image being created.
- **Re-use:** Attempting to reuse a `BufferedImageBuilder` instance for multiple different sizes without proper reset logic may lead to unexpected state if the builder is not handled as a transient configuration object.

### Fix Code Hint
```java
// Ensure dimensions are positive and build is called
BufferedImage img = new BufferedImageBuilder()
    .size(800, 600)
    .build();
```

## API Test: `java:net.coobird.thumbnailator.builders.BufferedImageBuilder.width`

### Signature
```java
(int width):BufferedImageBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/BufferedImageBuilder.java:150_

### Goal
Sets the width dimension for a `BufferedImage` being constructed by the `BufferedImageBuilder`.

### Parameters
- `width` (`int`): The desired width of the image in pixels; must be a positive integer.

### Input
The caller must have an initialized `BufferedImageBuilder` instance. This method is typically called as part of a fluent configuration chain before invoking the `build()` method to instantiate the `BufferedImage`.

### Output
Returns `BufferedImageBuilder` — The current `BufferedImageBuilder` instance, allowing for further method chaining (e.g., setting height or image type).

### Valid Call Patterns
```java
// Inferred from signature and builder pattern usage
BufferedImage image = new BufferedImageBuilder()
    .width(200)
    .height(200)
    .build();
```

### LLM Instruction Prompt
- Use `java:net.coobird.thumbnailator.builders.BufferedImageBuilder.width` only when configuring a `BufferedImageBuilder` instance.
- Ensure the `width` argument is a positive integer.
- Always chain this method with other builder configuration methods (like `height`) before calling `build()` to ensure the resulting `BufferedImage` has valid dimensions.

### Prompt Snippet
```text
BufferedImageBuilder builder = new BufferedImageBuilder();
builder.width(targetWidth);
```

### Common Failure Modes
- Passing a negative or zero value for `width`, which may result in an `IllegalArgumentException` or an invalid `BufferedImage` upon calling `build()`.
- Attempting to use the builder without setting the height, which may result in a default or invalid image dimension depending on the `BufferedImageBuilder` implementation state.

### Fix Code Hint
```java
// Ensure dimensions are positive
int w = Math.max(1, requestedWidth);
BufferedImage img = new BufferedImageBuilder()
    .width(w)
    .height(h)
    .build();
```

## API Test: `java:net.coobird.thumbnailator.builders.ThumbnailParameterBuilder`

### Signature
```java
():null
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:105_

### Goal
Creates a new instance of `ThumbnailParameterBuilder`, which provides a builder interface for configuring the parameters of a thumbnail generation task.

### Parameters
_None._

### Input
No input parameters are required to instantiate the builder. However, the resulting builder instance is intended to be used to configure a `ThumbnailParameter` object, which holds the settings for a thumbnail task.

### Output
Returns `null` — The constructor returns an instance of `ThumbnailParameterBuilder`. (Note: The signature indicates `null` as the return type in the provided metadata, but in Java, this constructor returns a `ThumbnailParameterBuilder` object).

### Valid Call Patterns
```java
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
```

### LLM Instruction Prompt
- Use `new ThumbnailParameterBuilder()` to initiate the configuration of a `ThumbnailParameter` object.
- Do not reuse the builder instance across multiple thumbnail tasks, as `ThumbnailParameter` is mutable and not thread-safe.
- Chain configuration methods provided by the builder to define resizing, filtering, and output settings before finalizing the parameter object.

### Prompt Snippet
```text
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
// Configure the builder using its methods
ThumbnailParameter param = builder.build();
```

### Common Failure Modes
- **Thread Safety:** Attempting to share a single `ThumbnailParameterBuilder` or the resulting `ThumbnailParameter` across multiple threads will lead to race conditions and unpredictable results.
- **State Reuse:** Reusing the same builder instance for multiple tasks can lead to configuration leakage between tasks. Always instantiate a new builder for each distinct thumbnail operation.
- **Incomplete Configuration:** Calling `build()` on a builder that has not had its required parameters (like size or scaling) set may result in an `IllegalStateException` or invalid thumbnail output.

### Fix Code Hint
```java
// Correct usage: Instantiate, configure, and build for a single task
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.size(200, 200);
ThumbnailParameter param = builder.build();
```

## API Test: `java:net.coobird.thumbnailator.builders.ThumbnailParameterBuilder.build`

### Signature
```java
():ThumbnailParameter
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:362_

### Goal
Constructs a `ThumbnailParameter` object containing the configuration settings defined within the `ThumbnailParameterBuilder` instance.

### Parameters
_None._

### Input
The builder must have its state configured prior to calling `build()`. Specifically, the caller must have defined either a target size (via `size()`) or a scaling factor (via `scale()`). The `ThumbnailParameter` object is mutable and intended for single-task use; it should not be reused across multiple thumbnail generation tasks.

### Output
Returns `ThumbnailParameter` — An object encapsulating the complete set of parameters (such as dimensions, scaling, filters, and output quality) required to execute a thumbnail generation task.

### Valid Call Patterns
```java
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.size(200, 200);
ThumbnailParameter param = builder.build();
```

### LLM Instruction Prompt
- When using `ThumbnailParameterBuilder.build()`, ensure that the builder has been initialized with either a fixed size or a scaling factor.
- Do not reuse the resulting `ThumbnailParameter` instance for multiple tasks, as it is not thread-safe and is designed for a single-use lifecycle.
- Always verify that the builder configuration is complete before invoking `build()`, as the method will throw an `IllegalStateException` if the required dimensions are missing.

### Prompt Snippet
```text
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
// Configure builder (e.g., .size(w, h) or .scale(s))
ThumbnailParameter params = builder.build();
```

### Common Failure Modes
- **`IllegalStateException`**: Occurs if neither the size nor the scaling factor has been set on the builder prior to calling `build()`.
- **`IllegalArgumentException`**: Occurs if required parameters for the `ThumbnailParameter` have not been set.
- **Thread Safety**: Attempting to share or reuse a `ThumbnailParameter` instance across concurrent threads or multiple processing tasks will lead to unpredictable behavior.

### Fix Code Hint
```java
// Ensure size or scale is set before building
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.size(100, 100); 
ThumbnailParameter params = builder.build();
```

## API Test: `java:net.coobird.thumbnailator.builders.ThumbnailParameterBuilder.filters`

### Signature
```java
(List<ImageFilter> filters):ThumbnailParameterBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:267_

### Goal
Sets the list of `ImageFilter` objects to be applied to the thumbnail image after the resizing process is complete.

### Parameters
- `filters` (`List<ImageFilter>`): A list of image processing filters (e.g., `Watermark`, `Caption`, `Rotation`, `Flip`, `Colorize`, `Transparency`) to be applied sequentially to the image.

### Input
- **Data:** A `List` containing implementations of the `ImageFilter` interface.
- **Preconditions:** The `ThumbnailParameterBuilder` instance should be configured with necessary resizing parameters before or alongside filter application. Filters are applied only after the original image has been resized.
- **Formats:** The filters operate on the `BufferedImage` resulting from the resizing step.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, allowing for method chaining.

### Valid Call Patterns
```java
// Example of setting filters via the builder
List<ImageFilter> myFilters = new ArrayList<>();
myFilters.add(new Watermark(Positions.BOTTOM_RIGHT, watermarkImage, 0.5f));

ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.filters(myFilters);
```

### LLM Instruction Prompt
- When using `filters(List<ImageFilter>)`, ensure the list is not null.
- Remember that these filters are applied *after* the resizing operation.
- If multiple filters are provided, they will be applied in the order they appear in the list.
- Use this method only when configuring a `ThumbnailParameter` object manually; for standard fluent API usage, prefer the `.addFilter()` method on the `Thumbnails.Builder`.

### Prompt Snippet
```text
// Applying a list of filters to a ThumbnailParameterBuilder
List<ImageFilter> filters = new ArrayList<>();
filters.add(new Rotation(90));
builder.filters(filters);
```

### Common Failure Modes
- **NullPointerException:** Passing a `null` list to the `filters` method.
- **Ordering Issues:** Expecting filters to be applied before resizing; the API explicitly applies them after the resize operation.
- **Type Mismatch:** Attempting to pass objects that do not implement the `ImageFilter` interface.

### Fix Code Hint
```java
// Ensure the list is initialized and contains valid ImageFilter implementations
List<ImageFilter> filters = new ArrayList<ImageFilter>();
filters.add(new Flip(Flip.HORIZONTAL));
builder.filters(filters);
```

## API Test: `java:net.coobird.thumbnailator.builders.ThumbnailParameterBuilder.fitWithinDimensions`

### Signature
```java
(boolean fit):ThumbnailParameterBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:330_

### Goal
Configures the `ThumbnailParameterBuilder` to determine whether the resulting thumbnail should be constrained to fit within the specified dimensions if the calculated size would otherwise exceed them.

### Parameters
- `fit` (`boolean`): Set to `true` to ensure the thumbnail dimensions do not exceed the specified bounds; set to `false` to allow the thumbnail to potentially exceed those dimensions.

### Input
The caller must provide a `boolean` value. This method is intended for use during the configuration phase of a `ThumbnailParameter` object before it is used in a thumbnail generation task.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, allowing for method chaining.

### Valid Call Patterns
```java
// Inferred from signature:
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.fitWithinDimensions(true);
```

### LLM Instruction Prompt
- When using `fitWithinDimensions`, ensure it is called on an instance of `ThumbnailParameterBuilder` before the parameter object is finalized.
- Use this method to enforce boundary constraints on thumbnail output dimensions.
- The method returns the builder instance, so it should be used in a fluent chain if applicable.

### Prompt Snippet
```text
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.fitWithinDimensions(true);
```

### Common Failure Modes
- **Reusing Builders:** `ThumbnailParameter` objects (and their builders) are not thread-safe and should not be reused across multiple thumbnail generation tasks.
- **Incorrect State:** Calling this method after the `ThumbnailParameter` has already been passed to a `ThumbnailTask` or `Thumbnails.Builder` may result in ignored configuration or unexpected behavior.
- **Logical Conflicts:** Ensure that the `fit` setting is compatible with other sizing constraints (like `size()` or `scale()`) defined in the same builder instance.

### Fix Code Hint
```java
// Ensure the builder is fresh and configuration is applied before task execution
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.size(200, 200)
       .fitWithinDimensions(true);
ThumbnailParameter param = builder.build();
```

## API Test: `java:net.coobird.thumbnailator.builders.ThumbnailParameterBuilder.format`

### Signature
```java
(String format):ThumbnailParameterBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:243_

### Goal
Sets the output image format for the thumbnail generation task, determining the encoding used when writing the resulting image.

### Parameters
- `format` (`String`): The name of the output format (e.g., "jpg", "png", "gif"). This string must correspond to a format supported by the underlying Java Image I/O API.

### Input
The caller must provide a `String` representing a valid output format. Precondition: The format must be supported by the current JRE's `ImageIO` implementation. Use `ThumbnailatorUtils.getSupportedOutputFormats()` or `ImageIO.getWriterFormatNames()` to verify availability before setting the format.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, allowing for method chaining in the fluent configuration pipeline.

### Valid Call Patterns
```java
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.format("jpg");
```

### LLM Instruction Prompt
- When using `ThumbnailParameterBuilder.format`, ensure the provided `String` is a valid format name recognized by the Java Image I/O API.
- This method is intended for use within a builder configuration chain; it returns the builder instance to allow further parameter setting.
- Do not assume all formats (like WebP or HEIF) are supported; verify support via `ImageIO.getWriterFormatNames()` if the environment is non-standard.

### Prompt Snippet
```text
// Example of setting the output format using ThumbnailParameterBuilder
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.format("png");
```

### Common Failure Modes
- **Unsupported Format:** Passing a format string not supported by the JRE (e.g., an unsupported proprietary format) will result in an `IIOException` or similar error during the final write operation.
- **Case Sensitivity:** While many `ImageIO` implementations are case-insensitive, it is best practice to use the standard lowercase format names (e.g., "jpg" instead of "JPG").
- **Pipeline Misuse:** Attempting to use this builder after the `ThumbnailParameter` has already been passed to a `ThumbnailTask` will not affect the task, as the parameter object is typically consumed during task execution.

### Fix Code Hint
```java
// Verify format support before setting
String format = "jpg";
if (Arrays.asList(ImageIO.getWriterFormatNames()).contains(format)) {
    builder.format(format);
} else {
    throw new IllegalArgumentException("Unsupported format: " + format);
}
```

## API Test: `java:net.coobird.thumbnailator.builders.ThumbnailParameterBuilder.formatType`

### Signature
```java
(String formatType):ThumbnailParameterBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:254_

### Goal
Sets the specific output format type (e.g., "JPEG", "PNG") for the thumbnail generation task within the `ThumbnailParameterBuilder`.

### Parameters
- `formatType` (`String`): The name of the image format to be used for the output (e.g., "jpg", "png", "gif").

### Input
The caller must provide a `String` representing a format supported by the underlying Java Image I/O API. Precondition: The format must be one of the formats returned by `ImageIO.getWriterFormatNames()`.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, allowing for method chaining.

### Valid Call Patterns
```java
// Inferred from API signature and builder pattern
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.formatType("jpg");
```

### LLM Instruction Prompt
- Use this method when you need to explicitly define the output format of a thumbnail task.
- Ensure the `formatType` string matches a valid format supported by the JRE's `ImageIO` implementation.
- Always chain this method with other `ThumbnailParameterBuilder` configuration methods before finalizing the task.

### Prompt Snippet
```text
// Example of setting the format type in a builder chain
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.formatType("png");
```

### Common Failure Modes
- **Unsupported Format:** Passing a format string that is not supported by the current JRE's `ImageIO` will result in an `IllegalArgumentException` or failure during the write phase.
- **Case Sensitivity:** While many `ImageIO` implementations are case-insensitive, it is best practice to use standard lowercase format names (e.g., "jpg" instead of "JPG").
- **Builder Reuse:** `ThumbnailParameterBuilder` is mutable and not thread-safe; reusing the same builder instance across multiple concurrent tasks will lead to race conditions and unpredictable output.

### Fix Code Hint
```java
// Ensure the format is supported before setting
String format = "webp";
if (Arrays.asList(ImageIO.getWriterFormatNames()).contains(format)) {
    builder.formatType(format);
} else {
    // Fallback to a safe default
    builder.formatType("jpg");
}
```

## API Test: `java:net.coobird.thumbnailator.builders.ThumbnailParameterBuilder.imageType`

### Signature
```java
(int type):ThumbnailParameterBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:113_

### Goal
Sets the `BufferedImage` type for the generated thumbnail, allowing explicit control over the color model and bit depth of the output image.

### Parameters
- `type` (`int`): An integer constant representing a `BufferedImage` type (e.g., `BufferedImage.TYPE_INT_ARGB`, `BufferedImage.TYPE_INT_RGB`).

### Input
The caller must provide an integer corresponding to a valid `BufferedImage` type constant defined in the `java.awt.image.BufferedImage` class. This method is typically called during the configuration phase of a `ThumbnailParameterBuilder` instance before the thumbnail generation task is executed.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, enabling fluent method chaining.

### Valid Call Patterns
```java
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.imageType(java.awt.image.BufferedImage.TYPE_INT_RGB);
```
*(Note: This call pattern is inferred from the API signature and standard Java Image I/O usage.)*

### LLM Instruction Prompt
- When calling `imageType`, ensure the integer provided is a valid constant from `java.awt.image.BufferedImage`.
- Use this method only when specific control over the output image's memory representation is required; otherwise, Thumbnailator defaults to an appropriate type.
- Always chain this method with other `ThumbnailParameterBuilder` configuration methods before finalizing the task.

### Prompt Snippet
```text
// Example of setting the image type to RGB
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.imageType(java.awt.image.BufferedImage.TYPE_INT_RGB);
```

### Common Failure Modes
- **Invalid Type Constants:** Passing an integer that does not map to a valid `BufferedImage` type constant may result in an `IllegalArgumentException` or unexpected behavior during the rendering phase.
- **Alpha Channel Mismatches:** Selecting an opaque type (e.g., `TYPE_INT_RGB`) for an image that requires transparency (e.g., PNG with alpha) will result in the loss of transparency data, often appearing as a black background.
- **Memory Overhead:** Choosing high-bit-depth types (e.g., `TYPE_INT_ARGB`) for simple thumbnails may unnecessarily increase memory consumption in large batch operations.

### Fix Code Hint
```java
// Ensure the type matches the source requirements
int targetType = java.awt.image.BufferedImage.TYPE_INT_ARGB;
builder.imageType(targetType);
```

## API Test: `java:net.coobird.thumbnailator.builders.ThumbnailParameterBuilder.keepAspectRatio`

### Signature
```java
(boolean keep):ThumbnailParameterBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:214_

### Goal
Configures whether the thumbnail generation process should preserve the original image's aspect ratio during resizing.

### Parameters
- `keep` (`boolean`): Set to `true` to maintain the original aspect ratio (scaling the image to fit within the specified dimensions), or `false` to force the image to the exact dimensions provided, potentially distorting the image.

### Input
- **Preconditions:** This method is used within the `ThumbnailParameterBuilder` configuration flow. The caller must have initialized a `ThumbnailParameterBuilder` instance.
- **Data:** A boolean flag indicating the desired aspect ratio behavior.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, allowing for method chaining.

### Valid Call Patterns
```java
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.keepAspectRatio(true);
```

### LLM Instruction Prompt
- When using `ThumbnailParameterBuilder`, call `keepAspectRatio(boolean)` to define scaling behavior before finalizing the `ThumbnailParameter` object. Ensure the boolean value aligns with the desired output dimensions; if `false` is passed, the resulting image will strictly match the dimensions set in the builder, regardless of the source image's proportions.

### Prompt Snippet
```text
// Example of configuring aspect ratio in a builder
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.size(200, 200).keepAspectRatio(true);
```

### Common Failure Modes
- **Logical Distortion:** Setting `keepAspectRatio(false)` when the source and target aspect ratios differ will result in a stretched or squashed image.
- **Builder Misuse:** `ThumbnailParameter` objects are mutable and not thread-safe; do not share a single builder instance across multiple threads or concurrent thumbnail tasks.
- **State Conflicts:** Ensure that other sizing parameters (like `size()`) are configured appropriately for the chosen aspect ratio setting to avoid unexpected output dimensions.

### Fix Code Hint
```java
// To ensure the image fits within 200x200 without distortion:
new ThumbnailParameterBuilder()
    .size(200, 200)
    .keepAspectRatio(true)
    .build();
```

## API Test: `java:net.coobird.thumbnailator.builders.ThumbnailParameterBuilder.quality`

### Signature
```java
(float quality):ThumbnailParameterBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:232_

### Goal
Sets the compression quality setting for the thumbnail output, allowing control over the balance between file size and image fidelity.

### Parameters
- `quality` (`float`): The compression quality setting, ranging from `0.0f` (lowest quality/highest compression) to `1.0f` (highest quality/lowest compression). Use `ThumbnailParameter.DEFAULT_QUALITY` to revert to the default setting.

### Input
The caller must provide a `float` value within the `[0.0, 1.0]` range. This method is intended for use during the configuration phase of a `ThumbnailParameterBuilder` instance before the parameters are applied to a thumbnail generation task.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, enabling fluent method chaining.

### Valid Call Patterns
```java
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.quality(0.85f);
```

### LLM Instruction Prompt
- When configuring a `ThumbnailParameterBuilder`, use the `quality` method to specify compression levels. 
- Ensure the provided `float` is between `0.0f` and `1.0f`. 
- If no specific quality is required, use the constant `ThumbnailParameter.DEFAULT_QUALITY`.
- This method is part of a builder pattern; always return the builder instance for subsequent configuration or task execution.

### Prompt Snippet
```text
// Example of setting quality in a builder chain
ThumbnailParameter param = new ThumbnailParameterBuilder()
    .quality(0.75f)
    .build();
```

### Common Failure Modes
- **Out-of-bounds values:** Providing a value outside the `0.0f` to `1.0f` range may result in unexpected behavior or `IllegalArgumentException` depending on the underlying image writer implementation.
- **Misunderstanding scope:** This setting primarily affects lossy formats like JPEG; it may have no effect on lossless formats (e.g., PNG) depending on the `ImageIO` writer capabilities.
- **Reusing builders:** `ThumbnailParameter` objects are not thread-safe; do not share a builder instance across multiple threads or concurrent tasks.

### Fix Code Hint
```java
// Ensure quality is within valid range
float q = Math.max(0.0f, Math.min(1.0f, userProvidedValue));
builder.quality(q);
```

## API Test: `java:net.coobird.thumbnailator.builders.ThumbnailParameterBuilder.region`

### Signature
```java
(Region sourceRegion):ThumbnailParameterBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:200_

### Goal
Sets the specific rectangular area of the source image to be used for thumbnail generation, effectively cropping the source before processing.

### Parameters
- `sourceRegion` (`Region`): The `Region` object defining the coordinates and dimensions of the area to be extracted from the source image.

### Input
The caller must provide a valid `Region` object. The source image must be loaded into the `ThumbnailParameterBuilder` context. This method is typically used when a specific sub-section of an image is required rather than the full source.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, allowing for method chaining in the fluent API.

### Valid Call Patterns
```java
// Inferred from API signature and usage patterns
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.region(new Region(new Point(0, 0), new Dimension(100, 100)));
```

### LLM Instruction Prompt
- When using `region`, ensure the `Region` object is instantiated with valid coordinates that fall within the bounds of the source image.
- This method is part of the `ThumbnailParameterBuilder` fluent API; chain it with other configuration methods before finalizing the task.
- Note that `ThumbnailParameter` objects are not thread-safe; do not share the builder across concurrent tasks.

### Prompt Snippet
```text
// Example of setting a region for a thumbnail task
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.region(myRegion);
```

### Common Failure Modes
- **Out of Bounds:** Providing a `Region` that exceeds the dimensions of the source image may lead to unexpected cropping behavior or `IllegalArgumentException` during the processing phase.
- **State Reuse:** Attempting to reuse a `ThumbnailParameterBuilder` instance across multiple threads or tasks will lead to race conditions, as the builder is not thread-safe.
- **Missing Context:** Calling `region` without having defined the source image or subsequent output destination in the pipeline.

### Fix Code Hint
```java
// Ensure the region is within the source image dimensions
Region cropArea = new Region(new Point(x, y), new Dimension(width, height));
builder.region(cropArea);
```

## API Test: `java:net.coobird.thumbnailator.builders.ThumbnailParameterBuilder.resizer`

### Signature
```java
(Resizer resizer):ThumbnailParameterBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:288_

### Goal
Sets the specific `Resizer` implementation to be used for the image scaling operation within the `ThumbnailParameter` configuration.

### Parameters
- `resizer` (`Resizer`): The specific resizing algorithm (e.g., `Resizers.BILINEAR`, `Resizers.BICUBIC`) to be used for the thumbnail generation.

### Input
The caller must provide a valid implementation of the `Resizer` interface. Note that calling this method overrides any previously set `ResizerFactory`, forcing the resulting `ThumbnailParameter` to use the provided `Resizer` instance exclusively.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, allowing for further method chaining.

### Valid Call Patterns
```java
// Inferred from API signature and Resizer usage
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.resizer(net.coobird.thumbnailator.resizers.Resizers.BILINEAR);
```

### LLM Instruction Prompt
- When calling `resizer`, ensure the argument is an instance of `net.coobird.thumbnailator.resizers.Resizer`. 
- Be aware that this method is mutually exclusive with `resizerFactory` in terms of configuration; if both are called, the last one invoked will determine the resizing behavior.

### Prompt Snippet
```text
// Example of setting a specific resizer
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.resizer(Resizers.PROGRESSIVE_BILINEAR);
```

### Common Failure Modes
- Passing a `null` value to the `resizer` parameter, which may result in a `NullPointerException` during the subsequent thumbnail generation process.
- Attempting to use this method on a `ThumbnailParameterBuilder` that has already been finalized or used to create a `ThumbnailParameter` object, if the builder state is intended to be immutable after configuration.

### Fix Code Hint
```java
// Ensure a valid Resizer is provided
builder.resizer(Resizers.BICUBIC); 
```

## API Test: `java:net.coobird.thumbnailator.builders.ThumbnailParameterBuilder.resizerFactory`

### Signature
```java
(ResizerFactory resizerFactory):ThumbnailParameterBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator.builders.ThumbnailParameterBuilder.java:311_

### Goal
Sets the `ResizerFactory` used to determine the appropriate `Resizer` algorithm for the thumbnail generation task.

### Parameters
- `resizerFactory` (`ResizerFactory`): The factory implementation responsible for providing a `Resizer` instance based on the source and destination image dimensions.

### Input
The caller must provide an implementation of the `ResizerFactory` interface. Preconditions include ensuring the `ThumbnailParameterBuilder` is in a valid state for configuration. Note that this method is typically used when custom resizing logic is required beyond the default `DefaultResizerFactory` behavior.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, allowing for method chaining.

### Valid Call Patterns
```java
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.resizerFactory(new DefaultResizerFactory());
```

### LLM Instruction Prompt
- When calling `resizerFactory`, ensure the `ResizerFactory` instance is fully initialized.
- Be aware that calling this method after `resizer(Resizer)` may cause the previously set `Resizer` to be ignored in favor of the factory's selection logic.
- Use this method only when the default resizing strategy (which automatically selects between `ProgressiveBilinear`, `Bicubic`, and `NullResizer`) is insufficient for the specific image processing requirements.

### Prompt Snippet
```text
// Example of setting a custom ResizerFactory on a builder
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.resizerFactory(myCustomResizerFactory);
```

### Common Failure Modes
- **Overwriting Configuration:** Calling `resizerFactory` after `resizer(Resizer)` can lead to unexpected behavior where the specific `Resizer` set by the latter is overridden by the factory.
- **Null Arguments:** Passing a `null` `resizerFactory` will likely result in a `NullPointerException` when the builder attempts to generate the thumbnail.
- **State Mismanagement:** Reusing a `ThumbnailParameterBuilder` across multiple tasks is discouraged as it is not thread-safe and maintains mutable state.

### Fix Code Hint
```java
// Ensure the factory is set before the task is built
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.resizerFactory(new DefaultResizerFactory());
// Proceed to build the parameter object
ThumbnailParameter param = builder.build();
```

## API Test: `java:net.coobird.thumbnailator.builders.ThumbnailParameterBuilder.scale`

### Signature
```java
(double widthScalingFactor,double heightScalingFactor):ThumbnailParameterBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:176_

### Goal
Sets the horizontal and vertical scaling factors for a thumbnail generation task, allowing for independent resizing of image dimensions.

### Parameters
- `widthScalingFactor` (`double`): The scaling factor to apply to the image width; must be a non-negative rational number.
- `heightScalingFactor` (`double`): The scaling factor to apply to the image height; must be a non-negative rational number.

### Input
The caller must provide two `double` values representing the desired scaling ratios. Preconditions: The scaling factors must be rational numbers and greater than or equal to 0.0. This method must not be called if a fixed size (via `.size()`) has already been configured for the builder.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, enabling fluent method chaining.

### Valid Call Patterns
```java
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.scale(0.5, 0.5);
```

### LLM Instruction Prompt
- When using `ThumbnailParameterBuilder.scale(double, double)`, ensure the scaling factors are non-negative.
- Do not chain this method if `.size()` has already been invoked on the same builder instance, as this will trigger an `IllegalStateException`.
- Use this method when specific, non-uniform scaling is required for width and height.

### Prompt Snippet
```text
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.scale(widthFactor, heightFactor);
```

### Common Failure Modes
- `IllegalArgumentException`: Thrown if either scaling factor is not a rational number or is less than 0.0.
- `IllegalStateException`: Thrown if the builder has already been configured with a fixed size using the `.size()` method.

### Fix Code Hint
```java
// Ensure no conflicting size configuration exists before scaling
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
// builder.size(100, 100); // Do not call this if using scale()
builder.scale(0.5, 0.5);
```

## API Test: `java:net.coobird.thumbnailator.builders.ThumbnailParameterBuilder.size`

### Signature
```java
(int width,int height):ThumbnailParameterBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:137_

### Goal
Sets the target dimensions for the thumbnail generation process within the `ThumbnailParameterBuilder`.

### Parameters
- `width` (`int`): The desired width of the thumbnail in pixels; must be greater than or equal to 0.
- `height` (`int`): The desired height of the thumbnail in pixels; must be greater than or equal to 0.

### Input
The caller must provide non-negative integer values for width and height. This method is typically called during the configuration phase of a `ThumbnailParameterBuilder` instance before the thumbnail task is executed.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, allowing for method chaining.

### Valid Call Patterns
```java
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.size(200, 200);
```

### LLM Instruction Prompt
- Use `size(int width, int height)` to define the output dimensions of the thumbnail.
- Ensure both `width` and `height` are non-negative integers.
- Chain this method with other configuration methods on the `ThumbnailParameterBuilder` before finalizing the task.

### Prompt Snippet
```text
// Example of setting size on a builder
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.size(width, height);
```

### Common Failure Modes
- **IllegalArgumentException**: Thrown if either `width` or `height` is less than 0.
- **IllegalStateException**: Occurs if `size()` is called after `scale()` has already been invoked on the same builder instance, as these are mutually exclusive resizing strategies.

### Fix Code Hint
```java
// Ensure dimensions are positive and avoid mixing with scale()
try {
    builder.size(200, 200);
} catch (IllegalArgumentException e) {
    // Handle negative dimension error
} catch (IllegalStateException e) {
    // Handle conflict with existing scale() configuration
}
```

## API Test: `java:net.coobird.thumbnailator.builders.ThumbnailParameterBuilder.useExifOrientation`

### Signature
```java
(boolean use):ThumbnailParameterBuilder
```
_Source: source/src/main/java/net/coobird/thumbnailator/builders/ThumbnailParameterBuilder.java:345_

### Goal
Configures the thumbnail generation process to automatically detect and apply orientation corrections based on the source image's Exif metadata.

### Parameters
- `use` (`boolean`): Set to `true` to enable automatic orientation correction using Exif metadata, or `false` to ignore Exif orientation tags.

### Input
The caller must provide a `boolean` value. This method is intended for use within a `ThumbnailParameterBuilder` configuration sequence. The source image must contain valid Exif metadata for the orientation correction to have a visible effect.

### Output
Returns `ThumbnailParameterBuilder` — A reference to the current builder instance, allowing for further method chaining.

### Valid Call Patterns
```java
ThumbnailParameterBuilder builder = new ThumbnailParameterBuilder();
builder.useExifOrientation(true);
```

### LLM Instruction Prompt
- Use this method when the source images (typically JPEGs from cameras or mobile devices) may contain orientation metadata that needs to be respected to ensure the output thumbnail is upright.
- Always chain this method with other `ThumbnailParameterBuilder` configuration methods before finalizing the parameter object.

### Prompt Snippet
```text
// Enable Exif orientation handling
builder.useExifOrientation(true);
```

### Common Failure Modes
- **Ignoring Metadata:** If set to `false` (or left unset), images captured in portrait mode on mobile devices may appear rotated 90 degrees in the output thumbnail.
- **Unsupported Formats:** Enabling this has no effect on image formats that do not support or contain Exif metadata (e.g., standard PNG or BMP files).
- **Builder Reuse:** `ThumbnailParameterBuilder` is not thread-safe; do not share the same builder instance across multiple threads or concurrent tasks.

### Fix Code Hint
```java
// Ensure orientation is corrected for camera-sourced images
ThumbnailParameter param = new ThumbnailParameterBuilder()
    .useExifOrientation(true)
    .size(200, 200)
    .build();
```

## API Test: `java:net.coobird.thumbnailator.filters.Canvas`

### Signature
```java
(int width,int height,Position position,boolean crop,Color fillColor):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Canvas.java:144_

### Goal
The `Canvas` filter adjusts the dimensions of an image to a specified size by placing the original image at a defined position and filling the remaining area with a specified color.

### Parameters
- `width` (`int`): The target width of the resulting image.
- `height` (`int`): The target height of the resulting image.
- `position` (`Position`): The geometric placement of the original image within the new canvas dimensions.
- `crop` (`boolean`): If `true`, the original image is cropped if its dimensions exceed the specified canvas width and height.
- `fillColor` (`Color`): The color used to fill areas not covered by the original image; also replaces transparent pixels in the original image.

### Input
The caller must provide a `BufferedImage` to be processed by the filter. This filter is typically applied within a `Thumbnails` pipeline using the `.addFilter()` method.

### Output
Returns `null` — The filter modifies the `BufferedImage` in-place or as part of the pipeline transformation; the resulting image is a `BufferedImage` with the new dimensions and filled background.

### Valid Call Patterns
```java
// Example inferred from signature and filter usage patterns
new net.coobird.thumbnailator.filters.Canvas(800, 600, Positions.CENTER, true, Color.WHITE);
```

### LLM Instruction Prompt
- When using `Canvas`, ensure the `Position` object is imported from `net.coobird.thumbnailator.geometry`.
- The `fillColor` parameter is mandatory in this constructor; if no color is desired, consider using an alternative constructor if available, or provide `null` if the implementation permits (though explicit `Color` is safer).
- Always verify that the `width` and `height` are positive integers.

### Prompt Snippet
```text
net.coobird.thumbnailator.filters.Canvas canvasFilter = new net.coobird.thumbnailator.filters.Canvas(width, height, position, crop, fillColor);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `position` or `fillColor` arguments are passed as `null` and the internal implementation does not handle them gracefully.
- **IllegalArgumentException:** May occur if `width` or `height` are non-positive values.
- **Pipeline Misconfiguration:** Attempting to use the filter without adding it to a `Thumbnails` builder chain or applying it to a `BufferedImage` directly.

### Fix Code Hint
```java
// Ensure the filter is added to the pipeline correctly
Thumbnails.of("input.jpg")
    .size(200, 200)
    .addFilter(new net.coobird.thumbnailator.filters.Canvas(800, 600, Positions.CENTER, true, Color.BLACK))
    .toFile("output.jpg");
```

## API Test: `java:net.coobird.thumbnailator.filters.Canvas.apply`

### Signature
```java
(BufferedImage img):BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Canvas.java:153_

### Goal
Applies a canvas transformation to a `BufferedImage`, allowing the image to be placed on a new background of a specified size and position.

### Parameters
- `img` (`BufferedImage`): The source image to be placed onto the canvas.

### Input
The caller must provide a `BufferedImage` instance. The `Canvas` filter must be configured (via its constructor or setter methods) with the desired dimensions and positioning logic before `apply` is invoked. The input image must be non-null.

### Output
Returns `BufferedImage` — The resulting image containing the original source image placed on the defined canvas area.

### Valid Call Patterns
```java
// Example of applying the Canvas filter to a BufferedImage
Canvas canvasFilter = new Canvas(width, height, position);
BufferedImage result = canvasFilter.apply(sourceImage);
```
*(Note: This call pattern is inferred from the API signature and the `ImageFilter` interface contract.)*

### LLM Instruction Prompt
- When using `java:net.coobird.thumbnailator.filters.Canvas.apply`, ensure the `Canvas` object is instantiated with valid dimensions and a `Position` object before calling `apply`. The `apply` method is part of the `ImageFilter` interface and is intended to be used within a processing pipeline or as a standalone transformation on a `BufferedImage`.

### Prompt Snippet
```text
Canvas canvas = new Canvas(width, height, position);
BufferedImage processedImage = canvas.apply(sourceImage);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `img` parameter passed to `apply` is null.
- **IllegalStateException:** May occur if the `Canvas` filter is not properly initialized with dimensions before the `apply` method is called.
- **Memory Constraints:** Applying large canvas transformations to high-resolution images may lead to `OutOfMemoryError` if the heap size is insufficient for the new `BufferedImage` dimensions.

### Fix Code Hint
```java
// Ensure the image is not null and the filter is initialized
if (sourceImage != null) {
    Canvas canvas = new Canvas(800, 600, Positions.CENTER);
    BufferedImage result = canvas.apply(sourceImage);
}
```

## API Test: `java:net.coobird.thumbnailator.filters.Caption`

### Signature
```java
(String caption,Font font,Color c,float alpha,Position position,int insets):null
```
_Source: source/src/main/java/net/coobird/thumbnailator.filters.Caption.java:92_

### Goal
Adds a text caption to an image as an `ImageFilter` within a Thumbnailator processing pipeline.

### Parameters
- `caption` (`String`): The text content to be rendered onto the image.
- `font` (`Font`): The `java.awt.Font` used to render the caption text.
- `c` (`Color`): The `java.awt.Color` of the caption text.
- `alpha` (`float`): The opacity level of the caption, ranging from `0.0f` (transparent) to `1.0f` (opaque).
- `position` (`Position`): The `net.coobird.thumbnailator.geometry.Position` defining where the caption is placed on the image.
- `insets` (`int`): The padding size in pixels around the caption; must be a non-negative integer.

### Input
The caller must provide a valid `BufferedImage` (typically handled by the `Thumbnails` fluent pipeline). The `insets` parameter must not be negative. The `alpha` value must be within the `0.0f` to `1.0f` range.

### Output
Returns `null` (the constructor initializes the filter object, which is then passed to the `addFilter` method of the `Thumbnails.Builder`).

### Valid Call Patterns
```java
Thumbnails.of("image.jpg")
    .size(200, 200)
    .addFilter(new Caption("My Caption", Font.decode("Arial"), Color.WHITE, 1.0f, Positions.BOTTOM_RIGHT, 10))
    .toFile("output.jpg");
```

### LLM Instruction Prompt
- When generating code using `Caption`, ensure it is passed as an argument to the `.addFilter()` method of a `Thumbnails.Builder`.
- Always provide a valid `java.awt.Font` and `java.awt.Color` instance.
- Ensure the `alpha` value is a float between `0.0f` and `1.0f`.
- Use the `net.coobird.thumbnailator.geometry.Positions` enum for the `position` argument to ensure compatibility with the `Position` interface.

### Prompt Snippet
```text
// Example of adding a caption filter to a Thumbnailator pipeline
.addFilter(new Caption("Text", new Font("Arial", Font.PLAIN, 12), Color.BLACK, 0.5f, Positions.CENTER, 5))
```

### Common Failure Modes
- **Negative Insets:** Passing a negative integer to the `insets` parameter will result in an `IllegalArgumentException`.
- **Invalid Alpha:** Providing an `alpha` value outside the `0.0f` to `1.0f` range may lead to unexpected rendering or runtime errors.
- **Pipeline Order:** Filters are applied in the order they are added; ensure the image has been resized or transformed as needed before applying the caption if specific placement relative to the final dimensions is required.

### Fix Code Hint
```java
// Ensure insets are non-negative and alpha is within [0.0, 1.0]
int padding = Math.max(0, userProvidedPadding);
float opacity = Math.min(1.0f, Math.max(0.0f, userProvidedAlpha));
new Caption("Caption", font, color, opacity, Positions.BOTTOM_RIGHT, padding);
```

## API Test: `java:net.coobird.thumbnailator.filters.Caption.apply`

### Signature
```java
(BufferedImage img):BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Caption.java:136_

### Goal
Applies a text caption overlay to a `BufferedImage` based on the configuration defined during the `Caption` object's instantiation.

### Parameters
- `img` (`BufferedImage`): The source image to which the caption will be drawn.

### Input
- **Data:** A valid `BufferedImage` object.
- **Preconditions:** The `Caption` instance must be initialized with the desired text, font, color, position, and inset before calling `apply`.
- **Formats:** Supports any `BufferedImage` type compatible with `Graphics2D` operations.

### Output
Returns `BufferedImage` — The resulting image containing the original content with the caption rendered at the specified position.

### Valid Call Patterns
```java
Caption captionFilter = new Caption("My Caption", Font.decode("Arial"), Color.WHITE, Positions.BOTTOM_RIGHT, 10);
BufferedImage result = captionFilter.apply(sourceImage);
```

### LLM Instruction Prompt
- When using `Caption.apply`, ensure the `Caption` object has been instantiated with valid `Font`, `Color`, and `Position` parameters. The `apply` method is typically invoked internally by the `Thumbnails` fluent pipeline via `.addFilter()`, but can be called manually on a `BufferedImage` if granular control is required.

### Prompt Snippet
```text
// Example of applying a caption filter manually
Caption caption = new Caption("Sample Text", new Font("Arial", Font.PLAIN, 12), Color.BLACK, Positions.CENTER, 5);
BufferedImage processedImage = caption.apply(inputImage);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `img` parameter passed to `apply` is `null`.
- **Graphics Configuration:** If the `BufferedImage` type does not support transparency or specific color models, the rendered caption may appear with artifacts or incorrect colors.
- **Font Issues:** Providing a `null` font or an unsupported font name may cause rendering errors during the `apply` operation.

### Fix Code Hint
```java
// Ensure the image is not null and the caption filter is properly configured
if (sourceImage != null) {
    BufferedImage result = new Caption("Text", font, color, pos, inset).apply(sourceImage);
}
```

## API Test: `java:net.coobird.thumbnailator.filters.Colorize`

### Signature
```java
(Color c,float alpha):null
```
_Source: source/src/main/java/net/coobird/thumbnailator.filters.Colorize.java:69_

### Goal
Applies a color tint to a `BufferedImage` with a specified transparency level to create stylized or color-adjusted thumbnails.

### Parameters
- `c` (`Color`): The `java.awt.Color` to use as the tinting agent.
- `alpha` (`float`): The opacity of the tint, ranging from `0.0f` (completely transparent) to `1.0f` (completely opaque).

### Input
- **Data:** Requires a `BufferedImage` as the target for the filter.
- **Preconditions:** The filter is intended to be used within the Thumbnailator pipeline via the `addFilter` method.
- **Compatibility:** If the provided `Color` object lacks an alpha channel, the resulting image will be rendered using the opaque color, effectively masking the original image content.

### Output
Returns `null` — The operation modifies the `BufferedImage` in-place or as part of the pipeline processing chain; it does not return a new image object directly from the constructor.

### Valid Call Patterns
```java
// Inferred from signature and project usage patterns
Thumbnails.of("image.jpg")
    .size(200, 200)
    .addFilter(new net.coobird.thumbnailator.filters.Colorize(Color.RED, 0.5f))
    .toFile("output.jpg");
```

### LLM Instruction Prompt
- When using `Colorize`, always instantiate it as a new object within the `.addFilter()` method of the `Thumbnails` fluent builder.
- Ensure the `alpha` value is a `float` between `0.0f` and `1.0f`.
- If the intent is to tint the image without full opacity, ensure the `Color` object is handled correctly to avoid unintended opaque masking.

### Prompt Snippet
```text
new net.coobird.thumbnailator.filters.Colorize(Color, float)
```

### Common Failure Modes
- **Opaque Masking:** Providing a `Color` without an alpha channel can result in the image being completely covered by the solid color if the `alpha` is set to `1.0f`.
- **Pipeline Order:** Applying filters before resizing or after other transformations may yield different visual results; ensure `Colorize` is placed in the correct sequence within the `addFilter` chain.
- **Type Mismatch:** Passing an `int` for the alpha parameter will trigger a different constructor (0-255 range); ensure the `float` type is explicitly used to match the `0.0f-1.0f` requirement.

### Fix Code Hint
```java
// Ensure float literal is used for alpha
.addFilter(new net.coobird.thumbnailator.filters.Colorize(Color.BLUE, 0.3f))
```

## API Test: `java:net.coobird.thumbnailator.filters.Colorize.apply`

### Signature
```java
(BufferedImage img):BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Colorize.java:97_

### Goal
Applies a colorization filter to a `BufferedImage`, modifying its color profile or tint based on the configuration of the `Colorize` instance.

### Parameters
- `img` (`BufferedImage`): The source image to which the colorization filter is applied.

### Input
- **Data:** A valid `BufferedImage` object.
- **Preconditions:** The `Colorize` instance must be initialized with the desired color parameters before calling `apply`. The input image must be non-null.
- **Formats:** Supports any `BufferedImage` type compatible with standard Java 2D graphics operations.

### Output
Returns `BufferedImage` — The resulting image after the colorization filter has been applied.

### Valid Call Patterns
```java
// Inferred from the ImageFilter interface implementation
Colorize filter = new Colorize(Color.RED); // Example initialization
BufferedImage result = filter.apply(sourceImage);
```

### LLM Instruction Prompt
- When using `Colorize.apply`, ensure the `Colorize` object is instantiated with the target color before invoking the method.
- Treat the returned `BufferedImage` as a new or modified instance; do not assume the original `img` remains unchanged if the filter performs in-place modification.
- Use this method within a `Pipeline` or as a standalone filter step when processing images via the Thumbnailator library.

### Prompt Snippet
```text
// Example of applying a Colorize filter to a BufferedImage
Colorize colorFilter = new Colorize(java.awt.Color.BLUE);
BufferedImage processedImage = colorFilter.apply(inputImage);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `img` parameter passed to `apply` is `null`.
- **IllegalStateException:** May occur if the `Colorize` filter is used in a context where the underlying graphics environment is not properly initialized (though Thumbnailator is designed for headless environments).
- **Unsupported Image Type:** If the `BufferedImage` type is incompatible with the `Graphics2D` operations used internally by the filter, unexpected rendering results or exceptions may occur.

### Fix Code Hint
```java
// Ensure the image is not null and is of a supported type
if (sourceImage != null) {
    Colorize filter = new Colorize(Color.BLUE);
    BufferedImage result = filter.apply(sourceImage);
}
```

## API Test: `java:net.coobird.thumbnailator.filters.Pipeline`

### Signature
```java
(ImageFilter... filters):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Pipeline.java:73_

### Goal
The `Pipeline` class aggregates multiple `ImageFilter` implementations into a single sequential processing unit to apply complex image transformations to a `BufferedImage`.

### Parameters
- `filters` (`ImageFilter...`): A variable-length array of `ImageFilter` objects (e.g., `Watermark`, `Caption`, `Rotation`, `Flip`, `Colorize`, `Transparency`) to be applied in the order provided.

### Input
The caller must provide one or more objects implementing the `net.coobird.thumbnailator.filters.ImageFilter` interface. These filters are applied to a `BufferedImage` during the thumbnail generation process. There are no specific file format requirements for the filters themselves, as they operate on in-memory `BufferedImage` objects.

### Output
Returns `null`. The `Pipeline` acts as a container for filters; the actual image transformation occurs when the pipeline is executed as part of a `ThumbnailTask` or via the fluent `Thumbnails` API.

### Valid Call Patterns
```java
// Example: Creating a pipeline with multiple filters
ImageFilter[] filters = new ImageFilter[] {
    new Rotation(90),
    new Flip(Flip.HORIZONTAL)
};
Pipeline pipeline = new Pipeline(filters);
```

### LLM Instruction Prompt
When using `java:net.coobird.thumbnailator.filters.Pipeline`, ensure that the `ImageFilter` objects passed to the constructor are instantiated correctly. The order of the `filters` array determines the sequence of image processing operations. Note that `Pipeline` itself is a container; it is typically used within the `addFilter` method of the `Thumbnails.Builder` fluent API.

### Prompt Snippet
```text
// Use Pipeline to group multiple filters for a thumbnail task
Pipeline pipeline = new Pipeline(filter1, filter2);
Thumbnails.of("input.jpg")
    .size(200, 200)
    .addFilter(pipeline)
    .toFile("output.jpg");
```

### Common Failure Modes
- **Null Elements:** Passing a `null` value within the `filters` array will likely result in a `NullPointerException` when the pipeline attempts to process an image.
- **Ordering Issues:** Applying filters in the wrong sequence (e.g., applying a watermark before a rotation) may result in unexpected positioning or orientation of the watermark.
- **Memory Constraints:** Chaining too many complex filters on very large images may lead to `OutOfMemoryError` in memory-constrained headless environments.

### Fix Code Hint
```java
// Ensure all filters are non-null before passing to the Pipeline
ImageFilter[] filters = { filter1, filter2 };
if (filters != null) {
    Pipeline pipeline = new Pipeline(filters);
    // Proceed with processing
}
```

## API Test: `java:net.coobird.thumbnailator.filters.Pipeline.add`

### Signature
```java
(ImageFilter filter):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Pipeline.java:99_

### Goal
Adds an `ImageFilter` to the processing pipeline to apply custom image transformations sequentially during thumbnail generation.

### Parameters
- `filter` (`ImageFilter`): An implementation of the `ImageFilter` interface (e.g., `Caption`, `Watermark`, `Rotation`, `Flip`, `Colorize`, or `Transparency`) that defines a specific image processing operation.

### Input
The caller must provide a valid object implementing the `ImageFilter` interface. The pipeline assumes that the provided filter is ready for execution. There are no specific file format restrictions for the filter itself, as it operates on `BufferedImage` objects in-memory.

### Output
Returns `void` — the method modifies the internal state of the `Pipeline` instance by appending the filter to the execution queue.

### Valid Call Patterns
```java
Pipeline pipeline = new Pipeline();
pipeline.add(new Caption("Example", Font.decode("Arial"), Color.WHITE, Positions.BOTTOM_RIGHT, 10));
```

### LLM Instruction Prompt
- When generating code, use `Pipeline.add` to register image processing filters. Ensure the `ImageFilter` implementation is instantiated with all required parameters (e.g., font, color, position) before passing it to the `add` method.

### Prompt Snippet
```text
// Example of adding a filter to a Pipeline
Pipeline pipeline = new Pipeline();
pipeline.add(new net.coobird.thumbnailator.filters.Caption("Text", font, color, position, inset));
```

### Common Failure Modes
- **Null Argument:** Passing `null` as the `filter` argument may result in a `NullPointerException` when the pipeline attempts to process the image.
- **Filter Order:** Since the pipeline executes filters in the order they are added, adding filters in the wrong sequence (e.g., applying a watermark before a rotation) may lead to unexpected visual results.
- **In-Memory Constraints:** While the pipeline is efficient, adding an excessive number of complex filters to a single pipeline may increase memory consumption during the processing of large `BufferedImage` objects.

### Fix Code Hint
```java
// Ensure the filter is not null before adding
if (myFilter != null) {
    pipeline.add(myFilter);
}
```

## API Test: `java:net.coobird.thumbnailator.filters.Pipeline.addAll`

### Signature
```java
(List<ImageFilter> filters):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Pipeline.java:125_

### Goal
Appends a collection of `ImageFilter` objects to the current processing pipeline to be applied sequentially to an image.

### Parameters
- `filters` (`List<ImageFilter>`): A list of image processing filters (e.g., `Watermark`, `Caption`, `Rotation`) to be added to the pipeline.

### Input
The caller must provide a `List` containing valid `ImageFilter` implementations. The pipeline assumes that the filters are compatible with the `BufferedImage` being processed. Preconditions include ensuring the list is not null and contains initialized filter instances.

### Output
Returns `void` — the method modifies the internal state of the `Pipeline` instance by appending the provided filters to the existing sequence.

### Valid Call Patterns
```java
// Inferred from API signature:
List<ImageFilter> myFilters = new ArrayList<>();
myFilters.add(new Watermark(Positions.BOTTOM_RIGHT, image, 0.5f));
pipeline.addAll(myFilters);
```

### LLM Instruction Prompt
- When using `addAll`, ensure the `List<ImageFilter>` is instantiated and populated with valid `ImageFilter` implementations before passing it to the `Pipeline`.
- Remember that `Pipeline` is used for chaining transformations; ensure the order of filters in the list reflects the desired order of application on the image.

### Prompt Snippet
```text
// Example of adding multiple filters to a pipeline
List<ImageFilter> filters = new ArrayList<>();
filters.add(new Rotation(90));
filters.add(new Flip(Flip.HORIZONTAL));
pipeline.addAll(filters);
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `filters` list passed to the method is `null`.
- **UnsupportedOperationException**: May occur if the provided `List` is immutable and the internal implementation attempts to modify it.
- **Filter Incompatibility**: Applying filters that are not suitable for the specific `BufferedImage` type (e.g., applying transparency filters to images that do not support alpha channels) may lead to unexpected visual results or runtime errors during the `apply` phase.

### Fix Code Hint
```java
// Ensure the list is mutable and non-null
List<ImageFilter> filters = new ArrayList<>();
filters.add(new MyImageFilter());
pipeline.addAll(filters);
```

## API Test: `java:net.coobird.thumbnailator.filters.Pipeline.addFirst`

### Signature
```java
(ImageFilter filter):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Pipeline.java:112_

### Goal
Adds a specified `ImageFilter` to the beginning of the current image processing pipeline, ensuring it is the first transformation applied to the image.

### Parameters
- `filter` (`ImageFilter`): An implementation of the `ImageFilter` interface (e.g., `Watermark`, `Caption`, `Rotation`, `Flip`, `Colorize`, or `Transparency`) to be executed at the start of the pipeline.

### Input
- The caller must have an existing `Pipeline` instance.
- The `filter` argument must be a valid, non-null implementation of `ImageFilter`.
- Precondition: The pipeline must be in a state where adding a filter is permitted; the filter will be applied to the `BufferedImage` processed by the pipeline.

### Output
Returns `void` — The method modifies the internal state of the `Pipeline` object by prepending the filter to the execution chain.

### Valid Call Patterns
```java
// Inferred from API signature:
Pipeline pipeline = new Pipeline();
pipeline.addFirst(new Watermark(Positions.CENTER, image, 0.5f));
```

### LLM Instruction Prompt
- When using `addFirst`, ensure the `ImageFilter` is fully initialized before passing it to the method.
- Remember that `Pipeline` is used to chain multiple `ImageFilter` objects; `addFirst` specifically alters the order of execution by placing the new filter at the head of the queue.
- Ensure the `ImageFilter` implementation is compatible with the `BufferedImage` type being processed.

### Prompt Snippet
```text
// Example of adding a filter to the start of a pipeline
pipeline.addFirst(new Rotation(90));
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `filter` argument passed to `addFirst` is `null`.
- **Pipeline State Issues**: Attempting to modify a pipeline that is currently being executed or has already been finalized may lead to unexpected behavior or `IllegalStateException` depending on the specific `Pipeline` implementation state.
- **Incompatible Filters**: Passing a filter that is not compatible with the specific `BufferedImage` color model or dimensions may result in runtime errors during the processing phase.

### Fix Code Hint
```java
// Ensure the filter is not null before adding
if (myFilter != null) {
    pipeline.addFirst(myFilter);
}
```

## API Test: `java:net.coobird.thumbnailator.filters.Pipeline.apply`

### Signature
```java
(BufferedImage img):BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Pipeline.java:144_

### Goal
Applies a sequence of registered `ImageFilter` operations to a `BufferedImage` in the order they were added to the pipeline.

### Parameters
- `img` (`BufferedImage`): The source image to be processed by the pipeline.

### Input
The caller must provide a valid `BufferedImage` instance. The pipeline assumes that all `ImageFilter` objects added to the `Pipeline` instance are ready to process the provided image. There are no specific file format restrictions at this stage, as the operation occurs entirely in-memory on the `BufferedImage` object.

### Output
Returns `BufferedImage` — The resulting image after all registered filters have been sequentially applied to the input image.

### Valid Call Patterns
```java
// Example inferred from the API signature and ImageFilter interface
Pipeline pipeline = new Pipeline();
pipeline.add(new Watermark(Positions.BOTTOM_RIGHT, watermarkImage, 0.5f));
BufferedImage result = pipeline.apply(sourceImage);
```

### LLM Instruction Prompt
- When using `java:net.coobird.thumbnailator.filters.Pipeline.apply`, ensure the `Pipeline` instance has been populated with at least one `ImageFilter` using the `add()` method.
- The `apply` method is the terminal operation for the pipeline; it performs the transformation in-memory.
- Always pass a non-null `BufferedImage` to avoid `NullPointerException`.

### Prompt Snippet
```text
// Apply a pipeline of filters to a BufferedImage
BufferedImage processedImage = pipeline.apply(sourceImage);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `img` argument passed to `apply` is null.
- **Filter-specific Exceptions:** If an added `ImageFilter` is incompatible with the image type (e.g., applying a filter requiring an alpha channel to an image type that does not support it), the underlying filter may throw an exception during the `apply` execution.
- **Pipeline State:** If the pipeline is empty, `apply` will return the original image without modifications.

### Fix Code Hint
```java
// Ensure the image is not null and the pipeline is configured
if (sourceImage != null) {
    BufferedImage result = pipeline.apply(sourceImage);
}
```

## API Test: `java:net.coobird.thumbnailator.filters.Pipeline.getFilters`

### Signature
```java
():List<ImageFilter>
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Pipeline.java:140_

### Goal
Retrieves the ordered list of `ImageFilter` objects currently registered to be applied by the `Pipeline` instance during image processing.

### Parameters
_None._

### Input
No parameters are required. The `Pipeline` instance must be initialized and populated with filters (e.g., via `addFilter`) prior to calling this method to receive a non-empty list.

### Output
Returns `List<ImageFilter>` — A list containing the `ImageFilter` implementations in the order they are scheduled to be applied to the image.

### Valid Call Patterns
```java
// Inferred from signature:
Pipeline pipeline = new Pipeline();
// ... add filters to pipeline ...
List<ImageFilter> filters = pipeline.getFilters();
```

### LLM Instruction Prompt
- When generating code that inspects a `Pipeline`, use `getFilters()` to retrieve the current transformation chain.
- Note that the returned `List` represents the sequence of `ImageFilter` operations applied to the `BufferedImage` during the processing task.
- Ensure the `Pipeline` object is instantiated before calling this method.

### Prompt Snippet
```text
// Accessing the filter chain from a Pipeline instance
List<ImageFilter> activeFilters = pipeline.getFilters();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `Pipeline` instance itself is null.
- **Empty List:** If no filters have been added to the `Pipeline`, the method returns an empty list, which may cause logic errors if the caller expects at least one filter to be present.
- **Modification:** Modifying the returned `List` may or may not affect the internal state of the `Pipeline` depending on the specific implementation of the `Pipeline` class; it is safer to treat the returned list as read-only.

### Fix Code Hint
```java
// Ensure the pipeline is not null before accessing filters
if (pipeline != null) {
    List<ImageFilter> filters = pipeline.getFilters();
    // Process filters safely
}
```

## API Test: `java:net.coobird.thumbnailator.filters.Rotation.newRotator`

### Signature
```java
(final double angle):Rotator
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Rotation.java:78_

### Goal
Creates a `Rotator` instance that, when applied to an image, rotates it clockwise by the specified angle in degrees.

### Parameters
- `angle` (`final double`): The angle in degrees by which the image should be rotated clockwise.

### Input
- **Data:** Requires a `double` value representing the rotation angle.
- **Preconditions:** The returned `Rotator` is an `ImageFilter` implementation intended for use within a `Pipeline` or via the `addFilter` method in the fluent `Thumbnails` API.

### Output
Returns `Rotator` — An instance of the `Rotator` class configured to perform the specified clockwise rotation on a `BufferedImage`.

### Valid Call Patterns
```java
// Inferred from API signature and usage context
net.coobird.thumbnailator.filters.Rotation.Rotator rotator = 
    net.coobird.thumbnailator.filters.Rotation.newRotator(90.0);
```

### LLM Instruction Prompt
- Use `net.coobird.thumbnailator.filters.Rotation.newRotator(double)` to generate a rotation filter.
- Pass the resulting `Rotator` object to the `.addFilter()` method of a `Thumbnails.Builder` instance.
- Ensure the angle is provided as a `double`.

### Prompt Snippet
```text
// Example of applying rotation via the fluent API
Thumbnails.of("input.jpg")
    .size(200, 200)
    .addFilter(net.coobird.thumbnailator.filters.Rotation.newRotator(90.0))
    .toFile("output.jpg");
```

### Common Failure Modes
- **Incorrect Angle Units:** Providing radians instead of degrees, as the API expects degrees for clockwise rotation.
- **Filter Misplacement:** Attempting to use the `Rotator` outside of the `ImageFilter` pipeline or applying it to a null `BufferedImage`.
- **Type Mismatch:** Passing an integer instead of a `double` may cause compilation issues if not explicitly cast, though Java handles this promotion automatically.

### Fix Code Hint
```java
// Ensure the angle is a double and the filter is added to the pipeline
double rotationAngle = 90.0;
Thumbnails.of(sourceImage)
    .size(100, 100)
    .addFilter(net.coobird.thumbnailator.filters.Rotation.newRotator(rotationAngle))
    .toFile("rotated_thumbnail.jpg");
```

## API Test: `java:net.coobird.thumbnailator.filters.SwapDimensions.apply`

### Signature
```java
(BufferedImage img):BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/SwapDimensions.java:44_

### Goal
Applies a transformation to a `BufferedImage` that swaps its width and height dimensions.

### Parameters
- `img` (`BufferedImage`): The source image to be processed.

### Input
- **Data:** A valid `BufferedImage` object.
- **Preconditions:** The image must be non-null. This filter is typically used as part of an `ImageFilter` pipeline to reorient images.

### Output
Returns `BufferedImage` — The resulting image with swapped width and height dimensions.

### Valid Call Patterns
```java
// Inferred from signature:
SwapDimensions filter = new SwapDimensions();
BufferedImage result = filter.apply(sourceImage);
```

### LLM Instruction Prompt
- Use `net.coobird.thumbnailator.filters.SwapDimensions` to perform a 90-degree dimension swap on a `BufferedImage`.
- Ensure the input `BufferedImage` is fully initialized before passing it to the `apply` method.
- This filter is intended for use within the Thumbnailator `ImageFilter` pipeline architecture.

### Prompt Snippet
```text
net.coobird.thumbnailator.filters.SwapDimensions filter = new net.coobird.thumbnailator.filters.SwapDimensions();
BufferedImage processedImage = filter.apply(sourceImage);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `img` parameter passed to the `apply` method is `null`.
- **Memory Constraints:** Swapping dimensions on very large `BufferedImage` objects may lead to `OutOfMemoryError` if the heap size is insufficient for the resulting image buffer.

### Fix Code Hint
```java
// Ensure the image is not null before applying the filter
if (sourceImage != null) {
    BufferedImage result = new SwapDimensions().apply(sourceImage);
}
```

## API Test: `java:net.coobird.thumbnailator.filters.SwapDimensions.getInstance`

### Signature
```java
():SwapDimensions
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/SwapDimensions.java:40_

### Goal
Retrieves a singleton instance of the `SwapDimensions` filter, which is used to transpose an image by swapping its width and height dimensions.

### Parameters
_None._

### Input
No input parameters are required. The filter is intended to be added to a `Pipeline` or passed to the `addFilter` method of a `Thumbnails.Builder` to perform the transformation on a `BufferedImage` during the processing chain.

### Output
Returns `SwapDimensions` — A filter instance that, when applied, swaps the width and height of the target `BufferedImage`.

### Valid Call Patterns
```java
// Inferred from signature:
SwapDimensions filter = net.coobird.thumbnailator.filters.SwapDimensions.getInstance();
```

### LLM Instruction Prompt
- Use `net.coobird.thumbnailator.filters.SwapDimensions.getInstance()` to obtain the filter instance.
- Do not attempt to instantiate `SwapDimensions` directly via `new`, as the constructor is not exposed; always use the `getInstance()` factory method.
- Chain this filter into a `Thumbnails.Builder` using the `.addFilter(...)` method.

### Prompt Snippet
```text
// Example of adding the SwapDimensions filter to a Thumbnailator pipeline
Thumbnails.of("input.jpg")
    .size(200, 200)
    .addFilter(net.coobird.thumbnailator.filters.SwapDimensions.getInstance())
    .toFile("output.jpg");
```

### Common Failure Modes
- **Pipeline Order:** Applying the filter before resizing may result in unexpected dimensions if the `Thumbnails.Builder` logic overrides the filter's output.
- **Null Pointer:** Attempting to use the filter without adding it to a valid `Thumbnails` pipeline or manually applying it to a null `BufferedImage`.
- **Memory Constraints:** While the filter itself is lightweight, applying it to extremely large images in a batch process without using `iterableBufferedImages()` may lead to `OutOfMemoryError`.

### Fix Code Hint
```java
// Ensure the filter is added to the builder chain correctly
Thumbnails.of(image)
    .addFilter(net.coobird.thumbnailator.filters.SwapDimensions.getInstance())
    .asBufferedImage();
```

## API Test: `java:net.coobird.thumbnailator.filters.Transparency`

### Signature
```java
(float alpha):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Transparency.java:58_

### Goal
Applies a uniform transparency (opacity) level to a `BufferedImage` by modifying its alpha channel.

### Parameters
- `alpha` (`float`): The opacity level of the resulting image, ranging from `0.0f` (fully transparent) to `1.0f` (fully opaque), inclusive.

### Input
- **Data:** A `BufferedImage` object processed within a `Thumbnailator` pipeline.
- **Preconditions:** The image must support an alpha channel for transparency to be visible. If the source image is in a format that does not support transparency (e.g., some JPEG types), the filter may not produce the expected visual result.

### Output
Returns `null` — The `Transparency` class implements the `ImageFilter` interface; the filter modifies the `BufferedImage` in-place or as part of the `Pipeline` processing chain.

### Valid Call Patterns
```java
// Inferred from API signature and usage patterns
new net.coobird.thumbnailator.filters.Transparency(0.5f);
```

### LLM Instruction Prompt
- When using `Transparency`, ensure the `alpha` value is a `float` between `0.0f` and `1.0f`.
- Always add this filter to a `Thumbnails` builder using the `.addFilter()` method within a fluent pipeline.
- Do not attempt to use the return value of the constructor as the image itself; it is a filter object to be passed into the pipeline.

### Prompt Snippet
```text
// Example of adding Transparency to a pipeline
Thumbnails.of("input.png")
    .size(200, 200)
    .addFilter(new net.coobird.thumbnailator.filters.Transparency(0.75f))
    .toFile("output.png");
```

### Common Failure Modes
- **IllegalArgumentException:** Thrown if the `alpha` value provided is outside the `0.0f` to `1.0f` range.
- **Visual Inconsistency:** Applying transparency to images saved as JPEG may result in unexpected background colors (usually black) because the JPEG format does not support alpha channels. Use PNG for images requiring transparency.

### Fix Code Hint
```java
// Ensure alpha is within bounds and output format supports transparency
float alphaValue = 0.5f;
if (alphaValue >= 0.0f && alphaValue <= 1.0f) {
    Thumbnails.of("input.png")
        .size(200, 200)
        .outputFormat("png")
        .addFilter(new net.coobird.thumbnailator.filters.Transparency(alphaValue))
        .toFile("output.png");
}
```

## API Test: `java:net.coobird.thumbnailator.filters.Transparency.apply`

### Signature
```java
(BufferedImage img):BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Transparency.java:85_

### Goal
Applies a transparency filter to a `BufferedImage`, modifying its alpha channel or opacity levels as defined by the `Transparency` filter instance.

### Parameters
- `img` (`BufferedImage`): The source image to which the transparency filter is applied.

### Input
- **Data:** A valid `BufferedImage` object.
- **Preconditions:** The `Transparency` object must be instantiated with the desired transparency level (e.g., an alpha value) before calling `apply`. The input `BufferedImage` should be in a format that supports transparency if the filter is intended to modify alpha channels.

### Output
Returns `BufferedImage` — The resulting image after the transparency filter has been applied.

### Valid Call Patterns
```java
// Example of applying the filter manually
Transparency filter = new Transparency(0.5f); // 50% transparency
BufferedImage result = filter.apply(sourceImage);
```
*(Note: This call pattern is inferred from the API signature and the `ImageFilter` interface implementation.)*

### LLM Instruction Prompt
- When using `java:net.coobird.thumbnailator.filters.Transparency.apply`, ensure the `Transparency` object is initialized with the appropriate alpha factor.
- Always pass a non-null `BufferedImage` to the `apply` method.
- The method returns a new or modified `BufferedImage`; ensure the returned reference is used for subsequent pipeline steps.

### Prompt Snippet
```text
// Example usage of Transparency filter
Transparency transparencyFilter = new Transparency(0.5f);
BufferedImage processedImage = transparencyFilter.apply(inputImage);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `img` parameter passed to the method is `null`.
- **Unsupported Image Type:** If the input `BufferedImage` type does not support alpha channels, the transparency effect may not be visible or may result in unexpected color artifacts.
- **Illegal State:** If the `Transparency` filter is not properly initialized with a valid alpha value, the resulting image may not reflect the intended transparency.

### Fix Code Hint
```java
// Ensure the image is not null and the filter is initialized
if (sourceImage != null) {
    Transparency filter = new Transparency(0.5f);
    BufferedImage result = filter.apply(sourceImage);
}
```

## API Test: `java:net.coobird.thumbnailator.filters.Transparency.getAlpha`

### Signature
```java
():float
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Transparency.java:113_

### Goal
Retrieves the current opacity level configured for a `Transparency` filter instance.

### Parameters
_None._

### Input
The caller must have an initialized instance of `net.coobird.thumbnailator.filters.Transparency`. No specific preconditions are required to query the alpha value, as it is a property of the filter object.

### Output
Returns `float` — The opacity value of the filter, represented as a float in the range of `0.0f` (fully transparent) to `1.0f` (fully opaque).

### Valid Call Patterns
```java
// Inferred from API signature:
Transparency transparencyFilter = new Transparency(0.5f);
float alpha = transparencyFilter.getAlpha();
```

### LLM Instruction Prompt
- When calling `getAlpha()`, ensure the receiver is an instance of `net.coobird.thumbnailator.filters.Transparency`.
- Do not assume the returned value is a percentage; it is a normalized float between 0.0 and 1.0.
- This method is a getter and does not modify the state of the filter or the image being processed.

### Prompt Snippet
```text
net.coobird.thumbnailator.filters.Transparency filter = ...;
float currentAlpha = filter.getAlpha();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `Transparency` filter instance has not been initialized before calling `getAlpha()`.
- **Misinterpretation of Range:** Assuming the return value is an integer or a byte (0-255) rather than the documented `0.0f` to `1.0f` float range.

### Fix Code Hint
```java
// Ensure the filter is instantiated before access
Transparency filter = new Transparency(0.75f);
if (filter != null) {
    float alpha = filter.getAlpha();
}
```

## API Test: `java:net.coobird.thumbnailator.filters.Watermark`

### Signature
```java
(Position position,BufferedImage watermarkImg,float opacity,int insets):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Watermark.java:76_

### Goal
Instantiates an image filter that overlays a provided `BufferedImage` watermark onto a target image at a specified position with defined opacity and padding.

### Parameters
- `position` (`Position`): The geometric placement of the watermark (e.g., `Positions.BOTTOM_RIGHT`).
- `watermarkImg` (`BufferedImage`): The source image to be used as the watermark overlay.
- `opacity` (`float`): The transparency level of the watermark, ranging from `0.0f` (completely transparent) to `1.0f` (completely opaque).
- `insets` (`int`): The padding size in pixels to apply around the watermark; must be a non-negative integer.

### Input
The caller must provide a valid `BufferedImage` for the watermark and a `Position` implementation. The target image being processed must be compatible with the `ImageFilter` pipeline. Precondition: `insets` cannot be negative.

### Output
Returns `null` (the constructor initializes the filter object for use within a `Thumbnails` pipeline).

### Valid Call Patterns
```java
// Inferred from signature and project usage patterns
new net.coobird.thumbnailator.filters.Watermark(
    Positions.BOTTOM_RIGHT, 
    watermarkImage, 
    0.5f, 
    10
);
```

### LLM Instruction Prompt
- Use this class to instantiate a filter for the `.addFilter()` method in the `Thumbnails` fluent API.
- Ensure the `opacity` is a `float` between `0.0f` and `1.0f`.
- Ensure the `insets` value is a non-negative `int`.
- Always use `net.coobird.thumbnailator.geometry.Positions` for the `position` argument.

### Prompt Snippet
```text
new net.coobird.thumbnailator.filters.Watermark(position, watermarkImg, opacity, insets)
```

### Common Failure Modes
- **Negative Insets:** Providing a negative integer for `insets` will result in an error.
- **Opacity Out of Bounds:** Providing an `opacity` value outside the `0.0f` to `1.0f` range may lead to unexpected rendering or runtime exceptions.
- **Null Images:** Passing a `null` `watermarkImg` will cause a `NullPointerException` during the filtering process.

### Fix Code Hint
```java
// Ensure non-negative insets and valid opacity
int padding = Math.max(0, userProvidedPadding);
float alpha = Math.max(0.0f, Math.min(1.0f, userProvidedOpacity));
new net.coobird.thumbnailator.filters.Watermark(Positions.CENTER, watermarkImg, alpha, padding);
```

## API Test: `java:net.coobird.thumbnailator.filters.Watermark.apply`

### Signature
```java
(BufferedImage img):BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/filters/Watermark.java:113_

### Goal
Applies a watermark image onto a source `BufferedImage` at a specified position and opacity level.

### Parameters
- `img` (`BufferedImage`): The source image to which the watermark will be applied.

### Input
- **Data:** A `BufferedImage` object.
- **Preconditions:** The `Watermark` filter instance must be initialized with a watermark image, a `Position` (e.g., `Positions.BOTTOM_RIGHT`), and an opacity value (0.0 to 1.0) before calling `apply`.
- **Formats:** Supports any `BufferedImage` type compatible with standard Java 2D graphics operations.

### Output
Returns `BufferedImage` — The resulting image containing the original source with the watermark composited onto it.

### Valid Call Patterns
```java
// Inferred from API signature and ImageFilter interface usage
Watermark watermarkFilter = new Watermark(Positions.BOTTOM_RIGHT, watermarkImage, 0.5f);
BufferedImage result = watermarkFilter.apply(sourceImage);
```

### LLM Instruction Prompt
- When using `Watermark.apply`, ensure the `Watermark` object is instantiated with valid `Position`, `BufferedImage` (watermark), and `float` (opacity) parameters.
- The `apply` method is part of the `ImageFilter` interface; it is intended to be used within a processing pipeline or directly on a `BufferedImage`.
- Do not attempt to modify the source `BufferedImage` directly if you require the original; `apply` returns a new or modified `BufferedImage`.

### Prompt Snippet
```text
// Example of applying a watermark filter
Watermark filter = new Watermark(Positions.CENTER, watermarkImg, 0.5f);
BufferedImage output = filter.apply(inputImage);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `img` parameter passed to `apply` is null.
- **IllegalStateException:** May occur if the internal watermark image or position configuration is invalid or null.
- **Graphics Configuration:** If the source `BufferedImage` type is incompatible with the watermark's alpha channel, unexpected color artifacts may appear; ensure the source image type supports transparency if the watermark has an alpha channel.

### Fix Code Hint
```java
// Ensure the watermark image is loaded and the filter is properly configured
if (sourceImage != null && watermarkImage != null) {
    Watermark filter = new Watermark(Positions.BOTTOM_RIGHT, watermarkImage, 0.5f);
    BufferedImage result = filter.apply(sourceImage);
}
```

## API Test: `java:net.coobird.thumbnailator.geometry.AbsoluteSize`

### Signature
```java
(int width,int height):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/geometry/AbsoluteSize.java:64_

### Goal
Instantiates an object that defines a fixed, absolute size for elements such as watermarks or captions within a thumbnail processing pipeline.

### Parameters
- `width` (`int`): The width of the object in pixels; must be greater than 0.
- `height` (`int`): The height of the object in pixels; must be greater than 0.

### Input
The caller must provide two positive integers representing the desired dimensions. This class is typically used when configuring filters (like `Watermark` or `Caption`) that require explicit sizing information rather than relative scaling.

### Output
Returns `null` (the constructor returns an instance of `net.coobird.thumbnailator.geometry.AbsoluteSize`). The resulting object represents a fixed-size constraint used by the Thumbnailator geometry engine.

### Valid Call Patterns
```java
// Example: Using AbsoluteSize to define dimensions for a watermark
AbsoluteSize size = new net.coobird.thumbnailator.geometry.AbsoluteSize(100, 50);
```

### LLM Instruction Prompt
- When generating code that requires a fixed size for image elements, use `new net.coobird.thumbnailator.geometry.AbsoluteSize(width, height)`.
- Ensure both `width` and `height` are positive integers.
- Do not use this class if you require dimensions that scale relative to the source image; use `RelativeSize` instead.

### Prompt Snippet
```text
net.coobird.thumbnailator.geometry.AbsoluteSize size = new net.coobird.thumbnailator.geometry.AbsoluteSize(width, height);
```

### Common Failure Modes
- `IllegalArgumentException`: Thrown if either `width` or `height` is less than or equal to 0.
- `NullPointerException`: While not applicable to this specific constructor, ensure that any `Dimension` objects used in the overloaded constructor are not null.

### Fix Code Hint
```java
// Ensure dimensions are positive
int w = Math.max(1, requestedWidth);
int h = Math.max(1, requestedHeight);
AbsoluteSize size = new net.coobird.thumbnailator.geometry.AbsoluteSize(w, h);
```

## API Test: `java:net.coobird.thumbnailator.geometry.AbsoluteSize.calculate`

### Signature
```java
(int width,int height):Dimension
```
_Source: source/src/main/java/net/coobird/thumbnailator.geometry.AbsoluteSize.java:73_

### Goal
Calculates the fixed dimensions of an object based on the provided enclosing container dimensions.

### Parameters
- `width` (`int`): The width of the enclosing container or parent object.
- `height` (`int`): The height of the enclosing container or parent object.

### Input
The caller must provide two integers representing the dimensions of the container. This method is typically used within the Thumbnailator geometry framework to determine the size of elements (like watermarks) that have been configured with an absolute size, regardless of the input image's actual dimensions.

### Output
Returns `Dimension` — A `java.awt.Dimension` object containing the fixed width and height defined for the `AbsoluteSize` instance, ignoring the input container dimensions.

### Valid Call Patterns
```java
// Inferred from API signature:
AbsoluteSize size = new AbsoluteSize(new Dimension(100, 50));
Dimension result = size.calculate(800, 600); // Returns Dimension(100, 50)
```

### LLM Instruction Prompt
- When using `AbsoluteSize.calculate`, remember that this class implements the `Size` interface to provide a fixed, non-scaling dimension. The `width` and `height` arguments passed to `calculate` represent the enclosing container, but `AbsoluteSize` will return its pre-configured fixed dimensions regardless of these inputs.

### Prompt Snippet
```text
// Example of using AbsoluteSize to define a fixed-size watermark placement
AbsoluteSize absoluteSize = new AbsoluteSize(new Dimension(50, 50));
Dimension dim = absoluteSize.calculate(enclosingWidth, enclosingHeight);
```

### Common Failure Modes
- **Misunderstanding behavior:** Expecting the method to scale or adjust based on the input `width` and `height`. `AbsoluteSize` is designed to return a constant size; use `RelativeSize` if dynamic scaling based on the container is required.
- **Null Pointer:** If the `AbsoluteSize` object itself is not initialized correctly, calling `calculate` on a null reference will throw a `NullPointerException`.

### Fix Code Hint
```java
// If you need the size to change based on the input image, use RelativeSize instead:
Size size = new RelativeSize(0.5, 0.5); // 50% of the container
Dimension dim = size.calculate(800, 600); // Returns Dimension(400, 300)
```

## API Test: `java:net.coobird.thumbnailator.geometry.Coordinate`

### Signature
```java
(int x,int y):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/geometry/Coordinate.java:56_

### Goal
Instantiates a `Coordinate` object that calculates the specific position of an image element based on fixed horizontal and vertical offsets.

### Parameters
- `x` (`int`): The horizontal component (offset) of the top-left corner of the image to be enclosed.
- `y` (`int`): The vertical component (offset) of the top-left corner of the image to be enclosed.

### Input
The caller must provide two integer values representing the absolute pixel coordinates. This class is typically used within the Thumbnailator geometry framework to define placement for watermarks or captions.

### Output
Returns `null` (Constructor). The resulting object represents a fixed coordinate position used by the library to determine where to render secondary image elements relative to the primary thumbnail.

### Valid Call Patterns
```java
// Inferred from signature:
net.coobird.thumbnailator.geometry.Coordinate coord = new net.coobird.thumbnailator.geometry.Coordinate(10, 10);
```

### LLM Instruction Prompt
- When using `net.coobird.thumbnailator.geometry.Coordinate`, ensure the coordinates provided are within the bounds of the target image to avoid unexpected clipping or placement issues.
- Use this class when absolute positioning is required, as opposed to the relative positioning provided by the `Positions` enum.

### Prompt Snippet
```text
net.coobird.thumbnailator.geometry.Coordinate coord = new net.coobird.thumbnailator.geometry.Coordinate(x, y);
```

### Common Failure Modes
- Providing negative coordinates, which may result in the element being rendered outside the visible bounds of the thumbnail.
- Assuming the coordinate system is relative; this class represents absolute pixel offsets from the top-left (0,0) corner of the image.

### Fix Code Hint
```java
// Ensure coordinates are non-negative and within the target image dimensions
int x = Math.max(0, requestedX);
int y = Math.max(0, requestedY);
net.coobird.thumbnailator.geometry.Coordinate coord = new net.coobird.thumbnailator.geometry.Coordinate(x, y);
```

## API Test: `java:net.coobird.thumbnailator.geometry.Coordinate.calculate`

### Signature
```java
(int enclosingWidth,int enclosingHeight,int width,int height,int insetLeft,int insetRight,int insetTop,int insetBottom):Point
```
_Source: source/src/main/java/net/coobird/thumbnailator/geometry/Coordinate.java:61_

### Goal
Calculates the specific `Point` coordinates for placing an object within an enclosing container, accounting for specified directional insets.

### Parameters
- `enclosingWidth` (`int`): The width of the container object.
- `enclosingHeight` (`int`): The height of the container object.
- `width` (`int`): The width of the object to be placed.
- `height` (`int`): The height of the object to be placed.
- `insetLeft` (`int`): The padding/inset on the left side of the object.
- `insetRight` (`int`): The padding/inset on the right side of the object.
- `insetTop` (`int`): The padding/inset on the top side of the object.
- `insetBottom` (`int`): The padding/inset on the bottom side of the object.

### Input
The caller must provide integer dimensions for both the container and the object, along with integer values for the four directional insets. This method is a utility for geometry calculations and does not perform I/O; it assumes the caller has already determined the dimensions of the images or elements involved.

### Output
Returns `Point` — A `java.awt.Point` object representing the calculated (x, y) coordinates where the top-left corner of the enclosed object should be positioned.

### Valid Call Patterns
```java
// Example of calculating a position for an object within a 800x600 container
// with 10px insets on all sides.
java.awt.Point p = new net.coobird.thumbnailator.geometry.Coordinate().calculate(800, 600, 100, 100, 10, 10, 10, 10);
```

### LLM Instruction Prompt
- When using `net.coobird.thumbnailator.geometry.Coordinate.calculate`, ensure all dimensions and insets are provided as non-negative integers.
- Note that this method is part of the `geometry` package and is used to determine placement; it does not modify images directly.
- Always instantiate `net.coobird.thumbnailator.geometry.Coordinate` before calling the method.

### Prompt Snippet
```text
net.coobird.thumbnailator.geometry.Coordinate coord = new net.coobird.thumbnailator.geometry.Coordinate();
java.awt.Point p = coord.calculate(enclosingWidth, enclosingHeight, width, height, insetLeft, insetRight, insetTop, insetBottom);
```

### Common Failure Modes
- Passing negative values for dimensions or insets, which may result in unexpected coordinate calculations outside the bounds of the enclosing object.
- Assuming the method performs image processing; it is a pure geometry calculation utility and does not interact with `BufferedImage` or `ThumbnailParameter` objects.

### Fix Code Hint
```java
// Ensure dimensions are positive and logical
int x = Math.max(0, enclosingWidth);
int y = Math.max(0, enclosingHeight);
java.awt.Point p = new net.coobird.thumbnailator.geometry.Coordinate().calculate(x, y, w, h, left, right, top, bottom);
```

## API Test: `java:net.coobird.thumbnailator.geometry.Region`

### Signature
```java
(Position position,Size size):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/geometry/Region.java:59_

### Goal
Instantiates a representation of a specific rectangular area within an image, defined by a coordinate-based `Position` and a `Size` constraint.

### Parameters
- `position` (`Position`): The anchor point or coordinate strategy used to determine the top-left origin of the region (e.g., `Positions.CENTER`, `Positions.TOP_LEFT`).
- `size` (`Size`): The dimensions of the region, defining the width and height (e.g., `AbsoluteSize` or `RelativeSize`).

### Input
The caller must provide valid `Position` and `Size` objects. There are no specific file format requirements as this is an in-memory geometry definition. Precondition: Both arguments must be non-null.

### Output
Returns `null` (Constructor). The object represents a geometric region used for operations such as cropping or defining areas for filters.

### Valid Call Patterns
```java
// Inferred from signature:
Region region = new Region(Positions.CENTER, new AbsoluteSize(100, 100));
```

### LLM Instruction Prompt
- When using `Region`, ensure the `Position` and `Size` objects are instantiated before passing them to the constructor.
- Do not pass `null` values, as this will trigger a `NullPointerException`.
- Use this class when you need to define a specific sub-area of an image for processing tasks.

### Prompt Snippet
```text
net.coobird.thumbnailator.geometry.Region region = new net.coobird.thumbnailator.geometry.Region(position, size);
```

### Common Failure Modes
- `NullPointerException`: Occurs if either the `position` or `size` argument is `null`.
- Logic errors: Providing a `Size` that exceeds the dimensions of the source image may lead to unexpected behavior depending on the downstream filter or operation using the `Region`.

### Fix Code Hint
```java
// Ensure non-null arguments
if (position != null && size != null) {
    Region region = new Region(position, size);
}
```

## API Test: `java:net.coobird.thumbnailator.geometry.Region.calculate`

### Signature
```java
(int outerWidth,int outerHeight,boolean flipHorizontal,boolean flipVertical,boolean swapDimensions):Rectangle
```
_Source: source/src/main/java/net/coobird/thumbnailator/geometry/Region.java:109_

### Goal
Calculates the intersection of an enclosed region within an enclosing region, applying optional transformations such as flipping and dimension swapping.

### Parameters
- `outerWidth` (`int`): The width of the enclosing region.
- `outerHeight` (`int`): The height of the enclosing region.
- `flipHorizontal` (`boolean`): Whether the enclosed region should be flipped horizontally within the enclosing region.
- `flipVertical` (`boolean`): Whether the enclosed region should be flipped vertically within the enclosing region.
- `swapDimensions` (`boolean`): Whether the components of the point and dimension of the enclosed region should be swapped.

### Input
The caller must provide the dimensions of the enclosing container. The method operates on the internal state of the `Region` instance, which defines the enclosed area. Preconditions include ensuring the `Region` instance is properly initialized before invoking this calculation.

### Output
Returns `Rectangle` — The resulting rectangle representing the intersection of the enclosed region and the enclosing region, adjusted by the specified transformation flags.

### Valid Call Patterns
```java
// Example inferred from the API signature
Rectangle rect = region.calculate(800, 600, false, false, false);
```

### LLM Instruction Prompt
- When calling `calculate`, ensure the `outerWidth` and `outerHeight` represent the bounds of the container.
- Use `flipHorizontal` and `flipVertical` to mirror the region's position relative to the container's axes.
- Set `swapDimensions` to `true` if the region needs to be rotated 90 degrees (swapping width/height and x/y coordinates).
- The returned `Rectangle` is the intersection; portions of the enclosed region falling outside the `outerWidth` and `outerHeight` are ignored.

### Prompt Snippet
```text
// Calculate the effective region within a 1024x768 container
Rectangle result = region.calculate(1024, 768, false, false, false);
```

### Common Failure Modes
- **Invalid Dimensions:** Passing negative values for `outerWidth` or `outerHeight` may result in an empty or invalid `Rectangle` intersection.
- **Instance State:** Calling this method on an uninitialized `Region` object may lead to unexpected coordinate calculations.
- **Coordinate Clipping:** If the enclosed region is entirely outside the bounds of the `outerWidth` and `outerHeight`, the returned `Rectangle` will have zero width or height.

### Fix Code Hint
```java
// Ensure dimensions are non-negative before calculation
if (outerWidth > 0 && outerHeight > 0) {
    Rectangle rect = region.calculate(outerWidth, outerHeight, flipH, flipV, swap);
}
```

## API Test: `java:net.coobird.thumbnailator.geometry.Region.getPosition`

### Signature
```java
():Position
```
_Source: source/src/main/java/net/coobird/thumbnailator/geometry/Region.java:77_

### Goal
Retrieves the `Position` object associated with a `Region`, defining the anchor point or placement strategy used for operations like watermarking or cropping.

### Parameters
_None._

### Input
The caller must have an initialized `net.coobird.thumbnailator.geometry.Region` object. No specific preconditions are required to invoke this getter, as it simply returns the state already encapsulated within the `Region` instance.

### Output
Returns `Position` — The `Position` object representing the geometric placement strategy (e.g., `Positions.CENTER`, `Positions.TOP_LEFT`) defined for the region.

### Valid Call Patterns
```java
// Inferred from signature:
Region region = new Region(Positions.CENTER, new Size(100, 100));
Position pos = region.getPosition();
```

### LLM Instruction Prompt
- When calling `getPosition()`, ensure the `Region` object has been instantiated with a valid `Position` implementation (such as those provided by the `Positions` enum).
- Do not assume the returned `Position` is mutable; treat the returned object as a read-only reference to the region's anchor configuration.

### Prompt Snippet
```text
Region myRegion = new Region(Positions.BOTTOM_RIGHT, new AbsoluteSize(50, 50));
Position currentPos = myRegion.getPosition();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `Region` instance itself is null.
- **Logic Errors:** If the `Region` was initialized with a custom `Position` implementation that does not correctly calculate coordinates relative to the image bounds, `getPosition()` will return that specific (potentially flawed) implementation.

### Fix Code Hint
```java
// Ensure the Region is properly initialized before accessing its position
if (region != null) {
    Position pos = region.getPosition();
    // Proceed with using the position
}
```

## API Test: `java:net.coobird.thumbnailator.geometry.Region.getSize`

### Signature
```java
():Size
```
_Source: source/src/main/java/net/coobird/thumbnailator/geometry/Region.java:86_

### Goal
Retrieves the dimensions of the defined `Region` object, which represents a specific area within an image used for operations like cropping or watermarking.

### Parameters
_None._

### Input
The `Region` object must be initialized with valid geometric bounds. No specific preconditions are required to call this method, as it simply returns the state of the existing `Region` instance.

### Output
Returns `Size` — An object representing the width and height of the region.

### Valid Call Patterns
```java
// Inferred from signature:
Region region = new Region(Positions.CENTER, new AbsoluteSize(100, 100));
Size size = region.getSize();
```

### LLM Instruction Prompt
- Use `getSize()` only on an instantiated `net.coobird.thumbnailator.geometry.Region` object.
- Do not assume the returned `Size` object is mutable; treat it as a read-only representation of the region's dimensions.
- Ensure the `Region` has been properly constructed with a `Position` and a `Size` implementation (e.g., `AbsoluteSize` or `RelativeSize`) before invoking this method.

### Prompt Snippet
```text
// Example of retrieving region dimensions
Region myRegion = new Region(Positions.TOP_LEFT, new AbsoluteSize(50, 50));
Size regionSize = myRegion.getSize();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `Region` instance itself is null.
- **Logic Errors:** If the `Region` was initialized with a `RelativeSize` that depends on a parent image, the `Size` returned by `getSize()` may not reflect the final pixel dimensions until the region is applied to a specific source image.

### Fix Code Hint
```java
// Ensure the region is fully defined before accessing its size
if (region != null) {
    Size s = region.getSize();
    // Proceed with processing using s.getWidth() and s.getHeight()
}
```

## API Test: `java:net.coobird.thumbnailator.geometry.RelativeSize`

### Signature
```java
(double scalingFactor):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/geometry/RelativeSize.java:52_

### Goal
Instantiates a `RelativeSize` object that calculates the dimensions of an element (such as a watermark) relative to the size of the target image using a specified scaling factor.

### Parameters
- `scalingFactor` (`double`): The ratio used to determine the size of the object relative to the source image; must be a value between 0.0 and 1.0, inclusive.

### Input
- **Data:** A `double` value representing the percentage of the base image size (e.g., `0.5` for 50%).
- **Preconditions:** The `scalingFactor` must be within the range `[0.0, 1.0]`. This class is typically used in conjunction with filters like `Watermark` to define dynamic sizing based on the primary image dimensions.

### Output
Returns `null` — The constructor initializes the `RelativeSize` instance, which is then passed to methods requiring a `Size` implementation to calculate dimensions dynamically at runtime.

### Valid Call Patterns
```java
// Example: Using RelativeSize to scale a watermark to 50% of the base image size
new RelativeSize(0.5);
```

### LLM Instruction Prompt
- When using `RelativeSize`, ensure the `scalingFactor` is a `double` literal or variable between `0.0` and `1.0`.
- Use this class when you need the size of an overlay or watermark to scale proportionally with the thumbnail rather than remaining a fixed pixel size.
- Pass the instantiated `RelativeSize` object to constructors or methods that accept the `net.coobird.thumbnailator.geometry.Size` interface.

### Prompt Snippet
```text
new net.coobird.thumbnailator.geometry.RelativeSize(scalingFactor)
```

### Common Failure Modes
- **IllegalArgumentException:** Thrown if the `scalingFactor` is less than `0.0` or greater than `1.0`.
- **Logic Errors:** Providing a `scalingFactor` of `0.0` will result in an object with zero dimensions, potentially causing issues in rendering filters.

### Fix Code Hint
```java
// Ensure the scaling factor is clamped between 0.0 and 1.0
double factor = Math.max(0.0, Math.min(1.0, inputFactor));
Size size = new RelativeSize(factor);
```

## API Test: `java:net.coobird.thumbnailator.geometry.RelativeSize.calculate`

### Signature
```java
(int width,int height):Dimension
```
_Source: source/src/main/java/net/coobird/thumbnailator/geometry/RelativeSize.java:62_

### Goal
Calculates the dimensions of an object relative to an enclosing area, typically used for determining the size of watermarks or other elements placed within a thumbnail.

### Parameters
- `width` (`int`): The width of the enclosing area (e.g., the width of the source image).
- `height` (`int`): The height of the enclosing area (e.g., the height of the source image).

### Input
The caller must provide the dimensions of the parent container. This method is typically invoked internally by the Thumbnailator pipeline when applying geometry-based filters like `Watermark`.

### Output
Returns `Dimension` — A `java.awt.Dimension` object representing the calculated width and height based on the relative scaling factor defined by the `RelativeSize` instance.

### Valid Call Patterns
```java
// Inferred from API signature:
RelativeSize relativeSize = new RelativeSize(0.5); // Example: 50% of parent
Dimension dim = relativeSize.calculate(800, 600);
```

### LLM Instruction Prompt
- When using `RelativeSize.calculate`, ensure the `RelativeSize` object has been initialized with the desired scaling factor (e.g., `new RelativeSize(double)`).
- Pass the dimensions of the target image as the `width` and `height` arguments.
- The returned `Dimension` object is a standard `java.awt.Dimension` and can be used for further layout or drawing operations.

### Prompt Snippet
```text
RelativeSize sizeCalculator = new RelativeSize(0.25);
Dimension result = sizeCalculator.calculate(sourceWidth, sourceHeight);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `RelativeSize` instance was not properly initialized.
- **Invalid Dimensions:** Passing negative values for `width` or `height` may result in unexpected `Dimension` values depending on the internal scaling logic.
- **Logic Mismatch:** Using `RelativeSize` when an `AbsoluteSize` is required for fixed-pixel placement.

### Fix Code Hint
```java
// Ensure the scaling factor is set correctly before calculation
RelativeSize relativeSize = new RelativeSize(0.5); 
Dimension calculated = relativeSize.calculate(img.getWidth(), img.getHeight());
```

## API Test: `java:net.coobird.thumbnailator.makers.FixedSizeThumbnailMaker`

### Signature
```java
(int width,int height,boolean aspectRatio,boolean fit):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/FixedSizeThumbnailMaker.java:158_

### Goal
Creates a `FixedSizeThumbnailMaker` instance configured to produce thumbnails with specific dimensions, aspect ratio preservation, and fitting logic.

### Parameters
- `width` (`int`): The target width of the thumbnail in pixels.
- `height` (`int`): The target height of the thumbnail in pixels.
- `aspectRatio` (`boolean`): If `true`, the thumbnail maintains the aspect ratio of the original image.
- `fit` (`boolean`): If `true`, the thumbnail is sized to fit within the specified width and height dimensions.

### Input
- **Data:** Requires a `BufferedImage` source to be passed to the `make(BufferedImage)` method of the resulting instance.
- **Preconditions:** The maker must be instantiated with valid dimensions. While this constructor sets the parameters, ensure the source image is not null before calling `make()`.

### Output
Returns `null` (the constructor returns the instance of `FixedSizeThumbnailMaker`). The `make()` method of the resulting object returns a `BufferedImage` representing the processed thumbnail.

### Valid Call Patterns
```java
// Inferred from constructor signature
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker(200, 200, true, true);
BufferedImage thumbnail = maker.make(sourceImage);
```

### LLM Instruction Prompt
- Use this constructor when you need explicit control over the resizing logic (aspect ratio and fitting) outside of the fluent `Thumbnails` builder API.
- Always follow instantiation with a call to the `.make(BufferedImage)` method to generate the actual thumbnail.

### Prompt Snippet
```text
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker(width, height, aspectRatio, fit);
BufferedImage result = maker.make(sourceImage);
```

### Common Failure Modes
- **NullPointerException:** Occurs if `make()` is called with a null `BufferedImage`.
- **IllegalStateException:** May occur if the internal state of the maker is not fully initialized or if the source image is incompatible with the requested dimensions.
- **Memory Issues:** Processing very large images without using memory-efficient workflows can lead to `OutOfMemoryError`.

### Fix Code Hint
```java
// Ensure the source image is loaded and valid before processing
if (sourceImage != null) {
    FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker(200, 200, true, true);
    BufferedImage thumbnail = maker.make(sourceImage);
}
```

## API Test: `java:net.coobird.thumbnailator.makers.FixedSizeThumbnailMaker.fitWithinDimensions`

### Signature
```java
(boolean fit):FixedSizeThumbnailMaker
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/FixedSizeThumbnailMaker.java:252_

### Goal
Configures whether the `FixedSizeThumbnailMaker` should constrain the resulting thumbnail dimensions to fit within the specified size while preserving the original image's aspect ratio.

### Parameters
- `fit` (`boolean`): If `true`, the thumbnail is scaled to fit within the defined dimensions while maintaining the original aspect ratio. If `false`, the larger dimension is aligned to the specified limit, potentially causing the other dimension to exceed the target bounds.

### Input
- **Preconditions:** The `FixedSizeThumbnailMaker` must have its target size set (e.g., via `.size(width, height)`) before processing.
- **Compatibility:** This method has no effect if `keepAspectRatio(false)` has been called, as the output will strictly match the defined dimensions regardless of this setting.
- **Data:** Operates on `BufferedImage` objects provided to the `make()` method.

### Output
Returns `FixedSizeThumbnailMaker` — A reference to the current `FixedSizeThumbnailMaker` instance, allowing for fluent method chaining.

### Valid Call Patterns
```java
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.size(200, 200)
     .fitWithinDimensions(true)
     .make(bufferedImage);
```

### LLM Instruction Prompt
- When using `FixedSizeThumbnailMaker`, always chain `fitWithinDimensions(boolean)` after setting the target size. Ensure that `keepAspectRatio(boolean)` is configured appropriately, as `fitWithinDimensions` is only relevant when aspect ratio preservation is enabled.

### Prompt Snippet
```text
// Example of configuring fitWithinDimensions
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.size(width, height).fitWithinDimensions(true).make(image);
```

### Common Failure Modes
- **IllegalStateException:** Thrown if `fitWithinDimensions` is called after the aspect ratio configuration has already been finalized or if the maker state is inconsistent.
- **Ignoring Settings:** If `keepAspectRatio(false)` is set, the `fitWithinDimensions` call will be ignored, which may lead to unexpected output dimensions if the developer assumes the fit logic is active.

### Fix Code Hint
```java
// Ensure aspect ratio is enabled for fitWithinDimensions to take effect
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.size(100, 100)
     .keepAspectRatio(true)
     .fitWithinDimensions(true)
     .make(sourceImage);
```

## API Test: `java:net.coobird.thumbnailator.makers.FixedSizeThumbnailMaker.keepAspectRatio`

### Signature
```java
(boolean keep):FixedSizeThumbnailMaker
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/FixedSizeThumbnailMaker.java:215_

### Goal
Configures the `FixedSizeThumbnailMaker` to either preserve or ignore the original image's aspect ratio during the thumbnail generation process.

### Parameters
- `keep` (`boolean`): If `true`, the resulting thumbnail will maintain the same aspect ratio as the source image; if `false`, the image will be resized to the exact dimensions specified, potentially resulting in distortion.

### Input
- **Preconditions:** The `FixedSizeThumbnailMaker` instance must have its target size defined (via `size(int, int)`) before processing.
- **Data:** A `BufferedImage` source provided to the `make(BufferedImage)` method.
- **Constraints:** This method can only be called once per `FixedSizeThumbnailMaker` instance; subsequent calls will result in an `IllegalStateException`.

### Output
Returns `FixedSizeThumbnailMaker` — A reference to the current `FixedSizeThumbnailMaker` instance, allowing for fluent method chaining.

### Valid Call Patterns
```java
BufferedImage img = ImageIO.read(new File("sourceImage.jpg"));
BufferedImage thumbnail = new FixedSizeThumbnailMaker()
    .size(200, 200)
    .keepAspectRatio(true)
    .make(img);
```

### LLM Instruction Prompt
- When using `FixedSizeThumbnailMaker`, always chain `keepAspectRatio` after defining the target dimensions with `size()`. Ensure the `boolean` argument is explicitly set to `true` if the original image proportions must be maintained.

### Prompt Snippet
```text
// Example of configuring aspect ratio preservation
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.size(width, height).keepAspectRatio(true);
```

### Common Failure Modes
- **IllegalStateException:** Occurs if `keepAspectRatio` is called more than once on the same `FixedSizeThumbnailMaker` instance.
- **Distortion:** If `keepAspectRatio(false)` is used, the output image will be stretched or squashed to fit the exact dimensions provided to `size()`.
- **Missing Size:** Calling `make()` before setting the dimensions via `size()` will result in an error, as the maker requires defined bounds.

### Fix Code Hint
```java
// Ensure the maker is configured in a single fluent chain to avoid state errors
BufferedImage thumbnail = new FixedSizeThumbnailMaker()
    .size(width, height)
    .keepAspectRatio(true)
    .make(sourceImage);
```

## API Test: `java:net.coobird.thumbnailator.makers.FixedSizeThumbnailMaker.make`

### Signature
```java
(BufferedImage img):BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/FixedSizeThumbnailMaker.java:264_

### Goal
Generates a thumbnail from a source `BufferedImage` by applying fixed-dimension resizing logic configured via the `FixedSizeThumbnailMaker` instance.

### Parameters
- `img` (`BufferedImage`): The source image to be processed into a thumbnail.

### Input
- **Data:** A valid `BufferedImage` object.
- **Preconditions:** The `FixedSizeThumbnailMaker` instance must have its target size configured (e.g., via `.size(width, height)`) prior to calling `make()`.
- **Formats:** Supports any image format compatible with `BufferedImage` and the Java Image I/O API.

### Output
Returns `BufferedImage` — A new `BufferedImage` containing the resized thumbnail, processed according to the maker's current configuration (e.g., aspect ratio settings).

### Valid Call Patterns
```java
BufferedImage img = ImageIO.read(new File("sourceImage.jpg"));
BufferedImage thumbnail = new FixedSizeThumbnailMaker()
    .size(200, 200)
    .keepAspectRatio(true)
    .make(img);
```

### LLM Instruction Prompt
- When using `FixedSizeThumbnailMaker`, always chain the `.size(int, int)` method before calling `.make(BufferedImage)`.
- Ensure the source `BufferedImage` is not null.
- Use this maker when specific target dimensions are required, as opposed to scaling by a factor.

### Prompt Snippet
```text
// Example of using FixedSizeThumbnailMaker to generate a 200x200 thumbnail
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.size(200, 200);
BufferedImage result = maker.make(sourceImage);
```

### Common Failure Modes
- **IllegalStateException:** Occurs if `make()` is called before the required size parameters have been set on the maker instance.
- **NullPointerException:** Occurs if the `img` argument passed to `make()` is null.
- **Memory Issues:** Processing very large images in memory can lead to `OutOfMemoryError`; consider using `Thumbnailator`'s fluent API with `iterableBufferedImages()` for batch processing.

### Fix Code Hint
```java
// Ensure size is set before calling make
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.size(width, height); 
BufferedImage thumbnail = maker.make(sourceImage);
```

## API Test: `java:net.coobird.thumbnailator.makers.FixedSizeThumbnailMaker.size`

### Signature
```java
(int width,int height):FixedSizeThumbnailMaker
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/FixedSizeThumbnailMaker.java:176_

### Goal
Sets the target dimensions for the thumbnail generation process within a `FixedSizeThumbnailMaker` instance.

### Parameters
- `width` (`int`): The desired width of the thumbnail in pixels; must be greater than zero.
- `height` (`int`): The desired height of the thumbnail in pixels; must be greater than zero.

### Input
The caller must provide a `FixedSizeThumbnailMaker` instance. The dimensions must be positive integers. Precondition: The size must be set before calling the `make(BufferedImage)` method.

### Output
Returns `FixedSizeThumbnailMaker` — A reference to the current `FixedSizeThumbnailMaker` instance, allowing for method chaining.

### Valid Call Patterns
```java
BufferedImage img = ImageIO.read(new File("sourceImage.jpg"));
BufferedImage thumbnail = new FixedSizeThumbnailMaker()
    .size(200, 200)
    .keepAspectRatio(true)
    .make(img);
```

### LLM Instruction Prompt
- When using `FixedSizeThumbnailMaker`, always call `.size(width, height)` before invoking `.make(BufferedImage)`.
- Ensure both `width` and `height` are positive integers.
- Do not attempt to call `.size()` more than once on the same `FixedSizeThumbnailMaker` instance.

### Prompt Snippet
```text
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.size(width, height);
```

### Common Failure Modes
- `IllegalStateException`: Thrown if `.size()` is called more than once on the same instance.
- `IllegalStateException`: Thrown if `width` or `height` is less than or equal to zero.
- `IllegalStateException`: Thrown if `.make()` is called before `.size()` has been configured.

### Fix Code Hint
```java
// Ensure size is set exactly once and with positive dimensions
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker();
if (width > 0 && height > 0) {
    maker.size(width, height);
}
```

## API Test: `java:net.coobird.thumbnailator.makers.ScaledThumbnailMaker`

### Signature
```java
(double widthFactor,double heightFactor):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ScaledThumbnailMaker.java:128_

### Goal
Creates a `ScaledThumbnailMaker` instance configured to resize images by applying independent scaling factors to the width and height dimensions.

### Parameters
- `widthFactor` (`double`): The scaling factor to apply to the image width (e.g., 0.5 for 50% of original width).
- `heightFactor` (`double`): The scaling factor to apply to the image height (e.g., 0.5 for 50% of original height).

### Input
The caller must provide two `double` values representing the desired scaling ratios. This constructor is used when non-uniform scaling is required. The resulting object is intended to be used with the `make(BufferedImage)` method to process an in-memory `BufferedImage`.

### Output
Returns `null` (constructor). The instance created is a `ScaledThumbnailMaker` configured for the specified width and height scaling factors.

### Valid Call Patterns
```java
// Inferred from constructor signature
net.coobird.thumbnailator.makers.ScaledThumbnailMaker maker = 
    new net.coobird.thumbnailator.makers.ScaledThumbnailMaker(0.5, 0.5);
BufferedImage thumbnail = maker.make(sourceImage);
```

### LLM Instruction Prompt
- Use this constructor only when you need to specify distinct scaling factors for width and height.
- If uniform scaling is required, consider using the `ScaledThumbnailMaker(double factor)` constructor instead.
- Always follow the instantiation with a call to the `make(BufferedImage)` method to perform the actual transformation.

### Prompt Snippet
```text
net.coobird.thumbnailator.makers.ScaledThumbnailMaker maker = new net.coobird.thumbnailator.makers.ScaledThumbnailMaker(widthFactor, heightFactor);
BufferedImage result = maker.make(sourceImage);
```

### Common Failure Modes
- **IllegalStateExceptions:** Attempting to use the maker without providing a valid `BufferedImage` to the `make()` method.
- **Invalid Scaling Factors:** Providing negative values or zero as scaling factors may result in unexpected behavior or empty images depending on the underlying `Resizer` implementation.
- **Memory Constraints:** Processing very large images with high scaling factors may lead to `OutOfMemoryError` in constrained environments.

### Fix Code Hint
```java
// Ensure scaling factors are positive and appropriate for the target memory footprint
double wFactor = 0.5;
double hFactor = 0.5;
net.coobird.thumbnailator.makers.ScaledThumbnailMaker maker = 
    new net.coobird.thumbnailator.makers.ScaledThumbnailMaker(wFactor, hFactor);
BufferedImage thumbnail = maker.make(sourceImage);
```

## API Test: `java:net.coobird.thumbnailator.makers.ScaledThumbnailMaker.make`

### Signature
```java
(BufferedImage img):BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ScaledThumbnailMaker.java:184_

### Goal
Generates a thumbnail from a source `BufferedImage` by applying a scaling factor defined by the `ScaledThumbnailMaker` instance.

### Parameters
- `img` (`BufferedImage`): The source image to be resized.

### Input
- **Data:** A valid `BufferedImage` object.
- **Preconditions:** The `ScaledThumbnailMaker` instance must have its scaling factor configured (e.g., via `.scale(double)`) prior to calling `make()`.
- **Formats:** Supports any `BufferedImage` type compatible with the Java 2D API.

### Output
Returns `BufferedImage` — A new `BufferedImage` containing the scaled version of the source image, processed according to the maker's configuration.

### Valid Call Patterns
```java
BufferedImage img = ImageIO.read(new File("sourceImage.jpg"));
BufferedImage thumbnail = new ScaledThumbnailMaker()
    .scale(0.25)
    .make(img);
```

### LLM Instruction Prompt
- When using `ScaledThumbnailMaker`, ensure the `.scale(double)` method is invoked on the builder instance before calling `make(BufferedImage)`.
- Do not reuse the `ScaledThumbnailMaker` instance for different scaling configurations if thread safety is a concern, as the maker maintains internal state.
- The `make` method is intended for direct, synchronous processing of in-memory images.

### Prompt Snippet
```text
// Example of using ScaledThumbnailMaker to resize an image by a factor of 0.25
ScaledThumbnailMaker maker = new ScaledThumbnailMaker();
maker.scale(0.25);
BufferedImage result = maker.make(sourceBufferedImage);
```

### Common Failure Modes
- **IllegalStateException:** Occurs if `make()` is called without first defining a scale factor.
- **NullPointerException:** Occurs if the provided `BufferedImage` argument is `null`.
- **Memory Issues:** Processing extremely large `BufferedImage` objects may lead to `OutOfMemoryError` in heap-constrained environments; consider using `Thumbnailator`'s fluent API with `iterableBufferedImages()` for batch operations.

### Fix Code Hint
```java
// Ensure the scale is set before calling make
ScaledThumbnailMaker maker = new ScaledThumbnailMaker();
if (scaleFactor > 0) {
    maker.scale(scaleFactor);
    BufferedImage result = maker.make(sourceImage);
}
```

## API Test: `java:net.coobird.thumbnailator.makers.ScaledThumbnailMaker.scale`

### Signature
```java
(double widthFactor,double heightFactor):ScaledThumbnailMaker
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ScaledThumbnailMaker.java:165_

### Goal
Sets the horizontal and vertical scaling factors for the `ScaledThumbnailMaker` to determine the dimensions of the resulting thumbnail relative to the source image.

### Parameters
- `widthFactor` (`double`): The scaling factor to apply to the width of the source image (e.g., 0.5 for 50% width).
- `heightFactor` (`double`): The scaling factor to apply to the height of the source image (e.g., 0.5 for 50% height).

### Input
The caller must provide a `ScaledThumbnailMaker` instance. The input image is processed when the `make(BufferedImage)` method is subsequently called. Precondition: The scaling factor must not have been previously set on the maker instance.

### Output
Returns `ScaledThumbnailMaker` — A reference to the current `ScaledThumbnailMaker` instance, allowing for fluent method chaining.

### Valid Call Patterns
```java
ScaledThumbnailMaker maker = new ScaledThumbnailMaker();
maker.scale(0.5, 0.5);
BufferedImage thumbnail = maker.make(sourceImage);
```

### LLM Instruction Prompt
- Use `java:net.coobird.thumbnailator.makers.ScaledThumbnailMaker.scale(double, double)` to define non-uniform scaling.
- Ensure the method is called only once per `ScaledThumbnailMaker` instance.
- Chain this method with `make(BufferedImage)` to produce the final thumbnail.

### Prompt Snippet
```text
ScaledThumbnailMaker maker = new ScaledThumbnailMaker();
maker.scale(widthFactor, heightFactor);
BufferedImage result = maker.make(sourceImage);
```

### Common Failure Modes
- `IllegalStateException`: Thrown if `scale()` is called more than once on the same `ScaledThumbnailMaker` instance.
- `IllegalStateException`: Thrown if the maker is used in a context where dimensions have already been fixed by other conflicting methods.
- Logic errors: Providing factors <= 0 may result in invalid image dimensions.

### Fix Code Hint
```java
// Ensure a fresh instance is used if scaling needs to be redefined
ScaledThumbnailMaker maker = new ScaledThumbnailMaker();
maker.scale(0.5, 0.5); 
// Do not call .scale() again on the same instance
```

## API Test: `java:net.coobird.thumbnailator.makers.ThumbnailMaker`

### Signature
```java
():null
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ThumbnailMaker.java:163_

### Goal
Initializes a new instance of `ThumbnailMaker`, which serves as a base class for creating thumbnail generation logic within the Thumbnailator library.

### Parameters
_None._

### Input
No parameters are required for instantiation. However, as a base class, it is intended to be extended by concrete implementations such as `FixedSizeThumbnailMaker` or `ScaledThumbnailMaker` to perform actual image processing tasks.

### Output
Returns `null` — The constructor returns an instance of the `ThumbnailMaker` class (or its subclass).

### Valid Call Patterns
```java
// Example of instantiating a concrete subclass (as ThumbnailMaker is a base class)
ThumbnailMaker maker = new FixedSizeThumbnailMaker();
```
*(Note: The call pattern is inferred from the constructor signature as no direct usage examples were provided in the test suite or README.)*

### LLM Instruction Prompt
- When generating code involving `ThumbnailMaker`, do not attempt to use it directly as a standalone utility.
- Always prefer the fluent `Thumbnails` API for standard tasks.
- If manual maker instantiation is required, use concrete subclasses like `FixedSizeThumbnailMaker` or `ScaledThumbnailMaker`.
- Ensure that any configuration (such as size or scaling factors) is applied to the concrete instance before calling the `make(BufferedImage)` method.

### Prompt Snippet
```text
// Use a concrete implementation of ThumbnailMaker
ThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.size(width, height);
BufferedImage result = maker.make(sourceImage);
```

### Common Failure Modes
- **Instantiation of Abstract Base:** Attempting to use `ThumbnailMaker` directly if it is defined as abstract or intended only for inheritance.
- **Missing Configuration:** Calling `make()` on a concrete subclass before setting required parameters (e.g., `size()` for `FixedSizeThumbnailMaker`), which will result in an `IllegalStateException`.
- **Type Mismatch:** Reusing a `ThumbnailParameter` or maker instance across multiple threads, as these are not thread-safe.

### Fix Code Hint
```java
// Ensure the maker is fully configured before processing
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.size(200, 200);
maker.keepAspectRatio(true);
BufferedImage thumbnail = maker.make(sourceImage);
```

## API Test: `java:net.coobird.thumbnailator.makers.ThumbnailMaker.defaultImageType`

### Signature
```java
():ThumbnailMaker
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ThumbnailMaker.java:240_

### Goal
Configures the `ThumbnailMaker` to produce a `BufferedImage` using the default image type.

### Parameters
_None._

### Input
No specific input parameters are required. The caller must have an existing instance of a `ThumbnailMaker` (or a subclass like `FixedSizeThumbnailMaker` or `ScaledThumbnailMaker`) to invoke this method.

### Output
Returns `ThumbnailMaker` — A reference to the current `ThumbnailMaker` instance, allowing for method chaining.

### Valid Call Patterns
```java
// Example inferred from signature and class hierarchy
ThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.size(200, 200)
     .defaultImageType()
     .make(sourceImage);
```

### LLM Instruction Prompt
- When generating code, use `defaultImageType()` to reset or explicitly set the output `BufferedImage` type to the library's default.
- Ensure this method is called on an instance of `ThumbnailMaker` or its subclasses.
- This method is part of a fluent API; chain it with other configuration methods like `size()` or `scale()` before calling `make()`.

### Prompt Snippet
```text
// Example usage of defaultImageType in a pipeline
ThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.size(100, 100).defaultImageType().make(bufferedImage);
```

### Common Failure Modes
- **NullPointerException:** Calling this method on an uninitialized `ThumbnailMaker` reference.
- **IllegalStateException:** If the `ThumbnailMaker` is in an invalid state for the specific implementation (e.g., `FixedSizeThumbnailMaker` requires `size()` to be set before `make()` is called).
- **Type Mismatch:** Assuming the output type is a specific format (like `TYPE_INT_ARGB`) when `defaultImageType()` may resolve to a different system-dependent default.

### Fix Code Hint
```java
// Ensure the maker is instantiated and configured before calling make()
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.size(200, 200)
     .defaultImageType(); // Explicitly set default type
BufferedImage result = maker.make(sourceImage);
```

## API Test: `java:net.coobird.thumbnailator.makers.ThumbnailMaker.defaultResizer`

### Signature
```java
():ThumbnailMaker
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ThumbnailMaker.java:263_

### Goal
Configures the `ThumbnailMaker` to use the default `Resizer` implementation for subsequent image processing tasks.

### Parameters
_None._

### Input
The caller must have an existing instance of a `ThumbnailMaker` (e.g., `FixedSizeThumbnailMaker` or `ScaledThumbnailMaker`). No specific preconditions are required for the resizer itself, as it resets the internal state to the library's default scaling algorithm.

### Output
Returns `ThumbnailMaker` — A reference to the current `ThumbnailMaker` instance, allowing for method chaining in the fluent API.

### Valid Call Patterns
```java
// Inferred from signature:
ThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.defaultResizer();
```

### LLM Instruction Prompt
- Use `defaultResizer()` when you need to revert a custom `Resizer` configuration back to the library's standard scaling behavior.
- This method is part of the fluent builder pattern; ensure it is called on an initialized `ThumbnailMaker` object.
- Do not assume this method performs the actual resizing; it only sets the configuration for the `make()` operation.

### Prompt Snippet
```text
// Example of resetting to default resizer
ThumbnailMaker maker = new FixedSizeThumbnailMaker().size(100, 100);
maker.defaultResizer().make(bufferedImage);
```

### Common Failure Modes
- **NullPointerException:** Calling this method on an uninitialized or null `ThumbnailMaker` reference.
- **State Misunderstanding:** Expecting the image to be resized immediately upon calling `defaultResizer()`; the actual processing only occurs when `make(BufferedImage)` is invoked.
- **Redundancy:** Calling this method is unnecessary if the `ThumbnailMaker` has not yet been configured with a custom `Resizer`, as the default is applied upon instantiation.

### Fix Code Hint
```java
// Ensure the maker is instantiated before calling configuration methods
ThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.defaultResizer(); 
BufferedImage result = maker.make(sourceImage);
```

## API Test: `java:net.coobird.thumbnailator.makers.ThumbnailMaker.defaultResizerFactory`

### Signature
```java
():ThumbnailMaker
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ThumbnailMaker.java:290_

### Goal
Configures the `ThumbnailMaker` instance to use the `DefaultResizerFactory`, which automatically selects the optimal resizing algorithm based on the source and destination image dimensions.

### Parameters
_None._

### Input
No input parameters are required. The caller must have an existing `ThumbnailMaker` instance (such as `FixedSizeThumbnailMaker` or `ScaledThumbnailMaker`) upon which this method is invoked.

### Output
Returns `ThumbnailMaker` — A reference to the `ThumbnailMaker` instance, allowing for method chaining in the fluent API.

### Valid Call Patterns
```java
// Example inferred from signature and API documentation
ThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.defaultResizerFactory();
```

### LLM Instruction Prompt
- When generating code, use `defaultResizerFactory()` to reset or explicitly set the resizing strategy to the library's default behavior.
- Ensure this method is called on a `ThumbnailMaker` instance before the `make(BufferedImage)` method is invoked to ensure the desired resizer is applied.
- This method returns the `ThumbnailMaker` instance itself, supporting fluent chaining.

### Prompt Snippet
```text
// Set the resizer factory to default and then generate the thumbnail
maker.defaultResizerFactory().make(sourceImage);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `ThumbnailMaker` instance is null.
- **IllegalStateException:** May occur if the `ThumbnailMaker` is in an invalid state (e.g., required dimensions not set) when `make()` is subsequently called.
- **Misconfiguration:** Calling this method overrides any previously set custom `ResizerFactory`, which may lead to unexpected resizing quality if a specific algorithm was intended.

### Fix Code Hint
```java
// Ensure the maker is initialized before calling defaultResizerFactory
FixedSizeThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.size(200, 200);
maker.defaultResizerFactory();
BufferedImage result = maker.make(sourceImage);
```

## API Test: `java:net.coobird.thumbnailator.makers.ThumbnailMaker.imageType`

### Signature
```java
(int imageType):ThumbnailMaker
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ThumbnailMaker.java:229_

### Goal
Sets the specific `BufferedImage` type (e.g., `BufferedImage.TYPE_INT_ARGB`) to be used for the generated thumbnail.

### Parameters
- `imageType` (`int`): An integer constant representing the `BufferedImage` type, as defined in the `java.awt.image.BufferedImage` class.

### Input
The caller must provide a valid integer constant corresponding to a supported `BufferedImage` type. This method is typically called on a `ThumbnailMaker` instance before the `make()` method is invoked to generate the image.

### Output
Returns `ThumbnailMaker` — A reference to the current `ThumbnailMaker` instance, allowing for method chaining.

### Valid Call Patterns
```java
// Inferred from signature:
ThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.imageType(BufferedImage.TYPE_INT_RGB);
```

### LLM Instruction Prompt
- When using `imageType`, ensure the integer provided matches a valid constant from `java.awt.image.BufferedImage`.
- This method is part of the `ThumbnailMaker` configuration chain; it must be called before the `make(BufferedImage)` method to ensure the output image is created with the desired color model and bit depth.

### Prompt Snippet
```text
// Example of setting the image type for a thumbnail maker
maker.imageType(BufferedImage.TYPE_INT_ARGB);
```

### Common Failure Modes
- **Invalid Type Constants:** Passing an integer that does not correspond to a valid `BufferedImage` type constant may result in an `IllegalArgumentException` or unexpected behavior during the `make()` process.
- **Post-Generation Calls:** Calling `imageType` after the `make()` method has already been executed will have no effect on the already generated thumbnail.
- **Memory Constraints:** Selecting an image type with a high bit depth (e.g., `TYPE_INT_ARGB`) for large batches may increase memory consumption; consider the `CONSERVE_MEMORY_WORKAROUND` if memory limits are reached.

### Fix Code Hint
```java
// Ensure the type is set before generating the thumbnail
BufferedImage thumbnail = new FixedSizeThumbnailMaker()
    .size(200, 200)
    .imageType(BufferedImage.TYPE_INT_RGB)
    .make(sourceImage);
```

## API Test: `java:net.coobird.thumbnailator.makers.ThumbnailMaker.resizer`

### Signature
```java
(Resizer resizer):ThumbnailMaker
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ThumbnailMaker.java:251_

### Goal
Sets the specific `Resizer` algorithm to be used for the image resizing operation within the `ThumbnailMaker` pipeline.

### Parameters
- `resizer` (`Resizer`): The resizing implementation (e.g., `Resizers.BILINEAR`, `Resizers.BICUBIC`, `Resizers.PROGRESSIVE`) to use when scaling the image.

### Input
The caller must provide an implementation of the `Resizer` interface. The `ThumbnailMaker` must be in a state where it is ready to accept configuration before the `make()` method is invoked.

### Output
Returns `ThumbnailMaker` — A reference to the current `ThumbnailMaker` instance, allowing for method chaining.

### Valid Call Patterns
```java
// Example inferred from API signature and Resizer usage
ThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.resizer(Resizers.PROGRESSIVE)
     .size(200, 200)
     .make(sourceImage);
```

### LLM Instruction Prompt
- When calling `resizer`, ensure the `Resizer` instance is chosen based on the desired quality/performance trade-off (e.g., `PROGRESSIVE` for high-quality downscaling, `BICUBIC` for upscaling).
- Always chain this method before calling `make()` to ensure the configuration is applied to the thumbnail generation task.

### Prompt Snippet
```text
// Configure the ThumbnailMaker with a specific resizer
maker.resizer(Resizers.BILINEAR);
```

### Common Failure Modes
- **Null Argument:** Passing `null` as the `resizer` argument will likely result in a `NullPointerException` when the `make()` method is subsequently called.
- **State Mismatch:** Calling `resizer` after the `make()` method has already been executed on the same instance may not affect the already-generated thumbnail.
- **Incompatible Resizer:** Using a custom `Resizer` that does not correctly handle the `BufferedImage` type of the source image may lead to runtime errors during the `make()` process.

### Fix Code Hint
```java
// Ensure a valid Resizer is provided
if (myResizer != null) {
    maker.resizer(myResizer);
} else {
    // Fallback to default or handle error
    maker.resizer(Resizers.DEFAULT);
}
```

## API Test: `java:net.coobird.thumbnailator.makers.ThumbnailMaker.resizerFactory`

### Signature
```java
(ResizerFactory resizerFactory):ThumbnailMaker
```
_Source: source/src/main/java/net/coobird/thumbnailator/makers/ThumbnailMaker.java:277_

### Goal
Sets the `ResizerFactory` used by the `ThumbnailMaker` to determine the appropriate resizing algorithm for the thumbnail generation process.

### Parameters
- `resizerFactory` (`ResizerFactory`): An implementation of the `ResizerFactory` interface that provides a `Resizer` instance based on the source and destination image dimensions.

### Input
- **Data:** A valid `ResizerFactory` instance (e.g., `DefaultResizerFactory`).
- **Preconditions:** The `ThumbnailMaker` instance must be in a state where it is ready to accept configuration before the `make()` method is invoked.

### Output
Returns `ThumbnailMaker` — A reference to the current `ThumbnailMaker` instance, allowing for method chaining.

### Valid Call Patterns
```java
// Inferred from API signature and usage patterns
ThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.resizerFactory(new DefaultResizerFactory());
```

### LLM Instruction Prompt
- Use this method to override the default resizing strategy when specific scaling behavior (e.g., `Bilinear`, `Bicubic`, or `ProgressiveBilinear`) is required.
- Ensure the `ResizerFactory` is instantiated before passing it to the `ThumbnailMaker`.
- This method is part of the configuration phase; call it before invoking `make(BufferedImage)`.

### Prompt Snippet
```text
// Example of setting a custom resizer factory
ThumbnailMaker maker = new FixedSizeThumbnailMaker();
maker.resizerFactory(new DefaultResizerFactory());
```

### Common Failure Modes
- **NullPointerException:** Passing a `null` value to `resizerFactory` will result in a `NullPointerException` when the `make()` method subsequently attempts to retrieve a `Resizer`.
- **Configuration Order:** Attempting to call `make()` before the `ThumbnailMaker` has been fully configured (e.g., missing size or scale parameters) will result in an `IllegalStateException`.

### Fix Code Hint
```java
// Ensure the factory is not null and the maker is configured
ResizerFactory factory = new DefaultResizerFactory();
maker.resizerFactory(factory);
maker.size(200, 200);
BufferedImage result = maker.make(sourceImage);
```

## API Test: `java:net.coobird.thumbnailator.name.ConsecutivelyNumberedFilenames`

### Signature
```java
(File dir,String format,int start):null
```
_Source: source/src/main/java/net/coobird/thumbnailator.name.ConsecutivelyNumberedFilenames.java:295_

### Goal
Instantiates a naming strategy that generates sequential filenames within a specified directory using a custom format string and a starting integer.

### Parameters
- `dir` (`File`): The target directory where the generated thumbnail files will be located.
- `format` (`String`): A format string containing `%d` to be replaced by the consecutive number (e.g., `"image-%d"`).
- `start` (`int`): The initial integer value to begin the sequence.

### Input
- **Data:** A `File` object representing an existing directory, a `String` format pattern, and an `int` starting value.
- **Preconditions:** The `dir` must exist and must be a directory; otherwise, an `IOException` is thrown. The `format` string must be compatible with the Java `Formatter` class syntax for numeric formatting.

### Output
Returns `null` — This constructor initializes an object that implements naming logic for batch thumbnail operations; the resulting object is used by `Thumbnailator` to determine output file paths.

### Valid Call Patterns
```java
// Example of initializing the naming strategy for use in batch processing
File outputDir = new File("/path/to/output");
ConsecutivelyNumberedFilenames naming = new ConsecutivelyNumberedFilenames(outputDir, "thumb-%d.jpg", 1);
```

### LLM Instruction Prompt
- Use this constructor when performing batch thumbnail generation where output files require a specific sequential naming pattern.
- Ensure the `dir` argument is a valid, existing directory object.
- Include the `%d` placeholder in the `format` string to ensure the sequence number is correctly injected.
- Wrap the instantiation in a `try-catch` block to handle potential `IOException` if the directory is invalid.

### Prompt Snippet
```text
new net.coobird.thumbnailator.name.ConsecutivelyNumberedFilenames(directoryFile, "prefix-%d", 0);
```

### Common Failure Modes
- **IOException:** Occurs if the provided `File` path does not exist or is not a directory.
- **IllegalFormatConversionException:** Occurs if the `format` string is malformed or incompatible with the integer sequence provided by the library.
- **NullPointerException:** Occurs if the `dir` or `format` arguments are passed as `null`.

### Fix Code Hint
```java
// Ensure directory exists before passing to the constructor
File dir = new File("output/path");
if (dir.exists() && dir.isDirectory()) {
    ConsecutivelyNumberedFilenames naming = new ConsecutivelyNumberedFilenames(dir, "img-%d.png", 0);
} else {
    // Handle invalid directory
}
```

## API Test: `java:net.coobird.thumbnailator.name.ConsecutivelyNumberedFilenames.iterator`

### Signature
```java
():Iterator<File>
```
_Source: source/src/main/java/net/coobird/thumbnailator/name/ConsecutivelyNumberedFilenames.java:343_

### Goal
Provides an iterator that generates a sequence of `File` objects with consecutively numbered names based on the configuration of the `ConsecutivelyNumberedFilenames` instance.

### Parameters
_None._

### Input
The caller must have instantiated `ConsecutivelyNumberedFilenames` with the desired base path, prefix, and suffix patterns. No specific file system state is required to initialize the iterator, as it generates the path objects programmatically.

### Output
Returns `Iterator<File>` — An iterator that yields `File` objects representing the sequence of filenames defined by the naming strategy.

### Valid Call Patterns
```java
ConsecutivelyNumberedFilenames naming = new ConsecutivelyNumberedFilenames("path/to/output", "thumb-", ".jpg");
Iterator<File> it = naming.iterator();
while (it.hasNext()) {
    File f = it.next();
    // Process file
}
```
*(Note: This call pattern is inferred from the API signature and the `Iterable<File>` interface implementation.)*

### LLM Instruction Prompt
- Use this method when you need to generate a sequence of unique, numbered filenames for batch thumbnail output operations.
- Ensure the `ConsecutivelyNumberedFilenames` object is fully configured before calling `iterator()`.
- The returned `Iterator<File>` is intended for use in loops where each thumbnail in a batch requires a distinct, sequential filename.

### Prompt Snippet
```text
// Example of using the iterator to generate sequential filenames
ConsecutivelyNumberedFilenames naming = new ConsecutivelyNumberedFilenames(directoryPath, prefix, extension);
Iterator<File> fileIterator = naming.iterator();
```

### Common Failure Modes
- **NoSuchElementException:** Occurs if `next()` is called on the iterator when no more files are available in the sequence.
- **Configuration Errors:** If the `ConsecutivelyNumberedFilenames` instance was initialized with invalid path strings, the resulting `File` objects may point to invalid locations or trigger `IOException` during subsequent I/O operations.
- **State Misuse:** Attempting to reuse the iterator after it has been exhausted; a new iterator must be requested from the `ConsecutivelyNumberedFilenames` instance.

### Fix Code Hint
```java
// Always check hasNext() before calling next()
if (naming.iterator().hasNext()) {
    File nextFile = naming.iterator().next();
}
```

## API Test: `java:net.coobird.thumbnailator.resizers.AbstractResizer.getRenderingHints`

### Signature
```java
():Map<RenderingHints.Key, Object>
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/AbstractResizer.java:146_

### Goal
Retrieves the `RenderingHints` configuration currently applied by the resizer to control the quality and performance of the image scaling process.

### Parameters
_None._

### Input
No input parameters are required. The caller must have an instance of a class extending `AbstractResizer` (e.g., `BilinearResizer`, `BicubicResizer`, or `ProgressiveBilinearResizer`).

### Output
Returns `Map<RenderingHints.Key, Object>` — A map containing the `RenderingHints` keys and their corresponding values that dictate how the `Graphics2D` object performs the image transformation.

### Valid Call Patterns
```java
// Example inferred from signature (not verified in test suite)
AbstractResizer resizer = new ProgressiveBilinearResizer();
Map<RenderingHints.Key, Object> hints = resizer.getRenderingHints();
```

### LLM Instruction Prompt
- Use this method to inspect the internal rendering configuration of a specific `Resizer` implementation.
- Do not attempt to pass arguments to this method as it takes none.
- Ensure the `AbstractResizer` instance is fully initialized before calling, as the hints are specific to the chosen resizing algorithm.

### Prompt Snippet
```text
// Accessing rendering hints from a Thumbnailator resizer
Map<RenderingHints.Key, Object> hints = myResizer.getRenderingHints();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `AbstractResizer` instance is null.
- **Misinterpretation:** Users may assume the returned map is mutable; while it represents the current state, modifying the map may not affect the internal state of the `Resizer` instance depending on the implementation.

### Fix Code Hint
```java
// Ensure the resizer is instantiated before accessing hints
Resizer resizer = Resizers.PROGRESSIVE_BILINEAR;
if (resizer instanceof AbstractResizer) {
    Map<RenderingHints.Key, Object> hints = ((AbstractResizer) resizer).getRenderingHints();
    // Process hints as needed
}
```

## API Test: `java:net.coobird.thumbnailator.resizers.AbstractResizer.resize`

### Signature
```java
(BufferedImage srcImage,BufferedImage destImage):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/AbstractResizer.java:98_

### Goal
Performs a deterministic image resizing operation by scaling the source `BufferedImage` and rendering the result into the provided destination `BufferedImage`.

### Parameters
- `srcImage` (`BufferedImage`): The source image to be resized.
- `destImage` (`BufferedImage`): The destination image buffer where the resized output will be drawn.

### Input
The caller must provide two initialized `BufferedImage` objects. The `srcImage` should contain the original image data. The `destImage` must be pre-allocated with the desired target dimensions. Both images must be non-null.

### Output
Returns `void` — The operation modifies the `destImage` in-place by drawing the scaled `srcImage` onto it.

### Valid Call Patterns
```java
BufferedImage sourceImage = new BufferedImageBuilder(400, 400).build();
BufferedImage destImage = new BufferedImageBuilder(200, 200).build();
Resizers.BILINEAR.resize(sourceImage, destImage);
```

### LLM Instruction Prompt
- When calling `resize`, ensure both `srcImage` and `destImage` are non-null `BufferedImage` instances.
- Use a concrete implementation of `AbstractResizer` (such as `Resizers.BILINEAR`, `Resizers.BICUBIC`, or `Resizers.PROGRESSIVE_BILINEAR`) to invoke this method.
- Ensure the `destImage` has been initialized with the target dimensions before passing it to the method.

### Prompt Snippet
```text
// Example of using a specific resizer implementation
Resizers.BILINEAR.resize(sourceImage, destImage);
```

### Common Failure Modes
- `NullPointerException`: Thrown if either `srcImage` or `destImage` is `null`.
- `IllegalArgumentException`: May occur if the `destImage` is not properly initialized or has incompatible color models for the drawing operation.
- Logic errors: If the `destImage` dimensions do not match the intended thumbnail size, the resulting image will be distorted or incorrectly cropped.

### Fix Code Hint
```java
// Ensure images are initialized before resizing
if (srcImage != null && destImage != null) {
    resizer.resize(srcImage, destImage);
} else {
    throw new IllegalArgumentException("Source and destination images must not be null.");
}
```

## API Test: `java:net.coobird.thumbnailator.resizers.BicubicResizer`

### Signature
```java
(Map<RenderingHints.Key, Object> hints):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/BicubicResizer.java:51_

### Goal
Instantiates a `BicubicResizer` that performs high-quality image scaling using the bicubic interpolation algorithm, typically utilized by the `DefaultResizerFactory` for upscaling operations.

### Parameters
- `hints` (`Map<RenderingHints.Key, Object>`): A map of `RenderingHints` to be applied to the `Graphics2D` context during the resizing process, allowing for fine-tuned control over interpolation quality and rendering speed.

### Input
- **Data:** Requires a `Map` of `RenderingHints.Key` to `Object`.
- **Preconditions:** The caller must ensure the provided map contains valid `RenderingHints` keys compatible with the Java 2D API. This class is intended for use within the Thumbnailator resizing pipeline; it is generally managed by `ResizerFactory` implementations rather than direct instantiation in standard fluent workflows.

### Output
Returns `null` — This is a constructor; it returns an instance of `BicubicResizer` which implements the `Resizer` interface.

### Valid Call Patterns
```java
Map<RenderingHints.Key, Object> hints = new HashMap<RenderingHints.Key, Object>();
hints.put(RenderingHints.KEY_INTERPOLATION, RenderingHints.VALUE_INTERPOLATION_BICUBIC);
BicubicResizer resizer = new BicubicResizer(hints);
```

### LLM Instruction Prompt
- When generating code that requires specific scaling algorithms, prefer using the `Resizers` enum or `DefaultResizerFactory` unless custom `RenderingHints` are strictly required. If direct instantiation is necessary, ensure the `Map` is correctly initialized with valid `RenderingHints` keys.

### Prompt Snippet
```text
// Instantiate BicubicResizer with custom rendering hints
Map<RenderingHints.Key, Object> hints = new HashMap<>();
hints.put(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);
BicubicResizer resizer = new BicubicResizer(hints);
```

### Common Failure Modes
- **NullPointerException:** Passing a `null` map to the constructor may result in an exception depending on the internal implementation of the `Resizer` base class.
- **Invalid Hints:** Providing unsupported or incorrect `RenderingHints` keys may lead to unexpected rendering behavior or `IllegalArgumentException` during the `resize` operation.
- **Redundant Usage:** Manually instantiating this class when `Resizers.BICUBIC` is available can lead to unnecessary object creation and bypasses the library's optimized `ResizerFactory` logic.

### Fix Code Hint
```java
// Use the pre-configured Resizers enum for standard operations
Resizer resizer = Resizers.BICUBIC;

// If custom hints are required, ensure the map is not null
Map<RenderingHints.Key, Object> hints = new HashMap<>();
hints.put(RenderingHints.KEY_INTERPOLATION, RenderingHints.VALUE_INTERPOLATION_BICUBIC);
Resizer customResizer = new BicubicResizer(hints);
```

## API Test: `java:net.coobird.thumbnailator.resizers.BicubicResizer.resize`

### Signature
```java
(BufferedImage srcImage,BufferedImage destImage):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/BicubicResizer.java:70_

### Goal
Performs a high-quality image resize operation using bicubic interpolation to scale the source `BufferedImage` into the provided destination `BufferedImage`.

### Parameters
- `srcImage` (`BufferedImage`): The source image to be resized.
- `destImage` (`BufferedImage`): The destination image where the resized content will be drawn.

### Input
- **Data:** Requires two initialized `BufferedImage` objects.
- **Preconditions:** Both `srcImage` and `destImage` must be non-null. The `destImage` should be pre-allocated with the desired target dimensions.
- **Formats:** Supports any `BufferedImage` type compatible with standard Java 2D graphics operations.

### Output
Returns `void` — The operation modifies the `destImage` in-place by rendering the scaled `srcImage` into it.

### Valid Call Patterns
```java
// Inferred from project usage patterns
BicubicResizer resizer = new BicubicResizer();
resizer.resize(sourceImage, destImage);
```

### LLM Instruction Prompt
- When calling `resize`, ensure both `srcImage` and `destImage` are instantiated and non-null.
- Use this method when explicit control over the bicubic interpolation algorithm is required, typically for upscaling tasks.
- Do not use this method if the images are null, as it will throw a `NullPointerException`.

### Prompt Snippet
```text
net.coobird.thumbnailator.resizers.BicubicResizer resizer = new net.coobird.thumbnailator.resizers.BicubicResizer();
resizer.resize(srcImage, destImage);
```

### Common Failure Modes
- **NullPointerException:** Occurs if either `srcImage` or `destImage` is passed as `null`.
- **IllegalStateException:** While not specific to this method, ensure the `destImage` is properly initialized; attempting to resize into a zero-dimension image may result in empty output.
- **Performance:** Bicubic interpolation is computationally more expensive than bilinear; for large-scale batch processing, consider if `ProgressiveBilinearResizer` is more appropriate for downscaling.

### Fix Code Hint
```java
// Ensure images are initialized before calling
if (srcImage != null && destImage != null) {
    new BicubicResizer().resize(srcImage, destImage);
} else {
    throw new IllegalArgumentException("Source and destination images must not be null.");
}
```

## API Test: `java:net.coobird.thumbnailator.resizers.BilinearResizer`

### Signature
```java
(Map<RenderingHints.Key, Object> hints):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/BilinearResizer.java:52_

### Goal
Instantiates a `BilinearResizer` that performs image scaling using the bilinear interpolation algorithm, allowing for custom `RenderingHints` to be applied during the transformation process.

### Parameters
- `hints` (`Map<RenderingHints.Key, Object>`): A map of `RenderingHints` keys and values that define the behavior of the `Graphics2D` object used during the resizing operation (e.g., `KEY_INTERPOLATION`).

### Input
- **Data:** Requires a `Map` containing valid `RenderingHints` keys.
- **Preconditions:** The caller must ensure the `Map` is correctly populated with `RenderingHints.Key` objects as keys and corresponding `Object` values compatible with the Java 2D API.

### Output
Returns `null` — The constructor initializes the `BilinearResizer` instance, which can then be used to perform resizing operations via the `resize(BufferedImage, BufferedImage)` method.

### Valid Call Patterns
```java
Map<RenderingHints.Key, Object> hints = new HashMap<>();
hints.put(RenderingHints.KEY_INTERPOLATION, RenderingHints.VALUE_INTERPOLATION_BILINEAR);
BilinearResizer resizer = new BilinearResizer(hints);
```

### LLM Instruction Prompt
- When using `BilinearResizer`, ensure the `Map` provided to the constructor contains valid `RenderingHints` keys. The resulting object should be used to invoke the `resize` method, passing source and destination `BufferedImage` objects.

### Prompt Snippet
```text
BilinearResizer resizer = new BilinearResizer(hintsMap);
resizer.resize(sourceImage, destImage);
```

### Common Failure Modes
- **NullPointerException:** Passing a `null` map to the constructor may result in an exception depending on the internal implementation of the resizer.
- **Invalid Hints:** Providing unsupported or incorrect `RenderingHints` values may lead to unexpected rendering behavior or `IllegalArgumentException` during the `resize` operation.
- **Type Mismatch:** Using keys that are not instances of `RenderingHints.Key` or values incompatible with the specific hint key will cause runtime errors.

### Fix Code Hint
```java
// Ensure hints are properly initialized
Map<RenderingHints.Key, Object> hints = new HashMap<>();
hints.put(RenderingHints.KEY_INTERPOLATION, RenderingHints.VALUE_INTERPOLATION_BILINEAR);
BilinearResizer resizer = new BilinearResizer(hints);
```

## API Test: `java:net.coobird.thumbnailator.resizers.BilinearResizer.resize`

### Signature
```java
(BufferedImage srcImage,BufferedImage destImage):void
```
_Source: source/src/main/java/net/coobird/thumbnailator.resizers.BilinearResizer.java:71_

### Goal
Performs a deterministic image resize operation using bilinear interpolation to scale the source `BufferedImage` into the provided destination `BufferedImage`.

### Parameters
- `srcImage` (`BufferedImage`): The source image to be resized.
- `destImage` (`BufferedImage`): The destination image buffer where the resized output will be drawn.

### Input
- **Data:** Requires two initialized `BufferedImage` objects.
- **Preconditions:** Both `srcImage` and `destImage` must be non-null. The `destImage` should be pre-allocated with the desired target dimensions.
- **Formats:** Supports any `BufferedImage` type compatible with standard Java 2D graphics operations.

### Output
Returns `void` — The operation modifies the `destImage` in-place by rendering the scaled `srcImage` into it.

### Valid Call Patterns
```java
BufferedImage sourceImage = new BufferedImageBuilder(400, 400).build();
BufferedImage destImage = new BufferedImageBuilder(200, 200).build();
new net.coobird.thumbnailator.resizers.BilinearResizer().resize(sourceImage, destImage);
```

### LLM Instruction Prompt
- When calling `resize`, ensure both `srcImage` and `destImage` are instantiated and non-null.
- Use this method when explicit control over the bilinear interpolation algorithm is required, rather than relying on the `DefaultResizerFactory`.
- Always ensure the `destImage` has been initialized with the correct target dimensions before passing it to the method.

### Prompt Snippet
```text
net.coobird.thumbnailator.resizers.BilinearResizer resizer = new net.coobird.thumbnailator.resizers.BilinearResizer();
resizer.resize(srcImage, destImage);
```

### Common Failure Modes
- **NullPointerException:** Thrown if either `srcImage` or `destImage` is `null`.
- **Dimension Mismatch:** While the method executes, providing a `destImage` with incorrect dimensions may lead to unexpected scaling artifacts or incomplete rendering if the destination buffer does not match the intended thumbnail size.

### Fix Code Hint
```java
// Ensure images are not null and destination is properly sized
if (srcImage != null && destImage != null) {
    new net.coobird.thumbnailator.resizers.BilinearResizer().resize(srcImage, destImage);
}
```

## API Test: `java:net.coobird.thumbnailator.resizers.DefaultResizerFactory.getInstance`

### Signature
```java
():ResizerFactory
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/DefaultResizerFactory.java:118_

### Goal
Retrieves the singleton instance of the `DefaultResizerFactory`, which provides the standard logic for selecting an appropriate `Resizer` algorithm based on source and destination image dimensions.

### Parameters
_None._

### Input
No input parameters are required. The factory operates based on the internal logic that automatically selects `ProgressiveBilinearResizer` for downscaling, `BicubicResizer` for upscaling, and `NullResizer` for identical dimensions.

### Output
Returns `ResizerFactory` — The singleton instance of the default factory used by Thumbnailator to determine the optimal resizing strategy for a given transformation task.

### Valid Call Patterns
```java
// Inferred from API signature
ResizerFactory factory = net.coobird.thumbnailator.resizers.DefaultResizerFactory.getInstance();
```

### LLM Instruction Prompt
- Use `net.coobird.thumbnailator.resizers.DefaultResizerFactory.getInstance()` to obtain the factory instance when you need to manually resolve a `Resizer` for a specific `BufferedImage` transformation.
- Do not attempt to instantiate `DefaultResizerFactory` directly via constructor; always use the `getInstance()` method.

### Prompt Snippet
```text
// Obtain the default resizer factory
ResizerFactory resizerFactory = net.coobird.thumbnailator.resizers.DefaultResizerFactory.getInstance();
```

### Common Failure Modes
- **Misunderstanding Scope:** Users may attempt to configure the factory as if it were a mutable state object; however, the `DefaultResizerFactory` is a stateless singleton.
- **Redundant Instantiation:** Attempting to create new instances of the factory instead of using the provided singleton, which is unnecessary and deviates from the library's design.

### Fix Code Hint
```java
// Correct usage:
ResizerFactory factory = net.coobird.thumbnailator.resizers.DefaultResizerFactory.getInstance();
Resizer resizer = factory.getResizer(sourceDimension, destDimension);
resizer.resize(sourceImage, destImage);
```

## API Test: `java:net.coobird.thumbnailator.resizers.DefaultResizerFactory.getResizer`

### Signature
```java
(Dimension originalSize,Dimension thumbnailSize):Resizer
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/DefaultResizerFactory.java:126_

### Goal
Selects and returns an appropriate `Resizer` implementation based on the dimensions of the source image and the target thumbnail to ensure optimal scaling quality.

### Parameters
- `originalSize` (`Dimension`): The dimensions of the source image to be resized.
- `thumbnailSize` (`Dimension`): The target dimensions for the resulting thumbnail.

### Input
The caller must provide two `java.awt.Dimension` objects representing the source and destination sizes. The factory uses these to determine the scaling operation: `ProgressiveBilinearResizer` is selected for downscaling, `BicubicResizer` for upscaling, and `NullResizer` if the dimensions are identical.

### Output
Returns `Resizer` — An implementation of the `Resizer` interface configured to perform the specific scaling operation required for the provided dimensions.

### Valid Call Patterns
```java
import java.awt.Dimension;
import net.coobird.thumbnailator.resizers.DefaultResizerFactory;
import net.coobird.thumbnailator.resizers.Resizer;

Dimension original = new Dimension(1024, 768);
Dimension target = new Dimension(200, 150);
Resizer resizer = DefaultResizerFactory.getInstance().getResizer(original, target);
```

### LLM Instruction Prompt
- When calling `getResizer`, ensure both `originalSize` and `thumbnailSize` are initialized `java.awt.Dimension` objects.
- Use the `DefaultResizerFactory.getInstance()` method to obtain the factory instance before calling `getResizer`.
- Do not attempt to instantiate `DefaultResizerFactory` directly if a singleton accessor is available.

### Prompt Snippet
```text
// Obtain the appropriate resizer for a 1024x768 to 200x150 transformation
Resizer resizer = DefaultResizerFactory.getInstance().getResizer(new Dimension(1024, 768), new Dimension(200, 150));
```

### Common Failure Modes
- Passing `null` for either `Dimension` argument will result in a `NullPointerException`.
- Providing negative values for width or height in the `Dimension` objects may lead to unexpected behavior or `IllegalArgumentException` during the subsequent `resize` operation.

### Fix Code Hint
```java
// Ensure dimensions are non-null and positive
Dimension original = new Dimension(Math.max(1, w), Math.max(1, h));
Dimension target = new Dimension(Math.max(1, tw), Math.max(1, th));
Resizer resizer = DefaultResizerFactory.getInstance().getResizer(original, target);
```

## API Test: `java:net.coobird.thumbnailator.resizers.FixedResizerFactory`

### Signature
```java
(Resizer resizer):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/FixedResizerFactory.java:49_

### Goal
Creates a `FixedResizerFactory` that consistently returns a specific, user-provided `Resizer` implementation for all image scaling operations.

### Parameters
- `resizer` (`Resizer`): The specific `Resizer` instance (e.g., `Resizers.BILINEAR`, `Resizers.BICUBIC`) that the factory will return whenever a resizing operation is requested.

### Input
- **Data:** A valid implementation of the `Resizer` interface.
- **Preconditions:** The `Resizer` instance must be initialized and ready for use. This factory is typically used when the default resizing logic (provided by `DefaultResizerFactory`) is not desired and a deterministic, single-algorithm approach is required for the entire pipeline.

### Output
Returns `null` — This is a constructor; it initializes a new `FixedResizerFactory` object which acts as a factory for the provided `Resizer`.

### Valid Call Patterns
```java
// Example: Forcing the use of the Bilinear resizer for all operations
Resizer myResizer = Resizers.BILINEAR;
FixedResizerFactory factory = new FixedResizerFactory(myResizer);
```

### LLM Instruction Prompt
- When generating code using `FixedResizerFactory`, ensure the `Resizer` argument is one of the standard implementations provided by the `Resizers` enum or a custom `Resizer` implementation.
- Note that this class is a constructor; it must be instantiated using the `new` keyword.
- Use this factory when you need to override the default `DefaultResizerFactory` behavior to ensure consistent, deterministic scaling across a thumbnail generation task.

### Prompt Snippet
```text
// Instantiate a FixedResizerFactory with a specific Resizer
FixedResizerFactory factory = new FixedResizerFactory(Resizers.BILINEAR);
```

### Common Failure Modes
- **Null Argument:** Passing `null` as the `resizer` argument will likely result in a `NullPointerException` when the factory is later invoked to perform a resize operation.
- **Misunderstanding Factory Role:** Attempting to use the factory as a `Resizer` directly; remember that the factory is a provider, and you must call its methods (or pass it to a `ThumbnailParameter`) to retrieve the `Resizer` instance.
- **Compatibility:** Ensure the chosen `Resizer` is compatible with the target image type, as some resizers may behave differently depending on the source and destination `BufferedImage` types.

### Fix Code Hint
```java
// Ensure a valid Resizer is provided
Resizer resizer = Resizers.PROGRESSIVE_BILINEAR;
FixedResizerFactory factory = new FixedResizerFactory(resizer);
```

## API Test: `java:net.coobird.thumbnailator.resizers.FixedResizerFactory.getResizer`

### Signature
```java
(Dimension originalSize,Dimension thumbnailSize):Resizer
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/FixedResizerFactory.java:57_

### Goal
Returns a suitable `Resizer` implementation based on the dimensions of the original image and the target thumbnail to ensure deterministic, high-quality scaling.

### Parameters
- `originalSize` (`Dimension`): The dimensions of the source image to be processed.
- `thumbnailSize` (`Dimension`): The target dimensions for the resulting thumbnail.

### Input
The caller must provide two `java.awt.Dimension` objects representing the source and destination sizes. This method is typically used when manual control over the resizing algorithm is required outside of the fluent `Thumbnails` builder API.

### Output
Returns `Resizer` — An implementation of the `Resizer` interface (such as `ProgressiveBilinearResizer`, `BicubicResizer`, or `NullResizer`) selected by the factory to perform the transformation for the given dimensions.

### Valid Call Patterns
```java
import java.awt.Dimension;
import net.coobird.thumbnailator.resizers.FixedResizerFactory;
import net.coobird.thumbnailator.resizers.Resizer;

Dimension original = new Dimension(800, 600);
Dimension target = new Dimension(200, 150);
Resizer resizer = new FixedResizerFactory().getResizer(original, target);
```

### LLM Instruction Prompt
- Use this method when you need to explicitly select a resizing algorithm based on specific input and output dimensions.
- Always instantiate `FixedResizerFactory` before calling `getResizer`.
- Ensure `originalSize` and `thumbnailSize` are non-null `java.awt.Dimension` objects.

### Prompt Snippet
```text
FixedResizerFactory factory = new FixedResizerFactory();
Resizer resizer = factory.getResizer(new Dimension(width, height), new Dimension(thumbW, thumbH));
```

### Common Failure Modes
- **NullPointerException**: Occurs if either `originalSize` or `thumbnailSize` is passed as `null`.
- **Invalid Dimensions**: Passing dimensions with zero or negative values may lead to unexpected behavior or `IllegalArgumentException` depending on the specific `Resizer` implementation returned.
- **Misuse of Factory**: Attempting to use this method as a static call (it is an instance method on `FixedResizerFactory`) will result in a compilation error.

### Fix Code Hint
```java
// Ensure dimensions are positive and non-null
if (originalSize != null && thumbnailSize != null && originalSize.width > 0 && thumbnailSize.width > 0) {
    Resizer resizer = new FixedResizerFactory().getResizer(originalSize, thumbnailSize);
    resizer.resize(sourceImage, destImage);
}
```

## API Test: `java:net.coobird.thumbnailator.resizers.NullResizer`

### Signature
```java
():null
```
_Source: source/src/main/java/net/coobird/thumbnailator.resizers.NullResizer.java:48_

### Goal
Instantiates a resizer that performs no scaling, instead drawing the source image directly at the origin of the destination image.

### Parameters
_None._

### Input
The caller must provide a source `BufferedImage` and a destination `BufferedImage` of identical dimensions to the `resize` method of the instantiated `NullResizer`. This resizer is automatically selected by `DefaultResizerFactory` when the source and destination dimensions are identical.

### Output
Returns `null` — The constructor itself returns the `NullResizer` instance; the `resize` operation performed by this class returns `void` (it modifies the destination `BufferedImage` in-place).

### Valid Call Patterns
```java
// Inferred from project usage of Resizer implementations
Resizer resizer = new net.coobird.thumbnailator.resizers.NullResizer();
resizer.resize(sourceImage, destImage);
```

### LLM Instruction Prompt
- When generating code using `NullResizer`, ensure it is only used when the source and destination image dimensions are identical.
- Do not attempt to use this class for scaling operations, as it does not perform interpolation or resizing logic.
- Always instantiate via the constructor `new net.coobird.thumbnailator.resizers.NullResizer()`.

### Prompt Snippet
```text
net.coobird.thumbnailator.resizers.NullResizer resizer = new net.coobird.thumbnailator.resizers.NullResizer();
resizer.resize(sourceImage, destImage);
```

### Common Failure Modes
- **Dimension Mismatch:** Attempting to use `NullResizer` when source and destination dimensions differ will result in an incomplete or incorrectly clipped image, as it performs a direct draw at the origin (0,0) without scaling.
- **Manual Instantiation:** While `NullResizer` is public, it is typically managed internally by `DefaultResizerFactory`; manual instantiation is only necessary for custom pipeline overrides.

### Fix Code Hint
```java
// Ensure dimensions match before using NullResizer
if (sourceImage.getWidth() == destImage.getWidth() && sourceImage.getHeight() == destImage.getHeight()) {
    new net.coobird.thumbnailator.resizers.NullResizer().resize(sourceImage, destImage);
} else {
    // Fallback to a scaling resizer like Resizers.BILINEAR
    net.coobird.thumbnailator.resizers.Resizers.BILINEAR.resize(sourceImage, destImage);
}
```

## API Test: `java:net.coobird.thumbnailator.resizers.NullResizer.resize`

### Signature
```java
(BufferedImage srcImage,BufferedImage destImage):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/NullResizer.java:66_

### Goal
Performs a direct copy of the source image to the destination image without applying any scaling or interpolation, typically used when source and destination dimensions are identical.

### Parameters
- `srcImage` (`BufferedImage`): The source image to be copied.
- `destImage` (`BufferedImage`): The destination image where the source image data will be written.

### Input
The caller must provide two initialized `BufferedImage` objects. The `NullResizer` is intended for use when the source and destination dimensions match. Preconditions include ensuring both images are non-null; otherwise, a `NullPointerException` will be thrown.

### Output
Returns `void` — the operation modifies the `destImage` in-place by copying the pixel data from `srcImage`.

### Valid Call Patterns
```java
BufferedImage sourceImage = new BufferedImageBuilder(200, 200).build();
BufferedImage destImage = new BufferedImageBuilder(200, 200).build();
new net.coobird.thumbnailator.resizers.NullResizer().resize(sourceImage, destImage);
```

### LLM Instruction Prompt
- When the target dimensions of a thumbnail task match the source dimensions, use `NullResizer.resize` to perform a direct copy.
- Always ensure both `srcImage` and `destImage` are instantiated and non-null before invoking this method.
- Do not use this resizer if scaling is required, as it performs no interpolation or resizing logic.

### Prompt Snippet
```text
net.coobird.thumbnailator.resizers.NullResizer resizer = new net.coobird.thumbnailator.resizers.NullResizer();
resizer.resize(srcImage, destImage);
```

### Common Failure Modes
- `NullPointerException`: Occurs if either `srcImage` or `destImage` is passed as `null`.
- `IllegalArgumentException` (or unexpected visual results): Occurs if the dimensions of `srcImage` and `destImage` do not match, as `NullResizer` does not perform scaling.
- Logic errors: Using `NullResizer` when downscaling or upscaling is required, resulting in an unscaled copy rather than a processed thumbnail.

### Fix Code Hint
```java
// Ensure dimensions match before using NullResizer
if (srcImage.getWidth() == destImage.getWidth() && srcImage.getHeight() == destImage.getHeight()) {
    new net.coobird.thumbnailator.resizers.NullResizer().resize(srcImage, destImage);
} else {
    // Fallback to a scaling resizer like Resizers.BILINEAR
    net.coobird.thumbnailator.resizers.Resizers.BILINEAR.resize(srcImage, destImage);
}
```

## API Test: `java:net.coobird.thumbnailator.resizers.ProgressiveBilinearResizer`

### Signature
```java
(Map<RenderingHints.Key, Object> hints):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/ProgressiveBilinearResizer.java:70_

### Goal
Instantiates a `ProgressiveBilinearResizer` that performs high-quality image downscaling by iteratively applying bilinear interpolation to reduce image dimensions in steps.

### Parameters
- `hints` (`Map<RenderingHints.Key, Object>`): A map of `RenderingHints` to be applied to the `Graphics2D` object during the resizing process, allowing control over rendering quality, interpolation, and color rendering.

### Input
- **Data:** Requires a `Map` of `RenderingHints.Key` to `Object`.
- **Preconditions:** The resizer is typically used by the `DefaultResizerFactory` for downscaling operations. When manually instantiated, ensure the provided hints are compatible with the `Graphics2D` rendering pipeline.

### Output
Returns `null` — This is a constructor; the return value is the initialized `ProgressiveBilinearResizer` instance.

### Valid Call Patterns
```java
Map<RenderingHints.Key, Object> hints = new HashMap<RenderingHints.Key, Object>();
hints.put(RenderingHints.KEY_INTERPOLATION, RenderingHints.VALUE_INTERPOLATION_BILINEAR);
ProgressiveBilinearResizer resizer = new net.coobird.thumbnailator.resizers.ProgressiveBilinearResizer(hints);
```
*(Note: This call pattern is inferred from the constructor signature and standard Java 2D API usage.)*

### LLM Instruction Prompt
- Use this class when you require a deterministic, progressive downscaling algorithm.
- Always pass a `Map<RenderingHints.Key, Object>` to the constructor.
- Do not use this resizer for upscaling; `DefaultResizerFactory` uses `BicubicResizer` for that purpose.

### Prompt Snippet
```text
// Instantiate the resizer with specific rendering hints
Map<RenderingHints.Key, Object> hints = new HashMap<>();
hints.put(RenderingHints.KEY_RENDERING, RenderingHints.VALUE_RENDER_QUALITY);
var resizer = new net.coobird.thumbnailator.resizers.ProgressiveBilinearResizer(hints);
```

### Common Failure Modes
- **NullPointerException:** Passing a `null` map to the constructor may result in an exception depending on the internal implementation of the resizer.
- **Incorrect Resizer Usage:** Using this resizer for upscaling tasks, which may result in lower quality compared to `BicubicResizer`.
- **Unsupported Hints:** Providing `RenderingHints` that are not supported by the underlying `Graphics2D` implementation.

### Fix Code Hint
```java
// Ensure the map is initialized before passing to the constructor
Map<RenderingHints.Key, Object> hints = new HashMap<>();
// Add valid RenderingHints keys
hints.put(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);
new net.coobird.thumbnailator.resizers.ProgressiveBilinearResizer(hints);
```

## API Test: `java:net.coobird.thumbnailator.resizers.ProgressiveBilinearResizer.resize`

### Signature
```java
(BufferedImage srcImage,BufferedImage destImage):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/ProgressiveBilinearResizer.java:90_

### Goal
Performs high-quality image downscaling using a progressive bilinear interpolation technique to minimize aliasing artifacts.

### Parameters
- `srcImage` (`BufferedImage`): The source image to be resized.
- `destImage` (`BufferedImage`): The destination image buffer where the resized result will be drawn.

### Input
- **Data:** Requires two `BufferedImage` objects.
- **Preconditions:** The `srcImage` and `destImage` must not be `null`.
- **Logic:** If the source image dimensions are not at least twice as large as the destination image dimensions for both width and height, the resizer automatically falls back to a standard one-step bilinear scaling.

### Output
Returns `void` — The operation modifies the `destImage` in-place by drawing the scaled content of `srcImage` onto it.

### Valid Call Patterns
```java
ProgressiveBilinearResizer resizer = new ProgressiveBilinearResizer();
resizer.resize(sourceImage, destImage);
```

### LLM Instruction Prompt
- When using `ProgressiveBilinearResizer.resize`, ensure both `srcImage` and `destImage` are initialized `BufferedImage` instances.
- Do not pass `null` values, as this will trigger a `NullPointerException`.
- This method is intended for manual control; for standard fluent pipelines, prefer `Thumbnails.of(...).size(...).toBufferedImage()`.

### Prompt Snippet
```text
// Example of manual resizing using ProgressiveBilinearResizer
BufferedImage src = ImageIO.read(new File("input.jpg"));
BufferedImage dest = new BufferedImage(200, 200, BufferedImage.TYPE_INT_RGB);
new ProgressiveBilinearResizer().resize(src, dest);
```

### Common Failure Modes
- **NullPointerException:** Occurs if either `srcImage` or `destImage` is `null`.
- **Incompatible Types:** If `destImage` is not properly initialized or has an incompatible color model, the resulting thumbnail may exhibit color distortion or rendering errors.
- **Performance:** While efficient for downscaling, this resizer is not optimized for upscaling; `BicubicResizer` is preferred for that use case.

### Fix Code Hint
```java
// Ensure destination is initialized before resizing
if (srcImage != null && destImage != null) {
    new ProgressiveBilinearResizer().resize(srcImage, destImage);
}
```

## API Test: `java:net.coobird.thumbnailator.resizers.Resizers.resize`

### Signature
```java
(BufferedImage srcImage,BufferedImage destImage):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/Resizers.java:97_

### Goal
Performs a deterministic resize operation by scaling the source `BufferedImage` to fit the dimensions of the destination `BufferedImage` using the specific algorithm defined by the `Resizers` enum instance.

### Parameters
- `srcImage` (`BufferedImage`): The source image to be resized.
- `destImage` (`BufferedImage`): The destination image buffer that will receive the resized content.

### Input
The caller must provide two initialized `BufferedImage` objects. The `destImage` should be pre-allocated with the desired target dimensions. The operation is performed in-memory and is suitable for headless environments.

### Output
Returns `void` — The `destImage` is modified in-place to contain the resized representation of the `srcImage`.

### Valid Call Patterns
```java
BufferedImage sourceImage = new BufferedImageBuilder(400, 400).build();
BufferedImage destImage = new BufferedImageBuilder(200, 200).build();
net.coobird.thumbnailator.resizers.Resizers.BILINEAR.resize(sourceImage, destImage);
```

### LLM Instruction Prompt
- Use this method when you need to perform a manual, low-level resize operation using a specific algorithm (e.g., `Resizers.BILINEAR`, `Resizers.BICUBIC`, `Resizers.PROGRESSIVE_BILINEAR`).
- Always ensure the `destImage` is properly initialized with the target dimensions before calling this method.
- Do not use this method if you require the fluent `Thumbnails` pipeline, which handles these operations automatically.

### Prompt Snippet
```text
// Example of using a specific Resizer instance to perform a resize
net.coobird.thumbnailator.resizers.Resizers.BILINEAR.resize(srcImage, destImage);
```

### Common Failure Modes
- **NullPointerException:** Occurs if either `srcImage` or `destImage` is null.
- **IllegalArgumentException:** May occur if the provided `BufferedImage` objects are incompatible or have invalid dimensions.
- **In-place modification errors:** If the `destImage` is not properly prepared or is of an incompatible color model, the resulting thumbnail may have visual artifacts or incorrect color rendering.

### Fix Code Hint
```java
// Ensure destination is created with the correct type and size
BufferedImage destImage = new BufferedImage(width, height, srcImage.getType());
net.coobird.thumbnailator.resizers.Resizers.PROGRESSIVE_BILINEAR.resize(srcImage, destImage);
```

## API Test: `java:net.coobird.thumbnailator.resizers.configurations.AlphaInterpolation.getKey`

### Signature
```java
():Key
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/configurations/AlphaInterpolation.java:71_

### Goal
Retrieves the `RenderingHints.Key` associated with the alpha interpolation configuration used during image resizing operations.

### Parameters
_None._

### Input
No input parameters are required. The caller must ensure that the `AlphaInterpolation` enum constant is accessed in a context where a `RenderingHints.Key` is expected for configuring Java 2D graphics pipelines.

### Output
Returns `Key` — The `RenderingHints.Key` object corresponding to the alpha interpolation setting, which can be used to configure `Graphics2D` rendering hints.

### Valid Call Patterns
```java
import net.coobird.thumbnailator.resizers.configurations.AlphaInterpolation;
import java.awt.RenderingHints;

// Example: Retrieving the key for the AlphaInterpolation configuration
RenderingHints.Key key = AlphaInterpolation.DEFAULT.getKey();
```
*(Note: This example is inferred from the API signature and standard Java 2D usage patterns.)*

### LLM Instruction Prompt
- When calling `getKey()`, ensure it is invoked on an instance of the `AlphaInterpolation` enum (e.g., `AlphaInterpolation.DEFAULT.getKey()`).
- Do not attempt to pass arguments to this method, as it takes no parameters.
- Use the returned `Key` only in contexts where `RenderingHints` are applied to a `Graphics2D` object.

### Prompt Snippet
```text
// Accessing the rendering hint key from an AlphaInterpolation configuration
RenderingHints.Key hintKey = AlphaInterpolation.DEFAULT.getKey();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the method is called on a null reference rather than a valid `AlphaInterpolation` enum constant.
- **Type Mismatch:** Attempting to use the returned `Key` in a context that does not accept `RenderingHints.Key` objects.
- **Contextual Misuse:** Using the key outside of a `Graphics2D` rendering configuration, where it will have no effect on the thumbnail generation process.

### Fix Code Hint
```java
// Ensure the enum constant is valid before calling getKey()
if (AlphaInterpolation.DEFAULT != null) {
    RenderingHints.Key key = AlphaInterpolation.DEFAULT.getKey();
    // Apply to Graphics2D object
    graphics2D.setRenderingHint(key, ...);
}
```

## API Test: `java:net.coobird.thumbnailator.resizers.configurations.AlphaInterpolation.getValue`

### Signature
```java
():Object
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/configurations/AlphaInterpolation.java:75_

### Goal
Retrieves the rendering hint value associated with the `AlphaInterpolation` configuration for use in Java 2D image processing pipelines.

### Parameters
_None._

### Input
No input parameters are required. The caller must ensure that the `AlphaInterpolation` instance is correctly initialized as part of the `Resizer` configuration process.

### Output
Returns `Object` — The rendering hint value (typically a `java.awt.RenderingHints` value) used to configure alpha interpolation during image scaling operations.

### Valid Call Patterns
```java
// Example inferred from the API signature and ResizerConfiguration interface
Object hintValue = AlphaInterpolation.DEFAULT.getValue();
```

### LLM Instruction Prompt
- When generating code that requires specific rendering configurations, use `AlphaInterpolation.getValue()` to retrieve the underlying hint object.
- Ensure the returned `Object` is cast or used appropriately within the context of `java.awt.Graphics2D` rendering hints if manual configuration is required.
- Note that this method is part of the `ResizerConfiguration` interface and is primarily used internally by Thumbnailator's `Resizer` implementations.

### Prompt Snippet
```text
// Accessing the rendering hint value for alpha interpolation
Object hint = net.coobird.thumbnailator.resizers.configurations.AlphaInterpolation.DEFAULT.getValue();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `AlphaInterpolation` enum constant is not properly referenced or if the internal state of the configuration object is uninitialized.
- **Type Mismatch:** The returned `Object` is a generic rendering hint; attempting to use it in a context expecting a specific type (e.g., `Integer` or `String`) without proper handling will cause runtime errors.
- **Contextual Misuse:** Attempting to apply this hint to a `Graphics2D` object that does not support the specific alpha interpolation hint provided by the library.

### Fix Code Hint
```java
// Ensure the use of the enum constant before calling getValue()
import net.coobird.thumbnailator.resizers.configurations.AlphaInterpolation;

Object hint = AlphaInterpolation.QUALITY.getValue();
// Use the hint in a RenderingHints map
Map<RenderingHints.Key, Object> hints = new HashMap<>();
hints.put(RenderingHints.KEY_ALPHA_INTERPOLATION, hint);
```

## API Test: `java:net.coobird.thumbnailator.resizers.configurations.Antialiasing.getKey`

### Signature
```java
():Key
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/configurations/Antialiasing.java:71_

### Goal
Retrieves the `RenderingHints.Key` associated with the antialiasing configuration, used to define how the Java 2D rendering pipeline handles edge smoothing during image transformation.

### Parameters
_None._

### Input
No parameters are required. The caller must ensure the `Antialiasing` enum constant is accessed (e.g., `Antialiasing.ON` or `Antialiasing.OFF`) before invoking this method to retrieve its specific rendering hint key.

### Output
Returns `Key` — A `java.awt.RenderingHints.Key` object representing the antialiasing hint, which can be used to configure `Graphics2D` objects during custom image processing tasks.

### Valid Call Patterns
```java
import net.coobird.thumbnailator.resizers.configurations.Antialiasing;
import java.awt.RenderingHints;

// Retrieve the key for the ON configuration
RenderingHints.Key key = Antialiasing.ON.getKey();
```

### LLM Instruction Prompt
- When calling `getKey()`, ensure it is invoked on an instance of the `Antialiasing` enum (e.g., `Antialiasing.ON.getKey()`).
- Do not attempt to instantiate `Antialiasing` directly; use the provided enum constants.
- The returned `Key` is intended for use with `java.awt.RenderingHints` when manually configuring `Graphics2D` contexts for image operations.

### Prompt Snippet
```text
// Example of retrieving the rendering hint key from an Antialiasing configuration
RenderingHints.Key antialiasingKey = Antialiasing.ON.getKey();
```

### Common Failure Modes
- **NullPointerException:** Occurs if attempting to call `getKey()` on a null reference instead of a valid `Antialiasing` enum constant.
- **Type Mismatch:** Confusing the returned `RenderingHints.Key` with the `RenderingHints` map itself; the key is only one part of the hint configuration.

### Fix Code Hint
```java
// Ensure the enum constant is valid before calling getKey()
if (Antialiasing.ON != null) {
    RenderingHints.Key key = Antialiasing.ON.getKey();
}
```

## API Test: `java:net.coobird.thumbnailator.resizers.configurations.Antialiasing.getValue`

### Signature
```java
():Object
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/configurations/Antialiasing.java:75_

### Goal
Retrieves the specific rendering hint value associated with an `Antialiasing` configuration for use in Java 2D image processing pipelines.

### Parameters
_None._

### Input
No input parameters are required. The caller must ensure the `Antialiasing` enum constant (e.g., `Antialiasing.ON` or `Antialiasing.OFF`) is correctly initialized within the `net.coobird.thumbnailator.resizers.configurations` package before invoking this method.

### Output
Returns `Object` — The rendering hint value corresponding to the antialiasing configuration, typically used as a value in a `java.awt.RenderingHints` map.

### Valid Call Patterns
```java
// Example inferred from signature and interface definition
Object hintValue = net.coobird.thumbnailator.resizers.configurations.Antialiasing.ON.getValue();
```

### LLM Instruction Prompt
- When generating code that requires custom `RenderingHints` for image resizing, use `Antialiasing.getValue()` to retrieve the appropriate hint object from the `Antialiasing` enum constants. Do not attempt to instantiate or guess the underlying `RenderingHints` key-value pairs manually.

### Prompt Snippet
```text
// Retrieve the antialiasing hint value
Object antialiasingHint = net.coobird.thumbnailator.resizers.configurations.Antialiasing.ON.getValue();
```

### Common Failure Modes
- Calling the method on a null reference if the enum constant was not properly selected.
- Assuming the returned `Object` is a specific type (like `Integer` or `Boolean`) without checking the `RenderingHints` documentation, as the return type is generic `Object`.

### Fix Code Hint
```java
// Ensure the enum constant is valid before calling getValue()
Antialiasing config = Antialiasing.ON;
if (config != null) {
    Object hint = config.getValue();
    // Use hint in RenderingHints map
}
```

## API Test: `java:net.coobird.thumbnailator.resizers.configurations.Dithering.getKey`

### Signature
```java
():Key
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/configurations/Dithering.java:71_

### Goal
Returns the `RenderingHints.Key` associated with the `Dithering` configuration, allowing the integration of Thumbnailator's dithering settings into standard Java 2D rendering pipelines.

### Parameters
_None._

### Input
No input parameters are required. The caller must ensure that the `Dithering` enum constant (e.g., `Dithering.ENABLE` or `Dithering.DISABLE`) is accessed as the receiver.

### Output
Returns `Key` — A `java.awt.RenderingHints.Key` object representing the dithering hint, which can be used to configure `Graphics2D` rendering attributes.

### Valid Call Patterns
```java
// Example inferred from signature and interface definition
RenderingHints.Key key = Dithering.ENABLE.getKey();
```

### LLM Instruction Prompt
- When calling `getKey()`, ensure it is invoked on a specific `Dithering` enum constant (e.g., `Dithering.ENABLE` or `Dithering.DISABLE`).
- Do not attempt to instantiate `Dithering` directly; use the provided enum constants.
- The returned `Key` is intended for use with `java.awt.Graphics2D` rendering hints.

### Prompt Snippet
```text
// Accessing the rendering hint key for dithering
RenderingHints.Key ditheringKey = Dithering.ENABLE.getKey();
```

### Common Failure Modes
- **NullPointerException:** Attempting to call `getKey()` on a null reference if the enum constant was not properly initialized or retrieved.
- **Type Mismatch:** Expecting a specific value rather than the `RenderingHints.Key` object itself; the method returns the key used to identify the hint, not the hint value (e.g., `VALUE_DITHER_ENABLE`).

### Fix Code Hint
```java
// Correct usage to retrieve the key for a specific dithering configuration
import java.awt.RenderingHints;
import net.coobird.thumbnailator.resizers.configurations.Dithering;

RenderingHints.Key key = Dithering.ENABLE.getKey();
Object value = RenderingHints.VALUE_DITHER_ENABLE;
// Use key and value with graphics.setRenderingHint(key, value);
```

## API Test: `java:net.coobird.thumbnailator.resizers.configurations.Dithering.getValue`

### Signature
```java
():Object
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/configurations/Dithering.java:75_

### Goal
Retrieves the rendering hint value associated with a specific dithering configuration for use in Java 2D image processing operations.

### Parameters
_None._

### Input
No input parameters are required. The caller must ensure that the `Dithering` enum constant (e.g., `Dithering.ENABLE` or `Dithering.DISABLE`) is correctly instantiated or referenced before invoking this method.

### Output
Returns `Object` — The rendering hint value corresponding to the dithering configuration, which is intended to be used with the Java 2D `RenderingHints` API.

### Valid Call Patterns
```java
// Example inferred from signature and interface definition
Object hintValue = net.coobird.thumbnailator.resizers.configurations.Dithering.ENABLE.getValue();
```

### LLM Instruction Prompt
- When generating code that requires specific image rendering configurations, use `net.coobird.thumbnailator.resizers.configurations.Dithering` enum constants.
- Always invoke `getValue()` on the selected enum constant to retrieve the underlying `Object` required by `RenderingHints`.
- Do not attempt to pass the enum constant directly to methods expecting a `RenderingHints` value; always extract the value first.

### Prompt Snippet
```text
// To apply dithering settings to a rendering process:
Object ditheringHint = Dithering.ENABLE.getValue();
// Use ditheringHint with RenderingHints.KEY_DITHERING
```

### Common Failure Modes
- **NullPointerException:** Attempting to call `getValue()` on a null reference if the `Dithering` enum constant was not properly initialized.
- **Type Mismatch:** Expecting a specific type other than `Object` from the return value; the caller must be prepared to cast or handle the returned `Object` as required by the `RenderingHints` API.

### Fix Code Hint
```java
// Ensure the enum is accessed correctly
import net.coobird.thumbnailator.resizers.configurations.Dithering;

public Object getDitheringHint() {
    return Dithering.ENABLE.getValue();
}
```

## API Test: `java:net.coobird.thumbnailator.resizers.configurations.Rendering.getKey`

### Signature
```java
():Key
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers.configurations.Rendering.java:69_

### Goal
Retrieves the `RenderingHints.Key` associated with the `Rendering` configuration, allowing the integration of Thumbnailator's rendering settings into standard Java 2D `RenderingHints` maps.

### Parameters
_None._

### Input
No input parameters are required. The caller must ensure that the `Rendering` enum constant is accessed (e.g., `Rendering.QUALITY` or `Rendering.SPEED`) before invoking this method.

### Output
Returns `Key` — A `RenderingHints.Key` object that identifies the specific rendering hint category managed by the `Rendering` configuration.

### Valid Call Patterns
```java
// Inferred from API signature and interface definition
Rendering.QUALITY.getKey();
```

### LLM Instruction Prompt
- When generating code that requires manual configuration of `Graphics2D` rendering hints, use `getKey()` on a `Rendering` enum constant to retrieve the correct `RenderingHints.Key` for the `RenderingHints` map. Do not attempt to instantiate a new key; always use the one provided by the library's configuration enums.

### Prompt Snippet
```text
// Example of retrieving the key for a rendering configuration
RenderingHints.Key key = Rendering.QUALITY.getKey();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the method is called on a null reference rather than a valid `Rendering` enum constant.
- **Type Mismatch:** Attempting to use the returned `Key` with incompatible `RenderingHints` values that do not align with the expected `Rendering` configuration.

### Fix Code Hint
```java
// Ensure the enum constant is valid before calling getKey()
Rendering config = Rendering.QUALITY;
if (config != null) {
    RenderingHints.Key key = config.getKey();
    // Use key with RenderingHints.VALUE_RENDER_QUALITY or similar
}
```

## API Test: `java:net.coobird.thumbnailator.resizers.configurations.Rendering.getValue`

### Signature
```java
():Object
```
_Source: source/src/main/java/net/coobird/thumbnailator/resizers/configurations/Rendering.java:73_

### Goal
Retrieves the specific rendering hint value associated with a `Rendering` configuration, used to influence the Java 2D rendering pipeline during thumbnail generation.

### Parameters
_None._

### Input
No input parameters are required. The caller must ensure the `Rendering` enum constant is correctly initialized or selected from the available `net.coobird.thumbnailator.resizers.configurations.Rendering` constants (e.g., `SPEED`, `BALANCED`, `QUALITY`).

### Output
Returns `Object` — The underlying rendering hint value (typically a `java.awt.RenderingHints.Key` value) that corresponds to the selected `Rendering` configuration.

### Valid Call Patterns
```java
// Inferred from API signature and interface definition
Object hintValue = net.coobird.thumbnailator.resizers.configurations.Rendering.QUALITY.getValue();
```

### LLM Instruction Prompt
- When generating code that requires specific rendering behavior, use the `net.coobird.thumbnailator.resizers.configurations.Rendering` enum constants.
- Call `getValue()` on the selected enum constant to retrieve the raw rendering hint object required by the underlying Java 2D `Graphics2D` context.
- Do not attempt to instantiate `Rendering` directly; use the provided enum members.

### Prompt Snippet
```text
// Example of retrieving a rendering hint value
Rendering config = Rendering.QUALITY;
Object hint = config.getValue();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `Rendering` enum constant is not properly referenced or if the internal state of the configuration object is accessed before initialization.
- **Type Mismatch:** The returned `Object` is a generic rendering hint value; attempting to cast it to an incorrect type (e.g., `String` or `Integer`) instead of the expected `java.awt.RenderingHints` value will cause a `ClassCastException`.

### Fix Code Hint
```java
// Ensure you are using the enum constants provided by the library
import net.coobird.thumbnailator.resizers.configurations.Rendering;

// Correct usage
Object renderingHint = Rendering.QUALITY.getValue();
```

## API Test: `java:net.coobird.thumbnailator.tasks.FileThumbnailTask`

### Signature
```java
(ThumbnailParameter param,File sourceFile,File destinationFile):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/FileThumbnailTask.java:61_

### Goal
`FileThumbnailTask` performs a granular thumbnail generation operation by reading image data from a source `File` and writing the processed result to a destination `File` according to the provided `ThumbnailParameter` configuration.

### Parameters
- `param` (`ThumbnailParameter`): The configuration object containing resizing, filtering, and output settings for the task.
- `sourceFile` (`File`): The filesystem path to the source image file to be processed.
- `destinationFile` (`File`): The filesystem path where the resulting thumbnail will be saved.

### Input
- **Data:** A valid `File` object pointing to an image format supported by the Java Image I/O API.
- **Preconditions:** The `ThumbnailParameter` must be fully configured before instantiation. The task only processes the first image in a multi-image file; subsequent images are ignored.
- **Constraints:** The `ThumbnailParameter` object is mutable and should not be reused across multiple tasks.

### Output
Returns `null` — The operation performs side-effect-based I/O, writing the processed image directly to the specified `destinationFile`.

### Valid Call Patterns
```java
ThumbnailParameter param = new ThumbnailParameter(
    new Dimension(200, 200), 
    null, 
    true, 
    "jpg", 
    null, 
    0.85, 
    0, 
    null, 
    null, 
    null, 
    false
);
File source = new File("input.jpg");
File dest = new File("output.jpg");

new net.coobird.thumbnailator.tasks.FileThumbnailTask(param, source, dest);
```
*(Note: Example inferred from signature and task-based processing documentation.)*

### LLM Instruction Prompt
- Always instantiate `FileThumbnailTask` with a pre-configured `ThumbnailParameter`.
- Ensure the `sourceFile` exists and is readable before task execution.
- Do not reuse `ThumbnailParameter` instances across multiple `FileThumbnailTask` objects.
- Use this class only when granular control over I/O is required; otherwise, prefer the fluent `Thumbnails` API.

### Prompt Snippet
```text
// Example of manual task instantiation
ThumbnailParameter params = ...; // Configure parameters
File source = new File("path/to/source.jpg");
File dest = new File("path/to/dest.jpg");
new net.coobird.thumbnailator.tasks.FileThumbnailTask(params, source, dest);
```

### Common Failure Modes
- **NullPointerException:** Thrown if the `ThumbnailParameter` argument is `null`.
- **IllegalStateException:** Occurs if the `ThumbnailParameter` contains conflicting settings (e.g., calling `scale()` after `size()`).
- **I/O Errors:** Failure to read the `sourceFile` or write to the `destinationFile` due to filesystem permissions or unsupported image formats.
- **Memory Issues:** Processing very large images without appropriate memory management settings.

### Fix Code Hint
```java
// Ensure parameters are valid and files exist
if (sourceFile.exists()) {
    ThumbnailParameter param = new ThumbnailParameterBuilder().size(200, 200).build();
    new net.coobird.thumbnailator.tasks.FileThumbnailTask(param, sourceFile, destinationFile);
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.FileThumbnailTask.getDestination`

### Signature
```java
():File
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/FileThumbnailTask.java:90_

### Goal
Retrieves the destination `File` object where the thumbnail is configured to be stored or written by the `FileThumbnailTask`.

### Parameters
_None._

### Input
The `FileThumbnailTask` must have been initialized with a valid destination `File` (or `FileImageSink`) during its construction. No specific preconditions are required to call this getter, provided the task instance is fully instantiated.

### Output
Returns `File` — The destination file path where the processed thumbnail will be saved.

### Valid Call Patterns
```java
// Inferred from API signature:
File destination = fileThumbnailTask.getDestination();
```

### LLM Instruction Prompt
- When using `FileThumbnailTask`, call `getDestination()` to verify the target filesystem path before initiating the task execution. Ensure the task is not null before invoking this method.

### Prompt Snippet
```text
File destination = task.getDestination();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `FileThumbnailTask` instance itself is null.
- **Uninitialized Destination:** If the task was constructed without a valid destination path, the returned `File` object may point to an invalid or null location, leading to `IOException` during subsequent task processing.

### Fix Code Hint
```java
if (task != null) {
    File dest = task.getDestination();
    // Proceed with validation or processing
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.FileThumbnailTask.getParam`

### Signature
```java
():ThumbnailParameter
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/FileThumbnailTask.java:80_

### Goal
Retrieves the `ThumbnailParameter` configuration object associated with a `FileThumbnailTask`, which defines the specific transformation settings for the thumbnail generation operation.

### Parameters
_None._

### Input
The caller must have an initialized `FileThumbnailTask` instance. No arguments are required for this method.

### Output
Returns `ThumbnailParameter` — The configuration object containing settings such as resizing dimensions, filters, and output quality that will be applied during the task execution.

### Valid Call Patterns
```java
// Inferred from API signature:
FileThumbnailTask task = new FileThumbnailTask(source, sink);
ThumbnailParameter params = task.getParam();
```

### LLM Instruction Prompt
- Use `getParam()` only on an existing `FileThumbnailTask` instance.
- Do not attempt to modify the returned `ThumbnailParameter` if the task is already in progress, as `ThumbnailParameter` is not thread-safe.
- Ensure the task has been properly initialized with a valid source and sink before calling this method.

### Prompt Snippet
```text
ThumbnailParameter params = fileThumbnailTask.getParam();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `FileThumbnailTask` instance is null.
- **IllegalStateException:** May occur if the task state is invalid or if the underlying configuration has not been initialized.
- **Thread Safety Issues:** Modifying the returned `ThumbnailParameter` in a multi-threaded environment may lead to unpredictable results, as the object is not thread-safe.

### Fix Code Hint
```java
// Ensure the task is initialized before accessing parameters
if (task != null) {
    ThumbnailParameter params = task.getParam();
    // Use params for inspection or configuration
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.FileThumbnailTask.getSource`

### Signature
```java
():File
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/FileThumbnailTask.java:85_

### Goal
Retrieves the `File` object representing the source image location associated with a `FileThumbnailTask`.

### Parameters
_None._

### Input
The caller must have an initialized `FileThumbnailTask` instance. The task must have been constructed with a valid `File` object representing an image file supported by the Java Image I/O API.

### Output
Returns `File` — The source file object from which the image is read for the thumbnail generation process.

### Valid Call Patterns
```java
FileThumbnailTask task = new FileThumbnailTask(new ThumbnailParameter(null, null, true, "jpg", 0.85f, null, null, null, null, null), new FileImageSource(new File("input.jpg")), new FileImageSink(new File("output.jpg")));
File sourceFile = task.getSource();
```

### LLM Instruction Prompt
- When calling `getSource()`, ensure the `FileThumbnailTask` has been properly instantiated with a `FileImageSource`.
- Do not assume the returned `File` exists or is readable; verify the file state if necessary before performing subsequent I/O operations.
- This method is part of the task-based processing workflow; it is not used in the fluent `Thumbnails` API.

### Prompt Snippet
```text
File source = task.getSource();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `FileThumbnailTask` instance itself is null.
- **Task Misuse:** Attempting to call this on a task that was not initialized with a `File` source (e.g., a `StreamThumbnailTask` or `BufferedImageSource` context) will result in a type mismatch or logic error if the task implementation does not support file-based retrieval.

### Fix Code Hint
```java
// Ensure the task is a FileThumbnailTask before calling getSource
if (task instanceof FileThumbnailTask) {
    File source = ((FileThumbnailTask) task).getSource();
    // Proceed with file-specific logic
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.FileThumbnailTask.read`

### Signature
```java
():BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/FileThumbnailTask.java:70_

### Goal
Reads the source image from the file associated with the `FileThumbnailTask` instance and returns it as a `BufferedImage`.

### Parameters
_None._

### Input
The `FileThumbnailTask` must be initialized with a valid source file. The underlying Java Image I/O API must support the format of the source file. If the file contains multiple images, this method will only process the first image.

### Output
Returns `BufferedImage` — The image data obtained from the source file, ready for further processing or transformation within the Thumbnailator pipeline.

### Valid Call Patterns
```java
// Inferred from API signature and ThumbnailTask usage
FileThumbnailTask task = new FileThumbnailTask(new File("input.jpg"), new File("output.jpg"));
BufferedImage image = task.read();
```

### LLM Instruction Prompt
- Use `read()` only after the `FileThumbnailTask` has been correctly instantiated with a valid source file.
- Ensure the calling environment has appropriate file system permissions to read the source file.
- Handle `IOException` as this method performs disk I/O.

### Prompt Snippet
```text
FileThumbnailTask task = new FileThumbnailTask(sourceFile, destinationFile);
BufferedImage img = task.read();
```

### Common Failure Modes
- `IOException`: Occurs if the file is inaccessible, does not exist, or is not a valid image format recognized by the Java Image I/O API.
- `NullPointerException`: Occurs if the `FileThumbnailTask` was initialized with a null file reference.
- Multi-image files: If the source file contains multiple images (e.g., animated GIF or multi-page TIFF), only the first image is read; subsequent images are ignored.

### Fix Code Hint
```java
try {
    BufferedImage img = task.read();
    // Proceed with processing
} catch (IOException e) {
    // Handle I/O error (e.g., log or rethrow)
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.FileThumbnailTask.write`

### Signature
```java
(BufferedImage img):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/FileThumbnailTask.java:75_

### Goal
Writes a processed `BufferedImage` to the destination file defined during the initialization of the `FileThumbnailTask`.

### Parameters
- `img` (`BufferedImage`): The image data to be written to the destination file.

### Input
The caller must provide a valid `BufferedImage` object. The `FileThumbnailTask` must have been previously initialized with a valid destination file path. The operation relies on the Java Image I/O API; therefore, the output format must be supported by the underlying JRE/JDK environment.

### Output
Returns `void` — The method performs a side-effect by writing the image data to the filesystem.

### Valid Call Patterns
```java
// Inferred from API signature and ThumbnailTask usage
FileThumbnailTask task = new FileThumbnailTask(sourceFile, destinationFile);
task.write(img);
```

### LLM Instruction Prompt
- When using `FileThumbnailTask.write`, ensure the `BufferedImage` is fully processed (resized, filtered, etc.) before passing it to the method.
- Always wrap the call in a `try-catch` block to handle `IOException` or `UnsupportedFormatException`.
- Ensure the destination file path is writable and the directory structure exists.

### Prompt Snippet
```text
// Example of writing a BufferedImage using FileThumbnailTask
try {
    task.write(processedImage);
} catch (IOException e) {
    // Handle I/O error
}
```

### Common Failure Modes
- `IOException`: Occurs if there is a problem writing to the filesystem (e.g., disk full, permission denied).
- `UnsupportedFormatException`: Occurs if the output format is not supported by the Java Image I/O implementation.
- `NullPointerException`: Occurs if the `img` parameter is null.
- `IllegalStateException`: Occurs if the task is not properly configured or the destination is invalid.

### Fix Code Hint
```java
// Ensure the image is not null and handle potential I/O exceptions
if (img != null) {
    try {
        task.write(img);
    } catch (IOException e) {
        System.err.println("Failed to write thumbnail: " + e.getMessage());
    }
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.SourceSinkThumbnailTask`

### Signature
```java
(ThumbnailParameter param,ImageSource<S> source,ImageSink<D> destination):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/SourceSinkThumbnailTask.java:77_

### Goal
Creates a task that orchestrates the retrieval of an image from a specified `ImageSource` and writes the processed thumbnail to a specified `ImageSink` using the provided `ThumbnailParameter` configuration.

### Parameters
- `param` (`ThumbnailParameter`): The configuration object defining resizing, filtering, and output quality settings for the thumbnail generation.
- `source` (`ImageSource<S>`): The source provider from which the original image is read.
- `destination` (`ImageSink<D>`): The destination provider to which the resulting thumbnail is written.

### Input
- **Data:** Requires a valid `ThumbnailParameter` instance, an `ImageSource` (e.g., `FileImageSource`, `InputStreamImageSource`), and an `ImageSink` (e.g., `FileImageSink`, `OutputStreamImageSink`).
- **Preconditions:** The `ThumbnailParameter` must be fully configured before task execution. The `ImageSource` must point to a valid image format supported by the underlying Java Image I/O API.
- **Constraints:** Only the first image in a multi-image file is processed; subsequent images are ignored.

### Output
Returns `null` — The operation performs the transformation and I/O side-effects directly; the result is stored in the provided `ImageSink`.

### Valid Call Patterns
```java
// Example inferred from the constructor signature
new net.coobird.thumbnailator.tasks.SourceSinkThumbnailTask(param, source, destination);
```

### LLM Instruction Prompt
- When using `SourceSinkThumbnailTask`, ensure that the `ThumbnailParameter` is not reused across multiple tasks as it is mutable and not thread-safe.
- Always verify that the `ImageSource` and `ImageSink` are initialized with valid paths or streams before passing them to the constructor.
- The task does not return a value; ensure the `ImageSink` is properly configured to handle the output format (e.g., JPEG, PNG) before execution.

### Prompt Snippet
```text
net.coobird.thumbnailator.tasks.SourceSinkThumbnailTask task = new net.coobird.thumbnailator.tasks.SourceSinkThumbnailTask(param, source, destination);
```

### Common Failure Modes
- `NullPointerException`: Thrown if any of the three arguments (`param`, `source`, or `destination`) are `null`.
- `IllegalStateException`: Occurs if the `ThumbnailParameter` is misconfigured (e.g., calling `scale()` after `size()` has already been set).
- `IOException`: May occur during the read/write process if the source file is inaccessible or the destination path is invalid.

### Fix Code Hint
```java
// Ensure parameters are non-null and properly initialized
if (param != null && source != null && destination != null) {
    new net.coobird.thumbnailator.tasks.SourceSinkThumbnailTask(param, source, destination);
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.SourceSinkThumbnailTask.getDestination`

### Signature
```java
():D
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/SourceSinkThumbnailTask.java:125_

### Goal
Retrieves the destination object (sink) associated with the current thumbnail task, representing the target where the processed image data will be written.

### Parameters
_None._

### Input
The caller must ensure that the `SourceSinkThumbnailTask` has been properly initialized with a destination sink (e.g., `FileImageSink`, `OutputStreamImageSink`, or `BufferedImageSink`). No specific preconditions are required to call this method, provided the task instance is not null.

### Output
Returns `D` — The destination object (sink) configured for the task, which defines the output medium (file, stream, or memory buffer) for the generated thumbnail.

### Valid Call Patterns
```java
// Inferred from API signature:
SourceSinkThumbnailTask<S, D> task = ...;
D destination = task.getDestination();
```

### LLM Instruction Prompt
- When calling `getDestination()`, ensure the `SourceSinkThumbnailTask` instance is fully initialized.
- The return type `D` is generic and corresponds to the specific sink type (e.g., `File`, `OutputStream`, or `BufferedImage`) defined during the task's construction.
- Do not assume the destination is writable or open; this method only returns the reference to the configured sink.

### Prompt Snippet
```text
// Accessing the destination of a task
Object destination = task.getDestination();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `SourceSinkThumbnailTask` instance itself is null.
- **Type Mismatch:** If the generic type `D` is incorrectly assumed, casting the result to an incompatible type will cause a `ClassCastException`.
- **Uninitialized Task:** If the task was created without a valid sink, the returned destination may be null or in an invalid state depending on the specific implementation.

### Fix Code Hint
```java
// Ensure the task is not null before accessing the destination
if (task != null) {
    Object dest = task.getDestination();
    // Proceed with handling the destination
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.SourceSinkThumbnailTask.getSource`

### Signature
```java
():S
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/SourceSinkThumbnailTask.java:120_

### Goal
Retrieves the source object associated with a `SourceSinkThumbnailTask`, representing the origin from which the image data is read or retrieved.

### Parameters
_None._

### Input
The task must be initialized with a valid source (e.g., `File`, `InputStream`, `URL`, or `BufferedImage`). The caller must ensure the task has been properly instantiated with a source implementation before invoking this method.

### Output
Returns `S` — The source object (e.g., `FileImageSource`, `InputStreamImageSource`, etc.) that provides the image data for the thumbnail generation task.

### Valid Call Patterns
```java
// Inferred from signature:
SourceSinkThumbnailTask<S, D> task = ...;
S source = task.getSource();
```

### LLM Instruction Prompt
- When calling `getSource()`, ensure the `SourceSinkThumbnailTask` instance is already initialized.
- The return type `S` is generic and corresponds to the specific `ImageSource` implementation used during the task's construction.
- Do not attempt to modify the returned source object directly if the task is currently executing, as this may lead to inconsistent state.

### Prompt Snippet
```text
// Accessing the source of a task
SourceSinkThumbnailTask task = ...;
Object source = task.getSource();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `SourceSinkThumbnailTask` was initialized with a null source or if the task instance itself is null.
- **Type Mismatch:** If the generic type `S` is incorrectly assumed, casting the result of `getSource()` to an incompatible `ImageSource` type will throw a `ClassCastException`.

### Fix Code Hint
```java
// Ensure the task is not null before accessing the source
if (task != null) {
    Object source = task.getSource();
    // Handle the source based on its specific type
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.SourceSinkThumbnailTask.read`

### Signature
```java
():BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/SourceSinkThumbnailTask.java:93_

### Goal
Reads an image from the configured source associated with the `SourceSinkThumbnailTask` and returns it as a `BufferedImage` for further processing.

### Parameters
_None._

### Input
The task must be initialized with a valid source (e.g., `File`, `InputStream`, `URL`, or `BufferedImage`). The source must contain readable image data compatible with the Java Image I/O API. If the source is a multi-image file, this method will only process the first image.

### Output
Returns `BufferedImage` — The image data retrieved from the source.

### Valid Call Patterns
```java
// Inferred from API signature and ThumbnailTask contract
SourceSinkThumbnailTask task = new SourceSinkThumbnailTask(source, sink, param);
BufferedImage image = task.read();
```

### LLM Instruction Prompt
- Use `read()` only after the `SourceSinkThumbnailTask` has been instantiated with a valid source and sink.
- Ensure the task is not reused across multiple threads, as `ThumbnailParameter` and task state are not thread-safe.
- Handle `IOException` as this method performs I/O operations.

### Prompt Snippet
```text
// Example of reading an image via a task
try {
    BufferedImage img = task.read();
    // Proceed with processing
} catch (IOException e) {
    // Handle I/O error
}
```

### Common Failure Modes
- `IOException`: Thrown if the source is inaccessible, the file format is unsupported, or the image data is corrupted.
- `IllegalStateException`: May occur if the task is invoked in an invalid state or if the underlying source has been closed.
- Multi-image limitation: If the source file contains multiple frames (e.g., animated GIF or multi-page TIFF), only the first frame is returned.

### Fix Code Hint
```java
// Ensure the source is valid and the task is properly initialized
SourceSinkThumbnailTask task = new SourceSinkThumbnailTask(source, sink, param);
try {
    BufferedImage img = task.read();
} catch (IOException e) {
    logger.error("Failed to read image from source", e);
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.SourceSinkThumbnailTask.write`

### Signature
```java
(BufferedImage img):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/SourceSinkThumbnailTask.java:101_

### Goal
Writes a processed `BufferedImage` to the destination defined by the `SourceSinkThumbnailTask` instance.

### Parameters
- `img` (`BufferedImage`): The thumbnail image to be written to the configured output destination.

### Input
- **Data:** A `BufferedImage` object that has typically undergone transformation (resizing, filtering, etc.).
- **Preconditions:** The `SourceSinkThumbnailTask` must be correctly initialized with a valid destination (e.g., `File`, `OutputStream`).
- **Formats:** The output format is determined by the task configuration; it must be a format supported by the underlying Java Image I/O API.

### Output
Returns `void` — The method performs a side-effect operation by writing the image data to the destination sink.

### Valid Call Patterns
```java
// Inferred from API signature and task-based processing pattern
SourceSinkThumbnailTask task = new SourceSinkThumbnailTask(source, sink, param);
task.write(thumbnailImage);
```

### LLM Instruction Prompt
- When using `SourceSinkThumbnailTask.write`, ensure the `BufferedImage` has been fully processed by the `ThumbnailParameter` configuration.
- This method is intended for granular task-based processing; for standard workflows, prefer the fluent `Thumbnails` API.
- Always handle `IOException` and `UnsupportedFormatException` as this method performs I/O operations.

### Prompt Snippet
```text
// Example of writing a processed image via a task
try {
    task.write(processedImage);
} catch (IOException e) {
    // Handle I/O failure
}
```

### Common Failure Modes
- **`IOException`**: Occurs if the destination is inaccessible, the disk is full, or the output stream is closed.
- **`UnsupportedFormatException`**: Occurs if the configured output format is not supported by the current JRE's Image I/O providers.
- **`IllegalStateException`**: May occur if the task is invoked before the source or destination is properly configured.

### Fix Code Hint
```java
// Ensure the destination is writable and the image is not null before calling write
if (img != null) {
    try {
        task.write(img);
    } catch (IOException e) {
        logger.error("Failed to write thumbnail", e);
    }
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.StreamThumbnailTask`

### Signature
```java
(ThumbnailParameter param,InputStream is,OutputStream os):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/StreamThumbnailTask.java:66_

### Goal
Performs a thumbnail generation task by reading source image data from an `InputStream` and writing the processed result to an `OutputStream` according to the provided `ThumbnailParameter` configuration.

### Parameters
- `param` (`ThumbnailParameter`): The configuration object defining resizing, filtering, and output format settings for the task.
- `is` (`InputStream`): The source stream containing the raw image data to be processed.
- `os` (`OutputStream`): The destination stream where the generated thumbnail image data will be written.

### Input
The caller must provide a valid `InputStream` containing image data in a format supported by the underlying Java Image I/O API. The `ThumbnailParameter` must be fully configured (e.g., size, scaling, or filters) before being passed to the task. Note that this task only processes the first image found in multi-image files.

### Output
Returns `null`. The operation performs side-effect-based I/O, writing the resulting thumbnail directly to the provided `OutputStream`.

### Valid Call Patterns
```java
// Example inferred from API signature and task-based processing patterns
ThumbnailParameter param = new ThumbnailParameterBuilder()
    .size(200, 200)
    .build();
new StreamThumbnailTask(param, inputStream, outputStream);
```

### LLM Instruction Prompt
- When using `StreamThumbnailTask`, ensure the `ThumbnailParameter` is not reused across multiple tasks as it is not thread-safe.
- Always ensure the `InputStream` and `OutputStream` are properly closed by the caller after the task completes to prevent resource leaks.
- Use this class only when granular control over I/O streams is required; otherwise, prefer the fluent `Thumbnails` API.

### Prompt Snippet
```text
new net.coobird.thumbnailator.tasks.StreamThumbnailTask(param, is, os);
```

### Common Failure Modes
- `NullPointerException`: Thrown if any of the arguments (`param`, `is`, or `os`) are `null`.
- `IOException`: Occurs if the `InputStream` is invalid, the image format is unsupported, or the `OutputStream` is inaccessible.
- `IllegalStateException`: May occur if the `ThumbnailParameter` is misconfigured (e.g., conflicting size and scale settings).

### Fix Code Hint
```java
try (InputStream is = new FileInputStream("input.jpg");
     OutputStream os = new FileOutputStream("output.jpg")) {
    ThumbnailParameter param = new ThumbnailParameterBuilder().size(100, 100).build();
    new StreamThumbnailTask(param, is, os);
} catch (IOException e) {
    // Handle I/O errors
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.StreamThumbnailTask.getDestination`

### Signature
```java
():OutputStream
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/StreamThumbnailTask.java:95_

### Goal
Retrieves the `OutputStream` destination associated with a `StreamThumbnailTask` where the generated thumbnail data will be written.

### Parameters
_None._

### Input
The caller must ensure that the `StreamThumbnailTask` has been correctly initialized with a valid `OutputStream` sink. As this is a task-based API, the task must be in a state where the destination has been defined, typically during the construction of the `StreamThumbnailTask` instance.

### Output
Returns `OutputStream` — The destination stream where the processed image data is to be written.

### Valid Call Patterns
```java
// Inferred from API signature; assumes an existing StreamThumbnailTask instance
OutputStream os = task.getDestination();
```

### LLM Instruction Prompt
- When using `StreamThumbnailTask`, call `getDestination()` only after the task has been instantiated with a valid `OutputStream`.
- Do not assume the stream is open or ready for writing; verify the task lifecycle state if necessary.
- Use this method when you need to inspect or manually interact with the output sink of a granular thumbnail task.

### Prompt Snippet
```text
StreamThumbnailTask task = new StreamThumbnailTask(source, sink, param);
OutputStream destination = task.getDestination();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `StreamThumbnailTask` was initialized with a null destination.
- **IllegalStateException:** May occur if the task has already been closed or if the destination is accessed in an invalid lifecycle phase of the task execution.

### Fix Code Hint
```java
// Ensure the task is properly initialized before accessing the destination
if (task != null) {
    OutputStream os = task.getDestination();
    // Proceed with operations on the output stream
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.StreamThumbnailTask.getParam`

### Signature
```java
():ThumbnailParameter
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/StreamThumbnailTask.java:85_

### Goal
Retrieves the `ThumbnailParameter` configuration object associated with a `StreamThumbnailTask`, which defines the settings for the thumbnail generation operation.

### Parameters
_None._

### Input
The caller must have an initialized `StreamThumbnailTask` instance. No arguments are required for this method.

### Output
Returns `ThumbnailParameter` — an object containing the configuration (such as dimensions, scaling, filters, and output format) that will be applied during the thumbnail generation process.

### Valid Call Patterns
```java
// Inferred from API signature:
StreamThumbnailTask task = new StreamThumbnailTask(inputStream, outputStream);
ThumbnailParameter param = task.getParam();
```

### LLM Instruction Prompt
- Use `getParam()` only on an existing `StreamThumbnailTask` instance.
- Do not attempt to modify the returned `ThumbnailParameter` if the task is already in progress, as `ThumbnailParameter` is not thread-safe.
- Ensure the task has been properly initialized with valid `InputStream` and `OutputStream` sources before accessing parameters.

### Prompt Snippet
```text
ThumbnailParameter params = streamThumbnailTask.getParam();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `StreamThumbnailTask` instance is null.
- **IllegalStateException:** May occur if the task state is invalid or if the parameter object is accessed in a multi-threaded context where the task configuration is being mutated concurrently.

### Fix Code Hint
```java
// Ensure the task is instantiated before retrieving parameters
StreamThumbnailTask task = new StreamThumbnailTask(in, out);
ThumbnailParameter params = task.getParam();
// Configure parameters if necessary before task execution
params.setOutputFormat("jpg");
```

## API Test: `java:net.coobird.thumbnailator.tasks.StreamThumbnailTask.getSource`

### Signature
```java
():InputStream
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/StreamThumbnailTask.java:90_

### Goal
Retrieves the `InputStream` associated with a `StreamThumbnailTask`, providing access to the raw image data source for the thumbnail generation process.

### Parameters
_None._

### Input
The `StreamThumbnailTask` must have been initialized with a valid `InputStream` source. As a `ThumbnailTask` implementation, this task is designed for headless environments and processes only the first image found within the provided stream; subsequent images in multi-image streams are ignored.

### Output
Returns `InputStream` — The source stream from which the image data is read for processing.

### Valid Call Patterns
```java
// Inferred from API signature:
StreamThumbnailTask task = new StreamThumbnailTask(param, inputStream, outputStream);
InputStream source = task.getSource();
```

### LLM Instruction Prompt
- When using `StreamThumbnailTask.getSource()`, ensure the `StreamThumbnailTask` instance has been correctly instantiated with a non-null `InputStream`. 
- Do not attempt to use this method to retrieve multiple images from a single stream, as Thumbnailator tasks are constrained to processing only the first image.
- Ensure the `InputStream` remains open and accessible until the task execution is complete.

### Prompt Snippet
```text
// Accessing the source stream from a StreamThumbnailTask instance
InputStream source = streamThumbnailTask.getSource();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `StreamThumbnailTask` was initialized with a null `InputStream`.
- **IOException:** May occur if the underlying stream is closed or inaccessible when `getSource()` is invoked or subsequently read.
- **Task Misuse:** Attempting to use the task for multi-image files, as the library only supports the first image in the stream.

### Fix Code Hint
```java
// Ensure the task is properly initialized before calling getSource()
if (task != null) {
    try (InputStream is = task.getSource()) {
        // Process the stream
    } catch (Exception e) {
        // Handle potential I/O errors
    }
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.StreamThumbnailTask.read`

### Signature
```java
():BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/StreamThumbnailTask.java:75_

### Goal
Reads an image from the associated `InputStream` source and returns it as a `BufferedImage` for further processing.

### Parameters
_None._

### Input
The caller must ensure the `StreamThumbnailTask` has been initialized with a valid `InputStream`. The input must be a format supported by the underlying Java Image I/O API. Per project constraints, this task only processes the first image in a multi-image file; subsequent images are ignored.

### Output
Returns `BufferedImage` — The image obtained from the source stream.

### Valid Call Patterns
```java
// Inferred from API signature and class purpose
StreamThumbnailTask task = new StreamThumbnailTask(inputStream, outputStream, param);
BufferedImage image = task.read();
```

### LLM Instruction Prompt
- When using `StreamThumbnailTask.read()`, ensure the `InputStream` is open and points to a valid image format.
- Always handle the `IOException` that may be thrown if the stream is inaccessible or the image data is corrupt.
- Note that this method is part of the task-based processing workflow; for standard fluent pipelines, prefer `Thumbnails.of(InputStream)`.

### Prompt Snippet
```text
StreamThumbnailTask task = new StreamThumbnailTask(inputStream, outputStream, param);
BufferedImage img = task.read();
```

### Common Failure Modes
- `IOException`: Occurs if the underlying stream is closed, unreachable, or contains data that cannot be decoded by the Java Image I/O API.
- `NullPointerException`: Occurs if the `InputStream` provided to the `StreamThumbnailTask` constructor was null.
- Multi-image limitation: If the input stream contains a multi-page file (e.g., multi-page TIFF), only the first image will be read.

### Fix Code Hint
```java
try {
    BufferedImage img = task.read();
    // Proceed with processing
} catch (IOException e) {
    // Handle I/O error (e.g., log or rethrow)
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.StreamThumbnailTask.write`

### Signature
```java
(BufferedImage img):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/StreamThumbnailTask.java:80_

### Goal
Writes a processed `BufferedImage` to the destination `OutputStream` associated with the `StreamThumbnailTask`.

### Parameters
- `img` (`BufferedImage`): The image data to be written to the output stream.

### Input
- **Data:** A `BufferedImage` object, typically resulting from a transformation pipeline.
- **Preconditions:** The `StreamThumbnailTask` must be initialized with a valid `OutputStream`. The task must be in a state ready to perform the write operation.
- **Formats:** The output format is determined by the configuration of the `StreamThumbnailTask` (e.g., JPEG, PNG), relying on the underlying Java Image I/O API.

### Output
Returns `void` — The method performs a side-effect by writing the image data to the configured `OutputStream`.

### Valid Call Patterns
```java
// Example inferred from API signature and task-based processing pattern
StreamThumbnailTask task = new StreamThumbnailTask(param, source, sink);
task.write(bufferedImage);
```

### LLM Instruction Prompt
- When using `StreamThumbnailTask.write`, ensure the `BufferedImage` is fully processed and ready for output.
- Always wrap the call in a `try-catch` block to handle `IOException` or `UnsupportedFormatException`.
- Ensure the `OutputStream` provided to the task constructor is open and writable.

### Prompt Snippet
```text
// Ensure the task is initialized with a valid sink before calling write
task.write(myBufferedImage);
```

### Common Failure Modes
- **`IOException`:** Occurs if there is a problem writing to the underlying `OutputStream` (e.g., stream closed, disk full).
- **`UnsupportedFormatException`:** Occurs if the image format is not supported by the registered Java Image I/O writers.
- **`NullPointerException`:** Occurs if the `img` parameter is null.

### Fix Code Hint
```java
try {
    task.write(img);
} catch (IOException e) {
    // Handle I/O error during write
    e.printStackTrace();
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.ThumbnailTask.getParam`

### Signature
```java
():ThumbnailParameter
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/ThumbnailTask.java:110_

### Goal
Retrieves the `ThumbnailParameter` object associated with a `ThumbnailTask`, which encapsulates the configuration settings (such as size, scaling, and filters) to be applied during the thumbnail generation process.

### Parameters
_None._

### Input
The caller must have an initialized instance of a `ThumbnailTask` (e.g., `FileThumbnailTask`, `StreamThumbnailTask`, or `SourceSinkThumbnailTask`). No arguments are required for this method.

### Output
Returns `ThumbnailParameter` — An object containing the specific configuration parameters (e.g., dimensions, aspect ratio, rotation, and filters) that define how the thumbnail will be processed for the current task.

### Valid Call Patterns
```java
// Inferred from API signature:
ThumbnailParameter params = task.getParam();
```

### LLM Instruction Prompt
- Use `getParam()` only when you have an existing `ThumbnailTask` instance.
- Do not attempt to modify the returned `ThumbnailParameter` if the task is already in progress, as `ThumbnailParameter` is not thread-safe.
- Use this method to inspect the current configuration of a task before execution.

### Prompt Snippet
```text
ThumbnailTask task = ...; // Existing task instance
ThumbnailParameter params = task.getParam();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `ThumbnailTask` instance is null.
- **IllegalState/Configuration Errors:** If the `ThumbnailParameter` is modified after the task has already begun processing, the behavior may be inconsistent or lead to errors, as the parameter object is intended to be configured prior to task execution.

### Fix Code Hint
```java
// Ensure the task is fully configured before retrieving parameters
ThumbnailTask task = new FileThumbnailTask(source, sink, param);
ThumbnailParameter currentParams = task.getParam();
```

## API Test: `java:net.coobird.thumbnailator.tasks.UnsupportedFormatException`

### Signature
```java
(String formatName,String s):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/UnsupportedFormatException.java:70_

### Goal
Signals that a requested image format is not supported by the current Java Image I/O environment during a thumbnail generation task.

### Parameters
- `formatName` (`String`): The name of the image format that caused the exception (e.g., "webp", "heif").
- `s` (`String`): A descriptive message providing additional context regarding the unsupported format error.

### Input
The caller must provide a `String` representing the format name and a `String` message. This exception is typically thrown when a `ThumbnailTask` attempts to process an image format that is not registered with the underlying `ImageIO` service provider interface.

### Output
Returns `null` — This is a constructor for an exception class; it does not return a value but instead initializes an `UnsupportedFormatException` object to be thrown.

### Valid Call Patterns
```java
throw new net.coobird.thumbnailator.tasks.UnsupportedFormatException("webp", "The format 'webp' is not supported by the current ImageIO configuration.");
```

### LLM Instruction Prompt
- Use this exception when implementing custom `ThumbnailTask` logic or validation routines where an image format is explicitly rejected or unsupported.
- Always provide the `formatName` as the first argument and a descriptive error message as the second.
- Ensure the exception is thrown within the context of a task-based processing workflow.

### Prompt Snippet
```text
// Example of throwing the exception when a format is unsupported
if (!isSupported(format)) {
    throw new net.coobird.thumbnailator.tasks.UnsupportedFormatException(format, "Format not supported by ImageIO.");
}
```

### Common Failure Modes
- **Missing Format Support:** Attempting to process formats (like WebP or HEIF) that are not natively supported by the JRE/JDK environment without the necessary third-party ImageIO plugins.
- **Incorrect Format String:** Passing an empty or null string as the `formatName`, which may lead to confusing error logs.
- **Misuse in Fluent API:** This exception is intended for low-level task management; it is rarely thrown directly by the high-level `Thumbnails` fluent API, which typically handles format validation via `ImageIO` internally.

### Fix Code Hint
```java
// Verify format support before initiating a task
if (java.util.Arrays.asList(javax.imageio.ImageIO.getWriterFormatNames()).contains(format)) {
    // Proceed with task
} else {
    throw new net.coobird.thumbnailator.tasks.UnsupportedFormatException(format, "Unsupported output format.");
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.UnsupportedFormatException.getFormatName`

### Signature
```java
():String
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/UnsupportedFormatException.java:80_

### Goal
Retrieves the name of the image format that caused an `UnsupportedFormatException` during a thumbnail generation task.

### Parameters
_None._

### Input
This method is called on an instance of `UnsupportedFormatException` that has been thrown by the library when an input source uses an image format not supported by the underlying Java Image I/O implementation. No external input is required for the method itself.

### Output
Returns `String` — The name of the unsupported image format (e.g., "webp" or "heif") that triggered the exception.

### Valid Call Patterns
```java
try {
    Thumbnails.of("input.unknown")
        .size(100, 100)
        .toFile("output.jpg");
} catch (UnsupportedFormatException e) {
    String format = e.getFormatName();
    System.out.println("Format not supported: " + format);
}
```

### LLM Instruction Prompt
- When handling exceptions in Thumbnailator pipelines, use `getFormatName()` on caught `UnsupportedFormatException` instances to identify the specific format that the current JRE environment cannot process.

### Prompt Snippet
```text
// Example of handling unsupported format exception
catch (UnsupportedFormatException e) {
    String unsupported = e.getFormatName();
    // Log or handle the specific unsupported format
}
```

### Common Failure Modes
- **Null Pointer Exception:** Calling this method on an object that is not an instance of `UnsupportedFormatException`.
- **Misinterpretation:** Assuming the returned string is a valid format for other systems; the string only represents the format that the current Java Image I/O environment failed to read or write.

### Fix Code Hint
```java
// Ensure the exception is caught specifically to access the format name
catch (UnsupportedFormatException e) {
    logger.error("Thumbnailator does not support the format: " + e.getFormatName());
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.AbstractImageSink.preferredOutputFormatName`

### Signature
```java
():String
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/AbstractImageSink.java:72_

### Goal
Retrieves the preferred output format name for the image sink, providing a fallback to the original format if no specific output format is determined.

### Parameters
_None._

### Input
The caller must ensure the `AbstractImageSink` instance has been properly initialized with the relevant task parameters. No arguments are required for this method call.

### Output
Returns `String` — The name of the preferred output format (e.g., "jpg", "png"). If the output format cannot be determined, it returns `ThumbnailParameter.ORIGINAL_FORMAT`.

### Valid Call Patterns
```java
// Inferred from API signature; assumes an instance of a concrete subclass of AbstractImageSink
String format = sink.preferredOutputFormatName();
```

### LLM Instruction Prompt
- When calling `preferredOutputFormatName()`, ensure the `AbstractImageSink` implementation has been configured with a `ThumbnailParameter` object, as the sink relies on this configuration to resolve the format. Do not assume a specific format string; always handle the potential return of `ThumbnailParameter.ORIGINAL_FORMAT`.

### Prompt Snippet
```text
String format = imageSink.preferredOutputFormatName();
if (format.equals(ThumbnailParameter.ORIGINAL_FORMAT)) {
    // Handle logic for preserving original format
}
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `AbstractImageSink` instance was not properly initialized or if the underlying `ThumbnailParameter` configuration is null.
- **Incorrect Format Assumption:** Assuming the method returns a specific file extension (like "jpg") without checking for `ThumbnailParameter.ORIGINAL_FORMAT`, which may lead to incorrect file naming or encoding logic.

### Fix Code Hint
```java
// Ensure the sink is initialized before querying the format
if (sink != null) {
    String format = sink.preferredOutputFormatName();
    // Use the format string for subsequent I/O operations
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.AbstractImageSink.setOutputFormatName`

### Signature
```java
(String format):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/AbstractImageSink.java:54_

### Goal
Sets the output format name for the image sink, which determines the encoding format used when writing the processed image to the destination.

### Parameters
- `format` (`String`): The file format name (e.g., "jpg", "png", "gif") to be used for storing the image.

### Input
The caller must provide a valid format string recognized by the Java Image I/O API. Supported formats can be queried via `ThumbnailatorUtils.getSupportedOutputFormats()`. For `ImageSink` implementations that store raw images, this parameter may be ignored.

### Output
Returns `void` — this method performs an internal state update on the `AbstractImageSink` instance.

### Valid Call Patterns
```java
// Example inferred from API signature and usage patterns
AbstractImageSink<?> sink = new FileImageSink(new File("output.jpg"));
sink.setOutputFormatName("jpg");
```

### LLM Instruction Prompt
- When using `setOutputFormatName`, ensure the provided string corresponds to a format supported by the underlying Java Image I/O environment.
- Note that this method is part of the `AbstractImageSink` class; it is typically used when configuring custom `ThumbnailTask` implementations rather than the fluent `Thumbnails` API.

### Prompt Snippet
```text
// Setting the output format for a custom ImageSink
sink.setOutputFormatName("png");
```

### Common Failure Modes
- Providing an unsupported format string, which may result in an `IIOException` or failure to write the image when the task is executed.
- Calling this method on an `ImageSink` that does not support format specification (e.g., raw image sinks), leading to the setting being ignored.
- Attempting to use this method on a `BufferedImageSink`, which does not require a format name as it returns an in-memory object.

### Fix Code Hint
```java
// Verify supported formats before setting
if (ThumbnailatorUtils.getSupportedOutputFormats().contains(format)) {
    sink.setOutputFormatName(format);
} else {
    throw new IllegalArgumentException("Unsupported format: " + format);
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.AbstractImageSink.setThumbnailParameter`

### Signature
```java
(ThumbnailParameter param):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/AbstractImageSink.java:58_

### Goal
Configures the `AbstractImageSink` with the necessary `ThumbnailParameter` settings required to govern how the processed image is written to the destination.

### Parameters
- `param` (`ThumbnailParameter`): A `ThumbnailParameter` object containing the configuration (such as output format, quality, or source region) to be applied during the image writing process.

### Input
The caller must provide a fully configured `ThumbnailParameter` instance. Note that `ThumbnailParameter` is not thread-safe and should not be reused across multiple tasks. The sink must be in a state ready to receive parameters before the write operation is initiated.

### Output
Returns `void` — This method performs an internal state update on the sink and does not return a value.

### Valid Call Patterns
```java
// Inferred from API signature:
AbstractImageSink sink = ...; // e.g., FileImageSink or OutputStreamImageSink
ThumbnailParameter param = new ThumbnailParameter();
// Configure param as needed
sink.setThumbnailParameter(param);
```

### LLM Instruction Prompt
- When calling `setThumbnailParameter`, ensure the `ThumbnailParameter` object is instantiated specifically for the current task. Do not share `ThumbnailParameter` instances between different `ImageSink` objects or concurrent threads, as the class is not thread-safe.

### Prompt Snippet
```text
// Example of setting parameters on an ImageSink
ThumbnailParameter param = new ThumbnailParameter();
param.setOutputFormat("jpg");
sink.setThumbnailParameter(param);
```

### Common Failure Modes
- **Thread Safety:** Attempting to use the same `ThumbnailParameter` instance across multiple threads or concurrent `ThumbnailTask` executions will lead to race conditions and unpredictable output.
- **Illegal State:** Calling this method after the image writing process has already commenced may result in the parameters being ignored or an `IllegalStateException` depending on the specific `ImageSink` implementation.
- **Null Pointer:** Passing a `null` parameter object will likely cause a `NullPointerException` when the sink attempts to access the configuration during the write phase.

### Fix Code Hint
```java
// Ensure a fresh parameter object is used for each sink
ThumbnailParameter param = new ThumbnailParameter();
// ... configure param ...
sink.setThumbnailParameter(param);
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.AbstractImageSink.write`

### Signature
```java
(BufferedImage img):void
```
_Source: source/src/main/java/net/coobird/thumbnailator.tasks.io.AbstractImageSink.java:62_

### Goal
Writes a processed `BufferedImage` to the destination defined by the specific `ImageSink` implementation.

### Parameters
- `img` (`BufferedImage`): The image data to be written to the destination (e.g., file, stream, or memory buffer).

### Input
- **Data:** A `BufferedImage` object, typically resulting from a transformation pipeline.
- **Preconditions:** The `AbstractImageSink` must be correctly initialized with a destination (e.g., a file path or output stream) before calling `write`.
- **Formats:** The output format is determined by the `ImageSink` configuration; ensure the `BufferedImage` type is compatible with the intended output format (e.g., avoid alpha channels when writing to JPEG if the destination does not support transparency).

### Output
Returns `void` — The method performs the side effect of persisting the image to the configured destination.

### Valid Call Patterns
```java
// Inferred from API structure:
// Assuming 'sink' is an instance of a concrete subclass of AbstractImageSink
sink.write(bufferedImage);
```

### LLM Instruction Prompt
- When using `AbstractImageSink.write`, ensure the `BufferedImage` has been fully processed by the Thumbnailator pipeline.
- Always handle `IOException` as this method performs I/O operations.
- Do not attempt to reuse the `ImageSink` instance for multiple writes if the implementation is stateful or tied to a specific output stream.

### Prompt Snippet
```text
// Example of writing a processed image to a sink
try {
    sink.write(processedImage);
} catch (IOException e) {
    // Handle I/O error during write
}
```

### Common Failure Modes
- **IOException:** Occurs if the destination is unreachable, the disk is full, or the output stream is closed.
- **IllegalStateException:** May occur if the sink has not been properly configured with a destination or output format before the write operation.
- **Color/Transparency Issues:** Writing a `BufferedImage` with an alpha channel to a format that does not support it (like JPEG) may result in unexpected color artifacts or errors depending on the underlying `ImageIO` writer.

### Fix Code Hint
```java
// Ensure the image is compatible with the output format before writing
if (outputFormat.equalsIgnoreCase("jpg") && img.getColorModel().hasAlpha()) {
    // Convert to RGB if necessary
    BufferedImage newImage = new BufferedImage(img.getWidth(), img.getHeight(), BufferedImage.TYPE_INT_RGB);
    Graphics2D g = newImage.createGraphics();
    g.drawImage(img, 0, 0, Color.WHITE, null);
    g.dispose();
    sink.write(newImage);
} else {
    sink.write(img);
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.AbstractImageSource.getInputFormatName`

### Signature
```java
():String
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/AbstractImageSource.java:80_

### Goal
Retrieves the name of the image format associated with the current image source, as determined by the underlying Java Image I/O provider.

### Parameters
_None._

### Input
The caller must ensure the `AbstractImageSource` implementation has been initialized with a valid image source (e.g., `File`, `InputStream`, `URL`, or `BufferedImage`). No specific preconditions are required to call this method, but the source must be capable of providing format metadata.

### Output
Returns `String` — The name of the image format (e.g., "jpg", "png", "gif"). If the image source does not contain format information or the format cannot be determined, the method returns `null`.

### Valid Call Patterns
```java
// Example inferred from the API signature and class hierarchy
String format = imageSource.getInputFormatName();
```

### LLM Instruction Prompt
- When generating code that uses `getInputFormatName`, ensure the `AbstractImageSource` instance is already instantiated.
- Do not assume the returned string is non-null; always include a null check before using the format name in logic or logging.
- Use this method only when you need to inspect the format of the source image; do not use it to set the output format of a thumbnail task.

### Prompt Snippet
```text
// Retrieve the format of the source image
String format = source.getInputFormatName();
if (format != null) {
    System.out.println("Processing image of type: " + format);
}
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `AbstractImageSource` instance itself is null.
- **Unexpected Null Return:** The method returns `null` if the source is an in-memory `BufferedImage` or if the input stream does not provide identifiable format metadata, which may cause issues if the caller expects a valid string.
- **Misuse for Output:** Developers often mistakenly use this to determine the output format; use `outputFormat(String)` on the `Thumbnails.Builder` instead.

### Fix Code Hint
```java
// Always handle the potential null return value
String format = source.getInputFormatName();
String safeFormat = (format != null) ? format : "unknown";
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.AbstractImageSource.setThumbnailParameter`

### Signature
```java
(ThumbnailParameter param):void
```
_Source: source/src/main/java/net/coobird/thumbnailator.tasks.io.AbstractImageSource.java:76_

### Goal
Configures the image source with specific `ThumbnailParameter` settings to control how the image is read and processed during the thumbnail generation task.

### Parameters
- `param` (`ThumbnailParameter`): The configuration object containing parameters such as source region, scaling factors, or Exif orientation settings to be applied when retrieving the image.

### Input
- **Data:** An instance of `ThumbnailParameter`.
- **Preconditions:** The `ThumbnailParameter` object must be fully configured before being passed to the source. Note that `ThumbnailParameter` is not thread-safe and should not be reused across multiple concurrent tasks.

### Output
Returns `void` — This method updates the internal state of the `AbstractImageSource` instance to apply the provided reading parameters.

### Valid Call Patterns
```java
// Inferred from API signature:
ThumbnailParameter param = new ThumbnailParameter();
// ... configure param ...
imageSource.setThumbnailParameter(param);
```

### LLM Instruction Prompt
- When generating code that uses this method, ensure the `ThumbnailParameter` is instantiated and configured prior to calling `setThumbnailParameter`. 
- Do not attempt to share a single `ThumbnailParameter` instance across multiple `AbstractImageSource` objects or concurrent threads, as it is not thread-safe.

### Prompt Snippet
```text
// Example of setting parameters on an image source
ThumbnailParameter param = new ThumbnailParameter();
param.setScale(0.5);
imageSource.setThumbnailParameter(param);
```

### Common Failure Modes
- **Thread Safety:** Attempting to use the same `ThumbnailParameter` instance across multiple threads will lead to race conditions and unpredictable behavior.
- **Illegal State:** Modifying the `ThumbnailParameter` object after it has been passed to the `setThumbnailParameter` method may result in inconsistent processing if the source has already begun reading the image.
- **Null Pointer:** Passing a `null` parameter will likely result in a `NullPointerException` when the source attempts to access the configuration during the read process.

### Fix Code Hint
```java
// Always create a fresh ThumbnailParameter for each task
ThumbnailParameter param = new ThumbnailParameter();
// Configure settings
param.setUseExifOrientation(true);
// Apply to source
imageSource.setThumbnailParameter(param);
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.BufferedImageSink.getSink`

### Signature
```java
():BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/BufferedImageSink.java:62_

### Goal
Retrieves the `BufferedImage` that has been processed and stored within a `BufferedImageSink` instance.

### Parameters
_None._

### Input
The caller must ensure that a thumbnail generation task has been successfully executed and written to the `BufferedImageSink` instance prior to calling this method. There are no input parameters for this method.

### Output
Returns `BufferedImage` — The resulting thumbnail image stored in the sink.

### Valid Call Patterns
```java
// Inferred from API documentation:
BufferedImageSink sink = new BufferedImageSink();
// ... perform thumbnail task using the sink ...
BufferedImage thumbnail = sink.getSink();
```

### LLM Instruction Prompt
- Use `getSink()` only after a `ThumbnailTask` has completed its write operation to the `BufferedImageSink`.
- Do not assume the sink contains an image if the task has not been executed or has failed.
- Ensure the `BufferedImageSink` instance is properly initialized before attempting to retrieve the result.

### Prompt Snippet
```text
// Retrieve the processed image from the sink
BufferedImage result = myBufferedImageSink.getSink();
```

### Common Failure Modes
- `IllegalStateException`: Thrown if the method is called before a thumbnail has been written to the `BufferedImageSink`.
- Null pointer exceptions if the `BufferedImageSink` instance itself is null.

### Fix Code Hint
```java
// Ensure the task is performed before retrieval
if (task.isCompleted()) {
    BufferedImage result = sink.getSink();
} else {
    // Handle the case where the thumbnail has not been generated yet
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.BufferedImageSink.setOutputFormatName`

### Signature
```java
(String format):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/BufferedImageSink.java:69_

### Goal
Sets the output format name for the `BufferedImageSink`, which informs the underlying image processing logic of the desired format for the resulting image.

### Parameters
- `format` (`String`): The name of the image format (e.g., "jpg", "png", "gif") to be used when storing the image.

### Input
The caller must provide a valid format string recognized by the Java Image I/O API. Supported formats can be queried via `ThumbnailatorUtils.getSupportedOutputFormats()`. Note that for `BufferedImageSink`, which stores raw images in memory, the format name specified may be ignored depending on the specific implementation context.

### Output
Returns `void` — the method updates the internal state of the `BufferedImageSink` instance to reflect the requested output format.

### Valid Call Patterns
```java
BufferedImageSink sink = new BufferedImageSink();
sink.setOutputFormatName("png");
```

### LLM Instruction Prompt
- When using `BufferedImageSink`, call `setOutputFormatName` to specify the target format string. Ensure the format string is a valid identifier supported by the Java Image I/O registry. If the sink is intended to store raw `BufferedImage` objects, be aware that this format setting may be ignored.

### Prompt Snippet
```text
BufferedImageSink sink = new BufferedImageSink();
sink.setOutputFormatName("jpg");
```

### Common Failure Modes
- Providing an unsupported format string that is not registered with the Java Image I/O API.
- Assuming the format name will be strictly enforced when the sink is used for in-memory `BufferedImage` storage, where the format is often irrelevant compared to file-based sinks.

### Fix Code Hint
```java
// Verify supported formats before setting
if (ThumbnailatorUtils.getSupportedOutputFormats().contains(format)) {
    sink.setOutputFormatName(format);
} else {
    throw new IllegalArgumentException("Unsupported format: " + format);
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.BufferedImageSink.write`

### Signature
```java
(BufferedImage img):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/BufferedImageSink.java:48_

### Goal
Writes a processed `BufferedImage` to an in-memory destination sink within the Thumbnailator task pipeline.

### Parameters
- `img` (`BufferedImage`): The image object resulting from a thumbnail transformation to be stored in the sink.

### Input
The caller must provide a valid `BufferedImage` instance. This method is typically invoked as the final step in a `SourceSinkThumbnailTask` workflow. Preconditions include ensuring the `BufferedImageSink` has been correctly initialized to receive the image data.

### Output
Returns `void` — The method performs the side effect of storing the provided `BufferedImage` into the sink's internal memory buffer.

### Valid Call Patterns
```java
// Inferred usage pattern based on API signature and task-based processing
BufferedImageSink sink = new BufferedImageSink();
sink.write(processedImage);
BufferedImage result = sink.getSink();
```

### LLM Instruction Prompt
- When using `BufferedImageSink.write`, ensure the `BufferedImage` has already undergone the desired transformations (resizing, filtering, etc.) via the Thumbnailator pipeline. This method is a terminal operation for the sink; do not attempt to chain further transformations after calling `write`.

### Prompt Snippet
```text
// Example of writing a processed image to a BufferedImageSink
BufferedImageSink sink = new BufferedImageSink();
sink.write(myProcessedImage);
```

### Common Failure Modes
- `IOException`: Thrown if an error occurs during the writing or storage process.
- `NullPointerException`: Occurs if the `img` parameter passed to the method is `null`.
- `IllegalStateException`: May occur if the sink is accessed or written to in an invalid state relative to the task lifecycle.

### Fix Code Hint
```java
try {
    sink.write(processedImage);
} catch (IOException e) {
    // Handle storage failure, e.g., log error or rethrow as a runtime exception
    throw new RuntimeException("Failed to write image to sink", e);
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.BufferedImageSource`

### Signature
```java
(BufferedImage img):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/BufferedImageSource.java:54_

### Goal
Instantiates a `BufferedImageSource` to provide an in-memory `BufferedImage` as the source for a thumbnail generation task.

### Parameters
- `img` (`BufferedImage`): The source image to be processed; must not be null.

### Input
- **Data:** A valid `java.awt.image.BufferedImage` object.
- **Preconditions:** The `BufferedImage` must be fully initialized and non-null. The caller is responsible for ensuring the image is in a format compatible with the subsequent processing pipeline.

### Output
Returns `null` — The constructor initializes the internal state of the `BufferedImageSource` object, which is then used by the Thumbnailator task system to access the image data.

### Valid Call Patterns
```java
// Inferred from API signature and constructor documentation
BufferedImage sourceImage = ImageIO.read(new File("input.jpg"));
net.coobird.thumbnailator.tasks.io.BufferedImageSource source = 
    new net.coobird.thumbnailator.tasks.io.BufferedImageSource(sourceImage);
```

### LLM Instruction Prompt
- When using `BufferedImageSource`, ensure the `BufferedImage` passed to the constructor is not null.
- Use this class when the image data is already loaded into memory as a `BufferedImage` rather than reading from a file, stream, or URL.
- This class is intended for use within the `ThumbnailTask` framework; it is not typically used directly when employing the fluent `Thumbnails.of(...)` API.

### Prompt Snippet
```text
// Example of initializing a BufferedImageSource
BufferedImage myImage = ...; // Loaded image
net.coobird.thumbnailator.tasks.io.BufferedImageSource source = 
    new net.coobird.thumbnailator.tasks.io.BufferedImageSource(myImage);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `img` argument passed to the constructor is `null`.
- **Memory Constraints:** Passing very large `BufferedImage` objects can lead to `OutOfMemoryError` in memory-constrained environments; consider using `Thumbnailator`'s memory-saving workarounds if processing large images.

### Fix Code Hint
```java
// Ensure the image is not null before instantiation
if (img != null) {
    net.coobird.thumbnailator.tasks.io.BufferedImageSource source = 
        new net.coobird.thumbnailator.tasks.io.BufferedImageSource(img);
} else {
    throw new IllegalArgumentException("Source image cannot be null.");
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.BufferedImageSource.getSource`

### Signature
```java
():BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/BufferedImageSource.java:79_

### Goal
Retrieves the underlying `BufferedImage` instance currently held by the `BufferedImageSource` task for subsequent processing.

### Parameters
_None._

### Input
The `BufferedImageSource` must have been initialized with a valid `BufferedImage` object. No specific file format constraints apply at this stage as the image is already loaded into memory.

### Output
Returns `BufferedImage` — The in-memory image object that serves as the source for the thumbnail generation task.

### Valid Call Patterns
```java
// Example inferred from signature and class purpose
BufferedImageSource source = new BufferedImageSource(myBufferedImage);
BufferedImage img = source.getSource();
```

### LLM Instruction Prompt
- When using `getSource()`, ensure the `BufferedImageSource` instance has been correctly instantiated with a non-null `BufferedImage`.
- This method is intended for retrieving the source image within a `ThumbnailTask` workflow; do not use it to modify the source image directly if the task is currently executing.

### Prompt Snippet
```text
// Retrieve the source image from a BufferedImageSource instance
BufferedImage sourceImage = bufferedImageSource.getSource();
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `BufferedImageSource` was initialized with a null image or if the source has been cleared.
- **IllegalStateException**: May occur if the source is accessed in a multi-threaded context where the task state is being modified concurrently, as `ThumbnailParameter` and associated task objects are not thread-safe.

### Fix Code Hint
```java
// Ensure the source is initialized before retrieval
if (bufferedImageSource != null) {
    BufferedImage img = bufferedImageSource.getSource();
    // Proceed with processing
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.BufferedImageSource.read`

### Signature
```java
():BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/BufferedImageSource.java:64_

### Goal
Retrieves the `BufferedImage` instance currently held by the `BufferedImageSource` for subsequent processing within a thumbnail generation task.

### Parameters
_None._

### Input
The caller must ensure the `BufferedImageSource` has been correctly initialized with a valid `BufferedImage` object. No specific file format constraints apply at this stage as the image is already loaded into memory.

### Output
Returns `BufferedImage` — The image object stored within the source, ready for transformation or filtering.

### Valid Call Patterns
```java
// Inferred from API signature:
BufferedImageSource source = new BufferedImageSource(myBufferedImage);
BufferedImage img = source.read();
```

### LLM Instruction Prompt
- Use this method only when you have an existing `BufferedImageSource` instance.
- Do not use this method to load images from files or URLs; use `FileImageSource` or `URLImageSource` for those purposes.
- Always handle the `IOException` that may be thrown during the read operation.

### Prompt Snippet
```text
BufferedImageSource source = new BufferedImageSource(inputImage);
try {
    BufferedImage img = source.read();
    // Proceed with processing
} catch (IOException e) {
    // Handle read error
}
```

### Common Failure Modes
- **Null Source:** Attempting to call `read()` on a `BufferedImageSource` that was initialized with a `null` image will result in a `NullPointerException`.
- **IOException:** While the source is in-memory, the `read()` method signature declares `IOException`, which must be caught or declared by the caller.
- **State Misuse:** This method is intended for task-based processing; avoid using it if you are already using the fluent `Thumbnails` API, which manages source reading internally.

### Fix Code Hint
```java
// Ensure the BufferedImage is not null before passing to the source
if (myImage != null) {
    BufferedImageSource source = new BufferedImageSource(myImage);
    BufferedImage result = source.read();
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.FileImageSink`

### Signature
```java
(File destinationFile,boolean allowOverwrite):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/FileImageSink.java:127_

### Goal
`FileImageSink` acts as a destination handler that writes processed `BufferedImage` data to a specified file on the filesystem.

### Parameters
- `destinationFile` (`File`): The target file object where the thumbnail will be saved.
- `allowOverwrite` (`boolean`): A flag determining whether the sink is permitted to replace an existing file at the destination path.

### Input
The caller must provide a valid `File` object representing the output path. The output format is inferred from the file extension by default; if a specific format is required, `setOutputFormatName(String)` must be called on the instance. Precondition: The application must have write permissions for the target directory.

### Output
Returns `null` — The operation performs a side-effect (writing to the filesystem) rather than returning a value.

### Valid Call Patterns
```java
// Inferred from API signature and constructor documentation
File destination = new File("path/to/thumbnail.jpg");
boolean allowOverwrite = true;
net.coobird.thumbnailator.tasks.io.FileImageSink sink = 
    new net.coobird.thumbnailator.tasks.io.FileImageSink(destination, allowOverwrite);
```

### LLM Instruction Prompt
- When using `FileImageSink`, ensure the `destinationFile` is not `null` to avoid a `NullPointerException`.
- If the output format does not match the file extension, explicitly invoke `setOutputFormatName(String)` after instantiation.
- Use this class when you need granular control over file-based output outside of the standard fluent `Thumbnails` pipeline.

### Prompt Snippet
```text
net.coobird.thumbnailator.tasks.io.FileImageSink sink = new net.coobird.thumbnailator.tasks.io.FileImageSink(file, true);
```

### Common Failure Modes
- `NullPointerException`: Occurs if the `destinationFile` argument is `null`.
- `IOException`: May occur if the destination directory is read-only, the disk is full, or the file is locked by another process.
- `IllegalStateException`: Occurs if an attempt is made to overwrite a file when `allowOverwrite` is set to `false` and the file already exists.

### Fix Code Hint
```java
// Ensure the file object is initialized and permissions are verified
File dest = new File("output.jpg");
if (dest.getParentFile() != null) {
    dest.getParentFile().mkdirs();
}
net.coobird.thumbnailator.tasks.io.FileImageSink sink = 
    new net.coobird.thumbnailator.tasks.io.FileImageSink(dest, true);
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.FileImageSink.getSink`

### Signature
```java
():File
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/FileImageSink.java:340_

### Goal
Retrieves the destination file associated with a `FileImageSink`, reflecting any modifications made to the file path during the thumbnail writing process.

### Parameters
_None._

### Input
The `FileImageSink` instance must be initialized with a target `File` object. No additional parameters are required to invoke this method.

### Output
Returns `File` — The destination file where the thumbnail is written. Note that this may differ from the initially provided file if the sink automatically appended a file extension to match the output format.

### Valid Call Patterns
```java
FileImageSink sink = new FileImageSink(new File("output.jpg"));
File destination = sink.getSink();
```
*(Note: This call pattern is inferred from the API signature and Javadoc documentation.)*

### LLM Instruction Prompt
- Use `getSink()` only on an initialized `FileImageSink` object.
- Do not assume the returned `File` object is identical to the one passed to the constructor, as the sink may have updated the path (e.g., appending extensions).
- Ensure the `FileImageSink` has been used in a task context if you expect the path to reflect format-specific adjustments.

### Prompt Snippet
```text
// Example of retrieving the sink file from a FileImageSink instance
FileImageSink sink = new FileImageSink(targetFile);
File actualFile = sink.getSink();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `FileImageSink` instance is null.
- **Stale Path:** Calling `getSink()` before the thumbnail task has completed may return the original path before any automatic extension adjustments have been applied by the sink.

### Fix Code Hint
```java
// Ensure the sink is fully processed before relying on the returned file path
FileImageSink sink = new FileImageSink(new File("output"));
// ... perform thumbnail task ...
File finalPath = sink.getSink(); 
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.FileImageSink.preferredOutputFormatName`

### Signature
```java
():String
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/FileImageSink.java:239_

### Goal
Retrieves the preferred output image format name for a `FileImageSink` based on the destination file's characteristics.

### Parameters
_None._

### Input
The `FileImageSink` must be initialized with a valid destination `File` object. The method relies on the file extension or internal configuration of the sink to determine the format.

### Output
Returns `String` — The name of the preferred output format (e.g., "jpg", "png"). If the format cannot be determined from the destination file, it returns `ThumbnailParameter.ORIGINAL_FORMAT`.

### Valid Call Patterns
```java
// Inferred from API signature; assumes an existing FileImageSink instance
String format = fileImageSink.preferredOutputFormatName();
```

### LLM Instruction Prompt
- When calling `preferredOutputFormatName()`, ensure the `FileImageSink` has been correctly instantiated with a target file path, as the return value is derived from the sink's destination context. Do not assume a specific format string; always handle the potential return of `ThumbnailParameter.ORIGINAL_FORMAT`.

### Prompt Snippet
```text
// Example usage of preferredOutputFormatName
FileImageSink sink = new FileImageSink(new File("output.jpg"));
String format = sink.preferredOutputFormatName();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `FileImageSink` was not properly initialized or if the underlying file reference is null.
- **Indeterminate Format:** If the file path lacks an extension or uses an unrecognized format, the method returns `ThumbnailParameter.ORIGINAL_FORMAT`, which may cause downstream `ImageIO` operations to fail if the original format is not supported for writing.

### Fix Code Hint
```java
// Ensure the sink is initialized with a file that has a valid extension
FileImageSink sink = new FileImageSink(new File("image.png"));
String format = sink.preferredOutputFormatName();

if (ThumbnailParameter.ORIGINAL_FORMAT.equals(format)) {
    // Handle case where format could not be inferred
    format = "jpg"; // Fallback to a default
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.FileImageSink.setOutputFormatName`

### Signature
```java
(String format):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/FileImageSink.java:344_

### Goal
Sets the output format for the image being written to the filesystem by the `FileImageSink`.

### Parameters
- `format` (`String`): The file format name (e.g., "jpg", "png", "gif") to be used when storing the image.

### Input
The caller must provide a `String` representing a format name supported by the underlying Java Image I/O API. Supported formats can be verified by calling `javax.imageio.ImageIO.getWriterFormatNames()`. Note that for `FileImageSink` instances storing raw images, this format name may be ignored.

### Output
Returns `void` — this method performs an internal state update on the `FileImageSink` instance.

### Valid Call Patterns
```java
FileImageSink sink = new FileImageSink(new File("output.jpg"));
sink.setOutputFormatName("jpg");
```

### LLM Instruction Prompt
- When using `FileImageSink` directly, ensure the `format` string matches a valid writer format supported by the JRE.
- Do not assume this method will override the file extension if the sink is initialized with a specific file path; it is intended to inform the `ImageWriter` of the desired encoding format.

### Prompt Snippet
```text
// Example of setting output format on a FileImageSink
FileImageSink sink = new FileImageSink(new File("path/to/output"));
sink.setOutputFormatName("png");
```

### Common Failure Modes
- **Unsupported Format:** Passing a format string not supported by the current JRE's `ImageIO` providers will result in an `IllegalArgumentException` or failure during the write operation.
- **Ignored Setting:** If the `FileImageSink` is configured to store raw data, the format name may be ignored, leading to unexpected file types if the user expects a specific conversion.
- **Null Argument:** Passing `null` as the format name may lead to a `NullPointerException` during the subsequent write process.

### Fix Code Hint
```java
// Verify format support before setting
String format = "jpg";
if (Arrays.asList(ImageIO.getWriterFormatNames()).contains(format)) {
    sink.setOutputFormatName(format);
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.FileImageSink.setThumbnailParameter`

### Signature
```java
(ThumbnailParameter param):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/FileImageSink.java:349_

### Goal
Configures the `FileImageSink` with specific `ThumbnailParameter` settings, such as output format or quality, to be applied when writing the thumbnail to the filesystem.

### Parameters
- `param` (`ThumbnailParameter`): A `ThumbnailParameter` object containing the configuration (e.g., output format, quality, or scaling settings) to be used during the image writing process.

### Input
The caller must provide a valid, non-null `ThumbnailParameter` instance. The `FileImageSink` must have been initialized with a target file destination. Note that `ThumbnailParameter` is not thread-safe and should not be reused across multiple tasks.

### Output
Returns `void` — the method updates the internal state of the `FileImageSink` to reflect the provided parameters.

### Valid Call Patterns
```java
FileImageSink sink = new FileImageSink(new File("output.jpg"));
ThumbnailParameter param = new ThumbnailParameter(
    new Dimension(200, 200), 
    null, 
    true, 
    "jpg", 
    0.85f, 
    ThumbnailParameter.DEFAULT_THUMBNAIL_FORMAT, 
    null, 
    null
);
sink.setThumbnailParameter(param);
```

### LLM Instruction Prompt
- When calling `setThumbnailParameter`, ensure the `ThumbnailParameter` object is fully configured before passing it to the sink.
- Do not reuse the same `ThumbnailParameter` instance across multiple `FileImageSink` instances or tasks, as the class is not thread-safe.
- Ensure the `FileImageSink` is properly instantiated with a destination file before invoking this method.

### Prompt Snippet
```text
// Example of setting parameters on a FileImageSink
FileImageSink sink = new FileImageSink(new File("destination.png"));
ThumbnailParameter params = new ThumbnailParameterBuilder().size(100, 100).build();
sink.setThumbnailParameter(params);
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `param` argument passed is `null`.
- **IllegalStateException**: May occur if the sink is in an invalid state or if parameters are incompatible with the target file format.
- **Thread Safety Issues**: Reusing a single `ThumbnailParameter` instance across concurrent tasks can lead to non-deterministic behavior or race conditions.

### Fix Code Hint
```java
// Always create a fresh ThumbnailParameter for each task
ThumbnailParameter param = new ThumbnailParameterBuilder()
    .size(width, height)
    .outputFormat("jpg")
    .outputQuality(0.9)
    .build();
sink.setThumbnailParameter(param);
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.FileImageSink.write`

### Signature
```java
(BufferedImage img):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/FileImageSink.java:275_

### Goal
Writes a processed `BufferedImage` to a specified file on the filesystem using the configured output format and settings.

### Parameters
- `img` (`BufferedImage`): The image data to be written to the destination file.

### Input
- **Data:** A valid `BufferedImage` object.
- **Preconditions:** The `FileImageSink` must be initialized with a valid destination file path. If the output format is not explicitly set via `setOutputFormatName(String)`, it must be determinable from the destination file's extension.
- **Formats:** Supports any image format recognized by the underlying Java Image I/O API.

### Output
Returns `void` — The method performs a side-effect operation by persisting the image to the disk.

### Valid Call Patterns
```java
FileImageSink sink = new FileImageSink(new File("output.jpg"));
sink.write(bufferedImage);
```

### LLM Instruction Prompt
- When using `FileImageSink.write`, ensure the `BufferedImage` is not null. 
- If the output format is ambiguous, explicitly call `setOutputFormatName(String)` on the `FileImageSink` instance before invoking `write`.
- Verify that the destination directory exists and the application has write permissions.

### Prompt Snippet
```text
FileImageSink sink = new FileImageSink(new File(outputPath));
sink.write(bufferedImage);
```

### Common Failure Modes
- `NullPointerException`: Thrown if the provided `BufferedImage` is null.
- `UnsupportedFormatException`: Thrown if the output format is not supported or cannot be inferred from the filename.
- `IOException`: Thrown if an error occurs during file I/O (e.g., disk full, permission denied).
- `IllegalArgumentException`: Thrown if the destination file already exists and the sink is configured to disallow overwriting.

### Fix Code Hint
```java
// Ensure the file exists and handle potential exceptions
try {
    sink.write(img);
} catch (IOException e) {
    // Handle I/O error (e.g., log or retry)
} catch (IllegalArgumentException e) {
    // Handle overwrite restriction
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.FileImageSource`

### Signature
```java
(File sourceFile):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/FileImageSource.java:86_

### Goal
Instantiates a `FileImageSource` to serve as an input provider for thumbnail generation tasks, reading image data directly from a specified filesystem `File`.

### Parameters
- `sourceFile` (`File`): The `File` object representing the source image to be processed.

### Input
- **Data:** A valid `java.io.File` pointing to an image file.
- **Formats:** Supports any image format recognized by the underlying Java Image I/O API.
- **Preconditions:** The file must exist and be readable by the application. The `File` object must not be `null`.

### Output
Returns `null` — The constructor initializes the internal state of the `FileImageSource` to prepare the file for subsequent reading by a `ThumbnailTask`.

### Valid Call Patterns
```java
File source = new File("path/to/image.jpg");
FileImageSource imageSource = new FileImageSource(source);
```

### LLM Instruction Prompt
- Use `new FileImageSource(File)` to wrap a file for use in a `ThumbnailTask`.
- Ensure the `File` object is non-null before instantiation to avoid `NullPointerException`.
- This class is intended for task-based processing; for fluent pipeline usage, prefer `Thumbnails.of(File)`.

### Prompt Snippet
```text
// Example of initializing a FileImageSource
File sourceFile = new File("input.png");
FileImageSource source = new FileImageSource(sourceFile);
```

### Common Failure Modes
- **NullPointerException:** Thrown if the `sourceFile` argument is `null`.
- **FileNotFoundException / Access Errors:** Occur during the actual processing phase if the file does not exist or the application lacks read permissions.
- **Unsupported Format:** If the file format is not supported by the JRE's Image I/O, the task will fail during the read operation.

### Fix Code Hint
```java
// Ensure the file is validated before passing to the constructor
File file = new File("path/to/image.jpg");
if (file.exists() && file.isFile()) {
    FileImageSource source = new FileImageSource(file);
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.FileImageSource.getInputFormatName`

### Signature
```java
():String
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/FileImageSource.java:149_

### Goal
Retrieves the image format name (e.g., "jpg", "png") associated with the source file currently held by the `FileImageSource` instance.

### Parameters
_None._

### Input
The `FileImageSource` must be initialized with a valid `File` object. The format name is derived from the image file's metadata or content as recognized by the underlying Java Image I/O API.

### Output
Returns `String` — The name of the image format, or `null` if the format information cannot be determined or is unavailable.

### Valid Call Patterns
```java
FileImageSource source = new FileImageSource(new File("path/to/image.jpg"));
String format = source.getInputFormatName();
```
*(Note: This call pattern is inferred from the API signature and interface definition as no direct test-suite example was provided.)*

### LLM Instruction Prompt
- When calling `getInputFormatName`, ensure the `FileImageSource` has been correctly instantiated with a path to an existing image file.
- Do not assume the returned string is always non-null; always handle the potential `null` return value if the image format is unrecognized by the JRE's Image I/O providers.

### Prompt Snippet
```text
FileImageSource source = new FileImageSource(new File("image.png"));
String format = source.getInputFormatName();
```

### Common Failure Modes
- **Null Return:** If the file is not a valid image or the format is not supported by the installed Java Image I/O providers, the method returns `null`.
- **Uninitialized Source:** Calling this method on a `FileImageSource` that has not been properly pointed to a file may result in unexpected behavior or `null` returns.
- **Environment Dependency:** The accuracy of the returned format name depends entirely on the underlying JRE's `ImageIO` capabilities; formats like WebP or HEIF may return `null` if the environment lacks the necessary SPI plugins.

### Fix Code Hint
```java
FileImageSource source = new FileImageSource(file);
String format = source.getInputFormatName();
if (format == null) {
    // Handle unknown or unsupported format
    format = "unknown";
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.FileImageSource.getSource`

### Signature
```java
():File
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/FileImageSource.java:145_

### Goal
Retrieves the `File` object currently configured as the source for a `FileImageSource` task.

### Parameters
_None._

### Input
The caller must have previously instantiated a `FileImageSource` object with a valid `File` reference. No specific file format validation is performed by this getter, but the underlying `File` must exist and be accessible to the Java process for subsequent read operations to succeed.

### Output
Returns `File` — The `File` object representing the source image file associated with the `FileImageSource` instance.

### Valid Call Patterns
```java
File sourceFile = new File("path/to/image.jpg");
FileImageSource source = new FileImageSource(sourceFile);
File retrievedFile = source.getSource();
```

### LLM Instruction Prompt
- Use `java:net.coobird.thumbnailator.tasks.io.FileImageSource.getSource` only when you need to inspect the source file path of an existing `FileImageSource` instance.
- Do not assume the returned `File` is readable or exists; verify the file state if necessary before performing I/O operations.
- This method is part of the `net.coobird.thumbnailator.tasks.io` package and is intended for task-based processing workflows.

### Prompt Snippet
```text
// Example of retrieving the source file from a FileImageSource instance
FileImageSource source = new FileImageSource(new File("input.jpg"));
File file = source.getSource();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `FileImageSource` instance was initialized with a null `File` reference.
- **Logic Errors:** Calling this method does not trigger the image reading process; it only returns the reference. If the source file is moved or deleted after the `FileImageSource` is created but before `getSource()` is called, the returned `File` object will point to a non-existent location.

### Fix Code Hint
```java
// Ensure the FileImageSource is initialized with a non-null File
File file = new File("image.jpg");
if (file.exists()) {
    FileImageSource source = new FileImageSource(file);
    File retrieved = source.getSource();
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.FileImageSource.read`

### Signature
```java
():BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/FileImageSource.java:113_

### Goal
Retrieves the image from a `File` source as a `BufferedImage` for subsequent processing within the Thumbnailator pipeline.

### Parameters
_None._

### Input
The `FileImageSource` must be initialized with a valid `File` object pointing to an image file. The underlying Java Image I/O API must support the format of the file (e.g., JPEG, PNG, GIF). As per project constraints, this method only processes the first image in a multi-image file; subsequent images are ignored.

### Output
Returns `BufferedImage` — The image data loaded from the file, ready for transformation or filtering.

### Valid Call Patterns
```java
FileImageSource source = new FileImageSource(new File("path/to/image.jpg"));
BufferedImage image = source.read();
```

### LLM Instruction Prompt
- Use `java:net.coobird.thumbnailator.tasks.io.FileImageSource.read` only when you have an instantiated `FileImageSource` object.
- Ensure the `File` provided to the constructor exists and is readable.
- Wrap the call in a `try-catch` block to handle `IOException` as the method signature declares it.
- Do not use this method for multi-page files if you require access to pages beyond the first one.

### Prompt Snippet
```text
// Example of reading an image from a file source
FileImageSource source = new FileImageSource(new File("input.jpg"));
try {
    BufferedImage img = source.read();
    // Proceed with processing
} catch (IOException e) {
    // Handle read error
}
```

### Common Failure Modes
- **`IOException`**: Thrown if the file does not exist, is inaccessible, or is not a valid image format recognized by the Java Image I/O API.
- **`NullPointerException`**: Occurs if the `File` object passed to the `FileImageSource` constructor was null.
- **Memory Constraints**: Loading very large images into memory via `read()` may trigger an `OutOfMemoryError`; consider using Thumbnailator's fluent API or memory-efficient alternatives if processing large batches.

### Fix Code Hint
```java
// Ensure the file exists before attempting to read
File file = new File("path/to/image.jpg");
if (file.exists()) {
    FileImageSource source = new FileImageSource(file);
    BufferedImage img = source.read();
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.FileImageSource.setThumbnailParameter`

### Signature
```java
(ThumbnailParameter param):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/FileImageSource.java:153_

### Goal
Configures the `FileImageSource` with specific `ThumbnailParameter` settings to control how the source image is read and processed during a thumbnail task.

### Parameters
- `param` (`ThumbnailParameter`): A `ThumbnailParameter` object containing the configuration (such as source region, scaling, or Exif orientation settings) to be applied when retrieving the image from the file.

### Input
The caller must provide a valid, non-null `ThumbnailParameter` instance. The `FileImageSource` must have been initialized with a valid `File` object. Note that `ThumbnailParameter` is not thread-safe and should not be shared across multiple tasks.

### Output
Returns `void` — the method updates the internal state of the `FileImageSource` instance to reflect the provided parameters.

### Valid Call Patterns
```java
FileImageSource source = new FileImageSource(new File("input.jpg"));
ThumbnailParameter param = new ThumbnailParameter(
    new Dimension(200, 200), 
    null, 
    true, 
    "jpg", 
    0.85f, 
    Resizers.BILINEAR, 
    false, 
    null
);
source.setThumbnailParameter(param);
```
*(Note: This call pattern is inferred from the API signature and class usage.)*

### LLM Instruction Prompt
- When generating code that uses `setThumbnailParameter`, ensure the `ThumbnailParameter` object is instantiated specifically for the current task, as it is not thread-safe. Always ensure the `FileImageSource` is properly initialized with a file path before calling this method.

### Prompt Snippet
```text
// Ensure ThumbnailParameter is configured before passing to FileImageSource
ThumbnailParameter param = new ThumbnailParameter(...);
fileImageSource.setThumbnailParameter(param);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `param` argument passed is `null`.
- **IllegalStateException:** May occur if the `ThumbnailParameter` is reused across multiple `ThumbnailTask` instances, as the parameter object is mutable and not thread-safe.
- **Invalid Configuration:** Providing a `ThumbnailParameter` with incompatible settings (e.g., invalid dimensions or unsupported output formats) may lead to errors during the subsequent image reading or processing phase.

### Fix Code Hint
```java
// Always create a fresh ThumbnailParameter for each task
ThumbnailParameter param = new ThumbnailParameterBuilder()
    .size(200, 200)
    .build();
fileImageSource.setThumbnailParameter(param);
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.InputStreamImageSource`

### Signature
```java
(InputStream is):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/InputStreamImageSource.java:78_

### Goal
Instantiates an image source that reads image data from a provided `InputStream` for use in Thumbnailator processing tasks.

### Parameters
- `is` (`InputStream`): The `InputStream` containing the raw image data to be processed.

### Input
- **Data:** A valid `InputStream` representing an image file.
- **Formats:** Supports any image format recognized by the underlying Java Image I/O API.
- **Preconditions:** The `InputStream` must not be `null`. The caller is responsible for opening the stream; the library will read the image data from it.

### Output
Returns `null` — The constructor initializes the internal state of the `InputStreamImageSource` object, which is then used by `ThumbnailTask` implementations to retrieve the source image.

### Valid Call Patterns
```java
// Inferred from API signature and usage patterns
InputStream is = new FileInputStream("path/to/image.jpg");
InputStreamImageSource source = new net.coobird.thumbnailator.tasks.io.InputStreamImageSource(is);
```

### LLM Instruction Prompt
- When using `InputStreamImageSource`, ensure the `InputStream` is initialized and points to a valid image format supported by the JRE's Image I/O.
- This class is intended for task-based processing; for fluent pipeline usage, prefer `Thumbnails.fromInputStream(InputStream)`.
- Always handle potential `IOException`s when working with `InputStream` objects.

### Prompt Snippet
```text
net.coobird.thumbnailator.tasks.io.InputStreamImageSource source = new net.coobird.thumbnailator.tasks.io.InputStreamImageSource(inputStream);
```

### Common Failure Modes
- **NullPointerException:** Occurs if the provided `InputStream` is `null`.
- **IOException:** Occurs if the stream is closed, inaccessible, or contains data that is not a valid image format recognized by the Java Image I/O API.
- **Resource Leak:** Failing to close the `InputStream` after the thumbnail task is complete can lead to resource exhaustion.

### Fix Code Hint
```java
try (InputStream is = new FileInputStream("image.jpg")) {
    InputStreamImageSource source = new net.coobird.thumbnailator.tasks.io.InputStreamImageSource(is);
    // Proceed with task execution
} catch (IOException e) {
    // Handle stream access errors
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.InputStreamImageSource.getSource`

### Signature
```java
():InputStream
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/InputStreamImageSource.java:642_

### Goal
Retrieves the underlying `InputStream` from which the image data is read within an `InputStreamImageSource` task.

### Parameters
_None._

### Input
The `InputStreamImageSource` must have been initialized with a valid `InputStream` containing image data compatible with the Java Image I/O API. No specific preconditions are required for the call itself, but the source stream must be open and readable.

### Output
Returns `InputStream` — The raw input stream containing the image data associated with this source.

### Valid Call Patterns
```java
// Inferred from API signature:
InputStream stream = inputStreamImageSource.getSource();
```

### LLM Instruction Prompt
- When using `InputStreamImageSource`, call `getSource()` only when you need direct access to the raw byte stream of the image.
- Ensure the `InputStream` is not closed by external processes before this method is invoked, as the library relies on the stream to perform thumbnail generation.
- This method is part of the task-based processing workflow; prefer the fluent `Thumbnails` API for standard resizing tasks.

### Prompt Snippet
```text
// Accessing the raw stream from an existing InputStreamImageSource instance
InputStream sourceStream = myInputStreamImageSource.getSource();
```

### Common Failure Modes
- **IOException:** If the underlying stream has been closed or is inaccessible, calling `getSource()` or subsequent read operations may throw an `IOException`.
- **NullPointerException:** If the `InputStreamImageSource` was instantiated with a null stream, calling this method will return null, leading to potential errors in downstream processing.
- **Stream Exhaustion:** If the stream has already been fully consumed by a previous `ThumbnailTask`, subsequent attempts to read from the returned `InputStream` may yield no data.

### Fix Code Hint
```java
// Ensure the stream is valid before retrieval
if (inputStreamImageSource != null) {
    InputStream is = inputStreamImageSource.getSource();
    // Process the stream safely
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.InputStreamImageSource.read`

### Signature
```java
():BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/InputStreamImageSource.java:432_

### Goal
Retrieves and decodes an image from an `InputStream` source into a `BufferedImage` for subsequent processing within the Thumbnailator pipeline.

### Parameters
_None._

### Input
The caller must provide an `InputStream` to the `InputStreamImageSource` constructor prior to calling `read()`. The input stream must contain valid image data in a format supported by the underlying Java Image I/O API. As per project constraints, this method processes only the first image found in the stream; any subsequent images in multi-image files are ignored.

### Output
Returns `BufferedImage` — The decoded image object. If the source stream is empty or contains non-image data, the behavior follows standard Java Image I/O exceptions.

### Valid Call Patterns
```java
InputStream is = new FileInputStream("image.jpg");
InputStreamImageSource source = new InputStreamImageSource(is);
BufferedImage img = source.read();
```
*(Note: This call pattern is inferred from the API signature and standard Thumbnailator task-based processing workflows.)*

### LLM Instruction Prompt
- When using `InputStreamImageSource.read()`, ensure the `InputStream` is initialized and valid.
- Always wrap the call in a try-catch block to handle `IOException`.
- Do not attempt to reuse the `InputStreamImageSource` instance for multiple reads; it is designed for a single task-based operation.

### Prompt Snippet
```text
InputStreamImageSource source = new InputStreamImageSource(inputStream);
BufferedImage image = source.read();
```

### Common Failure Modes
- `IOException`: Occurs if the stream is closed, inaccessible, or contains corrupted/unsupported image data.
- `NullPointerException`: Occurs if the `InputStream` provided to the constructor was null.
- Multi-image limitation: If the input stream points to a multi-frame file (e.g., animated GIF or multi-page TIFF), only the first frame is returned, which may lead to unexpected results if the user expects the entire sequence.

### Fix Code Hint
```java
try {
    BufferedImage img = source.read();
    // Proceed with processing
} catch (IOException e) {
    // Handle read failure (e.g., log error or close stream)
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.InputStreamImageSource.setThumbnailParameter`

### Signature
```java
(ThumbnailParameter param):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/InputStreamImageSource.java:92_

### Goal
Configures the `InputStreamImageSource` with specific `ThumbnailParameter` settings required for reading an image from an `InputStream`.

### Parameters
- `param` (`ThumbnailParameter`): The configuration object containing parameters (such as source region or image type hints) to be applied during the image reading process.

### Input
The caller must provide a valid `ThumbnailParameter` instance. The `InputStreamImageSource` must have been initialized with a valid `InputStream`. Note that `ThumbnailParameter` is not thread-safe and should not be reused across multiple tasks.

### Output
Returns `void` — this method updates the internal state of the `InputStreamImageSource` to prepare for the subsequent image reading operation.

### Valid Call Patterns
```java
InputStreamImageSource source = new InputStreamImageSource(inputStream);
ThumbnailParameter param = new ThumbnailParameter();
// Configure param as needed
source.setThumbnailParameter(param);
```
*(Note: This call pattern is inferred from the API signature and class usage.)*

### LLM Instruction Prompt
- When using `setThumbnailParameter`, ensure the `ThumbnailParameter` object is freshly instantiated or uniquely configured for the specific `InputStreamImageSource` instance, as the parameter object is mutable and not thread-safe.

### Prompt Snippet
```text
// Example of setting parameters for an InputStream source
ThumbnailParameter param = new ThumbnailParameter();
// ... configure param ...
inputStreamImageSource.setThumbnailParameter(param);
```

### Common Failure Modes
- **Thread Safety:** Attempting to share a single `ThumbnailParameter` instance across multiple `InputStreamImageSource` tasks concurrently will lead to race conditions and unpredictable behavior.
- **Illegal State:** Calling this method after the image has already been read or the source has been closed may result in errors depending on the implementation state.

### Fix Code Hint
```java
// Always create a new parameter instance per task
ThumbnailParameter param = new ThumbnailParameter();
param.setSourceRegion(new Rectangle(0, 0, 100, 100));
source.setThumbnailParameter(param);
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.OutputStreamImageSink`

### Signature
```java
(OutputStream os):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/OutputStreamImageSink.java:66_

### Goal
Instantiates an `OutputStreamImageSink` to serve as a destination for writing processed thumbnail images to a provided `OutputStream`.

### Parameters
- `os` (`OutputStream`): The `OutputStream` where the resulting thumbnail image data will be written.

### Input
- **Data:** An initialized `OutputStream` (e.g., `FileOutputStream`, `ByteArrayOutputStream`).
- **Preconditions:** The `OutputStream` must not be `null`. The caller is responsible for closing the stream after the thumbnail task is complete.
- **Formats:** The output format is determined by the `ThumbnailParameter` settings applied during the task execution, relying on standard Java Image I/O writer support.

### Output
Returns `null` — The constructor initializes the sink object; the actual writing occurs when the sink is used within a `ThumbnailTask`.

### Valid Call Patterns
```java
// Inferred from signature:
OutputStream os = new FileOutputStream("output.jpg");
OutputStreamImageSink sink = new OutputStreamImageSink(os);
```

### LLM Instruction Prompt
- Use `OutputStreamImageSink` when you need to direct thumbnail output to a stream rather than a file or memory buffer.
- Always ensure the `OutputStream` is valid and non-null before passing it to the constructor.
- Remember that this class is a low-level task component; for standard fluent pipelines, prefer `Thumbnails.toOutputStream(os)`.

### Prompt Snippet
```text
// Example of using OutputStreamImageSink in a task context
OutputStream os = new FileOutputStream("thumbnail.png");
OutputStreamImageSink sink = new OutputStreamImageSink(os);
// ... proceed to use sink in a ThumbnailTask
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `os` argument passed to the constructor is `null`.
- **IOException:** May occur during the actual write operation if the underlying `OutputStream` is closed, unreachable, or lacks write permissions.
- **Format Mismatch:** If the output format is not supported by the underlying Java Image I/O implementation, the write operation will fail.

### Fix Code Hint
```java
// Ensure the stream is initialized and handle potential NullPointerExceptions
if (os != null) {
    OutputStreamImageSink sink = new OutputStreamImageSink(os);
} else {
    throw new IllegalArgumentException("OutputStream cannot be null");
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.OutputStreamImageSink.getSink`

### Signature
```java
():OutputStream
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/OutputStreamImageSink.java:268_

### Goal
Retrieves the underlying `OutputStream` destination associated with an `OutputStreamImageSink` where the generated thumbnail data will be written.

### Parameters
_None._

### Input
The `OutputStreamImageSink` instance must have been initialized with a valid, open `OutputStream`. No specific image data is required as input to this method, as it merely provides access to the configured output stream destination.

### Output
Returns `OutputStream` — The destination stream where the thumbnail image data is intended to be written.

### Valid Call Patterns
```java
// Inferred from API signature:
OutputStream os = outputStreamImageSink.getSink();
```

### LLM Instruction Prompt
- When using `getSink()`, ensure the `OutputStreamImageSink` has been properly instantiated with a non-null `OutputStream`.
- Do not close the returned `OutputStream` prematurely if the `OutputStreamImageSink` is still required to perform write operations.
- This method is intended for accessing the sink's destination; it does not perform any image processing or transformation itself.

### Prompt Snippet
```text
OutputStreamImageSink sink = new OutputStreamImageSink(myOutputStream);
OutputStream target = sink.getSink();
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `OutputStreamImageSink` was initialized with a null stream or if the internal reference is uninitialized.
- **IOException**: May occur if the underlying stream has been closed or is otherwise inaccessible when the sink attempts to write to it.
- **State Mismanagement**: Attempting to write to the stream returned by `getSink()` while the `Thumbnailator` task is concurrently writing to the same sink may lead to corrupted output or stream contention.

### Fix Code Hint
```java
// Ensure the sink is initialized and the stream is valid before access
if (sink != null) {
    OutputStream os = sink.getSink();
    // Perform operations on the stream
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.OutputStreamImageSink.write`

### Signature
```java
(BufferedImage img):void
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/OutputStreamImageSink.java:92_

### Goal
Writes a `BufferedImage` to the associated `OutputStream` using the format specified in the sink configuration.

### Parameters
- `img` (`BufferedImage`): The image data to be encoded and written to the output stream.

### Input
- **Data:** A valid `BufferedImage` object.
- **Preconditions:** The `OutputStreamImageSink` must have its output format name set via `setOutputFormatName(String)` prior to calling this method.
- **Formats:** Supports any image format recognized by the underlying Java Image I/O API (e.g., "jpg", "png", "gif").

### Output
Returns `void` — The method performs a side-effect operation by writing the image bytes to the provided `OutputStream`.

### Valid Call Patterns
```java
// Inferred from API documentation
OutputStream os = new FileOutputStream("output.jpg");
OutputStreamImageSink sink = new OutputStreamImageSink(os);
sink.setOutputFormatName("jpg");
sink.write(bufferedImage);
```

### LLM Instruction Prompt
- When using `OutputStreamImageSink.write`, ensure the `OutputStream` is initialized and the output format is explicitly set using `setOutputFormatName` before invoking `write`. The `BufferedImage` must not be null.

### Prompt Snippet
```text
OutputStreamImageSink sink = new OutputStreamImageSink(outputStream);
sink.setOutputFormatName("png");
sink.write(bufferedImage);
```

### Common Failure Modes
- **`IllegalStateException`**: Thrown if `setOutputFormatName` has not been called to define the output format.
- **`NullPointerException`**: Thrown if the provided `BufferedImage` is `null`.
- **`UnsupportedFormatException`**: Thrown if the format specified via `setOutputFormatName` is not supported by the current Java environment.
- **`IOException`**: Thrown if an error occurs during the writing process to the underlying stream.

### Fix Code Hint
```java
// Ensure format is set before writing
sink.setOutputFormatName("jpg");
try {
    sink.write(img);
} catch (IOException e) {
    // Handle I/O error
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.URLImageSource`

### Signature
```java
(URL url,Proxy proxy):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/URLImageSource.java:99_

### Goal
Instantiates a source object that retrieves an image from a specified URL using a provided network proxy for connection.

### Parameters
- `url` (`URL`): The `URL` object pointing to the source image to be processed.
- `proxy` (`Proxy`): The `Proxy` configuration to be used when establishing the connection to the source URL.

### Input
- **Data:** A valid `URL` pointing to an image resource supported by the Java Image I/O API.
- **Preconditions:** The `url` and `proxy` arguments must not be null. The environment must allow network connections (though the library is designed for headless operation, this specific source requires network access to the provided URL).

### Output
Returns `null` — This constructor initializes the `URLImageSource` instance, which acts as a provider for the thumbnail generation task.

### Valid Call Patterns
```java
// Example inferred from signature and Javadoc
URL imageUrl = new URL("http://example.com/image.jpg");
Proxy proxy = new Proxy(Proxy.Type.HTTP, new InetSocketAddress("proxy.example.com", 8080));
URLImageSource source = new URLImageSource(imageUrl, proxy);
```

### LLM Instruction Prompt
- Use `java:net.coobird.thumbnailator.tasks.io.URLImageSource` when the thumbnail source is a remote network resource requiring proxy authentication or routing.
- Always ensure the `URL` and `Proxy` objects are instantiated and non-null before passing them to the constructor.
- Note that this class is part of the task-based processing workflow and is typically used when granular control over the I/O source is required outside of the fluent `Thumbnails` builder.

### Prompt Snippet
```text
URLImageSource source = new URLImageSource(url, proxy);
```

### Common Failure Modes
- `NullPointerException`: Thrown if either the `url` or `proxy` argument is null.
- `IOException`: May occur during the actual image retrieval process if the URL is unreachable, the proxy configuration is incorrect, or the remote resource is not a valid image format supported by the underlying Java Image I/O implementation.
- Network Restrictions: In strictly locked-down headless environments, network access may be blocked, causing connection failures.

### Fix Code Hint
```java
// Ensure the URL is valid and the proxy is correctly configured
try {
    URL url = new URL("http://example.com/image.jpg");
    Proxy proxy = new Proxy(Proxy.Type.HTTP, new InetSocketAddress("proxy.host", 8080));
    URLImageSource source = new URLImageSource(url, proxy);
    // Proceed to use source in a ThumbnailTask
} catch (MalformedURLException e) {
    // Handle invalid URL format
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.URLImageSource.getProxy`

### Signature
```java
():Proxy
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/URLImageSource.java:180_

### Goal
Retrieves the `java.net.Proxy` configuration currently assigned to the `URLImageSource` for establishing network connections when fetching source images from a URL.

### Parameters
_None._

### Input
The caller must have an initialized `URLImageSource` instance. No specific preconditions are required to call this getter, though the proxy returned may be `null` if no proxy has been explicitly configured for the source.

### Output
Returns `Proxy` — The `java.net.Proxy` object used to connect to the URL, or `null` if the connection is configured to use a direct connection without a proxy.

### Valid Call Patterns
```java
// Example inferred from signature
URLImageSource source = new URLImageSource(new URL("http://example.com/image.jpg"));
Proxy proxy = source.getProxy();
```

### LLM Instruction Prompt
- When generating code that interacts with `URLImageSource`, use `getProxy()` to inspect the network configuration of the source.
- Note that this method returns a `java.net.Proxy` object; ensure the calling code handles potential `null` values if no proxy is set.
- This method is specific to `URLImageSource` and is not applicable to other `ImageSource` implementations like `FileImageSource` or `BufferedImageSource`.

### Prompt Snippet
```text
// Accessing the proxy configuration of a URLImageSource
Proxy currentProxy = urlImageSource.getProxy();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `URLImageSource` instance itself is null.
- **Unexpected Null Return:** The method returns `null` if no proxy was set, which may cause issues if the caller assumes a non-null `Proxy` object is always returned.
- **Network Context:** This method only retrieves the proxy setting; it does not validate if the proxy is reachable or correctly configured for the target URL.

### Fix Code Hint
```java
// Always check for null when retrieving the proxy
Proxy proxy = source.getProxy();
if (proxy != null) {
    // Proceed with proxy-specific logic
} else {
    // Handle direct connection case
}
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.URLImageSource.getSource`

### Signature
```java
():URL
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/URLImageSource.java:170_

### Goal
Retrieves the `URL` object representing the source location from which the image data is to be fetched for a thumbnail processing task.

### Parameters
_None._

### Input
The caller must have initialized a `URLImageSource` instance with a valid `URL` pointing to an image resource. The library assumes the environment is headless and does not require network access for the `URL` object itself, though the `URL` must be reachable by the underlying Java Image I/O provider when the task is executed.

### Output
Returns `URL` — The `URL` object that was provided during the construction of the `URLImageSource`, representing the network or local resource location of the source image.

### Valid Call Patterns
```java
// Inferred from signature:
URL sourceUrl = urlImageSource.getSource();
```

### LLM Instruction Prompt
- Use this method only when you need to verify or retrieve the origin location of an image source that has already been encapsulated within a `URLImageSource` object.
- Do not attempt to use this method to modify the source; it is a getter for the existing `URL` configuration.
- Ensure the `URLImageSource` instance is properly instantiated before calling this method to avoid `NullPointerException`.

### Prompt Snippet
```text
// Example of retrieving the source URL from an existing URLImageSource instance
URL url = myUrlImageSource.getSource();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `URLImageSource` instance is null or if the internal reference to the `URL` was not initialized correctly.
- **Invalid URL State:** While the method returns the `URL`, the actual image processing may fail later if the `URL` points to an unreachable resource or an unsupported image format, as Thumbnailator relies on the standard Java Image I/O API for the actual data retrieval.

### Fix Code Hint
```java
// Ensure the URLImageSource is initialized with a valid URL before calling getSource()
URLImageSource source = new URLImageSource(new URL("http://example.com/image.jpg"));
URL retrievedUrl = source.getSource();
```

## API Test: `java:net.coobird.thumbnailator.tasks.io.URLImageSource.read`

### Signature
```java
():BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/tasks/io/URLImageSource.java:135_

### Goal
Retrieves and decodes an image from a specified URL into a `BufferedImage` for subsequent processing within the Thumbnailator pipeline.

### Parameters
_None._

### Input
The `URLImageSource` must be initialized with a valid `URL` pointing to an image resource. The underlying Java Image I/O API must support the format of the image located at the URL. As this library is designed for headless environments, ensure the URL is reachable without requiring a desktop UI or browser-based authentication.

### Output
Returns `BufferedImage` — The decoded image data retrieved from the source URL.

### Valid Call Patterns
```java
URL url = new URL("https://example.com/image.jpg");
URLImageSource source = new URLImageSource(url);
BufferedImage image = source.read();
```

### LLM Instruction Prompt
- When using `URLImageSource.read()`, ensure the `URLImageSource` instance has been correctly instantiated with a valid `java.net.URL` object.
- This method performs network I/O; ensure the execution environment permits network access, as Thumbnailator is intended for headless, non-interactive environments.
- The method throws an `IOException` if the URL is unreachable, the content is not a valid image, or the format is unsupported by the JRE's Image I/O providers.

### Prompt Snippet
```text
// Example of reading an image from a URL
URL imageUrl = new URL("https://example.com/image.jpg");
URLImageSource source = new URLImageSource(imageUrl);
BufferedImage img = source.read();
```

### Common Failure Modes
- **`IOException`**: Occurs if the network connection fails, the URL is malformed, or the server returns an error (e.g., 404).
- **Unsupported Format**: If the image format at the URL is not registered with the Java Image I/O API, the read operation may fail or return null depending on the provider.
- **Headless Restrictions**: In restricted environments, network access may be blocked by security managers or firewalls, causing the read to fail.

### Fix Code Hint
```java
try {
    URLImageSource source = new URLImageSource(new URL("https://example.com/image.jpg"));
    BufferedImage img = source.read();
} catch (IOException e) {
    // Handle network or decoding errors
    e.printStackTrace();
}
```

## API Test: `java:net.coobird.thumbnailator.util.BufferedImages.copy`

### Signature
```java
(BufferedImage img,int imageType):BufferedImage
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/BufferedImages.java:63_

### Goal
Creates a new `BufferedImage` with a specified image type that contains a graphical copy of the source image.

### Parameters
- `img` (`BufferedImage`): The source image to be copied.
- `imageType` (`int`): The `BufferedImage` type constant (e.g., `BufferedImage.TYPE_INT_ARGB`) to be used for the resulting image.

### Input
The caller must provide a valid, non-null `BufferedImage` instance and a valid integer representing a `BufferedImage` type constant defined in the `java.awt.image.BufferedImage` class. The source image must be fully initialized and accessible in the current headless environment.

### Output
Returns `BufferedImage` — A new image instance containing the graphical content of the source image, converted to the specified `imageType`.

### Valid Call Patterns
```java
// Inferred from signature: Copying an image to a specific type (e.g., ARGB)
BufferedImage source = ImageIO.read(new File("input.jpg"));
BufferedImage copy = net.coobird.thumbnailator.util.BufferedImages.copy(source, BufferedImage.TYPE_INT_ARGB);
```

### LLM Instruction Prompt
- Use `net.coobird.thumbnailator.util.BufferedImages.copy` when you need to perform a deep copy of a `BufferedImage` while simultaneously forcing a specific color model or image type.
- Always ensure the `imageType` provided is a valid constant from `java.awt.image.BufferedImage`.
- This method is useful for preparing images for filters or writers that require specific pixel formats (e.g., ensuring an alpha channel exists).

### Prompt Snippet
```text
net.coobird.thumbnailator.util.BufferedImages.copy(sourceImage, BufferedImage.TYPE_INT_ARGB)
```

### Common Failure Modes
- **NullPointerException**: Occurs if the `img` parameter is null.
- **IllegalArgumentException**: May occur if the provided `imageType` is not a valid constant recognized by the `BufferedImage` constructor.
- **Memory Exhaustion**: Copying very large images can lead to `OutOfMemoryError` in memory-constrained headless environments; consider using `Thumbnailator`'s fluent API for batch processing if memory is a concern.

### Fix Code Hint
```java
// Ensure the source image is not null before copying
if (sourceImage != null) {
    BufferedImage safeCopy = net.coobird.thumbnailator.util.BufferedImages.copy(sourceImage, BufferedImage.TYPE_INT_RGB);
}
```

## API Test: `java:net.coobird.thumbnailator.util.Configurations.getBoolean`

### Signature
```java
():boolean
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/Configurations.java:143_

### Goal
Determines whether a specific internal configuration setting is enabled within the Thumbnailator library environment.

### Parameters
_None._

### Input
There are no explicit parameters. The method relies on the internal state of the `Configurations` class, which is typically initialized via system properties (e.g., `-D` flags) or a `thumbnailator.properties` file located on the classpath.

### Output
Returns `boolean` — `true` if the specific configuration is enabled, `false` otherwise.

### Valid Call Patterns
```java
boolean isEnabled = net.coobird.thumbnailator.util.Configurations.getBoolean();
```
*(Note: This call pattern is inferred from the API signature as no direct test-suite or README examples were provided for this specific method.)*

### LLM Instruction Prompt
- Use this method to check the status of internal library configurations.
- Ensure the call is made within a context where the `Configurations` class has been properly initialized by the library's internal loading mechanism.
- Do not attempt to pass arguments, as the method signature is parameterless.

### Prompt Snippet
```text
// Check if a configuration is enabled
boolean status = net.coobird.thumbnailator.util.Configurations.getBoolean();
```

### Common Failure Modes
- **Initialization State:** Calling this method before the library has parsed the system properties or the `thumbnailator.properties` file may result in unexpected default values.
- **Contextual Ambiguity:** Since the method does not accept a key or identifier, it is intended for a specific, singular configuration check; ensure the surrounding code context aligns with the specific configuration intended for this method.

### Fix Code Hint
```java
// Ensure system properties are set before checking configurations
System.setProperty("thumbnailator.someProperty", "true");
boolean isEnabled = net.coobird.thumbnailator.util.Configurations.getBoolean();
```

## API Test: `java:net.coobird.thumbnailator.util.ThumbnailatorUtils.getSupportedOutputFormatTypes`

### Signature
```java
(String format):List<String>
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/ThumbnailatorUtils.java:97_

### Goal
Retrieves a list of supported compression types or subtypes available for a specific image output format, enabling developers to query capabilities before performing image encoding.

### Parameters
- `format` (`String`): The name of the image format (e.g., "jpg", "png", "bmp") for which supported compression types are to be queried.

### Input
The caller must provide a valid format string recognized by the underlying Java Image I/O API. This method is typically used to verify if specific compression schemes (like "JPEG" or "LZW") are available for the target format before initiating a `ThumbnailTask`.

### Output
Returns `List<String>` — A list of supported compression type strings for the specified format. If no specific compression types are supported, or if the format is not recognized, the method returns an empty list.

### Valid Call Patterns
```java
// Inferred from signature: Querying supported compression types for JPEG
List<String> jpegTypes = net.coobird.thumbnailator.util.ThumbnailatorUtils.getSupportedOutputFormatTypes("jpg");
```

### LLM Instruction Prompt
- Use `net.coobird.thumbnailator.util.ThumbnailatorUtils.getSupportedOutputFormatTypes(String format)` to inspect available compression options.
- Always check if the returned list is empty before attempting to set specific compression parameters in a `ThumbnailParameter` object.
- Ensure the `format` string matches standard Java Image I/O format names.

### Prompt Snippet
```text
// Example: Checking for available compression types for a JPEG output
List<String> types = net.coobird.thumbnailator.util.ThumbnailatorUtils.getSupportedOutputFormatTypes("jpg");
if (!types.isEmpty()) {
    // Proceed with configuration using one of the supported types
}
```

### Common Failure Modes
- **Empty List:** Passing a format that does not support compression (e.g., "png" in some environments) will return an empty list; do not assume the list will contain elements.
- **Case Sensitivity:** While Image I/O is generally flexible, passing null or unsupported format strings may result in an empty list rather than an exception.
- **Environment Dependency:** The returned list is strictly dependent on the underlying JRE/JDK's registered `ImageWriter` SPIs; results may vary between different Java distributions or headless environments.

### Fix Code Hint
```java
// Ensure the format is valid and handle the empty list case gracefully
String format = "jpg";
List<String> compressionTypes = net.coobird.thumbnailator.util.ThumbnailatorUtils.getSupportedOutputFormatTypes(format);
if (compressionTypes.contains("JPEG")) {
    // Apply specific compression settings
}
```

## API Test: `java:net.coobird.thumbnailator.util.ThumbnailatorUtils.getSupportedOutputFormats`

### Signature
```java
():List<String>
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/ThumbnailatorUtils.java:55_

### Goal
Retrieves a list of image format names that the underlying Java Image I/O environment is capable of writing as output.

### Parameters
_None._

### Input
No input parameters are required. The method relies on the runtime environment's `ImageIO` configuration. Precondition: The caller should ensure the JVM has access to the necessary image writers (e.g., standard JRE support for JPEG, PNG, and GIF).

### Output
Returns `List<String>` — A list of strings representing the names of supported output formats (e.g., "jpg", "png", "gif"). If no formats are supported by the current environment, an empty list is returned.

### Valid Call Patterns
```java
List<String> formats = net.coobird.thumbnailator.util.ThumbnailatorUtils.getSupportedOutputFormats();
```

### LLM Instruction Prompt
- Use `net.coobird.thumbnailator.util.ThumbnailatorUtils.getSupportedOutputFormats()` to programmatically verify if a specific output format is supported before attempting to write a thumbnail.
- Do not assume specific formats like "webp" or "heif" are available, as support depends on the underlying JRE/JDK environment.

### Prompt Snippet
```text
// Check if the desired output format is supported
List<String> supported = net.coobird.thumbnailator.util.ThumbnailatorUtils.getSupportedOutputFormats();
if (supported.contains("jpg")) {
    // Proceed with thumbnail generation
}
```

### Common Failure Modes
- **Environment Dependency:** The returned list may vary between different JRE/JDK distributions or versions, leading to unexpected behavior if the code assumes a specific format is always present.
- **Empty List:** If the environment is severely restricted or lacks standard `ImageIO` providers, the method may return an empty list, causing subsequent `outputFormat()` calls in the fluent API to fail.

### Fix Code Hint
```java
// Always validate format support before setting it in the fluent pipeline
List<String> supported = net.coobird.thumbnailator.util.ThumbnailatorUtils.getSupportedOutputFormats();
String format = "jpg";
if (supported.contains(format)) {
    Thumbnails.of("input.png").size(100, 100).outputFormat(format).toFile("output.jpg");
} else {
    throw new UnsupportedOperationException("Format " + format + " is not supported by this environment.");
}
```

## API Test: `java:net.coobird.thumbnailator.util.ThumbnailatorUtils.isSupportedOutputFormat`

### Signature
```java
(String format):boolean
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/ThumbnailatorUtils.java:72_

### Goal
Determines whether a specified image format is supported for output operations by the underlying Java Image I/O environment.

### Parameters
- `format` (`String`): The informal name of the image format (e.g., "jpg", "png", "gif") to be checked for output compatibility.

### Input
The caller must provide a `String` representing the format name. This method relies on the Java Image I/O API; therefore, the availability of specific formats depends on the underlying JRE/JDK environment and any installed image I/O plugins.

### Output
Returns `boolean` — `true` if the specified format is supported for writing by the current environment, `false` otherwise.

### Valid Call Patterns
```java
boolean isSupported = net.coobird.thumbnailator.util.ThumbnailatorUtils.isSupportedOutputFormat("jpg");
```
*(Note: This call pattern is inferred from the API signature and standard Java utility usage.)*

### LLM Instruction Prompt
- Use `net.coobird.thumbnailator.util.ThumbnailatorUtils.isSupportedOutputFormat(String format)` to verify format compatibility before attempting to set an output format in a `Thumbnails` pipeline.
- Always pass the format string as a simple identifier (e.g., "png", "jpeg").

### Prompt Snippet
```text
// Check if the desired output format is supported before configuring the pipeline
if (net.coobird.thumbnailator.util.ThumbnailatorUtils.isSupportedOutputFormat("png")) {
    Thumbnails.of("input.jpg").outputFormat("png").toFile("output.png");
}
```

### Common Failure Modes
- **Environment Dependency:** The method may return `false` for formats like "webp" or "heif" if the host JRE does not include native or third-party support for those specific encoders.
- **Case Sensitivity:** While many Image I/O implementations are case-insensitive, passing unexpected characters or non-standard format strings may result in `false`.
- **Null Input:** Passing `null` as the `format` argument may result in a `NullPointerException` depending on the internal implementation of the underlying `ImageIO` registry.

### Fix Code Hint
```java
// Ensure the format is supported before proceeding
String format = "webp";
if (net.coobird.thumbnailator.util.ThumbnailatorUtils.isSupportedOutputFormat(format)) {
    Thumbnails.of(source).outputFormat(format).toFile(destination);
} else {
    // Fallback to a standard format like "jpg"
    Thumbnails.of(source).outputFormat("jpg").toFile(destination);
}
```

## API Test: `java:net.coobird.thumbnailator.util.ThumbnailatorUtils.isSupportedOutputFormatType`

### Signature
```java
(String format,String type):boolean
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/ThumbnailatorUtils.java:130_

### Goal
Determines whether a specific image format type is supported for a given output format within the current Java Image I/O environment.

### Parameters
- `format` (`String`): The name of the image format (e.g., "jpg", "png").
- `type` (`String`): The specific format type or compression/subtype to verify for the given format.

### Input
The caller must provide valid `String` identifiers for the format and type. This method relies on the underlying Java Image I/O API; therefore, support is dependent on the JRE/JDK environment and available registered `ImageWriter` SPIs.

### Output
Returns `boolean` — `true` if the specified format type is supported by the given output format, `false` otherwise.

### Valid Call Patterns
```java
boolean isSupported = net.coobird.thumbnailator.util.ThumbnailatorUtils.isSupportedOutputFormatType("jpg", "jpeg");
```
*(Note: This call pattern is inferred from the API signature and standard Java Image I/O usage, as no direct test-suite example was provided.)*

### LLM Instruction Prompt
- Use this method to validate output format capabilities before attempting to write thumbnails, especially when dealing with specific format subtypes.
- Always pass the format name as the first argument and the specific type as the second.
- Remember that availability is environment-dependent; do not assume all formats are supported across all JVMs.

### Prompt Snippet
```text
net.coobird.thumbnailator.util.ThumbnailatorUtils.isSupportedOutputFormatType(format, type)
```

### Common Failure Modes
- **Environment Mismatch:** Returning `false` because the underlying JRE lacks the necessary `ImageWriter` SPI for the requested format/type combination.
- **Case Sensitivity:** Passing format strings that do not match the case expected by the registered `ImageWriter` (e.g., "JPG" vs "jpg").
- **Null Inputs:** Passing `null` values may result in a `NullPointerException` depending on the internal implementation of the `ImageIO` lookup.

### Fix Code Hint
```java
// Check support before proceeding with output operations
if (ThumbnailatorUtils.isSupportedOutputFormatType("png", "png")) {
    Thumbnails.of("input.jpg")
        .outputFormat("png")
        .toFile("output.png");
} else {
    // Fallback or error handling
}
```

## API Test: `java:net.coobird.thumbnailator.util.exif.ExifFilterUtils.getFilterForOrientation`

### Signature
```java
(Orientation orientation):ImageFilter
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/ExifFilterUtils.java:56_

### Goal
Returns an `ImageFilter` that applies the necessary geometric transformations to a `BufferedImage` to correct its orientation based on Exif metadata.

### Parameters
- `orientation` (`Orientation`): An `Orientation` enum value representing the Exif orientation tag extracted from the source image.

### Input
The caller must provide a valid `Orientation` enum constant. This utility assumes the caller has already parsed the Exif metadata from the source image to determine the correct orientation state.

### Output
Returns an `ImageFilter` instance that, when applied to an image, performs the rotation or flipping required to align the image with the specified `Orientation`.

### Valid Call Patterns
```java
// Example usage (inferred from signature):
import net.coobird.thumbnailator.util.exif.ExifFilterUtils;
import net.coobird.thumbnailator.util.exif.Orientation;
import net.coobird.thumbnailator.filters.ImageFilter;

ImageFilter filter = ExifFilterUtils.getFilterForOrientation(Orientation.TOP_LEFT);
```

### LLM Instruction Prompt
- When generating code that uses `ExifFilterUtils.getFilterForOrientation`, ensure the `Orientation` value is obtained from the image's metadata. The returned `ImageFilter` should be passed to the `addFilter` method of a `Thumbnails.Builder` or applied directly to a `BufferedImage` if performing manual processing.

### Prompt Snippet
```text
// Apply Exif orientation correction
ImageFilter orientationFilter = ExifFilterUtils.getFilterForOrientation(detectedOrientation);
builder.addFilter(orientationFilter);
```

### Common Failure Modes
- **Null Orientation:** Passing a `null` value for the `orientation` parameter will likely result in a `NullPointerException`.
- **Incorrect Metadata:** Providing an `Orientation` value that does not match the actual image data will result in an incorrectly rotated or flipped output image.
- **Unsupported Orientation:** If the `Orientation` enum does not cover the specific Exif tag found, the filter may not produce the expected visual result.

### Fix Code Hint
```java
// Ensure the orientation is retrieved from a reliable source before calling
if (orientation != null) {
    ImageFilter filter = ExifFilterUtils.getFilterForOrientation(orientation);
    // Apply filter to the pipeline
}
```

## API Test: `java:net.coobird.thumbnailator.util.exif.ExifUtils.getExifOrientation`

### Signature
```java
(ImageReader reader,int imageIndex):Orientation
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/ExifUtils.java:69_

### Goal
Retrieves the Exif orientation metadata from a specified image within an `ImageReader` to determine if rotation or flipping is required for correct display.

### Parameters
- `reader` (`ImageReader`): An active `ImageReader` instance currently reading the target image source.
- `imageIndex` (`int`): The zero-based index of the image within the file from which to extract Exif metadata.

### Input
The caller must provide an `ImageReader` that has been initialized with an image source (e.g., via `ImageIO.createImageInputStream`). The reader must have the target image set, and it must be processing a format that supports Exif metadata, specifically a JPEG.

### Output
Returns an `Orientation` enum representing the Exif orientation tag value. Returns `null` if no orientation metadata is found in the specified image.

### Valid Call Patterns
```java
// Inferred from signature:
Orientation orientation = net.coobird.thumbnailator.util.exif.ExifUtils.getExifOrientation(reader, 0);
```

### LLM Instruction Prompt
When calling `getExifOrientation`, ensure the `ImageReader` is already opened and pointing to a valid JPEG source. Always check for a `null` return value, as not all images contain Exif orientation tags. Ensure the `imageIndex` is valid for the provided reader to avoid `IllegalArgumentException`.

### Prompt Snippet
```text
// Example usage of ExifUtils to check orientation
ImageReader reader = ImageIO.getImageReadersByFormatName("jpeg").next();
reader.setInput(ImageIO.createImageInputStream(new File("image.jpg")));
Orientation orient = net.coobird.thumbnailator.util.exif.ExifUtils.getExifOrientation(reader, 0);
```

### Common Failure Modes
- `IOException`: Thrown if an error occurs during the reading process.
- `IllegalArgumentException`: Thrown if the `ImageReader` does not have the target image set or if the reader does not have a JPEG open.
- `NullPointerException`: Occurs if the `reader` argument is passed as `null`.

### Fix Code Hint
```java
try {
    Orientation orient = net.coobird.thumbnailator.util.exif.ExifUtils.getExifOrientation(reader, index);
    if (orient != null) {
        // Apply orientation logic
    }
} catch (IllegalArgumentException e) {
    // Handle case where reader is not properly initialized or not a JPEG
}
```

## API Test: `java:net.coobird.thumbnailator.util.exif.ExifUtils.getOrientationFromExif`

### Signature
```java
(byte[] exifData):Orientation
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/ExifUtils.java:109_

### Goal
Extracts the orientation metadata from a raw Exif byte array to determine the necessary rotation or flipping required to display an image correctly.

### Parameters
- `exifData` (`byte[]`): A byte array containing the raw Exif metadata extracted from an image file.

### Input
The caller must provide a valid byte array containing Exif metadata. This data is typically extracted from the image file's header or metadata segment. If the provided byte array does not contain valid Exif data or lacks orientation information, the method will return `null`.

### Output
Returns `Orientation` — An enum representing the orientation state (e.g., normal, rotated, or flipped). Returns `null` if no orientation information is found within the provided Exif data.

### Valid Call Patterns
```java
// Example of extracting orientation from a byte array
byte[] exifData = /* extracted from image source */;
Orientation orientation = net.coobird.thumbnailator.util.exif.ExifUtils.getOrientationFromExif(exifData);
```

### LLM Instruction Prompt
- Use `net.coobird.thumbnailator.util.exif.ExifUtils.getOrientationFromExif` when you need to programmatically determine the orientation of an image based on its Exif metadata.
- Always check for a `null` return value, as the method returns `null` if no orientation tag is present in the provided byte array.
- Ensure the input `byte[]` is correctly extracted from the image source before passing it to this utility.

### Prompt Snippet
```text
net.coobird.thumbnailator.util.exif.ExifUtils.getOrientationFromExif(exifData)
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `exifData` argument passed to the method is `null`.
- **Incorrect Metadata:** If the byte array provided is not valid Exif data, the method may fail to parse the orientation, returning `null` or potentially throwing an exception depending on the internal parsing logic.
- **Missing Orientation:** The method returns `null` if the Exif data is valid but does not contain an orientation tag; ensure your code handles this `null` case to avoid `NullPointerException` when accessing the `Orientation` enum.

### Fix Code Hint
```java
byte[] data = getExifDataFromImage();
Orientation orientation = net.coobird.thumbnailator.util.exif.ExifUtils.getOrientationFromExif(data);
if (orientation != null) {
    // Apply orientation correction logic
} else {
    // Handle case where no orientation is specified
}
```

## API Test: `java:net.coobird.thumbnailator.util.exif.IfdStructure`

### Signature
```java
(int tag,int type,int count,int offsetValue):null
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdStructure.java:49_

### Goal
Instantiates an Image File Directory (IFD) structure used for parsing and representing Exif metadata tags within image files.

### Parameters
- `tag` (`int`): The specific Exif tag identifier.
- `type` (`int`): The data type of the tag (e.g., BYTE, ASCII, SHORT, LONG, RATIONAL).
- `count` (`int`): The number of values associated with the tag.
- `offsetValue` (`int`): The offset to the data or the actual value if it fits within the structure.

### Input
The caller must provide raw integer values derived from the Exif metadata segment of an image file. This class is typically used internally by Thumbnailator's Exif parsing utilities to map binary metadata into a structured format.

### Output
Returns `null` (Constructor). The object represents a single entry in an IFD, encapsulating the metadata attribute's definition.

### Valid Call Patterns
```java
// Example inferred from signature:
net.coobird.thumbnailator.util.exif.IfdStructure ifd = new net.coobird.thumbnailator.util.exif.IfdStructure(tag, type, count, offsetValue);
```

### LLM Instruction Prompt
- Use this constructor only when manually parsing or reconstructing Exif metadata structures.
- Ensure the `type` parameter corresponds to valid Exif 2.3 data types as defined in the library's `IfdType` enum.
- This class is a low-level utility; prefer using `ExifUtils` for high-level orientation corrections.

### Prompt Snippet
```text
net.coobird.thumbnailator.util.exif.IfdStructure(tag, type, count, offsetValue)
```

### Common Failure Modes
- Providing invalid `type` integers that do not map to supported Exif data types, leading to incorrect metadata interpretation.
- Misinterpreting the `offsetValue` (which may contain the actual data or a pointer to it depending on the `type` and `count`), causing data corruption during metadata extraction.

### Fix Code Hint
```java
// Ensure type constants match the expected Exif specification
int tag = 0x0112; // Orientation tag
int type = 3;     // SHORT
int count = 1;
int offsetValue = 1; // Value for orientation
net.coobird.thumbnailator.util.exif.IfdStructure ifd = new net.coobird.thumbnailator.util.exif.IfdStructure(tag, type, count, offsetValue);
```

## API Test: `java:net.coobird.thumbnailator.util.exif.IfdStructure.getCount`

### Signature
```java
():int
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdStructure.java:82_

### Goal
Retrieves the count element from an Image File Directory (IFD) structure, which specifies the number of values contained within the associated value field of the Exif metadata.

### Parameters
_None._

### Input
The caller must have an initialized `net.coobird.thumbnailator.util.exif.IfdStructure` object. This class is used internally by Thumbnailator's Exif metadata parsing utilities to interpret image orientation and other metadata tags.

### Output
Returns `int` — The count value representing the number of data elements present in the IFD entry.

### Valid Call Patterns
```java
// Inferred from API signature:
int count = ifdStructure.getCount();
```

### LLM Instruction Prompt
- Use `getCount()` only when working with `IfdStructure` instances obtained during Exif metadata processing.
- Do not assume the returned integer represents the byte size of the field; it represents the number of values as defined by the Exif 2.3 specification.
- Ensure the `IfdStructure` instance is properly initialized from image metadata before invoking this method.

### Prompt Snippet
```text
// Accessing the count of values in an IFD entry
int valueCount = ifdStructure.getCount();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `IfdStructure` instance has not been initialized or is null.
- **Invalid Metadata Context:** Calling this on an `IfdStructure` that does not correspond to a valid Exif tag entry may return unexpected or zero values, as the structure relies on the underlying binary metadata being correctly parsed.

### Fix Code Hint
```java
// Ensure the IfdStructure is valid before accessing metadata properties
if (ifdStructure != null) {
    int count = ifdStructure.getCount();
    // Proceed with processing based on the count
}
```

## API Test: `java:net.coobird.thumbnailator.util.exif.IfdStructure.getOffsetValue`

### Signature
```java
():int
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdStructure.java:93_

### Goal
Retrieves the raw integer value stored within an Image File Directory (IFD) structure, which represents either a direct data value or an offset pointer to the actual data location.

### Parameters
_None._

### Input
The caller must have an initialized `net.coobird.thumbnailator.util.exif.IfdStructure` object. This method is typically used during the parsing of Exif metadata where the structure has already been populated from an image source.

### Output
Returns `int` — The integer value or offset contained in the IFD entry. The semantic meaning of this integer (whether it is a literal value or a memory/file offset) must be determined by checking the state of the `IfdStructure` using `isOffset()` or `isValue()` before interpreting the result.

### Valid Call Patterns
```java
// Inferred from API documentation
IfdStructure ifd = ...; // Obtained from Exif parsing
int value = ifd.getOffsetValue();
```

### LLM Instruction Prompt
- When calling `getOffsetValue()`, always precede the call with a check to `isOffset()` or `isValue()` to correctly interpret the returned integer. Do not assume the integer represents a literal value without verifying the structure's state.

### Prompt Snippet
```text
IfdStructure ifd = ...;
if (ifd.isValue()) {
    int data = ifd.getOffsetValue();
    // Process as literal value
} else if (ifd.isOffset()) {
    int pointer = ifd.getOffsetValue();
    // Process as offset pointer
}
```

### Common Failure Modes
- **Misinterpretation:** Treating an offset pointer as a literal data value (or vice versa) because the caller failed to verify the structure type using `isOffset()` or `isValue()`.
- **Null Pointer:** Calling this method on an uninitialized or null `IfdStructure` instance.

### Fix Code Hint
```java
// Always verify the type before usage
if (ifd.isValue()) {
    int val = ifd.getOffsetValue();
}
```

## API Test: `java:net.coobird.thumbnailator.util.exif.IfdStructure.getTag`

### Signature
```java
():int
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdStructure.java:63_

### Goal
Retrieves the tag element identifier from an Image File Directory (IFD) structure used during Exif metadata parsing.

### Parameters
_None._

### Input
The caller must have an initialized `net.coobird.thumbnailator.util.exif.IfdStructure` object. This class is used internally by Thumbnailator's Exif utility classes to process metadata; it is not typically instantiated directly by end-users in standard fluent pipeline workflows.

### Output
Returns `int` — An integer representation of the tag element, expected to be a value between `0x00` and `0xFF`.

### Valid Call Patterns
```java
// Inferred from signature:
int tag = ifdStructure.getTag();
```

### LLM Instruction Prompt
- When generating code involving Exif metadata, use `java:net.coobird.thumbnailator.util.exif.IfdStructure.getTag` only when working with low-level IFD parsing logic.
- Ensure the receiver is an instance of `net.coobird.thumbnailator.util.exif.IfdStructure`.
- Do not assume the returned integer maps to a specific human-readable tag without consulting the Exif 2.3 specification, as this method returns the raw tag element.

### Prompt Snippet
```text
// Accessing the tag from an IfdStructure instance
int tagValue = ifdStructure.getTag();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `IfdStructure` instance has not been properly initialized before calling `getTag()`.
- **Misinterpretation:** The returned `int` is a raw tag identifier; treating it as a data value or an orientation constant will lead to incorrect metadata processing.
- **Scope:** Attempting to use this method in a standard fluent `Thumbnails` pipeline is incorrect, as Exif handling is abstracted by `useExifOrientation(boolean)`.

### Fix Code Hint
```java
// Ensure the IfdStructure is valid before access
if (ifdStructure != null) {
    int tag = ifdStructure.getTag();
    // Process tag...
}
```

## API Test: `java:net.coobird.thumbnailator.util.exif.IfdStructure.getType`

### Signature
```java
():IfdType
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdStructure.java:72_

### Goal
Retrieves the data type associated with an Image File Directory (IFD) structure entry, as defined by the Exif 2.3 specification.

### Parameters
_None._

### Input
The caller must have an initialized `IfdStructure` object. This method assumes the structure has been correctly parsed from Exif metadata within an image file.

### Output
Returns `IfdType` — An enum constant representing the data type of the IFD entry (e.g., `BYTE`, `ASCII`, `SHORT`, `LONG`, `RATIONAL`).

### Valid Call Patterns
```java
// Example inferred from signature
IfdStructure structure = ...; // Obtained from Exif parsing
IfdType type = structure.getType();
```

### LLM Instruction Prompt
- Use `java:net.coobird.thumbnailator.util.exif.IfdStructure.getType` only when inspecting the metadata structure of an image that has been processed for Exif data.
- Ensure the `IfdStructure` instance is not null before invoking this method.
- The returned `IfdType` should be used to interpret the raw data values stored within the corresponding IFD entry.

### Prompt Snippet
```text
// Accessing the type of an IFD structure entry
IfdType entryType = ifdStructure.getType();
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `IfdStructure` instance is null.
- **Invalid Metadata State:** If the underlying image does not contain valid Exif metadata or the `IfdStructure` was not properly initialized, the behavior of the structure may be undefined.

### Fix Code Hint
```java
if (ifdStructure != null) {
    IfdType type = ifdStructure.getType();
    // Proceed with type-specific handling
}
```

## API Test: `java:net.coobird.thumbnailator.util.exif.IfdStructure.isOffset`

### Signature
```java
():boolean
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdStructure.java:122_

### Goal
Determines whether the value associated with an Exif Image File Directory (IFD) structure entry represents an offset to another data structure.

### Parameters
_None._

### Input
The caller must have an initialized `IfdStructure` object, typically obtained during the parsing of Exif metadata from an image file. No specific preconditions are required for the method call itself, but the `IfdStructure` instance must be in a valid state representing a parsed Exif tag.

### Output
Returns `boolean` — `true` if the value returned by `getOffsetValue()` is an offset value, `false` otherwise.

### Valid Call Patterns
```java
// Inferred from API signature; assumes an instance 'ifd' of IfdStructure
boolean isOffset = ifd.isOffset();
```

### LLM Instruction Prompt
- Use this method to inspect `IfdStructure` instances when traversing Exif metadata.
- Always check `isOffset()` before attempting to interpret the result of `getOffsetValue()` as a pointer or memory address within the Exif data block.
- This method is part of the internal Exif utility package; ensure the context involves image metadata processing.

### Prompt Snippet
```text
// Check if the current IFD entry points to another structure
if (ifdStructure.isOffset()) {
    long offset = ifdStructure.getOffsetValue();
    // Proceed with offset-based navigation
}
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `IfdStructure` instance is null.
- **Invalid State:** Calling this on an `IfdStructure` that has not been properly initialized or populated with metadata from an image source may lead to unexpected boolean results.
- **Misinterpretation:** Assuming the value is an offset without verifying via `isOffset()` can lead to incorrect data parsing if the value is actually a literal data value.

### Fix Code Hint
```java
// Ensure the instance is not null before checking
if (ifd != null && ifd.isOffset()) {
    // Safe to use the offset value
}
```

## API Test: `java:net.coobird.thumbnailator.util.exif.IfdStructure.isValue`

### Signature
```java
():boolean
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdStructure.java:105_

### Goal
Determines whether the data associated with an `IfdStructure` instance represents an actual value rather than an offset pointer to external data.

### Parameters
_None._

### Input
The caller must have an initialized `IfdStructure` object, typically obtained during the parsing of Exif metadata from an image file. No specific preconditions are required for the method call itself, though the `IfdStructure` instance must be in a valid state following metadata extraction.

### Output
Returns `boolean` — `true` if the value returned by `getOffsetValue()` is an actual value, and `false` if it represents an offset to data stored elsewhere.

### Valid Call Patterns
```java
// Inferred from signature:
IfdStructure structure = ...; // Obtained from Exif parsing
boolean isValue = structure.isValue();
```

### LLM Instruction Prompt
- Use this method to distinguish between inline Exif data and data that requires a secondary lookup (offset).
- Always check `isValue()` before interpreting the result of `getOffsetValue()` to ensure correct data handling.
- This method is part of the internal Exif utility package; ensure the `IfdStructure` instance is properly initialized by the library's metadata parsing logic before invocation.

### Prompt Snippet
```text
IfdStructure structure = ...;
if (structure.isValue()) {
    // Process the value directly
} else {
    // Handle the offset to external data
}
```

### Common Failure Modes
- **NullPointerException:** Occurs if the `IfdStructure` instance is null.
- **Logic Errors:** Incorrectly assuming that `getOffsetValue()` always returns the actual data without checking `isValue()` first, leading to misinterpretation of Exif tags that store pointers to large data blocks.

### Fix Code Hint
```java
// Always verify the nature of the data before access
if (ifdStructure.isValue()) {
    long actualValue = ifdStructure.getOffsetValue();
    // ... use value
} else {
    long offset = ifdStructure.getOffsetValue();
    // ... resolve offset
}
```

## API Test: `java:net.coobird.thumbnailator.util.exif.IfdType.size`

### Signature
```java
():int
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdType.java:93_

### Goal
Returns the size in bytes associated with a specific Exif Image File Directory (IFD) data type.

### Parameters
_None._

### Input
The caller must invoke this method on an instance of the `net.coobird.thumbnailator.util.exif.IfdType` enum. No external data or file formats are required as input, as the size is a property inherent to the defined Exif data type (e.g., `BYTE`, `ASCII`, `SHORT`, `LONG`, `RATIONAL`).

### Output
Returns `int` — The size in bytes corresponding to the specific IFD data type.

### Valid Call Patterns
```java
// Example: Retrieving the size of the SHORT IFD type
int size = net.coobird.thumbnailator.util.exif.IfdType.SHORT.size();
```

### LLM Instruction Prompt
- When generating code that interacts with Exif metadata parsing, use `IfdType.size()` to determine the byte-length of specific metadata fields.
- Ensure the receiver is a valid `IfdType` enum constant.
- Do not attempt to pass arguments to this method, as it takes no parameters.

### Prompt Snippet
```text
// Accessing the size of an IfdType constant
int byteSize = IfdType.RATIONAL.size();
```

### Common Failure Modes
- Attempting to call `size()` on a null reference if an `IfdType` variable has not been initialized.
- Misinterpreting the returned `int` as a count of elements rather than the size in bytes for a single unit of that data type.

### Fix Code Hint
```java
// Ensure the IfdType is correctly identified before calling size()
IfdType type = IfdType.valueOf("LONG");
int bytes = type.size();
```

## API Test: `java:net.coobird.thumbnailator.util.exif.IfdType.typeOf`

### Signature
```java
(int value):IfdType
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdType.java:115_

### Goal
Retrieves the `IfdType` enum constant corresponding to a specific integer value used in Exif metadata parsing.

### Parameters
- `value` (`int`): The integer representation of an Exif IFD (Image File Directory) data type as defined by the Exif 2.3 specification.

### Input
The caller must provide an integer value that maps to a valid Exif data type (e.g., `BYTE`, `ASCII`, `SHORT`, `LONG`, `RATIONAL`). No specific file format is required as input, but the value is typically extracted from the binary Exif metadata segment of an image file.

### Output
Returns `IfdType` — The corresponding enum constant for the provided integer value. Returns `null` if the given integer does not correspond to a valid `IfdType`.

### Valid Call Patterns
```java
// Inferred from signature:
IfdType type = net.coobird.thumbnailator.util.exif.IfdType.typeOf(3);
```

### LLM Instruction Prompt
- Use `net.coobird.thumbnailator.util.exif.IfdType.typeOf(int)` to resolve raw Exif metadata tags into type-safe `IfdType` constants.
- Always check for a `null` return value, as the method does not throw an exception for invalid or unrecognized Exif type integers.

### Prompt Snippet
```text
net.coobird.thumbnailator.util.exif.IfdType.typeOf(int_value)
```

### Common Failure Modes
- **NullPointerException:** Occurs if the caller attempts to invoke methods on the result of `typeOf` without checking if the returned `IfdType` is `null`.
- **Invalid Mapping:** Passing an integer that does not represent a valid Exif 2.3 data type will result in a `null` return, which may cause logic errors if the application expects a valid enum constant.

### Fix Code Hint
```java
int rawValue = getExifTypeFromMetadata();
IfdType type = IfdType.typeOf(rawValue);
if (type != null) {
    // Proceed with type-safe processing
} else {
    // Handle unknown or invalid Exif type
}
```

## API Test: `java:net.coobird.thumbnailator.util.exif.IfdType.value`

### Signature
```java
():int
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/IfdType.java:102_

### Goal
Retrieves the integer representation of an Exif Image File Directory (IFD) data type as defined by the Exif 2.3 specification.

### Parameters
_None._

### Input
The caller must have an instance of the `net.coobird.thumbnailator.util.exif.IfdType` enum. No external data or file formats are required as input to this method, as it operates on the internal state of the enum constant.

### Output
Returns `int` — The numeric type value associated with the specific Exif IFD data type (e.g., corresponding to types like BYTE, ASCII, SHORT, LONG, or RATIONAL).

### Valid Call Patterns
```java
// Example inferred from signature:
int typeValue = net.coobird.thumbnailator.util.exif.IfdType.BYTE.value();
```

### LLM Instruction Prompt
- Use this method only when working with `net.coobird.thumbnailator.util.exif.IfdType` enum constants.
- Do not attempt to instantiate `IfdType` directly; use the predefined enum members.
- The returned integer is intended for low-level Exif metadata parsing and should be treated as a constant identifier for the data type.

### Prompt Snippet
```text
// Accessing the integer value of an Exif IFD type
int val = IfdType.SHORT.value();
```

### Common Failure Modes
- **NullPointerException:** Calling `.value()` on a null reference if an `IfdType` variable has not been initialized.
- **Misinterpretation:** Assuming the returned integer corresponds to a specific byte-length without verifying the Exif 2.3 specification, as the value is an identifier, not necessarily the size in bytes.

### Fix Code Hint
```java
// Ensure the enum constant is valid before accessing its value
IfdType type = IfdType.ASCII;
if (type != null) {
    int val = type.value();
}
```

## API Test: `java:net.coobird.thumbnailator.util.exif.Orientation.typeOf`

### Signature
```java
(int value):Orientation
```
_Source: source/src/main/java/net/coobird/thumbnailator/util/exif/Orientation.java:124_

### Goal
Maps a raw integer Exif orientation value to its corresponding `Orientation` enum constant used for metadata-aware image transformations.

### Parameters
- `value` (`int`): The integer representation of the Exif orientation tag (typically ranging from 1 to 8).

### Input
The caller must provide an integer value derived from the Exif metadata of an image. No specific file format is required as input, but the value must correspond to the standard Exif orientation specifications (1-8).

### Output
Returns an `Orientation` enum constant representing the orientation state. Returns `null` if the provided integer does not correspond to a valid `Orientation` defined in the library.

### Valid Call Patterns
```java
// Inferred from signature:
Orientation orientation = net.coobird.thumbnailator.util.exif.Orientation.typeOf(exifValue);
```

### LLM Instruction Prompt
- Use `net.coobird.thumbnailator.util.exif.Orientation.typeOf(int)` to convert raw Exif metadata integers into type-safe `Orientation` objects.
- Always check for `null` return values, as the method returns `null` for invalid or unrecognized orientation integers.

### Prompt Snippet
```text
// Example of converting an Exif tag value to an Orientation enum
int exifTag = 6; // Example value for 90-degree clockwise rotation
Orientation orientation = net.coobird.thumbnailator.util.exif.Orientation.typeOf(exifTag);
if (orientation != null) {
    // Proceed with orientation-aware processing
}
```

### Common Failure Modes
- **NullPointerException:** Occurs if the caller assumes the return value is always a valid `Orientation` constant without checking for `null` when an invalid integer is passed.
- **Incorrect Metadata:** Passing an integer that does not conform to the Exif 2.3 standard will result in a `null` return, potentially breaking downstream logic that expects a valid orientation.

### Fix Code Hint
```java
int rawValue = getExifOrientationFromMetadata();
Orientation orientation = net.coobird.thumbnailator.util.exif.Orientation.typeOf(rawValue);
if (orientation == null) {
    // Handle invalid orientation value, e.g., default to Orientation.TOP_LEFT
    orientation = Orientation.TOP_LEFT;
}
```

