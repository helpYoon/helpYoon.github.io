---
title: "Last Mile Manipulation"
excerpt: "Whole-body pick and place for the Booster T1 humanoid that keeps the robot balanced and takes a step when an object is out of reach, built on GPU-parallel task and motion planning.<br/><img src='/images/projects/last-mile-manipulation.jpg' alt='Booster T1 squatting to pick up a block from the floor' style='max-width:500px; width:100%'>"
collection: portfolio
order: 1
---

<video src="/images/projects/last-mile-manipulation.mp4" autoplay loop muted playsinline style="width:100%; max-width:640px"></video>

*A plan for the Booster T1 in simulation: step toward the block, squat to pick it up from the floor, step again, and place it on the green pad.*

Once a humanoid has walked up to a table or a shelf, the last part of the task is still left: picking up and placing objects that sit low, far away or to the side. A humanoid reaches them by bending its hips, knees, ankles and torso. Every such posture moves its center of mass, and when bending is not enough, it has to take a step.

This project extends [cuTAMP](https://cutamp.github.io) to support humanoid robots, where balance is a hard constraint and steps are discrete actions.

How it works
======
* **Whole-body model.** The T1's legs, waist and both arms on a free-floating base, 33 degrees of freedom in all.
* **Balance-first inverse kinematics.** A batched, prioritized Gauss–Newton solve ranks the constraints that nothing else in the plan can fix (center of mass inside the support region, both soles on the floor, no collisions) above the hand pose. A centered center of mass and an upright torso come last. The same solve sets the candidates' starting configurations and, every few optimizer steps, re-projects each pick and place configuration onto its target.
* **Steps as actions.** Left and right steps are actions in the task plan, and where the feet stand is part of the planner's state. The goal is a planner that stands when the object is close and steps when it is far, and that can take several steps if needed.
* **A learned model of where a step lands.** The controller and world model from my [locomotion policy](/portfolio/locomotion-policy/) project predict where each commanded step actually lands. They run inside the optimization loop on every candidate, and the pick and place postures are re-solved on the predicted stance.
* **Balanced motion between keyframes.** Trajectory optimization limits joint torques, with the floor's push on the soles in the dynamics. A final check rejects any motion whose center of mass leaves the feet, and slows each motion down until its zero-moment point, torques and joint speeds stay within bounds.

This is ongoing work.

**Tools:** PyTorch, cuRobo, NVIDIA Warp, GraspGen
