# README: Robot Control with Whisper and PyBullet

This project integrates **Whisper**, an automatic speech recognition (ASR) model by OpenAI, with **PyBullet**, a physics simulation engine, to control a robotic arm (Kuka IIWA) in a simulated environment. The robot responds to spoken commands, plans its actions based on a language model (Mistral), and executes them in a physics-based simulation.

## Features

- **Voice Commands**: Use natural language to control the robot, e.g., "Robot, move forward one meter."
- **Physics Simulation**: The robot operates within a simulated 3D environment using PyBullet.
- **Object Handling**: The robot can pick up and move objects like "red_box", "green_box", and "blue_box" using a magnet.
- **Action Planning**: The system uses a language model (e.g., Mistral) to interpret commands and create a sequence of actions (e.g., move, magnet control).

## Requirements

### Hardware:
- **GPU** (optional but recommended for fast model inference): Requires CUDA-enabled GPU for Whisper inference.

### Software:
- **Python 3.8+**
- **Libraries**:
  - `torch`: PyTorch library for deep learning.
  - `whisper`: OpenAI's Whisper model for automatic speech recognition.
  - `speech_recognition`: For capturing microphone input.
  - `pybullet`: Physics simulation library to control the robot.
  - `numpy`: Numerical computations (used in physics simulations).
  - `ollama`: A wrapper for large language models (LLMs).
  - `msvcrt`: For handling keyboard input (Windows only).
  - `pybullet_data`: To access URDF files and other simulation assets.

You can install the required libraries using pip:

```bash
pip install torch numpy whisper speechrecognition pybullet ollama
```

### Model Files:
- Whisper model (`small` or `base` model recommended) for speech recognition.
- Mistral model for natural language processing (text-based control).

## Setup and Execution

### 1. **Loading the Whisper Model**

The `whisper` library is used to transcribe spoken commands. The model is loaded into memory at the start of the script.

### 2. **Setting up the PyBullet Environment**

The PyBullet environment is initialized by loading a table, robot (Kuka IIWA), and objects (red_box, green_box, blue_box). These objects are used for interaction with the robot.

- **URDF Files**: The robot and objects are defined in URDF format (Universal Robot Description Format).
- **Initial Position**: The robot is placed in the "home" position at the start of the simulation.

### 3. **Physics Thread**

A background thread runs the PyBullet physics engine, which is responsible for simulating gravity and other forces in real-time.

### 4. **Voice Command Input**

Once the setup is complete, the robot waits for a voice command. The `audio_by_user()` function listens for speech input via the microphone, transcribes it using the Whisper model, and sends the text to a language model (Mistral) for action planning.

### 5. **Action Planning**

The language model processes the user’s command and generates a JSON plan of actions (e.g., moving to a location, toggling the magnet on/off). The generated plan is then executed step-by-step by the robot.

Example command and generated plan:
- Command: `"Grab the red box"`
- Plan: 
    ```json
    [
      {"action": "move_to", "target": "red_box"},
      {"action": "magnet", "state": "on"},
      {"action": "move_to", "target": "home"}
    ]
    ```

### 6. **Executing Actions**

Each action in the plan is executed sequentially:
- **Movement**: The robot uses inverse kinematics to reach the target position.
- **Magnet Control**: The robot toggles its magnet state to pick up or drop objects.

### 7. **End Program**

The program continues to listen for commands until the user types "exit" or interrupts the program (Ctrl+C).

## Running the Program

To run the program, simply execute the Python script:

```bash
python robot_control.py
```

### Interacting with the Robot

1. **Voice Command**: The robot will prompt you to say something like "Robot, move forward one meter."
2. **Command Processing**: The system transcribes the command, processes it, and generates a plan.
3. **Action Execution**: The robot will move according to the plan.

To stop the program, type **"exit"** when prompted or press **Ctrl+C**.

## File Breakdown

- **`audio_by_user()`**: Captures and transcribes user speech using the Whisper model.
- **`get_mission_plan()`**: Sends the user input to the Mistral model and generates a list of actions.
- **`setup_world()`**: Initializes the PyBullet simulation environment (robot, objects, and locations).
- **`move_arm()`**: Moves the robot arm to a specific position using inverse kinematics.
- **`toggle_magnet()`**: Toggles the state of the robot's magnet to pick up or drop objects.
- **`physics_thread()`**: A background thread running the PyBullet physics simulation.
- **`spawn_box()`**: Helper function to spawn boxes in the simulation.
- **Main Execution Block**: Coordinates the setup, listening for voice commands, and executing the generated action plan.

## Example Interaction

```plaintext
--- 1. Loading Whisper Model ---
--- 2. Setting up PyBullet ---
[USER] >>> Press ENTER to start listening... (or Ctrl+C to exit)

--- RECORDING ---
Say: 'Robot, move forward one meter.'
Listening...

Whisper Result: "Robot, move forward one meter."
[BRAIN] Processing logic with mistral...
[BRAIN] Raw Model Output: [{"action": "move_to", "target": "home"}]
[PLAN] Executing 1 steps...
[ROBOT] Moving to [0.3, 0, 0.6]...
[SYSTEM] Execution finished.
```

## Troubleshooting

- **Voice Recognition Issues**: Ensure your microphone is working properly. If necessary, increase the `timeout` or `phrase_time_limit` in the `recognizer.listen()` function.
- **PyBullet Errors**: If the robot or objects aren't appearing in the simulation, verify that the URDF files are correctly loaded and that PyBullet is connected properly.
- **Memory Issues**: If running out of GPU memory, consider using a smaller Whisper model or reducing the simulation resolution.

## License

This project is licensed under the MIT License. See the LICENSE file for details.

---

This README provides an overview of the robot control system, explaining how it integrates voice input, language models, and physics simulation for a complete robotic system.
