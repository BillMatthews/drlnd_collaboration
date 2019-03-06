# Introduction
This report outines the implementation of a learning Agent to collaborate/compete in a game of Tennis. The game consists of two players (agents) that attempt to hit the ball over the net. An agent recieves a reward of +0.1 if it manages to hit the ball over the net and a reward of -0.01 if the agent lets the ball hit the ground or hits the ball out of bounds. The two agents aim to keep the ball in play to maximise their score.

The problem is episodic with an eposide ending when either the ball hits the ground or is hit out of bounds.

The Environment is solved when over 100 episodes the average score is 0.5 or greater where the score is the maximum of the two agents for an episode.

# Description of the Learning Algorithm
The Agent uses an implementation of the Multi-Agent Deep Determistic Policy Gradient algorithm ([MADDPG](https://arxiv.org/pdf/1706.02275.pdf)) using the code provided in [Udacity Deep Reinforcement Learning - ddpg-pendulum](https://github.com/udacity/deep-reinforcement-learning/tree/master/ddpg-pendulum) as a basis for the implemented Agent.

This MADDPG has 2 agents (one for each player) and each of these agents are DDPG (Deep Deterministic Policy Gradient) Agents which use seperate Actor and a Critic networks 
 - the Actor attempts to learn the Policy to decide on the action to take based on the current environment state
 - the Critic attempts to learn Value function to estimate value of the action taken 

In this implementation, both player agents share the same Experience Replay buffer rather than, in the case of DDPG, having it's own. This buffer contains the observations for both player agents.

During the training of the Agent, the 

Both the Actor and Critic networks have a local and target network; during learning the local network is updated and after a period of time the target network is updated using a soft update.

The Algorithm implemented had the following configuration:

**Actor**
- 2 Hidden layers with 256 and 128 units using a Leaky RELU activation
- Tanh activation function on output
- Local and Target networks initalised to the same weights before training

**Critic**
- 2 Hidden layers with 256 and 128 units using a leaky RELU activation
- Local and Target networks initalised to the same weights before training


**MADDPG Algorithm**
- Delays the start of learning until a period of exploration (300 episodes) was complete 
- Had multiple learning epochs per step (learned 3 times for each step)
- Shares additional state information with the Player Agent Critic networks; this was the state and actions for the other Player Agents
- Decays the random noise applied to actons over episodes


## Plot of Rewards
The Agent was used to train the player agents to achieve an average score of 0.5 over 100 episodes.

The Agent was able to solve the environment within 1835 episodes.

The following plot shows the plot of rewards during the training:

![Image containing a Plot of Rewards for the network during training](trainingRewardsPlot.png)

The plot shows a long period (in terms of episodes) where the rewards were meager followed by a period of increased rewards. There is a great deal of variation in the average rewards even towards the end of the training which suggests the training could be refined further.


# Ideas for Future Work
Additional avenues for exploration include:
## Using Prioritised Experience Replay
The plot of rewards indicates quite a long period of relatively low rewards followed by a shorter period of much higher rewards leading to the goal (average score >0.5 over 100 episodes). Observing play of a partially trained (during the low reward period) indicated that while there were some good rallies most of the time the rallies were short. Longer rallies have higher rewards and it would seem beneficial to learn more from these than from those with much lower rewards. However with a pure random sampling this is not always the case.

To improve the learning, the introduction of a Prioritised Experience Replay Buffer may allow the Agent to learn quicker as higher reward experiences have a higher chance of being selected for learning.

## Experimenting with Additional State information
The current implementation provides the Critic networks with additional state information, namely the information about the other agent's state and actions. Currently this information is provided as is from the Unity Environment. However, this data incldues some repetition which may bias the learning.

Experiments could be undertaken to reduce the level of duplication in this additional state data and to provide additional data that is derived from the experiences that condenses the essential additional state information into a smaller data se

These actions may reduce the compuation time and focus the learning of the agent. 

## Using a Shared Critic
The current implementation is using seperate critics for the two agents; each of these critics is being provided with similar information and so some of the learning may be duplicated. 

The use of a single Critic that is shared across the Player Agents may improve this and lead to quicker learning of the Value function which in turn may improve the learning of the policy function 

