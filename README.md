
# Emergency Vehicle Autonomous Driving (RL)

### Jump to main project: [notebook]()

Aim: Train an autonomous emergency vehicle model (ambulance, police, firefighter) within **CARLA** simulator, making use of **Reinforement Learning**.

Differences from a standard autonomous vehicle model:
* Prioritize speed
* Ignore traffic laws (eg. speed limits, traffic lights)
* Minimize time to destination as much as possible

The model uses Proximal Policy Optimization (PPO) and is gradually exposed to more complex situations, adding traffic and different weather conditions

## Installation

### Prerequisites
* Python 3.8 – Recommended for best stability with CARLA
* `py -3.8 -m pip install --upgrade pip`

### Installing CARLA
* [CARLA_0.9.15.zip](https://github.com/carla-simulator/carla/releases).
* Downaload and extract
* Begin the simulation, (fps 600, to speed up the process)
    ```bash
    ./CarlaUE4.exe -fps=600
    ```
    ![image](https://github.com/user-attachments/assets/ad335f76-6db4-4328-a413-dfd8a8ce1d55)
* Alternativley for off screen rendering which allows for RGB sensor (not compatibel with no rendering).
    ```bash
    ./CarlaUE4.exe -RenderOffScreen
    ```

### Notebook Location
```
CARLA_0.9.15/
├── CarlaUE4/
├── PythonAPI/
│   ├── examples/
│   ├── rl_project_autonomous/
│       ├── training/
│       ├── notebook.ipynb
│       └── requirements.txt
├── util/
├── LICENSE
└── README
```

#### Speeding up training
* Disable rendering, to remove GPU load
    ```bash
    py -3.8 ./PythonAPI/util/config.py --no-rendering
    ```
* Top down 2D view
    ```bash
    py -3.8 ./PythonAPI/examples/no_rendering_mode.py
    ```

#### Increasing Complexity
* Generate traffic
    ```bash
    py -3.8 ./PythonAPI/examples/generate_traffic -n {number of cars}
    ```

* Enable dynamic weather
    ```bash
    py -3.8 ./PythonAPI/examples/dynamic_weather
    ```

## Features
- **Sensors:**
  - RGB Camera (with renring mode)
  - LiDAR Sensor
  - Collision Sensor
  - GNSS Sensor
  - `no_rendering_mode.py` to create a top-down 2D view with Pygame.
    ![image](https://github.com/user-attachments/assets/169dbf9a-ba5c-49c4-9132-a8e30a331bed)


## Reinforcement Learning Details

### Observation Space
The agent's input includes:
- RGB Camera feed (`Box`): `(480, 320, 3)`.
- LiDAR Point Cloud (`Box`): `(360, 3)`.
- Collision status (`Discrete`): `0` or `1`.
- GNSS Location (`Box`): Latitude, Longitude, Altitude.
- Destination Location (`Box`): Latitude, Longitude, Altitude.

### Action Space
The agent controls:
- Steering: Continuous values between `[-1.0, 1.0]`.
- Throttle: Continuous values between `[0.1, 1.0]` (minimum throttle ensures movement)
### Reward Function
The reward function:
- Penalizes collisions (`-200`).
- Penalizes time (`-0.2` per step).
- Rewards higher speed (up to 45 m/s).
- Rewards proximity to the destination (`100 / (distance + 1)`).

## Visualizations
TensorBoard to monitor training progress:
```bash
tensorboard --logdir=training/logs
```

## Acknowledgements
- [CARLA Simulator](https://carla.org/)
- OpenAI Gym for the ENV framework.
- Stable Baselines3: PPO RL algorithm.
