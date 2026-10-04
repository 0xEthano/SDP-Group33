SDP GitHub inital design ideas.

[Motivation](#motivation)
[Design Goals](#design-goals)
[Deliverables](#deliverables)
[System Blocks](#system-blocks)
[Hardware Requirements](#hardware-requirements)
[Software Requirements](#software-requirements)
[Team Member Responsibilities](#team-member-responsibilities)
[Project Timeline](#project-timeline)
[References](#references)

# Motivation

Improper waste sorting is a common problem in public and private waste disposal systems. Recyclable and compostable materials are often placed in general trash, while non-recyclable materials may contaminate recycling streams. Manual sorting is inefficient and impractical for small-scale applications.

The goal of this project is to develop a low-cost automated waste sorting prototype capable of identifying and routing common waste items with minimal user interaction. The system will use a Raspberry Pi, camera, sensors, and a conveyor mechanism to detect when an item has been deposited, capture an image of the item, classify it using an offline machine-learning model, and route it toward the appropriate waste category.

The project also provides an opportunity to combine embedded systems, computer vision, machine learning, sensor interfacing, PCB design, and electromechanical control in a single integrated system.

# Design Goals

The primary design goal is to produce a functional prototype that can automatically classify and sort deposited waste. The system should be inexpensive, relatively compact, and capable of operating without an Internet connection.

Specific design goals include:

- Detect when a waste item has entered the sorting system.
- Capture a clear image of the item under controlled lighting conditions.
- Perform image classification locally on a Raspberry Pi.
- Classify waste into at least two categories, with a target of three categories: recycling, compost, and general trash.
- Route the identified item into the appropriate collection bin using a conveyor-based mechanism.
- Minimize classification and sorting latency so that items can be processed individually without significant delay.
- Use inexpensive and readily available components where practical.
- Provide a basic local status/debugging interface through an LCD and/or connected display.
- Keep the software lightweight to maximize the Raspberry Pi's available processing resources.
- Allow the selected machine-learning model to potentially be retrained or fine-tuned if classification accuracy needs improvement.
- Use clean and reliable electrical connections, preferably with proper connectors and a PCB rather than large numbers of loose jumper wires.

A stretch goal is the addition of an automatically opening lid. This could use an ultrasonic proximity sensor and a low-cost motor or servo to detect a user's hand and open the trash receptacle.

# Deliverables

The main project deliverable will be a working concept prototype demonstrating automated waste detection, classification, and sorting.

Supporting deliverables are expected to include:

- Functional automated waste-sorting prototype.
- Raspberry Pi-based image classification software.
- Conveyor and sensor control software.
- Hardware interconnection and system block diagrams.
- Software architecture or software-flow diagram.
- Bill of materials.
- PCB or structured interconnection hardware as required by the final design.
- Basic testing and demonstration of waste classification and routing.
- Documentation describing the major hardware and software components.

Extensive production-level documentation is not currently considered necessary. The emphasis will be placed on demonstrating a reliable proof of concept.

# System Blocks

The system can be divided into the following major functional blocks:

**1. User / Waste Input**

The user places a waste item into the trash receptacle. An optional future implementation may include an ultrasonic proximity sensor and motorized lid to automatically open the receptacle.

**2. Item Detection**

The deposited item lands on or enters the conveyor system. A break-beam sensor detects the presence of the object and signals the Raspberry Pi that an item is ready for classification.

**3. Image Capture**

An Arducam IMX219 USB/UVC camera captures an image of the object. White LED lighting and a diffuser provide more consistent illumination, reducing variation caused by ambient lighting.

**4. Image Classification**

The Raspberry Pi processes the captured image using a pretrained machine-learning image-classification model operating locally. The software determines whether the detected object belongs to the recycling, compost, or general-trash category.

The exact machine-learning framework and model have not yet been selected. The preferred solution will be sufficiently lightweight to execute on the Raspberry Pi and ideally allow later retraining or fine-tuning.

**5. Sorting Control**

Based on the classification result, the Raspberry Pi controls the conveyor system and associated motors or actuators.

The final three-way sorting mechanism has not yet been finalized. One proposed design uses a primary conveyor capable of operating in both directions, with a secondary conveyor or diversion mechanism installed at one end to create a third sorting path.

If three-way mechanical sorting proves impractical within the project constraints, compost and general trash may be grouped together, resulting in a two-category sorting system.

**6. Collection Bins**

Sorted items are deposited into separate smaller collection bins corresponding to the implemented waste categories.

**7. User / Debug Interface**

A 1602 I²C LCD may be used to display system state, classification results, diagnostic information, or error messages. A monitor connected through micro-HDMI may also be used during development and testing.

A simplified system block diagram would follow the sequence:

Waste Input → Break-Beam Sensor → Camera → Raspberry Pi → Image Classification → Motor/Conveyor Control → Sorting Path → Collection Bin

Additional supporting blocks include the lighting system, power system, LCD/debug interface, and optional automatic lid mechanism.

# Hardware Requirements

The current hardware design is centered around a Raspberry Pi 5 with 1 GB or 2 GB of RAM, with the 2 GB version currently being considered.

Expected hardware includes:

- Raspberry Pi 5, approximately 2 GB RAM.
- microSD card for operating system, application software, and local storage.
- Arducam IMX219 UVC-compatible camera.
- One or more break-beam sensors for object detection and conveyor position sensing.
- Existing conveyor belt system or an inexpensive commercially available replacement.
- Conveyor motor and appropriate motor driver.
- Additional conveyor, diverter, servo, or motor mechanism if required for three-category sorting.
- White LED illumination.
- Light diffuser for consistent imaging conditions.
- 1602 I²C LCD for status and debugging.
- BSS138-based logic-level converter where required for safe peripheral interfacing.
- HDMI or micro-HDMI display connection for development.
- Raspberry Pi power supply or battery.
- Separate motor power supply if required.
- PCB and connectors for reliable electrical integration.
- Trash receptacle and smaller destination bins.

The current estimated component cost is approximately $205.50 before tax, excluding some mechanical components and any parts that are already available to the team.

The power architecture will require particular attention. Motors should preferably be powered separately from the Raspberry Pi because motor startup and stall currents may cause voltage drops, electrical noise, or system instability. Grounds may still need to be referenced appropriately depending on the motor-control interface.

# Software Requirements

The Raspberry Pi will run a lightweight Linux-based operating system. A headless configuration is preferred because a full graphical desktop environment is unnecessary during normal operation and would consume additional memory and processing resources.

The software system will require the following capabilities:

- Interface with GPIO-connected sensors.
- Detect a waste item using a break-beam or similar sensor.
- Capture images from the USB/UVC camera.
- Preprocess images as required by the classification model.
- Run a pretrained image-classification model entirely offline.
- Categorize items into the selected waste classes.
- Control conveyor motors and/or actuators based on classification results.
- Display system status or results on the LCD.
- Provide logging and debugging information during development.
- Recover from invalid sensor readings, failed image captures, or uncertain classification results.

The exact machine-learning framework remains to be selected. Candidate solutions should prioritize low memory consumption, reasonable inference speed, compatibility with ARM-based Raspberry Pi hardware, and support for pretrained models. The ability to retrain or fine-tune the selected model would be beneficial.

A simplified software sequence is expected to be:

1. Initialize sensors, camera, model, and motor controller.
2. Wait for an item to be detected.
3. Stop or position the item for image capture if necessary.
4. Capture an image.
5. Run image preprocessing.
6. Perform machine-learning inference.
7. Determine the waste category.
8. Activate the appropriate conveyor or sorting mechanism.
9. Confirm that the item has cleared the sorting area.
10. Return the system to the idle state.

# Team Member Responsibilities

**Avaneesh**
- Raspberry Pi software setup.
- Machine-learning and image-classification development.
- Model selection, testing, and integration.

**Oisin**
- Raspberry Pi software setup.
- Machine-learning and image-classification development.
- Model selection, testing, and integration.

**Robert**
- Hardware design.
- PCB design.
- Sensor, motor-control, and electrical integration.

**Paul**
- Hardware design.
- PCB design.
- Sensor, motor-control, and electrical integration.

**Jeff**
- Component and part selection.
- Raspberry Pi operating-system setup.
- Software development support.
- PCB design and hardware integration support.

**Will**
- Component and part selection.
- Software development and integration support.

Although primary responsibilities are divided among team members, hardware and software integration will require collaboration across the entire team, particularly during prototype assembly and system testing.

# Project Timeline

The current goal is to have a functional concept prototype completed by approximately December 1.

**Early October — System Planning and Component Selection**

- Finalize overall system architecture.
- Select Raspberry Pi configuration.
- Confirm camera and sensor selections.
- Determine available conveyor hardware.
- Begin ordering required components.
- Research appropriate pretrained image-classification models.

**Mid October — Initial Hardware and Software Development**

- Configure Raspberry Pi operating system.
- Establish camera functionality.
- Test break-beam sensors.
- Test GPIO and motor-control interfaces.
- Begin evaluating pretrained classification models.
- Establish basic image-capture and inference pipeline.

**Late October — Subsystem Prototyping**

- Integrate camera, lighting, and object-detection sensor.
- Test waste-image classification.
- Characterize model accuracy and inference speed.
- Interface Raspberry Pi with conveyor motor hardware.
- Determine final two-way or three-way sorting mechanism.

**Early November — Full System Integration**

- Combine item detection, image capture, classification, and conveyor control.
- Build or finalize electrical interconnections.
- Begin PCB design if a PCB is used.
- Implement LCD/debug output.
- Test repeated sorting cycles.

**Mid November — Refinement and Testing**

- Improve classification reliability.
- Tune lighting and camera positioning.
- Resolve conveyor timing or mechanical issues.
- Complete PCB or wiring integration.
- Test multiple waste categories and object types.
- Implement error handling and uncertain-classification behavior.

**Late November — Final Prototype Preparation**

- Assemble the complete prototype.
- Perform end-to-end testing.
- Correct remaining software and hardware issues.
- Finalize block diagrams and bill of materials.
- Prepare system demonstration.

**Approximately December 1 — Concept Prototype**

- Demonstrate functional waste detection, image classification, and automated sorting.

Intermediate dates may be adjusted as official senior-design deadlines are established.

# References

The following sources are useful starting references for the project:

1. Raspberry Pi Ltd., **Raspberry Pi 5 Documentation**  
   https://www.raspberrypi.com/documentation/computers/raspberry-pi.html

2. Raspberry Pi Ltd., **Raspberry Pi GPIO Documentation**  
   https://www.raspberrypi.com/documentation/computers/raspberry-pi.html

3. Arducam, **Camera Modules and USB Camera Documentation**  
   https://docs.arducam.com/

4. TensorFlow, **TensorFlow Lite / TensorFlow Lite for Microcontrollers Documentation**  
   https://www.tensorflow.org/lite

5. PyTorch, **PyTorch Documentation**  
   https://pytorch.org/docs/

6. ONNX Runtime, **ONNX Runtime Documentation**  
   https://onnxruntime.ai/docs/

7. Adafruit Industries, **IR Breakbeam Sensors**  
   https://learn.adafruit.com/ir-breakbeam-sensors

Additional references will be added once the final image-classification model, motor controller, sensors, and sorting mechanism are selected.