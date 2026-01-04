# 🤖 Voice-Controlled KUKA Robot with LLM Planning

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![PyBullet](https://img.shields.io/badge/Simulation-PyBullet-orange)
![AI](https://img.shields.io/badge/GenAI-Ollama%20%7C%20Whisper-green)
![Hardware](https://img.shields.io/badge/Hardware-RTX%204070-red)

## 📖 Overview

This project implements an **end-to-end voice control pipeline** for a KUKA iiwa robotic manipulator. Unlike traditional voice control systems that rely on hard-coded keywords, this system utilizes a **Large Language Model (Mistral via Ollama)** to interpret natural language, understand intent, and generate structured mission plans (JSON) for the robot to execute in a physics-based simulation (PyBullet).

The entire pipeline—Speech Recognition, Reasoning, and Simulation—runs **locally** (Offline) for privacy and low latency, utilizing GPU acceleration.

### 🎯 Key Features
1) **Natural Language Understanding:** The robot understands complex commands like *"Grab the red box and put it in the drop zone"* rather than just fixed keywords.
2) **Local Inference:** Uses **OpenAI Whisper (Turbo)** for ASR and **Mistral** for logic, running entirely on local hardware (tested on Intel i9 + RTX 4070).
3) **Modular Architecture:** Decoupled "Ear" (Sensors), "Brain" (Planner), and "Body" (Actuator) modules.
4) **Physics Simulation:** Implements a magnetic gripper logic using PyBullet constraints to handle object manipulation reliability.

### 🛠️ Tech Stack
* **Language:** Python 3.12
* **Simulation:** PyBullet (Physics engine)
* **ASR (Speech-to-Text):** OpenAI Whisper (turbo model)
* **LLM (Logic):** Ollama running mistral
* **Libraries:** speech_recognition, numpy, torch, msvcrt

---

### 🏗️ System Architecture

The data flow follows a strictly modular **Perception-Cognition-Action** loop:

```mermaid
graph LR
    A[User Voice] -->|Audio| B(Whisper ASR)
    B -->|Text| C{Ollama / Mistral}
    C -->|JSON Plan| D[Robot Controller]
    D -->|Inverse Kinematics| E[PyBullet KUKA Sim]
```

1) The Ear: Captures audio via SpeechRecognition, pre-processes signal (noise reduction), and transcribes using Whisper.
2) The Brain: The transcript is fed into Mistral with a strict System Prompt. The LLM converts unstructured text into a structured JSON list of actions (e.g., move_to, magnet_on).
3) The Body: A Python controller parses the JSON, calculates Inverse Kinematics (IK) for the KUKA arm, and handles physics constraints.

### 🚀 Installation & Setup
**1. Prerequisites**
Ensure you have Python installed and an NVIDIA GPU (recommended for Whisper/Ollama speed).

**2. Install Dependencies**
pip install pybullet numpy torch openai-whisper speechrecognition ollama
(Note: You may need pyaudio for microphone access. On Windows: pip install pipwin && pipwin install pyaudio)

**3. Setup Ollama (The Brain)**
1) Download and install
    [Ollama](https://ollama.com/).

2) Pull the Mistral model:
   ```
    ollama pull mistral
    ```
4) Start the server (keep this terminal open):
   ```
    ollama serve
    ```
### 🕹️ Usage
1) Run the main script: python main.py
2) The PyBullet GUI will launch.
3) Press ENTER in the console to trigger the listening mode.
4) Speak a command:
    "Pick up the blue box."
5) Watch the robot plan and execute the task!

### 🧠 Logic Sample
**Input: "Grab the green one and drop it off."**

**LLM Generated Plan: JSON**
```json
[
  { "action": "move_to", "target": "green_box" },
  { "action": "magnet", "state": "on" },
  { "action": "move_to", "target": "drop_zone" },
  { "action": "magnet", "state": "off" }
]
```
### 📸 VISUAL IDEA
<img width="764" height="590" alt="Screenshot 2026-01-04 171929" src="https://github.com/user-attachments/assets/3488975a-fdd6-4a66-8b34-7c2de1f6ae3c" />
<img width="766" height="590" alt="Screenshot 2026-01-04 182009" src="https://github.com/user-attachments/assets/bf95386f-5c9e-4bfc-93aa-2aaa52a389bb" />
<img width="765" height="590" alt="Screenshot 2026-01-04 191724" src="https://github.com/user-attachments/assets/e5d33d44-ca9c-4cc9-b584-35481d2b788f" />

### 👤 Author
**Jai Doshi (JD)**  
Masters Student: Mechatronics [@RWU GERMANY](https://www.rwu.de/en)

