# 🤖 Voice-Controlled KUKA Robot with LLM Planning

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![PyBullet](https://img.shields.io/badge/Simulation-PyBullet-orange)
![AI](https://img.shields.io/badge/GenAI-Ollama%20%7C%20Whisper-green)
![Hardware](https://img.shields.io/badge/Hardware-RTX%204070-red)

## 📖 Overview

This project implements an **end-to-end voice control pipeline** for a KUKA iiwa robotic manipulator. Unlike traditional voice control systems that rely on hard-coded keywords, this system utilizes a **Large Language Model (Mistral via Ollama)** to interpret natural language, understand intent, and generate structured mission plans (JSON) for the robot to execute in a physics-based simulation (PyBullet).

The entire pipeline—Speech Recognition, Reasoning, and Simulation—runs **locally** (Offline) for privacy and low latency, utilizing GPU acceleration.

### 🎯 Key Features
* **Natural Language Understanding:** The robot understands complex commands like *"Grab the red box and put it in the drop zone"* rather than just fixed keywords.
* **Local Inference:** Uses **OpenAI Whisper (Turbo)** for ASR and **Mistral** for logic, running entirely on local hardware (tested on Intel i9 + RTX 4070).
* **Modular Architecture:** Decoupled "Ear" (Sensors), "Brain" (Planner), and "Body" (Actuator) modules.
* **Physics Simulation:** Implements a magnetic gripper logic using PyBullet constraints to handle object manipulation reliability.

1) The Ear: Captures audio via SpeechRecognition, pre-processes signal (noise reduction), and transcribes using Whisper.

2) The Brain: The transcript is fed into Mistral with a strict System Prompt. The LLM converts unstructured text into a structured JSON list of actions (e.g., move_to, magnet_on).

3) The Body: A Python controller parses the JSON, calculates Inverse Kinematics (IK) for the KUKA arm, and handles physics constraints.

---

## 🏗️ System Architecture

The data flow follows a strictly modular **Perception-Cognition-Action** loop:

```mermaid
graph LR
    A[User Voice] -->|Audio| B(Whisper ASR)
    B -->|Text| C{Ollama / Mistral}
    C -->|JSON Plan| D[Robot Controller]
    D -->|Inverse Kinematics| E[PyBullet KUKA Sim]
