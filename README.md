# 🪨 Rock Image Preprocessing for ImageJ

This R script batch-processes rock photos to enhance contrast and isolate features for easier analysis in **ImageJ**. It converts all `.jpg` images in the working directory by:

1. Increasing contrast  
2. Converting to HSL color space  
3. Extracting and inverting the lightness channel  
4. Saving the processed image to an `inverted/` subfolder

This method helps make rocks stand out more clearly from background textures, shadows, and lighting inconsistencies.

---

## 📁 Folder Structure
```bash
root/
├── example1.jpg
├── example2.jpg
├── ....jpg
├── photo_inversion.R
├── inverted/
│ ├── example1.jpg
│ └── example2.jpg
│ └── ....jpg
└── README.md
```

---

## 💻 How to Use

1. **Install R** and open the R console or RStudio  
2. Make sure the [`magick`](https://cran.r-project.org/web/packages/magick/index.html) package is installed  
3. Place your `.jpg` images in the root folder  
4. Run the script below  

---

## 🧠 R Script

```r
# Load required package
if (!require("magick")) install.packages("magick", dependencies = TRUE)
library(magick)

# Create a new folder for inverted images (if it doesn't exist)
output_folder <- "inverted"
if (!dir.exists(output_folder)) {
  dir.create(output_folder)
}

# List all .jpg files in the current directory
jpg_files <- list.files(pattern = "\\.jpg$", ignore.case = TRUE)

# Loop through and process each image
for (file in jpg_files) {
  img <- image_read(file)
  img <- image_contrast(img, sharpen = 1)                # Sharpen contrast
  img_hsl <- image_convert(img, colorspace = "HSL")      # Convert to HSL
  img_channel <- image_channel(img_hsl, "lightness")     # Extract lightness
  img_inverted <- image_negate(img_channel)              # Invert lightness
  output_path <- file.path(output_folder, file)
  image_write(img_inverted, path = output_path, format = "jpg")
  message("Lightness-inverted: ", file)
}

