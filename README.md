# AdvBridge
AdvBridge is a targeted data poisoning method for social recommendation. It constructs budget-constrained interaction and social profiles for controlled users to promote a target item to a specific target user.

## Overview

AdvBridge combines a diffusion-enhanced surrogate with dual-anchor profile optimization. The surrogate learns user and item representations from a clean graph using MHCN and cross-guided dual diffusion. These representations guide the construction of interaction and social connections around the target user and target item.

The method first builds candidate pools using embedding similarity, PageRank, local interactions, and item popularity. It then alternates between bridge/fidelity optimization and ranking/concentration optimization under degree budgets. Continuous profile weights are projected onto capped-simplex constraints and converted into discrete connections through Top-K selection. A fresh victim without diffusion is trained on the modified graph to evaluate the attack.

