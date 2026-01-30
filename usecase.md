# Use Cases and Component Documentation

## IoT Sound Monitoring Project - Complete Resource Usage Guide

---

## Table of Contents

1. [Hardware Components & Their Usage](#1-hardware-components--their-usage)
2. [Software Components & Their Usage](#2-software-components--their-usage)
3. [Detailed Use Cases](#3-detailed-use-cases)
4. [Resource Utilization Analysis](#4-resource-utilization-analysis)
5. [Component Integration Flow](#5-component-integration-flow)

---

# 1. Hardware Components & Their Usage

## 1.1 Raspberry Pi (Model 3B or Higher)

### Specifications:
- **Model**: Raspberry Pi 3B+
- **CPU**: 1.4GHz 64-bit quad-core ARM Cortex-A53
- **RAM**: 1GB LPDDR2 SDRAM
- **Storage**: MicroSD card slot
- **Network**: Gigabit Ethernet, 802.11ac Wi-Fi, Bluetooth 4.2

### Usage in Project:
**Primary Role**: Central Processing Unit and Controller

**Specific Functions:**
1. **Audio Data Acquisition**
   - Interfaces with USB sound card via USB 2.0 port
   - Executes `arecord` commands to capture 1-second audio samples
   - Manages continuous recording loop at 1 Hz frequency

2. **Signal Processing**
   - Performs real-time RMS (Root Mean Square) calculations
   - Processes 16,000 samples per second
   - Calculates 80 RMS values per recording (200 samples each)
   - Computes 8 FastDB values (125ms intervals)

3. **Data Transmission**
   - Sends HTTP POST requests to web server
   - Uses libcurl for network communication
   - Transmits 8 data points per second via Ethernet

4. **System Control**
   - Runs Linux-based ALSA audio subsystem
   - Manages file system operations (WAV file creation)
   - Handles user interrupts (Ctrl+C signal processing)

### Resource Consumption:
- **CPU Usage**: 30-40% (single core during processing)
- **Memory**: ~150MB for application
- **Storage**: ~10MB for compiled code + ~1MB for temp WAV files
- **Power**: ~2.5W during operation

### Why This Component:
- ✓ Low cost ($35-45)
- ✓ Built-in Ethernet for reliable connectivity
- ✓ Sufficient processing power for real-time audio analysis
- ✓ Extensive Linux audio driver support
- ✓ GPIO pins available for future expansion
- ✓ Large community support

---

## 1.2 USB External Sound Card

### Specifications:
- **Type**: USB Audio Adapter (C-Media Electronics chipset)
- **Interface**: USB 2.0
- **Audio Format**: 16-bit/48kHz stereo
- **Inputs**: 3.5mm microphone jack
- **Outputs**: 3.5mm headphone jack

### Usage in Project:
**Primary Role**: Audio Interface and ADC (Analog-to-Digital Converter)

**Specific Functions:**
1. **Analog-to-Digital Conversion**
   - Converts analog microphone signals to digital format
   - Samples audio at 16,000 Hz (configurable)
   - 16-bit resolution per sample (S16_LE format)
   - Mono channel operation

2. **Audio Input Management**
   - Provides microphone bias voltage
   - Pre-amplification of microphone signal
   - Adjustable gain control (via alsamixer)
   - Noise filtering at hardware level

3. **USB Audio Class Compliance**
   - Plug-and-play operation with Linux
   - Standard USB Audio Class 1.0 protocol
   - No custom drivers required
   - Low latency audio capture (<10ms)

4. **Electrical Isolation**
   - Isolates audio circuitry from Raspberry Pi
   - Reduces electrical noise from Pi's power supply
   - Protects Pi from voltage spikes through microphone

### Resource Consumption:
- **USB Bus Bandwidth**: ~0.5 Mbps for mono audio
- **Power Draw**: ~100mA from USB port (0.5W)
- **Latency**: 5-8ms capture latency

### Why This Component:
- ✓ Better audio quality than Pi's onboard audio (which doesn't exist on Pi 3B)
- ✓ Proper microphone input with bias voltage
- ✓ Hardware-level ADC reduces CPU load
- ✓ Electrically isolated from Pi's digital circuits
- ✓ Standard USB Audio Class support
- ✓ Very low cost ($8-15)

---

## 1.3 Microphone (3.5mm Jack)

### Specifications:
- **Type**: Electret condenser microphone
- **Connector**: 3.5mm TRS (Tip-Ring-Sleeve) plug
- **Impedance**: ~2.2kΩ
- **Sensitivity**: -45dB ±3dB
- **Frequency Response**: 100Hz - 10kHz

### Usage in Project:
**Primary Role**: Acoustic Sensor and Sound Transducer

**Specific Functions:**
1. **Sound Wave Capture**
   - Converts acoustic pressure waves to electrical signals
   - Omnidirectional pickup pattern
   - Captures sound pressure levels from 30dB to 120dB SPL

2. **Signal Generation**
   - Produces analog voltage proportional to sound pressure
   - Typical output: 10-100mV AC signal
   - Requires phantom power from sound card

3. **Environmental Monitoring**
   - Continuous ambient sound sampling
   - All-frequency acoustic detection
   - No mechanical moving parts (solid-state)

### Resource Consumption:
- **Power**: ~0.5mA from sound card bias voltage
- **Bandwidth**: 20Hz - 16kHz audio spectrum

### Why This Component:
- ✓ Omnidirectional capture for general noise monitoring
- ✓ Wide dynamic range (30-120dB SPL)
- ✓ Standard 3.5mm connector for easy replacement
- ✓ Low power consumption
- ✓ No external power source required
- ✓ Inexpensive and readily available ($5-10)

---

## 1.4 RJ45 Ethernet Cable

### Specifications:
- **Standard**: Cat5e or Cat6
- **Connector**: RJ45 (8P8C)
- **Maximum Length**: 100 meters
- **Data Rate**: 100 Mbps (Cat5e) / 1 Gbps (Cat6)

### Usage in Project:
**Primary Role**: Network Connectivity

**Specific Functions:**
1. **Data Transmission**
   - Carries HTTP POST requests to web server
   - Transmits ~8 data points per second
   - Average payload: ~100 bytes per transmission

2. **Network Access**
   - Connects Pi to local network/router
   - Provides internet access for curl operations
   - Enables SSH access for remote management

3. **Power Over Ethernet (Optional)**
   - Can deliver power via PoE hat (future upgrade)
   - Eliminates need for separate power cable

### Resource Consumption:
- **Bandwidth Usage**: ~1 Kbps average (negligible)
- **Peak Bandwidth**: ~10 Kbps during transmission bursts

### Why This Component:
- ✓ More reliable than Wi-Fi for continuous operation
- ✓ Lower latency than wireless
- ✓ No authentication/reconnection issues
- ✓ Consistent connection quality
- ✓ Available PoE support for future expansion
- ✓ Simple plug-and-play setup

---

## 1.5 Power Supply (5V 2.5A Micro-USB)

### Specifications:
- **Voltage**: 5V DC ±5%
- **Current**: 2.5A (2500mA)
- **Connector**: Micro-USB Type B
- **Power**: 12.5W maximum

### Usage in Project:
**Primary Role**: System Power Source

**Specific Functions:**
1. **Pi Power Delivery**
   - Provides stable 5V to Raspberry Pi
   - Powers main SoC and peripherals
   - Supplies power to USB ports (for sound card)

2. **System Stability**
   - Prevents brownouts during peak CPU usage
   - Maintains voltage during USB device enumeration
   - Supports sustained operation 24/7

### Resource Consumption:
- **Idle Power**: ~1.5W
- **Active Power**: ~3-4W (with sound card)
- **Peak Power**: ~5W (during boot)

### Why This Component:
- ✓ Official/recommended power rating for Pi 3B+
- ✓ Sufficient current for USB peripherals
- ✓ Prevents undervoltage warnings
- ✓ Stable voltage for reliable SD card operation
- ✓ Can run continuously without overheating

---

## 1.6 MicroSD Card (16GB Class 10)

### Specifications:
- **Capacity**: 16GB (14.4GB usable)
- **Speed Class**: Class 10 (10 MB/s minimum write)
- **Type**: MicroSDHC
- **File System**: ext4 (Linux)

### Usage in Project:
**Primary Role**: Operating System and Storage

**Specific Functions:**
1. **OS Storage**
   - Hosts Raspbian/Raspberry Pi OS (~4GB)
   - Stores system files and kernel
   - Contains boot partition

2. **Application Storage**
   - Stores compiled executables (~5MB)
   - Holds source code files (~50KB)
   - Contains configuration files

3. **Temporary Data**
   - Writes temporary WAV files (~1MB cyclic)
   - System logs and cache
   - Swap space (if enabled)

4. **File System Operations**
   - Handles ~1 file write per second (test.wav)
   - Supports concurrent read/write operations
   - Manages wear leveling automatically

### Resource Consumption:
- **Write Operations**: ~1 MB/s during audio recording
- **Read Operations**: ~2 MB/s during playback/processing
- **Storage Usage**: ~6-8GB total (OS + apps + data)

### Why This Component:
- ✓ Sufficient capacity for OS + applications
- ✓ Class 10 speed prevents bottlenecks
- ✓ Standard size compatible with all Pi models
- ✓ Affordable and easily replaceable
- ✓ Wear leveling prevents early failure

---

## 1.7 Web Server (Physical/Cloud)

### Specifications:
- **Type**: Apache/Nginx on Linux, or cloud hosting
- **CPU**: 1-2 cores minimum
- **RAM**: 512MB minimum
- **Storage**: 10GB minimum
- **Network**: 10 Mbps upload/download

### Usage in Project:
**Primary Role**: Data Storage and Web Interface Hosting

**Specific Functions:**
1. **Data Reception**
   - Receives HTTP POST requests from Pi
   - Processes incoming sound data via PHP
   - Validates and sanitizes data inputs

2. **Data Storage**
   - Writes data to log files (sound_log.txt)
   - Updates JSON file (last_line.json)
   - Manages file permissions and access

3. **Web Hosting**
   - Serves HTML dashboard (sound.html)
   - Delivers JavaScript files (soundGraph.js)
   - Hosts Chart.js library

4. **API Endpoint**
   - Provides JSON data endpoint for web interface
   - Handles AJAX requests from browser
   - Implements CORS headers if needed

### Resource Consumption:
- **CPU**: <5% during normal operation
- **RAM**: ~100MB for web server process
- **Disk I/O**: ~10KB/s write operations
- **Network**: ~1 Kbps incoming, ~5-10 Kbps outgoing
- **Storage Growth**: ~50MB per month of logs

### Why This Component:
- ✓ Enables remote access from anywhere
- ✓ Centralized data storage
- ✓ Scalable to multiple Pi units
- ✓ Can use free cloud hosting services
- ✓ Persistent storage of historical data

---

# 2. Software Components & Their Usage

## 2.1 Raspbian OS (Raspberry Pi OS)

### Specifications:
- **Base**: Debian Linux (Bullseye/Bookworm)
- **Kernel**: Linux 5.x or 6.x
- **Architecture**: ARM 32-bit or 64-bit
- **Init System**: systemd

### Usage in Project:
**Primary Role**: Operating System and Runtime Environment

**Specific Functions:**
1. **System Management**
   - Manages hardware drivers (USB, Ethernet, GPIO)
   - Provides system services (networking, logging)
   - Handles process scheduling and memory management

2. **Audio Subsystem**
   - ALSA (Advanced Linux Sound Architecture) framework
   - Audio device enumeration and configuration
   - PCM (Pulse Code Modulation) audio streaming

3. **Development Environment**
   - GCC compiler toolchain
   - Make build automation
   - Standard C libraries (glibc)

4. **Network Stack**
   - TCP/IP implementation
   - HTTP client libraries
   - DNS resolution

### Why This Component:
- ✓ Official OS for Raspberry Pi
- ✓ Optimized for ARM architecture
- ✓ Complete audio subsystem (ALSA)
- ✓ Pre-installed development tools
- ✓ Large package repository (apt)
- ✓ Extensive documentation and community

---

## 2.2 ALSA (Advanced Linux Sound Architecture)

### Specifications:
- **Version**: 1.0.25 (as specified in project)
- **Package**: alsa-utils
- **Components**: libasound2, arecord, aplay, alsamixer

### Usage in Project:
**Primary Role**: Audio Capture Interface

**Specific Functions:**
1. **Audio Device Management**
   - Detects USB sound card
   - Configures audio device as default
   - Manages device routing (hw:1,0)

2. **Audio Recording** (arecord command)
   ```bash
   arecord -q -r16000 -c1 -f S16_LE -d1 test.wav
   ```
   - `-q`: Quiet mode (no output)
   - `-r16000`: Sample rate 16,000 Hz
   - `-c1`: Mono channel
   - `-f S16_LE`: Signed 16-bit Little Endian format
   - `-d1`: Duration 1 second
   - `test.wav`: Output filename

3. **Audio Configuration**
   - `/etc/asound.conf`: System-wide audio config
   - `~/.asoundrc`: User-specific audio config
   - Sets PCM device routing
   - Configures control device

4. **Mixer Control** (alsamixer)
   - Adjusts microphone gain
   - Controls input/output levels
   - Monitors audio levels visually

### Resource Usage:
- **Memory**: ~20MB for ALSA libraries
- **CPU**: ~5-10% during recording
- **Disk I/O**: 32 KB/s for 16kHz mono

### Why This Component:
- ✓ Standard Linux audio framework
- ✓ Low-level hardware access
- ✓ Precise control over audio parameters
- ✓ Efficient PCM data handling
- ✓ Command-line tools for automation
- ✓ WAV file format support built-in

---

## 2.3 libcurl (cURL Library)

### Specifications:
- **Version**: libcurl 7.x
- **Protocol Support**: HTTP, HTTPS, FTP, etc.
- **Dependencies**: OpenSSL, zlib
- **License**: MIT/X derivative

### Usage in Project:
**Primary Role**: HTTP Communication Client

**Specific Functions:**
1. **HTTP POST Requests**
   ```c
   curl_easy_setopt(curl, CURLOPT_URL, URL);
   curl_easy_setopt(curl, CURLOPT_POSTFIELDS, post);
   res = curl_easy_perform(curl);
   ```
   - Establishes TCP connection to server
   - Sends POST data with sound levels
   - Handles HTTP headers automatically

2. **Data Transmission**
   - Formats data as POST parameters
   - Encodes data in URL-safe format
   - Sends data string: `data=124.00;219.49;317.29;...`

3. **Error Handling**
   - Returns error codes (CURLcode)
   - Provides error descriptions
   - Handles network timeouts and failures

4. **Connection Management**
   - Initializes curl globally
   - Creates easy handle per request
   - Cleans up resources after transmission

### Resource Usage:
- **Memory**: ~2MB per curl instance
- **Network**: ~100 bytes per POST request
- **CPU**: <5% during transmission

### Why This Component:
- ✓ Industry-standard HTTP library
- ✓ Reliable and battle-tested
- ✓ Simple C API
- ✓ Handles all HTTP complexity
- ✓ Supports HTTPS for secure transmission
- ✓ Excellent error handling

---

## 2.4 C Programming Language & GCC Compiler

### Specifications:
- **Language**: C (C99/C11 standard)
- **Compiler**: GCC (GNU Compiler Collection) 8.x or higher
- **Linker**: GNU ld
- **Build Tool**: GNU Make

### Usage in Project:
**Primary Role**: Application Development and Compilation

**Specific Functions:**
1. **Source Code Files**
   - `main.c`: Program entry point and main loop
   - `sound.c`: Audio processing and RMS calculations
   - `sound.h`: Sound processing function declarations
   - `screen.c`: Console visualization functions
   - `screen.h`: Display function declarations
   - `comm.c`: Network communication functions
   - `comm.h`: Communication definitions and URL

2. **Compilation Process**
   ```makefile
   gcc -c main.c -o main.o
   gcc -c sound.c -o sound.o
   gcc -c screen.c -o screen.o
   gcc -c comm.c -o comm.o
   gcc main.o sound.o screen.o comm.o -lcurl -lm -o sound.a
   ```

3. **Libraries Linked**
   - `-lcurl`: Links libcurl for HTTP
   - `-lm`: Links math library for sqrt(), pow()

4. **Optimization**
   - `-O2` flag for performance optimization
   - Fast floating-point calculations
   - Efficient memory access patterns

### Why This Component:
- ✓ Direct hardware/system access
- ✓ Minimal overhead and fast execution
- ✓ Excellent math library support
- ✓ Standard on all Linux systems
- ✓ Efficient memory management
- ✓ Real-time processing capability

---

## 2.5 PHP (Server-Side Script)

### Specifications:
- **Version**: PHP 7.0 or higher
- **Server API**: Apache/Nginx module
- **Extensions**: None required (uses core functions)

### Usage in Project:
**Primary Role**: Server-Side Data Handler

**Specific Functions:**
1. **POST Data Reception** (sound.php)
   ```php
   if (isset($_POST['data'])) {
       $data = $_POST['data'];
       $today = date("Y-m-d H:i:s");
   }
   ```
   - Receives data from Pi via HTTP POST
   - Validates incoming data presence
   - Timestamps each data point

2. **File Writing**
   ```php
   $fp_log = fopen($log_file, "a");
   fwrite($fp_log, $str);
   fclose($fp_log);
   ```
   - Appends data to sound_log.txt
   - Creates files if they don't exist
   - Handles file permissions

3. **JSON Generation**
   ```php
   $last_line_file_content = array('data' => $data);
   fwrite($fp_json, json_encode($last_line_file_content));
   ```
   - Creates JSON object with latest data
   - Overwrites last_line.json
   - Formats for JavaScript consumption

4. **Error Handling**
   - Checks file existence
   - Validates write operations
   - Handles concurrent access

### Resource Usage:
- **Memory**: ~2MB per request
- **CPU**: <1% per request
- **Disk I/O**: ~500 bytes per write

### Why This Component:
- ✓ Universal web server support
- ✓ Simple POST data handling
- ✓ Built-in file operations
- ✓ Easy JSON encoding
- ✓ No database required
- ✓ Fast execution for simple tasks

---

## 2.6 HTML5 (sound.html)

### Specifications:
- **Version**: HTML5
- **DOCTYPE**: `<!DOCTYPE html>`
- **Character Set**: UTF-8

### Usage in Project:
**Primary Role**: Web Dashboard Structure

**Specific Functions:**
1. **Page Structure**
   ```html
   <canvas id="soundGraph" height="400" width="400"></canvas>
   ```
   - Defines canvas element for Chart.js
   - Sets dimensions for graph display
   - Provides mount point for visualization

2. **External Resources**
   ```html
   <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.4.0/Chart.js"></script>
   <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
   <script src="soundGraph.js"></script>
   ```
   - Loads Chart.js library from CDN
   - Loads jQuery for AJAX
   - Includes custom JavaScript

3. **Styling**
   ```css
   html, body {
       background-color: rgba(0, 0, 0, 0.1);
       width: 100%;
       height: 100%;
   }
   ```
   - Full-screen layout
   - Responsive design
   - Clean presentation

### Why This Component:
- ✓ Standard web markup language
- ✓ Canvas element for dynamic graphics
- ✓ Semantic structure
- ✓ Universal browser support
- ✓ Lightweight and fast loading

---

## 2.7 JavaScript & Chart.js (soundGraph.js)

### Specifications:
- **Language**: JavaScript (ES5/ES6)
- **Library**: Chart.js 2.4.0
- **Helper**: jQuery 3.3.1
- **Execution**: Client-side browser

### Usage in Project:
**Primary Role**: Real-Time Data Visualization

**Specific Functions:**
1. **AJAX Data Fetching**
   ```javascript
   $.ajax({
       url: 'last_line.json',
       type: 'GET',
       dataType: 'json',
       success: function(response) {
           // Process data
       }
   });
   ```
   - Polls server for latest JSON data
   - Periodic updates (e.g., every 1-2 seconds)
   - Asynchronous loading

2. **Data Parsing**
   ```javascript
   var dataString = response.data;
   var values = dataString.split(';');
   ```
   - Splits semicolon-separated values
   - Converts strings to numbers
   - Validates data format

3. **Chart Rendering**
   ```javascript
   var ctx = document.getElementById('soundGraph');
   var chart = new Chart(ctx, {
       type: 'line',
       data: chartData,
       options: chartOptions
   });
   ```
   - Creates line chart visualization
   - Updates chart data dynamically
   - Animates transitions

4. **Chart Configuration**
   - X-axis: Time intervals (125ms each)
   - Y-axis: Decibel values (dB)
   - Line color: Gradient or solid
   - Responsive scaling

### Resource Usage:
- **Memory**: ~10-20MB in browser
- **CPU**: <5% for chart updates
- **Network**: ~1-2 KB per data fetch

### Why This Component:
- ✓ Chart.js provides beautiful visualizations
- ✓ Real-time updates without page refresh
- ✓ Interactive charts (hover tooltips)
- ✓ Responsive design
- ✓ jQuery simplifies AJAX
- ✓ Cross-browser compatibility

---

## 2.8 JSON (Data Exchange Format)

### Specifications:
- **Format**: JSON (JavaScript Object Notation)
- **File**: last_line.json
- **Encoding**: UTF-8

### Usage in Project:
**Primary Role**: Structured Data Transfer

**Specific Functions:**
1. **Data Structure**
   ```json
   {
       "data": "124.00;219.49;317.29;412.53;389.72;256.49;134.82;192.13;"
   }
   ```
   - Key-value pair format
   - Single "data" field
   - Semicolon-separated values

2. **Data Exchange**
   - PHP writes JSON file
   - JavaScript reads JSON file
   - Browser parses JSON natively

3. **Update Mechanism**
   - File overwritten each second
   - Contains only latest 8 values
   - Small file size (~100 bytes)

### Why This Component:
- ✓ Lightweight data format
- ✓ Easy to parse in JavaScript
- ✓ Human-readable
- ✓ Standard web format
- ✓ PHP native support
- ✓ No parsing library needed

---

## 2.9 Make (Build Automation)

### Specifications:
- **Tool**: GNU Make
- **File**: makefile
- **Rules**: Compilation and linking

### Usage in Project:
**Primary Role**: Build Automation

**Specific Functions:**
1. **Compilation Rules**
   ```makefile
   CC = gcc
   CFLAGS = -Wall -O2
   LIBS = -lcurl -lm
   
   sound.a: main.o sound.o screen.o comm.o
       $(CC) $^ $(LIBS) -o $@
   
   %.o: %.c
       $(CC) $(CFLAGS) -c $< -o $@
   ```

2. **Build Process**
   - Compiles changed source files only
   - Links object files into executable
   - Manages dependencies automatically

3. **Clean Target**
   ```makefile
   clean:
       rm -f *.o sound.a test.wav
   ```
   - Removes compiled artifacts
   - Allows fresh rebuild

### Why This Component:
- ✓ Automates compilation process
- ✓ Incremental builds (fast recompilation)
- ✓ Dependency tracking
- ✓ Standard on all Linux systems
- ✓ Simple syntax
- ✓ Prevents manual errors

---

## 2.10 PuTTY (SSH Client - Windows)

### Specifications:
- **Platform**: Windows
- **Protocol**: SSH (Secure Shell)
- **Terminal**: VT100/xterm emulation

### Usage in Project:
**Primary Role**: Remote Access and Control

**Specific Functions:**
1. **SSH Connection**
   - Connects to Raspberry Pi remotely
   - Encrypted terminal session
   - Password or key authentication

2. **Terminal Emulation**
   - Provides command-line interface
   - Displays console output (bar charts)
   - UTF-8 character support (for UNICODE mode)

3. **File Transfer** (PSCP/PSFTP)
   - Upload source files to Pi
   - Download log files from Pi
   - Batch file transfers

4. **Session Management**
   - Save connection profiles
   - Configure terminal settings
   - Manage encryption options

### Why This Component:
- ✓ Standard SSH client for Windows
- ✓ Free and open-source
- ✓ Reliable and secure
- ✓ Easy configuration
- ✓ UTF-8 support for visualization
- ✓ No installation required (portable)

---

# 3. Detailed Use Cases

## Use Case 1: Industrial Safety Monitoring

### Scenario:
Manufacturing facility needs to monitor noise levels to comply with OSHA regulations (85 dB time-weighted average over 8 hours).

### Components Used:
- **Raspberry Pi**: Continuous monitoring and data logging
- **USB Sound Card**: Industrial-grade audio capture
- **Microphone**: Calibrated for 30-120 dB SPL range
- **Ethernet**: Reliable connection in industrial environment
- **Web Server**: Remote monitoring from safety office
- **ALSA**: Precise audio sampling at 16kHz

### Implementation:
1. Install multiple Pi units across factory floor
2. Configure alert thresholds in software
3. Log data to server for compliance reports
4. Display real-time levels on web dashboard
5. Generate weekly/monthly reports

### Benefits:
- Continuous 24/7 monitoring
- Automated compliance reporting
- Early warning of hazardous conditions
- Historical data for trend analysis
- Remote monitoring without floor visits

---

## Use Case 2: Urban Noise Pollution Mapping

### Scenario:
City government wants to create noise pollution heat map of downtown area to identify problem zones.

### Components Used:
- **Raspberry Pi**: Weatherproof enclosure deployment
- **Multiple Microphones**: Distributed sensor network
- **4G/LTE Modem**: Connectivity in outdoor locations
- **Cloud Server**: Centralized data aggregation
- **Database**: Store geo-tagged noise data
- **Mapping Software**: Generate heat maps

### Implementation:
1. Deploy 20-50 Pi units across city
2. Add GPS module for location data
3. Transmit data with coordinates
4. Aggregate data on cloud server
5. Generate heat maps using GIS software

### Benefits:
- Wide-area coverage
- Real-time pollution monitoring
- Data-driven urban planning
- Identify noise complaint hotspots
- Validate noise ordinance enforcement

---

## Use Case 3: Smart Home Baby Monitor

### Scenario:
Parents want to monitor nursery noise levels and receive alerts when baby cries.

### Components Used:
- **Raspberry Pi**: Compact installation in nursery
- **Sensitive Microphone**: Detects quiet sounds
- **Wi-Fi Dongle**: Wireless connectivity
- **MQTT Broker**: Smart home integration
- **Mobile App**: Push notifications
- **Pattern Recognition**: Cry detection algorithm

### Implementation:
1. Install Pi in nursery corner
2. Configure cry pattern detection
3. Integrate with Home Assistant
4. Send push notifications to phone
5. Log sleep patterns over time

### Benefits:
- Cost-effective vs. commercial monitors
- Integration with existing smart home
- Historical sleep data analysis
- Customizable alert thresholds
- No subscription fees

---

## Use Case 4: Educational Institution Library Monitoring

### Scenario:
University library wants to maintain quiet study zones by monitoring and displaying noise levels.

### Components Used:
- **Raspberry Pi**: Per-floor installation
- **Multiple Microphones**: Zone-specific monitoring
- **LED Display**: Visual feedback to students
- **Web Dashboard**: Librarian monitoring station
- **Email Alerts**: Notify staff of violations

### Implementation:
1. Install Pi in each quiet zone
2. Connect LED strip for visual feedback:
   - Green: <40 dB (Quiet)
   - Yellow: 40-60 dB (Moderate)
   - Red: >60 dB (Too Loud)
3. Log violations for analysis
4. Display statistics on library website

### Benefits:
- Visual feedback encourages compliance
- No staff intervention needed
- Data for facility planning
- Student awareness tool
- Improved study environment

---

## Use Case 5: Construction Site Compliance

### Scenario:
Construction company must comply with city noise ordinances (65 dB limit during daytime).

### Components Used:
- **Raspberry Pi**: Ruggedized outdoor enclosure
- **Weatherproof Microphone**: IP65 rated
- **Solar Panel + Battery**: Off-grid power
- **4G Modem**: Remote site connectivity
- **GPS**: Location and timestamp verification
- **Cloud Storage**: Legal compliance records

### Implementation:
1. Install at property boundary
2. Continuous monitoring during work hours
3. Automatic reports to city portal
4. SMS alerts when approaching limit
5. Downloadable compliance certificates

### Benefits:
- Avoid fines and citations
- Prove compliance with regulations
- Identify noisy equipment
- Improve community relations
- Legal documentation

---

## Use Case 6: Event Sound Management

### Scenario:
Outdoor concert venue must not exceed 95 dB at property line per city permit.

### Components Used:
- **Raspberry Pi**: Multiple boundary monitors
- **Professional Microphones**: Calibrated dB(A) measurement
- **Wireless Mesh Network**: Multi-unit coordination
- **Sound Engineer Dashboard**: Real-time feedback
- **Automated Limiter Control**: Reduce volume if needed

### Implementation:
1. Install monitors at property boundaries
2. Link to sound board control system
3. Display levels on engineer's iPad
4. Trigger warnings at 90 dB
5. Auto-reduce at 95 dB (optional)

### Benefits:
- Real-time compliance monitoring
- Prevent permit violations
- Protect venue license
- Audience safety
- Neighbor relations

---

# 4. Resource Utilization Analysis

## 4.1 CPU Utilization Breakdown

### Raspberry Pi CPU Usage:
```
Component                 CPU %    Core(s)   Notes
--------------------------------------------------
ALSA Audio Capture        10%      Core 1    During arecord execution
RMS Calculation          15%      Core 2    80 calculations per second
FastDB Calculation        5%      Core 2    8 calculations per second
File I/O (WAV)           3%      Core 3    1 write per second
Network (curl)           7%      Core 4    8 transmissions per second
System Overhead          5%      All       Linux kernel, drivers
--------------------------------------------------
TOTAL                    45%      Avg       Peak during processing
Idle                     20%      Avg       Between samples
```

### Optimization Opportunities:
- Use hardware floating-point (NEON SIMD)
- Reduce sample rate if less precision needed
- Batch network transmissions (every 5 seconds vs 1)
- Use RAM disk for temporary WAV files

---

## 4.2 Memory Utilization

### RAM Allocation:
```
Component                Memory      Type        Notes
--------------------------------------------------------
Operating System         400 MB      Shared      Base Raspbian
ALSA Libraries          20 MB       Shared      Audio subsystem
Application Code        5 MB        Private     sound.a executable
Audio Buffers           2 MB        Private     PCM data buffers
libcurl                 3 MB        Shared      HTTP client
Stack/Heap              10 MB       Private     Runtime variables
Filesystem Cache        100 MB      Shared      Disk cache
--------------------------------------------------------
TOTAL                   540 MB      Used        ~46% of 1GB RAM
Available               460 MB      Free        Sufficient headroom
```

---

## 4.3 Network Bandwidth

### Outbound Traffic (Pi → Server):
```
Data Type               Size        Frequency   Bandwidth
----------------------------------------------------------
FastDB Data             100 bytes   8/second    800 bytes/s
HTTP Headers            200 bytes   8/second    1.6 KB/s
TCP Overhead            40 bytes    8/second    320 bytes/s
----------------------------------------------------------
TOTAL                   340 bytes   8/second    ~2.7 KB/s
                                                ~22 Kbps
```

### Inbound Traffic (Server → Browser):
```
Data Type               Size        Frequency   Bandwidth
----------------------------------------------------------
JSON Data               150 bytes   1/second    150 bytes/s
HTML Page (initial)     5 KB        1/session   5 KB
Chart.js Library        150 KB      1/session   150 KB (cached)
soundGraph.js           10 KB       1/session   10 KB
----------------------------------------------------------
TOTAL (ongoing)         150 bytes   1/second    ~1.2 Kbps
```

### Total Network Load:
- **Uplink**: ~22 Kbps (negligible)
- **Downlink**: ~1.2 Kbps (negligible)
- **Monthly Data**: ~250 MB (Pi to server)

---

## 4.4 Storage Requirements

### Raspberry Pi:
```
Component                   Size        Growth      Notes
-----------------------------------------------------------
Operating System            4 GB        Static      Raspbian Lite
Installed Packages          500 MB      Static      ALSA, curl, gcc
Application Files           10 MB       Static      Compiled code
Temporary WAV Files         1 MB        Cyclic      Overwritten
System Logs                 50 MB       ~1 MB/week  Rotated logs
-----------------------------------------------------------
TOTAL                       4.5 GB      Minimal     12 GB free on 16GB card
```

### Web Server:
```
Component                   Size        Growth      Notes
-----------------------------------------------------------
Server OS                   5 GB        Static      Linux base
Web Server                  100 MB      Static      Apache/Nginx
PHP                         50 MB       Static      PHP runtime
Application Files           100 KB      Static      HTML/JS/PHP
sound_log.txt               0 KB        ~2 MB/day   Continuous growth
last_line.json              150 bytes   Static      Overwritten
-----------------------------------------------------------
TOTAL                       5.2 GB      ~60 MB/month Need log rotation
```

---

## 4.5 Power Consumption

### Raspberry Pi Power Draw:
```
State                   Current     Power       Percentage
-----------------------------------------------------------
Boot                    800 mA      4.0 W       100%
Idle (no USB)           350 mA      1.75 W      44%
USB Sound Card          +100 mA     +0.5 W      13%
Audio Recording         +200 mA     +1.0 W      25%
Network Transmission    +50 mA      +0.25 W     6%
-----------------------------------------------------------
Average Running         700 mA      3.5 W       88%
Peak Load               800 mA      4.0 W       100%
```

### Daily Energy:
- **24-hour operation**: 3.5W × 24h = 84 Wh = 0.084 kWh
- **Monthly**: 0.084 × 30 = 2.52 kWh
- **Cost**: ~$0.30/month (at $0.12/kWh)

### Battery Operation:
- **10,000 mAh power bank**: ~14 hours runtime
- **26,800 mAh (max airline carry-on)**: ~38 hours
- **12V 7Ah lead-acid + USB adapter**: ~50 hours

---

# 5. Component Integration Flow

## 5.1 Audio Capture Pipeline

```
┌──────────────┐
│ Microphone   │ Acoustic pressure waves (analog)
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Sound Card   │ ADC: Analog → Digital (16-bit samples @ 16kHz)
└──────┬───────┘
       │
       ▼ USB 2.0
┌──────────────┐
│ ALSA Driver  │ PCM audio stream to kernel buffer
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ arecord      │ Writes 1-second WAV file to disk
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ File System  │ test.wav (32,000 bytes + 44-byte header)
└──────────────┘
```

---

## 5.2 Data Processing Pipeline

```
┌──────────────┐
│ test.wav     │ WAV file on disk
└──────┬───────┘
       │
       ▼ fopen(), fread()
┌──────────────┐
│ sound.c      │ Read 16,000 samples (int16_t array)
└──────┬───────┘
       │
       ▼ Loop: 80 iterations
┌──────────────┐
│ RMS Calc     │ For each 200 samples: sqrt(Σ(sample²)/200)
└──────┬───────┘
       │
       ▼ 80 RMS values
┌──────────────┐
│ FastDB Calc  │ Group 10 RMS values → 1 FastDB
└──────┬───────┘
       │
       ▼ 8 FastDB values
┌──────────────┐
│ Visualization│ Console bar chart OR network transmission
└──────────────┘
```

---

## 5.3 Network Transmission Flow

```
┌──────────────┐
│ comm.c       │ 8 FastDB values in array
└──────┬───────┘
       │
       ▼ sprintf()
┌──────────────┐
│ POST String  │ "data=124.00;219.49;317.29;..."
└──────┬───────┘
       │
       ▼ libcurl
┌──────────────┐
│ HTTP POST    │ TCP connection to server:80
└──────┬───────┘
       │
       ▼ Network
┌──────────────┐
│ Apache/Nginx │ Receives POST request
└──────┬───────┘
       │
       ▼ PHP handler
┌──────────────┐
│ sound.php    │ Parses $_POST['data']
└──────┬───────┘
       │
       ├──────▼ fwrite()
       │  ┌──────────────┐
       │  │ sound_log.txt│ Append with timestamp
       │  └──────────────┘
       │
       └──────▼ json_encode()
          ┌──────────────┐
          │ last_line.json│ Overwrite with latest data
          └──────────────┘
```

---

## 5.4 Web Visualization Flow

```
┌──────────────┐
│ Browser      │ User opens http://server.com/sound.html
└──────┬───────┘
       │
       ▼ HTTP GET
┌──────────────┐
│ Web Server   │ Serves sound.html
└──────┬───────┘
       │
       ▼ HTML parsed
┌──────────────┐
│ Browser DOM  │ Loads Chart.js, jQuery, soundGraph.js
└──────┬───────┘
       │
       ▼ JavaScript executes
┌──────────────┐
│ setInterval  │ Every 1-2 seconds: AJAX request
└──────┬───────┘
       │
       ▼ $.ajax()
┌──────────────┐
│ last_line.json│ Fetches latest 8 values
└──────┬───────┘
       │
       ▼ JSON.parse()
┌──────────────┐
│ Data Array   │ [124.00, 219.49, 317.29, ...]
└──────┬───────┘
       │
       ▼ chart.update()
┌──────────────┐
│ Canvas       │ Chart.js renders line graph
└──────────────┘
```

---

## 5.5 Complete System Integration

```
                         ┌────────────────────────┐
                         │   Physical World       │
                         │   (Sound Waves)        │
                         └───────────┬────────────┘
                                     │
                                     ▼
                         ┌────────────────────────┐
                         │   Microphone           │
                         │   (Transducer)         │
                         └───────────┬────────────┘
                                     │
                                     ▼
                         ┌────────────────────────┐
                         │   USB Sound Card       │
                         │   (ADC)                │
                         └───────────┬────────────┘
                                     │
                    ┌────────────────┴────────────────┐
                    ▼                                 ▼
        ┌───────────────────┐             ┌──────────────────┐
        │  ALSA (Capture)   │             │  ALSA Config     │
        │  - arecord        │             │  - asound.conf   │
        └─────────┬─────────┘             └──────────────────┘
                  │
                  ▼
        ┌───────────────────┐
        │  WAV File         │
        │  - test.wav       │
        └─────────┬─────────┘
                  │
                  ▼
        ┌───────────────────┐
        │  sound.c          │
        │  - Read WAV       │
        │  - Calculate RMS  │
        │  - Calculate dB   │
        └─────────┬─────────┘
                  │
         ┌────────┴──────────┐
         ▼                   ▼
┌────────────────┐  ┌────────────────┐
│  screen.c      │  │  comm.c        │
│  - Console     │  │  - HTTP POST   │
│  - Bar Chart   │  │  - libcurl     │
└────────────────┘  └────────┬───────┘
                              │
                              ▼ Network (Ethernet)
                    ┌─────────────────┐
                    │  Web Server     │
                    │  - Apache/Nginx │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │  sound.php      │
                    │  - Receive POST │
                    │  - Write files  │
                    └────────┬────────┘
                             │
                    ┌────────┴────────┐
                    ▼                 ▼
         ┌──────────────────┐  ┌──────────────────┐
         │  sound_log.txt   │  │  last_line.json  │
         │  (History)       │  │  (Latest)        │
         └──────────────────┘  └──────────┬───────┘
                                           │
                                           ▼ AJAX (jQuery)
                              ┌────────────────────┐
                              │  soundGraph.js     │
                              │  - Fetch JSON      │
                              │  - Parse data      │
                              │  - Update chart    │
                              └────────┬───────────┘
                                       │
                                       ▼
                              ┌────────────────────┐
                              │  Chart.js          │
                              │  - Render graph    │
                              │  - Canvas element  │
                              └────────┬───────────┘
                                       │
                                       ▼
                              ┌────────────────────┐
                              │  Browser Display   │
                              │  (User sees graph) │
                              └────────────────────┘
```

---

## Summary

This document provides a comprehensive breakdown of every hardware and software component used in the IoT Sound Monitoring Project, detailing:

- **Purpose**: Why each component is needed
- **Function**: What role it plays in the system
- **Usage**: How it's specifically utilized
- **Specifications**: Technical details
- **Resource consumption**: CPU, memory, bandwidth, power
- **Integration**: How components work together

Each component has been selected to provide:
- ✓ Cost-effectiveness
- ✓ Reliability
- ✓ Ease of implementation
- ✓ Scalability
- ✓ Community support

The system demonstrates a complete IoT solution from edge sensing (Raspberry Pi + microphone) through cloud storage (web server) to user visualization (web dashboard).

---

**Document Version**: 1.0  
**Last Updated**: January 30, 2026  
**Status**: Complete
