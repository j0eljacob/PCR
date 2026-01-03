# Stepper Motor Module 

## Overview

The stepper motor module is a critical component of the PCR analyzer's optical detection system, enabling precise automated scanning of the optical head across 16 sample wells. This module eliminates the need for manual sample positioning and ensures consistent, repeatable fluorescence measurements across all wells.

## Purpose

The stepper motor assembly provides:
- **Automated Well Scanning**: Sequential positioning of the optical detection head over each of the 16 reaction wells
- **High Precision Movement**: Accurate positioning required for optimal optical alignment and signal detection
- **Multiplexing Support**: Enables 4-channel fluorescence detection across multiple samples without human intervention
- **Increased Throughput**: Allows simultaneous processing of 16 samples with 4 fluorescent dye channels (64 total detection points per run)

## Technical Specifications

### Motor Requirements
- **Type**: Bipolar stepper motor (recommended for precise control)
- **Step Angle**: 1.8° (200 steps per revolution) or finer
- **Holding Torque**: Sufficient for optical head assembly (~0.2-0.5 Nm)
- **Operating Voltage**: 12V DC (typical)
- **Current Rating**: 0.5-1.5A per phase

### Performance Characteristics
- **Positioning Accuracy**: ±0.1 mm
- **Movement Speed**: Optimized for balance between scan time and stability
- **Well-to-Well Transition Time**: < 2 seconds
- **Total Scan Time (16 wells)**: < 30 seconds

## System Architecture

```
STM32 Microcontroller
        ↓
   Motor Driver
        ↓
   Stepper Motor
        ↓
   Linear/Gantry System
        ↓
   Optical Head Assembly
```

## Hardware Components

### 1. Stepper Motor [Nema 17]
- Provides rotational motion converted to linear movement
- Microstepping capability for smoother operation
- Low vibration design to prevent signal interference

### 2. Motor Driver
- SR2 Nano
- Microstepping control (1/2, 1/4, 1/8, 1/16 step modes)
- Current limiting for motor protection
- Integrated thermal protection

### 3. Mechanical Assembly
- **Linear Rail System**: Guides the optical head along the well array
- **Belt/Lead Screw Drive**: Converts rotary motion to linear translation
- **Mounting Brackets**: Secure motor and rail system to analyzer frame
- **End Stops**: Limit switches for homing and position calibration

### 4. Position Sensing (Optional)
- Optical encoders for position feedback
- Hall effect sensors for home position detection
- Limit switches for safety boundaries

## Control System

### Microcontroller Interface
The stepper motor is controlled by the STM32 microcontroller through the following signals:

- **STEP**: Pulse signal to advance motor one step
- **DIR**: Direction control (forward/reverse)
- **ENABLE**: Motor enable/disable
- **MS1, MS2, MS3**: Microstepping mode selection (for drivers like A4988)

### Firmware Implementation
The control firmware (written in Embedded C) manages:
- Step pulse generation with precise timing
- Acceleration/deceleration profiles to prevent missed steps
- Position tracking and well indexing
- Homing sequence on system startup
- Error detection and recovery

## Operation Sequence

### 1. System Initialization
```
1. Power on motor driver
2. Execute homing sequence (move to reference position)
3. Reset position counter to well #1
4. Ready for scanning operations
```

### 2. Well Scanning Cycle
```
FOR each PCR cycle:
    FOR each well (1 to 16):
        1. Move optical head to well position
        2. Wait for mechanical settling (~100-200ms)
        3. Trigger fluorescence measurement
        4. Store data with well index
        5. Move to next well
    END FOR
    6. Return to home position (optional)
END FOR
```

### 3. Multi-Channel Detection
For each well position, the system performs:
1. LED 1 (FAM) excitation → Photodiode reading
2. LED 2 (HEX) excitation → Photodiode reading
3. LED 3 (ROX) excitation → Photodiode reading
4. LED 4 (Cy5) excitation → Photodiode reading

## Key Features

### Precision Positioning
- Microstepping reduces vibration and increases resolution
- Closed-loop control possible with encoder feedback
- Repeatable positioning across thousands of cycles

### Speed Optimization
- Acceleration profiles prevent motor stalling
- Optimized move distances reduce overall scan time
- Concurrent data processing during motor movement

