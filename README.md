# data_driven_attack_encryptedmodel

This is a study of attack on encrypted models, as a means to defend them when they are residing around server or embedded devices. One typical example is the encryption mechanism mentioned in "Lin, Ning, et al. "Chaotic weights: A novel approach to protect intellectual property of deep neural networks." IEEE Transactions on Computer-Aided Design of Integrated Circuits and Systems 40.7 (2020): 1327-1339.".

Particularly, the abstraction of encryption is that each parameters gets mapped from real to real domain, and the inverse encryption function function is not unique (can be a lot) when the parameters are unknown.

![image](https://user-images.githubusercontent.com/47445756/233842611-6713c960-f754-4018-a3e8-aea599637084.png)

My theory surrounding the attack is that if we have a proxy of the original model, it has some rational training and validation curves. Now substituion of any encrypted layer with a given layer, should lead to drastic change in its training and validation curves, and the distinction can be captured in a deep learning manner. My motivation of attack is inspired from first Membership inference attack pipeline, though mine is different in several ways.

The attack model is that the adversary has knowledge of the model architecture information and the attacker has either some knowledge of the training dataset, or some opensource dataset similar to the training dataset (same as Shokri's MIA threat model).

Following is my pipeline of attack.

![image](https://user-images.githubusercontent.com/47445756/233842834-68f2252d-8250-4390-9f4d-2ea0ff08f84e.png)

Basically, my proposed attack contains three iterations (represented by black, orange and dark green boxes), whose output is a decrypted model. Within the black box, we first train a dummy model, as proxy or original model (as per attack model), and record its training profile. Then we substitute first layer with a encrypted layer, and performs arbitrary iterations on it such that the resultant layer matches the reference substituted layer by some ammount. I call the amount of match as 'match ratio' and create several such versions of this substituted layer so that match ratio can cover values between 0 and 1 in an uniform distribution manner. I then slowly train the new models, and get their training profiles. Later, I concatenate the original training profile with the training profiles gotten from dark green box, which forms a dataset, with input as training and validation accuracy and loss curves, and output as match ratio. This dataset is fitted on a neural network, which serves an eye for a search algorithm, that can realize whether a particular search for optimal decryption function is optimal or not.
The black box, utilizes this indicator function neural network to search for candidate decryption functions either through brute force manner, or through more optimized search algorithms like Genetic or Baeysian searches.

![image](https://user-images.githubusercontent.com/47445756/233843449-989b9cad-198f-4195-a093-e36d392b6ea4.png)



