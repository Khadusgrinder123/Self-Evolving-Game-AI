# Make Me Smarter!

An experimental 2D top-down survival environment driven by a custom Recurrent Neural Network (RNN) built entirely from scratch in Python.

Instead of relying on high-level machine learning frameworks (like PyTorch or TensorFlow), this project implements raw matrix operations, continuous temporal state tracking, and mutation-driven evolutionary mechanics to train real-time tactical AI decision-making.

---

## Architectural & Technical Highlights

### 1. Custom Recurrent Neural Network (RNN) Engine

* **Raw Matrix Operations:** Leverages NumPy for low-level matrix multiplications (`np.dot`), weight initializations, and activation functions.
* **Temporal Memory:** Utilizes hidden memory vectors (tanh activation) that persist across frames, allowing the AI agent to retain short-term temporal awareness of projectile trajectories and player velocity.
* **Custom Topology:**
  * **Input Layer:** 16-dimensional continuous state vector.
  * **Hidden Layer:** 32 recurrent hidden units.
  * **Output Layer:** 2 continuous control outputs (2D directional movement vector).

---

## Neural State Representation

At every tick, the game engine constructs a normalized 16-dimensional state vector fed directly into the network:

`S = [ dx_player, dy_player, dist_player, hp_enemy, hp_player, wall_left, wall_right, wall_top, wall_bottom, dx_threat, dy_threat, prev_action_x, prev_action_y, sin(t), cos(t), proximity_flag ]`

Key inputs include:

* **Spatial Tracking:** Relative Euclidean distance and directional offsets toward the target.
* **Threat Detection:** Dynamic tracking of the nearest active projectile velocity vector relative to the agent's center.
* **Boundary Proximity:** Normalized relative distance to screen borders to prevent edge-trapping.
* **Recurrent Feedback & Phase:** Last-action feedback loop paired with high-frequency time harmonics (`sin(t)`, `cos(t)`) for pattern generation.

---

## Evolutionary Reinforcement Mechanics

The agent's policy is optimized through an online mutation and dynamic fitness evaluation loop:

```text
┌─────────────────────────────────────────┐
│      Continuous Environment State       │
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
    Save Model         Mutate Weights (15%)
 (best_brain.pkl)     Reset Recurrent State
