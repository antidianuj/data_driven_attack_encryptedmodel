# data_driven_attack_encryptedmodel

This is a study of attack on encrypted models, as a means to defend them when they are residing around server or embedded devices. One typical example is the encryption mechanism mentioned in "Lin, Ning, et al. "Chaotic weights: A novel approach to protect intellectual property of deep neural networks." IEEE Transactions on Computer-Aided Design of Integrated Circuits and Systems 40.7 (2020): 1327-1339.".
Particularly, the abstraction of encryption is that each parameters gets mapped from real to real domain, and the inverse encryption function function is not unique (can be a lot) when the parameters are unknown. AN example of such encryption scheme is the Arnold Chaotic map based encryption, the work on which was done by the mentioned paper, and will be the source of proof of concept for this work.

![image](https://user-images.githubusercontent.com/47445756/233842611-6713c960-f754-4018-a3e8-aea599637084.png)

My theory surrounding the attack is that if we have a proxy of the original model, it has some rational training and validation curves. Now substituion of any encrypted layer with a given layer, should lead to drastic change in its training and validation curves, and the distinction can be captured in a deep learning manner. My motivation of attack is inspired from first Membership inference attack pipeline, though mine is different in several ways.

The attack model is that the adversary has knowledge of the model architecture information and the attacker has either some knowledge of the training dataset, or some opensource dataset similar to the training dataset (same as Shokri's MIA threat model).

Following is my pipeline of attack.

![image](https://user-images.githubusercontent.com/47445756/233842834-68f2252d-8250-4390-9f4d-2ea0ff08f84e.png)

Basically, my proposed attack contains three iterations (represented by black, orange and dark green boxes), whose output is a decrypted model. Within the black box, we first train a dummy model, as proxy of original model (as per attack model), and record its training profile. Then we substitute first layer with a encrypted layer, and performs arbitrary iterations on it such that the resultant layer matches the reference substituted layer by some ammount. I call the amount of match as 'match ratio' and create several such versions of this substituted layer so that match ratio can cover values between 0 and 1 in an uniform distribution manner. I then slowly train the new models, and get their training profiles. Later, I concatenate the original training profile with the training profiles gotten from dark green box, which forms a dataset, with input as training and validation accuracy and loss curves, and output as match ratio. This dataset is fitted on a neural network, which serves an eye for a search algorithm, that can realize whether a particular search for optimal decryption function is optimal or not.
The black box, utilizes this indicator function neural network to search for candidate decryption functions either through brute force manner, or through more optimized search algorithms like Genetic or Baeysian searches.

![image](https://user-images.githubusercontent.com/47445756/233843449-989b9cad-198f-4195-a093-e36d392b6ea4.png)


From the proof of concept perspective, if we focus on only first layer of an arbitrary CNN model over some image data, the attacker wants to determine how to get the original kernel back from the Arnold mapped kernel, which destroys the accuracy or feature representation of the model.

![image](https://user-images.githubusercontent.com/47445756/233843618-48a97665-560f-4bc1-b836-f19295d31eb4.png)

On implementing my scheme, as mentioned above, I was able to achieve following performance of indicator function where it tries to distinguish between optimal inverse functions and random inverse functions (which would be permutation of indices of the kernel in current case).

![image](https://user-images.githubusercontent.com/47445756/233843720-33b75e0a-3eb5-4388-bd4f-c70f1d7f8bce.png)

For this case, following is the training data gotten to train this indicator function.

![image](https://user-images.githubusercontent.com/47445756/233844040-84532787-05e2-4617-a551-fd45776b1d3a.png)



I first implemented a bruteforce search, and also injected the optimal points within the search domain to quicky verify if the optimal kernel of the layer can be determined. So, following is the evolution of actual match ratio, and approximated measurements (determined by the indicator function).

![image](https://user-images.githubusercontent.com/47445756/233843980-40679a10-e758-48c1-b3cc-7ecd61189400.png)


It was able to find the optimal kernel, and the corresponding output comparison of original layer, and reconstructed layer is show below.

![image](https://user-images.githubusercontent.com/47445756/233844152-78d9a5c5-3141-4a4c-84bd-167bf59ae1a7.png)


However, I took a different strategy next time, and consider iterations of parameters, instead of permutations of the parameter, to convert the problem into convex optimization problem, and the search faster. In this course, I considered basic genetic algorithm, and tried to determine iteration of parameters, that minimize the objective function (or maximize the measurement of the indicator function). Unfortunately, the results in this case were not great. While the algorithm was converging, as shown below, it failed to find the optimal kernal of the layer. 
![image](https://user-images.githubusercontent.com/47445756/233844320-6886c8ee-f095-4337-9ece-ce2fe56dddbf.png)


Following is the comparison of orignal layer output and output gotton through Genetic algorithm based convex optimization approach.
![image](https://user-images.githubusercontent.com/47445756/233844349-d2f13377-8223-49c9-a9ed-caa9324abafe.png)

One can conclude that the search algorithm should be reflective of the encryption mechanism. This is the end of my current investigation of the proposed attack for now.

