+++
draft = false
date = 2025-11-19T15:16:04+02:00
title = "LAB Implementing ML Agents in Unity 3D"
description = ""
slug = ""
authors = []
tags = []
categories = []
externalLink = ""
series = []
+++


!["pong"](/hci_blog/images/pong.png)


# Building a Game AI with Unity ML-Agents: A Pong Post-Mortem


## Introduction

Reinforcement learning in video games has fascinated developers since DeepMind demonstrated that AI could surpass humans at Atari games. Today, Unity ML-Agents makes this technology accessible to developers, allowing the creation of intelligent opponents without being a machine learning expert. In this article, I share my experience developing a Pong game with a trained AI, the challenges encountered, and the solutions found.

## What is Unity ML-Agents?

Unity ML-Agents is an open-source toolkit that transforms the Unity editor into a training environment for reinforcement learning. The principle is elegant: your Unity game becomes a simulator where virtual agents learn through trial and error, exactly like a human player would progressively discover the game mechanics.

The architecture relies on three main components. The agent perceives its environment through observations (positions, velocities, distances), makes decisions in the form of actions (move, jump, shoot), and receives rewards that guide its learning. The agent's brain can be controlled in three ways: heuristically for debugging, by a human for demonstration, or by a trained neural network.

## Setting Up ML-Agents: The Fundamentals

### Installation and Configuration

The first step involves installing the ML-Agents package in Unity via the Package Manager. Then you need to install Python and the `mlagents` package via pip, which contains the training algorithms (PPO, SAC). This part may seem simple in theory but comes with its share of surprises in practice.

The first pitfall concerns dependencies. ML-Agents relies on PyTorch, which itself may require CUDA for GPU acceleration. Versions must be carefully aligned: a GPU that's too recent may not be supported by the available CUDA versions, forcing you to fall back to CPU training. In my case, my graphics card was too new for the CUDA versions supported by ML-Agents, making GPU acceleration impossible. This significantly slowed down the training process, a constraint I had to work around throughout the project.

### Creating Your First Agent

To implement an agent, you need to inherit from the `Agent` class and override three key methods:

```csharp
public class PongAgent : Agent
{
    public override void CollectObservations(VectorSensor sensor)
    {
        // Add observations about the environment
        sensor.AddObservation(transform.localPosition.x);
        sensor.AddObservation(ball.transform.position);
        sensor.AddObservation(ball.velocity);
    }

    public override void OnActionReceived(ActionBuffers actions)
    {
        // Execute the chosen action
        float moveX = actions.DiscreteActions[0];
        // Move the paddle based on the action
    }

    public override void OnEpisodeBegin()
    {
        // Reset the environment for a new episode
        ResetBallPosition();
        ResetScore();
    }
}
```

The reward system is crucial: it's what teaches the agent to distinguish good from bad behavior. For Pong, I initially thought of simple rewards: +1 for scoring a point, -1 for conceding one. But reality proved more nuanced.

## The Pong Project: From Ambition to Reality

### Initial Architecture

My Pong project is built around four main scripts:

**BallController**: Manages ball behavior, ensuring it stays on the ground plane with constant velocity. I opted for Unity's physics engine rather than manual trajectory calculations, which proved to be the right choice.

**PongAgent**: Controls the AI paddle and manages agent training. This is where all the ML-Agents logic resides: observations, actions, and rewards.

**GameManager**: Orchestrates the overall game logic, managing episodes, scores, and game state transitions.

**UIManager**: Handles the user interface and interactions, providing a clean separation of concerns between game logic and presentation.

### Challenge #1: The Overpowered Opponent

The biggest challenge wasn't technical but conceptual: how do you design an effective training environment?

My first approach was to pit the learning agent against a simple heuristic opponent that just followed the ball. This seemed reasonable—after all, it's how a beginner would learn. But I quickly discovered a fundamental principle of reinforcement learning: **an opponent that's too strong prevents exploration**.

The agent needs to occasionally win to discover good strategies. Facing a perfect opponent, it never experienced the reward of scoring a point, so it couldn't understand which behaviors led to success. Training stagnated completely.

I then tested several approaches:

**Self-Play**: Two agents competing against each other. The theory was appealing—they would improve together. Reality: chaotic learning and sub-optimal behaviors. Without a reference point, the agents developed peculiar strategies that worked against each other but weren't generalisable.

**Agent vs Heuristic**: Going back to the heuristic opponent, but accepting it was too strong. Result: the agent remained stuck and learned nothing. This confirmed that the difficulty was the core issue, not the training method.

**Curriculum Learning**: Progressive learning in phases. Phase 1 aimed to catch the ball, Phase 2 to score points. This was the most promising approach—I saw visible progress. Despite the CPU-only training time being challenging, I persisted with longer training sessions, and it eventually paid off. The agent successfully learned to play competitively.

