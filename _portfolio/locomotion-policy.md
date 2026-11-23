---
title: "Training a Humanoid Locomotion Policy"
excerpt: "One policy for the Booster T1 and T2 humanoids that tracks whole-body motion clips and walks on velocity commands, trained through a learned world model.<br/><img src='/images/projects/locomotion-policy.jpg' alt='Booster T2 humanoid walking in simulation' style='max-width:500px; width:100%'>"
collection: portfolio
order: 1
---

<video src="/images/projects/locomotion-policy.mp4" autoplay loop muted playsinline style="width:100%; max-width:640px"></video>

*The trained policy on the Booster T2 in simulation, following velocity commands: forward at 1.0 m/s, backward at 0.6 m/s, sideways at 0.5 m/s, and turning at 1.0 rad/s while walking forward.*

I train whole-body control policies for Booster's T1 and T2 humanoids. One policy has two input modes that share a single decoder:

* **Motion tracking:** follow a full-body reference clip from motion capture.
* **Velocity commands:** walk at a commanded forward, sideways and turning speed.

How it trains
======
The learner comes from [SuperMimic](https://supermimic.github.io) and [BlinkMimic 2](https://github.com/jyl681/blinkmimic2). A world model is fit to data from 4,096 simulated robots, and the policy learns by backpropagating through short rollouts imagined in that model. The simulation runs on [mjlab](https://github.com/mujocolab/mjlab) (MuJoCo-Warp).

<div style="overflow-x:auto; margin:1.5em 0">
<svg viewBox="0 0 960 300" style="width:100%; min-width:640px; max-width:960px; height:auto; font-family:inherit" role="img" aria-labelledby="pipeline-title">
<title id="pipeline-title">Training pipeline: the simulator fills a replay buffer, a world model is fit to it, the policy learns from rollouts imagined in the world model, collects new data in the simulator, and is exported to the Booster T2 robot.</title>
<defs><marker id="ah" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto-start-reverse"><path d="M0,0 L10,5 L0,10 z" fill="currentColor"/></marker></defs>
<g fill="currentColor" fill-opacity="0.04" stroke="currentColor" stroke-opacity="0.35" stroke-width="1.5">
<rect x="340" y="20" width="280" height="100" rx="10"/>
<rect x="660" y="20" width="280" height="100" rx="10"/>
<rect x="660" y="180" width="280" height="100" rx="10"/>
</g>
<rect x="20" y="20" width="280" height="100" rx="10" fill="#3b82c4" fill-opacity="0.14" stroke="#3b82c4" stroke-width="1.5"/>
<rect x="340" y="180" width="280" height="100" rx="10" fill="#c9962b" fill-opacity="0.16" stroke="#c9962b" stroke-width="1.5"/>
<rect x="20" y="180" width="280" height="100" rx="10" fill="#3c9a5f" fill-opacity="0.14" stroke="#3c9a5f" stroke-width="1.5"/>
<g fill="none" stroke="currentColor" stroke-opacity="0.6" stroke-width="1.8" marker-end="url(#ah)">
<path d="M300,60 H336"/>
<path d="M620,60 H656"/>
<path d="M800,120 V176"/>
<path d="M660,230 H624"/>
<path d="M340,230 H304"/>
<path d="M440,180 V150 H320 V95 H304"/>
</g>
<g fill="currentColor" text-anchor="middle">
<text x="160" y="58" font-size="20" font-weight="600">Simulator</text>
<text x="160" y="84" font-size="16" fill-opacity="0.8">4,096 robots in mjlab</text>
<text x="160" y="106" font-size="16" fill-opacity="0.8">randomized physics</text>
<text x="480" y="62" font-size="20" font-weight="600">Replay buffer</text>
<text x="480" y="90" font-size="16" fill-opacity="0.8">simulator transitions</text>
<text x="800" y="62" font-size="20" font-weight="600">World model</text>
<text x="800" y="90" font-size="16" fill-opacity="0.8">learned dynamics</text>
<text x="800" y="218" font-size="20" font-weight="600">Imagined rollouts</text>
<text x="800" y="244" font-size="16" fill-opacity="0.8">policy learns by backprop</text>
<text x="800" y="266" font-size="16" fill-opacity="0.8">through the world model</text>
<text x="480" y="218" font-size="20" font-weight="600">Policy</text>
<text x="480" y="244" font-size="16" fill-opacity="0.8">motion + velocity encoders</text>
<text x="480" y="266" font-size="16" fill-opacity="0.8">one shared decoder</text>
<text x="160" y="222" font-size="20" font-weight="600">Booster T2 robot</text>
<text x="160" y="250" font-size="16" fill-opacity="0.8">ONNX, runs on hardware</text>
<text x="452" y="160" font-size="14" fill-opacity="0.7" text-anchor="start">collects new data</text>
</g>
</svg>
</div>

My work on top of it:

* Ported the learner to the Booster T1 (29 DoF) and T2 (31 DoF), and retargeted the BONES-SEED motion-capture dataset (about 12,000 clips) to both robots.
* Added the velocity-command mode. Half of the simulated robots train on velocity commands through their own encoder and critic, sharing the decoder with motion tracking.
* Adjusted the velocity-tracking reward (table below).
* Added a landing command that places a chosen foot at a commanded position and heading.
* Modeled the T2 as a real one was measured (joint friction and linkage damping), with domain randomization around those values.

Velocity reward
======
The reward of the T2 velocity policy that runs on the robot.

| Term | Expression | Weight |
|---|---|---|
| Linear velocity tracking | k + 0.75 (k⁴ − k), k = exp(−‖v<sub>xy</sub> − v<sub>cmd</sub>‖² / 0.25) | 5.0 |
| Yaw-rate tracking | k + 0.75 (k⁴ − k), k = exp(−(ω<sub>z</sub> − ω<sub>cmd</sub>)² / 0.25) | 5.0 |
| Survival | 1 per step | 30 |
| Action rate | ‖a<sub>t</sub> − a<sub>t−1</sub>‖ | −0.5 |
| Joint torque | ‖τ‖ | −0.0005 |
| Foot slide | Σ ‖v<sub>foot</sub>‖ over feet in contact | −0.25 |
| Foot jerk | Σ ‖jerk<sub>foot</sub>‖ | −0.001 |
| Ankle torque (standing) | 1 − exp(−e / 400), e = Σ (τ<sub>ankle pitch</sub> − 8.5)² + 2.8 Σ τ<sub>ankle roll</sub>² | −4 |
| Rest pose (standing) | 1 − exp(−‖q − q<sub>rest</sub>‖² / 0.05) over hip roll, hip yaw and waist | −2 |

Results
======
* In simulation, the T1 policy trains in 32 minutes on one RTX 4090 and tracks all 11,900 evaluation clips with no failures.
* With the landing command, 96.9–99.6% of single steps from a standing pose land within 5 cm of the target across three seeds, with no falls.
* On hardware, the T2 velocity policy runs on the real robot, exported to ONNX and deployed through Booster's deployment stack.

**Tools:** PyTorch, MuJoCo, MuJoCo-Warp, mjlab, ONNX
