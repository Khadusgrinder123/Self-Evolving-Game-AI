# Make Me Smarter!

An experimental 2D top-down survival environment driven by a custom Recurrent Neural Network (RNN) built entirely from scratch in Python. 

Instead of relying on high-level machine learning frameworks (like PyTorch or TensorFlow), this project implements raw matrix operations, continuous temporal state tracking, and mutation-driven evolutionary mechanics to train real-time tactical AI decision-making.

---## Architectural & Technical Highlights### 1. Custom Recurrent Neural Network (RNN) Engine* **Raw Matrix Operations:** Leverages NumPy for low-level matrix multiplications (`np.dot`), weight initializations, and activation functions.* **Temporal Memory:** Utilizes hidden memory vectors ($\tanh$ activation) that persist across frames, allowing the AI agent to retain short-term temporal awareness of projectile trajectories and player velocity.* **Custom Topology:**  * **Input Layer:** 16-dimensional continuous state vector.  * **Hidden Layer:** 32 recurrent hidden units.  * **Output Layer:** 2 continuous control outputs (2D directional movement vector).

---## Neural State Representation

At every tick, the game engine constructs a normalized **16-dimensional state vector** fed directly into the network:

$$S = \begin{bmatrix} \Delta x_{player}, & \Delta y_{player}, & d_{player}, & H_{enemy}\%, & H_{player}\%, & W_{left}, & W_{right}, & W_{top}, & W_{bottom}, & \Delta x_{threat}, & \Delta y_{threat}, & a_{x}^{t-1}, & a_{y}^{t-1}, & \sin(t), & \cos(t), & P_{proximity} \end{bmatrix}$$

Key inputs include:* **Spatial Tracking:** Relative Euclidean distance and directional offsets toward the target.* **Threat Detection:** Dynamic tracking of the nearest active projectile velocity vector relative to the agent's center.* **Boundary Proximity:** Normalized relative distance to screen borders ($W_{left}, W_{right}, W_{top}, W_{bottom}$) to prevent edge-trapping.* **Recurrent Feedback & Phase:** Last-action feedback loop paired with high-frequency time harmonics ($\sin(t), \cos(t)$) for pattern generation.

---## Evolutionary Reinforcement Mechanics

The agent's policy is optimized through an online mutation and dynamic fitness evaluation loop:
       ┌─────────────────────────────────────────┐
       │      Continuous Environment State      │
       └────────────────────┬────────────────────┘
                            │
                            ▼
       ┌─────────────────────────────────────────┐
       │     RNN Forward Pass (State -> Action)  │
       └────────────────────┬────────────────────┘
                            │
                            ▼
       ┌─────────────────────────────────────────┐
       │ Dynamic Fitness & Survival Evaluation   │
       └────────────────────┬────────────────────┘
                            │
                 ┌──────────┴──────────┐
                 ▼                     ▼
         [ New High Score ]    [ Standard Round ]
                 │                     │
                 ▼                     ▼
           Save Model         Mutate Weights (15% Rate)
        (best_brain.pkl)     Reset Recurrent State

* **Dynamic Fitness Scoring:** Evaluates real-time health differentials, distance maintenance (penalizing hyper-passive or suicidal behaviors), and round survival duration.
* **Persistent Serialization:** High-performing network weights and biases are serialized dynamically to `best_brain.pkl` via `pickle` to establish persistent model evolution across execution sessions.

---

## Tech Stack

* **Language:** Python 3
* **Computation Engine:** NumPy (Custom RNN implementation)
* **Graphics & Asset Pipeline:** Pygame
* **Model Persistence:** Binary Pickle Serialization
