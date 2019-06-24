### Secure and Private AI Udacity course notes （Concepts)

- [Secure and Private AI](#Secure-and-Private-AI)
- **[Example Code On Github](https://github.com/udacity/private-ai/)**

---
#### Secure and Private AI

#### Lesson 3: Introducing **Differential Privacy**
- scientists are restricted to access personal training data because of *privacy* issues
- Differential Privacy
    - *Privacy* is preserved if after the analysis, the analyzer doesn't know anything about the people in the dataset. They remain "*unobserved*".
    - learn without access database - impossible
    - *Cynthia Dwork*, father of differential privacy
        > "Differential Privacy" describes a promise, made by a data holder, or curator, to a data subject, and the promise is like this: "You will not be affected, adversely or otherwise by allowing your data to be used in any study or analysis, no matter what other studies, data sets, or information sources, are available"
- Can we just anonymize
    - De-anonymize strategies exist
- A query which doesn't change no matter who we remove from the database

#### Lesson 4: Evaluating The Privacy of A Function
- **Sensitivity**: The max amount that the query changes when removing an individual from the database
    - example: computing by sum, mean, threshold
- Differencing Attack
    - e.g. sum: query the sum of the entire database - the sum or the database w/o the person

#### Lesson 5: Introducing Local and Global Differential Privacy
- add random noise into the database
    - *Local Differential Privacy*: Add noise to function data points(function inputs)
        - most protected, each individual add noise before sending it to database
        - gained privacy but lost accuracy 
    - *Global Differential Privacy*: Add noise to function outputs
        - trusted curator 
        - more accurate
- Differential Privacy always requires a form of **randomness** or **noise** added to the query to protect from things like Differencing Attacks.
    - more data points, less influence of noise
- Technique
    - **Randomized Response** - localized 
        - Technique that is used in social sciences which trying to learn about the high level trends of a taboo behavior
        - **Plausible Deniability**
            - Flip a coin two times
            - if the first coin flip is heads, answer (yes/no honestly)
            - if the first coin flip is tails, answer according to the second coin flip
        - later researchers can remove the noise - process: takes the noise stats and average it with 50-50 coin flip then get the true result
            - e.g. 70% of people actually jaywalk
            - know that 60% would answer yes
                - 50% people would say w/ 50% 
                - 50% people would say w/ 70%
                - without knowing whether actual person jaywalk
            - Arithmetic 60 * 2 - 50 - 70
        ```python
        augmented_database_mean = true_dist_mean * (1 - noise) + noise_dist_mean * noise
        ```
- Differential Privacy (Intuition)
    - Most accurate query w/ the greatest amount of privacy 
        - goal: min noise while max accuracy
    - greatest fit with trust models in the actual world(don't waste trust)
        - if trust, no need to add noise
    - flexible
    - a complex kind of *filter* 
        - looks for information that is consistent across multiple different individuals
        - filter out information that is unique about individuals
- Differential Privacy Math Definition
    - globally adding noise to function output - reduce sensitivity
    - lean toward global versus local
    - <img src="https://github.com/udacity/private-ai/blob/master/dp_formula.png"  width="550"/>
    - constraint: "Epsilon" and "Delta" 
        - one query
        - the difference of the probability distribution over all database verses the probability distribution over all in the database minus one entry 
            - controlled by exp(epsilon)
        - delta is the probability that one *accidentally* leak more info than epsilon claims that one will leak
        -  perfect privacy - both variables are zero
- how do we actually use epsilon and delta
    - **Randomized mechanism**: A function w/ random noise added to its inputs, outputs, and/or inner workings.
    - **privacy budget** how much epsilon/delta leakage we allow for our analysis
    - how much noise should we add
        - Type: Gaussian dist or Laplacian(better) dist
        - sensitivity of query
        - desired epsilon or delta
    - Laplacian noise
        - `beta = sensitivity(query) / epsilon`
        - delta is zero
        - `nn.random.Laplace`
        - query repeatedly - add more epsilon
        - `noise = torch.tensor(np.random.laplace(0, beta, 1)); return `
        - epsilon lower - increase the amount of noise we add
            - stronger protection need more data points
- close relationship / trade-off
    - sensitivity (e.g. mean vs. sum, low sensitivity - low epsilon)
    - privacy leakage
    - number of entries
    - accuracy of the output
    
#### Differential Privacy for Deep Learning
- **Perfect Privacy** (AI Model)
    - training a model on a dataset should return the same model even if we remove any person from the training set
- Two points of complexity
    - Do we always know where "people" are referenced in dataset
        - no idea where all people are referenced
    - Neural models rarely ever to the same location, even trained on the same dataset twice
        - randomness in the training process
- techniques depends on *trust* between parties, trade-off between accuracy, noise
- example of medical dataset
    - ask each hospital to train a model on their own dataset
    - use each model to predict on your own local dataset, generating 10 labels for each data point
    - perform a DP query to generate the final true (DP) label for each datapoint (max function, most frequent label)
    - retrain a new model on our local dataset which now has DP labels
- PATE Analysis
    - output of series is immune to remove any person in the dataset
    - give better epsilon
    - agree - epsilon low
    - disagree 
    - `pip inslall syft`
    - `from syft.frameworks.torch.differential_privacy import pate`
    - `pate.perform_analysis(...)` returns data_dep_eps and data_ind_eps
    - increase disagreement, lower data dependent epsilon
- More about Differential Privacy
    - Read
        - [Algorithm Foundations of Differential Privacy](https://www.cis.upenn.edu/~aaroth/Papers/Privacybook.pdf)
        - [Deep Learning w/ Differential Privacy](https://arxiv.org/pdf/1607.00133.pdf)
    - Topics
        - The Exponential Mechanism
        - The Moment's Accountant
        - Differentially Private Stochastic Gradient Descent

#### Federated Learning
- **Federated Learning**: A technique for training machine learning models on data to which you do not have access (proposed by Google)
    - context of mobile phone typing 
        - when texting training on local but upload model to the cloud 
        - collaborating together training the model
        - use the model trained by everyone but without leaking one's privacy
        - initial intention: reduce the cost
- PySyft
    - pytorch cooperated on remote machine
    - `import torch as th import syft as sy` `hook = sy.TorchHook(th)`
- Garbage Collection
    - **PySyft** 
        - generally coding the same way as pytorch but send model and data to the virtual worker (small change)
        - send model to the data, do normal training get smarter model back
        <!-- - reverse the , model sent and model that got back -->
    - train on multiple models
        - average of other models
        - won't know which model do the changes
        - just pure remote execution before but now cross over 
<!-- - x at bob but send the pointer to alice now me->alice->bob joint owner of the asset
- x = x.get() free alice 
- again get the actual data back from bob -->
- pointer to pointer, *pointerchain*
    - move data between remote workers
    ```python
    x = th.tensor([1, 2, 3]).send(bob).send(alice) # alice contain a pointer from alice to bob
    x.remote_get() # pull the data from bob to alice
    x.move(bob) # move back to bob directly
    ```

#### Secure Federated Learning
- **trusted aggregator**
    - send gradience to a trusted third party
<!-- ```python3
iterations = 10
worker_iters = 5

for a_iter in range(iterations):

    bobs_model = model.copy().send(bob)
    alices_model = model.copy().send(alice)

    bobs_opt = optim.SGD(params=bobs_model.parameters(), lr=0.1)
    alices_opt = optim.SGD(params=alices_model.parameters(), lr=0.1)

    for wi in range(worker_iters):
        # Train Bob's Model
        bobs_opt.zero_grad()
        bobs_pred = bobs_model(bobs_data)
        bobs_loss = ((bobs_pred - bobs_target) ** 2).sum()
        bobs_loss.backward()

        bobs_opt.step()
        bobs_loss = bobs_loss.get().data

        # Train Alice's Model
        alices_opt.zero_grad()
        alices_pred = alices_model(alices_data)
        alices_loss = ((alices_pred - alices_target) ** 2).sum()
        alices_loss.backward()

        alices_opt.step()
        alices_loss = alices_loss.get().data

    alices_model.move(secure_worker)
    bobs_model.move(secure_worker)

    with th.no_grad():

        model.weight.set_(((alices_model.weight.data + bobs_model.weight.data) / 2).get())
        model.bias.set_(((alices_model.bias.data + bobs_model.bias.data) / 2).get())
    
    print("Bob:" + str(bobs_loss) + " Alice:" + str(alices_loss))
``` -->
- Additive Secret Sharing
    - Allows multiple individuals to add numbers together without any person learning anyone else's inputs to the addition
    - 5 = Bob(2) + Alice(3)
- Fixed Precision Encoding - supporting floating points
    - lean on pysyft 
        ```python3
        x = x.share(bob, alice, secure_worker) # nesting
        ```
    - multiple layers of abstraction
        - type `x` is a torch tensor 
        - `x.child`: fixed prec tensor (what we know)
        - `x.child.child`: native tensor: raw data
<!-- ```python3
BASE=10
PRECISION=4

def encode(x):
    return int((x * (BASE ** PRECISION)) % Q)

def decode(x):
    return (x if x <= Q/2 else x - Q) / BASE**PRECISION

x = encrypt(encode(5.5))
y = encrypt(encode(2.3))
z = add(x,y)
decode(decrypt(z)) # return 7.8
``` -->

#### Encrypted Deep Learning
- native way
    - subtraction with modulo
- decode positive or negative decimal numbers using divide Q into half
- multiplication 
    - just add multiple times
- Encrypted Computation in Pysyft
    - `x = x.share(bob, alice, crpto_provider=secre_workder)`
    - `crpto_provider` provides a more secure protocol 
    - also supports comparison operations > < == such as z = x > y
    - `x = x.fix_precision. ...`
        - work the same way on decimals
        - comparison also returns floating points 1. 0. ...
- able to slice the tensor the same way
- Project: Build an **Encrypted Database**
    - hint: store the strings as one-hot encoder or index-based
<!--     ```python3
    def string2values(str_input, max_len=8):

        str_input = str_input[:max_len].lower()

        # pad strings shorter than max len
        if(len(str_input) < max_len):
            str_input = str_input + "." * (max_len - len(str_input))

        values = list()
        for char in str_input:
            values.append(char2index[char])

        return th.tensor(values).long()

    def values2string(input_values):
        s = ""
        for value in input_values:
            s += index2char[int(value)]
        return s

    def strings_equal(str_a, str_b):

        vect = (str_a * str_b).sum(1)

        x = vect[0]

        for i in range(vect.shape[0] - 1):
            x = x * vect[i + 1]    

        return x

    def one_hot(index, length):
        vect = th.zeros(length).long()
        vect[index] = 1
        return vect

    def string2one_hot_matrix(str_input, max_len=8):

        str_input = str_input[:max_len].lower()

        # pad strings shorter than max len
        if(len(str_input) < max_len):
            str_input = str_input + "." * (max_len - len(str_input))

        char_vectors = list()
        for char in str_input:
            char_v = one_hot(char2index[char], len(char2index)).unsqueeze(0)
            char_vectors.append(char_v)
            
        return th.cat(char_vectors, dim=0)
    ```
    ```python3
    class EncryptedDB():
        
        def __init__(self, *owners, max_key_len=8, max_val_len=8):
            self.max_key_len = max_key_len
            self.max_val_len = max_val_len
            
            self.keys = list()
            self.values = list()
            self.owners = owners
            
        def add_entry(self, key, value):
            key = string2one_hot_matrix(key)
            key = key.share(*self.owners)
            self.keys.append(key)
            
            value = string2values(value, max_len=self.max_val_len)
            value = value.share(*self.owners)
            self.values.append(value)
            
        def query(self, query_str):
            query_matrix = string2one_hot_matrix(query_str)
            
            query_matrix = query_matrix.share(*self.owners)

            key_matches = list()
            for key in self.keys:

                key_match = strings_equal(key, query_matrix)
                key_matches.append(key_match)

            result = self.values[0] * key_matches[0]

            for i in range(len(self.values) - 1):
                result += self.values[i+1] * key_matches[i+1]
                
            result = result.get()

            return values2string(result).replace(".","")
    ```
    ```python3
    db = EncryptedDB(bob, alice, secure_worker, max_val_len=256)

    db.add_entry("Bob","(123) 456 7890")
    db.query("Bob")
    ``` -->
- Encrypted Deep Learning in PyTorch
    - bring the whole model together without knowing each other
        - one may own data, one own the model
<!--     ```python3
    encrypted_model = model.fix_precision().share(alice, bob, crypto_provider=secure_worker)

    encrypted_data = data.fix_precision().share(alice, bob, crypto_provider=secure_worker)

    encrypted_prediction = encrypted_model(encrypted_data)
    encrypted_prediction.get().float_precision()
    ```
- in Keras
    ```python3
    model = share(alice, bob, carol)
    model = serve(nums_requests=3)
    ``` -->