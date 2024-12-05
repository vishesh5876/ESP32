# ESP32 Camera Web Server

This repository contains the code and configurations for setting up an ESP32-based camera web server. The server streams live video, captures images on demand, and provides a web interface to control the camera and adjust settings like resolution, brightness, and more. It also supports optional features like face detection and LED flash control.

## Table of Contents

1. [CameraWebServer.ino](#camerawebserverino)
2. [app_httpd.cpp](#app_httpdcpp)
3. [partitions.csv](#partitionscsv)
4. [ci.json](#cijson)
5. [camera_pins.h](#camerapinsh)
6. [camera_index.h](#cameraindexh)
7. [Overall Functionality](#overall-functionality)
8. [Key Points](#key-points)

---

## CameraWebServer.ino

### Purpose:
This is the main Arduino sketch that initializes the camera, connects to Wi-Fi, and starts the web server.

### Key Sections:
- **Includes and Definitions:**
  - Includes libraries like `esp_camera.h` and `WiFi.h` for camera functions and Wi-Fi connectivity.
  - Defines the camera model (e.g., `CAMERA_MODEL_ESP_EYE`) to set up appropriate pin mappings in `camera_pins.h`.

- **Wi-Fi Credentials:**
  - The `ssid` and `password` variables store the Wi-Fi network credentials for connecting the ESP32.

- **Camera Configuration:**
  - Initializes a `camera_config_t` structure with settings like:
    - **Pin Assignments**: Maps camera pins to ESP32 GPIO pins.
    - **Camera Settings**: Configures parameters like resolution and image format.
    - **PSRAM Optimization**: Adjusts settings based on the presence of PSRAM for improved performance.

- **Camera Initialization:**
  - Calls `esp_camera_init(&config)` to initialize the camera with the configured settings.
  
- **LED Flash Setup:**
  - If an LED flash is defined, sets up the flash functionality for image capture.

- **Wi-Fi Connection:**
  - Connects to the Wi-Fi network and disables Wi-Fi sleep mode for better streaming performance.

- **Starting the Web Server:**
  - Initializes and starts the HTTP server that handles incoming requests for streaming and image capture.

### Main Functions:
- **`loop()`**: The loop function remains mostly empty, as the web server handles the main tasks.

---

## app_httpd.cpp

### Purpose:
Implements the HTTP server and handles incoming HTTP requests for streaming, capturing images, and adjusting camera settings.

### Key Sections:
- **Includes and Macros:**
  - Includes headers for HTTP server functionality, camera operations, and face detection (if enabled).
  
- **URI Handlers Registration:**
  - Registers various URI handlers:
    - `/cmd`: Adjusts camera settings.
    - `/status`: Returns the current camera status and configuration.
    - `/capture`: Captures a single image and sends it to the client.
    - `/stream`: Streams video frames continuously.

- **Streaming and Capture Functionality:**
  - Handles image capture and streaming by managing frame buffers and formatting data for HTTP transmission.

- **LED Flash Control:**
  - If enabled, controls the LED flash during image capture and streaming.

- **Conditional Compilation for Face Detection:**
  - Includes face detection features based on the configuration (requires sufficient memory and ESP32S3 support).

---

## partitions.csv

### Purpose:
Defines the partition scheme for the ESP32's flash memory.

### Key Sections:
- **Partition Entries**:
  - `nvs`: Non-volatile storage.
  - `otadata`: For over-the-air updates.
  - `app0`: Main application partition.
  - `fr`: Likely used for storing face recognition data.
  - `coredump`: Used for storing crash dumps.

- **Offsets and Sizes:**
  - Specifies memory offsets and sizes, ensuring that enough space is allocated for the application and additional data.

---

## ci.json

### Purpose:
Configuration file for Continuous Integration (CI), specifying build configurations for different board variants.

### Key Sections:
- **fqbn (Fully Qualified Board Names):**
  - Lists configurations for ESP32, ESP32-S2, and ESP32-S3 variants, including PSRAM usage and partition schemes.
  
- **Build Requirements:**
  - Ensures the build configuration sets `CONFIG_CAMERA_TASK_STACK_SIZE` appropriately.

---

## camera_pins.h

### Purpose:
Defines the pin mappings between the ESP32 and the camera module based on the selected camera model.

### Notes:
- This file is typically used to define `#define` statements for camera signal pin mappings (e.g., `Y2_GPIO_NUM`, `PCLK_GPIO_NUM`).
- The pin mappings depend on the camera model selected in `CameraWebServer.ino`.

---

## camera_index.h

### Purpose:
Contains definitions related to the web interface for the camera server.

### Key Sections:
- **HTML Resource Definitions:**
  - Defines compressed HTML file sizes (e.g., `index_ov2640.html.gz`) for different camera models.
  - These files are served to clients as web pages for camera control and streaming.

---

## Overall Functionality

The code sets up an ESP32-based camera module to act as a web server, which allows users to:

- **Video Streaming**: Access live video via a web browser by navigating to the ESP32’s IP address.
- **Image Capture**: Capture high-resolution images on demand.
- **Camera Control**: Adjust camera settings like resolution, brightness, contrast, and more.
- **LED Flash**: Control an LED flash if the hardware supports it.
- **Face Detection (Optional)**: Perform face detection and recognition if supported by the hardware and enabled in the configuration.

---

## Key Points

### Hardware Requirements:
- **PSRAM**: Recommended for higher resolutions and advanced features like face detection due to increased memory requirements.
- **Camera Model and Board Variant**: Must be correctly defined to ensure proper functionality and pin mappings.

### Memory Management:
- The partition scheme allocates sufficient space for the application and additional data (e.g., face recognition models).
- JPEG quality and frame buffers are adjusted based on available memory.

### Customization:
- The code is modular and uses conditional compilation to include or exclude features.
- Users can adjust configurations in `CameraWebServer.ino` and `app_httpd.cpp` to suit their specific needs.
