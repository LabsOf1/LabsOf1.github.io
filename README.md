# LabsOf1.github.io

### PROJECT 1 Multi-Power Distribution Sensor-Based MUX DC Motor Controller

**Summary:**  
An electromechanical multiplexer system that controls multiple DC/AC motors using a single control circuit, reducing hardware complexity and increasing scalability in multi-axis platforms.

**Problem Solved:**  
Controlling many actuators typically requires separate channels or motor drivers. This system enables full motion control across X, Y, Z, and rotational axes using a shared PID-regulated distribution logic and a PLC-based switching mechanism.

**Key Features / Innovations:**
- Memory-mapped control of 5+ actuators using shared sensor inputs
- Integrated PID voltage regulation with safety cutoff during signal drift
- Sensor-triggered motion along all degrees of freedom with minimal user inputs
- Power-efficient: multiple actuators run off a single AC/DC conversion loop

**Technical Overview:**
- Allen-Bradley CompactLogix PLC
- Custom DC motor controller logic (SW1, SW2, xa–xe terminals)
- Feedback from 8 proximity sensors
- Full AC/DC converter: 220V AC to regulated 12V DC with ripple filtering
- Relay-based protection and PID voltage switch (Vs_diff_SW)

**Outcomes / Results:**
- Successfully demonstrated back-and-forth motion with logical switching
- Reduced control input count by 75% while expanding actuator control
- Built a scalable architecture for industrial or research robotic platforms

**Diagrams / Schematics and Technical Document:**  
📎 [Link to diagram or embedded image]  
📎 [Optional: Link to source code or ladder logic file]

**Next Steps / Potential Applications:**
- Modular extension for robotic arms, industrial conveyor platforms
- Real-time integration with distributed control systems (via Ethernet/IP)
- Applicable to low-cost automation systems in labs or factories