### Challenge #2: Physics Management

Initially, I tried to manually manage collisions and ball trajectories. This seemed like a good idea to maintain full control. The reality: imprecise bounce angle calculations, the ball getting stuck on walls, and edge cases multiplying.

The solution was to embrace Unity's physics engine:

```csharp
// Ball setup with physics
Rigidbody rb = ball.AddComponent<Rigidbody>();
rb.collisionDetectionMode = CollisionDetectionMode.Continuous;
rb.constraints = RigidbodyConstraints.FreezePositionY; // Keep ball on ground

PhysicMaterial physicsMat = new PhysicMaterial();
physicsMat.bounciness = 1.0f;
physicsMat.friction = 0f;
ball.GetComponent<Collider>().material = physicsMat;
```

Result: simpler code, precise collisions, realistic physics. This illustrates an important lesson—sometimes the best solution is to use existing, well-tested tools rather than reinventing the wheel.

### The Breakthrough

After numerous training iterations and patience with CPU-only training, the ML-Agents approach finally succeeded. The key was accepting the longer training times and carefully tuning the reward structure in the curriculum learning phases.

The trained agent learned not just to react to the ball, but to anticipate trajectories and position itself strategically. While I kept a heuristic fallback in the codebase for testing purposes:

```csharp
float diffX = ball.position.x - paddle.position.x;
float threshold = 0.5f;

if (diffX < -threshold)
    discreteActions[0] = 1; // Move left
else if (diffX > threshold)
    discreteActions[0] = 2; // Move right
```

The neural network-based agent became the primary opponent, demonstrating more nuanced gameplay than the simple ball-tracking heuristic. The trained model weights are included in the project repository, ready to challenge players.

## Key Lessons Learned

### 1. Hardware Matters

Machine learning is computationally intensive. GPU incompatibility wasn't just an inconvenience—it fundamentally changed what was achievable within the project timeline. Always verify your hardware compatibility before committing to an ML approach.

### 2. Problem Design Before Implementation

The biggest roadblock wasn't coding but understanding how to structure the learning problem. An overpowered opponent, incorrect reward structure, or poorly designed observations can completely derail training. Analyze the problem thoroughly before diving in headfirst.

### 3. Embrace Incremental Development

Curriculum learning showed promise because it breaks down a complex problem into manageable sub-problems. This principle applies beyond ML—decompose challenges into smaller, achievable milestones.

### 4. Perseverance Pays Off

While constraints require pragmatism, sometimes persistence through challenges yields the best results. Despite CPU-only training being slow and frustrating, continuing with longer training sessions ultimately produced a working AI. The ML-Agents infrastructure not only remains in the codebase—it powers the game's core opponent.

### 5. Leverage Existing Tools

The physics engine saga illustrates a crucial point: specialized tools exist for a reason. Unity's physics engine handles countless edge cases I would have spent weeks debugging. Know when to build and when to reuse.

## The Final Product

After overcoming the challenges, the project resulted in a functional, enjoyable Pong game featuring:

- A futuristic stadium under a starry sky for immersion
- A trained ML-Agents AI opponent that demonstrates strategic gameplay
- Real-time score display and game timer
- Pause system and post-game restart option
- Clean, responsive UI

Players compete to be first to 7 points against a neural network-trained opponent capable of anticipating trajectories and positioning strategically, creating an engaging and challenging experience that goes beyond simple ball-tracking.

## Future Improvements

With the successful ML-Agents implementation, several enhancements could further improve the experience:

**GPU-Accelerated Retraining**: With GPU access, training time could be dramatically reduced, enabling more experimentation with hyperparameters and reward structures to create an even more sophisticated AI.

**Difficulty Levels**: Training multiple agents with different skill levels—from beginner-friendly to expert-level—would broaden the game's appeal and provide progressive challenges.

**Enhanced Observations**: Adding more contextual information like opponent paddle position, recent trajectory history, and game momentum could enable even more nuanced strategies.

**Opponent Behavior Variation**: Training agents with different playstyles (aggressive vs defensive, reactive vs predictive) would increase replayability and create distinct opponent personalities.

## Conclusion

Unity ML-Agents opens fascinating possibilities for game AI, and with patience and persistence, these possibilities become reality. Success requires understanding both the technical framework and the learning problem you're trying to solve. Hardware constraints are real and training takes time, but neither are insurmountable obstacles.

The key takeaway? Analyze problems before implementation, adapt your approach when needed, and don't give up when progress seems slow. CPU-only training is challenging but viable. Sometimes the solution requires not changing your approach, but giving it enough time to work.

The complete project, including trained model weights, is available on [GitHub](https://github.com/LeDuc34/Pong) for those interested in exploring ML-Agents implementation.




---

*Have you worked with Unity ML-Agents? What challenges did you encounter? Share your experiences in the comments!*