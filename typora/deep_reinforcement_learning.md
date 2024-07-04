- #### Monte Carlo

  - Probability approximation
  - definite integral approximation
  - Expectation approximation
  - stochastic gradient
    - Stochastic gradient allows for faster approximation of results

- ### Basic concepts of reinforcement learning

  - #### markov decision process, MDP
  
    - agents interact with the environment
    - agents makes decisions and actions
    - environment act as rules in the interaction
    - state: all the information for one frame
    - state space: a set of state(limited or infinite countable)
    - action: agents' s decision base on current state
    - action space: a set of possible decisions
    - **reward**: value of the decision, defines by us.
    - **state transition**:
      - Deterministic state transfer is a special case of stochastic state transfer
  
  - policy: agents make decision base on the state.
  
    - > The goal of reinforcement learning is to obtain a strategy function,the strategy can be deterministic or stochastic.
  
    - stochastic policy:  input state, output possibility of actions base on the state (probability density function).
  
    - deterministic policy: input state, output deterministic action instead of possibility (0-1 density function) - > a little similar to max function.
  
    - agent environment interaction: agent make decision base on the possibility, then programs calculate the new state and return a reward for agent.
  
  - stochasticity: action and state are the sources of stochasticity.
  
    - > markov property: the next state only relevant with the state and action now, and irrelevant with state and action before.
  
    - trajectory: all the actions, states, rewards that observable for agent in one episode.
  
  - return and discounted return: 
  
    > add up rewards in an episode. consist of return and discounted return(more often)
  
    - return : discount factor = 1
    - discounted return : discounted factor < 1
    - stochasticity : fundamentally because of the stochasticity of action and state.
    - finite-horizon MDP and infinite-horizon MDP:
      - finite-horizon MDP has a terminal state of one episode.
      - infinite-horizon MDP should attach a discounted return that smaller than 1 or its return can go infinite.
  
  - value function
  
    - > expectation of return, reflecting the  current situation good or bad, the larger it is, the better the current situation
  
    - action-value function: the result of Q(action-value function) is the expectation of U(discounted return) in current situation.