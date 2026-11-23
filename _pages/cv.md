---
layout: archive
title: "CV"
permalink: /cv/
author_profile: true
redirect_from:
  - /resume
---

{% include base_path %}

[Download CV (PDF)]({{ base_path }}/files/Yoonwoo_Kim_CV.pdf)

Education
======
* Ph.D. in Computer Science, University of Texas at Austin, Aug 2023 – present
  * Advisor: Prof. Peter Stone
* M.S. in Computer Science, Georgia Institute of Technology, Aug 2020 – May 2023
  * Thesis: Task Planning in Factor Graph Representation (Advisor: Prof. Frank Dellaert)
* B.S. in Computer Science, Georgia Institute of Technology, Aug 2016 – May 2020
  * Graduated with Highest Honors

Research experience
======
* Aug 2023 – present: Graduate Research Assistant, LARG Lab, University of Texas at Austin, Austin, TX
  * Advisor: Prof. Peter Stone
  * Task and motion planning for humanoid manipulation with a learned controller in the loop
  * LLM-guided state estimation for partially observable task and motion planning (ICRA 2026)
  * Mobile manipulation for household robotics (RoboCup@Home 2025, 2nd place)

* Aug 2020 – May 2023: Graduate Research Assistant, BORG Lab, Georgia Institute of Technology, Atlanta, GA
  * Advisor: Prof. Frank Dellaert
  * Proposed a classical planner using the max-product elimination algorithm on a factor graph representation, implemented in C++
  * Contributed to the open-source C++ library [GTSAM](https://github.com/borglab/gtsam) by implementing a discrete probabilistic factor optimized for sparsity, which sped up sparse factor multiplication by 19.6× on average

Work experience
======
* May 2025 – Aug 2025: Machine Learning Engineer, Roboligent, Austin, TX
  * Fine-tuned NVIDIA GR00T VLA for warehouse manipulation tasks
  * Developed a force-aware VLA for forceful manipulation tasks during interactions with articulated objects

Projects
======
* RoboCup@Home
  * Leading a team of five graduate students to solve common household tasks with the Booster T1 and T2 humanoids
  * Developed a whole-body controller with an RL policy, a task and motion planner for humanoids, and the overall system design for the deployment pipeline using DORA

Publications
======
  <ul>{% for post in site.publications reversed %}
    {% include archive-single-cv.html %}
  {% endfor %}</ul>

Skills
======
* Technical: C++, Python, PyTorch, Docker, ROS2, DORA
* Languages: English, Korean

Honors and awards
======
* Highest Honors, Institutional Award, Georgia Institute of Technology (2020)
* Finalist (top 3), Samsung Uventure Competition, Samsung Strategy and Innovation Center (2020)
* Faculty Honors, Georgia Institute of Technology (Summer 2017)
* Dean's List, Georgia Institute of Technology (Fall 2016, Spring 2017, Fall 2017, Spring 2018, Fall 2018)