### Reliability
- Minimal maintenance requirements
- Long operational lifetime (>100,000 cycles)
- Thermal management prevents overheating

## Integration with PCR System

The stepper motor module interfaces seamlessly with other subsystems:

### Thermal Cycling Module
- Scanning occurs between PCR cycles during temperature holds
- No movement during critical thermal transitions
- Synchronized with thermal profile timing

### Optical Detection Module
- Optical head carried by motor assembly
- Precise alignment maintained across all well positions
- Minimal optical path variation

### Data Acquisition System
- Position data synchronized with fluorescence readings
- Well identification embedded in data packets
- Real-time plotting on GUI with well labels

## Cost Analysis

| Component | Approximate Cost (₹) |
|-----------|---------------------|
| Stepper Motor | 1,500-3,000 |
| Motor Driver [SR2 Nano| 1,500-2,500 |
| Linear Rail & Carriage | 1,000-2,000 |
| Belt/Pulley or Lead Screw | 500-1,000 |
| Mounting Hardware | 300-500 |
| Limit Switches | 100-200 |
| **Total Module Cost** | **~₹4,900-9,200** |

This represents a significant cost saving compared to commercial scanning systems which can cost ₹50,000-₹1,00,000 or more.

## Advantages of This Design

1. **Cost-Effective**: Uses readily available COTS (Commercial Off-The-Shelf) components
2. **Modular**: Can be upgraded or replaced independently
3. **Scalable**: Design can be adapted for larger well arrays (24, 48, 96 wells)
4. **Automated**: Eliminates manual positioning errors
5. **Consistent**: Ensures uniform optical alignment across all samples
6. **Maintainable**: Simple mechanical design for easy servicing

## Future Enhancements

- **Dual-Axis Control**: 2D scanning for larger well plates
- **Faster Scanning**: Higher torque motors and optimized profiles
- **Position Feedback**: Closed-loop control with encoders
- **Multi-Head Design**: Parallel scanning of multiple wells simultaneously
- **Adaptive Positioning**: Auto-calibration for thermal expansion compensation

## Challenges and Solutions

### Challenge 1: Vibration Interference
**Problem**: Motor vibration affects optical measurements  
**Solution**: 
- Use microstepping mode
- Implement settling delay after positioning
- Isolate motor mechanically from optical components

### Challenge 2: Positioning Accuracy
**Problem**: Step loss or mechanical backlash  
**Solution**: 
- Closed-loop control with position sensors
- Quality mechanical components with minimal backlash
- Regular homing to reset accumulated errors

### Challenge 3: Speed vs. Accuracy Trade-off
**Problem**: Fast scanning may reduce positioning accuracy  
**Solution**: 
- Optimized acceleration profiles
- Different speed settings for move vs. measurement phases
- User-selectable scan modes (fast/standard/high-precision)

## Testing and Validation

### Positioning Accuracy Test
- Measure actual position vs. commanded position
- Verify repeatability across 100+ cycles
- Check for drift over extended operation

### Scan Time Optimization
- Benchmark full 16-well scan duration
- Optimize move profiles for minimum time
- Balance speed with measurement stability

### Reliability Testing
- Extended operation (1000+ cycles)
- Thermal cycling effects on positioning
- Vibration analysis during PCR operation

## Applications Beyond PCR

This stepper motor scanning system can be adapted for:
- **ELISA Readers**: Automated plate scanning for immunoassays
- **Microarray Scanners**: Fluorescence detection in genomic applications
- **Multi-Well Photometry**: Absorbance measurements in biochemistry
- **Automated Pipetting Systems**: Sample handling robotics
- **Cell Imaging**: Microscopy stage control



## Contributing

This module is part of an ongoing academic project focused on making molecular diagnostics accessible and affordable. Contributions, suggestions, and improvements are welcome.

## Acknowledgments

This stepper motor module is part of the "Development of a Low-Cost Real-Time PCR Analyzer" project, aimed at achieving 70-80% cost reduction compared to commercial systems while maintaining diagnostic-grade accuracy.



---

**Project Goal**: Democratize molecular diagnostics through affordable, reliable, and accessible PCR technology.

**Target Cost**: Complete system ≤ ₹5,00,000 (vs. ₹20-50 lakhs for commercial systems)

**Impact**: Enable PCR testing in small laboratories, educational institutions, rural healthcare facilities, and resource-limited settings worldwide.
